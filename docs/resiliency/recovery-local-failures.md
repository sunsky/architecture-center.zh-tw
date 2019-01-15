---
title: 技術指導：從 Azure 的本機失敗中復原
description: 了解和設計復原性、高可用性、容錯的應用程式及規劃災害復原 (著重於 Azure 內的本機失敗) 的相關文章。
author: adamglick
ms.date: 08/18/2016
ms.openlocfilehash: c5e26eefb8d5a8d424534081ddd5d1ea0454c17e
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54112850"
---
[!INCLUDE [header](../_includes/header.md)]

# <a name="azure-resiliency-technical-guidance-recovery-from-local-failures-in-azure"></a>Azure 復原技術指導：從 Azure 的本機失敗中復原

應用程式可用性有兩個主要威脅︰

- 裝置失敗，例如磁碟機及伺服器
- 重要資源耗盡，例如在尖峰負載狀況下計算

Azure 可提供資源管理、彈性、負載平衡和資料分割的組合，以在這些情況下實現高可用性。 在這些功能之中，有些會自動針對所有 Azure 服務來執行。 不過，有時候應用程式開發人員則必須另外執行一些工作，以從中受惠。

## <a name="cloud-services"></a>雲端服務

Azure 雲端服務是由含有一或多個 Web 或背景工作角色的集合所組成。 角色的一或多個執行個體可以同時執行。 組態會決定執行個體的數目。 角色執行個體可透過名為網狀架構控制器的元件進行監視及管理。 網狀架構控制器會自動偵測並回應軟體和硬體的失敗。

每個角色執行個體都會在自己的虛擬機器 (VM) 中執行，並透過客體代理程式與其網狀架構控制器通訊。 客體代理程式會收集資源和節點的度量，包括 VM 使用量、狀態、記錄檔、資源使用量、例外狀況和失敗狀況。 網狀架構控制器會以可設定的間隔查詢客體代理程式，並在客體代理程式未能回應時重新啟動 VM。 如果發生硬體失敗，相關聯的網狀架構控制器會將所有受影響的角色執行個體移至新的硬體節點，並將網路重新設定為將流量路由傳送至該處。

若要受益於這些功能，開發人員應確定服務角色都會避免在角色執行個體上儲存狀態。 相反地，所有永續性資料應存取自耐久性儲存體，例如 Azure 儲存體或 Azure SQL Database。 這可讓任何角色處理要求。 同時也表示角色執行個體即使在任何時候當機，也不會在服務的暫時性或永續性狀態導致不一致。

將狀態儲存於角色外部的要求有幾個含意。 例如，這表示 Azure 儲存體資料表的所有相關變更，皆應該盡可能在單一實體群組交易中完成變更。 當然，變更不可能總是可在單一交易中完成。 您必須特別小心地處理，才能確保當角色執行個體失敗，而中斷跨越服務的二個以上永續性狀態更新的長時間執行作業時，不會造成問題。 如果另一個角色嘗試重試這類作業，它應該要預料到並能處理工作僅部分完成的情況。

例如，假設有某項服務會將資料分割到多個存放區。 如果背景工作角色在重新放置分區時當機，分區重新放置作業可能並未完成。 或者重新放置作業可能會由不同的背景工作角色從頭開始重複此作業，因而可能造成孤立的資料或資料損毀。 為了避免發生問題，長時間執行的作業必須具有下列兩種特性之一或兩者兼具︰

- 等冪︰可重複執行且沒有副作用。 為了達到等冪，長時間執行的作業不論執行幾次，都應該具有相同的效果，即使在執行期間發生中斷也一樣。
- 以累加方式重新啟動︰能夠從最近的失敗點繼續。 為了能以累加方式重新啟動，長時間執行的作業應該包含一系列較小的不可部分完成作業。 它也應該在耐久性儲存體中記錄其進度，以便後續的每次叫用都會在其上次停止處重新開始。

最後，所有長時間執行的作業都應該重複叫用，直到成功為止。 例如，佈建作業可能會放在 Azure 佇列中，然後只會在成功時才由背景工作角色從佇列中移除。 記憶體回收可能必須清除中斷的作業所建立的資料。

### <a name="elasticity"></a>彈性

每個角色所執行的初始執行個體數目是由每個角色的組態決定。 系統管理員一開始應該根據預期的負載，設定讓每個角色執行兩個以上的執行個體。 但您可以在使用模式變更時，輕鬆地相應增加或減少角色執行個體。 您可以在 Azure 入口網站中手動執行此程序，或使用 Windows PowerShell、服務管理 API 或協力廠商工具自動進行此程序。 如需詳細資訊，請參閱 [如何自動調整應用程式](/azure/cloud-services/cloud-services-how-to-scale/)。

### <a name="partitioning"></a>分割

Azure 網狀架構控制器使用兩種類型的資料分割︰

- 「更新網域」  可用來以群組為單位升級服務的角色執行個體。 Azure 會在多個更新網域內部署服務執行個體。 在進行就地更新時，網狀架構控制器會關閉一個更新網域中的所有執行個體，加以更新，然後予以重新啟動，之後再移至下一個更新網域。 這種方法可避免整個服務在更新程序進行期間變得無法使用。
- 「容錯網域」  會定義硬體或網路可能的失敗點。 對於具有多個執行個體的任何角色，網狀架構控制器會確保執行個體能分配給多個容錯網域，以避免獨立的硬體故障造成服務中斷。 容錯網域負責控管所有的伺服器和叢集失敗的揭露。

[Azure 服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/) 保證，當兩個以上的 Web 角色執行個體部署到不同的容錯和升級網域時，它們會在至少 99.95% 的時間內具有外部連線能力。 和更新網域不同的是，容錯網域的數目無法透過任何方法來控制。 Azure 會自動配置容錯網域，並在其中分配角色執行個體。 每個角色至少會將前兩個執行個體置於不同的容錯和升級網域，以便確保任何具有至少兩個執行個體的角色都能符合 SLA。 下圖說明這個概念。

![容錯網域隔離的簡化檢視](./images/technical-guidance-recovery-local-failures/partitioning-1.png)

### <a name="load-balancing"></a>負載平衡

Web 角色的所有輸入流量都會通過無狀態的負載平衡器，它會將用戶端要求分配給角色執行個體。 個別角色執行個體沒有公用 IP 位址，且無法直接從網際網路定址。 Web 角色沒有狀態，因此任何用戶端要求都能路由至任何角色執行個體。 [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck.aspx) 事件每 15 秒就會引發一次。 您可以使用此事件來指出角色是否已準備好接收流量，或是否處於忙碌狀態，因此應該從負載平衡器輪替名單中剔除。

## <a name="virtual-machines"></a>虛擬機器

Azure 虛擬機器和平台即服務 (PaaS) 計算角色在高可用性的許多方面有不同之處。 在某些情況下，您必須進行額外工作才能確保高可用性。

### <a name="disk-durability"></a>磁碟耐久性

不同於 PaaS 角色執行個體，即使虛擬機器重新放置，虛擬機器的磁碟機上儲存的資料仍是永續性的。 Azure 虛擬機器會將存在的 VM 磁碟用做 Azure 儲存體中的 Blob。 由於 Azure 儲存體的可用性特性，虛擬機器的磁碟機上儲存的資料也是高可用性的。

請注意，磁碟機 D (在 Windows VM 中) 是這項規則的例外狀況。 實際上，磁碟機 D 是機架伺服器上裝載 VM 的實體儲存體，如果回收 VM，其資料將會遺失。 磁碟機 D 僅適用於暫時儲存。 在 Linux，Azure「通常」(但不是一定) 將本機暫存磁碟公開為 /dev/sdb 區塊裝置。 它通常由 Azure Linux 代理程式掛接為 /mnt/resource 或 /mnt 掛接點 (可透過 /etc/waagent.conf 設定)。

<!-- markdownlint-disable MD024 -->

### <a name="partitioning"></a>分割

Azure 原本就了解 PaaS 應用程式 (Web 角色和背景工作角色) 中的各層，因此可以正確地將它們分配到容錯網域和更新網域。 相反地，基礎結構即服務 (IaaS) 應用程式中的各層則必須透過可用性設定組手動定義。 IaaS 底下的 SLA 需要可用性設定組。

![Azure 虛擬機器的可用性設定組](./images/technical-guidance-recovery-local-failures/partitioning-2.png)

在上圖中，Internet Information Services (IIS) 層 (做為 Web 應用程式層) 和 SQL 層 (做為資料層) 會指派給不同的可用性設定組。 這可確保每一層的所有執行個體可透過分配虛擬機器給多個容錯網域而具備硬體備援，並且整個層級不會在更新期間遭到關閉。

### <a name="load-balancing"></a>負載平衡

如果流量應該分配給各個 VM，您必須將應用程式中的 VM 予以群組，並在特定 TCP 或 UDP 端點進行負載平衡。 如需詳細資訊，請參閱 [虛擬機器負載平衡](/azure/virtual-machines/virtual-machines-linux-load-balance/?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 如果 VM 接收來自其他來源 (例如佇列機制) 的輸入，則不需要負載平衡器。 負載平衡器會使用基本健康情況檢查，來判斷是否應該將流量傳送至節點。 此外，您也可以建立自己的探查，來實作可決定 VM 是否應接收流量的應用程式特定健康情況度量。

## <a name="storage"></a>儲存體

Azure 儲存體是 Azure 的基準耐久性資料服務。 它可提供 Blob、資料表、佇列和 VM 磁碟儲存體。 它會結合複寫和資源管理，在單一資料中心內提供高可用性。 Azure 儲存體可用性 SLA 保證，在至少 99.9% 的時間內︰

- 會成功且正確地處理格式正確的要求，以新增、更新、讀取和刪除資料。
- 儲存體帳戶能夠連線到網際網路閘道。

### <a name="replication"></a>複寫

Azure 儲存體可透過在位於區域內完全獨立的實體儲存子系統中，於其中的不同磁碟機上為所有資料維護多個複本，以促進資料耐久性。 這些資料會以同步方式進行複寫，並且會先認可所有複本再確認寫入。 Azure 儲存體有強烈一致性，也就是說，讀取一定會反映最近的寫入。 此外，資料複本會持續接受掃描，以偵測和修復位元損壞，這些損壞是經常被忽略的儲存資料完整性威脅。

只要使用 Azure 儲存體，服務就會從複寫獲益。 服務開發人員不必進行額外的工作，就能從本機失敗復原。

### <a name="resource-management"></a>資源管理

在 2014 年 5 月之後建立的儲存體帳戶，最多可以成長到 500 TB (先前的上限為 200 TB)。 如果需要額外空間，就必須將應用程式設計成會使用多個儲存體帳戶。

### <a name="virtual-machine-disks"></a>虛擬機器磁碟

在 Azure 儲存體中，虛擬機器的磁碟會儲存為分頁 Blob，因此具有和 Blob 儲存體完全相同的耐久性和延展性屬性。 即使執行 VM 的伺服器失敗，因此必須在另一部伺服器上重新啟動，此設計仍可讓虛擬機器磁碟上的資料永續存在。

## <a name="database"></a>資料庫

### <a name="sql-database"></a>SQL Database

Azure SQL Database 提供資料庫即服務。 它可讓應用程式快速佈建關聯式資料庫、在其中插入資料並進行查詢。 它提供許多熟悉的 SQL Server 特性與功能，同時減少了硬體、組態、修補和復原的負擔。

> [!NOTE]
> Azure SQL Database 不提供與 SQL Server 的一對一功能對應。 其目的是要滿足一組唯一適用於雲端應用程式的不同需求 (彈性調整、資料庫即服務以降低維護成本等等)。 如需詳細資訊，請參閱[選擇雲端 SQL Server 選項：Azure SQL (PaaS) Database 或 Azure VM (IaaS)](/azure/sql-database/sql-database-paas-vs-sql-server-iaas/) 上的 SQL Server。

#### <a name="replication"></a>複寫

Azure SQL Database 針對節點層級失敗提供內建的復原功能。 所有對資料庫的寫入，都會透過仲裁認可技術自動複寫到兩個以上的背景節點中  (主要節點和至少一個次要節點必須先確認活動已寫入交易記錄內，交易才會被視為成功並傳回)。萬一發生節點失敗，資料庫會自動容錯移轉至其中一個次要複本。 這會導致用戶端應用程式暫時中斷連線。 有鑑於此，所有 Azure SQL Database 用戶端都必須實作某種形式的暫時性連線處理。 如需詳細資訊，請參閱[重試服務的特定指引](/azure/best-practices-retry-service-specific/)。

#### <a name="resource-management"></a>資源管理

每個資料庫在建立時都會設定大小上限。 目前可用的大小上限為 1 TB (大小限制根據您的服務層而有所不同，請參閱 [Azure SQL Database 的服務層和效能層級](/azure/sql-database/sql-database-resource-limits/#service-tiers-and-performance-levels)。 當資料庫達到大小上限，就會拒絕其他 INSERT 或 UPDATE 命令  (但仍可查詢和刪除資料)。

在資料庫內，Azure SQL Database 會使用網狀架構來管理資源。 不過，它會使用通道拓撲 (而不是網狀架構控制器) 來偵測失敗。 叢集中的每個複本都有兩個鄰近項目，而且複本需負責在鄰近項目關閉時進行偵測。 當複本關閉時，其鄰近項目會觸發重新設定代理程式，以在另一部電腦上重新建立複本。 所提供的引擎節流可確保邏輯伺服器不會在電腦上使用太多資源，或超過電腦的實體限制。

### <a name="elasticity"></a>彈性

如果應用程式需要超過 1 TB 的資料庫限制，就必須實作相應放大方法。 您可以透過將資料手動分割 (也稱為分區化) 到多個 SQL Database，以使用 Azure SQL Database 相應放大。 此相應放大方法可讓成本有機會隨著規模達成接近線性的成長。 彈性成長或隨選容量可以視需要來成長，而其成本會隨之遞增，因為資料庫的計費是根據每天使用的實際大小平均值，而不是可能的最大大小。

## <a name="sql-server-on-virtual-machines"></a>虛擬機器上的 SQL Server

透過在 Azure 虛擬機器上安裝 SQL Server (2014 版或更新版本)，您將可以利用 SQL Server 的傳統可用性功能。 這些功能包括 AlwaysOn 可用性群組或資料庫鏡像。 請注意，與內部部署、非虛擬化的 IT 基礎結構相較之下，Azure VM、儲存體和網路各有不同的作業特性。 若要在 Azure 中成功實作高可用性/災害復原 (HA/DR) SQL Server 解決方案，您必須了解這些差異，並配合這些差異設計您的解決方案。

### <a name="high-availability-nodes-in-an-availability-set"></a>可用性設定組中的高可用性節點

當您在 Azure 中實作高可用性解決方案時，您可以使用 Azure 中的可用性設定組，將高可用性節點放入分隔的容錯網域和升級網域。 但您必須要清楚，可用性設定組是一種 Azure 概念。 您最好遵循此概念，以確保資料庫確實具有高可用性，不論您是使用 AlwaysOn 可用性群組、資料庫鏡像或其他方法。 如果您沒有遵循這個最佳做法，您可能會誤以為系統具有高可用性。 但事實上節點卻可能全部同時失敗，只因為它們剛好放在 Azure 區域內的相同容錯網域中。

這項建議不適用於記錄傳送。 因為既然要做為災害復原功能，您就應該確定伺服器是在不同的 Azure 區域執行。 根據定義，這些區域是不同的容錯網域。

若要將透過傳統入口網站部署的 Azure 雲端服務放入相同的可用性設定組中，您必須將其部署至相同的雲端服務中。 透過 Azure Resource Manager (目前的入口網站) 部署的 VM 沒有這項限制。 對於傳統入口網站部署在 Azure 雲端服務中的 VM，只有相同雲端服務內的節點可以參與相同的可用性設定組。 此外，雲端服務 VM 應該位於相同的虛擬網路中，以確保即使在服務修復之後，VM 也能保有其 IP。 這可避免 DNS 更新中斷。

### <a name="azure-only-high-availability-solutions"></a>僅限 Azure：高可用性解決方案

您可以使用 AlwaysOn 可用性群組或資料庫鏡像，為 Azure 中的 SQL Server 資料庫提供高可用性解決方案。

下圖說明在 Azure 虛擬機器中執行的 AlwaysOn 可用性群組的架構。 此圖擷取自關於此主題的深度文章： [Azure 虛擬機器中的 SQL Server 高可用性和災害復原](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr/)。

![Microsoft Azure 中的 AlwaysOn 可用性群組](./images/technical-guidance-recovery-local-failures/high_availability_solutions-1.png)

您也可以使用 Azure 入口網站中的 AlwaysOn 範本，以在 Azure VM 上端對端自動佈建 AlwaysOn 可用性群組部署。 如需詳細資訊，請參閱 [Microsoft Azure 入口網站資源庫提供的 SQL Server AlwaysOn](https://blogs.technet.microsoft.com/dataplatforminsider/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery/)。

下圖說明如何在 Azure 虛擬機器上使用資料庫鏡像。 此圖擷取自深度主題： [Azure 虛擬機器中的 SQL Server 高可用性和災害復原](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr/)。

![Microsoft Azure 中的資料庫鏡像](./images/technical-guidance-recovery-local-failures/high_availability_solutions-2.png)

> [!NOTE]
> 這兩種架構都必須要有網域控制站。 不過，在使用資料庫鏡像時，則可以使用伺服器憑證，而不需要有網域控制站。

## <a name="other-azure-platform-services"></a>其他 Azure 平台服務

建置在 Azure 之上的應用程式可獲益於平台功能而從本機失敗中復原。 在某些情況下，您可以採取特定動作來增加特定案例的可用性。

### <a name="service-bus"></a>服務匯流排

為了避免 Azure 服務匯流排暫時中斷，請考慮建立耐久性用戶端佇列。 這會暫時使用替代的本機儲存體機制，來儲存無法新增至服務匯流排佇列的訊息。 應用程式可以決定服務還原之後，要如何處理暫時儲存的訊息。 如需詳細資訊，請參閱[使用服務匯流排代理傳訊的效能改進最佳做法](/azure/service-bus-messaging/service-bus-performance-improvements/)和[服務匯流排 (災害復原)](recovery-loss-azure-region.md#other-azure-platform-services)。

### <a name="hdinsight"></a>HDInsight

根據預設，與 Azure HDInsight 相關聯的資料會儲存在 Azure Blob 儲存體中。 Azure 儲存體會指定 Blob 儲存體的高可用性和耐久性屬性。 與 Hadoop MapReduce 作業相關聯的多重節點處理，會在暫時性的 Hadoop 分散式檔案系統 (HDFS) 中進行，如果 HDInsight 有需要，便會佈建此系統。 根據預設，MapReduce 作業的結果也會儲存在 Azure Blob 儲存體中，因此在取消佈建 Hadoop 叢集之後，處理過的資料仍具有耐久性且維持高可用性。 如需詳細資訊，請參閱 [HDInsight (災害復原)](recovery-loss-azure-region.md#other-azure-platform-services)。

## <a name="checklists-for-local-failures"></a>本機失敗的檢查清單

### <a name="cloud-services-checklist"></a>雲端服務檢查清單

1. 檢閱此文件的＜雲端服務＞一節。
2. 為每個角色設定至少兩個執行個體。
3. 在耐久性儲存體 (而非角色執行個體) 中保存狀態。
4. 正確地處理 StatusCheck 事件。
5. 盡可能將相關的變更包裝在交易中。
6. 確認背景工作角色的工作具有等冪性且可以重新啟動。
7. 持續叫用作業，直到成功為止。
8. 考慮自動調整策略。

### <a name="virtual-machines-checklist"></a>虛擬機器檢查清單

1. 檢閱此文件的＜虛擬機器＞一節。
2. 請勿使用磁碟機 D 來進行永續性儲存。
3. 將服務層中的電腦群組為可用性設定組。
4. 設定負載平衡和選擇性探查。

### <a name="storage-checklist"></a>儲存體檢查清單

1. 檢閱此文件的＜儲存體＞一節。
2. 當資料或頻寬超出配額時，使用多個儲存體帳戶。

### <a name="sql-database-checklist"></a>SQL Database 檢查清單

1. 檢閱此文件的＜SQL Database＞一節。
2. 實作重試原則來處理暫時性錯誤。
3. 使用分割/分區化做為相應放大策略。

### <a name="sql-server-on-virtual-machines-checklist"></a>虛擬機器上的 SQL Server 檢查清單

1. 檢閱此文件的＜虛擬機器上的 SQL Server＞一節。
2. 遵循先前的虛擬機器建議。
3. 使用 SQL Server 高可用性功能，例如 AlwaysOn。

### <a name="service-bus-checklist"></a>服務匯流排檢查清單

1. 檢閱此文件的＜服務匯流排＞一節。
2. 考慮建立耐久性用戶端佇列來做為備份。

### <a name="hdinsight-checklist"></a>HDInsight 檢查清單

1. 檢閱此文件的＜HDInsight＞一節。
2. 本機失敗不需要其他可用性步驟。

<!-- markdownlint-enable MD024 -->

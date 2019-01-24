---
title: 從 Azure 區域遺失中復原
description: 了解和設計復原性、高可用性、容錯的應用程式及規劃災害復原。
author: adamglick
ms.date: 08/18/2016
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.custom: resiliency
ms.openlocfilehash: 7f207bbc0bb0128126f9b828dc100d43553cb100
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54487967"
---
[!INCLUDE [header](../_includes/header.md)]

# <a name="azure-resiliency-technical-guidance-recovery-from-a-region-wide-service-disruption"></a>Azure 復原技術指導：從全區域服務中斷復原

Azure 在實體和邏輯上劃分單位，稱為區域。 區域由一或多個非常接近的資料中心組成。

在罕見的情況下，整個區域內的設施可能變得無法存取 (例如，由於網路故障)。 或者，設施可能完全喪失 (例如，因為天然災害)。 本節說明 Azure 可用來建立跨區域分散之應用程式的能力。 這樣的分散有助於將某個區域失常而波及其他區域的可能性降到最低。

## <a name="cloud-services"></a>雲端服務

### <a name="resource-management"></a>資源管理

您可以將計算執行個體分散至各區域，方法是在每個目標區域建立個別的雲端服務，再將部署封裝發佈至每個雲端服務。 但請注意，將流量分散到不同區域的雲端服務時，必須由應用程式開發人員實作，或透過流量管理服務來達成。

在災害復原之前，先決定可部署的備用角色執行個體數目，是容量規劃的重要一環。 備妥完整規模的次要部署可確保容量在需要時垂手可得，但這實際上會增加成本。 常見的模式是備妥較小的次要部署，足夠執行關鍵服務即可。 這種小型的次要部署非常適合用來保留容量以及測試次要環境的組態。

> [!NOTE]
> 訂用帳戶配額不保證容量。 配額只是信用額度。 若要保證容量，必須在服務模型中定義所需的角色數目，還必須部署角色。

### <a name="load-balancing"></a>負載平衡

若要平衡各區域的流量負載，需要使用流量管理方案。 Azure 提供 [Azure 流量管理員](https://azure.microsoft.com/services/traffic-manager/)。 您也可以利用協力廠商服務所提供的類似流量管理功能。

### <a name="strategies"></a>策略

有許多不同的策略可實作跨區域的分散式計算。 這些都必須按照特定的商務需求和應用程式的狀況來量身打造。 概括而言，這些方法可分為下列類別︰

- **災害時重新部署**︰在此方法中，發生災害時會從頭開始重新部署應用程式。 這適用於不需要保證復原時間的非關鍵性應用程式。

- **暖備援 (主動/被動)**︰在替代區域建立次要託管服務，並部署角色以保證最起碼的容量，但是角色並不接收生產流量。 此方法適用於未設計成將流量分散於各區域的應用程式。

- **熱備援 (主動/主動)**：應用程式設計成接收多個區域的生產負載。 基於災害復原用途，每個區域的雲端服務可能設定為高於所需的容量。 此外，在災害和容錯移轉時，雲端服務可能視需要而相應放大。 此方法需要大量投注於應用程式設計，但成效顯著。 這些成效包括較短的保障復原時間、連續測試所有復原位置，以及有效運用容量。

分散式設計的完整討論已超出本文的範圍。 如需進一步資訊，請參閱 [Azure 應用程式的災害復原與高可用性](https://aka.ms/drtechguide)。

## <a name="virtual-machines"></a>虛擬機器

基礎結構即服務 (IaaS) 虛擬機器 (VM) 的復原在許多方面類似於平台即服務 (PaaS) 計算復原。 但由於 IaaS VM 同時包含 VM 和 VM 磁碟，仍有重大的差別。

- **使用 Azure 備份建立應用程式一致的跨區域備份**。
  [Azure 備份](https://azure.microsoft.com/services/backup/) 可讓客戶建立跨多個 VM 磁碟的應用程式一致備份，並支援跨區域的備份複寫。 建立時選擇異地複寫備份保存庫，即可做到這一切。 請注意，建立時就必須設定備份保存庫的複寫。 不能在稍後設定。 如果區域遺失，Microsoft 會提供備份給客戶使用。 客戶將能夠還原至任何已設定的還原點。

- **將資料磁碟與作業系統磁碟隔開**。 關於 IaaS VM，請注意您必須重新建立 VM，才能變更作業系統磁碟。 如果您的復原策略是於災害後重新部署，就不會有問題。 不過，如果您使用暖備援方法來保留容量，可能會有問題。 若要正確實作此方法，您必須將正確的作業系統磁碟部署到主要和次要位置，而且應用程式資料必須儲存在另一個磁碟機。 可能的話，請使用兩個位置都可提供的標準作業系統組態。 容錯移轉之後，必須再將資料磁碟機附加至次要 DC 內的現有 IaaS VM。 使用 AzCopy 將資料磁碟的快照複製到遠端網站。

- **請留意異地容錯移轉多個 VM 磁碟之後潛在的一致性問題**。 VM 磁碟會實作成 Azure 儲存體 Blob，並具有相同的異地複寫特性。 除非使用 [Azure 備份](https://azure.microsoft.com/services/backup/) ，否則不能保證磁碟之間的一致性，因為異地複寫並非同步，而且會獨立複寫。 異地容錯移轉後，保證個別 VM 磁碟處於當機時保持一致狀態，但不保證跨磁碟維持一致。 這在某些情況下會造成問題 (例如，磁碟串接的情況)。

## <a name="storage"></a>儲存體

### <a name="recovery-by-using-geo-redundant-storage-of-blob-table-queue-and-vm-disk-storage"></a>使用 Blob、資料表、佇列和 VM 磁碟儲存體的異地備援儲存體來復原

在 Azure 中，blob、資料表、佇列和 VM 磁碟都預設為異地複寫。 這稱為異地備援儲存體 (GRS)。 GRS 會將儲存體資料複寫至特定地理區域內數百英哩遠的配對資料中心。 GRS 目的是在資料中心重大災害情況下提供額外的持久性。 Microsoft 會控制容錯移轉時機，只有在確定原始主要位置不可能於合理時間內復原的重大災害下，才會進行容錯移轉。 在某些情況下，這可能需要好幾天。 雖然服務等級協定尚未涵蓋同步處理間隔，但通常幾分鐘之內就會複寫資料。

進行異地容錯移轉時，不會變更帳戶的存取方式 (URL 和帳戶金鑰不會變更)。 不過，儲存體帳戶在容錯移轉之後會移至不同區域。 這可能會影響儲存體帳戶需要有地緣性的應用程式。 即使服務和應用程式不要求儲存體帳戶必須在相同資料中心內，由於跨資料中心的延遲和頻寬費用，仍可能不得已必須暫時將流量移至容錯移轉區域。 這個因素需要納入整體災害復原策略中。

除了 GRS 所提供的自動容錯移轉，Azure 還引進一項服務，可讓您讀取次要儲存體位置中的資料複本。 這稱為讀取權限異地備援儲存體 (RA-GRS)。

如需 GRS 和 RA-GRS 儲存體的詳細資訊，請參閱 [Azure 儲存體複寫](/azure/storage/storage-redundancy/)。

### <a name="geo-replication-region-mappings"></a>異地複寫區域對應

必須知道資料異地複寫到何處，才能知道需要儲存體地緣性的其他資料執行個體要部署到何處。 如需詳細資訊，請參閱 [Azure 配對的區域](/azure/best-practices-availability-paired-regions)。

### <a name="geo-replication-pricing"></a>異地複寫價格

Azure 儲存體的目前價格包含異地複寫。 這稱為異地備援儲存體 (GRS)。 如果您不想異地複寫您的資料，您可以在帳戶中停用異地複寫。 這稱為本地備援儲存體，按照 GRS 的折扣價收費。

### <a name="determining-if-a-geo-failover-has-occurred"></a>判斷是否發生異地容錯移轉

如果發生異地容錯移轉，此消息會張貼到 [Azure 服務健全狀況儀表板](https://azure.microsoft.com/status/)。 不過，應用程式可以實作自動化方法來監視儲存體帳戶的區域，以偵測此狀況。 這可以用來觸發其他復原作業，例如，在儲存體移至的區域啟用計算資源。 您可以從服務管理 API 透過 [取得儲存體帳戶屬性](https://msdn.microsoft.com/library/ee460802.aspx)來針對此狀況進行查詢。 相關的屬性如下︰

    <GeoPrimaryRegion>primary-region</GeoPrimaryRegion>
    <StatusOfPrimary>[Available|Unavailable]</StatusOfPrimary>
    <LastGeoFailoverTime>DateTime</LastGeoFailoverTime>
    <GeoSecondaryRegion>secondary-region</GeoSecondaryRegion>
    <StatusOfSecondary>[Available|Unavailable]</StatusOfSecondary>

### <a name="vm-disks-and-geo-failover"></a>VM 磁碟和異地容錯移轉

如 VM 磁碟一節所述，容錯移轉之後，不保證 VM 磁碟之間的資料一致性。 為了確保備份的正確性，應該使用備份產品 (例如 Data Protection Manager) 來備份和還原應用程式資料。

## <a name="database"></a>資料庫

### <a name="sql-database"></a>SQL Database

Azure SQL Database 提供兩種復原：異地還原和作用中異地複寫。

#### <a name="geo-restore"></a>異地還原

[異地還原](/azure/sql-database/sql-database-recovery-using-backups/#geo-restore) 也適用於基本、標準和進階資料庫。 它會在資料庫因裝載區域中的事件而無法使用時，提供預設復原選項。 異地還原與還原時間點類似，需要使用 Azure 異地備援儲存體中的資料庫備份。 它會從異地複寫的備份複本還原，因此可彈性地回應主要區域的儲存體中斷情況。 詳細資訊請參閱 [還原 Azure SQL Database 或容錯移轉到次要資料庫](/azure/sql-database/sql-database-disaster-recovery/)。

#### <a name="active-geo-replication"></a>主動式異地複寫

[作用中異地複寫](/azure/sql-database/sql-database-geo-replication-overview/) 適用於所有資料庫層。 其設計是針對比異地還原需要更主動復原的應用程式。 透過主動式異地複寫，您可以在不同區域的伺服器上最多建立四個可讀取的次要資料庫。 您可以起始容錯移轉至任何次要資料庫。 此外，主動式異地複寫可用來支援應用程式升級或重新配置案例，以及對唯讀工作負載進行負載平衡。 如需詳細資訊，請參閱[設定異地複寫](/azure/sql-database/sql-database-geo-replication-portal/)和[容錯移轉至次要資料庫](/azure/sql-database/sql-database-geo-replication-failover-portal/)。 請參閱[使用 SQL Database 的主動式異地複寫設計雲端災害復原應用程式](/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery/)和[使用 SQL Database 主動式異地複寫管理雲端應用程式的輪流升級](/azure/sql-database/sql-database-manage-application-rolling-upgrade/)，以取得如何設計和實作應用程式以及在不停機情況下升級應用程式的詳細說明。

### <a name="sql-server-on-virtual-machines"></a>虛擬機器上的 SQL Server

Azure 虛擬機器中執行的 SQL Server 2012 (和更新版本) 有各種選項可進行復原和維持高可用性。 如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 高可用性和災害復原](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr/)。

## <a name="other-azure-platform-services"></a>其他 Azure 平台服務

嘗試在多個 Azure 區域執行您的雲端服務時，您必須考量每個相依性的含意。 在下列各節中，特定服務的指引假設您必須在替代的 Azure 資料中心內使用相同的 Azure 服務。 這牽涉到組態和資料複寫工作。

> [!NOTE]
> 在某些情況下，這些步驟有助於緩和特定服務中斷運作，而不是資料中心全面性事件。 從應用程式的觀點來看，特定服務中斷運作可能只是功能受限，需要暫時將服務移轉到替代的 Azure 區域。

### <a name="service-bus"></a>服務匯流排

Azure 服務匯流排使用不跨越 Azure 區域的唯一命名空間。 因此，第一項需求是在替代區域設定必要的服務匯流排命名空間。 不過，還需要考量佇列訊息的持久性。 跨 Azure 區域複寫訊息有幾種策略。 如需這些複寫策略和其他災害復原策略的詳細資訊，請參閱 [將應用程式與服務匯流排中斷和災害隔絕的最佳作法](/azure/service-bus-messaging/service-bus-outages-disasters/)。 關於其他可用性考量，請參閱 [服務匯流排 (可用性)](recovery-local-failures.md#other-azure-platform-services)。

### <a name="app-service"></a>App Service 方案

若要將 Azure App Service 應用程式，例如 Web Apps 或 Mobile Apps 移轉到次要 Azure 區域，您必須有可供發佈的網站備份。 如果運作中斷未波及整個 Azure 資料中心，或許可以使用 FTP 下載網站內容的最新備份。 然後，在替代區域建立新的應用程式，除非您先前為了保留容量而已經這樣做。 將網站發佈到新的區域，並進行任何必要的組態變更。 這些變更可能包括資料庫連接字串或區域專用的其他設定。 如有需要，新增網站的 SSL 憑證，並變更 DNS CNAME 記錄，使自訂網域名稱指向重新部署的 Azure Web 應用程式 URL。

### <a name="hdinsight"></a>HDInsight

根據預設，與 HDInsight 相關聯的資料會儲存在 Azure Blob 儲存體中。 HDInsight 要求 Hadoop 叢集處理 MapReduce 作業，以及包含要分析之資料的儲存體帳戶，必須並存於相同的區域。 假設您使用 Azure 儲存體可用的異地複寫功能，如果主要區域因為某些原因而無法再使用，您可以在已複寫資料的次要地區存取您的資料。 您可以在已複寫資料的區域建立新的 Hadoop 叢集，然後繼續處理資料。 關於其他可用性考量，請參閱 [HDInsight (可用性)](recovery-local-failures.md#other-azure-platform-services)。

### <a name="sql-reporting"></a>SQL Reporting

目前，需要有多個 SQL Reporting 執行個體在不同 Azure 區域中，才能從 Azure 區域遺失中復原。 這些 SQL Reporting 執行個體應該存取相同的資料，且該資料必須有自己的復原計畫來因應災害。 您也可以為每份報告維護 RDL 檔案的外部備份複本。

### <a name="media-services"></a>媒體服務

Azure 媒體服務在編碼和串流處理方面有不同的復原方法。 一般而言，在區域性中斷運作期間，串流處理較重要。 為了對此有所準備，您應該在兩個不同 Azure 區域中都有媒體服務帳戶。 編碼的內容應該放在這兩個區域。 在失敗期間，您可以將串流流量重新導向替代區域。 任何 Azure 區域中都可以執行編碼。 如果編碼時間緊迫，例如，在即時事件處理期間，您必須能夠在失敗期間將作業提交至替代資料中心。

### <a name="virtual-network"></a>虛擬網路

組態檔提供最快的方式在替代 Azure 區域中設定虛擬網路。 在主要 Azure 區域中設定虛擬網路之後，從目前網路 [匯出虛擬網路設定](/azure/virtual-network/virtual-networks-create-vnet-classic-portal/) 到網路組態檔。 萬一主要區域中斷運作，請從儲存的組態檔 [還原虛擬網路](/azure/virtual-network/virtual-networks-create-vnet-classic-portal/) 。 接著，設定其他雲端服務、虛擬機器或跨單位設定來使用新的虛擬網路。

## <a name="checklists-for-disaster-recovery"></a>災害復原檢查清單

## <a name="cloud-services-checklist"></a>雲端服務檢查清單

1. 檢閱此文件的＜雲端服務＞一節。
2. 建立跨區域災害復原策略。
3. 了解在替代區域保留容量的利弊取捨。
4. 使用流量路由工具，例如 Azure 流量管理員。

## <a name="virtual-machines-checklist"></a>虛擬機器檢查清單

1. 檢閱此文件的＜虛擬機器＞一節。
2. 使用 [Azure 備份](https://azure.microsoft.com/services/backup/) 建立跨區域的應用程式一致備份。

## <a name="storage-checklist"></a>儲存體檢查清單

1. 檢閱此文件的＜儲存體＞一節。
2. 請勿停用儲存體資源的異地複寫。
3. 了解容錯移轉時異地複寫的替代區域。
4. 針對使用者控制的容錯移轉策略建立自訂的備份策略。

## <a name="sql-database-checklist"></a>SQL Database 檢查清單

1. 檢閱此文件的＜SQL Database＞一節。
2. 視需要使用[異地還原](/azure/sql-database/sql-database-recovery-using-backups/#geo-restore)或[異地複寫](/azure/sql-database/sql-database-geo-replication-overview/)。

## <a name="sql-server-on-virtual-machines-checklist"></a>虛擬機器上的 SQL Server 檢查清單

1. 檢閱此文件的＜虛擬機器上的 SQL Server＞一節。
2. 使用跨區域 AlwaysOn 可用性群組或資料庫鏡像。
3. 或者使用備份及還原到 Blob 儲存體。

## <a name="service-bus-checklist"></a>服務匯流排檢查清單

1. 檢閱此文件的＜服務匯流排＞一節。
2. 在替代區域中設定服務匯流排命名空間。
3. 針對跨區域訊息考慮採用自訂的複寫策略。

## <a name="app-service-checklist"></a>App Service 檢查清單

1. 檢閱此文件的＜App Service＞一節。
2. 在主要區域外部維護網站備份。
3. 如果是局部性中斷運作，嘗試使用 FTP 擷取目前站台。
4. 規劃將網站部署到替代區域中的新網站或現有網站。
5. 規劃應用程式與 DNS CNAME 記錄的設定變更。

## <a name="hdinsight-checklist"></a>HDInsight 檢查清單

1. 檢閱此文件的＜HDInsight＞一節。
2. 使用複寫的資料在區域建立新的 Hadoop 叢集。

## <a name="sql-reporting-checklist"></a>SQL Reporting 檢查清單

1. 檢閱此文件的＜SQL Reporting＞一節。
2. 在不同區域維護替代 SQL Reporting 執行個體。
3. 維護個別計畫將目標複寫到該區域。

## <a name="media-services-checklist"></a>媒體服務檢查清單

1. 檢閱此文件的＜媒體服務＞一節。
2. 在替代區域建立媒體服務帳戶。
3. 編碼兩個區域中相同的內容以支援串流容錯移轉。
4. 於服務中斷時將編碼作業提交至替代區域。

## <a name="virtual-network-checklist"></a>虛擬網路檢查清單

1. 檢閱此文件的＜虛擬網路＞一節。
2. 使用匯出的虛擬網路設定在其他區域重新建立它。

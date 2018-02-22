---
title: "將內部部署資料解決方案延伸至雲端"
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 66fc225dc123202ba587d82f15ea0883e1bbf3b5
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2018
---
# <a name="extending-on-premises-data-solutions-to-the-cloud"></a>將內部部署資料解決方案延伸至雲端

當組織將工作負載和資料移至雲端時，其內部部署資料中心通常會繼續扮演重要角色。 「混合式雲端」一詞指的是公用雲端和內部部署資料中心的結合，結合目的是為了建立跨越二者的整合式 IT 環境。 某些組織會將混合式雲端作為隨時間慢慢將其整個資料中心移轉至雲端的途徑。 其他組織則會使用雲端服務來延伸其現有的內部部署基礎結構。 

本文說明在混合式雲端解決方案中管理資料的某些考量與最佳做法。

## <a name="when-to-use-a-hybrid-solution"></a>混合式解決方案的使用時機

遇到下列案例時，請考慮使用混合式解決方案：

* 作為長期移轉至完整雲端原生解決方案期間的轉換策略。
* 當法規或原則不允許將特定資料或工作負載移至雲端時。
* 為了獲得災害復原和容錯功能，方法是在內部部署與雲端環境之間複寫資料和服務。
* 為了減少內部部署資料中心與遠端位置之間的延遲，方法是在 Azure 中裝載架構的一部分。

## <a name="challenges"></a>挑戰

* 建立在安全性、管理和開發等方面皆保持一致的環境，並避免工作重複。

* 在內部部署與雲端環境之間建立可靠、低延遲且安全的資料連線。

* 複寫資料和修改應用程式與工具，以在每個環境中使用正確的資料存放區。

* 保護和加密裝載在雲端但從內部部署環境存取的資料，反之亦然。

## <a name="on-premises-data-stores"></a>內部部署資料存放區

內部部署資料存放區包括資料庫和檔案。 有幾個原因會讓您將這些項目保留在本機。 法規或原則可能不允許將特定資料或工作負載移至雲端。 資料主權、隱私權或安全性考量可能偏好置於內部部署環境。 在移轉期間，您可以將尚未移轉之應用程式的某些資料保存在本機。

在公用雲端中放置應用程式資料的考量事項包括：

* **成本**。 相較於在內部部署資料中心內具有類似特性之儲存體的維護成本，Azure 中的儲存體成本非常低。 當然，許多公司的資金目前已投資在高階 SAN，因此要等到現有硬體淘汰後，才能實現這些成本優勢。
* **彈性調整**。 要在內部部署環境中規劃及管理資料容量的成長並不容易，特別是當資料成長難以預測時。 這些應用程式可利用雲端所提供的隨選容量和幾乎無限的儲存體。 若應用程式所包含之資料集的大小不會有什麼變化，此考量就不是很重要了。
* **災害復原**。 儲存在 Azure 中的資料可在 Azure 區域內以及跨地理區域自動複寫。 在混合式環境中，同樣的技術也可用來在內部部署與雲端式資料存放區之間複寫資料。

## <a name="extending-data-stores-to-the-cloud"></a>將資料存放區延伸至雲端

有數個選項可將內部部署資料存放區延伸至雲端。 其中一個選項是製作內部部署和雲端複本。 這個方法可協助您實現優異的容錯功能，但可能需要變更應用程式，使其在發生容錯移轉時連線到適當的資料存放區。

另一個選項是將部分資料移到雲端儲存體，同時在內部部署環境保留更近期或更頻繁存取的資料。 這個方法能夠為長期儲存提供更符合成本效益的選項，以及藉由減少運作的資料集來改善資料存取回應時間。

第三個選項是在內部部署環境保留所有資料，但使用雲端運算來裝載應用程式。 若要這樣做，請將應用程式裝載在雲端，並透過安全連線將它連線到您的內部部署資料存放區。 

## <a name="azure-stack"></a>Azure Stack

如需完整的混合式雲端解決方案，請考慮使用 [Microsoft Azure Stack](/azure/azure-stack/)。 Azure Stack 是混合式雲端平台，可讓您從自己的資料中心提供 Azure 服務。 這可藉由使用相同的工具，在不需要變更任何程式碼的情況下，讓內部部署環境與 Azure 之間保有一致性。 

以下是 Azure 和 Azure Stack 的一些使用案例：

* **邊緣和離線解決方案**。 藉由在 Azure Stack 本機處理資料，然後在 Azure 中彙總以進一步分析，並在兩者之間使用通用應用程式邏輯，來解決延遲和連線需求。 
* **符合各種法規的雲端應用程式**。 在 Azure 中開發及部署應用程式，並有彈性可在 Azure Stack 內部部署相同的應用程式，以符合法規或原則需求。
* **內部部署雲端應用程式模型**。 使用 Azure 來更新及延伸現有應用程式或建置新的應用程式。 在雲端和 Azure Stack 內部部署環境中跨 Azure 使用一致的 DevOps 程序。

## <a name="sql-server-data-stores"></a>SQL Server 資料存放區

如果您要在內部部署環境執行 SQL Server，您可以使用 Microsoft Azure Blob 儲存體服務進行備份和還原。 如需詳細資訊，請參閱 [SQL Server 備份及還原與 Microsoft Azure Blob 儲存體服務](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)。 此功能可讓您無限制地異地儲存，並能夠在內部部署環境上所執行的 SQL Server 與在 Azure 虛擬機器中所執行的 SQL Server 之間共用相同的備份。 

[Azure SQL Database](/azure/sql-database/) 是受控的關聯式資料庫即服務。 由於 Azure SQL Database 使用 Microsoft SQL Server 引擎，因此應用程式可以相同的方式使用這兩種技術存取資料。 Azure SQL Database 也可透過實用方式來與 SQL Server 合併。 例如，[SQL Server Stretch Database](/sql/sql-server/stretch-database/stretch-database) 功能可讓應用程式存取 SQL Server 資料庫中類似單一資料表的項目，與此同時，該資料表的部分或所有資料列卻可能是儲存在 Azure SQL Database 中。 這項技術會自動將一段定義時間內未曾存取的資料移至雲端。 讀取此資料的應用程式不會察覺到已有資料移至雲端。

當您想要讓資料保持同步時，要在內部部署環境和雲端中保有資料存放區會很困難。 您可以使用 [SQL 資料同步](/azure/sql-database/sql-database-sync-data)來解決此問題，SQL 資料同步是建置在 Azure SQL Database 上的服務，可讓您跨多個 Azure SQL 資料庫和 SQL Server 執行個體，雙向同步處理您選取的資料。 雖然「資料同步」能讓您在這些不同的資料存放區上輕鬆保有最新的資料，但請勿將它用於災害復原或用於從內部部署 SQL Server 移轉至 Azure SQL Database。

如需災害復原和商務持續性功能，您可以使用 [AlwaysOn 可用性群組](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)，跨 SQL Server 的兩個或多個執行個體複寫資料，其中有些執行個體可以在另一個地理區域的 Azure 虛擬機器上執行。

## <a name="network-shares-and-file-based-data-stores"></a>網路共用和檔案型資料存放區

在混合式雲端架構中，組織通常會在內部部署環境保存較新的檔案，而將較舊的檔案封存至雲端。 這種情況有時稱為檔案階層處理，其可供您順暢存取分別是位於內部部署環境和裝載在雲端的兩組檔案。 這種方法有助於盡量降低可能最常存取之較新檔案的網路頻寬使用量和存取次數。 同時，可獲得使用雲端式儲存體來存放封存資料的優勢。 

組織也可以將其網路共用完全移至雲端。 比方說，如果存取網路共用的應用程式也位於雲端，就可以這麼做。 此程序即可使用[資料協調流程](../technology-choices/pipeline-orchestration-data-movement.md)工具來完成。


[Azure StorSimple](/azure/storsimple/) 提供了最完整的整合式儲存體解決方案，以供您管理內部部署裝置與 Azure 雲端儲存體之間的儲存工作。 StorSimple 是一個有效率、符合成本效益且易於管理的存放區域網路 (SAN) 解決方案，可減少許多與企業儲存體和資料保護相關聯的問題和支出。 它使用專屬的 StorSimple 8000 系列裝置、會與雲端服務整合，並提供一組整合式管理工具。

使用內部部署網路共用以及雲端式檔案儲存體的另一種方法是使用 [Azure 檔案服務](/azure/storage/files/storage-files-introduction)。 Azure 檔案服務會提供完全受控的檔案共用，您可使用標準的[伺服器訊息區](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx?f=255&MSPPError=-2147217396) (SMB) 通訊協定 (有時稱為 CIFS) 來加以存取。 您可以將 Azure 檔案服務掛接為本機電腦上的檔案共用，或與會存取本機或網路共用檔案的現有應用程式搭配使用。

若要讓 Azure 檔案服務中的檔案共用與內部部署 Windows 伺服器進行同步處理，請使用 [Azure 檔案同步](/azure/storage/files/storage-sync-files-planning)。Azure 檔案同步的一大好處是能夠在內部部署檔案伺服器與 Azure 檔案服務之間分層處理檔案。 這可讓您只在本機存放最新和最近存取的檔案。 

如需詳細資訊，請參閱[決定何時使用 Azure Blob 儲存體、Azure 檔案服務或 Azure 磁碟](/azure/storage/common/storage-decide-blobs-files-disks)。

## <a name="hybrid-networking"></a>混合式網路功能

本文將焦點放在混合式資料解決方案，但另一個考量是如何將內部部署網路延伸至 Azure。 如需混合式解決方案在這方面的詳細資訊，請參閱下列主題：

- [選擇用來將內部部署網路連線到 Azure 的解決方案](../../reference-architectures/hybrid-networking/considerations.md)
- [混合式網路參考架構](../../reference-architectures/hybrid-networking/index.md)


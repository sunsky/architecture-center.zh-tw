---
title: "從資料損毀或意外刪除復原"
description: "有關如何從意外損毀資料或刪除資料恢復資料，和設計可恢復、高可用性、容錯的應用程式，以及規劃災害復原的文章"
author: adamglick
ms.date: 08/18/2016
ms.openlocfilehash: b75c774f85c42f64472167897f08a7302ab50a3f
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
[!INCLUDE [header](../_includes/header.md)]
# <a name="azure-resiliency-technical-guidance-recovery-from-data-corruption-or-accidental-deletion"></a>Azure 復原技術指導：從資料損毀或意外刪除復原
健全的商務持續性計畫的一部分為對資料損毀或遭意外刪除有所計畫。 以下是因為應用程式錯誤或操作員錯誤而損毀或意外刪除資料之後，有關復原的資訊。

## <a name="virtual-machines"></a>虛擬機器
若要保護 Azure 虛擬機器 (有時稱為基礎結構即服務 VM) 不受應用程式錯誤或意外刪除，請使用 [Azure 備份](https://azure.microsoft.com/services/backup/)。 Azure 備份可讓您跨多個 VM 磁碟建立一致的備份。 此外，備份保存庫可以跨區域複寫，以從區域耗損提供復原。

## <a name="storage"></a>儲存體
請注意，雖然 Azure 儲存體透過自動化複本提供資料恢復，這無法防止您的應用程式程式碼 (或開發人員/使用者) 不會因為意外或非特意刪除、更新等而損毀資料。 維護應用程式或使用者錯誤的資料精確度需要更進階的技術，例如複製資料到具有稽核記錄檔的次要儲存體位置。 開發人員可以利用 Blob [快照集功能](https://msdn.microsoft.com/library/azure/ee691971.aspx)，這可以為 Blob 內容建立唯讀時間點快照集。 這可用來當做 Azure 儲存體 Blob 的資料精確度解決方案的基礎。

### <a name="blob-and-table-storage-backup"></a>Blob 和資料表儲存體備份
雖然 Blob 和資料表都很持久，但是它們一律會呈現資料的目前狀態。 從不想要的修改或刪除資料復原可能需要將資料還原到先前的狀態。 這可以利用 Azure 所提供用來儲存和保留時間點複本的功能來達成。

針對 Azure Blob，您可以使用 [Blob 快照集功能](https://msdn.microsoft.com/library/ee691971.aspx)執行時間點備份。 針對每個快照集，您只需支付自從上一個快照集狀態後，在 Blob 中儲存差異所需的儲存體。 快照集取決於其根據的原始 Blob，因此建議對另一個 Blob 或甚至是另一個儲存體帳戶進行複製作業。 這可確保該備份資料可正確地受到保護，防止被意外刪除。 對於 Azure 資料表，您可以對不同的資料表或 Azure Blob 建立時間點複本。 執行資料表和 Blob 應用程式層級備份的更詳細指導方針與範例可以在這裡找到：

* [保護您的資料表以免發生應用程式錯誤](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/05/03/protecting-your-tables-against-application-errors/)
* [保護您的 Blob 以免發生應用程式錯誤](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/04/29/protecting-your-blobs-against-application-errors/)

## <a name="database"></a>資料庫
有好幾種 [商務持續性](/azure/sql-database/sql-database-business-continuity/) (備份、還原) 選項可供 Azure SQL Database 使用。 透過[資料庫複製](/azure/sql-database/sql-database-copy/)功能，或透過[匯出](/azure/sql-database/sql-database-export/)與[匯入](https://msdn.microsoft.com/library/hh710052.aspx) SQL Server 的 bacpac 檔案即可複製資料庫。 資料庫複製可提供交易一致的結果，而 bacpac (透過匯入/匯出服務) 則不會。 這兩種選項都會在服務中心內以佇列為基礎的服務形式執行，並且目前不提供完成時間 SLA。

> [!NOTE]
> 資料庫複製和匯入/匯出選項對來源資料庫有極明顯程度的負載。 這些選項可能會觸發資源競爭或節流事件。
> 
> 

### <a name="sql-database-backup"></a>SQL Database 備份
Microsoft Azure SQL Database 的時間點備份，是透過 [複製您的 Azure SQL Database](/azure/sql-database/sql-database-copy/)達成。 您可以使用此命令對相同邏輯資料庫伺服器或不同伺服器建立資料庫的交易一致複本。 在任一情況下，資料庫複本完整可運作，且完全獨立於來源資料庫。 您所建立的每個複本代表一個時間點復原選項。 您可以將新的資料庫重新命名為來源資料庫名稱，藉以完全復原資料庫狀態。 或者，您可以使用 Transact-SQL 查詢，從新的資料庫復原特定資料子集。 如需 SQL Database 的其他詳細資訊，請參閱 [使用 Azure SQL Database 的商務持續性概觀](/azure/sql-database/sql-database-business-continuity/)。

### <a name="sql-server-on-virtual-machines-backup"></a>虛擬機器備份上的 SQL Server
針對做為服務虛擬機器用於 Azure 基礎結構的 SQL Server (通常稱為 IaaS 或 IaaS VM)，有兩個選項：傳統備份和記錄傳送。 使用傳統備份可讓您還原到特定時間點，但復原程序緩慢。 還原傳統備份需要從最初的完整備份開始，然後套用之後所建立的任何備份。 第二個選項是設定記錄傳送的工作階段，將記錄備份的還原延遲 (例如，兩個小時)。 這會提供從在主要伺服器上所造成的錯誤復原的時間。

## <a name="other-azure-platform-services"></a>其他 Azure 平台服務
某些 Azure 平台服務會將資訊儲存在使用者控制的儲存體帳戶或 Azure SQL Database。 如果帳戶或儲存體資源遭到刪除或損毀，這可能造成服務的嚴重錯誤。 在這些情況下，請務必維護備份，如果資料遭到刪除或損毀，便可讓您重新建立這些資源。

針對 Azure 網站和 Azure 行動服務，您必須備份及維護相關聯的資料庫。 針對 Azure 媒體服務和虛擬機器，您必須維護相關聯的 Azure 儲存體帳戶和該帳戶中的所有資源。 例如，針對虛擬機器，您必須在 Azure Blob 儲存體中備份和管理 VM 磁碟。

## <a name="checklists-for-data-corruption-or-accidental-deletion"></a>資料損毀或意外刪除的檢查清單
## <a name="virtual-machines-checklist"></a>虛擬機器檢查清單
1. 檢閱此文件的＜虛擬機器＞一節。
2. 使用 Azure 備份來備份及維護 VM 磁碟 (或使用 Azure Blob 儲存體和 VHD 的快照集之您自己的備份系統)。

## <a name="storage-checklist"></a>儲存體檢查清單
1. 檢閱此文件的＜儲存體＞一節。
2. 定期備份重要的儲存體資源。
3. 考慮對 Blob 使用快照集功能。

## <a name="database-checklist"></a>資料庫檢查清單
1. 檢閱此文件的＜資料庫＞一節。
2. 使用資料庫複製命令建立時間點備份。

## <a name="sql-server-on-virtual-machines-backup-checklist"></a>虛擬機器備份上的 SQL Server 檢查清單
1. 檢閱此文件的＜虛擬機器備份上的 SQL Server＞一節。
2. 使用傳統備份和還原技術。
3. 建立延後的記錄傳送工作階段。

## <a name="web-apps-checklist"></a>Web Apps 檢查清單
1. 備份及維護相關聯的資料庫，如果有的話。

## <a name="media-services-checklist"></a>媒體服務檢查清單
1. 備份及維護相關聯的儲存體資源。

## <a name="more-information"></a>詳細資訊
如需有關 Azure 的備份和還原功能的詳細資訊，請參閱 [儲存體、備份和復原案例](https://azure.microsoft.com/documentation/scenarios/storage-backup-recovery/)。



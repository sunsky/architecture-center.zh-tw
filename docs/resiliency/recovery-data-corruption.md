---
title: "從資料損毀或意外刪除復原"
description: "有關如何從意外損毀資料或刪除資料恢復資料，和設計可恢復、高可用性、容錯的應用程式，以及規劃災害復原的文章"
author: MikeWasson
ms.date: 01/10/2018
ms.openlocfilehash: 76d2f996750d5a67b67bd5dc4977580f3b8abbc3
ms.sourcegitcommit: 3d6dba524cc7661740bdbaf43870de7728d60a01
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2018
---
# <a name="recover-from-data-corruption-or-accidental-deletion"></a>從資料損毀或意外刪除復原 

健全的商務持續性計畫的一部分為對資料損毀或遭意外刪除有所計畫。 以下是因為應用程式錯誤或操作員錯誤而損毀或意外刪除資料之後，有關復原的資訊。

## <a name="virtual-machines"></a>虛擬機器

若要保護 Azure 虛擬機器 (VM) 不受應用程式錯誤影響或遭到意外刪除，請使用 [Azure 備份](/azure/backup/)。 Azure 備份可讓您跨多個 VM 磁碟建立一致的備份。 此外，備份保存庫可以跨區域複寫，以從區域耗損提供復原。

## <a name="storage"></a>儲存體

Azure 儲存體可透過自動化複本提供資料復原功能。 不過，這無法避免應用程式程式碼或使用者損毀資料 (無論是意外還是惡意地)。 維護應用程式或使用者錯誤的資料精確度需要更進階的技術，例如複製資料到具有稽核記錄檔的次要儲存體位置。 

- **區塊 Blob**。 建立每個區塊 Blob 的時間點快照集。 如需詳細資訊，請參閱 [建立 Blob 的快照集](/rest/api/storageservices/creating-a-snapshot-of-a-blob)。 針對每個快照集，您只需支付自從上一個快照集狀態後，在 Blob 中儲存差異所需的儲存體。 快照集取決於其根據的原始 Blob，因此建議對另一個 Blob 或甚至是另一個儲存體帳戶進行複製作業。 這可確保該備份資料可正確地受到保護，防止被意外刪除。 您可以使用 [AzCopy](/azure/storage/common/storage-use-azcopy) 或 [Azure PowerShell](/azure/storage/common/storage-powershell-guide-full)，將 Blob 複製到其他儲存體帳戶。

- **檔案**。 使用[共用快照集 (預覽)](/azure/storage/files/storage-how-to-use-files-snapshots)，或使用 AzCopy 或 Azure PowerShell 可將您的檔案複製到其他儲存體帳戶。

- **資料表**。 使用 AzCopy 可將資料表資料匯出到位於其他區域的其他儲存體帳戶。

## <a name="database"></a>資料庫

### <a name="azure-sql-database"></a>連接字串 

SQL Database 會每週自動執行完整資料庫備份、每小時自動執行差異資料庫備份，以及每 5 到 10 分鐘自動執行交易記錄備份，透過這樣的備份組合來防止您的企業遺失資料。 使用時間點還原可將資料庫還原到較早的時間。 如需詳細資訊，請參閱

- [使用自動資料庫備份復原 Azure SQL Database](/azure/sql-database/sql-database-recovery-using-backups)

- [使用 Azure SQL Database 的商務持續性概觀](/azure/sql-database/sql-database-business-continuity)

### <a name="sql-server-on-vms"></a>虛擬機器上的 SQL Server

對於在虛擬機器上執行的 SQL Server，有兩種選項：傳統備份和記錄傳送。 傳統備份可讓您還原到特定時間點，但復原程序緩慢。 還原傳統備份需要從最初的完整備份開始，然後套用之後所建立的任何備份。 第二個選項是設定記錄傳送的工作階段，將記錄備份的還原延遲 (例如，兩個小時)。 這會提供從在主要伺服器上所造成的錯誤復原的時間。

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB 可以定期自動進行備份。 備份會儲存在另一個儲存體服務中，而且系統會全域複寫這些備份，以便為區域性災害提供復原功能。 如果您不小心刪除資料庫或集合，您可以提出支援票證或連絡 Azure 支援，要求從最新的自動備份還原資料。 如需詳細資訊，請參閱[使用 Azure Cosmos DB 自動進行線上備份及還原](/azure/cosmos-db/online-backup-and-restore)。

### <a name="azure-database-for-mysql-azure-database-for-postresql"></a>適用於 MySQL 的 Azure 資料庫、適用於 PostreSQL 的 Azure 資料庫

使用適用於 MySQL 的 Azure 資料庫或適用於 PostreSQL 的 Azure 資料庫時，資料庫服務每隔五分鐘會自動備份一次服務。 透過這個自動備份功能，您可以將伺服器和其所有資料庫還原至新的伺服器，而且還原至更早的時間點。 如需詳細資訊，請參閱

- [如何使用 Azure 入口網站，在適用於 MySQL 的 Azure 資料庫中備份和還原伺服器](/azure/mysql/howto-restore-server-portal)

- [如何使用 Azure 入口網站，在適用於 PostreSQL 的 Azure 資料庫中備份和還原伺服器](/azure/postgresql/howto-restore-server-portal)


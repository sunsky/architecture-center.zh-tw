---
title: 從資料損毀或意外刪除復原
description: 了解如何從意外損毀資料或刪除資料恢復資料，和設計可恢復、高可用性、容錯的應用程式，以及規劃災害復原。
author: MikeWasson
ms.date: 11/11/2018
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.custom: resiliency
ms.openlocfilehash: e28f26683c6d7dba196d4351ef3942830c9e7fc2
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58242779"
---
# <a name="recover-from-data-corruption-or-accidental-deletion"></a><span data-ttu-id="80da0-103">從資料損毀或意外刪除復原</span><span class="sxs-lookup"><span data-stu-id="80da0-103">Recover from data corruption or accidental deletion</span></span>

<span data-ttu-id="80da0-104">健全的商務持續性計畫的一部分為對資料損毀或遭意外刪除有所計畫。</span><span class="sxs-lookup"><span data-stu-id="80da0-104">Part of a robust business continuity plan is having a plan if your data gets corrupted or accidentally deleted.</span></span> <span data-ttu-id="80da0-105">以下是因為應用程式錯誤或操作員錯誤而損毀或意外刪除資料之後，有關復原的資訊。</span><span class="sxs-lookup"><span data-stu-id="80da0-105">The following is information about recovery after data has been corrupted or accidentally deleted, due to application errors or operator error.</span></span>

## <a name="virtual-machines"></a><span data-ttu-id="80da0-106">虛擬機器</span><span class="sxs-lookup"><span data-stu-id="80da0-106">Virtual Machines</span></span>

<span data-ttu-id="80da0-107">若要保護 Azure 虛擬機器 (VM) 不受應用程式錯誤影響或遭到意外刪除，請使用 [Azure 備份](/azure/backup/)。</span><span class="sxs-lookup"><span data-stu-id="80da0-107">To protect Azure Virtual Machines (VMs) from application errors or accidental deletion, use [Azure Backup](/azure/backup/).</span></span> <span data-ttu-id="80da0-108">Azure 備份可讓您跨多個 VM 磁碟建立一致的備份。</span><span class="sxs-lookup"><span data-stu-id="80da0-108">Azure Backup enables the creation of backups that are consistent across multiple VM disks.</span></span> <span data-ttu-id="80da0-109">此外，備份保存庫可以跨區域複寫，以從區域耗損提供復原。</span><span class="sxs-lookup"><span data-stu-id="80da0-109">In addition, the Backup Vault can be replicated across regions to provide recovery from region loss.</span></span>

## <a name="storage"></a><span data-ttu-id="80da0-110">儲存體</span><span class="sxs-lookup"><span data-stu-id="80da0-110">Storage</span></span>

<span data-ttu-id="80da0-111">Azure 儲存體可透過自動化複本提供資料復原功能。</span><span class="sxs-lookup"><span data-stu-id="80da0-111">Azure Storage provides data resiliency through automated replicas.</span></span> <span data-ttu-id="80da0-112">不過，這無法避免應用程式程式碼或使用者損毀資料 (無論是意外還是惡意地)。</span><span class="sxs-lookup"><span data-stu-id="80da0-112">However, this does not prevent application code or users from corrupting data, whether accidentally or maliciously.</span></span> <span data-ttu-id="80da0-113">維護應用程式或使用者錯誤的資料精確度需要更進階的技術，例如複製資料到具有稽核記錄檔的次要儲存體位置。</span><span class="sxs-lookup"><span data-stu-id="80da0-113">Maintaining data fidelity in the face of application or user error requires more advanced techniques, such as copying the data to a secondary storage location with an audit log.</span></span>

- <span data-ttu-id="80da0-114">**區塊 Blob**。</span><span class="sxs-lookup"><span data-stu-id="80da0-114">**Block blobs**.</span></span> <span data-ttu-id="80da0-115">建立每個區塊 Blob 的時間點快照集。</span><span class="sxs-lookup"><span data-stu-id="80da0-115">Create a point-in-time snapshot of each block blob.</span></span> <span data-ttu-id="80da0-116">如需詳細資訊，請參閱 [建立 Blob 的快照集](/rest/api/storageservices/creating-a-snapshot-of-a-blob)。</span><span class="sxs-lookup"><span data-stu-id="80da0-116">For more information, see [Creating a Snapshot of a Blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob).</span></span> <span data-ttu-id="80da0-117">針對每個快照集，您只需支付自從上一個快照集狀態後，在 Blob 中儲存差異所需的儲存體。</span><span class="sxs-lookup"><span data-stu-id="80da0-117">For each snapshot, you are only charged for the storage required to store the differences within the blob since the last snapshot state.</span></span> <span data-ttu-id="80da0-118">快照集取決於其根據的原始 Blob，因此建議對另一個 Blob 或甚至是另一個儲存體帳戶進行複製作業。</span><span class="sxs-lookup"><span data-stu-id="80da0-118">The snapshots are dependent on the existence of the original blob they are based on, so a copy operation to another blob or even another storage account is advisable.</span></span> <span data-ttu-id="80da0-119">這可確保該備份資料可正確地受到保護，防止被意外刪除。</span><span class="sxs-lookup"><span data-stu-id="80da0-119">This ensures that backup data is properly protected against accidental deletion.</span></span> <span data-ttu-id="80da0-120">您可以使用 [AzCopy](/azure/storage/common/storage-use-azcopy) 或 [Azure PowerShell](/azure/storage/common/storage-powershell-guide-full)，將 Blob 複製到其他儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="80da0-120">You can use [AzCopy](/azure/storage/common/storage-use-azcopy) or [Azure PowerShell](/azure/storage/common/storage-powershell-guide-full) to copy the blobs to another storage account.</span></span>

- <span data-ttu-id="80da0-121">**檔案**。</span><span class="sxs-lookup"><span data-stu-id="80da0-121">**Files**.</span></span> <span data-ttu-id="80da0-122">使用[共用快照集](/azure/storage/files/storage-snapshots-files)或使用 AzCopy 或 Azure PowerShell，可將您的檔案複製到其他儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="80da0-122">Use [share snapshots](/azure/storage/files/storage-snapshots-files), or use AzCopy or PowerShell to copy your files to another storage account.</span></span>

- <span data-ttu-id="80da0-123">**資料表**。</span><span class="sxs-lookup"><span data-stu-id="80da0-123">**Tables**.</span></span> <span data-ttu-id="80da0-124">使用 AzCopy 可將資料表資料匯出到位於其他區域的其他儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="80da0-124">Use AzCopy to export the table data into another storage account in another region.</span></span>

## <a name="database"></a><span data-ttu-id="80da0-125">資料庫</span><span class="sxs-lookup"><span data-stu-id="80da0-125">Database</span></span>

### <a name="azure-sql-database"></a><span data-ttu-id="80da0-126">連接字串</span><span class="sxs-lookup"><span data-stu-id="80da0-126">Azure SQL Database</span></span>

<span data-ttu-id="80da0-127">SQL Database 會每週自動執行完整資料庫備份、每小時自動執行差異資料庫備份，以及每 5 到 10 分鐘自動執行交易記錄備份，透過這樣的備份組合來防止您的企業遺失資料。</span><span class="sxs-lookup"><span data-stu-id="80da0-127">SQL Database automatically performs a combination of full database backups weekly, differential database backups hourly, and transaction log backups every five - ten minutes to protect your business from data loss.</span></span> <span data-ttu-id="80da0-128">使用時間點還原可將資料庫還原到較早的時間。</span><span class="sxs-lookup"><span data-stu-id="80da0-128">Use point-in-time restore to restore a database to an earlier time.</span></span> <span data-ttu-id="80da0-129">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="80da0-129">For more information, see:</span></span>

- [<span data-ttu-id="80da0-130">使用自動資料庫備份復原 Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="80da0-130">Recover an Azure SQL database using automated database backups</span></span>](/azure/sql-database/sql-database-recovery-using-backups)

- [<span data-ttu-id="80da0-131">使用 Azure SQL Database 的商務持續性概觀</span><span class="sxs-lookup"><span data-stu-id="80da0-131">Overview of business continuity with Azure SQL Database</span></span>](/azure/sql-database/sql-database-business-continuity)

### <a name="sql-server-on-vms"></a><span data-ttu-id="80da0-132">虛擬機器上的 SQL Server</span><span class="sxs-lookup"><span data-stu-id="80da0-132">SQL Server on VMs</span></span>

<span data-ttu-id="80da0-133">對於在虛擬機器上執行的 SQL Server，有兩種選項：傳統備份和記錄傳送。</span><span class="sxs-lookup"><span data-stu-id="80da0-133">For SQL Server running on VMs, there are two options: traditional backups and log shipping.</span></span> <span data-ttu-id="80da0-134">傳統備份可讓您還原到特定時間點，但復原程序緩慢。</span><span class="sxs-lookup"><span data-stu-id="80da0-134">Traditional backups enables you to restore to a specific point in time, but the recovery process is slow.</span></span> <span data-ttu-id="80da0-135">還原傳統備份需要從最初的完整備份開始，然後套用之後所建立的任何備份。</span><span class="sxs-lookup"><span data-stu-id="80da0-135">Restoring traditional backups requires starting with an initial full backup, and then applying any backups taken after that.</span></span> <span data-ttu-id="80da0-136">第二個選項是設定記錄傳送的工作階段，將記錄備份的還原延遲 (例如，兩個小時)。</span><span class="sxs-lookup"><span data-stu-id="80da0-136">The second option is to configure a log shipping session to delay the restore of log backups (for example, by two hours).</span></span> <span data-ttu-id="80da0-137">這會提供從在主要伺服器上所造成的錯誤復原的時間。</span><span class="sxs-lookup"><span data-stu-id="80da0-137">This provides a window to recover from errors made on the primary.</span></span>

### <a name="azure-cosmos-db"></a><span data-ttu-id="80da0-138">Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="80da0-138">Azure Cosmos DB</span></span>

<span data-ttu-id="80da0-139">Azure Cosmos DB 可以定期自動進行備份。</span><span class="sxs-lookup"><span data-stu-id="80da0-139">Azure Cosmos DB automatically takes backups at regular intervals.</span></span> <span data-ttu-id="80da0-140">備份會儲存在另一個儲存體服務中，而且系統會全域複寫這些備份，以便為區域性災害提供復原功能。</span><span class="sxs-lookup"><span data-stu-id="80da0-140">Backups are stored separately in another storage service, and those backups are globally replicated for resiliency against regional disasters.</span></span> <span data-ttu-id="80da0-141">如果您不小心刪除資料庫或集合，您可以提出支援票證或連絡 Azure 支援，要求從最新的自動備份還原資料。</span><span class="sxs-lookup"><span data-stu-id="80da0-141">If you accidentally delete your database or collection, you can file a support ticket or call Azure support to restore the data from the last automatic backup.</span></span> <span data-ttu-id="80da0-142">如需詳細資訊，請參閱[使用 Azure Cosmos DB 自動進行線上備份及還原](/azure/cosmos-db/online-backup-and-restore)。</span><span class="sxs-lookup"><span data-stu-id="80da0-142">For more information, see [Automatic online backup and restore with Azure Cosmos DB](/azure/cosmos-db/online-backup-and-restore).</span></span>

### <a name="azure-database-for-mysql-azure-database-for-postgresql"></a><span data-ttu-id="80da0-143">適用於 MySQL 的 Azure 資料庫、適用於 PostgreSQL 的 Azure 資料庫</span><span class="sxs-lookup"><span data-stu-id="80da0-143">Azure Database for MySQL, Azure Database for PostgreSQL</span></span>

<span data-ttu-id="80da0-144">使用適用於 MySQL 的 Azure 資料庫或適用於 PostgreSQL 的 Azure 資料庫時，資料庫服務每隔五分鐘會自動備份一次服務。</span><span class="sxs-lookup"><span data-stu-id="80da0-144">When using Azure Database for MySQL or Azure Database for PostgreSQL, the database service automatically makes a backup of the service every five minutes.</span></span> <span data-ttu-id="80da0-145">透過這個自動備份功能，您可以將伺服器和其所有資料庫還原至新的伺服器，而且還原至更早的時間點。</span><span class="sxs-lookup"><span data-stu-id="80da0-145">Using this automatic backup feature you may restore the server and all its databases into a new server to an earlier point-in-time.</span></span> <span data-ttu-id="80da0-146">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="80da0-146">For more information, see:</span></span>

- [<span data-ttu-id="80da0-147">如何使用 Azure 入口網站，在適用於 MySQL 的 Azure 資料庫中備份和還原伺服器</span><span class="sxs-lookup"><span data-stu-id="80da0-147">How to back up and restore a server in Azure Database for MySQL by using the Azure portal</span></span>](/azure/mysql/howto-restore-server-portal)

- [<span data-ttu-id="80da0-148">如何使用 Azure 入口網站，在適用於 PostreSQL 的 Azure 資料庫中備份和還原伺服器</span><span class="sxs-lookup"><span data-stu-id="80da0-148">How to backup and restore a server in Azure Database for PostgreSQL using the Azure portal</span></span>](/azure/postgresql/howto-restore-server-portal)

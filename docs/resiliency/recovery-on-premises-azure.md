---
title: 技術指導：從內部部署復原至 Azure
description: 了解和設計從內部部署基礎結構復原至 Azure 的復原系統的文章
author: adamglick
ms.date: 08/18/2016
ms.openlocfilehash: 6992e27d148074b3d60c282318741f45974d1afd
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
ms.locfileid: "30847810"
---
[!INCLUDE [header](../_includes/header.md)]

# <a name="azure-resiliency-technical-guidance-recovery-from-on-premises-to-azure"></a><span data-ttu-id="c1c91-103">Azure 復原技術指導：從內部部署復原至 Azure</span><span class="sxs-lookup"><span data-stu-id="c1c91-103">Azure resiliency technical guidance: Recovery from on-premises to Azure</span></span>
<span data-ttu-id="c1c91-104">Azure 提供一組完整的服務，可針對高可用性和災害復原用途，啟用從內部部署資料中心至 Azure 的擴充︰</span><span class="sxs-lookup"><span data-stu-id="c1c91-104">Azure provides a comprehensive set of services for enabling the extension of an on-premises datacenter to Azure for high availability and disaster recovery purposes:</span></span>

* <span data-ttu-id="c1c91-105">**網路**︰您可以使用虛擬私人網路，將內部部署網路安全地擴充至雲端。</span><span class="sxs-lookup"><span data-stu-id="c1c91-105">**Networking**: With a virtual private network, you securely extend your on-premises network to the cloud.</span></span>
* <span data-ttu-id="c1c91-106">**計算**︰使用 Hyper-V 內部部署的客戶可以將現有虛擬機器 (VM)「提升並移轉」至 Azure。</span><span class="sxs-lookup"><span data-stu-id="c1c91-106">**Compute**: Customers using Hyper-V on-premises can “lift and shift” existing virtual machines (VMs) to Azure.</span></span>
* <span data-ttu-id="c1c91-107">**儲存體**：StorSimple 會將您的檔案系統擴充至 Azure 儲存體。</span><span class="sxs-lookup"><span data-stu-id="c1c91-107">**Storage**: StorSimple extends your file system to Azure Storage.</span></span> <span data-ttu-id="c1c91-108">Azure 備份服務會將檔案和 SQL Database 備份至 Azure 儲存體。</span><span class="sxs-lookup"><span data-stu-id="c1c91-108">The Azure Backup service provides backup for files and SQL databases to Azure Storage.</span></span>
* <span data-ttu-id="c1c91-109">**資料庫複寫**︰您可以使用 SQL Server 2014 (或更新版本) 可用性群組，為您的內部部署資料實作高可用性和災害復原。</span><span class="sxs-lookup"><span data-stu-id="c1c91-109">**Database replication**: With SQL Server 2014 (or later) Availability Groups, you can implement high availability and disaster recovery for your on-premises data.</span></span>

## <a name="networking"></a><span data-ttu-id="c1c91-110">網路</span><span class="sxs-lookup"><span data-stu-id="c1c91-110">Networking</span></span>
<span data-ttu-id="c1c91-111">您可以使用 Azure 虛擬網路在 Azure 中建立邏輯隔離區段，並透過 IPsec 連線，安全地將其連接到內部部署資料中心或單一用戶端機器。</span><span class="sxs-lookup"><span data-stu-id="c1c91-111">You can use Azure Virtual Network to create a logically isolated section in Azure and securely connect it to your on-premises datacenter or a single client machine by using an IPsec connection.</span></span> <span data-ttu-id="c1c91-112">透過虛擬網路，您可以運用 Azure 的可調整的隨選基礎結構，同時提供連線至內部部署資料和應用程式的能力，包括 Windows Server、大型主機及 UNIX 上執行的系統。</span><span class="sxs-lookup"><span data-stu-id="c1c91-112">With Virtual Network, you can take advantage of the scalable, on-demand infrastructure in Azure while providing connectivity to data and applications on-premises, including systems running on Windows Server, mainframes, and UNIX.</span></span> <span data-ttu-id="c1c91-113">如需詳細資訊，請參閱 [Azure 網路文件](/azure/virtual-network/virtual-networks-overview/) 。</span><span class="sxs-lookup"><span data-stu-id="c1c91-113">See [Azure networking documentation](/azure/virtual-network/virtual-networks-overview/) for more information.</span></span>

## <a name="compute"></a><span data-ttu-id="c1c91-114">計算</span><span class="sxs-lookup"><span data-stu-id="c1c91-114">Compute</span></span>
<span data-ttu-id="c1c91-115">如果您在內部部署使用 Hyper-V，則可以將現有的虛擬機器「提升並移轉」至執行 Windows Server 2012 (或更新版本) 的 Azure 和服務提供者，而不用變更 VM 或轉換 VM 格式。</span><span class="sxs-lookup"><span data-stu-id="c1c91-115">If you're using Hyper-V on-premises, you can “lift and shift” existing virtual machines to Azure and service providers running Windows Server 2012 (or later), without making changes to the VM or converting VM formats.</span></span> <span data-ttu-id="c1c91-116">如需詳細資訊，請參閱[關於 Azure 虛擬機器的磁碟與 VHD](/azure/virtual-machines/virtual-machines-linux-about-disks-vhds/?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。</span><span class="sxs-lookup"><span data-stu-id="c1c91-116">For more information, see [About disks and VHDs for Azure virtual machines](/azure/virtual-machines/virtual-machines-linux-about-disks-vhds/?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).</span></span>

## <a name="azure-site-recovery"></a><span data-ttu-id="c1c91-117">Azure Site Recovery</span><span class="sxs-lookup"><span data-stu-id="c1c91-117">Azure Site Recovery</span></span>
<span data-ttu-id="c1c91-118">如果您想要災害復原即服務 (DRaaS)，Azure 提供 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)。</span><span class="sxs-lookup"><span data-stu-id="c1c91-118">If you want disaster recovery as a service (DRaaS), Azure provides [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/).</span></span> <span data-ttu-id="c1c91-119">Azure Site Recovery 為 VMware、Hyper-V 與實體伺服器提供全面性的保護。</span><span class="sxs-lookup"><span data-stu-id="c1c91-119">Azure Site Recovery offers comprehensive protection for VMware, Hyper-V, and physical servers.</span></span> <span data-ttu-id="c1c91-120">透過 Azure Site Recovery，您可以使用另一部內部部署伺服器或 Azure 做為復原網站。</span><span class="sxs-lookup"><span data-stu-id="c1c91-120">With Azure Site Recovery, you can use another on-premises server or Azure as your recovery site.</span></span> <span data-ttu-id="c1c91-121">如需 Azure Site Recovery 的詳細資訊，請參閱 [Azure Site Recovery 文件](https://azure.microsoft.com/documentation/services/site-recovery/)。</span><span class="sxs-lookup"><span data-stu-id="c1c91-121">For more information on Azure Site Recovery, see the [Azure Site Recovery documentation](https://azure.microsoft.com/documentation/services/site-recovery/).</span></span>

## <a name="storage"></a><span data-ttu-id="c1c91-122">儲存體</span><span class="sxs-lookup"><span data-stu-id="c1c91-122">Storage</span></span>
<span data-ttu-id="c1c91-123">有數個選項可以使用 Azure 做為內部部署資料的備份網站。</span><span class="sxs-lookup"><span data-stu-id="c1c91-123">There are several options for using Azure as a backup site for on-premises data.</span></span>

### <a name="storsimple"></a><span data-ttu-id="c1c91-124">StorSimple</span><span class="sxs-lookup"><span data-stu-id="c1c91-124">StorSimple</span></span>
<span data-ttu-id="c1c91-125">StorSimple 安全而明確地整合內部部署應用程式的雲端儲存體。</span><span class="sxs-lookup"><span data-stu-id="c1c91-125">StorSimple securely and transparently integrates cloud storage for on-premises applications.</span></span> <span data-ttu-id="c1c91-126">它也提供單一的應用裝置，提供高效能分層式的本機和雲端儲存體、即時封存、雲端型資料保護和災害復原。</span><span class="sxs-lookup"><span data-stu-id="c1c91-126">It also offers a single appliance that delivers high-performance tiered local and cloud storage, live archiving, cloud-based data protection, and disaster recovery.</span></span> <span data-ttu-id="c1c91-127">如需詳細資訊，請參閱 [StorSimple 產品頁面](https://azure.microsoft.com/services/storsimple/)。</span><span class="sxs-lookup"><span data-stu-id="c1c91-127">For more information, see the [StorSimple product page](https://azure.microsoft.com/services/storsimple/).</span></span>

### <a name="azure-backup"></a><span data-ttu-id="c1c91-128">Azure 備份</span><span class="sxs-lookup"><span data-stu-id="c1c91-128">Azure Backup</span></span>
<span data-ttu-id="c1c91-129">Azure 備份可以在 Windows Server 2012 (或更新版本)、Windows Server 2012 Essentials (或更新版本) 和System Center 2012 Data Protection Manager (或更新版本) 中利用熟悉的備份工具啟用雲端備份。</span><span class="sxs-lookup"><span data-stu-id="c1c91-129">Azure Backup enables cloud backups by using the familiar backup tools in Windows Server 2012 (or later), Windows Server 2012 Essentials (or later), and System Center 2012 Data Protection Manager (or later).</span></span> <span data-ttu-id="c1c91-130">這些工具提供備份管理的工作流程，不受備份的儲存體位置的影響，無論是本機磁碟或 Azure 儲存體。</span><span class="sxs-lookup"><span data-stu-id="c1c91-130">These tools provide a workflow for backup management that is independent of the storage location of the backups, whether a local disk or Azure Storage.</span></span> <span data-ttu-id="c1c91-131">在將資料備份到雲端之後，授權使用者可以輕易地將備份復原至任何伺服器。</span><span class="sxs-lookup"><span data-stu-id="c1c91-131">After data is backed up to the cloud, authorized users can easily recover backups to any server.</span></span>

<span data-ttu-id="c1c91-132">之後進行增量備份時，只有變更的檔案才會傳輸到雲端，</span><span class="sxs-lookup"><span data-stu-id="c1c91-132">With incremental backups, only changes to files are transferred to the cloud.</span></span> <span data-ttu-id="c1c91-133">如此可協助有效使用儲存體空間、降低頻寬耗用，並且支援對多個版本的資料執行時間點復原。</span><span class="sxs-lookup"><span data-stu-id="c1c91-133">This helps to efficiently use storage space, reduce bandwidth consumption, and support point-in-time recovery of multiple versions of the data.</span></span> <span data-ttu-id="c1c91-134">您也可以選擇使用其他功能，例如資料保留原則、資料壓縮和資料傳輸節流。</span><span class="sxs-lookup"><span data-stu-id="c1c91-134">You can also choose to use additional features, such as data retention policies, data compression, and data transfer throttling.</span></span> <span data-ttu-id="c1c91-135">使用 Azure 做為備份位置有明顯的優點，備份會自動在「異地」處理。</span><span class="sxs-lookup"><span data-stu-id="c1c91-135">Using Azure as the backup location has the obvious advantage that the backups are automatically “offsite”.</span></span> <span data-ttu-id="c1c91-136">這樣就能排除保護本地備份媒體的額外需求。</span><span class="sxs-lookup"><span data-stu-id="c1c91-136">This eliminates the extra requirements to secure and protect on-site backup media.</span></span>

<span data-ttu-id="c1c91-137">如需詳細資訊，請參閱[何謂 Azure 備份？](/azure/backup/backup-introduction-to-azure-backup/)和[設定 DPM 資料的 Azure 備份](https://technet.microsoft.com/library/jj728752.aspx)。</span><span class="sxs-lookup"><span data-stu-id="c1c91-137">For more information, see [What is Azure Backup?](/azure/backup/backup-introduction-to-azure-backup/) and [Configure Azure Backup for DPM data](https://technet.microsoft.com/library/jj728752.aspx).</span></span>

## <a name="database"></a><span data-ttu-id="c1c91-138">資料庫</span><span class="sxs-lookup"><span data-stu-id="c1c91-138">Database</span></span>
<span data-ttu-id="c1c91-139">您可以使用 AlwaysOn 可用性群組、資料庫鏡像及記錄傳送，為混合式 IT 環境中的 SQL Server 資料庫提供災害復原解決方案，以及使用 Azure Blob 儲存體進行備份和還原。</span><span class="sxs-lookup"><span data-stu-id="c1c91-139">You can have a disaster recovery solution for your SQL Server databases in a hybrid-IT environment by using AlwaysOn Availability Groups, database mirroring, log shipping, and backup and restore with Azure Blob storage.</span></span> <span data-ttu-id="c1c91-140">這些解決方案都使用在 Azure 虛擬機器上執行的 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="c1c91-140">All of these solutions use SQL Server running on Azure Virtual Machines.</span></span>

<span data-ttu-id="c1c91-141">AlwaysOn 可用性群組可以在混合式 IT 環境中使用，在該環境中資料庫複本同時存在於內部部署和雲端中。</span><span class="sxs-lookup"><span data-stu-id="c1c91-141">AlwaysOn Availability Groups can be used in a hybrid-IT environment where database replicas exist both on-premises and in the cloud.</span></span> <span data-ttu-id="c1c91-142">如下圖所示。</span><span class="sxs-lookup"><span data-stu-id="c1c91-142">This is shown in the following diagram.</span></span>

![混合式雲端架構中的 SQL Server AlwaysOn 可用性群組](./images/technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-3.png)

<span data-ttu-id="c1c91-144">資料庫鏡像也能橫跨憑證式安裝中的內部部署伺服器和雲端。</span><span class="sxs-lookup"><span data-stu-id="c1c91-144">Database mirroring can also span on-premises servers and the cloud in a certificate-based setup.</span></span> <span data-ttu-id="c1c91-145">下圖說明此概念。</span><span class="sxs-lookup"><span data-stu-id="c1c91-145">The following diagram illustrates this concept.</span></span>

![混合式雲端架構中的 SQL Server Database 鏡像](./images/technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-4.png)

<span data-ttu-id="c1c91-147">記錄傳送可以用來同步處理內部部署資料庫與 Azure 虛擬機器中的 SQL Server 資料庫。</span><span class="sxs-lookup"><span data-stu-id="c1c91-147">Log shipping can be used to synchronize an on-premises database with a SQL Server database in an Azure virtual machine.</span></span>

![混合式雲端架構中的 SQL Server 記錄傳送](./images/technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-5.png)

<span data-ttu-id="c1c91-149">最後，您可以直接將內部部署資料庫備份至 Azure Blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="c1c91-149">Finally, you can back up an on-premises database directly to Azure Blob storage.</span></span>

![在混合式雲端架構中將 SQL Server 備份至 Azure Blob 儲存體](./images/technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-6.png)

<span data-ttu-id="c1c91-151">如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 高可用性和災害復原](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr/)和 [Azure 虛擬機器中的 SQL Server 備份和還原](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery/)。</span><span class="sxs-lookup"><span data-stu-id="c1c91-151">For more information, see [High availability and disaster recovery for SQL Server in Azure virtual machines](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr/) and [Backup and restore for SQL Server in Azure virtual machines](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery/).</span></span>

## <a name="checklists-for-on-premises-recovery-in-microsoft-azure"></a><span data-ttu-id="c1c91-152">Microsoft Azure 中的內部部署復原的檢查清單</span><span class="sxs-lookup"><span data-stu-id="c1c91-152">Checklists for on-premises recovery in Microsoft Azure</span></span>
### <a name="networking"></a><span data-ttu-id="c1c91-153">網路</span><span class="sxs-lookup"><span data-stu-id="c1c91-153">Networking</span></span>
1. <span data-ttu-id="c1c91-154">檢閱此文件的＜網路＞一節。</span><span class="sxs-lookup"><span data-stu-id="c1c91-154">Review the Networking section of this document.</span></span>
2. <span data-ttu-id="c1c91-155">使用虛擬網路將內部部署安全地連接至雲端。</span><span class="sxs-lookup"><span data-stu-id="c1c91-155">Use Virtual Network to securely connect on-premises to the cloud.</span></span>

### <a name="compute"></a><span data-ttu-id="c1c91-156">計算</span><span class="sxs-lookup"><span data-stu-id="c1c91-156">Compute</span></span>
1. <span data-ttu-id="c1c91-157">檢閱此文件的＜計算＞一節。</span><span class="sxs-lookup"><span data-stu-id="c1c91-157">Review the Compute section of this document.</span></span>
2. <span data-ttu-id="c1c91-158">在 Hyper-V 與 Azure 之間重新放置 VM。</span><span class="sxs-lookup"><span data-stu-id="c1c91-158">Relocate VMs between Hyper-V and Azure.</span></span>

### <a name="storage"></a><span data-ttu-id="c1c91-159">儲存體</span><span class="sxs-lookup"><span data-stu-id="c1c91-159">Storage</span></span>
1. <span data-ttu-id="c1c91-160">檢閱此文件的＜儲存體＞一節。</span><span class="sxs-lookup"><span data-stu-id="c1c91-160">Review the Storage section of this document.</span></span>
2. <span data-ttu-id="c1c91-161">利用 StorSimple 服務以使用雲端儲存體。</span><span class="sxs-lookup"><span data-stu-id="c1c91-161">Take advantage of StorSimple services for using cloud storage.</span></span>
3. <span data-ttu-id="c1c91-162">使用 Azure 備份服務。</span><span class="sxs-lookup"><span data-stu-id="c1c91-162">Use the Azure Backup service.</span></span>

### <a name="database"></a><span data-ttu-id="c1c91-163">資料庫</span><span class="sxs-lookup"><span data-stu-id="c1c91-163">Database</span></span>
1. <span data-ttu-id="c1c91-164">檢閱此文件的＜資料庫＞一節。</span><span class="sxs-lookup"><span data-stu-id="c1c91-164">Review the Database section of this document.</span></span>
2. <span data-ttu-id="c1c91-165">考量使用 Azure VM 上的 SQL Server 做為備份。</span><span class="sxs-lookup"><span data-stu-id="c1c91-165">Consider using SQL Server on Azure VMs as the backup.</span></span>
3. <span data-ttu-id="c1c91-166">設定 AlwaysOn 可用性群組。</span><span class="sxs-lookup"><span data-stu-id="c1c91-166">Set up AlwaysOn Availability Groups.</span></span>
4. <span data-ttu-id="c1c91-167">設定憑證式資料庫鏡像。</span><span class="sxs-lookup"><span data-stu-id="c1c91-167">Configure certificate-based database mirroring.</span></span>
5. <span data-ttu-id="c1c91-168">使用記錄傳送。</span><span class="sxs-lookup"><span data-stu-id="c1c91-168">Use log shipping.</span></span>
6. <span data-ttu-id="c1c91-169">將內部部署資料庫備份至 Azure Blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="c1c91-169">Back up on-premises databases to Azure Blob storage.</span></span>



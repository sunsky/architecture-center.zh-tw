---
title: 在 Azure 上執行 Windows VM
description: 如何在 Azure 上執行 Windows VM，並注意延展性、恢復能力、管理性和安全性。
author: telmosampaio
ms.date: 04/03/2018
pnp.series.title: Windows VM workloads
pnp.series.next: multi-vm
pnp.series.prev: ./index
ms.openlocfilehash: 9bc0b4af56b9194fd1bec8a189c86963ad2b0c98
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="run-a-windows-vm-on-azure"></a><span data-ttu-id="2d6e1-103">在 Azure 上執行 Windows VM</span><span class="sxs-lookup"><span data-stu-id="2d6e1-103">Run a Windows VM on Azure</span></span>

<span data-ttu-id="2d6e1-104">此參考架構顯示一組經過證實的作法，可用來在 Azure 上執行 Windows 虛擬機器 (VM)。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-104">This reference architecture shows a set of proven practices for running a Windows virtual machine (VM) on Azure.</span></span> <span data-ttu-id="2d6e1-105">其中包括適用於佈建 VM，以及網路和存放元件的建議。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-105">It includes recommendations for provisioning the VM along with networking and storage components.</span></span> <span data-ttu-id="2d6e1-106">此架構可用來執行單一 VM 執行個體，而且是適用於更複雜架構 (例如多層式架構應用程式) 的基礎。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-106">This architecture can be used to run a single VM instance, and is the basis for more complex architectures such as N-tier applications.</span></span> [<span data-ttu-id="2d6e1-107">**部署這個解決方案。**</span><span class="sxs-lookup"><span data-stu-id="2d6e1-107">**Deploy this solution.**</span></span>](#deploy-the-solution)

<span data-ttu-id="2d6e1-108">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="2d6e1-108">![[0]][0]</span></span>

<span data-ttu-id="2d6e1-109">*下載包含此架構圖表的 [Visio 檔案][visio-download]。*</span><span class="sxs-lookup"><span data-stu-id="2d6e1-109">*Download a [Visio file][visio-download] that contains this architecture diagram.*</span></span>

## <a name="architecture"></a><span data-ttu-id="2d6e1-110">架構</span><span class="sxs-lookup"><span data-stu-id="2d6e1-110">Architecture</span></span>

<span data-ttu-id="2d6e1-111">除了 VM 本身，佈建 Azure VM 還需要額外的元件，包括網路功能和儲存體資源。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-111">Provisioning an Azure VM requires some additional components besides the VM itself, including networking and storage resources.</span></span>

* <span data-ttu-id="2d6e1-112">**資源群組。**</span><span class="sxs-lookup"><span data-stu-id="2d6e1-112">**Resource group.**</span></span> <span data-ttu-id="2d6e1-113">[資源群組][resource-manager-overview]是保存 Azure 相關資源的本機容器。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-113">A [resource group][resource-manager-overview] is a logical container that holds related Azure resources.</span></span> <span data-ttu-id="2d6e1-114">一般來說，根據資源的存留期以及將管理資源的人員來群組資源。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-114">In general, group resources based on their lifetime and who will manage them.</span></span> 

* <span data-ttu-id="2d6e1-115">**VM**。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-115">**VM**.</span></span> <span data-ttu-id="2d6e1-116">您可以從已發佈的映像清單、自訂的受控映像或您上傳至 Azure Blob 儲存體的虛擬硬碟 (VHD) 檔案佈建 VM。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-116">You can provision a VM from a list of published images, or from a custom managed image or virtual hard disk (VHD) file uploaded to Azure Blob storage.</span></span>

* <span data-ttu-id="2d6e1-117">**受控磁碟**。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-117">**Managed Disks**.</span></span> <span data-ttu-id="2d6e1-118">[Azure 受控磁碟][managed-disks]藉由為您處理儲存體來簡化磁碟管理。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-118">[Azure Managed Disks][managed-disks] simplify disk management by handling the storage for you.</span></span> <span data-ttu-id="2d6e1-119">作業系統磁碟是儲存在 [Azure 儲存體][azure-storage]中的 VHD，因此即使主機電腦已關閉仍會保存下來。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-119">The OS disk is a VHD stored in [Azure Storage][azure-storage], so it persists even when the host machine is down.</span></span> <span data-ttu-id="2d6e1-120">我們也建議建立一或多個[資料磁碟][data-disk]，這些是用於應用程式資料的持續性 VHD。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-120">We also recommend creating one or more [data disks][data-disk], which are persistent VHDs used for application data.</span></span>

* <span data-ttu-id="2d6e1-121">**暫存磁碟。**</span><span class="sxs-lookup"><span data-stu-id="2d6e1-121">**Temporary disk.**</span></span> <span data-ttu-id="2d6e1-122">VM 是使用暫存磁碟 (Windows 上的 `D:` 磁碟機) 來建立。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-122">The VM is created with a temporary disk (the `D:` drive on Windows).</span></span> <span data-ttu-id="2d6e1-123">此磁碟會儲存在主機電腦的實體磁碟機上。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-123">This disk is stored on a physical drive on the host machine.</span></span> <span data-ttu-id="2d6e1-124">它「不會」儲存在 Azure 儲存體中，而且可能在重新開機期間和其他 VM 生命週期事件中遭到刪除。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-124">It is *not* saved in Azure Storage and may be deleted during reboots and other VM lifecycle events.</span></span> <span data-ttu-id="2d6e1-125">僅將此磁碟使用於暫存資料，例如分頁檔或交換檔。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-125">Use this disk only for temporary data, such as page or swap files.</span></span>

* <span data-ttu-id="2d6e1-126">**虛擬網路 (VNet)。**</span><span class="sxs-lookup"><span data-stu-id="2d6e1-126">**Virtual network (VNet).**</span></span> <span data-ttu-id="2d6e1-127">每部 Azure VM 都會部署到可以分割成多個子網路的 VNet。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-127">Every Azure VM is deployed into a VNet that can be segmented into multiple subnets.</span></span>

* <span data-ttu-id="2d6e1-128">**網路介面 (NIC)**。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-128">**Network interface (NIC)**.</span></span> <span data-ttu-id="2d6e1-129">NIC 可讓 VM 與虛擬網路通訊。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-129">The NIC enables the VM to communicate with the virtual network.</span></span>  

* <span data-ttu-id="2d6e1-130">**公用 IP 位址。**</span><span class="sxs-lookup"><span data-stu-id="2d6e1-130">**Public IP address.**</span></span> <span data-ttu-id="2d6e1-131">必須要有公用 IP 位址才能與 VM &mdash; 進行通訊，例如透過遠端桌面 (RDP)。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-131">A public IP address is needed to communicate with the VM &mdash; for example, via remote desktop (RDP).</span></span>  

* <span data-ttu-id="2d6e1-132">**Azure DNS**。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-132">**Azure DNS**.</span></span> <span data-ttu-id="2d6e1-133">[Azure DNS][azure-dns] 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構提供名稱解析。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-133">[Azure DNS][azure-dns] is a hosting service for DNS domains, providing name resolution using Microsoft Azure infrastructure.</span></span> <span data-ttu-id="2d6e1-134">只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-134">By hosting your domains in Azure, you can manage your DNS records using the same credentials, APIs, tools, and billing as your other Azure services.</span></span>

* <span data-ttu-id="2d6e1-135">**網路安全性群組 (NSG)**。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-135">**Network security group (NSG)**.</span></span> <span data-ttu-id="2d6e1-136">[網路安全性群組][nsg]可用來允許或拒絕 VM 的網路流量。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-136">[Network security groups][nsg] are used to allow or deny network traffic to VMs.</span></span> <span data-ttu-id="2d6e1-137">NSG 可與子網路或個別 VM 執行個體相關聯。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-137">NSGs can be associated either with subnets or with individual VM instances.</span></span> 

* <span data-ttu-id="2d6e1-138">**診斷。**</span><span class="sxs-lookup"><span data-stu-id="2d6e1-138">**Diagnostics.**</span></span> <span data-ttu-id="2d6e1-139">診斷記錄對於管理和針對 VM 進行疑難排解十分重要。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-139">Diagnostic logging is crucial for managing and troubleshooting the VM.</span></span>

## <a name="recommendations"></a><span data-ttu-id="2d6e1-140">建議</span><span class="sxs-lookup"><span data-stu-id="2d6e1-140">Recommendations</span></span>

<span data-ttu-id="2d6e1-141">此架構顯示在 Azure 中執行 Windows VM 的基準建議。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-141">This architecture shows the baseline recommendations for running a Windows VM in Azure.</span></span> <span data-ttu-id="2d6e1-142">但是，我們不建議針對關鍵任務工作負載使用單一 VM，因為它會建立單一失敗點。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-142">However, we don't recommend using a single VM for mission critical workloads because it creates a single point of failure.</span></span> <span data-ttu-id="2d6e1-143">如需較高的可用性，請部署兩部以上的負載平衡 VM。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-143">For higher availability, deploy two or more load-balanced VMs.</span></span> <span data-ttu-id="2d6e1-144">如需詳細資訊，請參閱[在 Azure 上執行多個 VM][multi-vm]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-144">For more information, see [Running multiple VMs on Azure][multi-vm].</span></span>

### <a name="vm-recommendations"></a><span data-ttu-id="2d6e1-145">VM 建議</span><span class="sxs-lookup"><span data-stu-id="2d6e1-145">VM recommendations</span></span>

<span data-ttu-id="2d6e1-146">Azure 提供許多不同的虛擬機器大小。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-146">Azure offers many different virtual machine sizes.</span></span> <span data-ttu-id="2d6e1-147">如需相關資訊，請參閱 [Azure 中虛擬機器的大小][virtual-machine-sizes]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-147">For more information, see [Sizes for virtual machines in Azure][virtual-machine-sizes].</span></span> <span data-ttu-id="2d6e1-148">如果您將現有的工作負載移至 Azure，則從最符合您內部部署伺服器的 VM 大小開始。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-148">If you are moving an existing workload to Azure, start with the VM size that's the closest match to your on-premises servers.</span></span> <span data-ttu-id="2d6e1-149">然後根據 CPU、記憶體和每秒的磁碟輸入/輸出作業 (IOPS) 測量您的實際工作負載效能，並視需要調整大小。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-149">Then measure the performance of your actual workload with respect to CPU, memory, and disk input/output operations per second (IOPS), and adjust the size as needed.</span></span> <span data-ttu-id="2d6e1-150">如果您的 VM 需要多個 NIC，請注意每種 [VM 大小][vm-size-tables]都有定義 NIC 的數目上限。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-150">If you require multiple NICs for your VM, be aware that a maximum number of NICs is defined for each [VM size][vm-size-tables].</span></span>

<span data-ttu-id="2d6e1-151">一般而言，選擇最接近您的內部使用者或客戶的 Azure 區域。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-151">Generally, choose an Azure region that is closest to your internal users or customers.</span></span> <span data-ttu-id="2d6e1-152">不過，並非所有 VM 大小在所有區域都可供使用。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-152">However, not all VM sizes are available in all regions.</span></span> <span data-ttu-id="2d6e1-153">如需詳細資訊，請參閱[依區域提供的服務][services-by-region]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-153">For more information, see [Services by region][services-by-region].</span></span> <span data-ttu-id="2d6e1-154">如需特定區域中可用的 VM 大小清單，請從 Azure 命令列介面 (CLI) 執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="2d6e1-154">For a list of the VM sizes available in a specific region, run the following command from the Azure command-line interface (CLI):</span></span>

```
az vm list-sizes --location <location>
```

<span data-ttu-id="2d6e1-155">如需選擇已發佈 VM 映像的相關資訊，請參閱[尋找 Windows VM 映像][select-vm-image]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-155">For information about choosing a published VM image, see [Find Windows VM images][select-vm-image].</span></span>

<span data-ttu-id="2d6e1-156">啟用監視和診斷，包括基本健全狀況度量、診斷基礎結構記錄檔及[開機診斷][boot-diagnostics]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-156">Enable monitoring and diagnostics, including basic health metrics, diagnostics infrastructure logs, and [boot diagnostics][boot-diagnostics].</span></span> <span data-ttu-id="2d6e1-157">如果您的 VM 進入無法開機的狀態，開機診斷能協助您診斷開機失敗。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-157">Boot diagnostics can help you diagnose boot failure if your VM gets into a non-bootable state.</span></span> <span data-ttu-id="2d6e1-158">如需詳細資訊，請參閱[啟用監視和診斷][enable-monitoring]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-158">For more information, see [Enable monitoring and diagnostics][enable-monitoring].</span></span>  

### <a name="disk-and-storage-recommendations"></a><span data-ttu-id="2d6e1-159">磁碟和儲存體建議</span><span class="sxs-lookup"><span data-stu-id="2d6e1-159">Disk and storage recommendations</span></span>

<span data-ttu-id="2d6e1-160">為了達到最佳的磁碟 I/O 效能，我們建議使用[進階儲存體][premium-storage]，這會將資料儲存在固態硬碟 (SSD)。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-160">For best disk I/O performance, we recommend [Premium Storage][premium-storage], which stores data on solid-state drives (SSDs).</span></span> <span data-ttu-id="2d6e1-161">成本是依佈建的磁碟容量而定。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-161">Cost is based on the capacity of the provisioned disk.</span></span> <span data-ttu-id="2d6e1-162">IOPS 和輸送量 (亦即，資料傳輸速率) 也取決於磁碟大小，因此當您佈建磁碟時，請考慮以下三個因素 (容量、IOPS 和輸送量)。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-162">IOPS and throughput (that is, data transfer rate) also depend on disk size, so when you provision a disk, consider all three factors (capacity, IOPS, and throughput).</span></span> 

<span data-ttu-id="2d6e1-163">我們也建議使用[受控磁碟][managed-disks]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-163">We also recommend using [Managed Disks][managed-disks].</span></span> <span data-ttu-id="2d6e1-164">受控磁碟不需要儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-164">Managed disks do not require a storage account.</span></span> <span data-ttu-id="2d6e1-165">您只需指定磁碟的大小和類型，它就會以高度可用的資源方式進行部署。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-165">You simply specify the size and type of disk and it is deployed as a highly available resource.</span></span>

<span data-ttu-id="2d6e1-166">新增一或多個資料磁碟。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-166">Add one or more data disks.</span></span> <span data-ttu-id="2d6e1-167">當您建立 VHD 時，它仍未格式化。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-167">When you create a VHD, it is unformatted.</span></span> <span data-ttu-id="2d6e1-168">登入 VM 來格式化磁碟。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-168">Log into the VM to format the disk.</span></span> <span data-ttu-id="2d6e1-169">若情況允許，請將應用程式安裝在資料磁碟，不要安裝在作業系統磁碟。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-169">When possible, install applications on a data disk, not the OS disk.</span></span> <span data-ttu-id="2d6e1-170">有些舊版應用程式可能需要在 C: 磁碟機上安裝元件，在此情況下，您可以使用 PowerShell [調整作業系統磁碟大小][resize-os-disk]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-170">Some legacy applications might need to install components on the C: drive; in that case, you can [resize the OS disk][resize-os-disk] using PowerShell.</span></span>

<span data-ttu-id="2d6e1-171">建立儲存體帳戶以放置診斷記錄。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-171">Create a storage account to hold diagnostic logs.</span></span> <span data-ttu-id="2d6e1-172">標準本地備援儲存體 (LRS) 帳戶已足以保存診斷記錄。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-172">A standard locally redundant storage (LRS) account is sufficient for diagnostic logs.</span></span>

> [!NOTE]
> <span data-ttu-id="2d6e1-173">如果您未使用受控磁碟，請針對每個 VM 建立個別的 Azure 儲存體帳戶來保存虛擬硬碟 (VHD)，以避免達到儲存體帳戶的 [IOPS 限制][vm-disk-limits]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-173">If you aren't using Managed Disks, create separate Azure storage accounts for each VM to hold the virtual hard disks (VHDs), in order to avoid hitting the [(IOPS) limits][vm-disk-limits] for storage accounts.</span></span> <span data-ttu-id="2d6e1-174">請注意儲存體帳戶的總 I/O 限制。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-174">Be aware of the total I/O limits of the storage account.</span></span> <span data-ttu-id="2d6e1-175">如需詳細資訊，請參閱[虛擬機器磁碟限制][vm-disk-limits]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-175">For more information, see [virtual machine disk limits][vm-disk-limits].</span></span>


### <a name="network-recommendations"></a><span data-ttu-id="2d6e1-176">網路建議</span><span class="sxs-lookup"><span data-stu-id="2d6e1-176">Network recommendations</span></span>

<span data-ttu-id="2d6e1-177">此公用 IP 位址可以是動態或靜態。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-177">The public IP address can be dynamic or static.</span></span> <span data-ttu-id="2d6e1-178">預設值為動態。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-178">The default is dynamic.</span></span>

* <span data-ttu-id="2d6e1-179">若您需要一個不會變更 &mdash; 的固定 IP 位址 (例如，若您需要建立 DNS「A」記錄，或將 IP 位址新增到安全清單中)，請保留一個[靜態 IP 位址][static-ip]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-179">Reserve a [static IP address][static-ip] if you need a fixed IP address that won't change &mdash; for example, if you need to create a DNS 'A' record or add the IP address to a safe list.</span></span>
* <span data-ttu-id="2d6e1-180">您也可以建立 IP 位址的完整網域名稱 (FQDN)。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-180">You can also create a fully qualified domain name (FQDN) for the IP address.</span></span> <span data-ttu-id="2d6e1-181">然後您可以在 DNS 中註冊指向該 FQDN 的 [CNAME 記錄][cname-record]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-181">You can then register a [CNAME record][cname-record] in DNS that points to the FQDN.</span></span> <span data-ttu-id="2d6e1-182">如需詳細資訊，請參閱[在 Azure 入口網站中建立完整網域名稱][fqdn]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-182">For more information, see [Create a fully qualified domain name in the Azure portal][fqdn].</span></span>

<span data-ttu-id="2d6e1-183">所有 NSG 都包含一組[預設規則][nsg-default-rules]，包括一個封鎖所有網際網路輸入流量的規則。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-183">All NSGs contain a set of [default rules][nsg-default-rules], including a rule that blocks all inbound Internet traffic.</span></span> <span data-ttu-id="2d6e1-184">預設的規則不能刪除，但其他規則可以覆寫它們。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-184">The default rules cannot be deleted, but other rules can override them.</span></span> <span data-ttu-id="2d6e1-185">若要啟用網際網路流量，請建立允許輸入流量輸入特定連接埠的規則 &mdash; 例如，允許連接埠 80 用於 HTTP。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-185">To enable Internet traffic, create rules that allow inbound traffic to specific ports &mdash; for example, port 80 for HTTP.</span></span>

<span data-ttu-id="2d6e1-186">若要啟用 RDP，請新增一個 NSG 規則，以允許將輸入流量輸入至 TCP 連接埠 3389。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-186">To enable RDP, add an NSG rule that allows inbound traffic to TCP port 3389.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="2d6e1-187">延展性考量</span><span class="sxs-lookup"><span data-stu-id="2d6e1-187">Scalability considerations</span></span>

<span data-ttu-id="2d6e1-188">您可以藉由[變更 VM 大小][vm-resize]來相應增加或相應減少 VM。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-188">You can scale a VM up or down by [changing the VM size][vm-resize].</span></span> <span data-ttu-id="2d6e1-189">若要水平相應放大，請將兩個以上的 VM 置於負載平衡器後方。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-189">To scale out horizontally, put two or more VMs behind a load balancer.</span></span> <span data-ttu-id="2d6e1-190">如需詳細資訊，請參閱[執行負載平衡的 VM 以獲得延展性和可用性][multi-vm]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-190">For more information, see [Run load-balanced VMs for scalability and availability][multi-vm].</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="2d6e1-191">可用性考量</span><span class="sxs-lookup"><span data-stu-id="2d6e1-191">Availability considerations</span></span>

<span data-ttu-id="2d6e1-192">若要擁有較高的可用性，請在可用性設定組中部署多個 VM。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-192">For higher availability, deploy multiple VMs in an availability set.</span></span> <span data-ttu-id="2d6e1-193">這也會提供較高的[服務等級協定 (SLA)][vm-sla]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-193">This also provides a higher [service level agreement (SLA)][vm-sla].</span></span>

<span data-ttu-id="2d6e1-194">您的 VM 可能會受到[計劃性維護][planned-maintenance]或[非計劃性維護][manage-vm-availability]影響。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-194">Your VM may be affected by [planned maintenance][planned-maintenance] or [unplanned maintenance][manage-vm-availability].</span></span> <span data-ttu-id="2d6e1-195">您可以使用 [VM 重新啟動記錄檔][reboot-logs]來判斷 VM 重新啟動是否是因為計劃性維護所造成。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-195">You can use [VM reboot logs][reboot-logs] to determine whether a VM reboot was caused by planned maintenance.</span></span>

<span data-ttu-id="2d6e1-196">為了防止在正常作業期間意外遺失資料 (例如，因使用者錯誤而造成)，您也應該使用 [Blob 快照][blob-snapshot]或其他工具來實作時間點備份。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-196">To protect against accidental data loss during normal operations (for example, because of user error), you should also implement point-in-time backups, using [blob snapshots][blob-snapshot] or another tool.</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="2d6e1-197">管理性考量</span><span class="sxs-lookup"><span data-stu-id="2d6e1-197">Manageability considerations</span></span>

<span data-ttu-id="2d6e1-198">**資源群組。**</span><span class="sxs-lookup"><span data-stu-id="2d6e1-198">**Resource groups.**</span></span> <span data-ttu-id="2d6e1-199">請將關係密切且具有相同生命週期的資源置於同一個[資源群組][resource-manager-overview]中。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-199">Put closely associated resources that share the same lifecycle into the same [resource group][resource-manager-overview].</span></span> <span data-ttu-id="2d6e1-200">資源群組可讓您以群組為單位來部署和監視資源，並根據資源群組追蹤帳單成本。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-200">Resource groups allow you to deploy and monitor resources as a group and track billing costs by resource group.</span></span> <span data-ttu-id="2d6e1-201">您也可以刪除整組資源，這對於測試部署非常有用。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-201">You can also delete resources as a set, which is very useful for test deployments.</span></span> <span data-ttu-id="2d6e1-202">請指派有意義的資源名稱，以簡化尋找特定資源及了解其角色的程序。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-202">Assign meaningful resource names to simplify locating a specific resource and understanding its role.</span></span> <span data-ttu-id="2d6e1-203">如需詳細資訊，請參閱[建議的 Azure 資源命名慣例][naming-conventions]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-203">For more information, see [Recommended Naming Conventions for Azure Resources][naming-conventions].</span></span>

<span data-ttu-id="2d6e1-204">**停止 VM。**</span><span class="sxs-lookup"><span data-stu-id="2d6e1-204">**Stopping a VM.**</span></span> <span data-ttu-id="2d6e1-205">Azure 會區分「已停止」和「已解除配置」狀態。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-205">Azure makes a distinction between "stopped" and "deallocated" states.</span></span> <span data-ttu-id="2d6e1-206">您需要在 VM 狀態停止時支付費用，而不是在取消配置 VM 時支付。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-206">You are charged when the VM status is stopped, but not when the VM is deallocated.</span></span> <span data-ttu-id="2d6e1-207">在 Azure 入口網站中，[停止] 按鈕會取消配置 VM。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-207">In the Azure portal, the **Stop** button deallocates the VM.</span></span> <span data-ttu-id="2d6e1-208">如果您已在登入時透過 OS 關閉，則會停止 VM，但不會取消配置，因此您仍需付費。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-208">If you shut down through the OS while logged in, the VM is stopped but **not** deallocated, so you will still be charged.</span></span>

<span data-ttu-id="2d6e1-209">**刪除 VM。**</span><span class="sxs-lookup"><span data-stu-id="2d6e1-209">**Deleting a VM.**</span></span> <span data-ttu-id="2d6e1-210">如果您刪除 VM，並不會刪除 VHD。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-210">If you delete a VM, the VHDs are not deleted.</span></span> <span data-ttu-id="2d6e1-211">這表示您可以放心地刪除 VM，而不會遺失任何資料。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-211">That means you can safely delete the VM without losing data.</span></span> <span data-ttu-id="2d6e1-212">不過，您仍需支付儲存體費用。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-212">However, you will still be charged for storage.</span></span> <span data-ttu-id="2d6e1-213">若要刪除 VHD，請將檔案從 [Blob 儲存體][blob-storage]中刪除。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-213">To delete the VHD, delete the file from [Blob storage][blob-storage].</span></span> <span data-ttu-id="2d6e1-214">若要防止意外刪除，請使用[資源鎖定][resource-lock]來鎖定整個資源群組或鎖定個別資源 (例如 VM)。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-214">To prevent accidental deletion, use a [resource lock][resource-lock] to lock the entire resource group or lock individual resources, such as a VM.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="2d6e1-215">安全性考量</span><span class="sxs-lookup"><span data-stu-id="2d6e1-215">Security considerations</span></span>

<span data-ttu-id="2d6e1-216">使用 [Azure 資訊安全中心][security-center]來集中檢視 Azure 資源的安全性狀態。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-216">Use [Azure Security Center][security-center] to get a central view of the security state of your Azure resources.</span></span> <span data-ttu-id="2d6e1-217">資訊安全中心會監視潛在的安全性問題，並提供全面性的部署安全性健康狀態。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-217">Security Center monitors potential security issues and provides a comprehensive picture of the security health of your deployment.</span></span> <span data-ttu-id="2d6e1-218">資訊安全中心是依每個 Azure 訂用帳戶設定。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-218">Security Center is configured per Azure subscription.</span></span> <span data-ttu-id="2d6e1-219">請按照 [Azure 資訊安全中心快速入門指南][security-center-get-started]中所述的方式，來啟用安全性資料收集。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-219">Enable security data collection as described in the [Azure Security Center quick start guide][security-center-get-started].</span></span> <span data-ttu-id="2d6e1-220">啟用資料收集時，資訊安全性中心就會自動掃描任何該訂用帳戶建立的 VM。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-220">When data collection is enabled, Security Center automatically scans any VMs created under that subscription.</span></span>

<span data-ttu-id="2d6e1-221">**修補程式管理。**</span><span class="sxs-lookup"><span data-stu-id="2d6e1-221">**Patch management.**</span></span> <span data-ttu-id="2d6e1-222">若已啟用，資訊安全性中心會檢查是否遺漏了任何安全性或重要更新。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-222">If enabled, Security Center checks whether any security and critical updates are missing.</span></span> <span data-ttu-id="2d6e1-223">使用 VM 上的[群組原則設定][group-policy]來啟用自動系統更新。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-223">Use [Group Policy settings][group-policy] on the VM to enable automatic system updates.</span></span>

<span data-ttu-id="2d6e1-224">**反惡意程式碼。**</span><span class="sxs-lookup"><span data-stu-id="2d6e1-224">**Antimalware.**</span></span> <span data-ttu-id="2d6e1-225">如果啟用，資訊安全性中心會檢查是已安裝反惡意程式碼軟體。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-225">If enabled, Security Center checks whether antimalware software is installed.</span></span> <span data-ttu-id="2d6e1-226">您也可以使用資訊安全中心來從 Azure 入口網站內安裝反惡意程式碼軟體。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-226">You can also use Security Center to install antimalware software from inside the Azure portal.</span></span>

<span data-ttu-id="2d6e1-227">**作業。**</span><span class="sxs-lookup"><span data-stu-id="2d6e1-227">**Operations.**</span></span> <span data-ttu-id="2d6e1-228">請使用[角色型存取控制 (RBAC)][rbac] 來控制對您所部署 Azure 資源的存取。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-228">Use [role-based access control (RBAC)][rbac] to control access to the Azure resources that you deploy.</span></span> <span data-ttu-id="2d6e1-229">RBAC 可讓您指派授權角色給您 DevOps 小組的成員。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-229">RBAC lets you assign authorization roles to members of your DevOps team.</span></span> <span data-ttu-id="2d6e1-230">例如，「讀取者」角色能檢視 Azure 資源但不能建立、管理或刪除它們。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-230">For example, the Reader role can view Azure resources but not create, manage, or delete them.</span></span> <span data-ttu-id="2d6e1-231">某些角色專門用於特定的 Azure 資源類型。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-231">Some roles are specific to particular Azure resource types.</span></span> <span data-ttu-id="2d6e1-232">例如，「虛擬機器參與者」角色能重新啟動或解除配置 VM、重設系統管理員密碼、建立新的 VM 等等。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-232">For example, the Virtual Machine Contributor role can restart or deallocate a VM, reset the administrator password, create a new VM, and so on.</span></span> <span data-ttu-id="2d6e1-233">其他對此架構可能有用的[內建 RBAC 角色][rbac-roles]包括 [DevTest Labs 使用者][rbac-devtest]和[網路參與者][rbac-network]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-233">Other [built-in RBAC roles][rbac-roles] that may be useful for this architecture include [DevTest Labs User][rbac-devtest] and [Network Contributor][rbac-network].</span></span> <span data-ttu-id="2d6e1-234">使用者可以被指派多個角色，且您可以針對更詳細的權限建立角色。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-234">A user can be assigned to multiple roles, and you can create custom roles for even more fine-grained permissions.</span></span>

> [!NOTE]
> <span data-ttu-id="2d6e1-235">RBAC 不會限制使用者登入 VM 可執行的動作。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-235">RBAC does not limit the actions that a user logged into a VM can perform.</span></span> <span data-ttu-id="2d6e1-236">這些權限是由客體 OS上的帳戶類型來決定。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-236">Those permissions are determined by the account type on the guest OS.</span></span>   

<span data-ttu-id="2d6e1-237">使用[稽核記錄檔][audit-logs]來查看佈建動作和其他 VM 事件。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-237">Use [audit logs][audit-logs] to see provisioning actions and other VM events.</span></span>

<span data-ttu-id="2d6e1-238">**資料加密。**</span><span class="sxs-lookup"><span data-stu-id="2d6e1-238">**Data encryption.**</span></span> <span data-ttu-id="2d6e1-239">如果您要加密作業系統和資料磁碟，請考慮使用 [Azure 磁碟加密][disk-encryption]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-239">Consider [Azure Disk Encryption][disk-encryption] if you need to encrypt the OS and data disks.</span></span> 

## <a name="deploy-the-solution"></a><span data-ttu-id="2d6e1-240">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="2d6e1-240">Deploy the solution</span></span>

<span data-ttu-id="2d6e1-241">適用於此架構的部署可在 [GitHub][github-folder] 上取得。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-241">A deployment for this architecture is available on [GitHub][github-folder].</span></span> <span data-ttu-id="2d6e1-242">它會部署下列各項：</span><span class="sxs-lookup"><span data-stu-id="2d6e1-242">It deploys the following:</span></span>

  * <span data-ttu-id="2d6e1-243">用來裝載 VM 且具有名為 **web** 之單一子網路的虛擬網路。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-243">A virtual network with a single subnet named **web** used to host the VM.</span></span>
  * <span data-ttu-id="2d6e1-244">含有兩個傳入規則的 NSG，允許 RDP 和 HTTP 流量到 VM。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-244">An NSG with two incoming rules to allow RDP and HTTP traffic to the VM.</span></span>
  * <span data-ttu-id="2d6e1-245">執行最新版 Windows Server 2016 Datacenter Edition 的 VM。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-245">A VM running the latest version of Windows Server 2016 Datacenter Edition.</span></span>
  * <span data-ttu-id="2d6e1-246">可將兩個資料磁碟格式化的範例自訂指令碼擴充功能，以及可部署 Internet Information Services (IIS) 的 PowerShell DSC 指令碼。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-246">A sample custom script extension that formats the two data disks, and a PowerShell DSC script that deploys Internet Information Services (IIS).</span></span>

### <a name="prerequisites"></a><span data-ttu-id="2d6e1-247">先決條件</span><span class="sxs-lookup"><span data-stu-id="2d6e1-247">Prerequisites</span></span>

1. <span data-ttu-id="2d6e1-248">複製、派生或下載適用於[參考架構][ref-arch-repo] GitHub 存放庫的 zip 檔案。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-248">Clone, fork, or download the zip file for the [reference architectures][ref-arch-repo] GitHub repository.</span></span>

2. <span data-ttu-id="2d6e1-249">確定您已在電腦上安裝 Azure CLI 2.0。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-249">Make sure you have the Azure CLI 2.0 installed on your computer.</span></span> <span data-ttu-id="2d6e1-250">如需 CLI 安裝指示，請參閱[安裝 Azure CLI 2.0][azure-cli-2]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-250">For CLI installation instructions, see [Install Azure CLI 2.0][azure-cli-2].</span></span>

3. <span data-ttu-id="2d6e1-251">安裝 [Azure 建置組塊][azbb] npm 封裝。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-251">Install the [Azure building blocks][azbb] npm package.</span></span>

4. <span data-ttu-id="2d6e1-252">從命令提示字元、bash 提示字元或 PowerShell 提示字元中，輸入下列命令來登入您的 Azure 帳戶。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-252">From a command prompt, bash prompt, or PowerShell prompt, enter the following command to log into your Azure account.</span></span>

   ```bash
   az login
   ```

### <a name="deploy-the-solution-using-azbb"></a><span data-ttu-id="2d6e1-253">使用 azbb 部署解決方案</span><span class="sxs-lookup"><span data-stu-id="2d6e1-253">Deploy the solution using azbb</span></span>

<span data-ttu-id="2d6e1-254">若要部署此參考架構，請依照下列步驟執行：</span><span class="sxs-lookup"><span data-stu-id="2d6e1-254">To deploy this reference architecture, follow these steps:</span></span>

1. <span data-ttu-id="2d6e1-255">瀏覽至您在上述必要條件步驟中所下載存放庫的 `virtual-machines\single-vm\parameters\windows` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-255">Navigate to the `virtual-machines\single-vm\parameters\windows` folder for the repository you downloaded in the prerequisites step above.</span></span>

2. <span data-ttu-id="2d6e1-256">開啟 `single-vm-v2.json` 檔案，然後在引號之間輸入使用者名稱和密碼，然後儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-256">Open the `single-vm-v2.json` file and enter a username and password between the quotes, then save the file.</span></span>

   ```bash
   "adminUsername": "",
   "adminPassword": "",
   ```

3. <span data-ttu-id="2d6e1-257">執行 `azbb` 以部署範例 VM，如下所示。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-257">Run `azbb` to deploy the sample VM as shown below.</span></span>

   ```bash
   azbb -s <subscription_id> -g <resource_group_name> -l <location> -p single-vm-v2.json --deploy
   ```

<span data-ttu-id="2d6e1-258">若要驗證部署，請執行下列 Azure CLI 命令，以尋找 VM 的公用 IP 位址：</span><span class="sxs-lookup"><span data-stu-id="2d6e1-258">To verify the deployment, run the following Azure CLI command to find the public IP address of the VM:</span></span>

```bash
az vm show -n ra-single-windows-vm1 -g <resource-group-name> -d -o table
```

<span data-ttu-id="2d6e1-259">如果您在網頁瀏覽器中巡覽到這個位址，您應會看到預設的 IIS 首頁。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-259">If you navigate to this address in a web browser, you should see the default IIS homepage.</span></span>

<span data-ttu-id="2d6e1-260">如需自訂此部署的相關資訊，請瀏覽我們的 [GitHub 存放庫][git]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-260">For information about customizing this deployment, visit our [GitHub repository][git].</span></span>

## <a name="next-steps"></a><span data-ttu-id="2d6e1-261">後續步驟</span><span class="sxs-lookup"><span data-stu-id="2d6e1-261">Next steps</span></span>

- <span data-ttu-id="2d6e1-262">了解我們的 [Azure 建置組塊][azbbv2]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-262">Learn about our [Azure building Blocks][azbbv2].</span></span>
- <span data-ttu-id="2d6e1-263">在 Azure 中部署[多個 VM][multi-vm]。</span><span class="sxs-lookup"><span data-stu-id="2d6e1-263">Deploy [multiple VMs][multi-vm] in Azure.</span></span>

<!-- links -->
[audit-logs]: https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: /azure/virtual-machines/virtual-machines-windows-create-availability-set
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[azbbv2]: https://github.com/mspnp/template-building-blocks
[azure-cli-2]: /cli/azure/install-azure-cli?view=azure-cli-latest
[azure-dns]: /azure/dns/dns-overview
[azure-storage]: /azure/storage/storage-introduction
[blob-snapshot]: /azure/storage/storage-blob-snapshots
[blob-storage]: /azure/storage/storage-introduction
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: /azure/virtual-machines/virtual-machines-windows-about-disks-vhds
[disk-encryption]: /azure/security/azure-security-disk-encryption
[enable-monitoring]: /azure/monitoring-and-diagnostics/insights-how-to-use-diagnostics
[fqdn]: /azure/virtual-machines/virtual-machines-windows-portal-create-fqdn
[git]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/single-vm
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/single-vm
[group-policy]: https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn595129(v=ws.11)
[log-collector]: https://azure.microsoft.com/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: /azure/virtual-machines/virtual-machines-windows-manage-availability
[managed-disks]: /azure/storage/storage-managed-disks-overview
[multi-vm]: multi-vm.md
[naming-conventions]: ../../best-practices/naming-conventions.md
[nsg]: /azure/virtual-network/virtual-networks-nsg
[nsg-default-rules]: /azure/virtual-network/virtual-networks-nsg#default-rules
[planned-maintenance]: /azure/virtual-machines/virtual-machines-windows-planned-maintenance
[premium-storage]: /azure/virtual-machines/windows/premium-storage
[premium-storage-supported]: /azure/virtual-machines/windows/premium-storage#supported-vms
[rbac]: /azure/active-directory/role-based-access-control-what-is
[rbac-roles]: /azure/active-directory/role-based-access-built-in-roles
[rbac-devtest]: /azure/active-directory/role-based-access-built-in-roles#devtest-labs-user
[rbac-network]: /azure/active-directory/role-based-access-built-in-roles#network-contributor
[reboot-logs]: https://azure.microsoft.com/blog/viewing-vm-reboot-logs/
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[resize-os-disk]: /azure/virtual-machines/virtual-machines-windows-expand-os-disk
[resource-lock]: /azure/resource-group-lock-resources
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[security-center]: /azure/security-center/security-center-intro
[security-center-get-started]: /azure/security-center/security-center-get-started
[select-vm-image]: /azure/virtual-machines/virtual-machines-windows-cli-ps-findimage
[services-by-region]: https://azure.microsoft.com/regions/#services
[static-ip]: /azure/virtual-network/virtual-networks-reserved-public-ip
[virtual-machine-sizes]: /azure/virtual-machines/virtual-machines-windows-sizes
[visio-download]: https://archcenter.blob.core.windows.net/cdn/vm-reference-architectures.vsdx
[vm-disk-limits]: /azure/azure-subscription-service-limits#virtual-machine-disk-limits
[vm-resize]: /azure/virtual-machines/virtual-machines-linux-change-vm-size
[vm-size-tables]: /azure/virtual-machines/virtual-machines-windows-sizes
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[0]: ./images/single-vm-diagram.png "Azure 中的單一 Windows VM 架構"

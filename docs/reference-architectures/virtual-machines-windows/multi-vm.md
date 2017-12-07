---
title: "在 Azure 上執行負載平衡的 VM 以獲得延展性和可用性"
description: "如何在 Azure 上執行多個 Windows VM 以獲得延展性和可用性。"
author: telmosampaio
ms.date: 11/16/2017
pnp.series.title: Windows VM workloads
pnp.series.next: n-tier
pnp.series.prev: single-vm
ms.openlocfilehash: c9b1e52044d38348ecf1bd29cb24b3c20d1d6a45
ms.sourcegitcommit: 115db7ee008a0b1f2b0be50a26471050742ddb04
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2017
---
# <a name="run-load-balanced-vms-for-scalability-and-availability"></a><span data-ttu-id="3666d-103">執行負載平衡的 VM 以獲得延展性和可用性</span><span class="sxs-lookup"><span data-stu-id="3666d-103">Run load-balanced VMs for scalability and availability</span></span>

<span data-ttu-id="3666d-104">此參考架構顯示一組經過證實的作法，可用來在負載平衡器後方的擴展集中執行多個 Windows 虛擬機器 (VM)，以提升可用性和延展性。</span><span class="sxs-lookup"><span data-stu-id="3666d-104">This reference architecture shows a set of proven practices for running multiple Windows virtual machines (VMs) in a scale set behind a load balancer, to improve availability and scalability.</span></span> <span data-ttu-id="3666d-105">此架構可用於任何無狀態的工作負載 (例如網頁伺服器)，而且是用於部署多層式架構應用程式的基礎。</span><span class="sxs-lookup"><span data-stu-id="3666d-105">This architecture can be used for any stateless workload, such as a web server, and is a foundation for deploying n-tier applications.</span></span> [<span data-ttu-id="3666d-106">**部署這個解決方案**。</span><span class="sxs-lookup"><span data-stu-id="3666d-106">**Deploy this solution**.</span></span>](#deploy-the-solution)

<span data-ttu-id="3666d-107">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="3666d-107">![[0]][0]</span></span>

<span data-ttu-id="3666d-108">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="3666d-108">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="3666d-109">架構</span><span class="sxs-lookup"><span data-stu-id="3666d-109">Architecture</span></span>

<span data-ttu-id="3666d-110">此架構是建置在[單一 VM 參考架構][single-vm]上。</span><span class="sxs-lookup"><span data-stu-id="3666d-110">This architecture builds on the [Single VM reference architecture][single-vm].</span></span> <span data-ttu-id="3666d-111">那些建議也適用於此架構。</span><span class="sxs-lookup"><span data-stu-id="3666d-111">Those recommendations also apply to this architecture.</span></span>

<span data-ttu-id="3666d-112">在此架構中，會將工作負載散發到多個 VM 執行個體。</span><span class="sxs-lookup"><span data-stu-id="3666d-112">In this architecture, a workload is distributed across multiple VM instances.</span></span> <span data-ttu-id="3666d-113">有個單一公用 IP 位址，而且會使用負載平衡器來將網際網路流量散發到 VM。</span><span class="sxs-lookup"><span data-stu-id="3666d-113">There is a single public IP address, and Internet traffic is distributed to the VMs using a load balancer.</span></span> <span data-ttu-id="3666d-114">此架構可用於單層式架構應用程式，例如無狀態的 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="3666d-114">This architecture can be used for a single-tier application, such as a stateless web application.</span></span>

<span data-ttu-id="3666d-115">此架構具有下列元件：</span><span class="sxs-lookup"><span data-stu-id="3666d-115">The architecture has the following components:</span></span>

* <span data-ttu-id="3666d-116">**資源群組。**</span><span class="sxs-lookup"><span data-stu-id="3666d-116">**Resource group.**</span></span> <span data-ttu-id="3666d-117">[資源群組][resource-manager-overview]可用來將資源組合在一起，讓它們可以依存留期、擁有者或其他準則管理。</span><span class="sxs-lookup"><span data-stu-id="3666d-117">[Resource groups][resource-manager-overview] are used to group resources so they can be managed by lifetime, owner, or other criteria.</span></span>
* <span data-ttu-id="3666d-118">**虛擬網路 (VNet) 和子網路。**</span><span class="sxs-lookup"><span data-stu-id="3666d-118">**Virtual network (VNet) and subnet.**</span></span> <span data-ttu-id="3666d-119">每部 Azure VM 都會部署到可以分割成多個子網路的 VNet。</span><span class="sxs-lookup"><span data-stu-id="3666d-119">Every Azure VM is deployed into a VNet that can be segmented into multiple subnets.</span></span>
* <span data-ttu-id="3666d-120">**Azure Load Balancer**。</span><span class="sxs-lookup"><span data-stu-id="3666d-120">**Azure Load Balancer**.</span></span> <span data-ttu-id="3666d-121">[負載平衡器][load-balancer]會將連入網際網路要求散發到 VM 執行個體。</span><span class="sxs-lookup"><span data-stu-id="3666d-121">The [load balancer][load-balancer] distributes incoming Internet requests to the VM instances.</span></span> 
* <span data-ttu-id="3666d-122">**公用 IP 位址**。</span><span class="sxs-lookup"><span data-stu-id="3666d-122">**Public IP address**.</span></span> <span data-ttu-id="3666d-123">負載平衡器需要公用 IP 位址才能接收網際網路流量。</span><span class="sxs-lookup"><span data-stu-id="3666d-123">A public IP address is needed for the load balancer to receive Internet traffic.</span></span>
* <span data-ttu-id="3666d-124">**VM 擴展集**。</span><span class="sxs-lookup"><span data-stu-id="3666d-124">**VM scale set**.</span></span> <span data-ttu-id="3666d-125">[VM 擴展集][vm-scaleset]是一組用來裝載工作負載且完全相同的 VM。</span><span class="sxs-lookup"><span data-stu-id="3666d-125">A [VM scale set][vm-scaleset] is a set of identical VMs used to host a workload.</span></span> <span data-ttu-id="3666d-126">擴展集允許以手動方式或根據預先定義的規則，自動相應縮小或放大 VM 數目。</span><span class="sxs-lookup"><span data-stu-id="3666d-126">Scale sets allow the number of VMs to be scaled in or out manually, or automatically based on predefined rules.</span></span>
* <span data-ttu-id="3666d-127">**可用性設定組**。</span><span class="sxs-lookup"><span data-stu-id="3666d-127">**Availability set**.</span></span> <span data-ttu-id="3666d-128">[可用性設定組][availability-set]包含 VM，可使 VM 符合較高[服務等級協定 (SLA)][vm-sla] 的資格。</span><span class="sxs-lookup"><span data-stu-id="3666d-128">The [availability set][availability-set] contains the VMs, making the VMs eligible for a higher [service level agreement (SLA)][vm-sla].</span></span> <span data-ttu-id="3666d-129">若要套用較高的 SLA，可用性設定組必須至少包含兩個 VM。</span><span class="sxs-lookup"><span data-stu-id="3666d-129">For the higher SLA to apply, the availability set must include a minimum of two VMs.</span></span> <span data-ttu-id="3666d-130">可用性設定組會隱含於擴展集中。</span><span class="sxs-lookup"><span data-stu-id="3666d-130">Availability sets are implicit in scale sets.</span></span> <span data-ttu-id="3666d-131">如果您在擴展集之外建立 VM，就需要個別建立可用性設定組。</span><span class="sxs-lookup"><span data-stu-id="3666d-131">If you create VMs outside a scale set, you need to create the availability set independently.</span></span>
* <span data-ttu-id="3666d-132">**受控磁碟**。</span><span class="sxs-lookup"><span data-stu-id="3666d-132">**Managed disks**.</span></span> <span data-ttu-id="3666d-133">Azure 受控磁碟會管理適用於 VM 磁碟的虛擬硬碟 (VHD) 檔案。</span><span class="sxs-lookup"><span data-stu-id="3666d-133">Azure Managed Disks manage the virtual hard disk (VHD) files for the VM disks.</span></span> 
* <span data-ttu-id="3666d-134">**儲存體**。</span><span class="sxs-lookup"><span data-stu-id="3666d-134">**Storage**.</span></span> <span data-ttu-id="3666d-135">建立 Azure 儲存體帳戶以保存 VM 的診斷記錄。</span><span class="sxs-lookup"><span data-stu-id="3666d-135">Create an Azure Storage acount to hold diagnostic logs for the VMs.</span></span>

## <a name="recommendations"></a><span data-ttu-id="3666d-136">建議</span><span class="sxs-lookup"><span data-stu-id="3666d-136">Recommendations</span></span>

<span data-ttu-id="3666d-137">此處所述的架構不見得完全與您的需求相符。</span><span class="sxs-lookup"><span data-stu-id="3666d-137">Your requirements may not align completely with the architecture described here.</span></span> <span data-ttu-id="3666d-138">請使用以下建議作為起點。</span><span class="sxs-lookup"><span data-stu-id="3666d-138">Use these recommendations as a starting point.</span></span> 

### <a name="availability-and-scalability-recommendations"></a><span data-ttu-id="3666d-139">可用性和延展性建議</span><span class="sxs-lookup"><span data-stu-id="3666d-139">Availability and scalability recommendations</span></span>

<span data-ttu-id="3666d-140">適用於可用性和延展性的選項是使用[虛擬機器擴展集][vmss]。</span><span class="sxs-lookup"><span data-stu-id="3666d-140">An option for availability and scalability is to use a [virtual machine scale set][vmss].</span></span> <span data-ttu-id="3666d-141">VM 擴展集可協助您部署及管理一組完全相同的 VM。</span><span class="sxs-lookup"><span data-stu-id="3666d-141">VM scale sets help you to deploy and manage a set of identical VMs.</span></span> <span data-ttu-id="3666d-142">擴展集支援根據效能計量自動調整規模。</span><span class="sxs-lookup"><span data-stu-id="3666d-142">Scale sets support autoscaling based on performance metrics.</span></span> <span data-ttu-id="3666d-143">當 VM 上的負載增加時，就會將其他 VM 自動新增到負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="3666d-143">As the load on the VMs increases, additional VMs are automatically added to the load balancer.</span></span> <span data-ttu-id="3666d-144">如果您需要快速相應放大 VM 數目，或需要自動調整規模，請考慮擴展集。</span><span class="sxs-lookup"><span data-stu-id="3666d-144">Consider scale sets if you need to quickly scale out VMs, or need to autoscale.</span></span>

<span data-ttu-id="3666d-145">根據預設，擴展集會使用「過度佈建」，這表示擴展集一開始會佈建比您所要求更多的 VM，然後刪除額外的 VM。</span><span class="sxs-lookup"><span data-stu-id="3666d-145">By default, scale sets use "overprovisioning," which means the scale set initially provisions more VMs than you ask for, then deletes the extra VMs.</span></span> <span data-ttu-id="3666d-146">這可改善佈建 VM 時的整體成功率。</span><span class="sxs-lookup"><span data-stu-id="3666d-146">This improves the overall success rate when provisioning the VMs.</span></span> <span data-ttu-id="3666d-147">若您未使用[受控磁碟](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-managed-disks)，則建議每個儲存體帳戶不要超過 20 部已啟用過度佈建的 VM，且不要超過 40 部已停用過度佈建的 VM。</span><span class="sxs-lookup"><span data-stu-id="3666d-147">If you are not using [managed disks](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-managed-disks), we recommend no more than 20 VMs per storage account with overprovisioning enabled, and no more than 40 VMs with overprovisioning disabled.</span></span>

<span data-ttu-id="3666d-148">有兩種基本方法可用來設定擴展集中部署的 VM：</span><span class="sxs-lookup"><span data-stu-id="3666d-148">There are two basic ways to configure VMs deployed in a scale set:</span></span>

- <span data-ttu-id="3666d-149">佈建 VM 之後，使用擴充功能來設定它。</span><span class="sxs-lookup"><span data-stu-id="3666d-149">Use extensions to configure the VM after it is provisioned.</span></span> <span data-ttu-id="3666d-150">透過此方法，新的 VM 執行個體可能需要較長的時間來啟動不具擴充功能的 VM。</span><span class="sxs-lookup"><span data-stu-id="3666d-150">With this approach, new VM instances may take longer to start up than a VM with no extensions.</span></span>

- <span data-ttu-id="3666d-151">利用自訂的磁碟映像來部署[受控磁碟](/azure/storage/storage-managed-disks-overview)。</span><span class="sxs-lookup"><span data-stu-id="3666d-151">Deploy a [managed disk](/azure/storage/storage-managed-disks-overview) with a custom disk image.</span></span> <span data-ttu-id="3666d-152">這個選項可能會更快速地部署。</span><span class="sxs-lookup"><span data-stu-id="3666d-152">This option may be quicker to deploy.</span></span> <span data-ttu-id="3666d-153">不過，它會要求您讓映像保持最新狀態。</span><span class="sxs-lookup"><span data-stu-id="3666d-153">However, it requires you to keep the image up to date.</span></span>

<span data-ttu-id="3666d-154">如需其他考量，請參閱[擴展集的設計考量][vmss-design]。</span><span class="sxs-lookup"><span data-stu-id="3666d-154">For additional considerations, see [Design considerations for scale sets][vmss-design].</span></span>

> [!TIP]
> <span data-ttu-id="3666d-155">使用任何自動調整規模解決方案時，事前也要利用生產層級的工作負載來進行測試。</span><span class="sxs-lookup"><span data-stu-id="3666d-155">When using any autoscale solution, test it with production-level workloads well in advance.</span></span>

<span data-ttu-id="3666d-156">如果您未使用擴展集，請考慮至少使用可用性設定組。</span><span class="sxs-lookup"><span data-stu-id="3666d-156">If you do not use a scale set, consider at least using an availability set.</span></span> <span data-ttu-id="3666d-157">在可用性設定組中至少建立兩部 VM，以支援[適用於 Azure VM 的可用性 SLA][vm-sla]。</span><span class="sxs-lookup"><span data-stu-id="3666d-157">Create at least two VMs in the availability set to support the [availability SLA for Azure VMs][vm-sla].</span></span> <span data-ttu-id="3666d-158">Azure 負載平衡器也會要求已負載平衡的 VM 屬於同一個可用性設定組。</span><span class="sxs-lookup"><span data-stu-id="3666d-158">The Azure load balancer also requires that load-balanced VMs belong to the same availability set.</span></span>

<span data-ttu-id="3666d-159">每個 Azure 訂用帳戶都已經有預設限制，包括每個區域的 VM 最大數目。</span><span class="sxs-lookup"><span data-stu-id="3666d-159">Each Azure subscription has default limits in place, including a maximum number of VMs per region.</span></span> <span data-ttu-id="3666d-160">您可以藉由提出支援要求來提高限制。</span><span class="sxs-lookup"><span data-stu-id="3666d-160">You can increase the limit by filing a support request.</span></span> <span data-ttu-id="3666d-161">如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束][subscription-limits]。</span><span class="sxs-lookup"><span data-stu-id="3666d-161">For more information, see [Azure subscription and service limits, quotas, and constraints][subscription-limits].</span></span>

### <a name="network-recommendations"></a><span data-ttu-id="3666d-162">網路建議</span><span class="sxs-lookup"><span data-stu-id="3666d-162">Network recommendations</span></span>

<span data-ttu-id="3666d-163">將 VM 部署到同一個子網路中。</span><span class="sxs-lookup"><span data-stu-id="3666d-163">Deploy the VMs within the same subnet.</span></span> <span data-ttu-id="3666d-164">不要直接將 VM 公開至網際網路，而是改為每個 VM 提供私人 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="3666d-164">Do not expose the VMs directly to the Internet, but instead give each VM a private IP address.</span></span> <span data-ttu-id="3666d-165">用戶端會使用負載平衡器的公用 IP 位址來連線。</span><span class="sxs-lookup"><span data-stu-id="3666d-165">Clients connect using the public IP address of the load balancer.</span></span>

<span data-ttu-id="3666d-166">若您需要登入負載平衡器後方的 VM，請考慮使用您可以登入的公用 IP 位址，來新增單一 VM 作為 jumpbox (也稱作防禦主機)。</span><span class="sxs-lookup"><span data-stu-id="3666d-166">If you need to log into the VMs behind the load balancer, consider adding a single VM as a jumpbox (also called a bastion host) with a public IP address you can log into.</span></span> <span data-ttu-id="3666d-167">然後從 Jumpbox 登入負載平衡器後方的 VM。</span><span class="sxs-lookup"><span data-stu-id="3666d-167">And then log into the VMs behind the load balancer from the jumpbox.</span></span> <span data-ttu-id="3666d-168">或者，您也可以設定負載平衡器的輸入網路位址轉譯 (NAT) 規則。</span><span class="sxs-lookup"><span data-stu-id="3666d-168">Alternatively, you can configure the load balancer's inbound network address translation (NAT) rules.</span></span> <span data-ttu-id="3666d-169">不過，當您裝載多層式架構工作負載或多個工作負載時，較好的解決方案是具備 Jumpbox。</span><span class="sxs-lookup"><span data-stu-id="3666d-169">However, having a jumpbox is a better solution when you are hosting n-tier workloads or multiple workloads.</span></span>

### <a name="load-balancer-recommendations"></a><span data-ttu-id="3666d-170">負載平衡器建議</span><span class="sxs-lookup"><span data-stu-id="3666d-170">Load balancer recommendations</span></span>

<span data-ttu-id="3666d-171">將可用性設定組中的所有 VM 新增至負載平衡器的後端位址集區。</span><span class="sxs-lookup"><span data-stu-id="3666d-171">Add all VMs in the availability set to the back-end address pool of the load balancer.</span></span>

<span data-ttu-id="3666d-172">定義負載平衡器規則，以將網路流量導向至 VM。</span><span class="sxs-lookup"><span data-stu-id="3666d-172">Define load balancer rules to direct network traffic to the VMs.</span></span> <span data-ttu-id="3666d-173">例如，若要啟用 HTTP 流量，請建立一個規則，將前端設定的連接埠 80 對應至後端位址集區的連接埠 80。</span><span class="sxs-lookup"><span data-stu-id="3666d-173">For example, to enable HTTP traffic, create a rule that maps port 80 from the front-end configuration to port 80 on the back-end address pool.</span></span> <span data-ttu-id="3666d-174">當用戶端將 HTTP 要求傳送到連接埠 80 時，負載平衡器會藉由使用[雜湊演算法][load-balancer-hashing] (其中包含來源 IP 位址) 來選取後端 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="3666d-174">When a client sends an HTTP request to port 80, the load balancer selects a back-end IP address by using a [hashing algorithm][load-balancer-hashing] that includes the source IP address.</span></span> <span data-ttu-id="3666d-175">如此一來，就會將用戶端要求散發到所有 VM。</span><span class="sxs-lookup"><span data-stu-id="3666d-175">In that way, client requests are distributed across all the VMs.</span></span>

<span data-ttu-id="3666d-176">若要將流量路由傳送到特定的 VM，請使用 NAT 規則。</span><span class="sxs-lookup"><span data-stu-id="3666d-176">To route traffic to a specific VM, use NAT rules.</span></span> <span data-ttu-id="3666d-177">例如，若要啟用 RDP 到 VM，可為每個 VM 建立不同的 NAT 規則。</span><span class="sxs-lookup"><span data-stu-id="3666d-177">For example, to enable RDP to the VMs, create a separate NAT rule for each VM.</span></span> <span data-ttu-id="3666d-178">每個規則都應該將不同的連接埠號碼對應至連接埠 3389 (RDP 的預設連接埠)。</span><span class="sxs-lookup"><span data-stu-id="3666d-178">Each rule should map a distinct port number to port 3389, the default port for RDP.</span></span> <span data-ttu-id="3666d-179">例如，針對 "VM1" 使用連接埠 50001、針對 "VM2" 使用連接埠 50002，依此類推。</span><span class="sxs-lookup"><span data-stu-id="3666d-179">For example, use port 50001 for "VM1," port 50002 for "VM2," and so on.</span></span> <span data-ttu-id="3666d-180">將 NAT 規則指派給 VM 上的 NIC。</span><span class="sxs-lookup"><span data-stu-id="3666d-180">Assign the NAT rules to the NICs on the VMs.</span></span>

### <a name="storage-account-recommendations"></a><span data-ttu-id="3666d-181">儲存體帳戶建議</span><span class="sxs-lookup"><span data-stu-id="3666d-181">Storage account recommendations</span></span>

<span data-ttu-id="3666d-182">我們建議搭配使用[受控磁碟](/azure/storage/storage-managed-disks-overview)與[進階儲存體][premium]。</span><span class="sxs-lookup"><span data-stu-id="3666d-182">We recommend the use of [managed disks](/azure/storage/storage-managed-disks-overview) with [premium storage][premium].</span></span> <span data-ttu-id="3666d-183">受控磁碟不需要儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="3666d-183">Managed disks do not require a storage account.</span></span> <span data-ttu-id="3666d-184">您只需指定磁碟的大小和類型，它就會以高度可用的資源方式進行部署。</span><span class="sxs-lookup"><span data-stu-id="3666d-184">You simply specify the size and type of disk and it is deployed as a highly available resource.</span></span>

<span data-ttu-id="3666d-185">若您使用的是非受控的磁碟，請針對每部 VM 建立個別的 Azure 儲存體帳戶來保存虛擬硬碟 (VHD)，以避免達到儲存體帳戶的每秒輸入/輸出作業 [(IOPS) 限制][vm-disk-limits]。</span><span class="sxs-lookup"><span data-stu-id="3666d-185">If you are using unmanaged disks, create separate Azure storage accounts for each VM to hold the virtual hard disks (VHDs), in order to avoid hitting the input/output operations per second [(IOPS) limits][vm-disk-limits] for storage accounts.</span></span>

<span data-ttu-id="3666d-186">建立一個儲存體帳戶以供診斷記錄使用。</span><span class="sxs-lookup"><span data-stu-id="3666d-186">Create one storage account for diagnostic logs.</span></span> <span data-ttu-id="3666d-187">所有 VM 都可共用這個儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="3666d-187">This storage account can be shared by all the VMs.</span></span> <span data-ttu-id="3666d-188">這可以是使用標準磁碟之未受管理的儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="3666d-188">This can be an unmanaged storage account using standard disks.</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="3666d-189">可用性考量</span><span class="sxs-lookup"><span data-stu-id="3666d-189">Availability considerations</span></span>

<span data-ttu-id="3666d-190">可用性設定組可讓您的應用程式能以更彈性的方式處理已計劃或尚未計劃的維護事件。</span><span class="sxs-lookup"><span data-stu-id="3666d-190">The availability set makes your application more resilient to both planned and unplanned maintenance events.</span></span>

* <span data-ttu-id="3666d-191">「已計劃的維護」會在 Microsoft 更新基礎平台時發生，有時會導致 VM 重新啟動。</span><span class="sxs-lookup"><span data-stu-id="3666d-191">*Planned maintenance* occurs when Microsoft updates the underlying platform, sometimes causing VMs to be restarted.</span></span> <span data-ttu-id="3666d-192">Azure 確保可用性設定組中的 VM 不會全部同時重新啟動。</span><span class="sxs-lookup"><span data-stu-id="3666d-192">Azure makes sure the VMs in an availability set are not all restarted at the same time.</span></span> <span data-ttu-id="3666d-193">當其他 VM 正在重新啟動時，至少會讓其中一個保留執行狀態。</span><span class="sxs-lookup"><span data-stu-id="3666d-193">At least one is kept running while others are restarting.</span></span>
* <span data-ttu-id="3666d-194">「尚未計劃的維護」會在硬體故障時發生。</span><span class="sxs-lookup"><span data-stu-id="3666d-194">*Unplanned maintenance* happens if there is a hardware failure.</span></span> <span data-ttu-id="3666d-195">Azure 確保可用性設定組中的 VM 會跨多個伺服器機架進行佈建。</span><span class="sxs-lookup"><span data-stu-id="3666d-195">Azure makes sure that VMs in an availability set are provisioned across more than one server rack.</span></span> <span data-ttu-id="3666d-196">這有助於降低對於硬體故障、網路中斷、電源中斷等的影響。</span><span class="sxs-lookup"><span data-stu-id="3666d-196">This helps to reduce the impact of hardware failures, network outages, power interruptions, and so on.</span></span>

<span data-ttu-id="3666d-197">如需詳細資訊，請參閱[管理虛擬機器的可用性][availability-set]。</span><span class="sxs-lookup"><span data-stu-id="3666d-197">For more information, see [Manage the availability of virtual machines][availability-set].</span></span> <span data-ttu-id="3666d-198">下列影片也提供適合可用性設定組的概觀：[如何設定可用性設定組來調整 VM][availability-set-ch9]。</span><span class="sxs-lookup"><span data-stu-id="3666d-198">The following video also provides a good overview of availability sets: [How Do I Configure an Availability Set to Scale VMs][availability-set-ch9].</span></span>

> [!WARNING]
> <span data-ttu-id="3666d-199">請確定會在佈建 VM 時設定可用性設定組。</span><span class="sxs-lookup"><span data-stu-id="3666d-199">Make sure to configure the availability set when you provision the VM.</span></span> <span data-ttu-id="3666d-200">目前沒有任何方法可在佈建 VM 之後，將資源管理員 VM 新增至可用性設定組。</span><span class="sxs-lookup"><span data-stu-id="3666d-200">Currently, there is no way to add a Resource Manager VM to an availability set after the VM is provisioned.</span></span>

<span data-ttu-id="3666d-201">負載平衡器會使用[健康情況探查][health-probes]來監視 VM 執行個體的可用性。</span><span class="sxs-lookup"><span data-stu-id="3666d-201">The load balancer uses [health probes][health-probes] to monitor the availability of VM instances.</span></span> <span data-ttu-id="3666d-202">如果探查無法在逾時期間內連線到執行個體，負載平衡器就會停止將流量傳送到該 VM。</span><span class="sxs-lookup"><span data-stu-id="3666d-202">If a probe cannot reach an instance within a timeout period, the load balancer stops sending traffic to that VM.</span></span> <span data-ttu-id="3666d-203">不過，負載平衡器將會繼續探查，而且如果 VM 再次變成可用，負載平衡器就會繼續將流量傳送到該 VM。</span><span class="sxs-lookup"><span data-stu-id="3666d-203">However, the load balancer will continue to probe, and if the VM becomes available again, the load balancer resumes sending traffic to that VM.</span></span>

<span data-ttu-id="3666d-204">以下是關於負載平衡器健康情況探查的建議：</span><span class="sxs-lookup"><span data-stu-id="3666d-204">Here are some recommendations on load balancer health probes:</span></span>

* <span data-ttu-id="3666d-205">探查可以測試 HTTP 或 TCP。</span><span class="sxs-lookup"><span data-stu-id="3666d-205">Probes can test either HTTP or TCP.</span></span> <span data-ttu-id="3666d-206">如果您的 VM 執行 HTTP 伺服器，請建立 HTTP 探查。</span><span class="sxs-lookup"><span data-stu-id="3666d-206">If your VMs run an HTTP server, create an HTTP probe.</span></span> <span data-ttu-id="3666d-207">否則，建立 TCP 探查。</span><span class="sxs-lookup"><span data-stu-id="3666d-207">Otherwise create a TCP probe.</span></span>
* <span data-ttu-id="3666d-208">針對 HTTP 探查，指定 HTTP 端點的路徑。</span><span class="sxs-lookup"><span data-stu-id="3666d-208">For an HTTP probe, specify the path to an HTTP endpoint.</span></span> <span data-ttu-id="3666d-209">探查會檢查來自此路徑的 HTTP 200 回應。</span><span class="sxs-lookup"><span data-stu-id="3666d-209">The probe checks for an HTTP 200 response from this path.</span></span> <span data-ttu-id="3666d-210">這可以是根路徑 ("/")，或是實作一些自訂邏輯來檢查應用程式健康情況的健康情況監視端點。</span><span class="sxs-lookup"><span data-stu-id="3666d-210">This can be the root path ("/"), or a health-monitoring endpoint that implements some custom logic to check the health of the application.</span></span> <span data-ttu-id="3666d-211">端點必須允許匿名的 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="3666d-211">The endpoint must allow anonymous HTTP requests.</span></span>
* <span data-ttu-id="3666d-212">探查會從[已知的 IP 位址][health-probe-ip] 168.63.129.16 傳送出來。</span><span class="sxs-lookup"><span data-stu-id="3666d-212">The probe is sent from a [known IP address][health-probe-ip], 168.63.129.16.</span></span> <span data-ttu-id="3666d-213">請確定您並未在任何防火牆原則或網路安全性群組 (NSG) 規則中封鎖往返此 IP 位址的流量。</span><span class="sxs-lookup"><span data-stu-id="3666d-213">Make sure you don't block traffic to or from this IP address in any firewall policies or network security group (NSG) rules.</span></span>
* <span data-ttu-id="3666d-214">使用[健康情況探查記錄][health-probe-log]來檢視健康情況探查的狀態。</span><span class="sxs-lookup"><span data-stu-id="3666d-214">Use [health probe logs][health-probe-log] to view the status of the health probes.</span></span> <span data-ttu-id="3666d-215">能夠針對每個負載平衡器登入 Azure 入口網站。</span><span class="sxs-lookup"><span data-stu-id="3666d-215">Enable logging in the Azure portal for each load balancer.</span></span> <span data-ttu-id="3666d-216">記錄會寫入 Azure Blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="3666d-216">Logs are written to Azure Blob storage.</span></span> <span data-ttu-id="3666d-217">記錄會顯示後端有多少個 VM 因探查回應失敗而不會接收網路流量。</span><span class="sxs-lookup"><span data-stu-id="3666d-217">The logs show how many VMs on the back end are not receiving network traffic due to failed probe responses.</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="3666d-218">管理性考量</span><span class="sxs-lookup"><span data-stu-id="3666d-218">Manageability considerations</span></span>

<span data-ttu-id="3666d-219">如果有多個 VM，請務必將程序自動化，如此一來，它們就會更可靠且可重複。</span><span class="sxs-lookup"><span data-stu-id="3666d-219">With multiple VMs, it is important to automate processes so they are reliable and repeatable.</span></span> <span data-ttu-id="3666d-220">您可以使用 [Azure 自動化][azure-automation]，來將部署、OS 修補及其他工作自動化。</span><span class="sxs-lookup"><span data-stu-id="3666d-220">You can use [Azure Automation][azure-automation] to automate deployment, OS patching, and other tasks.</span></span> <span data-ttu-id="3666d-221">[Azure 自動化][azure-automation]是以 Powershell 為基礎且可針對這個使用的自動化服務。</span><span class="sxs-lookup"><span data-stu-id="3666d-221">[Azure Automation][azure-automation] is an automation service based on PowerShell that can be used for this.</span></span> <span data-ttu-id="3666d-222">範例自動化指令碼可從 [Runbook 資源庫][runbook-gallery]中取得。</span><span class="sxs-lookup"><span data-stu-id="3666d-222">Example automation scripts are available from the [Runbook Gallery][runbook-gallery].</span></span>

## <a name="security-considerations"></a><span data-ttu-id="3666d-223">安全性考量</span><span class="sxs-lookup"><span data-stu-id="3666d-223">Security considerations</span></span>

<span data-ttu-id="3666d-224">虛擬網路是 Azure 中的流量隔離界限。</span><span class="sxs-lookup"><span data-stu-id="3666d-224">Virtual networks are a traffic isolation boundary in Azure.</span></span> <span data-ttu-id="3666d-225">某一個 VNet 中的 VM 無法直接與不同 VNet 中的 VM 通訊。</span><span class="sxs-lookup"><span data-stu-id="3666d-225">VMs in one VNet cannot communicate directly with VMs in a different VNet.</span></span> <span data-ttu-id="3666d-226">除非您建立[網路安全性群組][nsg] (NSG) 來限制流量，否則，相同 VNet 中的 VM 可以彼此通訊。</span><span class="sxs-lookup"><span data-stu-id="3666d-226">VMs within the same VNet can communicate, unless you create [network security groups][nsg] (NSGs) to restrict traffic.</span></span> <span data-ttu-id="3666d-227">如需詳細資訊，請參閱 [Microsoft 雲端服務和網路安全性][network-security]。</span><span class="sxs-lookup"><span data-stu-id="3666d-227">For more information, see [Microsoft cloud services and network security][network-security].</span></span>

<span data-ttu-id="3666d-228">針對傳入的網際網路流量，負載平衡器規則會定義哪些流量可以連線到後端。</span><span class="sxs-lookup"><span data-stu-id="3666d-228">For incoming Internet traffic, the load balancer rules define which traffic can reach the back end.</span></span> <span data-ttu-id="3666d-229">不過，負載平衡器規則不支援 IP 安全清單，因此，如果您想要將特定的公用 IP 位址新增至安全清單，請將 NSG 新增至子網路。</span><span class="sxs-lookup"><span data-stu-id="3666d-229">However, load balancer rules don't support IP safe lists, so if you want to add certain public IP addresses to a safe list, add an NSG to the subnet.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="3666d-230">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="3666d-230">Deploy the solution</span></span>

<span data-ttu-id="3666d-231">適用於此架構的部署可在 [GitHub][github-folder] 上取得。</span><span class="sxs-lookup"><span data-stu-id="3666d-231">A deployment for this architecture is available on [GitHub][github-folder].</span></span> <span data-ttu-id="3666d-232">它會部署下列各項：</span><span class="sxs-lookup"><span data-stu-id="3666d-232">It deploys the following:</span></span>

  * <span data-ttu-id="3666d-233">包含 VM 且具有名為 **web** 之單一子網路的虛擬網路。</span><span class="sxs-lookup"><span data-stu-id="3666d-233">A virtual network with a single subnet named **web** that contains the VMs.</span></span>
  * <span data-ttu-id="3666d-234">VM 擴展集，其中包含執行最新版 Windows Server 2016 Datacenter Edition 的 VM。</span><span class="sxs-lookup"><span data-stu-id="3666d-234">A VM scale set that contains VMs running the latest version of Windows Server 2016 Datacenter Edition.</span></span> <span data-ttu-id="3666d-235">已啟用自動調整規模。</span><span class="sxs-lookup"><span data-stu-id="3666d-235">Autoscale is enabled.</span></span>
  * <span data-ttu-id="3666d-236">位於 VM 擴展集前方的負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="3666d-236">A load balancer that sits in front of the VM scale set.</span></span>
  * <span data-ttu-id="3666d-237">含有傳入規則的 NSG，可允許 HTTP 流量通往 VM 擴展集。</span><span class="sxs-lookup"><span data-stu-id="3666d-237">An NSG with incoming rules that allow HTTP traffic to the VM scale set.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="3666d-238">必要條件</span><span class="sxs-lookup"><span data-stu-id="3666d-238">Prerequisites</span></span>

<span data-ttu-id="3666d-239">在您可以將參考架構部署到自己的訂用帳戶之前，必須執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="3666d-239">Before you can deploy the reference architecture to your own subscription, you must perform the following steps.</span></span>

1. <span data-ttu-id="3666d-240">複製、派生或下載適用於 [AzureCAT 參考架構][ref-arch-repo] GitHub 存放庫的 zip 檔案。</span><span class="sxs-lookup"><span data-stu-id="3666d-240">Clone, fork, or download the zip file for the [AzureCAT reference architectures][ref-arch-repo] GitHub repository.</span></span>

2. <span data-ttu-id="3666d-241">確定您已在電腦上安裝 Azure CLI 2.0。</span><span class="sxs-lookup"><span data-stu-id="3666d-241">Make sure you have the Azure CLI 2.0 installed on your computer.</span></span> <span data-ttu-id="3666d-242">如需 CLI 安裝指示，請參閱[安裝 Azure CLI 2.0][azure-cli-2]。</span><span class="sxs-lookup"><span data-stu-id="3666d-242">For CLI installation instructions, see [Install Azure CLI 2.0][azure-cli-2].</span></span>

3. <span data-ttu-id="3666d-243">安裝 [Azure 建置組塊][azbb] npm 封裝。</span><span class="sxs-lookup"><span data-stu-id="3666d-243">Install the [Azure building blocks][azbb] npm package.</span></span>

4. <span data-ttu-id="3666d-244">從命令提示字元、bash 提示字元或 PowerShell 提示字元中，使用下列其中一個命令登入 Azure 帳戶，並遵循提示進行。</span><span class="sxs-lookup"><span data-stu-id="3666d-244">From a command prompt, bash prompt, or PowerShell prompt, login to your Azure account by using one of the commands below, and follow the prompts.</span></span>

  ```bash
  az login
  ```

### <a name="deploy-the-solution-using-azbb"></a><span data-ttu-id="3666d-245">使用 azbb 部署解決方案</span><span class="sxs-lookup"><span data-stu-id="3666d-245">Deploy the solution using azbb</span></span>

<span data-ttu-id="3666d-246">若要部署範例單一 VM 工作負載，請依照下列步驟執行：</span><span class="sxs-lookup"><span data-stu-id="3666d-246">To deploy the sample single VM workload, follow these steps:</span></span>

1. <span data-ttu-id="3666d-247">瀏覽至您在上述必要條件步驟中所下載存放庫的 `virtual-machines\multi-vm\parameters\windows` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3666d-247">Navigate to the `virtual-machines\multi-vm\parameters\windows` folder for the repository you downloaded in the pre-requisites step above.</span></span>

2. <span data-ttu-id="3666d-248">開啟 `multi-vm-v2.json` 檔案，然後在引號之間輸入使用者名稱和密碼，如下所示，然後儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="3666d-248">Open the `multi-vm-v2.json` file and enter a username and password between the quotes, as shown below, then save the file.</span></span>

  ```bash
  "adminUsername": "",
  "adminPassword": "",
  ```

3. <span data-ttu-id="3666d-249">執行 `azbb` 以部署 VM，如下所示。</span><span class="sxs-lookup"><span data-stu-id="3666d-249">Run `azbb` to deploy the VMs as shown below.</span></span>

  ```bash
  azbb -s <subscription_id> -g <resource_group_name> -l <location> -p multi-vm-v2.json --deploy
  ```

<span data-ttu-id="3666d-250">如需部署此範例參考架構的詳細資訊，請瀏覽我們的 [GitHub 存放庫][git]。</span><span class="sxs-lookup"><span data-stu-id="3666d-250">For more information on deploying this sample reference architecture, visit our [GitHub repository][git].</span></span>

<!-- links -->

[availability-set]: /azure/virtual-machines/virtual-machines-windows-manage-availability
[availability-set-ch9]: https://channel9.msdn.com/Series/Microsoft-Azure-Fundamentals-Virtual-Machines/08
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[azure-automation]: /azure/automation/automation-intro
[azure-cli]: /azure/virtual-machines-command-line-tools
[azure-cli-2]: /azure/install-azure-cli?view=azure-cli-latest
[git]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/multi-vm
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/multi-vm
[health-probe-log]: /azure/load-balancer/load-balancer-monitor-log
[health-probes]: /azure/load-balancer/load-balancer-overview#load-balancer-features
[health-probe-ip]: /azure/virtual-network/virtual-networks-nsg#special-rules
[load-balancer]: /azure/load-balancer/load-balancer-get-started-internet-arm-cli
[load-balancer-hashing]: /azure/load-balancer/load-balancer-overview#load-balancer-features
[naming-conventions]: ../../best-practices/naming-conventions.md
[network-security]: /azure/best-practices-network-security
[nsg]: /azure/virtual-network/virtual-networks-nsg
[premium]: /azure/storage/common/storage-premium-storage
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview 
[runbook-gallery]: /azure/automation/automation-runbook-gallery#runbooks-in-runbook-gallery
[single-vm]: single-vm.md
[subscription-limits]: /azure/azure-subscription-service-limits
[visio-download]: https://archcenter.azureedge.net/cdn/vm-reference-architectures.vsdx
[vm-disk-limits]: /azure/azure-subscription-service-limits#virtual-machine-disk-limits
[vm-scaleset]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview
[vm-sizes]: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_2/
[vmss]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview
[vmss-design]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview
[vmss-quickstart]: https://azure.microsoft.com/documentation/templates/?term=scale+set
[0]: ./images/multi-vm-diagram.png "Azure 中多個 VM 的解決方案架構是由具有兩個 VM 和一個負載平衡器的可用性設定組所組成"

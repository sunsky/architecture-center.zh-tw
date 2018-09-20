---
title: 具有 Apache Cassandra 的多層式架構 (N-tier) 應用程式
description: 如何在 Microsoft Azure 上執行適用於多層式架構的 Linux VM。
author: MikeWasson
ms.date: 05/03/2018
ms.openlocfilehash: fa5faeda4ef1dcae46181c0a3be8f4e139dc27d0
ms.sourcegitcommit: 25bf02e89ab4609ae1b2eb4867767678a9480402
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2018
ms.locfileid: "45584709"
---
# <a name="n-tier-application-with-apache-cassandra"></a><span data-ttu-id="6e49a-103">具有 Apache Cassandra 的多層式架構 (N-tier) 應用程式</span><span class="sxs-lookup"><span data-stu-id="6e49a-103">N-tier application with Apache Cassandra</span></span>

<span data-ttu-id="6e49a-104">此參考架構展示如何在 Linux 上使用 Apache Cassandra 作為資料層，來部署針對多層式架構 (N-Tier) 應用程式設定的 VM 和虛擬網路。</span><span class="sxs-lookup"><span data-stu-id="6e49a-104">This reference architecture shows how to deploy VMs and a virtual network configured for an N-tier application, using Apache Cassandra on Linux for the data tier.</span></span> [<span data-ttu-id="6e49a-105">**部署這個解決方案**。</span><span class="sxs-lookup"><span data-stu-id="6e49a-105">**Deploy this solution**.</span></span>](#deploy-the-solution) 

<span data-ttu-id="6e49a-106">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="6e49a-106">![[0]][0]</span></span>

<span data-ttu-id="6e49a-107">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="6e49a-107">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="6e49a-108">架構</span><span class="sxs-lookup"><span data-stu-id="6e49a-108">Architecture</span></span> 

<span data-ttu-id="6e49a-109">此架構具有下列元件：</span><span class="sxs-lookup"><span data-stu-id="6e49a-109">The architecture has the following components:</span></span>

* <span data-ttu-id="6e49a-110">**資源群組。**</span><span class="sxs-lookup"><span data-stu-id="6e49a-110">**Resource group.**</span></span> <span data-ttu-id="6e49a-111">[資源群組][resource-manager-overview]可用來將資源組合在一起，讓它們可以依存留期、擁有者或其他準則管理。</span><span class="sxs-lookup"><span data-stu-id="6e49a-111">[Resource groups][resource-manager-overview] are used to group resources so they can be managed by lifetime, owner, or other criteria.</span></span>

* <span data-ttu-id="6e49a-112">**虛擬網路 (VNet) 和子網路。**</span><span class="sxs-lookup"><span data-stu-id="6e49a-112">**Virtual network (VNet) and subnets.**</span></span> <span data-ttu-id="6e49a-113">每部 Azure VM 都會部署到可以分割成多個子網路的 VNet。</span><span class="sxs-lookup"><span data-stu-id="6e49a-113">Every Azure VM is deployed into a VNet that can be segmented into multiple subnets.</span></span> <span data-ttu-id="6e49a-114">針對每一層建立不同的子網路。</span><span class="sxs-lookup"><span data-stu-id="6e49a-114">Create a separate subnet for each tier.</span></span> 

* <span data-ttu-id="6e49a-115">**NSG。**</span><span class="sxs-lookup"><span data-stu-id="6e49a-115">**NSGs.**</span></span> <span data-ttu-id="6e49a-116">使用[網路安全性群組][nsg] (NSG) 來限制 VNet 內的網路流量。</span><span class="sxs-lookup"><span data-stu-id="6e49a-116">Use [network security groups][nsg] (NSGs) to restrict network traffic within the VNet.</span></span> <span data-ttu-id="6e49a-117">例如，在如下所示的 3 層式架構中，資料庫層不接受來自 Web 前端的流量，只接受來自 Business 層和管理子網路的流量。</span><span class="sxs-lookup"><span data-stu-id="6e49a-117">For example, in the 3-tier architecture shown here, the database tier does not accept traffic from the web front end, only from the business tier and the management subnet.</span></span>

* <span data-ttu-id="6e49a-118">**虛擬機器**。</span><span class="sxs-lookup"><span data-stu-id="6e49a-118">**Virtual machines**.</span></span> <span data-ttu-id="6e49a-119">如需有關設定 VM 的建議，請參閱[在 Azure 上執行 Windows VM](./windows-vm.md) 和[在 Azure 上執行 Linux VM](./linux-vm.md)。</span><span class="sxs-lookup"><span data-stu-id="6e49a-119">For recommendations on configuring VMs, see [Run a Windows VM on Azure](./windows-vm.md) and [Run a Linux VM on Azure](./linux-vm.md).</span></span>

* <span data-ttu-id="6e49a-120">**可用性設定組。**</span><span class="sxs-lookup"><span data-stu-id="6e49a-120">**Availability sets.**</span></span> <span data-ttu-id="6e49a-121">針對每一層建立[可用性設定組][azure-availability-sets]，並且在每一層中至少佈建兩個 VM。</span><span class="sxs-lookup"><span data-stu-id="6e49a-121">Create an [availability set][azure-availability-sets] for each tier, and provision at least two VMs in each tier.</span></span> <span data-ttu-id="6e49a-122">這讓 VM 能夠符合適用於 VM 之較高[服務等級協定 (SLA)][vm-sla] 的資格。</span><span class="sxs-lookup"><span data-stu-id="6e49a-122">This makes the VMs eligible for a higher [service level agreement (SLA)][vm-sla] for VMs.</span></span> 

* <span data-ttu-id="6e49a-123">**VM 擴展集** (未顯示)。</span><span class="sxs-lookup"><span data-stu-id="6e49a-123">**VM scale set** (not shown).</span></span> <span data-ttu-id="6e49a-124">[VM 擴展集][vmss]是使用可用性設定組的替代方案。</span><span class="sxs-lookup"><span data-stu-id="6e49a-124">A [VM scale set][vmss] is an alternative to using an availability set.</span></span> <span data-ttu-id="6e49a-125">擴展集可讓您輕鬆地根據預先定義的規則，手動或自動相應放大層中的 VM。</span><span class="sxs-lookup"><span data-stu-id="6e49a-125">A scale sets makes it easy to scale out the VMs in a tier, either manually or automatically based on predefined rules.</span></span>

* <span data-ttu-id="6e49a-126">**Azure 負載平衡器。**</span><span class="sxs-lookup"><span data-stu-id="6e49a-126">**Azure Load balancers.**</span></span> <span data-ttu-id="6e49a-127">[負載平衡器][load-balancer]會將連入網際網路要求散發到 VM 執行個體。</span><span class="sxs-lookup"><span data-stu-id="6e49a-127">The [load balancers][load-balancer] distribute incoming Internet requests to the VM instances.</span></span> <span data-ttu-id="6e49a-128">使用[公用負載平衡器][load-balancer-external]，可將連入的網際網路流量散發到 Web 層，使用[內部負載平衡器][load-balancer-internal]，則可將來自 Web 層的網路流量散發到 Business 層。</span><span class="sxs-lookup"><span data-stu-id="6e49a-128">Use a [public load balancer][load-balancer-external] to distribute incoming Internet traffic to the web tier, and an [internal load balancer][load-balancer-internal] to distribute network traffic from the web tier to the business tier.</span></span>

* <span data-ttu-id="6e49a-129">**公用 IP 位址**。</span><span class="sxs-lookup"><span data-stu-id="6e49a-129">**Public IP address**.</span></span> <span data-ttu-id="6e49a-130">公用負載平衡器需要公用 IP 位址才能接收網際網路流量。</span><span class="sxs-lookup"><span data-stu-id="6e49a-130">A public IP address is needed for the public load balancer to receive Internet traffic.</span></span>

* <span data-ttu-id="6e49a-131">**Jumpbox。**</span><span class="sxs-lookup"><span data-stu-id="6e49a-131">**Jumpbox.**</span></span> <span data-ttu-id="6e49a-132">也稱為[防禦主機]。</span><span class="sxs-lookup"><span data-stu-id="6e49a-132">Also called a [bastion host].</span></span> <span data-ttu-id="6e49a-133">網路上系統管理員用來連線到其他 VM 的安全 VM。</span><span class="sxs-lookup"><span data-stu-id="6e49a-133">A secure VM on the network that administrators use to connect to the other VMs.</span></span> <span data-ttu-id="6e49a-134">Jumpbox 具有 NSG，能夠儘允許來自安全清單上公用 IP 位址的遠端流量。</span><span class="sxs-lookup"><span data-stu-id="6e49a-134">The jumpbox has an NSG that allows remote traffic only from public IP addresses on a safe list.</span></span> <span data-ttu-id="6e49a-135">NSG 應該允許安全殼層 (SSH) 流量。</span><span class="sxs-lookup"><span data-stu-id="6e49a-135">The NSG should permit ssh traffic.</span></span>

* <span data-ttu-id="6e49a-136">**Apache Cassandra 資料庫**。</span><span class="sxs-lookup"><span data-stu-id="6e49a-136">**Apache Cassandra database**.</span></span> <span data-ttu-id="6e49a-137">藉由啟用複寫和容錯移轉，在資料層提供高可用性。</span><span class="sxs-lookup"><span data-stu-id="6e49a-137">Provides high availability at the data tier, by enabling replication and failover.</span></span>

* <span data-ttu-id="6e49a-138">**Azure DNS**。</span><span class="sxs-lookup"><span data-stu-id="6e49a-138">**Azure DNS**.</span></span> <span data-ttu-id="6e49a-139">[Azure DNS][azure-dns] 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構提供名稱解析。</span><span class="sxs-lookup"><span data-stu-id="6e49a-139">[Azure DNS][azure-dns] is a hosting service for DNS domains, providing name resolution using Microsoft Azure infrastructure.</span></span> <span data-ttu-id="6e49a-140">只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。</span><span class="sxs-lookup"><span data-stu-id="6e49a-140">By hosting your domains in Azure, you can manage your DNS records using the same credentials, APIs, tools, and billing as your other Azure services.</span></span>

## <a name="recommendations"></a><span data-ttu-id="6e49a-141">建議</span><span class="sxs-lookup"><span data-stu-id="6e49a-141">Recommendations</span></span>

<span data-ttu-id="6e49a-142">您的需求可能和此處所述的架構不同。</span><span class="sxs-lookup"><span data-stu-id="6e49a-142">Your requirements might differ from the architecture described here.</span></span> <span data-ttu-id="6e49a-143">請使用以下建議作為起點。</span><span class="sxs-lookup"><span data-stu-id="6e49a-143">Use these recommendations as a starting point.</span></span> 

### <a name="vnet--subnets"></a><span data-ttu-id="6e49a-144">VNet / 子網路</span><span class="sxs-lookup"><span data-stu-id="6e49a-144">VNet / Subnets</span></span>

<span data-ttu-id="6e49a-145">當您建立 VNet 時，請判斷您在每個子網路中的資源需要多少個 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="6e49a-145">When you create the VNet, determine how many IP addresses your resources in each subnet require.</span></span> <span data-ttu-id="6e49a-146">使用 [CIDR] 標記法，針對所需的 IP 位址指定子網路遮罩和夠大的 VNet 位址範圍。</span><span class="sxs-lookup"><span data-stu-id="6e49a-146">Specify a subnet mask and a VNet address range large enough for the required IP addresses, using [CIDR] notation.</span></span> <span data-ttu-id="6e49a-147">使用落在標準[私人 IP 位址區塊][private-ip-space]內的位址空間，其為 10.0.0.0/8、172.16.0.0/12 及 192.168.0.0/16。</span><span class="sxs-lookup"><span data-stu-id="6e49a-147">Use an address space that falls within the standard [private IP address blocks][private-ip-space], which are 10.0.0.0/8, 172.16.0.0/12, and 192.168.0.0/16.</span></span>

<span data-ttu-id="6e49a-148">選擇不會與您的內部部署網路重疊的位址範圍，以防您稍後需要在 VNet 和您的內部部署網路之間設定閘道。</span><span class="sxs-lookup"><span data-stu-id="6e49a-148">Choose an address range that does not overlap with your on-premises network, in case you need to set up a gateway between the VNet and your on-premise network later.</span></span> <span data-ttu-id="6e49a-149">一旦建立 VNet 之後，就無法變更位址範圍。</span><span class="sxs-lookup"><span data-stu-id="6e49a-149">Once you create the VNet, you can't change the address range.</span></span>

<span data-ttu-id="6e49a-150">請記得以功能和安全性需求來設計子網路。</span><span class="sxs-lookup"><span data-stu-id="6e49a-150">Design subnets with functionality and security requirements in mind.</span></span> <span data-ttu-id="6e49a-151">位於相同層或角色的所有 VM 都應移入相同的子網路，它可以是安全性界限。</span><span class="sxs-lookup"><span data-stu-id="6e49a-151">All VMs within the same tier or role should go into the same subnet, which can be a security boundary.</span></span> <span data-ttu-id="6e49a-152">如需設計 VNet 和子網路的詳細資訊，請參閱[規劃和設計 Azure 虛擬網路][plan-network]。</span><span class="sxs-lookup"><span data-stu-id="6e49a-152">For more information about designing VNets and subnets, see [Plan and design Azure Virtual Networks][plan-network].</span></span>

### <a name="load-balancers"></a><span data-ttu-id="6e49a-153">負載平衡器</span><span class="sxs-lookup"><span data-stu-id="6e49a-153">Load balancers</span></span>

<span data-ttu-id="6e49a-154">不要直接將 VM 公開至網際網路，而是改為每個 VM 提供私人 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="6e49a-154">Do not expose the VMs directly to the Internet, but instead give each VM a private IP address.</span></span> <span data-ttu-id="6e49a-155">用戶端會使用公用負載平衡器的 IP 位址來連線。</span><span class="sxs-lookup"><span data-stu-id="6e49a-155">Clients connect using the IP address of the public load balancer.</span></span>

<span data-ttu-id="6e49a-156">定義負載平衡器規則，以將網路流量導向至 VM。</span><span class="sxs-lookup"><span data-stu-id="6e49a-156">Define load balancer rules to direct network traffic to the VMs.</span></span> <span data-ttu-id="6e49a-157">例如，若要啟用 HTTP 流量，請建立一個規則，將前端設定的連接埠 80 對應至後端位址集區的連接埠 80。</span><span class="sxs-lookup"><span data-stu-id="6e49a-157">For example, to enable HTTP traffic, create a rule that maps port 80 from the front-end configuration to port 80 on the back-end address pool.</span></span> <span data-ttu-id="6e49a-158">當用戶端將 HTTP 要求傳送到連接埠 80 時，負載平衡器會藉由使用[雜湊演算法][load-balancer-hashing] (其中包含來源 IP 位址) 來選取後端 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="6e49a-158">When a client sends an HTTP request to port 80, the load balancer selects a back-end IP address by using a [hashing algorithm][load-balancer-hashing] that includes the source IP address.</span></span> <span data-ttu-id="6e49a-159">如此一來，就會將用戶端要求散發到所有 VM。</span><span class="sxs-lookup"><span data-stu-id="6e49a-159">In that way, client requests are distributed across all the VMs.</span></span>

### <a name="network-security-groups"></a><span data-ttu-id="6e49a-160">網路安全性群組</span><span class="sxs-lookup"><span data-stu-id="6e49a-160">Network security groups</span></span>

<span data-ttu-id="6e49a-161">使用 NSG 規則來限制各層之間的流量。</span><span class="sxs-lookup"><span data-stu-id="6e49a-161">Use NSG rules to restrict traffic between tiers.</span></span> <span data-ttu-id="6e49a-162">例如，在如上所示的 3 層式架構中，Web 層不會直接與資料庫層通訊。</span><span class="sxs-lookup"><span data-stu-id="6e49a-162">For example, in the 3-tier architecture shown above, the web tier does not communicate directly with the database tier.</span></span> <span data-ttu-id="6e49a-163">若要強制執行此動作，資料庫層應該封鎖來自 Web 層子網路的連入流量。</span><span class="sxs-lookup"><span data-stu-id="6e49a-163">To enforce this, the database tier should block incoming traffic from the web tier subnet.</span></span>  

1. <span data-ttu-id="6e49a-164">拒絕來自 VNet 的所有輸入流量。</span><span class="sxs-lookup"><span data-stu-id="6e49a-164">Deny all inbound traffic from the VNet.</span></span> <span data-ttu-id="6e49a-165">(在規則中使用 `VIRTUAL_NETWORK` 標記)。</span><span class="sxs-lookup"><span data-stu-id="6e49a-165">(Use the `VIRTUAL_NETWORK` tag in the rule.)</span></span> 
2. <span data-ttu-id="6e49a-166">允許來自 Business 層子網路的輸入流量。</span><span class="sxs-lookup"><span data-stu-id="6e49a-166">Allow inbound traffic from the business tier subnet.</span></span>  
3. <span data-ttu-id="6e49a-167">允許來自資料庫層子網路本身的輸入流量。</span><span class="sxs-lookup"><span data-stu-id="6e49a-167">Allow inbound traffic from the database tier subnet itself.</span></span> <span data-ttu-id="6e49a-168">此規則允許資料庫 VM 之間的通訊，是進行資料庫複寫和容錯移轉所需的。</span><span class="sxs-lookup"><span data-stu-id="6e49a-168">This rule allows communication between the database VMs, which is needed for database replication and failover.</span></span>
4. <span data-ttu-id="6e49a-169">允許來自 Jumpbox 子網路的 ssh 流量 (連接埠 22)。</span><span class="sxs-lookup"><span data-stu-id="6e49a-169">Allow ssh traffic (port 22) from the jumpbox subnet.</span></span> <span data-ttu-id="6e49a-170">此規則讓系統管理員能夠從 Jumpbox 連線到資料庫層。</span><span class="sxs-lookup"><span data-stu-id="6e49a-170">This rule lets administrators connect to the database tier from the jumpbox.</span></span>

<span data-ttu-id="6e49a-171">建立規則 2 &ndash; 4，其優先順序高於第一個規則，因此它們會覆寫它。</span><span class="sxs-lookup"><span data-stu-id="6e49a-171">Create rules 2 &ndash; 4 with higher priority than the first rule, so they override it.</span></span>

### <a name="cassandra"></a><span data-ttu-id="6e49a-172">Cassandra</span><span class="sxs-lookup"><span data-stu-id="6e49a-172">Cassandra</span></span>

<span data-ttu-id="6e49a-173">我們建議針對生產環境使用 [DataStax Enterprise][datastax]，但這些建議適用於所有的 Cassandra 版本。</span><span class="sxs-lookup"><span data-stu-id="6e49a-173">We recommend [DataStax Enterprise][datastax] for production use, but these recommendations apply to any Cassandra edition.</span></span> <span data-ttu-id="6e49a-174">如需在 Azure 中執行 DataStax 的詳細資訊，請參閱[適用於 Azure 的DataStax Enterprise 部署指南][cassandra-in-azure]。</span><span class="sxs-lookup"><span data-stu-id="6e49a-174">For more information on running DataStax in Azure, see [DataStax Enterprise Deployment Guide for Azure][cassandra-in-azure].</span></span> 

<span data-ttu-id="6e49a-175">將適用於 Cassandra 叢集的 VM 放置於可用性設定組，以確保會將 Cassandra 複本散發到多個容錯網域和升級網域。</span><span class="sxs-lookup"><span data-stu-id="6e49a-175">Put the VMs for a Cassandra cluster in an availability set to ensure that the Cassandra replicas are distributed across multiple fault domains and upgrade domains.</span></span> <span data-ttu-id="6e49a-176">如需容錯網域和升級網域的詳細資訊，請參閱[管理虛擬機器的可用性][azure-availability-sets]。</span><span class="sxs-lookup"><span data-stu-id="6e49a-176">For more information about fault domains and upgrade domains, see [Manage the availability of virtual machines][azure-availability-sets].</span></span> 

<span data-ttu-id="6e49a-177">針對每個可用性設定組設定三個錯誤網域 (最大值)，以及針對每個可用性設定組設定 18 個升級網域。</span><span class="sxs-lookup"><span data-stu-id="6e49a-177">Configure three fault domains (the maximum) per availability set and 18 upgrade domains per availability set.</span></span> <span data-ttu-id="6e49a-178">這提供仍能平均散發到容錯網域的升級網域數目上限。</span><span class="sxs-lookup"><span data-stu-id="6e49a-178">This provides the maximum number of upgrade domains that can still be distributed evenly across the fault domains.</span></span>   

<span data-ttu-id="6e49a-179">在機架感知的模式中設定節點。</span><span class="sxs-lookup"><span data-stu-id="6e49a-179">Configure nodes in rack-aware mode.</span></span> <span data-ttu-id="6e49a-180">在 `cassandra-rackdc.properties` 檔案中，將容錯網域對應到機架。</span><span class="sxs-lookup"><span data-stu-id="6e49a-180">Map fault domains to racks in the `cassandra-rackdc.properties` file.</span></span>

<span data-ttu-id="6e49a-181">您不需要叢集前方的負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="6e49a-181">You don't need a load balancer in front of the cluster.</span></span> <span data-ttu-id="6e49a-182">用戶端會直接連線至用戶端中的節點。</span><span class="sxs-lookup"><span data-stu-id="6e49a-182">The client connects directly to a node in the cluster.</span></span>

<span data-ttu-id="6e49a-183">如需高可用性，在多個 Azure 區域中部署 Cassandra。</span><span class="sxs-lookup"><span data-stu-id="6e49a-183">For high availability, deploy Cassandra in more than one Azure region.</span></span> <span data-ttu-id="6e49a-184">在每個區域中，會在機架感知的模式中使用錯誤和升級網域來設定節點，以便在區域內部提供復原能力。</span><span class="sxs-lookup"><span data-stu-id="6e49a-184">Within each region, nodes are configured in rack-aware mode with fault and upgrade domains, for resiliency inside the region.</span></span>


### <a name="jumpbox"></a><span data-ttu-id="6e49a-185">Jumpbox</span><span class="sxs-lookup"><span data-stu-id="6e49a-185">Jumpbox</span></span>

<span data-ttu-id="6e49a-186">不允許從公用網際網路對執行應用程式工作負載的 VM 進行 ssh 存取。</span><span class="sxs-lookup"><span data-stu-id="6e49a-186">Do not allow ssh access from the public Internet to the VMs that run the application workload.</span></span> <span data-ttu-id="6e49a-187">對這些 VM 所做的所有 ssh 存取都必須改為透過 Jumpbox 進行。</span><span class="sxs-lookup"><span data-stu-id="6e49a-187">Instead, all ssh access to these VMs must come through the jumpbox.</span></span> <span data-ttu-id="6e49a-188">系統管理員會登入 Jumpbox，然後從 Jumpbox 登入其他 VM。</span><span class="sxs-lookup"><span data-stu-id="6e49a-188">An administrator logs into the jumpbox, and then logs into the other VM from the jumpbox.</span></span> <span data-ttu-id="6e49a-189">Jumpbox 允許來自網際網路，但只來自已知且安全 IP 位址的 ssh 流量。</span><span class="sxs-lookup"><span data-stu-id="6e49a-189">The jumpbox allows ssh traffic from the Internet, but only from known, safe IP addresses.</span></span>

<span data-ttu-id="6e49a-190">Jumpbox 有最低效能需求，因此選取小的 VM 大小。</span><span class="sxs-lookup"><span data-stu-id="6e49a-190">The jumpbox has minimal performance requirements, so select a small VM size.</span></span> <span data-ttu-id="6e49a-191">針對 Jumpbox 建立[公用 IP 位址]。</span><span class="sxs-lookup"><span data-stu-id="6e49a-191">Create a [public IP address] for the jumpbox.</span></span> <span data-ttu-id="6e49a-192">將 Jumpbox 放置於與其他 VM 相同的 VNet，但在個別的管理子網路中。</span><span class="sxs-lookup"><span data-stu-id="6e49a-192">Place the jumpbox in the same VNet as the other VMs, but in a separate management subnet.</span></span>

<span data-ttu-id="6e49a-193">若要保護 Jumpbox，請新增 NSG 規則，只允許來自一組安全公用 IP 位址的 ssh 連線。</span><span class="sxs-lookup"><span data-stu-id="6e49a-193">To secure the jumpbox, add an NSG rule that allows ssh connections only from a safe set of public IP addresses.</span></span> <span data-ttu-id="6e49a-194">設定其他子網路的 NSG，允許來自管理子網路的 ssh 流量。</span><span class="sxs-lookup"><span data-stu-id="6e49a-194">Configure the NSGs for the other subnets to allow ssh traffic from the management subnet.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="6e49a-195">延展性考量</span><span class="sxs-lookup"><span data-stu-id="6e49a-195">Scalability considerations</span></span>

<span data-ttu-id="6e49a-196">[VM 擴展集][vmss]可協助您部署及管理一組完全相同的 VM。</span><span class="sxs-lookup"><span data-stu-id="6e49a-196">[VM scale sets][vmss] help you to deploy and manage a set of identical VMs.</span></span> <span data-ttu-id="6e49a-197">擴展集支援根據效能計量自動調整規模。</span><span class="sxs-lookup"><span data-stu-id="6e49a-197">Scale sets support autoscaling based on performance metrics.</span></span> <span data-ttu-id="6e49a-198">當 VM 上的負載增加時，就會將其他 VM 自動新增到負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="6e49a-198">As the load on the VMs increases, additional VMs are automatically added to the load balancer.</span></span> <span data-ttu-id="6e49a-199">如果您需要快速相應放大 VM 數目，或需要自動調整規模，請考慮擴展集。</span><span class="sxs-lookup"><span data-stu-id="6e49a-199">Consider scale sets if you need to quickly scale out VMs, or need to autoscale.</span></span>

<span data-ttu-id="6e49a-200">有兩種基本方法可用來設定擴展集中部署的 VM：</span><span class="sxs-lookup"><span data-stu-id="6e49a-200">There are two basic ways to configure VMs deployed in a scale set:</span></span>

- <span data-ttu-id="6e49a-201">佈建 VM 之後，使用擴充功能來設定它。</span><span class="sxs-lookup"><span data-stu-id="6e49a-201">Use extensions to configure the VM after it is provisioned.</span></span> <span data-ttu-id="6e49a-202">透過此方法，新的 VM 執行個體可能需要較長的時間來啟動不具擴充功能的 VM。</span><span class="sxs-lookup"><span data-stu-id="6e49a-202">With this approach, new VM instances may take longer to start up than a VM with no extensions.</span></span>

- <span data-ttu-id="6e49a-203">利用自訂的磁碟映像來部署[受控磁碟](/azure/storage/storage-managed-disks-overview)。</span><span class="sxs-lookup"><span data-stu-id="6e49a-203">Deploy a [managed disk](/azure/storage/storage-managed-disks-overview) with a custom disk image.</span></span> <span data-ttu-id="6e49a-204">這個選項可能會更快速地部署。</span><span class="sxs-lookup"><span data-stu-id="6e49a-204">This option may be quicker to deploy.</span></span> <span data-ttu-id="6e49a-205">不過，它會要求您讓映像保持最新狀態。</span><span class="sxs-lookup"><span data-stu-id="6e49a-205">However, it requires you to keep the image up to date.</span></span>

<span data-ttu-id="6e49a-206">如需其他考量，請參閱[擴展集的設計考量][vmss-design]。</span><span class="sxs-lookup"><span data-stu-id="6e49a-206">For additional considerations, see [Design considerations for scale sets][vmss-design].</span></span>

> [!TIP]
> <span data-ttu-id="6e49a-207">使用任何自動調整規模解決方案時，事前也要利用生產層級的工作負載來進行測試。</span><span class="sxs-lookup"><span data-stu-id="6e49a-207">When using any autoscale solution, test it with production-level workloads well in advance.</span></span>

<span data-ttu-id="6e49a-208">每個 Azure 訂用帳戶都已經有預設限制，包括每個區域的 VM 最大數目。</span><span class="sxs-lookup"><span data-stu-id="6e49a-208">Each Azure subscription has default limits in place, including a maximum number of VMs per region.</span></span> <span data-ttu-id="6e49a-209">您可以藉由提出支援要求來提高限制。</span><span class="sxs-lookup"><span data-stu-id="6e49a-209">You can increase the limit by filing a support request.</span></span> <span data-ttu-id="6e49a-210">如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束][subscription-limits]。</span><span class="sxs-lookup"><span data-stu-id="6e49a-210">For more information, see [Azure subscription and service limits, quotas, and constraints][subscription-limits].</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="6e49a-211">可用性考量</span><span class="sxs-lookup"><span data-stu-id="6e49a-211">Availability considerations</span></span>

<span data-ttu-id="6e49a-212">如果您不是使用 VM 擴展集，請將同一層中的 VM 放入可用性設定組。</span><span class="sxs-lookup"><span data-stu-id="6e49a-212">If you are not using VM scale sets, put VMs in the same tier into an availability set.</span></span> <span data-ttu-id="6e49a-213">在可用性設定組中至少建立兩部 VM，以支援[適用於 Azure VM 的可用性 SLA][vm-sla]。</span><span class="sxs-lookup"><span data-stu-id="6e49a-213">Create at least two VMs in the availability set to support the [availability SLA for Azure VMs][vm-sla].</span></span> <span data-ttu-id="6e49a-214">如需詳細資訊，請參閱[管理虛擬機器的可用性][availability-set]。</span><span class="sxs-lookup"><span data-stu-id="6e49a-214">For more information, see [Manage the availability of virtual machines][availability-set].</span></span> 

<span data-ttu-id="6e49a-215">負載平衡器會使用[健康情況探查][health-probes]來監視 VM 執行個體的可用性。</span><span class="sxs-lookup"><span data-stu-id="6e49a-215">The load balancer uses [health probes][health-probes] to monitor the availability of VM instances.</span></span> <span data-ttu-id="6e49a-216">如果探查無法在逾時期間內連線到執行個體，負載平衡器就會停止將流量傳送到該 VM。</span><span class="sxs-lookup"><span data-stu-id="6e49a-216">If a probe cannot reach an instance within a timeout period, the load balancer stops sending traffic to that VM.</span></span> <span data-ttu-id="6e49a-217">不過，負載平衡器將會繼續探查，而且如果 VM 再次變成可用，負載平衡器就會繼續將流量傳送到該 VM。</span><span class="sxs-lookup"><span data-stu-id="6e49a-217">However, the load balancer will continue to probe, and if the VM becomes available again, the load balancer resumes sending traffic to that VM.</span></span>

<span data-ttu-id="6e49a-218">以下是關於負載平衡器健康情況探查的建議：</span><span class="sxs-lookup"><span data-stu-id="6e49a-218">Here are some recommendations on load balancer health probes:</span></span>

* <span data-ttu-id="6e49a-219">探查可以測試 HTTP 或 TCP。</span><span class="sxs-lookup"><span data-stu-id="6e49a-219">Probes can test either HTTP or TCP.</span></span> <span data-ttu-id="6e49a-220">如果您的 VM 執行 HTTP 伺服器，請建立 HTTP 探查。</span><span class="sxs-lookup"><span data-stu-id="6e49a-220">If your VMs run an HTTP server, create an HTTP probe.</span></span> <span data-ttu-id="6e49a-221">否則，建立 TCP 探查。</span><span class="sxs-lookup"><span data-stu-id="6e49a-221">Otherwise create a TCP probe.</span></span>
* <span data-ttu-id="6e49a-222">針對 HTTP 探查，指定 HTTP 端點的路徑。</span><span class="sxs-lookup"><span data-stu-id="6e49a-222">For an HTTP probe, specify the path to an HTTP endpoint.</span></span> <span data-ttu-id="6e49a-223">探查會檢查來自此路徑的 HTTP 200 回應。</span><span class="sxs-lookup"><span data-stu-id="6e49a-223">The probe checks for an HTTP 200 response from this path.</span></span> <span data-ttu-id="6e49a-224">這可以是根路徑 ("/")，或是實作一些自訂邏輯來檢查應用程式健康情況的健康情況監視端點。</span><span class="sxs-lookup"><span data-stu-id="6e49a-224">This can be the root path ("/"), or a health-monitoring endpoint that implements some custom logic to check the health of the application.</span></span> <span data-ttu-id="6e49a-225">端點必須允許匿名的 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="6e49a-225">The endpoint must allow anonymous HTTP requests.</span></span>
* <span data-ttu-id="6e49a-226">探查會從[已知的 IP 位址][health-probe-ip] 168.63.129.16 傳送出來。</span><span class="sxs-lookup"><span data-stu-id="6e49a-226">The probe is sent from a [known IP address][health-probe-ip], 168.63.129.16.</span></span> <span data-ttu-id="6e49a-227">請確定您並未在任何防火牆原則或網路安全性群組 (NSG) 規則中封鎖往返此 IP 位址的流量。</span><span class="sxs-lookup"><span data-stu-id="6e49a-227">Make sure you don't block traffic to or from this IP address in any firewall policies or network security group (NSG) rules.</span></span>
* <span data-ttu-id="6e49a-228">使用[健康情況探查記錄][health-probe-log]來檢視健康情況探查的狀態。</span><span class="sxs-lookup"><span data-stu-id="6e49a-228">Use [health probe logs][health-probe-log] to view the status of the health probes.</span></span> <span data-ttu-id="6e49a-229">能夠針對每個負載平衡器登入 Azure 入口網站。</span><span class="sxs-lookup"><span data-stu-id="6e49a-229">Enable logging in the Azure portal for each load balancer.</span></span> <span data-ttu-id="6e49a-230">記錄會寫入 Azure Blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="6e49a-230">Logs are written to Azure Blob storage.</span></span> <span data-ttu-id="6e49a-231">記錄會顯示後端有多少個 VM 因探查回應失敗而不會接收網路流量。</span><span class="sxs-lookup"><span data-stu-id="6e49a-231">The logs show how many VMs on the back end are not receiving network traffic due to failed probe responses.</span></span>

<span data-ttu-id="6e49a-232">針對 Cassandra 叢集，要考慮的容錯移轉案例取決於應用程式所使用的一致性層級，以及使用的複本數目。</span><span class="sxs-lookup"><span data-stu-id="6e49a-232">For the Cassandra cluster, the failover scenarios to consider depend on the consistency levels used by the application, as well as the number of replicas used.</span></span> <span data-ttu-id="6e49a-233">如需 Cassandra 中的一致性層級和使用方式，請參閱[設定資料一致性][cassandra-consistency]和 [Cassandra：有多少節點會與仲裁交談？][cassandra-consistency-usage]</span><span class="sxs-lookup"><span data-stu-id="6e49a-233">For consistency levels and usage in Cassandra, see [Configuring data consistency][cassandra-consistency] and [Cassandra: How many nodes are talked to with Quorum?][cassandra-consistency-usage]</span></span> <span data-ttu-id="6e49a-234">Cassandra 中的資料可用性取決於應用程式所使用的一致性層級和複寫機制。</span><span class="sxs-lookup"><span data-stu-id="6e49a-234">Data availability in Cassandra is determined by the consistency level used by the application and the replication mechanism.</span></span> <span data-ttu-id="6e49a-235">如需 Cassandra 中的複寫，請參閱 [NoSQL 資料庫的資料複寫說明][cassandra-replication]。</span><span class="sxs-lookup"><span data-stu-id="6e49a-235">For replication in Cassandra, see [Data Replication in NoSQL Databases Explained][cassandra-replication].</span></span>

## <a name="security-considerations"></a><span data-ttu-id="6e49a-236">安全性考量</span><span class="sxs-lookup"><span data-stu-id="6e49a-236">Security considerations</span></span>

<span data-ttu-id="6e49a-237">虛擬網路是 Azure 中的流量隔離界限。</span><span class="sxs-lookup"><span data-stu-id="6e49a-237">Virtual networks are a traffic isolation boundary in Azure.</span></span> <span data-ttu-id="6e49a-238">某一個 VNet 中的 VM 無法直接與不同 VNet 中的 VM 通訊。</span><span class="sxs-lookup"><span data-stu-id="6e49a-238">VMs in one VNet cannot communicate directly with VMs in a different VNet.</span></span> <span data-ttu-id="6e49a-239">除非您建立[網路安全性群組][nsg] (NSG) 來限制流量，否則，相同 VNet 中的 VM 可以彼此通訊。</span><span class="sxs-lookup"><span data-stu-id="6e49a-239">VMs within the same VNet can communicate, unless you create [network security groups][nsg] (NSGs) to restrict traffic.</span></span> <span data-ttu-id="6e49a-240">如需詳細資訊，請參閱 [Microsoft 雲端服務和網路安全性][network-security]。</span><span class="sxs-lookup"><span data-stu-id="6e49a-240">For more information, see [Microsoft cloud services and network security][network-security].</span></span>

<span data-ttu-id="6e49a-241">針對傳入的網際網路流量，負載平衡器規則會定義哪些流量可以連線到後端。</span><span class="sxs-lookup"><span data-stu-id="6e49a-241">For incoming Internet traffic, the load balancer rules define which traffic can reach the back end.</span></span> <span data-ttu-id="6e49a-242">不過，負載平衡器規則不支援 IP 安全清單，因此，如果您想要將特定的公用 IP 位址新增至安全清單，請將 NSG 新增至子網路。</span><span class="sxs-lookup"><span data-stu-id="6e49a-242">However, load balancer rules don't support IP safe lists, so if you want to add certain public IP addresses to a safe list, add an NSG to the subnet.</span></span>

<span data-ttu-id="6e49a-243">請考慮新增網路虛擬設備 (NVA)，在網際網路和 Azure 虛擬網路之間建立 DMZ。</span><span class="sxs-lookup"><span data-stu-id="6e49a-243">Consider adding a network virtual appliance (NVA) to create a DMZ between the Internet and the Azure virtual network.</span></span> <span data-ttu-id="6e49a-244">NVA 是虛擬設備的通稱，可以執行網路相關的工作，例如防火牆、封包檢查、稽核和自訂路由傳送。</span><span class="sxs-lookup"><span data-stu-id="6e49a-244">NVA is a generic term for a virtual appliance that can perform network-related tasks, such as firewall, packet inspection, auditing, and custom routing.</span></span> <span data-ttu-id="6e49a-245">如需詳細資訊，請參閱[實作 Azure 和網際網路之間的 DMZ][dmz]。</span><span class="sxs-lookup"><span data-stu-id="6e49a-245">For more information, see [Implementing a DMZ between Azure and the Internet][dmz].</span></span>

<span data-ttu-id="6e49a-246">將機密的待用資料加密，並使用 [Azure Key Vault][azure-key-vault] 來管理資料庫加密金鑰。</span><span class="sxs-lookup"><span data-stu-id="6e49a-246">Encrypt sensitive data at rest and use [Azure Key Vault][azure-key-vault] to manage the database encryption keys.</span></span> <span data-ttu-id="6e49a-247">Key Vault 可以在硬體安全模組 (HSM) 中儲存加密金鑰。</span><span class="sxs-lookup"><span data-stu-id="6e49a-247">Key Vault can store encryption keys in hardware security modules (HSMs).</span></span> <span data-ttu-id="6e49a-248">也建議將應用程式密碼 (例如資料庫連接字串) 儲存在金鑰保存庫中。</span><span class="sxs-lookup"><span data-stu-id="6e49a-248">It's also recommended to store application secrets, such as database connection strings, in Key Vault.</span></span>

<span data-ttu-id="6e49a-249">我們建議啟用 [Azure DDoS 保護標準](/azure/virtual-network/ddos-protection-overview)，該標準為 VNet 中的資源提供額外的 DDoS 安全防護功能。</span><span class="sxs-lookup"><span data-stu-id="6e49a-249">We recommend enabling [DDoS Protection Standard](/azure/virtual-network/ddos-protection-overview), which provides additional DDoS mitigation for resources in a VNet.</span></span> <span data-ttu-id="6e49a-250">雖然基本 DDoS 保護會隨著 Azure 平台而自動啟用，但 Azure DDoS 保護標準提供了專門針對 Azure 虛擬網路資源進行調整的安全防護功能。</span><span class="sxs-lookup"><span data-stu-id="6e49a-250">Although basic DDoS protection is automatically enabled as part of the Azure platform, DDoS Protection Standard provides mitigation capabilities that are tuned specifically to Azure Virtual Network resources.</span></span>  

## <a name="deploy-the-solution"></a><span data-ttu-id="6e49a-251">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="6e49a-251">Deploy the solution</span></span>

<span data-ttu-id="6e49a-252">此參考架構的部署可在 [GitHub][github-folder] 上取得。</span><span class="sxs-lookup"><span data-stu-id="6e49a-252">A deployment for this reference architecture is available on [GitHub][github-folder].</span></span> 

### <a name="prerequisites"></a><span data-ttu-id="6e49a-253">必要條件</span><span class="sxs-lookup"><span data-stu-id="6e49a-253">Prerequisites</span></span>

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-the-solution-using-azbb"></a><span data-ttu-id="6e49a-254">使用 azbb 部署解決方案</span><span class="sxs-lookup"><span data-stu-id="6e49a-254">Deploy the solution using azbb</span></span>

<span data-ttu-id="6e49a-255">若要以多層式架構 (N-Tier) 應用程式參考架構部署 Linux VM，請依以下步驟進行：</span><span class="sxs-lookup"><span data-stu-id="6e49a-255">To deploy the Linux VMs for an N-tier application reference architecture, follow these steps:</span></span>

1. <span data-ttu-id="6e49a-256">瀏覽至您在上述必要條件步驟 1 中複製存放庫的 `virtual-machines\n-tier-linux` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="6e49a-256">Navigate to the `virtual-machines\n-tier-linux` folder for the repository you cloned in step 1 of the pre-requisites above.</span></span>

2. <span data-ttu-id="6e49a-257">參數檔案會指定部署中每個 VM 的預設管理員使用者名稱及密碼。</span><span class="sxs-lookup"><span data-stu-id="6e49a-257">The parameter file specifies a default adminstrator user name and password for each VM in the deployment.</span></span> <span data-ttu-id="6e49a-258">您必須在部署參考架構前先變更這些內容。</span><span class="sxs-lookup"><span data-stu-id="6e49a-258">You must change these before you deploy the reference architecture.</span></span> <span data-ttu-id="6e49a-259">開啟 `n-tier-linux.json` 檔案，並用新設定取代每個 **adminUsername** 和 **adminPassword** 欄位。</span><span class="sxs-lookup"><span data-stu-id="6e49a-259">Open the `n-tier-linux.json` file and replace each **adminUsername** and **adminPassword** field with your new settings.</span></span>   <span data-ttu-id="6e49a-260">儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="6e49a-260">Save the file.</span></span>

3. <span data-ttu-id="6e49a-261">使用如下所示的 **azbb** 命令列來部署參考架構。</span><span class="sxs-lookup"><span data-stu-id="6e49a-261">Deploy the reference architecture using the **azbb** command line tool as shown below.</span></span>

   ```bash
   azbb -s <your subscription_id> -g <your resource_group_name> -l <azure region> -p n-tier-linux.json --deploy
   ```

<span data-ttu-id="6e49a-262">如需使用 Azure 組建區塊部署此範例參考架構的詳細資訊，請瀏覽 [GitHub 存放庫][git]。</span><span class="sxs-lookup"><span data-stu-id="6e49a-262">For more information on deploying this sample reference architecture using Azure Building Blocks, visit the [GitHub repository][git].</span></span>

<!-- links -->
[dmz]: ../dmz/secure-vnet-dmz.md
[multi-vm]: ./multi-vm.md
[naming conventions]: /azure/guidance/guidance-naming-conventions
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[azure-administration]: /azure/automation/automation-intro
[azure-availability-sets]: /azure/virtual-machines/virtual-machines-linux-manage-availability
[azure-cli-2]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[azure-dns]: /azure/dns/dns-overview
[azure-key-vault]: https://azure.microsoft.com/services/key-vault

[防禦主機]: https://en.wikipedia.org/wiki/Bastion_host
[bastion host]: https://en.wikipedia.org/wiki/Bastion_host
[cassandra-in-azure]: https://academy.datastax.com/resources/deployment-guide-azure
[cassandra-consistency]: http://docs.datastax.com/en/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
[cassandra-replication]: http://www.planetcassandra.org/data-replication-in-nosql-databases-explained/
[cassandra-consistency-usage]: https://medium.com/@foundev/cassandra-how-many-nodes-are-talked-to-with-quorum-also-should-i-use-it-98074e75d7d5#.b4pb4alb2

[cidr]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[datastax]: http://www.datastax.com/products/datastax-enterprise
[git]: https://github.com/mspnp/template-building-blocks
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/n-tier-linux
[lb-external-create]: /azure/load-balancer/load-balancer-get-started-internet-portal
[lb-internal-create]: /azure/load-balancer/load-balancer-get-started-ilb-arm-portal
[load-balancer-external]: /azure/load-balancer/load-balancer-internet-overview
[load-balancer-internal]: /azure/load-balancer/load-balancer-internal-overview
[nsg]: /azure/virtual-network/virtual-networks-nsg
[nsg-rules]: /azure/azure-resource-manager/best-practices-resource-manager-security#network-security-groups
[operations-management-suite]: https://www.microsoft.com/server-cloud/operations-management-suite/overview.aspx
[plan-network]: /azure/virtual-network/virtual-network-vnet-plan-design-arm
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[公用 IP 位址]: /azure/virtual-network/virtual-network-ip-addresses-overview-arm
[public IP address]: /azure/virtual-network/virtual-network-ip-addresses-overview-arm
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[vnet faq]: /azure/virtual-network/virtual-networks-faq
[visio-download]: https://archcenter.blob.core.windows.net/cdn/vm-reference-architectures.vsdx
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[0]: ./images/n-tier-cassandra.png "使用 Microsoft Azure 的多層式架構"

[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview 
[vmss]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview
[load-balancer]: /azure/load-balancer/load-balancer-get-started-internet-arm-cli
[load-balancer-hashing]: /azure/load-balancer/load-balancer-overview#load-balancer-features
[vmss-design]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview
[subscription-limits]: /azure/azure-subscription-service-limits
[availability-set]: /azure/virtual-machines/virtual-machines-windows-manage-availability
[health-probes]: /azure/load-balancer/load-balancer-overview#load-balancer-features
[health-probe-log]: /azure/load-balancer/load-balancer-monitor-log
[health-probe-ip]: /azure/virtual-network/virtual-networks-nsg#special-rules
[network-security]: /azure/best-practices-network-security
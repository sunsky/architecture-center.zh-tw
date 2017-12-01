---
title: "執行適用於多層式架構的 Windows VM"
description: "如何在 Azure 上實作多層式架構，請特別注意可用性、安全性、延展性及管理功能安全性。"
author: MikeWasson
ms.date: 11/22/2016
pnp.series.title: Windows VM workloads
pnp.series.next: multi-region-application
pnp.series.prev: multi-vm
ms.openlocfilehash: e25d10d661ac4759f209bd27384303dee2ee454e
ms.sourcegitcommit: 583e54a1047daa708a9b812caafb646af4d7607b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="run-windows-vms-for-an-n-tier-application"></a><span data-ttu-id="90932-103">執行適用於多層式架構應用程式的 Windows VM</span><span class="sxs-lookup"><span data-stu-id="90932-103">Run Windows VMs for an N-tier application</span></span>

<span data-ttu-id="90932-104">此參考架構顯示一組經過證實的作法，可用來執行適用於多層式架構應用程式的 Windows 虛擬機器 (VM)。</span><span class="sxs-lookup"><span data-stu-id="90932-104">This reference architecture shows a set of proven practices for running Windows virtual machines (VMs) for an N-tier application.</span></span> [<span data-ttu-id="90932-105">**部署這個解決方案**。</span><span class="sxs-lookup"><span data-stu-id="90932-105">**Deploy this solution**.</span></span>](#deploy-the-solution) 

<span data-ttu-id="90932-106">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="90932-106">![[0]][0]</span></span>

<span data-ttu-id="90932-107">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="90932-107">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="90932-108">架構</span><span class="sxs-lookup"><span data-stu-id="90932-108">Architecture</span></span> 

<span data-ttu-id="90932-109">有許多方式可實作多層式架構。</span><span class="sxs-lookup"><span data-stu-id="90932-109">There are many ways to implement an N-tier architecture.</span></span> <span data-ttu-id="90932-110">下圖顯示典型的 3 層式架構 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="90932-110">The diagram shows a typical 3-tier web application.</span></span> <span data-ttu-id="90932-111">此架構是根據[執行負載平衡的 VM 以獲得延展性和可用性][multi-vm]建置的。</span><span class="sxs-lookup"><span data-stu-id="90932-111">This architecture builds on [Run load-balanced VMs for scalability and availability][multi-vm].</span></span> <span data-ttu-id="90932-112">Web 及 Business 層會使用負載平衡的 VM。</span><span class="sxs-lookup"><span data-stu-id="90932-112">The web and business tiers use load-balanced VMs.</span></span>

* <span data-ttu-id="90932-113">**可用性設定組。**</span><span class="sxs-lookup"><span data-stu-id="90932-113">**Availability sets.**</span></span> <span data-ttu-id="90932-114">針對每一層建立[可用性設定組][azure-availability-sets]，並且在每一層中至少佈建兩個 VM。</span><span class="sxs-lookup"><span data-stu-id="90932-114">Create an [availability set][azure-availability-sets] for each tier, and provision at least two VMs in each tier.</span></span> <span data-ttu-id="90932-115">這讓 VM 能夠符合適用於 VM 之較高[服務等級協定 (SLA)][vm-sla] 的資格。</span><span class="sxs-lookup"><span data-stu-id="90932-115">This makes the VMs eligible for a higher [service level agreement (SLA)][vm-sla] for VMs.</span></span> <span data-ttu-id="90932-116">您可以在可用性設定組中部署單一 VM，但該單一 VM 無法當做 SLA 保證使用，除非該單一 VM 的所有作業系統及資料磁碟是使用 Azure 進階儲存體。</span><span class="sxs-lookup"><span data-stu-id="90932-116">You can deploy a single VM in an availability set, but the single VM will not qualify for an SLA guarantee unless the single VM is using Azure Premium Storage for all OS and data disks.</span></span>  
* <span data-ttu-id="90932-117">**子網路。**</span><span class="sxs-lookup"><span data-stu-id="90932-117">**Subnets.**</span></span> <span data-ttu-id="90932-118">針對每一層建立不同的子網路。</span><span class="sxs-lookup"><span data-stu-id="90932-118">Create a separate subnet for each tier.</span></span> <span data-ttu-id="90932-119">使用 [CIDR] 標記法來指定位址範圍和子網路遮罩。</span><span class="sxs-lookup"><span data-stu-id="90932-119">Specify the address range and subnet mask using [CIDR] notation.</span></span> 
* <span data-ttu-id="90932-120">**負載平衡器。**</span><span class="sxs-lookup"><span data-stu-id="90932-120">**Load balancers.**</span></span> <span data-ttu-id="90932-121">使用[網際網路面向的負載平衡器][load-balancer-external]，將連入的網際網路流量散發到 Web 層，並使用[內部負載平衡器][load-balancer-internal]，將來自 Web 層的網路流量散發到 Business 層。</span><span class="sxs-lookup"><span data-stu-id="90932-121">Use an [Internet-facing load balancer][load-balancer-external] to distribute incoming Internet traffic to the web tier, and an [internal load balancer][load-balancer-internal] to distribute network traffic from the web tier to the business tier.</span></span>
* <span data-ttu-id="90932-122">**Jumpbox。**</span><span class="sxs-lookup"><span data-stu-id="90932-122">**Jumpbox.**</span></span> <span data-ttu-id="90932-123">也稱為[防禦主機]。</span><span class="sxs-lookup"><span data-stu-id="90932-123">Also called a [bastion host].</span></span> <span data-ttu-id="90932-124">網路上系統管理員用來連線到其他 VM 的安全 VM。</span><span class="sxs-lookup"><span data-stu-id="90932-124">A secure VM on the network that administrators use to connect to the other VMs.</span></span> <span data-ttu-id="90932-125">Jumpbox 具有 NSG，只允許來自安全清單上公用 IP 位址的遠端流量。</span><span class="sxs-lookup"><span data-stu-id="90932-125">The jumpbox has an NSG that allows remote traffic only from public IP addresses on a safe list.</span></span> <span data-ttu-id="90932-126">NSG 應該允許遠端桌面 (RDP) 流量。</span><span class="sxs-lookup"><span data-stu-id="90932-126">The NSG should permit remote desktop (RDP) traffic.</span></span>
* <span data-ttu-id="90932-127">**監視。**</span><span class="sxs-lookup"><span data-stu-id="90932-127">**Monitoring.**</span></span> <span data-ttu-id="90932-128">監視軟體 (例如 [Nagios]、[Zabbix] 或 [Icinga]) 可讓您深入了解回應時間、VM 執行時間，以及系統的整體健康情況。</span><span class="sxs-lookup"><span data-stu-id="90932-128">Monitoring software such as [Nagios], [Zabbix], or [Icinga] can give you insight into response time, VM uptime, and the overall health of your system.</span></span> <span data-ttu-id="90932-129">在位於個別管理子網路中的 VM 上安裝監視軟體。</span><span class="sxs-lookup"><span data-stu-id="90932-129">Install the monitoring software on a VM that's placed in a separate management subnet.</span></span>
* <span data-ttu-id="90932-130">**NSG。**</span><span class="sxs-lookup"><span data-stu-id="90932-130">**NSGs.**</span></span> <span data-ttu-id="90932-131">使用[網路安全性群組][nsg] (NSG) 來限制 VNet 內的網路流量。</span><span class="sxs-lookup"><span data-stu-id="90932-131">Use [network security groups][nsg] (NSGs) to restrict network traffic within the VNet.</span></span> <span data-ttu-id="90932-132">例如，在如下所示的 3 層式架構中，資料庫層不接受來自 Web 前端的流量，只接受來自 Business 層和管理子網路的流量。</span><span class="sxs-lookup"><span data-stu-id="90932-132">For example, in the 3-tier architecture shown here, the database tier does not accept traffic from the web front end, only from the business tier and the management subnet.</span></span>
* <span data-ttu-id="90932-133">**SQL Server Always On 可用性群組。**</span><span class="sxs-lookup"><span data-stu-id="90932-133">**SQL Server Always On Availability Group.**</span></span> <span data-ttu-id="90932-134">藉由啟用複寫和容錯移轉，在資料層提供高可用性。</span><span class="sxs-lookup"><span data-stu-id="90932-134">Provides high availability at the data tier, by enabling replication and failover.</span></span>
* <span data-ttu-id="90932-135">**Active Directory Domain Services (AD DS) 伺服器**。</span><span class="sxs-lookup"><span data-stu-id="90932-135">**Active Directory Domain Services (AD DS) Servers**.</span></span> <span data-ttu-id="90932-136">在 Windows Server 2016 之前，必須將 SQL Server Always On 可用性群組加入網域。</span><span class="sxs-lookup"><span data-stu-id="90932-136">Prior to Windows Server 2016, SQL Server Always On Availability Groups must be joined to a domain.</span></span> <span data-ttu-id="90932-137">這是因為可用性群組相依於 Windows Server 容錯移轉叢集 (WSFC) 技術。</span><span class="sxs-lookup"><span data-stu-id="90932-137">This is because Availability Groups depend on Windows Server Failover Cluster (WSFC) technology.</span></span> <span data-ttu-id="90932-138">Windows Server 2016 引進了不需 Active Directory 就可建立容錯移轉叢集的能力，因此，對這個架構而言，AD DS 伺服器並非必要。</span><span class="sxs-lookup"><span data-stu-id="90932-138">Windows Server 2016 introduces the ability to create a Failover Cluster without Active Directory, in which case the AD DS servers are not required for this architecture.</span></span> <span data-ttu-id="90932-139">如需詳細資訊，請參閱 [Windows Server 2016 中容錯移轉叢集的新功能][wsfc-whats-new]。</span><span class="sxs-lookup"><span data-stu-id="90932-139">For more information, see [What's new in Failover Clustering in Windows Server 2016][wsfc-whats-new].</span></span>

## <a name="recommendations"></a><span data-ttu-id="90932-140">建議</span><span class="sxs-lookup"><span data-stu-id="90932-140">Recommendations</span></span>

<span data-ttu-id="90932-141">您的需求可能和此處所述的架構不同。</span><span class="sxs-lookup"><span data-stu-id="90932-141">Your requirements might differ from the architecture described here.</span></span> <span data-ttu-id="90932-142">請使用以下建議作為起點。</span><span class="sxs-lookup"><span data-stu-id="90932-142">Use these recommendations as a starting point.</span></span> 

### <a name="vnet--subnets"></a><span data-ttu-id="90932-143">VNet / 子網路</span><span class="sxs-lookup"><span data-stu-id="90932-143">VNet / Subnets</span></span>

<span data-ttu-id="90932-144">當您建立 VNet 時，請判斷您在每個子網路中的資源需要多少個 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="90932-144">When you create the VNet, determine how many IP addresses your resources in each subnet require.</span></span> <span data-ttu-id="90932-145">使用 [CIDR] 標記法，針對所需的 IP 位址指定子網路遮罩和夠大的 VNet 位址範圍。</span><span class="sxs-lookup"><span data-stu-id="90932-145">Specify a subnet mask and a VNet address range large enough for the required IP addresses, using [CIDR] notation.</span></span> <span data-ttu-id="90932-146">使用落在標準[私人 IP 位址區塊][private-ip-space]內的位址空間，其為 10.0.0.0/8、172.16.0.0/12 及 192.168.0.0/16。</span><span class="sxs-lookup"><span data-stu-id="90932-146">Use an address space that falls within the standard [private IP address blocks][private-ip-space], which are 10.0.0.0/8, 172.16.0.0/12, and 192.168.0.0/16.</span></span>

<span data-ttu-id="90932-147">選擇不會與您的內部部署網路重疊的位址範圍，以防您稍後需要在 VNet 和您的內部部署網路之間設定閘道。</span><span class="sxs-lookup"><span data-stu-id="90932-147">Choose an address range that does not overlap with your on-premises network, in case you need to set up a gateway between the VNet and your on-premise network later.</span></span> <span data-ttu-id="90932-148">一旦建立 VNet 之後，就無法變更位址範圍。</span><span class="sxs-lookup"><span data-stu-id="90932-148">Once you create the VNet, you can't change the address range.</span></span>

<span data-ttu-id="90932-149">請記得以功能和安全性需求來設計子網路。</span><span class="sxs-lookup"><span data-stu-id="90932-149">Design subnets with functionality and security requirements in mind.</span></span> <span data-ttu-id="90932-150">位於相同層或角色的所有 VM 都應移入相同的子網路，它可以是安全性界限。</span><span class="sxs-lookup"><span data-stu-id="90932-150">All VMs within the same tier or role should go into the same subnet, which can be a security boundary.</span></span> <span data-ttu-id="90932-151">如需設計 VNet 和子網路的詳細資訊，請參閱[規劃和設計 Azure 虛擬網路][plan-network]。</span><span class="sxs-lookup"><span data-stu-id="90932-151">For more information about designing VNets and subnets, see [Plan and design Azure Virtual Networks][plan-network].</span></span>

<span data-ttu-id="90932-152">針對每個子網路，以 CIDR 標記法來指定子網路的位址空間。</span><span class="sxs-lookup"><span data-stu-id="90932-152">For each subnet, specify the address space for the subnet in CIDR notation.</span></span> <span data-ttu-id="90932-153">例如，'10.0.0.0/24' 會建立 256 個 IP 位址範圍。</span><span class="sxs-lookup"><span data-stu-id="90932-153">For example, '10.0.0.0/24' creates a range of 256 IP addresses.</span></span> <span data-ttu-id="90932-154">VM 可以使用這其中的 251 個；保留五個。</span><span class="sxs-lookup"><span data-stu-id="90932-154">VMs can use 251 of these; five are reserved.</span></span> <span data-ttu-id="90932-155">確定位址範圍不會跨子網路重疊。</span><span class="sxs-lookup"><span data-stu-id="90932-155">Make sure the address ranges don't overlap across subnets.</span></span> <span data-ttu-id="90932-156">請參閱[虛擬網路常見問題集][vnet faq]。</span><span class="sxs-lookup"><span data-stu-id="90932-156">See the [Virtual Network FAQ][vnet faq].</span></span>

### <a name="network-security-groups"></a><span data-ttu-id="90932-157">網路安全性群組</span><span class="sxs-lookup"><span data-stu-id="90932-157">Network security groups</span></span>

<span data-ttu-id="90932-158">使用 NSG 規則來限制各層之間的流量。</span><span class="sxs-lookup"><span data-stu-id="90932-158">Use NSG rules to restrict traffic between tiers.</span></span> <span data-ttu-id="90932-159">例如，在如上所示的 3 層式架構中，Web 層不會直接與資料庫層通訊。</span><span class="sxs-lookup"><span data-stu-id="90932-159">For example, in the 3-tier architecture shown above, the web tier does not communicate directly with the database tier.</span></span> <span data-ttu-id="90932-160">若要強制執行此動作，資料庫層應該封鎖來自 Web 層子網路的連入流量。</span><span class="sxs-lookup"><span data-stu-id="90932-160">To enforce this, the database tier should block incoming traffic from the web tier subnet.</span></span>  

1. <span data-ttu-id="90932-161">建立 NSG，並將它關聯至資料庫層子網路。</span><span class="sxs-lookup"><span data-stu-id="90932-161">Create an NSG and associate it to the database tier subnet.</span></span>
2. <span data-ttu-id="90932-162">新增規則以拒絕來自 VNet 的所有輸入流量</span><span class="sxs-lookup"><span data-stu-id="90932-162">Add a rule that denies all inbound traffic from the VNet.</span></span> <span data-ttu-id="90932-163">(在規則中使用 `VIRTUAL_NETWORK` 標記)。</span><span class="sxs-lookup"><span data-stu-id="90932-163">(Use the `VIRTUAL_NETWORK` tag in the rule.)</span></span> 
3. <span data-ttu-id="90932-164">新增具有較高優先順序的規則，允許來自 Business 層子網路的輸入流量。</span><span class="sxs-lookup"><span data-stu-id="90932-164">Add a rule with a higher priority that allows inbound traffic from the business tier subnet.</span></span> <span data-ttu-id="90932-165">此規則會覆寫上一個規則，並讓 Business 層可與資料庫層對話。</span><span class="sxs-lookup"><span data-stu-id="90932-165">This rule overrides the previous rule, and allows the business tier to talk to the database tier.</span></span>
4. <span data-ttu-id="90932-166">新增規則，允許來自資料庫層子網路本身的輸入流量。</span><span class="sxs-lookup"><span data-stu-id="90932-166">Add a rule that allows inbound traffic from within the database tier subnet itself.</span></span> <span data-ttu-id="90932-167">此規則允許資料庫層中 VM 之間的通訊，是進行資料庫複寫和容錯移轉所需的。</span><span class="sxs-lookup"><span data-stu-id="90932-167">This rule allows communication between VMs in the database tier, which is needed for database replication and failover.</span></span>
5. <span data-ttu-id="90932-168">新增規則，允許來自 Jumpbox 子網路的 RDP 流量。</span><span class="sxs-lookup"><span data-stu-id="90932-168">Add a rule that allows RDP traffic from the jumpbox subnet.</span></span> <span data-ttu-id="90932-169">此規則讓系統管理員能夠從 Jumpbox 連線到資料庫層。</span><span class="sxs-lookup"><span data-stu-id="90932-169">This rule lets administrators connect to the database tier from the jumpbox.</span></span>
   
   > [!NOTE]
   > <span data-ttu-id="90932-170">NSG 的預設規則允許所有來自 VNet 的輸入流量。</span><span class="sxs-lookup"><span data-stu-id="90932-170">An NSG has default rules that allow any inbound traffic from within the VNet.</span></span> <span data-ttu-id="90932-171">這些規則無法刪除，但您可藉由建立較高優先順序的規則來覆寫它們。</span><span class="sxs-lookup"><span data-stu-id="90932-171">These rules can't be deleted, but you can override them by creating higher priority rules.</span></span>
   > 
   > 

### <a name="load-balancers"></a><span data-ttu-id="90932-172">負載平衡器</span><span class="sxs-lookup"><span data-stu-id="90932-172">Load balancers</span></span>

<span data-ttu-id="90932-173">外部負載平衡器會將網際網路流量散發到 Web 層。</span><span class="sxs-lookup"><span data-stu-id="90932-173">The external load balancer distributes Internet traffic to the web tier.</span></span> <span data-ttu-id="90932-174">建立此負載平衡器的公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="90932-174">Create a public IP address for this load balancer.</span></span> <span data-ttu-id="90932-175">請參閱[建立網際網路面向的負載平衡器][lb-external-create]。</span><span class="sxs-lookup"><span data-stu-id="90932-175">See [Creating an Internet-facing load balancer][lb-external-create].</span></span>

<span data-ttu-id="90932-176">內部負載平衡器會將網路流量從 Web 層散發到 Business 層。</span><span class="sxs-lookup"><span data-stu-id="90932-176">The internal load balancer distributes network traffic from the web tier to the business tier.</span></span> <span data-ttu-id="90932-177">若要為此負載平衡器提供私人 IP 位址，請建立前端 IP 設定，並將它關聯至 Business 層的子網路。</span><span class="sxs-lookup"><span data-stu-id="90932-177">To give this load balancer a private IP address, create a frontend IP configuration and associate it with the subnet for the business tier.</span></span> <span data-ttu-id="90932-178">請參閱[開始建立內部負載平衡器][lb-internal-create]。</span><span class="sxs-lookup"><span data-stu-id="90932-178">See [Get started creating an Internal load balancer][lb-internal-create].</span></span>

### <a name="sql-server-always-on-availability-groups"></a><span data-ttu-id="90932-179">SQL Server Always On 可用性群組</span><span class="sxs-lookup"><span data-stu-id="90932-179">SQL Server Always On Availability Groups</span></span>

<span data-ttu-id="90932-180">我們建議使用 [Always On 可用性群組][sql-alwayson]來獲得 SQL Server 高可用性。</span><span class="sxs-lookup"><span data-stu-id="90932-180">We recommend [Always On Availability Groups][sql-alwayson] for SQL Server high availability.</span></span> <span data-ttu-id="90932-181">在 Windows Server 2016 之前，Always On 可用性群組需要一個網域控制站，而且可用性群組中的所有節點都必須位於相同的 AD 網域。</span><span class="sxs-lookup"><span data-stu-id="90932-181">Prior to Windows Server 2016, Always On Availability Groups require a domain controller, and all nodes in the availability group must be in the same AD domain.</span></span>

<span data-ttu-id="90932-182">其他各層會透過[可用性群組接聽程式][sql-alwayson-listeners]連線到資料庫。</span><span class="sxs-lookup"><span data-stu-id="90932-182">Other tiers connect to the database through an [availability group listener][sql-alwayson-listeners].</span></span> <span data-ttu-id="90932-183">此接聽程式讓 SQL 用戶端能夠在不知道 SQL Server 實體執行個體名稱的情況下連線。</span><span class="sxs-lookup"><span data-stu-id="90932-183">The listener enables a SQL client to connect without knowing the name of the physical instance of SQL Server.</span></span> <span data-ttu-id="90932-184">存取資料庫的 VM 必須加入該網域。</span><span class="sxs-lookup"><span data-stu-id="90932-184">VMs that access the database must be joined to the domain.</span></span> <span data-ttu-id="90932-185">用戶端 (在此案例中為另一層) 會使用 DNS，將接聽程式的虛擬網路名稱解析為 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="90932-185">The client (in this case, another tier) uses DNS to resolve the listener's virtual network name into IP addresses.</span></span>

<span data-ttu-id="90932-186">設定 SQL Server Always On 可用性群組，如下：</span><span class="sxs-lookup"><span data-stu-id="90932-186">Configure the SQL Server Always On Availability Group as follows:</span></span>

1. <span data-ttu-id="90932-187">建立 Windows Server 容錯移轉叢集 (WSFC) 叢集、SQL Server Always On 可用性群組，以及主要複本。</span><span class="sxs-lookup"><span data-stu-id="90932-187">Create a Windows Server Failover Clustering (WSFC) cluster, a SQL Server Always On Availability Group, and a primary replica.</span></span> <span data-ttu-id="90932-188">如需詳細資訊，請參閱[開始使用 Always On 可用性群組][sql-alwayson-getting-started]。</span><span class="sxs-lookup"><span data-stu-id="90932-188">For more information, see [Getting Started with Always On Availability Groups][sql-alwayson-getting-started].</span></span> 
2. <span data-ttu-id="90932-189">建立具有靜態私人 IP 位址的內部負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="90932-189">Create an internal load balancer with a static private IP address.</span></span>
3. <span data-ttu-id="90932-190">建立可用性群組接聽程式，並將接聽程式的 DNS 名稱對應到內部負載平衡器的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="90932-190">Create an availability group listener, and map the listener's DNS name to the IP address of an internal load balancer.</span></span> 
4. <span data-ttu-id="90932-191">建立 SQL Server 接聽連接埠的負載平衡器規則 (預設為 TCP 連接埠 1433)。</span><span class="sxs-lookup"><span data-stu-id="90932-191">Create a load balancer rule for the SQL Server listening port (TCP port 1433 by default).</span></span> <span data-ttu-id="90932-192">負載平衡器規則必須啟用「浮動 IP」，也稱為「伺服器直接回傳」。</span><span class="sxs-lookup"><span data-stu-id="90932-192">The load balancer rule must enable *floating IP*, also called Direct Server Return.</span></span> <span data-ttu-id="90932-193">這樣會導致 VM 直接回覆用戶端，以啟用與主要複本的直接連線。</span><span class="sxs-lookup"><span data-stu-id="90932-193">This causes the VM to reply directly to the client, which enables a direct connection to the primary replica.</span></span>
  
  > [!NOTE]
  > <span data-ttu-id="90932-194">啟用浮動 IP 時，前端連接埠號碼必須與負載平衡器規則中的後端連接埠號碼相同。</span><span class="sxs-lookup"><span data-stu-id="90932-194">When floating IP is enabled, the front-end port number must be the same as the back-end port number in the load balancer rule.</span></span>
  > 
  > 

<span data-ttu-id="90932-195">當 SQL 用戶端嘗試連線時，負載平衡器會將連線要求路由傳送到主要複本。</span><span class="sxs-lookup"><span data-stu-id="90932-195">When a SQL client tries to connect, the load balancer routes the connection request to the primary replica.</span></span> <span data-ttu-id="90932-196">如果已容錯移轉至另一個複本，負載平衡器會自動將後續要求路由傳送到新的主要複本。</span><span class="sxs-lookup"><span data-stu-id="90932-196">If there is a failover to another replica, the load balancer automatically routes subsequent requests to a new primary replica.</span></span> <span data-ttu-id="90932-197">如需詳細資訊，請參閱[設定 SQL Server Always On 可用性群組的 ILB 接聽程式][sql-alwayson-ilb]。</span><span class="sxs-lookup"><span data-stu-id="90932-197">For more information, see [Configure an ILB listener for SQL Server Always On Availability Groups][sql-alwayson-ilb].</span></span>

<span data-ttu-id="90932-198">在容錯移轉期間，會關閉現有的用戶端連線。</span><span class="sxs-lookup"><span data-stu-id="90932-198">During a failover, existing client connections are closed.</span></span> <span data-ttu-id="90932-199">當容錯移轉完成之後，會將新的連線路由傳送到新的主要複本。</span><span class="sxs-lookup"><span data-stu-id="90932-199">After the failover completes, new connections will be routed to the new primary replica.</span></span>

<span data-ttu-id="90932-200">如果您應用程式的讀取次數明顯比寫入多很多，您可以將某些唯讀查詢卸載至次要複本。</span><span class="sxs-lookup"><span data-stu-id="90932-200">If your application makes significantly more reads than writes, you can offload some of the read-only queries to a secondary replica.</span></span> <span data-ttu-id="90932-201">請參閱[使用接聽程式連接到唯讀次要複本 (唯讀路由)][sql-alwayson-read-only-routing]。</span><span class="sxs-lookup"><span data-stu-id="90932-201">See [Using a Listener to Connect to a Read-Only Secondary Replica (Read-Only Routing)][sql-alwayson-read-only-routing].</span></span>

<span data-ttu-id="90932-202">執行可用性群組的[強制手動容錯移轉][sql-alwayson-force-failover]來測試您的部署。</span><span class="sxs-lookup"><span data-stu-id="90932-202">Test your deployment by [forcing a manual failover][sql-alwayson-force-failover] of the availability group.</span></span>

### <a name="jumpbox"></a><span data-ttu-id="90932-203">Jumpbox</span><span class="sxs-lookup"><span data-stu-id="90932-203">Jumpbox</span></span>

<span data-ttu-id="90932-204">Jumpbox 將具備最低效能需求，因此，請針對 Jumpbox 選取較小的 VM 大小，例如標準 A1。</span><span class="sxs-lookup"><span data-stu-id="90932-204">The jumpbox will have minimal performance requirements, so select a small VM size for the jumpbox such as Standard A1.</span></span> 

<span data-ttu-id="90932-205">針對 Jumpbox 建立[公用 IP 位址]。</span><span class="sxs-lookup"><span data-stu-id="90932-205">Create a [public IP address] for the jumpbox.</span></span> <span data-ttu-id="90932-206">將 Jumpbox 放置於與其他 VM 相同的 VNet，但在個別的管理子網路中。</span><span class="sxs-lookup"><span data-stu-id="90932-206">Place the jumpbox in the same VNet as the other VMs, but in a separate management subnet.</span></span>

<span data-ttu-id="90932-207">不允許從公用網際網路對執行應用程式工作負載的 VM 進行 RDP 存取。</span><span class="sxs-lookup"><span data-stu-id="90932-207">Do not allow RDP access from the public Internet to the VMs that run the application workload.</span></span> <span data-ttu-id="90932-208">對這些 VM 所做的所有 RDP 存取都必須改為透過 Jumpbox 進行。</span><span class="sxs-lookup"><span data-stu-id="90932-208">Instead, all RDP access to these VMs must come through the jumpbox.</span></span> <span data-ttu-id="90932-209">系統管理員會登入 Jumpbox，然後從 Jumpbox 登入其他 VM。</span><span class="sxs-lookup"><span data-stu-id="90932-209">An administrator logs into the jumpbox, and then logs into the other VM from the jumpbox.</span></span> <span data-ttu-id="90932-210">Jumpbox 允許來自網際網路，但只來自已知且安全 IP 位址的 RDP 流量。</span><span class="sxs-lookup"><span data-stu-id="90932-210">The jumpbox allows RDP traffic from the Internet, but only from known, safe IP addresses.</span></span>

<span data-ttu-id="90932-211">若要保護 Jumpbox，請建立 NSG 並將它套用到 Jumpbox 子網路。</span><span class="sxs-lookup"><span data-stu-id="90932-211">To secure the jumpbox, create an NSG and apply it to the jumpbox subnet.</span></span> <span data-ttu-id="90932-212">新增 NSG 規則，只允許來自一組安全公用 IP 位址的 RDP 連線。</span><span class="sxs-lookup"><span data-stu-id="90932-212">Add an NSG rule that allows RDP connections only from a safe set of public IP addresses.</span></span> <span data-ttu-id="90932-213">NSG 可以連接到子網路或 Jumpbox NIC。</span><span class="sxs-lookup"><span data-stu-id="90932-213">The NSG can be attached either to the subnet or to the jumpbox NIC.</span></span> <span data-ttu-id="90932-214">在此情況下，我們建議將它連接到 NIC，如此一來，就只允許 RDP 流量到 Jumpbox，即使您將其他 VM 新增至相同的子網路也一樣。</span><span class="sxs-lookup"><span data-stu-id="90932-214">In this case, we recommend attaching it to the NIC, so RDP traffic is permitted only to the jumpbox, even if you add other VMs to the same subnet.</span></span>

<span data-ttu-id="90932-215">設定其他子網路的 NSG，允許來自管理子網路的 RDP 流量。</span><span class="sxs-lookup"><span data-stu-id="90932-215">Configure the NSGs for the other subnets to allow RDP traffic from the management subnet.</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="90932-216">可用性考量</span><span class="sxs-lookup"><span data-stu-id="90932-216">Availability considerations</span></span>

<span data-ttu-id="90932-217">在資料庫層，具有多個 VM 不會自動會轉譯成高度可用的資料庫。</span><span class="sxs-lookup"><span data-stu-id="90932-217">At the database tier, having multiple VMs does not automatically translate into a highly available database.</span></span> <span data-ttu-id="90932-218">針對關聯式資料庫，您通常需要使用複寫和容錯移轉來實現高可用性。</span><span class="sxs-lookup"><span data-stu-id="90932-218">For a relational database, you will typically need to use replication and failover to achieve high availability.</span></span> <span data-ttu-id="90932-219">針對 SQL Server，我們建議使用 [Always On 可用性群組][sql-alwayson]。</span><span class="sxs-lookup"><span data-stu-id="90932-219">For SQL Server, we recommend using [Always On Availability Groups][sql-alwayson].</span></span> 

<span data-ttu-id="90932-220">如果您需要比[適用於 VM 的 Azure SLA][vm-sla] 所提供更高的可用性，請跨兩個區域複寫應用程式並使用 Azure 流量管理員進行容錯移轉。</span><span class="sxs-lookup"><span data-stu-id="90932-220">If you need higher availability than the [Azure SLA for VMs][vm-sla] provides, replicate the application across two regions and use Azure Traffic Manager for failover.</span></span> <span data-ttu-id="90932-221">如需詳細資訊，請參閱[在多個區域中執行 Windows VM 以獲得高可用性][multi-dc]。</span><span class="sxs-lookup"><span data-stu-id="90932-221">For more information, see [Run Windows VMs in multiple regions for high availability][multi-dc].</span></span>   

## <a name="security-considerations"></a><span data-ttu-id="90932-222">安全性考量</span><span class="sxs-lookup"><span data-stu-id="90932-222">Security considerations</span></span>

<span data-ttu-id="90932-223">將機密的待用資料加密，並使用 [Azure Key Vault][azure-key-vault] 來管理資料庫加密金鑰。</span><span class="sxs-lookup"><span data-stu-id="90932-223">Encrypt sensitive data at rest and use [Azure Key Vault][azure-key-vault] to manage the database encryption keys.</span></span> <span data-ttu-id="90932-224">Key Vault 可以在硬體安全模組 (HSM) 中儲存加密金鑰。</span><span class="sxs-lookup"><span data-stu-id="90932-224">Key Vault can store encryption keys in hardware security modules (HSMs).</span></span> <span data-ttu-id="90932-225">如需詳細資訊，請參閱[為 Azure VM 上的 SQL Server 設定 Azure Key Vault 整合][sql-keyvault]。同時也建議您將應用程式祕密 (例如資料庫連接字串) 儲存於 Key Vault 中。</span><span class="sxs-lookup"><span data-stu-id="90932-225">For more information, see [Configure Azure Key Vault Integration for SQL Server on Azure VMs][sql-keyvault] It's also recommended to store application secrets, such as database connection strings, in Key Vault.</span></span>

<span data-ttu-id="90932-226">請考慮新增網路虛擬設備 (NVA)，在網際網路和 Azure 虛擬網路之間建立 DMZ。</span><span class="sxs-lookup"><span data-stu-id="90932-226">Consider adding a network virtual appliance (NVA) to create a DMZ between the Internet and the Azure virtual network.</span></span> <span data-ttu-id="90932-227">NVA 是虛擬設備的通稱，可以執行網路相關的工作，例如防火牆、封包檢查、稽核和自訂路由傳送。</span><span class="sxs-lookup"><span data-stu-id="90932-227">NVA is a generic term for a virtual appliance that can perform network-related tasks, such as firewall, packet inspection, auditing, and custom routing.</span></span> <span data-ttu-id="90932-228">如需詳細資訊，請參閱[實作 Azure 和網際網路之間的 DMZ][dmz]。</span><span class="sxs-lookup"><span data-stu-id="90932-228">For more information, see [Implementing a DMZ between Azure and the Internet][dmz].</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="90932-229">延展性考量</span><span class="sxs-lookup"><span data-stu-id="90932-229">Scalability considerations</span></span>

<span data-ttu-id="90932-230">負載平衡器會將網路流量散發到 Web 和 Business 層。</span><span class="sxs-lookup"><span data-stu-id="90932-230">The load balancers distribute network traffic to the web and business tiers.</span></span> <span data-ttu-id="90932-231">藉由新增 VM 執行個體進行水平調整。</span><span class="sxs-lookup"><span data-stu-id="90932-231">Scale horizontally by adding new VM instances.</span></span> <span data-ttu-id="90932-232">請注意，您可以根據負載，分別調整 Web 和 Business 層。</span><span class="sxs-lookup"><span data-stu-id="90932-232">Note that you can scale the web and business tiers independently, based on load.</span></span> <span data-ttu-id="90932-233">為了降低因需要維護用戶端親和性而造成的可能複雜性，Web 層中的 VM 應該是無狀態的。</span><span class="sxs-lookup"><span data-stu-id="90932-233">To reduce possible complications caused by the need to maintain client affinity, the VMs in the web tier should be stateless.</span></span> <span data-ttu-id="90932-234">裝載商務邏輯的 VM 也應該是無狀態的。</span><span class="sxs-lookup"><span data-stu-id="90932-234">The VMs hosting the business logic should also be stateless.</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="90932-235">管理性考量</span><span class="sxs-lookup"><span data-stu-id="90932-235">Manageability considerations</span></span>

<span data-ttu-id="90932-236">使用集中式管理工具 (例如 [Azure 自動化][azure-administration]、[Microsoft Operations Management Suite][operations-management-suite]、[Chef][chef] 或 [Puppet][puppet]) 來簡化整個系統的管理。</span><span class="sxs-lookup"><span data-stu-id="90932-236">Simplify management of the entire system by using centralized administration tools such as [Azure Automation][azure-administration], [Microsoft Operations Management Suite][operations-management-suite], [Chef][chef], or [Puppet][puppet].</span></span> <span data-ttu-id="90932-237">這些工具可以合併擷取自多個 VM 的診斷和健康情況資訊，以提供系統的整體檢視。</span><span class="sxs-lookup"><span data-stu-id="90932-237">These tools can consolidate diagnostic and health information captured from multiple VMs to provide an overall view of the system.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="90932-238">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="90932-238">Deploy the solution</span></span>

<span data-ttu-id="90932-239">此參考架構的部署可在 [GitHub][github-folder] 上取得。</span><span class="sxs-lookup"><span data-stu-id="90932-239">A deployment for this reference architecture is available on [GitHub][github-folder].</span></span> 

### <a name="prerequisites"></a><span data-ttu-id="90932-240">必要條件</span><span class="sxs-lookup"><span data-stu-id="90932-240">Prerequisites</span></span>

<span data-ttu-id="90932-241">在您可以將參考架構部署到自己的訂用帳戶之前，必須執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="90932-241">Before you can deploy the reference architecture to your own subscription, you must perform the following steps.</span></span>

1. <span data-ttu-id="90932-242">複製、派生或下載適用於 [AzureCAT 參考架構][ref-arch-repo] GitHub 存放庫的 zip 檔案。</span><span class="sxs-lookup"><span data-stu-id="90932-242">Clone, fork, or download the zip file for the [AzureCAT reference architectures][ref-arch-repo] GitHub repository.</span></span>

2. <span data-ttu-id="90932-243">確定您已在電腦上安裝 Azure CLI 2.0。</span><span class="sxs-lookup"><span data-stu-id="90932-243">Make sure you have the Azure CLI 2.0 installed on your computer.</span></span> <span data-ttu-id="90932-244">若要安裝 CLI，請依照[安裝 Azure CLI 2.0][azure-cli-2] 中的指示進行。</span><span class="sxs-lookup"><span data-stu-id="90932-244">To install the CLI, follow the instructions in [Install Azure CLI 2.0][azure-cli-2].</span></span>

3. <span data-ttu-id="90932-245">安裝 [Azure 建置組塊][azbb] npm 封裝。</span><span class="sxs-lookup"><span data-stu-id="90932-245">Install the [Azure building blocks][azbb] npm package.</span></span>

  ```bash
  npm install -g @mspnp/azure-building-blocks
  ```

4. <span data-ttu-id="90932-246">從命令提示字元、bash 提示字元或 PowerShell 提示字元中，使用下列其中一個命令登入 Azure 帳戶，並遵循提示進行。</span><span class="sxs-lookup"><span data-stu-id="90932-246">From a command prompt, bash prompt, or PowerShell prompt, login to your Azure account by using one of the commands below, and follow the prompts.</span></span>

  ```bash
  az login
  ```

### <a name="deploy-the-solution-using-azbb"></a><span data-ttu-id="90932-247">使用 azbb 部署解決方案</span><span class="sxs-lookup"><span data-stu-id="90932-247">Deploy the solution using azbb</span></span>

<span data-ttu-id="90932-248">若要以多層式架構 (N-Tier) 應用程式參考架構部署 Windows VM，請依以下步驟進行：</span><span class="sxs-lookup"><span data-stu-id="90932-248">To deploy the Windows VMs for an N-tier application reference architecture, follow these steps:</span></span>

1. <span data-ttu-id="90932-249">瀏覽至您在上述必要條件步驟 1 中複製存放庫的 `virtual-machines\n-tier-windows` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="90932-249">Navigate to the `virtual-machines\n-tier-windows` folder for the repository you cloned in step 1 of the pre-requisites above.</span></span>

2. <span data-ttu-id="90932-250">參數檔案會指定部署中每個 VM 的預設管理員使用者名稱及密碼。</span><span class="sxs-lookup"><span data-stu-id="90932-250">The parameter file specifies a default adminstrator user name and password for each VM in the deployment.</span></span> <span data-ttu-id="90932-251">您必須在部署參考架構前先變更這些內容。</span><span class="sxs-lookup"><span data-stu-id="90932-251">You must change these before you deploy the reference architecture.</span></span> <span data-ttu-id="90932-252">開啟 `n-tier-windows.json` 檔案，並用新設定取代每個 **adminUsername** 和 **adminPassword** 欄位。</span><span class="sxs-lookup"><span data-stu-id="90932-252">Open the `n-tier-windows.json` file and replace each **adminUsername** and **adminPassword** field with your new settings.</span></span>
  
  > [!NOTE]
  > <span data-ttu-id="90932-253">在部署期間，會有多個指令碼在 **VirtualMachineExtension** 物件及 **VirtualMachine** 物件中部份的 **extensions** 設定中執行。</span><span class="sxs-lookup"><span data-stu-id="90932-253">There are multiple scripts that run during this deployment both in the  **VirtualMachineExtension** objects and in the **extensions** settings for some of the **VirtualMachine** objects.</span></span> <span data-ttu-id="90932-254">這些指令碼需要您之前變更的管理員使用者名稱和密碼。</span><span class="sxs-lookup"><span data-stu-id="90932-254">Some of these scripts require the administrator user name and password that you have just changed.</span></span> <span data-ttu-id="90932-255">建議您檢閱這些指令碼，以確保指定了正確的認證。</span><span class="sxs-lookup"><span data-stu-id="90932-255">It's recommended that you review these scripts to ensure that you specified the correct credentials.</span></span> <span data-ttu-id="90932-256">若您並未指定正確的認證，部署可能失敗。</span><span class="sxs-lookup"><span data-stu-id="90932-256">The deployment may fail if you have not specified the correct credentials.</span></span>
  > 
  > 

<span data-ttu-id="90932-257">儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="90932-257">Save the file.</span></span>

3. <span data-ttu-id="90932-258">使用如下所示的 **azbb** 命令列來部署參考架構。</span><span class="sxs-lookup"><span data-stu-id="90932-258">Deploy the reference architecture using the **azbb** command line tool as shown below.</span></span>

  ```bash
  azbb -s <your subscription_id> -g <your resource_group_name> -l <azure region> -p n-tier-windows.json --deploy
  ```

<span data-ttu-id="90932-259">如需使用 Azure 組建區塊部署此範例參考架構的詳細資訊，請瀏覽 [GitHub 存放庫][git]。</span><span class="sxs-lookup"><span data-stu-id="90932-259">For more information on deploying this sample reference architecture using Azure Building Blocks, visit the [GitHub repository][git].</span></span>


<!-- links -->
[dmz]: ../dmz/secure-vnet-dmz.md
[multi-dc]: multi-region-application.md
[multi-vm]: multi-vm.md
[n-tier]: n-tier.md
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[azure-administration]: /azure/automation/automation-intro
[azure-availability-sets]: /azure/virtual-machines/virtual-machines-windows-manage-availability#configure-each-application-tier-into-separate-availability-sets
[azure-cli]: /azure/virtual-machines-command-line-tools
[azure-cli-2]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[azure-key-vault]: https://azure.microsoft.com/services/key-vault
[防禦主機]: https://en.wikipedia.org/wiki/Bastion_host
[cidr]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[git]: https://github.com/mspnp/template-building-blocks
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/n-tier-windows
[lb-external-create]: /azure/load-balancer/load-balancer-get-started-internet-portal
[lb-internal-create]: /azure/load-balancer/load-balancer-get-started-ilb-arm-portal
[load-balancer-external]: /azure/load-balancer/load-balancer-internet-overview
[load-balancer-internal]: /azure/load-balancer/load-balancer-internal-overview
[nsg]: /azure/virtual-network/virtual-networks-nsg
[operations-management-suite]: https://www.microsoft.com/server-cloud/operations-management-suite/overview.aspx
[plan-network]: /azure/virtual-network/virtual-network-vnet-plan-design-arm
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[公用 IP 位址]: /azure/virtual-network/virtual-network-ip-addresses-overview-arm
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[sql-alwayson]: https://msdn.microsoft.com/library/hh510230.aspx
[sql-alwayson-force-failover]: https://msdn.microsoft.com/library/ff877957.aspx
[sql-alwayson-getting-started]: https://msdn.microsoft.com/library/gg509118.aspx
[sql-alwayson-ilb]: /azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener
[sql-alwayson-listeners]: https://msdn.microsoft.com/library/hh213417.aspx
[sql-alwayson-read-only-routing]: https://technet.microsoft.com/library/hh213417.aspx#ConnectToSecondary
[sql-keyvault]: /azure/virtual-machines/virtual-machines-windows-ps-sql-keyvault
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[vnet faq]: /azure/virtual-network/virtual-networks-faq
[wsfc-whats-new]: https://technet.microsoft.com/windows-server-docs/failover-clustering/whats-new-in-failover-clustering
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[visio-download]: https://archcenter.azureedge.net/cdn/vm-reference-architectures.vsdx
[0]: ./images/n-tier-diagram.png "使用 Microsoft Azure 的多層式架構"
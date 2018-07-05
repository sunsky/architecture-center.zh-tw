---
title: 具有 SQL Server 的多層式架構 (N-tier) 應用程式
description: 如何在 Azure 上實作多層式架構，以取得可用性、安全性、延展性及管理功能。
author: MikeWasson
ms.date: 06/23/2018
ms.openlocfilehash: 7c8184d25cf6b3bd358adc2728329fd3bd08503a
ms.sourcegitcommit: 58d93e7ac9a6d44d5668a187a6827d7cd4f5a34d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2018
ms.locfileid: "37142296"
---
# <a name="n-tier-application-with-sql-server"></a><span data-ttu-id="7a867-103">具有 SQL Server 的多層式架構 (N-tier) 應用程式</span><span class="sxs-lookup"><span data-stu-id="7a867-103">N-tier application with SQL Server</span></span>

<span data-ttu-id="7a867-104">此參考架構展示如何在 Windows 上使用 SQL Server 作為資料層，來部署針對多層式架構 (N-Tier) 應用程式設定的 VM 和虛擬網路。</span><span class="sxs-lookup"><span data-stu-id="7a867-104">This reference architecture shows how to deploy VMs and a virtual network configured for an N-tier application, using SQL Server on Windows for the data tier.</span></span> [<span data-ttu-id="7a867-105">**部署這個解決方案**。</span><span class="sxs-lookup"><span data-stu-id="7a867-105">**Deploy this solution**.</span></span>](#deploy-the-solution) 

<span data-ttu-id="7a867-106">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="7a867-106">![[0]][0]</span></span>

<span data-ttu-id="7a867-107">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="7a867-107">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="7a867-108">架構</span><span class="sxs-lookup"><span data-stu-id="7a867-108">Architecture</span></span> 

<span data-ttu-id="7a867-109">此架構具有下列元件：</span><span class="sxs-lookup"><span data-stu-id="7a867-109">The architecture has the following components:</span></span>

* <span data-ttu-id="7a867-110">**資源群組。**</span><span class="sxs-lookup"><span data-stu-id="7a867-110">**Resource group.**</span></span> <span data-ttu-id="7a867-111">[資源群組][resource-manager-overview]可用來將資源組合在一起，讓它們可以依存留期、擁有者或其他準則管理。</span><span class="sxs-lookup"><span data-stu-id="7a867-111">[Resource groups][resource-manager-overview] are used to group resources so they can be managed by lifetime, owner, or other criteria.</span></span>

* <span data-ttu-id="7a867-112">**虛擬網路 (VNet) 和子網路。**</span><span class="sxs-lookup"><span data-stu-id="7a867-112">**Virtual network (VNet) and subnets.**</span></span> <span data-ttu-id="7a867-113">每部 Azure VM 都會部署到可以分割成多個子網路的 VNet。</span><span class="sxs-lookup"><span data-stu-id="7a867-113">Every Azure VM is deployed into a VNet that can be segmented into multiple subnets.</span></span> <span data-ttu-id="7a867-114">針對每一層建立不同的子網路。</span><span class="sxs-lookup"><span data-stu-id="7a867-114">Create a separate subnet for each tier.</span></span> 

* <span data-ttu-id="7a867-115">**NSG。**</span><span class="sxs-lookup"><span data-stu-id="7a867-115">**NSGs.**</span></span> <span data-ttu-id="7a867-116">使用[網路安全性群組][nsg] (NSG) 來限制 VNet 內的網路流量。</span><span class="sxs-lookup"><span data-stu-id="7a867-116">Use [network security groups][nsg] (NSGs) to restrict network traffic within the VNet.</span></span> <span data-ttu-id="7a867-117">例如，在如下所示的 3 層式架構中，資料庫層不接受來自 Web 前端的流量，只接受來自 Business 層和管理子網路的流量。</span><span class="sxs-lookup"><span data-stu-id="7a867-117">For example, in the 3-tier architecture shown here, the database tier does not accept traffic from the web front end, only from the business tier and the management subnet.</span></span>

* <span data-ttu-id="7a867-118">**虛擬機器**。</span><span class="sxs-lookup"><span data-stu-id="7a867-118">**Virtual machines**.</span></span> <span data-ttu-id="7a867-119">如需有關設定 VM 的建議，請參閱[在 Azure 上執行 Windows VM](./windows-vm.md) 和[在 Azure 上執行 Linux VM](./linux-vm.md)。</span><span class="sxs-lookup"><span data-stu-id="7a867-119">For recommendations on configuring VMs, see [Run a Windows VM on Azure](./windows-vm.md) and [Run a Linux VM on Azure](./linux-vm.md).</span></span>

* <span data-ttu-id="7a867-120">**可用性設定組。**</span><span class="sxs-lookup"><span data-stu-id="7a867-120">**Availability sets.**</span></span> <span data-ttu-id="7a867-121">針對每一層建立[可用性設定組][azure-availability-sets]，並且在每一層中至少佈建兩個 VM。</span><span class="sxs-lookup"><span data-stu-id="7a867-121">Create an [availability set][azure-availability-sets] for each tier, and provision at least two VMs in each tier.</span></span> <span data-ttu-id="7a867-122">這讓 VM 能夠符合適用於 VM 之較高[服務等級協定 (SLA)][vm-sla] 的資格。</span><span class="sxs-lookup"><span data-stu-id="7a867-122">This makes the VMs eligible for a higher [service level agreement (SLA)][vm-sla] for VMs.</span></span> 

* <span data-ttu-id="7a867-123">**VM 擴展集** (未顯示)。</span><span class="sxs-lookup"><span data-stu-id="7a867-123">**VM scale set** (not shown).</span></span> <span data-ttu-id="7a867-124">[VM 擴展集][vmss]是使用可用性設定組的替代方案。</span><span class="sxs-lookup"><span data-stu-id="7a867-124">A [VM scale set][vmss] is an alternative to using an availability set.</span></span> <span data-ttu-id="7a867-125">擴展集可讓您輕鬆地根據預先定義的規則，手動或自動相應放大層中的 VM。</span><span class="sxs-lookup"><span data-stu-id="7a867-125">A scale sets makes it easy to scale out the VMs in a tier, either manually or automatically based on predefined rules.</span></span>

* <span data-ttu-id="7a867-126">**Azure 負載平衡器。**</span><span class="sxs-lookup"><span data-stu-id="7a867-126">**Azure Load balancers.**</span></span> <span data-ttu-id="7a867-127">[負載平衡器][load-balancer]會將連入網際網路要求散發到 VM 執行個體。</span><span class="sxs-lookup"><span data-stu-id="7a867-127">The [load balancers][load-balancer] distribute incoming Internet requests to the VM instances.</span></span> <span data-ttu-id="7a867-128">使用[公用負載平衡器][load-balancer-external]，可將連入的網際網路流量散發到 Web 層，使用[內部負載平衡器][load-balancer-internal]，則可將來自 Web 層的網路流量散發到 Business 層。</span><span class="sxs-lookup"><span data-stu-id="7a867-128">Use a [public load balancer][load-balancer-external] to distribute incoming Internet traffic to the web tier, and an [internal load balancer][load-balancer-internal] to distribute network traffic from the web tier to the business tier.</span></span>

* <span data-ttu-id="7a867-129">**公用 IP 位址**。</span><span class="sxs-lookup"><span data-stu-id="7a867-129">**Public IP address**.</span></span> <span data-ttu-id="7a867-130">公用負載平衡器需要公用 IP 位址才能接收網際網路流量。</span><span class="sxs-lookup"><span data-stu-id="7a867-130">A public IP address is needed for the public load balancer to receive Internet traffic.</span></span>

* <span data-ttu-id="7a867-131">**Jumpbox。**</span><span class="sxs-lookup"><span data-stu-id="7a867-131">**Jumpbox.**</span></span> <span data-ttu-id="7a867-132">也稱為[防禦主機]。</span><span class="sxs-lookup"><span data-stu-id="7a867-132">Also called a [bastion host].</span></span> <span data-ttu-id="7a867-133">網路上系統管理員用來連線到其他 VM 的安全 VM。</span><span class="sxs-lookup"><span data-stu-id="7a867-133">A secure VM on the network that administrators use to connect to the other VMs.</span></span> <span data-ttu-id="7a867-134">Jumpbox 具有 NSG，能夠儘允許來自安全清單上公用 IP 位址的遠端流量。</span><span class="sxs-lookup"><span data-stu-id="7a867-134">The jumpbox has an NSG that allows remote traffic only from public IP addresses on a safe list.</span></span> <span data-ttu-id="7a867-135">NSG 應該允許遠端桌面 (RDP) 流量。</span><span class="sxs-lookup"><span data-stu-id="7a867-135">The NSG should permit remote desktop (RDP) traffic.</span></span>

* <span data-ttu-id="7a867-136">**SQL Server Always On 可用性群組。**</span><span class="sxs-lookup"><span data-stu-id="7a867-136">**SQL Server Always On Availability Group.**</span></span> <span data-ttu-id="7a867-137">藉由啟用複寫和容錯移轉，在資料層提供高可用性。</span><span class="sxs-lookup"><span data-stu-id="7a867-137">Provides high availability at the data tier, by enabling replication and failover.</span></span> <span data-ttu-id="7a867-138">它會使用 Windows Server 容錯移轉叢集 (WSFC) 技術來進行容錯移轉。</span><span class="sxs-lookup"><span data-stu-id="7a867-138">It uses Windows Server Failover Cluster (WSFC) technology for failover.</span></span>

* <span data-ttu-id="7a867-139">**Active Directory Domain Services (AD DS) 伺服器**。</span><span class="sxs-lookup"><span data-stu-id="7a867-139">**Active Directory Domain Services (AD DS) Servers**.</span></span> <span data-ttu-id="7a867-140">容錯移轉叢集及其相關聯叢集角色的電腦物件，是在 Active Directory Domain Services (AD DS) 中建立。</span><span class="sxs-lookup"><span data-stu-id="7a867-140">The computer objects for the failover cluster and its associated clustered roles are created in Active Directory Domain Services (AD DS).</span></span>

* <span data-ttu-id="7a867-141">**雲端見證**。</span><span class="sxs-lookup"><span data-stu-id="7a867-141">**Cloud Witness**.</span></span> <span data-ttu-id="7a867-142">容錯移轉叢集需要它一半以上的節點執行，也就是具有仲裁。</span><span class="sxs-lookup"><span data-stu-id="7a867-142">A failover cluster requires more than half of its nodes to be running, which is known as having quorum.</span></span> <span data-ttu-id="7a867-143">如果叢集只有兩個節點，網路磁碟分割可能會導致每個節點都認為它是主要節點。</span><span class="sxs-lookup"><span data-stu-id="7a867-143">If the cluster has just two nodes, a network partition could cause each node to think it's the master node.</span></span> <span data-ttu-id="7a867-144">在此情況下，您需要「見證」以打破和局，並建立仲裁。</span><span class="sxs-lookup"><span data-stu-id="7a867-144">In that case, you need a *witness* to break ties and establish quorum.</span></span> <span data-ttu-id="7a867-145">見證是例如共用磁碟的資源，可以作為打破和局項目以建立仲裁。</span><span class="sxs-lookup"><span data-stu-id="7a867-145">A witness is a resource such as a shared disk that can act as a tie breaker to establish quorum.</span></span> <span data-ttu-id="7a867-146">雲端見證是使用 Azure Blob 儲存體的見證類型。</span><span class="sxs-lookup"><span data-stu-id="7a867-146">Cloud Witness is a type of witness that uses Azure Blob Storage.</span></span> <span data-ttu-id="7a867-147">若要深入了解有關仲裁的概念，請參閱＜[了解叢集和集區仲裁](/windows-server/storage/storage-spaces/understand-quorum)＞。</span><span class="sxs-lookup"><span data-stu-id="7a867-147">To learn more about the concept of quorum, see [Understanding cluster and pool quorum](/windows-server/storage/storage-spaces/understand-quorum).</span></span> <span data-ttu-id="7a867-148">如需雲端見證的詳細資訊，請參閱＜[為容錯移轉叢集部署雲端見證](/windows-server/failover-clustering/deploy-cloud-witness)＞。</span><span class="sxs-lookup"><span data-stu-id="7a867-148">For more information about Cloud Witness, see [Deploy a Cloud Witness for a Failover Cluster](/windows-server/failover-clustering/deploy-cloud-witness).</span></span> 

* <span data-ttu-id="7a867-149">**Azure DNS**。</span><span class="sxs-lookup"><span data-stu-id="7a867-149">**Azure DNS**.</span></span> <span data-ttu-id="7a867-150">[Azure DNS][azure-dns] 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構提供名稱解析。</span><span class="sxs-lookup"><span data-stu-id="7a867-150">[Azure DNS][azure-dns] is a hosting service for DNS domains, providing name resolution using Microsoft Azure infrastructure.</span></span> <span data-ttu-id="7a867-151">只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。</span><span class="sxs-lookup"><span data-stu-id="7a867-151">By hosting your domains in Azure, you can manage your DNS records using the same credentials, APIs, tools, and billing as your other Azure services.</span></span>

## <a name="recommendations"></a><span data-ttu-id="7a867-152">建議</span><span class="sxs-lookup"><span data-stu-id="7a867-152">Recommendations</span></span>

<span data-ttu-id="7a867-153">您的需求可能和此處所述的架構不同。</span><span class="sxs-lookup"><span data-stu-id="7a867-153">Your requirements might differ from the architecture described here.</span></span> <span data-ttu-id="7a867-154">請使用以下建議作為起點。</span><span class="sxs-lookup"><span data-stu-id="7a867-154">Use these recommendations as a starting point.</span></span> 

### <a name="vnet--subnets"></a><span data-ttu-id="7a867-155">VNet / 子網路</span><span class="sxs-lookup"><span data-stu-id="7a867-155">VNet / Subnets</span></span>

<span data-ttu-id="7a867-156">當您建立 VNet 時，請判斷您在每個子網路中的資源需要多少個 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="7a867-156">When you create the VNet, determine how many IP addresses your resources in each subnet require.</span></span> <span data-ttu-id="7a867-157">使用 [CIDR] 標記法，針對所需的 IP 位址指定子網路遮罩和夠大的 VNet 位址範圍。</span><span class="sxs-lookup"><span data-stu-id="7a867-157">Specify a subnet mask and a VNet address range large enough for the required IP addresses, using [CIDR] notation.</span></span> <span data-ttu-id="7a867-158">使用落在標準[私人 IP 位址區塊][private-ip-space]內的位址空間，其為 10.0.0.0/8、172.16.0.0/12 及 192.168.0.0/16。</span><span class="sxs-lookup"><span data-stu-id="7a867-158">Use an address space that falls within the standard [private IP address blocks][private-ip-space], which are 10.0.0.0/8, 172.16.0.0/12, and 192.168.0.0/16.</span></span>

<span data-ttu-id="7a867-159">選擇不會與您的內部部署網路重疊的位址範圍，以防您稍後需要在 VNet 和您的內部部署網路之間設定閘道。</span><span class="sxs-lookup"><span data-stu-id="7a867-159">Choose an address range that does not overlap with your on-premises network, in case you need to set up a gateway between the VNet and your on-premise network later.</span></span> <span data-ttu-id="7a867-160">一旦建立 VNet 之後，就無法變更位址範圍。</span><span class="sxs-lookup"><span data-stu-id="7a867-160">Once you create the VNet, you can't change the address range.</span></span>

<span data-ttu-id="7a867-161">請記得以功能和安全性需求來設計子網路。</span><span class="sxs-lookup"><span data-stu-id="7a867-161">Design subnets with functionality and security requirements in mind.</span></span> <span data-ttu-id="7a867-162">位於相同層或角色的所有 VM 都應移入相同的子網路，它可以是安全性界限。</span><span class="sxs-lookup"><span data-stu-id="7a867-162">All VMs within the same tier or role should go into the same subnet, which can be a security boundary.</span></span> <span data-ttu-id="7a867-163">如需設計 VNet 和子網路的詳細資訊，請參閱[規劃和設計 Azure 虛擬網路][plan-network]。</span><span class="sxs-lookup"><span data-stu-id="7a867-163">For more information about designing VNets and subnets, see [Plan and design Azure Virtual Networks][plan-network].</span></span>

### <a name="load-balancers"></a><span data-ttu-id="7a867-164">負載平衡器</span><span class="sxs-lookup"><span data-stu-id="7a867-164">Load balancers</span></span>

<span data-ttu-id="7a867-165">不要直接將 VM 公開至網際網路，而是改為每個 VM 提供私人 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="7a867-165">Do not expose the VMs directly to the Internet, but instead give each VM a private IP address.</span></span> <span data-ttu-id="7a867-166">用戶端會使用公用負載平衡器的 IP 位址來連線。</span><span class="sxs-lookup"><span data-stu-id="7a867-166">Clients connect using the IP address of the public load balancer.</span></span>

<span data-ttu-id="7a867-167">定義負載平衡器規則，以將網路流量導向至 VM。</span><span class="sxs-lookup"><span data-stu-id="7a867-167">Define load balancer rules to direct network traffic to the VMs.</span></span> <span data-ttu-id="7a867-168">例如，若要啟用 HTTP 流量，請建立一個規則，將前端設定的連接埠 80 對應至後端位址集區的連接埠 80。</span><span class="sxs-lookup"><span data-stu-id="7a867-168">For example, to enable HTTP traffic, create a rule that maps port 80 from the front-end configuration to port 80 on the back-end address pool.</span></span> <span data-ttu-id="7a867-169">當用戶端將 HTTP 要求傳送到連接埠 80 時，負載平衡器會藉由使用[雜湊演算法][load-balancer-hashing] (其中包含來源 IP 位址) 來選取後端 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="7a867-169">When a client sends an HTTP request to port 80, the load balancer selects a back-end IP address by using a [hashing algorithm][load-balancer-hashing] that includes the source IP address.</span></span> <span data-ttu-id="7a867-170">如此一來，就會將用戶端要求散發到所有 VM。</span><span class="sxs-lookup"><span data-stu-id="7a867-170">In that way, client requests are distributed across all the VMs.</span></span>

### <a name="network-security-groups"></a><span data-ttu-id="7a867-171">網路安全性群組</span><span class="sxs-lookup"><span data-stu-id="7a867-171">Network security groups</span></span>

<span data-ttu-id="7a867-172">使用 NSG 規則來限制各層之間的流量。</span><span class="sxs-lookup"><span data-stu-id="7a867-172">Use NSG rules to restrict traffic between tiers.</span></span> <span data-ttu-id="7a867-173">例如，在如上所示的 3 層式架構中，Web 層不會直接與資料庫層通訊。</span><span class="sxs-lookup"><span data-stu-id="7a867-173">For example, in the 3-tier architecture shown above, the web tier does not communicate directly with the database tier.</span></span> <span data-ttu-id="7a867-174">若要強制執行此動作，資料庫層應該封鎖來自 Web 層子網路的連入流量。</span><span class="sxs-lookup"><span data-stu-id="7a867-174">To enforce this, the database tier should block incoming traffic from the web tier subnet.</span></span>  

1. <span data-ttu-id="7a867-175">拒絕來自 VNet 的所有輸入流量。</span><span class="sxs-lookup"><span data-stu-id="7a867-175">Deny all inbound traffic from the VNet.</span></span> <span data-ttu-id="7a867-176">(在規則中使用 `VIRTUAL_NETWORK` 標記)。</span><span class="sxs-lookup"><span data-stu-id="7a867-176">(Use the `VIRTUAL_NETWORK` tag in the rule.)</span></span> 
2. <span data-ttu-id="7a867-177">允許來自 Business 層子網路的輸入流量。</span><span class="sxs-lookup"><span data-stu-id="7a867-177">Allow inbound traffic from the business tier subnet.</span></span>  
3. <span data-ttu-id="7a867-178">允許來自資料庫層子網路本身的輸入流量。</span><span class="sxs-lookup"><span data-stu-id="7a867-178">Allow inbound traffic from the database tier subnet itself.</span></span> <span data-ttu-id="7a867-179">此規則允許資料庫 VM 之間的通訊，是進行資料庫複寫和容錯移轉所需的。</span><span class="sxs-lookup"><span data-stu-id="7a867-179">This rule allows communication between the database VMs, which is needed for database replication and failover.</span></span>
4. <span data-ttu-id="7a867-180">允許來自 Jumpbox 子網路的 RDP 流量 (連接埠 3389)。</span><span class="sxs-lookup"><span data-stu-id="7a867-180">Allow RDP traffic (port 3389) from the jumpbox subnet.</span></span> <span data-ttu-id="7a867-181">此規則讓系統管理員能夠從 Jumpbox 連線到資料庫層。</span><span class="sxs-lookup"><span data-stu-id="7a867-181">This rule lets administrators connect to the database tier from the jumpbox.</span></span>

<span data-ttu-id="7a867-182">建立規則 2 &ndash; 4，其優先順序高於第一個規則，因此它們會覆寫它。</span><span class="sxs-lookup"><span data-stu-id="7a867-182">Create rules 2 &ndash; 4 with higher priority than the first rule, so they override it.</span></span>


### <a name="sql-server-always-on-availability-groups"></a><span data-ttu-id="7a867-183">SQL Server Always On 可用性群組</span><span class="sxs-lookup"><span data-stu-id="7a867-183">SQL Server Always On Availability Groups</span></span>

<span data-ttu-id="7a867-184">我們建議使用 [Always On 可用性群組][sql-alwayson]來獲得 SQL Server 高可用性。</span><span class="sxs-lookup"><span data-stu-id="7a867-184">We recommend [Always On Availability Groups][sql-alwayson] for SQL Server high availability.</span></span> <span data-ttu-id="7a867-185">在 Windows Server 2016 之前，Always On 可用性群組需要一個網域控制站，而且可用性群組中的所有節點都必須位於相同的 AD 網域。</span><span class="sxs-lookup"><span data-stu-id="7a867-185">Prior to Windows Server 2016, Always On Availability Groups require a domain controller, and all nodes in the availability group must be in the same AD domain.</span></span>

<span data-ttu-id="7a867-186">其他各層會透過[可用性群組接聽程式][sql-alwayson-listeners]連線到資料庫。</span><span class="sxs-lookup"><span data-stu-id="7a867-186">Other tiers connect to the database through an [availability group listener][sql-alwayson-listeners].</span></span> <span data-ttu-id="7a867-187">此接聽程式讓 SQL 用戶端能夠在不知道 SQL Server 實體執行個體名稱的情況下連線。</span><span class="sxs-lookup"><span data-stu-id="7a867-187">The listener enables a SQL client to connect without knowing the name of the physical instance of SQL Server.</span></span> <span data-ttu-id="7a867-188">存取資料庫的 VM 必須加入該網域。</span><span class="sxs-lookup"><span data-stu-id="7a867-188">VMs that access the database must be joined to the domain.</span></span> <span data-ttu-id="7a867-189">用戶端 (在此案例中為另一層) 會使用 DNS，將接聽程式的虛擬網路名稱解析為 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="7a867-189">The client (in this case, another tier) uses DNS to resolve the listener's virtual network name into IP addresses.</span></span>

<span data-ttu-id="7a867-190">設定 SQL Server Always On 可用性群組，如下：</span><span class="sxs-lookup"><span data-stu-id="7a867-190">Configure the SQL Server Always On Availability Group as follows:</span></span>

1. <span data-ttu-id="7a867-191">建立 Windows Server 容錯移轉叢集 (WSFC) 叢集、SQL Server Always On 可用性群組，以及主要複本。</span><span class="sxs-lookup"><span data-stu-id="7a867-191">Create a Windows Server Failover Clustering (WSFC) cluster, a SQL Server Always On Availability Group, and a primary replica.</span></span> <span data-ttu-id="7a867-192">如需詳細資訊，請參閱[開始使用 Always On 可用性群組][sql-alwayson-getting-started]。</span><span class="sxs-lookup"><span data-stu-id="7a867-192">For more information, see [Getting Started with Always On Availability Groups][sql-alwayson-getting-started].</span></span> 
2. <span data-ttu-id="7a867-193">建立具有靜態私人 IP 位址的內部負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="7a867-193">Create an internal load balancer with a static private IP address.</span></span>
3. <span data-ttu-id="7a867-194">建立可用性群組接聽程式，並將接聽程式的 DNS 名稱對應到內部負載平衡器的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="7a867-194">Create an availability group listener, and map the listener's DNS name to the IP address of an internal load balancer.</span></span> 
4. <span data-ttu-id="7a867-195">建立 SQL Server 接聽連接埠的負載平衡器規則 (預設為 TCP 連接埠 1433)。</span><span class="sxs-lookup"><span data-stu-id="7a867-195">Create a load balancer rule for the SQL Server listening port (TCP port 1433 by default).</span></span> <span data-ttu-id="7a867-196">負載平衡器規則必須啟用「浮動 IP」，也稱為「伺服器直接回傳」。</span><span class="sxs-lookup"><span data-stu-id="7a867-196">The load balancer rule must enable *floating IP*, also called Direct Server Return.</span></span> <span data-ttu-id="7a867-197">這樣會導致 VM 直接回覆用戶端，以啟用與主要複本的直接連線。</span><span class="sxs-lookup"><span data-stu-id="7a867-197">This causes the VM to reply directly to the client, which enables a direct connection to the primary replica.</span></span>
  
   > [!NOTE]
   > <span data-ttu-id="7a867-198">啟用浮動 IP 時，前端連接埠號碼必須與負載平衡器規則中的後端連接埠號碼相同。</span><span class="sxs-lookup"><span data-stu-id="7a867-198">When floating IP is enabled, the front-end port number must be the same as the back-end port number in the load balancer rule.</span></span>
   > 
   > 

<span data-ttu-id="7a867-199">當 SQL 用戶端嘗試連線時，負載平衡器會將連線要求路由傳送到主要複本。</span><span class="sxs-lookup"><span data-stu-id="7a867-199">When a SQL client tries to connect, the load balancer routes the connection request to the primary replica.</span></span> <span data-ttu-id="7a867-200">如果已容錯移轉至另一個複本，負載平衡器會自動將後續要求路由傳送到新的主要複本。</span><span class="sxs-lookup"><span data-stu-id="7a867-200">If there is a failover to another replica, the load balancer automatically routes subsequent requests to a new primary replica.</span></span> <span data-ttu-id="7a867-201">如需詳細資訊，請參閱[設定 SQL Server Always On 可用性群組的 ILB 接聽程式][sql-alwayson-ilb]。</span><span class="sxs-lookup"><span data-stu-id="7a867-201">For more information, see [Configure an ILB listener for SQL Server Always On Availability Groups][sql-alwayson-ilb].</span></span>

<span data-ttu-id="7a867-202">在容錯移轉期間，會關閉現有的用戶端連線。</span><span class="sxs-lookup"><span data-stu-id="7a867-202">During a failover, existing client connections are closed.</span></span> <span data-ttu-id="7a867-203">當容錯移轉完成之後，會將新的連線路由傳送到新的主要複本。</span><span class="sxs-lookup"><span data-stu-id="7a867-203">After the failover completes, new connections will be routed to the new primary replica.</span></span>

<span data-ttu-id="7a867-204">如果您應用程式的讀取次數明顯比寫入多很多，您可以將某些唯讀查詢卸載至次要複本。</span><span class="sxs-lookup"><span data-stu-id="7a867-204">If your application makes significantly more reads than writes, you can offload some of the read-only queries to a secondary replica.</span></span> <span data-ttu-id="7a867-205">請參閱[使用接聽程式連接到唯讀次要複本 (唯讀路由)][sql-alwayson-read-only-routing]。</span><span class="sxs-lookup"><span data-stu-id="7a867-205">See [Using a Listener to Connect to a Read-Only Secondary Replica (Read-Only Routing)][sql-alwayson-read-only-routing].</span></span>

<span data-ttu-id="7a867-206">執行可用性群組的[強制手動容錯移轉][sql-alwayson-force-failover]來測試您的部署。</span><span class="sxs-lookup"><span data-stu-id="7a867-206">Test your deployment by [forcing a manual failover][sql-alwayson-force-failover] of the availability group.</span></span>

### <a name="jumpbox"></a><span data-ttu-id="7a867-207">Jumpbox</span><span class="sxs-lookup"><span data-stu-id="7a867-207">Jumpbox</span></span>

<span data-ttu-id="7a867-208">不允許從公用網際網路對執行應用程式工作負載的 VM 進行 RDP 存取。</span><span class="sxs-lookup"><span data-stu-id="7a867-208">Do not allow RDP access from the public Internet to the VMs that run the application workload.</span></span> <span data-ttu-id="7a867-209">對這些 VM 所做的所有 RDP 存取都必須改為透過 Jumpbox 進行。</span><span class="sxs-lookup"><span data-stu-id="7a867-209">Instead, all RDP access to these VMs must come through the jumpbox.</span></span> <span data-ttu-id="7a867-210">系統管理員會登入 Jumpbox，然後從 Jumpbox 登入其他 VM。</span><span class="sxs-lookup"><span data-stu-id="7a867-210">An administrator logs into the jumpbox, and then logs into the other VM from the jumpbox.</span></span> <span data-ttu-id="7a867-211">Jumpbox 允許來自網際網路，但只來自已知且安全 IP 位址的 RDP 流量。</span><span class="sxs-lookup"><span data-stu-id="7a867-211">The jumpbox allows RDP traffic from the Internet, but only from known, safe IP addresses.</span></span>

<span data-ttu-id="7a867-212">Jumpbox 有最低效能需求，因此選取小的 VM 大小。</span><span class="sxs-lookup"><span data-stu-id="7a867-212">The jumpbox has minimal performance requirements, so select a small VM size.</span></span> <span data-ttu-id="7a867-213">針對 Jumpbox 建立[公用 IP 位址]。</span><span class="sxs-lookup"><span data-stu-id="7a867-213">Create a [public IP address] for the jumpbox.</span></span> <span data-ttu-id="7a867-214">將 Jumpbox 放置於與其他 VM 相同的 VNet，但在個別的管理子網路中。</span><span class="sxs-lookup"><span data-stu-id="7a867-214">Place the jumpbox in the same VNet as the other VMs, but in a separate management subnet.</span></span>

<span data-ttu-id="7a867-215">若要保護 Jumpbox，請新增 NSG 規則，只允許來自一組安全公用 IP 位址的 RDP 連線。</span><span class="sxs-lookup"><span data-stu-id="7a867-215">To secure the jumpbox, add an NSG rule that allows RDP connections only from a safe set of public IP addresses.</span></span> <span data-ttu-id="7a867-216">設定其他子網路的 NSG，允許來自管理子網路的 RDP 流量。</span><span class="sxs-lookup"><span data-stu-id="7a867-216">Configure the NSGs for the other subnets to allow RDP traffic from the management subnet.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="7a867-217">延展性考量</span><span class="sxs-lookup"><span data-stu-id="7a867-217">Scalability considerations</span></span>

<span data-ttu-id="7a867-218">[VM 擴展集][vmss]可協助您部署及管理一組完全相同的 VM。</span><span class="sxs-lookup"><span data-stu-id="7a867-218">[VM scale sets][vmss] help you to deploy and manage a set of identical VMs.</span></span> <span data-ttu-id="7a867-219">擴展集支援根據效能計量自動調整規模。</span><span class="sxs-lookup"><span data-stu-id="7a867-219">Scale sets support autoscaling based on performance metrics.</span></span> <span data-ttu-id="7a867-220">當 VM 上的負載增加時，就會將其他 VM 自動新增到負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="7a867-220">As the load on the VMs increases, additional VMs are automatically added to the load balancer.</span></span> <span data-ttu-id="7a867-221">如果您需要快速相應放大 VM 數目，或需要自動調整規模，請考慮擴展集。</span><span class="sxs-lookup"><span data-stu-id="7a867-221">Consider scale sets if you need to quickly scale out VMs, or need to autoscale.</span></span>

<span data-ttu-id="7a867-222">有兩種基本方法可用來設定擴展集中部署的 VM：</span><span class="sxs-lookup"><span data-stu-id="7a867-222">There are two basic ways to configure VMs deployed in a scale set:</span></span>

- <span data-ttu-id="7a867-223">佈建 VM 之後，使用擴充功能來設定它。</span><span class="sxs-lookup"><span data-stu-id="7a867-223">Use extensions to configure the VM after it is provisioned.</span></span> <span data-ttu-id="7a867-224">透過此方法，新的 VM 執行個體可能需要較長的時間來啟動不具擴充功能的 VM。</span><span class="sxs-lookup"><span data-stu-id="7a867-224">With this approach, new VM instances may take longer to start up than a VM with no extensions.</span></span>

- <span data-ttu-id="7a867-225">利用自訂的磁碟映像來部署[受控磁碟](/azure/storage/storage-managed-disks-overview)。</span><span class="sxs-lookup"><span data-stu-id="7a867-225">Deploy a [managed disk](/azure/storage/storage-managed-disks-overview) with a custom disk image.</span></span> <span data-ttu-id="7a867-226">這個選項可能會更快速地部署。</span><span class="sxs-lookup"><span data-stu-id="7a867-226">This option may be quicker to deploy.</span></span> <span data-ttu-id="7a867-227">不過，它會要求您讓映像保持最新狀態。</span><span class="sxs-lookup"><span data-stu-id="7a867-227">However, it requires you to keep the image up to date.</span></span>

<span data-ttu-id="7a867-228">如需其他考量，請參閱[擴展集的設計考量][vmss-design]。</span><span class="sxs-lookup"><span data-stu-id="7a867-228">For additional considerations, see [Design considerations for scale sets][vmss-design].</span></span>

> [!TIP]
> <span data-ttu-id="7a867-229">使用任何自動調整規模解決方案時，事前也要利用生產層級的工作負載來進行測試。</span><span class="sxs-lookup"><span data-stu-id="7a867-229">When using any autoscale solution, test it with production-level workloads well in advance.</span></span>

<span data-ttu-id="7a867-230">每個 Azure 訂用帳戶都已經有預設限制，包括每個區域的 VM 最大數目。</span><span class="sxs-lookup"><span data-stu-id="7a867-230">Each Azure subscription has default limits in place, including a maximum number of VMs per region.</span></span> <span data-ttu-id="7a867-231">您可以藉由提出支援要求來提高限制。</span><span class="sxs-lookup"><span data-stu-id="7a867-231">You can increase the limit by filing a support request.</span></span> <span data-ttu-id="7a867-232">如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束][subscription-limits]。</span><span class="sxs-lookup"><span data-stu-id="7a867-232">For more information, see [Azure subscription and service limits, quotas, and constraints][subscription-limits].</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="7a867-233">可用性考量</span><span class="sxs-lookup"><span data-stu-id="7a867-233">Availability considerations</span></span>

<span data-ttu-id="7a867-234">如果您不是使用 VM 擴展集，請將同一層中的 VM 放入可用性設定組。</span><span class="sxs-lookup"><span data-stu-id="7a867-234">If you are not using VM scale sets, put VMs in the same tier into an availability set.</span></span> <span data-ttu-id="7a867-235">在可用性設定組中至少建立兩部 VM，以支援[適用於 Azure VM 的可用性 SLA][vm-sla]。</span><span class="sxs-lookup"><span data-stu-id="7a867-235">Create at least two VMs in the availability set to support the [availability SLA for Azure VMs][vm-sla].</span></span> <span data-ttu-id="7a867-236">如需詳細資訊，請參閱[管理虛擬機器的可用性][availability-set]。</span><span class="sxs-lookup"><span data-stu-id="7a867-236">For more information, see [Manage the availability of virtual machines][availability-set].</span></span> 

<span data-ttu-id="7a867-237">負載平衡器會使用[健康情況探查][health-probes]來監視 VM 執行個體的可用性。</span><span class="sxs-lookup"><span data-stu-id="7a867-237">The load balancer uses [health probes][health-probes] to monitor the availability of VM instances.</span></span> <span data-ttu-id="7a867-238">如果探查無法在逾時期間內連線到執行個體，負載平衡器就會停止將流量傳送到該 VM。</span><span class="sxs-lookup"><span data-stu-id="7a867-238">If a probe cannot reach an instance within a timeout period, the load balancer stops sending traffic to that VM.</span></span> <span data-ttu-id="7a867-239">不過，負載平衡器將會繼續探查，而且如果 VM 再次變成可用，負載平衡器就會繼續將流量傳送到該 VM。</span><span class="sxs-lookup"><span data-stu-id="7a867-239">However, the load balancer will continue to probe, and if the VM becomes available again, the load balancer resumes sending traffic to that VM.</span></span>

<span data-ttu-id="7a867-240">以下是關於負載平衡器健康情況探查的建議：</span><span class="sxs-lookup"><span data-stu-id="7a867-240">Here are some recommendations on load balancer health probes:</span></span>

* <span data-ttu-id="7a867-241">探查可以測試 HTTP 或 TCP。</span><span class="sxs-lookup"><span data-stu-id="7a867-241">Probes can test either HTTP or TCP.</span></span> <span data-ttu-id="7a867-242">如果您的 VM 執行 HTTP 伺服器，請建立 HTTP 探查。</span><span class="sxs-lookup"><span data-stu-id="7a867-242">If your VMs run an HTTP server, create an HTTP probe.</span></span> <span data-ttu-id="7a867-243">否則，建立 TCP 探查。</span><span class="sxs-lookup"><span data-stu-id="7a867-243">Otherwise create a TCP probe.</span></span>
* <span data-ttu-id="7a867-244">針對 HTTP 探查，指定 HTTP 端點的路徑。</span><span class="sxs-lookup"><span data-stu-id="7a867-244">For an HTTP probe, specify the path to an HTTP endpoint.</span></span> <span data-ttu-id="7a867-245">探查會檢查來自此路徑的 HTTP 200 回應。</span><span class="sxs-lookup"><span data-stu-id="7a867-245">The probe checks for an HTTP 200 response from this path.</span></span> <span data-ttu-id="7a867-246">這可以是根路徑 ("/")，或是實作一些自訂邏輯來檢查應用程式健康情況的健康情況監視端點。</span><span class="sxs-lookup"><span data-stu-id="7a867-246">This can be the root path ("/"), or a health-monitoring endpoint that implements some custom logic to check the health of the application.</span></span> <span data-ttu-id="7a867-247">端點必須允許匿名的 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="7a867-247">The endpoint must allow anonymous HTTP requests.</span></span>
* <span data-ttu-id="7a867-248">探查會從[已知的 IP 位址][health-probe-ip] 168.63.129.16 傳送出來。</span><span class="sxs-lookup"><span data-stu-id="7a867-248">The probe is sent from a [known IP address][health-probe-ip], 168.63.129.16.</span></span> <span data-ttu-id="7a867-249">請確定您並未在任何防火牆原則或網路安全性群組 (NSG) 規則中封鎖往返此 IP 位址的流量。</span><span class="sxs-lookup"><span data-stu-id="7a867-249">Make sure you don't block traffic to or from this IP address in any firewall policies or network security group (NSG) rules.</span></span>
* <span data-ttu-id="7a867-250">使用[健康情況探查記錄][health-probe-log]來檢視健康情況探查的狀態。</span><span class="sxs-lookup"><span data-stu-id="7a867-250">Use [health probe logs][health-probe-log] to view the status of the health probes.</span></span> <span data-ttu-id="7a867-251">能夠針對每個負載平衡器登入 Azure 入口網站。</span><span class="sxs-lookup"><span data-stu-id="7a867-251">Enable logging in the Azure portal for each load balancer.</span></span> <span data-ttu-id="7a867-252">記錄會寫入 Azure Blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="7a867-252">Logs are written to Azure Blob storage.</span></span> <span data-ttu-id="7a867-253">記錄會顯示後端有多少個 VM 因探查回應失敗而不會接收網路流量。</span><span class="sxs-lookup"><span data-stu-id="7a867-253">The logs show how many VMs on the back end are not receiving network traffic due to failed probe responses.</span></span>

<span data-ttu-id="7a867-254">如果您需要比[適用於 VM 的 Azure SLA][vm-sla] 所提供更高的可用性，請考慮跨兩個區域複寫應用程式，同時使用 Azure 流量管理員進行容錯移轉。</span><span class="sxs-lookup"><span data-stu-id="7a867-254">If you need higher availability than the [Azure SLA for VMs][vm-sla] provides, consider replication the application across two regions, using Azure Traffic Manager for failover.</span></span> <span data-ttu-id="7a867-255">如需詳細資訊，請參閱[適用於高可用性的多重區域多層式架構 (N-tier) 應用程式][multi-dc]。</span><span class="sxs-lookup"><span data-stu-id="7a867-255">For more information, see [Multi-region N-tier application for high availability][multi-dc].</span></span>  

## <a name="security-considerations"></a><span data-ttu-id="7a867-256">安全性考量</span><span class="sxs-lookup"><span data-stu-id="7a867-256">Security considerations</span></span>

<span data-ttu-id="7a867-257">虛擬網路是 Azure 中的流量隔離界限。</span><span class="sxs-lookup"><span data-stu-id="7a867-257">Virtual networks are a traffic isolation boundary in Azure.</span></span> <span data-ttu-id="7a867-258">某一個 VNet 中的 VM 無法直接與不同 VNet 中的 VM 通訊。</span><span class="sxs-lookup"><span data-stu-id="7a867-258">VMs in one VNet cannot communicate directly with VMs in a different VNet.</span></span> <span data-ttu-id="7a867-259">除非您建立[網路安全性群組][nsg] (NSG) 來限制流量，否則，相同 VNet 中的 VM 可以彼此通訊。</span><span class="sxs-lookup"><span data-stu-id="7a867-259">VMs within the same VNet can communicate, unless you create [network security groups][nsg] (NSGs) to restrict traffic.</span></span> <span data-ttu-id="7a867-260">如需詳細資訊，請參閱 [Microsoft 雲端服務和網路安全性][network-security]。</span><span class="sxs-lookup"><span data-stu-id="7a867-260">For more information, see [Microsoft cloud services and network security][network-security].</span></span>

<span data-ttu-id="7a867-261">針對傳入的網際網路流量，負載平衡器規則會定義哪些流量可以連線到後端。</span><span class="sxs-lookup"><span data-stu-id="7a867-261">For incoming Internet traffic, the load balancer rules define which traffic can reach the back end.</span></span> <span data-ttu-id="7a867-262">不過，負載平衡器規則不支援 IP 安全清單，因此，如果您想要將特定的公用 IP 位址新增至安全清單，請將 NSG 新增至子網路。</span><span class="sxs-lookup"><span data-stu-id="7a867-262">However, load balancer rules don't support IP safe lists, so if you want to add certain public IP addresses to a safe list, add an NSG to the subnet.</span></span>

<span data-ttu-id="7a867-263">請考慮新增網路虛擬設備 (NVA)，在網際網路和 Azure 虛擬網路之間建立 DMZ。</span><span class="sxs-lookup"><span data-stu-id="7a867-263">Consider adding a network virtual appliance (NVA) to create a DMZ between the Internet and the Azure virtual network.</span></span> <span data-ttu-id="7a867-264">NVA 是虛擬設備的通稱，可以執行網路相關的工作，例如防火牆、封包檢查、稽核和自訂路由傳送。</span><span class="sxs-lookup"><span data-stu-id="7a867-264">NVA is a generic term for a virtual appliance that can perform network-related tasks, such as firewall, packet inspection, auditing, and custom routing.</span></span> <span data-ttu-id="7a867-265">如需詳細資訊，請參閱[實作 Azure 和網際網路之間的 DMZ][dmz]。</span><span class="sxs-lookup"><span data-stu-id="7a867-265">For more information, see [Implementing a DMZ between Azure and the Internet][dmz].</span></span>

<span data-ttu-id="7a867-266">將機密的待用資料加密，並使用 [Azure Key Vault][azure-key-vault] 來管理資料庫加密金鑰。</span><span class="sxs-lookup"><span data-stu-id="7a867-266">Encrypt sensitive data at rest and use [Azure Key Vault][azure-key-vault] to manage the database encryption keys.</span></span> <span data-ttu-id="7a867-267">Key Vault 可以在硬體安全模組 (HSM) 中儲存加密金鑰。</span><span class="sxs-lookup"><span data-stu-id="7a867-267">Key Vault can store encryption keys in hardware security modules (HSMs).</span></span> <span data-ttu-id="7a867-268">如需詳細資訊，請參閱[在 Azure VM 上設定 SQL Server 的 Azure Key Vault 整合][sql-keyvault]。</span><span class="sxs-lookup"><span data-stu-id="7a867-268">For more information, see [Configure Azure Key Vault Integration for SQL Server on Azure VMs][sql-keyvault].</span></span> <span data-ttu-id="7a867-269">也建議將應用程式密碼 (例如資料庫連接字串) 儲存在金鑰保存庫中。</span><span class="sxs-lookup"><span data-stu-id="7a867-269">It's also recommended to store application secrets, such as database connection strings, in Key Vault.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="7a867-270">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="7a867-270">Deploy the solution</span></span>

<span data-ttu-id="7a867-271">此參考架構的部署可在 [GitHub][github-folder] 上取得。</span><span class="sxs-lookup"><span data-stu-id="7a867-271">A deployment for this reference architecture is available on [GitHub][github-folder].</span></span> <span data-ttu-id="7a867-272">請注意，整個部署可能需要長達 2 小時的時間，包括執行指令碼以設定 AD DS、Windows Server 容錯移轉叢集和 SQL Server 可用性設定組。</span><span class="sxs-lookup"><span data-stu-id="7a867-272">Note that the entire deployment can take up to two hours, which includes running the scripts to configure AD DS, the Windows Server failover cluster, and the SQL Server availability group.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="7a867-273">先決條件</span><span class="sxs-lookup"><span data-stu-id="7a867-273">Prerequisites</span></span>

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-the-solution"></a><span data-ttu-id="7a867-274">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="7a867-274">Deploy the solution</span></span>

1. <span data-ttu-id="7a867-275">執行下列命令以建立資源群組。</span><span class="sxs-lookup"><span data-stu-id="7a867-275">Run the following command to create a resource group.</span></span>

    ```bash
    az group create --location <location> --name <resource-group-name>
    ```

2. <span data-ttu-id="7a867-276">執行下列命令為雲端見證建立儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="7a867-276">Run the following command to create a Storage account for the Cloud Witness.</span></span>

    ```bash
    az storage account create --location <location> \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --sku Standard_LRS
    ```

3. <span data-ttu-id="7a867-277">瀏覽至參考架構 GitHub 存放庫的 `virtual-machines\n-tier-windows` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="7a867-277">Navigate to the `virtual-machines\n-tier-windows` folder of the reference architectures GitHub repository.</span></span>

4. <span data-ttu-id="7a867-278">開啟 `n-tier-windows.json` 檔案。</span><span class="sxs-lookup"><span data-stu-id="7a867-278">Open the `n-tier-windows.json` file.</span></span> 

5. <span data-ttu-id="7a867-279">搜尋 "witnessStorageBlobEndPoint" 的所有執行個體，並且將預留位置文字取代為步驟 2 中儲存體帳戶的名稱。</span><span class="sxs-lookup"><span data-stu-id="7a867-279">Search for all instances of "witnessStorageBlobEndPoint" and replace the placeholder text with the name of the Storage account from step 2.</span></span>

    ```json
    "witnessStorageBlobEndPoint": "https://[replace-with-storageaccountname].blob.core.windows.net",
    ```

6. <span data-ttu-id="7a867-280">執行下列命令以列出儲存體帳戶的帳戶金鑰。</span><span class="sxs-lookup"><span data-stu-id="7a867-280">Run the following command to list the account keys for the storage account.</span></span>

    ```bash
    az storage account keys list \
      --account-name <storage-account-name> \
      --resource-group <resource-group-name>
    ```

    <span data-ttu-id="7a867-281">輸出應該看起來如下所示。</span><span class="sxs-lookup"><span data-stu-id="7a867-281">The output should look like the following.</span></span> <span data-ttu-id="7a867-282">複製 `key1` 的值。</span><span class="sxs-lookup"><span data-stu-id="7a867-282">Copy the value of `key1`.</span></span>

    ```json
    [
    {
        "keyName": "key1",
        "permissions": "Full",
        "value": "..."
    },
    {
        "keyName": "key2",
        "permissions": "Full",
        "value": "..."
    }
    ]
    ```

7. <span data-ttu-id="7a867-283">在 `n-tier-windows.json` 檔案中，搜尋 "witnessStorageAccountKey" 的所有執行個體，並且貼至帳戶金鑰。</span><span class="sxs-lookup"><span data-stu-id="7a867-283">In the `n-tier-windows.json` file, search for all instances of "witnessStorageAccountKey" and paste in the account key.</span></span>

    ```json
    "witnessStorageAccountKey": "[replace-with-storagekey]"
    ```

8. <span data-ttu-id="7a867-284">在 `n-tier-windows.json` 檔案中，搜尋 `testPassw0rd!23`、`test$!Passw0rd111` 和 `AweS0me@SQLServicePW` 的所有執行個體。</span><span class="sxs-lookup"><span data-stu-id="7a867-284">In the `n-tier-windows.json` file, search for all instances `testPassw0rd!23`, `test$!Passw0rd111`, and `AweS0me@SQLServicePW`.</span></span> <span data-ttu-id="7a867-285">將它們取代為您自己的密碼，然後儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="7a867-285">Replace them with your own passwords and save the file.</span></span>

    > [!NOTE]
    > <span data-ttu-id="7a867-286">如果您變更系統管理員使用者名稱，您也必須在 JSON 檔案中更新 `extensions` 區塊。</span><span class="sxs-lookup"><span data-stu-id="7a867-286">If you change the adminstrator user name, you must also update the `extensions` blocks in the JSON file.</span></span> 

9. <span data-ttu-id="7a867-287">執行下列命令來部署架構。</span><span class="sxs-lookup"><span data-stu-id="7a867-287">Run the following command to deploy the architecture.</span></span>

    ```bash
    azbb -s <your subscription_id> -g <resource_group_name> -l <location> -p n-tier-windows.json --deploy
    ```

<span data-ttu-id="7a867-288">如需使用 Azure 組建區塊部署此範例參考架構的詳細資訊，請瀏覽 [GitHub 存放庫][git]。</span><span class="sxs-lookup"><span data-stu-id="7a867-288">For more information on deploying this sample reference architecture using Azure Building Blocks, visit the [GitHub repository][git].</span></span>


<!-- links -->
[dmz]: ../dmz/secure-vnet-dmz.md
[multi-dc]: multi-region-sql-server.md
[n-tier]: n-tier.md
[azure-administration]: /azure/automation/automation-intro
[azure-availability-sets]: /azure/virtual-machines/virtual-machines-windows-manage-availability#configure-each-application-tier-into-separate-availability-sets
[azure-cli]: /azure/virtual-machines-command-line-tools
[azure-dns]: /azure/dns/dns-overview
[azure-key-vault]: https://azure.microsoft.com/services/key-vault
[防禦主機]: https://en.wikipedia.org/wiki/Bastion_host
[bastion host]: https://en.wikipedia.org/wiki/Bastion_host
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
[public IP address]: /azure/virtual-network/virtual-network-ip-addresses-overview-arm
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
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
[visio-download]: https://archcenter.blob.core.windows.net/cdn/vm-reference-architectures.vsdx
[0]: ./images/n-tier-sql-server.png "使用 Microsoft Azure 的多層式架構"
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

---
title: 在 Azure 中執行高可用性的 SharePoint Server 2016 伺服器陣列
description: 在 Azure 上設定高可用性 SharePoint Server 2016 伺服器陣列的作法已經過驗證。
author: njray
ms.date: 07/14/2018
ms.openlocfilehash: 04c69309e9f96e3bf7cd7faabeedd9b6d9da1ebd
ms.sourcegitcommit: 8b5fc0d0d735793b87677610b747f54301dcb014
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2018
ms.locfileid: "39334125"
---
# <a name="run-a-high-availability-sharepoint-server-2016-farm-in-azure"></a><span data-ttu-id="ca417-103">在 Azure 中執行高可用性的 SharePoint Server 2016 伺服器陣列</span><span class="sxs-lookup"><span data-stu-id="ca417-103">Run a high availability SharePoint Server 2016 farm in Azure</span></span>

<span data-ttu-id="ca417-104">此參考架構會示範一組經過驗證的作法，也就是使用 MinRole 拓樸和 SQL Server Always On 可用性群組，在 Azure 上設定高可用性的 SharePoint Server 2016 的伺服器陣列。</span><span class="sxs-lookup"><span data-stu-id="ca417-104">This reference architecture shows a set of proven practices for setting up a high availability SharePoint Server 2016 farm on Azure, using MinRole topology and SQL Server Always On availability groups.</span></span> <span data-ttu-id="ca417-105">SharePoint 伺服器陣列會部署在受保護且沒有網際網路對應端點或空間的虛擬網路。</span><span class="sxs-lookup"><span data-stu-id="ca417-105">The SharePoint farm is deployed in a secured virtual network with no Internet-facing endpoint or presence.</span></span> [<span data-ttu-id="ca417-106">**部署這個解決方案**。</span><span class="sxs-lookup"><span data-stu-id="ca417-106">**Deploy this solution**.</span></span>](#deploy-the-solution) 

![](./images/sharepoint-ha.png)

<span data-ttu-id="ca417-107">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="ca417-107">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="ca417-108">架構</span><span class="sxs-lookup"><span data-stu-id="ca417-108">Architecture</span></span>

<span data-ttu-id="ca417-109">此架構是根據[執行適用於多層式架構應用程式的 Windows 虛擬機器][windows-n-tier]建置的。</span><span class="sxs-lookup"><span data-stu-id="ca417-109">This architecture builds on the one shown in [Run Windows VMs for an N-tier application][windows-n-tier].</span></span> <span data-ttu-id="ca417-110">它會將具有高可用性的 SharePoint Server 2016 伺服器陣列部署在 Azure 虛擬網路 (VNet) 中。</span><span class="sxs-lookup"><span data-stu-id="ca417-110">It deploys a SharePoint Server 2016 farm with high availability inside an Azure virtual network (VNet).</span></span> <span data-ttu-id="ca417-111">此架構適用於測試或生產環境，以及搭配 Office 365 的 SharePoint 混合式基礎結構，或是當做災害復原案例的基礎。</span><span class="sxs-lookup"><span data-stu-id="ca417-111">This architecture is suitable for a test or production environment, a SharePoint hybrid infrastructure with Office 365, or as the basis for a disaster recovery scenario.</span></span>

<span data-ttu-id="ca417-112">此架構由下列元件組成：</span><span class="sxs-lookup"><span data-stu-id="ca417-112">The architecture consists of the following components:</span></span>

- <span data-ttu-id="ca417-113">**資源群組**。</span><span class="sxs-lookup"><span data-stu-id="ca417-113">**Resource groups**.</span></span> <span data-ttu-id="ca417-114">[資源群組][resource-group]是保存 Azure 相關資源的容器。</span><span class="sxs-lookup"><span data-stu-id="ca417-114">A [resource group][resource-group] is a container that holds related Azure resources.</span></span> <span data-ttu-id="ca417-115">一個資源群組會用於 SharePoint 伺服器，而另一個資源群組會用於虛擬網路和負載平衡器等不依附虛擬機器的基礎結構元件。</span><span class="sxs-lookup"><span data-stu-id="ca417-115">One resource group is used for the SharePoint servers, and another resource group is used for infrastructure components that are independent of VMs, such as the virtual network and load balancers.</span></span>

- <span data-ttu-id="ca417-116">**虛擬網路 (VNet)**。</span><span class="sxs-lookup"><span data-stu-id="ca417-116">**Virtual network (VNet)**.</span></span> <span data-ttu-id="ca417-117">虛擬機器會部署在具有唯一內部網路位址空間的 VNet 中。</span><span class="sxs-lookup"><span data-stu-id="ca417-117">The VMs are deployed in a VNet with a unique intranet address space.</span></span> <span data-ttu-id="ca417-118">VNet 會再細分為子網路。</span><span class="sxs-lookup"><span data-stu-id="ca417-118">The VNet is further subdivided into subnets.</span></span> 

- <span data-ttu-id="ca417-119">**虛擬機器 (VM)**。</span><span class="sxs-lookup"><span data-stu-id="ca417-119">**Virtual machines (VMs)**.</span></span> <span data-ttu-id="ca417-120">虛擬機器會部署至 VNet，而所有虛擬機器都會有指派的私人靜態 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="ca417-120">The VMs are deployed into the VNet, and private static IP addresses are assigned to all of the VMs.</span></span> <span data-ttu-id="ca417-121">靜態 IP 位址建議用於執行 SQL Server 和 SharePoint Server 2016 的虛擬機器，以避免在重新啟動後發生 IP 位址快取問題和位址變更的情形。</span><span class="sxs-lookup"><span data-stu-id="ca417-121">Static IP addresses are recommended for the VMs running SQL Server and SharePoint Server 2016, to avoid issues with IP address caching and changes of addresses after a restart.</span></span>

- <span data-ttu-id="ca417-122">**可用性集合**。</span><span class="sxs-lookup"><span data-stu-id="ca417-122">**Availability sets**.</span></span> <span data-ttu-id="ca417-123">將每個 SharePoint 角色的虛擬機器放在個別的[可用性集合][availability-set]中，然後為每個角色佈建至少兩部虛擬機器 (VM)。</span><span class="sxs-lookup"><span data-stu-id="ca417-123">Place the VMs for each SharePoint role into separate [availability sets][availability-set], and provision at least two virtual machines (VMs) for each role.</span></span> <span data-ttu-id="ca417-124">這讓虛擬機器能夠符合較高服務等級協定 (SLA) 的資格。</span><span class="sxs-lookup"><span data-stu-id="ca417-124">This makes the VMs eligible for a higher service level agreement (SLA).</span></span> 

- <span data-ttu-id="ca417-125">**內部負載平衡器**。</span><span class="sxs-lookup"><span data-stu-id="ca417-125">**Internal load balancer**.</span></span> <span data-ttu-id="ca417-126">[負載平衡器][load-balancer]會將 SharePoint 要求流量從內部部署網路散發至 SharePoint 伺服器陣列的前端 Web 伺服器。</span><span class="sxs-lookup"><span data-stu-id="ca417-126">The [load balancer][load-balancer] distributes SharePoint request traffic from the on-premises network to the front-end web servers of the SharePoint farm.</span></span> 

- <span data-ttu-id="ca417-127">**網路安全性群組 (NSG)**。</span><span class="sxs-lookup"><span data-stu-id="ca417-127">**Network security groups (NSGs)**.</span></span> <span data-ttu-id="ca417-128">系統會為包含虛擬機器的每個子網路，建立[網路安全性群組][nsg]。</span><span class="sxs-lookup"><span data-stu-id="ca417-128">For each subnet that contains virtual machines, a [network security group][nsg] is created.</span></span> <span data-ttu-id="ca417-129">使用 NSG 來限制 VNet 內的網路流量，以區隔子網路。</span><span class="sxs-lookup"><span data-stu-id="ca417-129">Use NSGs to restrict network traffic within the VNet, in order to isolate subnets.</span></span> 

- <span data-ttu-id="ca417-130">**閘道**。</span><span class="sxs-lookup"><span data-stu-id="ca417-130">**Gateway**.</span></span> <span data-ttu-id="ca417-131">閘道會提供內部部署網路與 Azure 虛擬網路之間的連線。</span><span class="sxs-lookup"><span data-stu-id="ca417-131">The gateway provides a connection between your on-premises network and the Azure virtual network.</span></span> <span data-ttu-id="ca417-132">您的連線可以使用 ExpressRoute 或站對站 VPN。</span><span class="sxs-lookup"><span data-stu-id="ca417-132">Your connection can use ExpressRoute or site-to-site VPN.</span></span> <span data-ttu-id="ca417-133">如需詳細資訊，請參閱[將內部部署網路連線至 Azure][hybrid-ra]。</span><span class="sxs-lookup"><span data-stu-id="ca417-133">For more information, see [Connect an on-premises network to Azure][hybrid-ra].</span></span>

- <span data-ttu-id="ca417-134">**Windows Server Active Directory (AD) 網域控制站**。</span><span class="sxs-lookup"><span data-stu-id="ca417-134">**Windows Server Active Directory (AD) domain controllers**.</span></span> <span data-ttu-id="ca417-135">此參考架構部署 Windows Server AD 網域控制站。</span><span class="sxs-lookup"><span data-stu-id="ca417-135">This reference architecture deploys Windows Server AD domain controllers.</span></span> <span data-ttu-id="ca417-136">這些網域控制站會在 Azure VNet 中執行，並與內部部署 Windows Server AD 樹系之間有信任關係。</span><span class="sxs-lookup"><span data-stu-id="ca417-136">These domain controllers run in the Azure VNet and have a trust relationship with the on-premises Windows Server AD forest.</span></span> <span data-ttu-id="ca417-137">SharePoint 伺服器陣列資源的用戶端 Web 要求會在 VNet 中進行驗證 ，而不是透過內部部署網路的閘道連線傳送該驗證流量。</span><span class="sxs-lookup"><span data-stu-id="ca417-137">Client web requests for SharePoint farm resources are authenticated in the VNet rather than sending that authentication traffic across the gateway connection to the on-premises network.</span></span> <span data-ttu-id="ca417-138">DNS 中會建立內部網路 A 或 CNAME 記錄，讓內部網路使用者可以將 SharePoint 伺服器陣列的名稱解析為內部負載平衡器的私用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="ca417-138">In DNS, intranet A or CNAME records are created so that intranet users can resolve the name of the SharePoint farm to the private IP address of the internal load balancer.</span></span>

  <span data-ttu-id="ca417-139">SharePoint Server 2016 也支援使用[Azure Active Directory Domain Services](/azure/active-directory-domain-services/)。</span><span class="sxs-lookup"><span data-stu-id="ca417-139">SharePoint Server 2016 also supports using [Azure Active Directory Domain Services](/azure/active-directory-domain-services/).</span></span> <span data-ttu-id="ca417-140">Azure AD Domain Services 提供受控網域服務，使您不需要部署和管理 Azure 中的網域控制站。</span><span class="sxs-lookup"><span data-stu-id="ca417-140">Azure AD Domain Services provides managed domain services, so that you don't need to deploy and manage domain controllers in Azure.</span></span>

- <span data-ttu-id="ca417-141">**SQL Server Always On 可用性群組**。</span><span class="sxs-lookup"><span data-stu-id="ca417-141">**SQL Server Always On Availability Group**.</span></span> <span data-ttu-id="ca417-142">若要取得 SQL Server 資料庫的高可用性使用，我們建議您使用 [可用性群組][sql-always-on]。</span><span class="sxs-lookup"><span data-stu-id="ca417-142">For high availability of the SQL Server database, we recommend [SQL Server Always On Availability Groups][sql-always-on].</span></span> <span data-ttu-id="ca417-143">用於 SQL Server 的虛擬機器有兩個。</span><span class="sxs-lookup"><span data-stu-id="ca417-143">Two virtual machines are used for SQL Server.</span></span> <span data-ttu-id="ca417-144">一個包含主要資料庫複本，另一個包含次要複本。</span><span class="sxs-lookup"><span data-stu-id="ca417-144">One contains the primary database replica and the other contains the secondary replica.</span></span> 

- <span data-ttu-id="ca417-145">**多數節點 VM**。</span><span class="sxs-lookup"><span data-stu-id="ca417-145">**Majority node VM**.</span></span> <span data-ttu-id="ca417-146">此虛擬機器可讓您為容錯移轉叢集建立仲裁。</span><span class="sxs-lookup"><span data-stu-id="ca417-146">This VM allows the failover cluster to establish quorum.</span></span> <span data-ttu-id="ca417-147">如需詳細資訊，請參閱[了解容錯移轉叢集中的仲裁設定][sql-quorum]。</span><span class="sxs-lookup"><span data-stu-id="ca417-147">For more information, see [Understanding Quorum Configurations in a Failover Cluster][sql-quorum].</span></span>

- <span data-ttu-id="ca417-148">**SharePoint 伺服器**。</span><span class="sxs-lookup"><span data-stu-id="ca417-148">**SharePoint servers**.</span></span> <span data-ttu-id="ca417-149">SharePoint 伺服器會執行 Web 前端、快取、應用程式和搜尋角色。</span><span class="sxs-lookup"><span data-stu-id="ca417-149">The SharePoint servers perform the web front-end, caching, application, and search roles.</span></span> 

- <span data-ttu-id="ca417-150">**Jumpbox**。</span><span class="sxs-lookup"><span data-stu-id="ca417-150">**Jumpbox**.</span></span> <span data-ttu-id="ca417-151">也稱為[防禦主機][bastion-host]。</span><span class="sxs-lookup"><span data-stu-id="ca417-151">Also called a [bastion host][bastion-host].</span></span> <span data-ttu-id="ca417-152">這是網路上系統管理員用來連線到其他 VM 的安全 VM。</span><span class="sxs-lookup"><span data-stu-id="ca417-152">This is a secure VM on the network that administrators use to connect to the other VMs.</span></span> <span data-ttu-id="ca417-153">Jumpbox 具有 NSG，只允許來自安全清單上公用 IP 位址的遠端流量。</span><span class="sxs-lookup"><span data-stu-id="ca417-153">The jumpbox has an NSG that allows remote traffic only from public IP addresses on a safe list.</span></span> <span data-ttu-id="ca417-154">NSG 應該允許遠端桌面 (RDP) 流量。</span><span class="sxs-lookup"><span data-stu-id="ca417-154">The NSG should permit remote desktop (RDP) traffic.</span></span>

## <a name="recommendations"></a><span data-ttu-id="ca417-155">建議</span><span class="sxs-lookup"><span data-stu-id="ca417-155">Recommendations</span></span>

<span data-ttu-id="ca417-156">您的需求可能和此處所述的架構不同。</span><span class="sxs-lookup"><span data-stu-id="ca417-156">Your requirements might differ from the architecture described here.</span></span> <span data-ttu-id="ca417-157">請使用以下建議作為起點。</span><span class="sxs-lookup"><span data-stu-id="ca417-157">Use these recommendations as a starting point.</span></span>

### <a name="resource-group-recommendations"></a><span data-ttu-id="ca417-158">資源群組建議</span><span class="sxs-lookup"><span data-stu-id="ca417-158">Resource group recommendations</span></span>

<span data-ttu-id="ca417-159">我們建議您根據伺服器角色來區隔資源群組，並且具有屬於基礎結構元件 (全域資源) 的個別資源群組。</span><span class="sxs-lookup"><span data-stu-id="ca417-159">We recommend separating resource groups according to the server role, and having a separate resource group for infrastructure components that are global resources.</span></span> <span data-ttu-id="ca417-160">在此架構中，SharePoint 資源會組成一個群組，而 SQL Server 和其他公用程式資產會組成另一個群組。</span><span class="sxs-lookup"><span data-stu-id="ca417-160">In this architecture, the SharePoint resources form one group, while the SQL Server and other utility assets form another.</span></span>

### <a name="virtual-network-and-subnet-recommendations"></a><span data-ttu-id="ca417-161">虛擬網路和子網路建議</span><span class="sxs-lookup"><span data-stu-id="ca417-161">Virtual network and subnet recommendations</span></span>

<span data-ttu-id="ca417-162">針對每個 SharePoint 角色使用一個子網路，另有一個屬於閘道的子網路，和另一個用於 jumpbox 的子網路。</span><span class="sxs-lookup"><span data-stu-id="ca417-162">Use one subnet for each SharePoint role, plus a subnet for the gateway and one for the jumpbox.</span></span> 

<span data-ttu-id="ca417-163">閘道子網路必須命名為 *GatewaySubnet*。</span><span class="sxs-lookup"><span data-stu-id="ca417-163">The gateway subnet must be named *GatewaySubnet*.</span></span> <span data-ttu-id="ca417-164">從虛擬網路位址空間的最後一個部分，指定閘道子網路位址空間。</span><span class="sxs-lookup"><span data-stu-id="ca417-164">Assign the gateway subnet address space from the last part of the virtual network address space.</span></span> <span data-ttu-id="ca417-165">如需詳細資訊，請參閱[使用 VPN 閘道將內部部署網路連線至 Azure][hybrid-vpn-ra]。</span><span class="sxs-lookup"><span data-stu-id="ca417-165">For more information, see [Connect an on-premises network to Azure using a VPN gateway][hybrid-vpn-ra].</span></span>

### <a name="vm-recommendations"></a><span data-ttu-id="ca417-166">VM 建議</span><span class="sxs-lookup"><span data-stu-id="ca417-166">VM recommendations</span></span>

<span data-ttu-id="ca417-167">此架構至少需要 44 個核心：</span><span class="sxs-lookup"><span data-stu-id="ca417-167">This architecture requires a minimum of 44 cores:</span></span>

- <span data-ttu-id="ca417-168">Standard_DS3_v2 上 8 個 SharePoint 伺服器 (每部伺服器 4 個核心) = 32 個核心</span><span class="sxs-lookup"><span data-stu-id="ca417-168">8 SharePoint servers on Standard_DS3_v2 (4 cores each) = 32 cores</span></span>
- <span data-ttu-id="ca417-169">Standard_DS1_v2 上 2 個 Active Directory 網域控制站 (每個控制站 1 個核心) = 2 個核心</span><span class="sxs-lookup"><span data-stu-id="ca417-169">2 Active Directory domain controllers on Standard_DS1_v2 (1 core each) = 2 cores</span></span>
- <span data-ttu-id="ca417-170">Standard_DS3_v2 上 2 個 SQL Server 虛擬機器 = 8 個核心</span><span class="sxs-lookup"><span data-stu-id="ca417-170">2 SQL Server VMs on Standard_DS3_v2 = 8 cores</span></span>
- <span data-ttu-id="ca417-171">Standard_DS1_v2 上 1 個多數節點 = 1 個核心</span><span class="sxs-lookup"><span data-stu-id="ca417-171">1 majority node on Standard_DS1_v2 = 1 core</span></span>
- <span data-ttu-id="ca417-172">Standard_DS1_v2 上 1 個管理伺服器 = 1 個核心</span><span class="sxs-lookup"><span data-stu-id="ca417-172">1 management server on Standard_DS1_v2 = 1 core</span></span>

<span data-ttu-id="ca417-173">請確定您的 Azure 訂用帳戶具有足夠的虛擬機器核心配額可用於部署，否則部署將會失敗。</span><span class="sxs-lookup"><span data-stu-id="ca417-173">Make sure your Azure subscription has enough VM core quota for the deployment, or the deployment will fail.</span></span> <span data-ttu-id="ca417-174">請參閱 [Azure 訂用帳戶和服務限制、配額與限制][quotas]。</span><span class="sxs-lookup"><span data-stu-id="ca417-174">See [Azure subscription and service limits, quotas, and constraints][quotas].</span></span> 

<span data-ttu-id="ca417-175">對於除了搜尋索引子以外的所有 SharePoint 角色，我們建議使用 [Standard_DS3_v2][vm-sizes-general] 虛擬機器大小。</span><span class="sxs-lookup"><span data-stu-id="ca417-175">For all SharePoint roles except the Search Indexer, we recommended using the [Standard_DS3_v2][vm-sizes-general] VM size.</span></span> <span data-ttu-id="ca417-176">搜尋索引子應至少使用 [Standard_DS13_v2][vm-sizes-memory]大小。</span><span class="sxs-lookup"><span data-stu-id="ca417-176">The Search Indexer should be at least the [Standard_DS13_v2][vm-sizes-memory] size.</span></span> <span data-ttu-id="ca417-177">對於測試，此參考架構的參數檔案會為搜尋索引子角色指定較小的 DS3_v2 大小。</span><span class="sxs-lookup"><span data-stu-id="ca417-177">For testing, the parameter files for this reference architecture specify the smaller DS3_v2 size for the Search Indexer role.</span></span> <span data-ttu-id="ca417-178">對於生產部署，請更新參數檔案以使用 DS13 或更大的大小。</span><span class="sxs-lookup"><span data-stu-id="ca417-178">For a production deployment, update the parameter files to use the DS13 size or larger.</span></span> <span data-ttu-id="ca417-179">如需詳細資訊，請參閱[適用於 SharePoint Server 2016 的硬體和軟體需求][sharepoint-reqs]。</span><span class="sxs-lookup"><span data-stu-id="ca417-179">For more information, see [Hardware and software requirements for SharePoint Server 2016][sharepoint-reqs].</span></span> 

<span data-ttu-id="ca417-180">對於 SQL Server 虛擬機器，我們建議至少 4 個核心和 8 GB 的 RAM。</span><span class="sxs-lookup"><span data-stu-id="ca417-180">For the SQL Server VMs, we recommend a minimum of 4 cores and 8 GB RAM.</span></span> <span data-ttu-id="ca417-181">此參考架構的參數檔案指定 DS3_v2 大小。</span><span class="sxs-lookup"><span data-stu-id="ca417-181">The parameter files for this reference architecture specify the DS3_v2 size.</span></span> <span data-ttu-id="ca417-182">對於生產部署，您可能需要指定較大的虛擬機器大小。</span><span class="sxs-lookup"><span data-stu-id="ca417-182">For a production deployment, you might need to specify a larger VM size.</span></span> <span data-ttu-id="ca417-183">如需詳細資訊，請參閱[儲存體和 SQL Server 容量規劃和設定 (SharePoint Server)](/sharepoint/administration/storage-and-sql-server-capacity-planning-and-configuration#estimate-memory-requirements)。</span><span class="sxs-lookup"><span data-stu-id="ca417-183">For more information, see [Storage and SQL Server capacity planning and configuration (SharePoint Server)](/sharepoint/administration/storage-and-sql-server-capacity-planning-and-configuration#estimate-memory-requirements).</span></span> 
 
### <a name="nsg-recommendations"></a><span data-ttu-id="ca417-184">NSG 建議</span><span class="sxs-lookup"><span data-stu-id="ca417-184">NSG recommendations</span></span>

<span data-ttu-id="ca417-185">我們建議每個包含虛擬機器的子網路都要有一個 NSG，以啟用子網路隔離。</span><span class="sxs-lookup"><span data-stu-id="ca417-185">We recommend having one NSG for each subnet that contains VMs, to enable subnet isolation.</span></span> <span data-ttu-id="ca417-186">如果您想要設定子網路隔離，請新增 NSG 規則，此規則會定義每個子網路允許或拒絕的輸入或輸出流量。</span><span class="sxs-lookup"><span data-stu-id="ca417-186">If you want to configure subnet isolation, add NSG rules that define the allowed or denied inbound or outbound traffic for each subnet.</span></span> <span data-ttu-id="ca417-187">如需詳細資訊，請參閱[使用網路安全性群組來篩選網路流量][virtual-networks-nsg]。</span><span class="sxs-lookup"><span data-stu-id="ca417-187">For more information, see [Filter network traffic with network security groups][virtual-networks-nsg].</span></span> 

<span data-ttu-id="ca417-188">請勿將 NSG 指派至閘道子網路，否則閘道將會停止運作。</span><span class="sxs-lookup"><span data-stu-id="ca417-188">Do not assign an NSG to the gateway subnet, or the gateway will stop functioning.</span></span> 

### <a name="storage-recommendations"></a><span data-ttu-id="ca417-189">儲存體建議</span><span class="sxs-lookup"><span data-stu-id="ca417-189">Storage recommendations</span></span>

<span data-ttu-id="ca417-190">伺服器陣列中的虛擬機器儲存體設定，應符合內部部署適用的最佳做法。</span><span class="sxs-lookup"><span data-stu-id="ca417-190">The storage configuration of the VMs in the farm should match the appropriate best practices used for on-premises deployments.</span></span> <span data-ttu-id="ca417-191">SharePoint 伺服器應有用於記錄的個別磁碟。</span><span class="sxs-lookup"><span data-stu-id="ca417-191">SharePoint servers should have a separate disk for logs.</span></span> <span data-ttu-id="ca417-192">裝載搜尋索引角色的 SharePoint 伺服器需要額外的磁碟空間來儲存搜尋索引。</span><span class="sxs-lookup"><span data-stu-id="ca417-192">SharePoint servers hosting search index roles require additional disk space for the search index to be stored.</span></span> <span data-ttu-id="ca417-193">SQL Server 的標準作法是將資料和記錄分開。</span><span class="sxs-lookup"><span data-stu-id="ca417-193">For SQL Server, the standard practice is to separate data and logs.</span></span> <span data-ttu-id="ca417-194">為資料庫備份儲存體新增更多磁碟，並針對 [tempdb][tempdb] 使用個別磁碟。</span><span class="sxs-lookup"><span data-stu-id="ca417-194">Add more disks for database backup storage, and use a separate disk for [tempdb][tempdb].</span></span>

<span data-ttu-id="ca417-195">為擁有最佳可靠性，我們建議您使用 [Azure 受控磁碟][managed-disks]。</span><span class="sxs-lookup"><span data-stu-id="ca417-195">For best reliability, we recommend using [Azure Managed Disks][managed-disks].</span></span> <span data-ttu-id="ca417-196">受控磁碟可確保可用性設定組內的虛擬機器磁碟是各自獨立的，以避免發生單一失敗點。</span><span class="sxs-lookup"><span data-stu-id="ca417-196">Managed disks ensure that the disks for VMs within an availability set are isolated to avoid single points of failure.</span></span> 

> [!NOTE]
> <span data-ttu-id="ca417-197">目前，此參考架構的 Resource Manager 範本並未使用受控磁碟。</span><span class="sxs-lookup"><span data-stu-id="ca417-197">Currently the Resource Manager template for this reference architecture does not use managed disks.</span></span> <span data-ttu-id="ca417-198">我們正計劃更新範本以使用受控磁碟。</span><span class="sxs-lookup"><span data-stu-id="ca417-198">We are planning to update the template to use managed disks.</span></span>

<span data-ttu-id="ca417-199">針對所有 SharePoint 和 SQL Server 虛擬機器，請使用進階受控磁碟。</span><span class="sxs-lookup"><span data-stu-id="ca417-199">Use Premium managed disks for all SharePoint and SQL Server VMs.</span></span> <span data-ttu-id="ca417-200">針對多數節點伺服器、網域控制站和管理伺服器，您可以使用標準受控磁碟。</span><span class="sxs-lookup"><span data-stu-id="ca417-200">You can use Standard managed disks for the majority node server, the domain controllers, and the management server.</span></span> 

### <a name="sharepoint-server-recommendations"></a><span data-ttu-id="ca417-201">SharePoint Server 建議</span><span class="sxs-lookup"><span data-stu-id="ca417-201">SharePoint Server recommendations</span></span>

<span data-ttu-id="ca417-202">設定 SharePoint 伺服器陣列之前，請確定您的每個服務都有一個 Windows Server Active Directory 帳戶。</span><span class="sxs-lookup"><span data-stu-id="ca417-202">Before configuring the SharePoint farm, make sure you have one Windows Server Active Directory service account per service.</span></span> <span data-ttu-id="ca417-203">針對此架構，您至少需要下列網域等級的帳戶來隔離每個角色的權限：</span><span class="sxs-lookup"><span data-stu-id="ca417-203">For this architecture, you need at a minimum the following domain-level accounts to isolate privilege per role:</span></span>

- <span data-ttu-id="ca417-204">SQL Server 服務帳戶</span><span class="sxs-lookup"><span data-stu-id="ca417-204">SQL Server Service account</span></span>
- <span data-ttu-id="ca417-205">設定使用者帳戶</span><span class="sxs-lookup"><span data-stu-id="ca417-205">Setup User account</span></span>
- <span data-ttu-id="ca417-206">伺服器陣列帳戶</span><span class="sxs-lookup"><span data-stu-id="ca417-206">Server Farm account</span></span>
- <span data-ttu-id="ca417-207">搜尋服務帳戶</span><span class="sxs-lookup"><span data-stu-id="ca417-207">Search Service account</span></span>
- <span data-ttu-id="ca417-208">內容存取帳戶</span><span class="sxs-lookup"><span data-stu-id="ca417-208">Content Access account</span></span>
- <span data-ttu-id="ca417-209">Web 應用程式集區帳戶</span><span class="sxs-lookup"><span data-stu-id="ca417-209">Web App Pool accounts</span></span>
- <span data-ttu-id="ca417-210">服務應用程式集區帳戶</span><span class="sxs-lookup"><span data-stu-id="ca417-210">Service App Pool accounts</span></span>
- <span data-ttu-id="ca417-211">快取進階使用者帳戶</span><span class="sxs-lookup"><span data-stu-id="ca417-211">Cache Super User account</span></span>
- <span data-ttu-id="ca417-212">快取進階讀者帳戶</span><span class="sxs-lookup"><span data-stu-id="ca417-212">Cache Super Reader account</span></span>

<span data-ttu-id="ca417-213">若要符合磁碟輸送量最低每秒 200 MB 的支援需求，請務必規劃搜尋架構。</span><span class="sxs-lookup"><span data-stu-id="ca417-213">To meet the support requirement for disk throughput of 200 MB per second minimum, make sure to plan the Search architecture.</span></span> <span data-ttu-id="ca417-214">請參閱[在 SharePoint Server 2013 中的規劃企業搜尋架構][sharepoint-search]。</span><span class="sxs-lookup"><span data-stu-id="ca417-214">See [Plan enterprise search architecture in SharePoint Server 2013][sharepoint-search].</span></span> <span data-ttu-id="ca417-215">也請遵循[在 SharePoint Server 2016 中進行編目的最佳做法][sharepoint-crawling]。</span><span class="sxs-lookup"><span data-stu-id="ca417-215">Also follow the guidelines in [Best practices for crawling in SharePoint Server 2016][sharepoint-crawling].</span></span>

<span data-ttu-id="ca417-216">此外，請將搜尋元件資料存放在高效能的個別儲存磁碟區或分割區。</span><span class="sxs-lookup"><span data-stu-id="ca417-216">In addition, store the search component data on a separate storage volume or partition with high performance.</span></span> <span data-ttu-id="ca417-217">若要減少負載並提高輸送量，請設定此架構所需的物件快取使用者帳戶。</span><span class="sxs-lookup"><span data-stu-id="ca417-217">To reduce load and improve throughput, configure the object cache user accounts, which are required in this architecture.</span></span> <span data-ttu-id="ca417-218">將 Windows Server 作業系統檔案、SharePoint Server 2016 程式檔和診斷記錄分散在三個一般效能的個別儲存磁碟區或分割區。</span><span class="sxs-lookup"><span data-stu-id="ca417-218">Split the Windows Server operating system files, the SharePoint Server 2016 program files, and diagnostics logs across three separate storage volumes or partitions with normal performance.</span></span> 

<span data-ttu-id="ca417-219">如需有關這些建議的詳細資訊，請參閱[在 SharePoint Server 2016 中初始部署管理與服務帳戶][sharepoint-accounts]。</span><span class="sxs-lookup"><span data-stu-id="ca417-219">For more information about these recommendations, see [Initial deployment administrative and service accounts in SharePoint Server 2016][sharepoint-accounts].</span></span>


### <a name="hybrid-workloads"></a><span data-ttu-id="ca417-220">混合式工作負載</span><span class="sxs-lookup"><span data-stu-id="ca417-220">Hybrid workloads</span></span>

<span data-ttu-id="ca417-221">此參考架構會部署可用來當作 [SharePoint 混合式環境][sharepoint-hybrid] &mdash; 的 SharePoint Server 2016 伺服器陣列，也就是將 SharePoint Server 2016 延伸為 Office 365 SharePoint Online。</span><span class="sxs-lookup"><span data-stu-id="ca417-221">This reference architecture deploys a SharePoint Server 2016 farm that can be used as a [SharePoint hybrid environment][sharepoint-hybrid] &mdash; that is, extending SharePoint Server 2016 to Office 365 SharePoint Online.</span></span> <span data-ttu-id="ca417-222">如果您有 Office Online Server，請參閱 [Azure 中的 Office Web Apps 和 Office Online Server 支援][office-web-apps]。</span><span class="sxs-lookup"><span data-stu-id="ca417-222">If you have Office Online Server, see [Office Web Apps and Office Online Server supportability in Azure][office-web-apps].</span></span>

<span data-ttu-id="ca417-223">此部署中的預設服務應用程式是專為支援混合式工作負載所設計。</span><span class="sxs-lookup"><span data-stu-id="ca417-223">The default service applications in this deployment are designed to support hybrid workloads.</span></span> <span data-ttu-id="ca417-224">所有 SharePoint Server 2016 和 Office 365 混合式工作負載，皆可在不變更 SharePoint 基礎結構的狀況下部署到此伺服器陣列，但有一個例外：雲端混合式搜尋服務應用程式不可部署到裝載現有搜尋拓撲的伺服器。</span><span class="sxs-lookup"><span data-stu-id="ca417-224">All SharePoint Server 2016 and Office 365 hybrid workloads can be deployed to this farm without changes to the SharePoint infrastructure, with one exception: The Cloud Hybrid Search Service Application must not be deployed onto servers hosting an existing search topology.</span></span> <span data-ttu-id="ca417-225">因此，必須將一個或多個以搜尋角色為基礎的虛擬機器新增至伺服器陣列，以支援此混合式案例。</span><span class="sxs-lookup"><span data-stu-id="ca417-225">Therefore, one or more search-role-based VMs must be added to the farm to support this hybrid scenario.</span></span>

### <a name="sql-server-always-on-availability-groups"></a><span data-ttu-id="ca417-226">SQL Server Always On 可用性群組</span><span class="sxs-lookup"><span data-stu-id="ca417-226">SQL Server Always On Availability Groups</span></span>

<span data-ttu-id="ca417-227">此架構會使用 SQL Server 虛擬機器，因為 SharePoint Server 2016 無法使用 Azure SQL Database。</span><span class="sxs-lookup"><span data-stu-id="ca417-227">This architecture uses SQL Server virtual machines because SharePoint Server 2016 cannot use Azure SQL Database.</span></span> <span data-ttu-id="ca417-228">若要在 SQL Server 中支援高可用性，我們建議您使用 Always On 可用性群組，此可用性群組會指定一組一起進行容錯移轉的資料庫，使其具有高可用性和復原能力。</span><span class="sxs-lookup"><span data-stu-id="ca417-228">To support high availability in SQL Server, we recommend using Always On Availability Groups, which specify a set of databases that fail over together, making them highly-available and recoverable.</span></span> <span data-ttu-id="ca417-229">在此參考架構中，資料庫會在部署期間建立，但您必須手動啟用 Always On 可用性群組，並將 SharePoint 資料庫新增至可用性群組。</span><span class="sxs-lookup"><span data-stu-id="ca417-229">In this reference architecture, the databases are created during deployment, but you must manually enable Always On Availability Groups and add the SharePoint databases to an availability group.</span></span> <span data-ttu-id="ca417-230">如需詳細資訊，請參閱[建立可用性群組並新增 SharePoint 資料庫][create-availability-group]。</span><span class="sxs-lookup"><span data-stu-id="ca417-230">For more information, see [Create the availability group and add the SharePoint databases][create-availability-group].</span></span>

<span data-ttu-id="ca417-231">我們也建議您將接聽程式 IP 位址新增到叢集，這是 SQL Server 虛擬機器內部負載平衡器的私人 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="ca417-231">We also recommend adding a listener IP address to the cluster, which is the private IP address of the internal load balancer for the SQL Server virtual machines.</span></span>

<span data-ttu-id="ca417-232">如需深入了解建議的 VM 大小和其他在 Azure 中執行的 SQL Server 效能建議，請參閱[Azure 虛擬機器中的 SQL Server 效能最佳做法][sql-performance]。</span><span class="sxs-lookup"><span data-stu-id="ca417-232">For recommended VM sizes and other performance recommendations for SQL Server running in Azure, see [Performance best practices for SQL Server in Azure Virtual Machines][sql-performance].</span></span> <span data-ttu-id="ca417-233">也請遵循 [SharePoint Server 2016 伺服器陣列中的 SQL Server 最佳作法][sql-sharepoint-best-practices]中的建議。</span><span class="sxs-lookup"><span data-stu-id="ca417-233">Also follow the recommendations in [Best practices for SQL Server in a SharePoint Server 2016 farm][sql-sharepoint-best-practices].</span></span>

<span data-ttu-id="ca417-234">我們建議您將多數節點伺服器所在的電腦和複寫協力電腦分開。</span><span class="sxs-lookup"><span data-stu-id="ca417-234">We recommend that the majority node server reside on a separate computer from the replication partners.</span></span> <span data-ttu-id="ca417-235">伺服器會在高安全性模式工作階段中啟用次要的複寫協力電腦伺服器，以辨別是否啟動自動容錯移轉。</span><span class="sxs-lookup"><span data-stu-id="ca417-235">The server enables the secondary replication partner server in a high-safety mode session to recognize whether to initiate an automatic failover.</span></span> <span data-ttu-id="ca417-236">與兩個協力電腦不同的是，多數節點伺服器的服務目標不是資料庫，而是支援自動容錯移轉。</span><span class="sxs-lookup"><span data-stu-id="ca417-236">Unlike the two partners, the majority node server doesn't serve the database but rather supports automatic failover.</span></span> 

## <a name="scalability-considerations"></a><span data-ttu-id="ca417-237">延展性考量</span><span class="sxs-lookup"><span data-stu-id="ca417-237">Scalability considerations</span></span>

<span data-ttu-id="ca417-238">若要相應增加現有伺服器，只需變更虛擬機器大小。</span><span class="sxs-lookup"><span data-stu-id="ca417-238">To scale up the existing servers, simply change the VM size.</span></span> 

<span data-ttu-id="ca417-239">透過 SharePoint Server 2016 中的 [MinRoles][minroles] 功能，您可以根據伺服器的角色擴充伺服器，也可從角色中移除伺服器。</span><span class="sxs-lookup"><span data-stu-id="ca417-239">With the [MinRoles][minroles] capability in SharePoint Server 2016, you can scale out servers based on the server's role and also remove servers from a role.</span></span> <span data-ttu-id="ca417-240">當您將伺服器新增至角色上時，您可以指定任何單一角色或合併角色的其中一個。</span><span class="sxs-lookup"><span data-stu-id="ca417-240">When you add servers to a role, you can specify any of the single roles or one of the combined roles.</span></span> <span data-ttu-id="ca417-241">不過，如果您將伺服器新增至搜尋角色，您也必須使用 PowerShell 重新設定搜尋拓撲。</span><span class="sxs-lookup"><span data-stu-id="ca417-241">If you add servers to the Search role, however, you must also reconfigure the search topology using PowerShell.</span></span> <span data-ttu-id="ca417-242">您也可以使用 MinRoles 來轉換角色。</span><span class="sxs-lookup"><span data-stu-id="ca417-242">You can also convert roles using MinRoles.</span></span> <span data-ttu-id="ca417-243">如需詳細資訊，請參閱[在 SharePoint Server 2016 中管理 MinRole 伺服器陣列][sharepoint-minrole]。</span><span class="sxs-lookup"><span data-stu-id="ca417-243">For more information, see [Managing a MinRole Server Farm in SharePoint Server 2016][sharepoint-minrole].</span></span>

<span data-ttu-id="ca417-244">請注意，SharePoint Server 2016 不支援使用虛擬機器擴展集來進行自動調整。</span><span class="sxs-lookup"><span data-stu-id="ca417-244">Note that SharePoint Server 2016 doesn't support using virtual machine scale sets for auto-scaling.</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="ca417-245">可用性考量</span><span class="sxs-lookup"><span data-stu-id="ca417-245">Availability considerations</span></span>

<span data-ttu-id="ca417-246">此參考架構支援 Azure 區域內的高可用性，因為每個角色至少有兩個虛擬機器部署在可用性設定組。</span><span class="sxs-lookup"><span data-stu-id="ca417-246">This reference architecture supports high availability within an Azure region, because each role has at least two VMs deployed in an availability set.</span></span>

<span data-ttu-id="ca417-247">若要防止受到區域性失敗的影響，請在不同 Azure 區域中建立個別的災害復原伺服器陣列。</span><span class="sxs-lookup"><span data-stu-id="ca417-247">To protect against a regional failure, create a separate disaster recovery farm in a different Azure region.</span></span> <span data-ttu-id="ca417-248">您的復原時間目標 (RTO) 和復原點目標 (RPO) 會決定安裝需求。</span><span class="sxs-lookup"><span data-stu-id="ca417-248">Your recovery time objectives (RTOs) and recovery point objectives (RPOs) will determine the setup requirements.</span></span> <span data-ttu-id="ca417-249">如需詳細資訊，請參閱[選擇適用於 SharePoint 2016 的災害復原策略][sharepoint-dr]。</span><span class="sxs-lookup"><span data-stu-id="ca417-249">For details, see [Choose a disaster recovery strategy for SharePoint 2016][sharepoint-dr].</span></span> <span data-ttu-id="ca417-250">次要區域應與主要區域是*配對的區域*。</span><span class="sxs-lookup"><span data-stu-id="ca417-250">The secondary region should be a *paired region* with the primary region.</span></span> <span data-ttu-id="ca417-251">若發生廣泛中斷事件，會優先復原所有配對中的一個區域。</span><span class="sxs-lookup"><span data-stu-id="ca417-251">In the event of a broad outage, recovery of one region is prioritized out of every pair.</span></span> <span data-ttu-id="ca417-252">如需詳細資訊，請參閱[商務持續性和災害復原 (BCDR)：Azure 配對的區域][paired-regions]。</span><span class="sxs-lookup"><span data-stu-id="ca417-252">For more information, see [Business continuity and disaster recovery (BCDR): Azure Paired Regions][paired-regions].</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="ca417-253">管理性考量</span><span class="sxs-lookup"><span data-stu-id="ca417-253">Manageability considerations</span></span>

<span data-ttu-id="ca417-254">若要操作和維護伺服器、伺服器陣列和站台，請遵循 SharePoint 作業的建議作法。</span><span class="sxs-lookup"><span data-stu-id="ca417-254">To operate and maintain servers, server farms, and sites, follow the recommended practices for SharePoint operations.</span></span> <span data-ttu-id="ca417-255">如需詳細資訊，請參閱 [SharePoint Server 2016 的作業][sharepoint-ops]。</span><span class="sxs-lookup"><span data-stu-id="ca417-255">For more information, see [Operations for SharePoint Server 2016][sharepoint-ops].</span></span>

<span data-ttu-id="ca417-256">在 SharePoint 環境中管理 SQL Server 時要考量的工作，可能會與一般管理資料庫應用程式要考量的工作不同。</span><span class="sxs-lookup"><span data-stu-id="ca417-256">The tasks to consider when managing SQL Server in a SharePoint environment may differ from the ones typically considered for a database application.</span></span> <span data-ttu-id="ca417-257">最佳作法是以每晚累計的備份，在每週完整備份所有 SQL 資料庫。</span><span class="sxs-lookup"><span data-stu-id="ca417-257">A best practice is to fully back up all SQL databases weekly with incremental nightly backups.</span></span> <span data-ttu-id="ca417-258">每隔 15 分鐘備份一次交易記錄。</span><span class="sxs-lookup"><span data-stu-id="ca417-258">Back up transaction logs every 15 minutes.</span></span> <span data-ttu-id="ca417-259">另一個做法是在資料庫上實作 SQL Server 維護工作，同時停用內建的 SharePoint 維護工作。</span><span class="sxs-lookup"><span data-stu-id="ca417-259">Another practice is to implement SQL Server maintenance tasks on the databases while disabling the built-in SharePoint ones.</span></span> <span data-ttu-id="ca417-260">如需詳細資訊，請參閱[儲存體和 SQL Server 容量規劃和設定][sql-server-capacity-planning]。</span><span class="sxs-lookup"><span data-stu-id="ca417-260">For more information, see [Storage and SQL Server capacity planning and configuration][sql-server-capacity-planning].</span></span> 

## <a name="security-considerations"></a><span data-ttu-id="ca417-261">安全性考量</span><span class="sxs-lookup"><span data-stu-id="ca417-261">Security considerations</span></span>

<span data-ttu-id="ca417-262">用來執行 SharePoint Server 2016 的網域層級服務帳戶，需要 Windows Server AD 網域控制站來進行網域加入及驗證處理程序。</span><span class="sxs-lookup"><span data-stu-id="ca417-262">The domain-level service accounts used to run SharePoint Server 2016 require Windows Server AD domain controllers for domain-join and authentication processes.</span></span> <span data-ttu-id="ca417-263">Azure Active Directory Domain Services 無法用於此用途。</span><span class="sxs-lookup"><span data-stu-id="ca417-263">Azure Active Directory Domain Services can't be used for this purpose.</span></span> <span data-ttu-id="ca417-264">若要擴充已經在內部網路中就緒的 Windows Server AD 身分識別基礎結構，此架構會使用現有內部部署 Windows Server AD 樹系的兩個 Windows Server AD 複本網域控制站。</span><span class="sxs-lookup"><span data-stu-id="ca417-264">To extend the Windows Server AD identity infrastructure already in place in the intranet, this architecture uses two Windows Server AD replica domain controllers of an existing on-premises Windows Server AD forest.</span></span>

<span data-ttu-id="ca417-265">此外，規劃安全性強化絕對是個明智的選擇。</span><span class="sxs-lookup"><span data-stu-id="ca417-265">In addition, it's always wise to plan for security hardening.</span></span> <span data-ttu-id="ca417-266">其他建議如下：</span><span class="sxs-lookup"><span data-stu-id="ca417-266">Other recommendations include:</span></span>

- <span data-ttu-id="ca417-267">將規則新增至 NSG 以隔離子網路和角色。</span><span class="sxs-lookup"><span data-stu-id="ca417-267">Add rules to NSGs to isolate subnets and roles.</span></span>
- <span data-ttu-id="ca417-268">不要將公用 IP 位址指派給虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="ca417-268">Don't assign public IP addresses to VMs.</span></span>
- <span data-ttu-id="ca417-269">針對入侵偵測和乘載分析，請考慮在前端 Web 伺服器之前使用網路虛擬設備，而不是使用內部 Azure 負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="ca417-269">For intrusion detection and analysis of payloads, consider using a network virtual appliance in front of the front-end web servers instead of an internal Azure load balancer.</span></span>
- <span data-ttu-id="ca417-270">您可以選擇對伺服器之間的純文字傳輸加密使用 IPsec 原則。</span><span class="sxs-lookup"><span data-stu-id="ca417-270">As an option, use IPsec policies for encryption of cleartext traffic between servers.</span></span> <span data-ttu-id="ca417-271">如果您也進行了子網路隔離，請將您的網路安全性群組規則更新為允許 IPsec 流量。</span><span class="sxs-lookup"><span data-stu-id="ca417-271">If you are also doing subnet isolation, update your network security group rules to allow IPsec traffic.</span></span>
- <span data-ttu-id="ca417-272">安裝適用於虛擬機器的反惡意程式碼代理程式。</span><span class="sxs-lookup"><span data-stu-id="ca417-272">Install anti-malware agents for the VMs.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="ca417-273">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="ca417-273">Deploy the solution</span></span>

<span data-ttu-id="ca417-274">此參考架構的部署可在 [GitHub][github] 上取得。</span><span class="sxs-lookup"><span data-stu-id="ca417-274">A deployment for this reference architecture is available on [GitHub][github].</span></span> <span data-ttu-id="ca417-275">整個部署可能需要數小時才能完成。</span><span class="sxs-lookup"><span data-stu-id="ca417-275">The entire deployment can take several hours to complete.</span></span>

<span data-ttu-id="ca417-276">此部署會在您的訂用帳戶中建立下列資源群組︰</span><span class="sxs-lookup"><span data-stu-id="ca417-276">The deployment creates the following resource groups in your subscription:</span></span>

- <span data-ttu-id="ca417-277">ra-onprem-sp2016-rg</span><span class="sxs-lookup"><span data-stu-id="ca417-277">ra-onprem-sp2016-rg</span></span>
- <span data-ttu-id="ca417-278">ra-sp2016-network-rg</span><span class="sxs-lookup"><span data-stu-id="ca417-278">ra-sp2016-network-rg</span></span>

<span data-ttu-id="ca417-279">範本參數檔案會參照這些名稱，因此，如果您變更這些名稱，請據以更新參數檔案。</span><span class="sxs-lookup"><span data-stu-id="ca417-279">The template parameter files refer to these names, so if you change them, update the parameter files to match.</span></span> 

<span data-ttu-id="ca417-280">參數檔案會在不同位置中包含硬式編碼的密碼。</span><span class="sxs-lookup"><span data-stu-id="ca417-280">The parameter files include a hard-coded password in various places.</span></span> <span data-ttu-id="ca417-281">在部署之前，請變更這些值。</span><span class="sxs-lookup"><span data-stu-id="ca417-281">Change these values before you deploy.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="ca417-282">必要條件</span><span class="sxs-lookup"><span data-stu-id="ca417-282">Prerequisites</span></span>

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-the-solution"></a><span data-ttu-id="ca417-283">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="ca417-283">Deploy the solution</span></span> 

1. <span data-ttu-id="ca417-284">執行下列命令以部署模擬的內部部署網路。</span><span class="sxs-lookup"><span data-stu-id="ca417-284">Run the following command to deploy a simulated on-premises network.</span></span>

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p onprem.json --deploy
    ```

2. <span data-ttu-id="ca417-285">執行下列命令來部署 Azure VNet 與 VPN 閘道。</span><span class="sxs-lookup"><span data-stu-id="ca417-285">Run the following command to deploy the Azure VNet and the VPN gateway.</span></span>

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p connections.json --deploy
    ```

3. <span data-ttu-id="ca417-286">執行下列命令以部署 jumpbox、AD 網域控制站與 SQL Server 虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="ca417-286">Run the following command to deploy the jumpbox, AD domain controllers, and SQL Server VMs.</span></span>

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p azure1.json --deploy
    ```

4. <span data-ttu-id="ca417-287">執行下列命令以建立容錯移轉叢集和可用性群組。</span><span class="sxs-lookup"><span data-stu-id="ca417-287">Run the following command to create the failover cluster and the availability group.</span></span> 

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p azure2-cluster.json --deploy

5. Run the following command to deploy the remaining VMs.

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p azure3.json --deploy
    ```

<span data-ttu-id="ca417-288">此時，請確認您可以針對 SQL Server Always On 可用性群組，建立網路端點到負載平衡器的 TCP 連線。</span><span class="sxs-lookup"><span data-stu-id="ca417-288">At this point, verify that you can make a TCP conection from the web front end to the load balancer for the SQL Server Always On availability group.</span></span> <span data-ttu-id="ca417-289">若要進行，請執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="ca417-289">To do so, perform the following steps:</span></span>

1. <span data-ttu-id="ca417-290">使用 Azure 入口網站尋找 `ra-sp2016-network-rg` 資源群組中名為 `ra-sp-jb-vm1` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="ca417-290">Use the Azure portal to find the VM named `ra-sp-jb-vm1` in the `ra-sp2016-network-rg` resource group.</span></span> <span data-ttu-id="ca417-291">這就是 jumpbox VM。</span><span class="sxs-lookup"><span data-stu-id="ca417-291">This is the jumpbox VM.</span></span>

2. <span data-ttu-id="ca417-292">按一下 `Connect` 以開啟 VM 的遠端桌面工作階段。</span><span class="sxs-lookup"><span data-stu-id="ca417-292">Click `Connect` to open a remote desktop session to the VM.</span></span> <span data-ttu-id="ca417-293">請使用您在 `azure1.json` 參數檔案中指定的密碼。</span><span class="sxs-lookup"><span data-stu-id="ca417-293">Use the password that you specified in the `azure1.json` parameter file.</span></span>

3. <span data-ttu-id="ca417-294">從遠端桌面工作階段，登入 10.0.5.4。</span><span class="sxs-lookup"><span data-stu-id="ca417-294">From the Remote Desktop session, log into 10.0.5.4.</span></span> <span data-ttu-id="ca417-295">這是名為 `ra-sp-app-vm1` 的 VM IP 位址。</span><span class="sxs-lookup"><span data-stu-id="ca417-295">This is the IP address of the VM named `ra-sp-app-vm1`.</span></span>

4. <span data-ttu-id="ca417-296">在 VM 中開啟 PowerShell 主控台，並使用 `Test-NetConnection` Cmdlet 確認您可以連線至負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="ca417-296">Open a PowerShell console in the VM, and use the `Test-NetConnection` cmdlet to verify that you can connect to the load balancer.</span></span>

    ```powershell
    Test-NetConnection 10.0.3.100 -Port 1433
    ```

<span data-ttu-id="ca417-297">輸出應如下所示：</span><span class="sxs-lookup"><span data-stu-id="ca417-297">The output should look similar to the following:</span></span>

```powershell
ComputerName     : 10.0.3.100
RemoteAddress    : 10.0.3.100
RemotePort       : 1433
InterfaceAlias   : Ethernet 3
SourceAddress    : 10.0.0.132
TcpTestSucceeded : True
```

<span data-ttu-id="ca417-298">如果失敗，使用 Azure 入口網站重新啟動名為 `ra-sp-sql-vm2` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="ca417-298">If it fails, use the Azure Portal to restart the VM named `ra-sp-sql-vm2`.</span></span> <span data-ttu-id="ca417-299">VM 重新啟動之後，再次執行 `Test-NetConnection` 命令。</span><span class="sxs-lookup"><span data-stu-id="ca417-299">After the VM restarts, run the `Test-NetConnection` command again.</span></span> <span data-ttu-id="ca417-300">VM 重新啟動之後，您可能需要等候大約一分鐘，才會連線成功。</span><span class="sxs-lookup"><span data-stu-id="ca417-300">You may need to wait about a minute after the VM restarts for the connection to succeed.</span></span> 

<span data-ttu-id="ca417-301">現在，如下所示完成部署。</span><span class="sxs-lookup"><span data-stu-id="ca417-301">Now complete the deployment as follows.</span></span>

1. <span data-ttu-id="ca417-302">執行下列命令，以部署 SharePoint 伺服器陣列的主要節點。</span><span class="sxs-lookup"><span data-stu-id="ca417-302">Run the following command to deploy the SharePoint farm primary node.</span></span>

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p azure4-sharepoint-server.json --deploy
    ```

2. <span data-ttu-id="ca417-303">執行下列命令，以部署 SharePoint 快取、搜尋和網路。</span><span class="sxs-lookup"><span data-stu-id="ca417-303">Run the following command to deploy the SharePoint cache, search, and web.</span></span>

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p azure5-sharepoint-farm.json --deploy
    ```

3. <span data-ttu-id="ca417-304">執行下列命令，以建立 NSG 規則。</span><span class="sxs-lookup"><span data-stu-id="ca417-304">Run the following command to create the NSG rules.</span></span>

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p azure6-security.json --deploy
    ```

### <a name="validate-the-deployment"></a><span data-ttu-id="ca417-305">驗證部署</span><span class="sxs-lookup"><span data-stu-id="ca417-305">Validate the deployment</span></span>

1. <span data-ttu-id="ca417-306">在 [Azure 入口網站][azure-portal]中，瀏覽至 `ra-onprem-sp2016-rg` 資源群組。</span><span class="sxs-lookup"><span data-stu-id="ca417-306">In the [Azure portal][azure-portal], navigate to the `ra-onprem-sp2016-rg` resource group.</span></span>

2. <span data-ttu-id="ca417-307">在資源清單中，選取名為 `ra-onpr-u-vm1` 的虛擬機器資源。</span><span class="sxs-lookup"><span data-stu-id="ca417-307">In the list of resources, select the VM resource named `ra-onpr-u-vm1`.</span></span> 

3. <span data-ttu-id="ca417-308">連線至虛擬機器，如[連線至虛擬機器][connect-to-vm]中所述。</span><span class="sxs-lookup"><span data-stu-id="ca417-308">Connect to the VM, as described in [Connect to virtual machine][connect-to-vm].</span></span> <span data-ttu-id="ca417-309">使用者名稱為 `\onpremuser`。</span><span class="sxs-lookup"><span data-stu-id="ca417-309">The user name is `\onpremuser`.</span></span>

5.  <span data-ttu-id="ca417-310">與虛擬機器建立遠端連線後，在虛擬機器中開啟瀏覽器並瀏覽至 `http://portal.contoso.local`。</span><span class="sxs-lookup"><span data-stu-id="ca417-310">When the remote connection to the VM is established, open a browser in the VM and navigate to `http://portal.contoso.local`.</span></span>

6.  <span data-ttu-id="ca417-311">在 [Windows 安全性] 方塊中，使用 `contoso.local\testuser` 作為使用者名稱來登入 SharePoint 入口網站。</span><span class="sxs-lookup"><span data-stu-id="ca417-311">In the **Windows Security** box, log on to the SharePoint portal using `contoso.local\testuser` for the user name.</span></span>

<span data-ttu-id="ca417-312">此登入可讓您從內部部署網路使用的 Fabrikam.com 網域通往 SharePoint 入口網站使用的 contoso.local 網域。</span><span class="sxs-lookup"><span data-stu-id="ca417-312">This logon tunnels from the Fabrikam.com domain used by the on-premises network to the contoso.local domain used by the SharePoint portal.</span></span> <span data-ttu-id="ca417-313">當 SharePoint 網站開啟時，您會看到根示範網站。</span><span class="sxs-lookup"><span data-stu-id="ca417-313">When the SharePoint site opens, you'll see the root demo site.</span></span>

<span data-ttu-id="ca417-314">**_此參考架構的參與者_** &mdash;  Joe Davies、Bob Fox、Neil Hodgkinson、Paul Stork</span><span class="sxs-lookup"><span data-stu-id="ca417-314">**_Contributors to this reference architecture_** &mdash;  Joe Davies, Bob Fox, Neil Hodgkinson, Paul Stork</span></span>

<!-- links -->

[availability-set]: /azure/virtual-machines/windows/manage-availability
[azure-portal]: https://portal.azure.com
[azure-ps]: /powershell/azure/overview
[azure-pricing]: https://azure.microsoft.com/pricing/calculator/
[bastion-host]: https://en.wikipedia.org/wiki/Bastion_host
[create-availability-group]: https://technet.microsoft.com/library/mt793548(v=office.16).aspx
[connect-to-vm]: /azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine
[github]: https://github.com/mspnp/reference-architectures
[hybrid-ra]: ../hybrid-networking/index.md
[hybrid-vpn-ra]: ../hybrid-networking/vpn.md
[load-balancer]: /azure/load-balancer/load-balancer-internal-overview
[managed-disks]: /azure/storage/storage-managed-disks-overview
[minroles]: https://technet.microsoft.com/library/mt346114(v=office.16).aspx
[nsg]: /azure/virtual-network/virtual-networks-nsg
[office-web-apps]: https://support.microsoft.com/help/3199955/office-web-apps-and-office-online-server-supportability-in-azure
[paired-regions]: /azure/best-practices-availability-paired-regions
[readme]: https://github.com/mspnp/reference-architectures/tree/master/sharepoint/sharepoint-2016
[resource-group]: /azure/azure-resource-manager/resource-group-overview
[quotas]: /azure/azure-subscription-service-limits
[sharepoint-accounts]: https://technet.microsoft.com/library/ee662513(v=office.16).aspx
[sharepoint-crawling]: https://technet.microsoft.com/library/dn535606(v=office.16).aspx
[sharepoint-dr]: https://technet.microsoft.com/library/ff628971(v=office.16).aspx
[sharepoint-hybrid]: https://aka.ms/sphybrid
[sharepoint-minrole]: https://technet.microsoft.com/library/mt743705(v=office.16).aspx
[sharepoint-ops]: https://technet.microsoft.com/library/cc262289(v=office.16).aspx
[sharepoint-reqs]: https://technet.microsoft.com/library/cc262485(v=office.16).aspx
[sharepoint-search]: https://technet.microsoft.com/library/dn342836.aspx
[sql-always-on]: /sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server
[sql-performance]: /virtual-machines/windows/sql/virtual-machines-windows-sql-performance
[sql-server-capacity-planning]: https://technet.microsoft.com/library/cc298801(v=office.16).aspx
[sql-quorum]: https://technet.microsoft.com/library/cc731739(v=ws.11).aspx
[sql-sharepoint-best-practices]: https://technet.microsoft.com/library/hh292622(v=office.16).aspx
[tempdb]: /sql/relational-databases/databases/tempdb-database
[virtual-networks-nsg]: /azure/virtual-network/virtual-networks-nsg
[visio-download]: https://archcenter.blob.core.windows.net/cdn/Sharepoint-2016.vsdx
[vm-sizes-general]: /azure/virtual-machines/windows/sizes-general
[vm-sizes-memory]: /azure/virtual-machines/windows/sizes-memory
[windows-n-tier]: ../virtual-machines-windows/n-tier.md

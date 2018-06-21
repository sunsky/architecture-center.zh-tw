---
title: 在 Azure 中實作安全的混合式網路架構
description: 如何在 Azure 中實作安全的混合式網路架構。
author: telmosampaio
ms.date: 11/23/2016
pnp.series.title: Network DMZ
pnp.series.prev: ./index
pnp.series.next: secure-vnet-dmz
cardTitle: DMZ between Azure and on-premises
ms.openlocfilehash: 81dea2e4439d5a01ebb88ab86dc0a59609bb7bc3
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
ms.locfileid: "30849649"
---
# <a name="dmz-between-azure-and-your-on-premises-datacenter"></a><span data-ttu-id="f5f96-103">Azure 與內部部署資料中心之間的 DMZ</span><span class="sxs-lookup"><span data-stu-id="f5f96-103">DMZ between Azure and your on-premises datacenter</span></span>

<span data-ttu-id="f5f96-104">此參考架構顯示的安全混合式網路，可將內部部署網路擴充至 Azure。</span><span class="sxs-lookup"><span data-stu-id="f5f96-104">This reference architecture shows a secure hybrid network that extends an on-premises network to Azure.</span></span> <span data-ttu-id="f5f96-105">此架構會在內部部署網路與 Azure 虛擬網路 (VNet) 之間實作 DMZ (也稱為「周邊網路」)。</span><span class="sxs-lookup"><span data-stu-id="f5f96-105">The architecture implements a DMZ, also called a *perimeter network*, between the on-premises network and an Azure virtual network (VNet).</span></span> <span data-ttu-id="f5f96-106">DMZ 包含實作安全性功能 (例如防火牆和封包檢查) 的網路虛擬裝置 (NVA)。</span><span class="sxs-lookup"><span data-stu-id="f5f96-106">The DMZ includes network virtual appliances (NVAs) that implement security functionality such as firewalls and packet inspection.</span></span> <span data-ttu-id="f5f96-107">VNet 的所有傳出流量會強制通過內部部署網路後再傳送至網際網路，以便進行稽核。</span><span class="sxs-lookup"><span data-stu-id="f5f96-107">All outgoing traffic from the VNet is force-tunneled to the Internet through the on-premises network, so that it can be audited.</span></span>

<span data-ttu-id="f5f96-108">[![0]][0]</span><span class="sxs-lookup"><span data-stu-id="f5f96-108">[![0]][0]</span></span> 

<span data-ttu-id="f5f96-109">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="f5f96-109">*Download a [Visio file][visio-download] of this architecture.*</span></span>

<span data-ttu-id="f5f96-110">此架構需要連線到內部部署資料中心，可使用 [VPN 閘道][ra-vpn]或 [ExpressRoute][ra-expressroute]連線。</span><span class="sxs-lookup"><span data-stu-id="f5f96-110">This architecture requires a connection to your on-premises datacenter, using either a [VPN gateway][ra-vpn] or an [ExpressRoute][ra-expressroute] connection.</span></span> <span data-ttu-id="f5f96-111">此架構的典型使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="f5f96-111">Typical uses for this architecture include:</span></span>

* <span data-ttu-id="f5f96-112">部分工作負載在內部部署中執行、部分在 Azure 中執行的混合式應用程式。</span><span class="sxs-lookup"><span data-stu-id="f5f96-112">Hybrid applications where workloads run partly on-premises and partly in Azure.</span></span>
* <span data-ttu-id="f5f96-113">針對從內部部署資料中心進入 Azure VNet 的流量，需要進行更精確控管的基礎結構。</span><span class="sxs-lookup"><span data-stu-id="f5f96-113">Infrastructure that requires granular control over traffic entering an Azure VNet from an on-premises datacenter.</span></span>
* <span data-ttu-id="f5f96-114">必須稽核傳出流量的應用程式。</span><span class="sxs-lookup"><span data-stu-id="f5f96-114">Applications that must audit outgoing traffic.</span></span> <span data-ttu-id="f5f96-115">這通常是許多商業系統的管理需求，並且有助於防止個人資訊遭到公開洩漏。</span><span class="sxs-lookup"><span data-stu-id="f5f96-115">This is often a regulatory requirement of many commercial systems and can help to prevent public disclosure of private information.</span></span>

## <a name="architecture"></a><span data-ttu-id="f5f96-116">架構</span><span class="sxs-lookup"><span data-stu-id="f5f96-116">Architecture</span></span>

<span data-ttu-id="f5f96-117">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="f5f96-117">The architecture consists of the following components.</span></span>

* <span data-ttu-id="f5f96-118">**內部部署網路**。</span><span class="sxs-lookup"><span data-stu-id="f5f96-118">**On-premises network**.</span></span> <span data-ttu-id="f5f96-119">在組織內實作的私人區域網路。</span><span class="sxs-lookup"><span data-stu-id="f5f96-119">A  private local-area network implemented in an organization.</span></span>
* <span data-ttu-id="f5f96-120">**Azure 虛擬網路 (VNet)**。</span><span class="sxs-lookup"><span data-stu-id="f5f96-120">**Azure virtual network (VNet)**.</span></span> <span data-ttu-id="f5f96-121">VNet 會裝載在 Azure 中執行的應用程式和其他資源。</span><span class="sxs-lookup"><span data-stu-id="f5f96-121">The VNet hosts the application and other resources running in Azure.</span></span>
* <span data-ttu-id="f5f96-122">**閘道**。</span><span class="sxs-lookup"><span data-stu-id="f5f96-122">**Gateway**.</span></span> <span data-ttu-id="f5f96-123">閘道會提供內部部署網路與 VNet 中路由器之間的連線。</span><span class="sxs-lookup"><span data-stu-id="f5f96-123">The gateway provides connectivity between the routers in the on-premises network and the VNet.</span></span>
* <span data-ttu-id="f5f96-124">**網路虛擬設備 (NVA)**。</span><span class="sxs-lookup"><span data-stu-id="f5f96-124">**Network virtual appliance (NVA)**.</span></span> <span data-ttu-id="f5f96-125">NVA 是統稱，用來描述執行以下這類工作的虛擬機器：作為防火牆來允許或拒絕存取、最佳化廣域網路 (WAN) 作業 (包括網路壓縮)、自訂路由或其他網路功能等。</span><span class="sxs-lookup"><span data-stu-id="f5f96-125">NVA is a generic term that describes a VM performing tasks such as allowing or denying access as a firewall, optimizing wide area network (WAN) operations (including network compression), custom routing, or other network functionality.</span></span>
* <span data-ttu-id="f5f96-126">**Web 層、商務層和資料層子網路**。</span><span class="sxs-lookup"><span data-stu-id="f5f96-126">**Web tier, business tier, and data tier subnets**.</span></span> <span data-ttu-id="f5f96-127">裝載虛擬機器和服務的子網路，其會實作在雲端中執行的 3 層應用程式範例。</span><span class="sxs-lookup"><span data-stu-id="f5f96-127">Subnets hosting the VMs and services that implement an example 3-tier application running in the cloud.</span></span> <span data-ttu-id="f5f96-128">如需詳細資訊，請參閱[在 Azure 上執行適用於多層式架構的 Windows 虛擬機器][ra-n-tier]。</span><span class="sxs-lookup"><span data-stu-id="f5f96-128">See [Running Windows VMs for an N-tier architecture on Azure][ra-n-tier] for more information.</span></span>
* <span data-ttu-id="f5f96-129">**使用者定義路由 (UDR)**。</span><span class="sxs-lookup"><span data-stu-id="f5f96-129">**User defined routes (UDR)**.</span></span> <span data-ttu-id="f5f96-130">[使用者定義路由][udr-overview]會定義 Azure VNet 內 IP 流量的流程。</span><span class="sxs-lookup"><span data-stu-id="f5f96-130">[User defined routes][udr-overview] define the flow of IP traffic within Azure VNets.</span></span>

    > [!NOTE]
    > <span data-ttu-id="f5f96-131">根據您 VPN 連線的需求，您可以設定邊界閘道協定 (BGP) 路由，而不是使用 UDR 來實作轉送規則，透過內部部署網路將流量導回。</span><span class="sxs-lookup"><span data-stu-id="f5f96-131">Depending on the requirements of your VPN connection, you can configure Border Gateway Protocol (BGP) routes instead of using UDRs to implement the forwarding rules that direct traffic back through the on-premises network.</span></span>
    > 
    > 

* <span data-ttu-id="f5f96-132">**管理子網路。**</span><span class="sxs-lookup"><span data-stu-id="f5f96-132">**Management subnet.**</span></span> <span data-ttu-id="f5f96-133">此子網路包含的虛擬機器會針對 VNet 中執行的元件實作管理和監視功能。</span><span class="sxs-lookup"><span data-stu-id="f5f96-133">This subnet contains VMs that implement management and monitoring capabilities for the components running in the VNet.</span></span>

## <a name="recommendations"></a><span data-ttu-id="f5f96-134">建議</span><span class="sxs-lookup"><span data-stu-id="f5f96-134">Recommendations</span></span>

<span data-ttu-id="f5f96-135">下列建議適用於大部分的案例。</span><span class="sxs-lookup"><span data-stu-id="f5f96-135">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="f5f96-136">除非您有特定的需求會覆寫它們，否則請遵循下列建議。</span><span class="sxs-lookup"><span data-stu-id="f5f96-136">Follow these recommendations unless you have a specific requirement that overrides them.</span></span> 

### <a name="access-control-recommendations"></a><span data-ttu-id="f5f96-137">存取控制建議</span><span class="sxs-lookup"><span data-stu-id="f5f96-137">Access control recommendations</span></span>

<span data-ttu-id="f5f96-138">使用[角色型存取控制] [rbac] (RBAC) 來管理您應用程式中的資源。</span><span class="sxs-lookup"><span data-stu-id="f5f96-138">Use [Role-Based Access Control ][rbac] (RBAC) to manage the resources in your application.</span></span> <span data-ttu-id="f5f96-139">請考慮建立下列[自訂角色][rbac-custom-roles]：</span><span class="sxs-lookup"><span data-stu-id="f5f96-139">Consider creating the following [custom roles][rbac-custom-roles]:</span></span>

- <span data-ttu-id="f5f96-140">DevOps 角色，有權管理應用程式的基礎結構、部署應用程式元件，以及監視和重新啟動虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="f5f96-140">A DevOps role with permissions to administer the infrastructure for the application, deploy the application components, and monitor and restart VMs.</span></span>  

- <span data-ttu-id="f5f96-141">中央 IT 管理員角色，管理和監視網路資源。</span><span class="sxs-lookup"><span data-stu-id="f5f96-141">A centralized IT administrator role to manage and monitor network resources.</span></span>

- <span data-ttu-id="f5f96-142">安全性 IT 管理員角色，管理安全的網路資源，例如 NVA。</span><span class="sxs-lookup"><span data-stu-id="f5f96-142">A security IT administrator role to manage secure network resources such as the NVAs.</span></span> 

<span data-ttu-id="f5f96-143">DevOps 與 IT 管理員角色應不可有 NVA 資源的存取權。</span><span class="sxs-lookup"><span data-stu-id="f5f96-143">The DevOps and IT administrator roles should not have access to the NVA resources.</span></span> <span data-ttu-id="f5f96-144">應僅限安全性 IT 管理員角色可存取。</span><span class="sxs-lookup"><span data-stu-id="f5f96-144">This should be restricted to the security IT administrator role.</span></span>

### <a name="resource-group-recommendations"></a><span data-ttu-id="f5f96-145">資源群組建議</span><span class="sxs-lookup"><span data-stu-id="f5f96-145">Resource group recommendations</span></span>

<span data-ttu-id="f5f96-146">將 Azure 資源 (例如 VM、VNet 及負載平衡器) 組成資源群組，您就可以輕鬆地進行管理。</span><span class="sxs-lookup"><span data-stu-id="f5f96-146">Azure resources such as VMs, VNets, and load balancers can be easily managed by grouping them together into resource groups.</span></span> <span data-ttu-id="f5f96-147">將 RBAC 角色指派給每個資源群組，以限制存取。</span><span class="sxs-lookup"><span data-stu-id="f5f96-147">Assign RBAC roles to each resource group to restrict access.</span></span>

<span data-ttu-id="f5f96-148">我們建議您建立下列資源群組：</span><span class="sxs-lookup"><span data-stu-id="f5f96-148">We recommend creating the following resource groups:</span></span>

* <span data-ttu-id="f5f96-149">資源群組中包含 VNet (但不包括虛擬機器)、NSG 和用以連線至內部部署網路的閘道資源。</span><span class="sxs-lookup"><span data-stu-id="f5f96-149">A resource group containing the VNet (excluding the VMs), NSGs, and the gateway resources for connecting to the on-premises network.</span></span> <span data-ttu-id="f5f96-150">將中央 IT 管理員角色指派給此資源群組。</span><span class="sxs-lookup"><span data-stu-id="f5f96-150">Assign the centralized IT administrator role to this resource group.</span></span>
* <span data-ttu-id="f5f96-151">資源群組中包含 NVA 的虛擬機器 (包括負載平衡器)、jumpbox 和其他管理虛擬機器，以及強制流量通過 NVA 的閘道子網路 UDR。</span><span class="sxs-lookup"><span data-stu-id="f5f96-151">A resource group containing the VMs for the NVAs (including the load balancer), the jumpbox and other management VMs, and the UDR for the gateway subnet that forces all traffic through the NVAs.</span></span> <span data-ttu-id="f5f96-152">將安全性 IT 管理員角色指派給此資源群組。</span><span class="sxs-lookup"><span data-stu-id="f5f96-152">Assign the security IT administrator role to this resource group.</span></span>
* <span data-ttu-id="f5f96-153">針對每個應用程式層分隔資源群組，其中包含負載平衡器和虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="f5f96-153">Separate resource groups for each application tier that contain the load balancer and VMs.</span></span> <span data-ttu-id="f5f96-154">請注意，此資源群組不應包含每一層的子網路。</span><span class="sxs-lookup"><span data-stu-id="f5f96-154">Note that this resource group shouldn't include the subnets for each tier.</span></span> <span data-ttu-id="f5f96-155">將 DevOps 角色指派給此資源群組。</span><span class="sxs-lookup"><span data-stu-id="f5f96-155">Assign the DevOps role to this resource group.</span></span>

### <a name="virtual-network-gateway-recommendations"></a><span data-ttu-id="f5f96-156">虛擬網路閘道建議</span><span class="sxs-lookup"><span data-stu-id="f5f96-156">Virtual network gateway recommendations</span></span>

<span data-ttu-id="f5f96-157">內部部署流量會透過虛擬網路閘道傳遞至 VNet。</span><span class="sxs-lookup"><span data-stu-id="f5f96-157">On-premises traffic passes to the VNet through a virtual network gateway.</span></span> <span data-ttu-id="f5f96-158">我們建議使用 [Azure VPN 閘道][guidance-vpn-gateway]或 [Azure ExpressRoute 閘道][guidance-expressroute]。</span><span class="sxs-lookup"><span data-stu-id="f5f96-158">We recommend an [Azure VPN gateway][guidance-vpn-gateway] or an [Azure ExpressRoute gateway][guidance-expressroute].</span></span>

### <a name="nva-recommendations"></a><span data-ttu-id="f5f96-159">NVA 建議</span><span class="sxs-lookup"><span data-stu-id="f5f96-159">NVA recommendations</span></span>

<span data-ttu-id="f5f96-160">NVA 提供不同的服務來管理和監視網路流量。</span><span class="sxs-lookup"><span data-stu-id="f5f96-160">NVAs provide different services for managing and monitoring network traffic.</span></span> <span data-ttu-id="f5f96-161">[Azure Marketplace][azure-marketplace-nva] 提供數個第三方廠商的 NVA 供您使用。</span><span class="sxs-lookup"><span data-stu-id="f5f96-161">The [Azure Marketplace][azure-marketplace-nva] offers several third-party vendor NVAs that you can use.</span></span> <span data-ttu-id="f5f96-162">如果這些第三方廠商的 NVA 都不符合您的需求，您可以使用虛擬機器建立自訂的 NVA。</span><span class="sxs-lookup"><span data-stu-id="f5f96-162">If none of these third-party NVAs meet your requirements, you can create a custom NVA using VMs.</span></span> 

<span data-ttu-id="f5f96-163">例如，此參考架構的解決方案部署，會在虛擬機器上實作具有下列功能的 NVA：</span><span class="sxs-lookup"><span data-stu-id="f5f96-163">For example, the solution deployment for this reference architecture implements an NVA with the following functionality on a VM:</span></span>

* <span data-ttu-id="f5f96-164">使用 NVA 網路介面 (NIC) 上的 [IP 轉送][ip-forwarding]來路由流量。</span><span class="sxs-lookup"><span data-stu-id="f5f96-164">Traffic is routed using [IP forwarding][ip-forwarding] on the NVA network interfaces (NICs).</span></span>
* <span data-ttu-id="f5f96-165">流量只會在合適的狀況下允許通過 NVA。</span><span class="sxs-lookup"><span data-stu-id="f5f96-165">Traffic is permitted to pass through the NVA only if it is appropriate to do so.</span></span> <span data-ttu-id="f5f96-166">參考架構中的每個 NVA 虛擬機器都是簡單的 Linux 路由器。</span><span class="sxs-lookup"><span data-stu-id="f5f96-166">Each NVA VM in the reference architecture is a simple Linux router.</span></span> <span data-ttu-id="f5f96-167">輸入流量會抵達網路介面 eth0，而輸出流量會符合自訂指令碼定義的規則，自訂指令碼是透過網路介面 eth1 分派。</span><span class="sxs-lookup"><span data-stu-id="f5f96-167">Inbound traffic arrives on network interface *eth0*, and outbound traffic matches rules defined by custom scripts dispatched through network interface *eth1*.</span></span>
* <span data-ttu-id="f5f96-168">NVA 只能從管理子網路進行設定。</span><span class="sxs-lookup"><span data-stu-id="f5f96-168">The NVAs can only be configured from the management subnet.</span></span> 
* <span data-ttu-id="f5f96-169">路由至管理子網路的流量不會通過 NVA。</span><span class="sxs-lookup"><span data-stu-id="f5f96-169">Traffic routed to the management subnet does not pass through the NVAs.</span></span> <span data-ttu-id="f5f96-170">否則，如果 NVA 失效，將不會有任何通往管理子網路的路由可修復此問題。</span><span class="sxs-lookup"><span data-stu-id="f5f96-170">Otherwise, if the NVAs fail, there would be no route to the management subnet to fix them.</span></span>  
* <span data-ttu-id="f5f96-171">NVA 的虛擬機器會置於負載平衡器後方的[可用性設定組][availability-set]。</span><span class="sxs-lookup"><span data-stu-id="f5f96-171">The VMs for the NVA are placed in an [availability set][availability-set] behind a load balancer.</span></span> <span data-ttu-id="f5f96-172">閘道子網路中的 UDR 會將 NVA 要求導向負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="f5f96-172">The UDR in the gateway subnet directs NVA requests to the load balancer.</span></span>

<span data-ttu-id="f5f96-173">加入第 7 層 NVA 來終止 NVA 層級的應用程式連線，並維持與後端層的同質性。</span><span class="sxs-lookup"><span data-stu-id="f5f96-173">Include a layer-7 NVA to terminate application connections at the NVA level and maintain affinity with the backend tiers.</span></span> <span data-ttu-id="f5f96-174">這可確保對稱的連線，其中來自後端層的回應流量會透過 NVA 傳回。</span><span class="sxs-lookup"><span data-stu-id="f5f96-174">This guarantees symmetric connectivity, in which response traffic from the backend tiers returns through the NVA.</span></span>

<span data-ttu-id="f5f96-175">要考量的另一個選項是與多個 NVA 串聯連線，且讓每個 NVA 執行特殊安全性工作。</span><span class="sxs-lookup"><span data-stu-id="f5f96-175">Another option to consider is connecting multiple NVAs in series, with each NVA performing a specialized security task.</span></span> <span data-ttu-id="f5f96-176">這可讓您以每個 NVA 為基礎地管理每個安全性功能。</span><span class="sxs-lookup"><span data-stu-id="f5f96-176">This allows each security function to be managed on a per-NVA basis.</span></span> <span data-ttu-id="f5f96-177">例如，實作防火牆的 NVA 可與執行身分識別服務的 NVA 串聯在一起。</span><span class="sxs-lookup"><span data-stu-id="f5f96-177">For example, an NVA implementing a firewall could be placed in series with an NVA running identity services.</span></span> <span data-ttu-id="f5f96-178">方便管理的代價是需要額外的網路躍點，而這可能會增加延遲，因此請確定此動作不會影響您的應用程式效能。</span><span class="sxs-lookup"><span data-stu-id="f5f96-178">The tradeoff for ease of management is the addition of extra network hops that may increase latency, so ensure that this doesn't affect your application's performance.</span></span>


### <a name="nsg-recommendations"></a><span data-ttu-id="f5f96-179">NSG 建議</span><span class="sxs-lookup"><span data-stu-id="f5f96-179">NSG recommendations</span></span>

<span data-ttu-id="f5f96-180">VPN 閘道會使連線所用的公用 IP 位址暴露在內部部署網路中。</span><span class="sxs-lookup"><span data-stu-id="f5f96-180">The VPN gateway exposes a public IP address for the connection to the on-premises network.</span></span> <span data-ttu-id="f5f96-181">我們建議您為輸入 NVA 子網路建立網路安全性群組 (NSG)，以及設定規則來封鎖所有不是源自於內部部署網路的流量。</span><span class="sxs-lookup"><span data-stu-id="f5f96-181">We recommend creating a network security group (NSG) for the inbound NVA subnet, with rules to block all traffic not originating from the on-premises network.</span></span>

<span data-ttu-id="f5f96-182">我們也建議針對每個子網路使用 NSG 來提供第二層保護，以避免輸入的流量略過設定不正確或已停用的 NVA。</span><span class="sxs-lookup"><span data-stu-id="f5f96-182">We also recommend NSGs for each subnet to provide a second level of protection against inbound traffic bypassing an incorrectly configured or disabled NVA.</span></span> <span data-ttu-id="f5f96-183">例如，參考架構中的 Web 層子網路會實作 NSG，並設定一個規則來略過不是從內部部署網路 (192.168.0.0/16) 或 VNet 接收的要求，以及設定另一個規則來忽略不是從連接埠 80 產生的所有要求。</span><span class="sxs-lookup"><span data-stu-id="f5f96-183">For example, the web tier subnet in the reference architecture implements an NSG with a rule to ignore all requests other than those received from the on-premises network (192.168.0.0/16) or the VNet, and another rule that ignores all requests not made on port 80.</span></span>

### <a name="internet-access-recommendations"></a><span data-ttu-id="f5f96-184">網際網路存取建議</span><span class="sxs-lookup"><span data-stu-id="f5f96-184">Internet access recommendations</span></span>

<span data-ttu-id="f5f96-185">使用站對站 VPN 通道來建立[強制通道][azure-forced-tunneling]，讓所有輸出網際網路流量通過內部部署網路，並使用網路位址轉譯 (NAT) 路由至網際網路。</span><span class="sxs-lookup"><span data-stu-id="f5f96-185">[Force-tunnel][azure-forced-tunneling] all outbound Internet traffic through your on-premises network using the site-to-site VPN tunnel, and route to the Internet using network address translation (NAT).</span></span> <span data-ttu-id="f5f96-186">如此可防止儲存在您資料層的機密資訊意外洩漏，並允許檢查和稽核所有傳出流量。</span><span class="sxs-lookup"><span data-stu-id="f5f96-186">This prevents accidental leakage of any confidential information stored in your data tier and allows inspection and auditing of all outgoing traffic.</span></span>

> [!NOTE]
> <span data-ttu-id="f5f96-187">請勿完全封鎖來自應用程式層的網際網路流量，因為這會阻止這些層使用依賴公用 IP 位址的 Azure PaaS 服務，例如虛擬機器診斷記錄和虛擬機器擴充功能下載等功能。</span><span class="sxs-lookup"><span data-stu-id="f5f96-187">Don't completely block Internet traffic from the application tiers, as this will prevent these tiers from using Azure PaaS services that rely on public IP addresses, such as VM diagnostics logging, downloading of VM extensions, and other functionality.</span></span> <span data-ttu-id="f5f96-188">Azure 診斷也需要元件可以讀取和寫入至 Azure 儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="f5f96-188">Azure diagnostics also requires that components can read and write to an Azure Storage account.</span></span>
> 
> 

<span data-ttu-id="f5f96-189">請確認輸出網際網路流量已正確地使用強制通道。</span><span class="sxs-lookup"><span data-stu-id="f5f96-189">Verify that outbound internet traffic is force-tunneled correctly.</span></span> <span data-ttu-id="f5f96-190">如果您在內部部署伺服器上使用的 VPN 連線具有[路由及遠端存取服務][routing-and-remote-access-service]，請使用 [WireShark][wireshark] 或 [Microsoft Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) 這類工具。</span><span class="sxs-lookup"><span data-stu-id="f5f96-190">If you're using a VPN connection with the [routing and remote access service][routing-and-remote-access-service] on an on-premises server, use a tool such as [WireShark][wireshark] or [Microsoft Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226).</span></span>

### <a name="management-subnet-recommendations"></a><span data-ttu-id="f5f96-191">管理子網路建議</span><span class="sxs-lookup"><span data-stu-id="f5f96-191">Management subnet recommendations</span></span>

<span data-ttu-id="f5f96-192">管理子網路包含可執行管理和監視功能的 jumpbox。</span><span class="sxs-lookup"><span data-stu-id="f5f96-192">The management subnet contains a jumpbox that performs management and monitoring functionality.</span></span> <span data-ttu-id="f5f96-193">僅限 Jumpbox 可執行所有安全的管理工作。</span><span class="sxs-lookup"><span data-stu-id="f5f96-193">Restrict execution of all secure management tasks to the jumpbox.</span></span>
 
<span data-ttu-id="f5f96-194">請勿針對 Jumpbox 建立公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="f5f96-194">Do not create a public IP address for the jumpbox.</span></span> <span data-ttu-id="f5f96-195">相反地，建立一個透過傳入閘道存取 jumpbox 的路由。</span><span class="sxs-lookup"><span data-stu-id="f5f96-195">Instead, create one route to access the jumpbox through the incoming gateway.</span></span> <span data-ttu-id="f5f96-196">建立 NSG 規則，讓管理子網路只回應來自許可路由的要求。</span><span class="sxs-lookup"><span data-stu-id="f5f96-196">Create NSG rules so the management subnet only responds to requests from the allowed route.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="f5f96-197">延展性考量</span><span class="sxs-lookup"><span data-stu-id="f5f96-197">Scalability considerations</span></span>

<span data-ttu-id="f5f96-198">參考架構會使用負載平衡器，來將內部部署網路流量導向會路由流量的 NVA 裝置集區。</span><span class="sxs-lookup"><span data-stu-id="f5f96-198">The reference architecture uses a load balancer to direct on-premises network traffic to a pool of NVA devices, which route the traffic.</span></span> <span data-ttu-id="f5f96-199">NVA 會放置在[可用性設定組][availability-set]中。</span><span class="sxs-lookup"><span data-stu-id="f5f96-199">The NVAs are placed in an [availability set][availability-set].</span></span> <span data-ttu-id="f5f96-200">此設計可讓您監視一段時間的 NVA 輸送量，並新增 NVA 裝置來回應增加的負載。</span><span class="sxs-lookup"><span data-stu-id="f5f96-200">This design allows you to monitor the throughput of the NVAs over time and add NVA devices in response to increases in load.</span></span>

<span data-ttu-id="f5f96-201">標準 SKU VPN 閘道會支援高達 100 Mbps 的持續性輸送量。</span><span class="sxs-lookup"><span data-stu-id="f5f96-201">The standard SKU VPN gateway supports sustained throughput of up to 100 Mbps.</span></span> <span data-ttu-id="f5f96-202">高效能 SKU 提供最多 200 Mbps。</span><span class="sxs-lookup"><span data-stu-id="f5f96-202">The High Performance SKU provides up to 200 Mbps.</span></span> <span data-ttu-id="f5f96-203">如需更高的頻寬，請考慮升級至 ExpressRoute 閘道。</span><span class="sxs-lookup"><span data-stu-id="f5f96-203">For higher bandwidths, consider upgrading to an ExpressRoute gateway.</span></span> <span data-ttu-id="f5f96-204">ExpressRoute 提供高達 10 Gbps，並具有比 VPN 連線更低的延遲。</span><span class="sxs-lookup"><span data-stu-id="f5f96-204">ExpressRoute provides up to 10 Gbps bandwidth with lower latency than a VPN connection.</span></span>

<span data-ttu-id="f5f96-205">如需有關 Azure 閘道延展性的詳細資訊，請參閱[使用 Azure 和內部部署 VPN 實作混合式網路架構][guidance-vpn-gateway-scalability]和[使用 Azure ExpressRoute 實作混合式網路架構][guidance-expressroute-scalability]中的延展性考量區段。</span><span class="sxs-lookup"><span data-stu-id="f5f96-205">For more information about the scalability of Azure gateways, see the scalability consideration section in [Implementing a hybrid network architecture with Azure and on-premises VPN][guidance-vpn-gateway-scalability] and [Implementing a hybrid network architecture with Azure ExpressRoute][guidance-expressroute-scalability].</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="f5f96-206">可用性考量</span><span class="sxs-lookup"><span data-stu-id="f5f96-206">Availability considerations</span></span>

<span data-ttu-id="f5f96-207">如前所述，參考架構會使用負載平衡器後方的 NVA 裝置集區。</span><span class="sxs-lookup"><span data-stu-id="f5f96-207">As mentioned, the reference architecture uses a pool of NVA devices behind a load balancer.</span></span> <span data-ttu-id="f5f96-208">負載平衡器會使用健康情況探查來監視每個 NVA ，然後從集區中移除任何沒有回應的 NVA。</span><span class="sxs-lookup"><span data-stu-id="f5f96-208">The load balancer uses a health probe to monitor each NVA and will remove any unresponsive NVAs from the pool.</span></span>

<span data-ttu-id="f5f96-209">如果您使用 Azure ExpressRoute 來提供 VNet 和內部部署網路之間的連線，請[設定 VPN 閘道來提供容錯移轉][ra-vpn-failover] (如果 ExpressRoute 連線變得無法使用)。</span><span class="sxs-lookup"><span data-stu-id="f5f96-209">If you're using Azure ExpressRoute to provide connectivity between the VNet and on-premises network, [configure a VPN gateway to provide failover][ra-vpn-failover] if the ExpressRoute connection becomes unavailable.</span></span>

<span data-ttu-id="f5f96-210">如需有關維護 VPN 和 ExpressRoute 連線可用性的詳細資訊，請參閱[使用 Azure 和內部部署 VPN 實作混合式網路架構][guidance-vpn-gateway-availability]和[使用 Azure ExpressRoute 實作混合式網路架構][guidance-expressroute-availability]中的可用性考量。</span><span class="sxs-lookup"><span data-stu-id="f5f96-210">For specific information on maintaining availability for VPN and ExpressRoute connections, see the availability considerations in [Implementing a hybrid network architecture with Azure and on-premises VPN][guidance-vpn-gateway-availability] and [Implementing a hybrid network architecture with Azure ExpressRoute][guidance-expressroute-availability].</span></span> 

## <a name="manageability-considerations"></a><span data-ttu-id="f5f96-211">管理性考量</span><span class="sxs-lookup"><span data-stu-id="f5f96-211">Manageability considerations</span></span>

<span data-ttu-id="f5f96-212">應只有管理子網路中的 jumpbox 才能執行所有應用程式和資源的監視。</span><span class="sxs-lookup"><span data-stu-id="f5f96-212">All application and resource monitoring should be performed by the jumpbox in the management subnet.</span></span> <span data-ttu-id="f5f96-213">根據您的應用程式需求，您可能需要管理子網路中的其他監視資源。</span><span class="sxs-lookup"><span data-stu-id="f5f96-213">Depending on your application requirements, you may need additional monitoring resources in the management subnet.</span></span> <span data-ttu-id="f5f96-214">如果是的話，應透過 jumpbox 來存取這些資源。</span><span class="sxs-lookup"><span data-stu-id="f5f96-214">If so, these resources should be accessed through the jumpbox.</span></span>

<span data-ttu-id="f5f96-215">如果從內部部署網路到 Azure 的閘道連線關閉，藉由部署公用 IP 位址、將它加入 jumpbox、然後從網際網路進行遠端登入，仍可連線至 jumpbox。</span><span class="sxs-lookup"><span data-stu-id="f5f96-215">If gateway connectivity from your on-premises network to Azure is down, you can still reach the jumpbox by deploying a public IP address, adding it to the jumpbox, and remoting in from the internet.</span></span>

<span data-ttu-id="f5f96-216">NSG 規則會保護參考架構中每一層的子網路。</span><span class="sxs-lookup"><span data-stu-id="f5f96-216">Each tier's subnet in the reference architecture is protected by NSG rules.</span></span> <span data-ttu-id="f5f96-217">您可能需要建立規則來開啟連接埠 3389，以便在 Windows 虛擬機器上使用遠端桌面通訊協定 (RDP) 的存取，或開啟連接埠 22，以便在 Linux 虛擬機器上使用安全殼層 (SSH) 的存取。</span><span class="sxs-lookup"><span data-stu-id="f5f96-217">You may need to create a rule to open port 3389 for remote desktop protocol (RDP) access on Windows VMs or port 22 for secure shell (SSH) access on Linux VMs.</span></span> <span data-ttu-id="f5f96-218">其他管理和監視工具可能需要規則來開啟其他連接埠。</span><span class="sxs-lookup"><span data-stu-id="f5f96-218">Other management and monitoring tools may require rules to open additional ports.</span></span>

<span data-ttu-id="f5f96-219">如果您使用 ExpressRoute 來提供您內部部署資料中心與 Azure 之間的連線，請使用 [Azure 連線工具組 (AzureCT)][azurect] 來監視連線問題並進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="f5f96-219">If you're using ExpressRoute to provide the connectivity between your on-premises datacenter and Azure, use the [Azure Connectivity Toolkit (AzureCT)][azurect] to monitor and troubleshoot connection issues.</span></span>

<span data-ttu-id="f5f96-220">您可以在[使用 Azure 和內部部署 VPN 實作混合式網路架構][guidance-vpn-gateway-manageability]和[使用 Azure ExpressRoute 實作混合式網路架構][guidance-expressroute-manageability]的文章中，找到特別針對監視和管理 VPN 與 ExpressRoute 連線的其他資訊。</span><span class="sxs-lookup"><span data-stu-id="f5f96-220">You can find additional information specifically aimed at monitoring and managing VPN and ExpressRoute connections in the articles [Implementing a hybrid network architecture with Azure and on-premises VPN][guidance-vpn-gateway-manageability] and [Implementing a hybrid network architecture with Azure ExpressRoute][guidance-expressroute-manageability].</span></span>

## <a name="security-considerations"></a><span data-ttu-id="f5f96-221">安全性考量</span><span class="sxs-lookup"><span data-stu-id="f5f96-221">Security considerations</span></span>

<span data-ttu-id="f5f96-222">此參考架構實作多個安全性層級。</span><span class="sxs-lookup"><span data-stu-id="f5f96-222">This reference architecture implements multiple levels of security.</span></span>

### <a name="routing-all-on-premises-user-requests-through-the-nva"></a><span data-ttu-id="f5f96-223">透過 NVA 路由所有內部部署使用者要求</span><span class="sxs-lookup"><span data-stu-id="f5f96-223">Routing all on-premises user requests through the NVA</span></span>
<span data-ttu-id="f5f96-224">閘道子網路中的 UDR 會封鎖不是從內部部署收到的所有使用者要求。</span><span class="sxs-lookup"><span data-stu-id="f5f96-224">The UDR in the gateway subnet blocks all user requests other than those received from on-premises.</span></span> <span data-ttu-id="f5f96-225">UDR 會將允許的要求傳遞至私人 DMZ 子網路中的 NVA，而且如果 NVA 規則允許的話，這些要求會繼續傳遞至應用程式。</span><span class="sxs-lookup"><span data-stu-id="f5f96-225">The UDR passes allowed requests to the NVAs in the private DMZ subnet, and these requests are passed on to the application if they are allowed by the NVA rules.</span></span> <span data-ttu-id="f5f96-226">您可以將其他路由新增至 UDR，但請確定路由不會不小心略過 NVA，或是封鎖要傳向管理子網路的系統管理流量。</span><span class="sxs-lookup"><span data-stu-id="f5f96-226">You can add other routes to the UDR, but make sure they don't inadvertently bypass the NVAs or block administrative traffic intended for the management subnet.</span></span>

<span data-ttu-id="f5f96-227">NVA 前方的負載平衡器也可以當做安全性裝置，如果流量不在負載平衡規則中開啟的連接埠上，則將其略過。</span><span class="sxs-lookup"><span data-stu-id="f5f96-227">The load balancer in front of the NVAs also acts as a security device by ignoring traffic on ports that are not open in the load balancing rules.</span></span> <span data-ttu-id="f5f96-228">參考架構中的負載平衡器只會接聽連接埠 80 上的 HTTP 要求和連接埠 443 上的 HTTPS 要求。</span><span class="sxs-lookup"><span data-stu-id="f5f96-228">The load balancers in the reference architecture only listen for HTTP requests on port 80 and HTTPS requests on port 443.</span></span> <span data-ttu-id="f5f96-229">記錄您新增至負載平衡器的任何其他規則，並監視流量以確保沒有安全性問題。</span><span class="sxs-lookup"><span data-stu-id="f5f96-229">Document any additional rules that you add to the load balancers, and monitor traffic to ensure there are no security issues.</span></span>

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a><span data-ttu-id="f5f96-230">使用 NSG 封鎖/傳遞應用程式層之間的流量</span><span class="sxs-lookup"><span data-stu-id="f5f96-230">Using NSGs to block/pass traffic between application tiers</span></span>
<span data-ttu-id="f5f96-231">使用 NSG 限制各層之間的流量。</span><span class="sxs-lookup"><span data-stu-id="f5f96-231">Traffic between tiers is restricted by using NSGs.</span></span> <span data-ttu-id="f5f96-232">商務層會封鎖不是源自於 Web 層的所有流量，而資料層會封鎖所有不是源自於商務層的所有流量。</span><span class="sxs-lookup"><span data-stu-id="f5f96-232">The business tier blocks all traffic that doesn't originate in the web tier, and the data tier blocks all traffic that doesn't originate in the business tier.</span></span> <span data-ttu-id="f5f96-233">如果您需要擴充 NSG 規則以讓這些層接受更廣泛的存取，請評估這些需求的安全性風險。</span><span class="sxs-lookup"><span data-stu-id="f5f96-233">If you have a requirement to expand the NSG rules to allow broader access to these tiers, weigh these requirements against the security risks.</span></span> <span data-ttu-id="f5f96-234">每個新的輸入路徑都代表著有機會發生意外或有意的資料外洩或應用程式損害。</span><span class="sxs-lookup"><span data-stu-id="f5f96-234">Each new inbound pathway represents an opportunity for accidental or purposeful data leakage or application damage.</span></span>

### <a name="devops-access"></a><span data-ttu-id="f5f96-235">DevOps 存取</span><span class="sxs-lookup"><span data-stu-id="f5f96-235">DevOps access</span></span>
<span data-ttu-id="f5f96-236">使用 [RBAC][rbac] 限制 DevOps 可在每一層執行的作業。</span><span class="sxs-lookup"><span data-stu-id="f5f96-236">Use [RBAC][rbac] to restrict the operations that DevOps can perform on each tier.</span></span> <span data-ttu-id="f5f96-237">授與權限時，請使用[最小權限的原則][security-principle-of-least-privilege]。</span><span class="sxs-lookup"><span data-stu-id="f5f96-237">When granting permissions, use the [principle of least privilege][security-principle-of-least-privilege].</span></span> <span data-ttu-id="f5f96-238">記錄所有的系統管理作業並定期執行稽核，以確保所有設定變更都是經過規劃的。</span><span class="sxs-lookup"><span data-stu-id="f5f96-238">Log all administrative operations and perform regular audits to ensure any configuration changes were planned.</span></span>

## <a name="solution-deployment"></a><span data-ttu-id="f5f96-239">解決方案部署</span><span class="sxs-lookup"><span data-stu-id="f5f96-239">Solution deployment</span></span>

<span data-ttu-id="f5f96-240">在 [GitHub][github-folder] 中有實作這些建議的參考架構部署。</span><span class="sxs-lookup"><span data-stu-id="f5f96-240">A deployment for a reference architecture that implements these recommendations is available on [GitHub][github-folder].</span></span> <span data-ttu-id="f5f96-241">您可以遵循下列指示來部署參考架構：</span><span class="sxs-lookup"><span data-stu-id="f5f96-241">The reference architecture can be deployed by following the directions below:</span></span>

1. <span data-ttu-id="f5f96-242">按一下下方的按鈕：</span><span class="sxs-lookup"><span data-stu-id="f5f96-242">Click the button below:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fdmz%2Fsecure-vnet-hybrid%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
2. <span data-ttu-id="f5f96-243">一旦連結已在 Azure 入口網站中開啟，您必須輸入部分設定的值：</span><span class="sxs-lookup"><span data-stu-id="f5f96-243">Once the link has opened in the Azure portal, you must enter values for some of the settings:</span></span>   
   * <span data-ttu-id="f5f96-244">**資源群組**名稱已在參數檔案中定義，因此請在文字方塊中選取 [新建] 並輸入 `ra-private-dmz-rg`。</span><span class="sxs-lookup"><span data-stu-id="f5f96-244">The **Resource group** name is already defined in the parameter file, so select **Create New** and enter `ra-private-dmz-rg` in the text box.</span></span>
   * <span data-ttu-id="f5f96-245">從 [位置] 下拉式方塊選取區域。</span><span class="sxs-lookup"><span data-stu-id="f5f96-245">Select the region from the **Location** drop down box.</span></span>
   * <span data-ttu-id="f5f96-246">請勿編輯 [範本的根 URI] 或 [參數根 URI] 文字方塊。</span><span class="sxs-lookup"><span data-stu-id="f5f96-246">Do not edit the **Template Root Uri** or the **Parameter Root Uri** text boxes.</span></span>
   * <span data-ttu-id="f5f96-247">檢閱條款和條件，然後按一下 [我同意上方所述的條款及條件] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="f5f96-247">Review the terms and conditions, then click the **I agree to the terms and conditions stated above** checkbox.</span></span>
   * <span data-ttu-id="f5f96-248">按一下 [購買] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="f5f96-248">Click the **Purchase** button.</span></span>
3. <span data-ttu-id="f5f96-249">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="f5f96-249">Wait for the deployment to complete.</span></span>
4. <span data-ttu-id="f5f96-250">參數檔案中有硬式編碼的所有 VM 的系統管理員使用者名稱和密碼，強烈建議您立即變更這兩項。</span><span class="sxs-lookup"><span data-stu-id="f5f96-250">The parameter files include hard-coded administrator user name and password for all VMs, and it is strongly recommended that you immediately change both.</span></span> <span data-ttu-id="f5f96-251">在 Azure 入口網站中選取部署中的每個虛擬機器，然後按一下 [支援與疑難排解] 刀鋒視窗中的 [重設密碼]。</span><span class="sxs-lookup"><span data-stu-id="f5f96-251">For each VM in the deployment, select it in the Azure portal and then click **Reset password** in the **Support + troubleshooting** blade.</span></span> <span data-ttu-id="f5f96-252">選取 [模式] 下拉式清單方塊中的 [重設密碼]，然後選取新的[使用者名稱] 和 [密碼]。</span><span class="sxs-lookup"><span data-stu-id="f5f96-252">Select **Reset password** in the **Mode** drop down box, then select a new **User name** and **Password**.</span></span> <span data-ttu-id="f5f96-253">按一下 [更新] 按鈕以儲存。</span><span class="sxs-lookup"><span data-stu-id="f5f96-253">Click the **Update** button to save.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f5f96-254">後續步驟</span><span class="sxs-lookup"><span data-stu-id="f5f96-254">Next steps</span></span>

* <span data-ttu-id="f5f96-255">了解如何[在 Azure 與網際網路之間實作 DMZ](secure-vnet-dmz.md)。</span><span class="sxs-lookup"><span data-stu-id="f5f96-255">Learn how to implement a [DMZ between Azure and the Internet](secure-vnet-dmz.md).</span></span>
* <span data-ttu-id="f5f96-256">了解如何實作[高可用性的混合式網路架構][ra-vpn-failover]。</span><span class="sxs-lookup"><span data-stu-id="f5f96-256">Learn how to implement a [highly available hybrid network architecture][ra-vpn-failover].</span></span>
* <span data-ttu-id="f5f96-257">如需有關使用 Azure 管理網路安全性的詳細資訊，請參閱 [Microsoft 雲端服務和網路安全性][cloud-services-network-security]。</span><span class="sxs-lookup"><span data-stu-id="f5f96-257">For more information about managing network security with Azure, see [Microsoft cloud services and network security][cloud-services-network-security].</span></span>
* <span data-ttu-id="f5f96-258">如需在 Azure 中保護資源的詳細資訊，請參閱[開始使用 Microsoft Azure 安全性][getting-started-with-azure-security]。</span><span class="sxs-lookup"><span data-stu-id="f5f96-258">For detailed information about protecting resources in Azure, see [Getting started with Microsoft Azure security][getting-started-with-azure-security].</span></span> 
* <span data-ttu-id="f5f96-259">若要深入了解如何解決 Azure 閘道連線上的安全性考量，請參閱[使用 Azure 和內部部署 VPN 實作混合式網路架構][guidance-vpn-gateway-security]和[使用 Azure ExpressRoute 實作混合式網路架構][guidance-expressroute-security]。</span><span class="sxs-lookup"><span data-stu-id="f5f96-259">For additional details on addressing security concerns across an Azure gateway connection, see [Implementing a hybrid network architecture with Azure and on-premises VPN][guidance-vpn-gateway-security] and [Implementing a hybrid network architecture with Azure ExpressRoute][guidance-expressroute-security].</span></span>
  > 

<!-- links -->

[availability-set]: /azure/virtual-machines/virtual-machines-windows-create-availability-set
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[azure-forced-tunneling]: https://azure.microsoft.com/en-gb/documentation/articles/vpn-gateway-forced-tunneling-rm/
[azure-marketplace-nva]: https://azuremarketplace.microsoft.com/marketplace/apps/category/networking
[cloud-services-network-security]: https://azure.microsoft.com/documentation/articles/best-practices-network-security/
[getting-started-with-azure-security]: /azure/security/azure-security-getting-started
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/dmz/secure-vnet-hybrid
[guidance-expressroute]: ../hybrid-networking/expressroute.md
[guidance-expressroute-availability]: ../hybrid-networking/expressroute.md#availability-considerations
[guidance-expressroute-manageability]: ../hybrid-networking/expressroute.md#manageability-considerations
[guidance-expressroute-security]: ../hybrid-networking/expressroute.md#security-considerations
[guidance-expressroute-scalability]: ../hybrid-networking/expressroute.md#scalability-considerations
[guidance-vpn-gateway]: ../hybrid-networking/vpn.md
[guidance-vpn-gateway-availability]: ../hybrid-networking/vpn.md#availability-considerations
[guidance-vpn-gateway-manageability]: ../hybrid-networking/vpn.md#manageability-considerations
[guidance-vpn-gateway-scalability]: ../hybrid-networking/vpn.md#scalability-considerations
[guidance-vpn-gateway-security]: ../hybrid-networking/vpn.md#security-considerations
[ip-forwarding]: /azure/virtual-network/virtual-networks-udr-overview#ip-forwarding
[ra-expressroute]: ../hybrid-networking/expressroute.md
[ra-n-tier]: ../virtual-machines-windows/n-tier.md
[ra-vpn]: ../hybrid-networking/vpn.md
[ra-vpn-failover]: ../hybrid-networking/expressroute-vpn-failover.md
[rbac]: /azure/active-directory/role-based-access-control-configure
[rbac-custom-roles]: /azure/active-directory/role-based-access-control-custom-roles
[routing-and-remote-access-service]: https://technet.microsoft.com/library/dd469790(v=ws.11).aspx
[security-principle-of-least-privilege]: https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1
[udr-overview]: /azure/virtual-network/virtual-networks-udr-overview
[visio-download]: https://archcenter.blob.core.windows.net/cdn/dmz-reference-architectures.vsdx
[wireshark]: https://www.wireshark.org/
[0]: ./images/dmz-private.png "安全混合式網路架構"

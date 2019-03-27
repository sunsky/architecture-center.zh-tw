---
title: 實作安全的混合式網路架構
titleSuffix: Azure Reference Architectures
description: 在 Azure 中實作安全的混合式網路架構。
author: telmosampaio
ms.date: 10/22/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18, networking
ms.openlocfilehash: 82327cca08e614bfe5226c9ca1a414388878a7c2
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243509"
---
# <a name="implement-a-dmz-between-azure-and-your-on-premises-datacenter"></a><span data-ttu-id="6a6f4-103">實作 Azure 與內部部署資料中心之間的 DMZ</span><span class="sxs-lookup"><span data-stu-id="6a6f4-103">Implement a DMZ between Azure and your on-premises datacenter</span></span>

<span data-ttu-id="6a6f4-104">此參考架構顯示的安全混合式網路，可將內部部署網路擴充至 Azure。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-104">This reference architecture shows a secure hybrid network that extends an on-premises network to Azure.</span></span> <span data-ttu-id="6a6f4-105">此架構會在內部部署網路與 Azure 虛擬網路 (VNet) 之間實作 DMZ (也稱為「周邊網路」)。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-105">The architecture implements a DMZ, also called a *perimeter network*, between the on-premises network and an Azure virtual network (VNet).</span></span> <span data-ttu-id="6a6f4-106">DMZ 包含實作安全性功能 (例如防火牆和封包檢查) 的網路虛擬裝置 (NVA)。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-106">The DMZ includes network virtual appliances (NVAs) that implement security functionality such as firewalls and packet inspection.</span></span> <span data-ttu-id="6a6f4-107">VNet 的所有傳出流量會強制通過內部部署網路後再傳送至網際網路，以便進行稽核。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-107">All outgoing traffic from the VNet is force-tunneled to the Internet through the on-premises network, so that it can be audited.</span></span> <span data-ttu-id="6a6f4-108">[**部署這個解決方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-108">[**Deploy this solution**](#deploy-the-solution).</span></span>

> [!NOTE]
> <span data-ttu-id="6a6f4-109">此案例也可以使用 [Azure 防火牆](/azure/firewall/) (以雲端為基礎的網路安全性服務) 來完成。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-109">This scenario can also be accomplished using [Azure Firewall](/azure/firewall/), a cloud-based network security service.</span></span>

![安全混合式網路架構](./images/dmz-private.png)

<span data-ttu-id="6a6f4-111">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-111">*Download a [Visio file][visio-download] of this architecture.*</span></span>

<span data-ttu-id="6a6f4-112">此架構需要連線到內部部署資料中心，可使用 [VPN 閘道][ra-vpn]或 [ExpressRoute][ra-expressroute]連線。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-112">This architecture requires a connection to your on-premises datacenter, using either a [VPN gateway][ra-vpn] or an [ExpressRoute][ra-expressroute] connection.</span></span> <span data-ttu-id="6a6f4-113">此架構的典型使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="6a6f4-113">Typical uses for this architecture include:</span></span>

- <span data-ttu-id="6a6f4-114">部分工作負載在內部部署中執行、部分在 Azure 中執行的混合式應用程式。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-114">Hybrid applications where workloads run partly on-premises and partly in Azure.</span></span>
- <span data-ttu-id="6a6f4-115">針對從內部部署資料中心進入 Azure VNet 的流量，需要進行更精確控管的基礎結構。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-115">Infrastructure that requires granular control over traffic entering an Azure VNet from an on-premises datacenter.</span></span>
- <span data-ttu-id="6a6f4-116">必須稽核傳出流量的應用程式。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-116">Applications that must audit outgoing traffic.</span></span> <span data-ttu-id="6a6f4-117">這通常是許多商業系統的管理需求，並且有助於防止個人資訊遭到公開洩漏。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-117">This is often a regulatory requirement of many commercial systems and can help to prevent public disclosure of private information.</span></span>

## <a name="architecture"></a><span data-ttu-id="6a6f4-118">架構</span><span class="sxs-lookup"><span data-stu-id="6a6f4-118">Architecture</span></span>

<span data-ttu-id="6a6f4-119">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-119">The architecture consists of the following components.</span></span>

- <span data-ttu-id="6a6f4-120">**內部部署網路**。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-120">**On-premises network**.</span></span> <span data-ttu-id="6a6f4-121">在組織內實作的私人區域網路。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-121">A private local-area network implemented in an organization.</span></span>
- <span data-ttu-id="6a6f4-122">**Azure 虛擬網路 (VNet)**。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-122">**Azure virtual network (VNet)**.</span></span> <span data-ttu-id="6a6f4-123">VNet 會裝載在 Azure 中執行的應用程式和其他資源。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-123">The VNet hosts the application and other resources running in Azure.</span></span>
- <span data-ttu-id="6a6f4-124">**閘道**。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-124">**Gateway**.</span></span> <span data-ttu-id="6a6f4-125">閘道會提供內部部署網路與 VNet 中路由器之間的連線。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-125">The gateway provides connectivity between the routers in the on-premises network and the VNet.</span></span>
- <span data-ttu-id="6a6f4-126">**網路虛擬設備 (NVA)**。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-126">**Network virtual appliance (NVA)**.</span></span> <span data-ttu-id="6a6f4-127">NVA 是統稱，用來描述執行以下這類工作的虛擬機器：作為防火牆來允許或拒絕存取、最佳化廣域網路 (WAN) 作業 (包括網路壓縮)、自訂路由或其他網路功能等。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-127">NVA is a generic term that describes a VM performing tasks such as allowing or denying access as a firewall, optimizing wide area network (WAN) operations (including network compression), custom routing, or other network functionality.</span></span>
- <span data-ttu-id="6a6f4-128">**Web 層、商務層和資料層子網路**。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-128">**Web tier, business tier, and data tier subnets**.</span></span> <span data-ttu-id="6a6f4-129">裝載虛擬機器和服務的子網路，其會實作在雲端中執行的 3 層應用程式範例。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-129">Subnets hosting the VMs and services that implement an example 3-tier application running in the cloud.</span></span> <span data-ttu-id="6a6f4-130">如需詳細資訊，請參閱[在 Azure 上執行適用於多層式架構的 Windows 虛擬機器][ra-n-tier]。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-130">See [Running Windows VMs for an N-tier architecture on Azure][ra-n-tier] for more information.</span></span>
- <span data-ttu-id="6a6f4-131">**使用者定義路由 (UDR)**。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-131">**User defined routes (UDR)**.</span></span> <span data-ttu-id="6a6f4-132">[使用者定義路由][udr-overview]會定義 Azure VNet 內 IP 流量的流程。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-132">[User defined routes][udr-overview] define the flow of IP traffic within Azure VNets.</span></span>

    > [!NOTE]
    > <span data-ttu-id="6a6f4-133">根據您 VPN 連線的需求，您可以設定邊界閘道協定 (BGP) 路由，而不是使用 UDR 來實作轉送規則，透過內部部署網路將流量導回。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-133">Depending on the requirements of your VPN connection, you can configure Border Gateway Protocol (BGP) routes instead of using UDRs to implement the forwarding rules that direct traffic back through the on-premises network.</span></span>
    >

- <span data-ttu-id="6a6f4-134">**管理子網路**。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-134">**Management subnet**.</span></span> <span data-ttu-id="6a6f4-135">此子網路包含的虛擬機器會針對 VNet 中執行的元件實作管理和監視功能。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-135">This subnet contains VMs that implement management and monitoring capabilities for the components running in the VNet.</span></span>

## <a name="recommendations"></a><span data-ttu-id="6a6f4-136">建議</span><span class="sxs-lookup"><span data-stu-id="6a6f4-136">Recommendations</span></span>

<span data-ttu-id="6a6f4-137">下列建議適用於大部分的案例。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-137">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="6a6f4-138">除非您有特定的需求會覆寫它們，否則請遵循下列建議。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-138">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="access-control-recommendations"></a><span data-ttu-id="6a6f4-139">存取控制建議</span><span class="sxs-lookup"><span data-stu-id="6a6f4-139">Access control recommendations</span></span>

<span data-ttu-id="6a6f4-140">使用[角色型存取控制][rbac] (RBAC) 來管理您應用程式中的資源。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-140">Use [Role-Based Access Control][rbac] (RBAC) to manage the resources in your application.</span></span> <span data-ttu-id="6a6f4-141">請考慮建立下列[自訂角色][rbac-custom-roles]：</span><span class="sxs-lookup"><span data-stu-id="6a6f4-141">Consider creating the following [custom roles][rbac-custom-roles]:</span></span>

- <span data-ttu-id="6a6f4-142">DevOps 角色，有權管理應用程式的基礎結構、部署應用程式元件，以及監視和重新啟動虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-142">A DevOps role with permissions to administer the infrastructure for the application, deploy the application components, and monitor and restart VMs.</span></span>

- <span data-ttu-id="6a6f4-143">中央 IT 管理員角色，管理和監視網路資源。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-143">A centralized IT administrator role to manage and monitor network resources.</span></span>

- <span data-ttu-id="6a6f4-144">安全性 IT 管理員角色，管理安全的網路資源，例如 NVA。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-144">A security IT administrator role to manage secure network resources such as the NVAs.</span></span>

<span data-ttu-id="6a6f4-145">DevOps 與 IT 管理員角色應不可有 NVA 資源的存取權。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-145">The DevOps and IT administrator roles should not have access to the NVA resources.</span></span> <span data-ttu-id="6a6f4-146">應僅限安全性 IT 管理員角色可存取。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-146">This should be restricted to the security IT administrator role.</span></span>

### <a name="resource-group-recommendations"></a><span data-ttu-id="6a6f4-147">資源群組建議</span><span class="sxs-lookup"><span data-stu-id="6a6f4-147">Resource group recommendations</span></span>

<span data-ttu-id="6a6f4-148">將 Azure 資源 (例如 VM、VNet 及負載平衡器) 組成資源群組，您就可以輕鬆地進行管理。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-148">Azure resources such as VMs, VNets, and load balancers can be easily managed by grouping them together into resource groups.</span></span> <span data-ttu-id="6a6f4-149">將 RBAC 角色指派給每個資源群組，以限制存取。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-149">Assign RBAC roles to each resource group to restrict access.</span></span>

<span data-ttu-id="6a6f4-150">我們建議您建立下列資源群組：</span><span class="sxs-lookup"><span data-stu-id="6a6f4-150">We recommend creating the following resource groups:</span></span>

- <span data-ttu-id="6a6f4-151">資源群組中包含 VNet (但不包括虛擬機器)、NSG 和用以連線至內部部署網路的閘道資源。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-151">A resource group containing the VNet (excluding the VMs), NSGs, and the gateway resources for connecting to the on-premises network.</span></span> <span data-ttu-id="6a6f4-152">將中央 IT 管理員角色指派給此資源群組。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-152">Assign the centralized IT administrator role to this resource group.</span></span>
- <span data-ttu-id="6a6f4-153">資源群組中包含 NVA 的虛擬機器 (包括負載平衡器)、jumpbox 和其他管理虛擬機器，以及強制流量通過 NVA 的閘道子網路 UDR。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-153">A resource group containing the VMs for the NVAs (including the load balancer), the jumpbox and other management VMs, and the UDR for the gateway subnet that forces all traffic through the NVAs.</span></span> <span data-ttu-id="6a6f4-154">將安全性 IT 管理員角色指派給此資源群組。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-154">Assign the security IT administrator role to this resource group.</span></span>
- <span data-ttu-id="6a6f4-155">針對每個應用程式層分隔資源群組，其中包含負載平衡器和虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-155">Separate resource groups for each application tier that contain the load balancer and VMs.</span></span> <span data-ttu-id="6a6f4-156">請注意，此資源群組不應包含每一層的子網路。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-156">Note that this resource group shouldn't include the subnets for each tier.</span></span> <span data-ttu-id="6a6f4-157">將 DevOps 角色指派給此資源群組。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-157">Assign the DevOps role to this resource group.</span></span>

### <a name="virtual-network-gateway-recommendations"></a><span data-ttu-id="6a6f4-158">虛擬網路閘道建議</span><span class="sxs-lookup"><span data-stu-id="6a6f4-158">Virtual network gateway recommendations</span></span>

<span data-ttu-id="6a6f4-159">內部部署流量會透過虛擬網路閘道傳遞至 VNet。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-159">On-premises traffic passes to the VNet through a virtual network gateway.</span></span> <span data-ttu-id="6a6f4-160">我們建議使用 [Azure VPN 閘道][guidance-vpn-gateway]或 [Azure ExpressRoute 閘道][guidance-expressroute]。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-160">We recommend an [Azure VPN gateway][guidance-vpn-gateway] or an [Azure ExpressRoute gateway][guidance-expressroute].</span></span>

### <a name="nva-recommendations"></a><span data-ttu-id="6a6f4-161">NVA 建議</span><span class="sxs-lookup"><span data-stu-id="6a6f4-161">NVA recommendations</span></span>

<span data-ttu-id="6a6f4-162">NVA 提供不同的服務來管理和監視網路流量。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-162">NVAs provide different services for managing and monitoring network traffic.</span></span> <span data-ttu-id="6a6f4-163">[Azure Marketplace][azure-marketplace-nva] 提供數個第三方廠商的 NVA 供您使用。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-163">The [Azure Marketplace][azure-marketplace-nva] offers several third-party vendor NVAs that you can use.</span></span> <span data-ttu-id="6a6f4-164">如果這些第三方廠商的 NVA 都不符合您的需求，您可以使用虛擬機器建立自訂的 NVA。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-164">If none of these third-party NVAs meet your requirements, you can create a custom NVA using VMs.</span></span>

<span data-ttu-id="6a6f4-165">例如，此參考架構的解決方案部署，會在虛擬機器上實作具有下列功能的 NVA：</span><span class="sxs-lookup"><span data-stu-id="6a6f4-165">For example, the solution deployment for this reference architecture implements an NVA with the following functionality on a VM:</span></span>

- <span data-ttu-id="6a6f4-166">使用 NVA 網路介面 (NIC) 上的 [IP 轉送][ip-forwarding]來路由流量。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-166">Traffic is routed using [IP forwarding][ip-forwarding] on the NVA network interfaces (NICs).</span></span>
- <span data-ttu-id="6a6f4-167">流量只會在合適的狀況下允許通過 NVA。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-167">Traffic is permitted to pass through the NVA only if it is appropriate to do so.</span></span> <span data-ttu-id="6a6f4-168">參考架構中的每個 NVA 虛擬機器都是簡單的 Linux 路由器。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-168">Each NVA VM in the reference architecture is a simple Linux router.</span></span> <span data-ttu-id="6a6f4-169">輸入流量會抵達網路介面 eth0，而輸出流量會符合自訂指令碼定義的規則，自訂指令碼是透過網路介面 eth1 分派。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-169">Inbound traffic arrives on network interface *eth0*, and outbound traffic matches rules defined by custom scripts dispatched through network interface *eth1*.</span></span>
- <span data-ttu-id="6a6f4-170">NVA 只能從管理子網路進行設定。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-170">The NVAs can only be configured from the management subnet.</span></span>
- <span data-ttu-id="6a6f4-171">路由至管理子網路的流量不會通過 NVA。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-171">Traffic routed to the management subnet does not pass through the NVAs.</span></span> <span data-ttu-id="6a6f4-172">否則，如果 NVA 失效，將不會有任何通往管理子網路的路由可修復此問題。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-172">Otherwise, if the NVAs fail, there would be no route to the management subnet to fix them.</span></span>
- <span data-ttu-id="6a6f4-173">NVA 的虛擬機器會置於負載平衡器後方的[可用性設定組][availability-set]。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-173">The VMs for the NVA are placed in an [availability set][availability-set] behind a load balancer.</span></span> <span data-ttu-id="6a6f4-174">閘道子網路中的 UDR 會將 NVA 要求導向負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-174">The UDR in the gateway subnet directs NVA requests to the load balancer.</span></span>

<span data-ttu-id="6a6f4-175">加入第 7 層 NVA 來終止 NVA 層級的應用程式連線，並維持與後端層的同質性。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-175">Include a layer-7 NVA to terminate application connections at the NVA level and maintain affinity with the backend tiers.</span></span> <span data-ttu-id="6a6f4-176">這可確保對稱的連線，其中來自後端層的回應流量會透過 NVA 傳回。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-176">This guarantees symmetric connectivity, in which response traffic from the backend tiers returns through the NVA.</span></span>

<span data-ttu-id="6a6f4-177">要考量的另一個選項是與多個 NVA 串聯連線，且讓每個 NVA 執行特殊安全性工作。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-177">Another option to consider is connecting multiple NVAs in series, with each NVA performing a specialized security task.</span></span> <span data-ttu-id="6a6f4-178">這可讓您以每個 NVA 為基礎地管理每個安全性功能。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-178">This allows each security function to be managed on a per-NVA basis.</span></span> <span data-ttu-id="6a6f4-179">例如，實作防火牆的 NVA 可與執行身分識別服務的 NVA 串聯在一起。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-179">For example, an NVA implementing a firewall could be placed in series with an NVA running identity services.</span></span> <span data-ttu-id="6a6f4-180">方便管理的代價是需要額外的網路躍點，而這可能會增加延遲，因此請確定此動作不會影響您的應用程式效能。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-180">The tradeoff for ease of management is the addition of extra network hops that may increase latency, so ensure that this doesn't affect your application's performance.</span></span>

### <a name="nsg-recommendations"></a><span data-ttu-id="6a6f4-181">NSG 建議</span><span class="sxs-lookup"><span data-stu-id="6a6f4-181">NSG recommendations</span></span>

<span data-ttu-id="6a6f4-182">VPN 閘道會使連線所用的公用 IP 位址暴露在內部部署網路中。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-182">The VPN gateway exposes a public IP address for the connection to the on-premises network.</span></span> <span data-ttu-id="6a6f4-183">我們建議您為輸入 NVA 子網路建立網路安全性群組 (NSG)，以及設定規則來封鎖所有不是源自於內部部署網路的流量。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-183">We recommend creating a network security group (NSG) for the inbound NVA subnet, with rules to block all traffic not originating from the on-premises network.</span></span>

<span data-ttu-id="6a6f4-184">我們也建議針對每個子網路使用 NSG 來提供第二層保護，以避免輸入的流量略過設定不正確或已停用的 NVA。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-184">We also recommend NSGs for each subnet to provide a second level of protection against inbound traffic bypassing an incorrectly configured or disabled NVA.</span></span> <span data-ttu-id="6a6f4-185">例如，參考架構中的 Web 層子網路會實作 NSG，並設定一個規則來略過不是從內部部署網路 (192.168.0.0/16) 或 VNet 接收的要求，以及設定另一個規則來忽略不是從連接埠 80 產生的所有要求。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-185">For example, the web tier subnet in the reference architecture implements an NSG with a rule to ignore all requests other than those received from the on-premises network (192.168.0.0/16) or the VNet, and another rule that ignores all requests not made on port 80.</span></span>

### <a name="internet-access-recommendations"></a><span data-ttu-id="6a6f4-186">網際網路存取建議</span><span class="sxs-lookup"><span data-stu-id="6a6f4-186">Internet access recommendations</span></span>

<span data-ttu-id="6a6f4-187">使用站對站 VPN 通道來建立[強制通道][azure-forced-tunneling]，讓所有輸出網際網路流量通過內部部署網路，並使用網路位址轉譯 (NAT) 路由至網際網路。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-187">[Force-tunnel][azure-forced-tunneling] all outbound Internet traffic through your on-premises network using the site-to-site VPN tunnel, and route to the Internet using network address translation (NAT).</span></span> <span data-ttu-id="6a6f4-188">如此可防止儲存在您資料層的機密資訊意外洩漏，並允許檢查和稽核所有傳出流量。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-188">This prevents accidental leakage of any confidential information stored in your data tier and allows inspection and auditing of all outgoing traffic.</span></span>

> [!NOTE]
> <span data-ttu-id="6a6f4-189">請勿完全封鎖來自應用程式層的網際網路流量，因為這會阻止這些層使用依賴公用 IP 位址的 Azure PaaS 服務，例如虛擬機器診斷記錄和虛擬機器擴充功能下載等功能。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-189">Don't completely block Internet traffic from the application tiers, as this will prevent these tiers from using Azure PaaS services that rely on public IP addresses, such as VM diagnostics logging, downloading of VM extensions, and other functionality.</span></span> <span data-ttu-id="6a6f4-190">Azure 診斷也需要元件可以讀取和寫入至 Azure 儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-190">Azure diagnostics also requires that components can read and write to an Azure Storage account.</span></span>

<span data-ttu-id="6a6f4-191">請確認輸出網際網路流量已正確地使用強制通道。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-191">Verify that outbound internet traffic is force-tunneled correctly.</span></span> <span data-ttu-id="6a6f4-192">如果您在內部部署伺服器上使用的 VPN 連線具有[路由及遠端存取服務][routing-and-remote-access-service]，請使用 [WireShark][wireshark] 或 [Microsoft Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) 這類工具。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-192">If you're using a VPN connection with the [routing and remote access service][routing-and-remote-access-service] on an on-premises server, use a tool such as [WireShark][wireshark] or [Microsoft Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226).</span></span>

### <a name="management-subnet-recommendations"></a><span data-ttu-id="6a6f4-193">管理子網路建議</span><span class="sxs-lookup"><span data-stu-id="6a6f4-193">Management subnet recommendations</span></span>

<span data-ttu-id="6a6f4-194">管理子網路包含可執行管理和監視功能的 jumpbox。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-194">The management subnet contains a jumpbox that performs management and monitoring functionality.</span></span> <span data-ttu-id="6a6f4-195">僅限 Jumpbox 可執行所有安全的管理工作。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-195">Restrict execution of all secure management tasks to the jumpbox.</span></span>

<span data-ttu-id="6a6f4-196">請勿針對 Jumpbox 建立公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-196">Do not create a public IP address for the jumpbox.</span></span> <span data-ttu-id="6a6f4-197">相反地，建立一個透過傳入閘道存取 jumpbox 的路由。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-197">Instead, create one route to access the jumpbox through the incoming gateway.</span></span> <span data-ttu-id="6a6f4-198">建立 NSG 規則，讓管理子網路只回應來自許可路由的要求。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-198">Create NSG rules so the management subnet only responds to requests from the allowed route.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="6a6f4-199">延展性考量</span><span class="sxs-lookup"><span data-stu-id="6a6f4-199">Scalability considerations</span></span>

<span data-ttu-id="6a6f4-200">參考架構會使用負載平衡器，來將內部部署網路流量導向會路由流量的 NVA 裝置集區。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-200">The reference architecture uses a load balancer to direct on-premises network traffic to a pool of NVA devices, which route the traffic.</span></span> <span data-ttu-id="6a6f4-201">NVA 會放置在[可用性設定組][availability-set]中。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-201">The NVAs are placed in an [availability set][availability-set].</span></span> <span data-ttu-id="6a6f4-202">此設計可讓您監視一段時間的 NVA 輸送量，並新增 NVA 裝置來回應增加的負載。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-202">This design allows you to monitor the throughput of the NVAs over time and add NVA devices in response to increases in load.</span></span>

<span data-ttu-id="6a6f4-203">標準 SKU VPN 閘道會支援高達 100 Mbps 的持續性輸送量。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-203">The standard SKU VPN gateway supports sustained throughput of up to 100 Mbps.</span></span> <span data-ttu-id="6a6f4-204">高效能 SKU 提供最多 200 Mbps。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-204">The High Performance SKU provides up to 200 Mbps.</span></span> <span data-ttu-id="6a6f4-205">如需更高的頻寬，請考慮升級至 ExpressRoute 閘道。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-205">For higher bandwidths, consider upgrading to an ExpressRoute gateway.</span></span> <span data-ttu-id="6a6f4-206">ExpressRoute 提供高達 10 Gbps，並具有比 VPN 連線更低的延遲。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-206">ExpressRoute provides up to 10 Gbps bandwidth with lower latency than a VPN connection.</span></span>

<span data-ttu-id="6a6f4-207">如需有關 Azure 閘道延展性的詳細資訊，請參閱[使用 Azure 和內部部署 VPN 實作混合式網路架構][guidance-vpn-gateway-scalability]和[使用 Azure ExpressRoute 實作混合式網路架構][guidance-expressroute-scalability]中的延展性考量區段。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-207">For more information about the scalability of Azure gateways, see the scalability consideration section in [Implementing a hybrid network architecture with Azure and on-premises VPN][guidance-vpn-gateway-scalability] and [Implementing a hybrid network architecture with Azure ExpressRoute][guidance-expressroute-scalability].</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="6a6f4-208">可用性考量</span><span class="sxs-lookup"><span data-stu-id="6a6f4-208">Availability considerations</span></span>

<span data-ttu-id="6a6f4-209">如前所述，參考架構會使用負載平衡器後方的 NVA 裝置集區。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-209">As mentioned, the reference architecture uses a pool of NVA devices behind a load balancer.</span></span> <span data-ttu-id="6a6f4-210">負載平衡器會使用健康情況探查來監視每個 NVA ，然後從集區中移除任何沒有回應的 NVA。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-210">The load balancer uses a health probe to monitor each NVA and will remove any unresponsive NVAs from the pool.</span></span>

<span data-ttu-id="6a6f4-211">如果您使用 Azure ExpressRoute 來提供 VNet 和內部部署網路之間的連線，請[設定 VPN 閘道來提供容錯移轉][ra-vpn-failover] (如果 ExpressRoute 連線變得無法使用)。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-211">If you're using Azure ExpressRoute to provide connectivity between the VNet and on-premises network, [configure a VPN gateway to provide failover][ra-vpn-failover] if the ExpressRoute connection becomes unavailable.</span></span>

<span data-ttu-id="6a6f4-212">如需有關維護 VPN 和 ExpressRoute 連線可用性的詳細資訊，請參閱[使用 Azure 和內部部署 VPN 實作混合式網路架構][guidance-vpn-gateway-availability]和[使用 Azure ExpressRoute 實作混合式網路架構][guidance-expressroute-availability]中的可用性考量。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-212">For specific information on maintaining availability for VPN and ExpressRoute connections, see the availability considerations in [Implementing a hybrid network architecture with Azure and on-premises VPN][guidance-vpn-gateway-availability] and [Implementing a hybrid network architecture with Azure ExpressRoute][guidance-expressroute-availability].</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="6a6f4-213">管理性考量</span><span class="sxs-lookup"><span data-stu-id="6a6f4-213">Manageability considerations</span></span>

<span data-ttu-id="6a6f4-214">應只有管理子網路中的 jumpbox 才能執行所有應用程式和資源的監視。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-214">All application and resource monitoring should be performed by the jumpbox in the management subnet.</span></span> <span data-ttu-id="6a6f4-215">根據您的應用程式需求，您可能需要管理子網路中的其他監視資源。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-215">Depending on your application requirements, you may need additional monitoring resources in the management subnet.</span></span> <span data-ttu-id="6a6f4-216">如果是的話，應透過 jumpbox 來存取這些資源。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-216">If so, these resources should be accessed through the jumpbox.</span></span>

<span data-ttu-id="6a6f4-217">如果從內部部署網路到 Azure 的閘道連線關閉，藉由部署公用 IP 位址、將它加入 jumpbox、然後從網際網路進行遠端登入，仍可連線至 jumpbox。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-217">If gateway connectivity from your on-premises network to Azure is down, you can still reach the jumpbox by deploying a public IP address, adding it to the jumpbox, and remoting in from the internet.</span></span>

<span data-ttu-id="6a6f4-218">NSG 規則會保護參考架構中每一層的子網路。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-218">Each tier's subnet in the reference architecture is protected by NSG rules.</span></span> <span data-ttu-id="6a6f4-219">您可能需要建立規則來開啟連接埠 3389，以便在 Windows 虛擬機器上使用遠端桌面通訊協定 (RDP) 的存取，或開啟連接埠 22，以便在 Linux 虛擬機器上使用安全殼層 (SSH) 的存取。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-219">You may need to create a rule to open port 3389 for remote desktop protocol (RDP) access on Windows VMs or port 22 for secure shell (SSH) access on Linux VMs.</span></span> <span data-ttu-id="6a6f4-220">其他管理和監視工具可能需要規則來開啟其他連接埠。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-220">Other management and monitoring tools may require rules to open additional ports.</span></span>

<span data-ttu-id="6a6f4-221">如果您使用 ExpressRoute 來提供您內部部署資料中心與 Azure 之間的連線，請使用 [Azure 連線工具組 (AzureCT)][azurect] 來監視連線問題並進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-221">If you're using ExpressRoute to provide the connectivity between your on-premises datacenter and Azure, use the [Azure Connectivity Toolkit (AzureCT)][azurect] to monitor and troubleshoot connection issues.</span></span>

<span data-ttu-id="6a6f4-222">您可以在[使用 Azure 和內部部署 VPN 實作混合式網路架構][guidance-vpn-gateway-manageability]和[使用 Azure ExpressRoute 實作混合式網路架構][guidance-expressroute-manageability]的文章中，找到特別針對監視和管理 VPN 與 ExpressRoute 連線的其他資訊。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-222">You can find additional information specifically aimed at monitoring and managing VPN and ExpressRoute connections in the articles [Implementing a hybrid network architecture with Azure and on-premises VPN][guidance-vpn-gateway-manageability] and [Implementing a hybrid network architecture with Azure ExpressRoute][guidance-expressroute-manageability].</span></span>

## <a name="security-considerations"></a><span data-ttu-id="6a6f4-223">安全性考量</span><span class="sxs-lookup"><span data-stu-id="6a6f4-223">Security considerations</span></span>

<span data-ttu-id="6a6f4-224">此參考架構實作多個安全性層級。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-224">This reference architecture implements multiple levels of security.</span></span>

### <a name="routing-all-on-premises-user-requests-through-the-nva"></a><span data-ttu-id="6a6f4-225">透過 NVA 路由所有內部部署使用者要求</span><span class="sxs-lookup"><span data-stu-id="6a6f4-225">Routing all on-premises user requests through the NVA</span></span>

<span data-ttu-id="6a6f4-226">閘道子網路中的 UDR 會封鎖不是從內部部署收到的所有使用者要求。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-226">The UDR in the gateway subnet blocks all user requests other than those received from on-premises.</span></span> <span data-ttu-id="6a6f4-227">UDR 會將允許的要求傳遞至私人 DMZ 子網路中的 NVA，而且如果 NVA 規則允許的話，這些要求會繼續傳遞至應用程式。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-227">The UDR passes allowed requests to the NVAs in the private DMZ subnet, and these requests are passed on to the application if they are allowed by the NVA rules.</span></span> <span data-ttu-id="6a6f4-228">您可以將其他路由新增至 UDR，但請確定路由不會不小心略過 NVA，或是封鎖要傳向管理子網路的系統管理流量。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-228">You can add other routes to the UDR, but make sure they don't inadvertently bypass the NVAs or block administrative traffic intended for the management subnet.</span></span>

<span data-ttu-id="6a6f4-229">NVA 前方的負載平衡器也可以當做安全性裝置，如果流量不在負載平衡規則中開啟的連接埠上，則將其略過。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-229">The load balancer in front of the NVAs also acts as a security device by ignoring traffic on ports that are not open in the load balancing rules.</span></span> <span data-ttu-id="6a6f4-230">參考架構中的負載平衡器只會接聽連接埠 80 上的 HTTP 要求和連接埠 443 上的 HTTPS 要求。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-230">The load balancers in the reference architecture only listen for HTTP requests on port 80 and HTTPS requests on port 443.</span></span> <span data-ttu-id="6a6f4-231">記錄您新增至負載平衡器的任何其他規則，並監視流量以確保沒有安全性問題。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-231">Document any additional rules that you add to the load balancers, and monitor traffic to ensure there are no security issues.</span></span>

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a><span data-ttu-id="6a6f4-232">使用 NSG 封鎖/傳遞應用程式層之間的流量</span><span class="sxs-lookup"><span data-stu-id="6a6f4-232">Using NSGs to block/pass traffic between application tiers</span></span>

<span data-ttu-id="6a6f4-233">使用 NSG 限制各層之間的流量。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-233">Traffic between tiers is restricted by using NSGs.</span></span> <span data-ttu-id="6a6f4-234">商務層會封鎖不是源自於 Web 層的所有流量，而資料層會封鎖所有不是源自於商務層的所有流量。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-234">The business tier blocks all traffic that doesn't originate in the web tier, and the data tier blocks all traffic that doesn't originate in the business tier.</span></span> <span data-ttu-id="6a6f4-235">如果您需要擴充 NSG 規則以讓這些層接受更廣泛的存取，請評估這些需求的安全性風險。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-235">If you have a requirement to expand the NSG rules to allow broader access to these tiers, weigh these requirements against the security risks.</span></span> <span data-ttu-id="6a6f4-236">每個新的輸入路徑都代表著有機會發生意外或有意的資料外洩或應用程式損害。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-236">Each new inbound pathway represents an opportunity for accidental or purposeful data leakage or application damage.</span></span>

### <a name="devops-access"></a><span data-ttu-id="6a6f4-237">DevOps 存取</span><span class="sxs-lookup"><span data-stu-id="6a6f4-237">DevOps access</span></span>

<span data-ttu-id="6a6f4-238">使用 [RBAC][rbac] 限制 DevOps 可在每一層執行的作業。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-238">Use [RBAC][rbac] to restrict the operations that DevOps can perform on each tier.</span></span> <span data-ttu-id="6a6f4-239">授與權限時，請使用[最小權限的原則][security-principle-of-least-privilege]。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-239">When granting permissions, use the [principle of least privilege][security-principle-of-least-privilege].</span></span> <span data-ttu-id="6a6f4-240">記錄所有的系統管理作業並定期執行稽核，以確保所有設定變更都是經過規劃的。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-240">Log all administrative operations and perform regular audits to ensure any configuration changes were planned.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="6a6f4-241">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="6a6f4-241">Deploy the solution</span></span>

<span data-ttu-id="6a6f4-242">在 [GitHub][github-folder] 中有實作這些建議的參考架構部署。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-242">A deployment for a reference architecture that implements these recommendations is available on [GitHub][github-folder].</span></span>

### <a name="prerequisites"></a><span data-ttu-id="6a6f4-243">必要條件</span><span class="sxs-lookup"><span data-stu-id="6a6f4-243">Prerequisites</span></span>

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-resources"></a><span data-ttu-id="6a6f4-244">部署資源</span><span class="sxs-lookup"><span data-stu-id="6a6f4-244">Deploy resources</span></span>

1. <span data-ttu-id="6a6f4-245">瀏覽至參考架構 GitHub 存放庫的 `/dmz/secure-vnet-hybrid` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-245">Navigate to the `/dmz/secure-vnet-hybrid` folder of the reference architectures GitHub repository.</span></span>

2. <span data-ttu-id="6a6f4-246">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6a6f4-246">Run the following command:</span></span>

    ```bash
    azbb -s <subscription_id> -g <resource_group_name> -l <region> -p onprem.json --deploy
    ```

3. <span data-ttu-id="6a6f4-247">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6a6f4-247">Run the following command:</span></span>

    ```bash
    azbb -s <subscription_id> -g <resource_group_name> -l <region> -p secure-vnet-hybrid.json --deploy
    ```

### <a name="connect-the-on-premises-and-azure-gateways"></a><span data-ttu-id="6a6f4-248">將內部部署連線到 Azure 閘道</span><span class="sxs-lookup"><span data-stu-id="6a6f4-248">Connect the on-premises and Azure gateways</span></span>

<span data-ttu-id="6a6f4-249">在此步驟中，您會將兩個區域網路閘道連線。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-249">In this step, you will connect the two local network gateways.</span></span>

1. <span data-ttu-id="6a6f4-250">在 Azure 入口網站中，巡覽至您所建立的資源群組。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-250">In the Azure Portal, navigate to the resource group that you created.</span></span>

2. <span data-ttu-id="6a6f4-251">尋找名為 `ra-vpn-vgw-pip` 的資源，並複製 [概觀] 刀鋒視窗中所顯示的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-251">Find the resource named `ra-vpn-vgw-pip` and copy the IP address shown in the **Overview** blade.</span></span>

3. <span data-ttu-id="6a6f4-252">尋找名為 `onprem-vpn-lgw` 的資源。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-252">Find the resource named `onprem-vpn-lgw`.</span></span>

4. <span data-ttu-id="6a6f4-253">按一下 [設定] 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-253">Click the **Configuration** blade.</span></span> <span data-ttu-id="6a6f4-254">在 [IP 位址] 底下，貼上步驟 2 中的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-254">Under **IP address**, paste in the IP address from step 2.</span></span>

    ![IP 位址欄位的螢幕擷取畫面](./images/local-net-gw.png)

5. <span data-ttu-id="6a6f4-256">按一下 [儲存]，並等候作業完成。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-256">Click **Save** and wait for the operation to complete.</span></span> <span data-ttu-id="6a6f4-257">可能需要大約 5 分鐘的時間。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-257">It can take about 5 minutes.</span></span>

6. <span data-ttu-id="6a6f4-258">尋找名為 `onprem-vpn-gateway1-pip` 的資源。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-258">Find the resource named `onprem-vpn-gateway1-pip`.</span></span> <span data-ttu-id="6a6f4-259">複製 [概觀] 刀鋒視窗中所顯示的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-259">Copy the IP address shown in the **Overview** blade.</span></span>

7. <span data-ttu-id="6a6f4-260">尋找名為 `ra-vpn-lgw` 的資源。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-260">Find the resource named `ra-vpn-lgw`.</span></span>

8. <span data-ttu-id="6a6f4-261">按一下 [設定] 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-261">Click the **Configuration** blade.</span></span> <span data-ttu-id="6a6f4-262">在 [IP 位址] 底下，貼上步驟 6 中的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-262">Under **IP address**, paste in the IP address from step 6.</span></span>

9. <span data-ttu-id="6a6f4-263">按一下 [儲存]，並等候作業完成。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-263">Click **Save** and wait for the operation to complete.</span></span>

10. <span data-ttu-id="6a6f4-264">若要確認連線，請前往每個閘道的 [連線] 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-264">To verify the connection, go to the **Connections** blade for each gateway.</span></span> <span data-ttu-id="6a6f4-265">狀態應該是 [已連線]。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-265">The status should be **Connected**.</span></span>

### <a name="verify-that-network-traffic-reaches-the-web-tier"></a><span data-ttu-id="6a6f4-266">請確認網路流量有到達 Web 層</span><span class="sxs-lookup"><span data-stu-id="6a6f4-266">Verify that network traffic reaches the web tier</span></span>

1. <span data-ttu-id="6a6f4-267">在 Azure 入口網站中，巡覽至您所建立的資源群組。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-267">In the Azure Portal, navigate to the resource group that you created.</span></span>

2. <span data-ttu-id="6a6f4-268">尋找名為 `int-dmz-lb` 的資源，也就是私人 DMZ 前方的負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-268">Find the resource named `int-dmz-lb`, which is the load balancer in front of the private DMZ.</span></span> <span data-ttu-id="6a6f4-269">複製 [概觀] 刀鋒視窗中的私人 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-269">Copy the private IP address from the **Overview** blade.</span></span>

3. <span data-ttu-id="6a6f4-270">尋找名為 `jb-vm1` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-270">Find the VM named `jb-vm1`.</span></span> <span data-ttu-id="6a6f4-271">請按一下 [連線]，並使用遠端桌面連線到 VM。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-271">Click **Connect** and use Remote Desktop to connect to the VM.</span></span> <span data-ttu-id="6a6f4-272">使用者名稱與密碼會在 onprem.json 檔案中指定。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-272">The user name and password are specified in the onprem.json file.</span></span>

4. <span data-ttu-id="6a6f4-273">從遠端桌面工作階段中，開啟網頁瀏覽器並巡覽至步驟 2 中的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-273">From the Remote Desktop Session, open a web browser and navigate to the IP address from step 2.</span></span> <span data-ttu-id="6a6f4-274">您應該會看到預設的 Apache2 伺服器首頁。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-274">You should see the default Apache2 server home page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6a6f4-275">後續步驟</span><span class="sxs-lookup"><span data-stu-id="6a6f4-275">Next steps</span></span>

- <span data-ttu-id="6a6f4-276">了解如何[在 Azure 與網際網路之間實作 DMZ](./secure-vnet-dmz.md)。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-276">Learn how to implement a [DMZ between Azure and the Internet](./secure-vnet-dmz.md).</span></span>
- <span data-ttu-id="6a6f4-277">了解如何實作[高可用性的混合式網路架構][ra-vpn-failover]。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-277">Learn how to implement a [highly available hybrid network architecture][ra-vpn-failover].</span></span>
- <span data-ttu-id="6a6f4-278">如需有關使用 Azure 管理網路安全性的詳細資訊，請參閱 [Microsoft 雲端服務和網路安全性][cloud-services-network-security]。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-278">For more information about managing network security with Azure, see [Microsoft cloud services and network security][cloud-services-network-security].</span></span>
- <span data-ttu-id="6a6f4-279">如需在 Azure 中保護資源的詳細資訊，請參閱[開始使用 Microsoft Azure 安全性][getting-started-with-azure-security]。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-279">For detailed information about protecting resources in Azure, see [Getting started with Microsoft Azure security][getting-started-with-azure-security].</span></span>
- <span data-ttu-id="6a6f4-280">若要深入了解如何解決 Azure 閘道連線上的安全性考量，請參閱[使用 Azure 和內部部署 VPN 實作混合式網路架構][guidance-vpn-gateway-security]和[使用 Azure ExpressRoute 實作混合式網路架構][guidance-expressroute-security]。</span><span class="sxs-lookup"><span data-stu-id="6a6f4-280">For additional details on addressing security concerns across an Azure gateway connection, see [Implementing a hybrid network architecture with Azure and on-premises VPN][guidance-vpn-gateway-security] and [Implementing a hybrid network architecture with Azure ExpressRoute][guidance-expressroute-security].</span></span>
- [<span data-ttu-id="6a6f4-281">針對 Azure 中的網路虛擬設備問題進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="6a6f4-281">Troubleshoot network virtual appliance issues in Azure</span></span>](/azure/virtual-network/virtual-network-troubleshoot-nva)

<!-- links -->

[availability-set]: /azure/virtual-machines/virtual-machines-windows-create-availability-set
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[azure-forced-tunneling]: https://azure.microsoft.com/en-gb/documentation/articles/vpn-gateway-forced-tunneling-rm/
[azure-marketplace-nva]: https://azuremarketplace.microsoft.com/marketplace/apps/category/networking
[cloud-services-network-security]: /azure/best-practices-network-security
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

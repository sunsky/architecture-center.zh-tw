---
title: 使用 ExpressRoute 將內部部署網路連線至 Azure
titleSuffix: Azure Reference Architectures
description: 在透過 Azure ExpressRoute 連線的 Azure 虛擬網路與內部部署網路間，實作安全的站對站網路架構。
author: telmosampaio
ms.date: 10/22/2017
ms.custom: seodec18
ms.openlocfilehash: 8e9de168fe2969159f62ce84a19f4b21fd1cb538
ms.sourcegitcommit: 88a68c7e9b6b772172b7faa4b9fd9c061a9f7e9d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53120385"
---
# <a name="connect-an-on-premises-network-to-azure-using-expressroute"></a><span data-ttu-id="68a04-103">使用 ExpressRoute 將內部部署網路連線至 Azure</span><span class="sxs-lookup"><span data-stu-id="68a04-103">Connect an on-premises network to Azure using ExpressRoute</span></span>

<span data-ttu-id="68a04-104">此參考架構會示範如何使用 [Azure ExpressRoute][expressroute-introduction] 將內部部署網路連線至 Azure 上的虛擬網路。</span><span class="sxs-lookup"><span data-stu-id="68a04-104">This reference architecture shows how to connect an on-premises network to virtual networks on Azure, using [Azure ExpressRoute][expressroute-introduction].</span></span> <span data-ttu-id="68a04-105">ExpressRoute 連線會透過第三方連線提供者，使用私人的專用連線。</span><span class="sxs-lookup"><span data-stu-id="68a04-105">ExpressRoute connections use a private, dedicated connection through a third-party connectivity provider.</span></span> <span data-ttu-id="68a04-106">私人連線會將內部部署網路延伸到 Azure。</span><span class="sxs-lookup"><span data-stu-id="68a04-106">The private connection extends your on-premises network into Azure.</span></span> <span data-ttu-id="68a04-107">[**部署這個解決方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="68a04-107">[**Deploy this solution**](#deploy-the-solution).</span></span>

<span data-ttu-id="68a04-108">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="68a04-108">![[0]][0]</span></span>

<span data-ttu-id="68a04-109">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="68a04-109">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="68a04-110">架構</span><span class="sxs-lookup"><span data-stu-id="68a04-110">Architecture</span></span>

<span data-ttu-id="68a04-111">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="68a04-111">The architecture consists of the following components.</span></span>

- <span data-ttu-id="68a04-112">**內部部署的公司網路**。</span><span class="sxs-lookup"><span data-stu-id="68a04-112">**On-premises corporate network**.</span></span> <span data-ttu-id="68a04-113">在組織內執行的私用區域網路。</span><span class="sxs-lookup"><span data-stu-id="68a04-113">A private local-area network running within an organization.</span></span>

- <span data-ttu-id="68a04-114">**ExpressRoute 線路**。</span><span class="sxs-lookup"><span data-stu-id="68a04-114">**ExpressRoute circuit**.</span></span> <span data-ttu-id="68a04-115">透過邊緣路由器聯結內部部署網路與 Azure 之連線提供者所提供的第 2 層或第 3 層線路。</span><span class="sxs-lookup"><span data-stu-id="68a04-115">A layer 2 or layer 3 circuit supplied by the connectivity provider that joins the on-premises network with Azure through the edge routers.</span></span> <span data-ttu-id="68a04-116">此線路使用連線提供者所管理的硬體基礎結構。</span><span class="sxs-lookup"><span data-stu-id="68a04-116">The circuit uses the hardware infrastructure managed by the connectivity provider.</span></span>

- <span data-ttu-id="68a04-117">**本機邊緣路由器**。</span><span class="sxs-lookup"><span data-stu-id="68a04-117">**Local edge routers**.</span></span> <span data-ttu-id="68a04-118">該路由器會將內部部署網路連線至提供者管理的線路。</span><span class="sxs-lookup"><span data-stu-id="68a04-118">Routers that connect the on-premises network to the circuit managed by the provider.</span></span> <span data-ttu-id="68a04-119">根據您佈建連線的方式，您可能需要提供路由器使用的公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="68a04-119">Depending on how your connection is provisioned, you may need to provide the public IP addresses used by the routers.</span></span>
- <span data-ttu-id="68a04-120">**Microsoft 邊緣路由器**。</span><span class="sxs-lookup"><span data-stu-id="68a04-120">**Microsoft edge routers**.</span></span> <span data-ttu-id="68a04-121">使用主動-主動高可用性設定的兩個路由器。</span><span class="sxs-lookup"><span data-stu-id="68a04-121">Two routers in an active-active highly available configuration.</span></span> <span data-ttu-id="68a04-122">這類路由器可讓連線提供者將他們的線路直接連線至資料中心。</span><span class="sxs-lookup"><span data-stu-id="68a04-122">These routers enable a connectivity provider to connect their circuits directly to their datacenter.</span></span> <span data-ttu-id="68a04-123">根據您佈建連線的方式，您可能需要提供路由器使用的公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="68a04-123">Depending on how your connection is provisioned, you may need to provide the public IP addresses used by the routers.</span></span>

- <span data-ttu-id="68a04-124">**Azure 虛擬網路 (VNet)**。</span><span class="sxs-lookup"><span data-stu-id="68a04-124">**Azure virtual networks (VNets)**.</span></span> <span data-ttu-id="68a04-125">每個 VNet 都位於單一 Azure 區域，而且可以裝載多個應用程式層。</span><span class="sxs-lookup"><span data-stu-id="68a04-125">Each VNet resides in a single Azure region, and can host multiple application tiers.</span></span> <span data-ttu-id="68a04-126">應用程式層可以使用每個 VNet 中的子網路區隔。</span><span class="sxs-lookup"><span data-stu-id="68a04-126">Application tiers can be segmented using subnets in each VNet.</span></span>

- <span data-ttu-id="68a04-127">**Azure 公用服務**。</span><span class="sxs-lookup"><span data-stu-id="68a04-127">**Azure public services**.</span></span> <span data-ttu-id="68a04-128">可以在混合式應用程式中使用的 Azure 服務。</span><span class="sxs-lookup"><span data-stu-id="68a04-128">Azure services that can be used within a hybrid application.</span></span> <span data-ttu-id="68a04-129">這些服務可透過網際網路存取，但使用 ExpressRoute 線路存取這些服務可提供低延遲且更能預測的效能，因為流量不會進出網際網路。</span><span class="sxs-lookup"><span data-stu-id="68a04-129">These services are also available over the Internet, but accessing them using an ExpressRoute circuit provides low latency and more predictable performance, because traffic does not go through the Internet.</span></span> <span data-ttu-id="68a04-130">連線會透過您組織所擁有或連線提供者提供的位址，使用[公用對等互連][expressroute-peering]來執行。</span><span class="sxs-lookup"><span data-stu-id="68a04-130">Connections are performed using [public peering][expressroute-peering], with addresses that are either owned by your organization or supplied by your connectivity provider.</span></span>

- <span data-ttu-id="68a04-131">**Office 365 服務**。</span><span class="sxs-lookup"><span data-stu-id="68a04-131">**Office 365 services**.</span></span> <span data-ttu-id="68a04-132">由 Microsoft 所提供可公開使用的 Office 365 應用程式和服務。</span><span class="sxs-lookup"><span data-stu-id="68a04-132">The publicly available Office 365 applications and services provided by Microsoft.</span></span> <span data-ttu-id="68a04-133">連線會透過您組織所擁有或連線提供者提供的位址，使用[Microsoft 對等互連][expressroute-peering]來執行。</span><span class="sxs-lookup"><span data-stu-id="68a04-133">Connections are performed using [Microsoft peering][expressroute-peering], with addresses that are either owned by your organization or supplied by your connectivity provider.</span></span> <span data-ttu-id="68a04-134">您也可以透過 Microsoft 對等互連直接連線至 Microsoft CRM Online。</span><span class="sxs-lookup"><span data-stu-id="68a04-134">You can also connect directly to Microsoft CRM Online through Microsoft peering.</span></span>

- <span data-ttu-id="68a04-135">**連線提供者** (未顯示)。</span><span class="sxs-lookup"><span data-stu-id="68a04-135">**Connectivity providers** (not shown).</span></span> <span data-ttu-id="68a04-136">使用第 2 層和第 3 層連線，在您資料中心和 Azure 資料中心之間提供連線的公司。</span><span class="sxs-lookup"><span data-stu-id="68a04-136">Companies that provide a connection either using layer 2 or layer 3 connectivity between your datacenter and an Azure datacenter.</span></span>

## <a name="recommendations"></a><span data-ttu-id="68a04-137">建議</span><span class="sxs-lookup"><span data-stu-id="68a04-137">Recommendations</span></span>

<span data-ttu-id="68a04-138">下列建議適用於大部分的案例。</span><span class="sxs-lookup"><span data-stu-id="68a04-138">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="68a04-139">除非您有特定的需求會覆寫它們，否則請遵循下列建議。</span><span class="sxs-lookup"><span data-stu-id="68a04-139">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="connectivity-providers"></a><span data-ttu-id="68a04-140">連線提供者</span><span class="sxs-lookup"><span data-stu-id="68a04-140">Connectivity providers</span></span>

<span data-ttu-id="68a04-141">根據您的所在位置，選取適當的 ExpressRoute 連線提供者。</span><span class="sxs-lookup"><span data-stu-id="68a04-141">Select a suitable ExpressRoute connectivity provider for your location.</span></span> <span data-ttu-id="68a04-142">若要取得您所在位置可用的連線提供者清單，請使用下列 Azure PowerShell 命令：</span><span class="sxs-lookup"><span data-stu-id="68a04-142">To get a list of connectivity providers available at your location, use the following Azure PowerShell command:</span></span>

```powershell
Get-AzureRmExpressRouteServiceProvider
```

<span data-ttu-id="68a04-143">ExpressRoute 連線提供者會使用下列方法將您的資料中心連線至 Microsoft：</span><span class="sxs-lookup"><span data-stu-id="68a04-143">ExpressRoute connectivity providers connect your datacenter to Microsoft in the following ways:</span></span>

- <span data-ttu-id="68a04-144">**共置於雲端 Exchange**。</span><span class="sxs-lookup"><span data-stu-id="68a04-144">**Co-located at a cloud exchange**.</span></span> <span data-ttu-id="68a04-145">如果您共置於具有雲端 Exchange 的設施中，您可以訂購虛擬交叉連線，透過共置提供者的乙太網路交換而連線至 Azure。</span><span class="sxs-lookup"><span data-stu-id="68a04-145">If you're co-located in a facility with a cloud exchange, you can order virtual cross-connections to Azure through the co-location provider’s Ethernet exchange.</span></span> <span data-ttu-id="68a04-146">共置提供者可以在您於共置設施中的基礎結構與 Azure 之間，提供第 2 層交叉連線或受控第 3 層交叉連線。</span><span class="sxs-lookup"><span data-stu-id="68a04-146">Co-location providers can offer either layer 2 cross-connections, or managed layer 3 cross-connections between your infrastructure in the co-location facility and Azure.</span></span>
- <span data-ttu-id="68a04-147">**點對點乙太網路連線**。</span><span class="sxs-lookup"><span data-stu-id="68a04-147">**Point-to-point Ethernet connections**.</span></span> <span data-ttu-id="68a04-148">您可以透過點對點乙太網路連結，將內部部署資料中心/辦公室連線到 Azure。</span><span class="sxs-lookup"><span data-stu-id="68a04-148">You can connect your on-premises datacenters/offices to Azure through point-to-point Ethernet links.</span></span> <span data-ttu-id="68a04-149">點對點乙太網路提供者可以在您的網路與 Azure 之間，提供第 2 層連線或受控第 3 層連線。</span><span class="sxs-lookup"><span data-stu-id="68a04-149">Point-to-point Ethernet providers can offer layer 2 connections, or managed layer 3 connections between your site and Azure.</span></span>
- <span data-ttu-id="68a04-150">**任意點對任意點 (IPVPN) 網路**。</span><span class="sxs-lookup"><span data-stu-id="68a04-150">**Any-to-any (IPVPN) networks**.</span></span> <span data-ttu-id="68a04-151">您可以將您的廣域網路 (WAN) 整合至 Azure。</span><span class="sxs-lookup"><span data-stu-id="68a04-151">You can integrate your wide area network (WAN) with Azure.</span></span> <span data-ttu-id="68a04-152">網際網路通訊協定虛擬私人網路 (IPVPN) 提供者 (通常是多重通訊協定標籤交換 VPN) 可在您的分公司與資料中心之間提供任意點對任意點連線。</span><span class="sxs-lookup"><span data-stu-id="68a04-152">Internet protocol virtual private network (IPVPN) providers (typically a multiprotocol label switching VPN) offer any-to-any connectivity between your branch offices and datacenters.</span></span> <span data-ttu-id="68a04-153">Azure 可以相互連線到您的 WAN，看起來就像任何其他分公司一樣。</span><span class="sxs-lookup"><span data-stu-id="68a04-153">Azure can be interconnected to your WAN to make it look just like any other branch office.</span></span> <span data-ttu-id="68a04-154">WAN 提供者通常會提供受控第 3 層連線能力。</span><span class="sxs-lookup"><span data-stu-id="68a04-154">WAN providers typically offer managed layer 3 connectivity.</span></span>

<span data-ttu-id="68a04-155">如需連線提供者的詳細資訊，請參閱 [ExpressRoute 簡介][expressroute-introduction]。</span><span class="sxs-lookup"><span data-stu-id="68a04-155">For more information about connectivity providers, see the [ExpressRoute introduction][expressroute-introduction].</span></span>

### <a name="expressroute-circuit"></a><span data-ttu-id="68a04-156">ExpressRoute 線路</span><span class="sxs-lookup"><span data-stu-id="68a04-156">ExpressRoute circuit</span></span>

<span data-ttu-id="68a04-157">請確認您的組織符合 [ExpressRoute 必要條件需求][expressroute-prereqs]以連線至 Azure。</span><span class="sxs-lookup"><span data-stu-id="68a04-157">Ensure that your organization has met the [ExpressRoute prerequisite requirements][expressroute-prereqs] for connecting to Azure.</span></span>

<span data-ttu-id="68a04-158">如果您尚未這麼做，請將名為 `GatewaySubnet` 的子網路新增至 Azure VNet，並使用 Azure VPN 閘道服務建立 ExpressRoute 虛擬網路閘道。</span><span class="sxs-lookup"><span data-stu-id="68a04-158">If you haven't already done so, add a subnet named `GatewaySubnet` to your Azure VNet and create an ExpressRoute virtual network gateway using the Azure VPN gateway service.</span></span> <span data-ttu-id="68a04-159">如需詳細資訊，請參閱 [線路佈建和線路狀態的 ExpressRoute 工作流程][ExpressRoute-provisioning]。</span><span class="sxs-lookup"><span data-stu-id="68a04-159">For more information about this process, see [ExpressRoute workflows for circuit provisioning and circuit states][ExpressRoute-provisioning].</span></span>

<span data-ttu-id="68a04-160">建立 ExpressRoute 線路，如下所示：</span><span class="sxs-lookup"><span data-stu-id="68a04-160">Create an ExpressRoute circuit as follows:</span></span>

1. <span data-ttu-id="68a04-161">執行下列 PowerShell 命令：</span><span class="sxs-lookup"><span data-stu-id="68a04-161">Run the following PowerShell command:</span></span>

    ```powershell
    New-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>> -Location <<location>> -SkuTier <<sku-tier>> -SkuFamily <<sku-family>> -ServiceProviderName <<service-provider-name>> -PeeringLocation <<peering-location>> -BandwidthInMbps <<bandwidth-in-mbps>>
    ```
2. <span data-ttu-id="68a04-162">將新線路的 `ServiceKey` 傳送給服務提供者。</span><span class="sxs-lookup"><span data-stu-id="68a04-162">Send the `ServiceKey` for the new circuit to the service provider.</span></span>

3. <span data-ttu-id="68a04-163">等候提供者佈建線路。</span><span class="sxs-lookup"><span data-stu-id="68a04-163">Wait for the provider to provision the circuit.</span></span> <span data-ttu-id="68a04-164">若要確認線路的佈建狀態，請執行下列 PowerShell 命令：</span><span class="sxs-lookup"><span data-stu-id="68a04-164">To verify the provisioning state of a circuit, run the following PowerShell command:</span></span>

    ```powershell
    Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
    ```

    <span data-ttu-id="68a04-165">當線路就緒時，在輸出的 `Service Provider` 區段中，`Provisioning state` 欄位會從 `NotProvisioned` 變更為 `Provisioned`。</span><span class="sxs-lookup"><span data-stu-id="68a04-165">The `Provisioning state` field in the `Service Provider` section of the output will change from `NotProvisioned` to `Provisioned` when the circuit is ready.</span></span>

    > [!NOTE]
    > <span data-ttu-id="68a04-166">如果您使用第 3 層連線，提供者應為您設定及管理路由。</span><span class="sxs-lookup"><span data-stu-id="68a04-166">If you're using a layer 3 connection, the provider should configure and manage routing for you.</span></span> <span data-ttu-id="68a04-167">您須提供必要資訊，讓提供者實作適當的路由。</span><span class="sxs-lookup"><span data-stu-id="68a04-167">You provide the information necessary to enable the provider to implement the appropriate routes.</span></span>
    >
    >

4. <span data-ttu-id="68a04-168">如果您使用第 2 層連線：</span><span class="sxs-lookup"><span data-stu-id="68a04-168">If you're using a layer 2 connection:</span></span>

    1. <span data-ttu-id="68a04-169">針對您要實作的每個對等互連類型，保留兩個由有效公用 IP 位址組成的 /30 子網路。</span><span class="sxs-lookup"><span data-stu-id="68a04-169">Reserve two /30 subnets composed of valid public IP addresses for each type of peering you want to implement.</span></span> <span data-ttu-id="68a04-170">這些 /30 子網路將用來提供線路所需的路由器 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="68a04-170">These /30 subnets will be used to provide IP addresses for the routers used for the circuit.</span></span> <span data-ttu-id="68a04-171">如果您實作私人、公用及 Microsoft 對等互連，您必須有 6 個包含有效公用 IP 位址的 /30 子網路。</span><span class="sxs-lookup"><span data-stu-id="68a04-171">If you are implementing private, public, and Microsoft peering, you'll need 6 /30 subnets with valid public IP addresses.</span></span>

    2. <span data-ttu-id="68a04-172">設定 ExpressRoute 線路的路由。</span><span class="sxs-lookup"><span data-stu-id="68a04-172">Configure routing for the ExpressRoute circuit.</span></span> <span data-ttu-id="68a04-173">針對您要設定的每個對等互連類型 (私人、公用和 Microsoft)，執行下列 PowerShell 命令。</span><span class="sxs-lookup"><span data-stu-id="68a04-173">Run the following PowerShell commands for each type of peering you want to configure (private, public, and Microsoft).</span></span> <span data-ttu-id="68a04-174">如需詳細資訊，請參閱[建立和修改 ExpressRoute 路線的路由][configure-expressroute-routing]。</span><span class="sxs-lookup"><span data-stu-id="68a04-174">For more information, see [Create and modify routing for an ExpressRoute circuit][configure-expressroute-routing].</span></span>

        ```powershell
        Set-AzureRmExpressRouteCircuitPeeringConfig -Name <<peering-name>> -Circuit <<circuit-name>> -PeeringType <<peering-type>> -PeerASN <<peer-asn>> -PrimaryPeerAddressPrefix <<primary-peer-address-prefix>> -SecondaryPeerAddressPrefix <<secondary-peer-address-prefix>> -VlanId <<vlan-id>>

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit <<circuit-name>>
        ```

    3. <span data-ttu-id="68a04-175">保留另一個有效的公用 IP 位址集區，以便用於公用和 Microsoft 對等互連的網路位址轉譯 (NAT)。</span><span class="sxs-lookup"><span data-stu-id="68a04-175">Reserve another pool of valid public IP addresses to use for network address translation (NAT) for public and Microsoft peering.</span></span> <span data-ttu-id="68a04-176">建議您針對每個對等互連使用不同集區。</span><span class="sxs-lookup"><span data-stu-id="68a04-176">It is recommended to have a different pool for each peering.</span></span> <span data-ttu-id="68a04-177">為連線提供者指定集區，讓他們可以設定這些範圍的邊界閘道通訊協定 (BGP) 公告。</span><span class="sxs-lookup"><span data-stu-id="68a04-177">Specify the pool to your connectivity provider, so they can configure border gateway protocol (BGP) advertisements for those ranges.</span></span>

5. <span data-ttu-id="68a04-178">執行下列 PowerShell 命令，將您的私人 VNet 連結至 ExpressRoute 線路。</span><span class="sxs-lookup"><span data-stu-id="68a04-178">Run the following PowerShell commands to link your private VNet(s) to the ExpressRoute circuit.</span></span> <span data-ttu-id="68a04-179">如需詳細資訊，請參閱[將虛擬網路連結到 ExpressRoute 線路][link-vnet-to-expressroute]。</span><span class="sxs-lookup"><span data-stu-id="68a04-179">For more information,see [Link a virtual network to an ExpressRoute circuit][link-vnet-to-expressroute].</span></span>

    ```powershell
    $circuit = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
    $gw = Get-AzureRmVirtualNetworkGateway -Name <<gateway-name>> -ResourceGroupName <<resource-group>>
    New-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> -ResourceGroupName <<resource-group>> -Location <<location> -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
    ```

<span data-ttu-id="68a04-180">您可以將不同區域中的多個 VNet 連線至相同 ExpressRoute 線路，前提是所有 VNet 和 ExpressRoute 線路皆位於相同的地緣政治區域內。</span><span class="sxs-lookup"><span data-stu-id="68a04-180">You can connect multiple VNets located in different regions to the same ExpressRoute circuit, as long as all VNets and the ExpressRoute circuit are located within the same geopolitical region.</span></span>

### <a name="troubleshooting"></a><span data-ttu-id="68a04-181">疑難排解</span><span class="sxs-lookup"><span data-stu-id="68a04-181">Troubleshooting</span></span>

<span data-ttu-id="68a04-182">如果先前可運作的 ExpressRoute 線路現在無法連線，且內部部署或私人 VNet 內的設定皆沒有變更，則您可能需要連絡連線提供者，並與他們一起修正問題。</span><span class="sxs-lookup"><span data-stu-id="68a04-182">If a previously functioning ExpressRoute circuit now fails to connect, in the absence of any configuration changes on-premises or within your private VNet, you may need to contact the connectivity provider and work with them to correct the issue.</span></span> <span data-ttu-id="68a04-183">您可以使用下列 Powershell 命令來確認 ExpressRoute 線路是否已佈建：</span><span class="sxs-lookup"><span data-stu-id="68a04-183">Use the following Powershell commands to verify that the ExpressRoute circuit has been provisioned:</span></span>

```powershell
Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

<span data-ttu-id="68a04-184">此命令的輸出會顯示數個線路屬性，包括 `ProvisioningState`、`CircuitProvisioningState` 和 `ServiceProviderProvisioningState`，如下所示。</span><span class="sxs-lookup"><span data-stu-id="68a04-184">The output of this command shows several properties for your circuit, including `ProvisioningState`, `CircuitProvisioningState`, and `ServiceProviderProvisioningState` as shown below.</span></span>

```powershell
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : NotProvisioned
```

<span data-ttu-id="68a04-185">在您嘗試建立新的線路之後，如果 `ProvisioningState` 未設為 `Succeeded` ，請使用下列命令移除線路，然後嘗試重新建立。</span><span class="sxs-lookup"><span data-stu-id="68a04-185">If the `ProvisioningState` is not set to `Succeeded` after you tried to create a new circuit, remove the circuit by using the command below and try to create it again.</span></span>

```powershell
Remove-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

<span data-ttu-id="68a04-186">如果您的提供者已佈建線路，但 `ProvisioningState` 設為 `Failed`，或 `CircuitProvisioningState` 不是 `Enabled`，請連絡您的提供者，以取得進一步協助。</span><span class="sxs-lookup"><span data-stu-id="68a04-186">If your provider had already provisioned the circuit, and the `ProvisioningState` is set to `Failed`, or the `CircuitProvisioningState` is not `Enabled`, contact your provider for further assistance.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="68a04-187">延展性考量</span><span class="sxs-lookup"><span data-stu-id="68a04-187">Scalability considerations</span></span>

<span data-ttu-id="68a04-188">ExpressRoute 線路會在網路間提供高頻寬路徑。</span><span class="sxs-lookup"><span data-stu-id="68a04-188">ExpressRoute circuits provide a high bandwidth path between networks.</span></span> <span data-ttu-id="68a04-189">一般而言，頻寬較高，成本也會提高。</span><span class="sxs-lookup"><span data-stu-id="68a04-189">Generally, the higher the bandwidth the greater the cost.</span></span>

<span data-ttu-id="68a04-190">ExpressRoute 提供兩個[價格方案][expressroute-pricing]給客戶，計量方案和無限制資料方案。</span><span class="sxs-lookup"><span data-stu-id="68a04-190">ExpressRoute offers two [pricing plans][expressroute-pricing] to customers, a metered plan and an unlimited data plan.</span></span> <span data-ttu-id="68a04-191">費用會因為線路頻寬不同而有所差異。</span><span class="sxs-lookup"><span data-stu-id="68a04-191">Charges vary according to circuit bandwidth.</span></span> <span data-ttu-id="68a04-192">可用的頻寬也可能因為提供者不同而有所差異。</span><span class="sxs-lookup"><span data-stu-id="68a04-192">Available bandwidth will likely vary from provider to provider.</span></span> <span data-ttu-id="68a04-193">使用 `Get-AzureRmExpressRouteServiceProvider` Cmdlet 可查看您區域中可用的提供者，以及他們所提供的頻寬。</span><span class="sxs-lookup"><span data-stu-id="68a04-193">Use the `Get-AzureRmExpressRouteServiceProvider` cmdlet to see the providers available in your region and the bandwidths that they offer.</span></span>

<span data-ttu-id="68a04-194">單一 ExpressRoute 線路可以支援特定數量的對等互連和 VNet 連結。</span><span class="sxs-lookup"><span data-stu-id="68a04-194">A single ExpressRoute circuit can support a certain number of peerings and VNet links.</span></span> <span data-ttu-id="68a04-195">如需詳細資訊，請參閱 [ExpressRoute 限制](/azure/azure-subscription-service-limits)。</span><span class="sxs-lookup"><span data-stu-id="68a04-195">See [ExpressRoute limits](/azure/azure-subscription-service-limits) for more information.</span></span>

<span data-ttu-id="68a04-196">包含在額外費用中的 ExpressRoute 進階附加元件會提供一些額外的功能：</span><span class="sxs-lookup"><span data-stu-id="68a04-196">For an extra charge, the ExpressRoute Premium add-on provides some additional capability:</span></span>

- <span data-ttu-id="68a04-197">提高公開和私人對等互連的路由限制數量。</span><span class="sxs-lookup"><span data-stu-id="68a04-197">Increased route limits for public and private peering.</span></span>
- <span data-ttu-id="68a04-198">提高每個 ExpressRoute 線路的 VNet 連結數目。</span><span class="sxs-lookup"><span data-stu-id="68a04-198">Increased number of VNet links per ExpressRoute circuit.</span></span>
- <span data-ttu-id="68a04-199">從全球連線到服務。</span><span class="sxs-lookup"><span data-stu-id="68a04-199">Global connectivity for services.</span></span>

<span data-ttu-id="68a04-200">如需詳細資訊，請參閱 [ExpressRoute 價格][expressroute-pricing]。</span><span class="sxs-lookup"><span data-stu-id="68a04-200">See [ExpressRoute pricing][expressroute-pricing] for details.</span></span>

<span data-ttu-id="68a04-201">在 ExpressRoute 線路的設計中，可允許暫時性網路的頻寬突增為所採購頻寬限制的兩倍，且無需另外付費。</span><span class="sxs-lookup"><span data-stu-id="68a04-201">ExpressRoute circuits are designed to allow temporary network bursts up to two times the bandwidth limit that you procured for no additional cost.</span></span> <span data-ttu-id="68a04-202">能達成這項功能，是因為使用了備援連結。</span><span class="sxs-lookup"><span data-stu-id="68a04-202">This is achieved by using redundant links.</span></span> <span data-ttu-id="68a04-203">不過，並非所有連線提供者都支援這項功能。</span><span class="sxs-lookup"><span data-stu-id="68a04-203">However, not all connectivity providers support this feature.</span></span> <span data-ttu-id="68a04-204">需要此功能前，請確認連線提供者已啟用此功能。</span><span class="sxs-lookup"><span data-stu-id="68a04-204">Verify that your connectivity provider enables this feature before depending on it.</span></span>

<span data-ttu-id="68a04-205">雖然有些提供者可讓您變更頻寬，但請確定您挑選的初始頻寬大於您的需求，且具有成長的空間。</span><span class="sxs-lookup"><span data-stu-id="68a04-205">Although some providers allow you to change your bandwidth, make sure you pick an initial bandwidth that surpasses your needs and provides room for growth.</span></span> <span data-ttu-id="68a04-206">如果您未來需要增加頻寬，您有兩個選項：</span><span class="sxs-lookup"><span data-stu-id="68a04-206">If you need to increase bandwidth in the future, you are left with two options:</span></span>

- <span data-ttu-id="68a04-207">增加頻寬。</span><span class="sxs-lookup"><span data-stu-id="68a04-207">Increase the bandwidth.</span></span> <span data-ttu-id="68a04-208">您應該盡可能避免使用此選項，並非所有提供者都可讓您隨意地增加頻寬。</span><span class="sxs-lookup"><span data-stu-id="68a04-208">You should avoid this option as much as possible, and not all providers allow you to increase bandwidth dynamically.</span></span> <span data-ttu-id="68a04-209">但是，如果增加頻寬是必須的，請洽詢您的提供者，確認他們是否支援透過 Powershell 命令變更 ExpressRoute 頻寬屬性。</span><span class="sxs-lookup"><span data-stu-id="68a04-209">But if a bandwidth increase is needed, check with your provider to verify they support changing ExpressRoute bandwidth properties via Powershell commands.</span></span> <span data-ttu-id="68a04-210">如果他們支援的話，請執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="68a04-210">If they do, run the commands below.</span></span>

    ```powershell
    $ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
    $ckt.ServiceProviderProperties.BandwidthInMbps = <<bandwidth-in-mbps>>
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

    <span data-ttu-id="68a04-211">您可以在不中斷連線的情況下，增加頻寬。</span><span class="sxs-lookup"><span data-stu-id="68a04-211">You can increase the bandwidth without loss of connectivity.</span></span> <span data-ttu-id="68a04-212">降低頻寬將會導致連線中斷，因為您必須刪除線路，然後以新的設定重新建立線路。</span><span class="sxs-lookup"><span data-stu-id="68a04-212">Downgrading the bandwidth will result in disruption in connectivity, because you must delete the circuit and recreate it with the new configuration.</span></span>

- <span data-ttu-id="68a04-213">變更您的價格方案及/或升級為進階版。</span><span class="sxs-lookup"><span data-stu-id="68a04-213">Change your pricing plan and/or upgrade to Premium.</span></span> <span data-ttu-id="68a04-214">若要這樣做，請執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="68a04-214">To do so, run the following commands.</span></span> <span data-ttu-id="68a04-215">`Sku.Tier` 屬性可以是 `Standard` 或 `Premium`；`Sku.Name` 屬性可以是 `MeteredData` 或 `UnlimitedData`。</span><span class="sxs-lookup"><span data-stu-id="68a04-215">The `Sku.Tier` property can be `Standard` or `Premium`; the `Sku.Name` property can be `MeteredData` or `UnlimitedData`.</span></span>

    ```powershell
    $ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>

    $ckt.Sku.Tier = "Premium"
    $ckt.Sku.Family = "MeteredData"
    $ckt.Sku.Name = "Premium_MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

    > [!IMPORTANT]
    > <span data-ttu-id="68a04-216">請確定 `Sku.Name` 屬性符合 `Sku.Tier` 和 `Sku.Family`。</span><span class="sxs-lookup"><span data-stu-id="68a04-216">Make sure the `Sku.Name` property matches the `Sku.Tier` and `Sku.Family`.</span></span> <span data-ttu-id="68a04-217">如果您變更系列和層次，而不是變更名稱，您的連線將會停用。</span><span class="sxs-lookup"><span data-stu-id="68a04-217">If you change the family and tier, but not the name, your connection will be disabled.</span></span>
    >
    >

    <span data-ttu-id="68a04-218">您可以在不中斷連線的情況下升級 SKU，但您不能從無限制的價格方案切換為計量方案。</span><span class="sxs-lookup"><span data-stu-id="68a04-218">You can upgrade the SKU without disruption, but you cannot switch from the unlimited pricing plan to metered.</span></span> <span data-ttu-id="68a04-219">將 SKU 降級時，您的頻寬耗用量必須維持在標準 SKU 的預設限制內。</span><span class="sxs-lookup"><span data-stu-id="68a04-219">When downgrading the SKU, your bandwidth consumption must remain within the default limit of the standard SKU.</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="68a04-220">可用性考量</span><span class="sxs-lookup"><span data-stu-id="68a04-220">Availability considerations</span></span>

<span data-ttu-id="68a04-221">ExpressRoute 不支援透過路由器備援通訊協定來實作高可用性，例如熱待命路由通訊協定 (HSRP) 和虛擬路由器備援通訊協定 (VRRP)。</span><span class="sxs-lookup"><span data-stu-id="68a04-221">ExpressRoute does not support router redundancy protocols such as hot standby routing protocol (HSRP) and virtual router redundancy protocol (VRRP) to implement high availability.</span></span> <span data-ttu-id="68a04-222">相反地，它會針對每個對等互連使用一組備援 BGP 工作階段。</span><span class="sxs-lookup"><span data-stu-id="68a04-222">Instead, it uses a redundant pair of BGP sessions per peering.</span></span> <span data-ttu-id="68a04-223">為了讓您的網路具有高可用性連線，Azure 會使用主動-主動設定，在兩個路由器上 (Microsoft 邊緣的一部分) 佈建兩個備援連接埠。</span><span class="sxs-lookup"><span data-stu-id="68a04-223">To facilitate highly-available connections to your network, Azure provisions you with two redundant ports on two routers (part of the Microsoft edge) in an active-active configuration.</span></span>

<span data-ttu-id="68a04-224">依預設，BGP 工作階段會設定 60 秒的閒置逾時值。</span><span class="sxs-lookup"><span data-stu-id="68a04-224">By default, BGP sessions use an idle timeout value of 60 seconds.</span></span> <span data-ttu-id="68a04-225">如果工作階段逾時 3 次 (共 180 秒)，路由器會標示為無法使用，而所有流量會被重新導向至其餘路由器。</span><span class="sxs-lookup"><span data-stu-id="68a04-225">If a session times out three times (180 seconds total), the router is marked as unavailable, and all traffic is redirected to the remaining router.</span></span> <span data-ttu-id="68a04-226">此 180 秒的逾時可能對某些重要應用程式而言太長。</span><span class="sxs-lookup"><span data-stu-id="68a04-226">This 180-second timeout might be too long for critical applications.</span></span> <span data-ttu-id="68a04-227">如果是這樣，您可以在內部部署路由器上將 BGP 逾時設定變更為較小的值。</span><span class="sxs-lookup"><span data-stu-id="68a04-227">If so, you can change your BGP time-out settings on the on-premises router to a smaller value.</span></span>

<span data-ttu-id="68a04-228">根據您使用的提供者類型，以及 ExpressRoute 線路數量和您要設定的虛擬網路閘道連線數量，您可以使用不同方式來設定 Azure 連線的高可用性。</span><span class="sxs-lookup"><span data-stu-id="68a04-228">You can configure high availability for your Azure connection in different ways, depending on the type of provider you use, and the number of ExpressRoute circuits and virtual network gateway connections you're willing to configure.</span></span> <span data-ttu-id="68a04-229">以下是可用性選項的摘要：</span><span class="sxs-lookup"><span data-stu-id="68a04-229">The following summarizes your availability options:</span></span>

- <span data-ttu-id="68a04-230">如果您使用第 2 層連線，可使用主動-主動設定將備援路由器部署在內部部署網路中。</span><span class="sxs-lookup"><span data-stu-id="68a04-230">If you're using a layer 2 connection, deploy redundant routers in your on-premises network in an active-active configuration.</span></span> <span data-ttu-id="68a04-231">將主要線路連線至一個路由器，然後將次要線路連線至另一個路由器。</span><span class="sxs-lookup"><span data-stu-id="68a04-231">Connect the primary circuit to one router, and the secondary circuit to the other.</span></span> <span data-ttu-id="68a04-232">這樣可讓您的連線兩端都有高可用性連線。</span><span class="sxs-lookup"><span data-stu-id="68a04-232">This will give you a highly available connection at both ends of the connection.</span></span> <span data-ttu-id="68a04-233">如果您需要 ExpressRoute 服務等級協定 (SLA)，這會是必要項目。</span><span class="sxs-lookup"><span data-stu-id="68a04-233">This is necessary if you require the ExpressRoute service level agreement (SLA).</span></span> <span data-ttu-id="68a04-234">如需詳細資訊，請參閱 [Azure ExpressRoute SLA][sla-for-expressroute]。</span><span class="sxs-lookup"><span data-stu-id="68a04-234">See [SLA for Azure ExpressRoute][sla-for-expressroute] for details.</span></span>

    <span data-ttu-id="68a04-235">下圖顯示的設定中，包含與主要和次要線路連線的備援內部部署路由器。</span><span class="sxs-lookup"><span data-stu-id="68a04-235">The following diagram shows a configuration with redundant on-premises routers connected to the primary and secondary circuits.</span></span> <span data-ttu-id="68a04-236">每個線路都會處理公用對等互連和私人對等互連的流量 (如上一節中所述，每個對等互連會指定一組 /30 的位址空)。</span><span class="sxs-lookup"><span data-stu-id="68a04-236">Each circuit handles the traffic for a public peering and a private peering (each peering is designated a pair of /30 address spaces, as described in the previous section).</span></span>

    <span data-ttu-id="68a04-237">![[1]][1]</span><span class="sxs-lookup"><span data-stu-id="68a04-237">![[1]][1]</span></span>

- <span data-ttu-id="68a04-238">如果您使用第 3 層連線，請確認此連線會為您提供處理可用性的備援 BGP 工作階段。</span><span class="sxs-lookup"><span data-stu-id="68a04-238">If you're using a layer 3 connection, verify that it provides redundant BGP sessions that handle availability for you.</span></span>

- <span data-ttu-id="68a04-239">將 VNet 連線至不同服務提供者提供的多個 ExpressRoute 線路。</span><span class="sxs-lookup"><span data-stu-id="68a04-239">Connect the VNet to multiple ExpressRoute circuits, supplied by different service providers.</span></span> <span data-ttu-id="68a04-240">此策略會提供額外的高可用性和災害復原功能。</span><span class="sxs-lookup"><span data-stu-id="68a04-240">This strategy provides additional high-availability and disaster recovery capabilities.</span></span>

- <span data-ttu-id="68a04-241">設定站對站 VPN 作為 ExpressRoute 的容錯移轉路徑。</span><span class="sxs-lookup"><span data-stu-id="68a04-241">Configure a site-to-site VPN as a failover path for ExpressRoute.</span></span> <span data-ttu-id="68a04-242">如需此選像的詳細資訊，請參閱[使用 ExpressRoute 搭配 VPN 容錯移轉，將內部部署網路連線至 Azure][highly-available-network-architecture]。</span><span class="sxs-lookup"><span data-stu-id="68a04-242">For more about this option, see [Connect an on-premises network to Azure using ExpressRoute with VPN failover][highly-available-network-architecture].</span></span>
 <span data-ttu-id="68a04-243">此選項僅適用於私人對等互連。</span><span class="sxs-lookup"><span data-stu-id="68a04-243">This option only applies to private peering.</span></span> <span data-ttu-id="68a04-244">針對 Azure 和 Office 365 服務，網際網路是唯一的容錯移轉路徑。</span><span class="sxs-lookup"><span data-stu-id="68a04-244">For Azure and Office 365 services, the Internet is the only failover path.</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="68a04-245">管理性考量</span><span class="sxs-lookup"><span data-stu-id="68a04-245">Manageability considerations</span></span>

<span data-ttu-id="68a04-246">您可以使用 [Azure 連線工具組 (AzureCT)][azurect] 來監視您內部部署資料中心與 Azure 之間的連線。</span><span class="sxs-lookup"><span data-stu-id="68a04-246">You can use the [Azure Connectivity Toolkit (AzureCT)][azurect] to monitor connectivity between your on-premises datacenter and Azure.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="68a04-247">安全性考量</span><span class="sxs-lookup"><span data-stu-id="68a04-247">Security considerations</span></span>

<span data-ttu-id="68a04-248">根據您的安全性考量和相容性需求，您可以使用不同方法來設定 Azure 連線的安全性選項。</span><span class="sxs-lookup"><span data-stu-id="68a04-248">You can configure security options for your Azure connection in different ways, depending on your security concerns and compliance needs.</span></span>

<span data-ttu-id="68a04-249">第 3 層中的 ExpressRoute 運作。</span><span class="sxs-lookup"><span data-stu-id="68a04-249">ExpressRoute operates in layer 3.</span></span> <span data-ttu-id="68a04-250">透過使用網路安全性裝備，限制流量只能傳送到合法的資源，就可以防止應用程式層中的威脅。</span><span class="sxs-lookup"><span data-stu-id="68a04-250">Threats in the application layer can be prevented by using a network security appliance that restricts traffic to legitimate resources.</span></span> <span data-ttu-id="68a04-251">此外，使用公用對等互連的 ExpressRoute 連線只能從內部部署中起始。</span><span class="sxs-lookup"><span data-stu-id="68a04-251">Additionally, ExpressRoute connections using public peering can only be initiated from on-premises.</span></span> <span data-ttu-id="68a04-252">這可防止惡意服務的存取，並防止內部部署資料從網際網路洩露出去。</span><span class="sxs-lookup"><span data-stu-id="68a04-252">This prevents a rogue service from accessing and compromising on-premises data from the Internet.</span></span>

<span data-ttu-id="68a04-253">為了盡可能提高安全性，請在內部部署網路與提供者邊緣路由器之間新增網路安全性設備。</span><span class="sxs-lookup"><span data-stu-id="68a04-253">To maximize security, add network security appliances between the on-premises network and the provider edge routers.</span></span> <span data-ttu-id="68a04-254">這有助於限制未經授權的流量從 VNet 流入：</span><span class="sxs-lookup"><span data-stu-id="68a04-254">This will help to restrict the inflow of unauthorized traffic from the VNet:</span></span>

<span data-ttu-id="68a04-255">![[2]][2]</span><span class="sxs-lookup"><span data-stu-id="68a04-255">![[2]][2]</span></span>

<span data-ttu-id="68a04-256">基於稽核或合規性考量，您可能需要禁止從 VNet 中執行的元件直接存取網際網路，以及實作[強制通道][forced-tuneling]。</span><span class="sxs-lookup"><span data-stu-id="68a04-256">For auditing or compliance purposes, it may be necessary to prohibit direct access from components running in the VNet to the Internet and implement [forced tunneling][forced-tuneling].</span></span> <span data-ttu-id="68a04-257">在此情況下，網際網路流量應透過內部部署中執行的 Proxy 重新導回，如此就可經過稽核。</span><span class="sxs-lookup"><span data-stu-id="68a04-257">In this situation, Internet traffic should be redirected back through a proxy running  on-premises where it can be audited.</span></span> <span data-ttu-id="68a04-258">Proxy 可以設定為封鎖未經授權的流量送出，並篩選潛在的惡意輸入流量。</span><span class="sxs-lookup"><span data-stu-id="68a04-258">The proxy can be configured to block unauthorized traffic flowing out, and filter potentially malicious inbound traffic.</span></span>

<span data-ttu-id="68a04-259">![[3]][3]</span><span class="sxs-lookup"><span data-stu-id="68a04-259">![[3]][3]</span></span>

<span data-ttu-id="68a04-260">為了盡可能提高安全性，請勿啟用您虛擬機器的公用 IP 位址，並且使用 NSG，以確保這些虛擬機器不可公開存取。</span><span class="sxs-lookup"><span data-stu-id="68a04-260">To maximize security, do not enable a public IP address for your VMs, and use NSGs to ensure that these VMs aren't publicly accessible.</span></span> <span data-ttu-id="68a04-261">虛擬機器應僅供內部 IP 位址使用。</span><span class="sxs-lookup"><span data-stu-id="68a04-261">VMs should only be available using the internal IP address.</span></span> <span data-ttu-id="68a04-262">這些位址可透過 ExpressRoute 網路設定為可存取，讓內部部署的 DevOps 人員可進行設定或維護。</span><span class="sxs-lookup"><span data-stu-id="68a04-262">These addresses can be made accessible through the ExpressRoute network, enabling on-premises DevOps staff to perform configuration or maintenance.</span></span>

<span data-ttu-id="68a04-263">如果您必須在外部網路中公開虛擬機器的管理端點，請使用 NSG 或存取控制清單，以 IP 位址或網路允許清單來限制這些連接埠的可見性。</span><span class="sxs-lookup"><span data-stu-id="68a04-263">If you must expose management endpoints for VMs to an external network, use NSGs or access control lists to restrict the visibility of these ports to a whitelist of IP addresses or networks.</span></span>

> [!NOTE]
> <span data-ttu-id="68a04-264">依預設，透過 Azure 入口網站部署的 Azure 虛擬機器包含提供登入存取的公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="68a04-264">By default, Azure VMs deployed through the Azure portal include a public IP address that provides login access.</span></span>
>

## <a name="deploy-the-solution"></a><span data-ttu-id="68a04-265">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="68a04-265">Deploy the solution</span></span>

<span data-ttu-id="68a04-266">**必要條件**。</span><span class="sxs-lookup"><span data-stu-id="68a04-266">**Prerequisites**.</span></span> <span data-ttu-id="68a04-267">您必須已經使用適當的網路設備，設定現有的內部部署基礎結構。</span><span class="sxs-lookup"><span data-stu-id="68a04-267">You must have an existing on-premises infrastructure already configured with a suitable network appliance.</span></span>

<span data-ttu-id="68a04-268">若要部署解決方案，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="68a04-268">To deploy the solution, perform the following steps.</span></span>

<!-- markdownlint-disable MD033 -->

1. <span data-ttu-id="68a04-269">按一下下方的按鈕：</span><span class="sxs-lookup"><span data-stu-id="68a04-269">Click the button below:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fexpressroute%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

2. <span data-ttu-id="68a04-270">等待此連結在 Azure 入口網站中開啟，然後按照下列步驟進行：</span><span class="sxs-lookup"><span data-stu-id="68a04-270">Wait for the link to open in the Azure portal, then follow these steps:</span></span>
   - <span data-ttu-id="68a04-271">**資源群組**名稱已在參數檔案中定義，因此請在文字方塊中選取 [新建] 並輸入 `ra-hybrid-er-rg`。</span><span class="sxs-lookup"><span data-stu-id="68a04-271">The **Resource group** name is already defined in the parameter file, so select **Create New** and enter `ra-hybrid-er-rg` in the text box.</span></span>
   - <span data-ttu-id="68a04-272">從 [位置] 下拉式方塊選取區域。</span><span class="sxs-lookup"><span data-stu-id="68a04-272">Select the region from the **Location** drop down box.</span></span>
   - <span data-ttu-id="68a04-273">請勿編輯 [範本的根 URI] 或 [參數根 URI] 文字方塊。</span><span class="sxs-lookup"><span data-stu-id="68a04-273">Do not edit the **Template Root Uri** or the **Parameter Root Uri** text boxes.</span></span>
   - <span data-ttu-id="68a04-274">檢閱條款和條件，然後按一下 [我同意上方所述的條款及條件] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="68a04-274">Review the terms and conditions, then click the **I agree to the terms and conditions stated above** checkbox.</span></span>
   - <span data-ttu-id="68a04-275">按一下 [購買] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="68a04-275">Click the **Purchase** button.</span></span>

3. <span data-ttu-id="68a04-276">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="68a04-276">Wait for the deployment to complete.</span></span>

4. <span data-ttu-id="68a04-277">按一下下方的按鈕：</span><span class="sxs-lookup"><span data-stu-id="68a04-277">Click the button below:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fexpressroute%2Fazuredeploy-expressRouteCircuit.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

5. <span data-ttu-id="68a04-278">等待此連結在 Azure 入口網站中開啟，然後按照下列步驟進行：</span><span class="sxs-lookup"><span data-stu-id="68a04-278">Wait for the link to open in the Azure portal, then follow these steps:</span></span>
   - <span data-ttu-id="68a04-279">選取 [資源群組] 區段中的 [使用現有的]，然後在文字方塊中輸入 `ra-hybrid-er-rg`。</span><span class="sxs-lookup"><span data-stu-id="68a04-279">Select **Use existing** in the **Resource group** section and enter `ra-hybrid-er-rg` in the text box.</span></span>
   - <span data-ttu-id="68a04-280">從 [位置] 下拉式方塊選取區域。</span><span class="sxs-lookup"><span data-stu-id="68a04-280">Select the region from the **Location** drop down box.</span></span>
   - <span data-ttu-id="68a04-281">請勿編輯 [範本的根 URI] 或 [參數根 URI] 文字方塊。</span><span class="sxs-lookup"><span data-stu-id="68a04-281">Do not edit the **Template Root Uri** or the **Parameter Root Uri** text boxes.</span></span>
   - <span data-ttu-id="68a04-282">檢閱條款和條件，然後按一下 [我同意上方所述的條款及條件] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="68a04-282">Review the terms and conditions, then click the **I agree to the terms and conditions stated above** checkbox.</span></span>
   - <span data-ttu-id="68a04-283">按一下 [購買] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="68a04-283">Click the **Purchase** button.</span></span>

6. <span data-ttu-id="68a04-284">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="68a04-284">Wait for the deployment to complete.</span></span>

<!-- markdownlint-enable MD033 -->

<!-- links -->

[forced-tuneling]: ../dmz/secure-vnet-hybrid.md
[highly-available-network-architecture]: ./expressroute-vpn-failover.md

[expressroute-technical-overview]: /azure/expressroute/expressroute-introduction
[expressroute-prereqs]: /azure/expressroute/expressroute-prerequisites
[configure-expressroute-routing]: /azure/expressroute/expressroute-howto-routing-arm
[sla-for-expressroute]: https://azure.microsoft.com/support/legal/sla/expressroute
[link-vnet-to-expressroute]: /azure/expressroute/expressroute-howto-linkvnet-arm
[ExpressRoute-provisioning]: /azure/expressroute/expressroute-workflows
[expressroute-introduction]: /azure/expressroute/expressroute-introduction
[expressroute-peering]: /azure/expressroute/expressroute-circuit-peerings
[expressroute-pricing]: https://azure.microsoft.com/pricing/details/expressroute/
[expressroute-limits]: /azure/azure-subscription-service-limits#networking-limits
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-architectures.vsdx
[er-circuit-parameters]: https://github.com/mspnp/reference-architectures/tree/master/hybrid-networking/expressroute/parameters/expressRouteCircuit.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/

[0]: ./images/expressroute.png "使用 Azure ExpressRoute 的混合式網路架構"
[1]: ../_images/guidance-hybrid-network-expressroute/figure2.png "搭配 ExpressRoute 主要和次要線路使用備援路由器"
[2]: ../_images/guidance-hybrid-network-expressroute/figure3.png "將安全性裝置新增至內部部署網路"
[3]: ../_images/guidance-hybrid-network-expressroute/figure4.png "使用強制通道來稽核網際網路繫結流量"
[4]: ../_images/guidance-hybrid-network-expressroute/figure5.png "找出 ExpressRoute 線路的 ServiceKey"

---
title: 實作高可用性的混合式網路架構
description: 如何實作跨越使用 ExpressRoute 搭配 VPN 閘道容錯移轉連線之 Azure 虛擬網路與內部部署網路的安全站對站網路架構。
author: telmosampaio
ms.date: 11/28/2016
pnp.series.title: Connect an on-premises network to Azure
pnp.series.prev: expressroute
cardTitle: Improving availability
ms.openlocfilehash: 81298215c814cee805eff57fdc28f7c127148b5f
ms.sourcegitcommit: c441fd165e6bebbbbbc19854ec6f3676be9c3b25
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2018
---
# <a name="connect-an-on-premises-network-to-azure-using-expressroute-with-vpn-failover"></a><span data-ttu-id="6c226-103">使用 ExpressRoute 搭配 VPN 容錯移轉，將內部部署網路連線至 Azure</span><span class="sxs-lookup"><span data-stu-id="6c226-103">Connect an on-premises network to Azure using ExpressRoute with VPN failover</span></span>

<span data-ttu-id="6c226-104">此參考架構會示範如何使用 ExpressRoute 搭配當作容錯移轉連線的站對站虛擬私人網路 (VPN)，將內部部署網路連線到 Azure 虛擬網路 (VNet)。</span><span class="sxs-lookup"><span data-stu-id="6c226-104">This reference architecture shows how to connect an on-premises network to an Azure virtual network (VNet) using ExpressRoute, with a site-to-site virtual private network (VPN) as a failover connection.</span></span> <span data-ttu-id="6c226-105">內部部署網路與 Azure VNet 之間的流量會透過 ExpressRoute 連線流動。</span><span class="sxs-lookup"><span data-stu-id="6c226-105">Traffic flows between the on-premises network and the Azure VNet through an ExpressRoute connection.</span></span> <span data-ttu-id="6c226-106">如果 ExpressRoute 線路的連線中斷，則流量會透過 IPSec VPN 通道路由傳送。</span><span class="sxs-lookup"><span data-stu-id="6c226-106">If there is a loss of connectivity in the ExpressRoute circuit, traffic is routed through an IPSec VPN tunnel.</span></span> [<span data-ttu-id="6c226-107">**部署這個解決方案**。</span><span class="sxs-lookup"><span data-stu-id="6c226-107">**Deploy this solution**.</span></span>](#deploy-the-solution)

<span data-ttu-id="6c226-108">請注意，如果無法使用 ExpressRoute 線路，VPN 路由將只會處理私用對等連線。</span><span class="sxs-lookup"><span data-stu-id="6c226-108">Note that if the ExpressRoute circuit is unavailable, the VPN route will only handle private peering connections.</span></span> <span data-ttu-id="6c226-109">公用對等和 Microsoft 對等連線將透過網際網路傳送。</span><span class="sxs-lookup"><span data-stu-id="6c226-109">Public peering and Microsoft peering connections will pass over the Internet.</span></span> 

<span data-ttu-id="6c226-110">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="6c226-110">![[0]][0]</span></span>

<span data-ttu-id="6c226-111">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="6c226-111">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="6c226-112">架構</span><span class="sxs-lookup"><span data-stu-id="6c226-112">Architecture</span></span> 

<span data-ttu-id="6c226-113">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="6c226-113">The architecture consists of the following components.</span></span>

* <span data-ttu-id="6c226-114">**內部部署網路**。</span><span class="sxs-lookup"><span data-stu-id="6c226-114">**On-premises network**.</span></span> <span data-ttu-id="6c226-115">在組織內執行的私用區域網路。</span><span class="sxs-lookup"><span data-stu-id="6c226-115">A private local-area network running within an organization.</span></span>

* <span data-ttu-id="6c226-116">**VPN 設備**。</span><span class="sxs-lookup"><span data-stu-id="6c226-116">**VPN appliance**.</span></span> <span data-ttu-id="6c226-117">可對內部部署網路提供外部連線的裝置或服務。</span><span class="sxs-lookup"><span data-stu-id="6c226-117">A device or service that provides external connectivity to the on-premises network.</span></span> <span data-ttu-id="6c226-118">VPN 設備可能是硬體裝置或軟體解決方案，例如，Windows Server 2012 中的路由及遠端存取服務 (RRAS)。</span><span class="sxs-lookup"><span data-stu-id="6c226-118">The VPN appliance may be a hardware device, or it can be a software solution such as the Routing and Remote Access Service (RRAS) in Windows Server 2012.</span></span> <span data-ttu-id="6c226-119">如需支援的 VPN 設備清單，以及設定選取的 VPN 設備以連線至 Azure 的相關資訊，請參閱[關於站對站 VPN 閘道連線的 VPN 裝置][vpn-appliance]。</span><span class="sxs-lookup"><span data-stu-id="6c226-119">For a list of supported VPN appliances and information on configuring selected VPN appliances for connecting to Azure, see [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance].</span></span>

* <span data-ttu-id="6c226-120">**ExpressRoute 線路**。</span><span class="sxs-lookup"><span data-stu-id="6c226-120">**ExpressRoute circuit**.</span></span> <span data-ttu-id="6c226-121">透過邊緣路由器聯結內部部署網路與 Azure 之連線提供者所提供的第 2 層或第 3 層線路。</span><span class="sxs-lookup"><span data-stu-id="6c226-121">A layer 2 or layer 3 circuit supplied by the connectivity provider that joins the on-premises network with Azure through the edge routers.</span></span> <span data-ttu-id="6c226-122">此線路使用連線提供者所管理的硬體基礎結構。</span><span class="sxs-lookup"><span data-stu-id="6c226-122">The circuit uses the hardware infrastructure managed by the connectivity provider.</span></span>

* <span data-ttu-id="6c226-123">**ExpressRoute 虛擬網路閘道**。</span><span class="sxs-lookup"><span data-stu-id="6c226-123">**ExpressRoute virtual network gateway**.</span></span> <span data-ttu-id="6c226-124">ExpressRoute 虛擬網路閘道可讓 VNet 連線到與內部部署網路連線所使用的 ExpressRoute 線路。</span><span class="sxs-lookup"><span data-stu-id="6c226-124">The ExpressRoute virtual network gateway enables the VNet to connect to the ExpressRoute circuit used for connectivity with your on-premises network.</span></span>

* <span data-ttu-id="6c226-125">**VPN 虛擬網路閘道**。</span><span class="sxs-lookup"><span data-stu-id="6c226-125">**VPN virtual network gateway**.</span></span> <span data-ttu-id="6c226-126">VPN 虛擬網路閘道可讓 VNet 連線到內部部署網路中的 VPN 設備。</span><span class="sxs-lookup"><span data-stu-id="6c226-126">The VPN virtual network gateway enables the VNet to connect to the VPN appliance in the on-premises network.</span></span> <span data-ttu-id="6c226-127">VPN 虛擬網路閘道設定為僅透過 VPN 設備，接受來自內部網路的要求。</span><span class="sxs-lookup"><span data-stu-id="6c226-127">The VPN virtual network gateway is configured to accept requests from the on-premises network only through the VPN appliance.</span></span> <span data-ttu-id="6c226-128">如需詳細資訊，請參閱[將內部部署網路連線至 Microsoft Azure 虛擬網路][connect-to-an-Azure-vnet]。</span><span class="sxs-lookup"><span data-stu-id="6c226-128">For more information, see [Connect an on-premises network to a Microsoft Azure virtual network][connect-to-an-Azure-vnet].</span></span>

* <span data-ttu-id="6c226-129">**VPN 連線**。</span><span class="sxs-lookup"><span data-stu-id="6c226-129">**VPN connection**.</span></span> <span data-ttu-id="6c226-130">連線的屬性會指定連線類型 (IPSec) 以及與內部部署 VPN 設備共用以加密流量的金鑰。</span><span class="sxs-lookup"><span data-stu-id="6c226-130">The connection has properties that specify the connection type (IPSec) and the key shared with the on-premises VPN appliance to encrypt traffic.</span></span>

* <span data-ttu-id="6c226-131">**Azure 虛擬網路 (VNet)**。</span><span class="sxs-lookup"><span data-stu-id="6c226-131">**Azure Virtual Network (VNet)**.</span></span> <span data-ttu-id="6c226-132">每個 VNet 都位於單一 Azure 區域，而且可以裝載多個應用程式層。</span><span class="sxs-lookup"><span data-stu-id="6c226-132">Each VNet resides in a single Azure region, and can host multiple application tiers.</span></span> <span data-ttu-id="6c226-133">應用程式層可以使用每個 VNet 中的子網路區隔。</span><span class="sxs-lookup"><span data-stu-id="6c226-133">Application tiers can be segmented using subnets in each VNet.</span></span>

* <span data-ttu-id="6c226-134">**閘道子網路**。</span><span class="sxs-lookup"><span data-stu-id="6c226-134">**Gateway subnet**.</span></span> <span data-ttu-id="6c226-135">虛擬網路閘道會保留在相同的子網路中。</span><span class="sxs-lookup"><span data-stu-id="6c226-135">The virtual network gateways are held in the same subnet.</span></span>

* <span data-ttu-id="6c226-136">**雲端應用程式**。</span><span class="sxs-lookup"><span data-stu-id="6c226-136">**Cloud application**.</span></span> <span data-ttu-id="6c226-137">在 Azure 中裝載的應用程式。</span><span class="sxs-lookup"><span data-stu-id="6c226-137">The application hosted in Azure.</span></span> <span data-ttu-id="6c226-138">此應用程式在透過 Azure 負載平衡器連線多個子網路的情況下，可能包括多層。</span><span class="sxs-lookup"><span data-stu-id="6c226-138">It might include multiple tiers, with multiple subnets connected through Azure load balancers.</span></span> <span data-ttu-id="6c226-139">如需有關應用程式基礎結構的詳細資訊，請參閱[執行 Windows VM 工作負載][windows-vm-ra]和[執行 Linux VM 工作負載][linux-vm-ra]。</span><span class="sxs-lookup"><span data-stu-id="6c226-139">For more information about the application infrastructure, see [Running Windows VM workloads][windows-vm-ra] and [Running Linux VM workloads][linux-vm-ra].</span></span>

## <a name="recommendations"></a><span data-ttu-id="6c226-140">建議</span><span class="sxs-lookup"><span data-stu-id="6c226-140">Recommendations</span></span>

<span data-ttu-id="6c226-141">下列建議適用於大部分的案例。</span><span class="sxs-lookup"><span data-stu-id="6c226-141">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="6c226-142">除非您有特定的需求會覆寫它們，否則請遵循下列建議。</span><span class="sxs-lookup"><span data-stu-id="6c226-142">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="vnet-and-gatewaysubnet"></a><span data-ttu-id="6c226-143">VNet 和 GatewaySubnet</span><span class="sxs-lookup"><span data-stu-id="6c226-143">VNet and GatewaySubnet</span></span>

<span data-ttu-id="6c226-144">在相同的 VNet 中建立 ExpressRoute 虛擬網路閘道與 VPN 虛擬網路閘道。</span><span class="sxs-lookup"><span data-stu-id="6c226-144">Create the ExpressRoute virtual network gateway and the VPN virtual network gateway in the same VNet.</span></span> <span data-ttu-id="6c226-145">這表示它們應該會共用名為 *GatewaySubnet* 的相同子網路。</span><span class="sxs-lookup"><span data-stu-id="6c226-145">This means that they should share the same subnet named *GatewaySubnet*.</span></span>

<span data-ttu-id="6c226-146">如果在 VNet 已經包含名為 *GatewaySubnet* 的子網路，請確認其位址空間為 /27 或更大。</span><span class="sxs-lookup"><span data-stu-id="6c226-146">If the VNet already includes a subnet named *GatewaySubnet*, ensure that it has a /27 or larger address space.</span></span> <span data-ttu-id="6c226-147">如果現有的子網路太小，請使用下列 PowerShell 命令移除子網路：</span><span class="sxs-lookup"><span data-stu-id="6c226-147">If the existing subnet is too small, use the following PowerShell command to remove the subnet:</span></span> 

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
```

<span data-ttu-id="6c226-148">如果 VNet 中不包含名為 **GatewaySubnet** 的子網路，請使用下列 PowerShell 命令建立一個新的子網路：</span><span class="sxs-lookup"><span data-stu-id="6c226-148">If the VNet does not contain a subnet named **GatewaySubnet**, create a new one using the following Powershell command:</span></span>

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.224/27"
$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="vpn-and-expressroute-gateways"></a><span data-ttu-id="6c226-149">VPN 和 ExpressRoute 閘道</span><span class="sxs-lookup"><span data-stu-id="6c226-149">VPN and ExpressRoute gateways</span></span>

<span data-ttu-id="6c226-150">請確認您的組織符合 [ExpressRoute 必要條件需求][expressroute-prereq]以連線至 Azure。</span><span class="sxs-lookup"><span data-stu-id="6c226-150">Verify that your organization meets the [ExpressRoute prerequisite requirements][expressroute-prereq] for connecting to Azure.</span></span>

<span data-ttu-id="6c226-151">如果您在 Azure VNet 中已經有一個 VPN 虛擬網路閘道，請使用下列 Powershell 命令將它移除：</span><span class="sxs-lookup"><span data-stu-id="6c226-151">If you already have a VPN virtual network gateway in your Azure VNet, use the following  Powershell command to remove it:</span></span>

```powershell
Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
```

<span data-ttu-id="6c226-152">請依照[使用 Azure ExpressRoute 實作混合式網路架構][implementing-expressroute]中的指示，建立 ExpressRoute 連線。</span><span class="sxs-lookup"><span data-stu-id="6c226-152">Follow the instructions in [Implementing a hybrid network architecture with Azure ExpressRoute][implementing-expressroute] to establish your ExpressRoute connection.</span></span>

<span data-ttu-id="6c226-153">請依照[使用 Azure 和內部部署 VPN 實作混合式網路架構][implementing-vpn]中的指示，建立 VPN 虛擬網路閘道連線。</span><span class="sxs-lookup"><span data-stu-id="6c226-153">Follow the instructions in [Implementing a hybrid network architecture with Azure and On-premises VPN][implementing-vpn] to establish your VPN virtual network gateway connection.</span></span>

<span data-ttu-id="6c226-154">建立虛擬網路閘道連線之後，依下列方式測試環境：</span><span class="sxs-lookup"><span data-stu-id="6c226-154">After you have established the virtual network gateway connections, test the environment as follows:</span></span>

1. <span data-ttu-id="6c226-155">確定您可以從內部部署網路連線到 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="6c226-155">Make sure you can connect from your on-premises network to your Azure VNet.</span></span>
2. <span data-ttu-id="6c226-156">連絡您的提供者停止 ExpressRoute 連線以進行測試。</span><span class="sxs-lookup"><span data-stu-id="6c226-156">Contact your provider to stop ExpressRoute connectivity for testing.</span></span>
3. <span data-ttu-id="6c226-157">確認您仍然可以使用 VPN 虛擬網路閘道連線，從內部部署網路連線到 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="6c226-157">Verify that you can still connect from your on-premises network to your Azure VNet using the VPN virtual network gateway connection.</span></span>
4. <span data-ttu-id="6c226-158">連絡您的提供者以重新建立 ExpressRoute 連線。</span><span class="sxs-lookup"><span data-stu-id="6c226-158">Contact your provider to reestablish ExpressRoute connectivity.</span></span>

## <a name="considerations"></a><span data-ttu-id="6c226-159">考量</span><span class="sxs-lookup"><span data-stu-id="6c226-159">Considerations</span></span>

<span data-ttu-id="6c226-160">如需 ExpressRoute 的考量，請參閱[使用 Azure ExpressRoute 實作混合式網路架構][guidance-expressroute]指引。</span><span class="sxs-lookup"><span data-stu-id="6c226-160">For ExpressRoute considerations, see the [Implementing a Hybrid Network Architecture with Azure ExpressRoute][guidance-expressroute] guidance.</span></span>

<span data-ttu-id="6c226-161">如需站對站 VPN 的考量，請參閱[使用 Azure 和內部部署 VPN 實作混合式網路架構][guidance-vpn]指引。</span><span class="sxs-lookup"><span data-stu-id="6c226-161">For site-to-site VPN considerations, see the [Implementing a Hybrid Network Architecture with Azure and On-premises VPN][guidance-vpn] guidance.</span></span>

<span data-ttu-id="6c226-162">如需 Azure 的一般安全性考量，請參閱 [Microsoft 雲端服務和網路安全性][best-practices-security]。</span><span class="sxs-lookup"><span data-stu-id="6c226-162">For general Azure security considerations, see [Microsoft cloud services and network security][best-practices-security].</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="6c226-163">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="6c226-163">Deploy the solution</span></span>

<span data-ttu-id="6c226-164">**必要條件。**</span><span class="sxs-lookup"><span data-stu-id="6c226-164">**Prequisites.**</span></span> <span data-ttu-id="6c226-165">您必須已經使用適當的網路設備，設定現有的內部部署基礎結構。</span><span class="sxs-lookup"><span data-stu-id="6c226-165">You must have an existing on-premises infrastructure already configured with a suitable network appliance.</span></span>

<span data-ttu-id="6c226-166">若要部署解決方案，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="6c226-166">To deploy the solution, perform the following steps.</span></span>

1. <span data-ttu-id="6c226-167">按一下下方的按鈕：</span><span class="sxs-lookup"><span data-stu-id="6c226-167">Click the button below:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fexpressroute-vpn-failover%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
2. <span data-ttu-id="6c226-168">等待此連結在 Azure 入口網站中開啟，然後按照下列步驟進行：</span><span class="sxs-lookup"><span data-stu-id="6c226-168">Wait for the link to open in the Azure portal, then follow these steps:</span></span>   
   * <span data-ttu-id="6c226-169">**資源群組**名稱已在參數檔案中定義，因此請在文字方塊中選取 [新建] 並輸入 `ra-hybrid-vpn-er-rg`。</span><span class="sxs-lookup"><span data-stu-id="6c226-169">The **Resource group** name is already defined in the parameter file, so select **Create New** and enter `ra-hybrid-vpn-er-rg` in the text box.</span></span>
   * <span data-ttu-id="6c226-170">從 [位置] 下拉式方塊選取區域。</span><span class="sxs-lookup"><span data-stu-id="6c226-170">Select the region from the **Location** drop down box.</span></span>
   * <span data-ttu-id="6c226-171">請勿編輯 [範本的根 URI] 或 [參數根 URI] 文字方塊。</span><span class="sxs-lookup"><span data-stu-id="6c226-171">Do not edit the **Template Root Uri** or the **Parameter Root Uri** text boxes.</span></span>
   * <span data-ttu-id="6c226-172">檢閱條款和條件，然後按一下 [我同意上方所述的條款及條件] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="6c226-172">Review the terms and conditions, then click the **I agree to the terms and conditions stated above** checkbox.</span></span>
   * <span data-ttu-id="6c226-173">按一下 [購買] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="6c226-173">Click the **Purchase** button.</span></span>
3. <span data-ttu-id="6c226-174">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="6c226-174">Wait for the deployment to complete.</span></span>
4. <span data-ttu-id="6c226-175">按一下下方的按鈕：</span><span class="sxs-lookup"><span data-stu-id="6c226-175">Click the button below:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fexpressroute-vpn-failover%2Fazuredeploy-expressRouteCircuit.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
5. <span data-ttu-id="6c226-176">等待此連結在 Azure 入口網站中開啟，進入後按照下列步驟進行：</span><span class="sxs-lookup"><span data-stu-id="6c226-176">Wait for the link to open in the Azure portal, then enter then follow these steps:</span></span>
   * <span data-ttu-id="6c226-177">選取 [資源群組] 區段中的 [使用現有的]，然後在文字方塊中輸入 `ra-hybrid-vpn-er-rg`。</span><span class="sxs-lookup"><span data-stu-id="6c226-177">Select **Use existing** in the **Resource group** section and enter `ra-hybrid-vpn-er-rg` in the text box.</span></span>
   * <span data-ttu-id="6c226-178">從 [位置] 下拉式方塊選取區域。</span><span class="sxs-lookup"><span data-stu-id="6c226-178">Select the region from the **Location** drop down box.</span></span>
   * <span data-ttu-id="6c226-179">請勿編輯 [範本的根 URI] 或 [參數根 URI] 文字方塊。</span><span class="sxs-lookup"><span data-stu-id="6c226-179">Do not edit the **Template Root Uri** or the **Parameter Root Uri** text boxes.</span></span>
   * <span data-ttu-id="6c226-180">檢閱條款和條件，然後按一下 [我同意上方所述的條款及條件] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="6c226-180">Review the terms and conditions, then click the **I agree to the terms and conditions stated above** checkbox.</span></span>
   * <span data-ttu-id="6c226-181">按一下 [購買] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="6c226-181">Click the **Purchase** button.</span></span>

<!-- links -->

[windows-vm-ra]: ../virtual-machines-windows/index.md
[linux-vm-ra]: ../virtual-machines-linux/index.md


[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[azure-vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[expressroute-prereq]: /azure/expressroute/expressroute-prerequisites
[implementing-expressroute]: ./expressroute.md
[implementing-vpn]: ./vpn.md
[guidance-expressroute]: ./expressroute.md
[guidance-vpn]: ./vpn.md
[best-practices-security]: /azure/best-practices-network-security
[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-architectures.vsdx
[0]: ./images/expressroute-vpn-failover.png "使用 ExpressRoute 與 VPN 閘道之高可用性混合式網路架構的架構"

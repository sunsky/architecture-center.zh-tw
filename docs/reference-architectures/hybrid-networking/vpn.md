---
title: 使用 VPN 將內部部署網路連線至 Azure
description: 如何實作安全的站對站網路架構，並且在透過 VPN 連線的 Azure 虛擬網路與內部部署網路中使用。
author: RohitSharma-pnp
ms.date: 11/28/2016
pnp.series.title: Connect an on-premises network to Azure
pnp.series.next: expressroute
pnp.series.prev: ./index
cardTitle: VPN
ms.openlocfilehash: dafcee6607d9cc7c56c332f9ed5d9568ff70f0e7
ms.sourcegitcommit: c441fd165e6bebbbbbc19854ec6f3676be9c3b25
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2018
ms.locfileid: "30270688"
---
# <a name="connect-an-on-premises-network-to-azure-using-a-vpn-gateway"></a><span data-ttu-id="0f699-103">使用 VPN 閘道將內部部署網路連線至 Azure</span><span class="sxs-lookup"><span data-stu-id="0f699-103">Connect an on-premises network to Azure using a VPN gateway</span></span>

<span data-ttu-id="0f699-104">此參考架構會示範如何使用網站對網站虛擬私人網路 (VPN)，將內部部署網路擴充至 Azure。</span><span class="sxs-lookup"><span data-stu-id="0f699-104">This reference architecture shows how to extend an on-premises network to Azure, using a site-to-site virtual private network (VPN).</span></span> <span data-ttu-id="0f699-105">內部部署網路和 Azure 虛擬網路 (VNet) 之間的流量會透過 IPSec VPN 通道流動。</span><span class="sxs-lookup"><span data-stu-id="0f699-105">Traffic flows between the on-premises network and an Azure Virtual Network (VNet) through an IPSec VPN tunnel.</span></span> [<span data-ttu-id="0f699-106">**部署這個解決方案**。</span><span class="sxs-lookup"><span data-stu-id="0f699-106">**Deploy this solution**.</span></span>](#deploy-the-solution)

<span data-ttu-id="0f699-107">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="0f699-107">![[0]][0]</span></span>

<span data-ttu-id="0f699-108">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="0f699-108">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="0f699-109">架構</span><span class="sxs-lookup"><span data-stu-id="0f699-109">Architecture</span></span> 

<span data-ttu-id="0f699-110">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="0f699-110">The architecture consists of the following components.</span></span>

* <span data-ttu-id="0f699-111">**內部部署網路**。</span><span class="sxs-lookup"><span data-stu-id="0f699-111">**On-premises network**.</span></span> <span data-ttu-id="0f699-112">在組織內執行的私用區域網路。</span><span class="sxs-lookup"><span data-stu-id="0f699-112">A private local-area network running within an organization.</span></span>

* <span data-ttu-id="0f699-113">**VPN 設備**。</span><span class="sxs-lookup"><span data-stu-id="0f699-113">**VPN appliance**.</span></span> <span data-ttu-id="0f699-114">可對內部部署網路提供外部連線的裝置或服務。</span><span class="sxs-lookup"><span data-stu-id="0f699-114">A device or service that provides external connectivity to the on-premises network.</span></span> <span data-ttu-id="0f699-115">VPN 設備可能是硬體裝置或軟體解決方案，例如，Windows Server 2012 中的路由及遠端存取服務 (RRAS)。</span><span class="sxs-lookup"><span data-stu-id="0f699-115">The VPN appliance may be a hardware device, or it can be a software solution such as the Routing and Remote Access Service (RRAS) in Windows Server 2012.</span></span> <span data-ttu-id="0f699-116">如需支援的 VPN 設備清單和將其設定為連線至 Azure VPN 閘道的詳細資訊，請參閱[關於站對站 VPN 閘道連線的 VPN 裝置][vpn-appliance]一文中所選取裝置的指示。</span><span class="sxs-lookup"><span data-stu-id="0f699-116">For a list of supported VPN appliances and information on configuring them to connect to an Azure VPN gateway, see the instructions for the selected device in the article [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance].</span></span>

* <span data-ttu-id="0f699-117">**虛擬網路 (VNet)**。</span><span class="sxs-lookup"><span data-stu-id="0f699-117">**Virtual network (VNet)**.</span></span> <span data-ttu-id="0f699-118">雲端應用程式與 Azure VPN 閘道的元件存會位於相同 [VNet][azure-virtual-network]。</span><span class="sxs-lookup"><span data-stu-id="0f699-118">The cloud application and the components for the Azure VPN gateway reside in the same [VNet][azure-virtual-network].</span></span>

* <span data-ttu-id="0f699-119">**Azure VPN 閘道**。</span><span class="sxs-lookup"><span data-stu-id="0f699-119">**Azure VPN gateway**.</span></span> <span data-ttu-id="0f699-120">[VPN 閘道][azure-vpn-gateway]服務可讓您透過 VPN 設備將 VNet 連線至內部部署網路。</span><span class="sxs-lookup"><span data-stu-id="0f699-120">The [VPN gateway][azure-vpn-gateway] service enables you to connect the VNet to the on-premises network through a VPN appliance.</span></span> <span data-ttu-id="0f699-121">如需詳細資訊，請參閱[將內部部署網路連線至 Microsoft Azure 虛擬網路][connect-to-an-Azure-vnet]。</span><span class="sxs-lookup"><span data-stu-id="0f699-121">For more information, see [Connect an on-premises network to a Microsoft Azure virtual network][connect-to-an-Azure-vnet].</span></span> <span data-ttu-id="0f699-122">VPN 閘道包含下列元素：</span><span class="sxs-lookup"><span data-stu-id="0f699-122">The VPN gateway includes the following elements:</span></span>
  
  * <span data-ttu-id="0f699-123">**虛擬網路閘道**。</span><span class="sxs-lookup"><span data-stu-id="0f699-123">**Virtual network gateway**.</span></span> <span data-ttu-id="0f699-124">為 VNet 提供虛擬 VPN 設備的資源。</span><span class="sxs-lookup"><span data-stu-id="0f699-124">A resource that provides a virtual VPN appliance for the VNet.</span></span> <span data-ttu-id="0f699-125">負責將流量從內部部署網路由至 VNet。</span><span class="sxs-lookup"><span data-stu-id="0f699-125">It is responsible for routing traffic from the on-premises network to the VNet.</span></span>
  * <span data-ttu-id="0f699-126">**區域網路閘道**。</span><span class="sxs-lookup"><span data-stu-id="0f699-126">**Local network gateway**.</span></span> <span data-ttu-id="0f699-127">內部部署 VPN 設備的抽象概念。</span><span class="sxs-lookup"><span data-stu-id="0f699-127">An abstraction of the on-premises VPN appliance.</span></span> <span data-ttu-id="0f699-128">從雲端應用程式流至內部部署網路的網路流量會透過此閘道路由傳送。</span><span class="sxs-lookup"><span data-stu-id="0f699-128">Network traffic from the cloud application to the on-premises network is routed through this gateway.</span></span>
  * <span data-ttu-id="0f699-129">**連線**。</span><span class="sxs-lookup"><span data-stu-id="0f699-129">**Connection**.</span></span> <span data-ttu-id="0f699-130">連線的屬性會指定連線類型 (IPSec) 以及與內部部署 VPN 設備共用以加密流量的金鑰。</span><span class="sxs-lookup"><span data-stu-id="0f699-130">The connection has properties that specify the connection type (IPSec) and the key shared with the on-premises VPN appliance to encrypt traffic.</span></span>
  * <span data-ttu-id="0f699-131">**閘道子網路**。</span><span class="sxs-lookup"><span data-stu-id="0f699-131">**Gateway subnet**.</span></span> <span data-ttu-id="0f699-132">虛擬網路閘道保留在自己的子網路中，其受限於下方＜建議＞一節中所述的各種需求。</span><span class="sxs-lookup"><span data-stu-id="0f699-132">The virtual network gateway is held in its own subnet, which is subject to various requirements, described in the Recommendations section below.</span></span>

* <span data-ttu-id="0f699-133">**雲端應用程式**。</span><span class="sxs-lookup"><span data-stu-id="0f699-133">**Cloud application**.</span></span> <span data-ttu-id="0f699-134">在 Azure 中裝載的應用程式。</span><span class="sxs-lookup"><span data-stu-id="0f699-134">The application hosted in Azure.</span></span> <span data-ttu-id="0f699-135">此應用程式在透過 Azure 負載平衡器連線多個子網路的情況下，可能包括多層。</span><span class="sxs-lookup"><span data-stu-id="0f699-135">It might include multiple tiers, with multiple subnets connected through Azure load balancers.</span></span> <span data-ttu-id="0f699-136">如需有關應用程式基礎結構的詳細資訊，請參閱[執行 Windows VM 工作負載][windows-vm-ra]和[執行 Linux VM 工作負載][linux-vm-ra]。</span><span class="sxs-lookup"><span data-stu-id="0f699-136">For more information about the application infrastructure, see [Running Windows VM workloads][windows-vm-ra] and [Running Linux VM workloads][linux-vm-ra].</span></span>

* <span data-ttu-id="0f699-137">**內部負載平衡器**。</span><span class="sxs-lookup"><span data-stu-id="0f699-137">**Internal load balancer**.</span></span> <span data-ttu-id="0f699-138">來自 VPN 閘道的網路流量會透過內部負載平衡器，路由傳送至雲端應用程式。</span><span class="sxs-lookup"><span data-stu-id="0f699-138">Network traffic from the VPN gateway is routed to the cloud application through an internal load balancer.</span></span> <span data-ttu-id="0f699-139">負載平衡器會位於應用程式的前端子網路中。</span><span class="sxs-lookup"><span data-stu-id="0f699-139">The load balancer is located in the front-end subnet of the application.</span></span>

## <a name="recommendations"></a><span data-ttu-id="0f699-140">建議</span><span class="sxs-lookup"><span data-stu-id="0f699-140">Recommendations</span></span>

<span data-ttu-id="0f699-141">下列建議適用於大部分的案例。</span><span class="sxs-lookup"><span data-stu-id="0f699-141">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="0f699-142">除非您有特定的需求會覆寫它們，否則請遵循下列建議。</span><span class="sxs-lookup"><span data-stu-id="0f699-142">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="vnet-and-gateway-subnet"></a><span data-ttu-id="0f699-143">VNet 和閘道子網路</span><span class="sxs-lookup"><span data-stu-id="0f699-143">VNet and gateway subnet</span></span>

<span data-ttu-id="0f699-144">建立 Azure VNet，且其位址空間要夠大，以便使用所有必要資源。</span><span class="sxs-lookup"><span data-stu-id="0f699-144">Create an Azure VNet with an address space large enough for all of your required resources.</span></span> <span data-ttu-id="0f699-145">如果未來可能需要其他虛擬機器，請確定 VNet 位址空間有足夠的空間可擴增。</span><span class="sxs-lookup"><span data-stu-id="0f699-145">Ensure that the VNet address space has sufficient room for growth if additional VMs are likely to be needed in the future.</span></span> <span data-ttu-id="0f699-146">VNet 的位址空間不得與內部部署網路重疊。</span><span class="sxs-lookup"><span data-stu-id="0f699-146">The address space of the VNet must not overlap with the on-premises network.</span></span> <span data-ttu-id="0f699-147">例如，上圖中 VNet 使用位址空間 10.20.0.0/16。</span><span class="sxs-lookup"><span data-stu-id="0f699-147">For example, the diagram above uses the address space 10.20.0.0/16 for the VNet.</span></span>

<span data-ttu-id="0f699-148">建立一個名為 *GatewaySubnet* 的子網路，且其位址範圍為 /27。</span><span class="sxs-lookup"><span data-stu-id="0f699-148">Create a subnet named *GatewaySubnet*, with an address range of /27.</span></span> <span data-ttu-id="0f699-149">虛擬網路閘道需要使用這個子網路。</span><span class="sxs-lookup"><span data-stu-id="0f699-149">This subnet is required by the virtual network gateway.</span></span> <span data-ttu-id="0f699-150">將 32 個位址配置給這個子網路，將有助於防止未來達到閘道大小限制。</span><span class="sxs-lookup"><span data-stu-id="0f699-150">Allocating 32 addresses to this subnet will help to prevent reaching gateway size limitations in the future.</span></span> <span data-ttu-id="0f699-151">此外，請避免將此子網路放在位址空間的中間。</span><span class="sxs-lookup"><span data-stu-id="0f699-151">Also, avoid placing this subnet in the middle of the address space.</span></span> <span data-ttu-id="0f699-152">較好的做法是，將閘道子網路的位址空間設定在 VNet 位址空間的上端。</span><span class="sxs-lookup"><span data-stu-id="0f699-152">A good practice is to set the address space for the gateway subnet at the upper end of the VNet address space.</span></span> <span data-ttu-id="0f699-153">圖中所示的範例是使用 10.20.255.224/27。</span><span class="sxs-lookup"><span data-stu-id="0f699-153">The example shown in the diagram uses 10.20.255.224/27.</span></span>  <span data-ttu-id="0f699-154">以下是計算 [CIDR] 的快速程序：</span><span class="sxs-lookup"><span data-stu-id="0f699-154">Here is a quick procedure to calculate the [CIDR]:</span></span>

1. <span data-ttu-id="0f699-155">將 VNet 位址空間中的變動位元數設為 1 (根據閘道正在使用的位元數)，然後將其他位元數設為 0。</span><span class="sxs-lookup"><span data-stu-id="0f699-155">Set the variable bits in the address space of the VNet to 1, up to the bits being used by the gateway subnet, then set the remaining bits to 0.</span></span>
2. <span data-ttu-id="0f699-156">將產生的位元數轉換成十進位，並將其表示為前置長度設為閘道子網路大小的位址空間。</span><span class="sxs-lookup"><span data-stu-id="0f699-156">Convert the resulting bits to decimal and express it as an address space with the prefix length set to the size of the gateway subnet.</span></span>

<span data-ttu-id="0f699-157">例如，IP 位址範圍是 10.20.0.0/16 的 VNet，套用上述步驟 1 後會變成 10.20.0b11111111.0b11100000。</span><span class="sxs-lookup"><span data-stu-id="0f699-157">For example, for a VNet with an IP address range of 10.20.0.0/16, applying step #1 above becomes 10.20.0b11111111.0b11100000.</span></span>  <span data-ttu-id="0f699-158">將其轉換成十進位並表示為位址空間後會產生 10.20.255.224/27。</span><span class="sxs-lookup"><span data-stu-id="0f699-158">Converting that to decimal and expressing it as an address space yields 10.20.255.224/27.</span></span> 

> [!WARNING]
> <span data-ttu-id="0f699-159">請勿將任何虛擬機器部署至閘道子網路。</span><span class="sxs-lookup"><span data-stu-id="0f699-159">Do not deploy any VMs to the gateway subnet.</span></span> <span data-ttu-id="0f699-160">此外，請勿指派 NSG 至此子網路，因為會導致閘道停止運作。</span><span class="sxs-lookup"><span data-stu-id="0f699-160">Also, do not assign an NSG to this subnet, as it will cause the gateway to stop functioning.</span></span>
> 
> 

### <a name="virtual-network-gateway"></a><span data-ttu-id="0f699-161">虛擬網路閘道</span><span class="sxs-lookup"><span data-stu-id="0f699-161">Virtual network gateway</span></span>

<span data-ttu-id="0f699-162">配置虛擬網路閘道的公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="0f699-162">Allocate a public IP address for the virtual network gateway.</span></span>

<span data-ttu-id="0f699-163">在閘道子網路中建立虛擬網路閘道，並為其指派新配置的公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="0f699-163">Create the virtual network gateway in the gateway subnet and assign it the newly allocated public IP address.</span></span> <span data-ttu-id="0f699-164">使用最符合您需求且由您的 VPN 設備啟用的閘道類型：</span><span class="sxs-lookup"><span data-stu-id="0f699-164">Use the gateway type that most closely matches your requirements and that is enabled by your VPN appliance:</span></span>

- <span data-ttu-id="0f699-165">如果您需要根據原則準則 (例如位址首碼)，密切地控制要求的路由方式，可建立[原則型閘道][policy-based-routing]。</span><span class="sxs-lookup"><span data-stu-id="0f699-165">Create a [policy-based gateway][policy-based-routing] if you need to closely control how requests are routed based on policy criteria such as address prefixes.</span></span> <span data-ttu-id="0f699-166">原則型閘道會使用靜態路由，並只適用於站對站連線。</span><span class="sxs-lookup"><span data-stu-id="0f699-166">Policy-based gateways use static routing, and only work with site-to-site connections.</span></span>

- <span data-ttu-id="0f699-167">如果您使用 RRAS 連線至內部部署網路、支援多站台或跨區域連線或實作 VNet 對 VNet 連線 (包括跨越多個 Vnet 的路由)，可建立[路由型閘道][route-based-routing]。</span><span class="sxs-lookup"><span data-stu-id="0f699-167">Create a [route-based gateway][route-based-routing] if you connect to the on-premises network using RRAS, support multi-site or cross-region connections, or implement VNet-to-VNet connections (including routes that traverse multiple VNets).</span></span> <span data-ttu-id="0f699-168">路由型閘道會使用動態路由來指引網路之間的流量。</span><span class="sxs-lookup"><span data-stu-id="0f699-168">Route-based gateways use dynamic routing to direct traffic between networks.</span></span> <span data-ttu-id="0f699-169">動態路由在網路路徑中的容錯能力比靜態路由好，因為動態路由可以嘗試替代路由。</span><span class="sxs-lookup"><span data-stu-id="0f699-169">They can tolerate failures in the network path better than static routes because they can try alternative routes.</span></span> <span data-ttu-id="0f699-170">路由型閘道也可減少額外負荷，因為當網路位址變更時，路由可能不需要以手動方式更新。</span><span class="sxs-lookup"><span data-stu-id="0f699-170">Route-based gateways can also reduce the management overhead because routes might not need to be updated manually when network addresses change.</span></span>

<span data-ttu-id="0f699-171">如需支援的 VPN 設備清單，請參閱[關於站對站 VPN 閘道連線的 VPN 裝置][vpn-appliances]。</span><span class="sxs-lookup"><span data-stu-id="0f699-171">For a list of supported VPN appliances, see [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliances].</span></span>

> [!NOTE]
> <span data-ttu-id="0f699-172">一旦建立閘道之後，您必須刪除並重新建立閘道才能變更閘道類型。</span><span class="sxs-lookup"><span data-stu-id="0f699-172">After the gateway has been created, you cannot change between gateway types without deleting and re-creating the gateway.</span></span>
> 
> 

<span data-ttu-id="0f699-173">選取最符合您輸送量需求的 Azure VPN 閘道 SKU。</span><span class="sxs-lookup"><span data-stu-id="0f699-173">Select the Azure VPN gateway SKU that most closely matches your throughput requirements.</span></span> <span data-ttu-id="0f699-174">有三個 SKU 適用於Azure VPN 閘道，如下表所示。</span><span class="sxs-lookup"><span data-stu-id="0f699-174">Azure VPN gateway is available in three SKUs shown in the following table.</span></span> 

| <span data-ttu-id="0f699-175">SKU</span><span class="sxs-lookup"><span data-stu-id="0f699-175">SKU</span></span> | <span data-ttu-id="0f699-176">VPN 輸送量</span><span class="sxs-lookup"><span data-stu-id="0f699-176">VPN Throughput</span></span> | <span data-ttu-id="0f699-177">IPSec 通道數上限</span><span class="sxs-lookup"><span data-stu-id="0f699-177">Max IPSec Tunnels</span></span> |
| --- | --- | --- |
| <span data-ttu-id="0f699-178">基本</span><span class="sxs-lookup"><span data-stu-id="0f699-178">Basic</span></span> |<span data-ttu-id="0f699-179">100 Mbps</span><span class="sxs-lookup"><span data-stu-id="0f699-179">100 Mbps</span></span> |<span data-ttu-id="0f699-180">10</span><span class="sxs-lookup"><span data-stu-id="0f699-180">10</span></span> |
| <span data-ttu-id="0f699-181">標準</span><span class="sxs-lookup"><span data-stu-id="0f699-181">Standard</span></span> |<span data-ttu-id="0f699-182">100 Mbps</span><span class="sxs-lookup"><span data-stu-id="0f699-182">100 Mbps</span></span> |<span data-ttu-id="0f699-183">10</span><span class="sxs-lookup"><span data-stu-id="0f699-183">10</span></span> |
| <span data-ttu-id="0f699-184">高效能</span><span class="sxs-lookup"><span data-stu-id="0f699-184">High Performance</span></span> |<span data-ttu-id="0f699-185">200 Mbps</span><span class="sxs-lookup"><span data-stu-id="0f699-185">200 Mbps</span></span> |<span data-ttu-id="0f699-186">30</span><span class="sxs-lookup"><span data-stu-id="0f699-186">30</span></span> |

> [!NOTE]
> <span data-ttu-id="0f699-187">基本 SKU 與 Azure ExpressRoute 不相容。</span><span class="sxs-lookup"><span data-stu-id="0f699-187">The Basic SKU is not compatible with Azure ExpressRoute.</span></span> <span data-ttu-id="0f699-188">建立閘道之後，您可以[變更 SKU][changing-SKUs]。</span><span class="sxs-lookup"><span data-stu-id="0f699-188">You can [change the SKU][changing-SKUs] after the gateway has been created.</span></span>
> 
> 

<span data-ttu-id="0f699-189">您須根據閘道上所佈建及可用時間量支付費用。</span><span class="sxs-lookup"><span data-stu-id="0f699-189">You are charged based on the amount of time that the gateway is provisioned and available.</span></span> <span data-ttu-id="0f699-190">請參閱 [VPN 閘道價格][azure-gateway-charges]。</span><span class="sxs-lookup"><span data-stu-id="0f699-190">See [VPN Gateway Pricing][azure-gateway-charges].</span></span>

<span data-ttu-id="0f699-191">建立閘道子網路的路由規則，將傳入的應用程式流量從閘道引導至內部負載平衡器，而不允許將要求直接傳遞至應用程式虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="0f699-191">Create routing rules for the gateway subnet that direct incoming application traffic from the gateway to the internal load balancer, rather than allowing requests to pass directly to the application VMs.</span></span>

### <a name="on-premises-network-connection"></a><span data-ttu-id="0f699-192">內部部署網路連線</span><span class="sxs-lookup"><span data-stu-id="0f699-192">On-premises network connection</span></span>

<span data-ttu-id="0f699-193">建立區域網路閘道。</span><span class="sxs-lookup"><span data-stu-id="0f699-193">Create a local network gateway.</span></span> <span data-ttu-id="0f699-194">指定內部部署 VPN 設備的公用 IP 位址，以及內部部署網路的位址空間。</span><span class="sxs-lookup"><span data-stu-id="0f699-194">Specify the public IP address of the on-premises VPN appliance, and the address space of the on-premises network.</span></span> <span data-ttu-id="0f699-195">請注意，內部部署 VPN 設備必須有可以存取的公用 IP 位址，讓 Azure VPN 閘道中的區域網路閘道進行存取。</span><span class="sxs-lookup"><span data-stu-id="0f699-195">Note that the on-premises VPN appliance must have a public IP address that can be accessed by the local network gateway in Azure VPN Gateway.</span></span> <span data-ttu-id="0f699-196">在網路位址轉譯 (NAT) 裝置後面找不到 VPN 裝置。</span><span class="sxs-lookup"><span data-stu-id="0f699-196">The VPN device cannot be located behind a network address translation (NAT) device.</span></span>

<span data-ttu-id="0f699-197">建立虛擬網路閘道與區域網路閘道的站對站連線。</span><span class="sxs-lookup"><span data-stu-id="0f699-197">Create a site-to-site connection for the virtual network gateway and the local network gateway.</span></span> <span data-ttu-id="0f699-198">選取站對站 (IPSec) 連線類型，並指定共用金鑰。</span><span class="sxs-lookup"><span data-stu-id="0f699-198">Select the site-to-site (IPSec) connection type, and specify the shared key.</span></span> <span data-ttu-id="0f699-199">Azure VPN 閘道的站對站加密是以 IPSec 通訊協定為基礎，並使用預先共用的金鑰來進行驗證。</span><span class="sxs-lookup"><span data-stu-id="0f699-199">Site-to-site encryption with the Azure VPN gateway is based on the IPSec protocol, using preshared keys for authentication.</span></span> <span data-ttu-id="0f699-200">您會在建立 Azure VPN 閘道時指定金鑰。</span><span class="sxs-lookup"><span data-stu-id="0f699-200">You specify the key when you create the Azure VPN gateway.</span></span> <span data-ttu-id="0f699-201">您必須使用相同金鑰來設定在內部部署執行的 VPN 設備。</span><span class="sxs-lookup"><span data-stu-id="0f699-201">You must configure the VPN appliance running on-premises with the same key.</span></span> <span data-ttu-id="0f699-202">目前不支援其他驗證機制。</span><span class="sxs-lookup"><span data-stu-id="0f699-202">Other authentication mechanisms are not currently supported.</span></span>

<span data-ttu-id="0f699-203">請確認內部部署路由基礎結構已設定為，將原本目的地是 Azure VNet 位址的要求轉送至 VPN 裝置。</span><span class="sxs-lookup"><span data-stu-id="0f699-203">Ensure that the on-premises routing infrastructure is configured to forward requests intended for addresses in the Azure VNet to the VPN device.</span></span>

<span data-ttu-id="0f699-204">在內部部署網路中，開啟任何雲端應用程式所需的連接埠。</span><span class="sxs-lookup"><span data-stu-id="0f699-204">Open any ports required by the cloud application in the on-premises network.</span></span>

<span data-ttu-id="0f699-205">測試連線以確認下列事項：</span><span class="sxs-lookup"><span data-stu-id="0f699-205">Test the connection to verify that:</span></span>

* <span data-ttu-id="0f699-206">內部部署 VPN 設備透過 Azure VPN 閘道，正確地將流量路由至雲端應用程式。</span><span class="sxs-lookup"><span data-stu-id="0f699-206">The on-premises VPN appliance correctly routes traffic to the cloud application through the Azure VPN gateway.</span></span>
* <span data-ttu-id="0f699-207">VNet 正確地將流量路由回內部部署網路。</span><span class="sxs-lookup"><span data-stu-id="0f699-207">The VNet correctly routes traffic back to the on-premises network.</span></span>
* <span data-ttu-id="0f699-208">兩個方向中禁止的流量已正確地封鎖。</span><span class="sxs-lookup"><span data-stu-id="0f699-208">Prohibited traffic in both directions is blocked correctly.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="0f699-209">延展性考量</span><span class="sxs-lookup"><span data-stu-id="0f699-209">Scalability considerations</span></span>

<span data-ttu-id="0f699-210">您可以從基本或標準 VPN 閘道 SKU 移到高效能 VPN SKU，以達到所限制的垂直延展性。</span><span class="sxs-lookup"><span data-stu-id="0f699-210">You can achieve limited vertical scalability by moving from the Basic or Standard VPN Gateway SKUs to the High Performance VPN SKU.</span></span>

<span data-ttu-id="0f699-211">對於預期有大量 VPN 流量的 Vnet，請考慮將不同工作負載分散到較小的個別 VNet，以及為每一個個別 VNet 設定 VPN 閘道。</span><span class="sxs-lookup"><span data-stu-id="0f699-211">For VNets that expect a large volume of VPN traffic, consider distributing the different workloads into separate smaller VNets and configuring a VPN gateway for each of them.</span></span>

<span data-ttu-id="0f699-212">您可以水平或垂直分割 VNet。</span><span class="sxs-lookup"><span data-stu-id="0f699-212">You can partition the VNet either horizontally or vertically.</span></span> <span data-ttu-id="0f699-213">若要以水平方式分割，請將一些虛擬機器執行個體從每個層移至新 VNet 的子網路。</span><span class="sxs-lookup"><span data-stu-id="0f699-213">To partition horizontally, move some VM instances from each tier into subnets of the new VNet.</span></span> <span data-ttu-id="0f699-214">結果會是每個 VNet 都具有相同的結構與功能。</span><span class="sxs-lookup"><span data-stu-id="0f699-214">The result is that each VNet has the same structure and functionality.</span></span> <span data-ttu-id="0f699-215">若要以垂直方式分割，請重新設計每個層以將功能分成不同的邏輯區域 (例如處理訂單、發票開立和客戶帳戶管理等)。</span><span class="sxs-lookup"><span data-stu-id="0f699-215">To partition vertically, redesign each tier to divide the functionality into different logical areas (such as handling orders, invoicing, customer account management, and so on).</span></span> <span data-ttu-id="0f699-216">然後每個功能區域就可以放在自己的 VNet 中。</span><span class="sxs-lookup"><span data-stu-id="0f699-216">Each functional area can then be placed in its own VNet.</span></span>

<span data-ttu-id="0f699-217">複寫 VNet 中的內部部署 Active Directory 網域控制站，以及在 VNet 中實作 DNS，可以協助減少部分安全性相關流量與系統管理流量從內部部署流至雲端。</span><span class="sxs-lookup"><span data-stu-id="0f699-217">Replicating an on-premises Active Directory domain controller in the VNet, and implementing DNS in the VNet, can help to reduce some of the security-related and administrative traffic flowing from on-premises to the cloud.</span></span> <span data-ttu-id="0f699-218">如需詳細資訊，請參閱[將 Active Directory Domain Services (AD DS) 擴充至 Azure][adds-extend-domain]。</span><span class="sxs-lookup"><span data-stu-id="0f699-218">For more information, see [Extending Active Directory Domain Services (AD DS) to Azure][adds-extend-domain].</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="0f699-219">可用性考量</span><span class="sxs-lookup"><span data-stu-id="0f699-219">Availability considerations</span></span>

<span data-ttu-id="0f699-220">如果您需要確保內部部署網路仍然可供 Azure VPN 閘道使用，可為內部部署 VPN 閘道實作容錯移轉叢集。</span><span class="sxs-lookup"><span data-stu-id="0f699-220">If you need to ensure that the on-premises network remains available to the Azure VPN gateway, implement a failover cluster for the on-premises VPN gateway.</span></span>

<span data-ttu-id="0f699-221">如果您的組織具有多個內部部署站台，可對一個或多個 Azure Vnet 建立[多站台連線][vpn-gateway-multi-site]。</span><span class="sxs-lookup"><span data-stu-id="0f699-221">If your organization has multiple on-premises sites, create [multi-site connections][vpn-gateway-multi-site] to one or more Azure VNets.</span></span> <span data-ttu-id="0f699-222">此方法需要建立動態 (路由型) 路由，因此請確定內部部署 VPN 閘道支援此功能。</span><span class="sxs-lookup"><span data-stu-id="0f699-222">This approach requires dynamic (route-based) routing, so make sure that the on-premises VPN gateway supports this feature.</span></span>

<span data-ttu-id="0f699-223">如需有關服務等級協定的詳細資訊，請參閱 [VPN 閘道的 SLA][sla-for-vpn-gateway]。</span><span class="sxs-lookup"><span data-stu-id="0f699-223">For details about service level agreements, see [SLA for VPN Gateway][sla-for-vpn-gateway].</span></span> 

## <a name="manageability-considerations"></a><span data-ttu-id="0f699-224">管理性考量</span><span class="sxs-lookup"><span data-stu-id="0f699-224">Manageability considerations</span></span>

<span data-ttu-id="0f699-225">監視內部部署 VPN 設備中的診斷資訊。</span><span class="sxs-lookup"><span data-stu-id="0f699-225">Monitor diagnostic information from on-premises VPN appliances.</span></span> <span data-ttu-id="0f699-226">此流程取決於 VPN 設備所提供的功能。</span><span class="sxs-lookup"><span data-stu-id="0f699-226">This process depends on the features provided by the VPN appliance.</span></span> <span data-ttu-id="0f699-227">例如，如果您使用 Windows Server 2012 上的路由及遠端存取服務，也就是 [RRAS 記錄][rras-logging]。</span><span class="sxs-lookup"><span data-stu-id="0f699-227">For example, if you are using the Routing and Remote Access Service on Windows Server 2012, [RRAS logging][rras-logging].</span></span>

<span data-ttu-id="0f699-228">請使用 [Azure VPN 閘道診斷][gateway-diagnostic-logs]來擷取連線問題的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="0f699-228">Use [Azure VPN gateway diagnostics][gateway-diagnostic-logs] to capture information about connectivity issues.</span></span> <span data-ttu-id="0f699-229">這些記錄可用來追蹤資訊，例如連線要求的來源和目的地、使用的通訊協定，以及建立連線的方式 (或嘗試失敗的原因)。</span><span class="sxs-lookup"><span data-stu-id="0f699-229">These logs can be used to track information such as the source and destinations of connection requests, which protocol was used, and how the connection was established (or why the attempt failed).</span></span>

<span data-ttu-id="0f699-230">在 Azure 入口網站中，使用可用的稽核記錄來監視 Azure VPN 閘道的作業記錄。</span><span class="sxs-lookup"><span data-stu-id="0f699-230">Monitor the operational logs of the Azure VPN gateway using the audit logs available in the Azure portal.</span></span> <span data-ttu-id="0f699-231">個別的記錄適用於區域網路閘道、Azure 網路閘道和連線。</span><span class="sxs-lookup"><span data-stu-id="0f699-231">Separate logs are available for the local network gateway, the Azure network gateway, and the connection.</span></span> <span data-ttu-id="0f699-232">此資訊可用來追蹤閘道上所做的任何變更，而且有助於了解先前可運作的閘道為什麼會停止運作。</span><span class="sxs-lookup"><span data-stu-id="0f699-232">This information can be used to track any changes made to the gateway, and can be useful if a previously functioning gateway stops working for some reason.</span></span>

<span data-ttu-id="0f699-233">![[2]][2]</span><span class="sxs-lookup"><span data-stu-id="0f699-233">![[2]][2]</span></span>

<span data-ttu-id="0f699-234">監視連線，並追蹤連線失敗事件。</span><span class="sxs-lookup"><span data-stu-id="0f699-234">Monitor connectivity, and track connectivity failure events.</span></span> <span data-ttu-id="0f699-235">您可以使用監視套件 (例如 [Nagios][nagios]) 來擷取和提報此資訊。</span><span class="sxs-lookup"><span data-stu-id="0f699-235">You can use a monitoring package such as [Nagios][nagios] to capture and report this information.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="0f699-236">安全性考量</span><span class="sxs-lookup"><span data-stu-id="0f699-236">Security considerations</span></span>

<span data-ttu-id="0f699-237">針對每個 VPN 閘道產生不同的共用金鑰。</span><span class="sxs-lookup"><span data-stu-id="0f699-237">Generate a different shared key for each VPN gateway.</span></span> <span data-ttu-id="0f699-238">使用強式共用金鑰，以協助抵禦暴力密碼破解攻擊。</span><span class="sxs-lookup"><span data-stu-id="0f699-238">Use a strong shared key to help resist brute-force attacks.</span></span>

> [!NOTE]
> <span data-ttu-id="0f699-239">目前，您無法使用 Azure Key Vault 預先共用 Azure VPN 閘道的金鑰。</span><span class="sxs-lookup"><span data-stu-id="0f699-239">Currently, you cannot use Azure Key Vault to preshare keys for the Azure VPN gateway.</span></span>
> 
> 

<span data-ttu-id="0f699-240">請確定內部部署 VPN 設備使用的加密方法[與 Azure VPN 閘道相容][vpn-appliance-ipsec]。</span><span class="sxs-lookup"><span data-stu-id="0f699-240">Ensure that the on-premises VPN appliance uses an encryption method that is [compatible with the Azure VPN gateway][vpn-appliance-ipsec].</span></span> <span data-ttu-id="0f699-241">針對原則型路由，Azure VPN 閘道支援 AES256、AES128 和 3DES 加密演算法。</span><span class="sxs-lookup"><span data-stu-id="0f699-241">For policy-based routing, the Azure VPN gateway supports the AES256, AES128, and 3DES encryption algorithms.</span></span> <span data-ttu-id="0f699-242">路由型閘道支援 AES256 和 3DES。</span><span class="sxs-lookup"><span data-stu-id="0f699-242">Route-based gateways support AES256 and 3DES.</span></span>

<span data-ttu-id="0f699-243">如果您的內部部署 VPN 設備位在與網際網路之間有防火牆的周邊網路 (DMZ) 上，您可能必須設定[其他防火牆規則][additional-firewall-rules]，以允許站對站 VPN 連線。</span><span class="sxs-lookup"><span data-stu-id="0f699-243">If your on-premises VPN appliance is on a perimeter network (DMZ) that has a firewall between the perimeter network and the Internet, you might have to configure [additional firewall rules][additional-firewall-rules] to allow the site-to-site VPN connection.</span></span>

<span data-ttu-id="0f699-244">如果 VNet 中的應用程式會將資料傳送至網際網路，請考慮[實作強制通道][forced-tunneling]，以透過內部部署網路來路由所有網際網路繫結流量。</span><span class="sxs-lookup"><span data-stu-id="0f699-244">If the application in the VNet sends data to the Internet, consider [implementing forced tunneling][forced-tunneling] to route all Internet-bound traffic through the on-premises network.</span></span> <span data-ttu-id="0f699-245">此方法可讓您稽核應用程式在內部部署基礎結構中所做的傳出要求。</span><span class="sxs-lookup"><span data-stu-id="0f699-245">This approach enables you to audit outgoing requests made by the application from the on-premises infrastructure.</span></span>

> [!NOTE]
> <span data-ttu-id="0f699-246">強制通道可能會影響與 Azure 服務 (例如儲存體服務) 和 Windows 授權管理員的連線。</span><span class="sxs-lookup"><span data-stu-id="0f699-246">Forced tunneling can impact connectivity to Azure services (the Storage Service, for example) and the Windows license manager.</span></span>
> 
> 


## <a name="troubleshooting"></a><span data-ttu-id="0f699-247">疑難排解</span><span class="sxs-lookup"><span data-stu-id="0f699-247">Troubleshooting</span></span> 

<span data-ttu-id="0f699-248">如需針對常見 VPN 相關錯誤進行疑難排解的一般資訊，請參閱[針對常見 VPN 相關錯誤進行移難排解][troubleshooting-vpn-errors]。</span><span class="sxs-lookup"><span data-stu-id="0f699-248">For general information on troubleshooting common VPN-related errors, see [Troubleshooting common VPN related errors][troubleshooting-vpn-errors].</span></span>

<span data-ttu-id="0f699-249">下列建議有助於判斷您的內部部署 VPN 設備是否正常運作。</span><span class="sxs-lookup"><span data-stu-id="0f699-249">The following recommendations are useful for determining if your on-premises VPN appliance is functioning correctly.</span></span>

- <span data-ttu-id="0f699-250">**請查看 VPN 設備產生的任何記錄，以檢查是否有錯誤或失敗。**</span><span class="sxs-lookup"><span data-stu-id="0f699-250">**Check any log files generated by the VPN appliance for errors or failures.**</span></span>

    <span data-ttu-id="0f699-251">這可協助您判斷 VPN 設備是否正確運作。</span><span class="sxs-lookup"><span data-stu-id="0f699-251">This will help you determine if the VPN appliance is functioning correctly.</span></span> <span data-ttu-id="0f699-252">此資訊的位置會因為您的設備不同而有所差異。</span><span class="sxs-lookup"><span data-stu-id="0f699-252">The location of this information will vary according to your appliance.</span></span> <span data-ttu-id="0f699-253">例如，如果您在 Windows Server 2012 上使用 RRAS，您可以使用下列 PowerShell 命令來顯示 RRAS 服務的錯誤事件資訊：</span><span class="sxs-lookup"><span data-stu-id="0f699-253">For example, if you are using RRAS on Windows Server 2012, you can use the following PowerShell command to display error event information for the RRAS service:</span></span>

    ```PowerShell
    Get-EventLog -LogName System -EntryType Error -Source RemoteAccess | Format-List -Property *
    ```

    <span data-ttu-id="0f699-254">每個項目的「訊息」屬性會提供錯誤的描述。</span><span class="sxs-lookup"><span data-stu-id="0f699-254">The *Message* property of each entry provides a description of the error.</span></span> <span data-ttu-id="0f699-255">一些常見的範例包括：</span><span class="sxs-lookup"><span data-stu-id="0f699-255">Some common examples are:</span></span>

        - Inability to connect, possibly due to an incorrect IP address specified for the Azure VPN gateway in the RRAS VPN network interface configuration.

        ```
        EventID            : 20111
        MachineName        : on-prem-vm
        Data               : {41, 3, 0, 0}
        Index              : 14231
        Category           : (0)
        CategoryNumber     : 0
        EntryType          : Error
        Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A demand dial connection to the remote
                             interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                             successfully because of the  following error: The network connection between your computer and
                             the VPN server could not be established because the remote server is not responding. This could
                             be because one of the network devices (for example, firewalls, NAT, routers, and so on) between your computer
                             and the remote server is not configured to allow VPN connections. Please contact your
                             Administrator or your service provider to determine which device may be causing the problem.
        Source             : RemoteAccess
        ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, The network connection between
                             your computer and the VPN server could not be established because the remote server is not
                             responding. This could be because one of the network devices (for example, firewalls, NAT, routers, and so on)
                             between your computer and the remote server is not configured to allow VPN connections. Please
                             contact your Administrator or your service provider to determine which device may be causing the
                             problem.}
        InstanceId         : 20111
        TimeGenerated      : 3/18/2016 1:26:02 PM
        TimeWritten        : 3/18/2016 1:26:02 PM
        UserName           :
        Site               :
        Container          :
        ```

        - The wrong shared key being specified in the RRAS VPN network interface configuration.

        ```
        EventID            : 20111
        MachineName        : on-prem-vm
        Data               : {233, 53, 0, 0}
        Index              : 14245
        Category           : (0)
        CategoryNumber     : 0
        EntryType          : Error
        Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A demand dial connection to the remote
                             interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                             successfully because of the  following error: Internet key exchange (IKE) authentication credentials are unacceptable.

        Source             : RemoteAccess
        ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, IKE authentication credentials are
                             unacceptable.
                             }
        InstanceId         : 20111
        TimeGenerated      : 3/18/2016 1:34:22 PM
        TimeWritten        : 3/18/2016 1:34:22 PM
        UserName           :
        Site               :
        Container          :
        ```

    <span data-ttu-id="0f699-256">您也可以使用下列 PowerShell 命令，取得嘗試透過 RRAS 服務連線的相關事件記錄資訊：</span><span class="sxs-lookup"><span data-stu-id="0f699-256">You can also obtain event log information about attempts to connect through the RRAS service using the following PowerShell command:</span></span> 

    ```
    Get-EventLog -LogName Application -Source RasClient | Format-List -Property *
    ```

    <span data-ttu-id="0f699-257">在連線失敗的事件中，此記錄會包含看起來類似下列的錯誤：</span><span class="sxs-lookup"><span data-stu-id="0f699-257">In the event of a failure to connect, this log will contain errors that look similar to the following:</span></span>

    ```
    EventID            : 20227
    MachineName        : on-prem-vm
    Data               : {}
    Index              : 4203
    Category           : (0)
    CategoryNumber     : 0
    EntryType          : Error
    Message            : CoId={B4000371-A67F-452F-AA4C-3125AA9CFC78}: The user SYSTEM dialed a connection named
                         AzureGateway that has failed. The error code returned on failure is 809.
    Source             : RasClient
    ReplacementStrings : {{B4000371-A67F-452F-AA4C-3125AA9CFC78}, SYSTEM, AzureGateway, 809}
    InstanceId         : 20227
    TimeGenerated      : 3/18/2016 1:29:21 PM
    TimeWritten        : 3/18/2016 1:29:21 PM
    UserName           :
    Site               :
    Container          :
    ```

- <span data-ttu-id="0f699-258">**請檢查 VPN 閘道之間的連線和路由。**</span><span class="sxs-lookup"><span data-stu-id="0f699-258">**Verify connectivity and routing across the VPN gateway.**</span></span>

    <span data-ttu-id="0f699-259">VPN 設備可能沒有正確地透過 Azure VPN 閘道路由流量。</span><span class="sxs-lookup"><span data-stu-id="0f699-259">The VPN appliance may not be correctly routing traffic through the Azure VPN Gateway.</span></span> <span data-ttu-id="0f699-260">請使用 [PsPing][psping] 等工具來檢查 VPN 閘道之間的連線和路由。</span><span class="sxs-lookup"><span data-stu-id="0f699-260">Use a tool such as [PsPing][psping] to verify connectivity and routing across the VPN gateway.</span></span> <span data-ttu-id="0f699-261">例如，若要測試內部部署機器是否能連線至位於 VNet 上的 Web 伺服器，請執行下列命令 (使用 Web 伺服器的位址取代 `<<web-server-address>>`)：</span><span class="sxs-lookup"><span data-stu-id="0f699-261">For example, to test connectivity from an on-premises machine to a web server located on the VNet, run the following command (replacing `<<web-server-address>>` with the address of the web server):</span></span>

    ```
    PsPing -t <<web-server-address>>:80
    ```

    <span data-ttu-id="0f699-262">如果內部部署機器可以將流量路由至 Web 伺服器，您應該會看到類似下列的輸出：</span><span class="sxs-lookup"><span data-stu-id="0f699-262">If the on-premises machine can route traffic to the web server, you should see output similar to the following:</span></span>

    ```
    D:\PSTools>psping -t 10.20.0.5:80

    PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2014 Mark Russinovich
    Sysinternals - www.sysinternals.com

    TCP connect to 10.20.0.5:80:
    Infinite iterations (warmup 1) connecting test:
    Connecting to 10.20.0.5:80 (warmup): 6.21ms
    Connecting to 10.20.0.5:80: 3.79ms
    Connecting to 10.20.0.5:80: 3.44ms
    Connecting to 10.20.0.5:80: 4.81ms

      Sent = 3, Received = 3, Lost = 0 (0% loss),
      Minimum = 3.44ms, Maximum = 4.81ms, Average = 4.01ms
    ```

    <span data-ttu-id="0f699-263">如果內部部署機器無法與指定的目的地通訊，您會看到像這樣的訊息：</span><span class="sxs-lookup"><span data-stu-id="0f699-263">If the on-premises machine cannot communicate with the specified destination, you will see messages like this:</span></span>

    ```
    D:\PSTools>psping -t 10.20.1.6:80

    PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2014 Mark Russinovich
    Sysinternals - www.sysinternals.com

    TCP connect to 10.20.1.6:80:
    Infinite iterations (warmup 1) connecting test:
    Connecting to 10.20.1.6:80 (warmup): This operation returned because the timeout period expired.
    Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
    Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
    Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
    Connecting to 10.20.1.6:80:
      Sent = 3, Received = 0, Lost = 3 (100% loss),
      Minimum = 0.00ms, Maximum = 0.00ms, Average = 0.00ms
    ```

- <span data-ttu-id="0f699-264">**請確認內部部署防火牆允許 VPN 流量通過且正確的連接埠已開啟。**</span><span class="sxs-lookup"><span data-stu-id="0f699-264">**Verify that the on-premises firewall allows VPN traffic to pass and that the correct ports are opened.**</span></span>

- <span data-ttu-id="0f699-265">**請確認內部部署 VPN 設備使用的加密方法[與 Azure VPN 閘道相容][vpn-appliance]。**</span><span class="sxs-lookup"><span data-stu-id="0f699-265">**Verify that the on-premises VPN appliance uses an encryption method that is [compatible with the Azure VPN gateway][vpn-appliance].**</span></span> <span data-ttu-id="0f699-266">針對原則型路由，Azure VPN 閘道支援 AES256、AES128 和 3DES 加密演算法。</span><span class="sxs-lookup"><span data-stu-id="0f699-266">For policy-based routing, the Azure VPN gateway supports the AES256, AES128, and 3DES encryption algorithms.</span></span> <span data-ttu-id="0f699-267">路由型閘道支援 AES256 和 3DES。</span><span class="sxs-lookup"><span data-stu-id="0f699-267">Route-based gateways support AES256 and 3DES.</span></span>

<span data-ttu-id="0f699-268">下列建議有助於判斷 Azure VPN 閘道是否有問題：</span><span class="sxs-lookup"><span data-stu-id="0f699-268">The following recommendations are useful for determining if there is a problem with the Azure VPN gateway:</span></span>

- <span data-ttu-id="0f699-269">**檢查 [Azure VPN 閘道診斷記錄][gateway-diagnostic-logs]，以查看是否有潛在問題。**</span><span class="sxs-lookup"><span data-stu-id="0f699-269">**Examine [Azure VPN gateway diagnostic logs][gateway-diagnostic-logs] for potential issues.**</span></span>

- <span data-ttu-id="0f699-270">**確認 Azure VPN 閘道與內部部署 VPN 設備是使用相同的共用驗證金鑰進行設定。**</span><span class="sxs-lookup"><span data-stu-id="0f699-270">**Verify that the Azure VPN gateway and on-premises VPN appliance are configured with the same shared authentication key.**</span></span>

    <span data-ttu-id="0f699-271">您可以使用下列 Azure CLI 命令，檢視 Azure VPN 閘道所儲存的共用金鑰：</span><span class="sxs-lookup"><span data-stu-id="0f699-271">You can view the shared key stored by the Azure VPN gateway using the following Azure CLI command:</span></span>

    ```
    azure network vpn-connection shared-key show <<resource-group>> <<vpn-connection-name>>
    ```

    <span data-ttu-id="0f699-272">您可以使用適用於您內部部署 VPN 設備的命令，來顯示該設備上設定的共用金鑰。</span><span class="sxs-lookup"><span data-stu-id="0f699-272">Use the command appropriate for your on-premises VPN appliance to show the shared key configured for that appliance.</span></span>

    <span data-ttu-id="0f699-273">確認持有 Azure VPN 閘道的 GatewaySubnet 子網路沒有與 NSG 建立關聯。</span><span class="sxs-lookup"><span data-stu-id="0f699-273">Verify that the *GatewaySubnet* subnet holding the Azure VPN gateway is not associated with an NSG.</span></span>

    <span data-ttu-id="0f699-274">您可使用下列 Azure CLI 命令檢視子網路詳細資料：</span><span class="sxs-lookup"><span data-stu-id="0f699-274">You can view the subnet details using the following Azure CLI command:</span></span>

    ```
    azure network vnet subnet show -g <<resource-group>> -e <<vnet-name>> -n GatewaySubnet
    ```

    <span data-ttu-id="0f699-275">請確定沒有名為「網路安全性群組識別碼」的資料欄位。下列範例會示範具有指派 NSG (VPN-Gateway-Group) 的 GatewaySubnet 執行個體結果。</span><span class="sxs-lookup"><span data-stu-id="0f699-275">Ensure there is no data field named *Network Security Group id*. The following example shows the results for an instance of the *GatewaySubnet* that has an assigned NSG (*VPN-Gateway-Group*).</span></span> <span data-ttu-id="0f699-276">如果您有對此 NSG 定義任何規則，此動作可能會使得閘道無法正常運作。</span><span class="sxs-lookup"><span data-stu-id="0f699-276">This can prevent the gateway from working correctly if there are any rules defined for this NSG.</span></span>

    ```
    C:\>azure network vnet subnet show -g profx-prod-rg -e profx-vnet -n GatewaySubnet
        info:    Executing command network vnet subnet show
        + Looking up virtual network "profx-vnet"
        + Looking up the subnet "GatewaySubnet"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/virtualNetworks/profx-vnet/subnets/GatewaySubnet
        data:    Name                            : GatewaySubnet
        data:    Provisioning state              : Succeeded
        data:    Address prefix                  : 10.20.3.0/27
        data:    Network Security Group id       : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/networkSecurityGroups/VPN-Gateway-Group
        info:    network vnet subnet show command OK
    ```

- <span data-ttu-id="0f699-277">**請確認 Azure VNet 中的虛擬機器設定為允許流量從外部 VNet 傳入。**</span><span class="sxs-lookup"><span data-stu-id="0f699-277">**Verify that the virtual machines in the Azure VNet are configured to permit traffic coming in from outside the VNet.**</span></span>

    <span data-ttu-id="0f699-278">如果有任何 NSG 規則與包含這些虛擬機器的子網路相關聯，請檢查這些規則。</span><span class="sxs-lookup"><span data-stu-id="0f699-278">Check any NSG rules associated with subnets containing these virtual machines.</span></span> <span data-ttu-id="0f699-279">您可使用下列 Azure CLI 命令檢視所有 NSG 規則：</span><span class="sxs-lookup"><span data-stu-id="0f699-279">You can view all NSG rules using the following Azure CLI command:</span></span>

    ```
    azure network nsg show -g <<resource-group>> -n <<nsg-name>>
    ```

- <span data-ttu-id="0f699-280">**請確認 Azure VPN 閘道已連線。**</span><span class="sxs-lookup"><span data-stu-id="0f699-280">**Verify that the Azure VPN gateway is connected.**</span></span>

    <span data-ttu-id="0f699-281">您可以使用下列 Azure PowerShell 命令來檢查 Azure VPN 連線的目前狀態。</span><span class="sxs-lookup"><span data-stu-id="0f699-281">You can use the following Azure PowerShell command to check the current status of the Azure VPN connection.</span></span> <span data-ttu-id="0f699-282">`<<connection-name>>` 參數是連結虛擬網路閘道與區域閘道的 Azure VPN 連線名稱。</span><span class="sxs-lookup"><span data-stu-id="0f699-282">The `<<connection-name>>` parameter is the name of the Azure VPN connection that links the virtual network gateway and the local gateway.</span></span>

    ```
    Get-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> - ResourceGroupName <<resource-group>>
    ```

    <span data-ttu-id="0f699-283">下列程式碼片段會醒目提示閘道連線時 (第一個範例) 和中斷連線時 (第二個範例) 所產生的輸出：</span><span class="sxs-lookup"><span data-stu-id="0f699-283">The following snippets highlight the output generated if the gateway is connected (the first example), and disconnected (the second example):</span></span>

    ```
    PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection -ResourceGroupName profx-prod-rg

    AuthorizationKey           :
    VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
    VirtualNetworkGateway2     :
    LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
    Peer                       :
    ConnectionType             : IPsec
    RoutingWeight              : 0
    SharedKey                  : ####################################
    ConnectionStatus           : Connected
    EgressBytesTransferred     : 55254803
    IngressBytesTransferred    : 32227221
    ProvisioningState          : Succeeded
    ...
    ```

    ```
    PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection2 -ResourceGroupName profx-prod-rg

    AuthorizationKey           :
    VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
    VirtualNetworkGateway2     :
    LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
    Peer                       :
    ConnectionType             : IPsec
    RoutingWeight              : 0
    SharedKey                  : ####################################
    ConnectionStatus           : NotConnected
    EgressBytesTransferred     : 0
    IngressBytesTransferred    : 0
    ProvisioningState          : Succeeded
    ...
    ```

<span data-ttu-id="0f699-284">下列建議有助於判斷主機虛擬機器設定、網路頻寬使用率或應用程式效能是否有問題：</span><span class="sxs-lookup"><span data-stu-id="0f699-284">The following recommendations are useful for determining if there is an issue with Host VM configuration, network bandwidth utilization, or application performance:</span></span>

- <span data-ttu-id="0f699-285">**如果子網路中的 Azure 虛擬機器上正在執行客體作業系統，請確認其中的防火牆已正確設定，可允許內部部署 IP 範圍中的許可流量。**</span><span class="sxs-lookup"><span data-stu-id="0f699-285">**Verify that the firewall in the guest operating system running on the Azure VMs in the subnet is configured correctly to allow permitted traffic from the on-premises IP ranges.**</span></span>

- <span data-ttu-id="0f699-286">**請確認流量尚未接近 Azure VPN 閘道可用的頻寬上限。**</span><span class="sxs-lookup"><span data-stu-id="0f699-286">**Verify that the volume of traffic is not close to the limit of the bandwidth available to the Azure VPN gateway.**</span></span>

    <span data-ttu-id="0f699-287">驗證此項目的方式取決於在內部部署的 VPN 設備。</span><span class="sxs-lookup"><span data-stu-id="0f699-287">How to verify this depends on the VPN appliance running on-premises.</span></span> <span data-ttu-id="0f699-288">例如，如果您在 Windows Server 2012 上使用 RRAS，您可以使用效能監視器來追蹤透過 VPN 連線收到和傳輸的資料量。</span><span class="sxs-lookup"><span data-stu-id="0f699-288">For example, if you are using RRAS on Windows Server 2012, you can use Performance Monitor to track the volume of data being received and transmitted over the VPN connection.</span></span> <span data-ttu-id="0f699-289">如果使用「RAS 總計」物件，請選取「接收的位元組/秒」和「傳輸的位元組/秒」計數器：</span><span class="sxs-lookup"><span data-stu-id="0f699-289">Using the *RAS Total* object, select the *Bytes Received/Sec* and *Bytes Transmitted/Sec* counters:</span></span>

    <span data-ttu-id="0f699-290">![[3]][3]</span><span class="sxs-lookup"><span data-stu-id="0f699-290">![[3]][3]</span></span>

    <span data-ttu-id="0f699-291">您應比較使用 VPN 閘道可用頻寬的結果 (基本與標準 SKU 是 100 Mbps，而高效能 SKU 是 200 Mbps)：</span><span class="sxs-lookup"><span data-stu-id="0f699-291">You should compare the results with the bandwidth available to the VPN gateway (100 Mbps for the Basic and Standard SKUs, and 200 Mbps for the High Performance SKU):</span></span>

    <span data-ttu-id="0f699-292">![[4]][4]</span><span class="sxs-lookup"><span data-stu-id="0f699-292">![[4]][4]</span></span>

- <span data-ttu-id="0f699-293">**請確認您已針對應用程式負載，部署正確的虛擬機器數量和大小。**</span><span class="sxs-lookup"><span data-stu-id="0f699-293">**Verify that you have deployed the right number and size of VMs for your application load.**</span></span>

    <span data-ttu-id="0f699-294">判斷 Azure VNet 中是否有任何一部虛擬機器執行速度緩慢。</span><span class="sxs-lookup"><span data-stu-id="0f699-294">Determine if any of the virtual machines in the Azure VNet are running slowly.</span></span> <span data-ttu-id="0f699-295">如果有，則表示虛擬機器負載可能過重、虛擬機器可能太少以至於無法處理負載，或是負載平衡器可能未正確設定。</span><span class="sxs-lookup"><span data-stu-id="0f699-295">If so, they may be overloaded, there may be too few to handle the load, or the load-balancers may not be configured correctly.</span></span> <span data-ttu-id="0f699-296">若要判斷原因，可[擷取及分析診斷資訊][azure-vm-diagnostics]。</span><span class="sxs-lookup"><span data-stu-id="0f699-296">To determine this, [capture and analyze diagnostic information][azure-vm-diagnostics].</span></span> <span data-ttu-id="0f699-297">您可以使用 Azure 入口網站檢查結果，但也有許多第三方工具可協助您深入了解效能資料。</span><span class="sxs-lookup"><span data-stu-id="0f699-297">You can examine the results using the Azure portal, but many third-party tools are also available that can provide detailed insights into the performance data.</span></span>

- <span data-ttu-id="0f699-298">**請確認應用程式正在有效率地使用雲端資源。**</span><span class="sxs-lookup"><span data-stu-id="0f699-298">**Verify that the application is making efficient use of cloud resources.**</span></span>

    <span data-ttu-id="0f699-299">檢測每個虛擬機器上執行的應用程式程式碼，判斷應用程式是否以最佳方式在使用資源。</span><span class="sxs-lookup"><span data-stu-id="0f699-299">Instrument application code running on each VM to determine whether applications are making the best use of resources.</span></span> <span data-ttu-id="0f699-300">您可以使用 [Application Insights][application-insights] 等工具。</span><span class="sxs-lookup"><span data-stu-id="0f699-300">You can use tools such as [Application Insights][application-insights].</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="0f699-301">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="0f699-301">Deploy the solution</span></span>


<span data-ttu-id="0f699-302">**必要條件。**</span><span class="sxs-lookup"><span data-stu-id="0f699-302">**Prequisites.**</span></span> <span data-ttu-id="0f699-303">您必須已經使用適當的網路設備，設定現有的內部部署基礎結構。</span><span class="sxs-lookup"><span data-stu-id="0f699-303">You must have an existing on-premises infrastructure already configured with a suitable network appliance.</span></span>

<span data-ttu-id="0f699-304">若要部署解決方案，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="0f699-304">To deploy the solution, perform the following steps.</span></span>

1. <span data-ttu-id="0f699-305">按一下下方的按鈕：</span><span class="sxs-lookup"><span data-stu-id="0f699-305">Click the button below:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fvpn%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
2. <span data-ttu-id="0f699-306">等待此連結在 Azure 入口網站中開啟，然後按照下列步驟進行：</span><span class="sxs-lookup"><span data-stu-id="0f699-306">Wait for the link to open in the Azure portal, then follow these steps:</span></span> 
   * <span data-ttu-id="0f699-307">**資源群組**名稱已在參數檔案中定義，因此請在文字方塊中選取 [新建] 並輸入 `ra-hybrid-vpn-rg`。</span><span class="sxs-lookup"><span data-stu-id="0f699-307">The **Resource group** name is already defined in the parameter file, so select **Create New** and enter `ra-hybrid-vpn-rg` in the text box.</span></span>
   * <span data-ttu-id="0f699-308">從 [位置] 下拉式方塊選取區域。</span><span class="sxs-lookup"><span data-stu-id="0f699-308">Select the region from the **Location** drop down box.</span></span>
   * <span data-ttu-id="0f699-309">請勿編輯 [範本的根 URI] 或 [參數根 URI] 文字方塊。</span><span class="sxs-lookup"><span data-stu-id="0f699-309">Do not edit the **Template Root Uri** or the **Parameter Root Uri** text boxes.</span></span>
   * <span data-ttu-id="0f699-310">檢閱條款和條件，然後按一下 [我同意上方所述的條款及條件] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="0f699-310">Review the terms and conditions, then click the **I agree to the terms and conditions stated above** checkbox.</span></span>
   * <span data-ttu-id="0f699-311">按一下 [購買] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="0f699-311">Click the **Purchase** button.</span></span>
3. <span data-ttu-id="0f699-312">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="0f699-312">Wait for the deployment to complete.</span></span>



<!-- links -->

[adds-extend-domain]: ../identity/adds-extend-domain.md
[expressroute]: ../hybrid-networking/expressroute.md
[windows-vm-ra]: ../virtual-machines-windows/index.md
[linux-vm-ra]: ../virtual-machines-linux/index.md

[naming conventions]: /azure/guidance/guidance-naming-conventions

[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[arm-templates]: /azure/resource-group-authoring-templates
[azure-cli]: /azure/virtual-machines-command-line-tools
[azure-portal]: /azure/azure-portal/resource-group-portal
[azure-powershell]: /azure/powershell-azure-resource-manager
[azure-virtual-network]: /azure/virtual-network/virtual-networks-overview
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[azure-vpn-gateway]: https://azure.microsoft.com/services/vpn-gateway/
[azure-gateway-charges]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[vpn-gateway-multi-site]: /azure/vpn-gateway/vpn-gateway-multi-site
[policy-based-routing]: https://en.wikipedia.org/wiki/Policy-based_routing
[route-based-routing]: https://en.wikipedia.org/wiki/Static_routing
[network-security-group]: /azure/virtual-network/virtual-networks-nsg
[sla-for-vpn-gateway]: https://azure.microsoft.com/support/legal/sla/vpn-gateway/v1_2/
[additional-firewall-rules]: https://technet.microsoft.com/library/dn786406.aspx#firewall
[nagios]: https://www.nagios.org/
[azure-vpn-gateway-diagnostics]: http://blogs.technet.com/b/keithmayer/archive/2014/12/18/diagnose-azure-virtual-network-vpn-connectivity-issues-with-powershell.aspx
[ping]: https://technet.microsoft.com/library/ff961503.aspx
[tracert]: https://technet.microsoft.com/library/ff961507.aspx
[psping]: http://technet.microsoft.com/sysinternals/jj729731.aspx
[nmap]: http://nmap.org
[changing-SKUs]: https://azure.microsoft.com/blog/azure-virtual-network-gateway-improvements/
[gateway-diagnostic-logs]: http://blogs.technet.com/b/keithmayer/archive/2015/12/07/step-by-step-capturing-azure-resource-manager-arm-vnet-gateway-diagnostic-logs.aspx
[troubleshooting-vpn-errors]: https://blogs.technet.microsoft.com/rrasblog/2009/08/12/troubleshooting-common-vpn-related-errors/
[rras-logging]: https://www.petri.com/enable-diagnostic-logging-in-windows-server-2012-r2-routing-and-remote-access
[create-on-prem-network]: https://technet.microsoft.com/library/dn786406.aspx#routing
[azure-vm-diagnostics]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[application-insights]: /azure/application-insights/app-insights-overview-usage
[forced-tunneling]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-forced-tunneling/
[vpn-appliances]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-architectures.vsdx
[vpn-appliance-ipsec]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec-parameters
<!--[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/Deploy-ReferenceArchitecture.ps1-->
<!--[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/deploy-reference-architecture.sh-->
<!--[virtualNetworkGateway-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/parameters/virtualNetworkGateway.parameters.json-->
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[0]: ./images/vpn.png "跨越內部部署和 Azure 基礎結構的混合式網路"
[2]: ../_images/guidance-hybrid-network-vpn/audit-logs.png "Azure 入口網站中的稽核記錄"
[3]: ../_images/guidance-hybrid-network-vpn/RRAS-perf-counters.png "監視 VPN 網路流量的效能計數器"
[4]: ../_images/guidance-hybrid-network-vpn/RRAS-perf-graph.png "範例 VPN 網路的效能圖表"
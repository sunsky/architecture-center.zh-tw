---
title: 使用 VPN 將內部部署網路連線至 Azure
titleSuffix: Azure Reference Architectures
description: 實作安全的站對站網路架構，並且在透過 VPN 連線的 Azure 虛擬網路與內部部署網路中使用。
author: RohitSharma-pnp
ms.date: 10/22/2018
ms.openlocfilehash: 92a5a12675ca12075bec3c7f59f73a19287fe5d7
ms.sourcegitcommit: bb7fcffbb41e2c26a26f8781df32825eb60df70c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2018
ms.locfileid: "53644201"
---
# <a name="connect-an-on-premises-network-to-azure-using-a-vpn-gateway"></a><span data-ttu-id="b23ed-103">使用 VPN 閘道將內部部署網路連線至 Azure</span><span class="sxs-lookup"><span data-stu-id="b23ed-103">Connect an on-premises network to Azure using a VPN gateway</span></span>

<span data-ttu-id="b23ed-104">此參考架構會示範如何使用網站對網站虛擬私人網路 (VPN)，將內部部署網路擴充至 Azure。</span><span class="sxs-lookup"><span data-stu-id="b23ed-104">This reference architecture shows how to extend an on-premises network to Azure, using a site-to-site virtual private network (VPN).</span></span> <span data-ttu-id="b23ed-105">內部部署網路和 Azure 虛擬網路 (VNet) 之間的流量會透過 IPSec VPN 通道流動。</span><span class="sxs-lookup"><span data-stu-id="b23ed-105">Traffic flows between the on-premises network and an Azure Virtual Network (VNet) through an IPSec VPN tunnel.</span></span> <span data-ttu-id="b23ed-106">[**部署這個解決方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="b23ed-106">[**Deploy this solution**](#deploy-the-solution).</span></span>

![跨越內部部署和 Azure 基礎結構的混合式網路](./images/vpn.png)

<span data-ttu-id="b23ed-108">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="b23ed-108">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="b23ed-109">架構</span><span class="sxs-lookup"><span data-stu-id="b23ed-109">Architecture</span></span>

<span data-ttu-id="b23ed-110">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="b23ed-110">The architecture consists of the following components.</span></span>

- <span data-ttu-id="b23ed-111">**內部部署網路**。</span><span class="sxs-lookup"><span data-stu-id="b23ed-111">**On-premises network**.</span></span> <span data-ttu-id="b23ed-112">在組織內執行的私用區域網路。</span><span class="sxs-lookup"><span data-stu-id="b23ed-112">A private local-area network running within an organization.</span></span>

- <span data-ttu-id="b23ed-113">**VPN 設備**。</span><span class="sxs-lookup"><span data-stu-id="b23ed-113">**VPN appliance**.</span></span> <span data-ttu-id="b23ed-114">可對內部部署網路提供外部連線的裝置或服務。</span><span class="sxs-lookup"><span data-stu-id="b23ed-114">A device or service that provides external connectivity to the on-premises network.</span></span> <span data-ttu-id="b23ed-115">VPN 設備可能是硬體裝置或軟體解決方案，例如，Windows Server 2012 中的路由及遠端存取服務 (RRAS)。</span><span class="sxs-lookup"><span data-stu-id="b23ed-115">The VPN appliance may be a hardware device, or it can be a software solution such as the Routing and Remote Access Service (RRAS) in Windows Server 2012.</span></span> <span data-ttu-id="b23ed-116">如需支援的 VPN 設備清單和將其設定為連線至 Azure VPN 閘道的詳細資訊，請參閱[關於站對站 VPN 閘道連線的 VPN 裝置][vpn-appliance]一文中所選取裝置的指示。</span><span class="sxs-lookup"><span data-stu-id="b23ed-116">For a list of supported VPN appliances and information on configuring them to connect to an Azure VPN gateway, see the instructions for the selected device in the article [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance].</span></span>

- <span data-ttu-id="b23ed-117">**虛擬網路 (VNet)**。</span><span class="sxs-lookup"><span data-stu-id="b23ed-117">**Virtual network (VNet)**.</span></span> <span data-ttu-id="b23ed-118">雲端應用程式與 Azure VPN 閘道的元件存會位於相同 [VNet][azure-virtual-network]。</span><span class="sxs-lookup"><span data-stu-id="b23ed-118">The cloud application and the components for the Azure VPN gateway reside in the same [VNet][azure-virtual-network].</span></span>

- <span data-ttu-id="b23ed-119">**Azure VPN 閘道**。</span><span class="sxs-lookup"><span data-stu-id="b23ed-119">**Azure VPN gateway**.</span></span> <span data-ttu-id="b23ed-120">[VPN 閘道][azure-vpn-gateway]服務可讓您透過 VPN 設備將 VNet 連線至內部部署網路。</span><span class="sxs-lookup"><span data-stu-id="b23ed-120">The [VPN gateway][azure-vpn-gateway] service enables you to connect the VNet to the on-premises network through a VPN appliance.</span></span> <span data-ttu-id="b23ed-121">如需詳細資訊，請參閱[將內部部署網路連線至 Microsoft Azure 虛擬網路][connect-to-an-Azure-vnet]。</span><span class="sxs-lookup"><span data-stu-id="b23ed-121">For more information, see [Connect an on-premises network to a Microsoft Azure virtual network][connect-to-an-Azure-vnet].</span></span> <span data-ttu-id="b23ed-122">VPN 閘道包含下列元素：</span><span class="sxs-lookup"><span data-stu-id="b23ed-122">The VPN gateway includes the following elements:</span></span>

  - <span data-ttu-id="b23ed-123">**虛擬網路閘道**。</span><span class="sxs-lookup"><span data-stu-id="b23ed-123">**Virtual network gateway**.</span></span> <span data-ttu-id="b23ed-124">為 VNet 提供虛擬 VPN 設備的資源。</span><span class="sxs-lookup"><span data-stu-id="b23ed-124">A resource that provides a virtual VPN appliance for the VNet.</span></span> <span data-ttu-id="b23ed-125">負責將流量從內部部署網路由至 VNet。</span><span class="sxs-lookup"><span data-stu-id="b23ed-125">It is responsible for routing traffic from the on-premises network to the VNet.</span></span>
  - <span data-ttu-id="b23ed-126">**區域網路閘道**。</span><span class="sxs-lookup"><span data-stu-id="b23ed-126">**Local network gateway**.</span></span> <span data-ttu-id="b23ed-127">內部部署 VPN 設備的抽象概念。</span><span class="sxs-lookup"><span data-stu-id="b23ed-127">An abstraction of the on-premises VPN appliance.</span></span> <span data-ttu-id="b23ed-128">從雲端應用程式流至內部部署網路的網路流量會透過此閘道路由傳送。</span><span class="sxs-lookup"><span data-stu-id="b23ed-128">Network traffic from the cloud application to the on-premises network is routed through this gateway.</span></span>
  - <span data-ttu-id="b23ed-129">**連線**。</span><span class="sxs-lookup"><span data-stu-id="b23ed-129">**Connection**.</span></span> <span data-ttu-id="b23ed-130">連線的屬性會指定連線類型 (IPSec) 以及與內部部署 VPN 設備共用以加密流量的金鑰。</span><span class="sxs-lookup"><span data-stu-id="b23ed-130">The connection has properties that specify the connection type (IPSec) and the key shared with the on-premises VPN appliance to encrypt traffic.</span></span>
  - <span data-ttu-id="b23ed-131">**閘道子網路**。</span><span class="sxs-lookup"><span data-stu-id="b23ed-131">**Gateway subnet**.</span></span> <span data-ttu-id="b23ed-132">虛擬網路閘道保留在自己的子網路中，其受限於下方＜建議＞一節中所述的各種需求。</span><span class="sxs-lookup"><span data-stu-id="b23ed-132">The virtual network gateway is held in its own subnet, which is subject to various requirements, described in the Recommendations section below.</span></span>

- <span data-ttu-id="b23ed-133">**雲端應用程式**。</span><span class="sxs-lookup"><span data-stu-id="b23ed-133">**Cloud application**.</span></span> <span data-ttu-id="b23ed-134">在 Azure 中裝載的應用程式。</span><span class="sxs-lookup"><span data-stu-id="b23ed-134">The application hosted in Azure.</span></span> <span data-ttu-id="b23ed-135">此應用程式在透過 Azure 負載平衡器連線多個子網路的情況下，可能包括多層。</span><span class="sxs-lookup"><span data-stu-id="b23ed-135">It might include multiple tiers, with multiple subnets connected through Azure load balancers.</span></span> <span data-ttu-id="b23ed-136">如需有關應用程式基礎結構的詳細資訊，請參閱[執行 Windows VM 工作負載][windows-vm-ra]和[執行 Linux VM 工作負載][linux-vm-ra]。</span><span class="sxs-lookup"><span data-stu-id="b23ed-136">For more information about the application infrastructure, see [Running Windows VM workloads][windows-vm-ra] and [Running Linux VM workloads][linux-vm-ra].</span></span>

- <span data-ttu-id="b23ed-137">**內部負載平衡器**。</span><span class="sxs-lookup"><span data-stu-id="b23ed-137">**Internal load balancer**.</span></span> <span data-ttu-id="b23ed-138">來自 VPN 閘道的網路流量會透過內部負載平衡器，路由傳送至雲端應用程式。</span><span class="sxs-lookup"><span data-stu-id="b23ed-138">Network traffic from the VPN gateway is routed to the cloud application through an internal load balancer.</span></span> <span data-ttu-id="b23ed-139">負載平衡器會位於應用程式的前端子網路中。</span><span class="sxs-lookup"><span data-stu-id="b23ed-139">The load balancer is located in the front-end subnet of the application.</span></span>

## <a name="recommendations"></a><span data-ttu-id="b23ed-140">建議</span><span class="sxs-lookup"><span data-stu-id="b23ed-140">Recommendations</span></span>

<span data-ttu-id="b23ed-141">下列建議適用於大部分的案例。</span><span class="sxs-lookup"><span data-stu-id="b23ed-141">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="b23ed-142">除非您有特定的需求會覆寫它們，否則請遵循下列建議。</span><span class="sxs-lookup"><span data-stu-id="b23ed-142">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="vnet-and-gateway-subnet"></a><span data-ttu-id="b23ed-143">VNet 和閘道子網路</span><span class="sxs-lookup"><span data-stu-id="b23ed-143">VNet and gateway subnet</span></span>

<span data-ttu-id="b23ed-144">建立 Azure VNet，且其位址空間要夠大，以便使用所有必要資源。</span><span class="sxs-lookup"><span data-stu-id="b23ed-144">Create an Azure VNet with an address space large enough for all of your required resources.</span></span> <span data-ttu-id="b23ed-145">如果未來可能需要其他虛擬機器，請確定 VNet 位址空間有足夠的空間可擴增。</span><span class="sxs-lookup"><span data-stu-id="b23ed-145">Ensure that the VNet address space has sufficient room for growth if additional VMs are likely to be needed in the future.</span></span> <span data-ttu-id="b23ed-146">VNet 的位址空間不得與內部部署網路重疊。</span><span class="sxs-lookup"><span data-stu-id="b23ed-146">The address space of the VNet must not overlap with the on-premises network.</span></span> <span data-ttu-id="b23ed-147">例如，上圖中 VNet 使用位址空間 10.20.0.0/16。</span><span class="sxs-lookup"><span data-stu-id="b23ed-147">For example, the diagram above uses the address space 10.20.0.0/16 for the VNet.</span></span>

<span data-ttu-id="b23ed-148">建立一個名為 *GatewaySubnet* 的子網路，且其位址範圍為 /27。</span><span class="sxs-lookup"><span data-stu-id="b23ed-148">Create a subnet named *GatewaySubnet*, with an address range of /27.</span></span> <span data-ttu-id="b23ed-149">虛擬網路閘道需要使用這個子網路。</span><span class="sxs-lookup"><span data-stu-id="b23ed-149">This subnet is required by the virtual network gateway.</span></span> <span data-ttu-id="b23ed-150">將 32 個位址配置給這個子網路，將有助於防止未來達到閘道大小限制。</span><span class="sxs-lookup"><span data-stu-id="b23ed-150">Allocating 32 addresses to this subnet will help to prevent reaching gateway size limitations in the future.</span></span> <span data-ttu-id="b23ed-151">此外，請避免將此子網路放在位址空間的中間。</span><span class="sxs-lookup"><span data-stu-id="b23ed-151">Also, avoid placing this subnet in the middle of the address space.</span></span> <span data-ttu-id="b23ed-152">較好的做法是，將閘道子網路的位址空間設定在 VNet 位址空間的上端。</span><span class="sxs-lookup"><span data-stu-id="b23ed-152">A good practice is to set the address space for the gateway subnet at the upper end of the VNet address space.</span></span> <span data-ttu-id="b23ed-153">圖中所示的範例是使用 10.20.255.224/27。</span><span class="sxs-lookup"><span data-stu-id="b23ed-153">The example shown in the diagram uses 10.20.255.224/27.</span></span>  <span data-ttu-id="b23ed-154">以下是計算 [CIDR] 的快速程序：</span><span class="sxs-lookup"><span data-stu-id="b23ed-154">Here is a quick procedure to calculate the [CIDR]:</span></span>

1. <span data-ttu-id="b23ed-155">將 VNet 位址空間中的變動位元數設為 1 (根據閘道正在使用的位元數)，然後將其他位元數設為 0。</span><span class="sxs-lookup"><span data-stu-id="b23ed-155">Set the variable bits in the address space of the VNet to 1, up to the bits being used by the gateway subnet, then set the remaining bits to 0.</span></span>
2. <span data-ttu-id="b23ed-156">將產生的位元數轉換成十進位，並將其表示為前置長度設為閘道子網路大小的位址空間。</span><span class="sxs-lookup"><span data-stu-id="b23ed-156">Convert the resulting bits to decimal and express it as an address space with the prefix length set to the size of the gateway subnet.</span></span>

<span data-ttu-id="b23ed-157">例如，IP 位址範圍是 10.20.0.0/16 的 VNet，套用上述步驟 1 後會變成 10.20.0b11111111.0b11100000。</span><span class="sxs-lookup"><span data-stu-id="b23ed-157">For example, for a VNet with an IP address range of 10.20.0.0/16, applying step #1 above becomes 10.20.0b11111111.0b11100000.</span></span>  <span data-ttu-id="b23ed-158">將其轉換成十進位並表示為位址空間後會產生 10.20.255.224/27。</span><span class="sxs-lookup"><span data-stu-id="b23ed-158">Converting that to decimal and expressing it as an address space yields 10.20.255.224/27.</span></span>

> [!WARNING]
> <span data-ttu-id="b23ed-159">請勿將任何虛擬機器部署至閘道子網路。</span><span class="sxs-lookup"><span data-stu-id="b23ed-159">Do not deploy any VMs to the gateway subnet.</span></span> <span data-ttu-id="b23ed-160">此外，請勿指派 NSG 至此子網路，因為會導致閘道停止運作。</span><span class="sxs-lookup"><span data-stu-id="b23ed-160">Also, do not assign an NSG to this subnet, as it will cause the gateway to stop functioning.</span></span>
>

### <a name="virtual-network-gateway"></a><span data-ttu-id="b23ed-161">虛擬網路閘道</span><span class="sxs-lookup"><span data-stu-id="b23ed-161">Virtual network gateway</span></span>

<span data-ttu-id="b23ed-162">配置虛擬網路閘道的公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="b23ed-162">Allocate a public IP address for the virtual network gateway.</span></span>

<span data-ttu-id="b23ed-163">在閘道子網路中建立虛擬網路閘道，並為其指派新配置的公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="b23ed-163">Create the virtual network gateway in the gateway subnet and assign it the newly allocated public IP address.</span></span> <span data-ttu-id="b23ed-164">使用最符合您需求且由您的 VPN 設備啟用的閘道類型：</span><span class="sxs-lookup"><span data-stu-id="b23ed-164">Use the gateway type that most closely matches your requirements and that is enabled by your VPN appliance:</span></span>

- <span data-ttu-id="b23ed-165">如果您需要根據原則準則 (例如位址首碼)，密切地控制要求的路由方式，可建立[原則型閘道][policy-based-routing]。</span><span class="sxs-lookup"><span data-stu-id="b23ed-165">Create a [policy-based gateway][policy-based-routing] if you need to closely control how requests are routed based on policy criteria such as address prefixes.</span></span> <span data-ttu-id="b23ed-166">原則型閘道會使用靜態路由，並只適用於站對站連線。</span><span class="sxs-lookup"><span data-stu-id="b23ed-166">Policy-based gateways use static routing, and only work with site-to-site connections.</span></span>

- <span data-ttu-id="b23ed-167">如果您使用 RRAS 連線至內部部署網路、支援多站台或跨區域連線或實作 VNet 對 VNet 連線 (包括跨越多個 Vnet 的路由)，可建立[路由型閘道][route-based-routing]。</span><span class="sxs-lookup"><span data-stu-id="b23ed-167">Create a [route-based gateway][route-based-routing] if you connect to the on-premises network using RRAS, support multi-site or cross-region connections, or implement VNet-to-VNet connections (including routes that traverse multiple VNets).</span></span> <span data-ttu-id="b23ed-168">路由型閘道會使用動態路由來指引網路之間的流量。</span><span class="sxs-lookup"><span data-stu-id="b23ed-168">Route-based gateways use dynamic routing to direct traffic between networks.</span></span> <span data-ttu-id="b23ed-169">動態路由在網路路徑中的容錯能力比靜態路由好，因為動態路由可以嘗試替代路由。</span><span class="sxs-lookup"><span data-stu-id="b23ed-169">They can tolerate failures in the network path better than static routes because they can try alternative routes.</span></span> <span data-ttu-id="b23ed-170">路由型閘道也可減少額外負荷，因為當網路位址變更時，路由可能不需要以手動方式更新。</span><span class="sxs-lookup"><span data-stu-id="b23ed-170">Route-based gateways can also reduce the management overhead because routes might not need to be updated manually when network addresses change.</span></span>

<span data-ttu-id="b23ed-171">如需支援的 VPN 設備清單，請參閱[關於站對站 VPN 閘道連線的 VPN 裝置][vpn-appliances]。</span><span class="sxs-lookup"><span data-stu-id="b23ed-171">For a list of supported VPN appliances, see [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliances].</span></span>

> [!NOTE]
> <span data-ttu-id="b23ed-172">一旦建立閘道之後，您必須刪除並重新建立閘道才能變更閘道類型。</span><span class="sxs-lookup"><span data-stu-id="b23ed-172">After the gateway has been created, you cannot change between gateway types without deleting and re-creating the gateway.</span></span>
>

<span data-ttu-id="b23ed-173">選取最符合您輸送量需求的 Azure VPN 閘道 SKU。</span><span class="sxs-lookup"><span data-stu-id="b23ed-173">Select the Azure VPN gateway SKU that most closely matches your throughput requirements.</span></span> <span data-ttu-id="b23ed-174">如需詳細資訊，請參閱[閘道 SKU][azure-gateway-skus]</span><span class="sxs-lookup"><span data-stu-id="b23ed-174">For more information, see [Gateway SKUs][azure-gateway-skus]</span></span>

> [!NOTE]
> <span data-ttu-id="b23ed-175">基本 SKU 與 Azure ExpressRoute 不相容。</span><span class="sxs-lookup"><span data-stu-id="b23ed-175">The Basic SKU is not compatible with Azure ExpressRoute.</span></span> <span data-ttu-id="b23ed-176">建立閘道之後，您可以[變更 SKU][changing-SKUs]。</span><span class="sxs-lookup"><span data-stu-id="b23ed-176">You can [change the SKU][changing-SKUs] after the gateway has been created.</span></span>
>

<span data-ttu-id="b23ed-177">您須根據閘道上所佈建及可用時間量支付費用。</span><span class="sxs-lookup"><span data-stu-id="b23ed-177">You are charged based on the amount of time that the gateway is provisioned and available.</span></span> <span data-ttu-id="b23ed-178">請參閱 [VPN 閘道價格][azure-gateway-charges]。</span><span class="sxs-lookup"><span data-stu-id="b23ed-178">See [VPN Gateway Pricing][azure-gateway-charges].</span></span>

<span data-ttu-id="b23ed-179">建立閘道子網路的路由規則，將傳入的應用程式流量從閘道引導至內部負載平衡器，而不允許將要求直接傳遞至應用程式虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="b23ed-179">Create routing rules for the gateway subnet that direct incoming application traffic from the gateway to the internal load balancer, rather than allowing requests to pass directly to the application VMs.</span></span>

### <a name="on-premises-network-connection"></a><span data-ttu-id="b23ed-180">內部部署網路連線</span><span class="sxs-lookup"><span data-stu-id="b23ed-180">On-premises network connection</span></span>

<span data-ttu-id="b23ed-181">建立區域網路閘道。</span><span class="sxs-lookup"><span data-stu-id="b23ed-181">Create a local network gateway.</span></span> <span data-ttu-id="b23ed-182">指定內部部署 VPN 設備的公用 IP 位址，以及內部部署網路的位址空間。</span><span class="sxs-lookup"><span data-stu-id="b23ed-182">Specify the public IP address of the on-premises VPN appliance, and the address space of the on-premises network.</span></span> <span data-ttu-id="b23ed-183">請注意，內部部署 VPN 設備必須有可以存取的公用 IP 位址，讓 Azure VPN 閘道中的區域網路閘道進行存取。</span><span class="sxs-lookup"><span data-stu-id="b23ed-183">Note that the on-premises VPN appliance must have a public IP address that can be accessed by the local network gateway in Azure VPN Gateway.</span></span> <span data-ttu-id="b23ed-184">在網路位址轉譯 (NAT) 裝置後面找不到 VPN 裝置。</span><span class="sxs-lookup"><span data-stu-id="b23ed-184">The VPN device cannot be located behind a network address translation (NAT) device.</span></span>

<span data-ttu-id="b23ed-185">建立虛擬網路閘道與區域網路閘道的站對站連線。</span><span class="sxs-lookup"><span data-stu-id="b23ed-185">Create a site-to-site connection for the virtual network gateway and the local network gateway.</span></span> <span data-ttu-id="b23ed-186">選取站對站 (IPSec) 連線類型，並指定共用金鑰。</span><span class="sxs-lookup"><span data-stu-id="b23ed-186">Select the site-to-site (IPSec) connection type, and specify the shared key.</span></span> <span data-ttu-id="b23ed-187">Azure VPN 閘道的站對站加密是以 IPSec 通訊協定為基礎，並使用預先共用的金鑰來進行驗證。</span><span class="sxs-lookup"><span data-stu-id="b23ed-187">Site-to-site encryption with the Azure VPN gateway is based on the IPSec protocol, using preshared keys for authentication.</span></span> <span data-ttu-id="b23ed-188">您會在建立 Azure VPN 閘道時指定金鑰。</span><span class="sxs-lookup"><span data-stu-id="b23ed-188">You specify the key when you create the Azure VPN gateway.</span></span> <span data-ttu-id="b23ed-189">您必須使用相同金鑰來設定在內部部署執行的 VPN 設備。</span><span class="sxs-lookup"><span data-stu-id="b23ed-189">You must configure the VPN appliance running on-premises with the same key.</span></span> <span data-ttu-id="b23ed-190">目前不支援其他驗證機制。</span><span class="sxs-lookup"><span data-stu-id="b23ed-190">Other authentication mechanisms are not currently supported.</span></span>

<span data-ttu-id="b23ed-191">請確認內部部署路由基礎結構已設定為，將原本目的地是 Azure VNet 位址的要求轉送至 VPN 裝置。</span><span class="sxs-lookup"><span data-stu-id="b23ed-191">Ensure that the on-premises routing infrastructure is configured to forward requests intended for addresses in the Azure VNet to the VPN device.</span></span>

<span data-ttu-id="b23ed-192">在內部部署網路中，開啟任何雲端應用程式所需的連接埠。</span><span class="sxs-lookup"><span data-stu-id="b23ed-192">Open any ports required by the cloud application in the on-premises network.</span></span>

<span data-ttu-id="b23ed-193">測試連線以確認下列事項：</span><span class="sxs-lookup"><span data-stu-id="b23ed-193">Test the connection to verify that:</span></span>

- <span data-ttu-id="b23ed-194">內部部署 VPN 設備透過 Azure VPN 閘道，正確地將流量路由至雲端應用程式。</span><span class="sxs-lookup"><span data-stu-id="b23ed-194">The on-premises VPN appliance correctly routes traffic to the cloud application through the Azure VPN gateway.</span></span>
- <span data-ttu-id="b23ed-195">VNet 正確地將流量路由回內部部署網路。</span><span class="sxs-lookup"><span data-stu-id="b23ed-195">The VNet correctly routes traffic back to the on-premises network.</span></span>
- <span data-ttu-id="b23ed-196">兩個方向中禁止的流量已正確地封鎖。</span><span class="sxs-lookup"><span data-stu-id="b23ed-196">Prohibited traffic in both directions is blocked correctly.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="b23ed-197">延展性考量</span><span class="sxs-lookup"><span data-stu-id="b23ed-197">Scalability considerations</span></span>

<span data-ttu-id="b23ed-198">您可以從基本或標準 VPN 閘道 SKU 移到高效能 VPN SKU，以達到所限制的垂直延展性。</span><span class="sxs-lookup"><span data-stu-id="b23ed-198">You can achieve limited vertical scalability by moving from the Basic or Standard VPN Gateway SKUs to the High Performance VPN SKU.</span></span>

<span data-ttu-id="b23ed-199">對於預期有大量 VPN 流量的 Vnet，請考慮將不同工作負載分散到較小的個別 VNet，以及為每一個個別 VNet 設定 VPN 閘道。</span><span class="sxs-lookup"><span data-stu-id="b23ed-199">For VNets that expect a large volume of VPN traffic, consider distributing the different workloads into separate smaller VNets and configuring a VPN gateway for each of them.</span></span>

<span data-ttu-id="b23ed-200">您可以水平或垂直分割 VNet。</span><span class="sxs-lookup"><span data-stu-id="b23ed-200">You can partition the VNet either horizontally or vertically.</span></span> <span data-ttu-id="b23ed-201">若要以水平方式分割，請將一些虛擬機器執行個體從每個層移至新 VNet 的子網路。</span><span class="sxs-lookup"><span data-stu-id="b23ed-201">To partition horizontally, move some VM instances from each tier into subnets of the new VNet.</span></span> <span data-ttu-id="b23ed-202">結果會是每個 VNet 都具有相同的結構與功能。</span><span class="sxs-lookup"><span data-stu-id="b23ed-202">The result is that each VNet has the same structure and functionality.</span></span> <span data-ttu-id="b23ed-203">若要以垂直方式分割，請重新設計每個層以將功能分成不同的邏輯區域 (例如處理訂單、發票開立和客戶帳戶管理等)。</span><span class="sxs-lookup"><span data-stu-id="b23ed-203">To partition vertically, redesign each tier to divide the functionality into different logical areas (such as handling orders, invoicing, customer account management, and so on).</span></span> <span data-ttu-id="b23ed-204">然後每個功能區域就可以放在自己的 VNet 中。</span><span class="sxs-lookup"><span data-stu-id="b23ed-204">Each functional area can then be placed in its own VNet.</span></span>

<span data-ttu-id="b23ed-205">複寫 VNet 中的內部部署 Active Directory 網域控制站，以及在 VNet 中實作 DNS，可以協助減少部分安全性相關流量與系統管理流量從內部部署流至雲端。</span><span class="sxs-lookup"><span data-stu-id="b23ed-205">Replicating an on-premises Active Directory domain controller in the VNet, and implementing DNS in the VNet, can help to reduce some of the security-related and administrative traffic flowing from on-premises to the cloud.</span></span> <span data-ttu-id="b23ed-206">如需詳細資訊，請參閱[將 Active Directory Domain Services (AD DS) 擴充至 Azure][adds-extend-domain]。</span><span class="sxs-lookup"><span data-stu-id="b23ed-206">For more information, see [Extending Active Directory Domain Services (AD DS) to Azure][adds-extend-domain].</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="b23ed-207">可用性考量</span><span class="sxs-lookup"><span data-stu-id="b23ed-207">Availability considerations</span></span>

<span data-ttu-id="b23ed-208">如果您需要確保內部部署網路仍然可供 Azure VPN 閘道使用，可為內部部署 VPN 閘道實作容錯移轉叢集。</span><span class="sxs-lookup"><span data-stu-id="b23ed-208">If you need to ensure that the on-premises network remains available to the Azure VPN gateway, implement a failover cluster for the on-premises VPN gateway.</span></span>

<span data-ttu-id="b23ed-209">如果您的組織具有多個內部部署站台，可對一個或多個 Azure Vnet 建立[多站台連線][vpn-gateway-multi-site]。</span><span class="sxs-lookup"><span data-stu-id="b23ed-209">If your organization has multiple on-premises sites, create [multi-site connections][vpn-gateway-multi-site] to one or more Azure VNets.</span></span> <span data-ttu-id="b23ed-210">此方法需要建立動態 (路由型) 路由，因此請確定內部部署 VPN 閘道支援此功能。</span><span class="sxs-lookup"><span data-stu-id="b23ed-210">This approach requires dynamic (route-based) routing, so make sure that the on-premises VPN gateway supports this feature.</span></span>

<span data-ttu-id="b23ed-211">如需有關服務等級協定的詳細資訊，請參閱 [VPN 閘道的 SLA][sla-for-vpn-gateway]。</span><span class="sxs-lookup"><span data-stu-id="b23ed-211">For details about service level agreements, see [SLA for VPN Gateway][sla-for-vpn-gateway].</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="b23ed-212">管理性考量</span><span class="sxs-lookup"><span data-stu-id="b23ed-212">Manageability considerations</span></span>

<span data-ttu-id="b23ed-213">監視內部部署 VPN 設備中的診斷資訊。</span><span class="sxs-lookup"><span data-stu-id="b23ed-213">Monitor diagnostic information from on-premises VPN appliances.</span></span> <span data-ttu-id="b23ed-214">此流程取決於 VPN 設備所提供的功能。</span><span class="sxs-lookup"><span data-stu-id="b23ed-214">This process depends on the features provided by the VPN appliance.</span></span> <span data-ttu-id="b23ed-215">例如，如果您使用 Windows Server 2012 上的路由及遠端存取服務，也就是 [RRAS 記錄][rras-logging]。</span><span class="sxs-lookup"><span data-stu-id="b23ed-215">For example, if you are using the Routing and Remote Access Service on Windows Server 2012, [RRAS logging][rras-logging].</span></span>

<span data-ttu-id="b23ed-216">請使用 [Azure VPN 閘道診斷][gateway-diagnostic-logs]來擷取連線問題的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="b23ed-216">Use [Azure VPN gateway diagnostics][gateway-diagnostic-logs] to capture information about connectivity issues.</span></span> <span data-ttu-id="b23ed-217">這些記錄可用來追蹤資訊，例如連線要求的來源和目的地、使用的通訊協定，以及建立連線的方式 (或嘗試失敗的原因)。</span><span class="sxs-lookup"><span data-stu-id="b23ed-217">These logs can be used to track information such as the source and destinations of connection requests, which protocol was used, and how the connection was established (or why the attempt failed).</span></span>

<span data-ttu-id="b23ed-218">在 Azure 入口網站中，使用可用的稽核記錄來監視 Azure VPN 閘道的作業記錄。</span><span class="sxs-lookup"><span data-stu-id="b23ed-218">Monitor the operational logs of the Azure VPN gateway using the audit logs available in the Azure portal.</span></span> <span data-ttu-id="b23ed-219">個別的記錄適用於區域網路閘道、Azure 網路閘道和連線。</span><span class="sxs-lookup"><span data-stu-id="b23ed-219">Separate logs are available for the local network gateway, the Azure network gateway, and the connection.</span></span> <span data-ttu-id="b23ed-220">此資訊可用來追蹤閘道上所做的任何變更，而且有助於了解先前可運作的閘道為什麼會停止運作。</span><span class="sxs-lookup"><span data-stu-id="b23ed-220">This information can be used to track any changes made to the gateway, and can be useful if a previously functioning gateway stops working for some reason.</span></span>

![Azure 入口網站中的稽核記錄](../_images/guidance-hybrid-network-vpn/audit-logs.png)

<span data-ttu-id="b23ed-222">監視連線，並追蹤連線失敗事件。</span><span class="sxs-lookup"><span data-stu-id="b23ed-222">Monitor connectivity, and track connectivity failure events.</span></span> <span data-ttu-id="b23ed-223">您可以使用監視套件 (例如 [Nagios][nagios]) 來擷取和提報此資訊。</span><span class="sxs-lookup"><span data-stu-id="b23ed-223">You can use a monitoring package such as [Nagios][nagios] to capture and report this information.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="b23ed-224">安全性考量</span><span class="sxs-lookup"><span data-stu-id="b23ed-224">Security considerations</span></span>

<span data-ttu-id="b23ed-225">針對每個 VPN 閘道產生不同的共用金鑰。</span><span class="sxs-lookup"><span data-stu-id="b23ed-225">Generate a different shared key for each VPN gateway.</span></span> <span data-ttu-id="b23ed-226">使用強式共用金鑰，以協助抵禦暴力密碼破解攻擊。</span><span class="sxs-lookup"><span data-stu-id="b23ed-226">Use a strong shared key to help resist brute-force attacks.</span></span>

> [!NOTE]
> <span data-ttu-id="b23ed-227">目前，您無法使用 Azure Key Vault 預先共用 Azure VPN 閘道的金鑰。</span><span class="sxs-lookup"><span data-stu-id="b23ed-227">Currently, you cannot use Azure Key Vault to preshare keys for the Azure VPN gateway.</span></span>
>

<span data-ttu-id="b23ed-228">請確定內部部署 VPN 設備使用的加密方法[與 Azure VPN 閘道相容][vpn-appliance-ipsec]。</span><span class="sxs-lookup"><span data-stu-id="b23ed-228">Ensure that the on-premises VPN appliance uses an encryption method that is [compatible with the Azure VPN gateway][vpn-appliance-ipsec].</span></span> <span data-ttu-id="b23ed-229">針對原則型路由，Azure VPN 閘道支援 AES256、AES128 和 3DES 加密演算法。</span><span class="sxs-lookup"><span data-stu-id="b23ed-229">For policy-based routing, the Azure VPN gateway supports the AES256, AES128, and 3DES encryption algorithms.</span></span> <span data-ttu-id="b23ed-230">路由型閘道支援 AES256 和 3DES。</span><span class="sxs-lookup"><span data-stu-id="b23ed-230">Route-based gateways support AES256 and 3DES.</span></span>

<span data-ttu-id="b23ed-231">如果您的內部部署 VPN 設備位在與網際網路之間有防火牆的周邊網路 (DMZ) 上，您可能必須設定[其他防火牆規則][additional-firewall-rules]，以允許站對站 VPN 連線。</span><span class="sxs-lookup"><span data-stu-id="b23ed-231">If your on-premises VPN appliance is on a perimeter network (DMZ) that has a firewall between the perimeter network and the Internet, you might have to configure [additional firewall rules][additional-firewall-rules] to allow the site-to-site VPN connection.</span></span>

<span data-ttu-id="b23ed-232">如果 VNet 中的應用程式會將資料傳送至網際網路，請考慮[實作強制通道][forced-tunneling]，以透過內部部署網路來路由所有網際網路繫結流量。</span><span class="sxs-lookup"><span data-stu-id="b23ed-232">If the application in the VNet sends data to the Internet, consider [implementing forced tunneling][forced-tunneling] to route all Internet-bound traffic through the on-premises network.</span></span> <span data-ttu-id="b23ed-233">此方法可讓您稽核應用程式在內部部署基礎結構中所做的傳出要求。</span><span class="sxs-lookup"><span data-stu-id="b23ed-233">This approach enables you to audit outgoing requests made by the application from the on-premises infrastructure.</span></span>

> [!NOTE]
> <span data-ttu-id="b23ed-234">強制通道可能會影響與 Azure 服務 (例如儲存體服務) 和 Windows 授權管理員的連線。</span><span class="sxs-lookup"><span data-stu-id="b23ed-234">Forced tunneling can impact connectivity to Azure services (the Storage Service, for example) and the Windows license manager.</span></span>
>

## <a name="deploy-the-solution"></a><span data-ttu-id="b23ed-235">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="b23ed-235">Deploy the solution</span></span>

<span data-ttu-id="b23ed-236">**必要條件**。</span><span class="sxs-lookup"><span data-stu-id="b23ed-236">**Prerequisites**.</span></span> <span data-ttu-id="b23ed-237">您必須已經使用適當的網路設備，設定現有的內部部署基礎結構。</span><span class="sxs-lookup"><span data-stu-id="b23ed-237">You must have an existing on-premises infrastructure already configured with a suitable network appliance.</span></span>

<span data-ttu-id="b23ed-238">若要部署解決方案，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="b23ed-238">To deploy the solution, perform the following steps.</span></span>

<!-- markdownlint-disable MD033 -->

1. <span data-ttu-id="b23ed-239">按一下下方的按鈕：</span><span class="sxs-lookup"><span data-stu-id="b23ed-239">Click the button below:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fvpn%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. <span data-ttu-id="b23ed-240">等待此連結在 Azure 入口網站中開啟，然後按照下列步驟進行：</span><span class="sxs-lookup"><span data-stu-id="b23ed-240">Wait for the link to open in the Azure portal, then follow these steps:</span></span>
   - <span data-ttu-id="b23ed-241">**資源群組**名稱已在參數檔案中定義，因此請在文字方塊中選取 [新建] 並輸入 `ra-hybrid-vpn-rg`。</span><span class="sxs-lookup"><span data-stu-id="b23ed-241">The **Resource group** name is already defined in the parameter file, so select **Create New** and enter `ra-hybrid-vpn-rg` in the text box.</span></span>
   - <span data-ttu-id="b23ed-242">從 [位置] 下拉式方塊選取區域。</span><span class="sxs-lookup"><span data-stu-id="b23ed-242">Select the region from the **Location** drop down box.</span></span>
   - <span data-ttu-id="b23ed-243">請勿編輯 [範本的根 URI] 或 [參數根 URI] 文字方塊。</span><span class="sxs-lookup"><span data-stu-id="b23ed-243">Do not edit the **Template Root Uri** or the **Parameter Root Uri** text boxes.</span></span>
   - <span data-ttu-id="b23ed-244">檢閱條款和條件，然後按一下 [我同意上方所述的條款及條件] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="b23ed-244">Review the terms and conditions, then click the **I agree to the terms and conditions stated above** checkbox.</span></span>
   - <span data-ttu-id="b23ed-245">按一下 [購買] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="b23ed-245">Click the **Purchase** button.</span></span>
3. <span data-ttu-id="b23ed-246">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="b23ed-246">Wait for the deployment to complete.</span></span>

<!-- markdownlint-enable MD033 -->

<span data-ttu-id="b23ed-247">若要對連線進行疑難排解，請參閱[針對混合式 VPN 連線進行疑難排解](./troubleshoot-vpn.md)。</span><span class="sxs-lookup"><span data-stu-id="b23ed-247">To troubleshoot the connection, see [Troubleshoot a hybrid VPN connection](./troubleshoot-vpn.md).</span></span>

<!-- links -->

[adds-extend-domain]: ../identity/adds-extend-domain.md
[windows-vm-ra]: ../virtual-machines-windows/index.md
[linux-vm-ra]: ../virtual-machines-linux/index.md

[azure-cli]: /azure/virtual-machines-command-line-tools
[azure-virtual-network]: /azure/virtual-network/virtual-networks-overview
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[azure-vpn-gateway]: https://azure.microsoft.com/services/vpn-gateway/
[azure-gateway-charges]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[azure-gateway-skus]: /azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[vpn-gateway-multi-site]: /azure/vpn-gateway/vpn-gateway-multi-site
[policy-based-routing]: https://en.wikipedia.org/wiki/Policy-based_routing
[route-based-routing]: https://en.wikipedia.org/wiki/Static_routing
[sla-for-vpn-gateway]: https://azure.microsoft.com/support/legal/sla/vpn-gateway/
[additional-firewall-rules]: https://technet.microsoft.com/library/dn786406.aspx#firewall
[nagios]: https://www.nagios.org/
[changing-SKUs]: https://azure.microsoft.com/blog/azure-virtual-network-gateway-improvements/
[gateway-diagnostic-logs]: https://blogs.technet.microsoft.com/keithmayer/2016/10/12/step-by-step-capturing-azure-resource-manager-arm-vnet-gateway-diagnostic-logs/
[rras-logging]: https://www.petri.com/enable-diagnostic-logging-in-windows-server-2012-r2-routing-and-remote-access
[forced-tunneling]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-forced-tunneling/
[vpn-appliances]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-architectures.vsdx
[vpn-appliance-ipsec]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec-parameters
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing

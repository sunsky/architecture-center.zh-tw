---
title: 在 Azure 中實作中樞輪輻網路拓撲
description: 如何在 Azure 中實作中樞輪輻網路拓撲。
author: telmosampaio
ms.date: 04/09/2018
pnp.series.title: Implement a hub-spoke network topology in Azure
pnp.series.prev: expressroute
ms.openlocfilehash: fcdbb7ca8d02745d4d9ab82f0bce79ab378d843c
ms.sourcegitcommit: f6be2825bf2d37dfe25cfab92b9e3973a6b51e16
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2018
ms.locfileid: "48858192"
---
# <a name="implement-a-hub-spoke-network-topology-in-azure"></a><span data-ttu-id="6bc87-103">在 Azure 中實作中樞輪輻網路拓撲</span><span class="sxs-lookup"><span data-stu-id="6bc87-103">Implement a hub-spoke network topology in Azure</span></span>

<span data-ttu-id="6bc87-104">此參考架構會顯示如何在 Azure 中實作中樞輪輻拓撲。</span><span class="sxs-lookup"><span data-stu-id="6bc87-104">This reference architecture shows how to implement a hub-spoke topology in Azure.</span></span> <span data-ttu-id="6bc87-105">「中樞」是 Azure 中的虛擬網路 (VNet)，可當作內部部署網路的連線中心點。</span><span class="sxs-lookup"><span data-stu-id="6bc87-105">The *hub* is a virtual network (VNet) in Azure that acts as a central point of connectivity to your on-premises network.</span></span> <span data-ttu-id="6bc87-106">「輪輻」是與中樞對等互連的 VNet，可用於隔離工作負載。</span><span class="sxs-lookup"><span data-stu-id="6bc87-106">The *spokes* are VNets that peer with the hub, and can be used to isolate workloads.</span></span> <span data-ttu-id="6bc87-107">流量會透過 ExpressRoute 或 VPN 閘道連線，在內部部署資料中心和中樞之間流動。</span><span class="sxs-lookup"><span data-stu-id="6bc87-107">Traffic flows between the on-premises datacenter and the hub through an ExpressRoute or VPN gateway connection.</span></span>  <span data-ttu-id="6bc87-108">[**部署這個解決方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="6bc87-108">[**Deploy this solution**](#deploy-the-solution).</span></span>

<span data-ttu-id="6bc87-109">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="6bc87-109">![[0]][0]</span></span>

<span data-ttu-id="6bc87-110">*下載這個架構的 [Visio 檔案][visio-download]*</span><span class="sxs-lookup"><span data-stu-id="6bc87-110">*Download a [Visio file][visio-download] of this architecture*</span></span>


<span data-ttu-id="6bc87-111">此拓撲的優點包括：</span><span class="sxs-lookup"><span data-stu-id="6bc87-111">The benefits of this toplogy include:</span></span>

* <span data-ttu-id="6bc87-112">**節省成本**：將可由多個工作負載共用的服務 (例如網路虛擬裝置 (NVA) 和 DNS 伺服器) 集中在單一位置。</span><span class="sxs-lookup"><span data-stu-id="6bc87-112">**Cost savings** by centralizing services that can be shared by multiple workloads, such as network virtual appliances (NVAs) and DNS servers, in a single location.</span></span>
* <span data-ttu-id="6bc87-113">**克服訂用帳戶限制**：將不同訂用帳戶中的 VNet 對等互連到中心中樞。</span><span class="sxs-lookup"><span data-stu-id="6bc87-113">**Overcome subscriptions limits** by peering VNets from different subscriptions to the central hub.</span></span>
* <span data-ttu-id="6bc87-114">**關注點分離**：中央 IT (SecOps、InfraOps) 和工作負載 (DevOps)。</span><span class="sxs-lookup"><span data-stu-id="6bc87-114">**Separation of concerns** between central IT (SecOps, InfraOps) and workloads (DevOps).</span></span>

<span data-ttu-id="6bc87-115">此架構的一般使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="6bc87-115">Typical uses for this architecture include:</span></span>

* <span data-ttu-id="6bc87-116">在不同環境 (例如開發、測試和生產環境) 下部署，且需要共用服務 (例如 DNS、IDS、NTP 或 AD DS) 的工作負載。</span><span class="sxs-lookup"><span data-stu-id="6bc87-116">Workloads deployed in different environments, such as development, testing, and production, that require shared services such as DNS, IDS, NTP, or AD DS.</span></span> <span data-ttu-id="6bc87-117">系統會將共用服務置於中樞 VNet，同時將每個環境部署至輪輻以維持隔離。</span><span class="sxs-lookup"><span data-stu-id="6bc87-117">Shared services are placed in the hub VNet, while each environment is deployed to a spoke to maintain isolation.</span></span>
* <span data-ttu-id="6bc87-118">不需要彼此連線，但需要存取共用服務的工作負載。</span><span class="sxs-lookup"><span data-stu-id="6bc87-118">Workloads that do not require connectivity to each other, but require access to shared services.</span></span>
* <span data-ttu-id="6bc87-119">需要集中控制安全性層面 (例如，在中樞當作 DMZ 的防火牆)，並對每個輪輻中的工作負載進行分離管理的企業。</span><span class="sxs-lookup"><span data-stu-id="6bc87-119">Enterprises that require central control over security aspects, such as a firewall in the hub as a DMZ, and segregated management for the workloads in each spoke.</span></span>

## <a name="architecture"></a><span data-ttu-id="6bc87-120">架構</span><span class="sxs-lookup"><span data-stu-id="6bc87-120">Architecture</span></span>

<span data-ttu-id="6bc87-121">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="6bc87-121">The architecture consists of the following components.</span></span>

* <span data-ttu-id="6bc87-122">**內部部署網路**。</span><span class="sxs-lookup"><span data-stu-id="6bc87-122">**On-premises network**.</span></span> <span data-ttu-id="6bc87-123">在組織內執行的私用區域網路。</span><span class="sxs-lookup"><span data-stu-id="6bc87-123">A private local-area network running within an organization.</span></span>

* <span data-ttu-id="6bc87-124">**VPN 裝置**。</span><span class="sxs-lookup"><span data-stu-id="6bc87-124">**VPN device**.</span></span> <span data-ttu-id="6bc87-125">可對內部部署網路提供外部連線的裝置或服務。</span><span class="sxs-lookup"><span data-stu-id="6bc87-125">A device or service that provides external connectivity to the on-premises network.</span></span> <span data-ttu-id="6bc87-126">VPN 裝置可能是硬體裝置或軟體解決方案，例如，Windows Server 2012 中的路由及遠端存取服務 (RRAS)。</span><span class="sxs-lookup"><span data-stu-id="6bc87-126">The VPN device may be a hardware device, or a software solution such as the Routing and Remote Access Service (RRAS) in Windows Server 2012.</span></span> <span data-ttu-id="6bc87-127">如需支援的 VPN 設備清單，以及設定選取的 VPN 設備以連線至 Azure 的相關資訊，請參閱[關於站對站 VPN 閘道連線的 VPN 裝置][vpn-appliance]。</span><span class="sxs-lookup"><span data-stu-id="6bc87-127">For a list of supported VPN appliances and information on configuring selected VPN appliances for connecting to Azure, see [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance].</span></span>

* <span data-ttu-id="6bc87-128">**VPN 虛擬網路閘道或 ExpressRoute 閘道**。</span><span class="sxs-lookup"><span data-stu-id="6bc87-128">**VPN virtual network gateway or ExpressRoute gateway**.</span></span> <span data-ttu-id="6bc87-129">虛擬網路閘道可讓 VNet 連線到與內部部署網路連線所使用的 VPN 裝置或 ExpressRoute 線路。</span><span class="sxs-lookup"><span data-stu-id="6bc87-129">The virtual network gateway enables the VNet to connect to the VPN device, or ExpressRoute circuit, used for connectivity with your on-premises network.</span></span> <span data-ttu-id="6bc87-130">如需詳細資訊，請參閱[將內部部署網路連線至 Microsoft Azure 虛擬網路][connect-to-an-Azure-vnet]。</span><span class="sxs-lookup"><span data-stu-id="6bc87-130">For more information, see [Connect an on-premises network to a Microsoft Azure virtual network][connect-to-an-Azure-vnet].</span></span>

> [!NOTE]
> <span data-ttu-id="6bc87-131">此參考架構的部署指令碼會使用 VPN 閘道進行連線，並使用 Azure 中的 VNet 模擬內部部署網路。</span><span class="sxs-lookup"><span data-stu-id="6bc87-131">The deployment scripts for this reference architecture use a VPN gateway for connectivity, and a VNet in Azure to simulate your on-premises network.</span></span>

* <span data-ttu-id="6bc87-132">**中樞 VNet**。</span><span class="sxs-lookup"><span data-stu-id="6bc87-132">**Hub VNet**.</span></span> <span data-ttu-id="6bc87-133">在中樞輪輻拓撲當作中樞使用的 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="6bc87-133">Azure VNet used as the hub in the hub-spoke topology.</span></span> <span data-ttu-id="6bc87-134">中樞是內部部署網路的連線中心點，也是裝載可由輪輻 VNet 中裝載的不同工作負載使用之服務的位置。</span><span class="sxs-lookup"><span data-stu-id="6bc87-134">The hub is the central point of connectivity to your on-premises network, and a place to host services that can be consumed by the different workloads hosted in the spoke VNets.</span></span>

* <span data-ttu-id="6bc87-135">**閘道子網路**。</span><span class="sxs-lookup"><span data-stu-id="6bc87-135">**Gateway subnet**.</span></span> <span data-ttu-id="6bc87-136">虛擬網路閘道會保留在相同的子網路中。</span><span class="sxs-lookup"><span data-stu-id="6bc87-136">The virtual network gateways are held in the same subnet.</span></span>

* <span data-ttu-id="6bc87-137">**輪輻 VNet**。</span><span class="sxs-lookup"><span data-stu-id="6bc87-137">**Spoke VNets**.</span></span> <span data-ttu-id="6bc87-138">在中樞輪輻拓撲中用來當作輪輻的一個或多個 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="6bc87-138">One or more Azure VNets that are used as spokes in the hub-spoke topology.</span></span> <span data-ttu-id="6bc87-139">輪輻可在自己的 VNet 中，用來隔離從其他輪輻分開管理的工作負載。</span><span class="sxs-lookup"><span data-stu-id="6bc87-139">Spokes can be used to isolate workloads in their own VNets, managed separately from other spokes.</span></span> <span data-ttu-id="6bc87-140">每個工作負載在透過 Azure 負載平衡器連線多個子網路的情況下，都可能包括多層。</span><span class="sxs-lookup"><span data-stu-id="6bc87-140">Each workload might include multiple tiers, with multiple subnets connected through Azure load balancers.</span></span> <span data-ttu-id="6bc87-141">如需有關應用程式基礎結構的詳細資訊，請參閱[執行 Windows VM 工作負載][windows-vm-ra]和[執行 Linux VM 工作負載][linux-vm-ra]。</span><span class="sxs-lookup"><span data-stu-id="6bc87-141">For more information about the application infrastructure, see [Running Windows VM workloads][windows-vm-ra] and [Running Linux VM workloads][linux-vm-ra].</span></span>

* <span data-ttu-id="6bc87-142">**VNet 對等互連**。</span><span class="sxs-lookup"><span data-stu-id="6bc87-142">**VNet peering**.</span></span> <span data-ttu-id="6bc87-143">使用[對等連線][vnet-peering]可以連線兩個 VNet。</span><span class="sxs-lookup"><span data-stu-id="6bc87-143">Two VNets can be connected using a [peering connection][vnet-peering].</span></span> <span data-ttu-id="6bc87-144">對等連線是 VNet 之間不可轉移的低延遲連線。</span><span class="sxs-lookup"><span data-stu-id="6bc87-144">Peering connections are non-transitive, low latency connections between VNets.</span></span> <span data-ttu-id="6bc87-145">因此，一旦對等互連之後，VNet 就會使用 Azure 骨幹交換流量，而不需要使用路由器。</span><span class="sxs-lookup"><span data-stu-id="6bc87-145">Once peered, the VNets exchange traffic by using the Azure backbone, without the need for a router.</span></span> <span data-ttu-id="6bc87-146">在中樞輪輻網路拓撲中，您可以使用 VNet 對等互連，將中樞連線至每個輪輻。</span><span class="sxs-lookup"><span data-stu-id="6bc87-146">In a hub-spoke network topology, you use VNet peering to connect the hub to each spoke.</span></span> <span data-ttu-id="6bc87-147">您可以將相同區域或不同區域中的虛擬網路對等互連。</span><span class="sxs-lookup"><span data-stu-id="6bc87-147">You can peer virtual networks in the same region, or different regions.</span></span> <span data-ttu-id="6bc87-148">如需詳細資訊，請參閱[需求和限制][vnet-peering-requirements]。</span><span class="sxs-lookup"><span data-stu-id="6bc87-148">For more information, see [Requirements and constraints][vnet-peering-requirements].</span></span>

> [!NOTE]
> <span data-ttu-id="6bc87-149">本文僅涵蓋 [Resource Manager](/azure/azure-resource-manager/resource-group-overview) 部署，但是您也可以在相同的訂用帳戶中，將傳統 VNet 連線至 Resource Manager VNet。</span><span class="sxs-lookup"><span data-stu-id="6bc87-149">This article only covers [Resource Manager](/azure/azure-resource-manager/resource-group-overview) deployments, but you can also connect a classic VNet to a Resource Manager VNet in the same subscription.</span></span> <span data-ttu-id="6bc87-150">如此一來，您的輪輻就可以裝載傳統部署，而且仍然受益於中樞中共用的服務。</span><span class="sxs-lookup"><span data-stu-id="6bc87-150">That way, your spokes can host classic deployments and still benefit from services shared in the hub.</span></span>

## <a name="recommendations"></a><span data-ttu-id="6bc87-151">建議</span><span class="sxs-lookup"><span data-stu-id="6bc87-151">Recommendations</span></span>

<span data-ttu-id="6bc87-152">下列建議適用於大部分的案例。</span><span class="sxs-lookup"><span data-stu-id="6bc87-152">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="6bc87-153">除非您有特定的需求會覆寫它們，否則請遵循下列建議。</span><span class="sxs-lookup"><span data-stu-id="6bc87-153">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="resource-groups"></a><span data-ttu-id="6bc87-154">資源群組</span><span class="sxs-lookup"><span data-stu-id="6bc87-154">Resource groups</span></span>

<span data-ttu-id="6bc87-155">中樞 VNet 以及每個輪輻 VNet 都可以在不同的資源群組，甚至是不同的訂用帳戶中實作。</span><span class="sxs-lookup"><span data-stu-id="6bc87-155">The hub VNet, and each spoke VNet, can be implemented in different resource groups, and even different subscriptions.</span></span> <span data-ttu-id="6bc87-156">當您將不同訂用帳戶中的虛擬網路對等互連時，這兩個訂用帳戶可以與相同或不同的 Azure Active Directory 租用戶相關聯。</span><span class="sxs-lookup"><span data-stu-id="6bc87-156">When you peer virtual networks in different subscriptions, both subscriptions can be associated to the same or different Azure Active Directory tenant.</span></span> <span data-ttu-id="6bc87-157">如此可對每個工作負載進行非集中式管理，同時在中樞 VNet 中維護共用服務。</span><span class="sxs-lookup"><span data-stu-id="6bc87-157">This allows for a decentralized management of each workload, while sharing services maintained in the hub VNet.</span></span> 

### <a name="vnet-and-gatewaysubnet"></a><span data-ttu-id="6bc87-158">VNet 和 GatewaySubnet</span><span class="sxs-lookup"><span data-stu-id="6bc87-158">VNet and GatewaySubnet</span></span>

<span data-ttu-id="6bc87-159">建立一個名為 *GatewaySubnet* 的子網路，且其位址範圍為 /27。</span><span class="sxs-lookup"><span data-stu-id="6bc87-159">Create a subnet named *GatewaySubnet*, with an address range of /27.</span></span> <span data-ttu-id="6bc87-160">虛擬網路閘道需要使用這個子網路。</span><span class="sxs-lookup"><span data-stu-id="6bc87-160">This subnet is required by the virtual network gateway.</span></span> <span data-ttu-id="6bc87-161">將 32 個位址配置給這個子網路，將有助於防止未來達到閘道大小限制。</span><span class="sxs-lookup"><span data-stu-id="6bc87-161">Allocating 32 addresses to this subnet will help to prevent reaching gateway size limitations in the future.</span></span>

<span data-ttu-id="6bc87-162">如需有關設定閘道的詳細資訊，請根據您的連線類型，參閱下列參考架構：</span><span class="sxs-lookup"><span data-stu-id="6bc87-162">For more information about setting up the gateway, see the following reference architectures, depending on your connection type:</span></span>

- <span data-ttu-id="6bc87-163">[使用 ExpressRoute 的混合式網路][guidance-expressroute]</span><span class="sxs-lookup"><span data-stu-id="6bc87-163">[Hybrid network using ExpressRoute][guidance-expressroute]</span></span>
- <span data-ttu-id="6bc87-164">[使用 VPN 閘道的混合式網路][guidance-vpn]</span><span class="sxs-lookup"><span data-stu-id="6bc87-164">[Hybrid network using a VPN gateway][guidance-vpn]</span></span>

<span data-ttu-id="6bc87-165">如需高可用性，您可以使用 ExpressRoute 加上 VPN 進行容錯移轉。</span><span class="sxs-lookup"><span data-stu-id="6bc87-165">For higher availability, you can use ExpressRoute plus a VPN for failover.</span></span> <span data-ttu-id="6bc87-166">請參閱[使用 ExpressRoute 搭配 VPN 容錯移轉，將內部部署網路連線至 Azure][hybrid-ha]。</span><span class="sxs-lookup"><span data-stu-id="6bc87-166">See [Connect an on-premises network to Azure using ExpressRoute with VPN failover][hybrid-ha].</span></span>

<span data-ttu-id="6bc87-167">如果您不需要與內部部署網路連線，則不需要閘道也可以使用中樞輪輻拓撲。</span><span class="sxs-lookup"><span data-stu-id="6bc87-167">A hub-spoke topology can also be used without a gateway, if you don't need connectivity with your on-premises network.</span></span> 

### <a name="vnet-peering"></a><span data-ttu-id="6bc87-168">VNet 對等互連</span><span class="sxs-lookup"><span data-stu-id="6bc87-168">VNet peering</span></span>

<span data-ttu-id="6bc87-169">VNet 對等互連是兩個 VNet 之間不可轉移的關聯性。</span><span class="sxs-lookup"><span data-stu-id="6bc87-169">VNet peering is a non-transitive relationship between two VNets.</span></span> <span data-ttu-id="6bc87-170">如果您需要輪輻彼此連線，請考慮在這些輪輻之間加入個別的對等連線。</span><span class="sxs-lookup"><span data-stu-id="6bc87-170">If you require spokes to connect to each other, consider adding a separate peering connection between those spokes.</span></span>

<span data-ttu-id="6bc87-171">不過，如果您有數個需要彼此連線的輪輻，將會因為[每個 VNet 的 VNet 對等互連數目限制][vnet-peering-limit]，而非常快速地用盡可能的對等連線。</span><span class="sxs-lookup"><span data-stu-id="6bc87-171">However, if you have several spokes that need to connect with each other, you will run out of possible peering connections very quickly due to the [limitation on number of VNets peerings per VNet][vnet-peering-limit].</span></span> <span data-ttu-id="6bc87-172">在此案例中，請考慮使用使用者定義的路由 (UDR)，強制將目的地為輪輻的流量傳送到當作中樞 VNet 路由器的 NVA。</span><span class="sxs-lookup"><span data-stu-id="6bc87-172">In this scenario, consider using user defined routes (UDRs) to force traffic destined to a spoke to be sent to an NVA acting as a router at the hub VNet.</span></span> <span data-ttu-id="6bc87-173">這可讓輪輻彼此連線。</span><span class="sxs-lookup"><span data-stu-id="6bc87-173">This will allow the spokes to connect to each other.</span></span>

<span data-ttu-id="6bc87-174">您也可以將輪輻設定為使用中樞 VNet 閘道，與遠端網路進行通訊。</span><span class="sxs-lookup"><span data-stu-id="6bc87-174">You can also configure spokes to use the hub VNet gateway to communicate with remote networks.</span></span> <span data-ttu-id="6bc87-175">若要允許閘道流量從輪輻流向中樞，然後連線至遠端網路，您必須：</span><span class="sxs-lookup"><span data-stu-id="6bc87-175">To allow gateway traffic to flow from spoke to hub, and connect to remote networks, you must:</span></span>

  - <span data-ttu-id="6bc87-176">將中樞中的 VNet 對等連線設定為**允許閘道傳輸**。</span><span class="sxs-lookup"><span data-stu-id="6bc87-176">Configure the VNet peering connection in the hub to **allow gateway transit**.</span></span>
  - <span data-ttu-id="6bc87-177">將每個輪輻中的 VNet 對等連線設定為**使用遠端閘道**。</span><span class="sxs-lookup"><span data-stu-id="6bc87-177">Configure the VNet peering connection in each spoke to **use remote gateways**.</span></span>
  - <span data-ttu-id="6bc87-178">將所有 VNet 對等連線設定為**允許轉送的流量**。</span><span class="sxs-lookup"><span data-stu-id="6bc87-178">Configure all VNet peering connections to **allow forwarded traffic**.</span></span>

## <a name="considerations"></a><span data-ttu-id="6bc87-179">考量</span><span class="sxs-lookup"><span data-stu-id="6bc87-179">Considerations</span></span>

### <a name="spoke-connectivity"></a><span data-ttu-id="6bc87-180">輪輻連線能力</span><span class="sxs-lookup"><span data-stu-id="6bc87-180">Spoke connectivity</span></span>

<span data-ttu-id="6bc87-181">如果您需要在輪輻之間連線，請考慮實作 NVA 以便在中樞路由傳送，並在輪輻中使用 UDR，將流量轉送到中樞。</span><span class="sxs-lookup"><span data-stu-id="6bc87-181">If you require connectivity between spokes, consider implementing an NVA for routing in the hub, and using UDRs in the spoke to forward traffic to the hub.</span></span>

<span data-ttu-id="6bc87-182">![[2]][2]</span><span class="sxs-lookup"><span data-stu-id="6bc87-182">![[2]][2]</span></span>

<span data-ttu-id="6bc87-183">在此案例中，您必須將對等連線設定為**允許轉送的流量**。</span><span class="sxs-lookup"><span data-stu-id="6bc87-183">In this scenario, you must configure the peering connections to **allow forwarded traffic**.</span></span>

### <a name="overcoming-vnet-peering-limits"></a><span data-ttu-id="6bc87-184">克服 VNet 對等互連的限制</span><span class="sxs-lookup"><span data-stu-id="6bc87-184">Overcoming VNet peering limits</span></span>

<span data-ttu-id="6bc87-185">請確定您有在 Azure 中考慮[每個 VNet 的 VNet 對等互連數目限制][vnet-peering-limit]。</span><span class="sxs-lookup"><span data-stu-id="6bc87-185">Make sure you consider the [limitation on number of VNets peerings per VNet][vnet-peering-limit] in Azure.</span></span> <span data-ttu-id="6bc87-186">如果您決定您需要的輪輻比允許的限制還多，請考慮建立中樞輪輻中樞輪輻拓撲，其中輪輻的第一層也可作為中樞。</span><span class="sxs-lookup"><span data-stu-id="6bc87-186">If you decide you need more spokes than the limit will allow, consider creating a hub-spoke-hub-spoke topology, where the first level of spokes also act as hubs.</span></span> <span data-ttu-id="6bc87-187">下圖顯示這個方法。</span><span class="sxs-lookup"><span data-stu-id="6bc87-187">The following diagram shows this approach.</span></span>

<span data-ttu-id="6bc87-188">![[3]][3]</span><span class="sxs-lookup"><span data-stu-id="6bc87-188">![[3]][3]</span></span>

<span data-ttu-id="6bc87-189">此外，請考慮要在中樞共用哪些服務，以確保中樞可針對大量輪輻進行調整。</span><span class="sxs-lookup"><span data-stu-id="6bc87-189">Also consider what services are shared in the hub, to ensure the hub scales for a larger number of spokes.</span></span> <span data-ttu-id="6bc87-190">例如，如果您的中樞提供防火牆服務，請在新增多個輪輻時，考慮防火牆解決方案的頻寬限制。</span><span class="sxs-lookup"><span data-stu-id="6bc87-190">For instance, if your hub provides firewall services, consider the bandwidth limits of your firewall solution when adding multiple spokes.</span></span> <span data-ttu-id="6bc87-191">您可以將其中部分共用服務移到中樞的第二層。</span><span class="sxs-lookup"><span data-stu-id="6bc87-191">You might want to move some of these shared services to a second level of hubs.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="6bc87-192">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="6bc87-192">Deploy the solution</span></span>

<span data-ttu-id="6bc87-193">適用於此架構的部署可在 [GitHub][ref-arch-repo] 上取得。</span><span class="sxs-lookup"><span data-stu-id="6bc87-193">A deployment for this architecture is available on [GitHub][ref-arch-repo].</span></span> <span data-ttu-id="6bc87-194">它會使用每個 VNet 中的 VM 來測試連線。</span><span class="sxs-lookup"><span data-stu-id="6bc87-194">It uses VMs in each VNet to test connectivity.</span></span> <span data-ttu-id="6bc87-195">**中樞 VNet** 的**共用服務**子網路中沒有裝載任何實際的服務。</span><span class="sxs-lookup"><span data-stu-id="6bc87-195">There are no actual services hosted in the **shared-services** subnet in the **hub VNet**.</span></span>

<span data-ttu-id="6bc87-196">此部署會在您的訂用帳戶中建立下列資源群組︰</span><span class="sxs-lookup"><span data-stu-id="6bc87-196">The deployment creates the following resource groups in your subscription:</span></span>

- <span data-ttu-id="6bc87-197">hub-nva-rg</span><span class="sxs-lookup"><span data-stu-id="6bc87-197">hub-nva-rg</span></span>
- <span data-ttu-id="6bc87-198">hub-vnet-rg</span><span class="sxs-lookup"><span data-stu-id="6bc87-198">hub-vnet-rg</span></span>
- <span data-ttu-id="6bc87-199">onprem-jb-rg</span><span class="sxs-lookup"><span data-stu-id="6bc87-199">onprem-jb-rg</span></span>
- <span data-ttu-id="6bc87-200">onprem-vnet-rg</span><span class="sxs-lookup"><span data-stu-id="6bc87-200">onprem-vnet-rg</span></span>
- <span data-ttu-id="6bc87-201">spoke1-vnet-rg</span><span class="sxs-lookup"><span data-stu-id="6bc87-201">spoke1-vnet-rg</span></span>
- <span data-ttu-id="6bc87-202">spoke2-vent-rg</span><span class="sxs-lookup"><span data-stu-id="6bc87-202">spoke2-vent-rg</span></span>

<span data-ttu-id="6bc87-203">範本參數檔案會參照這些名稱，因此，如果您變更這些名稱，請據以更新參數檔案。</span><span class="sxs-lookup"><span data-stu-id="6bc87-203">The template parameter files refer to these names, so if you change them, update the parameter files to match.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="6bc87-204">必要條件</span><span class="sxs-lookup"><span data-stu-id="6bc87-204">Prerequisites</span></span>

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-the-simulated-on-premises-datacenter"></a><span data-ttu-id="6bc87-205">部署模擬的內部部署資料中心</span><span class="sxs-lookup"><span data-stu-id="6bc87-205">Deploy the simulated on-premises datacenter</span></span>

<span data-ttu-id="6bc87-206">若要將模擬的內部部署資料中心部署為 Azure VNet，請遵循下列步驟：</span><span class="sxs-lookup"><span data-stu-id="6bc87-206">To deploy the simulated on-premises datacenter as an Azure VNet, follow these steps:</span></span>

1. <span data-ttu-id="6bc87-207">瀏覽至參考架構存放庫的 `hybrid-networking/hub-spoke` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="6bc87-207">Navigate to the `hybrid-networking/hub-spoke` folder of the reference architectures repository.</span></span>

2. <span data-ttu-id="6bc87-208">開啟 `onprem.json` 檔案。</span><span class="sxs-lookup"><span data-stu-id="6bc87-208">Open the `onprem.json` file.</span></span> <span data-ttu-id="6bc87-209">取代 `adminUsername` 和 `adminPassword` 的值。</span><span class="sxs-lookup"><span data-stu-id="6bc87-209">Replace the values for `adminUsername` and `adminPassword`.</span></span>

    ```bash
    "adminUsername": "<user name>",
    "adminPassword": "<password>",
    ```

3. <span data-ttu-id="6bc87-210">(選擇性) 針對 Linux 部署，請將 `osType` 設為 `Linux`。</span><span class="sxs-lookup"><span data-stu-id="6bc87-210">(Optional) For a Linux deployment, set `osType` to `Linux`.</span></span>

4. <span data-ttu-id="6bc87-211">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6bc87-211">Run the following command:</span></span>

    ```bash
    azbb -s <subscription_id> -g onprem-vnet-rg -l <location> -p onprem.json --deploy
    ```

5. <span data-ttu-id="6bc87-212">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="6bc87-212">Wait for the deployment to finish.</span></span> <span data-ttu-id="6bc87-213">此部署會建立一個虛擬網路、一個虛擬機器，以及一個 VPN 閘道。</span><span class="sxs-lookup"><span data-stu-id="6bc87-213">This deployment creates a virtual network, a virtual machine, and a VPN gateway.</span></span> <span data-ttu-id="6bc87-214">建立 VPN 閘道約需要 40 分鐘的時間。</span><span class="sxs-lookup"><span data-stu-id="6bc87-214">It can take about 40 minutes to create the VPN gateway.</span></span>

### <a name="deploy-the-hub-vnet"></a><span data-ttu-id="6bc87-215">部署中樞 VNet</span><span class="sxs-lookup"><span data-stu-id="6bc87-215">Deploy the hub VNet</span></span>

<span data-ttu-id="6bc87-216">若要部署中樞 VNet，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="6bc87-216">To deploy the hub VNet, perform the following steps.</span></span>

1. <span data-ttu-id="6bc87-217">開啟 `hub-vnet.json` 檔案。</span><span class="sxs-lookup"><span data-stu-id="6bc87-217">Open the `hub-vnet.json` file.</span></span> <span data-ttu-id="6bc87-218">取代 `adminUsername` 和 `adminPassword` 的值。</span><span class="sxs-lookup"><span data-stu-id="6bc87-218">Replace the values for `adminUsername` and `adminPassword`.</span></span>

    ```bash
    "adminUsername": "<user name>",
    "adminPassword": "<password>",
    ```

2. <span data-ttu-id="6bc87-219">(選擇性) 針對 Linux 部署，請將 `osType` 設為 `Linux`。</span><span class="sxs-lookup"><span data-stu-id="6bc87-219">(Optional) For a Linux deployment, set `osType` to `Linux`.</span></span>

3. <span data-ttu-id="6bc87-220">尋找 `sharedKey` 的兩個執行個體，輸入 VPN 連線的共用金鑰。</span><span class="sxs-lookup"><span data-stu-id="6bc87-220">Find both instances of `sharedKey` and enter a shared key for the VPN connection.</span></span> <span data-ttu-id="6bc87-221">這兩個值必須相符。</span><span class="sxs-lookup"><span data-stu-id="6bc87-221">The values must match.</span></span>

    ```bash
    "sharedKey": "",
    ```

4. <span data-ttu-id="6bc87-222">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6bc87-222">Run the following command:</span></span>

    ```bash
    azbb -s <subscription_id> -g hub-vnet-rg -l <location> -p hub-vnet.json --deploy
    ```

5. <span data-ttu-id="6bc87-223">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="6bc87-223">Wait for the deployment to finish.</span></span> <span data-ttu-id="6bc87-224">此部署會建立虛擬網路、虛擬機器、VPN 閘道，以及閘道的連線。</span><span class="sxs-lookup"><span data-stu-id="6bc87-224">This deployment creates a virtual network, a virtual machine, a VPN gateway, and a connection to the gateway.</span></span>  <span data-ttu-id="6bc87-225">建立 VPN 閘道約需要 40 分鐘的時間。</span><span class="sxs-lookup"><span data-stu-id="6bc87-225">It can take about 40 minutes to create the VPN gateway.</span></span>

### <a name="test-connectivity-with-the-hub"></a><span data-ttu-id="6bc87-226">測試中樞的連線</span><span class="sxs-lookup"><span data-stu-id="6bc87-226">Test connectivity with the hub</span></span>

<span data-ttu-id="6bc87-227">測試從模擬的內部部署環境到中樞 VNet 的連線。</span><span class="sxs-lookup"><span data-stu-id="6bc87-227">Test conectivity from the simulated on-premises environment to the hub VNet.</span></span>

<span data-ttu-id="6bc87-228">**Windows 部署**</span><span class="sxs-lookup"><span data-stu-id="6bc87-228">**Windows deployment**</span></span>

1. <span data-ttu-id="6bc87-229">使用 Azure 入口網站尋找 `onprem-jb-rg` 資源群組中名為 `jb-vm1` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="6bc87-229">Use the Azure portal to find the VM named `jb-vm1` in the `onprem-jb-rg` resource group.</span></span>

2. <span data-ttu-id="6bc87-230">按一下 `Connect` 以開啟 VM 的遠端桌面工作階段。</span><span class="sxs-lookup"><span data-stu-id="6bc87-230">Click `Connect` to open a remote desktop session to the VM.</span></span> <span data-ttu-id="6bc87-231">請使用您在 `onprem.json` 參數檔案中指定的密碼。</span><span class="sxs-lookup"><span data-stu-id="6bc87-231">Use the password that you specified in the `onprem.json` parameter file.</span></span>

3. <span data-ttu-id="6bc87-232">在 VM 中開啟 PowerShell 主控台，並使用 `Test-NetConnection` Cmdlet 確認您可以連線至中樞 VNet 中的 jumpbox VM。</span><span class="sxs-lookup"><span data-stu-id="6bc87-232">Open a PowerShell console in the VM, and use the `Test-NetConnection` cmdlet to verify that you can connect to the jumpbox VM in the hub VNet.</span></span>

   ```powershell
   Test-NetConnection 10.0.0.68 -CommonTCPPort RDP
   ```
<span data-ttu-id="6bc87-233">輸出應如下所示：</span><span class="sxs-lookup"><span data-stu-id="6bc87-233">The output should look similar to the following:</span></span>

```powershell
ComputerName     : 10.0.0.68
RemoteAddress    : 10.0.0.68
RemotePort       : 3389
InterfaceAlias   : Ethernet 2
SourceAddress    : 192.168.1.000
TcpTestSucceeded : True
```

> [!NOTE]
> <span data-ttu-id="6bc87-234">根據預設，Windows Server VM 在 Azure 中不允許 ICMP 回應。</span><span class="sxs-lookup"><span data-stu-id="6bc87-234">By default, Windows Server VMs do not allow ICMP responses in Azure.</span></span> <span data-ttu-id="6bc87-235">如果您想要使用 `ping` 來測試連線，您需要在 Windows 進階防火牆中為每個 VM 啟用 ICMP 流量。</span><span class="sxs-lookup"><span data-stu-id="6bc87-235">If you want to use `ping` to test connectivity, you need to enable ICMP traffic in the Windows Advanced Firewall for each VM.</span></span>

<span data-ttu-id="6bc87-236">**Linux 部署**</span><span class="sxs-lookup"><span data-stu-id="6bc87-236">**Linux deployment**</span></span>

1. <span data-ttu-id="6bc87-237">使用 Azure 入口網站尋找 `onprem-jb-rg` 資源群組中名為 `jb-vm1` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="6bc87-237">Use the Azure portal to find the VM named `jb-vm1` in the `onprem-jb-rg` resource group.</span></span>

2. <span data-ttu-id="6bc87-238">按一下 `Connect`，並複製入口網站中顯示的 `ssh` 命令。</span><span class="sxs-lookup"><span data-stu-id="6bc87-238">Click `Connect` and copy the `ssh` command shown in the portal.</span></span> 

3. <span data-ttu-id="6bc87-239">在 Linux 提示字元中執行 `ssh`，以連線至模擬的內部部署環境。</span><span class="sxs-lookup"><span data-stu-id="6bc87-239">From a Linux prompt, run `ssh` to connect to the simulated on-premises environment.</span></span> <span data-ttu-id="6bc87-240">請使用您在 `onprem.json` 參數檔案中指定的密碼。</span><span class="sxs-lookup"><span data-stu-id="6bc87-240">Use the password that you specified in the `onprem.json` parameter file.</span></span>

4. <span data-ttu-id="6bc87-241">使用 `ping` 命令來測試中樞 VNet 中的 jumpbox VM 的連線：</span><span class="sxs-lookup"><span data-stu-id="6bc87-241">Use the `ping` command to test connectivity to the jumpbox VM in the hub VNet:</span></span>

   ```bash
   ping 10.0.0.68
   ```

### <a name="deploy-the-spoke-vnets"></a><span data-ttu-id="6bc87-242">部署輪輻 VNet</span><span class="sxs-lookup"><span data-stu-id="6bc87-242">Deploy the spoke VNets</span></span>

<span data-ttu-id="6bc87-243">若要部署輪輻 VNet，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="6bc87-243">To deploy the spoke VNets, perform the following steps.</span></span>

1. <span data-ttu-id="6bc87-244">開啟 `spoke1.json` 檔案。</span><span class="sxs-lookup"><span data-stu-id="6bc87-244">Open the `spoke1.json` file.</span></span> <span data-ttu-id="6bc87-245">取代 `adminUsername` 和 `adminPassword` 的值。</span><span class="sxs-lookup"><span data-stu-id="6bc87-245">Replace the values for `adminUsername` and `adminPassword`.</span></span>

    ```bash
    "adminUsername": "<user name>",
    "adminPassword": "<password>",
    ```

2. <span data-ttu-id="6bc87-246">(選擇性) 針對 Linux 部署，請將 `osType` 設為 `Linux`。</span><span class="sxs-lookup"><span data-stu-id="6bc87-246">(Optional) For a Linux deployment, set `osType` to `Linux`.</span></span>

3. <span data-ttu-id="6bc87-247">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6bc87-247">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g spoke1-vnet-rg -l <location> -p spoke1.json --deploy
   ```
  
4. <span data-ttu-id="6bc87-248">對 `spoke2.json` 檔案重複步驟 1-2。</span><span class="sxs-lookup"><span data-stu-id="6bc87-248">Repeat steps 1-2 for the `spoke2.json` file.</span></span>

5. <span data-ttu-id="6bc87-249">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6bc87-249">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g spoke2-vnet-rg -l <location> -p spoke2.json --deploy
   ```

6. <span data-ttu-id="6bc87-250">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6bc87-250">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-vnet-rg -l <location> -p hub-vnet-peering.json --deploy
   ```

### <a name="test-connectivity"></a><span data-ttu-id="6bc87-251">測試連線能力</span><span class="sxs-lookup"><span data-stu-id="6bc87-251">Test connectivity</span></span>

<span data-ttu-id="6bc87-252">測試從模擬的內部部署環境到輪輻 VNet 的連線。</span><span class="sxs-lookup"><span data-stu-id="6bc87-252">Test conectivity from the simulated on-premises environment to the spoke VNets.</span></span>

<span data-ttu-id="6bc87-253">**Windows 部署**</span><span class="sxs-lookup"><span data-stu-id="6bc87-253">**Windows deployment**</span></span>

1. <span data-ttu-id="6bc87-254">使用 Azure 入口網站尋找 `onprem-jb-rg` 資源群組中名為 `jb-vm1` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="6bc87-254">Use the Azure portal to find the VM named `jb-vm1` in the `onprem-jb-rg` resource group.</span></span>

2. <span data-ttu-id="6bc87-255">按一下 `Connect` 以開啟 VM 的遠端桌面工作階段。</span><span class="sxs-lookup"><span data-stu-id="6bc87-255">Click `Connect` to open a remote desktop session to the VM.</span></span> <span data-ttu-id="6bc87-256">請使用您在 `onprem.json` 參數檔案中指定的密碼。</span><span class="sxs-lookup"><span data-stu-id="6bc87-256">Use the password that you specified in the `onprem.json` parameter file.</span></span>

3. <span data-ttu-id="6bc87-257">在 VM 中開啟 PowerShell 主控台，並使用 `Test-NetConnection` Cmdlet 確認您可以連線至支點 VNet 中的 jumpbox VM。</span><span class="sxs-lookup"><span data-stu-id="6bc87-257">Open a PowerShell console in the VM, and use the `Test-NetConnection` cmdlet to verify that you can connect to the jumpbox VMs in the spoke VNets.</span></span>

   ```powershell
   Test-NetConnection 10.1.0.68 -CommonTCPPort RDP
   Test-NetConnection 10.2.0.68 -CommonTCPPort RDP
   ```

<span data-ttu-id="6bc87-258">**Linux 部署**</span><span class="sxs-lookup"><span data-stu-id="6bc87-258">**Linux deployment**</span></span>

<span data-ttu-id="6bc87-259">若要測試從模擬的內部部署環境到使用 Linux VM 之輪輻 VNet 的連線，請執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="6bc87-259">To test conectivity from the simulated on-premises environment to the spoke VNets using Linux VMs, perform the following steps:</span></span>

1. <span data-ttu-id="6bc87-260">使用 Azure 入口網站尋找 `onprem-jb-rg` 資源群組中名為 `jb-vm1` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="6bc87-260">Use the Azure portal to find the VM named `jb-vm1` in the `onprem-jb-rg` resource group.</span></span>

2. <span data-ttu-id="6bc87-261">按一下 `Connect`，並複製入口網站中顯示的 `ssh` 命令。</span><span class="sxs-lookup"><span data-stu-id="6bc87-261">Click `Connect` and copy the `ssh` command shown in the portal.</span></span> 

3. <span data-ttu-id="6bc87-262">在 Linux 提示字元中執行 `ssh`，以連線至模擬的內部部署環境。</span><span class="sxs-lookup"><span data-stu-id="6bc87-262">From a Linux prompt, run `ssh` to connect to the simulated on-premises environment.</span></span> <span data-ttu-id="6bc87-263">請使用您在 `onprem.json` 參數檔案中指定的密碼。</span><span class="sxs-lookup"><span data-stu-id="6bc87-263">Use the password that you specified in the `onprem.json` parameter file.</span></span>

5. <span data-ttu-id="6bc87-264">使用 `ping` 命令來測試每個輪輻中的 jumpbox VM 的連線：</span><span class="sxs-lookup"><span data-stu-id="6bc87-264">Use the `ping` command to test connectivity to the jumpbox VMs in each spoke:</span></span>

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

### <a name="add-connectivity-between-spokes"></a><span data-ttu-id="6bc87-265">新增輪輻之間的連線</span><span class="sxs-lookup"><span data-stu-id="6bc87-265">Add connectivity between spokes</span></span>

<span data-ttu-id="6bc87-266">此為選用步驟。</span><span class="sxs-lookup"><span data-stu-id="6bc87-266">This step is optional.</span></span> <span data-ttu-id="6bc87-267">如果您想要允許輪輻彼此連線，您必須使用網路虛擬設備 (NVA) 作為中樞 VNet 中的路由器，並且在嘗試連線至另一個輪輻時強制執行從輪輻到路由器的流量。</span><span class="sxs-lookup"><span data-stu-id="6bc87-267">If you want to allow spokes to connect to each other, you must use a network virtual appliance (NVA) as a router in the hub VNet, and force traffic from spokes to the router when trying to connect to another spoke.</span></span> <span data-ttu-id="6bc87-268">若要部署作為單一 VM 的基本範例 NVA 和使用者定義的路由 (UDR)，讓兩個輪輻 Vnet 能夠連線，請執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="6bc87-268">To deploy a basic sample NVA as a single VM, along with user-defined routes (UDRs) to allow the two spoke VNets to connect, perform the following steps:</span></span>

1. <span data-ttu-id="6bc87-269">開啟 `hub-nva.json` 檔案。</span><span class="sxs-lookup"><span data-stu-id="6bc87-269">Open the `hub-nva.json` file.</span></span> <span data-ttu-id="6bc87-270">取代 `adminUsername` 和 `adminPassword` 的值。</span><span class="sxs-lookup"><span data-stu-id="6bc87-270">Replace the values for `adminUsername` and `adminPassword`.</span></span>

    ```bash
    "adminUsername": "<user name>",
    "adminPassword": "<password>",
    ```

2. <span data-ttu-id="6bc87-271">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6bc87-271">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-nva-rg -l <location> -p hub-nva.json --deploy
   ```

<!-- links -->

[azure-cli-2]: /azure/install-azure-cli
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[azure-vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[best-practices-security]: /azure/best-practices-network-securit
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[guidance-expressroute]: ./expressroute.md
[guidance-vpn]: ./vpn.md
[linux-vm-ra]: ../virtual-machines-linux/index.md
[hybrid-ha]: ./expressroute-vpn-failover.md
[naming conventions]: /azure/guidance/guidance-naming-conventions
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[vnet-peering]: /azure/virtual-network/virtual-network-peering-overview
[vnet-peering-limit]: /azure/azure-subscription-service-limits#networking-limits
[vnet-peering-requirements]: /azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[windows-vm-ra]: ../virtual-machines-windows/index.md

[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-hub-spoke.vsdx
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[0]: ./images/hub-spoke.png "Azure 中的中樞輪輻拓撲"
[1]: ./images/hub-spoke-gateway-routing.svg "Azure 中具有可轉移路由的中樞輪輻拓撲"
[2]: ./images/hub-spoke-no-gateway-routing.svg "Azure 中具有使用 NVA 之可轉移路由的中樞輪輻拓撲"
[3]: ./images/hub-spokehub-spoke.svg "Azure 中的中樞輪輻中樞輪輻拓撲"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/

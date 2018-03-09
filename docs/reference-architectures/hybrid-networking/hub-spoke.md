---
title: "在 Azure 中實作中樞輪輻網路拓撲"
description: "如何在 Azure 中實作中樞輪輻網路拓撲。"
author: telmosampaio
ms.date: 02/23/2018
pnp.series.title: Implement a hub-spoke network topology in Azure
pnp.series.prev: expressroute
ms.openlocfilehash: 1a2855f0d4a903fc4d7a022aef20ea73fe763e2c
ms.sourcegitcommit: 2123c25b1a0b5501ff1887f98030787191cf6994
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="implement-a-hub-spoke-network-topology-in-azure"></a><span data-ttu-id="f5b06-103">在 Azure 中實作中樞輪輻網路拓撲</span><span class="sxs-lookup"><span data-stu-id="f5b06-103">Implement a hub-spoke network topology in Azure</span></span>

<span data-ttu-id="f5b06-104">此參考架構會顯示如何在 Azure 中實作中樞輪輻拓撲。</span><span class="sxs-lookup"><span data-stu-id="f5b06-104">This reference architecture shows how to implement a hub-spoke topology in Azure.</span></span> <span data-ttu-id="f5b06-105">「中樞」是 Azure 中的虛擬網路 (VNet)，可當作內部部署網路的連線中心點。</span><span class="sxs-lookup"><span data-stu-id="f5b06-105">The *hub* is a virtual network (VNet) in Azure that acts as a central point of connectivity to your on-premises network.</span></span> <span data-ttu-id="f5b06-106">「輪輻」是與中樞對等互連的 VNet，可用於隔離工作負載。</span><span class="sxs-lookup"><span data-stu-id="f5b06-106">The *spokes* are VNets that peer with the hub, and can be used to isolate workloads.</span></span> <span data-ttu-id="f5b06-107">流量會透過 ExpressRoute 或 VPN 閘道連線，在內部部署資料中心和中樞之間流動。</span><span class="sxs-lookup"><span data-stu-id="f5b06-107">Traffic flows between the on-premises datacenter and the hub through an ExpressRoute or VPN gateway connection.</span></span>  <span data-ttu-id="f5b06-108">[**部署這個解決方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="f5b06-108">[**Deploy this solution**](#deploy-the-solution).</span></span>

<span data-ttu-id="f5b06-109">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="f5b06-109">![[0]][0]</span></span>

<span data-ttu-id="f5b06-110">*下載這個架構的 [Visio 檔案][visio-download]*</span><span class="sxs-lookup"><span data-stu-id="f5b06-110">*Download a [Visio file][visio-download] of this architecture*</span></span>


<span data-ttu-id="f5b06-111">此拓撲的優點包括：</span><span class="sxs-lookup"><span data-stu-id="f5b06-111">The benefits of this toplogy include:</span></span>

* <span data-ttu-id="f5b06-112">**節省成本**：將可由多個工作負載共用的服務 (例如網路虛擬裝置 (NVA) 和 DNS 伺服器) 集中在單一位置。</span><span class="sxs-lookup"><span data-stu-id="f5b06-112">**Cost savings** by centralizing services that can be shared by multiple workloads, such as network virtual appliances (NVAs) and DNS servers, in a single location.</span></span>
* <span data-ttu-id="f5b06-113">**克服訂用帳戶限制**：將不同訂用帳戶中的 VNet 對等互連到中心中樞。</span><span class="sxs-lookup"><span data-stu-id="f5b06-113">**Overcome subscriptions limits** by peering VNets from different subscriptions to the central hub.</span></span>
* <span data-ttu-id="f5b06-114">**關注點分離**：中央 IT (SecOps、InfraOps) 和工作負載 (DevOps)。</span><span class="sxs-lookup"><span data-stu-id="f5b06-114">**Separation of concerns** between central IT (SecOps, InfraOps) and workloads (DevOps).</span></span>

<span data-ttu-id="f5b06-115">此架構的一般使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="f5b06-115">Typical uses for this architecture include:</span></span>

* <span data-ttu-id="f5b06-116">在不同環境 (例如開發、測試和生產環境) 下部署，且需要共用服務 (例如 DNS、IDS、NTP 或 AD DS) 的工作負載。</span><span class="sxs-lookup"><span data-stu-id="f5b06-116">Workloads deployed in different environments, such as development, testing, and production, that require shared services such as DNS, IDS, NTP, or AD DS.</span></span> <span data-ttu-id="f5b06-117">系統會將共用服務置於中樞 VNet，同時將每個環境部署至輪輻以維持隔離。</span><span class="sxs-lookup"><span data-stu-id="f5b06-117">Shared services are placed in the hub VNet, while each environment is deployed to a spoke to maintain isolation.</span></span>
* <span data-ttu-id="f5b06-118">不需要彼此連線，但需要存取共用服務的工作負載。</span><span class="sxs-lookup"><span data-stu-id="f5b06-118">Workloads that do not require connectivity to each other, but require access to shared services.</span></span>
* <span data-ttu-id="f5b06-119">需要集中控制安全性層面 (例如，在中樞當作 DMZ 的防火牆)，並對每個輪輻中的工作負載進行分離管理的企業。</span><span class="sxs-lookup"><span data-stu-id="f5b06-119">Enterprises that require central control over security aspects, such as a firewall in the hub as a DMZ, and segregated management for the workloads in each spoke.</span></span>

## <a name="architecture"></a><span data-ttu-id="f5b06-120">架構</span><span class="sxs-lookup"><span data-stu-id="f5b06-120">Architecture</span></span>

<span data-ttu-id="f5b06-121">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="f5b06-121">The architecture consists of the following components.</span></span>

* <span data-ttu-id="f5b06-122">**內部部署網路**。</span><span class="sxs-lookup"><span data-stu-id="f5b06-122">**On-premises network**.</span></span> <span data-ttu-id="f5b06-123">在組織內執行的私用區域網路。</span><span class="sxs-lookup"><span data-stu-id="f5b06-123">A private local-area network running within an organization.</span></span>

* <span data-ttu-id="f5b06-124">**VPN 裝置**。</span><span class="sxs-lookup"><span data-stu-id="f5b06-124">**VPN device**.</span></span> <span data-ttu-id="f5b06-125">可對內部部署網路提供外部連線的裝置或服務。</span><span class="sxs-lookup"><span data-stu-id="f5b06-125">A device or service that provides external connectivity to the on-premises network.</span></span> <span data-ttu-id="f5b06-126">VPN 裝置可能是硬體裝置或軟體解決方案，例如，Windows Server 2012 中的路由及遠端存取服務 (RRAS)。</span><span class="sxs-lookup"><span data-stu-id="f5b06-126">The VPN device may be a hardware device, or a software solution such as the Routing and Remote Access Service (RRAS) in Windows Server 2012.</span></span> <span data-ttu-id="f5b06-127">如需支援的 VPN 設備清單，以及設定選取的 VPN 設備以連線至 Azure 的相關資訊，請參閱[關於站對站 VPN 閘道連線的 VPN 裝置][vpn-appliance]。</span><span class="sxs-lookup"><span data-stu-id="f5b06-127">For a list of supported VPN appliances and information on configuring selected VPN appliances for connecting to Azure, see [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance].</span></span>

* <span data-ttu-id="f5b06-128">**VPN 虛擬網路閘道或 ExpressRoute 閘道**。</span><span class="sxs-lookup"><span data-stu-id="f5b06-128">**VPN virtual network gateway or ExpressRoute gateway**.</span></span> <span data-ttu-id="f5b06-129">虛擬網路閘道可讓 VNet 連線到與內部部署網路連線所使用的 VPN 裝置或 ExpressRoute 線路。</span><span class="sxs-lookup"><span data-stu-id="f5b06-129">The virtual network gateway enables the VNet to connect to the VPN device, or ExpressRoute circuit, used for connectivity with your on-premises network.</span></span> <span data-ttu-id="f5b06-130">如需詳細資訊，請參閱[將內部部署網路連線至 Microsoft Azure 虛擬網路][connect-to-an-Azure-vnet]。</span><span class="sxs-lookup"><span data-stu-id="f5b06-130">For more information, see [Connect an on-premises network to a Microsoft Azure virtual network][connect-to-an-Azure-vnet].</span></span>

> [!NOTE]
> <span data-ttu-id="f5b06-131">此參考架構的部署指令碼會使用 VPN 閘道進行連線，並使用 Azure 中的 VNet 模擬內部部署網路。</span><span class="sxs-lookup"><span data-stu-id="f5b06-131">The deployment scripts for this reference architecture use a VPN gateway for connectivity, and a VNet in Azure to simulate your on-premises network.</span></span>

* <span data-ttu-id="f5b06-132">**中樞 VNet**。</span><span class="sxs-lookup"><span data-stu-id="f5b06-132">**Hub VNet**.</span></span> <span data-ttu-id="f5b06-133">在中樞輪輻拓撲當作中樞使用的 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="f5b06-133">Azure VNet used as the hub in the hub-spoke topology.</span></span> <span data-ttu-id="f5b06-134">中樞是內部部署網路的連線中心點，也是裝載可由輪輻 VNet 中裝載的不同工作負載使用之服務的位置。</span><span class="sxs-lookup"><span data-stu-id="f5b06-134">The hub is the central point of connectivity to your on-premises network, and a place to host services that can be consumed by the different workloads hosted in the spoke VNets.</span></span>

* <span data-ttu-id="f5b06-135">**閘道子網路**。</span><span class="sxs-lookup"><span data-stu-id="f5b06-135">**Gateway subnet**.</span></span> <span data-ttu-id="f5b06-136">虛擬網路閘道會保留在相同的子網路中。</span><span class="sxs-lookup"><span data-stu-id="f5b06-136">The virtual network gateways are held in the same subnet.</span></span>

* <span data-ttu-id="f5b06-137">**輪輻 VNet**。</span><span class="sxs-lookup"><span data-stu-id="f5b06-137">**Spoke VNets**.</span></span> <span data-ttu-id="f5b06-138">在中樞輪輻拓撲中用來當作輪輻的一個或多個 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="f5b06-138">One or more Azure VNets that are used as spokes in the hub-spoke topology.</span></span> <span data-ttu-id="f5b06-139">輪輻可在自己的 VNet 中，用來隔離從其他輪輻分開管理的工作負載。</span><span class="sxs-lookup"><span data-stu-id="f5b06-139">Spokes can be used to isolate workloads in their own VNets, managed separately from other spokes.</span></span> <span data-ttu-id="f5b06-140">每個工作負載在透過 Azure 負載平衡器連線多個子網路的情況下，都可能包括多層。</span><span class="sxs-lookup"><span data-stu-id="f5b06-140">Each workload might include multiple tiers, with multiple subnets connected through Azure load balancers.</span></span> <span data-ttu-id="f5b06-141">如需有關應用程式基礎結構的詳細資訊，請參閱[執行 Windows VM 工作負載][windows-vm-ra]和[執行 Linux VM 工作負載][linux-vm-ra]。</span><span class="sxs-lookup"><span data-stu-id="f5b06-141">For more information about the application infrastructure, see [Running Windows VM workloads][windows-vm-ra] and [Running Linux VM workloads][linux-vm-ra].</span></span>

* <span data-ttu-id="f5b06-142">**VNet 對等互連**。</span><span class="sxs-lookup"><span data-stu-id="f5b06-142">**VNet peering**.</span></span> <span data-ttu-id="f5b06-143">在相同的 Azure 區域中，可以使用[對等連線][vnet-peering]來連線兩個 VNet。</span><span class="sxs-lookup"><span data-stu-id="f5b06-143">Two VNets in the same Azure region can be connected using a [peering connection][vnet-peering].</span></span> <span data-ttu-id="f5b06-144">對等連線是 VNet 之間不可轉移的低延遲連線。</span><span class="sxs-lookup"><span data-stu-id="f5b06-144">Peering connections are non-transitive, low latency connections between VNets.</span></span> <span data-ttu-id="f5b06-145">因此，一旦對等互連之後，VNet 就會使用 Azure 骨幹交換流量，而不需要使用路由器。</span><span class="sxs-lookup"><span data-stu-id="f5b06-145">Once peered, the VNets exchange traffic by using the Azure backbone, without the need for a router.</span></span> <span data-ttu-id="f5b06-146">在中樞輪輻網路拓撲中，您可以使用 VNet 對等互連，將中樞連線至每個輪輻。</span><span class="sxs-lookup"><span data-stu-id="f5b06-146">In a hub-spoke network topology, you use VNet peering to connect the hub to each spoke.</span></span>

> [!NOTE]
> <span data-ttu-id="f5b06-147">本文僅涵蓋 [Resource Manager](/azure/azure-resource-manager/resource-group-overview) 部署，但是您也可以在相同的訂用帳戶中，將傳統 VNet 連線至 Resource Manager VNet。</span><span class="sxs-lookup"><span data-stu-id="f5b06-147">This article only covers [Resource Manager](/azure/azure-resource-manager/resource-group-overview) deployments, but you can also connect a classic VNet to a Resource Manager VNet in the same subscription.</span></span> <span data-ttu-id="f5b06-148">如此一來，您的輪輻就可以裝載傳統部署，而且仍然受益於中樞中共用的服務。</span><span class="sxs-lookup"><span data-stu-id="f5b06-148">That way, your spokes can host classic deployments and still benefit from services shared in the hub.</span></span>

## <a name="recommendations"></a><span data-ttu-id="f5b06-149">建議</span><span class="sxs-lookup"><span data-stu-id="f5b06-149">Recommendations</span></span>

<span data-ttu-id="f5b06-150">下列建議適用於大部分的案例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-150">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="f5b06-151">除非您有特定的需求會覆寫它們，否則請遵循下列建議。</span><span class="sxs-lookup"><span data-stu-id="f5b06-151">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="resource-groups"></a><span data-ttu-id="f5b06-152">資源群組</span><span class="sxs-lookup"><span data-stu-id="f5b06-152">Resource groups</span></span>

<span data-ttu-id="f5b06-153">中樞 VNet 以及每個輪輻 VNet 都可以在不同的資源群組，甚至是不同的訂用帳戶中實作，只要這些訂用帳戶屬於相同 Azure 區域中的相同 Azure Active Directory (Azure AD) 租用戶即可。</span><span class="sxs-lookup"><span data-stu-id="f5b06-153">The hub VNet, and each spoke VNet, can be implemented in different resource groups, and even different subscriptions, as long as they belong to the same Azure Active Directory (Azure AD) tenant in the same Azure region.</span></span> <span data-ttu-id="f5b06-154">如此可對每個工作負載進行非集中式管理，同時在中樞 VNet 中維護共用服務。</span><span class="sxs-lookup"><span data-stu-id="f5b06-154">This allows for a decentralized management of each workload, while sharing services maintained in the hub VNet.</span></span>

### <a name="vnet-and-gatewaysubnet"></a><span data-ttu-id="f5b06-155">VNet 和 GatewaySubnet</span><span class="sxs-lookup"><span data-stu-id="f5b06-155">VNet and GatewaySubnet</span></span>

<span data-ttu-id="f5b06-156">建立一個名為 *GatewaySubnet* 的子網路，且其位址範圍為 /27。</span><span class="sxs-lookup"><span data-stu-id="f5b06-156">Create a subnet named *GatewaySubnet*, with an address range of /27.</span></span> <span data-ttu-id="f5b06-157">虛擬網路閘道需要使用這個子網路。</span><span class="sxs-lookup"><span data-stu-id="f5b06-157">This subnet is required by the virtual network gateway.</span></span> <span data-ttu-id="f5b06-158">將 32 個位址配置給這個子網路，將有助於防止未來達到閘道大小限制。</span><span class="sxs-lookup"><span data-stu-id="f5b06-158">Allocating 32 addresses to this subnet will help to prevent reaching gateway size limitations in the future.</span></span>

<span data-ttu-id="f5b06-159">如需有關設定閘道的詳細資訊，請根據您的連線類型，參閱下列參考架構：</span><span class="sxs-lookup"><span data-stu-id="f5b06-159">For more information about setting up the gateway, see the following reference architectures, depending on your connection type:</span></span>

- <span data-ttu-id="f5b06-160">[使用 ExpressRoute 的混合式網路][guidance-expressroute]</span><span class="sxs-lookup"><span data-stu-id="f5b06-160">[Hybrid network using ExpressRoute][guidance-expressroute]</span></span>
- <span data-ttu-id="f5b06-161">[使用 VPN 閘道的混合式網路][guidance-vpn]</span><span class="sxs-lookup"><span data-stu-id="f5b06-161">[Hybrid network using a VPN gateway][guidance-vpn]</span></span>

<span data-ttu-id="f5b06-162">如需高可用性，您可以使用 ExpressRoute 加上 VPN 進行容錯移轉。</span><span class="sxs-lookup"><span data-stu-id="f5b06-162">For higher availability, you can use ExpressRoute plus a VPN for failover.</span></span> <span data-ttu-id="f5b06-163">請參閱[使用 ExpressRoute 搭配 VPN 容錯移轉，將內部部署網路連線至 Azure][hybrid-ha]。</span><span class="sxs-lookup"><span data-stu-id="f5b06-163">See [Connect an on-premises network to Azure using ExpressRoute with VPN failover][hybrid-ha].</span></span>

<span data-ttu-id="f5b06-164">如果您不需要與內部部署網路連線，則不需要閘道也可以使用中樞輪輻拓撲。</span><span class="sxs-lookup"><span data-stu-id="f5b06-164">A hub-spoke topology can also be used without a gateway, if you don't need connectivity with your on-premises network.</span></span> 

### <a name="vnet-peering"></a><span data-ttu-id="f5b06-165">VNet 對等互連</span><span class="sxs-lookup"><span data-stu-id="f5b06-165">VNet peering</span></span>

<span data-ttu-id="f5b06-166">VNet 對等互連是兩個 VNet 之間不可轉移的關聯性。</span><span class="sxs-lookup"><span data-stu-id="f5b06-166">VNet peering is a non-transitive relationship between two VNets.</span></span> <span data-ttu-id="f5b06-167">如果您需要輪輻彼此連線，請考慮在這些輪輻之間加入個別的對等連線。</span><span class="sxs-lookup"><span data-stu-id="f5b06-167">If you require spokes to connect to each other, consider adding a separate peering connection between those spokes.</span></span>

<span data-ttu-id="f5b06-168">不過，如果您有數個需要彼此連線的輪輻，將會因為[每個 VNet 的 VNet 對等互連數目限制][vnet-peering-limit]，而非常快速地用盡可能的對等連線。</span><span class="sxs-lookup"><span data-stu-id="f5b06-168">However, if you have several spokes that need to connect with each other, you will run out of possible peering connections very quickly due to the [limitation on number of VNets peerings per VNet][vnet-peering-limit].</span></span> <span data-ttu-id="f5b06-169">在此案例中，請考慮使用使用者定義的路由 (UDR)，強制將目的地為輪輻的流量傳送到當作中樞 VNet 路由器的 NVA。</span><span class="sxs-lookup"><span data-stu-id="f5b06-169">In this scenario, consider using user defined routes (UDRs) to force traffic destined to a spoke to be sent to an NVA acting as a router at the hub VNet.</span></span> <span data-ttu-id="f5b06-170">這可讓輪輻彼此連線。</span><span class="sxs-lookup"><span data-stu-id="f5b06-170">This will allow the spokes to connect to each other.</span></span>

<span data-ttu-id="f5b06-171">您也可以將輪輻設定為使用中樞 VNet 閘道，與遠端網路進行通訊。</span><span class="sxs-lookup"><span data-stu-id="f5b06-171">You can also configure spokes to use the hub VNet gateway to communicate with remote networks.</span></span> <span data-ttu-id="f5b06-172">若要允許閘道流量從輪輻流向中樞，然後連線至遠端網路，您必須：</span><span class="sxs-lookup"><span data-stu-id="f5b06-172">To allow gateway traffic to flow from spoke to hub, and connect to remote networks, you must:</span></span>

  - <span data-ttu-id="f5b06-173">將中樞中的 VNet 對等連線設定為**允許閘道傳輸**。</span><span class="sxs-lookup"><span data-stu-id="f5b06-173">Configure the VNet peering connection in the hub to **allow gateway transit**.</span></span>
  - <span data-ttu-id="f5b06-174">將每個輪輻中的 VNet 對等連線設定為**使用遠端閘道**。</span><span class="sxs-lookup"><span data-stu-id="f5b06-174">Configure the VNet peering connection in each spoke to **use remote gateways**.</span></span>
  - <span data-ttu-id="f5b06-175">將所有 VNet 對等連線設定為**允許轉送的流量**。</span><span class="sxs-lookup"><span data-stu-id="f5b06-175">Configure all VNet peering connections to **allow forwarded traffic**.</span></span>

## <a name="considerations"></a><span data-ttu-id="f5b06-176">考量</span><span class="sxs-lookup"><span data-stu-id="f5b06-176">Considerations</span></span>

### <a name="spoke-connectivity"></a><span data-ttu-id="f5b06-177">輪輻連線能力</span><span class="sxs-lookup"><span data-stu-id="f5b06-177">Spoke connectivity</span></span>

<span data-ttu-id="f5b06-178">如果您需要在輪輻之間連線，請考慮實作 NVA 以便在中樞路由傳送，並在輪輻中使用 UDR，將流量轉送到中樞。</span><span class="sxs-lookup"><span data-stu-id="f5b06-178">If you require connectivity between spokes, consider implementing an NVA for routing in the hub, and using UDRs in the spoke to forward traffic to the hub.</span></span>

<span data-ttu-id="f5b06-179">![[2]][2]</span><span class="sxs-lookup"><span data-stu-id="f5b06-179">![[2]][2]</span></span>

<span data-ttu-id="f5b06-180">在此案例中，您必須將對等連線設定為**允許轉送的流量**。</span><span class="sxs-lookup"><span data-stu-id="f5b06-180">In this scenario, you must configure the peering connections to **allow forwarded traffic**.</span></span>

### <a name="overcoming-vnet-peering-limits"></a><span data-ttu-id="f5b06-181">克服 VNet 對等互連的限制</span><span class="sxs-lookup"><span data-stu-id="f5b06-181">Overcoming VNet peering limits</span></span>

<span data-ttu-id="f5b06-182">請確定您有在 Azure 中考慮[每個 VNet 的 VNet 對等互連數目限制][vnet-peering-limit]。</span><span class="sxs-lookup"><span data-stu-id="f5b06-182">Make sure you consider the [limitation on number of VNets peerings per VNet][vnet-peering-limit] in Azure.</span></span> <span data-ttu-id="f5b06-183">如果您決定您需要的輪輻比允許的限制還多，請考慮建立中樞輪輻中樞輪輻拓撲，其中輪輻的第一層也可作為中樞。</span><span class="sxs-lookup"><span data-stu-id="f5b06-183">If you decide you need more spokes than the limit will allow, consider creating a hub-spoke-hub-spoke topology, where the first level of spokes also act as hubs.</span></span> <span data-ttu-id="f5b06-184">下圖顯示這個方法。</span><span class="sxs-lookup"><span data-stu-id="f5b06-184">The following diagram shows this approach.</span></span>

<span data-ttu-id="f5b06-185">![[3]][3]</span><span class="sxs-lookup"><span data-stu-id="f5b06-185">![[3]][3]</span></span>

<span data-ttu-id="f5b06-186">此外，請考慮要在中樞共用哪些服務，以確保中樞可針對大量輪輻進行調整。</span><span class="sxs-lookup"><span data-stu-id="f5b06-186">Also consider what services are shared in the hub, to ensure the hub scales for a larger number of spokes.</span></span> <span data-ttu-id="f5b06-187">例如，如果您的中樞提供防火牆服務，請在新增多個輪輻時，考慮防火牆解決方案的頻寬限制。</span><span class="sxs-lookup"><span data-stu-id="f5b06-187">For instance, if your hub provides firewall services, consider the bandwidth limits of your firewall solution when adding multiple spokes.</span></span> <span data-ttu-id="f5b06-188">您可以將其中部分共用服務移到中樞的第二層。</span><span class="sxs-lookup"><span data-stu-id="f5b06-188">You might want to move some of these shared services to a second level of hubs.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="f5b06-189">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="f5b06-189">Deploy the solution</span></span>

<span data-ttu-id="f5b06-190">適用於此架構的部署可在 [GitHub][ref-arch-repo] 上取得。</span><span class="sxs-lookup"><span data-stu-id="f5b06-190">A deployment for this architecture is available on [GitHub][ref-arch-repo].</span></span> <span data-ttu-id="f5b06-191">它會使用每個 VNet 中的 Ubuntu VM 測試連線。</span><span class="sxs-lookup"><span data-stu-id="f5b06-191">It uses Ubuntu VMs in each VNet to test connectivity.</span></span> <span data-ttu-id="f5b06-192">**中樞 VNet** 的**共用服務**子網路中沒有裝載任何實際的服務。</span><span class="sxs-lookup"><span data-stu-id="f5b06-192">There are no actual services hosted in the **shared-services** subnet in the **hub VNet**.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="f5b06-193">先決條件</span><span class="sxs-lookup"><span data-stu-id="f5b06-193">Prerequisites</span></span>

<span data-ttu-id="f5b06-194">在您可以將參考架構部署到自己的訂用帳戶之前，必須執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="f5b06-194">Before you can deploy the reference architecture to your own subscription, you must perform the following steps.</span></span>

1. <span data-ttu-id="f5b06-195">複製、派生或下載適用於 [AzureCAT 參考架構][ref-arch-repo] GitHub 存放庫的 zip 檔案。</span><span class="sxs-lookup"><span data-stu-id="f5b06-195">Clone, fork, or download the zip file for the [AzureCAT reference architectures][ref-arch-repo] GitHub repository.</span></span>

2. <span data-ttu-id="f5b06-196">確定您已在電腦上安裝 Azure CLI 2.0。</span><span class="sxs-lookup"><span data-stu-id="f5b06-196">Make sure you have the Azure CLI 2.0 installed on your computer.</span></span> <span data-ttu-id="f5b06-197">如需 CLI 安裝指示，請參閱[安裝 Azure CLI 2.0][azure-cli-2]。</span><span class="sxs-lookup"><span data-stu-id="f5b06-197">For CLI installation instructions, see [Install Azure CLI 2.0][azure-cli-2].</span></span>

3. <span data-ttu-id="f5b06-198">安裝 [Azure 建置組塊][azbb] npm 套件。</span><span class="sxs-lookup"><span data-stu-id="f5b06-198">Install the [Azure buulding blocks][azbb] npm package.</span></span>

4. <span data-ttu-id="f5b06-199">從命令提示字元、bash 提示字元或 PowerShell 提示字元中，使用下列命令登入 Azure 帳戶，並遵循提示進行。</span><span class="sxs-lookup"><span data-stu-id="f5b06-199">From a command prompt, bash prompt, or PowerShell prompt, login to your Azure account by using the command below, and follow the prompts.</span></span>

  ```bash
  az login
  ```

### <a name="deploy-the-simulated-on-premises-datacenter-using-azbb"></a><span data-ttu-id="f5b06-200">使用 azbb 部署模擬的內部部署資料中心</span><span class="sxs-lookup"><span data-stu-id="f5b06-200">Deploy the simulated on-premises datacenter using azbb</span></span>

<span data-ttu-id="f5b06-201">若要將模擬的內部部署資料中心部署為 Azure VNet，請遵循下列步驟：</span><span class="sxs-lookup"><span data-stu-id="f5b06-201">To deploy the simulated on-premises datacenter as an Azure VNet, follow these steps:</span></span>

1. <span data-ttu-id="f5b06-202">瀏覽至您在上述必要條件步驟中所下載存放庫的 `hybrid-networking\hub-spoke\` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="f5b06-202">Navigate to the `hybrid-networking\hub-spoke\` folder for the repository you downloaded in the pre-requisites step above.</span></span>

2. <span data-ttu-id="f5b06-203">開啟 `onprem.json` 檔案，然後在第 36 和 37 行的引號之間輸入使用者名稱和密碼，如下所示，然後儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="f5b06-203">Open the `onprem.json` file and enter a username and password between the quotes in line 36 and 37, as shown below, then save the file.</span></span>

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```

3. <span data-ttu-id="f5b06-204">在第 38 行上，針對 `osType` 輸入 `Windows` 或 `Linux`，以安裝 Windows Server 2016 Datacenter 或 Ubuntu 16.04 作為 jumpbox 的作業系統。</span><span class="sxs-lookup"><span data-stu-id="f5b06-204">On line 38, for `osType`, type `Windows` or `Linux` to install either Windows Server 2016 Datacenter, or Ubuntu 16.04 as the operating system for the jumpbox.</span></span>

4. <span data-ttu-id="f5b06-205">執行 `azbb` 以部署模擬的內部部署環境，如下所示。</span><span class="sxs-lookup"><span data-stu-id="f5b06-205">Run `azbb` to deploy the simulated onprem environment as shown below.</span></span>

  ```bash
  azbb -s <subscription_id> -g onprem-vnet-rg - l <location> -p onoprem.json --deploy
  ```
  > [!NOTE]
  > <span data-ttu-id="f5b06-206">如果您決定使用不同的資源群組名稱 (非 `onprem-vnet-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。</span><span class="sxs-lookup"><span data-stu-id="f5b06-206">If you decide to use a different resource group name (other than `onprem-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

5. <span data-ttu-id="f5b06-207">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="f5b06-207">Wait for the deployment to finish.</span></span> <span data-ttu-id="f5b06-208">此部署會建立一個虛擬網路、一個虛擬機器，以及一個 VPN 閘道。</span><span class="sxs-lookup"><span data-stu-id="f5b06-208">This deployment creates a virtual network, a virtual machine, and a VPN gateway.</span></span> <span data-ttu-id="f5b06-209">建立 VPN 閘道可能需要超過 40 分鐘才能完成。</span><span class="sxs-lookup"><span data-stu-id="f5b06-209">The VPN gateway creation can take more than 40 minutes to complete.</span></span>

### <a name="azure-hub-vnet"></a><span data-ttu-id="f5b06-210">Azure 中樞 VNet</span><span class="sxs-lookup"><span data-stu-id="f5b06-210">Azure hub VNet</span></span>

<span data-ttu-id="f5b06-211">若要部署中樞 VNet 並連線到以上所建立的模擬內部部署 VNet，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="f5b06-211">To deploy the hub VNet, and connect to the simulated on-premises VNet created above, perform the following steps.</span></span>

1. <span data-ttu-id="f5b06-212">開啟 `hub-vnet.json` 檔案，然後在第 39 和 40 行的引號之間輸入使用者名稱和密碼，如下所示。</span><span class="sxs-lookup"><span data-stu-id="f5b06-212">Open the `hub-vnet.json` file and enter a username and password between the quotes in line 39 and 40, as shown below.</span></span>

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```

2. <span data-ttu-id="f5b06-213">在第 41 行上，針對 `osType` 輸入 `Windows` 或 `Linux` 以安裝 Windows Server 2016 Datacenter 或 Ubuntu 16.04 作為 jumpbox 的作業系統。</span><span class="sxs-lookup"><span data-stu-id="f5b06-213">On line 41, for `osType`, type `Windows` or `Linux` to install either Windows Server 2016 Datacenter, or Ubuntu 16.04 as the operating system for the jumpbox.</span></span>

3. <span data-ttu-id="f5b06-214">在第 72 行的引號之間輸入共用金鑰，如下所示，然後儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="f5b06-214">Enter a shared key between the quotes in line 72, as shown below, then save the file.</span></span>

  ```bash
  "sharedKey": "",
  ```

4. <span data-ttu-id="f5b06-215">執行 `azbb` 以部署模擬的內部部署環境，如下所示。</span><span class="sxs-lookup"><span data-stu-id="f5b06-215">Run `azbb` to deploy the simulated onprem environment as shown below.</span></span>

  ```bash
  azbb -s <subscription_id> -g hub-vnet-rg - l <location> -p hub-vnet.json --deploy
  ```
  > [!NOTE]
  > <span data-ttu-id="f5b06-216">如果您決定使用不同的資源群組名稱 (非 `hub-vnet-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。</span><span class="sxs-lookup"><span data-stu-id="f5b06-216">If you decide to use a different resource group name (other than `hub-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

5. <span data-ttu-id="f5b06-217">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="f5b06-217">Wait for the deployment to finish.</span></span> <span data-ttu-id="f5b06-218">此部署會建立一個虛擬網路、一個虛擬機器、一個 VPN 閘道，以及一個與上節中建立之閘道的連線。</span><span class="sxs-lookup"><span data-stu-id="f5b06-218">This deployment creates a virtual network, a virtual machine, a VPN gateway, and a connection to the gateway created in the previous section.</span></span> <span data-ttu-id="f5b06-219">建立 VPN 閘道可能需要超過 40 分鐘才能完成。</span><span class="sxs-lookup"><span data-stu-id="f5b06-219">The VPN gateway creation can take more than 40 minutes to complete.</span></span>

### <a name="optional-test-connectivity-from-onprem-to-hub"></a><span data-ttu-id="f5b06-220">(選擇性) 測試從內部部署到中樞的連線</span><span class="sxs-lookup"><span data-stu-id="f5b06-220">(Optional) Test connectivity from onprem to hub</span></span>

<span data-ttu-id="f5b06-221">若要測試從模擬的內部部署環境到使用 Windows VM 之中樞 VNet 的連線，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="f5b06-221">To test conectivity from the simulated on-premises environment to the hub VNet using Windows VMs, perform the following steps.</span></span>

1. <span data-ttu-id="f5b06-222">從 Azure 入口網站中，瀏覽至 `onprem-jb-rg` 資源群組，然後按一下 `jb-vm1` 虛擬機器資源。</span><span class="sxs-lookup"><span data-stu-id="f5b06-222">From the Azure portal, navigate to the `onprem-jb-rg` resource group, then click on the `jb-vm1` virtual machine resource.</span></span>

2.  <span data-ttu-id="f5b06-223">在入口網站中虛擬機器刀鋒視窗的左上角，按一下 `Connect`，並遵循提示來使用遠端桌面連線至 VM。</span><span class="sxs-lookup"><span data-stu-id="f5b06-223">On the top left hand corner of your VM blade in the portal, click `Connect`, and follow the prompts to use remote desktop to connect to the VM.</span></span> <span data-ttu-id="f5b06-224">請務必使用您在 `onprem.json` 檔案 36 和 37 行中指定的使用者名稱和密碼。</span><span class="sxs-lookup"><span data-stu-id="f5b06-224">Make sure to use the username and password you specified in lines 36 and 37 in the `onprem.json` file.</span></span>

3. <span data-ttu-id="f5b06-225">在 VM 中開啟 PowerShell 主控台，並使用 `Test-NetConnection` Cmdlet 來確認您可以連線至中樞 jumpbox VM，如下所示。</span><span class="sxs-lookup"><span data-stu-id="f5b06-225">Open a PowerShell console in the VM, and use the `Test-NetConnection` cmdlet to verify that you can connect to the hub jumpbox VM as shown below.</span></span>

  ```powershell
  Test-NetConnection 10.0.0.68 -CommonTCPPort RDP
  ```
  > [!NOTE]
  > <span data-ttu-id="f5b06-226">根據預設，Windows Server VM 在 Azure 中不允許 ICMP 回應。</span><span class="sxs-lookup"><span data-stu-id="f5b06-226">By default, Windows Server VMs do not allow ICMP responses in Azure.</span></span> <span data-ttu-id="f5b06-227">如果您想要使用 `ping` 來測試連線，您需要在 Windows 進階防火牆中為每個 VM 啟用 ICMP 流量。</span><span class="sxs-lookup"><span data-stu-id="f5b06-227">If you want to use `ping` to test connectivity, you need to enable ICMP traffic in the Windows Advanced Firewall for each VM.</span></span>

<span data-ttu-id="f5b06-228">若要測試從模擬的內部部署環境到使用 Linux VM 之中樞 VNet 的連線，請執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="f5b06-228">To test conectivity from the simulated on-premises environment to the hub VNet using Linux VMs, perform the following steps:</span></span>

1. <span data-ttu-id="f5b06-229">從 Azure 入口網站中，瀏覽至 `onprem-jb-rg` 資源群組，然後按一下 `jb-vm1` 虛擬機器資源。</span><span class="sxs-lookup"><span data-stu-id="f5b06-229">From the Azure portal, navigate to the `onprem-jb-rg` resource group, then click on the `jb-vm1` virtual machine resource.</span></span>

2. <span data-ttu-id="f5b06-230">在入口網站 VM 刀鋒視窗的左上角，按一下 `Connect`，然後複製入口網站上顯示的 `ssh` 命令。</span><span class="sxs-lookup"><span data-stu-id="f5b06-230">On the top left hand corner of your VM blade in the portal, click `Connect`, and then copy the `ssh` command shown on the portal.</span></span> 

3. <span data-ttu-id="f5b06-231">從 Linux 提示中執行 `ssh`，使用您在上述步驟 2 中複製的資訊，連線到模擬的內部部署環境 jumpbox，如下所示。</span><span class="sxs-lookup"><span data-stu-id="f5b06-231">From a Linux prompt, run `ssh` to connect to the simulated on-premises environment jumpbox witht the information you copied in step 2 above, as shown below.</span></span>

  ```bash
  ssh <your_user>@<public_ip_address>
  ```

4. <span data-ttu-id="f5b06-232">使用您在 `onprem.json` 檔案 37 行中指定的密碼連線到 VM。</span><span class="sxs-lookup"><span data-stu-id="f5b06-232">Use the password you specified in line 37 in the `onprem.json` file to the connect to the VM.</span></span>

5. <span data-ttu-id="f5b06-233">使用 `ping` 命令來測試與中樞 jumpbox 的連線，如下所示。</span><span class="sxs-lookup"><span data-stu-id="f5b06-233">Use the `ping` command to test connectivity to the hub jumpbox, as shown below.</span></span>

  ```bash
  ping 10.0.0.68
  ```

### <a name="azure-spoke-vnets"></a><span data-ttu-id="f5b06-234">Azure 輪輻 VNet</span><span class="sxs-lookup"><span data-stu-id="f5b06-234">Azure spoke VNets</span></span>

<span data-ttu-id="f5b06-235">若要部署輪輻 VNet，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="f5b06-235">To deploy the spoke VNets, perform the following steps.</span></span>

1. <span data-ttu-id="f5b06-236">開啟 `spoke1.json` 檔案，然後在第 47 和 48 行的引號之間輸入使用者名稱和密碼，如下所示，然後儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="f5b06-236">Open the `spoke1.json` file and enter a username and password between the quotes in lines 47 and 48, as shown below, then save the file.</span></span>

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```

2. <span data-ttu-id="f5b06-237">在第 49 行上，針對 `osType` 輸入 `Windows` 或 `Linux` 以安裝 Windows Server 2016 Datacenter 或 Ubuntu 16.04 作為 jumpbox 的作業系統。</span><span class="sxs-lookup"><span data-stu-id="f5b06-237">On line 49, for `osType`, type `Windows` or `Linux` to install either Windows Server 2016 Datacenter, or Ubuntu 16.04 as the operating system for the jumpbox.</span></span>

3. <span data-ttu-id="f5b06-238">執行 `azbb` 以部署第一個輪輻 VNet 環境，如下所示。</span><span class="sxs-lookup"><span data-stu-id="f5b06-238">Run `azbb` to deploy the first spoke VNet environment as shown below.</span></span>

  ```bash
  azbb -s <subscription_id> -g spoke1-vnet-rg - l <location> -p spoke1.json --deploy
  ```
  
  > [!NOTE]
  > <span data-ttu-id="f5b06-239">如果您決定使用不同的資源群組名稱 (非 `spoke1-vnet-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。</span><span class="sxs-lookup"><span data-stu-id="f5b06-239">If you decide to use a different resource group name (other than `spoke1-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

3. <span data-ttu-id="f5b06-240">對 `spoke2.json` 檔案重複上述的步驟 1。</span><span class="sxs-lookup"><span data-stu-id="f5b06-240">Repeat step 1 above for file `spoke2.json`.</span></span>

4. <span data-ttu-id="f5b06-241">執行 `azbb` 以部署第二個輪輻 VNet 環境，如下所示。</span><span class="sxs-lookup"><span data-stu-id="f5b06-241">Run `azbb` to deploy the second spoke VNet environment as shown below.</span></span>

  ```bash
  azbb -s <subscription_id> -g spoke2-vnet-rg - l <location> -p spoke2.json --deploy
  ```
  > [!NOTE]
  > <span data-ttu-id="f5b06-242">如果您決定使用不同的資源群組名稱 (非 `spoke2-vnet-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。</span><span class="sxs-lookup"><span data-stu-id="f5b06-242">If you decide to use a different resource group name (other than `spoke2-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

### <a name="azure-hub-vnet-peering-to-spoke-vnets"></a><span data-ttu-id="f5b06-243">Azure 中樞 VNet 對等互連至輪輻 VNet</span><span class="sxs-lookup"><span data-stu-id="f5b06-243">Azure hub VNet peering to spoke VNets</span></span>

<span data-ttu-id="f5b06-244">若要建立從中樞 VNet 到輪輻 VNet 的對等互連連線，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="f5b06-244">To create a peering connection from the hub VNet to the spoke VNets, perform the following steps.</span></span>

1. <span data-ttu-id="f5b06-245">開啟 `hub-vnet-peering.json` 檔案並確認從 29 行開始之每個虛擬網路對等互連的資源群組名稱和虛擬網路名稱正確無誤。</span><span class="sxs-lookup"><span data-stu-id="f5b06-245">Open the `hub-vnet-peering.json` file and verify that the resource group name, and virtual network name for each of the virtual network peerings starting in line 29 are correct.</span></span>

2. <span data-ttu-id="f5b06-246">執行 `azbb` 以部署第一個輪輻 VNet 環境，如下所示。</span><span class="sxs-lookup"><span data-stu-id="f5b06-246">Run `azbb` to deploy the first spoke VNet environment as shown below.</span></span>

  ```bash
  azbb -s <subscription_id> -g hub-vnet-rg - l <location> -p hub-vnet-peering.json --deploy
  ```

  > [!NOTE]
  > <span data-ttu-id="f5b06-247">如果您決定使用不同的資源群組名稱 (非 `hub-vnet-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。</span><span class="sxs-lookup"><span data-stu-id="f5b06-247">If you decide to use a different resource group name (other than `hub-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

### <a name="test-connectivity"></a><span data-ttu-id="f5b06-248">測試連線能力</span><span class="sxs-lookup"><span data-stu-id="f5b06-248">Test connectivity</span></span>

<span data-ttu-id="f5b06-249">若要測試從模擬的內部部署環境到使用 Windows VM 之輪輻 VNet 的連線，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="f5b06-249">To test conectivity from the simulated on-premises environment to the spoke VNets using Windows VMs, perform the following steps.</span></span>

1. <span data-ttu-id="f5b06-250">從 Azure 入口網站中，瀏覽至 `onprem-jb-rg` 資源群組，然後按一下 `jb-vm1` 虛擬機器資源。</span><span class="sxs-lookup"><span data-stu-id="f5b06-250">From the Azure portal, navigate to the `onprem-jb-rg` resource group, then click on the `jb-vm1` virtual machine resource.</span></span>

2.  <span data-ttu-id="f5b06-251">在入口網站中虛擬機器刀鋒視窗的左上角，按一下 `Connect`，並遵循提示來使用遠端桌面連線至 VM。</span><span class="sxs-lookup"><span data-stu-id="f5b06-251">On the top left hand corner of your VM blade in the portal, click `Connect`, and follow the prompts to use remote desktop to connect to the VM.</span></span> <span data-ttu-id="f5b06-252">請務必使用您在 `onprem.json` 檔案 36 和 37 行中指定的使用者名稱和密碼。</span><span class="sxs-lookup"><span data-stu-id="f5b06-252">Make sure to use the username and password you specified in lines 36 and 37 in the `onprem.json` file.</span></span>

3. <span data-ttu-id="f5b06-253">在 VM 中開啟 PowerShell 主控台，並使用 `Test-NetConnection` Cmdlet 來確認您可以連線至中樞 jumpbox VM，如下所示。</span><span class="sxs-lookup"><span data-stu-id="f5b06-253">Open a PowerShell console in the VM, and use the `Test-NetConnection` cmdlet to verify that you can connect to the hub jumpbox VM as shown below.</span></span>

  ```powershell
  Test-NetConnection 10.1.0.68 -CommonTCPPort RDP
  Test-NetConnection 10.2.0.68 -CommonTCPPort RDP
  ```

<span data-ttu-id="f5b06-254">若要測試從模擬的內部部署環境到使用 Linux VM 之輪輻 VNet 的連線，請執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="f5b06-254">To test conectivity from the simulated on-premises environment to the spoke VNets using Linux VMs, perform the following steps:</span></span>

1. <span data-ttu-id="f5b06-255">從 Azure 入口網站中，瀏覽至 `onprem-jb-rg` 資源群組，然後按一下 `jb-vm1` 虛擬機器資源。</span><span class="sxs-lookup"><span data-stu-id="f5b06-255">From the Azure portal, navigate to the `onprem-jb-rg` resource group, then click on the `jb-vm1` virtual machine resource.</span></span>

2. <span data-ttu-id="f5b06-256">在入口網站 VM 刀鋒視窗的左上角，按一下 `Connect`，然後複製入口網站上顯示的 `ssh` 命令。</span><span class="sxs-lookup"><span data-stu-id="f5b06-256">On the top left hand corner of your VM blade in the portal, click `Connect`, and then copy the `ssh` command shown on the portal.</span></span> 

3. <span data-ttu-id="f5b06-257">從 Linux 提示中執行 `ssh`，使用您在上述步驟 2 中複製的資訊，連線到模擬的內部部署環境 jumpbox，如下所示。</span><span class="sxs-lookup"><span data-stu-id="f5b06-257">From a Linux prompt, run `ssh` to connect to the simulated on-premises environment jumpbox witht the information you copied in step 2 above, as shown below.</span></span>

  ```bash
  ssh <your_user>@<public_ip_address>
  ```

5. <span data-ttu-id="f5b06-258">使用您在 `onprem.json` 檔案 37 行中指定的密碼連線到 VM。</span><span class="sxs-lookup"><span data-stu-id="f5b06-258">Use the password you specified in line 37 in the `onprem.json` file to the connect to the VM.</span></span>

6. <span data-ttu-id="f5b06-259">使用 `ping` 命令來測試與每個輪輻中 jumpbox VM 的連線，如下所示。</span><span class="sxs-lookup"><span data-stu-id="f5b06-259">Use the `ping` command to test connectivity to the jumpbox VMs in each spoke, as shown below.</span></span>

  ```bash
  ping 10.1.0.68
  ping 10.2.0.68
  ```

### <a name="add-connectivity-between-spokes"></a><span data-ttu-id="f5b06-260">新增輪輻之間的連線</span><span class="sxs-lookup"><span data-stu-id="f5b06-260">Add connectivity between spokes</span></span>

<span data-ttu-id="f5b06-261">如果您想要允許輪輻彼此連線，您必須使用網路虛擬設備 (NVA) 作為中樞虛擬網路的路由器，並且在嘗試連線到另一個輪輻時強制執行從輪輻到路由器的流量。</span><span class="sxs-lookup"><span data-stu-id="f5b06-261">If you want to allow spokes to connect to each other, you need to use a newtwork virtual appliance (NVA) as a router in the hub virtual netowrk, and force traffic from spokes to the router when trying to connect to another spoke.</span></span> <span data-ttu-id="f5b06-262">若要部署基本範例 NVA 作為單一 VM，且需要使用者定義的路由器允許兩個輪輻 Vnet 連線，請執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="f5b06-262">To deploy a basic sample NVA as a single VM, and the necessary uder defined routes to allow the two spoke VNets to connect, perform the following steps:</span></span>

1. <span data-ttu-id="f5b06-263">開啟 `hub-nva.json` 檔案，然後在第 13 和 14 行的引號之間輸入使用者名稱和密碼，如下所示，然後儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="f5b06-263">Open the `hub-nva.json` file and enter a username and password between the quotes in lines 13 and 14, as shown below, then save the file.</span></span>

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```
2. <span data-ttu-id="f5b06-264">執行 `azbb` 以部署 NVA VM 和使用者定義的路由。</span><span class="sxs-lookup"><span data-stu-id="f5b06-264">Run `azbb` to deploy the NVA VM and user defined routes.</span></span>

  ```bash
  azbb -s <subscription_id> -g hub-nva-rg - l <location> -p hub-nva.json --deploy
  ```
  > [!NOTE]
  > <span data-ttu-id="f5b06-265">如果您決定使用不同的資源群組名稱 (非 `hub-nva-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。</span><span class="sxs-lookup"><span data-stu-id="f5b06-265">If you decide to use a different resource group name (other than `hub-nva-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

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
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[windows-vm-ra]: ../virtual-machines-windows/index.md

[visio-download]: https://archcenter.azureedge.net/cdn/hybrid-network-hub-spoke.vsdx
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[0]: ./images/hub-spoke.png "Azure 中的中樞輪輻拓撲"
[1]: ./images/hub-spoke-gateway-routing.svg "Azure 中具有可轉移路由的中樞輪輻拓撲"
[2]: ./images/hub-spoke-no-gateway-routing.svg "Azure 中具有使用 NVA 之可轉移路由的中樞輪輻拓撲"
[3]: ./images/hub-spokehub-spoke.svg "Azure 中的中樞輪輻中樞輪輻拓撲"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/

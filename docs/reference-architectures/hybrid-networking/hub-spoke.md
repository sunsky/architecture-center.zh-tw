---
title: "在 Azure 中實作中樞輪輻網路拓撲"
description: "如何在 Azure 中實作中樞輪輻網路拓撲。"
author: telmosampaio
ms.date: 05/05/2017
pnp.series.title: Implement a hub-spoke network topology in Azure
pnp.series.prev: expressroute
ms.openlocfilehash: e6f07a7962dd5728226b023700268340590d97a3
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="implement-a-hub-spoke-network-topology-in-azure"></a><span data-ttu-id="bd1a7-103">在 Azure 中實作中樞輪輻網路拓撲</span><span class="sxs-lookup"><span data-stu-id="bd1a7-103">Implement a hub-spoke network topology in Azure</span></span>

<span data-ttu-id="bd1a7-104">此參考架構會顯示如何在 Azure 中實作中樞輪輻拓撲。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-104">This reference architecture shows how to implement a hub-spoke topology in Azure.</span></span> <span data-ttu-id="bd1a7-105">「中樞」是 Azure 中的虛擬網路 (VNet)，可當作內部部署網路的連線中心點。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-105">The *hub* is a virtual network (VNet) in Azure that acts as a central point of connectivity to your on-premises network.</span></span> <span data-ttu-id="bd1a7-106">「輪輻」是與中樞對等互連的 VNet，可用於隔離工作負載。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-106">The *spokes* are VNets that peer with the hub, and can be used to isolate workloads.</span></span> <span data-ttu-id="bd1a7-107">流量會透過 ExpressRoute 或 VPN 閘道連線，在內部部署資料中心和中樞之間流動。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-107">Traffic flows between the on-premises datacenter and the hub through an ExpressRoute or VPN gateway connection.</span></span>  <span data-ttu-id="bd1a7-108">[**部署這個解決方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-108">[**Deploy this solution**](#deploy-the-solution).</span></span>

<span data-ttu-id="bd1a7-109">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="bd1a7-109">![[0]][0]</span></span>

<span data-ttu-id="bd1a7-110">*下載這個架構的 [Visio 檔案][visio-download]*</span><span class="sxs-lookup"><span data-stu-id="bd1a7-110">*Download a [Visio file][visio-download] of this architecture*</span></span>


<span data-ttu-id="bd1a7-111">此拓撲的優點包括：</span><span class="sxs-lookup"><span data-stu-id="bd1a7-111">The benefits of this toplogy include:</span></span>

* <span data-ttu-id="bd1a7-112">**節省成本**：將可由多個工作負載共用的服務 (例如網路虛擬裝置 (NVA) 和 DNS 伺服器) 集中在單一位置。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-112">**Cost savings** by centralizing services that can be shared by multiple workloads, such as network virtual appliances (NVAs) and DNS servers, in a single location.</span></span>
* <span data-ttu-id="bd1a7-113">**克服訂用帳戶限制**：將不同訂用帳戶中的 VNet 對等互連到中心中樞。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-113">**Overcome subscriptions limits** by peering VNets from different subscriptions to the central hub.</span></span>
* <span data-ttu-id="bd1a7-114">**關注點分離**：中央 IT (SecOps、InfraOps) 和工作負載 (DevOps)。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-114">**Separation of concerns** between central IT (SecOps, InfraOps) and workloads (DevOps).</span></span>

<span data-ttu-id="bd1a7-115">此架構的一般使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="bd1a7-115">Typical uses for this architecture include:</span></span>

* <span data-ttu-id="bd1a7-116">在不同環境 (例如開發、測試和生產環境) 下部署，且需要共用服務 (例如 DNS、IDS、NTP 或 AD DS) 的工作負載。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-116">Workloads deployed in different environments, such as development, testing, and production, that require shared services such as DNS, IDS, NTP, or AD DS.</span></span> <span data-ttu-id="bd1a7-117">系統會將共用服務置於中樞 VNet，同時將每個環境部署至輪輻以維持隔離。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-117">Shared services are placed in the hub VNet, while each environment is deployed to a spoke to maintain isolation.</span></span>
* <span data-ttu-id="bd1a7-118">不需要彼此連線，但需要存取共用服務的工作負載。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-118">Workloads that do not require connectivity to each other, but require access to shared services.</span></span>
* <span data-ttu-id="bd1a7-119">需要集中控制安全性層面 (例如，在中樞當作 DMZ 的防火牆)，並對每個輪輻中的工作負載進行分離管理的企業。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-119">Enterprises that require central control over security aspects, such as a firewall in the hub as a DMZ, and segregated management for the workloads in each spoke.</span></span>

## <a name="architecture"></a><span data-ttu-id="bd1a7-120">架構</span><span class="sxs-lookup"><span data-stu-id="bd1a7-120">Architecture</span></span>

<span data-ttu-id="bd1a7-121">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-121">The architecture consists of the following components.</span></span>

* <span data-ttu-id="bd1a7-122">**內部部署網路**。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-122">**On-premises network**.</span></span> <span data-ttu-id="bd1a7-123">在組織內執行的私用區域網路。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-123">A private local-area network running within an organization.</span></span>

* <span data-ttu-id="bd1a7-124">**VPN 裝置**。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-124">**VPN device**.</span></span> <span data-ttu-id="bd1a7-125">可對內部部署網路提供外部連線的裝置或服務。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-125">A device or service that provides external connectivity to the on-premises network.</span></span> <span data-ttu-id="bd1a7-126">VPN 裝置可能是硬體裝置或軟體解決方案，例如，Windows Server 2012 中的路由及遠端存取服務 (RRAS)。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-126">The VPN device may be a hardware device, or a software solution such as the Routing and Remote Access Service (RRAS) in Windows Server 2012.</span></span> <span data-ttu-id="bd1a7-127">如需支援的 VPN 設備清單，以及設定選取的 VPN 設備以連線至 Azure 的相關資訊，請參閱[關於站對站 VPN 閘道連線的 VPN 裝置][vpn-appliance]。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-127">For a list of supported VPN appliances and information on configuring selected VPN appliances for connecting to Azure, see [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance].</span></span>

* <span data-ttu-id="bd1a7-128">**VPN 虛擬網路閘道或 ExpressRoute 閘道**。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-128">**VPN virtual network gateway or ExpressRoute gateway**.</span></span> <span data-ttu-id="bd1a7-129">虛擬網路閘道可讓 VNet 連線到與內部部署網路連線所使用的 VPN 裝置或 ExpressRoute 線路。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-129">The virtual network gateway enables the VNet to connect to the VPN device, or ExpressRoute circuit, used for connectivity with your on-premises network.</span></span> <span data-ttu-id="bd1a7-130">如需詳細資訊，請參閱[將內部部署網路連線至 Microsoft Azure 虛擬網路][connect-to-an-Azure-vnet]。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-130">For more information, see [Connect an on-premises network to a Microsoft Azure virtual network][connect-to-an-Azure-vnet].</span></span>

> [!NOTE]
> <span data-ttu-id="bd1a7-131">此參考架構的部署指令碼會使用 VPN 閘道進行連線，並使用 Azure 中的 VNet 模擬內部部署網路。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-131">The deployment scripts for this reference architecture use a VPN gateway for connectivity, and a VNet in Azure to simulate your on-premises network.</span></span>

* <span data-ttu-id="bd1a7-132">**中樞 VNet**。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-132">**Hub VNet**.</span></span> <span data-ttu-id="bd1a7-133">在中樞輪輻拓撲當作中樞使用的 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-133">Azure VNet used as the hub in the hub-spoke topology.</span></span> <span data-ttu-id="bd1a7-134">中樞是內部部署網路的連線中心點，也是裝載可由輪輻 VNet 中裝載的不同工作負載使用之服務的位置。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-134">The hub is the central point of connectivity to your on-premises network, and a place to host services that can be consumed by the different workloads hosted in the spoke VNets.</span></span>

* <span data-ttu-id="bd1a7-135">**閘道子網路**。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-135">**Gateway subnet**.</span></span> <span data-ttu-id="bd1a7-136">虛擬網路閘道會保留在相同的子網路中。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-136">The virtual network gateways are held in the same subnet.</span></span>

* <span data-ttu-id="bd1a7-137">**共用服務子網路**。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-137">**Shared services subnet**.</span></span> <span data-ttu-id="bd1a7-138">中樞 VNet 中用來裝載可在所有輪輻間共用之服務 (例如 DNS 或 AD DS) 的子網路。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-138">A subnet in the hub VNet used to host services that can be shared among all spokes, such as DNS or AD DS.</span></span>

* <span data-ttu-id="bd1a7-139">**輪輻 VNet**。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-139">**Spoke VNets**.</span></span> <span data-ttu-id="bd1a7-140">在中樞輪輻拓撲中用來當作輪輻的一個或多個 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-140">One or more Azure VNets that are used as spokes in the hub-spoke topology.</span></span> <span data-ttu-id="bd1a7-141">輪輻可在自己的 VNet 中，用來隔離從其他輪輻分開管理的工作負載。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-141">Spokes can be used to isolate workloads in their own VNets, managed separately from other spokes.</span></span> <span data-ttu-id="bd1a7-142">每個工作負載在透過 Azure 負載平衡器連線多個子網路的情況下，都可能包括多層。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-142">Each workload might include multiple tiers, with multiple subnets connected through Azure load balancers.</span></span> <span data-ttu-id="bd1a7-143">如需有關應用程式基礎結構的詳細資訊，請參閱[執行 Windows VM 工作負載][windows-vm-ra]和[執行 Linux VM 工作負載][linux-vm-ra]。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-143">For more information about the application infrastructure, see [Running Windows VM workloads][windows-vm-ra] and [Running Linux VM workloads][linux-vm-ra].</span></span>

* <span data-ttu-id="bd1a7-144">**VNet 對等互連**。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-144">**VNet peering**.</span></span> <span data-ttu-id="bd1a7-145">在相同的 Azure 區域中，可以使用[對等連線][vnet-peering]來連線兩個 VNet。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-145">Two VNets in the same Azure region can be connected using a [peering connection][vnet-peering].</span></span> <span data-ttu-id="bd1a7-146">對等連線是 VNet 之間不可轉移的低延遲連線。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-146">Peering connections are non-transitive, low latency connections between VNets.</span></span> <span data-ttu-id="bd1a7-147">因此，一旦對等互連之後，VNet 就會使用 Azure 骨幹交換流量，而不需要使用路由器。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-147">Once peered, the VNets exchange traffic by using the Azure backbone, without the need for a router.</span></span> <span data-ttu-id="bd1a7-148">在中樞輪輻網路拓撲中，您可以使用 VNet 對等互連，將中樞連線至每個輪輻。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-148">In a hub-spoke network topology, you use VNet peering to connect the hub to each spoke.</span></span>

> [!NOTE]
> <span data-ttu-id="bd1a7-149">本文僅涵蓋 [Resource Manager](/azure/azure-resource-manager/resource-group-overview) 部署，但是您也可以在相同的訂用帳戶中，將傳統 VNet 連線至 Resource Manager VNet。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-149">This article only covers [Resource Manager](/azure/azure-resource-manager/resource-group-overview) deployments, but you can also connect a classic VNet to a Resource Manager VNet in the same subscription.</span></span> <span data-ttu-id="bd1a7-150">如此一來，您的輪輻就可以裝載傳統部署，而且仍然受益於中樞中共用的服務。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-150">That way, your spokes can host classic deployments and still benefit from services shared in the hub.</span></span>


## <a name="recommendations"></a><span data-ttu-id="bd1a7-151">建議</span><span class="sxs-lookup"><span data-stu-id="bd1a7-151">Recommendations</span></span>

<span data-ttu-id="bd1a7-152">下列建議適用於大部分的案例。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-152">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="bd1a7-153">除非您有特定的需求會覆寫它們，否則請遵循下列建議。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-153">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="resource-groups"></a><span data-ttu-id="bd1a7-154">資源群組</span><span class="sxs-lookup"><span data-stu-id="bd1a7-154">Resource groups</span></span>

<span data-ttu-id="bd1a7-155">中樞 VNet 以及每個輪輻 VNet 都可以在不同的資源群組，甚至是不同的訂用帳戶中實作，只要這些訂用帳戶屬於相同 Azure 區域中的相同 Azure Active Directory (Azure AD) 租用戶即可。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-155">The hub VNet, and each spoke VNet, can be implemented in different resource groups, and even different subscriptions, as long as they belong to the same Azure Active Directory (Azure AD) tenant in the same Azure region.</span></span> <span data-ttu-id="bd1a7-156">如此可對每個工作負載進行非集中式管理，同時在中樞 VNet 中維護共用服務。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-156">This allows for a decentralized management of each workload, while sharing services maintained in the hub VNet.</span></span>

### <a name="vnet-and-gatewaysubnet"></a><span data-ttu-id="bd1a7-157">VNet 和 GatewaySubnet</span><span class="sxs-lookup"><span data-stu-id="bd1a7-157">VNet and GatewaySubnet</span></span>

<span data-ttu-id="bd1a7-158">建立一個名為 *GatewaySubnet* 的子網路，且其位址範圍為 /27。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-158">Create a subnet named *GatewaySubnet*, with an address range of /27.</span></span> <span data-ttu-id="bd1a7-159">虛擬網路閘道需要使用這個子網路。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-159">This subnet is required by the virtual network gateway.</span></span> <span data-ttu-id="bd1a7-160">將 32 個位址配置給這個子網路，將有助於防止未來達到閘道大小限制。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-160">Allocating 32 addresses to this subnet will help to prevent reaching gateway size limitations in the future.</span></span>

<span data-ttu-id="bd1a7-161">如需有關設定閘道的詳細資訊，請根據您的連線類型，參閱下列參考架構：</span><span class="sxs-lookup"><span data-stu-id="bd1a7-161">For more information about setting up the gateway, see the following reference architectures, depending on your connection type:</span></span>

- <span data-ttu-id="bd1a7-162">[使用 ExpressRoute 的混合式網路][guidance-expressroute]</span><span class="sxs-lookup"><span data-stu-id="bd1a7-162">[Hybrid network using ExpressRoute][guidance-expressroute]</span></span>
- <span data-ttu-id="bd1a7-163">[使用 VPN 閘道的混合式網路][guidance-vpn]</span><span class="sxs-lookup"><span data-stu-id="bd1a7-163">[Hybrid network using a VPN gateway][guidance-vpn]</span></span>

<span data-ttu-id="bd1a7-164">如需高可用性，您可以使用 ExpressRoute 加上 VPN 進行容錯移轉。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-164">For higher availability, you can use ExpressRoute plus a VPN for failover.</span></span> <span data-ttu-id="bd1a7-165">請參閱[使用 ExpressRoute 搭配 VPN 容錯移轉，將內部部署網路連線至 Azure][hybrid-ha]。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-165">See [Connect an on-premises network to Azure using ExpressRoute with VPN failover][hybrid-ha].</span></span>

<span data-ttu-id="bd1a7-166">如果您不需要與內部部署網路連線，則不需要閘道也可以使用中樞輪輻拓撲。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-166">A hub-spoke topology can also be used without a gateway, if you don't need connectivity with your on-premises network.</span></span> 

### <a name="vnet-peering"></a><span data-ttu-id="bd1a7-167">VNet 對等互連</span><span class="sxs-lookup"><span data-stu-id="bd1a7-167">VNet peering</span></span>

<span data-ttu-id="bd1a7-168">VNet 對等互連是兩個 VNet 之間不可轉移的關聯性。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-168">VNet peering is a non-transitive relationship between two VNets.</span></span> <span data-ttu-id="bd1a7-169">如果您需要輪輻彼此連線，請考慮在這些輪輻之間加入個別的對等連線。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-169">If you require spokes to connect to each other, consider adding a separate peering connection between those spokes.</span></span>

<span data-ttu-id="bd1a7-170">不過，如果您有數個需要彼此連線的輪輻，將會因為[每個 VNet 的 VNet 對等互連數目限制][vnet-peering-limit]，而非常快速地用盡可能的對等連線。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-170">However, if you have several spokes that need to connect with each other, you will run out of possible peering connections very quickly due to the [limitation on number of VNets peerings per VNet][vnet-peering-limit].</span></span> <span data-ttu-id="bd1a7-171">在此案例中，請考慮使用使用者定義的路由 (UDR)，強制將目的地為輪輻的流量傳送到當作中樞 VNet 路由器的 NVA。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-171">In this scenario, consider using user defined routes (UDRs) to force traffic destined to a spoke to be sent to an NVA acting as a router at the hub VNet.</span></span> <span data-ttu-id="bd1a7-172">這可讓輪輻彼此連線。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-172">This will allow the spokes to connect to each other.</span></span>

<span data-ttu-id="bd1a7-173">您也可以將輪輻設定為使用中樞 VNet 閘道，與遠端網路進行通訊。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-173">You can also configure spokes to use the hub VNet gateway to communicate with remote networks.</span></span> <span data-ttu-id="bd1a7-174">若要允許閘道流量從輪輻流向中樞，然後連線至遠端網路，您必須：</span><span class="sxs-lookup"><span data-stu-id="bd1a7-174">To allow gateway traffic to flow from spoke to hub, and connect to remote networks, you must:</span></span>

  - <span data-ttu-id="bd1a7-175">將中樞中的 VNet 對等連線設定為**允許閘道傳輸**。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-175">Configure the VNet peering connection in the hub to **allow gateway transit**.</span></span>
  - <span data-ttu-id="bd1a7-176">將每個輪輻中的 VNet 對等連線設定為**使用遠端閘道**。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-176">Configure the VNet peering connection in each spoke to **use remote gateways**.</span></span>
  - <span data-ttu-id="bd1a7-177">將所有 VNet 對等連線設定為**允許轉送的流量**。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-177">Configure all VNet peering connections to **allow forwarded traffic**.</span></span>

## <a name="considerations"></a><span data-ttu-id="bd1a7-178">考量</span><span class="sxs-lookup"><span data-stu-id="bd1a7-178">Considerations</span></span>

### <a name="spoke-connectivity"></a><span data-ttu-id="bd1a7-179">輪輻連線能力</span><span class="sxs-lookup"><span data-stu-id="bd1a7-179">Spoke connectivity</span></span>

<span data-ttu-id="bd1a7-180">如果您需要在輪輻之間連線，請考慮實作 NVA 以便在中樞路由傳送，並在輪輻中使用 UDR，將流量轉送到中樞。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-180">If you require connectivity between spokes, consider implementing an NVA for routing in the hub, and using UDRs in the spoke to forward traffic to the hub.</span></span>

<span data-ttu-id="bd1a7-181">![[2]][2]</span><span class="sxs-lookup"><span data-stu-id="bd1a7-181">![[2]][2]</span></span>

<span data-ttu-id="bd1a7-182">在此案例中，您必須將對等連線設定為**允許轉送的流量**。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-182">In this scenario, you must configure the peering connections to **allow forwarded traffic**.</span></span>

### <a name="overcoming-vnet-peering-limits"></a><span data-ttu-id="bd1a7-183">克服 VNet 對等互連的限制</span><span class="sxs-lookup"><span data-stu-id="bd1a7-183">Overcoming VNet peering limits</span></span>

<span data-ttu-id="bd1a7-184">請確定您有在 Azure 中考慮[每個 VNet 的 VNet 對等互連數目限制][vnet-peering-limit]。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-184">Make sure you consider the [limitation on number of VNets peerings per VNet][vnet-peering-limit] in Azure.</span></span> <span data-ttu-id="bd1a7-185">如果您決定您需要的輪輻比允許的限制還多，請考慮建立中樞輪輻中樞輪輻拓撲，其中輪輻的第一層也可作為中樞。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-185">If you decide you need more spokes than the limit will allow, consider creating a hub-spoke-hub-spoke topology, where the first level of spokes also act as hubs.</span></span> <span data-ttu-id="bd1a7-186">下圖顯示這個方法。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-186">The following diagram shows this approach.</span></span>

<span data-ttu-id="bd1a7-187">![[3]][3]</span><span class="sxs-lookup"><span data-stu-id="bd1a7-187">![[3]][3]</span></span>

<span data-ttu-id="bd1a7-188">此外，請考慮要在中樞共用哪些服務，以確保中樞可針對大量輪輻進行調整。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-188">Also consider what services are shared in the hub, to ensure the hub scales for a larger number of spokes.</span></span> <span data-ttu-id="bd1a7-189">例如，如果您的中樞提供防火牆服務，請在新增多個輪輻時，考慮防火牆解決方案的頻寬限制。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-189">For instance, if your hub provides firewall services, consider the bandwidth limits of your firewall solution when adding multiple spokes.</span></span> <span data-ttu-id="bd1a7-190">您可以將其中部分共用服務移到中樞的第二層。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-190">You might want to move some of these shared services to a second level of hubs.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="bd1a7-191">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="bd1a7-191">Deploy the solution</span></span>

<span data-ttu-id="bd1a7-192">適用於此架構的部署可在 [GitHub][ref-arch-repo] 上取得。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-192">A deployment for this architecture is available on [GitHub][ref-arch-repo].</span></span> <span data-ttu-id="bd1a7-193">它會使用每個 VNet 中的 Ubuntu VM 測試連線。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-193">It uses Ubuntu VMs in each VNet to test connectivity.</span></span> <span data-ttu-id="bd1a7-194">**中樞 VNet** 的**共用服務**子網路中沒有裝載任何實際的服務。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-194">There are no actual services hosted in the **shared-services** subnet in the **hub VNet**.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="bd1a7-195">必要條件</span><span class="sxs-lookup"><span data-stu-id="bd1a7-195">Prerequisites</span></span>

<span data-ttu-id="bd1a7-196">在您可以將參考架構部署到自己的訂用帳戶之前，必須執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-196">Before you can deploy the reference architecture to your own subscription, you must perform the following steps.</span></span>

1. <span data-ttu-id="bd1a7-197">複製、派生或下載適用於 [AzureCAT 參考架構][ref-arch-repo] GitHub 存放庫的 zip 檔案。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-197">Clone, fork, or download the zip file for the [AzureCAT reference architectures][ref-arch-repo] GitHub repository.</span></span>

2. <span data-ttu-id="bd1a7-198">如果您想要使用 Azure CLI，請確定您已在電腦上安裝 Azure CLI 2.0。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-198">If you prefer to use the Azure CLI, make sure you have the Azure CLI 2.0 installed on your computer.</span></span> <span data-ttu-id="bd1a7-199">若要安裝 CLI，請依照[安裝 Azure CLI 2.0][azure-cli-2] 中的指示進行。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-199">To install the CLI, follow the instructions in [Install Azure CLI 2.0][azure-cli-2].</span></span>

3. <span data-ttu-id="bd1a7-200">如果您想要使用 PowerShell，請確定您已在電腦上安裝適用於 Azure 的最新 PowerShell 模組。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-200">If you prefer to use PowerShell, make sure you have the latest PowerShell module for Azure installed on you computer.</span></span> <span data-ttu-id="bd1a7-201">若要安裝最新的 Azure PowerShell 模組，請依照[安裝適用於 Azure 的 PowerShell][azure-powershell] 中的指示進行。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-201">To install the latest Azure PowerShell module, follow the instructions in [Install PowerShell for Azure][azure-powershell].</span></span>

4. <span data-ttu-id="bd1a7-202">從命令提示字元、bash 提示字元或 PowerShell 提示字元中，使用下列其中一個命令登入 Azure 帳戶，並遵循提示進行。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-202">From a command prompt, bash prompt, or PowerShell prompt, login to your Azure account by using one of the commands below, and follow the prompts.</span></span>

  ```bash
  az login
  ```

  ```powershell
  Login-AzureRmAccount
  ```

### <a name="deploy-the-simulated-on-premises-datacenter"></a><span data-ttu-id="bd1a7-203">部署模擬的內部部署資料中心</span><span class="sxs-lookup"><span data-stu-id="bd1a7-203">Deploy the simulated on-premises datacenter</span></span>

<span data-ttu-id="bd1a7-204">若要將模擬的內部部署資料中心部署為 Azure VNet，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-204">To deploy the simulated on-premises datacenter as an Azure VNet, perform the following steps.</span></span>

1. <span data-ttu-id="bd1a7-205">瀏覽至您在上述必要條件步驟中所下載存放庫的 `hybrid-networking\hub-spoke\onprem` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-205">Navigate to the `hybrid-networking\hub-spoke\onprem` folder for the repository you downloaded in the pre-requisites step above.</span></span>

2. <span data-ttu-id="bd1a7-206">開啟 `onprem.vm.parameters.json` 檔案，然後在第 11 和 12 行的引號之間輸入使用者名稱和密碼，如下所示，然後儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-206">Open the `onprem.vm.parameters.json` file and enter a username and password between the quotes in line 11 and 12, as shown below, then save the file.</span></span>

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```

3. <span data-ttu-id="bd1a7-207">執行以下的 bash 或 PowerShell 命令，將模擬的內部部署環境部署為 Azure 中的 VNet。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-207">Run the bash or PowerShell command below to deploy the simulated on-premises environment as a VNet in Azure.</span></span> <span data-ttu-id="bd1a7-208">將這些值取代為您的訂用帳戶、資源群組名稱和 Azure 區域。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-208">Substitute the values with your subscription, resource group name, and Azure region.</span></span>

  ```bash
  sh ./onprem.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-onprem-rg \
    --location westus
  ```

  ```powershell
  ./onprem.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-onprem-rg `
    -Location westus
  ```
  > [!NOTE]
  > <span data-ttu-id="bd1a7-209">如果您決定使用不同的資源群組名稱 (非 `ra-onprem-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-209">If you decide to use a different resource group name (other than `ra-onprem-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

4. <span data-ttu-id="bd1a7-210">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-210">Wait for the deployment to finish.</span></span> <span data-ttu-id="bd1a7-211">此部署會建立一個虛擬網路、一個執行 Ubuntu 的虛擬機器，以及一個 VPN 閘道。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-211">This deployment creates a virtual network, a virtual machine running Ubuntu, and a VPN gateway.</span></span> <span data-ttu-id="bd1a7-212">建立 VPN 閘道可能需要超過 40 分鐘才能完成。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-212">The VPN gateway creation can take more than 40 minutes to complete.</span></span>

### <a name="azure-hub-vnet"></a><span data-ttu-id="bd1a7-213">Azure 中樞 VNet</span><span class="sxs-lookup"><span data-stu-id="bd1a7-213">Azure hub VNet</span></span>

<span data-ttu-id="bd1a7-214">若要部署中樞 VNet 並連線到以上所建立的模擬內部部署 VNet，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-214">To deploy the hub VNet, and connect to the simulated on-premises VNet created above, perform the following steps.</span></span>

1. <span data-ttu-id="bd1a7-215">瀏覽至您在上述必要條件步驟中所下載存放庫的 `hybrid-networking\hub-spoke\hub` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-215">Navigate to the `hybrid-networking\hub-spoke\hub` folder for the repository you downloaded in the pre-requisites step above.</span></span>

2. <span data-ttu-id="bd1a7-216">開啟 `hub.vm.parameters.json` 檔案，然後在第 11 和 12 行的引號之間輸入使用者名稱和密碼，如下所示，然後儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-216">Open the `hub.vm.parameters.json` file and enter a username and password between the quotes in line 11 and 12, as shown below, then save the file.</span></span>

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```

3. <span data-ttu-id="bd1a7-217">開啟 `hub.gateway.parameters.json` 檔案，然後在第 23 行的引號之間輸入共用金鑰，如下所示，然後儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-217">Open the `hub.gateway.parameters.json` file and enter a shared key between the quotes in line 23, as shown below, then save the file.</span></span> <span data-ttu-id="bd1a7-218">請記下此值，稍後在部署時會使用到這個值。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-218">Keep a note of this value, you will need to use it later in the deployment.</span></span>

  ```bash
  "sharedKey": "",
  ```

4. <span data-ttu-id="bd1a7-219">執行以下的 bash 或 PowerShell 命令，將模擬的內部部署環境部署為 Azure 中的 VNet。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-219">Run the bash or PowerShell command below to deploy the simulated on-premises environment as a VNet in Azure.</span></span> <span data-ttu-id="bd1a7-220">將這些值取代為您的訂用帳戶、資源群組名稱和 Azure 區域。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-220">Substitute the values with your subscription, resource group name, and Azure region.</span></span>

  ```bash
  sh ./hub.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-hub-rg \
    --location westus
  ```

  ```powershell
  ./hub.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-hub-rg `
    -Location westus
  ```
  > [!NOTE]
  > <span data-ttu-id="bd1a7-221">如果您決定使用不同的資源群組名稱 (非 `ra-hub-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-221">If you decide to use a different resource group name (other than `ra-hub-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

5. <span data-ttu-id="bd1a7-222">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-222">Wait for the deployment to finish.</span></span> <span data-ttu-id="bd1a7-223">此部署會建立一個虛擬網路、一個執行 Ubuntu 的虛擬機器、一個 VPN 閘道，以及一個與上節中建立之閘道的連線。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-223">This deployment creates a virtual network, a virtual machine running Ubuntu, a VPN gateway, and a connection to the gateway created in the previous section.</span></span> <span data-ttu-id="bd1a7-224">建立 VPN 閘道可能需要超過 40 分鐘才能完成。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-224">The VPN gateway creation can take more than 40 minutes to complete.</span></span>

### <a name="connection-from-on-premises-to-the-hub"></a><span data-ttu-id="bd1a7-225">從內部部署連線到中樞</span><span class="sxs-lookup"><span data-stu-id="bd1a7-225">Connection from on-premises to the hub</span></span>

<span data-ttu-id="bd1a7-226">若要從模擬的內部部署資料中心連線到中樞 VNet，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-226">To connect from the simulated on-premises datacenter to the hub VNet, perform the following steps.</span></span>

1. <span data-ttu-id="bd1a7-227">瀏覽至您在上述必要條件步驟中所下載存放庫的 `hybrid-networking\hub-spoke\onprem` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-227">Navigate to the `hybrid-networking\hub-spoke\onprem` folder for the repository you downloaded in the pre-requisites step above.</span></span>

2. <span data-ttu-id="bd1a7-228">開啟 `onprem.connection.parameters.json` 檔案，然後在第 9 行的引號之間輸入共用金鑰，如下所示，然後儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-228">Open the `onprem.connection.parameters.json` file and enter a shared key between the quotes in line 9, as shown below, then save the file.</span></span> <span data-ttu-id="bd1a7-229">此共用金鑰值必須與您先前部署之內部部署閘道中使用的值相同。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-229">This shared key value must be the same used in the on-premises gateway you deployed previously.</span></span>

  ```bash
  "sharedKey": "",
  ```

3. <span data-ttu-id="bd1a7-230">執行以下的 bash 或 PowerShell 命令，將模擬的內部部署環境部署為 Azure 中的 VNet。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-230">Run the bash or PowerShell command below to deploy the simulated on-premises environment as a VNet in Azure.</span></span> <span data-ttu-id="bd1a7-231">將這些值取代為您的訂用帳戶、資源群組名稱和 Azure 區域。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-231">Substitute the values with your subscription, resource group name, and Azure region.</span></span>

  ```bash
  sh ./onprem.connection.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-onprem-rg \
    --location westus
  ```

  ```powershell
  ./onprem.connection.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-onprem-rg `
    -Location westus
  ```
  > [!NOTE]
  > <span data-ttu-id="bd1a7-232">如果您決定使用不同的資源群組名稱 (非 `ra-onprem-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-232">If you decide to use a different resource group name (other than `ra-onprem-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

4. <span data-ttu-id="bd1a7-233">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-233">Wait for the deployment to finish.</span></span> <span data-ttu-id="bd1a7-234">此部署會在用來模擬內部部署資料中心的 VNet 和中樞 VNet 之間建立一個連線。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-234">This deployment creates a connection between the VNet used to simulate an on-premises datacenter, and the hub VNet.</span></span>

### <a name="azure-spoke-vnets"></a><span data-ttu-id="bd1a7-235">Azure 輪輻 VNet</span><span class="sxs-lookup"><span data-stu-id="bd1a7-235">Azure spoke VNets</span></span>

<span data-ttu-id="bd1a7-236">若要部署輪輻 VNet 並連線到以上所建立的中樞 VNet，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-236">To deploy the spoke VNets, and connect to the hub VNet created above, perform the following steps.</span></span>

1. <span data-ttu-id="bd1a7-237">切換至您在上述先決條件步驟中下載之存放庫的 `hybrid-networking\hub-spoke\spokes` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-237">Switch to the `hybrid-networking\hub-spoke\spokes` folder for the repository you download in the pre-requisites step above.</span></span>

2. <span data-ttu-id="bd1a7-238">開啟 `spoke1.web.parameters.json` 檔案，然後在第 53 和 54 行的引號之間輸入使用者名稱和密碼，如下所示，然後儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-238">Open the `spoke1.web.parameters.json` file and enter a username and password between the quotes in line 53 and 54, as shown below, then save the file.</span></span>

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```

3. <span data-ttu-id="bd1a7-239">針對檔案 `spoke2.web.parameters.json` 重複上述步驟。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-239">Repeat the previous step for file `spoke2.web.parameters.json`.</span></span>

4. <span data-ttu-id="bd1a7-240">執行以下的 bash 或 PowerShell 命令，以部署第一個輪輻並將其連線至中樞。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-240">Run the bash or PowerShell command below to deploy the first spoke and connect it to the hub.</span></span> <span data-ttu-id="bd1a7-241">將這些值取代為您的訂用帳戶、資源群組名稱和 Azure 區域。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-241">Substitute the values with your subscription, resource group name, and Azure region.</span></span>

  ```bash
  sh ./spoke.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-spoke1-rg \
    --location westus \
    --spoke 1
  ```

  ```powershell
  ./spoke.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-spoke1-rg `
    -Location westus `
    -Spoke 1
  ```
  > [!NOTE]
  > <span data-ttu-id="bd1a7-242">如果您決定使用不同的資源群組名稱 (非 `ra-spoke1-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-242">If you decide to use a different resource group name (other than `ra-spoke1-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

5. <span data-ttu-id="bd1a7-243">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-243">Wait for the deployment to finish.</span></span> <span data-ttu-id="bd1a7-244">此部署建立一個虛擬網路、一個包含三個執行 Ubuntu 和 Apache 之虛擬機器的負載平衡器，以及一個與上節中建立之中樞 VNet 的 VNet 對等連線。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-244">This deployment creates a virtual network, a load balancer with three virtual machine running Ubuntu and Apache, and a VNet peering connection to the hub VNet created in the previous section.</span></span> <span data-ttu-id="bd1a7-245">此部署可能需要超過 20 分鐘。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-245">This deployment may take over 20 minutes.</span></span>

6. <span data-ttu-id="bd1a7-246">執行以下的 bash 或 PowerShell 命令，以部署第一個輪輻並將其連線至中樞。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-246">Run the bash or PowerShell command below to deploy the first spoke and connect it to the hub.</span></span> <span data-ttu-id="bd1a7-247">將這些值取代為您的訂用帳戶、資源群組名稱和 Azure 區域。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-247">Substitute the values with your subscription, resource group name, and Azure region.</span></span>

  ```bash
  sh ./spoke.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-spoke2-rg \
    --location westus \
    --spoke 2
  ```

  ```powershell
  ./spoke.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-spoke2-rg `
    -Location westus `
    -Spoke 2
  ```
  > [!NOTE]
  > <span data-ttu-id="bd1a7-248">如果您決定使用不同的資源群組名稱 (非 `ra-spoke2-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-248">If you decide to use a different resource group name (other than `ra-spoke2-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

5. <span data-ttu-id="bd1a7-249">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-249">Wait for the deployment to finish.</span></span> <span data-ttu-id="bd1a7-250">此部署建立一個虛擬網路、一個包含三個執行 Ubuntu 和 Apache 之虛擬機器的負載平衡器，以及一個與上節中建立之中樞 VNet 的 VNet 對等連線。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-250">This deployment creates a virtual network, a load balancer with three virtual machine running Ubuntu and Apache, and a VNet peering connection to the hub VNet created in the previous section.</span></span> <span data-ttu-id="bd1a7-251">此部署可能需要超過 20 分鐘。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-251">This deployment may take over 20 minutes.</span></span>

### <a name="azure-hub-vnet-peering-to-spoke-vnets"></a><span data-ttu-id="bd1a7-252">Azure 中樞 VNet 對等互連至輪輻 VNet</span><span class="sxs-lookup"><span data-stu-id="bd1a7-252">Azure hub VNet peering to spoke VNets</span></span>

<span data-ttu-id="bd1a7-253">若要部署中樞 VNet 的 VNet 對等連線，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-253">To deploy the VNet peering connections for the hub VNet, perform the following steps.</span></span>

1. <span data-ttu-id="bd1a7-254">切換至您在上述先決條件步驟中下載之存放庫的 `hybrid-networking\hub-spoke\hub` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-254">Switch to the `hybrid-networking\hub-spoke\hub` folder for the repository you download in the pre-requisites step above.</span></span>

2. <span data-ttu-id="bd1a7-255">執行以下的 bash 或 PowerShell 命令，以部署與第一個輪輻的對等連線。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-255">Run the bash or PowerShell command below to deploy the peering connection to the first spoke.</span></span> <span data-ttu-id="bd1a7-256">將這些值取代為您的訂用帳戶、資源群組名稱和 Azure 區域。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-256">Substitute the values with your subscription, resource group name, and Azure region.</span></span>

  ```bash
  sh ./hub.peering.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-hub-rg \
    --location westus \
    --spoke 1
  ```

  ```powershell
  ./hub.peering.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-hub-rg `
    -Location westus `
    -Spoke 1
  ```

2. <span data-ttu-id="bd1a7-257">執行以下的 bash 或 PowerShell 命令，以部署與第二個輪輻的對等連線。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-257">Run the bash or PowerShell command below to deploy the peering connection to the second spoke.</span></span> <span data-ttu-id="bd1a7-258">將這些值取代為您的訂用帳戶、資源群組名稱和 Azure 區域。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-258">Substitute the values with your subscription, resource group name, and Azure region.</span></span>

  ```bash
  sh ./hub.peering.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-hub-rg \
    --location westus \
    --spoke 2
  ```

  ```powershell
  ./hub.peering.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-hub-rg `
    -Location westus `
    -Spoke 2
  ```

### <a name="test-connectivity"></a><span data-ttu-id="bd1a7-259">測試連線能力</span><span class="sxs-lookup"><span data-stu-id="bd1a7-259">Test connectivity</span></span>

<span data-ttu-id="bd1a7-260">若要確認連線至內部部署資料中心部署的中樞輪輻拓撲正常運作，請依照下列步驟進行。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-260">To verify that the hub-spoke topology connected to an on-premises datacenter deployment worked, follow these steps.</span></span>

1. <span data-ttu-id="bd1a7-261">從 [Azure 入口網站][入口網站] 連線到您的訂用帳戶，然後瀏覽至 `ra-onprem-rg` 資源群組中的 `ra-onprem-vm1` 虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-261">From the [Azure portal][portal], connect to your subscription, and navigate to the `ra-onprem-vm1` virtual machine in the `ra-onprem-rg` resource group.</span></span>

2. <span data-ttu-id="bd1a7-262">在 `Overview` 刀鋒視窗中，記下 VM 的 `Public IP address`。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-262">In the `Overview` blade, note the `Public IP address` for the VM.</span></span>

3. <span data-ttu-id="bd1a7-263">使用您在部署期間指定的使用者名稱和密碼，透過 SSH 用戶端連線至您在上述步驟中記下的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-263">Use an SSH client to connect to the IP address you noted above using the user name and password you specified during deployment.</span></span>

4. <span data-ttu-id="bd1a7-264">從連線 VM 上的命令提示字元執行以下的命令，以測試內部部署 VNet 到 Spoke1 VNet 的連線。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-264">From the command prompt on the VM you connected to, run the command below to test connectivity from the on-premises VNet to the Spoke1 VNet.</span></span>

  ```bash
  ping 10.1.1.37
  ```

### <a name="add-connectivity-between-spokes"></a><span data-ttu-id="bd1a7-265">新增輪輻之間的連線</span><span class="sxs-lookup"><span data-stu-id="bd1a7-265">Add connectivity between spokes</span></span>

<span data-ttu-id="bd1a7-266">如果您想要允許輪輻彼此連線，必須將 UDR 部署至每個輪輻，以便將目的地為其他輪輻的流量轉送至中樞 VNet 中的閘道。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-266">If you want to allow spokes to connect to each other, you must deploy UDRs to each spoke that forward traffic destined to other spokes to the gateway in the hub VNet.</span></span> <span data-ttu-id="bd1a7-267">執行下列步驟，以確認您目前無法從一個輪輻連線到另一個輪輻，然後部署 UDR 並再次測試連線。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-267">Perform the following steps to verify that currently you are not able to connect from a spoke to another, then deploy the UDRs and test connectivity again.</span></span>

1. <span data-ttu-id="bd1a7-268">如果您無法再連線到 Jumpbox VM，請重複以上的步驟 1 到 4。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-268">Repeat steps 1 to 4 above, if you are not connected to the jumpbox VM any longer.</span></span>

2. <span data-ttu-id="bd1a7-269">連線至輪輻 1 中其中一個網頁伺服器。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-269">Connect to one of the web servers in spoke 1.</span></span>

  ```bash
  ssh 10.1.1.37
  ```

3. <span data-ttu-id="bd1a7-270">測試輪輻 1 和輪輻 2 之間的連線。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-270">Test the connectivity between spoke 1 and spoke 2.</span></span> <span data-ttu-id="bd1a7-271">連線應該會失敗。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-271">It should fail.</span></span>

  ```bash
  ping 10.1.2.37
  ```

4. <span data-ttu-id="bd1a7-272">切換回您電腦的命令提示字元。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-272">Switch back to your computer's command prompt.</span></span>

5. <span data-ttu-id="bd1a7-273">切換至您在上述必要條件步驟中所下載存放庫的 `hybrid-networking\hub-spoke\spokes` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-273">Switch to the `hybrid-networking\hub-spoke\spokes` folder for the repository you downloaded in the pre-requisites step above.</span></span>

6. <span data-ttu-id="bd1a7-274">執行以下的 bash 或 PowerShell 命令，將 UDR 部署至第一個輪輻。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-274">Run the bash or PowerShell command below to deploy an UDR to the first spoke.</span></span> <span data-ttu-id="bd1a7-275">將這些值取代為您的訂用帳戶、資源群組名稱和 Azure 區域。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-275">Substitute the values with your subscription, resource group name, and Azure region.</span></span>

  ```bash
  sh ./spoke.udr.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-spoke1-rg \
    --location westus \
    --spoke 1
  ```

  ```powershell
  ./spoke.udr.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-spoke1-rg `
    -Location westus `
    -Spoke 1
  ```

7. <span data-ttu-id="bd1a7-276">執行以下的 bash 或 PowerShell 命令，將 UDR 部署至第二個輪輻。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-276">Run the bash or PowerShell command below to deploy an UDR to the second spoke.</span></span> <span data-ttu-id="bd1a7-277">將這些值取代為您的訂用帳戶、資源群組名稱和 Azure 區域。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-277">Substitute the values with your subscription, resource group name, and Azure region.</span></span>

  ```bash
  sh ./spoke.udr.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-spoke2-rg \
    --location westus \
    --spoke 2
  ```

  ```powershell
  ./spoke.udr.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-spoke2-rg `
    -Location westus `
    -Spoke 2
  ```

8. <span data-ttu-id="bd1a7-278">切換回 SSH 終端機。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-278">Switch back to the ssh terminal.</span></span>

9. <span data-ttu-id="bd1a7-279">測試輪輻 1 和輪輻 2 之間的連線。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-279">Test the connectivity between spoke 1 and spoke 2.</span></span> <span data-ttu-id="bd1a7-280">連線應該會成功。</span><span class="sxs-lookup"><span data-stu-id="bd1a7-280">It should succeed.</span></span>

  ```bash
  ping 10.1.2.37
  ```

<!-- links -->

[azure-cli-2]: /azure/install-azure-cli
[azure-powershell]: /powershell/azure/install-azure-ps?view=azuresmps-3.7.0
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

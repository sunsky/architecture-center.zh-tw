---
title: 選擇將內部部署網路連線到 Azure 的解決方案
description: 比較將內部部署網路連線到 Azure 的參考架構。
author: telmosampaio
ms.date: 07/02/2018
ms.openlocfilehash: 0cc07d3b7d45accf9f99ce32914b0ef065d62f32
ms.sourcegitcommit: 776b8c1efc662d42273a33de3b82ec69e3cd80c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38987473"
---
# <a name="connect-an-on-premises-network-to-azure"></a><span data-ttu-id="24f0b-103">將內部部署網路連線到 Azure</span><span class="sxs-lookup"><span data-stu-id="24f0b-103">Connect an on-premises network to Azure</span></span>

<span data-ttu-id="24f0b-104">本文會比較將內部部署網路連線到 Azure 虛擬網路 (VNet) 的選項。</span><span class="sxs-lookup"><span data-stu-id="24f0b-104">This article compares options for connecting an on-premises network to an Azure Virtual Network (VNet).</span></span> <span data-ttu-id="24f0b-105">對於每個選項都可以使用更詳細的參考架構。</span><span class="sxs-lookup"><span data-stu-id="24f0b-105">For each option, a more detailed reference architecture is available.</span></span>

## <a name="vpn-connection"></a><span data-ttu-id="24f0b-106">VPN 連線</span><span class="sxs-lookup"><span data-stu-id="24f0b-106">VPN connection</span></span>

<span data-ttu-id="24f0b-107">[VPN 閘道](/azure/vpn-gateway/vpn-gateway-about-vpngateways)是一種虛擬網路閘道，可在 Azure 虛擬網路和內部部署位置之間傳送加密流量。</span><span class="sxs-lookup"><span data-stu-id="24f0b-107">A [VPN gateway](/azure/vpn-gateway/vpn-gateway-about-vpngateways) is a type of virtual network gateway that sends encrypted traffic between an Azure virtual network and an on-premises location.</span></span> <span data-ttu-id="24f0b-108">加密流量會通過公用網際網路。</span><span class="sxs-lookup"><span data-stu-id="24f0b-108">The encrypted traffic goes over the public Internet.</span></span>

<span data-ttu-id="24f0b-109">此架構適合內部部署硬體和雲端之間的流量可能不大的混合式應用程式，或是您願意用稍微延長的延遲交換雲端彈性和處理能力的混合式應用程式。</span><span class="sxs-lookup"><span data-stu-id="24f0b-109">This architecture is suitable for hybrid applications where the traffic between on-premises hardware and the cloud is likely to be light, or you are willing to trade slightly extended latency for the flexibility and processing power of the cloud.</span></span>

<span data-ttu-id="24f0b-110">**優點**</span><span class="sxs-lookup"><span data-stu-id="24f0b-110">**Benefits**</span></span>

- <span data-ttu-id="24f0b-111">設定容易。</span><span class="sxs-lookup"><span data-stu-id="24f0b-111">Simple to configure.</span></span>

<span data-ttu-id="24f0b-112">**挑戰**</span><span class="sxs-lookup"><span data-stu-id="24f0b-112">**Challenges**</span></span>

- <span data-ttu-id="24f0b-113">需要有內部部署 VPN 裝置。</span><span class="sxs-lookup"><span data-stu-id="24f0b-113">Requires an on-premises VPN device.</span></span>
- <span data-ttu-id="24f0b-114">雖然 Microsoft 保證每個 VPN 閘道可達 99.9% 的可用性，但是此 [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) 只涵蓋 VPN 閘道，而不涵蓋閘道的網路連線。</span><span class="sxs-lookup"><span data-stu-id="24f0b-114">Although Microsoft guarantees 99.9% availability for each VPN Gateway, this [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) only covers the VPN gateway, and not your network connection to the gateway.</span></span>
- <span data-ttu-id="24f0b-115">透過 Azure VPN 閘道的 VPN 連線目前最多可支援 200 Mbps 的頻寬。</span><span class="sxs-lookup"><span data-stu-id="24f0b-115">A VPN connection over Azure VPN Gateway currently supports a maximum of 200 Mbps bandwidth.</span></span> <span data-ttu-id="24f0b-116">如果您預期會超過此輸送量，可能需要跨多個 VPN 連線分割 Azure 虛擬網路。</span><span class="sxs-lookup"><span data-stu-id="24f0b-116">You may need to partition your Azure virtual network across multiple VPN connections if you expect to exceed this throughput.</span></span>

<span data-ttu-id="24f0b-117">**參考架構**</span><span class="sxs-lookup"><span data-stu-id="24f0b-117">**Reference architecture**</span></span>

- [<span data-ttu-id="24f0b-118">使用 VPN 閘道的混合式網路</span><span class="sxs-lookup"><span data-stu-id="24f0b-118">Hybrid network with VPN gateway</span></span>](./vpn.md)

## <a name="azure-expressroute-connection"></a><span data-ttu-id="24f0b-119">Azure ExpressRoute 連線</span><span class="sxs-lookup"><span data-stu-id="24f0b-119">Azure ExpressRoute connection</span></span>

<span data-ttu-id="24f0b-120">[ExpressRoute](/azure/expressroute/) 連線會透過第三方連線提供者，使用私人的專用連線。</span><span class="sxs-lookup"><span data-stu-id="24f0b-120">[ExpressRoute](/azure/expressroute/) connections use a private, dedicated connection through a third-party connectivity provider.</span></span> <span data-ttu-id="24f0b-121">私人連線會將內部部署網路延伸到 Azure。</span><span class="sxs-lookup"><span data-stu-id="24f0b-121">The private connection extends your on-premises network into Azure.</span></span> 

<span data-ttu-id="24f0b-122">此架構適合執行需要高度延展性之大型、任務關鍵性工作負載的混合式應用程式。</span><span class="sxs-lookup"><span data-stu-id="24f0b-122">This architecture is suitable for hybrid applications running large-scale, mission-critical workloads that require a high degree of scalability.</span></span> 

<span data-ttu-id="24f0b-123">**優點**</span><span class="sxs-lookup"><span data-stu-id="24f0b-123">**Benefits**</span></span>

- <span data-ttu-id="24f0b-124">可用的頻寬更高；根據連線提供者而定，最多可達 10 Gbps。</span><span class="sxs-lookup"><span data-stu-id="24f0b-124">Much higher bandwidth available; up to 10 Gbps depending on the connectivity provider.</span></span>
- <span data-ttu-id="24f0b-125">支援動態調整頻寬，有助於在要求較低的期間降低成本。</span><span class="sxs-lookup"><span data-stu-id="24f0b-125">Supports dynamic scaling of bandwidth to help reduce costs during periods of lower demand.</span></span> <span data-ttu-id="24f0b-126">不過，並非所有連線提供者都有這個選項。</span><span class="sxs-lookup"><span data-stu-id="24f0b-126">However, not all connectivity providers have this option.</span></span>
- <span data-ttu-id="24f0b-127">根據連線提供者而定，或許可讓您的組織直接存取國家雲端。</span><span class="sxs-lookup"><span data-stu-id="24f0b-127">May allow your organization direct access to national clouds, depending on the connectivity provider.</span></span>
- <span data-ttu-id="24f0b-128">整個連線的可用性 SLA 為 99.9%。</span><span class="sxs-lookup"><span data-stu-id="24f0b-128">99.9% availability SLA across the entire connection.</span></span>

<span data-ttu-id="24f0b-129">**挑戰**</span><span class="sxs-lookup"><span data-stu-id="24f0b-129">**Challenges**</span></span>

- <span data-ttu-id="24f0b-130">設定可能相當複雜。</span><span class="sxs-lookup"><span data-stu-id="24f0b-130">Can be complex to set up.</span></span> <span data-ttu-id="24f0b-131">建立 ExpressRoute 連線需要使用第三方連線提供者。</span><span class="sxs-lookup"><span data-stu-id="24f0b-131">Creating an ExpressRoute connection requires working with a third-party connectivity provider.</span></span> <span data-ttu-id="24f0b-132">此提供者負責佈建網路連線。</span><span class="sxs-lookup"><span data-stu-id="24f0b-132">The provider is responsible for provisioning the network connection.</span></span>
- <span data-ttu-id="24f0b-133">需要高頻寬的路由器內部部署。</span><span class="sxs-lookup"><span data-stu-id="24f0b-133">Requires high-bandwidth routers on-premises.</span></span>

<span data-ttu-id="24f0b-134">**參考架構**</span><span class="sxs-lookup"><span data-stu-id="24f0b-134">**Reference architecture**</span></span>

- [<span data-ttu-id="24f0b-135">使用 ExpressRoute 的混合式網路</span><span class="sxs-lookup"><span data-stu-id="24f0b-135">Hybrid network with ExpressRoute</span></span>](./expressroute.md)

## <a name="expressroute-with-vpn-failover"></a><span data-ttu-id="24f0b-136">含 VPN 容錯移轉的 ExpressRoute</span><span class="sxs-lookup"><span data-stu-id="24f0b-136">ExpressRoute with VPN failover</span></span>

<span data-ttu-id="24f0b-137">此選項結合上述兩者，在正常情況下使用 ExpressRoute，但在 ExpressRoute 線路的連線中斷時，容錯移轉到 VPN 連線。</span><span class="sxs-lookup"><span data-stu-id="24f0b-137">This options combines the previous two, using ExpressRoute in normal conditions, but failing over to a VPN connection if there is a loss of connectivity in the ExpressRoute circuit.</span></span>

<span data-ttu-id="24f0b-138">此架構適合需要較高頻寬的 ExpressRoute，而且也需要高可用性網路連線的混合式應用程式。</span><span class="sxs-lookup"><span data-stu-id="24f0b-138">This architecture is suitable for hybrid applications that need the higher bandwidth of ExpressRoute, and also require highly available network connectivity.</span></span> 

<span data-ttu-id="24f0b-139">**優點**</span><span class="sxs-lookup"><span data-stu-id="24f0b-139">**Benefits**</span></span>

- <span data-ttu-id="24f0b-140">如果 ExpressRoute 線路失敗，雖然後援連線位於較低頻寬的網路，但仍然可達高可用性。</span><span class="sxs-lookup"><span data-stu-id="24f0b-140">High availability if the ExpressRoute circuit fails, although the fallback connection is on a lower bandwidth network.</span></span>

<span data-ttu-id="24f0b-141">**挑戰**</span><span class="sxs-lookup"><span data-stu-id="24f0b-141">**Challenges**</span></span>

- <span data-ttu-id="24f0b-142">設定複雜。</span><span class="sxs-lookup"><span data-stu-id="24f0b-142">Complex to configure.</span></span> <span data-ttu-id="24f0b-143">您需要同時設定 VPN 連線與 ExpressRoute 線路。</span><span class="sxs-lookup"><span data-stu-id="24f0b-143">You need to set up both a VPN connection and an ExpressRoute circuit.</span></span>
- <span data-ttu-id="24f0b-144">需要備援硬體 (VPN 設備)，以及您必須支付費用的備援 Azure VPN 閘道連線。</span><span class="sxs-lookup"><span data-stu-id="24f0b-144">Requires redundant hardware (VPN appliances), and a redundant Azure VPN Gateway connection for which you pay charges.</span></span>

<span data-ttu-id="24f0b-145">**參考架構**</span><span class="sxs-lookup"><span data-stu-id="24f0b-145">**Reference architecture**</span></span>

- [<span data-ttu-id="24f0b-146">使用 ExpressRoute 和 VPN 容錯移轉的混合式網路</span><span class="sxs-lookup"><span data-stu-id="24f0b-146">Hybrid network with ExpressRoute and VPN failover</span></span>](./expressroute-vpn-failover.md)


## <a name="hub-spoke-network-topology"></a><span data-ttu-id="24f0b-147">中樞輪輻網路拓撲</span><span class="sxs-lookup"><span data-stu-id="24f0b-147">Hub-spoke network topology</span></span>

<span data-ttu-id="24f0b-148">中樞輪輻網路拓撲是一種在共用服務 (例如身分識別和安全性) 時隔離工作負載的方式。</span><span class="sxs-lookup"><span data-stu-id="24f0b-148">A hub-spoke network topology is a way to isolate workloads while sharing services such as identity and security.</span></span> <span data-ttu-id="24f0b-149">「中樞」是 Azure 中的虛擬網路 (VNet)，可當作內部部署網路的連線中心點。</span><span class="sxs-lookup"><span data-stu-id="24f0b-149">The hub is a virtual network (VNet) in Azure that acts as a central point of connectivity to your on-premises network.</span></span> <span data-ttu-id="24f0b-150">「輪輻」是與中樞對等的 VNet。</span><span class="sxs-lookup"><span data-stu-id="24f0b-150">The spokes are VNets that peer with the hub.</span></span> <span data-ttu-id="24f0b-151">共用的服務會部署在中樞中，而個別的工作負載會部署為輪輻。</span><span class="sxs-lookup"><span data-stu-id="24f0b-151">Shared services are deployed in the hub, while individual workloads are deployed as spokes.</span></span>


<span data-ttu-id="24f0b-152">**參考架構**</span><span class="sxs-lookup"><span data-stu-id="24f0b-152">**Reference architectures**</span></span>

- [<span data-ttu-id="24f0b-153">中樞輪輻拓撲</span><span class="sxs-lookup"><span data-stu-id="24f0b-153">Hub-spoke topology</span></span>](./hub-spoke.md)
- [<span data-ttu-id="24f0b-154">具有共用服務的中樞輪輻</span><span class="sxs-lookup"><span data-stu-id="24f0b-154">Hub-spoke with shared services</span></span>](./shared-services.md)

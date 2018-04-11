---
title: 選擇將內部部署網路連線到 Azure 的解決方案
description: 比較將內部部署網路連線到 Azure 的參考架構。
author: telmosampaio
ms.date: 04/06/2017
ms.openlocfilehash: 274b9df1817632a7f3eaafa8bf02e965fdc3feea
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="choose-a-solution-for-connecting-an-on-premises-network-to-azure"></a><span data-ttu-id="0ff17-103">選擇將內部部署網路連線到 Azure 的解決方案</span><span class="sxs-lookup"><span data-stu-id="0ff17-103">Choose a solution for connecting an on-premises network to Azure</span></span>

<span data-ttu-id="0ff17-104">本文會比較將內部部署網路連線到 Azure 虛擬網路 (VNet) 的選項。</span><span class="sxs-lookup"><span data-stu-id="0ff17-104">This article compares options for connecting an on-premises network to an Azure Virtual Network (VNet).</span></span> <span data-ttu-id="0ff17-105">我們會為每個選項提供一個參考架構和可部署的解決方案。</span><span class="sxs-lookup"><span data-stu-id="0ff17-105">We provide a reference architecture and a deployable solution for each option.</span></span>

## <a name="vpn-connection"></a><span data-ttu-id="0ff17-106">VPN 連線</span><span class="sxs-lookup"><span data-stu-id="0ff17-106">VPN connection</span></span>

<span data-ttu-id="0ff17-107">使用虛擬私人網路 (VPN)，透過 IPSec VPN 通道將您的內部部署網路與 Azure VNet 連線。</span><span class="sxs-lookup"><span data-stu-id="0ff17-107">Use a virtual private network (VPN) to connect your on-premises network with an Azure VNet through an IPSec VPN tunnel.</span></span>

<span data-ttu-id="0ff17-108">此架構適合內部部署硬體和雲端之間的流量可能不大的混合式應用程式，或是您願意用稍微延長的延遲交換雲端彈性和處理能力的混合式應用程式。</span><span class="sxs-lookup"><span data-stu-id="0ff17-108">This architecture is suitable for hybrid applications where the traffic between on-premises hardware and the cloud is likely to be light, or you are willing to trade slightly extended latency for the flexibility and processing power of the cloud.</span></span>

<span data-ttu-id="0ff17-109">**優點**</span><span class="sxs-lookup"><span data-stu-id="0ff17-109">**Benefits**</span></span>

- <span data-ttu-id="0ff17-110">設定容易。</span><span class="sxs-lookup"><span data-stu-id="0ff17-110">Simple to configure.</span></span>

<span data-ttu-id="0ff17-111">**挑戰**</span><span class="sxs-lookup"><span data-stu-id="0ff17-111">**Challenges**</span></span>

- <span data-ttu-id="0ff17-112">需要有內部部署 VPN 裝置。</span><span class="sxs-lookup"><span data-stu-id="0ff17-112">Requires an on-premises VPN device.</span></span>
- <span data-ttu-id="0ff17-113">雖然 Microsoft 保證每個 VPN 閘道可達 99.9% 的可用性，但是此 SLA 只涵蓋 VPN 閘道，而不涵蓋閘道的網路連線。</span><span class="sxs-lookup"><span data-stu-id="0ff17-113">Although Microsoft guarantees 99.9% availability for each VPN Gateway, this SLA only covers the VPN gateway, and not your network connection to the gateway.</span></span>
- <span data-ttu-id="0ff17-114">透過 Azure VPN 閘道的 VPN 連線目前最多可支援 200 Mbps 的頻寬。</span><span class="sxs-lookup"><span data-stu-id="0ff17-114">A VPN connection over Azure VPN Gateway currently supports a maximum of 200 Mbps bandwidth.</span></span> <span data-ttu-id="0ff17-115">如果您預期會超過此輸送量，可能需要跨多個 VPN 連線分割 Azure 虛擬網路。</span><span class="sxs-lookup"><span data-stu-id="0ff17-115">You may need to partition your Azure virtual network across multiple VPN connections if you expect to exceed this throughput.</span></span>

<span data-ttu-id="0ff17-116">**[閱讀更多...][vpn]**</span><span class="sxs-lookup"><span data-stu-id="0ff17-116">**[Read more...][vpn]**</span></span>

## <a name="azure-expressroute-connection"></a><span data-ttu-id="0ff17-117">Azure ExpressRoute 連線</span><span class="sxs-lookup"><span data-stu-id="0ff17-117">Azure ExpressRoute connection</span></span>

<span data-ttu-id="0ff17-118">ExpressRoute 連線會透過第三方連線提供者，使用私人的專用連線。</span><span class="sxs-lookup"><span data-stu-id="0ff17-118">ExpressRoute connections use a private, dedicated connection through a third-party connectivity provider.</span></span> <span data-ttu-id="0ff17-119">私人連線會將內部部署網路延伸到 Azure。</span><span class="sxs-lookup"><span data-stu-id="0ff17-119">The private connection extends your on-premises network into Azure.</span></span> 

<span data-ttu-id="0ff17-120">此架構適合執行需要高度延展性之大型、任務關鍵性工作負載的混合式應用程式。</span><span class="sxs-lookup"><span data-stu-id="0ff17-120">This architecture is suitable for hybrid applications running large-scale, mission-critical workloads that require a high degree of scalability.</span></span> 

<span data-ttu-id="0ff17-121">**優點**</span><span class="sxs-lookup"><span data-stu-id="0ff17-121">**Benefits**</span></span>

- <span data-ttu-id="0ff17-122">可用的頻寬更高；根據連線提供者而定，最多可達 10 Gbps。</span><span class="sxs-lookup"><span data-stu-id="0ff17-122">Much higher bandwidth available; up to 10 Gbps depending on the connectivity provider.</span></span>
- <span data-ttu-id="0ff17-123">支援動態調整頻寬，有助於在要求較低的期間降低成本。</span><span class="sxs-lookup"><span data-stu-id="0ff17-123">Supports dynamic scaling of bandwidth to help reduce costs during periods of lower demand.</span></span> <span data-ttu-id="0ff17-124">不過，並非所有連線提供者都有這個選項。</span><span class="sxs-lookup"><span data-stu-id="0ff17-124">However, not all connectivity providers have this option.</span></span>
- <span data-ttu-id="0ff17-125">根據連線提供者而定，或許可讓您的組織直接存取國家雲端。</span><span class="sxs-lookup"><span data-stu-id="0ff17-125">May allow your organization direct access to national clouds, depending on the connectivity provider.</span></span>
- <span data-ttu-id="0ff17-126">整個連線的可用性 SLA 為 99.9%。</span><span class="sxs-lookup"><span data-stu-id="0ff17-126">99.9% availability SLA across the entire connection.</span></span>

<span data-ttu-id="0ff17-127">**挑戰**</span><span class="sxs-lookup"><span data-stu-id="0ff17-127">**Challenges**</span></span>

- <span data-ttu-id="0ff17-128">設定可能相當複雜。</span><span class="sxs-lookup"><span data-stu-id="0ff17-128">Can be complex to set up.</span></span> <span data-ttu-id="0ff17-129">建立 ExpressRoute 連線需要使用第三方連線提供者。</span><span class="sxs-lookup"><span data-stu-id="0ff17-129">Creating an ExpressRoute connection requires working with a third-party connectivity provider.</span></span> <span data-ttu-id="0ff17-130">此提供者負責佈建網路連線。</span><span class="sxs-lookup"><span data-stu-id="0ff17-130">The provider is responsible for provisioning the network connection.</span></span>
- <span data-ttu-id="0ff17-131">需要高頻寬的路由器內部部署。</span><span class="sxs-lookup"><span data-stu-id="0ff17-131">Requires high-bandwidth routers on-premises.</span></span>

<span data-ttu-id="0ff17-132">**[閱讀更多...][expressroute]**</span><span class="sxs-lookup"><span data-stu-id="0ff17-132">**[Read more...][expressroute]**</span></span>

## <a name="expressroute-with-vpn-failover"></a><span data-ttu-id="0ff17-133">含 VPN 容錯移轉的 ExpressRoute</span><span class="sxs-lookup"><span data-stu-id="0ff17-133">ExpressRoute with VPN failover</span></span>

<span data-ttu-id="0ff17-134">此選項結合上述兩者，在正常情況下使用 ExpressRoute，但在 ExpressRoute 線路的連線中斷時，容錯移轉到 VPN 連線。</span><span class="sxs-lookup"><span data-stu-id="0ff17-134">This options combines the previous two, using ExpressRoute in normal conditions, but failing over to a VPN connection if there is a loss of connectivity in the ExpressRoute circuit.</span></span>

<span data-ttu-id="0ff17-135">此架構適合需要較高頻寬的 ExpressRoute，而且也需要高可用性網路連線的混合式應用程式。</span><span class="sxs-lookup"><span data-stu-id="0ff17-135">This architecture is suitable for hybrid applications that need the higher bandwidth of ExpressRoute, and also require highly available network connectivity.</span></span> 

<span data-ttu-id="0ff17-136">**優點**</span><span class="sxs-lookup"><span data-stu-id="0ff17-136">**Benefits**</span></span>

- <span data-ttu-id="0ff17-137">如果 ExpressRoute 線路失敗，雖然後援連線位於較低頻寬的網路，但仍然可達高可用性。</span><span class="sxs-lookup"><span data-stu-id="0ff17-137">High availability if the ExpressRoute circuit fails, although the fallback connection is on a lower bandwidth network.</span></span>

<span data-ttu-id="0ff17-138">**挑戰**</span><span class="sxs-lookup"><span data-stu-id="0ff17-138">**Challenges**</span></span>

- <span data-ttu-id="0ff17-139">設定複雜。</span><span class="sxs-lookup"><span data-stu-id="0ff17-139">Complex to configure.</span></span> <span data-ttu-id="0ff17-140">您需要同時設定 VPN 連線與 ExpressRoute 線路。</span><span class="sxs-lookup"><span data-stu-id="0ff17-140">You need to set up both a VPN connection and an ExpressRoute circuit.</span></span>
- <span data-ttu-id="0ff17-141">需要備援硬體 (VPN 設備)，以及您必須支付費用的備援 Azure VPN 閘道連線。</span><span class="sxs-lookup"><span data-stu-id="0ff17-141">Requires redundant hardware (VPN appliances), and a redundant Azure VPN Gateway connection for which you pay charges.</span></span>

<span data-ttu-id="0ff17-142">**[閱讀更多...][expressroute-vpn-failover]**</span><span class="sxs-lookup"><span data-stu-id="0ff17-142">**[Read more...][expressroute-vpn-failover]**</span></span>

<!-- links -->
[expressroute]: ./expressroute.md
[expressroute-vpn-failover]: ./expressroute-vpn-failover.md
[vpn]: ./vpn.md
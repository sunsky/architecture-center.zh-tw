---
title: CAF：軟體定義網路 - 混合式網路
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 討論混合式網路如何讓您的雲端虛擬網路連線到內部部署資源
author: rotycenh
ms.openlocfilehash: 02d181db0ae9baef3b453b8623d212b624f6b16a
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243359"
---
# <a name="software-defined-networks-hybrid-network"></a><span data-ttu-id="23fe6-103">軟體定義網路：混合式網路</span><span class="sxs-lookup"><span data-stu-id="23fe6-103">Software Defined Networks: Hybrid network</span></span>

<span data-ttu-id="23fe6-104">混合式雲端網路架構可讓虛擬網路使用專用 WAN 連線 (例如 ExpressRoute 或其他直接連線網路的連線方法) 存取您的內部部署資源和服務，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="23fe6-104">The hybrid cloud network architecture allows virtual networks to access your on-premises resources and services and vice versa, using a Dedicated WAN connection such as ExpressRoute or other connection method to directly connect the networks.</span></span>

![混合式網路](../../../reference-architectures/hybrid-networking/images/expressroute.png)

<span data-ttu-id="23fe6-106">混合式虛擬網路是建置在雲端原生虛擬網路架構上，最初建立時是獨立的。</span><span class="sxs-lookup"><span data-stu-id="23fe6-106">Building on the cloud native virtual network architecture, a hybrid virtual network is isolated when initially created.</span></span> <span data-ttu-id="23fe6-107">將連線新增至內部部署環境會授與內部部署網路的存取權，雖然必須明確允許虛擬網路中的其他所有輸入流量目標資源。</span><span class="sxs-lookup"><span data-stu-id="23fe6-107">Adding connectivity to the on-premises environment grants access to and from the on-premises network, although all other inbound traffic targeting resources in the virtual network need to be explicitly allowed.</span></span> <span data-ttu-id="23fe6-108">您可以使用虛擬防火牆裝置和路由規則來保護連線以限制存取，或者您可以準確指定可使用雲端原生路由功能存取兩個網路之間的哪些服務，或部署網路虛擬設備 (NVA) 以管理流量。</span><span class="sxs-lookup"><span data-stu-id="23fe6-108">You can secure the connection using virtual firewall devices and routing rules to limit access or you can specify exactly what services can be accessed between the two networks using cloud-native routing features or deploying network virtual appliances (NVAs) to manage traffic.</span></span>

<span data-ttu-id="23fe6-109">雖然混合式網路架構支援 VPN 連線，但是例如 ExpressRoute 的專用 WAN 連線因為有較高的效能和更高的安全性所以經常是偏好選項。</span><span class="sxs-lookup"><span data-stu-id="23fe6-109">Although the hybrid networking architecture supports VPN connections, dedicated WAN connections like ExpressRoute are generally preferred due to higher performance and increased security.</span></span>

## <a name="hybrid-assumptions"></a><span data-ttu-id="23fe6-110">混合式假設</span><span class="sxs-lookup"><span data-stu-id="23fe6-110">Hybrid assumptions</span></span>

<span data-ttu-id="23fe6-111">部署混合式虛擬網路假設以下事項：</span><span class="sxs-lookup"><span data-stu-id="23fe6-111">Deploying a hybrid virtual network assumes the following:</span></span>

- <span data-ttu-id="23fe6-112">您的 IT 安全性小組具備一致的內部部署和雲端型網路安全性原則，以確保可以信任雲端型虛擬網路，直接與內部部署系統通訊。</span><span class="sxs-lookup"><span data-stu-id="23fe6-112">Your IT security teams have aligned on-premises and cloud-based network security policy to ensure cloud-based virtual networks can be trusted to communicated directly with on-premises systems.</span></span>
- <span data-ttu-id="23fe6-113">您的雲端型工作負載需要存取儲存體、應用程式和您的內部部署或第三方網路上裝載的服務，或您的使用者或您內部部署中的應用程式需要存取雲端裝載資源。</span><span class="sxs-lookup"><span data-stu-id="23fe6-113">Your cloud-based workloads require access to storage, applications, and services hosted on your on-premises or third-party networks, or your users or applications in your on-premises need access to cloud-hosted resources.</span></span>
- <span data-ttu-id="23fe6-114">您需要遷移現有的應用程式和服務 (相依於內部部署資源)，但不想要耗費資源重新開發來移除這些相依性。</span><span class="sxs-lookup"><span data-stu-id="23fe6-114">You need to migrate existing applications and services that depend on on-premises resources, but don't want to expend the resources on redevelopment to remove those dependencies.</span></span>
- <span data-ttu-id="23fe6-115">在您的內部部署網路和雲端提供者之間實作 VPN 或專用 WAN 連線，不會被公司原則、法規需求或技術功能問題阻止。</span><span class="sxs-lookup"><span data-stu-id="23fe6-115">Implementing a VPN or dedicated WAN connection between your on-premises networks and cloud provider is not prevented by corporate policy, regulatory requirements, or technical compatibility issues.</span></span>
- <span data-ttu-id="23fe6-116">您的工作負載不需要多個訂用帳戶以略過訂用帳戶資源限制，或您的工作負載涉及多個訂用帳戶，但不需要集中管理多個訂用帳戶之間分散之資源使用的連線或共用資源。</span><span class="sxs-lookup"><span data-stu-id="23fe6-116">Your workloads either do not require multiple subscriptions to bypass subscription resource limits, OR your workloads involve multiple subscriptions but do not require central management of connectivity or shared services used by resources spread across multiple subscriptions.</span></span>

<span data-ttu-id="23fe6-117">您的雲端採用小組在查看實作混合式虛擬網路架構時，應該考量下列問題：</span><span class="sxs-lookup"><span data-stu-id="23fe6-117">Your Cloud Adoption team should consider the following issues when looking at implementing a hybrid virtual networking architecture:</span></span>

- <span data-ttu-id="23fe6-118">連線內部部署網路與雲端網路，增加您的安全性需求的複雜度。</span><span class="sxs-lookup"><span data-stu-id="23fe6-118">Connecting on-premises networks with cloud networks increases the complexity of your security requirements.</span></span> <span data-ttu-id="23fe6-119">這兩個網路都必須受到保護，免於混合式環境兩端的外部弱點和未經授權存取的侵襲。</span><span class="sxs-lookup"><span data-stu-id="23fe6-119">Both networks need to be secured against external vulnerabilities and unauthorized access from both sides of the hybrid environment.</span></span>
- <span data-ttu-id="23fe6-120">調整混合式雲端環境內工作負載的數量和大小，可以對路由和流量管理增加大幅複雜性。</span><span class="sxs-lookup"><span data-stu-id="23fe6-120">Scaling the number and size of workloads within a hybrid cloud environment can add significant complexity to routing and traffic management.</span></span>
- <span data-ttu-id="23fe6-121">您必須開發相容的管理和存取控制原則，以維護整個組織一致的治理。</span><span class="sxs-lookup"><span data-stu-id="23fe6-121">You will need to develop compatible management and access control policies to maintain consistent governance throughout your organization.</span></span>

## <a name="learn-more"></a><span data-ttu-id="23fe6-122">深入了解</span><span class="sxs-lookup"><span data-stu-id="23fe6-122">Learn more</span></span>

<span data-ttu-id="23fe6-123">如需 Azure 平台中混合式網路的詳細資訊，請參閱下列各項。</span><span class="sxs-lookup"><span data-stu-id="23fe6-123">See the following for more information about hybrid networking in the Azure platform.</span></span>

- <span data-ttu-id="23fe6-124">[混合式網路參考架構](../../../reference-architectures/hybrid-networking/expressroute.md)。</span><span class="sxs-lookup"><span data-stu-id="23fe6-124">[Hybrid network reference architecture](../../../reference-architectures/hybrid-networking/expressroute.md).</span></span> <span data-ttu-id="23fe6-125">Azure 混合式虛擬網路使用 ExpressRoute 線路或 Azure VPN，連接您的虛擬網路與貴組織現有非 Azure 裝載 IT 資產。</span><span class="sxs-lookup"><span data-stu-id="23fe6-125">Azure hybrid virtual networks use either an ExpressRoute circuit or Azure VPN to connect your virtual network with your organization's existing non-Azure hosted IT assets.</span></span> <span data-ttu-id="23fe6-126">本文討論在 Azure 中建立混合式網路的選項。</span><span class="sxs-lookup"><span data-stu-id="23fe6-126">This article discusses the options for creating a hybrid network in Azure.</span></span>

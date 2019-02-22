---
title: CAF：軟體定義網路 - 雲端原生
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 討論雲端原生的虛擬網路服務
author: rotycenh
ms.openlocfilehash: e0ad6803f2ddc982ea0c42c59fdf2486e1710433
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900871"
---
# <a name="software-defined-networks-hub-and-spoke"></a><span data-ttu-id="686e5-103">軟體定義網路：中樞與輪輻</span><span class="sxs-lookup"><span data-stu-id="686e5-103">Software Defined Networks: Hub and Spoke</span></span>

<span data-ttu-id="686e5-104">中樞與輪輻網路模型會將以 Azure 為基礎的雲端網路基礎結構組織為多個已連線的虛擬網路。</span><span class="sxs-lookup"><span data-stu-id="686e5-104">The hub and spoke networking model organizes your Azure-based cloud network infrastructure into multiple connected virtual networks.</span></span> <span data-ttu-id="686e5-105">此模型可讓您更有效率地管理一般通訊或安全性需求，以及處理潛在的訂用帳戶限制。</span><span class="sxs-lookup"><span data-stu-id="686e5-105">This model allows you to more efficiently manage common communication or security requirements and deal with potential subscription limitations.</span></span>

<span data-ttu-id="686e5-106">在中樞與輪輻模型中，「中樞」是一個虛擬網路，可做為中心位置來管理外部連線能力，以及裝載多個工作負載所使用的服務。</span><span class="sxs-lookup"><span data-stu-id="686e5-106">In the hub and spoke model, the *hub* is a virtual network that acts as a central location for managing external connectivity and hosting services used by multiple workloads.</span></span> <span data-ttu-id="686e5-107">「輪輻」是可裝載工作負載並透過[虛擬網路對等互連](/virtual-network/virtual-network-peering-overview)來連線到中央中樞的虛擬網路。</span><span class="sxs-lookup"><span data-stu-id="686e5-107">The *spokes* are virtual networks that host workloads and connect to the central hub through [virtual network peering](/virtual-network/virtual-network-peering-overview).</span></span>

<span data-ttu-id="686e5-108">傳入或傳出工作負載輪輻網路的所有流量，都會透過中樞網路進行路由傳送，可透過集中管理的 IT 規則或流程，對其進行路由傳送、檢查，或以其他方式來管理。</span><span class="sxs-lookup"><span data-stu-id="686e5-108">All traffic passing in or out of the workload spoke networks is routed through the hub network where it can be routed, inspected, or otherwise managed by centrally managed IT rules or processes.</span></span>

<span data-ttu-id="686e5-109">此模型旨在處理下列問題：</span><span class="sxs-lookup"><span data-stu-id="686e5-109">This model aims to address the following issues:</span></span>

- <span data-ttu-id="686e5-110">節省成本和管理效率。</span><span class="sxs-lookup"><span data-stu-id="686e5-110">Cost savings and management efficiency.</span></span> <span data-ttu-id="686e5-111">將可由多個工作負載共用的服務 (例如網路虛擬設備 (NVA) 和 DNS 伺服器) 集中在單一位置，讓 IT 能夠跨多個工作負載，將多餘的資源和管理投入量降至最低。</span><span class="sxs-lookup"><span data-stu-id="686e5-111">Centralizing services that can be shared by multiple workloads, such as network virtual appliances (NVAs) and DNS servers, in a single location allows IT to minimize redundant resources and management effort across multiple workloads.</span></span>
- <span data-ttu-id="686e5-112">克服訂用帳戶限制。</span><span class="sxs-lookup"><span data-stu-id="686e5-112">Overcoming subscriptions limits.</span></span> <span data-ttu-id="686e5-113">大型雲端式工作負載需要使用的資源，可能比單一 Azure 訂用帳戶內所允許的資源還要多 (請參閱[訂用帳戶限制](/azure/azure-subscription-service-limits))。</span><span class="sxs-lookup"><span data-stu-id="686e5-113">Large cloud-based workloads may require the use of more resources than are allowed within a single Azure subscription (see [subscription limits](/azure/azure-subscription-service-limits)).</span></span> <span data-ttu-id="686e5-114">將工作負載虛擬網路從不同的訂用帳戶對等互連到中央中樞，即可克服這些限制。</span><span class="sxs-lookup"><span data-stu-id="686e5-114">Peering workload virtual networks from different subscriptions to a central hub can overcome these limits.</span></span>
- <span data-ttu-id="686e5-115">分開考量。</span><span class="sxs-lookup"><span data-stu-id="686e5-115">Separation of concerns.</span></span> <span data-ttu-id="686e5-116">能夠在中央 IT 小組和工作負載小組之間部署個別工作負載。</span><span class="sxs-lookup"><span data-stu-id="686e5-116">The ability to deploy individual workloads between central IT teams and workloads teams.</span></span>

<span data-ttu-id="686e5-117">下圖顯示一個範例中樞與輪輻架構，其中包括集中管理的混合式連線。</span><span class="sxs-lookup"><span data-stu-id="686e5-117">The following diagram shows an example hub and spoke architecture including centrally managed hybrid connectivity.</span></span>

![中樞輪輻網路架構](../../../reference-architectures/hybrid-networking/images/hub-spoke.png)

<span data-ttu-id="686e5-119">中樞與輪輻架構通常會與混合式網路架構搭配使用，為您在多個工作負載之間共用的內部部署環境提供集中管理的連線。</span><span class="sxs-lookup"><span data-stu-id="686e5-119">The hub and spoke architecture is often used alongside the hybrid networking architecture, providing a centrally managed connection to your on-premises environment shared between multiple workloads.</span></span> <span data-ttu-id="686e5-120">在此案例中，往返工作負載與內部部署之間的所有流量，都會通過可管理並保護它的中樞。</span><span class="sxs-lookup"><span data-stu-id="686e5-120">In this scenario, all traffic traveling between the workloads and on-premises passes through the hub where it can be managed and secured.</span></span>

## <a name="hub-and-spoke-assumptions"></a><span data-ttu-id="686e5-121">中樞與輪輻的假設</span><span class="sxs-lookup"><span data-stu-id="686e5-121">Hub and spoke assumptions</span></span>

<span data-ttu-id="686e5-122">實作中樞與輪輻虛擬網路架構的假設如下：</span><span class="sxs-lookup"><span data-stu-id="686e5-122">Implementing a hub and spoke virtual networking architecture assumes the following:</span></span>

- <span data-ttu-id="686e5-123">您的雲端部署將涵蓋裝載於個別工作環境 (例如開發、測試和生產) 中的工作負載，這些工作環境全都依賴一組常見的服務 (例如 DNS 或目錄服務)。</span><span class="sxs-lookup"><span data-stu-id="686e5-123">Your cloud deployments will involve workloads hosted in separate working environments, such as development, test, and production, that all rely on a set of common services such as DNS or directory services.</span></span>
- <span data-ttu-id="686e5-124">您的工作負載不需要彼此通訊，但會有常見的外部通訊和共用服務需求。</span><span class="sxs-lookup"><span data-stu-id="686e5-124">Your workloads do not need to communicate with each other but have common external communications and shared services requirements.</span></span>
- <span data-ttu-id="686e5-125">您的工作負載所需的資源，比單一 Azure 訂用帳戶內可用的資源還要多。</span><span class="sxs-lookup"><span data-stu-id="686e5-125">Your workloads require more resources than are available within a single Azure subscription.</span></span>
- <span data-ttu-id="686e5-126">您需要為工作負載小組提供其本身資源的委派管理權限，同時保有對外部連線的集中安全性管理。</span><span class="sxs-lookup"><span data-stu-id="686e5-126">You need to provide workload teams with delegated management rights over their own resources while maintaining central security control over external connectivity.</span></span>

## <a name="global-hub-and-spoke"></a><span data-ttu-id="686e5-127">全域中樞與輪輻</span><span class="sxs-lookup"><span data-stu-id="686e5-127">Global hub and spoke</span></span>

<span data-ttu-id="686e5-128">中樞與輪輻架構通常會使用部署至同一個 Azure 區域的虛擬網路來實作，以便將網路之間的延遲降至最低。</span><span class="sxs-lookup"><span data-stu-id="686e5-128">Hub and spoke architectures are commonly implemented with virtual networks deployed to the same Azure Region to minimize latency between networks.</span></span> <span data-ttu-id="686e5-129">不過，觸角擴及全球的大型組織可能需要跨多個區域部署工作負載，以滿足可用性、災害復原或法規需求。</span><span class="sxs-lookup"><span data-stu-id="686e5-129">However, large organizations with global reach may need to deploy workloads across multiple regions for availability, disaster recovery, or regulatory requirements.</span></span> <span data-ttu-id="686e5-130">透過使用 Azure [全域虛擬網路對等互連](/azure/virtual-network/virtual-network-peering-overview)，中樞與輪輻模型可以跨區域擴充集中式管理和共用服務，以支援分散在世界各地的工作負載。</span><span class="sxs-lookup"><span data-stu-id="686e5-130">Through the use of Azure [global virtual network peering](/azure/virtual-network/virtual-network-peering-overview), the hub and spoke model can extend centralized management and shared services across regions to support workloads distributed across the world.</span></span>

## <a name="learn-more"></a><span data-ttu-id="686e5-131">深入了解</span><span class="sxs-lookup"><span data-stu-id="686e5-131">Learn more</span></span>

<span data-ttu-id="686e5-132">如需如何在 Azure 上實作中樞與輪輻網路的範例，請參閱 Azure 參考架構網站上的下列範例：</span><span class="sxs-lookup"><span data-stu-id="686e5-132">For examples of how to implement hub and spoke networks on Azure, see the following examples on the Azure Reference Architectures site:</span></span>

- [<span data-ttu-id="686e5-133">在 Azure 中實作中樞輪輻網路拓撲</span><span class="sxs-lookup"><span data-stu-id="686e5-133">Implement a hub-spoke network topology in Azure</span></span>](../../../reference-architectures/hybrid-networking/hub-spoke.md)
- [<span data-ttu-id="686e5-134">在 Azure 中實作中樞輪輻網路拓撲與共用服務</span><span class="sxs-lookup"><span data-stu-id="686e5-134">Implement a hub-spoke network topology with shared services in Azure</span></span>](../../../reference-architectures/hybrid-networking/shared-services.md)

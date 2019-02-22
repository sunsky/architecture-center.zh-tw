---
title: CAF：大型企業治理旅程圖
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 大型企業治理旅程圖
author: BrianBlanchard
ms.openlocfilehash: 689b600524c3be6c505ade8c5aaa447d772c6e35
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900762"
---
# <a name="caf-large-enterprise-governance-journey"></a><span data-ttu-id="61acc-103">CAF：大型企業治理旅程圖</span><span class="sxs-lookup"><span data-stu-id="61acc-103">CAF: Large enterprise governance journey</span></span>

## <a name="best-practice-overview"></a><span data-ttu-id="61acc-104">最佳做法概觀</span><span class="sxs-lookup"><span data-stu-id="61acc-104">Best practice overview</span></span>

<span data-ttu-id="61acc-105">這個治理旅程圖會遵循虛構公司在治理成熟度各個階段的體驗，</span><span class="sxs-lookup"><span data-stu-id="61acc-105">This governance journey follows the experiences of a fictional company through various stages of governance maturity.</span></span> <span data-ttu-id="61acc-106">並以實際的客戶旅程圖為基礎。</span><span class="sxs-lookup"><span data-stu-id="61acc-106">It is based on real customer journeys.</span></span> <span data-ttu-id="61acc-107">建議的最佳做法則會以該虛構公司的條件約束和需求為主。</span><span class="sxs-lookup"><span data-stu-id="61acc-107">The suggested best practices are based on the constraints and needs of the fictional company.</span></span>

<span data-ttu-id="61acc-108">此概觀會根據最佳做法來定義治理的最簡可行產品 (MVP) 以作為快速起點。</span><span class="sxs-lookup"><span data-stu-id="61acc-108">As a quick starting point, this overview defines a minimum viable product (MVP) for governance based on best practices.</span></span> <span data-ttu-id="61acc-109">它還提供一些治理演進的連結，這些演進會隨著新業務或技術風險的出現，進一步新增最佳做法。</span><span class="sxs-lookup"><span data-stu-id="61acc-109">It also provides links to some governance evolutions that add further best practices as new business or technical risks emerge.</span></span>

> [!WARNING]
> <span data-ttu-id="61acc-110">這個 MVP 是基於一組假設的基準起點。</span><span class="sxs-lookup"><span data-stu-id="61acc-110">This MVP is a baseline starting point, based on a set of assumptions.</span></span> <span data-ttu-id="61acc-111">即便是這組基本的最佳做法，也會以獨特的業務風險和風險承受度所衍生的公司原則為基礎。</span><span class="sxs-lookup"><span data-stu-id="61acc-111">Even this minimal set of best practices is based on corporate policies driven by unique business risks and risk tolerances.</span></span> <span data-ttu-id="61acc-112">若要查看您是否適用這些假設，請閱讀本文後面[較長的敘述](./narrative.md)。</span><span class="sxs-lookup"><span data-stu-id="61acc-112">To see if these assumptions apply to you, read the [longer narrative](./narrative.md) that follows this article.</span></span>

### <a name="governance-best-practice"></a><span data-ttu-id="61acc-113">治理最佳做法</span><span class="sxs-lookup"><span data-stu-id="61acc-113">Governance best practice</span></span>

<span data-ttu-id="61acc-114">這個最佳做法會作為組織可用來在多個 Azure 訂用帳戶中快速且一致地新增治理防護的基礎。</span><span class="sxs-lookup"><span data-stu-id="61acc-114">This best practice serves as a foundation that an organization can use to quickly and consistently add governance guardrails across multiple Azure subscriptions.</span></span>

### <a name="resource-organization"></a><span data-ttu-id="61acc-115">資源組織</span><span class="sxs-lookup"><span data-stu-id="61acc-115">Resource organization</span></span>

<span data-ttu-id="61acc-116">下圖顯示組織資源的治理 MVP 階層。</span><span class="sxs-lookup"><span data-stu-id="61acc-116">The following diagram shows the governance MVP hierarchy for organizing resources.</span></span>

![資源組織圖](../../../_images/governance/resource-organization.png)

<span data-ttu-id="61acc-118">每個應用程式都應該在管理群組、訂用帳戶，以及資源群組階層的適當區域中部署。</span><span class="sxs-lookup"><span data-stu-id="61acc-118">Every application should be deployed in the proper area of the management group, subscription, and resource group hierarchy.</span></span> <span data-ttu-id="61acc-119">在部署規劃期間，雲端治理小組將在階層中建立必要的節點，以使雲端採用小組更具產能。</span><span class="sxs-lookup"><span data-stu-id="61acc-119">During deployment planning, the Cloud Governance team will create the necessary nodes in the hierarchy to empower the cloud adoption teams.</span></span>

1. <span data-ttu-id="61acc-120">適用於每個營業單位的管理群組，以及詳細反映地理位置和環境類型 (生產、非生產) 的階層。</span><span class="sxs-lookup"><span data-stu-id="61acc-120">A management group for each business unit with a detailed hierarchy that reflects geography then environment type (Production, Non-Production).</span></span>
2. <span data-ttu-id="61acc-121">適用於每個獨特的營業單位、地理位置、環境和「應用程式分類」組合的訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="61acc-121">A subscription for each unique combination of business unit, geography, environment, and "Application Categorization."</span></span>
3. <span data-ttu-id="61acc-122">適用於每個應用程式的單獨資源群組。</span><span class="sxs-lookup"><span data-stu-id="61acc-122">A separate resource group for each application.</span></span>
4. <span data-ttu-id="61acc-123">應該在此群組階層的每個層級套用一致的命名法。</span><span class="sxs-lookup"><span data-stu-id="61acc-123">Consistent nomenclature should be applied at each level of this grouping hierarchy.</span></span>

![大型企業資源組織圖](../../../_images/governance/large-enterprise-resource-organization.png)

<span data-ttu-id="61acc-125">這些模式會提供成長的空間，而不會不必要地使階層複雜化。</span><span class="sxs-lookup"><span data-stu-id="61acc-125">These patterns provide room for growth without complicating the hierarchy unnecessarily.</span></span>

[!INCLUDE [governance-of-resources](../../../../../includes/cloud-adoption/governance/governance-of-resources.md)]

## <a name="governance-evolutions"></a><span data-ttu-id="61acc-126">治理演進</span><span class="sxs-lookup"><span data-stu-id="61acc-126">Governance evolutions</span></span>

<span data-ttu-id="61acc-127">一旦部署此 MVP 之後，其他治理層就可以快速地合併到環境中。</span><span class="sxs-lookup"><span data-stu-id="61acc-127">Once this MVP has been deployed, additional layers of governance can be quickly incorporated into the environment.</span></span> <span data-ttu-id="61acc-128">以下是一些推演 MVP 以符合特定業務需求的方式：</span><span class="sxs-lookup"><span data-stu-id="61acc-128">Here are some ways to evolve the MVP to meet specific business needs:</span></span>

- [<span data-ttu-id="61acc-129">受保護資料的安全性基準</span><span class="sxs-lookup"><span data-stu-id="61acc-129">Security Baseline for protected data</span></span>](./security-baseline-evolution.md)
- [<span data-ttu-id="61acc-130">任務關鍵性應用程式的資源設定</span><span class="sxs-lookup"><span data-stu-id="61acc-130">Resource configurations for mission-critical applications</span></span>](./resource-consistency-evolution.md)
- [<span data-ttu-id="61acc-131">成本管理控制</span><span class="sxs-lookup"><span data-stu-id="61acc-131">Controls for Cost Management</span></span>](./cost-management-evolution.md)
- [<span data-ttu-id="61acc-132">多重雲端演進的控制</span><span class="sxs-lookup"><span data-stu-id="61acc-132">Controls for multi-cloud evolution</span></span>](./multi-cloud-evolution.md)

<!-- markdownlint-disable MD026 -->

## <a name="what-does-this-best-practice-do"></a><span data-ttu-id="61acc-133">這個最佳做法有哪些用途？</span><span class="sxs-lookup"><span data-stu-id="61acc-133">What does this best practice do?</span></span>

<span data-ttu-id="61acc-134">在 MVP 中，從[部署加速](../../deployment-acceleration/overview.md)專業領域建立做法和工具是為了快速套用公司原則。</span><span class="sxs-lookup"><span data-stu-id="61acc-134">In the MVP, practices and tools from the [Deployment Acceleration](../../deployment-acceleration/overview.md) discipline are established to quickly apply corporate policy.</span></span> <span data-ttu-id="61acc-135">特別是，MVP 會使用 Azure 藍圖、Azure 原則及 Azure 管理群組來套用數個基本的公司原則，如這家虛構公司的敘述中所定義。</span><span class="sxs-lookup"><span data-stu-id="61acc-135">In particular, the MVP uses Azure Blueprints, Azure Policy, and Azure management groups to apply a few basic corporate policies, as defined in the narrative for this fictional company.</span></span> <span data-ttu-id="61acc-136">這些公司原則會使用 Azure Resource Manager 範本與 Azure 原則來套用，以建立很小的身分識別和安全性基準。</span><span class="sxs-lookup"><span data-stu-id="61acc-136">Those corporate policies are applied using Azure Resource Manager templates and Azure policies to establish a very small baseline for identity and security.</span></span>

![累加式治理 MVP 的範例](../../../_images/governance/governance-mvp.png)

## <a name="evolving-the-best-practice"></a><span data-ttu-id="61acc-138">推演最佳做法</span><span class="sxs-lookup"><span data-stu-id="61acc-138">Evolving the best practice</span></span>

<span data-ttu-id="61acc-139">經過一段時間之後，這個治理 MVP 將用於推演治理做法。</span><span class="sxs-lookup"><span data-stu-id="61acc-139">Over time, this governance MVP will be used to evolve the governance practices.</span></span> <span data-ttu-id="61acc-140">隨著採用率提高，業務風險也會增加。</span><span class="sxs-lookup"><span data-stu-id="61acc-140">As adoption advances, business risk grows.</span></span> <span data-ttu-id="61acc-141">CAF 治理模型中的各種專業領域將持續推演以降低這些風險。</span><span class="sxs-lookup"><span data-stu-id="61acc-141">Various disciplines within the CAF governance model will evolve to mitigate those risks.</span></span> <span data-ttu-id="61acc-142">本系列的後續文章將討論影響這家虛構公司的公司原則演進。</span><span class="sxs-lookup"><span data-stu-id="61acc-142">Later articles in this series discuss the evolution of corporate policy affecting the fictional company.</span></span> <span data-ttu-id="61acc-143">這些演進會跨三個專業領域進行：</span><span class="sxs-lookup"><span data-stu-id="61acc-143">These evolutions happen across three disciplines:</span></span>

- <span data-ttu-id="61acc-144">身分識別基準 (移轉相依性在敘述中持續推演時)</span><span class="sxs-lookup"><span data-stu-id="61acc-144">Identity Baseline, as migration dependencies evolve in the narrative</span></span>
- <span data-ttu-id="61acc-145">成本管理 (採用擴大規模時)</span><span class="sxs-lookup"><span data-stu-id="61acc-145">Cost Management, as adoption scales.</span></span>
- <span data-ttu-id="61acc-146">安全性基準 (部署受保護的資料時)。</span><span class="sxs-lookup"><span data-stu-id="61acc-146">Security Baseline, as protected data is deployed.</span></span>
- <span data-ttu-id="61acc-147">資源一致性 (IT 操作開始支援任務關鍵性工作負載時)。</span><span class="sxs-lookup"><span data-stu-id="61acc-147">Resource Consistency, as IT Operations begins supporting mission-critical workloads.</span></span>

![累加式治理 MVP 的範例](../../../_images/governance/governance-evolution-large.png)

## <a name="next-steps"></a><span data-ttu-id="61acc-149">後續步驟</span><span class="sxs-lookup"><span data-stu-id="61acc-149">Next steps</span></span>

<span data-ttu-id="61acc-150">既然您已經熟悉治理 MVP，並且了解後續的治理演進，請閱讀對於其他內容的支持性敘述。</span><span class="sxs-lookup"><span data-stu-id="61acc-150">Now that you’re familiar with the governance MVP and have an idea of the governance evolutions to follow, read the supporting narrative for additional context.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="61acc-151">閱讀支持性敘述</span><span class="sxs-lookup"><span data-stu-id="61acc-151">Read the supporting narrative</span></span>](./narrative.md)

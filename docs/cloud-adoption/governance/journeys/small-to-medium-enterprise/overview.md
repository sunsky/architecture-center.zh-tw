---
title: CAF：中小型企業治理旅程
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 中小型企業治理旅程
author: BrianBlanchard
ms.openlocfilehash: a3e078845038a12977e7be5affbf22708411069f
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58245159"
---
# <a name="small-to-medium-enterprise-governance-journey"></a><span data-ttu-id="d7a11-103">中小型企業治理旅程</span><span class="sxs-lookup"><span data-stu-id="d7a11-103">Small-to-medium enterprise governance journey</span></span>

## <a name="best-practice-overview"></a><span data-ttu-id="d7a11-104">最佳做法概觀</span><span class="sxs-lookup"><span data-stu-id="d7a11-104">Best practice overview</span></span>

<span data-ttu-id="d7a11-105">這個治理旅程圖會遵循虛構公司在治理成熟度各個階段的體驗，</span><span class="sxs-lookup"><span data-stu-id="d7a11-105">This governance journey follows the experiences of a fictional company through various stages of governance maturity.</span></span> <span data-ttu-id="d7a11-106">並以實際的客戶旅程圖為基礎。</span><span class="sxs-lookup"><span data-stu-id="d7a11-106">It is based on real customer journeys.</span></span> <span data-ttu-id="d7a11-107">建議的最佳做法則會以該虛構公司的條件約束和需求為主。</span><span class="sxs-lookup"><span data-stu-id="d7a11-107">The suggested best practices are based on the constraints and needs of the fictional company.</span></span>

<span data-ttu-id="d7a11-108">此概觀會根據最佳做法來定義治理的最簡可行產品 (MVP) 以作為快速起點。</span><span class="sxs-lookup"><span data-stu-id="d7a11-108">As a quick starting point, this overview defines a minimum viable product (MVP) for governance based on best practices.</span></span> <span data-ttu-id="d7a11-109">它還提供一些治理演進的連結，這些演進會隨著新業務或技術風險的出現，進一步新增最佳做法。</span><span class="sxs-lookup"><span data-stu-id="d7a11-109">It also provides links to some governance evolutions that add further best practices as new business or technical risks emerge.</span></span>

> [!WARNING]
> <span data-ttu-id="d7a11-110">這個 MVP 是基於一組假設的基準起點。</span><span class="sxs-lookup"><span data-stu-id="d7a11-110">This MVP is a baseline starting point, based on a set of assumptions.</span></span> <span data-ttu-id="d7a11-111">即便是這一系列最佳做法，也是以獨特的業務風險和風險承受度推動的公司原則。</span><span class="sxs-lookup"><span data-stu-id="d7a11-111">Even this minimal set of best practices is based on corporate policies driven by unique business risks and risk tolerances.</span></span> <span data-ttu-id="d7a11-112">若要查看您是否適用這些假設，請閱讀本文後面[較長的敘述](./narrative.md)。</span><span class="sxs-lookup"><span data-stu-id="d7a11-112">To see if these assumptions apply to you, read the [longer narrative](./narrative.md) that follows this article.</span></span>

## <a name="governance-best-practice"></a><span data-ttu-id="d7a11-113">治理最佳做法</span><span class="sxs-lookup"><span data-stu-id="d7a11-113">Governance best practice</span></span>

<span data-ttu-id="d7a11-114">這個最佳做法會作為組織可用來在多個 Azure 訂用帳戶中快速且一致地新增治理防護的基礎。</span><span class="sxs-lookup"><span data-stu-id="d7a11-114">This best practice serves as a foundation that an organization can use to quickly and consistently add governance guardrails across multiple Azure subscriptions.</span></span>

### <a name="resource-organization"></a><span data-ttu-id="d7a11-115">資源組織</span><span class="sxs-lookup"><span data-stu-id="d7a11-115">Resource organization</span></span>

<span data-ttu-id="d7a11-116">下圖顯示組織資源的治理 MVP 階層。</span><span class="sxs-lookup"><span data-stu-id="d7a11-116">The following diagram shows the governance MVP hierarchy for organizing resources.</span></span>

![資源組織圖](../../../_images/governance/resource-organization.png)

<span data-ttu-id="d7a11-118">每個應用程式都應該在管理群組、訂用帳戶，以及資源群組階層的適當區域中部署。</span><span class="sxs-lookup"><span data-stu-id="d7a11-118">Every application should be deployed in the proper area of the management group, subscription, and resource group hierarchy.</span></span> <span data-ttu-id="d7a11-119">在部署規劃期間，雲端治理小組將會在階層中建立必要的節點，讓雲端採用小組更強大。</span><span class="sxs-lookup"><span data-stu-id="d7a11-119">During deployment planning, the Cloud Governance team will create the necessary nodes in the hierarchy to empower the cloud adoption teams.</span></span>  

1. <span data-ttu-id="d7a11-120">每種類型環境 (例如生產、開發和測試) 的管理群組。</span><span class="sxs-lookup"><span data-stu-id="d7a11-120">A management group for each type of environment (such as Production, Development, and Test).</span></span>
2. <span data-ttu-id="d7a11-121">每個「應用程式分類」的訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="d7a11-121">A subscription for each "application categorization".</span></span>
3. <span data-ttu-id="d7a11-122">每個應用程式單獨的資源群組。</span><span class="sxs-lookup"><span data-stu-id="d7a11-122">A separate resource group for each application.</span></span>
4. <span data-ttu-id="d7a11-123">應該在此群組階層的每個層級應用一致的命名法。</span><span class="sxs-lookup"><span data-stu-id="d7a11-123">Consistent nomenclature should be applied at each level of this grouping hierarchy.</span></span>

<span data-ttu-id="d7a11-124">以下是使用中的這種模式的一個範例：</span><span class="sxs-lookup"><span data-stu-id="d7a11-124">Here is an example of this pattern in use:</span></span>

![中型市場公司的資源組織範例](../../../_images/governance/mid-market-resource-organization.png)

<span data-ttu-id="d7a11-126">這些模式會提供成長的空間，而不會不必要地使階層複雜化。</span><span class="sxs-lookup"><span data-stu-id="d7a11-126">These patterns provide room for growth without complicating the hierarchy unnecessarily.</span></span>

[!INCLUDE [governance-of-resources](../../../../../includes/cloud-adoption/governance/governance-of-resources.md)]

## <a name="governance-evolutions"></a><span data-ttu-id="d7a11-127">治理演進</span><span class="sxs-lookup"><span data-stu-id="d7a11-127">Governance evolutions</span></span>

<span data-ttu-id="d7a11-128">一旦部署此 MVP 之後，其他治理層就可以快速地合併到環境中。</span><span class="sxs-lookup"><span data-stu-id="d7a11-128">Once this MVP has been deployed, additional layers of governance can be quickly incorporated into the environment.</span></span> <span data-ttu-id="d7a11-129">以下是一些推演 MVP 以符合特定業務需求的方式：</span><span class="sxs-lookup"><span data-stu-id="d7a11-129">Here are some ways to evolve the MVP to meet specific business needs:</span></span>

- [<span data-ttu-id="d7a11-130">受保護資料的安全性基準</span><span class="sxs-lookup"><span data-stu-id="d7a11-130">Security Baseline for protected data</span></span>](./security-baseline-evolution.md)
- [<span data-ttu-id="d7a11-131">任務關鍵性應用程式的資源設定</span><span class="sxs-lookup"><span data-stu-id="d7a11-131">Resource configurations for mission-critical applications</span></span>](./resource-consistency-evolution.md)
- [<span data-ttu-id="d7a11-132">成本管理控制</span><span class="sxs-lookup"><span data-stu-id="d7a11-132">Controls for Cost Management</span></span>](./cost-management-evolution.md)
- [<span data-ttu-id="d7a11-133">多重雲端演進的控制</span><span class="sxs-lookup"><span data-stu-id="d7a11-133">Controls for multi-cloud evolution</span></span>](./multi-cloud-evolution.md)

<!-- markdownlint-disable MD026 -->

## <a name="what-does-this-best-practice-do"></a><span data-ttu-id="d7a11-134">這個最佳做法有哪些用途？</span><span class="sxs-lookup"><span data-stu-id="d7a11-134">What does this best practice do?</span></span>

<span data-ttu-id="d7a11-135">在 MVP 中，從[部署加速](../../deployment-acceleration/overview.md)專業領域建立做法和工具是為了快速套用公司原則。</span><span class="sxs-lookup"><span data-stu-id="d7a11-135">In the MVP, practices and tools from the [Deployment Acceleration](../../deployment-acceleration/overview.md) discipline are established to quickly apply corporate policy.</span></span> <span data-ttu-id="d7a11-136">特別是，MVP 會使用 Azure 藍圖、Azure 原則以及 Azure 管理群組套用幾個基本的公司原則，如這個虛構公司的敘述中所定義。</span><span class="sxs-lookup"><span data-stu-id="d7a11-136">In particular, the MVP uses Azure Blueprints, Azure Policy, and Azure management groups to apply a few basic corporate policies, as defined in the narrative for this fictional company.</span></span> <span data-ttu-id="d7a11-137">這些公司原則是使用 Resource Manager 範本與 Azure 原則套用的，可建立非常小的身分識別和安全性基準。</span><span class="sxs-lookup"><span data-stu-id="d7a11-137">Those corporate policies are applied using Resource Manager templates and Azure policies to establish a very small baseline for identity and security.</span></span>

![漸進式治理 MVP 的範例](../../../_images/governance/governance-mvp.png)

## <a name="evolving-the-best-practice"></a><span data-ttu-id="d7a11-139">推演最佳做法</span><span class="sxs-lookup"><span data-stu-id="d7a11-139">Evolving the best practice</span></span>

<span data-ttu-id="d7a11-140">經過一段時間之後，這個治理 MVP 將用於推演治理做法。</span><span class="sxs-lookup"><span data-stu-id="d7a11-140">Over time, this governance MVP will be used to evolve the governance practices.</span></span> <span data-ttu-id="d7a11-141">隨著採用率提高，業務風險也會增加。</span><span class="sxs-lookup"><span data-stu-id="d7a11-141">As adoption advances, business risk grows.</span></span> <span data-ttu-id="d7a11-142">CAF 治理模型中的各種專業領域將持續推演以降低這些風險。</span><span class="sxs-lookup"><span data-stu-id="d7a11-142">Various disciplines within the CAF governance model will evolve to mitigate those risks.</span></span> <span data-ttu-id="d7a11-143">本系列的後續文章將討論影響虛構公司的公司原則演變。</span><span class="sxs-lookup"><span data-stu-id="d7a11-143">Later articles in this series discuss the evolution of corporate policy affecting the fictional company.</span></span> <span data-ttu-id="d7a11-144">這些演變會跨三個專業領域進行：</span><span class="sxs-lookup"><span data-stu-id="d7a11-144">These evolutions happen across three disciplines:</span></span>

- <span data-ttu-id="d7a11-145">成本管理 (隨著採用的規模)。</span><span class="sxs-lookup"><span data-stu-id="d7a11-145">Cost Management, as adoption scales.</span></span>
- <span data-ttu-id="d7a11-146">安全性基準 (部署受保護的資料時)。</span><span class="sxs-lookup"><span data-stu-id="d7a11-146">Security Baseline, as protected data is deployed.</span></span>
- <span data-ttu-id="d7a11-147">資源一致性 (IT 操作開始支援任務關鍵性工作負載時)。</span><span class="sxs-lookup"><span data-stu-id="d7a11-147">Resource Consistency, as IT Operations begins supporting mission-critical workloads.</span></span>

![累加式治理 MVP 的範例](../../../_images/governance/governance-evolution.png)

## <a name="next-steps"></a><span data-ttu-id="d7a11-149">後續步驟</span><span class="sxs-lookup"><span data-stu-id="d7a11-149">Next steps</span></span>

<span data-ttu-id="d7a11-150">既然您已經熟悉治理 MVP，並且了解後續的治理演進，請閱讀對於其他內容的支持性敘述。</span><span class="sxs-lookup"><span data-stu-id="d7a11-150">Now that you’re familiar with the governance MVP and have an idea of the governance evolutions to follow, read the supporting narrative for additional context.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="d7a11-151">閱讀支持性敘述</span><span class="sxs-lookup"><span data-stu-id="d7a11-151">Read the supporting narrative</span></span>](./narrative.md)

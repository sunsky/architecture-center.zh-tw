---
title: CAF：成本管理範例原則聲明
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 1/4/2019
description: 成本管理範例原則聲明
author: BrianBlanchard
ms.openlocfilehash: 1c8b94ae5285fa26cdf9760892beaced2487af8b
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901112"
---
# <a name="cost-management-sample-policy-statements"></a><span data-ttu-id="832df-103">成本管理範例原則聲明</span><span class="sxs-lookup"><span data-stu-id="832df-103">Cost Management sample policy statements</span></span>

<span data-ttu-id="832df-104">個別的雲端原則聲明是用來解決在風險評估流程中識別出之特定風險的方針。</span><span class="sxs-lookup"><span data-stu-id="832df-104">Individual cloud policy statements are guidelines for addressing specific risks identified during your risk assessment process.</span></span> <span data-ttu-id="832df-105">這些聲明應該會提供風險的簡明摘要，以及如何處理它們的方案。</span><span class="sxs-lookup"><span data-stu-id="832df-105">These statements should provide a concise summary of risks and plans to deal with them.</span></span> <span data-ttu-id="832df-106">每個聲明定義都應該包含下列資訊：</span><span class="sxs-lookup"><span data-stu-id="832df-106">Each statement definition should include these pieces of information:</span></span>

- <span data-ttu-id="832df-107">業務風險：此原則將解決的風險摘要。</span><span class="sxs-lookup"><span data-stu-id="832df-107">Business risk - A summary of the risk this policy will address.</span></span>
- <span data-ttu-id="832df-108">原則聲明：明確地摘要說明原則需求。</span><span class="sxs-lookup"><span data-stu-id="832df-108">Policy statement - A clear summary explanation of the policy requirements.</span></span>
- <span data-ttu-id="832df-109">設計選項：IT 小組與開發人員可以在實作原則時使用之可操作的建議、規格或其他指引。</span><span class="sxs-lookup"><span data-stu-id="832df-109">Design options - Actionable recommendations, specifications, or other guidance that IT teams and developers can use when implementing the policy.</span></span>

<span data-ttu-id="832df-110">下列範例原則聲明會解決一些與成本相關的常見業務風險，並提供來做為您撰寫實際原則聲明的草稿以滿足自己的組織需求時所參考的範例。</span><span class="sxs-lookup"><span data-stu-id="832df-110">The following sample policy statements address a number of common cost-related business risks, and are provided as examples for you to reference when drafting actual policy statements addressing your own organization's needs.</span></span> <span data-ttu-id="832df-111">這些範例並非禁止使用，而且可能提供數個原則選項來處理任何已識別出的單一風險。</span><span class="sxs-lookup"><span data-stu-id="832df-111">These examples are not meant to be proscriptive, and there are potentially several policy options for dealing with any single identified risk.</span></span> <span data-ttu-id="832df-112">與業務和 IT 小組密切合作，來識別適用於特定成本相關風險的最佳原則解決方案。</span><span class="sxs-lookup"><span data-stu-id="832df-112">Work closely with business and IT teams to identify the best policy solutions for your particular cost-related risks.</span></span>  

## <a name="future-proofing"></a><span data-ttu-id="832df-113">與未來技術的兼容性</span><span class="sxs-lookup"><span data-stu-id="832df-113">Future-proofing</span></span>

<span data-ttu-id="832df-114">**業務風險**。</span><span class="sxs-lookup"><span data-stu-id="832df-114">**Business risk**.</span></span> <span data-ttu-id="832df-115">目前不保證治理小組會投資成本管理專業領域的準則。</span><span class="sxs-lookup"><span data-stu-id="832df-115">Current criteria that don't warrant an investment in a Cost Management discipline from the governance team.</span></span> <span data-ttu-id="832df-116">不過，您可以預期未來將有這類投資。</span><span class="sxs-lookup"><span data-stu-id="832df-116">However, you anticipate such an investment in the future.</span></span>

<span data-ttu-id="832df-117">**原則聲明**。</span><span class="sxs-lookup"><span data-stu-id="832df-117">**Policy statement**.</span></span> <span data-ttu-id="832df-118">您應該將部署至雲端的所有資產都關聯至一個計費單位 (應用程式/工作負載)，並符合命名標準。</span><span class="sxs-lookup"><span data-stu-id="832df-118">You should associate all assets deployed to the cloud with a billing unit, application/workload, and meet naming standards.</span></span> <span data-ttu-id="832df-119">此原則將確保未來的成本管理成果將會生效。</span><span class="sxs-lookup"><span data-stu-id="832df-119">This policy will ensure that future Cost Management efforts will be effective.</span></span>

<span data-ttu-id="832df-120">**設計選項**。</span><span class="sxs-lookup"><span data-stu-id="832df-120">**Design options**.</span></span> <span data-ttu-id="832df-121">如需建立與未來技術的兼容性基礎相關資訊，請參閱 CAF 指引隨附之[可操作的設計指南](../journeys/overview.md)中，與建立治理 MVP 相關的討論。</span><span class="sxs-lookup"><span data-stu-id="832df-121">For information on establishing a future-proof foundation, see the discussions related to creating a governance MVP in the [actionable design guides](../journeys/overview.md) included as part of the CAF guidance.</span></span>

## <a name="budget-overruns"></a><span data-ttu-id="832df-122">預算超支</span><span class="sxs-lookup"><span data-stu-id="832df-122">Budget overruns</span></span>

<span data-ttu-id="832df-123">**業務風險：** 自助服務部署會造成超支風險。</span><span class="sxs-lookup"><span data-stu-id="832df-123">**Business risk:** Self-service deployment creates a risk of overspending.</span></span>

<span data-ttu-id="832df-124">**原則聲明：** 所有雲端部署都必須配置給已核准預算的計費單位，以及適用於預算限制的機制。</span><span class="sxs-lookup"><span data-stu-id="832df-124">**Policy statement:** Any cloud deployment must be allocated to a billing unit with approved budget and a mechanism for budgetary limits.</span></span>

<span data-ttu-id="832df-125">**設計選項：** 在 Azure 中，可以使用 [Azure 成本管理](/azure/cost-management/manage-budgets)來控制預算。</span><span class="sxs-lookup"><span data-stu-id="832df-125">**Design options:** In Azure, budget can be controlled with [Azure Cost Management](/azure/cost-management/manage-budgets)</span></span>

## <a name="underutilization"></a><span data-ttu-id="832df-126">使用量過低</span><span class="sxs-lookup"><span data-stu-id="832df-126">Underutilization</span></span>

<span data-ttu-id="832df-127">**業務風險：** 公司已針對雲端服務進行預付，或已承諾每年會花費特定金額。</span><span class="sxs-lookup"><span data-stu-id="832df-127">**Business risk:** The company has prepaid for cloud services or has made an annual commitment to spend a specific amount.</span></span> <span data-ttu-id="832df-128">這樣會有將用不到已達成協議之金額的風險，因而導致投資損失。</span><span class="sxs-lookup"><span data-stu-id="832df-128">There is a risk that the agreed upon amount won't be used, resulting in a lost investment.</span></span>

<span data-ttu-id="832df-129">**原則聲明：** 每個具有已配置雲端預算的計費單位每年都將召開會議來設定預算、每季都會調整預算，而且每月都會配置時間來檢閱已規劃與實際的支出。</span><span class="sxs-lookup"><span data-stu-id="832df-129">**Policy statement:** Each billing unit with an allocated cloud budget will meet annually to set budgets, quarterly to adjust budgets, and monthly to allocate time for reviewing planned versus actual spending.</span></span> <span data-ttu-id="832df-130">每月都要與計費單位的負責人討論任何大於 20% 的偏差。</span><span class="sxs-lookup"><span data-stu-id="832df-130">Discuss any deviations greater than 20% with the billing unit leader monthly.</span></span> <span data-ttu-id="832df-131">基於追蹤目的，請將所有資產都指派給一個計費單位。</span><span class="sxs-lookup"><span data-stu-id="832df-131">For tracking purposes, assign all assets to a billing unit.</span></span>

<span data-ttu-id="832df-132">**設計選項：**</span><span class="sxs-lookup"><span data-stu-id="832df-132">**Design options:**</span></span>

- <span data-ttu-id="832df-133">在 Azure 中，已規劃與實際的支出均可透過 [Azure 成本管理](/azure/cost-management/quick-acm-cost-analysis)來管理。</span><span class="sxs-lookup"><span data-stu-id="832df-133">In Azure, planned versus actual spending can be managed via [Azure Cost Management](/azure/cost-management/quick-acm-cost-analysis)</span></span>
- <span data-ttu-id="832df-134">有數個選項可讓計費單位用來群組資源。</span><span class="sxs-lookup"><span data-stu-id="832df-134">There are several options for grouping resources by billing unit.</span></span> <span data-ttu-id="832df-135">在 Azure 中，應該與治理小組一同選擇[資源一致性模型](../../decision-guides/resource-consistency/overview.md)並套用至所有資產。</span><span class="sxs-lookup"><span data-stu-id="832df-135">In Azure, a [resource consistency model](../../decision-guides/resource-consistency/overview.md) should be chosen in conjunction with the governance team and applied to all assets.</span></span>

## <a name="overprovisioned-assets"></a><span data-ttu-id="832df-136">過度佈建的資產</span><span class="sxs-lookup"><span data-stu-id="832df-136">Overprovisioned assets</span></span>

<span data-ttu-id="832df-137">**業務風險：** 在傳統內部部署資料中心，常見的做法是部署已額外規劃容量的資產，以便在長遠的未來中隨之增長。</span><span class="sxs-lookup"><span data-stu-id="832df-137">**Business risk:** In traditional on-premises datacenters, it is common practice to deploy assets with extra capacity planning for growth in the distant future.</span></span> <span data-ttu-id="832df-138">相較於傳統設備，雲端可以更快速地進行調整。</span><span class="sxs-lookup"><span data-stu-id="832df-138">The cloud can scale more quickly than traditional equipment.</span></span> <span data-ttu-id="832df-139">雲端中的資產也會根據技術容量來計費。</span><span class="sxs-lookup"><span data-stu-id="832df-139">Assets in the cloud are also priced based on the technical capacity.</span></span> <span data-ttu-id="832df-140">對於舊的內部部署做法，會有以人為方式誇大雲端支出的風險。</span><span class="sxs-lookup"><span data-stu-id="832df-140">There is a risk of the old on-premises practice artificially inflating cloud spending.</span></span>

<span data-ttu-id="832df-141">**原則聲明：** 所有部署至雲端的資產都必須在計劃中註冊，此計劃可以監視使用量，並報告任何超過使用量 50% 的容量。</span><span class="sxs-lookup"><span data-stu-id="832df-141">**Policy statement:** Any asset deployed to the cloud must be enrolled in a program that can monitor utilization and report any capacity in excess of 50% of utilization.</span></span> <span data-ttu-id="832df-142">所有部署至雲端的資產都必須以邏輯方式加以群組或標記，如此一來，治理小組成員就能讓工作負載擁有者參與關於過度佈建資產的任何最佳化。</span><span class="sxs-lookup"><span data-stu-id="832df-142">Any asset deployed to the cloud must be grouped or tagged in a logical manner, so governance team members can engage the workload owner regarding any optimization of overprovisioned assets.</span></span>

<span data-ttu-id="832df-143">**設計選項：**</span><span class="sxs-lookup"><span data-stu-id="832df-143">**Design options:**</span></span>

- <span data-ttu-id="832df-144">在 Azure 中，[Azure Advisor](/azure/advisor/advisor-cost-recommendations) 可以提供最佳化建議。</span><span class="sxs-lookup"><span data-stu-id="832df-144">In Azure, [Azure Advisor](/azure/advisor/advisor-cost-recommendations) can provide optimization recommendations.</span></span>
- <span data-ttu-id="832df-145">有數個選項可讓計費單位用來群組資源。</span><span class="sxs-lookup"><span data-stu-id="832df-145">There are several options for grouping resources by billing unit.</span></span> <span data-ttu-id="832df-146">在 Azure 中，應該與治理小組一同選擇[資源一致性模型](../../decision-guides/resource-consistency/overview.md)並套用至所有資產。</span><span class="sxs-lookup"><span data-stu-id="832df-146">In Azure, a [resource consistency model](../../decision-guides/resource-consistency/overview.md) should be chosen in conjunction with the governance team and applied to all assets.</span></span>

## <a name="overoptimization"></a><span data-ttu-id="832df-147">過度最佳化</span><span class="sxs-lookup"><span data-stu-id="832df-147">Overoptimization</span></span>

<span data-ttu-id="832df-148">**業務風險：** 有效的成本管理實際上可能會導致新風險。</span><span class="sxs-lookup"><span data-stu-id="832df-148">**Business risk:** Effective cost management can actually create new risks.</span></span> <span data-ttu-id="832df-149">將支出最佳化，正好與系統效能相反。</span><span class="sxs-lookup"><span data-stu-id="832df-149">Optimization of spending is inverse to system performance.</span></span> <span data-ttu-id="832df-150">降低成本時，會造成過度削減支出，而產生不良使用者體驗的風險。</span><span class="sxs-lookup"><span data-stu-id="832df-150">When reducing costs, there is a risk of overtightening spending and producing poor user experiences.</span></span>

<span data-ttu-id="832df-151">**原則聲明：** 所有直接影響客戶體驗的資產都必須透過群組或標記來識別。</span><span class="sxs-lookup"><span data-stu-id="832df-151">**Policy statement:** Any asset that directly affects customer experiences must be identified through grouping or tagging.</span></span> <span data-ttu-id="832df-152">將影響客戶體驗的任何資產最佳化之前，雲端治理小組必須根據大於或等於 90 天的使用量趨勢來調整最佳化。</span><span class="sxs-lookup"><span data-stu-id="832df-152">Before optimizing any asset that affects customer experience, the Cloud Governance team must adjust optimization based on no fewer than 90 days of utilization trends.</span></span> <span data-ttu-id="832df-153">記載在將資產最佳化時所考慮的任何季節性或事件驅動的高載。</span><span class="sxs-lookup"><span data-stu-id="832df-153">Document any seasonal or event driven bursts considered when optimizing assets.</span></span>

<span data-ttu-id="832df-154">**設計選項：**</span><span class="sxs-lookup"><span data-stu-id="832df-154">**Design options:**</span></span>

- <span data-ttu-id="832df-155">在 Azure 中，[Azure 監視器的深入解析功能](/azure/azure-monitor/insights/vminsights-performance)有助於分析系統使用量。</span><span class="sxs-lookup"><span data-stu-id="832df-155">In Azure, [Azure Monitor's insights features](/azure/azure-monitor/insights/vminsights-performance) can help with analysis of system utilization.</span></span>
- <span data-ttu-id="832df-156">有數個選項可根據角色來群組和標記資源。</span><span class="sxs-lookup"><span data-stu-id="832df-156">There are several options for grouping and tagging resources based on roles.</span></span> <span data-ttu-id="832df-157">在 Azure 中，您應該與治理小組一同選擇[資源一致性模型](../../decision-guides/resource-consistency/overview.md)，並將此項套用至所有資產。</span><span class="sxs-lookup"><span data-stu-id="832df-157">In Azure, you should choose a [resource consistency model](../../decision-guides/resource-consistency/overview.md) in conjunction with the governance team and apply this to all assets.</span></span>

## <a name="next-steps"></a><span data-ttu-id="832df-158">後續步驟</span><span class="sxs-lookup"><span data-stu-id="832df-158">Next steps</span></span>

<span data-ttu-id="832df-159">使用本文提及的範例做為起點，以開發與您雲端採用方案保持一致的原則來解決特定的業務風險。</span><span class="sxs-lookup"><span data-stu-id="832df-159">Use the samples mentioned in this article as a starting point to develop policies that address specific business risks that align with your cloud adoption plans.</span></span>

<span data-ttu-id="832df-160">若要開始自行開發與成本管理相關的自訂原則聲明，請下載[成本管理範本](template.md)。</span><span class="sxs-lookup"><span data-stu-id="832df-160">To begin developing your own custom policy statements related to Cost Management, download the [Cost Management template](template.md).</span></span>

<span data-ttu-id="832df-161">若要加速採用這個專業領域，請選擇最密切配合您的環境且[可操作的治理旅程](../journeys/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="832df-161">To accelerate adoption of this discipline, choose the [Actionable Governance Journey](../journeys/overview.md) that most closely aligns with your environment.</span></span> <span data-ttu-id="832df-162">然後修改設計，以納入您特定的公司原則決策。</span><span class="sxs-lookup"><span data-stu-id="832df-162">Then modify the design to incorporate your specific corporate policy decisions.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="832df-163">可操作的治理旅程</span><span class="sxs-lookup"><span data-stu-id="832df-163">Actionable Governance Journeys</span></span>](../journeys/overview.md)

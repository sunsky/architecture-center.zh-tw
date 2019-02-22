---
title: CAF：成本管理專業領域改進
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 成本管理專業領域改進
author: BrianBlanchard
ms.openlocfilehash: 34975d195a95b1a85ada96efe8c76a6138385ec1
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901164"
---
# <a name="cost-management-discipline-improvement"></a><span data-ttu-id="9f802-103">成本管理專業領域改進</span><span class="sxs-lookup"><span data-stu-id="9f802-103">Cost Management discipline improvement</span></span>

<span data-ttu-id="9f802-104">成本管理專業領域會嘗試解決與在裝載雲端式工作負載時所產生費用相關的核心業務風險。</span><span class="sxs-lookup"><span data-stu-id="9f802-104">The Cost Management discipline attempts to address core business risks related to expenses incurred when hosting cloud-based workloads.</span></span> <span data-ttu-id="9f802-105">在這五個雲端管治理專業領域中，成本管理會涉及控制雲端資源的成本和使用量，而且目標為建立和維護已規劃的成本週期。</span><span class="sxs-lookup"><span data-stu-id="9f802-105">Within the five disciplines of Cloud Governance, Cost Management is involved in controlling cost and usage of cloud resources with the goal of creating and maintaining a planned cost cycle.</span></span>

<span data-ttu-id="9f802-106">本文將概述貴公司可執行來開發成本管理專業領域並使其臻至成熟的潛在工作。</span><span class="sxs-lookup"><span data-stu-id="9f802-106">This article outlines potential tasks your company perform to develop and mature your Cost Management discipline.</span></span> <span data-ttu-id="9f802-107">這些工作可以細分為實作雲端解決方案的規劃、建置、採用及操作階段，接著反覆執行以允許開發[雲端治理的累加方法](../journeys/overview.md#an-incremental-approach-to-cloud-governance)。</span><span class="sxs-lookup"><span data-stu-id="9f802-107">These tasks can be broken down into planning, building, adopting, and operating phases of implementing a cloud solution, which are then iterated on allowing the development of an [incremental approach to cloud governance](../journeys/overview.md#an-incremental-approach-to-cloud-governance).</span></span>

![四個採用階段](../../_images/adoption-phases.png)

<span data-ttu-id="9f802-109">*圖 1.雲端治理累加方法的採用階段。*</span><span class="sxs-lookup"><span data-stu-id="9f802-109">*Figure 1. Adoption phases of the incremental approach to cloud governance.*</span></span>

<span data-ttu-id="9f802-110">沒有任何單一文件可以滿足所有企業需求。</span><span class="sxs-lookup"><span data-stu-id="9f802-110">No single document can account for the requirements of all businesses.</span></span> <span data-ttu-id="9f802-111">因此，本文將針對治理成熟流程的每個階段，概述建議的最小和潛在範例活動。</span><span class="sxs-lookup"><span data-stu-id="9f802-111">As such, this article outlines suggested minimum and potential example activities for each phase of the governance maturation process.</span></span> <span data-ttu-id="9f802-112">這些活動的初始目的是協助您建置[原則 MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance)，並建立累加式原則演進的架構。</span><span class="sxs-lookup"><span data-stu-id="9f802-112">The initial objective of these activities is to help you build a [Policy MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance) and establish a framework for incremental policy evolution.</span></span> <span data-ttu-id="9f802-113">您的雲端治理小組將需決定要對這些活動進行多少投資，以改進您的成本管理治理功能。</span><span class="sxs-lookup"><span data-stu-id="9f802-113">Your Cloud Governance team will need to decide how much to invest in these activities to improve your Cost Management governance capabilities.</span></span>

> [!CAUTION]
> <span data-ttu-id="9f802-114">本文中概述的最小或潛在活動都不會與特定的公司原則或協力廠商合規性需求保持一致。</span><span class="sxs-lookup"><span data-stu-id="9f802-114">Neither the minimum or potential activities outlined in this article are aligned to specific corporate policies or third party compliance requirements.</span></span> <span data-ttu-id="9f802-115">本指引旨在協助促成交談，從而使這兩個需求與雲端治理模型保持一致。</span><span class="sxs-lookup"><span data-stu-id="9f802-115">This guidance is designed to help facilitate the conversations that will lead to alignment of both requirements with a cloud governance model.</span></span>

## <a name="planning-and-readiness"></a><span data-ttu-id="9f802-116">規劃和整備</span><span class="sxs-lookup"><span data-stu-id="9f802-116">Planning and readiness</span></span>

<span data-ttu-id="9f802-117">這個階段的治理成熟度可消彌業務成果與可操作之策略間的鴻溝。</span><span class="sxs-lookup"><span data-stu-id="9f802-117">This phase of governance maturity bridges the divide between business outcomes and actionable strategies.</span></span> <span data-ttu-id="9f802-118">在此流程中，領導小組會定義特定的計量、將這些計量對應至數位資產，並開始規劃整體移轉工作。</span><span class="sxs-lookup"><span data-stu-id="9f802-118">During this process, the leadership team defines specific metrics, maps those metrics to the digital estate, and begins planning the overall migration effort.</span></span>

<span data-ttu-id="9f802-119">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="9f802-119">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="9f802-120">評估您的[成本管理工具鏈](toolchain.md)選項。</span><span class="sxs-lookup"><span data-stu-id="9f802-120">Evaluate your [Cost Management toolchain](toolchain.md) options.</span></span>
* <span data-ttu-id="9f802-121">開發架構方針文件的草稿，並散發給重要的專案關係人。</span><span class="sxs-lookup"><span data-stu-id="9f802-121">Develop a draft Architecture Guidelines document and distribute to key stakeholders.</span></span>
* <span data-ttu-id="9f802-122">教育並涵蓋受到開發架構方針影響的人員和小組。</span><span class="sxs-lookup"><span data-stu-id="9f802-122">Educate and involve the people and teams affected by the development of Architecture Guidelines.</span></span>

<span data-ttu-id="9f802-123">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="9f802-123">**Potential activities:**</span></span>

* <span data-ttu-id="9f802-124">確定支援您雲端策略商業論證的預算決策。</span><span class="sxs-lookup"><span data-stu-id="9f802-124">Ensure budgetary decisions that support the business justification for your cloud strategy.</span></span>
* <span data-ttu-id="9f802-125">驗證您用來報告成功配置資金的學習計量。</span><span class="sxs-lookup"><span data-stu-id="9f802-125">Validate learning metrics that you use to report on the successful allocation of funding.</span></span>
* <span data-ttu-id="9f802-126">了解影響雲端成本處理方式的預期雲端帳戶處理模型。</span><span class="sxs-lookup"><span data-stu-id="9f802-126">Understand the desired cloud accounting model that affects how cloud costs should be accounted for.</span></span>
* <span data-ttu-id="9f802-127">熟悉數位資產方案，並驗證準確的成本期望。</span><span class="sxs-lookup"><span data-stu-id="9f802-127">Become familiar with the digital estate plan and validate accurate costing expectations.</span></span>
* <span data-ttu-id="9f802-128">評估購買選項，以判斷是否更適合「預付方案」，或是購買 Enterprise 合約進行預先承諾。</span><span class="sxs-lookup"><span data-stu-id="9f802-128">Evaluate buying options to determine if it's better to "pay as you go" or to make a precommitment by purchasing an Enterprise Agreement.</span></span>
* <span data-ttu-id="9f802-129">使業務目標和規劃的預算保持一致，並視需要調整預算方案。</span><span class="sxs-lookup"><span data-stu-id="9f802-129">Align business goals with planned budgets and adjust budgetary plans as necessary.</span></span>
* <span data-ttu-id="9f802-130">開發目標和預算報告機制，以便在每個成本週期結束時通知技術和業務的專案關係人。</span><span class="sxs-lookup"><span data-stu-id="9f802-130">Develop a goals and budget reporting mechanism to notify technical and business stakeholders at the end of each cost cycle.</span></span>

## <a name="build-and-pre-deployment"></a><span data-ttu-id="9f802-131">建置和部署前</span><span class="sxs-lookup"><span data-stu-id="9f802-131">Build and pre-deployment</span></span>

<span data-ttu-id="9f802-132">成功移轉環境需要一些技術性和非技術性的必要條件。</span><span class="sxs-lookup"><span data-stu-id="9f802-132">A number of technical and nontechnical prerequisites are required to successfully migrate an environment.</span></span> <span data-ttu-id="9f802-133">此流程著重於可繼續進行移轉的決策、整備和核心基礎結構。</span><span class="sxs-lookup"><span data-stu-id="9f802-133">This process focuses on the decisions, readiness, and core infrastructure that proceeds a migration.</span></span>

<span data-ttu-id="9f802-134">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="9f802-134">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="9f802-135">在部署前階段中推出，以實作您的[成本管理工具鏈](toolchain.md)。</span><span class="sxs-lookup"><span data-stu-id="9f802-135">Implement your [Cost Management toolchain](toolchain.md) by rolling out in a pre-deployment phase.</span></span>
* <span data-ttu-id="9f802-136">更新架構方針文件，並散發給重要的專案關係人。</span><span class="sxs-lookup"><span data-stu-id="9f802-136">Update the Architecture Guidelines document and distribute to key stakeholders.</span></span>
* <span data-ttu-id="9f802-137">開發教育性資料和文件、認知溝通、獎勵和其他計畫，以協助試用產品的使用者採用。</span><span class="sxs-lookup"><span data-stu-id="9f802-137">Develop educational materials and documentation, awareness communications, incentives, and other programs to help drive user adoption.</span></span>
* <span data-ttu-id="9f802-138">決定您的購買需求是否要與預算和目標保持一致。</span><span class="sxs-lookup"><span data-stu-id="9f802-138">Determine if your purchase requirements align with your budgets and goals.</span></span>

<span data-ttu-id="9f802-139">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="9f802-139">**Potential activities:**</span></span>

* <span data-ttu-id="9f802-140">使您的預算方案與[訂用帳戶策略](../../decision-guides/subscriptions/overview.md)保持一致，此策略會定義您的核心擁有權模型。</span><span class="sxs-lookup"><span data-stu-id="9f802-140">Align your budgetary plans with the [Subscription Strategy](../../decision-guides/subscriptions/overview.md) that defines your core ownership model.</span></span>
* <span data-ttu-id="9f802-141">隨著時間運用[資源一致性策略](../../decision-guides/resource-consistency/overview.md)以強制執行架構和成本方針。</span><span class="sxs-lookup"><span data-stu-id="9f802-141">Leverage the [Resource Consistency Strategy](../../decision-guides/resource-consistency/overview.md) to enforce architecture and cost guidelines over time.</span></span>
* <span data-ttu-id="9f802-142">判斷是否有任何會影響您採用和移轉方案的成本異常狀況。</span><span class="sxs-lookup"><span data-stu-id="9f802-142">Determine if there are any cost anomalies that affect your adoption and migration plans.</span></span>

## <a name="adopt-and-migrate"></a><span data-ttu-id="9f802-143">採用和移轉</span><span class="sxs-lookup"><span data-stu-id="9f802-143">Adopt and migrate</span></span>

<span data-ttu-id="9f802-144">移轉是一個累加式流程，著重於在現有的數位資產中，移動、測試和採用應用程式或工作負載。</span><span class="sxs-lookup"><span data-stu-id="9f802-144">Migration is an incremental process that focuses on the movement, testing, and adoption of applications or workloads in an existing digital estate.</span></span>

<span data-ttu-id="9f802-145">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="9f802-145">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="9f802-146">將您的[成本管理工具鏈](toolchain.md)從部署前環境移轉至生產環境。</span><span class="sxs-lookup"><span data-stu-id="9f802-146">Migrate your [Cost Management toolchain](toolchain.md) from pre-deployment to production.</span></span>
* <span data-ttu-id="9f802-147">更新架構方針文件，並散發給重要的專案關係人。</span><span class="sxs-lookup"><span data-stu-id="9f802-147">Update the Architecture Guidelines document and distribute to key stakeholders.</span></span>
* <span data-ttu-id="9f802-148">開發教育性資料和文件、認知溝通、獎勵和其他計畫，以協助試用產品的使用者採用。</span><span class="sxs-lookup"><span data-stu-id="9f802-148">Develop educational materials and documentation, awareness communications, incentives, and other programs to help drive user adoption.</span></span>

<span data-ttu-id="9f802-149">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="9f802-149">**Potential activities:**</span></span>

* <span data-ttu-id="9f802-150">實作您的雲端帳戶處理模型。</span><span class="sxs-lookup"><span data-stu-id="9f802-150">Implement your Cloud Accounting Model.</span></span>
* <span data-ttu-id="9f802-151">確定預算會在每次發行期間反映實際支出，並視需要調整。</span><span class="sxs-lookup"><span data-stu-id="9f802-151">Ensure that your budgets reflect your actual spending during each release and adjust as necessary.</span></span>
* <span data-ttu-id="9f802-152">監視預算方案中的變更，並且在需要額外的登出時與專案關係人進行驗證。</span><span class="sxs-lookup"><span data-stu-id="9f802-152">Monitor changes in budgetary plans and validate with stakeholders if additional sign-offs are needed.</span></span>
* <span data-ttu-id="9f802-153">更新架構方針文件的變更，以反映實際成本。</span><span class="sxs-lookup"><span data-stu-id="9f802-153">Update changes to the Architecture Guidelines document to reflect actual costs.</span></span>

## <a name="operate-and-post-implementation"></a><span data-ttu-id="9f802-154">操作和實作後</span><span class="sxs-lookup"><span data-stu-id="9f802-154">Operate and post-implementation</span></span>

<span data-ttu-id="9f802-155">轉換完成之後，治理和操作必須依存於應用程式或工作負載的自然生命週期。</span><span class="sxs-lookup"><span data-stu-id="9f802-155">Once the transformation is complete, governance and operations must live on for the natural lifecycle of an application or workload.</span></span> <span data-ttu-id="9f802-156">這個階段的治理成熟度著重於通常會在實作解決方案且轉換週期開始穩定後隨之而來的活動。</span><span class="sxs-lookup"><span data-stu-id="9f802-156">This phase of governance maturity focuses on the activities that commonly come after the solution is implemented and the transformation cycle begins to stabilize.</span></span>

<span data-ttu-id="9f802-157">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="9f802-157">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="9f802-158">根據貴組織成本管理需求的變更，自訂您的[成本管理工具鏈](toolchain.md)。</span><span class="sxs-lookup"><span data-stu-id="9f802-158">Customize your [Cost Management toolchain](toolchain.md) based on changes in your organization’s cost management needs.</span></span>
* <span data-ttu-id="9f802-159">考慮將任何通知和報告自動化，以反映實際支出。</span><span class="sxs-lookup"><span data-stu-id="9f802-159">Consider automating any notifications and reports to reflect actual spending.</span></span>
* <span data-ttu-id="9f802-160">精簡架構方針，以引導未來的採用程序。</span><span class="sxs-lookup"><span data-stu-id="9f802-160">Refine Architecture Guidelines to guide future adoption processes.</span></span>
* <span data-ttu-id="9f802-161">定期教育受影響的小組，以確保會持續遵循架構方針。</span><span class="sxs-lookup"><span data-stu-id="9f802-161">Educate affected teams on a periodic basis to ensure ongoing adherence to the Architecture Guidelines.</span></span>

<span data-ttu-id="9f802-162">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="9f802-162">**Potential activities:**</span></span>

* <span data-ttu-id="9f802-163">執行每季雲端業務檢閱，以傳達要傳遞給企業的價值與相關聯的成本。</span><span class="sxs-lookup"><span data-stu-id="9f802-163">Execute a quarterly cloud business review to communicate value delivered to the business and associated costs.</span></span>
* <span data-ttu-id="9f802-164">每季調整方案，以反映實際支出的變更。</span><span class="sxs-lookup"><span data-stu-id="9f802-164">Adjust plans quarterly to reflect changes to actual spending.</span></span>
* <span data-ttu-id="9f802-165">判斷財務會與商業單位訂用帳戶的損益保持一致。</span><span class="sxs-lookup"><span data-stu-id="9f802-165">Determine financial alignment to P&Ls for business unit subscriptions.</span></span>
* <span data-ttu-id="9f802-166">每月分析專案關係人價值和成本報告方法。</span><span class="sxs-lookup"><span data-stu-id="9f802-166">Analyze stakeholder value and cost reporting methods on a monthly basis.</span></span>
* <span data-ttu-id="9f802-167">修復未充分使用的資產，並判斷其是否值得繼續。</span><span class="sxs-lookup"><span data-stu-id="9f802-167">Remediate underused assets and determine if they're worth continuing.</span></span>
* <span data-ttu-id="9f802-168">偵測方案與實際支出之間的不一致與異常狀況。</span><span class="sxs-lookup"><span data-stu-id="9f802-168">Detect misalignments and anomalies between the plan and actual spending.</span></span>
* <span data-ttu-id="9f802-169">協助雲端採用小組和雲端策略小組了解並解決這些異常狀況。</span><span class="sxs-lookup"><span data-stu-id="9f802-169">Assist the cloud adoption teams and the Cloud Strategy team with understanding and resolving these anomalies.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9f802-170">後續步驟</span><span class="sxs-lookup"><span data-stu-id="9f802-170">Next steps</span></span>

<span data-ttu-id="9f802-171">既然您已了解雲端身分識別治理的概念，請檢查[成本管理工具鏈](toolchain.md)，來識別您在 Azure 平台上開發成本管理治理專業領域時所需的 Azure 工具和功能。</span><span class="sxs-lookup"><span data-stu-id="9f802-171">Now that you understand the concept of cloud identity governance, examine the [Cost Management toolchain](toolchain.md) to identify Azure tools and features that you'll need when developing the Cost Management governance discipline on the Azure platform.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="9f802-172">適用於 Azure 成本管理工具鏈</span><span class="sxs-lookup"><span data-stu-id="9f802-172">Cost Management toolchain for Azure</span></span>](toolchain.md)
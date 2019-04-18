---
title: CAF：建立雲端轉換的財務模型
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
description: 如何建立雲端轉換的財務模型。
author: BrianBlanchard
ms.date: 12/10/2018
ms.topic: guide
ms.openlocfilehash: 1f3ed8a84b84ba577ad5e5db8b1becd318dc04a3
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068866"
---
# <a name="create-a-financial-model-for-cloud-transformation"></a><span data-ttu-id="5356d-103">建立雲端轉換的財務模型</span><span class="sxs-lookup"><span data-stu-id="5356d-103">Create a financial model for cloud transformation</span></span>

<span data-ttu-id="5356d-104">要建立財務模型以準確呈現任何雲端轉換的完整商業價值，可能是很複雜的作業。</span><span class="sxs-lookup"><span data-stu-id="5356d-104">Creating a financial model that accurately represents the full business value of any cloud transformation can be complicated.</span></span> <span data-ttu-id="5356d-105">不同的組織會有不同的財務模型和商業論證。</span><span class="sxs-lookup"><span data-stu-id="5356d-105">Financial models and business justifications tend to be different from one organization to the next.</span></span> <span data-ttu-id="5356d-106">本文將建立某些公式，並指出在建立財務模型時常會疏漏的若干要點。</span><span class="sxs-lookup"><span data-stu-id="5356d-106">This article establishes some formulas and points out a few things that are commonly missed when creating a financial model.</span></span>

## <a name="return-on-investment-roi"></a><span data-ttu-id="5356d-107">投資報酬率 (ROI)</span><span class="sxs-lookup"><span data-stu-id="5356d-107">Return on investment (ROI)</span></span>

<span data-ttu-id="5356d-108">投資報酬率 (ROI) 常是高層主管或董事的重要考量依據。</span><span class="sxs-lookup"><span data-stu-id="5356d-108">Return on investment (ROI) is often an important criteria with the C-Suite or the board.</span></span> <span data-ttu-id="5356d-109">投資報酬率可用來比較投入有限資本資源的不同方式。</span><span class="sxs-lookup"><span data-stu-id="5356d-109">ROI is used to compare different ways to invest limited capital resources.</span></span> <span data-ttu-id="5356d-110">投資報酬率的公式相當簡單。</span><span class="sxs-lookup"><span data-stu-id="5356d-110">The formula for ROI is fairly simple.</span></span> <span data-ttu-id="5356d-111">但建立各項公式輸入所需的詳細資料可能不易取得。</span><span class="sxs-lookup"><span data-stu-id="5356d-111">The details required to create each input to the formula may not be as simple.</span></span> <span data-ttu-id="5356d-112">基本上，ROI 會是初始投資所產生的回收金額。</span><span class="sxs-lookup"><span data-stu-id="5356d-112">Essentially, ROI is the amount of return produced from an initial investment.</span></span> <span data-ttu-id="5356d-113">此值通常以百分比表示：</span><span class="sxs-lookup"><span data-stu-id="5356d-113">Usually it is represented as a percentage:</span></span>

![投資報酬率 (ROI) 等於 (投資所得 – 投資成本)/投資成本](../_images/formula-roi.png)

<!-- markdownlint-disable MD036 -->
<!--*ROI = (Gain from Investment &minus; Initial Investment) / Initial Investment*-->
<!-- markdownlint-enable MD036 -->

<span data-ttu-id="5356d-115">在下一節中，我們將逐步說明計算初始投資和投資所得 (盈餘) 所需的資料。</span><span class="sxs-lookup"><span data-stu-id="5356d-115">In the next sections, we will walk through the data needed to calculate the initial investment and the gain from investment (earnings).</span></span>

## <a name="calculating-initial-investment"></a><span data-ttu-id="5356d-116">計算初始投資</span><span class="sxs-lookup"><span data-stu-id="5356d-116">Calculating initial investment</span></span>

<span data-ttu-id="5356d-117">初始投資是指完成轉換所需的資本支出 (CapEx) 和營運費用 (OpEx)。</span><span class="sxs-lookup"><span data-stu-id="5356d-117">Initial investment is the capital expenditure (CapEx) and operating expenditure (OpEx) required to complete a transformation.</span></span> <span data-ttu-id="5356d-118">成本的分類可能會隨著會計模型和 CFO 的偏好而不同。</span><span class="sxs-lookup"><span data-stu-id="5356d-118">The classification of costs can vary depending on accounting models and CFO preference.</span></span> <span data-ttu-id="5356d-119">不過，此類別會包含如下的項目：要轉換的專業服務、僅適用於轉換期間的軟體授權、轉換期間的雲端服務成本，以及轉換期間可能產生的支薪員工成本。</span><span class="sxs-lookup"><span data-stu-id="5356d-119">However, this category would include things like: Professional services to transform, software licenses that are used solely during the transformation, cost of cloud services during the transformation, and potentially the cost of the salaried employees during the transformation.</span></span>

<span data-ttu-id="5356d-120">將這些成本加總，即會產生初始投資的估計值。</span><span class="sxs-lookup"><span data-stu-id="5356d-120">Add these costs together to create an estimate of the initial investment.</span></span>

## <a name="calculating-the-gain-from-investment"></a><span data-ttu-id="5356d-121">計算投資所得</span><span class="sxs-lookup"><span data-stu-id="5356d-121">Calculating the gain from investment</span></span>

<span data-ttu-id="5356d-122">投資所得常需要以第二個公式計算，而此公式大多會隨著商務成果和相關的技術變更而不同。</span><span class="sxs-lookup"><span data-stu-id="5356d-122">Gain from investment often requires a second formula for calculation, which is very specific to the business outcomes and associated technical changes.</span></span> <span data-ttu-id="5356d-123">盈餘並不是扣除掉成本就能計算出來的。</span><span class="sxs-lookup"><span data-stu-id="5356d-123">Earnings are not as simple as calculating reduction in costs.</span></span>

<span data-ttu-id="5356d-124">若要計算盈餘，必須使用兩個變數：</span><span class="sxs-lookup"><span data-stu-id="5356d-124">To calculate earnings, two variables are required:</span></span>

![投資所得等於收益差異 + 成本差異](../_images/formula-gain-from-investment.png)

<!-- markdownlint-disable MD036 -->
<!--*Gain from Investment = Revenue Deltas + Cost Deltas*-->
<!-- markdownlint-enable MD036 -->

<span data-ttu-id="5356d-126">各項目分述如下。</span><span class="sxs-lookup"><span data-stu-id="5356d-126">Each is described below.</span></span>

## <a name="revenue-delta"></a><span data-ttu-id="5356d-127">收益差異</span><span class="sxs-lookup"><span data-stu-id="5356d-127">Revenue delta</span></span>

<span data-ttu-id="5356d-128">收益差異的預測應與企業合作共同進行。</span><span class="sxs-lookup"><span data-stu-id="5356d-128">Revenue delta should be forecasted in partnership with the business.</span></span> <span data-ttu-id="5356d-129">在企業的利害關係人達成收益影響的共識後，即可據以改善盈利狀況。</span><span class="sxs-lookup"><span data-stu-id="5356d-129">Once the business stakeholders agree on a revenue impact, that can be used to improve the earning position.</span></span>

## <a name="cost-deltas"></a><span data-ttu-id="5356d-130">成本差異</span><span class="sxs-lookup"><span data-stu-id="5356d-130">Cost deltas</span></span>

<span data-ttu-id="5356d-131">成本差異是指因轉換而將增加或減少的金額。</span><span class="sxs-lookup"><span data-stu-id="5356d-131">Cost deltas are the amount of increase or decrease that will come as a result of the transformation.</span></span> <span data-ttu-id="5356d-132">有數個獨立變數可能會影響成本的差異。</span><span class="sxs-lookup"><span data-stu-id="5356d-132">There are a number of independent variables that can affect cost deltas.</span></span> <span data-ttu-id="5356d-133">盈餘主要取決於資本支出降低、成本規避、營運成本降低和折舊降低等硬性成本。</span><span class="sxs-lookup"><span data-stu-id="5356d-133">Earnings are largely based on hard costs like capital expense reductions, cost avoidance, operational cost reductions, and depreciation reductions.</span></span> <span data-ttu-id="5356d-134">以下幾節將以範例說明應考量的成本差異。</span><span class="sxs-lookup"><span data-stu-id="5356d-134">The following sections are examples of cost deltas to consider.</span></span>

### <a name="depreciation-reductions-or-acceleration"></a><span data-ttu-id="5356d-135">折舊縮減或加速</span><span class="sxs-lookup"><span data-stu-id="5356d-135">Depreciation reductions or acceleration</span></span>

<span data-ttu-id="5356d-136">如需折舊的相關指引，請諮詢 CFO 或財務小組。</span><span class="sxs-lookup"><span data-stu-id="5356d-136">For guidance on depreciation, speak with the CFO or finance team.</span></span> <span data-ttu-id="5356d-137">以下內容是折舊相關主題的的通用性參考。</span><span class="sxs-lookup"><span data-stu-id="5356d-137">The following is meant to serve as a general reference on the topic of depreciation.</span></span>

<span data-ttu-id="5356d-138">投入資本以取得資產時，該項投資有可能是基於財務或稅務用途，目的是要在資產的預期使用期限內產生持續性的效益。</span><span class="sxs-lookup"><span data-stu-id="5356d-138">When capital is invested in the acquisition of an asset, that investment could be used for financial or tax purposes to produce ongoing benefits over the expected lifespan of the asset.</span></span> <span data-ttu-id="5356d-139">有些公司會將折舊列為稅務利益。</span><span class="sxs-lookup"><span data-stu-id="5356d-139">Some companies see depreciation as a positive tax advantage.</span></span> <span data-ttu-id="5356d-140">有些公司則將其認列為持續性費用，類似於其他歸屬於年度 IT 預算的週期性費用。</span><span class="sxs-lookup"><span data-stu-id="5356d-140">Others see it as committed, ongoing expense similar to other recurring expenses attributed to the annual IT budget.</span></span>

<span data-ttu-id="5356d-141">請向財務人員諮詢是否有可能消除折舊，以及此做法是否可對成本差異產生正面貢獻。</span><span class="sxs-lookup"><span data-stu-id="5356d-141">Speak with the finance office to see if elimination of depreciation is possible, and if it would make a positive contribution to cost deltas.</span></span>

### <a name="physical-asset-recovery"></a><span data-ttu-id="5356d-142">實體資產回收</span><span class="sxs-lookup"><span data-stu-id="5356d-142">Physical asset recovery</span></span>

<span data-ttu-id="5356d-143">在某些情況下，汰用的資產可轉售而成為收益來源。</span><span class="sxs-lookup"><span data-stu-id="5356d-143">In some cases, retired assets can be sold as a source of revenue.</span></span> <span data-ttu-id="5356d-144">為求簡便，此收益通常會一併計入成本降低金額中。</span><span class="sxs-lookup"><span data-stu-id="5356d-144">Often, this revenue is lumped into cost reduction for simplicity.</span></span> <span data-ttu-id="5356d-145">不過，其實質上的意義是收益增加，可能需要按金額課稅。</span><span class="sxs-lookup"><span data-stu-id="5356d-145">However, it's truly an increase in revenue and may be taxed as such.</span></span> <span data-ttu-id="5356d-146">請諮詢財務人員以了解此選項的可行性，並了解相關收益的會計處理方式。</span><span class="sxs-lookup"><span data-stu-id="5356d-146">Speak with the finance office to understand the viability of this option and how to account for the resulting revenue.</span></span>

### <a name="operational-cost-reductions"></a><span data-ttu-id="5356d-147">營運成本降低</span><span class="sxs-lookup"><span data-stu-id="5356d-147">Operational cost reductions</span></span>

<span data-ttu-id="5356d-148">企業營運所需的週期性費用，通常稱為營運費用 (OpEx)。</span><span class="sxs-lookup"><span data-stu-id="5356d-148">Recurring expenses required to operate the business are often referred to as operational expenses (OpEx).</span></span> <span data-ttu-id="5356d-149">OpEx 類別的涵蓋範圍相當廣泛。</span><span class="sxs-lookup"><span data-stu-id="5356d-149">OpEx is a very broad category.</span></span> <span data-ttu-id="5356d-150">在大多數的會計模型中，此類別會包含軟體授權、裝載費用、電費、不動產租金、冷卻費用、營運所需的臨時員工、設備租金、更換零件、維護合約、維修服務、商務持續性/災害復原 (BC/DR) 服務，和其他多項不需經過資本支出核准的費用。</span><span class="sxs-lookup"><span data-stu-id="5356d-150">In most accounting models, it would include software licensing, hosting expenses, electric bills, real estate rentals, cooling expenses, temporary staff required for operations, equipment rentals, replacement parts, maintenance contracts, repair services, Business Continuity/Disaster Recovery (BC/DR) services, and a number of other expenses that don't require capital expense approvals.</span></span>

<span data-ttu-id="5356d-151">此類別時，下列其中一個最大的盈餘區域考慮操作的轉換。</span><span class="sxs-lookup"><span data-stu-id="5356d-151">This category is one of the largest earnings areas when considering an Operational Transformation.</span></span> <span data-ttu-id="5356d-152">只要花時間詳盡列出此份清單，通常都可獲得效益。</span><span class="sxs-lookup"><span data-stu-id="5356d-152">Time invested in making this list exhaustive is seldom wasted.</span></span> <span data-ttu-id="5356d-153">請洽詢 CIO 和財務小組，確定所有營運成本都已納入會計帳目。</span><span class="sxs-lookup"><span data-stu-id="5356d-153">Ask questions of the CIO and finance team to ensure all operational costs are accounted for.</span></span>

### <a name="cost-avoidance"></a><span data-ttu-id="5356d-154">成本規避</span><span class="sxs-lookup"><span data-stu-id="5356d-154">Cost avoidance</span></span>

<span data-ttu-id="5356d-155">營運費用 (OpEx) 預期會產生、但尚未納入核准的預算中時，可能無法列為成本降低類別。</span><span class="sxs-lookup"><span data-stu-id="5356d-155">When an operational expense (OpEx) is expected, but not yet in an approved budget, it may not fit into a cost reduction category.</span></span> <span data-ttu-id="5356d-156">例如，如果 VMWare 和 Microsoft 授權必須重新議價並於下一個年度付費，則尚不屬於既定成本。</span><span class="sxs-lookup"><span data-stu-id="5356d-156">For instance, if VMWare and Microsoft licenses need to be renegotiated and paid next year, they aren't fully qualified costs yet.</span></span> <span data-ttu-id="5356d-157">這類預期成本的降低，將視同因成本差異計算而產生的營運成本。</span><span class="sxs-lookup"><span data-stu-id="5356d-157">Reductions in those expected costs would be treated like operational costs for the sake of cost delta calculations.</span></span> <span data-ttu-id="5356d-158">但非正式的做法是，在議價和預算核准完成之前，應稱之為「成本規避」。</span><span class="sxs-lookup"><span data-stu-id="5356d-158">Informally, however, they should be referred to as "cost avoidance," until negotiation and budget approval is complete.</span></span>

### <a name="soft-cost-reductions"></a><span data-ttu-id="5356d-159">軟性成本降低</span><span class="sxs-lookup"><span data-stu-id="5356d-159">Soft cost reductions</span></span>

<span data-ttu-id="5356d-160">某些公司也有可能納入軟性成本，例如降低操作複雜度，或縮減操作資料中心的全職人員。</span><span class="sxs-lookup"><span data-stu-id="5356d-160">In some companies, soft costs such as reductions in operational complexity or reduction in full-time staff to operate a datacenter could also be included.</span></span> <span data-ttu-id="5356d-161">不過，納入軟性成本可能不是理想的做法。</span><span class="sxs-lookup"><span data-stu-id="5356d-161">However, including soft costs can be ill-advised.</span></span> <span data-ttu-id="5356d-162">一旦納入軟性成本，等於是沒由來地假設成本降低金額將等同於有形成本節省金額。</span><span class="sxs-lookup"><span data-stu-id="5356d-162">Including soft costs inserts an undocumented assumption that the reduction in costs will equate to tangible cost savings.</span></span> <span data-ttu-id="5356d-163">科技專案鮮少會產生實際的軟性成本回收。</span><span class="sxs-lookup"><span data-stu-id="5356d-163">Technology projects seldom result in actual soft cost recovery.</span></span>

### <a name="headcount-reductions"></a><span data-ttu-id="5356d-164">員工人數縮減</span><span class="sxs-lookup"><span data-stu-id="5356d-164">Headcount reductions</span></span>

<span data-ttu-id="5356d-165">節省的工時通常會計入降低的軟性成本中。</span><span class="sxs-lookup"><span data-stu-id="5356d-165">Time savings for staff are often included under soft cost reduction.</span></span> <span data-ttu-id="5356d-166">這些節省的時間在對應至實際降低的 IT 薪資或人員配置時，可以個別計為員工人數縮減值。</span><span class="sxs-lookup"><span data-stu-id="5356d-166">When those time savings map to actual reduction of IT salary or staffing, it could be calculated separately as a headcount reduction.</span></span>

<span data-ttu-id="5356d-167">即便如此，內部部署環境所需的技能，通常會對應於雲端中所需的類似 (或更高層級) 的技能集。</span><span class="sxs-lookup"><span data-stu-id="5356d-167">That said, the skills needed on-premises generally map to a similar (or higher level) set of skills needed in the cloud.</span></span> <span data-ttu-id="5356d-168">這表示在一般情況下，員工並不會因為雲端移轉而遭到解雇。</span><span class="sxs-lookup"><span data-stu-id="5356d-168">That means people generally don't get laid off after a cloud migration.</span></span>

<span data-ttu-id="5356d-169">但由第三方或受控服務提供者 (MSP) 提供運作容量時，則屬例外。</span><span class="sxs-lookup"><span data-stu-id="5356d-169">An exception is when operational capacity is provided by a third party or managed services provider (MSP).</span></span> <span data-ttu-id="5356d-170">如果 IT 系統由第三方管理，則運作成本可能會取代為雲端原生解決方案或雲端原生 MSP。</span><span class="sxs-lookup"><span data-stu-id="5356d-170">If IT systems are managed by a third party, the costs to operate could be replaced by a cloud-native solution or cloud-native MSP.</span></span> <span data-ttu-id="5356d-171">雲端原生 MSP 的運作可能更有效率，且成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="5356d-171">A cloud native MSP is likely to operate more efficiently and potentially at a lower cost.</span></span> <span data-ttu-id="5356d-172">若是如此，營運成本降低就會歸屬於硬性成本的計算。</span><span class="sxs-lookup"><span data-stu-id="5356d-172">If that's the case, operational cost reductions belong in the hard cost calculations.</span></span>

### <a name="capital-expense-reductions-or-avoidance"></a><span data-ttu-id="5356d-173">資本支出降低或規避</span><span class="sxs-lookup"><span data-stu-id="5356d-173">Capital expense reductions or avoidance</span></span>

<span data-ttu-id="5356d-174">資本支出 (CapEx) 與營運費用略有不同。</span><span class="sxs-lookup"><span data-stu-id="5356d-174">Capital expenses (CapEx) are slightly different that operational expenses.</span></span> <span data-ttu-id="5356d-175">此類別通常由更新週期或資料中心擴充所驅動。</span><span class="sxs-lookup"><span data-stu-id="5356d-175">Generally, this category is driven by refresh cycles or datacenter expansion.</span></span> <span data-ttu-id="5356d-176">以新的高效能叢集裝載巨量資料解決方案或資料倉儲時，若要將其統合納入 CapEx 類別中，即屬於資料中心擴充的範例。</span><span class="sxs-lookup"><span data-stu-id="5356d-176">An example of a datacenter expansion would be a new high-performance cluster to host a Big Data solution or data warehouse, and would generally fit into a CapEx category.</span></span> <span data-ttu-id="5356d-177">更常見的是基本更新週期。</span><span class="sxs-lookup"><span data-stu-id="5356d-177">More common are the basic refresh cycles.</span></span> <span data-ttu-id="5356d-178">有些公司具有嚴謹的硬體，重新整理循環，意義資產已停用，並取代一般的循環 （通常每隔三、 五年或八年）。</span><span class="sxs-lookup"><span data-stu-id="5356d-178">Some companies have rigid hardware refresh cycles, meaning assets are retired and replaced on a regular cycle (usually every three, five, or eight years).</span></span> <span data-ttu-id="5356d-179">這些週期通常會與資產租用週期或預測的設備使用期限一致。</span><span class="sxs-lookup"><span data-stu-id="5356d-179">These cycles often coincide with asset lease cycles or forecasted lifespan of equipment.</span></span> <span data-ttu-id="5356d-180">在達到更新週期時，IT 即會提取 CapEx 以購買新設備。</span><span class="sxs-lookup"><span data-stu-id="5356d-180">When a refresh cycle hits, IT draws CapEx to acquire new equipment.</span></span>

<span data-ttu-id="5356d-181">在更新週期通過核准並編入預算後，雲端轉換將有助於消除該成本。</span><span class="sxs-lookup"><span data-stu-id="5356d-181">If a refresh cycle is approved and budgeted, the Cloud Transformation could help eliminate that cost.</span></span> <span data-ttu-id="5356d-182">若已規劃更新週期但尚未核准，雲端轉換將可產生 CapEx 成本規避的效果。</span><span class="sxs-lookup"><span data-stu-id="5356d-182">If a refresh cycle is planned but not yet approved, the Cloud Transformation could create a CapEx cost avoidance.</span></span> <span data-ttu-id="5356d-183">這兩個案例都會計入成本差異中。</span><span class="sxs-lookup"><span data-stu-id="5356d-183">Both scenarios would be added to the cost delta.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5356d-184">後續步驟</span><span class="sxs-lookup"><span data-stu-id="5356d-184">Next steps</span></span>

<span data-ttu-id="5356d-185">請閱讀雲端轉換所造就的範例財會結果。</span><span class="sxs-lookup"><span data-stu-id="5356d-185">Read some example fiscal outcomes in the context of a cloud transformation.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="5356d-186">財會結果範例</span><span class="sxs-lookup"><span data-stu-id="5356d-186">Examples of fiscal outcomes</span></span>](./business-outcomes/fiscal-outcomes.md)
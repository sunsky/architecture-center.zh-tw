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
ms.openlocfilehash: 4fe9b178962bf2cd6a79233278c73085237772f0
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55898199"
---
# <a name="create-a-financial-model-for-cloud-transformation"></a><span data-ttu-id="2cf66-103">建立雲端轉換的財務模型</span><span class="sxs-lookup"><span data-stu-id="2cf66-103">Create a financial model for cloud transformation</span></span>

<span data-ttu-id="2cf66-104">要建立財務模型以準確呈現任何雲端轉換的完整商業價值，可能是很複雜的作業。</span><span class="sxs-lookup"><span data-stu-id="2cf66-104">Creating a financial model that accurately represents the full business value of any cloud transformation can be complicated.</span></span> <span data-ttu-id="2cf66-105">不同的組織會有不同的財務模型和商業論證。</span><span class="sxs-lookup"><span data-stu-id="2cf66-105">Financial models and business justifications tend to be different from one organization to the next.</span></span> <span data-ttu-id="2cf66-106">本文將建立某些公式，並指出在建立財務模型時常會疏漏的若干要點。</span><span class="sxs-lookup"><span data-stu-id="2cf66-106">This article establishes some formulas and points out a few things that are commonly missed when creating a financial model.</span></span>

## <a name="return-on-investment-roi"></a><span data-ttu-id="2cf66-107">投資報酬率 (ROI)</span><span class="sxs-lookup"><span data-stu-id="2cf66-107">Return on investment (ROI)</span></span>

<span data-ttu-id="2cf66-108">投資報酬率 (ROI) 常是高層主管或董事的重要考量依據。</span><span class="sxs-lookup"><span data-stu-id="2cf66-108">Return on investment (ROI) is often an important criteria with the C-Suite or the board.</span></span> <span data-ttu-id="2cf66-109">投資報酬率可用來比較投入有限資本資源的不同方式。</span><span class="sxs-lookup"><span data-stu-id="2cf66-109">ROI is used to compare different ways to invest limited capital resources.</span></span> <span data-ttu-id="2cf66-110">投資報酬率的公式相當簡單。</span><span class="sxs-lookup"><span data-stu-id="2cf66-110">The formula for ROI is fairly simple.</span></span> <span data-ttu-id="2cf66-111">但建立各項公式輸入所需的詳細資料可能不易取得。</span><span class="sxs-lookup"><span data-stu-id="2cf66-111">The details required to create each input to the formula may not be as simple.</span></span> <span data-ttu-id="2cf66-112">基本上，ROI 會是初始投資所產生的回收金額。</span><span class="sxs-lookup"><span data-stu-id="2cf66-112">Essentially, ROI is the amount of return produced from an initial investment.</span></span> <span data-ttu-id="2cf66-113">此值通常以百分比表示：</span><span class="sxs-lookup"><span data-stu-id="2cf66-113">Usually it is represented as a percentage:</span></span>

![投資報酬率 (ROI) 等於 (投資所得 – 投資成本)/投資成本](../_images/formula-roi.png)

<span data-ttu-id="2cf66-115"><!-- markdownlint-disable MD036 -->
*ROI = (投資所得 &minus; 初始投資)/初始投資*
<!-- markdownlint-enable MD036 --></span><span class="sxs-lookup"><span data-stu-id="2cf66-115"><!-- markdownlint-disable MD036 -->
*ROI = (Gain from Investment &minus; Initial Investment) / Initial Investment*
<!-- markdownlint-enable MD036 --></span></span>

<span data-ttu-id="2cf66-116">在下一節中，我們將逐步說明計算初始投資和投資所得 (盈餘) 所需的資料。</span><span class="sxs-lookup"><span data-stu-id="2cf66-116">In the next sections, we will walk through the data needed to calculate the initial investment and the gain from investment (earnings).</span></span>

## <a name="calculating-initial-investment"></a><span data-ttu-id="2cf66-117">計算初始投資</span><span class="sxs-lookup"><span data-stu-id="2cf66-117">Calculating initial investment</span></span>

<span data-ttu-id="2cf66-118">初始投資是指完成轉換所需的資本支出 (CapEx) 和營運費用 (OpEx)。</span><span class="sxs-lookup"><span data-stu-id="2cf66-118">Initial investment is the capital expenditure (CapEx) and operating expenditure (OpEx) required to complete a transformation.</span></span> <span data-ttu-id="2cf66-119">成本的分類可能會隨著會計模型和 CFO 的偏好而不同。</span><span class="sxs-lookup"><span data-stu-id="2cf66-119">The classification of costs can vary depending on accounting models and CFO preference.</span></span> <span data-ttu-id="2cf66-120">不過，此類別會包含如下的項目：要轉換的專業服務、僅適用於轉換期間的軟體授權、轉換期間的雲端服務成本，以及轉換期間可能產生的支薪員工成本。</span><span class="sxs-lookup"><span data-stu-id="2cf66-120">However, this category would include things like: Professional services to transform, software licenses that are used solely during the transformation, cost of cloud services during the transformation, and potentially the cost of the salaried employees during the transformation.</span></span>

<span data-ttu-id="2cf66-121">將這些成本加總，即會產生初始投資的估計值。</span><span class="sxs-lookup"><span data-stu-id="2cf66-121">Add these costs together to create an estimate of the initial investment.</span></span>

## <a name="calculating-the-gain-from-investment"></a><span data-ttu-id="2cf66-122">計算投資所得</span><span class="sxs-lookup"><span data-stu-id="2cf66-122">Calculating the gain from investment</span></span>

<span data-ttu-id="2cf66-123">投資所得常需要以第二個公式計算，而此公式大多會隨著商務成果和相關的技術變更而不同。</span><span class="sxs-lookup"><span data-stu-id="2cf66-123">Gain from investment often requires a second formula for calculation, which is very specific to the business outcomes and associated technical changes.</span></span> <span data-ttu-id="2cf66-124">盈餘並不是扣除掉成本就能計算出來的。</span><span class="sxs-lookup"><span data-stu-id="2cf66-124">Earnings are not as simple as calculating reduction in costs.</span></span>

<span data-ttu-id="2cf66-125">若要計算盈餘，必須使用兩個變數：</span><span class="sxs-lookup"><span data-stu-id="2cf66-125">To calculate earnings, two variables are required:</span></span>

![投資所得等於收益差異 + 成本差異](../_images/formula-gain-from-investment.png)

<span data-ttu-id="2cf66-127"><!-- markdownlint-disable MD036 -->
*投資所得 = 收益差異 + 成本差異*
<!-- markdownlint-enable MD036 --></span><span class="sxs-lookup"><span data-stu-id="2cf66-127"><!-- markdownlint-disable MD036 -->
*Gain from Investment = Revenue Deltas + Cost Deltas*
<!-- markdownlint-enable MD036 --></span></span>

<span data-ttu-id="2cf66-128">各項目分述如下。</span><span class="sxs-lookup"><span data-stu-id="2cf66-128">Each is described below.</span></span>

## <a name="revenue-delta"></a><span data-ttu-id="2cf66-129">收益差異</span><span class="sxs-lookup"><span data-stu-id="2cf66-129">Revenue delta</span></span>

<span data-ttu-id="2cf66-130">收益差異的預測應與企業合作共同進行。</span><span class="sxs-lookup"><span data-stu-id="2cf66-130">Revenue delta should be forecasted in partnership with the business.</span></span> <span data-ttu-id="2cf66-131">在企業的利害關係人達成收益影響的共識後，即可據以改善盈利狀況。</span><span class="sxs-lookup"><span data-stu-id="2cf66-131">Once the business stakeholders agree on a revenue impact, that can be used to improve the earning position.</span></span>

## <a name="cost-deltas"></a><span data-ttu-id="2cf66-132">成本差異</span><span class="sxs-lookup"><span data-stu-id="2cf66-132">Cost deltas</span></span>

<span data-ttu-id="2cf66-133">成本差異是指因轉換而將增加或減少的金額。</span><span class="sxs-lookup"><span data-stu-id="2cf66-133">Cost deltas are the amount of increase or decrease that will come as a result of the transformation.</span></span> <span data-ttu-id="2cf66-134">有多個獨立變數可能會對成本差異造成影響。</span><span class="sxs-lookup"><span data-stu-id="2cf66-134">There are a number of independent variables that can impact cost deltas.</span></span> <span data-ttu-id="2cf66-135">盈餘主要取決於資本支出降低、成本規避、營運成本降低和折舊降低等硬性成本。</span><span class="sxs-lookup"><span data-stu-id="2cf66-135">Earnings are largely based on hard costs like capital expense reductions, cost avoidance, operational cost reductions, and depreciation reductions.</span></span> <span data-ttu-id="2cf66-136">以下幾節將以範例說明應考量的成本差異。</span><span class="sxs-lookup"><span data-stu-id="2cf66-136">The following sections are examples of cost deltas to consider.</span></span>

### <a name="depreciation-reductions-or-acceleration"></a><span data-ttu-id="2cf66-137">折舊縮減或加速</span><span class="sxs-lookup"><span data-stu-id="2cf66-137">Depreciation reductions or acceleration</span></span>

<span data-ttu-id="2cf66-138">如需折舊的相關指引，請諮詢 CFO 或財務小組。</span><span class="sxs-lookup"><span data-stu-id="2cf66-138">For guidance on depreciation, speak with the CFO or finance team.</span></span> <span data-ttu-id="2cf66-139">以下內容是折舊相關主題的的通用性參考。</span><span class="sxs-lookup"><span data-stu-id="2cf66-139">The following is meant to serve as a general reference on the topic of depreciation.</span></span>

<span data-ttu-id="2cf66-140">投入資本以取得資產時，該項投資有可能是基於財務或稅務用途，目的是要在資產的預期使用期限內產生持續性的效益。</span><span class="sxs-lookup"><span data-stu-id="2cf66-140">When capital is invested in the acquisition of an asset, that investment could be used for financial or tax purposes to produce ongoing benefits over the expected lifespan of the asset.</span></span> <span data-ttu-id="2cf66-141">有些公司會將折舊列為稅務利益。</span><span class="sxs-lookup"><span data-stu-id="2cf66-141">Some companies see depreciation as a positive tax advantage.</span></span> <span data-ttu-id="2cf66-142">有些公司則將其認列為持續性費用，類似於其他歸屬於年度 IT 預算的週期性費用。</span><span class="sxs-lookup"><span data-stu-id="2cf66-142">Others see it as committed, ongoing expense similar to other recurring expenses attributed to the annual IT budget.</span></span>

<span data-ttu-id="2cf66-143">請向財務人員諮詢是否有可能消除折舊，以及此做法是否可對成本差異產生正面貢獻。</span><span class="sxs-lookup"><span data-stu-id="2cf66-143">Speak with the finance office to see if elimination of depreciation is possible, and if it would make a positive contribution to cost deltas.</span></span>

### <a name="physical-asset-recovery"></a><span data-ttu-id="2cf66-144">實體資產回收</span><span class="sxs-lookup"><span data-stu-id="2cf66-144">Physical asset recovery</span></span>

<span data-ttu-id="2cf66-145">在某些情況下，汰用的資產可轉售而成為收益來源。</span><span class="sxs-lookup"><span data-stu-id="2cf66-145">In some cases, retired assets can be sold as a source of revenue.</span></span> <span data-ttu-id="2cf66-146">為求簡便，此收益通常會一併計入成本降低金額中。</span><span class="sxs-lookup"><span data-stu-id="2cf66-146">Often, this revenue is lumped into cost reduction for simplicity.</span></span> <span data-ttu-id="2cf66-147">不過，其實質上的意義是收益增加，可能需要按金額課稅。</span><span class="sxs-lookup"><span data-stu-id="2cf66-147">However, it's truly an increase in revenue and may be taxed as such.</span></span> <span data-ttu-id="2cf66-148">請諮詢財務人員以了解此選項的可行性，並了解相關收益的會計處理方式。</span><span class="sxs-lookup"><span data-stu-id="2cf66-148">Speak with the finance office to understand the viability of this option and how to account for the resulting revenue.</span></span>

### <a name="operational-cost-reductions"></a><span data-ttu-id="2cf66-149">營運成本降低</span><span class="sxs-lookup"><span data-stu-id="2cf66-149">Operational cost reductions</span></span>

<span data-ttu-id="2cf66-150">企業營運所需的週期性費用，通常稱為營運費用 (OpEx)。</span><span class="sxs-lookup"><span data-stu-id="2cf66-150">Recurring expenses required to operate the business are often referred to as operational expenses (OpEx).</span></span> <span data-ttu-id="2cf66-151">OpEx 類別的涵蓋範圍相當廣泛。</span><span class="sxs-lookup"><span data-stu-id="2cf66-151">OpEx is a very broad category.</span></span> <span data-ttu-id="2cf66-152">在大多數的會計模型中，此類別會包含軟體授權、裝載費用、電費、不動產租金、冷卻費用、營運所需的臨時員工、設備租金、更換零件、維護合約、維修服務、商務持續性/災害復原 (BC/DR) 服務，和其他多項不需經過資本支出核准的費用。</span><span class="sxs-lookup"><span data-stu-id="2cf66-152">In most accounting models, it would include software licensing, hosting expenses, electric bills, real estate rentals, cooling expenses, temporary staff required for operations, equipment rentals, replacement parts, maintenance contracts, repair services, Business Continuity/Disaster Recovery (BC/DR) services, and a number of other expenses that don't require capital expense approvals.</span></span>

<span data-ttu-id="2cf66-153">在考量營運轉型流程時，此類別是可產生最大盈餘的區塊之一。</span><span class="sxs-lookup"><span data-stu-id="2cf66-153">This category is one of the largest earnings areas when considering an Operational Transformation Journey.</span></span> <span data-ttu-id="2cf66-154">只要花時間詳盡列出此份清單，通常都可獲得效益。</span><span class="sxs-lookup"><span data-stu-id="2cf66-154">Time invested in making this list exhaustive is seldom wasted.</span></span> <span data-ttu-id="2cf66-155">請洽詢 CIO 和財務小組，確定所有營運成本都已納入會計帳目。</span><span class="sxs-lookup"><span data-stu-id="2cf66-155">Ask questions of the CIO and finance team to ensure all operational costs are accounted for.</span></span>

### <a name="cost-avoidance"></a><span data-ttu-id="2cf66-156">成本規避</span><span class="sxs-lookup"><span data-stu-id="2cf66-156">Cost avoidance</span></span>

<span data-ttu-id="2cf66-157">營運費用 (OpEx) 預期會產生、但尚未納入核准的預算中時，可能無法列為成本降低類別。</span><span class="sxs-lookup"><span data-stu-id="2cf66-157">When an operational expense (OpEx) is expected, but not yet in an approved budget, it may not fit into a cost reduction category.</span></span> <span data-ttu-id="2cf66-158">例如，如果 VMWare 和 Microsoft 授權必須重新議價並於下一個年度付費，則尚不屬於既定成本。</span><span class="sxs-lookup"><span data-stu-id="2cf66-158">For instance, if VMWare and Microsoft licenses need to be renegotiated and paid next year, they aren't fully qualified costs yet.</span></span> <span data-ttu-id="2cf66-159">這類預期成本的降低，將視同因成本差異計算而產生的營運成本。</span><span class="sxs-lookup"><span data-stu-id="2cf66-159">Reductions in those expected costs would be treated like operational costs for the sake of cost delta calculations.</span></span> <span data-ttu-id="2cf66-160">但非正式的做法是，在議價和預算核准完成之前，應稱之為「成本規避」。</span><span class="sxs-lookup"><span data-stu-id="2cf66-160">Informally, however, they should be referred to as "cost avoidance," until negotiation and budget approval is complete.</span></span>

### <a name="soft-cost-reductions"></a><span data-ttu-id="2cf66-161">軟性成本降低</span><span class="sxs-lookup"><span data-stu-id="2cf66-161">Soft cost reductions</span></span>

<span data-ttu-id="2cf66-162">某些公司也有可能納入軟性成本，例如降低操作複雜度，或縮減操作資料中心的全職人員。</span><span class="sxs-lookup"><span data-stu-id="2cf66-162">In some companies, soft costs such as reductions in operational complexity or reduction in full-time staff to operate a datacenter could also be included.</span></span> <span data-ttu-id="2cf66-163">不過，納入軟性成本可能不是理想的做法。</span><span class="sxs-lookup"><span data-stu-id="2cf66-163">However, including soft costs can be ill-advised.</span></span> <span data-ttu-id="2cf66-164">一旦納入軟性成本，等於是沒由來地假設成本降低金額將等同於有形成本節省金額。</span><span class="sxs-lookup"><span data-stu-id="2cf66-164">Including soft costs inserts an undocumented assumption that the reduction in costs will equate to tangible cost savings.</span></span> <span data-ttu-id="2cf66-165">科技專案鮮少會產生實際的軟性成本回收。</span><span class="sxs-lookup"><span data-stu-id="2cf66-165">Technology projects seldom result in actual soft cost recovery.</span></span>

### <a name="headcount-reductions"></a><span data-ttu-id="2cf66-166">員工人數縮減</span><span class="sxs-lookup"><span data-stu-id="2cf66-166">Headcount reductions</span></span>

<span data-ttu-id="2cf66-167">節省的工時通常會計入降低的軟性成本中。</span><span class="sxs-lookup"><span data-stu-id="2cf66-167">Time savings for staff are often included under soft cost reduction.</span></span> <span data-ttu-id="2cf66-168">這些節省的時間在對應至實際降低的 IT 薪資或人員配置時，可以個別計為員工人數縮減值。</span><span class="sxs-lookup"><span data-stu-id="2cf66-168">When those time savings map to actual reduction of IT salary or staffing, it could be calculated separately as a headcount reduction.</span></span>

<span data-ttu-id="2cf66-169">即便如此，內部部署環境所需的技能，通常會對應於雲端中所需的類似 (或更高層級) 的技能集。</span><span class="sxs-lookup"><span data-stu-id="2cf66-169">That said, the skills needed on-premises generally map to a similar (or higher level) set of skills needed in the cloud.</span></span> <span data-ttu-id="2cf66-170">這表示在一般情況下，員工並不會因為雲端移轉而遭到解雇。</span><span class="sxs-lookup"><span data-stu-id="2cf66-170">That means people generally don't get laid off after a cloud migration.</span></span>

<span data-ttu-id="2cf66-171">但由第三方或受控服務提供者 (MSP) 提供運作容量時，則屬例外。</span><span class="sxs-lookup"><span data-stu-id="2cf66-171">An exception is when operational capacity is provided by a third party or managed services provider (MSP).</span></span> <span data-ttu-id="2cf66-172">如果 IT 系統由第三方管理，則運作成本可能會取代為雲端原生解決方案或雲端原生 MSP。</span><span class="sxs-lookup"><span data-stu-id="2cf66-172">If IT systems are managed by a third party, the costs to operate could be replaced by a cloud-native solution or cloud-native MSP.</span></span> <span data-ttu-id="2cf66-173">雲端原生 MSP 的運作可能更有效率，且成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="2cf66-173">A cloud native MSP is likely to operate more efficiently and potentially at a lower cost.</span></span> <span data-ttu-id="2cf66-174">若是如此，營運成本降低就會歸屬於硬性成本的計算。</span><span class="sxs-lookup"><span data-stu-id="2cf66-174">If that's the case, operational cost reductions belong in the hard cost calculations.</span></span>

### <a name="capital-expense-reductions-or-avoidance"></a><span data-ttu-id="2cf66-175">資本支出降低或規避</span><span class="sxs-lookup"><span data-stu-id="2cf66-175">Capital expense reductions or avoidance</span></span>

<span data-ttu-id="2cf66-176">資本支出 (CapEx) 與營運費用略有不同。</span><span class="sxs-lookup"><span data-stu-id="2cf66-176">Capital expenses (CapEx) are slightly different that operational expenses.</span></span> <span data-ttu-id="2cf66-177">此類別通常由更新週期或資料中心擴充所驅動。</span><span class="sxs-lookup"><span data-stu-id="2cf66-177">Generally, this category is driven by refresh cycles or datacenter expansion.</span></span> <span data-ttu-id="2cf66-178">以新的高效能叢集裝載巨量資料解決方案或資料倉儲時，若要將其統合納入 CapEx 類別中，即屬於資料中心擴充的範例。</span><span class="sxs-lookup"><span data-stu-id="2cf66-178">An example of a datacenter expansion would be a new high-performance cluster to host a Big Data solution or data warehouse, and would generally fit into a CapEx category.</span></span> <span data-ttu-id="2cf66-179">更常見的是基本更新週期。</span><span class="sxs-lookup"><span data-stu-id="2cf66-179">More common are the basic refresh cycles.</span></span> <span data-ttu-id="2cf66-180">有些公司採用嚴謹的硬體更新週期，亦即資產會以固定週期汰用和換新 (通常為每 3、5 或 8 年一次)。</span><span class="sxs-lookup"><span data-stu-id="2cf66-180">Some companies have rigid hardware refresh cycles, meaning assets are retired and replaced on a regular cycle (usually every 3, 5, or 8 years).</span></span> <span data-ttu-id="2cf66-181">這些週期通常會與資產租用週期或預測的設備使用期限一致。</span><span class="sxs-lookup"><span data-stu-id="2cf66-181">These cycles often coincide with asset lease cycles or forecasted lifespan of equipment.</span></span> <span data-ttu-id="2cf66-182">在達到更新週期時，IT 即會提取 CapEx 以購買新設備。</span><span class="sxs-lookup"><span data-stu-id="2cf66-182">When a refresh cycle hits, IT draws CapEx to acquire new equipment.</span></span>

<span data-ttu-id="2cf66-183">在更新週期通過核准並編入預算後，雲端轉換將有助於消除該成本。</span><span class="sxs-lookup"><span data-stu-id="2cf66-183">If a refresh cycle is approved and budgeted, the Cloud Transformation could help eliminate that cost.</span></span> <span data-ttu-id="2cf66-184">若已規劃更新週期但尚未核准，雲端轉換將可產生 CapEx 成本規避的效果。</span><span class="sxs-lookup"><span data-stu-id="2cf66-184">If a refresh cycle is planned but not yet approved, the Cloud Transformation could create a CapEx cost avoidance.</span></span> <span data-ttu-id="2cf66-185">這兩個案例都會計入成本差異中。</span><span class="sxs-lookup"><span data-stu-id="2cf66-185">Both scenarios would be added to the cost delta.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2cf66-186">後續步驟</span><span class="sxs-lookup"><span data-stu-id="2cf66-186">Next steps</span></span>

<span data-ttu-id="2cf66-187">請閱讀雲端轉換所造就的範例財會結果。</span><span class="sxs-lookup"><span data-stu-id="2cf66-187">Read some example fiscal outcomes in the context of a cloud transformation.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="2cf66-188">財會結果範例</span><span class="sxs-lookup"><span data-stu-id="2cf66-188">Examples of fiscal outcomes</span></span>](./business-outcomes/fiscal-outcomes.md)
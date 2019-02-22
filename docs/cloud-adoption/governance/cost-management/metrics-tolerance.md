---
title: CAF：成本管理的計量、指標及風險承受度
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 說明與雲端治理相關的成本管理
author: BrianBlanchard
ms.openlocfilehash: 76e6b1b32dd862322f6cafd9aa63c6c4f79f4f5d
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901096"
---
# <a name="cost-management-metrics-indicators-and-risk-tolerance"></a><span data-ttu-id="ace3b-103">成本管理的計量、指標及風險承受度</span><span class="sxs-lookup"><span data-stu-id="ace3b-103">Cost Management metrics, indicators, and risk tolerance</span></span>

<span data-ttu-id="ace3b-104">本文旨在協助您量化業務風險承受度，因為這與成本管理息息相關。</span><span class="sxs-lookup"><span data-stu-id="ace3b-104">This article is intended to help you quantify business risk tolerance as it relates to Cost Management.</span></span> <span data-ttu-id="ace3b-105">定義計量和指標可協助您建立商務案例，藉此讓成本管理專業領域變得更完善。</span><span class="sxs-lookup"><span data-stu-id="ace3b-105">Defining metrics and indicators helps you create a business case for making an investment in the maturity of the Cost Management discipline.</span></span>

## <a name="metrics"></a><span data-ttu-id="ace3b-106">度量</span><span class="sxs-lookup"><span data-stu-id="ace3b-106">Metrics</span></span>

<span data-ttu-id="ace3b-107">成本管理通常著重於與成本相關的計量。</span><span class="sxs-lookup"><span data-stu-id="ace3b-107">Cost Management generally focuses on metrics related to costs.</span></span> <span data-ttu-id="ace3b-108">若要進行風險分析，您可以收集雲端式工作負載上目前和已規劃的費用資料，以判斷要面對多少風險，以及針對您雲端採用策略投資成本治理有多重要。</span><span class="sxs-lookup"><span data-stu-id="ace3b-108">As part of your risk analysis, you'll want to gather data related to your current and planned spending on cloud-based workloads to determine how much risk you face, and how important investment in cost governance is to your cloud adoption strategy.</span></span>

<span data-ttu-id="ace3b-109">下列是實用計量的範例，您應收集這些計量來協助評估安全性基準專業領域的風險承受度：</span><span class="sxs-lookup"><span data-stu-id="ace3b-109">The following are examples of useful metrics that you should gather to help evaluate risk tolerance within the Security Baseline discipline:</span></span>

- <span data-ttu-id="ace3b-110">每年費用：雲端提供者所供服務的年度成本</span><span class="sxs-lookup"><span data-stu-id="ace3b-110">Annual spending: The total annual cost for services provided by a cloud provider</span></span>
- <span data-ttu-id="ace3b-111">每月費用：雲端提供者所供服務的每月成本</span><span class="sxs-lookup"><span data-stu-id="ace3b-111">Monthly spending: The total monthly cost for services provided by a cloud provider</span></span>
- <span data-ttu-id="ace3b-112">預測與實際費用的比率：比較預測與實際費用的比率 (每月或每年)</span><span class="sxs-lookup"><span data-stu-id="ace3b-112">Forecasted versus actual ratio: The ratio comparing forecasted and actual spending (monthly or annual)</span></span>
- <span data-ttu-id="ace3b-113">採用步調 (MOM) 的比率：每個月的雲端成本差異百分比</span><span class="sxs-lookup"><span data-stu-id="ace3b-113">Pace of adoption (MOM) ratio: The percentage of the delta in cloud costs from month to month</span></span>
- <span data-ttu-id="ace3b-114">累積成本：累算的每日費用總計，從當月月初開始</span><span class="sxs-lookup"><span data-stu-id="ace3b-114">Accumulated cost: Total accrued daily spending, starting from the beginning of the month</span></span>
- <span data-ttu-id="ace3b-115">費用趨勢：與預算對照的費用趨勢</span><span class="sxs-lookup"><span data-stu-id="ace3b-115">Spending trends: Spending trend against the budget</span></span>

## <a name="risk-tolerance-indicators"></a><span data-ttu-id="ace3b-116">風險承受度指標</span><span class="sxs-lookup"><span data-stu-id="ace3b-116">Risk tolerance indicators</span></span>

<span data-ttu-id="ace3b-117">在部署的早期階段中，例如開發/測試或實驗初期工作負載時，成本管理的風險可能極低。</span><span class="sxs-lookup"><span data-stu-id="ace3b-117">During early deployments, such as Dev/Test or experimental first workloads, Cost Management is likely to be of relatively low risk.</span></span> <span data-ttu-id="ace3b-118">當部署愈來愈多個資產時，風險就會增加，而業務的風險承受度可能會降低。</span><span class="sxs-lookup"><span data-stu-id="ace3b-118">As more assets are deployed, the risk grows and the business' tolerance for risk is likely to decline.</span></span> <span data-ttu-id="ace3b-119">此外，因為愈來愈多的雲端採用小組有能力將資產設定或部署至雲端，使得風險增加，而承受度降低。</span><span class="sxs-lookup"><span data-stu-id="ace3b-119">Additionally, as more cloud adoption teams are given the ability to configure or deploy assets to the cloud, the risk grows and tolerance decreases.</span></span> <span data-ttu-id="ace3b-120">反過來說，成本管理專業領域的成長，將會讓雲端採用階段上的人員部署更多創新的新技術。</span><span class="sxs-lookup"><span data-stu-id="ace3b-120">Conversely, growing a Cost Management discipline will take people from the cloud adoption phase to deploy more innovative new technologies.</span></span>

<span data-ttu-id="ace3b-121">在雲端採用的早期階段，您會與企業一起判斷風險承受度基準。</span><span class="sxs-lookup"><span data-stu-id="ace3b-121">In the early stages of cloud adoption, you will work with your business to determine a risk tolerance baseline.</span></span> <span data-ttu-id="ace3b-122">有了基準之後，您必須決定會觸發成本管理專業領域投資的準則。</span><span class="sxs-lookup"><span data-stu-id="ace3b-122">Once you have a baseline, you will need to determine the criteria that would trigger an investment in the Cost Management discipline.</span></span> <span data-ttu-id="ace3b-123">這些準則可能會因為不同組織而有所差異。</span><span class="sxs-lookup"><span data-stu-id="ace3b-123">These criteria will likely be different for every organization.</span></span>

<span data-ttu-id="ace3b-124">當您發現[業務風險](./business-risks.md)後，您將與您的企業一起識別可用來識別觸發條件的基準，而這些觸發條件可能會提高這些風險。</span><span class="sxs-lookup"><span data-stu-id="ace3b-124">Once you have identified [business risks](./business-risks.md), you will work with your business to identify benchmarks that you can use to identify triggers that could potentially increase those risks.</span></span> <span data-ttu-id="ace3b-125">以下幾個範例會說明計量 (例如前面提及的那些) 如何與您的風險基準承受度做比較，以指出您業務在成本管理上所需的進一步投資。</span><span class="sxs-lookup"><span data-stu-id="ace3b-125">The following are a few examples of how metrics, such as those mentioned above, can be compared against your risk baseline tolerance to indicate your business's need to further invest in Cost Management.</span></span>

- <span data-ttu-id="ace3b-126">承諾驅動 (最常見)：公司今年的目標是在雲端廠商上支出 $X,000,000 元。</span><span class="sxs-lookup"><span data-stu-id="ace3b-126">Commitment-driven (most common): A company that is committed to spending $X,000,000 this year on a cloud vendor.</span></span> <span data-ttu-id="ace3b-127">他們需要成本管理專業領域來確保業務量不會超過費用目標的 20%，並且會使用最少 90% 的承諾用量。</span><span class="sxs-lookup"><span data-stu-id="ace3b-127">They need a Cost Management discipline to ensure that the business doesn't exceed its spending targets by more than 20%, and that they will use at least 90% of that commitment.</span></span>
- <span data-ttu-id="ace3b-128">百分比觸發條件：某公司在其生產系統上有穩定的雲端費用支出。</span><span class="sxs-lookup"><span data-stu-id="ace3b-128">Percentage trigger: A company with cloud spending that is stable for their production systems.</span></span> <span data-ttu-id="ace3b-129">如果這些費用的變更超過 X%，則成本管理專業領域會是明智的投資。</span><span class="sxs-lookup"><span data-stu-id="ace3b-129">If that changes by more that X%, then a Cost Management discipline will be a wise investment.</span></span>
- <span data-ttu-id="ace3b-130">過度佈建的觸發條件：某公司認為他們已部署的解決方案是過度佈建。</span><span class="sxs-lookup"><span data-stu-id="ace3b-130">Overprovisioned trigger: A company who believes their deployed solutions are overprovisioned.</span></span> <span data-ttu-id="ace3b-131">在他們能示範正確的佈建標準和資產使用率之前，成本管理會是優先的投資選擇。</span><span class="sxs-lookup"><span data-stu-id="ace3b-131">Cost Management is a priority investment until they can demonstrate proper alignment of provisioning and asset utilization.</span></span>
- <span data-ttu-id="ace3b-132">每月費用觸發條件：某公司將每月花費超過 $x,000 元視為可觀的成本。</span><span class="sxs-lookup"><span data-stu-id="ace3b-132">Monthly spending trigger: A company that spends over $x,000 per month is considered a sizable cost.</span></span> <span data-ttu-id="ace3b-133">如果某月的花費超過該數量，則他們將必須投資成本管理。</span><span class="sxs-lookup"><span data-stu-id="ace3b-133">If spending exceeds that amount in a given month, they will need to invest in Cost Management.</span></span>
- <span data-ttu-id="ace3b-134">年度費用觸發條件：某個具有 IT 研發預算的公司允許每年在雲端測試上支出 $X,000 元。</span><span class="sxs-lookup"><span data-stu-id="ace3b-134">Annual spending trigger: A company with an IT R&D budget that allows for spending $X,000 per year on cloud experimentation.</span></span> <span data-ttu-id="ace3b-135">他們可能會在雲端中執行生產工作負載，但如果預算未超過該金額，這些工作負載仍然會被視為實驗性解決方案。</span><span class="sxs-lookup"><span data-stu-id="ace3b-135">They may run production workloads in the cloud, but they will still be considered experimental solutions if the budget doesn't exceed that amount.</span></span> <span data-ttu-id="ace3b-136">一旦超過後，他們就必須將預算視為生產投資，並密切管理費用。</span><span class="sxs-lookup"><span data-stu-id="ace3b-136">Once it goes over, they will need to treat the budget like a production investment and manage spending closely.</span></span>
- <span data-ttu-id="ace3b-137">不利的 OpEx (不常見)：如果公司的 OpEx 狀況非常不利，將需要有成本管理控制，才能部署開發/測試工作負載。</span><span class="sxs-lookup"><span data-stu-id="ace3b-137">OpEx adverse (uncommon): As a company, they are very OpEx adverse and will need Cost Management controls in place before deploying a dev/test workload.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ace3b-138">後續步驟</span><span class="sxs-lookup"><span data-stu-id="ace3b-138">Next steps</span></span>

<span data-ttu-id="ace3b-139">使用[雲端管理範本](./template.md)，記錄與目前雲端採用方案一致的計量和承受度指標。</span><span class="sxs-lookup"><span data-stu-id="ace3b-139">Using the [Cloud Management template](./template.md), document metrics and tolerance indicators that align to the current cloud adoption plan.</span></span>

<span data-ttu-id="ace3b-140">根據風險和承受度建立流程來治理和傳達成本管理原則的遵循情況。</span><span class="sxs-lookup"><span data-stu-id="ace3b-140">Building on risks and tolerance, establish a process for governing and communicating Cost Management policy adherence.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="ace3b-141">建立原則合規性流程</span><span class="sxs-lookup"><span data-stu-id="ace3b-141">Establish policy compliance processes</span></span>](compliance-processes.md)

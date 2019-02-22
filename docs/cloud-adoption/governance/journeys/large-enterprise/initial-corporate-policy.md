---
title: CAF：大型企業 - 治理策略背後的初始公司原則
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 2/11/2019
description: 大型企業 - 治理策略背後的初始公司原則。
author: BrianBlanchard
ms.openlocfilehash: d3bc31f95229a82cbc2f6ac6e684a87107783c07
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901088"
---
# <a name="large-enterprise-initial-corporate-policy-behind-the-governance-strategy"></a><span data-ttu-id="6b445-103">大型企業：治理策略背後的初始公司原則</span><span class="sxs-lookup"><span data-stu-id="6b445-103">Large enterprise: Initial corporate policy behind the governance strategy</span></span>

<span data-ttu-id="6b445-104">下列公司原則會定義初始治理立場，也就是這趟旅程的起點。</span><span class="sxs-lookup"><span data-stu-id="6b445-104">The following corporate policy defines the initial governance position, which is the starting point for this journey.</span></span> <span data-ttu-id="6b445-105">本文定義初期風險、初始原則聲明，以及強制執行原則聲明的初期流程。</span><span class="sxs-lookup"><span data-stu-id="6b445-105">This article defines early-stage risks, initial policy statements, and early processes to enforce policy statements.</span></span>

> [!NOTE]
><span data-ttu-id="6b445-106">公司原則不是技術文件，但是會推動許多技術決策。</span><span class="sxs-lookup"><span data-stu-id="6b445-106">The corporate policy is not a technical document, but it drives many technical decisions.</span></span> <span data-ttu-id="6b445-107">[概觀](./overview.md)中所述的治理 MVP 最終會衍生自這個原則。</span><span class="sxs-lookup"><span data-stu-id="6b445-107">The governance MVP described in the [overview](./overview.md) ultimately derives from this policy.</span></span> <span data-ttu-id="6b445-108">實作治理 MVP 之前，貴組織應該根據自己的目標和業務風險來開發公司原則。</span><span class="sxs-lookup"><span data-stu-id="6b445-108">Before implementing a governance MVP, your organization should develop a corporate policy based on your own objectives and business risks.</span></span>

## <a name="cloud-governance-team"></a><span data-ttu-id="6b445-109">雲端治理小組</span><span class="sxs-lookup"><span data-stu-id="6b445-109">Cloud Governance team</span></span>

<span data-ttu-id="6b445-110">CIO 最近與 IT 治理小組開會，以了解 PII 和任務關鍵性原則的歷程記錄，及檢閱變更這些原則的影響。</span><span class="sxs-lookup"><span data-stu-id="6b445-110">The CIO recently held a meeting with the IT Governance team to understand the history of the PII and mission-critical policies and review the impact of changing those policies.</span></span> <span data-ttu-id="6b445-111">她也討論了雲端對於 IT 部門和公司的整體潛力。</span><span class="sxs-lookup"><span data-stu-id="6b445-111">She also discussed the overall potential of the cloud for IT and the company.</span></span>

<span data-ttu-id="6b445-112">會議之後，有兩個 IT 治理小組成員要求了研究並支援雲端規劃工作的權限。</span><span class="sxs-lookup"><span data-stu-id="6b445-112">After the meeting, two members of the IT Governance team requested permission to research and support the cloud planning efforts.</span></span> <span data-ttu-id="6b445-113">IT 治理主管認可治理需求以及限制影子 IT 的機會，因此支援這個想法。</span><span class="sxs-lookup"><span data-stu-id="6b445-113">Recognizing the need for governance and an opportunity to limit shadow IT, the Director of IT Governance supported this idea.</span></span> <span data-ttu-id="6b445-114">因此，雲端治理小組於是誕生。</span><span class="sxs-lookup"><span data-stu-id="6b445-114">With that, the Cloud Governance team was born.</span></span> <span data-ttu-id="6b445-115">在接下來的幾個月，他們會從治理的觀點，接手清除在探索雲端期間所造成的許多錯誤。</span><span class="sxs-lookup"><span data-stu-id="6b445-115">Over the next several months, they will inherit the cleanup of many mistakes made during exploration in the cloud from a governance perspective.</span></span> <span data-ttu-id="6b445-116">這讓他們贏得雲端守護者的美名。</span><span class="sxs-lookup"><span data-stu-id="6b445-116">This will earn them the moniker of Cloud Custodians.</span></span> <span data-ttu-id="6b445-117">在後續的發展中，這趟旅程會顯示如何隨著時間變更其角色。</span><span class="sxs-lookup"><span data-stu-id="6b445-117">In later evolutions, this journey will show how their roles change over time.</span></span>

[!INCLUDE [business-risk](../../../../../includes/cloud-adoption/governance/business-risks.md)]

## <a name="tolerance-indicators"></a><span data-ttu-id="6b445-118">承受度指標</span><span class="sxs-lookup"><span data-stu-id="6b445-118">Tolerance indicators</span></span>

<span data-ttu-id="6b445-119">目前的風險承受度很高，且投資雲端治理的意願偏低。</span><span class="sxs-lookup"><span data-stu-id="6b445-119">The current risk tolerance is high and the appetite for investing in cloud governance is low.</span></span> <span data-ttu-id="6b445-120">因此，承受度指標可當作早期警告系統來觸發時間和精力的投資。</span><span class="sxs-lookup"><span data-stu-id="6b445-120">As such, the tolerance indicators act as an early warning system to trigger the investment of time and energy.</span></span> <span data-ttu-id="6b445-121">如果發現下列指標，最好能夠發展治理策略。</span><span class="sxs-lookup"><span data-stu-id="6b445-121">If the following indicators are observed, it would be wise to evolve the governance strategy.</span></span>

- <span data-ttu-id="6b445-122">成本管理：部署到雲端的規模超過 1,000 項資產，或每月支出超過每個月 $10,000 美元。</span><span class="sxs-lookup"><span data-stu-id="6b445-122">Cost Management: Scale of deployment exceeds 1,000 assets to the cloud, or monthly spending exceeds $10,000 USD per month.</span></span>
- <span data-ttu-id="6b445-123">身分識別基準：納入具有舊版或第三方多因素驗證 (MFA) 需求的應用程式。</span><span class="sxs-lookup"><span data-stu-id="6b445-123">Identity Baseline: Inclusion of applications with legacy or third-party multifactor authentication (MFA) requirements.</span></span>
- <span data-ttu-id="6b445-124">安全性基準：在定義的雲端採用計劃中納入受保護的資料。</span><span class="sxs-lookup"><span data-stu-id="6b445-124">Security Baseline: Inclusion of protected data in defined cloud adoption plans.</span></span>
- <span data-ttu-id="6b445-125">資源一致性：在定義的雲端採用計劃中納入任何任務關鍵性應用程式。</span><span class="sxs-lookup"><span data-stu-id="6b445-125">Resource Consistency: Inclusion of any mission-critical applications in defined cloud adoption plans.</span></span>

[!INCLUDE [policy-statements](../../../../../includes/cloud-adoption/governance/policy-statements.md)]

## <a name="next-steps"></a><span data-ttu-id="6b445-126">後續步驟</span><span class="sxs-lookup"><span data-stu-id="6b445-126">Next steps</span></span>

<span data-ttu-id="6b445-127">此公司原則會籌備雲端治理小組來實作治理 MVP，這會是採用基礎。</span><span class="sxs-lookup"><span data-stu-id="6b445-127">This corporate policy prepares the Cloud Governance team to implement the governance MVP, which will be the foundation for adoption.</span></span> <span data-ttu-id="6b445-128">下一步是實作此 MVP。</span><span class="sxs-lookup"><span data-stu-id="6b445-128">The next step is to implement this MVP.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="6b445-129">最佳做法說明</span><span class="sxs-lookup"><span data-stu-id="6b445-129">Best practice explained</span></span>](./best-practice-explained.md)
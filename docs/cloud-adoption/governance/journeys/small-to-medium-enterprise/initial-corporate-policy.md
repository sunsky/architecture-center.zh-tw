---
title: CAF：小型至中型企業的初始控管策略背後的公司原則
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 小型至中型企業的初始控管策略背後的公司原則
author: BrianBlanchard
ms.openlocfilehash: 2133145c9933ad450ea3cc55ecd68b8a667df783
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640017"
---
# <a name="small-to-medium-enterprise-initial-corporate-policy-behind-the-governance-strategy"></a><span data-ttu-id="483e9-103">中小型企業：治理策略背後的初始公司原則</span><span class="sxs-lookup"><span data-stu-id="483e9-103">Small-to-medium enterprise: Initial corporate policy behind the governance strategy</span></span>

<span data-ttu-id="483e9-104">下列公司原則會定義初始治理立場，也就是這趟旅程的起點。</span><span class="sxs-lookup"><span data-stu-id="483e9-104">The following corporate policy defines an initial governance position, which is the starting point for this journey.</span></span> <span data-ttu-id="483e9-105">本文定義初期風險、初始原則聲明，以及強制執行原則聲明的初期流程。</span><span class="sxs-lookup"><span data-stu-id="483e9-105">This article defines early-stage risks, initial policy statements, and early processes to enforce policy statements.</span></span>

> [!NOTE]
><span data-ttu-id="483e9-106">公司原則不是技術文件，但是會推動許多技術決策。</span><span class="sxs-lookup"><span data-stu-id="483e9-106">The corporate policy is not a technical document, but it drives many technical decisions.</span></span> <span data-ttu-id="483e9-107">[概觀](./overview.md)中所述的治理 MVP 最終會衍生自這個原則。</span><span class="sxs-lookup"><span data-stu-id="483e9-107">The governance MVP described in the [overview](./overview.md) ultimately derives from this policy.</span></span> <span data-ttu-id="483e9-108">實作治理 MVP 之前，貴組織應該根據自己的目標和業務風險來開發公司原則。</span><span class="sxs-lookup"><span data-stu-id="483e9-108">Before implementing a governance MVP, your organization should develop a corporate policy based on your own objectives and business risks.</span></span>

## <a name="cloud-governance-team"></a><span data-ttu-id="483e9-109">雲端治理小組</span><span class="sxs-lookup"><span data-stu-id="483e9-109">Cloud Governance team</span></span>

<span data-ttu-id="483e9-110">在此敘述中，雲端治理小組是由兩個系統管理員 (辨識治理的需求) 所組成。</span><span class="sxs-lookup"><span data-stu-id="483e9-110">In this narrative, the Cloud Governance team is comprised of two systems administrators who have recognized the need for governance.</span></span> <span data-ttu-id="483e9-111">接下來幾個月，他們會繼承清除公司雲端目前狀態治理的作業，為他們贏得雲端監管人的稱謂。</span><span class="sxs-lookup"><span data-stu-id="483e9-111">Over the next several months, they will inherit the job of cleaning up the governance of the company’s cloud presence, earning them the title of Cloud Custodians.</span></span> <span data-ttu-id="483e9-112">在後續演進中，這個稱謂很有可能會變更。</span><span class="sxs-lookup"><span data-stu-id="483e9-112">In subsequent evolutions, this title will likely change.</span></span>

[!INCLUDE [business-risk](../../../../../includes/cloud-adoption/governance/business-risks.md)]

## <a name="tolerance-indicators"></a><span data-ttu-id="483e9-113">承受度指標</span><span class="sxs-lookup"><span data-stu-id="483e9-113">Tolerance indicators</span></span>

<span data-ttu-id="483e9-114">目前的風險承受度很高，且投資雲端治理的意願偏低。</span><span class="sxs-lookup"><span data-stu-id="483e9-114">The current tolerance for risk is high and the appetite for investing in cloud governance is low.</span></span> <span data-ttu-id="483e9-115">因此，承受度指標可當作早期警告系統來觸發更多時間和精力的投資。</span><span class="sxs-lookup"><span data-stu-id="483e9-115">As such, the tolerance indicators act as an early warning system to trigger more investment of time and energy.</span></span> <span data-ttu-id="483e9-116">如果發現下列指標，您應該發展治理策略。</span><span class="sxs-lookup"><span data-stu-id="483e9-116">If and when the following indicators are observed, you should evolve the governance strategy.</span></span>

- <span data-ttu-id="483e9-117">成本管理：部署到雲端的規模超過 100 項資產，或每月支出超過每個月 $1,000 美元。</span><span class="sxs-lookup"><span data-stu-id="483e9-117">Cost Management: The scale of deployment exceeds 100 assets to the cloud, or monthly spending exceeds $1,000 USD per month.</span></span>
- <span data-ttu-id="483e9-118">安全性基準：在定義的雲端採用計劃中納入受保護的資料。</span><span class="sxs-lookup"><span data-stu-id="483e9-118">Security Baseline: Inclusion of protected data in defined cloud adoption plans.</span></span>
- <span data-ttu-id="483e9-119">資源一致性：在定義的雲端採用計劃中納入任何任務關鍵性應用程式。</span><span class="sxs-lookup"><span data-stu-id="483e9-119">Resource Consistency: Inclusion of any mission-critical applications in defined cloud adoption plans.</span></span>

[!INCLUDE [policy-statements](../../../../../includes/cloud-adoption/governance/policy-statements.md)]

## <a name="next-steps"></a><span data-ttu-id="483e9-120">後續步驟</span><span class="sxs-lookup"><span data-stu-id="483e9-120">Next steps</span></span>

<span data-ttu-id="483e9-121">此公司原則會籌備雲端治理小組來實作治理 MVP，這會是採用基礎。</span><span class="sxs-lookup"><span data-stu-id="483e9-121">This corporate policy prepares the Cloud Governance team to implement the governance MVP, which will be the foundation for adoption.</span></span> <span data-ttu-id="483e9-122">下一步是實作此 MVP。</span><span class="sxs-lookup"><span data-stu-id="483e9-122">The next step is to implement this MVP.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="483e9-123">最佳做法說明</span><span class="sxs-lookup"><span data-stu-id="483e9-123">Best practice explained</span></span>](./best-practice-explained.md)
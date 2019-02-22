---
title: CAF：成本管理動機和業務風險
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 成本管理動機和業務風險
author: BrianBlanchard
ms.openlocfilehash: b9bf31a796ea1a7530c6a668a231d74b9b765509
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900928"
---
# <a name="cost-management-motivations-and-business-risks"></a><span data-ttu-id="54246-103">成本管理動機和業務風險</span><span class="sxs-lookup"><span data-stu-id="54246-103">Cost Management motivations and business risks</span></span>

<span data-ttu-id="54246-104">本文將討論客戶通常會在雲端治理策略中採用成本管理專業領域的原因。</span><span class="sxs-lookup"><span data-stu-id="54246-104">This article discusses the reasons that customers typically adopt a Cost Management discipline within a cloud governance strategy.</span></span> <span data-ttu-id="54246-105">它也提供數個衍生原則聲明的業務風險範例。</span><span class="sxs-lookup"><span data-stu-id="54246-105">It also provides a few examples of business risks that drive policy statements.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="is-cost-management-relevant"></a><span data-ttu-id="54246-106">是否與成本管理相關？</span><span class="sxs-lookup"><span data-stu-id="54246-106">Is Cost Management relevant?</span></span>

<span data-ttu-id="54246-107">在成本治理方面，採用雲端會建立範式轉移。</span><span class="sxs-lookup"><span data-stu-id="54246-107">In terms of cost governance, cloud adoption creates a paradigm shift.</span></span> <span data-ttu-id="54246-108">傳統內部部署世界中的成本管理會以下列各項為基礎：重新整理週期、資料中心收購、主機更新，以及週期性維護問題。</span><span class="sxs-lookup"><span data-stu-id="54246-108">Management of cost in a traditional on-premises world is based on refresh cycles, datacenter acquisitions, host renewals, and recurring maintenance issues.</span></span> <span data-ttu-id="54246-109">您可以預測、規劃和精簡這其中每一個成本，以便與每年的資本支出預算保持一致。</span><span class="sxs-lookup"><span data-stu-id="54246-109">You can forecast, plan, and refine each of these costs to align with annual capital expenditure budgets.</span></span>

<span data-ttu-id="54246-110">對於雲端解決方案，許多企業都傾向於對成本管理採用更具反應性的方法。</span><span class="sxs-lookup"><span data-stu-id="54246-110">For cloud solutions, many businesses tend to take a more reactive approach to Cost Management.</span></span> <span data-ttu-id="54246-111">在許多情況下，企業將會預購 (或承諾使用) 一定數量的雲端服務。</span><span class="sxs-lookup"><span data-stu-id="54246-111">In many cases, businesses will prepurchase, or commit to use, a set amount of cloud services.</span></span> <span data-ttu-id="54246-112">此模型假設要根據企業規劃來針對特定雲端廠商花費的費用來取得最大折扣，即會建立對已規劃之主動式成本週期的認知。</span><span class="sxs-lookup"><span data-stu-id="54246-112">This model assumes that maximizing discounts, based on how much the business plans on spending with a specific cloud vendor, creates the perception of a proactive, planned cost cycle.</span></span> <span data-ttu-id="54246-113">不過，該認知只有在企業也會實作成熟的成本管理專業領域時成真。</span><span class="sxs-lookup"><span data-stu-id="54246-113">However, that perception will only become a reality if the business also implements mature Cost Management disciplines.</span></span>

<span data-ttu-id="54246-114">雲端提供在傳統內部部署資料中心前所未聞的自助功能。</span><span class="sxs-lookup"><span data-stu-id="54246-114">The cloud offers self-service capabilities that were previously unheard of in traditional on-premises datacenters.</span></span> <span data-ttu-id="54246-115">這些新功能讓企業能夠更靈活、更少限制且更開放地採用新技術。</span><span class="sxs-lookup"><span data-stu-id="54246-115">These new capabilities empower businesses to be more agile, less restrictive, and more open to adopt new technologies.</span></span> <span data-ttu-id="54246-116">不過，自助的缺點是終端使用者會在不知情的情況下超過已配置的預算。</span><span class="sxs-lookup"><span data-stu-id="54246-116">However, the downside of self-service is that end users can unknowingly exceed allocated budgets.</span></span> <span data-ttu-id="54246-117">相反地，相同的使用者可以體驗方案中的變化，並且意外地不使用所預測的雲端服務數量。</span><span class="sxs-lookup"><span data-stu-id="54246-117">Conversely, the same users can experience a change in plans and unexpectedly not use the amount of cloud services forecasted.</span></span> <span data-ttu-id="54246-118">在治理小組內，任一個方向的轉移可能性，會證明成本管理專業領域中投資的正當性。</span><span class="sxs-lookup"><span data-stu-id="54246-118">The potential of shift in either direction justifies investment in a Cost Management discipline within the governance team.</span></span>

## <a name="business-risk"></a><span data-ttu-id="54246-119">業務風險</span><span class="sxs-lookup"><span data-stu-id="54246-119">Business risk</span></span>

<span data-ttu-id="54246-120">成本管理專業領域會嘗試解決與在裝載雲端式工作負載時所產生費用相關的核心業務風險。</span><span class="sxs-lookup"><span data-stu-id="54246-120">The Cost Management discipline attempts to address core business risks related to expenses incurred when hosting cloud-based workloads.</span></span> <span data-ttu-id="54246-121">在您規劃和實作雲端部署時，與您的企業一起識別這些風險，並監視它們的關聯性。</span><span class="sxs-lookup"><span data-stu-id="54246-121">Work with your business to identify these risks and monitor each of them for relevance as you plan for and implement your cloud deployments.</span></span>

<span data-ttu-id="54246-122">組織之間的風險各不相同，但是下列項目可成為與成本相關的常見風險，可讓您在雲端治理小組內用來作為討論的起點：</span><span class="sxs-lookup"><span data-stu-id="54246-122">Risks will differ between organization, but the following serve as common cost-related risks that you can use as a starting point for discussions within your Cloud Governance team:</span></span>

- <span data-ttu-id="54246-123">**預算控制**。</span><span class="sxs-lookup"><span data-stu-id="54246-123">**Budget control**.</span></span> <span data-ttu-id="54246-124">未控制預算，可能導致對於雲端廠商的過度支出。</span><span class="sxs-lookup"><span data-stu-id="54246-124">Not controlling budget can lead to excessive spending with a cloud vendor.</span></span>
- <span data-ttu-id="54246-125">**損失使用量**。</span><span class="sxs-lookup"><span data-stu-id="54246-125">**Utilization loss**.</span></span> <span data-ttu-id="54246-126">未使用預購或預先承諾，可能導致投資浪費。</span><span class="sxs-lookup"><span data-stu-id="54246-126">Prepurchases or precommitments that are not used can result in wasted investments.</span></span>
- <span data-ttu-id="54246-127">**支出異常狀況**：在任一個方向中以未預期的方式突然增加，即為不當使用的指標。</span><span class="sxs-lookup"><span data-stu-id="54246-127">**Spending anomalies**: Unexpected spikes in either direction can be indicators of improper usage.</span></span>
- <span data-ttu-id="54246-128">**過度佈建的資產**。</span><span class="sxs-lookup"><span data-stu-id="54246-128">**Overprovisioned assets**.</span></span> <span data-ttu-id="54246-129">在超過應用程式或虛擬機器 (VM) 需求的設定中部署資產時，它們會增加成本並形成浪費。</span><span class="sxs-lookup"><span data-stu-id="54246-129">When assets are deployed in a configuration that exceed the needs of an application or virtual machine (VM), they can increase costs and create waste.</span></span>

## <a name="next-steps"></a><span data-ttu-id="54246-130">後續步驟</span><span class="sxs-lookup"><span data-stu-id="54246-130">Next steps</span></span>

<span data-ttu-id="54246-131">使用[雲端管理範本](./template.md)，記載目前雲端採用方案可能引入的業務風險。</span><span class="sxs-lookup"><span data-stu-id="54246-131">Using the [Cloud Management Template](./template.md), document business risks that are likely to be introduced by the current cloud adoption plan.</span></span>

<span data-ttu-id="54246-132">一旦建立對於實際業務風險的了解之後，下一步就是記載風險的業務承受度與用來監視該承受度的指標和關鍵計量。</span><span class="sxs-lookup"><span data-stu-id="54246-132">Once an understanding of realistic business risks is established, the next step is to document the business's tolerance for risk and the indicators and key metrics to monitor that tolerance.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="54246-133">了解指標、計量及風險承受度</span><span class="sxs-lookup"><span data-stu-id="54246-133">Understand indicators, metrics, and risk tolerance</span></span>](./metrics-tolerance.md)

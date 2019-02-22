---
title: CAF：安全性基準動機和業務風險
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 1/8/2019
description: 安全性基準動機和業務風險
author: BrianBlanchard
ms.openlocfilehash: d57b79b37aa257d07df0168a7fee53ec8ecd1526
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900439"
---
# <a name="security-baseline-motivations-and-business-risks"></a><span data-ttu-id="84fc9-103">安全性基準動機和業務風險</span><span class="sxs-lookup"><span data-stu-id="84fc9-103">Security Baseline motivations and business risks</span></span>

<span data-ttu-id="84fc9-104">本文討論客戶通常會在雲端治理策略中採用安全性基準專業領域的原因。</span><span class="sxs-lookup"><span data-stu-id="84fc9-104">This article discusses the reasons that customers typically adopt a Security Baseline discipline within a cloud governance strategy.</span></span> <span data-ttu-id="84fc9-105">它也提供幾個能驅使原則聲明之潛在商務風險的範例。</span><span class="sxs-lookup"><span data-stu-id="84fc9-105">It also provides a few examples of potential business risks that can drive policy statements.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="is-a-security-baseline-relevant"></a><span data-ttu-id="84fc9-106">安全性基準是否相關？</span><span class="sxs-lookup"><span data-stu-id="84fc9-106">Is a Security Baseline relevant?</span></span>

<span data-ttu-id="84fc9-107">安全性是任何 IT 組織的一大考量。</span><span class="sxs-lookup"><span data-stu-id="84fc9-107">Security is a key concern for any IT organization.</span></span> <span data-ttu-id="84fc9-108">雲端部署面臨許多與裝載於傳統內部部署資料中心的工作負載相同的安全性風險。</span><span class="sxs-lookup"><span data-stu-id="84fc9-108">Cloud deployments face many of the same security risks as workloads hosted in traditional on-premises datacenters.</span></span> <span data-ttu-id="84fc9-109">不過，公用雲端平台的性質，缺乏對儲存和執行工作負載之實體硬體的直接擁有權，意味著雲端安全性需要其本身的原則和程序。</span><span class="sxs-lookup"><span data-stu-id="84fc9-109">However, the nature of public cloud platforms, with a lack of direct ownership of the physical hardware storing and running your workloads, means cloud security requires its own policy and processes.</span></span>

<span data-ttu-id="84fc9-110">區分雲端安全性治理與傳統安全性原則的主要原因之一是可以輕鬆地建立資源，如果在部署之前未考慮安全性，則可能會增加漏洞。</span><span class="sxs-lookup"><span data-stu-id="84fc9-110">One of the primary things that set cloud security governance apart from traditional security policy is the ease with which resources can be created, potentially adding vulnerabilities if security isn't considered before deployment.</span></span> <span data-ttu-id="84fc9-111">[軟體定義網路 (SDN)](../../decision-guides/software-defined-network/overview.md) 等技術為快速變更雲端架網路拓樸提供的靈活性，也可以輕鬆地以出人意料的方式修改整體網路攻擊面。</span><span class="sxs-lookup"><span data-stu-id="84fc9-111">The flexibility that technologies like [software defined networking (SDN)](../../decision-guides/software-defined-network/overview.md) provide for rapidly changing your cloud-based network topology can also easily modify your overall network attack surface in unforeseen ways.</span></span> <span data-ttu-id="84fc9-112">雲端平台也提供工具和功能，可以不一定可行的方式在內部部署環境中改善您的安全性功能。</span><span class="sxs-lookup"><span data-stu-id="84fc9-112">Cloud platforms also provide tools and features that can improve your security capabilities in ways not always possible in on-premises environments.</span></span>

<span data-ttu-id="84fc9-113">您投入安全性原則和程序的金額，將大量取決於您雲端部署的本質。</span><span class="sxs-lookup"><span data-stu-id="84fc9-113">The amount you invest into security policy and processes will depend a great deal on the nature of your cloud deployment.</span></span> <span data-ttu-id="84fc9-114">初始測試部署可能只需要最基本的安全性原則，而任務關鍵性工作負載則需要解決複雜而廣泛的安全性需求。</span><span class="sxs-lookup"><span data-stu-id="84fc9-114">Initial test deployments may only need the most basic of security policies in place, while a mission-critical workload will entail addressing complex and extensive security needs.</span></span> <span data-ttu-id="84fc9-115">在某種程度上所有部署都需要參與該專業領域。</span><span class="sxs-lookup"><span data-stu-id="84fc9-115">All deployments will need to engage with the discipline at some level.</span></span>

<span data-ttu-id="84fc9-116">安全性基準專業領域涵蓋了您可以採取的公司原則和手動程序，以保護您的雲端部署免受安全性風險的影響。</span><span class="sxs-lookup"><span data-stu-id="84fc9-116">The Security Baseline discipline covers the corporate policies and manual processes that you can put in place to protect your cloud deployment against security risks.</span></span>

> [!NOTE]
><span data-ttu-id="84fc9-117">雖然在安全性基準內容下理解[身分識別基準](../identity-baseline/overview.md)以及它與存取控制的關係非常重要，但[五個雲端治理專業領域](../overview.md)會呼叫[身分識別基準](../identity-baseline/overview.md)作為自己的專業領域，與安全性基準區隔。</span><span class="sxs-lookup"><span data-stu-id="84fc9-117">While it is important to understand [Identity Baseline](../identity-baseline/overview.md) in the context of Security Baseline and how that relates to Access Control, the [Five Disciplines of Cloud Governance](../overview.md) calls out [Identity Baseline](../identity-baseline/overview.md) as its own discipline, separate from Security Baseline.</span></span>

## <a name="business-risk"></a><span data-ttu-id="84fc9-118">業務風險</span><span class="sxs-lookup"><span data-stu-id="84fc9-118">Business risk</span></span>

<span data-ttu-id="84fc9-119">安全性基準專業領域會嘗試解決與核心安全性相關的商業風險。</span><span class="sxs-lookup"><span data-stu-id="84fc9-119">The Security Baseline discipline attempts to address core security-related business risks.</span></span> <span data-ttu-id="84fc9-120">在您規劃和實作雲端部署時，與您的企業一起識別這些風險，並監視它們的關聯性。</span><span class="sxs-lookup"><span data-stu-id="84fc9-120">Work with your business to identify these risks and monitor each of them for relevance as you plan for and implement your cloud deployments.</span></span>

<span data-ttu-id="84fc9-121">組織之間的風險不同，但是下列項目可作為通用安全性相關風險，供您在雲端治理小組內討論時作為起點：</span><span class="sxs-lookup"><span data-stu-id="84fc9-121">Risks will differ between organization, but the following serve as common security-related risks that you can use as a starting point for discussions within your Cloud Governance team:</span></span>

- <span data-ttu-id="84fc9-122">**資料外洩**。</span><span class="sxs-lookup"><span data-stu-id="84fc9-122">**Data breach**.</span></span> <span data-ttu-id="84fc9-123">意外暴露或遺失裝載於雲端的敏感性資料，可能會導致失去客戶、合約問題或法律責任。</span><span class="sxs-lookup"><span data-stu-id="84fc9-123">Inadvertent exposure or loss of sensitive cloud-hosted data can lead to losing customers, contractual issues, or legal consequences.</span></span>
- <span data-ttu-id="84fc9-124">**服務中斷**。</span><span class="sxs-lookup"><span data-stu-id="84fc9-124">**Service disruption**.</span></span> <span data-ttu-id="84fc9-125">由於不安全的基礎結構所導致的中斷和其他效能問題會中斷正常作業，並可能導致產能損失或業務損失。</span><span class="sxs-lookup"><span data-stu-id="84fc9-125">Outages and other performance issues due to insecure infrastructure interrupts normal operations and can result in lost productivity or lost business.</span></span>

## <a name="next-steps"></a><span data-ttu-id="84fc9-126">後續步驟</span><span class="sxs-lookup"><span data-stu-id="84fc9-126">Next steps</span></span>

<span data-ttu-id="84fc9-127">使用[雲端管理範本](./template.md)，記錄目前雲端採用方案可能會引入的商務風險。</span><span class="sxs-lookup"><span data-stu-id="84fc9-127">Using the [Cloud Management Template](./template.md), document business risks that are likely to be introduced by the current cloud adoption plan.</span></span>

<span data-ttu-id="84fc9-128">一旦建立對於實際商務風險的了解，下一步是記錄風險的業務承受度與用來監視承受度的指標和關鍵計量。</span><span class="sxs-lookup"><span data-stu-id="84fc9-128">Once an understanding of realistic business risks is established, the next step is to document the business's tolerance for risk and the indicators and key metrics to monitor that tolerance.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="84fc9-129">了解指標、計量及風險承受度</span><span class="sxs-lookup"><span data-stu-id="84fc9-129">Understand indicators, metrics, and risk tolerance</span></span>](./metrics-tolerance.md)

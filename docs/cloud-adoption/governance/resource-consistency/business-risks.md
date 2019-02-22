---
title: CAF：資源一致性動機和商務風險
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 資源一致性動機和商務風險
author: alexbuckgit
ms.openlocfilehash: 19e0d761e4afa3473099bde2edc960c8b9eadb79
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900916"
---
# <a name="resource-consistency-motivations-and-business-risks"></a><span data-ttu-id="54288-103">資源一致性動機和商務風險</span><span class="sxs-lookup"><span data-stu-id="54288-103">Resource Consistency motivations and business risks</span></span>

<span data-ttu-id="54288-104">本文討論客戶通常會在雲端治理策略中採用資源一致性專業領域的原因。</span><span class="sxs-lookup"><span data-stu-id="54288-104">This article discusses the reasons that customers typically adopt a Resource Consistency discipline within a cloud governance strategy.</span></span> <span data-ttu-id="54288-105">它也提供幾個能驅使原則聲明之潛在商務風險的範例。</span><span class="sxs-lookup"><span data-stu-id="54288-105">It also provides a few examples of potential business risks that can drive policy statements.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="is-resource-consistency-relevant"></a><span data-ttu-id="54288-106">資源一致性有相關性嗎？</span><span class="sxs-lookup"><span data-stu-id="54288-106">Is Resource Consistency relevant?</span></span>

<span data-ttu-id="54288-107">談到部署資源和工作負載，雲端提供比多數傳統內部部署資料中心更高的靈活度和彈性。</span><span class="sxs-lookup"><span data-stu-id="54288-107">When it comes to deploying resources and workloads, the cloud offers increased agility and flexibility over most traditional on-premises datacenters.</span></span> <span data-ttu-id="54288-108">不過，這些潛在的雲端優勢也伴隨潛在管理缺點，可能嚴重危害您雲端採用的成功。</span><span class="sxs-lookup"><span data-stu-id="54288-108">However, these potential cloud-based advantages also come paired with potential management drawbacks that can seriously jeopardize the success of your cloud adoption.</span></span> <span data-ttu-id="54288-109">您已經部署哪些資產？</span><span class="sxs-lookup"><span data-stu-id="54288-109">What assets have you deployed?</span></span> <span data-ttu-id="54288-110">哪些小組擁有何種資產？</span><span class="sxs-lookup"><span data-stu-id="54288-110">What teams own what assets?</span></span> <span data-ttu-id="54288-111">您有足夠支援工作負載的資源嗎？</span><span class="sxs-lookup"><span data-stu-id="54288-111">Do you have enough resources supporting a workload?</span></span> <span data-ttu-id="54288-112">如何知道工作負載是否狀況良好？</span><span class="sxs-lookup"><span data-stu-id="54288-112">How do you know if workloads are healthy?</span></span>

<span data-ttu-id="54288-113">資源一致性非常重要，可採用一致且可重複的方式確認資源已部署、更新及設定，並確保服務中斷降到最低且盡可能在最短時間內補救。</span><span class="sxs-lookup"><span data-stu-id="54288-113">Resource Consistency is crucial to ensure that resources are deployed, updated, and configured consistently and repeatably, and that service disruptions are minimized and remedied in as little time as possible.</span></span>

<span data-ttu-id="54288-114">資源一致性專業領域渉及識別及降低與您雲端部署作業層面相關的商務風險。</span><span class="sxs-lookup"><span data-stu-id="54288-114">The Resource Consistency discipline is concerned with identifying and mitigating business risks related to the operational aspects of your cloud deployment.</span></span> <span data-ttu-id="54288-115">資源一致性包含應用程式、工作負載和資產效能的監視。</span><span class="sxs-lookup"><span data-stu-id="54288-115">Resource Consistency includes monitoring of applications, workloads, and asset performance.</span></span> <span data-ttu-id="54288-116">它也包含滿足擴展需求、補救效能服務等級協定 (SLA) 違規和藉由自動補救的方式主動避免效能 SLA 違規所需任務。</span><span class="sxs-lookup"><span data-stu-id="54288-116">It also includes the tasks required to meet scale demands, remediate performance Service Level Agreement (SLA) violations, and proactively avoid performance SLA violations through automated remediation.</span></span>

<span data-ttu-id="54288-117">初始測試部署可能只需採用某些粗略命名和標記標準來支援您的資源一致性需求。</span><span class="sxs-lookup"><span data-stu-id="54288-117">Initial test deployments may not require much beyond adopting some cursory naming and tagging standards to support your Resource Consistency needs.</span></span> <span data-ttu-id="54288-118">隨著您的雲端採用日趨成熟並部署更複雜任務關鍵性資產，在資源一致性專業領域的投資需求會快速增加。</span><span class="sxs-lookup"><span data-stu-id="54288-118">As your cloud adoption matures and you deploy more complicated and mission-critical assets, the need to invest in the Resource Consistency discipline increases rapidly.</span></span>

## <a name="business-risk"></a><span data-ttu-id="54288-119">商務風險</span><span class="sxs-lookup"><span data-stu-id="54288-119">Business risk</span></span>

<span data-ttu-id="54288-120">資源一致性專業領域會嘗試解決核心作業商務風險。</span><span class="sxs-lookup"><span data-stu-id="54288-120">The Resource Consistency discipline attempts to address core operational business risks.</span></span> <span data-ttu-id="54288-121">在您規劃和實作雲端部署時，與您的企業和 IT 小組一起識別這些風險，並監視它們的關聯性。</span><span class="sxs-lookup"><span data-stu-id="54288-121">Work with your business and IT teams to identify these risks and monitor each of them for relevance as you plan for and implement your cloud deployments.</span></span>

<span data-ttu-id="54288-122">組織之間的風險各不相同，但是下列項目可作為常見風險，供您在雲端治理小組內討論時作為起點：</span><span class="sxs-lookup"><span data-stu-id="54288-122">Risks will differ between organization, but the following serve as common risks that you can use as a starting point for discussions within your Cloud Governance team:</span></span>

- <span data-ttu-id="54288-123">**不必要的作業成本**。</span><span class="sxs-lookup"><span data-stu-id="54288-123">**Unnecessary operational cost**.</span></span> <span data-ttu-id="54288-124">已淘汰、未使用或在低需求期間過度佈建的資源會增加不必要作業成本。</span><span class="sxs-lookup"><span data-stu-id="54288-124">Obsolete or unused resources, or resources that are overprovisioned during times of low demand, add unnecessary operational costs.</span></span>
- <span data-ttu-id="54288-125">**佈建不足的資源**。</span><span class="sxs-lookup"><span data-stu-id="54288-125">**Underprovisioned resources**.</span></span> <span data-ttu-id="54288-126">高於預期需求的資源可能會造成業務中斷，因為雲端資源無法負荷需求。</span><span class="sxs-lookup"><span data-stu-id="54288-126">Resources that experience higher than anticipated demand can result in business disruption as cloud resources are overwhelmed by demand.</span></span>
- <span data-ttu-id="54288-127">**管理效率不足**。</span><span class="sxs-lookup"><span data-stu-id="54288-127">**Management inefficiencies**.</span></span> <span data-ttu-id="54288-128">缺乏一致的資源相關命名和標記中繼資料可能導致 IT 人員難以尋找管理工作所需資源或辨識資產相關所有權及會計資訊。</span><span class="sxs-lookup"><span data-stu-id="54288-128">Lack of consistent naming and tagging metadata associated with resources can lead to IT staff having difficulty finding resources for management tasks or identifying ownership and accounting information related to assets.</span></span> <span data-ttu-id="54288-129">這會導致管理效率不足的情況，其可能會增加成本並減緩 IT 對於服務中斷或其他作業問題的回應能力。</span><span class="sxs-lookup"><span data-stu-id="54288-129">This results in management inefficiencies that can increase cost and slow IT responsiveness to service disruption or other operational issues.</span></span>
- <span data-ttu-id="54288-130">**業務中斷**。</span><span class="sxs-lookup"><span data-stu-id="54288-130">**Business Interruption**.</span></span> <span data-ttu-id="54288-131">造成您組織既有服務等級協定 (SLA) 違規的服務中斷可能會導致公司的企業損失或其他財務影響。</span><span class="sxs-lookup"><span data-stu-id="54288-131">Service disruptions that result in violations of your organization's established Service Level Agreements (SLAs) can result in loss of business or other financial impacts to your company.</span></span>

## <a name="next-steps"></a><span data-ttu-id="54288-132">後續步驟</span><span class="sxs-lookup"><span data-stu-id="54288-132">Next steps</span></span>

<span data-ttu-id="54288-133">使用[雲端管理範本](./template.md)，記錄目前雲端採用方案可能會引入的商務風險。</span><span class="sxs-lookup"><span data-stu-id="54288-133">Using the [Cloud Management Template](./template.md), document business risks that are likely to be introduced by the current cloud adoption plan.</span></span>

<span data-ttu-id="54288-134">一旦建立對於實際商務風險的了解，下一步是記錄風險的業務承受度與用來監視承受度的指標和關鍵計量。</span><span class="sxs-lookup"><span data-stu-id="54288-134">Once an understanding of realistic business risks is established, the next step is to document the business's tolerance for risk and the indicators and key metrics to monitor that tolerance.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="54288-135">了解指標、計量及風險承受度</span><span class="sxs-lookup"><span data-stu-id="54288-135">Understand indicators, metrics, and risk tolerance</span></span>](./metrics-tolerance.md)

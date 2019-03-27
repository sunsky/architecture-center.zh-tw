---
title: CAF：驅使部署加速的動機和業務風險
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 深入了解雲端治理策略一部分的部署加速專業領域。
author: alexbuckgit
ms.openlocfilehash: 854b1fd420de605a665922e9b207e6aecbfab2f0
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58242139"
---
# <a name="deployment-acceleration-motivations-and-business-risks"></a><span data-ttu-id="10ed2-103">部署加速動機和業務風險</span><span class="sxs-lookup"><span data-stu-id="10ed2-103">Deployment Acceleration motivations and business risks</span></span>

<span data-ttu-id="10ed2-104">本文討論客戶通常會在雲端治理策略中採用部署加速專業領域的原因。</span><span class="sxs-lookup"><span data-stu-id="10ed2-104">This article discusses the reasons that customers typically adopt a Deployment Acceleration discipline within a cloud governance strategy.</span></span> <span data-ttu-id="10ed2-105">它也提供幾個驅使原則聲明之業務風險的範例。</span><span class="sxs-lookup"><span data-stu-id="10ed2-105">It also provides a few examples of business risks that drive policy statements.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="is-deployment-acceleration-relevant"></a><span data-ttu-id="10ed2-106">部署加速是否相關？</span><span class="sxs-lookup"><span data-stu-id="10ed2-106">Is Deployment Acceleration relevant?</span></span>

<span data-ttu-id="10ed2-107">內部部署系統通常會使用基準映像或安裝指令碼來部署。</span><span class="sxs-lookup"><span data-stu-id="10ed2-107">On-premises systems are often deployed using baseline images or installation scripts.</span></span> <span data-ttu-id="10ed2-108">通常需要額外設定，這可能會牽涉到多個步驟或人為介入。</span><span class="sxs-lookup"><span data-stu-id="10ed2-108">Additional configuration is usually necessary, which may involve multiple steps or human intervention.</span></span> <span data-ttu-id="10ed2-109">這些手動流程容易出錯，經常會導致「設定漂移」，需要耗時進行疑難排解和補救工作。</span><span class="sxs-lookup"><span data-stu-id="10ed2-109">These manual processes are error-prone and often result in "configuration drift", requiring time-consuming troubleshooting and remediation tasks.</span></span>

<span data-ttu-id="10ed2-110">大部分 Azure 資源可以透過 Azure 入口網站以手動方式部署及設定。</span><span class="sxs-lookup"><span data-stu-id="10ed2-110">Most Azure resources can be deployed and configured manually via the Azure portal.</span></span> <span data-ttu-id="10ed2-111">當您只有一些資源要管理的時候，這種方法對於您的需求可能就已足夠。</span><span class="sxs-lookup"><span data-stu-id="10ed2-111">This approach may be sufficient for your needs when only have a few resources to manage.</span></span> <span data-ttu-id="10ed2-112">不過，隨著您的雲端資產成長，貴組織應該將您的雲端資源部署自動化，以便利用 Azure 提供的調整、容錯移轉和災害復原功能。</span><span class="sxs-lookup"><span data-stu-id="10ed2-112">However, as your cloud estate grows, your organization should automate the deployment of your cloud resources to take advantage of the scaling, failover, and disaster recovery capabilities that Azure provides.</span></span> <span data-ttu-id="10ed2-113">採用 DevOps 或 DevSecOps 方法通常是管理您的部署的最佳方法。</span><span class="sxs-lookup"><span data-stu-id="10ed2-113">Adopting a DevOps or DevSecOps approach is often the best way to manage your deployments.</span></span>

<span data-ttu-id="10ed2-114">強固的部署加速計劃可確保您的雲端資源正確且一致地部署、更新及設定，並且保持如此。</span><span class="sxs-lookup"><span data-stu-id="10ed2-114">A robust Deployment Acceleration plan ensures that your cloud resources are deployed, updated, and configured correctly and consistently, and remain that way.</span></span> <span data-ttu-id="10ed2-115">您的部署加速策略的成熟度，也會是您[成本管理策略](../cost-management/overview.md)中的重要因素。</span><span class="sxs-lookup"><span data-stu-id="10ed2-115">The maturity of your Deployment Acceleration strategy can also be a significant factor in your [Cost Management strategy](../cost-management/overview.md).</span></span> <span data-ttu-id="10ed2-116">自動化佈建及設定您的雲端資源，可讓您在需求較低或有時間限制時相應減少或解除配置資源，讓您僅需支付所需資源的費用。</span><span class="sxs-lookup"><span data-stu-id="10ed2-116">Automated provisioning and configuration of your cloud resources allows you to scale down or deallocate resources when demand is low or time-bound, so you only pay for resources as you need them.</span></span>

## <a name="business-risk"></a><span data-ttu-id="10ed2-117">業務風險</span><span class="sxs-lookup"><span data-stu-id="10ed2-117">Business risk</span></span>

<span data-ttu-id="10ed2-118">部署加速專業領域會嘗試解決下列業務風險。</span><span class="sxs-lookup"><span data-stu-id="10ed2-118">The Deployment Acceleration discipline attempts to address the following business risks.</span></span> <span data-ttu-id="10ed2-119">在雲端採用期間，監視下列各項的相關性：</span><span class="sxs-lookup"><span data-stu-id="10ed2-119">During cloud adoption, monitor each of the following for relevance:</span></span>

- <span data-ttu-id="10ed2-120">**服務中斷**。</span><span class="sxs-lookup"><span data-stu-id="10ed2-120">**Service disruption**.</span></span> <span data-ttu-id="10ed2-121">缺乏可預測的可重複部署流程或未受管理的系統設定變更，會中斷正常作業以及導致遺失產能或遺失商務。</span><span class="sxs-lookup"><span data-stu-id="10ed2-121">Lack of predictable repeatable deployment processes or unmanaged changes to system configurations can disrupt normal operations and can result in lost productivity or lost business.</span></span>
- <span data-ttu-id="10ed2-122">**成本超支**。</span><span class="sxs-lookup"><span data-stu-id="10ed2-122">**Cost overruns**.</span></span> <span data-ttu-id="10ed2-123">系統資源設定中非預期的變更會讓識別問題的根本原因更困難，提高開發、作業和維護的成本。</span><span class="sxs-lookup"><span data-stu-id="10ed2-123">Unexpected changes in configuration of system resources can make identifying root cause of issues more difficult, raising the costs of development, operations, and maintenance.</span></span>
- <span data-ttu-id="10ed2-124">**組織效率不佳**。</span><span class="sxs-lookup"><span data-stu-id="10ed2-124">**Organizational inefficiencies**.</span></span> <span data-ttu-id="10ed2-125">開發、作業及安全性小組之間的屏障，會導致雲端技術的採用和整合雲端治理模型的開發效率出現無數的挑戰。</span><span class="sxs-lookup"><span data-stu-id="10ed2-125">Barriers between development, operations, and security teams can cause numerous challenges to effective adoption of cloud technologies and the development of a unified cloud governance model.</span></span>

## <a name="next-steps"></a><span data-ttu-id="10ed2-126">後續步驟</span><span class="sxs-lookup"><span data-stu-id="10ed2-126">Next steps</span></span>

<span data-ttu-id="10ed2-127">使用[雲端管理範本](./template.md)，記錄目前雲端採用方案可能會引入的商務風險。</span><span class="sxs-lookup"><span data-stu-id="10ed2-127">Using the [Cloud Management Template](./template.md), document business risks that are likely to be introduced by the current cloud adoption plan.</span></span>

<span data-ttu-id="10ed2-128">一旦建立對於實際業務風險的了解，下一步是記錄風險的業務承受度與用來監視承受度的指標和關鍵計量。</span><span class="sxs-lookup"><span data-stu-id="10ed2-128">Once an understanding of realistic business risks is established, the next step is to document the business's tolerance for risk and the indicators and key metrics to monitor that tolerance.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="10ed2-129">計量、指標及風險承受度</span><span class="sxs-lookup"><span data-stu-id="10ed2-129">Metrics, indicators, and risk tolerance</span></span>](./metrics-tolerance.md)

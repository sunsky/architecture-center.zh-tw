---
title: Azure 計算服務的決策樹
description: 選取計算服務的流程圖
author: MikeWasson
ms.date: 10/23/2018
ms.openlocfilehash: 35002b4840b80bcc35b5baf36ec8e414ed8f20be
ms.sourcegitcommit: 2ae794de13c45cf24ad60d4f4dbb193c25944eff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50001893"
---
# <a name="decision-tree-for-azure-compute-services"></a><span data-ttu-id="df6eb-103">Azure 計算服務的決策樹</span><span class="sxs-lookup"><span data-stu-id="df6eb-103">Decision tree for Azure compute services</span></span>

<span data-ttu-id="df6eb-104">Azure 提供了多種方式來裝載您的應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="df6eb-104">Azure offers a number of ways to host your application code.</span></span> <span data-ttu-id="df6eb-105">*計算*一詞是指您的應用程式執行所在運算資源的裝載模型。</span><span class="sxs-lookup"><span data-stu-id="df6eb-105">The term *compute* refers to the hosting model for the computing resources that your application runs on.</span></span> <span data-ttu-id="df6eb-106">下列流程圖可協助您選擇應用程式適用的計算服務。</span><span class="sxs-lookup"><span data-stu-id="df6eb-106">The following flowchart will help you to choose a compute service for your application.</span></span> <span data-ttu-id="df6eb-107">此流程圖會引導您完成一組重要決策準則，以導出建議事項。</span><span class="sxs-lookup"><span data-stu-id="df6eb-107">The flowchart guides you through a set of key decision criteria to reach a recommendation.</span></span> 

<span data-ttu-id="df6eb-108">**將此流程圖視為起始點。**</span><span class="sxs-lookup"><span data-stu-id="df6eb-108">**Treat this flowchart as a starting point.**</span></span> <span data-ttu-id="df6eb-109">每個應用程式各有不同的需求，因此將此建議視為起始點。</span><span class="sxs-lookup"><span data-stu-id="df6eb-109">Every application has unique requirements, so use the recommendation as a starting point.</span></span> <span data-ttu-id="df6eb-110">接著，請執行詳細評估，查看如下的層面：</span><span class="sxs-lookup"><span data-stu-id="df6eb-110">Then perform a more detailed evaluation, looking at aspects such as:</span></span>
 
- <span data-ttu-id="df6eb-111">功能集</span><span class="sxs-lookup"><span data-stu-id="df6eb-111">Feature set</span></span>
- [<span data-ttu-id="df6eb-112">服務限制</span><span class="sxs-lookup"><span data-stu-id="df6eb-112">Service limits</span></span>](/azure/azure-subscription-service-limits)
- [<span data-ttu-id="df6eb-113">成本</span><span class="sxs-lookup"><span data-stu-id="df6eb-113">Cost</span></span>](https://azure.microsoft.com/pricing/)
- [<span data-ttu-id="df6eb-114">SLA</span><span class="sxs-lookup"><span data-stu-id="df6eb-114">SLA</span></span>](https://azure.microsoft.com/support/legal/sla/)
- [<span data-ttu-id="df6eb-115">區域可用性</span><span class="sxs-lookup"><span data-stu-id="df6eb-115">Regional availability</span></span>](https://azure.microsoft.com/global-infrastructure/services/)
- <span data-ttu-id="df6eb-116">開發人員生態系統和小組技能</span><span class="sxs-lookup"><span data-stu-id="df6eb-116">Developer ecosystem and team skills</span></span>
- [<span data-ttu-id="df6eb-117">計算比較資料表</span><span class="sxs-lookup"><span data-stu-id="df6eb-117">Compute comparison tables</span></span>](./compute-comparison.md)

<span data-ttu-id="df6eb-118">如果您的應用程式包含多個工作負載，請個別評估每個工作負載。</span><span class="sxs-lookup"><span data-stu-id="df6eb-118">If your application consists of multiple workloads, evaluate each workload separately.</span></span> <span data-ttu-id="df6eb-119">完整的解決方案可能會納入兩個或更多計算服務。</span><span class="sxs-lookup"><span data-stu-id="df6eb-119">A complete solution may incorporate two or more compute services.</span></span>

<span data-ttu-id="df6eb-120">如需在 Azure 中裝載容器選項的詳細資訊，請參閱＜ https://azure.microsoft.com/overview/containers/＞。</span><span class="sxs-lookup"><span data-stu-id="df6eb-120">For more information about your options for hosting containers in Azure, see https://azure.microsoft.com/overview/containers/.</span></span>

## <a name="flowchart"></a><span data-ttu-id="df6eb-121">流程圖</span><span class="sxs-lookup"><span data-stu-id="df6eb-121">Flowchart</span></span>

![](../images/compute-decision-tree.svg)

## <a name="definitions"></a><span data-ttu-id="df6eb-122">定義</span><span class="sxs-lookup"><span data-stu-id="df6eb-122">Definitions</span></span>

- <span data-ttu-id="df6eb-123">**隨即轉移**是一種將工作負載移轉至雲端，而不需要重新設計應用程式或變更程式碼的策略。</span><span class="sxs-lookup"><span data-stu-id="df6eb-123">**Lift and shift** is a strategy for migrating a workload to the cloud without redesigning the application or making code changes.</span></span> <span data-ttu-id="df6eb-124">也稱為*重新裝載*。</span><span class="sxs-lookup"><span data-stu-id="df6eb-124">Also called *rehosting*.</span></span> <span data-ttu-id="df6eb-125">如需詳細資訊，請參閱 [Azure 移轉中心](https://azure.microsoft.com/migration/)。</span><span class="sxs-lookup"><span data-stu-id="df6eb-125">For more information, see [Azure migration center](https://azure.microsoft.com/migration/).</span></span>

- <span data-ttu-id="df6eb-126">**雲端最佳化**是透過重構應用程式以充分利用雲端原生功能來移轉至雲端的策略。</span><span class="sxs-lookup"><span data-stu-id="df6eb-126">**Cloud optimized** is a strategy for migrating to the cloud by refactoring an application to take advantage of cloud-native features and capabilities.</span></span>

## <a name="next-steps"></a><span data-ttu-id="df6eb-127">後續步驟</span><span class="sxs-lookup"><span data-stu-id="df6eb-127">Next steps</span></span>

<span data-ttu-id="df6eb-128">如要考量其他準則，請參閱[選擇 Azure 計算服務的準則](./compute-comparison.md)。</span><span class="sxs-lookup"><span data-stu-id="df6eb-128">For additional criteria to consider, see [Criteria for choosing an Azure compute service](./compute-comparison.md).</span></span>

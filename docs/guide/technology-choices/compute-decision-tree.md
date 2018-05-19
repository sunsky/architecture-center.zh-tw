---
title: Azure 計算服務的決策樹
description: 選取計算服務的流程圖
author: MikeWasson
ms.date: 04/21/2018
ms.openlocfilehash: 3dcfbd156d4fced863a56bcc8bb74483aa665f9f
ms.sourcegitcommit: 7ced70ebc11aa0df0dc0104092d3cc6ad5c28bd6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2018
---
# <a name="decision-tree-for-azure-compute-services"></a><span data-ttu-id="8432a-103">Azure 計算服務的決策樹</span><span class="sxs-lookup"><span data-stu-id="8432a-103">Decision tree for Azure compute services</span></span>

<span data-ttu-id="8432a-104">Azure 提供了多種方式來裝載您的應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="8432a-104">Azure offers a number of ways to host your application code.</span></span> <span data-ttu-id="8432a-105">*計算*一詞是指您的應用程式執行所在運算資源的裝載模型。</span><span class="sxs-lookup"><span data-stu-id="8432a-105">The term *compute* refers to the hosting model for the computing resources that your application runs on.</span></span> <span data-ttu-id="8432a-106">下列流程圖可協助您選擇應用程式適用的計算服務。</span><span class="sxs-lookup"><span data-stu-id="8432a-106">The following flowchart will help you to choose a compute service for your application.</span></span> <span data-ttu-id="8432a-107">此流程圖會引導您完成一組重要決策準則，以導出建議事項。</span><span class="sxs-lookup"><span data-stu-id="8432a-107">The flowchart guides you through a set of key decision criteria to reach a recommendation.</span></span> 

<span data-ttu-id="8432a-108">**將此流程圖視為起始點。**</span><span class="sxs-lookup"><span data-stu-id="8432a-108">**Treat this flowchart as a starting point.**</span></span> <span data-ttu-id="8432a-109">每個應用程式各有不同的需求，因此將此建議視為起始點。</span><span class="sxs-lookup"><span data-stu-id="8432a-109">Every application has unique requirements, so use the recommendation as a starting point.</span></span> <span data-ttu-id="8432a-110">接著，請執行詳細評估，查看如下的層面：</span><span class="sxs-lookup"><span data-stu-id="8432a-110">Then perform a more detailed evaluation, looking at aspects such as:</span></span>
 
- <span data-ttu-id="8432a-111">功能集</span><span class="sxs-lookup"><span data-stu-id="8432a-111">Feature set</span></span>
- [<span data-ttu-id="8432a-112">服務限制</span><span class="sxs-lookup"><span data-stu-id="8432a-112">Service limits</span></span>](/azure/azure-subscription-service-limits)
- [<span data-ttu-id="8432a-113">成本</span><span class="sxs-lookup"><span data-stu-id="8432a-113">Cost</span></span>](https://azure.microsoft.com/pricing/)
- [<span data-ttu-id="8432a-114">SLA</span><span class="sxs-lookup"><span data-stu-id="8432a-114">SLA</span></span>](https://azure.microsoft.com/support/legal/sla/)
- [<span data-ttu-id="8432a-115">區域可用性</span><span class="sxs-lookup"><span data-stu-id="8432a-115">Regional availability</span></span>](https://azure.microsoft.com/global-infrastructure/services/)
- <span data-ttu-id="8432a-116">開發人員生態系統和小組技能</span><span class="sxs-lookup"><span data-stu-id="8432a-116">Developer ecosystem and team skills</span></span>
- [<span data-ttu-id="8432a-117">計算比較資料表</span><span class="sxs-lookup"><span data-stu-id="8432a-117">Compute comparison tables</span></span>](./compute-comparison.md)

<span data-ttu-id="8432a-118">如果您的應用程式包含多個工作負載，請個別評估每個工作負載。</span><span class="sxs-lookup"><span data-stu-id="8432a-118">If your application consists of multiple workloads, evaluate each workload separately.</span></span> <span data-ttu-id="8432a-119">完整的解決方案可能會納入兩個或更多計算服務。</span><span class="sxs-lookup"><span data-stu-id="8432a-119">A complete solution may incorporate two or more compute services.</span></span>

![](../images/compute-decision-tree.svg)


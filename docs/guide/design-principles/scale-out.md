---
title: 相應放大的設計
titleSuffix: Azure Application Architecture Guide
description: 雲端應用程式的設計應能水平調整規模。
author: MikeWasson
ms.date: 08/30/2018
ms.custom: seojan19
ms.openlocfilehash: 9e8a36146c711fda2b03e00dfeb3554caf1fd5f0
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54113054"
---
# <a name="design-to-scale-out"></a><span data-ttu-id="f3195-103">相應放大的設計</span><span class="sxs-lookup"><span data-stu-id="f3195-103">Design to scale out</span></span>

## <a name="design-your-application-so-that-it-can-scale-horizontally"></a><span data-ttu-id="f3195-104">設計您的應用程式，讓它可水平調整</span><span class="sxs-lookup"><span data-stu-id="f3195-104">Design your application so that it can scale horizontally</span></span>

<span data-ttu-id="f3195-105">雲端的主要優點是彈性調整 &mdash; 能夠使用所需要的容量、在負載增加時相應放大、在不需要額外的容量時相應縮小。</span><span class="sxs-lookup"><span data-stu-id="f3195-105">A primary advantage of the cloud is elastic scaling &mdash; the ability to use as much capacity as you need, scaling out as load increases, and scaling in when the extra capacity is not needed.</span></span> <span data-ttu-id="f3195-106">設計您的應用程式，讓它可水平調整，依需求新增或移除新的執行個體。</span><span class="sxs-lookup"><span data-stu-id="f3195-106">Design your application so that it can scale horizontally, adding or removing new instances as demand requires.</span></span>

## <a name="recommendations"></a><span data-ttu-id="f3195-107">建議</span><span class="sxs-lookup"><span data-stu-id="f3195-107">Recommendations</span></span>

<span data-ttu-id="f3195-108">**避免執行個體綁定**。</span><span class="sxs-lookup"><span data-stu-id="f3195-108">**Avoid instance stickiness**.</span></span> <span data-ttu-id="f3195-109">綁定 (或「工作階段親和性」) 意指來自相同用戶端的要求一定會路由至相同的伺服器。</span><span class="sxs-lookup"><span data-stu-id="f3195-109">Stickiness, or *session affinity*, is when requests from the same client are always routed to the same server.</span></span> <span data-ttu-id="f3195-110">綁定會限制應用程式相應放大的能力。例如，來自大量使用者的流量不會在執行個體之間分散。</span><span class="sxs-lookup"><span data-stu-id="f3195-110">Stickiness limits the application's ability to scale out. For example, traffic from a high-volume user will not be distributed across instances.</span></span> <span data-ttu-id="f3195-111">造成綁定的原因包括工作階段狀態儲存在記憶體中，以及使用電腦專用金鑰進行加密。</span><span class="sxs-lookup"><span data-stu-id="f3195-111">Causes of stickiness include storing session state in memory, and using machine-specific keys for encryption.</span></span> <span data-ttu-id="f3195-112">請確定任何執行個體都可以處理任何要求。</span><span class="sxs-lookup"><span data-stu-id="f3195-112">Make sure that any instance can handle any request.</span></span>

<span data-ttu-id="f3195-113">**找出瓶頸**。</span><span class="sxs-lookup"><span data-stu-id="f3195-113">**Identify bottlenecks**.</span></span> <span data-ttu-id="f3195-114">相應放大不是每個效能問題的修正魔法。</span><span class="sxs-lookup"><span data-stu-id="f3195-114">Scaling out isn't a magic fix for every performance issue.</span></span> <span data-ttu-id="f3195-115">例如，如果後端資料庫是瓶頸，新增更多 Web 伺服器也不會有幫助。</span><span class="sxs-lookup"><span data-stu-id="f3195-115">For example, if your backend database is the bottleneck, it won't help to add more web servers.</span></span> <span data-ttu-id="f3195-116">先找出並解決系統中的瓶頸，再在問題所在之處增加更多執行個體。</span><span class="sxs-lookup"><span data-stu-id="f3195-116">Identify and resolve the bottlenecks in the system first, before throwing more instances at the problem.</span></span> <span data-ttu-id="f3195-117">系統的具狀態組件是最有可能造成瓶頸的原因。</span><span class="sxs-lookup"><span data-stu-id="f3195-117">Stateful parts of the system are the most likely cause of bottlenecks.</span></span>

<span data-ttu-id="f3195-118">**依延展性需求分解工作負載。**</span><span class="sxs-lookup"><span data-stu-id="f3195-118">**Decompose workloads by scalability requirements.**</span></span>  <span data-ttu-id="f3195-119">應用程式通常有多個工作負載，各有不同的調整需求。</span><span class="sxs-lookup"><span data-stu-id="f3195-119">Applications often consist of multiple workloads, with different requirements for scaling.</span></span> <span data-ttu-id="f3195-120">例如，應用程式有面對大眾的公用網站與分開的管理網站。</span><span class="sxs-lookup"><span data-stu-id="f3195-120">For example, an application might have a public-facing site and a separate administration site.</span></span> <span data-ttu-id="f3195-121">公用網站可能遇到流量突然大增，而管理網站的負載則較小、可預測。</span><span class="sxs-lookup"><span data-stu-id="f3195-121">The public site may experience sudden surges in traffic, while the administration site has a smaller, more predictable load.</span></span>

<span data-ttu-id="f3195-122">**卸載需要大量資源的工作。**</span><span class="sxs-lookup"><span data-stu-id="f3195-122">**Offload resource-intensive tasks.**</span></span> <span data-ttu-id="f3195-123">如果可能的話，應該將需要大量 CPU 或 I/O 資源的工作移到[背景工作][background-jobs]，以將負責處理使用者要求的前端的負載降到最低。</span><span class="sxs-lookup"><span data-stu-id="f3195-123">Tasks that require a lot of CPU or I/O resources should be moved to [background jobs][background-jobs] when possible, to minimize the load on the front end that is handling user requests.</span></span>

<span data-ttu-id="f3195-124">**使用內建的自動調整功能**。</span><span class="sxs-lookup"><span data-stu-id="f3195-124">**Use built-in autoscaling features**.</span></span> <span data-ttu-id="f3195-125">許多 Azure 計算服務內建支援自動調整。</span><span class="sxs-lookup"><span data-stu-id="f3195-125">Many Azure compute services have built-in support for autoscaling.</span></span> <span data-ttu-id="f3195-126">如果應用程式有可預測的一般工作負載，則利用排程進行相應放大。</span><span class="sxs-lookup"><span data-stu-id="f3195-126">If the application has a predictable, regular workload, scale out on a schedule.</span></span> <span data-ttu-id="f3195-127">例如，在營業時間執行相應放大。</span><span class="sxs-lookup"><span data-stu-id="f3195-127">For example, scale out during business hours.</span></span> <span data-ttu-id="f3195-128">否則，如果工作負載不可預測，請使用效能計量 (例如 CPU 或要求佇列長度) 觸發自動調整。</span><span class="sxs-lookup"><span data-stu-id="f3195-128">Otherwise, if the workload is not predictable, use performance metrics such as CPU or request queue length to trigger autoscaling.</span></span> <span data-ttu-id="f3195-129">如需自動調整的最佳作法，請參閱[自動調整][autoscaling]。</span><span class="sxs-lookup"><span data-stu-id="f3195-129">For autoscaling best practices, see [Autoscaling][autoscaling].</span></span>

<span data-ttu-id="f3195-130">**考慮對關鍵工作負載主動進行自動調整**。</span><span class="sxs-lookup"><span data-stu-id="f3195-130">**Consider aggressive autoscaling for critical workloads**.</span></span> <span data-ttu-id="f3195-131">面對關鍵工作負載，您要搶先一步，未雨綢繆。</span><span class="sxs-lookup"><span data-stu-id="f3195-131">For critical workloads, you want to keep ahead of demand.</span></span> <span data-ttu-id="f3195-132">最好在負載過重時快速增加新執行個體來處理額外的流量，然後逐漸減少執行個體。</span><span class="sxs-lookup"><span data-stu-id="f3195-132">It's better to add new instances quickly under heavy load to handle the additional traffic, and then gradually scale back.</span></span>

<span data-ttu-id="f3195-133">**相應縮小的設計**。</span><span class="sxs-lookup"><span data-stu-id="f3195-133">**Design for scale in**.</span></span>  <span data-ttu-id="f3195-134">請記住，利用彈性調整，應用程式會有一段逐漸移除執行個體的相應縮小期間。</span><span class="sxs-lookup"><span data-stu-id="f3195-134">Remember that with elastic scale, the application will have periods of scale in, when instances get removed.</span></span> <span data-ttu-id="f3195-135">應用程式必須依正常程序處理要移除的執行個體。</span><span class="sxs-lookup"><span data-stu-id="f3195-135">The application must gracefully handle instances being removed.</span></span> <span data-ttu-id="f3195-136">以下是處理相應縮小的一些做法：</span><span class="sxs-lookup"><span data-stu-id="f3195-136">Here are some ways to handle scalein:</span></span>

- <span data-ttu-id="f3195-137">接聽關機事件 (如果有的話)，然後完全關機。</span><span class="sxs-lookup"><span data-stu-id="f3195-137">Listen for shutdown events (when available) and shut down cleanly.</span></span>
- <span data-ttu-id="f3195-138">服務的用戶端/取用者應該支援暫時性錯誤處理和重試。</span><span class="sxs-lookup"><span data-stu-id="f3195-138">Clients/consumers of a service should support transient fault handling and retry.</span></span>
- <span data-ttu-id="f3195-139">針對長時間執行的工作，考慮使用檢查點或[管道和篩選][pipes-filters-pattern]模式將工作切開。</span><span class="sxs-lookup"><span data-stu-id="f3195-139">For long-running tasks, consider breaking up the work, using checkpoints or the [Pipes and Filters][pipes-filters-pattern] pattern.</span></span>
- <span data-ttu-id="f3195-140">將工作項目放在佇列上，如果在處理過程中執行個體被移除了，另一個執行個體就可以收取該工作。</span><span class="sxs-lookup"><span data-stu-id="f3195-140">Put work items on a queue so that another instance can pick up the work, if an instance is removed in the middle of processing.</span></span>

<!-- links -->

[autoscaling]: ../../best-practices/auto-scaling.md
[background-jobs]: ../../best-practices/background-jobs.md
[pipes-filters-pattern]: ../../patterns/pipes-and-filters.md

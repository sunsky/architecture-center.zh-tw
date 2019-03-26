---
title: 雲端應用程式的效能反模式
titleSuffix: Azure Architecture Center
description: 可能會導致延展性問題的常見做法。
author: dragon119
ms.date: 06/05/2017
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
---

# <a name="performance-antipatterns-for-cloud-applications"></a><span data-ttu-id="9413d-103">雲端應用程式的效能反模式</span><span class="sxs-lookup"><span data-stu-id="9413d-103">Performance antipatterns for cloud applications</span></span>

<span data-ttu-id="9413d-104">「效能反模式」是在應用程式處於壓力下可能會造成延展性問題的常見做法。</span><span class="sxs-lookup"><span data-stu-id="9413d-104">A *performance antipattern* is a common practice that is likely to cause scalability problems when an application is under pressure.</span></span>

<span data-ttu-id="9413d-105">以下是常見案例：應用程式在效能測試期間行為正常。</span><span class="sxs-lookup"><span data-stu-id="9413d-105">Here is a common scenario: An application behaves well during performance testing.</span></span> <span data-ttu-id="9413d-106">它發行至生產，並開始處理實際工作負載。</span><span class="sxs-lookup"><span data-stu-id="9413d-106">It's released to production, and begins to handle real workloads.</span></span> <span data-ttu-id="9413d-107">屆時，它開始執行效能不佳 &mdash; 拒絕使用者要求、停止，或擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9413d-107">At that point, it starts to perform poorly &mdash; rejecting user requests, stalling, or throwing exceptions.</span></span> <span data-ttu-id="9413d-108">開發小組接著面臨兩個問題：</span><span class="sxs-lookup"><span data-stu-id="9413d-108">The development team is then faced with two questions:</span></span>

- <span data-ttu-id="9413d-109">這個行為為何未在測試期間顯現？</span><span class="sxs-lookup"><span data-stu-id="9413d-109">Why didn't this behavior show up during testing?</span></span>
- <span data-ttu-id="9413d-110">我們要如何修正它？</span><span class="sxs-lookup"><span data-stu-id="9413d-110">How do we fix it?</span></span>

<span data-ttu-id="9413d-111">第一個問題的答案很直截了當。</span><span class="sxs-lookup"><span data-stu-id="9413d-111">The answer to the first question is straightforward.</span></span> <span data-ttu-id="9413d-112">在測試環境中模擬真實使用者、他們的行為模式和他們可能執行的工作量很困難。</span><span class="sxs-lookup"><span data-stu-id="9413d-112">It's very difficult in a test environment to simulate real users, their behavior patterns, and the volumes of work they might perform.</span></span> <span data-ttu-id="9413d-113">了解系統在負載之下如何運作的唯一完整確定方式是在生產中觀察。</span><span class="sxs-lookup"><span data-stu-id="9413d-113">The only completely sure way to understand how a system behaves under load is to observe it in production.</span></span> <span data-ttu-id="9413d-114">要說明清楚的是，我們並非建議您應該略過效能測試。</span><span class="sxs-lookup"><span data-stu-id="9413d-114">To be clear, we aren't suggesting that you should skip performance testing.</span></span> <span data-ttu-id="9413d-115">效能測試對於取得基準效能計量相當關鍵。</span><span class="sxs-lookup"><span data-stu-id="9413d-115">Performance tests are crucial for getting baseline performance metrics.</span></span> <span data-ttu-id="9413d-116">但是，您必須準備好在即時系統中觀察效能問題，並且在發生時進行修正。</span><span class="sxs-lookup"><span data-stu-id="9413d-116">But you must be prepared to observe and correct performance issues when they arise in the live system.</span></span>

<span data-ttu-id="9413d-117">第二個問題的答案，如何修正此問題，則比較不直接。</span><span class="sxs-lookup"><span data-stu-id="9413d-117">The answer to the second question, how to fix the problem, is less straightforward.</span></span> <span data-ttu-id="9413d-118">造成問題的因素不計其數，有時問題只會在特定情況下展現出來。</span><span class="sxs-lookup"><span data-stu-id="9413d-118">Any number of factors might contribute, and sometimes the problem only manifests under certain circumstances.</span></span> <span data-ttu-id="9413d-119">檢測和記錄索引鍵，以尋找根本原因，但是您也必須知道要尋找什麼項目。</span><span class="sxs-lookup"><span data-stu-id="9413d-119">Instrumentation and logging are key to finding the root cause, but you also have to know what to look for.</span></span>

<span data-ttu-id="9413d-120">根據我們與 Microsoft Azure 客戶的承諾，我們已識別客戶在生產中會看到的部分常見效能問題。</span><span class="sxs-lookup"><span data-stu-id="9413d-120">Based on our engagements with Microsoft Azure customers, we've identified some of the most common performance issues that customers see in production.</span></span> <span data-ttu-id="9413d-121">針對每個反模式，我們說明反模式的典型發生原因、反模式的徵兆和解決問題的技術。</span><span class="sxs-lookup"><span data-stu-id="9413d-121">For each antipattern, we describe why the antipattern typically occurs, symptoms of the antipattern, and techniques for resolving the problem.</span></span> <span data-ttu-id="9413d-122">我們也會提供範例程式碼，說明反模式和建議的解決方案。</span><span class="sxs-lookup"><span data-stu-id="9413d-122">We also provide sample code that illustrates both the antipattern and a suggested solution.</span></span>

<span data-ttu-id="9413d-123">當您閱讀描述時，某些反模式可能顯而易見，但是它們比您以為的還要經常發生。</span><span class="sxs-lookup"><span data-stu-id="9413d-123">Some of these antipatterns may seem obvious when you read the descriptions, but they occur more often than you might think.</span></span> <span data-ttu-id="9413d-124">有時候應用程式會繼承在內部部署運作，但是未在雲端中相應縮小的設計。</span><span class="sxs-lookup"><span data-stu-id="9413d-124">Sometimes an application inherits a design that worked on-premises, but doesn't scale in the cloud.</span></span> <span data-ttu-id="9413d-125">或者應用程式可能會以非常乾淨的設計開始，但是隨著新增功能，這些反模式一一浮現。</span><span class="sxs-lookup"><span data-stu-id="9413d-125">Or an application might start with a very clean design, but as new features are added, one or more of these antipatterns creeps in.</span></span> <span data-ttu-id="9413d-126">不論如何，本指南將協助您識別並修正這些反模式。</span><span class="sxs-lookup"><span data-stu-id="9413d-126">Regardless, this guide will help you to identify and fix these antipatterns.</span></span>

<span data-ttu-id="9413d-127">以下是我們所識別的反模式清單：</span><span class="sxs-lookup"><span data-stu-id="9413d-127">Here is the list of the antipatterns that we've identified:</span></span>

| <span data-ttu-id="9413d-128">反模式</span><span class="sxs-lookup"><span data-stu-id="9413d-128">Antipattern</span></span> | <span data-ttu-id="9413d-129">說明</span><span class="sxs-lookup"><span data-stu-id="9413d-129">Description</span></span> |
|-------------|-------------|
| <span data-ttu-id="9413d-130">[忙碌資料庫][BusyDatabase]</span><span class="sxs-lookup"><span data-stu-id="9413d-130">[Busy Database][BusyDatabase]</span></span> | <span data-ttu-id="9413d-131">卸載太多處理到資料存放區。</span><span class="sxs-lookup"><span data-stu-id="9413d-131">Offloading too much processing to a data store.</span></span> |
| <span data-ttu-id="9413d-132">[忙碌前端][BusyFrontEnd]</span><span class="sxs-lookup"><span data-stu-id="9413d-132">[Busy Front End][BusyFrontEnd]</span></span> | <span data-ttu-id="9413d-133">將耗用大量資源的工作移至背景執行緒。</span><span class="sxs-lookup"><span data-stu-id="9413d-133">Moving resource-intensive tasks onto background threads.</span></span> |
| <span data-ttu-id="9413d-134">[多對話 I/O][ChattyIO]</span><span class="sxs-lookup"><span data-stu-id="9413d-134">[Chatty I/O][ChattyIO]</span></span> | <span data-ttu-id="9413d-135">持續傳送許多小型網路要求。</span><span class="sxs-lookup"><span data-stu-id="9413d-135">Continually sending many small network requests.</span></span> |
| <span data-ttu-id="9413d-136">[沒有直接關聯的擷取][ExtraneousFetching]</span><span class="sxs-lookup"><span data-stu-id="9413d-136">[Extraneous Fetching][ExtraneousFetching]</span></span> | <span data-ttu-id="9413d-137">擷取超過所需的資料，造成不必要的 I/O。</span><span class="sxs-lookup"><span data-stu-id="9413d-137">Retrieving more data than is needed, resulting in unnecessary I/O.</span></span> |
| <span data-ttu-id="9413d-138">[不適當的具現化][ImproperInstantiation]</span><span class="sxs-lookup"><span data-stu-id="9413d-138">[Improper Instantiation][ImproperInstantiation]</span></span> | <span data-ttu-id="9413d-139">重複建立和終結設計用來共用及重複使用的物件。</span><span class="sxs-lookup"><span data-stu-id="9413d-139">Repeatedly creating and destroying objects that are designed to be shared and reused.</span></span> |
| <span data-ttu-id="9413d-140">[整合的持續性][MonolithicPersistence]</span><span class="sxs-lookup"><span data-stu-id="9413d-140">[Monolithic Persistence][MonolithicPersistence]</span></span> | <span data-ttu-id="9413d-141">針對使用模式差異極大的資料使用相同的資料存放區。</span><span class="sxs-lookup"><span data-stu-id="9413d-141">Using the same data store for data with very different usage patterns.</span></span> |
| <span data-ttu-id="9413d-142">[無快取][NoCaching]</span><span class="sxs-lookup"><span data-stu-id="9413d-142">[No Caching][NoCaching]</span></span> | <span data-ttu-id="9413d-143">無法快取資料。</span><span class="sxs-lookup"><span data-stu-id="9413d-143">Failing to cache data.</span></span> |
| <span data-ttu-id="9413d-144">[同步 I/O][SynchronousIO]</span><span class="sxs-lookup"><span data-stu-id="9413d-144">[Synchronous I/O][SynchronousIO]</span></span> | <span data-ttu-id="9413d-145">I/O 完成時，封鎖呼叫執行緒。</span><span class="sxs-lookup"><span data-stu-id="9413d-145">Blocking the calling thread while I/O completes.</span></span> |

[BusyDatabase]: ./busy-database/index.md
[BusyFrontEnd]: ./busy-front-end/index.md
[ChattyIO]: ./chatty-io/index.md
[ExtraneousFetching]: ./extraneous-fetching/index.md
[ImproperInstantiation]: ./improper-instantiation/index.md
[MonolithicPersistence]: ./monolithic-persistence/index.md
[NoCaching]: ./no-caching/index.md
[SynchronousIO]: ./synchronous-io/index.md

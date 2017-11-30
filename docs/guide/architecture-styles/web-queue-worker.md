---
title: "Web-佇列-背景工作角色架構樣式"
description: "說明 Azure 上 Web-佇列-背景工作角色架構的優點、挑戰和最佳作法"
author: MikeWasson
ms.openlocfilehash: 545472e71ffcd43717ad24af0dc9218a221ca910
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="web-queue-worker-architecture-style"></a><span data-ttu-id="6984c-103">Web-佇列-背景工作角色架構樣式</span><span class="sxs-lookup"><span data-stu-id="6984c-103">Web-Queue-Worker architecture style</span></span>

<span data-ttu-id="6984c-104">這個架構的核心元件是**Web 前端**和**背景工作角色**，前者負責用戶端要求，後者則執行需要大量資源的工作、長時間執行工作流程或批次作業。</span><span class="sxs-lookup"><span data-stu-id="6984c-104">The core components of this architecture are a **web front end** that serves client requests, and a **worker** that performs resource-intensive tasks, long-running workflows, or batch jobs.</span></span>  <span data-ttu-id="6984c-105">Web 前端透過**訊息佇列**與背景工作角色通訊。</span><span class="sxs-lookup"><span data-stu-id="6984c-105">The web front end communicates with the worker through a **message queue**.</span></span>  

![](./images/web-queue-worker-logical.svg)

<span data-ttu-id="6984c-106">其他經常併入這個架構的元件包括：</span><span class="sxs-lookup"><span data-stu-id="6984c-106">Other components that are commonly incorporated into this architecture include:</span></span>

- <span data-ttu-id="6984c-107">一或多個資料庫。</span><span class="sxs-lookup"><span data-stu-id="6984c-107">One or more databases.</span></span> 
- <span data-ttu-id="6984c-108">快取，儲存資料庫中之值以利快速讀取。</span><span class="sxs-lookup"><span data-stu-id="6984c-108">A cache to store values from the database for quick reads.</span></span>
- <span data-ttu-id="6984c-109">CDN，處理靜態內容</span><span class="sxs-lookup"><span data-stu-id="6984c-109">CDN to serve static content</span></span>
- <span data-ttu-id="6984c-110">遠端服務，例如電子郵件或 SMS 服務。</span><span class="sxs-lookup"><span data-stu-id="6984c-110">Remote services, such as email or SMS service.</span></span> <span data-ttu-id="6984c-111">通常這些是由第三方提供。</span><span class="sxs-lookup"><span data-stu-id="6984c-111">Often these are provided by third parties.</span></span>
- <span data-ttu-id="6984c-112">識別提供者，用於驗證。</span><span class="sxs-lookup"><span data-stu-id="6984c-112">Identity provider for authentication.</span></span>

<span data-ttu-id="6984c-113">Web 和背景工作角色都是無狀態。</span><span class="sxs-lookup"><span data-stu-id="6984c-113">The web and worker are both stateless.</span></span> <span data-ttu-id="6984c-114">工作階段狀態可以儲存在分散式快取中。</span><span class="sxs-lookup"><span data-stu-id="6984c-114">Session state can be stored in a distributed cache.</span></span> <span data-ttu-id="6984c-115">任何長時間執行的工作都是由背景工作角色以非同步方式完成。</span><span class="sxs-lookup"><span data-stu-id="6984c-115">Any long-running work is done asynchronously by the worker.</span></span> <span data-ttu-id="6984c-116">背景工作角色可由佇列上的訊息觸發，或在批次處理中依照排程執行。</span><span class="sxs-lookup"><span data-stu-id="6984c-116">The worker can be triggered by messages on the queue, or run on a schedule for batch processing.</span></span> <span data-ttu-id="6984c-117">背景工作角色是選擇性元件。</span><span class="sxs-lookup"><span data-stu-id="6984c-117">The worker is an optional component.</span></span> <span data-ttu-id="6984c-118">如果沒有長時間執行的作業，就可以省略背景工作角色。</span><span class="sxs-lookup"><span data-stu-id="6984c-118">If there are no long-running operations, the worker can be omitted.</span></span>  

<span data-ttu-id="6984c-119">前端可能包含 Web API。</span><span class="sxs-lookup"><span data-stu-id="6984c-119">The front end might consist of a web API.</span></span> <span data-ttu-id="6984c-120">在用戶端，可以透過會發出 AJAX 呼叫的單一頁面應用程式取用 Web API，或由原生用戶端應用程式取用 Web API。</span><span class="sxs-lookup"><span data-stu-id="6984c-120">On the client side, the web API can be consumed by a single-page application that makes AJAX calls, or by a native client application.</span></span>

## <a name="when-to-use-this-architecture"></a><span data-ttu-id="6984c-121">使用此架構的時機</span><span class="sxs-lookup"><span data-stu-id="6984c-121">When to use this architecture</span></span>

<span data-ttu-id="6984c-122">「Web-佇列-背景工作角色」架構的實作，通常是使用受管理的計算服務：Azure App Service 或 Azure 雲端服務實作。</span><span class="sxs-lookup"><span data-stu-id="6984c-122">The Web-Queue-Worker architecture is typically implemented using managed compute services, either Azure App Service or Azure Cloud Services.</span></span> 

<span data-ttu-id="6984c-123">請考慮將此架構樣式用於：</span><span class="sxs-lookup"><span data-stu-id="6984c-123">Consider this architecture style for:</span></span>

- <span data-ttu-id="6984c-124">具備相對簡單網域的應用程式。</span><span class="sxs-lookup"><span data-stu-id="6984c-124">Applications with a relatively simple domain.</span></span>
- <span data-ttu-id="6984c-125">有一些長時間執行工作流程或批次作業的應用程式。</span><span class="sxs-lookup"><span data-stu-id="6984c-125">Applications with some long-running workflows or batch operations.</span></span>
- <span data-ttu-id="6984c-126">當您想要使用受管理的服務，而不是基礎結構即服務 (IaaS)。</span><span class="sxs-lookup"><span data-stu-id="6984c-126">When you want to use managed services, rather than infrastructure as a service (IaaS).</span></span>

## <a name="benefits"></a><span data-ttu-id="6984c-127">優點</span><span class="sxs-lookup"><span data-stu-id="6984c-127">Benefits</span></span>

- <span data-ttu-id="6984c-128">相對簡單的架構相當容易理解。</span><span class="sxs-lookup"><span data-stu-id="6984c-128">Relatively simple architecture that is easy to understand.</span></span>
- <span data-ttu-id="6984c-129">容易部署及管理。</span><span class="sxs-lookup"><span data-stu-id="6984c-129">Easy to deploy and manage.</span></span>
- <span data-ttu-id="6984c-130">清楚區分重要事項。</span><span class="sxs-lookup"><span data-stu-id="6984c-130">Clear separation of concerns.</span></span>
- <span data-ttu-id="6984c-131">使用非同步傳訊就可以分開前端和背景工作角色。</span><span class="sxs-lookup"><span data-stu-id="6984c-131">The front end is decoupled from the worker using asynchronous messaging.</span></span>
- <span data-ttu-id="6984c-132">可以獨立調整前端和背景工作角色。</span><span class="sxs-lookup"><span data-stu-id="6984c-132">The front end and the worker can be scaled independently.</span></span>

## <a name="challenges"></a><span data-ttu-id="6984c-133">挑戰</span><span class="sxs-lookup"><span data-stu-id="6984c-133">Challenges</span></span>

- <span data-ttu-id="6984c-134">若設計不慎，前端與背景工作角色可能會成為難以維護及更新的大型整合元件。</span><span class="sxs-lookup"><span data-stu-id="6984c-134">Without careful design, the front end and the worker can become large, monolithic components that are difficult to maintain and update.</span></span>
- <span data-ttu-id="6984c-135">如果前端和背景工作共用資料結構描述或程式碼模組，則可能會隱藏的相依性。</span><span class="sxs-lookup"><span data-stu-id="6984c-135">There may be hidden dependencies, if the front end and worker share data schemas or code modules.</span></span> 

## <a name="best-practices"></a><span data-ttu-id="6984c-136">最佳作法</span><span class="sxs-lookup"><span data-stu-id="6984c-136">Best practices</span></span>

- <span data-ttu-id="6984c-137">向用戶端公開設計良好的 API。</span><span class="sxs-lookup"><span data-stu-id="6984c-137">Expose a well-designed API to the client.</span></span> <span data-ttu-id="6984c-138">請參閱 [API 設計最佳作法][api-design]。</span><span class="sxs-lookup"><span data-stu-id="6984c-138">See [API design best practices][api-design].</span></span>
- <span data-ttu-id="6984c-139">使用自動調整來因應負載的改變。</span><span class="sxs-lookup"><span data-stu-id="6984c-139">Autoscale to handle changes in load.</span></span> <span data-ttu-id="6984c-140">請參閱[自動調整最佳作法][autoscaling]。</span><span class="sxs-lookup"><span data-stu-id="6984c-140">See [Autoscaling best practices][autoscaling].</span></span>
- <span data-ttu-id="6984c-141">快取半靜態資料。</span><span class="sxs-lookup"><span data-stu-id="6984c-141">Cache semi-static data.</span></span> <span data-ttu-id="6984c-142">請參閱[快取最佳作法][caching]。</span><span class="sxs-lookup"><span data-stu-id="6984c-142">See [Caching best practices][caching].</span></span>
- <span data-ttu-id="6984c-143">使用 CDN 來裝載靜態內容。</span><span class="sxs-lookup"><span data-stu-id="6984c-143">Use a CDN to host static content.</span></span> <span data-ttu-id="6984c-144">請參閱 [CDN 最佳作法][cdn]。</span><span class="sxs-lookup"><span data-stu-id="6984c-144">See [CDN best practices][cdn].</span></span>
- <span data-ttu-id="6984c-145">適當的時候使用 polyglot persistence。</span><span class="sxs-lookup"><span data-stu-id="6984c-145">Use polyglot persistence when appropriate.</span></span> <span data-ttu-id="6984c-146">請參閱[使用作業的最佳資料存放區][polyglot]。</span><span class="sxs-lookup"><span data-stu-id="6984c-146">See [Use the best data store for the job][polyglot].</span></span>
- <span data-ttu-id="6984c-147">分割資料以改善延展性、減少爭用，以及最佳化效能。</span><span class="sxs-lookup"><span data-stu-id="6984c-147">Partition data to improve scalability, reduce contention, and optimize performance.</span></span> <span data-ttu-id="6984c-148">請參閱[資料分割的最佳作法][data-partition]。</span><span class="sxs-lookup"><span data-stu-id="6984c-148">See [Data partitioning best practices][data-partition].</span></span>


## <a name="web-queue-worker-on-azure-app-service"></a><span data-ttu-id="6984c-149">Azure App Service 上的 Web-佇列-背景工作角色</span><span class="sxs-lookup"><span data-stu-id="6984c-149">Web-Queue-Worker on Azure App Service</span></span>

<span data-ttu-id="6984c-150">本節描述建議的 Web-佇列-背景工作角色架構，它使用 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="6984c-150">This section describes a recommended Web-Queue-Worker architecture that uses Azure App Service.</span></span> 

![](./images/web-queue-worker-physical.png)

<span data-ttu-id="6984c-151">前端實作成 Azure App Service Web 應用程式，而背景工作角色實作為 WebJob。</span><span class="sxs-lookup"><span data-stu-id="6984c-151">The front end is implemented as an Azure App Service web app, and the worker is implemented as a WebJob.</span></span> <span data-ttu-id="6984c-152">Web 應用程式和 WebJob 都與提供 VM 執行個體的 App Service 方案相關聯。</span><span class="sxs-lookup"><span data-stu-id="6984c-152">The web app and the WebJob are both associated with an App Service plan that provides the VM instances.</span></span> 

<span data-ttu-id="6984c-153">您可以使用 Azure 服務匯流排或 Azure 儲存體佇列來提供訊息佇列。</span><span class="sxs-lookup"><span data-stu-id="6984c-153">You can use either Azure Service Bus or Azure Storage queues for the message queue.</span></span> <span data-ttu-id="6984c-154">(圖中為 Azure 儲存體佇列。)</span><span class="sxs-lookup"><span data-stu-id="6984c-154">(The diagram shows an Azure Storage queue.)</span></span>

<span data-ttu-id="6984c-155">Azure Redis 快取會儲存工作階段狀態和其他需要低延遲存取的資料。</span><span class="sxs-lookup"><span data-stu-id="6984c-155">Azure Redis Cache stores session state and other data that needs low latency access.</span></span>

<span data-ttu-id="6984c-156">Azure CDN 用於快取靜態內容，例如影像、CSS 或 HTML。</span><span class="sxs-lookup"><span data-stu-id="6984c-156">Azure CDN is used to cache static content such as images, CSS, or HTML.</span></span>

<span data-ttu-id="6984c-157">在儲存體方面，選擇最符合應用程式需求的儲存體技術。</span><span class="sxs-lookup"><span data-stu-id="6984c-157">For storage, choose the storage technologies that best fit the needs of the application.</span></span> <span data-ttu-id="6984c-158">您可以使用多個儲存體技術 (polyglot persistence)。</span><span class="sxs-lookup"><span data-stu-id="6984c-158">You might use multiple storage technologies (polyglot persistence).</span></span> <span data-ttu-id="6984c-159">為了說明這個概念，圖中顯示 Azure SQL Database 和 Azure Cosmos DB。</span><span class="sxs-lookup"><span data-stu-id="6984c-159">To illustrate this idea, the diagram shows Azure SQL Database and Azure Cosmos DB.</span></span>  

<span data-ttu-id="6984c-160">如需詳細資訊，請參閱 [App Service Web 應用程式參考架構][scalable-web-app]。</span><span class="sxs-lookup"><span data-stu-id="6984c-160">For more details, see [App Service web application reference architecture][scalable-web-app].</span></span>

### <a name="additional-considerations"></a><span data-ttu-id="6984c-161">其他考量</span><span class="sxs-lookup"><span data-stu-id="6984c-161">Additional considerations</span></span>

- <span data-ttu-id="6984c-162">並非每個交易皆必須透過佇列和背景工作角色移至儲存體。</span><span class="sxs-lookup"><span data-stu-id="6984c-162">Not every transaction has to go through the queue and worker to storage.</span></span> <span data-ttu-id="6984c-163">Web 前端可以直接執行簡單的讀取/寫入作業。</span><span class="sxs-lookup"><span data-stu-id="6984c-163">The web front end can perform simple read/write operations directly.</span></span> <span data-ttu-id="6984c-164">背景工作角色專為需要大量資源的工作或長時間執行的工作流程而設計。</span><span class="sxs-lookup"><span data-stu-id="6984c-164">Workers are designed for resource-intensive tasks or long-running workflows.</span></span> <span data-ttu-id="6984c-165">在某些情況下，您可能根本不需要背景工作角色。</span><span class="sxs-lookup"><span data-stu-id="6984c-165">In some cases, you might not need a worker at all.</span></span>

- <span data-ttu-id="6984c-166">使用 App Service 內建的自動調整功能，相應放大 VM 的執行個體數目。</span><span class="sxs-lookup"><span data-stu-id="6984c-166">Use the built-in autoscale feature of App Service to scale out the number of VM instances.</span></span> <span data-ttu-id="6984c-167">如果應用程式的負載有固定可預測的模式，使用依排程執行的自動調整。</span><span class="sxs-lookup"><span data-stu-id="6984c-167">If the load on the application follows predictable patterns, use schedule-based autoscale.</span></span> <span data-ttu-id="6984c-168">如果負載無法預期，使用依計量執行的自動調整規則。</span><span class="sxs-lookup"><span data-stu-id="6984c-168">If the load is unpredictable, use metrics-based autoscaling rules.</span></span>      

- <span data-ttu-id="6984c-169">請考慮將 Web 應用程式和 WebJob 放入不同的 App Service 方案。</span><span class="sxs-lookup"><span data-stu-id="6984c-169">Consider putting the web app and the WebJob into separate App Service plans.</span></span> <span data-ttu-id="6984c-170">這樣一來，它們裝載在不同的 VM 執行個體上，可以獨立調整。</span><span class="sxs-lookup"><span data-stu-id="6984c-170">That way, they are hosted on separate VM instances and can be scaled independently.</span></span> 

- <span data-ttu-id="6984c-171">生產和測試請使用不同的 App Service 方案。</span><span class="sxs-lookup"><span data-stu-id="6984c-171">Use separate App Service plans for production and testing.</span></span> <span data-ttu-id="6984c-172">否則，如果您在生產和測試使用相同方案，表示您的測試會在生產環境的 VM 上執行。</span><span class="sxs-lookup"><span data-stu-id="6984c-172">Otherwise, if you use the same plan for production and testing, it means your tests are running on your production VMs.</span></span>

- <span data-ttu-id="6984c-173">使用部署位置管理部署。</span><span class="sxs-lookup"><span data-stu-id="6984c-173">Use deployment slots to manage deployments.</span></span> <span data-ttu-id="6984c-174">這可讓您將更新後的版本部署到預備位置，然後換到新的版本。</span><span class="sxs-lookup"><span data-stu-id="6984c-174">This lets you to deploy an updated version to a staging slot, then swap over to the new version.</span></span> <span data-ttu-id="6984c-175">如果更新發生問題，它也讓您可以換回先前的版本。</span><span class="sxs-lookup"><span data-stu-id="6984c-175">It also lets you swap back to the previous version, if there was a problem with the update.</span></span>

<!-- links -->

[api-design]: ../../best-practices/api-design.md
[autoscaling]: ../../best-practices/auto-scaling.md
[caching]: ../../best-practices/caching.md
[cdn]: ../../best-practices/cdn.md
[data-partition]: ../../best-practices/data-partitioning.md
[polyglot]: ../design-principles/use-the-best-data-store.md
[scalable-web-app]: ../../reference-architectures/app-service-web-app/scalable-web-app.md
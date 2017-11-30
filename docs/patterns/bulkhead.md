---
title: "隔艙模式"
description: "將應用程式的元素隔離到集區中，以便在其中一個元素失敗時，其他元素可以繼續運行"
author: dragon119
ms.date: 06/23/2017
ms.openlocfilehash: a2c499d77fafc4bee6b74ee0e0d84e6c23b47851
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="bulkhead-pattern"></a><span data-ttu-id="519e9-103">隔艙模式</span><span class="sxs-lookup"><span data-stu-id="519e9-103">Bulkhead pattern</span></span>

<span data-ttu-id="519e9-104">將應用程式的元素隔離到集區中，以便在其中一個元素失敗時，其他元素可以繼續運行。</span><span class="sxs-lookup"><span data-stu-id="519e9-104">Isolate elements of an application into pools so that if one fails, the others will continue to function.</span></span>

<span data-ttu-id="519e9-105">這種模式被命名為「隔艙」，因為它類似於船體的剖面分區。</span><span class="sxs-lookup"><span data-stu-id="519e9-105">This pattern is named *Bulkhead* because it resembles the sectioned partitions of a ship's hull.</span></span> <span data-ttu-id="519e9-106">如果船體受損，只有受損部分充滿水，這樣可以防止船舶沉沒。</span><span class="sxs-lookup"><span data-stu-id="519e9-106">If the hull of a ship is compromised, only the damaged section fills with water, which prevents the ship from sinking.</span></span> 

## <a name="context-and-problem"></a><span data-ttu-id="519e9-107">內容和問題</span><span class="sxs-lookup"><span data-stu-id="519e9-107">Context and problem</span></span>

<span data-ttu-id="519e9-108">雲端式應用程式可能包含多個服務，每個服務具有一或多個取用者。</span><span class="sxs-lookup"><span data-stu-id="519e9-108">A cloud-based application may include multiple services, with each service having one or more consumers.</span></span> <span data-ttu-id="519e9-109">服務負載超量或失敗會影響服務的所有取用者。</span><span class="sxs-lookup"><span data-stu-id="519e9-109">Excessive load or failure in a service will impact all consumers of the service.</span></span>

<span data-ttu-id="519e9-110">此外，取用者可以使用每個要求的資源，同時向多個服務傳送要求。</span><span class="sxs-lookup"><span data-stu-id="519e9-110">Moreover, a consumer may send requests to multiple services simultaneously, using resources for each request.</span></span> <span data-ttu-id="519e9-111">當取用者向設定不正確或沒有回應的服務傳送要求時，用戶端要求使用的資源可能無法及時釋放。</span><span class="sxs-lookup"><span data-stu-id="519e9-111">When the consumer sends a request to a service that is misconfigured or not responding, the resources used by the client's request may not be freed in a timely manner.</span></span> <span data-ttu-id="519e9-112">隨著要求繼續傳送給服務，那些資源可能會耗盡。</span><span class="sxs-lookup"><span data-stu-id="519e9-112">As requests to the service continue, those resources may be exhausted.</span></span> <span data-ttu-id="519e9-113">例如，用戶端的連線集區可能會耗盡。</span><span class="sxs-lookup"><span data-stu-id="519e9-113">For example, the client's connection pool may be exhausted.</span></span> <span data-ttu-id="519e9-114">此時，取用者對其他服務的要求會受到影響。</span><span class="sxs-lookup"><span data-stu-id="519e9-114">At that point, requests by the consumer to other services are impacted.</span></span> <span data-ttu-id="519e9-115">最終取用者不能再向其他服務傳送要求，不僅僅是停止回應的原始服務。</span><span class="sxs-lookup"><span data-stu-id="519e9-115">Eventually the consumer can no longer send requests to other services, not just the original unresponsive service.</span></span>

<span data-ttu-id="519e9-116">相同的資源耗盡問題會影響具有多個取用者的服務。</span><span class="sxs-lookup"><span data-stu-id="519e9-116">The same issue of resource exhaustion affects services with multiple consumers.</span></span> <span data-ttu-id="519e9-117">來自一個用戶端的大量請求可能會耗盡服務中的可用資源。</span><span class="sxs-lookup"><span data-stu-id="519e9-117">A large number of requests originating from one client may exhaust available resources in the service.</span></span> <span data-ttu-id="519e9-118">其他取用者無法再取用該服務，導致連鎖性失效效應。</span><span class="sxs-lookup"><span data-stu-id="519e9-118">Other consumers are no longer able to consume the service, causing a cascading failure effect.</span></span>

## <a name="solution"></a><span data-ttu-id="519e9-119">解決方式</span><span class="sxs-lookup"><span data-stu-id="519e9-119">Solution</span></span>

<span data-ttu-id="519e9-120">根據取用者負載和可用性需求，將服務執行個體分割成不同的群組。</span><span class="sxs-lookup"><span data-stu-id="519e9-120">Partition service instances into different groups, based on consumer load and availability requirements.</span></span> <span data-ttu-id="519e9-121">這種設計可以幫助隔離失敗，並允許您為某些取用者維持服務功能，即使在失敗期間也是如此。</span><span class="sxs-lookup"><span data-stu-id="519e9-121">This design helps to isolate failures, and allows you to sustain service functionality for some consumers, even during a failure.</span></span>

<span data-ttu-id="519e9-122">取用者也可以分割資源，以確保用來呼叫一個服務的資源，不會影響用來呼叫另一個服務的資源。</span><span class="sxs-lookup"><span data-stu-id="519e9-122">A consumer can also partition resources, to ensure that resources used to call one service don't affect the resources used to call another service.</span></span> <span data-ttu-id="519e9-123">例如，呼叫多個服務的取用者可能會被指派每個服務的連線集區。</span><span class="sxs-lookup"><span data-stu-id="519e9-123">For example, a consumer that calls multiple services may be assigned a connection pool for each service.</span></span> <span data-ttu-id="519e9-124">如果服務開始失敗，它只會影響針對該服務指派的連線集區，讓取用者繼續使用其他服務。</span><span class="sxs-lookup"><span data-stu-id="519e9-124">If a service begins to fail, it only affects the connection pool assigned for that service, allowing the consumer to continue using the other services.</span></span>

<span data-ttu-id="519e9-125">這種模式的好處包括：</span><span class="sxs-lookup"><span data-stu-id="519e9-125">The benefits of this pattern include:</span></span>

- <span data-ttu-id="519e9-126">將取用者和服務與連鎖性失效隔離。</span><span class="sxs-lookup"><span data-stu-id="519e9-126">Isolates consumers and services from cascading failures.</span></span> <span data-ttu-id="519e9-127">影響取用者或服務的問題可以在其自身的隔艙內隔離，避免整個方案失敗。</span><span class="sxs-lookup"><span data-stu-id="519e9-127">An issue affecting a consumer or service can be isolated within its own bulkhead, preventing the entire solution from failing.</span></span>
- <span data-ttu-id="519e9-128">允許您在服務失敗時保留某些功能。</span><span class="sxs-lookup"><span data-stu-id="519e9-128">Allows you to preserve some functionality in the event of a service failure.</span></span> <span data-ttu-id="519e9-129">其他服務和應用程式的功能將繼續運作。</span><span class="sxs-lookup"><span data-stu-id="519e9-129">Other services and features of the application will continue to work.</span></span>
- <span data-ttu-id="519e9-130">可讓您部署為取用應用程式提供不同服務品質的服務。</span><span class="sxs-lookup"><span data-stu-id="519e9-130">Allows you to deploy services that offer a different quality of service for consuming applications.</span></span> <span data-ttu-id="519e9-131">高優先順序的取用者集區可以設定為使用高優先順序服務。</span><span class="sxs-lookup"><span data-stu-id="519e9-131">A high-priority consumer pool can be configured to use high-priority services.</span></span> 

<span data-ttu-id="519e9-132">下圖顯示圍繞呼叫個別服務之連線集區建構的隔艙。</span><span class="sxs-lookup"><span data-stu-id="519e9-132">The following diagram shows bulkheads structured around connection pools that call individual services.</span></span> <span data-ttu-id="519e9-133">如果服務 A 失敗或造成其他問題，則連線集區將被隔離，因此只有使用指派給服務 A 之執行緒集區的工作負載會受到影響。</span><span class="sxs-lookup"><span data-stu-id="519e9-133">If Service A fails or causes some other issue, the connection pool is isolated, so only workloads using the thread pool assigned to Service A are affected.</span></span> <span data-ttu-id="519e9-134">使用服務 B 和 C 的工作負載不會受到影響，且可以繼續工作，不會中斷。</span><span class="sxs-lookup"><span data-stu-id="519e9-134">Workloads that use Service B and C are not affected and can continue working without interruption.</span></span>

![](./_images/bulkhead-1.png) 

<span data-ttu-id="519e9-135">下圖顯示呼叫單一服務的多個用戶端。</span><span class="sxs-lookup"><span data-stu-id="519e9-135">The next diagram shows multiple clients calling a single service.</span></span> <span data-ttu-id="519e9-136">每個用戶端都被指派個別的服務執行個體。</span><span class="sxs-lookup"><span data-stu-id="519e9-136">Each client is assigned a separate service instance.</span></span> <span data-ttu-id="519e9-137">用戶端 1 提出了太多的要求，而壓倒其執行個體。</span><span class="sxs-lookup"><span data-stu-id="519e9-137">Client 1 has made too many requests and overwhelmed its instance.</span></span> <span data-ttu-id="519e9-138">由於每個服務執行個體相互隔離，其他用戶端可以繼續進行呼叫。</span><span class="sxs-lookup"><span data-stu-id="519e9-138">Because each service instance is isolated from the others, the other clients can continue making calls.</span></span>

![](./_images/bulkhead-2.png)
     
## <a name="issues-and-considerations"></a><span data-ttu-id="519e9-139">問題和考量</span><span class="sxs-lookup"><span data-stu-id="519e9-139">Issues and considerations</span></span>

- <span data-ttu-id="519e9-140">定義圍繞應用程式之商務和技術需求的分割區。</span><span class="sxs-lookup"><span data-stu-id="519e9-140">Define partitions around the business and technical requirements of the application.</span></span>
- <span data-ttu-id="519e9-141">在將服務或取用者分割到隔艙時，請考慮技術提供的隔離等級，以及成本、效能和管理性方面的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="519e9-141">When partitioning services or consumers into bulkheads, consider the level of isolation offered by the technology as well as the overhead in terms of cost, performance and manageability.</span></span>
- <span data-ttu-id="519e9-142">請考慮將隔艙與重試模式、斷路器模式和節流模式相結合，以提供更完善的錯誤處理。</span><span class="sxs-lookup"><span data-stu-id="519e9-142">Consider combining bulkheads with retry, circuit breaker, and throttling patterns to provide more sophisticated fault handling.</span></span>
- <span data-ttu-id="519e9-143">將取用者分割到隔艙時，請考慮使用處理序、執行緒集區和旗號。</span><span class="sxs-lookup"><span data-stu-id="519e9-143">When partitioning consumers into bulkheads, consider using processes, thread pools, and semaphores.</span></span> <span data-ttu-id="519e9-144">像 [Netflix Hystrix][hystrix] 和 [Polly][polly] 這樣的專案為建立取用者隔艙提供了一個架構。</span><span class="sxs-lookup"><span data-stu-id="519e9-144">Projects like [Netflix Hystrix][hystrix] and [Polly][polly] offer a framework for creating consumer bulkheads.</span></span>
- <span data-ttu-id="519e9-145">將服務分割到隔艙時，請考慮將它們部署至不同的虛擬機器、容器或處理序。</span><span class="sxs-lookup"><span data-stu-id="519e9-145">When partitioning services into bulkheads, consider deploying them into separate virtual machines, containers, or processes.</span></span> <span data-ttu-id="519e9-146">容器在相當低的額外負荷下，提供了資源隔離的良好平衡。</span><span class="sxs-lookup"><span data-stu-id="519e9-146">Containers offer a good balance of resource isolation with fairly low overhead.</span></span>
- <span data-ttu-id="519e9-147">使用非同步訊息進行通訊的服務可以透過不同的佇列集隔離。</span><span class="sxs-lookup"><span data-stu-id="519e9-147">Services that communicate using asynchronous messages can be isolated through different sets of queues.</span></span> <span data-ttu-id="519e9-148">每個佇列都可以有一組處理佇列訊息的專用執行個體，或者一組使用演算法來清除佇列並分派處理的執行個體。</span><span class="sxs-lookup"><span data-stu-id="519e9-148">Each queue can have a dedicated set of instances processing messages on the queue, or a single group of instances using an algorithm to dequeue and dispatch processing.</span></span>
- <span data-ttu-id="519e9-149">決定隔艙的資料粒度層級。</span><span class="sxs-lookup"><span data-stu-id="519e9-149">Determine the level of granularity for the bulkheads.</span></span> <span data-ttu-id="519e9-150">例如，如果要在分割區之間分配租用戶，則可以將每個租用戶放入個別的分割區，將多個租用戶放入一個分割區。</span><span class="sxs-lookup"><span data-stu-id="519e9-150">For example, if you want to distribute tenants across partitions, you could place each tenant into a separate partition, a put several tenants into one partition.</span></span>
- <span data-ttu-id="519e9-151">監視每個分割區的效能和 SLA。</span><span class="sxs-lookup"><span data-stu-id="519e9-151">Monitor each partition’s performance and SLA.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="519e9-152">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="519e9-152">When to use this pattern</span></span>

<span data-ttu-id="519e9-153">使用這種模式來：</span><span class="sxs-lookup"><span data-stu-id="519e9-153">Use this pattern to:</span></span>

- <span data-ttu-id="519e9-154">隔離用於取用一組後端服務的資源，特別是當應用程式可以提供某種程度的功能，即使其中一個服務沒有回應。</span><span class="sxs-lookup"><span data-stu-id="519e9-154">Isolate resources used to consume a set of backend services, especially if the application can provide some level of functionality even when one of the services is not responding.</span></span>
- <span data-ttu-id="519e9-155">隔離重要取用者與標準取用者。</span><span class="sxs-lookup"><span data-stu-id="519e9-155">Isolate critical consumers from standard consumers.</span></span>
- <span data-ttu-id="519e9-156">保護應用程序免受連鎖性失效。</span><span class="sxs-lookup"><span data-stu-id="519e9-156">Protect the application from cascading failures.</span></span>

<span data-ttu-id="519e9-157">此模式可能不適合下列時機︰</span><span class="sxs-lookup"><span data-stu-id="519e9-157">This pattern may not be suitable when:</span></span>

- <span data-ttu-id="519e9-158">專案中不接受資源低效率使用。</span><span class="sxs-lookup"><span data-stu-id="519e9-158">Less efficient use of resources may not be acceptable in the project.</span></span>
- <span data-ttu-id="519e9-159">增加的複雜性是沒有必要的</span><span class="sxs-lookup"><span data-stu-id="519e9-159">The added complexity is not necessary</span></span>

## <a name="example"></a><span data-ttu-id="519e9-160">範例</span><span class="sxs-lookup"><span data-stu-id="519e9-160">Example</span></span>

<span data-ttu-id="519e9-161">下列 Kubernetes 設定檔會建立一個隔離容器來執行單一服務，並具有自己的 CPU 和記憶體資源與限制。</span><span class="sxs-lookup"><span data-stu-id="519e9-161">The following Kubernetes configuration file creates an isolated container to run a single service, with its own CPU and memory resources and limits.</span></span>

```yml
apiVersion: v1
kind: Pod
metadata:
  name: drone-management
spec:
  containers:
  - name: drone-management-container
    image: drone-service
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "1"
```

## <a name="related-guidance"></a><span data-ttu-id="519e9-162">相關指導方針</span><span class="sxs-lookup"><span data-stu-id="519e9-162">Related guidance</span></span>

- [<span data-ttu-id="519e9-163">斷路器模式</span><span class="sxs-lookup"><span data-stu-id="519e9-163">Circuit Breaker pattern</span></span>](./circuit-breaker.md)
- [<span data-ttu-id="519e9-164">為 Azure 設計有彈性的應用程式</span><span class="sxs-lookup"><span data-stu-id="519e9-164">Designing resilient applications for Azure</span></span>](../resiliency/index.md)
- [<span data-ttu-id="519e9-165">重試模式</span><span class="sxs-lookup"><span data-stu-id="519e9-165">Retry pattern</span></span>](./retry.md)
- [<span data-ttu-id="519e9-166">節流模式</span><span class="sxs-lookup"><span data-stu-id="519e9-166">Throttling pattern</span></span>](./throttling.md)


<!-- links -->

[hystrix]: https://github.com/Netflix/Hystrix
[polly]: https://github.com/App-vNext/Polly
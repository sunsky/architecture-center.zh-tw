---
title: 佇列型負載調節模式
titleSuffix: Cloud Design Patterns
description: 使用佇列來作為工作與其所叫用服務之間的緩衝區，以使間歇性的繁重負載順暢。
keywords: 設計模式
author: dragon119
ms.date: 01/02/2019
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: c736afced1b0478e8eb1a2694acc4d6a6f0c62fc
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58248723"
---
# <a name="queue-based-load-leveling-pattern"></a><span data-ttu-id="d909c-104">佇列型負載調節模式</span><span class="sxs-lookup"><span data-stu-id="d909c-104">Queue-Based Load Leveling pattern</span></span>

<span data-ttu-id="d909c-105">使用佇列來作為工作與其所叫用服務之間的緩衝區，以緩和導致服務失敗或工作逾時之間歇性的繁重負載。這可協助將需求尖峰對工作及服務之可用性和回應性的影響降到最低。</span><span class="sxs-lookup"><span data-stu-id="d909c-105">Use a queue that acts as a buffer between a task and a service it invokes in order to smooth intermittent heavy loads that can cause the service to fail or the task to time out. This can help to minimize the impact of peaks in demand on availability and responsiveness for both the task and the service.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="d909c-106">內容和問題</span><span class="sxs-lookup"><span data-stu-id="d909c-106">Context and problem</span></span>

<span data-ttu-id="d909c-107">許多雲端解決方案都涉及執行叫用服務的工作。</span><span class="sxs-lookup"><span data-stu-id="d909c-107">Many solutions in the cloud involve running tasks that invoke services.</span></span> <span data-ttu-id="d909c-108">在此環境中，如果服務受間歇性的繁重負載限制，就可能造成效能或可靠性問題。</span><span class="sxs-lookup"><span data-stu-id="d909c-108">In this environment, if a service is subjected to intermittent heavy loads, it can cause performance or reliability issues.</span></span>

<span data-ttu-id="d909c-109">服務可以與使用它的工作屬於同一個解決方案，也可以是提供對常用資源 (例如快取或儲存體服務) 之存取權的協力廠商服務。</span><span class="sxs-lookup"><span data-stu-id="d909c-109">A service could be part of the same solution as the tasks that use it, or it could be a third-party service providing access to frequently used resources such as a cache or a storage service.</span></span> <span data-ttu-id="d909c-110">如果一些同時執行的工作使用相同的服務，則可能在任何時間都很難預測對該服務的要求量。</span><span class="sxs-lookup"><span data-stu-id="d909c-110">If the same service is used by a number of tasks running concurrently, it can be difficult to predict the volume of requests to the service at any time.</span></span>

<span data-ttu-id="d909c-111">服務可能會經歷造成它多載的需求尖峰，而無法及時回應要求。</span><span class="sxs-lookup"><span data-stu-id="d909c-111">A service might experience peaks in demand that cause it to overload and be unable to respond to requests in a timely manner.</span></span> <span data-ttu-id="d909c-112">讓大量的並行要求湧入服務時，如果服務無法處理這些要求所造成的爭用，就也可能導致服務失敗。</span><span class="sxs-lookup"><span data-stu-id="d909c-112">Flooding a service with a large number of concurrent requests can also result in the service failing if it's unable to handle the contention these requests cause.</span></span>

## <a name="solution"></a><span data-ttu-id="d909c-113">解決方法</span><span class="sxs-lookup"><span data-stu-id="d909c-113">Solution</span></span>

<span data-ttu-id="d909c-114">重構解決方案並在工作與服務之間導入佇列。</span><span class="sxs-lookup"><span data-stu-id="d909c-114">Refactor the solution and introduce a queue between the task and the service.</span></span> <span data-ttu-id="d909c-115">工作與服務會以非同步方式執行。</span><span class="sxs-lookup"><span data-stu-id="d909c-115">The task and the service run asynchronously.</span></span> <span data-ttu-id="d909c-116">工作會將包含服務所需資料的訊息張貼至佇列。</span><span class="sxs-lookup"><span data-stu-id="d909c-116">The task posts a message containing the data required by the service to a queue.</span></span> <span data-ttu-id="d909c-117">佇列會作為緩衝區來儲存訊息，直到服務擷取該訊息為止。</span><span class="sxs-lookup"><span data-stu-id="d909c-117">The queue acts as a buffer, storing the message until it's retrieved by the service.</span></span> <span data-ttu-id="d909c-118">服務會從佇列擷取訊息，然後處理這些訊息。</span><span class="sxs-lookup"><span data-stu-id="d909c-118">The service retrieves the messages from the queue and processes them.</span></span> <span data-ttu-id="d909c-119">來自一些工作的要求 (可能以高變動的速率產生) 可以透過相同的訊息佇列傳遞給服務。</span><span class="sxs-lookup"><span data-stu-id="d909c-119">Requests from a number of tasks, which can be generated at a highly variable rate, can be passed to the service through the same message queue.</span></span> <span data-ttu-id="d909c-120">下圖顯示如何使用佇列來調節服務上的負載。</span><span class="sxs-lookup"><span data-stu-id="d909c-120">This figure shows using a queue to level the load on a service.</span></span>

![圖 1 - 使用佇列來調節服務上的負載](./_images/queue-based-load-leveling-pattern.png)

<span data-ttu-id="d909c-122">佇列會將工作與服務分離，服務可以用自己的步調來處理訊息，而不需顧及來自並行工作的要求量。</span><span class="sxs-lookup"><span data-stu-id="d909c-122">The queue decouples the tasks from the service, and the service can handle the messages at its own pace regardless of the volume of requests from concurrent tasks.</span></span> <span data-ttu-id="d909c-123">此外，在工作將訊息張貼至佇列時，如果服務無法供使用，也不會對工作造成延遲。</span><span class="sxs-lookup"><span data-stu-id="d909c-123">Additionally, there's no delay to a task if the service isn't available at the time it posts a message to the queue.</span></span>

<span data-ttu-id="d909c-124">此模式提供下列優點：</span><span class="sxs-lookup"><span data-stu-id="d909c-124">This pattern provides the following benefits:</span></span>

- <span data-ttu-id="d909c-125">它可協助將可用性提升到最高，因為在服務增加的延遲對應用程式不會有立即且直接的影響，即使服務無法供使用或目前並沒有在處理訊息，應用程式仍可繼續將訊息張貼至佇列。</span><span class="sxs-lookup"><span data-stu-id="d909c-125">It can help to maximize availability because delays arising in services won't have an immediate and direct impact on the application, which can continue to post messages to the queue even when the service isn't available or isn't currently processing messages.</span></span>
- <span data-ttu-id="d909c-126">它可協助將延展性擴充到最大，因為佇列數目和服務數目都可因應需求進行調整。</span><span class="sxs-lookup"><span data-stu-id="d909c-126">It can help to maximize scalability because both the number of queues and the number of services can be varied to meet demand.</span></span>
- <span data-ttu-id="d909c-127">它可協助控制成本，因為只需部署適當數目的服務執行個體來滿足平均負載需求即可，無須考慮尖峰負載需求。</span><span class="sxs-lookup"><span data-stu-id="d909c-127">It can help to control costs because the number of service instances deployed only have to be adequate to meet average load rather than the peak load.</span></span>

    >  <span data-ttu-id="d909c-128">有些服務會在需求達到臨界值時實作節流，超出此臨界值時系統可能就會發生失敗。</span><span class="sxs-lookup"><span data-stu-id="d909c-128">Some services implement throttling when demand reaches a threshold beyond which the system could fail.</span></span> <span data-ttu-id="d909c-129">節流會縮減可用的功能。</span><span class="sxs-lookup"><span data-stu-id="d909c-129">Throttling can reduce the functionality available.</span></span> <span data-ttu-id="d909c-130">您可以搭配這些服務實作負載調節，以確保不會達到此臨界值。</span><span class="sxs-lookup"><span data-stu-id="d909c-130">You can implement load leveling with these services to ensure that this threshold isn't reached.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="d909c-131">問題和考量</span><span class="sxs-lookup"><span data-stu-id="d909c-131">Issues and considerations</span></span>

<span data-ttu-id="d909c-132">當您決定如何實作此模式時，請考慮下列幾點：</span><span class="sxs-lookup"><span data-stu-id="d909c-132">Consider the following points when deciding how to implement this pattern:</span></span>

- <span data-ttu-id="d909c-133">必須實作應用程式邏輯來控制服務處理訊息的速率，以避免超出目標資源負荷。</span><span class="sxs-lookup"><span data-stu-id="d909c-133">It's necessary to implement application logic that controls the rate at which services handle messages to avoid overwhelming the target resource.</span></span> <span data-ttu-id="d909c-134">避免將需求尖峰傳遞到下一個系統階段。</span><span class="sxs-lookup"><span data-stu-id="d909c-134">Avoid passing spikes in demand to the next stage of the system.</span></span> <span data-ttu-id="d909c-135">測試負載中的系統以確保它提供所需的調節，並調整處理訊息的佇列數目和服務執行個體數目以達到此目的。</span><span class="sxs-lookup"><span data-stu-id="d909c-135">Test the system under load to ensure that it provides the required leveling, and adjust the number of queues and the number of service instances that handle messages to achieve this.</span></span>
- <span data-ttu-id="d909c-136">訊息佇列是一種單向通訊機制。</span><span class="sxs-lookup"><span data-stu-id="d909c-136">Message queues are a one-way communication mechanism.</span></span> <span data-ttu-id="d909c-137">如果工作預期要從服務收到回覆，可能就必須實作可供服務用來傳送回應的機制。</span><span class="sxs-lookup"><span data-stu-id="d909c-137">If a task expects a reply from a service, it might be necessary to implement a mechanism that the service can use to send a response.</span></span> <span data-ttu-id="d909c-138">如需詳細資訊，請參閱[非同步傳訊入門](https://msdn.microsoft.com/library/dn589781.aspx) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="d909c-138">For more information, see the [Asynchronous Messaging Primer](https://msdn.microsoft.com/library/dn589781.aspx).</span></span>
- <span data-ttu-id="d909c-139">對接聽佇列上要求的服務套用自動調整時，請小心。</span><span class="sxs-lookup"><span data-stu-id="d909c-139">Be careful if you apply autoscaling to services that are listening for requests on the queue.</span></span> <span data-ttu-id="d909c-140">這可能導致對這些服務所共用之任何資源發生爭用的情況增加，而降低使用佇列來調節負載的效果。</span><span class="sxs-lookup"><span data-stu-id="d909c-140">This can result in increased contention for any resources that these services share and diminish the effectiveness of using the queue to level the load.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="d909c-141">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="d909c-141">When to use this pattern</span></span>

<span data-ttu-id="d909c-142">如果應用程式使用受多載限制的服務，就適用此模式。</span><span class="sxs-lookup"><span data-stu-id="d909c-142">This pattern is useful to any application that uses services that are subject to overloading.</span></span>

<span data-ttu-id="d909c-143">如果應用程式預期要在最短的延遲時間內從服務收到回應，則不適用此模式。</span><span class="sxs-lookup"><span data-stu-id="d909c-143">This pattern isn't useful if the application expects a response from the service with minimal latency.</span></span>

## <a name="example"></a><span data-ttu-id="d909c-144">範例</span><span class="sxs-lookup"><span data-stu-id="d909c-144">Example</span></span>

<span data-ttu-id="d909c-145">Web 應用程式會將資料寫入外部資料存放區。</span><span class="sxs-lookup"><span data-stu-id="d909c-145">A web app writes data to an external data store.</span></span> <span data-ttu-id="d909c-146">如果 Web 應用程式有大量的執行個體同時執行，資料存放區回應要求的速度可能會不夠快，而導致要求逾時、遭到節流或失敗。</span><span class="sxs-lookup"><span data-stu-id="d909c-146">If a large number of instances of the web app run concurrently, the data store might be unable to respond to requests quickly enough, causing requests to time out, be throttled, or otherwise fail.</span></span> <span data-ttu-id="d909c-147">下圖顯示來自應用程式執行個體的大量並行要求導致資料存放區超過負荷。</span><span class="sxs-lookup"><span data-stu-id="d909c-147">The following diagram shows a data store being overwhelmed by a large number of concurrent requests from instances of an application.</span></span>

![圖 2 - 來自 Web 應用程式執行個體的大量並行要求導致服務超過負荷](./_images/queue-based-load-leveling-overwhelmed.png)

<span data-ttu-id="d909c-149">若要解決此問題，您可以使用佇列來調節應用程式執行個體與資料存放區之間的負載。</span><span class="sxs-lookup"><span data-stu-id="d909c-149">To resolve this, you can use a queue to level the load between the application instances and the data store.</span></span> <span data-ttu-id="d909c-150">Azure Functions 應用程式會從佇列讀取訊息，並針對資料存放區執行讀取/寫入要求。</span><span class="sxs-lookup"><span data-stu-id="d909c-150">An Azure Functions app reads the messages from the queue and performs the read/write requests to the data store.</span></span> <span data-ttu-id="d909c-151">函式應用程式中的應用程式邏輯可以控制將要求傳遞給資料存放區的速率，以防止存放區超過負荷。</span><span class="sxs-lookup"><span data-stu-id="d909c-151">The application logic in the function app can control the rate at which it passes requests to the data store, to prevent the store from being overwhelmed.</span></span> <span data-ttu-id="d909c-152">(否則，函式應用程式只會在後端重新引入相同的問題)。</span><span class="sxs-lookup"><span data-stu-id="d909c-152">(Otherwise the function app will just re-introduce the same problem at the back end.)</span></span>

![圖 3 - 使用佇列和函式應用程式來調節負載](./_images/queue-based-load-leveling-function.png)



## <a name="related-patterns-and-guidance"></a><span data-ttu-id="d909c-154">相關的模式和指導方針</span><span class="sxs-lookup"><span data-stu-id="d909c-154">Related patterns and guidance</span></span>

<span data-ttu-id="d909c-155">實作此模式時，下列模式和指導方針可能也相關：</span><span class="sxs-lookup"><span data-stu-id="d909c-155">The following patterns and guidance might also be relevant when implementing this pattern:</span></span>

- <span data-ttu-id="d909c-156">[非同步傳訊入門](https://msdn.microsoft.com/library/dn589781.aspx)。</span><span class="sxs-lookup"><span data-stu-id="d909c-156">[Asynchronous Messaging Primer](https://msdn.microsoft.com/library/dn589781.aspx).</span></span> <span data-ttu-id="d909c-157">訊息佇列原本就是非同步的。</span><span class="sxs-lookup"><span data-stu-id="d909c-157">Message queues are inherently asynchronous.</span></span> <span data-ttu-id="d909c-158">如果將工作從與服務直接通訊改成使用訊息佇列，可能就必須重新設計工作中的應用程式邏輯。</span><span class="sxs-lookup"><span data-stu-id="d909c-158">It might be necessary to redesign the application logic in a task if it's adapted from communicating directly with a service to using a message queue.</span></span> <span data-ttu-id="d909c-159">同樣地，可能必須重構服務以從訊息佇列接受要求。</span><span class="sxs-lookup"><span data-stu-id="d909c-159">Similarly, it might be necessary to refactor a service to accept requests from a message queue.</span></span> <span data-ttu-id="d909c-160">或者，也可以實作 Proxy 服務，如範例中所述。</span><span class="sxs-lookup"><span data-stu-id="d909c-160">Alternatively, it might be possible to implement a proxy service, as described in the example.</span></span>

- <span data-ttu-id="d909c-161">[競爭取用者模式](./competing-consumers.md)。</span><span class="sxs-lookup"><span data-stu-id="d909c-161">[Competing Consumers pattern](./competing-consumers.md).</span></span> <span data-ttu-id="d909c-162">您可以執行多個服務執行個體，每個執行個體都作為來自負載調節佇列的訊息取用者。</span><span class="sxs-lookup"><span data-stu-id="d909c-162">It might be possible to run multiple instances of a service, each acting as a message consumer from the load-leveling queue.</span></span> <span data-ttu-id="d909c-163">您可以使用此方法來調整接收訊息並將訊息傳遞給服務的速率。</span><span class="sxs-lookup"><span data-stu-id="d909c-163">You can use this approach to adjust the rate at which messages are received and passed to a service.</span></span>

- <span data-ttu-id="d909c-164">[節流模式](./throttling.md)。</span><span class="sxs-lookup"><span data-stu-id="d909c-164">[Throttling pattern](./throttling.md).</span></span> <span data-ttu-id="d909c-165">有一個搭配服務實作節流的簡單方式，就是使用佇列型負載調節，然後透過訊息佇列將所有要求路由傳送至服務。</span><span class="sxs-lookup"><span data-stu-id="d909c-165">A simple way to implement throttling with a service is to use queue-based load leveling and route all requests to a service through a message queue.</span></span> <span data-ttu-id="d909c-166">服務可以用確保服務所需資源不會耗盡且可減少可能發生之爭用情況的速率來處理要求。</span><span class="sxs-lookup"><span data-stu-id="d909c-166">The service can process requests at a rate that ensures that resources required by the service aren't exhausted, and to reduce the amount of contention that could occur.</span></span>

- <span data-ttu-id="d909c-167">[選擇 Azure 傳訊服務](/azure/event-grid/compare-messaging-services)。</span><span class="sxs-lookup"><span data-stu-id="d909c-167">[Choose between Azure messaging services](/azure/event-grid/compare-messaging-services).</span></span> <span data-ttu-id="d909c-168">有關 Azure 應用程式中選擇傳訊和佇列機制的資訊。</span><span class="sxs-lookup"><span data-stu-id="d909c-168">Information about choosing a messaging and queuing mechanism in Azure applications.</span></span>

- <span data-ttu-id="d909c-169">[改善 Azure Web 應用程式的延展性](../reference-architectures/app-service-web-app/scalable-web-app.md)。</span><span class="sxs-lookup"><span data-stu-id="d909c-169">[Improve scalability in an Azure web application](../reference-architectures/app-service-web-app/scalable-web-app.md).</span></span> <span data-ttu-id="d909c-170">此參考架構中包含佇列型負載調節功能。</span><span class="sxs-lookup"><span data-stu-id="d909c-170">This reference architecture includes queue-based load leveling as part of the architecture.</span></span>

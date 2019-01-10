---
title: 競爭取用者模式
titleSuffix: Cloud Design Patterns
description: 讓多個並行取用者處理在相同傳訊通道上接收的訊息。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
ms.custom: seodec18
ms.openlocfilehash: 77459ff42422969acdc83e66535197547d555de1
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54112102"
---
# <a name="competing-consumers-pattern"></a><span data-ttu-id="4c2fb-104">競爭取用者模式</span><span class="sxs-lookup"><span data-stu-id="4c2fb-104">Competing Consumers pattern</span></span>

[!INCLUDE [header](../_includes/header.md)]

<span data-ttu-id="4c2fb-105">讓多個並行取用者處理在相同傳訊通道上接收的訊息。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-105">Enable multiple concurrent consumers to process messages received on the same messaging channel.</span></span> <span data-ttu-id="4c2fb-106">此方法可讓系統同時處理多個訊息，以達到最佳輸送量、改進延展性及可用性，以及平衡工作負載。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-106">This enables a system to process multiple messages concurrently to optimize throughput, to improve scalability and availability, and to balance the workload.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="4c2fb-107">內容和問題</span><span class="sxs-lookup"><span data-stu-id="4c2fb-107">Context and problem</span></span>

<span data-ttu-id="4c2fb-108">在雲端中執行的應用程式預期會處理大量要求。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-108">An application running in the cloud is expected to handle a large number of requests.</span></span> <span data-ttu-id="4c2fb-109">每個要求並非以同步方式處理，常用的技巧是讓應用程式透過傳訊系統將要求傳遞至以非同步方式處理的其他服務 (取用者服務)。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-109">Rather than process each request synchronously, a common technique is for the application to pass them through a messaging system to another service (a consumer service) that handles them asynchronously.</span></span> <span data-ttu-id="4c2fb-110">此策略有助於確保處理要求時，應用程式中的商務邏輯不會被封鎖。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-110">This strategy helps to ensure that the business logic in the application isn't blocked while the requests are being processed.</span></span>

<span data-ttu-id="4c2fb-111">基於多種原因，要求數目可能會隨著時間而明顯起伏。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-111">The number of requests can vary significantly over time for many reasons.</span></span> <span data-ttu-id="4c2fb-112">來自多個租用戶突增的使用者活動或彙總要求，可能會造成無法預期的工作負載。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-112">A sudden increase in user activity or aggregated requests coming from multiple tenants can cause an unpredictable workload.</span></span> <span data-ttu-id="4c2fb-113">在尖峰時間，系統每秒可能需要處理數百筆要求，而其他時間的數字可能非常小。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-113">At peak hours a system might need to process many hundreds of requests per second, while at other times the number could be very small.</span></span> <span data-ttu-id="4c2fb-114">此外，處理這些要求所需執行工作的性質可能變化極大。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-114">Additionally, the nature of the work performed to handle these requests might be highly variable.</span></span> <span data-ttu-id="4c2fb-115">使用單一取用者服務執行個體，可能會導致該執行個體大量湧入要求，或傳訊系統可能因從應用程式大量湧進的訊息而超載。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-115">Using a single instance of the consumer service can cause that instance to become flooded with requests, or the messaging system might be overloaded by an influx of messages coming from the application.</span></span> <span data-ttu-id="4c2fb-116">為了處理此種變動的工作負載，系統可以執行多個取用者服務執行個體。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-116">To handle this fluctuating workload, the system can run multiple instances of the consumer service.</span></span> <span data-ttu-id="4c2fb-117">不過，這些取用者必須加以協調，以確保每個訊息只會傳遞給單一取用者。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-117">However, these consumers must be coordinated to ensure that each message is only delivered to a single consumer.</span></span> <span data-ttu-id="4c2fb-118">工作負載必須在取用者之間進行負載平衡，以防止某個執行個體變成瓶頸。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-118">The workload also needs to be load balanced across consumers to prevent an instance from becoming a bottleneck.</span></span>

## <a name="solution"></a><span data-ttu-id="4c2fb-119">解決方法</span><span class="sxs-lookup"><span data-stu-id="4c2fb-119">Solution</span></span>

<span data-ttu-id="4c2fb-120">您可以使用訊息佇列來實作應用程式和取用者服務執行個體之間的通訊通道。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-120">Use a message queue to implement the communication channel between the application and the instances of the consumer service.</span></span> <span data-ttu-id="4c2fb-121">應用程式會以訊息形式將要求張貼至佇列，取用者服務執行個體會從佇列接收訊息並加以處理。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-121">The application posts requests in the form of messages to the queue, and the consumer service instances receive messages from the queue and process them.</span></span> <span data-ttu-id="4c2fb-122">這個方法可讓相同取用者服務執行個體的集區得以處理來自應用程式所有執行個體的訊息。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-122">This approach enables the same pool of consumer service instances to handle messages from any instance of the application.</span></span> <span data-ttu-id="4c2fb-123">此圖說明使用訊息佇列將工作分配給服務的執行個體。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-123">The figure illustrates using a message queue to distribute work to instances of a service.</span></span>

![使用訊息佇列將工作分配給服務的執行個體](./_images/competing-consumers-diagram.png)

<span data-ttu-id="4c2fb-125">此解決方案有下列優點：</span><span class="sxs-lookup"><span data-stu-id="4c2fb-125">This solution has the following benefits:</span></span>

- <span data-ttu-id="4c2fb-126">它提供負載調節系統，可應付應用程式執行個體傳送要求量的大量變化。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-126">It provides a load-leveled system that can handle wide variations in the volume of requests sent by application instances.</span></span> <span data-ttu-id="4c2fb-127">佇列會做為應用程式執行個體與取用者服務執行個體之間的緩衝區。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-127">The queue acts as a buffer between the application instances and the consumer service instances.</span></span> <span data-ttu-id="4c2fb-128">這可協助將對應用程式和服務執行個體可用性和回應性的影響降到最低，如[佇列型負載調節模式](./queue-based-load-leveling.md)所述。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-128">This can help to minimize the impact on availability and responsiveness for both the application and the service instances, as described by the [Queue-based Load Leveling pattern](./queue-based-load-leveling.md).</span></span> <span data-ttu-id="4c2fb-129">處理需要長時間處理的訊息，並不妨礙取用者服務的其他執行個體同時處理其他訊息。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-129">Handling a message that requires some long-running processing doesn't prevent other messages from being handled concurrently by other instances of the consumer service.</span></span>

- <span data-ttu-id="4c2fb-130">它可提升可靠性。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-130">It improves reliability.</span></span> <span data-ttu-id="4c2fb-131">如果產生者與取用者直接通訊而不是使用此模式，但不監視取用者，則訊息很可能遺失或因為取用者失敗而無法處理。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-131">If a producer communicates directly with a consumer instead of using this pattern, but doesn't monitor the consumer, there's a high probability that messages could be lost or fail to be processed if the consumer fails.</span></span> <span data-ttu-id="4c2fb-132">在此模式中，訊息不是傳送至特定服務執行個體。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-132">In this pattern, messages aren't sent to a specific service instance.</span></span> <span data-ttu-id="4c2fb-133">失敗的服務執行個體將不會封鎖產生者，任何工作中的服務執行個體都可以處理訊息。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-133">A failed service instance won't block a producer, and messages can be processed by any working service instance.</span></span>

- <span data-ttu-id="4c2fb-134">它不需要取用者之間或生產者與取用者執行個體之間的複雜協調。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-134">It doesn't require complex coordination between the consumers, or between the producer and the consumer instances.</span></span> <span data-ttu-id="4c2fb-135">訊息佇列可確保每個訊息會傳遞至少一次。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-135">The message queue ensures that each message is delivered at least once.</span></span>

- <span data-ttu-id="4c2fb-136">它是可調整的。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-136">It's scalable.</span></span> <span data-ttu-id="4c2fb-137">系統可隨著訊息量的波動來動態增加或減少取用者服務的執行個體數。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-137">The system can dynamically increase or decrease the number of instances of the consumer service as the volume of messages fluctuates.</span></span>

- <span data-ttu-id="4c2fb-138">如果訊息佇列提供交易式讀取作業，它可以加強復原功能。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-138">It can improve resiliency if the message queue provides transactional read operations.</span></span> <span data-ttu-id="4c2fb-139">如果取用者服務執行個體在交易式作業過程中讀取並處理訊息，且取用者服務執行個體失敗，則此模式可確保訊息能夠傳回佇列，由取用者服務的其他執行個體接收並處理。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-139">If a consumer service instance reads and processes the message as part of a transactional operation, and the consumer service instance fails, this pattern can ensure that the message will be returned to the queue to be picked up and handled by another instance of the consumer service.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="4c2fb-140">問題和考量</span><span class="sxs-lookup"><span data-stu-id="4c2fb-140">Issues and considerations</span></span>

<span data-ttu-id="4c2fb-141">當您決定如何實作此模式時，請考慮下列幾點：</span><span class="sxs-lookup"><span data-stu-id="4c2fb-141">Consider the following points when deciding how to implement this pattern:</span></span>

- <span data-ttu-id="4c2fb-142">**訊息排序**。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-142">**Message ordering**.</span></span> <span data-ttu-id="4c2fb-143">取用者服務執行個體接收訊息的順序並不一定，而且不一定反映建立訊息的順序。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-143">The order in which consumer service instances receive messages isn't guaranteed, and doesn't necessarily reflect the order in which the messages were created.</span></span> <span data-ttu-id="4c2fb-144">設計系統時，請確保訊息處理為等冪方式，因為這將有助避免對訊息處理順序有依賴性。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-144">Design the system to ensure that message processing is idempotent because this will help to eliminate any dependency on the order in which messages are handled.</span></span> <span data-ttu-id="4c2fb-145">如需詳細資訊，請參閱 Jonathon Oliver 部落格上的[等冪模式](https://blog.jonathanoliver.com/idempotency-patterns/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-145">For more information, see [Idempotency Patterns](https://blog.jonathanoliver.com/idempotency-patterns/) on Jonathon Oliver’s blog.</span></span>

    > <span data-ttu-id="4c2fb-146">Microsoft Azure 服務匯流排佇列可使用訊息工作階段，以實作保證先進先出的訊息順序。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-146">Microsoft Azure Service Bus Queues can implement guaranteed first-in-first-out ordering of messages by using message sessions.</span></span> <span data-ttu-id="4c2fb-147">如需詳細資訊，請參閱[使用工作階段的傳訊模式](https://msdn.microsoft.com/magazine/jj863132.aspx) \(機器翻譯\)。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-147">For more information, see [Messaging Patterns Using Sessions](https://msdn.microsoft.com/magazine/jj863132.aspx).</span></span>

- <span data-ttu-id="4c2fb-148">**設計服務以提供復原功能**。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-148">**Designing services for resiliency**.</span></span> <span data-ttu-id="4c2fb-149">如果系統設計為偵測並重新啟動失敗的服務執行個體，則可能需要實作由服務執行個體以等冪作業來執行處理，以將單一訊息擷取和處理超過一次的效應降至最低。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-149">If the system is designed to detect and restart failed service instances, it might be necessary to implement the processing performed by the service instances as idempotent operations to minimize the effects of a single message being retrieved and processed more than once.</span></span>

- <span data-ttu-id="4c2fb-150">**偵測有害訊息**。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-150">**Detecting poison messages**.</span></span> <span data-ttu-id="4c2fb-151">訊息格式不正確，或工作需要存取的資源無法使用，可能會導致服務執行個體失敗。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-151">A malformed message, or a task that requires access to resources that aren't available, can cause a service instance to fail.</span></span> <span data-ttu-id="4c2fb-152">系統應該避免這類訊息傳回佇列，改為擷取這些訊息的詳細資料並儲存到其他位置，以視需要對它們進行分析。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-152">The system should prevent such messages being returned to the queue, and instead capture and store the details of these messages elsewhere so that they can be analyzed if necessary.</span></span>

- <span data-ttu-id="4c2fb-153">**處理結果**。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-153">**Handling results**.</span></span> <span data-ttu-id="4c2fb-154">處理訊息的服務執行個體與產生訊息的應用程式邏輯是完全分開的，並可能無法彼此直接通訊。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-154">The service instance handling a message is fully decoupled from the application logic that generates the message, and they might not be able to communicate directly.</span></span> <span data-ttu-id="4c2fb-155">如果服務執行個體產生的結果必須傳回應用程式邏輯，這項資訊必須儲存在雙方都能存取的位置。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-155">If the service instance generates results that must be passed back to the application logic, this information must be stored in a location that's accessible to both.</span></span> <span data-ttu-id="4c2fb-156">為避免應用程式邏輯擷取不完整的資料，系統必須指出處理已完成。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-156">In order to prevent the application logic from retrieving incomplete data the system must indicate when processing is complete.</span></span>

     > <span data-ttu-id="4c2fb-157">如果您使用 Azure，背景工作處理序可以使用專用的訊息回覆佇列將結果傳回應用程式邏輯。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-157">If you're using Azure, a worker process can pass results back to the application logic by using a dedicated message reply queue.</span></span> <span data-ttu-id="4c2fb-158">應用程式邏輯必須能將這些結果與原始訊息相互關聯。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-158">The application logic must be able to correlate these results with the original message.</span></span> <span data-ttu-id="4c2fb-159">此案例會在[非同步傳訊入門](https://msdn.microsoft.com/library/dn589781.aspx) \(英文\) 中詳細說明。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-159">This scenario is described in more detail in the [Asynchronous Messaging Primer](https://msdn.microsoft.com/library/dn589781.aspx).</span></span>

- <span data-ttu-id="4c2fb-160">**調整傳訊系統大小**。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-160">**Scaling the messaging system**.</span></span> <span data-ttu-id="4c2fb-161">在大規模的解決方案中，單一訊息佇列的訊息數目可能會大量湧入，因而成為系統中的瓶頸。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-161">In a large-scale solution, a single message queue could be overwhelmed by the number of messages and become a bottleneck in the system.</span></span> <span data-ttu-id="4c2fb-162">在此情況下，請考慮分割傳訊系統，以從特定的生產者傳送訊息至特定佇列，或使用負載平衡將訊息分散至多個訊息佇列。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-162">In this situation, consider partitioning the messaging system to send messages from specific producers to a particular queue, or use load balancing to distribute messages across multiple message queues.</span></span>

- <span data-ttu-id="4c2fb-163">**確保傳訊系統的可靠性**。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-163">**Ensuring reliability of the messaging system**.</span></span> <span data-ttu-id="4c2fb-164">您需要可靠的傳訊系統，以保證應用程式將訊息加入佇列之後，將不會遺失訊息。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-164">A reliable messaging system is needed to guarantee that after the application enqueues a message it won't be lost.</span></span> <span data-ttu-id="4c2fb-165">為確保所有訊息會都傳遞至少一次，這是必要的。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-165">This is essential for ensuring that all messages are delivered at least once.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="4c2fb-166">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="4c2fb-166">When to use this pattern</span></span>

<span data-ttu-id="4c2fb-167">使用此模式的時機包括：</span><span class="sxs-lookup"><span data-stu-id="4c2fb-167">Use this pattern when:</span></span>

- <span data-ttu-id="4c2fb-168">應用程式的工作負載會分成可非同步執行的數個工作。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-168">The workload for an application is divided into tasks that can run asynchronously.</span></span>
- <span data-ttu-id="4c2fb-169">工作是獨立的，而且可平行執行。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-169">Tasks are independent and can run in parallel.</span></span>
- <span data-ttu-id="4c2fb-170">工作量具有高變動性，需要可調整的解決方案。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-170">The volume of work is highly variable, requiring a scalable solution.</span></span>
- <span data-ttu-id="4c2fb-171">解決方案必須提供高可用性，而且必須具有工作處理失敗的復原功能。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-171">The solution must provide high availability, and must be resilient if the processing for a task fails.</span></span>

<span data-ttu-id="4c2fb-172">此模式可能不適合下列時機︰</span><span class="sxs-lookup"><span data-stu-id="4c2fb-172">This pattern might not be useful when:</span></span>

- <span data-ttu-id="4c2fb-173">應用程式工作負載難以分割為不同的工作，或工作之間有高度相依性。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-173">It's not easy to separate the application workload into discrete tasks, or there's a high degree of dependence between tasks.</span></span>
- <span data-ttu-id="4c2fb-174">工作必須以同步方式執行，且應用程式邏輯必須等候工作完成才能繼續。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-174">Tasks must be performed synchronously, and the application logic must wait for a task to complete before continuing.</span></span>
- <span data-ttu-id="4c2fb-175">工作必須依特定順序執行。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-175">Tasks must be performed in a specific sequence.</span></span>

> <span data-ttu-id="4c2fb-176">某些傳訊系統支援工作階段，可讓產生者將訊息加以分組，並可確保它們會由相同的取用者處理。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-176">Some messaging systems support sessions that enable a producer to group messages together and ensure that they're all handled by the same consumer.</span></span> <span data-ttu-id="4c2fb-177">此機制可搭配使用高優先順序的訊息 (如果支援)，以實作依序將訊息從生產者傳遞至單一取用者的訊息順序形式。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-177">This mechanism can be used with prioritized messages (if they are supported) to implement a form of message ordering that delivers messages in sequence from a producer to a single consumer.</span></span>

## <a name="example"></a><span data-ttu-id="4c2fb-178">範例</span><span class="sxs-lookup"><span data-stu-id="4c2fb-178">Example</span></span>

<span data-ttu-id="4c2fb-179">Azure 提供儲存體佇列和服務匯流排佇列，可作為實作此模式的機制。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-179">Azure provides storage queues and Service Bus queues that can act as a mechanism for implementing this pattern.</span></span> <span data-ttu-id="4c2fb-180">應用程式邏輯可以張貼訊息至佇列，而實作為一或多個角色中工作的取用者可以從這個佇列擷取訊息並進行處理。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-180">The application logic can post messages to a queue, and consumers implemented as tasks in one or more roles can retrieve messages from this queue and process them.</span></span> <span data-ttu-id="4c2fb-181">為提供復原功能，服務匯流排佇列可讓取用者從佇列擷取訊息時使用 `PeekLock` 模式。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-181">For resiliency, a Service Bus queue enables a consumer to use `PeekLock` mode when it retrieves a message from the queue.</span></span> <span data-ttu-id="4c2fb-182">此模式不會實際移除訊息，只會針對其他取用者來隱藏訊息。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-182">This mode doesn't actually remove the message, but simply hides it from other consumers.</span></span> <span data-ttu-id="4c2fb-183">原始取用者完成處理訊息時，就可以刪除訊息。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-183">The original consumer can delete the message when it's finished processing it.</span></span> <span data-ttu-id="4c2fb-184">如果取用者失敗，查看鎖定將會逾時，訊息將會再次顯示，以允許其他取用者擷取訊息。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-184">If the consumer fails, the peek lock will time out and the message will become visible again, allowing another consumer to retrieve it.</span></span>

<span data-ttu-id="4c2fb-185">如需使用 Azure 服務匯流排佇列的詳細資訊，請參閱[服務匯流排佇列、主題和訂用帳戶](https://msdn.microsoft.com/library/windowsazure/hh367516.aspx)。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-185">For detailed information on using Azure Service Bus queues, see [Service Bus queues, topics, and subscriptions](https://msdn.microsoft.com/library/windowsazure/hh367516.aspx).</span></span>

<span data-ttu-id="4c2fb-186">如需使用 Azure 儲存體佇列的詳細資訊，請參閱[以 .NET 開始使用 Azure 佇列儲存體](/azure/storage/queues/storage-dotnet-how-to-use-queues)。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-186">For information on using Azure storage queues, see [Get started with Azure Queue storage using .NET](/azure/storage/queues/storage-dotnet-how-to-use-queues).</span></span>

<span data-ttu-id="4c2fb-187">[GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/competing-consumers) \(英文\) 提供 CompetingConsumers 解決方案中 `QueueManager` 類別的下列程式碼，其中顯示如何在 Web 或背景工作角色中使用 `Start` 事件處理常式中的 `QueueClient` 執行個體來建立佇列。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-187">The following code from the `QueueManager` class in CompetingConsumers solution available on [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/competing-consumers) shows how you can create a queue by using a `QueueClient` instance in the `Start` event handler in a web or worker role.</span></span>

```csharp
private string queueName = ...;
private string connectionString = ...;
...

public async Task Start()
{
  // Check if the queue already exists.
  var manager = NamespaceManager.CreateFromConnectionString(this.connectionString);
  if (!manager.QueueExists(this.queueName))
  {
    var queueDescription = new QueueDescription(this.queueName);

    // Set the maximum delivery count for messages in the queue. A message
    // is automatically dead-lettered after this number of deliveries. The
    // default value for dead letter count is 10.
    queueDescription.MaxDeliveryCount = 3;

    await manager.CreateQueueAsync(queueDescription);
  }
  ...

  // Create the queue client. By default the PeekLock method is used.
  this.client = QueueClient.CreateFromConnectionString(
    this.connectionString, this.queueName);
}
```

<span data-ttu-id="4c2fb-188">下一個程式碼片段示範應用程式如何建立和傳送訊息批次至佇列。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-188">The next code snippet shows how an application can create and send a batch of messages to the queue.</span></span>

```csharp
public async Task SendMessagesAsync()
{
  // Simulate sending a batch of messages to the queue.
  var messages = new List<BrokeredMessage>();

  for (int i = 0; i < 10; i++)
  {
    var message = new BrokeredMessage() { MessageId = Guid.NewGuid().ToString() };
    messages.Add(message);
  }
  await this.client.SendBatchAsync(messages);
}
```

<span data-ttu-id="4c2fb-189">下列程式碼示範取用者服務執行個體如何採用事件驅動方法，以從佇列接收訊息。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-189">The following code shows how a consumer service instance can receive messages from the queue by following an event-driven approach.</span></span> <span data-ttu-id="4c2fb-190">`ReceiveMessages` 方法的 `processMessageTask` 參數是一種代理參數，可參照收到訊息時要執行的程式碼。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-190">The `processMessageTask` parameter to the `ReceiveMessages` method is a delegate that references the code to run when a message is received.</span></span> <span data-ttu-id="4c2fb-191">此程式碼會以非同步方式執行。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-191">This code is run asynchronously.</span></span>

```csharp
private ManualResetEvent pauseProcessingEvent;
...

public void ReceiveMessages(Func<BrokeredMessage, Task> processMessageTask)
{
  // Set up the options for the message pump.
  var options = new OnMessageOptions();

  // When AutoComplete is disabled it's necessary to manually
  // complete or abandon the messages and handle any errors.
  options.AutoComplete = false;
  options.MaxConcurrentCalls = 10;
  options.ExceptionReceived += this.OptionsOnExceptionReceived;

  // Use of the Service Bus OnMessage message pump.
  // The OnMessage method must be called once, otherwise an exception will occur.
  this.client.OnMessageAsync(
    async (msg) =>
    {
      // Will block the current thread if Stop is called.
      this.pauseProcessingEvent.WaitOne();

      // Execute processing task here.
      await processMessageTask(msg);
    },
    options);
}
...

private void OptionsOnExceptionReceived(object sender,
  ExceptionReceivedEventArgs exceptionReceivedEventArgs)
{
  ...
}
```

<span data-ttu-id="4c2fb-192">請注意，您可以使用類似 Azure 中提供的自動調整功能，以隨著佇列長度變動來啟動和停止角色執行個體。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-192">Note that autoscaling features, such as those available in Azure, can be used to start and stop role instances as the queue length fluctuates.</span></span> <span data-ttu-id="4c2fb-193">如需詳細資訊，請參閱[自動調整指導方針](https://msdn.microsoft.com/library/dn589774.aspx) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-193">For more information, see [Autoscaling Guidance](https://msdn.microsoft.com/library/dn589774.aspx).</span></span> <span data-ttu-id="4c2fb-194">此外，不需要維護角色執行個體與背景工作處理序之間的一對一對應，因為單一角色執行個體可以實作多個背景工作處理序。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-194">Also, it's not necessary to maintain a one-to-one correspondence between role instances and worker processes&mdash;a single role instance can implement multiple worker processes.</span></span> <span data-ttu-id="4c2fb-195">如需詳細資訊，請參閱[計算資源彙總模式](./compute-resource-consolidation.md)。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-195">For more information, see [Compute Resource Consolidation pattern](./compute-resource-consolidation.md).</span></span>

## <a name="related-patterns-and-guidance"></a><span data-ttu-id="4c2fb-196">相關的模式和指導方針</span><span class="sxs-lookup"><span data-stu-id="4c2fb-196">Related patterns and guidance</span></span>

<span data-ttu-id="4c2fb-197">下列是實作此模式時可能相關的模式和指導方針：</span><span class="sxs-lookup"><span data-stu-id="4c2fb-197">The following patterns and guidance might be relevant when implementing this pattern:</span></span>

- <span data-ttu-id="4c2fb-198">[非同步傳訊入門](https://msdn.microsoft.com/library/dn589781.aspx)。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-198">[Asynchronous Messaging Primer](https://msdn.microsoft.com/library/dn589781.aspx).</span></span> <span data-ttu-id="4c2fb-199">訊息佇列是非同步通訊機制。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-199">Message queues are an asynchronous communications mechanism.</span></span> <span data-ttu-id="4c2fb-200">如果取用者服務需要傳送回覆至應用程式，可能必須實作某種形式的回應訊息。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-200">If a consumer service needs to send a reply to an application, it might be necessary to implement some form of response messaging.</span></span> <span data-ttu-id="4c2fb-201">「非同步傳訊入門」提供如何使用訊息佇列實作要求/回覆訊息的資訊。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-201">The Asynchronous Messaging Primer provides information on how to implement request/reply messaging using message queues.</span></span>

- <span data-ttu-id="4c2fb-202">[自動調整指導方針](https://msdn.microsoft.com/library/dn589774.aspx)。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-202">[Autoscaling Guidance](https://msdn.microsoft.com/library/dn589774.aspx).</span></span> <span data-ttu-id="4c2fb-203">由於佇列應用程式張貼訊息的長度不盡相同，因此可以啟動和停止取用者服務的執行個體。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-203">It might be possible to start and stop instances of a consumer service since the length of the queue applications post messages on varies.</span></span> <span data-ttu-id="4c2fb-204">自動調整有助於維護尖峰處理時間的輸送量。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-204">Autoscaling can help to maintain throughput during times of peak processing.</span></span>

- <span data-ttu-id="4c2fb-205">[計算資源彙總模式](./compute-resource-consolidation.md)。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-205">[Compute Resource Consolidation pattern](./compute-resource-consolidation.md).</span></span> <span data-ttu-id="4c2fb-206">將多個取用者服務的執行個體合併成單一處理序，可降低成本和管理額外負荷。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-206">It might be possible to consolidate multiple instances of a consumer service into a single process to reduce costs and management overhead.</span></span> <span data-ttu-id="4c2fb-207">「計算資源彙總」模式描述採用此方法的優缺點。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-207">The Compute Resource Consolidation pattern describes the benefits and tradeoffs of following this approach.</span></span>

- <span data-ttu-id="4c2fb-208">[佇列型負載調節模式](./queue-based-load-leveling.md)。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-208">[Queue-based Load Leveling pattern](./queue-based-load-leveling.md).</span></span> <span data-ttu-id="4c2fb-209">導入訊息佇列可為系統加入復原功能，使服務執行個體可應付應用程式執行個體要求量的大量變化。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-209">Introducing a message queue can add resiliency to the system, enabling service instances to handle widely varying volumes of requests from application instances.</span></span> <span data-ttu-id="4c2fb-210">訊息佇列是作為調節負載的緩衝區。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-210">The message queue acts as a buffer, which levels the load.</span></span> <span data-ttu-id="4c2fb-211">「佇列型負載調節模式」對此案例有更詳細的描述。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-211">The Queue-based Load Leveling pattern describes this scenario in more detail.</span></span>

- <span data-ttu-id="4c2fb-212">這個模式有相關連的[應用程式範例](https://github.com/mspnp/cloud-design-patterns/tree/master/competing-consumers) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="4c2fb-212">This pattern has a [sample application](https://github.com/mspnp/cloud-design-patterns/tree/master/competing-consumers) associated with it.</span></span>

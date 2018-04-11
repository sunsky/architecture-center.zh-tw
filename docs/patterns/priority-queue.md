---
title: 優先順序佇列
description: 針對傳送給服務的要求排列優先順序，讓高優先順序要求的接收和處理順序在低優先順序要求之前。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories:
- messaging
- performance-scalability
ms.openlocfilehash: ecfbb38304bb95587e9ca15523ad9594898d9b32
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="priority-queue-pattern"></a><span data-ttu-id="a1e7e-104">優先順序佇列模式</span><span class="sxs-lookup"><span data-stu-id="a1e7e-104">Priority Queue pattern</span></span>

[!INCLUDE [header](../_includes/header.md)]

<span data-ttu-id="a1e7e-105">針對傳送給服務的要求排列優先順序，讓高優先順序要求的接收和處理順序在低優先順序要求之前。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-105">Prioritize requests sent to services so that requests with a higher priority are received and processed more quickly than those with a lower priority.</span></span> <span data-ttu-id="a1e7e-106">在針對個別用戶端提供不同服務等級保證的應用程式中，此模式相當有用。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-106">This pattern is useful in applications that offer different service level guarantees to individual clients.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="a1e7e-107">內容和問題</span><span class="sxs-lookup"><span data-stu-id="a1e7e-107">Context and Problem</span></span>

<span data-ttu-id="a1e7e-108">應用程式可以將特定工作委派給其他服務，例如用以執行背景處理，或是與其他應用程式或服務整合。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-108">Applications can delegate specific tasks to other services, for example, to perform background processing or to integrate with other applications or services.</span></span> <span data-ttu-id="a1e7e-109">在雲端，訊息佇列通常是用來將工作委派給背景處理。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-109">In the cloud, a message queue is typically used to delegate tasks to background processing.</span></span> <span data-ttu-id="a1e7e-110">在許多情況下，服務接收要求的順序並不重要。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-110">In many cases the order requests are received in by a service isn't important.</span></span> <span data-ttu-id="a1e7e-111">不過，在某些情況下，則有必要針對特定要求排列優先順序。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-111">In some cases, though, it's necessary to prioritize specific requests.</span></span> <span data-ttu-id="a1e7e-112">這些要求的處理順序應該要在應用程式先前傳送的低優先順序要求之前。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-112">These requests should be processed earlier than lower priority requests that were sent previously by the application.</span></span>

## <a name="solution"></a><span data-ttu-id="a1e7e-113">方案</span><span class="sxs-lookup"><span data-stu-id="a1e7e-113">Solution</span></span>

<span data-ttu-id="a1e7e-114">佇列通常採用先進先出 (FIFO) 結構，而取用者接收訊息的順序通常與訊息被張貼至佇列的順序相同。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-114">A queue is usually a first-in, first-out (FIFO) structure, and consumers typically receive messages in the same order that they were posted to the queue.</span></span> <span data-ttu-id="a1e7e-115">不過，有些訊息佇列可支援優先順序傳訊。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-115">However, some message queues support priority messaging.</span></span> <span data-ttu-id="a1e7e-116">張貼訊息的應用程式可以指派優先順序，而佇列中的訊息將會自動重新排列順序，讓高優先順序訊息的接收順序在低優先順序訊息之前。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-116">The application posting a message can assign a priority and the messages in the queue are automatically reordered so that those with a higher priority will be received before those with a lower priority.</span></span> <span data-ttu-id="a1e7e-117">下圖說明使用優先順序傳訊的佇列。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-117">The figure illustrates a queue with priority messaging.</span></span>

![圖 1 - 使用支援訊息優先順序的佇列機制](./_images/priority-queue-pattern.png)

> <span data-ttu-id="a1e7e-119">大多數訊息佇列實作都支援多個取用者 (依循[競爭取用者模式](https://msdn.microsoft.com/library/dn568101.aspx) \(英文\))，並且可以依需求增加或減少取用者處理序數目。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-119">Most message queue implementations support multiple consumers (following the [Competing Consumers pattern](https://msdn.microsoft.com/library/dn568101.aspx)), and the number of consumer processes can be scaled up or down depending on demand.</span></span>

<span data-ttu-id="a1e7e-120">在不支援優先順序型佇列的系統中，替代的解決方案是針對每個優先順序維護一個個別的佇列。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-120">In systems that don't support priority-based message queues, an alternative solution is to maintain a separate queue for each priority.</span></span> <span data-ttu-id="a1e7e-121">應用程式需負責將訊息張貼至適當的佇列。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-121">The application is responsible for posting messages to the appropriate queue.</span></span> <span data-ttu-id="a1e7e-122">每個佇列可以有一個個別的取用者集區。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-122">Each queue can have a separate pool of consumers.</span></span> <span data-ttu-id="a1e7e-123">高優先順序佇列的取用者集區，可以比低優先順序佇列的執行於更快速的硬體上，且可以有較大的集區。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-123">Higher priority queues can have a larger pool of consumers running on faster hardware than lower priority queues.</span></span> <span data-ttu-id="a1e7e-124">下一張圖說明針對每個優先順序使用個別的訊息佇列。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-124">The next figure illustrates using separate message queues for each priority.</span></span>

![圖 2 - 針對每個優先順序使用個別的訊息佇列](./_images/priority-queue-separate.png)


<span data-ttu-id="a1e7e-126">此策略的一種變換方式是使用單一取用者集區，並讓這些取用者先檢查高優先順序佇列上的訊息，然後才開始從較低優先順序的佇列擷取訊息。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-126">A variation on this strategy is to have a single pool of consumers that check for messages on high priority queues first, and only then start to fetch messages from lower priority queues.</span></span> <span data-ttu-id="a1e7e-127">使用單一取用者處理序集區 (不論是搭配支援具有不同優先順序之訊息的單一佇列，還是搭配各處理單一優先順序之訊息的多個佇列) 的解決方案與使用多個佇列且每個佇列具有個別集區的解決方案之間，有一些語意差異。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-127">There are some semantic differences between a solution that uses a single pool of consumer processes (either with a single queue that supports messages with different priorities or with multiple queues that each handle messages of a single priority), and a solution that uses multiple queues with a separate pool for each queue.</span></span>

<span data-ttu-id="a1e7e-128">在單一集區方法中，高優先順序訊息的接收和處理順序一律是在低優先順序訊息之前。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-128">In the single pool approach, higher priority messages are always received and processed before lower priority messages.</span></span> <span data-ttu-id="a1e7e-129">理論上，優先順序非常低的訊息有可能不斷地被取代而永遠不被處理。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-129">In theory, messages that have a very low priority could be continually superseded and might never be processed.</span></span> <span data-ttu-id="a1e7e-130">在多集區方法中，一律會處理較低優先順序的訊息，只是處理順序會在較高優先順序的訊息之後 (視集區的相對大小及其可用的資源而定)。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-130">In the multiple pool approach, lower priority messages will always be processed, just not as quickly as those of a higher priority (depending on the relative size of the pools and the resources that they have available).</span></span>

<span data-ttu-id="a1e7e-131">使用優先順序佇列機制可提供下列優點：</span><span class="sxs-lookup"><span data-stu-id="a1e7e-131">Using a priority queuing mechanism can provide the following advantages:</span></span>

- <span data-ttu-id="a1e7e-132">它可讓應用程式符合要求針對可用性或效能排列優先順序的業務需求，例如針對特定的客戶群組提供不同等級的服務。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-132">It allows applications to meet business requirements that require prioritization of availability or performance, such as offering different levels of service to specific groups of customers.</span></span>

- <span data-ttu-id="a1e7e-133">它可協助將營運成本降到最低。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-133">It can help to minimize operational costs.</span></span> <span data-ttu-id="a1e7e-134">在單一佇列方法中，您可以視需要縮減取用者數目。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-134">In the single queue approach, you can scale back the number of consumers if necessary.</span></span> <span data-ttu-id="a1e7e-135">系統仍然會先處理高優先順序訊息 (雖然處理速度可能較慢)，而低優先順序訊息可能會被延遲更久。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-135">High priority messages will still be processed first (although possibly more slowly), and lower priority messages might be delayed for longer.</span></span> <span data-ttu-id="a1e7e-136">如果您已實作每個佇列搭配個別取用者集區的多訊息佇列方法，您可以將較低優先順序佇列的取用者集區縮小，或甚至是將接聽優先順序非常低之佇列上訊息的所有取用者停止，來暫停處理這些佇列。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-136">If you've implemented the multiple message queue approach with separate pools of consumers for each queue, you can reduce the pool of consumers for lower priority queues, or even suspend processing for some very low priority queues by stopping all the consumers that listen for messages on those queues.</span></span>

- <span data-ttu-id="a1e7e-137">多訊息佇列方法可根據處理需求來分割訊息，協助將應用程式的效能和延展性發揮到極致。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-137">The multiple message queue approach can help maximize application performance and scalability by partitioning messages based on processing requirements.</span></span> <span data-ttu-id="a1e7e-138">例如，可以將重要的工作優先交由立即執行的接收者處理，而將較不重要的背景工作交由排定在較不忙碌時段執行的接收者處理。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-138">For example, vital tasks can be prioritized to be handled by receivers that run immediately while less important background tasks can be handled by receivers that are scheduled to run at less busy periods.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="a1e7e-139">問題和考量</span><span class="sxs-lookup"><span data-stu-id="a1e7e-139">Issues and Considerations</span></span>

<span data-ttu-id="a1e7e-140">當您決定如何實作此模式時，請考慮下列幾點：</span><span class="sxs-lookup"><span data-stu-id="a1e7e-140">Consider the following points when deciding how to implement this pattern:</span></span>

<span data-ttu-id="a1e7e-141">在解決方案的內容中定義優先順序。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-141">Define the priorities in the context of the solution.</span></span> <span data-ttu-id="a1e7e-142">例如，高優先順序可能意謂著應該在十秒內處理訊息。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-142">For example, high priority could mean that messages should be processed within ten seconds.</span></span> <span data-ttu-id="a1e7e-143">識別處理高優先順序項目的需求，以及其他應該配置以符合這些準則的資源。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-143">Identify the requirements for handling high priority items, and the other resources that should be allocated to meet these criteria.</span></span>

<span data-ttu-id="a1e7e-144">決定是否所有高優先順序項目的處理順序都必須在低優先順序項目之前。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-144">Decide if all high priority items must be processed before any lower priority items.</span></span> <span data-ttu-id="a1e7e-145">如果是由單一取用者集區處理訊息，您就必須提供一個機制，它在高優先順序訊息變成可處理時，能先佔並暫停處理低優先順序訊息的工作。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-145">If the messages are being processed by a single pool of consumers, you have to provide a mechanism that can preempt and suspend a task that's handling a low priority message if a higher priority message becomes available.</span></span>

<span data-ttu-id="a1e7e-146">在多佇列方法中，當使用單一取用者處理序集區來接聽所有佇列，而不是每個佇列使用一個專用取用者集區時，取用者必須套用演算法來確保一律會先為來自高優先順序佇列的訊息提供服務，然後才為來自低優先順序佇列的訊息提供服務。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-146">In the multiple queue approach, when using a single pool of consumer processes that listen on all queues rather than a dedicated consumer pool for each queue, the consumer must apply an algorithm that ensures it always services messages from higher priority queues before those from lower priority queues.</span></span>

<span data-ttu-id="a1e7e-147">監視高低優先順序佇列上的處理速度，以確保依預期的速率處理這些佇列中的訊息。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-147">Monitor the processing speed on high and low priority queues to ensure that messages in these queues are processed at the expected rates.</span></span>

<span data-ttu-id="a1e7e-148">如果您需要保證處理低優先順序訊息，就必須實作搭配多個取用者集區的多訊息佇列方法。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-148">If you need to guarantee that low priority messages will be processed, it's necessary to implement the multiple message queue approach with multiple pools of consumers.</span></span> <span data-ttu-id="a1e7e-149">或者，在支援訊息優先順序的佇列中，也可以隨著所佇列的訊息老化而動態地提高其優先順序。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-149">Alternatively, in a queue that supports message prioritization, it's possible to dynamically increase the priority of a queued message as it ages.</span></span> <span data-ttu-id="a1e7e-150">不過，此方法有賴於訊息佇列提供這項功能。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-150">However, this approach depends on the message queue providing this feature.</span></span>

<span data-ttu-id="a1e7e-151">對於只有少量妥善定義之優先順序的系統來說，針對每個訊息優先順序使用個別佇列的效果最佳。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-151">Using a separate queue for each message priority works best for systems that have a small number of well-defined priorities.</span></span>

<span data-ttu-id="a1e7e-152">訊息優先順序可以由系統以邏輯方式決定。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-152">Message priorities can be determined logically by the system.</span></span> <span data-ttu-id="a1e7e-153">例如，可以不使用明確的高低優先順序訊息，而是指定為「付費客戶」或「非付費客戶」。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-153">For example, rather than having explicit high and low priority messages, they could be designated as “fee paying customer,” or “non-fee paying customer.”</span></span> <span data-ttu-id="a1e7e-154">視您的業務模式而定，您的系統可以配置比非付費客戶更多的資源來處理來自付費客戶的訊息。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-154">Depending on your business model, your system can allocate more resources to processing messages from fee paying customers than non-fee paying ones.</span></span>

<span data-ttu-id="a1e7e-155">可能會有與檢查佇列訊息關聯的財務和處理成本　(有些商業傳訊系統會在每次張貼或接收訊息以及每次查詢佇列訊息時，收取一小筆費用)。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-155">There might be a financial and processing cost associated with checking a queue for a message (some commercial messaging systems charge a small fee each time a message is posted or retrieved, and each time a queue is queried for messages).</span></span> <span data-ttu-id="a1e7e-156">當檢查多個佇列時，此成本會增加。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-156">This cost increases when checking multiple queues.</span></span>

<span data-ttu-id="a1e7e-157">您可以根據取用者集區所服務的佇列長度，動態地調整該集區的大小。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-157">It's possible to dynamically adjust the size of a pool of consumers based on the length of the queue that the pool is servicing.</span></span> <span data-ttu-id="a1e7e-158">如需詳細資訊，請參閱[自動調整指導方針](https://msdn.microsoft.com/library/dn589774.aspx) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-158">For more information, see the [Autoscaling Guidance](https://msdn.microsoft.com/library/dn589774.aspx).</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="a1e7e-159">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="a1e7e-159">When to use this pattern</span></span>

<span data-ttu-id="a1e7e-160">此模式在具有下列情況的案例中相當有用：</span><span class="sxs-lookup"><span data-stu-id="a1e7e-160">This pattern is useful in scenarios where:</span></span>

- <span data-ttu-id="a1e7e-161">系統必須處理具有不同優先順序的多個工作。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-161">The system must handle multiple tasks that have different priorities.</span></span>

- <span data-ttu-id="a1e7e-162">應該以不同的優先順序服務不同的使用者或租用戶。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-162">Different users or tenants should be served with different priority.</span></span>

## <a name="example"></a><span data-ttu-id="a1e7e-163">範例</span><span class="sxs-lookup"><span data-stu-id="a1e7e-163">Example</span></span>

<span data-ttu-id="a1e7e-164">Microsoft Azure 並未提供原生地支援透過排序即可自動排列訊息優先順序的佇列機制。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-164">Microsoft Azure doesn't provide a queuing mechanism that natively supports automatic prioritization of messages through sorting.</span></span> <span data-ttu-id="a1e7e-165">不過，有提供支援佇列機制的「Azure 服務匯流排」主題和訂用帳戶，此機制除了提供訊息篩選功能之外，還提供廣泛的彈性功能，使得它適用於大多數優先順序佇列實作。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-165">However, it does provide Azure Service Bus topics and subscriptions that support a queuing mechanism that provides message filtering, together with a wide range of flexible capabilities that make it ideal for use in most priority queue implementations.</span></span>

<span data-ttu-id="a1e7e-166">Azure 解決方案可以用實作佇列的相同方式，實作可供應用程式張貼訊息的「服務匯流排」主題。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-166">An Azure solution can implement a Service Bus topic an application can post messages to, in the same way as a queue.</span></span> <span data-ttu-id="a1e7e-167">訊息可以包含以應用程式所定義之自訂屬性形式呈現的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-167">Messages can contain metadata in the form of application-defined custom properties.</span></span> <span data-ttu-id="a1e7e-168">「服務匯流排」訂用帳戶可以與該主題建立關聯，然後這些訂用帳戶便可根據訊息的屬性來篩選訊息。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-168">Service Bus subscriptions can be associated with the topic, and these subscriptions can filter messages based on their properties.</span></span> <span data-ttu-id="a1e7e-169">當應用程式將訊息傳送給主題時，系統會將訊息導向到可供取用者讀取的適當訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-169">When an application sends a message to a topic, the message is directed to the appropriate subscription where it can be read by a consumer.</span></span> <span data-ttu-id="a1e7e-170">取用者處理序可以使用與訊息佇列 (訂用帳戶是邏輯佇列) 相同的語意從訂用帳戶擷取訊息。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-170">Consumer processes can retrieve messages from a subscription using the same semantics as a message queue (a subscription is a logical queue).</span></span> <span data-ttu-id="a1e7e-171">下圖說明如何使用「Azure 服務匯流排」主題和訂用帳戶來實作優先順序佇列。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-171">The following figure illustrates implementing a priority queue with Azure Service Bus topics and subscriptions.</span></span>

![圖 3 - 使用 Azure 服務匯流排主題和訂用帳戶來實作優先順序佇列](./_images/priority-queue-service-bus.png)


<span data-ttu-id="a1e7e-173">在上圖中，應用程式建立數個訊息，並在每個訊息中指派一個名為 `Priority` 且值為 `High` 或 `Low` 的自訂屬性。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-173">In the figure above, the application creates several messages and assigns a custom property called `Priority` in each message with a value, either `High` or `Low`.</span></span> <span data-ttu-id="a1e7e-174">應用程式會將這些訊息張貼至主題。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-174">The application posts these messages to a topic.</span></span> <span data-ttu-id="a1e7e-175">此主題有兩個關聯的訂用帳戶，這兩個訂用帳戶都會檢查 `Priority` 屬性來篩選訊息。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-175">The topic has two associated subscriptions that both filter messages by examining the `Priority` property.</span></span> <span data-ttu-id="a1e7e-176">其中一個訂用帳戶會接受 `Priority` 屬性設定為 `High` 的訊息，另一個訂用帳戶則會接受 `Priority` 屬性設定為 `Low` 的訊息。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-176">One subscription accepts messages where the `Priority` property is set to `High`, and the other accepts messages where the `Priority` property is set to `Low`.</span></span> <span data-ttu-id="a1e7e-177">取用者集區會從每個訂用帳戶讀取訊息。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-177">A pool of consumers reads messages from each subscription.</span></span> <span data-ttu-id="a1e7e-178">高優先順序訂用帳戶具有較大的集區，而這些取用者相較於低優先順序集區中的取用者，可能執行於更強大的電腦且有更多可用資源。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-178">The high priority subscription has a larger pool, and these consumers might be running on more powerful computers with more resources available than the consumers in the low priority pool.</span></span>

<span data-ttu-id="a1e7e-179">請注意，在此範例中，關於指定高低優先順序訊息方面並無任何特別之處。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-179">Note that there's nothing special about the designation of high and low priority messages in this example.</span></span> <span data-ttu-id="a1e7e-180">它們只是標籤，在每個訊息中是以屬性的形式指定的，並且用來將訊息導向到特定的訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-180">They're simply labels specified as properties in each message, and are used to direct messages to a specific subscription.</span></span> <span data-ttu-id="a1e7e-181">如果需要其他屬性，相當容易即可建立進一步的訂用帳戶和取用者處理序集區來處理這些優先順序。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-181">If additional priorities are required, it's relatively easy to create further subscriptions and pools of consumer processes to handle these priorities.</span></span>

<span data-ttu-id="a1e7e-182">[GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/priority-queue) \(英文\) 上提供的 PriorityQueue 解決方案即包含此方法的實作。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-182">The PriorityQueue solution available on [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/priority-queue) contains an implementation of this approach.</span></span> <span data-ttu-id="a1e7e-183">此解決方案包含名為 `PriorityQueue.High` 和 `PriorityQueue.Low` 的兩個背景工作角色專案。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-183">This solution contains two worker role projects named `PriorityQueue.High` and `PriorityQueue.Low`.</span></span> <span data-ttu-id="a1e7e-184">這些背景工作角色會從 `PriorityWorkerRole` 類別繼承，此類別包含可連線到 `OnStart` 方法中所指定訂用帳戶的功能。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-184">These worker roles inherit from the `PriorityWorkerRole` class that contains the functionality for connecting to a specified subscription in the `OnStart` method.</span></span>

<span data-ttu-id="a1e7e-185">`PriorityQueue.High` 和 `PriorityQueue.Low` 背景工作角色會連線到其組態設定所定義的不同訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-185">The `PriorityQueue.High` and `PriorityQueue.Low` worker roles connect to different subscriptions, defined by their configuration settings.</span></span> <span data-ttu-id="a1e7e-186">系統管理員可以為每個要執行的角色設定不同的數目。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-186">An administrator can configure different numbers of each role to be run.</span></span> <span data-ttu-id="a1e7e-187">通常 `PriorityQueue.High` 背景工作角色的執行個體會比 `PriorityQueue.Low` 背景工作角色的多。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-187">Typically there'll be more instances of the `PriorityQueue.High` worker role than the `PriorityQueue.Low` worker role.</span></span>

<span data-ttu-id="a1e7e-188">`PriorityWorkerRole` 類別中的 `Run` 方法會安排針對佇列上收到的每個訊息執行虛擬 `ProcessMessage` 方法 (也定義於 `PriorityWorkerRole` 類別中)。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-188">The `Run` method in the `PriorityWorkerRole` class arranges for the virtual `ProcessMessage` method (also defined in the `PriorityWorkerRole` class) to be run for each message received on the queue.</span></span> <span data-ttu-id="a1e7e-189">下列程式碼顯示 `Run` 和 `ProcessMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-189">The following code shows the `Run` and `ProcessMessage` methods.</span></span> <span data-ttu-id="a1e7e-190">PriorityQueue.Shared 專案中定義的 `QueueManager` 類別會提供使用「Azure 服務匯流排」的協助程式方法。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-190">The `QueueManager` class, defined in the PriorityQueue.Shared project, provides helper methods for using Azure Service Bus queues.</span></span>

```csharp
public class PriorityWorkerRole : RoleEntryPoint
{
  private QueueManager queueManager;
  ...

  public override void Run()
  {
    // Start listening for messages on the subscription.
    var subscriptionName = CloudConfigurationManager.GetSetting("SubscriptionName");
    this.queueManager.ReceiveMessages(subscriptionName, this.ProcessMessage);
    ...;
  }
  ...

  protected virtual async Task ProcessMessage(BrokeredMessage message)
  {
    // Simulating processing.
    await Task.Delay(TimeSpan.FromSeconds(2));
  }
}
```
<span data-ttu-id="a1e7e-191">`PriorityQueue.High` 和 `PriorityQueue.Low` 背景工作角色都會覆寫 `ProcessMessage` 方法的預設功能。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-191">The `PriorityQueue.High` and `PriorityQueue.Low` worker roles both override the default functionality of the `ProcessMessage` method.</span></span> <span data-ttu-id="a1e7e-192">下方程式碼顯示 `PriorityQueue.High` 背景工作角色的 `ProcessMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-192">The code below shows the `ProcessMessage` method for the `PriorityQueue.High` worker role.</span></span>

```csharp
protected override async Task ProcessMessage(BrokeredMessage message)
{
  // Simulate message processing for High priority messages.
  await base.ProcessMessage(message);
  Trace.TraceInformation("High priority message processed by " +
    RoleEnvironment.CurrentRoleInstance.Id + " MessageId: " + message.MessageId);
}
```

<span data-ttu-id="a1e7e-193">當應用程式將訊息張貼至與 `PriorityQueue.High` 和 `PriorityQueue.Low` 背景工作角色所用訂用帳戶關聯的主題時，它會使用 `Priority` 自訂屬性來指定優先順序，如下列程式碼範例所示。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-193">When an application posts messages to the topic associated with the subscriptions used by the `PriorityQueue.High` and `PriorityQueue.Low` worker roles, it specifies the priority by using the `Priority` custom property, as shown in the following code example.</span></span> <span data-ttu-id="a1e7e-194">此程式碼 (在 PriorityQueue.Sender 專案的 `WorkerRole` 類別中實作) 會使用 `QueueManager` 類別的 `SendBatchAsync` 協助程式方法將訊息批次張貼至主題。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-194">This code (implemented in the `WorkerRole` class in the PriorityQueue.Sender project), uses the `SendBatchAsync` helper method of the `QueueManager` class to post messages to a topic in batches.</span></span>

```csharp
// Send a low priority batch.
var lowMessages = new List<BrokeredMessage>();

for (int i = 0; i < 10; i++)
{
  var message = new BrokeredMessage() { MessageId = Guid.NewGuid().ToString() };
  message.Properties["Priority"] = Priority.Low;
  lowMessages.Add(message);
}

this.queueManager.SendBatchAsync(lowMessages).Wait();
...

// Send a high priority batch.
var highMessages = new List<BrokeredMessage>();

for (int i = 0; i < 10; i++)
{
  var message = new BrokeredMessage() { MessageId = Guid.NewGuid().ToString() };
  message.Properties["Priority"] = Priority.High;
  highMessages.Add(message);
}

this.queueManager.SendBatchAsync(highMessages).Wait();
```

## <a name="related-patterns-and-guidance"></a><span data-ttu-id="a1e7e-195">相關的模式和指導方針</span><span class="sxs-lookup"><span data-stu-id="a1e7e-195">Related patterns and guidance</span></span>

<span data-ttu-id="a1e7e-196">實作此模式時，下列模式和指導方針可能也相關：</span><span class="sxs-lookup"><span data-stu-id="a1e7e-196">The following patterns and guidance might also be relevant when implementing this pattern:</span></span>

- <span data-ttu-id="a1e7e-197">[GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/priority-queue) \(英文\) 上有提供示範此模式的範例。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-197">A sample that demonstrates this pattern is available on [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/priority-queue).</span></span>

- <span data-ttu-id="a1e7e-198">[非同步傳訊入門](https://msdn.microsoft.com/library/dn589781.aspx) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-198">[Asynchronous Messaging Primer](https://msdn.microsoft.com/library/dn589781.aspx).</span></span> <span data-ttu-id="a1e7e-199">處理要求的取用者服務可能需要傳送回覆給張貼要求的應用程式執行個體。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-199">A consumer service that processes a request might need to send a reply to the instance of the application that posted the request.</span></span> <span data-ttu-id="a1e7e-200">提供有關您可用來實作要求/回應傳訊之策略的資訊。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-200">Provides information on the strategies that you can use to implement request/response messaging.</span></span>

- <span data-ttu-id="a1e7e-201">[競爭取用者模式](competing-consumers.md)。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-201">[Competing Consumers pattern](competing-consumers.md).</span></span> <span data-ttu-id="a1e7e-202">若要增加佇列的輸送量，您可以讓多個取用者接聽相同的佇列，然後平行處理工作。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-202">To increase the throughput of the queues, it’s possible to have multiple consumers that listen on the same queue, and process the tasks in parallel.</span></span> <span data-ttu-id="a1e7e-203">這些取用者將會競用訊息，但每個訊息應只能由一個取用者處理。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-203">These consumers will compete for messages, but only one should be able to process each message.</span></span> <span data-ttu-id="a1e7e-204">提供有關實作此方法之優點和權衡取捨的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-204">Provides more information on the benefits and tradeoffs of implementing this approach.</span></span>

- <span data-ttu-id="a1e7e-205">[節流模式](throttling.md)。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-205">[Throttling pattern](throttling.md).</span></span> <span data-ttu-id="a1e7e-206">您可以使用佇列來實作節流。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-206">You can implement throttling by using queues.</span></span> <span data-ttu-id="a1e7e-207">優先順序傳訊可用來確保為來自重要應用程式的要求或由高價值客戶執行的應用程式，賦予比來自較不重要應用程式的要求更高的優先順序。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-207">Priority messaging can be used to ensure that requests from critical applications, or applications being run by high-value customers, are given priority over requests from less important applications.</span></span>

- <span data-ttu-id="a1e7e-208">[自動調整指導方針](https://msdn.microsoft.com/library/dn589774.aspx) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-208">[Autoscaling Guidance](https://msdn.microsoft.com/library/dn589774.aspx).</span></span> <span data-ttu-id="a1e7e-209">您可以根據佇列的長度，調整處理佇列之取用者處理序集區的大小。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-209">It might be possible to scale the size of the pool of consumer processes handling a queue depending on the length of the queue.</span></span> <span data-ttu-id="a1e7e-210">此策略有助於改善效能，特別是針對處理高優先順序訊息的集區。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-210">This strategy can help to improve performance, especially for pools handling high priority messages.</span></span>

- <span data-ttu-id="a1e7e-211">Abhishek Lal 部落格上的[搭配服務匯流排的企業整合模式](http://abhishekrlal.com/2013/01/11/enterprise-integration-patterns-with-service-bus-part-2/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="a1e7e-211">[Enterprise Integration Patterns with Service Bus](http://abhishekrlal.com/2013/01/11/enterprise-integration-patterns-with-service-bus-part-2/) on Abhishek Lal’s blog.</span></span>


---
title: "事件驅動架構樣式"
description: "說明 Azure 上事件驅動架構和 IoT 架構的優點、挑戰和最佳做法"
author: MikeWasson
ms.openlocfilehash: 3289bf784b02d62e3d0c1a29b4839c9be3501134
ms.sourcegitcommit: 3d9ee03e2dda23753661a80c7106d1789f5223bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2018
---
# <a name="event-driven-architecture-style"></a><span data-ttu-id="c09e9-103">事件驅動架構樣式</span><span class="sxs-lookup"><span data-stu-id="c09e9-103">Event-driven architecture style</span></span>

<span data-ttu-id="c09e9-104">事件驅動架構包含產生事件串流的**事件產生者**，以及接聽事件的**事件取用者**。</span><span class="sxs-lookup"><span data-stu-id="c09e9-104">An event-driven architecture consists of **event producers** that generate a stream of events, and **event consumers** that listen for the events.</span></span> 

![](./images/event-driven.svg)

<span data-ttu-id="c09e9-105">事件會以幾近即時的方式傳遞，讓取用者可以在事件發生時立即做出回應。</span><span class="sxs-lookup"><span data-stu-id="c09e9-105">Events are delivered in near real time, so consumers can respond immediately to events as they occur.</span></span> <span data-ttu-id="c09e9-106">產生者會與取用者分離，也就是說，產生者並不會知道是哪一個取用者在接聽。</span><span class="sxs-lookup"><span data-stu-id="c09e9-106">Producers are decoupled from consumers &mdash; a producer doesn't know which consumers are listening.</span></span> <span data-ttu-id="c09e9-107">取用者也會彼此分離，而且每個取用者都會看到所有事件。</span><span class="sxs-lookup"><span data-stu-id="c09e9-107">Consumers are also decoupled from each other, and every consumer sees all of the events.</span></span> <span data-ttu-id="c09e9-108">這與[競爭取用者][competing-consumers]模式不同，競爭取用者模式的取用者會從佇列提取訊息，而且一個訊息只會處理一次 (在沒有錯誤的前提下)。</span><span class="sxs-lookup"><span data-stu-id="c09e9-108">This differs from a [Competing Consumers][competing-consumers] pattern, where consumers pull messages from a queue and a message is processed just once (assuming no errors).</span></span> <span data-ttu-id="c09e9-109">某些系統 (例如 IoT) 必須極為大量地擷取事件。</span><span class="sxs-lookup"><span data-stu-id="c09e9-109">In some systems, such as IoT, events must be ingested at very high volumes.</span></span>

<span data-ttu-id="c09e9-110">事件驅動架構可以使用發行/訂閱模型或事件串流模型。</span><span class="sxs-lookup"><span data-stu-id="c09e9-110">An event driven architecture can use a pub/sub model or an event stream model.</span></span> 

- <span data-ttu-id="c09e9-111">**發行/訂閱**：傳訊基礎結構會追蹤訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="c09e9-111">**Pub/sub**: The messaging infrastructure keeps track of subscriptions.</span></span> <span data-ttu-id="c09e9-112">有事件發行時，它就會將該事件傳送給每個訂閱者。</span><span class="sxs-lookup"><span data-stu-id="c09e9-112">When an event is published, it sends the event to each subscriber.</span></span> <span data-ttu-id="c09e9-113">事件經過接收後就無法重新執行，因此新的訂閱者不會看到該事件。</span><span class="sxs-lookup"><span data-stu-id="c09e9-113">After an event is received, it cannot be replayed, and new subscribers do not see the event.</span></span> 

- <span data-ttu-id="c09e9-114">**事件串流**：事件會寫入記錄中。</span><span class="sxs-lookup"><span data-stu-id="c09e9-114">**Event streaming**: Events are written to a log.</span></span> <span data-ttu-id="c09e9-115">事件有嚴格的排序 (在資料分割內) 並具有持久性。</span><span class="sxs-lookup"><span data-stu-id="c09e9-115">Events are strictly ordered (within a partition) and durable.</span></span> <span data-ttu-id="c09e9-116">用戶端不會訂閱串流，相反地，用戶端可以讀取串流的任何部分。</span><span class="sxs-lookup"><span data-stu-id="c09e9-116">Clients don't subscribe to the stream, instead a client can read from any part of the stream.</span></span> <span data-ttu-id="c09e9-117">用戶端負責讓其在串流中的位置前進。</span><span class="sxs-lookup"><span data-stu-id="c09e9-117">The client is responsible for advancing its position in the stream.</span></span> <span data-ttu-id="c09e9-118">這表示用戶端可以在任何時間加入，並可重新執行事件。</span><span class="sxs-lookup"><span data-stu-id="c09e9-118">That means a client can join at any time, and can replay events.</span></span>

<span data-ttu-id="c09e9-119">在取用者端，則有一些常見的變化：</span><span class="sxs-lookup"><span data-stu-id="c09e9-119">On the consumer side, there are some common variations:</span></span>

- <span data-ttu-id="c09e9-120">**簡單事件處理**。</span><span class="sxs-lookup"><span data-stu-id="c09e9-120">**Simple event processing**.</span></span> <span data-ttu-id="c09e9-121">事件會立即在取用者中觸發動作。</span><span class="sxs-lookup"><span data-stu-id="c09e9-121">An event immediately triggers an action in the consumer.</span></span> <span data-ttu-id="c09e9-122">例如，您可以使用 Azure Functions 並搭配服務匯流排觸發程序，以便在每當有訊息發佈至服務匯流排主題時執行函式。</span><span class="sxs-lookup"><span data-stu-id="c09e9-122">For example, you could use Azure Functions with a Service Bus trigger, so that a function executes whenever a message is published to a Service Bus topic.</span></span>

- <span data-ttu-id="c09e9-123">**複雜事件處理**。</span><span class="sxs-lookup"><span data-stu-id="c09e9-123">**Complex event processing**.</span></span> <span data-ttu-id="c09e9-124">取用者會處理一系列的事件，使用 Azure 串流分析或 Apache Storm 之類的技術尋找事件資料的模式。</span><span class="sxs-lookup"><span data-stu-id="c09e9-124">A consumer processes a series of events, looking for patterns in the event data, using a technology such as Azure Stream Analytics or Apache Storm.</span></span> <span data-ttu-id="c09e9-125">例如，您可以彙總內嵌裝置在一段時間內的數據，並在移動平均值超過特定閾值時產生通知。</span><span class="sxs-lookup"><span data-stu-id="c09e9-125">For example, you could aggregate readings from an embedded device over a time window, and generate a notification if the moving average crosses a certain threshold.</span></span> 

- <span data-ttu-id="c09e9-126">**事件串流處理**。</span><span class="sxs-lookup"><span data-stu-id="c09e9-126">**Event stream processing**.</span></span> <span data-ttu-id="c09e9-127">使用資料串流平台 (例如 Azure IoT 中樞或 Apache Kafka) 做為管線，以擷取事件並饋送至串流處理器。</span><span class="sxs-lookup"><span data-stu-id="c09e9-127">Use a data streaming platform, such as Azure IoT Hub or Apache Kafka, as a pipeline to ingest events and feed them to stream processors.</span></span> <span data-ttu-id="c09e9-128">串流處理器會採取行動，以處理或轉換串流。</span><span class="sxs-lookup"><span data-stu-id="c09e9-128">The stream processors act to process or transform the stream.</span></span> <span data-ttu-id="c09e9-129">不同的應用程式子系統可能會有多個串流處理器。</span><span class="sxs-lookup"><span data-stu-id="c09e9-129">There may be multiple stream processors for different subsystems of the application.</span></span> <span data-ttu-id="c09e9-130">這個方法很適合用於 IoT 工作負載。</span><span class="sxs-lookup"><span data-stu-id="c09e9-130">This approach is a good fit for IoT workloads.</span></span>

<span data-ttu-id="c09e9-131">事件的來源可能是系統外部，例如 IoT 解決方案中的實體裝置。</span><span class="sxs-lookup"><span data-stu-id="c09e9-131">The source of the events may be external to the system, such as physical devices in an IoT solution.</span></span> <span data-ttu-id="c09e9-132">在此情況下，系統必須能夠以資料來源所需要的數量和輸送量擷取資料。</span><span class="sxs-lookup"><span data-stu-id="c09e9-132">In that case, the system must be able to ingest the data at the volume and throughput that is required by the data source.</span></span>

<span data-ttu-id="c09e9-133">在上述邏輯圖中，每種類型的取用者各顯示為一個方塊。</span><span class="sxs-lookup"><span data-stu-id="c09e9-133">In the logical diagram above, each type of consumer is shown as a single box.</span></span> <span data-ttu-id="c09e9-134">實際上，取用者經常會有多個執行個體，以免取用者成為系統中的單一失敗點。</span><span class="sxs-lookup"><span data-stu-id="c09e9-134">In practice, it's common to have multiple instances of a consumer, to avoid having the consumer become a single point of failure in system.</span></span> <span data-ttu-id="c09e9-135">可能也必須有多個執行個體才能處理事件的數量和頻率。</span><span class="sxs-lookup"><span data-stu-id="c09e9-135">Multiple instances might also be necessary to handle the volume and frequency of events.</span></span> <span data-ttu-id="c09e9-136">此外，單一取用者也可以在多個執行緒上處理事件。</span><span class="sxs-lookup"><span data-stu-id="c09e9-136">Also, a single consumer might process events on multiple threads.</span></span> <span data-ttu-id="c09e9-137">如果事件必須按順序處理，或需要正好一次的語意，這可能會產生困難。</span><span class="sxs-lookup"><span data-stu-id="c09e9-137">This can create challenges if events must be processed in order, or require exactly-once semantics.</span></span> <span data-ttu-id="c09e9-138">請參閱[最小化協調][minimize-coordination]。</span><span class="sxs-lookup"><span data-stu-id="c09e9-138">See [Minimize Coordination][minimize-coordination].</span></span> 

## <a name="when-to-use-this-architecture"></a><span data-ttu-id="c09e9-139">使用此架構的時機</span><span class="sxs-lookup"><span data-stu-id="c09e9-139">When to use this architecture</span></span>

- <span data-ttu-id="c09e9-140">多個子系統必須處理相同的事件。</span><span class="sxs-lookup"><span data-stu-id="c09e9-140">Multiple subsystems must process the same events.</span></span> 
- <span data-ttu-id="c09e9-141">具有最小延遲時間的即時處理。</span><span class="sxs-lookup"><span data-stu-id="c09e9-141">Real-time processing with minimum time lag.</span></span>
- <span data-ttu-id="c09e9-142">複雜事件處理，例如模式比對或一段時間的彙總。</span><span class="sxs-lookup"><span data-stu-id="c09e9-142">Complex event processing, such as pattern matching or aggregation over time windows.</span></span>
- <span data-ttu-id="c09e9-143">大量和高速度的資料，例如 IoT。</span><span class="sxs-lookup"><span data-stu-id="c09e9-143">High volume and high velocity of data, such as IoT.</span></span>

## <a name="benefits"></a><span data-ttu-id="c09e9-144">優點</span><span class="sxs-lookup"><span data-stu-id="c09e9-144">Benefits</span></span>

- <span data-ttu-id="c09e9-145">產生者和取用者分離。</span><span class="sxs-lookup"><span data-stu-id="c09e9-145">Producers and consumers are decoupled.</span></span>
- <span data-ttu-id="c09e9-146">沒有點對點整合。</span><span class="sxs-lookup"><span data-stu-id="c09e9-146">No point-to point-integrations.</span></span> <span data-ttu-id="c09e9-147">可以輕鬆地將新的取用者新增至系統。</span><span class="sxs-lookup"><span data-stu-id="c09e9-147">It's easy to add new consumers to the system.</span></span>
- <span data-ttu-id="c09e9-148">取用者可以在事件到達時立即做出回應。</span><span class="sxs-lookup"><span data-stu-id="c09e9-148">Consumers can respond to events immediately as they arrive.</span></span> 
- <span data-ttu-id="c09e9-149">高度延展性且分散。</span><span class="sxs-lookup"><span data-stu-id="c09e9-149">Highly scalable and distributed.</span></span> 
- <span data-ttu-id="c09e9-150">子系統有獨立的事件串流檢視。</span><span class="sxs-lookup"><span data-stu-id="c09e9-150">Subsystems have independent views of the event stream.</span></span>

## <a name="challenges"></a><span data-ttu-id="c09e9-151">挑戰</span><span class="sxs-lookup"><span data-stu-id="c09e9-151">Challenges</span></span>

- <span data-ttu-id="c09e9-152">有保證的傳遞。</span><span class="sxs-lookup"><span data-stu-id="c09e9-152">Guaranteed delivery.</span></span> <span data-ttu-id="c09e9-153">在某些系統上，尤其是在 IoT 案例中，保證事件的傳遞是相當重要的事。</span><span class="sxs-lookup"><span data-stu-id="c09e9-153">In some systems, especially in IoT scenarios, it's crucial to guarantee that events are delivered.</span></span>
- <span data-ttu-id="c09e9-154">按順序處理事件，或只處理一次。</span><span class="sxs-lookup"><span data-stu-id="c09e9-154">Processing events in order or exactly once.</span></span> <span data-ttu-id="c09e9-155">每個取用者類型通常會在多個執行個體中執行，以便擁有復原功能和延展性。</span><span class="sxs-lookup"><span data-stu-id="c09e9-155">Each consumer type typically runs in multiple instances, for resiliency and scalability.</span></span> <span data-ttu-id="c09e9-156">如果事件必須按順序處理 (在某個取用者類型中)，或如果處理邏輯不是等冪的，這會產生挑戰。</span><span class="sxs-lookup"><span data-stu-id="c09e9-156">This can create a challenge if the events must be processed in order (within a consumer type), or if the processing logic is not idempotent.</span></span>

## <a name="iot-architecture"></a><span data-ttu-id="c09e9-157">IoT 架構</span><span class="sxs-lookup"><span data-stu-id="c09e9-157">IoT architecture</span></span>

<span data-ttu-id="c09e9-158">事件驅動架構是 IoT 解決方案的核心。</span><span class="sxs-lookup"><span data-stu-id="c09e9-158">Event-driven architectures are central to IoT solutions.</span></span> <span data-ttu-id="c09e9-159">下圖顯示 IoT 可能的邏輯架構。</span><span class="sxs-lookup"><span data-stu-id="c09e9-159">The following diagram shows a possible logical architecture for IoT.</span></span> <span data-ttu-id="c09e9-160">此圖強調架構的事件串流元件。</span><span class="sxs-lookup"><span data-stu-id="c09e9-160">The diagram emphasizes the event-streaming components of the architecture.</span></span>

![](./images/iot.png)

<span data-ttu-id="c09e9-161">**雲端閘道**會使用可靠、低延遲的傳訊系統在雲端邊界擷取裝置事件。</span><span class="sxs-lookup"><span data-stu-id="c09e9-161">The **cloud gateway** ingests device events at the cloud boundary, using a reliable, low latency messaging system.</span></span>

<span data-ttu-id="c09e9-162">裝置可能會將事件直接傳送到雲端閘道，或透過**現場閘道**來傳送。</span><span class="sxs-lookup"><span data-stu-id="c09e9-162">Devices might send events directly to the cloud gateway, or through a **field gateway**.</span></span> <span data-ttu-id="c09e9-163">現場閘道是專用的裝置或軟體，通常會與裝置共置，以便接收事件並將它們轉送到雲端閘道。</span><span class="sxs-lookup"><span data-stu-id="c09e9-163">A field gateway is a specialized device or software, usually colocated with the devices, that receives events and forwards them to the cloud gateway.</span></span> <span data-ttu-id="c09e9-164">現場閘道可能也會前置處理未經處理的裝置事件，以執行篩選、彙總或通訊協定轉換等功能。</span><span class="sxs-lookup"><span data-stu-id="c09e9-164">The field gateway might also preprocess the raw device events, performing functions such as filtering, aggregation, or protocol transformation.</span></span>

<span data-ttu-id="c09e9-165">在擷取之後，事件會通過一個或多個**串流處理器**，這些處理器可以路由傳送資料 (例如，傳送到儲存體) 或執行分析和其他處理。</span><span class="sxs-lookup"><span data-stu-id="c09e9-165">After ingestion, events go through one or more **stream processors** that can route the data (for example, to storage) or perform analytics and other processing.</span></span>

<span data-ttu-id="c09e9-166">以下是一些常見的處理類型。</span><span class="sxs-lookup"><span data-stu-id="c09e9-166">The following are some common types of processing.</span></span> <span data-ttu-id="c09e9-167">(此清單一定不怎麼詳盡)。</span><span class="sxs-lookup"><span data-stu-id="c09e9-167">(This list is certainly not exhaustive.)</span></span>

- <span data-ttu-id="c09e9-168">將事件資料寫入冷儲存體，以便封存或批次分析。</span><span class="sxs-lookup"><span data-stu-id="c09e9-168">Writing event data to cold storage, for archiving or batch analytics.</span></span>

- <span data-ttu-id="c09e9-169">最忙碌路徑分析，(近乎) 即時地分析事件串流，以偵測異常行為、辨識滾動時間範圍的模式，或在串流中發生特定情況時觸發警示。</span><span class="sxs-lookup"><span data-stu-id="c09e9-169">Hot path analytics, analyzing the event stream in (near) real time, to detect anomalies, recognize patterns over rolling time windows, or trigger alerts when a specific condition occurs in the stream.</span></span> 

- <span data-ttu-id="c09e9-170">處理裝置中特殊的非遙測訊息類型，例如通知和警示。</span><span class="sxs-lookup"><span data-stu-id="c09e9-170">Handling special types of non-telemetry messages from devices, such as notifications and alarms.</span></span> 

- <span data-ttu-id="c09e9-171">機器學習。</span><span class="sxs-lookup"><span data-stu-id="c09e9-171">Machine learning.</span></span>

<span data-ttu-id="c09e9-172">深灰色的方塊顯示的是 IoT 系統中，與事件串流沒有直接關係，但為求完整所以在此納入的元件。</span><span class="sxs-lookup"><span data-stu-id="c09e9-172">The boxes that are shaded gray show components of an IoT system that are not directly related to event streaming, but are included here for completeness.</span></span>

- <span data-ttu-id="c09e9-173">**裝置登錄**是已佈建之裝置的資料庫，包括裝置識別碼，且通常會包括裝置中繼資料，例如位置。</span><span class="sxs-lookup"><span data-stu-id="c09e9-173">The **device registry** is a database of the provisioned devices, including the device IDs and usually device metadata, such as location.</span></span>

- <span data-ttu-id="c09e9-174">**佈建 API** 是常見用於佈建和註冊新裝置的外部介面。</span><span class="sxs-lookup"><span data-stu-id="c09e9-174">The **provisioning API** is a common external interface for provisioning and registering new devices.</span></span>

- <span data-ttu-id="c09e9-175">某些 IoT 解決方案允許將**命令和控制訊息**傳送至裝置。</span><span class="sxs-lookup"><span data-stu-id="c09e9-175">Some IoT solutions allow **command and control messages** to be sent to devices.</span></span>

> <span data-ttu-id="c09e9-176">本節提供了極為高階的 IoT 檢視，其中有許多要考慮的微妙之處和挑戰。</span><span class="sxs-lookup"><span data-stu-id="c09e9-176">This section has presented a very high-level view of IoT, and there are many subtleties and challenges to consider.</span></span> <span data-ttu-id="c09e9-177">如需更詳細的參考架構和討論，請參閱 [Microsoft Azure IoT 參考架構][ iot-ref-arch] (PDF 下載)。</span><span class="sxs-lookup"><span data-stu-id="c09e9-177">For a more detailed reference architecture and discussion, see the [Microsoft Azure IoT Reference Architecture][iot-ref-arch] (PDF download).</span></span>

 <!-- links -->

[competing-consumers]: ../../patterns/competing-consumers.md
[iot-ref-arch]: https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/
[minimize-coordination]: ../design-principles/minimize-coordination.md



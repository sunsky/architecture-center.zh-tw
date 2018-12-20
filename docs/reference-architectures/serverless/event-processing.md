---
title: 使用 Azure Functions 進行無伺服器事件處理
titleSuffix: Azure Reference Architectures
description: 參考架構，該架構顯示無伺服器事件擷取和處理
author: MikeWasson
ms.date: 10/16/2018
ms.custom: seodec18
ms.openlocfilehash: 1a3c73ca35f7e849211837dee33a530d786c827f
ms.sourcegitcommit: 88a68c7e9b6b772172b7faa4b9fd9c061a9f7e9d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53119892"
---
# <a name="serverless-event-processing-using-azure-functions"></a><span data-ttu-id="281d7-103">使用 Azure Functions 進行無伺服器事件處理</span><span class="sxs-lookup"><span data-stu-id="281d7-103">Serverless event processing using Azure Functions</span></span>

<span data-ttu-id="281d7-104">此參考架構顯示[無伺服器](https://azure.microsoft.com/solutions/serverless/)、事件驅動的架構，該架構會擷取資料流、處理資料，並將結果寫入至後端資料庫。</span><span class="sxs-lookup"><span data-stu-id="281d7-104">This reference architecture shows a [serverless](https://azure.microsoft.com/solutions/serverless/), event-driven architecture that ingests a stream of data, processes the data, and writes the results to a back-end database.</span></span> <span data-ttu-id="281d7-105">此架構的參考實作可在 [GitHub][github] 上取得。</span><span class="sxs-lookup"><span data-stu-id="281d7-105">A reference implementation for this architecture is available on [GitHub][github].</span></span>

![使用 Azure Functions 的無伺服器事件處理參考架構](./_images/serverless-event-processing.png)

## <a name="architecture"></a><span data-ttu-id="281d7-107">架構</span><span class="sxs-lookup"><span data-stu-id="281d7-107">Architecture</span></span>

<span data-ttu-id="281d7-108">**事件中樞**會擷取資料流。</span><span class="sxs-lookup"><span data-stu-id="281d7-108">**Event Hubs** ingests the data stream.</span></span> <span data-ttu-id="281d7-109">[事件中樞][eh]是針對高輸送量資料流案例所設計的。</span><span class="sxs-lookup"><span data-stu-id="281d7-109">[Event Hubs][eh] is designed for high-throughput data streaming scenarios.</span></span>

> [!NOTE]
> <span data-ttu-id="281d7-110">針對 IoT 案例，我們建議 IoT 中樞。</span><span class="sxs-lookup"><span data-stu-id="281d7-110">For IoT scenarios, we recommend IoT Hub.</span></span> <span data-ttu-id="281d7-111">IoT 中樞有內建端點，與 Azure 事件中樞 API 相容，所以您可以在此架構中使用任一個服務，不需要在後端處理中進行重大變更。</span><span class="sxs-lookup"><span data-stu-id="281d7-111">IoT Hub has a built-in endpoint that’s compatible with the Azure Event Hubs API, so you can use either service in this architecture with no major changes in the backend processing.</span></span> <span data-ttu-id="281d7-112">如需詳細資訊，請參閱[將 IoT 裝置連線到 Azure：IoT 中樞和事件中樞][iot]。</span><span class="sxs-lookup"><span data-stu-id="281d7-112">For more information, see [Connecting IoT Devices to Azure: IoT Hub and Event Hubs][iot].</span></span>

<span data-ttu-id="281d7-113">**函式應用程式**。</span><span class="sxs-lookup"><span data-stu-id="281d7-113">**Function App**.</span></span> <span data-ttu-id="281d7-114">[Azure Functions][functions] 是無伺服器計算選項。</span><span class="sxs-lookup"><span data-stu-id="281d7-114">[Azure Functions][functions] is a serverless compute option.</span></span> <span data-ttu-id="281d7-115">它會使用事件驅動的模型，在該模型中一段程式碼 ("function") 是由觸發程序叫用。</span><span class="sxs-lookup"><span data-stu-id="281d7-115">It uses an event-driven model, where a piece of code (a “function”) is invoked by a trigger.</span></span> <span data-ttu-id="281d7-116">在此架構中，當事件抵達事件中樞時，它們會觸發函式，該函式會處理事件並將結果寫入至儲存體。</span><span class="sxs-lookup"><span data-stu-id="281d7-116">In this architecture, when events arrive at Event Hubs, they trigger a function that processes the events and writes the results to storage.</span></span>

<span data-ttu-id="281d7-117">函式應用程式適用於處理來自事件中樞的個別記錄。</span><span class="sxs-lookup"><span data-stu-id="281d7-117">Function Apps are suitable for processing individual records from Event Hubs.</span></span> <span data-ttu-id="281d7-118">對於更複雜的資料流處理案例，請考慮使用 Azure Databricks 或 Azure 串流分析的 Apache Spark。</span><span class="sxs-lookup"><span data-stu-id="281d7-118">For more complex stream processing scenarios, consider Apache Spark using Azure Databricks, or Azure Stream Analytics.</span></span>

<span data-ttu-id="281d7-119">**Cosmos DB**。</span><span class="sxs-lookup"><span data-stu-id="281d7-119">**Cosmos DB**.</span></span> <span data-ttu-id="281d7-120">[Cosmos DB][cosmosdb] 是多模型資料庫服務。</span><span class="sxs-lookup"><span data-stu-id="281d7-120">[Cosmos DB][cosmosdb] is a multi-model database service.</span></span> <span data-ttu-id="281d7-121">針對此案例，事件處理函式會使用 Cosmos DB [SQL API][cosmosdb-sql] 來儲存 JSON 記錄。</span><span class="sxs-lookup"><span data-stu-id="281d7-121">For this scenario, the event-processing function stores JSON records, using the Cosmos DB [SQL API][cosmosdb-sql].</span></span>

<span data-ttu-id="281d7-122">**佇列儲存體**。</span><span class="sxs-lookup"><span data-stu-id="281d7-122">**Queue storage**.</span></span> <span data-ttu-id="281d7-123">[佇列儲存體][queue]是用於無效信件訊息。</span><span class="sxs-lookup"><span data-stu-id="281d7-123">[Queue storage][queue] is used for dead letter messages.</span></span> <span data-ttu-id="281d7-124">如果在處理事件時發生錯誤，函式會儲存無效信件佇列中的事件資料供稍後處理。</span><span class="sxs-lookup"><span data-stu-id="281d7-124">If an error occurs while processing an event, the function stores the event data in a dead letter queue for later processing.</span></span> <span data-ttu-id="281d7-125">如需詳細資訊，請參閱[恢復功能考量](#resiliency-considerations)。</span><span class="sxs-lookup"><span data-stu-id="281d7-125">For more information, see [Resiliency Considerations](#resiliency-considerations).</span></span>

<span data-ttu-id="281d7-126">**Azure 監視器**。</span><span class="sxs-lookup"><span data-stu-id="281d7-126">**Azure Monitor**.</span></span> <span data-ttu-id="281d7-127">[監視器][monitor]會收集解決方案中部署的 Azure 服務相關效能計量。</span><span class="sxs-lookup"><span data-stu-id="281d7-127">[Monitor][monitor] collects performance metrics about the Azure services deployed in the solution.</span></span> <span data-ttu-id="281d7-128">透過儀表板中的資料視覺化，您可以看到解決方案的健康情況。</span><span class="sxs-lookup"><span data-stu-id="281d7-128">By visualizing these in a dashboard, you can get visibility  into the health of the solution.</span></span>

<span data-ttu-id="281d7-129">**Azure Pipelines**。</span><span class="sxs-lookup"><span data-stu-id="281d7-129">**Azure Pipelines**.</span></span> <span data-ttu-id="281d7-130">[Pipelines][pipelines] 是持續整合 (CI) 和持續傳遞 (CD) 服務，它會建置、測試及部署應用程式。</span><span class="sxs-lookup"><span data-stu-id="281d7-130">[Pipelines][pipelines] is a continuous integration (CI) and continuous delivery (CD) service that builds, tests, and deploys the application.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="281d7-131">延展性考量</span><span class="sxs-lookup"><span data-stu-id="281d7-131">Scalability considerations</span></span>

### <a name="event-hubs"></a><span data-ttu-id="281d7-132">事件中樞</span><span class="sxs-lookup"><span data-stu-id="281d7-132">Event Hubs</span></span>

<span data-ttu-id="281d7-133">事件中樞的輸送量容量會以[輸送量單位][eh-throughput]來測量。</span><span class="sxs-lookup"><span data-stu-id="281d7-133">The throughput capacity of Event Hubs is measured in [throughput units][eh-throughput].</span></span> <span data-ttu-id="281d7-134">您可以啟用[自動擴充][eh-autoscale]以自動調整事件中樞，這會根據流量 (上限為設定的最大值) 自動調整輸送量單位。</span><span class="sxs-lookup"><span data-stu-id="281d7-134">You can autoscale an event hub by enabling [auto-inflate][eh-autoscale], which automatically scales the throughput units based on traffic, up to a configured maximum.</span></span>

<span data-ttu-id="281d7-135">函式應用程式中的[事件中樞觸發程序][eh-trigger]會根據事件中樞中的分割區數目來進行調整。</span><span class="sxs-lookup"><span data-stu-id="281d7-135">The [Event Hub trigger][eh-trigger] in the function app scales according to the number of partitions in the event hub.</span></span> <span data-ttu-id="281d7-136">每個分割區一次獲指派一個函式執行個體。</span><span class="sxs-lookup"><span data-stu-id="281d7-136">Each partition is assigned one function instance at a time.</span></span> <span data-ttu-id="281d7-137">若要將輸送量最大化，請批次接收事件，而不是一次一個。</span><span class="sxs-lookup"><span data-stu-id="281d7-137">To maximize throughput, receive the events in a batch, instead of one at a time.</span></span>

### <a name="cosmos-db"></a><span data-ttu-id="281d7-138">Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="281d7-138">Cosmos DB</span></span>

<span data-ttu-id="281d7-139">Cosmos DB 的輸送量容量會以[要求單位][ru] (RU) 來測量。</span><span class="sxs-lookup"><span data-stu-id="281d7-139">Throughput capacity for Cosmos DB is measured in [Request Units][ru] (RU).</span></span> <span data-ttu-id="281d7-140">若要將 Cosmos DB 容器調整超過 10,000 RU，建立容器時您必須指定[分割區索引鍵][partition-key]，並在您建立的每份文件中包含分割區索引鍵。</span><span class="sxs-lookup"><span data-stu-id="281d7-140">In order to scale a Cosmos DB container past 10,000 RU, you must specify a [partition key][partition-key] when you create the container, and include the partition key in every document that you create.</span></span>

<span data-ttu-id="281d7-141">以下是良好分割區索引鍵的一些特性：</span><span class="sxs-lookup"><span data-stu-id="281d7-141">Here are some characteristics of a good partition key:</span></span>

- <span data-ttu-id="281d7-142">索引鍵值空間很大。</span><span class="sxs-lookup"><span data-stu-id="281d7-142">The key value space is large.</span></span>
- <span data-ttu-id="281d7-143">會針對每個索引鍵值平均分配讀取/寫入，避免熱門索引鍵。</span><span class="sxs-lookup"><span data-stu-id="281d7-143">There will be an even distribution of reads/writes per key value, avoiding hot keys.</span></span>
- <span data-ttu-id="281d7-144">針對任何單一索引鍵值所儲存的資料上限不會超過最大實體分割區大小 (10 GB)。</span><span class="sxs-lookup"><span data-stu-id="281d7-144">The maximum data stored for any single key value will not exceed the maximum physical partition size (10 GB).</span></span>
- <span data-ttu-id="281d7-145">文件的分割區索引鍵不會變更。</span><span class="sxs-lookup"><span data-stu-id="281d7-145">The partition key for a document won't change.</span></span> <span data-ttu-id="281d7-146">您無法更新現有文件上的分割區索引鍵。</span><span class="sxs-lookup"><span data-stu-id="281d7-146">You can't update the partition key on an existing document.</span></span>

<span data-ttu-id="281d7-147">在此參考架構的案例中，函式會針對傳送資料的各個裝置，確實儲存一個文件。</span><span class="sxs-lookup"><span data-stu-id="281d7-147">In the scenario for this reference architecture, the function stores exactly one document per device that is sending data.</span></span> <span data-ttu-id="281d7-148">函式會使用 upsert 作業，持續以最新的裝置狀態來更新文件。</span><span class="sxs-lookup"><span data-stu-id="281d7-148">The function continually updates the documents with latest device status, using an upsert operation.</span></span> <span data-ttu-id="281d7-149">裝置識別碼對於此案例是很好的分割區索引鍵，因為寫入會平均分配到索引鍵，而且每個分割區的大小會嚴格限定，因為每個索引鍵值有單一文件。</span><span class="sxs-lookup"><span data-stu-id="281d7-149">Device ID is a good partition key for this scenario, because writes will be evenly distributed across the keys, and the size of each partition will be strictly bounded, because there is a single document for each key value.</span></span> <span data-ttu-id="281d7-150">如需有關分割區索引鍵的詳細資訊，請參閱[在 Azure Cosmos DB 中進行資料分割和調整][cosmosdb-scale]。</span><span class="sxs-lookup"><span data-stu-id="281d7-150">For more information about partition keys, see [Partition and scale in Azure Cosmos DB][cosmosdb-scale].</span></span>

## <a name="resiliency-considerations"></a><span data-ttu-id="281d7-151">恢復功能考量</span><span class="sxs-lookup"><span data-stu-id="281d7-151">Resiliency considerations</span></span>

<span data-ttu-id="281d7-152">搭配 Functions 使用事件中樞觸發程序時，攔截您處理迴圈內的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="281d7-152">When using the Event Hubs trigger with Functions, catch exceptions within your processing loop.</span></span> <span data-ttu-id="281d7-153">如果發生無法處理的例外狀況，Functions 執行階段不會重試訊息。</span><span class="sxs-lookup"><span data-stu-id="281d7-153">If an unhandled exception occurs, the Functions runtime does not retry the messages.</span></span> <span data-ttu-id="281d7-154">如果無法處理訊息，請將訊息放入無效信件佇列。</span><span class="sxs-lookup"><span data-stu-id="281d7-154">If a message cannot be processed, put the message into a dead letter queue.</span></span> <span data-ttu-id="281d7-155">使用頻外程序來檢查訊息並判斷矯正措施。</span><span class="sxs-lookup"><span data-stu-id="281d7-155">Use an out-of-band process to examine the messages and determine corrective action.</span></span>

<span data-ttu-id="281d7-156">下列程式碼示範擷取函式如何攔截例外狀況，並將未處理的訊息放入無效信件佇列。</span><span class="sxs-lookup"><span data-stu-id="281d7-156">The following code shows how the ingestion function catches exceptions and puts unprocessed messages onto a dead letter queue.</span></span>

```csharp
[FunctionName("RawTelemetryFunction")]
[StorageAccount("DeadLetterStorage")]
public static async Task RunAsync(
    [EventHubTrigger("%EventHubName%", Connection = "EventHubConnection", ConsumerGroup ="%EventHubConsumerGroup%")]EventData[] messages,
    [Queue("deadletterqueue")] IAsyncCollector<DeadLetterMessage> deadLetterMessages,
    ILogger logger)
{
    foreach (var message in messages)
    {
        DeviceState deviceState = null;

        try
        {
            deviceState = telemetryProcessor.Deserialize(message.Body.Array, logger);
        }
        catch (Exception ex)
        {
            logger.LogError(ex, "Error deserializing message", message.SystemProperties.PartitionKey, message.SystemProperties.SequenceNumber);
            await deadLetterMessages.AddAsync(new DeadLetterMessage { Issue = ex.Message, EventData = message });
        }

        try
        {
            await stateChangeProcessor.UpdateState(deviceState, logger);
        }
        catch (Exception ex)
        {
            logger.LogError(ex, "Error updating status document", deviceState);
            await deadLetterMessages.AddAsync(new DeadLetterMessage { Issue = ex.Message, EventData = message, DeviceState = deviceState });
        }
    }
}
```

<span data-ttu-id="281d7-157">請注意，函式會使用[佇列儲存體輸出繫結][queue-binding]將項目放入佇列。</span><span class="sxs-lookup"><span data-stu-id="281d7-157">Notice that the function uses the [Queue storage output binding][queue-binding] to put items in the queue.</span></span>

<span data-ttu-id="281d7-158">如上所示的程式碼也會將例外狀況記錄至 Application Insights。</span><span class="sxs-lookup"><span data-stu-id="281d7-158">The code shown above also logs exceptions to Application Insights.</span></span> <span data-ttu-id="281d7-159">您可以使用分割區索引鍵和序號，將無效信件訊息與記錄中的例外狀況相互關聯。</span><span class="sxs-lookup"><span data-stu-id="281d7-159">You can use the partition key and sequence number to correlate dead letter messages with the exceptions in the logs.</span></span>

<span data-ttu-id="281d7-160">無效信件佇列中的訊息應該有足夠的資訊，讓您可以了解錯誤的內容。</span><span class="sxs-lookup"><span data-stu-id="281d7-160">Messages in the dead letter queue should have enough information so that you can understand the context of error.</span></span> <span data-ttu-id="281d7-161">在此範例中，`DeadLetterMessage` 類別包含例外狀況訊息、原始事件資料，以及還原序列化事件訊息 (如果有的話)。</span><span class="sxs-lookup"><span data-stu-id="281d7-161">In this example, the `DeadLetterMessage` class contains the exception message, the original event data, and the deserialized event message (if available).</span></span>

```csharp
public class DeadLetterMessage
{
    public string Issue { get; set; }
    public EventData EventData { get; set; }
    public DeviceState DeviceState { get; set; }
}
```

<span data-ttu-id="281d7-162">使用 [Azure 監視器][monitor]來監視事件中樞。</span><span class="sxs-lookup"><span data-stu-id="281d7-162">Use [Azure Monitor][monitor] to monitor the event hub.</span></span> <span data-ttu-id="281d7-163">如果您發現有輸入但沒有輸出，這表示未處理訊息。</span><span class="sxs-lookup"><span data-stu-id="281d7-163">If you see there is input but no output, it means that messages are not being processed.</span></span> <span data-ttu-id="281d7-164">在此情況下，進入 [Log Analytics][log-analytics] 並尋找例外狀況或其他錯誤。</span><span class="sxs-lookup"><span data-stu-id="281d7-164">In that case, go into [Log Analytics][log-analytics] and look for exceptions or other errors.</span></span>

## <a name="disaster-recovery-considerations"></a><span data-ttu-id="281d7-165">災害復原考量</span><span class="sxs-lookup"><span data-stu-id="281d7-165">Disaster recovery considerations</span></span>

<span data-ttu-id="281d7-166">這裡示範的部署是位於單一 Azure 區域中。</span><span class="sxs-lookup"><span data-stu-id="281d7-166">The deployment shown here resides in a single Azure region.</span></span> <span data-ttu-id="281d7-167">如需更有彈性的災害復原方法，請利用各種服務中的地區散發功能：</span><span class="sxs-lookup"><span data-stu-id="281d7-167">For a more resilient approach to disaster-recovery, take advantage of geo-distribution features in the various services:</span></span>

- <span data-ttu-id="281d7-168">**事件中樞**。</span><span class="sxs-lookup"><span data-stu-id="281d7-168">**Event Hubs**.</span></span> <span data-ttu-id="281d7-169">建立兩個事件中樞命名空間，主要 (主動) 命名空間和次要 (被動) 命名空間。</span><span class="sxs-lookup"><span data-stu-id="281d7-169">Create two Event Hubs namespaces, a primary (active) namespace and a secondary (passive) namespace.</span></span> <span data-ttu-id="281d7-170">訊息會自動路由傳送至主動命名空間，除非您容錯移轉至次要命名空間。</span><span class="sxs-lookup"><span data-stu-id="281d7-170">Messages are automatically routed to the active namespace unless you fail over to the secondary namespace.</span></span> <span data-ttu-id="281d7-171">如需詳細資訊，請參閱 [Azure 事件中樞地理災害復原][eh-dr]。</span><span class="sxs-lookup"><span data-stu-id="281d7-171">For more information, see [Azure Event Hubs Geo-disaster recovery][eh-dr].</span></span>

- <span data-ttu-id="281d7-172">**函式應用程式**。</span><span class="sxs-lookup"><span data-stu-id="281d7-172">**Function App**.</span></span> <span data-ttu-id="281d7-173">部署第二個函式應用程式，該應用程式正在等候從次要事件中樞命名空間中讀取。</span><span class="sxs-lookup"><span data-stu-id="281d7-173">Deploy a second function app that is waiting to read from the secondary Event Hubs namespace.</span></span> <span data-ttu-id="281d7-174">此函式會寫入至無效信件佇列的次要儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="281d7-174">This function writes to a secondary storage account for dead letter queue.</span></span>

- <span data-ttu-id="281d7-175">**Cosmos DB**。</span><span class="sxs-lookup"><span data-stu-id="281d7-175">**Cosmos DB**.</span></span> <span data-ttu-id="281d7-176">Cosmos DB 支援[多重主要區域][cosmosdb-geo]，可讓您寫入至新增到 Cosmos DB 帳戶的任何區域。</span><span class="sxs-lookup"><span data-stu-id="281d7-176">Cosmos DB supports [multiple master regions][cosmosdb-geo], which enables writes to any region that you add to your Cosmos DB account.</span></span> <span data-ttu-id="281d7-177">如果您未啟用多重主要，您仍然可以容錯移轉主要寫入區域。</span><span class="sxs-lookup"><span data-stu-id="281d7-177">If you don’t enable multi-master, you can still fail over the primary write region.</span></span> <span data-ttu-id="281d7-178">Cosmos DB 用戶端 SDK 與 Azure Function 繫結會自動處理容錯移轉，因此您不需要更新任何應用程式組態設定。</span><span class="sxs-lookup"><span data-stu-id="281d7-178">The Cosmos DB client SDKs and the Azure Function bindings automatically handle the failover, so you don’t need to update any application configuration settings.</span></span>

- <span data-ttu-id="281d7-179">**Azure 儲存體**。</span><span class="sxs-lookup"><span data-stu-id="281d7-179">**Azure Storage**.</span></span> <span data-ttu-id="281d7-180">針對無效信件佇列使用 [RA-GRS][ra-grs] 儲存體。</span><span class="sxs-lookup"><span data-stu-id="281d7-180">Use [RA-GRS][ra-grs] storage for the dead letter queue.</span></span> <span data-ttu-id="281d7-181">這樣會在另一個區域中建立唯讀複本。</span><span class="sxs-lookup"><span data-stu-id="281d7-181">This creates a read-only replica in another region.</span></span> <span data-ttu-id="281d7-182">如果主要區域無法使用，您可以讀取目前在佇列中的項目。</span><span class="sxs-lookup"><span data-stu-id="281d7-182">If the primary region becomes unavailable, you can read the items currently in the queue.</span></span> <span data-ttu-id="281d7-183">此外，在次要區域中佈建另一個儲存體帳戶，讓函式可以在容錯移轉之後寫入至其中。</span><span class="sxs-lookup"><span data-stu-id="281d7-183">In addition, provision another storage account in the secondary region that the function can write to after a fail-over.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="281d7-184">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="281d7-184">Deploy the solution</span></span>

<span data-ttu-id="281d7-185">若要部署此參考架構，請檢視 [GitHub 讀我檔案][readme]。</span><span class="sxs-lookup"><span data-stu-id="281d7-185">To deploy this reference architecture, view the [GitHub readme][readme].</span></span>

<!-- links -->

[cosmosdb]: /azure/cosmos-db/introduction
[cosmosdb-geo]: /azure/cosmos-db/distribute-data-globally
[cosmosdb-scale]: /azure/cosmos-db/partition-data
[cosmosdb-sql]: /azure/cosmos-db/sql-api-introduction
[eh]: /azure/event-hubs/
[eh-autoscale]: /azure/event-hubs/event-hubs-auto-inflate
[eh-dr]: /azure/event-hubs/event-hubs-geo-dr
[eh-throughput]: /azure/event-hubs/event-hubs-features#throughput-units
[eh-trigger]: /azure/azure-functions/functions-bindings-event-hubs
[functions]: /azure/azure-functions/functions-overview
[iot]: /azure/iot-hub/iot-hub-compare-event-hubs
[log-analytics]: /azure/log-analytics/log-analytics-queries
[monitor]: /azure/azure-monitor/overview
[partition-key]: /azure/cosmos-db/partition-data
[pipelines]: /azure/devops/pipelines/index
[queue]: /azure/storage/queues/storage-queues-introduction
[queue-binding]: /azure/azure-functions/functions-bindings-storage-queue#output
[ra-grs]: /azure/storage/common/storage-redundancy-grs
[ru]: /azure/cosmos-db/request-units

[github]: https://github.com/mspnp/serverless-reference-implementation
[readme]: https://github.com/mspnp/serverless-reference-implementation/blob/master/README.md

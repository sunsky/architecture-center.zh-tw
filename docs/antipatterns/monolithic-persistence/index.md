---
title: 整合的持續性反模式
description: 將所有應用程式的資料放入單一資料存放區會降低效能。
author: dragon119
ms.date: 06/05/2017
ms.openlocfilehash: 8cc67a41adf7ca4e3c5475eea86e38b75dd65d4d
ms.sourcegitcommit: 94d50043db63416c4d00cebe927a0c88f78c3219
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2018
ms.locfileid: "47429106"
---
# <a name="monolithic-persistence-antipattern"></a><span data-ttu-id="3d296-103">整合的持續性反模式</span><span class="sxs-lookup"><span data-stu-id="3d296-103">Monolithic Persistence antipattern</span></span>

<span data-ttu-id="3d296-104">將所有應用程式的資料放入單一資料存放區會降低效能，因為會造成資源爭用，或資料存放區不適合某些資料。</span><span class="sxs-lookup"><span data-stu-id="3d296-104">Putting all of an application's data into a single data store can hurt performance, either because it leads to resource contention, or because the data store is not a good fit for some of the data.</span></span>

## <a name="problem-description"></a><span data-ttu-id="3d296-105">問題說明</span><span class="sxs-lookup"><span data-stu-id="3d296-105">Problem description</span></span>

<span data-ttu-id="3d296-106">以前應用程式經常使用單一資料存放區，不論需要儲存之應用程式資料的類型是否不同。</span><span class="sxs-lookup"><span data-stu-id="3d296-106">Historically, applications have often used a single data store, regardless of the different types of data that the application might need to store.</span></span> <span data-ttu-id="3d296-107">通常這是為了簡化應用程式設計，或是符合開發小組的現有技能集。</span><span class="sxs-lookup"><span data-stu-id="3d296-107">Usually this was done to simplify the application design, or else to match the existing skill set of the development team.</span></span> 

<span data-ttu-id="3d296-108">現代化雲端式系統通常會有其他的功能性和非功能性需求，而且需要儲存許多異質性類型的資料，例如文件、映像、快取資料、已佇列的訊息、應用程式記錄檔和遙測。</span><span class="sxs-lookup"><span data-stu-id="3d296-108">Modern cloud-based systems often have additional functional and nonfunctional requirements, and need to store many heterogenous types of data, such as documents, images, cached data, queued messages, application logs, and telemetry.</span></span> <span data-ttu-id="3d296-109">遵循將這項資訊全部放入相同資料存放區的傳統方法會降低效能，有兩個主要原因：</span><span class="sxs-lookup"><span data-stu-id="3d296-109">Following the traditional approach and putting all of this information into the same data store can hurt performance, for two main reasons:</span></span>

- <span data-ttu-id="3d296-110">在相同資料存放區中儲存和擷取大量不相關的資料，可能會造成爭用，進而導致緩慢回應時間和連線失敗。</span><span class="sxs-lookup"><span data-stu-id="3d296-110">Storing and retrieving large amounts of unrelated data in the same data store can cause contention, which in turn leads to slow response times and connection failures.</span></span>
- <span data-ttu-id="3d296-111">無論選擇哪個資料存放區，無法適合所有不同類型的資料，或無法針對應用程式執行的作業最佳化。</span><span class="sxs-lookup"><span data-stu-id="3d296-111">Whichever data store is chosen, it might not be the best fit for all of the different types of data, or it might not be optimized for the operations that the application performs.</span></span> 

<span data-ttu-id="3d296-112">下列範例會顯示 ASP.NET Web API 控制器，它會將新記錄新增至資料庫，也會將結果記錄到記錄檔。</span><span class="sxs-lookup"><span data-stu-id="3d296-112">The following example shows an ASP.NET Web API controller that adds a new record to a database and also records the result to a log.</span></span> <span data-ttu-id="3d296-113">記錄會保留在與商務資料相同的資料庫。</span><span class="sxs-lookup"><span data-stu-id="3d296-113">The log is held in the same database as the business data.</span></span> <span data-ttu-id="3d296-114">您可以在[這裡][sample-app]找到完整的範例。</span><span class="sxs-lookup"><span data-stu-id="3d296-114">You can find the complete sample [here][sample-app].</span></span>

```csharp
public class MonoController : ApiController
{
    private static readonly string ProductionDb = ...;

    public async Task<IHttpActionResult> PostAsync([FromBody]string value)
    {
        await DataAccess.InsertPurchaseOrderHeaderAsync(ProductionDb);
        await DataAccess.LogAsync(ProductionDb, LogTableName);
        return Ok();
    }
}
```

<span data-ttu-id="3d296-115">記錄檔記錄產生的速率可能會影響商務作業的效能。</span><span class="sxs-lookup"><span data-stu-id="3d296-115">The rate at which log records are generated will probably affect the performance of the business operations.</span></span> <span data-ttu-id="3d296-116">如果另一個元件 (例如應用程式處理序監視器) 定期讀取並處理記錄資料，也會影響商務作業。</span><span class="sxs-lookup"><span data-stu-id="3d296-116">And if another component, such as an application process monitor, regularly reads and processes the log data, that can also affect the business operations.</span></span>

## <a name="how-to-fix-the-problem"></a><span data-ttu-id="3d296-117">如何修正問題</span><span class="sxs-lookup"><span data-stu-id="3d296-117">How to fix the problem</span></span>

<span data-ttu-id="3d296-118">根據其使用分隔資料。</span><span class="sxs-lookup"><span data-stu-id="3d296-118">Separate data according to its use.</span></span> <span data-ttu-id="3d296-119">針對每個資料集，選取最符合該資料集使用方式的資料存放區。</span><span class="sxs-lookup"><span data-stu-id="3d296-119">For each data set, select a data store that best matches how that data set will be used.</span></span> <span data-ttu-id="3d296-120">在上一個範例中，應用程式應該會記錄到與保存商務資料之資料庫不同的個別存放區：</span><span class="sxs-lookup"><span data-stu-id="3d296-120">In the previous example, the application should be logging to a separate store from the database that holds business data:</span></span> 

```csharp
public class PolyController : ApiController
{
    private static readonly string ProductionDb = ...;
    private static readonly string LogDb = ...;

    public async Task<IHttpActionResult> PostAsync([FromBody]string value)
    {
        await DataAccess.InsertPurchaseOrderHeaderAsync(ProductionDb);
        // Log to a different data store.
        await DataAccess.LogAsync(LogDb, LogTableName);
        return Ok();
    }
}
```

## <a name="considerations"></a><span data-ttu-id="3d296-121">考量</span><span class="sxs-lookup"><span data-stu-id="3d296-121">Considerations</span></span>

- <span data-ttu-id="3d296-122">根據資料的使用方式及存取方式來分隔資料。</span><span class="sxs-lookup"><span data-stu-id="3d296-122">Separate data by the way it is used and how it is accessed.</span></span> <span data-ttu-id="3d296-123">例如，不要將記錄資訊和商務資料儲存在相同的資料存放區。</span><span class="sxs-lookup"><span data-stu-id="3d296-123">For example, don't store log information and business data in the same data store.</span></span> <span data-ttu-id="3d296-124">這些類型的資料有明顯不同的需求和存取模式。</span><span class="sxs-lookup"><span data-stu-id="3d296-124">These types of data have significantly different requirements and patterns of access.</span></span> <span data-ttu-id="3d296-125">記錄檔記錄本來就是循序的，而商務資料比較像是需要隨機存取，且通常是關聯式。</span><span class="sxs-lookup"><span data-stu-id="3d296-125">Log records are inherently sequential, while business data is more likely to require random access, and is often relational.</span></span>

- <span data-ttu-id="3d296-126">請考慮每種資料類型的資料存取模式。</span><span class="sxs-lookup"><span data-stu-id="3d296-126">Consider the data access pattern for each type of data.</span></span> <span data-ttu-id="3d296-127">例如，在像是 [Cosmos DB][CosmosDB] 的文件資料庫儲存格式化報告和文件，但是使用 [Azure Redis 快取][Azure-cache] 以快取暫存資料。</span><span class="sxs-lookup"><span data-stu-id="3d296-127">For example, store formatted reports and documents in a document database such as [Cosmos DB][CosmosDB], but use [Azure Redis Cache][Azure-cache] to cache temporary data.</span></span>

- <span data-ttu-id="3d296-128">如果您遵循本指南，但仍然達到資料庫的限制，您可能需要相應增加資料庫。</span><span class="sxs-lookup"><span data-stu-id="3d296-128">If you follow this guidance but still reach the limits of the database, you may need to scale up the database.</span></span> <span data-ttu-id="3d296-129">另外請考慮水平調整以及在資料庫伺服器之間分割負載。</span><span class="sxs-lookup"><span data-stu-id="3d296-129">Also consider scaling horizontally and partitioning the load across database servers.</span></span> <span data-ttu-id="3d296-130">不過，資料分割可能需要重新設計應用程式。</span><span class="sxs-lookup"><span data-stu-id="3d296-130">However, partitioning may require redesigning the application.</span></span> <span data-ttu-id="3d296-131">如需詳細資訊，請參閱[資料分割指引][DataPartitioningGuidance]。</span><span class="sxs-lookup"><span data-stu-id="3d296-131">For more information, see [Data partitioning][DataPartitioningGuidance].</span></span>

## <a name="how-to-detect-the-problem"></a><span data-ttu-id="3d296-132">如何偵測問題</span><span class="sxs-lookup"><span data-stu-id="3d296-132">How to detect the problem</span></span>

<span data-ttu-id="3d296-133">系統可能會大幅減慢，最終失敗，因為系統用盡例如資料庫連線的資源。</span><span class="sxs-lookup"><span data-stu-id="3d296-133">The system will likely slow down dramatically and eventually fail, as the system runs out of resources such as database connections.</span></span>

<span data-ttu-id="3d296-134">您可以執行下列步驟來協助識別原因。</span><span class="sxs-lookup"><span data-stu-id="3d296-134">You can perform the following steps to help identify the cause.</span></span>

1. <span data-ttu-id="3d296-135">檢測系統以記錄關鍵效能統計資料。</span><span class="sxs-lookup"><span data-stu-id="3d296-135">Instrument the system to record the key performance statistics.</span></span> <span data-ttu-id="3d296-136">擷取每個作業的計時資訊，以及應用程式讀取及寫入資料的位置點。</span><span class="sxs-lookup"><span data-stu-id="3d296-136">Capture timing information for each operation, as well as the points where the application reads and writes data.</span></span>
1. <span data-ttu-id="3d296-137">盡可能監視在生產環境中執行數天的系統，以取得系統使用方式的實際檢視。</span><span class="sxs-lookup"><span data-stu-id="3d296-137">If possible, monitor the system running for a few days in a production environment to get a real-world view of how the system is used.</span></span> <span data-ttu-id="3d296-138">如果不可行，以執行典型系列作業的虛擬使用者實際數量執行指令碼式負載測試。</span><span class="sxs-lookup"><span data-stu-id="3d296-138">If this is not possible, run scripted load tests with a realistic volume of virtual users performing a typical series of operations.</span></span>
2. <span data-ttu-id="3d296-139">使用遙測資料來識別效能不佳的期間。</span><span class="sxs-lookup"><span data-stu-id="3d296-139">Use the telemetry data to identify periods of poor performance.</span></span>
3. <span data-ttu-id="3d296-140">識別在這些期間存取了哪些資料存放區。</span><span class="sxs-lookup"><span data-stu-id="3d296-140">Identify which data stores were accessed during those periods.</span></span>
4. <span data-ttu-id="3d296-141">識別可能經歷爭用的資料儲存體資源。</span><span class="sxs-lookup"><span data-stu-id="3d296-141">Identify data storage resources that might be experiencing contention.</span></span>

## <a name="example-diagnosis"></a><span data-ttu-id="3d296-142">範例診斷</span><span class="sxs-lookup"><span data-stu-id="3d296-142">Example diagnosis</span></span>

<span data-ttu-id="3d296-143">下列各節會將這些步驟套用到稍早所述的範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="3d296-143">The following sections apply these steps to the sample application described earlier.</span></span>

### <a name="instrument-and-monitor-the-system"></a><span data-ttu-id="3d296-144">檢測和監視系統</span><span class="sxs-lookup"><span data-stu-id="3d296-144">Instrument and monitor the system</span></span>

<span data-ttu-id="3d296-145">下圖顯示針對稍早所述之範例應用程式進行負載測試的結果。</span><span class="sxs-lookup"><span data-stu-id="3d296-145">The following graph shows the results of load testing the sample application described earlier.</span></span> <span data-ttu-id="3d296-146">測試使用最多 1000 個並行使用者的步驟負載。</span><span class="sxs-lookup"><span data-stu-id="3d296-146">The test used a step load of up to 1000 concurrent users.</span></span>

![以 SQL 為基礎之控制站的負載測試效能結果][MonolithicScenarioLoadTest]

<span data-ttu-id="3d296-148">隨著負載增加至 700 個使用者，輸送量也會跟著增加。</span><span class="sxs-lookup"><span data-stu-id="3d296-148">As the load increases to 700 users, so does the throughput.</span></span> <span data-ttu-id="3d296-149">屆時，輸送量會穩定，系統會以其最大容量執行。</span><span class="sxs-lookup"><span data-stu-id="3d296-149">But at that point, throughput levels off, and the system appears to be running at its maximum capacity.</span></span> <span data-ttu-id="3d296-150">平均回應會隨著使用者負載而逐漸增加，顯示系統無法跟上需求。</span><span class="sxs-lookup"><span data-stu-id="3d296-150">The average response gradually increases with user load, showing that the system can't keep up with demand.</span></span>

### <a name="identify-periods-of-poor-performance"></a><span data-ttu-id="3d296-151">識別效能不佳的期間</span><span class="sxs-lookup"><span data-stu-id="3d296-151">Identify periods of poor performance</span></span>

<span data-ttu-id="3d296-152">如果您正在監視生產系統，您可能會發現模式。</span><span class="sxs-lookup"><span data-stu-id="3d296-152">If you are monitoring the production system, you might notice patterns.</span></span> <span data-ttu-id="3d296-153">例如，回應時間可能會在每天的相同時間大幅下滑。</span><span class="sxs-lookup"><span data-stu-id="3d296-153">For example, response times might drop off significantly at the same time each day.</span></span> <span data-ttu-id="3d296-154">這可能是由一般工作負載或排程批次作業所造成，或者只是因為系統在特定時間有更多使用者。</span><span class="sxs-lookup"><span data-stu-id="3d296-154">This could be caused by a regular workload or scheduled batch job, or just because the system has more users at certain times.</span></span> <span data-ttu-id="3d296-155">您應該專注於這些事件的遙測資料。</span><span class="sxs-lookup"><span data-stu-id="3d296-155">You should focus on the telemetry data for these events.</span></span>

<span data-ttu-id="3d296-156">尋找增加的回應時間和增加的資料庫活動或 I/O 之間的相互關聯，以共用資源。</span><span class="sxs-lookup"><span data-stu-id="3d296-156">Look for correlations between increased response times and increased database activity or I/O to shared resources.</span></span> <span data-ttu-id="3d296-157">如果有相互關聯，則表示資料庫可能是瓶頸。</span><span class="sxs-lookup"><span data-stu-id="3d296-157">If there are correlations, it means the database might be a bottleneck.</span></span>

### <a name="identify-which-data-stores-are-accessed-during-those-periods"></a><span data-ttu-id="3d296-158">識別在這些期間存取了哪些資料存放區</span><span class="sxs-lookup"><span data-stu-id="3d296-158">Identify which data stores are accessed during those periods</span></span>

<span data-ttu-id="3d296-159">下圖會顯示在負載測試期間資料庫輸送量單位 (DTU) 的使用率。</span><span class="sxs-lookup"><span data-stu-id="3d296-159">The next graph shows the utilization of database throughput units (DTU) during the load test.</span></span> <span data-ttu-id="3d296-160">(DTU 是可用容量的量值，也是 CPU 使用率、記憶體配置、I/O 速率的組合。)DTU 使用率快速達到 100%。</span><span class="sxs-lookup"><span data-stu-id="3d296-160">(A DTU is a measure of available capacity, and is a combination of CPU utilization, memory allocation, I/O rate.) Utilization of DTUs quickly reached 100%.</span></span> <span data-ttu-id="3d296-161">這大約是上圖中的輸送量尖峰點。</span><span class="sxs-lookup"><span data-stu-id="3d296-161">This is roughly the point where throughput peaked in the previous graph.</span></span> <span data-ttu-id="3d296-162">測試完成之前，資料庫使用率仍然相當高。</span><span class="sxs-lookup"><span data-stu-id="3d296-162">Database utilization remained very high until the test finished.</span></span> <span data-ttu-id="3d296-163">到結束之前會有些微下滑，可能是因為節流、資料庫連線的競爭或其他因素所造成。</span><span class="sxs-lookup"><span data-stu-id="3d296-163">There is a slight drop toward the end, which could be caused by throttling, competition for database connections, or other factors.</span></span>

![Azure 傳統入口網站中的資料庫監視會顯示資料庫的資源使用率][MonolithicDatabaseUtilization]

### <a name="examine-the-telemetry-for-the-data-stores"></a><span data-ttu-id="3d296-165">檢查資料存放區的遙測</span><span class="sxs-lookup"><span data-stu-id="3d296-165">Examine the telemetry for the data stores</span></span>

<span data-ttu-id="3d296-166">檢測資料存放區以擷取活動的低層級詳細資料。</span><span class="sxs-lookup"><span data-stu-id="3d296-166">Instrument the data stores to capture the low-level details of the activity.</span></span> <span data-ttu-id="3d296-167">在範例應用程式中，資料存取統計資料會顯示針對 `PurchaseOrderHeader` 資料表和 `MonoLog` 資料表執行的大量插入作業。</span><span class="sxs-lookup"><span data-stu-id="3d296-167">In the sample application, the data access statistics showed a high volume of insert operations performed against both the `PurchaseOrderHeader` table and the `MonoLog` table.</span></span> 

![範例應用程式的資料存取統計資料][MonolithicDataAccessStats]

### <a name="identify-resource-contention"></a><span data-ttu-id="3d296-169">識別資源爭用</span><span class="sxs-lookup"><span data-stu-id="3d296-169">Identify resource contention</span></span>

<span data-ttu-id="3d296-170">此時，您可以檢閱原始程式碼，將焦點放在應用程式存取之爭用資源的位置點。</span><span class="sxs-lookup"><span data-stu-id="3d296-170">At this point, you can review the source code, focusing on the points where contended resources are accessed by the application.</span></span> <span data-ttu-id="3d296-171">尋找如下的情況：</span><span class="sxs-lookup"><span data-stu-id="3d296-171">Look for situations such as:</span></span>

- <span data-ttu-id="3d296-172">以邏輯方式分隔的資料被寫入相同的存放區。</span><span class="sxs-lookup"><span data-stu-id="3d296-172">Data that is logically separate being written to the same store.</span></span> <span data-ttu-id="3d296-173">例如記錄、報告和已佇列的訊息之資料不應該保存在與商務資訊相同的資料庫。</span><span class="sxs-lookup"><span data-stu-id="3d296-173">Data such as logs, reports, and queued messages should not be held in the same database as business information.</span></span>
- <span data-ttu-id="3d296-174">資料存放區和資料類型選擇之間的不相符，例如大型 blob 或關聯式資料庫中的 XML 文件。</span><span class="sxs-lookup"><span data-stu-id="3d296-174">A mismatch between the choice of data store and the type of data, such as large blobs or XML documents in a relational database.</span></span>
- <span data-ttu-id="3d296-175">具有差異極大之使用模式但是共用相同存放區的資料，例如以與低寫入/高讀取資料儲存的高寫入/低讀取資料。</span><span class="sxs-lookup"><span data-stu-id="3d296-175">Data with significantly different usage patterns that share the same store, such as high-write/low-read data being stored with low-write/high-read data.</span></span>

### <a name="implement-the-solution-and-verify-the-result"></a><span data-ttu-id="3d296-176">實作解決方案並確認結果</span><span class="sxs-lookup"><span data-stu-id="3d296-176">Implement the solution and verify the result</span></span>

<span data-ttu-id="3d296-177">應用程式已變更為將記錄寫入個別資料存放區。</span><span class="sxs-lookup"><span data-stu-id="3d296-177">The application was changed to write logs to a separate data store.</span></span> <span data-ttu-id="3d296-178">以下是負載測試結果：</span><span class="sxs-lookup"><span data-stu-id="3d296-178">Here are the load test results:</span></span>

![使用 Polyglot 控制器的負載測試效能結果][PolyglotScenarioLoadTest]

<span data-ttu-id="3d296-180">輸送量的模式類似於先前的圖表，但是效能尖峰點大約是每秒要求數 500 個以上。</span><span class="sxs-lookup"><span data-stu-id="3d296-180">The pattern of throughput is similar to the earlier graph, but the point at which performance peaks is approximately 500 requests per second higher.</span></span> <span data-ttu-id="3d296-181">平均回應時間稍低。</span><span class="sxs-lookup"><span data-stu-id="3d296-181">The average response time is marginally lower.</span></span> <span data-ttu-id="3d296-182">不過，這些統計資料不會訴說完整的資訊。</span><span class="sxs-lookup"><span data-stu-id="3d296-182">However, these statistics don't tell the full story.</span></span> <span data-ttu-id="3d296-183">商務資料庫的遙測顯示 DTU 使用率尖峰於大約在 75%，而不是 100%。</span><span class="sxs-lookup"><span data-stu-id="3d296-183">Telemetry for the business database shows that DTU utilization peaks at around 75%, rather than 100%.</span></span>

![Azure 傳統入口網站中的資料庫監視會顯示 polyglot 案例中資料庫的資源使用率][PolyglotDatabaseUtilization]

<span data-ttu-id="3d296-185">同樣地，記錄資料庫的最大 DTU 使用率只會達到大約 70%。</span><span class="sxs-lookup"><span data-stu-id="3d296-185">Similarly, the maximum DTU utilization of the log database only reaches about 70%.</span></span> <span data-ttu-id="3d296-186">資料庫不再是系統效能的限制因素。</span><span class="sxs-lookup"><span data-stu-id="3d296-186">The databases are no longer the limiting factor in the performance of the system.</span></span>

![Azure 傳統入口網站中的資料庫監視會顯示 polyglot 案例中記錄資料庫的資源使用率][LogDatabaseUtilization]


## <a name="related-resources"></a><span data-ttu-id="3d296-188">相關資源</span><span class="sxs-lookup"><span data-stu-id="3d296-188">Related resources</span></span>

- <span data-ttu-id="3d296-189">[選擇正確的資料存放區][data-store-overview]</span><span class="sxs-lookup"><span data-stu-id="3d296-189">[Choose the right data store][data-store-overview]</span></span>
- <span data-ttu-id="3d296-190">[選擇資料存放區的準則][data-store-comparison]</span><span class="sxs-lookup"><span data-stu-id="3d296-190">[Criteria for choosing a data store][data-store-comparison]</span></span>
- <span data-ttu-id="3d296-191">[可高度擴充解決方案的資料存取：使用 SQL、NoSQL 和 Polyglot 持續性][Data-Access-Guide]</span><span class="sxs-lookup"><span data-stu-id="3d296-191">[Data Access for Highly-Scalable Solutions: Using SQL, NoSQL, and Polyglot Persistence][Data-Access-Guide]</span></span>
- <span data-ttu-id="3d296-192">[資料分割][DataPartitioningGuidance]</span><span class="sxs-lookup"><span data-stu-id="3d296-192">[Data partitioning][DataPartitioningGuidance]</span></span>

[sample-app]: https://github.com/mspnp/performance-optimization/tree/master/MonolithicPersistence
[CosmosDB]: https://azure.microsoft.com/services/cosmos-db/
[Azure-cache]: /azure/redis-cache/
[Data-Access-Guide]: https://msdn.microsoft.com/library/dn271399.aspx
[DataPartitioningGuidance]: ../../best-practices/data-partitioning.md
[data-store-overview]: ../../guide/technology-choices/data-store-overview.md
[data-store-comparison]: ../../guide/technology-choices/data-store-comparison.md

[MonolithicScenarioLoadTest]: _images/MonolithicScenarioLoadTest.jpg
[MonolithicDatabaseUtilization]: _images/MonolithicDatabaseUtilization.jpg
[MonolithicDataAccessStats]: _images/MonolithicDataAccessStats.jpg
[PolyglotScenarioLoadTest]: _images/PolyglotScenarioLoadTest.jpg
[PolyglotDatabaseUtilization]: _images/PolyglotDatabaseUtilization.jpg
[LogDatabaseUtilization]: _images/LogDatabaseUtilization.jpg

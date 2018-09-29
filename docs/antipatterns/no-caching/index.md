---
title: 沒有快取的反模式
description: 重複擷取相同資料可能會降低效能和延展性。
author: dragon119
ms.date: 06/05/2017
ms.openlocfilehash: f94a9f3f9166e87949a0e60af818cd89796dc3e2
ms.sourcegitcommit: 94d50043db63416c4d00cebe927a0c88f78c3219
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2018
ms.locfileid: "47428938"
---
# <a name="no-caching-antipattern"></a><span data-ttu-id="ac278-103">沒有快取的反模式</span><span class="sxs-lookup"><span data-stu-id="ac278-103">No Caching antipattern</span></span>

<span data-ttu-id="ac278-104">在處理許多並行要求的雲端應用程式中，重複擷取相同資料可能會降低效能和延展性。</span><span class="sxs-lookup"><span data-stu-id="ac278-104">In a cloud application that handles many concurrent requests, repeatedly fetching the same data can reduce performance and scalability.</span></span> 

## <a name="problem-description"></a><span data-ttu-id="ac278-105">問題說明</span><span class="sxs-lookup"><span data-stu-id="ac278-105">Problem description</span></span>

<span data-ttu-id="ac278-106">如果沒有進行資料快取，可能會造成許多非預期行為，包括：</span><span class="sxs-lookup"><span data-stu-id="ac278-106">When data is not cached, it can cause a number of undesirable behaviors, including:</span></span>

- <span data-ttu-id="ac278-107">從存取成本高 (I/O 額外負荷或延遲方面) 的資源中重複擷取相同資訊。</span><span class="sxs-lookup"><span data-stu-id="ac278-107">Repeatedly fetching the same information from a resource that is expensive to access, in terms of I/O overhead or latency.</span></span>
- <span data-ttu-id="ac278-108">重複為多個要求建構相同物件或資料結構。</span><span class="sxs-lookup"><span data-stu-id="ac278-108">Repeatedly constructing the same objects or data structures for multiple requests.</span></span>
- <span data-ttu-id="ac278-109">對有服務配額及會對用戶端進行節流 (如果超過特定限制) 的遠端服務進行過多呼叫。</span><span class="sxs-lookup"><span data-stu-id="ac278-109">Making excessive calls to a remote service that has a service quota and throttles clients past a certain limit.</span></span>

<span data-ttu-id="ac278-110">而這些問題可能會導致回應時間不佳、資料存放區的競爭增加和延展性不佳。</span><span class="sxs-lookup"><span data-stu-id="ac278-110">In turn, these problems can lead to poor response times, increased contention in the data store, and poor scalability.</span></span>

<span data-ttu-id="ac278-111">下列範例會使用 Entity Framework 來連接到資料庫。</span><span class="sxs-lookup"><span data-stu-id="ac278-111">The following example uses Entity Framework to connect to a database.</span></span> <span data-ttu-id="ac278-112">每個用戶端要求都會產生傳送至資料庫的呼叫，即使有多個要求都擷取完全相同的資料。</span><span class="sxs-lookup"><span data-stu-id="ac278-112">Every client request results in a call to the database, even if multiple requests are fetching exactly the same data.</span></span> <span data-ttu-id="ac278-113">重複要求的成本 (在 I/O 額外負荷與資料存取費用方面) 會快速累積。</span><span class="sxs-lookup"><span data-stu-id="ac278-113">The cost of repeated requests, in terms of I/O overhead and data access charges, can accumulate quickly.</span></span>

```csharp
public class PersonRepository : IPersonRepository
{
    public async Task<Person> GetAsync(int id)
    {
        using (var context = new AdventureWorksContext())
        {
            return await context.People
                .Where(p => p.Id == id)
                .FirstOrDefaultAsync()
                .ConfigureAwait(false);
        }
    }
}
```

<span data-ttu-id="ac278-114">您可以在[這裡][sample-app]找到完整的範例。</span><span class="sxs-lookup"><span data-stu-id="ac278-114">You can find the complete sample [here][sample-app].</span></span>

<span data-ttu-id="ac278-115">此反模式會發生通常是因為：</span><span class="sxs-lookup"><span data-stu-id="ac278-115">This antipattern typically occurs because:</span></span>

- <span data-ttu-id="ac278-116">不使用快取會更容易實作，並在低負載下可以正常運作。</span><span class="sxs-lookup"><span data-stu-id="ac278-116">Not using a cache is simpler to implement, and it works fine under low loads.</span></span> <span data-ttu-id="ac278-117">快取會讓程式碼更複雜。</span><span class="sxs-lookup"><span data-stu-id="ac278-117">Caching makes the code more complicated.</span></span> 
- <span data-ttu-id="ac278-118">不是很清楚使用快取的優點和缺點。</span><span class="sxs-lookup"><span data-stu-id="ac278-118">The benefits and drawbacks of using a cache are not clearly understood.</span></span>
- <span data-ttu-id="ac278-119">顧慮到維護快取資料精確度和最新狀態所造成的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="ac278-119">There is concern about the overhead of maintaining the accuracy and freshness of cached data.</span></span>
- <span data-ttu-id="ac278-120">應用程式已從內部部署系統進行移轉，其中網路延遲並不是問題，且系統已在成本高且效能高的硬體上執行，因此快取並不在原始設計的考量中。</span><span class="sxs-lookup"><span data-stu-id="ac278-120">An application was migrated from an on-premises system, where network latency was not an issue, and the system ran on expensive high-performance hardware, so caching wasn't considered in the original design.</span></span>
- <span data-ttu-id="ac278-121">開發人員不清楚將快取用在特定案例中的價值。</span><span class="sxs-lookup"><span data-stu-id="ac278-121">Developers aren't aware that caching is a possibility in a given scenario.</span></span> <span data-ttu-id="ac278-122">例如，開發人員可能不會想到在實作 Web API 時，使用 ETag。</span><span class="sxs-lookup"><span data-stu-id="ac278-122">For example, developers may not think of using ETags when implementing a web API.</span></span>

## <a name="how-to-fix-the-problem"></a><span data-ttu-id="ac278-123">如何修正問題</span><span class="sxs-lookup"><span data-stu-id="ac278-123">How to fix the problem</span></span>

<span data-ttu-id="ac278-124">最常用的快取策略是「隨選」或「另行快取」策略。</span><span class="sxs-lookup"><span data-stu-id="ac278-124">The most popular caching strategy is the *on-demand* or *cache-aside* strategy.</span></span>

- <span data-ttu-id="ac278-125">進行讀取時，應用程式會嘗試從快取讀取資料。</span><span class="sxs-lookup"><span data-stu-id="ac278-125">On read, the application tries to read the data from the cache.</span></span> <span data-ttu-id="ac278-126">如果資料不在快取中，則應用程式會從資料來源擷取資料，並將它新增至快取。</span><span class="sxs-lookup"><span data-stu-id="ac278-126">If the data isn't in the cache, the application retrieves it from the data source and adds it to the cache.</span></span>
- <span data-ttu-id="ac278-127">進行寫入時，應用程式會直接將變更寫入資料來源，並從快取中移除舊的值。</span><span class="sxs-lookup"><span data-stu-id="ac278-127">On write, the application writes the change directly to the data source and removes the old value from the cache.</span></span> <span data-ttu-id="ac278-128">下一次需要這些資料時，資料會被擷取並新增至快取中。</span><span class="sxs-lookup"><span data-stu-id="ac278-128">It will be retrieved and added to the cache the next time it is required.</span></span>

<span data-ttu-id="ac278-129">此方法適用於經常變更的資料。</span><span class="sxs-lookup"><span data-stu-id="ac278-129">This approach is suitable for data that changes frequently.</span></span> <span data-ttu-id="ac278-130">以下是上一個範例的更新，改為使用 [另行快取] 模式。</span><span class="sxs-lookup"><span data-stu-id="ac278-130">Here is the previous example updated to use the [Cache-Aside][cache-aside] pattern.</span></span>  

```csharp
public class CachedPersonRepository : IPersonRepository
{
    private readonly PersonRepository _innerRepository;

    public CachedPersonRepository(PersonRepository innerRepository)
    {
        _innerRepository = innerRepository;
    }

    public async Task<Person> GetAsync(int id)
    {
        return await CacheService.GetAsync<Person>("p:" + id, () => _innerRepository.GetAsync(id)).ConfigureAwait(false);
    }
}

public class CacheService
{
    private static ConnectionMultiplexer _connection;

    public static async Task<T> GetAsync<T>(string key, Func<Task<T>> loadCache, double expirationTimeInMinutes)
    {
        IDatabase cache = Connection.GetDatabase();
        T value = await GetAsync<T>(cache, key).ConfigureAwait(false);
        if (value == null)
        {
            // Value was not found in the cache. Call the lambda to get the value from the database.
            value = await loadCache().ConfigureAwait(false);
            if (value != null)
            {
                // Add the value to the cache.
                await SetAsync(cache, key, value, expirationTimeInMinutes).ConfigureAwait(false);
            }
        }
        return value;
    }
}
```

<span data-ttu-id="ac278-131">請注意，`GetAsync` 方法現在會呼叫`CacheService` 類別，而不是直接呼叫資料庫。</span><span class="sxs-lookup"><span data-stu-id="ac278-131">Notice that the `GetAsync` method now calls the `CacheService` class, rather than calling the database directly.</span></span> <span data-ttu-id="ac278-132">`CacheService` 類別會先嘗試從 Azure Redis 快取中取得項目。</span><span class="sxs-lookup"><span data-stu-id="ac278-132">The `CacheService` class first tries to get the item from Azure Redis Cache.</span></span> <span data-ttu-id="ac278-133">如果 Redis 快取中找不到該值，則 `CacheService` 會叫用由呼叫端傳遞給它的匿名函式。</span><span class="sxs-lookup"><span data-stu-id="ac278-133">If the value isn't found in Redis Cache, the `CacheService` invokes a lambda function that was passed to it by the caller.</span></span> <span data-ttu-id="ac278-134">匿名函式會負責從資料庫擷取資料。</span><span class="sxs-lookup"><span data-stu-id="ac278-134">The lambda function is responsible for fetching the data from the database.</span></span> <span data-ttu-id="ac278-135">這項實作會將存放庫從特定快取解決方案中分離，以及從資料庫中分離 `CacheService`。</span><span class="sxs-lookup"><span data-stu-id="ac278-135">This implementation decouples the repository from the particular caching solution, and decouples the `CacheService` from the database.</span></span> 

## <a name="considerations"></a><span data-ttu-id="ac278-136">考量</span><span class="sxs-lookup"><span data-stu-id="ac278-136">Considerations</span></span>

- <span data-ttu-id="ac278-137">如果無法使用快取，可能是因為暫時性錯誤，請勿將錯誤傳回用戶端。</span><span class="sxs-lookup"><span data-stu-id="ac278-137">If the cache is unavailable, perhaps because of a transient failure, don't return an error to the client.</span></span> <span data-ttu-id="ac278-138">相反地，請從原始資料來源擷取資料。</span><span class="sxs-lookup"><span data-stu-id="ac278-138">Instead, fetch the data from the original data source.</span></span> <span data-ttu-id="ac278-139">但是，請注意，當快取正在復原時，原始資料存放區可能忙於處理要求，因而導致逾時和連線失敗。</span><span class="sxs-lookup"><span data-stu-id="ac278-139">However, be aware that while the cache is being recovered, the original data store could be swamped with requests, resulting in timeouts and failed connections.</span></span> <span data-ttu-id="ac278-140">(畢竟，這是一個優先使用快取的動機。)使用[斷路器模式][circuit-breaker]等技術可避免資料來源癱瘓。</span><span class="sxs-lookup"><span data-stu-id="ac278-140">(After all, this is one of the motivations for using a cache in the first place.) Use a technique such as the [Circuit Breaker pattern][circuit-breaker] to avoid overwhelming the data source.</span></span>

- <span data-ttu-id="ac278-141">快取非靜態資料的應用程式應該設計成支援最終一致性。</span><span class="sxs-lookup"><span data-stu-id="ac278-141">Applications that cache nonstatic data should be designed to support eventual consistency.</span></span>

- <span data-ttu-id="ac278-142">針對 Web API，您可以透過在要求和回應訊息中包含快取控制標頭，以及使用 ETag 識別物件版本，來支援用戶端快取。</span><span class="sxs-lookup"><span data-stu-id="ac278-142">For web APIs, you can support client-side caching by including a Cache-Control header in request and response messages, and using ETags to identify versions of objects.</span></span> <span data-ttu-id="ac278-143">如需詳細資訊，請參閱 [API 實作][api-implementation]。</span><span class="sxs-lookup"><span data-stu-id="ac278-143">For more information, see [API implementation][api-implementation].</span></span>

- <span data-ttu-id="ac278-144">您不需要快取整個實體。</span><span class="sxs-lookup"><span data-stu-id="ac278-144">You don't have to cache entire entities.</span></span> <span data-ttu-id="ac278-145">如果大部分的實體是靜態，只有小部分會頻繁地變更，則快取靜態項目，並從資料來源擷取動態項目。</span><span class="sxs-lookup"><span data-stu-id="ac278-145">If most of an entity is static but only a small piece changes frequently, cache the static elements and retrieve the dynamic elements from the data source.</span></span> <span data-ttu-id="ac278-146">此方法有助於減少針對資料來源執行的 I/O 數量。</span><span class="sxs-lookup"><span data-stu-id="ac278-146">This approach can help to reduce the volume of I/O being performed against the data source.</span></span>

- <span data-ttu-id="ac278-147">在某些情況下，如果變動性資料是短期的，則快取該資料可能十分實用。</span><span class="sxs-lookup"><span data-stu-id="ac278-147">In some cases, if volatile data is short-lived, it can be useful to cache it.</span></span> <span data-ttu-id="ac278-148">例如，試想持續傳送狀態更新的裝置。</span><span class="sxs-lookup"><span data-stu-id="ac278-148">For example, consider a device that continually sends status updates.</span></span> <span data-ttu-id="ac278-149">這就很適合在此資訊到達時就加以快取，根本無須將該資料寫入永久存放區。</span><span class="sxs-lookup"><span data-stu-id="ac278-149">It might make sense to cache this information as it arrives, and not write it to a persistent store at all.</span></span>  

- <span data-ttu-id="ac278-150">為防止資料過時，許多快取解決方案都可支援設定到期日，因此在指定的間隔之後，資料就會自動從快取中移除。</span><span class="sxs-lookup"><span data-stu-id="ac278-150">To prevent data from becoming stale, many caching solutions support configurable expiration periods, so that data is automatically removed from the cache after a specified interval.</span></span> <span data-ttu-id="ac278-151">您可能需要根據您的情況調整到期時間。</span><span class="sxs-lookup"><span data-stu-id="ac278-151">You may need to tune the expiration time for your scenario.</span></span> <span data-ttu-id="ac278-152">比起可能會快速過期的變動性資料，高度靜態的資料可以在快取中停留較長時間。</span><span class="sxs-lookup"><span data-stu-id="ac278-152">Data that is highly static can stay in the cache for longer periods than volatile data that may become stale quickly.</span></span>

- <span data-ttu-id="ac278-153">如果快取解決方案未提供內建到期日，您可能需要實作會偶爾清除快取的背景處理程序，以避免快取資料無限成長。</span><span class="sxs-lookup"><span data-stu-id="ac278-153">If the caching solution doesn't provide built-in expiration, you may need to implement a background process that occasionally sweeps the cache, to prevent it from growing without limits.</span></span> 

- <span data-ttu-id="ac278-154">除了從外部資料來源快取資料，您也可以使用快取來儲存複雜的計算結果。</span><span class="sxs-lookup"><span data-stu-id="ac278-154">Besides caching data from an external data source, you can use caching to save the results of complex computations.</span></span> <span data-ttu-id="ac278-155">但是在您這麼做之前，請檢測應用程式以判斷應用程式是否真的是與 CPU 繫結。</span><span class="sxs-lookup"><span data-stu-id="ac278-155">Before you do that, however, instrument the application to determine whether the application is really CPU bound.</span></span>

- <span data-ttu-id="ac278-156">在應用程式啟動時就準備好快取可能會有所幫助。</span><span class="sxs-lookup"><span data-stu-id="ac278-156">It might be useful to prime the cache when the application starts.</span></span> <span data-ttu-id="ac278-157">請將最可能使用的資料填入快取。</span><span class="sxs-lookup"><span data-stu-id="ac278-157">Populate the cache with the data that is most likely to be used.</span></span>

- <span data-ttu-id="ac278-158">請務必包含偵測快取命中和快取遺漏的檢測設備。</span><span class="sxs-lookup"><span data-stu-id="ac278-158">Always include instrumentation that detects cache hits and cache misses.</span></span> <span data-ttu-id="ac278-159">您可以使用此資訊來調整快取原則，例如哪些資料要快取，以及資料要在快取中保留多久才算過期。</span><span class="sxs-lookup"><span data-stu-id="ac278-159">Use this information to tune caching policies, such what data to cache, and how long to hold data in the cache before it expires.</span></span>

- <span data-ttu-id="ac278-160">如果缺少快取是瓶頸，則新增快取可能會增加要求數量，甚至使 Web 前端負載過重。</span><span class="sxs-lookup"><span data-stu-id="ac278-160">If the lack of caching is a bottleneck, then adding caching may increase the volume of requests so much that the web front end becomes overloaded.</span></span> <span data-ttu-id="ac278-161">用戶端可能會開始收到 HTTP 503 (服務無法使用) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="ac278-161">Clients may start to receive HTTP 503 (Service Unavailable) errors.</span></span> <span data-ttu-id="ac278-162">這些都表示您應該相應放大前端。</span><span class="sxs-lookup"><span data-stu-id="ac278-162">These are an indication that you should scale out the front end.</span></span>

## <a name="how-to-detect-the-problem"></a><span data-ttu-id="ac278-163">如何偵測問題</span><span class="sxs-lookup"><span data-stu-id="ac278-163">How to detect the problem</span></span>

<span data-ttu-id="ac278-164">您可以執行下列步驟，以協助識別缺少快取是否會造成效能問題：</span><span class="sxs-lookup"><span data-stu-id="ac278-164">You can perform the following steps to help identify whether lack of caching is causing performance problems:</span></span>

1. <span data-ttu-id="ac278-165">檢閱應用程式程式設計。</span><span class="sxs-lookup"><span data-stu-id="ac278-165">Review the application design.</span></span> <span data-ttu-id="ac278-166">清查應用程式使用的所有資料存放區。</span><span class="sxs-lookup"><span data-stu-id="ac278-166">Take an inventory of all the data stores that the application uses.</span></span> <span data-ttu-id="ac278-167">針對每個資料存放區，判斷應用程式是否使用快取。</span><span class="sxs-lookup"><span data-stu-id="ac278-167">For each, determine whether the application is using a cache.</span></span> <span data-ttu-id="ac278-168">可能的話，判斷資料變更的頻率。</span><span class="sxs-lookup"><span data-stu-id="ac278-168">If possible, determine how frequently the data changes.</span></span> <span data-ttu-id="ac278-169">一開始，理想的快取候選項目包含變更緩慢的資料和經常讀取的靜態參考資料。</span><span class="sxs-lookup"><span data-stu-id="ac278-169">Good initial candidates for caching include data that changes slowly, and static reference data that is read frequently.</span></span> 

2. <span data-ttu-id="ac278-170">檢測應用程式和監視即時系統，以便找出應用程式擷取資料或計算資訊的頻率。</span><span class="sxs-lookup"><span data-stu-id="ac278-170">Instrument the application and monitor the live system to find out how frequently the application retrieves data or calculates information.</span></span>

3. <span data-ttu-id="ac278-171">在測試環境中分析應用程式，以針對資料存取作業或其他經常執行的計算所造成的額外負荷，擷取相關的低層級度量。</span><span class="sxs-lookup"><span data-stu-id="ac278-171">Profile the application in a test environment to capture low-level metrics about the overhead associated with data access operations or other frequently performed calculations.</span></span>

4. <span data-ttu-id="ac278-172">在測試環境中執行負載測試，以識別系統在工作負載正常和負載過重時如何回應。</span><span class="sxs-lookup"><span data-stu-id="ac278-172">Perform load testing in a test environment to identify how the system responds under a normal workload and under heavy load.</span></span> <span data-ttu-id="ac278-173">負載測試應使用實際工作負載，來模擬在生產環境中觀察到的資料存取模式。</span><span class="sxs-lookup"><span data-stu-id="ac278-173">Load testing should simulate the pattern of data access observed in the production environment using realistic workloads.</span></span> 

5. <span data-ttu-id="ac278-174">檢查基礎資料存放區的資料存取統計資料，並檢閱重複相同資料要求的頻率。</span><span class="sxs-lookup"><span data-stu-id="ac278-174">Examine the data access statistics for the underlying data stores and review how often the same data requests are repeated.</span></span> 


## <a name="example-diagnosis"></a><span data-ttu-id="ac278-175">範例診斷</span><span class="sxs-lookup"><span data-stu-id="ac278-175">Example diagnosis</span></span>

<span data-ttu-id="ac278-176">下列各節會將這些步驟套用到稍早所述的範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="ac278-176">The following sections apply these steps to the sample application described earlier.</span></span>

### <a name="instrument-the-application-and-monitor-the-live-system"></a><span data-ttu-id="ac278-177">檢測應用程式和監視即時系統</span><span class="sxs-lookup"><span data-stu-id="ac278-177">Instrument the application and monitor the live system</span></span>

<span data-ttu-id="ac278-178">檢測應用程式並進行監視，以取得使用者在生產環境中所做的特定要求相關資訊。</span><span class="sxs-lookup"><span data-stu-id="ac278-178">Instrument the application and monitor it to get information about the specific requests that users make while the application is in production.</span></span> 

<span data-ttu-id="ac278-179">下圖顯示 [New Relic][NewRelic] 在負載測試期間擷取的監視資料。</span><span class="sxs-lookup"><span data-stu-id="ac278-179">The following image shows monitoring data captured by [New Relic][NewRelic] during a load test.</span></span> <span data-ttu-id="ac278-180">在此情況下，唯一執行的 HTTP GET 作業是 `Person/GetAsync`。</span><span class="sxs-lookup"><span data-stu-id="ac278-180">In this case, the only HTTP GET operation performed is `Person/GetAsync`.</span></span> <span data-ttu-id="ac278-181">但在實際生產環境中，了解每個要求執行的相對頻率，可以讓您深入了解應該要快取哪些資源。</span><span class="sxs-lookup"><span data-stu-id="ac278-181">But in a live production environment, knowing the relative frequency that each request is performed can give you insight into which resources should be cached.</span></span>

![New Relic 顯示 CachingDemo 應用程式的伺服器要求][NewRelic-server-requests]

<span data-ttu-id="ac278-183">如果您需要更深入的分析，您可以使用分析工具，來擷取測試環境中 (不是生產系統) 的低層級效能資料。</span><span class="sxs-lookup"><span data-stu-id="ac278-183">If you need a deeper analysis, you can use a profiler to capture low-level performance data in a test environment (not the production system).</span></span> <span data-ttu-id="ac278-184">觀看 I/O 要求比率、記憶體使用量和 CPU 使用率等。</span><span class="sxs-lookup"><span data-stu-id="ac278-184">Look at metrics such as I/O request rates, memory usage, and CPU utilization.</span></span> <span data-ttu-id="ac278-185">這些度量資訊可能會顯示大量的資料存放區或服務要求，或是執行相同計算的重複處理程序。</span><span class="sxs-lookup"><span data-stu-id="ac278-185">These metrics may show a large number of requests to a data store or service, or repeated processing that performs the same calculation.</span></span> 

### <a name="load-test-the-application"></a><span data-ttu-id="ac278-186">對應用程式進行負載測試</span><span class="sxs-lookup"><span data-stu-id="ac278-186">Load test the application</span></span>

<span data-ttu-id="ac278-187">下圖顯示針對範例應用程式進行負載測試的結果。</span><span class="sxs-lookup"><span data-stu-id="ac278-187">The following graph shows the results of load testing the sample application.</span></span> <span data-ttu-id="ac278-188">負載測試會模擬多達 800 位使用者執行一系列典型作業的步驟負載。</span><span class="sxs-lookup"><span data-stu-id="ac278-188">The load test simulates a step load of up to 800 users performing a typical series of operations.</span></span> 

![未快取案例的效能負載測試結果][Performance-Load-Test-Results-Uncached]

<span data-ttu-id="ac278-190">每秒中成功執行的測試數目到達平穩，而其他要求會因此變慢。</span><span class="sxs-lookup"><span data-stu-id="ac278-190">The number of successful tests performed each second reaches a plateau, and additional requests are slowed as a result.</span></span> <span data-ttu-id="ac278-191">平均測試時間會隨著工作負載穩定地增加。</span><span class="sxs-lookup"><span data-stu-id="ac278-191">The average test time steadily increases with the workload.</span></span> <span data-ttu-id="ac278-192">一旦使用者負載達到尖峰，回應時間會趨於平整。</span><span class="sxs-lookup"><span data-stu-id="ac278-192">The response time levels off once the user load peaks.</span></span>

### <a name="examine-data-access-statistics"></a><span data-ttu-id="ac278-193">檢查資料存取統計資料</span><span class="sxs-lookup"><span data-stu-id="ac278-193">Examine data access statistics</span></span>

<span data-ttu-id="ac278-194">資料存放區提供的資料存取統計資料和其他資訊，可帶來十分有用的資訊，例如哪些查詢最常重複。</span><span class="sxs-lookup"><span data-stu-id="ac278-194">Data access statistics and other information provided by a data store can give useful information, such as which queries are repeated most frequently.</span></span> <span data-ttu-id="ac278-195">例如，在 Microsoft SQL Server 中，`sys.dm_exec_query_stats` 管理檢視會有最近執行之查詢的統計資訊。</span><span class="sxs-lookup"><span data-stu-id="ac278-195">For example, in Microsoft SQL Server, the `sys.dm_exec_query_stats` management view has statistical information for recently executed queries.</span></span> <span data-ttu-id="ac278-196">每個查詢的文字皆可用於 `sys.dm_exec-query_plan` 檢視。</span><span class="sxs-lookup"><span data-stu-id="ac278-196">The text for each query is available in the `sys.dm_exec-query_plan` view.</span></span> <span data-ttu-id="ac278-197">您可以使用 SQL Server Management Studio 等工具來執行下列 SQL 查詢，並決定執行查詢的頻率。</span><span class="sxs-lookup"><span data-stu-id="ac278-197">You can use a tool such as SQL Server Management Studio to run the following SQL query and determine how frequently queries are performed.</span></span>

```SQL
SELECT UseCounts, Text, Query_Plan
FROM sys.dm_exec_cached_plans
CROSS APPLY sys.dm_exec_sql_text(plan_handle)
CROSS APPLY sys.dm_exec_query_plan(plan_handle)
```

<span data-ttu-id="ac278-198">結果中的 `UseCount` 資料行會指出每個查詢的執行頻率。</span><span class="sxs-lookup"><span data-stu-id="ac278-198">The `UseCount` column in the results indicates how frequently each query is run.</span></span> <span data-ttu-id="ac278-199">下圖顯示第三個查詢執行了 250,000 次以上，大幅超過任何其他查詢。</span><span class="sxs-lookup"><span data-stu-id="ac278-199">The following image shows that the third query was run more than 250,000 times, significantly more than any other query.</span></span>

![在 SQL Server 管理伺服器中查詢動態管理檢視的結果][Dynamic-Management-Views]

<span data-ttu-id="ac278-201">以下是造成此資料庫要求數量的 SQL 查詢：</span><span class="sxs-lookup"><span data-stu-id="ac278-201">Here is the SQL query that is causing so many database requests:</span></span> 

```SQL
(@p__linq__0 int)SELECT TOP (2)
[Extent1].[BusinessEntityId] AS [BusinessEntityId],
[Extent1].[FirstName] AS [FirstName],
[Extent1].[LastName] AS [LastName]
FROM [Person].[Person] AS [Extent1]
WHERE [Extent1].[BusinessEntityId] = @p__linq__0
```

<span data-ttu-id="ac278-202">這是 Entity Framework 在稍早所示的 `GetByIdAsync` 方法中產生的查詢。</span><span class="sxs-lookup"><span data-stu-id="ac278-202">This is the query that Entity Framework generates in `GetByIdAsync` method shown earlier.</span></span>

### <a name="implement-the-solution-and-verify-the-result"></a><span data-ttu-id="ac278-203">實作解決方案並確認結果</span><span class="sxs-lookup"><span data-stu-id="ac278-203">Implement the solution and verify the result</span></span>

<span data-ttu-id="ac278-204">當您加入快取之後，重複負載測試，並與沒有快取的稍早負載測試結果進行比較。</span><span class="sxs-lookup"><span data-stu-id="ac278-204">After you incorporate a cache, repeat the load tests and compare the results to the earlier load tests without a cache.</span></span> <span data-ttu-id="ac278-205">以下是將快取加入至範例應用程式之後，產生的負載測試結果。</span><span class="sxs-lookup"><span data-stu-id="ac278-205">Here are the load test results after adding a cache to the sample application.</span></span>

![快取案例的效能負載測試結果][Performance-Load-Test-Results-Cached]

<span data-ttu-id="ac278-207">成功的測試數量仍到達平穩狀態，但是使用者負載量較高。</span><span class="sxs-lookup"><span data-stu-id="ac278-207">The volume of successful tests still reaches a plateau, but at a higher user load.</span></span> <span data-ttu-id="ac278-208">此負載的要求比率遠大於稍早版本。</span><span class="sxs-lookup"><span data-stu-id="ac278-208">The request rate at this load is significantly higher than earlier.</span></span> <span data-ttu-id="ac278-209">平均測試時間仍會隨著負載增加，但最大回應時間為 0.05 毫秒，相較於之前的 1 毫秒，已改善 &mdash;20&times; 倍。</span><span class="sxs-lookup"><span data-stu-id="ac278-209">Average test time still increases with load, but the maximum response time is 0.05 ms, compared with 1ms earlier &mdash; a 20&times; improvement.</span></span> 

## <a name="related-resources"></a><span data-ttu-id="ac278-210">相關資源</span><span class="sxs-lookup"><span data-stu-id="ac278-210">Related resources</span></span>

- <span data-ttu-id="ac278-211">[API 實作最佳作法][api-implementation]</span><span class="sxs-lookup"><span data-stu-id="ac278-211">[API implementation best practices][api-implementation]</span></span>
- <span data-ttu-id="ac278-212">[另行快取模式][cache-aside-pattern]</span><span class="sxs-lookup"><span data-stu-id="ac278-212">[Cache-Aside Pattern][cache-aside-pattern]</span></span>
- <span data-ttu-id="ac278-213">[快取最佳做法][caching-guidance]</span><span class="sxs-lookup"><span data-stu-id="ac278-213">[Caching best practices][caching-guidance]</span></span>
- <span data-ttu-id="ac278-214">[斷路器模式][circuit-breaker]</span><span class="sxs-lookup"><span data-stu-id="ac278-214">[Circuit Breaker pattern][circuit-breaker]</span></span>

[sample-app]: https://github.com/mspnp/performance-optimization/tree/master/NoCaching
[cache-aside-pattern]: /azure/architecture/patterns/cache-aside
[caching-guidance]: ../../best-practices/caching.md
[circuit-breaker]: ../../patterns/circuit-breaker.md
[api-implementation]: ../../best-practices/api-implementation.md#optimizing-client-side-data-access
[NewRelic]: https://newrelic.com/partner/azure
[NewRelic-server-requests]: _images/New-Relic.jpg
[Performance-Load-Test-Results-Uncached]:_images/InitialLoadTestResults.jpg
[Dynamic-Management-Views]: _images/SQLServerManagementStudio.jpg
[Performance-Load-Test-Results-Cached]: _images/CachedLoadTestResults.jpg
---
title: 沒有直接關聯的擷取反模式
titleSuffix: Performance antipatterns for cloud apps
description: 擷取超過商務作業所需的資料，會導致不必要的 I/O 額外負荷並且降低回應能力。
author: dragon119
ms.date: 06/05/2017
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: c1172531b332854a6d4940c072b61cb3f6bcd7ba
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54488183"
---
# <a name="extraneous-fetching-antipattern"></a><span data-ttu-id="1a61b-103">沒有直接關聯的擷取反模式</span><span class="sxs-lookup"><span data-stu-id="1a61b-103">Extraneous Fetching antipattern</span></span>

<span data-ttu-id="1a61b-104">擷取超過商務作業所需的資料，會導致不必要的 I/O 額外負荷並且降低回應能力。</span><span class="sxs-lookup"><span data-stu-id="1a61b-104">Retrieving more data than needed for a business operation can result in unnecessary I/O overhead and reduce responsiveness.</span></span>

## <a name="problem-description"></a><span data-ttu-id="1a61b-105">問題說明</span><span class="sxs-lookup"><span data-stu-id="1a61b-105">Problem description</span></span>

<span data-ttu-id="1a61b-106">如果應用程式藉由擷取「可能」需要的所有資料，嘗試將 I/O 要求減至最少，可能會發生此反模式。</span><span class="sxs-lookup"><span data-stu-id="1a61b-106">This antipattern can occur if the application tries to minimize I/O requests by retrieving all of the data that it *might* need.</span></span> <span data-ttu-id="1a61b-107">這通常是過度補償[多對話 I/O][chatty-io] 反模式的結果。</span><span class="sxs-lookup"><span data-stu-id="1a61b-107">This is often a result of overcompensating for the [Chatty I/O][chatty-io] antipattern.</span></span> <span data-ttu-id="1a61b-108">例如，應用程式可能會擷取資料庫中每個產品的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="1a61b-108">For example, an application might fetch the details for every product in a database.</span></span> <span data-ttu-id="1a61b-109">但是使用者可能只需要詳細資料的子集 (有些可能與客戶不相關)，而且可能不需要一次查看「所有」產品。</span><span class="sxs-lookup"><span data-stu-id="1a61b-109">But the user may need just a subset of the details (some may not be relevant to customers), and probably doesn't need to see *all* of the products at once.</span></span> <span data-ttu-id="1a61b-110">即使使用者瀏覽整個目錄，但是將結果編頁比較有道理 &mdash; 例如一次顯示 20 個項目。</span><span class="sxs-lookup"><span data-stu-id="1a61b-110">Even if the user is browsing the entire catalog, it would make sense to paginate the results &mdash; showing 20 at a time, for example.</span></span>

<span data-ttu-id="1a61b-111">這個問題的另一個來源是下列不良的程式設計或設計做法。</span><span class="sxs-lookup"><span data-stu-id="1a61b-111">Another source of this problem is following poor programming or design practices.</span></span> <span data-ttu-id="1a61b-112">例如，下列程式碼會使用 Entity Framework 擷取每個產品的完整詳細資料。</span><span class="sxs-lookup"><span data-stu-id="1a61b-112">For example, the following code uses Entity Framework to fetch the complete details for every product.</span></span> <span data-ttu-id="1a61b-113">然後它會篩選結果，僅傳回欄位的子集，並且捨棄其餘欄位。</span><span class="sxs-lookup"><span data-stu-id="1a61b-113">Then it filters the results to return only a subset of the fields, discarding the rest.</span></span> <span data-ttu-id="1a61b-114">您可以在[這裡][sample-app]找到完整的範例。</span><span class="sxs-lookup"><span data-stu-id="1a61b-114">You can find the complete sample [here][sample-app].</span></span>

```csharp
public async Task<IHttpActionResult> GetAllFieldsAsync()
{
    using (var context = new AdventureWorksContext())
    {
        // Execute the query. This happens at the database.
        var products = await context.Products.ToListAsync();

        // Project fields from the query results. This happens in application memory.
        var result = products.Select(p => new ProductInfo { Id = p.ProductId, Name = p.Name });
        return Ok(result);
    }
}
```

<span data-ttu-id="1a61b-115">在下一個範例中，應用程式會擷取資料，以執行無法由資料庫完成的彙總。</span><span class="sxs-lookup"><span data-stu-id="1a61b-115">In the next example, the application retrieves data to perform an aggregation that could be done by the database instead.</span></span> <span data-ttu-id="1a61b-116">應用程式會取得所有銷售訂單的每筆記錄，然後計算這些記錄的總和，來計算總銷售額。</span><span class="sxs-lookup"><span data-stu-id="1a61b-116">The application calculates total sales by getting every record for all orders sold, and then computing the sum over those records.</span></span> <span data-ttu-id="1a61b-117">您可以在[這裡][sample-app]找到完整的範例。</span><span class="sxs-lookup"><span data-stu-id="1a61b-117">You can find the complete sample [here][sample-app].</span></span>

```csharp
public async Task<IHttpActionResult> AggregateOnClientAsync()
{
    using (var context = new AdventureWorksContext())
    {
        // Fetch all order totals from the database.
        var orderAmounts = await context.SalesOrderHeaders.Select(soh => soh.TotalDue).ToListAsync();

        // Sum the order totals in memory.
        var total = orderAmounts.Sum();
        return Ok(total);
    }
}
```

<span data-ttu-id="1a61b-118">下一個範例示範 Entity Framework 使用 LINQ to Entities 所造成的輕微問題。</span><span class="sxs-lookup"><span data-stu-id="1a61b-118">The next example shows a subtle problem caused by the way Entity Framework uses LINQ to Entities.</span></span>

```csharp
var query = from p in context.Products.AsEnumerable()
            where p.SellStartDate < DateTime.Now.AddDays(-7) // AddDays cannot be mapped by LINQ to Entities
            select ...;

List<Product> products = query.ToList();
```

<span data-ttu-id="1a61b-119">應用程式嘗試尋找 `SellStartDate` 超過一週的產品。</span><span class="sxs-lookup"><span data-stu-id="1a61b-119">The application is trying to find products with a `SellStartDate` more than a week old.</span></span> <span data-ttu-id="1a61b-120">在大部分情況下，LINQ to Entities 會將 `where` 子句轉譯為資料庫執行的 SQL 陳述式。</span><span class="sxs-lookup"><span data-stu-id="1a61b-120">In most cases, LINQ to Entities would translate a `where` clause to a SQL statement that is executed by the database.</span></span> <span data-ttu-id="1a61b-121">不過在此情況下，LINQ to Entities 無法將 `AddDays` 方法對應至 SQL。</span><span class="sxs-lookup"><span data-stu-id="1a61b-121">In this case, however, LINQ to Entities cannot map the `AddDays` method to SQL.</span></span> <span data-ttu-id="1a61b-122">相反地，會傳回 `Product` 資料表的每個資料列，並且在記憶體中篩選結果。</span><span class="sxs-lookup"><span data-stu-id="1a61b-122">Instead, every row from the `Product` table is returned, and the results are filtered in memory.</span></span>

<span data-ttu-id="1a61b-123">對 `AsEnumerable` 的呼叫就是有問題的提示。</span><span class="sxs-lookup"><span data-stu-id="1a61b-123">The call to `AsEnumerable` is a hint that there is a problem.</span></span> <span data-ttu-id="1a61b-124">這個方法會將結果轉換至 `IEnumerable` 介面。</span><span class="sxs-lookup"><span data-stu-id="1a61b-124">This method converts the results to an `IEnumerable` interface.</span></span> <span data-ttu-id="1a61b-125">雖然 `IEnumerable` 支援篩選，但是篩選是在「用戶端」完成，而不是資料庫。</span><span class="sxs-lookup"><span data-stu-id="1a61b-125">Although `IEnumerable` supports filtering, the filtering is done on the *client* side, not the database.</span></span> <span data-ttu-id="1a61b-126">根據預設，LINQ to Entities 會使用 `IQueryable`，它會將篩選的責任傳送給資料來源。</span><span class="sxs-lookup"><span data-stu-id="1a61b-126">By default, LINQ to Entities uses `IQueryable`, which passes the responsibility for filtering to the data source.</span></span>

## <a name="how-to-fix-the-problem"></a><span data-ttu-id="1a61b-127">如何修正問題</span><span class="sxs-lookup"><span data-stu-id="1a61b-127">How to fix the problem</span></span>

<span data-ttu-id="1a61b-128">避免擷取很快就過時或被捨棄的大量資料，而且只擷取執行之作業所需的資料。</span><span class="sxs-lookup"><span data-stu-id="1a61b-128">Avoid fetching large volumes of data that may quickly become outdated or might be discarded, and only fetch the data needed for the operation being performed.</span></span>

<span data-ttu-id="1a61b-129">並非從資料表取得每個資料行然後篩選它們，而是從資料庫選取您需要的資料行。</span><span class="sxs-lookup"><span data-stu-id="1a61b-129">Instead of getting every column from a table and then filtering them, select the columns that you need from the database.</span></span>

```csharp
public async Task<IHttpActionResult> GetRequiredFieldsAsync()
{
    using (var context = new AdventureWorksContext())
    {
        // Project fields as part of the query itself
        var result = await context.Products
            .Select(p => new ProductInfo {Id = p.ProductId, Name = p.Name})
            .ToListAsync();
        return Ok(result);
    }
}
```

<span data-ttu-id="1a61b-130">同樣地，在資料庫中執行彙總，而不是在應用程式記憶體中。</span><span class="sxs-lookup"><span data-stu-id="1a61b-130">Similarly, perform aggregation in the database and not in application memory.</span></span>

```csharp
public async Task<IHttpActionResult> AggregateOnDatabaseAsync()
{
    using (var context = new AdventureWorksContext())
    {
        // Sum the order totals as part of the database query.
        var total = await context.SalesOrderHeaders.SumAsync(soh => soh.TotalDue);
        return Ok(total);
    }
}
```

<span data-ttu-id="1a61b-131">當使用 Entity Framework 時，請確定使用 `IQueryable` 介面而非 `IEnumerable` 介面來解析 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="1a61b-131">When using Entity Framework, ensure that LINQ queries are resolved using the `IQueryable`interface and not `IEnumerable`.</span></span> <span data-ttu-id="1a61b-132">您可能需要調整查詢，僅使用可以對應至資料來源的函式。</span><span class="sxs-lookup"><span data-stu-id="1a61b-132">You may need to adjust the query to use only functions that can be mapped to the data source.</span></span> <span data-ttu-id="1a61b-133">先前的範例可以重構，以從查詢移除 `AddDays`，讓篩選由資料庫完成。</span><span class="sxs-lookup"><span data-stu-id="1a61b-133">The earlier example can be refactored to remove the `AddDays` method from the query, allowing filtering to be done by the database.</span></span>

```csharp
DateTime dateSince = DateTime.Now.AddDays(-7); // AddDays has been factored out.
var query = from p in context.Products
            where p.SellStartDate < dateSince // This criterion can be passed to the database by LINQ to Entities
            select ...;

List<Product> products = query.ToList();
```

## <a name="considerations"></a><span data-ttu-id="1a61b-134">考量</span><span class="sxs-lookup"><span data-stu-id="1a61b-134">Considerations</span></span>

- <span data-ttu-id="1a61b-135">在某些情況下，您可以藉由水平分割資料來改善效能。</span><span class="sxs-lookup"><span data-stu-id="1a61b-135">In some cases, you can improve performance by partitioning data horizontally.</span></span> <span data-ttu-id="1a61b-136">如果不同的作業會存取資料的不同屬性，水平資料分割可能會減少爭用。</span><span class="sxs-lookup"><span data-stu-id="1a61b-136">If different operations access different attributes of the data, horizontal partitioning may reduce contention.</span></span> <span data-ttu-id="1a61b-137">通常，大部分作業是針對資料的小子集來執行，因此分散此負載可能會改善效能。</span><span class="sxs-lookup"><span data-stu-id="1a61b-137">Often, most operations are run against a small subset of the data, so spreading this load may improve performance.</span></span> <span data-ttu-id="1a61b-138">請參閱[資料分割][data-partitioning]。</span><span class="sxs-lookup"><span data-stu-id="1a61b-138">See [Data partitioning][data-partitioning].</span></span>

- <span data-ttu-id="1a61b-139">對於必須支援無限制查詢的作業，實作編頁並且一次僅擷取有限數目的實體。</span><span class="sxs-lookup"><span data-stu-id="1a61b-139">For operations that have to support unbounded queries, implement pagination and only fetch a limited number of entities at a time.</span></span> <span data-ttu-id="1a61b-140">例如，如果客戶瀏覽產品目錄，您可以一次顯示一頁的結果。</span><span class="sxs-lookup"><span data-stu-id="1a61b-140">For example, if a customer is browsing a product catalog, you can show one page of results at a time.</span></span>

- <span data-ttu-id="1a61b-141">盡可能利用資料存放區的內建功能。</span><span class="sxs-lookup"><span data-stu-id="1a61b-141">When possible, take advantage of features built into the data store.</span></span> <span data-ttu-id="1a61b-142">例如，SQL 資料庫通常會提供彙總函式。</span><span class="sxs-lookup"><span data-stu-id="1a61b-142">For example, SQL databases typically provide aggregate functions.</span></span>

- <span data-ttu-id="1a61b-143">如果您使用不支援特定函式 (例如彙總) 的資料存放區，您可以將計算的結果儲存在其他位置，當新增或更新記錄時更新值，讓應用程式不需要每次重新計算值。</span><span class="sxs-lookup"><span data-stu-id="1a61b-143">If you're using a data store that doesn't support a particular function, such as aggregration, you could store the calculated result elsewhere, updating the value as records are added or updated, so the application doesn't have to recalculate the value each time it's needed.</span></span>

- <span data-ttu-id="1a61b-144">如果您看到要求擷取大量的欄位，檢查原始程式碼以判斷這些欄位實際上是否都有需要。</span><span class="sxs-lookup"><span data-stu-id="1a61b-144">If you see that requests are retrieving a large number of fields, examine the source code to determine whether all of these fields are actually necessary.</span></span> <span data-ttu-id="1a61b-145">有時候這些要求是不良設計 `SELECT *` 查詢的結果。</span><span class="sxs-lookup"><span data-stu-id="1a61b-145">Sometimes these requests are the result of poorly designed `SELECT *` query.</span></span>

- <span data-ttu-id="1a61b-146">同樣地，擷取大量實體的要求可能是應用程式未正確篩選資料的徵兆。</span><span class="sxs-lookup"><span data-stu-id="1a61b-146">Similarly, requests that retrieve a large number of entities may be sign that the application is not filtering data correctly.</span></span> <span data-ttu-id="1a61b-147">請確認這些實體實際上都有需要。</span><span class="sxs-lookup"><span data-stu-id="1a61b-147">Verify that all of these entities are actually needed.</span></span> <span data-ttu-id="1a61b-148">可能的話，使用資料庫端篩選，例如，SQL 中的 `WHERE` 子句。</span><span class="sxs-lookup"><span data-stu-id="1a61b-148">Use database-side filtering if possible, for example, by using `WHERE` clauses in SQL.</span></span>

- <span data-ttu-id="1a61b-149">資料庫的卸載處理不一定最佳選項。</span><span class="sxs-lookup"><span data-stu-id="1a61b-149">Offloading processing to the database is not always the best option.</span></span> <span data-ttu-id="1a61b-150">只在資料庫是設計或最佳化來執行這項操作時，才使用這個策略。</span><span class="sxs-lookup"><span data-stu-id="1a61b-150">Only use this strategy when the database is designed or optimized to do so.</span></span> <span data-ttu-id="1a61b-151">大部分資料庫系統都針對特定函式高度最佳化，但是並非設計來作為一般用途應用程式引擎。</span><span class="sxs-lookup"><span data-stu-id="1a61b-151">Most database systems are highly optimized for certain functions, but are not designed to act as general-purpose application engines.</span></span> <span data-ttu-id="1a61b-152">如需詳細資訊，請參閱[忙碌資料庫反模式][BusyDatabase]。</span><span class="sxs-lookup"><span data-stu-id="1a61b-152">For more information, see the [Busy Database antipattern][BusyDatabase].</span></span>

## <a name="how-to-detect-the-problem"></a><span data-ttu-id="1a61b-153">如何偵測問題</span><span class="sxs-lookup"><span data-stu-id="1a61b-153">How to detect the problem</span></span>

<span data-ttu-id="1a61b-154">沒有直接關聯的擷取之徵兆包括高延遲及低輸送量。</span><span class="sxs-lookup"><span data-stu-id="1a61b-154">Symptoms of extraneous fetching include high latency and low throughput.</span></span> <span data-ttu-id="1a61b-155">如果資料是從資料存放區擷取，也可能會增加爭用。</span><span class="sxs-lookup"><span data-stu-id="1a61b-155">If the data is retrieved from a data store, increased contention is also probable.</span></span> <span data-ttu-id="1a61b-156">使用者可能會回報服務逾時造成的擴充回應時間或失敗。這些失敗可能會傳回 HTTP 500 (內部伺服器) 錯誤或 HTTP 503 (服務無法使用) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="1a61b-156">End users are likely to report extended response times or failures caused by services timing out. These failures could return HTTP 500 (Internal Server) errors or HTTP 503 (Service Unavailable) errors.</span></span> <span data-ttu-id="1a61b-157">檢查網頁伺服器的事件記錄，其中可能包含錯誤原因和情況的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="1a61b-157">Examine the event logs for the web server, which are likely to contain more detailed information about the causes and circumstances of the errors.</span></span>

<span data-ttu-id="1a61b-158">這個反模式的徵兆和所獲得的部分遙測可能非常類似於[整合的持續性反模式][MonolithicPersistence]。</span><span class="sxs-lookup"><span data-stu-id="1a61b-158">The symptoms of this antipattern and some of the telemetry obtained might be very similar to those of the [Monolithic Persistence antipattern][MonolithicPersistence].</span></span>

<span data-ttu-id="1a61b-159">您可以執行下列步驟來協助識別原因：</span><span class="sxs-lookup"><span data-stu-id="1a61b-159">You can perform the following steps to help identify the cause:</span></span>

1. <span data-ttu-id="1a61b-160">執行負載測試、處理監控，或擷取檢測資料的其他方法，來識別緩慢工作負載或交易。</span><span class="sxs-lookup"><span data-stu-id="1a61b-160">Identify slow workloads or transactions by performing load-testing, process monitoring, or other methods of capturing instrumentation data.</span></span>
2. <span data-ttu-id="1a61b-161">觀察系統所展現的任何行為模式。</span><span class="sxs-lookup"><span data-stu-id="1a61b-161">Observe any behavioral patterns exhibited by the system.</span></span> <span data-ttu-id="1a61b-162">每秒交易數或使用者數量是否有特定限制？</span><span class="sxs-lookup"><span data-stu-id="1a61b-162">Are there particular limits in terms of transactions per second or volume of users?</span></span>
3. <span data-ttu-id="1a61b-163">讓緩慢工作負載的執行個體與行為模式相互關聯。</span><span class="sxs-lookup"><span data-stu-id="1a61b-163">Correlate the instances of slow workloads with behavioral patterns.</span></span>
4. <span data-ttu-id="1a61b-164">識別正在使用的資料存放區。</span><span class="sxs-lookup"><span data-stu-id="1a61b-164">Identify the data stores being used.</span></span> <span data-ttu-id="1a61b-165">對於每個資料來源，執行較低層級的遙測，以觀察作業的行為。</span><span class="sxs-lookup"><span data-stu-id="1a61b-165">For each data source, run lower level telemetry to observe the behavior of operations.</span></span>
5. <span data-ttu-id="1a61b-166">識別參考這些資料來源的任何執行緩慢查詢。</span><span class="sxs-lookup"><span data-stu-id="1a61b-166">Identify any slow-running queries that reference these data sources.</span></span>
6. <span data-ttu-id="1a61b-167">執行執行緩慢查詢的資源特定分析，並確認使用及取用資料的方式。</span><span class="sxs-lookup"><span data-stu-id="1a61b-167">Perform a resource-specific analysis of the slow-running queries and ascertain how the data is used and consumed.</span></span>

<span data-ttu-id="1a61b-168">尋找任何徵兆：</span><span class="sxs-lookup"><span data-stu-id="1a61b-168">Look for any of these symptoms:</span></span>

- <span data-ttu-id="1a61b-169">對相同資源或資料存放區進行的頻繁、大型 I/O 要求。</span><span class="sxs-lookup"><span data-stu-id="1a61b-169">Frequent, large I/O requests made to the same resource or data store.</span></span>
- <span data-ttu-id="1a61b-170">共用資源或資料存放區中的爭用。</span><span class="sxs-lookup"><span data-stu-id="1a61b-170">Contention in a shared resource or data store.</span></span>
- <span data-ttu-id="1a61b-171">頻繁透過網路接收大量資料的作業。</span><span class="sxs-lookup"><span data-stu-id="1a61b-171">An operation that frequently receives large volumes of data over the network.</span></span>
- <span data-ttu-id="1a61b-172">應用程式和服務花費很長的時間等候 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="1a61b-172">Applications and services spending significant time waiting for I/O to complete.</span></span>

## <a name="example-diagnosis"></a><span data-ttu-id="1a61b-173">範例診斷</span><span class="sxs-lookup"><span data-stu-id="1a61b-173">Example diagnosis</span></span>

<span data-ttu-id="1a61b-174">下列各節會將這些步驟套用到先前的範例。</span><span class="sxs-lookup"><span data-stu-id="1a61b-174">The following sections apply these steps to the previous examples.</span></span>

### <a name="identify-slow-workloads"></a><span data-ttu-id="1a61b-175">識別緩慢工作負載</span><span class="sxs-lookup"><span data-stu-id="1a61b-175">Identify slow workloads</span></span>

<span data-ttu-id="1a61b-176">這個圖表會顯示模擬最多 400 個並行使用者執行先前所示 `GetAllFieldsAsync` 方法之負載測試的結果。</span><span class="sxs-lookup"><span data-stu-id="1a61b-176">This graph shows performance results from a load test that simulated up to 400 concurrent users running the `GetAllFieldsAsync` method shown earlier.</span></span> <span data-ttu-id="1a61b-177">輸送量隨著負載增加而緩慢減少。</span><span class="sxs-lookup"><span data-stu-id="1a61b-177">Throughput diminishes slowly as the load increases.</span></span> <span data-ttu-id="1a61b-178">平均回應時間隨著工作負載增加而提升。</span><span class="sxs-lookup"><span data-stu-id="1a61b-178">Average response time goes up as the workload increases.</span></span>

![GetAllFieldsAsync 方法的負載測試結果][Load-Test-Results-Client-Side1]

<span data-ttu-id="1a61b-180">`AggregateOnClientAsync` 作業的負載測試會顯示類似模式。</span><span class="sxs-lookup"><span data-stu-id="1a61b-180">A load test for the `AggregateOnClientAsync` operation shows a similar pattern.</span></span> <span data-ttu-id="1a61b-181">要求數量相當穩定。</span><span class="sxs-lookup"><span data-stu-id="1a61b-181">The volume of requests is reasonably stable.</span></span> <span data-ttu-id="1a61b-182">平均回應時間會隨著工作負載而增加，雖然比先前的圖表更慢。</span><span class="sxs-lookup"><span data-stu-id="1a61b-182">The average response time increases with the workload, although more slowly than the previous graph.</span></span>

![AggregateOnClientAsync 方法的負載測試結果][Load-Test-Results-Client-Side2]

### <a name="correlate-slow-workloads-with-behavioral-patterns"></a><span data-ttu-id="1a61b-184">讓緩慢工作負載與行為模式相互關聯</span><span class="sxs-lookup"><span data-stu-id="1a61b-184">Correlate slow workloads with behavioral patterns</span></span>

<span data-ttu-id="1a61b-185">高使用率和緩慢效能的標準期間之間的任何相互關聯，可以表示關切的區域。</span><span class="sxs-lookup"><span data-stu-id="1a61b-185">Any correlation between regular periods of high usage and slowing performance can indicate areas of concern.</span></span> <span data-ttu-id="1a61b-186">仔細檢查可能緩慢執行之功能的效能設定檔，以判斷它是否符合稍早執行的負載測試。</span><span class="sxs-lookup"><span data-stu-id="1a61b-186">Closely examine the performance profile of functionality that is suspected to be slow running, to determine whether it matches the load testing performed earlier.</span></span>

<span data-ttu-id="1a61b-187">使用以步驟為基礎的使用者負載對相同功能進行負載測試，以找出效能大幅降低或完全失敗的點。</span><span class="sxs-lookup"><span data-stu-id="1a61b-187">Load test the same functionality using step-based user loads, to find the point where performance drops significantly or fails completely.</span></span> <span data-ttu-id="1a61b-188">如果該點落在您預期的真實世界使用方式界限內，請檢查功能的實作方式。</span><span class="sxs-lookup"><span data-stu-id="1a61b-188">If that point falls within the bounds of your expected real-world usage, examine how the functionality is implemented.</span></span>

<span data-ttu-id="1a61b-189">如果作業未在系統處於壓力下執行、並非時間關鍵，且不會對效能或其他重要作業產生負面影響，則緩慢作業不一定是問題。</span><span class="sxs-lookup"><span data-stu-id="1a61b-189">A slow operation is not necessarily a problem, if it is not being performed when the system is under stress, is not time critical, and does not negatively affect the performance of other important operations.</span></span> <span data-ttu-id="1a61b-190">例如，產生每月作業統計資料可能會是長時間執行的作業，但是可能可以批次處理和低優先順序作業的方式來執行。</span><span class="sxs-lookup"><span data-stu-id="1a61b-190">For example, generating monthly operational statistics might be a long-running operation, but it can probably be performed as a batch process and run as a low priority job.</span></span> <span data-ttu-id="1a61b-191">相反地，查詢產品目錄的客戶是重要商務作業。</span><span class="sxs-lookup"><span data-stu-id="1a61b-191">On the other hand, customers querying the product catalog is a critical business operation.</span></span> <span data-ttu-id="1a61b-192">專注於這些重要作業所產生的遙測，以查看效能在高使用量期間如何變化。</span><span class="sxs-lookup"><span data-stu-id="1a61b-192">Focus on the telemetry generated by these critical operations to see how the performance varies during periods of high usage.</span></span>

### <a name="identify-data-sources-in-slow-workloads"></a><span data-ttu-id="1a61b-193">識別緩慢工作負載中的資料來源</span><span class="sxs-lookup"><span data-stu-id="1a61b-193">Identify data sources in slow workloads</span></span>

<span data-ttu-id="1a61b-194">如果您懷疑服務因為其擷取資料的方式而效能不佳，請調查應用程式與其使用之存放庫的互動方式。</span><span class="sxs-lookup"><span data-stu-id="1a61b-194">If you suspect that a service is performing poorly because of the way it retrieves data, investigate how the application interacts with the repositories it uses.</span></span> <span data-ttu-id="1a61b-195">監視即時系統，以查看在效能不佳的期間存取了哪些來源。</span><span class="sxs-lookup"><span data-stu-id="1a61b-195">Monitor the live system to see which sources are accessed during periods of poor performance.</span></span>

<span data-ttu-id="1a61b-196">對於每個資料來源，檢測系統以擷取下列項目：</span><span class="sxs-lookup"><span data-stu-id="1a61b-196">For each data source, instrument the system to capture the following:</span></span>

- <span data-ttu-id="1a61b-197">存取每個資料存放區的頻率。</span><span class="sxs-lookup"><span data-stu-id="1a61b-197">The frequency that each data store is accessed.</span></span>
- <span data-ttu-id="1a61b-198">進入和結束資料存放區的資料數量。</span><span class="sxs-lookup"><span data-stu-id="1a61b-198">The volume of data entering and exiting the data store.</span></span>
- <span data-ttu-id="1a61b-199">這些作業的時間，特別是要求的延遲。</span><span class="sxs-lookup"><span data-stu-id="1a61b-199">The timing of these operations, especially the latency of requests.</span></span>
- <span data-ttu-id="1a61b-200">在一般負載下存取每個資料存放區時發生之任何錯誤的本質和速率。</span><span class="sxs-lookup"><span data-stu-id="1a61b-200">The nature and rate of any errors that occur while accessing each data store under typical load.</span></span>

<span data-ttu-id="1a61b-201">比較此資訊與應用程式傳回給用戶端的資料數量。</span><span class="sxs-lookup"><span data-stu-id="1a61b-201">Compare this information against the volume of data being returned by the application to the client.</span></span> <span data-ttu-id="1a61b-202">追蹤資料存放區針對傳回給用戶端之資料數量所傳回的資料數量比率。</span><span class="sxs-lookup"><span data-stu-id="1a61b-202">Track the ratio of the volume of data returned by the data store against the volume of data returned to the client.</span></span> <span data-ttu-id="1a61b-203">如果有任何大型差異，請調查以判斷應用程式是否擷取不需要的的資料。</span><span class="sxs-lookup"><span data-stu-id="1a61b-203">If there is any large disparity, investigate to determine whether the application is fetching data that it doesn't need.</span></span>

<span data-ttu-id="1a61b-204">您可以透過觀察即時系統並追蹤每個使用者要求的生命週期，來擷取這項資料，或者您可以建立一系列綜合工作負載的模型，並且針對測試系統執行。</span><span class="sxs-lookup"><span data-stu-id="1a61b-204">You may be able to capture this data by observing the live system and tracing the lifecycle of each user request, or you can model a series of synthetic workloads and run them against a test system.</span></span>

<span data-ttu-id="1a61b-205">下圖顯示在 `GetAllFieldsAsync` 方法的負載測試期間，使用 [New Relic APM][new-relic] 擷取的遙測。</span><span class="sxs-lookup"><span data-stu-id="1a61b-205">The following graphs show telemetry captured using [New Relic APM][new-relic] during a load test of the `GetAllFieldsAsync` method.</span></span> <span data-ttu-id="1a61b-206">請注意從資料庫和對應 HTTP 回應收到的資料數量之間的差異。</span><span class="sxs-lookup"><span data-stu-id="1a61b-206">Note the difference between the volumes of data received from the database and the corresponding HTTP responses.</span></span>

![`GetAllFieldsAsync` 方法的遙測][TelemetryAllFields]

<span data-ttu-id="1a61b-208">針對每個要求，資料庫會傳回 80503 個位元組，但是對用戶端的回應只包含 19855 個位元組，大約是資料庫回應大小的 25%。</span><span class="sxs-lookup"><span data-stu-id="1a61b-208">For each request, the database returned 80,503 bytes, but the response to the client only contained 19,855 bytes, about 25% of the size of the database response.</span></span> <span data-ttu-id="1a61b-209">傳回給用戶端的資料大小會根據格式而不同。</span><span class="sxs-lookup"><span data-stu-id="1a61b-209">The size of the data returned to the client can vary depending on the format.</span></span> <span data-ttu-id="1a61b-210">對於此負載測試，用戶端要求 JSON 資料。</span><span class="sxs-lookup"><span data-stu-id="1a61b-210">For this load test, the client requested JSON data.</span></span> <span data-ttu-id="1a61b-211">使用 XML (未顯示) 的個別測試具有 35655 個位元組的回應大小，或 44% 的資料庫回應大小。</span><span class="sxs-lookup"><span data-stu-id="1a61b-211">Separate testing using XML (not shown) had a response size of 35,655 bytes, or 44% of the size of the database response.</span></span>

<span data-ttu-id="1a61b-212">`AggregateOnClientAsync` 方法的負載測試會顯示更多極端的結果。</span><span class="sxs-lookup"><span data-stu-id="1a61b-212">The load test for the `AggregateOnClientAsync` method shows more extreme results.</span></span> <span data-ttu-id="1a61b-213">在此情況下，每個測試會執行從資料庫擷取超過 280 Kb 資料的查詢，但是 JSON 回應只有 14 個位元組。</span><span class="sxs-lookup"><span data-stu-id="1a61b-213">In this case, each test performed a query that retrieved over 280Kb of data from the database, but the JSON response was a mere 14 bytes.</span></span> <span data-ttu-id="1a61b-214">造成這麼大的差異是因為方法會從大量資料計算彙總結果。</span><span class="sxs-lookup"><span data-stu-id="1a61b-214">The wide disparity is because the method calculates an aggregated result from a large volume of data.</span></span>

![`AggregateOnClientAsync` 方法的遙測][TelemetryAggregateOnClient]

### <a name="identify-and-analyze-slow-queries"></a><span data-ttu-id="1a61b-216">識別及分析緩慢查詢</span><span class="sxs-lookup"><span data-stu-id="1a61b-216">Identify and analyze slow queries</span></span>

<span data-ttu-id="1a61b-217">尋找取用最多資源且耗費最多時間執行的資料庫查詢。</span><span class="sxs-lookup"><span data-stu-id="1a61b-217">Look for database queries that consume the most resources and take the most time to execute.</span></span> <span data-ttu-id="1a61b-218">您可以新增檢測以尋找許多資料庫作業的開始和完成時間。</span><span class="sxs-lookup"><span data-stu-id="1a61b-218">You can add instrumentation to find the start and completion times for many database operations.</span></span> <span data-ttu-id="1a61b-219">許多資料存放區也會提供查詢如何執行和最佳化的深入資訊。</span><span class="sxs-lookup"><span data-stu-id="1a61b-219">Many data stores also provide in-depth information on how queries are performed and optimized.</span></span> <span data-ttu-id="1a61b-220">例如，Azure SQL Database 管理入口網站中的 [查詢效能] 窗格可讓您選取查詢並檢視詳細的執行階段效能資訊。</span><span class="sxs-lookup"><span data-stu-id="1a61b-220">For example, the Query Performance pane in the Azure SQL Database management portal lets you select a query and view detailed runtime performance information.</span></span> <span data-ttu-id="1a61b-221">以下是 `GetAllFieldsAsync` 作業所產生的查詢：</span><span class="sxs-lookup"><span data-stu-id="1a61b-221">Here is the query generated by the `GetAllFieldsAsync` operation:</span></span>

![Windows Azure SQL Database 管理入口網站中的 [查詢詳細資料] 窗格][QueryDetails]

## <a name="implement-the-solution-and-verify-the-result"></a><span data-ttu-id="1a61b-223">實作解決方案並確認結果</span><span class="sxs-lookup"><span data-stu-id="1a61b-223">Implement the solution and verify the result</span></span>

<span data-ttu-id="1a61b-224">將 `GetRequiredFieldsAsync` 方法變更為在資料庫端使用 SELECT 陳述式之後，負載測試會顯示下列結果。</span><span class="sxs-lookup"><span data-stu-id="1a61b-224">After changing the `GetRequiredFieldsAsync` method to use a SELECT statement on the database side, load testing showed the following results.</span></span>

![GetRequiredFieldsAsync 方法的負載測試結果][Load-Test-Results-Database-Side1]

<span data-ttu-id="1a61b-226">此負載測試使用和以前相同的部署和相同的 400 個並行使用者模擬工作負載。</span><span class="sxs-lookup"><span data-stu-id="1a61b-226">This load test used the same deployment and the same simulated workload of 400 concurrent users as before.</span></span> <span data-ttu-id="1a61b-227">圖表會顯示較低的延遲。</span><span class="sxs-lookup"><span data-stu-id="1a61b-227">The graph shows much lower latency.</span></span> <span data-ttu-id="1a61b-228">回應時間隨著負載提高約 1.3 秒，相較於上一個案例中的 4 秒。</span><span class="sxs-lookup"><span data-stu-id="1a61b-228">Response time rises with load to approximately 1.3 seconds, compared to 4 seconds in the previous case.</span></span> <span data-ttu-id="1a61b-229">相較於先前的 100 個每秒要求數，350 個每秒要求數的輸送量也提高。</span><span class="sxs-lookup"><span data-stu-id="1a61b-229">The throughput is also higher at 350 requests per second compared to 100 earlier.</span></span> <span data-ttu-id="1a61b-230">從資料庫擷取的資料數量現在會密切符合 HTTP 回應訊息的大小。</span><span class="sxs-lookup"><span data-stu-id="1a61b-230">The volume of data retrieved from the database now closely matches the size of the HTTP response messages.</span></span>

![`GetRequiredFieldsAsync` 方法的遙測][TelemetryRequiredFields]

<span data-ttu-id="1a61b-232">使用 `AggregateOnDatabaseAsync` 方法的負載測試會產生下列結果：</span><span class="sxs-lookup"><span data-stu-id="1a61b-232">Load testing using the `AggregateOnDatabaseAsync` method generates the following results:</span></span>

![AggregateOnDatabaseAsync 方法的負載測試結果][Load-Test-Results-Database-Side2]

<span data-ttu-id="1a61b-234">平均回應時間現在最少。</span><span class="sxs-lookup"><span data-stu-id="1a61b-234">The average response time is now minimal.</span></span> <span data-ttu-id="1a61b-235">這是效能範圍改善的順序，主要是由大量減少資料庫的 I/O 所造成。</span><span class="sxs-lookup"><span data-stu-id="1a61b-235">This is an order of magnitude improvement in performance, caused primarily by the large reduction in I/O from the database.</span></span>

<span data-ttu-id="1a61b-236">以下是 `AggregateOnDatabaseAsync` 方法的對應遙測。</span><span class="sxs-lookup"><span data-stu-id="1a61b-236">Here is the corresponding telemetry for the `AggregateOnDatabaseAsync` method.</span></span> <span data-ttu-id="1a61b-237">從資料庫擷取的資料數量大幅減少，由每筆交易 280 Kb 減少為 53 個位元組。</span><span class="sxs-lookup"><span data-stu-id="1a61b-237">The amount of data retrieved from the database was vastly reduced, from over 280Kb per transaction to 53 bytes.</span></span> <span data-ttu-id="1a61b-238">如此一來，每分鐘持續要求數目上限會從大約 2000 提升至 25000。</span><span class="sxs-lookup"><span data-stu-id="1a61b-238">As a result, the maximum sustained number of requests per minute was raised from around 2,000 to over 25,000.</span></span>

![`AggregateOnDatabaseAsync` 方法的遙測][TelemetryAggregateInDatabaseAsync]

## <a name="related-resources"></a><span data-ttu-id="1a61b-240">相關資源</span><span class="sxs-lookup"><span data-stu-id="1a61b-240">Related resources</span></span>

- <span data-ttu-id="1a61b-241">[忙碌資料庫反模式][BusyDatabase]</span><span class="sxs-lookup"><span data-stu-id="1a61b-241">[Busy Database antipattern][BusyDatabase]</span></span>
- <span data-ttu-id="1a61b-242">[多對話 I/O 反模式][chatty-io]</span><span class="sxs-lookup"><span data-stu-id="1a61b-242">[Chatty I/O antipattern][chatty-io]</span></span>
- <span data-ttu-id="1a61b-243">[資料分割的最佳做法][data-partitioning]</span><span class="sxs-lookup"><span data-stu-id="1a61b-243">[Data partitioning best practices][data-partitioning]</span></span>

[BusyDatabase]: ../busy-database/index.md
[data-partitioning]: ../../best-practices/data-partitioning.md
[new-relic]: https://newrelic.com/application-monitoring

[sample-app]: https://github.com/mspnp/performance-optimization/tree/master/ExtraneousFetching

[chatty-io]: ../chatty-io/index.md
[MonolithicPersistence]: ../monolithic-persistence/index.md
[Load-Test-Results-Client-Side1]:_images/LoadTestResultsClientSide1.jpg
[Load-Test-Results-Client-Side2]:_images/LoadTestResultsClientSide2.jpg
[Load-Test-Results-Database-Side1]:_images/LoadTestResultsDatabaseSide1.jpg
[Load-Test-Results-Database-Side2]:_images/LoadTestResultsDatabaseSide2.jpg
[QueryPerformanceZoomed]: _images/QueryPerformanceZoomed.jpg
[QueryDetails]: _images/QueryDetails.jpg
[TelemetryAllFields]: _images/TelemetryAllFields.jpg
[TelemetryAggregateOnClient]: _images/TelemetryAggregateOnClient.jpg
[TelemetryRequiredFields]: _images/TelemetryRequiredFields.jpg
[TelemetryAggregateInDatabaseAsync]: _images/TelemetryAggregateInDatabase.jpg

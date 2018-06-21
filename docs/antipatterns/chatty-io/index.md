---
title: 多對話 I/O 反模式
description: 大量 I/O 要求可能會損害效能及回應能力。
author: dragon119
ms.openlocfilehash: 4f0e0e455ceb58317d3029d8ab4631d476802499
ms.sourcegitcommit: 3d9ee03e2dda23753661a80c7106d1789f5223bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2018
ms.locfileid: "29477735"
---
# <a name="chatty-io-antipattern"></a><span data-ttu-id="cf5ae-103">多對話 I/O 反模式</span><span class="sxs-lookup"><span data-stu-id="cf5ae-103">Chatty I/O antipattern</span></span>

<span data-ttu-id="cf5ae-104">大量 I/O 要求的累積影響可能會在效能和回應能力上有明顯影響。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-104">The cumulative effect of a large number of I/O requests can have a significant impact on performance and responsiveness.</span></span>

## <a name="problem-description"></a><span data-ttu-id="cf5ae-105">問題說明</span><span class="sxs-lookup"><span data-stu-id="cf5ae-105">Problem description</span></span>

<span data-ttu-id="cf5ae-106">相較於計算工作，網路呼叫和其他 I/O 作業原本就比較慢。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-106">Network calls and other I/O operations are inherently slow compared to compute tasks.</span></span> <span data-ttu-id="cf5ae-107">每個 I/O 要求通常會有大量額外負荷，而眾多 I/O 作業所累積的影響可能會降低系統速度。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-107">Each I/O request typically has significant overhead, and the cumulative effect of numerous I/O operations can slow down the system.</span></span> <span data-ttu-id="cf5ae-108">以下是造成多對話 I/O 的一些常見原因。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-108">Here are some common causes of chatty I/O.</span></span>

### <a name="reading-and-writing-individual-records-to-a-database-as-distinct-requests"></a><span data-ttu-id="cf5ae-109">將個別記錄作為不同要求來讀取和寫入至資料庫</span><span class="sxs-lookup"><span data-stu-id="cf5ae-109">Reading and writing individual records to a database as distinct requests</span></span>

<span data-ttu-id="cf5ae-110">下列範例會從產品資料庫進行讀取。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-110">The following example reads from a database of products.</span></span> <span data-ttu-id="cf5ae-111">其中有三個資料表：`Product`、`ProductSubcategory` 和 `ProductPriceListHistory`。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-111">There are three tables, `Product`, `ProductSubcategory`, and `ProductPriceListHistory`.</span></span> <span data-ttu-id="cf5ae-112">藉由執行一系列的查詢，程式碼會擷取子類別中的所有產品及定價資訊：</span><span class="sxs-lookup"><span data-stu-id="cf5ae-112">The code retrieves all of the products in a subcategory, along with the pricing information, by executing a series of queries:</span></span>  

1. <span data-ttu-id="cf5ae-113">從 `ProductSubcategory` 資料表查詢子類別。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-113">Query the subcategory from the `ProductSubcategory` table.</span></span>
2. <span data-ttu-id="cf5ae-114">藉由查詢 `Product` 資料表，找出此子類別中所有產品。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-114">Find all products in that subcategory by querying the `Product` table.</span></span>
3. <span data-ttu-id="cf5ae-115">針對每個產品，從 `ProductPriceListHistory` 資料表查詢定價資料。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-115">For each product, query the pricing data from the `ProductPriceListHistory` table.</span></span>

<span data-ttu-id="cf5ae-116">應用程式會使用 [Entity Framework][ef] 查詢資料庫。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-116">The application uses [Entity Framework][ef] to query the database.</span></span> <span data-ttu-id="cf5ae-117">您可以在[這裡][code-sample]找到完整的範例。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-117">You can find the complete sample [here][code-sample].</span></span> 

```csharp
public async Task<IHttpActionResult> GetProductsInSubCategoryAsync(int subcategoryId)
{
    using (var context = GetContext())
    {
        // Get product subcategory.
        var productSubcategory = await context.ProductSubcategories
                .Where(psc => psc.ProductSubcategoryId == subcategoryId)
                .FirstOrDefaultAsync();

        // Find products in that category.
        productSubcategory.Product = await context.Products
            .Where(p => subcategoryId == p.ProductSubcategoryId)
            .ToListAsync();

        // Find price history for each product.
        foreach (var prod in productSubcategory.Product)
        {
            int productId = prod.ProductId;
            var productListPriceHistory = await context.ProductListPriceHistory
                .Where(pl => pl.ProductId == productId)
                .ToListAsync();
            prod.ProductListPriceHistory = productListPriceHistory;
        }
        return Ok(productSubcategory);
    }
}
```

<span data-ttu-id="cf5ae-118">此範例會明確顯示問題，但如果 O/RM 以隱含方式一次提取一個子記錄，則可能會遮蓋問題。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-118">This example shows the problem explicitly, but sometimes an O/RM can mask the problem, if it implicitly fetches child records one at a time.</span></span> <span data-ttu-id="cf5ae-119">這稱為「N+1 問題」。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-119">This is known as the "N+1 problem".</span></span> 

### <a name="implementing-a-single-logical-operation-as-a-series-of-http-requests"></a><span data-ttu-id="cf5ae-120">將單一邏輯運算實作為一系列 HTTP 要求</span><span class="sxs-lookup"><span data-stu-id="cf5ae-120">Implementing a single logical operation as a series of HTTP requests</span></span>

<span data-ttu-id="cf5ae-121">這通常會在開發人員嘗試遵循物件導向的範例，並將遠端物件視為記憶體中的本機物件時發生。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-121">This often happens when developers try to follow an object-oriented paradigm, and treat remote objects as if they were local objects in memory.</span></span> <span data-ttu-id="cf5ae-122">這會導致過多的網路往返。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-122">This can result in too many network round trips.</span></span> <span data-ttu-id="cf5ae-123">例如，下列 Web API 會透過個別 HTTP GET 方法，公開 `User` 物件的個別屬性。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-123">For example, the following web API exposes the individual properties of `User` objects through individual HTTP GET methods.</span></span> 

```csharp
public class UserController : ApiController
{
    [HttpGet]
    [Route("users/{id:int}/username")]
    public HttpResponseMessage GetUserName(int id)
    {
        ...
    }

    [HttpGet]
    [Route("users/{id:int}/gender")]
    public HttpResponseMessage GetGender(int id)
    {
        ...
    }

    [HttpGet]
    [Route("users/{id:int}/dateofbirth")]
    public HttpResponseMessage GetDateOfBirth(int id)
    {
        ...
    }
}
```

<span data-ttu-id="cf5ae-124">雖然此方法沒有技術上的錯誤，但大部分用戶端可能需要取得每個 `User` 的數個屬性，產生的用戶端程式碼如下所示。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-124">While there's nothing technically wrong with this approach, most clients will probably need to get several properties for each `User`, resulting in client code like the following.</span></span> 

```csharp
HttpResponseMessage response = await client.GetAsync("users/1/username");
response.EnsureSuccessStatusCode();
var userName = await response.Content.ReadAsStringAsync();

response = await client.GetAsync("users/1/gender");
response.EnsureSuccessStatusCode();
var gender = await response.Content.ReadAsStringAsync();

response = await client.GetAsync("users/1/dateofbirth");
response.EnsureSuccessStatusCode();
var dob = await response.Content.ReadAsStringAsync();
```

### <a name="reading-and-writing-to-a-file-on-disk"></a><span data-ttu-id="cf5ae-125">讀取和寫入磁碟上的檔案</span><span class="sxs-lookup"><span data-stu-id="cf5ae-125">Reading and writing to a file on disk</span></span>

<span data-ttu-id="cf5ae-126">檔案 I/O 包括：在讀取或寫入資料前開啟檔案和將檔案移至適當的位置。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-126">File I/O involves opening a file and moving to the appropriate point before reading or writing data.</span></span> <span data-ttu-id="cf5ae-127">作業完成時，檔案可能會關閉以儲存作業系統資源。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-127">When the operation is complete, the file might be closed to save operating system resources.</span></span> <span data-ttu-id="cf5ae-128">如果應用程式持續讀取和寫入少量資訊到檔案，則會產生大量 I/O 額外負荷。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-128">An application that continually reads and writes small amounts of information to a file will generate significant I/O overhead.</span></span> <span data-ttu-id="cf5ae-129">小型寫入要求也可能導致檔案分散，進一步拖慢後續的 I/O 作業。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-129">Small write requests can also lead to file fragmentation, slowing subsequent I/O operations still further.</span></span> 

<span data-ttu-id="cf5ae-130">下列範例會使用 `FileStream` 來將 `Customer` 物件寫入檔案。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-130">The following example uses a `FileStream` to write a `Customer` object to a file.</span></span> <span data-ttu-id="cf5ae-131">建立 `FileStream` 會開啟檔案，而加以處理則會關閉檔案。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-131">Creating the `FileStream` opens the file, and disposing it closes the file.</span></span> <span data-ttu-id="cf5ae-132">(`using` 陳述式會自動處理 `FileStream` 物件。)如果應用程式在新取用者加入時，重複呼叫此方法，就會快速累積 I/O 額外負荷。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-132">(The `using` statement automatically disposes the `FileStream` object.) If the application calls this method repeatedly as new customers are added, the I/O overhead can accumulate quickly.</span></span>

```csharp
private async Task SaveCustomerToFileAsync(Customer cust)
{
    using (Stream fileStream = new FileStream(CustomersFileName, FileMode.Append))
    {
        BinaryFormatter formatter = new BinaryFormatter();
        byte [] data = null;
        using (MemoryStream memStream = new MemoryStream())
        {
            formatter.Serialize(memStream, cust);
            data = memStream.ToArray();
        }
        await fileStream.WriteAsync(data, 0, data.Length);
    }
}
```

## <a name="how-to-fix-the-problem"></a><span data-ttu-id="cf5ae-133">如何修正問題</span><span class="sxs-lookup"><span data-stu-id="cf5ae-133">How to fix the problem</span></span>

<span data-ttu-id="cf5ae-134">將資料封裝成更大、更少的要求可減少 I/O 要求的次數。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-134">Reduce the number of I/O requests by packaging the data into larger, fewer requests.</span></span>

<span data-ttu-id="cf5ae-135">以單一查詢從資料庫擷取資料，而不是數個較小的查詢。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-135">Fetch data from a database as a single query, instead of several smaller queries.</span></span> <span data-ttu-id="cf5ae-136">以下擷取產品資訊的程式碼版本已經過修訂。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-136">Here's a revised version of the code that retrieves product information.</span></span>

```csharp
public async Task<IHttpActionResult> GetProductCategoryDetailsAsync(int subCategoryId)
{
    using (var context = GetContext())
    {
        var subCategory = await context.ProductSubcategories
                .Where(psc => psc.ProductSubcategoryId == subCategoryId)
                .Include("Product.ProductListPriceHistory")
                .FirstOrDefaultAsync();

        if (subCategory == null)
            return NotFound();

        return Ok(subCategory);
    }
}
```

<span data-ttu-id="cf5ae-137">請遵循 Web API 的 REST 設計原則。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-137">Follow REST design principles for web APIs.</span></span> <span data-ttu-id="cf5ae-138">以下是先前範例中 Web API 的修訂版本。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-138">Here's a revised version of the web API from the earlier example.</span></span> <span data-ttu-id="cf5ae-139">不同於每一個屬性都有 GET 方法，傳回 `User` 的只有單一 GET 方法。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-139">Instead of separate GET methods for each property, there is a single GET method that returns the `User`.</span></span> <span data-ttu-id="cf5ae-140">這會導致每個要求都有較大的回應主體，但每個用戶端可能要執行較少的 API 呼叫。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-140">This results in a larger response body per request, but each client is likely to make fewer API calls.</span></span>

```csharp
public class UserController : ApiController
{
    [HttpGet]
    [Route("users/{id:int}")]
    public HttpResponseMessage GetUser(int id)
    {
        ...
    }
}

// Client code
HttpResponseMessage response = await client.GetAsync("users/1");
response.EnsureSuccessStatusCode();
var user = await response.Content.ReadAsStringAsync();
```

<span data-ttu-id="cf5ae-141">針對檔案 I/O，請考慮在記憶體中緩衝處理資料，然後將緩衝的資料當做單一作業寫入檔案。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-141">For file I/O, consider buffering data in memory and then writing the buffered data to a file as a single operation.</span></span> <span data-ttu-id="cf5ae-142">此方法會減少重複開啟和關閉檔案所產生的額外負荷，並有助於減少磁碟上的檔案分散。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-142">This approach reduces the overhead from repeatedly opening and closing the file, and helps to reduce fragmentation of the file on disk.</span></span>

```csharp
// Save a list of customer objects to a file
private async Task SaveCustomerListToFileAsync(List<Customer> customers)
{
    using (Stream fileStream = new FileStream(CustomersFileName, FileMode.Append))
    {
        BinaryFormatter formatter = new BinaryFormatter();
        foreach (var cust in customers)
        {
            byte[] data = null;
            using (MemoryStream memStream = new MemoryStream())
            {
                formatter.Serialize(memStream, cust);
                data = memStream.ToArray();
            }
            await fileStream.WriteAsync(data, 0, data.Length);
        }
    }
}

// In-memory buffer for customers.
List<Customer> customers = new List<Customers>();

// Create a new customer and add it to the buffer
var cust = new Customer(...);
customers.Add(cust);

// Add more customers to the list as they are created
...

// Save the contents of the list, writing all customers in a single operation
await SaveCustomerListToFileAsync(customers);
```

## <a name="considerations"></a><span data-ttu-id="cf5ae-143">考量</span><span class="sxs-lookup"><span data-stu-id="cf5ae-143">Considerations</span></span>

- <span data-ttu-id="cf5ae-144">前兩個範例會產生較少的 I/O 呼叫，但每個呼叫會擷取較多資訊。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-144">The first two examples make *fewer* I/O calls, but each one retrieves *more* information.</span></span> <span data-ttu-id="cf5ae-145">您必須考慮這兩項因素之間的取捨。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-145">You must consider the tradeoff between these two factors.</span></span> <span data-ttu-id="cf5ae-146">正確的答案將取決於實際使用模式。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-146">The right answer will depend on the actual usage patterns.</span></span> <span data-ttu-id="cf5ae-147">例如，在 Web API 範例中，您可能會發現用戶端通常只需要使用者名稱。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-147">For example, in the web API example, it might turn out that clients often need just the user name.</span></span> <span data-ttu-id="cf5ae-148">在此情況下，將其公開為個別 API 呼叫是有意義的。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-148">In that case, it might make sense to expose it as a separate API call.</span></span> <span data-ttu-id="cf5ae-149">如需詳細資訊，請參閱[沒有直接關聯的擷取][extraneous-fetching]反模式。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-149">For more information, see the [Extraneous Fetching][extraneous-fetching] antipattern.</span></span>

- <span data-ttu-id="cf5ae-150">讀取資料時，請勿產生太大的 I/O 要求。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-150">When reading data, do not make your I/O requests too large.</span></span> <span data-ttu-id="cf5ae-151">應用程式應該只擷取可能會使用的資訊。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-151">An application should only retrieve the information that it is likely to use.</span></span> 

- <span data-ttu-id="cf5ae-152">有時候將資料分割分成兩個區塊是有幫助的，可分為佔據大部分要求的經常存取資料，以及較少使用的不常存取資料。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-152">Sometimes it helps to partition the information for an object into two chunks, *frequently accessed data* that accounts for most requests, and *less frequently accessed data* that is used rarely.</span></span> <span data-ttu-id="cf5ae-153">通常，最常存取的資料是物件總資料量中相對較少的部分，因此只傳回該部分可降低大量 I/O 額外負荷。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-153">Often the most frequently accessed data is a relatively small portion of the total data for an object, so returning just that portion can save significant I/O overhead.</span></span>

- <span data-ttu-id="cf5ae-154">寫入資料時，鎖定資源的時間應避免超過所需時間，如此可減少長時間作業期間發生競爭的機率。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-154">When writing data, avoid locking resources for longer than necessary, to reduce the chances of contention during a lengthy operation.</span></span> <span data-ttu-id="cf5ae-155">如果寫入作業會跨越多個資料存放區、檔案或服務，那麼則採用結果會一致的方法。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-155">If a write operation spans multiple data stores, files, or services, then adopt an eventually consistent approach.</span></span> <span data-ttu-id="cf5ae-156">請參閱[資料一致性指引][data-consistency-guidance]。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-156">See [Data Consistency guidance][data-consistency-guidance].</span></span>

- <span data-ttu-id="cf5ae-157">如果在寫入資料之前，您在記憶體中緩衝處理資料，則處理程序若損毀，資料會容易受到攻擊。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-157">If you buffer data in memory before writing it, the data is vulnerable if the process crashes.</span></span> <span data-ttu-id="cf5ae-158">如果資料速率通常有高載或是相對疏鬆，較安全的作法可能是在[事件中樞](http://azure.microsoft.com/services/event-hubs/)等外部長期佇列 (durable queue) 中緩衝資料。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-158">If the data rate typically has bursts or is relatively sparse, it may be safer to buffer the data in an external durable queue such as [Event Hubs](http://azure.microsoft.com/services/event-hubs/).</span></span>

- <span data-ttu-id="cf5ae-159">考慮快取從服務或資料庫中擷取的資料。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-159">Consider caching data that you retrieve from a service or a database.</span></span> <span data-ttu-id="cf5ae-160">這可藉由避免發生相同資料的重複要求，進而有助於減少 I/O 數量。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-160">This can help to reduce the volume of I/O by avoiding repeated requests for the same data.</span></span> <span data-ttu-id="cf5ae-161">如需詳細資訊，請參閱[快取最佳作法][caching-guidance]。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-161">For more information, see [Caching best practices][caching-guidance].</span></span>

## <a name="how-to-detect-the-problem"></a><span data-ttu-id="cf5ae-162">如何偵測問題</span><span class="sxs-lookup"><span data-stu-id="cf5ae-162">How to detect the problem</span></span>

<span data-ttu-id="cf5ae-163">多對話 I/O 的徵兆包括高延遲及低輸送量。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-163">Symptoms of chatty I/O include high latency and low throughput.</span></span> <span data-ttu-id="cf5ae-164">由於 I/O 資源的競爭增加，使用者可能會回報服務逾時造成的回應時間延長或失敗。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-164">End users are likely to report extended response times or failures caused by services timing out, due to increased contention for I/O resources.</span></span>

<span data-ttu-id="cf5ae-165">您可以執行下列步驟來協助識別任何問題的原因：</span><span class="sxs-lookup"><span data-stu-id="cf5ae-165">You can perform the following steps to help identify the causes of any problems:</span></span>

1. <span data-ttu-id="cf5ae-166">執行生產系統的處理程序監視，以識別回應時間緩慢的作業。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-166">Perform process monitoring of the production system to identify operations with poor response times.</span></span>
2. <span data-ttu-id="cf5ae-167">對上一個步驟中所識別的每一項作業，執行負載測試。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-167">Perform load testing of each operation identified in the previous step.</span></span>
3. <span data-ttu-id="cf5ae-168">在負載測試期間，針對每一項作業產生的資料存取要求，收集相關遙測資料。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-168">During the load tests, gather telemetry data about the data access requests made by each operation.</span></span>
4. <span data-ttu-id="cf5ae-169">針對每個傳送至資料存放區的要求，收集其詳細統計資料。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-169">Gather detailed statistics for each request sent to a data store.</span></span>
5. <span data-ttu-id="cf5ae-170">分析測試環境中的應用程式，以建立可能發生 I/O 瓶頸的情況。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-170">Profile the application in the test environment to establish where possible I/O bottlenecks might be occurring.</span></span> 

<span data-ttu-id="cf5ae-171">尋找任何徵兆：</span><span class="sxs-lookup"><span data-stu-id="cf5ae-171">Look for any of these symptoms:</span></span>

- <span data-ttu-id="cf5ae-172">相同檔案的大量小型 I/O 要求。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-172">A large number of small I/O requests made to the same file.</span></span>
- <span data-ttu-id="cf5ae-173">應用程式執行個體對相同服務所作的大量小型網路要求。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-173">A large number of small network requests made by an application instance to the same service.</span></span>
- <span data-ttu-id="cf5ae-174">應用程式執行個體對相同資料存放區所作的大量小型要求。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-174">A large number of small requests made by an application instance to the same data store.</span></span>
- <span data-ttu-id="cf5ae-175">成為 I/O 繫結的應用程式和服務。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-175">Applications and services becoming I/O bound.</span></span>

## <a name="example-diagnosis"></a><span data-ttu-id="cf5ae-176">範例診斷</span><span class="sxs-lookup"><span data-stu-id="cf5ae-176">Example diagnosis</span></span>

<span data-ttu-id="cf5ae-177">下列各節會將這些步驟套用至先前查詢資料庫所示的範例。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-177">The following sections apply these steps to the example shown earlier that queries a database.</span></span>

### <a name="load-test-the-application"></a><span data-ttu-id="cf5ae-178">對應用程式進行負載測試</span><span class="sxs-lookup"><span data-stu-id="cf5ae-178">Load test the application</span></span>

<span data-ttu-id="cf5ae-179">此圖表顯示負載測試的結果。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-179">This graph shows the results of load testing.</span></span> <span data-ttu-id="cf5ae-180">回應時間中間值是在每個要求 10 幾秒時測量的。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-180">Median response time is measured in 10s of seconds per request.</span></span> <span data-ttu-id="cf5ae-181">圖表顯示非常高的延遲。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-181">The graph shows very high latency.</span></span> <span data-ttu-id="cf5ae-182">因為有 1000 位使用者的負載，一個使用者可能幾乎必須等候 1 分鐘才可看到查詢結果。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-182">With a load of 1000 users, a user might have to wait for nearly a minute to see the results of a query.</span></span> 

![多對話 I/O 範例應用程式的主要指標負載測試結果][key-indicators-chatty-io]

> [!NOTE]
> <span data-ttu-id="cf5ae-184">應用程式已使用 Azure SQL Database 部署為 Azure App Service Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-184">The application was deployed as an Azure App Service web app, using Azure SQL Database.</span></span> <span data-ttu-id="cf5ae-185">負載測試使用的模擬步驟工作負載，有最多 1000 位並行使用者。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-185">The load test used a simulated step workload of up to 1000 concurrent users.</span></span> <span data-ttu-id="cf5ae-186">資料庫已透過連接集區設定，可支援最多 1000 位並行使用者，以減少發生影響結果的連線競爭。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-186">The database was configured with a connection pool supporting up to 1000 concurrent connections, to reduce the chance that contention for connections would affect the results.</span></span> 

### <a name="monitor-the-application"></a><span data-ttu-id="cf5ae-187">監視應用程式</span><span class="sxs-lookup"><span data-stu-id="cf5ae-187">Monitor the application</span></span>

<span data-ttu-id="cf5ae-188">您可以使用應用程式效能監視 (APM) 套件，來擷取及分析可能會識別多對話 I/O 的關鍵度量。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-188">You can use an application performance monitoring (APM) package to capture and analyze the key metrics that might identify chatty I/O.</span></span> <span data-ttu-id="cf5ae-189">度量的重要程度會將取決於 I/O 工作負載。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-189">Which metrics are important will depend on the I/O workload.</span></span> <span data-ttu-id="cf5ae-190">針對此範例，令人關注的 I/O 要求是資料庫查詢。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-190">For this example, the interesting I/O requests were the database queries.</span></span> 

<span data-ttu-id="cf5ae-191">下圖顯示使用 [New Relic APM][new-relic] 產生的結果。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-191">The following image shows results generated using [New Relic APM][new-relic].</span></span> <span data-ttu-id="cf5ae-192">在最大的工作負載期間，每個要求的平均資料庫回應時間尖峰大約在 5.6 秒。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-192">The average database response time peaked at approximately 5.6 seconds per request during the maximum workload.</span></span> <span data-ttu-id="cf5ae-193">在整個測試中，系統已能夠支援每分鐘平均 410 個要求。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-193">The system was able to support an average of 410 requests per minute throughout the test.</span></span>

![流量達到 AdventureWorks2012 資料庫的概觀][databasetraffic]

### <a name="gather-detailed-data-access-information"></a><span data-ttu-id="cf5ae-195">收集詳細的資料存取資訊</span><span class="sxs-lookup"><span data-stu-id="cf5ae-195">Gather detailed data access information</span></span>

<span data-ttu-id="cf5ae-196">挖掘更深入的監視資料會顯示應用程式執行三個不同的 SQL SELECT 陳述式。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-196">Digging deeper into the monitoring data shows the application executes three different SQL SELECT statements.</span></span> <span data-ttu-id="cf5ae-197">這些會對應至由 Entity Framework 所產生的要求，並從 `ProductListPriceHistory`、`Product` 和 `ProductSubcategory` 資料表擷取資料。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-197">These correspond to the requests generated by Entity Framework to fetch data from the `ProductListPriceHistory`, `Product`, and `ProductSubcategory` tables.</span></span>
<span data-ttu-id="cf5ae-198">此外，從 `ProductListPriceHistory` 資料表擷取資料的查詢是目前為止最常執行的 SELECT 陳述式 (依據重要性排序)。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-198">Furthermore, the query that retrieves data from the `ProductListPriceHistory` table is by far the most frequently executed SELECT statement, by an order of magnitude.</span></span>

![測試中範例應用程式所執行的查詢][queries]

<span data-ttu-id="cf5ae-200">結果，稍早所示的 `GetProductsInSubCategoryAsync` 方法會執行 45 次 SELECT 查詢。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-200">It turns out that the `GetProductsInSubCategoryAsync` method, shown earlier, performs 45 SELECT queries.</span></span> <span data-ttu-id="cf5ae-201">每個查詢會導致應用程式開啟新的 SQL 連線。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-201">Each query causes the application to open a new SQL connection.</span></span>

![測試中範例應用程式的查詢統計資料][queries2]

> [!NOTE]
> <span data-ttu-id="cf5ae-203">此圖顯示的追蹤資訊，是來自負載測試中最緩慢的 `GetProductsInSubCategoryAsync` 作業執行個體。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-203">This image shows trace information for the slowest instance of the `GetProductsInSubCategoryAsync` operation in the load test.</span></span> <span data-ttu-id="cf5ae-204">在生產環境中，檢查最慢執行個體的追蹤十分實用，可查看是否有可建議問題的模式。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-204">In a production environment, it's useful to examine traces of the slowest instances, to see if there is a pattern that suggests a problem.</span></span> <span data-ttu-id="cf5ae-205">如果您只查看平均值，您可能會忽略會使問題大幅惡化的負載問題。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-205">If you just look at the average values, you might overlook problems that will get dramatically worse under load.</span></span>

<span data-ttu-id="cf5ae-206">下圖顯示已發出的實際 SQL 陳述式。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-206">The next image shows the actual SQL statements that were issued.</span></span> <span data-ttu-id="cf5ae-207">擷取定價資訊的查詢會針對每個產品子類別中的個別產品執行。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-207">The query that fetches price information is run for each individual product in the product subcategory.</span></span> <span data-ttu-id="cf5ae-208">使用聯結方式將會大幅減少資料庫呼叫的次數。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-208">Using a join would considerably reduce the number of database calls.</span></span>

![測試中範例應用程式的查詢詳細資料][queries3]

<span data-ttu-id="cf5ae-210">如果您使用 Entity Framework 等 O/RM 追蹤 SQL 查詢，則可以深入了解 O/RM 如何將程式設計的呼叫轉譯成 SQL 陳述式，並指定資料存取可能會進行最佳化的區域。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-210">If you are using an O/RM, such as Entity Framework, tracing the SQL queries can provide insight into how the O/RM translates programmatic calls into SQL statements, and indicate areas where data access might be optimized.</span></span> 

### <a name="implement-the-solution-and-verify-the-result"></a><span data-ttu-id="cf5ae-211">實作解決方案並確認結果</span><span class="sxs-lookup"><span data-stu-id="cf5ae-211">Implement the solution and verify the result</span></span>

<span data-ttu-id="cf5ae-212">重寫 Entity Framework 的呼叫會產生下列結果。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-212">Rewriting the call to Entity Framework produced the following results.</span></span>

![多對話 I/O 範例應用程式中區塊 API 的主要指標負載測試結果][key-indicators-chunky-io]

<span data-ttu-id="cf5ae-214">此負載測試會在相同部署上執行 (使用相同負載設定檔)。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-214">This load test was performed on the same deployment, using the same load profile.</span></span> <span data-ttu-id="cf5ae-215">這次圖表顯示較低的延遲。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-215">This time the graph shows much lower latency.</span></span> <span data-ttu-id="cf5ae-216">1000 位使用者的平均要求時間從幾乎一分鐘降為介於 5 至 6 秒間。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-216">The average request time at 1000 users is between 5 and 6 seconds, down from nearly a minute.</span></span>

<span data-ttu-id="cf5ae-217">相較於之前測試的每分鐘 410 個要求，這時系統已可支援平均每分鐘 3,970 個要求。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-217">This time the system supported an average of 3,970 requests per minute, compared to 410 for the earlier test.</span></span>

![區塊 API 的交易概觀][databasetraffic2]

<span data-ttu-id="cf5ae-219">追蹤 SQL 陳述式會顯示所有資料已在 SELECT 陳述式進行擷取。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-219">Tracing the SQL statement shows that all the data is fetched in a single SELECT statement.</span></span> <span data-ttu-id="cf5ae-220">雖然此查詢可能更複雜，但是每個作業只須執行一次。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-220">Although this query is considerably more complex, it is performed only once per operation.</span></span> <span data-ttu-id="cf5ae-221">雖然複雜的聯結會產生較高成本，但相關資料庫系統會為此類型的查詢進行最佳化。</span><span class="sxs-lookup"><span data-stu-id="cf5ae-221">And while complex joins can become expensive, relational database systems are optimized for this type of query.</span></span>  

![區塊 API 的查詢詳細資料][queries4]

## <a name="related-resources"></a><span data-ttu-id="cf5ae-223">相關資源</span><span class="sxs-lookup"><span data-stu-id="cf5ae-223">Related resources</span></span>

- <span data-ttu-id="cf5ae-224">[API 設計最佳作法][api-design]</span><span class="sxs-lookup"><span data-stu-id="cf5ae-224">[API Design best practices][api-design]</span></span>
- <span data-ttu-id="cf5ae-225">[快取最佳做法][caching-guidance]</span><span class="sxs-lookup"><span data-stu-id="cf5ae-225">[Caching best practices][caching-guidance]</span></span>
- <span data-ttu-id="cf5ae-226">[資料一致性入門][data-consistency-guidance]</span><span class="sxs-lookup"><span data-stu-id="cf5ae-226">[Data Consistency Primer][data-consistency-guidance]</span></span>
- <span data-ttu-id="cf5ae-227">[沒有直接關聯的擷取反模式][extraneous-fetching]</span><span class="sxs-lookup"><span data-stu-id="cf5ae-227">[Extraneous Fetching antipattern][extraneous-fetching]</span></span>
- <span data-ttu-id="cf5ae-228">[沒有快取的反模式][no-cache]</span><span class="sxs-lookup"><span data-stu-id="cf5ae-228">[No Caching antipattern][no-cache]</span></span>

[api-design]: ../../best-practices/api-design.md
[caching-guidance]: ../../best-practices/caching.md
[code-sample]:  https://github.com/mspnp/performance-optimization/tree/master/ChattyIO
[data-consistency-guidance]: http://https://msdn.microsoft.com/library/dn589800.aspx
[ef]: /ef/
[extraneous-fetching]: ../extraneous-fetching/index.md
[new-relic]: https://newrelic.com/application-monitoring
[no-cache]: ../no-caching/index.md

[key-indicators-chatty-io]: _images/ChattyIO.jpg
[key-indicators-chunky-io]: _images/ChunkyIO.jpg
[databasetraffic]: _images/DatabaseTraffic.jpg
[databasetraffic2]: _images/DatabaseTraffic2.jpg
[queries]: _images/DatabaseQueries.jpg
[queries2]: _images/DatabaseQueries2.jpg
[queries3]: _images/DatabaseQueries3.jpg
[queries4]: _images/DatabaseQueries4.jpg


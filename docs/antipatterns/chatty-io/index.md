---
title: "多對話 I/O 反模式"
description: "大量 I/O 要求可能會損害效能及回應能力。"
author: dragon119
ms.openlocfilehash: 50001316939b56c9b57a119f6ae20f0878f54c0f
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="chatty-io-antipattern"></a>多對話 I/O 反模式

大量 I/O 要求的累積影響可能會在效能和回應能力上有明顯影響。

## <a name="problem-description"></a>問題說明

相較於計算工作，網路呼叫和其他 I/O 作業原本就比較慢。 每個 I/O 要求通常會有大量額外負荷，而眾多 I/O 作業所累積的影響可能會降低系統速度。 以下是造成多對話 I/O 的一些常見原因。

### <a name="reading-and-writing-individual-records-to-a-database-as-distinct-requests"></a>將個別記錄作為不同要求來讀取和寫入至資料庫

下列範例會從產品資料庫進行讀取。 其中有三個資料表：`Product`、`ProductSubcategory` 和 `ProductPriceListHistory`。 藉由執行一系列的查詢，程式碼會擷取子類別中的所有產品及定價資訊：  

1. 從 `ProductSubcategory` 資料表查詢子類別。
2. 藉由查詢 `Product` 資料表，找出此子類別中所有產品。
3. 針對每個產品，從 `ProductPriceListHistory` 資料表查詢定價資料。

應用程式會使用 [Entity Framework][ef] 查詢資料庫。 您可以在[這裡][code-sample]找到完整的範例。 

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

此範例會明確顯示問題，但如果 O/RM 以隱含方式一次提取一個子記錄，則可能會遮蓋問題。 這稱為「N+1 問題」。 

### <a name="implementing-a-single-logical-operation-as-a-series-of-http-requests"></a>將單一邏輯運算實作為一系列 HTTP 要求

這通常會在開發人員嘗試遵循物件導向的範例，並將遠端物件視為記憶體中的本機物件時發生。 這會導致過多的網路往返。 例如，下列 Web API 會透過個別 HTTP GET 方法，公開 `User` 物件的個別屬性。 

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

雖然此方法沒有技術上的錯誤，但大部分用戶端可能需要取得每個 `User` 的數個屬性，產生的用戶端程式碼如下所示。 

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

### <a name="reading-and-writing-to-a-file-on-disk"></a>讀取和寫入磁碟上的檔案

檔案 I/O 包括：在讀取或寫入資料前開啟檔案和將檔案移至適當的位置。 作業完成時，檔案可能會關閉以儲存作業系統資源。 如果應用程式持續讀取和寫入少量資訊到檔案，則會產生大量 I/O 額外負荷。 小型寫入要求也可能導致檔案分散，進一步拖慢後續的 I/O 作業。 

下列範例會使用 `FileStream` 來將 `Customer` 物件寫入檔案。 建立 `FileStream` 會開啟檔案，而加以處理則會關閉檔案。 (`using` 陳述式會自動處理 `FileStream` 物件。)如果應用程式在新取用者加入時，重複呼叫此方法，就會快速累積 I/O 額外負荷。

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

## <a name="how-to-fix-the-problem"></a>如何修正問題

將資料封裝成更大、更少的要求可減少 I/O 要求的次數。

以單一查詢從資料庫擷取資料，而不是數個較小的查詢。 以下擷取產品資訊的程式碼版本已經過修訂。

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

請遵循 Web API 的 REST 設計原則。 以下是先前範例中 Web API 的修訂版本。 不同於每一個屬性都有 GET 方法，傳回 `User` 的只有單一 GET 方法。 這會導致每個要求都有較大的回應主體，但每個用戶端可能要執行較少的 API 呼叫。

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

針對檔案 I/O，請考慮在記憶體中緩衝處理資料，然後將緩衝的資料當做單一作業寫入檔案。 此方法會減少重複開啟和關閉檔案所產生的額外負荷，並有助於減少磁碟上的檔案分散。

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

## <a name="considerations"></a>考量

- 前兩個範例會產生較少的 I/O 呼叫，但每個呼叫會擷取較多資訊。 您必須考慮這兩項因素之間的取捨。 正確的答案將取決於實際使用模式。 例如，在 Web API 範例中，您可能會發現用戶端通常只需要使用者名稱。 在此情況下，將其公開為個別 API 呼叫是有意義的。 如需詳細資訊，請參閱[沒有直接關聯的擷取][extraneous-fetching]反模式。

- 讀取資料時，請勿產生太大的 I/O 要求。 應用程式應該只擷取可能會使用的資訊。 

- 有時候將資料分割分成兩個區塊是有幫助的，可分為佔據大部分要求的經常存取資料，以及較少使用的不常存取資料。 通常，最常存取的資料是物件總資料量中相對較少的部分，因此只傳回該部分可降低大量 I/O 額外負荷。

- 寫入資料時，鎖定資源的時間應避免超過所需時間，如此可減少長時間作業期間發生競爭的機率。 如果寫入作業會跨越多個資料存放區、檔案或服務，那麼則採用結果會一致的方法。 請參閱[資料一致性指引][data-consistency-guidance]。

- 如果在寫入資料之前，您在記憶體中緩衝處理資料，則處理程序若損毀，資料會容易受到攻擊。 如果資料速率通常有高載或是相對疏鬆，較安全的作法可能是在[事件中樞](http://azure.microsoft.com/en-us/services/event-hubs/)等外部長期佇列 (durable queue) 中緩衝資料。

- 考慮快取從服務或資料庫中擷取的資料。 這可藉由避免發生相同資料的重複要求，進而有助於減少 I/O 數量。 如需詳細資訊，請參閱[快取最佳作法][caching-guidance]。

## <a name="how-to-detect-the-problem"></a>如何偵測問題

多對話 I/O 的徵兆包括高延遲及低輸送量。 由於 I/O 資源的競爭增加，使用者可能會回報服務逾時造成的回應時間延長或失敗。

您可以執行下列步驟來協助識別任何問題的原因：

1. 執行生產系統的處理程序監視，以識別回應時間緩慢的作業。
2. 對上一個步驟中所識別的每一項作業，執行負載測試。
3. 在負載測試期間，針對每一項作業產生的資料存取要求，收集相關遙測資料。
4. 針對每個傳送至資料存放區的要求，收集其詳細統計資料。
5. 分析測試環境中的應用程式，以建立可能發生 I/O 瓶頸的情況。 

尋找任何徵兆：

- 相同檔案的大量小型 I/O 要求。
- 應用程式執行個體對相同服務所作的大量小型網路要求。
- 應用程式執行個體對相同資料存放區所作的大量小型要求。
- 成為 I/O 繫結的應用程式和服務。

## <a name="example-diagnosis"></a>範例診斷

下列各節會將這些步驟套用至先前查詢資料庫所示的範例。

### <a name="load-test-the-application"></a>對應用程式進行負載測試

此圖表顯示負載測試的結果。 回應時間中間值是在每個要求 10 幾秒時測量的。 圖表顯示非常高的延遲。 因為有 1000 位使用者的負載，一個使用者可能幾乎必須等候 1 分鐘才可看到查詢結果。 

![多對話 I/O 範例應用程式的主要指標負載測試結果][key-indicators-chatty-io]

> [!NOTE]
> 應用程式已使用 Azure SQL Database 部署為 Azure App Service Web 應用程式。 負載測試使用的模擬步驟工作負載，有最多 1000 位並行使用者。 資料庫已透過連接集區設定，可支援最多 1000 位並行使用者，以減少發生影響結果的連線競爭。 

### <a name="monitor-the-application"></a>監視應用程式

您可以使用應用程式效能監視 (APM) 套件，來擷取及分析可能會識別多對話 I/O 的關鍵度量。 度量的重要程度會將取決於 I/O 工作負載。 針對此範例，令人關注的 I/O 要求是資料庫查詢。 

下圖顯示使用 [New Relic APM][new-relic] 產生的結果。 在最大的工作負載期間，每個要求的平均資料庫回應時間尖峰大約在 5.6 秒。 在整個測試中，系統已能夠支援每分鐘平均 410 個要求。

![流量達到 AdventureWorks2012 資料庫的概觀][databasetraffic]

### <a name="gather-detailed-data-access-information"></a>收集詳細的資料存取資訊

挖掘更深入的監視資料會顯示應用程式執行三個不同的 SQL SELECT 陳述式。 這些會對應至由 Entity Framework 所產生的要求，並從 `ProductListPriceHistory`、`Product` 和 `ProductSubcategory` 資料表擷取資料。
此外，從 `ProductListPriceHistory` 資料表擷取資料的查詢是目前為止最常執行的 SELECT 陳述式 (依據重要性排序)。

![測試中範例應用程式所執行的查詢][queries]

結果，稍早所示的 `GetProductsInSubCategoryAsync` 方法會執行 45 次 SELECT 查詢。 每個查詢會導致應用程式開啟新的 SQL 連線。

![測試中範例應用程式的查詢統計資料][queries2]

> [!NOTE]
> 此圖顯示的追蹤資訊，是來自負載測試中最緩慢的 `GetProductsInSubCategoryAsync` 作業執行個體。 在生產環境中，檢查最慢執行個體的追蹤十分實用，可查看是否有可建議問題的模式。 如果您只查看平均值，您可能會忽略會使問題大幅惡化的負載問題。

下圖顯示已發出的實際 SQL 陳述式。 擷取定價資訊的查詢會針對每個產品子類別中的個別產品執行。 使用聯結方式將會大幅減少資料庫呼叫的次數。

![測試中範例應用程式的查詢詳細資料][queries3]

如果您使用 Entity Framework 等 O/RM 追蹤 SQL 查詢，則可以深入了解 O/RM 如何將程式設計的呼叫轉譯成 SQL 陳述式，並指定資料存取可能會進行最佳化的區域。 

### <a name="implement-the-solution-and-verify-the-result"></a>實作解決方案並確認結果

重寫 Entity Framework 的呼叫會產生下列結果。

![多對話 I/O 範例應用程式中區塊 API 的主要指標負載測試結果][key-indicators-chunky-io]

此負載測試會在相同部署上執行 (使用相同負載設定檔)。 這次圖表顯示較低的延遲。 1000 位使用者的平均要求時間從幾乎一分鐘降為介於 5 至 6 秒間。

相較於之前測試的每分鐘 410 個要求，這時系統已可支援平均每分鐘 3,970 個要求。

![區塊 API 的交易概觀][databasetraffic2]

追蹤 SQL 陳述式會顯示所有資料已在 SELECT 陳述式進行擷取。 雖然此查詢可能更複雜，但是每個作業只須執行一次。 雖然複雜的聯結會產生較高成本，但相關資料庫系統會為此類型的查詢進行最佳化。  

![區塊 API 的查詢詳細資料][queries4]

## <a name="related-resources"></a>相關資源

- [API 設計最佳作法][api-design]
- [快取最佳做法][caching-guidance]
- [資料一致性入門][data-consistency-guidance]
- [沒有直接關聯的擷取反模式][extraneous-fetching]
- [沒有快取的反模式][no-cache]

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


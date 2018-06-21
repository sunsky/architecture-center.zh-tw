---
title: 沒有直接關聯的擷取反模式
description: 擷取超過商務作業所需的資料，會導致不必要的 I/O 額外負荷並且降低回應能力。
author: dragon119
ms.date: 06/05/2017
ms.openlocfilehash: 7a72bfd3e4b2e206f3266a046fac2083224ecb4f
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
ms.locfileid: "30846598"
---
# <a name="extraneous-fetching-antipattern"></a>沒有直接關聯的擷取反模式

擷取超過商務作業所需的資料，會導致不必要的 I/O 額外負荷並且降低回應能力。 

## <a name="problem-description"></a>問題說明

如果應用程式藉由擷取「可能」需要的所有資料，嘗試將 I/O 要求減至最少，可能會發生此反模式。 這通常是過度補償[多對話 I/O][chatty-io] 反模式的結果。 例如，應用程式可能會擷取資料庫中每個產品的詳細資料。 但是使用者可能只需要詳細資料的子集 (有些可能與客戶不相關)，而且可能不需要一次查看「所有」產品。 即使使用者瀏覽整個目錄，但是將結果編頁比較有道理 &mdash; 例如一次顯示 20 個項目。

這個問題的另一個來源是下列不良的程式設計或設計做法。 例如，下列程式碼會使用 Entity Framework 擷取每個產品的完整詳細資料。 然後它會篩選結果，僅傳回欄位的子集，並且捨棄其餘欄位。 您可以在[這裡][sample-app]找到完整的範例。

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

在下一個範例中，應用程式會擷取資料，以執行無法由資料庫完成的彙總。 應用程式會取得所有銷售訂單的每筆記錄，然後計算這些記錄的總和，來計算總銷售額。 您可以在[這裡][sample-app]找到完整的範例。

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

下一個範例示範 Entity Framework 使用 LINQ to Entities 所造成的輕微問題。 

```csharp
var query = from p in context.Products.AsEnumerable()
            where p.SellStartDate < DateTime.Now.AddDays(-7) // AddDays cannot be mapped by LINQ to Entities
            select ...;

List<Product> products = query.ToList();
```

應用程式嘗試尋找 `SellStartDate` 超過一週的產品。 在大部分情況下，LINQ to Entities 會將 `where` 子句轉譯為資料庫執行的 SQL 陳述式。 不過在此情況下，LINQ to Entities 無法將 `AddDays` 方法對應至 SQL。 相反地，會傳回 `Product` 資料表的每個資料列，並且在記憶體中篩選結果。 

對 `AsEnumerable` 的呼叫就是有問題的提示。 這個方法會將結果轉換至 `IEnumerable` 介面。 雖然 `IEnumerable` 支援篩選，但是篩選是在「用戶端」完成，而不是資料庫。 根據預設，LINQ to Entities 會使用 `IQueryable`，它會將篩選的責任傳送給資料來源。 

## <a name="how-to-fix-the-problem"></a>如何修正問題

避免擷取很快就過時或被捨棄的大量資料，而且只擷取執行之作業所需的資料。 

並非從資料表取得每個資料行然後篩選它們，而是從資料庫選取您需要的資料行。

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

同樣地，在資料庫中執行彙總，而不是在應用程式記憶體中。

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

當使用 Entity Framework 時，請確定使用 `IQueryable` 介面而非 `IEnumerable` 介面來解析 LINQ 查詢。 您可能需要調整查詢，僅使用可以對應至資料來源的函式。 先前的範例可以重構，以從查詢移除 `AddDays`，讓篩選由資料庫完成。

```csharp
DateTime dateSince = DateTime.Now.AddDays(-7); // AddDays has been factored out. 
var query = from p in context.Products
            where p.SellStartDate < dateSince // This criterion can be passed to the database by LINQ to Entities
            select ...;

List<Product> products = query.ToList();
```

## <a name="considerations"></a>考量

- 在某些情況下，您可以藉由水平分割資料來改善效能。 如果不同的作業會存取資料的不同屬性，水平資料分割可能會減少爭用。 通常，大部分作業是針對資料的小子集來執行，因此分散此負載可能會改善效能。 請參閱[資料分割][data-partitioning]。

- 對於必須支援無限制查詢的作業，實作編頁並且一次僅擷取有限數目的實體。 例如，如果客戶瀏覽產品目錄，您可以一次顯示一頁的結果。

- 盡可能利用資料存放區的內建功能。 例如，SQL 資料庫通常會提供彙總函式。 

- 如果您使用不支援特定函式 (例如彙總) 的資料存放區，您可以將計算的結果儲存在其他位置，當新增或更新記錄時更新值，讓應用程式不需要每次重新計算值。

- 如果您看到要求擷取大量的欄位，檢查原始程式碼以判斷這些欄位實際上是否都有需要。 有時候這些要求是不良設計 `SELECT *` 查詢的結果。 

- 同樣地，擷取大量實體的要求可能是應用程式未正確篩選資料的徵兆。 請確認這些實體實際上都有需要。 可能的話，使用資料庫端篩選，例如，SQL 中的 `WHERE` 子句。 

- 資料庫的卸載處理不一定最佳選項。 只在資料庫是設計或最佳化來執行這項操作時，才使用這個策略。 大部分資料庫系統都針對特定函式高度最佳化，但是並非設計來作為一般用途應用程式引擎。 如需詳細資訊，請參閱[忙碌資料庫反模式][BusyDatabase]。


## <a name="how-to-detect-the-problem"></a>如何偵測問題

沒有直接關聯的擷取之徵兆包括高延遲及低輸送量。 如果資料是從資料存放區擷取，也可能會增加爭用。 使用者可能會回報服務逾時造成的擴充回應時間或失敗。這些失敗可能會傳回 HTTP 500 (內部伺服器) 錯誤或 HTTP 503 (服務無法使用) 錯誤。 檢查網頁伺服器的事件記錄，其中可能包含錯誤原因和情況的詳細資訊。

這個反模式的徵兆和所獲得的部分遙測可能非常類似於[整合的持續性反模式][MonolithicPersistence]。 

您可以執行下列步驟來協助識別原因：

1. 執行負載測試、處理監控，或擷取檢測資料的其他方法，來識別緩慢工作負載或交易。
2. 觀察系統所展現的任何行為模式。 每秒交易數或使用者數量是否有特定限制？
3. 讓緩慢工作負載的執行個體與行為模式相互關聯。
4. 識別正在使用的資料存放區。 對於每個資料來源，執行較低層級的遙測，以觀察作業的行為。
6. 識別參考這些資料來源的任何執行緩慢查詢。
7. 執行執行緩慢查詢的資源特定分析，並確認使用及取用資料的方式。

尋找任何徵兆：

- 對相同資源或資料存放區進行的頻繁、大型 I/O 要求。
- 共用資源或資料存放區中的爭用。
- 頻繁透過網路接收大量資料的作業。
- 應用程式和服務花費很長的時間等候 I/O 完成。

## <a name="example-diagnosis"></a>範例診斷    

下列各節會將這些步驟套用到先前的範例。

### <a name="identify-slow-workloads"></a>識別緩慢工作負載

這個圖表會顯示模擬最多 400 個並行使用者執行先前所示 `GetAllFieldsAsync` 方法之負載測試的結果。 輸送量隨著負載增加而緩慢減少。 平均回應時間隨著工作負載增加而提升。 

![GetAllFieldsAsync 方法的負載測試結果][Load-Test-Results-Client-Side1]

`AggregateOnClientAsync` 作業的負載測試會顯示類似模式。 要求數量相當穩定。 平均回應時間會隨著工作負載而增加，雖然比先前的圖表更慢。

![AggregateOnClientAsync 方法的負載測試結果][Load-Test-Results-Client-Side2]

### <a name="correlate-slow-workloads-with-behavioral-patterns"></a>讓緩慢工作負載與行為模式相互關聯

高使用率和緩慢效能的標準期間之間的任何相互關聯，可以表示關切的區域。 仔細檢查可能緩慢執行之功能的效能設定檔，以判斷它是否符合稍早執行的負載測試。

使用以步驟為基礎的使用者負載對相同功能進行負載測試，以找出效能大幅降低或完全失敗的點。 如果該點落在您預期的真實世界使用方式界限內，請檢查功能的實作方式。

如果作業未在系統處於壓力下執行、並非時間關鍵，且不會對效能或其他重要作業產生負面影響，則緩慢作業不一定是問題。 例如，產生每月作業統計資料可能會是長時間執行的作業，但是可能可以批次處理和低優先順序作業的方式來執行。 相反地，查詢產品目錄的客戶是重要商務作業。 專注於這些重要作業所產生的遙測，以查看效能在高使用量期間如何變化。

### <a name="identify-data-sources-in-slow-workloads"></a>識別緩慢工作負載中的資料來源

如果您懷疑服務因為其擷取資料的方式而效能不佳，請調查應用程式與其使用之存放庫的互動方式。 監視即時系統，以查看在效能不佳的期間存取了哪些來源。 

對於每個資料來源，檢測系統以擷取下列項目：

- 存取每個資料存放區的頻率。
- 進入和結束資料存放區的資料數量。
- 這些作業的時間，特別是要求的延遲。
- 在一般負載下存取每個資料存放區時發生之任何錯誤的本質和速率。

比較此資訊與應用程式傳回給用戶端的資料數量。 追蹤資料存放區針對傳回給用戶端之資料數量所傳回的資料數量比率。 如果有任何大型差異，請調查以判斷應用程式是否擷取不需要的的資料。

您可以透過觀察即時系統並追蹤每個使用者要求的生命週期，來擷取這項資料，或者您可以建立一系列綜合工作負載的模型，並且針對測試系統執行。

下圖顯示在 `GetAllFieldsAsync` 方法的負載測試期間，使用 [New Relic APM][new-relic] 擷取的遙測。 請注意從資料庫和對應 HTTP 回應收到的資料數量之間的差異。

![`GetAllFieldsAsync` 方法的遙測][TelemetryAllFields]

針對每個要求，資料庫會傳回 80503 個位元組，但是對用戶端的回應只包含 19855 個位元組，大約是資料庫回應大小的 25%。 傳回給用戶端的資料大小會根據格式而不同。 對於此負載測試，用戶端要求 JSON 資料。 使用 XML (未顯示) 的個別測試具有 35655 個位元組的回應大小，或 44% 的資料庫回應大小。

`AggregateOnClientAsync` 方法的負載測試會顯示更多極端的結果。 在此情況下，每個測試會執行從資料庫擷取超過 280 Kb 資料的查詢，但是 JSON 回應只有 14 個位元組。 造成這麼大的差異是因為方法會從大量資料計算彙總結果。

![`AggregateOnClientAsync` 方法的遙測][TelemetryAggregateOnClient]

### <a name="identify-and-analyze-slow-queries"></a>識別及分析緩慢查詢

尋找取用最多資源且耗費最多時間執行的資料庫查詢。 您可以新增檢測以尋找許多資料庫作業的開始和完成時間。 許多資料存放區也會提供查詢如何執行和最佳化的深入資訊。 例如，Azure SQL Database 管理入口網站中的 [查詢效能] 窗格可讓您選取查詢並檢視詳細的執行階段效能資訊。 以下是 `GetAllFieldsAsync` 作業所產生的查詢：

![Windows Azure SQL Database 管理入口網站中的 [查詢詳細資料] 窗格][QueryDetails]


## <a name="implement-the-solution-and-verify-the-result"></a>實作解決方案並確認結果

將 `GetRequiredFieldsAsync` 方法變更為在資料庫端使用 SELECT 陳述式之後，負載測試會顯示下列結果。

![GetRequiredFieldsAsync 方法的負載測試結果][Load-Test-Results-Database-Side1]

此負載測試使用和以前相同的部署和相同的 400 個並行使用者模擬工作負載。 圖表會顯示較低的延遲。 回應時間隨著負載提高約 1.3 秒，相較於上一個案例中的 4 秒。 相較於先前的 100 個每秒要求數，350 個每秒要求數的輸送量也提高。 從資料庫擷取的資料數量現在會密切符合 HTTP 回應訊息的大小。

![`GetRequiredFieldsAsync` 方法的遙測][TelemetryRequiredFields]

使用 `AggregateOnDatabaseAsync` 方法的負載測試會產生下列結果：

![AggregateOnDatabaseAsync 方法的負載測試結果][Load-Test-Results-Database-Side2]

平均回應時間現在最少。 這是效能範圍改善的順序，主要是由大量減少資料庫的 I/O 所造成。 

以下是 `AggregateOnDatabaseAsync` 方法的對應遙測。 從資料庫擷取的資料數量大幅減少，由每筆交易 280 Kb 減少為 53 個位元組。 如此一來，每分鐘持續要求數目上限會從大約 2000 提升至 25000。

![`AggregateOnDatabaseAsync` 方法的遙測][TelemetryAggregateInDatabaseAsync]


## <a name="related-resources"></a>相關資源

- [忙碌資料庫反模式][BusyDatabase]
- [多對話 I/O 反模式][chatty-io]
- [資料分割的最佳做法][data-partitioning]


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

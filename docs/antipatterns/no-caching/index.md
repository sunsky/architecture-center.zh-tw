---
title: 沒有快取的反模式
titleSuffix: Performance antipatterns for cloud apps
description: 重複擷取相同資料可能會降低效能和延展性。
author: dragon119
ms.date: 06/05/2017
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 5c3062c0a17de708ada83ba81dcb111e6b1f8440
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58343844"
---
# <a name="no-caching-antipattern"></a>沒有快取的反模式

在處理許多並行要求的雲端應用程式中，重複擷取相同資料可能會降低效能和延展性。

## <a name="problem-description"></a>問題說明

如果沒有進行資料快取，可能會造成許多非預期行為，包括：

- 從存取成本高 (I/O 額外負荷或延遲方面) 的資源中重複擷取相同資訊。
- 重複為多個要求建構相同物件或資料結構。
- 對有服務配額及會對用戶端進行節流 (如果超過特定限制) 的遠端服務進行過多呼叫。

而這些問題可能會導致回應時間不佳、資料存放區的競爭增加和延展性不佳。

下列範例會使用 Entity Framework 來連接到資料庫。 每個用戶端要求都會產生傳送至資料庫的呼叫，即使有多個要求都擷取完全相同的資料。 重複要求的成本 (在 I/O 額外負荷與資料存取費用方面) 會快速累積。

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

您可以在[這裡][sample-app]找到完整的範例。

此反模式會發生通常是因為：

- 不使用快取會更容易實作，並在低負載下可以正常運作。 快取會讓程式碼更複雜。
- 不是很清楚使用快取的優點和缺點。
- 顧慮到維護快取資料精確度和最新狀態所造成的額外負荷。
- 應用程式已從內部部署系統進行移轉，其中網路延遲並不是問題，且系統已在成本高且效能高的硬體上執行，因此快取並不在原始設計的考量中。
- 開發人員不清楚將快取用在特定案例中的價值。 例如，開發人員可能不會想到在實作 Web API 時，使用 ETag。

## <a name="how-to-fix-the-problem"></a>如何修正問題

最常用的快取策略是「隨選」或「另行快取」策略。

- 進行讀取時，應用程式會嘗試從快取讀取資料。 如果資料不在快取中，則應用程式會從資料來源擷取資料，並將它新增至快取。
- 進行寫入時，應用程式會直接將變更寫入資料來源，並從快取中移除舊的值。 下一次需要這些資料時，資料會被擷取並新增至快取中。

此方法適用於經常變更的資料。 以下是上一個範例的更新，改為使用 [另行快取][cache-aside-pattern] 模式。

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

請注意，`GetAsync` 方法現在會呼叫`CacheService` 類別，而不是直接呼叫資料庫。 `CacheService` 類別會先嘗試從 Azure Redis 快取中取得項目。 如果 Redis 快取中找不到該值，則 `CacheService` 會叫用由呼叫端傳遞給它的匿名函式。 匿名函式會負責從資料庫擷取資料。 這項實作會將存放庫從特定快取解決方案中分離，以及從資料庫中分離 `CacheService`。

## <a name="considerations"></a>考量

- 如果無法使用快取，可能是因為暫時性錯誤，請勿將錯誤傳回用戶端。 相反地，請從原始資料來源擷取資料。 但是，請注意，當快取正在復原時，原始資料存放區可能忙於處理要求，因而導致逾時和連線失敗。 (畢竟，這是一個優先使用快取的動機。)使用[斷路器模式][circuit-breaker]等技術可避免資料來源癱瘓。

- 快取非靜態資料的應用程式應該設計成支援最終一致性。

- 針對 Web API，您可以透過在要求和回應訊息中包含快取控制標頭，以及使用 ETag 識別物件版本，來支援用戶端快取。 如需詳細資訊，請參閱 [API 實作][api-implementation]。

- 您不需要快取整個實體。 如果大部分的實體是靜態，只有小部分會頻繁地變更，則快取靜態項目，並從資料來源擷取動態項目。 此方法有助於減少針對資料來源執行的 I/O 數量。

- 在某些情況下，如果變動性資料是短期的，則快取該資料可能十分實用。 例如，試想持續傳送狀態更新的裝置。 這就很適合在此資訊到達時就加以快取，根本無須將該資料寫入永久存放區。

- 為防止資料過時，許多快取解決方案都可支援設定到期日，因此在指定的間隔之後，資料就會自動從快取中移除。 您可能需要根據您的情況調整到期時間。 比起可能會快速過期的變動性資料，高度靜態的資料可以在快取中停留較長時間。

- 如果快取解決方案未提供內建到期日，您可能需要實作會偶爾清除快取的背景處理程序，以避免快取資料無限成長。

- 除了從外部資料來源快取資料，您也可以使用快取來儲存複雜的計算結果。 但是在您這麼做之前，請檢測應用程式以判斷應用程式是否真的是與 CPU 繫結。

- 在應用程式啟動時就準備好快取可能會有所幫助。 請將最可能使用的資料填入快取。

- 請務必包含偵測快取命中和快取遺漏的檢測設備。 您可以使用此資訊來調整快取原則，例如哪些資料要快取，以及資料要在快取中保留多久才算過期。

- 如果缺少快取是瓶頸，則新增快取可能會增加要求數量，甚至使 Web 前端負載過重。 用戶端可能會開始收到 HTTP 503 (服務無法使用) 錯誤。 這些都表示您應該相應放大前端。

## <a name="how-to-detect-the-problem"></a>如何偵測問題

您可以執行下列步驟，以協助識別缺少快取是否會造成效能問題：

1. 檢閱應用程式程式設計。 清查應用程式使用的所有資料存放區。 針對每個資料存放區，判斷應用程式是否使用快取。 可能的話，判斷資料變更的頻率。 一開始，理想的快取候選項目包含變更緩慢的資料和經常讀取的靜態參考資料。

2. 檢測應用程式和監視即時系統，以便找出應用程式擷取資料或計算資訊的頻率。

3. 在測試環境中分析應用程式，以針對資料存取作業或其他經常執行的計算所造成的額外負荷，擷取相關的低層級度量。

4. 在測試環境中執行負載測試，以識別系統在工作負載正常和負載過重時如何回應。 負載測試應使用實際工作負載，來模擬在生產環境中觀察到的資料存取模式。

5. 檢查基礎資料存放區的資料存取統計資料，並檢閱重複相同資料要求的頻率。

## <a name="example-diagnosis"></a>範例診斷

下列各節會將這些步驟套用到稍早所述的範例應用程式。

### <a name="instrument-the-application-and-monitor-the-live-system"></a>檢測應用程式和監視即時系統

檢測應用程式並進行監視，以取得使用者在生產環境中所做的特定要求相關資訊。

下圖顯示 [New Relic][NewRelic] 在負載測試期間擷取的監視資料。 在此情況下，唯一執行的 HTTP GET 作業是 `Person/GetAsync`。 但在實際生產環境中，了解每個要求執行的相對頻率，可以讓您深入了解應該要快取哪些資源。

![New Relic 顯示 CachingDemo 應用程式的伺服器要求][NewRelic-server-requests]

如果您需要更深入的分析，您可以使用分析工具，來擷取測試環境中 (不是生產系統) 的低層級效能資料。 觀看 I/O 要求比率、記憶體使用量和 CPU 使用率等。 這些度量資訊可能會顯示大量的資料存放區或服務要求，或是執行相同計算的重複處理程序。

### <a name="load-test-the-application"></a>對應用程式進行負載測試

下圖顯示針對範例應用程式進行負載測試的結果。 負載測試會模擬多達 800 位使用者執行一系列典型作業的步驟負載。

![未快取案例的效能負載測試結果][Performance-Load-Test-Results-Uncached]

每秒中成功執行的測試數目到達平穩，而其他要求會因此變慢。 平均測試時間會隨著工作負載穩定地增加。 一旦使用者負載達到尖峰，回應時間會趨於平整。

### <a name="examine-data-access-statistics"></a>檢查資料存取統計資料

資料存放區提供的資料存取統計資料和其他資訊，可帶來十分有用的資訊，例如哪些查詢最常重複。 例如，在 Microsoft SQL Server 中，`sys.dm_exec_query_stats` 管理檢視會有最近執行之查詢的統計資訊。 每個查詢的文字皆可用於 `sys.dm_exec-query_plan` 檢視。 您可以使用 SQL Server Management Studio 等工具來執行下列 SQL 查詢，並決定執行查詢的頻率。

```SQL
SELECT UseCounts, Text, Query_Plan
FROM sys.dm_exec_cached_plans
CROSS APPLY sys.dm_exec_sql_text(plan_handle)
CROSS APPLY sys.dm_exec_query_plan(plan_handle)
```

結果中的 `UseCount` 資料行會指出每個查詢的執行頻率。 下圖顯示第三個查詢執行了 250,000 次以上，大幅超過任何其他查詢。

![在 SQL Server 管理伺服器中查詢動態管理檢視的結果][Dynamic-Management-Views]

以下是造成此資料庫要求數量的 SQL 查詢：

```SQL
(@p__linq__0 int)SELECT TOP (2)
[Extent1].[BusinessEntityId] AS [BusinessEntityId],
[Extent1].[FirstName] AS [FirstName],
[Extent1].[LastName] AS [LastName]
FROM [Person].[Person] AS [Extent1]
WHERE [Extent1].[BusinessEntityId] = @p__linq__0
```

這是 Entity Framework 在稍早所示的 `GetByIdAsync` 方法中產生的查詢。

### <a name="implement-the-solution-and-verify-the-result"></a>實作解決方案並確認結果

當您加入快取之後，重複負載測試，並與沒有快取的稍早負載測試結果進行比較。 以下是將快取加入至範例應用程式之後，產生的負載測試結果。

![快取案例的效能負載測試結果][Performance-Load-Test-Results-Cached]

成功的測試數量仍到達平穩狀態，但是使用者負載量較高。 此負載的要求比率遠大於稍早版本。 平均測試時間仍會隨著負載增加，但最大回應時間為 0.05 毫秒，相較於之前的 1 毫秒，已改善 &mdash;20&times; 倍。

## <a name="related-resources"></a>相關資源

- [API 實作最佳作法][api-implementation]
- [另行快取模式][cache-aside-pattern]
- [快取最佳做法][caching-guidance]
- [斷路器模式][circuit-breaker]

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

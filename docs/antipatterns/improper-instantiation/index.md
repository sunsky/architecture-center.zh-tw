---
title: "不適當的具現化反模式"
description: "避免對只需建立一次然後進行共用的物件持續建立新執行個體。"
author: dragon119
ms.date: 06/05/2017
ms.openlocfilehash: 8955f37e76c8b5e66c1ed7737d200d11ed321612
ms.sourcegitcommit: 9ba82cf84cee06ccba398ec04c51dab0e1ca8974
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2018
---
# <a name="improper-instantiation-antipattern"></a>不適當的具現化反模式

有些物件只需建立一次並分享即可，持續為此類物件建立新的執行個體會傷害效能。 

## <a name="problem-description"></a>問題說明

許多程式庫都提供對外部資源的抽象功能。 就內部而言，這些類別通常會管理自己的資源連線，並作為用戶端可用來存取資源的代理程式。 以下是一些與 Azure 應用程式相關的代理程式類別範例：

- `System.Net.Http.HttpClient`。 使用 HTTP 與 Web 服務通訊。
- `Microsoft.ServiceBus.Messaging.QueueClient`。 發佈和接收服務匯流排佇列的訊息。 
- `Microsoft.Azure.Documents.Client.DocumentClient`。 連線至 Cosmos DB 執行個體
- `StackExchange.Redis.ConnectionMultiplexer`。 連線至 Redis，包括 Azure Redis 快取。

這些類別的用意是只需具現化一次，並在應用程式的整個存留期間重複使用。 不過，人們往往誤以為這些類別只應在需要時取得，取得後還要快速發行。 (雖然此處列出的剛好都是 .NET 程式庫的項目，但並非只有 .NET 才會出現這種模式)。

下列 ASP.NET 範例會建立 `HttpClient` 的執行個體，以便與遠端服務進行通訊。 您可以在[這裡][sample-app]找到完整的範例。

```csharp
public class NewHttpClientInstancePerRequestController : ApiController
{
    // This method creates a new instance of HttpClient and disposes it for every call to GetProductAsync.
    public async Task<Product> GetProductAsync(string id)
    {
        using (var httpClient = new HttpClient())
        {
            var hostName = HttpContext.Current.Request.Url.Host;
            var result = await httpClient.GetStringAsync(string.Format("http://{0}:8080/api/...", hostName));
            return new Product { Name = result };
        }
    }
}
```

由於在 Web 應用程式中，此技術無法進行調整， 因此系統會為各個使用者要求建立新的 `HttpClient` 物件。 如果負載過重，網頁伺服器可能會耗盡可用的通訊端，導致出現 `SocketException` 錯誤。

此問題不限定於 `HttpClient` 類別。 其他包裝資源或建立成本高的類別也可能會造成類似問題。 下列範例會建立 `ExpensiveToCreateService` 類別的執行個體。 此處的問題不一定是通訊端耗盡，有可能只是與建立每個執行個體的所需時間有關。 持續建立和終結此類別的執行個體可能會對系統延展性有不好的影響。

```csharp
public class NewServiceInstancePerRequestController : ApiController
{
    public async Task<Product> GetProductAsync(string id)
    {
        var expensiveToCreateService = new ExpensiveToCreateService();
        return await expensiveToCreateService.GetProductByIdAsync(id);
    }
}

public class ExpensiveToCreateService
{
    public ExpensiveToCreateService()
    {
        // Simulate delay due to setup and configuration of ExpensiveToCreateService
        Thread.SpinWait(Int32.MaxValue / 100);
    }
    ...
}
```

## <a name="how-to-fix-the-problem"></a>如何修正問題

如果包裝外部資源的類別可共用且是安全執行緒，請為該類別的可重複使用執行個體，建立共用的單一執行個體或集區。

下列範例使用靜態 `HttpClient` 執行個體，因此可在所有要求間共用連線。

```csharp
public class SingleHttpClientInstanceController : ApiController
{
    private static readonly HttpClient HttpClient;

    static SingleHttpClientInstanceController()
    {
        HttpClient = new HttpClient();
    }

    // This method uses the shared instance of HttpClient for every call to GetProductAsync.
    public async Task<Product> GetProductAsync(string id)
    {
        var hostName = HttpContext.Current.Request.Url.Host;
        var result = await HttpClient.GetStringAsync(string.Format("http://{0}:8080/api/...", hostName));
        return new Product { Name = result };
    }
}
```

## <a name="considerations"></a>考量

- 此反模式的重點是會重複建立和終結*可共用*物件的執行個體。 如果類別不可共用 (不安全的執行緒)，則不適用此反模式。

- 所共用的資源類型可能會決定您應使用單一執行個體或建立集區。 `HttpClient` 類別的設計主要用於共用，而非用於集區。 其他物件可能支援集區，可讓系統將工作負載分散到多個執行個體。

- 您在多個要求間共用的物件「必須」是安全執行緒。 `HttpClient` 類別是為使用此方法而設計，但其他類別可能不支援並行要求，因此請查看相關文件。

- 某些資源類型十分稀缺，不應久佔。 資料庫連線就是個例子。 保留不需要的開放式資料庫連線，可能會阻止其他並行使用者存取資料庫。

- 在.NET Framework 中，許多與外部資源建立連線的物件，皆是在管理這些連線的其他類別上使用靜態 Factory 方法所建立的。 這些處理站物件的用途是儲存和重複使用，而不是處置和重新建立。 例如，在 Azure 服務匯流排中，`QueueClient` 物件是透過 `MessagingFactory` 物件建立的。 就內部而言，`MessagingFactory` 會管理連線。 如需詳細資訊，請參閱[使用服務匯流排傳訊的效能改進最佳作法][service-bus-messaging]。

## <a name="how-to-detect-the-problem"></a>如何偵測問題

此問題的徵兆包括輸送量降低或錯誤率增加，以及以下一個或多個狀況： 

- 例外狀況增加，這表示通訊端、資料庫連線或檔案控制代碼等資源耗盡。 
- 記憶體使用量和記憶體回收量增加。
- 網路、磁碟或資料庫活動增加。

您可以執行下列步驟來協助識別此問題：

1. 執行生產系統的處理程序監視，以識別回應時間變慢的時間點，或系統因缺少資源而失敗的時間點。
2. 請檢查從這些點上擷取到的遙測資料，以判斷哪些作業可能正在建立或終結資源耗用物件。
3. 在受控制的測試環境中 (不要使用生產系統)，對每個可疑作業進行負載測試。
4. 檢閱原始碼，並檢查代理程式物件的管理方式。

針對負載系統上執行緩慢或產生例外狀況的作業，查看堆疊追蹤。 這項資訊可協助識別這些作業使用資源的方式。 例外狀況可以協助判斷錯誤是否是因為共用資源即將耗盡所致。 

## <a name="example-diagnosis"></a>範例診斷

下列各節會將這些步驟套用到稍早所述的範例應用程式。

### <a name="identify-points-of-slow-down-or-failure"></a>識別速度變慢或失敗的點

下圖顯示使用 [New Relic APM][new-relic] 產生的結果，其中顯示回應時間不佳的作業。 在此情況下，`NewHttpClientInstancePerRequest` 控制器中的 `GetProductAsync` 方法值得您深入調查。 請注意，當這些作業正在執行時，錯誤率也會增加。 

![顯示範例應用程式為每個要求建立新 HttpClient 物件執行個體的 New Relic 監視器儀表板][dashboard-new-HTTPClient-instance]

### <a name="examine-telemetry-data-and-find-correlations"></a>檢查遙測資料並尋找相互關聯

下圖顯示使用執行緒分析擷取的資料，使用的期間與上一張圖相同。 系統會花很長的時間來開啟通訊端連線，且甚至花更多時間來關閉這些連線並處理通訊端例外狀況。

![顯示範例應用程式為每個要求建立新 HttpClient 物件執行個體的 New Relic 執行緒分析工具][thread-profiler-new-HTTPClient-instance]

### <a name="performing-load-testing"></a>執行負載測試

使用負載測試來模擬使用者可能會執行的一般作業。 這有助於識別系統中的哪些組件，會在不同負載下受到資源耗盡問題的影響。 請在受控制的環境執行這些測試，而不是在生產系統中執行。 下圖顯示當使用者負載增加至 100 位並行使用者時，`NewHttpClientInstancePerRequest` 控制器處理的要求輸送量。

![範例應用程式為每個要求建立新 HttpClient 物件執行個體的輸送量][throughput-new-HTTPClient-instance]

首先，工作負載增加時，每秒處理的要求數量就會增加。 但是，在大約有 30 位使用者時，成功的要求數量達到限制，而且系統開始產生例外狀況。 從那時起，例外狀況的數量會隨著使用者負載量增加而逐漸增加。 

負載測試會將這些失敗回報為 HTTP 500 (內部伺服器) 錯誤。 檢閱遙測資料後顯示，造成這些錯誤的原因是系統即將用完通訊端資源，因為建立了越來越多的 `HttpClient` 物件。

下圖會顯示類似的測試，但測試對象是建立自訂 `ExpensiveToCreateService` 物件的控制器。

![範例應用程式為每個要求建立新 ExpensiveToCreateService 物件執行個體的輸送量][throughput-new-ExpensiveToCreateService-instance]

這次，控制器不會產生任何例外狀況，但輸送量仍在平均回應時間增加到 20 時達到平穩狀態。 (圖表使用對數刻度作為回應時間與輸送量。)遙測資料顯示，建立 `ExpensiveToCreateService` 的新執行個體是此問題的主要原因。

### <a name="implement-the-solution-and-verify-the-result"></a>實作解決方案並確認結果

將 `GetProductAsync` 方法替換為共用單一 `HttpClient` 執行個體後，第二個負載測試結果顯示效能已獲改善。 沒有任何錯誤回報，且系統能夠處理增加的負載 (每秒最多 500 個要求)。 相較於先前的測試，平均回應時間已減半。

![範例應用程式為每個要求重新使用相同 HttpClient 物件執行個體的輸送量][throughput-single-HTTPClient-instance]

為方便比較，下圖顯示堆疊追蹤遙測資料。 這次，系統會花費大部分時間執行實際工作，而不是開啟和關閉通訊端。

![顯示範例應用程式為所有要求建立單一 HttpClient 物件執行個體的 New Relic 執行緒分析工具][thread-profiler-single-HTTPClient-instance]

下圖顯示使用 `ExpensiveToCreateService` 物件的共用執行個體所進行的類似測試。 同樣地，已處理的要求數量會隨著使用者負載量增加而增加，但平均回應時間仍然很低。 

![範例應用程式為每個要求重新使用相同 HttpClient 物件執行個體的輸送量][throughput-single-ExpensiveToCreateService-instance]



[sample-app]: https://github.com/mspnp/performance-optimization/tree/master/ImproperInstantiation
[service-bus-messaging]: /azure/service-bus-messaging/service-bus-performance-improvements
[new-relic]: https://newrelic.com/application-monitoring
[throughput-new-HTTPClient-instance]: _images/HttpClientInstancePerRequest.jpg
[dashboard-new-HTTPClient-instance]: _images/HttpClientInstancePerRequestWebTransactions.jpg
[thread-profiler-new-HTTPClient-instance]: _images/HttpClientInstancePerRequestThreadProfile.jpg
[throughput-new-ExpensiveToCreateService-instance]: _images/ServiceInstancePerRequest.jpg
[throughput-single-HTTPClient-instance]: _images/SingleHttpClientInstance.jpg
[throughput-single-ExpensiveToCreateService-instance]: _images/SingleServiceInstance.jpg
[thread-profiler-single-HTTPClient-instance]: _images/SingleHttpClientInstanceThreadProfile.jpg

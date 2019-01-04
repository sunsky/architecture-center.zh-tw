---
title: 忙碌前端反模式
titleSuffix: Performance antipatterns for cloud apps
description: 在大量背景執行緒上進行的非同步工作，會讓資源的其他前景工作無資源可用。
author: dragon119
ms.date: 06/05/2017
ms.custom: seodec18
ms.openlocfilehash: f52cedde5a17f098fb9218c48479fae981a2c7df
ms.sourcegitcommit: 680c9cef945dff6fee5e66b38e24f07804510fa9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54011492"
---
# <a name="busy-front-end-antipattern"></a>忙碌前端反模式

在大量背景執行緒上執行非同步工作，會讓資源的其他並行前景工作無資源可用，減少對於無法接受層級的回應時間。

## <a name="problem-description"></a>問題說明

耗用大量資源的工作會增加使用者要求的回應時間，並且導致高延遲。 改善回應時間的一種方法是將耗用大量資源的工作卸載至個別執行緒。 這種方法可讓應用程式在背景處理時保持回應。 不過，在背景執行緒上執行的工作仍會耗用資源。 如果有太多此類工作，它們會讓處理要求的執行緒無資源可用。

> [!NOTE]
> 詞彙「資源」可以包含許多項目，例如 CPU 使用率、佔用記憶體和網路或磁碟 I/O。

這個問題通常會在應用程式開發作為程式碼片段，所有商務邏輯都合併至與展示層共用的單一層時發生。

以下是使用 ASP.NET 示範問題的範例。 您可以在[這裡][code-sample]找到完整的範例。

```csharp
public class WorkInFrontEndController : ApiController
{
    [HttpPost]
    [Route("api/workinfrontend")]
    public HttpResponseMessage Post()
    {
        new Thread(() =>
        {
            //Simulate processing
            Thread.SpinWait(Int32.MaxValue / 100);
        }).Start();

        return Request.CreateResponse(HttpStatusCode.Accepted);
    }
}

public class UserProfileController : ApiController
{
    [HttpGet]
    [Route("api/userprofile/{id}")]
    public UserProfile Get(int id)
    {
        //Simulate processing
        return new UserProfile() { FirstName = "Alton", LastName = "Hudgens" };
    }
}
```

- `WorkInFrontEnd` 控制器中的 `Post` 方法會實作 HTTP POST 作業。 這項作業會模擬長時間執行、需要大量 CPU 的工作。 工作是在個別執行緒上執行，嘗試讓 POST 作業快速完成。

- `UserProfile` 控制器中的 `Get` 方法會實作 HTTP GET 作業。 這個方法是比較不需要大量 CPU。

主要考量是 `Post` 方法的資源需求。 雖然它會將工作放到背景執行緒，但是工作仍然會耗用相當多的 CPU 資源。 這些資源是與其他並行使用者執行的其他作業共用。 如果適量的使用者同時傳送此要求，整體效能可能會變差，讓所有作業減緩。 舉例來說，使用者可能會在 `Get` 方法中遇到重大延遲。

## <a name="how-to-fix-the-problem"></a>如何修正問題

將耗用大量資源的處理程序移至個別後端。

使用此方法，前端會將耗用大量資源的工作放入訊息佇列。 後端會挑選工作以進行非同步處理。 佇列也會作為負載架橋，緩衝後端的要求。 如果佇列長度變得太長，您可以設定自動調整以相應放大後端。

以下是先前程式碼的修訂版本。 在此版本中，`Post` 方法會將訊息放在服務匯流排佇列。

```csharp
public class WorkInBackgroundController : ApiController
{
    private static readonly QueueClient QueueClient;
    private static readonly string QueueName;
    private static readonly ServiceBusQueueHandler ServiceBusQueueHandler;

    public WorkInBackgroundController()
    {
        var serviceBusConnectionString = ...;
        QueueName = ...;
        ServiceBusQueueHandler = new ServiceBusQueueHandler(serviceBusConnectionString);
        QueueClient = ServiceBusQueueHandler.GetQueueClientAsync(QueueName).Result;
    }

    [HttpPost]
    [Route("api/workinbackground")]
    public async Task<long> Post()
    {
        return await ServiceBusQueuehandler.AddWorkLoadToQueueAsync(QueueClient, QueueName, 0);
    }
}
```

後端會從服務匯流排佇列提取訊息，並進行處理。

```csharp
public async Task RunAsync(CancellationToken cancellationToken)
{
    this._queueClient.OnMessageAsync(
        // This lambda is invoked for each message received.
        async (receivedMessage) =>
        {
            try
            {
                // Simulate processing of message
                Thread.SpinWait(Int32.Maxvalue / 1000);

                await receivedMessage.CompleteAsync();
            }
            catch
            {
                receivedMessage.Abandon();
            }
        });
}
```

## <a name="considerations"></a>考量

- 這個方法會將一些額外的複雜性新增至應用程式。 您必須安全地處理佇列和清除佇列，以避免在失敗事件中遺失要求。
- 應用程式會接受訊息佇列之其他服務的相依性。
- 處理環境必須有足夠的可擴充空間，來處理預期的工作負載，並符合所需的輸送量目標。
- 雖然這個方法應該會改善整體回應性，但是移至後端的工作可能需要更久的時間才能完成。

## <a name="how-to-detect-the-problem"></a>如何偵測問題

忙碌前端的徵兆包括執行耗用將耗用大量資源的工作之高延遲。 使用者可能會回報服務逾時造成的擴充回應時間或失敗。這些失敗也可能會傳回 HTTP 500 (內部伺服器) 錯誤或 HTTP 503 (服務無法使用) 錯誤。 檢查網頁伺服器的事件記錄，其中可能包含錯誤原因和情況的詳細資訊。

您可以執行下列步驟來協助識別此問題：

1. 執行生產系統的處理程序監視，以識別回應時間變慢的時間點。
2. 檢查在這些點所擷取的遙測資料，來判斷執行之作業與使用之資源的混合。
3. 尋找回應時間不佳與當時發生之作業量和組合之間的任何相互關聯。
4. 對每個懷疑的作業進行負載測試，以識別哪些作業耗用資源，並且讓其他作業無資源可用。
5. 檢閱這些作業的原始程式碼，來判斷為何它們可能會導致過量的資源耗用。

## <a name="example-diagnosis"></a>範例診斷

下列各節會將這些步驟套用到稍早所述的範例應用程式。

### <a name="identify-points-of-slowdown"></a>識別變慢的點

檢測每個方法來追蹤每個要求耗用的持續時間和資源。 然後在生產中監視應用程式。 這樣可以提供要求如何彼此競爭的整體檢視。 在壓力期間，緩慢執行且渴望資源的要求可能會影響其他作業，此行為可以透過監視系統和記下效能的下滑來觀察。

下圖顯示監視儀表板。 (我們針對測試使用 [AppDynamics]。)一開始，系統有輕微的負載。 然後使用者開始要求 `UserProfile` GET 方法。 效能還相當良好，直到其他使用者開始對 `WorkInFrontEnd` POST 方法發出要求。 屆時，回應時間會大幅提高 (第一個箭號)。 回應時間只會在對 `WorkInFrontEnd` 控制器的要求數量減少之後改善 (第二個箭號)。

![AppDynamics 業務交易窗格顯示當使用 WorkInFrontEnd 控制器時，所有要求之回應時間的影響][AppDynamics-Transactions-Front-End-Requests]

### <a name="examine-telemetry-data-and-find-correlations"></a>檢查遙測資料並尋找相互關聯

下圖顯示用來在相同的間隔期間監視資源使用率，所蒐集的計量。 一開始，只有少數使用者存取系統。 隨著越來越多的使用者連線，CPU 使用率變得非常高 (100%)。 另外請注意，網路 I/O 速率一開始會隨著 CPU 使用率上升。 但是一旦達到 CPU 使用率尖峰，網路 I/O 實際上會降低。 這是因為系統在 CPU 容量被佔據時，只能處理相對少數的要求。 當使用者中斷連線時，CPU 負載就會變小。

![AppDynamics 計量顯示 CPU 和網路使用率][AppDynamics-Metrics-Front-End-Requests]

此時，`WorkInFrontEnd` 控制器中的 `Post` 方法似乎是供進一步調查的主要候選項目。 必須在受控制的環境中執行進一步的工作，才能確認假設。

### <a name="perform-load-testing"></a>執行負載測試

下一個步驟是在受控制的環境中執行測試。 例如，執行所包含的一系列負載測試，然後省略每個要求以查看效果。

下圖顯示對先前測試中使用之雲端服務的相同部署執行的負載測試結果。 測試使用 500 個使用者的常數負載，在 `UserProfile` 控制器中執行 `Get` 作業，以及使用者的步驟負載，在 `WorkInFrontEnd` 控制器中執行 `Post` 作業。

![WorkInFrontEnd 控制器的初始負載測試結果][Initial-Load-Test-Results-Front-End]

一開始，步驟負載是 0，因此只有一個作用中使用者執行 `UserProfile` 要求。 系統可以回應大約 500 個每秒要求數。 60 秒之後，100 個額外使用者的負載會開始將 POST 要求傳送至 `WorkInFrontEnd` 控制器。 工作負載幾乎是立即傳送至 `UserProfile` 控制器，下降到大約 150 個每秒要求數。 這是由於負載測試執行器函式的方式。 它會在傳送下一個要求之前等候回應，因此收到回應的時間越久，要求速率越低。

隨著越來越多使用者將 POST 要求傳送至 `WorkInFrontEnd` 控制器，`UserProfile` 控制器的回應速率持續下降。 不過請注意，由 `WorkInFrontEnd` 控制器處理的要求量仍然相對穩定。 系統的飽和度變得很明顯，因為這兩個要求的整體速率都傾向於穩定但低限制。

### <a name="review-the-source-code"></a>檢閱原始程式碼

最後一個步驟是檢查原始程式碼。 開發小組已知道，`Post` 方法可能需要花一些時間，這就是為什麼原始實作會使用個別執行緒。 那樣可以解決當前的問題，因為 `Post` 方法在等候長時間執行工作完成時未封鎖。

不過，這個方法所執行的工作仍會耗用 CPU、記憶體和其他資源。 啟用此處理程序以非同步方式執行實際上可能會損害效能，因為使用者可以未受控制的方式同時觸發這些大量作業。 伺服器可以執行的執行緒數目有限制。 超過此限制，應用程式很可能會在嘗試啟動新執行緒時，發生例外狀況。

> [!NOTE]
> 這並不表示您應該避免非同步作業。 在網路呼叫上執行非同步等候是建議的做法。 (請參閱[同步 I/O][sync-io] 反模式。)此處的問題是需要大量 CPU 的工作已在另一個執行緒上繁衍。

### <a name="implement-the-solution-and-verify-the-result"></a>實作解決方案並確認結果

下圖顯示實作解決方案之後的效能監視。 負載與稍早所示類似，但是 `UserProfile` 控制器的回應時間現在更快。 相同持續期間內增加的要求數量，從 2759 到 23565。

![AppDynamics 業務交易窗格顯示當使用 WorkInBackground 控制器時，所有要求之回應時間的影響][AppDynamics-Transactions-Background-Requests]

請注意，`WorkInBackground` 控制器也會處理較大量的要求。 不過，您無法在此情況下進行直接比較，因為在此控制器中執行的工作與原始程式碼非常不同。 新版本只會將要求排入佇列，而不是執行耗時的計算。 主要的重點是這個方法不會再於負載之下拖慢整個系統。

CPU 和網路使用率也會顯示改善的效能。 CPU 使用率永遠不會達到 100%，而且處理的網路要求數量遠大於舊版，直到卸除工作負載之前都不會變小。

![AppDynamics 計量顯示 WorkInBackground 控制器的 CPU 和網路使用率][AppDynamics-Metrics-Background-Requests]

下圖顯示負載測試的結果。 為要求提供服務的整體數量相較於舊版測試大幅提升。

![BackgroundImageProcessing 控制器的負載測試結果][Load-Test-Results-Background]

## <a name="related-guidance"></a>相關的指引

- [自動調整最佳做法][autoscaling]
- [背景作業最佳做法][background-jobs]
- [佇列型負載調節模式][load-leveling]
- [Web 佇列背景工作角色架構樣式][web-queue-worker]

[AppDyanamics]: https://www.appdynamics.com/
[autoscaling]: /azure/architecture/best-practices/auto-scaling
[background-jobs]: /azure/architecture/best-practices/background-jobs
[code-sample]: https://github.com/mspnp/performance-optimization/tree/master/BusyFrontEnd
[fullDemonstrationOfSolution]: https://github.com/mspnp/performance-optimization/tree/master/BusyFrontEnd
[load-leveling]: /azure/architecture/patterns/queue-based-load-leveling
[sync-io]: ../synchronous-io/index.md
[web-queue-worker]: /azure/architecture/guide/architecture-styles/web-queue-worker

[WebJobs]: https://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx
[ComputePartitioning]: https://msdn.microsoft.com/library/dn589773.aspx
[ServiceBusQueues]: https://msdn.microsoft.com/library/azure/hh367516.aspx
[AppDynamics-Transactions-Front-End-Requests]: ./_images/AppDynamicsPerformanceStats.jpg
[AppDynamics-Metrics-Front-End-Requests]: ./_images/AppDynamicsFrontEndMetrics.jpg
[Initial-Load-Test-Results-Front-End]: ./_images/InitialLoadTestResultsFrontEnd.jpg
[AppDynamics-Transactions-Background-Requests]: ./_images/AppDynamicsBackgroundPerformanceStats.jpg
[AppDynamics-Metrics-Background-Requests]: ./_images/AppDynamicsBackgroundMetrics.jpg
[Load-Test-Results-Background]: ./_images/LoadTestResultsBackground.jpg

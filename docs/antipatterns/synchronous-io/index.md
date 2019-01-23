---
title: 同步 I/O 反模式
titleSuffix: Performance antipatterns for cloud apps
description: 在 I/O 完成時封鎖呼叫執行緒，會降低效能並且影響垂直延展性。
author: dragon119
ms.date: 06/05/2017
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 1b53806b2939a7c44a8b48c9146d5e86c84d9e2e
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54486160"
---
# <a name="synchronous-io-antipattern"></a>同步 I/O 反模式

在 I/O 完成時封鎖呼叫執行緒，會降低效能並且影響垂直延展性。

## <a name="problem-description"></a>問題說明

同步 I/O 作業會在 I/O 完成時封鎖呼叫執行緒。 呼叫執行緒會進入等候狀態，並且在此間隔期間無法執行有用的工作，因而浪費處理資源。

I/O 的常見範例包括：

- 將資料擷取或保存至資料庫或任何類型的永續性儲存體。
- 將要求傳送至 Web 服務。
- 張貼訊息，或從佇列擷取訊息。
- 寫入至本機檔案或從中讀取。

此反模式會發生通常是因為：

- 它似乎是執行作業最直覺的方式。
- 應用程式需要來自要求的回應。
- 應用程式會使用程式庫，它針對 I/O 僅提供同步方法。
- 外部程式庫會在內部執行同步 I/O 作業。 單一同步 I/O 呼叫會封鎖整個呼叫鏈結。

下列程式碼會將檔案上傳到 Azure Blob 儲存體。 程式碼區塊會在兩個位置等候同步 I/O，`CreateIfNotExists` 方法和 `UploadFromStream` 方法。

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference("uploadedfiles");

container.CreateIfNotExists();
var blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = File.OpenRead(HostingEnvironment.MapPath("~/FileToUpload.txt")))
{
    blockBlob.UploadFromStream(fileStream);
}
```

等候來自外部服務之回應的範例如下。 `GetUserProfile` 方法會呼叫傳回 `UserProfile` 的遠端服務。

```csharp
public interface IUserProfileService
{
    UserProfile GetUserProfile();
}

public class SyncController : ApiController
{
    private readonly IUserProfileService _userProfileService;

    public SyncController()
    {
        _userProfileService = new FakeUserProfileService();
    }

    // This is a synchronous method that calls the synchronous GetUserProfile method.
    public UserProfile GetUserProfile()
    {
        return _userProfileService.GetUserProfile();
    }
}
```

您可以在[這裡][sample-app]找到這兩個範例的完整程式碼。

## <a name="how-to-fix-the-problem"></a>如何修正問題

將同步 I/O 作業取代為非同步作業。 這會釋出目前的執行緒以繼續執行有意義的工作，而不是封鎖，並且協助改善計算資源的使用率。 以非同步方式執行 I/O 對於處理來自用戶端應用程式之要求的非預期突波特別有效率。

許多程式庫同時提供同步和非同步方法版本。 盡可能使用非同步版本。 以下是將檔案上傳至 Azure blob 儲存體之上一個範例的非同步版本。

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference("uploadedfiles");

await container.CreateIfNotExistsAsync();

var blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = File.OpenRead(HostingEnvironment.MapPath("~/FileToUpload.txt")))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

執行非同步作業時，`await` 運算子會將控制權傳回給呼叫環境。 當非同步作業完成時，這個陳述式後面的程式碼會接續執行。

設計良好的服務也應該提供非同步作業。 以下是傳回使用者設定檔之 Web 服務的非同步版本。 `GetUserProfileAsync` 方法取決於具有使用者設定檔服務的非同步版本。

```csharp
public interface IUserProfileService
{
    Task<UserProfile> GetUserProfileAsync();
}

public class AsyncController : ApiController
{
    private readonly IUserProfileService _userProfileService;

    public AsyncController()
    {
        _userProfileService = new FakeUserProfileService();
    }

    // This is an synchronous method that calls the Task based GetUserProfileAsync method.
    public Task<UserProfile> GetUserProfileAsync()
    {
        return _userProfileService.GetUserProfileAsync();
    }
}
```

對於未提供非同步版本作業的程式庫，可能無法在選取之同步方法周圍建立非同步包裝函式。 請謹慎遵循此方法。 雖然這樣可以改善叫用非同步包裝函式之執行緒的回應性，但是實際上會耗用更多資源。 可能會建立額外的執行緒，而且會有與這個執行緒所完成之工作同步處理相關聯的額外負荷。 此部落格文章中會討論一些利弊：[是否應該為同步方法公開非同步包裝函式？][async-wrappers]

以下是同步方法周圍非同步包裝函式的範例。

```csharp
// Asynchronous wrapper around synchronous library method
private async Task<int> LibraryIOOperationAsync()
{
    return await Task.Run(() => LibraryIOOperation());
}
```

現在，呼叫程式碼可以在包裝函式上等候：

```csharp
// Invoke the asynchronous wrapper using a task
await LibraryIOOperationAsync();
```

## <a name="considerations"></a>考量

- 預期有非常短存留期且不太可能造成爭用的 I/O 作業，可能會比同步作業更有效能。 範例可能會讀取 SSD 磁碟機上的小型檔案。 當工作完成時將工作分派到另一個執行緒，並且與該執行緒同步處理的額外負荷，可能會高於非同步 I/O 的效益。 不過，這些案例相當罕見，大部分 I/O 作業應該以非同步方式完成。

- 改善 I/O 效能可能會導致系統的其他部分變成瓶頸。 例如，解除封鎖執行緒可能會導致共用資源的更大量並行要求，進而造成資源耗盡或節流。 如果造成問題，您可能需要相應放大網頁伺服器或磁碟分割資料存放區的數目，以減少爭用。

## <a name="how-to-detect-the-problem"></a>如何偵測問題

對於使用者而言，應用程式可能看起來沒有回應或似乎定期停止回應。 應用程式可能會因逾時例外狀況而失敗。 這些失敗也會傳回 HTTP 500 (內部伺服器) 錯誤。 在伺服器上，連入用戶端要求可能會遭到封鎖，直到執行緒變成可用，導致過度的要求佇列長度，顯示為 HTTP 503 (服務無法使用) 錯誤。

您可以執行下列步驟來協助識別問題：

1. 監視生產系統，並且判斷封鎖的背景工作執行緒是否約束輸送量。

2. 如果要求因為缺乏執行緒而遭到封鎖，請檢閱應用程式以判斷哪些作業可能以同步方式執行 I/O。

3. 對執行同步 I/O 的每個作業執行受控制的負載測試，以找出是否是這些作業影響系統效能。

## <a name="example-diagnosis"></a>範例診斷

下列各節會將這些步驟套用到稍早所述的範例應用程式。

### <a name="monitor-web-server-performance"></a>監視網頁伺服器效能

針對 Azure Web 應用程式和 Web 角色，值得監視 IIS 網頁伺服器的效能。 特別注意要建立的要求佇列長度，要求是否在高度活動期間等候可用執行緒而遭到封鎖。 您可以啟用 Azure 診斷來蒐集這項資訊。 如需詳細資訊，請參閱

- [監視 Azure App Service 中的應用程式][web-sites-monitor]
- [在 Azure 應用程式中建立及使用效能計數器][performance-counters]

檢測應用程式以查看要求在被接受之後的處理方式。 追蹤要求的流程可協助識別它是否執行緩慢執行呼叫，以及封鎖目前的執行緒。 執行緒分析也可以醒目提示遭到封鎖的要求。

### <a name="load-test-the-application"></a>對應用程式進行負載測試

下圖顯示前述之同步 `GetUserProfile` 方法在最多 4000 個並行使用者之各種負載下的效能。 應用程式是 Azure 雲端服務 Web 角色中執行的 ASP.NET 應用程式。

![執行同步 I/O 作業之範例應用程式的效能圖表][sync-performance]

同步作業硬式編碼為睡眠 2 秒，以模擬同步 I/O，因此最小回應時間會稍微超過 2 秒。 當負載達到約 2500 個並行使用者時，平均回應時間會到達高原，雖然每秒的要求量會繼續增加。 請注意，這兩個量值的規模為對數。 每秒要求數在此點與測試結束之間加倍。

在隔離中，同步 I/O 是否是問題在此測試中不一定明顯。 在較重負載之下，應用程式可能會達到引爆點，網頁伺服器再也無法及時處理要求，導致用戶端應用程式收到逾時例外狀況。

連入要求是由 IIS 網頁伺服器排入佇列，並且交付給在 ASP.NET 執行緒集區中執行的執行緒。 因為每個作業會以同步方式執行 I/O，所以執行緒在作業完成之前都會遭到封鎖。 隨著工作負載增加，最後執行緒集區中的所有 ASP.NET 執行緒都會配置及封鎖。 屆時任何進一步的連入要求都必須在佇列中等待可用的執行緒。 隨著佇列長度成長，要求會開始逾時。

### <a name="implement-the-solution-and-verify-the-result"></a>實作解決方案並確認結果

下圖顯示針對非同步版本程式碼進行負載測試的結果。

![執行非同步 I/O 作業之範例應用程式的效能圖表][async-performance]

輸送量遠遠高出許多。 在與前一個測試相同的持續時間裡面，系統成功處理增加將近十倍的輸送量，以每秒的要求進行測量。 此外，平均回應時間相對穩定，而且維持比前一個測試少了大約 25 倍。

[sample-app]: https://github.com/mspnp/performance-optimization/tree/master/SynchronousIO
[async-wrappers]: https://blogs.msdn.microsoft.com/pfxteam/2012/03/24/should-i-expose-asynchronous-wrappers-for-synchronous-methods/
[performance-counters]: /azure/cloud-services/cloud-services-dotnet-diagnostics-performance-counters
[web-sites-monitor]: /azure/app-service-web/web-sites-monitor

[sync-performance]: _images/SyncPerformance.jpg
[async-performance]: _images/AsyncPerformance.jpg

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
---

# <a name="synchronous-io-antipattern"></a><span data-ttu-id="15d20-103">同步 I/O 反模式</span><span class="sxs-lookup"><span data-stu-id="15d20-103">Synchronous I/O antipattern</span></span>

<span data-ttu-id="15d20-104">在 I/O 完成時封鎖呼叫執行緒，會降低效能並且影響垂直延展性。</span><span class="sxs-lookup"><span data-stu-id="15d20-104">Blocking the calling thread while I/O completes can reduce performance and affect vertical scalability.</span></span>

## <a name="problem-description"></a><span data-ttu-id="15d20-105">問題說明</span><span class="sxs-lookup"><span data-stu-id="15d20-105">Problem description</span></span>

<span data-ttu-id="15d20-106">同步 I/O 作業會在 I/O 完成時封鎖呼叫執行緒。</span><span class="sxs-lookup"><span data-stu-id="15d20-106">A synchronous I/O operation blocks the calling thread while the I/O completes.</span></span> <span data-ttu-id="15d20-107">呼叫執行緒會進入等候狀態，並且在此間隔期間無法執行有用的工作，因而浪費處理資源。</span><span class="sxs-lookup"><span data-stu-id="15d20-107">The calling thread enters a wait state and is unable to perform useful work during this interval, wasting processing resources.</span></span>

<span data-ttu-id="15d20-108">I/O 的常見範例包括：</span><span class="sxs-lookup"><span data-stu-id="15d20-108">Common examples of I/O include:</span></span>

- <span data-ttu-id="15d20-109">將資料擷取或保存至資料庫或任何類型的永續性儲存體。</span><span class="sxs-lookup"><span data-stu-id="15d20-109">Retrieving or persisting data to a database or any type of persistent storage.</span></span>
- <span data-ttu-id="15d20-110">將要求傳送至 Web 服務。</span><span class="sxs-lookup"><span data-stu-id="15d20-110">Sending a request to a web service.</span></span>
- <span data-ttu-id="15d20-111">張貼訊息，或從佇列擷取訊息。</span><span class="sxs-lookup"><span data-stu-id="15d20-111">Posting a message or retrieving a message from a queue.</span></span>
- <span data-ttu-id="15d20-112">寫入至本機檔案或從中讀取。</span><span class="sxs-lookup"><span data-stu-id="15d20-112">Writing to or reading from a local file.</span></span>

<span data-ttu-id="15d20-113">此反模式會發生通常是因為：</span><span class="sxs-lookup"><span data-stu-id="15d20-113">This antipattern typically occurs because:</span></span>

- <span data-ttu-id="15d20-114">它似乎是執行作業最直覺的方式。</span><span class="sxs-lookup"><span data-stu-id="15d20-114">It appears to be the most intuitive way to perform an operation.</span></span>
- <span data-ttu-id="15d20-115">應用程式需要來自要求的回應。</span><span class="sxs-lookup"><span data-stu-id="15d20-115">The application requires a response from a request.</span></span>
- <span data-ttu-id="15d20-116">應用程式會使用程式庫，它針對 I/O 僅提供同步方法。</span><span class="sxs-lookup"><span data-stu-id="15d20-116">The application uses a library that only provides synchronous methods for I/O.</span></span>
- <span data-ttu-id="15d20-117">外部程式庫會在內部執行同步 I/O 作業。</span><span class="sxs-lookup"><span data-stu-id="15d20-117">An external library performs synchronous I/O operations internally.</span></span> <span data-ttu-id="15d20-118">單一同步 I/O 呼叫會封鎖整個呼叫鏈結。</span><span class="sxs-lookup"><span data-stu-id="15d20-118">A single synchronous I/O call can block an entire call chain.</span></span>

<span data-ttu-id="15d20-119">下列程式碼會將檔案上傳到 Azure Blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="15d20-119">The following code uploads a file to Azure blob storage.</span></span> <span data-ttu-id="15d20-120">程式碼區塊會在兩個位置等候同步 I/O，`CreateIfNotExists` 方法和 `UploadFromStream` 方法。</span><span class="sxs-lookup"><span data-stu-id="15d20-120">There are two places where the code blocks waiting for synchronous I/O, the `CreateIfNotExists` method and the `UploadFromStream` method.</span></span>

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

<span data-ttu-id="15d20-121">等候來自外部服務之回應的範例如下。</span><span class="sxs-lookup"><span data-stu-id="15d20-121">Here's an example of waiting for a response from an external service.</span></span> <span data-ttu-id="15d20-122">`GetUserProfile` 方法會呼叫傳回 `UserProfile` 的遠端服務。</span><span class="sxs-lookup"><span data-stu-id="15d20-122">The `GetUserProfile` method calls a remote service that returns a `UserProfile`.</span></span>

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

<span data-ttu-id="15d20-123">您可以在[這裡][sample-app]找到這兩個範例的完整程式碼。</span><span class="sxs-lookup"><span data-stu-id="15d20-123">You can find the complete code for both of these examples [here][sample-app].</span></span>

## <a name="how-to-fix-the-problem"></a><span data-ttu-id="15d20-124">如何修正問題</span><span class="sxs-lookup"><span data-stu-id="15d20-124">How to fix the problem</span></span>

<span data-ttu-id="15d20-125">將同步 I/O 作業取代為非同步作業。</span><span class="sxs-lookup"><span data-stu-id="15d20-125">Replace synchronous I/O operations with asynchronous operations.</span></span> <span data-ttu-id="15d20-126">這會釋出目前的執行緒以繼續執行有意義的工作，而不是封鎖，並且協助改善計算資源的使用率。</span><span class="sxs-lookup"><span data-stu-id="15d20-126">This frees the current thread to continue performing meaningful work rather than blocking, and helps improve the utilization of compute resources.</span></span> <span data-ttu-id="15d20-127">以非同步方式執行 I/O 對於處理來自用戶端應用程式之要求的非預期突波特別有效率。</span><span class="sxs-lookup"><span data-stu-id="15d20-127">Performing I/O asynchronously is particularly efficient for handling an unexpected surge in requests from client applications.</span></span>

<span data-ttu-id="15d20-128">許多程式庫同時提供同步和非同步方法版本。</span><span class="sxs-lookup"><span data-stu-id="15d20-128">Many libraries provide both synchronous and asynchronous versions of methods.</span></span> <span data-ttu-id="15d20-129">盡可能使用非同步版本。</span><span class="sxs-lookup"><span data-stu-id="15d20-129">Whenever possible, use the asynchronous versions.</span></span> <span data-ttu-id="15d20-130">以下是將檔案上傳至 Azure blob 儲存體之上一個範例的非同步版本。</span><span class="sxs-lookup"><span data-stu-id="15d20-130">Here is the asynchronous version of the previous example that uploads a file to Azure blob storage.</span></span>

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

<span data-ttu-id="15d20-131">執行非同步作業時，`await` 運算子會將控制權傳回給呼叫環境。</span><span class="sxs-lookup"><span data-stu-id="15d20-131">The `await` operator returns control to the calling environment while the asynchronous operation is performed.</span></span> <span data-ttu-id="15d20-132">當非同步作業完成時，這個陳述式後面的程式碼會接續執行。</span><span class="sxs-lookup"><span data-stu-id="15d20-132">The code after this statement acts as a continuation that runs when the asynchronous operation has completed.</span></span>

<span data-ttu-id="15d20-133">設計良好的服務也應該提供非同步作業。</span><span class="sxs-lookup"><span data-stu-id="15d20-133">A well designed service should also provide asynchronous operations.</span></span> <span data-ttu-id="15d20-134">以下是傳回使用者設定檔之 Web 服務的非同步版本。</span><span class="sxs-lookup"><span data-stu-id="15d20-134">Here is an asynchronous version of the web service that returns user profiles.</span></span> <span data-ttu-id="15d20-135">`GetUserProfileAsync` 方法取決於具有使用者設定檔服務的非同步版本。</span><span class="sxs-lookup"><span data-stu-id="15d20-135">The `GetUserProfileAsync` method depends on having an asynchronous version of the User Profile service.</span></span>

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

<span data-ttu-id="15d20-136">對於未提供非同步版本作業的程式庫，可能無法在選取之同步方法周圍建立非同步包裝函式。</span><span class="sxs-lookup"><span data-stu-id="15d20-136">For libraries that don't provide asynchronous versions of operations, it may be possible to create asynchronous wrappers around selected synchronous methods.</span></span> <span data-ttu-id="15d20-137">請謹慎遵循此方法。</span><span class="sxs-lookup"><span data-stu-id="15d20-137">Follow this approach with caution.</span></span> <span data-ttu-id="15d20-138">雖然這樣可以改善叫用非同步包裝函式之執行緒的回應性，但是實際上會耗用更多資源。</span><span class="sxs-lookup"><span data-stu-id="15d20-138">While it may improve responsiveness on the thread that invokes the asynchronous wrapper, it actually consumes more resources.</span></span> <span data-ttu-id="15d20-139">可能會建立額外的執行緒，而且會有與這個執行緒所完成之工作同步處理相關聯的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="15d20-139">An extra thread may be created, and there is overhead associated with synchronizing the work done by this thread.</span></span> <span data-ttu-id="15d20-140">此部落格文章中會討論一些利弊：[是否應該為同步方法公開非同步包裝函式？][async-wrappers]</span><span class="sxs-lookup"><span data-stu-id="15d20-140">Some tradeoffs are discussed in this blog post: [Should I expose asynchronous wrappers for synchronous methods?][async-wrappers]</span></span>

<span data-ttu-id="15d20-141">以下是同步方法周圍非同步包裝函式的範例。</span><span class="sxs-lookup"><span data-stu-id="15d20-141">Here is an example of an asynchronous wrapper around a synchronous method.</span></span>

```csharp
// Asynchronous wrapper around synchronous library method
private async Task<int> LibraryIOOperationAsync()
{
    return await Task.Run(() => LibraryIOOperation());
}
```

<span data-ttu-id="15d20-142">現在，呼叫程式碼可以在包裝函式上等候：</span><span class="sxs-lookup"><span data-stu-id="15d20-142">Now the calling code can await on the wrapper:</span></span>

```csharp
// Invoke the asynchronous wrapper using a task
await LibraryIOOperationAsync();
```

## <a name="considerations"></a><span data-ttu-id="15d20-143">考量</span><span class="sxs-lookup"><span data-stu-id="15d20-143">Considerations</span></span>

- <span data-ttu-id="15d20-144">預期有非常短存留期且不太可能造成爭用的 I/O 作業，可能會比同步作業更有效能。</span><span class="sxs-lookup"><span data-stu-id="15d20-144">I/O operations that are expected to be very short lived and are unlikely to cause contention might be more performant as synchronous operations.</span></span> <span data-ttu-id="15d20-145">範例可能會讀取 SSD 磁碟機上的小型檔案。</span><span class="sxs-lookup"><span data-stu-id="15d20-145">An example might be reading small files on an SSD drive.</span></span> <span data-ttu-id="15d20-146">當工作完成時將工作分派到另一個執行緒，並且與該執行緒同步處理的額外負荷，可能會高於非同步 I/O 的效益。</span><span class="sxs-lookup"><span data-stu-id="15d20-146">The overhead of dispatching a task to another thread, and synchronizing with that thread when the task completes, might outweigh the benefits of asynchronous I/O.</span></span> <span data-ttu-id="15d20-147">不過，這些案例相當罕見，大部分 I/O 作業應該以非同步方式完成。</span><span class="sxs-lookup"><span data-stu-id="15d20-147">However, these cases are relatively rare, and most I/O operations should be done asynchronously.</span></span>

- <span data-ttu-id="15d20-148">改善 I/O 效能可能會導致系統的其他部分變成瓶頸。</span><span class="sxs-lookup"><span data-stu-id="15d20-148">Improving I/O performance may cause other parts of the system to become bottlenecks.</span></span> <span data-ttu-id="15d20-149">例如，解除封鎖執行緒可能會導致共用資源的更大量並行要求，進而造成資源耗盡或節流。</span><span class="sxs-lookup"><span data-stu-id="15d20-149">For example, unblocking threads might result in a higher volume of concurrent requests to shared resources, leading in turn to resource starvation or throttling.</span></span> <span data-ttu-id="15d20-150">如果造成問題，您可能需要相應放大網頁伺服器或磁碟分割資料存放區的數目，以減少爭用。</span><span class="sxs-lookup"><span data-stu-id="15d20-150">If that becomes a problem, you might need to scale out the number of web servers or partition data stores to reduce contention.</span></span>

## <a name="how-to-detect-the-problem"></a><span data-ttu-id="15d20-151">如何偵測問題</span><span class="sxs-lookup"><span data-stu-id="15d20-151">How to detect the problem</span></span>

<span data-ttu-id="15d20-152">對於使用者而言，應用程式可能看起來沒有回應或似乎定期停止回應。</span><span class="sxs-lookup"><span data-stu-id="15d20-152">For users, the application may seem unresponsive or appear to hang periodically.</span></span> <span data-ttu-id="15d20-153">應用程式可能會因逾時例外狀況而失敗。</span><span class="sxs-lookup"><span data-stu-id="15d20-153">The application might fail with timeout exceptions.</span></span> <span data-ttu-id="15d20-154">這些失敗也會傳回 HTTP 500 (內部伺服器) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="15d20-154">These failures could also return HTTP 500 (Internal Server) errors.</span></span> <span data-ttu-id="15d20-155">在伺服器上，連入用戶端要求可能會遭到封鎖，直到執行緒變成可用，導致過度的要求佇列長度，顯示為 HTTP 503 (服務無法使用) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="15d20-155">On the server, incoming client requests might be blocked until a thread becomes available, resulting in excessive request queue lengths, manifested as HTTP 503 (Service Unavailable) errors.</span></span>

<span data-ttu-id="15d20-156">您可以執行下列步驟來協助識別問題：</span><span class="sxs-lookup"><span data-stu-id="15d20-156">You can perform the following steps to help identify the problem:</span></span>

1. <span data-ttu-id="15d20-157">監視生產系統，並且判斷封鎖的背景工作執行緒是否約束輸送量。</span><span class="sxs-lookup"><span data-stu-id="15d20-157">Monitor the production system and determine whether blocked worker threads are constraining throughput.</span></span>

2. <span data-ttu-id="15d20-158">如果要求因為缺乏執行緒而遭到封鎖，請檢閱應用程式以判斷哪些作業可能以同步方式執行 I/O。</span><span class="sxs-lookup"><span data-stu-id="15d20-158">If requests are being blocked due to lack of threads, review the application to determine which operations may be performing I/O synchronously.</span></span>

3. <span data-ttu-id="15d20-159">對執行同步 I/O 的每個作業執行受控制的負載測試，以找出是否是這些作業影響系統效能。</span><span class="sxs-lookup"><span data-stu-id="15d20-159">Perform controlled load testing of each operation that is performing synchronous I/O, to find out whether those operations are affecting system performance.</span></span>

## <a name="example-diagnosis"></a><span data-ttu-id="15d20-160">範例診斷</span><span class="sxs-lookup"><span data-stu-id="15d20-160">Example diagnosis</span></span>

<span data-ttu-id="15d20-161">下列各節會將這些步驟套用到稍早所述的範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="15d20-161">The following sections apply these steps to the sample application described earlier.</span></span>

### <a name="monitor-web-server-performance"></a><span data-ttu-id="15d20-162">監視網頁伺服器效能</span><span class="sxs-lookup"><span data-stu-id="15d20-162">Monitor web server performance</span></span>

<span data-ttu-id="15d20-163">針對 Azure Web 應用程式和 Web 角色，值得監視 IIS 網頁伺服器的效能。</span><span class="sxs-lookup"><span data-stu-id="15d20-163">For Azure web applications and web roles, it's worth monitoring the performance of the IIS web server.</span></span> <span data-ttu-id="15d20-164">特別注意要建立的要求佇列長度，要求是否在高度活動期間等候可用執行緒而遭到封鎖。</span><span class="sxs-lookup"><span data-stu-id="15d20-164">In particular, pay attention to the request queue length to establish whether requests are being blocked waiting for available threads during periods of high activity.</span></span> <span data-ttu-id="15d20-165">您可以啟用 Azure 診斷來蒐集這項資訊。</span><span class="sxs-lookup"><span data-stu-id="15d20-165">You can gather this information by enabling Azure diagnostics.</span></span> <span data-ttu-id="15d20-166">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="15d20-166">For more information, see:</span></span>

- <span data-ttu-id="15d20-167">[監視 Azure App Service 中的應用程式][web-sites-monitor]</span><span class="sxs-lookup"><span data-stu-id="15d20-167">[Monitor Apps in Azure App Service][web-sites-monitor]</span></span>
- <span data-ttu-id="15d20-168">[在 Azure 應用程式中建立及使用效能計數器][performance-counters]</span><span class="sxs-lookup"><span data-stu-id="15d20-168">[Create and use performance counters in an Azure application][performance-counters]</span></span>

<span data-ttu-id="15d20-169">檢測應用程式以查看要求在被接受之後的處理方式。</span><span class="sxs-lookup"><span data-stu-id="15d20-169">Instrument the application to see how requests are handled once they have been accepted.</span></span> <span data-ttu-id="15d20-170">追蹤要求的流程可協助識別它是否執行緩慢執行呼叫，以及封鎖目前的執行緒。</span><span class="sxs-lookup"><span data-stu-id="15d20-170">Tracing the flow of a request can help to identify whether it is performing slow-running calls and blocking the current thread.</span></span> <span data-ttu-id="15d20-171">執行緒分析也可以醒目提示遭到封鎖的要求。</span><span class="sxs-lookup"><span data-stu-id="15d20-171">Thread profiling can also highlight requests that are being blocked.</span></span>

### <a name="load-test-the-application"></a><span data-ttu-id="15d20-172">對應用程式進行負載測試</span><span class="sxs-lookup"><span data-stu-id="15d20-172">Load test the application</span></span>

<span data-ttu-id="15d20-173">下圖顯示前述之同步 `GetUserProfile` 方法在最多 4000 個並行使用者之各種負載下的效能。</span><span class="sxs-lookup"><span data-stu-id="15d20-173">The following graph shows the performance of the synchronous `GetUserProfile` method shown earlier, under varying loads of up to 4000 concurrent users.</span></span> <span data-ttu-id="15d20-174">應用程式是 Azure 雲端服務 Web 角色中執行的 ASP.NET 應用程式。</span><span class="sxs-lookup"><span data-stu-id="15d20-174">The application is an ASP.NET application running in an Azure Cloud Service web role.</span></span>

![執行同步 I/O 作業之範例應用程式的效能圖表][sync-performance]

<span data-ttu-id="15d20-176">同步作業硬式編碼為睡眠 2 秒，以模擬同步 I/O，因此最小回應時間會稍微超過 2 秒。</span><span class="sxs-lookup"><span data-stu-id="15d20-176">The synchronous operation is hard-coded to sleep for 2 seconds, to simulate synchronous I/O, so the minimum response time is slightly over 2 seconds.</span></span> <span data-ttu-id="15d20-177">當負載達到約 2500 個並行使用者時，平均回應時間會到達高原，雖然每秒的要求量會繼續增加。</span><span class="sxs-lookup"><span data-stu-id="15d20-177">When the load reaches approximately 2500 concurrent users, the average response time reaches a plateau, although the volume of requests per second continues to increase.</span></span> <span data-ttu-id="15d20-178">請注意，這兩個量值的規模為對數。</span><span class="sxs-lookup"><span data-stu-id="15d20-178">Note that the scale for these two measures is logarithmic.</span></span> <span data-ttu-id="15d20-179">每秒要求數在此點與測試結束之間加倍。</span><span class="sxs-lookup"><span data-stu-id="15d20-179">The number of requests per second doubles between this point and the end of the test.</span></span>

<span data-ttu-id="15d20-180">在隔離中，同步 I/O 是否是問題在此測試中不一定明顯。</span><span class="sxs-lookup"><span data-stu-id="15d20-180">In isolation, it's not necessarily clear from this test whether the synchronous I/O is a problem.</span></span> <span data-ttu-id="15d20-181">在較重負載之下，應用程式可能會達到引爆點，網頁伺服器再也無法及時處理要求，導致用戶端應用程式收到逾時例外狀況。</span><span class="sxs-lookup"><span data-stu-id="15d20-181">Under heavier load, the application may reach a tipping point where the web server can no longer process requests in a timely manner, causing client applications to receive time-out exceptions.</span></span>

<span data-ttu-id="15d20-182">連入要求是由 IIS 網頁伺服器排入佇列，並且交付給在 ASP.NET 執行緒集區中執行的執行緒。</span><span class="sxs-lookup"><span data-stu-id="15d20-182">Incoming requests are queued by the IIS web server and handed to a thread running in the ASP.NET thread pool.</span></span> <span data-ttu-id="15d20-183">因為每個作業會以同步方式執行 I/O，所以執行緒在作業完成之前都會遭到封鎖。</span><span class="sxs-lookup"><span data-stu-id="15d20-183">Because each operation performs I/O synchronously, the thread is blocked until the operation completes.</span></span> <span data-ttu-id="15d20-184">隨著工作負載增加，最後執行緒集區中的所有 ASP.NET 執行緒都會配置及封鎖。</span><span class="sxs-lookup"><span data-stu-id="15d20-184">As the workload increases, eventually all of the ASP.NET threads in the thread pool are allocated and blocked.</span></span> <span data-ttu-id="15d20-185">屆時任何進一步的連入要求都必須在佇列中等待可用的執行緒。</span><span class="sxs-lookup"><span data-stu-id="15d20-185">At that point, any further incoming requests must wait in the queue for an available thread.</span></span> <span data-ttu-id="15d20-186">隨著佇列長度成長，要求會開始逾時。</span><span class="sxs-lookup"><span data-stu-id="15d20-186">As the queue length grows, requests start to time out.</span></span>

### <a name="implement-the-solution-and-verify-the-result"></a><span data-ttu-id="15d20-187">實作解決方案並確認結果</span><span class="sxs-lookup"><span data-stu-id="15d20-187">Implement the solution and verify the result</span></span>

<span data-ttu-id="15d20-188">下圖顯示針對非同步版本程式碼進行負載測試的結果。</span><span class="sxs-lookup"><span data-stu-id="15d20-188">The next graph shows the results from load testing the asynchronous version of the code.</span></span>

![執行非同步 I/O 作業之範例應用程式的效能圖表][async-performance]

<span data-ttu-id="15d20-190">輸送量遠遠高出許多。</span><span class="sxs-lookup"><span data-stu-id="15d20-190">Throughput is far higher.</span></span> <span data-ttu-id="15d20-191">在與前一個測試相同的持續時間裡面，系統成功處理增加將近十倍的輸送量，以每秒的要求進行測量。</span><span class="sxs-lookup"><span data-stu-id="15d20-191">Over the same duration as the previous test, the system successfully handles a nearly tenfold increase in throughput, as measured in requests per second.</span></span> <span data-ttu-id="15d20-192">此外，平均回應時間相對穩定，而且維持比前一個測試少了大約 25 倍。</span><span class="sxs-lookup"><span data-stu-id="15d20-192">Moreover, the average response time is relatively constant and remains approximately 25 times smaller than the previous test.</span></span>

[sample-app]: https://github.com/mspnp/performance-optimization/tree/master/SynchronousIO
[async-wrappers]: https://blogs.msdn.microsoft.com/pfxteam/2012/03/24/should-i-expose-asynchronous-wrappers-for-synchronous-methods/
[performance-counters]: /azure/cloud-services/cloud-services-dotnet-diagnostics-performance-counters
[web-sites-monitor]: /azure/app-service-web/web-sites-monitor

[sync-performance]: _images/SyncPerformance.jpg
[async-performance]: _images/AsyncPerformance.jpg

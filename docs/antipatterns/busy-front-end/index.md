---
title: 忙碌前端反模式
titleSuffix: Performance antipatterns for cloud apps
description: 在大量背景執行緒上進行的非同步工作，會讓資源的其他前景工作無資源可用。
author: dragon119
ms.date: 06/05/2017
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 61470b630f735c1d49ad9b4bfbec853b308630cf
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54481519"
---
# <a name="busy-front-end-antipattern"></a><span data-ttu-id="c10f0-103">忙碌前端反模式</span><span class="sxs-lookup"><span data-stu-id="c10f0-103">Busy Front End antipattern</span></span>

<span data-ttu-id="c10f0-104">在大量背景執行緒上執行非同步工作，會讓資源的其他並行前景工作無資源可用，減少對於無法接受層級的回應時間。</span><span class="sxs-lookup"><span data-stu-id="c10f0-104">Performing asynchronous work on a large number of background threads can starve other concurrent foreground tasks of resources, decreasing response times to unacceptable levels.</span></span>

## <a name="problem-description"></a><span data-ttu-id="c10f0-105">問題說明</span><span class="sxs-lookup"><span data-stu-id="c10f0-105">Problem description</span></span>

<span data-ttu-id="c10f0-106">耗用大量資源的工作會增加使用者要求的回應時間，並且導致高延遲。</span><span class="sxs-lookup"><span data-stu-id="c10f0-106">Resource-intensive tasks can increase the response times for user requests and cause high latency.</span></span> <span data-ttu-id="c10f0-107">改善回應時間的一種方法是將耗用大量資源的工作卸載至個別執行緒。</span><span class="sxs-lookup"><span data-stu-id="c10f0-107">One way to improve response times is to offload a resource-intensive task to a separate thread.</span></span> <span data-ttu-id="c10f0-108">這種方法可讓應用程式在背景處理時保持回應。</span><span class="sxs-lookup"><span data-stu-id="c10f0-108">This approach lets the application stay responsive while processing happens in the background.</span></span> <span data-ttu-id="c10f0-109">不過，在背景執行緒上執行的工作仍會耗用資源。</span><span class="sxs-lookup"><span data-stu-id="c10f0-109">However, tasks that run on a background thread still consume resources.</span></span> <span data-ttu-id="c10f0-110">如果有太多此類工作，它們會讓處理要求的執行緒無資源可用。</span><span class="sxs-lookup"><span data-stu-id="c10f0-110">If there are too many of them, they can starve the threads that are handling requests.</span></span>

> [!NOTE]
> <span data-ttu-id="c10f0-111">詞彙「資源」可以包含許多項目，例如 CPU 使用率、佔用記憶體和網路或磁碟 I/O。</span><span class="sxs-lookup"><span data-stu-id="c10f0-111">The term *resource* can encompass many things, such as CPU utilization, memory occupancy, and network or disk I/O.</span></span>

<span data-ttu-id="c10f0-112">這個問題通常會在應用程式開發作為程式碼片段，所有商務邏輯都合併至與展示層共用的單一層時發生。</span><span class="sxs-lookup"><span data-stu-id="c10f0-112">This problem typically occurs when an application is developed as monolithic piece of code, with all of the business logic combined into a single tier shared with the presentation layer.</span></span>

<span data-ttu-id="c10f0-113">以下是使用 ASP.NET 示範問題的範例。</span><span class="sxs-lookup"><span data-stu-id="c10f0-113">Here’s an example using ASP.NET that demonstrates the problem.</span></span> <span data-ttu-id="c10f0-114">您可以在[這裡][code-sample]找到完整的範例。</span><span class="sxs-lookup"><span data-stu-id="c10f0-114">You can find the complete sample [here][code-sample].</span></span>

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

- <span data-ttu-id="c10f0-115">`WorkInFrontEnd` 控制器中的 `Post` 方法會實作 HTTP POST 作業。</span><span class="sxs-lookup"><span data-stu-id="c10f0-115">The `Post` method in the `WorkInFrontEnd` controller implements an HTTP POST operation.</span></span> <span data-ttu-id="c10f0-116">這項作業會模擬長時間執行、需要大量 CPU 的工作。</span><span class="sxs-lookup"><span data-stu-id="c10f0-116">This operation simulates a long-running, CPU-intensive task.</span></span> <span data-ttu-id="c10f0-117">工作是在個別執行緒上執行，嘗試讓 POST 作業快速完成。</span><span class="sxs-lookup"><span data-stu-id="c10f0-117">The work is performed on a separate thread, in an attempt to enable the POST operation to complete quickly.</span></span>

- <span data-ttu-id="c10f0-118">`UserProfile` 控制器中的 `Get` 方法會實作 HTTP GET 作業。</span><span class="sxs-lookup"><span data-stu-id="c10f0-118">The `Get` method in the `UserProfile` controller implements an HTTP GET operation.</span></span> <span data-ttu-id="c10f0-119">這個方法是比較不需要大量 CPU。</span><span class="sxs-lookup"><span data-stu-id="c10f0-119">This method is much less CPU intensive.</span></span>

<span data-ttu-id="c10f0-120">主要考量是 `Post` 方法的資源需求。</span><span class="sxs-lookup"><span data-stu-id="c10f0-120">The primary concern is the resource requirements of the `Post` method.</span></span> <span data-ttu-id="c10f0-121">雖然它會將工作放到背景執行緒，但是工作仍然會耗用相當多的 CPU 資源。</span><span class="sxs-lookup"><span data-stu-id="c10f0-121">Although it puts the work onto a background thread, the work can still consume considerable CPU resources.</span></span> <span data-ttu-id="c10f0-122">這些資源是與其他並行使用者執行的其他作業共用。</span><span class="sxs-lookup"><span data-stu-id="c10f0-122">These resources are shared with other operations being performed by other concurrent users.</span></span> <span data-ttu-id="c10f0-123">如果適量的使用者同時傳送此要求，整體效能可能會變差，讓所有作業減緩。</span><span class="sxs-lookup"><span data-stu-id="c10f0-123">If a moderate number of users send this request at the same time, overall performance is likely to suffer, slowing down all operations.</span></span> <span data-ttu-id="c10f0-124">舉例來說，使用者可能會在 `Get` 方法中遇到重大延遲。</span><span class="sxs-lookup"><span data-stu-id="c10f0-124">Users might experience significant latency in the `Get` method, for example.</span></span>

## <a name="how-to-fix-the-problem"></a><span data-ttu-id="c10f0-125">如何修正問題</span><span class="sxs-lookup"><span data-stu-id="c10f0-125">How to fix the problem</span></span>

<span data-ttu-id="c10f0-126">將耗用大量資源的處理程序移至個別後端。</span><span class="sxs-lookup"><span data-stu-id="c10f0-126">Move processes that consume significant resources to a separate back end.</span></span>

<span data-ttu-id="c10f0-127">使用此方法，前端會將耗用大量資源的工作放入訊息佇列。</span><span class="sxs-lookup"><span data-stu-id="c10f0-127">With this approach, the front end puts resource-intensive tasks onto a message queue.</span></span> <span data-ttu-id="c10f0-128">後端會挑選工作以進行非同步處理。</span><span class="sxs-lookup"><span data-stu-id="c10f0-128">The back end picks up the tasks for asynchronous processing.</span></span> <span data-ttu-id="c10f0-129">佇列也會作為負載架橋，緩衝後端的要求。</span><span class="sxs-lookup"><span data-stu-id="c10f0-129">The queue also acts as a load leveler, buffering requests for the back end.</span></span> <span data-ttu-id="c10f0-130">如果佇列長度變得太長，您可以設定自動調整以相應放大後端。</span><span class="sxs-lookup"><span data-stu-id="c10f0-130">If the queue length becomes too long, you can configure autoscaling to scale out the back end.</span></span>

<span data-ttu-id="c10f0-131">以下是先前程式碼的修訂版本。</span><span class="sxs-lookup"><span data-stu-id="c10f0-131">Here is a revised version of the previous code.</span></span> <span data-ttu-id="c10f0-132">在此版本中，`Post` 方法會將訊息放在服務匯流排佇列。</span><span class="sxs-lookup"><span data-stu-id="c10f0-132">In this version, the `Post` method puts a message on a Service Bus queue.</span></span>

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

<span data-ttu-id="c10f0-133">後端會從服務匯流排佇列提取訊息，並進行處理。</span><span class="sxs-lookup"><span data-stu-id="c10f0-133">The back end pulls messages from the Service Bus queue and does the processing.</span></span>

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

## <a name="considerations"></a><span data-ttu-id="c10f0-134">考量</span><span class="sxs-lookup"><span data-stu-id="c10f0-134">Considerations</span></span>

- <span data-ttu-id="c10f0-135">這個方法會將一些額外的複雜性新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="c10f0-135">This approach adds some additional complexity to the application.</span></span> <span data-ttu-id="c10f0-136">您必須安全地處理佇列和清除佇列，以避免在失敗事件中遺失要求。</span><span class="sxs-lookup"><span data-stu-id="c10f0-136">You must handle queuing and dequeuing safely to avoid losing requests in the event of a failure.</span></span>
- <span data-ttu-id="c10f0-137">應用程式會接受訊息佇列之其他服務的相依性。</span><span class="sxs-lookup"><span data-stu-id="c10f0-137">The application takes a dependency on an additional service for the message queue.</span></span>
- <span data-ttu-id="c10f0-138">處理環境必須有足夠的可擴充空間，來處理預期的工作負載，並符合所需的輸送量目標。</span><span class="sxs-lookup"><span data-stu-id="c10f0-138">The processing environment must be sufficiently scalable to handle the expected workload and meet the required throughput targets.</span></span>
- <span data-ttu-id="c10f0-139">雖然這個方法應該會改善整體回應性，但是移至後端的工作可能需要更久的時間才能完成。</span><span class="sxs-lookup"><span data-stu-id="c10f0-139">While this approach should improve overall responsiveness, the tasks that are moved to the back end may take longer to complete.</span></span>

## <a name="how-to-detect-the-problem"></a><span data-ttu-id="c10f0-140">如何偵測問題</span><span class="sxs-lookup"><span data-stu-id="c10f0-140">How to detect the problem</span></span>

<span data-ttu-id="c10f0-141">忙碌前端的徵兆包括執行耗用將耗用大量資源的工作之高延遲。</span><span class="sxs-lookup"><span data-stu-id="c10f0-141">Symptoms of a busy front end include high latency when resource-intensive tasks are being performed.</span></span> <span data-ttu-id="c10f0-142">使用者可能會回報服務逾時造成的擴充回應時間或失敗。這些失敗也可能會傳回 HTTP 500 (內部伺服器) 錯誤或 HTTP 503 (服務無法使用) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="c10f0-142">End users are likely to report extended response times or failures caused by services timing out. These failures could also return HTTP 500 (Internal Server) errors or HTTP 503 (Service Unavailable) errors.</span></span> <span data-ttu-id="c10f0-143">檢查網頁伺服器的事件記錄，其中可能包含錯誤原因和情況的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="c10f0-143">Examine the event logs for the web server, which are likely to contain more detailed information about the causes and circumstances of the errors.</span></span>

<span data-ttu-id="c10f0-144">您可以執行下列步驟來協助識別此問題：</span><span class="sxs-lookup"><span data-stu-id="c10f0-144">You can perform the following steps to help identify this problem:</span></span>

1. <span data-ttu-id="c10f0-145">執行生產系統的處理程序監視，以識別回應時間變慢的時間點。</span><span class="sxs-lookup"><span data-stu-id="c10f0-145">Perform process monitoring of the production system, to identify points when response times slow down.</span></span>
2. <span data-ttu-id="c10f0-146">檢查在這些點所擷取的遙測資料，來判斷執行之作業與使用之資源的混合。</span><span class="sxs-lookup"><span data-stu-id="c10f0-146">Examine the telemetry data captured at these points to determine the mix of operations being performed and the resources being used.</span></span>
3. <span data-ttu-id="c10f0-147">尋找回應時間不佳與當時發生之作業量和組合之間的任何相互關聯。</span><span class="sxs-lookup"><span data-stu-id="c10f0-147">Find any correlations between poor response times and the volumes and combinations of operations that were happening at those times.</span></span>
4. <span data-ttu-id="c10f0-148">對每個懷疑的作業進行負載測試，以識別哪些作業耗用資源，並且讓其他作業無資源可用。</span><span class="sxs-lookup"><span data-stu-id="c10f0-148">Load test each suspected operation to identify which operations are consuming resources and starving other operations.</span></span>
5. <span data-ttu-id="c10f0-149">檢閱這些作業的原始程式碼，來判斷為何它們可能會導致過量的資源耗用。</span><span class="sxs-lookup"><span data-stu-id="c10f0-149">Review the source code for those operations to determine why they might cause excessive resource consumption.</span></span>

## <a name="example-diagnosis"></a><span data-ttu-id="c10f0-150">範例診斷</span><span class="sxs-lookup"><span data-stu-id="c10f0-150">Example diagnosis</span></span>

<span data-ttu-id="c10f0-151">下列各節會將這些步驟套用到稍早所述的範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="c10f0-151">The following sections apply these steps to the sample application described earlier.</span></span>

### <a name="identify-points-of-slowdown"></a><span data-ttu-id="c10f0-152">識別變慢的點</span><span class="sxs-lookup"><span data-stu-id="c10f0-152">Identify points of slowdown</span></span>

<span data-ttu-id="c10f0-153">檢測每個方法來追蹤每個要求耗用的持續時間和資源。</span><span class="sxs-lookup"><span data-stu-id="c10f0-153">Instrument each method to track the duration and resources consumed by each request.</span></span> <span data-ttu-id="c10f0-154">然後在生產中監視應用程式。</span><span class="sxs-lookup"><span data-stu-id="c10f0-154">Then monitor the application in production.</span></span> <span data-ttu-id="c10f0-155">這樣可以提供要求如何彼此競爭的整體檢視。</span><span class="sxs-lookup"><span data-stu-id="c10f0-155">This can provide an overall view of how requests compete with each other.</span></span> <span data-ttu-id="c10f0-156">在壓力期間，緩慢執行且渴望資源的要求可能會影響其他作業，此行為可以透過監視系統和記下效能的下滑來觀察。</span><span class="sxs-lookup"><span data-stu-id="c10f0-156">During periods of stress, slow-running resource-hungry requests will likely impact other operations, and this behavior can be observed by monitoring the system and noting the drop off in performance.</span></span>

<span data-ttu-id="c10f0-157">下圖顯示監視儀表板。</span><span class="sxs-lookup"><span data-stu-id="c10f0-157">The following image shows a monitoring dashboard.</span></span> <span data-ttu-id="c10f0-158">(我們針對測試使用 [AppDynamics]。)一開始，系統有輕微的負載。</span><span class="sxs-lookup"><span data-stu-id="c10f0-158">(We used [AppDynamics] for our tests.) Initially, the system has light load.</span></span> <span data-ttu-id="c10f0-159">然後使用者開始要求 `UserProfile` GET 方法。</span><span class="sxs-lookup"><span data-stu-id="c10f0-159">Then users start requesting the `UserProfile` GET method.</span></span> <span data-ttu-id="c10f0-160">效能還相當良好，直到其他使用者開始對 `WorkInFrontEnd` POST 方法發出要求。</span><span class="sxs-lookup"><span data-stu-id="c10f0-160">The performance is reasonably good until other users start issuing requests to the `WorkInFrontEnd` POST method.</span></span> <span data-ttu-id="c10f0-161">屆時，回應時間會大幅提高 (第一個箭號)。</span><span class="sxs-lookup"><span data-stu-id="c10f0-161">At that point, response times increase dramatically (first arrow).</span></span> <span data-ttu-id="c10f0-162">回應時間只會在對 `WorkInFrontEnd` 控制器的要求數量減少之後改善 (第二個箭號)。</span><span class="sxs-lookup"><span data-stu-id="c10f0-162">Response times only improve after the volume of requests to the `WorkInFrontEnd` controller diminishes (second arrow).</span></span>

![AppDynamics 業務交易窗格顯示當使用 WorkInFrontEnd 控制器時，所有要求之回應時間的影響][AppDynamics-Transactions-Front-End-Requests]

### <a name="examine-telemetry-data-and-find-correlations"></a><span data-ttu-id="c10f0-164">檢查遙測資料並尋找相互關聯</span><span class="sxs-lookup"><span data-stu-id="c10f0-164">Examine telemetry data and find correlations</span></span>

<span data-ttu-id="c10f0-165">下圖顯示用來在相同的間隔期間監視資源使用率，所蒐集的計量。</span><span class="sxs-lookup"><span data-stu-id="c10f0-165">The next image shows some of the metrics gathered to monitor resource utilization during the same interval.</span></span> <span data-ttu-id="c10f0-166">一開始，只有少數使用者存取系統。</span><span class="sxs-lookup"><span data-stu-id="c10f0-166">At first, few users are accessing the system.</span></span> <span data-ttu-id="c10f0-167">隨著越來越多的使用者連線，CPU 使用率變得非常高 (100%)。</span><span class="sxs-lookup"><span data-stu-id="c10f0-167">As more users connect, CPU utilization becomes very high (100%).</span></span> <span data-ttu-id="c10f0-168">另外請注意，網路 I/O 速率一開始會隨著 CPU 使用率上升。</span><span class="sxs-lookup"><span data-stu-id="c10f0-168">Also notice that the network I/O rate initially goes up as CPU usage rises.</span></span> <span data-ttu-id="c10f0-169">但是一旦達到 CPU 使用率尖峰，網路 I/O 實際上會降低。</span><span class="sxs-lookup"><span data-stu-id="c10f0-169">But once CPU usage peaks, network I/O actually goes down.</span></span> <span data-ttu-id="c10f0-170">這是因為系統在 CPU 容量被佔據時，只能處理相對少數的要求。</span><span class="sxs-lookup"><span data-stu-id="c10f0-170">That's because the system can only handle a relatively small number of requests once the CPU is at capacity.</span></span> <span data-ttu-id="c10f0-171">當使用者中斷連線時，CPU 負載就會變小。</span><span class="sxs-lookup"><span data-stu-id="c10f0-171">As users disconnect, the CPU load tails off.</span></span>

![AppDynamics 計量顯示 CPU 和網路使用率][AppDynamics-Metrics-Front-End-Requests]

<span data-ttu-id="c10f0-173">此時，`WorkInFrontEnd` 控制器中的 `Post` 方法似乎是供進一步調查的主要候選項目。</span><span class="sxs-lookup"><span data-stu-id="c10f0-173">At this point, it appears the `Post` method in the `WorkInFrontEnd` controller is a prime candidate for closer examination.</span></span> <span data-ttu-id="c10f0-174">必須在受控制的環境中執行進一步的工作，才能確認假設。</span><span class="sxs-lookup"><span data-stu-id="c10f0-174">Further work in a controlled environment is needed to confirm the hypothesis.</span></span>

### <a name="perform-load-testing"></a><span data-ttu-id="c10f0-175">執行負載測試</span><span class="sxs-lookup"><span data-stu-id="c10f0-175">Perform load testing</span></span>

<span data-ttu-id="c10f0-176">下一個步驟是在受控制的環境中執行測試。</span><span class="sxs-lookup"><span data-stu-id="c10f0-176">The next step is to perform tests in a controlled environment.</span></span> <span data-ttu-id="c10f0-177">例如，執行所包含的一系列負載測試，然後省略每個要求以查看效果。</span><span class="sxs-lookup"><span data-stu-id="c10f0-177">For example, run a series of load tests that include and then omit each request in turn to see the effects.</span></span>

<span data-ttu-id="c10f0-178">下圖顯示對先前測試中使用之雲端服務的相同部署執行的負載測試結果。</span><span class="sxs-lookup"><span data-stu-id="c10f0-178">The graph below shows the results of a load test performed against an identical deployment of the cloud service used in the previous tests.</span></span> <span data-ttu-id="c10f0-179">測試使用 500 個使用者的常數負載，在 `UserProfile` 控制器中執行 `Get` 作業，以及使用者的步驟負載，在 `WorkInFrontEnd` 控制器中執行 `Post` 作業。</span><span class="sxs-lookup"><span data-stu-id="c10f0-179">The test used a constant load of 500 users performing the `Get` operation in the `UserProfile` controller, along with a step load of users performing the `Post` operation in the `WorkInFrontEnd` controller.</span></span>

![WorkInFrontEnd 控制器的初始負載測試結果][Initial-Load-Test-Results-Front-End]

<span data-ttu-id="c10f0-181">一開始，步驟負載是 0，因此只有一個作用中使用者執行 `UserProfile` 要求。</span><span class="sxs-lookup"><span data-stu-id="c10f0-181">Initially, the step load is 0, so the only active users are performing the `UserProfile` requests.</span></span> <span data-ttu-id="c10f0-182">系統可以回應大約 500 個每秒要求數。</span><span class="sxs-lookup"><span data-stu-id="c10f0-182">The system is able to respond to approximately 500 requests per second.</span></span> <span data-ttu-id="c10f0-183">60 秒之後，100 個額外使用者的負載會開始將 POST 要求傳送至 `WorkInFrontEnd` 控制器。</span><span class="sxs-lookup"><span data-stu-id="c10f0-183">After 60 seconds, a load of 100 additional users starts sending POST requests to the `WorkInFrontEnd` controller.</span></span> <span data-ttu-id="c10f0-184">工作負載幾乎是立即傳送至 `UserProfile` 控制器，下降到大約 150 個每秒要求數。</span><span class="sxs-lookup"><span data-stu-id="c10f0-184">Almost immediately, the workload sent to the `UserProfile` controller drops to about 150 requests per second.</span></span> <span data-ttu-id="c10f0-185">這是由於負載測試執行器函式的方式。</span><span class="sxs-lookup"><span data-stu-id="c10f0-185">This is due to the way the load-test runner functions.</span></span> <span data-ttu-id="c10f0-186">它會在傳送下一個要求之前等候回應，因此收到回應的時間越久，要求速率越低。</span><span class="sxs-lookup"><span data-stu-id="c10f0-186">It waits for a response before sending the next request, so the longer it takes to receive a response, the lower the request rate.</span></span>

<span data-ttu-id="c10f0-187">隨著越來越多使用者將 POST 要求傳送至 `WorkInFrontEnd` 控制器，`UserProfile` 控制器的回應速率持續下降。</span><span class="sxs-lookup"><span data-stu-id="c10f0-187">As more users send POST requests to the `WorkInFrontEnd` controller, the response rate of the `UserProfile` controller continues to drop.</span></span> <span data-ttu-id="c10f0-188">不過請注意，由 `WorkInFrontEnd` 控制器處理的要求量仍然相對穩定。</span><span class="sxs-lookup"><span data-stu-id="c10f0-188">But note that the volume of requests handled by the `WorkInFrontEnd`controller remains relatively constant.</span></span> <span data-ttu-id="c10f0-189">系統的飽和度變得很明顯，因為這兩個要求的整體速率都傾向於穩定但低限制。</span><span class="sxs-lookup"><span data-stu-id="c10f0-189">The saturation of the system becomes apparent as the overall rate of both requests tends towards a steady but low limit.</span></span>

### <a name="review-the-source-code"></a><span data-ttu-id="c10f0-190">檢閱原始程式碼</span><span class="sxs-lookup"><span data-stu-id="c10f0-190">Review the source code</span></span>

<span data-ttu-id="c10f0-191">最後一個步驟是檢查原始程式碼。</span><span class="sxs-lookup"><span data-stu-id="c10f0-191">The final step is to look at the source code.</span></span> <span data-ttu-id="c10f0-192">開發小組已知道，`Post` 方法可能需要花一些時間，這就是為什麼原始實作會使用個別執行緒。</span><span class="sxs-lookup"><span data-stu-id="c10f0-192">The development team was aware that the `Post` method could take a considerable amount of time, which is why the original implementation used a separate thread.</span></span> <span data-ttu-id="c10f0-193">那樣可以解決當前的問題，因為 `Post` 方法在等候長時間執行工作完成時未封鎖。</span><span class="sxs-lookup"><span data-stu-id="c10f0-193">That solved the immediate problem, because the `Post` method did not block waiting for a long-running task to complete.</span></span>

<span data-ttu-id="c10f0-194">不過，這個方法所執行的工作仍會耗用 CPU、記憶體和其他資源。</span><span class="sxs-lookup"><span data-stu-id="c10f0-194">However, the work performed by this method still consumes CPU, memory, and other resources.</span></span> <span data-ttu-id="c10f0-195">啟用此處理程序以非同步方式執行實際上可能會損害效能，因為使用者可以未受控制的方式同時觸發這些大量作業。</span><span class="sxs-lookup"><span data-stu-id="c10f0-195">Enabling this process to run asynchronously might actually damage performance, as users can trigger a large number of these operations simultaneously, in an uncontrolled manner.</span></span> <span data-ttu-id="c10f0-196">伺服器可以執行的執行緒數目有限制。</span><span class="sxs-lookup"><span data-stu-id="c10f0-196">There is a limit to the number of threads that a server can run.</span></span> <span data-ttu-id="c10f0-197">超過此限制，應用程式很可能會在嘗試啟動新執行緒時，發生例外狀況。</span><span class="sxs-lookup"><span data-stu-id="c10f0-197">Past this limit, the application is likely to get an exception when it tries to start a new thread.</span></span>

> [!NOTE]
> <span data-ttu-id="c10f0-198">這並不表示您應該避免非同步作業。</span><span class="sxs-lookup"><span data-stu-id="c10f0-198">This doesn't mean you should avoid asynchronous operations.</span></span> <span data-ttu-id="c10f0-199">在網路呼叫上執行非同步等候是建議的做法。</span><span class="sxs-lookup"><span data-stu-id="c10f0-199">Performing an asynchronous await on a network call is a recommended practice.</span></span> <span data-ttu-id="c10f0-200">(請參閱[同步 I/O][sync-io] 反模式。)此處的問題是需要大量 CPU 的工作已在另一個執行緒上繁衍。</span><span class="sxs-lookup"><span data-stu-id="c10f0-200">(See the [Synchronous I/O][sync-io] antipattern.) The problem here is that CPU-intensive work was spawned on another thread.</span></span>

### <a name="implement-the-solution-and-verify-the-result"></a><span data-ttu-id="c10f0-201">實作解決方案並確認結果</span><span class="sxs-lookup"><span data-stu-id="c10f0-201">Implement the solution and verify the result</span></span>

<span data-ttu-id="c10f0-202">下圖顯示實作解決方案之後的效能監視。</span><span class="sxs-lookup"><span data-stu-id="c10f0-202">The following image shows performance monitoring after the solution was implemented.</span></span> <span data-ttu-id="c10f0-203">負載與稍早所示類似，但是 `UserProfile` 控制器的回應時間現在更快。</span><span class="sxs-lookup"><span data-stu-id="c10f0-203">The load was similar to that shown earlier, but the response times for the `UserProfile` controller are now much faster.</span></span> <span data-ttu-id="c10f0-204">相同持續期間內增加的要求數量，從 2759 到 23565。</span><span class="sxs-lookup"><span data-stu-id="c10f0-204">The volume of requests increased over the same duration, from 2,759 to 23,565.</span></span>

![AppDynamics 業務交易窗格顯示當使用 WorkInBackground 控制器時，所有要求之回應時間的影響][AppDynamics-Transactions-Background-Requests]

<span data-ttu-id="c10f0-206">請注意，`WorkInBackground` 控制器也會處理較大量的要求。</span><span class="sxs-lookup"><span data-stu-id="c10f0-206">Note that the `WorkInBackground` controller also handled a much larger volume of requests.</span></span> <span data-ttu-id="c10f0-207">不過，您無法在此情況下進行直接比較，因為在此控制器中執行的工作與原始程式碼非常不同。</span><span class="sxs-lookup"><span data-stu-id="c10f0-207">However, you can't make a direct comparison in this case, because the work being performed in this controller is very different from the original code.</span></span> <span data-ttu-id="c10f0-208">新版本只會將要求排入佇列，而不是執行耗時的計算。</span><span class="sxs-lookup"><span data-stu-id="c10f0-208">The new version simply queues a request, rather than performing a time consuming calculation.</span></span> <span data-ttu-id="c10f0-209">主要的重點是這個方法不會再於負載之下拖慢整個系統。</span><span class="sxs-lookup"><span data-stu-id="c10f0-209">The main point is that this method no longer drags down the entire system under load.</span></span>

<span data-ttu-id="c10f0-210">CPU 和網路使用率也會顯示改善的效能。</span><span class="sxs-lookup"><span data-stu-id="c10f0-210">CPU and network utilization also show the improved performance.</span></span> <span data-ttu-id="c10f0-211">CPU 使用率永遠不會達到 100%，而且處理的網路要求數量遠大於舊版，直到卸除工作負載之前都不會變小。</span><span class="sxs-lookup"><span data-stu-id="c10f0-211">The CPU utilization never reached 100%, and the volume of handled network requests was far greater than earlier, and did not tail off until the workload dropped.</span></span>

![AppDynamics 計量顯示 WorkInBackground 控制器的 CPU 和網路使用率][AppDynamics-Metrics-Background-Requests]

<span data-ttu-id="c10f0-213">下圖顯示負載測試的結果。</span><span class="sxs-lookup"><span data-stu-id="c10f0-213">The following graph shows the results of a load test.</span></span> <span data-ttu-id="c10f0-214">為要求提供服務的整體數量相較於舊版測試大幅提升。</span><span class="sxs-lookup"><span data-stu-id="c10f0-214">The overall volume of requests serviced is greatly improved compared to the the earlier tests.</span></span>

![BackgroundImageProcessing 控制器的負載測試結果][Load-Test-Results-Background]

## <a name="related-guidance"></a><span data-ttu-id="c10f0-216">相關的指引</span><span class="sxs-lookup"><span data-stu-id="c10f0-216">Related guidance</span></span>

- <span data-ttu-id="c10f0-217">[自動調整最佳做法][autoscaling]</span><span class="sxs-lookup"><span data-stu-id="c10f0-217">[Autoscaling best practices][autoscaling]</span></span>
- <span data-ttu-id="c10f0-218">[背景作業最佳做法][background-jobs]</span><span class="sxs-lookup"><span data-stu-id="c10f0-218">[Background jobs best practices][background-jobs]</span></span>
- <span data-ttu-id="c10f0-219">[佇列型負載調節模式][load-leveling]</span><span class="sxs-lookup"><span data-stu-id="c10f0-219">[Queue-Based Load Leveling pattern][load-leveling]</span></span>
- <span data-ttu-id="c10f0-220">[Web 佇列背景工作角色架構樣式][web-queue-worker]</span><span class="sxs-lookup"><span data-stu-id="c10f0-220">[Web Queue Worker architecture style][web-queue-worker]</span></span>

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

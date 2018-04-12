---
title: 斷路器
description: 在連線到遠端服務或資源時，處理可能需要不同時間來修復的錯誤。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories:
- resiliency
ms.openlocfilehash: 0f93c1ef664c8e7385895e3854835699f674ee0e
ms.sourcegitcommit: c441fd165e6bebbbbbc19854ec6f3676be9c3b25
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2018
---
# <a name="circuit-breaker-pattern"></a><span data-ttu-id="cb211-104">斷路器模式</span><span class="sxs-lookup"><span data-stu-id="cb211-104">Circuit Breaker pattern</span></span>

<span data-ttu-id="cb211-105">在連線到遠端服務或資源時，處理可能需要不同時間來復原的錯誤。</span><span class="sxs-lookup"><span data-stu-id="cb211-105">Handle faults that might take a variable amount of time to recover from, when connecting to a remote service or resource.</span></span> <span data-ttu-id="cb211-106">這可以改善應用程式的穩定性和恢復功能。</span><span class="sxs-lookup"><span data-stu-id="cb211-106">This can improve the stability and resiliency of an application.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="cb211-107">內容和問題</span><span class="sxs-lookup"><span data-stu-id="cb211-107">Context and problem</span></span>

<span data-ttu-id="cb211-108">在分散式環境中，對遠端資源和服務進行的呼叫可能會因為暫時性錯誤而失敗，例如低速網路連線、逾時、資源過度認可或資源暫時無法使用。</span><span class="sxs-lookup"><span data-stu-id="cb211-108">In a distributed environment, calls to remote resources and services can fail due to transient faults, such as slow network connections, timeouts, or the resources being overcommitted or temporarily unavailable.</span></span> <span data-ttu-id="cb211-109">這些錯誤通常會在短時間內自行修正，而強大的雲端應用程式應會使用[重試模式][retry-pattern]來準備處理這些錯誤。</span><span class="sxs-lookup"><span data-stu-id="cb211-109">These faults typically correct themselves after a short period of time, and a robust cloud application should be prepared to handle them by using a strategy such as the [Retry pattern][retry-pattern].</span></span>

<span data-ttu-id="cb211-110">不過，也可能發生因為非預期事件導致的錯誤，這可能需要更長的時間來進行修正。</span><span class="sxs-lookup"><span data-stu-id="cb211-110">However, there can also be situations where faults are due to unanticipated events, and that might take much longer to fix.</span></span> <span data-ttu-id="cb211-111">這些錯誤的嚴重性範圍包含失去部分連線到整個服務無法運作。</span><span class="sxs-lookup"><span data-stu-id="cb211-111">These faults can range in severity from a partial loss of connectivity to the complete failure of a service.</span></span> <span data-ttu-id="cb211-112">在這些情況下，持續重試不太可能成功的作業是無意義的，相反地，應用程式應快速接受作業失敗，並且據以處理此失敗事件。</span><span class="sxs-lookup"><span data-stu-id="cb211-112">In these situations it might be pointless for an application to continually retry an operation that is unlikely to succeed, and instead the application should quickly accept that the operation has failed and handle this failure accordingly.</span></span>

<span data-ttu-id="cb211-113">此外，如果服務非常忙碌，則系統中某一部分的失敗可能會導致階層式失敗。</span><span class="sxs-lookup"><span data-stu-id="cb211-113">Additionally, if a service is very busy, failure in one part of the system might lead to cascading failures.</span></span> <span data-ttu-id="cb211-114">例如，叫用服務的作業可設定為能實作逾時，並在該服務無法在此期間內回應時，回覆失敗訊息。</span><span class="sxs-lookup"><span data-stu-id="cb211-114">For example, an operation that invokes a service could be configured to implement a timeout, and reply with a failure message if the service fails to respond within this period.</span></span> <span data-ttu-id="cb211-115">不過，此策略可能會導致同個作業的許多並行要求遭到封鎖，直到逾時期限到期。</span><span class="sxs-lookup"><span data-stu-id="cb211-115">However, this strategy could cause many concurrent requests to the same operation to be blocked until the timeout period expires.</span></span> <span data-ttu-id="cb211-116">這些已封鎖的要求可能會佔據重要的系統資源，例如記憶體、執行緒、資料庫連線等等。</span><span class="sxs-lookup"><span data-stu-id="cb211-116">These blocked requests might hold critical system resources such as memory, threads, database connections, and so on.</span></span> <span data-ttu-id="cb211-117">因此，這些資源可能會被耗盡，導致其他必須使用相同資源但可能不相關的部分系統作業失敗。</span><span class="sxs-lookup"><span data-stu-id="cb211-117">Consequently, these resources could become exhausted, causing failure of other possibly unrelated parts of the system that need to use the same resources.</span></span> <span data-ttu-id="cb211-118">在這些情況下，作業最好能立即失敗，並只嘗試叫用可能成功的服務。</span><span class="sxs-lookup"><span data-stu-id="cb211-118">In these situations, it would be preferable for the operation to fail immediately, and only attempt to invoke the service if it's likely to succeed.</span></span> <span data-ttu-id="cb211-119">請注意，設定較短的逾時可能有助於解決此問題，但逾時不應該過短而造成作業一直失敗，即使服務要求最終會成功。</span><span class="sxs-lookup"><span data-stu-id="cb211-119">Note that setting a shorter timeout might help to resolve this problem, but the timeout shouldn't be so short that the operation fails most of the time, even if the request to the service would eventually succeed.</span></span>

## <a name="solution"></a><span data-ttu-id="cb211-120">解決方法</span><span class="sxs-lookup"><span data-stu-id="cb211-120">Solution</span></span>

<span data-ttu-id="cb211-121">因 Michael Nygard 的著作《[Release It!](https://pragprog.com/book/mnee/release-it)》而廣受歡迎的斷路器模式，可防止應用程式重複嘗試執行可能會失敗的作業。</span><span class="sxs-lookup"><span data-stu-id="cb211-121">The Circuit Breaker pattern, popularized by Michael Nygard in his book, [Release It!](https://pragprog.com/book/mnee/release-it), can prevent an application from repeatedly trying to execute an operation that's likely to fail.</span></span> <span data-ttu-id="cb211-122">一旦判斷該錯誤會持續較長時間時，該模式會讓應用程式繼續執行，而不用等候錯誤修正或浪費 CPU 循環。</span><span class="sxs-lookup"><span data-stu-id="cb211-122">Allowing it to continue without waiting for the fault to be fixed or wasting CPU cycles while it determines that the fault is long lasting.</span></span> <span data-ttu-id="cb211-123">斷路器模式也可讓應用程式偵測錯誤是否已解決。</span><span class="sxs-lookup"><span data-stu-id="cb211-123">The Circuit Breaker pattern also enables an application to detect whether the fault has been resolved.</span></span> <span data-ttu-id="cb211-124">如果此問題已獲得修正，應用程式可以嘗試叫用作業。</span><span class="sxs-lookup"><span data-stu-id="cb211-124">If the problem appears to have been fixed, the application can try to invoke the operation.</span></span>

> <span data-ttu-id="cb211-125">斷路器模式的用途與重試模式不同。</span><span class="sxs-lookup"><span data-stu-id="cb211-125">The purpose of the Circuit Breaker pattern is different than the Retry pattern.</span></span> <span data-ttu-id="cb211-126">重試模式會在預期作業會成功的情況下，讓應用程式重試作業。</span><span class="sxs-lookup"><span data-stu-id="cb211-126">The Retry pattern enables an application to retry an operation in the expectation that it'll succeed.</span></span> <span data-ttu-id="cb211-127">斷路器模式會防止應用程式執行很可能會失敗的作業。</span><span class="sxs-lookup"><span data-stu-id="cb211-127">The Circuit Breaker pattern prevents an application from performing an operation that is likely to fail.</span></span> <span data-ttu-id="cb211-128">應用程式可以結合這兩種模式，透過斷路器使用重試模式來叫用作業。</span><span class="sxs-lookup"><span data-stu-id="cb211-128">An application can combine these two patterns by using the Retry pattern to invoke an operation through a circuit breaker.</span></span> <span data-ttu-id="cb211-129">不過，重試邏輯應對斷路器傳回的任何例外狀況十分敏感，並在斷路器指出錯誤並非暫時性時，放棄重試。</span><span class="sxs-lookup"><span data-stu-id="cb211-129">However, the retry logic should be sensitive to any exceptions returned by the circuit breaker and abandon retry attempts if the circuit breaker indicates that a fault is not transient.</span></span>

<span data-ttu-id="cb211-130">針對可能失敗的作業，斷路器會充當 Proxy。</span><span class="sxs-lookup"><span data-stu-id="cb211-130">A circuit breaker acts as a proxy for operations that might fail.</span></span> <span data-ttu-id="cb211-131">Proxy 應監視最近發生的失敗數量，並使用此資訊來決定是否允許作業繼續，或是立即傳回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="cb211-131">The proxy should monitor the number of recent failures that have occurred, and use this information to decide whether to allow the operation to proceed, or simply return an exception immediately.</span></span>

<span data-ttu-id="cb211-132">Proxy 可以實作為具有下列狀態的狀態機，模擬電力斷路器的功能：</span><span class="sxs-lookup"><span data-stu-id="cb211-132">The proxy can be implemented as a state machine with the following states that mimic the functionality of an electrical circuit breaker:</span></span>

- <span data-ttu-id="cb211-133">**已關閉**：來自應用程式的要求會路由至作業。</span><span class="sxs-lookup"><span data-stu-id="cb211-133">**Closed**: The request from the application is routed to the operation.</span></span> <span data-ttu-id="cb211-134">Proxy 會更新最近的失敗計數，如果作業呼叫不成功時，Proxy 就會增加此計數。</span><span class="sxs-lookup"><span data-stu-id="cb211-134">The proxy maintains a count of the number of recent failures, and if the call to the operation is unsuccessful the proxy increments this count.</span></span> <span data-ttu-id="cb211-135">如果最近的失敗數量超出給定期間內的指定臨界值，則 Proxy 會置於**開啟**狀態。</span><span class="sxs-lookup"><span data-stu-id="cb211-135">If the number of recent failures exceeds a specified threshold within a given time period, the proxy is placed into the **Open** state.</span></span> <span data-ttu-id="cb211-136">此時的 Proxy 會啟動逾時計時器，當此計時器過期時，Proxy 會置於**半開啟**狀態。</span><span class="sxs-lookup"><span data-stu-id="cb211-136">At this point the proxy starts a timeout timer, and when this timer expires the proxy is placed into the **Half-Open** state.</span></span>

    > <span data-ttu-id="cb211-137">逾時計時器的用途是讓系統有時間修正造成失敗的問題，然後才能允許應用程式嘗試再次執行作業。</span><span class="sxs-lookup"><span data-stu-id="cb211-137">The purpose of the timeout timer is to give the system time to fix the problem that caused the failure before allowing the application to try to perform the operation again.</span></span>

- <span data-ttu-id="cb211-138">**開啟**：來自應用程式的要求會立即失敗，並傳回例外狀況給應用程式。</span><span class="sxs-lookup"><span data-stu-id="cb211-138">**Open**: The request from the application fails immediately and an exception is returned to the application.</span></span>

- <span data-ttu-id="cb211-139">**半開啟**：允許來自應用程式的有限要求數量通過，並叫用作業。</span><span class="sxs-lookup"><span data-stu-id="cb211-139">**Half-Open**: A limited number of requests from the application are allowed to pass through and invoke the operation.</span></span> <span data-ttu-id="cb211-140">如果這些要求成功，則會假設先前導致失敗的錯誤已修正，而且斷路器會切換為**已關閉**狀態 (失敗計數器會重設)。</span><span class="sxs-lookup"><span data-stu-id="cb211-140">If these requests are successful, it's assumed that the fault that was previously causing the failure has been fixed and the circuit breaker switches to the **Closed** state (the failure counter is reset).</span></span> <span data-ttu-id="cb211-141">如果有任何要求失敗，斷路器就會假設該錯誤仍然存在，因此還原成**開啟**狀態，並重新啟動時逾時計時器，讓系統有更多時間從失敗中復原。</span><span class="sxs-lookup"><span data-stu-id="cb211-141">If any request fails, the circuit breaker assumes that the fault is still present so it reverts back to the **Open** state and restarts the timeout timer to give the system a further period of time to recover from the failure.</span></span>

    > <span data-ttu-id="cb211-142">**半開啟**狀態有助於防止復原服務突然出現許多要求。</span><span class="sxs-lookup"><span data-stu-id="cb211-142">The **Half-Open** state is useful to prevent a recovering service from suddenly being flooded with requests.</span></span> <span data-ttu-id="cb211-143">服務在復原時可以支援有限的要求量，直到復原完成，但當復原正在進行時，大量的工作會造成服務逾時或再次失敗。</span><span class="sxs-lookup"><span data-stu-id="cb211-143">As a service recovers, it might be able to support a limited volume of requests until the recovery is complete, but while recovery is in progress a flood of work can cause the service to time out or fail again.</span></span>

![斷路器狀態](./_images/circuit-breaker-diagram.png)

<span data-ttu-id="cb211-145">圖中**已關閉**狀態使用的失敗計數器是以時間為基礎。</span><span class="sxs-lookup"><span data-stu-id="cb211-145">In the figure, the failure counter used by the **Closed** state is time based.</span></span> <span data-ttu-id="cb211-146">它會定期自動重設。</span><span class="sxs-lookup"><span data-stu-id="cb211-146">It's automatically reset at periodic intervals.</span></span> <span data-ttu-id="cb211-147">這有助於防止斷路器在經歷偶爾的失敗時進入**開啟**狀態。</span><span class="sxs-lookup"><span data-stu-id="cb211-147">This helps to prevent the circuit breaker from entering the **Open** state if it experiences occasional failures.</span></span> <span data-ttu-id="cb211-148">只有在指定時間間隔內發生指定的失敗數量時，會達到造成斷路器進入**開啟**狀態的失敗臨界值。</span><span class="sxs-lookup"><span data-stu-id="cb211-148">The failure threshold that trips the circuit breaker into the **Open** state is only reached when a specified number of failures have occurred during a specified interval.</span></span> <span data-ttu-id="cb211-149">**半開啟**狀態使用的計數器會記錄成功嘗試叫用作業的數目。</span><span class="sxs-lookup"><span data-stu-id="cb211-149">The counter used by the **Half-Open** state records the number of successful attempts to invoke the operation.</span></span> <span data-ttu-id="cb211-150">在指定數量的連續作業引動過程成功後，斷路器會還原為**已關閉**狀態。</span><span class="sxs-lookup"><span data-stu-id="cb211-150">The circuit breaker reverts to the **Closed** state after a specified number of consecutive operation invocations have been successful.</span></span> <span data-ttu-id="cb211-151">如果有任何引動過程失敗，斷路器便會立即進入**開啟**狀態，而成功計數器將會在斷路器下次進入**半開啟**狀態時重設。</span><span class="sxs-lookup"><span data-stu-id="cb211-151">If any invocation fails, the circuit breaker enters the **Open** state immediately and the success counter will be reset the next time it enters the **Half-Open** state.</span></span>

> <span data-ttu-id="cb211-152">系統復原的方式會在外部處理，可能是透過還原或重新啟動失敗的元件，或是修復網路連線。</span><span class="sxs-lookup"><span data-stu-id="cb211-152">How the system recovers is handled externally, possibly by restoring or restarting a failed component or repairing a network connection.</span></span>

<span data-ttu-id="cb211-153">當系統從失敗中復原時，斷路器模式可以使其保持穩定，並盡可能不影響效能。</span><span class="sxs-lookup"><span data-stu-id="cb211-153">The Circuit Breaker pattern provides stability while the system recovers from a failure and minimizes the impact on performance.</span></span> <span data-ttu-id="cb211-154">斷路器模式有助於保持系統的回應時間，因為其會快速拒絕作業可能會失敗的要求，而不是等候作業逾時或永遠不傳回。</span><span class="sxs-lookup"><span data-stu-id="cb211-154">It can help to maintain the response time of the system by quickly rejecting a request for an operation that's likely to fail, rather than waiting for the operation to time out, or never return.</span></span> <span data-ttu-id="cb211-155">如果斷路器在每次變更狀態時都會引發事件，則此資訊可以用來監視受到斷路器保護的部分系統健康情況，或在斷路器進入**開啟**狀態向系統管理員發出警示。</span><span class="sxs-lookup"><span data-stu-id="cb211-155">If the circuit breaker raises an event each time it changes state, this information can be used to monitor the health of the part of the system protected by the circuit breaker, or to alert an administrator when a circuit breaker trips to the **Open** state.</span></span>

<span data-ttu-id="cb211-156">模式可自訂，且可根據可能發生的失敗類型進行調整。</span><span class="sxs-lookup"><span data-stu-id="cb211-156">The pattern is customizable and can be adapted according to the type of the possible failure.</span></span> <span data-ttu-id="cb211-157">例如，您可以將遞增的逾時計時器套用至斷路器。</span><span class="sxs-lookup"><span data-stu-id="cb211-157">For example, you can apply an increasing timeout timer to a circuit breaker.</span></span> <span data-ttu-id="cb211-158">一開始您可以將斷路器置於**開啟**狀態約幾秒鐘的時間，如果失敗事件尚未解決，則將逾時時間增加至幾分鐘，以此類推。</span><span class="sxs-lookup"><span data-stu-id="cb211-158">You could place the circuit breaker in the **Open** state for a few seconds initially, and then if the failure hasn't been resolved increase the timeout to a few minutes, and so on.</span></span> <span data-ttu-id="cb211-159">在某些情況下，與其讓**開啟**狀態傳回失敗並引發例外狀況，傳回對應用程式有意義的預設值可能會比較實用。</span><span class="sxs-lookup"><span data-stu-id="cb211-159">In some cases, rather than the **Open** state returning failure and raising an exception, it could be useful to return a default value that is meaningful to the application.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="cb211-160">問題和考量</span><span class="sxs-lookup"><span data-stu-id="cb211-160">Issues and considerations</span></span>

<span data-ttu-id="cb211-161">當您在決定如何實作此模式時，應考慮下列幾點：</span><span class="sxs-lookup"><span data-stu-id="cb211-161">You should consider the following points when deciding how to implement this pattern:</span></span>

<span data-ttu-id="cb211-162">**例外狀況處理**。</span><span class="sxs-lookup"><span data-stu-id="cb211-162">**Exception Handling**.</span></span> <span data-ttu-id="cb211-163">透過斷路器叫用作業的應用程式必須做好準備，以處理作業無法使用時引發的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="cb211-163">An application invoking an operation through a circuit breaker must be prepared to handle the exceptions raised if the operation is unavailable.</span></span> <span data-ttu-id="cb211-164">每個應用程式都有專屬的例外狀況處理方式。</span><span class="sxs-lookup"><span data-stu-id="cb211-164">The way exceptions are handled will be application specific.</span></span> <span data-ttu-id="cb211-165">例如，應用程式可能會暫時降低功能、叫用替代作業來嘗試執行相同工作或取得相同資料，或提報例外狀況給使用者並要求他們稍後再試一次。</span><span class="sxs-lookup"><span data-stu-id="cb211-165">For example, an application could temporarily degrade its functionality, invoke an alternative operation to try to perform the same task or obtain the same data, or report the exception to the user and ask them to try again later.</span></span>

<span data-ttu-id="cb211-166">**例外狀況類型**。</span><span class="sxs-lookup"><span data-stu-id="cb211-166">**Types of Exceptions**.</span></span> <span data-ttu-id="cb211-167">要求失敗的原因可能有很多，其中有些原因所造成的失敗類型會比其他類型嚴重。</span><span class="sxs-lookup"><span data-stu-id="cb211-167">A request might fail for many reasons, some of which might indicate a more severe type of failure than others.</span></span> <span data-ttu-id="cb211-168">例如，要求可能會因為遠端服務當機而失敗，並將需要幾分鐘才能復原，或是因為服務暫時超載而發生逾時。</span><span class="sxs-lookup"><span data-stu-id="cb211-168">For example, a request might fail because a remote service has crashed and will take several minutes to recover, or because of a timeout due to the service being temporarily overloaded.</span></span> <span data-ttu-id="cb211-169">斷路器可能會檢查發生的例外狀況類型，並根據這些例外狀況的本質調整其策略。</span><span class="sxs-lookup"><span data-stu-id="cb211-169">A circuit breaker might be able to examine the types of exceptions that occur and adjust its strategy depending on the nature of these exceptions.</span></span> <span data-ttu-id="cb211-170">例如，斷路器進入**開啟**狀態可能需要更大量的逾時例外狀況 (相較於服務完全無法使用造成的失敗數量)。</span><span class="sxs-lookup"><span data-stu-id="cb211-170">For example, it might require a larger number of timeout exceptions to trip the circuit breaker to the **Open** state compared to the number of failures due to the service being completely unavailable.</span></span>

<span data-ttu-id="cb211-171">**記錄**。</span><span class="sxs-lookup"><span data-stu-id="cb211-171">**Logging**.</span></span> <span data-ttu-id="cb211-172">斷路器應記錄所有失敗的要求 (和可能成功的要求)，讓系統管理員可監視作業的健康情況。</span><span class="sxs-lookup"><span data-stu-id="cb211-172">A circuit breaker should log all failed requests (and possibly successful requests) to enable an administrator to monitor the health of the operation.</span></span>

<span data-ttu-id="cb211-173">**可復原性**。</span><span class="sxs-lookup"><span data-stu-id="cb211-173">**Recoverability**.</span></span> <span data-ttu-id="cb211-174">您應針對斷路器保護的作業，將斷路器設定為符合適當的復原模式。</span><span class="sxs-lookup"><span data-stu-id="cb211-174">You should configure the circuit breaker to match the likely recovery pattern of the operation it's protecting.</span></span> <span data-ttu-id="cb211-175">例如，如果斷路器長時間維持在**開啟**狀態，則它可能會引發例外狀況，即使失敗的原因已解決。</span><span class="sxs-lookup"><span data-stu-id="cb211-175">For example, if the circuit breaker remains in the **Open** state for a long period, it could raise exceptions even if the reason for the failure has been resolved.</span></span> <span data-ttu-id="cb211-176">同樣地，如果斷路器從**開啟**狀態切換至**半開啟**狀態的速度太快，則斷路器可能會變動和減少應用程式的回應次數。</span><span class="sxs-lookup"><span data-stu-id="cb211-176">Similarly, a circuit breaker could fluctuate and reduce the response times of applications if it switches from the **Open** state to the **Half-Open** state too quickly.</span></span>

<span data-ttu-id="cb211-177">**測試失敗的作業**。</span><span class="sxs-lookup"><span data-stu-id="cb211-177">**Testing Failed Operations**.</span></span> <span data-ttu-id="cb211-178">在**開啟**狀態下，斷路器會定期偵測遠端服務或資源，以判斷這些項目是否又可以使用了，而不是使用計時器來判斷何時要切換至**半開啟**狀態。</span><span class="sxs-lookup"><span data-stu-id="cb211-178">In the **Open** state, rather than using a timer to determine when to switch to the **Half-Open** state, a circuit breaker can instead periodically ping the remote service or resource to determine whether it's become available again.</span></span> <span data-ttu-id="cb211-179">此偵測作業 可能會採取的方式是嘗試叫用先前失敗的作業，或使用遠端服務 (專門用於測試該服務的健康情況) 所提供的特殊作業，如[健康情況端點監視模式](health-endpoint-monitoring.md)中所述。</span><span class="sxs-lookup"><span data-stu-id="cb211-179">This ping could take the form of an attempt to invoke an operation that had previously failed, or it could use a special operation provided by the remote service specifically for testing the health of the service, as described by the [Health Endpoint Monitoring pattern](health-endpoint-monitoring.md).</span></span>

<span data-ttu-id="cb211-180">**手動覆寫**。</span><span class="sxs-lookup"><span data-stu-id="cb211-180">**Manual Override**.</span></span> <span data-ttu-id="cb211-181">在失敗作業復原時間極為不同的系統中，十分適合提供手動重設選項，讓系統管理員可關閉斷路器 (和重設失敗計數器)。</span><span class="sxs-lookup"><span data-stu-id="cb211-181">In a system where the recovery time for a failing operation is extremely variable, it's beneficial to provide a manual reset option that enables an administrator to close a circuit breaker (and reset the failure counter).</span></span> <span data-ttu-id="cb211-182">同樣地，如果受斷路器保護的作業暫時無法使用，系統管理員也可以強制斷路器進入**開啟**狀態 (及重新啟動逾時計時器)。</span><span class="sxs-lookup"><span data-stu-id="cb211-182">Similarly, an administrator could force a circuit breaker into the **Open** state (and restart the timeout timer) if the operation protected by the circuit breaker is temporarily unavailable.</span></span>

<span data-ttu-id="cb211-183">**並行存取**。</span><span class="sxs-lookup"><span data-stu-id="cb211-183">**Concurrency**.</span></span> <span data-ttu-id="cb211-184">大量的並行應用程式執行個體可存取同一個斷路器。</span><span class="sxs-lookup"><span data-stu-id="cb211-184">The same circuit breaker could be accessed by a large number of concurrent instances of an application.</span></span> <span data-ttu-id="cb211-185">實作不應封鎖並行要求或將過多額外負荷新增至作業的每次呼叫中。</span><span class="sxs-lookup"><span data-stu-id="cb211-185">The implementation shouldn't block concurrent requests or add excessive overhead to each call to an operation.</span></span>

<span data-ttu-id="cb211-186">**資源差異**。</span><span class="sxs-lookup"><span data-stu-id="cb211-186">**Resource Differentiation**.</span></span> <span data-ttu-id="cb211-187">針對一種資源類型使用單一斷路器時請謹慎，因為其中可能有多個在根本上是獨立的提供者。</span><span class="sxs-lookup"><span data-stu-id="cb211-187">Be careful when using a single circuit breaker for one type of resource if there might be multiple underlying independent providers.</span></span> <span data-ttu-id="cb211-188">例如，在包含多個分區的資料存放區中，當一個分區發生暫時性問題時，另一個分區可能可以完整存取。</span><span class="sxs-lookup"><span data-stu-id="cb211-188">For example, in a data store that contains multiple shards, one shard might be fully accessible while another is experiencing a temporary issue.</span></span> <span data-ttu-id="cb211-189">如果這些情況中的錯誤回應合併在一起，則應用程式可能會嘗試存取很可能發生失敗的部分分區，而存取其他可能成功的分區時可能會遭到封鎖。</span><span class="sxs-lookup"><span data-stu-id="cb211-189">If the error responses in these scenarios are merged, an application might try to access some shards even when failure is highly likely, while access to other shards might be blocked even though it's likely to succeed.</span></span>

<span data-ttu-id="cb211-190">**加速斷路**。</span><span class="sxs-lookup"><span data-stu-id="cb211-190">**Accelerated Circuit Breaking**.</span></span> <span data-ttu-id="cb211-191">有時候失敗回應可能包含足以讓斷路器立即進行中斷的資訊，並在最短的時間內維持中斷狀態。</span><span class="sxs-lookup"><span data-stu-id="cb211-191">Sometimes a failure response can contain enough information for the circuit breaker to trip immediately and stay tripped for a minimum amount of time.</span></span> <span data-ttu-id="cb211-192">例如，分區資源若是因為負載過重而產生錯誤回應，則表示不建議立即重試，相反地，應用程式應稍待數分鐘後再試一次。</span><span class="sxs-lookup"><span data-stu-id="cb211-192">For example, the error response from a shared resource that's overloaded could indicate that an immediate retry isn't recommended and that the application should instead try again in a few minutes.</span></span>

> [!NOTE]
> <span data-ttu-id="cb211-193">如果服務正在對用戶端進行節流，則服務可能傳回 HTTP 429 (太多要求)，如果服務目前無法使用，則傳回 HTTP 503 (服務無法使用)。</span><span class="sxs-lookup"><span data-stu-id="cb211-193">A service can return HTTP 429 (Too Many Requests) if it is throttling the client, or HTTP 503 (Service Unavailable) if the service is not currently available.</span></span> <span data-ttu-id="cb211-194">回應可以包含其他資訊，例如預期的延遲時間。</span><span class="sxs-lookup"><span data-stu-id="cb211-194">The response can include additional information, such as the anticipated duration of the delay.</span></span>

<span data-ttu-id="cb211-195">**重新執行失敗的要求**。</span><span class="sxs-lookup"><span data-stu-id="cb211-195">**Replaying Failed Requests**.</span></span> <span data-ttu-id="cb211-196">在**開啟**狀態下，不是只有簡單地讓要求快速失敗，斷路器也會將每個要求的詳細資料記錄到日誌，並在遠端資源或服務可用時，安排重新執行這些要求。</span><span class="sxs-lookup"><span data-stu-id="cb211-196">In the **Open** state, rather than simply failing quickly, a circuit breaker could also record the details of each request to a journal and arrange for these requests to be replayed when the remote resource or service becomes available.</span></span>

<span data-ttu-id="cb211-197">**外部服務上不適當的逾時**。</span><span class="sxs-lookup"><span data-stu-id="cb211-197">**Inappropriate Timeouts on External Services**.</span></span> <span data-ttu-id="cb211-198">如果外部服務上設定的逾時時間過長，當外部服務中發生作業失敗時，斷路器可能無法完全保護應用程式。</span><span class="sxs-lookup"><span data-stu-id="cb211-198">A circuit breaker might not be able to fully protect applications from operations that fail in external services that are configured with a lengthy timeout period.</span></span> <span data-ttu-id="cb211-199">如果逾時時間太長，執行斷路器的執行緒可能會遭到更長時間的封鎖，然後才讓斷路器指示作業已失敗。</span><span class="sxs-lookup"><span data-stu-id="cb211-199">If the timeout is too long, a thread running a circuit breaker might be blocked for an extended period before the circuit breaker indicates that the operation has failed.</span></span> <span data-ttu-id="cb211-200">在這段時間內，其他許多應用程式執行個體也可能嘗試透過斷路器叫用服務，並在集結大量執行緒後全部失敗。</span><span class="sxs-lookup"><span data-stu-id="cb211-200">In this time, many other application instances might also try to invoke the service through the circuit breaker and tie up a significant number of threads before they all fail.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="cb211-201">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="cb211-201">When to use this pattern</span></span>

<span data-ttu-id="cb211-202">使用此模式：</span><span class="sxs-lookup"><span data-stu-id="cb211-202">Use this pattern:</span></span>

- <span data-ttu-id="cb211-203">防止應用程式嘗試叫用遠端服務或存取共用資源 (如果此作業非常有可能失敗)。</span><span class="sxs-lookup"><span data-stu-id="cb211-203">To prevent an application from trying to invoke a remote service or access a shared resource if this operation is highly likely to fail.</span></span>

<span data-ttu-id="cb211-204">不建議使用此模式：</span><span class="sxs-lookup"><span data-stu-id="cb211-204">This pattern isn't recommended:</span></span>

- <span data-ttu-id="cb211-205">處理應用程式中的本機私人資源存取，例如記憶體內的資料結構。</span><span class="sxs-lookup"><span data-stu-id="cb211-205">For handling access to local private resources in an application, such as in-memory data structure.</span></span> <span data-ttu-id="cb211-206">在此環境中，使用斷路器會增加您系統的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="cb211-206">In this environment, using a circuit breaker would add overhead to your system.</span></span>
- <span data-ttu-id="cb211-207">作為替代方來處理您應用程式的商務邏輯例外狀況。</span><span class="sxs-lookup"><span data-stu-id="cb211-207">As a substitute for handling exceptions in the business logic of your applications.</span></span>

## <a name="example"></a><span data-ttu-id="cb211-208">範例</span><span class="sxs-lookup"><span data-stu-id="cb211-208">Example</span></span>

<span data-ttu-id="cb211-209">在 Web 應用程式中，有數個頁面會填入從外部服務擷取的資料。</span><span class="sxs-lookup"><span data-stu-id="cb211-209">In a web application, several of the pages are populated with data retrieved from an external service.</span></span> <span data-ttu-id="cb211-210">如果系統實作最小快取，這些頁面大部分的命中數會造成服務往返。</span><span class="sxs-lookup"><span data-stu-id="cb211-210">If the system implements minimal caching, most hits to these pages will cause a round trip to the service.</span></span> <span data-ttu-id="cb211-211">Web 應用程式至服務的連線通常會設定逾時時間 (通常是 60 秒)，而且如果服務不在此時間內回應，每個網頁中的邏輯會假設服務無法使用，並擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="cb211-211">Connections from the web application to the service could be configured with a timeout period (typically 60 seconds), and if the service doesn't respond in this time the logic in each web page will assume that the service is unavailable and throw an exception.</span></span>

<span data-ttu-id="cb211-212">不過，如果服務失敗且系統非常忙碌，則發生例外狀況前，系統會強制使用者等候 60 秒。</span><span class="sxs-lookup"><span data-stu-id="cb211-212">However, if the service fails and the system is very busy, users could be forced to wait for up to 60 seconds before an exception occurs.</span></span> <span data-ttu-id="cb211-213">最後記憶體、連線和執行緒等資源可能會耗盡，並阻止其他使用者連線至系統，即使他們沒有存取從該服務擷取資料的頁面。</span><span class="sxs-lookup"><span data-stu-id="cb211-213">Eventually resources such as memory, connections, and threads could be exhausted, preventing other users from connecting to the system, even if they aren't accessing pages that retrieve data from the service.</span></span>

<span data-ttu-id="cb211-214">新增其他網頁伺服器和實作負載平衡來延展系統，可能會延遲資源耗盡的時間，但它不會解決此問題，因為使用者的要求仍然沒有回應，而且所有網頁伺服器最終仍會耗盡資源。</span><span class="sxs-lookup"><span data-stu-id="cb211-214">Scaling the system by adding further web servers and implementing load balancing might delay when resources become exhausted, but it won't resolve the issue because user requests will still be unresponsive and all web servers could still eventually run out of resources.</span></span>

<span data-ttu-id="cb211-215">在斷路器中連線至服務並擷取資料的邏輯可能有助於解決此問題，並更從容地處理服務失敗。</span><span class="sxs-lookup"><span data-stu-id="cb211-215">Wrapping the logic that connects to the service and retrieves the data in a circuit breaker could help to solve this problem and handle the service failure more elegantly.</span></span> <span data-ttu-id="cb211-216">使用者要求還是會失敗，但是這些要求會失敗地更快速，而且資源不會遭到封鎖。</span><span class="sxs-lookup"><span data-stu-id="cb211-216">User requests will still fail, but they'll fail more quickly and the resources won't be blocked.</span></span>

<span data-ttu-id="cb211-217">`CircuitBreaker` 類別會在物件中維護斷路器的狀態資訊，該物件會實作 `ICircuitBreakerStateStore` 下列程式碼所示的介面。</span><span class="sxs-lookup"><span data-stu-id="cb211-217">The `CircuitBreaker` class maintains state information about a circuit breaker in an object that implements the `ICircuitBreakerStateStore` interface shown in the following code.</span></span>

```csharp
interface ICircuitBreakerStateStore
{
  CircuitBreakerStateEnum State { get; }

  Exception LastException { get; }

  DateTime LastStateChangedDateUtc { get; }

  void Trip(Exception ex);

  void Reset();

  void HalfOpen();

  bool IsClosed { get; }
}
```

<span data-ttu-id="cb211-218">`State` 屬性表示斷路器目前的狀態，可能是**開啟**、**半開啟**或**已關閉**，如同 `CircuitBreakerStateEnum` 列舉中所定義。</span><span class="sxs-lookup"><span data-stu-id="cb211-218">The `State` property indicates the current state of the circuit breaker, and will be either **Open**, **HalfOpen**, or **Closed** as defined by the `CircuitBreakerStateEnum` enumeration.</span></span> <span data-ttu-id="cb211-219">如果斷路器已關閉，`IsClosed` 屬性就是正確的，但如果是開啟或半開啟則不正確。</span><span class="sxs-lookup"><span data-stu-id="cb211-219">The `IsClosed` property should be true if the circuit breaker is closed, but false if it's open or half open.</span></span> <span data-ttu-id="cb211-220">`Trip` 方法會將斷路器的狀態會切換成開啟狀態，並記錄導致狀態變更的例外狀況，包含發生例外狀況的日期和時間。</span><span class="sxs-lookup"><span data-stu-id="cb211-220">The `Trip` method switches the state of the circuit breaker to the open state and records the exception that caused the change in state, together with the date and time that the exception occurred.</span></span> <span data-ttu-id="cb211-221">`LastException` 和 `LastStateChangedDateUtc` 屬性會傳回此資訊。</span><span class="sxs-lookup"><span data-stu-id="cb211-221">The `LastException` and the `LastStateChangedDateUtc` properties return this information.</span></span> <span data-ttu-id="cb211-222">`Reset` 方法會關閉斷路器，而 `HalfOpen` 方法會將斷路器設定為半開啟。</span><span class="sxs-lookup"><span data-stu-id="cb211-222">The `Reset` method closes the circuit breaker, and the `HalfOpen` method sets the circuit breaker to half open.</span></span>

<span data-ttu-id="cb211-223">範例中的 `InMemoryCircuitBreakerStateStore` 類別包含 `ICircuitBreakerStateStore` 介面的實作。</span><span class="sxs-lookup"><span data-stu-id="cb211-223">The `InMemoryCircuitBreakerStateStore` class in the example contains an implementation of the `ICircuitBreakerStateStore` interface.</span></span> <span data-ttu-id="cb211-224">`CircuitBreaker` 類別會建立此類別的執行個體來保存斷路器的狀態。</span><span class="sxs-lookup"><span data-stu-id="cb211-224">The `CircuitBreaker` class creates an instance of this class to hold the state of the circuit breaker.</span></span>

<span data-ttu-id="cb211-225">`CircuitBreaker` 類別中的 `ExecuteAction` 方法會包裝作業，並指定為 `Action` 委派。</span><span class="sxs-lookup"><span data-stu-id="cb211-225">The `ExecuteAction` method in the `CircuitBreaker` class wraps an operation, specified as an `Action` delegate.</span></span> <span data-ttu-id="cb211-226">如果斷路器已關閉，`ExecuteAction` 會叫用`Action` 委派。</span><span class="sxs-lookup"><span data-stu-id="cb211-226">If the circuit breaker is closed, `ExecuteAction` invokes the `Action` delegate.</span></span> <span data-ttu-id="cb211-227">如果作業失敗，例外狀況處理常式會呼叫 `TrackException`，它會將斷路器狀態設為開啟。</span><span class="sxs-lookup"><span data-stu-id="cb211-227">If the operation fails, an exception handler calls `TrackException`, which sets the circuit breaker state to open.</span></span> <span data-ttu-id="cb211-228">下列程式碼範例會重點說明此流程。</span><span class="sxs-lookup"><span data-stu-id="cb211-228">The following code example highlights this flow.</span></span>

```csharp
public class CircuitBreaker
{
  private readonly ICircuitBreakerStateStore stateStore =
    CircuitBreakerStateStoreFactory.GetCircuitBreakerStateStore();

  private readonly object halfOpenSyncObject = new object ();
  ...
  public bool IsClosed { get { return stateStore.IsClosed; } }

  public bool IsOpen { get { return !IsClosed; } }

  public void ExecuteAction(Action action)
  {
    ...
    if (IsOpen)
    {
      // The circuit breaker is Open.
      ... (see code sample below for details)
    }

    // The circuit breaker is Closed, execute the action.
    try
    {
      action();
    }
    catch (Exception ex)
    {
      // If an exception still occurs here, simply
      // retrip the breaker immediately.
      this.TrackException(ex);

      // Throw the exception so that the caller can tell
      // the type of exception that was thrown.
      throw;
    }
  }

  private void TrackException(Exception ex)
  {
    // For simplicity in this example, open the circuit breaker on the first exception.
    // In reality this would be more complex. A certain type of exception, such as one
    // that indicates a service is offline, might trip the circuit breaker immediately.
    // Alternatively it might count exceptions locally or across multiple instances and
    // use this value over time, or the exception/success ratio based on the exception
    // types, to open the circuit breaker.
    this.stateStore.Trip(ex);
  }
}
```

<span data-ttu-id="cb211-229">下列範例會示範斷路器未關閉時所執行的程式碼 (從上述範例作刪減)。</span><span class="sxs-lookup"><span data-stu-id="cb211-229">The following example shows the code (omitted from the previous example) that is executed if the circuit breaker isn't closed.</span></span> <span data-ttu-id="cb211-230">它會先檢查斷路器的開啟時間是否超過 `CircuitBreaker` 類別中本機 `OpenToHalfOpenWaitTime` 欄位所指定的時間。</span><span class="sxs-lookup"><span data-stu-id="cb211-230">It first checks if the circuit breaker has been open for a period longer than the time specified by the local `OpenToHalfOpenWaitTime` field in the `CircuitBreaker` class.</span></span> <span data-ttu-id="cb211-231">如果是這種情況，`ExecuteAction` 方法會將斷路器設為半開啟，然後嘗試執行 `Action` 委派指定的作業。</span><span class="sxs-lookup"><span data-stu-id="cb211-231">If this is the case, the `ExecuteAction` method sets the circuit breaker to half open, then tries to perform the operation specified by the `Action` delegate.</span></span>

<span data-ttu-id="cb211-232">如果作業成功，斷路器會重設為已關閉狀態。</span><span class="sxs-lookup"><span data-stu-id="cb211-232">If the operation is successful, the circuit breaker is reset to the closed state.</span></span> <span data-ttu-id="cb211-233">如果作業失敗，斷路器會跳回開啟狀態，且發生例外狀況的時間會更新，如此一來，斷路器會在等待更長的時間之後，才嘗試再次執行作業。</span><span class="sxs-lookup"><span data-stu-id="cb211-233">If the operation fails, it is tripped back to the open state and the time the exception occurred is updated so that the circuit breaker will wait for a further period before trying to perform the operation again.</span></span>

<span data-ttu-id="cb211-234">如果斷路器只開啟一小段時間，小於 `OpenToHalfOpenWaitTime` 值，`ExecuteAction` 方法只會擲回 `CircuitBreakerOpenException` 例外狀況，並傳回造成斷路器轉換至開啟狀態的錯誤。</span><span class="sxs-lookup"><span data-stu-id="cb211-234">If the circuit breaker has only been open for a short time, less than the `OpenToHalfOpenWaitTime` value, the `ExecuteAction` method simply throws a `CircuitBreakerOpenException` exception and returns the error that caused the circuit breaker to transition to the open state.</span></span>

<span data-ttu-id="cb211-235">此外，它會使用鎖定來防止斷路器在半開啟時，對作業執行並行呼叫。</span><span class="sxs-lookup"><span data-stu-id="cb211-235">Additionally, it uses a lock to prevent the circuit breaker from trying to perform concurrent calls to the operation while it's half open.</span></span> <span data-ttu-id="cb211-236">處理並行叫用作業嘗試的方式如同斷路器已開啟，作業會失敗並產生例外狀況，如稍後所述。</span><span class="sxs-lookup"><span data-stu-id="cb211-236">A concurrent attempt to invoke the operation will be handled as if the circuit breaker was open, and it'll fail with an exception as described later.</span></span>

```csharp
    ...
    if (IsOpen)
    {
      // The circuit breaker is Open. Check if the Open timeout has expired.
      // If it has, set the state to HalfOpen. Another approach might be to
      // check for the HalfOpen state that had be set by some other operation.
      if (stateStore.LastStateChangedDateUtc + OpenToHalfOpenWaitTime < DateTime.UtcNow)
      {
        // The Open timeout has expired. Allow one operation to execute. Note that, in
        // this example, the circuit breaker is set to HalfOpen after being
        // in the Open state for some period of time. An alternative would be to set
        // this using some other approach such as a timer, test method, manually, and
        // so on, and check the state here to determine how to handle execution
        // of the action.
        // Limit the number of threads to be executed when the breaker is HalfOpen.
        // An alternative would be to use a more complex approach to determine which
        // threads or how many are allowed to execute, or to execute a simple test
        // method instead.
        bool lockTaken = false;
        try
        {
          Monitor.TryEnter(halfOpenSyncObject, ref lockTaken);
          if (lockTaken)
          {
            // Set the circuit breaker state to HalfOpen.
            stateStore.HalfOpen();

            // Attempt the operation.
            action();

            // If this action succeeds, reset the state and allow other operations.
            // In reality, instead of immediately returning to the Closed state, a counter
            // here would record the number of successful operations and return the
            // circuit breaker to the Closed state only after a specified number succeed.
            this.stateStore.Reset();
            return;
          }
          catch (Exception ex)
          {
            // If there's still an exception, trip the breaker again immediately.
            this.stateStore.Trip(ex);

            // Throw the exception so that the caller knows which exception occurred.
            throw;
          }
          finally
          {
            if (lockTaken)
            {
              Monitor.Exit(halfOpenSyncObject);
            }
          }
        }
      }
      // The Open timeout hasn't yet expired. Throw a CircuitBreakerOpen exception to
      // inform the caller that the call was not actually attempted,
      // and return the most recent exception received.
      throw new CircuitBreakerOpenException(stateStore.LastException);
    }
    ...
```

<span data-ttu-id="cb211-237">為使用 `CircuitBreaker` 物件保護作業，應用程式會建立 `CircuitBreaker` 類別的執行個體，並叫用 `ExecuteAction` 方法，指定要作為參數執行的作業。</span><span class="sxs-lookup"><span data-stu-id="cb211-237">To use a `CircuitBreaker` object to protect an operation, an application creates an instance of the `CircuitBreaker` class and invokes the `ExecuteAction` method, specifying the operation to be performed as the parameter.</span></span> <span data-ttu-id="cb211-238">應用程式應該準備好在作業失敗時攔截 `CircuitBreakerOpenException` 例外狀況，因為斷路器已開啟。</span><span class="sxs-lookup"><span data-stu-id="cb211-238">The application should be prepared to catch the `CircuitBreakerOpenException` exception if the operation fails because the circuit breaker is open.</span></span> <span data-ttu-id="cb211-239">下列程式碼顯示一個範例：</span><span class="sxs-lookup"><span data-stu-id="cb211-239">The following code shows an example:</span></span>

```csharp
var breaker = new CircuitBreaker();

try
{
  breaker.ExecuteAction(() =>
  {
    // Operation protected by the circuit breaker.
    ...
  });
}
catch (CircuitBreakerOpenException ex)
{
  // Perform some different action when the breaker is open.
  // Last exception details are in the inner exception.
  ...
}
catch (Exception ex)
{
  ...
}
```

## <a name="related-patterns-and-guidance"></a><span data-ttu-id="cb211-240">相關的模式和指導方針</span><span class="sxs-lookup"><span data-stu-id="cb211-240">Related patterns and guidance</span></span>

<span data-ttu-id="cb211-241">下列模式在實作此模式時也很有用：</span><span class="sxs-lookup"><span data-stu-id="cb211-241">The following patterns might also be useful when implementing this pattern:</span></span>

- <span data-ttu-id="cb211-242">[重試模式][retry-pattern]。</span><span class="sxs-lookup"><span data-stu-id="cb211-242">[Retry Pattern][retry-pattern].</span></span> <span data-ttu-id="cb211-243">說明應用程式為何可以在嘗試連線到服務或網路資源時，藉由明確地重試先前失敗的作業，處理預期的暫時性失敗。</span><span class="sxs-lookup"><span data-stu-id="cb211-243">Describes how an application can handle anticipated temporary failures when it tries to connect to a service or network resource by transparently retrying an operation that has previously failed.</span></span>

- <span data-ttu-id="cb211-244">[健康情況端點監視模式](health-endpoint-monitoring.md)。</span><span class="sxs-lookup"><span data-stu-id="cb211-244">[Health Endpoint Monitoring Pattern](health-endpoint-monitoring.md).</span></span> <span data-ttu-id="cb211-245">斷路器可以藉由將要求傳送至服務所公開的端點，來測試服務的健康情況。</span><span class="sxs-lookup"><span data-stu-id="cb211-245">A circuit breaker might be able to test the health of a service by sending a request to an endpoint exposed by the service.</span></span> <span data-ttu-id="cb211-246">此服務應傳回指出其狀態的資訊。</span><span class="sxs-lookup"><span data-stu-id="cb211-246">The service should return information indicating its status.</span></span>


[retry-pattern]: ./retry.md

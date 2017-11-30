---
title: Retry
description: "讓應用程式可以在嘗試連線到服務或網路資源時，藉由明確地重試先前失敗的作業，處理預期的暫時性失敗。"
keywords: "設計模式"
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories: resiliency
ms.openlocfilehash: 6c02b384e71c068ecbc78f3170d28cea406538e2
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="retry-pattern"></a><span data-ttu-id="f959c-104">重試模式</span><span class="sxs-lookup"><span data-stu-id="f959c-104">Retry pattern</span></span>

[!INCLUDE [header](../_includes/header.md)]

<span data-ttu-id="f959c-105">讓應用程式可以在嘗試連線到服務或網路資源時，藉由明確地重試失敗的作業，處理暫時性失敗。</span><span class="sxs-lookup"><span data-stu-id="f959c-105">Enable an application to handle transient failures when it tries to connect to a service or network resource, by transparently retrying a failed operation.</span></span> <span data-ttu-id="f959c-106">這可以改善應用程式的穩定性。</span><span class="sxs-lookup"><span data-stu-id="f959c-106">This can improve the stability of the application.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="f959c-107">內容和問題</span><span class="sxs-lookup"><span data-stu-id="f959c-107">Context and problem</span></span>

<span data-ttu-id="f959c-108">應用程式若要與在雲端中執行的元素通訊，必須能感應在此環境中發生的暫時性錯誤。</span><span class="sxs-lookup"><span data-stu-id="f959c-108">An application that communicates with elements running in the cloud has to be sensitive to the transient faults that can occur in this environment.</span></span> <span data-ttu-id="f959c-109">錯誤包括瞬間失去元件和服務的網路連線、暫時無法使用服務，或當服務忙碌時逾時。</span><span class="sxs-lookup"><span data-stu-id="f959c-109">Faults include the momentary loss of network connectivity to components and services, the temporary unavailability of a service, or timeouts that occur when a service is busy.</span></span>

<span data-ttu-id="f959c-110">這些錯誤通常會自行修正，而且如果在適當的延遲後再重複觸發錯誤的動作，動作可能會成功。</span><span class="sxs-lookup"><span data-stu-id="f959c-110">These faults are typically self-correcting, and if the action that triggered a fault is repeated after a suitable delay it's likely to be successful.</span></span> <span data-ttu-id="f959c-111">例如，正在處理大量並行要求的資料庫服務可以實作節流策略，暫時拒絕任何進一步的要求直到其工作負載降低。</span><span class="sxs-lookup"><span data-stu-id="f959c-111">For example, a database service that's processing a large number of concurrent requests can implement a throttling strategy that temporarily rejects any further requests until its workload has eased.</span></span> <span data-ttu-id="f959c-112">嘗試存取資料庫的應用程式可能會無法連線，但如果延遲一段時間之後再次嘗試可能會成功。</span><span class="sxs-lookup"><span data-stu-id="f959c-112">An application trying to access the database might fail to connect, but if it tries again after a delay it might succeed.</span></span>

## <a name="solution"></a><span data-ttu-id="f959c-113">方案</span><span class="sxs-lookup"><span data-stu-id="f959c-113">Solution</span></span>

<span data-ttu-id="f959c-114">在雲端中，暫時性錯誤不是很常見，應用程式應該設計為可以適當且明確地處理它們。</span><span class="sxs-lookup"><span data-stu-id="f959c-114">In the cloud, transient faults aren't uncommon and an application should be designed to handle them elegantly and transparently.</span></span> <span data-ttu-id="f959c-115">這會將錯誤可能會對應用程式正在執行的商務工作造成的影響降到最低。</span><span class="sxs-lookup"><span data-stu-id="f959c-115">This minimizes the effects faults can have on the business tasks the application is performing.</span></span>

<span data-ttu-id="f959c-116">如果應用程式嘗試將要求傳送至遠端服務時偵測到失敗，它可以使用下列策略處理失敗：</span><span class="sxs-lookup"><span data-stu-id="f959c-116">If an application detects a failure when it tries to send a request to a remote service, it can handle the failure using the following strategies:</span></span>

- <span data-ttu-id="f959c-117">**取消**。</span><span class="sxs-lookup"><span data-stu-id="f959c-117">**Cancel**.</span></span> <span data-ttu-id="f959c-118">如果錯誤顯然不是暫時性失敗，或重複執行不太可能會成功，則應用程式應該取消作業，並報告例外狀況。</span><span class="sxs-lookup"><span data-stu-id="f959c-118">If the fault indicates that the failure isn't transient or is unlikely to be successful if repeated, the application should cancel the operation and report an exception.</span></span> <span data-ttu-id="f959c-119">例如，因為提供不正確認證而導致的驗證失敗，無論嘗試多少次都不可能成功。</span><span class="sxs-lookup"><span data-stu-id="f959c-119">For example, an authentication failure caused by providing invalid credentials is not likely to succeed no matter how many times it's attempted.</span></span>

- <span data-ttu-id="f959c-120">**重試**。</span><span class="sxs-lookup"><span data-stu-id="f959c-120">**Retry**.</span></span> <span data-ttu-id="f959c-121">如果特定錯誤回報為不尋常或罕見，可能是不尋常的情況由造成，例如在傳輸時損毀的網路封包。</span><span class="sxs-lookup"><span data-stu-id="f959c-121">If the specific fault reported is unusual or rare, it might have been caused by unusual circumstances such as a network packet becoming corrupted while it was being transmitted.</span></span> <span data-ttu-id="f959c-122">在此情況下，應用程式可以立即再次重試失敗的要求，因此相同的失敗不太可能再發生，要求可能會成功。</span><span class="sxs-lookup"><span data-stu-id="f959c-122">In this case, the application could retry the failing request again immediately because the same failure is unlikely to be repeated and the request will probably be successful.</span></span>

- <span data-ttu-id="f959c-123">**延遲之後重試。**</span><span class="sxs-lookup"><span data-stu-id="f959c-123">**Retry after delay.**</span></span> <span data-ttu-id="f959c-124">如果錯誤是因為幾個常見的連線問題之一或忙碌而失敗，網路或服務可能需要一小段時間，等待連線問題更正或工作的待辦項目清除。</span><span class="sxs-lookup"><span data-stu-id="f959c-124">If the fault is caused by one of the more commonplace connectivity or busy failures, the network or service might need a short period while the connectivity issues are corrected or the backlog of work is cleared.</span></span> <span data-ttu-id="f959c-125">應用程式應適當等待一段時間後再重試要求。</span><span class="sxs-lookup"><span data-stu-id="f959c-125">The application should wait for a suitable time before retrying the request.</span></span>

<span data-ttu-id="f959c-126">遇到常見的暫時性失敗，應慎選重試之間的間隔時間 (延遲)，儘可能平均分配來自多個應用程式執行個體的要求。</span><span class="sxs-lookup"><span data-stu-id="f959c-126">For the more common transient failures, the period between retries should be chosen to spread requests from multiple instances of the application as evenly as possible.</span></span> <span data-ttu-id="f959c-127">這可以減少忙碌服務繼續多載的機會。</span><span class="sxs-lookup"><span data-stu-id="f959c-127">This reduces the chance of a busy service continuing to be overloaded.</span></span> <span data-ttu-id="f959c-128">如果許多的應用程式執行個體持續重試要求使服務不勝任負荷，就會需要更長時間才能復原服務。</span><span class="sxs-lookup"><span data-stu-id="f959c-128">If many instances of an application are continually overwhelming a service with retry requests, it'll take the service longer to recover.</span></span>

<span data-ttu-id="f959c-129">如果要求仍然失敗，應用程式可以等待之後再嘗試。</span><span class="sxs-lookup"><span data-stu-id="f959c-129">If the request still fails, the application can wait and make another attempt.</span></span> <span data-ttu-id="f959c-130">如有必要，可以重複此程序並增加重試嘗試之間間隔的延遲，直到嘗試達到要求次數上限。</span><span class="sxs-lookup"><span data-stu-id="f959c-130">If necessary, this process can be repeated with increasing delays between retry attempts, until some maximum number of requests have been attempted.</span></span> <span data-ttu-id="f959c-131">延遲可以累加方式或以指數方式增加，取決於失敗類型以及它在這段期間內會修正的機率。</span><span class="sxs-lookup"><span data-stu-id="f959c-131">The delay can be increased incrementally or exponentially, depending on the type of failure and the probability that it'll be corrected during this time.</span></span>

<span data-ttu-id="f959c-132">下圖顯示使用此模式叫用託管服務中的作業。</span><span class="sxs-lookup"><span data-stu-id="f959c-132">The following diagram illustrates invoking an operation in a hosted service using this pattern.</span></span> <span data-ttu-id="f959c-133">如果在預先定義的次數之後要求仍失敗，應用程式應將錯誤視為例外狀況，並加以處理。</span><span class="sxs-lookup"><span data-stu-id="f959c-133">If the request is unsuccessful after a predefined number of attempts, the application should treat the fault as an exception and handle it accordingly.</span></span>

![圖 1 - 使用重試模式叫用託管服務中的作業](./_images/retry-pattern.png)

<span data-ttu-id="f959c-135">應用程式應將對遠端服務的所有存取嘗試包裝在程式碼中，以程式碼實作符合上述策略之一的重試原則。</span><span class="sxs-lookup"><span data-stu-id="f959c-135">The application should wrap all attempts to access a remote service in code that implements a retry policy matching one of the strategies listed above.</span></span> <span data-ttu-id="f959c-136">傳送至不同的服務要求可能會採用不同原則。</span><span class="sxs-lookup"><span data-stu-id="f959c-136">Requests sent to different services can be subject to different policies.</span></span> <span data-ttu-id="f959c-137">有些廠商提供實作了重試原則的程式庫，其中的應用程式可以指定重試次數上限、重試嘗試的間隔時間和其他參數。</span><span class="sxs-lookup"><span data-stu-id="f959c-137">Some vendors provide libraries that implement retry policies, where the application can specify the maximum number of retries, the time between retry attempts, and other parameters.</span></span>

<span data-ttu-id="f959c-138">應用程式應記錄錯誤和失敗作業的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="f959c-138">An application should log the details of faults and failing operations.</span></span> <span data-ttu-id="f959c-139">這項資訊對執行作業的人非常有用。</span><span class="sxs-lookup"><span data-stu-id="f959c-139">This information is useful to operators.</span></span> <span data-ttu-id="f959c-140">如果服務經常無法使用或忙碌，通常是因為服務耗盡其資源。</span><span class="sxs-lookup"><span data-stu-id="f959c-140">If a service is frequently unavailable or busy, it's often because the service has exhausted its resources.</span></span> <span data-ttu-id="f959c-141">您可以相應放大服務，減少這些錯誤的發生頻率。</span><span class="sxs-lookup"><span data-stu-id="f959c-141">You can reduce the frequency of these faults by scaling out the service.</span></span> <span data-ttu-id="f959c-142">例如，如果資料庫服務持續多載，分割資料庫並將負載分散到多部伺服器可能有幫助。</span><span class="sxs-lookup"><span data-stu-id="f959c-142">For example, if a database service is continually overloaded, it might be beneficial to partition the database and spread the load across multiple servers.</span></span>

> <span data-ttu-id="f959c-143">[Microsoft Entity Framework](https://docs.microsoft.com/ef/) 提供重試資料庫作業的機能。</span><span class="sxs-lookup"><span data-stu-id="f959c-143">[Microsoft Entity Framework](https://docs.microsoft.com/ef/) provides facilities for retrying database operations.</span></span> <span data-ttu-id="f959c-144">此外，大多數的 Azure 服務與用戶端 SDK 皆包含重試機制。</span><span class="sxs-lookup"><span data-stu-id="f959c-144">Also, most Azure services and client SDKs include a retry mechanism.</span></span> <span data-ttu-id="f959c-145">如需詳細資訊，請參閱[特定服務的重試指引](https://docs.microsoft.com/en-us/azure/architecture/best-practices/retry-service-specific)。</span><span class="sxs-lookup"><span data-stu-id="f959c-145">For more information, see [Retry guidance for specific services](https://docs.microsoft.com/en-us/azure/architecture/best-practices/retry-service-specific).</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="f959c-146">問題和考量</span><span class="sxs-lookup"><span data-stu-id="f959c-146">Issues and considerations</span></span>

<span data-ttu-id="f959c-147">當您在決定如何實作此模式時，應考慮下列幾點。</span><span class="sxs-lookup"><span data-stu-id="f959c-147">You should consider the following points when deciding how to implement this pattern.</span></span>

<span data-ttu-id="f959c-148">重試原則應該加以調整以符合應用程式的商務需求和失敗的本質。</span><span class="sxs-lookup"><span data-stu-id="f959c-148">The retry policy should be tuned to match the business requirements of the application and the nature of the failure.</span></span> <span data-ttu-id="f959c-149">就某些非關鍵的作業而言，最好是快速失敗而不是重試數次，以免影響應用程式的輸送量。</span><span class="sxs-lookup"><span data-stu-id="f959c-149">For some noncritical operations, it's better to fail fast rather than retry several times and impact the throughput of the application.</span></span> <span data-ttu-id="f959c-150">例如，在存取遠端服務的互動式 Web 應用程式中，最好是經歷少數次短延遲時間的重試嘗試之後便失敗，並向使用者顯示適當的訊息 (例如，「請稍後再試一次」)。</span><span class="sxs-lookup"><span data-stu-id="f959c-150">For example, in an interactive web application accessing a remote service, it's better to fail after a smaller number of retries with only a short delay between retry attempts, and display a suitable message to the user (for example, “please try again later”).</span></span> <span data-ttu-id="f959c-151">就批次應用程式而言，可能更適合增加重試嘗試的次數，並以指數方式增加嘗試之間的延遲。</span><span class="sxs-lookup"><span data-stu-id="f959c-151">For a batch application, it might be more appropriate to increase the number of retry attempts with an exponentially increasing delay between attempts.</span></span>

<span data-ttu-id="f959c-152">積極的重試原則在嘗試之間間隔的延遲時間較短，再加上大量重試，對已接近或達到產能的忙碌執行服務更是雪上加霜。</span><span class="sxs-lookup"><span data-stu-id="f959c-152">An aggressive retry policy with minimal delay between attempts, and a large number of retries, could further degrade a busy service that's running close to or at capacity.</span></span> <span data-ttu-id="f959c-153">如果持續嘗試執行失敗的作業，此重試原則也可能會影響應用程式的回應能力。</span><span class="sxs-lookup"><span data-stu-id="f959c-153">This retry policy could also affect the responsiveness of the application if it's continually trying to perform a failing operation.</span></span>

<span data-ttu-id="f959c-154">如果在大量重試之後要求仍失敗，最好避免應用程式再向相同資源提出要求，只要立即回報失敗。</span><span class="sxs-lookup"><span data-stu-id="f959c-154">If a request still fails after a significant number of retries, it's better for the application to prevent further requests going to the same resource and simply report a failure immediately.</span></span> <span data-ttu-id="f959c-155">當期限到期時，應用程式可以暫時允許一或多個要求，看看是否成功。</span><span class="sxs-lookup"><span data-stu-id="f959c-155">When the period expires, the application can tentatively allow one or more requests through to see whether they're successful.</span></span> <span data-ttu-id="f959c-156">如需此策略的詳細資料，請參閱[斷路器模式](circuit-breaker.md)。</span><span class="sxs-lookup"><span data-stu-id="f959c-156">For more details of this strategy, see the [Circuit Breaker pattern](circuit-breaker.md).</span></span>

<span data-ttu-id="f959c-157">考慮作業是否為等冪。</span><span class="sxs-lookup"><span data-stu-id="f959c-157">Consider whether the operation is idempotent.</span></span> <span data-ttu-id="f959c-158">如果是，重試原本就安全。</span><span class="sxs-lookup"><span data-stu-id="f959c-158">If so, it's inherently safe to retry.</span></span> <span data-ttu-id="f959c-159">否則，重試可能會導致作業執行一次以上，並產品非預期的副作用。</span><span class="sxs-lookup"><span data-stu-id="f959c-159">Otherwise, retries could cause the operation to be executed more than once, with unintended side effects.</span></span> <span data-ttu-id="f959c-160">例如，服務可能會接收要求、處理要求成功，但無法傳送回應。</span><span class="sxs-lookup"><span data-stu-id="f959c-160">For example, a service might receive the request, process the request successfully, but fail to send a response.</span></span> <span data-ttu-id="f959c-161">此時，重試邏輯可能會假設服務沒有收到第一個要求，並重新傳送要求。</span><span class="sxs-lookup"><span data-stu-id="f959c-161">At that point, the retry logic might re-send the request, assuming that the first request wasn't received.</span></span>

<span data-ttu-id="f959c-162">對服務的要求失敗可能因為各種原因並引發不同的例外狀況，取決於失敗的本質。</span><span class="sxs-lookup"><span data-stu-id="f959c-162">A request to a service can fail for a variety of reasons raising different exceptions depending on the nature of the failure.</span></span> <span data-ttu-id="f959c-163">某些例外狀況表示失敗可以快速地解決，有些例外狀況則表示失敗會再持續下去。</span><span class="sxs-lookup"><span data-stu-id="f959c-163">Some exceptions indicate a failure that can be resolved quickly, while others indicate that the failure is longer lasting.</span></span> <span data-ttu-id="f959c-164">根據例外狀況的類型來調整重試原則的重試嘗試間隔時間，十分實用。</span><span class="sxs-lookup"><span data-stu-id="f959c-164">It's useful for the retry policy to adjust the time between retry attempts based on the type of the exception.</span></span>

<span data-ttu-id="f959c-165">重試作業是交易的一部分，考慮其如何影響整體交易的一致性。</span><span class="sxs-lookup"><span data-stu-id="f959c-165">Consider how retrying an operation that's part of a transaction will affect the overall transaction consistency.</span></span> <span data-ttu-id="f959c-166">微調交易作業的重試原則，以獲得最高的成功機率，並減少復原所有交易步驟的必要。</span><span class="sxs-lookup"><span data-stu-id="f959c-166">Fine tune the retry policy for transactional operations to maximize the chance of success and reduce the need to undo all the transaction steps.</span></span>

<span data-ttu-id="f959c-167">確保所有的重試程式碼針對各種失敗狀況經過完整測試。</span><span class="sxs-lookup"><span data-stu-id="f959c-167">Ensure that all retry code is fully tested against a variety of failure conditions.</span></span> <span data-ttu-id="f959c-168">確認它不會嚴重影響應用程式的效能或可靠性，不會導致服務和資源的負載過大、或產生競爭情況或瓶頸。</span><span class="sxs-lookup"><span data-stu-id="f959c-168">Check that it doesn't severely impact the performance or reliability of the application, cause excessive load on services and resources, or generate race conditions or bottlenecks.</span></span>

<span data-ttu-id="f959c-169">只在完全了解失敗作業的脈絡內容時，才實作重試邏輯。</span><span class="sxs-lookup"><span data-stu-id="f959c-169">Implement retry logic only where the full context of a failing operation is understood.</span></span> <span data-ttu-id="f959c-170">例如，如果包含重試原則的工作會叫用另一項也包含重試原則的工作，這額外的重試層會大大增加處理的時間。</span><span class="sxs-lookup"><span data-stu-id="f959c-170">For example, if a task that contains a retry policy invokes another task that also contains a retry policy, this extra layer of retries can add long delays to the processing.</span></span> <span data-ttu-id="f959c-171">最好是將較低層級的工作設定為可以快速失敗，並向叫用它的工作回報失敗的原因。</span><span class="sxs-lookup"><span data-stu-id="f959c-171">It might be better to configure the lower-level task to fail fast and report the reason for the failure back to the task that invoked it.</span></span> <span data-ttu-id="f959c-172">然後這個較高層級的工作就可以根據自己的原則處理失敗。</span><span class="sxs-lookup"><span data-stu-id="f959c-172">This higher-level task can then handle the failure based on its own policy.</span></span>

<span data-ttu-id="f959c-173">請務必記錄所有導致重試的連線失敗，以利找出基礎應用程式、服務或資源的問題。</span><span class="sxs-lookup"><span data-stu-id="f959c-173">It's important to log all connectivity failures that cause a retry so that underlying problems with the application, services, or resources can be identified.</span></span>

<span data-ttu-id="f959c-174">調查服務或資源最有可能發生的錯誤，探索其是否會長時間持續或可以終結。</span><span class="sxs-lookup"><span data-stu-id="f959c-174">Investigate the faults that are most likely to occur for a service or a resource to discover if they're likely to be long lasting or terminal.</span></span> <span data-ttu-id="f959c-175">如有會，最好是將錯誤當作例外狀況處理。</span><span class="sxs-lookup"><span data-stu-id="f959c-175">If they are, it's better to handle the fault as an exception.</span></span> <span data-ttu-id="f959c-176">應用程式可以報告或記錄例外狀況，然後叫用替代服務 (如果有的話) 或提供效能降低的功能，並繼續操作。</span><span class="sxs-lookup"><span data-stu-id="f959c-176">The application can report or log the exception, and then try to continue either by invoking an alternative service (if one is available), or by offering degraded functionality.</span></span> <span data-ttu-id="f959c-177">如需有關如何偵測及處理持續性錯誤的詳細資訊，請參閱[斷路器模式](circuit-breaker.md)。</span><span class="sxs-lookup"><span data-stu-id="f959c-177">For more information on how to detect and handle long-lasting faults, see the [Circuit Breaker pattern](circuit-breaker.md).</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="f959c-178">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="f959c-178">When to use this pattern</span></span>

<span data-ttu-id="f959c-179">當應用程式與遠端服務互動或存取遠端資源時若可能發生暫時性錯誤，請使用此模式。</span><span class="sxs-lookup"><span data-stu-id="f959c-179">Use this pattern when an application could experience transient faults as it interacts with a remote service or accesses a remote resource.</span></span> <span data-ttu-id="f959c-180">這些錯誤應該存在時間很短，且先前已失敗的要求可能在後續的嘗試時成功。</span><span class="sxs-lookup"><span data-stu-id="f959c-180">These faults are expected to be short lived, and repeating a request that has previously failed could succeed on a subsequent attempt.</span></span>

<span data-ttu-id="f959c-181">此模式可能不適合用於︰</span><span class="sxs-lookup"><span data-stu-id="f959c-181">This pattern might not be useful:</span></span>

- <span data-ttu-id="f959c-182">錯誤時可能長持續時間，因為它可能會影響應用程式的回應能力。</span><span class="sxs-lookup"><span data-stu-id="f959c-182">When a fault is likely to be long lasting, because this can affect the responsiveness of an application.</span></span> <span data-ttu-id="f959c-183">應用程式嘗試重複可能失敗的要求，可能會浪費時間和資源。</span><span class="sxs-lookup"><span data-stu-id="f959c-183">The application might be wasting time and resources trying to repeat a request that's likely to fail.</span></span>
- <span data-ttu-id="f959c-184">處理不是因為暫時性錯誤造成的失敗，例如應用程式商務邏輯中的錯誤所造成的內部例外狀況。</span><span class="sxs-lookup"><span data-stu-id="f959c-184">For handling failures that aren't due to transient faults, such as internal exceptions caused by errors in the business logic of an application.</span></span>
- <span data-ttu-id="f959c-185">作為處理系統中延展性問題的替代選項。</span><span class="sxs-lookup"><span data-stu-id="f959c-185">As an alternative to addressing scalability issues in a system.</span></span> <span data-ttu-id="f959c-186">如果應用程式頻繁遇到忙碌的錯誤，通常是存取的服務或資源應該擴充規模的訊號。</span><span class="sxs-lookup"><span data-stu-id="f959c-186">If an application experiences frequent busy faults, it's often a sign that the service or resource being accessed should be scaled up.</span></span>

## <a name="example"></a><span data-ttu-id="f959c-187">範例</span><span class="sxs-lookup"><span data-stu-id="f959c-187">Example</span></span>

<span data-ttu-id="f959c-188">此 C# 範例示範重試模式的實作。</span><span class="sxs-lookup"><span data-stu-id="f959c-188">This example in C# illustrates an implementation of the Retry pattern.</span></span> <span data-ttu-id="f959c-189">`OperationWithBasicRetryAsync` 方法 (如下所示) 會透過 `TransientOperationAsync` 方法非同步叫用外部服務。</span><span class="sxs-lookup"><span data-stu-id="f959c-189">The `OperationWithBasicRetryAsync` method, shown below, invokes an external service asynchronously through the `TransientOperationAsync` method.</span></span> <span data-ttu-id="f959c-190">`TransientOperationAsync` 方法的詳細資料為服務特定，在此範例程式碼中會省略。</span><span class="sxs-lookup"><span data-stu-id="f959c-190">The details of the `TransientOperationAsync` method will be specific to the service and are omitted from the sample code.</span></span>

```csharp
private int retryCount = 3;
private readonly TimeSpan delay = TimeSpan.FromSeconds(5);

public async Task OperationWithBasicRetryAsync()
{
  int currentRetry = 0;

  for (;;)
  {
    try
    {
      // Call external service.
      await TransientOperationAsync();

      // Return or break.
      break;
    }
    catch (Exception ex)
    {
      Trace.TraceError("Operation Exception");

      currentRetry++;

      // Check if the exception thrown was a transient exception
      // based on the logic in the error detection strategy.
      // Determine whether to retry the operation, as well as how
      // long to wait, based on the retry strategy.
      if (currentRetry > this.retryCount || !IsTransient(ex))
      {
        // If this isn't a transient error or we shouldn't retry, 
        // rethrow the exception.
        throw;
      }
    }

    // Wait to retry the operation.
    // Consider calculating an exponential delay here and
    // using a strategy best suited for the operation and fault.
    await Task.Delay(delay);
  }
}

// Async method that wraps a call to a remote service (details not shown).
private async Task TransientOperationAsync()
{
  ...
}
```

<span data-ttu-id="f959c-191">叫用這個方法的陳述式在 try/catch 區塊中，包裝在 for 迴圈中。</span><span class="sxs-lookup"><span data-stu-id="f959c-191">The statement that invokes this method is contained in a try/catch block wrapped in a for loop.</span></span> <span data-ttu-id="f959c-192">如果呼叫 `TransientOperationAsync` 方法成功，且沒有擲回例外狀況，for 迴圈就會結束。</span><span class="sxs-lookup"><span data-stu-id="f959c-192">The for loop exits if the call to the `TransientOperationAsync` method succeeds without throwing an exception.</span></span> <span data-ttu-id="f959c-193">如果 `TransientOperationAsync` 方法失敗，catch 區塊會檢查失敗的原因。</span><span class="sxs-lookup"><span data-stu-id="f959c-193">If the `TransientOperationAsync` method fails, the catch block examines the reason for the failure.</span></span> <span data-ttu-id="f959c-194">如果認為是暫時性的錯誤，程式碼會等待短暫的延遲後再重試操作。</span><span class="sxs-lookup"><span data-stu-id="f959c-194">If it's believed to be a transient error the code waits for a short delay before retrying the operation.</span></span>

<span data-ttu-id="f959c-195">for 迴圈也會追蹤此作業的嘗試次數，如果程式碼失敗三次，便假設例外狀況會長時間持續。</span><span class="sxs-lookup"><span data-stu-id="f959c-195">The for loop also tracks the number of times that the operation has been attempted, and if the code fails three times the exception is assumed to be more long lasting.</span></span> <span data-ttu-id="f959c-196">如果例外狀況不是暫時性而是持續性，catch 處理常式會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="f959c-196">If the exception isn't transient or it's long lasting, the catch handler throws an exception.</span></span> <span data-ttu-id="f959c-197">這個例外狀況會結束 for 迴圈，叫用 `OperationWithBasicRetryAsync` 方法的程式碼應該會攔截到此例外狀況。</span><span class="sxs-lookup"><span data-stu-id="f959c-197">This exception exits the for loop and should be caught by the code that invokes the `OperationWithBasicRetryAsync` method.</span></span>

<span data-ttu-id="f959c-198">`IsTransient`方法 (如下所示) 會檢查與程式碼執行環境相關的一組特定例外狀況。</span><span class="sxs-lookup"><span data-stu-id="f959c-198">The `IsTransient` method, shown below, checks for a specific set of exceptions that are relevant to the environment the code is run in.</span></span> <span data-ttu-id="f959c-199">暫時性例外狀況的定義會根據存取的資源和執行作業的環境而有所不同。</span><span class="sxs-lookup"><span data-stu-id="f959c-199">The definition of a transient exception will vary according to the resources being accessed and the environment the operation is being performed in.</span></span>

```csharp
private bool IsTransient(Exception ex)
{
  // Determine if the exception is transient.
  // In some cases this is as simple as checking the exception type, in other
  // cases it might be necessary to inspect other properties of the exception.
  if (ex is OperationTransientException)
    return true;

  var webException = ex as WebException;
  if (webException != null)
  {
    // If the web exception contains one of the following status values
    // it might be transient.
    return new[] {WebExceptionStatus.ConnectionClosed,
                  WebExceptionStatus.Timeout,
                  WebExceptionStatus.RequestCanceled }.
            Contains(webException.Status);
  }

  // Additional exception checking logic goes here.
  return false;
}
```

## <a name="related-patterns-and-guidance"></a><span data-ttu-id="f959c-200">相關的模式和指導方針</span><span class="sxs-lookup"><span data-stu-id="f959c-200">Related patterns and guidance</span></span>

- <span data-ttu-id="f959c-201">[斷路器模式](circuit-breaker.md)。</span><span class="sxs-lookup"><span data-stu-id="f959c-201">[Circuit Breaker pattern](circuit-breaker.md).</span></span> <span data-ttu-id="f959c-202">重試模式在處理暫時性錯誤上相當實用。</span><span class="sxs-lookup"><span data-stu-id="f959c-202">The Retry pattern is useful for handling transient faults.</span></span> <span data-ttu-id="f959c-203">如果失敗會長時間持續，則可能較適合實作「斷路器」模式。</span><span class="sxs-lookup"><span data-stu-id="f959c-203">If a failure is expected to be more long lasting, it might be more appropriate to implement the Circuit Breaker pattern.</span></span> <span data-ttu-id="f959c-204">重試模式也能與斷路器搭配使用，提供處理錯誤的全方位方法。</span><span class="sxs-lookup"><span data-stu-id="f959c-204">The Retry pattern can also be used in conjunction with a circuit breaker to provide a comprehensive approach to handling faults.</span></span>
- [<span data-ttu-id="f959c-205">特定服務的重試指引</span><span class="sxs-lookup"><span data-stu-id="f959c-205">Retry guidance for specific services</span></span>](https://docs.microsoft.com/en-us/azure/architecture/best-practices/retry-service-specific)
- [<span data-ttu-id="f959c-206">連線恢復功能</span><span class="sxs-lookup"><span data-stu-id="f959c-206">Connection Resiliency</span></span>](https://docs.microsoft.com/en-us/ef/core/miscellaneous/connection-resiliency)

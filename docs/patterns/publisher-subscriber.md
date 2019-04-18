---
title: 發行者-訂閱者模式
description: 讓應用程式能夠非同步地向多個感興趣的取用者宣告事件。
keywords: 設計模式
author: alexbuckgit
ms.date: 12/07/2018
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: f3d15d65aeab41977e6d30b8141baaa956da29d3
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640765"
---
# <a name="publisher-subscriber-pattern"></a><span data-ttu-id="b53a5-104">發行者-訂閱者模式</span><span class="sxs-lookup"><span data-stu-id="b53a5-104">Publisher-Subscriber pattern</span></span>

<span data-ttu-id="b53a5-105">讓應用程式而不需要結合的寄件者到接收者以非同步方式宣告的事件至多個想要的取用者。</span><span class="sxs-lookup"><span data-stu-id="b53a5-105">Enable an application to announce events to multiple interested consumers asynchronously, without coupling the senders to the receivers.</span></span>

<span data-ttu-id="b53a5-106">**也稱為**：發佈/訂閱傳訊</span><span class="sxs-lookup"><span data-stu-id="b53a5-106">**Also called**: Pub/sub messaging</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="b53a5-107">內容和問題</span><span class="sxs-lookup"><span data-stu-id="b53a5-107">Context and problem</span></span>

<span data-ttu-id="b53a5-108">在雲端式和分散式應用程式中，系統的元件常需要在發生事件時提供資訊給其他元件。</span><span class="sxs-lookup"><span data-stu-id="b53a5-108">In cloud-based and distributed applications, components of the system often need to provide information to other components as events happen.</span></span>

<span data-ttu-id="b53a5-109">非同步傳訊是分離傳送者與取用者，並避免在傳送者等候回應時加以封鎖的有效方式。</span><span class="sxs-lookup"><span data-stu-id="b53a5-109">Asynchronous messaging is an effective way to decouple senders from consumers, and avoid blocking the sender to wait for a response.</span></span> <span data-ttu-id="b53a5-110">不過，讓每個取用者使用專用訊息佇列的做法，並無法有效地擴充至許多取用者。</span><span class="sxs-lookup"><span data-stu-id="b53a5-110">However, using a dedicated message queue for each consumer does not effectively scale to many consumers.</span></span> <span data-ttu-id="b53a5-111">此外，某些取用者可能只對某部分的資訊感興趣。</span><span class="sxs-lookup"><span data-stu-id="b53a5-111">Also, some of the consumers might be interested in only a subset of the information.</span></span> <span data-ttu-id="b53a5-112">傳送者如何將事件宣告給所有感興趣的取用者，而不需要知道他們的身分識別？</span><span class="sxs-lookup"><span data-stu-id="b53a5-112">How can the sender announce events to all interested consumers without knowing their identities?</span></span>

## <a name="solution"></a><span data-ttu-id="b53a5-113">解決方法</span><span class="sxs-lookup"><span data-stu-id="b53a5-113">Solution</span></span>

<span data-ttu-id="b53a5-114">導入非同步傳訊子系統，其中包括下列各項：</span><span class="sxs-lookup"><span data-stu-id="b53a5-114">Introduce an asynchronous messaging subsystem that includes the following:</span></span>

- <span data-ttu-id="b53a5-115">傳送者所使用的輸入傳訊通道。</span><span class="sxs-lookup"><span data-stu-id="b53a5-115">An input messaging channel used by the sender.</span></span> <span data-ttu-id="b53a5-116">傳送者可使用已知的訊息格式將事件封裝到訊息中，並透過輸入通道傳送這些訊息。</span><span class="sxs-lookup"><span data-stu-id="b53a5-116">The sender packages events into messages, using a known message format, and sends these messages via the input channel.</span></span> <span data-ttu-id="b53a5-117">採用此模式的傳送者也稱為*發行者*。</span><span class="sxs-lookup"><span data-stu-id="b53a5-117">The sender in this pattern is also called the *publisher*.</span></span>

  > [!NOTE]
  > <span data-ttu-id="b53a5-118">*訊息*是資料的封包。</span><span class="sxs-lookup"><span data-stu-id="b53a5-118">A *message* is a packet of data.</span></span> <span data-ttu-id="b53a5-119">*事件*是一種訊息，可向其他元件通知有變更或動作發生的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="b53a5-119">An *event* is a message that notifies other components about a change or an action that has taken place.</span></span>

- <span data-ttu-id="b53a5-120">每個取用者會有一個輸出傳訊通道。</span><span class="sxs-lookup"><span data-stu-id="b53a5-120">One output messaging channel per consumer.</span></span> <span data-ttu-id="b53a5-121">取用者也稱為*訂閱者*。</span><span class="sxs-lookup"><span data-stu-id="b53a5-121">The consumers are known as *subscribers*.</span></span>

- <span data-ttu-id="b53a5-122">將每則訊息從輸入通道複製到輸出通道，讓所有對該訊息感興趣的訂閱者取得訊息的機制。</span><span class="sxs-lookup"><span data-stu-id="b53a5-122">A mechanism for copying each message from the input channel to the output channels for all subscribers interested in that message.</span></span> <span data-ttu-id="b53a5-123">這項作業通常由訊息代理程式或事件匯流排之類的媒介處理。</span><span class="sxs-lookup"><span data-stu-id="b53a5-123">This operation is typically handled by a intermediary such as a message broker or event bus.</span></span>

<span data-ttu-id="b53a5-124">下列圖表顯示此模式的邏輯元件：</span><span class="sxs-lookup"><span data-stu-id="b53a5-124">The following diagram shows the logical components of this pattern:</span></span>

![使用訊息代理程式的發佈-訂閱模式](./_images/publish-subscribe.png)
 
<span data-ttu-id="b53a5-126">發佈/訂閱傳訊具有下列優點：</span><span class="sxs-lookup"><span data-stu-id="b53a5-126">Pub/sub messaging has the following benefits:</span></span>

- <span data-ttu-id="b53a5-127">它可分離仍需要進行通訊的子系統。</span><span class="sxs-lookup"><span data-stu-id="b53a5-127">It decouples subsystems that still need to communicate.</span></span> <span data-ttu-id="b53a5-128">子系統可以分開管理，而訊息即使在一或多個接收者離線時仍可受到適當管理。</span><span class="sxs-lookup"><span data-stu-id="b53a5-128">Subsystems can be managed independently, and messages can be properly managed even if one or more receivers are offline.</span></span>

- <span data-ttu-id="b53a5-129">它可增加延展性，並改善傳送者的回應能力。</span><span class="sxs-lookup"><span data-stu-id="b53a5-129">It increases scalability and improves responsiveness of the sender.</span></span> <span data-ttu-id="b53a5-130">傳送者可以將單一訊息快速傳送至輸入通道，然後返回其核心處理職責。</span><span class="sxs-lookup"><span data-stu-id="b53a5-130">The sender can quickly send a single message to the input channel, then return to its core processing responsibilities.</span></span> <span data-ttu-id="b53a5-131">傳訊基礎結構負責確保訊息會傳遞給感興趣的訂閱者。</span><span class="sxs-lookup"><span data-stu-id="b53a5-131">The messaging infrastructure is responsible for ensuring messages are delivered to interested subscribers.</span></span>

- <span data-ttu-id="b53a5-132">它可提升可靠性。</span><span class="sxs-lookup"><span data-stu-id="b53a5-132">It improves reliability.</span></span> <span data-ttu-id="b53a5-133">非同步傳訊可協助應用程式在增加的負載下仍繼續順暢地執行，並更有效地處理間歇性失敗。</span><span class="sxs-lookup"><span data-stu-id="b53a5-133">Asynchronous messaging helps applications continue to run smoothly under increased loads and handle intermittent failures more effectively.</span></span>

- <span data-ttu-id="b53a5-134">它可支援延後或排程的處理。</span><span class="sxs-lookup"><span data-stu-id="b53a5-134">It allows for deferred or scheduled processing.</span></span> <span data-ttu-id="b53a5-135">訂閱者可等到離峰時間再收取訊息，或者，訊息可根據特定排程進行路由或處理。</span><span class="sxs-lookup"><span data-stu-id="b53a5-135">Subscribers can wait to pick up messages until off-peak hours, or messages can be routed or processed according to a specific schedule.</span></span>

- <span data-ttu-id="b53a5-136">它可讓使用不同平台、程式設計語言或通訊協定的系統輕易整合，並且可輕易整合內部部署系統與雲端中執行的應用程式。</span><span class="sxs-lookup"><span data-stu-id="b53a5-136">It enables simpler integration between systems using different platforms, programming languages, or communication protocols, as well as between on-premises systems and applications running in the cloud.</span></span>

- <span data-ttu-id="b53a5-137">它有助於執行整個企業的非同步工作流程。</span><span class="sxs-lookup"><span data-stu-id="b53a5-137">It facilitates asynchronous workflows across an enterprise.</span></span>

- <span data-ttu-id="b53a5-138">它能夠提高可測試性。</span><span class="sxs-lookup"><span data-stu-id="b53a5-138">It improves testability.</span></span> <span data-ttu-id="b53a5-139">在整體的整合測試策略之中，可同時監視通道以及檢查或記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="b53a5-139">Channels can be monitored and messages can be inspected or logged as part of an overall integration test strategy.</span></span>

- <span data-ttu-id="b53a5-140">它可區隔應用程式的關注點。</span><span class="sxs-lookup"><span data-stu-id="b53a5-140">It provides separation of concerns for your applications.</span></span> <span data-ttu-id="b53a5-141">每個應用程式都可以專注於其核心功能，而由傳訊基礎結構負責處理可靠地將訊息路由至多個取用者所需的一切事務。</span><span class="sxs-lookup"><span data-stu-id="b53a5-141">Each application can focus on its core capabilities, while the messaging infrastructure handles everything required to reliably route messages to multiple consumers.</span></span> 

## <a name="issues-and-considerations"></a><span data-ttu-id="b53a5-142">問題和考量</span><span class="sxs-lookup"><span data-stu-id="b53a5-142">Issues and considerations</span></span>

<span data-ttu-id="b53a5-143">當您決定如何實作此模式時，請考慮下列幾點：</span><span class="sxs-lookup"><span data-stu-id="b53a5-143">Consider the following points when deciding how to implement this pattern:</span></span>

- <span data-ttu-id="b53a5-144">**現有技術。**</span><span class="sxs-lookup"><span data-stu-id="b53a5-144">**Existing technologies.**</span></span> <span data-ttu-id="b53a5-145">強烈建議您使用支援發佈-訂閱模型的可用傳訊產品和服務，而不要自行建置。</span><span class="sxs-lookup"><span data-stu-id="b53a5-145">It is strongly recommended to use available messaging products and services that support a publish-subscribe model, rather than building your own.</span></span> <span data-ttu-id="b53a5-146">在 Azure 中，請考慮使用[服務匯流排](/azure/service-bus-messaging/)或[事件方格](/azure/event-grid/)。</span><span class="sxs-lookup"><span data-stu-id="b53a5-146">In Azure, consider using [Service Bus](/azure/service-bus-messaging/) or [Event Grid](/azure/event-grid/).</span></span> <span data-ttu-id="b53a5-147">其他可用於發佈/訂閱傳訊的技術包含 Redis、RabbitMQ 及 Apache Kafka。</span><span class="sxs-lookup"><span data-stu-id="b53a5-147">Other technologies that can be used for pub/sub messaging include Redis, RabbitMQ, and Apache Kafka.</span></span>

- <span data-ttu-id="b53a5-148">**訂閱處理。**</span><span class="sxs-lookup"><span data-stu-id="b53a5-148">**Subscription handling.**</span></span> <span data-ttu-id="b53a5-149">傳訊基礎結構必須提供取用者可用來訂閱或取消訂閱可用通道的機制。</span><span class="sxs-lookup"><span data-stu-id="b53a5-149">The messaging infrastructure must provide mechanisms that consumers can use to subscribe to or unsubscribe from available channels.</span></span>

- <span data-ttu-id="b53a5-150">**安全性。**</span><span class="sxs-lookup"><span data-stu-id="b53a5-150">**Security.**</span></span> <span data-ttu-id="b53a5-151">對任何訊息通道的連線都必須受到安全性原則的限制，以防止未經授權的使用者或應用程式竊聽。</span><span class="sxs-lookup"><span data-stu-id="b53a5-151">Connecting to any message channel must be restricted by security policy to prevent eavesdropping by unauthorized users or applications.</span></span>

- <span data-ttu-id="b53a5-152">**訊息子集。**</span><span class="sxs-lookup"><span data-stu-id="b53a5-152">**Subsets of messages.**</span></span> <span data-ttu-id="b53a5-153">訂閱者通常只會對發行者所分送的某部分訊息感興趣。</span><span class="sxs-lookup"><span data-stu-id="b53a5-153">Subscribers are usually only interested in subset of the messages distributed by a publisher.</span></span> <span data-ttu-id="b53a5-154">傳訊服務通常可讓訂閱者依據下列條件縮小收到訊息集的範圍：</span><span class="sxs-lookup"><span data-stu-id="b53a5-154">Messaging services often allow subscribers to narrow the set of messages received by:</span></span>

  - <span data-ttu-id="b53a5-155">**主題。**</span><span class="sxs-lookup"><span data-stu-id="b53a5-155">**Topics.**</span></span> <span data-ttu-id="b53a5-156">每個主題都有專用的輸出通道，而每個取用者可以訂閱所有相關的主題。</span><span class="sxs-lookup"><span data-stu-id="b53a5-156">Each topic has a dedicated output channel, and each consumer can subscribe to all relevant topics.</span></span>
  - <span data-ttu-id="b53a5-157">**內容篩選。**</span><span class="sxs-lookup"><span data-stu-id="b53a5-157">**Content filtering.**</span></span> <span data-ttu-id="b53a5-158">訊息會根據每個訊息的內容受到檢查和進行分送。</span><span class="sxs-lookup"><span data-stu-id="b53a5-158">Messages are inspected and distributed based on the content of each message.</span></span> <span data-ttu-id="b53a5-159">每個訂閱者均可指定他感興趣的內容。</span><span class="sxs-lookup"><span data-stu-id="b53a5-159">Each subscriber can specify the content it is interested in.</span></span>

- <span data-ttu-id="b53a5-160">**萬用字元訂閱者。**</span><span class="sxs-lookup"><span data-stu-id="b53a5-160">**Wildcard subscribers.**</span></span> <span data-ttu-id="b53a5-161">請考慮允許訂閱者透過萬用字元訂閱多個主題。</span><span class="sxs-lookup"><span data-stu-id="b53a5-161">Consider allowing subscribers to subscribe to multiple topics via wildcards.</span></span>

- <span data-ttu-id="b53a5-162">**雙向通訊。**</span><span class="sxs-lookup"><span data-stu-id="b53a5-162">**Bi-directional communication.**</span></span> <span data-ttu-id="b53a5-163">發佈-訂閱系統中的通道會被視為單向。</span><span class="sxs-lookup"><span data-stu-id="b53a5-163">The channels in a publish-subscribe system are treated as unidirectional.</span></span> <span data-ttu-id="b53a5-164">如果特定訂閱者都需要傳送通知或傳回狀態給發行者，請考慮使用[要求/回覆模式](http://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html)。</span><span class="sxs-lookup"><span data-stu-id="b53a5-164">If a specific subscriber needs to send acknowledgement or communicate status back to the publisher, consider using the [Request/Reply Pattern](http://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html).</span></span> <span data-ttu-id="b53a5-165">此模式會使用一個通道將訊息傳送給訂閱者，並使用另一個回覆通道與發行者通訊。</span><span class="sxs-lookup"><span data-stu-id="b53a5-165">This pattern uses one channel to send a message to the subscriber, and a separate reply channel for communicating back to the publisher.</span></span>

- <span data-ttu-id="b53a5-166">**訊息排序。**</span><span class="sxs-lookup"><span data-stu-id="b53a5-166">**Message ordering.**</span></span> <span data-ttu-id="b53a5-167">取用者執行個體接收訊息的順序並不一定，而且不一定會反映訊息的建立順序。</span><span class="sxs-lookup"><span data-stu-id="b53a5-167">The order in which consumer instances receive messages isn't guaranteed, and doesn't necessarily reflect the order in which the messages were created.</span></span> <span data-ttu-id="b53a5-168">設計系統時，請確保訊息處理為等冪方式，以避免對訊息處理順序有依賴性。</span><span class="sxs-lookup"><span data-stu-id="b53a5-168">Design the system to ensure that message processing is idempotent to help eliminate any dependency on the order of message handling.</span></span>

- <span data-ttu-id="b53a5-169">**訊息優先順序。**</span><span class="sxs-lookup"><span data-stu-id="b53a5-169">**Message priority.**</span></span> <span data-ttu-id="b53a5-170">有些解決方案可能會需要以特定順序處理訊息。</span><span class="sxs-lookup"><span data-stu-id="b53a5-170">Some solutions may require that messages are processed in a specific order.</span></span> <span data-ttu-id="b53a5-171">[優先順序佇列模式](priority-queue.md)提供可確保特定訊息會在其他訊息之前傳遞的機制。</span><span class="sxs-lookup"><span data-stu-id="b53a5-171">The [Priority Queue pattern](priority-queue.md) provides a mechanism for ensuring specific messages are delivered before others.</span></span>

- <span data-ttu-id="b53a5-172">**有害訊息。**</span><span class="sxs-lookup"><span data-stu-id="b53a5-172">**Poison messages.**</span></span> <span data-ttu-id="b53a5-173">訊息格式不正確，或工作需要存取的資源無法使用，可能會導致服務執行個體失敗。</span><span class="sxs-lookup"><span data-stu-id="b53a5-173">A malformed message, or a task that requires access to resources that aren't available, can cause a service instance to fail.</span></span> <span data-ttu-id="b53a5-174">系統應避免讓這類訊息傳回至佇列。</span><span class="sxs-lookup"><span data-stu-id="b53a5-174">The system should prevent such messages being returned to the queue.</span></span> <span data-ttu-id="b53a5-175">系統應擷取這些訊息的詳細資料並儲存到其他位置，以視需要對它們進行分析。</span><span class="sxs-lookup"><span data-stu-id="b53a5-175">Instead, capture and store the details of these messages elsewhere so that they can be analyzed if necessary.</span></span>

- <span data-ttu-id="b53a5-176">**重複訊息。**</span><span class="sxs-lookup"><span data-stu-id="b53a5-176">**Repeated messages.**</span></span> <span data-ttu-id="b53a5-177">相同的訊息可能會傳送多次。</span><span class="sxs-lookup"><span data-stu-id="b53a5-177">The same message might be sent more than once.</span></span> <span data-ttu-id="b53a5-178">例如，傳送者有可能在張貼訊息後作業失敗。</span><span class="sxs-lookup"><span data-stu-id="b53a5-178">For example, the sender might fail after posting a message.</span></span> <span data-ttu-id="b53a5-179">然後，傳送者的新執行個體可能會啟動並重複傳訊。</span><span class="sxs-lookup"><span data-stu-id="b53a5-179">Then a new instance of the sender might start up and repeat the message.</span></span> <span data-ttu-id="b53a5-180">傳訊基礎結構應根據訊息識別碼實作重複訊息偵測和移除 (也稱為刪除重複訊息)，以提供「最多一次」的訊息傳遞。</span><span class="sxs-lookup"><span data-stu-id="b53a5-180">The messaging infrastructure should implement duplicate message detection and removal (also known as de-duping) based on message IDs in order to provide at-most-once delivery of messages.</span></span>

- <span data-ttu-id="b53a5-181">**訊息到期。**</span><span class="sxs-lookup"><span data-stu-id="b53a5-181">**Message expiration.**</span></span> <span data-ttu-id="b53a5-182">訊息可能會有有限的存留期。</span><span class="sxs-lookup"><span data-stu-id="b53a5-182">A message might have a limited lifetime.</span></span> <span data-ttu-id="b53a5-183">訊息若未在這段時間內處理，則可能不再具有相關性，而應予以捨棄。</span><span class="sxs-lookup"><span data-stu-id="b53a5-183">If it isn't processed within this period, it might no longer be relevant and should be discarded.</span></span> <span data-ttu-id="b53a5-184">寄件者可以指定到期時間，做為訊息中的資料的一部分。</span><span class="sxs-lookup"><span data-stu-id="b53a5-184">A sender can specify an expiration time as part of the data in the message.</span></span> <span data-ttu-id="b53a5-185">接收者可以先檢查此資訊，再決定是否要執行與訊息相關聯的商務邏輯。</span><span class="sxs-lookup"><span data-stu-id="b53a5-185">A receiver can examine this information before deciding whether to perform the business logic associated with the message.</span></span>

- <span data-ttu-id="b53a5-186">**訊息排程。**</span><span class="sxs-lookup"><span data-stu-id="b53a5-186">**Message scheduling.**</span></span> <span data-ttu-id="b53a5-187">訊息可能會暫時禁止傳送，而在特定日期和時間之前不會進行處理。</span><span class="sxs-lookup"><span data-stu-id="b53a5-187">A message might be temporarily embargoed and should not be processed until a specific date and time.</span></span> <span data-ttu-id="b53a5-188">接收者在此時間之前應該不會收到訊息。</span><span class="sxs-lookup"><span data-stu-id="b53a5-188">The message should not be available to a receiver until this time.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="b53a5-189">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="b53a5-189">When to use this pattern</span></span>

<span data-ttu-id="b53a5-190">使用此模式的時機包括：</span><span class="sxs-lookup"><span data-stu-id="b53a5-190">Use this pattern when:</span></span>

- <span data-ttu-id="b53a5-191">應用程式需要將訊息廣播給大量取用者。</span><span class="sxs-lookup"><span data-stu-id="b53a5-191">An application needs to broadcast information to a significant number of consumers.</span></span>

- <span data-ttu-id="b53a5-192">應用程式需要與一或多個獨立開發的應用程式或服務通訊，且此類應用程式或服務可能使用不同的平台、程式設計語言和通訊協定。</span><span class="sxs-lookup"><span data-stu-id="b53a5-192">An application needs to communicate with one or more independently-developed applications or services, which may use different platforms, programming languages, and communication protocols.</span></span>

- <span data-ttu-id="b53a5-193">應用程式可傳送訊息給取用者資訊，而不需要取用者提供即時回應。</span><span class="sxs-lookup"><span data-stu-id="b53a5-193">An application can send information to consumers without requiring real-time responses from the consumers.</span></span>

- <span data-ttu-id="b53a5-194">要整合的系統依設計可支援其資料的最終一致性模型。</span><span class="sxs-lookup"><span data-stu-id="b53a5-194">The systems being integrated are designed to support an eventual consistency model for their data.</span></span>

- <span data-ttu-id="b53a5-195">應用程式需要將資訊傳達給多個取用者，而這些取用者可能具有與傳送者不同的可用性需求或執行時間排程。</span><span class="sxs-lookup"><span data-stu-id="b53a5-195">An application needs to communicate information to multiple consumers, which may have different availability requirements or uptime schedules than the sender.</span></span>

<span data-ttu-id="b53a5-196">此模式可能不適合下列時機︰</span><span class="sxs-lookup"><span data-stu-id="b53a5-196">This pattern might not be useful when:</span></span>

- <span data-ttu-id="b53a5-197">應用程式只有少數取用者需要與產生端應用程式截然不同的資訊。</span><span class="sxs-lookup"><span data-stu-id="b53a5-197">An application has only a few consumers who need significantly different information from the producing application.</span></span>

- <span data-ttu-id="b53a5-198">應用程式需要與取用者進行近乎即時的互動。</span><span class="sxs-lookup"><span data-stu-id="b53a5-198">An application requires near real-time interaction with consumers.</span></span>

## <a name="example"></a><span data-ttu-id="b53a5-199">範例</span><span class="sxs-lookup"><span data-stu-id="b53a5-199">Example</span></span>

<span data-ttu-id="b53a5-200">下圖顯示使用服務匯流排來協調工作流程，並使用事件方格通知子系統有事件發生的企業整合架構。</span><span class="sxs-lookup"><span data-stu-id="b53a5-200">The following diagram shows an enterprise integration architecture that uses Service Bus to coordinate workflows, and Event Grid notify subsystems of events that occur.</span></span> <span data-ttu-id="b53a5-201">如需詳細資訊，請參閱[使用訊息佇列和事件在 Azure 上的企業整合](../reference-architectures/enterprise-integration/queues-events.md)。</span><span class="sxs-lookup"><span data-stu-id="b53a5-201">For more information, see [Enterprise integration on Azure using message queues and events](../reference-architectures/enterprise-integration/queues-events.md).</span></span>

![企業整合架構](../reference-architectures/enterprise-integration/_images/enterprise-integration-queues-events.png)

## <a name="related-patterns-and-guidance"></a><span data-ttu-id="b53a5-203">相關的模式和指導方針</span><span class="sxs-lookup"><span data-stu-id="b53a5-203">Related patterns and guidance</span></span>

<span data-ttu-id="b53a5-204">下列是實作此模式時可能相關的模式和指導方針：</span><span class="sxs-lookup"><span data-stu-id="b53a5-204">The following patterns and guidance might be relevant when implementing this pattern:</span></span>

- <span data-ttu-id="b53a5-205">[在傳遞訊息的 Azure 服務之間進行選擇](/azure/event-grid/compare-messaging-services)。</span><span class="sxs-lookup"><span data-stu-id="b53a5-205">[Choose between Azure services that deliver messages](/azure/event-grid/compare-messaging-services).</span></span>

- <span data-ttu-id="b53a5-206">[事件驅動架構樣式](../guide/architecture-styles/event-driven.md)是使用發佈/訂閱傳訊的架構樣式。</span><span class="sxs-lookup"><span data-stu-id="b53a5-206">The [Event-driven architecture style](../guide/architecture-styles/event-driven.md) is an architecture style that uses pub/sub messaging.</span></span>

- <span data-ttu-id="b53a5-207">[非同步傳訊入門](https://msdn.microsoft.com/library/dn589781.aspx)。</span><span class="sxs-lookup"><span data-stu-id="b53a5-207">[Asynchronous Messaging Primer](https://msdn.microsoft.com/library/dn589781.aspx).</span></span> <span data-ttu-id="b53a5-208">訊息佇列是非同步通訊機制。</span><span class="sxs-lookup"><span data-stu-id="b53a5-208">Message queues are an asynchronous communications mechanism.</span></span> <span data-ttu-id="b53a5-209">如果取用者服務需要傳送回覆至應用程式，可能必須實作某種形式的回應訊息。</span><span class="sxs-lookup"><span data-stu-id="b53a5-209">If a consumer service needs to send a reply to an application, it might be necessary to implement some form of response messaging.</span></span> <span data-ttu-id="b53a5-210">「非同步傳訊入門」提供如何使用訊息佇列實作要求/回覆訊息的資訊。</span><span class="sxs-lookup"><span data-stu-id="b53a5-210">The Asynchronous Messaging Primer provides information on how to implement request/reply messaging using message queues.</span></span>

- <span data-ttu-id="b53a5-211">[觀察者模式](https://en.wikipedia.org/wiki/Observer_pattern)。</span><span class="sxs-lookup"><span data-stu-id="b53a5-211">[Observer Pattern](https://en.wikipedia.org/wiki/Observer_pattern).</span></span> <span data-ttu-id="b53a5-212">發佈-訂閱模式透過非同步傳訊將來自觀察者的主題分離，以觀察者模式作為其建置基礎。</span><span class="sxs-lookup"><span data-stu-id="b53a5-212">The Publish-Subscribe pattern builds on the Observer pattern by decoupling subjects from observers via asynchronous messaging.</span></span>

- <span data-ttu-id="b53a5-213">[訊息代理程式模式](https://en.wikipedia.org/wiki/Message_broker)。</span><span class="sxs-lookup"><span data-stu-id="b53a5-213">[Message Broker Pattern](https://en.wikipedia.org/wiki/Message_broker).</span></span> <span data-ttu-id="b53a5-214">許多傳訊子系統支援發佈-訂閱模型可透過訊息代理程式會實作。</span><span class="sxs-lookup"><span data-stu-id="b53a5-214">Many messaging subsystems that support a publish-subscribe model are implemented via a message broker.</span></span>

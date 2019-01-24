---
title: 使用訊息佇列和事件的企業整合
titleSuffix: Azure Reference Architectures
description: 使用 Azure Logic Apps、Azure APIM、Azure 服務匯流排和 Azure 事件方格來實作企業整合模式的建議架構。
author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: reference-architecture
ms.date: 12/03/2018
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: integration-services
ms.openlocfilehash: 4c9d2e201bcfc077990d746a1decd55ede2f220a
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54480669"
---
# <a name="enterprise-integration-on-azure-using-message-queues-and-events"></a><span data-ttu-id="1e884-103">使用訊息佇列和事件在 Azure 上的企業整合</span><span class="sxs-lookup"><span data-stu-id="1e884-103">Enterprise integration on Azure using message queues and events</span></span>

<span data-ttu-id="1e884-104">此參考架構整合企業後端系統，使用訊息佇列和事件來分離服務，以提升延展性和可靠性。</span><span class="sxs-lookup"><span data-stu-id="1e884-104">This reference architecture integrates enterprise backend systems, using message queues and events to decouple services for greater scalability and reliability.</span></span> <span data-ttu-id="1e884-105">後端系統可能包括軟體即服務 (SaaS) 系統、Azure 服務，以及企業中的現有 Web 服務。</span><span class="sxs-lookup"><span data-stu-id="1e884-105">The backend systems may include software as a service (SaaS) systems, Azure services, and existing web services in your enterprise.</span></span>

![使用佇列和事件的企業整合參考架構](./_images/enterprise-integration-queues-events.png)

## <a name="architecture"></a><span data-ttu-id="1e884-107">架構</span><span class="sxs-lookup"><span data-stu-id="1e884-107">Architecture</span></span>

<span data-ttu-id="1e884-108">此處所示架構建置在[基本企業整合][basic-enterprise-integration]中所示較簡單的架構之上。</span><span class="sxs-lookup"><span data-stu-id="1e884-108">The architecture shown here builds on a simpler architecture that is shown in [Basic enterprise integration][basic-enterprise-integration].</span></span> <span data-ttu-id="1e884-109">該架構使用 [Logic Apps][logic-apps] 來協調工作流程，使用 [API 管理][apim]來建立 API 目錄。</span><span class="sxs-lookup"><span data-stu-id="1e884-109">That architecture uses [Logic Apps][logic-apps] to orchestrate workflows and [API Management][apim] to create catalogs of APIs.</span></span>

<span data-ttu-id="1e884-110">這個版本的架構新增了讓系統更可靠且可擴充的兩個元件：</span><span class="sxs-lookup"><span data-stu-id="1e884-110">This version of the architecture adds two components that help make the system more reliable and scalable:</span></span>

- <span data-ttu-id="1e884-111">**[Azure 服務匯流排][service-bus]**。</span><span class="sxs-lookup"><span data-stu-id="1e884-111">**[Azure Service Bus][service-bus]**.</span></span> <span data-ttu-id="1e884-112">服務匯流排是安全、可靠的訊息代理程式。</span><span class="sxs-lookup"><span data-stu-id="1e884-112">Service Bus is a secure, reliable message broker.</span></span>

- <span data-ttu-id="1e884-113">**[Azure 事件格線][event-grid]**。</span><span class="sxs-lookup"><span data-stu-id="1e884-113">**[Azure Event Grid][event-grid]**.</span></span> <span data-ttu-id="1e884-114">事件格線是事件路由服務。</span><span class="sxs-lookup"><span data-stu-id="1e884-114">Event Grid is an event routing service.</span></span> <span data-ttu-id="1e884-115">它使用[發佈/訂閱](../../patterns/publisher-subscriber.md) (pub/sub) 事件模型。</span><span class="sxs-lookup"><span data-stu-id="1e884-115">It uses a [publish/subscribe](../../patterns/publisher-subscriber.md) (pub/sub) eventing model.</span></span>

<span data-ttu-id="1e884-116">相較於直接同步呼叫後端服務，使用訊息代理程式的非同步通訊可提供許多優點：</span><span class="sxs-lookup"><span data-stu-id="1e884-116">Asynchronous communication using a message broker provides a number of advantages over making direct, synchronous calls to backend services:</span></span>

- <span data-ttu-id="1e884-117">使用[佇列型負載調節模式](../../patterns/queue-based-load-leveling.md)，提供負載調節以處理工作負載的高載。</span><span class="sxs-lookup"><span data-stu-id="1e884-117">Provides load-leveling to handle bursts in workloads, using the [Queue-Based Load Leveling pattern](../../patterns/queue-based-load-leveling.md).</span></span>
- <span data-ttu-id="1e884-118">可靠追蹤長時間執行且牽涉到多個步驟或多個應用程式的工作流程進度。</span><span class="sxs-lookup"><span data-stu-id="1e884-118">Reliably tracks the progress of long-running workflows that involve multiple steps or multiple applications.</span></span>
- <span data-ttu-id="1e884-119">協助分離應用程式。</span><span class="sxs-lookup"><span data-stu-id="1e884-119">Helps to decouple applications.</span></span>
- <span data-ttu-id="1e884-120">與現有訊息型系統整合。</span><span class="sxs-lookup"><span data-stu-id="1e884-120">Integrates with existing message-based systems.</span></span>
- <span data-ttu-id="1e884-121">當後端系統無法使用時，可讓工作排入佇列。</span><span class="sxs-lookup"><span data-stu-id="1e884-121">Allows work to be queued when a backend system is not available.</span></span>

<span data-ttu-id="1e884-122">事件格線可讓系統中的各個元件回應發生的事件，而非依賴輪詢或已排程工作。</span><span class="sxs-lookup"><span data-stu-id="1e884-122">Event Grid enables the various components in the system to react to events as they happen, rather than relying on polling or scheduled tasks.</span></span> <span data-ttu-id="1e884-123">與訊息佇列相同，它可協助分離應用程式和服務。</span><span class="sxs-lookup"><span data-stu-id="1e884-123">As with a message queue, it helps decouple applications and services.</span></span> <span data-ttu-id="1e884-124">應用程式或服務可以發佈事件，而任何有興趣的訂閱者將會收到通知。</span><span class="sxs-lookup"><span data-stu-id="1e884-124">An application or service can publish events, and any interested subscribers will be notified.</span></span> <span data-ttu-id="1e884-125">可以新增新訂閱者，而不需要更新寄件者。</span><span class="sxs-lookup"><span data-stu-id="1e884-125">New subscribers can be added without updating the sender.</span></span>

<span data-ttu-id="1e884-126">許多 Azure 服務支援將事件傳送到事件格線。</span><span class="sxs-lookup"><span data-stu-id="1e884-126">Many Azure services support sending events to Event Grid.</span></span> <span data-ttu-id="1e884-127">比方說，當新檔案新增至 blob 存放區時，邏輯應用程式可以接聽事件。</span><span class="sxs-lookup"><span data-stu-id="1e884-127">For example, a logic app can listen for an event when new files are added to a blob store.</span></span> <span data-ttu-id="1e884-128">此模式可讓回應式工作流程啟動一系列程序，這些工作流程包括上傳檔案或將訊息放在佇列中。</span><span class="sxs-lookup"><span data-stu-id="1e884-128">This pattern enables reactive workflows, where uploading a file or putting a message on a queue kicks off a series of processes.</span></span> <span data-ttu-id="1e884-129">程序可能以平行方式或依特定順序執行。</span><span class="sxs-lookup"><span data-stu-id="1e884-129">The processes might be executed in parallel or in a specific sequence.</span></span>

## <a name="recommendations"></a><span data-ttu-id="1e884-130">建議</span><span class="sxs-lookup"><span data-stu-id="1e884-130">Recommendations</span></span>

<span data-ttu-id="1e884-131">[基本企業整合][basic-enterprise-integration]中所述的建議適用於此架構。</span><span class="sxs-lookup"><span data-stu-id="1e884-131">The recommendations described in [Basic enterprise integration][basic-enterprise-integration] apply to this architecture.</span></span> <span data-ttu-id="1e884-132">下列建議也適用：</span><span class="sxs-lookup"><span data-stu-id="1e884-132">The following recommendations also apply:</span></span>

### <a name="service-bus"></a><span data-ttu-id="1e884-133">服務匯流排</span><span class="sxs-lookup"><span data-stu-id="1e884-133">Service Bus</span></span>

<span data-ttu-id="1e884-134">服務匯流排具有兩種傳遞模式：*提取*或*推送*。</span><span class="sxs-lookup"><span data-stu-id="1e884-134">Service Bus has two delivery modes, *pull* or *push*.</span></span> <span data-ttu-id="1e884-135">在提取模型中，接收者持續輪詢新訊息。</span><span class="sxs-lookup"><span data-stu-id="1e884-135">In the pull model, the receiver continuously polls for new messages.</span></span> <span data-ttu-id="1e884-136">輪詢可能效率不佳，尤其如果您有許多佇列，每個接收一些訊息，或如果訊息之間有很多時間。</span><span class="sxs-lookup"><span data-stu-id="1e884-136">Polling can be inefficient, especially if you have many queues that each receive a few messages, or if there a lot of time between messages.</span></span> <span data-ttu-id="1e884-137">在推送模型中，當有新訊息時，服務匯流排會透過事件格線傳送事件。</span><span class="sxs-lookup"><span data-stu-id="1e884-137">In the push model, Service Bus sends an event through Event Grid when there are new messages.</span></span> <span data-ttu-id="1e884-138">接收者會訂閱事件。</span><span class="sxs-lookup"><span data-stu-id="1e884-138">The receiver subscribes to the event.</span></span> <span data-ttu-id="1e884-139">觸發事件時，接收者會從服務匯流排提取下一批次的訊息。</span><span class="sxs-lookup"><span data-stu-id="1e884-139">When the event is triggered, the receiver pulls the next batch of messages from Service Bus.</span></span>

<span data-ttu-id="1e884-140">建立邏輯應用程式以取用服務匯流排訊息時，建議您使用推送模型搭配事件格線整合。</span><span class="sxs-lookup"><span data-stu-id="1e884-140">When you create a logic app to consume Service Bus messages, we recommend using the push model with Event Grid integration.</span></span> <span data-ttu-id="1e884-141">這通常更符合成本效益，因為邏輯應用程式不需要輪詢服務匯流排。</span><span class="sxs-lookup"><span data-stu-id="1e884-141">It's often more cost efficient, because the logic app doesn't need to poll Service Bus.</span></span> <span data-ttu-id="1e884-142">如需詳細資訊，請參閱 [Azure 服務匯流排與事件格線的整合概觀](/azure/service-bus-messaging/service-bus-to-event-grid-integration-concept)。</span><span class="sxs-lookup"><span data-stu-id="1e884-142">For more information, see [Azure Service Bus to Event Grid integration overview](/azure/service-bus-messaging/service-bus-to-event-grid-integration-concept).</span></span> <span data-ttu-id="1e884-143">目前，事件格線通知需要有服務匯流排[進階層](https://azure.microsoft.com/pricing/details/service-bus/)。</span><span class="sxs-lookup"><span data-stu-id="1e884-143">Currently, Service Bus [Premium tier](https://azure.microsoft.com/pricing/details/service-bus/) is required for Event Grid notifications.</span></span>

<span data-ttu-id="1e884-144">使用 [PeekLock](/azure/service-bus-messaging/service-bus-messaging-overview#queues) 存取一組訊息。</span><span class="sxs-lookup"><span data-stu-id="1e884-144">Use [PeekLock](/azure/service-bus-messaging/service-bus-messaging-overview#queues) for accessing a group of messages.</span></span> <span data-ttu-id="1e884-145">當您使用 PeekLock 時，邏輯應用程式可以先執行步驟來驗證每個訊息，然後再完成或放棄訊息。</span><span class="sxs-lookup"><span data-stu-id="1e884-145">When you use PeekLock, the logic app can perform steps to validate each message before completing or abandoning the message.</span></span> <span data-ttu-id="1e884-146">此方法可防止意外的訊息遺失。</span><span class="sxs-lookup"><span data-stu-id="1e884-146">This approach protects against accidental message loss.</span></span>

### <a name="event-grid"></a><span data-ttu-id="1e884-147">Event Grid</span><span class="sxs-lookup"><span data-stu-id="1e884-147">Event Grid</span></span>

<span data-ttu-id="1e884-148">引發事件格線觸發程序時，表示*至少一個*事件發生。</span><span class="sxs-lookup"><span data-stu-id="1e884-148">When an Event Grid trigger fires, it means *at least one* event happened.</span></span> <span data-ttu-id="1e884-149">例如，當邏輯應用程式收到服務匯流排訊息的事件格線觸發程序，它應假設可能有數個訊息可處理。</span><span class="sxs-lookup"><span data-stu-id="1e884-149">For example, when a logic app gets an Event Grid triggers for a Service Bus message, it should assume that several messages might be available to process.</span></span>

<span data-ttu-id="1e884-150">事件方格會使用無伺服器模型。</span><span class="sxs-lookup"><span data-stu-id="1e884-150">Event Grid uses a serverless model.</span></span> <span data-ttu-id="1e884-151">計費的計算方式以作業 (事件執行) 的數目為基礎。</span><span class="sxs-lookup"><span data-stu-id="1e884-151">Billing is calculated based on the number of operations (event executions).</span></span> <span data-ttu-id="1e884-152">如需詳細資訊，請參閱[事件方格價格](https://azure.microsoft.com/pricing/details/event-grid/)。</span><span class="sxs-lookup"><span data-stu-id="1e884-152">For more information, see [Event Grid pricing](https://azure.microsoft.com/pricing/details/event-grid/).</span></span> <span data-ttu-id="1e884-153">目前沒有任何針對事件方格的階層考量。</span><span class="sxs-lookup"><span data-stu-id="1e884-153">Currently, there are no tier considerations for Event Grid.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="1e884-154">延展性考量</span><span class="sxs-lookup"><span data-stu-id="1e884-154">Scalability considerations</span></span>

<span data-ttu-id="1e884-155">若要達到更高的延展性，服務匯流排進階層可以將傳訊單位數目相應放大。</span><span class="sxs-lookup"><span data-stu-id="1e884-155">To achieve higher scalability, the Service Bus Premium tier can scale out the number of messaging units.</span></span> <span data-ttu-id="1e884-156">進階層設定可以有一個、兩個或四個傳訊單位。</span><span class="sxs-lookup"><span data-stu-id="1e884-156">Premium tier configurations can have one, two, or four messaging units.</span></span> <span data-ttu-id="1e884-157">如需調整服務匯流排規模的詳細資訊，請參閱[使用服務匯流排傳訊的效能改進最佳作法](/azure/service-bus-messaging/service-bus-performance-improvements)。</span><span class="sxs-lookup"><span data-stu-id="1e884-157">For more information about scaling Service Bus, see [Best practices for performance improvements by using Service Bus Messaging](/azure/service-bus-messaging/service-bus-performance-improvements).</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="1e884-158">可用性考量</span><span class="sxs-lookup"><span data-stu-id="1e884-158">Availability considerations</span></span>

<span data-ttu-id="1e884-159">檢閱每個服務的 SLA：</span><span class="sxs-lookup"><span data-stu-id="1e884-159">Review the SLA for each service:</span></span>

- <span data-ttu-id="1e884-160">[API 管理 SLA][apim-sla]</span><span class="sxs-lookup"><span data-stu-id="1e884-160">[API Management SLA][apim-sla]</span></span>
- <span data-ttu-id="1e884-161">[事件格線 SLA][event-grid-sla]</span><span class="sxs-lookup"><span data-stu-id="1e884-161">[Event Grid SLA][event-grid-sla]</span></span>
- <span data-ttu-id="1e884-162">[Logic Apps SLA][logic-apps-sla]</span><span class="sxs-lookup"><span data-stu-id="1e884-162">[Logic Apps SLA][logic-apps-sla]</span></span>
- <span data-ttu-id="1e884-163">[服務匯流排 SLA][sb-sla]</span><span class="sxs-lookup"><span data-stu-id="1e884-163">[Service Bus SLA][sb-sla]</span></span>

<span data-ttu-id="1e884-164">若要能夠在發生嚴重服務中斷時進行容錯移轉，請考慮在服務匯流排進階層中實作異地災害復原。</span><span class="sxs-lookup"><span data-stu-id="1e884-164">To enable failover if a serious outage occurs, consider implementing geo-disaster recovery in Service Bus Premium.</span></span> <span data-ttu-id="1e884-165">如需詳細資訊，請參閱 [Azure 服務匯流排異地災害復原](/azure/service-bus-messaging/service-bus-geo-dr)。</span><span class="sxs-lookup"><span data-stu-id="1e884-165">For more information, see [Azure Service Bus geo-disaster recovery](/azure/service-bus-messaging/service-bus-geo-dr).</span></span>

## <a name="security-considerations"></a><span data-ttu-id="1e884-166">安全性考量</span><span class="sxs-lookup"><span data-stu-id="1e884-166">Security considerations</span></span>

<span data-ttu-id="1e884-167">若要保護服務匯流排，請使用共用存取簽章 (SAS)。</span><span class="sxs-lookup"><span data-stu-id="1e884-167">To secure Service Bus, use shared access signature (SAS).</span></span> <span data-ttu-id="1e884-168">您可以使用 [SAS 驗證](/azure/service-bus-messaging/service-bus-sas)，授與使用者具特定權限的服務匯流排資源存取權。</span><span class="sxs-lookup"><span data-stu-id="1e884-168">You can grant a user access to Service Bus resources with specific rights by using [SAS authentication](/azure/service-bus-messaging/service-bus-sas).</span></span> <span data-ttu-id="1e884-169">如需詳細資訊，請參閱[服務匯流排驗證和授權](/azure/service-bus-messaging/service-bus-authentication-and-authorization)。</span><span class="sxs-lookup"><span data-stu-id="1e884-169">For more information, see [Service Bus authentication and authorization](/azure/service-bus-messaging/service-bus-authentication-and-authorization).</span></span>

<span data-ttu-id="1e884-170">如果您需要將服務匯流排佇列以 HTTP 端點的形式 (舉例而言) 公開，以發佈新訊息，請使用 API 管理讓該端點成為前端，以保護佇列。</span><span class="sxs-lookup"><span data-stu-id="1e884-170">If you need to expose a Service Bus queue as an HTTP endpoint, for example, to post new messages, use API Management to secure the queue by fronting the endpoint.</span></span> <span data-ttu-id="1e884-171">接著，您可以視情況使用憑證或 OAuth 驗證來保護該端點。</span><span class="sxs-lookup"><span data-stu-id="1e884-171">You can then secure the endpoint with certificates or OAuth authentication as appropriate.</span></span> <span data-ttu-id="1e884-172">要保護端點，最簡單的方法是使用邏輯應用程式與 HTTP 要求/回應觸發程序作為媒介。</span><span class="sxs-lookup"><span data-stu-id="1e884-172">The easiest way to secure an endpoint is using a logic app with an HTTP request/response trigger as an intermediary.</span></span>

<span data-ttu-id="1e884-173">事件方格服務會透過驗證碼來保護事件傳遞。</span><span class="sxs-lookup"><span data-stu-id="1e884-173">The Event Grid service secures event delivery through a validation code.</span></span> <span data-ttu-id="1e884-174">如果您使用 Logic Apps 來取用事件，將會自動執行驗證。</span><span class="sxs-lookup"><span data-stu-id="1e884-174">If you use Logic Apps to consume the event, validation is automatically performed.</span></span> <span data-ttu-id="1e884-175">如需詳細資訊，請參閱 [Event Grid 安全性和驗證](/azure/event-grid/security-authentication)。</span><span class="sxs-lookup"><span data-stu-id="1e884-175">For more information, see [Event Grid security and authentication](/azure/event-grid/security-authentication).</span></span>

[apim]: /azure/api-management
[apim-sla]: https://azure.microsoft.com/support/legal/sla/api-management/
[event-grid]: /azure/event-grid/
[event-grid-sla]: https://azure.microsoft.com/support/legal/sla/event-grid
[logic-apps]: /azure/logic-apps/logic-apps-overview
[logic-apps-sla]: https://azure.microsoft.com/support/legal/sla/logic-apps
[sb-sla]: https://azure.microsoft.com/support/legal/sla/service-bus/
[service-bus]: /azure/service-bus-messaging/
[basic-enterprise-integration]: ./basic-enterprise-integration.md

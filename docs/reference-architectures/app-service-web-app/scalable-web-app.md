---
title: 可調整規模的 Web 應用程式
titleSuffix: Azure Reference Architectures
description: 改善 Microsoft Azure 中所執行 Web 應用程式的延展性。
author: MikeWasson
ms.date: 10/25/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18
ms.openlocfilehash: b015a130a74f3160c0e737b436d41e1b1ea7b5bf
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241369"
---
# <a name="improve-scalability-in-an-azure-web-application"></a><span data-ttu-id="ebb8d-103">改善 Azure Web 應用程式的延展性</span><span class="sxs-lookup"><span data-stu-id="ebb8d-103">Improve scalability in an Azure web application</span></span>

<span data-ttu-id="ebb8d-104">此參考架構顯示經過證實的改善 Azure App Service Web 應用程式延展性和效能的作法。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-104">This reference architecture shows proven practices for improving scalability and performance in an Azure App Service web application.</span></span>

![Azure 中 Web 應用程式的延展性改善](./images/scalable-web-app.png)

<span data-ttu-id="ebb8d-106">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-106">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="ebb8d-107">架構</span><span class="sxs-lookup"><span data-stu-id="ebb8d-107">Architecture</span></span>

<span data-ttu-id="ebb8d-108">此架構是根據[基本 Web 應用程式][basic-web-app]中的架構建置的。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-108">This architecture builds on the one shown in [Basic web application][basic-web-app].</span></span> <span data-ttu-id="ebb8d-109">包括下列元件：</span><span class="sxs-lookup"><span data-stu-id="ebb8d-109">It includes the following components:</span></span>

- <span data-ttu-id="ebb8d-110">**資源群組**。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-110">**Resource group**.</span></span> <span data-ttu-id="ebb8d-111">[資源群組][resource-group]是 Azure 資源的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-111">A [resource group][resource-group] is a logical container for Azure resources.</span></span>
- <span data-ttu-id="ebb8d-112">**[Web 應用程式][app-service-web-app]**.</span><span class="sxs-lookup"><span data-stu-id="ebb8d-112">**[Web app][app-service-web-app]**.</span></span> <span data-ttu-id="ebb8d-113">典型的現代應用程式可能包含一個網站以及一個或多個符合 REST 的 Web API。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-113">A typical modern application might include both a website and one or more RESTful web APIs.</span></span> <span data-ttu-id="ebb8d-114">瀏覽器用戶端透過 AJAX、原生用戶端應用程式或伺服器端應用程式耗用 Web API。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-114">A web API might be consumed by browser clients through AJAX, by native client applications, or by server-side applications.</span></span> <span data-ttu-id="ebb8d-115">如需 Web API 的設計考量，請參閱 [API 指導方針][api-guidance]。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-115">For considerations on designing web APIs, see [API design guidance][api-guidance].</span></span>
- <span data-ttu-id="ebb8d-116">**函式應用程式**。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-116">**Function App**.</span></span> <span data-ttu-id="ebb8d-117">使用[函式應用程式][functions]執行背景工作。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-117">Use [Function Apps][functions] to run background tasks.</span></span> <span data-ttu-id="ebb8d-118">函式由觸發程序叫用，例如計時器事件或位於佇列上的訊息。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-118">Functions are invoked by a trigger, such as a timer event or a message being placed on queue.</span></span> <span data-ttu-id="ebb8d-119">對於長時間執行具狀態的工作，使用 [Durable Functions][durable-functions]。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-119">For long-running stateful tasks, use [Durable Functions][durable-functions].</span></span>
- <span data-ttu-id="ebb8d-120">**佇列**。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-120">**Queue**.</span></span> <span data-ttu-id="ebb8d-121">在此處的架構中，應用程式將訊息放到 [Azure 佇列儲存體][queue-storage]佇列上，藉此將背景工作排入佇列。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-121">In the architecture shown here, the application queues background tasks by putting a message onto an [Azure Queue storage][queue-storage] queue.</span></span> <span data-ttu-id="ebb8d-122">訊息會觸發函式應用程式。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-122">The message triggers a function app.</span></span> <span data-ttu-id="ebb8d-123">或者，也可以使用服務匯流排佇列。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-123">Alternatively, you can use Service Bus queues.</span></span> <span data-ttu-id="ebb8d-124">如需兩者的比較，請參閱 [Azure 佇列和服務匯流排佇列 - 異同比較][queues-compared]。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-124">For a comparison, see [Azure Queues and Service Bus queues - compared and contrasted][queues-compared].</span></span>
- <span data-ttu-id="ebb8d-125">**快取**。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-125">**Cache**.</span></span> <span data-ttu-id="ebb8d-126">在 [Azure Redis 快取][azure-redis]中儲存半靜態資料。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-126">Store semi-static data in [Azure Redis Cache][azure-redis].</span></span>
- <span data-ttu-id="ebb8d-127">**CDN**。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-127">**CDN**.</span></span> <span data-ttu-id="ebb8d-128">使用 [Azure 內容傳遞網路][azure-cdn] (CDN) 快取公開可用的內容，以降低延遲而且更快速傳遞內容。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-128">Use [Azure Content Delivery Network][azure-cdn] (CDN) to cache publicly available content for lower latency and faster delivery of content.</span></span>
- <span data-ttu-id="ebb8d-129">**資料儲存體**。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-129">**Data storage**.</span></span> <span data-ttu-id="ebb8d-130">使用 [SQL Database][sql-db] 儲存關聯式資料。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-130">Use [Azure SQL Database][sql-db] for relational data.</span></span> <span data-ttu-id="ebb8d-131">針對非關聯式資料，請考慮 [Cosmos DB][cosmosdb]。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-131">For non-relational data, consider [Cosmos DB][cosmosdb].</span></span>
- <span data-ttu-id="ebb8d-132">**Azure 搜尋服務**。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-132">**Azure Search**.</span></span> <span data-ttu-id="ebb8d-133">使用 [Azure 搜尋][azure-search] 新增搜尋功能，例如搜尋建議、模糊搜尋、特定語言搜尋。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-133">Use [Azure Search][azure-search] to add search functionality such as search suggestions, fuzzy search, and language-specific search.</span></span> <span data-ttu-id="ebb8d-134">Azure 搜尋服務通會搭配其他資料存放區，特別是當主要資料存放區需要嚴格的一致性。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-134">Azure Search is typically used in conjunction with another data store, especially if the primary data store requires strict consistency.</span></span> <span data-ttu-id="ebb8d-135">這種方式會將授權的資料儲存在 Azure 搜尋服務中的另一個資料存放區和搜尋索引。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-135">In this approach, store authoritative data in the other data store and the search index in Azure Search.</span></span> <span data-ttu-id="ebb8d-136">Azure 搜尋服務也可用於合併多個資料存放區中的單一搜尋索引。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-136">Azure Search can also be used to consolidate a single search index from multiple data stores.</span></span>
- <span data-ttu-id="ebb8d-137">**Azure DNS**。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-137">**Azure DNS**.</span></span> <span data-ttu-id="ebb8d-138">[Azure DNS][azure-dns] 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構提供名稱解析。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-138">[Azure DNS][azure-dns] is a hosting service for DNS domains, providing name resolution using Microsoft Azure infrastructure.</span></span> <span data-ttu-id="ebb8d-139">只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-139">By hosting your domains in Azure, you can manage your DNS records using the same credentials, APIs, tools, and billing as your other Azure services.</span></span>
- <span data-ttu-id="ebb8d-140">**應用程式閘道**。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-140">**Application gateway**.</span></span> <span data-ttu-id="ebb8d-141">[應用程式閘道](/azure/application-gateway/)是第 7 層負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-141">[Application Gateway](/azure/application-gateway/) is a layer 7 load balancer.</span></span> <span data-ttu-id="ebb8d-142">在此架構中，它會將 HTTP 要求路由傳送至 Web 前端。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-142">In this architecture, it routes HTTP requests to the web front end.</span></span> <span data-ttu-id="ebb8d-143">應用程式閘道也會提供 [Web 應用程式防火牆](/azure/application-gateway/waf-overview) (WAF)，該防火牆會保護應用程式免於常見惡意探索和弱點。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-143">Application Gateway also provides a [web application firewall](/azure/application-gateway/waf-overview) (WAF) that protects the application from common exploits and vulnerabilities.</span></span>

## <a name="recommendations"></a><span data-ttu-id="ebb8d-144">建議</span><span class="sxs-lookup"><span data-stu-id="ebb8d-144">Recommendations</span></span>

<span data-ttu-id="ebb8d-145">您的需求可能和此處所述的架構不同。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-145">Your requirements might differ from the architecture described here.</span></span> <span data-ttu-id="ebb8d-146">以本節的建議作為起點。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-146">Use the recommendations in this section as a starting point.</span></span>

### <a name="app-service-apps"></a><span data-ttu-id="ebb8d-147">App Service 應用程式</span><span class="sxs-lookup"><span data-stu-id="ebb8d-147">App Service apps</span></span>

<span data-ttu-id="ebb8d-148">建議您將 Web 應用程式和 Web API 建立為不同的 App Service 應用程式。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-148">We recommend creating the web application and the web API as separate App Service apps.</span></span> <span data-ttu-id="ebb8d-149">此設計可讓您在個別的 App Service 方案中執行它們，使它們可以獨立調整規模。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-149">This design lets you run them in separate App Service plans so they can be scaled independently.</span></span> <span data-ttu-id="ebb8d-150">如果您一開始不需要這種程度的延展性，可以將應用程式部署到相同的方案中，之後有需要時再將它們移到別的方案。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-150">If you don't need that level of scalability initially, you can deploy the apps into the same plan and move them into separate plans later if necessary.</span></span>

> [!NOTE]
> <span data-ttu-id="ebb8d-151">在基本、標準和進階方案中，是依照方案中的 VM 執行個體計費，而不是依照應用程式。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-151">For the Basic, Standard, and Premium plans, you are billed for the VM instances in the plan, not per app.</span></span> <span data-ttu-id="ebb8d-152">請參閱 [App Service 價格][app-service-pricing]。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-152">See [App Service Pricing][app-service-pricing]</span></span>
>

### <a name="cache"></a><span data-ttu-id="ebb8d-153">快取</span><span class="sxs-lookup"><span data-stu-id="ebb8d-153">Cache</span></span>

<span data-ttu-id="ebb8d-154">您可以使用 [Azure Redis 快取][azure-redis]來快取某些資料，以改善效能和延展性。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-154">You can improve performance and scalability by using [Azure Redis Cache][azure-redis] to cache some data.</span></span> <span data-ttu-id="ebb8d-155">請考慮將 Redis 快取用於：</span><span class="sxs-lookup"><span data-stu-id="ebb8d-155">Consider using Redis Cache for:</span></span>

- <span data-ttu-id="ebb8d-156">半靜態交易資料。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-156">Semi-static transaction data.</span></span>
- <span data-ttu-id="ebb8d-157">工作階段狀態。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-157">Session state.</span></span>
- <span data-ttu-id="ebb8d-158">HTML 輸出。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-158">HTML output.</span></span> <span data-ttu-id="ebb8d-159">這對於要呈現複雜 HTML 輸出的應用程式很實用。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-159">This can be useful in applications that render complex HTML output.</span></span>

<span data-ttu-id="ebb8d-160">如需設計快取策略的詳細指引，請參閱[快取指引][caching-guidance]。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-160">For more detailed guidance on designing a caching strategy, see [Caching guidance][caching-guidance].</span></span>

### <a name="cdn"></a><span data-ttu-id="ebb8d-161">CDN</span><span class="sxs-lookup"><span data-stu-id="ebb8d-161">CDN</span></span>

<span data-ttu-id="ebb8d-162">使用 [Azure CDN][azure-cdn] 快取靜態內容。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-162">Use [Azure CDN][azure-cdn] to cache static content.</span></span> <span data-ttu-id="ebb8d-163">CDN 的主要優點是可以為使用者降低延遲，因為是在地理位置接近使用者的邊緣伺服器快取內容。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-163">The main benefit of a CDN is to reduce latency for users, because content is cached at an edge server that is geographically close to the user.</span></span> <span data-ttu-id="ebb8d-164">CDN 也可以減少應用程式的負載，因為流量不是由應用程式處理。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-164">CDN can also reduce load on the application, because that traffic is not being handled by the application.</span></span>

<span data-ttu-id="ebb8d-165">如果您的應用程式大部分是靜態網頁，請考慮使用 [CDN 快取整個應用程式][cdn-app-service]。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-165">If your app consists mostly of static pages, consider using [CDN to cache the entire app][cdn-app-service].</span></span> <span data-ttu-id="ebb8d-166">否則，將靜態內容 (例如影像、CSS、HTML 檔案) 放在 [Azure 儲存體，並使用 CDN 快取這些檔案][cdn-storage-account]。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-166">Otherwise, put static content such as images, CSS, and HTML files, into [Azure Storage and use CDN to cache those files][cdn-storage-account].</span></span>

> [!NOTE]
> <span data-ttu-id="ebb8d-167">Azure CDN 無法服務需要驗證的內容。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-167">Azure CDN cannot serve content that requires authentication.</span></span>
>

<span data-ttu-id="ebb8d-168">如需詳細指引，請參閱[內容傳遞網路 (CDN) 指引][cdn-guidance]。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-168">For more detailed guidance, see [Content Delivery Network (CDN) guidance][cdn-guidance].</span></span>

### <a name="storage"></a><span data-ttu-id="ebb8d-169">儲存體</span><span class="sxs-lookup"><span data-stu-id="ebb8d-169">Storage</span></span>

<span data-ttu-id="ebb8d-170">現代應用程式通常要處理大量的資料。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-170">Modern applications often process large amounts of data.</span></span> <span data-ttu-id="ebb8d-171">為了能針對雲端調整規模，請務必選擇正確的儲存體類型。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-171">In order to scale for the cloud, it's important to choose the right storage type.</span></span> <span data-ttu-id="ebb8d-172">以下是一些基準建議。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-172">Here are some baseline recommendations.</span></span>

| <span data-ttu-id="ebb8d-173">您想要儲存的內容</span><span class="sxs-lookup"><span data-stu-id="ebb8d-173">What you want to store</span></span> | <span data-ttu-id="ebb8d-174">範例</span><span class="sxs-lookup"><span data-stu-id="ebb8d-174">Example</span></span> | <span data-ttu-id="ebb8d-175">建議的儲存體</span><span class="sxs-lookup"><span data-stu-id="ebb8d-175">Recommended storage</span></span> |
| --- | --- | --- |
| <span data-ttu-id="ebb8d-176">檔案</span><span class="sxs-lookup"><span data-stu-id="ebb8d-176">Files</span></span> |<span data-ttu-id="ebb8d-177">影像、文件、PDF</span><span class="sxs-lookup"><span data-stu-id="ebb8d-177">Images, documents, PDFs</span></span> |<span data-ttu-id="ebb8d-178">Azure Blob 儲存體</span><span class="sxs-lookup"><span data-stu-id="ebb8d-178">Azure Blob Storage</span></span> |
| <span data-ttu-id="ebb8d-179">索引鍵/值組</span><span class="sxs-lookup"><span data-stu-id="ebb8d-179">Key/Value pairs</span></span> |<span data-ttu-id="ebb8d-180">依使用者識別碼查閱的使用者設定檔資料</span><span class="sxs-lookup"><span data-stu-id="ebb8d-180">User profile data looked up by user ID</span></span> |<span data-ttu-id="ebb8d-181">Azure 資料表儲存體</span><span class="sxs-lookup"><span data-stu-id="ebb8d-181">Azure Table storage</span></span> |
| <span data-ttu-id="ebb8d-182">用來觸發進一步處理的簡訊</span><span class="sxs-lookup"><span data-stu-id="ebb8d-182">Short messages intended to trigger further processing</span></span> |<span data-ttu-id="ebb8d-183">訂單要求</span><span class="sxs-lookup"><span data-stu-id="ebb8d-183">Order requests</span></span> |<span data-ttu-id="ebb8d-184">Azure 佇列儲存體、服務匯流排佇列或服務匯流排主題</span><span class="sxs-lookup"><span data-stu-id="ebb8d-184">Azure Queue storage, Service Bus queue, or Service Bus topic</span></span> |
| <span data-ttu-id="ebb8d-185">具有彈性結構描述且需要基本查詢的非關聯式資料</span><span class="sxs-lookup"><span data-stu-id="ebb8d-185">Non-relational data with a flexible schema requiring basic querying</span></span> |<span data-ttu-id="ebb8d-186">產品目錄</span><span class="sxs-lookup"><span data-stu-id="ebb8d-186">Product catalog</span></span> |<span data-ttu-id="ebb8d-187">文件資料庫，例如 Azure Cosmos DB、MongoDB 或 Apache CouchDB</span><span class="sxs-lookup"><span data-stu-id="ebb8d-187">Document database, such as Azure Cosmos DB, MongoDB, or Apache CouchDB</span></span> |
| <span data-ttu-id="ebb8d-188">需要更豐富查詢支援、嚴格結構描述，及/或強式一致性的關聯式資料</span><span class="sxs-lookup"><span data-stu-id="ebb8d-188">Relational data requiring richer query support, strict schema, and/or strong consistency</span></span> |<span data-ttu-id="ebb8d-189">產品庫存</span><span class="sxs-lookup"><span data-stu-id="ebb8d-189">Product inventory</span></span> |<span data-ttu-id="ebb8d-190">連接字串</span><span class="sxs-lookup"><span data-stu-id="ebb8d-190">Azure SQL Database</span></span> |

<span data-ttu-id="ebb8d-191">請參閱[選擇正確的資料存放區][datastore]。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-191">See [Choose the right data store][datastore].</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="ebb8d-192">延展性考量</span><span class="sxs-lookup"><span data-stu-id="ebb8d-192">Scalability considerations</span></span>

<span data-ttu-id="ebb8d-193">Azure App Service 的主要優點是能夠根據負載調整應用程式規模。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-193">A major benefit of Azure App Service is the ability to scale your application based on load.</span></span> <span data-ttu-id="ebb8d-194">以下是規劃調整應用程式時，應記住的一些考量。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-194">Here are some considerations to keep in mind when planning to scale your application.</span></span>

### <a name="app-service-app"></a><span data-ttu-id="ebb8d-195">App Service 應用程式</span><span class="sxs-lookup"><span data-stu-id="ebb8d-195">App Service app</span></span>

<span data-ttu-id="ebb8d-196">如果您的解決方案包含數個 App Service 應用程式，考慮將它們部署在不同的 App Service 方案中。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-196">If your solution includes several App Service apps, consider deploying them to separate App Service plans.</span></span> <span data-ttu-id="ebb8d-197">這種做法可讓您分別調整它們，因為它們在不同的執行個體上執行。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-197">This approach enables you to scale them independently because they run on separate instances.</span></span>

<span data-ttu-id="ebb8d-198">同樣地，請考慮將函式應用程式放入它自己的方案中，使背景工作不在處理 HTTP 要求的相同執行個體上執行。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-198">Similarly, consider putting a function app into its own plan so that background tasks don't run on the same instances that handle HTTP requests.</span></span> <span data-ttu-id="ebb8d-199">如果背景工作會間歇性地執行，請考慮使用[使用情況方案][functions-consumption-plan]，該方案是依據執行次數來計費，而不是每小時計費。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-199">If background tasks run intermittently, consider using a [consumption plan][functions-consumption-plan], which is billed based on the number of executions, rather than hourly.</span></span>

### <a name="sql-database"></a><span data-ttu-id="ebb8d-200">SQL Database</span><span class="sxs-lookup"><span data-stu-id="ebb8d-200">SQL Database</span></span>

<span data-ttu-id="ebb8d-201">藉由將資料庫「分區」，提高 SQL 資料庫的延展性。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-201">Increase scalability of a SQL database by *sharding* the database.</span></span> <span data-ttu-id="ebb8d-202">分區是指以水平方式分割資料庫。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-202">Sharding refers to partitioning the database horizontally.</span></span> <span data-ttu-id="ebb8d-203">分區可讓您使用[彈性資料庫工具][sql-elastic]以水平方式相應放大資料庫。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-203">Sharding allows you to scale out the database horizontally using [Elastic Database tools][sql-elastic].</span></span> <span data-ttu-id="ebb8d-204">分區的潛在優點包括：</span><span class="sxs-lookup"><span data-stu-id="ebb8d-204">Potential benefits of sharding include:</span></span>

- <span data-ttu-id="ebb8d-205">較佳的交易輸送量。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-205">Better transaction throughput.</span></span>
- <span data-ttu-id="ebb8d-206">對資料子集合執行的查詢可以更快速。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-206">Queries can run faster over a subset of the data.</span></span>

### <a name="azure-search"></a><span data-ttu-id="ebb8d-207">Azure 搜尋服務</span><span class="sxs-lookup"><span data-stu-id="ebb8d-207">Azure Search</span></span>

<span data-ttu-id="ebb8d-208">Azure 搜尋服務替主要資料存放區省去了執行複雜資料搜尋的額外負荷，而且可加以調整以處理負載。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-208">Azure Search removes the overhead of performing complex data searches from the primary data store, and it can scale to handle load.</span></span> <span data-ttu-id="ebb8d-209">請參閱[在 Azure 搜尋服務中調整適用於查詢和編製索引工作負載的資源等級][azure-search-scaling]。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-209">See [Scale resource levels for query and indexing workloads in Azure Search][azure-search-scaling].</span></span>

## <a name="security-considerations"></a><span data-ttu-id="ebb8d-210">安全性考量</span><span class="sxs-lookup"><span data-stu-id="ebb8d-210">Security considerations</span></span>

<span data-ttu-id="ebb8d-211">本節列出 Azure 服務專屬的安全性考量，</span><span class="sxs-lookup"><span data-stu-id="ebb8d-211">This section lists security considerations that are specific to the Azure services described in this article.</span></span> <span data-ttu-id="ebb8d-212">但不是安全性最佳做法的完整清單。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-212">It's not a complete list of security best practices.</span></span> <span data-ttu-id="ebb8d-213">如需更多的安全性考量，請參閱[保護 Azure App Service 中的應用程式][app-service-security]。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-213">For some additional security considerations, see [Secure an app in Azure App Service][app-service-security].</span></span>

### <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="ebb8d-214">跨原始來源資源分享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="ebb8d-214">Cross-Origin Resource Sharing (CORS)</span></span>

<span data-ttu-id="ebb8d-215">如果您將網站和 Web API 建立為不同的應用程式，則網站無法對 API 進行用戶端 AJAX 呼叫，除非您啟用 CORS。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-215">If you create a website and web API as separate apps, the website cannot make client-side AJAX calls to the API unless you enable CORS.</span></span>

> [!NOTE]
> <span data-ttu-id="ebb8d-216">瀏覽器安全性可防止網頁對另一個網域提出 AJAX 要求。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-216">Browser security prevents a web page from making AJAX requests to another domain.</span></span> <span data-ttu-id="ebb8d-217">這項限制稱為同源原則，可防止惡意網站從另一個網站讀取敏感性資料。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-217">This restriction is called the same-origin policy, and prevents a malicious site from reading sentitive data from another site.</span></span> <span data-ttu-id="ebb8d-218">跨原始來源資源共用 (CORS) 是 W3C 標準，可讓伺服器放寬同源原則，允許一些跨來源要求，並拒絕其他要求。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-218">CORS is a W3C standard that allows a server to relax the same-origin policy and allow some cross-origin requests while rejecting others.</span></span>
>

<span data-ttu-id="ebb8d-219">App Service 已內建 CORS 支援，不需要再撰寫任何應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-219">App Services has built-in support for CORS, without needing to write any application code.</span></span> <span data-ttu-id="ebb8d-220">請參閱[使用 CORS 從 JavaScript 取用 API 應用程式][cors]。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-220">See [Consume an API app from JavaScript using CORS][cors].</span></span> <span data-ttu-id="ebb8d-221">將網站新增至 API 允許的來源清單。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-221">Add the website to the list of allowed origins for the API.</span></span>

### <a name="sql-database-encryption"></a><span data-ttu-id="ebb8d-222">SQL Database 加密</span><span class="sxs-lookup"><span data-stu-id="ebb8d-222">SQL Database encryption</span></span>

<span data-ttu-id="ebb8d-223">如果您要加密資料庫中的靜止資料，使用[透明資料加密][sql-encryption]。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-223">Use [Transparent Data Encryption][sql-encryption] if you need to encrypt data at rest in the database.</span></span> <span data-ttu-id="ebb8d-224">這個功能可執行整個資料庫 (包括備份和交易記錄) 的即時加密和解密，不需變更應用程式。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-224">This feature performs real-time encryption and decryption of an entire database (including backups and transaction log files) and requires no changes to the application.</span></span> <span data-ttu-id="ebb8d-225">加密會增加一些延遲，所以最好的作法是將必須保護的資料另外放在它自己的資料庫中，只啟用該資料庫的加密。</span><span class="sxs-lookup"><span data-stu-id="ebb8d-225">Encryption does add some latency, so it's a good practice to separate the data that must be secure into its own database and enable encryption only for that database.</span></span>

<!-- links -->

[api-guidance]: ../../best-practices/api-design.md
[app-service-security]: /azure/app-service-web/web-sites-security
[app-service-web-app]: /azure/app-service-web/app-service-web-overview
[app-service-api-app]: /azure/app-service-api/app-service-api-apps-why-best-platform
[app-service-pricing]: https://azure.microsoft.com/pricing/details/app-service/
[azure-cdn]: https://azure.microsoft.com/services/cdn/
[azure-dns]: /azure/dns/dns-overview
[azure-redis]: https://azure.microsoft.com/services/cache/
[azure-search]: /azure/search
[azure-search-scaling]: /azure/search/search-capacity-planning
[basic-web-app]: basic-web-app.md
[basic-web-app-scalability]: basic-web-app.md#scalability-considerations
[caching-guidance]: ../../best-practices/caching.md
[cdn-app-service]: /azure/app-service-web/cdn-websites-with-cdn
[cdn-storage-account]: /azure/cdn/cdn-create-a-storage-account-with-cdn
[cdn-guidance]: ../../best-practices/cdn.md
[cors]: /azure/app-service-api/app-service-api-cors-consume-javascript
[cosmosdb]: /azure/cosmos-db/
[datastore]: ../..//guide/technology-choices/data-store-overview.md
[durable-functions]: /azure/azure-functions/durable-functions-overview
[functions]: /azure/azure-functions/functions-overview
[functions-consumption-plan]: /azure/azure-functions/functions-scale#consumption-plan
[queue-storage]: /azure/storage/storage-dotnet-how-to-use-queues
[queues-compared]: /azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted
[resource-group]: /azure/azure-resource-manager/resource-group-overview#resource-groups
[sql-db]: /azure/sql-database/
[sql-elastic]: /azure/sql-database/sql-database-elastic-scale-introduction
[sql-encryption]: https://msdn.microsoft.com/library/dn948096.aspx
[tm]: https://azure.microsoft.com/services/traffic-manager/
[visio-download]: https://archcenter.blob.core.windows.net/cdn/app-service-reference-architectures.vsdx
[web-app-multi-region]: ./multi-region.md

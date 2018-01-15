---
title: "可調整規模的 Web 應用程式"
description: "改善 Microsoft Azure 中執行之 Web 應用程式的延展性。"
author: MikeWasson
pnp.series.title: Azure App Service
pnp.series.prev: basic-web-app
pnp.series.next: multi-region-web-app
ms.date: 11/23/2016
cardTitle: Improve scalability
ms.openlocfilehash: 1fdaf6e3695cb814fa4c275a4a273f9fa9a7b71b
ms.sourcegitcommit: c9e6d8edb069b8c513de748ce8114c879bad5f49
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2018
---
# <a name="improve-scalability-in-a-web-application"></a><span data-ttu-id="aa080-103">改善 Web 應用程式的延展性</span><span class="sxs-lookup"><span data-stu-id="aa080-103">Improve scalability in a web application</span></span>

<span data-ttu-id="aa080-104">此參考架構顯示經過證實的改善 Azure App Service Web 應用程式延展性和效能的作法。</span><span class="sxs-lookup"><span data-stu-id="aa080-104">This reference architecture shows proven practices for improving scalability and performance in an Azure App Service web application.</span></span>

<span data-ttu-id="aa080-105">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="aa080-105">![[0]][0]</span></span>

<span data-ttu-id="aa080-106">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="aa080-106">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="aa080-107">架構</span><span class="sxs-lookup"><span data-stu-id="aa080-107">Architecture</span></span>  

<span data-ttu-id="aa080-108">此架構是根據[基本 Web 應用程式][basic-web-app]中的架構建置的。</span><span class="sxs-lookup"><span data-stu-id="aa080-108">This architecture builds on the one shown in [Basic web application][basic-web-app].</span></span> <span data-ttu-id="aa080-109">包括下列元件元：</span><span class="sxs-lookup"><span data-stu-id="aa080-109">It includes the following components:</span></span>

* <span data-ttu-id="aa080-110">**資源群組**。</span><span class="sxs-lookup"><span data-stu-id="aa080-110">**Resource group**.</span></span> <span data-ttu-id="aa080-111">[資源群組][resource-group]是 Azure 資源的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="aa080-111">A [resource group][resource-group] is a logical container for Azure resources.</span></span>
* <span data-ttu-id="aa080-112">**[Web 應用程式][app-service-web-app]**和 **[API 應用程式][app-service-api-app]**。</span><span class="sxs-lookup"><span data-stu-id="aa080-112">**[Web app][app-service-web-app]** and **[API app][app-service-api-app]**.</span></span> <span data-ttu-id="aa080-113">典型的現代應用程式可能包含一個網站以及一個或多個符合 REST 的 Web API。</span><span class="sxs-lookup"><span data-stu-id="aa080-113">A typical modern application might include both a website and one or more RESTful web APIs.</span></span> <span data-ttu-id="aa080-114">瀏覽器用戶端透過 AJAX、原生用戶端應用程式或伺服器端應用程式耗用 Web API。</span><span class="sxs-lookup"><span data-stu-id="aa080-114">A web API might be consumed by browser clients through AJAX, by native client applications, or by server-side applications.</span></span> <span data-ttu-id="aa080-115">如需 Web API 的設計考量，請參閱 [API 指導方針][api-guidance]。</span><span class="sxs-lookup"><span data-stu-id="aa080-115">For considerations on designing web APIs, see [API design guidance][api-guidance].</span></span>    
* <span data-ttu-id="aa080-116">**WebJob**。</span><span class="sxs-lookup"><span data-stu-id="aa080-116">**WebJob**.</span></span> <span data-ttu-id="aa080-117">使用 [Azure WebJobs][webjobs] 在背景中執行長時間工作。</span><span class="sxs-lookup"><span data-stu-id="aa080-117">Use [Azure WebJobs][webjobs] to run long-running tasks in the background.</span></span> <span data-ttu-id="aa080-118">WebJob 可以依照排程持續執行，或為了回應觸發程序 (例如將訊息放在佇列上) 而執行。</span><span class="sxs-lookup"><span data-stu-id="aa080-118">WebJobs can run on a schedule, continously, or in response to a trigger, such as putting a message on a queue.</span></span> <span data-ttu-id="aa080-119">在 App Service 應用程式的環境中 WebJob 是以背景處理序執行。</span><span class="sxs-lookup"><span data-stu-id="aa080-119">A WebJob runs as a background process in the context of an App Service app.</span></span>
* <span data-ttu-id="aa080-120">**佇列**。</span><span class="sxs-lookup"><span data-stu-id="aa080-120">**Queue**.</span></span> <span data-ttu-id="aa080-121">在此處的架構中，應用程式將訊息放到 [Azure 佇列儲存體][queue-storage]佇列上，藉此將背景工作排入佇列。</span><span class="sxs-lookup"><span data-stu-id="aa080-121">In the architecture shown here, the application queues background tasks by putting a message onto an [Azure Queue storage][queue-storage] queue.</span></span> <span data-ttu-id="aa080-122">訊息會觸發 WebJob 中的函式。</span><span class="sxs-lookup"><span data-stu-id="aa080-122">The message triggers a function in the WebJob.</span></span> <span data-ttu-id="aa080-123">或者，也可以使用服務匯流排佇列。</span><span class="sxs-lookup"><span data-stu-id="aa080-123">Alternatively, you can use Service Bus queues.</span></span> <span data-ttu-id="aa080-124">如需兩者的比較，請參閱 [Azure 佇列和服務匯流排佇列 - 異同比較][queues-compared]。</span><span class="sxs-lookup"><span data-stu-id="aa080-124">For a comparison, see [Azure Queues and Service Bus queues - compared and contrasted][queues-compared].</span></span>
* <span data-ttu-id="aa080-125">**快取**。</span><span class="sxs-lookup"><span data-stu-id="aa080-125">**Cache**.</span></span> <span data-ttu-id="aa080-126">在 [Azure Redis 快取][azure-redis]中儲存半靜態資料。</span><span class="sxs-lookup"><span data-stu-id="aa080-126">Store semi-static data in [Azure Redis Cache][azure-redis].</span></span>  
* <span data-ttu-id="aa080-127">**CDN**。</span><span class="sxs-lookup"><span data-stu-id="aa080-127">**CDN**.</span></span> <span data-ttu-id="aa080-128">使用 [Azure 內容傳遞網路][azure-cdn] (CDN) 快取公開可用的內容，以降低延遲而且更快速傳遞內容。</span><span class="sxs-lookup"><span data-stu-id="aa080-128">Use [Azure Content Delivery Network][azure-cdn] (CDN) to cache publicly available content for lower latency and faster delivery of content.</span></span>
* <span data-ttu-id="aa080-129">**資料儲存體**。</span><span class="sxs-lookup"><span data-stu-id="aa080-129">**Data storage**.</span></span> <span data-ttu-id="aa080-130">使用 [SQL Database][sql-db] 儲存關聯式資料。</span><span class="sxs-lookup"><span data-stu-id="aa080-130">Use [Azure SQL Database][sql-db] for relational data.</span></span> <span data-ttu-id="aa080-131">至於非關聯式資料，請考慮 NoSQL 存放區，例如 [Cosmos DB][documentdb]。</span><span class="sxs-lookup"><span data-stu-id="aa080-131">For non-relational data, consider a NoSQL store, such as [Cosmos DB][documentdb].</span></span>
* <span data-ttu-id="aa080-132">**Azure 搜尋服務**。</span><span class="sxs-lookup"><span data-stu-id="aa080-132">**Azure Search**.</span></span> <span data-ttu-id="aa080-133">使用 [Azure 搜尋][azure-search] 新增搜尋功能，例如搜尋建議、模糊搜尋、特定語言搜尋。</span><span class="sxs-lookup"><span data-stu-id="aa080-133">Use [Azure Search][azure-search] to add search functionality such as search suggestions, fuzzy search, and language-specific search.</span></span> <span data-ttu-id="aa080-134">Azure 搜尋服務通會搭配其他資料存放區，特別是當主要資料存放區需要嚴格的一致性。</span><span class="sxs-lookup"><span data-stu-id="aa080-134">Azure Search is typically used in conjunction with another data store, especially if the primary data store requires strict consistency.</span></span> <span data-ttu-id="aa080-135">這種方式會將授權的資料儲存在 Azure 搜尋服務中的另一個資料存放區和搜尋索引。</span><span class="sxs-lookup"><span data-stu-id="aa080-135">In this approach, store authoritative data in the other data store and the search index in Azure Search.</span></span> <span data-ttu-id="aa080-136">Azure 搜尋服務也可用於合併多個資料存放區中的單一搜尋索引。</span><span class="sxs-lookup"><span data-stu-id="aa080-136">Azure Search can also be used to consolidate a single search index from multiple data stores.</span></span>  
* <span data-ttu-id="aa080-137">**電子郵件/文字簡訊**。</span><span class="sxs-lookup"><span data-stu-id="aa080-137">**Email/SMS**.</span></span> <span data-ttu-id="aa080-138">您可以使用 SendGrid、Twilio 等第三方服務來傳送電子郵件或文字簡訊，而不必直接在應用程式中建立這項功能。</span><span class="sxs-lookup"><span data-stu-id="aa080-138">Use a third-party service such as SendGrid or Twilio to send email or SMS messages instead of building this functionality directly into the application.</span></span>
* <span data-ttu-id="aa080-139">**Azure DNS**。</span><span class="sxs-lookup"><span data-stu-id="aa080-139">**Azure DNS**.</span></span> <span data-ttu-id="aa080-140">[Azure DNS][azure-dns] 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構提供名稱解析。</span><span class="sxs-lookup"><span data-stu-id="aa080-140">[Azure DNS][azure-dns] is a hosting service for DNS domains, providing name resolution using Microsoft Azure infrastructure.</span></span> <span data-ttu-id="aa080-141">只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。</span><span class="sxs-lookup"><span data-stu-id="aa080-141">By hosting your domains in Azure, you can manage your DNS records using the same credentials, APIs, tools, and billing as your other Azure services.</span></span>

## <a name="recommendations"></a><span data-ttu-id="aa080-142">建議</span><span class="sxs-lookup"><span data-stu-id="aa080-142">Recommendations</span></span>

<span data-ttu-id="aa080-143">您的需求可能和此處所述的架構不同。</span><span class="sxs-lookup"><span data-stu-id="aa080-143">Your requirements might differ from the architecture described here.</span></span> <span data-ttu-id="aa080-144">以本節的建議作為起點。</span><span class="sxs-lookup"><span data-stu-id="aa080-144">Use the recommendations in this section as a starting point.</span></span>

### <a name="app-service-apps"></a><span data-ttu-id="aa080-145">App Service 應用程式</span><span class="sxs-lookup"><span data-stu-id="aa080-145">App Service apps</span></span>
<span data-ttu-id="aa080-146">建議您將 Web 應用程式和 Web API 建立為不同的 App Service 應用程式。</span><span class="sxs-lookup"><span data-stu-id="aa080-146">We recommend creating the web application and the web API as separate App Service apps.</span></span> <span data-ttu-id="aa080-147">此設計可讓您在個別的 App Service 方案中執行它們，使它們可以獨立調整規模。</span><span class="sxs-lookup"><span data-stu-id="aa080-147">This design lets you run them in separate App Service plans so they can be scaled independently.</span></span> <span data-ttu-id="aa080-148">如果您一開始不需要這種程度的延展性，可以將應用程式部署到相同的方案中，之後有需要時再將它們移到別的方案。</span><span class="sxs-lookup"><span data-stu-id="aa080-148">If you don't need that level of scalability initially, you can deploy the apps into the same plan and move them into separate plans later if necessary.</span></span>

> [!NOTE]
> <span data-ttu-id="aa080-149">在基本、標準和進階方案中，是依照方案中的 VM 執行個體計費，而不是依照應用程式。</span><span class="sxs-lookup"><span data-stu-id="aa080-149">For the Basic, Standard, and Premium plans, you are billed for the VM instances in the plan, not per app.</span></span> <span data-ttu-id="aa080-150">請參閱 [App Service 價格][app-service-pricing]。</span><span class="sxs-lookup"><span data-stu-id="aa080-150">See [App Service Pricing][app-service-pricing]</span></span>
> 
> 

<span data-ttu-id="aa080-151">如果您想要使用 App Service Mobile Apps 的「簡單資料表」或「簡單 API」功能，針對此用途建立個別的 App Service 應用程式。</span><span class="sxs-lookup"><span data-stu-id="aa080-151">If you intend to use the *Easy Tables* or *Easy APIs* features of App Service Mobile Apps, create a separate App Service app for this purpose.</span></span>  <span data-ttu-id="aa080-152">這些功能依賴特定應用程式架構來實踐它們。</span><span class="sxs-lookup"><span data-stu-id="aa080-152">These features rely on a specific application framework to enable them.</span></span>

### <a name="webjobs"></a><span data-ttu-id="aa080-153">WebJobs</span><span class="sxs-lookup"><span data-stu-id="aa080-153">WebJobs</span></span>
<span data-ttu-id="aa080-154">請考慮將需耗用大量資源的 WebJob 部署到個別 App Service 方案中的空白 App Service 應用程式。</span><span class="sxs-lookup"><span data-stu-id="aa080-154">Consider deploying resource intensive WebJobs to an empty App Service app within a separate App Service plan.</span></span> <span data-ttu-id="aa080-155">這可為 WebJob 提供專用的執行個體。</span><span class="sxs-lookup"><span data-stu-id="aa080-155">This provides dedicated instances for the WebJob.</span></span> <span data-ttu-id="aa080-156">請參閱[背景作業指引][webjobs-guidance]。</span><span class="sxs-lookup"><span data-stu-id="aa080-156">See [Background jobs guidance][webjobs-guidance].</span></span>  

### <a name="cache"></a><span data-ttu-id="aa080-157">快取</span><span class="sxs-lookup"><span data-stu-id="aa080-157">Cache</span></span>
<span data-ttu-id="aa080-158">您可以使用 [Azure Redis 快取][azure-redis]來快取某些資料，以改善效能和延展性。</span><span class="sxs-lookup"><span data-stu-id="aa080-158">You can improve performance and scalability by using [Azure Redis Cache][azure-redis] to cache some data.</span></span> <span data-ttu-id="aa080-159">請考慮將 Redis 快取用於：</span><span class="sxs-lookup"><span data-stu-id="aa080-159">Consider using Redis Cache for:</span></span>

* <span data-ttu-id="aa080-160">半靜態交易資料。</span><span class="sxs-lookup"><span data-stu-id="aa080-160">Semi-static transaction data.</span></span>
* <span data-ttu-id="aa080-161">工作階段狀態。</span><span class="sxs-lookup"><span data-stu-id="aa080-161">Session state.</span></span>
* <span data-ttu-id="aa080-162">HTML 輸出。</span><span class="sxs-lookup"><span data-stu-id="aa080-162">HTML output.</span></span> <span data-ttu-id="aa080-163">這對於要呈現複雜 HTML 輸出的應用程式很實用。</span><span class="sxs-lookup"><span data-stu-id="aa080-163">This can be useful in applications that render complex HTML output.</span></span>

<span data-ttu-id="aa080-164">如需設計快取策略的詳細指引，請參閱[快取指引][caching-guidance]。</span><span class="sxs-lookup"><span data-stu-id="aa080-164">For more detailed guidance on designing a caching strategy, see [Caching guidance][caching-guidance].</span></span>

### <a name="cdn"></a><span data-ttu-id="aa080-165">CDN</span><span class="sxs-lookup"><span data-stu-id="aa080-165">CDN</span></span>
<span data-ttu-id="aa080-166">使用 [Azure CDN][azure-cdn] 快取靜態內容。</span><span class="sxs-lookup"><span data-stu-id="aa080-166">Use [Azure CDN][azure-cdn] to cache static content.</span></span> <span data-ttu-id="aa080-167">CDN 的主要優點是可以為使用者降低延遲，因為是在地理位置接近使用者的邊緣伺服器快取內容。</span><span class="sxs-lookup"><span data-stu-id="aa080-167">The main benefit of a CDN is to reduce latency for users, because content is cached at an edge server that is geographically close to the user.</span></span> <span data-ttu-id="aa080-168">CDN 也可以減少應用程式的負載，因為流量不是由應用程式處理。</span><span class="sxs-lookup"><span data-stu-id="aa080-168">CDN can also reduce load on the application, because that traffic is not being handled by the application.</span></span>

<span data-ttu-id="aa080-169">如果您的應用程式大部分是靜態網頁，請考慮使用 [CDN 快取整個應用程式][cdn-app-service]。</span><span class="sxs-lookup"><span data-stu-id="aa080-169">If your app consists mostly of static pages, consider using [CDN to cache the entire app][cdn-app-service].</span></span> <span data-ttu-id="aa080-170">否則，將靜態內容 (例如影像、CSS、HTML 檔案) 放在 [Azure 儲存體，並使用 CDN 快取這些檔案][cdn-storage-account]。</span><span class="sxs-lookup"><span data-stu-id="aa080-170">Otherwise, put static content such as images, CSS, and HTML files, into [Azure Storage and use CDN to cache those files][cdn-storage-account].</span></span>

> [!NOTE]
> <span data-ttu-id="aa080-171">Azure CDN 無法服務需要驗證的內容。</span><span class="sxs-lookup"><span data-stu-id="aa080-171">Azure CDN cannot serve content that requires authentication.</span></span>
> 
> 

<span data-ttu-id="aa080-172">如需詳細指引，請參閱[內容傳遞網路 (CDN) 指引][cdn-guidance]。</span><span class="sxs-lookup"><span data-stu-id="aa080-172">For more detailed guidance, see [Content Delivery Network (CDN) guidance][cdn-guidance].</span></span>

### <a name="storage"></a><span data-ttu-id="aa080-173">儲存體</span><span class="sxs-lookup"><span data-stu-id="aa080-173">Storage</span></span>
<span data-ttu-id="aa080-174">現代應用程式通常要處理大量的資料。</span><span class="sxs-lookup"><span data-stu-id="aa080-174">Modern applications often process large amounts of data.</span></span> <span data-ttu-id="aa080-175">為了能針對雲端調整規模，請務必選擇正確的儲存體類型。</span><span class="sxs-lookup"><span data-stu-id="aa080-175">In order to scale for the cloud, it's important to choose the right storage type.</span></span> <span data-ttu-id="aa080-176">以下是一些基準建議。</span><span class="sxs-lookup"><span data-stu-id="aa080-176">Here are some baseline recommendations.</span></span> 

| <span data-ttu-id="aa080-177">您想要儲存的內容</span><span class="sxs-lookup"><span data-stu-id="aa080-177">What you want to store</span></span> | <span data-ttu-id="aa080-178">範例</span><span class="sxs-lookup"><span data-stu-id="aa080-178">Example</span></span> | <span data-ttu-id="aa080-179">建議的儲存體</span><span class="sxs-lookup"><span data-stu-id="aa080-179">Recommended storage</span></span> |
| --- | --- | --- |
| <span data-ttu-id="aa080-180">檔案</span><span class="sxs-lookup"><span data-stu-id="aa080-180">Files</span></span> |<span data-ttu-id="aa080-181">影像、文件、PDF</span><span class="sxs-lookup"><span data-stu-id="aa080-181">Images, documents, PDFs</span></span> |<span data-ttu-id="aa080-182">Azure Blob 儲存體</span><span class="sxs-lookup"><span data-stu-id="aa080-182">Azure Blob Storage</span></span> |
| <span data-ttu-id="aa080-183">索引鍵/值組</span><span class="sxs-lookup"><span data-stu-id="aa080-183">Key/Value pairs</span></span> |<span data-ttu-id="aa080-184">依使用者識別碼查閱的使用者設定檔資料</span><span class="sxs-lookup"><span data-stu-id="aa080-184">User profile data looked up by user ID</span></span> |<span data-ttu-id="aa080-185">Azure 資料表儲存體</span><span class="sxs-lookup"><span data-stu-id="aa080-185">Azure Table storage</span></span> |
| <span data-ttu-id="aa080-186">用來觸發進一步處理的簡訊</span><span class="sxs-lookup"><span data-stu-id="aa080-186">Short messages intended to trigger further processing</span></span> |<span data-ttu-id="aa080-187">訂單要求</span><span class="sxs-lookup"><span data-stu-id="aa080-187">Order requests</span></span> |<span data-ttu-id="aa080-188">Azure 佇列儲存體、服務匯流排佇列或服務匯流排主題</span><span class="sxs-lookup"><span data-stu-id="aa080-188">Azure Queue storage, Service Bus queue, or Service Bus topic</span></span> |
| <span data-ttu-id="aa080-189">具有彈性結構描述且需要基本查詢的非關聯式資料</span><span class="sxs-lookup"><span data-stu-id="aa080-189">Non-relational data with a flexible schema requiring basic querying</span></span> |<span data-ttu-id="aa080-190">產品目錄</span><span class="sxs-lookup"><span data-stu-id="aa080-190">Product catalog</span></span> |<span data-ttu-id="aa080-191">文件資料庫，例如 Azure Cosmos DB、MongoDB 或 Apache CouchDB</span><span class="sxs-lookup"><span data-stu-id="aa080-191">Document database, such as Azure Cosmos DB, MongoDB, or Apache CouchDB</span></span> |
| <span data-ttu-id="aa080-192">需要更豐富查詢支援、嚴格結構描述，及/或強式一致性的關聯式資料</span><span class="sxs-lookup"><span data-stu-id="aa080-192">Relational data requiring richer query support, strict schema, and/or strong consistency</span></span> |<span data-ttu-id="aa080-193">產品庫存</span><span class="sxs-lookup"><span data-stu-id="aa080-193">Product inventory</span></span> |<span data-ttu-id="aa080-194">連接字串</span><span class="sxs-lookup"><span data-stu-id="aa080-194">Azure SQL Database</span></span> |

## <a name="scalability-considerations"></a><span data-ttu-id="aa080-195">延展性考量</span><span class="sxs-lookup"><span data-stu-id="aa080-195">Scalability considerations</span></span>

<span data-ttu-id="aa080-196">Azure App Service 的主要優點是能夠根據負載調整應用程式規模。</span><span class="sxs-lookup"><span data-stu-id="aa080-196">A major benefit of Azure App Service is the ability to scale your application based on load.</span></span> <span data-ttu-id="aa080-197">以下是規劃調整應用程式時，應記住的一些考量。</span><span class="sxs-lookup"><span data-stu-id="aa080-197">Here are some considerations to keep in mind when planning to scale your application.</span></span>

### <a name="app-service-app"></a><span data-ttu-id="aa080-198">App Service 應用程式</span><span class="sxs-lookup"><span data-stu-id="aa080-198">App Service app</span></span>
<span data-ttu-id="aa080-199">如果您的解決方案包含數個 App Service 應用程式，考慮將它們部署在不同的 App Service 方案中。</span><span class="sxs-lookup"><span data-stu-id="aa080-199">If your solution includes several App Service apps, consider deploying them to separate App Service plans.</span></span> <span data-ttu-id="aa080-200">這種做法可讓您分別調整它們，因為它們在不同的執行個體上執行。</span><span class="sxs-lookup"><span data-stu-id="aa080-200">This approach enables you to scale them independently because they run on separate instances.</span></span> 

<span data-ttu-id="aa080-201">同樣地，請考慮將 WebJob 放入它自己的方案中，使背景工作不在處理 HTTP 要求的相同執行個體上執行。</span><span class="sxs-lookup"><span data-stu-id="aa080-201">Similarly, consider putting a WebJob into its own plan so that background tasks don't run on the same instances that handle HTTP requests.</span></span>  

### <a name="sql-database"></a><span data-ttu-id="aa080-202">SQL Database</span><span class="sxs-lookup"><span data-stu-id="aa080-202">SQL Database</span></span>
<span data-ttu-id="aa080-203">藉由將資料庫「分區」，提高 SQL 資料庫的延展性。</span><span class="sxs-lookup"><span data-stu-id="aa080-203">Increase scalability of a SQL database by *sharding* the database.</span></span> <span data-ttu-id="aa080-204">分區是指以水平方式分割資料庫。</span><span class="sxs-lookup"><span data-stu-id="aa080-204">Sharding refers to partitioning the database horizontally.</span></span> <span data-ttu-id="aa080-205">分區可讓您使用[彈性資料庫工具][sql-elastic]以水平方式相應放大資料庫。</span><span class="sxs-lookup"><span data-stu-id="aa080-205">Sharding allows you to scale out the database horizontally using [Elastic Database tools][sql-elastic].</span></span> <span data-ttu-id="aa080-206">分區的潛在優點包括：</span><span class="sxs-lookup"><span data-stu-id="aa080-206">Potential benefits of sharding include:</span></span>

- <span data-ttu-id="aa080-207">較佳的交易輸送量。</span><span class="sxs-lookup"><span data-stu-id="aa080-207">Better transaction throughput.</span></span>
- <span data-ttu-id="aa080-208">對資料子集合執行的查詢可以更快速。</span><span class="sxs-lookup"><span data-stu-id="aa080-208">Queries can run faster over a subset of the data.</span></span>

### <a name="azure-search"></a><span data-ttu-id="aa080-209">Azure 搜尋服務</span><span class="sxs-lookup"><span data-stu-id="aa080-209">Azure Search</span></span>
<span data-ttu-id="aa080-210">Azure 搜尋服務替主要資料存放區省去了執行複雜資料搜尋的額外負荷，而且可加以調整以處理負載。</span><span class="sxs-lookup"><span data-stu-id="aa080-210">Azure Search removes the overhead of performing complex data searches from the primary data store, and it can scale to handle load.</span></span> <span data-ttu-id="aa080-211">請參閱[在 Azure 搜尋服務中調整適用於查詢和編製索引工作負載的資源等級][azure-search-scaling]。</span><span class="sxs-lookup"><span data-stu-id="aa080-211">See [Scale resource levels for query and indexing workloads in Azure Search][azure-search-scaling].</span></span>

## <a name="security-considerations"></a><span data-ttu-id="aa080-212">安全性考量</span><span class="sxs-lookup"><span data-stu-id="aa080-212">Security considerations</span></span>
<span data-ttu-id="aa080-213">本節列出本文所述的 Azure 服務專屬的安全性考量，</span><span class="sxs-lookup"><span data-stu-id="aa080-213">This section lists security considerations that are specific to the Azure services described in this article.</span></span> <span data-ttu-id="aa080-214">但不是安全性最佳做法的完整清單。</span><span class="sxs-lookup"><span data-stu-id="aa080-214">It's not a complete list of security best practices.</span></span> <span data-ttu-id="aa080-215">如需更多的安全性考量，請參閱[保護 Azure App Service 中的應用程式][app-service-security]。</span><span class="sxs-lookup"><span data-stu-id="aa080-215">For some additional security considerations, see [Secure an app in Azure App Service][app-service-security].</span></span>

### <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="aa080-216">跨原始來源資源分享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="aa080-216">Cross-Origin Resource Sharing (CORS)</span></span>
<span data-ttu-id="aa080-217">如果您將網站和 Web API 建立為不同的應用程式，則網站無法對 API 進行用戶端 AJAX 呼叫，除非您啟用 CORS。</span><span class="sxs-lookup"><span data-stu-id="aa080-217">If you create a website and web API as separate apps, the website cannot make client-side AJAX calls to the API unless you enable CORS.</span></span>

> [!NOTE]
> <span data-ttu-id="aa080-218">瀏覽器安全性可防止網頁對另一個網域提出 AJAX 要求。</span><span class="sxs-lookup"><span data-stu-id="aa080-218">Browser security prevents a web page from making AJAX requests to another domain.</span></span> <span data-ttu-id="aa080-219">這項限制稱為同源原則，可防止惡意網站從另一個網站讀取敏感性資料。</span><span class="sxs-lookup"><span data-stu-id="aa080-219">This restriction is called the same-origin policy, and prevents a malicious site from reading sentitive data from another site.</span></span> <span data-ttu-id="aa080-220">跨原始來源資源共用 (CORS) 是 W3C 標準，可讓伺服器放寬同源原則，允許一些跨來源要求，並拒絕其他要求。</span><span class="sxs-lookup"><span data-stu-id="aa080-220">CORS is a W3C standard that allows a server to relax the same-origin policy and allow some cross-origin requests while rejecting others.</span></span>
> 
> 

<span data-ttu-id="aa080-221">App Service 已內建 CORS 支援，不需要再撰寫任何應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="aa080-221">App Services has built-in support for CORS, without needing to write any application code.</span></span> <span data-ttu-id="aa080-222">請參閱[使用 CORS 從 JavaScript 取用 API 應用程式][cors]。</span><span class="sxs-lookup"><span data-stu-id="aa080-222">See [Consume an API app from JavaScript using CORS][cors].</span></span> <span data-ttu-id="aa080-223">將網站新增至 API 允許的來源清單。</span><span class="sxs-lookup"><span data-stu-id="aa080-223">Add the website to the list of allowed origins for the API.</span></span>

### <a name="sql-database-encryption"></a><span data-ttu-id="aa080-224">SQL Database 加密</span><span class="sxs-lookup"><span data-stu-id="aa080-224">SQL Database encryption</span></span>
<span data-ttu-id="aa080-225">如果您要加密資料庫中的靜止資料，使用[透明資料加密][sql-encryption]。</span><span class="sxs-lookup"><span data-stu-id="aa080-225">Use [Transparent Data Encryption][sql-encryption] if you need to encrypt data at rest in the database.</span></span> <span data-ttu-id="aa080-226">這個功能可執行整個資料庫 (包括備份和交易記錄) 的即時加密和解密，不需變更應用程式。</span><span class="sxs-lookup"><span data-stu-id="aa080-226">This feature performs real-time encryption and decryption of an entire database (including backups and transaction log files) and requires no changes to the application.</span></span> <span data-ttu-id="aa080-227">加密會增加一些延遲，所以最好的作法是將必須保護的資料另外放在它自己的資料庫中，只啟用該資料庫的加密。</span><span class="sxs-lookup"><span data-stu-id="aa080-227">Encryption does add some latency, so it's a good practice to separate the data that must be secure into its own database and enable encryption only for that database.</span></span>  
  

<!-- links -->

[api-guidance]: ../../best-practices/api-design.md
[app-service-security]: /azure/app-service-web/web-sites-security
[app-service-web-app]: /azure/app-service-web/app-service-web-overview
[app-service-api-app]: /azure/app-service-api/app-service-api-apps-why-best-platform
[app-service-pricing]: https://azure.microsoft.com/pricing/details/app-service/
[azure-cdn]: https://azure.microsoft.com/services/cdn/
[azure-dns]: /azure/dns/dns-overview
[azure-redis]: https://azure.microsoft.com/services/cache/
[azure-search]: https://azure.microsoft.com/documentation/services/search/
[azure-search-scaling]: /azure/search/search-capacity-planning
[background-jobs]: ../../best-practices/background-jobs.md
[basic-web-app]: basic-web-app.md
[basic-web-app-scalability]: basic-web-app.md#scalability-considerations
[caching-guidance]: ../../best-practices/caching.md
[cdn-app-service]: /azure/app-service-web/cdn-websites-with-cdn
[cdn-storage-account]: /azure/cdn/cdn-create-a-storage-account-with-cdn
[cdn-guidance]: ../../best-practices/cdn.md
[cors]: /azure/app-service-api/app-service-api-cors-consume-javascript
[documentdb]: https://azure.microsoft.com/documentation/services/documentdb/
[queue-storage]: /azure/storage/storage-dotnet-how-to-use-queues
[queues-compared]: /azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted
[resource-group]: /azure/azure-resource-manager/resource-group-overview#resource-groups
[sql-db]: https://azure.microsoft.com/documentation/services/sql-database/
[sql-elastic]: /azure/sql-database/sql-database-elastic-scale-introduction
[sql-encryption]: https://msdn.microsoft.com/library/dn948096.aspx
[tm]: https://azure.microsoft.com/services/traffic-manager/
[visio-download]: https://archcenter.azureedge.net/cdn/app-service-reference-architectures.vsdx
[web-app-multi-region]: ./multi-region.md
[webjobs-guidance]: ../../best-practices/background-jobs.md
[webjobs]: /azure/app-service/app-service-webjobs-readme
[0]: ./images/scalable-web-app.png "Azure 中 Web 應用程式的延展性改善"

---
title: 電子商務的智慧產品搜尋引擎
description: 提供電子商務應用程式中的世界級搜尋體驗。
author: jelledruyts
ms.date: 09/14/2018
ms.custom: fasttrack
ms.openlocfilehash: 5eabdb94b9345e73b21526681e0dbd6ae859d7be
ms.sourcegitcommit: a0e8d11543751d681953717f6e78173e597ae207
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2018
ms.locfileid: "53004898"
---
# <a name="intelligent-product-search-engine-for-e-commerce"></a><span data-ttu-id="e04f3-103">電子商務的智慧產品搜尋引擎</span><span class="sxs-lookup"><span data-stu-id="e04f3-103">Intelligent product search engine for e-commerce</span></span>

<span data-ttu-id="e04f3-104">此範例案例示範使用專用的搜尋服務，如何大幅增加電子商務客戶的搜尋結果相關性。</span><span class="sxs-lookup"><span data-stu-id="e04f3-104">This example scenario shows how using a dedicated search service can dramatically increase the relevance of search results for your e-commerce customers.</span></span>

<span data-ttu-id="e04f3-105">搜尋是可讓客戶尋找且最終購買產品的主要機制，因此一定要讓搜尋結果與搜尋查詢「意圖」相關，藉由提供幾近即時的結果、語言分析、地理位置比對、篩選、Faceting、自動完成、命中標示等，讓端對端搜尋經驗符合搜尋巨人的搜尋經驗。</span><span class="sxs-lookup"><span data-stu-id="e04f3-105">Search is the primary mechanism through which customers find and ultimately purchase products, making it essential that search results are relevant to the _intent_ of the search query, and that the end-to-end search experience matches that of search giants by providing near-instant results, linguistic analysis, geo-location matching, filtering, faceting, autocomplete, hit highlighting, etc.</span></span>

<span data-ttu-id="e04f3-106">假設有一個典型電子商務 Web 應用程式，其產品資料儲存在 SQL Server 或 Azure SQL Database 等關聯式資料庫中。</span><span class="sxs-lookup"><span data-stu-id="e04f3-106">Imagine a typical e-commerce web application with product data stored in a relational database like SQL Server or Azure SQL Database.</span></span> <span data-ttu-id="e04f3-107">使用 `LIKE` 查詢或[全文檢索搜尋][docs-sql-fts]功能，通常會在資料庫內部處理搜尋查詢。</span><span class="sxs-lookup"><span data-stu-id="e04f3-107">Search queries are often handled inside the database using `LIKE` queries or [Full-Text Search][docs-sql-fts] features.</span></span> <span data-ttu-id="e04f3-108">改用 [Azure 搜尋服務][docs-search]，即可將操作資料庫從查詢處理中釋出，並可輕鬆地開始利用這些難以實作的功能，進而為您的客戶提供最佳的搜尋體驗。</span><span class="sxs-lookup"><span data-stu-id="e04f3-108">By using [Azure Search][docs-search] instead, you free up your operational database from the query processing and you can easily start taking advantage of those hard-to-implement features that provide your customers with the best possible search experience.</span></span> <span data-ttu-id="e04f3-109">此外，因為 Azure 搜尋服務是平台即服務 (PaaS) 元件，所以您不必擔心管理基礎結構或成為搜尋專家。</span><span class="sxs-lookup"><span data-stu-id="e04f3-109">Also, because Azure Search is a platform as a service (PaaS) component, you don't have to worry about managing infrastructure or becoming a search expert.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="e04f3-110">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="e04f3-110">Relevant use cases</span></span>

<span data-ttu-id="e04f3-111">其他相關的使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="e04f3-111">Other relevant use cases include:</span></span>

* <span data-ttu-id="e04f3-112">尋找使用者實體位置附近的房地產清單或商店。</span><span class="sxs-lookup"><span data-stu-id="e04f3-112">Finding real estate listings or stores near the user's physical location.</span></span>
* <span data-ttu-id="e04f3-113">搜尋新聞網站中的文章，或尋找運動賽事結果，而且偏好比較「近期」的資訊。</span><span class="sxs-lookup"><span data-stu-id="e04f3-113">Searching for articles in a news site or looking for sports results, with a higher preference for more _recent_ information.</span></span>
* <span data-ttu-id="e04f3-114">在大型存放庫中搜尋「以文件為主」的組織，例如政策制定者和公證人。</span><span class="sxs-lookup"><span data-stu-id="e04f3-114">Searching through large repositories for _document-centric_ organizations like policy makers and notaries.</span></span>

<span data-ttu-id="e04f3-115">最終「任何」具有某種形式搜尋功能的應用程式均可受益於專用的搜尋服務。</span><span class="sxs-lookup"><span data-stu-id="e04f3-115">Ultimately, _any_ application that has some form of search functionality can benefit from a dedicated search service.</span></span>

## <a name="architecture"></a><span data-ttu-id="e04f3-116">架構</span><span class="sxs-lookup"><span data-stu-id="e04f3-116">Architecture</span></span>

![電子商務用智慧型產品搜尋引擎相關 Azure 元件的架構概觀][architecture]

<span data-ttu-id="e04f3-118">此案例涵蓋客戶可以搜尋產品目錄的電子商務解決方案。</span><span class="sxs-lookup"><span data-stu-id="e04f3-118">This scenario covers an e-commerce solution where customers can search through a product catalog.</span></span>
1. <span data-ttu-id="e04f3-119">客戶可以從任何裝置瀏覽至**電子商務 Web 應用程式**。</span><span class="sxs-lookup"><span data-stu-id="e04f3-119">Customers navigate to the **e-commerce web application** from any device.</span></span>
2. <span data-ttu-id="e04f3-120">產品目錄會保留在 **Azure SQL Database**中進行交易處理。</span><span class="sxs-lookup"><span data-stu-id="e04f3-120">The product catalog is maintained in an **Azure SQL Database** for transactional processing.</span></span>
3. <span data-ttu-id="e04f3-121">Azure 搜尋服務會使用**搜尋索引子**，透過整合式變更追蹤使其搜尋索引自動保持最新狀態。</span><span class="sxs-lookup"><span data-stu-id="e04f3-121">Azure Search uses a **search indexer** to automatically keep its search index up-to-date through integrated change tracking.</span></span>
4. <span data-ttu-id="e04f3-122">客戶的搜尋查詢會卸載至 **Azure 搜尋服務**，該服務會處理查詢並傳回最相關的結果。</span><span class="sxs-lookup"><span data-stu-id="e04f3-122">Customer's search queries are offloaded to the **Azure Search** service, which processes the query and returns the most relevant results.</span></span>
5. <span data-ttu-id="e04f3-123">除了網頁式搜尋體驗，客戶也可以在社交媒體中使用**交談式 Bot**，或直接從個人數位助理搜尋產品，並逐漸精簡其搜尋查詢和結果。</span><span class="sxs-lookup"><span data-stu-id="e04f3-123">As an alternative to a web-based search experience, customers can also use a **conversational bot** in social media or straight from digital assistants to search for products and incrementally refine their search query and results.</span></span>
6. <span data-ttu-id="e04f3-124">(選擇性) **認知搜尋**功能可用來套用人工智慧，甚至更聰明的處理。</span><span class="sxs-lookup"><span data-stu-id="e04f3-124">Optionally, the **Cognitive Search** feature can be used to apply artificial intelligence for even smarter processing.</span></span>

### <a name="components"></a><span data-ttu-id="e04f3-125">元件</span><span class="sxs-lookup"><span data-stu-id="e04f3-125">Components</span></span>

* <span data-ttu-id="e04f3-126">[應用程式服務 - Web Apps][docs-webapps] 會裝載 Web 應用程式，允許自動調整及高可用性，而不需要管理基礎結構。</span><span class="sxs-lookup"><span data-stu-id="e04f3-126">[App Services - Web Apps][docs-webapps] hosts web applications allowing autoscale and high availability without having to manage infrastructure.</span></span>
* <span data-ttu-id="e04f3-127">[SQL Database][docs-sql-database] 是 Microsoft Azure 中的一般用途關聯式資料庫受控服務，可支援關聯式資料、JSON、空間和 XML 等結構。</span><span class="sxs-lookup"><span data-stu-id="e04f3-127">[SQL Database][docs-sql-database] is a general-purpose relational database-managed service in Microsoft Azure that supports structures such as relational data, JSON, spatial, and XML.</span></span>
* <span data-ttu-id="e04f3-128">[Azure 搜尋服務][docs-search]是搜尋即服務雲端解決方案，透過 Web、行動和企業應用程式中的私用和異質內容來提供豐富的搜尋體驗。</span><span class="sxs-lookup"><span data-stu-id="e04f3-128">[Azure Search][docs-search] is a search-as-a-service cloud solution that provides a rich search experience over private, heterogenous content in web, mobile, and enterprise applications.</span></span>
* <span data-ttu-id="e04f3-129">[Bot 服務][docs-botservice] 提供工具，可以建置、測試、部署及管理智慧型聊天機器人。</span><span class="sxs-lookup"><span data-stu-id="e04f3-129">[Bot Service][docs-botservice] provides tools to build, test, deploy, and manage intelligent bots.</span></span>
* <span data-ttu-id="e04f3-130">[認知服務][docs-cognitive]可讓您使用智慧型演算法，透過自然的溝通方式，來查看、聆聽、述說、了解及詮釋您的使用者需求。</span><span class="sxs-lookup"><span data-stu-id="e04f3-130">[Cognitive Services][docs-cognitive] lets you use intelligent algorithms to see, hear, speak, understand, and interpret your user needs through natural methods of communication.</span></span>

### <a name="alternatives"></a><span data-ttu-id="e04f3-131">替代項目</span><span class="sxs-lookup"><span data-stu-id="e04f3-131">Alternatives</span></span>

* <span data-ttu-id="e04f3-132">您可以使用**資料庫內搜尋**功能，例如，透過 SQL Server 全文檢索搜尋，但是您的交易存放區也會處理查詢 (增加處理能力的需求)，而且資料庫內的搜尋功能更加有限。</span><span class="sxs-lookup"><span data-stu-id="e04f3-132">You could use **in-database search** capabilities, for example, through SQL Server full-text search, but then your transactional store also processes queries (increasing the need for processing power) and the search capabilities inside the database are more limited.</span></span>
* <span data-ttu-id="e04f3-133">您可以在 Azure 虛擬機器上裝載開放原始碼的 [Apache Lucene][apache-lucene] (Azure 搜尋服務的建置基礎)，但是您會回到管理基礎結構即服務 (IaaS)，而且並未受益於 Azure 搜尋服務根據 Lucene 所提供的許多功能。</span><span class="sxs-lookup"><span data-stu-id="e04f3-133">You could host the open-source [Apache Lucene][apache-lucene] (on which Azure Search is built upon) on Azure Virtual Machines, but then you are back to managing Infrastructure-as-a-Service (IaaS) and don't benefit from the many features that Azure Search provides on top of Lucene.</span></span>
* <span data-ttu-id="e04f3-134">您也可以考慮從 Azure Marketplace 部署 [Elastic Search][elastic-marketplace]，這是來自第三方廠商的替代搜尋產品，但在此情況下您也會執行 IaaS 工作負載。</span><span class="sxs-lookup"><span data-stu-id="e04f3-134">You could also consider deploying [Elastic Search][elastic-marketplace] from the Azure Marketplace, which is an alternative and capable search product from a third-party vendor, but also in this case you are running an IaaS workload.</span></span>

<span data-ttu-id="e04f3-135">資料層的其他選項包括：</span><span class="sxs-lookup"><span data-stu-id="e04f3-135">Other options for the data tier include:</span></span>

* <span data-ttu-id="e04f3-136">[Cosmos DB](/azure/cosmos-db/introduction) - Microsoft 全球發行的多模型資料庫。</span><span class="sxs-lookup"><span data-stu-id="e04f3-136">[Cosmos DB](/azure/cosmos-db/introduction) - Microsoft's globally distributed, multi-model database.</span></span> <span data-ttu-id="e04f3-137">Cosmos DB 提供平台來執行其他資料模型，例如 Mongo DB、Cassandra、Graph 資料或簡單的資料表儲存體。</span><span class="sxs-lookup"><span data-stu-id="e04f3-137">Costmos DB provides a platform to run other data models such as Mongo DB, Cassandra, Graph data, or simple table storage.</span></span> <span data-ttu-id="e04f3-138">Azure 搜尋服務也支援直接從 Cosmos DB 將資料編製索引。</span><span class="sxs-lookup"><span data-stu-id="e04f3-138">Azure Search also supports indexing the data from Cosmos DB directly.</span></span>

## <a name="considerations"></a><span data-ttu-id="e04f3-139">考量</span><span class="sxs-lookup"><span data-stu-id="e04f3-139">Considerations</span></span>

### <a name="scalability"></a><span data-ttu-id="e04f3-140">延展性</span><span class="sxs-lookup"><span data-stu-id="e04f3-140">Scalability</span></span>

<span data-ttu-id="e04f3-141">Azure 搜尋服務的[定價層][search-tier]並不會決定可用的功能，但主要用於[容量規劃][search-capacity]，因為它會定義您可取得的最大儲存體，以及您可佈建的分割區和複本數目。</span><span class="sxs-lookup"><span data-stu-id="e04f3-141">The [pricing tier][search-tier] of the Azure Search service doesn't determine the available features but is used mainly for [capacity planning][search-capacity] as it defines the maximum storage you get and how many partitions and replicas you can provision.</span></span> <span data-ttu-id="e04f3-142">**分割區**可讓您將更多文件編製索引並取得更高的寫入輸送量，然而**複本**可提供更多每秒查詢次數 (QPS) 和高可用性。</span><span class="sxs-lookup"><span data-stu-id="e04f3-142">**Partitions** allow you to index more documents and get higher write throughputs, whereas **replicas** provide more Queries-Per-Second (QPS) and High Availability.</span></span>

<span data-ttu-id="e04f3-143">您可以動態變更分割區和複本數目，但不可能變更定價層，因此您應該仔細考慮目標工作負載的適合階層。</span><span class="sxs-lookup"><span data-stu-id="e04f3-143">You can dynamically change the number of partitions and replicas but it's not possible to change the pricing tier, so you should carefully consider the right tier for your target workload.</span></span> <span data-ttu-id="e04f3-144">如果您無論如何都需要變更階層，則必須並排佈建新的服務並在其中重新載入索引，以便屆時指向新服務上的應用程式。</span><span class="sxs-lookup"><span data-stu-id="e04f3-144">If you need to change the tier anyway, you will need to provision a new service side by side and reload your indexes there, at which point you can point your applications at the new service.</span></span>

### <a name="availability"></a><span data-ttu-id="e04f3-145">可用性</span><span class="sxs-lookup"><span data-stu-id="e04f3-145">Availability</span></span>

<span data-ttu-id="e04f3-146">Azure 搜尋服務可提供 [99.9% 可用性 SLA][search-sla]：如果有至少兩個複本，則適用於「讀取」 (也就查詢)，而如果有至少三個複本，則適用於「更新」 (也就是更新搜尋索引)。</span><span class="sxs-lookup"><span data-stu-id="e04f3-146">Azure Search provides a [99.9% availability SLA][search-sla] for _reads_ (that is, querying) if you have at least two replicas, and for _updates_ (that is, updating the search indexes) if you have at least three replicas.</span></span> <span data-ttu-id="e04f3-147">因此，如果您希望客戶能夠可靠地「搜尋」，則應該佈建至少兩個複本，而如果實際「索引變更」也應視為高可用性作業，則佈建三個複本。</span><span class="sxs-lookup"><span data-stu-id="e04f3-147">Therefore you should provision at least two replicas if you want your customers to be able to _search_ reliably, and 3 if actual _changes to the index_ should also be considered high availability operations.</span></span>

<span data-ttu-id="e04f3-148">如果需要在不停機的情況下，對索引進行重大變更 (例如，變更資料類型、刪除或重新命名欄位)，則必須重建索引。</span><span class="sxs-lookup"><span data-stu-id="e04f3-148">If there is a need to make breaking changes to the index without downtime (for example, changing data types, deleting or renaming fields), the index will need to be rebuilt.</span></span> <span data-ttu-id="e04f3-149">類似於變更服務層，這表示建立新的索引、重新填入資料，然後將您的應用程式更新為指向新索引。</span><span class="sxs-lookup"><span data-stu-id="e04f3-149">Similar to changing service tier, this means creating a new index, repopulating it with the data, and then updating your applications to point at the new index.</span></span>

### <a name="security"></a><span data-ttu-id="e04f3-150">安全性</span><span class="sxs-lookup"><span data-stu-id="e04f3-150">Security</span></span>

<span data-ttu-id="e04f3-151">Azure 搜尋服務符合許多[安全性和資料隱私權標準][search-security]，因而可能使用於大部分的產業。</span><span class="sxs-lookup"><span data-stu-id="e04f3-151">Azure Search is compliant with many [security and data privacy standards][search-security], which makes it possible to be used in most industries.</span></span>

<span data-ttu-id="e04f3-152">為了保護服務的存取權，Azure 搜尋服務會使用兩種金鑰：**系統管理金鑰**，這可讓您對此服務執行「任何」工作，以及**查詢金鑰**，這只能使用於查詢等唯讀作業。</span><span class="sxs-lookup"><span data-stu-id="e04f3-152">For securing access to the service, Azure Search uses two types of keys: **admin keys**, which allow you to perform _any_ task against the service, and **query keys**, which can only be used for read-only operations like querying.</span></span> <span data-ttu-id="e04f3-153">執行搜尋的應用程式通常不會更新索引，因此只能以查詢金鑰設定，而不能以系統管理金鑰設定 (尤其是從使用者裝置執行搜尋時，例如在網頁瀏覽器中執行的指令碼)。</span><span class="sxs-lookup"><span data-stu-id="e04f3-153">Typically, the application that performs the search does not update the index, so it should only be configured with a query key and not an admin key (especially if the search is performed from an end-user device like script running in a web browser).</span></span>

### <a name="search-relevance"></a><span data-ttu-id="e04f3-154">搜尋相關性</span><span class="sxs-lookup"><span data-stu-id="e04f3-154">Search Relevance</span></span>

<span data-ttu-id="e04f3-155">電子商務應用程式的成功程度主要取決於搜尋結果與您客戶的相關性。</span><span class="sxs-lookup"><span data-stu-id="e04f3-155">How successful your e-commerce application is depends largely on the relevance of the search results to your customers.</span></span> <span data-ttu-id="e04f3-156">仔細調整您的搜尋服務，以提供以使用者研究為基礎的最佳結果，或依賴內建功能 (例如[搜尋流量分析][search-analysis]) 來了解客戶的搜尋模式，可讓您根據資料做出決策。</span><span class="sxs-lookup"><span data-stu-id="e04f3-156">Carefully tuning your search service to provide optimal results based on user research, or relying on built-in features such as [search traffic analysis][search-analysis] to understand your customer's search patterns allows you to make decisions based on data.</span></span>

<span data-ttu-id="e04f3-157">微調搜尋服務的典型方式包括：</span><span class="sxs-lookup"><span data-stu-id="e04f3-157">Typical ways to tune your search service include:</span></span>

* <span data-ttu-id="e04f3-158">使用[評分設定檔][search-scoring]來影響搜尋結果的相關性，比方說，根據哪個欄位與查詢相符、資料有多新、使用者的地理距離等等。</span><span class="sxs-lookup"><span data-stu-id="e04f3-158">Using [scoring profiles][search-scoring] to influence the relevance of search results, for example, based on which field matched the query, how recent the data is, the geographical distance to the user, ...</span></span>
* <span data-ttu-id="e04f3-159">使用 [Microsoft 提供的語言分析器][search-languages]，以便使用進階自然語言處理 (NLP) 堆疊進一步解譯查詢。</span><span class="sxs-lookup"><span data-stu-id="e04f3-159">Using [Microsoft provided language analyzers][search-languages] that use an advanced Natural Language Processing (NLP) stack to better interpret queries</span></span>
* <span data-ttu-id="e04f3-160">使用[自訂分析器][ search-analyzers]，確保正確找到您的產品，尤其在您想要搜尋非語言型資訊時，例如產品的製造商和型號。</span><span class="sxs-lookup"><span data-stu-id="e04f3-160">Using [custom analyzers][search-analyzers] to ensure your products are found correctly, especially if you want to search on non-language based information like a product's make and model.</span></span>

## <a name="deploy-this-scenario"></a><span data-ttu-id="e04f3-161">部署此案例</span><span class="sxs-lookup"><span data-stu-id="e04f3-161">Deploy this scenario</span></span>

<span data-ttu-id="e04f3-162">若要部署此案例更完整的電子商務版本，您可以遵循此[逐步教學課程][end-to-end-walkthrough]，其中提供執行簡單票證購買應用程式的 .NET 範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="e04f3-162">To deploy a more complete e-commerce version of this scenario, you can follow this [step-by-step tutorial][end-to-end-walkthrough] that provides a .NET sample application that runs a simple ticket purchasing application.</span></span> <span data-ttu-id="e04f3-163">它還包含 Azure 搜尋服務，並使用許多討論的功能。</span><span class="sxs-lookup"><span data-stu-id="e04f3-163">It also includes Azure Search and uses many of the features discussed.</span></span> <span data-ttu-id="e04f3-164">此外，還有 Resource Manager 範本，可以將大部分 Azure 資源的部署自動化。</span><span class="sxs-lookup"><span data-stu-id="e04f3-164">Additionally, there is a Resource Manager template to automate the deployment of most of the Azure resources.</span></span>

## <a name="pricing"></a><span data-ttu-id="e04f3-165">價格</span><span class="sxs-lookup"><span data-stu-id="e04f3-165">Pricing</span></span>

<span data-ttu-id="e04f3-166">為了探索執行此案例的成本，上述所有服務都會在成本計算機中預先設定。</span><span class="sxs-lookup"><span data-stu-id="e04f3-166">To explore the cost of running this scenario, all the services mentioned above are pre-configured in the cost calculator.</span></span> <span data-ttu-id="e04f3-167">若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的使用量。</span><span class="sxs-lookup"><span data-stu-id="e04f3-167">To see how the pricing would change for your particular use case change the appropriate variables to match your expected usage.</span></span>

<span data-ttu-id="e04f3-168">我們根據您預期取得的流量，提供了三個範例成本設定檔：</span><span class="sxs-lookup"><span data-stu-id="e04f3-168">We have provided three sample cost profiles based on amount of traffic you expect to get:</span></span>

* <span data-ttu-id="e04f3-169">[小型][small-pricing]：在此設定檔中，我們會使用單一 `Standard S1` Web 應用程式，來裝載網站、Azure Bot 服務的免費層、單一 `Basic` Azure 搜尋服務及 `Standard S2` SQL Database。</span><span class="sxs-lookup"><span data-stu-id="e04f3-169">[Small][small-pricing]: In this profile, we're using a single `Standard S1` Web App to host the website, the free tier of the Azure Bot service, a single `Basic` Azure Search service, and a `Standard S2` SQL Database.</span></span>
* <span data-ttu-id="e04f3-170">[中型][medium-pricing]：在此我們會將 Web 應用程式相應增加為兩個 `Standard S3` 層執行個體、將搜尋服務升級為 `Standard S1` 層，以及使用 `Standard S6` SQL Database。</span><span class="sxs-lookup"><span data-stu-id="e04f3-170">[Medium][medium-pricing]: Here we are scaling up the Web App to two instances of the `Standard S3` tier, upgrading the Search Service to a `Standard S1` tier, and using a `Standard S6` SQL Database.</span></span>
* <span data-ttu-id="e04f3-171">[大型][large-pricing]：在最大的設定檔中，我們會使用四個 `Premium P2V2` Web 應用程式執行個體、將 Azure Bot 服務升級為 `Standard S1` 層 (進階通道中有 1.000.000 則訊息)、使用 2 單位的 `Standard S3` Azure 搜尋服務以及 `Premium P6` SQL Database。</span><span class="sxs-lookup"><span data-stu-id="e04f3-171">[Large][large-pricing]: In the largest profile, we use four instances of a `Premium P2V2` Web App, upgrade the Azure Bot service to the `Standard S1` tier (with 1.000.000 messages in Premium channels), use 2 units of the `Standard S3` Azure Search service, and a `Premium P6` SQL Database.</span></span>

## <a name="related-resources"></a><span data-ttu-id="e04f3-172">相關資源</span><span class="sxs-lookup"><span data-stu-id="e04f3-172">Related resources</span></span>

<span data-ttu-id="e04f3-173">若要深入了解 Azure 搜尋服務，請造訪[文件中心][docs-search]，查看[範例][search-samples]，或觀看完全成熟的運作中[示範網站][search-demo]。</span><span class="sxs-lookup"><span data-stu-id="e04f3-173">To learn more about Azure Search, visit the [documentation center][docs-search], check out the [samples][search-samples], or see a full fledged [demo site][search-demo] in action.</span></span>

<!-- links -->
[architecture]: ./media/architecture-ecommerce-search.png
[docs-sql-fts]: /sql/relational-databases/search/query-with-full-text-search
[docs-search]: /azure/search/search-what-is-azure-search
[docs-sql-database]: /azure/sql-database/sql-database-technical-overview
[docs-webapps]: /azure/app-service/app-service-web-overview
[docs-botservice]: /azure/bot-service/
[docs-cognitive]: /azure/cognitive-services/
[apache-lucene]: https://lucene.apache.org/
[elastic-marketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/elastic.elasticsearch
[end-to-end-walkthrough]: https://github.com/Azure/fta-customerfacingapps/tree/master/ecommerce/articles
[search-sla]: https://go.microsoft.com/fwlink/?LinkId=716855
[search-tier]: /azure/search/search-sku-tier
[search-capacity]: /azure/search/search-capacity-planning
[search-security]: /azure/search/search-security-overview
[search-analysis]: /azure/search/search-traffic-analytics
[search-languages]: /rest/api/searchservice/language-support
[search-analyzers]: /rest/api/searchservice/custom-analyzers-in-azure-search
[search-scoring]: /rest/api/searchservice/add-scoring-profiles-to-a-search-index
[search-samples]: https://azure.microsoft.com/resources/samples/?service=search&sort=0
[search-demo]: https://azjobsdemo.azurewebsites.net/
[small-pricing]: https://azure.com/e/db2672a55b6b4d768ef0060a8d9759bd
[medium-pricing]: https://azure.com/e/a5ad0706c9e74add811e83ef83766a1c
[large-pricing]: https://azure.com/e/57f95a898daa487795bd305599973ee6

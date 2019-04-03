---
title: 大量擷取和分析 Azure 上的新聞摘要
description: 建立一個僅使用 Azure 服務 (包括 Azure Cosmos DB 和 Azure 認知服務) 從 RSS 新聞摘要擷取和分析文字、影像、人氣和其他資料的管道。
author: njray
ms.date: 2/1/2019
ms.custom: azcat-ai, AI
social_image_url: /azure/architecture/example-scenario/ai/media/mass-ingestion-newsfeeds-architecture.png
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.openlocfilehash: e1bc636103753d474b545a6e3e9118b2ef302b34
ms.sourcegitcommit: ea97ac004c38c6b456794c1a8eef29f8d2b77d50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58489257"
---
# <a name="mass-ingestion-and-analysis-of-news-feeds-on-azure"></a><span data-ttu-id="a121b-103">大量擷取和分析 Azure 上的新聞摘要</span><span class="sxs-lookup"><span data-stu-id="a121b-103">Mass ingestion and analysis of news feeds on Azure</span></span>

<span data-ttu-id="a121b-104">此範例案例說明大量擷取和接近即時的分析，使用公開 RSS 新聞饋送功能的文件的管線。</span><span class="sxs-lookup"><span data-stu-id="a121b-104">This example scenario describes a pipeline for mass ingestion and near real-time analysis of documents using public RSS news feeds.</span></span>  <span data-ttu-id="a121b-105">它會使用 Azure 認知服務提供有用的深入解析，包括文字翻譯、 臉部辨識和情感偵測。</span><span class="sxs-lookup"><span data-stu-id="a121b-105">It uses Azure Cognitive Services to offer useful insights including text translation, facial recognition, and sentiment detection.</span></span>

<span data-ttu-id="a121b-106">這個案例包含的範例[英文][english]，[俄文][russian]，以及[德文][german]新聞摘要，但您可以輕鬆地延伸至其他 RSS 摘要。</span><span class="sxs-lookup"><span data-stu-id="a121b-106">This scenario contains examples for [English][english], [Russian][russian], and [German][german] news feeds, but you can easily extend it to other RSS feeds.</span></span> <span data-ttu-id="a121b-107">為了便於部署，資料收集、 處理和分析以完全基礎 Azure 服務。</span><span class="sxs-lookup"><span data-stu-id="a121b-107">For ease of deployment, the data collection, processing, and analysis are based entirely on Azure services.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="a121b-108">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="a121b-108">Relevant use cases</span></span>

<span data-ttu-id="a121b-109">雖然此案例為基礎的 RSS 摘要的處理，它是適用於任何文件、 網站，或是會需要您的文章：</span><span class="sxs-lookup"><span data-stu-id="a121b-109">While this scenario is based on processing of RSS feeds, it's relevant to any document, website, or article where you would need to:</span></span>

* <span data-ttu-id="a121b-110">轉譯任何要選擇的語言的文字。</span><span class="sxs-lookup"><span data-stu-id="a121b-110">Translate any text to the language of choice.</span></span>
* <span data-ttu-id="a121b-111">在數位內容中尋找主要片語、 實體和使用者的情感。</span><span class="sxs-lookup"><span data-stu-id="a121b-111">Find key phrases, entities, and user sentiment in digital content.</span></span>
* <span data-ttu-id="a121b-112">偵測與數位文件相關聯的映像中的物件、 文字和地標。</span><span class="sxs-lookup"><span data-stu-id="a121b-112">Detect objects, text, and landmarks in images associated with a digital article.</span></span>
* <span data-ttu-id="a121b-113">數位內容相關聯的任何映像中，偵測到由其性別和年齡的人。</span><span class="sxs-lookup"><span data-stu-id="a121b-113">Detect people by their gender and age in any image associated with digital content.</span></span>

## <a name="architecture"></a><span data-ttu-id="a121b-114">架構</span><span class="sxs-lookup"><span data-stu-id="a121b-114">Architecture</span></span>

![][architecture]

<span data-ttu-id="a121b-115">整個解決方案的資料流程如下所示：</span><span class="sxs-lookup"><span data-stu-id="a121b-115">The data flows through the solution as follows:</span></span>

1. <span data-ttu-id="a121b-116">RSS 新聞摘要做為文件或文件從取得資料的產生器。</span><span class="sxs-lookup"><span data-stu-id="a121b-116">An RSS news feed acts as the generator that obtains data from a document or article.</span></span> <span data-ttu-id="a121b-117">比方說，與發行項中，資料通常包含標題、 摘要的新聞項目中，原始的主體，而且有時候映像。</span><span class="sxs-lookup"><span data-stu-id="a121b-117">For example, with an article, data typically includes a title, a summary of the original body of the news item, and sometimes images.</span></span>

2. <span data-ttu-id="a121b-118">產生器或擷取的程序會將文件和任何相關聯的影像插入至 Azure Cosmos DB[集合][collection]。</span><span class="sxs-lookup"><span data-stu-id="a121b-118">A generator or ingestion process inserts the article and any associated images into an Azure Cosmos DB [Collection][collection].</span></span>

3. <span data-ttu-id="a121b-119">通知會觸發 Azure Functions 儲存 CosmosDB 和文件中的映像 （如果有的話） Azure Blob 儲存體中的文章內文中的內嵌函式。</span><span class="sxs-lookup"><span data-stu-id="a121b-119">A notification triggers an ingest function in Azure Functions that stores the article text in CosmosDB and the article images (if any) in Azure Blob Storage.</span></span>  <span data-ttu-id="a121b-120">本文接著會傳遞至下一個佇列中。</span><span class="sxs-lookup"><span data-stu-id="a121b-120">The article is then passed to the next queue.</span></span>

4. <span data-ttu-id="a121b-121">Translate 函式是由佇列事件觸發。</span><span class="sxs-lookup"><span data-stu-id="a121b-121">A translate function is triggered by the queue event.</span></span> <span data-ttu-id="a121b-122">它會使用[翻譯文字 API] [ translate-text]的 Azure 認知服務來偵測語言，但如果有必要，請將轉譯和人氣、 關鍵片語和實體從中收集本文和標題。</span><span class="sxs-lookup"><span data-stu-id="a121b-122">It uses the [Translate Text API][translate-text] of Azure Cognitive Services to detect the language, translate if necessary, and collect the sentiment, key phrases, and entities from the body and the title.</span></span> <span data-ttu-id="a121b-123">然後將文件傳遞至下一個佇列。</span><span class="sxs-lookup"><span data-stu-id="a121b-123">Then it passes the article to the next queue.</span></span>

5. <span data-ttu-id="a121b-124">從已排入佇列的發行項，就會觸發偵測函式。</span><span class="sxs-lookup"><span data-stu-id="a121b-124">A detect function is triggered from the queued article.</span></span> <span data-ttu-id="a121b-125">它會使用[Computer Vision] [ vision]偵測物件、 地標和寫入的文字中相關聯的映像，然後給發行項的下一個佇列的服務。</span><span class="sxs-lookup"><span data-stu-id="a121b-125">It uses the [Computer Vision][vision] service to detect objects, landmarks, and written words in the associated image, then passes the article to the next queue.</span></span>

6. <span data-ttu-id="a121b-126">觸發臉部函式時就會觸發從已排入佇列的發行項。</span><span class="sxs-lookup"><span data-stu-id="a121b-126">A face function is triggered is triggered from the queued article.</span></span> <span data-ttu-id="a121b-127">它會使用[Azure 人臉識別 API] [ face]服務偵測所面臨的性別和年齡中相關聯的映像，然後將文件傳遞至下一個佇列。</span><span class="sxs-lookup"><span data-stu-id="a121b-127">It uses the [Azure Face API][face] service to detect faces for gender and age in the associated image, then passes the article to the next queue.</span></span>

7. <span data-ttu-id="a121b-128">所有函式完成時，會觸發此通知函式。</span><span class="sxs-lookup"><span data-stu-id="a121b-128">When all functions are complete, the notify function is triggered.</span></span> <span data-ttu-id="a121b-129">它會載入已處理的記錄，發行項，並掃描其中有任何您想要的結果。</span><span class="sxs-lookup"><span data-stu-id="a121b-129">It loads the processed records for the article and scans them for any results you want.</span></span> <span data-ttu-id="a121b-130">如果找不到，內容會標示，而且會傳送通知到您選擇的系統。</span><span class="sxs-lookup"><span data-stu-id="a121b-130">If found, the content is flagged and a notification is sent to the system of your choice.</span></span>

<span data-ttu-id="a121b-131">在每個處理步驟中，此函式會將結果寫入 Azure Cosmos DB。</span><span class="sxs-lookup"><span data-stu-id="a121b-131">At each processing step, the function writes the results to Azure Cosmos DB.</span></span> <span data-ttu-id="a121b-132">最後，可以使用資料，視需要。</span><span class="sxs-lookup"><span data-stu-id="a121b-132">Ultimately, the data can be used as desired.</span></span> <span data-ttu-id="a121b-133">例如，您可以使用它來加強業務程序，找出新的客戶，或者找出客戶滿意度問題。</span><span class="sxs-lookup"><span data-stu-id="a121b-133">For example, you can use it to enhance business processes, locate new customers, or identify customer satisfaction issues.</span></span>

### <a name="components"></a><span data-ttu-id="a121b-134">元件</span><span class="sxs-lookup"><span data-stu-id="a121b-134">Components</span></span>

<span data-ttu-id="a121b-135">此範例中使用下列 Azure 元件的清單。</span><span class="sxs-lookup"><span data-stu-id="a121b-135">The following list of Azure components is used in this example.</span></span>

* <span data-ttu-id="a121b-136">[Azure 儲存體][ storage]用來保存原始映像及影片與文章相關聯的檔案。</span><span class="sxs-lookup"><span data-stu-id="a121b-136">[Azure Storage][storage] is used to hold raw image and video files associated with an article.</span></span> <span data-ttu-id="a121b-137">次要儲存體帳戶會透過 Azure App Service，以及用來裝載 Azure 函式程式碼和記錄檔。</span><span class="sxs-lookup"><span data-stu-id="a121b-137">A secondary storage account is created with Azure App Service and is used to host the Azure Function code and logs.</span></span>

* <span data-ttu-id="a121b-138">[Azure Cosmos DB] [ cosmos-db]保留文章文字、 影像和影片追蹤資訊。</span><span class="sxs-lookup"><span data-stu-id="a121b-138">[Azure Cosmos DB][cosmos-db] holds article text, image, and video tracking information.</span></span> <span data-ttu-id="a121b-139">認知服務步驟的結果也儲存在這裡。</span><span class="sxs-lookup"><span data-stu-id="a121b-139">The results of the Cognitive Services steps are also stored here.</span></span>

* <span data-ttu-id="a121b-140">[Azure Functions] [ functions]執行函式程式碼來回應佇列的訊息，並轉換傳入的內容。</span><span class="sxs-lookup"><span data-stu-id="a121b-140">[Azure Functions][functions] executes the function code used to respond to queue messages and transform the incoming content.</span></span> <span data-ttu-id="a121b-141">[Azure App Service] [ aas]裝載函式程式碼，並依序處理記錄。</span><span class="sxs-lookup"><span data-stu-id="a121b-141">[Azure App Service][aas] hosts the function code and processes the records serially.</span></span> <span data-ttu-id="a121b-142">此案例包含五個函式：擷取、 轉換、 偵測臉部的物件，並通知。</span><span class="sxs-lookup"><span data-stu-id="a121b-142">This scenario includes five functions: Ingest, Transform, Detect Object, Face, and Notify.</span></span>

* <span data-ttu-id="a121b-143">[Azure 服務匯流排][ service-bus]裝載函式使用的 Azure 服務匯流排佇列。</span><span class="sxs-lookup"><span data-stu-id="a121b-143">[Azure Service Bus][service-bus] hosts the Azure Service Bus queues used by the functions.</span></span>

* <span data-ttu-id="a121b-144">[Azure 認知服務][ acs] AI 提供的實作為基礎的管線[Computer Vision] [ vision]服務[臉部API][face]，以及[轉譯文字][ translate-text]機器翻譯服務。</span><span class="sxs-lookup"><span data-stu-id="a121b-144">[Azure Cognitive Services][acs] provides the AI for the pipeline based on implementations of the [Computer Vision][vision] service, [Face API][face], and [Translate Text][translate-text] machine translation service.</span></span>

* <span data-ttu-id="a121b-145">[Azure Application Insights] [ aai]提供分析可協助您診斷問題，並了解您的應用程式的功能。</span><span class="sxs-lookup"><span data-stu-id="a121b-145">[Azure Application Insights][aai] provides analytics to help you diagnose issues and to understand functionality of your application.</span></span>

### <a name="alternatives"></a><span data-ttu-id="a121b-146">替代項目</span><span class="sxs-lookup"><span data-stu-id="a121b-146">Alternatives</span></span>

* <span data-ttu-id="a121b-147">而不是使用根據佇列通知和 Azure Functions 的模式，請對此資料流程中使用另一種模式。</span><span class="sxs-lookup"><span data-stu-id="a121b-147">Instead of using a pattern based on queue notification and Azure Functions, use another pattern for this data flow.</span></span> <span data-ttu-id="a121b-148">例如， [Azure 服務匯流排主題][ topics]可以用來完成此範例中的序列處理而不是以平行方式為文件的各個部分的程序。</span><span class="sxs-lookup"><span data-stu-id="a121b-148">For example, [Azure Service Bus Topics][topics] can be used to processes the various parts of the article in parallel as opposed to the serial processing done in this example.</span></span> <span data-ttu-id="a121b-149">如需詳細資訊，請比較[佇列和主題][queues-topics]。</span><span class="sxs-lookup"><span data-stu-id="a121b-149">For more information, compare [queues and topics][queues-topics].</span></span>

* <span data-ttu-id="a121b-150">使用[Azure Logic App] [ logic-app]實作函式程式碼，並實作記錄層級鎖定這類[Redlock] [ redlock] （需要平行處理，直到 Azure Cosmos DB 支援[部分的文件更新][partial])。</span><span class="sxs-lookup"><span data-stu-id="a121b-150">Use [Azure Logic App][logic-app] to implement the function code and implement record-level locking such as [Redlock][redlock] (needed for parallel processing until Azure Cosmos DB supports [partial document updates][partial]).</span></span> <span data-ttu-id="a121b-151">如需詳細資訊，[比較 Functions 和 Logic Apps][compare]。</span><span class="sxs-lookup"><span data-stu-id="a121b-151">For more information, [compare Functions and Logic Apps][compare].</span></span>

* <span data-ttu-id="a121b-152">實作此架構中使用自訂的 AI 元件，而不是現有的 Azure 服務。</span><span class="sxs-lookup"><span data-stu-id="a121b-152">Implement this architecture using customized AI components rather than existing Azure services.</span></span> <span data-ttu-id="a121b-153">比方說，擴充管線使用中的映像，而不是泛型的人員計數、 性別，偵測到特定人員的自訂的模型，並在此範例中所收集資料的保留時間。</span><span class="sxs-lookup"><span data-stu-id="a121b-153">For example, extend the pipeline using a customized model that detects certain people in an image as opposed to the generic people count, gender, and age data collected in this example.</span></span> <span data-ttu-id="a121b-154">若要使用此架構的自訂的機器學習服務或人工智慧模型，建置模型做為 RESTful 端點，讓他們可以從 Azure 函式呼叫。</span><span class="sxs-lookup"><span data-stu-id="a121b-154">To use customized machine learning or AI models with this architecture, build the models as RESTful endpoints so they can be called from Azure Functions.</span></span>

* <span data-ttu-id="a121b-155">使用不同的輸入的機制，而不是 RSS 摘要。</span><span class="sxs-lookup"><span data-stu-id="a121b-155">Use a different input mechanism instead of RSS feeds.</span></span> <span data-ttu-id="a121b-156">使用多個產生器或擷取程序摘要 Azure Cosmos DB 和 Azure 儲存體。</span><span class="sxs-lookup"><span data-stu-id="a121b-156">Use multiple generators or ingestion processes to feed Azure Cosmos DB and Azure Storage.</span></span>

## <a name="considerations"></a><span data-ttu-id="a121b-157">考量</span><span class="sxs-lookup"><span data-stu-id="a121b-157">Considerations</span></span>

<span data-ttu-id="a121b-158">為了簡單起見，此範例案例中會使用只有幾個可用的 Api 和 Azure 認知服務的服務。</span><span class="sxs-lookup"><span data-stu-id="a121b-158">For simplicity, this example scenario uses only a few of the available APIs and services from Azure Cognitive Services.</span></span> <span data-ttu-id="a121b-159">比方說，在映像中的文字可以使用分析[文字分析 API][text-analytics]。</span><span class="sxs-lookup"><span data-stu-id="a121b-159">For example, text in images can be analyzed using the [Text Analytics API][text-analytics].</span></span> <span data-ttu-id="a121b-160">假設在此案例中的目標語言是英文，但是您可以變更任何的輸入[支援的語言][ language]您選擇。</span><span class="sxs-lookup"><span data-stu-id="a121b-160">The target language in this scenario is assumed to be English, but you can change the input to any [supported language][language] of your choice.</span></span>

### <a name="scalability"></a><span data-ttu-id="a121b-161">延展性</span><span class="sxs-lookup"><span data-stu-id="a121b-161">Scalability</span></span>

<span data-ttu-id="a121b-162">Azure Functions 縮放比例取決於[主控方案][ plan]您使用。</span><span class="sxs-lookup"><span data-stu-id="a121b-162">Azure Functions scaling depends on the [hosting plan][plan] you use.</span></span> <span data-ttu-id="a121b-163">這個解決方案假設[耗用量計劃][plan-c]，哪些計算電源會自動配置給函式時所需。</span><span class="sxs-lookup"><span data-stu-id="a121b-163">This solution assumes a [Consumption plan][plan-c], in which compute power is automatically allocated to the functions when required.</span></span> <span data-ttu-id="a121b-164">只有當您的函式執行時需要付費。</span><span class="sxs-lookup"><span data-stu-id="a121b-164">You pay only when your functions are running.</span></span> <span data-ttu-id="a121b-165">另一個選項是使用[Azure App Service] [ plan-aas]計劃，可讓您配置不同的資源量的各層之間調整。</span><span class="sxs-lookup"><span data-stu-id="a121b-165">Another option is to use an [Azure App Service][plan-aas] plan, which allows you to scale between tiers to allocate a different amount of resources.</span></span>

<span data-ttu-id="a121b-166">使用 Azure Cosmos DB，關鍵在於您的工作負載大致平均分配夠大幾部[資料分割索引鍵][keys]。</span><span class="sxs-lookup"><span data-stu-id="a121b-166">With Azure Cosmos DB, the key is to distribute your workload roughly evenly among a sufficiently large number of [partition keys][keys].</span></span> <span data-ttu-id="a121b-167">沒有任何限制的容器可以儲存的資料總量或總數量[輸送量][ throughput]可支援容器。</span><span class="sxs-lookup"><span data-stu-id="a121b-167">There's no limit to the total amount of data that a container can store or to the total amount of [throughput][throughput] that a container can support.</span></span>

### <a name="management-and-logging"></a><span data-ttu-id="a121b-168">管理和記錄</span><span class="sxs-lookup"><span data-stu-id="a121b-168">Management and logging</span></span>

<span data-ttu-id="a121b-169">此解決方案會使用[Application Insights] [ aai]收集效能和記錄資訊。</span><span class="sxs-lookup"><span data-stu-id="a121b-169">This solution uses [Application Insights][aai] to collect performance and logging information.</span></span> <span data-ttu-id="a121b-170">與此部署所需的其他服務相同的資源群組中的部署建立的 Application Insights 執行個體。</span><span class="sxs-lookup"><span data-stu-id="a121b-170">An instance of Application Insights is created with the deployment in the same resource group as the other services needed for this deployment.</span></span>

<span data-ttu-id="a121b-171">若要檢視解決方案所產生的記錄檔：</span><span class="sxs-lookup"><span data-stu-id="a121b-171">To view the logs generated by the solution:</span></span>

1. <span data-ttu-id="a121b-172">移至[Azure 入口網站][ portal]並瀏覽至建立部署的資源群組。</span><span class="sxs-lookup"><span data-stu-id="a121b-172">Go to [Azure portal][portal] and navigate to the resource group created for the deployment.</span></span>

2. <span data-ttu-id="a121b-173">按一下  **Application Insights**執行個體。</span><span class="sxs-lookup"><span data-stu-id="a121b-173">Click the **Application Insights** instance.</span></span>

3. <span data-ttu-id="a121b-174">從**Application Insights**區段中，瀏覽至**調查\\搜尋**和搜尋資料。</span><span class="sxs-lookup"><span data-stu-id="a121b-174">From the **Application Insights** section, navigate to **Investigate\\Search** and search the data.</span></span>

### <a name="security"></a><span data-ttu-id="a121b-175">安全性</span><span class="sxs-lookup"><span data-stu-id="a121b-175">Security</span></span>

<span data-ttu-id="a121b-176">Azure Cosmos DB 會使用安全的連線和 C 透過共用的存取簽章\#Microsoft 所提供的 SDK。</span><span class="sxs-lookup"><span data-stu-id="a121b-176">Azure Cosmos DB uses a secured connection and shared access signature through the C\# SDK provided by Microsoft.</span></span> <span data-ttu-id="a121b-177">沒有其他對外公開介面區。</span><span class="sxs-lookup"><span data-stu-id="a121b-177">There are no other externally facing surface areas.</span></span> <span data-ttu-id="a121b-178">深入了解安全性[最佳做法][ db-practices]適用於 Azure Cosmos DB。</span><span class="sxs-lookup"><span data-stu-id="a121b-178">Learn more about security [best practices][db-practices] for Azure Cosmos DB.</span></span>

## <a name="pricing"></a><span data-ttu-id="a121b-179">價格</span><span class="sxs-lookup"><span data-stu-id="a121b-179">Pricing</span></span>

<span data-ttu-id="a121b-180">要保留這項部署的預估每日成本大約是\$20 美國不含系統中移動的資料。</span><span class="sxs-lookup"><span data-stu-id="a121b-180">The estimated daily cost to keep this deployment available is approximately \$20 U.S. with no data moving through the system.</span></span>

<span data-ttu-id="a121b-181">Azure Cosmos DB 是功能強大，但會產生最大[成本][ db-cost]此部署中。</span><span class="sxs-lookup"><span data-stu-id="a121b-181">Azure Cosmos DB is powerful but incurs the greatest [cost][db-cost] in this deployment.</span></span> <span data-ttu-id="a121b-182">您可以使用另一個儲存體解決方案，重構提供的 Azure Functions 程式碼。</span><span class="sxs-lookup"><span data-stu-id="a121b-182">You can use another storage solution by refactoring the Azure Functions code provided.</span></span>

<span data-ttu-id="a121b-183">價格取決於不同的 Azure Functions[計劃][ function-plan]時執行。</span><span class="sxs-lookup"><span data-stu-id="a121b-183">Pricing for Azure Functions varies depending on the [plan][function-plan] it runs in.</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="a121b-184">部署案例</span><span class="sxs-lookup"><span data-stu-id="a121b-184">Deploy the scenario</span></span>

> [!NOTE]
> <span data-ttu-id="a121b-185">您必須具有現有的 Azure 帳戶。</span><span class="sxs-lookup"><span data-stu-id="a121b-185">You must have an existing Azure account.</span></span> <span data-ttu-id="a121b-186">如果您沒有 Azure 訂用帳戶，請在開始前建立 [[免費帳戶]][free]。</span><span class="sxs-lookup"><span data-stu-id="a121b-186">If you don't have an Azure subscription, create a [free account][free] before you begin.</span></span>

<span data-ttu-id="a121b-187">此案例中的所有程式碼位於[GitHub] [ github]存放庫。</span><span class="sxs-lookup"><span data-stu-id="a121b-187">All the code for this scenario is available in the [GitHub][github] repository.</span></span> <span data-ttu-id="a121b-188">此存放庫包含用來建置摘要這段示範影片的管線產生器應用程式的原始程式碼。</span><span class="sxs-lookup"><span data-stu-id="a121b-188">This repository contains the source code used to build the generator application that feeds the pipeline for this demo.</span></span>

[architecture]: ./media/mass-ingestion-newsfeeds-architecture.png
[aai]: /azure/azure-monitor/app/app-insights-overview
[aas]: https://azure.microsoft.com/try/app-service/
[acs]: https://azure.microsoft.com/services/cognitive-services/directory/
[collection]: /rest/api/cosmos-db/collections
[compare]: /azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs#compare-azure-functions-and-azure-logic-apps
[cosmos-db]: /azure/cosmos-db/introduction
[db-cost]: https://azure.microsoft.com/pricing/details/cosmos-db/
[db-practices]: /azure/cosmos-db/database-security
[db-collection]: /azure/cosmos-db/databases-containers-items
[english]: https://www.nasa.gov/rss/dyn/breaking_news.rss
[face]: /azure/cognitive-services/face/overview
[free]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[functions]: /azure/azure-functions/functions-overview
[function-plan]: /azure/azure-functions/functions-scale
[german]: http://www.bamf.de/SiteGlobals/Functions/RSS/DE/Feed/RSSNewsfeed_Meldungen
[github]: https://github.com/Azure/cognitive-services
[keys]: /azure/cosmos-db/partition-data
[language]: /azure/cognitive-services/translator/reference/v3-0-languages
[logic-app]: /azure/logic-apps/logic-apps-overview
[queues-topics]: /azure/service-bus-messaging/service-bus-queues-topics-subscriptions
[partial]: https://feedback.azure.com/forums/263030-azure-cosmos-db/suggestions/6693091-be-able-to-do-partial-updates-on-document
[plan]: /azure/azure-functions/functions-scale
[plan-aas]: /azure/azure-functions/functions-scale#app-service-plan
[plan-c]: /azure/azure-functions/functions-scale#consumption-plan
[portal]: http://portal.azure.com
[redlock]: https://redis.io/topics/distlock
[russian]: http://government.ru/all/rss/
[service-bus]: /azure/service-bus-messaging/
[storage]: /azure/storage/common/storage-account-overview 
[throughput]: /azure/cosmos-db/scaling-throughput
[topics]: /azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions
[text-analytics]: /azure/cognitive-services/text-analytics/
[translate-text]: /azure/cognitive-services/translator/translator-info-overview
[vision]: /azure/cognitive-services/computer-vision/home

---
title: 保險理賠映像分類
titleSuffix: Azure Example Scenarios
description: 將影像處理建置到您的 Azure 應用程式。
author: david-stanford
ms.date: 07/05/2018
ms.openlocfilehash: 12dd197c6df4a8d7a90a09436d86ce4a9e5ccc72
ms.sourcegitcommit: bb7fcffbb41e2c26a26f8781df32825eb60df70c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2018
ms.locfileid: "53643440"
---
# <a name="image-classification-for-insurance-claims-on-azure"></a><span data-ttu-id="d3249-103">Azure 上的保險理賠映像分類</span><span class="sxs-lookup"><span data-stu-id="d3249-103">Image classification for insurance claims on Azure</span></span>

<span data-ttu-id="d3249-104">此案例與需要處理影像的企業有關。</span><span class="sxs-lookup"><span data-stu-id="d3249-104">This scenario is relevant for businesses that need to process images.</span></span>

<span data-ttu-id="d3249-105">潛在的應用程式包含分類潮流網站的映像、分析保險理賠的文字和映像，或了解來自遊戲螢幕擷取畫面的遙測資料。</span><span class="sxs-lookup"><span data-stu-id="d3249-105">Potential applications include classifying images for a fashion website, analyzing text and images for insurance claims, or understanding telemetry data from game screenshots.</span></span> <span data-ttu-id="d3249-106">在過去，公司需要開發機器學習服務模型的專業知識、定型模型，最終透過其自訂程序執行映像，以取得映像中的資料。</span><span class="sxs-lookup"><span data-stu-id="d3249-106">Traditionally, companies would need to develop expertise in machine learning models, train the models, and finally run the images through their custom process to get the data out of the images.</span></span>

<span data-ttu-id="d3249-107">藉由使用 Azure 服務 (例如電腦視覺 API 和 Azure Functions)，公司可以消除管理個別伺服器的需求，同時降低成本及利用 Microsoft 透過認知服務針對影像處理所開發的專業知識。</span><span class="sxs-lookup"><span data-stu-id="d3249-107">By using Azure services such as the Computer Vision API and Azure Functions, companies can eliminate the need to manage individual servers, while reducing costs and leveraging the expertise that Microsoft has already developed around processing images with Cognitive Services.</span></span> <span data-ttu-id="d3249-108">這個範例案例特別說明映像處理的使用案例。</span><span class="sxs-lookup"><span data-stu-id="d3249-108">This example scenario specifically addresses an image-processing use case.</span></span> <span data-ttu-id="d3249-109">如果您有不同的 AI 需求，請考慮整套的[認知服務](/azure/#pivot=products&panel=ai)。</span><span class="sxs-lookup"><span data-stu-id="d3249-109">If you have different AI needs, consider the full suite of [Cognitive Services](/azure/#pivot=products&panel=ai).</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="d3249-110">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="d3249-110">Relevant use cases</span></span>

<span data-ttu-id="d3249-111">其他相關的使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="d3249-111">Other relevant use cases include:</span></span>

- <span data-ttu-id="d3249-112">分類潮流網站上的映像。</span><span class="sxs-lookup"><span data-stu-id="d3249-112">Classifying images on a fashion website.</span></span>
- <span data-ttu-id="d3249-113">分類來自遊戲螢幕擷取畫面的遙測資料。</span><span class="sxs-lookup"><span data-stu-id="d3249-113">Classifying telemetry data from screenshots of games.</span></span>

## <a name="architecture"></a><span data-ttu-id="d3249-114">架構</span><span class="sxs-lookup"><span data-stu-id="d3249-114">Architecture</span></span>

![影像分類的架構][architecture]

<span data-ttu-id="d3249-116">此案例涵蓋了 Web 或行動裝置應用程式的後端元件。</span><span class="sxs-lookup"><span data-stu-id="d3249-116">This scenario covers the back-end components of a web or mobile application.</span></span> <span data-ttu-id="d3249-117">整個案例的資料流程如下所示：</span><span class="sxs-lookup"><span data-stu-id="d3249-117">Data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="d3249-118">使用 Azure Functions 建置 API 層。</span><span class="sxs-lookup"><span data-stu-id="d3249-118">The API layer is built using Azure Functions.</span></span> <span data-ttu-id="d3249-119">這些 API 可讓應用程式上傳映像，並且從 Cosmos DB 擷取資料。</span><span class="sxs-lookup"><span data-stu-id="d3249-119">These APIs enable the application to upload images and retrieve data from Cosmos DB.</span></span>
2. <span data-ttu-id="d3249-120">透過 API 呼叫來上傳映像時，映像會儲存在 Blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="d3249-120">When an image is uploaded via an API call, it's stored in Blob storage.</span></span>
3. <span data-ttu-id="d3249-121">將新檔案新增至 Blob 儲存體，會觸發事件方格通知傳送至 Azure Function。</span><span class="sxs-lookup"><span data-stu-id="d3249-121">Adding new files to Blob storage triggers an Event Grid notification to be sent to an Azure Function.</span></span>
4. <span data-ttu-id="d3249-122">Azure Functions 會將新上傳檔案的連結傳送至電腦視覺 API 以進行分析。</span><span class="sxs-lookup"><span data-stu-id="d3249-122">Azure Functions sends a link to the newly uploaded file to the Computer Vision API to analyze.</span></span>
5. <span data-ttu-id="d3249-123">一旦資料從電腦視覺 API 傳回，Azure Functions 會在 Cosmos DB 中製作輸入項，一併保存影像中繼資料與分析結果。</span><span class="sxs-lookup"><span data-stu-id="d3249-123">Once the data has been returned from the Computer Vision API, Azure Functions makes an entry in Cosmos DB to persist the results of the analysis along with the image metadata.</span></span>

### <a name="components"></a><span data-ttu-id="d3249-124">元件</span><span class="sxs-lookup"><span data-stu-id="d3249-124">Components</span></span>

- <span data-ttu-id="d3249-125">[電腦視覺 API](/azure/cognitive-services/computer-vision/home) 是認知服務套件的一部分，用來擷取每個影像的資訊。</span><span class="sxs-lookup"><span data-stu-id="d3249-125">[Computer Vision API](/azure/cognitive-services/computer-vision/home) is part of the Cognitive Services suite and is used to retrieve information about each image.</span></span>
- <span data-ttu-id="d3249-126">[Azure Functions](/azure/azure-functions/functions-overview) 提供 Web 應用程式的後端 API，以及已上傳影像的事件處理。</span><span class="sxs-lookup"><span data-stu-id="d3249-126">[Azure Functions](/azure/azure-functions/functions-overview) provides the back-end API for the web application, as well as the event processing for uploaded images.</span></span>
- <span data-ttu-id="d3249-127">[事件方格](/azure/event-grid/overview)會在新影像上傳至 Blob 儲存體時觸發事件。</span><span class="sxs-lookup"><span data-stu-id="d3249-127">[Event Grid](/azure/event-grid/overview) triggers an event when a new image is uploaded to blob storage.</span></span> <span data-ttu-id="d3249-128">然後使用 Azure functions 處理映像。</span><span class="sxs-lookup"><span data-stu-id="d3249-128">The image is then processed with Azure functions.</span></span>
- <span data-ttu-id="d3249-129">[Blob 儲存體](/azure/storage/blobs/storage-blobs-introduction)會儲存上傳至 Web 應用程式的所有影像檔案，以及 Web 應用程式取用的任何靜態檔案。</span><span class="sxs-lookup"><span data-stu-id="d3249-129">[Blob storage](/azure/storage/blobs/storage-blobs-introduction) stores all of the image files that are uploaded into the web application, as well any static files that the web application consumes.</span></span>
- <span data-ttu-id="d3249-130">[Cosmos DB](/azure/cosmos-db/introduction) 會儲存每個已上傳影像的中繼資料，包括從電腦視覺 API 處理的結果。</span><span class="sxs-lookup"><span data-stu-id="d3249-130">[Cosmos DB](/azure/cosmos-db/introduction) stores metadata about each image that is uploaded, including the results of the processing from Computer Vision API.</span></span>

## <a name="alternatives"></a><span data-ttu-id="d3249-131">替代項目</span><span class="sxs-lookup"><span data-stu-id="d3249-131">Alternatives</span></span>

- <span data-ttu-id="d3249-132">[自訂視覺服務](/azure/cognitive-services/custom-vision-service/home)。</span><span class="sxs-lookup"><span data-stu-id="d3249-132">[Custom Vision Service](/azure/cognitive-services/custom-vision-service/home).</span></span> <span data-ttu-id="d3249-133">電腦視覺 API 會傳回一組[分類法型分類][cv-categories]。</span><span class="sxs-lookup"><span data-stu-id="d3249-133">The Computer Vision API returns a set of [taxonomy-based categories][cv-categories].</span></span> <span data-ttu-id="d3249-134">如果您需要處理並非由電腦視覺 API 所傳回的資訊，請考慮自訂視覺服務，它可讓您建置自訂映像分類器。</span><span class="sxs-lookup"><span data-stu-id="d3249-134">If you need to process information that isn't returned by the Computer Vision API, consider the Custom Vision Service, which lets you build custom image classifiers.</span></span>
- <span data-ttu-id="d3249-135">[Azure 搜尋服務](/azure/search/search-what-is-azure-search)。</span><span class="sxs-lookup"><span data-stu-id="d3249-135">[Azure Search](/azure/search/search-what-is-azure-search).</span></span> <span data-ttu-id="d3249-136">如果您的使用案例牽涉到查詢中繼資料以尋找符合特定準則的映像，請考慮使用 Azure 搜尋服務。</span><span class="sxs-lookup"><span data-stu-id="d3249-136">If your use case involves querying the metadata to find images that meet specific criteria, consider using Azure Search.</span></span> <span data-ttu-id="d3249-137">[認知搜尋](/azure/search/cognitive-search-concept-intro) (目前處於預覽狀態) 與此工作流程緊密整合。</span><span class="sxs-lookup"><span data-stu-id="d3249-137">Currently in preview, [Cognitive search](/azure/search/cognitive-search-concept-intro) seamlessly integrates this workflow.</span></span>

## <a name="considerations"></a><span data-ttu-id="d3249-138">考量</span><span class="sxs-lookup"><span data-stu-id="d3249-138">Considerations</span></span>

### <a name="scalability"></a><span data-ttu-id="d3249-139">延展性</span><span class="sxs-lookup"><span data-stu-id="d3249-139">Scalability</span></span>

<span data-ttu-id="d3249-140">此範例案例中使用的大部分元件都是會自動調整的受控服務。</span><span class="sxs-lookup"><span data-stu-id="d3249-140">The majority of the components used in this example scenario are managed services that will automatically scale.</span></span> <span data-ttu-id="d3249-141">幾個值得注意的例外狀況：Azure Functions 的上限為 200 個執行個體。</span><span class="sxs-lookup"><span data-stu-id="d3249-141">A couple notable exceptions: Azure Functions has a limit of a maximum of 200 instances.</span></span> <span data-ttu-id="d3249-142">如果您需要調整超過此上限，請考慮多個區域或應用程式方案。</span><span class="sxs-lookup"><span data-stu-id="d3249-142">If you need to scale beyond this limit, consider multiple regions or app plans.</span></span>

<span data-ttu-id="d3249-143">Cosmos DB 不會依據佈建要求單位 (RU) 自動調整。</span><span class="sxs-lookup"><span data-stu-id="d3249-143">Cosmos DB doesn’t autoscale in terms of provisioned request units (RUs).</span></span> <span data-ttu-id="d3249-144">如需評估需求的指引，請參閱我們文件中的[要求單位](/azure/cosmos-db/request-units)。</span><span class="sxs-lookup"><span data-stu-id="d3249-144">For guidance on estimating your requirements see [request units](/azure/cosmos-db/request-units) in our documentation.</span></span> <span data-ttu-id="d3249-145">若要充分利用 Cosmos DB 中的調整，請探索[分割區索引鍵](/azure/cosmos-db/partition-data)。</span><span class="sxs-lookup"><span data-stu-id="d3249-145">To fully take advantage of the scaling in Cosmos DB, understand how [partition keys](/azure/cosmos-db/partition-data) work in CosmosDB.</span></span>

<span data-ttu-id="d3249-146">NoSQL 資料庫會經常交換可用性、延展性及分割區的一致性 (CAP 定理的概念)。</span><span class="sxs-lookup"><span data-stu-id="d3249-146">NoSQL databases frequently trade consistency (in the sense of the CAP theorem) for availability, scalability, and partitioning.</span></span> <span data-ttu-id="d3249-147">在此範例案例中，會使用索引鍵-值資料模型，由於大部分作業定義為不可部分完成，所以不太需要交易一致性。</span><span class="sxs-lookup"><span data-stu-id="d3249-147">In this example scenario, a key-value data model is used and transaction consistency is rarely needed as most operations are by definition atomic.</span></span> <span data-ttu-id="d3249-148">[選擇正確的資料存放區](../../guide/technology-choices/data-store-overview.md)的額外指引可於 Azure 架構中心找到。</span><span class="sxs-lookup"><span data-stu-id="d3249-148">Additional guidance to [Choose the right data store](../../guide/technology-choices/data-store-overview.md) is available in the Azure Architecture Center.</span></span> <span data-ttu-id="d3249-149">如果您的實作需要高度一致性，您可以在 CosmosDB 中[選擇您的一致性層級](/azure/cosmos-db/consistency-levels)。</span><span class="sxs-lookup"><span data-stu-id="d3249-149">If your implementation requires high consistency, you can [choose your consistency level](/azure/cosmos-db/consistency-levels) in CosmosDB.</span></span>

<span data-ttu-id="d3249-150">如需設計可調整解決方案的一般指引，請參閱 Azure Architecture Center 中的[延展性檢查清單][scalability]。</span><span class="sxs-lookup"><span data-stu-id="d3249-150">For general guidance on designing scalable solutions, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="d3249-151">安全性</span><span class="sxs-lookup"><span data-stu-id="d3249-151">Security</span></span>

<span data-ttu-id="d3249-152">[Azure 茲玵的受控識別][msi]是用來提供帳戶內部其他資源的存取權，然後指派給您的 Azure Functions。</span><span class="sxs-lookup"><span data-stu-id="d3249-152">[Managed identities for Azure resources][msi] are used to provide access to other resources internal to your account and then assigned to your Azure Functions.</span></span> <span data-ttu-id="d3249-153">只允許以這些身分識別存取必要資源，確保沒有其他項目公開到您的函式 (而且可能公開給您的客戶)。</span><span class="sxs-lookup"><span data-stu-id="d3249-153">Only allow access to the requisite resources in those identities to ensure that nothing extra is exposed to your functions (and potentially to your customers).</span></span>

<span data-ttu-id="d3249-154">如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。</span><span class="sxs-lookup"><span data-stu-id="d3249-154">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="d3249-155">災害復原</span><span class="sxs-lookup"><span data-stu-id="d3249-155">Resiliency</span></span>

<span data-ttu-id="d3249-156">此案例中的所有元件都是受控元件，所以它們在區域層級都會自動復原。</span><span class="sxs-lookup"><span data-stu-id="d3249-156">All of the components in this scenario are managed, so at a regional level they are all resilient automatically.</span></span>

<span data-ttu-id="d3249-157">如需設計彈性解決方案的一般指引，請參閱[為 Azure 設計有彈性的應用程式][resiliency]。</span><span class="sxs-lookup"><span data-stu-id="d3249-157">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="pricing"></a><span data-ttu-id="d3249-158">價格</span><span class="sxs-lookup"><span data-stu-id="d3249-158">Pricing</span></span>

<span data-ttu-id="d3249-159">為了探索執行此案例的成本，所有服務會在成本計算機中預先設定。</span><span class="sxs-lookup"><span data-stu-id="d3249-159">To explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="d3249-160">若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的流量。</span><span class="sxs-lookup"><span data-stu-id="d3249-160">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="d3249-161">我們根據流量 (假設所有映像的大小是 100 kb)，提供了三個範例成本設定檔：</span><span class="sxs-lookup"><span data-stu-id="d3249-161">We have provided three sample cost profiles based on amount of traffic (we assume all images are 100 kb in size):</span></span>

- <span data-ttu-id="d3249-162">[小型][small-pricing]：這個定價範例適用於每月處理 &lt; 5000 個映像。</span><span class="sxs-lookup"><span data-stu-id="d3249-162">[Small][small-pricing]: this pricing example correlates to processing &lt; 5000 images a month.</span></span>
- <span data-ttu-id="d3249-163">[中型][medium-pricing]：這個定價範例適用於每月處理 500,000 個映像。</span><span class="sxs-lookup"><span data-stu-id="d3249-163">[Medium][medium-pricing]: this pricing example correlates to processing 500,000 images a month.</span></span>
- <span data-ttu-id="d3249-164">[大型][large-pricing]：這個定價範例適用於每月處理 5,000 萬個映像。</span><span class="sxs-lookup"><span data-stu-id="d3249-164">[Large][large-pricing]: this pricing example correlates to processing 50 million images a month.</span></span>

## <a name="related-resources"></a><span data-ttu-id="d3249-165">相關資源</span><span class="sxs-lookup"><span data-stu-id="d3249-165">Related resources</span></span>

<span data-ttu-id="d3249-166">如需引導式學習路徑，請參閱[在 Azure 中建置無伺服器 Web 應用程式][serverless]。</span><span class="sxs-lookup"><span data-stu-id="d3249-166">For a guided learning path, see [Build a serverless web app in Azure][serverless].</span></span>

<span data-ttu-id="d3249-167">在生產環境中部署此範例案例之前，請檢閱[最佳化 Azure Functions 的效能和可靠性][functions-best-practices]的建議做法。</span><span class="sxs-lookup"><span data-stu-id="d3249-167">Before deploying this example scenario in a production environment, review recommended practices for [optimizing the performance and reliability of Azure Functions][functions-best-practices].</span></span>

<!-- links -->
[architecture]: ./media/architecture-intelligent-apps-image-processing.png
[small-pricing]: https://azure.com/e/f9b59d238b43423683db73f4a31dc380
[medium-pricing]: https://azure.com/e/7c7fc474db344b87aae93bc29ae27108
[large-pricing]: https://azure.com/e/cbadbca30f8640d6a061f8457a74ba7d
[cognitive-search]: /azure/search/cognitive-search-concept-intro
[serverless]: /azure/functions/tutorial-static-website-serverless-api-with-database
[cv-categories]: /azure/cognitive-services/computer-vision/home#the-86-category-concept
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[functions-best-practices]: /azure/azure-functions/functions-best-practices
[msi]: /azure/app-service/app-service-managed-service-identity

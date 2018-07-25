---
title: Azure 上的保險理賠映像分類
description: 經過證明的案例，可以將映像處理建置到您的 Azure 應用程式。
author: david-stanford
ms.date: 07/05/2018
ms.openlocfilehash: 361a88234fd9ed918ab7664893f86666b4328b8c
ms.sourcegitcommit: 71cbef121c40ef36e2d6e3a088cb85c4260599b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060824"
---
# <a name="image-classification-for-insurance-claims-on-azure"></a><span data-ttu-id="28b6a-103">Azure 上的保險理賠映像分類</span><span class="sxs-lookup"><span data-stu-id="28b6a-103">Image classification for insurance claims on Azure</span></span>

<span data-ttu-id="28b6a-104">此範例案例適用於需要處理映像的企業。</span><span class="sxs-lookup"><span data-stu-id="28b6a-104">This example scenario is applicable for businesses that need to process images.</span></span>

<span data-ttu-id="28b6a-105">潛在的應用程式包含分類潮流網站的映像、分析保險理賠的文字和映像，或了解來自遊戲螢幕擷取畫面的遙測資料。</span><span class="sxs-lookup"><span data-stu-id="28b6a-105">Potential applications include classifying images for a fashion website, analyzing text and images for insurance claims, or understanding telemetry data from game screenshots.</span></span> <span data-ttu-id="28b6a-106">在過去，公司需要開發機器學習服務模型的專業知識、定型模型，最終透過其自訂程序執行映像，以取得映像中的資料。</span><span class="sxs-lookup"><span data-stu-id="28b6a-106">Traditionally, companies would need to develop expertise in machine learning models, train the models, and finally run the images through their custom process to get the data out of the images.</span></span>

<span data-ttu-id="28b6a-107">藉由使用 Azure 服務 (例如電腦視覺 API 和 Azure Functions)，公司可以消除管理個別伺服器的需求，同時減少成本及利用 Microsoft 已透過認知服務針對處理映像開發的專業知識。</span><span class="sxs-lookup"><span data-stu-id="28b6a-107">By using Azure services such as the Computer Vision API and Azure Functions, companies can eliminate the need to manage individual servers, while reducing costs and leveraging the expertise that Microsoft has already developed around processing images with Cognitive services.</span></span> <span data-ttu-id="28b6a-108">這個案例特別說明映像處理案例。</span><span class="sxs-lookup"><span data-stu-id="28b6a-108">This scenario specifically addresses an image processing scenario.</span></span> <span data-ttu-id="28b6a-109">如果您有不同的 AI 需求，請考慮整套的[認知服務][cognitive-docs]。</span><span class="sxs-lookup"><span data-stu-id="28b6a-109">If you have different AI needs, consider the full suite of [Cognitive Services][cognitive-docs].</span></span>

## <a name="related-use-cases"></a><span data-ttu-id="28b6a-110">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="28b6a-110">Related use cases</span></span>

<span data-ttu-id="28b6a-111">請針對下列使用案例考慮此案例：</span><span class="sxs-lookup"><span data-stu-id="28b6a-111">Consider this scenario for the following use cases:</span></span>

* <span data-ttu-id="28b6a-112">分類潮流網站上的映像。</span><span class="sxs-lookup"><span data-stu-id="28b6a-112">Classify images on a fashion website.</span></span>
* <span data-ttu-id="28b6a-113">分類來自遊戲螢幕擷取畫面的遙測資料。</span><span class="sxs-lookup"><span data-stu-id="28b6a-113">Classify telemetry data from screenshots of games.</span></span>

## <a name="architecture"></a><span data-ttu-id="28b6a-114">架構</span><span class="sxs-lookup"><span data-stu-id="28b6a-114">Architecture</span></span>

![智慧型應用程式架構 - 電腦視覺][architecture-computer-vision]

<span data-ttu-id="28b6a-116">此案例涵蓋了 Web 或行動裝置應用程式的後端元件。</span><span class="sxs-lookup"><span data-stu-id="28b6a-116">This scenario covers the back-end components of a web or mobile application.</span></span> <span data-ttu-id="28b6a-117">整個案例的資料流程如下所示：</span><span class="sxs-lookup"><span data-stu-id="28b6a-117">Data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="28b6a-118">Azure Functions 作為 API 層。</span><span class="sxs-lookup"><span data-stu-id="28b6a-118">Azure Functions acts as the API layer.</span></span> <span data-ttu-id="28b6a-119">這些 API 可讓應用程式上傳映像，並且從 Cosmos DB 擷取資料。</span><span class="sxs-lookup"><span data-stu-id="28b6a-119">These APIs enable the application to upload images and retrieve data from Cosmos DB.</span></span>

2. <span data-ttu-id="28b6a-120">透過 API 呼叫來上傳映像時，映像會儲存在 Blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="28b6a-120">When an image is uploaded via an API call, it's stored in Blob storage.</span></span>

3. <span data-ttu-id="28b6a-121">將新檔案新增至 Blob 儲存體，會觸發 EventGrid 通知傳送至 Azure Function。</span><span class="sxs-lookup"><span data-stu-id="28b6a-121">Adding new files to Blob storage triggers an EventGrid notification to be sent to an Azure Function.</span></span>

4. <span data-ttu-id="28b6a-122">Azure Functions 會將新上傳檔案的連結傳送至電腦視覺 API 以進行分析。</span><span class="sxs-lookup"><span data-stu-id="28b6a-122">Azure Functions sends a link to the newly uploaded file to the Computer Vision API to analyze.</span></span>

5. <span data-ttu-id="28b6a-123">一旦資料從電腦視覺 API 傳回，Azure Functions 會在 Cosmos DB 中製作輸入項，一併保存映像中繼資料與分析結果。</span><span class="sxs-lookup"><span data-stu-id="28b6a-123">Once the data has been returned from the Computer Vision API, Azure Functions makes an entry in Cosmos DB to persist the results of the analysis alongside the image metadata.</span></span>

### <a name="components"></a><span data-ttu-id="28b6a-124">元件</span><span class="sxs-lookup"><span data-stu-id="28b6a-124">Components</span></span>

* <span data-ttu-id="28b6a-125">[電腦視覺 API][computer-vision-docs] 是認知服務套件的一部分，用來擷取每個映像的資訊。</span><span class="sxs-lookup"><span data-stu-id="28b6a-125">[Computer Vision API][computer-vision-docs] is part of the Cognitive Services suite and is used to retrieve information about each image.</span></span>

* <span data-ttu-id="28b6a-126">[Azure Functions][functions-docs] 提供 Web 應用程式的後端 API，以及已上傳映像的事件處理。</span><span class="sxs-lookup"><span data-stu-id="28b6a-126">[Azure Functions][functions-docs] provides the backend API for the web application, as well as the event processing for uploaded images.</span></span>

* <span data-ttu-id="28b6a-127">[事件方格][eventgrid-docs]會在新映像上傳至 Blob 儲存體時觸發事件。</span><span class="sxs-lookup"><span data-stu-id="28b6a-127">[Event Grid][eventgrid-docs] triggers an event when a new image is uploaded to blob storage.</span></span> <span data-ttu-id="28b6a-128">然後使用 Azure functions 處理映像。</span><span class="sxs-lookup"><span data-stu-id="28b6a-128">The image is then processed with Azure functions.</span></span>

* <span data-ttu-id="28b6a-129">[Blob 儲存體][storage-docs]會儲存上傳至 Web 應用程式的所有映像檔案，以及 Web 應用程式取用的任何靜態檔案。</span><span class="sxs-lookup"><span data-stu-id="28b6a-129">[Blob Storage][storage-docs] stores all of the image files that are uploaded into the web application, as well any static files that the web application consumes.</span></span>

* <span data-ttu-id="28b6a-130">[Cosmos DB][cosmos-docs] 會儲存每個已上傳映像的中繼資料，包括從電腦視覺 API 處理的結果。</span><span class="sxs-lookup"><span data-stu-id="28b6a-130">[Cosmos DB][cosmos-docs] stores metadata about each image that is uploaded, including the results of the processing from Computer Vision API.</span></span>

## <a name="alternatives"></a><span data-ttu-id="28b6a-131">替代項目</span><span class="sxs-lookup"><span data-stu-id="28b6a-131">Alternatives</span></span>

* <span data-ttu-id="28b6a-132">[自訂視覺服務][custom-vision-docs]。</span><span class="sxs-lookup"><span data-stu-id="28b6a-132">[Custom Vision Service][custom-vision-docs].</span></span> <span data-ttu-id="28b6a-133">電腦視覺 API 會傳回一組[分類法型分類][cv-categories]。</span><span class="sxs-lookup"><span data-stu-id="28b6a-133">The Computer Vision API returns a set of [taxonomy-based categories][cv-categories].</span></span> <span data-ttu-id="28b6a-134">如果您需要處理並非由電腦視覺 API 所傳回的資訊，請考慮自訂視覺服務，它可讓您建置自訂映像分類器。</span><span class="sxs-lookup"><span data-stu-id="28b6a-134">If you need to process information that isn't returned by the Computer Vision API, consider the Custom Vision Service, which lets you build custom image classifiers.</span></span>

* <span data-ttu-id="28b6a-135">[Azure 搜尋服務][azure-search-docs]。</span><span class="sxs-lookup"><span data-stu-id="28b6a-135">[Azure Search][azure-search-docs].</span></span> <span data-ttu-id="28b6a-136">如果您的使用案例牽涉到查詢中繼資料以尋找符合特定準則的映像，請考慮使用 Azure 搜尋服務。</span><span class="sxs-lookup"><span data-stu-id="28b6a-136">If your use case involves querying the metadata to find images that meet specific criteria, consider using Azure Search.</span></span> <span data-ttu-id="28b6a-137">[認知搜尋][cognitive-search] (目前處於預覽狀態) 與此工作流程緊密整合。</span><span class="sxs-lookup"><span data-stu-id="28b6a-137">Currently in preview, [Cognitive search][cognitive-search] seamlessly integrates this workflow.</span></span>

## <a name="considerations"></a><span data-ttu-id="28b6a-138">考量</span><span class="sxs-lookup"><span data-stu-id="28b6a-138">Considerations</span></span>

### <a name="scalability"></a><span data-ttu-id="28b6a-139">延展性</span><span class="sxs-lookup"><span data-stu-id="28b6a-139">Scalability</span></span>

<span data-ttu-id="28b6a-140">大部分情況下，此案例的所有元件都是會自動調整的受控服務。</span><span class="sxs-lookup"><span data-stu-id="28b6a-140">For the most part all of the components of this scenario are managed services that will automatically scale.</span></span> <span data-ttu-id="28b6a-141">幾個值得注意的例外狀況：Azure Functions 的上限為 200 個執行個體。</span><span class="sxs-lookup"><span data-stu-id="28b6a-141">A couple notable exceptions: Azure Functions has a limit of a maximum of 200 instances.</span></span> <span data-ttu-id="28b6a-142">如果您需要調整超過上限，請考慮多個區域或應用程式方案。</span><span class="sxs-lookup"><span data-stu-id="28b6a-142">If you need to scale beyond, consider multiple regions or app plans.</span></span>

<span data-ttu-id="28b6a-143">Cosmos DB 不會依據佈建要求單位 (RU) 自動調整。</span><span class="sxs-lookup"><span data-stu-id="28b6a-143">Cosmos DB doesn’t auto-scale in terms of provisioned request units (RUs).</span></span>  <span data-ttu-id="28b6a-144">如需評估需求的指引，請參閱我們文件中的[要求單位][request-units]。</span><span class="sxs-lookup"><span data-stu-id="28b6a-144">For guidance on estimating your requirements see [request units][request-units] in our documentation.</span></span> <span data-ttu-id="28b6a-145">若要充分利用 Cosmos DB 中的調整，您也應該參閱[分割區索引鍵][partition-key]。</span><span class="sxs-lookup"><span data-stu-id="28b6a-145">To fully take advantage of the scaling in Cosmos DB you should also take a look at [partition keys][partition-key].</span></span>

<span data-ttu-id="28b6a-146">NoSQL 資料庫會經常交換可用性、延展性及分割區的一致性 (CAP 定理的概念)。</span><span class="sxs-lookup"><span data-stu-id="28b6a-146">NoSQL databases frequently trade consistency (in the sense of the CAP theorem) for availability, scalability and partition.</span></span>  <span data-ttu-id="28b6a-147">不過，如果索引鍵-值資料模型用於此案例中，由於大部分作業定義為不可部分完成，所以不太需要交易一致性。</span><span class="sxs-lookup"><span data-stu-id="28b6a-147">However, in the case of key-value data models which is used in this scenario, transaction consistency is rarely needed as most operations are by definition atomic.</span></span> <span data-ttu-id="28b6a-148">[選擇正確的資料存放區](../../guide/technology-choices/data-store-overview.md)的額外指引可於架構中心找到。</span><span class="sxs-lookup"><span data-stu-id="28b6a-148">Additional guidance to [Choose the right data store](../../guide/technology-choices/data-store-overview.md) is available in the architecture center.</span></span>

<span data-ttu-id="28b6a-149">如需設計可調整解決方案的一般指引，請參閱 Azure Architecture Center 中的[延展性檢查清單][scalability]。</span><span class="sxs-lookup"><span data-stu-id="28b6a-149">For general guidance on designing scalable solutions, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="28b6a-150">安全性</span><span class="sxs-lookup"><span data-stu-id="28b6a-150">Security</span></span>

<span data-ttu-id="28b6a-151">[受控服務識別][msi] (MSI) 是用來提供帳戶內部其他資源的存取權，然後指派給您的 Azure Functions。</span><span class="sxs-lookup"><span data-stu-id="28b6a-151">[Managed service identities][msi] (MSI) are used to provide access to other resources internal to your account and then assigned to your Azure Functions.</span></span> <span data-ttu-id="28b6a-152">只允許以這些身分識別存取必要資源，確保沒有其他項目公開到您的函式 (而且可能公開給您的客戶)。</span><span class="sxs-lookup"><span data-stu-id="28b6a-152">Only allow access to the requisite resources in those identities to ensure that nothing extra is exposed to your functions (and potentially to your customers).</span></span>  

<span data-ttu-id="28b6a-153">如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。</span><span class="sxs-lookup"><span data-stu-id="28b6a-153">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="28b6a-154">災害復原</span><span class="sxs-lookup"><span data-stu-id="28b6a-154">Resiliency</span></span>

<span data-ttu-id="28b6a-155">此案例中的所有元件都是受控元件，所以它們在區域層級都會自動復原。</span><span class="sxs-lookup"><span data-stu-id="28b6a-155">All of the components in this scenario are managed, so at a regional level they are all resilient automatically.</span></span>

<span data-ttu-id="28b6a-156">如需設計彈性解決方案的一般指引，請參閱[為 Azure 設計有彈性的應用程式][resiliency]。</span><span class="sxs-lookup"><span data-stu-id="28b6a-156">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="pricing"></a><span data-ttu-id="28b6a-157">價格</span><span class="sxs-lookup"><span data-stu-id="28b6a-157">Pricing</span></span>

<span data-ttu-id="28b6a-158">為了探索執行此案例的成本，所有服務會在成本計算機中預先設定。</span><span class="sxs-lookup"><span data-stu-id="28b6a-158">To explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="28b6a-159">若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的流量。</span><span class="sxs-lookup"><span data-stu-id="28b6a-159">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="28b6a-160">我們根據流量 (假設所有映像的大小是 100kb)，提供了三個範例成本設定檔：</span><span class="sxs-lookup"><span data-stu-id="28b6a-160">We have provided three sample cost profiles based on amount of traffic (we assume all images are 100kb in size):</span></span>

* <span data-ttu-id="28b6a-161">[小型][pricing]：這個設定檔適用於每月處理 &lt; 5000 個映像。</span><span class="sxs-lookup"><span data-stu-id="28b6a-161">[Small][pricing]: this correlates to processing &lt; 5000 images a month.</span></span>
* <span data-ttu-id="28b6a-162">[中型][medium-pricing]：這個設定檔適用於每月處理 500000 個映像。</span><span class="sxs-lookup"><span data-stu-id="28b6a-162">[Medium][medium-pricing]: this correlates to processing 500,000 images a month.</span></span>
* <span data-ttu-id="28b6a-163">[大型][large-pricing]：這個設定檔適用於每月處理 5000 萬個映像。</span><span class="sxs-lookup"><span data-stu-id="28b6a-163">[Large][large-pricing]: this correlates to processing 50 million images a month.</span></span>

## <a name="related-resources"></a><span data-ttu-id="28b6a-164">相關資源</span><span class="sxs-lookup"><span data-stu-id="28b6a-164">Related Resources</span></span>

<span data-ttu-id="28b6a-165">如需此案例的引導式學習路徑，請參閱[在 Azure 中建置無伺服器 Web 應用程式][serverless]。</span><span class="sxs-lookup"><span data-stu-id="28b6a-165">For a guided learning path of this scenario, see [Build a serverless web app in Azure][serverless].</span></span>  

<span data-ttu-id="28b6a-166">在將這個解決方案放入生產環境之前，請檢閱 Azure Functions [最佳做法][functions-best-practices]。</span><span class="sxs-lookup"><span data-stu-id="28b6a-166">Before putting this in a production environment, review the Azure Functions [best practices][functions-best-practices].</span></span>

<!-- links -->
[pricing]: https://azure.com/e/f9b59d238b43423683db73f4a31dc380
[medium-pricing]: https://azure.com/e/7c7fc474db344b87aae93bc29ae27108
[large-pricing]: https://azure.com/e/cbadbca30f8640d6a061f8457a74ba7d
[functions-docs]: /azure/azure-functions/
[computer-vision-docs]: /azure/cognitive-services/computer-vision/home
[storage-docs]: /azure/storage/
[azure-search-docs]: /azure/search/
[cognitive-search]: /azure/search/cognitive-search-concept-intro
[architecture-computer-vision]: ./media/architecture-computer-vision.png
[serverless]: /azure/functions/tutorial-static-website-serverless-api-with-database
[cosmos-docs]: /azure/cosmos-db/
[eventgrid-docs]: /azure/event-grid/
[cognitive-docs]: /azure/#pivot=products&panel=ai
[custom-vision-docs]: /azure/cognitive-services/Custom-Vision-Service/home
[cv-categories]: /azure/cognitive-services/computer-vision/home#the-86-category-concept
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[functions-best-practices]: /azure/azure-functions/functions-best-practices
[msi]: /azure/app-service/app-service-managed-service-identity
[request-units]: /azure/cosmos-db/request-units
[partition-key]: /azure/cosmos-db/partition-data
---
title: 電子商務前端
titleSuffix: Azure Example Scenarios
description: 在 Azure 上裝載電子商務網站。
author: masonch
ms.date: 07/13/2018
ms.custom: fasttrack
ms.openlocfilehash: f07c21b8eb9d812b9831abe8f2e4f6d131893df2
ms.sourcegitcommit: 7d9efe716e8c9e99f3fafa9d0213d48c23d9713d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2019
ms.locfileid: "54160803"
---
# <a name="an-e-commerce-front-end-on-azure"></a><span data-ttu-id="d8df6-103">Azure 上的電子商務前端</span><span class="sxs-lookup"><span data-stu-id="d8df6-103">An e-commerce front end on Azure</span></span>

<span data-ttu-id="d8df6-104">此範例案例會逐步引導您使用 Azure 平台即服務 (PaaS) 工具，實作電子商務前端。</span><span class="sxs-lookup"><span data-stu-id="d8df6-104">This example scenario walks you through an implementation of an e-commerce front end using Azure platform as a service (PaaS) tools.</span></span> <span data-ttu-id="d8df6-105">許多電子商務網站都會面臨隨著時間而異的季節性和流量變化。</span><span class="sxs-lookup"><span data-stu-id="d8df6-105">Many e-commerce websites face seasonality and traffic variability over time.</span></span> <span data-ttu-id="d8df6-106">當產品或服務的需求激增時 (不論預期或非預期的情況下)，使用 PaaS 工具可讓您自動服務更多客戶與處理更多交易。</span><span class="sxs-lookup"><span data-stu-id="d8df6-106">When demand for your products or services takes off, whether predictably or unpredictably, using PaaS tools will allow you to handle more customers and more transactions automatically.</span></span> <span data-ttu-id="d8df6-107">此外，此案例可僅支付所需的容量，妥善發揮雲端的經濟效益。</span><span class="sxs-lookup"><span data-stu-id="d8df6-107">Additionally, this scenario takes advantage of cloud economics by paying only for the capacity you use.</span></span>

<span data-ttu-id="d8df6-108">此文件會協助您了解一起用來部署範例電子商務應用程式 (Relecloud Concerts，這是一個線上演唱會購票平台) 的各種 Azure PaaS 元件。</span><span class="sxs-lookup"><span data-stu-id="d8df6-108">This document will help you will learn about various Azure PaaS components and considerations used to bring together to deploy a sample e-commerce application, *Relecloud Concerts*, an online concert ticketing platform.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="d8df6-109">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="d8df6-109">Relevant use cases</span></span>

<span data-ttu-id="d8df6-110">其他相關的使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="d8df6-110">Other relevant use cases include:</span></span>

- <span data-ttu-id="d8df6-111">建置應用程式，該應用程式需要彈性的規模以便處理不同時間爆量的使用者。</span><span class="sxs-lookup"><span data-stu-id="d8df6-111">Building an application that needs elastic scale to handle bursts of users at different times.</span></span>
- <span data-ttu-id="d8df6-112">建置應用程式，該應用程式的設計目的是在世界各地不同 Azure 區域中以高可用性運作。</span><span class="sxs-lookup"><span data-stu-id="d8df6-112">Building an application that is designed to operate at high availability in different Azure regions around the world.</span></span>

## <a name="architecture"></a><span data-ttu-id="d8df6-113">架構</span><span class="sxs-lookup"><span data-stu-id="d8df6-113">Architecture</span></span>

![電子商務應用程式的範例案例架構][architecture]

<span data-ttu-id="d8df6-115">此案例涵蓋了從電子商務網站購買票證，整個案例的資料流程如下所示：</span><span class="sxs-lookup"><span data-stu-id="d8df6-115">This scenario covers purchasing tickets from an e-commerce site, the data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="d8df6-116">Azure 流量管理員會將使用者的要求路由傳送至 Azure App Service 中裝載的電子商務網站。</span><span class="sxs-lookup"><span data-stu-id="d8df6-116">Azure Traffic Manager routes a user's request to the e-commerce site hosted in Azure App Service.</span></span>
2. <span data-ttu-id="d8df6-117">Azure CDN 為使用者提供靜態映像和內容。</span><span class="sxs-lookup"><span data-stu-id="d8df6-117">Azure CDN serves static images and content to the user.</span></span>
3. <span data-ttu-id="d8df6-118">使用者透過 Azure Active Directory B2C 租用戶登入應用程式。</span><span class="sxs-lookup"><span data-stu-id="d8df6-118">User signs in to the application through an Azure Active Directory B2C tenant.</span></span>
4. <span data-ttu-id="d8df6-119">使用者使用 Azure 搜尋服務來搜尋演唱會。</span><span class="sxs-lookup"><span data-stu-id="d8df6-119">User searches for concerts using Azure Search.</span></span>
5. <span data-ttu-id="d8df6-120">網站會從 Azure SQL Database 提取演唱會詳細資料。</span><span class="sxs-lookup"><span data-stu-id="d8df6-120">Web site pulls concert details from Azure SQL Database.</span></span>
6. <span data-ttu-id="d8df6-121">網站會參考 Blob 儲存體中已購買的票證映像。</span><span class="sxs-lookup"><span data-stu-id="d8df6-121">Web site refers to purchased ticket images in Blob Storage.</span></span>
7. <span data-ttu-id="d8df6-122">資料庫查詢結果會快取到 Azure Redis 快取中，以達到更佳效能。</span><span class="sxs-lookup"><span data-stu-id="d8df6-122">Database query results are cached in Azure Redis Cache for better performance.</span></span>
8. <span data-ttu-id="d8df6-123">使用者提交票證訂單及演唱會評論 (位於佇列中)。</span><span class="sxs-lookup"><span data-stu-id="d8df6-123">User submits ticket orders and concert reviews, which are placed in the queue.</span></span>
9. <span data-ttu-id="d8df6-124">Azure Functions 會處理訂單款項和演唱會評論。</span><span class="sxs-lookup"><span data-stu-id="d8df6-124">Azure Functions processes order payment and concert reviews.</span></span>
10. <span data-ttu-id="d8df6-125">認知服務會提供演唱會評論的分析，以判斷人氣 (正面或負面)。</span><span class="sxs-lookup"><span data-stu-id="d8df6-125">Cognitive services provide an analysis of the concert review to determine the sentiment (positive or negative).</span></span>
11. <span data-ttu-id="d8df6-126">Application Insights 會提供效能計量，來監視 Web 應用程式的健康情況。</span><span class="sxs-lookup"><span data-stu-id="d8df6-126">Application Insights provides performance metrics for monitoring the health of the web application.</span></span>

### <a name="components"></a><span data-ttu-id="d8df6-127">元件</span><span class="sxs-lookup"><span data-stu-id="d8df6-127">Components</span></span>

- <span data-ttu-id="d8df6-128">[Azure CDN][docs-cdn] 從使用者附近的位置傳遞靜態、快取的內容，以減少延遲。</span><span class="sxs-lookup"><span data-stu-id="d8df6-128">[Azure CDN][docs-cdn] delivers static, cached content from locations close to users to reduce latency.</span></span>
- <span data-ttu-id="d8df6-129">[Azure 流量管理員][docs-traffic-manager]會為不同 Azure 區域的服務端點，控制使用者流量的散佈情況。</span><span class="sxs-lookup"><span data-stu-id="d8df6-129">[Azure Traffic Manager][docs-traffic-manager] controls the distribution of user traffic for service endpoints in different Azure regions.</span></span>
- <span data-ttu-id="d8df6-130">[應用程式服務 - Web Apps][docs-webapps] 會裝載 Web 應用程式，允許自動調整及高可用性，而不需要管理基礎結構。</span><span class="sxs-lookup"><span data-stu-id="d8df6-130">[App Services - Web Apps][docs-webapps] hosts web applications allowing autoscale and high availability without having to manage infrastructure.</span></span>
- <span data-ttu-id="d8df6-131">[Azure Active Directory - B2C][docs-b2c] 是一項身分識別管理服務，可以自訂和控制客戶在應用程式中如何註冊、登入及管理其設定檔。</span><span class="sxs-lookup"><span data-stu-id="d8df6-131">[Azure Active Directory - B2C][docs-b2c] is an identity management service that enables customization and control over how customers sign up, sign in, and manage their profiles in an application.</span></span>
- <span data-ttu-id="d8df6-132">[儲存體佇列][docs-storage-queues]會儲存可以由應用程式存取的大量佇列訊息。</span><span class="sxs-lookup"><span data-stu-id="d8df6-132">[Storage Queues][docs-storage-queues] stores large numbers of queue messages that can be accessed by an application.</span></span>
- <span data-ttu-id="d8df6-133">[Functions][docs-functions] 是無伺服器計算選項，可以讓應用程式隨選執行，不需要管理基礎結構。</span><span class="sxs-lookup"><span data-stu-id="d8df6-133">[Functions][docs-functions] are serverless compute options that allow applications to run on-demand without having to manage infrastructure.</span></span>
- <span data-ttu-id="d8df6-134">[認知服務 - 情感分析][docs-sentiment-analysis]會使用機器學習 API，讓開發人員輕鬆地將智慧型功能 (例如情緒和影片偵測；臉部、語音和視覺辨識；以及語音和語言理解) 新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="d8df6-134">[Cognitive Services - Sentiment Analysis][docs-sentiment-analysis] uses machine learning APIs and enables developers to easily add intelligent features – such as emotion and video detection; facial, speech, and vision recognition; and speech and language understanding – into applications.</span></span>
- <span data-ttu-id="d8df6-135">[Azure 搜尋服務][docs-search]是搜尋即服務雲端解決方案，透過 Web、行動和企業應用程式中的私用和異質內容來提供豐富的搜尋體驗。</span><span class="sxs-lookup"><span data-stu-id="d8df6-135">[Azure Search][docs-search] is a search-as-a-service cloud solution that provides a rich search experience over private, heterogenous content in web, mobile, and enterprise applications.</span></span>
- <span data-ttu-id="d8df6-136">[儲存體 Blob][docs-storage-blobs] 已針對儲存大量非結構化物件資料 (例如文字或二進位資料) 最佳化。</span><span class="sxs-lookup"><span data-stu-id="d8df6-136">[Storage Blobs][docs-storage-blobs] are optimized to store large amounts of unstructured data, such as text or binary data.</span></span>
- <span data-ttu-id="d8df6-137">[Redis 快取][docs-redis-cache]改善了系統的效能和延展性，這些系統相當倚賴後端資料存放區，其方法是將頻繁存取的資料暫時複製到位於應用程式附近的快速儲存體。</span><span class="sxs-lookup"><span data-stu-id="d8df6-137">[Redis Cache][docs-redis-cache] improves the performance and scalability of systems that rely heavily on back-end data stores by temporarily copying frequently accessed data to fast storage located close to the application.</span></span>
- <span data-ttu-id="d8df6-138">[SQL Database][docs-sql-database] 是 Microsoft Azure 中的一般用途關聯式資料庫受控服務，可支援關聯式資料、JSON、空間和 XML 等結構。</span><span class="sxs-lookup"><span data-stu-id="d8df6-138">[SQL Database][docs-sql-database] is a general-purpose relational database managed service in Microsoft Azure that supports structures such as relational data, JSON, spatial, and XML.</span></span>
- <span data-ttu-id="d8df6-139">[Application Insights][docs-application-insights] 的設計目的是協助您持續改善效能和可用性，方法是透過內建分析工具自動偵測效能異常，以協助了解使用者使用應用程式執行了什麼操作。</span><span class="sxs-lookup"><span data-stu-id="d8df6-139">[Application Insights][docs-application-insights] is designed to help you continuously improve performance and usability by automatically detecting performance anomalies through built-in analytics tools to help understand what users do with an app.</span></span>

### <a name="alternatives"></a><span data-ttu-id="d8df6-140">替代項目</span><span class="sxs-lookup"><span data-stu-id="d8df6-140">Alternatives</span></span>

<span data-ttu-id="d8df6-141">其他多項技術可供建置著重在大規模電子商務的客戶對應應用程式。</span><span class="sxs-lookup"><span data-stu-id="d8df6-141">Many other technologies are available for building a customer facing application focused on e-commerce at scale.</span></span> <span data-ttu-id="d8df6-142">這些技術涵蓋應用程式的前端以及資料層。</span><span class="sxs-lookup"><span data-stu-id="d8df6-142">These cover both the front end of the application as well as the data tier.</span></span>

<span data-ttu-id="d8df6-143">Web 層和函式的其他選項包括：</span><span class="sxs-lookup"><span data-stu-id="d8df6-143">Other options for the web tier and functions include:</span></span>

- <span data-ttu-id="d8df6-144">[Service Fabric][docs-service-fabric] - 這是一個平台，焦點在於建置分散式元件，可受益於在具有高度控制權的叢集之間部署及執行。</span><span class="sxs-lookup"><span data-stu-id="d8df6-144">[Service Fabric][docs-service-fabric] - A platform focused around building distributed components that benefit from being deployed and run across a cluster with a high degree of control.</span></span> <span data-ttu-id="d8df6-145">Service Fabric 也可用來裝載容器。</span><span class="sxs-lookup"><span data-stu-id="d8df6-145">Service Fabric can also be used to host containers.</span></span>
- <span data-ttu-id="d8df6-146">[Azure Kubernetes Service][docs-kubernetes-service] - 這是一個平台，用來建置及部署容器型解決方案，這些解決方案可以作為微服務架構的一個實作。</span><span class="sxs-lookup"><span data-stu-id="d8df6-146">[Azure Kubernetes Service][docs-kubernetes-service] - A platform for building and deploying container-based solutions that can be used as one implementation of a microservices architecture.</span></span> <span data-ttu-id="d8df6-147">這樣可讓應用程式的不同元件更有靈活度，以便隨選獨立調整。</span><span class="sxs-lookup"><span data-stu-id="d8df6-147">This allows for agility of different components of the application to be able to scale independently on demand.</span></span>
- <span data-ttu-id="d8df6-148">[Azure 容器執行個體][docs-container-instances] - 以短期生命週期快速部署及執行容器的方式。</span><span class="sxs-lookup"><span data-stu-id="d8df6-148">[Azure Container Instances][docs-container-instances] - A way of quickly deploying and running containers with a short lifecycle.</span></span> <span data-ttu-id="d8df6-149">這裡的容器會部署以執行快速處理作業，例如處理訊息或執行計算，然後在完成時取消佈建。</span><span class="sxs-lookup"><span data-stu-id="d8df6-149">Containers here are deployed to run a quick processing job such as processing a message or performing a calculation and then deprovisioned as soon as they are complete.</span></span>
- <span data-ttu-id="d8df6-150">[服務匯流排][service-bus]可用來取代儲存體佇列。</span><span class="sxs-lookup"><span data-stu-id="d8df6-150">[Service Bus][service-bus] could be used in place of a Storage Queue.</span></span>

<span data-ttu-id="d8df6-151">資料層的其他選項包括：</span><span class="sxs-lookup"><span data-stu-id="d8df6-151">Other options for the data tier include:</span></span>

- <span data-ttu-id="d8df6-152">[Cosmos DB](/azure/cosmos-db/introduction)：Microsoft 全球發行的多模型資料庫。</span><span class="sxs-lookup"><span data-stu-id="d8df6-152">[Cosmos DB](/azure/cosmos-db/introduction): Microsoft's globally distributed, multi-model database.</span></span> <span data-ttu-id="d8df6-153">此服務提供平台來執行其他資料模型，例如 Mongo DB、Cassandra、Graph 資料或簡單的資料表儲存體。</span><span class="sxs-lookup"><span data-stu-id="d8df6-153">This service provides a platform to run other data models such as Mongo DB, Cassandra, Graph data, or simple table storage.</span></span>

## <a name="considerations"></a><span data-ttu-id="d8df6-154">考量</span><span class="sxs-lookup"><span data-stu-id="d8df6-154">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="d8df6-155">可用性</span><span class="sxs-lookup"><span data-stu-id="d8df6-155">Availability</span></span>

- <span data-ttu-id="d8df6-156">建置您的雲端應用程式時，請考量利用[獲得可用性的典型設計模式][design-patterns-availability]。</span><span class="sxs-lookup"><span data-stu-id="d8df6-156">Consider leveraging the [typical design patterns for availability][design-patterns-availability] when building your cloud application.</span></span>
- <span data-ttu-id="d8df6-157">在適當的 [App Service Web 應用程式參考架構][app-service-reference-architecture]中檢閱可用性考量</span><span class="sxs-lookup"><span data-stu-id="d8df6-157">Review the availability considerations in the appropriate [App Service web application reference architecture][app-service-reference-architecture]</span></span>
- <span data-ttu-id="d8df6-158">如需有關可用性的其他考量，請參閱 Azure 架構中心的[可用性檢查清單][availability]。</span><span class="sxs-lookup"><span data-stu-id="d8df6-158">For additional considerations concerning availability, see the [availability checklist][availability] in the Azure Architecture Center.</span></span>

### <a name="scalability"></a><span data-ttu-id="d8df6-159">延展性</span><span class="sxs-lookup"><span data-stu-id="d8df6-159">Scalability</span></span>

- <span data-ttu-id="d8df6-160">建置雲端應用程式時，請了解[獲得延展性的典型設計模式][design-patterns-scalability]。</span><span class="sxs-lookup"><span data-stu-id="d8df6-160">When building a cloud application be aware of the [typical design patterns for scalability][design-patterns-scalability].</span></span>
- <span data-ttu-id="d8df6-161">在適當的 [App Service Web 應用程式參考架構][app-service-reference-architecture]中檢閱延展性考量</span><span class="sxs-lookup"><span data-stu-id="d8df6-161">Review the scalability considerations in the appropriate [App Service web application reference architecture][app-service-reference-architecture]</span></span>
- <span data-ttu-id="d8df6-162">如需其他延展性主題，請參閱 Azure 架構中心的[延展性檢查清單][scalability]。</span><span class="sxs-lookup"><span data-stu-id="d8df6-162">For other scalability topics, see the [scalability checklist][scalability] available in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="d8df6-163">安全性</span><span class="sxs-lookup"><span data-stu-id="d8df6-163">Security</span></span>

- <span data-ttu-id="d8df6-164">請在適用時考量利用[獲得安全性的典型設計模式][design-patterns-security]。</span><span class="sxs-lookup"><span data-stu-id="d8df6-164">Consider leveraging the [typical design patterns for security][design-patterns-security] where appropriate.</span></span>
- <span data-ttu-id="d8df6-165">在適當的 [App Service Web 應用程式參考架構][app-service-reference-architecture]中檢閱安全性考量。</span><span class="sxs-lookup"><span data-stu-id="d8df6-165">Review the security considerations in the appropriate [App Service web application reference architecture][app-service-reference-architecture].</span></span>
- <span data-ttu-id="d8df6-166">請考量遵循[安全開發生命週期][secure-development]程序，以協助開發人員在降低開發成本的同時，又能建置更安全的軟體並且解決安全性合規性需求。</span><span class="sxs-lookup"><span data-stu-id="d8df6-166">Consider following a [secure development lifecycle][secure-development] process to help developers build more secure software and address security compliance requirements while reducing development cost.</span></span>
- <span data-ttu-id="d8df6-167">檢閱 [Azure PCI DSS 合規性][pci-dss-blueprint]的藍圖架構。</span><span class="sxs-lookup"><span data-stu-id="d8df6-167">Review the blueprint architecture for [Azure PCI DSS compliance][pci-dss-blueprint].</span></span>

### <a name="resiliency"></a><span data-ttu-id="d8df6-168">復原功能</span><span class="sxs-lookup"><span data-stu-id="d8df6-168">Resiliency</span></span>

- <span data-ttu-id="d8df6-169">請考量利用[斷路器模式][circuit-breaker]，以在應用程式某部分無法使用時，提供柔性錯誤處理。</span><span class="sxs-lookup"><span data-stu-id="d8df6-169">Consider leveraging the [circuit breaker pattern][circuit-breaker] to provide graceful error handling should one part of the application not be available.</span></span>
- <span data-ttu-id="d8df6-170">檢閱[獲得復原的典型設計模式][design-patterns-resiliency]，並考量在適當時實作。</span><span class="sxs-lookup"><span data-stu-id="d8df6-170">Review the [typical design patterns for resiliency][design-patterns-resiliency] and consider implementing these where appropriate.</span></span>
- <span data-ttu-id="d8df6-171">您可以在 Azure 架構中心上找到一些 [App Service 的建議做法][resiliency-app-service]。</span><span class="sxs-lookup"><span data-stu-id="d8df6-171">You can find a number of [recommended practices for App Service][resiliency-app-service] in the Azure Architecture Center.</span></span>
- <span data-ttu-id="d8df6-172">請考量針對資料層使用作用中[異地複寫][sql-geo-replication]，針對映像和佇列使用[異地備援][storage-geo-redudancy] 儲存體。</span><span class="sxs-lookup"><span data-stu-id="d8df6-172">Consider using active [geo-replication][sql-geo-replication] for the data tier and [geo-redundant][storage-geo-redudancy] storage for images and queues.</span></span>
- <span data-ttu-id="d8df6-173">如需關於[復原][resiliency]的深入討論，請參閱 Azure 架構中心的相關文章。</span><span class="sxs-lookup"><span data-stu-id="d8df6-173">For a deeper discussion on [resiliency][resiliency], see the relevant article in the Azure Architecture Center.</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="d8df6-174">部署案例</span><span class="sxs-lookup"><span data-stu-id="d8df6-174">Deploy the scenario</span></span>

<span data-ttu-id="d8df6-175">若要部署此案例，您可以遵循示範如何以手動方式部署每個元件的這個[逐步教學課程][end-to-end-walkthrough]。</span><span class="sxs-lookup"><span data-stu-id="d8df6-175">To deploy this scenario, you can follow this [step-by-step tutorial][end-to-end-walkthrough] demonstrating how to manually deploy each component.</span></span> <span data-ttu-id="d8df6-176">本教學課程也提供 .NET 範例應用程式，該應用程式會執行簡單的票證購買應用程式。</span><span class="sxs-lookup"><span data-stu-id="d8df6-176">This tutorial also provides a .NET sample application that runs a simple ticket purchasing application.</span></span> <span data-ttu-id="d8df6-177">此外，還有 Resource Manager 範本，可以將大部分 Azure 資源的部署自動化。</span><span class="sxs-lookup"><span data-stu-id="d8df6-177">Additionally, there is a Resource Manager template to automate the deployment of most of the Azure resources.</span></span>

## <a name="pricing"></a><span data-ttu-id="d8df6-178">價格</span><span class="sxs-lookup"><span data-stu-id="d8df6-178">Pricing</span></span>

<span data-ttu-id="d8df6-179">探索執行此案例的成本，所有服務會在成本計算機中預先設定。</span><span class="sxs-lookup"><span data-stu-id="d8df6-179">Explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="d8df6-180">若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的流量。</span><span class="sxs-lookup"><span data-stu-id="d8df6-180">To see how the pricing would change for your particular use case change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="d8df6-181">我們根據您預期取得的流量，提供了三個範例成本設定檔：</span><span class="sxs-lookup"><span data-stu-id="d8df6-181">We have provided three sample cost profiles based on amount of traffic you expect to get:</span></span>

- <span data-ttu-id="d8df6-182">[小型][small-pricing]：這個定價範例表示建置最小生產層級執行個體所需的元件。</span><span class="sxs-lookup"><span data-stu-id="d8df6-182">[Small][small-pricing]: This pricing example represents the components necessary to build the out for a minimum production level instance.</span></span> <span data-ttu-id="d8df6-183">我們在這裡假設是少量使用者，每個月只有數千個。</span><span class="sxs-lookup"><span data-stu-id="d8df6-183">Here we are assuming a small number of users, numbering only in a few thousand per month.</span></span> <span data-ttu-id="d8df6-184">應用程式使用標準 Web 應用程式的單一執行個體，這就足以啟用自動調整。</span><span class="sxs-lookup"><span data-stu-id="d8df6-184">The app is using a single instance of a standard web app that will be enough to enable autoscaling.</span></span> <span data-ttu-id="d8df6-185">每個其他元件會調整到基本層，允許最少的成本，但是仍然確保有 SLA 支援和足夠的容量，可以處理生產層級工作負載。</span><span class="sxs-lookup"><span data-stu-id="d8df6-185">Each of the other components are scaled to a basic tier that will allow for a minimum amount of cost but still ensure that there is SLA support and enough capacity to handle a production level workload.</span></span>
- <span data-ttu-id="d8df6-186">[中型][medium-pricing]：這個定價範例表示元件代表中等大小部署。</span><span class="sxs-lookup"><span data-stu-id="d8df6-186">[Medium][medium-pricing]: This pricing example represents the components indicative of a moderate size deployment.</span></span> <span data-ttu-id="d8df6-187">我們在這裡預估每月使用系統的使用者大約有 100000 個。</span><span class="sxs-lookup"><span data-stu-id="d8df6-187">Here we estimate approximately 100,000 users using the system over the course of a month.</span></span> <span data-ttu-id="d8df6-188">預期流量是在具有中等標準層的單一應用程式服務執行個體中處理。</span><span class="sxs-lookup"><span data-stu-id="d8df6-188">The expected traffic is handled in a single app service instance with a moderate standard tier.</span></span> <span data-ttu-id="d8df6-189">此外，認知和搜尋服務的中等層會新增至計算機。</span><span class="sxs-lookup"><span data-stu-id="d8df6-189">Additionally, moderate tiers of cognitive and search services are added to the calculator.</span></span>
- <span data-ttu-id="d8df6-190">[大型][large-pricing]：這個定價範例表示應用程式適用於大規模，有數百萬個使用者每月移動以 TB 計算的資料。</span><span class="sxs-lookup"><span data-stu-id="d8df6-190">[Large][large-pricing]: This pricing example represents an application meant for high scale, at the order of millions of users per month moving terabytes of data.</span></span> <span data-ttu-id="d8df6-191">在這個使用量層級，高效能、進階層 Web 應用程式會部署在需要流量管理員的多個區域前端。</span><span class="sxs-lookup"><span data-stu-id="d8df6-191">At this level of usage high performance, premium tier web apps deployed in multiple regions fronted by traffic manager is required.</span></span> <span data-ttu-id="d8df6-192">資料是由下列項目組成：儲存體、資料庫和 CDN，已針對數 TB 的資料進行設定。</span><span class="sxs-lookup"><span data-stu-id="d8df6-192">Data consists of the following: storage, databases, and CDN, are configured for terabytes of data.</span></span>

## <a name="related-resources"></a><span data-ttu-id="d8df6-193">相關資源</span><span class="sxs-lookup"><span data-stu-id="d8df6-193">Related resources</span></span>

- <span data-ttu-id="d8df6-194">[多區域 Web 應用程式的參考架構][multi-region-web-app]</span><span class="sxs-lookup"><span data-stu-id="d8df6-194">[Reference Architecture for Multi-Region Web Application][multi-region-web-app]</span></span>
- <span data-ttu-id="d8df6-195">[容器上的 eShop 參考範例][microservices-ecommerce]</span><span class="sxs-lookup"><span data-stu-id="d8df6-195">[eShop on Containers Reference Example][microservices-ecommerce]</span></span>

<!-- links -->
[architecture]: ./media/architecture-ecommerce-scenario.png
[small-pricing]: https://azure.com/e/90fbb6a661a04888a57322985f9b34ac
[medium-pricing]: https://azure.com/e/38d5d387e3234537b6859660db1c9973
[large-pricing]: https://azure.com/e/f07f99b6c3134803a14c9b43fcba3e2f
[app-service-reference-architecture]: ../../reference-architectures/app-service-web-app/basic-web-app.md
[availability]: /azure/architecture/checklist/availability
[circuit-breaker]: /azure/architecture/patterns/circuit-breaker
[design-patterns-availability]: /azure/architecture/patterns/category/availability
[design-patterns-resiliency]: /azure/architecture/patterns/category/resiliency
[design-patterns-scalability]: /azure/architecture/patterns/category/performance-scalability
[design-patterns-security]: /azure/architecture/patterns/category/security
[docs-application-insights]: /azure/application-insights/app-insights-overview
[docs-b2c]: /azure/active-directory-b2c/active-directory-b2c-overview
[docs-cdn]: /azure/cdn/cdn-overview
[docs-container-instances]: /azure/container-instances/
[docs-kubernetes-service]: /azure/aks/
[docs-functions]: /azure/azure-functions/functions-overview
[docs-redis-cache]: /azure/redis-cache/cache-overview
[docs-search]: /azure/search/search-what-is-azure-search
[docs-service-fabric]: /azure/service-fabric/
[docs-sentiment-analysis]: /azure/cognitive-services/welcome
[docs-sql-database]: /azure/sql-database/sql-database-technical-overview
[docs-storage-blobs]: /azure/storage/blobs/storage-blobs-introduction
[docs-storage-queues]: /azure/storage/queues/storage-queues-introduction
[docs-traffic-manager]: /azure/traffic-manager/traffic-manager-overview
[docs-webapps]: /azure/app-service/app-service-web-overview
[end-to-end-walkthrough]: https://github.com/Azure/fta-customerfacingapps/tree/master/ecommerce/articles
[microservices-ecommerce]: https://github.com/dotnet-architecture/eShopOnContainers
[multi-region-web-app]: /azure/architecture/reference-architectures/app-service-web-app/multi-region
[pci-dss-blueprint]: /azure/security/blueprints/payment-processing-blueprint
[resiliency-app-service]: /azure/architecture/checklist/resiliency-per-service#app-service
[resiliency]: /azure/architecture/checklist/resiliency
[scalability]: /azure/architecture/checklist/scalability
[secure-development]: https://www.microsoft.com/SDL/process/design.aspx
[service-bus]: /azure/service-bus-messaging/
[sql-geo-replication]: /azure/sql-database/sql-database-geo-replication-overview
[storage-geo-redudancy]: /azure/storage/common/storage-redundancy-grs

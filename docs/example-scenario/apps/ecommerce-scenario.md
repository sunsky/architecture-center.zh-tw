---
title: Azure 上的電子商務前端
description: 經過證明的案例，可以在 Azure 上裝載電子商務網站
author: masonch
ms.date: 7/13/18
ms.openlocfilehash: 568821e97c6b90a36429dfa8ec0ef9ed38c7963c
ms.sourcegitcommit: 71cbef121c40ef36e2d6e3a088cb85c4260599b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060969"
---
# <a name="e-commerce-front-end-on-azure"></a>Azure 上的電子商務前端

此範例案例會逐步引導您使用 Azure 平台即服務 (PaaS) 工具，實作電子商務前端。 許多電子商務網站都會面臨隨著時間而異的季節性和流量變化。 當產品或服務的需求激增時 (不論預期或非預期的情況下)，使用 PaaS 工具可讓您自動服務更多客戶與處理更多交易。 此外，此案例可僅支付所需的容量，妥善發揮雲端的經濟效益。

此文件會協助您了解一起用來部署範例電子商務應用程式 (Relecloud Concerts，這是一個線上演唱會購票平台) 的各種 Azure PaaS 元件。

## <a name="potential-use-cases"></a>潛在使用案例

請針對下列使用案例考慮此案例：

* 建置應用程式，該應用程式需要彈性的規模以便處理不同時間爆量的使用者。
* 建置應用程式，該應用程式的設計目的是在世界各地不同 Azure 區域中以高可用性運作。

## <a name="architecture"></a>架構

![電子商務應用程式的範例案例架構][architecture-diagram]

此案例涵蓋了從電子商務網站購買票證，整個案例的資料流程如下所示：

1. Azure 流量管理員會將使用者的要求路由傳送至 Azure App Service 中裝載的電子商務網站。
2. Azure CDN 為使用者提供靜態映像和內容。
3. 使用者透過 Azure Active Directory B2C 租用戶登入應用程式。
4. 使用者使用 Azure 搜尋服務來搜尋演唱會。
5. 網站會從 Azure SQL Database 提取演唱會詳細資料。 
6. 網站會參考 Blob 儲存體中已購買的票證映像。
7. 資料庫查詢結果會快取到 Azure Redis 快取中，以達到更佳效能。
8. 使用者提交票證訂單及演唱會評論 (位於佇列中)。
9. Azure Functions 會處理訂單款項和演唱會評論。
10. 認知服務會提供演唱會評論的分析，以判斷人氣 (正面或負面)。
11. Application Insights 會提供效能計量，來監視 Web 應用程式的健康情況。

### <a name="components"></a>元件

* [Azure CDN][docs-cdn] 從使用者附近的位置傳遞靜態、快取的內容，以減少延遲。
* [Azure 流量管理員][docs-traffic-manager]會為不同 Azure 區域的服務端點，控制使用者流量的散佈情況。
* [App Services - Web 應用程式][docs-webapps] 會裝載 Web 應用程式，允許自動調整及高可用性，而不需要管理基礎結構。
* [Azure Active Directory - B2C][docs-b2c] 是一項身分識別管理服務，可以自訂和控制客戶在應用程式中如何註冊、登入及管理其設定檔。
* [儲存體佇列][docs-storage-queues]會儲存可以由應用程式存取的大量佇列訊息。
* [Functions][docs-functions] 是無伺服器計算選項，可以讓應用程式隨選執行，不需要管理基礎結構。
* [認知服務 - 情感分析][docs-sentiment-analysis]會使用機器學習 API，讓開發人員輕鬆地將智慧型功能 (例如情緒和影片偵測；臉部、語音和視覺辨識；以及語音和語言理解) 新增至應用程式。
* [Azure 搜尋服務][docs-search]是搜尋即服務雲端解決方案，透過 Web、行動和企業應用程式中的私用和異質內容來提供豐富的搜尋體驗。
* [儲存體 Blob][docs-storage-blobs] 已針對儲存大量非結構化物件資料 (例如文字或二進位資料) 最佳化。
* [Redis 快取][docs-redis-cache]改善了系統的效能和延展性，這些系統相當倚賴後端資料存放區，其方法是將頻繁存取的資料暫時複製到位於應用程式附近的快速儲存體。
* [SQL Database][docs-sql-database] 是 Microsoft Azure 中的一般用途關聯式資料庫受控服務，可支援關聯式資料、JSON、空間和 XML 等結構。
* [Application Insights][docs-application-insights] 的設計目的是協助您持續改善效能和可用性，方法是透過內建分析工具自動偵測效能異常，以協助了解使用者使用應用程式執行了什麼操作。

### <a name="alternatives"></a>替代項目

其他多項技術可供建置著重在大規模電子商務的客戶對應應用程式。 這些技術涵蓋應用程式的前端以及資料層。

Web 層和函式的其他選項包括：

* [Service Fabric][docs-service-fabric] - 這是一個平台，焦點在於建置分散式元件，可受益於在具有高度控制權的叢集之間部署及執行。 Service Fabric 也可用來裝載容器。
* [Azure Kubernetes Service][docs-kubernetes-service] - 這是一個平台，用來建置及部署容器型解決方案，這些解決方案可以作為微服務架構的一個實作。 這樣可讓應用程式的不同元件更有靈活度，以便隨選獨立調整。
* [Azure 容器執行個體][docs-container-instances] - 以短期生命週期快速部署及執行容器的方式。 這裡的容器通常會部署以執行快速處理作業，例如處理訊息或執行計算，然後在完成時取消佈建。
* [服務匯流排][service-bus] 可用來取代儲存體佇列。

資料層的其他選項包括：

* [Cosmos DB][docs-cosmosdb] - Microsoft 全球發行的多模型資料庫。 提供平台來執行其他資料模型，例如 Mongo DB、Cassandra、Graph 資料或簡單的資料表儲存體。

## <a name="considerations"></a>考量

### <a name="availability"></a>可用性

* 建置您的雲端應用程式時，請考量利用[獲得可用性的典型設計模式][design-patterns-availability]。
* 在適當的 [App Service Web 應用程式參考架構][app-service-reference-architecture]中檢閱可用性考量
* 如需有關可用性的其他考量，請參閱架構中心的[可用性檢查清單][availability]。

### <a name="scalability"></a>延展性

* 建置雲端應用程式時，請了解[獲得延展性的典型設計模式][design-patterns-scalability]。
* 在適當的 [App Service Web 應用程式參考架構][app-service-reference-architecture]中檢閱延展性考量
* 如需其他延展性主題，請參閱架構中心的[延展性檢查清單][scalability]。

### <a name="security"></a>安全性

* 請在適用時考量利用[獲得安全性的典型設計模式][design-patterns-security]。
* 在適當的 [App Service Web 應用程式參考架構][app-service-reference-architecture]中檢閱安全性考量。
* 請考量遵循[安全開發生命週期][secure-development]程序，以協助開發人員在降低開發成本的同時，又能建置更安全的軟體並且解決安全性合規性需求。
* 檢閱 [Azure PCI DSS 合規性][pci-dss-blueprint]的藍圖架構。

### <a name="resiliency"></a>災害復原

* 請考量利用[斷路器模式][circuit-breaker]，以在應用程式某部分無法使用時，提供柔性錯誤處理。
* 檢閱[獲得復原的典型設計模式][design-patterns-resiliency]，並考量在適當時實作。
* 您可以在架構中心上找到一些 [App Service 的復原建議做法][resiliency-app-service]。
* 請考量針對資料層使用作用中[異地複寫][sql-geo-replication]，針對映像和佇列使用[異地備援][storage-geo-redudancy] 儲存體。
* 如需關於[復原][resiliency]的深入討論，請參閱架構中心的相關文章。

## <a name="deploy-the-scenario"></a>部署案例

若要部署此案例，您可以遵循示範如何以手動方式部署每個元件的這個[逐步教學課程][end-to-end-walkthrough]。 本教學課程也提供 .NET 範例應用程式，該應用程式會執行簡單的票證購買應用程式。 此外，還有 ARM 範本，可以將大部分 Azure 資源的部署自動化。

## <a name="pricing"></a>價格

探索執行此案例的成本，所有服務會在成本計算機中預先設定。 若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的流量。

我們根據您預期取得的流量，提供了三個範例成本設定檔：

* [小型][small-pricing]：這表示建置最小生產層級執行個體所需的元件。 我們在這裡假設是小量使用者，每個月只有數千個。 應用程式使用標準 Web 應用程式的單一執行個體，這就足以啟用自動調整。 每個其他元件會調整到基本層，允許最少的成本，但是仍然確保有 SLA 支援和足夠的容量可以處理生產層級工作負載。
* [中型][medium-pricing]：這表示元件代表中等大小部署。 我們在這裡預估每月使用系統的使用者大約有 100000 個。 預期流量是在具有中等標準層的單一應用程式服務執行個體中處理。 此外，認知和搜尋服務的中等層會新增至計算機。
* [大型][large-pricing]：這表示應用程式適用於大規模，有數百萬個使用者每月移動以 TB 計算的資料。 在這個使用量層級，高效能、進階層 Web 應用程式會部署在需要流量管理員的多個區域前端。 資料是由下列項目組成：儲存體、資料庫和 CDN，已針對數 TB 的資料進行設定。

## <a name="related-resources"></a>相關資源

* [多區域 Web 應用程式的參考架構][multi-region-web-app]
* [容器上的 eShop 參考範例][microservices-ecommerce]

<!-- links -->
[small-pricing]: https://azure.com/e/90fbb6a661a04888a57322985f9b34ac
[medium-pricing]: https://azure.com/e/38d5d387e3234537b6859660db1c9973
[large-pricing]: https://azure.com/e/f07f99b6c3134803a14c9b43fcba3e2f
[app-service-reference-architecture]: /azure/architecture/reference-architectures/app-service-web-app/
[architecture-diagram]: ./media/architecture-diagram-ecommerce-solution.png
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
[docs-cosmosdb]: /azure/cosmos-db/
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
[secure-development]: https://www.microsoft.com/en-us/SDL/process/design.aspx
[sql-geo-replication]: /azure/sql-database/sql-database-geo-replication-overview
[storage-geo-redudancy]: /azure/storage/common/storage-redundancy-grs

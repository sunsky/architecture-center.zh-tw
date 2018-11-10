---
title: 可調整規模的 Web 應用程式
description: 改善 Microsoft Azure 中執行之 Web 應用程式的延展性。
author: MikeWasson
pnp.series.title: Azure App Service
pnp.series.prev: basic-web-app
pnp.series.next: multi-region-web-app
ms.date: 10/25/2018
cardTitle: Improve scalability
ms.openlocfilehash: 208413a49fe4a3f9ca308fa1a939ba426e7fa636
ms.sourcegitcommit: 065fa8ecb37c8be1827da861243ad6a33c75c99d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2018
ms.locfileid: "50136687"
---
# <a name="improve-scalability-in-a-web-application"></a>改善 Web 應用程式的延展性

此參考架構顯示經過證實的改善 Azure App Service Web 應用程式延展性和效能的作法。

![[0]][0]

下載這個架構的 [Visio 檔案][visio-download]。

## <a name="architecture"></a>架構

此架構是根據[基本 Web 應用程式][basic-web-app]中的架構建置的。 包括下列元件：

* **資源群組**。 [資源群組][resource-group]是 Azure 資源的邏輯容器。
* **[Web 應用程式][app-service-web-app]**. 典型的現代應用程式可能包含一個網站以及一個或多個符合 REST 的 Web API。 瀏覽器用戶端透過 AJAX、原生用戶端應用程式或伺服器端應用程式耗用 Web API。 如需 Web API 的設計考量，請參閱 [API 指導方針][api-guidance]。
* **函式應用程式**。 使用[函式應用程式][functions]執行背景工作。 函式由觸發程序叫用，例如計時器事件或位於佇列上的訊息。 對於長時間執行具狀態的工作，使用 [Durable Functions][durable-functions]。
* **佇列**。 在此處的架構中，應用程式將訊息放到 [Azure 佇列儲存體][queue-storage]佇列上，藉此將背景工作排入佇列。 訊息會觸發函式應用程式。 或者，也可以使用服務匯流排佇列。 如需兩者的比較，請參閱 [Azure 佇列和服務匯流排佇列 - 異同比較][queues-compared]。
* **快取**。 在 [Azure Redis 快取][azure-redis]中儲存半靜態資料。  
* <strong>CDN</strong>。 使用 [Azure 內容傳遞網路][azure-cdn] (CDN) 快取公開可用的內容，以降低延遲而且更快速傳遞內容。
* **資料儲存體**。 使用 [SQL Database][sql-db] 儲存關聯式資料。 針對非關聯式資料，請考慮 [Cosmos DB][cosmosdb]。
* **Azure 搜尋服務**。 使用 [Azure 搜尋][azure-search] 新增搜尋功能，例如搜尋建議、模糊搜尋、特定語言搜尋。 Azure 搜尋服務通會搭配其他資料存放區，特別是當主要資料存放區需要嚴格的一致性。 這種方式會將授權的資料儲存在 Azure 搜尋服務中的另一個資料存放區和搜尋索引。 Azure 搜尋服務也可用於合併多個資料存放區中的單一搜尋索引。  
* **Azure DNS**。 [Azure DNS][azure-dns] 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構提供名稱解析。 只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。
* **應用程式閘道**。 [應用程式閘道](/azure/application-gateway/)是第 7 層負載平衡器。 在此架構中，它會將 HTTP 要求路由傳送至 Web 前端。 應用程式閘道也會提供 [Web 應用程式防火牆](/azure/application-gateway/waf-overview) (WAF)，該防火牆會保護應用程式免於常見惡意探索和弱點。 

## <a name="recommendations"></a>建議

您的需求可能和此處所述的架構不同。 以本節的建議作為起點。

### <a name="app-service-apps"></a>App Service 應用程式
建議您將 Web 應用程式和 Web API 建立為不同的 App Service 應用程式。 此設計可讓您在個別的 App Service 方案中執行它們，使它們可以獨立調整規模。 如果您一開始不需要這種程度的延展性，可以將應用程式部署到相同的方案中，之後有需要時再將它們移到別的方案。

> [!NOTE]
> 在基本、標準和進階方案中，是依照方案中的 VM 執行個體計費，而不是依照應用程式。 請參閱 [App Service 價格][app-service-pricing]。
> 
> 

### <a name="cache"></a>快取
您可以使用 [Azure Redis 快取][azure-redis]來快取某些資料，以改善效能和延展性。 請考慮將 Redis 快取用於：

* 半靜態交易資料。
* 工作階段狀態。
* HTML 輸出。 這對於要呈現複雜 HTML 輸出的應用程式很實用。

如需設計快取策略的詳細指引，請參閱[快取指引][caching-guidance]。

### <a name="cdn"></a>CDN
使用 [Azure CDN][azure-cdn] 快取靜態內容。 CDN 的主要優點是可以為使用者降低延遲，因為是在地理位置接近使用者的邊緣伺服器快取內容。 CDN 也可以減少應用程式的負載，因為流量不是由應用程式處理。

如果您的應用程式大部分是靜態網頁，請考慮使用 [CDN 快取整個應用程式][cdn-app-service]。 否則，將靜態內容 (例如影像、CSS、HTML 檔案) 放在 [Azure 儲存體，並使用 CDN 快取這些檔案][cdn-storage-account]。

> [!NOTE]
> Azure CDN 無法服務需要驗證的內容。
> 
> 

如需詳細指引，請參閱[內容傳遞網路 (CDN) 指引][cdn-guidance]。

### <a name="storage"></a>儲存體
現代應用程式通常要處理大量的資料。 為了能針對雲端調整規模，請務必選擇正確的儲存體類型。 以下是一些基準建議。 

| 您想要儲存的內容 | 範例 | 建議的儲存體 |
| --- | --- | --- |
| 檔案 |影像、文件、PDF |Azure Blob 儲存體 |
| 索引鍵/值組 |依使用者識別碼查閱的使用者設定檔資料 |Azure 資料表儲存體 |
| 用來觸發進一步處理的簡訊 |訂單要求 |Azure 佇列儲存體、服務匯流排佇列或服務匯流排主題 |
| 具有彈性結構描述且需要基本查詢的非關聯式資料 |產品目錄 |文件資料庫，例如 Azure Cosmos DB、MongoDB 或 Apache CouchDB |
| 需要更豐富查詢支援、嚴格結構描述，及/或強式一致性的關聯式資料 |產品庫存 |連接字串 |

 請參閱[選擇正確的資料存放區][datastore]。

## <a name="scalability-considerations"></a>延展性考量

Azure App Service 的主要優點是能夠根據負載調整應用程式規模。 以下是規劃調整應用程式時，應記住的一些考量。

### <a name="app-service-app"></a>App Service 應用程式
如果您的解決方案包含數個 App Service 應用程式，考慮將它們部署在不同的 App Service 方案中。 這種做法可讓您分別調整它們，因為它們在不同的執行個體上執行。 

同樣地，請考慮將函式應用程式放入它自己的方案中，使背景工作不在處理 HTTP 要求的相同執行個體上執行。 如果背景工作會間歇性地執行，請考慮使用[使用情況方案][functions-consumption-plan]，該方案是依據執行次數來計費，而不是每小時計費。 

### <a name="sql-database"></a>SQL Database
藉由將資料庫「分區」，提高 SQL 資料庫的延展性。 分區是指以水平方式分割資料庫。 分區可讓您使用[彈性資料庫工具][sql-elastic]以水平方式相應放大資料庫。 分區的潛在優點包括：

- 較佳的交易輸送量。
- 對資料子集合執行的查詢可以更快速。

### <a name="azure-search"></a>Azure 搜尋服務
Azure 搜尋服務替主要資料存放區省去了執行複雜資料搜尋的額外負荷，而且可加以調整以處理負載。 請參閱[在 Azure 搜尋服務中調整適用於查詢和編製索引工作負載的資源等級][azure-search-scaling]。

## <a name="security-considerations"></a>安全性考量
本節列出 Azure 服務專屬的安全性考量， 但不是安全性最佳做法的完整清單。 如需更多的安全性考量，請參閱[保護 Azure App Service 中的應用程式][app-service-security]。

### <a name="cross-origin-resource-sharing-cors"></a>跨原始來源資源分享 (CORS)
如果您將網站和 Web API 建立為不同的應用程式，則網站無法對 API 進行用戶端 AJAX 呼叫，除非您啟用 CORS。

> [!NOTE]
> 瀏覽器安全性可防止網頁對另一個網域提出 AJAX 要求。 這項限制稱為同源原則，可防止惡意網站從另一個網站讀取敏感性資料。 跨原始來源資源共用 (CORS) 是 W3C 標準，可讓伺服器放寬同源原則，允許一些跨來源要求，並拒絕其他要求。
> 
> 

App Service 已內建 CORS 支援，不需要再撰寫任何應用程式程式碼。 請參閱[使用 CORS 從 JavaScript 取用 API 應用程式][cors]。 將網站新增至 API 允許的來源清單。

### <a name="sql-database-encryption"></a>SQL Database 加密
如果您要加密資料庫中的靜止資料，使用[透明資料加密][sql-encryption]。 這個功能可執行整個資料庫 (包括備份和交易記錄) 的即時加密和解密，不需變更應用程式。 加密會增加一些延遲，所以最好的作法是將必須保護的資料另外放在它自己的資料庫中，只啟用該資料庫的加密。  
  

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
[sql-db]: https://azure.microsoft.com/documentation/services/sql-database/
[sql-elastic]: /azure/sql-database/sql-database-elastic-scale-introduction
[sql-encryption]: https://msdn.microsoft.com/library/dn948096.aspx
[tm]: https://azure.microsoft.com/services/traffic-manager/
[visio-download]: https://archcenter.blob.core.windows.net/cdn/app-service-reference-architectures.vsdx
[web-app-multi-region]: ./multi-region.md
[0]: ./images/scalable-web-app.png "Azure 中 Web 應用程式的延展性改善"

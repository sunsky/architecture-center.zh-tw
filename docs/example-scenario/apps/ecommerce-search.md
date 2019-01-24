---
title: 電子商務的智慧產品搜尋引擎
titleSuffix: Azure Example Scenarios
description: 提供電子商務應用程式中的世界級搜尋體驗。
author: jelledruyts
ms.date: 09/14/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: fasttrack
ms.openlocfilehash: fe67c891a9e42d5216fe6fd81de6ea1333d5bd37
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54488234"
---
# <a name="intelligent-product-search-engine-for-e-commerce"></a>電子商務的智慧產品搜尋引擎

此範例案例示範使用專用的搜尋服務，如何大幅增加電子商務客戶的搜尋結果相關性。

搜尋是可讓客戶尋找且最終購買產品的主要機制，因此一定要讓搜尋結果與搜尋查詢「意圖」相關，藉由提供幾近即時的結果、語言分析、地理位置比對、篩選、Faceting、自動完成、命中標示等，讓端對端搜尋經驗符合搜尋巨人的搜尋經驗。

假設有一個典型電子商務 Web 應用程式，其產品資料儲存在 SQL Server 或 Azure SQL Database 等關聯式資料庫中。 使用 `LIKE` 查詢或[全文檢索搜尋][docs-sql-fts]功能，通常會在資料庫內部處理搜尋查詢。 改用 [Azure 搜尋服務][docs-search]，即可將操作資料庫從查詢處理中釋出，並可輕鬆地開始利用這些難以實作的功能，進而為您的客戶提供最佳的搜尋體驗。 此外，因為 Azure 搜尋服務是平台即服務 (PaaS) 元件，所以您不必擔心管理基礎結構或成為搜尋專家。

## <a name="relevant-use-cases"></a>相關使用案例

其他相關的使用案例包括：

- 尋找使用者實體位置附近的房地產清單或商店。
- 搜尋新聞網站中的文章，或尋找運動賽事結果，而且偏好比較「近期」的資訊。
- 在大型存放庫中搜尋「以文件為主」的組織，例如政策制定者和公證人。

最終「任何」具有某種形式搜尋功能的應用程式均可受益於專用的搜尋服務。

## <a name="architecture"></a>架構

![電子商務用智慧型產品搜尋引擎相關 Azure 元件的架構概觀][architecture]

此案例涵蓋客戶可以搜尋產品目錄的電子商務解決方案。

1. 客戶可以從任何裝置瀏覽至**電子商務 Web 應用程式**。
2. 產品目錄會保留在 **Azure SQL Database**中進行交易處理。
3. Azure 搜尋服務會使用**搜尋索引子**，透過整合式變更追蹤使其搜尋索引自動保持最新狀態。
4. 客戶的搜尋查詢會卸載至 **Azure 搜尋服務**，該服務會處理查詢並傳回最相關的結果。
5. 除了網頁式搜尋體驗，客戶也可以在社交媒體中使用**交談式 Bot**，或直接從個人數位助理搜尋產品，並逐漸精簡其搜尋查詢和結果。
6. (選擇性) **認知搜尋**功能可用來套用人工智慧，甚至更聰明的處理。

### <a name="components"></a>元件

- [應用程式服務 - Web Apps][docs-webapps] 會裝載 Web 應用程式，允許自動調整及高可用性，而不需要管理基礎結構。
- [SQL Database][docs-sql-database] 是 Microsoft Azure 中的一般用途關聯式資料庫受控服務，可支援關聯式資料、JSON、空間和 XML 等結構。
- [Azure 搜尋服務][docs-search]是搜尋即服務雲端解決方案，透過 Web、行動和企業應用程式中的私用和異質內容來提供豐富的搜尋體驗。
- [Bot 服務][docs-botservice] 提供工具，可以建置、測試、部署及管理智慧型聊天機器人。
- [認知服務][docs-cognitive]可讓您使用智慧型演算法，透過自然的溝通方式，來查看、聆聽、述說、了解及詮釋您的使用者需求。

### <a name="alternatives"></a>替代項目

- 您可以使用**資料庫內搜尋**功能，例如，透過 SQL Server 全文檢索搜尋，但是您的交易存放區也會處理查詢 (增加處理能力的需求)，而且資料庫內的搜尋功能更加有限。
- 您可以在 Azure 虛擬機器上裝載開放原始碼的 [Apache Lucene][apache-lucene] (Azure 搜尋服務的建置基礎)，但是您會回到管理基礎結構即服務 (IaaS)，而且並未受益於 Azure 搜尋服務根據 Lucene 所提供的許多功能。
- 您也可以考慮從 Azure Marketplace 部署 [Elastic Search][elastic-marketplace]，這是來自第三方廠商的替代搜尋產品，但在此情況下您也會執行 IaaS 工作負載。

資料層的其他選項包括：

- [Cosmos DB](/azure/cosmos-db/introduction) - Microsoft 全球發行的多模型資料庫。 Cosmos DB 提供平台來執行其他資料模型，例如 Mongo DB、Cassandra、Graph 資料或簡單的資料表儲存體。 Azure 搜尋服務也支援直接從 Cosmos DB 將資料編製索引。

## <a name="considerations"></a>考量

### <a name="scalability"></a>延展性

Azure 搜尋服務的[定價層][search-tier]並不會決定可用的功能，但主要用於[容量規劃][search-capacity]，因為它會定義您可取得的最大儲存體，以及您可佈建的分割區和複本數目。 **分割區**可讓您將更多文件編製索引並取得更高的寫入輸送量，然而**複本**可提供更多每秒查詢次數 (QPS) 和高可用性。

您可以動態變更分割區和複本數目，但不可能變更定價層，因此您應該仔細考慮目標工作負載的適合階層。 如果您無論如何都需要變更階層，則必須並排佈建新的服務並在其中重新載入索引，以便屆時指向新服務上的應用程式。

### <a name="availability"></a>可用性

Azure 搜尋服務可提供 [99.9% 可用性 SLA][search-sla]：如果有至少兩個複本，則適用於「讀取」 (也就查詢)，而如果有至少三個複本，則適用於「更新」 (也就是更新搜尋索引)。 因此，如果您希望客戶能夠可靠地「搜尋」，則應該佈建至少兩個複本，而如果實際「索引變更」也應視為高可用性作業，則佈建三個複本。

如果需要在不停機的情況下，對索引進行重大變更 (例如，變更資料類型、刪除或重新命名欄位)，則必須重建索引。 類似於變更服務層，這表示建立新的索引、重新填入資料，然後將您的應用程式更新為指向新索引。

### <a name="security"></a>安全性

Azure 搜尋服務符合許多[安全性和資料隱私權標準][search-security]，因而可能使用於大部分的產業。

為了保護服務的存取權，Azure 搜尋服務會使用兩種金鑰：**系統管理金鑰**，這可讓您對此服務執行「任何」工作，以及**查詢金鑰**，這只能使用於查詢等唯讀作業。 執行搜尋的應用程式通常不會更新索引，因此只能以查詢金鑰設定，而不能以系統管理金鑰設定 (尤其是從使用者裝置執行搜尋時，例如在網頁瀏覽器中執行的指令碼)。

### <a name="search-relevance"></a>搜尋相關性

電子商務應用程式的成功程度主要取決於搜尋結果與您客戶的相關性。 仔細調整您的搜尋服務，以提供以使用者研究為基礎的最佳結果，或依賴內建功能 (例如[搜尋流量分析][search-analysis]) 來了解客戶的搜尋模式，可讓您根據資料做出決策。

微調搜尋服務的典型方式包括：

- 使用[評分設定檔][search-scoring]來影響搜尋結果的相關性，比方說，根據哪個欄位與查詢相符、資料有多新、使用者的地理距離等等。
- 使用 [Microsoft 提供的語言分析器][search-languages]，以便使用進階自然語言處理 (NLP) 堆疊進一步解譯查詢。
- 使用[自訂分析器][ search-analyzers]，確保正確找到您的產品，尤其在您想要搜尋非語言型資訊時，例如產品的製造商和型號。

## <a name="deploy-the-scenario"></a>部署案例

若要部署此案例更完整的電子商務版本，您可以遵循此[逐步教學課程][end-to-end-walkthrough]，其中提供執行簡單票證購買應用程式的 .NET 範例應用程式。 它還包含 Azure 搜尋服務，並使用許多討論的功能。 此外，還有 Resource Manager 範本，可以將大部分 Azure 資源的部署自動化。

## <a name="pricing"></a>價格

為了探索執行此案例的成本，上述所有服務都會在成本計算機中預先設定。 若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的使用量。

我們根據您預期取得的流量，提供了三個範例成本設定檔：

- [小型][small-pricing]：在此設定檔中，我們會使用單一 `Standard S1` Web 應用程式，來裝載網站、Azure Bot 服務的免費層、單一 `Basic` Azure 搜尋服務及 `Standard S2` SQL Database。
- [中型][medium-pricing]：在此我們會將 Web 應用程式相應增加為兩個 `Standard S3` 層執行個體、將搜尋服務升級為 `Standard S1` 層，以及使用 `Standard S6` SQL Database。
- [大型][large-pricing]：在最大的設定檔中，我們會使用四個 `Premium P2V2` Web 應用程式執行個體、將 Azure Bot 服務升級為 `Standard S1` 層 (進階通道中有 1.000.000 則訊息)、使用 2 單位的 `Standard S3` Azure 搜尋服務以及 `Premium P6` SQL Database。

## <a name="related-resources"></a>相關資源

若要深入了解 Azure 搜尋服務，請造訪[文件中心][docs-search]，查看[範例][search-samples]，或觀看完全成熟的運作中[示範網站][search-demo]。

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

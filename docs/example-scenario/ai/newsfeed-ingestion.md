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
# <a name="mass-ingestion-and-analysis-of-news-feeds-on-azure"></a>大量擷取和分析 Azure 上的新聞摘要

此範例案例說明大量擷取和接近即時的分析，使用公開 RSS 新聞饋送功能的文件的管線。  它會使用 Azure 認知服務提供有用的深入解析，包括文字翻譯、 臉部辨識和情感偵測。

這個案例包含的範例[英文][english]，[俄文][russian]，以及[德文][german]新聞摘要，但您可以輕鬆地延伸至其他 RSS 摘要。 為了便於部署，資料收集、 處理和分析以完全基礎 Azure 服務。

## <a name="relevant-use-cases"></a>相關使用案例

雖然此案例為基礎的 RSS 摘要的處理，它是適用於任何文件、 網站，或是會需要您的文章：

* 轉譯任何要選擇的語言的文字。
* 在數位內容中尋找主要片語、 實體和使用者的情感。
* 偵測與數位文件相關聯的映像中的物件、 文字和地標。
* 數位內容相關聯的任何映像中，偵測到由其性別和年齡的人。

## <a name="architecture"></a>架構

![][architecture]

整個解決方案的資料流程如下所示：

1. RSS 新聞摘要做為文件或文件從取得資料的產生器。 比方說，與發行項中，資料通常包含標題、 摘要的新聞項目中，原始的主體，而且有時候映像。

2. 產生器或擷取的程序會將文件和任何相關聯的影像插入至 Azure Cosmos DB[集合][collection]。

3. 通知會觸發 Azure Functions 儲存 CosmosDB 和文件中的映像 （如果有的話） Azure Blob 儲存體中的文章內文中的內嵌函式。  本文接著會傳遞至下一個佇列中。

4. Translate 函式是由佇列事件觸發。 它會使用[翻譯文字 API] [ translate-text]的 Azure 認知服務來偵測語言，但如果有必要，請將轉譯和人氣、 關鍵片語和實體從中收集本文和標題。 然後將文件傳遞至下一個佇列。

5. 從已排入佇列的發行項，就會觸發偵測函式。 它會使用[Computer Vision] [ vision]偵測物件、 地標和寫入的文字中相關聯的映像，然後給發行項的下一個佇列的服務。

6. 觸發臉部函式時就會觸發從已排入佇列的發行項。 它會使用[Azure 人臉識別 API] [ face]服務偵測所面臨的性別和年齡中相關聯的映像，然後將文件傳遞至下一個佇列。

7. 所有函式完成時，會觸發此通知函式。 它會載入已處理的記錄，發行項，並掃描其中有任何您想要的結果。 如果找不到，內容會標示，而且會傳送通知到您選擇的系統。

在每個處理步驟中，此函式會將結果寫入 Azure Cosmos DB。 最後，可以使用資料，視需要。 例如，您可以使用它來加強業務程序，找出新的客戶，或者找出客戶滿意度問題。

### <a name="components"></a>元件

此範例中使用下列 Azure 元件的清單。

* [Azure 儲存體][ storage]用來保存原始映像及影片與文章相關聯的檔案。 次要儲存體帳戶會透過 Azure App Service，以及用來裝載 Azure 函式程式碼和記錄檔。

* [Azure Cosmos DB] [ cosmos-db]保留文章文字、 影像和影片追蹤資訊。 認知服務步驟的結果也儲存在這裡。

* [Azure Functions] [ functions]執行函式程式碼來回應佇列的訊息，並轉換傳入的內容。 [Azure App Service] [ aas]裝載函式程式碼，並依序處理記錄。 此案例包含五個函式：擷取、 轉換、 偵測臉部的物件，並通知。

* [Azure 服務匯流排][ service-bus]裝載函式使用的 Azure 服務匯流排佇列。

* [Azure 認知服務][ acs] AI 提供的實作為基礎的管線[Computer Vision] [ vision]服務[臉部API][face]，以及[轉譯文字][ translate-text]機器翻譯服務。

* [Azure Application Insights] [ aai]提供分析可協助您診斷問題，並了解您的應用程式的功能。

### <a name="alternatives"></a>替代項目

* 而不是使用根據佇列通知和 Azure Functions 的模式，請對此資料流程中使用另一種模式。 例如， [Azure 服務匯流排主題][ topics]可以用來完成此範例中的序列處理而不是以平行方式為文件的各個部分的程序。 如需詳細資訊，請比較[佇列和主題][queues-topics]。

* 使用[Azure Logic App] [ logic-app]實作函式程式碼，並實作記錄層級鎖定這類[Redlock] [ redlock] （需要平行處理，直到 Azure Cosmos DB 支援[部分的文件更新][partial])。 如需詳細資訊，[比較 Functions 和 Logic Apps][compare]。

* 實作此架構中使用自訂的 AI 元件，而不是現有的 Azure 服務。 比方說，擴充管線使用中的映像，而不是泛型的人員計數、 性別，偵測到特定人員的自訂的模型，並在此範例中所收集資料的保留時間。 若要使用此架構的自訂的機器學習服務或人工智慧模型，建置模型做為 RESTful 端點，讓他們可以從 Azure 函式呼叫。

* 使用不同的輸入的機制，而不是 RSS 摘要。 使用多個產生器或擷取程序摘要 Azure Cosmos DB 和 Azure 儲存體。

## <a name="considerations"></a>考量

為了簡單起見，此範例案例中會使用只有幾個可用的 Api 和 Azure 認知服務的服務。 比方說，在映像中的文字可以使用分析[文字分析 API][text-analytics]。 假設在此案例中的目標語言是英文，但是您可以變更任何的輸入[支援的語言][ language]您選擇。

### <a name="scalability"></a>延展性

Azure Functions 縮放比例取決於[主控方案][ plan]您使用。 這個解決方案假設[耗用量計劃][plan-c]，哪些計算電源會自動配置給函式時所需。 只有當您的函式執行時需要付費。 另一個選項是使用[Azure App Service] [ plan-aas]計劃，可讓您配置不同的資源量的各層之間調整。

使用 Azure Cosmos DB，關鍵在於您的工作負載大致平均分配夠大幾部[資料分割索引鍵][keys]。 沒有任何限制的容器可以儲存的資料總量或總數量[輸送量][ throughput]可支援容器。

### <a name="management-and-logging"></a>管理和記錄

此解決方案會使用[Application Insights] [ aai]收集效能和記錄資訊。 與此部署所需的其他服務相同的資源群組中的部署建立的 Application Insights 執行個體。

若要檢視解決方案所產生的記錄檔：

1. 移至[Azure 入口網站][ portal]並瀏覽至建立部署的資源群組。

2. 按一下  **Application Insights**執行個體。

3. 從**Application Insights**區段中，瀏覽至**調查\\搜尋**和搜尋資料。

### <a name="security"></a>安全性

Azure Cosmos DB 會使用安全的連線和 C 透過共用的存取簽章\#Microsoft 所提供的 SDK。 沒有其他對外公開介面區。 深入了解安全性[最佳做法][ db-practices]適用於 Azure Cosmos DB。

## <a name="pricing"></a>價格

要保留這項部署的預估每日成本大約是\$20 美國不含系統中移動的資料。

Azure Cosmos DB 是功能強大，但會產生最大[成本][ db-cost]此部署中。 您可以使用另一個儲存體解決方案，重構提供的 Azure Functions 程式碼。

價格取決於不同的 Azure Functions[計劃][ function-plan]時執行。

## <a name="deploy-the-scenario"></a>部署案例

> [!NOTE]
> 您必須具有現有的 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [[免費帳戶]][free]。

此案例中的所有程式碼位於[GitHub] [ github]存放庫。 此存放庫包含用來建置摘要這段示範影片的管線產生器應用程式的原始程式碼。

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

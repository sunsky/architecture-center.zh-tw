---
title: Azure 上的保險理賠映像分類
description: 經過證明的案例，可以將映像處理建置到您的 Azure 應用程式。
author: david-stanford
ms.date: 07/05/2018
ms.openlocfilehash: 0ca0b46e83219afc5e22c2ac6467bf4be945c97a
ms.sourcegitcommit: c49aeef818d7dfe271bc4128b230cfc676f05230
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2018
ms.locfileid: "44389157"
---
# <a name="image-classification-for-insurance-claims-on-azure"></a>Azure 上的保險理賠映像分類

此範例案例適用於需要處理映像的企業。

潛在的應用程式包含分類潮流網站的映像、分析保險理賠的文字和映像，或了解來自遊戲螢幕擷取畫面的遙測資料。 在過去，公司需要開發機器學習服務模型的專業知識、定型模型，最終透過其自訂程序執行映像，以取得映像中的資料。

藉由使用 Azure 服務 (例如電腦視覺 API 和 Azure Functions)，公司可以消除管理個別伺服器的需求，同時減少成本及利用 Microsoft 已透過認知服務針對處理映像開發的專業知識。 這個範例案例特別說明映像處理的使用案例。 如果您有不同的 AI 需求，請考慮整套的[認知服務][cognitive-docs]。

## <a name="related-use-cases"></a>相關使用案例

請針對下列使用案例考慮此案例：

* 分類潮流網站上的映像。
* 分類來自遊戲螢幕擷取畫面的遙測資料。

## <a name="architecture"></a>架構

![智慧型應用程式架構 - 電腦視覺][architecture-computer-vision]

此案例涵蓋了 Web 或行動裝置應用程式的後端元件。 整個案例的資料流程如下所示：

1. Azure Functions 作為 API 層。 這些 API 可讓應用程式上傳映像，並且從 Cosmos DB 擷取資料。

2. 透過 API 呼叫來上傳映像時，映像會儲存在 Blob 儲存體。

3. 將新檔案新增至 Blob 儲存體，會觸發 EventGrid 通知傳送至 Azure Function。

4. Azure Functions 會將新上傳檔案的連結傳送至電腦視覺 API 以進行分析。

5. 一旦資料從電腦視覺 API 傳回，Azure Functions 會在 Cosmos DB 中製作輸入項，一併保存映像中繼資料與分析結果。

### <a name="components"></a>元件

* [電腦視覺 API][computer-vision-docs] 是認知服務套件的一部分，用來擷取每個映像的資訊。

* [Azure Functions][functions-docs] 提供 Web 應用程式的後端 API，以及已上傳映像的事件處理。

* [事件方格][eventgrid-docs]會在新映像上傳至 Blob 儲存體時觸發事件。 然後使用 Azure functions 處理映像。

* [Blob 儲存體][storage-docs]會儲存上傳至 Web 應用程式的所有映像檔案，以及 Web 應用程式取用的任何靜態檔案。

* [Cosmos DB][cosmos-docs] 會儲存每個已上傳映像的中繼資料，包括從電腦視覺 API 處理的結果。

## <a name="alternatives"></a>替代項目

* [自訂視覺服務][custom-vision-docs]。 電腦視覺 API 會傳回一組[分類法型分類][cv-categories]。 如果您需要處理並非由電腦視覺 API 所傳回的資訊，請考慮自訂視覺服務，它可讓您建置自訂映像分類器。

* [Azure 搜尋服務][azure-search-docs]。 如果您的使用案例牽涉到查詢中繼資料以尋找符合特定準則的映像，請考慮使用 Azure 搜尋服務。 [認知搜尋][cognitive-search] (目前處於預覽狀態) 與此工作流程緊密整合。

## <a name="considerations"></a>考量

### <a name="scalability"></a>延展性

此範例案例中使用的大部分元件都是會自動調整的受控服務。 幾個值得注意的例外狀況：Azure Functions 的上限為 200 個執行個體。 如果您需要調整超過此上限，請考慮多個區域或應用程式方案。

Cosmos DB 不會依據佈建要求單位 (RU) 自動調整。  如需評估需求的指引，請參閱我們文件中的[要求單位][request-units]。 若要充分利用 Cosmos DB 中的調整，請探索[分割區索引鍵][partition-key]。

NoSQL 資料庫會經常交換可用性、延展性及分割區的一致性 (CAP 定理的概念)。  在此範例案例中，會使用索引鍵-值資料模型，由於大部分作業定義為不可部分完成，所以不太需要交易一致性。 [選擇正確的資料存放區](../../guide/technology-choices/data-store-overview.md)的額外指引可於 Azure 架構中心找到。

如需設計可調整解決方案的一般指引，請參閱 Azure Architecture Center 中的[延展性檢查清單][scalability]。

### <a name="security"></a>安全性

[受控服務識別][msi] (MSI) 是用來提供帳戶內部其他資源的存取權，然後指派給您的 Azure Functions。 只允許以這些身分識別存取必要資源，確保沒有其他項目公開到您的函式 (而且可能公開給您的客戶)。  

如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。

### <a name="resiliency"></a>災害復原

此案例中的所有元件都是受控元件，所以它們在區域層級都會自動復原。

如需設計彈性解決方案的一般指引，請參閱[為 Azure 設計有彈性的應用程式][resiliency]。

## <a name="pricing"></a>價格

為了探索執行此案例的成本，所有服務會在成本計算機中預先設定。 若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的流量。

我們根據流量 (假設所有映像的大小是 100 kb)，提供了三個範例成本設定檔：

* [小型][pricing]：這個定價範例適用於每月處理 &lt; 5000 個映像。
* [中型][medium-pricing]：這個定價範例適用於每月處理 500,000 個映像。
* [大型][large-pricing]：這個定價範例適用於每月處理 5,000 萬個映像。

## <a name="related-resources"></a>相關資源

如需此案例的引導式學習路徑，請參閱[在 Azure 中建置無伺服器 Web 應用程式][serverless]。  

在將這個範例案例部署到生產環境之前，請檢閱 Azure Functions [最佳做法][functions-best-practices]。

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
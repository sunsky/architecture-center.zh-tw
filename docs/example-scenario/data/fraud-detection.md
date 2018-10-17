---
title: Azure 上的即時詐騙偵測
description: 使用 Azure 事件中樞和串流分析，即時偵測詐騙活動。
author: alexbuckgit
ms.date: 07/05/2018
ms.openlocfilehash: 4de988731aa1c5b0e4c0ba06fa5aed59e2bb7d81
ms.sourcegitcommit: b2a4eb132857afa70201e28d662f18458865a48e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48818661"
---
# <a name="real-time-fraud-detection-on-azure"></a>Azure 上的即時詐騙偵測

這個範例案例與需要即時分析資料以偵測詐騙交易或其他異常活動的組織相關。

潛在適用情形包括識別詐騙信用卡活動或行動電話通話。 傳統的線上分析系統可能需要數小時來轉換及分析資料，以識別異常活動。

藉由使用完全受控的 Azure 服務，例如事件中樞和串流分析，公司可以消除管理個別伺服器的需求，同時減少成本及利用 Microsoft 在雲端規模資料擷取和即時分析方面的專業知識。 這個案例特別說明詐騙活動的偵測。 如果您有其他資料分析的需求，應該檢閱可用 [Azure Analytics 服務][product-category]的清單。

這個範例代表更廣泛資料處理架構和策略的一部分。 這方面的其他選項和整體架構會在本文稍後討論。

## <a name="relevant-use-cases"></a>相關使用案例

請針對下列使用案例考慮此案例：

* 偵測電子通訊案例中的詐騙行動電話通話。
* 識別銀行機構的詐騙信用卡交易。
* 識別零售或電子商務案例中的詐騙購買。

## <a name="architecture"></a>架構

![即時詐騙偵測案例的 Azure 元件架構概觀][architecture]

此案例涵蓋了即時分析管線的後端元件。 整個案例的資料流程如下所示：

1. 行動電話通話中繼資料從來源系統傳送到 Azure 事件中樞執行個體。 
2. 串流分析作業隨即啟動，它會透過事件中樞來源接收資料。
3. 串流分析作業會執行預先定義的查詢以轉換輸入串流，並且根據詐騙交易演算法來分析它。 此查詢會使用輪轉視窗將串流分割成不同的時態性單位。
4. 串流分析作業會寫入轉換後的串流，該串流代表對於 Azure Blob 儲存體中輸出接收的已偵測詐騙通話。

### <a name="components"></a>元件

* [Azure 事件中樞][docs-event-hubs]是即時串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 在此案例中，事件中樞會接收要分析是否有詐騙活動的所有通話中繼資料。
* [Azure 串流分析][docs-stream-analytics]是事件處理引擎，可以分析來自裝置和其他資料來源的大量資料流。 它也支援從資料流擷取資訊，以識別模式和關聯性。 這些模式可以觸發其他下游動作。 在此案例中，串流分析會轉換來自事件中樞的輸入串流，以識別詐騙通話。
* 在此案例中使用 [Blob 儲存體](/azure/storage/blobs/storage-blobs-introduction)，以儲存串流分析作業的結果。

## <a name="considerations"></a>考量

### <a name="alternatives"></a>替代項目

許多技術選項都適用於即時訊息擷取、資料儲存、串流處理、分析資料儲存，以及分析和報告。 如需這些選項、其功能及重要選取準則的概觀，請參閱《Azure 資料架構指南》中的[巨量資料架構：即時處理](/azure/architecture/data-guide/technology-choices/real-time-ingestion)。

此外，Azure 中各種機器學習服務可以產生適用於詐騙偵測的更複雜演算法。 如需這些選項的概觀，請參閱《[Azure 資料架構指南](../../data-guide/index.md)》中的[適用於機器學習的技術選項](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)。

### <a name="availability"></a>可用性

「Azure 監視器」提供統一的使用者介面，可供您監視各個不同的 Azure 服務。 如需詳細資訊，請參閱[在 Microsoft Azure 中監視](/azure/monitoring-and-diagnostics/monitoring-overview)。 事件中樞和串流分析都與 Azure 監視器整合在一起。 

如需其他可用性考量，請參閱 Azure Architecture Center 中的[可用性檢查清單][availability]。

### <a name="scalability"></a>延展性

此案例的元件是專為超大規模擷取與大量平行即時分析而設計。 Azure 事件中樞可高度調整，每秒可接收和處理數百萬個事件且低延遲。 事件中樞可以[自動相應增加](/azure/event-hubs/event-hubs-auto-inflate)輸送量單位數，來符合使用量需求。 Azure 串流分析可以分析來自許多來源的大量串流資料。 您可以藉由增加配置來執行串流作業的[串流單位](/azure/stream-analytics/stream-analytics-streaming-unit-consumption)數，以相應增加串流分析。

如需設計可調整案例的一般指引，請參閱 Azure Architecture Center 中的[延展性檢查清單][scalability]。

### <a name="security"></a>安全性

Azure 事件中樞是以根據共用存取簽章 (SAS) 權杖和事件發行者組合的[驗證和安全性模型][docs-event-hubs-security-model]，來保護資料。 事件發行者會定義事件中樞的虛擬端點。 發行者只能用來將訊息傳送到事件中樞。 您無法接收來自發佈者的訊息。

如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。

### <a name="resiliency"></a>災害復原

如需設計彈性解決方案的一般指引，請參閱[為 Azure 設計有彈性的應用程式][resiliency]。

## <a name="deploy-the-scenario"></a>部署案例

若要部署此案例，您可以遵循示範如何以手動方式部署案例每個元件的這個[逐步教學課程][tutorial]。 本教學課程也會提供 .NET 用戶端應用程式，以產生範例通話中繼資料，並將該資料傳送至事件中樞執行個體。

## <a name="pricing"></a>價格

為了探索執行此案例的成本，所有服務會在成本計算機中預先設定。 若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的資料量。

我們根據您預期取得的流量，提供了三個範例成本設定檔：

* [小型][small-pricing]：每月透過 1 個標準串流單位處理 100 萬個事件。
* [中型][medium-pricing]：每月透過 5 個標準串流單位處理 1 億個事件。
* [大型][large-pricing]：每月透過 20 個標準串流單位處理 9 億 9900 萬個事件。

## <a name="related-resources"></a>相關資源

更複雜的詐騙偵測案例受益於機器學習模型。 如需使用 Machine Learning Server 建置的案例，請參閱[使用 Machine Learning Server 的詐騙偵測][r-server-fraud-detection]。 如需使用 Machine Learning Server 的其他解決方案範本，請參閱[資料科學案例和解決方案範本][docs-r-server-sample-solutions]。 如需使用 Azure Data Lake Analytics 的範例解決方案，請參閱[使用 Azure Data Lake 和 R 進行詐騙偵測][technet-fraud-detection]。

<!-- links -->
[product-category]: https://azure.microsoft.com/product-categories/analytics/
[tutorial]: /azure/stream-analytics/stream-analytics-real-time-fraud-detection
[small-pricing]: https://azure.com/e/74149ec312c049ccba79bfb3cfa67606
[medium-pricing]: https://azure.com/e/4fc94f7376de484d8ae67a6958cae60a
[large-pricing]: https://azure.com/e/7da8804396f9428a984578700003ba42
[architecture]: ./media/architecture-fraud-detection.png
[docs-event-hubs]: /azure/event-hubs/event-hubs-what-is-event-hubs
[docs-event-hubs-security-model]: /azure/event-hubs/event-hubs-authentication-and-security-model-overview
[docs-stream-analytics]: /azure/stream-analytics/stream-analytics-introduction
[docs-r-server-sample-solutions]: /machine-learning-server/r/sample-solutions
[r-server-fraud-detection]: https://microsoft.github.io/r-server-fraud-detection/
[technet-fraud-detection]: https://blogs.technet.microsoft.com/machinelearning/2017/06/28/using-azure-data-lake-and-r-for-fraud-detection/
[availability]: /azure/architecture/checklist/availability
[scalability]: /azure/architecture/checklist/scalability
[resiliency]: ../../resiliency/index.md
[security]: /azure/security/


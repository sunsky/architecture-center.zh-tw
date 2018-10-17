---
title: 擷取和處理即時汽車 IoT 資料
description: 使用 IoT 擷取和處理即時車輛資料。
author: meeral
ms.date: 09/12/2018
ms.openlocfilehash: 663332185f64987215384a1d4af4b7ed9b50847c
ms.sourcegitcommit: 62945777e519d650159f0f963a2489b6bb6ce094
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2018
ms.locfileid: "48876880"
---
# <a name="ingestion-and-processing-of-real-time-automotive-iot-data"></a>擷取和處理即時汽車 IoT 資料

此範例案例會建置即時資料擷取和處理管線，將來自 IoT 裝置 (在一般感應器中) 的訊息擷取到 Azure 的巨量資料分析平台中，然後加以處理。 車輛車載資通訊 (Telematics) 擷取和處理平台是建立已連線汽車解決方案的關鍵。 此特定案例是由汽車車載資通訊擷取和處理系統所驅使。 不過，對於許多使用感應器來管理和監視複雜系統的產業，例如智慧型建築、通訊、製造、零售和醫療保健產業，其設計模式關係重大。

此範例示範車載 IoT 裝置訊息的即時資料擷取和處理管線。 IoT 裝置和感應器會產生數以百萬計的訊息 (或事件)。 藉由擷取和分析這些訊息，我們即可獲得寶貴的見解並採取適當的動作。 比方說，利用配備車載資通訊裝置的汽車，如果我們可以即時擷取裝置 (IoT) 訊息，我們就能夠監視車輛的即時位置、規劃最佳路線、對駕駛提供協助，以及支援車載資通訊相關產業，例如自動保險。

針對此範例示範，想像有一家汽車製造公司想要建立一套即時系統，擷取及處理來自車載資通訊裝置的訊息。 公司的目標包括：
* 即時擷取和儲存來自車輛、感應器和裝置的資料。
* 分析訊息以了解車輛位置，以及其他透過不同類型的感應器 (例如引擎相關感應器和環境相關感應器) 所發出的資訊。
* 在分析之後儲存資料，以供進行其他下游處理，進而提供可採取行動的見解 (例如，在意外事故案例中，保險機構可能會想要知道意外事故過程中發生什麼事情等)。

## <a name="relevant-use-cases"></a>相關使用案例

在建立車載資通訊擷取和處理系統時，請針對下列使用案例及上述目標考量此案例：

* 車輛維護提醒和警示。
* 車輛乘客的行動定位服務 (也就是 SOS)。
* 自動駕駛的車輛。

## <a name="architecture"></a>架構

![螢幕擷取畫面](media/architecture-realtime-analytics-vehicle-data1.png)

在典型的巨量資料處理管線實作中，資料會由左到右流動。 在此即時巨量資料處理管線中，資料會如下所示流經解決方案：

1. 從 IoT 資料來源產生的事件會透過 Azure HDInsight Kafka，以一連串訊息的方式傳送至資料流擷取層。 HDInsight Kafka 會將資料流儲存在主題中 (您可設定時間長度)。
2. Kafka 取用者 (Azure Databricks) 會從 Kafka 主題中即時挑選訊息，以根據商務邏輯處理資料，然後傳送到服務層進行儲存。
3. 然後，下游儲存服務 (例如 Azure Cosmos DB、Azure SQL 資料倉儲或 Azure SQL DB) 會成為簡報和動作層級的資料來源。
4. 商務分析師可使用 Microsoft Power BI 來分析倉儲中的資料。 您也可以根據服務層建置其他應用程式。 例如，我們可以根據服務層資料公開 API，以供第三方使用。

### <a name="components"></a>元件
使用下列 Azure 元件，擷取、處理 IoT 裝置所產生的事件 (資料或訊息)，然後儲存起來，以供進一步分析、進行簡報及採取動作：
* [Apache Kafka on HDInsight](/azure/hdinsight/kafka/apache-kafka-introduction) 位於擷取層中。 使用 Kafka 產生者 API 將資料寫入至 Kafka 主題。
* [Azure Databricks](/services/databricks) 位於轉換和分析層。 Databricks Notebook 會實作 Kafka 取用者 API，以從 Kafka 主題讀取資料。
* [Azure Cosmos DB](/services/cosmos-db)、[Azure SQL Database](/azure/sql-database/sql-database-technical-overview) 和 Azure SQL 資料倉儲位於儲存體服務層中，Azure Databricks 可以透過資料連接器在其中寫入資料。
* [Azure SQL 資料倉儲](/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)是一套分散式系統，用於儲存和分析大型資料集。 它會使用大量平行處理 (MPP)，因而適合用來執行高效能分析。
* [Power BI](https://docs.microsoft.com/power-bi) 是一套商務分析工具，用來分析資料及分享見解。 Power BI 可以查詢 Analysis Services 中儲存的語意模型，也可以直接查詢 SQL 資料倉儲。
* [Azure Active Directory (Azure AD)](/azure/active-directory) 會在連線至 [Azure Databricks](https://azure.microsoft.com/services/databricks) 時，驗證使用者。 如果我們要根據以 Azure SQL 資料倉儲資料為基礎的模型，在 [Analysis Services](/azure/analysis-services) 中建立 Cube，我們可以透過 Power BI 使用 AAD 來連線到 Analysis Services 伺服器。 Data Factory 也可以透過服務主體或受控服務識別 (MSI)，使用 Azure AD 來驗證 SQL 資料倉儲。
* [Azure App Service](/azure/app-service/app-service-web-overview)，尤其是 [API 應用程式](/services/app-service/api)可根據服務層中儲存的資料向第三方公開資料。

## <a name="alternatives"></a>替代項目

![螢幕擷取畫面](media/architecture-realtime-analytics-vehicle-data2.png)

使用其他 Azure 元件，可以實作更廣義的巨量資料管線。
* 在資料流擷取層中，我們可以使用 [IoT 中樞](https://azure.microsoft.com/services/iot-hub)或[事件中樞](https://azure.microsoft.com/services/event-hubs)，而非使用 [HDInsight Kafka](/azure/hdinsight/kafka/apache-kafka-introduction) 來擷取資料。
* 在轉換和分析層中，我們可以使用 [HDInsight Storm](/azure/hdinsight/storm/apache-storm-overview)、[HDInsight Spark](/azure/hdinsight/spark/apache-spark-overview) 或 [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics)。
* [Analysis Services](/azure/analysis-services) 可提供您資料的語意模型。 也可以在分析資料時提升系統效能。 您可以根據 Azure DW 資料建置模型。

## <a name="considerations"></a>考量

根據處理事件、服務 SLA、成本管理和元件管理方便性所需的規模，選擇此架構中的技術。
* 隨附 99.9% SLA 的受控 [HDInsight Kafka](/azure/hdinsight/kafka/apache-kafka-introduction) 會與 Azure 受控磁碟整合
* [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) 已針對在雲端中的效能和成本效益，從零開始最佳化。 Databricks Runtime 將數個重要功能新增至 Apache Spark 工作負載，以提升效能並降低成本 (效能是在 Azure 上執行時的 10-100 倍)，這些功能包括：
* Azure Databricks 會與 Azure 資料庫和存放區深入整合：[Azure SQL Data Warehouse](/azure/sql-data-warehouse)、[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db)、[Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage) 和 [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs)
    * 自動調整和自動終止 Spark 叢集，以自動將成本降至最低。
    * 效能最佳化包括快取、編製索引，以及進階查詢最佳化，這可改善效能，達到雲端或內部部署環境中傳統 Apache Spark 部署的 10-100 倍。
    * 與 Azure Active Directory 整合可讓您使用 Azure Databricks 執行完整的 Azure 型解決方案。
    * Azure Databricks 中的角色型存取可讓您針對 Notebook、叢集、作業和資料提供更細緻的使用者權限。
    * 隨附企業級 SLA。
* Azure Cosmos DB 是 Microsoft 全球發行的多模型資料庫。 全新打造的 Azure Cosmos DB 具備全域散發功能，且可依其核心進行水平調整。 不論您的使用者身在何處，都可以透明調整及複寫您的資料，以周全地全域散發到任何數目的 Azure 區域。 您可以在世界各地彈性地調整輸送量和儲存體規模，並且只支付所需輸送量和儲存體的費用。
* SQL 資料倉儲的大量平行處理架構可提供延展性和高效能。
* Azure SQL 資料倉儲具有保證的 SLA 以及達到高可用性的建議做法。
* 當分析活動變少時，公司可以隨需調整 Azure SQL 資料倉儲、減少或甚至暫停計算，以降低成本。
* Azure SQL 資料倉儲安全性模型可透過 Azure AD 或 SQL Server 驗證和加密，提供連線安全性、驗證和授權。

## <a name="pricing"></a>價格

透過 Azure 定價計算機，檢閱 [Azure Databricks 定價](https://azure.microsoft.com/pricing/details/databricks)、[Azure HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight)、[資料倉儲案例的定價範例](https://azure.com/e/b798fb70c53e4dd19fdeacea4db78276)。 調整一些值，以查看需求對於成本有何影響。
* [Azure HDInsight](/azure/hdinsight) 是管理完善的雲端服務，不僅可以簡化處理大量資料的程序，而且速度快，成本低。
* [Azure Databricks](https://azure.microsoft.com/services/databricks) 在多個 [VM 執行個體](https://azure.microsoft.com/pricing/details/databricks/#instances)上提供專為資料分析工作流程打造的兩種不同之工作負載：「資料工程」工作負載可讓資料工程師能夠輕鬆建置及執行作業，而「資料分析」工作負載則能讓資料科學家以互動方式探索、具體呈現、操控及共用資料與深入解析。
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) 保證世界各地第 99 個百分位數的個位數毫秒延遲時間，提供[多個定義完善的一致性模型](/azure/cosmos-db/consistency-levels)以微調效能，並保證透過多路連接功能提供高可用性 - 全都享有領先業界之全方位[服務等級協定](https://azure.microsoft.com/support/legal/sla/cosmos-db) (SLA) 的安心保障。
* [Azure SQL 資料倉儲](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2)可讓您獨立調整計算和儲存體層級。 計算資源需每小時付費，而您可以依照需求調整或暫停這些資源。 儲存體資源會依照 TB 計費，因此成本會隨著您內嵌更多資料而增加。
* [Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services) 會以開發人員、基本及標準層提供。 執行個體的定價是以查詢處理單位 (QPU) 和可用的記憶體為基礎。 為了保持較低的成本，請將您執行的查詢數目、其所處理的資料數量，以及其執行頻率降至最低。
* [Power BI](https://powerbi.microsoft.com/pricing) 有不同的產品選項可滿足不同的需求。 [Power BI Embedded](https://azure.microsoft.com/pricing/details/power-bi-embedded) 針對您的應用程式內嵌的 Power BI 功能提供一個 Azure 型選項。 Power BI Embedded 執行個體包含在上面的定價範例中。

## <a name="next-steps"></a>後續步驟

* 檢閱[即時分析](https://azure.microsoft.com/solutions/architecture/real-time-analytics)參考架構，其中包括巨量資料管線流程。
* 檢閱[進階巨量資料分析](https://azure.microsoft.com/solutions/architecture/advanced-analytics-on-big-data)參考架構，以窺視不同的 Azure 元件如何協助建置巨量資料管線。
* 閱讀 Azure [即時處理](/azure/architecture/data-guide/big-data/real-time-processing)文件，以快速檢視不同的 Azure 元件如何協助即時處理資料流。
* 在 [Azure 資料架構指南](/azure/architecture/data-guide)中尋找有關資料管線、資料倉儲、線上分析處理 (OLAP) 和巨量資料的完整架構指引。

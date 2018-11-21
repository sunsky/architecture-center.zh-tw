---
title: 營建產業的 IoT 和資料分析
description: 使用 IoT 裝置和資料分析，提供營建專案的全方位管理和作業。
author: alexbuckgit
ms.date: 08/29/2018
ms.openlocfilehash: 74868191687e63a54a69fdacb7276983d98faf74
ms.sourcegitcommit: 0a31fad9b68d54e2858314ca5fe6cba6c6b95ae4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51610918"
---
# <a name="iot-and-data-analytics-in-the-construction-industry"></a>營建產業的 IoT 和資料分析

此範例案例與建置解決方案的組織有關，這類組織會將許多 IoT 裝置中的資料整合到完整的資料分析架構中，以改善和自動執行決策。 潛在的應用程式包括營建、採礦、製造或其他產業解決方案，包含許多 IoT 型資料輸入中的大量資料。

在此案例中，營建設備製造商會建造車輛、計量器，及使用 IoT 和 GPS 技術來發出遙測資料的無人機。 該公司想要將其資料架構現代化，以便更妥善地監控作業狀況和設備健康情況。 使用內部部署基礎結構取代公司的傳統解決方案，可能需要投注大量時間和人力，而且無法充分調整，以處理預期的資料量。

該公司想要建置雲端式「智慧型營建」解決方案。 它應該蒐集一組完整的營建工地資料，並自動執行各種工地元素的作業和維護。 公司的目標包括：

* 整合及分析所有營建工地設備和資料，將設備停機時間降至最低並減少竊取行為。
* 在遠端自動控制營建設備，以減緩人力不足的影響，而終極目標則是所需的工作人員比較少，並且讓低技術的工作人員有所成就。
* 將支援基礎結構的運作成本和人力需求降至最低，同時提高生產力和安全性。
* 輕鬆地調整基礎結構，以支援遙測資料增加。
* 在不會危害系統可用性的情況下，於境內佈建資源，以符合所有相關法律需求。
* 使用開放原始碼軟體，將工作人員的目前技能投資最大化。

使用 Azure 受控服務 (例如 IoT 中樞與 HDInsight)，可讓客戶快速建置及部署具有較低運作成本的完整解決方案。 如果您有其他資料分析需求，則應檢閱 Azure 中可用的[完全受控分析服務][product-category]清單。

## <a name="relevant-use-cases"></a>相關使用案例

其他相關的使用案例包括：

* 營建、 採礦或設備製造案例
* 大規模收集裝置資料以便儲存和分析
* 擷取及分析大型資料集

## <a name="architecture"></a>架構

![營建產業的 IoT 和資料分析架構][architecture]

整個解決方案的資料流程如下所示：

1. 營建設備會收集感應器資料，並且定期傳送營建結果資料，以平衡 Azure 虛擬機器叢集上裝載的 Web 服務負載。
2. 自訂 Web 服務會擷取營建結果資料，並將它儲存於同樣在 Azure 虛擬機器上執行的 Apache Cassandra 叢集中。
3. 各種營建設備上的 IoT 感應器會蒐集另一個資料集並傳送至 IoT 中樞。
4. 所收集的原始資料會直接從 IoT 中樞傳送至 Azure blob 儲存體，且立即可供檢視和分析。
5. 透過 IoT 中樞所收集的資料是由 Azure Stream Analytics 作業以近乎即時的方式處理，並儲存在 Azure SQL 資料庫中。
6. Smart Construction Cloud Web 應用程式可供分析師和使用者檢視，並分析感應器資料和影像。 
7. Web 應用程式的使用者可以視需要起始批次作業。 批次作業會在 HDInsight 上的 Apache Spark 中執行，並分析 Cassandra 叢集中儲存的新資料。 

### <a name="components"></a>元件

* [IoT 中樞](/azure/iot-hub/about-iot-hub)可作為中央訊息中樞，以便在雲端平台與營建設備和其他工地元素之間使用各裝置的身分識別進行安全的雙向通訊。 IoT 中樞可快速收集每個裝置的資料，以便擷取到資料分析管線。 
* [Azure 串流分析](/azure/stream-analytics/stream-analytics-introduction)是事件處理引擎，可以分析來自裝置和其他資料來源的大量資料流。 它也支援從資料流擷取資訊，以識別模式和關聯性。 在此案例中，Stream Analytics 會擷取和分析 IoT 裝置的資料，並將結果儲存在 Azure SQL Database 中。 
* [Azure SQL Database](/azure/sql-database/sql-database-technical-overview) 包含來自 IoT 裝置和計量器的已分析資料結果，而分析師和使用者可以透過 Azure 型 Web 應用程式來檢視結果。 
* [Blob 儲存體](/azure/storage/blobs/storage-blobs-introduction)可存放從 IoT 中樞裝置蒐集的影像資料。 透過 Web 應用程式可以檢視影像資料。
* [流量管理員](/azure/traffic-manager/traffic-manager-overview)會為不同 Azure 區域的服務端點，控制使用者流量的散佈情況。
* [Load Balancer](/azure/load-balancer/load-balancer-overview) 會將來自營建設備裝置的資料提交散發到所有 VM 型 Web 服務，以提供高可用性。
* [Azure 虛擬機器](/azure/virtual-machines)會裝載一些 Web 服務，以接收營建結果資料並內嵌到 Apache Cassandra 資料庫中。
* [Apache Cassandra](https://cassandra.apache.org) 是一個分散式 NoSQL 資料庫，用來儲存營建資料以供 Apache Spark 稍後處理。
* [Web Apps](/azure/app-service/app-service-web-overview) 會裝載使用者 Web 應用程式，而這些應用程式可用來查詢及檢視來源資料與影像。 使用者也可以在 Apache Spark 中透過應用程式起始批次作業。
* [HDInsight 上的 Apache Spark](/azure/hdinsight/spark/apache-spark-overview) 會平行處理可支援記憶體內部處理的架構，以大幅提升巨量資料分析應用程式的效能。 在此案例中，Spark 用來對 Apache Cassandra 中儲存的資料執行複雜的演算法。


### <a name="alternatives"></a>替代項目

* [Cosmos DB](/azure/cosmos-db/introduction) 是替代的 NoSQL 資料庫技術。 Cosmos DB 可提供[全球規模的多主機支援](/azure/cosmos-db/multi-region-writers)，包含[多個定義完善的一致性層級](/azure/cosmos-db/consistency-levels)，以符合各種客戶需求。 它也支援 [Cassandra API](/azure/cosmos-db/cassandra-introduction)。 
* [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) 是針對 Azure 最佳化的 Apache Spark 型分析平台。 他可與 Azure 整合，提供一鍵式設定、順暢的工作流程，以及互動式共同作業工作區。
* [Data Lake 儲存體](/azure/storage/data-lake-storage)是 Blob 儲存體的替代項目。 此案例中，Data Lake 儲存體無法使用於目標區域。
* [Web Apps](/azure/app-service) 也可用來裝載 Web 服務，以便擷取營建結果資料。
* 許多技術選項都適用於即時訊息擷取、資料儲存、串流處理、分析資料儲存，以及分析和報告。 如需這些選項、其功能及重要選取準則的概觀，請參閱《[Azure 資料架構指南](/azure/architecture/data-guide)》中的[巨量資料架構：即時處理](/azure/architecture/data-guide/technology-choices/real-time-ingestion)。

## <a name="considerations"></a>考量

Azure 區域的廣泛可用性是此案例的重要因素。 在單一國家/地區中有多個區域不僅可提供災害復原，同時也能符合契約義務和法規需求。 Azure 在區域間的高速通訊也是此案例的重要因素。

Azure 的開放原始碼技術支援，讓客戶得以利用其現有的人力技能。 客戶也可以使用相較於內部部署解決方案較低的成本和運作工作負載，來加速新技術的採用。 

## <a name="pricing"></a>價格

下列考量會影響此解決方案的大部分成本。

* 佈建額外的執行個體時，Azure 虛擬機器成本會以線性方式增加。 解除配置的虛擬機器只會產生儲存體成本，而不會產生計算成本。 當要求很高時，即可重新配置這些已解除配置的機器。
* [IoT 中樞](https://azure.microsoft.com/pricing/details/iot-hub)成本是由已佈建的 IoT 單位數以及所選的服務層所驅使，可決定每個單位每天所允許的訊息數目。 
* [串流分析](https://azure.microsoft.com/pricing/details/stream-analytics)會依據將資料處理到服務中所需的串流處理單位數計價。

## <a name="related-resources"></a>相關資源

若要查看類似架構的實作，請閱讀 [Komatsu 客戶案例][customer-story]。

在 [Azure 資料架構指南](/azure/architecture/data-guide)中可取得巨量資料架構的指引。

<!-- links -->
[product-category]: https://azure.microsoft.com/product-categories/analytics/
[customer-site]: https://home.komatsu/en/
[customer-story]: https://customers.microsoft.com/story/komatsu-manufacturing-azure-iot-hub-japan
[architecture]: ./media/architecture-big-data-with-iot.png

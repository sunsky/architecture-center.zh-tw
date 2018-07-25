---
title: Azure 上可調整的訂單處理
description: 使用 Azure Cosmos DB 建置高度可調整訂單處理管線的範例案例。
author: alexbuckgit
ms.date: 07/10/2018
ms.openlocfilehash: 541b5e9f523c64bc55526e4e2dffc57a5212e67f
ms.sourcegitcommit: 71cbef121c40ef36e2d6e3a088cb85c4260599b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060979"
---
# <a name="scalable-order-processing-on-azure"></a>Azure 上可調整的訂單處理

此範例案例與需要線上訂單處理高度可調整和彈性架構的組織相關。 潛在的應用程式包括電子商務和零售銷售點、訂單履行及庫存保留和追蹤。 

此案例會採用事件來源方法，使用透過微服務實作的功能程式設計模型。 系統會將每個微服務視為串流處理器，所有商務邏輯都是透過微服務實作。 這個方法可提供高可用性和復原、異地複寫及快速效能。

使用受控 Azure 服務 (例如 Cosmos DB 和 HDInsight)，可以藉由利用 Microsoft 在全域分散式雲端規模資料儲存和擷取方面的專業知識，協助降低成本。 此案例特別說明電子商務或零售案例，如果您有其他資料服務的需求，應該檢閱可用的 [Azure 中完全受控智慧型資料庫服務][product-category]清單。

## <a name="related-use-cases"></a>相關使用案例

請針對下列使用案例考慮此案例：

* 電子商務或零售銷售點後端系統。
* 庫存管理系統。
* 訂單履行系統。
* 其他與訂單處理管線相關的整合案例。

## <a name="architecture"></a>架構

![可調整訂單處理管線的範例架構][architecture-diagram]

此架構會詳細說明訂單處理管線的主要元件。 整個案例的資料流程如下所示：

1. 事件訊息會透過客戶對應應用程式 (透過 HTTP 同步) 和各種後端系統 (透過 Apache Kafka 非同步) 輸入系統。 這些訊息會傳遞至命令處理管線。
2. 每個事件訊息會由命令處理器微服務擷取並對應至其中一個已定義命令集。 命令處理器會從事件串流快照集資料庫擷取與執行命令相關的任何目前狀態。 然後會執行命令，命令輸出會發出為新事件。
3. 系統會使用 Cosmos DB 將發出為命令輸出的每個事件認可至事件串流資料庫。
4. 針對認可至事件串流資料庫的每個資料庫插入或更新，事件會由 Cosmos DB 變更摘要引發。 下游系統可以訂閱與該系統相關的任何事件主題。
5. 來自 Cosmos DB 變更摘要的所有事件也會傳送到快照集事件串流微服務，這個微服務會計算由已發生事件所造成的任何狀態變更。 然後新的狀態會認可至儲存在 Cosmos DB 中的事件串流快照集資料庫。  快照集資料庫為所有資料元素的目前狀態，提供全域分散式、低延遲資料來源。 事件串流資料庫提供所有事件訊息的完整記錄，這些事件訊息已通過架構，而這個架構提供強固的測試、疑難排解及災害復原案例。  

### <a name="components"></a>元件

* [Cosmos DB][docs-cosmos-db] 是 Microsoft 的全域分散式、多模型資料庫，可讓您的解決方案有彈性且獨立地調整任意數量地理區域之間的輸送量和儲存體。 它利用完整的服務等級協定 (SLA) 提供了輸送量、延遲、可用性和一致性的保證。 此案例會針對事件串流儲存體與快照集儲存體使用 Cosmos DB，而且會利用 Cosmos DB 的變更摘要功能來提供資料一致性和錯誤復原。 
* [HDInsight 上的 Apache Kafka][docs-kafka] 是 Apache Kafka 的受控服務實作，這是一個開放原始碼分散式串流平台，可以建置即時串流資料管線和應用程式。 Kafka 也提供類似於訊息佇列的訊息代理程式功能，可以發佈和訂閱具名資料流。 此案例會使用 Kafka 來處理傳入事件以及訂單處理管線中的下游事件。 

## <a name="considerations"></a>考量

許多技術選項都適用於即時訊息擷取、資料儲存、串流處理、分析資料儲存，以及分析和報告。 如需這些選項、其功能及重要選取準則的概觀，請參閱《[Azure 資料架構指南](/azure/architecture/data-guide/)》中的[巨量資料架構：即時處理](/azure/architecture/data-guide/technology-choices/real-time-ingestion)。

微服務已成為熱門的架構樣式，用於建置可復原、高延展性、可獨立部署，以及能夠快速發展的雲端應用程式。 微服務需要不同的方法來設計和建置應用程式。 例如 Docker、Kubernetes、Azure Service Fabric 及 Nomad 的工具可以進行微服務型架構的開發。 如需建置及執行微服務型架構的指引，請參閱 Azure Architecture Center 中的[在 Azure 上設計微服務](/azure/architecture/microservices/)。

### <a name="availability"></a>可用性

此案例的事件來源方法可讓系統元件鬆散結合，並且彼此獨立地部署。 Cosmos DB 提供[高可用性][docs-cosmos-db-regional-failover]並且協助組織管理與一致性、可用性及效能相關聯的利弊取捨，都有[對應保證][docs-cosmos-db-guarantees]。 HDInsight 上的 Apache Kafka 也是針對[高可用性][docs-kafka-high-availability]進行設計的。

「Azure 監視器」提供統一的使用者介面，可供您監視各個不同的 Azure 服務。 如需詳細資訊，請參閱[在 Microsoft Azure 中監視](/azure/monitoring-and-diagnostics/monitoring-overview)。 事件中樞和串流分析都與 Azure 監視器整合在一起。 

如需其他可用性考量，請參閱[可用性檢查清單][availability]。

### <a name="scalability"></a>延展性

HDInsight 上的 Kafka 可以針對 Kafka 叢集進行[儲存體和延展性的設定](/azure/hdinsight/kafka/apache-kafka-scalability)。 Cosmos DB 會隨著您的應用程式成長，提供快速、可預測的效能和[順暢調整](/azure/cosmos-db/partition-data)。
此案例的事件來源微服務型架構也會讓調整系統及擴充其功能更加容易。

如需其他延展性考量，請參閱 Azure Architecture Center 的[延展性檢查清單][scalability]。

### <a name="security"></a>安全性

[Cosmos DB 安全性模型](/azure/cosmos-db/secure-access-to-data)會驗證使用者，並提供其資料和資源的存取權。 如需詳細資訊，請參閱 [Cosmos DB 資料庫安全性](/en-us/azure/cosmos-db/database-security)。

如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。

### <a name="resiliency"></a>災害復原

此範例案例中的事件來源架構和相關聯技術，可以讓此案例在發生失敗時具有高度復原能力。 如需設計彈性解決方案的一般指引，請參閱[為 Azure 設計有彈性的應用程式][resiliency]。

## <a name="pricing"></a>價格

為了檢查執行此案例的成本，所有服務會在成本計算機中預先設定。  若要查看價格如何針對您的特定案例而變更，請變更適當的變數，以符合您預期的資料量。 針對此案例，範例價格只包含 Cosmos DB 和 Kafka 叢集，用來處理由 Cosmos DB 變更摘要引發的事件。 原始系統和其他下游系統的事件處理器和微服務不包含在內，而且它們的成本高度相依於這些服務的數量和規模，以及為了實作所選擇的技術。

Azure Cosmos DB 的貨幣是「要求單位 (RU)」。 使用要求單位，就不需要保留讀取/寫入容量，或是佈建 CPU、記憶體及 IOPS。 Azure Cosmos DB 支援各種 API 來執行不同的作業，範圍可從簡單地讀取及寫入到複雜的圖表查詢。 因為並非所有要求都一樣，所以系統會根據處理要求所需的計算量，為要求指派標準化的要求單位數量。 您的解決方案所需的要求單位數量，是依據資料元素大小和每秒資料庫讀取和寫入作業量。 如需詳細資訊，請參閱 [Azure Cosmos DB 中的要求單位](/azure/cosmos-db/request-units)。 這些預估價格是根據在兩個 Azure 區域中執行的 Cosmos DB。

我們根據您預期的活動量，提供了三個範例成本設定檔：

* [小型][small-pricing]：這個設定檔適用於已保留 5 RU，且在 Cosmos DB 和小型 (D3 v2) Kafka 叢集中具有 1TB 資料存放區。
* [中型][medium-pricing]：這個設定檔適用於已保留 50 RU，且在 Cosmos DB 和中型 (D4 v2) Kafka 叢集中具有 10TB 資料存放區。
* [大型][large-pricing]：這個設定檔適用於已保留 500 RU，且在 Cosmos DB 和大型 (D5 v2) Kafka 叢集中具有 30TB 資料存放區。

## <a name="related-resources"></a>相關資源

此範例案例是根據由 [Jet.com](https://jet.com) 針對其端對端訂單處理管線建置的此架構更廣泛版本。 如需詳細資訊，請參閱 [jet.com 技術客戶設定檔][source-document]和 [jet.com 的 Build 2018 簡報][source-presentation]。 

其他相關資源包括：
* _[設計資料密集應用程式](https://dataintensive.net/)_ (英文)，Martin Kleppmann (O'Reilly Media，2017 年)。
* _[網域模型實用：使用網域驅動的設計和 F# 來解決軟體複雜度](https://pragprog.com/book/swdddf/domain-modeling-made-functional)_ (英文)，Scott Wlaschin (Pragmatic Programmers LLC，2018 年)。
* 其他 [Cosmos DB 使用案例][docs-cosmos-db-use-cases]
* 《[Azure 資料架構指南](/azure/architecture/data-guide/)》中的[即時處理架構](/azure/architecture/data-guide/big-data/real-time-processing)

<!-- links -->
[product-category]: https://azure.microsoft.com/product-categories/databases/
[source-document]: https://customers.microsoft.com/en-us/story/jet-com-powers-innovative-e-commerce-engine-on-azure-in-less-than-12-months
[source-presentation]: https://channel9.msdn.com/events/Build/2018/BRK3602
[small-pricing]: https://azure.com/e/3d43949ffbb945a88cc0a126dc3a0e6e
[medium-pricing]: https://azure.com/e/1f1e7bf2a6ad4f7799581211f4369b9b
[large-pricing]: https://azure.com/e/75207172ece94cf6b5fb354a2252b333
[architecture-diagram]: ./images/architecture-diagram-cosmos-db.png
[docs-cosmos-db]: /azure/cosmos-db
[docs-cosmos-db-change-feed]: /azure/cosmos-db/change-feed
[docs-cosmos-db-regional-failover]: /azure/cosmos-db/regional-failover
[docs-cosmos-db-guarantees]: /azure/cosmos-db/distribute-data-globally#AvailabilityGuarantees
[docs-cosmos-db-use-cases]: /azure/cosmos-db/use-cases
[docs-kafka]: /azure/hdinsight/kafka/apache-kafka-introduction
[docs-kafka-high-availability]: /azure/hdinsight/kafka/apache-kafka-high-availability
[docs-event-hubs]: /azure/event-hubs/event-hubs-what-is-event-hubs
[docs-stream-analytics]: /azure/stream-analytics/stream-analytics-introduction
[docs-blob-storage]: /azure/storage/blobs/storage-blobs-introduction
[availability]: /azure/architecture/checklist/availability
[scalability]: /azure/architecture/checklist/scalability
[resiliency]: /azure/architecture/patterns/category/resiliency/
[security]: /azure/security/

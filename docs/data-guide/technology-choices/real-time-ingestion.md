---
title: "選擇即時訊息擷取技術"
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 4f76e63a50c1d689ea3a37219a44aa94477a2e2e
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2018
---
# <a name="choosing-a-real-time-message-ingestion-technology-in-azure"></a>在 Azure 中選擇即時訊息擷取技術

即時處理可處理即時擷取的資料流，並以延遲性最低的方式加以處理。 許多即時處理解決方案需要有訊息擷取存放區，以作為訊息的緩衝區，以及支援相應放大處理、可靠的傳遞和其他訊息佇列語意。 

## <a name="what-are-your-options-for-real-time-message-ingestion"></a>即時訊息擷取有哪些選項？

- [Azure 事件中樞](/azure/event-hubs/)
- [Azure IoT 中心](/azure/iot-hub/)
- [HDInsight 上的 Kafka](/azure/hdinsight/kafka/apache-kafka-get-started)

## <a name="azure-event-hubs"></a>Azure 事件中心

[Azure 事件中樞](/azure/event-hubs/)是可高度調整的資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 事件中樞可提供低延遲性、大規模的發佈-訂閱功能，因而適用於巨量資料案例。

## <a name="azure-iot-hub"></a>Azure IoT 中樞

[Azure IoT 中樞](/azure/iot-hub/)是一項受控服務，可在數百萬個 IoT 裝置和一個雲端式後端之間支援可靠且安全的雙向通訊。

IoT 中樞的功能包括：

* 多個裝置到雲端和雲端到裝置的通訊選項。 這些選項包括單向通訊、檔案傳輸和要求回覆方法。
* 對其他 Azure 服務的訊息路由。
* 裝置中繼資料與同步化狀態資訊的可查詢存放區。
* 使用個別裝置安全性金鑰或 X.509 憑證的安全通訊與存取控制。
* 可監視裝置的連線情況和裝置的身分識別管理事件。

就訊息擷取的角度而言，IoT 中樞與事件中樞相類似。 不過，前者是專為管理 IoT 裝置連線 (而不只是訊息擷取) 而設計的。 如需詳細資訊，請參閱 [Azure IoT 中樞和 Azure 事件中樞的比較](/azure/iot-hub/iot-hub-compare-event-hubs)。 

## <a name="kafka-on-hdinsight"></a>HDInsight 上的 Kafka

[Apache Kafka](https://kafka.apache.org/) 是開放原始碼分散式串流平台，可用來建置即時資料管線和串流應用程式。 Kafka 也提供類似於訊息佇列的訊息代理程式功能，可讓您發佈和訂閱具名資料流。 它可進行水平擴充、具備容錯能力，且速度極快。 [HDInsight 上的 Kafka](/azure/hdinsight/kafka/apache-kafka-get-started) 可為您提供 Kafka，作為 Azure 中的受控、具備高度調整性和高可用性的服務。 

Kafka 的常見使用案例包括：

* **傳訊**。 Kafka 支援發佈-訂閱傳訊模式，因此常會作為訊息代理程式。
* **活動追蹤**。 Kafka 能夠依序登載記錄，因此可用來追蹤和重新建立活動，例如網站上的使用者動作。
* **彙總**。 您可以利用串流處理來彙總不同串流的資訊，將資訊結合並集中而成為可操作的資料。
* **轉換**。 您可以利用串流處理來結合並充實多個輸入主題的資料，而成為一或多個輸出主題。

## <a name="key-selection-criteria"></a>關鍵選取準則

為了縮小選擇範圍，請先回答下列問題：

- 您的 IoT 裝置與 Azure 之間是否需要雙向通訊？ 如果是，請選擇 IoT 中樞。

- 您需要管理對個別裝置的存取，並且能夠撤銷對特定裝置的存取嗎？ 如果是，請選擇 IoT 中樞。

## <a name="capability-matrix"></a>功能對照表

下表摘錄主要的功能差異。 

| | IoT 中樞 | 事件中樞 | HDInsight 上的 Kafka |
| --- | --- | --- | --- |
| 雲端到裝置通訊 | yes | 否 | 否 |
| 裝置起始的檔案上傳 | yes | 否 | 否 |
| 裝置狀態資訊 | [裝置對應項](/azure/iot-hub/iot-hub-devguide-device-twins) | 否 | 否 |
| 通訊協定支援 | MQTT、AMQP、HTTPS <sup>1</sup> | AMQP、HTTPS | [Kafka 通訊協定](https://cwiki.apache.org/confluence/display/KAFKA/A+Guide+To+The+Kafka+Protocol) |
| 安全性 | 提供個別裝置身分識別，可撤銷的存取控制。 | 共用存取原則，透過發行者原則的有限撤銷。 | 使用 SASL 的驗證，插入式授權，與支援的外部驗證服務整合。 |

[1] 您也可以使用 [Azure IoT 通訊協定閘道](/azure/iot-hub/iot-hub-protocol-gateway)作為自訂閘道，為 IoT 中樞啟用通訊協定調適。

如需詳細資訊，請參閱 [Azure IoT 中樞和 Azure 事件中樞的比較](/azure/iot-hub/iot-hub-compare-event-hubss)。
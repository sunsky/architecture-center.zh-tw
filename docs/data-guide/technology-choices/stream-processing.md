---
title: 選擇串流處理技術
description: ''
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: fd93418c62b584e79f229e9f42703d148aeb0eca
ms.sourcegitcommit: e9d9e214529edd0dc78df5bda29615b8fafd0e56
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2018
ms.locfileid: "37091058"
---
# <a name="choosing-a-stream-processing-technology-in-azure"></a>在 Azure 中選擇串流處理技術

本文將比較 Azure 中的即時串流處理適用的技術選項。

即時串流處理會使用來自佇列或檔案型儲存體的訊息、處理訊息，然後將結果轉送至其他訊息佇列、檔案存放區或資料庫。 此處理可能包括訊息的查詢、篩選和彙總。 串流處理引擎必須能夠使用無數的資料流，並以最低延遲產生結果。 如需詳細資訊，請參閱[即時處理](../big-data/real-time-processing.md)。

## <a name="what-are-your-options-when-choosing-a-technology-for-real-time-processing"></a>選擇即時處理的技術時有哪些選項？
在 Azure 中，下列所有資料存放區都將符合支援即時處理的核心需求：
- [Azure 串流分析](/azure/stream-analytics/)
- [使用 Spark Streaming 的 HDInsight](/azure/hdinsight/spark/apache-spark-streaming-overview)
- [Azure Databricks 中的 Apache Spark](/azure/azure-databricks/)
- [使用 Storm 的 HDInsight](/azure/hdinsight/storm/apache-storm-overview)
- [Azure Functions](/azure/azure-functions/functions-overview)
- [Azure App Service WebJobs](/azure/app-service/web-sites-create-web-jobs)

## <a name="key-selection-criteria"></a>重要選取準則

對於即時處理案例，請藉由回答下列問題來選擇符合需求的適當服務：

- 您想要以宣告式還是命令式方法來撰寫串流處理邏輯？

- 您是否需要時序處理或時間範圍的內建支援？

- 您的資料是否會以 Avro、JSON 或 CSV 以外的格式送達？ 如果是，請考慮使用支援任何採用自訂程式碼之格式的選項。

- 您是否需要將處理速率調整為 1 GB/s 以上？ 如果是，請考慮使用會以叢集大小進行調整的選項。 

## <a name="capability-matrix"></a>功能對照表

下表摘要列出各項功能的主要差異。 

### <a name="general-capabilities"></a>一般功能

| | Azure 串流分析 | 使用 Spark Streaming 的 HDInsight | Azure Databricks 中的 Apache Spark | 使用 Storm 的 HDInsight | Azure Functions | Azure App Service WebJobs |
| --- | --- | --- | --- | --- | --- | --- | 
| 可程式性 | 串流分析查詢語言，JavaScript | Scala、Python、Java | Scala、Python、Java、R | Java、C# | C#、F#、Node.js | C#、Node.js、PHP、Java、Python |
| 程式設計架構 | 宣告式 | 宣告式和命令式混合 | 宣告式和命令式混合 | 命令式 | 命令式 | 命令式 |    
| 定價模式 | [串流處理單位](https://azure.microsoft.com/pricing/details/stream-analytics/) | 每小時叢集 | [Databricks 單位](https://azure.microsoft.com/pricing/details/databricks/) | 每小時叢集 | 依函式執行和資源耗用量 | 依 App Service 方案時數 |  

### <a name="integration-capabilities"></a>整合功能

| | Azure 串流分析 | 使用 Spark Streaming 的 HDInsight | Azure Databricks 中的 Apache Spark | 使用 Storm 的 HDInsight | Azure Functions | Azure App Service WebJobs |
| --- | --- | --- | --- | --- | --- | --- | 
| 輸入 | Azure 事件中樞、Azure IoT 中樞、Azure Blob 儲存體  | 事件中樞、IoT 中樞、Kafka、HDFS、儲存體 Blob、Azure Data Lake Store  | 事件中樞、IoT 中樞、Kafka、HDFS、儲存體 Blob、Azure Data Lake Store  | 事件中樞、IoT 中樞、儲存體 Blob、Azure Data Lake Store  | [支援的繫結](/azure/azure-functions/functions-triggers-bindings#supported-bindings) | 服務匯流排、儲存體佇列、儲存體 Blob、事件中樞、Webhook、Cosmos DB、檔案 |
| 接收 |  Azure Data Lake Store、Azure SQL Database、儲存體 Blob、事件中樞、Power BI、表格儲存體、服務匯流排佇列、服務匯流排主題、Cosmos DB、Azure Functions  | HDFS、Kafka、儲存體 Blob、Azure Data Lake Store、Cosmos DB | HDFS、Kafka、儲存體 Blob、Azure Data Lake Store、Cosmos DB | 事件中樞、服務匯流排、Kafka | [支援的繫結](/azure/azure-functions/functions-triggers-bindings#supported-bindings) | 服務匯流排、儲存體佇列、儲存體 Blob、事件中樞、Webhook、Cosmos DB、檔案 | 

### <a name="processing-capabilities"></a>處理功能

| | Azure 串流分析 | 使用 Spark Streaming 的 HDInsight | Azure Databricks 中的 Apache Spark | 使用 Storm 的 HDInsight | Azure Functions | Azure App Service WebJobs |
| --- | --- | --- | --- | --- | --- | --- | 
| 內建時序/時間範圍支援 | yes | yes | yes | yes | 否 | 否 |
| 輸入資料格式 | Avro、JSON 或 CSV、UTF-8 編碼 | 任何使用自訂程式碼的格式 | 任何使用自訂程式碼的格式 | 任何使用自訂程式碼的格式 | 任何使用自訂程式碼的格式 | 任何使用自訂程式碼的格式 |
| 延展性 | [查詢分割區](/azure/stream-analytics/stream-analytics-parallelization) | 依叢集大小設定範圍 | 受限於 Databricks 叢集調整組態 | 依叢集大小設定範圍 | 最多可平行處理 200 個函式應用程式執行個體 | 依 App Service 方案容量設定範圍 | 
| 延遲送達和失序事件處理支援 | yes | yes | yes | yes | 否 | 否 |

另請參閱：

- [選擇即時訊息擷取技術](./real-time-ingestion.md)
- [比較 Apache Storm 與 Azure Stream Analytics](/azure/stream-analytics/stream-analytics-comparison-storm)
- [即時處理](../big-data/real-time-processing.md)

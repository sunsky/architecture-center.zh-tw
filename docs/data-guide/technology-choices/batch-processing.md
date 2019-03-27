---
title: 選擇批次處理技術
description: ''
author: zoinerTejada
ms.date: 11/03/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: 53f8b233b0e0c1ff83a72a04b2707caa528d6f6b
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58248513"
---
# <a name="choosing-a-batch-processing-technology-in-azure"></a>在 Azure 中選擇批次處理技術

巨量資料解決方案通常使用長時間執行的批次作業來篩選、彙總和準備資料以供分析。 這些作業通常包括從可擴充儲存體 (例如 HDFS、Azure Data Lake Store 與 Azure 儲存體) 讀取原始檔並加以處理，以及將輸出寫入到可擴充儲存體中的新檔案。

這類批次處理引擎的關鍵需求是相應放大計算，以便處理大量資料的能力。 但不像即時處理，批次處理應該會有以分鐘到小時測量的延遲 (擷取資料與計算結果之間的時間)。

## <a name="technology-choices-for-batch-processing"></a>批次處理的技術選擇

### <a name="azure-sql-data-warehouse"></a>Azure SQL 資料倉儲

[SQL 資料倉儲](/azure/sql-data-warehouse/)是為了對大型資料執行分析而設計的分散式系統。 它支援大量平行處理 (MPP)，因而適合用來執行高效能分析。 當您的資料龐大 (超過 1 TB)，且您的分析工作負載透過平行處理來執行又會比較好的話，請考慮使用 SQL 資料倉儲。

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

[Data Lake Analytics](/azure/data-lake-analytics/data-lake-analytics-overview) 是隨選分析作業服務。 針對分散處理儲存在 Azure Data Lake Store 中非常大量的資料集而最佳化。

- 語言：[U-SQL](/azure/data-lake-analytics/data-lake-analytics-u-sql-get-started) (包括 Python、R 及 C# 擴充功能)。
- 與 Azure Data Lake Store、Azure 儲存體 Blob、Azure SQL Database 及 SQL 資料倉儲整合。
- 定價模型是針對各個作業。

### <a name="hdinsight"></a>HDInsight

HDInsight 是受控 Hadoop 服務。 使用它以在 Azure 中部署及管理 Hadoop 叢集。 針對批次處理，您可以使用 [Spark](/azure/hdinsight/spark/apache-spark-overview)、[Hive](/azure/hdinsight/hadoop/hdinsight-use-hive)、[Hive LLAP](/azure/hdinsight/interactive-query/apache-interactive-query-get-started)、[MapReduce](/azure/hdinsight/hadoop/hdinsight-use-mapreduce)。

- 語言：R、Python、Java、Scala、SQL
- 使用 Active Directory、Apache Ranger 型存取控制進行Kerberos 驗證
- 給予您 Hadoop 叢集的完整控制權

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/azure-databricks/) 是 Apache Spark 型分析平台。 您可以將它視為「Spark 即服務」。 它是在 Azure 平台上使用 Spark 的最簡單方式。

- 語言：R、Python、Java、Scala、Spark SQL
- 加快叢集開始時間、自動終止、自動調整。
- 為您管理 Spark 叢集。
- 內建與 Azure Blob 儲存體、Azure Data Lake Storage (ADLS)、Azure SQL 資料倉儲 (SQL DW) 及其他服務的整合。 請參閱[資料來源](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) (英文)。
- 使用 Azure Active Directory 進行使用者驗證。
- 適用於共同作業和資料探索的網頁型 [Notebook](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) (英文)。
- 支援[已啟用 GPU 功能的叢集](https://docs.azuredatabricks.net/user-guide/clusters/gpu.html) (英文)

### <a name="azure-distributed-data-engineering-toolkit"></a>Azure 分散式資料工程工具組

[分散式資料工程工具組](https://github.com/azure/aztk) (英文) (AZTK) 是一種工具，用來在 Azure 中的 Docker 叢集上佈建隨選 Spark。

AZTK 不是 Azure 服務。 而是建置在 Azure Batch 上，具有 CLI 和 Python SDK 介面的用戶端工具。 此選項可讓您在部署 Spark 叢集時具有基礎結構的大部分控制權。

- 攜帶您自己的 Docker 映像。
- 使用低優先順序 VM 以獲得 80% 折扣。
- 混合模式叢集是同時使用低優先順序和專用 VM。
- 內建對於 Azure Blob 儲存體和 Azure Data Lake 連線的支援。

## <a name="key-selection-criteria"></a>重要選取準則

若要縮小選項範圍，請開始回答這些問題：

- 您要受控服務，而不是自行管理自己的伺服器？

- 否要以宣告或命令的方式撰寫批處理邏輯？

- 您是否有突然要大量執行批次處理的情況？ 如果是，請考慮可讓您自動終止叢集的選項，或其計價模式是以每個批次作業為單位的選項。

- 是否需要同時進行查詢關聯式資料存放區以及批次處理，例如查閱參考資料？ 如果是，請考慮啟用外部關聯式存放區查詢的選項。

## <a name="capability-matrix"></a>相容性矩陣

下表摘要列出各項功能的主要差異。

### <a name="general-capabilities"></a>一般功能

<!-- markdownlint-disable MD033 -->

| | Azure Data Lake Analytics | Azure SQL 資料倉儲 | HDInsight | Azure Databricks |
| --- | --- | --- | --- | --- | --- |
| 屬於受控服務 | yes | yes | 是 <sup>1</sup> | yes |
| 關聯式資料存放區 | yes | 是 | 否 | 否 |
| 定價模式 | 以每個批次作業為單位 | 依叢集小時 | 依叢集時數 | Databricks 單位 <sup>2</sup> + 叢集時數 |

[1] 使用手動設定和調整。

[2] 一個 Databricks 單位 (DBU) 即一個單位的每小時處理能力。

### <a name="capabilities"></a>功能

| | Azure Data Lake Analytics | SQL 資料倉儲 | 具有 Spark 的 HDInsight | 具有 Hive 的 HDInsight | 具有 Hive LLAP 的 HDInsight | Azure Databricks |
| --- | --- | --- | --- | --- | --- | --- |
| 自動調整 | 否 | 否 | 否 | 否 | 否 | yes |
| 向外延展細微性  | 每個作業 | 每個叢集 | 每個叢集 | 每個叢集 | 每個叢集 | 每個叢集 |
| 資料的記憶體快取 | 否 | yes | 是 | 否 | yes | yes |
| 從外部關聯式存放區查詢 | yes | 否 | yes | 否 | 否 | yes |
| Authentication  | Azure AD | SQL / Azure AD | 否 | Azure AD<sup>1</sup> | Azure AD<sup>1</sup> | Azure AD |
| 稽核  | yes | 是 | 否 | 是 <sup>1</sup> | 是 <sup>1</sup> | yes |
| 資料列層級安全性 | 否 | 否 | 否 | 是 <sup>1</sup> | 是 <sup>1</sup> | 否 |
| 支援防火牆 | yes | 是 | yes | 是 <sup>2</sup> | 是 <sup>2</sup> | 否 |
| 動態資料遮罩 | 否 | 否 | 否 | 是 <sup>1</sup> | 是 <sup>1</sup> | 否 |

<!-- markdownlint-enable MD033 -->

[1] 使用[已加入網域的 HDInsight 叢集](/azure/hdinsight/domain-joined/apache-domain-joined-introduction)時所需。

[2] [在 Azure 虛擬網路中使用](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)時支援。

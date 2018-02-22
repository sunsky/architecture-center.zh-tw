---
title: "選擇批次處理技術"
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: bfb850ee8e9d8fd41927b4ca3b612e15b5ae6b11
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2018
---
# <a name="choosing-a-batch-processing-technology-in-azure"></a>在 Azure 中選擇批次處理技術

巨量資料解決方案通常使用長時間執行的批次作業來篩選、彙總和準備資料以供分析。 這些作業通常包括從可擴充儲存體 (例如 HDFS、Azure Data Lake Store 與 Azure 儲存體) 讀取原始檔並加以處理，以及將輸出寫入到可擴充儲存體中的新檔案。 

這類批次處理引擎的關鍵需求是相應放大計算，以便處理大量資料的能力。 但不像即時處理，批次處理應該會有以分鐘到小時測量的延遲 (擷取資料與計算結果之間的時間)。

## <a name="what-are-your-options-when-choosing-a-batch-processing-technology"></a>選擇批次處理技術時有那些選項？

在 Azure 中，下列所有資料存放區將符合批次處理的核心需求：

- [Azure Data Lake Analytics](/azure/data-lake-analytics/)
- [Azure SQL 資料倉儲](/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)
- [具有 Spark 的 HDInsight](/azure/hdinsight/spark/apache-spark-overview)
- [具有 Hive 的 HDInsight](/azure/hdinsight/hadoop/hdinsight-use-hive)
- [具有 Hive LLAP 的 HDInsight](/azure/hdinsight/interactive-query/apache-interactive-query-get-started)

## <a name="key-selection-criteria"></a>關鍵選取準則

若要縮小選項範圍，請開始回答這些問題：

- 您要受控服務，而不是自行管理自己的伺服器？

- 否要以宣告或命令的方式撰寫批處理邏輯？

- 您是否有突然要大量執行批次處理的情況？ 如果是，請考慮可讓您暫停叢集的選項，或其計價模式是以每個批次作業為單位的選項。

- 是否需要同時進行查詢關聯式資料存放區以及批次處理，例如查閱參考資料？ 如果是，請考慮啟用外部關聯式存放區查詢的選項。

## <a name="capability-matrix"></a>相容性矩陣

下表是主要功能差異的摘要。 

### <a name="general-capabilities"></a>一般功能

| | Azure Data Lake Analytics | Azure SQL 資料倉儲 | 具有 Spark 的 HDInsight | 具有 Hive 的 HDInsight | 具有 Hive LLAP 的 HDInsight |
| --- | --- | --- | --- | --- | --- |
| 為受控服務 | yes | yes | 是 <sup>1</sup> | 是 <sup>1</sup> | 是 <sup>1</sup> |
| 支援計算暫停 | 否 | yes | 否 | 否 | 否 |
| 關聯式資料存放區 | yes | yes | 否 | 否 | 否 |
| 可程式性 | U-SQL | T-SQL | Python、Scala、Java、R | HiveQL | HiveQL |
| 程式設計架構 | 宣告式和命令式混合  | 宣告式 | 宣告式和命令式混合 | 宣告式 | 宣告式 | 
| 定價模式 | 以每個批次作業為單位 | 依叢集小時 | 依叢集小時 | 依叢集小時 | 依叢集小時 |  

[1] 使用手動設定和調整。
 
### <a name="integration-capabilities"></a>整合功能
| | Azure Data Lake Analytics | SQL 資料倉儲 | 具有 Spark 的 HDInsight | 具有 Hive 的 HDInsight | 具有 Hive LLAP 的 HDInsight |
| --- | --- | --- | --- | --- | --- |
| 從 Azure Data Lake Store 存取 | yes | yes | yes | yes | yes |
| 從 Azure 儲存體查詢 | yes | yes | yes | yes | yes |
| 從外部關聯式存放區查詢 | yes | 否 | yes | 否 | 否 |

### <a name="scalability-capabilities"></a>延展性功能
| | Azure Data Lake Analytics | SQL 資料倉儲 | 具有 Spark 的 HDInsight | 具有 Hive 的 HDInsight | 具有 Hive LLAP 的 HDInsight |
| --- | --- | --- | --- | --- | --- |
| 向外延展細微性  | 每個作業 | 每個叢集 | 每個叢集 | 每個叢集 | 每個叢集 |
| 快速相應放大 (小於 1 分鐘) | yes | yes | 否 | 否 | 否 |
| 資料的記憶體快取 | 否 | yes | yes | 否 | yes | 

### <a name="security-capabilities"></a>安全性功能
| | Azure Data Lake Analytics | SQL 資料倉儲 | 具有 Spark 的 HDInsight | HDInsight 上的 Apache Hive | HDInsight 上的 Hive LLAP |
| --- | --- | --- | --- | --- | --- |
| 驗證  | Azure Active Directory (Azure AD) | SQL / Azure AD | 否 | 本機 / Azure AD <sup>1</sup> | 本機 / Azure AD <sup>1</sup> |
| Authorization  | yes | yes| 否 | 是 <sup>1</sup> | 是 <sup>1</sup> |
| 稽核  | yes | yes | 否 | 是 <sup>1</sup> | 是 <sup>1</sup> |
| 待用資料加密 | yes| 是 <sup>2</sup> | yes | yes | yes |
| 資料列層級安全性 | 否 | yes | 否 | 是 <sup>1</sup> | 是 <sup>1</sup> |
| 支援防火牆 | yes | yes | yes | 是 <sup>3</sup> | 是 <sup>3</sup> |
| 動態資料遮罩 | 否 | 否 | 否 | 是 <sup>1</sup> | 是 <sup>1</sup> |

[1] 使用[已加入網域的 HDInsight 叢集](/azure/hdinsight/domain-joined/apache-domain-joined-introduction)時所需。

[2] 使用透明資料加密 (TDE) 來加密和解密待用資料時所需。

[3] [在 Azure 虛擬網路中使用](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)時支援。

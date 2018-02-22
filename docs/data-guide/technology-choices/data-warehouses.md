---
title: "選擇資料倉儲"
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 9cb3d4d0196b02da76d85c7f7f0e4a2a69d531e9
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2018
---
# <a name="choosing-a-data-warehouse-in-azure"></a>在 Azure 中選擇資料倉儲

資料倉儲是位於中心且具組織性的關聯式存放庫，用來存放從一個或多個不同來源整合的資料。 本主題會比較 Azure 中的資料倉儲選項。

> [!NOTE]
> 如需何時使用資料倉儲的詳細資訊，請參閱[資料倉儲和資料超市](../scenarios/data-warehousing.md)。

## <a name="what-are-your-options-when-choosing-a-data-warehouse"></a>選擇資料倉儲時有哪些選項？

在 Azure 中實作資料倉儲有多種選項，您可以依據您的需求做選擇。 下列清單分成兩個類別，[對稱式多處理](https://en.wikipedia.org/wiki/Symmetric_multiprocessing) (SMP) 和[大量平行處理](https://en.wikipedia.org/wiki/Massively_parallel) (MPP)。 

SMP：

- [Azure SQL Database](/azure/sql-database/)
- [虛擬機器中的 SQL Server](/sql/sql-server/sql-server-technical-documentation)

MPP：

- [Azure 資料倉儲](/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)
- [HDInsight 上的 Apache Hive](/azure/hdinsight/hadoop/hdinsight-use-hive)
- [HDInsight 上的互動式查詢 (Hive LLAP)](/azure/hdinsight/interactive-query/apache-interactive-query-get-started)

一般而言，SMP 架構的倉儲最適用於小型至中型資料集 (上限為 4-100 TB)，而 MPP 通常用於巨量資料。 中/小和巨量資料之間的劃分，有部分與您組織的定義和支援基礎結構有關。 (請參閱[選擇 OLTP 資料存放區](oltp-data-stores.md#scalability-capabilities)。) 

除了資料大小，工作負載模式的類型可能是更大的決定因素。 比方說，SMP 解決方案用於複雜的查詢可能會太慢，反而需要 MPP 解決方案。 作業在節點之間散發和彙總的方式，可能會使得 MPP 系統對小型資料的效能造成負面影響。 如果您的資料大小已超過 1 TB，並預期要持續成長，請考慮選取 MPP 解決方案。 不過，如果您的資料大小少於 1 TB，但您的工作負載即將超過 SMP 解決方案的可用資源，則 MPP 可能是最佳的選項。

資料倉儲存取或儲存的資料可能來自許多資料來源，包括 Data Lake，例如 [Azure Data Lake Store](/azure/data-lake-store/)。 如需針對使用 Azure Data Lake 的 MPP 服務進行不同的優點比較，請參閱此視訊研討會＜[Azure Data Lake 和 Azure 資料倉儲：將現代做法套用至您的應用程式](https://azure.microsoft.com/resources/videos/build-2016-azure-data-lake-and-azure-data-warehouse-applying-modern-practices-to-your-app/)＞。

SMP 系統的特性是關聯式資料庫管理系統的單一執行個體會共用所有資源 (CPU/記憶體/磁碟)。 您可以相應增加 SMP 系統。 對於 VM 上執行的 SQL Server 而言，您可以相應增加 VM 大小。 對於 Azure SQL Database，您可以選取不同的服務層以相應增加。 

您可以新增更多計算節點 (其中有他們自己的 CPU、記憶體和 I/O 子系統) 來相應放大 MPP 系統。 對伺服器進行相應增加存在實體限制，此時進行相應放大會更為理想 (視工作負載而定)。 不過，由於查詢、塑模、資料分割，及其他平行處理獨有的其他因素中存在差異，MPP 解決方案需要不同的技能集。 

決定要使用哪一種 SMP 解決方案時，請參閱[仔細看看 Azure SQL Database 和 Azure VM 上的 SQL Server](/azure/sql-database/sql-database-paas-vs-sql-server-iaas#a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms)。 

Azure SQL 資料倉儲也適用於小型及中型資料集，其中的工作負載屬於運算和記憶體密集型。 深入了解 SQL 資料倉儲模式和常見案例：

- [SQL 資料倉儲模式和反向模式](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/)
- [SQL 資料倉儲載入模式和策略](https://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [將資料移轉至 Azure SQL 資料倉儲](https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/)
- [使用 Azure SQL 資料倉儲的常見 ISV 應用程式模式](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/)

## <a name="key-selection-criteria"></a>關鍵選取準則

若要縮小選項範圍，請開始回答這些問題：

- 您要受控服務，而不是自行管理自己的伺服器？

- 您正在使用極大型資料集，或相當複雜且長時間執行的查詢嗎？ 如果是，請考慮 MPP 選項。 

- 對於大型資料集，資料來源是結構化或非結構化？ 非結構化資料可能需要在巨量資料環境中處理，例如 HDInsight 上的 Spark、Azure Databricks、HDInsight 上的 Hive LLAP，或 Azure Data Lake Analytics。 可將其全部作為 ELT (擷取、載入、轉換) 和 ETL (擷取、轉換、載入) 引擎。 它們可以將處理的資料輸出為結構化資料，以便更容易載入到 SQL 資料倉儲或其他選項的其中一個。 對於結構化資料，SQL 資料倉儲具有稱為「針對計算最佳化」的效能層級，適用於需要超高效能的大量計算工作負載。

- 是否要區隔您的歷史資料與您目前的操作資料？ 如果是的話，請選取需要[協調流程](pipeline-orchestration-data-movement.md)的其中一個選項。 這些是為大量讀取存取所最佳化的獨立倉儲，而且最適合作為個別的歷程記錄資料存放區。

- 除了您的 OLTP 資料存放區，您是否需要整合多個來源的資料？ 如果是的話，請考慮可輕鬆整合多個資料來源的選項。 

- 您是否有多租用戶的需求？ 如果是，這項需求不適用 SQL 資料倉儲。 如需詳細資訊，請參閱 [SQL 資料倉儲模式和反向模式](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/)。

- 您是否慣用關聯式資料存放區？ 如果是這樣，將選項縮小為包含關聯式資料存放區的選項，但也請注意，您可以視需要，使用 PolyBase 此類工具來查詢非關聯式資料存放區。 不過，如果決定要使用 PolyBase，請針對工作負載的非結構化資料集來執行效能測試。

- 您是否有即時的報告需求？ 如果需要在大量單一插入中具有快速的查詢回應時間，請將選項縮小為可支援即時報告的選項。

- 您需要支援大量的並行使用者及連線嗎？ 支援眾多並行使用者/連線的能力則取決於許多因素。 

    - 對於 Azure SQL Database，請根據服務層級參閱[記錄的資源限制](/azure/sql-database/sql-database-resource-limits)。 
    
    - SQL Server 最多允許 32,767 筆使用者連線。 在 VM 上執行時，效能將取決於 VM 大小和其他因素。 
    
    - SQL 資料倉儲具有並行查詢和並行連線限制。 如需詳細資訊，請參閱 [SQL 資料倉儲中的並行存取和工作負載管理](/azure/sql-data-warehouse/sql-data-warehouse-develop-concurrency)。 請考慮使用互補服務，例如 [Azure Analysis Services](/azure/analysis-services/analysis-services-overview)，以克服 SQL 資料倉儲中的限制。

- 您有哪些種類的工作負載？ 一般情況下，MPP 型倉儲解決方案最適合用於分析、批次導向的工作負載。 如果您的工作負載具備交易式本質，包含許多小型的讀取/寫入作業或多個按個別資料列執行的作業，請考慮使用其中一個 SMP 選項。 這個指導方針的例外狀況有：在 HDInsight 叢集上使用串流處理 (例如 Spark 串流)，及在 Hive 資料表中儲存資料。

## <a name="capability-matrix"></a>相容性矩陣

下表是主要功能差異的摘要。

### <a name="general-capabilities"></a>一般功能

| | 連接字串 | SQL Server (VM) | SQL 資料倉儲 | HDInsight 上的 Apache Hive | HDInsight 上的 Hive LLAP |
| --- | --- | --- | --- | --- | --- | -- |
| 為受控服務 | yes | 否 | yes | 是 <sup>1</sup> | 是 <sup>1</sup> |
| 需要資料協調流程 (保存資料/歷程記錄資料的複本) | 否 | 否 | yes | yes | yes |
| 輕鬆整合多個資料來源 | 否 | 否 | yes | yes | yes |
| 支援計算暫停 | 否 | 否 | yes | 否 <sup>2</sup> | 否 <sup>2</sup> |
| 關聯式資料存放區 | yes | yes |  yes | 否 | 否 |
| 即時報告 | yes | yes | 否 | 否 | yes |
| 彈性備份還原點 | yes | yes | 否 <sup>3</sup> | 是 <sup>4</sup> | 是 <sup>4</sup> |
| SMP/MPP | SMP | SMP | MPP | MPP | MPP |

[1] 手動設定和調整。

[2] HDInsight 叢集可在不需要時刪除然後重新建立。 將外部資料存放區連結至您的叢集，因此在刪除叢集後可保留資料。 您可以使用 Azure Data Factory 來自動化叢集的生命週期，方法是建立隨選 HDInsight 叢集來處理您的工作負載，然後在完成處理後刪除它。

[3] 利用 SQL 資料倉儲，您可以將資料庫還原到過去七天內的任何可用還原點。 快照集每四到八個小時會啟動，並且可供使用七天。 當快照集存在時間超過七天，它就會過期，而且無法再使用其還原點。

[4] 考慮使用可視需要備份和還原的[外部 Hive 中繼存放區](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters#use-hiveoozie-metastore)。 套用至 Blob 儲存體或 Data Lake Store 的標準備份和還原選項可用於資料，或第三方 HDInsight 備份與還原解決方案，例如 [Imanis 資料](https://azure.microsoft.com/blog/imanis-data-cloud-migration-backup-for-your-big-data-applications-on-azure-hdinsight/) 可更靈活和輕易地使用。

### <a name="scalability-capabilities"></a>延展性功能

| | 連接字串 | SQL Server (VM) |  SQL 資料倉儲 | HDInsight 上的 Apache Hive | HDInsight 上的 Hive LLAP |
| --- | --- | --- | --- | --- | --- | -- |
| 高可用性的備援區域伺服器  | yes | yes | yes | 否 | 否 |
| 支援查詢相應放大 (分散式查詢)  | 否 | 否 | yes | yes | yes |
| 動態延展性 (相應增加)  | yes | 否 | 是 <sup>1</sup> | 否 | 否 |
| 支援資料的記憶體內快取 | yes |  yes | 否 | yes | yes |

[1] SQL 資料倉儲可讓您調整資料倉儲單位 (DWU) 數目以相應增加或減少。 請參閱[管理 Azure SQL 資料倉儲中的計算能力](/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview)。

### <a name="security-capabilities"></a>安全性功能

| | 連接字串 | 虛擬機器中的 SQL Server | SQL 資料倉儲 | HDInsight 上的 Apache Hive | HDInsight 上的 Hive LLAP |
| --- | --- | --- | --- | --- | --- | -- |
| 驗證  | SQL / Azure Active Directory (Azure AD) | SQL / Azure AD / Active Directory | SQL / Azure AD | 本機 / Azure AD <sup>1</sup> | 本機 / Azure AD <sup>1</sup> |
| Authorization  | yes | yes | yes | yes | 是 <sup>1</sup> | 是 <sup>1</sup> |
| 稽核  | yes | yes | yes | yes | 是 <sup>1</sup> | 是 <sup>1</sup> |
| 待用資料加密 | 是 <sup>2</sup> | 是 <sup>2</sup> | 是 <sup>2</sup> | 是 <sup>2</sup> | 是 <sup>1</sup> | 是 <sup>1</sup> |
| 資料列層級安全性 | yes | yes | yes | 否 | 是 <sup>1</sup> | 是 <sup>1</sup> |
| 支援防火牆 | yes | yes | yes | yes | 是 <sup>3</sup> | 是 <sup>3</sup> |
| 動態資料遮罩 | yes | yes | yes | 否 | 是 <sup>1</sup> | 是 <sup>1</sup> |

[1] 使用[已加入網域的 HDInsight 叢集](/azure/hdinsight/domain-joined/apache-domain-joined-introduction)時所需。

[2] 使用透明資料加密 (TDE) 來加密和解密待用資料時所需。

[3] [在 Azure 虛擬網路中使用](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)時支援。

深入了解資料倉儲保護：

* [保護您的 SQL Database](/azure/sql-database/sql-database-security-overview#connection-security)
* [保護 SQL 資料倉儲中的資料庫](/azure/sql-data-warehouse/sql-data-warehouse-overview-manage-security)
* [使用 Azure 虛擬網路延伸 Azure HDInsight](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)
* [已加入網域之 HDInsight 叢集的企業等級 Hadoop 安全性](/azure/hdinsight/domain-joined/apache-domain-joined-introduction)


---
title: 選擇分析資料存放區
description: ''
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: cdc32c16e30aec5e1c0cb6959182215f99d56b56
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
ms.locfileid: "30846877"
---
# <a name="choosing-an-analytical-data-store-in-azure"></a>在 Azure 中選擇分析資料存放區

在[巨量資料](../big-data/index.md)架構中，通常需要提供分析資料存放區給已經過處理並使用結構化格式的資料，以便使用分析工具進行查詢。 支援查詢熱路徑 (hot-path) 及冷路徑 (cold-path) 資料的分析資料存放區統稱為服務層或資料服務儲存體。

服務層會處理熱路徑和冷路徑中已處理過的資料。 在 [lambda 架構](../big-data/index.md#lambda-architecture)中，服務層會細分成_速度處理_層 (其中儲存尚未以累加方式處理的資料) 和_批次服務_層 (其中包含批次處理的輸出)。 服務層需要強力支援低延遲的隨機讀取。 速度層的資料儲存體也應該支援隨機寫入，因為將資料批次載入此存放區會導致不想要的延遲。 相反地，批次層的資料存放體不需要支援隨機寫入，而是批次寫入。

沒有適用於所有資料儲存體工作的單一最佳資料管理選項。 已針對不同的工作最佳化不同的資料管理解決方案。 大部分實際運作的雲端應用程式和巨量資料處理程序有各種不同的資料儲存需求，而且通常可以組合使用資料儲存解決方案。

## <a name="what-are-your-options-when-choosing-an-analytical-data-store"></a>選擇分析資料存放區時有哪些選項？

在 Azure 中有多個資料服務儲存體選項，您可以根據您的需求做選擇：

- [SQL 資料倉儲](/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)
- [Azure SQL Database](/azure/sql-database/)
- [Azure VM 中 SQL Server](/sql/sql-server/sql-server-technical-documentation)
- [HDInsight 上的 HBase/Phoenix](/azure/hdinsight/hbase/apache-hbase-overview)
- [HDInsight 上的 Hive LLAP](/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
- [Azure Analysis Services](/azure/analysis-services/analysis-services-overview)
- [Azure Cosmos DB](/azure/cosmos-db/)

這些選項可讓您針對不同類型的工作，提供最佳化的各種資料庫模型：

- [索引鍵/值](https://msdn.microsoft.com/library/dn313285.aspx#sec7)資料庫針對每個索引鍵值保存單一序列化物件。 適合用來儲存大量資料，也就是您要在其中取得指定索引鍵值的一個項目，而且不必根據項目的其他屬性進行查詢。
- [文件](https://msdn.microsoft.com/library/dn313285.aspx#sec8)資料庫是索引鍵/值資料庫，其中的值是 documents。 在此內容中，"document" 是具名欄位和值的集合。 資料庫通常會以 XML、YAML、JSON 或 BSON 格式儲存資料，但可能使用純文字。 文件資料庫可查詢非索引鍵欄位，並定義可讓查詢更有效率的次要索引。 如果應用程式需要根據比文件索引鍵值更複雜的準則來擷取資料，這能讓文件資料庫更適用於此類應用程式。 例如，您可以查詢產品識別碼、客戶識別碼或客戶名稱等欄位。
- [資料行系列](https://msdn.microsoft.com/library/dn313285.aspx#sec9)資料庫是索引鍵/值資料存放區，其將資料儲存體結構化成相關的資料行集合，稱為資料行系列。 比方說，人口普查資料庫可能有一組資料行是某人的姓名 (名字、中間名、姓氏)、一個群組是此人的地址，和一個群組是此人的個人資料資訊 (生日、性別)。 資料庫可在不同的分區中儲存每個資料行系列，同時保留與相同索引鍵相關之某個人的所有資料。 應用程式可以讀取單一資料行系列，而無須讀取一個實體的所有資料。
- [圖表](https://msdn.microsoft.com/library/dn313285.aspx#sec10)資料庫將資訊儲存為物件和關聯性的集合。 圖表資料庫可有效執行查詢，以周遊物件的網路及其間的關聯性。 比方說，物件可能是人力資源資料庫中的員工，而您可以進行快速查詢，例如「尋找直接或間接為 Scott 工作的所有員工。」

## <a name="key-selection-criteria"></a>關鍵選取準則

若要縮小選項範圍，請開始回答這些問題：

- 是否需要提供儲存體來作為資料的熱路徑？ 如果是，請將選項縮小到針對速度服務層最佳化的選項。

- 是否需要巨量平行處理 (MPP) 支援，其中查詢會自動分散於數個處理程序或節點之間？ 如果是，請選取支援查詢相應放大的選項。

- 是否要使用關聯式資料存放區？ 如果是，請將選項縮小為包含關聯式資料庫模型的選項。 不過，請注意，某些非關聯式存放區支援 SQL 語法的查詢，並可使用 PolyBase 等工具來查詢非關聯式資料存放區。

## <a name="capability-matrix"></a>相容性矩陣

下表摘要列出各項功能的主要差異。

### <a name="general-capabilities"></a>一般功能

| | SQL Database | SQL 資料倉儲 | HDInsight 上的 HBase/Phoenix | HDInsight 上的 Hive LLAP | Azure Analysis Services | Cosmos DB |
| --- | --- | --- | --- | --- | --- | --- |
| 屬於受控服務 | yes | yes | 是 <sup>1</sup> | 是 <sup>1</sup> | yes | yes |
| 主要資料庫模型 | 關聯式 (使用資料行存放區索引時的單欄式格式) | 具有單欄式儲存體的關聯式資料表 | 寬資料行存放區 | Hive/記憶體內 | 表格式/MOLAP 語意模型 | 文件存放區、圖表、索引鍵-值存放區、寬資料行存放區 |
| SQL 語言支援 | yes | yes | 是 (使用 [Phoenix](http://phoenix.apache.org/) JDBC 驅動程式) | yes | 否 | yes |
| 已針對速度服務層最佳化 | 是 <sup>2</sup> | 否 | yes | yes | 否 | yes |

[1] 使用手動設定和調整。

[2] 使用經記憶體最佳化的資料表和雜湊或非叢集索引。
 
### <a name="scalability-capabilities"></a>延展性功能

|                                                  | SQL Database | SQL 資料倉儲 | HDInsight 上的 HBase/Phoenix | HDInsight 上的 Hive LLAP | Azure Analysis Services | Cosmos DB |
|--------------------------------------------------|--------------|--------------------|----------------------------|------------------------|-------------------------|-----------|
| 高可用性的備援區域伺服器 |     yes      |        yes         |            yes             |           否           |           否            |    yes    |
|             支援查詢相應放大             |      否      |        yes         |            yes             |          yes           |           yes           |    yes    |
|          動態延展性 (相應增加)          |     yes      |        yes         |             否             |           否           |           yes           |    yes    |
|        支援資料的記憶體內快取        |     yes      |        yes         |             否             |          yes           |           yes           |    否     |

### <a name="security-capabilities"></a>安全性功能

| | SQL Database | SQL 資料倉儲 | HDInsight 上的 HBase/Phoenix | HDInsight 上的 Hive LLAP | Azure Analysis Services | Cosmos DB |
| --- | --- | --- | --- | --- | --- | --- |
| 驗證  | SQL / Azure Active Directory (Azure AD) | SQL / Azure AD | 本機 / Azure AD <sup>1</sup> | 本機 / Azure AD <sup>1</sup> | Azure AD | 資料庫使用者 / 透過存取控制存取的 Azure AD (IAM) |
| 待用資料加密 | 是 <sup>2</sup> | 是 <sup>2</sup> | 是 <sup>1</sup> | 是 <sup>1</sup> | yes | yes |
| 資料列層級安全性 | yes | 否 | 是 <sup>1</sup> | 是 <sup>1</sup> | 是 (透過模型中的物件等級安全性) | 否 |
| 支援防火牆 | yes | yes | 是 <sup>3</sup> | 是 <sup>3</sup> | yes | yes |
| 動態資料遮罩 | yes | 否 | 是 <sup>1</sup> | 是 * | 否 | 否 |

[1] 使用[已加入網域的 HDInsight 叢集](/azure/hdinsight/domain-joined/apache-domain-joined-introduction)時所需。

[2] 使用透明資料加密 (TDE) 來加密和解密待用資料時所需。

[3] 在 Azure 虛擬網路中使用時。 請參閱[使用 Azure 虛擬網路延伸 Azure HDInsight](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)。

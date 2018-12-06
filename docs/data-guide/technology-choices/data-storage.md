---
title: 選擇資料儲存技術
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.openlocfilehash: c97249228ca45a7a17822b6dd55acad6360c6f6b
ms.sourcegitcommit: e7e0e0282fa93f0063da3b57128ade395a9c1ef9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52902641"
---
# <a name="choosing-a-big-data-storage-technology-in-azure"></a>在 Azure 中選擇巨量資料儲存技術

本主題將比較巨量資料解決方案的資料存放區選項 &mdash; 具體來說，就是比較大量資料擷取和批次處理的資料儲存體，與[分析資料存放區](./analytical-data-stores.md)或[即時串流擷取](./real-time-ingestion.md)的儲存體。

## <a name="what-are-your-options-when-choosing-data-storage-in-azure"></a>選擇 Azure 中的資料儲存體時有哪些選項？

根據您的需求，您可以透過數個選項將資料擷取到 Azure 中：

**檔案儲存體**

- [Azure 儲存體 Blob](/azure/storage/blobs/storage-blobs-introduction)
- [Azure Data Lake Store](/azure/data-lake-store/)

**NoSQL 資料庫**

- [Azure Cosmos DB](/azure/cosmos-db/)
- [HDInsight 上的 HBase](https://hbase.apache.org/)

## <a name="azure-storage-blobs"></a>Azure 儲存體 Blob

Azure 儲存體是一項受控儲存體服務，具有高可用性、安全、耐用、可擴充和備援性等優點。 Microsoft 會為您進行維護和處理重大問題。 在 Azure 所提供的儲存體解決方案中，Azure 儲存體是最普及的一個，因為適用此儲存體的服務和工具數量眾多。

您可以使用多項 Azure 儲存體服務來儲存資料。 要儲存來自多個資料來源的 Blob，最具彈性的選項是 [Blob 儲存體](/azure/storage/blobs/storage-blobs-introduction)。 Blob 基本上是檔案。 它們可以儲存圖片、文件、HTML 檔案、虛擬硬碟 (VHD)、巨量資料 (例如記錄、資料庫備份) &mdash; 幾乎無所不包。 Blob 會儲存在容器 (類似於資料夾) 中。 容器可對一組 Blob 進行分組。 儲存體帳戶可以包含無限數量的容器，而一個容器則可儲存無限數量的 Blob。

Azure 儲存體是巨量資料與分析解決方案的理想選擇，因為它具有高彈性、高可用性和低成本等優點。 它針對不同的使用案例提供了經常性存取、非經常性存取和封存等儲存層。 如需詳細資訊，請參閱 [Azure Blob 儲存體︰經常性存取、非經常性存取和封存儲存層](/azure/storage/blobs/storage-blob-storage-tiers)。

您可以從 Hadoop (可透過 HDInsight 使用) 存取 Azure Blob 儲存體。 HDInsight 可以使用 Azure 儲存體中的 Blob 容器做為叢集的預設檔案系統。 透過 WASB 驅動程式所提供的 Hadoop 分散式檔案系統 (HDFS) 介面，HDInsight 中的完整元件集可直接處理儲存為 Blob 的結構化或非結構化資料。 Azure Blob 儲存體也可透過 Azure SQL 資料倉儲的 PolyBase 功能來存取。

其他促使 Azure 儲存體成為理想選項的功能包括：

- [多重並行策略](/azure/storage/common/storage-concurrency?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。
- [災害復原和高可用性選項](/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。
- [待用加密](/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。
- [角色型存取控制 (RBAC)](/azure/storage/common/storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#management-plane-security)，可使用 Azure Active Directory 使用者和群組來控制存取。

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

[Azure Data Lake Store](/azure/data-lake-store/) 是容納巨量資料分析工作負載的企業級超大規模存放庫。 Data Lake 可讓您在單一[安全](/azure/data-lake-store/data-lake-store-overview#DataLakeStoreSecurity)位置擷取任何大小、類型和擷取速度的資料，以便進行運作和探究分析。

對於帳戶大小、檔案大小，或 Data Lake 中可儲存的資料量，Data Lake Store 均無任何限制。 藉由製作多個複本來長期儲存資料，而資料可以儲存在 Data Lake 中的持續時間並沒有限制。 除了建立檔案的多個複本以防範非預期的失敗以外，Data Lake 也會將檔案的某些部分分散到多個個別的儲存體伺服器。 這可改善以平行方式讀取檔案以便執行資料分析時的輸送量。

使用與 WebHDFS 相容的 REST API，可以從 Hadoop (可透過 HDInsight 使用) 存取 Data Lake Store。 當個別或合併的檔案大小超出 Azure 儲存體所支援的限制時，您可以考慮以此作為 Azure 儲存體替代選項。 不過，在使用 Data Lake Store 作為 HDInsight 叢集的主要儲存體時，您必須遵循若干[效能調整指引](/azure/data-lake-store/data-lake-store-performance-tuning-guidance#optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight)，其中包括適用於 [Spark](/azure/data-lake-store/data-lake-store-performance-tuning-spark)、[Hive](/azure/data-lake-store/data-lake-store-performance-tuning-hive)、[MapReduce](/azure/data-lake-store/data-lake-store-performance-tuning-mapreduce) 和 [Storm](/azure/data-lake-store/data-lake-store-performance-tuning-storm) 的特定指引。 此外，請務必檢查 Data Lake Store 的[區域可用性](https://azure.microsoft.com/regions/#services)，因為它不像 Azure 儲存體可在那麼多區域使用，而且必須位於與您的 HDInsight 叢集相同的區域中。

Data Lake Store 可與 Azure Data Lake Analytics 搭配使用，它是專為預存資料分析而設計的，並且針對資料分析案例的效能進行了調整。 Data Lake Store 也可透過 Azure SQL Data Warehouse 的 PolyBase 功能來存取。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

[Azure Cosmos DB](/azure/cosmos-db/) 是 Microsoft 全球發行的多模型資料庫。 Cosmos DB 保證世界各地第 99 個百分位數的個位數毫秒延遲時間，提供多個定義完善的一致性模型以微調效能，並保證透過多路連接功能提供高可用性。

Azure Cosmos DB 是無從驗證結構描述。 它會自動編製所有資料的索引，您不需要處理結構描述和索引管理。 它也是多模型、原生支援文件、索引鍵值、圖表和資料行系列資料模型。 

Azure Cosmos DB 功能：

- [異地複寫](/azure/cosmos-db/distribute-data-globally)
- 在全球[彈性調整輸送量與儲存體](/azure/cosmos-db/partition-data)
- [五個定義完善的一致性層級](/azure/cosmos-db/consistency-levels)

## <a name="hbase-on-hdinsight"></a>HDInsight 上的 HBase

[Apache HBase](https://hbase.apache.org/) 是開放原始碼的 NoSQL 資料庫，以 Hadoop 作為建置基礎，並仿照 Google BigTable 建立模型。 HBase 可針對依資料行系列組織的無結構描述資料庫中的大量非結構化及半結構化資料，提供隨機存取功能和強大一致性。

資料儲存在資料表的資料列中，而資料列中的資料會依據資料行系列進行分組。 HBase 沒有結構描述，也就是說，在使用資料行之前，並不需要先定義資料行和其中儲存之資料的類型。 開放原始碼的程式碼會以線性方式延展，以處理數千個節點上的 PB 資料。 它可依賴散佈在 Hadoop 生態系統中的應用程式所提供的資料備援、批次處理及其他功能。

[HDInsight 實作](/azure/hdinsight/hbase/apache-hbase-overview)運用 HBase 的相應放大架構，提供資料表自動分區功能、讀取和寫入的強大一致性，以及自動容錯移轉功能。 透過在記憶體內部快取讀取和高輸送量的串流寫入，來提高效能。 在大部分情況下，您都會想要[在虛擬網路內建立 HBase 叢集](/azure/hdinsight/hbase/apache-hbase-provision-vnet)，讓其他 HDInsight 叢集和應用程式可直接存取資料表。

## <a name="key-selection-criteria"></a>關鍵選取準則

為了縮小選擇範圍，請先回答下列問題：

- 您是否有任何類型的文字或二進位資料需要使用受控、高速的雲端架構儲存體？ 如果是，請選取其中一個檔案儲存體選項。

- 您是否需要針對平行分析工作負載和高輸送量/IOPS 進行最佳化的檔案儲存體？ 如果是，請選擇已針對分析工作負載效能進行調整的選項。

- 您是否需要將非結構化或半結構化資料儲存在無結構描述資料庫中？ 如果是，請選取其中一個非關聯式的選項。 請比較索引和資料庫模型的選項。 根據您需要儲存的資料類型，主要資料庫模型可能是最重大的因素。

- 您可以在您的區域使用此服務嗎？ 請查看各項 Azure 服務的適用區域。 請參閱[依區域提供的產品](https://azure.microsoft.com/regions/services/)。

## <a name="capability-matrix"></a>功能對照表

下表摘錄主要的功能差異。

### <a name="file-storage-capabilities"></a>檔案儲存體功能

|  | Azure Data Lake Store | Azure Blob 儲存體容器 |
| --- | --- | --- |
| 目的 | 適用於巨量資料分析工作負載的最佳化儲存機制 |適用於各種不同儲存體案例的一般用途物件存放區 |
| 使用案例 | 批次、串流分析和機器學習資料，例如記錄檔、IoT 資料、點擊串流、大型資料集 | 任何類型的文字或二進位資料，例如應用程式後端、備份資料、串流和一般用途資料的媒體儲存體 |
| Structure | 階層式檔案系統 | 具有扁平命名空間的物件存放區 |
| 驗證 | 採用 [Azure Active Directory 身分識別](/azure/active-directory/active-directory-authentication-scenarios) | 採用共用密碼：[帳戶存取金鑰](/azure/storage/common/storage-create-storage-account#manage-your-storage-account)、[共用存取簽章金鑰](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)和[角色型存取控制 (RBAC)](/azure/security/security-storage-overview) |
| 驗證通訊協定 | OAuth 2.0。 呼叫必須包含由 Azure Active Directory 發行的有效 JWT (JSON Web 權杖) | 雜湊式訊息驗證碼 (HMAC)。 呼叫必須包含透過 HTTP 要求之一部分的 Base64 編碼 SHA-256 雜湊。 |
| Authorization | POSIX 存取控制清單 (ACL)。 ACL 採用 Azure Active Directory 身分識別，可設為檔案或資料夾層級。 | 針對帳戶層級授權，使用[帳戶存取金鑰](/azure/storage/common/storage-create-storage-account#manage-your-storage-account)。 針對帳戶、容器或 Blob 授權，使用[共用存取簽章金鑰](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)。 |
| 稽核 | 可用。  |可用 |
| 待用加密 | 透明、伺服器端 | 透明、伺服器端、用戶端加密 |
| 開發人員 SDK | .NET、Java、Python、Node.js | .Net、Java、Python、Node.js、C++、Ruby |
| 分析工作負載效能 | 平行分析工作負載、高輸送量和 IOPS 的最佳化效能 | 未針對分析工作負載最佳化 |
| 大小限制 | 帳戶大小、檔案大小或檔案數目沒有限制 | 特定限制記載於 [這裡](/azure/azure-subscription-service-limits#storage-limits) |
| 異地備援 | 本機備援 (在一個 Azure 區域中多個資料複本） | 本機備援 (LRS)、全域備援 (GRS)、讀取存取全域備援 (RA-GRS)。 詳細資訊請參閱 [這裡](/azure/storage/common/storage-redundancy) |

### <a name="nosql-database-capabilities"></a>NoSQL 資料庫功能

|                                    |                                           Azure Cosmos DB                                           |                                                             HDInsight 上的 HBase                                                             |
|------------------------------------|-----------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|       主要資料庫模型       |                      文件存放區、圖表、索引鍵-值存放區、寬資料行存放區                      |                                                             寬資料行存放區                                                              |
|         次要索引          |                                                 是                                                 |                                                                     否                                                                     |
|        SQL 語言支援        |                                                 是                                                 |                                     是 (使用 [Phoenix](https://phoenix.apache.org/) JDBC 驅動程式)                                      |
|            一致性             |                   強式、限定過期、工作階段、一致前置詞、最終                   |                                                                   強式                                                                   |
| 原生 Azure Functions 整合 |                        [是](/azure/cosmos-db/serverless-computing-database)                        |                                                                     否                                                                     |
|   自動全球發行    |                          [是](/azure/cosmos-db/distribute-data-globally)                           | 否 [HBase 叢集複寫可使用最終一致性跨區域設定](/azure/hdinsight/hbase/apache-hbase-replication) |
|           定價模式            | 隨需以秒計費、可彈性擴充的要求單位 (RU)，可彈性擴充的儲存體 |                              以分鐘計價的 HDInsight 叢集 (節點的水平調整)，儲存體                               |


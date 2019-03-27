---
title: 擷取、轉換和載入 (ETL)
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: 1551736d8ef3d2b82eb0a2fdb626330798ec1c65
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58246189"
---
# <a name="extract-transform-and-load-etl"></a>擷取、轉換和載入 (ETL)

組織常面臨的問題之一，是如何收集來自多個資料來源、採用多種格式的資料，並將其移至一或多個資料存放區。 目的地可能不是與來源相同類型的資料存放區，且格式也經常不同，或需要先經過圖形化或清理才能載入其最終目的地中。

過去幾年已開發出多種有助於克服這些難題的工具、服務和程序。 無論使用何種程序，都需要協調工作，並在資料管線中執行某種程度的資料轉換。 下列幾節將特別說明用來執行這些工作的常見方法。

## <a name="extract-transform-and-load-etl-process"></a>擷取、轉換和載入 (ETL) 流程

擷取、轉換和載入 (ETL) 是一種資料管線，用以收集不同來源的資料、根據商務規則轉換資料，然後將其載入至目的地資料存放區。 ETL 的轉換工作會以特殊引擎執行，且通常牽涉到使用暫存資料表在資料轉換時暫時保存資料，而最終載入至其目的地。

資料轉換在執行時通常牽涉到各種作業，例如篩選、排序、彙總、聯結資料、清除資料、刪除重複資料，以及驗證資料。

![擷取-轉換-載入 (ETL) 程序](../images/etl.png)

這三個 ETL 階段通常會以平行方式執行，以節省時間。 例如，在擷取資料時，轉換程序即會處理使用已接收的資料，並且準備進行載入，而載入程序也無須等到整個擷取程序完成後才開始處理已備妥的資料。

相關 Azure 服務：

- [Azure Data Factory v2](https://azure.microsoft.com/services/data-factory/)

其他工具：

- [SQL Server Integration Services (SSIS)](/sql/integration-services/sql-server-integration-services)

## <a name="extract-load-and-transform-elt"></a>擷取、載入和轉換 (ELT)

擷取、載入和轉換 (ELT) 與 ETL 的不同之處，僅在於轉換的執行位置。 在 ELT 管線中，轉換會在目標資料存放區中執行。 此時並不會使用個別的轉換引擎，而是使用目標資料存放區的處理功能來轉換資料。 如此即不需要在管線中使用轉換引擎，因而能簡化架構。 這個方法的另一個好處是，調整目標資料存放區時，也會調整 ELT 管線效能。 不過，只有在目標系統的功能足以有效率地轉換資料時，ELT 才能妥善運作。

![擷取-載入-轉換 (ELT) 程序](../images/elt.png)

ELT 通常會用於巨量資料領域中。 例如，一開始您可能會將所有來源資料擷取到可擴充儲存體中的一般檔案，例如 Hadoop 分散式檔案系統 (HDFS) 或 Azure Data Lake Store。 接著，您可以使用 Spark、Hive 或 PolyBase 等技術來查詢來源資料。 使用 ELT 的重點在於，用來執行轉換的資料存放區與最終使用資料的是同一個資料存放區。 此資料存放區會直接讀取可擴充儲存體，而不是將資料載入到本身專屬的儲存體。 這種方法可略過存在於 ETL 中的資料複製步驟，此作業在處理大型資料集時可能十分耗時。

在實務上，目標資料存放區會是使用 Hadoop 叢集 (使用 Hive 或 Spark) 或 SQL 資料倉儲的[資料倉儲](./data-warehousing.md)。 一般而言，結構描述在查詢時期會覆疊在一般檔案資料上，並儲存為資料表，讓資料能夠像資料存放區中的任何其他資料表一樣受到查詢。 這些資料表稱為外部資料表，因為資料並不在資料存放區本身所管理的儲存體中，而是在某個外部可擴充儲存體上。

資料存放區只會管理資料的結構描述，並在讀取時套用結構描述。 例如，使用 Hive 的 Hadoop 叢集會描述資料來源可作為 HDFS 中一組檔案之路徑的 Hive 資料表。 在 SQL 資料倉儲中，PolyBase 可達到相同的結果 &mdash; 針對在資料庫本身以外儲存的資料，建立一個資料表。 在來源資料載入後，可以使用資料存放區的功能來處理外部資料表中的資料。 在巨量資料案例中，這意味著資料存放區必須具有大量平行處理 (MPP) 的能力，而將資料分成較小的區塊，並將區塊的處理以平行方式分散到多部電腦。

ELT 管線的最後階段，通常是將來源資料轉換為能夠使需要支援的查詢類型更有效率的最終格式。 例如，此時可能會分割資料。 此外，ELT 可能會使用 Parquet 之類的最佳化儲存格式，以單欄方式儲存資料列導向的資料，並提供最佳化索引。

相關 Azure 服務：

- [Azure SQL 資料倉儲](/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)
- [具有 Hive 的 HDInsight](/azure/hdinsight/hadoop/hdinsight-use-hive)
- [Azure Data Factory v2](https://azure.microsoft.com/services/data-factory/)
- [HDInsight 上的 Oozie](/azure/hdinsight/hdinsight-use-oozie-linux-mac)

其他工具：

- [SQL Server Integration Services (SSIS)](/sql/integration-services/sql-server-integration-services)

## <a name="data-flow-and-control-flow"></a>資料流程和控制流程

在資料管線的內容中，控制流程可確保一組工作能夠以正確的順序進行處理。 為了對這些工作強制執行正確的處理順序，會使用優先順序條件約束。 您可以將這些條件約束視為工作流程圖中的連接線，如下圖所示。 每個工作都會有結果，例如成功、失敗或完成。 任何後續的工作都必須等到上一個工作完成並產生前述其中一個結果後，才會開始處理。

控制流程會以工作的形式執行資料流程。 在資料流程工作中，會從來源擷取資料、加以轉換，或載入資料存放區中。 一個資料流程工作的輸出可以是下一個資料流程工作的輸入，且資料流程可以平行執行。 不同於控制流程，您無法在資料流程中的工作之間新增條件約束。 不過，您可以新增資料檢視器，以觀察每項工作正在處理的資料。

![控制流程中以工作的形式執行的資料流程](../images/control-flow-data-flow.png)

在上圖中，控制流程內有數項工作，其中之一是資料流程工作。 其中一項工作內嵌於容器中。 容器可以用來提供工作的結構，進而提供工作單位。 舉例來說，集合中的重複元素即是如此，例如資料夾中的檔案或資料庫陳述式。

相關 Azure 服務：

- [Azure Data Factory v2](https://azure.microsoft.com/services/data-factory/)

其他工具：

- [SQL Server Integration Services (SSIS)](/sql/integration-services/sql-server-integration-services)

## <a name="technology-choices"></a>技術選擇

- [線上交易處理 (OLTP) 資料存放區](./online-transaction-processing.md#oltp-in-azure)
- [線上分析處理 (OLTP) 資料存放區](./online-analytical-processing.md#olap-in-azure)
- [資料倉儲](./data-warehousing.md)
- [管線協調流程](../technology-choices/pipeline-orchestration-data-movement.md)

## <a name="next-steps"></a>後續步驟

下列參考架構顯示 Azure 上的端對端 ELT 管線：

- [Azure 中具 SQL 資料倉儲的 Enterprise BI](../../reference-architectures/data/enterprise-bi-sqldw.md)
- [具 SQL 資料倉儲和 Azure Data Factory 的自動化 Enterprise BI](../../reference-architectures/data/enterprise-bi-adf.md)
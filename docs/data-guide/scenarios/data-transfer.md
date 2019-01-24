---
title: 選擇資料傳輸技術
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: c58d06813e3a500c6bb1b6c7889e65f401be6c33
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54484528"
---
# <a name="transferring-data-to-and-from-azure"></a>在 Azure 來回傳輸資料

根據您的需求，有數個選項可以在 Azure 來回傳輸資料。

## <a name="physical-transfer"></a>實體傳輸

下列情況適合使用實體硬體將資料傳輸至 Azure：

- 您的網路速度很慢或不可靠。
- 取得額外網路頻寬的成本很高。
- 安全性或組織原則不允許在處理敏感性資料時建立輸出連線。

如果您主要的考量是資料傳輸時間長短，建議您執行測試以確認網路傳輸速度實際上是否比實體傳輸慢。

有兩大選項可將資料實體傳輸至 Azure：

- **Azure 匯入/匯出**。 [Azure 匯入/匯出服務](/azure/storage/common/storage-import-export-service)可讓您藉由將內部的 SATA HDD 或 SDD 寄送到 Azure 資料中心，安全地將大量資料傳輸至 Azure Blob 儲存體或 Azure 檔案服務。 您也可以使用這項服務，將資料從 Azure 儲存體傳輸至硬碟，然後將硬碟寄送到您手上以在內部部署環境中載入。

- **Azure 資料箱**。 [Azure 資料箱](https://azure.microsoft.com/services/storage/databox/)是 Microsoft 所提供的設備，其作用非常類似 Azure 匯入/匯出服務。 Microsoft 會將專屬且安全的防篡改傳輸設備寄送給您，並為您處理端到端物流程序，而您可以透過入口網站來追蹤此過程。 Azure 資料箱服務的其中一個優點是容易使用。 您不需要購買數個硬碟、讓硬碟做好準備，然後將檔案傳輸到每一個硬碟。 有許多領先業界的 Azure 合作夥伴支援 Azure 資料箱，因此您可以輕鬆且順暢地透過他們的產品離線傳輸至雲端。

## <a name="command-line-tools-and-apis"></a>命令列工具和 API

當您想要已編寫指令碼和完成程式設計的資料傳輸時，請考慮這些選項。

- **Azure CLI**。 [Azure CLI](/azure/hdinsight/hdinsight-upload-data#commandline) 是可讓您管理 Azure 服務，並將資料上傳至 Azure 儲存體的跨平台工具。

- **AzCopy**。 透過 [Windows](/azure/storage/common/storage-use-azcopy?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 或 [Linux](/azure/storage/common/storage-use-azcopy-linux?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 命令列使用 AzCopy，即可以最佳的效能輕鬆地將檔案複製到 Azure Blob、檔案和資料表儲存體，以及從中複製。 AzCopy 支援並行和平行處理原則，並且能夠繼續中斷的複製作業。 其速度也比其他大部分的選項快。 若要以程式設計方式存取，[Microsoft Azure 儲存體資料移動程式庫](/azure/storage/common/storage-use-data-movement-library)是支援 AzCopy 的核心架構。 它是以 .NET Core 文件庫的形式來提供。

- **PowerShell**。 [`Start-AzureStorageBlobCopy` PowerShell Cmdlet](/powershell/module/azure.storage/start-azurestorageblobcopy?view=azurermps-5.0.0) 是 Windows 系統管理員用於 PowerShell 的選項。

- **AdlCopy**。 [AdlCopy](/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) 可讓您將資料從 Azure 儲存體 Blob 複製到 Data Lake Store 中。 它也可用來在兩個 Azure Data Lake Store 帳戶之間複製資料。 不過，它不能用來將資料從 Data Lake Store 複製到儲存體 Blob。

- **Distcp**。 如果您有可存取 Data Lake Store 的 HDInsight 叢集，就可以使用 [Distcp](/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp) 之類的 Hadoop 生態系統工具，將送至/來自 HDInsight 叢集儲存體 (WASB) 的資料複製到 Data Lake Store 帳戶中。

- **Sqoop**。 [Sqoop](/azure/hdinsight/hadoop/hdinsight-use-sqoop) 是 Apache 專案，也是 Hadoop 生態系統的一部分。 它會預先安裝在所有 HDInsight 叢集上。 它可在 HDInsight 叢集與關聯式資料庫 (例如 SQL、Oracle、MySQL 等) 之間傳輸資料。 Sqoop 是相關工具的集合，包括匯入和匯出。 Sqoop 使用 Azure 儲存體 Blob 或 Data Lake Store 連結儲存體來處理 HDInsight 叢集。

- **PolyBase**。 [PolyBase](/sql/relational-databases/polybase/get-started-with-polybase) 是一種技術，它會透過 T-SQL 語言存取資料庫外部的資料。 在 SQL Server 2016 中，它能讓您對 Hadoop 的外部資料執行查詢，或讓您從 Azure Blob 儲存體匯入/匯出資料。 在 Azure SQL 資料倉儲中，您可以從 Azure Blob 儲存體和 Azure Data Lake Store 匯入/匯出資料。 到目前為止，PolyBase 是將資料匯入到 SQL 資料倉儲的最快方法。

- **Hadoop 命令列**。 當您有資料位於 HDInsight 叢集的前端節點時，您可以使用 `hadoop -copyFromLocal` 命令將該資料複製到叢集的連結儲存體，例如 Azure 儲存體 Blob 或 Azure Data Lake Store。 若要使用 Hadoop 命令，您必須先連線到前端節點。 連線之後，您就可以將檔案上傳到儲存體。

## <a name="graphical-interface"></a>圖形化介面

如果您只要傳輸幾個檔案或資料物件，而且不需要將程序自動化，請考慮下列選項。

- **Azure 儲存體總管**。 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)是跨平台工具，可讓您管理 Azure 儲存體帳戶的內容。 它能讓您上傳、下載及管理 Blob、檔案、佇列、資料表與 Azure Cosmos DB 實體。 將其用於 Blob 儲存體來管理 Blob 和資料夾，以及在您的本機檔案系統與 Blob 儲存體之間，或在儲存體帳戶彼此之間上傳和下載 Blob。

- **Azure 入口網站**。 Blob 儲存體和 Data Lake Store 皆提供 Web 型介面，可供您瀏覽檔案和上傳新檔案 (一次一個)。 如果您不要安裝任何工具或發出命令來快速瀏覽檔案，或只需上傳少數幾個新檔案，就很適合使用這個選項。

## <a name="data-pipeline"></a>Data Pipeline

**Azure Data Factory**。 [Azure Data Factory](/azure/data-factory/) 是受控服務，最適合在眾多 Azure 服務和 (或) 內部部署環境之間定期傳輸檔案。 您可以使用 Azure Data Factory 建立並排程資料驅動的工作流程 (稱為管線)，以從不同的資料存放區擷取資料。 使用計算服務 (例如，Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics 和 Azure Machine Learning) 可以處理或轉換資料。 建立資料驅動的工作流程，以便[協調](../technology-choices/pipeline-orchestration-data-movement.md)及自動進行資料移動和資料轉換。

## <a name="key-selection-criteria"></a>重要選取準則

在資料傳輸案例中，請回答下列問題來選擇適合您需求的系統：

- 您是否需要傳輸非常大量的資料，但透過網際網路連線來傳輸太過費時、不穩定或成本過高？ 如果是，請考慮實體傳輸。

- 您是否想要為資料傳輸工作編寫指令碼以便重複使用？ 如果是，請選取其中一個命令列選項或 Azure Data Factory。

- 您是否需要透過網路連線傳輸非常大量的資料？ 如果是，請選取最適用於巨量資料的選項。

- 您是否需要在關聯式資料庫中往/返傳輸資料？ 如果是，請選擇支援一或多個關聯式資料庫的選項。 請注意，其中的某些選項另外需要 Hadoop 叢集。

- 您是否需要自動化的資料管線或工作流程協調流程？ 如果是，請考慮 Azure Data Factory。

## <a name="capability-matrix"></a>功能對照表

下表摘要列出各項功能的主要差異。

<!-- markdownlint-disable MD024 -->

### <a name="physical-transfer"></a>實體傳輸

<!-- markdownlint-enable MD033 -->

| | Azure 匯入/匯出服務 | Azure 資料箱 |
| --- | --- | --- |
| 板型規格 | 內部 SATA HDD 或 SDD | 安全且防篡改的單一硬體設備 |
| Microsoft 會管理運送物流 | 否 | 是 |
| 與合作夥伴的產品整合 | 否 | 是 |
| 自訂設備 | 否 | 是 |

### <a name="command-line-tools"></a>命令列工具。

**Hadoop/HDInsight：**

| | Distcp | Sqoop | Hadoop CLI |
| --- | --- | --- | --- |
| 已針對巨量資料最佳化 | 是 | 是 |  是 |
| 複製到關聯式資料庫 |  否 | 是 | 否 |
| 從關聯式資料庫複製 |  否 | 是 | 否 |
| 複製到 Blob 儲存體 |  是 | 是 | 是 |
| 從 Blob 儲存體複製 | 是 |  是 | 否 |
| 複製到 Data Lake Store | 是 | 是 | 是 |
| 從 Data Lake Store 複製 | 是 | 是 | 否 |

**其他：**

<!-- markdownlint-disable MD033 -->

| | Azure CLI | AzCopy | PowerShell | AdlCopy | PolyBase |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 相容平台 | Linux、OS X、Windows | Linux、Windows | Windows | Linux、OS X、Windows | SQL Server、Azure SQL 資料倉儲 |
| 已針對巨量資料最佳化 | 否 | 否 | 否 | 是 <sup>1</sup> | 是 <sup>2</sup> |
| 複製到關聯式資料庫 | 否 | 否 | 否 | 否 | 是 |
| 從關聯式資料庫複製 | 否 | 否 | 否 | 否 | 是 |
| 複製到 Blob 儲存體 | 是 | 是 | 是 | 否 | 是 |
| 從 Blob 儲存體複製 | 是 | 是 | 是 | 是 | 是 |
| 複製到 Data Lake Store | 否 | 否 | yes | 是 |  是 |
| 從 Data Lake Store 複製 | 否 | 否 | yes | 是 | 是 |

<!-- markdownlint-enable MD033 -->

[1] AdlCopy 已針對使用 Data Lake Analytics 帳戶來傳輸巨量資料的作業予以最佳化。

[2] PolyBase 的[效能可提升](/sql/relational-databases/polybase/polybase-guide#performance)，方法是將計算推送到 Hadoop 並使用 [PolyBase 向外延展群組](/sql/relational-databases/polybase/polybase-scale-out-groups)，以便能夠在 SQL Server 執行個體與 Hadoop 節點之間平行傳輸資料。

### <a name="graphical-interface-and-azure-data-factory"></a>圖形化介面和 Azure Data Factory

| | Azure 儲存體總管 | Azure 入口網站 * | Azure Data Factory |
| --- | --- | --- | --- |
| 已針對巨量資料最佳化 | 否 | 否 | 是 |
| 複製到關聯式資料庫 | 否 | 否 | 是 |
| 從關聯式資料庫複製 | 否 | 否 | 是 |
| 複製到 Blob 儲存體 | 是 | 否 | 是 |
| 從 Blob 儲存體複製 | 是 | 否 | 是 |
| 複製到 Data Lake Store | 否 | 否 | 是 |
| 從 Data Lake Store 複製 | 否 | 否 | 是 |
| 上傳到 Blob 儲存體 | 是 | 是 | 是 |
| 上傳到 Data Lake Store | 是 | 是 | 是 |
| 協調資料傳輸 | 否 | 否 | 是 |
| 自訂資料轉換 | 否 | 否 | 是 |
| 定價模式 | 免費 | 免費 | 依使用量付費 |

\* Azure 入口網站在此案例中代表會針對 Blob 儲存體和 Data Lake Store 使用 Web 型瀏覽工具。

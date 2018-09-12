---
title: 互動式資料探索
description: ''
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 2e3d61fa5e1903c7fee6ebc84db3fa7c28d515cb
ms.sourcegitcommit: ae8a1de6f4af7a89a66a8339879843d945201f85
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2018
ms.locfileid: "43325112"
---
# <a name="interactive-data-exploration"></a>互動式資料探索

在許多企業商業智慧 (BI) 解決方案中，都會由 BI 專業人員建立報表和語意模型，並集中加以管理。 不過，有越來越多的組織開始想為使用者提供資料導向決策能力。 此外，也有越來越多的組織開始雇用*資料科學家*或*資料分析師*，其工作是以互動方式探索資料，並套用統計模型和分析技術，以在資料中找出趨勢與模式。 要執行互動式資料探索，必須要有相關工具和平台，提供以低延遲性處理特定查詢和資料視覺效果的能力。

![](./images/data-exploration.png)

## <a name="self-service-bi"></a>自助式 BI

自助式 BI 是新型商業決策方法被賦予的名稱；此方法的使用者能夠尋找、探索及共用對整個企業的資料產生的判讀。 為此，資料解決方案必須支援幾項需求：

* 可透過資料目錄探索商業資料來源。
* 可掌控資料管理，以確保資料實體定義和值的一致性。
* 有互動式資料模型和視覺效果工具供商業使用者使用。

在自助式 BI 解決方案中，商業使用者通常會尋找並取用與其特定業務領域相關的資料來源，並使用直覺式工具和生產力應用程式定義可與同事共用的個人資料模型和報表。

相關 Azure 服務：

- [Azure 資料目錄](/azure/data-catalog/data-catalog-what-is-data-catalog)
- [Microsoft Power BI](https://powerbi.microsoft.com/)

## <a name="data-science-experimentation"></a>資料科學測試
當組織需要執行進階分析和建立預測模型時，通常會由專業的資料科學家負責進行最初的準備工作。 資料科學家會探索資料並套用統計分析技術，以找出資料*特性*與所需的預測*標籤*之間的關聯性。 資料探索通常會使用原本即支援統計模型和視覺效果的 Python 或 R 之類的程式設計語言來完成。 用來探索資料的指令碼通常會裝載在特製化環境中，例如 Jupyter Notebook。 這些工具可讓資料科學家以程式設計方式探索資料，同時記錄及共用他們所發現的判讀結果。

相關 Azure 服務：

- [Azure Notebooks](https://notebooks.azure.com/)
- [Azure Machine Learning Studio](/azure/machine-learning/studio/what-is-ml-studio)
- [Azure Machine Learning 測試服務](/azure/machine-learning/preview/experimentation-service-configuration)
- [資料科學虛擬機器](/azure/machine-learning/data-science-virtual-machine/overview)

## <a name="challenges"></a>挑戰

- **資料隱私權合規性。** 您將個人資料提供給使用者用於自助式分析和報告時，必須十分謹慎。 基於組織原則和法令規定，可能會有合規性方面的事項需要注意。 

- **資料量。** 雖然為使用者提供完整資料來源的存取權可能有其效益，但這可能會導致執行時間冗長的 Excel 或 Power BI 作業，或是使用大量叢集資源的 Spark SQL 查詢。

- **使用者知識。** 使用者可建立自己的查詢與彙總，以傳達商業決策。 您確定使用者擁有取得正確結果所需的分析資料和查詢技能嗎？

- **共用結果。** 如果使用者可以建立和共用報表或視覺化資料，即可能有安全上的顧慮。

## <a name="architecture"></a>架構

雖然此案例的目標是要支援互動式資料分析，但資料科學所牽涉到的資料清理、取樣和結構化工作，常會包含長時間執行的程序。 此時，[批次處理](../big-data/batch-processing.md)就是適當的架構。

## <a name="technology-choices"></a>技術選擇

下列技術是 Azure 中的互動式資料探索適用的建議選項。

### <a name="data-storage"></a>資料儲存體

- **Azure 儲存體 Blob 容器**或 **Azure Data Lake Store**。 資料科學家通常會使用原始來源資料，以確定他們能夠存取資料中所有可能的特性、極端值和誤差。 在巨量資料案例中，此資料的形式通常會是資料存放區中的檔案。

如需詳細資訊，請參閱[資料儲存體](../technology-choices/data-storage.md)。

### <a name="batch-processing"></a>批次處理

- **R 伺服器**或 **Spark**。 大部分的資料科學家都會使用可有效支援數學與統計套件的程式設計語言，例如 R 或 Python。 在使用大量資料時，您可以使用允許此類語言使用分散式處理的平台，藉以降低延遲性。 R 伺服器可單獨使用，或與 Spark 搭配使用以相應放大 R 處理函式，而 Spark 原本即支援 Python 語言的類似相應放大功能。
- **Hive**。 在使用類似 SQL 的語意來轉換資料時，Hive 是不錯的選擇。 使用者可以使用在語意上類似於 SQL 的 HiveQL 陳述式，來建立及載入資料表。

如需詳細資訊，請參閱[批次處理](../technology-choices/batch-processing.md)。

### <a name="analytical-data-store"></a>分析資料存放區

- **Spark SQL**。 Spark SQL 是以 Spark 作為建置基礎的 API，可支援您建立能夠使用 SQL 語法來查詢的資料框架和資料表。 無論要分析的資料檔案是原始來源檔案還是已用批次程序清理並備妥的新檔案，使用者皆可定義其 Spark SQL 資料表，以進一步查詢分析。 
- **Hive**。 除了使用 Hive 對原始資料進行批次處理以外，您也可以根據資料存放所在的資料夾，建立包含 Hive 資料表和檢視的 Hive 資料庫，以啟用分析和報告的互動式查詢。 HDInsight 包含使用記憶體內部快取以縮短 Hive 查詢回應時間的 Interactive Hive 叢集類型。 熟悉類 SQL 語法的使用者，可以使用 Interactive Hive 來探索資料。

如需詳細資訊，請參閱[分析資料存放區](../technology-choices/analytical-data-stores.md)。

### <a name="analytics-and-reporting"></a>分析和報告

- **Jupyter**。 Jupyter Notebook 提供的瀏覽器架構介面，可使用 R、Python 或 Scala 等語言執行程式碼。 使用 R 伺服器或 Spark 對資料進行批次處理時，或使用 Spark SQL 定義要查詢之資料表的結構描述時，Jupyter 可能是查詢資料的理想選擇。 使用 Spark 時，您可以使用標準 Spark 資料框架 API 或是 Spark SQL API 和內嵌的 SQL 陳述式，來查詢資料及產生視覺效果。
- **深入探詢**。 如果您想要執行隨選資料瀏覽，[Apache Drill](https://drill.apache.org/) 是無結構描述的 SQL 查詢引擎。 它不需要結構描述，因此您可以從各種資料來源查詢資料，且引擎會自動了解資料的結構。
- **Interactive Hive 用戶端**。 如果您使用 Interactive Hive 叢集來查詢資料，您將可使用 Ambari 叢集儀表板中的 Hive 檢視、Beeline 命令列工具，或任何以 ODBC 為基礎的工具 (使用 Hive ODBC 驅動程式)，例如 Microsoft Excel 或 Power BI。

如需詳細資訊，請參閱[資料分析和報告技術](../technology-choices/analysis-visualizations-reporting.md)。

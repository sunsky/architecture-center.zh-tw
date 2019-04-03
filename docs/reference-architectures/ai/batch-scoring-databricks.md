---
title: Azure Databricks 上的 Spark 模型批次評分
description: 建置可調整的解決方案，使用 Azure Databricks 對排程上的 Apache Spark 分類模型進行批次評分。
author: njray
ms.date: 02/07/2019
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai
ms.openlocfilehash: cba8d272ddbdbf2c2da94f68b288e9fb79be7de2
ms.sourcegitcommit: 1a3cc91530d56731029ea091db1f15d41ac056af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887806"
---
# <a name="batch-scoring-of-spark-machine-learning-models-on-azure-databricks"></a>在 Azure Databricks 上的 Spark 機器學習服務的批次評分模型

此參考架構說明如何建置可調整的解決方案，使用 Azure Databricks (以 Apache Spark 為基礎，並已針對 Azure 最佳化的分析平台) 對排程上的 Apache Spark 分類模型進行批次評分。 此方案可作為範本，並可廣泛用於其他不同情況。

此架構的參考實作可在  [GitHub][github] 上取得。

![Azure Databricks 上的 Spark 模型批次評分](./_images/batch-scoring-spark.png)

**案例**：資產密集型產業的企業希望能最大限度地降低及縮短與非預期機械故障有關的成本和停機時間。 他們可以使用從機器收集的 IoT 數據，建立預測性維護模型。 此模型讓企業能夠主動維護元件，並在它們故障之前預先修復。 企業可以將機械元件物盡其用，藉此控制並縮短停機時間。

預測性維護模型會從機器收集資料，並保留元件故障的歷史範例。 然後，就可以使用模型監視元件的目前狀態，並預測指定元件是否很快就會故障。 如需一般使用案例和模型建立方式，請參閱[適用於預測性維護解決方案的 Azure AI 指南][ai-guide]。

此參考架構適用於當有元件機器的新資料出現時就會觸發的工作負載。 處理程序包含下列步驟：

1. 將資料從外部資料存放區內嵌到 Azure Databricks 資料存放區。

2. 透過將資料轉換為訓練資料集，然後建置 Spark MLlib 模型來訓練機器學習模型。 MLlib包含最常用的機器學習算法和已最佳化的公用程式，以利用 Spark 資料擴充性功能。

3. 透過將資料轉換成計分資料集，以套用訓練模型來預測 (分類) 元件故障。 運用 Spark MLLib 模型為資料評分。

4. 將結果儲存在後期處理耗用的 Databricks 資料存放區。

 [GitHub][github] 中提供了 Notebooks 以執行這些工作。

## <a name="architecture"></a>架構

此架構定義了完整包含在 [Azure Databricks] [databricks] (根據一組循序執行的 [notebooks][notebooks]) 內的資料流程。 它是由下列元件組成：

**[Data files][github]**。 參考實作使用包含在五個靜態資料檔案中的資組一組模擬資料集。

**[Ingestion][notebooks]**。 資料擷取 Notebook 會將輸入資料檔案下載到 Databricks 資料集的集合中。 在真實案例中，來自 IoT 裝置的資料會串流到 Databricks 可存取的儲存體，例如 Azure SQL Server 或 Azure Blob 儲存體。 Databricks 支援多個 [data sources][data-sources]。

**訓練管線**。 此 Notebook 會執行功能工程設計 Notebook，以從擷取的資料建立分析資料集。 接著它會使用 [Apache Spark MLlib][mllib] 可調整機器學習程式庫，執行可將機器學習模型定型的模型建置 Notebook。

**評分管線**。 此 Notebook 會執行功能工程設計 Notebook，以從擷取的資料建立評分資料集，然後執行評分 Notebook。 評分 Notebook 會使用定型的 [Spark MLlib][mllib-spark] 模型，以針對評分資料集中的觀測產生預測模型。 預測會儲存在結果存放區，這是 Databricks 資料存放區中的新資料集。

**排程器**。 已排程的 Databricks [job][job] 會使用 Spark 模型處理批次評分。 作業會執行評分管道 Notebook，通過 Notebook 參數傳遞變數引數，以指定詳細資料以構建評分資料集，以及指定儲存結果資料集的位置。

此案例會建構為管線流程。 每個筆記本已每項作業的批次設定進行最佳化：擷取、功能工程設計、模型建置，以及模型評分。 為實現此目的，功能工程設計 Notebook 的設計目的即在於針對任何定型、校準、測試或評分作業產生通用資料集。 在此案例中，我們針對這些作業使用暫時分割策略，讓 Notebook 參數能夠用來設定日期範圍篩選。

由於此案例會建立批次管線，因此我們會提供一組選擇性檢查 Notebook 來探索管線 Notebook 的輸出。 您可以在 GitHub 存放庫中上找到這些項目：

- `1a_raw-data_exploring`
- `2a_feature_exploration`
- `2b_model_testing`
- `3b_model_scoring_evaluation`

## <a name="recommendations"></a>建議

設定 Databricks 可讓您能夠載入及部署定型的模型，以搭配使用新資料來進行預測。 我們針對此案例使用 Databricks 的原因在於它提供了以下額外優點：

- 使用 Azure Active Directory 認證支援單一登入。
- 可針對生產環境管線執行作業的作業排程器。
- 使用共同作業、儀表板、REST API 的完整互動式 Notebook。
- 可以調整成任何規模的無限制叢集。
- 進階安全性、角色型存取控制和稽核記錄。

若要與 Azure Databricks 服務互動，請使用網頁瀏覽器中的 Databricks [Workspace][workspace] 介面，或 [command-line interface][cli] (CLI)。 從支援 Python 2.7.9 至 3.6 的任何平台存取 Databricks CLI。

參考實作會使用 [notebooks][notebooks] 依序執行工作。 每個 Notebook 都會將中繼資料成品 (定型、測試、評分或結果資料集) 儲存至相同的資料存放區作為輸入資料。 目標是要讓您能夠輕鬆地依據自己的特定使用情況運用資料。 在實務上，您會將資料來源連線至 Azure Databricks 執行個體，讓 Notebook 能夠直接在您的儲存體中讀取及寫入。

您可以視需要透過 Databricks 使用者介面、資料存放區，或 Databricks [CLI][cli] 監視作業執行。 使用 Databricks 提供的 [event log][log] 和其他 [metrics][metrics] 監視叢集。

## <a name="performance-considerations"></a>效能考量

Azure Databricks 叢集預設啟用自動調整規模功能，因此 Databricks 會在執行階段期間，動態重新配置背景工作角色，以考量您的作業特性。 您管線的特定部分可能會比其他部分需要更多運算需求。 Databricks 會在作業的這些階段期間新增其他背景工作角色 (並在不再需要時將它們移除)。 自動調整規模可讓您更輕鬆地達到高[叢集使用率][cluster]，因為您不需要佈建叢集以配合工作負載。

此外，可使用 [Azure Data Factory][adf] 搭配 Azure Databricks 開發更複雜的排程式管線。

## <a name="storage-considerations"></a>儲存體考量

在此參考實作中，資料會直接儲存在 Databricks 儲存體內，以簡化程序。 但是，在生產環境設定中，資料可以儲存在雲端資料存放區，例如 [Azure Blob 儲存體][blob]。 [Databricks][databricks-connect] 也支援 Azure Data Lake Store、Azure SQL 資料倉儲、Azure Cosmos DB、Apache Kafka 和 Hadoop。

## <a name="cost-considerations"></a>成本考量

Azure Databricks 是包含相關成本的進階 Spark 供應項目。 此外，也有標準和進階 Databricks [定價層][pricing]。

針對此案例，標準定價層已足夠。 不過如果您的特定應用程式需要自動調整叢集，以處理較大的工作負載或互動式 Databricks 儀表板，進階層級可能會進一步提高成本。

解決方案 Notebook 可在任何以 Spark 為基礎的平台上執行，只需最少的編輯即可移除 Databricks 特定封裝。 請參閱下列適用於各種 Azure 平台的類似解決方案：

- [Azure Machine Learning Studio 中的 Python][python-aml]
- [SQL Server R Services][sql-r]
- [Azure 資料科學虛擬機器上的 PySpark][py-dvsm]

## <a name="deploy-the-solution"></a>部署解決方案

若要部署此參考架構，請依照  [GitHub][github] 儲存機制中描述的步驟，在 Azure Databricks 上的批次中，針對評分 Spark 模型建置可調整規模的解決方案。

## <a name="related-architectures"></a>相關架構

我們也已經建置使用 Spark 的參考架構，搭配離線、預先計算的分數建置 [即時建議系統][recommendation]。 這些建議系統是以批次方式處理分數的常見案例。

[adf]: https://azure.microsoft.com/blog/operationalize-azure-databricks-notebooks-using-data-factory/
[ai-guide]: /azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance
[blob]: https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[cli]: https://docs.databricks.com/user-guide/dev-tools/databricks-cli.html
[cluster]: https://docs.azuredatabricks.net/user-guide/clusters/sizing.html
[databricks]: /azure/azure-databricks/
[databricks-connect]: /azure/azure-databricks/databricks-connect-to-data-sources
[data-sources]: https://docs.databricks.com/spark/latest/data-sources/index.html
[github]: https://github.com/Azure/BatchSparkScoringPredictiveMaintenance
[job]: https://docs.databricks.com/user-guide/jobs.html
[log]: https://docs.databricks.com/user-guide/clusters/event-log.html
[metrics]: https://docs.databricks.com/user-guide/clusters/metrics.html
[mllib]: https://docs.databricks.com/spark/latest/mllib/index.html
[mllib-spark]: https://docs.databricks.com/spark/latest/mllib/index.html#apache-spark-mllib
[notebooks]: https://docs.databricks.com/user-guide/notebooks/index.html
[pricing]: https://azure.microsoft.com/en-us/pricing/details/databricks/
[python-aml]: https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1
[py-dvsm]: https://gallery.azure.ai/Tutorial/Predictive-Maintenance-using-PySpark
[recommendation]: /azure/architecture/reference-architectures/ai/real-time-recommendation
[sql-r]: https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1
[workspace]: https://docs.databricks.com/user-guide/workspace.html

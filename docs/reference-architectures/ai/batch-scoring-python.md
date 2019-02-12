---
title: Azure 上的 Python 模型批次評分
description: 使用 Azure Machine Learning 服務組建可根據排程對模型進行批次評分的可調整解決方案。
author: njray
ms.date: 01/30/2019
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai, AI
ms.openlocfilehash: 81dc353735eaa6573c72d9e588c949fe96a329ef
ms.sourcegitcommit: eee3a35dd5a5a2f0dc117fa1c30f16d6db213ba2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55782008"
---
# <a name="batch-scoring-of-python-models-on-azure"></a>Azure 上的 Python 模型批次評分

此參考架構會示範如何使用 Azure Machine Learning 服務組建可根據排程對許多模型進行批次評分的可調整解決方案。 此方案可作為範本，並可廣泛用於不同的問題。

此架構的參考實作可在 [GitHub][github] 上取得。

![Azure 上的 Python 模型批次評分](./_images/batch-scoring-python.png)

**案例**：此解決方案可監視 IoT 設定中大量裝置的作業，這其中的每個裝置都會持續傳送感應器讀數。 我們假設每個裝置已與預先定型的異常偵測模型相關聯，而這些模型可用來預測一系列的量值 (在預先定義的時間間隔內彙總) 是否會對應到異常狀況。 在真實案例中，這可能是需要先進行篩選和彙總的感應器讀數資料流，然後才能用於訓練和即時評分。 為了方便起見，此解決方案會在執行評分作業時使用相同的資料檔案。

此參考架構是針對根據排程觸發的工作負載而設計的。 處理程序包含下列步驟：
1.  將擷取的感應器讀數傳送至 Azure 事件中樞。
2.  執行資料流處理並儲存未經處理資料。
3.  將資料傳送至已準備好開始工作的 Machine Learning 叢集。 叢集中的每個節點都會針對特定感應器執行評分作業。 
4.  執行評分管線，這會使用 Machine Learning Python 指令碼平行執行評分作業。 系統會建立、發佈管線，並將其排定在預先定義的時間間隔上執行。
5.  產生預測並將它們儲存在 Blob 儲存體中，供後續使用。

## <a name="architecture"></a>架構

此架構由下列元件組成：

[Azure 事件中樞][event-hubs]。 此訊息擷取服務每秒可內嵌數百萬個事件訊息。 在此架構中，感應器會將資料流傳送到事件中樞。

[Azure 串流分析][stream-analytics]。 事件處理引擎。 串流分析作業會從事件中樞讀取資料流，並執行串流處理。

[Azure SQL Database][sql-database]。 來自感應器讀數的資料會載入至 SQL Database。 SQL 是儲存已處理串流資料 (即表格式和結構化資料) 的熟悉方式，但可以使用其他資料存放區。

[Azure Machine Learning 服務][amls]。 Machine Learning 是雲端服務，用於訓練、評分、部署和管理大規模的機器學習模型。 在批次評分的內容中，Machine Learning 會依需求使用自動調整選項來建立虛擬機器，而叢集中的每個節點都會執行特定感應器的評分作業。 評分作業是以 Python 指令碼步驟形式平行執行，而這些步驟由 Machine Learning 置入佇列和管理。 這些步驟屬於系統建立、發佈，並排定在預先定義時間間隔執行的 Machine Learning 管線。

[Azure Blob 儲存體][storage]。 Blob 容器會用來儲存預先定型的模型、資料和輸出預測。 模型會上傳至 [01_create_resources.ipynb][create-resources] 筆記本中的 Blob 儲存體。 用來訓練這些[單一類別 SVM][one-class-svm] 模型的資料，代表著不同裝置上不同感應器的值。 此解決方案會假設資料值是在固定時間間隔上彙總的。

[Azure Container Registry][acr]。 評分 Python [指令碼][pyscript] 會在建立於每個叢集節點上的 Docker 容器中執行，並在其中讀取相關的感應器資料、產生預測，然後將這些預測儲存在 Blob 儲存體中。

## <a name="performance-considerations"></a>效能考量

對於標準 Python 模型，普遍認為 CPU 足以處理工作負載。 此架構會使用 CPU。 但是，針對[深度學習工作負載][deep]，GPU 的效能通常遠勝於 CPU，但若只能使用 CPU，通常需要大量 CPU 才能擁有相當的效能。

### <a name="parallelizing-across-vms-vs-cores"></a>在 VM 與核心上平行處理

若要以批次模式執行許多模型的評分程序，這些作業必須在各 VM 上平行進行。 這可能會有兩種方法：

* 使用低成本 VM 建立更大的叢集。

* 使用高效能 VM 建立較小的叢集，而每個 VM 上有多個可用核心。

一般情況下，標準 Python 模型的評分需求與深度學習模型的評分需求不同，小型叢集應能夠有效率地處理大量已排入佇列的模型。 您可以在資料集大小增加時增加叢集節點數目。

為了方便起見，在此情節中，某個評分工作會在單一 Machine Learning 管線步驟內提交。 但是，在相同管線步驟內評分多個資料區塊其實會更有效率。 在這些情況下，可撰寫自訂程式碼在多個資料集中進行讀取，以及在執行單一步驟期間對這些資料集執行評分指令碼。

## <a name="management-considerations"></a>管理考量

- **監視作業**。 監視執行中作業的進度相當重要，但要監視叢集上的作用中節點可能很困難。 若要檢查叢集中節點的狀態，請使用 [Azure 入口網站][ portal]來管理[機器學習工作區][ml-workspace]。 如果節點狀態是非使用中或作業已失敗，則錯誤記錄會儲存在 Blob 儲存體中，您也可在 Pipelines 區段中存取錯誤記錄。 如需更多監視功能，您可以將記錄連線到 [Application Insights][app-insights]，或執行不同處理程序來對叢集和其作業狀態進行輪詢。
-   **記錄**。 Machine Learning 服務會將所有 stdout/stderr 記錄至相關聯的 Azure 儲存體帳戶。 若要輕鬆檢視記錄檔，請使用 [Azure 儲存體總管][explorer]之類的儲存體瀏覽工具。

## <a name="cost-considerations"></a>成本考量

此參考架構中成本最高的元件是計算資源。 計算叢集大小可根據佇列中的作業來相應增加和減少。 修改計算的佈建設定，透過 Python SDK 以程式設計方式啟用自動調整。 或者，使用 [Azure CLI][cli] 來設定叢集的自動調整參數。

對於不需要立即處理的工作，可設定自動調整公式，如此一來，預設狀態 (最小值) 就是零個節點的叢集。 使用此設定時，叢集一開始會有零個節點，並只在偵測到佇列中有作業時，才會相應增加。 如果批次評分程序一天只會發生幾次 (或更少)，則此設定可省下大量成本。

自動調整可能不適合間距太近的批次作業。 啟動及關閉叢集所花費的時間也會產生成本，因此如果批次工作負載會在前一個作業結束後的幾分鐘內啟動，那麼讓叢集在作業之間持續運作可能比較符合成本效益。 這取決於評分程序是排定為高頻率執行 (例如每小時) 或低頻率執行 (例如一個月一次)。


## <a name="deployment"></a>部署

若要部署此參考架構，請遵循 [GitHub 存放庫][github]中所述的步驟。

[acr]: /azure/container-registry/container-registry-intro
[ai]: /azure/application-insights/app-insights-overview
[aml-compute]: /azure/machine-learning/service/how-to-set-up-training-targets#amlcompute
[amls]: /azure/machine-learning/service/overview-what-is-azure-ml
[automatic-scaling]: /azure/batch/batch-automatic-scaling
[azure-files]: /azure/storage/files/storage-files-introduction
[cli]: https://docs.microsoft.com/en-us/cli/azure
[create-resources]: https://github.com/Microsoft/AMLBatchScoringPipeline/blob/master/01_create_resources.ipynb
[deep]: /azure/architecture/reference-architectures/ai/batch-scoring-deep-learning
[event-hubs]: /azure/event-hubs/event-hubs-geo-dr
[explorer]: https://azure.microsoft.com/en-us/features/storage-explorer/
[github]: https://github.com/Microsoft/AMLBatchScoringPipeline
[one-class-svm]: http://scikit-learn.org/stable/modules/generated/sklearn.svm.OneClassSVM.html
[portal]: https://portal.azure.com
[ml-workspace]: https://docs.microsoft.com/en-us/azure/machine-learning/studio/create-workspace
[python-script]: https://github.com/Azure/BatchAIAnomalyDetection/blob/master/batchai/predict.py
[pyscript]: https://github.com/Microsoft/AMLBatchScoringPipeline/blob/master/scripts/predict.py
[storage]: /azure/storage/blobs/storage-blobs-overview
[stream-analytics]: /azure/stream-analytics/
[sql-database]: https://docs.microsoft.com/en-us/azure/sql-database/
[app-insights]: https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview

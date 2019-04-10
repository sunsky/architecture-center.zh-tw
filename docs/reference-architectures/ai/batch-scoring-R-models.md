---
title: 批次評分，以在 Azure 上的 R 模型
description: 執行批次評分，以使用 Azure Batch 和根據零售商店銷售預測的資料集的 R 模型。
author: njray
ms.date: 03/29/2019
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai
ms.openlocfilehash: 4fa57168c337b01c8e7d0fc86ba54fee59a7ae47
ms.sourcegitcommit: 1a3cc91530d56731029ea091db1f15d41ac056af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887948"
---
# <a name="batch-scoring-of-r-machine-learning-models-on-azure"></a>在 Azure 批次計分的 R 機器學習服務模型

此參考架構示範如何執行批次評分，以使用 Azure Batch 的 R 模型。 案例根據零售商店銷售預測，但是這個架構可以歸納需要大型 scaler 使用 R 模型的預測產生的任何案例。 此架構的參考實作可在 [GitHub][github] 上取得。

![架構圖表][0]

**案例**：連鎖必須預測未來的季銷售的產品。 預測可讓公司更佳地管理其供應鏈，並確保它可以符合對在其存放區的每個產品的需求。 因為新的銷售資料，從上一週就會變成可用，而行銷策略的下一季的產品則設定，該公司將更新其預測每週。 分量預測會產生以估計的不確定性的個別銷售預測。

處理程序包含下列步驟：

1. Azure 邏輯應用程式就會觸發每週一次預測的產生程序。

1. 邏輯應用程式會啟動 Azure 容器執行個體執行排程器觸發程序的計分工作批次在叢集上的 Docker 容器。

1. 批次叢集中的節點之間平行執行評分的工作。 每個節點：

    1. 從 Docker Hub 的 Docker 映像提取背景工作角色，並啟動容器。

    1. 讀取輸入的資料，並預先定型的 R 模型，從 Azure Blob 儲存體。

    1. 分數的資料來產生預測。

    1. 將預測的結果寫入至 blob 儲存體。

下圖顯示一個存放區中的四個產品 (Sku) 的預測的銷售量。 黑色線條是銷售的歷程記錄、 虛線是預測的中間值 (q50)、 粉紅色的頻外代表第 25 和 seventy 第五個百分位數，而藍色的頻外代表第五個和九十第五個百分位數。

![銷售預測][1]

## <a name="architecture"></a>架構

此架構由下列元件組成。

[Azure Batch] [ batch]用來以平行方式執行預測的產生作業，在叢集上的虛擬機器。 預測可使用預先定型的機器學習服務 R Azure Batch 中實作的模型可以自動調整規模的作業提交至叢集數目為基礎的 Vm 數目。 每個節點，來評分資料，並產生預測的 Docker 容器內執行 R 指令碼。

[Azure Blob 儲存體][ blob]用來儲存輸入的資料、 預先定型的機器學習服務模型和預測的結果。 它提供非常符合成本效益的儲存體，需要這個工作負載的效能。

[Azure Container Instances] [ aci]隨需提供無伺服器計算。 在此情況下，觸發程序產生之預測的批次作業的排程部署容器執行個體。 批次作業會觸發從 R 指令碼使用[doAzureParallel][doAzureParallel]封裝。 容器執行個體會自動關閉作業完成之後。

[Azure Logic Apps] [ logic-apps]部署容器執行個體上的排程來觸發整個工作流程。 在 Logic Apps 中的 Azure Container Instances 連接器可讓部署觸發程序事件範圍的執行個體。

## <a name="performance-considerations"></a>效能考量

### <a name="containerized-deployment"></a>容器化的部署

使用此架構中，所有的 R 指令碼執行內[Docker](https://www.docker.com/)容器。 這可確保指令碼執行在一致的環境中，使用相同的 R 版本和套件的版本中，每次。 不同的 Docker 映像用於 「 排程器 」 和 「 背景工作的容器，因為每個有一組不同的 R 封裝相依性。

Azure Container Instances 提供無伺服器環境來執行排程器的容器。 排程器容器會執行 R 指令碼，在 Azure Batch 的叢集上執行的個別評分工作觸發程序。

批次叢集中的每個節點會執行背景工作角色容器，它會執行評分指令碼。

### <a name="parallelizing-the-workload"></a>平行處理工作負載

當批次計分的 R 模型的資料，請考慮如何平行處理工作負載。 輸入的資料必須以某種方式分割，以便評分作業可以分散到叢集節點。 請嘗試不同的方法，來探索散發您的工作負載的最佳選擇。 以案例的基礎，請考慮下列各項：

- 可以載入及處理記憶體中的單一節點多少資料。
- 啟動每個批次作業的額外負荷。
- 載入的 R 模型的額外負荷。

在此範例使用案例中，模型物件很大，而且只要幾秒產生適用於個別產品的預測。 基於這個理由，您可以分組產品並執行每個節點的單一批次作業。 在迴圈內的每個工作會循序產生產品的預測。 這個方法其實是最有效率的方式，來平行處理這個特定的工作負載。 它可避免啟動許多較小的批次作業和重複載入 R 模型的額外負荷。

替代方法是以觸發每項產品的一個批次作業。 Azure Batch 自動形成工作的佇列，並將其提交至叢集上執行，因為節點變成可用。 使用[自動調整][ autoscale]調整的作業數目根據叢集中的節點數目。 如果要花較長的時間才能完成每個計分的作業，對齊的啟動工作並重新載入模型物件的額外負荷，這種方法會比較合適。 這種方法也會更容易實作，並可讓您使用自動調整的彈性 — 很重要的考量，如果事先不知道的總工作負載的大小。

## <a name="monitoring-and-logging-considerations"></a>監視和記錄的考量

### <a name="monitoring-azure-batch-jobs"></a>監視 Azure Batch 作業

監視並終止批次作業，從**作業**窗格中的 Azure 入口網站中的 Batch 帳戶。 監視批次的叢集，包括個別的節點的狀態從**集區**窗格。

### <a name="logging-with-doazureparallel"></a>DoAzureParallel 記錄

DoAzureParallel 套件會自動收集所有 stdout/stderr 中每個工作提交 Azure Batch 上的記錄的檔。 這些可於安裝時建立的儲存體帳戶中。 若要檢視它們，請使用儲存體瀏覽工具這類[Azure 儲存體總管][ storage-explorer]或 Azure 入口網站。

若要在開發期間，快速偵錯的批次作業，列印在本機 R 工作階段使用的記錄檔[getJobFiles][getJobFiles] doAzureParallel 函式。

## <a name="cost-considerations"></a>成本考量

在此參考架構中使用的計算資源是成本最高的元件。 此案例中，每當作業會觸發並關閉作業完成之後，就會建立一個固定大小的叢集。 只有當啟動、 執行，或正在關閉叢集節點時，即會產生費用。 這個方法很適合案例，其中要產生預測所需的計算資源保持相當穩定工作作業。

在完成工作所需的計算數量無法事先知道的情況下，可能更適合使用自動調整。 使用此方法時，叢集的大小被相應增加或減少作業的大小而定。 Azure Batch 支援一組定義叢集中使用時，您可以設定自動調整公式[doAzureParallel][doAzureParallel] API。

某些情況下，作業之間的時間可能太短而無法關閉，並啟動叢集。 在這些情況下，請視執行作業之間的叢集。

Azure 批次和 doAzureParallel 支援低優先順序的 Vm 使用。 這些 Vm 會隨附一個可觀的折扣，但由其他較高優先權工作負載所適用的風險。 這些 Vm 會因此不建議使用重要生產工作負載。 不過，它們是非常適用於實驗或開發工作負載。

## <a name="deployment"></a>部署

若要部署此參考架構，請依照下列所述的步驟[GitHub][github]存放庫。


[0]: ./_images/batch-scoring-r-models.png
[1]: ./_images/sales-forecasts.png
[aci]: /azure/container-instances/container-instances-overview
[autoscale]: /azure/batch/batch-automatic-scaling
[batch]: /azure/batch/batch-technical-overview
[blob]: /azure/storage/blobs/storage-blobs-introduction
[doAzureParallel]: https://github.com/Azure/doAzureParallel/blob/master/docs/32-autoscale.md
[getJobFiles]: /azure/machine-learning/service/how-to-train-ml-models
[github]: https://github.com/Azure/RBatchScoring
[logic-apps]: /azure/logic-apps/logic-apps-overview
[storage-explorer]: /azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows
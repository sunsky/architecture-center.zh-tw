---
title: Azure 上的電影推薦
description: 透過機器學習和 Azure 資料科學虛擬機器 (DSVM) 使用機器學習來自動推薦電影、產品和其他建議，以在 Azure 上訓練模型。
author: njray
ms.date: 01/09/2019
ms.custom: azcat-ai, AI
social_image_url: /azure/architecture/example-scenario/ai/media/architecture-movie-recommender.png
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.openlocfilehash: be7959c1201e3a2aaf92c192d73a0ca47a990934
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58249643"
---
# <a name="movie-recommendations-on-azure"></a>Azure 上的電影推薦

此範例案例顯示某家企業如何使用機器學習來自動為其客戶提供產品推薦。 將使用 Azure 資料科學虛擬機器 (DSVM) 在 Azure 上訓練模型，以便根據電影獲得的評分，向使用者推薦電影。

從零售業、新聞到媒體等各種產業中，推薦功能非常有用。 潛在的應用情境包括在虛擬商店中推薦產品、推薦新聞或發布貼文，或者推薦音樂。 在傳統上，企業必須聘僱並培訓助理，以便向客戶提供個人化的推薦。 現在，我們可以利用 Azure 來訓練模型以了解客戶的喜好，從而提供大規模的客製化推薦。

## <a name="relevant-use-cases"></a>相關使用案例

請針對下列使用案例考慮此案例：

* 網站上的電影推薦。
* 行動裝置應用程式中的消費者產品推薦。
* 串流媒體中的新聞推薦。

## <a name="architecture"></a>架構

![用於定型電影建議的機器學習模型架構][architecture]

此案例在電影評分資料集上使用 Spark [替代最小平方][als] (ALS) 演算法，來訓練和評估機器學習模型。 此案例的步驟如下：

1. 前端網站或應用程式服務會收集使用者與影片互動的歷程記錄資料，這些資料會顯示於使用者、項目和數字評分元組的資料表中。

2. 收集的歷程記錄資料會儲存在 Blob 儲存體中。

3. 通常使用 DSVM 來實驗 Spark ALS 建議程式模型或將其產品化。 ALS 模型是使用訓練資料集來進行訓練，該資料集透過套用適當的資料分割策略從整體資料集所產生。 例如，您可以視商務要求而定，以隨機、依時間先後順序或分層方式將資料集分割為數個集合。 類似於其他機器學習工作，可以使用評估度量 (例如，precision\@*k*、recall\@*k*、[MAP][map]、[nDCG\@k][ndcg]) 來驗證建議程式。

4. Azure Machine Learning 服務用於協調實驗，例如超參數清除和模型管理。

5. Azure Cosmos DB 中保留了訓練的模型，然後可以應用該模型向指定的使用者推薦排名前 *k* 名的電影。

6. 接著，使用 Azure Container Instances 或 Azure Kubernetes Service 將該模型部署到 Web 或應用程式服務上。

如需建置和縮放建議程式服務的深入指南，請參閱[在 Azure 上建置即時建議 API][ref-arch]。

### <a name="components"></a>元件

* [資料科學虛擬機器][dsvm] (DSVM) 是一種 Azure 虛擬機器，其中包含可用於機器學習和資料科學的深度學習架構和工具。 DSVM 具有可用於執行 ALS 的獨立 Spark 環境。

* [Azure Blob 儲存體][blob]儲存電影推薦的資料集。

* [Azure Machine Learning 服務][mls]用於加速機器學習模型的建置、管理和部署。

* [Azure Cosmos DB][cosmosdb] 啟用全域散發和多模型資料庫儲存體。

* [Azure Container Instances][aci] 用於將訓練的模型部署到 Web 或應用程式服務 (可選擇使用 [Azure Kubernetes Service][aks])。

### <a name="alternatives"></a>替代項目

[Azure Databricks][databricks] 是可以執行模型訓練和評估的受控 Spark 叢集。 您可以在幾分鐘內設定受控 Spark 環境，並可向上和向下[自動縮放][autoscale]，以協助減少與手動縮放叢集相關聯的資源和成本。 另一種節省資源的選項是將非作用中的[叢集][clusters]設定為自動終止。

## <a name="considerations"></a>考量

### <a name="availability"></a>可用性

機器學習建置應用程式分成兩個資源元件：用於訓練的資源和用於服務的資源。 用於訓練的資源通常不需要高可用性，因為即時生產環境要求不會直接存取這些資源。 用於服務的資源需要具有高可用性，以便滿足客戶的要求。

對用於訓練的資源，全球[多個區域][regions]中都已推出符合虛擬機器[服務等級協定][sla] (SLA) 的 DSVM。 對用於服務的資源，Azure Kubernetes Service 提供[高可用性][ha]的基礎結構。 代理程式節點也會遵循虛擬機器的 [SLA][sla-aks]。

### <a name="scalability"></a>延展性

如果您有大型的資料，則可以調整 DSVM 以縮短訓練時間。 可以藉由變更 [VM 大小][vm-size]來相應擴充或縮減 VM。 選擇夠大的記憶體大小以符合記憶體內部資料集和更高的 vCPU 計數，以減少訓練所需的時間。

### <a name="security"></a>安全性

您可以在此案例中使用 Azure Active Directory 對[存取 DSVM][dsvm-id] 的使用者進行驗證，其中包含程式碼、模型和 (記憶體內部) 的資料。 資料會先儲存在 Azure 儲存體上，然後再載入 DSVM 上，且會使用[儲存體服務加密][storage-security]自動將資料加密。 可以透過 Azure Active Directory 驗證或角色型存取控制來管理權限。

## <a name="deploy-this-scenario"></a>部署此案例

**必要條件**：您必須具有現有的 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [[免費帳戶]][free]。

[Microsoft Recommender 存放庫][github]中提供了此案例的所有程式碼。

請遵循下列步驟來執行 [ALS 快速入門筆記本][notebook]：

1. 從 Azure 入口網站[建立 DSVM][dsvm-ubuntu]。

2. 複製 Notebooks 資料夾中的存放庫：

    ```shell
    cd notebooks
    git clone https://github.com/Microsoft/Recommenders
    ```

3. 按照 [SETUP.md][setup] 檔案中所述的步驟安裝 conda 相依性。

4. 在瀏覽器中，移至 jupyterlab VM 並瀏覽至 `notebooks/00_quick_start/als_pyspark_movielens.ipynb`。

5. 執行筆記本。

## <a name="related-resources"></a>相關資源

如需建置和縮放建議程式服務的深入指南，請參閱[在 Azure 上建置即時建議 API][ref-arch]。 如需推薦系統的教學課程和範例，請參閱 [Microsoft Recommender 存放庫][github]。

[architecture]: ./media/architecture-movie-recommender.png
[aci]: /azure/container-instances/container-instances-overview
[aad]: /azure/active-directory-b2c/active-directory-b2c-overview
[aks]: /azure/aks/intro-kubernetes
[als]: https://spark.apache.org/docs/latest/ml-collaborative-filtering.html
[autoscale]: https://docs.azuredatabricks.net/user-guide/clusters/sizing.html#autoscaling
[blob]: /azure/storage/blobs/storage-blobs-introduction
[clusters]: https://docs.azuredatabricks.net/user-guide/clusters/configure.html
[cosmosdb]: /azure/cosmos-db/introduction
[databricks]: /azure/azure-databricks/what-is-azure-databricks
[dsvm]: /azure/machine-learning/data-science-virtual-machine/overview
[dsvm-id]: /azure/machine-learning/data-science-virtual-machine/dsvm-common-identity
[dsvm-ubuntu]: /azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro
[free]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[github]: https://github.com/Microsoft/Recommenders
[ha]: /azure/aks/container-service-quotas
[map]: https://en.wikipedia.org/wiki/Evaluation_measures_(information_retrieval)
[mls]: /azure/machine-learning/service/
[n-tier]: /azure/architecture/reference-architectures/n-tier/n-tier-cassandra
[ndcg]: https://en.wikipedia.org/wiki/Discounted_cumulative_gain
[notebook]: https://github.com/Microsoft/Recommenders/notebooks/00_quick_start/als_pyspark_movielens.ipynb
[ref-arch]: /azure/architecture/reference-architectures/ai/real-time-recommendation
[regions]: https://azure.microsoft.com/en-us/global-infrastructure/services/?products=virtual-machines&regions=all
[resiliency]: /azure/architecture/resiliency/
[sec-docs]: /azure/security/
[setup]: https://github.com/Microsoft/Recommenders/blob/master/SETUP.md%60
[sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_8/
[sla-aks]: https://azure.microsoft.com/en-us/support/legal/sla/kubernetes-service/v1_0/
[storage-security]: /azure/storage/common/storage-service-encryption
[vm-size]: /azure/virtual-machines/virtual-machines-linux-change-vm-size

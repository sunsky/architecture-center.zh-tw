---
title: Azure 上的電影推薦
description: 透過機器學習和 Azure 資料科學虛擬機器 (DSVM) 使用機器學習來自動推薦電影、產品和其他建議，以在 Azure 上訓練模型。
author: njray
ms.date: 1/9/2019
ms.custom: azcat-ai
social_image_url: /azure/architecture/example-scenario/ai/media/architecture-movie-recommender.png
ms.openlocfilehash: 9e68f38cb61d7a3255b76a662c58907704914052
ms.sourcegitcommit: 3b15d65e7c35a19506e562c444343f8467b6a073
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2019
ms.locfileid: "54908251"
---
# <a name="movie-recommendations-on-azure"></a><span data-ttu-id="ae484-103">Azure 上的電影推薦</span><span class="sxs-lookup"><span data-stu-id="ae484-103">Movie recommendations on Azure</span></span>

<span data-ttu-id="ae484-104">此範例案例顯示某家企業如何使用機器學習來自動為其客戶提供產品推薦。</span><span class="sxs-lookup"><span data-stu-id="ae484-104">This example scenario shows how a business can use machine learning to automate product recommendations for their customers.</span></span> <span data-ttu-id="ae484-105">將使用 Azure 資料科學虛擬機器 (DSVM) 在 Azure 上訓練模型，以便根據電影獲得的評分，向使用者推薦電影。</span><span class="sxs-lookup"><span data-stu-id="ae484-105">An Azure Data Science Virtual Machine (DSVM) is used to train a model on Azure that recommends movies to users based on ratings that have been given to movies.</span></span>

<span data-ttu-id="ae484-106">從零售業、新聞到媒體等各種產業中，推薦功能非常有用。</span><span class="sxs-lookup"><span data-stu-id="ae484-106">Recommendations can be useful in various industries from retail to news to media.</span></span> <span data-ttu-id="ae484-107">潛在的應用情境包括在虛擬商店中推薦產品、推薦新聞或發布貼文，或者推薦音樂。</span><span class="sxs-lookup"><span data-stu-id="ae484-107">Potential applications include providing  product recommendations in a virtual store, providing news or post recommendations, or providing music recommendations.</span></span> <span data-ttu-id="ae484-108">在傳統上，企業必須聘僱並培訓助理，以便向客戶提供個人化的推薦。</span><span class="sxs-lookup"><span data-stu-id="ae484-108">Traditionally, businesses had to hire and train assistants to make personalized recommendations to customers.</span></span> <span data-ttu-id="ae484-109">現在，我們可以利用 Azure 來訓練模型以了解客戶的喜好，從而提供大規模的客製化推薦。</span><span class="sxs-lookup"><span data-stu-id="ae484-109">Today, we can  provide customized recommendations at scale by utilizing Azure to train models to understand customer preferences.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="ae484-110">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="ae484-110">Relevant use cases</span></span>

<span data-ttu-id="ae484-111">請針對下列使用案例考慮此案例：</span><span class="sxs-lookup"><span data-stu-id="ae484-111">Consider this scenario for the following use cases:</span></span>

* <span data-ttu-id="ae484-112">網站上的電影推薦。</span><span class="sxs-lookup"><span data-stu-id="ae484-112">Movie recommendations on a website.</span></span>
* <span data-ttu-id="ae484-113">行動裝置應用程式中的消費者產品推薦。</span><span class="sxs-lookup"><span data-stu-id="ae484-113">Consumer product recommendations in a mobile app.</span></span>
* <span data-ttu-id="ae484-114">串流媒體中的新聞推薦。</span><span class="sxs-lookup"><span data-stu-id="ae484-114">News recommendations on streaming media.</span></span>

## <a name="architecture"></a><span data-ttu-id="ae484-115">架構</span><span class="sxs-lookup"><span data-stu-id="ae484-115">Architecture</span></span>

![用於定型電影建議的機器學習模型架構][architecture]

<span data-ttu-id="ae484-117">此案例在電影評分資料集上使用 Spark [替代最小平方][als] (ALS) 演算法，來訓練和評估機器學習模型。</span><span class="sxs-lookup"><span data-stu-id="ae484-117">This scenario covers the training and evaluating of the machine learning model using the Spark [alternating least squares][als] (ALS) algorithm on a dataset of movie ratings.</span></span> <span data-ttu-id="ae484-118">此案例的步驟如下：</span><span class="sxs-lookup"><span data-stu-id="ae484-118">The steps for this scenario are as following:</span></span>

1. <span data-ttu-id="ae484-119">前端網站或應用程式服務會收集使用者與影片互動的歷程記錄資料，這些資料會顯示於使用者、項目和數字評分元組的資料表中。</span><span class="sxs-lookup"><span data-stu-id="ae484-119">The front-end website or app service collects historical data of user-movie interactions, which are represented in a table of user, item, and numerical rating tuples.</span></span>

2. <span data-ttu-id="ae484-120">收集的歷程記錄資料會儲存在 Blob 儲存體中。</span><span class="sxs-lookup"><span data-stu-id="ae484-120">The collected historical data is stored in a blob storage.</span></span>

3. <span data-ttu-id="ae484-121">通常使用 DSVM 來實驗 Spark ALS 建議程式模型或將其產品化。</span><span class="sxs-lookup"><span data-stu-id="ae484-121">A DSVM is often used to experiment with or productize a Spark ALS recommender model.</span></span> <span data-ttu-id="ae484-122">ALS 模型是使用訓練資料集來進行訓練，該資料集透過套用適當的資料分割策略從整體資料集所產生。</span><span class="sxs-lookup"><span data-stu-id="ae484-122">The ALS model is trained using a training dataset, which is produced from the overall dataset by applying the appropriate data splitting strategy.</span></span> <span data-ttu-id="ae484-123">例如，您可以視商務要求而定，以隨機、依時間先後順序或分層方式將資料集分割為數個集合。</span><span class="sxs-lookup"><span data-stu-id="ae484-123">For example, the dataset can be split into sets randomly, chronologically, or stratified, depending on the business requirement.</span></span> <span data-ttu-id="ae484-124">類似於其他機器學習工作，可以使用評估度量 (例如，precision\@*k*、recall\@*k*、[MAP][map]、[nDCG\@k][ndcg]) 來驗證建議程式。</span><span class="sxs-lookup"><span data-stu-id="ae484-124">Similar to other machine learning tasks, a recommender is validated by using evaluation metrics (for example, precision\@*k*, recall\@*k*, [MAP][map], [nDCG\@k][ndcg]).</span></span>

4. <span data-ttu-id="ae484-125">Azure Machine Learning 服務用於協調實驗，例如超參數清除和模型管理。</span><span class="sxs-lookup"><span data-stu-id="ae484-125">Azure Machine Learning service is used for coordinating the experimentation, such as hyperparameter sweeping and model management.</span></span>

5. <span data-ttu-id="ae484-126">Azure Cosmos DB 中保留了訓練的模型，然後可以應用該模型向指定的使用者推薦排名前 *k* 名的電影。</span><span class="sxs-lookup"><span data-stu-id="ae484-126">A trained model is preserved on Azure Cosmos DB, which can then be applied for recommending the top *k* movies for a given user.</span></span>

6. <span data-ttu-id="ae484-127">接著，使用 Azure Container Instances 或 Azure Kubernetes Service 將該模型部署到 Web 或應用程式服務上。</span><span class="sxs-lookup"><span data-stu-id="ae484-127">The model is then deployed onto a web or app service by using Azure Container Instances or Azure Kubernetes Service.</span></span>

<span data-ttu-id="ae484-128">如需建置和縮放建議程式服務的深入指南，請參閱[在 Azure 上建置即時建議 API][ref-arch]。</span><span class="sxs-lookup"><span data-stu-id="ae484-128">For an in-depth guide to building and scaling a recommender service, see [Build a real-time recommendation API on Azure][ref-arch].</span></span>

### <a name="components"></a><span data-ttu-id="ae484-129">元件</span><span class="sxs-lookup"><span data-stu-id="ae484-129">Components</span></span>

* <span data-ttu-id="ae484-130">[資料科學虛擬機器][dsvm] (DSVM) 是一種 Azure 虛擬機器，其中包含可用於機器學習和資料科學的深度學習架構和工具。</span><span class="sxs-lookup"><span data-stu-id="ae484-130">[Data Science Virtual Machine][dsvm] (DSVM) is an Azure virtual machine with deep learning frameworks and tools for machine learning and data science.</span></span> <span data-ttu-id="ae484-131">DSVM 具有可用於執行 ALS 的獨立 Spark 環境。</span><span class="sxs-lookup"><span data-stu-id="ae484-131">The DSVM has a standalone Spark environment that can be used to run ALS.</span></span>

* <span data-ttu-id="ae484-132">[Azure Blob 儲存體][blob]儲存電影推薦的資料集。</span><span class="sxs-lookup"><span data-stu-id="ae484-132">[Azure Blob storage][blob] stores the dataset for movie recommendations.</span></span>

* <span data-ttu-id="ae484-133">[Azure Machine Learning 服務][mls]用於加速機器學習模型的建置、管理和部署。</span><span class="sxs-lookup"><span data-stu-id="ae484-133">[Azure Machine Learning service][mls] is used to accelerate the building, managing, and deploying of machine learning models.</span></span>

* <span data-ttu-id="ae484-134">[Azure Cosmos DB][cosmosdb] 啟用全域散發和多模型資料庫儲存體。</span><span class="sxs-lookup"><span data-stu-id="ae484-134">[Azure Cosmos DB][cosmosdb] enables globally distributed and multi-model database storage.</span></span>

* <span data-ttu-id="ae484-135">[Azure Container Instances][aci] 用於將訓練的模型部署到 Web 或應用程式服務 (可選擇使用 [Azure Kubernetes Service][aks])。</span><span class="sxs-lookup"><span data-stu-id="ae484-135">[Azure Container Instances][aci] is used to deploy the trained models to web or app services, optionally using [Azure Kubernetes Service][aks].</span></span>

### <a name="alternatives"></a><span data-ttu-id="ae484-136">替代項目</span><span class="sxs-lookup"><span data-stu-id="ae484-136">Alternatives</span></span>

<span data-ttu-id="ae484-137">[Azure Databricks][databricks] 是可以執行模型訓練和評估的受控 Spark 叢集。</span><span class="sxs-lookup"><span data-stu-id="ae484-137">[Azure Databricks][databricks] is a managed Spark cluster where model training and evaluating is performed.</span></span> <span data-ttu-id="ae484-138">您可以在幾分鐘內設定受控 Spark 環境，並可向上和向下[自動縮放][autoscale]，以協助減少與手動縮放叢集相關聯的資源和成本。</span><span class="sxs-lookup"><span data-stu-id="ae484-138">You can set up a managed Spark environment in minutes, and [autoscale][autoscale] up and down to help reduce the resources and costs associated with scaling clusters manually.</span></span> <span data-ttu-id="ae484-139">另一種節省資源的選項是將非作用中的[叢集][clusters]設定為自動終止。</span><span class="sxs-lookup"><span data-stu-id="ae484-139">Another resource-saving option is to configure inactive [clusters][clusters] to terminate automatically.</span></span>

## <a name="considerations"></a><span data-ttu-id="ae484-140">考量</span><span class="sxs-lookup"><span data-stu-id="ae484-140">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="ae484-141">可用性</span><span class="sxs-lookup"><span data-stu-id="ae484-141">Availability</span></span>

<span data-ttu-id="ae484-142">機器學習建置應用程式分成兩個資源元件：用於訓練的資源和用於服務的資源。</span><span class="sxs-lookup"><span data-stu-id="ae484-142">Machine-learning-built apps are split into two resource components: resources for training, and resources for serving.</span></span> <span data-ttu-id="ae484-143">用於訓練的資源通常不需要高可用性，因為即時生產環境要求不會直接存取這些資源。</span><span class="sxs-lookup"><span data-stu-id="ae484-143">Resources required for training generally do not need  high availability, as live production requests do not directly hit these resources.</span></span> <span data-ttu-id="ae484-144">用於服務的資源需要具有高可用性，以便滿足客戶的要求。</span><span class="sxs-lookup"><span data-stu-id="ae484-144">Resources required for serving need to have high availability to serve customer requests.</span></span>

<span data-ttu-id="ae484-145">對用於訓練的資源，全球[多個區域][regions]中都已推出符合虛擬機器[服務等級協定][sla] (SLA) 的 DSVM。</span><span class="sxs-lookup"><span data-stu-id="ae484-145">For training, the DSVM is available in [multiple regions][regions] around the globe and meets the [service level agreement][sla] (SLA) for virtual machines.</span></span> <span data-ttu-id="ae484-146">對用於服務的資源，Azure Kubernetes Service 提供[高可用性][ha]的基礎結構。</span><span class="sxs-lookup"><span data-stu-id="ae484-146">For serving, Azure Kubernetes Service provides a [highly available][ha] infrastructure.</span></span> <span data-ttu-id="ae484-147">代理程式節點也會遵循虛擬機器的 [SLA][sla-aks]。</span><span class="sxs-lookup"><span data-stu-id="ae484-147">Agent nodes also follow the [SLA][sla-aks] for virtual machines.</span></span>

### <a name="scalability"></a><span data-ttu-id="ae484-148">延展性</span><span class="sxs-lookup"><span data-stu-id="ae484-148">Scalability</span></span>

<span data-ttu-id="ae484-149">如果您有大型的資料，則可以調整 DSVM 以縮短訓練時間。</span><span class="sxs-lookup"><span data-stu-id="ae484-149">If you have a large data size, you can scale your DSVM to shorten training time.</span></span> <span data-ttu-id="ae484-150">可以藉由變更 [VM 大小][vm-size]來相應擴充或縮減 VM。</span><span class="sxs-lookup"><span data-stu-id="ae484-150">You can scale a VM up or down by changing the [VM size][vm-size].</span></span> <span data-ttu-id="ae484-151">選擇夠大的記憶體大小以符合記憶體內部資料集和更高的 vCPU 計數，以減少訓練所需的時間。</span><span class="sxs-lookup"><span data-stu-id="ae484-151">Choose a memory size large enough to fit your dataset in-memory and a higher vCPU count in order to decrease the amount of time that training takes.</span></span>

### <a name="security"></a><span data-ttu-id="ae484-152">安全性</span><span class="sxs-lookup"><span data-stu-id="ae484-152">Security</span></span>

<span data-ttu-id="ae484-153">您可以在此案例中使用 Azure Active Directory 對[存取 DSVM][dsvm-id] 的使用者進行驗證，其中包含程式碼、模型和 (記憶體內部) 的資料。</span><span class="sxs-lookup"><span data-stu-id="ae484-153">This scenario can use Azure Active Directory to authenticate users for [access to the DSVM][dsvm-id], which contains your code, models, and (in-memory) data.</span></span> <span data-ttu-id="ae484-154">資料會先儲存在 Azure 儲存體上，然後再載入 DSVM 上，且會使用[儲存體服務加密][storage-security]自動將資料加密。</span><span class="sxs-lookup"><span data-stu-id="ae484-154">Data is stored in Azure Storage prior to being loaded on a DSVM, where it is automatically encrypted using [Storage Service Encryption][storage-security].</span></span> <span data-ttu-id="ae484-155">可以透過 Azure Active Directory 驗證或角色型存取控制來管理權限。</span><span class="sxs-lookup"><span data-stu-id="ae484-155">Permissions can be managed via Azure Active Directory authentication or role-based access control.</span></span>

## <a name="deploy-this-scenario"></a><span data-ttu-id="ae484-156">部署此案例</span><span class="sxs-lookup"><span data-stu-id="ae484-156">Deploy this scenario</span></span>

<span data-ttu-id="ae484-157">**必要條件**：您必須具有現有的 Azure 帳戶。</span><span class="sxs-lookup"><span data-stu-id="ae484-157">**Prerequisites**: You must have an existing Azure account.</span></span> <span data-ttu-id="ae484-158">如果您沒有 Azure 訂用帳戶，請在開始前建立 [[免費帳戶]][free]。</span><span class="sxs-lookup"><span data-stu-id="ae484-158">If you don't have an Azure subscription, create a [free account][free] before you begin.</span></span>

<span data-ttu-id="ae484-159">[Microsoft Recommender 存放庫][github]中提供了此案例的所有程式碼。</span><span class="sxs-lookup"><span data-stu-id="ae484-159">All the code for this scenario is available in the [Microsoft Recommenders repository][github].</span></span>

<span data-ttu-id="ae484-160">請遵循下列步驟來執行 [ALS 快速入門筆記本][notebook]：</span><span class="sxs-lookup"><span data-stu-id="ae484-160">Follow these steps to run the [ALS quickstart notebook][notebook]:</span></span>

1. <span data-ttu-id="ae484-161">從 Azure 入口網站[建立 DSVM][dsvm-ubuntu]。</span><span class="sxs-lookup"><span data-stu-id="ae484-161">[Create a DSVM][dsvm-ubuntu] from the Azure portal.</span></span>

2. <span data-ttu-id="ae484-162">複製 Notebooks 資料夾中的存放庫：</span><span class="sxs-lookup"><span data-stu-id="ae484-162">Clone the repo in the Notebooks folder:</span></span>

    ```shell
    cd notebooks
    git clone https://github.com/Microsoft/Recommenders
    ```

3. <span data-ttu-id="ae484-163">按照 [SETUP.md][setup] 檔案中所述的步驟安裝 conda 相依性。</span><span class="sxs-lookup"><span data-stu-id="ae484-163">Install the conda dependencies following the steps described in the [SETUP.md][setup] file.</span></span>

4. <span data-ttu-id="ae484-164">在瀏覽器中，移至 jupyterlab VM 並瀏覽至 `notebooks/00_quick_start/als_pyspark_movielens.ipynb`。</span><span class="sxs-lookup"><span data-stu-id="ae484-164">In a browser, go to your jupyterlab VM and navigate to `notebooks/00_quick_start/als_pyspark_movielens.ipynb`.</span></span>

5. <span data-ttu-id="ae484-165">執行筆記本。</span><span class="sxs-lookup"><span data-stu-id="ae484-165">Execute the notebook.</span></span>

## <a name="related-resources"></a><span data-ttu-id="ae484-166">相關資源</span><span class="sxs-lookup"><span data-stu-id="ae484-166">Related resources</span></span>

<span data-ttu-id="ae484-167">如需建置和縮放建議程式服務的深入指南，請參閱[在 Azure 上建置即時建議 API][ref-arch]。</span><span class="sxs-lookup"><span data-stu-id="ae484-167">For an in-depth guide to building and scaling a recommender service, see [Build a real-time recommendation API on Azure][ref-arch].</span></span> <span data-ttu-id="ae484-168">如需推薦系統的教學課程和範例，請參閱 [Microsoft Recommender 存放庫][github]。</span><span class="sxs-lookup"><span data-stu-id="ae484-168">For tutorials and examples of recommendation systems, see [Microsoft Recommenders repository][github].</span></span>

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

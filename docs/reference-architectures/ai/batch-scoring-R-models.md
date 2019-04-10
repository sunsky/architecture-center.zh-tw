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
# <a name="batch-scoring-of-r-machine-learning-models-on-azure"></a><span data-ttu-id="64c75-103">在 Azure 批次計分的 R 機器學習服務模型</span><span class="sxs-lookup"><span data-stu-id="64c75-103">Batch scoring of R machine learning models on Azure</span></span>

<span data-ttu-id="64c75-104">此參考架構示範如何執行批次評分，以使用 Azure Batch 的 R 模型。</span><span class="sxs-lookup"><span data-stu-id="64c75-104">This reference architecture shows how to perform batch scoring with R models using Azure Batch.</span></span> <span data-ttu-id="64c75-105">案例根據零售商店銷售預測，但是這個架構可以歸納需要大型 scaler 使用 R 模型的預測產生的任何案例。</span><span class="sxs-lookup"><span data-stu-id="64c75-105">The scenario is based on retail store sales forecasting, but this architecture can be generalized for any scenario requiring the generation of predictions on a large scaler using R models.</span></span> <span data-ttu-id="64c75-106">此架構的參考實作可在 [GitHub][github] 上取得。</span><span class="sxs-lookup"><span data-stu-id="64c75-106">A reference implementation for this architecture is available on [GitHub][github].</span></span>

![架構圖表][0]

<span data-ttu-id="64c75-108">**案例**：連鎖必須預測未來的季銷售的產品。</span><span class="sxs-lookup"><span data-stu-id="64c75-108">**Scenario**: A supermarket chain needs to forecast sales of products over the upcoming quarter.</span></span> <span data-ttu-id="64c75-109">預測可讓公司更佳地管理其供應鏈，並確保它可以符合對在其存放區的每個產品的需求。</span><span class="sxs-lookup"><span data-stu-id="64c75-109">The forecast allows the company to manage its supply chain better and ensure it can meet demand for products at each of its stores.</span></span> <span data-ttu-id="64c75-110">因為新的銷售資料，從上一週就會變成可用，而行銷策略的下一季的產品則設定，該公司將更新其預測每週。</span><span class="sxs-lookup"><span data-stu-id="64c75-110">The company updates its forecasts every week as new sales data from the previous week becomes available and the product marketing strategy for next quarter is set.</span></span> <span data-ttu-id="64c75-111">分量預測會產生以估計的不確定性的個別銷售預測。</span><span class="sxs-lookup"><span data-stu-id="64c75-111">Quantile forecasts are generated to estimate the uncertainty of the individual sales forecasts.</span></span>

<span data-ttu-id="64c75-112">處理程序包含下列步驟：</span><span class="sxs-lookup"><span data-stu-id="64c75-112">Processing involves the following steps:</span></span>

1. <span data-ttu-id="64c75-113">Azure 邏輯應用程式就會觸發每週一次預測的產生程序。</span><span class="sxs-lookup"><span data-stu-id="64c75-113">An Azure Logic App triggers the forecast generation process once per week.</span></span>

1. <span data-ttu-id="64c75-114">邏輯應用程式會啟動 Azure 容器執行個體執行排程器觸發程序的計分工作批次在叢集上的 Docker 容器。</span><span class="sxs-lookup"><span data-stu-id="64c75-114">The logic app starts an Azure Container Instance running the scheduler Docker container, which triggers the scoring jobs on the Batch cluster.</span></span>

1. <span data-ttu-id="64c75-115">批次叢集中的節點之間平行執行評分的工作。</span><span class="sxs-lookup"><span data-stu-id="64c75-115">Scoring jobs run in parallel across the nodes of the Batch cluster.</span></span> <span data-ttu-id="64c75-116">每個節點：</span><span class="sxs-lookup"><span data-stu-id="64c75-116">Each node:</span></span>

    1. <span data-ttu-id="64c75-117">從 Docker Hub 的 Docker 映像提取背景工作角色，並啟動容器。</span><span class="sxs-lookup"><span data-stu-id="64c75-117">Pulls the worker Docker image from Docker Hub and starts a container.</span></span>

    1. <span data-ttu-id="64c75-118">讀取輸入的資料，並預先定型的 R 模型，從 Azure Blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="64c75-118">Reads input data and pre-trained R models from Azure Blob storage.</span></span>

    1. <span data-ttu-id="64c75-119">分數的資料來產生預測。</span><span class="sxs-lookup"><span data-stu-id="64c75-119">Scores the data to produce the forecasts.</span></span>

    1. <span data-ttu-id="64c75-120">將預測的結果寫入至 blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="64c75-120">Writes the forecast results to blob storage.</span></span>

<span data-ttu-id="64c75-121">下圖顯示一個存放區中的四個產品 (Sku) 的預測的銷售量。</span><span class="sxs-lookup"><span data-stu-id="64c75-121">The figure below shows the forecasted sales for four products (SKUs) in one store.</span></span> <span data-ttu-id="64c75-122">黑色線條是銷售的歷程記錄、 虛線是預測的中間值 (q50)、 粉紅色的頻外代表第 25 和 seventy 第五個百分位數，而藍色的頻外代表第五個和九十第五個百分位數。</span><span class="sxs-lookup"><span data-stu-id="64c75-122">The black line is the sales history, the dashed line is the median (q50) forecast, the pink band represents the twenty-fifth and seventy-fifth percentiles, and the blue band represents the fifth and ninety-fifth percentiles.</span></span>

![銷售預測][1]

## <a name="architecture"></a><span data-ttu-id="64c75-124">架構</span><span class="sxs-lookup"><span data-stu-id="64c75-124">Architecture</span></span>

<span data-ttu-id="64c75-125">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="64c75-125">This architecture consists of the following components.</span></span>

<span data-ttu-id="64c75-126">[Azure Batch] [ batch]用來以平行方式執行預測的產生作業，在叢集上的虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="64c75-126">[Azure Batch][batch] is used to run forecast generation jobs in parallel on a cluster of virtual machines.</span></span> <span data-ttu-id="64c75-127">預測可使用預先定型的機器學習服務 R Azure Batch 中實作的模型可以自動調整規模的作業提交至叢集數目為基礎的 Vm 數目。</span><span class="sxs-lookup"><span data-stu-id="64c75-127">Predictions are made using pre-trained machine learning models implemented in R. Azure Batch can automatically scale the number of VMs based on the number of jobs submitted to the cluster.</span></span> <span data-ttu-id="64c75-128">每個節點，來評分資料，並產生預測的 Docker 容器內執行 R 指令碼。</span><span class="sxs-lookup"><span data-stu-id="64c75-128">On each node, an R script runs within a Docker container to score data and generate forecasts.</span></span>

<span data-ttu-id="64c75-129">[Azure Blob 儲存體][ blob]用來儲存輸入的資料、 預先定型的機器學習服務模型和預測的結果。</span><span class="sxs-lookup"><span data-stu-id="64c75-129">[Azure Blob Storage][blob] is used to store the input data, the pre-trained machine learning models, and the forecast results.</span></span> <span data-ttu-id="64c75-130">它提供非常符合成本效益的儲存體，需要這個工作負載的效能。</span><span class="sxs-lookup"><span data-stu-id="64c75-130">It delivers very cost-effective storage for the performance that this workload requires.</span></span>

<span data-ttu-id="64c75-131">[Azure Container Instances] [ aci]隨需提供無伺服器計算。</span><span class="sxs-lookup"><span data-stu-id="64c75-131">[Azure Container Instances][aci] provide serverless compute on demand.</span></span> <span data-ttu-id="64c75-132">在此情況下，觸發程序產生之預測的批次作業的排程部署容器執行個體。</span><span class="sxs-lookup"><span data-stu-id="64c75-132">In this case, a container instance is deployed on a schedule to trigger the Batch jobs that generate the forecasts.</span></span> <span data-ttu-id="64c75-133">批次作業會觸發從 R 指令碼使用[doAzureParallel][doAzureParallel]封裝。</span><span class="sxs-lookup"><span data-stu-id="64c75-133">The Batch jobs are triggered from an R script using the [doAzureParallel][doAzureParallel] package.</span></span> <span data-ttu-id="64c75-134">容器執行個體會自動關閉作業完成之後。</span><span class="sxs-lookup"><span data-stu-id="64c75-134">The container instance automatically shuts down once the jobs have finished.</span></span>

<span data-ttu-id="64c75-135">[Azure Logic Apps] [ logic-apps]部署容器執行個體上的排程來觸發整個工作流程。</span><span class="sxs-lookup"><span data-stu-id="64c75-135">[Azure Logic Apps][logic-apps] trigger the entire workflow by deploying the container instances on a schedule.</span></span> <span data-ttu-id="64c75-136">在 Logic Apps 中的 Azure Container Instances 連接器可讓部署觸發程序事件範圍的執行個體。</span><span class="sxs-lookup"><span data-stu-id="64c75-136">An Azure Container Instances connector in Logic Apps allows an instance to be deployed upon a range of trigger events.</span></span>

## <a name="performance-considerations"></a><span data-ttu-id="64c75-137">效能考量</span><span class="sxs-lookup"><span data-stu-id="64c75-137">Performance considerations</span></span>

### <a name="containerized-deployment"></a><span data-ttu-id="64c75-138">容器化的部署</span><span class="sxs-lookup"><span data-stu-id="64c75-138">Containerized deployment</span></span>

<span data-ttu-id="64c75-139">使用此架構中，所有的 R 指令碼執行內[Docker](https://www.docker.com/)容器。</span><span class="sxs-lookup"><span data-stu-id="64c75-139">With this architecture, all R scripts run within [Docker](https://www.docker.com/) containers.</span></span> <span data-ttu-id="64c75-140">這可確保指令碼執行在一致的環境中，使用相同的 R 版本和套件的版本中，每次。</span><span class="sxs-lookup"><span data-stu-id="64c75-140">This ensures that the scripts run in a consistent environment, with the same R version and packages versions, every time.</span></span> <span data-ttu-id="64c75-141">不同的 Docker 映像用於 「 排程器 」 和 「 背景工作的容器，因為每個有一組不同的 R 封裝相依性。</span><span class="sxs-lookup"><span data-stu-id="64c75-141">Separate Docker images are used for the scheduler and worker containers, because each has a different set of R package dependencies.</span></span>

<span data-ttu-id="64c75-142">Azure Container Instances 提供無伺服器環境來執行排程器的容器。</span><span class="sxs-lookup"><span data-stu-id="64c75-142">Azure Container Instances provides a serverless environment to run the scheduler container.</span></span> <span data-ttu-id="64c75-143">排程器容器會執行 R 指令碼，在 Azure Batch 的叢集上執行的個別評分工作觸發程序。</span><span class="sxs-lookup"><span data-stu-id="64c75-143">The scheduler container runs an R script that triggers the individual scoring jobs running on an Azure Batch cluster.</span></span>

<span data-ttu-id="64c75-144">批次叢集中的每個節點會執行背景工作角色容器，它會執行評分指令碼。</span><span class="sxs-lookup"><span data-stu-id="64c75-144">Each node of the Batch cluster runs the worker container, which executes the scoring script.</span></span>

### <a name="parallelizing-the-workload"></a><span data-ttu-id="64c75-145">平行處理工作負載</span><span class="sxs-lookup"><span data-stu-id="64c75-145">Parallelizing the workload</span></span>

<span data-ttu-id="64c75-146">當批次計分的 R 模型的資料，請考慮如何平行處理工作負載。</span><span class="sxs-lookup"><span data-stu-id="64c75-146">When batch scoring data with R models, consider how to parallelize the workload.</span></span> <span data-ttu-id="64c75-147">輸入的資料必須以某種方式分割，以便評分作業可以分散到叢集節點。</span><span class="sxs-lookup"><span data-stu-id="64c75-147">The input data must be partitioned somehow so that the scoring operation can be distributed  across the cluster nodes.</span></span> <span data-ttu-id="64c75-148">請嘗試不同的方法，來探索散發您的工作負載的最佳選擇。</span><span class="sxs-lookup"><span data-stu-id="64c75-148">Try different approaches to discover the best choice for distributing your workload.</span></span> <span data-ttu-id="64c75-149">以案例的基礎，請考慮下列各項：</span><span class="sxs-lookup"><span data-stu-id="64c75-149">On a case-by-case basis, consider the following:</span></span>

- <span data-ttu-id="64c75-150">可以載入及處理記憶體中的單一節點多少資料。</span><span class="sxs-lookup"><span data-stu-id="64c75-150">How much data can be loaded and processed in the memory of a single node.</span></span>
- <span data-ttu-id="64c75-151">啟動每個批次作業的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="64c75-151">The overhead of starting each batch job.</span></span>
- <span data-ttu-id="64c75-152">載入的 R 模型的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="64c75-152">The overhead of loading the R models.</span></span>

<span data-ttu-id="64c75-153">在此範例使用案例中，模型物件很大，而且只要幾秒產生適用於個別產品的預測。</span><span class="sxs-lookup"><span data-stu-id="64c75-153">In the scenario used for this example, the model objects are large, and it takes only a few seconds to generate a forecast for individual products.</span></span> <span data-ttu-id="64c75-154">基於這個理由，您可以分組產品並執行每個節點的單一批次作業。</span><span class="sxs-lookup"><span data-stu-id="64c75-154">For this reason, you can group the products and execute a single Batch job per node.</span></span> <span data-ttu-id="64c75-155">在迴圈內的每個工作會循序產生產品的預測。</span><span class="sxs-lookup"><span data-stu-id="64c75-155">A loop within each job generates forecasts for the products sequentially.</span></span> <span data-ttu-id="64c75-156">這個方法其實是最有效率的方式，來平行處理這個特定的工作負載。</span><span class="sxs-lookup"><span data-stu-id="64c75-156">This method turns out to be the most efficient way to parallelize this particular workload.</span></span> <span data-ttu-id="64c75-157">它可避免啟動許多較小的批次作業和重複載入 R 模型的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="64c75-157">It avoids the overhead of starting many smaller Batch jobs and repeatedly loading the R models.</span></span>

<span data-ttu-id="64c75-158">替代方法是以觸發每項產品的一個批次作業。</span><span class="sxs-lookup"><span data-stu-id="64c75-158">An alternative approach is to trigger one Batch job per product.</span></span> <span data-ttu-id="64c75-159">Azure Batch 自動形成工作的佇列，並將其提交至叢集上執行，因為節點變成可用。</span><span class="sxs-lookup"><span data-stu-id="64c75-159">Azure Batch automatically forms a queue of jobs and submits them to be executed on the cluster as nodes become available.</span></span> <span data-ttu-id="64c75-160">使用[自動調整][ autoscale]調整的作業數目根據叢集中的節點數目。</span><span class="sxs-lookup"><span data-stu-id="64c75-160">Use [automatic scaling][autoscale] to adjust the number of nodes in the cluster depending on the number of jobs.</span></span> <span data-ttu-id="64c75-161">如果要花較長的時間才能完成每個計分的作業，對齊的啟動工作並重新載入模型物件的額外負荷，這種方法會比較合適。</span><span class="sxs-lookup"><span data-stu-id="64c75-161">This approach makes more sense if it takes a relatively long time to complete each scoring operation, justifying the overhead of starting the jobs and reloading the model objects.</span></span> <span data-ttu-id="64c75-162">這種方法也會更容易實作，並可讓您使用自動調整的彈性 — 很重要的考量，如果事先不知道的總工作負載的大小。</span><span class="sxs-lookup"><span data-stu-id="64c75-162">This approach is also simpler to implement and gives you the flexibility to use automatic scaling—an important consideration if the size of the total workload is not known in advance.</span></span>

## <a name="monitoring-and-logging-considerations"></a><span data-ttu-id="64c75-163">監視和記錄的考量</span><span class="sxs-lookup"><span data-stu-id="64c75-163">Monitoring and logging considerations</span></span>

### <a name="monitoring-azure-batch-jobs"></a><span data-ttu-id="64c75-164">監視 Azure Batch 作業</span><span class="sxs-lookup"><span data-stu-id="64c75-164">Monitoring Azure Batch jobs</span></span>

<span data-ttu-id="64c75-165">監視並終止批次作業，從**作業**窗格中的 Azure 入口網站中的 Batch 帳戶。</span><span class="sxs-lookup"><span data-stu-id="64c75-165">Monitor and terminate Batch jobs from the **Jobs** pane of the Batch account in the Azure portal.</span></span> <span data-ttu-id="64c75-166">監視批次的叢集，包括個別的節點的狀態從**集區**窗格。</span><span class="sxs-lookup"><span data-stu-id="64c75-166">Monitor the batch cluster, including the state of individual nodes, from the **Pools** pane.</span></span>

### <a name="logging-with-doazureparallel"></a><span data-ttu-id="64c75-167">DoAzureParallel 記錄</span><span class="sxs-lookup"><span data-stu-id="64c75-167">Logging with doAzureParallel</span></span>

<span data-ttu-id="64c75-168">DoAzureParallel 套件會自動收集所有 stdout/stderr 中每個工作提交 Azure Batch 上的記錄的檔。</span><span class="sxs-lookup"><span data-stu-id="64c75-168">The doAzureParallel package automatically collects logs of all stdout/stderr for every job submitted on Azure Batch.</span></span> <span data-ttu-id="64c75-169">這些可於安裝時建立的儲存體帳戶中。</span><span class="sxs-lookup"><span data-stu-id="64c75-169">These can be found in the storage account created at setup.</span></span> <span data-ttu-id="64c75-170">若要檢視它們，請使用儲存體瀏覽工具這類[Azure 儲存體總管][ storage-explorer]或 Azure 入口網站。</span><span class="sxs-lookup"><span data-stu-id="64c75-170">To view them, use a storage navigation tool such as [Azure Storage Explorer][storage-explorer] or Azure portal.</span></span>

<span data-ttu-id="64c75-171">若要在開發期間，快速偵錯的批次作業，列印在本機 R 工作階段使用的記錄檔[getJobFiles][getJobFiles] doAzureParallel 函式。</span><span class="sxs-lookup"><span data-stu-id="64c75-171">To quickly debug Batch jobs during development, print logs in your local R session using the [getJobFiles][getJobFiles] function of doAzureParallel.</span></span>

## <a name="cost-considerations"></a><span data-ttu-id="64c75-172">成本考量</span><span class="sxs-lookup"><span data-stu-id="64c75-172">Cost considerations</span></span>

<span data-ttu-id="64c75-173">在此參考架構中使用的計算資源是成本最高的元件。</span><span class="sxs-lookup"><span data-stu-id="64c75-173">The compute resources used in this reference architecture are the most costly components.</span></span> <span data-ttu-id="64c75-174">此案例中，每當作業會觸發並關閉作業完成之後，就會建立一個固定大小的叢集。</span><span class="sxs-lookup"><span data-stu-id="64c75-174">For this scenario, a cluster of fixed size is created whenever the job is triggered and then shut down after the job has completed.</span></span> <span data-ttu-id="64c75-175">只有當啟動、 執行，或正在關閉叢集節點時，即會產生費用。</span><span class="sxs-lookup"><span data-stu-id="64c75-175">Cost is incurred only while the cluster nodes are starting, running, or shutting down.</span></span> <span data-ttu-id="64c75-176">這個方法很適合案例，其中要產生預測所需的計算資源保持相當穩定工作作業。</span><span class="sxs-lookup"><span data-stu-id="64c75-176">This approach is suitable for a scenario where the compute resources required to generate the forecasts remain relatively constant from job to job.</span></span>

<span data-ttu-id="64c75-177">在完成工作所需的計算數量無法事先知道的情況下，可能更適合使用自動調整。</span><span class="sxs-lookup"><span data-stu-id="64c75-177">In scenarios where the amount of compute required to complete the job is not known in advance, it may be more suitable to use automatic scaling.</span></span> <span data-ttu-id="64c75-178">使用此方法時，叢集的大小被相應增加或減少作業的大小而定。</span><span class="sxs-lookup"><span data-stu-id="64c75-178">With this approach, the size of the cluster is scaled up or down depending on the size of the job.</span></span> <span data-ttu-id="64c75-179">Azure Batch 支援一組定義叢集中使用時，您可以設定自動調整公式[doAzureParallel][doAzureParallel] API。</span><span class="sxs-lookup"><span data-stu-id="64c75-179">Azure Batch supports a range of auto-scale formulae which you can set when defining the cluster using the [doAzureParallel][doAzureParallel] API.</span></span>

<span data-ttu-id="64c75-180">某些情況下，作業之間的時間可能太短而無法關閉，並啟動叢集。</span><span class="sxs-lookup"><span data-stu-id="64c75-180">For some scenarios, the time between jobs may be too short to shut down and start up the cluster.</span></span> <span data-ttu-id="64c75-181">在這些情況下，請視執行作業之間的叢集。</span><span class="sxs-lookup"><span data-stu-id="64c75-181">In these cases, keep the cluster running between jobs if appropriate.</span></span>

<span data-ttu-id="64c75-182">Azure 批次和 doAzureParallel 支援低優先順序的 Vm 使用。</span><span class="sxs-lookup"><span data-stu-id="64c75-182">Azure Batch and doAzureParallel support the use of low-priority VMs.</span></span> <span data-ttu-id="64c75-183">這些 Vm 會隨附一個可觀的折扣，但由其他較高優先權工作負載所適用的風險。</span><span class="sxs-lookup"><span data-stu-id="64c75-183">These VMs come with a significant discount but risk being appropriated by other higher priority workloads.</span></span> <span data-ttu-id="64c75-184">這些 Vm 會因此不建議使用重要生產工作負載。</span><span class="sxs-lookup"><span data-stu-id="64c75-184">The use of these VMs are therefore not recommended for critical production workloads.</span></span> <span data-ttu-id="64c75-185">不過，它們是非常適用於實驗或開發工作負載。</span><span class="sxs-lookup"><span data-stu-id="64c75-185">However, they are very useful for experimental or development workloads.</span></span>

## <a name="deployment"></a><span data-ttu-id="64c75-186">部署</span><span class="sxs-lookup"><span data-stu-id="64c75-186">Deployment</span></span>

<span data-ttu-id="64c75-187">若要部署此參考架構，請依照下列所述的步驟[GitHub][github]存放庫。</span><span class="sxs-lookup"><span data-stu-id="64c75-187">To deploy this reference architecture, follow the steps described in the [GitHub][github] repo.</span></span>


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
---
title: Azure 上的 Python 模型批次評分
description: 使用 Azure Machine Learning 服務組建可根據排程對模型進行批次評分的可調整解決方案。
author: njray
ms.date: 01/30/2019
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai, AI
ms.openlocfilehash: b7607984bcf2c4bd046421aeb6e9d52dd8e7c18e
ms.sourcegitcommit: 1a3cc91530d56731029ea091db1f15d41ac056af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887738"
---
# <a name="batch-scoring-of-python-machine-learning-models-on-azure"></a><span data-ttu-id="bc9c7-103">在 Azure 上的 Python 機器學習服務的批次評分模型</span><span class="sxs-lookup"><span data-stu-id="bc9c7-103">Batch scoring of Python machine learning models on Azure</span></span>

<span data-ttu-id="bc9c7-104">此參考架構會示範如何使用 Azure Machine Learning 服務組建可根據排程對許多模型進行批次評分的可調整解決方案。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-104">This reference architecture shows how to build a scalable solution for batch scoring many models on a schedule in parallel using Azure Machine Learning Service.</span></span> <span data-ttu-id="bc9c7-105">此方案可作為範本，並可廣泛用於不同的問題。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-105">The solution can be used as a template and can generalize to different problems.</span></span>

<span data-ttu-id="bc9c7-106">此架構的參考實作可在 [GitHub][github] 上取得。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-106">A reference implementation for this architecture is available on [GitHub][github].</span></span>

![Azure 上的 Python 模型批次評分](./_images/batch-scoring-python.png)

<span data-ttu-id="bc9c7-108">**案例**：此解決方案可監視 IoT 設定中大量裝置的作業，這其中的每個裝置都會持續傳送感應器讀數。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-108">**Scenario**: This solution monitors the operation of a large number of devices in an IoT setting where each device sends sensor readings continuously.</span></span> <span data-ttu-id="bc9c7-109">我們假設每個裝置已與預先定型的異常偵測模型相關聯，而這些模型可用來預測一系列的量值 (在預先定義的時間間隔內彙總) 是否會對應到異常狀況。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-109">Each device is assumed to be associated with pretrained anomaly detection models that need to be used to predict whether a series of measurements, that are aggregated over a predefined time interval, correspond to an anomaly or not.</span></span> <span data-ttu-id="bc9c7-110">在真實案例中，這可能是需要先進行篩選和彙總的感應器讀數資料流，然後才能用於訓練和即時評分。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-110">In real-world scenarios, this could be a stream of sensor readings that need to be filtered and aggregated before being used in training or real-time scoring.</span></span> <span data-ttu-id="bc9c7-111">為了方便起見，此解決方案會在執行評分作業時使用相同的資料檔案。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-111">For simplicity, this solution uses the same data file when executing scoring jobs.</span></span>

<span data-ttu-id="bc9c7-112">此參考架構是針對根據排程觸發的工作負載而設計的。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-112">This reference architecture is designed for workloads that are triggered on a schedule.</span></span> <span data-ttu-id="bc9c7-113">處理程序包含下列步驟：</span><span class="sxs-lookup"><span data-stu-id="bc9c7-113">Processing involves the following steps:</span></span>
1.  <span data-ttu-id="bc9c7-114">將擷取的感應器讀數傳送至 Azure 事件中樞。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-114">Send sensor readings for ingestion to Azure Event Hubs.</span></span>
2.  <span data-ttu-id="bc9c7-115">執行資料流處理並儲存未經處理資料。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-115">Perform stream processing and store the raw data.</span></span>
3.  <span data-ttu-id="bc9c7-116">將資料傳送至已準備好開始工作的 Machine Learning 叢集。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-116">Send the data to a Machine Learning cluster that is ready to start taking work.</span></span> <span data-ttu-id="bc9c7-117">叢集中的每個節點都會針對特定感應器執行評分作業。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-117">Each node in the cluster runs a scoring job for a specific sensor.</span></span> 
4.  <span data-ttu-id="bc9c7-118">執行評分管線，這會使用 Machine Learning Python 指令碼平行執行評分作業。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-118">Execute the scoring pipeline, which runs the scoring jobs in parallel using Machine Learning Python scripts.</span></span> <span data-ttu-id="bc9c7-119">系統會建立、發佈管線，並將其排定在預先定義的時間間隔上執行。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-119">The pipeline is created, published, and scheduled to run on a predefined interval of time.</span></span>
5.  <span data-ttu-id="bc9c7-120">產生預測並將它們儲存在 Blob 儲存體中，供後續使用。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-120">Generate predictions and store them in Blob storage for later consumption.</span></span>

## <a name="architecture"></a><span data-ttu-id="bc9c7-121">架構</span><span class="sxs-lookup"><span data-stu-id="bc9c7-121">Architecture</span></span>

<span data-ttu-id="bc9c7-122">此架構由下列元件組成：</span><span class="sxs-lookup"><span data-stu-id="bc9c7-122">This architecture consists of the following components:</span></span>

<span data-ttu-id="bc9c7-123">[Azure 事件中樞][event-hubs]。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-123">[Azure Event Hubs][event-hubs].</span></span> <span data-ttu-id="bc9c7-124">此訊息擷取服務每秒可內嵌數百萬個事件訊息。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-124">This message ingestion service can ingest millions of event messages per second.</span></span> <span data-ttu-id="bc9c7-125">在此架構中，感應器會將資料流傳送到事件中樞。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-125">In this architecture, sensors send a stream of data to the event hub.</span></span>

<span data-ttu-id="bc9c7-126">[Azure 串流分析][stream-analytics]。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-126">[Azure Stream Analytics][stream-analytics].</span></span> <span data-ttu-id="bc9c7-127">事件處理引擎。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-127">An event-processing engine.</span></span> <span data-ttu-id="bc9c7-128">串流分析作業會從事件中樞讀取資料流，並執行串流處理。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-128">A Stream Analytics job reads the data streams from the event hub and performs stream processing.</span></span>

<span data-ttu-id="bc9c7-129">[Azure SQL Database][sql-database]。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-129">[Azure SQL Database][sql-database].</span></span> <span data-ttu-id="bc9c7-130">來自感應器讀數的資料會載入至 SQL Database。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-130">Data from the sensor readings is loaded into SQL Database.</span></span> <span data-ttu-id="bc9c7-131">SQL 是儲存已處理串流資料 (即表格式和結構化資料) 的熟悉方式，但可以使用其他資料存放區。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-131">SQL is a familiar way to store the processed, streamed data (which is tabular and structured), but other data stores can be used.</span></span>

<span data-ttu-id="bc9c7-132">[Azure Machine Learning 服務][amls]。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-132">[Azure Machine Learning Service][amls].</span></span> <span data-ttu-id="bc9c7-133">Machine Learning 是雲端服務，用於訓練、評分、部署和管理大規模的機器學習模型。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-133">Machine Learning is a cloud service for training, scoring, deploying, and managing machine learning models at scale.</span></span> <span data-ttu-id="bc9c7-134">在批次評分的內容中，Machine Learning 會依需求使用自動調整選項來建立虛擬機器，而叢集中的每個節點都會執行特定感應器的評分作業。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-134">In the context of batch scoring, Machine Learning creates a cluster of virtual machines on demand with an automatic scaling option, where each node in the cluster runs a scoring job for a specific sensor.</span></span> <span data-ttu-id="bc9c7-135">評分作業是以 Python 指令碼步驟形式平行執行，而這些步驟由 Machine Learning 置入佇列和管理。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-135">The scoring jobs are executed in parallel as Python-script steps that are queued and managed by Machine Learning.</span></span> <span data-ttu-id="bc9c7-136">這些步驟屬於系統建立、發佈，並排定在預先定義時間間隔執行的 Machine Learning 管線。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-136">These steps are part of a Machine Learning pipeline that is created, published, and scheduled to run on a predefined interval of time.</span></span>

<span data-ttu-id="bc9c7-137">[Azure Blob 儲存體][storage]。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-137">[Azure Blob Storage][storage].</span></span> <span data-ttu-id="bc9c7-138">Blob 容器會用來儲存預先定型的模型、資料和輸出預測。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-138">Blob containers are used to store the pretrained models, the data, and the output predictions.</span></span> <span data-ttu-id="bc9c7-139">模型會上傳至 [01_create_resources.ipynb][create-resources] 筆記本中的 Blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-139">The models are uploaded to Blob storage in the [01_create_resources.ipynb][create-resources] notebook.</span></span> <span data-ttu-id="bc9c7-140">用來訓練這些[單一類別 SVM][one-class-svm] 模型的資料，代表著不同裝置上不同感應器的值。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-140">These [one-class SVM][one-class-svm] models are trained on data that represents values of different sensors for different devices.</span></span> <span data-ttu-id="bc9c7-141">此解決方案會假設資料值是在固定時間間隔上彙總的。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-141">This solution assumes that the data values are aggregated over a fixed interval of time.</span></span>

<span data-ttu-id="bc9c7-142">[Azure Container Registry][acr]。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-142">[Azure Container Registry][acr].</span></span> <span data-ttu-id="bc9c7-143">評分 Python [指令碼][pyscript] 會在建立於每個叢集節點上的 Docker 容器中執行，並在其中讀取相關的感應器資料、產生預測，然後將這些預測儲存在 Blob 儲存體中。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-143">The scoring Python [script][pyscript] runs in Docker containers that are created on each node of the cluster, where it reads the relevant sensor data, generates predictions and stores them in Blob storage.</span></span>

## <a name="performance-considerations"></a><span data-ttu-id="bc9c7-144">效能考量</span><span class="sxs-lookup"><span data-stu-id="bc9c7-144">Performance considerations</span></span>

<span data-ttu-id="bc9c7-145">對於標準 Python 模型，普遍認為 CPU 足以處理工作負載。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-145">For standard Python models, it's generally accepted that CPUs are sufficient to handle the workload.</span></span> <span data-ttu-id="bc9c7-146">此架構會使用 CPU。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-146">This architecture uses CPUs.</span></span> <span data-ttu-id="bc9c7-147">不過，為了[深度學習工作負載][deep]，Gpu 通常勝過相當多的 Cpu &mdash; Cpu 相當叢集通常需要取得相當的效能。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-147">However, for [deep learning workloads][deep], GPUs generally outperform CPUs by a considerable amount &mdash; a sizeable cluster of CPUs is usually needed to get comparable performance.</span></span>

### <a name="parallelizing-across-vms-versus-cores"></a><span data-ttu-id="bc9c7-148">與核心的平行處理跨 Vm</span><span class="sxs-lookup"><span data-stu-id="bc9c7-148">Parallelizing across VMs versus cores</span></span>

<span data-ttu-id="bc9c7-149">若要以批次模式執行許多模型的評分程序，這些作業必須在各 VM 上平行進行。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-149">When running scoring processes of many models in batch mode, the jobs need to be parallelized across VMs.</span></span> <span data-ttu-id="bc9c7-150">這可能會有兩種方法：</span><span class="sxs-lookup"><span data-stu-id="bc9c7-150">Two approaches are possible:</span></span>

* <span data-ttu-id="bc9c7-151">使用低成本 VM 建立更大的叢集。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-151">Create a larger cluster using low-cost VMs.</span></span>

* <span data-ttu-id="bc9c7-152">使用高效能 VM 建立較小的叢集，而每個 VM 上有多個可用核心。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-152">Create a smaller cluster using high performing VMs with more cores available on each.</span></span>

<span data-ttu-id="bc9c7-153">一般情況下，標準 Python 模型的評分需求與深度學習模型的評分需求不同，小型叢集應能夠有效率地處理大量已排入佇列的模型。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-153">In general, scoring of standard Python models is not as demanding as scoring of deep learning models, and a small cluster should be able to handle a large number of queued models efficiently.</span></span> <span data-ttu-id="bc9c7-154">您可以在資料集大小增加時增加叢集節點數目。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-154">You can increase the number of cluster nodes as the dataset sizes increase.</span></span>

<span data-ttu-id="bc9c7-155">為了方便起見，在此情節中，某個評分工作會在單一 Machine Learning 管線步驟內提交。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-155">For convenience in this scenario, one scoring task is submitted within a single Machine Learning pipeline step.</span></span> <span data-ttu-id="bc9c7-156">但是，在相同管線步驟內評分多個資料區塊其實會更有效率。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-156">However, it can be more efficient to score multiple data chunks within the same pipeline step.</span></span> <span data-ttu-id="bc9c7-157">在這些情況下，可撰寫自訂程式碼在多個資料集中進行讀取，以及在執行單一步驟期間對這些資料集執行評分指令碼。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-157">In those cases, write custom code to read in multiple datasets and execute the scoring script for those during a single-step execution.</span></span>

## <a name="management-considerations"></a><span data-ttu-id="bc9c7-158">管理考量</span><span class="sxs-lookup"><span data-stu-id="bc9c7-158">Management considerations</span></span>

- <span data-ttu-id="bc9c7-159">**監視作業**。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-159">**Monitor jobs**.</span></span> <span data-ttu-id="bc9c7-160">監視執行中作業的進度相當重要，但要監視叢集上的作用中節點可能很困難。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-160">It's important to monitor the progress of running jobs, but it can be a challenge to monitor across a cluster of active nodes.</span></span> <span data-ttu-id="bc9c7-161">若要檢查叢集中節點的狀態，請使用 [Azure 入口網站][ portal]來管理[機器學習工作區][ml-workspace]。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-161">To inspect the state of the nodes in the cluster, use the [Azure Portal][portal] to manage the [machine learning workspace][ml-workspace].</span></span> <span data-ttu-id="bc9c7-162">如果節點狀態是非使用中或作業已失敗，則錯誤記錄會儲存在 Blob 儲存體中，您也可在 Pipelines 區段中存取錯誤記錄。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-162">If a node is inactive or a job has failed, the error logs are saved to blob storage, and are also accessible in the Pipelines section.</span></span> <span data-ttu-id="bc9c7-163">如需更多監視功能，您可以將記錄連線到 [Application Insights][app-insights]，或執行不同處理程序來對叢集和其作業狀態進行輪詢。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-163">For richer monitoring, connect logs to [Application Insights][app-insights], or run separate processes to poll for the state of the cluster and its jobs.</span></span>
-   <span data-ttu-id="bc9c7-164">**記錄**。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-164">**Logging**.</span></span> <span data-ttu-id="bc9c7-165">Machine Learning 服務會將所有 stdout/stderr 記錄至相關聯的 Azure 儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-165">Machine Learning Service logs all stdout/stderr to the associated Azure Storage account.</span></span> <span data-ttu-id="bc9c7-166">若要輕鬆檢視記錄檔，請使用 [Azure 儲存體總管][explorer]之類的儲存體瀏覽工具。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-166">To easily view the log files, use a storage navigation tool such as [Azure Storage Explorer][explorer].</span></span>

## <a name="cost-considerations"></a><span data-ttu-id="bc9c7-167">成本考量</span><span class="sxs-lookup"><span data-stu-id="bc9c7-167">Cost considerations</span></span>

<span data-ttu-id="bc9c7-168">此參考架構中成本最高的元件是計算資源。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-168">The most expensive components used in this reference architecture are the compute resources.</span></span> <span data-ttu-id="bc9c7-169">計算叢集大小可根據佇列中的作業來相應增加和減少。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-169">The compute cluster size scales up and down depending on the jobs in the queue.</span></span> <span data-ttu-id="bc9c7-170">修改計算的佈建設定，透過 Python SDK 以程式設計方式啟用自動調整。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-170">Enable automatic scaling programmatically through the Python SDK by modifying the compute’s provisioning configuration.</span></span> <span data-ttu-id="bc9c7-171">或者，使用 [Azure CLI][cli] 來設定叢集的自動調整參數。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-171">Or use the [Azure CLI][cli] to set the automatic scaling parameters of the cluster.</span></span>

<span data-ttu-id="bc9c7-172">對於不需要立即處理的工作，可設定自動調整公式，如此一來，預設狀態 (最小值) 就是零個節點的叢集。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-172">For work that doesn't require immediate processing, configure the automatic scaling formula so the default state (minimum) is a cluster of zero nodes.</span></span> <span data-ttu-id="bc9c7-173">使用此設定時，叢集一開始會有零個節點，並只在偵測到佇列中有作業時，才會相應增加。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-173">With this configuration, the cluster starts with zero nodes and only scales up when it detects jobs in the queue.</span></span> <span data-ttu-id="bc9c7-174">如果批次評分程序一天只會發生幾次 (或更少)，則此設定可省下大量成本。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-174">If the batch scoring process happens only a few times a day or less, this setting enables significant cost savings.</span></span>

<span data-ttu-id="bc9c7-175">自動調整可能不適合間距太近的批次作業。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-175">Automatic scaling may not be appropriate for batch jobs that happen too close to each other.</span></span> <span data-ttu-id="bc9c7-176">啟動及關閉叢集所花費的時間也會產生成本，因此如果批次工作負載會在前一個作業結束後的幾分鐘內啟動，那麼讓叢集在作業之間持續運作可能比較符合成本效益。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-176">The time that it takes for a cluster to spin up and spin down also incurs a cost, so if a batch workload begins only a few minutes after the previous job ends, it might be more cost effective to keep the cluster running between jobs.</span></span> <span data-ttu-id="bc9c7-177">這取決於評分程序是排定為高頻率執行 (例如每小時) 或低頻率執行 (例如一個月一次)。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-177">That depends on whether scoring processes are scheduled to run at a high frequency (every hour, for example), or less frequently (once a month, for example).</span></span>


## <a name="deployment"></a><span data-ttu-id="bc9c7-178">部署</span><span class="sxs-lookup"><span data-stu-id="bc9c7-178">Deployment</span></span>

<span data-ttu-id="bc9c7-179">若要部署此參考架構，請遵循 [GitHub 存放庫][github]中所述的步驟。</span><span class="sxs-lookup"><span data-stu-id="bc9c7-179">To deploy this reference architecture, follow the steps described in the [GitHub repo][github].</span></span>

[acr]: /azure/container-registry/container-registry-intro
[ai]: /azure/application-insights/app-insights-overview
[aml-compute]: /azure/machine-learning/service/how-to-set-up-training-targets#amlcompute
[amls]: /azure/machine-learning/service/overview-what-is-azure-ml
[automatic-scaling]: /azure/batch/batch-automatic-scaling
[azure-files]: /azure/storage/files/storage-files-introduction
[cli]: /cli/azure
[create-resources]: https://github.com/Microsoft/AMLBatchScoringPipeline/blob/master/01_create_resources.ipynb
[deep]: /azure/architecture/reference-architectures/ai/batch-scoring-deep-learning
[event-hubs]: /azure/event-hubs/event-hubs-geo-dr
[explorer]: https://azure.microsoft.com/en-us/features/storage-explorer/
[github]: https://github.com/Microsoft/AMLBatchScoringPipeline
[one-class-svm]: http://scikit-learn.org/stable/modules/generated/sklearn.svm.OneClassSVM.html
[portal]: https://portal.azure.com
[ml-workspace]: /azure/machine-learning/studio/create-workspace
[python-script]: https://github.com/Azure/BatchAIAnomalyDetection/blob/master/batchai/predict.py
[pyscript]: https://github.com/Microsoft/AMLBatchScoringPipeline/blob/master/scripts/predict.py
[storage]: /azure/storage/blobs/storage-blobs-overview
[stream-analytics]: /azure/stream-analytics/
[sql-database]: /azure/sql-database/
[app-insights]: /azure/application-insights/app-insights-overview

---
title: Azure 上的 Python 模型批次評分
description: 使用 Azure Batch AI 建置可根據排程同時對模型進行批次評分的可調整解決方案。
author: njray
ms.date: 12/13/18
ms.custom: azcat-ai
ms.openlocfilehash: 93fc0c81663931c0a8b0f54b41934287056e6953
ms.sourcegitcommit: fb22348f917a76e30a6c090fcd4a18decba0b398
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2018
ms.locfileid: "53450816"
---
# <a name="batch-scoring-of-python-models-on-azure"></a><span data-ttu-id="5542e-103">Azure 上的 Python 模型批次評分</span><span class="sxs-lookup"><span data-stu-id="5542e-103">Batch scoring of Python models on Azure</span></span>

<span data-ttu-id="5542e-104">此參考架構會示範如何使用 Azure Batch AI 建置可根據排程同時對許多模型進行批次評分的可調整解決方案。</span><span class="sxs-lookup"><span data-stu-id="5542e-104">This reference architecture shows how to build a scalable solution for batch scoring many models on a schedule in parallel using Azure Batch AI.</span></span> <span data-ttu-id="5542e-105">此方案可作為範本，並可廣泛用於不同的問題。</span><span class="sxs-lookup"><span data-stu-id="5542e-105">The solution can be used as a template and can generalize to different problems.</span></span>

<span data-ttu-id="5542e-106">此架構的參考實作可在  [GitHub][github] 上取得。</span><span class="sxs-lookup"><span data-stu-id="5542e-106">A reference implementation for this architecture is available on [GitHub][github].</span></span>

![Azure 上的 Python 模型批次評分](./_images/batch-scoring-python.png)

<span data-ttu-id="5542e-108">**案例**：此解決方案可監視 IoT 設定中大量裝置的作業，這其中的每個裝置都會持續傳送感應器讀數。</span><span class="sxs-lookup"><span data-stu-id="5542e-108">**Scenario**: This solution monitors the operation of a large number of devices in an IoT setting where each device sends sensor readings continuously.</span></span> <span data-ttu-id="5542e-109">我們假設每個裝置已預先定型異常偵測模型，可用來預測一系列的量值 (在預先定義的時間間隔內彙總) 是否會對應到異常狀況。</span><span class="sxs-lookup"><span data-stu-id="5542e-109">Each device is assumed to have pre-trained anomaly detection models that need to be used to predict whether a series of measurements, that are aggregated over a predefined time interval, correspond to an anomaly or not.</span></span> <span data-ttu-id="5542e-110">在真實案例中，這可能是需要先進行篩選和彙總的感應器讀數資料流，然後才能用於訓練和即時評分。</span><span class="sxs-lookup"><span data-stu-id="5542e-110">In real-world scenarios, this could be a stream of sensor readings that need to be filtered and aggregated before being used in training or real-time scoring.</span></span> <span data-ttu-id="5542e-111">為了方便起見，此解決方案會在執行評分作業時使用相同的資料檔案。</span><span class="sxs-lookup"><span data-stu-id="5542e-111">For simplicity, the solution uses the same data file when executing scoring jobs.</span></span>

## <a name="architecture"></a><span data-ttu-id="5542e-112">架構</span><span class="sxs-lookup"><span data-stu-id="5542e-112">Architecture</span></span>

<span data-ttu-id="5542e-113">此架構由下列元件組成：</span><span class="sxs-lookup"><span data-stu-id="5542e-113">This architecture consists of the following components:</span></span>

<span data-ttu-id="5542e-114">[Azure 事件中樞][event-hubs]。</span><span class="sxs-lookup"><span data-stu-id="5542e-114">[Azure Event Hubs][event-hubs].</span></span> <span data-ttu-id="5542e-115">此訊息擷取服務每秒可內嵌數百萬個事件訊息。</span><span class="sxs-lookup"><span data-stu-id="5542e-115">This message ingestion service can ingest millions of event messages per second.</span></span> <span data-ttu-id="5542e-116">在此架構中，感應器會將資料流傳送到事件中樞。</span><span class="sxs-lookup"><span data-stu-id="5542e-116">In this architecture, sensors send a stream of data to the event hub.</span></span>

<span data-ttu-id="5542e-117">[Azure 串流分析][stream-analytics]。</span><span class="sxs-lookup"><span data-stu-id="5542e-117">[Azure Stream Analytics][stream-analytics].</span></span> <span data-ttu-id="5542e-118">事件處理引擎。</span><span class="sxs-lookup"><span data-stu-id="5542e-118">An event-processing engine.</span></span> <span data-ttu-id="5542e-119">串流分析作業會從事件中樞讀取資料流，並執行串流處理。</span><span class="sxs-lookup"><span data-stu-id="5542e-119">A Stream Analytics job reads the data streams from the event hub and performs stream processing.</span></span>

<span data-ttu-id="5542e-120">[Azure Batch AI][batch-ai]。</span><span class="sxs-lookup"><span data-stu-id="5542e-120">[Azure Batch AI][batch-ai].</span></span> <span data-ttu-id="5542e-121">此分散式計算引擎會用來定型及測試 Azure 中大量機器學習和 AI 模型。</span><span class="sxs-lookup"><span data-stu-id="5542e-121">This distributed computing engine is used to train and test machine learning and AI models at scale in Azure.</span></span> <span data-ttu-id="5542e-122">Batch AI 會依需求使用自動調整選項來建立虛擬機器，而 Batch AI 叢集中的每個節點都會執行特定感應器的評分作業。</span><span class="sxs-lookup"><span data-stu-id="5542e-122">Batch AI creates virtual machines on demand with an automatic scaling option, where each node in the Batch AI cluster runs a scoring job for a specific sensor.</span></span> <span data-ttu-id="5542e-123">評分 Python [指令碼][python-script] 會在建立於每個叢集節點上的 Docker 容器中執行，並在其中讀取相關的感應器資料、產生預測，然後將這些預測儲存在 Blob 儲存體中。</span><span class="sxs-lookup"><span data-stu-id="5542e-123">The scoring Python [script][python-script] runs in Docker containers that are created on each node of the cluster, where it reads the relevant sensor data, generates predictions and stores them in Blob storage.</span></span>

<span data-ttu-id="5542e-124">[Azure Blob 儲存體][storage]。</span><span class="sxs-lookup"><span data-stu-id="5542e-124">[Azure Blob Storage][storage].</span></span> <span data-ttu-id="5542e-125">Blob 容器會用來儲存預先定型的模型、資料和輸出預測。</span><span class="sxs-lookup"><span data-stu-id="5542e-125">Blob containers are used to store the pretrained models, the data, and the output predictions.</span></span> <span data-ttu-id="5542e-126">模型會上傳至 [create\_resources.ipynb][create-resources] Notebook 中的 Blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="5542e-126">The models are uploaded to Blob storage in the [create\_resources.ipynb][create-resources] notebook.</span></span> <span data-ttu-id="5542e-127">用來訓練這些[單一類別 SVM][one-class-svm] 模型的資料，代表著不同裝置上不同感應器的值。</span><span class="sxs-lookup"><span data-stu-id="5542e-127">These [one-class SVM][one-class-svm] models are trained on data that represents values of different sensors for different devices.</span></span> <span data-ttu-id="5542e-128">此解決方案會假設資料值是在固定時間間隔上彙總的。</span><span class="sxs-lookup"><span data-stu-id="5542e-128">This solution assumes that the data values are aggregated over a fixed interval of time.</span></span>

<span data-ttu-id="5542e-129">[Azure Logic Apps][logic-apps]。</span><span class="sxs-lookup"><span data-stu-id="5542e-129">[Azure Logic Apps][logic-apps].</span></span> <span data-ttu-id="5542e-130">此解決方案會建立每小時執行 Batch AI 作業的邏輯應用程式。</span><span class="sxs-lookup"><span data-stu-id="5542e-130">This solution creates a Logic App that runs hourly Batch AI jobs.</span></span> <span data-ttu-id="5542e-131">Logic Apps 提供建立執行階段工作流程的簡單方法，並且會針對解決方案進行排程。</span><span class="sxs-lookup"><span data-stu-id="5542e-131">Logic Apps provides an easy way to create the runtime workflow and scheduling for the solution.</span></span> <span data-ttu-id="5542e-132">Batch AI 作業會使用 Python [指令碼][script] 來提交，而此指令碼也在 Docker 容器中執行。</span><span class="sxs-lookup"><span data-stu-id="5542e-132">The Batch AI jobs are submitted using a Python [script][script] that also runs in a Docker container.</span></span>

<span data-ttu-id="5542e-133">[Azure Container Registry][acr]。</span><span class="sxs-lookup"><span data-stu-id="5542e-133">[Azure Container Registry][acr].</span></span> <span data-ttu-id="5542e-134">Batch AI 和 Logic Apps 中都會使用 Docker 映像，而該映像會建立在 [create\_resources.ipynb][create-resources] Notebook 中，然後推送至 Container Registry。</span><span class="sxs-lookup"><span data-stu-id="5542e-134">Docker images are used in both Batch AI and Logic Apps and are created in the [create\_resources.ipynb][create-resources] notebook, then pushed to Container Registry.</span></span> <span data-ttu-id="5542e-135">這方式可讓您輕鬆透過其他 Azure 服務 (在此解決方案中為 Logic Apps 和 Batch AI) 來裝載映像和具現化容器。</span><span class="sxs-lookup"><span data-stu-id="5542e-135">This provides a convenient way to host images and instantiate containers through other Azure services—Logic Apps and Batch AI in this solution.</span></span>

## <a name="performance-considerations"></a><span data-ttu-id="5542e-136">效能考量</span><span class="sxs-lookup"><span data-stu-id="5542e-136">Performance considerations</span></span>

<span data-ttu-id="5542e-137">對於標準 Python 模型，普遍認為 CPU 足以處理工作負載。</span><span class="sxs-lookup"><span data-stu-id="5542e-137">For standard Python models, it's generally accepted that CPUs are sufficient to handle the workload.</span></span> <span data-ttu-id="5542e-138">此架構會使用 CPU。</span><span class="sxs-lookup"><span data-stu-id="5542e-138">This architecture uses CPUs.</span></span> <span data-ttu-id="5542e-139">但是，針對[深度學習工作負載][deep]，GPU 的效能通常遠勝於 CPU，但若只能使用 CPU，通常需要大量 CPU 才能擁有相當的效能。</span><span class="sxs-lookup"><span data-stu-id="5542e-139">However, for [deep learning workloads][deep], GPUs generally outperform CPUs by a considerable amount—a sizeable cluster of CPUs is usually needed to get comparable performance.</span></span>

### <a name="parallelizing-across-vms-vs-cores"></a><span data-ttu-id="5542e-140">在 VM 與核心上平行處理</span><span class="sxs-lookup"><span data-stu-id="5542e-140">Parallelizing across VMs vs cores</span></span>

<span data-ttu-id="5542e-141">若要以批次模式執行許多模型的評分程序，這些作業必須在各 VM 上平行進行。</span><span class="sxs-lookup"><span data-stu-id="5542e-141">When running scoring processes of many models in batch mode, the jobs need to be parallelized across VMs.</span></span> <span data-ttu-id="5542e-142">這可能會有兩種方法：</span><span class="sxs-lookup"><span data-stu-id="5542e-142">Two approaches are possible:</span></span> 

* <span data-ttu-id="5542e-143">使用低成本 VM 建立更大的叢集。</span><span class="sxs-lookup"><span data-stu-id="5542e-143">Create a larger cluster using low-cost VMs.</span></span>

* <span data-ttu-id="5542e-144">使用高效能 VM 建立較小的叢集，而每個 VM 上有多個可用核心。</span><span class="sxs-lookup"><span data-stu-id="5542e-144">Create a smaller cluster using high performing VMs with more cores available on each.</span></span>

<span data-ttu-id="5542e-145">一般情況下，標準 Python 模型的評分需求與深度學習模型的評分需求不同，小型叢集應能夠有效率地處理大量已排入佇列的模型。</span><span class="sxs-lookup"><span data-stu-id="5542e-145">In general, scoring of standard Python models is not as demanding as scoring of deep learning models, and a small cluster should be able to handle a large number of queued models efficiently.</span></span> <span data-ttu-id="5542e-146">您可以在資料集大小增加時增加叢集節點數目。</span><span class="sxs-lookup"><span data-stu-id="5542e-146">You can increase the number of cluster nodes as the dataset sizes increase.</span></span>

<span data-ttu-id="5542e-147">為了方便起見，在此案例中，單一評分工作會在單一 Batch AI 作業中提交。</span><span class="sxs-lookup"><span data-stu-id="5542e-147">For convenience in this scenario, one scoring task is submitted within a single Batch AI job.</span></span> <span data-ttu-id="5542e-148">但是，在相同 Batch AI 作業內評分多個資料區塊其實會更有效率。</span><span class="sxs-lookup"><span data-stu-id="5542e-148">However, it can be more efficient to score multiple data chunks within the same Batch AI job.</span></span> <span data-ttu-id="5542e-149">在這些情況下，可撰寫自訂程式碼在多個資料集中進行讀取，以及在執行單一 Batch AI 作業期間對這些資料集執行評分指令碼。</span><span class="sxs-lookup"><span data-stu-id="5542e-149">In those cases, write custom code to read in multiple datasets and execute the scoring script for those during a single Batch AI job execution.</span></span>

### <a name="file-servers"></a><span data-ttu-id="5542e-150">檔案伺服器</span><span class="sxs-lookup"><span data-stu-id="5542e-150">File servers</span></span>

<span data-ttu-id="5542e-151">使用 Batch AI 時，您可以根據您案例所需輸送量選擇多個儲存體選項。</span><span class="sxs-lookup"><span data-stu-id="5542e-151">When using Batch AI, you can choose multiple storage options depending on the throughput needed for your scenario.</span></span> <span data-ttu-id="5542e-152">針對輸送量需求低的工作負載，使用 Blob 儲存體應該就已足夠。</span><span class="sxs-lookup"><span data-stu-id="5542e-152">For workloads with low throughput requirements, using blob storage should be enough.</span></span> <span data-ttu-id="5542e-153">或者，Batch AI 也支援 [Batch AI 檔案伺服器][bai-file-server]，此受控的單一節點 NFS 可自動裝載到叢集節點上，為作業提供集中存取的儲存體位置。</span><span class="sxs-lookup"><span data-stu-id="5542e-153">Alternatively, Batch AI also supports a [Batch AI File Server][bai-file-server], a managed, single-node NFS, which can be automatically mounted on cluster nodes to provide a centrally accessible storage location for jobs.</span></span> <span data-ttu-id="5542e-154">在大部分情況下，工作區中只需要一個檔案伺服器，而且您可以將訓練作業的資料分到不同目錄中。</span><span class="sxs-lookup"><span data-stu-id="5542e-154">For most cases, only one file server is needed in a workspace, and you can separate data for your training jobs into different directories.</span></span>

<span data-ttu-id="5542e-155">如果單一節點 NFS 不適用於您的工作負載，Batch AI 還可支援其他儲存體選項，包括 [Azure 檔案服務][azure-files]和自訂解決方案，例如 Gluster 或 Lustre 檔案系統。</span><span class="sxs-lookup"><span data-stu-id="5542e-155">If a single-node NFS isn't appropriate for your workloads, Batch AI supports other storage options, including [Azure Files][azure-files] and custom solutions such as a Gluster or Lustre file system.</span></span>

## <a name="management-considerations"></a><span data-ttu-id="5542e-156">管理考量</span><span class="sxs-lookup"><span data-stu-id="5542e-156">Management considerations</span></span>

### <a name="monitoring-batch-ai-jobs"></a><span data-ttu-id="5542e-157">監視 Batch AI 作業</span><span class="sxs-lookup"><span data-stu-id="5542e-157">Monitoring Batch AI jobs</span></span>

<span data-ttu-id="5542e-158">監視執行中作業的進度相當重要，但要監視叢集上的作用中節點可能很困難。</span><span class="sxs-lookup"><span data-stu-id="5542e-158">It's important to monitor the progress of running jobs, but it can be a challenge to monitor across a cluster of active nodes.</span></span> <span data-ttu-id="5542e-159">若要了解叢集的整體狀態，請移至 [Azure 入口網站][portal]的 [Batch AI] 刀鋒視窗，以檢查叢集中的節點狀態。</span><span class="sxs-lookup"><span data-stu-id="5542e-159">To get a sense of the overall state of the cluster, go to the **Batch AI** blade of the [Azure Portal][portal] to inspect the state of the nodes in the cluster.</span></span> <span data-ttu-id="5542e-160">如果節點狀態是非使用中或作業已失敗，則錯誤記錄會儲存在 Blob 儲存體中，您也可在入口網站的 [作業] 刀鋒視窗中存取錯誤記錄。</span><span class="sxs-lookup"><span data-stu-id="5542e-160">If a node is inactive or a job has failed, the error logs are saved to blob storage, and are also accessible in the **Jobs** blade of the portal.</span></span>

<span data-ttu-id="5542e-161">如需更多監視功能，您可以將記錄連線到 [Application Insights][ai]，或執行不同處理程序來對 Batch AI 叢集和其作業狀態進行輪詢。</span><span class="sxs-lookup"><span data-stu-id="5542e-161">For richer monitoring, connect logs to [Application Insights][ai], or run separate processes to poll for the state of the Batch AI cluster and its jobs.</span></span>

### <a name="logging-in-batch-ai"></a><span data-ttu-id="5542e-162">Batch AI 中的記錄功能</span><span class="sxs-lookup"><span data-stu-id="5542e-162">Logging in Batch AI</span></span>

<span data-ttu-id="5542e-163">Batch AI 會記錄相關聯 Azure 儲存體帳戶的所有 stdout/stderr。</span><span class="sxs-lookup"><span data-stu-id="5542e-163">Batch AI logs all stdout/stderr to the associated Azure storage account.</span></span> <span data-ttu-id="5542e-164">若要輕鬆瀏覽記錄檔，您可以使用 [Azure 儲存體總管][explorer]之類的儲存體瀏覽工具。</span><span class="sxs-lookup"><span data-stu-id="5542e-164">For easy navigation of the log files, use a storage navigation tool such as [Azure Storage Explorer][explorer].</span></span>

<span data-ttu-id="5542e-165">當您部署此參考架構時，有個選項可讓您設定更簡單的記錄系統。</span><span class="sxs-lookup"><span data-stu-id="5542e-165">When you deploy this reference architecture, you have the option to set up a simpler logging system.</span></span> <span data-ttu-id="5542e-166">使用此選項時，不同作業上的所有記錄都會儲存到您 Blob 容器中的相同目錄，如下所示。</span><span class="sxs-lookup"><span data-stu-id="5542e-166">With this option, all the logs across the different jobs are saved to the same directory in your blob container as shown below.</span></span> <span data-ttu-id="5542e-167">使用這些記錄來監視每個作業和每個映像的處理時間，就可讓您更佳了解如何最佳化程序。</span><span class="sxs-lookup"><span data-stu-id="5542e-167">Use these logs to monitor how long it takes for each job and each image to process, so you have a better sense of how to optimize the process.</span></span>

![Azure 儲存體總管](./_images/batch-scoring-python-monitor.png)

## <a name="cost-considerations"></a><span data-ttu-id="5542e-169">成本考量</span><span class="sxs-lookup"><span data-stu-id="5542e-169">Cost considerations</span></span>

<span data-ttu-id="5542e-170">此參考架構中成本最高的元件是計算資源。</span><span class="sxs-lookup"><span data-stu-id="5542e-170">The most expensive components used in this reference architecture are the compute resources.</span></span>

<span data-ttu-id="5542e-171">Batch AI 叢集大小可根據佇列中的作業來相應增加和減少。</span><span class="sxs-lookup"><span data-stu-id="5542e-171">The Batch AI cluster size scales up and down depending on the jobs in the queue.</span></span> <span data-ttu-id="5542e-172">您可以使用兩種方式中的其中一種來啟用 Batch AI 的[自動調整功能][automatic-scaling]。</span><span class="sxs-lookup"><span data-stu-id="5542e-172">You can enable [automatic scaling][automatic-scaling] with Batch AI in one of two ways.</span></span> <span data-ttu-id="5542e-173">您可以透過程式設計的方式來完成，也就是在 .env 檔案中進行設定 (這是[部署步驟][github]的一部份)，或是在建立叢集之後，直接在入口網站中變更調整公式。</span><span class="sxs-lookup"><span data-stu-id="5542e-173">You can do so programmatically, which can be configured in the .env file that is part of the [deployment steps][github], or you can change the scale formula directly in the portal after the cluster is created.</span></span>

<span data-ttu-id="5542e-174">對於不需要立即處理的工作，可設定自動調整公式，如此一來，預設狀態 (最小值) 就是零個節點的叢集。</span><span class="sxs-lookup"><span data-stu-id="5542e-174">For work that doesn't require immediate processing, configure the automatic scaling formula so the default state (minimum) is a cluster of zero nodes.</span></span> <span data-ttu-id="5542e-175">使用此設定時，叢集一開始會有零個節點，並只在偵測到佇列中有作業時，才會相應增加。</span><span class="sxs-lookup"><span data-stu-id="5542e-175">With this configuration, the cluster starts with zero nodes and only scales up when it detects jobs in the queue.</span></span> <span data-ttu-id="5542e-176">如果批次評分程序一天只會發生幾次 (或更少)，則此設定可省下大量成本。</span><span class="sxs-lookup"><span data-stu-id="5542e-176">If the batch scoring process only happens a few times a day or less, this setting enables significant cost savings.</span></span>

<span data-ttu-id="5542e-177">自動調整可能不適合間距太近的批次作業。</span><span class="sxs-lookup"><span data-stu-id="5542e-177">Automatic scaling may not be appropriate for batch jobs that happen too close to each other.</span></span> <span data-ttu-id="5542e-178">啟動及關閉叢集所花費的時間也會產生成本，因此如果批次工作負載會在前一個作業結束後的幾分鐘內啟動，那麼讓叢集在作業之間持續運作可能比較符合成本效益。</span><span class="sxs-lookup"><span data-stu-id="5542e-178">The time that it takes for a cluster to spin up and spin down also incur a cost, so if a batch workload begins only a few minutes after the previous job ends, it might be more cost effective to keep the cluster running between jobs.</span></span> <span data-ttu-id="5542e-179">這取決於評分程序是排定為高頻率執行 (例如每小時) 或低頻率執行 (例如一個月一次)。</span><span class="sxs-lookup"><span data-stu-id="5542e-179">That depends on whether scoring processes are scheduled to run at a high frequency (every hour, for example), or less frequently (once a month, for example).</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="5542e-180">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="5542e-180">Deploy the solution</span></span>

<span data-ttu-id="5542e-181">此架構的參考實作可在 [GitHub][github] 上取得。</span><span class="sxs-lookup"><span data-stu-id="5542e-181">The reference implementation of this architecture is available on [GitHub][github].</span></span> <span data-ttu-id="5542e-182">遵循該處的設定步驟，即可使用 Batch AI 建置可同時對許多模型進行批次評分的可調整解決方案。</span><span class="sxs-lookup"><span data-stu-id="5542e-182">Follow the setup steps there to build a scalable solution for scoring many models in parallel using Batch AI.</span></span>

[acr]: /azure/container-registry/container-registry-intro
[ai]: /azure/application-insights/app-insights-overview
[automatic-scaling]: /azure/batch/batch-automatic-scaling
[azure-files]: /azure/storage/files/storage-files-introduction
[batch-ai]: /azure/batch-ai/
[bai-file-server]: /azure/batch-ai/resource-concepts#file-server
[create-resources]: https://github.com/Azure/BatchAIAnomalyDetection/blob/master/create_resources.ipynb
[deep]: /azure/architecture/reference-architectures/ai/batch-scoring-deep-learning
[event-hubs]: /azure/event-hubs/event-hubs-geo-dr
[explorer]: https://azure.microsoft.com/en-us/features/storage-explorer/
[github]: https://github.com/Azure/BatchAIAnomalyDetection
[logic-apps]: /azure/logic-apps/logic-apps-overview
[one-class-svm]: http://scikit-learn.org/stable/modules/generated/sklearn.svm.OneClassSVM.html
[portal]: https://portal.azure.com
[python-script]: https://github.com/Azure/BatchAIAnomalyDetection/blob/master/batchai/predict.py
[script]: https://github.com/Azure/BatchAIAnomalyDetection/blob/master/sched/submit_jobs.py
[storage]: /azure/storage/blobs/storage-blobs-overview
[stream-analytics]: /azure/stream-analytics/

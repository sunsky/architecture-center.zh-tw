---
title: Azure Databricks 使用 Azure 監視器的效能疑難排解
titleSuffix: ''
description: 使用 Azure Databricks 中的效能問題進行疑難排解的 Grafana 儀表板
author: petertaylor9999
ms.date: 04/02/2019
ms.topic: ''
ms.service: ''
ms.subservice: ''
ms.openlocfilehash: 49ec63d0c45ab388ca83b3ab0562428327539619
ms.sourcegitcommit: 1a3cc91530d56731029ea091db1f15d41ac056af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887956"
---
# <a name="troubleshoot-performance-bottlenecks-in-azure-databricks"></a><span data-ttu-id="f01bc-103">疑難排解在 Azure Databricks 中的效能瓶頸</span><span class="sxs-lookup"><span data-stu-id="f01bc-103">Troubleshoot performance bottlenecks in Azure Databricks</span></span>

<span data-ttu-id="f01bc-104">本文說明如何在 Azure Databricks 上的 Spark 作業中找出效能瓶頸時，用以監視儀表板。</span><span class="sxs-lookup"><span data-stu-id="f01bc-104">This article describes how to use monitoring dashboards to find performance bottlenecks in Spark jobs on Azure Databricks.</span></span>

<span data-ttu-id="f01bc-105">[Azure Databricks](/azure/azure-databricks/)已[Apache Spark](https://spark.apache.org/)– 基礎分析服務，可讓您更快速開發並部署巨量資料分析變得更加容易。</span><span class="sxs-lookup"><span data-stu-id="f01bc-105">[Azure Databricks](/azure/azure-databricks/) is an [Apache Spark](https://spark.apache.org/)–based analytics service that makes it easy to rapidly develop and deploy big data analytics.</span></span> <span data-ttu-id="f01bc-106">監視和疑難排解效能問題時，重要作業系統生產 Azure Databricks 工作負載。</span><span class="sxs-lookup"><span data-stu-id="f01bc-106">Monitoring and troubleshooting performance issues is a critical when operating production Azure Databricks workloads.</span></span> <span data-ttu-id="f01bc-107">若要識別常見效能問題，最好使用監視根據遙測資料的視覺效果。</span><span class="sxs-lookup"><span data-stu-id="f01bc-107">To identify common performance issues, it's helpful to use monitoring visualizations based on telemetry data.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f01bc-108">必要條件</span><span class="sxs-lookup"><span data-stu-id="f01bc-108">Prerequisites</span></span>

<span data-ttu-id="f01bc-109">若要設定本文中所示的 Grafana 儀表板：</span><span class="sxs-lookup"><span data-stu-id="f01bc-109">To set up the Grafana dashboards shown in this article:</span></span>

- <span data-ttu-id="f01bc-110">設定您的 Databricks 叢集，以將遙測傳送至 Log Analytics 工作區中，使用 Azure Databricks 監視程式庫。</span><span class="sxs-lookup"><span data-stu-id="f01bc-110">Configure your Databricks cluster to send telemetry to a Log Analytics workspace, using the Azure Databricks Monitoring Library.</span></span> <span data-ttu-id="f01bc-111">如需詳細資訊，請參閱 <<c0> [ 若要將計量傳送至 Azure 監視器設定 Azure Databricks](./configure-cluster.md)。</span><span class="sxs-lookup"><span data-stu-id="f01bc-111">For details, see [Configure Azure Databricks to send metrics to Azure Monitor](./configure-cluster.md).</span></span>

- <span data-ttu-id="f01bc-112">虛擬機器中部署 Grafana。</span><span class="sxs-lookup"><span data-stu-id="f01bc-112">Deploy Grafana in a virtual machine.</span></span> <span data-ttu-id="f01bc-113">請參閱[使用儀表板，以視覺化方式檢視 Azure Databricks 計量](./dashboards.md)。</span><span class="sxs-lookup"><span data-stu-id="f01bc-113">See [Use dashboards to visualize Azure Databricks metrics](./dashboards.md).</span></span>

<span data-ttu-id="f01bc-114">部署 Grafana 儀表板包含一組的時間序列視覺效果。</span><span class="sxs-lookup"><span data-stu-id="f01bc-114">The Grafana dashboard that is deployed includes a set of time-series visualizations.</span></span> <span data-ttu-id="f01bc-115">每個圖表會的 Apache Spark 作業，相關的度量的作業，以及每個階段所組成的工作階段的時間序列圖。</span><span class="sxs-lookup"><span data-stu-id="f01bc-115">Each graph is time-series plot of metrics related to an Apache Spark job, the stages of the job, and tasks that make up each stage.</span></span>

## <a name="azure-databricks-performance-overview"></a><span data-ttu-id="f01bc-116">Azure Databricks 效能概觀</span><span class="sxs-lookup"><span data-stu-id="f01bc-116">Azure Databricks performance overview</span></span>

<span data-ttu-id="f01bc-117">Azure Databricks 是以 Apache Spark，這是一般用途的分散式運算系統為基礎。</span><span class="sxs-lookup"><span data-stu-id="f01bc-117">Azure Databricks is based on Apache Spark, a general-purpose distributed computing system.</span></span> <span data-ttu-id="f01bc-118">應用程式程式碼，稱為**作業**，協調叢集管理員的 Apache Spark 叢集上執行。</span><span class="sxs-lookup"><span data-stu-id="f01bc-118">Application code, known as a **job**, executes on an Apache Spark cluster, coordinated by the cluster manager.</span></span> <span data-ttu-id="f01bc-119">一般情況下，作業是計算的最高層級單位。</span><span class="sxs-lookup"><span data-stu-id="f01bc-119">In general, a job is the highest-level unit of computation.</span></span> <span data-ttu-id="f01bc-120">工作代表完成的作業執行的 Spark 應用程式。</span><span class="sxs-lookup"><span data-stu-id="f01bc-120">A job represents the complete operation performed by the Spark application.</span></span> <span data-ttu-id="f01bc-121">一般的作業包括從來源讀取資料、 套用資料轉換，並將結果寫入至儲存體或另一個目的地。</span><span class="sxs-lookup"><span data-stu-id="f01bc-121">A typical operation includes reading data from a source, applying data transformations, and writing the results to storage or another destination.</span></span>

<span data-ttu-id="f01bc-122">工作可分成**階段**。</span><span class="sxs-lookup"><span data-stu-id="f01bc-122">Jobs are broken down into **stages**.</span></span> <span data-ttu-id="f01bc-123">作業播放階段循序，表示後面的階段中必須等候較早的階段，才能完成。</span><span class="sxs-lookup"><span data-stu-id="f01bc-123">The job advances through the stages sequentially, which means that later stages must wait for earlier stages to complete.</span></span> <span data-ttu-id="f01bc-124">階段包含群組的相同**任務**，可以平行執行的 Spark 叢集的多個節點上。</span><span class="sxs-lookup"><span data-stu-id="f01bc-124">Stages contain groups of identical **tasks** that can be executed in parallel on multiple nodes of the Spark cluster.</span></span> <span data-ttu-id="f01bc-125">工作是執行的最細微的資料子集上進行單位。</span><span class="sxs-lookup"><span data-stu-id="f01bc-125">Tasks are the most granular unit of execution taking place on a subset of the data.</span></span>

<span data-ttu-id="f01bc-126">下節說明一些適用於效能疑難排解的儀表板視覺效果。</span><span class="sxs-lookup"><span data-stu-id="f01bc-126">The next sections describe some dashboard visualizations that are useful for performance troubleshooting.</span></span>

## <a name="job-and-stage-latency"></a><span data-ttu-id="f01bc-127">工作和階段延遲</span><span class="sxs-lookup"><span data-stu-id="f01bc-127">Job and stage latency</span></span>

<span data-ttu-id="f01bc-128">此作業完成之前啟動時，作業延遲是從工作執行的持續時間。</span><span class="sxs-lookup"><span data-stu-id="f01bc-128">Job latency is the duration of a job execution from when it starts until it completes.</span></span> <span data-ttu-id="f01bc-129">它會顯示為工作執行每個叢集和應用程式識別碼的百分位數，以允許極端值的視覺效果。</span><span class="sxs-lookup"><span data-stu-id="f01bc-129">It is shown as percentiles of a job execution per cluster and application ID, to allow the visualization of outliers.</span></span> <span data-ttu-id="f01bc-130">下圖顯示作業歷程記錄的第 90 個百分位數，達到 50 秒，即使第 50 個百分位數一致的方式是大約 10 秒鐘左右。</span><span class="sxs-lookup"><span data-stu-id="f01bc-130">The following graph shows a job history where the 90th percentile reached 50 seconds, even though the 50th percentile was consistently around 10 seconds.</span></span>

![顯示作業的延遲圖表](./_images/grafana-job-latency.png)

<span data-ttu-id="f01bc-132">請調查叢集和應用程式，查看尖峰延遲的作業執行。</span><span class="sxs-lookup"><span data-stu-id="f01bc-132">Investigate job execution by cluster and application, looking for spikes in latency.</span></span> <span data-ttu-id="f01bc-133">一旦識別出叢集和應用程式具有高延遲，移至調查階段延遲。</span><span class="sxs-lookup"><span data-stu-id="f01bc-133">Once clusters and applications with high latency are identified, move on to investigate stage latency.</span></span>

<span data-ttu-id="f01bc-134">若要讓極端值的視覺效果，階段延遲也會顯示為百分位數。</span><span class="sxs-lookup"><span data-stu-id="f01bc-134">Stage latency is also shown as percentiles to allow the visualization of outliers.</span></span> <span data-ttu-id="f01bc-135">階段延遲分散的叢集、 應用程式，以及階段名稱。</span><span class="sxs-lookup"><span data-stu-id="f01bc-135">Stage latency is broken out by cluster, application, and stage name.</span></span> <span data-ttu-id="f01bc-136">識別圖形中要判斷哪些工作持有回階段完成的工作延遲尖峰。</span><span class="sxs-lookup"><span data-stu-id="f01bc-136">Identify spikes in task latency in the graph to determine which tasks are holding back completion of the stage.</span></span>

![顯示階段延遲圖表](./_images/grafana-stage-latency.png)

<span data-ttu-id="f01bc-138">叢集輸送量圖形顯示工作、 階段和每分鐘完成的工作的數目。</span><span class="sxs-lookup"><span data-stu-id="f01bc-138">The cluster throughput graph shows the number of jobs, stages, and tasks completed per minute.</span></span> <span data-ttu-id="f01bc-139">這可協助您了解階段和每個作業的工作數目相對方面的工作負載。</span><span class="sxs-lookup"><span data-stu-id="f01bc-139">This helps you to understand the workload in terms of the relative number of stages and tasks per job.</span></span> <span data-ttu-id="f01bc-140">您可以看到每分鐘的作業數目範圍介於 2 到 6，雖然階段數大約 12 &ndash; 24 每分鐘。</span><span class="sxs-lookup"><span data-stu-id="f01bc-140">Here you can see that the number of jobs per minute ranges between 2 and 6, while the number of stages is about 12 &ndash; 24 per minute.</span></span>

![圖形顯示叢集輸送量](./_images/grafana-cluster-throughput.png)

## <a name="sum-of-task-execution-latency"></a><span data-ttu-id="f01bc-142">工作執行延遲的總和</span><span class="sxs-lookup"><span data-stu-id="f01bc-142">Sum of task execution latency</span></span>

<span data-ttu-id="f01bc-143">此視覺效果會顯示每個主機叢集上執行的工作執行延遲的總和。</span><span class="sxs-lookup"><span data-stu-id="f01bc-143">This visualization shows the sum of task execution latency per host running on a cluster.</span></span> <span data-ttu-id="f01bc-144">您可以使用此圖表來偵測在叢集或每個執行程式的工作 misallocation 因為主應用程式變慢而執行速度變慢的工作。</span><span class="sxs-lookup"><span data-stu-id="f01bc-144">Use this graph to detect tasks that run slowly due to the host slowing down on a cluster, or a misallocation of tasks per executor.</span></span> <span data-ttu-id="f01bc-145">下圖中，大部分的主機會有約 30 秒的總和。</span><span class="sxs-lookup"><span data-stu-id="f01bc-145">In the following graph, most of the hosts have a sum of about 30 seconds.</span></span> <span data-ttu-id="f01bc-146">不過，兩個主機有大約 10 分鐘，將滑鼠停留的總和。</span><span class="sxs-lookup"><span data-stu-id="f01bc-146">However, two of the hosts have sums that hover around 10 minutes.</span></span> <span data-ttu-id="f01bc-147">主機執行速度很慢或會如每個執行程式的工作數目。</span><span class="sxs-lookup"><span data-stu-id="f01bc-147">Either the hosts are running slow or the number of tasks per executor is misallocated.</span></span>

![圖形顯示加總的每個主機的工作執行](./_images/grafana-sum-task-exec.png)

<span data-ttu-id="f01bc-149">每個執行程式的工作數目會顯示，兩個執行程式會指派的工作，數目不相稱造成瓶頸。</span><span class="sxs-lookup"><span data-stu-id="f01bc-149">The number of tasks per executor shows that two executors are assigned a disproportionate number of tasks, causing a bottleneck.</span></span>

![圖形來表示每個執行程式的工作](./_images/grafana-tasks-per-exec.png)

## <a name="task-metrics-per-stage"></a><span data-ttu-id="f01bc-151">工作計量，每個階段</span><span class="sxs-lookup"><span data-stu-id="f01bc-151">Task metrics per stage</span></span>

<span data-ttu-id="f01bc-152">工作計量視覺效果提供工作執行的成本細分。</span><span class="sxs-lookup"><span data-stu-id="f01bc-152">The task metrics visualization gives the cost breakdown for a task execution.</span></span> <span data-ttu-id="f01bc-153">您可以使用它所花費的相對的時間，請參閱上工作，例如序列化和還原序列化。</span><span class="sxs-lookup"><span data-stu-id="f01bc-153">You can use it see the relative time spent on tasks such as serialization and deserialization.</span></span> <span data-ttu-id="f01bc-154">這項資料可能會顯示最佳化的機會&mdash;例如，藉由使用[將變數廣播](https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#broadcast-variables)以避免傳送資料。</span><span class="sxs-lookup"><span data-stu-id="f01bc-154">This data might show opportunities to optimize &mdash; for example, by using [broadcast variables](https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#broadcast-variables) to avoid shipping data.</span></span> <span data-ttu-id="f01bc-155">工作計量也會顯示工作的隨機資料大小和隨機讀取和寫入次數。</span><span class="sxs-lookup"><span data-stu-id="f01bc-155">The task metrics also show the shuffle data size for a task, and the shuffle read and write times.</span></span> <span data-ttu-id="f01bc-156">如果這些值很高，這表示在網路之間是移動大量資料。</span><span class="sxs-lookup"><span data-stu-id="f01bc-156">If these values are high, it means that a lot of data is moving across the network.</span></span>

<span data-ttu-id="f01bc-157">另一個工作計量是排程器的延遲，排程工作所花費的。</span><span class="sxs-lookup"><span data-stu-id="f01bc-157">Another task metric is the scheduler delay, which measures how long it takes to schedule a task.</span></span> <span data-ttu-id="f01bc-158">在理想情況下，這個值應該是低相較於執行程式計算時間，這是時間花在實際執行工作。</span><span class="sxs-lookup"><span data-stu-id="f01bc-158">Ideally, this value should be low compared to the executor compute time, which is the time spent actually executing the task.</span></span>

<span data-ttu-id="f01bc-159">下圖所示的排程器的延遲時間 (3.7 s)，超過執行程式運算時間 (1.1 s)。</span><span class="sxs-lookup"><span data-stu-id="f01bc-159">The following graph shows a scheduler delay time (3.7 s) that exceeds the executor compute time (1.1 s).</span></span> <span data-ttu-id="f01bc-160">這表示更多的時間花在等候比執行實際的工作排程的工作。</span><span class="sxs-lookup"><span data-stu-id="f01bc-160">That means more time is spent waiting for tasks to be scheduled than doing the actual work.</span></span>

![顯示每個階段的工作計量圖表](./_images/grafana-metrics-per-stage.png)

<span data-ttu-id="f01bc-162">在此情況下，問題被因太多的資料分割，而造成的額外負荷很多。</span><span class="sxs-lookup"><span data-stu-id="f01bc-162">In this case, the problem was caused by having too many partitions, which caused a lot of overhead.</span></span> <span data-ttu-id="f01bc-163">減少資料分割數目降低的排程器的延遲時間。</span><span class="sxs-lookup"><span data-stu-id="f01bc-163">Reducing the number of partitions lowered the scheduler delay time.</span></span> <span data-ttu-id="f01bc-164">下圖所示，大部分的時間是花費在執行工作。</span><span class="sxs-lookup"><span data-stu-id="f01bc-164">The next graph shows that most of the time is spent executing the task.</span></span>

![顯示每個階段的工作計量圖表](./_images/grafana-metrics-per-stage2.png)

## <a name="streaming-throughput-and-latency"></a><span data-ttu-id="f01bc-166">串流的輸送量和延遲</span><span class="sxs-lookup"><span data-stu-id="f01bc-166">Streaming throughput and latency</span></span>

<span data-ttu-id="f01bc-167">串流處理輸送量直接相關結構化串流。</span><span class="sxs-lookup"><span data-stu-id="f01bc-167">Streaming throughput is directly related to structured streaming.</span></span> <span data-ttu-id="f01bc-168">有兩個重要的計量資料流的輸送量相關聯：每秒的第二個和處理資料列的輸入資料列。</span><span class="sxs-lookup"><span data-stu-id="f01bc-168">There are two important metrics associated with streaming throughput: Input rows per second and processed rows per second.</span></span> <span data-ttu-id="f01bc-169">如果每秒的輸入資料列 outpaces 每秒處理的資料列，則表示串流處理系統落後。</span><span class="sxs-lookup"><span data-stu-id="f01bc-169">If input rows per second outpaces processed rows per second, it means the stream processing system is falling behind.</span></span> <span data-ttu-id="f01bc-170">此外，如果輸入的資料來自事件中樞或 Kafka，然後每秒的輸入資料列應該跟在後端的資料擷取率。</span><span class="sxs-lookup"><span data-stu-id="f01bc-170">Also, if the input data comes from Event Hubs or Kafka, then input rows per second should keep up with the data ingestion rate at the front end.</span></span>

<span data-ttu-id="f01bc-171">兩個工作可以有類似的叢集輸送量，但非常不同的串流度量。</span><span class="sxs-lookup"><span data-stu-id="f01bc-171">Two jobs can have similar cluster throughput but very different streaming metrics.</span></span> <span data-ttu-id="f01bc-172">下列螢幕擷取畫面顯示兩個不同的工作負載。</span><span class="sxs-lookup"><span data-stu-id="f01bc-172">The following screenshot shows two different workloads.</span></span> <span data-ttu-id="f01bc-173">也就是類似叢集的輸送量 （工作、 階段和工作每分鐘）。</span><span class="sxs-lookup"><span data-stu-id="f01bc-173">They are similar in terms of cluster throughput (jobs, stages, and tasks per minute).</span></span> <span data-ttu-id="f01bc-174">但第二次執行會處理 12,000 資料列/秒與 4,000 資料列/秒。</span><span class="sxs-lookup"><span data-stu-id="f01bc-174">But the second run processes 12,000 rows/sec versus 4,000 rows/sec.</span></span>

![圖形顯示選串流輸送量](./_images/grafana-streaming-throughput.png)

<span data-ttu-id="f01bc-176">串流處理能力通常是較佳的商務計量比叢集輸送量，因為它會測量所處理的資料記錄數目。</span><span class="sxs-lookup"><span data-stu-id="f01bc-176">Streaming throughput is often a better business metric than cluster throughput, because it measures the number of data records that are processed.</span></span>

## <a name="resource-consumption-per-executor"></a><span data-ttu-id="f01bc-177">每個執行程式的資源耗用量</span><span class="sxs-lookup"><span data-stu-id="f01bc-177">Resource consumption per executor</span></span>

<span data-ttu-id="f01bc-178">這些計量有助於了解每個執行程式執行的工作。</span><span class="sxs-lookup"><span data-stu-id="f01bc-178">These metrics help to understand the work that each executor performs.</span></span>

<span data-ttu-id="f01bc-179">**百分比計量**測量執行程式執行了多少時間花費在各種項目，表示為與整體的執行程式計算時間花費的時間比例。</span><span class="sxs-lookup"><span data-stu-id="f01bc-179">**Percentage metrics** measure how much time an executor spends on various things, expressed as a ratio of time spent versus the overall executor compute time.</span></span> <span data-ttu-id="f01bc-180">計量為：</span><span class="sxs-lookup"><span data-stu-id="f01bc-180">The metrics are:</span></span>

- <span data-ttu-id="f01bc-181">%序列化時間</span><span class="sxs-lookup"><span data-stu-id="f01bc-181">% Serialize time</span></span>
- <span data-ttu-id="f01bc-182">%還原序列化時間</span><span class="sxs-lookup"><span data-stu-id="f01bc-182">% Deserialize time</span></span>
- <span data-ttu-id="f01bc-183">%Cpu 執行程式的時間</span><span class="sxs-lookup"><span data-stu-id="f01bc-183">% CPU executor time</span></span>
- <span data-ttu-id="f01bc-184">JVM 時間 %</span><span class="sxs-lookup"><span data-stu-id="f01bc-184">% JVM time</span></span>

<span data-ttu-id="f01bc-185">這些視覺效果顯示多少每個這些度量佔整體的執行程式處理。</span><span class="sxs-lookup"><span data-stu-id="f01bc-185">These visualizations show how much each of these metrics contributes to overall executor processing.</span></span>

![顯示百分比計量圖表](./_images/grafana-percentage.png)

<span data-ttu-id="f01bc-187">**隨機播放計量**度量相關資料要跳過整個執行程式。</span><span class="sxs-lookup"><span data-stu-id="f01bc-187">**Shuffle metrics** are metrics related to data shuffling across the executors.</span></span>

- <span data-ttu-id="f01bc-188">隨機 I/O</span><span class="sxs-lookup"><span data-stu-id="f01bc-188">Shuffle I/O</span></span>
- <span data-ttu-id="f01bc-189">隨機記憶體</span><span class="sxs-lookup"><span data-stu-id="f01bc-189">Shuffle memory</span></span>
- <span data-ttu-id="f01bc-190">檔案系統使用量</span><span class="sxs-lookup"><span data-stu-id="f01bc-190">File system usage</span></span>
- <span data-ttu-id="f01bc-191">磁碟使用量</span><span class="sxs-lookup"><span data-stu-id="f01bc-191">Disk usage</span></span>

## <a name="common-performance-bottlenecks"></a><span data-ttu-id="f01bc-192">常見的效能瓶頸</span><span class="sxs-lookup"><span data-stu-id="f01bc-192">Common performance bottlenecks</span></span>

<span data-ttu-id="f01bc-193">在 Spark 中的兩個常見效能瓶頸*落後的執行緒執行的工作*並*非最佳的隨機分割區計數*。</span><span class="sxs-lookup"><span data-stu-id="f01bc-193">Two common performance bottlenecks in Spark are *task stragglers* and a *non-optimal shuffle partition count*.</span></span>

### <a name="task-stragglers"></a><span data-ttu-id="f01bc-194">工作落後的執行緒執行</span><span class="sxs-lookup"><span data-stu-id="f01bc-194">Task stragglers</span></span>

<span data-ttu-id="f01bc-195">中的工作階段會循序執行，以封鎖後續階段的早期階段。</span><span class="sxs-lookup"><span data-stu-id="f01bc-195">The stages in a job are executed sequentially, with earlier stages blocking later stages.</span></span> <span data-ttu-id="f01bc-196">如果一項工作執行隨機分割區的速度比其他工作，在叢集中的所有工作必須都等候趕上之前可以在結束階段緩慢的工作。</span><span class="sxs-lookup"><span data-stu-id="f01bc-196">If one task executes a shuffle partition more slowly than other tasks, all tasks in the cluster must wait for the slow task to catch up before the stage can end.</span></span> <span data-ttu-id="f01bc-197">這種情形，原因如下：</span><span class="sxs-lookup"><span data-stu-id="f01bc-197">This can happen for the following reasons:</span></span>

1. <span data-ttu-id="f01bc-198">主機群組的執行速度很慢。</span><span class="sxs-lookup"><span data-stu-id="f01bc-198">A host or group of hosts are running slow.</span></span> <span data-ttu-id="f01bc-199">徵兆：高的工作、 階段或作業延遲和輸送量低的叢集。</span><span class="sxs-lookup"><span data-stu-id="f01bc-199">Symptoms: High task, stage, or job latency and low cluster throughput.</span></span> <span data-ttu-id="f01bc-200">不會平均分散工作延遲，每部主機的總和。</span><span class="sxs-lookup"><span data-stu-id="f01bc-200">The summation of tasks latencies per host won't be evenly distributed.</span></span> <span data-ttu-id="f01bc-201">不過，資源耗用量會平均分散執行程式。</span><span class="sxs-lookup"><span data-stu-id="f01bc-201">However, resource consumption will be evenly distributed across executors.</span></span>

1. <span data-ttu-id="f01bc-202">工作必須耗費資源的彙總來執行 （資料扭曲）。</span><span class="sxs-lookup"><span data-stu-id="f01bc-202">Tasks have an expensive aggregation to execute (data skewing).</span></span> <span data-ttu-id="f01bc-203">徵兆：高的工作、 階段，或作業和低的叢集的輸送量，但延遲每部主機的總和會平均分散。</span><span class="sxs-lookup"><span data-stu-id="f01bc-203">Symptoms: High task, stage, or job and low cluster throughput, but the summation of latencies per host is evenly distributed.</span></span> <span data-ttu-id="f01bc-204">資源耗用量會平均分散執行程式。</span><span class="sxs-lookup"><span data-stu-id="f01bc-204">Resource consumption will be evenly distributed across executors.</span></span>

1. <span data-ttu-id="f01bc-205">如果資料分割的大小不相等，較大的磁碟分割可能會導致 （扭曲的磁碟分割） 的不平衡的工作執行。</span><span class="sxs-lookup"><span data-stu-id="f01bc-205">If partitions are of unequal size, a larger partition may cause unbalanced task execution (partition skewing).</span></span> <span data-ttu-id="f01bc-206">徵兆：執行程式資源耗用量很高，相較於在叢集上執行其他執行程式。</span><span class="sxs-lookup"><span data-stu-id="f01bc-206">Symptoms: Executor resource consumption is high compared to other executors running on the cluster.</span></span> <span data-ttu-id="f01bc-207">在該執行程式上執行的所有工作將執行速度過慢，並保存在管線中的階段執行。</span><span class="sxs-lookup"><span data-stu-id="f01bc-207">All tasks running on that executor will run slow and hold the stage execution in the pipeline.</span></span> <span data-ttu-id="f01bc-208">這些階段會稱為 「*預備阻礙*。</span><span class="sxs-lookup"><span data-stu-id="f01bc-208">Those stages are said to be *stage barriers*.</span></span>

### <a name="non-optimal-shuffle-partition-count"></a><span data-ttu-id="f01bc-209">非最佳的隨機分割區計數</span><span class="sxs-lookup"><span data-stu-id="f01bc-209">Non-optimal shuffle partition count</span></span>

<span data-ttu-id="f01bc-210">結構化串流查詢，在工作指派給一個執行程式會是叢集的資源密集型作業。</span><span class="sxs-lookup"><span data-stu-id="f01bc-210">During a structured streaming query, the assignment of a task to an executor is a resource-intensive operation for the cluster.</span></span> <span data-ttu-id="f01bc-211">如果隨機資料不是理想的大小，工作延遲數量會在輸送量和延遲造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="f01bc-211">If the shuffle data isn't the optimal size, the amount of delay for a task will negatively impact throughput and latency.</span></span> <span data-ttu-id="f01bc-212">如果有太少的分割區，叢集中的核心將會是使用量過低會導致處理效率低落。</span><span class="sxs-lookup"><span data-stu-id="f01bc-212">If there are too few partitions, the cores in the cluster will be underutilized which can result in processing inefficiency.</span></span> <span data-ttu-id="f01bc-213">相反地，如果有太多資料分割，是管理的大量負擔少量的工作。</span><span class="sxs-lookup"><span data-stu-id="f01bc-213">Conversely, if there are too many partitions, there's a great deal of management overhead for a small number of tasks.</span></span>

<span data-ttu-id="f01bc-214">使用資源消耗度量扭曲的磁碟分割和 misallocation 的叢集上執行的程式進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="f01bc-214">Use the resource consumption metrics to troubleshoot partition skewing and misallocation of executors on the cluster.</span></span> <span data-ttu-id="f01bc-215">如果資料分割已扭曲，相較於在叢集上執行其他執行程式即將提升執行程式的資源。</span><span class="sxs-lookup"><span data-stu-id="f01bc-215">If a partition is skewed, executor resources will be elevated in comparison to other executors running on the cluster.</span></span>

<span data-ttu-id="f01bc-216">例如下, 圖顯示所要跳過前兩個執行程式上使用的記憶體是 90 X 大於其他執行程式：</span><span class="sxs-lookup"><span data-stu-id="f01bc-216">For example, the following graph shows that the memory used by shuffling on the first two executors is 90X bigger than the other executors:</span></span>

![顯示百分比計量圖表](./_images/grafana-shuffle-memory.png)
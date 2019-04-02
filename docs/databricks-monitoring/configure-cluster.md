---
title: 設定傳送至 Azure 監視器計量的 Azure Databricks
description: 若要啟用監視的計量和記錄資料，Azure Log Analytics 中的 scala 程式庫
author: petertaylor9999
ms.date: 03/26/2019
ms.openlocfilehash: af6b6433f87964ac60c179ecf498e54129344126
ms.sourcegitcommit: 9854bd27fb5cf92041bbfb743d43045cd3552a69
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58503379"
---
<!-- markdownlint-disable MD040 -->

# <a name="configure-azure-databricks-to-send-metrics-to-azure-monitor"></a><span data-ttu-id="8fa43-103">設定傳送至 Azure 監視器計量的 Azure Databricks</span><span class="sxs-lookup"><span data-stu-id="8fa43-103">Configure Azure Databricks to send metrics to Azure Monitor</span></span>

<span data-ttu-id="8fa43-104">本文說明如何設定 Azure Databricks 叢集傳送至的計量[Log Analytics 工作區](/azure/azure-monitor/platform/manage-access)。</span><span class="sxs-lookup"><span data-stu-id="8fa43-104">This article shows how to configure an Azure Databricks cluster to send metrics to a [Log Analytics workspace](/azure/azure-monitor/platform/manage-access).</span></span> <span data-ttu-id="8fa43-105">它會使用[Azure Databricks 監視程式庫](https://github.com/mspnp/spark-monitoring)，這是可在 GitHub 上取得。</span><span class="sxs-lookup"><span data-stu-id="8fa43-105">It uses the [Azure Databricks Monitoring Library](https://github.com/mspnp/spark-monitoring), which is available on GitHub.</span></span> <span data-ttu-id="8fa43-106">了解 Java、 Scala 和 Maven 是 prerequisistes 建議。</span><span class="sxs-lookup"><span data-stu-id="8fa43-106">Understanding of Java, Scala, and Maven are recommended as prerequisistes.</span></span>

## <a name="about-the-azure-databricks-monitoring-library"></a><span data-ttu-id="8fa43-107">關於 Azure Databricks，監視程式庫</span><span class="sxs-lookup"><span data-stu-id="8fa43-107">About the Azure Databricks Monitoring Library</span></span>

<span data-ttu-id="8fa43-108">[GitHub 存放庫](https://github.com/mspnp/spark-monitoring)Azure Databricks 監視程式庫可讓您有下列目錄結構：</span><span class="sxs-lookup"><span data-stu-id="8fa43-108">The [GitHub repo](https://github.com/mspnp/spark-monitoring) for the Azure Databricks Monitoring Library has following directory structure:</span></span>

```
/src  
    /spark-jobs  
    /spark-listeners-loganalytics  
    /spark-listeners  
    /pom.xml  
```

<span data-ttu-id="8fa43-109">**Spark 作業**目錄是示範如何實作 Spark 應用程式計量計數器的 Spark 應用程式範例。</span><span class="sxs-lookup"><span data-stu-id="8fa43-109">The **spark-jobs** directory is a sample Spark application demonstrating how to implement a Spark application metric counter.</span></span>

<span data-ttu-id="8fa43-110">**Spark 接聽程式**目錄包含可讓服務層級的 Apache Spark 的事件傳送至 Azure Log Analytics 工作區的 Azure Databricks 的功能。</span><span class="sxs-lookup"><span data-stu-id="8fa43-110">The **spark-listeners** directory includes functionality that enables Azure Databricks to send Apache Spark events at the service level to an Azure Log Analytics workspace.</span></span> <span data-ttu-id="8fa43-111">Azure Databricks 是包含一組結構化的 Api，為您批次處理使用資料集、 Dataframe 和 SQL 資料的 Apache Spark 為基礎的服務。</span><span class="sxs-lookup"><span data-stu-id="8fa43-111">Azure Databricks is a service based on Apache Spark, which includes a set of structured APIs for batch processing data using Datasets, DataFrames, and SQL.</span></span> <span data-ttu-id="8fa43-112">使用 Apache Spark 2.0，支援已針對[結構化串流](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)、 資料流處理在處理 Api 的 Spark 的批次上建置的 API。</span><span class="sxs-lookup"><span data-stu-id="8fa43-112">With Apache Spark 2.0, support was added for [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html), a data stream processing API built on Spark's batch processing APIs.</span></span>

<span data-ttu-id="8fa43-113">**Spark-接聽程式-loganalytics**目錄包含接收 Spark 接聽程式、 DropWizard，接收和 Azure Log Analytics 工作區的用戶端。</span><span class="sxs-lookup"><span data-stu-id="8fa43-113">The **spark-listeners-loganalytics** directory includes a sink for Spark listeners, a sink for DropWizard, and a client for an Azure Log Analytics Workspace.</span></span> <span data-ttu-id="8fa43-114">這個目錄也包含 Apache Spark 應用程式記錄的 log4j 附加器。</span><span class="sxs-lookup"><span data-stu-id="8fa43-114">This directory also includes a log4j Appender for your Apache Spark application logs.</span></span>

<span data-ttu-id="8fa43-115">**Spark-接聽程式-loganalytics**並**spark 接聽程式**目錄包含建置部署至 Databricks 叢集的兩個 JAR 檔案的程式碼。</span><span class="sxs-lookup"><span data-stu-id="8fa43-115">The **spark-listeners-loganalytics** and **spark-listeners** directories contain the code for building the two JAR files that are deployed to the Databricks cluster.</span></span> <span data-ttu-id="8fa43-116">**接聽程式的 spark**目錄包含**指令碼**中 Azure Databricks 檔案系統，以包含叢集節點初始化指令碼將 JAR 複製目錄的檔案從暫存目錄執行節點。</span><span class="sxs-lookup"><span data-stu-id="8fa43-116">The **spark-listeners** directory includes a **scripts** directory that contains a cluster node initialization script to copy the JAR files from a staging directory in the Azure Databricks file system to execution nodes.</span></span>

<span data-ttu-id="8fa43-117">**Pom.xml**檔案是整個專案的主要 Maven 組建檔案。</span><span class="sxs-lookup"><span data-stu-id="8fa43-117">The **pom.xml** file is the main Maven build file for the entire project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8fa43-118">必要條件</span><span class="sxs-lookup"><span data-stu-id="8fa43-118">Prerequisites</span></span>

<span data-ttu-id="8fa43-119">若要開始，部署下列 Azure 資源：</span><span class="sxs-lookup"><span data-stu-id="8fa43-119">To get started, deploy the following Azure resources:</span></span>

- <span data-ttu-id="8fa43-120">Azure Databricks 工作區中。</span><span class="sxs-lookup"><span data-stu-id="8fa43-120">An Azure Databricks workspace.</span></span> <span data-ttu-id="8fa43-121">請參閱[快速入門：在使用 Azure 入口網站的 Azure Databricks 上執行 Spark 作業](/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。</span><span class="sxs-lookup"><span data-stu-id="8fa43-121">See [Quickstart: Run a Spark job on Azure Databricks using the Azure portal](/azure/azure-databricks/quickstart-create-databricks-workspace-portal).</span></span>
- <span data-ttu-id="8fa43-122">Log Analytics 工作區。</span><span class="sxs-lookup"><span data-stu-id="8fa43-122">A Log Analytics workspace.</span></span> <span data-ttu-id="8fa43-123">請參閱[在 Azure 入口網站中建立 Log Analytics 工作區](/azure/azure-monitor/learn/quick-create-workspace)。</span><span class="sxs-lookup"><span data-stu-id="8fa43-123">See [Create a Log Analytics workspace in the Azure portal](/azure/azure-monitor/learn/quick-create-workspace).</span></span>

<span data-ttu-id="8fa43-124">接下來，安裝[Azure Databricks CLI](https://docs.databricks.com/user-guide/dev-tools/databricks-cli.html#install-the-cli)。</span><span class="sxs-lookup"><span data-stu-id="8fa43-124">Next, install the [Azure Databricks CLI](https://docs.databricks.com/user-guide/dev-tools/databricks-cli.html#install-the-cli).</span></span> <span data-ttu-id="8fa43-125">Azure Databricks 個人存取權杖，才能使用 CLI。</span><span class="sxs-lookup"><span data-stu-id="8fa43-125">An Azure Databricks personal access token is required to use the CLI.</span></span> <span data-ttu-id="8fa43-126">如需相關指示，請參閱 <<c0> [ 產生權杖](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management)。</span><span class="sxs-lookup"><span data-stu-id="8fa43-126">For instructions, see [Generate a token](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).</span></span>

<span data-ttu-id="8fa43-127">尋找您的 Log Analytics 工作區識別碼和金鑰。</span><span class="sxs-lookup"><span data-stu-id="8fa43-127">Find your Log Analytics workspace ID and key.</span></span> <span data-ttu-id="8fa43-128">如需相關指示，請參閱 <<c0> [ 取得工作區識別碼和金鑰](/azure/azure-monitor/platform/agent-windows#obtain-workspace-id-and-key)。</span><span class="sxs-lookup"><span data-stu-id="8fa43-128">For instructions, see [Obtain workspace ID and key](/azure/azure-monitor/platform/agent-windows#obtain-workspace-id-and-key).</span></span> <span data-ttu-id="8fa43-129">當您設定在 Databricks 叢集時，您將需要這些值。</span><span class="sxs-lookup"><span data-stu-id="8fa43-129">You will need these values when you configure the Databricks cluster.</span></span>

<span data-ttu-id="8fa43-130">若要建立的監視程式庫，您需要的 Java IDE，使用下列資源：</span><span class="sxs-lookup"><span data-stu-id="8fa43-130">To build the monitoring library, you will need a Java IDE with the following resources:</span></span>

- [<span data-ttu-id="8fa43-131">Java Development Kit (JDK) 1.8 版</span><span class="sxs-lookup"><span data-stu-id="8fa43-131">Java Development Kit (JDK) version 1.8</span></span>](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
- [<span data-ttu-id="8fa43-132">Scala 語言 SDK 2.11</span><span class="sxs-lookup"><span data-stu-id="8fa43-132">Scala language SDK 2.11</span></span>](https://www.scala-lang.org/download/)
- [<span data-ttu-id="8fa43-133">Apache Maven 3.5.4</span><span class="sxs-lookup"><span data-stu-id="8fa43-133">Apache Maven 3.5.4</span></span>](http://maven.apache.org/download.cgi)

## <a name="build-the-azure-databricks-monitoring-library"></a><span data-ttu-id="8fa43-134">建立 Azure Databricks，監視程式庫</span><span class="sxs-lookup"><span data-stu-id="8fa43-134">Build the Azure Databricks Monitoring Library</span></span>

<span data-ttu-id="8fa43-135">若要建立 Azure Databricks 監視程式庫，執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="8fa43-135">To build the Azure Databricks Monitoring Library, perform the following steps:</span></span>

1. <span data-ttu-id="8fa43-136">複製、 派生或下載[GitHub 存放庫](https://github.com/mspnp/spark-monitoring)。</span><span class="sxs-lookup"><span data-stu-id="8fa43-136">Clone, fork, or download the [GitHub repository](https://github.com/mspnp/spark-monitoring).</span></span>

1. <span data-ttu-id="8fa43-137">匯入 Maven 專案物件模型檔案中， _pom.xml_，位於 **/src**到您的專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="8fa43-137">Import the Maven project object model file, _pom.xml_, located in the **/src** folder into your project.</span></span> <span data-ttu-id="8fa43-138">這將會匯入三個專案：</span><span class="sxs-lookup"><span data-stu-id="8fa43-138">This will import three projects:</span></span>

    - <span data-ttu-id="8fa43-139">spark-jobs</span><span class="sxs-lookup"><span data-stu-id="8fa43-139">spark-jobs</span></span>
    - <span data-ttu-id="8fa43-140">spark-listeners</span><span class="sxs-lookup"><span data-stu-id="8fa43-140">spark-listeners</span></span>
    - <span data-ttu-id="8fa43-141">spark-listeners-loganalytics</span><span class="sxs-lookup"><span data-stu-id="8fa43-141">spark-listeners-loganalytics</span></span>

1. <span data-ttu-id="8fa43-142">執行 Maven**封裝**中您要建置 JAR 檔案的每個這些專案的 Java IDE 的建置階段：</span><span class="sxs-lookup"><span data-stu-id="8fa43-142">Execute the Maven **package** build phase in your Java IDE to build the JAR files for each of these projects:</span></span>

    |<span data-ttu-id="8fa43-143">隨附此逐步解說的專案</span><span class="sxs-lookup"><span data-stu-id="8fa43-143">Project</span></span>| <span data-ttu-id="8fa43-144">JAR 檔案</span><span class="sxs-lookup"><span data-stu-id="8fa43-144">JAR file</span></span>|
    |-------|---------|
    |<span data-ttu-id="8fa43-145">spark-jobs</span><span class="sxs-lookup"><span data-stu-id="8fa43-145">spark-jobs</span></span>|<span data-ttu-id="8fa43-146">spark-jobs-1.0-SNAPSHOT.jar</span><span class="sxs-lookup"><span data-stu-id="8fa43-146">spark-jobs-1.0-SNAPSHOT.jar</span></span>|
    |<span data-ttu-id="8fa43-147">spark-listeners</span><span class="sxs-lookup"><span data-stu-id="8fa43-147">spark-listeners</span></span>|<span data-ttu-id="8fa43-148">spark-listeners-1.0-SNAPSHOT.jar</span><span class="sxs-lookup"><span data-stu-id="8fa43-148">spark-listeners-1.0-SNAPSHOT.jar</span></span>|
    |<span data-ttu-id="8fa43-149">spark-listeners-loganalytics</span><span class="sxs-lookup"><span data-stu-id="8fa43-149">spark-listeners-loganalytics</span></span>|<span data-ttu-id="8fa43-150">spark-listeners-loganalytics-1.0-SNAPSHOT.jar</span><span class="sxs-lookup"><span data-stu-id="8fa43-150">spark-listeners-loganalytics-1.0-SNAPSHOT.jar</span></span>|

1. <span data-ttu-id="8fa43-151">使用 Azure Databricks CLI 來建立名為**dbfs: / databricks/監視-預備**:</span><span class="sxs-lookup"><span data-stu-id="8fa43-151">Use the Azure Databricks CLI to create a directory named **dbfs:/databricks/monitoring-staging**:</span></span>  

    ```bash
    dbfs mkdirs dbfs:/databricks/monitoring-staging
    ```

1. <span data-ttu-id="8fa43-152">使用 Azure Databricks CLI 來複製 **/src/spark-listeners/scripts/listeners.sh**到先前的目錄：</span><span class="sxs-lookup"><span data-stu-id="8fa43-152">Use the Azure Databricks CLI to copy **/src/spark-listeners/scripts/listeners.sh** to the directory previously:</span></span>

    ```bash
    dbfs cp ./src/spark-listeners/scripts/listeners.sh dbfs:/databricks/monitoring-staging/listeners.sh
    ```

1. <span data-ttu-id="8fa43-153">使用 Azure Databricks CLI 來複製 **/src/spark-listeners/scripts/metrics.properties**至先前建立的目錄：</span><span class="sxs-lookup"><span data-stu-id="8fa43-153">Use the Azure Databricks CLI to copy **/src/spark-listeners/scripts/metrics.properties** to the directory created previously:</span></span>

    ```bash
    dbfs cp <local path to metrics.properties> dbfs:/databricks/monitoring-staging/metrics.properties
    ```

1. <span data-ttu-id="8fa43-154">使用 Azure Databricks CLI 來複製**spark-接聽程式-1.0-SNAPSHOT.jar**並**spark-接聽程式-loganalytics-1.0-SNAPSHOT.jar**至先前建立的目錄：</span><span class="sxs-lookup"><span data-stu-id="8fa43-154">Use the Azure Databricks CLI to copy **spark-listeners-1.0-SNAPSHOT.jar** and **spark-listeners-loganalytics-1.0-SNAPSHOT.jar** to the directory created previously:</span></span>

    ```bash
    dbfs cp ./src/spark-listeners/target/spark-listeners-1.0-SNAPSHOT.jar dbfs:/databricks/monitoring-staging/spark-listeners-1.0-SNAPSHOT.jar
    dbfs cp ./src/spark-listeners-loganalytics/target/spark-listeners-loganalytics-1.0-SNAPSHOT.jar dbfs:/databricks/monitoring-staging/spark-listeners-loganalytics-1.0-SNAPSHOT.jar
    ```

## <a name="create-and-configure-an-azure-databricks-cluster"></a><span data-ttu-id="8fa43-155">建立和設定 Azure Databricks 叢集</span><span class="sxs-lookup"><span data-stu-id="8fa43-155">Create and configure an Azure Databricks cluster</span></span>

<span data-ttu-id="8fa43-156">若要建立及設定 Azure Databricks 叢集，請遵循下列步驟：</span><span class="sxs-lookup"><span data-stu-id="8fa43-156">To create and configure an Azure Databricks cluster, follow these steps:</span></span>

1. <span data-ttu-id="8fa43-157">瀏覽至您的 Azure Databricks 工作區，在 Azure 入口網站中。</span><span class="sxs-lookup"><span data-stu-id="8fa43-157">Navigate to your Azure Databricks workspace in the Azure portal.</span></span>
1. <span data-ttu-id="8fa43-158">在 首頁 頁面中，按一下 **新的叢集**。</span><span class="sxs-lookup"><span data-stu-id="8fa43-158">On the home page, click **New Cluster**.</span></span>
1. <span data-ttu-id="8fa43-159">輸入的名稱中的叢集**叢集名稱**文字方塊。</span><span class="sxs-lookup"><span data-stu-id="8fa43-159">Enter a name for your cluster in the **Cluster Name** text box.</span></span>
1. <span data-ttu-id="8fa43-160">在  **Databricks 執行階段版本**下拉式清單中，選取**4.3**或更新版本。</span><span class="sxs-lookup"><span data-stu-id="8fa43-160">In the **Databricks Runtime Version** dropdown, select **4.3** or greater.</span></span>
1. <span data-ttu-id="8fa43-161">底下**進階選項**，按一下**Spark**  索引標籤。輸入中的下列名稱 / 值組**Spark 組態**文字方塊：</span><span class="sxs-lookup"><span data-stu-id="8fa43-161">Under **Advanced Options**, click on the **Spark** tab. Enter the following name-value pairs in the **Spark Config** text box:</span></span>

    ```
    spark.extraListeners com.databricks.backend.daemon.driver.DBCEventLoggingListener,org.apache.spark.listeners.UnifiedSparkListener
    spark.unifiedListener.sink org.apache.spark.listeners.sink.loganalytics.LogAnalyticsListenerSink
    spark.unifiedListener.logBlockUpdates false
    ```

1. <span data-ttu-id="8fa43-162">仍在 while **Spark**索引標籤上，輸入下列值中的**環境變數**文字方塊：</span><span class="sxs-lookup"><span data-stu-id="8fa43-162">While still under the **Spark** tab, enter the following values in the **Environment Variables** text box:</span></span>

    ```
    LOG_ANALYTICS_WORKSPACE_ID=[your Azure Log Analytics workspace ID]
    LOG_ANALYTICS_WORKSPACE_KEY=[your Azure Log Analytics workspace key]
    ```

    ![Databricks UI 的螢幕擷取畫面](./_images/create-cluster1.png)

1. <span data-ttu-id="8fa43-164">仍在 while**進階選項**區段中，按一下**Init 指令碼** 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="8fa43-164">While still under the **Advanced Options** section, click on the **Init Scripts** tab.</span></span>
1. <span data-ttu-id="8fa43-165">在 **目的地**下拉式清單中，選取**DBFS**。</span><span class="sxs-lookup"><span data-stu-id="8fa43-165">In the **Destination** dropdown, select **DBFS**.</span></span>
1. <span data-ttu-id="8fa43-166">底下**Init 指令碼路徑**，輸入`dbfs:/databricks/monitoring-staging/listeners.sh`。</span><span class="sxs-lookup"><span data-stu-id="8fa43-166">Under **Init Script Path**, enter `dbfs:/databricks/monitoring-staging/listeners.sh`.</span></span>
1. <span data-ttu-id="8fa43-167">按一下 [新增] 。</span><span class="sxs-lookup"><span data-stu-id="8fa43-167">Click **Add**.</span></span>

    ![Databricks UI 的螢幕擷取畫面](./_images/create-cluster2.png)

1. <span data-ttu-id="8fa43-169">按一下 **建立叢集**來建立叢集。</span><span class="sxs-lookup"><span data-stu-id="8fa43-169">Click **Create Cluster** to create the cluster.</span></span>

## <a name="view-metrics"></a><span data-ttu-id="8fa43-170">檢視計量</span><span class="sxs-lookup"><span data-stu-id="8fa43-170">View metrics</span></span>

<span data-ttu-id="8fa43-171">完成這些步驟之後，Databricks 叢集串流處理至 Azure 監視器本身之叢集的相關的一些計量資料。</span><span class="sxs-lookup"><span data-stu-id="8fa43-171">After you complete these steps, your Databricks cluster streams some metric data about the cluster itself to Azure Monitor.</span></span> <span data-ttu-id="8fa43-172">此記錄資料可在您的 Azure Log Analytics 工作區，在 「 Active |自訂記錄檔 |SparkMetric_CL 」 結構描述。</span><span class="sxs-lookup"><span data-stu-id="8fa43-172">This log data is available in your Azure Log Analytics workspace under the "Active | Custom Logs | SparkMetric_CL" schema.</span></span> <span data-ttu-id="8fa43-173">如需詳細的記錄的度量類型的詳細資訊，請參閱[計量 Core](https://metrics.dropwizard.io/4.0.0/manual/core.html) Dropwizard 文件中。</span><span class="sxs-lookup"><span data-stu-id="8fa43-173">For more information about the types of metrics that are logged, see [Metrics Core](https://metrics.dropwizard.io/4.0.0/manual/core.html) in the Dropwizard documentation.</span></span> <span data-ttu-id="8fa43-174">[類型] 欄位會寫入計量的類型，例如量測計、 計數器或長條圖。</span><span class="sxs-lookup"><span data-stu-id="8fa43-174">The metric type, such as gauge, counter, or histogram, is written to the Type field.</span></span>

<span data-ttu-id="8fa43-175">此外，Apache Spark 的層級事件和 Spark 結構化串流從您的工作要監視的度量 Azure，資料流導向的程式庫。</span><span class="sxs-lookup"><span data-stu-id="8fa43-175">In addition, the library streams Apache Spark level events and Spark Structured Streaming metrics from your jobs to Azure Monitor.</span></span> <span data-ttu-id="8fa43-176">您不需要對您的應用程式程式碼，這些事件和度量的任何變更。</span><span class="sxs-lookup"><span data-stu-id="8fa43-176">You don't need to make any changes to your application code for these events and metrics.</span></span> <span data-ttu-id="8fa43-177">Spark 結構化串流記錄檔資料會位於"Active |自訂記錄檔 |SparkListenerEvent_CL 」 結構描述。</span><span class="sxs-lookup"><span data-stu-id="8fa43-177">Spark Structured Streaming log data is available under the "Active | Custom Logs | SparkListenerEvent_CL" schema.</span></span>

![Log Analytics 工作區的螢幕擷取畫面](./_images/workspace.png)

<span data-ttu-id="8fa43-179">如需有關如何檢視記錄檔的詳細資訊，請參閱 <<c0> [ 檢視和分析 Azure 監視器中的記錄檔資料](/azure/azure-monitor/log-query/portals)。</span><span class="sxs-lookup"><span data-stu-id="8fa43-179">For more information about viewing logs, see [Viewing and analyzing log data in Azure Monitor](/azure/azure-monitor/log-query/portals).</span></span>

## <a name="next-steps"></a><span data-ttu-id="8fa43-180">後續步驟</span><span class="sxs-lookup"><span data-stu-id="8fa43-180">Next steps</span></span>

<span data-ttu-id="8fa43-181">這篇文章說明如何設定您的叢集將計量傳送至 Azure 監視器。</span><span class="sxs-lookup"><span data-stu-id="8fa43-181">This article described how to configure your cluster to send metrics to Azure Monitor.</span></span> <span data-ttu-id="8fa43-182">下一篇文章會示範如何使用 Azure Databricks 監視程式庫來傳送應用程式計量和記錄檔。</span><span class="sxs-lookup"><span data-stu-id="8fa43-182">The next article shows how to use the Azure Databricks Monitoring Library to send application metrics and logs.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="8fa43-183">將應用程式記錄檔傳送至 Azure 監視器</span><span class="sxs-lookup"><span data-stu-id="8fa43-183">Send application logs to Azure Monitor</span></span>](./application-logs.md)

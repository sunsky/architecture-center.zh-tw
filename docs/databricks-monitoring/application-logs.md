---
title: 傳送 Azure Databricks 應用程式記錄檔至 Azure 監視器
description: 如何從 Azure Databricks 的自訂記錄檔和度量傳送給 Azure 監視器
author: petertaylor9999
ms.date: 03/26/2019
ms.openlocfilehash: ea67122d7871663e8aaf42b7af0043492f63b6b1
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59639178"
---
# <a name="send-azure-databricks-application-logs-to-azure-monitor"></a><span data-ttu-id="f443f-103">傳送 Azure Databricks 應用程式記錄檔至 Azure 監視器</span><span class="sxs-lookup"><span data-stu-id="f443f-103">Send Azure Databricks application logs to Azure Monitor</span></span>

<span data-ttu-id="f443f-104">這篇文章示範如何將應用程式記錄檔和度量傳送從 Azure Databricks [Log Analytics 工作區](/azure/azure-monitor/platform/manage-access)。</span><span class="sxs-lookup"><span data-stu-id="f443f-104">This article shows how to send application logs and metrics from Azure Databricks to a [Log Analytics workspace](/azure/azure-monitor/platform/manage-access).</span></span> <span data-ttu-id="f443f-105">它會使用[Azure Databricks 監視程式庫](https://github.com/mspnp/spark-monitoring)，這是可在 GitHub 上取得。</span><span class="sxs-lookup"><span data-stu-id="f443f-105">It uses the [Azure Databricks Monitoring Library](https://github.com/mspnp/spark-monitoring), which is available on GitHub.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f443f-106">必要條件</span><span class="sxs-lookup"><span data-stu-id="f443f-106">Prerequisites</span></span>

<span data-ttu-id="f443f-107">設定您的 Azure Databricks 叢集中使用監視程式庫，如中所述[若要將計量傳送至 Azure 監視器設定 Azure Databricks][config-cluster]。</span><span class="sxs-lookup"><span data-stu-id="f443f-107">Configure your Azure Databricks cluster to use the monitoring library, as described in [Configure Azure Databricks to send metrics to Azure Monitor][config-cluster].</span></span>

> [!NOTE]
> <span data-ttu-id="f443f-108">Apache Spark 層級事件和 Spark 結構化串流度量從您的工作以 Azure 監視器，監視程式庫資料流。</span><span class="sxs-lookup"><span data-stu-id="f443f-108">The monitoring library streams Apache Spark level events and Spark Structured Streaming metrics from your jobs to Azure Monitor.</span></span> <span data-ttu-id="f443f-109">您不需要對您的應用程式程式碼，這些事件和度量的任何變更。</span><span class="sxs-lookup"><span data-stu-id="f443f-109">You don't need to make any changes to your application code for these events and metrics.</span></span>

## <a name="send-application-metrics-using-dropwizard"></a><span data-ttu-id="f443f-110">傳送使用 Dropwizard 的應用程式計量</span><span class="sxs-lookup"><span data-stu-id="f443f-110">Send application metrics using Dropwizard</span></span>

<span data-ttu-id="f443f-111">Spark 會使用 Dropwizard 度量程式庫為基礎的可設定的計量系統。</span><span class="sxs-lookup"><span data-stu-id="f443f-111">Spark uses a configurable metrics system based on the Dropwizard Metrics Library.</span></span> <span data-ttu-id="f443f-112">如需詳細資訊，請參閱 <<c0> [ 計量](https://spark.apache.org/docs/latest/monitoring.html#metrics)Spark 文件中。</span><span class="sxs-lookup"><span data-stu-id="f443f-112">For more information, see [Metrics](https://spark.apache.org/docs/latest/monitoring.html#metrics) in the Spark documentation.</span></span>

<span data-ttu-id="f443f-113">若要從 Azure Databricks 應用程式程式碼的應用程式計量傳送至 Azure 監視器中，請遵循下列步驟：</span><span class="sxs-lookup"><span data-stu-id="f443f-113">To send application metrics from Azure Databricks application code to Azure Monitor, follow these steps:</span></span>

1. <span data-ttu-id="f443f-114">建置**spark-接聽程式-loganalytics-1.0-SNAPSHOT.jar** JAR 檔案中所述[建置 Azure Databricks 監視程式庫][build-lib]。</span><span class="sxs-lookup"><span data-stu-id="f443f-114">Build the **spark-listeners-loganalytics-1.0-SNAPSHOT.jar** JAR file as described in [Build the Azure Databricks Monitoring Library][build-lib].</span></span>

1. <span data-ttu-id="f443f-115">建立 Dropwizard[量測計或計數器](https://metrics.dropwizard.io/4.0.0/manual/core.html)應用程式程式碼中。</span><span class="sxs-lookup"><span data-stu-id="f443f-115">Create Dropwizard [gauges or counters](https://metrics.dropwizard.io/4.0.0/manual/core.html) in your application code.</span></span> <span data-ttu-id="f443f-116">您可以使用`UserMetricsSystem`監視程式庫中定義的類別。</span><span class="sxs-lookup"><span data-stu-id="f443f-116">You can use the `UserMetricsSystem` class defined in the monitoring library.</span></span> <span data-ttu-id="f443f-117">下列範例會建立一個計數器名為`counter1`。</span><span class="sxs-lookup"><span data-stu-id="f443f-117">The following example creates a counter named `counter1`.</span></span>

    ```Scala
    import org.apache.spark.metrics.UserMetricsSystems
    import org.apache.spark.sql.SparkSession

    object StreamingQueryListenerSampleJob  {

      private final val METRICS_NAMESPACE = "samplejob"
      private final val COUNTER_NAME = "counter1"

      def main(args: Array[String]): Unit = {

        val spark = SparkSession
          .builder
          .getOrCreate

        val driverMetricsSystem = UserMetricsSystems
            .getMetricSystem(METRICS_NAMESPACE, builder => {
              builder.registerCounter(COUNTER_NAME)
            })

        driverMetricsSystem.counter(COUNTER_NAME).inc(5)
      }
    }
    ```

    <span data-ttu-id="f443f-118">監視程式庫包含[範例應用程式][ sample-app]示範如何使用`UserMetricsSystem`類別。</span><span class="sxs-lookup"><span data-stu-id="f443f-118">The monitoring library includes a [sample application][sample-app] that demonstrates how to use the `UserMetricsSystem` class.</span></span>

## <a name="send-application-logs-using-log4j"></a><span data-ttu-id="f443f-119">傳送使用 Log4j 的應用程式記錄檔</span><span class="sxs-lookup"><span data-stu-id="f443f-119">Send application logs using Log4j</span></span>

<span data-ttu-id="f443f-120">若要將您的 Azure Databricks 應用程式記錄檔傳送至使用 Azure Log Analytics [Log4j 附加器](https://logging.apache.org/log4j/2.x/manual/appenders.html)在媒體櫃，請遵循下列步驟：</span><span class="sxs-lookup"><span data-stu-id="f443f-120">To send your Azure Databricks application logs to Azure Log Analytics using the [Log4j appender](https://logging.apache.org/log4j/2.x/manual/appenders.html) in the library, follow these steps:</span></span>

1. <span data-ttu-id="f443f-121">建置**spark-接聽程式-loganalytics-1.0-SNAPSHOT.jar** JAR 檔案中所述[建置 Azure Databricks 監視程式庫][build-lib]。</span><span class="sxs-lookup"><span data-stu-id="f443f-121">Build the **spark-listeners-loganalytics-1.0-SNAPSHOT.jar** JAR file as described in [Build the Azure Databricks Monitoring Library][build-lib].</span></span>

1. <span data-ttu-id="f443f-122">建立**log4j.properties** [組態檔](https://logging.apache.org/log4j/2.x/manual/configuration.html)應用程式。</span><span class="sxs-lookup"><span data-stu-id="f443f-122">Create a **log4j.properties** [configuration file](https://logging.apache.org/log4j/2.x/manual/configuration.html) for your application.</span></span> <span data-ttu-id="f443f-123">包括下列的組態屬性。</span><span class="sxs-lookup"><span data-stu-id="f443f-123">Include the following configuration properties.</span></span> <span data-ttu-id="f443f-124">您的應用程式封裝名稱和記錄層級指示的位置取代：</span><span class="sxs-lookup"><span data-stu-id="f443f-124">Substitute your application package name and log level where indicated:</span></span>

    ```YAML
    log4j.appender.A1=com.microsoft.pnp.logging.loganalytics.LogAnalyticsAppender
    log4j.appender.A1.layout=com.microsoft.pnp.logging.JSONLayout
    log4j.appender.A1.layout.LocationInfo=false
    log4j.additivity.<your application package name>=false
    log4j.logger.<your application package name>=<log level>, A1
    ```

    <span data-ttu-id="f443f-125">您可以找到組態檔範例[此處][log4j.properties]。</span><span class="sxs-lookup"><span data-stu-id="f443f-125">You can find a sample configuration file [here][log4j.properties].</span></span>

1. <span data-ttu-id="f443f-126">在您的應用程式程式碼，包括**spark-接聽程式-loganalytics**專案，然後匯入`com.microsoft.pnp.logging.Log4jconfiguration`您的應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="f443f-126">In your application code, include the **spark-listeners-loganalytics** project, and import `com.microsoft.pnp.logging.Log4jconfiguration` to your application code.</span></span>

    ```Scala
    import com.microsoft.pnp.logging.Log4jConfiguration
    ```

1. <span data-ttu-id="f443f-127">設定使用 Log4j **log4j.properties**您在步驟 3 中建立的檔案：</span><span class="sxs-lookup"><span data-stu-id="f443f-127">Configure Log4j using the **log4j.properties** file you created in step 3:</span></span>

    ```Scala
    getClass.getResourceAsStream("<path to file in your JAR file>/log4j.properties")) {
          stream => {
            Log4jConfiguration.configure(stream)
          }
    }
    ```

1. <span data-ttu-id="f443f-128">在您的程式碼所需的適當層級加入 Apache Spark 記錄檔訊息。</span><span class="sxs-lookup"><span data-stu-id="f443f-128">Add Apache Spark log messages at the appropriate level in your code as required.</span></span> <span data-ttu-id="f443f-129">例如，使用`logDebug`傳送偵錯記錄檔訊息的方法。</span><span class="sxs-lookup"><span data-stu-id="f443f-129">For example, use the `logDebug` method to send a debug log message.</span></span> <span data-ttu-id="f443f-130">如需詳細資訊，請參閱 <<c0> [ 記錄][ spark-logging] Spark 文件中。</span><span class="sxs-lookup"><span data-stu-id="f443f-130">For more information, see [Logging][spark-logging] in the Spark documentation.</span></span>

    ```Scala
    logTrace("Trace message")
    logDebug("Debug message")
    logInfo("Info message")
    logWarning("Warning message")
    logError("Error message")
    ```

## <a name="run-the-sample-application"></a><span data-ttu-id="f443f-131">執行範例應用程式</span><span class="sxs-lookup"><span data-stu-id="f443f-131">Run the sample application</span></span>

<span data-ttu-id="f443f-132">監視程式庫包含[範例應用程式][ sample-app] ，示範如何將應用程式計量和應用程式記錄檔傳送至 Azure 監視器。</span><span class="sxs-lookup"><span data-stu-id="f443f-132">The monitoring library includes a [sample application][sample-app] that demonstrates how to send both application metrics and application logs to Azure Monitor.</span></span> <span data-ttu-id="f443f-133">執行範例：</span><span class="sxs-lookup"><span data-stu-id="f443f-133">To run the sample:</span></span>

1. <span data-ttu-id="f443f-134">建置**spark 作業**中所述，在監視庫中，專案[建置 Azure Databricks 監視程式庫][build-lib]。</span><span class="sxs-lookup"><span data-stu-id="f443f-134">Build the **spark-jobs** project in the monitoring library, as described in [Build the Azure Databricks Monitoring Library][build-lib].</span></span>

1. <span data-ttu-id="f443f-135">瀏覽至您的 Databricks 工作區並建立新的作業，請如所述[此處](https://docs.azuredatabricks.net/user-guide/jobs.html#create-a-job)。</span><span class="sxs-lookup"><span data-stu-id="f443f-135">Navigate to your Databricks workspace and create a new job, as described [here](https://docs.azuredatabricks.net/user-guide/jobs.html#create-a-job).</span></span>

1. <span data-ttu-id="f443f-136">在 [作業詳細資料] 頁面中，選取**設定 JAR**。</span><span class="sxs-lookup"><span data-stu-id="f443f-136">In the job detail page, select **Set JAR**.</span></span>

1. <span data-ttu-id="f443f-137">上傳 JAR 檔案從`/src/spark-jobs/target/spark-jobs-1.0-SNAPSHOT.jar`。</span><span class="sxs-lookup"><span data-stu-id="f443f-137">Upload the JAR file from `/src/spark-jobs/target/spark-jobs-1.0-SNAPSHOT.jar`.</span></span>

1. <span data-ttu-id="f443f-138">針對**主要類別**，輸入`com.microsoft.pnp.samplejob.StreamingQueryListenerSampleJob`。</span><span class="sxs-lookup"><span data-stu-id="f443f-138">For **Main class**, enter `com.microsoft.pnp.samplejob.StreamingQueryListenerSampleJob`.</span></span>

1. <span data-ttu-id="f443f-139">選取已設定為使用監視程式庫的叢集。</span><span class="sxs-lookup"><span data-stu-id="f443f-139">Select a cluster that is already configured to use the monitoring library.</span></span> <span data-ttu-id="f443f-140">請參閱[若要將計量傳送至 Azure 監視器設定 Azure Databricks][config-cluster]。</span><span class="sxs-lookup"><span data-stu-id="f443f-140">See [Configure Azure Databricks to send metrics to Azure Monitor][config-cluster].</span></span>

<span data-ttu-id="f443f-141">工作執行時，您可以檢視您的 Log Analytics 工作區中的應用程式記錄檔和度量。</span><span class="sxs-lookup"><span data-stu-id="f443f-141">When the job runs, you can view the application logs and metrics in your Log Analytics workspace.</span></span>

<span data-ttu-id="f443f-142">應用程式記錄檔會出現在 SparkLoggingEvent_CL 下：</span><span class="sxs-lookup"><span data-stu-id="f443f-142">Application logs appear under SparkLoggingEvent_CL:</span></span>

```Kusto
SparkLoggingEvent_CL | where logger_name_s contains "com.microsoft.pnp"
```

<span data-ttu-id="f443f-143">應用程式的度量會出現在 SparkMetric_CL 下：</span><span class="sxs-lookup"><span data-stu-id="f443f-143">Application metrics appear under SparkMetric_CL:</span></span>

```Kusto
SparkMetric_CL | where name_s contains "rowcounter" | limit 50
```

## <a name="next-steps"></a><span data-ttu-id="f443f-144">後續步驟</span><span class="sxs-lookup"><span data-stu-id="f443f-144">Next steps</span></span>

<span data-ttu-id="f443f-145">部署效能監控儀表板隨附此程式碼程式庫，在生產 Azure Databricks 工作負載的效能問題進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="f443f-145">Deploy the performance monitoring dashboard that accompanies this code library to troubleshoot performance issues in your production Azure Databricks workloads.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="f443f-146">使用儀表板將 Azure Databricks 計量視覺化</span><span class="sxs-lookup"><span data-stu-id="f443f-146">Use dashboards to visualize Azure Databricks metrics</span></span>](./dashboards.md)

<!-- links -->

[build-lib]: ./configure-cluster.md##build-the-azure-databricks-monitoring-library
[config-cluster]: ./configure-cluster.md
[log4j.properties]: https://github.com/mspnp/spark-monitoring/blob/master/src/spark-jobs/src/main/resources/com/microsoft/pnp/samplejob/log4j.properties
[sample-app]: https://github.com/mspnp/spark-monitoring/tree/master/src/spark-jobs
[spark-logging]: https://spark.apache.org/docs/2.3.0/api/java/org/apache/spark/internal/Logging.html
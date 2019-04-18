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
# <a name="send-azure-databricks-application-logs-to-azure-monitor"></a>傳送 Azure Databricks 應用程式記錄檔至 Azure 監視器

這篇文章示範如何將應用程式記錄檔和度量傳送從 Azure Databricks [Log Analytics 工作區](/azure/azure-monitor/platform/manage-access)。 它會使用[Azure Databricks 監視程式庫](https://github.com/mspnp/spark-monitoring)，這是可在 GitHub 上取得。

## <a name="prerequisites"></a>必要條件

設定您的 Azure Databricks 叢集中使用監視程式庫，如中所述[若要將計量傳送至 Azure 監視器設定 Azure Databricks][config-cluster]。

> [!NOTE]
> Apache Spark 層級事件和 Spark 結構化串流度量從您的工作以 Azure 監視器，監視程式庫資料流。 您不需要對您的應用程式程式碼，這些事件和度量的任何變更。

## <a name="send-application-metrics-using-dropwizard"></a>傳送使用 Dropwizard 的應用程式計量

Spark 會使用 Dropwizard 度量程式庫為基礎的可設定的計量系統。 如需詳細資訊，請參閱 <<c0> [ 計量](https://spark.apache.org/docs/latest/monitoring.html#metrics)Spark 文件中。

若要從 Azure Databricks 應用程式程式碼的應用程式計量傳送至 Azure 監視器中，請遵循下列步驟：

1. 建置**spark-接聽程式-loganalytics-1.0-SNAPSHOT.jar** JAR 檔案中所述[建置 Azure Databricks 監視程式庫][build-lib]。

1. 建立 Dropwizard[量測計或計數器](https://metrics.dropwizard.io/4.0.0/manual/core.html)應用程式程式碼中。 您可以使用`UserMetricsSystem`監視程式庫中定義的類別。 下列範例會建立一個計數器名為`counter1`。

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

    監視程式庫包含[範例應用程式][ sample-app]示範如何使用`UserMetricsSystem`類別。

## <a name="send-application-logs-using-log4j"></a>傳送使用 Log4j 的應用程式記錄檔

若要將您的 Azure Databricks 應用程式記錄檔傳送至使用 Azure Log Analytics [Log4j 附加器](https://logging.apache.org/log4j/2.x/manual/appenders.html)在媒體櫃，請遵循下列步驟：

1. 建置**spark-接聽程式-loganalytics-1.0-SNAPSHOT.jar** JAR 檔案中所述[建置 Azure Databricks 監視程式庫][build-lib]。

1. 建立**log4j.properties** [組態檔](https://logging.apache.org/log4j/2.x/manual/configuration.html)應用程式。 包括下列的組態屬性。 您的應用程式封裝名稱和記錄層級指示的位置取代：

    ```YAML
    log4j.appender.A1=com.microsoft.pnp.logging.loganalytics.LogAnalyticsAppender
    log4j.appender.A1.layout=com.microsoft.pnp.logging.JSONLayout
    log4j.appender.A1.layout.LocationInfo=false
    log4j.additivity.<your application package name>=false
    log4j.logger.<your application package name>=<log level>, A1
    ```

    您可以找到組態檔範例[此處][log4j.properties]。

1. 在您的應用程式程式碼，包括**spark-接聽程式-loganalytics**專案，然後匯入`com.microsoft.pnp.logging.Log4jconfiguration`您的應用程式程式碼。

    ```Scala
    import com.microsoft.pnp.logging.Log4jConfiguration
    ```

1. 設定使用 Log4j **log4j.properties**您在步驟 3 中建立的檔案：

    ```Scala
    getClass.getResourceAsStream("<path to file in your JAR file>/log4j.properties")) {
          stream => {
            Log4jConfiguration.configure(stream)
          }
    }
    ```

1. 在您的程式碼所需的適當層級加入 Apache Spark 記錄檔訊息。 例如，使用`logDebug`傳送偵錯記錄檔訊息的方法。 如需詳細資訊，請參閱 <<c0> [ 記錄][ spark-logging] Spark 文件中。

    ```Scala
    logTrace("Trace message")
    logDebug("Debug message")
    logInfo("Info message")
    logWarning("Warning message")
    logError("Error message")
    ```

## <a name="run-the-sample-application"></a>執行範例應用程式

監視程式庫包含[範例應用程式][ sample-app] ，示範如何將應用程式計量和應用程式記錄檔傳送至 Azure 監視器。 執行範例：

1. 建置**spark 作業**中所述，在監視庫中，專案[建置 Azure Databricks 監視程式庫][build-lib]。

1. 瀏覽至您的 Databricks 工作區並建立新的作業，請如所述[此處](https://docs.azuredatabricks.net/user-guide/jobs.html#create-a-job)。

1. 在 [作業詳細資料] 頁面中，選取**設定 JAR**。

1. 上傳 JAR 檔案從`/src/spark-jobs/target/spark-jobs-1.0-SNAPSHOT.jar`。

1. 針對**主要類別**，輸入`com.microsoft.pnp.samplejob.StreamingQueryListenerSampleJob`。

1. 選取已設定為使用監視程式庫的叢集。 請參閱[若要將計量傳送至 Azure 監視器設定 Azure Databricks][config-cluster]。

工作執行時，您可以檢視您的 Log Analytics 工作區中的應用程式記錄檔和度量。

應用程式記錄檔會出現在 SparkLoggingEvent_CL 下：

```Kusto
SparkLoggingEvent_CL | where logger_name_s contains "com.microsoft.pnp"
```

應用程式的度量會出現在 SparkMetric_CL 下：

```Kusto
SparkMetric_CL | where name_s contains "rowcounter" | limit 50
```

## <a name="next-steps"></a>後續步驟

部署效能監控儀表板隨附此程式碼程式庫，在生產 Azure Databricks 工作負載的效能問題進行疑難排解。

> [!div class="nextstepaction"]
> [使用儀表板將 Azure Databricks 計量視覺化](./dashboards.md)

<!-- links -->

[build-lib]: ./configure-cluster.md##build-the-azure-databricks-monitoring-library
[config-cluster]: ./configure-cluster.md
[log4j.properties]: https://github.com/mspnp/spark-monitoring/blob/master/src/spark-jobs/src/main/resources/com/microsoft/pnp/samplejob/log4j.properties
[sample-app]: https://github.com/mspnp/spark-monitoring/tree/master/src/spark-jobs
[spark-logging]: https://spark.apache.org/docs/2.3.0/api/java/org/apache/spark/internal/Logging.html
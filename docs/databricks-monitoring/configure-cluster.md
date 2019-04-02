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

# <a name="configure-azure-databricks-to-send-metrics-to-azure-monitor"></a>設定傳送至 Azure 監視器計量的 Azure Databricks

本文說明如何設定 Azure Databricks 叢集傳送至的計量[Log Analytics 工作區](/azure/azure-monitor/platform/manage-access)。 它會使用[Azure Databricks 監視程式庫](https://github.com/mspnp/spark-monitoring)，這是可在 GitHub 上取得。 了解 Java、 Scala 和 Maven 是 prerequisistes 建議。

## <a name="about-the-azure-databricks-monitoring-library"></a>關於 Azure Databricks，監視程式庫

[GitHub 存放庫](https://github.com/mspnp/spark-monitoring)Azure Databricks 監視程式庫可讓您有下列目錄結構：

```
/src  
    /spark-jobs  
    /spark-listeners-loganalytics  
    /spark-listeners  
    /pom.xml  
```

**Spark 作業**目錄是示範如何實作 Spark 應用程式計量計數器的 Spark 應用程式範例。

**Spark 接聽程式**目錄包含可讓服務層級的 Apache Spark 的事件傳送至 Azure Log Analytics 工作區的 Azure Databricks 的功能。 Azure Databricks 是包含一組結構化的 Api，為您批次處理使用資料集、 Dataframe 和 SQL 資料的 Apache Spark 為基礎的服務。 使用 Apache Spark 2.0，支援已針對[結構化串流](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)、 資料流處理在處理 Api 的 Spark 的批次上建置的 API。

**Spark-接聽程式-loganalytics**目錄包含接收 Spark 接聽程式、 DropWizard，接收和 Azure Log Analytics 工作區的用戶端。 這個目錄也包含 Apache Spark 應用程式記錄的 log4j 附加器。

**Spark-接聽程式-loganalytics**並**spark 接聽程式**目錄包含建置部署至 Databricks 叢集的兩個 JAR 檔案的程式碼。 **接聽程式的 spark**目錄包含**指令碼**中 Azure Databricks 檔案系統，以包含叢集節點初始化指令碼將 JAR 複製目錄的檔案從暫存目錄執行節點。

**Pom.xml**檔案是整個專案的主要 Maven 組建檔案。

## <a name="prerequisites"></a>必要條件

若要開始，部署下列 Azure 資源：

- Azure Databricks 工作區中。 請參閱[快速入門：在使用 Azure 入口網站的 Azure Databricks 上執行 Spark 作業](/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。
- Log Analytics 工作區。 請參閱[在 Azure 入口網站中建立 Log Analytics 工作區](/azure/azure-monitor/learn/quick-create-workspace)。

接下來，安裝[Azure Databricks CLI](https://docs.databricks.com/user-guide/dev-tools/databricks-cli.html#install-the-cli)。 Azure Databricks 個人存取權杖，才能使用 CLI。 如需相關指示，請參閱 <<c0> [ 產生權杖](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management)。

尋找您的 Log Analytics 工作區識別碼和金鑰。 如需相關指示，請參閱 <<c0> [ 取得工作區識別碼和金鑰](/azure/azure-monitor/platform/agent-windows#obtain-workspace-id-and-key)。 當您設定在 Databricks 叢集時，您將需要這些值。

若要建立的監視程式庫，您需要的 Java IDE，使用下列資源：

- [Java Development Kit (JDK) 1.8 版](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
- [Scala 語言 SDK 2.11](https://www.scala-lang.org/download/)
- [Apache Maven 3.5.4](http://maven.apache.org/download.cgi)

## <a name="build-the-azure-databricks-monitoring-library"></a>建立 Azure Databricks，監視程式庫

若要建立 Azure Databricks 監視程式庫，執行下列步驟：

1. 複製、 派生或下載[GitHub 存放庫](https://github.com/mspnp/spark-monitoring)。

1. 匯入 Maven 專案物件模型檔案中， _pom.xml_，位於 **/src**到您的專案資料夾。 這將會匯入三個專案：

    - spark-jobs
    - spark-listeners
    - spark-listeners-loganalytics

1. 執行 Maven**封裝**中您要建置 JAR 檔案的每個這些專案的 Java IDE 的建置階段：

    |隨附此逐步解說的專案| JAR 檔案|
    |-------|---------|
    |spark-jobs|spark-jobs-1.0-SNAPSHOT.jar|
    |spark-listeners|spark-listeners-1.0-SNAPSHOT.jar|
    |spark-listeners-loganalytics|spark-listeners-loganalytics-1.0-SNAPSHOT.jar|

1. 使用 Azure Databricks CLI 來建立名為**dbfs: / databricks/監視-預備**:  

    ```bash
    dbfs mkdirs dbfs:/databricks/monitoring-staging
    ```

1. 使用 Azure Databricks CLI 來複製 **/src/spark-listeners/scripts/listeners.sh**到先前的目錄：

    ```bash
    dbfs cp ./src/spark-listeners/scripts/listeners.sh dbfs:/databricks/monitoring-staging/listeners.sh
    ```

1. 使用 Azure Databricks CLI 來複製 **/src/spark-listeners/scripts/metrics.properties**至先前建立的目錄：

    ```bash
    dbfs cp <local path to metrics.properties> dbfs:/databricks/monitoring-staging/metrics.properties
    ```

1. 使用 Azure Databricks CLI 來複製**spark-接聽程式-1.0-SNAPSHOT.jar**並**spark-接聽程式-loganalytics-1.0-SNAPSHOT.jar**至先前建立的目錄：

    ```bash
    dbfs cp ./src/spark-listeners/target/spark-listeners-1.0-SNAPSHOT.jar dbfs:/databricks/monitoring-staging/spark-listeners-1.0-SNAPSHOT.jar
    dbfs cp ./src/spark-listeners-loganalytics/target/spark-listeners-loganalytics-1.0-SNAPSHOT.jar dbfs:/databricks/monitoring-staging/spark-listeners-loganalytics-1.0-SNAPSHOT.jar
    ```

## <a name="create-and-configure-an-azure-databricks-cluster"></a>建立和設定 Azure Databricks 叢集

若要建立及設定 Azure Databricks 叢集，請遵循下列步驟：

1. 瀏覽至您的 Azure Databricks 工作區，在 Azure 入口網站中。
1. 在 首頁 頁面中，按一下 **新的叢集**。
1. 輸入的名稱中的叢集**叢集名稱**文字方塊。
1. 在  **Databricks 執行階段版本**下拉式清單中，選取**4.3**或更新版本。
1. 底下**進階選項**，按一下**Spark**  索引標籤。輸入中的下列名稱 / 值組**Spark 組態**文字方塊：

    ```
    spark.extraListeners com.databricks.backend.daemon.driver.DBCEventLoggingListener,org.apache.spark.listeners.UnifiedSparkListener
    spark.unifiedListener.sink org.apache.spark.listeners.sink.loganalytics.LogAnalyticsListenerSink
    spark.unifiedListener.logBlockUpdates false
    ```

1. 仍在 while **Spark**索引標籤上，輸入下列值中的**環境變數**文字方塊：

    ```
    LOG_ANALYTICS_WORKSPACE_ID=[your Azure Log Analytics workspace ID]
    LOG_ANALYTICS_WORKSPACE_KEY=[your Azure Log Analytics workspace key]
    ```

    ![Databricks UI 的螢幕擷取畫面](./_images/create-cluster1.png)

1. 仍在 while**進階選項**區段中，按一下**Init 指令碼** 索引標籤。
1. 在 **目的地**下拉式清單中，選取**DBFS**。
1. 底下**Init 指令碼路徑**，輸入`dbfs:/databricks/monitoring-staging/listeners.sh`。
1. 按一下 [新增] 。

    ![Databricks UI 的螢幕擷取畫面](./_images/create-cluster2.png)

1. 按一下 **建立叢集**來建立叢集。

## <a name="view-metrics"></a>檢視計量

完成這些步驟之後，Databricks 叢集串流處理至 Azure 監視器本身之叢集的相關的一些計量資料。 此記錄資料可在您的 Azure Log Analytics 工作區，在 「 Active |自訂記錄檔 |SparkMetric_CL 」 結構描述。 如需詳細的記錄的度量類型的詳細資訊，請參閱[計量 Core](https://metrics.dropwizard.io/4.0.0/manual/core.html) Dropwizard 文件中。 [類型] 欄位會寫入計量的類型，例如量測計、 計數器或長條圖。

此外，Apache Spark 的層級事件和 Spark 結構化串流從您的工作要監視的度量 Azure，資料流導向的程式庫。 您不需要對您的應用程式程式碼，這些事件和度量的任何變更。 Spark 結構化串流記錄檔資料會位於"Active |自訂記錄檔 |SparkListenerEvent_CL 」 結構描述。

![Log Analytics 工作區的螢幕擷取畫面](./_images/workspace.png)

如需有關如何檢視記錄檔的詳細資訊，請參閱 <<c0> [ 檢視和分析 Azure 監視器中的記錄檔資料](/azure/azure-monitor/log-query/portals)。

## <a name="next-steps"></a>後續步驟

這篇文章說明如何設定您的叢集將計量傳送至 Azure 監視器。 下一篇文章會示範如何使用 Azure Databricks 監視程式庫來傳送應用程式計量和記錄檔。

> [!div class="nextstepaction"]
> [將應用程式記錄檔傳送至 Azure 監視器](./application-logs.md)

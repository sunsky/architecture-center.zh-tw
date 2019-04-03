---
title: 使用儀表板，以視覺化方式檢視 Azure Databricks 計量
description: 如何部署 Grafana 儀表板來監視在 Azure Databricks 中的效能
author: petertaylor9999
ms.date: 03/26/2019
ms.openlocfilehash: 36fcd93f6ca757e8e750d0fcbbdf0311c08560b0
ms.sourcegitcommit: 1a3cc91530d56731029ea091db1f15d41ac056af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887823"
---
# <a name="use-dashboards-to-visualize-azure-databricks-metrics"></a>使用儀表板，以視覺化方式檢視 Azure Databricks 計量

本文說明如何設定 Grafana 儀表板來監視 Azure Databricks 作業的效能問題。

[Azure Databricks](/azure/azure-databricks/)是一項快速、 功能強大且共同作業[Apache Spark](https://spark.apache.org/)– 基礎分析服務，可讓您更快速開發並部署巨量資料分析和人工智慧 (AI) 解決方案變得更加容易。 監視是作業系統在生產環境中的 Azure Databricks 工作負載的重要元件。 第一個步驟是收集計量進行分析的工作區。 在 Azure 中，是最佳的解決方案，以管理記錄檔資料[Azure 監視器](/azure/azure-monitor/)。 Azure Databricks 原本不支援將記錄資料傳送到 Azure 監視器，但[這項功能的程式庫](https://github.com/mspnp/spark-monitoring)位於[Github](https://github.com)。

此程式庫可讓 Azure Databricks 服務計量，以及查詢事件計量資料流處理的 Apache Spark 結構的記錄。 一旦您成功地部署此程式庫至 Azure Databricks 叢集，您可以進一步將部署一組[Grafana](https://granfana.com)儀表板，您可以部署到生產環境的一部分。

![儀表板的螢幕擷取畫面](./_images/dashboard-screenshot.png)

## <a name="prequisites"></a>必要條件

複製品[Github 儲存機制](https://github.com/mspnp/spark-monitoring)並[遵循的部署指示](./configure-cluster.md)建置和設定 Azure Databricks 文件庫，將記錄傳送至您的 Azure Log Analytics 工作區的 Azure 監視器記錄。

## <a name="deploy-the-azure-log-analytics-workspace"></a>部署 Azure Log Analytics 工作區

若要部署 Azure Log Analytics 工作區，請遵循下列步驟：

1. 瀏覽至`/perftools/deployment/loganalytics`目錄。
1. 部署**logAnalyticsDeploy.json** Azure Resource Manager 範本。 如需部署 Resource Manager 範本的詳細資訊，請參閱[使用 Resource Manager 範本和 Azure CLI 來部署資源][rm-cli]。 範本具有下列參數：

    * **location**：Log Analytics 工作區和儀表板會部署所在的區域。
    * **serviceTier**:以工作區定價層。 請參閱[此處][ sku]取得一份有效的值。
    * **dataRetention** （選擇性）：數天的記錄資料會保留在 Log Analytics 工作區。 預設值為 30 天。 如果定價層`Free`，資料保留期必須是 7 天。
    * **WorkspaceName** （選擇性）：工作區的名稱。 如果未指定，則範本會產生名稱。

    ```bash
    az group deployment create --resource-group <resource-group-name> --template-file logAnalyticsDeploy.json --parameters location='East US' serviceTier='Standalone'
    ```

此範本會建立工作區，而且也會建立一組預先定義的查詢所使用的儀表板。

## <a name="deploy-grafana-in-a-virtual-machine"></a>在 虛擬機器中部署 Grafana

Grafana 是開放原始碼專案，您可以部署以視覺化方式檢視儲存在您使用 Azure 監視器 Grafana 外掛程式的 Azure Log Analytics 工作區中的時間序列計量。 Grafana 的虛擬機器 (VM) 上執行，並需要儲存體帳戶、 虛擬網路，以及其他資源。 若要部署虛擬機器透過 bitnami 認證的 Grafana 映像和相關聯的資源，請遵循下列步驟：

1. 您可以使用 Azure CLI 來接受 Grafana 的 Azure Marketplace 映像條款。

    ```bash
    az vm image accept-terms --publisher bitnami --offer grafana --plan default
    ```

1. 瀏覽至`/spark-monitoring/perftools/deployment/grafana`目錄中的 GitHub 存放庫本機複本。
1. 部署**grafanaDeploy.json** Resource Manager 範本，如下所示：

    ```bash
    export DATA_SOURCE="https://raw.githubusercontent.com/mspnp/spark-monitoring/master/perftools/deployment/grafana/AzureDataSource.sh"
    az group deployment create \
        --resource-group <resource-group-name> \
        --template-file grafanaDeploy.json \
        --parameters adminPass='<vm password>' dataSource=$DATA_SOURCE
    ```

部署完成之後，虛擬機器上安裝 Grafana 的 bitnami 映像。

## <a name="update-the-grafana-password"></a>Grafana 密碼更新

安裝程序的一部分，Grafana 安裝指令碼輸出的暫時密碼**admin**使用者。 您需要登入此暫時密碼。 若要取得暫時密碼，請遵循下列步驟：  

1. 登入 Azure 管理入口網站。  
1. 選取的資源相互部署所在的資源群組。
1. 選取已安裝 Grafana 的 VM。 如果您在部署範本中使用的預設參數名稱，前面加上的 VM 名稱**sparkmonitoring-vm-grafana**。
1. 在 **支援與疑難排解**區段中，按一下**開機診斷**以開啟 開機診斷 頁面。
1. 按一下 **序列記錄檔**開機診斷 頁面上。
1. 搜尋下列字串：「 若要設定 Bitnami 應用程式密碼 」。
1. 將密碼複製到安全的位置。

接下來，遵循下列步驟變更 Grafana 系統管理員密碼：

1. 在 Azure 入口網站中，選取 VM，然後按一下**概觀**。
1. 複製公用 IP 位址。
1. 開啟網頁瀏覽器並瀏覽至下列 URL: `http://<IP addresss>:3000`。
1. 在 Grafana 登入畫面中，輸入**admin**使用者名稱，與使用 Grafana 密碼，從先前的步驟。
1. 登入之後，選取**組態**（齒輪圖示）。
1. 選取 **伺服器管理員**。
1. 在 **使用者**索引標籤上，選取**管理員**登入。
1. 更新密碼。

## <a name="create-an-azure-monitor-data-source"></a>建立 Azure 監視器資料來源

1. 建立服務主體，可讓 Grafana 來管理您的 Log Analytics 工作區的存取。 如需詳細資訊，請參閱[使用 Azure CLI 建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)

    ```bash
    az ad sp create-for-rbac --name http://<service principal name> --role "Log Analytics Reader"
    ```

1. 請注意 appId、 密碼及租用戶，此命令的輸出中的值：

    ```json
    {
        "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "displayName": "azure-cli-2019-03-27-00-33-39",
        "name": "http://<service principal name>",
        "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. 登入 Grafana 述稍早。 選取 **組態**（齒輪圖示），然後**Zdroje dat**。
1. 在 **資料來源**索引標籤上，按一下**新增資料來源**。
1. 選取  **Azure 監視器**做為資料來源類型。
1. 在 **設定**區段中，輸入中的資料來源的名稱**名稱**文字方塊中。
1. 在  **Azure 監視器 API 的詳細資料**區段中，輸入下列資訊：

    * 訂閱識別碼:您的 Azure 訂用帳戶識別碼。
    * 租用戶識別碼：來自 先前的租用戶識別碼。
    * 用戶端識別碼：稍早的"appId"值。
    * 用戶端密碼：稍早的 「 密碼 」 值。

1. 在  **Azure Log Analytics API 的詳細資料**區段中，按一下**相同的詳細資訊，為 Azure 監視器 API**核取方塊。
1. 按一下 **儲存並測試**。 如果已正確設定 Log Analytics 資料來源，則會顯示成功訊息。

## <a name="create-the-dashboard"></a>建立儀表板

在 Grafana 中建立儀表板，依照下列步驟：

1. 瀏覽至`/perftools/dashboards/grafana`目錄中的 GitHub 存放庫本機複本。
1. 執行下列指令碼：

    ```bash
    export WORKSPACE=<your Azure Log Analytics workspace ID>
    export LOGTYPE=SparkListenerEvent_CL

    sh DashGen.sh
    ```

    指令碼的輸出是名為**SparkMonitoringDash.json**。

1. 返回 Grafana 儀表板，然後選取**建立**（加號圖示）。
1. 選取 [匯入]。
1. 按一下  **.json 檔案上傳**。
1. 選取  **SparkMonitoringDash.json**步驟 2 中建立的檔案。
1. 在 **選項**下方區段**ALA**，選取稍早建立的 Azure 監視器資料來源。
1. 按一下 [匯入] 。

## <a name="visualizations-in-the-dashboards"></a>在儀表板的視覺效果

在 Azure Log Analytics 和 Grafana 儀表板包含一組的時間序列視覺效果。 每個圖形會的 Apache Spark 與相關的計量資料的時間序列圖[作業](https://spark.apache.org/docs/latest/job-scheduling.html)的作業，以及每個階段所組成的工作階段。

視覺效果如下所示：

### <a name="job-latency"></a>作業延遲

此視覺效果顯示是作業的整體效能的粗略檢視工作執行延遲。 顯示在工作執行期間從開始，到完成為止。 請注意工作開始時間不是作業提交時間相同。 延遲被以百分位數 （10%，30%、 50%，90%）工作執行以叢集識別碼和應用程式編製索引的識別碼。

### <a name="stage-latency"></a>階段延遲

視覺效果會顯示每個叢集，每個應用程式，以及每個個別的階段，每個階段的延遲。 此視覺效果可用來識別執行速度很慢的特定階段。

### <a name="task-latency"></a>工作延遲

此視覺效果會顯示工作執行延遲。 延遲被以每個叢集、 階段名稱，以及應用程式的工作執行的百分位數。

### <a name="sum-task-execution-per-host"></a>每個主機的總和工作執行

此視覺效果會顯示每個主機叢集上執行的工作執行延遲的總和。 檢視每個主機的工作執行延遲，識別具有較高的整體工作延遲比其他主機的主機。 這可能表示該工作已無效率或平均地散發給主機。

### <a name="task-metrics"></a>工作計量

此視覺效果顯示指定的工作執行的執行計量組。 這些度量資訊包含大小和資料隨機的持續時間、 序列化和還原序列化作業期間及其他項目。 針對完整的度量，檢視 [面板] 中的 Log Analytics 查詢。 此視覺效果可用於了解組成的工作和識別的資源耗用量，每個作業的作業。 圖表中的高峰表示耗費資源的作業，應予調查。

### <a name="cluster-throughput"></a>叢集輸送量

此視覺效果是由叢集和應用程式來代表針對每個叢集和應用程式的工作量編製索引的工作項目的高層級檢視。 它會顯示作業、 工作和階段完成每個叢集、 應用程式，以及在一分鐘的時間遞增的階段的數目。 

### <a name="streaming-throughputlatency"></a>串流的輸送量/延遲

此 visualzation 與相關的結構化串流查詢相關聯的度量。 這些圖表顯示每秒的輸入資料列數目和每秒處理的資料列數目。 串流度量也都被表示每個應用程式。 處理結構化串流查詢，並在視覺效果表示產生 OnQueryProgress 事件時，會傳送這些計量資料流處理的時間量的延遲，以毫秒為單位，前往執行查詢批次。

### <a name="resource-consumption-per-executor"></a>每個執行程式的資源耗用量

接下來是資源的一組儀表板顯示該特定類型和它的使用方式，每個執行程式，每個叢集上的視覺效果。 這些視覺效果可協助識別每個執行程式的資源耗用量的極端值。 例如，如果特定的執行程式的工作配置已扭曲，則資源耗用量即將提升相對於在叢集上執行其他執行程式。 這可以識別所執行程式的資源耗用量的暴增情況。

### <a name="executor-compute-time-metrics"></a>執行程式的計算時間計量

接下來是一組的顯示比例執行程式的序列化時，還原序列化時間、 CPU 時間和 Java 虛擬機器有時間來執行程式整體運算時間的儀表板的視覺效果。 這示範了以視覺化方式各佔這些四個度量的正參與將處理的整體執行程式。

### <a name="shuffle-metrics"></a>隨機計量

最終的視覺效果顯示資料隨機播放跨所有執行程式相關聯的結構化串流查詢的計量集合。 這些包括的隨機位元組讀取、 隨機寫入的位元組、 隨機記憶體和磁碟使用量在查詢中使用檔案系統的位置。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [疑難排解效能瓶頸](./performance-troubleshooting.md)

<!-- links -->

[rm-cli]: /azure/azure-resource-manager/resource-group-template-deploy-cli
[sku]: /azure/templates/Microsoft.OperationalInsights/2015-11-01-preview/workspaces#sku-object
---
title: 使用儀表板，以視覺化方式檢視 Azure Databricks 計量
description: 如何部署 Grafana 儀表板來監視在 Azure Databricks 中的效能
author: petertaylor9999
ms.date: 03/26/2019
ms.openlocfilehash: dbc04b00a781dd20c3224b5a031a8d98ddadce94
ms.sourcegitcommit: 9854bd27fb5cf92041bbfb743d43045cd3552a69
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58503382"
---
# <a name="use-dashboards-to-visualize-azure-databricks-metrics"></a><span data-ttu-id="76318-103">使用儀表板，以視覺化方式檢視 Azure Databricks 計量</span><span class="sxs-lookup"><span data-stu-id="76318-103">Use dashboards to visualize Azure Databricks metrics</span></span>

<span data-ttu-id="76318-104">[Azure Databricks](/azure/azure-databricks/)是一項快速、 功能強大且共同作業[Apache Spark](https://spark.apache.org/)– 基礎分析服務，可讓您更快速開發並部署巨量資料分析和人工智慧 (AI) 解決方案變得更加容易。</span><span class="sxs-lookup"><span data-stu-id="76318-104">[Azure Databricks](/azure/azure-databricks/) is a fast, powerful, and collaborative [Apache Spark](https://spark.apache.org/)–based analytics service that makes it easy to rapidly develop and deploy big data analytics and artificial intelligence (AI) solutions.</span></span> <span data-ttu-id="76318-105">監視是作業系統在生產環境中的 Azure Databricks 工作負載的重要元件。</span><span class="sxs-lookup"><span data-stu-id="76318-105">Monitoring is a critical component of operating Azure Databricks workloads in production.</span></span> <span data-ttu-id="76318-106">第一個步驟是收集計量進行分析的工作區。</span><span class="sxs-lookup"><span data-stu-id="76318-106">The first step is to gather metrics into a workspace for analysis.</span></span> <span data-ttu-id="76318-107">在 Azure 中，是最佳的解決方案，以管理記錄檔資料[Azure 監視器](/azure/azure-monitor/)。</span><span class="sxs-lookup"><span data-stu-id="76318-107">In Azure, the best solution for managing log data is [Azure Monitor](/azure/azure-monitor/).</span></span> <span data-ttu-id="76318-108">Azure Databricks 原本不支援將記錄資料傳送到 Azure 監視器，但[這項功能的程式庫](https://github.com/mspnp/spark-monitoring)位於[Github](https://github.com)。</span><span class="sxs-lookup"><span data-stu-id="76318-108">Azure Databricks does not natively support sending log data to Azure monitor, but a [library for this functionality](https://github.com/mspnp/spark-monitoring) is available in [Github](https://github.com).</span></span>

<span data-ttu-id="76318-109">此程式庫可讓 Azure Databricks 服務計量，以及查詢事件計量資料流處理的 Apache Spark 結構的記錄。</span><span class="sxs-lookup"><span data-stu-id="76318-109">This library enables logging of Azure Databricks service metrics as well as Apache Spark structure streaming query event metrics.</span></span> <span data-ttu-id="76318-110">一旦您成功地部署此程式庫至 Azure Databricks 叢集，您可以進一步將部署一組[Azure 監視器](/azure/azure-monitor/)或是[Grafana](https://granfana.com)儀表板，您可以部署您的生產環境的一部分環境。</span><span class="sxs-lookup"><span data-stu-id="76318-110">Once you've successfully deployed this library to an Azure Databricks cluster, you can further deploy a set of [Azure Monitor](/azure/azure-monitor/) or [Grafana](https://granfana.com) dashboards that you can deploy as part of your production environment.</span></span> <span data-ttu-id="76318-111">這份文件包含的常見效能問題，以及如何找出使用這些儀表板的討論。</span><span class="sxs-lookup"><span data-stu-id="76318-111">This document includes a discussion of the common types of performance issues and how to identify them using these dashboards.</span></span>

![儀表板的螢幕擷取畫面](./_images/dashboard-screenshot.png)

## <a name="prequisites"></a><span data-ttu-id="76318-113">必要條件</span><span class="sxs-lookup"><span data-stu-id="76318-113">Prequisites</span></span>

<span data-ttu-id="76318-114">複製品[Github 儲存機制](https://github.com/mspnp/spark-monitoring)並[遵循的部署指示](./configure-cluster.md)建置和設定 Azure Databricks 文件庫，將記錄傳送至您的 Azure Log Analytics 工作區的 Azure 監視器記錄。</span><span class="sxs-lookup"><span data-stu-id="76318-114">Clone the [Github repository](https://github.com/mspnp/spark-monitoring) and [follow the deployment instructions](./configure-cluster.md) to build and configure the Azure Monitor logging for Azure Databricks library to send logs to your Azure Log Analytics workspace.</span></span>

## <a name="deploy-the-azure-log-analytics-workspace"></a><span data-ttu-id="76318-115">部署 Azure Log Analytics 工作區</span><span class="sxs-lookup"><span data-stu-id="76318-115">Deploy the Azure Log Analytics workspace</span></span>

<span data-ttu-id="76318-116">若要部署 Azure Log Analytics 工作區，請遵循下列步驟：</span><span class="sxs-lookup"><span data-stu-id="76318-116">To deploy the Azure Log Analytics workspace, follow these steps:</span></span>

1. <span data-ttu-id="76318-117">瀏覽至`/perftools/deployment/loganalytics`目錄。</span><span class="sxs-lookup"><span data-stu-id="76318-117">Navigate to the `/perftools/deployment/loganalytics` directory.</span></span>
1. <span data-ttu-id="76318-118">部署**logAnalyticsDeploy.json** Azure Resource Manager 範本。</span><span class="sxs-lookup"><span data-stu-id="76318-118">Deploy the **logAnalyticsDeploy.json** Azure Resource Manager template.</span></span> <span data-ttu-id="76318-119">如需部署 Resource Manager 範本的詳細資訊，請參閱[使用 Resource Manager 範本和 Azure CLI 來部署資源][rm-cli]。</span><span class="sxs-lookup"><span data-stu-id="76318-119">For more information about deploying Resource Manager templates, see [Deploy resources with Resource Manager templates and Azure CLI][rm-cli].</span></span> <span data-ttu-id="76318-120">範本具有下列參數：</span><span class="sxs-lookup"><span data-stu-id="76318-120">The template has the following parameters:</span></span>

    * <span data-ttu-id="76318-121">**location**：Log Analytics 工作區和儀表板會部署所在的區域。</span><span class="sxs-lookup"><span data-stu-id="76318-121">**location**: The region where the Log Analytics workspace and dashboards are deployed.</span></span>
    * <span data-ttu-id="76318-122">**serviceTier**:以工作區定價層。</span><span class="sxs-lookup"><span data-stu-id="76318-122">**serviceTier**: Rhe workspace pricing tier.</span></span> <span data-ttu-id="76318-123">請參閱[此處][ sku]取得一份有效的值。</span><span class="sxs-lookup"><span data-stu-id="76318-123">See [here][sku] for a list of valid values.</span></span>
    * <span data-ttu-id="76318-124">**dataRetention** （選擇性）：數天的記錄資料會保留在 Log Analytics 工作區。</span><span class="sxs-lookup"><span data-stu-id="76318-124">**dataRetention** (optional): The number of days log data is retained in the Log Analytics workspace.</span></span> <span data-ttu-id="76318-125">預設值為 30 天。</span><span class="sxs-lookup"><span data-stu-id="76318-125">The default value is 30 days.</span></span> <span data-ttu-id="76318-126">如果定價層`Free`，資料保留期必須是 7 天。</span><span class="sxs-lookup"><span data-stu-id="76318-126">If the pricing tier is `Free`, the data retention must be 7 days.</span></span>
    * <span data-ttu-id="76318-127">**WorkspaceName** （選擇性）：工作區的名稱。</span><span class="sxs-lookup"><span data-stu-id="76318-127">**workspaceName** (optional): A name for the workspace.</span></span> <span data-ttu-id="76318-128">如果未指定，則範本會產生名稱。</span><span class="sxs-lookup"><span data-stu-id="76318-128">If not specified, the template generates a name.</span></span>

    ```bash
    az group deployment create --resource-group <resource-group-name> --template-file logAnalyticsDeploy.json --parameters location='East US' serviceTier='Standalone'
    ```

<span data-ttu-id="76318-129">此範本會建立工作區，而且也會建立一組預先定義的查詢所使用的儀表板。</span><span class="sxs-lookup"><span data-stu-id="76318-129">This template creates the workspace and also creates a set of predefined queries that are used by by dashboard.</span></span>

## <a name="deploy-grafana-in-a-virtual-machine"></a><span data-ttu-id="76318-130">在 虛擬機器中部署 Grafana</span><span class="sxs-lookup"><span data-stu-id="76318-130">Deploy Grafana in a virtual machine</span></span>

<span data-ttu-id="76318-131">Grafana 是開放原始碼專案，您可以部署以視覺化方式檢視儲存在您使用 Azure 監視器 Grafana 外掛程式的 Azure Log Analytics 工作區中的時間序列計量。</span><span class="sxs-lookup"><span data-stu-id="76318-131">Grafana is an open source project you can deploy to visualize the time series metrics stored in your Azure Log Analytics workspace using the Grafana plugin for Azure Monitor.</span></span> <span data-ttu-id="76318-132">Grafana 的虛擬機器 (VM) 上執行，並需要儲存體帳戶、 虛擬網路，以及其他資源。</span><span class="sxs-lookup"><span data-stu-id="76318-132">Grafana executes on a virtual machine (VM) and requires a storage account, virtual network, and other resources.</span></span> <span data-ttu-id="76318-133">若要部署虛擬機器透過 bitnami 認證的 Grafana 映像和相關聯的資源，請遵循下列步驟：</span><span class="sxs-lookup"><span data-stu-id="76318-133">To deploy a virtual machine with the bitnami certified Grafana image and associated resources, follow these steps:</span></span>

1. <span data-ttu-id="76318-134">您可以使用 Azure CLI 來接受 Grafana 的 Azure Marketplace 映像條款。</span><span class="sxs-lookup"><span data-stu-id="76318-134">Use the Azure CLI to accept the Azure Marketplace image terms for Grafana.</span></span>

    ```bash
    az vm image accept-terms --publisher bitnami --offer grafana --plan default
    ```

1. <span data-ttu-id="76318-135">瀏覽至`/spark-monitoring/perftools/deployment/grafana`目錄中的 GitHub 存放庫本機複本。</span><span class="sxs-lookup"><span data-stu-id="76318-135">Navigate to the `/spark-monitoring/perftools/deployment/grafana` directory in your local copy of the GitHub repo.</span></span>
1. <span data-ttu-id="76318-136">部署**grafanaDeploy.json** Resource Manager 範本，如下所示：</span><span class="sxs-lookup"><span data-stu-id="76318-136">Deploy the **grafanaDeploy.json** Resource Manager template as follows:</span></span>

    ```bash
    export DATA_SOURCE="https://raw.githubusercontent.com/mspnp/spark-monitoring/master/perftools/deployment/grafana/AzureDataSource.sh"
    az group deployment create \
        --resource-group <resource-group-name> \
        --template-file grafanaDeploy.json \
        --parameters adminPass='<vm password>' dataSource=$DATA_SOURCE
    ```

<span data-ttu-id="76318-137">部署完成之後，虛擬機器上安裝 Grafana 的 bitnami 映像。</span><span class="sxs-lookup"><span data-stu-id="76318-137">Once the deployment is complete, the bitnami image of Grafana is installed on the virtual machine.</span></span>

## <a name="update-the-grafana-password"></a><span data-ttu-id="76318-138">Grafana 密碼更新</span><span class="sxs-lookup"><span data-stu-id="76318-138">Update the Grafana password</span></span>

<span data-ttu-id="76318-139">安裝程序的一部分，Grafana 安裝指令碼輸出的暫時密碼**admin**使用者。</span><span class="sxs-lookup"><span data-stu-id="76318-139">As part of the setup process, the Grafana installation script outputs a temporary password for the **admin** user.</span></span> <span data-ttu-id="76318-140">您需要登入此暫時密碼。</span><span class="sxs-lookup"><span data-stu-id="76318-140">You need this temporary password to sign in.</span></span> <span data-ttu-id="76318-141">若要取得暫時密碼，請遵循下列步驟：</span><span class="sxs-lookup"><span data-stu-id="76318-141">To obtain the temporary password, follow these steps:</span></span>  

1. <span data-ttu-id="76318-142">登入 Azure 管理入口網站。</span><span class="sxs-lookup"><span data-stu-id="76318-142">Log in to the Azure portal.</span></span>  
1. <span data-ttu-id="76318-143">選取的資源相互部署所在的資源群組。</span><span class="sxs-lookup"><span data-stu-id="76318-143">Select the resource group where the resources were deployed.</span></span>
1. <span data-ttu-id="76318-144">選取已安裝 Grafana 的 VM。</span><span class="sxs-lookup"><span data-stu-id="76318-144">Select the VM where Grafana was installed.</span></span> <span data-ttu-id="76318-145">如果您在部署範本中使用的預設參數名稱，前面加上的 VM 名稱**sparkmonitoring-vm-grafana**。</span><span class="sxs-lookup"><span data-stu-id="76318-145">If you used the default parameter name in the deployment template, the VM name is prefaced with **sparkmonitoring-vm-grafana**.</span></span>
1. <span data-ttu-id="76318-146">在 **支援與疑難排解**區段中，按一下**開機診斷**以開啟 開機診斷 頁面。</span><span class="sxs-lookup"><span data-stu-id="76318-146">In the **Support + troubleshooting** section, click **Boot diagnostics** to open the boot diagnostics page.</span></span>
1. <span data-ttu-id="76318-147">按一下 **序列記錄檔**開機診斷 頁面上。</span><span class="sxs-lookup"><span data-stu-id="76318-147">Click **Serial log** on the boot diagnostics page.</span></span>
1. <span data-ttu-id="76318-148">搜尋下列字串：「 若要設定 Bitnami 應用程式密碼 」。</span><span class="sxs-lookup"><span data-stu-id="76318-148">Search for the following string: "Setting Bitnami application password to".</span></span>
1. <span data-ttu-id="76318-149">將密碼複製到安全的位置。</span><span class="sxs-lookup"><span data-stu-id="76318-149">Copy the password to a safe location.</span></span>

<span data-ttu-id="76318-150">接下來，遵循下列步驟變更 Grafana 系統管理員密碼：</span><span class="sxs-lookup"><span data-stu-id="76318-150">Next, change the Grafana administrator password by following these steps:</span></span>

1. <span data-ttu-id="76318-151">在 Azure 入口網站中，選取 VM，然後按一下**概觀**。</span><span class="sxs-lookup"><span data-stu-id="76318-151">In the Azure portal, select the VM and click **Overview**.</span></span>
1. <span data-ttu-id="76318-152">複製公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="76318-152">Copy the public IP address.</span></span>
1. <span data-ttu-id="76318-153">開啟網頁瀏覽器並瀏覽至下列 URL: `http://<IP addresss>:3000`。</span><span class="sxs-lookup"><span data-stu-id="76318-153">Open a web browser and navigate to the following URL: `http://<IP addresss>:3000`.</span></span>
1. <span data-ttu-id="76318-154">在 Grafana 登入畫面中，輸入**admin**使用者名稱，與使用 Grafana 密碼，從先前的步驟。</span><span class="sxs-lookup"><span data-stu-id="76318-154">At the Grafana log in screen, enter **admin** for the user name, and use the Grafana password from the previous steps.</span></span>
1. <span data-ttu-id="76318-155">登入之後，選取**組態**（齒輪圖示）。</span><span class="sxs-lookup"><span data-stu-id="76318-155">Once logged in, select **Configuration** (the gear icon).</span></span>
1. <span data-ttu-id="76318-156">選取 **伺服器管理員**。</span><span class="sxs-lookup"><span data-stu-id="76318-156">Select **Server Admin**.</span></span>
1. <span data-ttu-id="76318-157">在 **使用者**索引標籤上，選取**管理員**登入。</span><span class="sxs-lookup"><span data-stu-id="76318-157">On the **Users** tab, select the **admin** login.</span></span>
1. <span data-ttu-id="76318-158">更新密碼。</span><span class="sxs-lookup"><span data-stu-id="76318-158">Update the password.</span></span>

## <a name="create-an-azure-monitor-data-source"></a><span data-ttu-id="76318-159">建立 Azure 監視器資料來源</span><span class="sxs-lookup"><span data-stu-id="76318-159">Create an Azure Monitor data source</span></span>

1. <span data-ttu-id="76318-160">建立服務主體，可讓 Grafana 來管理您的 Log Analytics 工作區的存取。</span><span class="sxs-lookup"><span data-stu-id="76318-160">Create a service principal that allows Grafana to manage access to your Log Analytics workspace.</span></span> <span data-ttu-id="76318-161">如需詳細資訊，請參閱[使用 Azure CLI 建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)</span><span class="sxs-lookup"><span data-stu-id="76318-161">For more information, see [Create an Azure service principal with Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)</span></span>

    ```bash
    az ad sp create-for-rbac --name http://<service principal name> --role "Log Analytics Reader"
    ```

1. <span data-ttu-id="76318-162">請注意 appId、 密碼及租用戶，此命令的輸出中的值：</span><span class="sxs-lookup"><span data-stu-id="76318-162">Note the values for appId, password, and tenant in the output from this command:</span></span>

    ```json
    {
        "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "displayName": "azure-cli-2019-03-27-00-33-39",
        "name": "http://<service principal name>",
        "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. <span data-ttu-id="76318-163">登入 Grafana 述稍早。</span><span class="sxs-lookup"><span data-stu-id="76318-163">Log into Grafana as described earlier.</span></span> <span data-ttu-id="76318-164">選取 **組態**（齒輪圖示），然後**Zdroje dat**。</span><span class="sxs-lookup"><span data-stu-id="76318-164">Select **Configuration** (the gear icon) and then **Data Sources**.</span></span>
1. <span data-ttu-id="76318-165">在 **資料來源**索引標籤上，按一下**新增資料來源**。</span><span class="sxs-lookup"><span data-stu-id="76318-165">In the **Data Sources** tab, click **Add data source**.</span></span>
1. <span data-ttu-id="76318-166">選取  **Azure 監視器**做為資料來源類型。</span><span class="sxs-lookup"><span data-stu-id="76318-166">Select **Azure Monitor** as the data source type.</span></span>
1. <span data-ttu-id="76318-167">在 **設定**區段中，輸入中的資料來源的名稱**名稱**文字方塊中。</span><span class="sxs-lookup"><span data-stu-id="76318-167">In the **Settings** section, enter a name for the data source in the **Name** textbox.</span></span>
1. <span data-ttu-id="76318-168">在  **Azure 監視器 API 的詳細資料**區段中，輸入下列資訊：</span><span class="sxs-lookup"><span data-stu-id="76318-168">In the **Azure Monitor API Details** section, enter the following information:</span></span>

    * <span data-ttu-id="76318-169">訂閱識別碼:您的 Azure 訂用帳戶識別碼。</span><span class="sxs-lookup"><span data-stu-id="76318-169">Subscription Id: Your Azure subscription ID.</span></span>
    * <span data-ttu-id="76318-170">租用戶識別碼：來自 先前的租用戶識別碼。</span><span class="sxs-lookup"><span data-stu-id="76318-170">Tenant Id: The tenant ID from earlier.</span></span>
    * <span data-ttu-id="76318-171">用戶端識別碼：稍早的"appId"值。</span><span class="sxs-lookup"><span data-stu-id="76318-171">Client Id: The value of "appId" from earlier.</span></span>
    * <span data-ttu-id="76318-172">用戶端密碼：稍早的 「 密碼 」 值。</span><span class="sxs-lookup"><span data-stu-id="76318-172">Client Secret: The value of "password" from earlier.</span></span>

1. <span data-ttu-id="76318-173">在  **Azure Log Analytics API 的詳細資料**區段中，按一下**相同的詳細資訊，為 Azure 監視器 API**核取方塊。</span><span class="sxs-lookup"><span data-stu-id="76318-173">In the **Azure Log Analytics API Details** section, check the **Same Details as Azure Monitor API** checkbox.</span></span>
1. <span data-ttu-id="76318-174">按一下 **儲存並測試**。</span><span class="sxs-lookup"><span data-stu-id="76318-174">Click **Save & Test**.</span></span> <span data-ttu-id="76318-175">如果已正確設定 Log Analytics 資料來源，則會顯示成功訊息。</span><span class="sxs-lookup"><span data-stu-id="76318-175">If the Log Analytics data source is correctly configured, a success message is displayed.</span></span>

## <a name="create-the-dashboard"></a><span data-ttu-id="76318-176">建立儀表板</span><span class="sxs-lookup"><span data-stu-id="76318-176">Create the dashboard</span></span>

<span data-ttu-id="76318-177">在 Grafana 中建立儀表板，依照下列步驟：</span><span class="sxs-lookup"><span data-stu-id="76318-177">Create the dashboards in Grafana by following these steps:</span></span>

1. <span data-ttu-id="76318-178">瀏覽至`/perftools/dashboards/grafana`目錄中的 GitHub 存放庫本機複本。</span><span class="sxs-lookup"><span data-stu-id="76318-178">Navigate to the `/perftools/dashboards/grafana` directory in your local copy of the GitHub repo.</span></span>
1. <span data-ttu-id="76318-179">執行下列指令碼：</span><span class="sxs-lookup"><span data-stu-id="76318-179">Run the following script:</span></span>

    ```bash
    export WORKSPACE=<your Azure Log Analytics workspace ID>
    export LOGTYPE=SparkListenerEvent_CL

    sh DashGen.sh
    ```

    <span data-ttu-id="76318-180">指令碼的輸出是名為**SparkMonitoringDash.json**。</span><span class="sxs-lookup"><span data-stu-id="76318-180">The output from the script is a file named **SparkMonitoringDash.json**.</span></span>

1. <span data-ttu-id="76318-181">返回 Grafana 儀表板，然後選取**建立**（加號圖示）。</span><span class="sxs-lookup"><span data-stu-id="76318-181">Return to the Grafana dashboard and select **Create** (the plus icon).</span></span>
1. <span data-ttu-id="76318-182">選取 [匯入]。</span><span class="sxs-lookup"><span data-stu-id="76318-182">Select **Import**.</span></span>
1. <span data-ttu-id="76318-183">按一下  **.json 檔案上傳**。</span><span class="sxs-lookup"><span data-stu-id="76318-183">Click **Upload .json File**.</span></span>
1. <span data-ttu-id="76318-184">選取  **SparkMonitoringDash.json**步驟 2 中建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="76318-184">Select the **SparkMonitoringDash.json** file created in step 2.</span></span>
1. <span data-ttu-id="76318-185">在 **選項**下方區段**ALA**，選取稍早建立的 Azure 監視器資料來源。</span><span class="sxs-lookup"><span data-stu-id="76318-185">In the **Options** section, under **ALA**, select the Azure Monitor data source created earlier.</span></span>
1. <span data-ttu-id="76318-186">按一下 [匯入] 。</span><span class="sxs-lookup"><span data-stu-id="76318-186">Click **Import**.</span></span>

## <a name="visualizations-in-the-dashboards"></a><span data-ttu-id="76318-187">在儀表板的視覺效果</span><span class="sxs-lookup"><span data-stu-id="76318-187">Visualizations in the dashboards</span></span>

<span data-ttu-id="76318-188">在 Azure Log Analytics 和 Grafana 儀表板包含一組的時間序列視覺效果。</span><span class="sxs-lookup"><span data-stu-id="76318-188">Both the Azure Log Analytics and Grafana dashboards include a set of time-series visualizations.</span></span> <span data-ttu-id="76318-189">每個圖形會的 Apache Spark 與相關的計量資料的時間序列圖[作業](https://spark.apache.org/docs/latest/job-scheduling.html)的作業，以及每個階段所組成的工作階段。</span><span class="sxs-lookup"><span data-stu-id="76318-189">Each graph is time-series plot of metric data related to an Apache Spark [job](https://spark.apache.org/docs/latest/job-scheduling.html), stages of the job, and tasks that make up each stage.</span></span>

<span data-ttu-id="76318-190">視覺效果如下所示：</span><span class="sxs-lookup"><span data-stu-id="76318-190">The visualizations are as follows:</span></span>

### <a name="job-latency"></a><span data-ttu-id="76318-191">作業延遲</span><span class="sxs-lookup"><span data-stu-id="76318-191">Job latency</span></span>

<span data-ttu-id="76318-192">此視覺效果顯示是作業的整體效能的粗略檢視工作執行延遲。</span><span class="sxs-lookup"><span data-stu-id="76318-192">This visualization shows execution latency for a job, which is a coarse view on the overall peformance of a job.</span></span> <span data-ttu-id="76318-193">顯示在工作執行期間從開始，到完成為止。</span><span class="sxs-lookup"><span data-stu-id="76318-193">Displays the job execution duration from start to completion.</span></span> <span data-ttu-id="76318-194">請注意工作開始時間不是作業提交時間相同。</span><span class="sxs-lookup"><span data-stu-id="76318-194">Note that the job start time is not the same as the job submission time.</span></span> <span data-ttu-id="76318-195">延遲被以百分位數 （10%，30%、 50%，90%）工作執行以叢集識別碼和應用程式編製索引的識別碼。</span><span class="sxs-lookup"><span data-stu-id="76318-195">Latency is represented as percentiles (10%, 30%, 50%, 90%) of job execution indexed by cluster ID and application ID.</span></span>

### <a name="stage-latency"></a><span data-ttu-id="76318-196">階段延遲</span><span class="sxs-lookup"><span data-stu-id="76318-196">Stage latency</span></span>

<span data-ttu-id="76318-197">視覺效果會顯示每個叢集，每個應用程式，以及每個個別的階段，每個階段的延遲。</span><span class="sxs-lookup"><span data-stu-id="76318-197">The visualization shows the latency of each stage per cluster, per application, and per individual stage.</span></span> <span data-ttu-id="76318-198">此視覺效果可用來識別執行速度很慢的特定階段。</span><span class="sxs-lookup"><span data-stu-id="76318-198">This visualization is useful for identifying a particular stage that is running slowly.</span></span>

### <a name="task-latency"></a><span data-ttu-id="76318-199">工作延遲</span><span class="sxs-lookup"><span data-stu-id="76318-199">Task latency</span></span>

<span data-ttu-id="76318-200">此視覺效果會顯示工作執行延遲。</span><span class="sxs-lookup"><span data-stu-id="76318-200">This visualization shows task execution latency.</span></span> <span data-ttu-id="76318-201">延遲被以每個叢集、 階段名稱，以及應用程式的工作執行的百分位數。</span><span class="sxs-lookup"><span data-stu-id="76318-201">Latency is represented as a percentile of task execution per cluster, stage name, and application.</span></span>

### <a name="sum-task-execution-per-host"></a><span data-ttu-id="76318-202">每個主機的總和工作執行</span><span class="sxs-lookup"><span data-stu-id="76318-202">Sum Task Execution per host</span></span>

<span data-ttu-id="76318-203">此視覺效果會顯示每個主機叢集上執行的工作執行延遲的總和。</span><span class="sxs-lookup"><span data-stu-id="76318-203">This visualization shows the sum of task execution latency per host running on a cluster.</span></span> <span data-ttu-id="76318-204">檢視每個主機的工作執行延遲，識別具有較高的整體工作延遲比其他主機的主機。</span><span class="sxs-lookup"><span data-stu-id="76318-204">Viewing task execution latency per host identifies hosts that have much higher overall task latency than other hosts.</span></span> <span data-ttu-id="76318-205">這可能表示該工作已無效率或平均地散發給主機。</span><span class="sxs-lookup"><span data-stu-id="76318-205">This may mean that tasks have been inefficiently or unevenly distributed to hosts.</span></span>

### <a name="task-metrics"></a><span data-ttu-id="76318-206">工作計量</span><span class="sxs-lookup"><span data-stu-id="76318-206">Task metrics</span></span>

<span data-ttu-id="76318-207">此視覺效果顯示指定的工作執行的執行計量組。</span><span class="sxs-lookup"><span data-stu-id="76318-207">This visualization shows a set of the execution metrics for a given task's execution.</span></span> <span data-ttu-id="76318-208">這些度量資訊包含大小和資料隨機的持續時間、 序列化和還原序列化作業期間及其他項目。</span><span class="sxs-lookup"><span data-stu-id="76318-208">These metrics include the size and duration of a data shuffle, duration of serialization and deserialization operations, and others.</span></span> <span data-ttu-id="76318-209">針對完整的度量，檢視 [面板] 中的 Log Analytics 查詢。</span><span class="sxs-lookup"><span data-stu-id="76318-209">For the full set of metrics, view the Log Analytics query for the panel.</span></span> <span data-ttu-id="76318-210">此視覺效果可用於了解組成的工作和識別的資源耗用量，每個作業的作業。</span><span class="sxs-lookup"><span data-stu-id="76318-210">This visualization is useful for understanding the operations that make up a task and identifying resource consumption of each operation.</span></span> <span data-ttu-id="76318-211">圖表中的高峰表示耗費資源的作業，應予調查。</span><span class="sxs-lookup"><span data-stu-id="76318-211">Spikes in the graph represent costly operations that should be investigated.</span></span>

### <a name="cluster-throughput"></a><span data-ttu-id="76318-212">叢集輸送量</span><span class="sxs-lookup"><span data-stu-id="76318-212">Cluster throughput</span></span>

<span data-ttu-id="76318-213">此視覺效果是由叢集和應用程式來代表針對每個叢集和應用程式的工作量編製索引的工作項目的高層級檢視。</span><span class="sxs-lookup"><span data-stu-id="76318-213">This visualization is a high level view of work items indexed by cluster and application to represent the amount of work done per cluster and application.</span></span> <span data-ttu-id="76318-214">它會顯示作業、 工作和階段完成每個叢集、 應用程式，以及在一分鐘的時間遞增的階段的數目。</span><span class="sxs-lookup"><span data-stu-id="76318-214">It shows the number of jobs, tasks, and stages completed per cluster, application, and stage in one minute increments.</span></span> 

### <a name="streaming-throughputlatency"></a><span data-ttu-id="76318-215">串流的輸送量/延遲</span><span class="sxs-lookup"><span data-stu-id="76318-215">Streaming Throughput/Latency</span></span>

<span data-ttu-id="76318-216">此 visualzation 與相關的結構化串流查詢相關聯的度量。</span><span class="sxs-lookup"><span data-stu-id="76318-216">This visualzation is related to the metrics associated with a structured streaming query.</span></span> <span data-ttu-id="76318-217">這些圖表顯示每秒的輸入資料列數目和每秒處理的資料列數目。</span><span class="sxs-lookup"><span data-stu-id="76318-217">The graphs shows the number of input rows per second and the number of rows processed per second.</span></span> <span data-ttu-id="76318-218">串流度量也都被表示每個應用程式。</span><span class="sxs-lookup"><span data-stu-id="76318-218">The streaming metrics are also represented per application.</span></span> <span data-ttu-id="76318-219">處理結構化串流查詢，並在視覺效果表示產生 OnQueryProgress 事件時，會傳送這些計量資料流處理的時間量的延遲，以毫秒為單位，前往執行查詢批次。</span><span class="sxs-lookup"><span data-stu-id="76318-219">These metrics are sent when the OnQueryProgress event is generated as the structured streaming query is processed and the visualization represents streaming latency as the amount of time, in milliseconds, taken to execute a query batch.</span></span>

### <a name="resource-consumption-per-executor"></a><span data-ttu-id="76318-220">每個執行程式的資源耗用量</span><span class="sxs-lookup"><span data-stu-id="76318-220">Resource consumption per executor</span></span>

<span data-ttu-id="76318-221">接下來是資源的一組儀表板顯示該特定類型和它的使用方式，每個執行程式，每個叢集上的視覺效果。</span><span class="sxs-lookup"><span data-stu-id="76318-221">Next is a set of visualizations for the dashboard show the particular type of resource and how it is consumed per executor on each cluster.</span></span> <span data-ttu-id="76318-222">這些視覺效果可協助識別每個執行程式的資源耗用量的極端值。</span><span class="sxs-lookup"><span data-stu-id="76318-222">These visualizations help identify outliers in resource consumption per executor.</span></span> <span data-ttu-id="76318-223">例如，如果特定的執行程式的工作配置已扭曲，則資源耗用量即將提升相對於在叢集上執行其他執行程式。</span><span class="sxs-lookup"><span data-stu-id="76318-223">For example, if the work allocation for a particular executor is skewed, resource consumption will be elevated in relation to other executors running on the cluster.</span></span> <span data-ttu-id="76318-224">這可以識別所執行程式的資源耗用量的暴增情況。</span><span class="sxs-lookup"><span data-stu-id="76318-224">This can be identified by spikes in the resource consumption for an executor.</span></span>

### <a name="executor-compute-time-metrics"></a><span data-ttu-id="76318-225">執行程式的計算時間計量</span><span class="sxs-lookup"><span data-stu-id="76318-225">Executor compute time metrics</span></span>

<span data-ttu-id="76318-226">接下來是一組的顯示比例執行程式的序列化時，還原序列化時間、 CPU 時間和 Java 虛擬機器有時間來執行程式整體運算時間的儀表板的視覺效果。</span><span class="sxs-lookup"><span data-stu-id="76318-226">Next is a set of visualizations for the dashboard that show the ratio of executor serialize time, deserialize time, CPU time, and Java virtual machine time to overall executor compute time.</span></span> <span data-ttu-id="76318-227">這示範了以視覺化方式各佔這些四個度量的正參與將處理的整體執行程式。</span><span class="sxs-lookup"><span data-stu-id="76318-227">This demonstrates visually how much each of these four metrics are contributing to overall executor processing.</span></span>

### <a name="shuffle-metrics"></a><span data-ttu-id="76318-228">隨機計量</span><span class="sxs-lookup"><span data-stu-id="76318-228">Shuffle metrics</span></span>

<span data-ttu-id="76318-229">最終的視覺效果顯示資料隨機播放跨所有執行程式相關聯的結構化串流查詢的計量集合。</span><span class="sxs-lookup"><span data-stu-id="76318-229">The final set of visualizations show the data shuffle metrics associated with a structured streaming query across all executors.</span></span> <span data-ttu-id="76318-230">這些包括的隨機位元組讀取、 隨機寫入的位元組、 隨機記憶體和磁碟使用量在查詢中使用檔案系統的位置。</span><span class="sxs-lookup"><span data-stu-id="76318-230">These include shuffle bytes read, shuffle bytes written, shuffle memory, and disk usage in queries where the file system is used.</span></span>

<!-- links -->

[rm-cli]: /azure/azure-resource-manager/resource-group-template-deploy-cli
[sku]: /azure/templates/Microsoft.OperationalInsights/2015-11-01-preview/workspaces#sku-object
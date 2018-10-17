---
title: 具 SQL 資料倉儲和 Azure Data Factory 的自動化 Enterprise BI
description: 使用 Azure Data Factory 將 Azure 上的 ELT 工作流程自動化
author: MikeWasson
ms.date: 07/01/2018
ms.openlocfilehash: f004c02da93335e74b07b9720236832ad7f744db
ms.sourcegitcommit: 62945777e519d650159f0f963a2489b6bb6ce094
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2018
ms.locfileid: "48876897"
---
# <a name="automated-enterprise-bi-with-sql-data-warehouse-and-azure-data-factory"></a>具 SQL 資料倉儲和 Azure Data Factory 的自動化 Enterprise BI

此參考架構示範如何在 [ELT](../../data-guide/relational-data/etl.md#extract-load-and-transform-elt) (擷取-載入-轉換) 管線中執行累加式載入。 該架構會使用 Azure Data Factory 將 ELT 管線自動化。 管線會以累加方式，將最新的 OLTP 資料從內部部署 SQL Server 資料庫載入 SQL 資料倉儲中。 交易資料會轉換成表格式模型以供分析。 [**部署這個解決方案**。](#deploy-the-solution)

![](./images/enterprise-bi-sqldw-adf.png)

此架構是基於[具 SQL 資料倉儲的 Enterprise BI](./enterprise-bi-sqldw.md)，但增加了一些對企業資料倉儲情況很重要的功能。

-   使用 Data Factory 將管線自動化。
-   累加式載入。
-   可整合多個資料來源。
-   可載入二進位資料，立如地理空間資料與影像。

## <a name="architecture"></a>架構

此架構由下列元件組成。

### <a name="data-sources"></a>資料來源

**內部部署的 SQL Server**。 來源資料位於內部部署的 SQL Server 資料庫中。 為了模擬內部部署環境，此架構的部署指令碼會在 Azure 中佈建已安裝 SQL Server 的虛擬機器。 系統會使用 [Wide World Importers OLTP sample database][wwi] 作為來源資料庫。

**外部資料**。 資料倉儲常見的一種情況是整合多個資料來源。 此參考架構會載入外部資料集，其中包含依年度的城市人口，且會將資料集與 OLTP 資料庫中的資料整合。 您可以使用這項資料以進行深入解析，例如「每個區域中的銷售成長率是符合或超過人口成長率？」

### <a name="ingestion-and-data-storage"></a>擷取和資料儲存體

**Blob 儲存體**。 Blob 儲存體會作為來源資料在載入至 SQL 資料倉儲之前的臨時區域。

**Azure SQL 資料倉儲**。 [SQL 資料倉儲](/azure/sql-data-warehouse/)是為了對大型資料執行分析而設計的分散式系統。 它支援大量平行處理 (MPP)，因而適合用來執行高效能分析。 

**Azure Data Factory**。 [Data Factory][adf] 是受控服務，可協調並自動化資料移動和資料轉換。 在此架構中，該服務會協調 ELT 程序的各個階段。

### <a name="analysis-and-reporting"></a>分析和報告

**Azure Analysis Services**。 [Analysis Services](/azure/analysis-services/) 是完全受控的服務，可提供資料模型功能。 語意模型會載入 Analysis Services 中。

**Power BI**。 Power BI 是一套用來分析資料以產生商業見解的商務分析工具。 在此架構中，它會查詢儲存在 Analysis Services 中的語意模型。

### <a name="authentication"></a>驗證

**Azure Active Directory** (Azure AD) 會驗證透過 Power BI 連線至 Analysis Services 伺服器的使用者。

Data Factory 也可以透過使用服務主體或受控服務識別 (MSI)，利用 Azure AD 來驗證 SQL 資料倉儲。 為了簡單起見，範例部署會使用 SQL Server 驗證。

## <a name="data-pipeline"></a>Data Pipeline

在 [Azure Data Factory][adf] 中，管線是在此情況下，用來協調 &mdash; 工作的活動邏輯群組，會將資料載入 SQL 資料倉儲中，並加以轉換。 

此參考架構會定義執行一連串的子管線的主要管線。 每條子管線都會將資料載入一個或多個資料倉儲資料表中。

![](./images/adf-pipeline.png)

## <a name="incremental-loading"></a>累加式載入

當您執行自動化的 ETL 或 ELT 程序時，只載入從上次執行之後變更的資料最有效率。 這就叫做「累加式載入」，而非載入所有資料的完整載入。 若要執行累加式載入，您需要有識別哪些資料已經變更的方式。 最常見的方法是使用「上限標準」值，這表示要追蹤來源資料表中某些資料行 (可能是日期時間資料行，或唯一的整數資料行) 的最新值。 

從 SQL Server 2016 開始，您可以使用[時態表](/sql/relational-databases/tables/temporal-tables)。 這些是由系統建立版本的資料表，會保留資料變更的完整歷程記錄。 資料庫引擎會將每一項變更的歷程記錄，自動記錄在個別的歷程記錄資料表中。 您可以將 FOR SYSTEM_TIME 子句新增到查詢中，來查詢歷程記錄資料。 就內部而言，資料庫引擎會查詢記錄資料表，但這對應用程式而言是透明的。 

> [!NOTE]
> 對於舊版的 SQL Server，您可以使用[異動資料擷取](/sql/relational-databases/track-changes/about-change-data-capture-sql-server)(CDC)。 這個方法比起時態表較不方便，因為您需要查詢個別變更資料表，而且此方法不是按照時間戳記，而是按照記錄序號來追蹤變更。 

時態表對於維度資料很有用，因為後者會隨著時間而變更。 事實資料表通常代表固定的交易 (例如銷售)，在此情況下保留由系統建立版本的歷程記錄沒有任何意義。 因此，交易通常會有代表交易日期的資料行，可用來當作上限標準值。 例如，在 Wide World Importers OLTP 資料庫中，Sales.Invoices 和 Sales.InvoiceLines 資料表有 `LastEditedWhen` 欄位，預設值為 `sysdatetime()`。 

以下是 ELT 管線的一般流程：

1. 針對來源資料庫中每個資料表，追蹤上次 ELT 作業執行的截止時間。 將這項資訊儲存在資料倉儲中。 (在初始設定時，所有時間都設為 '1-1-1900'。)

2. 在資料匯出步驟期間，會將截止時間當作參數，傳遞至來源資料庫中的一組預存程序。 這些預存程序會查詢截止時間之後所變更或建立的任何記錄。 「銷售」事實資料表會使用 `LastEditedWhen` 資料行。 維度資料則會使用由系統建立版本的時態表。

3. 資料移轉完成時，會更新儲存截止時間的資料表。

記錄每次 ELT 執行的*歷程*也很有幫助。 對於給定的記錄，歷程會將該記錄與產生資料的 ELT 執行產生關聯。 每次 ELT 執行時，都會針對每份資料表建立新的歷程記錄，顯示開始和結束載入時間。 每筆記錄的歷程索引鍵都會儲存在維度資料表和事實資料表中。

![](./images/city-dimension-table.png)

新批次的資料載入至倉儲之後，會重新整理 Analysis Services 表格式模型。 請參閱[使用 REST API 進行非同步重新整理](/azure/analysis-services/analysis-services-async-refresh)。

## <a name="data-cleansing"></a>資料清理

資料清理應該是 ELT 程序的一部分。 在此參考架構中，有錯誤的資料其中來源會是城市人口資料表，其中部分城市可能會因為沒有資料可用，使得人口數為零。 在處理期間，ELT 管線會從城市人口資料表中移除那些城市。 請在暫存表格中執行資料清理，而不是在外部資料表中。

以下的預存程序會從城市人口資料表中移除人口數為零的城市。 (您可以在[此處](https://github.com/mspnp/reference-architectures/blob/master/data/enterprise_bi_sqldw_advanced/azure/sqldw_scripts/citypopulation/%5BIntegration%5D.%5BMigrateExternalCityPopulationData%5D.sql)找到來源檔案。) 

```sql
DELETE FROM [Integration].[CityPopulation_Staging]
WHERE RowNumber in (SELECT DISTINCT RowNumber
FROM [Integration].[CityPopulation_Staging]
WHERE POPULATION = 0
GROUP BY RowNumber
HAVING COUNT(RowNumber) = 4)
```

## <a name="external-data-sources"></a>外部資料來源

資料倉儲通常會合併來自多個來源的資料。 此參考架構會載入包含人口統計資料的外部資料來源。 在 [WorldWideImportersDW](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/wide-world-importers/sample-scripts/polybase) 範例中的 Azure Blob 儲存體內提供了此資料集。

Azure Data Factory 可以使用 [Blob 儲存體連接器](/azure/data-factory/connector-azure-blob-storage)，直接從 Blob 儲存體複製。 不過，連接器需要連接字串或共用存取簽章，因此不能用來複製具有公用讀取權限的 Blob。 因應措施是使用 PolyBase 從 Blob 儲存體建立外部資料表，再將外部資料表複製到 SQL 資料倉儲中。 

## <a name="handling-large-binary-data"></a>處理大型二進位資料 

在來源資料庫中，城市資料表具有包含[地理](/sql/t-sql/spatial-geography/spatial-types-geography)空間資料類型的「位置」資料行。 SQL 資料倉儲原生不支援 **geography** 類型，因此在載入期間會將此欄位轉換成 **varbinary** 類型。 (請參閱[不支援資料類型的因應措施](/azure/sql-data-warehouse/sql-data-warehouse-tables-data-types#unsupported-data-types)。)

不過，PolyBase 支援的最大資料行大小為 `varbinary(8000)`，這表示可能會截斷某些資料。 此問題的因應措施是將資料在匯出期間分成區塊，然後再重組區塊，如下所示：

1. 針對「位置」資料行，建立暫時的暫存資料表。

2. 針對每座城市，將位置資料分割成 8000 個位元組的區塊，產生每座城市 1 &ndash; N 個資料列。

3. 若要重組區塊，請使用 T-SQL [PIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot)運算子將資料列轉換成資料行，然後再串連每座城市的資料行值。

所面臨的挑戰是每座城市會根據地理位置資料的大小，分割成不同數目的資料列。 每座城市都必須具有相同數目的資料列，PIVOT 運算子才能夠運作。 為了進行這項作業，T-SQL 查詢 (您可以在 [here][MergeLocation] 檢視) 藉助一些技巧，用空白值填補了資料列，讓每座城市在樞紐後都具有相同數目的資料行。 產生的查詢比一次重複一列的迴圈快得多。

影像資料也使用了相同的方法。

## <a name="slowly-changing-dimensions"></a>緩時變維度

維度資料相對來說是靜態的，但仍可以變更。 例如，產品可能會指派給不同的產品類別目錄。 有數種方法可處理緩時變維度。 有一種常用的技巧，稱為[類型 2](https://wikipedia.org/wiki/Slowly_changing_dimension#Type_2:_add_new_row)，是在每次維度變更時新增記錄。 

若要實作類型 2 方式，維度資料表會需要可針對給定記錄，指定有效日期範圍的額外資料行。 此外，系統會複製來自來源資料庫的主索引鍵，因此維度資料表必須要有人工的主索引鍵。

下圖顯示 Dimension.City 資料表。 `WWI City ID` 資料行是來自來源資料庫的主索引鍵。 `City Key` 資料行是在 ETL 管線期間所產生的人工索引鍵。 也請注意，資料表有 `Valid From` 和 `Valid To` 資料行，其中定義了每個資料列有效的時間範圍。 `Valid To` 目前值的等於 ' 9999-12-31'。

![](./images/city-dimension-table.png)

這種方法的優點是會保留歷程記錄資料，這對分析來說很有價值。 不過，這也表示相同的實體會有多個資料列。 例如，以下是符合 `WWI City ID` = 28561 的記錄：

![](./images/city-dimension-table-2.png)

針對每個「銷售」事實，建議您將事實與「城市」維度資料表中的單一資料列產生關聯，並對應到發票日期。 請建立具有下列項目的額外資料行，作為 ETL 程序的一部分 

下列 T-SQL 查詢會建立暫存資料表，讓每張發票與來自「城市」維度資料表中正確的「城市索引鍵」產生關聯。

```sql
CREATE TABLE CityHolder
WITH (HEAP , DISTRIBUTION = HASH([WWI Invoice ID]))
AS
SELECT DISTINCT s1.[WWI Invoice ID] AS [WWI Invoice ID],
                c.[City Key] AS [City Key]
    FROM [Integration].[Sale_Staging] s1
    CROSS APPLY (
                SELECT TOP 1 [City Key]
                    FROM [Dimension].[City]
                WHERE [WWI City ID] = s1.[WWI City ID]
                    AND s1.[Last Modified When] > [Valid From]
                    AND s1.[Last Modified When] <= [Valid To]
                ORDER BY [Valid From], [City Key] DESC
                ) c

```

此資料表會用來填入「銷售」事實資料表中的資料行：

```sql
UPDATE [Integration].[Sale_Staging]
SET [Integration].[Sale_Staging].[WWI Customer ID] =  CustomerHolder.[WWI Customer ID]
```

此資料行可讓 Power BI 查詢針對給定的銷售發票，找出正確的「城市」記錄。

## <a name="security-considerations"></a>安全性考量

為了增加安全性，您可以使用[虛擬網路服務端點](/azure/virtual-network/virtual-network-service-endpoints-overview)，讓 Azure 服務資源只連線到您的虛擬網路，以保護其安全。 這會完全移除公用網際網路對這些資源的存取權，而只允許來自您虛擬網路的流量。

使用此方法，您就可以在 Azure 中建立 VNet，然後再建立 Azure 服務的私用服務端點。 接著這些服務會限制為只允許來自該虛擬網路的流量。 您也可以透過閘道，從您的內部部署網路存取這些服務。

請留意以下限制：

- 在此參考架構建立時，Azure 儲存體和 Azure SQL 資料倉儲會支援 VNet 服務端點，但 Azure Analysis Service 則不支援。 請在[此處](https://azure.microsoft.com/updates/?product=virtual-network)檢查最新狀態。 

- 若啟用了 Azure 儲存體的服務端點，PolyBase 就無法從儲存體將資料複製到 SQL 資料倉儲中。 此問題沒有緩和措施。 如需詳細資訊，請參閱[使用 VNet 服務端點搭配 Azure 儲存體的影響](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fvirtual-network%2ftoc.json#impact-of-using-vnet-service-endpoints-with-azure-storage)。 

- 若要將資料從內部部署移動到 Azure 儲存體中，您需要將來自內部部署或 ExpressRoute 的公用 IP 位址，列入白名單中。 欲知詳情，請參閱[將 Azure 服務放到虛擬網路保護](/azure/virtual-network/virtual-network-service-endpoints-overview#securing-azure-services-to-virtual-networks)。

- 若要讓 Analysis Services 可從 SQL 資料倉儲中讀取資料，請將 Windows VM 部署到包含 SQL 資料倉儲服務端點的虛擬網路中。 在 VM 上安裝 [Azure 內部部署資料閘道](/azure/analysis-services/analysis-services-gateway)。 然後將您的 Azure 分析服務連線到資料閘道。

## <a name="deploy-the-solution"></a>部署解決方案

此參考架構的部署可在 [GitHub][ref-arch-repo-folder] 上取得。 它會部署下列各項：

  * 一個 Windows VM，用以模擬內部部署資料庫伺服器。 其中包含 SQL Server 2017 和相關工具以及 Power BI Desktop。
  * 一個提供 Blob 儲存體的 Azure 儲存體帳戶，用以保存從 SQL Server 資料庫匯出的資料。
  * 一個 Azure SQL 資料倉儲執行個體。
  * 一個 Azure Analysis Services 執行個體。
  * Azure Data Factory 和 ELT 作業的 Data Factory 管線。

### <a name="prerequisites"></a>必要條件

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="variables"></a>變數

下列步驟包括了一些使用者定義的變數。 您會需要用您定義的值加以取代。

- `<data_factory_name>` 。 Data Factory 名稱。
- `<analysis_server_name>` 。 Analysis Services 伺服器名稱。
- `<active_directory_upn>` 。 您的 Azure Active Directory 使用者主體名稱 (UPN)。 例如： `user@contoso.com`。
- `<data_warehouse_server_name>` 。 SQL 資料倉儲的伺服器名稱。
- `<data_warehouse_password>` 。 SQL 資料倉儲的系統管理員密碼。
- `<resource_group_name>` 。 資源群組的名稱。
- `<region>` 。 將部署資源的 Azure 區域。
- `<storage_account_name>` 。 儲存體帳戶名稱。 您必須遵循儲存體帳戶的[命名規則](../../best-practices/naming-conventions.md#naming-rules-and-restrictions)。
- `<sql-db-password>` 。 SQL Server 登入密碼。

### <a name="deploy-azure-data-factory"></a>部署 Azure Data Factory

1. 巡覽至 [GitHub repository][ref-arch-repo] 的 `data\enterprise_bi_sqldw_advanced\azure\templates` 資料夾。

2. 請執行下列 Azure CLI 命令以建立資源群組。  

    ```bash
    az group create --name <resource_group_name> --location <region>  
    ```

    指定可支援 SQL 資料倉儲、Azure Analysis Services 和 Data Factory v2 的區域。 請參閱[不同區域的產品](https://azure.microsoft.com/global-infrastructure/services/)

3. 執行下列命令

    ```
    az group deployment create --resource-group <resource_group_name> \
        --template-file adf-create-deploy.json \
        --parameters factoryName=<data_factory_name> location=<location>
    ```

接下來，使用 Azure 入口網站取得 Azure Data Factory [整合執行階段](/azure/data-factory/concepts-integration-runtime)的驗證金鑰，如下所示：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，巡覽至 Data Factory 執行個體。

2. 在 [Data Factory] 刀鋒視窗中，按一下 [撰寫和監視]。 這會在另一個瀏覽器視窗中開啟 Azure Data Factory 入口網站。

    ![](./images/adf-blade.png)

3. 在 Azure Data Factory 入口網站中，選取鉛筆圖示 ([撰寫])。 

4. 按一下 [連線]，然後選取 [整合執行階段]。

5. 在 **sourceIntegrationRuntime** 底下，按一下鉛筆圖示 ([編輯])。

    > [!NOTE]
    > 現在入口網站會將狀態顯示為「無法使用」。 在您部署內部部署伺服器之前，這是預期的行為。

6. 請尋找 **Key1** 並複製驗證金鑰值。

您會需要驗證金鑰以進行下一個步驟。

### <a name="deploy-the-simulated-on-premises-server"></a>部署模擬的內部部署伺服器

在此步驟中，您會將 VM 部署為模擬的內部部署伺服器，其中包含 SQL Server 2017 和相關工具。 這個步驟也會將 [Wide World Importers OLTP database][wwi] 載入 SQL Server 中。

1. 巡覽至存放庫的 `data\enterprise_bi_sqldw_advanced\onprem\templates` 資料夾。

2. 在 `onprem.parameters.json` 檔案中，搜尋 `adminPassword`。 這是登入 SQL Server VM 的密碼。 請用另一個密碼取代該值。

3. 在相同的檔案中，搜尋 `SqlUserCredentials`。 此屬性會指定 SQL Server 的帳戶認證。 請使用不同的值取代該密碼。

4. 在相同的檔案中，將 Integration Runtime 驗證金鑰貼入 `IntegrationRuntimeGatewayKey` 參數中，如下所示：

    ```json
    "protectedSettings": {
        "configurationArguments": {
            "SqlUserCredentials": {
                "userName": ".\\adminUser",
                "password": "<sql-db-password>"
            },
            "IntegrationRuntimeGatewayKey": "<authentication key>"
        }
    ```

5. 執行下列命令。

    ```bash
    azbb -s <subscription_id> -g <resource_group_name> -l <region> -p onprem.parameters.json --deploy
    ```

此步驟可能需要 20 到 30 分鐘才能完成。 此步驟包含執行 [DSC](/powershell/dsc/overview) 指令碼來安裝工具，並將資料庫還原。 

### <a name="deploy-azure-resources"></a>部署 Azure 資源

此步驟會佈建 SQL 資料倉儲、Azure Analysis Services 和 Data Factory。

1. 巡覽至 [GitHub repository][ref-arch-repo] 的 `data\enterprise_bi_sqldw_advanced\azure\templates` 資料夾。

2. 執行下列 Azure CLI 命令。 請取代角括弧中所顯示的參數值。

    ```bash
    az group deployment create --resource-group <resource_group_name> \
     --template-file azure-resources-deploy.json \
     --parameters "dwServerName"="<data_warehouse_server_name>" \
     "dwAdminLogin"="adminuser" "dwAdminPassword"="<data_warehouse_password>" \ 
     "storageAccountName"="<storage_account_name>" \
     "analysisServerName"="<analysis_server_name>" \
     "analysisServerAdmin"="<user@contoso.com>"
    ```

    - `storageAccountName` 參數必須遵循儲存體帳戶的[命名規則](../../best-practices/naming-conventions.md#naming-rules-and-restrictions)。 
    - 對於 `analysisServerAdmin` 參數，請使用您的 Azure Active Directory 使用者主體名稱 (UPN)。

3. 請執行下列 Azure CLI 命令，以取得儲存體帳戶的存取金鑰。 您將會在下個步驟中使用此金鑰。

    ```bash
    az storage account keys list -n <storage_account_name> -g <resource_group_name> --query [0].value
    ```

4. 執行下列 Azure CLI 命令。 請取代角括弧中所顯示的參數值。 

    ```bash
    az group deployment create --resource-group <resource_group_name> \
    --template-file adf-pipeline-deploy.json \
    --parameters "factoryName"="<data_factory_name>" \
    "sinkDWConnectionString"="Server=tcp:<data_warehouse_server_name>.database.windows.net,1433;Initial Catalog=wwi;Persist Security Info=False;User ID=adminuser;Password=<data_warehouse_password>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" \
    "blobConnectionString"="DefaultEndpointsProtocol=https;AccountName=<storage_account_name>;AccountKey=<storage_account_key>;EndpointSuffix=core.windows.net" \
    "sourceDBConnectionString"="Server=sql1;Database=WideWorldImporters;User Id=adminuser;Password=<sql-db-password>;Trusted_Connection=True;"
    ```

    連接字串會以角括弧顯示必須取代的子字串。 針對 `<storage_account_key>`，請使用您在上一個步驟中取得的金鑰。 針對 `<sql-db-password>`，請使用您先前在 `onprem.parameters.json` 檔案中指定的 SQL Server 帳戶密碼。

### <a name="run-the-data-warehouse-scripts"></a>執行資料倉儲指令碼

1. 在 [Azure 入口網站](https://portal.azure.com/)中，找出名為 `sql-vm1` 的內部部署 VM。 該 VM 的使用者名稱與密碼會在 `onprem.parameters.json` 檔案中指定。

2. 請按一下 [連線]，並使用遠端桌面連線到 VM。

3. 從遠端桌面工作階段中，開啟命令提示字元，並巡覽至 VM 上的下列資料夾：

    ```
    cd C:\SampleDataFiles\reference-architectures\data\enterprise_bi_sqldw_advanced\azure\sqldw_scripts
    ```

4. 執行以下命令：

    ```
    deploy_database.cmd -S <data_warehouse_server_name>.database.windows.net -d wwi -U adminuser -P <data_warehouse_password> -N -I
    ```

    針對 `<data_warehouse_server_name>` 和 `<data_warehouse_password>`，請使用先前取得的資料倉儲伺服器名稱和密碼。

若要確認此步驟，您可以使用 SQL Server Management Studio (SSMS) 連線到 SQL 資料倉儲資料庫。 您應該會看到資料庫資料表結構描述。

### <a name="run-the-data-factory-pipeline"></a>執行 Data Factory 管線

1. 從相同的遠端桌面工作階段中，開啟 PowerShell 視窗。

2. 執行下列 PowerShell 命令。 出現提示時，請選擇 [是]。

    ```powershell
    Install-Module -Name AzureRM -AllowClobber
    ```

3. 執行下列 PowerShell 命令。 出現提示時，請輸入您的 Azure 認證。

    ```powershell
    Connect-AzureRmAccount 
    ```

4. 請執行下列 PowerShell 命令。 請取代角括弧中的值。

    ```powershell
    Set-AzureRmContext -SubscriptionId <subscription id>

    Invoke-AzureRmDataFactoryV2Pipeline -DataFactory <data-factory-name> -PipelineName "MasterPipeline" -ResourceGroupName <resource_group_name>

5. In the Azure Portal, navigate to the Data Factory instance that was created earlier.

6. In the Data Factory blade, click **Author & Monitor**. This opens the Azure Data Factory portal in another browser window.

    ![](./images/adf-blade.png)

7. In the Azure Data Factory portal, click the **Monitor** icon. 

8. Verify that the pipeline completes successfully. It can take a few minutes.

    ![](./images/adf-pipeline-progress.png)


## Build the Analysis Services model

In this step, you will create a tabular model that imports data from the data warehouse. Then you will deploy the model to Azure Analysis Services.

**Create a new tabular project**

1. From your Remote Desktop session, launch SQL Server Data Tools 2015.

2. Select **File** > **New** > **Project**.

3. In the **New Project** dialog, under **Templates**, select  **Business Intelligence** > **Analysis Services** > **Analysis Services Tabular Project**. 

4. Name the project and click **OK**.

5. In the **Tabular model designer** dialog, select **Integrated workspace**  and set **Compatibility level** to `SQL Server 2017 / Azure Analysis Services (1400)`. 

6. Click **OK**.


**Import data**

1. In the **Tabular Model Explorer** window, right-click the project and select **Import from Data Source**.

2. Select **Azure SQL Data Warehouse** and click **Connect**.

3. For **Server**, enter the fully qualified name of your Azure SQL Data Warehouse server. You can get this value from the Azure Portal. For **Database**, enter `wwi`. Click **OK**.

4. In the next dialog, choose **Database** authentication and enter your Azure SQL Data Warehouse user name and password, and click **OK**.

5. In the **Navigator** dialog, select the checkboxes for the **Fact.\*** and **Dimension.\*** tables.

    ![](./images/analysis-services-import-2.png)

6. Click **Load**. When processing is complete, click **Close**. You should now see a tabular view of the data.

**Create measures**

1. In the model designer, select the **Fact Sale** table.

2. Click a cell in the the measure grid. By default, the measure grid is displayed below the table. 

    ![](./images/tabular-model-measures.png)

3. In the formula bar, enter the following and press ENTER:

    ```
    Total Sales:=SUM('Fact Sale'[Total Including Tax])
    ```

4. Repeat these steps to create the following measures:

    ```
    Number of Years:=(MAX('Fact CityPopulation'[YearNumber])-MIN('Fact CityPopulation'[YearNumber]))+1
    
    Beginning Population:=CALCULATE(SUM('Fact CityPopulation'[Population]),FILTER('Fact CityPopulation','Fact CityPopulation'[YearNumber]=MIN('Fact CityPopulation'[YearNumber])))
    
    Ending Population:=CALCULATE(SUM('Fact CityPopulation'[Population]),FILTER('Fact CityPopulation','Fact CityPopulation'[YearNumber]=MAX('Fact CityPopulation'[YearNumber])))
    
    CAGR:=IFERROR((([Ending Population]/[Beginning Population])^(1/[Number of Years]))-1,0)
    ```

    ![](./images/analysis-services-measures.png)

For more information about creating measures in SQL Server Data Tools, see [Measures](/sql/analysis-services/tabular-models/measures-ssas-tabular).

**Create relationships**

1. In the **Tabular Model Explorer** window, right-click the project and select **Model View** > **Diagram View**.

2. Drag the **[Fact Sale].[City Key]** field to the **[Dimension City].[City Key]** field to create a relationship.  

3. Drag the **[Face CityPopulation].[City Key]** field to the **[Dimension City].[City Key]** field.  

    ![](./images/analysis-services-relations-2.png)

**Deploy the model**

1. From the **File** menu, choose **Save All**.

2. In **Solution Explorer**, right-click the project and select **Properties**. 

3. Under **Server**, enter the URL of your Azure Analysis Services instance. You can get this value from the Azure Portal. In the portal, select the Analysis Services resource, click the Overview pane, and look for the **Server Name** property. It will be similar to `asazure://westus.asazure.windows.net/contoso`. Click **OK**.

    ![](./images/analysis-services-properties.png)

4. In **Solution Explorer**, right-click the project and select **Deploy**. Sign into Azure if prompted. When processing is complete, click **Close**.

5. In the Azure portal, view the details for your Azure Analysis Services instance. Verify that your model appears in the list of models.

    ![](./images/analysis-services-models.png)

## Analyze the data in Power BI Desktop

In this step, you will use Power BI to create a report from the data in Analysis Services.

1. From your Remote Desktop session, launch Power BI Desktop.

2. In the Welcome Scren, click **Get Data**.

3. Select **Azure** > **Azure Analysis Services database**. Click **Connect**

    ![](./images/power-bi-get-data.png)

4. Enter the URL of your Analysis Services instance, then click **OK**. Sign into Azure if prompted.

5. In the **Navigator** dialog, expand the tabular project, select the model, and click **OK**.

2. In the **Visualizations** pane, select the **Table** icon. In the Report view, resize the visualization to make it larger.

6. In the **Fields** pane, expand **Dimension City**.

7. From **Dimension City**, drag **City** and **State Province** to the **Values** well.

9. In the **Fields** pane, expand **Fact Sale**.

10. From **Fact Sale**, drag **CAGR**, **Ending Population**,  and **Total Sales** to the **Value** well.

11. Under **Visual Level Filters**, select **Ending Population**. Set the filter to "is greater than 100000" and click **Apply filter**.

12. Under **Visual Level Filters**, select **Total Sales**. Set the filter to "is 0" and click **Apply filter**.

![](./images/power-bi-report-2.png)

The table now shows cities with population greater than 100,000 and zero sales. CAGR  stands for Compounded Annual Growth Rate and measures the rate of population growth per city. You could use this value to find cities with high growth rates, for example. However, note that the values for CAGR in the model aren't accurate, because they are derived from sample data.

To learn more about Power BI Desktop, see [Getting started with Power BI Desktop](/power-bi/desktop-getting-started).


[adf]: //azure/data-factory
[azure-cli-2]: //azure/install-azure-cli
[azbb-repo]: https://github.com/mspnp/template-building-blocks
[azbb-wiki]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[MergeLocation]: https://github.com/mspnp/reference-architectures/blob/master/data/enterprise_bi_sqldw_advanced/azure/sqldw_scripts/city/%5BIntegration%5D.%5BMergeLocation%5D.sql
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[ref-arch-repo-folder]: https://github.com/mspnp/reference-architectures/tree/master/data/enterprise_bi_sqldw_advanced
[wwi]: //sql/sample/world-wide-importers/wide-world-importers-oltp-database

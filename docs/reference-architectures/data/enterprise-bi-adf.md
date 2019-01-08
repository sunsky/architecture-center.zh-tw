---
title: 自動化企業商業智慧 (BI)
titleSuffix: Azure Reference Architectures
description: 使用 Azure Data Factory 與 SQL 資料倉儲來自動化 Azure 中的擷取、載入和轉換 (ELT) 工作流程。
author: MikeWasson
ms.date: 11/06/2018
ms.custom: seodec18
ms.openlocfilehash: 8263da7675beb61add371c945aab72b203c2349c
ms.sourcegitcommit: bb7fcffbb41e2c26a26f8781df32825eb60df70c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2018
ms.locfileid: "53643987"
---
# <a name="automated-enterprise-bi-with-sql-data-warehouse-and-azure-data-factory"></a>具 SQL 資料倉儲和 Azure Data Factory 的自動化 Enterprise BI

此參考架構示範如何在[擷取、載入和轉換 (ELT)](../../data-guide/relational-data/etl.md#extract-load-and-transform-elt) 管線中執行累加式載入。 該架構會使用 Azure Data Factory 將 ELT 管線自動化。 管線會以累加方式，將最新的 OLTP 資料從內部部署 SQL Server 資料庫載入 SQL 資料倉儲中。 交易資料會轉換成表格式模型以供分析。

> [!VIDEO <https://www.microsoft.com/videoplayer/embed/RE2Gnz2>]

此架構的參考實作可在 [GitHub][github] 上取得。

![具 SQL 資料倉儲和 Azure Data Factory 的自動化企業 BI 架構圖](./images/enterprise-bi-sqldw-adf.png)

此架構是基於[具 SQL 資料倉儲的 Enterprise BI](./enterprise-bi-sqldw.md)，但增加了一些對企業資料倉儲情況很重要的功能。

- 使用 Data Factory 將管線自動化。
- 累加式載入。
- 可整合多個資料來源。
- 可載入二進位資料，立如地理空間資料與影像。

## <a name="architecture"></a>架構

此架構由下列元件組成。

### <a name="data-sources"></a>資料來源

**內部部署的 SQL Server**。 來源資料位於內部部署的 SQL Server 資料庫中。 為了模擬內部部署環境，此架構的部署指令碼會在 Azure 中佈建已安裝 SQL Server 的虛擬機器。 系統會使用 [Wide World Importers OLTP 範例資料庫][wwi] 作為來源資料庫。

**外部資料**。 資料倉儲常見的一種情況是整合多個資料來源。 此參考架構會載入外部資料集，其中包含依年度的城市人口，且會將資料集與 OLTP 資料庫中的資料整合。 您可以使用此資料取得深入解析，例如：「每個區域中的銷售成長率是符合或超過人口成長率？」

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

在 [Azure Data Factory][adf] 中，管線是在此情況下，用來協調工作的活動邏輯群組，會將資料載入 SQL 資料倉儲中，並加以轉換。

此參考架構會定義執行一連串的子管線的主要管線。 每條子管線都會將資料載入一個或多個資料倉儲資料表中。

![Azure Data Factory 中管線的螢幕擷取畫面](./images/adf-pipeline.png)

## <a name="incremental-loading"></a>累加式載入

當您執行自動化的 ETL 或 ELT 程序時，只載入從上次執行之後變更的資料最有效率。 這就叫做「累加式載入」，而非載入所有資料的完整載入。 若要執行累加式載入，您需要有識別哪些資料已經變更的方式。 最常見的方法是使用「上限標準」值，這表示要追蹤來源資料表中某些資料行 (可能是日期時間資料行，或唯一的整數資料行) 的最新值。

從 SQL Server 2016 開始，您可以使用[時態表](/sql/relational-databases/tables/temporal-tables)。 這些是由系統建立版本的資料表，會保留資料變更的完整歷程記錄。 資料庫引擎會將每一項變更的歷程記錄，自動記錄在個別的歷程記錄資料表中。 您可以將 FOR SYSTEM_TIME 子句新增到查詢中，來查詢歷程記錄資料。 就內部而言，資料庫引擎會查詢記錄資料表，但這對應用程式而言是透明的。

> [!NOTE]
> 對於舊版的 SQL Server，您可以使用[異動資料擷取](/sql/relational-databases/track-changes/about-change-data-capture-sql-server)(CDC)。 這個方法比起時態表較不方便，因為您需要查詢個別變更資料表，而且此方法不是按照時間戳記，而是按照記錄序號來追蹤變更。
>

時態表對於維度資料很有用，因為後者會隨著時間而變更。 事實資料表通常代表固定的交易 (例如銷售)，在此情況下保留由系統建立版本的歷程記錄沒有任何意義。 因此，交易通常會有代表交易日期的資料行，可用來當作上限標準值。 例如，在 Wide World Importers OLTP 資料庫中，Sales.Invoices 和 Sales.InvoiceLines 資料表有 `LastEditedWhen` 欄位，預設值為 `sysdatetime()`。

以下是 ELT 管線的一般流程：

1. 針對來源資料庫中每個資料表，追蹤上次 ELT 作業執行的截止時間。 將這項資訊儲存在資料倉儲中。 (在初始設定時，所有時間都設為 '1-1-1900'。)

2. 在資料匯出步驟期間，會將截止時間當作參數，傳遞至來源資料庫中的一組預存程序。 這些預存程序會查詢截止時間之後所變更或建立的任何記錄。 「銷售」事實資料表會使用 `LastEditedWhen` 資料行。 維度資料則會使用由系統建立版本的時態表。

3. 資料移轉完成時，會更新儲存截止時間的資料表。

記錄每次 ELT 執行的*歷程*也很有幫助。 對於給定的記錄，歷程會將該記錄與產生資料的 ELT 執行產生關聯。 每次 ELT 執行時，都會針對每份資料表建立新的歷程記錄，顯示開始和結束載入時間。 每筆記錄的歷程索引鍵都會儲存在維度資料表和事實資料表中。

![城市維度資料表的螢幕擷取畫面](./images/city-dimension-table.png)

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

所面臨的挑戰是每座城市會根據地理位置資料的大小，分割成不同數目的資料列。 每座城市都必須具有相同數目的資料列，PIVOT 運算子才能夠運作。 為了進行這項作業，T-SQL 查詢 (您可以在 [這裡][MergeLocation]檢視) 藉助一些技巧，用空白值填補了資料列，讓每座城市在樞紐後都具有相同數目的資料行。 產生的查詢比一次重複一列的迴圈快得多。

影像資料也使用了相同的方法。

## <a name="slowly-changing-dimensions"></a>緩時變維度

維度資料相對來說是靜態的，但仍可以變更。 例如，產品可能會指派給不同的產品類別目錄。 有數種方法可處理緩時變維度。 有一種常用的技巧，稱為[類型 2](https://wikipedia.org/wiki/Slowly_changing_dimension#Type_2:_add_new_row)，是在每次維度變更時新增記錄。

若要實作類型 2 方式，維度資料表會需要可針對給定記錄，指定有效日期範圍的額外資料行。 此外，系統會複製來自來源資料庫的主索引鍵，因此維度資料表必須要有人工的主索引鍵。

下圖顯示 Dimension.City 資料表。 `WWI City ID` 資料行是來自來源資料庫的主索引鍵。 `City Key` 資料行是在 ETL 管線期間所產生的人工索引鍵。 也請注意，資料表有 `Valid From` 和 `Valid To` 資料行，其中定義了每個資料列有效的時間範圍。 `Valid To` 目前值的等於 ' 9999-12-31'。

![城市維度資料表的螢幕擷取畫面](./images/city-dimension-table.png)

這種方法的優點是會保留歷程記錄資料，這對分析來說很有價值。 不過，這也表示相同的實體會有多個資料列。 例如，以下是符合 `WWI City ID` = 28561 的記錄：

![城市維度資料表的第二個螢幕擷取畫面](./images/city-dimension-table-2.png)

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

- 若要將資料從內部部署移動到 Azure 儲存體中，您需要將來自內部部署或 ExpressRoute 的公用 IP 位址，列入允許清單中。 欲知詳情，請參閱[將 Azure 服務放到虛擬網路保護](/azure/virtual-network/virtual-network-service-endpoints-overview#securing-azure-services-to-virtual-networks)。

- 若要讓 Analysis Services 可從 SQL 資料倉儲中讀取資料，請將 Windows VM 部署到包含 SQL 資料倉儲服務端點的虛擬網路中。 在 VM 上安裝 [Azure 內部部署資料閘道](/azure/analysis-services/analysis-services-gateway)。 然後將您的 Azure 分析服務連線到資料閘道。

## <a name="deploy-the-solution"></a>部署解決方案

若要部署及執行參考實作，請依照 [GitHub 讀我檔案][github]中的步驟。 它會部署下列各項：

- 一個 Windows VM，用以模擬內部部署資料庫伺服器。 其中包含 SQL Server 2017 和相關工具以及 Power BI Desktop。
- 一個提供 Blob 儲存體的 Azure 儲存體帳戶，用以保存從 SQL Server 資料庫匯出的資料。
- 一個 Azure SQL 資料倉儲執行個體。
- 一個 Azure Analysis Services 執行個體。
- Azure Data Factory 和 ELT 作業的 Data Factory 管線。

## <a name="related-resources"></a>相關資源

您可以檢閱下列 [Azure 範例案例](/azure/architecture/example-scenario)，其中示範使用相同技術的一些特定解決方案：

- [適用於銷售和行銷的資料倉儲和分析](/azure/architecture/example-scenario/data/data-warehouse)
- [使用現有內部部署 SSIS 和 Azure Data Factory 的混合式 ETL](/azure/architecture/example-scenario/data/hybrid-etl-with-adf)

<!-- links -->

[adf]: /azure/data-factory
[github]: https://github.com/mspnp/azure-data-factory-sqldw-elt-pipeline
[MergeLocation]: https://github.com/mspnp/reference-architectures/blob/master/data/enterprise_bi_sqldw_advanced/azure/sqldw_scripts/city/%5BIntegration%5D.%5BMergeLocation%5D.sql
[wwi]: /sql/sample/world-wide-importers/wide-world-importers-oltp-database

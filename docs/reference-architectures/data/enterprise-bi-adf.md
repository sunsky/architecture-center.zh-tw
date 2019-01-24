---
title: 自動化企業商業智慧 (BI)
titleSuffix: Azure Reference Architectures
description: 使用 Azure Data Factory 與 SQL 資料倉儲來自動化 Azure 中的擷取、載入和轉換 (ELT) 工作流程。
author: MikeWasson
ms.date: 11/06/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18
ms.openlocfilehash: 020c401e9db85b76fd48c6df9be9c80d2ba5c7e4
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54481468"
---
# <a name="automated-enterprise-bi-with-sql-data-warehouse-and-azure-data-factory"></a><span data-ttu-id="0b1a3-103">具 SQL 資料倉儲和 Azure Data Factory 的自動化 Enterprise BI</span><span class="sxs-lookup"><span data-stu-id="0b1a3-103">Automated enterprise BI with SQL Data Warehouse and Azure Data Factory</span></span>

<span data-ttu-id="0b1a3-104">此參考架構示範如何在[擷取、載入和轉換 (ELT)](../../data-guide/relational-data/etl.md#extract-load-and-transform-elt) 管線中執行累加式載入。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-104">This reference architecture shows how to perform incremental loading in an [extract, load, and transform (ELT)](../../data-guide/relational-data/etl.md#extract-load-and-transform-elt) pipeline.</span></span> <span data-ttu-id="0b1a3-105">該架構會使用 Azure Data Factory 將 ELT 管線自動化。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-105">It uses Azure Data Factory to automate the ELT pipeline.</span></span> <span data-ttu-id="0b1a3-106">管線會以累加方式，將最新的 OLTP 資料從內部部署 SQL Server 資料庫載入 SQL 資料倉儲中。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-106">The pipeline incrementally moves the latest OLTP data from an on-premises SQL Server database into SQL Data Warehouse.</span></span> <span data-ttu-id="0b1a3-107">交易資料會轉換成表格式模型以供分析。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-107">Transactional data is transformed into a tabular model for analysis.</span></span>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2Gnz2]

<span data-ttu-id="0b1a3-108">此架構的參考實作可在 [GitHub][github] 上取得。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-108">A reference implementation for this architecture is available on [GitHub][github].</span></span>

![具 SQL 資料倉儲和 Azure Data Factory 的自動化企業 BI 架構圖](./images/enterprise-bi-sqldw-adf.png)

<span data-ttu-id="0b1a3-110">此架構是基於[具 SQL 資料倉儲的 Enterprise BI](./enterprise-bi-sqldw.md)，但增加了一些對企業資料倉儲情況很重要的功能。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-110">This architecture builds on the one shown in [Enterprise BI with SQL Data Warehouse](./enterprise-bi-sqldw.md), but adds some features that are important for enterprise data warehousing scenarios.</span></span>

- <span data-ttu-id="0b1a3-111">使用 Data Factory 將管線自動化。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-111">Automation of the pipeline using Data Factory.</span></span>
- <span data-ttu-id="0b1a3-112">累加式載入。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-112">Incremental loading.</span></span>
- <span data-ttu-id="0b1a3-113">可整合多個資料來源。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-113">Integrating multiple data sources.</span></span>
- <span data-ttu-id="0b1a3-114">可載入二進位資料，立如地理空間資料與影像。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-114">Loading binary data such as geospatial data and images.</span></span>

## <a name="architecture"></a><span data-ttu-id="0b1a3-115">架構</span><span class="sxs-lookup"><span data-stu-id="0b1a3-115">Architecture</span></span>

<span data-ttu-id="0b1a3-116">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-116">The architecture consists of the following components.</span></span>

### <a name="data-sources"></a><span data-ttu-id="0b1a3-117">資料來源</span><span class="sxs-lookup"><span data-stu-id="0b1a3-117">Data sources</span></span>

<span data-ttu-id="0b1a3-118">**內部部署的 SQL Server**。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-118">**On-premises SQL Server**.</span></span> <span data-ttu-id="0b1a3-119">來源資料位於內部部署的 SQL Server 資料庫中。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-119">The source data is located in a SQL Server database on premises.</span></span> <span data-ttu-id="0b1a3-120">為了模擬內部部署環境，此架構的部署指令碼會在 Azure 中佈建已安裝 SQL Server 的虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-120">To simulate the on-premises environment, the deployment scripts for this architecture provision a virtual machine in Azure with SQL Server installed.</span></span> <span data-ttu-id="0b1a3-121">系統會使用 [Wide World Importers OLTP 範例資料庫][wwi] 作為來源資料庫。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-121">The [Wide World Importers OLTP sample database][wwi] is used as the source database.</span></span>

<span data-ttu-id="0b1a3-122">**外部資料**。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-122">**External data**.</span></span> <span data-ttu-id="0b1a3-123">資料倉儲常見的一種情況是整合多個資料來源。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-123">A common scenario for data warehouses is to integrate multiple data sources.</span></span> <span data-ttu-id="0b1a3-124">此參考架構會載入外部資料集，其中包含依年度的城市人口，且會將資料集與 OLTP 資料庫中的資料整合。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-124">This reference architecture loads an external data set that contains city populations by year, and integrates it with the data from the OLTP database.</span></span> <span data-ttu-id="0b1a3-125">您可以使用此資料取得深入解析，例如：「每個區域中的銷售成長率是符合或超過人口成長率？」</span><span class="sxs-lookup"><span data-stu-id="0b1a3-125">You can use this data for insights such as: "Does sales growth in each region match or exceed population growth?"</span></span>

### <a name="ingestion-and-data-storage"></a><span data-ttu-id="0b1a3-126">擷取和資料儲存體</span><span class="sxs-lookup"><span data-stu-id="0b1a3-126">Ingestion and data storage</span></span>

<span data-ttu-id="0b1a3-127">**Blob 儲存體**。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-127">**Blob Storage**.</span></span> <span data-ttu-id="0b1a3-128">Blob 儲存體會作為來源資料在載入至 SQL 資料倉儲之前的臨時區域。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-128">Blob storage is used as a staging area for the source data before loading it into SQL Data Warehouse.</span></span>

<span data-ttu-id="0b1a3-129">**Azure SQL 資料倉儲**。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-129">**Azure SQL Data Warehouse**.</span></span> <span data-ttu-id="0b1a3-130">[SQL 資料倉儲](/azure/sql-data-warehouse/)是為了對大型資料執行分析而設計的分散式系統。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-130">[SQL Data Warehouse](/azure/sql-data-warehouse/) is a distributed system designed to perform analytics on large data.</span></span> <span data-ttu-id="0b1a3-131">它支援大量平行處理 (MPP)，因而適合用來執行高效能分析。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-131">It supports massive parallel processing (MPP), which makes it suitable for running high-performance analytics.</span></span>

<span data-ttu-id="0b1a3-132">**Azure Data Factory**。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-132">**Azure Data Factory**.</span></span> <span data-ttu-id="0b1a3-133">[Data Factory][adf] 是受控服務，可協調並自動化資料移動和資料轉換。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-133">[Data Factory][adf] is a managed service that orchestrates and automates data movement and data transformation.</span></span> <span data-ttu-id="0b1a3-134">在此架構中，該服務會協調 ELT 程序的各個階段。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-134">In this architecture, it coordinates the various stages of the ELT process.</span></span>

### <a name="analysis-and-reporting"></a><span data-ttu-id="0b1a3-135">分析和報告</span><span class="sxs-lookup"><span data-stu-id="0b1a3-135">Analysis and reporting</span></span>

<span data-ttu-id="0b1a3-136">**Azure Analysis Services**。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-136">**Azure Analysis Services**.</span></span> <span data-ttu-id="0b1a3-137">[Analysis Services](/azure/analysis-services/) 是完全受控的服務，可提供資料模型功能。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-137">[Analysis Services](/azure/analysis-services/) is a fully managed service that provides data modeling capabilities.</span></span> <span data-ttu-id="0b1a3-138">語意模型會載入 Analysis Services 中。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-138">The semantic model is loaded into Analysis Services.</span></span>

<span data-ttu-id="0b1a3-139">**Power BI**。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-139">**Power BI**.</span></span> <span data-ttu-id="0b1a3-140">Power BI 是一套用來分析資料以產生商業見解的商務分析工具。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-140">Power BI is a suite of business analytics tools to analyze data for business insights.</span></span> <span data-ttu-id="0b1a3-141">在此架構中，它會查詢儲存在 Analysis Services 中的語意模型。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-141">In this architecture, it queries the semantic model stored in Analysis Services.</span></span>

### <a name="authentication"></a><span data-ttu-id="0b1a3-142">驗證</span><span class="sxs-lookup"><span data-stu-id="0b1a3-142">Authentication</span></span>

<span data-ttu-id="0b1a3-143">**Azure Active Directory** (Azure AD) 會驗證透過 Power BI 連線至 Analysis Services 伺服器的使用者。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-143">**Azure Active Directory** (Azure AD) authenticates users who connect to the Analysis Services server through Power BI.</span></span>

<span data-ttu-id="0b1a3-144">Data Factory 也可以透過使用服務主體或受控服務識別 (MSI)，利用 Azure AD 來驗證 SQL 資料倉儲。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-144">Data Factory can use also use Azure AD to authenticate to SQL Data Warehouse, by using a service principal or Managed Service Identity (MSI).</span></span> <span data-ttu-id="0b1a3-145">為了簡單起見，範例部署會使用 SQL Server 驗證。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-145">For simplicity, the example deployment uses SQL Server authentication.</span></span>

## <a name="data-pipeline"></a><span data-ttu-id="0b1a3-146">Data Pipeline</span><span class="sxs-lookup"><span data-stu-id="0b1a3-146">Data pipeline</span></span>

<span data-ttu-id="0b1a3-147">在 [Azure Data Factory][adf] 中，管線是在此情況下，用來協調工作的活動邏輯群組，會將資料載入 SQL 資料倉儲中，並加以轉換。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-147">In [Azure Data Factory][adf], a pipeline is a logical grouping of activities used to coordinate a task &mdash; in this case, loading and transforming data into SQL Data Warehouse.</span></span>

<span data-ttu-id="0b1a3-148">此參考架構會定義執行一連串的子管線的主要管線。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-148">This reference architecture defines a master pipeline that runs a sequence of child pipelines.</span></span> <span data-ttu-id="0b1a3-149">每條子管線都會將資料載入一個或多個資料倉儲資料表中。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-149">Each child pipeline loads data into one or more data warehouse tables.</span></span>

![Azure Data Factory 中管線的螢幕擷取畫面](./images/adf-pipeline.png)

## <a name="incremental-loading"></a><span data-ttu-id="0b1a3-151">累加式載入</span><span class="sxs-lookup"><span data-stu-id="0b1a3-151">Incremental loading</span></span>

<span data-ttu-id="0b1a3-152">當您執行自動化的 ETL 或 ELT 程序時，只載入從上次執行之後變更的資料最有效率。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-152">When you run an automated ETL or ELT process, it's most efficient to load only the data that changed since the previous run.</span></span> <span data-ttu-id="0b1a3-153">這就叫做「累加式載入」，而非載入所有資料的完整載入。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-153">This is called an *incremental load*, as opposed to a full load that loads all of the data.</span></span> <span data-ttu-id="0b1a3-154">若要執行累加式載入，您需要有識別哪些資料已經變更的方式。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-154">To perform an incremental load, you need a way to identify which data has changed.</span></span> <span data-ttu-id="0b1a3-155">最常見的方法是使用「上限標準」值，這表示要追蹤來源資料表中某些資料行 (可能是日期時間資料行，或唯一的整數資料行) 的最新值。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-155">The most common approach is to use a *high water mark* value, which means tracking the latest value of some column in the source table, either a datetime column or a unique integer column.</span></span>

<span data-ttu-id="0b1a3-156">從 SQL Server 2016 開始，您可以使用[時態表](/sql/relational-databases/tables/temporal-tables)。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-156">Starting with SQL Server 2016, you can use [temporal tables](/sql/relational-databases/tables/temporal-tables).</span></span> <span data-ttu-id="0b1a3-157">這些是由系統建立版本的資料表，會保留資料變更的完整歷程記錄。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-157">These are system-versioned tables that keep a full history of data changes.</span></span> <span data-ttu-id="0b1a3-158">資料庫引擎會將每一項變更的歷程記錄，自動記錄在個別的歷程記錄資料表中。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-158">The database engine automatically records the history of every change in a separate history table.</span></span> <span data-ttu-id="0b1a3-159">您可以將 FOR SYSTEM_TIME 子句新增到查詢中，來查詢歷程記錄資料。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-159">You can query the historical data by adding a FOR SYSTEM_TIME clause to a query.</span></span> <span data-ttu-id="0b1a3-160">就內部而言，資料庫引擎會查詢記錄資料表，但這對應用程式而言是透明的。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-160">Internally, the database engine queries the history table, but this is transparent to the application.</span></span>

> [!NOTE]
> <span data-ttu-id="0b1a3-161">對於舊版的 SQL Server，您可以使用[異動資料擷取](/sql/relational-databases/track-changes/about-change-data-capture-sql-server)(CDC)。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-161">For earlier versions of SQL Server, you can use [Change Data Capture](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) (CDC).</span></span> <span data-ttu-id="0b1a3-162">這個方法比起時態表較不方便，因為您需要查詢個別變更資料表，而且此方法不是按照時間戳記，而是按照記錄序號來追蹤變更。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-162">This approach is less convenient than temporal tables, because you have to query a separate change table, and changes are tracked by a log sequence number, rather than a timestamp.</span></span>
>

<span data-ttu-id="0b1a3-163">時態表對於維度資料很有用，因為後者會隨著時間而變更。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-163">Temporal tables are useful for dimension data, which can change over time.</span></span> <span data-ttu-id="0b1a3-164">事實資料表通常代表固定的交易 (例如銷售)，在此情況下保留由系統建立版本的歷程記錄沒有任何意義。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-164">Fact tables usually represent an immutable transaction such as a sale, in which case keeping the system version history doesn't make sense.</span></span> <span data-ttu-id="0b1a3-165">因此，交易通常會有代表交易日期的資料行，可用來當作上限標準值。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-165">Instead, transactions usually have a column that represents the transaction date, which can be used as the watermark value.</span></span> <span data-ttu-id="0b1a3-166">例如，在 Wide World Importers OLTP 資料庫中，Sales.Invoices 和 Sales.InvoiceLines 資料表有 `LastEditedWhen` 欄位，預設值為 `sysdatetime()`。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-166">For example, in the Wide World Importers OLTP database, the Sales.Invoices and Sales.InvoiceLines tables have a `LastEditedWhen` field that defaults to `sysdatetime()`.</span></span>

<span data-ttu-id="0b1a3-167">以下是 ELT 管線的一般流程：</span><span class="sxs-lookup"><span data-stu-id="0b1a3-167">Here is the general flow for the ELT pipeline:</span></span>

1. <span data-ttu-id="0b1a3-168">針對來源資料庫中每個資料表，追蹤上次 ELT 作業執行的截止時間。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-168">For each table in the source database, track the cutoff time when the last ELT job ran.</span></span> <span data-ttu-id="0b1a3-169">將這項資訊儲存在資料倉儲中。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-169">Store this information in the data warehouse.</span></span> <span data-ttu-id="0b1a3-170">(在初始設定時，所有時間都設為 '1-1-1900'。)</span><span class="sxs-lookup"><span data-stu-id="0b1a3-170">(On initial setup, all times are set to '1-1-1900'.)</span></span>

2. <span data-ttu-id="0b1a3-171">在資料匯出步驟期間，會將截止時間當作參數，傳遞至來源資料庫中的一組預存程序。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-171">During the data export step, the cutoff time is passed as a parameter to a set of stored procedures in the source database.</span></span> <span data-ttu-id="0b1a3-172">這些預存程序會查詢截止時間之後所變更或建立的任何記錄。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-172">These stored procedures query for any records that were changed or created after the cutoff time.</span></span> <span data-ttu-id="0b1a3-173">「銷售」事實資料表會使用 `LastEditedWhen` 資料行。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-173">For the Sales fact table, the `LastEditedWhen` column is used.</span></span> <span data-ttu-id="0b1a3-174">維度資料則會使用由系統建立版本的時態表。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-174">For the dimension data, system-versioned temporal tables are used.</span></span>

3. <span data-ttu-id="0b1a3-175">資料移轉完成時，會更新儲存截止時間的資料表。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-175">When the data migration is complete, update the table that stores the cutoff times.</span></span>

<span data-ttu-id="0b1a3-176">記錄每次 ELT 執行的*歷程*也很有幫助。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-176">It's also useful to record a *lineage* for each ELT run.</span></span> <span data-ttu-id="0b1a3-177">對於給定的記錄，歷程會將該記錄與產生資料的 ELT 執行產生關聯。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-177">For a given record, the lineage associates that record with the ELT run that produced the data.</span></span> <span data-ttu-id="0b1a3-178">每次 ELT 執行時，都會針對每份資料表建立新的歷程記錄，顯示開始和結束載入時間。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-178">For each ETL run, a new lineage record is created for every table, showing the starting and ending load times.</span></span> <span data-ttu-id="0b1a3-179">每筆記錄的歷程索引鍵都會儲存在維度資料表和事實資料表中。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-179">The lineage keys for each record are stored in the dimension and fact tables.</span></span>

![城市維度資料表的螢幕擷取畫面](./images/city-dimension-table.png)

<span data-ttu-id="0b1a3-181">新批次的資料載入至倉儲之後，會重新整理 Analysis Services 表格式模型。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-181">After a new batch of data is loaded into the warehouse, refresh the Analysis Services tabular model.</span></span> <span data-ttu-id="0b1a3-182">請參閱[使用 REST API 進行非同步重新整理](/azure/analysis-services/analysis-services-async-refresh)。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-182">See [Asynchronous refresh with the REST API](/azure/analysis-services/analysis-services-async-refresh).</span></span>

## <a name="data-cleansing"></a><span data-ttu-id="0b1a3-183">資料清理</span><span class="sxs-lookup"><span data-stu-id="0b1a3-183">Data cleansing</span></span>

<span data-ttu-id="0b1a3-184">資料清理應該是 ELT 程序的一部分。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-184">Data cleansing should be part of the ELT process.</span></span> <span data-ttu-id="0b1a3-185">在此參考架構中，有錯誤的資料其中來源會是城市人口資料表，其中部分城市可能會因為沒有資料可用，使得人口數為零。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-185">In this reference architecture, one source of bad data is the city population table, where some cities have zero population, perhaps because no data was available.</span></span> <span data-ttu-id="0b1a3-186">在處理期間，ELT 管線會從城市人口資料表中移除那些城市。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-186">During processing, the ELT pipeline removes those cities from the city population table.</span></span> <span data-ttu-id="0b1a3-187">請在暫存表格中執行資料清理，而不是在外部資料表中。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-187">Perform data cleansing on staging tables, rather than external tables.</span></span>

<span data-ttu-id="0b1a3-188">以下的預存程序會從城市人口資料表中移除人口數為零的城市。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-188">Here is the stored procedure that removes the cities with zero population from the City Population table.</span></span> <span data-ttu-id="0b1a3-189">(您可以在[此處](https://github.com/mspnp/reference-architectures/blob/master/data/enterprise_bi_sqldw_advanced/azure/sqldw_scripts/citypopulation/%5BIntegration%5D.%5BMigrateExternalCityPopulationData%5D.sql)找到來源檔案。)</span><span class="sxs-lookup"><span data-stu-id="0b1a3-189">(You can find the source file [here](https://github.com/mspnp/reference-architectures/blob/master/data/enterprise_bi_sqldw_advanced/azure/sqldw_scripts/citypopulation/%5BIntegration%5D.%5BMigrateExternalCityPopulationData%5D.sql).)</span></span>

```sql
DELETE FROM [Integration].[CityPopulation_Staging]
WHERE RowNumber in (SELECT DISTINCT RowNumber
FROM [Integration].[CityPopulation_Staging]
WHERE POPULATION = 0
GROUP BY RowNumber
HAVING COUNT(RowNumber) = 4)
```

## <a name="external-data-sources"></a><span data-ttu-id="0b1a3-190">外部資料來源</span><span class="sxs-lookup"><span data-stu-id="0b1a3-190">External data sources</span></span>

<span data-ttu-id="0b1a3-191">資料倉儲通常會合併來自多個來源的資料。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-191">Data warehouses often consolidate data from multiple sources.</span></span> <span data-ttu-id="0b1a3-192">此參考架構會載入包含人口統計資料的外部資料來源。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-192">This reference architecture loads an external data source that contains demographics data.</span></span> <span data-ttu-id="0b1a3-193">在 [WorldWideImportersDW](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/wide-world-importers/sample-scripts/polybase) 範例中的 Azure Blob 儲存體內提供了此資料集。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-193">This dataset is available in Azure blob storage as part of the [WorldWideImportersDW](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/wide-world-importers/sample-scripts/polybase) sample.</span></span>

<span data-ttu-id="0b1a3-194">Azure Data Factory 可以使用 [Blob 儲存體連接器](/azure/data-factory/connector-azure-blob-storage)，直接從 Blob 儲存體複製。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-194">Azure Data Factory can copy directly from blob storage, using the [blob storage connector](/azure/data-factory/connector-azure-blob-storage).</span></span> <span data-ttu-id="0b1a3-195">不過，連接器需要連接字串或共用存取簽章，因此不能用來複製具有公用讀取權限的 Blob。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-195">However, the connector requires a connection string or a shared access signature, so it can't be used to copy a blob with public read access.</span></span> <span data-ttu-id="0b1a3-196">因應措施是使用 PolyBase 從 Blob 儲存體建立外部資料表，再將外部資料表複製到 SQL 資料倉儲中。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-196">As a workaround, you can use PolyBase to create an external table over Blob storage and then copy the external tables into SQL Data Warehouse.</span></span>

## <a name="handling-large-binary-data"></a><span data-ttu-id="0b1a3-197">處理大型二進位資料</span><span class="sxs-lookup"><span data-stu-id="0b1a3-197">Handling large binary data</span></span>

<span data-ttu-id="0b1a3-198">在來源資料庫中，城市資料表具有包含[地理](/sql/t-sql/spatial-geography/spatial-types-geography)空間資料類型的「位置」資料行。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-198">In the source database, the Cities table has a Location column that holds a [geography](/sql/t-sql/spatial-geography/spatial-types-geography) spatial data type.</span></span> <span data-ttu-id="0b1a3-199">SQL 資料倉儲原生不支援 **geography** 類型，因此在載入期間會將此欄位轉換成 **varbinary** 類型。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-199">SQL Data Warehouse doesn't support the **geography** type natively, so this field is converted to a **varbinary** type during loading.</span></span> <span data-ttu-id="0b1a3-200">(請參閱[不支援資料類型的因應措施](/azure/sql-data-warehouse/sql-data-warehouse-tables-data-types#unsupported-data-types)。)</span><span class="sxs-lookup"><span data-stu-id="0b1a3-200">(See [Workarounds for unsupported data types](/azure/sql-data-warehouse/sql-data-warehouse-tables-data-types#unsupported-data-types).)</span></span>

<span data-ttu-id="0b1a3-201">不過，PolyBase 支援的最大資料行大小為 `varbinary(8000)`，這表示可能會截斷某些資料。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-201">However, PolyBase supports a maximum column size of `varbinary(8000)`, which means some data could be truncated.</span></span> <span data-ttu-id="0b1a3-202">此問題的因應措施是將資料在匯出期間分成區塊，然後再重組區塊，如下所示：</span><span class="sxs-lookup"><span data-stu-id="0b1a3-202">A workaround for this problem is to break the data up into chunks during export, and then reassemble the chunks, as follows:</span></span>

1. <span data-ttu-id="0b1a3-203">針對「位置」資料行，建立暫時的暫存資料表。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-203">Create a temporary staging table for the Location column.</span></span>

2. <span data-ttu-id="0b1a3-204">針對每座城市，將位置資料分割成 8000 個位元組的區塊，產生每座城市 1 &ndash; N 個資料列。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-204">For each city, split the location data into 8000-byte chunks, resulting in 1 &ndash; N rows for each city.</span></span>

3. <span data-ttu-id="0b1a3-205">若要重組區塊，請使用 T-SQL [PIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot)運算子將資料列轉換成資料行，然後再串連每座城市的資料行值。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-205">To reassemble the chunks, use the T-SQL [PIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot) operator to convert rows into columns and then concatenate the column values for each city.</span></span>

<span data-ttu-id="0b1a3-206">所面臨的挑戰是每座城市會根據地理位置資料的大小，分割成不同數目的資料列。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-206">The challenge is that each city will be split into a different number of rows, depending on the size of geography data.</span></span> <span data-ttu-id="0b1a3-207">每座城市都必須具有相同數目的資料列，PIVOT 運算子才能夠運作。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-207">For the PIVOT operator to work, every city must have the same number of rows.</span></span> <span data-ttu-id="0b1a3-208">為了進行這項作業，T-SQL 查詢 (您可以在 [這裡][MergeLocation]檢視) 藉助一些技巧，用空白值填補了資料列，讓每座城市在樞紐後都具有相同數目的資料行。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-208">To make this work, the T-SQL query (which you can view [here][MergeLocation]) does some tricks to pad out the rows with blank values, so that every city has the same number of columns after the pivot.</span></span> <span data-ttu-id="0b1a3-209">產生的查詢比一次重複一列的迴圈快得多。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-209">The resulting query turns out to be much faster than looping through the rows one at a time.</span></span>

<span data-ttu-id="0b1a3-210">影像資料也使用了相同的方法。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-210">The same approach is used for image data.</span></span>

## <a name="slowly-changing-dimensions"></a><span data-ttu-id="0b1a3-211">緩時變維度</span><span class="sxs-lookup"><span data-stu-id="0b1a3-211">Slowly changing dimensions</span></span>

<span data-ttu-id="0b1a3-212">維度資料相對來說是靜態的，但仍可以變更。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-212">Dimension data is relatively static, but it can change.</span></span> <span data-ttu-id="0b1a3-213">例如，產品可能會指派給不同的產品類別目錄。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-213">For example, a product might get reassigned to a different product category.</span></span> <span data-ttu-id="0b1a3-214">有數種方法可處理緩時變維度。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-214">There are several approaches to handling slowly changing dimensions.</span></span> <span data-ttu-id="0b1a3-215">有一種常用的技巧，稱為[類型 2](https://wikipedia.org/wiki/Slowly_changing_dimension#Type_2:_add_new_row)，是在每次維度變更時新增記錄。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-215">A common technique, called [Type 2](https://wikipedia.org/wiki/Slowly_changing_dimension#Type_2:_add_new_row), is to add a new record whenever a dimension changes.</span></span>

<span data-ttu-id="0b1a3-216">若要實作類型 2 方式，維度資料表會需要可針對給定記錄，指定有效日期範圍的額外資料行。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-216">In order to implement the Type 2 approach, dimension tables need additional columns that specify the effective date range for a given record.</span></span> <span data-ttu-id="0b1a3-217">此外，系統會複製來自來源資料庫的主索引鍵，因此維度資料表必須要有人工的主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-217">Also, primary keys from the source database will be duplicated, so the dimension table must have an artificial primary key.</span></span>

<span data-ttu-id="0b1a3-218">下圖顯示 Dimension.City 資料表。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-218">The following image shows the Dimension.City table.</span></span> <span data-ttu-id="0b1a3-219">`WWI City ID` 資料行是來自來源資料庫的主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-219">The `WWI City ID` column is the primary key from the source database.</span></span> <span data-ttu-id="0b1a3-220">`City Key` 資料行是在 ETL 管線期間所產生的人工索引鍵。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-220">The `City Key` column is an artificial key generated during the ETL pipeline.</span></span> <span data-ttu-id="0b1a3-221">也請注意，資料表有 `Valid From` 和 `Valid To` 資料行，其中定義了每個資料列有效的時間範圍。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-221">Also notice that the table has `Valid From` and `Valid To` columns, which define the range when each row was valid.</span></span> <span data-ttu-id="0b1a3-222">`Valid To` 目前值的等於 ' 9999-12-31'。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-222">Current values have a `Valid To` equal to '9999-12-31'.</span></span>

![城市維度資料表的螢幕擷取畫面](./images/city-dimension-table.png)

<span data-ttu-id="0b1a3-224">這種方法的優點是會保留歷程記錄資料，這對分析來說很有價值。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-224">The advantage of this approach is that it preserves historical data, which can be valuable for analysis.</span></span> <span data-ttu-id="0b1a3-225">不過，這也表示相同的實體會有多個資料列。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-225">However, it also means there will be multiple rows for the same entity.</span></span> <span data-ttu-id="0b1a3-226">例如，以下是符合 `WWI City ID` = 28561 的記錄：</span><span class="sxs-lookup"><span data-stu-id="0b1a3-226">For example, here are the records that match `WWI City ID` = 28561:</span></span>

![城市維度資料表的第二個螢幕擷取畫面](./images/city-dimension-table-2.png)

<span data-ttu-id="0b1a3-228">針對每個「銷售」事實，建議您將事實與「城市」維度資料表中的單一資料列產生關聯，並對應到發票日期。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-228">For each Sales fact, you want to associate that fact with a single row in City dimension table, corresponding to the invoice date.</span></span> <span data-ttu-id="0b1a3-229">請建立具有下列項目的額外資料行，作為 ETL 程序的一部分</span><span class="sxs-lookup"><span data-stu-id="0b1a3-229">As part of the ETL process, create an additional column that</span></span> 

<span data-ttu-id="0b1a3-230">下列 T-SQL 查詢會建立暫存資料表，讓每張發票與來自「城市」維度資料表中正確的「城市索引鍵」產生關聯。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-230">The following T-SQL query creates a temporary table that associates each invoice with the correct City Key from the City dimension table.</span></span>

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

<span data-ttu-id="0b1a3-231">此資料表會用來填入「銷售」事實資料表中的資料行：</span><span class="sxs-lookup"><span data-stu-id="0b1a3-231">This table is used to populate a column in the Sales fact table:</span></span>

```sql
UPDATE [Integration].[Sale_Staging]
SET [Integration].[Sale_Staging].[WWI Customer ID] =  CustomerHolder.[WWI Customer ID]
```

<span data-ttu-id="0b1a3-232">此資料行可讓 Power BI 查詢針對給定的銷售發票，找出正確的「城市」記錄。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-232">This column enables a Power BI query to find the correct City record for a given sales invoice.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="0b1a3-233">安全性考量</span><span class="sxs-lookup"><span data-stu-id="0b1a3-233">Security considerations</span></span>

<span data-ttu-id="0b1a3-234">為了增加安全性，您可以使用[虛擬網路服務端點](/azure/virtual-network/virtual-network-service-endpoints-overview)，讓 Azure 服務資源只連線到您的虛擬網路，以保護其安全。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-234">For additional security, you can use [Virtual Network service endpoints](/azure/virtual-network/virtual-network-service-endpoints-overview) to secure Azure service resources to only your virtual network.</span></span> <span data-ttu-id="0b1a3-235">這會完全移除公用網際網路對這些資源的存取權，而只允許來自您虛擬網路的流量。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-235">This fully removes public Internet access to those resources, allowing traffic only from your virtual network.</span></span>

<span data-ttu-id="0b1a3-236">使用此方法，您就可以在 Azure 中建立 VNet，然後再建立 Azure 服務的私用服務端點。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-236">With this approach, you create a VNet in Azure and then create private service endpoints for Azure services.</span></span> <span data-ttu-id="0b1a3-237">接著這些服務會限制為只允許來自該虛擬網路的流量。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-237">Those services are then restricted to traffic from that virtual network.</span></span> <span data-ttu-id="0b1a3-238">您也可以透過閘道，從您的內部部署網路存取這些服務。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-238">You can also reach them from your on-premises network through a gateway.</span></span>

<span data-ttu-id="0b1a3-239">請留意以下限制：</span><span class="sxs-lookup"><span data-stu-id="0b1a3-239">Be aware of the following limitations:</span></span>

- <span data-ttu-id="0b1a3-240">在此參考架構建立時，Azure 儲存體和 Azure SQL 資料倉儲會支援 VNet 服務端點，但 Azure Analysis Service 則不支援。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-240">At the time this reference architecture was created, VNet service endpoints are supported for Azure Storage and Azure SQL Data Warehouse, but not for Azure Analysis Service.</span></span> <span data-ttu-id="0b1a3-241">請在[此處](https://azure.microsoft.com/updates/?product=virtual-network)檢查最新狀態。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-241">Check the latest status [here](https://azure.microsoft.com/updates/?product=virtual-network).</span></span>

- <span data-ttu-id="0b1a3-242">若啟用了 Azure 儲存體的服務端點，PolyBase 就無法從儲存體將資料複製到 SQL 資料倉儲中。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-242">If service endpoints are enabled for Azure Storage, PolyBase cannot copy data from Storage into SQL Data Warehouse.</span></span> <span data-ttu-id="0b1a3-243">此問題沒有緩和措施。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-243">There is a mitigation for this issue.</span></span> <span data-ttu-id="0b1a3-244">如需詳細資訊，請參閱[使用 VNet 服務端點搭配 Azure 儲存體的影響](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fvirtual-network%2ftoc.json#impact-of-using-vnet-service-endpoints-with-azure-storage)。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-244">For more information, see [Impact of using VNet Service Endpoints with Azure storage](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fvirtual-network%2ftoc.json#impact-of-using-vnet-service-endpoints-with-azure-storage).</span></span>

- <span data-ttu-id="0b1a3-245">若要將資料從內部部署移動到 Azure 儲存體中，您需要將來自內部部署或 ExpressRoute 的公用 IP 位址，列入允許清單中。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-245">To move data from on-premises into Azure Storage, you will need to whitelist public IP addresses from your on-premises or ExpressRoute.</span></span> <span data-ttu-id="0b1a3-246">欲知詳情，請參閱[將 Azure 服務放到虛擬網路保護](/azure/virtual-network/virtual-network-service-endpoints-overview#securing-azure-services-to-virtual-networks)。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-246">For details, see [Securing Azure services to virtual networks](/azure/virtual-network/virtual-network-service-endpoints-overview#securing-azure-services-to-virtual-networks).</span></span>

- <span data-ttu-id="0b1a3-247">若要讓 Analysis Services 可從 SQL 資料倉儲中讀取資料，請將 Windows VM 部署到包含 SQL 資料倉儲服務端點的虛擬網路中。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-247">To enable Analysis Services to read data from SQL Data Warehouse, deploy a Windows VM to the virtual network that contains the SQL Data Warehouse service endpoint.</span></span> <span data-ttu-id="0b1a3-248">在 VM 上安裝 [Azure 內部部署資料閘道](/azure/analysis-services/analysis-services-gateway)。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-248">Install [Azure On-premises Data Gateway](/azure/analysis-services/analysis-services-gateway) on this VM.</span></span> <span data-ttu-id="0b1a3-249">然後將您的 Azure 分析服務連線到資料閘道。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-249">Then connect your Azure Analysis service to the data gateway.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="0b1a3-250">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="0b1a3-250">Deploy the solution</span></span>

<span data-ttu-id="0b1a3-251">若要部署及執行參考實作，請依照 [GitHub 讀我檔案][github]中的步驟。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-251">To the deploy and run the reference implementation, follow the steps in the [GitHub readme][github].</span></span> <span data-ttu-id="0b1a3-252">它會部署下列各項：</span><span class="sxs-lookup"><span data-stu-id="0b1a3-252">It deploys the following:</span></span>

- <span data-ttu-id="0b1a3-253">一個 Windows VM，用以模擬內部部署資料庫伺服器。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-253">A Windows VM to simulate an on-premises database server.</span></span> <span data-ttu-id="0b1a3-254">其中包含 SQL Server 2017 和相關工具以及 Power BI Desktop。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-254">It includes SQL Server 2017 and related tools, along with Power BI Desktop.</span></span>
- <span data-ttu-id="0b1a3-255">一個提供 Blob 儲存體的 Azure 儲存體帳戶，用以保存從 SQL Server 資料庫匯出的資料。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-255">An Azure storage account that provides Blob storage to hold data exported from the SQL Server database.</span></span>
- <span data-ttu-id="0b1a3-256">一個 Azure SQL 資料倉儲執行個體。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-256">An Azure SQL Data Warehouse instance.</span></span>
- <span data-ttu-id="0b1a3-257">一個 Azure Analysis Services 執行個體。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-257">An Azure Analysis Services instance.</span></span>
- <span data-ttu-id="0b1a3-258">Azure Data Factory 和 ELT 作業的 Data Factory 管線。</span><span class="sxs-lookup"><span data-stu-id="0b1a3-258">Azure Data Factory and the Data Factory pipeline for the ELT job.</span></span>

## <a name="related-resources"></a><span data-ttu-id="0b1a3-259">相關資源</span><span class="sxs-lookup"><span data-stu-id="0b1a3-259">Related resources</span></span>

<span data-ttu-id="0b1a3-260">您可以檢閱下列 [Azure 範例案例](/azure/architecture/example-scenario)，其中示範使用相同技術的一些特定解決方案：</span><span class="sxs-lookup"><span data-stu-id="0b1a3-260">You may want to review the following [Azure example scenarios](/azure/architecture/example-scenario) that demonstrate specific solutions using some of the same technologies:</span></span>

- [<span data-ttu-id="0b1a3-261">適用於銷售和行銷的資料倉儲和分析</span><span class="sxs-lookup"><span data-stu-id="0b1a3-261">Data warehousing and analytics for sales and marketing</span></span>](/azure/architecture/example-scenario/data/data-warehouse)
- [<span data-ttu-id="0b1a3-262">使用現有內部部署 SSIS 和 Azure Data Factory 的混合式 ETL</span><span class="sxs-lookup"><span data-stu-id="0b1a3-262">Hybrid ETL with existing on-premises SSIS and Azure Data Factory</span></span>](/azure/architecture/example-scenario/data/hybrid-etl-with-adf)

<!-- links -->

[adf]: /azure/data-factory
[github]: https://github.com/mspnp/azure-data-factory-sqldw-elt-pipeline
[MergeLocation]: https://github.com/mspnp/reference-architectures/blob/master/data/enterprise_bi_sqldw_advanced/azure/sqldw_scripts/city/%5BIntegration%5D.%5BMergeLocation%5D.sql
[wwi]: /sql/sample/world-wide-importers/wide-world-importers-oltp-database

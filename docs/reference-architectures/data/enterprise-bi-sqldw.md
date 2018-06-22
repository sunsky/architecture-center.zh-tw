---
title: 具 SQL 資料倉儲的 Enterprise BI
description: 使用 Azure 從儲存在內部部署的關聯式資料取得商業見解
author: alexbuckgit
ms.date: 04/13/2018
ms.openlocfilehash: b5e5aa32fc9cc8c7b8b5a42c9a4fc3e0216b2f72
ms.sourcegitcommit: f665226cec96ec818ca06ac6c2d83edb23c9f29c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
ms.locfileid: "31012834"
---
# <a name="enterprise-bi-with-sql-data-warehouse"></a><span data-ttu-id="1c21a-103">具 SQL 資料倉儲的 Enterprise BI</span><span class="sxs-lookup"><span data-stu-id="1c21a-103">Enterprise BI with SQL Data Warehouse</span></span>
 
<span data-ttu-id="1c21a-104">此參考架構會實作 [ELT](../../data-guide/relational-data/etl.md#extract-load-and-transform-elt) (擷取-載入-轉換) 管線，將資料從內部部署 SQL Server 資料庫移至 SQL 資料倉儲，並轉換資料以供分析。</span><span class="sxs-lookup"><span data-stu-id="1c21a-104">This reference architecture implements an [ELT](../../data-guide/relational-data/etl.md#extract-load-and-transform-elt) (extract-load-transform) pipeline that moves data from an on-premises SQL Server database into SQL Data Warehouse and transforms the data for analysis.</span></span> [<span data-ttu-id="1c21a-105">**部署這個解決方案**。</span><span class="sxs-lookup"><span data-stu-id="1c21a-105">**Deploy this solution**.</span></span>](#deploy-the-solution)

![](./images/enterprise-bi-sqldw.png)

<span data-ttu-id="1c21a-106">**案例**：組織具有在內部部署的 SQL Server 資料庫中儲存的大型 OLTP 資料集。</span><span class="sxs-lookup"><span data-stu-id="1c21a-106">**Scenario**: An organization has a large OLTP data set stored in a SQL Server database on premises.</span></span> <span data-ttu-id="1c21a-107">組織想要使用 SQL 資料倉儲，透過 Power BI 執行分析。</span><span class="sxs-lookup"><span data-stu-id="1c21a-107">The organization wants to use SQL Data Warehouse to perform analysis using Power BI.</span></span> 

<span data-ttu-id="1c21a-108">此參考架構是針對一次性或隨需作業而設計的。</span><span class="sxs-lookup"><span data-stu-id="1c21a-108">This reference architecture is designed for one-time or on-demand jobs.</span></span> <span data-ttu-id="1c21a-109">如果您需要持續移動資料 (每小時或每日)，建議您使用 Azure Data Factory 來定義自動化工作流程。</span><span class="sxs-lookup"><span data-stu-id="1c21a-109">If you need to move data on a continuing basis (hourly or daily), we recommend using Azure Data Factory to define an automated workflow.</span></span>

## <a name="architecture"></a><span data-ttu-id="1c21a-110">架構</span><span class="sxs-lookup"><span data-stu-id="1c21a-110">Architecture</span></span>

<span data-ttu-id="1c21a-111">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="1c21a-111">The architecture consists of the following components.</span></span>

<span data-ttu-id="1c21a-112">**SQL Server**。</span><span class="sxs-lookup"><span data-stu-id="1c21a-112">**SQL Server**.</span></span> <span data-ttu-id="1c21a-113">來源資料位於內部部署的 SQL Server 資料庫中。</span><span class="sxs-lookup"><span data-stu-id="1c21a-113">The source data is located in a SQL Server database on premises.</span></span> <span data-ttu-id="1c21a-114">為了模擬內部部署環境，此架構的部署指令碼會在 Azure 中佈建已安裝 SQL Server 的虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="1c21a-114">To simulate the on-premises environment, the deployment scripts for this architecture provision a virtual machine in Azure with SQL Server installed.</span></span> 

<span data-ttu-id="1c21a-115">**Blob 儲存體**。</span><span class="sxs-lookup"><span data-stu-id="1c21a-115">**Blob Storage**.</span></span> <span data-ttu-id="1c21a-116">Blob 儲存體會作為在資料載入至 SQL 資料倉儲之前用來複製資料的臨時區域。</span><span class="sxs-lookup"><span data-stu-id="1c21a-116">Blob storage is used as a staging area to copy the data before loading it into SQL Data Warehouse.</span></span>

<span data-ttu-id="1c21a-117">**Azure SQL 資料倉儲**。</span><span class="sxs-lookup"><span data-stu-id="1c21a-117">**Azure SQL Data Warehouse**.</span></span> <span data-ttu-id="1c21a-118">[SQL 資料倉儲](/azure/sql-data-warehouse/)是為了對大型資料執行分析而設計的分散式系統。</span><span class="sxs-lookup"><span data-stu-id="1c21a-118">[SQL Data Warehouse](/azure/sql-data-warehouse/) is a distributed system designed to perform analytics on large data.</span></span> <span data-ttu-id="1c21a-119">它支援大量平行處理 (MPP)，因而適合用來執行高效能分析。</span><span class="sxs-lookup"><span data-stu-id="1c21a-119">It supports massive parallel processing (MPP), which makes it suitable for running high-performance analytics.</span></span> 

<span data-ttu-id="1c21a-120">**Azure Analysis Services**。</span><span class="sxs-lookup"><span data-stu-id="1c21a-120">**Azure Analysis Services**.</span></span> <span data-ttu-id="1c21a-121">[Analysis Services](/azure/analysis-services/) 是完全受控的服務，可提供資料模型功能。</span><span class="sxs-lookup"><span data-stu-id="1c21a-121">[Analysis Services](/azure/analysis-services/) is a fully managed service that provides data modeling capabilities.</span></span> <span data-ttu-id="1c21a-122">使用 Analysis Services 可建立可供使用者查詢的語意模型。</span><span class="sxs-lookup"><span data-stu-id="1c21a-122">Use Analysis Services to create a semantic model that users can query.</span></span> <span data-ttu-id="1c21a-123">Analysis Services 在 BI 儀表板案例中最能發揮效用。</span><span class="sxs-lookup"><span data-stu-id="1c21a-123">Analysis Services is especially useful in a BI dashboard scenario.</span></span> <span data-ttu-id="1c21a-124">在此架構中，Analysis Services 會從資料倉儲讀取資料以處理語意模型，並有效率地為儀表板查詢提供服務。</span><span class="sxs-lookup"><span data-stu-id="1c21a-124">In this architecture, Analysis Services reads data from the data warehouse to process the semantic model, and efficiently serves dashboard queries.</span></span> <span data-ttu-id="1c21a-125">它也可藉由相應放大複本以加快查詢處理速度，而支援彈性的並行存取。</span><span class="sxs-lookup"><span data-stu-id="1c21a-125">It also supports elastic concurrency, by scaling out replicas for faster query processing.</span></span>

<span data-ttu-id="1c21a-126">目前，Azure Analysis Services 支援表格式模型，但不支援多維度模型。</span><span class="sxs-lookup"><span data-stu-id="1c21a-126">Currently, Azure Analysis Services supports tabular models but not multidimensional models.</span></span> <span data-ttu-id="1c21a-127">表格式模型使用關聯式模型建構 (資料表和資料行)，而多維度模型則使用 OLAP 模型建構 (Cube、維度和量值)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-127">Tabular models use relational modeling constructs (tables and columns), whereas multidimensional models use OLAP modeling constructs (cubes, dimensions, and measures).</span></span> <span data-ttu-id="1c21a-128">如果您需要多維度模型，請使用 SQL Server Analysis Services (SSAS)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-128">If you require multidimensional models, use SQL Server Analysis Services (SSAS).</span></span> <span data-ttu-id="1c21a-129">如需詳細資訊，請參閱[比較表格式和多維度解決方案](/sql/analysis-services/comparing-tabular-and-multidimensional-solutions-ssas)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-129">For more information, see [Comparing tabular and multidimensional solutions](/sql/analysis-services/comparing-tabular-and-multidimensional-solutions-ssas).</span></span>

<span data-ttu-id="1c21a-130">**Power BI**。</span><span class="sxs-lookup"><span data-stu-id="1c21a-130">**Power BI**.</span></span> <span data-ttu-id="1c21a-131">Power BI 是一套用來分析資料以產生商業見解的商務分析工具。</span><span class="sxs-lookup"><span data-stu-id="1c21a-131">Power BI is a suite of business analytics tools to analyze data for business insights.</span></span> <span data-ttu-id="1c21a-132">在此架構中，它會查詢儲存在 Analysis Services 中的語意模型。</span><span class="sxs-lookup"><span data-stu-id="1c21a-132">In this architecture, it queries the semantic model stored in Analysis Services.</span></span>

<span data-ttu-id="1c21a-133">**Azure Active Directory** (Azure AD) 會驗證透過 Power BI 連線至 Analysis Services 伺服器的使用者。</span><span class="sxs-lookup"><span data-stu-id="1c21a-133">**Azure Active Directory** (Azure AD) authenticates users who connect to the Analysis Services server through Power BI.</span></span>

## <a name="data-pipeline"></a><span data-ttu-id="1c21a-134">Data Pipeline</span><span class="sxs-lookup"><span data-stu-id="1c21a-134">Data Pipeline</span></span>
 
<span data-ttu-id="1c21a-135">此參考架構會使用 [WorldWideImporters](/sql/sample/world-wide-importers/wide-world-importers-oltp-database) 範例資料庫做作為資料來源。</span><span class="sxs-lookup"><span data-stu-id="1c21a-135">This reference architecture uses the [WorldWideImporters](/sql/sample/world-wide-importers/wide-world-importers-oltp-database) sample database as data source.</span></span> <span data-ttu-id="1c21a-136">資料管線具有下列階段：</span><span class="sxs-lookup"><span data-stu-id="1c21a-136">The data pipeline has the following stages:</span></span>

1. <span data-ttu-id="1c21a-137">將資料從 SQL Server 匯出至一般檔案 (bcp 公用程式)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-137">Export the data from SQL Server to flat files (bcp utility).</span></span>
2. <span data-ttu-id="1c21a-138">將一般檔案複製到 Azure Blob 儲存體 (AzCopy)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-138">Copy the flat files to Azure Blob Storage (AzCopy).</span></span>
3. <span data-ttu-id="1c21a-139">將資料載入 SQL 資料倉儲中 (PolyBase)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-139">Load the data into SQL Data Warehouse (PolyBase).</span></span>
4. <span data-ttu-id="1c21a-140">將資料轉換為星型結構描述 (T-SQL)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-140">Transform the data into a star schema (T-SQL).</span></span>
5. <span data-ttu-id="1c21a-141">將語意模型載入 Analysis Services 中 (SQL Server Data Tools)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-141">Load a semantic model into Analysis Services (SQL Server Data Tools).</span></span>

![](./images/enterprise-bi-sqldw-pipeline.png)
 
> [!NOTE]
> <span data-ttu-id="1c21a-142">在步驟 1 &ndash; 3 中，請考慮使用 Redgate Data Platform Studio。</span><span class="sxs-lookup"><span data-stu-id="1c21a-142">For steps 1 &ndash; 3, consider using Redgate Data Platform Studio.</span></span> <span data-ttu-id="1c21a-143">Data Platform Studio 會套用最適當的相容性修正檔與最佳化，因此它是開始使用 SQL 資料倉儲最快的方式。</span><span class="sxs-lookup"><span data-stu-id="1c21a-143">Data Platform Studio applies the most appropriate compatibility fixes and optimizations, so it's the quickest way to get started with SQL Data Warehouse.</span></span> <span data-ttu-id="1c21a-144">如需詳細資訊，請參閱[使用 Redgate Data Platform Studio 載入資料](/azure/sql-data-warehouse/sql-data-warehouse-load-with-redgate)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-144">For more information, see [Load data with Redgate Data Platform Studio](/azure/sql-data-warehouse/sql-data-warehouse-load-with-redgate).</span></span> 

<span data-ttu-id="1c21a-145">以下幾節將詳細說明這些階段。</span><span class="sxs-lookup"><span data-stu-id="1c21a-145">The next sections describe these stages in more detail.</span></span>

### <a name="export-data-from-sql-server"></a><span data-ttu-id="1c21a-146">從 SQL Server 匯入資料</span><span class="sxs-lookup"><span data-stu-id="1c21a-146">Export data from SQL Server</span></span>

<span data-ttu-id="1c21a-147">[bcp](/sql/tools/bcp-utility) (大量複製程式) 公用程式是從 SQL 資料表建立一般文字檔的快速途徑。</span><span class="sxs-lookup"><span data-stu-id="1c21a-147">The [bcp](/sql/tools/bcp-utility) (bulk copy program) utility is a fast way to create flat text files from SQL tables.</span></span> <span data-ttu-id="1c21a-148">在此步驟中，您會選取您要匯出的資料行，但不會轉換資料。</span><span class="sxs-lookup"><span data-stu-id="1c21a-148">In this step, you select the columns that you want to export, but don't transform the data.</span></span> <span data-ttu-id="1c21a-149">任何資料轉換均應在 SQL 資料倉儲中執行。</span><span class="sxs-lookup"><span data-stu-id="1c21a-149">Any data transformations should happen in SQL Data Warehouse.</span></span>

<span data-ttu-id="1c21a-150">**建議**</span><span class="sxs-lookup"><span data-stu-id="1c21a-150">**Recommendations**</span></span>

<span data-ttu-id="1c21a-151">可能的話，請將資料擷取排程於離峰時間，以盡可能避免生產環境中的資源爭用。</span><span class="sxs-lookup"><span data-stu-id="1c21a-151">If possible, schedule data extraction during off-peak hours, to minimize resource contention in the production environment.</span></span> 

<span data-ttu-id="1c21a-152">請避免在資料庫伺服器上執行 bcp。</span><span class="sxs-lookup"><span data-stu-id="1c21a-152">Avoid running bcp on the database server.</span></span> <span data-ttu-id="1c21a-153">改以其他機器來執行。</span><span class="sxs-lookup"><span data-stu-id="1c21a-153">Instead, run it from another machine.</span></span> <span data-ttu-id="1c21a-154">請將檔案寫入至本機磁碟機。</span><span class="sxs-lookup"><span data-stu-id="1c21a-154">Write the files to a local drive.</span></span> <span data-ttu-id="1c21a-155">請確定您有足夠的 I/O 資源可處理並行寫入。</span><span class="sxs-lookup"><span data-stu-id="1c21a-155">Ensure that you have sufficient I/O resources to handle the concurrent writes.</span></span> <span data-ttu-id="1c21a-156">為了達到最佳效能，請將檔案匯出至專用的快速儲存磁碟機。</span><span class="sxs-lookup"><span data-stu-id="1c21a-156">For best performance, export the files to dedicated fast storage drives.</span></span>

<span data-ttu-id="1c21a-157">您可以用 Gzip 壓縮格式儲存匯出的資料，以加快網路傳輸速度。</span><span class="sxs-lookup"><span data-stu-id="1c21a-157">You can speed up the network transfer by saving the exported data in Gzip compressed format.</span></span> <span data-ttu-id="1c21a-158">不過，壓縮的檔案載入至倉儲的速度會低於非壓縮檔案的載入速度，因此，您必須在網路傳輸速度與載入速度之間做出取捨。</span><span class="sxs-lookup"><span data-stu-id="1c21a-158">However, loading compressed files into the warehouse is slower than loading uncompressed files, so there is a tradeoff between faster network transfer versus faster loading.</span></span> <span data-ttu-id="1c21a-159">如果您決定使用 Gzip 壓縮，請勿建立單一 Gzip 檔案。</span><span class="sxs-lookup"><span data-stu-id="1c21a-159">If you decide to use Gzip compression, don't create a single Gzip file.</span></span> <span data-ttu-id="1c21a-160">此時，請將資料分成多個壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="1c21a-160">Instead, split the data into multiple compressed files.</span></span>

### <a name="copy-flat-files-into-blob-storage"></a><span data-ttu-id="1c21a-161">將一般檔案複製到 Blob 儲存體中</span><span class="sxs-lookup"><span data-stu-id="1c21a-161">Copy flat files into blob storage</span></span>

<span data-ttu-id="1c21a-162">[AzCopy](/azure/storage/common/storage-use-azcopy) 公用程式可讓您以高效能將資料複製到 Azure Blob 儲存體中。</span><span class="sxs-lookup"><span data-stu-id="1c21a-162">The [AzCopy](/azure/storage/common/storage-use-azcopy) utility is designed for high-performance copying of data into Azure blob storage.</span></span>

<span data-ttu-id="1c21a-163">**建議**</span><span class="sxs-lookup"><span data-stu-id="1c21a-163">**Recommendations**</span></span>

<span data-ttu-id="1c21a-164">儲存體帳戶請建立在來源資料所在位置的鄰近區域中。</span><span class="sxs-lookup"><span data-stu-id="1c21a-164">Create the storage account in a region near the location of the source data.</span></span> <span data-ttu-id="1c21a-165">請將儲存體帳戶和 SQL 資料倉儲執行個體部署在相同區域中。 </span><span class="sxs-lookup"><span data-stu-id="1c21a-165">Deploy the storage account and the SQL Data Warehouse instance in the same region.</span></span> 

<span data-ttu-id="1c21a-166">請勿在執行生產工作負載的相同機器上執行 AzCopy，因為 CPU 和 I/O 的耗用可能會影響到生產工作負載。</span><span class="sxs-lookup"><span data-stu-id="1c21a-166">Don't run AzCopy on the same machine that runs your production workloads, because the CPU and I/O consumption can interfere with the production workload.</span></span> 

<span data-ttu-id="1c21a-167">請先測試上傳，以確認上傳速度。</span><span class="sxs-lookup"><span data-stu-id="1c21a-167">Test the upload first to see what the upload speed is like.</span></span> <span data-ttu-id="1c21a-168">您可以使用 AzCopy 中的 /NC 選項來指定並行複製作業數目。</span><span class="sxs-lookup"><span data-stu-id="1c21a-168">You can use the /NC option in AzCopy to specify the number of concurrent copy operations.</span></span> <span data-ttu-id="1c21a-169">一開始請先使用預設值，然後再試著以這項設定調整效能。</span><span class="sxs-lookup"><span data-stu-id="1c21a-169">Start with the default value, then experiment with this setting to tune the performance.</span></span> <span data-ttu-id="1c21a-170">在低頻寬環境中，過多的並行作業有可能會拖垮網路連線，而使作業無法順利完成。</span><span class="sxs-lookup"><span data-stu-id="1c21a-170">In a low-bandwidth environment, too many concurrent operations can overwhelm the network connection and prevent the operations from completing successfully.</span></span>  

<span data-ttu-id="1c21a-171">AzCopy 會透過公用網際網路將資料移至儲存體。</span><span class="sxs-lookup"><span data-stu-id="1c21a-171">AzCopy moves data to storage over the public internet.</span></span> <span data-ttu-id="1c21a-172">若其速度不夠快，請考慮設定 [ExpressRoute](/azure/expressroute/) 線路。</span><span class="sxs-lookup"><span data-stu-id="1c21a-172">If this isn't fast enough, consider setting up an [ExpressRoute](/azure/expressroute/) circuit.</span></span> <span data-ttu-id="1c21a-173">ExpressRoute 是一項服務，它會透過專用私人連線將您的資料路由傳送至 Azure。</span><span class="sxs-lookup"><span data-stu-id="1c21a-173">ExpressRoute is a service that routes your data through a dedicated private connection to Azure.</span></span> <span data-ttu-id="1c21a-174">當網路連線速度太慢時，您也可以考慮將磁碟上的資料傳送至 Azure 資料中心。</span><span class="sxs-lookup"><span data-stu-id="1c21a-174">Another option, if your network connection is too slow, is to physically ship the data on disk to an Azure datacenter.</span></span> <span data-ttu-id="1c21a-175">如需詳細資訊，請參閱[從 Azure 來回傳輸資料](/azure/architecture/data-guide/scenarios/data-transfer)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-175">For more information, see [Transferring data to and from Azure](/azure/architecture/data-guide/scenarios/data-transfer).</span></span>

<span data-ttu-id="1c21a-176">在複製作業期間，AzCopy 會建立暫存日誌檔案，讓 AzCopy 能夠在作業中斷 (例如，由於網路錯誤) 時加以重新啟動。</span><span class="sxs-lookup"><span data-stu-id="1c21a-176">During a copy operation, AzCopy creates a temporary journal file, which enables AzCopy to restart the operation if it gets interrupted (for example, due to a network error).</span></span> <span data-ttu-id="1c21a-177">請確定有足夠的磁碟空間可儲存日誌檔案。</span><span class="sxs-lookup"><span data-stu-id="1c21a-177">Make sure there is enough disk space to store the journal files.</span></span> <span data-ttu-id="1c21a-178">您可以使用 /Z 選項來指定日誌檔案的寫入位置。</span><span class="sxs-lookup"><span data-stu-id="1c21a-178">You can use the /Z option to specify where the journal files are written.</span></span>

### <a name="load-data-into-sql-data-warehouse"></a><span data-ttu-id="1c21a-179">將資料載入至 SQL 資料倉儲</span><span class="sxs-lookup"><span data-stu-id="1c21a-179">Load data into SQL Data Warehouse</span></span>

<span data-ttu-id="1c21a-180">使用 [PolyBase](/sql/relational-databases/polybase/polybase-guide) 可將檔案從 Blob 儲存體載入資料倉儲中。</span><span class="sxs-lookup"><span data-stu-id="1c21a-180">Use [PolyBase](/sql/relational-databases/polybase/polybase-guide) to load the files from blob storage into the data warehouse.</span></span> <span data-ttu-id="1c21a-181">PolyBase 依設計會運用 SQL 資料倉儲的 MPP (大量平行處理) 架構，因此能夠以最快的速度將資料載入 SQL 資料倉儲中。</span><span class="sxs-lookup"><span data-stu-id="1c21a-181">PolyBase is designed to leverage the MPP (Massively Parallel Processing) architecture of SQL Data Warehouse, which makes it the fastest way to load data into SQL Data Warehouse.</span></span> 

<span data-ttu-id="1c21a-182">載入資料的程序由兩個步驟組成：</span><span class="sxs-lookup"><span data-stu-id="1c21a-182">Loading the data is a two-step process:</span></span>

1. <span data-ttu-id="1c21a-183">為資料建立一組外部資料表。</span><span class="sxs-lookup"><span data-stu-id="1c21a-183">Create a set of external tables for the data.</span></span> <span data-ttu-id="1c21a-184">外部資料表是一項資料表定義，會指向儲存在倉儲外部的資料 &mdash; 在此案例中，是指 Blob 儲存體中的一般檔案。</span><span class="sxs-lookup"><span data-stu-id="1c21a-184">An external table is a table definition that points to data stored outside of the warehouse &mdash; in this case, the flat files in blob storage.</span></span> <span data-ttu-id="1c21a-185">此步驟不會將任何資料移至倉儲中。</span><span class="sxs-lookup"><span data-stu-id="1c21a-185">This step does not move any data into the warehouse.</span></span>
2. <span data-ttu-id="1c21a-186">建立暫存資料表，並將資料載入暫存資料表中。</span><span class="sxs-lookup"><span data-stu-id="1c21a-186">Create staging tables, and load the data into the staging tables.</span></span> <span data-ttu-id="1c21a-187">此步驟會將資料複製到倉儲中。</span><span class="sxs-lookup"><span data-stu-id="1c21a-187">This step copies the data into the warehouse.</span></span>

<span data-ttu-id="1c21a-188">**建議**</span><span class="sxs-lookup"><span data-stu-id="1c21a-188">**Recommendations**</span></span>

<span data-ttu-id="1c21a-189">當您的資料龐大 (超過 1 TB)，且您的分析工作負載透過平行處理來執行又會比較好的話，請考慮使用 SQL 資料倉儲。</span><span class="sxs-lookup"><span data-stu-id="1c21a-189">Consider SQL Data Warehouse when you have large amounts of data (more than 1 TB) and are running an analytics workload that will benefit from parallelism.</span></span> <span data-ttu-id="1c21a-190">SQL 資料倉儲不適用於 OLTP 工作負載或較小的資料集 (< 250 GB)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-190">SQL Data Warehouse is not a good fit for OLTP workloads or smaller data sets (< 250GB).</span></span> <span data-ttu-id="1c21a-191">對於小於 250 GB 的資料集，請考慮使用 Azure SQL Database 或 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="1c21a-191">For data sets less than 250GB, consider Azure SQL Database or SQL Server.</span></span> <span data-ttu-id="1c21a-192">如需詳細資訊，請參閱[資料倉儲](../../data-guide/relational-data/data-warehousing.md)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-192">For more information, see [Data warehousing](../../data-guide/relational-data/data-warehousing.md).</span></span>

<span data-ttu-id="1c21a-193">建立堆積資料表形式的暫存資料表 (不會編製索引)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-193">Create the staging tables as heap tables, which are not indexed.</span></span> <span data-ttu-id="1c21a-194">建立生產資料表的查詢時將會執行完整資料表掃描，因此不需要編製暫存資料表的索引。</span><span class="sxs-lookup"><span data-stu-id="1c21a-194">The queries that create the production tables will result in a full table scan, so there is no reason to index the staging tables.</span></span>

<span data-ttu-id="1c21a-195">PolyBase 會在倉儲中自動使用平行處理。</span><span class="sxs-lookup"><span data-stu-id="1c21a-195">PolyBase automatically takes advantage of parallelism in the warehouse.</span></span> <span data-ttu-id="1c21a-196">載入效能會在您增加 DWU 時隨之調整。</span><span class="sxs-lookup"><span data-stu-id="1c21a-196">The load performance scales as you increase DWUs.</span></span> <span data-ttu-id="1c21a-197">若要達到最佳效能，請使用單一載入作業。</span><span class="sxs-lookup"><span data-stu-id="1c21a-197">For best performance, use a single load operation.</span></span> <span data-ttu-id="1c21a-198">將輸入資料分成多個區塊，並執行多項並行載入，並不會帶來效能上的優勢。</span><span class="sxs-lookup"><span data-stu-id="1c21a-198">There is no performance benefit to breaking the input data into chunks and running multiple concurrent loads.</span></span>

<span data-ttu-id="1c21a-199">PolyBase 可讀取 Gzip 壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="1c21a-199">PolyBase can read Gzip compressed files.</span></span> <span data-ttu-id="1c21a-200">不過，每個壓縮檔案只會使用一個讀取器，因為解壓縮檔案是單一執行緒作業。</span><span class="sxs-lookup"><span data-stu-id="1c21a-200">However, only a single reader is used per compressed file, because uncompressing the file is a single-threaded operation.</span></span> <span data-ttu-id="1c21a-201">因此，請避免載入單一大型壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="1c21a-201">Therefore, avoid loading a single large compressed file.</span></span> <span data-ttu-id="1c21a-202">此時，請將資料分成多個壓縮檔案，以運用平行處理。</span><span class="sxs-lookup"><span data-stu-id="1c21a-202">Instead, split the data into multiple compressed files, in order to take advantage of parallelism.</span></span> 

<span data-ttu-id="1c21a-203">請留意以下限制：</span><span class="sxs-lookup"><span data-stu-id="1c21a-203">Be aware of the following limitations:</span></span>

- <span data-ttu-id="1c21a-204">PolyBase 支援的資料行大小上限為 `varchar(8000)`、`nvarchar(4000)` 或 `varbinary(8000)`。</span><span class="sxs-lookup"><span data-stu-id="1c21a-204">PolyBase supports a maximum column size of `varchar(8000)`, `nvarchar(4000)`, or `varbinary(8000)`.</span></span> <span data-ttu-id="1c21a-205">如果您有超出這些限制的資料，其中一個選項是在資料匯出時將其分成多個區塊，並等到匯入後再重組區塊。</span><span class="sxs-lookup"><span data-stu-id="1c21a-205">If you have data that exceeds these limits, one option is to break the data up into chunks when you export it, and then reassemble the chunks after import.</span></span> 

- <span data-ttu-id="1c21a-206">PolyBase 會使用固定的資料列結束字元 \n 或新行。</span><span class="sxs-lookup"><span data-stu-id="1c21a-206">PolyBase uses a fixed row terminator of \n or newline.</span></span> <span data-ttu-id="1c21a-207">如果來源資料中出現新行字元，這可能會造成問題。</span><span class="sxs-lookup"><span data-stu-id="1c21a-207">This can cause problems if newline characters appear in the source data.</span></span>

- <span data-ttu-id="1c21a-208">您的來源資料結構描述可能會包含 SQL 資料倉儲中不支援的資料類型。</span><span class="sxs-lookup"><span data-stu-id="1c21a-208">Your source data schema might contain data types that are not supported in SQL Data Warehouse.</span></span>

<span data-ttu-id="1c21a-209">為了因應這些限制，您可以建立會執行必要轉換的預存程序。</span><span class="sxs-lookup"><span data-stu-id="1c21a-209">To work around these limitations, you can create a stored procedure that performs the necessary conversions.</span></span> <span data-ttu-id="1c21a-210">在執行 bcp 時，請參考此預存程序。</span><span class="sxs-lookup"><span data-stu-id="1c21a-210">Reference this stored procedure when you run bcp.</span></span> <span data-ttu-id="1c21a-211">或者，[Redgate 資料平台 Studio](/azure/sql-data-warehouse/sql-data-warehouse-load-with-redgate) 會自動轉換在 SQL 資料倉儲中不支援的資料類型。</span><span class="sxs-lookup"><span data-stu-id="1c21a-211">Alternatively, [Redgate Data Platform Studio](/azure/sql-data-warehouse/sql-data-warehouse-load-with-redgate) automatically converts data types that aren’t supported in SQL Data Warehouse.</span></span>

<span data-ttu-id="1c21a-212">如需詳細資訊，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="1c21a-212">For more information, see the following articles:</span></span>

- <span data-ttu-id="1c21a-213">[將資料載入 Azure SQL 資料倉儲中的最佳做法](/azure/sql-data-warehouse/guidance-for-loading-data)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-213">[Best practices for loading data into Azure SQL Data Warehouse](/azure/sql-data-warehouse/guidance-for-loading-data).</span></span>
- [<span data-ttu-id="1c21a-214">將您的結構描述移轉至 SQL 資料倉儲</span><span class="sxs-lookup"><span data-stu-id="1c21a-214">Migrate your schemas to SQL Data Warehouse</span></span>](/azure/sql-data-warehouse/sql-data-warehouse-migrate-schema)
- [<span data-ttu-id="1c21a-215">定義 SQL 資料倉儲中的資料表資料類型的指引</span><span class="sxs-lookup"><span data-stu-id="1c21a-215">Guidance for defining data types for tables in SQL Data Warehouse</span></span>](/azure/sql-data-warehouse/sql-data-warehouse-tables-data-types)

### <a name="transform-the-data"></a><span data-ttu-id="1c21a-216">轉換資料</span><span class="sxs-lookup"><span data-stu-id="1c21a-216">Transform the data</span></span>

<span data-ttu-id="1c21a-217">轉換資料，並將其移至生產資料表中。</span><span class="sxs-lookup"><span data-stu-id="1c21a-217">Transform the data and move it into production tables.</span></span> <span data-ttu-id="1c21a-218">在此步驟中，資料會轉換成具有維度資料表與事實資料表、且適用於語意模型的星型結構描述。</span><span class="sxs-lookup"><span data-stu-id="1c21a-218">In this step, the data is transformed into a star schema with dimension tables and fact tables, suitable for semantic modeling.</span></span>

<span data-ttu-id="1c21a-219">建立具有叢集資料行存放區索引的生產資料表，以提供最理想的整體查詢效能。</span><span class="sxs-lookup"><span data-stu-id="1c21a-219">Create the production tables with clustered columnstore indexes, which offer the best overall query performance.</span></span> <span data-ttu-id="1c21a-220">資料行存放區索引已針對會掃描多筆記錄的查詢進行最佳化。</span><span class="sxs-lookup"><span data-stu-id="1c21a-220">Columnstore indexes are optimized for queries that scan many records.</span></span> <span data-ttu-id="1c21a-221">資料行存放區索引也不會就單一查閱而執行 (也就是查閱單一資料列)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-221">Columnstore indexes don't perform as well for singleton lookups (that is, looking up a single row).</span></span> <span data-ttu-id="1c21a-222">如果您需要執行頻繁的單一查詢，您可以將非叢集索引新增至資料表。</span><span class="sxs-lookup"><span data-stu-id="1c21a-222">If you need to perform frequent singleton lookups, you can add a non-clustered index to a table.</span></span> <span data-ttu-id="1c21a-223">使用非叢集索引的單一查閱可大幅提升執行速度。</span><span class="sxs-lookup"><span data-stu-id="1c21a-223">Singleton lookups can run significantly faster using a non-clustered index.</span></span> <span data-ttu-id="1c21a-224">不過，相較於 OLTP 工作負載，在資料倉儲案例中通常較不常使用單一查閱。</span><span class="sxs-lookup"><span data-stu-id="1c21a-224">However, singleton lookups are typically less common in data warehouse scenarios than OLTP workloads.</span></span> <span data-ttu-id="1c21a-225">如需詳細資訊，請參閱[在 SQL 資料倉儲中編製資料表的索引](/azure/sql-data-warehouse/sql-data-warehouse-tables-index)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-225">For more information, see [Indexing tables in SQL Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-tables-index).</span></span>

> [!NOTE]
> <span data-ttu-id="1c21a-226">叢集資料行存放區資料表不支援 `varchar(max)`、`nvarchar(max)` 或 `varbinary(max)` 資料類型。</span><span class="sxs-lookup"><span data-stu-id="1c21a-226">Clustered columnstore tables do not support `varchar(max)`, `nvarchar(max)`, or `varbinary(max)` data types.</span></span> <span data-ttu-id="1c21a-227">在此情況下，請考慮使用堆積或叢集索引。</span><span class="sxs-lookup"><span data-stu-id="1c21a-227">In that case, consider a heap or clustered index.</span></span> <span data-ttu-id="1c21a-228">您可以將這些資料行放入個別的資料表中。</span><span class="sxs-lookup"><span data-stu-id="1c21a-228">You might put those columns into a separate table.</span></span>

<span data-ttu-id="1c21a-229">由於範例資料庫並不龐大，因此我們建立的複寫資料表不含分割區。</span><span class="sxs-lookup"><span data-stu-id="1c21a-229">Because the sample database is not very large, we created replicated tables with no partitions.</span></span> <span data-ttu-id="1c21a-230">對於生產工作負載，使用分散式資料表應可改善查詢效能。</span><span class="sxs-lookup"><span data-stu-id="1c21a-230">For production workloads, using distributed tables is likely to improve query performance.</span></span> <span data-ttu-id="1c21a-231">請參閱[在 Azure SQL 資料倉儲中設計分散式資料表的指引](/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-231">See [Guidance for designing distributed tables in Azure SQL Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute).</span></span> <span data-ttu-id="1c21a-232">我們的範例指令碼會使用靜態[資源類別](/azure/sql-data-warehouse/resource-classes-for-workload-management)執行查詢。</span><span class="sxs-lookup"><span data-stu-id="1c21a-232">Our example scripts run the queries using a static [resource class](/azure/sql-data-warehouse/resource-classes-for-workload-management).</span></span>

### <a name="load-the-semantic-model"></a><span data-ttu-id="1c21a-233">載入語意模型</span><span class="sxs-lookup"><span data-stu-id="1c21a-233">Load the semantic model</span></span>

<span data-ttu-id="1c21a-234">將資料載入至 Azure Analysis Services 中的表格式模型。</span><span class="sxs-lookup"><span data-stu-id="1c21a-234">Load the data into a tabular model in Azure Analysis Services.</span></span> <span data-ttu-id="1c21a-235">在此步驟中，您會使用 SQL Server Data Tools (SSDT) 建立語意資料模型。</span><span class="sxs-lookup"><span data-stu-id="1c21a-235">In this step, you create a semantic data model by using SQL Server Data Tools (SSDT).</span></span> <span data-ttu-id="1c21a-236">您也可以從 Power BI Desktop 檔案匯入模型，藉以建立模型。</span><span class="sxs-lookup"><span data-stu-id="1c21a-236">You can also create a model by importing it from a Power BI Desktop file.</span></span> <span data-ttu-id="1c21a-237">SQL 資料倉儲並不支援外部索引鍵，因此您必須將關聯性新增至語意模型，以便在資料表之間進行聯結。</span><span class="sxs-lookup"><span data-stu-id="1c21a-237">Because SQL Data Warehouse does not support foreign keys, you must add the relationships to the semantic model, so that you can join across tables.</span></span>

### <a name="use-power-bi-to-visualize-the-data"></a><span data-ttu-id="1c21a-238">使用 Power BI 以視覺方式呈現資料</span><span class="sxs-lookup"><span data-stu-id="1c21a-238">Use Power BI to visualize the data</span></span>

<span data-ttu-id="1c21a-239">Power BI 支援兩個連線至 Azure Analysis Services 的選項：</span><span class="sxs-lookup"><span data-stu-id="1c21a-239">Power BI supports two options for connecting to Azure Analysis Services:</span></span>

- <span data-ttu-id="1c21a-240">匯入。</span><span class="sxs-lookup"><span data-stu-id="1c21a-240">Import.</span></span> <span data-ttu-id="1c21a-241">資料會匯入 Power BI 模型中。</span><span class="sxs-lookup"><span data-stu-id="1c21a-241">The data is imported into the Power BI model.</span></span>
- <span data-ttu-id="1c21a-242">即時連線。</span><span class="sxs-lookup"><span data-stu-id="1c21a-242">Live Connection.</span></span> <span data-ttu-id="1c21a-243">資料會直接提取自 Analysis Services。</span><span class="sxs-lookup"><span data-stu-id="1c21a-243">Data is pulled directly from Analysis Services.</span></span>

<span data-ttu-id="1c21a-244">建議您使用即時連線，因為它不需要將資料複製到 Power BI 模型中。</span><span class="sxs-lookup"><span data-stu-id="1c21a-244">We recommend Live Connection because it doesn't require copying data into the Power BI model.</span></span> <span data-ttu-id="1c21a-245">此外，使用 DirectQuery 可確保結果絕對會與最新的來源資料一致。</span><span class="sxs-lookup"><span data-stu-id="1c21a-245">Also, using DirectQuery ensures that results are always consistent with the latest source data.</span></span> <span data-ttu-id="1c21a-246">如需詳細資訊，請參閱[使用 Power BI 進行連線](/azure/analysis-services/analysis-services-connect-pbi)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-246">For more information, see [Connect with Power BI](/azure/analysis-services/analysis-services-connect-pbi).</span></span>

<span data-ttu-id="1c21a-247">**建議**</span><span class="sxs-lookup"><span data-stu-id="1c21a-247">**Recommendations**</span></span>

<span data-ttu-id="1c21a-248">請避免直接對資料倉儲執行 BI 儀表板查詢。</span><span class="sxs-lookup"><span data-stu-id="1c21a-248">Avoid running BI dashboard queries directly against the data warehouse.</span></span> <span data-ttu-id="1c21a-249">BI 儀表板需要非常短的回應時間，直接對倉儲執行的查詢可能達不到此需求。</span><span class="sxs-lookup"><span data-stu-id="1c21a-249">BI dashboards require very low response times, which direct queries against the warehouse may be unable to satisfy.</span></span> <span data-ttu-id="1c21a-250">此外，重新整理儀表板將會計入並行查詢數目，而可能會影響效能。</span><span class="sxs-lookup"><span data-stu-id="1c21a-250">Also, refreshing the dashboard will count against the number of concurrent queries, which could impact performance.</span></span> 

<span data-ttu-id="1c21a-251">Azure Analysis Services 依設計可用來處理 BI 儀表板的查詢需求，因此，建議的作法是從 Power BI 查詢 Analysis Services。</span><span class="sxs-lookup"><span data-stu-id="1c21a-251">Azure Analysis Services is designed to handle the query requirements of a BI dashboard, so the recommended practice is to query Analysis Services from Power BI.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="1c21a-252">延展性考量</span><span class="sxs-lookup"><span data-stu-id="1c21a-252">Scalability Considerations</span></span>

### <a name="sql-data-warehouse"></a><span data-ttu-id="1c21a-253">SQL 資料倉儲</span><span class="sxs-lookup"><span data-stu-id="1c21a-253">SQL Data Warehouse</span></span>

<span data-ttu-id="1c21a-254">透過 SQL 資料倉儲，您可以隨需相應放大您的計算資源。</span><span class="sxs-lookup"><span data-stu-id="1c21a-254">With SQL Data Warehouse, you can scale out your compute resources on demand.</span></span> <span data-ttu-id="1c21a-255">查詢引擎可根據計算節點的數目最佳化要平行處理的查詢，並視需要在節點之間移動資料。</span><span class="sxs-lookup"><span data-stu-id="1c21a-255">The query engine optimizes queries for parallel processing based on the number of compute nodes, and moves data between nodes as necessary.</span></span> <span data-ttu-id="1c21a-256">如需詳細資訊，請參閱[管理 Azure SQL 資料倉儲中的計算能力](/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-256">For more information, see [Manage compute in Azure SQL Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview).</span></span>

### <a name="analysis-services"></a><span data-ttu-id="1c21a-257">Analysis Services</span><span class="sxs-lookup"><span data-stu-id="1c21a-257">Analysis Services</span></span>

<span data-ttu-id="1c21a-258">對於生產工作負載，建議您使用 Azure Analysis Services 的標準層，因為它支援分割區和 DirectQuery。</span><span class="sxs-lookup"><span data-stu-id="1c21a-258">For production workloads, we recommend the Standard Tier for Azure Analysis Services, because it supports partitioning and DirectQuery.</span></span> <span data-ttu-id="1c21a-259">在一層之中，執行個體大小會決定記憶體和處理能力。</span><span class="sxs-lookup"><span data-stu-id="1c21a-259">Within a tier, the instance size determines the memory and processing power.</span></span> <span data-ttu-id="1c21a-260">處理能力會以查詢處理單位 (QPU) 來測量。</span><span class="sxs-lookup"><span data-stu-id="1c21a-260">Processing power is measured in Query Processing Units (QPUs).</span></span> <span data-ttu-id="1c21a-261">請監視 QPU 使用量以選取適當的大小。</span><span class="sxs-lookup"><span data-stu-id="1c21a-261">Monitor your QPU usage to select the appropriate size.</span></span> <span data-ttu-id="1c21a-262">如需詳細資訊，請參閱[監視伺服器計量](/azure/analysis-services/analysis-services-monitor)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-262">For more information, see [Monitor server metrics](/azure/analysis-services/analysis-services-monitor).</span></span>

<span data-ttu-id="1c21a-263">在高負載的情況下，查詢效能可能會因為查詢並行而下降。</span><span class="sxs-lookup"><span data-stu-id="1c21a-263">Under high load, query performance can become degraded due to query concurrency.</span></span> <span data-ttu-id="1c21a-264">您可以建立用來處理查詢的複本集區以相應放大 Analysis Services，以便同時執行多個查詢。</span><span class="sxs-lookup"><span data-stu-id="1c21a-264">You can scale out Analysis Services by creating a pool of replicas to process queries, so that more queries can be performed concurrently.</span></span> <span data-ttu-id="1c21a-265">處理資料模型的工作一律會在主要伺服器上執行。</span><span class="sxs-lookup"><span data-stu-id="1c21a-265">The work of processing the data model always happens on the primary server.</span></span> <span data-ttu-id="1c21a-266">根據預設，主要伺服器也會處理查詢。</span><span class="sxs-lookup"><span data-stu-id="1c21a-266">By default, the primary server also handles queries.</span></span> <span data-ttu-id="1c21a-267">您可以選擇性地指定以獨佔方式執行處理的主要伺服器，讓查詢集區可處理所有查詢。</span><span class="sxs-lookup"><span data-stu-id="1c21a-267">Optionally, you can designate the primary server to run processing exclusively, so that the query pool handles all queries.</span></span> <span data-ttu-id="1c21a-268">如果您的處理需求較高，則應將處理獨立於查詢集區以外。</span><span class="sxs-lookup"><span data-stu-id="1c21a-268">If you have high processing requirements, you should separate the processing from the query pool.</span></span> <span data-ttu-id="1c21a-269">如果您的查詢負載較高，且處理負載相對較低，則可以將主要伺服器包含在查詢集區中。</span><span class="sxs-lookup"><span data-stu-id="1c21a-269">If you have high query loads, and relatively light processing, you can include the primary server in the query pool.</span></span> <span data-ttu-id="1c21a-270">如需詳細資訊，請參閱 [Azure Analysis Services 相應放大](/azure/analysis-services/analysis-services-scale-out)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-270">For more information, see [Azure Analysis Services scale-out](/azure/analysis-services/analysis-services-scale-out).</span></span> 

<span data-ttu-id="1c21a-271">若要減少非必要的處理量，請考慮使用分割區將表格式模型分成多個邏輯部分。</span><span class="sxs-lookup"><span data-stu-id="1c21a-271">To reduce the amount of unnecessary processing, consider using partitions to divide the tabular model into logical parts.</span></span> <span data-ttu-id="1c21a-272">每個分割區將可個別處理。</span><span class="sxs-lookup"><span data-stu-id="1c21a-272">Each partition can be processed separately.</span></span> <span data-ttu-id="1c21a-273">如需詳細資訊，請參閱[分割區](/sql/analysis-services/tabular-models/partitions-ssas-tabular)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-273">For more information, see [Partitions](/sql/analysis-services/tabular-models/partitions-ssas-tabular).</span></span>

## <a name="security-considerations"></a><span data-ttu-id="1c21a-274">安全性考量</span><span class="sxs-lookup"><span data-stu-id="1c21a-274">Security Considerations</span></span>

### <a name="ip-whitelisting-of-analysis-services-clients"></a><span data-ttu-id="1c21a-275">Analysis Services 用戶端的 IP 白名單</span><span class="sxs-lookup"><span data-stu-id="1c21a-275">IP whitelisting of Analysis Services clients</span></span>

<span data-ttu-id="1c21a-276">請考慮使用 Analysis Services 防火牆功能，將用戶端 IP 位址列入白清單中。</span><span class="sxs-lookup"><span data-stu-id="1c21a-276">Consider using the Analysis Services firewall feature to whitelist client IP addresses.</span></span> <span data-ttu-id="1c21a-277">防火牆啟用時，會封鎖未指定於防火牆規則中的所有用戶端連線。</span><span class="sxs-lookup"><span data-stu-id="1c21a-277">If enabled, the firewall blocks all client connections other than those specified in the firewall rules.</span></span> <span data-ttu-id="1c21a-278">預設規則會將 Power BI 服務列入白名單中，但您可以視需要停用此規則。</span><span class="sxs-lookup"><span data-stu-id="1c21a-278">The default rules whitelist the Power BI service, but you can disable this rule if desired.</span></span> <span data-ttu-id="1c21a-279">如需詳細資訊，請參閱[使用新的防火牆功能強化 Azure Analysis Services](https://azure.microsoft.com/blog/hardening-azure-analysis-services-with-the-new-firewall-capability/)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-279">For more information, see [Hardening Azure Analysis Services with the new firewall capability](https://azure.microsoft.com/blog/hardening-azure-analysis-services-with-the-new-firewall-capability/).</span></span>

### <a name="authorization"></a><span data-ttu-id="1c21a-280">Authorization</span><span class="sxs-lookup"><span data-stu-id="1c21a-280">Authorization</span></span>

<span data-ttu-id="1c21a-281">Azure Analysis Services 會使用 Azure Active Directory (Azure AD) 驗證連線至 Analysis Services 伺服器的使用者。</span><span class="sxs-lookup"><span data-stu-id="1c21a-281">Azure Analysis Services uses Azure Active Directory (Azure AD) to authenticate users who connect to an Analysis Services server.</span></span> <span data-ttu-id="1c21a-282">您可以建立角色，然後將 Azure AD 使用者或群組指派給這些角色，以限制特定使用者所能檢視的資料。</span><span class="sxs-lookup"><span data-stu-id="1c21a-282">You can restrict what data a particular user is able to view, by creating roles and then assigning Azure AD users or groups to those roles.</span></span> <span data-ttu-id="1c21a-283">對於每個角色，您可以︰</span><span class="sxs-lookup"><span data-stu-id="1c21a-283">For each role, you can:</span></span> 

- <span data-ttu-id="1c21a-284">保護資料表或個別資料行。</span><span class="sxs-lookup"><span data-stu-id="1c21a-284">Protect tables or individual columns.</span></span> 
- <span data-ttu-id="1c21a-285">根據篩選運算式保護個別資料列。</span><span class="sxs-lookup"><span data-stu-id="1c21a-285">Protect individual rows based on filter expressions.</span></span> 

<span data-ttu-id="1c21a-286">如需詳細資訊，請參閱[管理資料庫角色和使用者](/azure/analysis-services/analysis-services-database-users)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-286">For more information, see [Manage database roles and users](/azure/analysis-services/analysis-services-database-users).</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="1c21a-287">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="1c21a-287">Deploy the solution</span></span>

<span data-ttu-id="1c21a-288">此參考架構的部署可在 [GitHub][ref-arch-repo-folder] 上取得。</span><span class="sxs-lookup"><span data-stu-id="1c21a-288">A deployment for this reference architecture is available on [GitHub][ref-arch-repo-folder].</span></span> <span data-ttu-id="1c21a-289">它會部署下列各項：</span><span class="sxs-lookup"><span data-stu-id="1c21a-289">It deploys the following:</span></span>

  * <span data-ttu-id="1c21a-290">一個 Windows VM，用以模擬內部部署資料庫伺服器。</span><span class="sxs-lookup"><span data-stu-id="1c21a-290">A Windows VM to simulate an on-premises database server.</span></span> <span data-ttu-id="1c21a-291">其中包含 SQL Server 2017 和相關工具以及 Power BI Desktop。</span><span class="sxs-lookup"><span data-stu-id="1c21a-291">It includes SQL Server 2017 and related tools, along with Power BI Desktop.</span></span>
  * <span data-ttu-id="1c21a-292">一個提供 Blob 儲存體的 Azure 儲存體帳戶，用以保存從 SQL Server 資料庫匯出的資料。</span><span class="sxs-lookup"><span data-stu-id="1c21a-292">An Azure storage account that provides Blob storage to hold data exported from the SQL Server database.</span></span>
  * <span data-ttu-id="1c21a-293">一個 Azure SQL 資料倉儲執行個體。</span><span class="sxs-lookup"><span data-stu-id="1c21a-293">An Azure SQL Data Warehouse instance.</span></span>
  * <span data-ttu-id="1c21a-294">一個 Azure Analysis Services 執行個體。</span><span class="sxs-lookup"><span data-stu-id="1c21a-294">An Azure Analysis Services instance.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="1c21a-295">先決條件</span><span class="sxs-lookup"><span data-stu-id="1c21a-295">Prerequisites</span></span>

1. <span data-ttu-id="1c21a-296">複製、派生或下載適用於 [Azure 參考架構][ref-arch-repo] GitHub 存放庫的 zip 檔案。</span><span class="sxs-lookup"><span data-stu-id="1c21a-296">Clone, fork, or download the zip file for the [Azure reference architectures][ref-arch-repo] GitHub repository.</span></span>

2. <span data-ttu-id="1c21a-297">安裝 [Azure 建置組塊][azbb-wiki] (azbb)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-297">Install the [Azure Building Blocks][azbb-wiki] (azbb).</span></span>

3. <span data-ttu-id="1c21a-298">從命令提示字元、bash 提示字元或 PowerShell 提示字元中，使用下列命令登入 Azure 帳戶，並依照指示操作。</span><span class="sxs-lookup"><span data-stu-id="1c21a-298">From a command prompt, bash prompt, or PowerShell prompt, login to your Azure account by using the command below and following the instructions.</span></span>

  ```bash
  az login  
  ```

### <a name="deploy-the-simulated-on-premises-server"></a><span data-ttu-id="1c21a-299">部署模擬的內部部署伺服器</span><span class="sxs-lookup"><span data-stu-id="1c21a-299">Deploy the simulated on-premises server</span></span>

<span data-ttu-id="1c21a-300">首先，您會將 VM 部署為模擬的內部部署伺服器，其中包含 SQL Server 2017 和相關工具。</span><span class="sxs-lookup"><span data-stu-id="1c21a-300">First you'll deploy a VM as a simulated on-premises server, which includes SQL Server 2017 and related tools.</span></span> <span data-ttu-id="1c21a-301">這個步驟也會將範例 [Wide World Importers OLTP 資料庫](/sql/sample/world-wide-importers/wide-world-importers-oltp-database)載入至 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="1c21a-301">This step also loads the sample [Wide World Importers OLTP database](/sql/sample/world-wide-importers/wide-world-importers-oltp-database) into SQL Server.</span></span>

1. <span data-ttu-id="1c21a-302">瀏覽至您在上述必要條件中下載之存放庫的 `data\enterprise-bi-sqldw\onprem\templates` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="1c21a-302">Navigate to the `data\enterprise-bi-sqldw\onprem\templates` folder of the repository you downloaded in the prerequisites above.</span></span>

2. <span data-ttu-id="1c21a-303">在 `onprem.parameters.json` 檔案中，取代 `adminUsername` 和 `adminPassword` 的值。</span><span class="sxs-lookup"><span data-stu-id="1c21a-303">In the `onprem.parameters.json` file, replace the values for `adminUsername` and `adminPassword`.</span></span> <span data-ttu-id="1c21a-304">此外也應變更 `SqlUserCredentials` 區段中的值，以符合使用者名稱和密碼。</span><span class="sxs-lookup"><span data-stu-id="1c21a-304">Also change the values in the `SqlUserCredentials` section to match the user name and password.</span></span> <span data-ttu-id="1c21a-305">請留意 userName 屬性中的 `.\\` 前置詞。</span><span class="sxs-lookup"><span data-stu-id="1c21a-305">Note the `.\\` prefix in the userName property.</span></span>
    
    ```bash
    "SqlUserCredentials": {
      "userName": ".\\username",
      "password": "password"
    }
    ```

3. <span data-ttu-id="1c21a-306">依照下列方式執行 `azbb`，以部署內部部署伺服器。</span><span class="sxs-lookup"><span data-stu-id="1c21a-306">Run `azbb` as shown below to deploy the on-premises server.</span></span>

    ```bash
    azbb -s <subscription_id> -g <resource_group_name> -l <location> -p onprem.parameters.json --deploy
    ```

4. <span data-ttu-id="1c21a-307">此部署可能需要 20 到 30 分鐘才能完成，其中包括執行 [DSC](/powershell/dsc/overview) 指令碼以安裝工具和還原資料庫。</span><span class="sxs-lookup"><span data-stu-id="1c21a-307">The deployment may take 20 to 30 minutes to complete, which includes running the [DSC](/powershell/dsc/overview) script to install the tools and restore the database.</span></span> <span data-ttu-id="1c21a-308">在 Azure 入口網站中檢閱資源群組中的資源，以驗證部署。</span><span class="sxs-lookup"><span data-stu-id="1c21a-308">Verify the deployment in the Azure portal by reviewing the resources in the resource group.</span></span> <span data-ttu-id="1c21a-309">您應該會看到 `sql-vm1` 虛擬機器及其相關聯的資源。</span><span class="sxs-lookup"><span data-stu-id="1c21a-309">You should see the `sql-vm1` virtual machine and its associated resources.</span></span>

### <a name="deploy-the-azure-resources"></a><span data-ttu-id="1c21a-310">部署 Azure 資源</span><span class="sxs-lookup"><span data-stu-id="1c21a-310">Deploy the Azure resources</span></span>

<span data-ttu-id="1c21a-311">此步驟會佈建 Azure SQL 資料倉儲和 Azure Analysis Services，以及儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="1c21a-311">This step provisions Azure SQL Data Warehouse and Azure Analysis Services, along with a Storage account.</span></span> <span data-ttu-id="1c21a-312">如果您想，您可以將此步驟與上一個步驟平行執行。</span><span class="sxs-lookup"><span data-stu-id="1c21a-312">If you want, you can run this step in parallel with the previous step.</span></span>

1. <span data-ttu-id="1c21a-313">瀏覽至您在上述必要條件中下載之存放庫的 `data\enterprise-bi-sqldw\azure\templates` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="1c21a-313">Navigate to the `data\enterprise-bi-sqldw\azure\templates` folder of the repository you downloaded in the prerequisites above.</span></span>

2. <span data-ttu-id="1c21a-314">執行下列 Azure CLI 命令以建立資源群組 (請取代在方括弧中指定的參數)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-314">Run the following Azure CLI command to create a resource group, replacing the bracketed parameters specified.</span></span> <span data-ttu-id="1c21a-315">請注意，您可以部署至與您在上一個步驟中用於內部部署伺服器的資源群組不同的群組。</span><span class="sxs-lookup"><span data-stu-id="1c21a-315">Note that you can deploy to a different resource group than you used for the on-premises server in the previous step.</span></span> 

    ```bash
    az group create --name <resource_group_name> --location <location>  
    ```

3. <span data-ttu-id="1c21a-316">執行下列 Azure CLI 命令以部署 Azure 資源 (請取代在方括弧中指定的參數)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-316">Run the following Azure CLI command to deploy the Azure resources, replacing the bracketed parameters specified.</span></span> <span data-ttu-id="1c21a-317">`storageAccountName` 參數必須遵循儲存體帳戶的[命名規則](../../best-practices/naming-conventions.md#naming-rules-and-restrictions)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-317">The `storageAccountName` parameter must follow the [naming rules](../../best-practices/naming-conventions.md#naming-rules-and-restrictions) for Storage accounts.</span></span> <span data-ttu-id="1c21a-318">對於 `analysisServerAdmin` 參數，請使用您的 Azure Active Directory 使用者主體名稱 (UPN)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-318">For the `analysisServerAdmin` parameter, use your Azure Active Directory user principal name (UPN).</span></span>

    ```bash
    az group deployment create --resource-group <resource_group_name> --template-file azure-resources-deploy.json --parameters "dwServerName"="<server_name>" "dwAdminLogin"="<admin_username>" "dwAdminPassword"="<password>" "storageAccountName"="<storage_account_name>" "analysisServerName"="<analysis_server_name>" "analysisServerAdmin"="user@contoso.com"
    ```

4. <span data-ttu-id="1c21a-319">在 Azure 入口網站中檢閱資源群組中的資源，以驗證部署。</span><span class="sxs-lookup"><span data-stu-id="1c21a-319">Verify the deployment in the Azure portal by reviewing the resources in the resource group.</span></span> <span data-ttu-id="1c21a-320">您應該會看到儲存體帳戶、Azure SQL 資料倉儲執行個體和 Analysis Services 執行個體。</span><span class="sxs-lookup"><span data-stu-id="1c21a-320">You should see a storage account, Azure SQL Data Warehouse instance, and Analysis Services instance.</span></span>

5. <span data-ttu-id="1c21a-321">使用 Azure 入口網站取得儲存體帳戶的存取金鑰。</span><span class="sxs-lookup"><span data-stu-id="1c21a-321">Use the Azure portal to get the access key for the storage account.</span></span> <span data-ttu-id="1c21a-322">選取要開啟的儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="1c21a-322">Select the storage account to open it.</span></span> <span data-ttu-id="1c21a-323">在 [設定] 底下，選取 [存取金鑰]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-323">Under **Settings**, select **Access keys**.</span></span> <span data-ttu-id="1c21a-324">複製主要金鑰值。</span><span class="sxs-lookup"><span data-stu-id="1c21a-324">Copy the primary key value.</span></span> <span data-ttu-id="1c21a-325">您在下一個步驟將會用到此值。</span><span class="sxs-lookup"><span data-stu-id="1c21a-325">You will use it in the next step.</span></span>

### <a name="export-the-source-data-to-azure-blob-storage"></a><span data-ttu-id="1c21a-326">將來源資料匯出至 Azure Blob 儲存體</span><span class="sxs-lookup"><span data-stu-id="1c21a-326">Export the source data to Azure Blob storage</span></span> 

<span data-ttu-id="1c21a-327">在此步驟中，您將執行 PowerShell 指令碼以使用 bcp 將 SQL 資料庫匯出至 VM 上的一般檔案，然後使用 AzCopy 將這些檔案複製到 Azure Blob 儲存體中。</span><span class="sxs-lookup"><span data-stu-id="1c21a-327">In this step, you will run a PowerShell script that uses bcp to export the SQL database to flat files on the VM, and then uses AzCopy to copy those files into Azure Blob Storage.</span></span>

1. <span data-ttu-id="1c21a-328">使用遠端桌面連線至模擬的內部部署 VM。</span><span class="sxs-lookup"><span data-stu-id="1c21a-328">Use Remote Desktop to connect to the simulated on-premises VM.</span></span>

2. <span data-ttu-id="1c21a-329">登入 VM 後，從 PowerShell 視窗執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="1c21a-329">While logged into the VM, run the following commands from a PowerShell window.</span></span>  

    ```powershell
    cd 'C:\SampleDataFiles\reference-architectures\data\enterprise_bi_sqldw\onprem'

    .\Load_SourceData_To_Blob.ps1 -File .\sql_scripts\db_objects.txt -Destination 'https://<storage_account_name>.blob.core.windows.net/wwi' -StorageAccountKey '<storage_account_key>'
    ```

    <span data-ttu-id="1c21a-330">針對 `Destination` 參數，請將 `<storage_account_name>` 取代為您先前建立的儲存體帳戶的名稱。</span><span class="sxs-lookup"><span data-stu-id="1c21a-330">For the `Destination` parameter, replace `<storage_account_name>` with the name the Storage account that you created previously.</span></span> <span data-ttu-id="1c21a-331">針對 `StorageAccountKey` 參數，請使用該儲存體帳戶的存取金鑰。</span><span class="sxs-lookup"><span data-stu-id="1c21a-331">For the `StorageAccountKey` parameter, use the access key for that Storage account.</span></span>

3. <span data-ttu-id="1c21a-332">在 Azure 入口網站中，瀏覽至儲存體帳戶、選取 Blob 服務，並開啟 `wwi` 容器，以確認來源資料已複製到 Blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="1c21a-332">In the Azure portal, verify that the source data was copied to Blob storage by navigating to the storage account, selecting the Blob service, and opening the `wwi` container.</span></span> <span data-ttu-id="1c21a-333">您應該會看到以 `WorldWideImporters_Application_*` 開頭的資料表清單。</span><span class="sxs-lookup"><span data-stu-id="1c21a-333">You should see a list of tables prefaced with `WorldWideImporters_Application_*`.</span></span>

### <a name="execute-the-data-warehouse-scripts"></a><span data-ttu-id="1c21a-334">執行資料倉儲指令碼</span><span class="sxs-lookup"><span data-stu-id="1c21a-334">Execute the data warehouse scripts</span></span>

1. <span data-ttu-id="1c21a-335">在您的遠端桌面工作階段中，啟動 SQL Server Management Studio (SSMS)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-335">From your Remote Desktop session, launch SQL Server Management Studio (SSMS).</span></span> 

2. <span data-ttu-id="1c21a-336">連線到 SQL 資料倉儲</span><span class="sxs-lookup"><span data-stu-id="1c21a-336">Connect to SQL Data Warehouse</span></span>

    - <span data-ttu-id="1c21a-337">伺服器類型：資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="1c21a-337">Server type: Database Engine</span></span>
    
    - <span data-ttu-id="1c21a-338">伺服器名稱：`<dwServerName>.database.windows.net`，其中，`<dwServerName>` 是您在部署 Azure 資源時所指定的名稱。</span><span class="sxs-lookup"><span data-stu-id="1c21a-338">Server name: `<dwServerName>.database.windows.net`, where `<dwServerName>` is the name that you specified when you deployed the Azure resources.</span></span> <span data-ttu-id="1c21a-339">您可以從 Azure 入口網站中取得此名稱。</span><span class="sxs-lookup"><span data-stu-id="1c21a-339">You can get this name from the Azure portal.</span></span>
    
    - <span data-ttu-id="1c21a-340">驗證：SQL Server 驗證。</span><span class="sxs-lookup"><span data-stu-id="1c21a-340">Authentication: SQL Server Authentication.</span></span> <span data-ttu-id="1c21a-341">請使用您在部署 Azure 資源時指定於 `dwAdminLogin` 和 `dwAdminPassword` 參數中的認證。</span><span class="sxs-lookup"><span data-stu-id="1c21a-341">Use the credentials that you specified when you deployed the Azure resources, in the `dwAdminLogin` and `dwAdminPassword` parameters.</span></span>

2. <span data-ttu-id="1c21a-342">瀏覽至 VM 上的 `C:\SampleDataFiles\reference-architectures\data\enterprise_bi_sqldw\azure\sqldw_scripts` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="1c21a-342">Navigate to the `C:\SampleDataFiles\reference-architectures\data\enterprise_bi_sqldw\azure\sqldw_scripts` folder on the VM.</span></span> <span data-ttu-id="1c21a-343">您將依數值順序執行此資料夾中的指令碼 `STEP_1` 到 `STEP_7`。</span><span class="sxs-lookup"><span data-stu-id="1c21a-343">You will execute the scripts in this folder in numerical order, `STEP_1` through `STEP_7`.</span></span>

3. <span data-ttu-id="1c21a-344">在 SSMS 中選取 `master` 資料庫，並開啟 `STEP_1` 指令碼。</span><span class="sxs-lookup"><span data-stu-id="1c21a-344">Select the `master` database in SSMS and open the `STEP_1` script.</span></span> <span data-ttu-id="1c21a-345">請變更以下這一行中的密碼值，然後執行指令碼。</span><span class="sxs-lookup"><span data-stu-id="1c21a-345">Change the value of the password in the following line, then execute the script.</span></span>

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = '<change this value>';
    ```

4. <span data-ttu-id="1c21a-346">在 SSMS 中選取 `wwi` 資料庫。</span><span class="sxs-lookup"><span data-stu-id="1c21a-346">Select the `wwi` database in SSMS.</span></span> <span data-ttu-id="1c21a-347">開啟 `STEP_2` 指令碼，並執行指令碼。</span><span class="sxs-lookup"><span data-stu-id="1c21a-347">Open the `STEP_2` script and execute the script.</span></span> <span data-ttu-id="1c21a-348">如果發生錯誤，請確定您是對 `wwi` 資料庫執行指令碼，而非對 `master` 執行。</span><span class="sxs-lookup"><span data-stu-id="1c21a-348">If you get an error, make sure you are running the script against the `wwi` database and not `master`.</span></span>

5. <span data-ttu-id="1c21a-349">使用 `STEP_1` 指令碼中指定的 `LoaderRC20` 使用者和密碼，開啟對 SQL 資料倉儲的新連線。</span><span class="sxs-lookup"><span data-stu-id="1c21a-349">Open a new connection to SQL Data Warehouse, using the `LoaderRC20` user and the password indicated in the `STEP_1` script.</span></span>

6. <span data-ttu-id="1c21a-350">使用此連線開啟 `STEP_3` 指令碼。</span><span class="sxs-lookup"><span data-stu-id="1c21a-350">Using this connection, open the `STEP_3` script.</span></span> <span data-ttu-id="1c21a-351">在指令碼中設定下列值：</span><span class="sxs-lookup"><span data-stu-id="1c21a-351">Set the following values in the script:</span></span>

    - <span data-ttu-id="1c21a-352">密碼：使用儲存體帳戶的存取金鑰。</span><span class="sxs-lookup"><span data-stu-id="1c21a-352">SECRET: Use the access key for your storage account.</span></span>
    - <span data-ttu-id="1c21a-353">位置︰使用儲存體帳戶的名稱，如下所示：`wasbs://wwi@<storage_account_name>.blob.core.windows.net`。</span><span class="sxs-lookup"><span data-stu-id="1c21a-353">LOCATION: Use the name of the storage account as follows: `wasbs://wwi@<storage_account_name>.blob.core.windows.net`.</span></span>

7. <span data-ttu-id="1c21a-354">使用相同的連線，依序執行指令碼 `STEP_4` 到 `STEP_7`。</span><span class="sxs-lookup"><span data-stu-id="1c21a-354">Using the same connection, execute scripts `STEP_4` through `STEP_7` sequentially.</span></span> <span data-ttu-id="1c21a-355">請確認一個指令碼順利完成後，再執行下一個。</span><span class="sxs-lookup"><span data-stu-id="1c21a-355">Verify that each script completes successfully before running the next.</span></span>

<span data-ttu-id="1c21a-356">在 SMSS 中，您應該會在 `wwi` 資料庫中看到一組 `prd.*` 資料表。</span><span class="sxs-lookup"><span data-stu-id="1c21a-356">In SMSS, you should see a set of `prd.*` tables in the `wwi` database.</span></span> <span data-ttu-id="1c21a-357">若要確認資料已產生，請執行下列查詢：</span><span class="sxs-lookup"><span data-stu-id="1c21a-357">To verify that the data was generated, run the following query:</span></span> 

```sql
SELECT TOP 10 * FROM prd.CityDimensions
```

### <a name="build-the-azure-analysis-services-model"></a><span data-ttu-id="1c21a-358">建置 Azure Analysis Services 模型</span><span class="sxs-lookup"><span data-stu-id="1c21a-358">Build the Azure Analysis Services model</span></span>

<span data-ttu-id="1c21a-359">在此步驟中，您將建立會從資料倉儲匯入資料的表格式模型。</span><span class="sxs-lookup"><span data-stu-id="1c21a-359">In this step, you will create a tabular model that imports data from the data warehouse.</span></span> <span data-ttu-id="1c21a-360">然後，您會將模型部署至 Azure Analysis Services。</span><span class="sxs-lookup"><span data-stu-id="1c21a-360">Then you will deploy the model to Azure Analysis Services.</span></span>

1. <span data-ttu-id="1c21a-361">在您的遠端桌面工作階段中，啟動 SQL Server Data Tools 2015。</span><span class="sxs-lookup"><span data-stu-id="1c21a-361">From your Remote Desktop session, launch SQL Server Data Tools 2015.</span></span>

2. <span data-ttu-id="1c21a-362">選取 [檔案] > [新增] > [專案]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-362">Select **File** > **New** > **Project**.</span></span>

3. <span data-ttu-id="1c21a-363">在 [新增專案] 對話方塊中的 [範本] 下，選取 [商業智慧] > [Analysis Services] > [Analysis Services 表格式專案]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-363">In the **New Project** dialog, under **Templates**, select  **Business Intelligence** > **Analysis Services** > **Analysis Services Tabular Project**.</span></span> 

4. <span data-ttu-id="1c21a-364">為專案命名，然後按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-364">Name the project and click **OK**.</span></span>

5. <span data-ttu-id="1c21a-365">在 [表格式模型設計工具] 對話方塊中，選取 [整合式工作區]，然後將 [相容性層級] 設為 `SQL Server 2017 / Azure Analysis Services (1400)`。</span><span class="sxs-lookup"><span data-stu-id="1c21a-365">In the **Tabular model designer** dialog, select **Integrated workspace**  and set **Compatibility level** to `SQL Server 2017 / Azure Analysis Services (1400)`.</span></span> <span data-ttu-id="1c21a-366">按一下 [SERVICEPRINCIPAL] 。</span><span class="sxs-lookup"><span data-stu-id="1c21a-366">Click **OK**.</span></span>

6. <span data-ttu-id="1c21a-367">在 [表格式模型總管] 視窗中，以滑鼠右鍵按一下專案，然後選取 [從資料來源匯入]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-367">In the **Tabular Model Explorer** window, right-click the project and select **Import from Data Source**.</span></span>

7. <span data-ttu-id="1c21a-368">選取 [Azure SQL 資料倉儲]，然後按一下 [連線]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-368">Select **Azure SQL Data Warehouse** and click **Connect**.</span></span>

8. <span data-ttu-id="1c21a-369">針對 [伺服器]，輸入 Azure SQL 資料倉儲伺服器的完整名稱。</span><span class="sxs-lookup"><span data-stu-id="1c21a-369">For **Server**, enter the fully qualified name of your Azure SQL Data Warehouse server.</span></span> <span data-ttu-id="1c21a-370">針對 [資料庫]，輸入 `wwi`。</span><span class="sxs-lookup"><span data-stu-id="1c21a-370">For **Database**, enter `wwi`.</span></span> <span data-ttu-id="1c21a-371">按一下 [SERVICEPRINCIPAL] 。</span><span class="sxs-lookup"><span data-stu-id="1c21a-371">Click **OK**.</span></span>

9. <span data-ttu-id="1c21a-372">在下一個對話方塊中，選擇 [資料庫] 驗證，並輸入您的 Azure SQL 資料倉儲的使用者名稱和密碼，然後按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-372">In the next dialog, choose **Database** authentication and enter your Azure SQL Data Warehouse user name and password, and click **OK**.</span></span>

10. <span data-ttu-id="1c21a-373">在 [導覽器] 對話方塊中，選取 **prd.CityDimensions**、**prd.DateDimensions** 和 **prd.SalesFact** 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="1c21a-373">In the **Navigator** dialog, select the checkboxes for **prd.CityDimensions**, **prd.DateDimensions**, and **prd.SalesFact**.</span></span> 

    ![](./images/analysis-services-import.png)

11. <span data-ttu-id="1c21a-374">按一下 [載入]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-374">Click **Load**.</span></span> <span data-ttu-id="1c21a-375">在處理完成時，按一下 [關閉]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-375">When processing is complete, click **Close**.</span></span> <span data-ttu-id="1c21a-376">您現在應該會看到資料的表格式檢視。</span><span class="sxs-lookup"><span data-stu-id="1c21a-376">You should now see a tabular view of the data.</span></span>

12. <span data-ttu-id="1c21a-377">在 [表格式模型總管] 視窗中，以滑鼠右鍵按一下專案，然後選取 [模型檢視] > [圖表檢視]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-377">In the **Tabular Model Explorer** window, right-click the project and select **Model View** > **Diagram View**.</span></span>

13. <span data-ttu-id="1c21a-378">將 **[prd.SalesFact].[WWI City ID]** 欄位拖曳至 **[prd.CityDimensions].[WWI City ID]** 欄位，以建立關聯性。</span><span class="sxs-lookup"><span data-stu-id="1c21a-378">Drag the **[prd.SalesFact].[WWI City ID]** field to the **[prd.CityDimensions].[WWI City ID]** field to create a relationship.</span></span>  

14. <span data-ttu-id="1c21a-379">將 **[prd.SalesFact].[Invoice Date Key]** 欄位拖曳至 **[prd.DateDimensions].[Date]** 欄位。</span><span class="sxs-lookup"><span data-stu-id="1c21a-379">Drag the **[prd.SalesFact].[Invoice Date Key]** field to the **[prd.DateDimensions].[Date]** field.</span></span>  
    ![](./images/analysis-services-relations.png)

15. <span data-ttu-id="1c21a-380">在 [檔案] 功能表中，選擇 [全部儲存]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-380">From the **File** menu, choose **Save All**.</span></span>  

16. <span data-ttu-id="1c21a-381">在 [方案總管] 中，以滑鼠右鍵按一下專案，然後選取 [屬性]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-381">In **Solution Explorer**, right-click the project and select **Properties**.</span></span> 

17. <span data-ttu-id="1c21a-382">在 [伺服器] 下，輸入 Azure Analysis Services 執行個體的 URL。</span><span class="sxs-lookup"><span data-stu-id="1c21a-382">Under **Server**, enter the URL of your Azure Analysis Services instance.</span></span> <span data-ttu-id="1c21a-383">您可以從 Azure 入口網站中取得此值。</span><span class="sxs-lookup"><span data-stu-id="1c21a-383">You can get this value from the Azure Portal.</span></span> <span data-ttu-id="1c21a-384">在入口網站中選取 Analysis Services 資源，按一下 [概觀] 窗格，然後尋找 [伺服器名稱] 屬性。</span><span class="sxs-lookup"><span data-stu-id="1c21a-384">In the portal, select the Analysis Services resource, click the Overview pane, and look for the **Server Name** property.</span></span> <span data-ttu-id="1c21a-385">該屬性將類似於 `asazure://westus.asazure.windows.net/contoso`。</span><span class="sxs-lookup"><span data-stu-id="1c21a-385">It will be similar to `asazure://westus.asazure.windows.net/contoso`.</span></span> <span data-ttu-id="1c21a-386">按一下 [SERVICEPRINCIPAL] 。</span><span class="sxs-lookup"><span data-stu-id="1c21a-386">Click **OK**.</span></span>

    ![](./images/analysis-services-properties.png)

18. <span data-ttu-id="1c21a-387">在 [方案總管] 中，以滑鼠右鍵按一下專案，然後選取 [部署]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-387">In **Solution Explorer**, right-click the project and select **Deploy**.</span></span> <span data-ttu-id="1c21a-388">在出現提示時，登入 Azure。</span><span class="sxs-lookup"><span data-stu-id="1c21a-388">Sign into Azure if prompted.</span></span> <span data-ttu-id="1c21a-389">在處理完成時，按一下 [關閉]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-389">When processing is complete, click **Close**.</span></span>

19. <span data-ttu-id="1c21a-390">在 Azure 入口網站中，檢視 Azure Analysis Services 執行個體的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="1c21a-390">In the Azure portal, view the details for your Azure Analysis Services instance.</span></span> <span data-ttu-id="1c21a-391">請確認您的模型出現在模型清單中。</span><span class="sxs-lookup"><span data-stu-id="1c21a-391">Verify that your model appears in the list of models.</span></span>

    ![](./images/analysis-services-models.png)

### <a name="analyze-the-data-in-power-bi-desktop"></a><span data-ttu-id="1c21a-392">在 Power BI Desktop 中分析資料</span><span class="sxs-lookup"><span data-stu-id="1c21a-392">Analyze the data in Power BI Desktop</span></span>

<span data-ttu-id="1c21a-393">在此步驟中，您將使用 Power BI 從 Analysis Services 中的資料建立報告。</span><span class="sxs-lookup"><span data-stu-id="1c21a-393">In this step, you will use Power BI to create a report from the data in Analysis Services.</span></span>

1. <span data-ttu-id="1c21a-394">在您的遠端桌面工作階段中，啟動 Power BI Desktop。</span><span class="sxs-lookup"><span data-stu-id="1c21a-394">From your Remote Desktop session, launch Power BI Desktop.</span></span>

2. <span data-ttu-id="1c21a-395">在 [歡迎使用] 畫面中，按一下 [取得資料]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-395">In the Welcome Scren, click **Get Data**.</span></span>

3. <span data-ttu-id="1c21a-396">選取 [Azure] > [Azure Analysis Services 資料庫]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-396">Select **Azure** > **Azure Analysis Services database**.</span></span> <span data-ttu-id="1c21a-397">按一下 [連接] </span><span class="sxs-lookup"><span data-stu-id="1c21a-397">Click **Connect**</span></span>

    ![](./images/power-bi-get-data.png)

4. <span data-ttu-id="1c21a-398">輸入 Analysis Services 執行個體的 URL，然後按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-398">Enter the URL of your Analysis Services instance, then click **OK**.</span></span> <span data-ttu-id="1c21a-399">在出現提示時，登入 Azure。</span><span class="sxs-lookup"><span data-stu-id="1c21a-399">Sign into Azure if prompted.</span></span>

5. <span data-ttu-id="1c21a-400">在 [導覽器] 對話方塊中，展開您所部署的表格式專案，選取您所建立的模型，然後按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-400">In the **Navigator** dialog, expand the tabular project that you deployed, select the model that you created, and click **OK**.</span></span>

2. <span data-ttu-id="1c21a-401">在 [視覺效果] 窗格中，選取 [堆疊橫條圖] 圖示。</span><span class="sxs-lookup"><span data-stu-id="1c21a-401">In the **Visualizations** pane, select the **Stacked Bar Chart** icon.</span></span> <span data-ttu-id="1c21a-402">在 [報告] 檢視中，將視覺效果調整得大一些。</span><span class="sxs-lookup"><span data-stu-id="1c21a-402">In the Report view, resize the visualization to make it larger.</span></span>

6. <span data-ttu-id="1c21a-403">在 [欄位] 窗格中，展開 **prd.CityDimensions**。</span><span class="sxs-lookup"><span data-stu-id="1c21a-403">In the **Fields** pane, expand **prd.CityDimensions**.</span></span>

7. <span data-ttu-id="1c21a-404">將 **prd.CityDimensions** > [WWI 城市識別碼] 拖曳至 [軸] 區。</span><span class="sxs-lookup"><span data-stu-id="1c21a-404">Drag **prd.CityDimensions** > **WWI City ID** to the **Axis well**.</span></span>

8. <span data-ttu-id="1c21a-405">將 **prd.CityDimensions** > [城市] 拖曳至 [圖例] 區。</span><span class="sxs-lookup"><span data-stu-id="1c21a-405">Drag **prd.CityDimensions** > **City** to the **Legend** well.</span></span>

9. <span data-ttu-id="1c21a-406">在 [欄位] 窗格中，展開 **prd.SalesFact**。</span><span class="sxs-lookup"><span data-stu-id="1c21a-406">In the Fields pane, expand **prd.SalesFact**.</span></span>

10. <span data-ttu-id="1c21a-407">將 **prd.SalesFact** > [未稅額總計] 拖曳至 [值] 區。</span><span class="sxs-lookup"><span data-stu-id="1c21a-407">Drag **prd.SalesFact** > **Total Excluding Tax** to the **Value** well.</span></span>

    ![](./images/power-bi-visualization.png)

11. <span data-ttu-id="1c21a-408">在 [視覺效果層級篩選] 下，選取 [WWI 城市識別碼]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-408">Under **Visual Level Filters**, select **WWI City ID**.</span></span>

12. <span data-ttu-id="1c21a-409">將 [篩選類型] 設為 `Top N`，並將 [顯示項目] 設為 `Top 10`。</span><span class="sxs-lookup"><span data-stu-id="1c21a-409">Set the **Filter Type** to `Top N`, and set **Show Items** to `Top 10`.</span></span>

13. <span data-ttu-id="1c21a-410">將 **prd.SalesFact** > [未稅額總計] 拖曳至 [依據值] 區</span><span class="sxs-lookup"><span data-stu-id="1c21a-410">Drag **prd.SalesFact** > **Total Excluding Tax** to the **By Value** well</span></span>

    ![](./images/power-bi-visualization2.png)

14. <span data-ttu-id="1c21a-411">按一下 [套用篩選]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-411">Click **Apply Filter**.</span></span> <span data-ttu-id="1c21a-412">視覺效果會依城市顯示前 10 個總銷售量。</span><span class="sxs-lookup"><span data-stu-id="1c21a-412">The visualization shows the top 10 total sales by city.</span></span>

    ![](./images/power-bi-report.png)

<span data-ttu-id="1c21a-413">若要深入了解 Power BI Desktop，請參閱[開始使用 Power BI Desktop](/power-bi/desktop-getting-started)。</span><span class="sxs-lookup"><span data-stu-id="1c21a-413">To learn more about Power BI Desktop, see [Getting started with Power BI Desktop](/power-bi/desktop-getting-started).</span></span>

## <a name="next-steps"></a><span data-ttu-id="1c21a-414">後續步驟</span><span class="sxs-lookup"><span data-stu-id="1c21a-414">Next steps</span></span>

- <span data-ttu-id="1c21a-415">如需關於此參考架構的詳細資訊，請瀏覽我們的 [GitHub 存放庫][ref-arch-repo-folder]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-415">For more information about this reference architecture, visit our [GitHub repository][ref-arch-repo-folder].</span></span>
- <span data-ttu-id="1c21a-416">了解 [Azure 建置組塊][azbb-repo]。</span><span class="sxs-lookup"><span data-stu-id="1c21a-416">Learn about the [Azure Building Blocks][azbb-repo].</span></span>

<!-- links -->

[azure-cli-2]: /azure/install-azure-cli
[azbb-repo]: https://github.com/mspnp/template-building-blocks
[azbb-wiki]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/data/enterprise_bi_sqldw
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[ref-arch-repo-folder]: https://github.com/mspnp/reference-architectures/tree/master/data/enterprise_bi_sqldw


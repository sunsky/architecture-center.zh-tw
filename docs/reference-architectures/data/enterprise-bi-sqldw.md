---
title: 具 SQL 資料倉儲的 Enterprise BI
description: 使用 Azure 從儲存在內部部署的關聯式資料取得商業見解
author: MikeWasson
ms.date: 07/01/2018
ms.openlocfilehash: e3542e40b4b6d1f604f93bb21528f34ba7f22fc6
ms.sourcegitcommit: 58d93e7ac9a6d44d5668a187a6827d7cd4f5a34d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2018
ms.locfileid: "37142330"
---
# <a name="enterprise-bi-with-sql-data-warehouse"></a><span data-ttu-id="6e5f1-103">具 SQL 資料倉儲的 Enterprise BI</span><span class="sxs-lookup"><span data-stu-id="6e5f1-103">Enterprise BI with SQL Data Warehouse</span></span>

<span data-ttu-id="6e5f1-104">此參考架構會實作 [ELT](../../data-guide/relational-data/etl.md#extract-load-and-transform-elt) (擷取-載入-轉換) 管線，將資料從內部部署 SQL Server 資料庫移至 SQL 資料倉儲，並轉換資料以供分析。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-104">This reference architecture implements an [ELT](../../data-guide/relational-data/etl.md#extract-load-and-transform-elt) (extract-load-transform) pipeline that moves data from an on-premises SQL Server database into SQL Data Warehouse and transforms the data for analysis.</span></span> [<span data-ttu-id="6e5f1-105">**部署這個解決方案**。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-105">**Deploy this solution**.</span></span>](#deploy-the-solution)

![](./images/enterprise-bi-sqldw.png)

<span data-ttu-id="6e5f1-106">**案例**：組織具有在內部部署的 SQL Server 資料庫中儲存的大型 OLTP 資料集。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-106">**Scenario**: An organization has a large OLTP data set stored in a SQL Server database on premises.</span></span> <span data-ttu-id="6e5f1-107">組織想要使用 SQL 資料倉儲，透過 Power BI 執行分析。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-107">The organization wants to use SQL Data Warehouse to perform analysis using Power BI.</span></span> 

<span data-ttu-id="6e5f1-108">此參考架構是針對一次性或隨需作業而設計的。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-108">This reference architecture is designed for one-time or on-demand jobs.</span></span> <span data-ttu-id="6e5f1-109">如果您需要持續移動資料 (每小時或每日)，建議您使用 Azure Data Factory 來定義自動化工作流程。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-109">If you need to move data on a continuing basis (hourly or daily), we recommend using Azure Data Factory to define an automated workflow.</span></span> <span data-ttu-id="6e5f1-110">如需使用 Data Factory 的參考架構，請參閱[具 SQL 資料倉儲和 Azure Data Factory 的自動化 Enterprise BI](./enterprise-bi-adf.md) (英文)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-110">For a reference architecture that uses Data Factory, see [Automated enterprise BI with SQL Data Warehouse and Azure Data Factory](./enterprise-bi-adf.md).</span></span>

## <a name="architecture"></a><span data-ttu-id="6e5f1-111">架構</span><span class="sxs-lookup"><span data-stu-id="6e5f1-111">Architecture</span></span>

<span data-ttu-id="6e5f1-112">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-112">The architecture consists of the following components.</span></span>

### <a name="data-source"></a><span data-ttu-id="6e5f1-113">資料來源</span><span class="sxs-lookup"><span data-stu-id="6e5f1-113">Data source</span></span>

<span data-ttu-id="6e5f1-114">**SQL Server**。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-114">**SQL Server**.</span></span> <span data-ttu-id="6e5f1-115">來源資料位於內部部署的 SQL Server 資料庫中。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-115">The source data is located in a SQL Server database on premises.</span></span> <span data-ttu-id="6e5f1-116">為了模擬內部部署環境，此架構的部署指令碼會在 Azure 中佈建已安裝 SQL Server 的 VM。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-116">To simulate the on-premises environment, the deployment scripts for this architecture provision a VM in Azure with SQL Server installed.</span></span> <span data-ttu-id="6e5f1-117">系統會使用 [Wide World Importers OLTP 範例資料庫][wwi] (機器翻譯) 作為來源資料。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-117">The [Wide World Importers OLTP sample database][wwi] is used as the source data.</span></span>

### <a name="ingestion-and-data-storage"></a><span data-ttu-id="6e5f1-118">擷取和資料儲存體</span><span class="sxs-lookup"><span data-stu-id="6e5f1-118">Ingestion and data storage</span></span>

<span data-ttu-id="6e5f1-119">**Blob 儲存體**。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-119">**Blob Storage**.</span></span> <span data-ttu-id="6e5f1-120">Blob 儲存體會作為在資料載入至 SQL 資料倉儲之前用來複製資料的臨時區域。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-120">Blob storage is used as a staging area to copy the data before loading it into SQL Data Warehouse.</span></span>

<span data-ttu-id="6e5f1-121">**Azure SQL 資料倉儲**。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-121">**Azure SQL Data Warehouse**.</span></span> <span data-ttu-id="6e5f1-122">[SQL 資料倉儲](/azure/sql-data-warehouse/)是為了對大型資料執行分析而設計的分散式系統。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-122">[SQL Data Warehouse](/azure/sql-data-warehouse/) is a distributed system designed to perform analytics on large data.</span></span> <span data-ttu-id="6e5f1-123">它支援大量平行處理 (MPP)，因而適合用來執行高效能分析。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-123">It supports massive parallel processing (MPP), which makes it suitable for running high-performance analytics.</span></span> 

### <a name="analysis-and-reporting"></a><span data-ttu-id="6e5f1-124">分析和報告</span><span class="sxs-lookup"><span data-stu-id="6e5f1-124">Analysis and reporting</span></span>

<span data-ttu-id="6e5f1-125">**Azure Analysis Services**。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-125">**Azure Analysis Services**.</span></span> <span data-ttu-id="6e5f1-126">[Analysis Services](/azure/analysis-services/) 是完全受控的服務，可提供資料模型功能。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-126">[Analysis Services](/azure/analysis-services/) is a fully managed service that provides data modeling capabilities.</span></span> <span data-ttu-id="6e5f1-127">使用 Analysis Services 可建立可供使用者查詢的語意模型。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-127">Use Analysis Services to create a semantic model that users can query.</span></span> <span data-ttu-id="6e5f1-128">Analysis Services 在 BI 儀表板案例中最能發揮效用。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-128">Analysis Services is especially useful in a BI dashboard scenario.</span></span> <span data-ttu-id="6e5f1-129">在此架構中，Analysis Services 會從資料倉儲讀取資料以處理語意模型，並有效率地為儀表板查詢提供服務。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-129">In this architecture, Analysis Services reads data from the data warehouse to process the semantic model, and efficiently serves dashboard queries.</span></span> <span data-ttu-id="6e5f1-130">它也可藉由相應放大複本以加快查詢處理速度，而支援彈性的並行存取。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-130">It also supports elastic concurrency, by scaling out replicas for faster query processing.</span></span>

<span data-ttu-id="6e5f1-131">目前，Azure Analysis Services 支援表格式模型，但不支援多維度模型。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-131">Currently, Azure Analysis Services supports tabular models but not multidimensional models.</span></span> <span data-ttu-id="6e5f1-132">表格式模型使用關聯式模型建構 (資料表和資料行)，而多維度模型則使用 OLAP 模型建構 (Cube、維度和量值)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-132">Tabular models use relational modeling constructs (tables and columns), whereas multidimensional models use OLAP modeling constructs (cubes, dimensions, and measures).</span></span> <span data-ttu-id="6e5f1-133">如果您需要多維度模型，請使用 SQL Server Analysis Services (SSAS)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-133">If you require multidimensional models, use SQL Server Analysis Services (SSAS).</span></span> <span data-ttu-id="6e5f1-134">如需詳細資訊，請參閱[比較表格式和多維度解決方案](/sql/analysis-services/comparing-tabular-and-multidimensional-solutions-ssas)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-134">For more information, see [Comparing tabular and multidimensional solutions](/sql/analysis-services/comparing-tabular-and-multidimensional-solutions-ssas).</span></span>

<span data-ttu-id="6e5f1-135">**Power BI**。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-135">**Power BI**.</span></span> <span data-ttu-id="6e5f1-136">Power BI 是一套用來分析資料以產生商業見解的商務分析工具。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-136">Power BI is a suite of business analytics tools to analyze data for business insights.</span></span> <span data-ttu-id="6e5f1-137">在此架構中，它會查詢儲存在 Analysis Services 中的語意模型。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-137">In this architecture, it queries the semantic model stored in Analysis Services.</span></span>

### <a name="authentication"></a><span data-ttu-id="6e5f1-138">驗證</span><span class="sxs-lookup"><span data-stu-id="6e5f1-138">Authentication</span></span>

<span data-ttu-id="6e5f1-139">**Azure Active Directory** (Azure AD) 會驗證透過 Power BI 連線至 Analysis Services 伺服器的使用者。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-139">**Azure Active Directory** (Azure AD) authenticates users who connect to the Analysis Services server through Power BI.</span></span>

## <a name="data-pipeline"></a><span data-ttu-id="6e5f1-140">Data Pipeline</span><span class="sxs-lookup"><span data-stu-id="6e5f1-140">Data pipeline</span></span>
 
<span data-ttu-id="6e5f1-141">此參考架構會使用 [WorldWideImporters](/sql/sample/world-wide-importers/wide-world-importers-oltp-database) 範例資料庫作為資料來源。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-141">This reference architecture uses the [WorldWideImporters](/sql/sample/world-wide-importers/wide-world-importers-oltp-database) sample database as a data source.</span></span> <span data-ttu-id="6e5f1-142">資料管線具有下列階段：</span><span class="sxs-lookup"><span data-stu-id="6e5f1-142">The data pipeline has the following stages:</span></span>

1. <span data-ttu-id="6e5f1-143">將資料從 SQL Server 匯出至一般檔案 (bcp 公用程式)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-143">Export the data from SQL Server to flat files (bcp utility).</span></span>
2. <span data-ttu-id="6e5f1-144">將一般檔案複製到 Azure Blob 儲存體 (AzCopy)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-144">Copy the flat files to Azure Blob Storage (AzCopy).</span></span>
3. <span data-ttu-id="6e5f1-145">將資料載入 SQL 資料倉儲中 (PolyBase)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-145">Load the data into SQL Data Warehouse (PolyBase).</span></span>
4. <span data-ttu-id="6e5f1-146">將資料轉換為星型結構描述 (T-SQL)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-146">Transform the data into a star schema (T-SQL).</span></span>
5. <span data-ttu-id="6e5f1-147">將語意模型載入 Analysis Services 中 (SQL Server Data Tools)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-147">Load a semantic model into Analysis Services (SQL Server Data Tools).</span></span>

![](./images/enterprise-bi-sqldw-pipeline.png)
 
> [!NOTE]
> <span data-ttu-id="6e5f1-148">在步驟 1 &ndash; 3 中，請考慮使用 Redgate Data Platform Studio。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-148">For steps 1 &ndash; 3, consider using Redgate Data Platform Studio.</span></span> <span data-ttu-id="6e5f1-149">Data Platform Studio 會套用最適當的相容性修正檔與最佳化，因此它是開始使用 SQL 資料倉儲最快的方式。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-149">Data Platform Studio applies the most appropriate compatibility fixes and optimizations, so it's the quickest way to get started with SQL Data Warehouse.</span></span> <span data-ttu-id="6e5f1-150">如需詳細資訊，請參閱[使用 Redgate Data Platform Studio 載入資料](/azure/sql-data-warehouse/sql-data-warehouse-load-with-redgate)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-150">For more information, see [Load data with Redgate Data Platform Studio](/azure/sql-data-warehouse/sql-data-warehouse-load-with-redgate).</span></span> 

<span data-ttu-id="6e5f1-151">以下幾節將詳細說明這些階段。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-151">The next sections describe these stages in more detail.</span></span>

### <a name="export-data-from-sql-server"></a><span data-ttu-id="6e5f1-152">從 SQL Server 匯入資料</span><span class="sxs-lookup"><span data-stu-id="6e5f1-152">Export data from SQL Server</span></span>

<span data-ttu-id="6e5f1-153">[bcp](/sql/tools/bcp-utility) (大量複製程式) 公用程式是從 SQL 資料表建立一般文字檔的快速途徑。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-153">The [bcp](/sql/tools/bcp-utility) (bulk copy program) utility is a fast way to create flat text files from SQL tables.</span></span> <span data-ttu-id="6e5f1-154">在此步驟中，您會選取您要匯出的資料行，但不會轉換資料。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-154">In this step, you select the columns that you want to export, but don't transform the data.</span></span> <span data-ttu-id="6e5f1-155">任何資料轉換均應在 SQL 資料倉儲中執行。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-155">Any data transformations should happen in SQL Data Warehouse.</span></span>

<span data-ttu-id="6e5f1-156">**建議**</span><span class="sxs-lookup"><span data-stu-id="6e5f1-156">**Recommendations**</span></span>

<span data-ttu-id="6e5f1-157">可能的話，請將資料擷取排程於離峰時間，以盡可能避免生產環境中的資源爭用。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-157">If possible, schedule data extraction during off-peak hours, to minimize resource contention in the production environment.</span></span> 

<span data-ttu-id="6e5f1-158">請避免在資料庫伺服器上執行 bcp。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-158">Avoid running bcp on the database server.</span></span> <span data-ttu-id="6e5f1-159">改以其他機器來執行。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-159">Instead, run it from another machine.</span></span> <span data-ttu-id="6e5f1-160">請將檔案寫入至本機磁碟機。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-160">Write the files to a local drive.</span></span> <span data-ttu-id="6e5f1-161">請確定您有足夠的 I/O 資源可處理並行寫入。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-161">Ensure that you have sufficient I/O resources to handle the concurrent writes.</span></span> <span data-ttu-id="6e5f1-162">為了達到最佳效能，請將檔案匯出至專用的快速儲存磁碟機。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-162">For best performance, export the files to dedicated fast storage drives.</span></span>

<span data-ttu-id="6e5f1-163">您可以用 Gzip 壓縮格式儲存匯出的資料，以加快網路傳輸速度。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-163">You can speed up the network transfer by saving the exported data in Gzip compressed format.</span></span> <span data-ttu-id="6e5f1-164">不過，壓縮的檔案載入至倉儲的速度會低於非壓縮檔案的載入速度，因此，您必須在網路傳輸速度與載入速度之間做出取捨。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-164">However, loading compressed files into the warehouse is slower than loading uncompressed files, so there is a tradeoff between faster network transfer versus faster loading.</span></span> <span data-ttu-id="6e5f1-165">如果您決定使用 Gzip 壓縮，請勿建立單一 Gzip 檔案。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-165">If you decide to use Gzip compression, don't create a single Gzip file.</span></span> <span data-ttu-id="6e5f1-166">此時，請將資料分成多個壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-166">Instead, split the data into multiple compressed files.</span></span>

### <a name="copy-flat-files-into-blob-storage"></a><span data-ttu-id="6e5f1-167">將一般檔案複製到 Blob 儲存體中</span><span class="sxs-lookup"><span data-stu-id="6e5f1-167">Copy flat files into blob storage</span></span>

<span data-ttu-id="6e5f1-168">[AzCopy](/azure/storage/common/storage-use-azcopy) 公用程式可讓您以高效能將資料複製到 Azure Blob 儲存體中。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-168">The [AzCopy](/azure/storage/common/storage-use-azcopy) utility is designed for high-performance copying of data into Azure blob storage.</span></span>

<span data-ttu-id="6e5f1-169">**建議**</span><span class="sxs-lookup"><span data-stu-id="6e5f1-169">**Recommendations**</span></span>

<span data-ttu-id="6e5f1-170">儲存體帳戶請建立在來源資料所在位置的鄰近區域中。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-170">Create the storage account in a region near the location of the source data.</span></span> <span data-ttu-id="6e5f1-171">請將儲存體帳戶和 SQL 資料倉儲執行個體部署在相同區域中。 </span><span class="sxs-lookup"><span data-stu-id="6e5f1-171">Deploy the storage account and the SQL Data Warehouse instance in the same region.</span></span> 

<span data-ttu-id="6e5f1-172">請勿在執行生產工作負載的相同機器上執行 AzCopy，因為 CPU 和 I/O 的耗用可能會影響到生產工作負載。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-172">Don't run AzCopy on the same machine that runs your production workloads, because the CPU and I/O consumption can interfere with the production workload.</span></span> 

<span data-ttu-id="6e5f1-173">請先測試上傳，以確認上傳速度。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-173">Test the upload first to see what the upload speed is like.</span></span> <span data-ttu-id="6e5f1-174">您可以使用 AzCopy 中的 /NC 選項來指定並行複製作業數目。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-174">You can use the /NC option in AzCopy to specify the number of concurrent copy operations.</span></span> <span data-ttu-id="6e5f1-175">一開始請先使用預設值，然後再試著以這項設定調整效能。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-175">Start with the default value, then experiment with this setting to tune the performance.</span></span> <span data-ttu-id="6e5f1-176">在低頻寬環境中，過多的並行作業有可能會拖垮網路連線，而使作業無法順利完成。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-176">In a low-bandwidth environment, too many concurrent operations can overwhelm the network connection and prevent the operations from completing successfully.</span></span>  

<span data-ttu-id="6e5f1-177">AzCopy 會透過公用網際網路將資料移至儲存體。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-177">AzCopy moves data to storage over the public internet.</span></span> <span data-ttu-id="6e5f1-178">若其速度不夠快，請考慮設定 [ExpressRoute](/azure/expressroute/) 線路。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-178">If this isn't fast enough, consider setting up an [ExpressRoute](/azure/expressroute/) circuit.</span></span> <span data-ttu-id="6e5f1-179">ExpressRoute 是一項服務，它會透過專用私人連線將您的資料路由傳送至 Azure。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-179">ExpressRoute is a service that routes your data through a dedicated private connection to Azure.</span></span> <span data-ttu-id="6e5f1-180">當網路連線速度太慢時，您也可以考慮將磁碟上的資料傳送至 Azure 資料中心。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-180">Another option, if your network connection is too slow, is to physically ship the data on disk to an Azure datacenter.</span></span> <span data-ttu-id="6e5f1-181">如需詳細資訊，請參閱[從 Azure 來回傳輸資料](/azure/architecture/data-guide/scenarios/data-transfer)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-181">For more information, see [Transferring data to and from Azure](/azure/architecture/data-guide/scenarios/data-transfer).</span></span>

<span data-ttu-id="6e5f1-182">在複製作業期間，AzCopy 會建立暫存日誌檔案，讓 AzCopy 能夠在作業中斷 (例如，由於網路錯誤) 時加以重新啟動。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-182">During a copy operation, AzCopy creates a temporary journal file, which enables AzCopy to restart the operation if it gets interrupted (for example, due to a network error).</span></span> <span data-ttu-id="6e5f1-183">請確定有足夠的磁碟空間可儲存日誌檔案。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-183">Make sure there is enough disk space to store the journal files.</span></span> <span data-ttu-id="6e5f1-184">您可以使用 /Z 選項來指定日誌檔案的寫入位置。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-184">You can use the /Z option to specify where the journal files are written.</span></span>

### <a name="load-data-into-sql-data-warehouse"></a><span data-ttu-id="6e5f1-185">將資料載入至 SQL 資料倉儲</span><span class="sxs-lookup"><span data-stu-id="6e5f1-185">Load data into SQL Data Warehouse</span></span>

<span data-ttu-id="6e5f1-186">使用 [PolyBase](/sql/relational-databases/polybase/polybase-guide) 可將檔案從 Blob 儲存體載入資料倉儲中。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-186">Use [PolyBase](/sql/relational-databases/polybase/polybase-guide) to load the files from blob storage into the data warehouse.</span></span> <span data-ttu-id="6e5f1-187">PolyBase 依設計會運用 SQL 資料倉儲的 MPP (大量平行處理) 架構，因此能夠以最快的速度將資料載入 SQL 資料倉儲中。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-187">PolyBase is designed to leverage the MPP (Massively Parallel Processing) architecture of SQL Data Warehouse, which makes it the fastest way to load data into SQL Data Warehouse.</span></span> 

<span data-ttu-id="6e5f1-188">載入資料的程序由兩個步驟組成：</span><span class="sxs-lookup"><span data-stu-id="6e5f1-188">Loading the data is a two-step process:</span></span>

1. <span data-ttu-id="6e5f1-189">為資料建立一組外部資料表。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-189">Create a set of external tables for the data.</span></span> <span data-ttu-id="6e5f1-190">外部資料表是一項資料表定義，會指向儲存在倉儲外部的資料 &mdash; 在此案例中，是指 Blob 儲存體中的一般檔案。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-190">An external table is a table definition that points to data stored outside of the warehouse &mdash; in this case, the flat files in blob storage.</span></span> <span data-ttu-id="6e5f1-191">此步驟不會將任何資料移至倉儲中。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-191">This step does not move any data into the warehouse.</span></span>
2. <span data-ttu-id="6e5f1-192">建立暫存資料表，並將資料載入暫存資料表中。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-192">Create staging tables, and load the data into the staging tables.</span></span> <span data-ttu-id="6e5f1-193">此步驟會將資料複製到倉儲中。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-193">This step copies the data into the warehouse.</span></span>

<span data-ttu-id="6e5f1-194">**建議**</span><span class="sxs-lookup"><span data-stu-id="6e5f1-194">**Recommendations**</span></span>

<span data-ttu-id="6e5f1-195">當您的資料龐大 (超過 1 TB)，且您的分析工作負載透過平行處理來執行又會比較好的話，請考慮使用 SQL 資料倉儲。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-195">Consider SQL Data Warehouse when you have large amounts of data (more than 1 TB) and are running an analytics workload that will benefit from parallelism.</span></span> <span data-ttu-id="6e5f1-196">SQL 資料倉儲不適用於 OLTP 工作負載或較小的資料集 (< 250 GB)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-196">SQL Data Warehouse is not a good fit for OLTP workloads or smaller data sets (< 250GB).</span></span> <span data-ttu-id="6e5f1-197">對於小於 250 GB 的資料集，請考慮使用 Azure SQL Database 或 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-197">For data sets less than 250GB, consider Azure SQL Database or SQL Server.</span></span> <span data-ttu-id="6e5f1-198">如需詳細資訊，請參閱[資料倉儲](../../data-guide/relational-data/data-warehousing.md)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-198">For more information, see [Data warehousing](../../data-guide/relational-data/data-warehousing.md).</span></span>

<span data-ttu-id="6e5f1-199">建立堆積資料表形式的暫存資料表 (不會編製索引)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-199">Create the staging tables as heap tables, which are not indexed.</span></span> <span data-ttu-id="6e5f1-200">建立生產資料表的查詢時將會執行完整資料表掃描，因此不需要編製暫存資料表的索引。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-200">The queries that create the production tables will result in a full table scan, so there is no reason to index the staging tables.</span></span>

<span data-ttu-id="6e5f1-201">PolyBase 會在倉儲中自動使用平行處理。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-201">PolyBase automatically takes advantage of parallelism in the warehouse.</span></span> <span data-ttu-id="6e5f1-202">載入效能會在您增加 DWU 時隨之調整。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-202">The load performance scales as you increase DWUs.</span></span> <span data-ttu-id="6e5f1-203">若要達到最佳效能，請使用單一載入作業。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-203">For best performance, use a single load operation.</span></span> <span data-ttu-id="6e5f1-204">將輸入資料分成多個區塊，並執行多項並行載入，並不會帶來效能上的優勢。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-204">There is no performance benefit to breaking the input data into chunks and running multiple concurrent loads.</span></span>

<span data-ttu-id="6e5f1-205">PolyBase 可讀取 Gzip 壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-205">PolyBase can read Gzip compressed files.</span></span> <span data-ttu-id="6e5f1-206">不過，每個壓縮檔案只會使用一個讀取器，因為解壓縮檔案是單一執行緒作業。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-206">However, only a single reader is used per compressed file, because uncompressing the file is a single-threaded operation.</span></span> <span data-ttu-id="6e5f1-207">因此，請避免載入單一大型壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-207">Therefore, avoid loading a single large compressed file.</span></span> <span data-ttu-id="6e5f1-208">此時，請將資料分成多個壓縮檔案，以運用平行處理。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-208">Instead, split the data into multiple compressed files, in order to take advantage of parallelism.</span></span> 

<span data-ttu-id="6e5f1-209">請留意以下限制：</span><span class="sxs-lookup"><span data-stu-id="6e5f1-209">Be aware of the following limitations:</span></span>

- <span data-ttu-id="6e5f1-210">PolyBase 支援的資料行大小上限為 `varchar(8000)`、`nvarchar(4000)` 或 `varbinary(8000)`。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-210">PolyBase supports a maximum column size of `varchar(8000)`, `nvarchar(4000)`, or `varbinary(8000)`.</span></span> <span data-ttu-id="6e5f1-211">如果您有超出這些限制的資料，其中一個選項是在資料匯出時將其分成多個區塊，並等到匯入後再重組區塊。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-211">If you have data that exceeds these limits, one option is to break the data up into chunks when you export it, and then reassemble the chunks after import.</span></span> 

- <span data-ttu-id="6e5f1-212">PolyBase 會使用固定的資料列結束字元 \n 或新行。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-212">PolyBase uses a fixed row terminator of \n or newline.</span></span> <span data-ttu-id="6e5f1-213">如果來源資料中出現新行字元，這可能會造成問題。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-213">This can cause problems if newline characters appear in the source data.</span></span>

- <span data-ttu-id="6e5f1-214">您的來源資料結構描述可能會包含 SQL 資料倉儲中不支援的資料類型。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-214">Your source data schema might contain data types that are not supported in SQL Data Warehouse.</span></span>

<span data-ttu-id="6e5f1-215">為了因應這些限制，您可以建立會執行必要轉換的預存程序。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-215">To work around these limitations, you can create a stored procedure that performs the necessary conversions.</span></span> <span data-ttu-id="6e5f1-216">在執行 bcp 時，請參考此預存程序。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-216">Reference this stored procedure when you run bcp.</span></span> <span data-ttu-id="6e5f1-217">或者，[Redgate 資料平台 Studio](/azure/sql-data-warehouse/sql-data-warehouse-load-with-redgate) 會自動轉換在 SQL 資料倉儲中不支援的資料類型。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-217">Alternatively, [Redgate Data Platform Studio](/azure/sql-data-warehouse/sql-data-warehouse-load-with-redgate) automatically converts data types that aren’t supported in SQL Data Warehouse.</span></span>

<span data-ttu-id="6e5f1-218">如需詳細資訊，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="6e5f1-218">For more information, see the following articles:</span></span>

- <span data-ttu-id="6e5f1-219">[將資料載入 Azure SQL 資料倉儲中的最佳做法](/azure/sql-data-warehouse/guidance-for-loading-data)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-219">[Best practices for loading data into Azure SQL Data Warehouse](/azure/sql-data-warehouse/guidance-for-loading-data).</span></span>
- [<span data-ttu-id="6e5f1-220">將您的結構描述移轉至 SQL 資料倉儲</span><span class="sxs-lookup"><span data-stu-id="6e5f1-220">Migrate your schemas to SQL Data Warehouse</span></span>](/azure/sql-data-warehouse/sql-data-warehouse-migrate-schema)
- [<span data-ttu-id="6e5f1-221">定義 SQL 資料倉儲中的資料表資料類型的指引</span><span class="sxs-lookup"><span data-stu-id="6e5f1-221">Guidance for defining data types for tables in SQL Data Warehouse</span></span>](/azure/sql-data-warehouse/sql-data-warehouse-tables-data-types)

### <a name="transform-the-data"></a><span data-ttu-id="6e5f1-222">轉換資料</span><span class="sxs-lookup"><span data-stu-id="6e5f1-222">Transform the data</span></span>

<span data-ttu-id="6e5f1-223">轉換資料，並將其移至生產資料表中。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-223">Transform the data and move it into production tables.</span></span> <span data-ttu-id="6e5f1-224">在此步驟中，資料會轉換成具有維度資料表與事實資料表、且適用於語意模型的星型結構描述。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-224">In this step, the data is transformed into a star schema with dimension tables and fact tables, suitable for semantic modeling.</span></span>

<span data-ttu-id="6e5f1-225">建立具有叢集資料行存放區索引的生產資料表，以提供最理想的整體查詢效能。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-225">Create the production tables with clustered columnstore indexes, which offer the best overall query performance.</span></span> <span data-ttu-id="6e5f1-226">資料行存放區索引已針對會掃描多筆記錄的查詢進行最佳化。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-226">Columnstore indexes are optimized for queries that scan many records.</span></span> <span data-ttu-id="6e5f1-227">資料行存放區索引也不會就單一查閱而執行 (也就是查閱單一資料列)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-227">Columnstore indexes don't perform as well for singleton lookups (that is, looking up a single row).</span></span> <span data-ttu-id="6e5f1-228">如果您需要執行頻繁的單一查詢，您可以將非叢集索引新增至資料表。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-228">If you need to perform frequent singleton lookups, you can add a non-clustered index to a table.</span></span> <span data-ttu-id="6e5f1-229">使用非叢集索引的單一查閱可大幅提升執行速度。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-229">Singleton lookups can run significantly faster using a non-clustered index.</span></span> <span data-ttu-id="6e5f1-230">不過，相較於 OLTP 工作負載，在資料倉儲案例中通常較不常使用單一查閱。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-230">However, singleton lookups are typically less common in data warehouse scenarios than OLTP workloads.</span></span> <span data-ttu-id="6e5f1-231">如需詳細資訊，請參閱[在 SQL 資料倉儲中編製資料表的索引](/azure/sql-data-warehouse/sql-data-warehouse-tables-index)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-231">For more information, see [Indexing tables in SQL Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-tables-index).</span></span>

> [!NOTE]
> <span data-ttu-id="6e5f1-232">叢集資料行存放區資料表不支援 `varchar(max)`、`nvarchar(max)` 或 `varbinary(max)` 資料類型。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-232">Clustered columnstore tables do not support `varchar(max)`, `nvarchar(max)`, or `varbinary(max)` data types.</span></span> <span data-ttu-id="6e5f1-233">在此情況下，請考慮使用堆積或叢集索引。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-233">In that case, consider a heap or clustered index.</span></span> <span data-ttu-id="6e5f1-234">您可以將這些資料行放入個別的資料表中。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-234">You might put those columns into a separate table.</span></span>

<span data-ttu-id="6e5f1-235">由於範例資料庫並不龐大，因此我們建立的複寫資料表不含分割區。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-235">Because the sample database is not very large, we created replicated tables with no partitions.</span></span> <span data-ttu-id="6e5f1-236">對於生產工作負載，使用分散式資料表應可改善查詢效能。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-236">For production workloads, using distributed tables is likely to improve query performance.</span></span> <span data-ttu-id="6e5f1-237">請參閱[在 Azure SQL 資料倉儲中設計分散式資料表的指引](/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-237">See [Guidance for designing distributed tables in Azure SQL Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute).</span></span> <span data-ttu-id="6e5f1-238">我們的範例指令碼會使用靜態[資源類別](/azure/sql-data-warehouse/resource-classes-for-workload-management)執行查詢。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-238">Our example scripts run the queries using a static [resource class](/azure/sql-data-warehouse/resource-classes-for-workload-management).</span></span>

### <a name="load-the-semantic-model"></a><span data-ttu-id="6e5f1-239">載入語意模型</span><span class="sxs-lookup"><span data-stu-id="6e5f1-239">Load the semantic model</span></span>

<span data-ttu-id="6e5f1-240">將資料載入至 Azure Analysis Services 中的表格式模型。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-240">Load the data into a tabular model in Azure Analysis Services.</span></span> <span data-ttu-id="6e5f1-241">在此步驟中，您會使用 SQL Server Data Tools (SSDT) 建立語意資料模型。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-241">In this step, you create a semantic data model by using SQL Server Data Tools (SSDT).</span></span> <span data-ttu-id="6e5f1-242">您也可以從 Power BI Desktop 檔案匯入模型，藉以建立模型。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-242">You can also create a model by importing it from a Power BI Desktop file.</span></span> <span data-ttu-id="6e5f1-243">SQL 資料倉儲並不支援外部索引鍵，因此您必須將關聯性新增至語意模型，以便在資料表之間進行聯結。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-243">Because SQL Data Warehouse does not support foreign keys, you must add the relationships to the semantic model, so that you can join across tables.</span></span>

### <a name="use-power-bi-to-visualize-the-data"></a><span data-ttu-id="6e5f1-244">使用 Power BI 以視覺方式呈現資料</span><span class="sxs-lookup"><span data-stu-id="6e5f1-244">Use Power BI to visualize the data</span></span>

<span data-ttu-id="6e5f1-245">Power BI 支援兩個連線至 Azure Analysis Services 的選項：</span><span class="sxs-lookup"><span data-stu-id="6e5f1-245">Power BI supports two options for connecting to Azure Analysis Services:</span></span>

- <span data-ttu-id="6e5f1-246">匯入。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-246">Import.</span></span> <span data-ttu-id="6e5f1-247">資料會匯入 Power BI 模型中。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-247">The data is imported into the Power BI model.</span></span>
- <span data-ttu-id="6e5f1-248">即時連線。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-248">Live Connection.</span></span> <span data-ttu-id="6e5f1-249">資料會直接提取自 Analysis Services。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-249">Data is pulled directly from Analysis Services.</span></span>

<span data-ttu-id="6e5f1-250">建議您使用即時連線，因為它不需要將資料複製到 Power BI 模型中。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-250">We recommend Live Connection because it doesn't require copying data into the Power BI model.</span></span> <span data-ttu-id="6e5f1-251">此外，使用 DirectQuery 可確保結果絕對會與最新的來源資料一致。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-251">Also, using DirectQuery ensures that results are always consistent with the latest source data.</span></span> <span data-ttu-id="6e5f1-252">如需詳細資訊，請參閱[使用 Power BI 進行連線](/azure/analysis-services/analysis-services-connect-pbi)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-252">For more information, see [Connect with Power BI](/azure/analysis-services/analysis-services-connect-pbi).</span></span>

<span data-ttu-id="6e5f1-253">**建議**</span><span class="sxs-lookup"><span data-stu-id="6e5f1-253">**Recommendations**</span></span>

<span data-ttu-id="6e5f1-254">請避免直接對資料倉儲執行 BI 儀表板查詢。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-254">Avoid running BI dashboard queries directly against the data warehouse.</span></span> <span data-ttu-id="6e5f1-255">BI 儀表板需要非常短的回應時間，直接對倉儲執行的查詢可能達不到此需求。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-255">BI dashboards require very low response times, which direct queries against the warehouse may be unable to satisfy.</span></span> <span data-ttu-id="6e5f1-256">此外，重新整理儀表板將會計入並行查詢數目，而可能會影響效能。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-256">Also, refreshing the dashboard will count against the number of concurrent queries, which could impact performance.</span></span> 

<span data-ttu-id="6e5f1-257">Azure Analysis Services 依設計可用來處理 BI 儀表板的查詢需求，因此，建議的作法是從 Power BI 查詢 Analysis Services。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-257">Azure Analysis Services is designed to handle the query requirements of a BI dashboard, so the recommended practice is to query Analysis Services from Power BI.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="6e5f1-258">延展性考量</span><span class="sxs-lookup"><span data-stu-id="6e5f1-258">Scalability considerations</span></span>

### <a name="sql-data-warehouse"></a><span data-ttu-id="6e5f1-259">SQL 資料倉儲</span><span class="sxs-lookup"><span data-stu-id="6e5f1-259">SQL Data Warehouse</span></span>

<span data-ttu-id="6e5f1-260">透過 SQL 資料倉儲，您可以隨需相應放大您的計算資源。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-260">With SQL Data Warehouse, you can scale out your compute resources on demand.</span></span> <span data-ttu-id="6e5f1-261">查詢引擎可根據計算節點的數目最佳化要平行處理的查詢，並視需要在節點之間移動資料。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-261">The query engine optimizes queries for parallel processing based on the number of compute nodes, and moves data between nodes as necessary.</span></span> <span data-ttu-id="6e5f1-262">如需詳細資訊，請參閱[管理 Azure SQL 資料倉儲中的計算能力](/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-262">For more information, see [Manage compute in Azure SQL Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview).</span></span>

### <a name="analysis-services"></a><span data-ttu-id="6e5f1-263">Analysis Services</span><span class="sxs-lookup"><span data-stu-id="6e5f1-263">Analysis Services</span></span>

<span data-ttu-id="6e5f1-264">對於生產工作負載，建議您使用 Azure Analysis Services 的標準層，因為它支援分割區和 DirectQuery。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-264">For production workloads, we recommend the Standard Tier for Azure Analysis Services, because it supports partitioning and DirectQuery.</span></span> <span data-ttu-id="6e5f1-265">在一層之中，執行個體大小會決定記憶體和處理能力。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-265">Within a tier, the instance size determines the memory and processing power.</span></span> <span data-ttu-id="6e5f1-266">處理能力會以查詢處理單位 (QPU) 來測量。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-266">Processing power is measured in Query Processing Units (QPUs).</span></span> <span data-ttu-id="6e5f1-267">請監視 QPU 使用量以選取適當的大小。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-267">Monitor your QPU usage to select the appropriate size.</span></span> <span data-ttu-id="6e5f1-268">如需詳細資訊，請參閱[監視伺服器計量](/azure/analysis-services/analysis-services-monitor)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-268">For more information, see [Monitor server metrics](/azure/analysis-services/analysis-services-monitor).</span></span>

<span data-ttu-id="6e5f1-269">在高負載的情況下，查詢效能可能會因為查詢並行而下降。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-269">Under high load, query performance can become degraded due to query concurrency.</span></span> <span data-ttu-id="6e5f1-270">您可以建立用來處理查詢的複本集區以相應放大 Analysis Services，以便同時執行多個查詢。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-270">You can scale out Analysis Services by creating a pool of replicas to process queries, so that more queries can be performed concurrently.</span></span> <span data-ttu-id="6e5f1-271">處理資料模型的工作一律會在主要伺服器上執行。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-271">The work of processing the data model always happens on the primary server.</span></span> <span data-ttu-id="6e5f1-272">根據預設，主要伺服器也會處理查詢。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-272">By default, the primary server also handles queries.</span></span> <span data-ttu-id="6e5f1-273">您可以選擇性地指定以獨佔方式執行處理的主要伺服器，讓查詢集區可處理所有查詢。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-273">Optionally, you can designate the primary server to run processing exclusively, so that the query pool handles all queries.</span></span> <span data-ttu-id="6e5f1-274">如果您的處理需求較高，則應將處理獨立於查詢集區以外。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-274">If you have high processing requirements, you should separate the processing from the query pool.</span></span> <span data-ttu-id="6e5f1-275">如果您的查詢負載較高，且處理負載相對較低，則可以將主要伺服器包含在查詢集區中。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-275">If you have high query loads, and relatively light processing, you can include the primary server in the query pool.</span></span> <span data-ttu-id="6e5f1-276">如需詳細資訊，請參閱 [Azure Analysis Services 相應放大](/azure/analysis-services/analysis-services-scale-out)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-276">For more information, see [Azure Analysis Services scale-out](/azure/analysis-services/analysis-services-scale-out).</span></span> 

<span data-ttu-id="6e5f1-277">若要減少非必要的處理量，請考慮使用分割區將表格式模型分成多個邏輯部分。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-277">To reduce the amount of unnecessary processing, consider using partitions to divide the tabular model into logical parts.</span></span> <span data-ttu-id="6e5f1-278">每個分割區將可個別處理。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-278">Each partition can be processed separately.</span></span> <span data-ttu-id="6e5f1-279">如需詳細資訊，請參閱[分割區](/sql/analysis-services/tabular-models/partitions-ssas-tabular)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-279">For more information, see [Partitions](/sql/analysis-services/tabular-models/partitions-ssas-tabular).</span></span>

## <a name="security-considerations"></a><span data-ttu-id="6e5f1-280">安全性考量</span><span class="sxs-lookup"><span data-stu-id="6e5f1-280">Security considerations</span></span>

### <a name="ip-whitelisting-of-analysis-services-clients"></a><span data-ttu-id="6e5f1-281">Analysis Services 用戶端的 IP 白名單</span><span class="sxs-lookup"><span data-stu-id="6e5f1-281">IP whitelisting of Analysis Services clients</span></span>

<span data-ttu-id="6e5f1-282">請考慮使用 Analysis Services 防火牆功能，將用戶端 IP 位址列入白清單中。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-282">Consider using the Analysis Services firewall feature to whitelist client IP addresses.</span></span> <span data-ttu-id="6e5f1-283">防火牆啟用時，會封鎖未指定於防火牆規則中的所有用戶端連線。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-283">If enabled, the firewall blocks all client connections other than those specified in the firewall rules.</span></span> <span data-ttu-id="6e5f1-284">預設規則會將 Power BI 服務列入白名單中，但您可以視需要停用此規則。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-284">The default rules whitelist the Power BI service, but you can disable this rule if desired.</span></span> <span data-ttu-id="6e5f1-285">如需詳細資訊，請參閱[使用新的防火牆功能強化 Azure Analysis Services](https://azure.microsoft.com/blog/hardening-azure-analysis-services-with-the-new-firewall-capability/)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-285">For more information, see [Hardening Azure Analysis Services with the new firewall capability](https://azure.microsoft.com/blog/hardening-azure-analysis-services-with-the-new-firewall-capability/).</span></span>

### <a name="authorization"></a><span data-ttu-id="6e5f1-286">Authorization</span><span class="sxs-lookup"><span data-stu-id="6e5f1-286">Authorization</span></span>

<span data-ttu-id="6e5f1-287">Azure Analysis Services 會使用 Azure Active Directory (Azure AD) 驗證連線至 Analysis Services 伺服器的使用者。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-287">Azure Analysis Services uses Azure Active Directory (Azure AD) to authenticate users who connect to an Analysis Services server.</span></span> <span data-ttu-id="6e5f1-288">您可以建立角色，然後將 Azure AD 使用者或群組指派給這些角色，以限制特定使用者所能檢視的資料。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-288">You can restrict what data a particular user is able to view, by creating roles and then assigning Azure AD users or groups to those roles.</span></span> <span data-ttu-id="6e5f1-289">對於每個角色，您可以︰</span><span class="sxs-lookup"><span data-stu-id="6e5f1-289">For each role, you can:</span></span> 

- <span data-ttu-id="6e5f1-290">保護資料表或個別資料行。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-290">Protect tables or individual columns.</span></span> 
- <span data-ttu-id="6e5f1-291">根據篩選運算式保護個別資料列。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-291">Protect individual rows based on filter expressions.</span></span> 

<span data-ttu-id="6e5f1-292">如需詳細資訊，請參閱[管理資料庫角色和使用者](/azure/analysis-services/analysis-services-database-users)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-292">For more information, see [Manage database roles and users](/azure/analysis-services/analysis-services-database-users).</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="6e5f1-293">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="6e5f1-293">Deploy the solution</span></span>

<span data-ttu-id="6e5f1-294">此參考架構的部署可在 [GitHub][ref-arch-repo-folder] 上取得。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-294">A deployment for this reference architecture is available on [GitHub][ref-arch-repo-folder].</span></span> <span data-ttu-id="6e5f1-295">它會部署下列各項：</span><span class="sxs-lookup"><span data-stu-id="6e5f1-295">It deploys the following:</span></span>

  * <span data-ttu-id="6e5f1-296">一個 Windows VM，用以模擬內部部署資料庫伺服器。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-296">A Windows VM to simulate an on-premises database server.</span></span> <span data-ttu-id="6e5f1-297">其中包含 SQL Server 2017 和相關工具以及 Power BI Desktop。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-297">It includes SQL Server 2017 and related tools, along with Power BI Desktop.</span></span>
  * <span data-ttu-id="6e5f1-298">一個提供 Blob 儲存體的 Azure 儲存體帳戶，用以保存從 SQL Server 資料庫匯出的資料。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-298">An Azure storage account that provides Blob storage to hold data exported from the SQL Server database.</span></span>
  * <span data-ttu-id="6e5f1-299">一個 Azure SQL 資料倉儲執行個體。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-299">An Azure SQL Data Warehouse instance.</span></span>
  * <span data-ttu-id="6e5f1-300">一個 Azure Analysis Services 執行個體。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-300">An Azure Analysis Services instance.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="6e5f1-301">先決條件</span><span class="sxs-lookup"><span data-stu-id="6e5f1-301">Prerequisites</span></span>

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-the-simulated-on-premises-server"></a><span data-ttu-id="6e5f1-302">部署模擬的內部部署伺服器</span><span class="sxs-lookup"><span data-stu-id="6e5f1-302">Deploy the simulated on-premises server</span></span>

<span data-ttu-id="6e5f1-303">首先，您會將 VM 部署為模擬的內部部署伺服器，其中包含 SQL Server 2017 和相關工具。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-303">First you'll deploy a VM as a simulated on-premises server, which includes SQL Server 2017 and related tools.</span></span> <span data-ttu-id="6e5f1-304">這個步驟也會將 [Wide World Importers OLTP 資料庫][wwi]載入 SQL Server 中。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-304">This step also loads the [Wide World Importers OLTP database][wwi] into SQL Server.</span></span>

1. <span data-ttu-id="6e5f1-305">巡覽至存放庫的 `data\enterprise_bi_sqldw\onprem\templates` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-305">Navigate to the `data\enterprise_bi_sqldw\onprem\templates` folder of the repository.</span></span>

2. <span data-ttu-id="6e5f1-306">在 `onprem.parameters.json` 檔案中，取代 `adminUsername` 和 `adminPassword` 的值。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-306">In the `onprem.parameters.json` file, replace the values for `adminUsername` and `adminPassword`.</span></span> <span data-ttu-id="6e5f1-307">此外也應變更 `SqlUserCredentials` 區段中的值，以符合使用者名稱和密碼。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-307">Also change the values in the `SqlUserCredentials` section to match the user name and password.</span></span> <span data-ttu-id="6e5f1-308">請留意 userName 屬性中的 `.\\` 前置詞。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-308">Note the `.\\` prefix in the userName property.</span></span>
    
    ```bash
    "SqlUserCredentials": {
      "userName": ".\\username",
      "password": "password"
    }
    ```

3. <span data-ttu-id="6e5f1-309">依照下列方式執行 `azbb`，以部署內部部署伺服器。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-309">Run `azbb` as shown below to deploy the on-premises server.</span></span>

    ```bash
    azbb -s <subscription_id> -g <resource_group_name> -l <region> -p onprem.parameters.json --deploy
    ```

    <span data-ttu-id="6e5f1-310">指定可支援 SQL 資料倉儲和 Azure Analysis Services 的區域。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-310">Specify a region that supports SQL Data Warehouse and Azure Analysis Services.</span></span> <span data-ttu-id="6e5f1-311">請參閱[不同區域的產品](https://azure.microsoft.com/global-infrastructure/services/)</span><span class="sxs-lookup"><span data-stu-id="6e5f1-311">See [Azure Products by Region](https://azure.microsoft.com/global-infrastructure/services/)</span></span>

4. <span data-ttu-id="6e5f1-312">此部署可能需要 20 到 30 分鐘才能完成，其中包括執行 [DSC](/powershell/dsc/overview) 指令碼以安裝工具和還原資料庫。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-312">The deployment may take 20 to 30 minutes to complete, which includes running the [DSC](/powershell/dsc/overview) script to install the tools and restore the database.</span></span> <span data-ttu-id="6e5f1-313">在 Azure 入口網站中檢閱資源群組中的資源，以驗證部署。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-313">Verify the deployment in the Azure portal by reviewing the resources in the resource group.</span></span> <span data-ttu-id="6e5f1-314">您應該會看到 `sql-vm1` 虛擬機器及其相關聯的資源。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-314">You should see the `sql-vm1` virtual machine and its associated resources.</span></span>

### <a name="deploy-the-azure-resources"></a><span data-ttu-id="6e5f1-315">部署 Azure 資源</span><span class="sxs-lookup"><span data-stu-id="6e5f1-315">Deploy the Azure resources</span></span>

<span data-ttu-id="6e5f1-316">此步驟會佈建 SQL 資料倉儲和 Azure Analysis Services，以及儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-316">This step provisions SQL Data Warehouse and Azure Analysis Services, along with a Storage account.</span></span> <span data-ttu-id="6e5f1-317">如果您想，您可以將此步驟與上一個步驟平行執行。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-317">If you want, you can run this step in parallel with the previous step.</span></span>

1. <span data-ttu-id="6e5f1-318">巡覽至存放庫的 `data\enterprise_bi_sqldw\azure\templates` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-318">Navigate to the `data\enterprise_bi_sqldw\azure\templates` folder of the repository.</span></span>

2. <span data-ttu-id="6e5f1-319">請執行下列 Azure CLI 命令以建立資源群組。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-319">Run the following Azure CLI command to create a resource group.</span></span> <span data-ttu-id="6e5f1-320">您可以部署到與上一個步驟中不同的資源群組，但請選擇相同的區域。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-320">You can deploy to a different resource group than the previous step, but choose the same region.</span></span> 

    ```bash
    az group create --name <resource_group_name> --location <region>  
    ```

3. <span data-ttu-id="6e5f1-321">請執行下列 Azure CLI 命令以部署 Azure 資源。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-321">Run the following Azure CLI command to deploy the Azure resources.</span></span> <span data-ttu-id="6e5f1-322">請取代角括弧中所顯示的參數值。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-322">Replace the parameter values shown in angle brackets.</span></span> 

    ```bash
    az group deployment create --resource-group <resource_group_name> \
     --template-file azure-resources-deploy.json \
     --parameters "dwServerName"="<server_name>" \
     "dwAdminLogin"="<admin_username>" "dwAdminPassword"="<password>" \ 
     "storageAccountName"="<storage_account_name>" \
     "analysisServerName"="<analysis_server_name>" \
     "analysisServerAdmin"="user@contoso.com"
    ```

    - <span data-ttu-id="6e5f1-323">`storageAccountName` 參數必須遵循儲存體帳戶的[命名規則](../../best-practices/naming-conventions.md#naming-rules-and-restrictions)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-323">The `storageAccountName` parameter must follow the [naming rules](../../best-practices/naming-conventions.md#naming-rules-and-restrictions) for Storage accounts.</span></span>
    - <span data-ttu-id="6e5f1-324">對於 `analysisServerAdmin` 參數，請使用您的 Azure Active Directory 使用者主體名稱 (UPN)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-324">For the `analysisServerAdmin` parameter, use your Azure Active Directory user principal name (UPN).</span></span>

4. <span data-ttu-id="6e5f1-325">在 Azure 入口網站中檢閱資源群組中的資源，以驗證部署。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-325">Verify the deployment in the Azure portal by reviewing the resources in the resource group.</span></span> <span data-ttu-id="6e5f1-326">您應該會看到儲存體帳戶、Azure SQL 資料倉儲執行個體和 Analysis Services 執行個體。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-326">You should see a storage account, Azure SQL Data Warehouse instance, and Analysis Services instance.</span></span>

5. <span data-ttu-id="6e5f1-327">使用 Azure 入口網站取得儲存體帳戶的存取金鑰。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-327">Use the Azure portal to get the access key for the storage account.</span></span> <span data-ttu-id="6e5f1-328">選取要開啟的儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-328">Select the storage account to open it.</span></span> <span data-ttu-id="6e5f1-329">在 [設定] 底下，選取 [存取金鑰]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-329">Under **Settings**, select **Access keys**.</span></span> <span data-ttu-id="6e5f1-330">複製主要金鑰值。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-330">Copy the primary key value.</span></span> <span data-ttu-id="6e5f1-331">您在下一個步驟將會用到此值。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-331">You will use it in the next step.</span></span>

### <a name="export-the-source-data-to-azure-blob-storage"></a><span data-ttu-id="6e5f1-332">將來源資料匯出至 Azure Blob 儲存體</span><span class="sxs-lookup"><span data-stu-id="6e5f1-332">Export the source data to Azure Blob storage</span></span> 

<span data-ttu-id="6e5f1-333">在此步驟中，您將執行 PowerShell 指令碼以使用 bcp 將 SQL 資料庫匯出至 VM 上的一般檔案，然後使用 AzCopy 將這些檔案複製到 Azure Blob 儲存體中。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-333">In this step, you will run a PowerShell script that uses bcp to export the SQL database to flat files on the VM, and then uses AzCopy to copy those files into Azure Blob Storage.</span></span>

1. <span data-ttu-id="6e5f1-334">使用遠端桌面連線至模擬的內部部署 VM。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-334">Use Remote Desktop to connect to the simulated on-premises VM.</span></span>

2. <span data-ttu-id="6e5f1-335">登入 VM 後，從 PowerShell 視窗執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-335">While logged into the VM, run the following commands from a PowerShell window.</span></span>  

    ```powershell
    cd 'C:\SampleDataFiles\reference-architectures\data\enterprise_bi_sqldw\onprem'

    .\Load_SourceData_To_Blob.ps1 -File .\sql_scripts\db_objects.txt -Destination 'https://<storage_account_name>.blob.core.windows.net/wwi' -StorageAccountKey '<storage_account_key>'
    ```

    <span data-ttu-id="6e5f1-336">針對 `Destination` 參數，請將 `<storage_account_name>` 取代為您先前建立的儲存體帳戶的名稱。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-336">For the `Destination` parameter, replace `<storage_account_name>` with the name the Storage account that you created previously.</span></span> <span data-ttu-id="6e5f1-337">針對 `StorageAccountKey` 參數，請使用該儲存體帳戶的存取金鑰。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-337">For the `StorageAccountKey` parameter, use the access key for that Storage account.</span></span>

3. <span data-ttu-id="6e5f1-338">在 Azure 入口網站中，瀏覽至儲存體帳戶、選取 Blob 服務，並開啟 `wwi` 容器，以確認來源資料已複製到 Blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-338">In the Azure portal, verify that the source data was copied to Blob storage by navigating to the storage account, selecting the Blob service, and opening the `wwi` container.</span></span> <span data-ttu-id="6e5f1-339">您應該會看到以 `WorldWideImporters_Application_*` 開頭的資料表清單。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-339">You should see a list of tables prefaced with `WorldWideImporters_Application_*`.</span></span>

### <a name="run-the-data-warehouse-scripts"></a><span data-ttu-id="6e5f1-340">執行資料倉儲指令碼</span><span class="sxs-lookup"><span data-stu-id="6e5f1-340">Run the data warehouse scripts</span></span>

1. <span data-ttu-id="6e5f1-341">在您的遠端桌面工作階段中，啟動 SQL Server Management Studio (SSMS)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-341">From your Remote Desktop session, launch SQL Server Management Studio (SSMS).</span></span> 

2. <span data-ttu-id="6e5f1-342">連線到 SQL 資料倉儲</span><span class="sxs-lookup"><span data-stu-id="6e5f1-342">Connect to SQL Data Warehouse</span></span>

    - <span data-ttu-id="6e5f1-343">伺服器類型：資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="6e5f1-343">Server type: Database Engine</span></span>
    
    - <span data-ttu-id="6e5f1-344">伺服器名稱：`<dwServerName>.database.windows.net`，其中，`<dwServerName>` 是您在部署 Azure 資源時所指定的名稱。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-344">Server name: `<dwServerName>.database.windows.net`, where `<dwServerName>` is the name that you specified when you deployed the Azure resources.</span></span> <span data-ttu-id="6e5f1-345">您可以從 Azure 入口網站中取得此名稱。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-345">You can get this name from the Azure portal.</span></span>
    
    - <span data-ttu-id="6e5f1-346">驗證：SQL Server 驗證。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-346">Authentication: SQL Server Authentication.</span></span> <span data-ttu-id="6e5f1-347">請使用您在部署 Azure 資源時指定於 `dwAdminLogin` 和 `dwAdminPassword` 參數中的認證。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-347">Use the credentials that you specified when you deployed the Azure resources, in the `dwAdminLogin` and `dwAdminPassword` parameters.</span></span>

2. <span data-ttu-id="6e5f1-348">瀏覽至 VM 上的 `C:\SampleDataFiles\reference-architectures\data\enterprise_bi_sqldw\azure\sqldw_scripts` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-348">Navigate to the `C:\SampleDataFiles\reference-architectures\data\enterprise_bi_sqldw\azure\sqldw_scripts` folder on the VM.</span></span> <span data-ttu-id="6e5f1-349">您將依數值順序執行此資料夾中的指令碼 `STEP_1` 到 `STEP_7`。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-349">You will execute the scripts in this folder in numerical order, `STEP_1` through `STEP_7`.</span></span>

3. <span data-ttu-id="6e5f1-350">在 SSMS 中選取 `master` 資料庫，並開啟 `STEP_1` 指令碼。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-350">Select the `master` database in SSMS and open the `STEP_1` script.</span></span> <span data-ttu-id="6e5f1-351">請變更以下這一行中的密碼值，然後執行指令碼。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-351">Change the value of the password in the following line, then execute the script.</span></span>

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = '<change this value>';
    ```

4. <span data-ttu-id="6e5f1-352">在 SSMS 中選取 `wwi` 資料庫。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-352">Select the `wwi` database in SSMS.</span></span> <span data-ttu-id="6e5f1-353">開啟 `STEP_2` 指令碼，並執行指令碼。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-353">Open the `STEP_2` script and execute the script.</span></span> <span data-ttu-id="6e5f1-354">如果發生錯誤，請確定您是對 `wwi` 資料庫執行指令碼，而非對 `master` 執行。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-354">If you get an error, make sure you are running the script against the `wwi` database and not `master`.</span></span>

5. <span data-ttu-id="6e5f1-355">使用 `STEP_1` 指令碼中指定的 `LoaderRC20` 使用者和密碼，開啟對 SQL 資料倉儲的新連線。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-355">Open a new connection to SQL Data Warehouse, using the `LoaderRC20` user and the password indicated in the `STEP_1` script.</span></span>

6. <span data-ttu-id="6e5f1-356">使用此連線開啟 `STEP_3` 指令碼。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-356">Using this connection, open the `STEP_3` script.</span></span> <span data-ttu-id="6e5f1-357">在指令碼中設定下列值：</span><span class="sxs-lookup"><span data-stu-id="6e5f1-357">Set the following values in the script:</span></span>

    - <span data-ttu-id="6e5f1-358">密碼：使用儲存體帳戶的存取金鑰。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-358">SECRET: Use the access key for your storage account.</span></span>
    - <span data-ttu-id="6e5f1-359">位置︰使用儲存體帳戶的名稱，如下所示：`wasbs://wwi@<storage_account_name>.blob.core.windows.net`。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-359">LOCATION: Use the name of the storage account as follows: `wasbs://wwi@<storage_account_name>.blob.core.windows.net`.</span></span>

7. <span data-ttu-id="6e5f1-360">使用相同的連線，依序執行指令碼 `STEP_4` 到 `STEP_7`。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-360">Using the same connection, execute scripts `STEP_4` through `STEP_7` sequentially.</span></span> <span data-ttu-id="6e5f1-361">請確認一個指令碼順利完成後，再執行下一個。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-361">Verify that each script completes successfully before running the next.</span></span>

<span data-ttu-id="6e5f1-362">在 SMSS 中，您應該會在 `wwi` 資料庫中看到一組 `prd.*` 資料表。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-362">In SMSS, you should see a set of `prd.*` tables in the `wwi` database.</span></span> <span data-ttu-id="6e5f1-363">若要確認資料已產生，請執行下列查詢：</span><span class="sxs-lookup"><span data-stu-id="6e5f1-363">To verify that the data was generated, run the following query:</span></span> 

```sql
SELECT TOP 10 * FROM prd.CityDimensions
```

## <a name="build-the-analysis-services-model"></a><span data-ttu-id="6e5f1-364">建置 Analysis Services 模型</span><span class="sxs-lookup"><span data-stu-id="6e5f1-364">Build the Analysis Services model</span></span>

<span data-ttu-id="6e5f1-365">在此步驟中，您將建立會從資料倉儲匯入資料的表格式模型。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-365">In this step, you will create a tabular model that imports data from the data warehouse.</span></span> <span data-ttu-id="6e5f1-366">然後，您會將模型部署至 Azure Analysis Services。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-366">Then you will deploy the model to Azure Analysis Services.</span></span>

1. <span data-ttu-id="6e5f1-367">在您的遠端桌面工作階段中，啟動 SQL Server Data Tools 2015。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-367">From your Remote Desktop session, launch SQL Server Data Tools 2015.</span></span>

2. <span data-ttu-id="6e5f1-368">選取 [檔案] > [新增] > [專案]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-368">Select **File** > **New** > **Project**.</span></span>

3. <span data-ttu-id="6e5f1-369">在 [新增專案] 對話方塊中的 [範本] 下，選取 [商業智慧] > [Analysis Services] > [Analysis Services 表格式專案]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-369">In the **New Project** dialog, under **Templates**, select  **Business Intelligence** > **Analysis Services** > **Analysis Services Tabular Project**.</span></span> 

4. <span data-ttu-id="6e5f1-370">為專案命名，然後按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-370">Name the project and click **OK**.</span></span>

5. <span data-ttu-id="6e5f1-371">在 [表格式模型設計工具] 對話方塊中，選取 [整合式工作區]，然後將 [相容性層級] 設為 `SQL Server 2017 / Azure Analysis Services (1400)`。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-371">In the **Tabular model designer** dialog, select **Integrated workspace**  and set **Compatibility level** to `SQL Server 2017 / Azure Analysis Services (1400)`.</span></span> <span data-ttu-id="6e5f1-372">按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-372">Click **OK**.</span></span>

6. <span data-ttu-id="6e5f1-373">在 [表格式模型總管] 視窗中，以滑鼠右鍵按一下專案，然後選取 [從資料來源匯入]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-373">In the **Tabular Model Explorer** window, right-click the project and select **Import from Data Source**.</span></span>

7. <span data-ttu-id="6e5f1-374">選取 [Azure SQL 資料倉儲]，然後按一下 [連線]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-374">Select **Azure SQL Data Warehouse** and click **Connect**.</span></span>

8. <span data-ttu-id="6e5f1-375">針對 [伺服器]，輸入 Azure SQL 資料倉儲伺服器的完整名稱。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-375">For **Server**, enter the fully qualified name of your Azure SQL Data Warehouse server.</span></span> <span data-ttu-id="6e5f1-376">針對 [資料庫]，輸入 `wwi`。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-376">For **Database**, enter `wwi`.</span></span> <span data-ttu-id="6e5f1-377">按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-377">Click **OK**.</span></span>

9. <span data-ttu-id="6e5f1-378">在下一個對話方塊中，選擇 [資料庫] 驗證，並輸入您的 Azure SQL 資料倉儲的使用者名稱和密碼，然後按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-378">In the next dialog, choose **Database** authentication and enter your Azure SQL Data Warehouse user name and password, and click **OK**.</span></span>

10. <span data-ttu-id="6e5f1-379">在 [導覽器] 對話方塊中，選取 **prd.CityDimensions**、**prd.DateDimensions** 和 **prd.SalesFact** 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-379">In the **Navigator** dialog, select the checkboxes for **prd.CityDimensions**, **prd.DateDimensions**, and **prd.SalesFact**.</span></span> 

    ![](./images/analysis-services-import.png)

11. <span data-ttu-id="6e5f1-380">按一下 [載入]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-380">Click **Load**.</span></span> <span data-ttu-id="6e5f1-381">在處理完成時，按一下 [關閉]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-381">When processing is complete, click **Close**.</span></span> <span data-ttu-id="6e5f1-382">您現在應該會看到資料的表格式檢視。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-382">You should now see a tabular view of the data.</span></span>

12. <span data-ttu-id="6e5f1-383">在 [表格式模型總管] 視窗中，以滑鼠右鍵按一下專案，然後選取 [模型檢視] > [圖表檢視]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-383">In the **Tabular Model Explorer** window, right-click the project and select **Model View** > **Diagram View**.</span></span>

13. <span data-ttu-id="6e5f1-384">將 **[prd.SalesFact].[WWI City ID]** 欄位拖曳至 **[prd.CityDimensions].[WWI City ID]** 欄位，以建立關聯性。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-384">Drag the **[prd.SalesFact].[WWI City ID]** field to the **[prd.CityDimensions].[WWI City ID]** field to create a relationship.</span></span>  

14. <span data-ttu-id="6e5f1-385">將 **[prd.SalesFact].[Invoice Date Key]** 欄位拖曳至 **[prd.DateDimensions].[Date]** 欄位。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-385">Drag the **[prd.SalesFact].[Invoice Date Key]** field to the **[prd.DateDimensions].[Date]** field.</span></span>  
    ![](./images/analysis-services-relations.png)

15. <span data-ttu-id="6e5f1-386">在 [檔案] 功能表中，選擇 [全部儲存]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-386">From the **File** menu, choose **Save All**.</span></span>  

16. <span data-ttu-id="6e5f1-387">在 [方案總管] 中，以滑鼠右鍵按一下專案，然後選取 [屬性]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-387">In **Solution Explorer**, right-click the project and select **Properties**.</span></span> 

17. <span data-ttu-id="6e5f1-388">在 [伺服器] 下，輸入 Azure Analysis Services 執行個體的 URL。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-388">Under **Server**, enter the URL of your Azure Analysis Services instance.</span></span> <span data-ttu-id="6e5f1-389">您可以從 Azure 入口網站中取得此值。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-389">You can get this value from the Azure Portal.</span></span> <span data-ttu-id="6e5f1-390">在入口網站中選取 Analysis Services 資源，按一下 [概觀] 窗格，然後尋找 [伺服器名稱] 屬性。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-390">In the portal, select the Analysis Services resource, click the Overview pane, and look for the **Server Name** property.</span></span> <span data-ttu-id="6e5f1-391">該屬性將類似於 `asazure://westus.asazure.windows.net/contoso`。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-391">It will be similar to `asazure://westus.asazure.windows.net/contoso`.</span></span> <span data-ttu-id="6e5f1-392">按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-392">Click **OK**.</span></span>

    ![](./images/analysis-services-properties.png)

18. <span data-ttu-id="6e5f1-393">在 [方案總管] 中，以滑鼠右鍵按一下專案，然後選取 [部署]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-393">In **Solution Explorer**, right-click the project and select **Deploy**.</span></span> <span data-ttu-id="6e5f1-394">在出現提示時，登入 Azure。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-394">Sign into Azure if prompted.</span></span> <span data-ttu-id="6e5f1-395">在處理完成時，按一下 [關閉]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-395">When processing is complete, click **Close**.</span></span>

19. <span data-ttu-id="6e5f1-396">在 Azure 入口網站中，檢視 Azure Analysis Services 執行個體的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-396">In the Azure portal, view the details for your Azure Analysis Services instance.</span></span> <span data-ttu-id="6e5f1-397">請確認您的模型出現在模型清單中。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-397">Verify that your model appears in the list of models.</span></span>

    ![](./images/analysis-services-models.png)

## <a name="analyze-the-data-in-power-bi-desktop"></a><span data-ttu-id="6e5f1-398">在 Power BI Desktop 中分析資料</span><span class="sxs-lookup"><span data-stu-id="6e5f1-398">Analyze the data in Power BI Desktop</span></span>

<span data-ttu-id="6e5f1-399">在此步驟中，您將使用 Power BI 從 Analysis Services 中的資料建立報告。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-399">In this step, you will use Power BI to create a report from the data in Analysis Services.</span></span>

1. <span data-ttu-id="6e5f1-400">在您的遠端桌面工作階段中，啟動 Power BI Desktop。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-400">From your Remote Desktop session, launch Power BI Desktop.</span></span>

2. <span data-ttu-id="6e5f1-401">在 [歡迎使用] 畫面中，按一下 [取得資料]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-401">In the Welcome Scren, click **Get Data**.</span></span>

3. <span data-ttu-id="6e5f1-402">選取 [Azure] > [Azure Analysis Services 資料庫]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-402">Select **Azure** > **Azure Analysis Services database**.</span></span> <span data-ttu-id="6e5f1-403">按一下 [連接] </span><span class="sxs-lookup"><span data-stu-id="6e5f1-403">Click **Connect**</span></span>

    ![](./images/power-bi-get-data.png)

4. <span data-ttu-id="6e5f1-404">輸入 Analysis Services 執行個體的 URL，然後按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-404">Enter the URL of your Analysis Services instance, then click **OK**.</span></span> <span data-ttu-id="6e5f1-405">在出現提示時，登入 Azure。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-405">Sign into Azure if prompted.</span></span>

5. <span data-ttu-id="6e5f1-406">在 [導覽器] 對話方塊中，展開您所部署的表格式專案，選取您所建立的模型，然後按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-406">In the **Navigator** dialog, expand the tabular project that you deployed, select the model that you created, and click **OK**.</span></span>

2. <span data-ttu-id="6e5f1-407">在 [視覺效果] 窗格中，選取 [堆疊橫條圖] 圖示。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-407">In the **Visualizations** pane, select the **Stacked Bar Chart** icon.</span></span> <span data-ttu-id="6e5f1-408">在 [報告] 檢視中，將視覺效果調整得大一些。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-408">In the Report view, resize the visualization to make it larger.</span></span>

6. <span data-ttu-id="6e5f1-409">在 [欄位] 窗格中，展開 **prd.CityDimensions**。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-409">In the **Fields** pane, expand **prd.CityDimensions**.</span></span>

7. <span data-ttu-id="6e5f1-410">也請將 [prd.CityDimensions] > [WWI 城市識別碼] 拖曳至 [軸] 區。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-410">Drag **prd.CityDimensions** > **WWI City ID** to the **Axis** well.</span></span>

8. <span data-ttu-id="6e5f1-411">將 **prd.CityDimensions** > [城市] 拖曳至 [圖例] 區。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-411">Drag **prd.CityDimensions** > **City** to the **Legend** well.</span></span>

9. <span data-ttu-id="6e5f1-412">在 [欄位] 窗格中，展開 **prd.SalesFact**。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-412">In the **Fields** pane, expand **prd.SalesFact**.</span></span>

10. <span data-ttu-id="6e5f1-413">將 **prd.SalesFact** > [未稅額總計] 拖曳至 [值] 區。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-413">Drag **prd.SalesFact** > **Total Excluding Tax** to the **Value** well.</span></span>

    ![](./images/power-bi-visualization.png)

11. <span data-ttu-id="6e5f1-414">在 [視覺效果層級篩選] 下，選取 [WWI 城市識別碼]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-414">Under **Visual Level Filters**, select **WWI City ID**.</span></span>

12. <span data-ttu-id="6e5f1-415">將 [篩選類型] 設為 `Top N`，並將 [顯示項目] 設為 `Top 10`。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-415">Set the **Filter Type** to `Top N`, and set **Show Items** to `Top 10`.</span></span>

13. <span data-ttu-id="6e5f1-416">將 **prd.SalesFact** > [未稅額總計] 拖曳至 [依據值] 區</span><span class="sxs-lookup"><span data-stu-id="6e5f1-416">Drag **prd.SalesFact** > **Total Excluding Tax** to the **By Value** well</span></span>

    ![](./images/power-bi-visualization2.png)

14. <span data-ttu-id="6e5f1-417">按一下 [套用篩選]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-417">Click **Apply Filter**.</span></span> <span data-ttu-id="6e5f1-418">視覺效果會依城市顯示前 10 個總銷售量。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-418">The visualization shows the top 10 total sales by city.</span></span>

    ![](./images/power-bi-report.png)

<span data-ttu-id="6e5f1-419">若要深入了解 Power BI Desktop，請參閱[開始使用 Power BI Desktop](/power-bi/desktop-getting-started)。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-419">To learn more about Power BI Desktop, see [Getting started with Power BI Desktop](/power-bi/desktop-getting-started).</span></span>

## <a name="next-steps"></a><span data-ttu-id="6e5f1-420">後續步驟</span><span class="sxs-lookup"><span data-stu-id="6e5f1-420">Next steps</span></span>

- <span data-ttu-id="6e5f1-421">如需關於此參考架構的詳細資訊，請瀏覽我們的 [GitHub 存放庫][ref-arch-repo-folder]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-421">For more information about this reference architecture, visit our [GitHub repository][ref-arch-repo-folder].</span></span>
- <span data-ttu-id="6e5f1-422">了解 [Azure 建置組塊][azbb-repo]。</span><span class="sxs-lookup"><span data-stu-id="6e5f1-422">Learn about the [Azure Building Blocks][azbb-repo].</span></span>

<!-- links -->

[azure-cli-2]: /azure/install-azure-cli
[azbb-repo]: https://github.com/mspnp/template-building-blocks
[azbb-wiki]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/data/enterprise_bi_sqldw
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[ref-arch-repo-folder]: https://github.com/mspnp/reference-architectures/tree/master/data/enterprise_bi_sqldw
[wwi]: /sql/sample/world-wide-importers/wide-world-importers-oltp-database

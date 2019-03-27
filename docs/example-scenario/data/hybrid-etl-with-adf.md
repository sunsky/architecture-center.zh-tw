---
title: 使用現有內部部署 SSIS 和 Azure Data Factory 的混合式 ETL
titleSuffix: Azure Example Scenarios
description: 使用現有內部部署 SQL Server Integration Services (SSIS) 部署和 Azure Data Factory 的混合式 ETL。
author: alhieng
ms.date: 09/20/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: tsp-team
social_image_url: /azure/architecture/example-scenario/data/media/architecture-diagram-hybrid-etl-with-adf.png
ms.openlocfilehash: 354b8ee14f82631842902da3de852f777b1954cc
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58248493"
---
# <a name="hybrid-etl-with-existing-on-premises-ssis-and-azure-data-factory"></a><span data-ttu-id="2f027-103">使用現有內部部署 SSIS 和 Azure Data Factory 的混合式 ETL</span><span class="sxs-lookup"><span data-stu-id="2f027-103">Hybrid ETL with existing on-premises SSIS and Azure Data Factory</span></span>

<span data-ttu-id="2f027-104">將 SQL Server 資料庫移轉至雲端的組織，可獲得極可觀的成本樽節效益、效能提升、彈性增長和更大的延展性。</span><span class="sxs-lookup"><span data-stu-id="2f027-104">Organizations that migrate their SQL Server databases to the cloud can realize tremendous cost savings, performance gains, added flexibility, and greater scalability.</span></span> <span data-ttu-id="2f027-105">不過，要修改以 SQL Server Integration Services (SSIS) 建置的現有擷取、轉換和載入 (ETL) 程序，可能是移轉過程中的一項難題。</span><span class="sxs-lookup"><span data-stu-id="2f027-105">However, reworking existing extract, transform, and load (ETL) processes built with SQL Server Integration Services (SSIS) can be a migration roadblock.</span></span> <span data-ttu-id="2f027-106">在其他情況下，資料載入程序需要複雜的邏輯和/或特定的資料工具元件，但 Azure Data Factory v2 尚未加以支援。</span><span class="sxs-lookup"><span data-stu-id="2f027-106">In other cases, the data load process requires complex logic and/or specific data tool components that are not yet supported by Azure Data Factory v2.</span></span> <span data-ttu-id="2f027-107">常用的 SSIS 功能包括模糊查閱和模糊群組轉換、異動資料擷取 (CDC)、緩時變維度 (SCD) 和 Data Quality Services (DQS)。</span><span class="sxs-lookup"><span data-stu-id="2f027-107">Commonly used SSIS capabilities include Fuzzy Lookup and Fuzzy Grouping transformations, Change Data Capture (CDC), Slowly Changing Dimensions (SCD), and Data Quality Services (DQS).</span></span>

<span data-ttu-id="2f027-108">若要讓現有 SQL 資料庫的隨即移轉更順暢地執行，混合式 ETL 方法可能是最適合的選項。</span><span class="sxs-lookup"><span data-stu-id="2f027-108">To facilitate a lift-and-shift migration of an existing SQL database, a hybrid ETL approach may be the most suitable option.</span></span> <span data-ttu-id="2f027-109">混合式方法以 Data Factory 作為主要的協調流程引擎，但也繼續運用現有的 SSIS 套件來清除資料和使用內部部署資源。</span><span class="sxs-lookup"><span data-stu-id="2f027-109">A hybrid approach uses Data Factory as the primary orchestration engine, but continues to leverage existing SSIS packages to clean data and work with on-premises resources.</span></span> <span data-ttu-id="2f027-110">此方法會使用 Data Factory SQL Server 整合執行階段 (IR) 來啟用將現有的資料庫隨即轉移到雲端的功能，同時也使用現有的程式碼和 SSIS 套件。</span><span class="sxs-lookup"><span data-stu-id="2f027-110">This approach uses the Data Factory SQL Server Integrated Runtime (IR) to enable a lift-and-shift of existing databases into the cloud, while using existing code and SSIS packages.</span></span>

<span data-ttu-id="2f027-111">此範例案例適用於要將資料庫移至雲端，並考慮使用 Data Factory 作為其主要雲端 ETL 引擎，同時要將現有的 SSIS 套件整合至其新雲端資料工作流程的組織。</span><span class="sxs-lookup"><span data-stu-id="2f027-111">This example scenario is relevant to organizations that are moving databases to the cloud and are considering using Data Factory as their primary cloud-based ETL engine while incorporating existing SSIS packages into their new cloud data workflow.</span></span> <span data-ttu-id="2f027-112">許多組織都已投注相當多的資源來開發特定資料工作的 SSIS ETL 套件。</span><span class="sxs-lookup"><span data-stu-id="2f027-112">Many organizations have significant invested in developing SSIS ETL packages for specific data tasks.</span></span> <span data-ttu-id="2f027-113">重寫這些套件是令人怯步的工作。</span><span class="sxs-lookup"><span data-stu-id="2f027-113">Rewriting these packages can be daunting.</span></span> <span data-ttu-id="2f027-114">此外，許多現有的程式碼套件也都須依存於本機資源，而導致無法移轉至雲端。</span><span class="sxs-lookup"><span data-stu-id="2f027-114">Also, many existing code packages have dependencies on local resources, preventing migration to the cloud.</span></span>

<span data-ttu-id="2f027-115">Data Factory 可讓客戶充分利用其現有的 ETL 套件，同時將進一步的投資集中在內部部署 ETL 的開發上。</span><span class="sxs-lookup"><span data-stu-id="2f027-115">Data Factory lets customers take advantage of their existing ETL packages while limiting further investment in on-premises ETL development.</span></span> <span data-ttu-id="2f027-116">此範例將討論使用 Azure Data Factory v2 將現有的 SSIS 套件納入新的雲端資料工作流程中的潛在使用案例。</span><span class="sxs-lookup"><span data-stu-id="2f027-116">This example discusses potential use cases for leveraging existing SSIS packages as part of a new cloud data workflow using Azure Data Factory v2.</span></span>

## <a name="potential-use-cases"></a><span data-ttu-id="2f027-117">潛在使用案例</span><span class="sxs-lookup"><span data-stu-id="2f027-117">Potential use cases</span></span>

<span data-ttu-id="2f027-118">過去，許多 SQL Server 資料專業人員都會選擇 SSIS 這項 ETL 工具來進行資料轉換和載入。</span><span class="sxs-lookup"><span data-stu-id="2f027-118">Traditionally, SSIS has been the ETL tool of choice for many SQL Server data professionals for data transformation and loading.</span></span> <span data-ttu-id="2f027-119">他們有時也會使用特定的 SSIS 功能或第三方外掛元件來加速執行開發工作。</span><span class="sxs-lookup"><span data-stu-id="2f027-119">Sometimes, specific SSIS features or third-party plugging components have been used to accelerate the development effort.</span></span> <span data-ttu-id="2f027-120">替換或重開發新這些套件可能不在其考慮之列，而客戶也因此無法將資料庫移轉至雲端。</span><span class="sxs-lookup"><span data-stu-id="2f027-120">Replacement or redevelopment of these packages may not be an option, which prevents customers from migrating their databases to the cloud.</span></span> <span data-ttu-id="2f027-121">客戶期望能以影響較小的方法將現有的資料庫移轉至雲端，同時能充分利用其現有的 SSIS 套件。</span><span class="sxs-lookup"><span data-stu-id="2f027-121">Customers are looking for low impact approaches to migrating their existing databases to the cloud and taking advantage of their existing SSIS packages.</span></span>

<span data-ttu-id="2f027-122">以下列出數個潛在的內部部署使用案例：</span><span class="sxs-lookup"><span data-stu-id="2f027-122">Several potential on-premises use cases are listed below:</span></span>

- <span data-ttu-id="2f027-123">將網路路由器記錄載入至資料庫以進行分析。</span><span class="sxs-lookup"><span data-stu-id="2f027-123">Loading network router logs to a database for analysis.</span></span>
- <span data-ttu-id="2f027-124">準備人力資源雇用資料以提供分析報告。</span><span class="sxs-lookup"><span data-stu-id="2f027-124">Preparing human resources employment data for analytical reporting.</span></span>
- <span data-ttu-id="2f027-125">將產品和銷售資料載入資料倉儲中以進行銷售預測。</span><span class="sxs-lookup"><span data-stu-id="2f027-125">Loading product and sales data into a data warehouse for sales forecasting.</span></span>
- <span data-ttu-id="2f027-126">自動載入至營運資料存放區或資料倉儲以供財會之用。</span><span class="sxs-lookup"><span data-stu-id="2f027-126">Automating loading of operational data stores or data warehouses for finance and accounting.</span></span>

## <a name="architecture"></a><span data-ttu-id="2f027-127">架構</span><span class="sxs-lookup"><span data-stu-id="2f027-127">Architecture</span></span>

![概述使用 Azure Data Factory 的混合式 ETL 程序架構][architecture-diagram]

1. <span data-ttu-id="2f027-129">資料從 Blob 儲存體載入 Data Factory 中。</span><span class="sxs-lookup"><span data-stu-id="2f027-129">Data is sourced from Blob storage into Data Factory.</span></span>
2. <span data-ttu-id="2f027-130">Data Factory 管線叫用預存程序，以透過整合執行階段執行裝載於內部部署的 SSIS 作業。</span><span class="sxs-lookup"><span data-stu-id="2f027-130">The Data Factory pipeline invokes a stored procedure to execute an SSIS job hosted on-premises via the Integrated Runtime.</span></span>
3. <span data-ttu-id="2f027-131">執行資料清理作業以備妥供下游取用的資料。</span><span class="sxs-lookup"><span data-stu-id="2f027-131">The data cleansing jobs are executed to prepare the data for downstream consumption.</span></span>
4. <span data-ttu-id="2f027-132">資料清理工作順利完成後，即會執行複製工作，將乾淨資料載入 Azure 中。</span><span class="sxs-lookup"><span data-stu-id="2f027-132">Once the data cleansing task completes successfully, a copy task is executed to load the clean data into Azure.</span></span>
5. <span data-ttu-id="2f027-133">乾淨資料隨後會載入至 SQL 資料倉儲中的資料表。</span><span class="sxs-lookup"><span data-stu-id="2f027-133">The clean data is then loaded into tables in the SQL Data Warehouse.</span></span>

### <a name="components"></a><span data-ttu-id="2f027-134">元件</span><span class="sxs-lookup"><span data-stu-id="2f027-134">Components</span></span>

- <span data-ttu-id="2f027-135">[Blob 儲存體][docs-blob-storage]可用來儲存檔案，並可作為 Data Factory 擷取資料的來源。</span><span class="sxs-lookup"><span data-stu-id="2f027-135">[Blob storage][docs-blob-storage] is used to store files and as a source for Data Factory to retrieve data.</span></span>
- <span data-ttu-id="2f027-136">[SQL Server Integration Services][docs-ssis] 包含用來執行工作特定工作負載的內部部署 ETL 套件。</span><span class="sxs-lookup"><span data-stu-id="2f027-136">[SQL Server Integration Services][docs-ssis] contains the on-premises ETL packages used to execute task-specific workloads.</span></span>
- <span data-ttu-id="2f027-137">[Azure Data Factory][docs-data-factory] 是雲端協調流程引擎，可從多個來源取得資料，然後將資料結合、協調並載入資料倉儲中。</span><span class="sxs-lookup"><span data-stu-id="2f027-137">[Azure Data Factory][docs-data-factory] is the cloud orchestration engine that takes data from multiple sources and combines, orchestrates, and loads the data into a data warehouse.</span></span>
- <span data-ttu-id="2f027-138">[SQL 資料倉儲][docs-sql-data-warehouse]可集中管理雲端中的資料，以方便使用標準 ANSI SQL 查詢來存取。</span><span class="sxs-lookup"><span data-stu-id="2f027-138">[SQL Data Warehouse][docs-sql-data-warehouse] centralizes data in the cloud for easy access using standard ANSI SQL queries.</span></span>

### <a name="alternatives"></a><span data-ttu-id="2f027-139">替代項目</span><span class="sxs-lookup"><span data-stu-id="2f027-139">Alternatives</span></span>

<span data-ttu-id="2f027-140">Data Factory 可叫用使用其他技術實作的資料清理程序，例如 Databricks Notebook、Python 指令碼，或在虛擬機器中執行的 SSIS 執行個體。</span><span class="sxs-lookup"><span data-stu-id="2f027-140">Data Factory could invoke data cleansing procedures implemented using other technologies, such as a Databricks notebook, Python script, or SSIS instance running in a virtual machine.</span></span> <span data-ttu-id="2f027-141">[安裝 Azure-SSIS 整合執行階段的付費或授權自訂元件](/azure/data-factory/how-to-develop-azure-ssis-ir-licensed-components)可作為混合式方法的替代方法。</span><span class="sxs-lookup"><span data-stu-id="2f027-141">[Installing paid or licensed custom components for the Azure-SSIS integration runtime](/azure/data-factory/how-to-develop-azure-ssis-ir-licensed-components) may be a viable alternative to the hybrid approach.</span></span>

## <a name="considerations"></a><span data-ttu-id="2f027-142">考量</span><span class="sxs-lookup"><span data-stu-id="2f027-142">Considerations</span></span>

<span data-ttu-id="2f027-143">整合執行階段 (IR) 支援兩種模型：自我裝載 IR 或 Azure 裝載 IR。</span><span class="sxs-lookup"><span data-stu-id="2f027-143">The Integrated Runtime (IR) supports two models: self-hosted IR or Azure-hosted IR.</span></span> <span data-ttu-id="2f027-144">首先，您必須從這兩個選項中擇一使用。</span><span class="sxs-lookup"><span data-stu-id="2f027-144">You first must decide between these two options.</span></span> <span data-ttu-id="2f027-145">自我裝載較具成本效益，但在維護及管理方面會產生額外負荷。</span><span class="sxs-lookup"><span data-stu-id="2f027-145">Self-hosting is more cost effective but has more overhead for maintenance and management.</span></span> <span data-ttu-id="2f027-146">如需詳細資訊，請參閱[自我裝載 IR](/azure/data-factory/concepts-integration-runtime#self-hosted-integration-runtime)。</span><span class="sxs-lookup"><span data-stu-id="2f027-146">For more information, see [Self-hosted IR](/azure/data-factory/concepts-integration-runtime#self-hosted-integration-runtime).</span></span> <span data-ttu-id="2f027-147">如果您在判斷所應使用的 IR 時需要協助，請參閱[決定使用哪一個 IR](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)。</span><span class="sxs-lookup"><span data-stu-id="2f027-147">If you need help determining which IR to use, see [Determining which IR to use](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use).</span></span>

<span data-ttu-id="2f027-148">對於 Azure 裝載方法，您應決定需要以多少資源來處理資料。</span><span class="sxs-lookup"><span data-stu-id="2f027-148">For the Azure-hosted approach, you should decide how much power is required to process your data.</span></span> <span data-ttu-id="2f027-149">Azure 裝載組態可讓您在設定步驟中選取 VM 大小。</span><span class="sxs-lookup"><span data-stu-id="2f027-149">The Azure-hosted configuration allows you to select the VM size as part of the configuration steps.</span></span> <span data-ttu-id="2f027-150">若要深入了解如何選取 VM 大小，請參閱 [VM 效能考量](/azure/cloud-services/cloud-services-sizes-specs#performance-considerations)。</span><span class="sxs-lookup"><span data-stu-id="2f027-150">To learn more about selecting VM sizes, see [VM performance considerations](/azure/cloud-services/cloud-services-sizes-specs#performance-considerations).</span></span>

<span data-ttu-id="2f027-151">如果您已有 SSIS 套件具有內部部署相依性 (例如，無法從 Azure 存取的資料來源或檔案)，這項決定就會容易得多。</span><span class="sxs-lookup"><span data-stu-id="2f027-151">The decision is much easier when you already have existing SSIS packages that have on-premises dependencies such as data sources or files that are not accessible from Azure.</span></span> <span data-ttu-id="2f027-152">在此情況下，自我裝載 IR 是您唯一的選項。</span><span class="sxs-lookup"><span data-stu-id="2f027-152">In this scenario, your only option is the self-hosted IR.</span></span> <span data-ttu-id="2f027-153">此方法可讓您有最大的彈性將雲端作為協調流程引擎，而無須重寫現有的套件。</span><span class="sxs-lookup"><span data-stu-id="2f027-153">This approach provides the most flexibility to leverage the cloud as the orchestration engine, without having to rewrite existing packages.</span></span>

<span data-ttu-id="2f027-154">最後，其目的是要將處理過的資料移至雲端以進一步的細分，或與雲端中儲存的其他資料結合。</span><span class="sxs-lookup"><span data-stu-id="2f027-154">Ultimately, the intent is to move the processed data into the cloud for further refinement or combining with other data stored in the cloud.</span></span> <span data-ttu-id="2f027-155">在設計的過程中，請追蹤 Data Factory 管線中使用的活動數目。</span><span class="sxs-lookup"><span data-stu-id="2f027-155">As part of the design process, keep track of the number of activities used in the Data Factory pipelines.</span></span> <span data-ttu-id="2f027-156">如需詳細資訊，請參閱[了解 Azure Data Factory 中的管線和活動](/azure/data-factory/concepts-pipelines-activities)。</span><span class="sxs-lookup"><span data-stu-id="2f027-156">For more information, see [Pipelines and activities in Azure Data Factory](/azure/data-factory/concepts-pipelines-activities).</span></span>

## <a name="pricing"></a><span data-ttu-id="2f027-157">價格</span><span class="sxs-lookup"><span data-stu-id="2f027-157">Pricing</span></span>

<span data-ttu-id="2f027-158">Data Factory 可讓您以符合成本效益的方式協調雲端中的資料移動。</span><span class="sxs-lookup"><span data-stu-id="2f027-158">Data Factory is a cost-effective way to orchestrate data movement in the cloud.</span></span> <span data-ttu-id="2f027-159">成本取決於若干因素。</span><span class="sxs-lookup"><span data-stu-id="2f027-159">The cost is based on the several factors.</span></span>

- <span data-ttu-id="2f027-160">管線執行數目</span><span class="sxs-lookup"><span data-stu-id="2f027-160">Number of pipeline executions</span></span>
- <span data-ttu-id="2f027-161">管線內使用的實體/活動數目</span><span class="sxs-lookup"><span data-stu-id="2f027-161">Number of entities/activities used within the pipeline</span></span>
- <span data-ttu-id="2f027-162">監視作業數目</span><span class="sxs-lookup"><span data-stu-id="2f027-162">Number of monitoring operations</span></span>
- <span data-ttu-id="2f027-163">整合執行 (Azure 裝載 IR 或自我裝載 IR) 的數目</span><span class="sxs-lookup"><span data-stu-id="2f027-163">Number of Integration Runs (Azure-hosted IR or self-hosted IR)</span></span>

<span data-ttu-id="2f027-164">Data Factory 的計費以耗用量為基礎。</span><span class="sxs-lookup"><span data-stu-id="2f027-164">Data Factory uses consumption-based billing.</span></span> <span data-ttu-id="2f027-165">因此，只有在管線執行和監視期間才會產生成本。</span><span class="sxs-lookup"><span data-stu-id="2f027-165">Therefore, cost is only incurred during pipeline executions and monitoring.</span></span> <span data-ttu-id="2f027-166">基本管線執行的成本最低只需 50 美分，監視則只需 25 美分。</span><span class="sxs-lookup"><span data-stu-id="2f027-166">The execution of a basic pipeline would cost as little as 50 cents and the monitoring as little as 25 cents.</span></span> <span data-ttu-id="2f027-167">[Azure 成本計算機](https://azure.microsoft.com/pricing/calculator/)可讓您根據本身的工作負載產生更精確的估計。</span><span class="sxs-lookup"><span data-stu-id="2f027-167">The [Azure cost calculator](https://azure.microsoft.com/pricing/calculator/) can be used to create a more accurate estimate based on your specific workload.</span></span>

<span data-ttu-id="2f027-168">執行混合式 ETL 工作負載時，您必須考量用來裝載 SSIS 套件的虛擬機器成本。</span><span class="sxs-lookup"><span data-stu-id="2f027-168">When running a hybrid ETL workload, you must factor in the cost of the virtual machine used to host your SSIS packages.</span></span> <span data-ttu-id="2f027-169">這項成本以 VM 的大小為準，其範圍介於 D1v2 (1 個核心，3.5 GB 的 RAM，50 GB 的磁碟) 到 E64V3 (64 個核心、 432 GB 的 RAM，1600 GB 的磁碟) 之間。</span><span class="sxs-lookup"><span data-stu-id="2f027-169">This cost is based on the size of the VM ranging from a D1v2 (1 core, 3.5 GB RAM, 50 GB Disk) to E64V3 (64 cores, 432 GB RAM, 1600 GB disk).</span></span> <span data-ttu-id="2f027-170">如果您需要選取適當 VM 大小的詳細指引，請參閱 [VM 效能考量](/azure/cloud-services/cloud-services-sizes-specs#performance-considerations)。</span><span class="sxs-lookup"><span data-stu-id="2f027-170">If you need further guidance on selection the appropriate VM size, see [VM performance considerations](/azure/cloud-services/cloud-services-sizes-specs#performance-considerations).</span></span>

## <a name="next-steps"></a><span data-ttu-id="2f027-171">後續步驟</span><span class="sxs-lookup"><span data-stu-id="2f027-171">Next Steps</span></span>

- <span data-ttu-id="2f027-172">深入了解 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)。</span><span class="sxs-lookup"><span data-stu-id="2f027-172">Learn more about [Azure Data Factory](https://azure.microsoft.com/services/data-factory/).</span></span>
- <span data-ttu-id="2f027-173">依照[逐步教學課程](/azure/data-factory/#step-by-step-tutorials)開始使用 Azure Data Factory。</span><span class="sxs-lookup"><span data-stu-id="2f027-173">Get started with Azure Data Factory by following the [Step-by-step tutorial](/azure/data-factory/#step-by-step-tutorials).</span></span>
- <span data-ttu-id="2f027-174">[在 Azure Data Factory 中佈建 Azure-SSIS 整合執行階段](/azure/data-factory/tutorial-deploy-ssis-packages-azure)。</span><span class="sxs-lookup"><span data-stu-id="2f027-174">[Provision the Azure-SSIS Integration Runtime in Azure Data Factory](/azure/data-factory/tutorial-deploy-ssis-packages-azure).</span></span>

<!-- links -->
[architecture-diagram]: ./media/architecture-diagram-hybrid-etl-with-adf.png
[small-pricing]: https://azure.com/e/
[medium-pricing]: https://azure.com/e/
[large-pricing]: https://azure.com/e/
[availability]: /azure/architecture/checklist/availability
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[docs-blob-storage]: /azure/storage/blobs/
[docs-data-factory]: /azure/data-factory/introduction
[docs-resource-groups]: /azure/azure-resource-manager/resource-group-overview
[docs-ssis]: /sql/integration-services/sql-server-integration-services
[docs-sql-data-warehouse]: /azure/sql-data-warehouse/sql-data-warehouse-overview-what-is

---
title: 適用於銷售和行銷的資料倉儲和分析
titleSuffix: Azure Example Scenarios
description: 合併多個來源的資料，並最佳化資料分析。
author: alexbuckgit
ms.date: 09/15/2018
ms.openlocfilehash: 5727b6ab475224541e272c6da6243cabe851b919
ms.sourcegitcommit: bb7fcffbb41e2c26a26f8781df32825eb60df70c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2018
ms.locfileid: "53643982"
---
# <a name="data-warehousing-and-analytics-for-sales-and-marketing"></a>適用於銷售和行銷的資料倉儲和分析

此範例案例示範的資料管線可將多個來源的大量資料整合成 Azure 中的統一分析平台。 此特定案例是以銷售和行銷解決方案為基礎，但是對於許多需要進階分析大型資料集的產業 (例如電子商務、零售和醫療保健) 而言，設計模式關係重大。

此範例示範銷售和行銷公司如何建立獎勵方案。 這些方案可獎勵客戶、供應商、銷售人員和員工。 資料是這些方案的基礎，而公司想要使用 Azure 來改善透過資料分析所取得的見解。

公司需要新式的資料分析方法，才能在適當的時間使用正確的資料進行決策。 公司的目標包括：

- 將不同種類的資料來源組合為雲端規模的平台。
- 將來源資料轉換成常見的分類法和結構，讓資料變得一致並可輕鬆比較。
- 使用可支援數千個獎勵方案的高度平行化方法載入資料，而沒有部署和維護內部部署基礎結構的高額成本。
- 大幅減少收集和轉換資料所需的時間，讓您可以專心分析資料。

## <a name="relevant-use-cases"></a>相關使用案例

這個方法也可用來：

- 建立資料倉儲，以成為您資料的單一真相來源。
- 整合關聯式資料來源與其他非結構化資料集。
- 使用語意模型和強大的視覺效果工具進行更簡單的資料分析。

## <a name="architecture"></a>架構

![Azure 中資料倉儲和分析案例的架構][architecture]

整個解決方案的資料流程如下所示：

1. 對於每個資料來源而言，所有更新都會定期匯出到 Azure Blob 儲存體的暫存區域中。
2. Data Factory 會以累加方式將資料從 Blob 儲存體載入到 SQL 資料倉儲的暫存資料表中。 在此程序期間，系統會清理並轉換資料。 Polybase 可以平行處理大型資料集的程序。
3. 將新的資料批次載入倉儲之後，系統會重新整理先前建立的 Analysis Services 表格式模型。 這個語意模型簡化了商務資料和關聯性的分析。
4. 商務分析師可使用 Microsoft Power BI，透過 Analysis Services 語意模型來分析資料倉儲資料。

### <a name="components"></a>元件

公司有許多不同平台上的資料來源：

- 內部部署的 SQL Server
- 內部部署的 Orace
- 連接字串
- Azure 表格儲存體
- Cosmos DB

使用數個 Azure 元件可從下列不同的資料來源載入資料：

- 在資料載入至 SQL 資料倉儲之前，[Blob 儲存體](/azure/storage/blobs/storage-blobs-introduction)用來暫存來源資料。
- [Data Factory](/azure/data-factory) 可協調將暫存資料轉換成 SQL 資料倉儲中一般結構的作業。 Data Factory [在將資料載入 SQL 資料倉儲時會使用 Polybase](/azure/data-factory/connector-azure-sql-data-warehouse#use-polybase-to-load-data-into-azure-sql-data-warehouse)，讓輸送量最大化。
- [SQL 資料倉儲](/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)是一套分散式系統，用於儲存和分析大型資料集。 它會使用大量平行處理 (MPP)，因而適合用來執行高效能分析。 SQL 資料倉儲可以使用 [PolyBase](/sql/relational-databases/polybase/polybase-guide) 快速從 Blob 儲存體載入資料。
- [Analysis Services](/azure/analysis-services) 可提供您資料的語意模型。 也可以在分析資料時提升系統效能。
- [Power BI](/power-bi) 是一套商務分析工具，用來分析資料及分享見解。 Power BI 可以查詢 Analysis Services 中儲存的語意模型，也可以直接查詢 SQL 資料倉儲。
- [Azure Active Directory](/azure/active-directory) (Azure AD) 會驗證透過 Power BI 連線至 Analysis Services 伺服器的使用者。 Data Factory 也可以使用 Azure AD，透過服務主體或 [Azure 資源受控識別](/azure/active-directory/managed-identities-azure-resources/overview)來驗證 SQL 資料倉儲。

### <a name="alternatives"></a>替代項目

- 此範例管線包含好幾種不同的資料來源。 此架構可以處理各種不同的關聯式和非關聯式資料來源。
- Data Factory 會協調資料管線的工作流程。 如果您想要只載入資料一次或隨需載入，則可使用 SQL Server 大量複製 (bcp) 和 AzCopy 等工具，將資料複製到 Blob 儲存體中。 接著可以使用 PolyBase 將資料直接載入 SQL 資料倉儲中。
- 如果您有非常大型的資料集，請考慮使用 [Data Lake 儲存體](/azure/storage/data-lake-storage/introduction)，以提供無限的分析資料儲存空間。
- 內部部署 [SQL Server 平行處理資料倉儲](/sql/analytics-platform-system)設備也可以用於巨量資料處理。 不過，使用 SQL 資料倉儲等受控雲端式解決方案的作業成本通常低很多。
- SQL 資料倉儲不適用於小於 250GB 的 OLTP 工作負載或資料集。 在這些案例中，您應該使用 Azure SQL Database 或 SQL Server。
- 如需其他替代項目的比較，請參閱：

  - [在 Azure 中選擇資料管線協調流程技術](/azure/architecture/data-guide/technology-choices/pipeline-orchestration-data-movement)
  - [在 Azure 中選擇批次處理技術](/azure/architecture/data-guide/technology-choices/batch-processing)
  - [在 Azure 中選擇分析資料存放區](/azure/architecture/data-guide/technology-choices/analytical-data-stores)
  - [在 Azure 中選擇資料分析技術](/azure/architecture/data-guide/technology-choices/analysis-visualizations-reporting)

## <a name="considerations"></a>考量

選擇此架構中的技術是因為它們符合了公司的延展性和可用性需求，同時協助他們控制成本。

- SQL 資料倉儲的[大量平行處理架構](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)可提供延展性和高效能。
- SQL 資料倉儲具有[保證的 SLA](https://azure.microsoft.com/support/legal/sla/sql-data-warehouse) 以及[達到高可用性的建議做法](/azure/sql-data-warehouse/sql-data-warehouse-best-practices)。
- 當分析活動變少時，公司可以[隨需調整 SQL 資料倉儲](/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview)、減少或甚至暫停計算，以降低成本。
- Azure Analysis Services 可以[相應放大](/azure/analysis-services/analysis-services-scale-out)，以降低在高度查詢工作負載期間的回應時間。 您也可以將處理作業從查詢集區中區隔出來，處理作業才不會讓用戶端查詢變慢。
- Azure Analysis Services 也有[保證的 SLA](https://azure.microsoft.com/support/legal/sla/analysis-services) 以及[達到高可用性的建議做法](/azure/analysis-services/analysis-services-bcdr)。
- [SQL 資料倉儲安全性模型](/azure/sql-data-warehouse/sql-data-warehouse-overview-manage-security)可透過 Azure AD 或 SQL Server 驗證和加密，提供連線安全性、[驗證和授權](/azure/sql-data-warehouse/sql-data-warehouse-authentication)。 [Azure Analysis Services](/azure/analysis-services/analysis-services-manage-users) 會使用 Azure AD 進行身分識別管理和使用者驗證。

## <a name="pricing"></a>價格

透過 Azure 定價計算機，檢閱[資料倉儲案例的定價範例][calculator]。 調整一些值，以查看需求對於成本有何影響。

- [SQL 資料倉儲](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2)可讓您獨立調整計算和儲存體層級。 計算資源需每小時付費，而您可以依照需求調整或暫停這些資源。 儲存體資源會依照 TB 計費，因此成本會隨著您內嵌更多資料而增加。
- [Data Factory](https://azure.microsoft.com/pricing/details/data-factory) 成本是以讀取/寫入作業數目、監視作業數目，以及在工作負載中執行的協調流程活動數目為基礎。 Data Factory 成本會隨著每個額外的資料流和每個資料流所處理的資料量增加。
- [Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services) 會以開發人員、基本及標準層提供。 執行個體的定價是以查詢處理單位 (QPU) 和可用的記憶體為基礎。 為了保持較低的成本，請將您執行的查詢數目、其所處理的資料數量，以及其執行頻率降至最低。
- [Power BI](https://powerbi.microsoft.com/pricing) 有不同的產品選項可滿足不同的需求。 [Power BI Embedded](https://azure.microsoft.com/pricing/details/power-bi-embedded) 針對您的應用程式內嵌的 Power BI 功能提供一個 Azure 型選項。 Power BI Embedded 執行個體包含在上面的定價範例中。

## <a name="next-steps"></a>後續步驟

- 檢閱 [自動化企業 BI 的 Azure 參考架構](/azure/architecture/reference-architectures/data/enterprise-bi-adf)，其中包括在 Azure 中部署這個架構執行個體的指示。
- 閱讀 [Maritz Motivation Solutions 客戶案例][source-document]。 該案例說明用來管理客戶資料的類似方法。
- 在 [Azure 資料架構指南](/azure/architecture/data-guide)中尋找有關資料管線、資料倉儲、線上分析處理 (OLAP) 和巨量資料的完整架構指引。

<!-- links -->
[source-document]: https://customers.microsoft.com/story/maritz
[calculator]: https://azure.com/e/b798fb70c53e4dd19fdeacea4db78276
[architecture]: ./media/architecture-data-warehouse.png

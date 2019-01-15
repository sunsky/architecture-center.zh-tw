---
title: 使用現有內部部署 SSIS 和 Azure Data Factory 的混合式 ETL
titleSuffix: Azure Example Scenarios
description: 使用現有內部部署 SQL Server Integration Services (SSIS) 部署和 Azure Data Factory 的混合式 ETL。
author: alhieng
ms.date: 09/20/2018
ms.custom: tsp-team
ms.openlocfilehash: a2ca3817ed172e6d2332a92f68970ea2a5ad8f6c
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54110385"
---
# <a name="hybrid-etl-with-existing-on-premises-ssis-and-azure-data-factory"></a>使用現有內部部署 SSIS 和 Azure Data Factory 的混合式 ETL

將 SQL Server 資料庫移轉至雲端的組織，可獲得極可觀的成本樽節效益、效能提升、彈性增長和更大的延展性。 不過，要修改以 SQL Server Integration Services (SSIS) 建置的現有擷取、轉換和載入 (ETL) 程序，可能是移轉過程中的一項難題。 在其他情況下，資料載入程序需要複雜的邏輯和/或特定的資料工具元件，但 Azure Data Factory v2 尚未加以支援。 常用的 SSIS 功能包括模糊查閱和模糊群組轉換、異動資料擷取 (CDC)、緩時變維度 (SCD) 和 Data Quality Services (DQS)。

若要讓現有 SQL 資料庫的隨即移轉更順暢地執行，混合式 ETL 方法可能是最適合的選項。 混合式方法以 Data Factory 作為主要的協調流程引擎，但也繼續運用現有的 SSIS 套件來清除資料和使用內部部署資源。 此方法會使用 Data Factory SQL Server 整合執行階段 (IR) 來啟用將現有的資料庫隨即轉移到雲端的功能，同時也使用現有的程式碼和 SSIS 套件。

此範例案例適用於要將資料庫移至雲端，並考慮使用 Data Factory 作為其主要雲端 ETL 引擎，同時要將現有的 SSIS 套件整合至其新雲端資料工作流程的組織。 許多組織都已投注相當多的資源來開發特定資料工作的 SSIS ETL 套件。 重寫這些套件是令人怯步的工作。 此外，許多現有的程式碼套件也都須依存於本機資源，而導致無法移轉至雲端。

Data Factory 可讓客戶充分利用其現有的 ETL 套件，同時將進一步的投資集中在內部部署 ETL 的開發上。 此範例將討論使用 Azure Data Factory v2 將現有的 SSIS 套件納入新的雲端資料工作流程中的潛在使用案例。

## <a name="potential-use-cases"></a>潛在使用案例

過去，許多 SQL Server 資料專業人員都會選擇 SSIS 這項 ETL 工具來進行資料轉換和載入。 他們有時也會使用特定的 SSIS 功能或第三方外掛元件來加速執行開發工作。 替換或重開發新這些套件可能不在其考慮之列，而客戶也因此無法將資料庫移轉至雲端。 客戶期望能以影響較小的方法將現有的資料庫移轉至雲端，同時能充分利用其現有的 SSIS 套件。

以下列出數個潛在的內部部署使用案例：

- 將網路路由器記錄載入至資料庫以進行分析。
- 準備人力資源雇用資料以提供分析報告。
- 將產品和銷售資料載入資料倉儲中以進行銷售預測。
- 自動載入至營運資料存放區或資料倉儲以供財會之用。

## <a name="architecture"></a>架構

![概述使用 Azure Data Factory 的混合式 ETL 程序架構][architecture-diagram]

1. 資料從 Blob 儲存體載入 Data Factory 中。
2. Data Factory 管線叫用預存程序，以透過整合執行階段執行裝載於內部部署的 SSIS 作業。
3. 執行資料清理作業以備妥供下游取用的資料。
4. 資料清理工作順利完成後，即會執行複製工作，將乾淨資料載入 Azure 中。
5. 乾淨資料隨後會載入至 SQL 資料倉儲中的資料表。

### <a name="components"></a>元件

- [Blob 儲存體][docs-blob-storage]可用來儲存檔案，並可作為 Data Factory 擷取資料的來源。
- [SQL Server Integration Services][docs-ssis] 包含用來執行工作特定工作負載的內部部署 ETL 套件。
- [Azure Data Factory][docs-data-factory] 是雲端協調流程引擎，可從多個來源取得資料，然後將資料結合、協調並載入資料倉儲中。
- [SQL 資料倉儲][docs-sql-data-warehouse]可集中管理雲端中的資料，以方便使用標準 ANSI SQL 查詢來存取。

### <a name="alternatives"></a>替代項目

Data Factory 可叫用使用其他技術實作的資料清理程序，例如 Databricks Notebook、Python 指令碼，或在虛擬機器中執行的 SSIS 執行個體。 [安裝 Azure-SSIS 整合執行階段的付費或授權自訂元件](/azure/data-factory/how-to-develop-azure-ssis-ir-licensed-components)可作為混合式方法的替代方法。

## <a name="considerations"></a>考量

整合執行階段 (IR) 支援兩種模型：自我裝載 IR 或 Azure 裝載 IR。 首先，您必須從這兩個選項中擇一使用。 自我裝載較具成本效益，但在維護及管理方面會產生額外負荷。 如需詳細資訊，請參閱[自我裝載 IR](/azure/data-factory/concepts-integration-runtime#self-hosted-integration-runtime)。 如果您在判斷所應使用的 IR 時需要協助，請參閱[決定使用哪一個 IR](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)。

對於 Azure 裝載方法，您應決定需要以多少資源來處理資料。 Azure 裝載組態可讓您在設定步驟中選取 VM 大小。 若要深入了解如何選取 VM 大小，請參閱 [VM 效能考量](/azure/cloud-services/cloud-services-sizes-specs#performance-considerations)。

如果您已有 SSIS 套件具有內部部署相依性 (例如，無法從 Azure 存取的資料來源或檔案)，這項決定就會容易得多。 在此情況下，自我裝載 IR 是您唯一的選項。 此方法可讓您有最大的彈性將雲端作為協調流程引擎，而無須重寫現有的套件。

最後，其目的是要將處理過的資料移至雲端以進一步的細分，或與雲端中儲存的其他資料結合。 在設計的過程中，請追蹤 Data Factory 管線中使用的活動數目。 如需詳細資訊，請參閱[了解 Azure Data Factory 中的管線和活動](/azure/data-factory/concepts-pipelines-activities)。

## <a name="pricing"></a>價格

Data Factory 可讓您以符合成本效益的方式協調雲端中的資料移動。 成本取決於若干因素。

- 管線執行數目
- 管線內使用的實體/活動數目
- 監視作業數目
- 整合執行 (Azure 裝載 IR 或自我裝載 IR) 的數目

Data Factory 的計費以耗用量為基礎。 因此，只有在管線執行和監視期間才會產生成本。 基本管線執行的成本最低只需 50 美分，監視則只需 25 美分。 [Azure 成本計算機](https://azure.microsoft.com/pricing/calculator/)可讓您根據本身的工作負載產生更精確的估計。

執行混合式 ETL 工作負載時，您必須考量用來裝載 SSIS 套件的虛擬機器成本。 這項成本以 VM 的大小為準，其範圍介於 D1v2 (1 個核心，3.5 GB 的 RAM，50 GB 的磁碟) 到 E64V3 (64 個核心、 432 GB 的 RAM，1600 GB 的磁碟) 之間。 如果您需要選取適當 VM 大小的詳細指引，請參閱 [VM 效能考量](/azure/cloud-services/cloud-services-sizes-specs#performance-considerations)。

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)。
- 依照[逐步教學課程](/azure/data-factory/#step-by-step-tutorials)開始使用 Azure Data Factory。
- [在 Azure Data Factory 中佈建 Azure-SSIS 整合執行階段](/azure/data-factory/tutorial-deploy-ssis-packages-azure)。

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

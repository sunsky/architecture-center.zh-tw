---
title: "選擇 OLAP 資料存放區"
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: f3041b95696c9408a2c9ab747fe1ec3041db0743
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2018
---
# <a name="choosing-an-olap-data-store-in-azure"></a>在 Azure 中選擇 OLAP 資料存放區

線上分析處理 (OLAP) 是一種可以組織大型企業資料庫和支援複雜分析的技術。 本主題會比較 Azure 中各種 OLAP 解決方案的選項。

> [!NOTE]
> 如需 OLAP 資料存放區使用時機的詳細資訊，請參閱[線上分析處理](../scenarios/online-analytical-processing.md)。

## <a name="what-are-your-options-when-choosing-an-olap-data-store"></a>您在選擇 OLAP 資料存放區時有哪些選項可用？

在 Azure 中，下列所有資料存放區均符合 OLAP 的核心需求：

- [包含資料行存放區索引的 SQL Server](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)
- [Azure Analysis Services](/azure/analysis-services/analysis-services-overview)
- [SQL Server Analysis Services (SSAS)](/sql/analysis-services/analysis-services)

SQL Server Analysis Services (SSAS) 可提供適用於商業智慧應用程式的 OLAP 和資料採礦功能。 您可以將 SSAS 安裝在本機伺服器上，也可以將其裝載在 Azure 的虛擬機器中。 Azure Analysis Services 是完全受控的服務，可提供與 SSAS 相同的主要功能。 Azure Analysis Services 支援連線到貴組織之雲端和內部部署環境的[各種資料來源](/azure/analysis-services/analysis-services-datasource)。

叢集化的資料行存放區索引可在 SQL Server 2014 和更新版本以及 Azure SQL Database 中取得，而且適用於 OLAP 工作負載。 不過，從 SQL Server 2016 (包括 Azure SQL Database) 開始，您已可透過使用可更新的非叢集化資料行存放區索引來利用混合式交易式/分析處理 (HTAP)。 HTAP 可讓您在相同平台上執行 OLTP 與 OLAP 處理，讓您不必儲存多個資料複本，也不需要有不同的 OLTP 與 OLAP 系統。 如需詳細資訊，請參閱[開始使用資料行存放區來進行即時作業分析](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)。

## <a name="key-selection-criteria"></a>重要選取準則

若要縮小選項範圍，請從回答下列問題來開始：

- 您是否想擁有受控服務，而不是自行管理伺服器？

- 您是否需要使用 Azure Active Directory (Azure AD) 的安全驗證？

- 您是否要進行即時分析？ 如果是，請將您的選擇範圍縮小至支援即時分析的選項。 

    此內容中的「即時分析」適用於會同時執行操作和分析工作負載的單一資料來源 (例如企業資源規劃 (ERP) 應用程式)。 如果您需要整合多個來源的資料，或是需要使用預先彙總的資料 (如 Cube) 來獲得極致的分析效能，您可能還是需要個別的資料倉儲。

- 您是否需要使用預先彙總的資料，例如，為了提供語意模型從而讓商務使用者更容易進行分析？ 如果是，請選擇支援多維度 Cube 或表格式語意模型的選項。 

    提供彙總可協助使用者以一致的方式計算資料彙總。 預先彙總的資料在處理跨多個資料列的數個資料行時，也可以提供大幅提升的效能。 資料可在多維度 Cube 或表格式語意模型中預先彙總。

- 除了您的 OLTP 資料存放區，您是否需要整合多個來源的資料？ 如果是，請考慮可輕鬆整合多個資料來源的選項。

## <a name="capability-matrix"></a>功能對照表

下表摘要列出各項功能的主要差異。

### <a name="general-capabilities"></a>一般功能

| | Azure Analysis Services | SQL Server Analysis Services | 包含資料行存放區索引的 SQL Server | 包含資料行存放區索引的 Azure SQL Database |
| --- | --- | --- | --- | --- |
| 屬於受控服務 | yes | 否 | 否 | yes |
| 支援多維度 Cube | 否 | yes | 否 | 否 |
| 支援表格式語意模型 | yes | yes | 否 | 否 |
| 可輕鬆整合多個資料來源 | yes | yes | 否 <sup>1</sup> | 否 <sup>1</sup> |
| 支援即時分析 | 否 | 否 | yes | yes |
| 需要從來源複製資料的程序 | yes | yes | 否 | 否 |
| Azure AD 整合 | yes | 否 | 否 <sup>2</sup> | yes |

[1] SQL Server 和 Azure SQL Database 雖無法作為查詢來源並整合多個外部資料來源，但您仍可使用 [SSIS](/sql/integration-services/sql-server-integration-services) 或 [Azure Data Factory](/azure/data-factory/) 建置管線來為您執行此工作。 裝載於 Azure 虛擬機器的 SQL Server 有其他選項，例如連結的伺服器和 [PolyBase](/sql/relational-databases/polybase/polybase-guide)。 如需詳細資訊，請參閱[管線協調流程、控制流程和資料移動](../technology-choices/pipeline-orchestration-data-movement.md)。

[2] 不支援使用 Azure AD 帳戶來連線至 Azure 虛擬機器上所執行的 SQL Server。 請改用 Active Directory 網域帳戶。

### <a name="scalability-capabilities"></a>延展性功能

| | Azure Analysis Services | SQL Server Analysis Services | 包含資料行存放區索引的 SQL Server | 包含資料行存放區索引的 Azure SQL Database |
| --- | --- | --- | --- | --- |
| 高可用性的備援區域伺服器  | yes | 否 | yes | yes |
| 支援查詢相應放大  | yes | 否 | yes | 否 |
| 動態延展性 (相應增加)  | yes | 否 | yes | 否 |


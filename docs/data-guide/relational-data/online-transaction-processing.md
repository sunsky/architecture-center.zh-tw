---
title: 線上交易處理 (OLTP)
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.openlocfilehash: be24bc173359539785385de4a188e7536f6d2ffe
ms.sourcegitcommit: e7e0e0282fa93f0063da3b57128ade395a9c1ef9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52902749"
---
# <a name="online-transaction-processing-oltp"></a>線上交易處理 (OLTP)

我們將使用電腦系統的交易資料管理稱為「線上交易處理 (OLTP)」。 OLTP 系統會計錄組織的日常營運所發生的商務互動，並可查詢此資料來做出推斷。

## <a name="transactional-data"></a>交易資料

交易資料是追蹤組織活動的相關互動所產生的資訊。 這些互動通常是商業交易，例如，客戶的付款、對供應商的付款、庫存的產品、接到的訂單，或是交付的服務。 交易事件代表交易本身，通常會包含時間維度、一些數值，以及對其他資料的參考。 

交易通常必須是*不可部分完成*且*一致*的。 不可部分完成性表示整個交易無論成功或失敗都一律是工作單位整體的，且絕不會處於半完成的狀態。 如果無法完成交易，則資料庫系統必須復原該交易中已完成的任何步驟。 在傳統的 RDBMS 中，只要交易無法完成，就會自動執行此復原。 一致性表示交易一律會讓資料保持在有效狀態。 (以上關於不可部分完成性與一致性的說明只是口語化的。 這些屬性另有更為正式的定義，例如 [ACID](https://en.wikipedia.org/wiki/ACID)。)

交易資料庫可對使用各種不同鎖定策略 (例如封閉式鎖定) 的交易支援強式一致性，以確保企業內容中的所有資料對於所有使用者和程序而言都絕對是一致的。 

使用交易資料最常見的部署架構，是 3 層式架構中的資料存放區層。 3 層式架構通常由展示層、商務邏輯層和資料存放區層所組成。 相關部署架構為[多層式](/azure/architecture/guide/architecture-styles/n-tier)架構，此類架構可能會以多個中介層來處理商務邏輯。

## <a name="typical-traits-of-transactional-data"></a>交易資料的一般特性

交易資料常會有下列特性：

| 需求 | 說明 |
| --- | --- |
| 正規化 | 高度正規化 |
| 結構描述 | 寫入結構描述，強制執行|
| 一致性 | 強式一致性、ACID 保證 |
| 完整性 | 高度完整性 |
| 使用交易 | 是 |
| 鎖定策略 | 封閉式或開放式|
| 可更新 | 是 |
| 可附加 | 是 |
| 工作負載 | 大量寫入，中度讀取 |
| 編製索引 | 主要和次要索引 |
| 資料大小 | 中小型 |
| 模型 | 關聯式 |
| 資料圖形 | 表格式 |
| 查詢彈性 | 高彈性 |
| 調整 | 小型 (數 MB) 至大型 (數 TB) | 

## <a name="when-to-use-this-solution"></a>此解決方案的使用時機

當您需要有效處理和儲存商務交易，並以一致的方式，立即將其提供給用戶端應用程式使用時，請選擇 OLTP。 當處理中的任何具體延遲會對公司的日常營運產生負面影響時，請使用此架構。

OLTP 系統是設計成能夠有效率地處理和儲存交易，以及查詢交易資料。 若要讓 OLTP 系統有效率地處理並儲存個別交易，部分需藉助資料正規化來達成 &mdash;，也就是將資料分解成較不重複的較小區塊。 如此會提升效率，因為它可讓 OLTP 系統獨立處理大量交易，並避免為維護資料完整性所需的額外處理 (如果存在重複的資料)。

## <a name="challenges"></a>挑戰
實作和使用 OLTP 系統可能產生幾個挑戰：

- OLTP 系統並不一定適合用來處理大量資料的彙總，但有例外狀況，例如規劃良好的 SQL Server 型解決方案。 對 OLTP 系統而言，針對資料 (依賴超過數百萬筆個別交易的彙總計算) 進行分析相當耗費資源。 它們可能會很慢的執行，並可能因封鎖資料庫的其他交易而導致速度變慢。
- 進行分析和報告高度正規化的資料時，查詢通常會很複雜，因為大多數的查詢需要使用聯結來解除資料的正規化。 此外，OLTP 系統中資料庫物件的命名慣例通常要求簡單易讀。 增加的正規化搭配簡易的命名慣例，使得商務使用者很難在不求助 DBA 或資料開發人員的協助下，使用 OLTP 系統進行查詢。
- 無限期儲存交易記錄，以及在任何一個資料表中儲存太多資料時，可能會導致查詢效能下降 (根據所儲存的交易數目而定)。 常見的解決方案是在 OLTP 系統中保留一段相關的時段 (例如目前的會計年度)，並將歷史資料卸載至其他系統，例如資料超市或[資料倉儲](./data-warehousing.md)。

## <a name="oltp-in-azure"></a>Azure 中的 OLTP

應用程式 (例如 [App Service Web Apps](/azure/app-service/app-service-web-overview) 中裝載的網站、App Servic 中執行的 REST API，或行動或桌面應用程式) 通常是間接透過 REST API 與 OLTP 系統通訊。

實際上，大部分工作負載都不是單純的 OLTP。 它們往往也會是分析元件。 此外，即時報告需求亦漸增，例如針對作業系統執行報告。 這也稱為 HTAP (混合式交易和分析處理)。 如需詳細資訊，請參閱[線上分析處理 (OLAP)](./online-analytical-processing.md)。

在 Azure 中，下列所有資料存放區都將符合 OLAP 和管理交易資料的核心需求：

- [Azure SQL Database](/azure/sql-database/)
- [Azure 虛擬機器中的 SQL Server](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)
- [適用於 MySQL 的 Azure 資料庫](/azure/mysql/)
- [適用於 PostgreSQL 的 Azure 資料庫](/azure/postgresql/)

## <a name="key-selection-criteria"></a>重要選取準則

若要縮小選項範圍，請開始回答這些問題：

- 您是需要受控服務，而不是要管理您自己的伺服器嗎？

- 您的解決方案對於 Microsoft SQL Server、MySQL 或 PostgreSQL 相容性有特定的相依性嗎？ 您的應用程式可能會根據它可用來與資料存放區通訊的支援驅動程式，或是它在使用的資料庫方面所做的假設，限制您所能選擇的資料存放區。

- 您的寫入輸送量需求是否特別高？ 如果是，請選擇提供記憶體內部資料表的選項。 

- 您的解決方案是多租用戶的嗎？ 如果是，請考慮使用支援容量集區的選項，讓多個資料庫執行個體從彈性的資源集區取用資料，而不是讓每個資料庫使用固定資源。 這有助於您將容量妥善分配到所有的資料庫執行個體，並且可讓您的解決方案更符合成本效益。

- 您的資料是否需要可在多個區域以低延遲性接受讀取？ 如果是，請選擇支援可讀取次要複本的選項。

- 您的資料庫是否需要跨地理區域的高可用性？ 如果是，請選擇支援地理複寫的選項。 同時也請考慮使用支援從主要複本自動容錯移轉至次要複本的選項。

- 您的資料庫是否有特定的安全需求？ 如果是，請檢查提供資料列層級安全性、資料遮罩和透明資料加密等功能的選項。

## <a name="capability-matrix"></a>功能對照表

下表摘要列出各項功能的主要差異。

### <a name="general-capabilities"></a>一般功能 

|                              | 連接字串 | Azure 虛擬機器中的 SQL Server | 適用於 MySQL 的 Azure 資料庫 | 適用於 PostgreSQL 的 Azure 資料庫 |
|------------------------------|--------------------|----------------------------------------|--------------------------|-------------------------------|
|      為受控服務      |        是         |                   否                   |           yes            |              是              |
|       執行平台       |        N/A         |         Windows、Linux、Docker         |           N/A            |              N/A              |
| 可程式性 <sup>1</sup> |   T-SQL、.NET、R   |         T-SQL、.NET、R、Python         |  T-SQL、.NET、R、Python  |              SQL              |

[1] 未包含可讓多種程式設計語言連線至 OLTP 資料存放區並加以使用的用戶端驅動程式支援。

### <a name="scalability-capabilities"></a>延展性功能

| | 連接字串 | Azure 虛擬機器中的 SQL Server| 適用於 MySQL 的 Azure 資料庫 | 適用於 PostgreSQL 的 Azure 資料庫|
| --- | --- | --- | --- | --- | --- |
| 資料庫執行個體大小上限 | [4 TB](/azure/sql-database/sql-database-resource-limits) | 256 TB | [1 TB](/azure/mysql/concepts-limits) | [1 TB](/azure/postgresql/concepts-limits) |
| 支援容量集區  | 是 | 是 | 否 | 否 |
| 支援叢集相應放大  | 否 | 是 | 否 | 否 |
| 動態延展性 (相應增加)  | yes | 否 | yes | 是 |

### <a name="analytic-workload-capabilities"></a>分析工作負載功能

| | 連接字串 | Azure 虛擬機器中的 SQL Server| 適用於 MySQL 的 Azure 資料庫 | 適用於 PostgreSQL 的 Azure 資料庫|
| --- | --- | --- | --- | --- | --- | 
| 暫存資料表 | 是 | 是 | 否 | 否 |
| 記憶體內部 (記憶體最佳化) 資料表 | 是 | 是 | 否 | 否 |
| 資料行存放區支援 | 是 | 是 | 否 | 否 |
| 自適性查詢處理 | 是 | 是 | 否 | 否 |

### <a name="availability-capabilities"></a>可用性功能

| | 連接字串 | Azure 虛擬機器中的 SQL Server| 適用於 MySQL 的 Azure 資料庫 | 適用於 PostgreSQL 的 Azure 資料庫|
| --- | --- | --- | --- | --- | --- | 
| 可讀取次要 | 是 | 是 | 否 | 否 | 
| 地理複寫 | 是 | 是 | 否 | 否 | 
| 自動容錯移轉至次要 | 是 | 否 | 否 | 否|
| 還原時間點 | 是 | 是 | 是 | 是 |

### <a name="security-capabilities"></a>安全性功能

|                                                                                                             | 連接字串 | Azure 虛擬機器中的 SQL Server | 適用於 MySQL 的 Azure 資料庫 | 適用於 PostgreSQL 的 Azure 資料庫 |
|-------------------------------------------------------------------------------------------------------------|--------------------|----------------------------------------|--------------------------|-------------------------------|
|                                             資料列層級安全性                                              |        是         |                  是                   |           是            |              是              |
|                                                資料遮罩                                                 |        是         |                  是                   |            否            |              否               |
|                                         透明資料加密                                         |        是         |                  是                   |           是            |              是              |
|                                  限制對特定 IP 位址的存取                                   |        是         |                  是                   |           是            |              是              |
|                                  限定為僅允許 VNET 存取                                  |        是         |                  是                   |            否            |              否               |
|                                    Azure Active Directory 驗證                                    |        是         |                  是                   |            否            |              否               |
|                                       Active Directory 驗證                                       |         否         |                  是                   |            否            |              否               |
|                                         Multi-Factor Authentication                                         |        是         |                  是                   |            否            |              否               |
| 支援[一律加密](/sql/relational-databases/security/encryption/always-encrypted-database-engine) |        是         |                  是                   |           是            |              否               |
|                                                 私人 IP                                                  |         否         |                  yes                   |           是            |              否               |


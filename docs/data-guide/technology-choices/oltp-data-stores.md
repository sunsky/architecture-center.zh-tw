---
title: 選擇 OLTP 資料存放區
description: ''
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 1c27d7d5f3b78f40822de6b77664dbf49b1367f6
ms.sourcegitcommit: c441fd165e6bebbbbbc19854ec6f3676be9c3b25
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2018
---
# <a name="choosing-an-oltp-data-store-in-azure"></a>在 Azure 中選擇 OLAP 資料存放區

線上交易處理 (OLTP) 是交易資料和交易處理的管理。 本主題將比較 Azure 中的 OLAP 解決方案適用的選項。

> [!NOTE]
> 如需關於何時使用 OLTP 資料存放區的詳細資訊，請參閱[線上交易處理](../scenarios/online-analytical-processing.md)。

## <a name="what-are-your-options-when-choosing-an-oltp-data-store"></a>您在選擇 OLAP 資料存放區時有哪些選項？

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
| | 連接字串 | Azure 虛擬機器中的 SQL Server | 適用於 MySQL 的 Azure 資料庫 | 適用於 PostgreSQL 的 Azure 資料庫 |
| --- | --- | --- | --- | --- | --- |
| 為受控服務 | yes | 否 | yes | yes |
| 執行平台 | N/A | Windows、Linux、Docker | N/A | N/A |
| 可程式性 <sup>1</sup> | T-SQL、.NET、R | T-SQL、.NET、R、Python | T-SQL、.NET、R、Python | SQL | SQL |

[1] 未包含可讓多種程式設計語言連線至 OLTP 資料存放區並加以使用的用戶端驅動程式支援。

### <a name="scalability-capabilities"></a>延展性功能
| | 連接字串 | Azure 虛擬機器中的 SQL Server| 適用於 MySQL 的 Azure 資料庫 | 適用於 PostgreSQL 的 Azure 資料庫|
| --- | --- | --- | --- | --- | --- |
| 資料庫執行個體大小上限 | [4 TB](/azure/sql-database/sql-database-resource-limits) | 256 TB | [1 TB](/azure/mysql/concepts-limits) | [1 TB](/azure/postgresql/concepts-limits) |
| 支援容量集區  | yes | yes | 否 | 否 |
| 支援叢集相應放大  | 否 | yes | 否 | 否 |
| 動態延展性 (相應增加)  | yes | 否 | yes | yes |

### <a name="analytic-workload-capabilities"></a>分析工作負載功能
| | 連接字串 | Azure 虛擬機器中的 SQL Server| 適用於 MySQL 的 Azure 資料庫 | 適用於 PostgreSQL 的 Azure 資料庫|
| --- | --- | --- | --- | --- | --- | 
| 暫存資料表 | yes | yes | 否 | 否 |
| 記憶體內部 (記憶體最佳化) 資料表 | yes | yes | 否 | 否 |
| 資料行存放區支援 | yes | yes | 否 | 否 |
| 自適性查詢處理 | yes | yes | 否 | 否 |

### <a name="availability-capabilities"></a>可用性功能
| | 連接字串 | Azure 虛擬機器中的 SQL Server| 適用於 MySQL 的 Azure 資料庫 | 適用於 PostgreSQL 的 Azure 資料庫|
| --- | --- | --- | --- | --- | --- | 
| 可讀取次要 | yes | yes | 否 | 否 | 
| 地理複寫 | yes | yes | 否 | 否 | 
| 自動容錯移轉至次要 | yes | 否 | 否 | 否|
| 還原時間點 | yes | yes | yes | yes |

### <a name="security-capabilities"></a>安全性功能
| | 連接字串 | Azure 虛擬機器中的 SQL Server| 適用於 MySQL 的 Azure 資料庫 | 適用於 PostgreSQL 的 Azure 資料庫|
| --- | --- | --- | --- | --- | --- | 
| 資料列層級安全性 | yes | yes | yes | yes |
| 資料遮罩 | yes | yes | 否 | 否 |
| 透明資料加密 | yes | yes | yes | yes |
| 限制對特定 IP 位址的存取 | yes | yes | yes | yes |
| 限定為僅允許 VNET 存取 | yes | yes | 否 | 否 |
| Azure Active Directory 驗證 | yes | yes | 否 | 否 |
| Active Directory 驗證 | 否 | yes | 否 | 否 |
| Multi-Factor Authentication | yes | yes | 否 | 否 |
| 支援[一律加密](/sql/relational-databases/security/encryption/always-encrypted-database-engine) | yes | yes | yes | 否 | 否 |
| 私人 IP | 否 | yes | yes | 否 | 否 |


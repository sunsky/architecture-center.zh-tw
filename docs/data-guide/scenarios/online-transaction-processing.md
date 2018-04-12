---
title: 線上交易處理 (OLTP)
description: ''
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 07e7f680c8ee5e8589ff7cd2236ff95f6ee84f4c
ms.sourcegitcommit: c441fd165e6bebbbbbc19854ec6f3676be9c3b25
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2018
---
# <a name="online-transaction-processing-oltp"></a>線上交易處理 (OLTP)

我們將使用電腦系統的[交易資料](../concepts/transactional-data.md)管理稱為「線上交易處理 (OLTP)」。 OLTP 系統會計錄組織的日常營運所發生的商務互動，並可查詢此資料來做出推斷。

![Azure 中的 OLTP](./images/oltp-data-pipeline.png)

## <a name="when-to-use-this-solution"></a>此解決方案的使用時機

當您需要有效處理和儲存商務交易，並以一致的方式，立即將其提供給用戶端應用程式使用時，請選擇 OLTP。 當處理中的任何具體延遲會對公司的日常營運產生負面影響時，請使用此架構。

OLTP 系統是設計成能夠有效率地處理和儲存交易，以及查詢交易資料。 若要讓 OLTP 系統有效率地處理並儲存個別交易，部分需藉助資料正規化來達成 &mdash;，也就是將資料分解成較不重複的較小區塊。 如此會提升效率，因為它可讓 OLTP 系統獨立處理大量交易，並避免為維護資料完整性所需的額外處理 (如果存在重複的資料)。

## <a name="challenges"></a>挑戰
實作和使用 OLTP 系統可能產生幾個挑戰：

- OLTP 系統並不一定適合用來處理大量資料的彙總，但有例外狀況，例如規劃良好的 SQL Server 型解決方案。 對 OLTP 系統而言，針對資料 (依賴超過數百萬筆個別交易的彙總計算) 進行分析相當耗費資源。 它們可能會很慢的執行，並可能因封鎖資料庫的其他交易而導致速度變慢。
- 進行分析和報告高度正規化的資料時，查詢通常會很複雜，因為大多數的查詢需要使用聯結來解除資料的正規化。 此外，OLTP 系統中資料庫物件的命名慣例通常要求簡單易讀。 增加的正規化搭配簡易的命名慣例，使得商務使用者很難在不求助 DBA 或資料開發人員的協助下，使用 OLTP 系統進行查詢。
- 無限期儲存交易記錄，以及在任何一個資料表中儲存太多資料時，可能會導致查詢效能下降 (根據所儲存的交易數目而定)。 常見的解決方案是在 OLTP 系統中保留一段相關的時段 (例如目前的會計年度)，並將歷史資料卸載至其他系統，例如資料超市或[資料倉儲](../technology-choices/data-warehouses.md)。

## <a name="oltp-in-azure"></a>Azure 中的 OLTP

應用程式 (例如 [App Service Web Apps](/azure/app-service/app-service-web-overview) 中裝載的網站、App Servic 中執行的 REST API，或行動或桌面應用程式) 通常是間接透過 REST API 與 OLTP 系統通訊。

實際上，大部分工作負載都不是單純的 OLTP。 它們往往也會是[分析元件](../scenarios/online-analytical-processing.md)。 此外，即時報告需求亦漸增，例如針對作業系統執行報告。 這也稱為 HTAP (混合式交易和分析處理)。 如需詳細資訊，請參閱[線上分析處理 (OLAP) 資料存放區](../technology-choices/olap-data-stores.md)。

## <a name="technology-choices"></a>技術選擇

資料儲存體：

- [Azure SQL Database](/azure/sql-database/)
- [Azure 虛擬機器中的 SQL Server](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)
- [適用於 MySQL 的 Azure 資料庫](/azure/mysql/)
- [適用於 PostgreSQL 的 Azure 資料庫](/azure/postgresql/)

如需詳細資訊，請參閱[選擇 OLTP 資料存放區](../technology-choices/oltp-data-stores.md)

資料來源：

- [App Service](/azure/app-service/)
- [行動應用程式](/azure/app-service-mobile/)


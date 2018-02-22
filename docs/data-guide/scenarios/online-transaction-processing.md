---
title: "線上交易處理 (OLTP)"
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 07e7f680c8ee5e8589ff7cd2236ff95f6ee84f4c
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2018
---
# <a name="online-transaction-processing-oltp"></a><span data-ttu-id="57b3b-102">線上交易處理 (OLTP)</span><span class="sxs-lookup"><span data-stu-id="57b3b-102">Online transaction processing (OLTP)</span></span>

<span data-ttu-id="57b3b-103">我們將使用電腦系統的[交易資料](../concepts/transactional-data.md)管理稱為「線上交易處理 (OLTP)」。</span><span class="sxs-lookup"><span data-stu-id="57b3b-103">The management of [transactional data](../concepts/transactional-data.md) using computer systems is referred to as Online Transaction Processing (OLTP).</span></span> <span data-ttu-id="57b3b-104">OLTP 系統會計錄組織的日常營運所發生的商務互動，並可查詢此資料來做出推斷。</span><span class="sxs-lookup"><span data-stu-id="57b3b-104">OLTP systems record business interactions as they occur in the day-to-day operation of the organization, and support querying of this data to make inferences.</span></span>

![Azure 中的 OLTP](./images/oltp-data-pipeline.png)

## <a name="when-to-use-this-solution"></a><span data-ttu-id="57b3b-106">此解決方案的使用時機</span><span class="sxs-lookup"><span data-stu-id="57b3b-106">When to use this solution</span></span>

<span data-ttu-id="57b3b-107">當您需要有效處理和儲存商務交易，並以一致的方式，立即將其提供給用戶端應用程式使用時，請選擇 OLTP。</span><span class="sxs-lookup"><span data-stu-id="57b3b-107">Choose OLTP when you need to efficiently process and store business transactions and immediately make them available to client applications in a consistent way.</span></span> <span data-ttu-id="57b3b-108">當處理中的任何具體延遲會對公司的日常營運產生負面影響時，請使用此架構。</span><span class="sxs-lookup"><span data-stu-id="57b3b-108">Use this architecture when any tangible delay in processing would have a negative impact on the day-to-day operations of the business.</span></span>

<span data-ttu-id="57b3b-109">OLTP 系統是設計成能夠有效率地處理和儲存交易，以及查詢交易資料。</span><span class="sxs-lookup"><span data-stu-id="57b3b-109">OLTP systems are designed to efficiently process and store transactions, as well as query transactional data.</span></span> <span data-ttu-id="57b3b-110">若要讓 OLTP 系統有效率地處理並儲存個別交易，部分需藉助資料正規化來達成 &mdash;，也就是將資料分解成較不重複的較小區塊。</span><span class="sxs-lookup"><span data-stu-id="57b3b-110">The goal of efficiently processing and storing individual transactions by an OLTP system is partly accomplished by data normalization &mdash; that is, breaking the data up into smaller chunks that are less redundant.</span></span> <span data-ttu-id="57b3b-111">如此會提升效率，因為它可讓 OLTP 系統獨立處理大量交易，並避免為維護資料完整性所需的額外處理 (如果存在重複的資料)。</span><span class="sxs-lookup"><span data-stu-id="57b3b-111">This supports efficiency because it enables the OLTP system to process large numbers of transactions independently, and avoids extra processing needed to maintain data integrity in the presence of redundant data.</span></span>

## <a name="challenges"></a><span data-ttu-id="57b3b-112">挑戰</span><span class="sxs-lookup"><span data-stu-id="57b3b-112">Challenges</span></span>
<span data-ttu-id="57b3b-113">實作和使用 OLTP 系統可能產生幾個挑戰：</span><span class="sxs-lookup"><span data-stu-id="57b3b-113">Implementing and using an OLTP system can create a few challenges:</span></span>

- <span data-ttu-id="57b3b-114">OLTP 系統並不一定適合用來處理大量資料的彙總，但有例外狀況，例如規劃良好的 SQL Server 型解決方案。</span><span class="sxs-lookup"><span data-stu-id="57b3b-114">OLTP systems are not always good for handling aggregates over large amounts of data, although there are exceptions, such as a well-planned SQL Server-based solution.</span></span> <span data-ttu-id="57b3b-115">對 OLTP 系統而言，針對資料 (依賴超過數百萬筆個別交易的彙總計算) 進行分析相當耗費資源。</span><span class="sxs-lookup"><span data-stu-id="57b3b-115">Analytics against the data, that rely on aggregate calculations over millions of individual transactions, are very resource intensive for an OLTP system.</span></span> <span data-ttu-id="57b3b-116">它們可能會很慢的執行，並可能因封鎖資料庫的其他交易而導致速度變慢。</span><span class="sxs-lookup"><span data-stu-id="57b3b-116">They can be slow to execute and can cause a slow-down by blocking other transactions in the database.</span></span>
- <span data-ttu-id="57b3b-117">進行分析和報告高度正規化的資料時，查詢通常會很複雜，因為大多數的查詢需要使用聯結來解除資料的正規化。</span><span class="sxs-lookup"><span data-stu-id="57b3b-117">When conducting analytics and reporting on data that is highly normalized, the queries tend to be complex, because most queries need to de-normalize the data by using joins.</span></span> <span data-ttu-id="57b3b-118">此外，OLTP 系統中資料庫物件的命名慣例通常要求簡單易讀。</span><span class="sxs-lookup"><span data-stu-id="57b3b-118">Also, naming conventions for database objects in OLTP systems tend to be terse and succinct.</span></span> <span data-ttu-id="57b3b-119">增加的正規化搭配簡易的命名慣例，使得商務使用者很難在不求助 DBA 或資料開發人員的協助下，使用 OLTP 系統進行查詢。</span><span class="sxs-lookup"><span data-stu-id="57b3b-119">The increased normalization coupled with terse naming conventions makes OLTP systems difficult for business users to query, without the help of a DBA or data developer.</span></span>
- <span data-ttu-id="57b3b-120">無限期儲存交易記錄，以及在任何一個資料表中儲存太多資料時，可能會導致查詢效能下降 (根據所儲存的交易數目而定)。</span><span class="sxs-lookup"><span data-stu-id="57b3b-120">Storing the history of transactions indefinitely and storing too much data in any one table can lead to slow query performance, depending on the number of transactions stored.</span></span> <span data-ttu-id="57b3b-121">常見的解決方案是在 OLTP 系統中保留一段相關的時段 (例如目前的會計年度)，並將歷史資料卸載至其他系統，例如資料超市或[資料倉儲](../technology-choices/data-warehouses.md)。</span><span class="sxs-lookup"><span data-stu-id="57b3b-121">The common solution is to maintain a relevant window of time (such as the current fiscal year) in the OLTP system and offload historical data to other systems, such as a data mart or [data warehouse](../technology-choices/data-warehouses.md).</span></span>

## <a name="oltp-in-azure"></a><span data-ttu-id="57b3b-122">Azure 中的 OLTP</span><span class="sxs-lookup"><span data-stu-id="57b3b-122">OLTP in Azure</span></span>

<span data-ttu-id="57b3b-123">應用程式 (例如 [App Service Web Apps](/azure/app-service/app-service-web-overview) 中裝載的網站、App Servic 中執行的 REST API，或行動或桌面應用程式) 通常是間接透過 REST API 與 OLTP 系統通訊。</span><span class="sxs-lookup"><span data-stu-id="57b3b-123">Applications such as websites hosted in [App Service Web Apps](/azure/app-service/app-service-web-overview), REST APIs running in App Service, or mobile or desktop applications communicate with the OLTP system, typically via a REST API intermediary.</span></span>

<span data-ttu-id="57b3b-124">實際上，大部分工作負載都不是單純的 OLTP。</span><span class="sxs-lookup"><span data-stu-id="57b3b-124">In practice, most workloads are not purely OLTP.</span></span> <span data-ttu-id="57b3b-125">它們往往也會是[分析元件](../scenarios/online-analytical-processing.md)。</span><span class="sxs-lookup"><span data-stu-id="57b3b-125">There tends to be an [analytical component](../scenarios/online-analytical-processing.md) as well.</span></span> <span data-ttu-id="57b3b-126">此外，即時報告需求亦漸增，例如針對作業系統執行報告。</span><span class="sxs-lookup"><span data-stu-id="57b3b-126">In addition, there is an increasing demand for real-time reporting, such as running reports against the operational system.</span></span> <span data-ttu-id="57b3b-127">這也稱為 HTAP (混合式交易和分析處理)。</span><span class="sxs-lookup"><span data-stu-id="57b3b-127">This is also referred to as HTAP (Hybrid Transactional and Analytical Processing).</span></span> <span data-ttu-id="57b3b-128">如需詳細資訊，請參閱[線上分析處理 (OLAP) 資料存放區](../technology-choices/olap-data-stores.md)。</span><span class="sxs-lookup"><span data-stu-id="57b3b-128">For more information, see [Online Analytical Processing (OLAP) data stores](../technology-choices/olap-data-stores.md).</span></span>

## <a name="technology-choices"></a><span data-ttu-id="57b3b-129">技術選擇</span><span class="sxs-lookup"><span data-stu-id="57b3b-129">Technology choices</span></span>

<span data-ttu-id="57b3b-130">資料儲存體：</span><span class="sxs-lookup"><span data-stu-id="57b3b-130">Data storage:</span></span>

- [<span data-ttu-id="57b3b-131">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="57b3b-131">Azure SQL Database</span></span>](/azure/sql-database/)
- [<span data-ttu-id="57b3b-132">Azure 虛擬機器中的 SQL Server</span><span class="sxs-lookup"><span data-stu-id="57b3b-132">SQL Server in an Azure VM</span></span>](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)
- [<span data-ttu-id="57b3b-133">適用於 MySQL 的 Azure 資料庫</span><span class="sxs-lookup"><span data-stu-id="57b3b-133">Azure Database for MySQL</span></span>](/azure/mysql/)
- [<span data-ttu-id="57b3b-134">適用於 PostgreSQL 的 Azure 資料庫</span><span class="sxs-lookup"><span data-stu-id="57b3b-134">Azure Database for PostgreSQL</span></span>](/azure/postgresql/)

<span data-ttu-id="57b3b-135">如需詳細資訊，請參閱[選擇 OLTP 資料存放區](../technology-choices/oltp-data-stores.md)</span><span class="sxs-lookup"><span data-stu-id="57b3b-135">For more information, see [Choosing an OLTP data store](../technology-choices/oltp-data-stores.md)</span></span>

<span data-ttu-id="57b3b-136">資料來源：</span><span class="sxs-lookup"><span data-stu-id="57b3b-136">Data sources:</span></span>

- [<span data-ttu-id="57b3b-137">App Service</span><span class="sxs-lookup"><span data-stu-id="57b3b-137">App service</span></span>](/azure/app-service/)
- [<span data-ttu-id="57b3b-138">行動應用程式</span><span class="sxs-lookup"><span data-stu-id="57b3b-138">Mobile Apps</span></span>](/azure/app-service-mobile/)


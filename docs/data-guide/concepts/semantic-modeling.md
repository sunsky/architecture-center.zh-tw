---
title: "語意模型"
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 343d17af0d933d515c724a062237c8d5df3a9e31
ms.sourcegitcommit: 29fbcb1eec44802d2c01b6d3bcf7d7bd0bae65fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="semantic-modeling"></a><span data-ttu-id="5b596-102">語意模型</span><span class="sxs-lookup"><span data-stu-id="5b596-102">Semantic modeling</span></span>

<span data-ttu-id="5b596-103">語意資料模型是一種會說明它所包含的資料元素代表何意的概念模型。</span><span class="sxs-lookup"><span data-stu-id="5b596-103">A semantic data model is a conceptual model that describes the meaning of the data elements it contains.</span></span> <span data-ttu-id="5b596-104">組織通常會有自己的詞彙，有時也會有同義字，甚或相同的詞彙會有不同的意義。</span><span class="sxs-lookup"><span data-stu-id="5b596-104">Organizations often have their own terms for things, sometimes with synonyms, or even different meanings for the same term.</span></span> <span data-ttu-id="5b596-105">例如，庫存資料庫可能會以資產識別碼和序號追蹤某項設備，但銷售資料庫卻可能將序號視為資產識別碼。</span><span class="sxs-lookup"><span data-stu-id="5b596-105">For example, an inventory database might track a piece of equipment with an asset ID and a serial number, but a sales database might refer to the serial number as the asset ID.</span></span> <span data-ttu-id="5b596-106">若未以模型說明關聯性，即無法輕易釐清這些值的關聯。</span><span class="sxs-lookup"><span data-stu-id="5b596-106">There is no simple way to relate these values without a model that describes the relationship.</span></span> 

<span data-ttu-id="5b596-107">語意模型提供了資料庫結構描述的抽象層，讓使用者無須了解基礎資料結構。</span><span class="sxs-lookup"><span data-stu-id="5b596-107">Semantic modeling provides a level of abstraction over the database schema, so that users don't need to know the underlying data structures.</span></span> <span data-ttu-id="5b596-108">這可讓使用者更容易查詢資料，而無須對基礎結構描述執行彙總與聯結。</span><span class="sxs-lookup"><span data-stu-id="5b596-108">This makes it easier for end users to query data without performing aggregates and joins over the underlying schema.</span></span> <span data-ttu-id="5b596-109">此外，資料行通常也會重新命名為使用者易記的名稱，而使得資料的內容和意義更容易理解。</span><span class="sxs-lookup"><span data-stu-id="5b596-109">Also, usually columns are renamed to more user-friendly names, so that the context and meaning of the data are more obvious.</span></span>

<span data-ttu-id="5b596-110">語意模型絕大多數用於大量讀取案例，例如分析和商業智慧 (OLAP)，而不是大量寫入的交易資料處理 (OLTP)。</span><span class="sxs-lookup"><span data-stu-id="5b596-110">Semantic modeling is predominately used for read-heavy scenarios, such as analytics and business intelligence (OLAP), as opposed to more write-heavy transactional data processing (OLTP).</span></span> <span data-ttu-id="5b596-111">這主要是由於一般語意層的本質所致：</span><span class="sxs-lookup"><span data-stu-id="5b596-111">This is mostly due to the nature of a typical semantic layer:</span></span>

- <span data-ttu-id="5b596-112">會設定彙總行為，讓報告工具能夠正確顯示這些行為。</span><span class="sxs-lookup"><span data-stu-id="5b596-112">Aggregation behaviors are set so that reporting tools display them properly.</span></span>
- <span data-ttu-id="5b596-113">會定義商務邏輯和計算方式。</span><span class="sxs-lookup"><span data-stu-id="5b596-113">Business logic and calculations are defined.</span></span>
- <span data-ttu-id="5b596-114">會包含時間導向的計算。</span><span class="sxs-lookup"><span data-stu-id="5b596-114">Time-oriented calculations are included.</span></span>
- <span data-ttu-id="5b596-115">資料常整合自多個來源。</span><span class="sxs-lookup"><span data-stu-id="5b596-115">Data is often integrated from multiple sources.</span></span> 

<span data-ttu-id="5b596-116">傳統上，資料倉儲之所以加上語意層，都是基於這些原因。</span><span class="sxs-lookup"><span data-stu-id="5b596-116">Traditionally, the semantic layer is placed over a data warehouse for these reasons.</span></span>

![資料倉儲與報告工具之間的語意層範例圖](./images/semantic-modeling.png)

<span data-ttu-id="5b596-118">語意模型主要分成兩種類型：</span><span class="sxs-lookup"><span data-stu-id="5b596-118">There are two primary types of semantic models:</span></span>

* <span data-ttu-id="5b596-119">**表格式**。</span><span class="sxs-lookup"><span data-stu-id="5b596-119">**Tabular**.</span></span> <span data-ttu-id="5b596-120">使用關聯式模型建構 (模型、資料表、資料行)。</span><span class="sxs-lookup"><span data-stu-id="5b596-120">Uses relational modeling constructs (model, tables, columns).</span></span> <span data-ttu-id="5b596-121">在內部，中繼資料會繼承自 OLAP 模型建構 (Cube、維度、量值)。</span><span class="sxs-lookup"><span data-stu-id="5b596-121">Internally, metadata is inherited from OLAP modeling constructs (cubes, dimensions, measures).</span></span> <span data-ttu-id="5b596-122">程式碼和指令碼會使用 OLAP 中繼資料。</span><span class="sxs-lookup"><span data-stu-id="5b596-122">Code and script use OLAP metadata.</span></span>
* <span data-ttu-id="5b596-123">**多維度**。</span><span class="sxs-lookup"><span data-stu-id="5b596-123">**Multidimensional**.</span></span> <span data-ttu-id="5b596-124">使用傳統 OLAP 模型建構 (Cube、維度、量值)。</span><span class="sxs-lookup"><span data-stu-id="5b596-124">Uses traditional OLAP modeling constructs (cubes, dimensions, measures).</span></span>

<span data-ttu-id="5b596-125">相關 Azure 服務：</span><span class="sxs-lookup"><span data-stu-id="5b596-125">Relevant Azure service:</span></span>
- [<span data-ttu-id="5b596-126">Azure Analysis Services</span><span class="sxs-lookup"><span data-stu-id="5b596-126">Azure Analysis Services</span></span>](https://azure.microsoft.com/services/analysis-services/)

## <a name="example-use-case"></a><span data-ttu-id="5b596-127">使用案例範例</span><span class="sxs-lookup"><span data-stu-id="5b596-127">Example use case</span></span>

<span data-ttu-id="5b596-128">某個組織有儲存在大型資料庫中的資料。</span><span class="sxs-lookup"><span data-stu-id="5b596-128">An organization has data stored in a large database.</span></span> <span data-ttu-id="5b596-129">該組織想要將此資料提供給商業使用者與客戶建立其自己的報表，並進行某些分析。</span><span class="sxs-lookup"><span data-stu-id="5b596-129">It wants to make this data available to business users and customers to create their own reports and do some analysis.</span></span> <span data-ttu-id="5b596-130">其中一個選項，是直接將資料庫的直接存取權提供給這些使用者。</span><span class="sxs-lookup"><span data-stu-id="5b596-130">One option is just to give those users direct access to the database.</span></span> <span data-ttu-id="5b596-131">不過，這麼做有幾個缺點，包括必須管理安全性和控制存取。</span><span class="sxs-lookup"><span data-stu-id="5b596-131">However, there are several drawbacks to doing this, including managing security and controlling access.</span></span> <span data-ttu-id="5b596-132">此外，使用者可能難以了解資料庫的設計，包括資料表和資料行的名稱。</span><span class="sxs-lookup"><span data-stu-id="5b596-132">Also, the design of the database, including the names of tables and columns, may be hard for a user to understand.</span></span> <span data-ttu-id="5b596-133">使用者必須知道應查詢哪些資料表、如何聯結這些資料表，以及還必須套用哪些商務邏輯才能取得正確的結果。</span><span class="sxs-lookup"><span data-stu-id="5b596-133">Users would need to know which tables to query, how those tables should be joined, and other business logic that must be applied to get the correct results.</span></span> <span data-ttu-id="5b596-134">使用者甚至還必須了解類似於 SQL 的查詢語言，才能展開作業。</span><span class="sxs-lookup"><span data-stu-id="5b596-134">Users would also need to know a query language like SQL even to get started.</span></span> <span data-ttu-id="5b596-135">這通常會導致報告相同計量的多個使用者得到不同的結果。</span><span class="sxs-lookup"><span data-stu-id="5b596-135">Typically this leads to multiple users reporting the same metrics but with different results.</span></span>

<span data-ttu-id="5b596-136">另一個選項，是將使用者所需的所有資訊封裝在語意模型中。</span><span class="sxs-lookup"><span data-stu-id="5b596-136">Another option is to encapsulate all of the information that users need into a semantic model.</span></span> <span data-ttu-id="5b596-137">使用者可使用自己選擇的報告工具，更輕鬆地查詢語意模型。</span><span class="sxs-lookup"><span data-stu-id="5b596-137">The semantic model can be more easily queried by users with a reporting tool of their choice.</span></span> <span data-ttu-id="5b596-138">語意模型所提供的資料提取自資料倉儲，以確保所有使用者會都看到相同版本的事實。</span><span class="sxs-lookup"><span data-stu-id="5b596-138">The data provided by the semantic model is pulled from a data warehouse, ensuring that all users see a single version of the truth.</span></span> <span data-ttu-id="5b596-139">語意模型也提供易記的資料表和資料行名稱、資料表之間的關聯性、描述、計算和資料列層級安全性。</span><span class="sxs-lookup"><span data-stu-id="5b596-139">The semantic model also provides friendly table and column names, relationships between tables, descriptions, calculations, and row-level security.</span></span>

## <a name="typical-traits-of-semantic-modeling"></a><span data-ttu-id="5b596-140">語意模型的一般特性</span><span class="sxs-lookup"><span data-stu-id="5b596-140">Typical traits of semantic modeling</span></span>

<span data-ttu-id="5b596-141">語意模型和分析處理常會有下列特性：</span><span class="sxs-lookup"><span data-stu-id="5b596-141">Semantic modeling and analytical processing tends to have the following traits:</span></span>

| <span data-ttu-id="5b596-142">需求</span><span class="sxs-lookup"><span data-stu-id="5b596-142">Requirement</span></span> | <span data-ttu-id="5b596-143">說明</span><span class="sxs-lookup"><span data-stu-id="5b596-143">Description</span></span> |
| --- | --- |
| <span data-ttu-id="5b596-144">結構描述</span><span class="sxs-lookup"><span data-stu-id="5b596-144">Schema</span></span> | <span data-ttu-id="5b596-145">寫入結構描述，強制執行</span><span class="sxs-lookup"><span data-stu-id="5b596-145">Schema on write, strongly enforced</span></span>|
| <span data-ttu-id="5b596-146">使用交易</span><span class="sxs-lookup"><span data-stu-id="5b596-146">Uses Transactions</span></span> | <span data-ttu-id="5b596-147">否</span><span class="sxs-lookup"><span data-stu-id="5b596-147">No</span></span> |
| <span data-ttu-id="5b596-148">鎖定策略</span><span class="sxs-lookup"><span data-stu-id="5b596-148">Locking Strategy</span></span> | <span data-ttu-id="5b596-149">None</span><span class="sxs-lookup"><span data-stu-id="5b596-149">None</span></span> |
| <span data-ttu-id="5b596-150">可更新</span><span class="sxs-lookup"><span data-stu-id="5b596-150">Updateable</span></span> | <span data-ttu-id="5b596-151">否 (通常需要重新計算 Cube)</span><span class="sxs-lookup"><span data-stu-id="5b596-151">No (typically requires recomputing cube)</span></span> |
| <span data-ttu-id="5b596-152">可附加</span><span class="sxs-lookup"><span data-stu-id="5b596-152">Appendable</span></span> | <span data-ttu-id="5b596-153">否 (通常需要重新計算 Cube)</span><span class="sxs-lookup"><span data-stu-id="5b596-153">No (typically requires recomputing cube)</span></span> |
| <span data-ttu-id="5b596-154">工作負載</span><span class="sxs-lookup"><span data-stu-id="5b596-154">Workload</span></span> | <span data-ttu-id="5b596-155">大量讀取，唯讀</span><span class="sxs-lookup"><span data-stu-id="5b596-155">Heavy reads, read-only</span></span> |
| <span data-ttu-id="5b596-156">編製索引</span><span class="sxs-lookup"><span data-stu-id="5b596-156">Indexing</span></span> | <span data-ttu-id="5b596-157">多維度索引</span><span class="sxs-lookup"><span data-stu-id="5b596-157">Multidimensional indexing</span></span> |
| <span data-ttu-id="5b596-158">資料大小</span><span class="sxs-lookup"><span data-stu-id="5b596-158">Datum size</span></span> | <span data-ttu-id="5b596-159">中小型</span><span class="sxs-lookup"><span data-stu-id="5b596-159">Small to medium sized</span></span> |
| <span data-ttu-id="5b596-160">模型</span><span class="sxs-lookup"><span data-stu-id="5b596-160">Model</span></span> | <span data-ttu-id="5b596-161">多維度</span><span class="sxs-lookup"><span data-stu-id="5b596-161">Multidimensional</span></span> |
| <span data-ttu-id="5b596-162">資料圖形：</span><span class="sxs-lookup"><span data-stu-id="5b596-162">Data shape:</span></span>| <span data-ttu-id="5b596-163">Cube 或星狀/雪花式結構描述</span><span class="sxs-lookup"><span data-stu-id="5b596-163">Cube or star/snowflake schema</span></span> |
| <span data-ttu-id="5b596-164">查詢彈性</span><span class="sxs-lookup"><span data-stu-id="5b596-164">Query flexibility</span></span> | <span data-ttu-id="5b596-165">高彈性</span><span class="sxs-lookup"><span data-stu-id="5b596-165">Highly flexible</span></span> |
| <span data-ttu-id="5b596-166">規模：</span><span class="sxs-lookup"><span data-stu-id="5b596-166">Scale:</span></span> | <span data-ttu-id="5b596-167">大型 (10s-100s GB)</span><span class="sxs-lookup"><span data-stu-id="5b596-167">Large (10s-100s GBs)</span></span> |

## <a name="see-also"></a><span data-ttu-id="5b596-168">另請參閱</span><span class="sxs-lookup"><span data-stu-id="5b596-168">See also</span></span>

- [<span data-ttu-id="5b596-169">資料倉儲</span><span class="sxs-lookup"><span data-stu-id="5b596-169">Data warehousing</span></span>](../scenarios/data-warehousing.md)
- [<span data-ttu-id="5b596-170">線上分析處理 (OLAP)</span><span class="sxs-lookup"><span data-stu-id="5b596-170">Online analytical processing (OLAP)</span></span>](../scenarios/online-analytical-processing.md)

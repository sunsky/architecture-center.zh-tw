---
title: 處理自由格式文字以供搜尋之用
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: ba1685d12834b04e7d231929c53453b51527cce2
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54484579"
---
# <a name="processing-free-form-text-for-search"></a><span data-ttu-id="53ce8-102">處理自由格式文字以供搜尋之用</span><span class="sxs-lookup"><span data-stu-id="53ce8-102">Processing free-form text for search</span></span>

<span data-ttu-id="53ce8-103">若要支援搜尋，可以對包含文字段落的文件執行自由格式文字處理。</span><span class="sxs-lookup"><span data-stu-id="53ce8-103">To support search, free-form text processing can be performed against documents containing paragraphs of text.</span></span>

<span data-ttu-id="53ce8-104">文字搜尋的運作方式是建構預先對文件集合執行計算的特殊索引。</span><span class="sxs-lookup"><span data-stu-id="53ce8-104">Text search works by constructing a specialized index that is precomputed against a collection of documents.</span></span> <span data-ttu-id="53ce8-105">用戶端應用程式會提交包含搜尋詞彙的查詢。</span><span class="sxs-lookup"><span data-stu-id="53ce8-105">A client application submits a query that contains the search terms.</span></span> <span data-ttu-id="53ce8-106">查詢會傳回結果集，其中包含一份依照每個文件符合搜尋準則的程度排序的文件清單。</span><span class="sxs-lookup"><span data-stu-id="53ce8-106">The query returns a result set, consisting of a list of documents sorted by how well each document matches the search criteria.</span></span> <span data-ttu-id="53ce8-107">結果集也可包含文件符合準則的內容，讓應用程式能夠醒目提示文件中相符的片語。</span><span class="sxs-lookup"><span data-stu-id="53ce8-107">The result set may also include the context in which the document matches the criteria, which enables the application to highlight the matching phrase in the document.</span></span>

![搜尋管線圖](./images/search-pipeline.png)

<span data-ttu-id="53ce8-109">自由格式文字處理可從大量混雜的文字資料中產生有用而可操作的資料。</span><span class="sxs-lookup"><span data-stu-id="53ce8-109">Free-form text processing can produce useful, actionable data from large amounts of noisy text data.</span></span> <span data-ttu-id="53ce8-110">其結果可為非結構化文件提供完整定義且可供查詢的結構。</span><span class="sxs-lookup"><span data-stu-id="53ce8-110">The results can give unstructured documents a well-defined and queryable structure.</span></span>

## <a name="challenges"></a><span data-ttu-id="53ce8-111">挑戰</span><span class="sxs-lookup"><span data-stu-id="53ce8-111">Challenges</span></span>

- <span data-ttu-id="53ce8-112">處理自由格式文字文件的集合，通常需要耗費大量的計算資源和時間。</span><span class="sxs-lookup"><span data-stu-id="53ce8-112">Processing a collection of free-form text documents is typically computationally intensive, as well as time intensive.</span></span>
- <span data-ttu-id="53ce8-113">若要有效率地搜尋自由格式文字，搜尋索引應根據具有類似建構的詞彙支援模糊搜尋。</span><span class="sxs-lookup"><span data-stu-id="53ce8-113">In order to search free-form text effectively, the search index should support fuzzy search based on terms that have a similar construction.</span></span> <span data-ttu-id="53ce8-114">比方說，以詞形歸併還原和語言詞幹分析建置搜尋索引，對於 "run" 的查詢就會比對出包含 "ran" 和 "running" 的文件。</span><span class="sxs-lookup"><span data-stu-id="53ce8-114">For example, search indexes are built with lemmatization and linguistic stemming, so that queries for "run" will match documents that contain "ran" and "running."</span></span>

## <a name="architecture"></a><span data-ttu-id="53ce8-115">架構</span><span class="sxs-lookup"><span data-stu-id="53ce8-115">Architecture</span></span>

<span data-ttu-id="53ce8-116">在大部分情況下，來源文字文件會載入 Azure 儲存體或 Azure Data Lake Store 之類的物件儲存體中。</span><span class="sxs-lookup"><span data-stu-id="53ce8-116">In most scenarios, the source text documents are loaded into object storage such as Azure Storage or Azure Data Lake Store.</span></span> <span data-ttu-id="53ce8-117">在 SQL Server 或 Azure SQL Database 內使用全文檢索搜尋，則屬例外。</span><span class="sxs-lookup"><span data-stu-id="53ce8-117">An exception is using full text search within SQL Server or Azure SQL Database.</span></span> <span data-ttu-id="53ce8-118">在此情況下，文件資料會載入資料庫所管理的資料表中。</span><span class="sxs-lookup"><span data-stu-id="53ce8-118">In this case, the document data is loaded into tables managed by the database.</span></span> <span data-ttu-id="53ce8-119">經儲存後，文件即會進行批次處理，以建立索引。</span><span class="sxs-lookup"><span data-stu-id="53ce8-119">Once stored, the documents are processed in a batch to create the index.</span></span>

## <a name="technology-choices"></a><span data-ttu-id="53ce8-120">技術選擇</span><span class="sxs-lookup"><span data-stu-id="53ce8-120">Technology choices</span></span>

<span data-ttu-id="53ce8-121">建立搜尋索引的選項包括 Azure 搜尋服務、Elasticsearch，以及使用 Solr 的 HDInsight。</span><span class="sxs-lookup"><span data-stu-id="53ce8-121">Options for creating a search index include Azure Search, Elasticsearch, and HDInsight with Solr.</span></span> <span data-ttu-id="53ce8-122">前述各項技術都可從文件集合填入搜尋索引。</span><span class="sxs-lookup"><span data-stu-id="53ce8-122">Each of these technologies can populate a search index from a collection of documents.</span></span> <span data-ttu-id="53ce8-123">Azure 搜尋服務提供可自動為多種文件填入索引的索引子，舉凡純文字、Excel 乃至於 PDF 格式均適用。</span><span class="sxs-lookup"><span data-stu-id="53ce8-123">Azure Search provides indexers that can automatically populate the index for documents ranging from plain text to Excel and PDF formats.</span></span> <span data-ttu-id="53ce8-124">在 HDInsight 上，Apache Solr 可為許多類型的二進位檔案編製索引，包括純文字、Word 和 PDF。</span><span class="sxs-lookup"><span data-stu-id="53ce8-124">On HDInsight, Apache Solr can index binary files of many types, including plain text, Word, and PDF.</span></span> <span data-ttu-id="53ce8-125">索引建構完成後，用戶端可以透過 REST API 存取搜尋介面。</span><span class="sxs-lookup"><span data-stu-id="53ce8-125">Once the index is constructed, clients can access the search interface by means of a REST API.</span></span>

<span data-ttu-id="53ce8-126">如果您的文字資料儲存在 SQL Server 或 Azure SQL Database 中，您可以使用資料庫內建的全文檢索搜尋。</span><span class="sxs-lookup"><span data-stu-id="53ce8-126">If your text data is stored in SQL Server or Azure SQL Database, you can use the full-text search that is built into the database.</span></span> <span data-ttu-id="53ce8-127">資料庫會從儲存在相同資料庫內的文字、二進位檔或 XML 資料填入索引。</span><span class="sxs-lookup"><span data-stu-id="53ce8-127">The database populates the index from text, binary, or XML data stored within the same database.</span></span> <span data-ttu-id="53ce8-128">用戶端會使用 T-SQL 查詢進行搜尋。</span><span class="sxs-lookup"><span data-stu-id="53ce8-128">Clients search by using T-SQL queries.</span></span>

<span data-ttu-id="53ce8-129">如需詳細資訊，請參閱[搜尋資料存放區](../technology-choices/search-options.md)。</span><span class="sxs-lookup"><span data-stu-id="53ce8-129">For more information, see [Search data stores](../technology-choices/search-options.md).</span></span>

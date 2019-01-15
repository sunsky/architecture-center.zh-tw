---
title: 處理自由格式文字以供搜尋之用
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.openlocfilehash: 6b90d70129f6b5ba978ce0f79dc5a04d57126b5d
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54113020"
---
# <a name="processing-free-form-text-for-search"></a>處理自由格式文字以供搜尋之用

若要支援搜尋，可以對包含文字段落的文件執行自由格式文字處理。

文字搜尋的運作方式是建構預先對文件集合執行計算的特殊索引。 用戶端應用程式會提交包含搜尋詞彙的查詢。 查詢會傳回結果集，其中包含一份依照每個文件符合搜尋準則的程度排序的文件清單。 結果集也可包含文件符合準則的內容，讓應用程式能夠醒目提示文件中相符的片語。

![搜尋管線圖](./images/search-pipeline.png)

自由格式文字處理可從大量混雜的文字資料中產生有用而可操作的資料。 其結果可為非結構化文件提供完整定義且可供查詢的結構。

## <a name="challenges"></a>挑戰

- 處理自由格式文字文件的集合，通常需要耗費大量的計算資源和時間。
- 若要有效率地搜尋自由格式文字，搜尋索引應根據具有類似建構的詞彙支援模糊搜尋。 比方說，以詞形歸併還原和語言詞幹分析建置搜尋索引，對於 "run" 的查詢就會比對出包含 "ran" 和 "running" 的文件。

## <a name="architecture"></a>架構

在大部分情況下，來源文字文件會載入 Azure 儲存體或 Azure Data Lake Store 之類的物件儲存體中。 在 SQL Server 或 Azure SQL Database 內使用全文檢索搜尋，則屬例外。 在此情況下，文件資料會載入資料庫所管理的資料表中。 經儲存後，文件即會進行批次處理，以建立索引。

## <a name="technology-choices"></a>技術選擇

建立搜尋索引的選項包括 Azure 搜尋服務、Elasticsearch，以及使用 Solr 的 HDInsight。 前述各項技術都可從文件集合填入搜尋索引。 Azure 搜尋服務提供可自動為多種文件填入索引的索引子，舉凡純文字、Excel 乃至於 PDF 格式均適用。 在 HDInsight 上，Apache Solr 可為許多類型的二進位檔案編製索引，包括純文字、Word 和 PDF。 索引建構完成後，用戶端可以透過 REST API 存取搜尋介面。

如果您的文字資料儲存在 SQL Server 或 Azure SQL Database 中，您可以使用資料庫內建的全文檢索搜尋。 資料庫會從儲存在相同資料庫內的文字、二進位檔或 XML 資料填入索引。 用戶端會使用 T-SQL 查詢進行搜尋。

如需詳細資訊，請參閱[搜尋資料存放區](../technology-choices/search-options.md)。

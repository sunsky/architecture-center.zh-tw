---
title: 處理 CSV 和 JSON 檔案
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.openlocfilehash: 78d57ac4f229e863e676bf3cad0140864cd243d1
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54113977"
---
# <a name="working-with-csv-and-json-files-for-data-solutions"></a>將 CSV 和 JSON 檔案用於資料解決方案

CSV 和 JSON 可能是擷取、交換和儲存非結構化或半結構化資料時最常用的格式。

## <a name="about-csv-format"></a>關於 CSV 格式

CSV (逗點分隔值) 檔案常用來以純文字格式交換系統之間的表格式資料。 它們通常包含為資料提供資料行名稱的標頭資料列，但在其他情況下，則被視為半結構化。 這是因為 CSV 無法以其原有的格式呈現階層式或關聯式資料。 資料關聯性通常由有外部索引鍵儲存在一或多個檔案之資料行中的多個 CSV 檔案進行處理，但這些檔案之間的關聯性不會以格式本身表示。 CSV 格式的檔案可使用逗點以外的其他分隔符號，例如定位點或空格。

CSV 檔案儘管有其限制，但仍是資料交換的常見選擇，因為各種不同的商業、消費者和科學應用程式均加以支援。 例如，資料庫和試算表程式即可匯入和匯出 CSV 檔案。 同樣地，大部分的批次和資料流資料處理引擎 (例如 Spark 和 Hadoop) 原本即支援序列化和還原序列化 CSV 格式的檔案，並提供在讀取時套用結構描述的方式。 這可為您提供對資料進行查詢，和以更有效率的資料格式儲存訊以加速處理的選項，讓您更輕鬆地使用資料。

## <a name="about-json-format"></a>關於 JSON 格式

JSON (JavaScript 物件標記法) 資料會顯示為半結構化格式的索引鍵/值組。 JSON 常會拿來與 XML 比較，因為兩者都能夠以階層格式儲存資料，並將子資料顯示為其父代的內嵌資料。 兩者都具有自我描述能力，並可供常人閱讀，但 JSON 文件通常小得多，因而廣泛用於線上資料交換，尤其是以 REST 為基礎的 Web 服務出現後，更是如此。

JSON 格式的檔案有幾項優於 CSV 的特點：

- JSON 可維護階層式結構，因此更易於將相關資料保存在單一文件中，並呈現複雜的關聯性。
- 大部分的程式設計語言皆提供將 JSON 還原序列化為物件的原生支援，或提供輕量型的 JSON 序列化程式庫。
- JSON 支援物件清單，而有助於避免將雜亂的清單轉譯為關聯式資料模型。
- JSON 是經常用於 NoSQL 資料庫 (例如 MongoDB、Couchbase 和 Azure Cosmos DB) 的檔案格式。

許多透過網路傳輸的資料原本即為 JSON 格式，因此大部分以 Web 為基礎的程式語言原本就支援與 JSON 搭配使用，或可藉由使用外部程式庫來序列化和還原序列化 JSON 資料。 這種對於 JSON 的通用支援，使 JSON 能夠以邏輯格式運用於資料結構顯示，以交換格式用於熱資料，以及用於冷資料的資料儲存。

許多批次和串流資料處理引擎原本即支援 JSON 序列化和還原序列化。 雖然 JSON 文件內包含的資料最終可能儲存為經過效能最佳化的格式 (例如 Parquet 或 Avro)，但它仍可作為真實來源的原始資料，這在有必要重新處理資料時，是相當重要的。

## <a name="when-to-use-csv-or-json-formats"></a>使用 CSV 或 JSON 格式的時機

CSV 較常用來匯出和匯入資料，或處理資料以進行分析和機器學習。 JSON 格式的檔案具有相同的優點，但較常用於熱資料交換解決方案。 JSON 文件常會由 Web 和行動裝置傳送以執行線上交易、由 IoT (物聯網) 裝置傳送以進行單向或雙向通訊，或是由用戶端應用程式傳送，與 SaaS 和 PaaS 服務或無伺服器架構進行通訊。

CSV 和 JSON 檔案格式都可簡化不同系統與裝置之間的資料交換。 其半結構化格式可提供傳輸絕大多數資料類型的彈性，並提供對於這些格式的通用支援，使其更易於使用。 兩者皆可在已處理的資料以二進位格式儲存時作為真實來源的原始資料，以提高查詢效能。

## <a name="working-with-csv-and-json-data-in-azure"></a>在 Azure 中使用 CSV 和 JSON 資料

Azure 根據您的需求提供了數個使用 CSV 和 JSON 檔案的解決方案。 這些檔案主要登陸位置是 Azure 儲存體或 Azure Data Lake Store。 大部分使用這些和其他文字檔案的 Azure 服務，都會與其中一個物件儲存體服務整合。 但在某些情況下，您也可以選擇直接將資料匯入 SQL Azure 或其他資料存放區中。 SQL Server 具有儲存和處理 JSON 文件的原生支援，因此可讓您輕鬆地[匯入和處理這些類型的檔案](/sql/relational-databases/json/import-json-documents-into-sql-server)。 您可以輕鬆地使用如「SQL 大量匯入」之類的公用程式，輕鬆地[匯入 CSV 檔案](/sql/relational-databases/json/import-json-documents-into-sql-server)。

您也可以直接從 Azure Blob 儲存體查詢 JSON 檔案，而不需要將其匯入 Azure SQL。 如需此方法的完整範例，請參閱[使用 Azure SQL 處理 JSON 檔案](https://medium.com/@mauridb/work-with-json-files-with-azure-sql-8946f066ddd4)。 目前此選項不適用於 CSV 檔案。

視情況之不同，您可以執行資料的[批次處理](../big-data/batch-processing.md)或[即時處理](../big-data/real-time-processing.md)。

## <a name="challenges"></a>挑戰

在使用這些格式時，將有若干問題需要克服：

- 在資料模型不受任何限制的情況下，CSV 和 JSON 檔案很容易發生資料損毀 (「垃圾進、垃圾出」)。 例如，這兩種檔案都沒有日期/時間物件的概念存在，因此，檔案格式不會阻止您在日期欄位中插入 "ABC123" (舉例而言)。

- 使用 CSV 和 JSON 檔案作為冷儲存體解決方案，將無法在處理巨量資料時適當調整。 在大多數的情況下，它們無法切分為資料分割進行平行處理，也無法壓縮為二進位格式。 這常會使這些資料處理並儲存成讀取最佳化格式，例如 Parquet 和 ORC (最佳化資料列單欄式)，因而提供與包含的資料有關的索引和內嵌統計資料。

- 您可能必須對半結構化資料套用結構描述，使其更易於查詢和分析。 為此，您通常需要將資料儲存為其他與環境的資料儲存需求相符的格式，例如儲存在資料庫中。

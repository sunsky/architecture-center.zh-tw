---
title: "資料倉儲和資料超市"
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: eec883c68cf94637c3061814d0841c73b58d7e52
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2018
---
# <a name="data-warehousing-and-data-marts"></a>資料倉儲和資料超市

資料倉儲是從一或多個不同的來源 (跨多個或所有主體區域) 整合資料的中央、組織、關聯式存放庫。 資料倉儲會儲存目前的資料和歷史資料，可多方面運用於資料的報告和分析。

![Azure 中的資料倉儲](./images/data-warehousing.png)

資料移至資料倉儲時，系統會定期從包含重要商業資訊的各種來源擷取資料。 資料移動時，可進行格式化、清理、驗證、摘要和重新組織。 或者，也可以用最低的詳細程度儲存資料，並在倉儲中提供彙總檢視以供報告之用。 無論採用何種方式，資料倉儲都會成為用於報告、分析和使用商業智慧 (BI) 工具形成重要商務決策的資料所使用的永久儲存空間。

## <a name="data-marts-and-operational-data-stores"></a>資料超市和作業資料存放區

管理大規模的資料是複雜的工作，而以單一資料倉儲呈現整個企業的所有資料，也愈來愈少見了。 反之，組織會建立較小而更為聚焦的資料倉儲 (稱為*資料超市*)，以公開分析所需的資料。 協調程序會將作業資料存放區中維護的資料填入資料超市。 作業資料存放區會作為來源交易系統與資料超市之間的媒介。 作業資料存放區所管理的資料來源是交易系統中的資料經清理後的版本，且通常是資料倉儲或資料超市所維護之歷史資料的子集。 

## <a name="when-to-use-this-solution"></a>使用此解決方案的時機

當您需要將作業系統中的大量資料轉換為容易了解、最新且正確的格式時，請選擇資料倉儲。 資料倉儲不需要依循您在作業/OLTP 資料庫中可能使用的相同簡易資料結構。 您可以使用對商業使用者和分析師具有意義的資料行名稱、重新建構結構描述以簡化資料關聯性，並將多個資料表合併為一個。 這些步驟有助於引導需要建立隨選報表或需要在 BI 系統中建立報表及分析資料的使用者，在沒有資料庫管理員 (DBA) 或資料開發人員的協助下完成操作。

如果您基於效能考量而需要在來源交易系統以外保留歷史資料，請考慮使用資料倉儲。 資料倉儲提供使用通用格式、通用索引鍵、通用資料模型和通用存取方法的集中式位置，可讓您輕鬆地多個位置存取歷史資料。

資料倉儲針對讀取存取進行了最佳化，因此報表產生的速度會優於直接對來源交易系統執行報表。 此外，資料倉儲還具有下列優點：

* 來自多個來源的所有歷史資料，都可視為單一真實來源進行儲存以及從資料倉儲存取。
* 您可以在資料匯入至資料倉儲加以清理、提供更精確的資料，以及提供一致的程式碼和描述，而改善資料品質。
* 報告工具不會與交易來源系統競爭查詢處理週期。 資料倉儲可讓交易系統主要著重於寫入的處理，而大部分的讀取要求則由資料倉儲負責因應。
* 資料倉儲有助於整合來自不同軟體的資料。
* 資料採礦工具可協助您使用自動方法在您的倉儲所儲存的資料中找出隱藏的模式。
* 透過資料倉儲，將更容易為授權使用者提供安全的存取，同時限制其他人的存取。 由於不需要為商業使用者授與資料來源的存取權，對於一或多個生產交易系統的潛在攻擊媒介因而消除。
* 資料倉儲可讓您輕鬆地以資料建立商業智慧解決方案，例如 [OLAP Cube](online-analytical-processing.md)。

## <a name="challenges"></a>挑戰

要適當設定資料倉儲以符合個人商業需求，可能必須先克服下列難題：

* 確認正確建立商業概念模型所需的時間。 這是重要的步驟，因為資料倉儲是由資訊驅動的，專案的其餘部分皆由概念對應所推動。 這牽涉到商業相關詞彙和通用格式的標準化 (例如貨幣或日期)，和以對商業使用者具有意義，但仍可確保資料彙總與關聯性之正確性的方式重新建構結構描述。
* 規劃和設定您的資料協調流程。 應考量的事項包括如何將資料從來源交易系統複製到資料倉儲，以及何時將歷史資料移出作業資料存放區，並移至倉儲中。
* 在資料匯入至倉儲時加以清理，以維護或改善資料品質。

## <a name="data-warehousing-in-azure"></a>Azure 中的資料倉儲

在 Azure 中，您的資料可能會有一或多個來源，無論是來自客戶交易，還是來自不同部門所使用的各種商業應用程式。 過去，此資料會儲存在一或多個 [OLTP](online-transaction-processing.md) 資料庫中。 資料可持續保存在其他儲存媒體中，例如網路共用、Azure 儲存體 Blob 或 Data Lake。 資料也可由資料倉儲本身儲存，或儲存在關聯式資料庫中，例如 Azure SQL Database。 分析資料存放區層的目的是為了因應分析和報告工具對資料倉儲或資料超市所發出的查詢。 在 Azure 中，這項分析存放區功能可透過 Azure SQL 資料倉儲或使用 Hive 或互動式查詢的 Azure HDInsight 來提供。 此外，您將需要某種程度的協調流程，以定期將資料從資料儲存體移動或複製到資料倉儲，而此功能則可藉由 Azure Data Factory 或 Azure HDInsight 上的 Oozie 來提供。

相關服務：

* [Azure SQL Database](/azure/sql-database/)
* [虛擬機器中的 SQL Server](/sql/sql-server/sql-server-technical-documentation)
* [Azure 資料倉儲](/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)
* [HDInsight 上的 Apache Hive](/azure/hdinsight/hadoop/hdinsight-use-hive)
* [HDInsight 上的互動式查詢 (Hive LLAP)](/azure/hdinsight/interactive-query/apache-interactive-query-get-started)


## <a name="technology-choices"></a>技術選擇

- [資料倉儲](../technology-choices/data-warehouses.md)
- [管線協調流程](../technology-choices/pipeline-orchestration-data-movement.md)


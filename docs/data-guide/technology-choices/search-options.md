---
title: 選擇搜尋資料存放區
description: ''
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: ead07e307e96696faa5ddf48505eee378027523c
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="choosing-a-search-data-store-in-azure"></a>在 Azure 中選擇搜尋資料存放區

本文將比較 Azure 中的搜尋資料存放區所適用的技術選項。 搜尋資料存放區可用來建立及儲存可對自由格式文字執行搜尋的特殊索引。 已編製索引的文字可存放於個別的資料存放區中，例如 Blob 儲存體。 應用程式對搜尋資料存放區提交查詢時，其結果會是相符文件的清單。 如需關於此案例的詳細資訊，請參閱[處理自由格式文字以供搜尋之用](../scenarios/search.md)。 

## <a name="what-are-your-options-when-choosing-a-search-data-store"></a>選擇搜尋資料存放區時有哪些選項？
在 Azure 中，下列所有資料存放區皆提供搜尋索引，而符合對自由格式文字資料進行搜尋的核心需求：
- [Azure 搜尋服務](/azure/search/search-what-is-azure-search)
- [Elasticsearch](https://azuremarketplace.microsoft.com/marketplace/apps/elastic.elasticsearch?tab=Overview)
- [使用 Solr 的 HDInsight](/azure/hdinsight/hdinsight-hadoop-solr-install-linux)
- [使用全文檢索搜尋的 Azure SQL Database](/sql/relational-databases/search/full-text-search)


## <a name="key-selection-criteria"></a>關鍵選取準則

針對搜尋案例，在您開始根據需求選擇適當的搜尋資料存放區之前，請先回答下列問題：

- 您是需要受控服務，而不是要管理您自己的伺服器嗎？

- 您可以在設計階段指定索引結構描述嗎？ 若否，請選擇支援可更新結構描述的選項。

- 您是只需要全文檢索搜尋的索引，還是也需要能夠快速彙總數值資料和其他分析？ 如果您需要的功能不只是全文檢索搜尋，請考慮使用支援更多分析功能的選項。

- 您是否需要記錄分析的搜尋索引，同時支援對已編製索引的資料進行記錄收集、彙總和視覺呈現？ 如果是，請考慮使用 Elasticsearch，這是記錄分析堆疊的一部分。

- 您需要為常用文件格式 (例如 PDF、Word、PowerPoint 和 Excel) 的資料編製索引嗎？ 如果是，請選擇提供文件索引子的選項。

- 您的資料庫是否有特定的安全需求？ 如果是，請考慮使用下列安全性功能。

## <a name="capability-matrix"></a>功能對照表

下表摘要列出各項功能的主要差異。

### <a name="general-capabilities"></a>一般功能

| | Azure 搜尋服務 | Elasticsearch | 使用 Solr 的 HDInsight | SQL Database | 
| --- | --- | --- | --- | --- | 
| 屬於受控服務 | yes | 否 | yes | yes |  
| REST API | yes | yes | yes | 否 |
| 可程式性 | .NET | Java | Java | T-SQL | 
| 常用檔案類型 (PDF、DOCX、TXT 等) 的文件索引子 | yes | 否 | yes | 否 |

### <a name="manageability-capabilities"></a>可管理性功能

| | Azure 搜尋服務 | Elasticsearch | 使用 Solr 的 HDInsight | SQL Database | 
| --- | --- | --- | --- | --- |
| 可更新的結構描述 | 否 | yes | yes | yes |
| 支援相應放大  | yes | yes | yes | 否 |

### <a name="analytic-workload-capabilities"></a>分析工作負載功能

| | Azure 搜尋服務 | Elasticsearch | 使用 Solr 的 HDInsight | SQL Database | 
| --- | --- | --- | --- | --- | 
| 支援全文檢索搜尋以外的分析 | 否 | yes | yes | yes |
| 記錄分析堆疊的一部分 | 否 | 是 (ELK) |  否 | 否 |
| 支援語意搜尋 | 是 (僅尋找類似文件) | yes | yes | yes | 

### <a name="security-capabilities"></a>安全性功能

| | Azure 搜尋服務 | Elasticsearch | 使用 Solr 的 HDInsight | SQL Database | 
| --- | --- | --- | --- | --- | 
| 資料列層級安全性 | 部分 (需要應用程式查詢以依群組識別碼篩選) | 部分 (需要應用程式查詢以依群組識別碼篩選) | yes | yes | 
| 透明資料加密 | 否 | 否 | 否 | yes |  
| 限制對特定 IP 位址的存取 | 否 | yes | yes | yes |   
| 限定為僅允許虛擬網路存取 | 否 | yes | yes | yes |  
| Active Directory 驗證 (整合式驗證) | 否 | 否 | 否 | yes | 

## <a name="see-also"></a>另請參閱

[處理自由格式文字以供搜尋之用](../scenarios/search.md)
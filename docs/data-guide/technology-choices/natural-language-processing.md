---
title: 選擇自然語言處理技術
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.openlocfilehash: 699e01bc9905d02fc8ec1113039087189f6e8caf
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54114108"
---
# <a name="choosing-a-natural-language-processing-technology-in-azure"></a>在 Azure 中選擇自然語言處理技術

自由格式文字處理會對包含文字段落的文件執行，通常是為了支援搜尋，但是也用來執行其他自然語言處理 (NLP) 工作，例如情感分析、主題偵測、語言偵測、關鍵片語擷取和文件分類。 本文主要說明可用來支援 NLP 工作的技術選項。

<!-- markdownlint-disable MD026 -->

## <a name="what-are-your-options-when-choosing-an-nlp-service"></a>選擇 NLP 服務時有哪些選項？

<!-- markdownlint-enable MD026 -->

在 Azure 中，下列服務皆提供自然語言處理 (NLP) 功能：

- [使用 Spark 和 Spark MLlib 的 Azure HDInsight](/azure/hdinsight/spark/apache-spark-overview)
- [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks)
- [Microsoft 認知服務](/azure/cognitive-services/welcome)

## <a name="key-selection-criteria"></a>關鍵選取準則

為了縮小選擇範圍，請先回答下列問題：

- 您是否要使用預先建置的模型？ 如果是，請考慮使用 Microsoft 認知服務所提供的 API。

- 您是否需要根據大量文字資料訓練自訂模型？ 如果是，請考慮使用具有 Spark MLlib 和 Spark NLP 的 Azure HDInsight。

- 您是否需要 Token 化、詞幹分析、詞形歸併還原和詞彙頻率/反向文件頻率 (TF/IDF) 等低階 NLP 功能？ 如果是，請考慮使用具有 Spark MLlib 和 Spark NLP 的 Azure HDInsight。

- 您是否需要簡單的高階 NLP 功能，例如實體和意圖識別、主題偵測、拼字檢查或情感分析？ 如果是，請考慮使用 Microsoft 認知服務所提供的 API。

## <a name="capability-matrix"></a>功能對照表

下表摘要列出各項功能的主要差異。

### <a name="general-capabilities"></a>一般功能

| | Azure HDInsight | Microsoft 認知服務 |
| --- | --- | --- |
| 提供預先訓練的模型作為服務 | 否 | 是 |
| REST API | 是 | 是 |
| 可程式性 | Python、Scala、Java | C#、Java、Node.js、Python、PHP、Ruby |
| 支援對巨量資料集和大型文件進行處理 | 是 | 否 |

### <a name="low-level-natural-language-processing-capabilities"></a>低階自然語言處理功能

| | Azure HDInsight | Microsoft 認知服務 |  
| --- | --- | --- |
| 權杖化工具 | 是 (Spark NLP) | 是 (語言分析 API) |
| 詞幹分析器 | 是 (Spark NLP) | 否 |
| 詞形分析器 | 是 (Spark NLP) | 否 |
| 詞性標記 | 是 (Spark NLP) | 是 (語言分析 API) |
| 詞彙頻率/反向文件頻率 (TF/IDF) | 是 (Spark MLlib) | 否 |
| 字串相似度&mdash;編輯距離計算 | 是 (Spark MLlib) | 否 |
| N-Gram 計算 | 是 (Spark MLlib) | 否 |
| 停用字詞移除 | 是 (Spark MLlib) | 否 |

### <a name="high-level-natural-language-processing-capabilities"></a>高階自然語言處理功能

| | Azure HDInsight | Microsoft 認知服務 |
| --- | --- | --- |
| 實體/意圖識別與擷取 | 否 | 是 (Language Understanding Intelligent Service (LUIS) API) |
| 主題偵測 | 是 (Spark NLP) | 是 (文字分析 API) |
| 拼字檢查 | 是 (Spark NLP) | 是 (Bing 拼字檢查 API) |
| 情感分析 | 是 (Spark NLP) | 是 (文字分析 API) |
| 語言偵測 | 否 | 是 (文字分析 API) |
| 支援英文以外的多種語言 | 否 | 是 (隨 API 而不同) |

## <a name="see-also"></a>另請參閱

[自然語言處理](../scenarios/natural-language-processing.md)

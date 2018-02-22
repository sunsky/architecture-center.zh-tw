---
title: "自然語言處理"
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: c03e2d017f9b4eb955a0e3494b5bc6c2603d1058
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2018
---
# <a name="natural-language-processing"></a>自然語言處理

自然語言處理 (NLP) 可用於多種工作，例如情感分析、主題偵測、語言偵測、關鍵片語擷取和文件分類。

![](./images/nlp-pipeline.png)

## <a name="when-to-use-this-solution"></a>使用此解決方案的時機

NLP 可用來進行文件分類，例如，將文件標示為機密或垃圾郵件。 NLP 的輸出可用於後續的查詢處理或搜尋。 NLP 的另一項用途，是藉由識別文件中的實體來彙總文字。 這些實體也可用來為文件標記關鍵字，而讓您能夠根據內容進行搜尋和擷取。 實體可合併為主題，並以摘要說明每個文件中的重要主題。 偵測到的主題都可用來分類文件以供導覽，或根據選定的主題列舉相關文件。 NLP 的另一項用途是為文字進行情感評分，以評估文件的調性是正面還是負面的。 這些方法都用到許多來自於自然語言處理的技術，例如： 

- **權杖化工具**。 將文字分割成單字或片語。
- **詞幹分析和詞形歸併還原**。 將單字正規化，使不同的形態會對應至具有相同意義的常態單字。 例如，"running" 和 "ran" 會對應至 "run"。 
- **實體擷取**。 識別文字中的主題。
- **詞性偵測**。 將文字識別為動詞、名詞、分詞、動詞片語等等。
- **文句界限偵測**。 偵測文字段落內的完整句子。

使用 NLP 從自由格式文字中擷取資訊和深入資訊時，通常會先從物件儲存體 (例如 Azure 儲存體或 Azure Data Lake Store) 中儲存的原始文件開始著手。 

## <a name="challenges"></a>挑戰

- 處理自由格式文字文件的集合，通常需要耗費大量的計算資源和時間。
- 若沒有標準化的文件格式，使用自由格式文字處理來擷取文件中的特定事實時，可能非常難以達到一貫精確的結果。 以發票的文字表示法為例 &mdash; 要建置可對任意數目的供應商提供的發票正確擷取發票號碼和發票日期的程序，可能非常不容易。

## <a name="architecture"></a>架構

在 NLP 解決方案中，會對包含文字段落的文件執行自由格式文字處理。 整體架構可以是[批次處理](./batch-processing.md)或[即時串流處理](./real-time-processing.md)架構。

實際的處理會隨著所需的結果而不同，但就管線而言，NLP 可能會以批次或即時的方式套用。 例如，針對文字區塊可以使用情感分析，以產生情感分數。 此作業可藉由對儲存體中的資料執行批次程序來完成，或即時使用透過傳訊服務傳遞的較小資料區塊來完成。

## <a name="technology-choices"></a>技術選擇

- [自然語言處理](../technology-choices/natural-language-processing.md)
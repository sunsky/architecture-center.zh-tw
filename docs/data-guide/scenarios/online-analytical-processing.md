---
title: "線上分析處理 (OLAP)"
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: f5ceea9c9dd03812e92fff811e54316edc22b59c
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2018
---
# <a name="online-analytical-processing-olap"></a>線上分析處理 (OLAP)

線上分析處理 (OLAP) 是一種可組織大型企業資料庫和支援複雜分析的技術。 它可用來執行複雜的分析查詢，且不會對交易系統造成負面影響。

企業用來儲存其所有交易和記錄的資料庫，稱為[線上交易處理 (OLTP)](online-transaction-processing.md) 資料庫。 這些資料庫通常具有逐一輸入的記錄。 它們常包含大量對組織有價值的資訊。 不過，用於 OLTP 的資料庫並不是針對分析而設計的。 因此，要從這些資料庫擷取答案，必須耗費許多時間與精力。 OLAP 系統的目的是要協助您以高效能的方式從資料中擷取這項商業智慧資訊。 這是因為，OLAP 資料庫針對大量讀取、低量寫入工作負載而進行了最佳化。

![Azure 中的 OLAP](./images/olap-data-pipeline.png) 

## <a name="when-to-use-this-solution"></a>使用此解決方案的時機

在下列情況下請考慮使用 OLAP：

- 您需要快速執行複雜的分析與特定查詢，且不會對 OLTP 系統造成負面影響。 
- 您想要為商業使用者提供簡單的方式，從您的資料產生報表
- 您想要提供多種彙總方式，讓使用者能夠取得快速且一致的結果。 

為大量資料套用彙總計算時，OLAP 特別具有效益。 OLAP 系統針對大量讀取案例進行了最佳化，例如分析和商業智慧。 OLAP 可讓使用者將多維度資料切分成能夠二維檢視的配量 (例如樞紐分析表)，或是依特定值篩選資料。 此程序有時也稱為「切割與細分」資料，無論資料是否跨數個資料來源進行分割，都可以執行。 這有助於使用者找出趨勢和模式以及瀏覽資料，而無須得知傳統資料分析的詳細資料。

[語意模型](../concepts/semantic-modeling.md)可協助商業使用者抽離關聯的複雜性，而更輕鬆快速地分析資料。

## <a name="challenges"></a>挑戰

在獲得 OLAP 系統提供的所有優點之前，有若干難題有待克服：

- 雖然 OLTP 系統中的資料會透過從各種來源流入的交易持續更新，但 OLAP 資料存放區通常會根據商業需求，以頻率遠低於此的間隔重新整理。 這表示 OLAP 系統較適用於策略性商業決策，而不是對變更的立即回應。 此外，也必須規劃某種程度的資料清理和協調流程，讓 OLAP 資料存放區保持在最新狀態。
- 不同於出現在 OLTP 系統中的傳統正規化關聯式資料表，OLAP 資料模型通常是多維度的。 這樣就不容易甚至無法直接對應至實體關聯性或物件導向模型，因為每個屬性會對應至一個資料行。 相對地，OLAP 系統通常會以星形或雪花式結構描述來取代傳統的正規化。

## <a name="olap-in-azure"></a>Azure 中的 OLAP

在 Azure 中，保存在 OLTP 系統 (例如 Azure SQL Database) 中的資料會複製到 OLAP 系統中，例如 [Azure Analysis Services](/azure/analysis-services/analysis-services-overview)。 資料探索和視覺效果工具 (像是 [Power BI](https://powerbi.microsoft.com)、Excel 和第三方工具) 會連線至 Analysis Services 伺服器，並且可讓使用者以高度互動和豐富的視覺化方式深入了解模型資料。 從 OLTP 到 OLAP 的資料流程通常會使用 SQL Server Integration Services 進行協調，而這些服務可使用 [Azure Data Factory](/azure/data-factory/concepts-integration-runtime) 來執行。

## <a name="technology-choices"></a>技術選擇

- [線上分析處理 (OLTP) 資料存放區](../technology-choices/olap-data-stores.md)


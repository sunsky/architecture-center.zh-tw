---
title: 使用作業的最佳資料存放區
titleSuffix: Azure Application Architecture Guide
description: 挑選最適合您資料的儲存體技術，以及了解使用方式。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 2f83e7a184e8fa94f49da4e8fd5252396ecfd632
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58242009"
---
# <a name="use-the-best-data-store-for-the-job"></a>使用作業的最佳資料存放區

## <a name="pick-the-storage-technology-that-is-the-best-fit-for-your-data-and-how-it-will-be-used"></a>挑選最適合您資料的儲存體技術，以及了解使用方式

將所有資料插入大型關聯式 SQL 資料庫的日子已經過去了。 關聯式資料庫很好 &mdash; 前提是關聯式資料的交易提供 ACID 保證。 但是它們都帶伴隨著成本：

- 查詢可能需要昂貴的聯結。
- 資料都必須正規化，而且符合預先定義的結構描述 (寫入結構描述)。
- 鎖定爭用情況可能會影響效能。

在任何大型解決方案中，有可能單一資料存放區技術不能滿足您的全部需求。 關聯式資料庫的替代方案有索引鍵/值存放區、文件資料庫、搜尋引擎資料庫、時間序列資料庫、資料行系列資料庫、圖形資料庫。 它們各有其優缺點，而每種類型的資料恰好適合其中一、兩個。

例如，您可能將產品目錄儲存在具有彈性結構描述的文件資料庫中，像是 Cosmos DB。 在此案例中，每個產品描述都是獨立的文件。 在查詢整個目錄時，您可能會索引目錄，並將索引儲存在 Azure 搜尋服務。 產品庫存可能放在 SQL 資料庫中，因為這些資料需要 ACID 保證。

記住，資料不只是保存應用程式資料。 它也包含應用程式記錄、事件、訊息和快取。

## <a name="recommendations"></a>建議

**不要每樣東西都使用關聯式資料庫**。 適當時請考慮其他資料存放區。 請參閱[選擇正確的資料存放區][data-store-overview]。

**採納 polyglot persistence**。 在任何大型解決方案中，有可能單一資料存放區技術不能滿足您的全部需求。

**考慮資料的類型**。 例如，將交易資料放在 SQL、將 JSON 文件放在文件資料庫、將遙測資料放在時間序列資料庫、將應用程式記錄放在 Elasticsearch、將 blob 放在 Azure Blob 儲存體。

**把可用性擺在 (增強式) 一致性之前**。 CAP 理論表示分散式系統必須在可用性和一致性之間取捨。 (網路磁碟分割是 CAP 理論的另一要角，永遠無法完全避免。)通常，採用「最終一致性」模型可以獲得更高的可用性。

**考慮開發小組的技能組合**。 使用 polyglot persistence 有其優點，但也有可能力不從心。 採用新的資料儲存技術需要一套新的技能。 開發團隊必須了解如何充分利用技術。 他們必須了解適當的使用模式、如何將查詢最佳化、微調效能等等。 在考慮儲存體技術時請把這一點納入考量。

**使用補償交易**。 polyglot persistence 的副作用是單一交易可能會將資料寫入多個存放區。 如果哪個環節出錯了，使用補償交易來復原任何已完成的步驟。

**查看限界內容**。 「限界內容」一詞來自網域導向設計。 限界內容是環繞網域模型的明確界限，定義了模型套用至網域的哪些部分。 在理想情況下，限界內容會對應到商務網域的子網域。 系統中的限界內容是考慮 polyglot persistence 的最佳位置。 例如，「產品」會出現在「產品目錄」和「產品庫存」兩個子網域中，但很可能這兩個子網域在儲存、更新、查詢產品方面有不同的需求。

[data-store-overview]: ../technology-choices/data-store-overview.md

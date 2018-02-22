---
title: "Azure 資料架構指南"
description: 
author: zoinerTejada
ms:date: 02/12/2018
layout: LandingPage
ms.openlocfilehash: 848601f27faf56ea069852d8983e4d10fbad9d77
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2018
---
# <a name="azure-data-architecture-guide"></a>Azure 資料架構指南

本指南提供結構化的方式，可供您在 Microsoft Azure 上設計以資料為中心的解決方案。 它是以我們與客戶合作後所得到的實證做法為基礎。

## <a name="introduction"></a>簡介

雲端正在改變應用程式的設計方式，包括資料的處理和儲存方式。 「polyglot persistence」解決方案不會使用一個可處理所有解決方案資料的一般用途資料庫，而是會使用多個專門的資料存放區，每個都會經過最佳化以提供特定功能。 您對於解決方案中資料的看法也會因此改變。 您不會再使用多層式的商務邏輯來讀取和寫入至單一資料層。 相反地，您會圍繞「資料管線」來設計解決方案，資料管線可描述資料如何流經解決方案、處理的位置、儲存在何處，以及管線的下一個元件如何使用它。 

## <a name="how-this-guide-is-structured"></a>指南結構

本指南是圍繞下列基本核心所建構的：「關聯式」資料和「非關聯式」資料之間的差異。 

![](./images/guide-steps.svg)

關聯式資料通常儲存在傳統的 RDBMS 或資料倉儲。 它有一個預先定義的結構描述 (「寫入時結構描述」)，而這個結構描述具有一組用來維護參考完整性的條件約束。 大部分的關聯式資料庫使用結構化查詢語言 (SQL) 來進行查詢。 使用關聯式資料庫的解決方案包括線上交易處理 (OLTP) 和線上分析處理 (OLAP)。

非關聯式資料是不會使用傳統 RDBMS 系統中找到之[關聯式模式](https://en.wikipedia.org/wiki/Relational_model)的任何資料。 這可能包括索引鍵-值資料、JSON 資料、圖表資料，時間序列資料，以及其他資料類型。 「NoSQL」 一詞指的是用來保存各種非關聯式資料的資料庫。 不過，該詞不完全精確，因為許多非關聯式資料存放區有支援 SQL 相容查詢。 非關聯式資料和 NoSQL 資料庫往往會在討論「巨量資料」解決方案時提及。 巨量資料架構的設計，可處理對於傳統資料庫系統而言太大或太複雜之資料的擷取、處理和分析。 

在這兩大類別之中，資料架構指南包含下列章節：

- **概念。** 概觀文章，簡介在處理這類資料時必須了解的主要概念。
- **案例。** 一組代表性的資料案例，包括適用於案例之相關 Azure 服務和適當架構的討論。
- **技術選擇。** Azure 上各種可用資料技術的詳細比較，包括開放原始碼選項。 在每個類別中，我們會描述重要選取準則和功能對照表，以便協助您選擇適合您案例的技術。

本指南不打算告訴您資料科學或資料庫理論 &mdash; 您可以找到關於這些主題的完整書籍。 相反地，本指南的目標是要協助您選取適合您案例的資料架構或資料管線，然後選取最適合您需求的 Azure 服務和技術。 如果您心裡已經有關於架構的想法，則可以直接跳到技術選項。

## <a name="traditional-rdbms"></a>傳統的 RDBMS

### <a name="concepts"></a>概念

- [關聯式資料](./concepts/relational-data.md) 
- [交易資料](./concepts/transactional-data.md) 
- [語意模型](./concepts/semantic-modeling.md) 

### <a name="scenarios"></a>案例

- [線上分析處理 (OLAP)](./scenarios/online-analytical-processing.md)
- [線上交易處理 (OLTP)](./scenarios/online-transaction-processing.md) 
- [資料倉儲和資料超市](./scenarios/data-warehousing.md)
- [ETL](./scenarios/etl.md) 

## <a name="big-data-and-nosql"></a>巨量資料和 NoSQL

### <a name="concepts"></a>概念

- [非關聯式資料存放區](./concepts/non-relational-data.md)
- [使用 CSV 和 JSON 檔案](./concepts/csv-and-json.md)
- [巨量資料架構](./concepts/big-data.md)
- [進階分析](./concepts/advanced-analytics.md) 
- [大規模機器學習](./concepts/machine-learning-at-scale.md)

### <a name="scenarios"></a>案例

- [批次處理](./scenarios/batch-processing.md)
- [即時處理](./scenarios/real-time-processing.md)
- [自由格式文字檢索](./scenarios/search.md)
- [互動式資料探索](./scenarios/interactive-data-exploration.md)
- [自然語言處理](./scenarios/natural-language-processing.md)
- [時間序列解決方案](./scenarios/time-series.md)

## <a name="cross-cutting-concerns"></a>跨領域考量

- [資料傳輸](./scenarios/data-transfer.md) 
- [將內部部署資料解決方案延伸至雲端](./scenarios/hybrid-on-premises-and-cloud.md) 
- [保護資料解決方案](./scenarios/securing-data-solutions.md) 

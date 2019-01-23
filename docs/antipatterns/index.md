---
title: 雲端應用程式的效能反模式
titleSuffix: Azure Architecture Center
description: 可能會導致延展性問題的常見做法。
author: dragon119
ms.date: 06/05/2017
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 212930368942728fc0be0c9b2af1a90293906b39
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54483049"
---
# <a name="performance-antipatterns-for-cloud-applications"></a>雲端應用程式的效能反模式

「效能反模式」是在應用程式處於壓力下可能會造成延展性問題的常見做法。

以下是常見案例：應用程式在效能測試期間行為正常。 它發行至生產，並開始處理實際工作負載。 屆時，它開始執行效能不佳 &mdash; 拒絕使用者要求、停止，或擲回例外狀況。 開發小組接著面臨兩個問題：

- 這個行為為何未在測試期間顯現？
- 我們要如何修正它？

第一個問題的答案很直截了當。 在測試環境中模擬真實使用者、他們的行為模式和他們可能執行的工作量很困難。 了解系統在負載之下如何運作的唯一完整確定方式是在生產中觀察。 要說明清楚的是，我們並非建議您應該略過效能測試。 效能測試對於取得基準效能計量相當關鍵。 但是，您必須準備好在即時系統中觀察效能問題，並且在發生時進行修正。

第二個問題的答案，如何修正此問題，則比較不直接。 造成問題的因素不計其數，有時問題只會在特定情況下展現出來。 檢測和記錄索引鍵，以尋找根本原因，但是您也必須知道要尋找什麼項目。

根據我們與 Microsoft Azure 客戶的承諾，我們已識別客戶在生產中會看到的部分常見效能問題。 針對每個反模式，我們說明反模式的典型發生原因、反模式的徵兆和解決問題的技術。 我們也會提供範例程式碼，說明反模式和建議的解決方案。

當您閱讀描述時，某些反模式可能顯而易見，但是它們比您以為的還要經常發生。 有時候應用程式會繼承在內部部署運作，但是未在雲端中相應縮小的設計。 或者應用程式可能會以非常乾淨的設計開始，但是隨著新增功能，這些反模式一一浮現。 不論如何，本指南將協助您識別並修正這些反模式。

以下是我們所識別的反模式清單：

| 反模式 | 說明 |
|-------------|-------------|
| [忙碌資料庫][BusyDatabase] | 卸載太多處理到資料存放區。 |
| [忙碌前端][BusyFrontEnd] | 將耗用大量資源的工作移至背景執行緒。 |
| [多對話 I/O][ChattyIO] | 持續傳送許多小型網路要求。 |
| [沒有直接關聯的擷取][ExtraneousFetching] | 擷取超過所需的資料，造成不必要的 I/O。 |
| [不適當的具現化][ImproperInstantiation] | 重複建立和終結設計用來共用及重複使用的物件。 |
| [整合的持續性][MonolithicPersistence] | 針對使用模式差異極大的資料使用相同的資料存放區。 |
| [無快取][NoCaching] | 無法快取資料。 |
| [同步 I/O][SynchronousIO] | I/O 完成時，封鎖呼叫執行緒。 |

[BusyDatabase]: ./busy-database/index.md
[BusyFrontEnd]: ./busy-front-end/index.md
[ChattyIO]: ./chatty-io/index.md
[ExtraneousFetching]: ./extraneous-fetching/index.md
[ImproperInstantiation]: ./improper-instantiation/index.md
[MonolithicPersistence]: ./monolithic-persistence/index.md
[NoCaching]: ./no-caching/index.md
[SynchronousIO]: ./synchronous-io/index.md

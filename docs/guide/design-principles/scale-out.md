---
title: 相應放大的設計
titleSuffix: Azure Application Architecture Guide
description: 雲端應用程式的設計應能水平調整規模。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 32ea1e7dc732c819783ad2fc06dbcffd75685d23
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243079"
---
# <a name="design-to-scale-out"></a>相應放大的設計

## <a name="design-your-application-so-that-it-can-scale-horizontally"></a>設計您的應用程式，讓它可水平調整

雲端的主要優點是彈性調整 &mdash; 能夠使用所需要的容量、在負載增加時相應放大、在不需要額外的容量時相應縮小。 設計您的應用程式，讓它可水平調整，依需求新增或移除新的執行個體。

## <a name="recommendations"></a>建議

**避免執行個體綁定**。 綁定 (或「工作階段親和性」) 意指來自相同用戶端的要求一定會路由至相同的伺服器。 綁定會限制應用程式相應放大的能力。例如，來自大量使用者的流量不會在執行個體之間分散。 造成綁定的原因包括工作階段狀態儲存在記憶體中，以及使用電腦專用金鑰進行加密。 請確定任何執行個體都可以處理任何要求。

**找出瓶頸**。 相應放大不是每個效能問題的修正魔法。 例如，如果後端資料庫是瓶頸，新增更多 Web 伺服器也不會有幫助。 先找出並解決系統中的瓶頸，再在問題所在之處增加更多執行個體。 系統的具狀態組件是最有可能造成瓶頸的原因。

**依延展性需求分解工作負載。**  應用程式通常有多個工作負載，各有不同的調整需求。 例如，應用程式有面對大眾的公用網站與分開的管理網站。 公用網站可能遇到流量突然大增，而管理網站的負載則較小、可預測。

**卸載需要大量資源的工作。** 如果可能的話，應該將需要大量 CPU 或 I/O 資源的工作移到[背景工作][background-jobs]，以將負責處理使用者要求的前端的負載降到最低。

**使用內建的自動調整功能**。 許多 Azure 計算服務內建支援自動調整。 如果應用程式有可預測的一般工作負載，則利用排程進行相應放大。 例如，在營業時間執行相應放大。 否則，如果工作負載不可預測，請使用效能計量 (例如 CPU 或要求佇列長度) 觸發自動調整。 如需自動調整的最佳作法，請參閱[自動調整][autoscaling]。

**考慮對關鍵工作負載主動進行自動調整**。 面對關鍵工作負載，您要搶先一步，未雨綢繆。 最好在負載過重時快速增加新執行個體來處理額外的流量，然後逐漸減少執行個體。

**相應縮小的設計**。  請記住，利用彈性調整，應用程式會有一段逐漸移除執行個體的相應縮小期間。 應用程式必須依正常程序處理要移除的執行個體。 以下是處理相應縮小的一些做法：

- 接聽關機事件 (如果有的話)，然後完全關機。
- 服務的用戶端/取用者應該支援暫時性錯誤處理和重試。
- 針對長時間執行的工作，考慮使用檢查點或[管道和篩選][pipes-filters-pattern]模式將工作切開。
- 將工作項目放在佇列上，如果在處理過程中執行個體被移除了，另一個執行個體就可以收取該工作。

<!-- links -->

[autoscaling]: ../../best-practices/auto-scaling.md
[background-jobs]: ../../best-practices/background-jobs.md
[pipes-filters-pattern]: ../../patterns/pipes-and-filters.md

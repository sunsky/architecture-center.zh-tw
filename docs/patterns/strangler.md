---
title: 扼制模式
titleSuffix: Cloud Design Patterns
description: 透過將功能的特定片段逐漸取代成新的應用程式和服務，來逐步移轉舊有系統。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: f139d368c98256c0190753930983a47df81a5134
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241449"
---
# <a name="strangler-pattern"></a>扼制模式

透過將功能的特定片段逐漸取代成新的應用程式和服務，來逐步移轉舊有系統。 隨著舊有系統的功能被取代，新系統最終會取代舊系統所有的功能，扼制舊系統或功能，讓您將它解除任務。

## <a name="context-and-problem"></a>內容和問題

隨著系統老化，開發工具、裝載技術、甚至是建置它們的系統架構，都可能變得越來越過時。 因為新的特色與功能加入，這些應用程式的複雜性會大幅增加，使其更難以維護或加入新功能。

完全取代複雜的系統可能會是個大工程。 通常，您將需要漸進式移轉到新的系統，同時保留舊系統來處理尚未移轉的功能。 但是，執行兩個不同版本的應用程式，表示用戶端必須知道特定功能在哪裡。 每次移轉功能或服務時，需要讓用戶端知道新的位置。

## <a name="solution"></a>解決方法

將功能的特定片段逐漸取代成新的應用程式和服務。 建立外觀以攔截前往後端舊版系統的要求。 外觀會將這些要求路由傳送至舊版應用程式或新的服務。 現有功能可以逐漸移轉至新系統，取用者可以繼續使用相同的介面，不會察覺任何移轉的發生。

![扼制模式圖](./_images/strangler.png)

此模式有助於將移轉的風險降至最低，並將開發分散在一段時間內。 使用外觀安全地將使用者路由傳送至正確的應用程式，您可以任何您喜歡的步調將功能新增至新的系統，同時確保舊版應用程式持續運作。 經過一段時間，當功能都移轉到新的系統，舊系統終將被「扼制」，且不再需要它。 完成此程序之後，可以安全地淘汰舊系統。

## <a name="issues-and-considerations"></a>問題和考量

- 請考慮如何處理新舊系統可能同時使用的服務和資料存放區。 確定兩者可以同時存取這些資源。
- 建構新應用程式和服務的方式，使它們在未來的扼制移轉中可以輕鬆地被攔截和取代。
- 在未來移轉完成時，扼制外觀將會消失或發展成舊版用戶端的配接器。
- 請確定外觀可配合移轉。
- 請確定外觀就不會成為單點失敗或效能瓶頸。

## <a name="when-to-use-this-pattern"></a>使用此模式的時機

逐漸將後端應用程式移轉至新架構時，請使用此模式。

此模式可能不適合下列時機：

- 當無法攔截送到後端系統的要求。
- 整批取代的複雜度較低的小型系統。

## <a name="related-guidance"></a>相關的指引

- [StranglerApplication](https://www.martinfowler.com/bliki/StranglerApplication.html) 上 Martin Fowler 的部落格文章

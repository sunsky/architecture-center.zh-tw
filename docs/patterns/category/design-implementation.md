---
title: 設計與實作模式
titleSuffix: Cloud Design Patterns
description: 良好的設計會包含一些要素，例如元件設計和部署中的一致性及連貫性、用於簡化管理及開發的可維護性，以及可讓元件和子系統在其他應用程式和其他案例中使用的重複使用性。 設計和實作階段所做的決策，會對雲端上裝載的應用程式和服務在品質和擁有權總成本上產生重大影響。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: ff609679bed4ee4aa88daf95ff1cab4ab2d90d48
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58248913"
---
# <a name="design-and-implementation-patterns"></a>設計與實作模式

良好的設計會包含一些要素，例如元件設計和部署中的一致性及連貫性、用於簡化管理及開發的可維護性，以及可讓元件和子系統在其他應用程式和其他案例中使用的重複使用性。 設計和實作階段所做的決策，會對雲端上裝載的應用程式和服務在品質和擁有權總成本上產生重大影響。

|                                模式                                 |                                                                                                      總結                                                                                                       |
|------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                     [外交官 (Ambassador)](../ambassador.md)                     |                                                         建立會代表取用者服務或應用程式傳送網路要求的協助程式服務。                                                          |
|          [防損毀層](../anti-corruption-layer.md)          |                                                               在現代應用程式和舊版系統間實作外觀或配接器層。                                                                |
|         [前端的後端](../backends-for-frontends.md)         |                                                          建立由特定前端應用程式或介面取用的個別後端服務。                                                          |
|                           [CQRS](../cqrs.md)                           |                                                         如果作業讀取的資料來自使用個別介面更新資料的作業，則隔離該作業。                                                         |
| [計算資源彙總](../compute-resource-consolidation.md) |                                                                     將多個工作或作業合併為單一計算單位                                                                      |
|   [外部組態存放區](../external-configuration-store.md)   |                                                        將設定資訊從應用程式部署套件移至集中位置。                                                         |
|            [閘道彙總](../gateway-aggregation.md)            |                                                                   您可以使用閘道來將多個個別的要求彙總成單一要求。                                                                   |
|             [閘道卸載](../gateway-offloading.md)             |                                                                      將共用或特殊服務功能卸載至閘道 Proxy。                                                                       |
|                [閘道路由](../gateway-routing.md)                |                                                                            使用單一端點將要求路由至多個服務。                                                                            |
|                [選出領導者](../leader-election.md)                | 選取一個執行個體作為領導者，負責管理其他執行個體，協調分散式應用程式中共同作業工作執行個體集合執行的動作。 |
|              [管道和篩選器](../pipes-and-filters.md)              |                                                     將執行複雜處理程序的工作，細分成一系列可重複使用的個別元素。                                                      |
|                        [Sidecar](../sidecar.md)                        |                                                  將應用程式的元件部署到個別的處理序或容器，以提供隔離和封裝。                                                  |
|         [靜態內容裝載](../static-content-hosting.md)         |                                                        將靜態內容部署到可以直接將其交付給用戶端的雲端儲存體服務。                                                        |
|                      [Strangler](../strangler.md)                      |                                         透過將功能的特定片段逐漸取代成新的應用程式和服務，來逐步移轉舊有系統。                                          |

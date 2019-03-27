---
title: 管理與監視模式
titleSuffix: Cloud Design Patterns
description: 雲端應用程式會在遠端資料中心內執行，其中您沒有基礎結構或作業系統 (部份情況下) 的完整控制權。 比起內部部署，這可能會讓管理和監視作業變得更困難。 應用程式必須公開執行階段資訊，讓系統管理員及操作員可以使用該資訊來管理及監視系統，以及支援變更商業需求和自訂，而不需要停止或重新部署應用程式。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 587caf680a884cda208baec50ff914f6c7238b48
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58242999"
---
# <a name="management-and-monitoring-patterns"></a>管理與監視模式

雲端應用程式會在遠端資料中心內執行，其中您沒有基礎結構或作業系統 (部份情況下) 的完整控制權。 比起內部部署，這可能會讓管理和監視作業變得更困難。 應用程式必須公開執行階段資訊，讓系統管理員及操作員可以使用該資訊來管理及監視系統，以及支援變更商業需求和自訂，而不需要停止或重新部署應用程式。

|                              模式                               |                                                              總結                                                              |
|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------|
|                   [外交官 (Ambassador)](../ambassador.md)                   |                 建立會代表取用者服務或應用程式傳送網路要求的協助程式服務。                 |
|        [防損毀層](../anti-corruption-layer.md)        |                       在最新應用程式和舊系統間實作外觀或配接層。                       |
| [外部設定存放區](../external-configuration-store.md) |                將設定資訊從應用程式部署套件移至集中位置。                |
|          [閘道彙總](../gateway-aggregation.md)          |                          您可以使用閘道來將多個個別的要求彙總成單一要求。                           |
|           [閘道卸載](../gateway-offloading.md)           |                              將共用或特殊服務功能卸載至閘道 Proxy。                              |
|              [閘道路由](../gateway-routing.md)              |                                   使用單一端點將要求路由至多個服務。                                    |
|   [健康情況端點監視](../health-endpoint-monitoring.md)   |   實作應用程式中的功能檢查，而外部工具可透過公開的端點定期存取此應用程式。    |
|                      [側車](../sidecar.md)                      |         將應用程式的元件部署到個別的處理序或容器，以提供隔離和封裝。          |
|                    [Strangler](../strangler.md)                    | 透過將功能的特定片段逐漸取代成新的應用程式和服務，來逐步移轉舊有系統。 |

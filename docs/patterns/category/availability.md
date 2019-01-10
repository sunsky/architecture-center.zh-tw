---
title: 可用性模式
titleSuffix: Cloud Design Patterns
description: 可用性定義的是系統運作且工作中的時間比例。 它將會受到系統錯誤、基礎結構問題、惡意攻擊和負載系統的影響。 通常以運作時間百分比加以測量。 雲端應用程式一般會提供使用者服務等級協定 (SLA)，這表示必須以可充分提高可用性的方式設計應用程式並且實作。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
ms.custom: seodec18
ms.openlocfilehash: d256ddd39ca3e8a452162b7b75d67f26f7bb22c2
ms.sourcegitcommit: 680c9cef945dff6fee5e66b38e24f07804510fa9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54009809"
---
# <a name="availability-patterns"></a>可用性模式

[!INCLUDE [header](../../_includes/header.md)]

可用性定義的是系統運作且工作中的時間比例。 它將會受到系統錯誤、基礎結構問題、惡意攻擊和負載系統的影響。 通常以運作時間百分比加以測量。 雲端應用程式一般會提供使用者服務等級協定 (SLA)，這表示必須以可充分提高可用性的方式設計應用程式並且實作。

|                            模式                             |                                                           總結                                                            |
|----------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| [健康情況端點監視](../health-endpoint-monitoring.md) | 實作應用程式中的功能檢查，而外部工具可透過公開的端點定期存取此應用程式。 |
|  [佇列型負載調節](../queue-based-load-leveling.md)  | 使用佇列來作為工作與其所叫用服務之間的緩衝區，以使間歇性的繁重負載順暢。  |
|                 [節流](../throttling.md)                 |   控制應用程式執行個體、個別租用戶或整個服務所使用的資源耗用量。    |

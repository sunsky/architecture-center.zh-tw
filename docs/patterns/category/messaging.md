---
title: 訊息模式
titleSuffix: Cloud Design Patterns
description: 雲端應用程式的分散式本質需要可連接元件和服務的傳訊基礎結構，最理想的情況是使用鬆散耦合的方式來達到延展性最大化。 已廣泛使用的非同步傳訊提供許多優點，但也帶來傳訊的順序安排、有害訊息的管理和冪等性 (idempotency) 等挑戰。
keywords: 設計模式
author: dragon119
ms.date: 03/01/2018
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: f838288e10acf9af5776c93972028b6b878bd7b0
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640663"
---
# <a name="messaging-patterns"></a>訊息模式

雲端應用程式的分散式本質需要可連接元件和服務的傳訊基礎結構，最理想的情況是使用鬆散耦合的方式來達到延展性最大化。 已廣泛使用的非同步傳訊提供許多優點，但也帶來傳訊的順序安排、有害訊息的管理和冪等性 (idempotency) 等挑戰。

| 模式 | 總結 |
| ------- | ------- |
| [提領票證](../claim-check.md) | 將大型訊息分割成提領票證與承載，以免癱瘓訊息匯流排。 |
| [競爭取用者](../competing-consumers.md) | 讓多個並行取用者處理在相同的傳訊通道上接收的訊息。 |
| [管道與篩選器](../pipes-and-filters.md) | 將執行複雜處理程序的工作，細分成一系列可重複使用的個別元素。 |
| [優先順序佇列](../priority-queue.md) | 針對傳送給服務的要求排列優先順序，讓高優先順序要求的接收和處理順序在低優先順序要求之前。 |
| [Publisher-Subscriber](../publisher-subscriber.md) | 讓應用程式而不需要結合的寄件者到接收者以非同步方式宣告的事件至多個想要的取用者。 |
| [佇列型負載調節](../queue-based-load-leveling.md) | 使用佇列來作為工作與其所叫用服務之間的緩衝區，以使間歇性的繁重負載順暢。 |
| [排程器代理程式監督員](../scheduler-agent-supervisor.md) | 在一組分散式服務和其他遠端資源中協調一組動作。 |

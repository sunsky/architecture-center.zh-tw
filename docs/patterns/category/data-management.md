---
title: 資料管理模式
titleSuffix: Cloud Design Patterns
description: 資料管理是雲端應用程式的關鍵元素，並且會影響多數的品質屬性。 因為效能、延展性或可用性之類的原因，資料通常裝載在不同位置以及在多個伺服器上，而這可能出現一些挑戰。 例如，必須維持資料的一致性，並且通常需要同步處理跨不同位置的資料。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 25571a431836656856ed3f299455dfdb94ae3477
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243049"
---
# <a name="data-management-patterns"></a>資料管理模式

[!INCLUDE [header](../../_includes/header.md)]

資料管理是雲端應用程式的關鍵元素，並且會影響多數的品質屬性。 因為效能、延展性或可用性之類的原因，資料通常裝載在不同位置以及在多個伺服器上，而這可能出現一些挑戰。 例如，必須維持資料的一致性，並且通常需要同步處理跨不同位置的資料。

|                        模式                         |                                                                  總結                                                                  |
|--------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
|            [另行快取](../cache-aside.md)            |                                            依需要從資料存放區將資料載入快取中                                             |
|                   [CQRS](../cqrs.md)                   |                    隔離自使用個別介面來更新資料的作業中讀取資料的作業。                     |
|         [事件來源](../event-sourcing.md)         |               使用僅附加的存放區記錄完整系列的事件，其描述對網域中的資料採取的動作。               |
|            [索引資料表](../index-table.md)            |                         針對資料存放區中查詢經常參考的欄位建立索引。                          |
|      [具體化檢視](../materialized-view.md)      | 當資料格式對必要的查詢作業而言不理想時，對一或多個資料存放區中的資料產生預先填入的檢視。 |
|               [分區化](../sharding.md)               |                                    將資料存放區分割為一組水平分割或分區。                                     |
| [靜態內容裝載](../static-content-hosting.md) |                   將靜態內容部署到可以直接將其交付給用戶端的雲端儲存體服務。                    |
|              [Valet 金鑰](../valet-key.md)              |                 使用可提供用戶端對特定資源或服務受限制的直接存取的權杖或金鑰。                 |

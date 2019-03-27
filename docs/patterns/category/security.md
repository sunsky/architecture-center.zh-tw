---
title: 安全性模式
titleSuffix: Cloud Design Patterns
description: 安全性是指系統有能力防止正確用法以外的惡意或意外動作，並且防止洩漏或遺失資訊。 雲端應用程式皆暴露在受信任內部部署範圍外的網際網路上，且開放給大眾使用，也可能服務不受信任的使用者。 應用程式的設計和部署方式應要能保護應用程式不受到惡意攻擊、僅限核准的使用者可存取應用程式，以及保護敏感性資料。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: c18255dccdbd8659705884a4141f5ecd099d9ece
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58248339"
---
# <a name="security-patterns"></a>安全性模式

[!INCLUDE [header](../../_includes/header.md)]

安全性是指系統有能力防止正確用法以外的惡意或意外動作，並且防止洩漏或遺失資訊。 雲端應用程式皆暴露在受信任內部部署範圍外的網際網路上，且開放給大眾使用，也可能服務不受信任的使用者。 應用程式的設計和部署方式應要能保護應用程式不受到惡意攻擊、僅限核准的使用者可存取應用程式，以及保護敏感性資料。

|                    模式                     |                                                                                                         總結                                                                                                         |
|------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [同盟身分識別](../federated-identity.md) |                                                                                將驗證委派給外部身分識別提供者。                                                                                |
|         [閘道管理員](../gatekeeper.md)         | 保護應用程式和服務，方法是使用專用的主機執行個體，其會作為用戶端和應用程式或服務之間的代理程式、會驗證和處理要求，並在兩者之間傳遞要求和資料。 |
|          [Valet 金鑰](../valet-key.md)          |                                                        使用可提供用戶端對特定資源或服務受限制的直接存取的權杖或金鑰。                                                        |

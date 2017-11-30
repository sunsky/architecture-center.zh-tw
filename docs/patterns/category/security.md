---
title: "安全性模式"
description: "安全性是指系統有能力防止正確用法以外的惡意或意外動作，並且防止洩漏或遺失資訊。 雲端應用程式皆暴露在受信任內部部署範圍外的網際網路上，且開放給大眾使用，也可能服務不受信任的使用者。 應用程式的設計和部署方式應要能保護應用程式不受到惡意攻擊、僅限核准的使用者可存取應用程式，以及保護敏感性資料。"
keywords: "設計模式"
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
ms.openlocfilehash: 266b5c4283d82a107783fc7a746f065be9027b51
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="security-patterns"></a>安全性模式

[!INCLUDE [header](../../_includes/header.md)]

安全性是指系統有能力防止正確用法以外的惡意或意外動作，並且防止洩漏或遺失資訊。 雲端應用程式皆暴露在受信任內部部署範圍外的網際網路上，且開放給大眾使用，也可能服務不受信任的使用者。 應用程式的設計和部署方式應要能保護應用程式不受到惡意攻擊、僅限核准的使用者可存取應用程式，以及保護敏感性資料。

| 模式 | 摘要 |
| ------- | ------- |
| [同盟身分識別](../federated-identity.md) | 將驗證委派給外部身分識別提供者。 |
| [閘道管理員](../gatekeeper.md) | 保護應用程式和服務，方法是使用專用的主機執行個體，其會作為用戶端和應用程式或服務之間的代理程式、會驗證和處理要求，並在兩者之間傳遞要求和資料。 |
| [Valet 金鑰](../valet-key.md) | 使用可提供用戶端對特定資源或服務受限制的直接存取的權杖或金鑰。 |
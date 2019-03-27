---
title: CAF：Azure 中的成本管理工具
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Azure 中的成本管理工具
author: BrianBlanchard
ms.openlocfilehash: 58dfa604863f704fd9b9fbb8d0693447cecdaf84
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58246599"
---
# <a name="cost-management-tools-in-azure"></a>Azure 中的成本管理工具

[成本管理](overview.md)是[五個雲端治理專業領域](../governance-disciplines.md)之一。 此專業領域著重於執行下列動作的方式：建立雲端支出方案、配置雲端預算、監視和強制執行雲端預算、偵測成本異常狀況，以及在實際支出不一致時調整雲端治理方案。

以下為 Azure 原生工具的清單，可協助使支援此治理專業領域的原則和流程臻至成熟。

|  | [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)  | [Azure 成本管理](/azure/cost-management/overview-cost-mgt)  | [Azure EA 內容套件](/power-bi/service-connect-to-azure-enterprise)  | [Azure 原則](/azure/governance/policy/overview) |
|---------|---------|---------|---------|---------|
|需要 Enterprise 合約嗎？     | 否         | 是 (不需要使用 [Cloudyn](/azure/cost-management/overview))         | yes         | 否         |
|預算控制     | 否         | yes         | 否         | yes         |
|監視對於單一資源的支出    | yes         | 是         | 是         | 否         |
|監視對於多個資源的支出    | 否         | yes        | 是         | 否         |
|控制對於單一資源的支出     | 是：手動調整大小         | yes         | 否         | yes         |
|強制執行對於多個資源的支出    | 否         | yes         | 否         | yes         |
|監視和偵測趨勢     | 是：有限制         | yes        | 是         | 否         |
|偵測支出異常狀況     | 否         | yes        | 是         | 否        |
|進行社交偏差     | 否        | yes        | 是        | 否        |

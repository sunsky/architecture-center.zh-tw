---
title: CAF：Azure 中的部署加速工具
description: Azure 中的部署加速工具
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
author: BrianBlanchard
ms.openlocfilehash: cd00f2fa132f5f177ccc12f61be8b5342b71197d
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58247419"
---
# <a name="deployment-acceleration-tools-in-azure"></a>Azure 中的部署加速工具

[部署加速](overview.md)是[五個雲端治理專業領域](../governance-disciplines.md)其中之一。 這個專業領域著重於建立原則來治理資產設定或部署的方式。 在這五個雲端治理專業領域中，設定治理包括部署、使設定保持一致，以及 HA/DR 策略。 這可能會透過手動活動或完全自動化的 DevOps 活動。 在任一種情況下，原則基本上會保持不變。

對治理有興趣的雲端監管人、雲端守護者和雲端架構設計人員，經常投入許多時間在部署加速專業領域中，這個專業領域跨多個雲端採用工作制訂原則和需求。 此工具鏈中的工具對於雲端治理小組很重要，應該在小組學習路徑上有高優先順序。

以下為 Azure 工具的清單，可協助使支援此治理專業領域的原則和流程臻至成熟。

|  | Azure 原則 | Azure 管理群組 | Azure Resource Manager 範本 | Azure 藍圖 | Azure Resource Graph | Azure 成本管理 |
|---------|---------|---------|---------|---------|---------|---------|
|實作公司原則     |yes |否  |否  |否 | 否 |否 |
|跨訂用帳戶套用原則     |必要 |yes  |否  |否 | 否 |否 |
|部署定義的資源     |否 |否  |yes  |否 | 否 |否 |
|建立完全相容的環境      |必要 |必要  |必要  |yes | 否 |否 |
|稽核原則      |yes |否  |否  |否 | 否 |否 |
|查詢 Azure 資源      |否 |否  |否  |否 |yes |否 |
|資源成本報告      |否 |否  |否  |否 |否 |yes |

以下是達成特定部署加速目標所需的其他工具。 這些工具通常是在治理小組外部使用，但是仍然可視為部署加速專業領域的一個層面。

|  |Azure 入口網站  |Azure Resource Manager 範本  |Azure 原則  | Azure DevOps | Azure 備份 | Azure Site Recovery |
|---------|---------|---------|---------|---------|---------|---------|
|手動部署 (單一資產)     | yes | 是  | 否  | 無效率 | 否 | yes |
|手動部署 (完整環境)     | 無效率 | yes | 否  | 無效率 | 否 | yes |
|自動化部署 (完整環境)     | 否  | yes  | 否  | yes  | 否 | yes |
|更新單一資產的設定     | yes | yes | 無效率 | 無效率 | 否 | 是 - 在複寫期間 |
|完整環境的更新設定     | 無效率 | yes | 是 | 是  | 否 | 是 - 在複寫期間 |
|管理設定飄移     | 無效率 | 無效率 | yes  | 是  | 否 | 是 - 在複寫期間 |
|建立自動化管線來部署程式碼和設定資產 (DevOps)     | 否 | 否 | 否 | yes | 否 | 否 |
|在發生中斷或 SLA 違規期間復原資料     | 否 | 否 | 否 | yes | 是 | yes |
|在發生中斷或 SLA 違規期間復原應用程式和資料     | 否 | 否 | 否 | yes | 否 | yes |

除了上述的 Azure 原生工具，客戶通常會使用第三方工具來輔助部署加速與 DevOps 部署。

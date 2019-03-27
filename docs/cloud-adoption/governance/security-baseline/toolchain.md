---
title: CAF：Azure 中的安全性基準工具
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 說明 Azure 中有助於提升安全性基準的工具
author: BrianBlanchard
ms.openlocfilehash: b316626c8ad717514f7f592abefa0f33a92afdca
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243169"
---
# <a name="security-baseline-tools-in-azure"></a>Azure 中的安全性基準工具

[安全性基準](overview.md)是[五個雲端治理專業領域](../governance-disciplines.md)的其中之一。 此專業領域著重於如何建立適當原則來保護網路、資產，以及將放在雲端提供者解決方案上的資料；其中以最後一項最為重要。 在五個雲端治理專業領域中，安全性基準包含數位資產和資料的分類。 此外也包含與資料、資產和網路的安全性相關聯的風險、商業容忍度和風險降低策略的文件說明。 從技術觀點來看，其中也牽涉到以下項目的相關決策：[加密](../../decision-guides/encryption/overview.md)、[網路需求](../../decision-guides/software-defined-network/overview.md)、[混合式身分識別策略](../../decision-guides/identity/overview.md)，以及在各個[資源群組](../../decision-guides/resource-consistency/overview.md)間[自動強制執行](../../decision-guides/policy-enforcement/overview.md)安全性原則的工具。

下列 Azure 工具有助於讓支援安全性基準的原則和流程趨於完備。

|                                                            | [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/) / [資源管理員](/azure/azure-resource-manager/resource-group-overview)  | [Azure 金鑰保存庫](/azure/key-vault)  | [Azure AD](/azure/active-directory/fundamentals/active-directory-whatis) | [Azure 原則](/azure/governance/policy/overview) | [Azure 資訊安全中心](/azure/security-center/security-center-intro) | [Azure 監視器](/azure/azure-monitor/overview) |
|------------------------------------------------------------|---------------------------------|-----------------|----------|--------------|-----------------------|---------------|
| 對資源和建立資源套用存取控制   | yes                             | 否              | yes      | 否           | 否                    | 否            |
| 保護虛擬網路                                    | yes                             | 否              | 否       | yes          | 否                    | 否            |
| 為虛擬磁碟機加密                                     | 否                              | yes             | 否       | 否           | 否                    | 否            |
| 為 PaaS 儲存體和資料庫加密                         | 否                              | yes             | 否       | 否           | 否                    | 否            |
| 管理混合式識別服務                            | 否                              | 否              | yes      | 否           | 否                    | 否            |
| 限制允許的資源類型                         | 否                              | 否              | 否       | yes          | 否                    | 否            |
| 強制執行地理區域限制                          | 否                              | 否              | 否       | yes          | 否                    | 否            |
| 監視網路和資源的安全性健康情況          | 否                              | 否              | 否       | 否           | yes                   | yes           |
| 偵測惡意活動                                  | 否                              | 否              | 否       | 否           | yes                   | yes           |
| 事先偵測弱點                        | 否                              | 否              | 否       | 否           | yes                   | 否            |
| 設定備份和災害復原                     | yes                             | 否              | 否       | 否           | 否                    | 否            |

如需 Azure 安全性工具和服務的完整清單，請參閱 [Azure 可用的安全性服務與技術](/azure/security/azure-security-services-technologies)。

此外，許多客戶也會使用第三方工具來推動安全性基準活動。 如需詳細資訊，請參閱[在 Azure 資訊安全中心整合安全性解決方案](/azure/security-center/security-center-partner-integration)一文。

除了安全性工具以外，[Microsoft 信任中心](https://www.microsoft.com/trustcenter/guidance/risk-assessment)也包含廣泛的指引、報告和相關文件，可協助您在移轉規劃程序中執行風險評估。

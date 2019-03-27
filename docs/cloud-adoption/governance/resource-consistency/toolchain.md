---
title: CAF：Azure 中的資源一致性工具
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Azure 中的資源一致性工具
author: BrianBlanchard
ms.openlocfilehash: 68503289f60fbb3682264ff39546ca7b7700cef5
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241579"
---
# <a name="resource-consistency-tools-in-azure"></a>Azure 中的資源一致性工具

[資源一致性](overview.md)是[五個雲端治理專業領域](../governance-disciplines.md)的其中之一。 這個專業領域著重於訂定與環境、應用程式或工作負載之作業管理相關的原則。 在雲端治理的五個專業領域中，資源一致性包含應用程式、工作負載，和資產效能監視功能。 它也包含滿足擴展需求、補救效能 SLA 違規和藉由自動補救的方式主動避免效能 SLA 違規所需任務。

以下為 Azure 工具的清單，可協助使支援此治理專業領域的原則和流程臻至成熟。

|    | [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)  | [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)  | [Azure 藍圖](/azure/governance/blueprints/overview) | [Azure 自動化](/azure/automation/automation-intro) | [Azure AD](/azure/active-directory/fundamentals/active-directory-whatis) |
|---------|---------|---------|---------|---------|---------|
| 部署資源                             | yes | 是 | 是 | 是 | 否  |
| 管理資源                             | yes | 是 | 是 | 是 | 否  |
| 使用範本部署資源             | 否  | yes | 否  | yes | 否  |
| 協調環境部署          | 否  | 否  | yes | 否  | 否  |
| 定義資源群組                       | yes | 是 | 是 | 否  | 否  |
| 管理工作負載和帳戶擁有者           | yes | 是 | 是 | 否  | 否  |
| 管理資源的條件式存取       | yes | 是 | 是 | 否  | 否  |
| 設定 RBAC 使用者                         | yes | 否  | 否  | 否  | yes |
| 將角色和權限指派給資源 | yes | 是 | 是 | 否  | yes |
| 定義資源間的相依性        | 否  | yes | 是 | 否  | 否  |
| 套用存取控制                         | yes | 是 | 是 | 否  | yes |
| 存取可用性和延展性          | 否  | 否  | 否  | yes | 否  |
| 將標記套用到資源                      | yes | 是 | 是 | 否  | 否  |
| 指派 Azure 原則規則                    | yes | 是 | 是 | 否  | 否  |
| 規劃災害復原的資源         | yes | 是 | 是 | 否  | 否  |
| 套用自動化的補救方法                  | 否  | 否  | 否  | yes | 否  |
| 管理計費                               | yes | 否  | 否  | 否  | 否  |

除了這些資源一致性工具和功能外，您還必須監視已部署的資源，以了解效能和健康情況問題。 [Azure 監視器](/azure/azure-monitor/overview)是 Azure 中的預設監視和報告解決方案。 Azure 監視器提供了許多可用於監視雲端資源的個別功能，下表顯示可讓您處理常見監視需求的功能。

|                                                    | [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/) | [Application Insights](/azure/application-insights/app-insights-overview) | [Log Analytics](/azure/azure-monitor/log-query/log-query-overview) | [Azure 監視器 REST API](/rest/api/monitor/) |
|----------------------------------------------------|--------------|----------------------|---------------|------------------------|
| 記錄虛擬機器的遙測資料                 | 否           | 否                   | yes           | 否                     |
| 記錄虛擬網路的遙測資料              | 否           | 否                   | yes           | 否                     |
| 記錄 PaaS 服務的遙測資料                   | 否           | 否                   | yes           | 否                     |
| 記錄應用程式的遙測資料                     | 否           | yes                  | 否            | 否                     |
| 設定報告和警示                       | yes          | 否                   | 否            | yes                    |
| 排程一般報表或自訂分析        | 否           | 否                   | 否            | 否                     |
| 以視覺化方式檢視和分析記錄和效能資料     | yes          | 否                   | 否            | 否                     |
| 與內部部署或第三方監視解決方案整合     | 否           | 否                   | 否            | yes                    |

規劃部署時，您將需要考量記錄資料儲存位置，以及如何將雲端式[報告和監視服務](../../decision-guides/log-and-report/overview.md)與現有的處理程序和工具整合。

> [!NOTE]
> 組織也會使用第三方 DevOps 工具來監視工作負載和資源。 如需詳細資訊，請參閱 [DevOps Tool Integrations](https://azure.microsoft.com/products/devops-tool-integrations/)。

# <a name="next-steps"></a>後續步驟

了解如何在 Azure 中建立、指派和管理[原則定義](/azure/governance/policy/)。

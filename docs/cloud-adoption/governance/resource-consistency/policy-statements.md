---
title: CAF：資源一致性原則聲明範例
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 資源一致性原則聲明範例
author: BrianBlanchard
ms.openlocfilehash: 9217ae73b0edf5b40bedac1cdc961b7a34da87d1
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900836"
---
# <a name="resource-consistency-sample-policy-statements"></a>資源一致性原則聲明範例

每個雲端原則聲明都是一個指導方針，用來解決在風險評估流程中識別到的特定風險。 這些聲明應該會提供風險的簡明摘要，以及如何處理這些風險的方案。 每個聲明定義都應該包含下列資訊：

- 技術風險 - 此原則將解決的風險摘要。
- 原則聲明 - 明確地摘要說明原則需求。
- 設計選項 - IT 小組與開發人員可以在實作原則時使用的可操作建議、規格或其他指引。

下列範例原則聲明會解決一些與資源一致性相關的常見業務風險，並可作為您為自己組織需求撰寫原則聲明草稿時的參考範例。 請注意，這些範例並非禁止使用，而且可能提供數個原則選項來處理任何特定的風險。 請與業務和 IT 小組密切合作，以識別適用於您獨特風險集合的最佳原則解決方案。

## <a name="tagging"></a>標記

**技術風險**：如果沒有與已部署的資源相關聯的適當中繼資料標記，IT 作業就無法根據所需的 SLA、商務作業的重要性或營運成本，排定支援的優先順序或最佳化資源。 這可能會導致 IT 資源的錯誤配置，以及事件解決方案中的潛在延遲。

**原則聲明**：您將實作下列原則：

- 已部署的資產應使用下列值標記：成本、嚴重性、SLA 和環境。
- 治理工具必須確認與成本、重要性、SLA、應用程式及環境相關的標記。 所有值必須符合由治理小組管理的預先定義值。

**潛在的設計選項**：在 Azure 中，大部分的資源類型都支援[標準名稱/值中繼資料標記](/azure/azure-resource-manager/resource-group-using-tags)。 [Azure 原則](/azure/governance/policy/overview)用於在資源建立期間強制執行特定標記。

## <a name="ungoverned-subscriptions"></a>未治理的訂用帳戶

**技術風險**：任意建立訂用帳戶和管理群組，可能會導致您雲端資產中的隔離部分未正確地受限於您的治理原則。

**原則聲明**：為任何任務關鍵性應用程式或受保護的資料建立新的訂用帳戶或管理群組將需要經過雲端治理小組審查。 核准的變更會整合到適當的藍圖指派中。

**潛在的設計選項**：將對組織的系統管理存取權 [Azure 管理群組](/azure/governance/management-groups/)鎖定為僅限核准的控管小組成員，他們會控制訂用帳戶建立和存取控制程序。

## <a name="manage-updates-to-virtual-machines"></a>管理虛擬機器的更新

**技術風險**：未使用最新更新和軟體修補程式的虛擬機器 (VM) 容易受到安全性或效能問題的影響，這可能會導致服務中斷。

**原則聲明**：治理工具必須強制在所有已部署的 VM 上啟用自動更新。 必須與作業管理小組一起檢閱違規事件，並根據作業原則來進行修復。 不會自動更新的資產必須包含在 IT 部門所負責的處理程序中。

**潛在的設計選項**：針對 Azure 裝載的 VM，您可以使用 [Azure 自動化中的更新管理解決方案](/azure/automation/automation-update-management)提供一致的更新管理。

## <a name="deployment-compliance"></a>部署合規性

**技術風險**：未經雲端管理小組完全審查的部署指令碼和自動化工具可能會導致資源部署違反原則。

**原則聲明**：您將實作下列原則：

- 部署工具必須由雲端治理小組核准，以確保能持續治理已部署的資產。
- 部署指令碼必須在雲端治理小組可存取的中央存放庫中進行維護，以便定進行定期檢閱和稽核。

**潛在的設計選項**：透過一致使用 [Azure 藍圖](/azure/governance/blueprints/)來管理自動化部署，可以實現符合貴組織治理標準和原則的 Azure 資源的一致部署。

## <a name="monitoring"></a>監視

**技術風險**：未正確地實作或不一致地檢測監視，可以避免檢測到工作負載的健康狀態問題或其他違反合規性原則的行為。

**原則聲明**：您將實作下列原則：

- 治理工具必須確認所有與任務關鍵性應用程式或受保護資料相關的資產都會受到監視，以了解資源損耗與最佳化的情形。
- 治理工具必須確認會針對所有任務關鍵性應用程式或受保護的資料，收集適當層級的記錄資料。

**潛在的設計選項**：[Azure 監視器](/azure/azure-monitor/overview)是 Azure 平台中的預設監視服務，在部署資源時可以透過使用 [Azure 藍圖](/azure/governance/blueprints/)來強制執行一致的監視。

## <a name="disaster-recovery"></a>災害復原

**技術風險**：資源失敗、刪除或損毀，可能導致任務關鍵性應用程式或服務中斷和敏感性資料遺失。

**原則聲明**：所有任務關鍵性應用程式和受保護的資料都必須實作備份和復原解決方案，以將中斷或系統失敗的商務影響降到最低。

**潛在的設計選項**：[Azure Site Recovery] 服務提供備份、復原及複寫功能，目的在將商務持續性和災害復原 (BCDR) 之使用案例中的中斷期間降到最低。

## <a name="next-steps"></a>後續步驟

使用本文提及的範例作為起點，以開發與您雲端採用方案保持一致的原則來解決特定業務風險。

若要開始開發與資源一致性相關的自訂原則聲明，請下載[資源一致性範本](template.md)。

若要加速採用這個專業領域，請選擇最密切配合您環境的[可採取動作的治理旅程](../journeys/overview.md)。 然後修改設計，以納入您特定的公司原則決策。

> [!div class="nextstepaction"]
> [可採取動作的治理旅程](../journeys/overview.md)

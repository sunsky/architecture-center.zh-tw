---
title: CAF：部署加速範例原則聲明
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 部署加速範例原則聲明
author: alexbuckgit
ms.openlocfilehash: 4f7d59e6653c29db03f966d1c7105524b72586fc
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900996"
---
# <a name="deployment-acceleration-sample-policy-statements"></a>部署加速範例原則聲明

個別的雲端原則聲明是用來解決在風險評估流程中識別出之特定風險的方針。 這些聲明應該會提供風險的簡明摘要，以及如何處理它們的方案。 每個聲明定義都應該包含下列資訊：

- **技術風險。** 此原則將解決的風險摘要。
- **原則聲明。** 明確地摘要說明原則需求。
- **設計選項。** IT 小組與開發人員可以在實作原則時使用之可操作的建議、規格或其他指引。

下列範例原則聲明會解決一些與設定相關的常見業務風險，並提供來做為您撰寫原則聲明的草稿以滿足自己的組織需求時所參考的範例。 請注意，這些範例並非禁止使用，而且可能提供數個原則選項來處理任何特定的風險。 與業務和 IT 小組密切合作，來識別適用於您獨特風險集合的最佳原則解決方案。

## <a name="reliance-on-manual-deployment-or-configuration-of-systems"></a>依賴手動部署或設定系統

**技術風險**：依賴部署或設定期間的人為介入，會提高人為錯誤的可能性，並降低系統部署和設定的重複性和可預測性。 它通常也會導致系統資源的部署變慢。

**原則聲明**：部署到雲端的所有資產都應盡可能地使用範本或自動化指令碼來部署。

**潛在的設計選項**：[Azure Resource Manager 範本](/azure/azure-resource-manager/resource-group-overview#template-deployment)提供基礎結構即程式碼的方法來將資源部署到 Azure。 [Azure 建置組塊](https://github.com/mspnp/template-building-blocks/wiki) \(英文\) 提供命令列工具，以及一組設計來簡化 Azure 資源部署的 Resource Manager 範本。

## <a name="lack-of-visibility-into-system-issues"></a>缺少系統問題的可見性

**技術風險**：對於商務系統監視與診斷的不足，會妨礙操作人員在發生系統中斷之前找出並修復問題，而且可能大幅增加正確解析中斷情況所需的時間。

**原則聲明**：您將實作下列原則：

- 系統將針對所有生產系統和元件找出關鍵計量和診斷量值，而且會將監視與診斷工具套用至這些系統，並由操作人員定期監控。
- 操作人員將考慮在預備和 QA 之類的非生產環境中使用監視與診斷工具，並於生產環境中發生系統問題之前先找到它們。

**潛在的設計選項**：[Azure 監視器](/azure/azure-monitor/) (其中也包括 Log Analytics 和 Application Insights) 提供工具來收集和分析遙測，以協助您了解應用程式的執行方式，並主動找出影響它們的問題及其相依的資源。

## <a name="configuration-security-reviews"></a>設定安全性檢閱

**技術風險**：經過一段時間之後，新的安全性威脅或考量可能會提高未經授權存取安全資源的風險。

**原則聲明**：雲端治理流程必須包括每季與設定管理小組一起檢閱，以找出雲端資產設定應該避免的惡意執行者或使用模式。

**潛在的設計選項**：召開每季安全性檢閱會議，其中包括治理小組成員，以及負責設定雲端應用程式和資源的 IT 人員。 檢閱現有的安全性資料和計量，以便在目前的部署加速原則和工具中建立間距，並更新原則來降低任何新風險。

## <a name="next-steps"></a>後續步驟

使用本文提及的範例作為起點，以開發與您雲端採用方案保持一致的原則來解決特定的業務風險。

若要開始自行開發與身分識別管理相關的自訂原則聲明，請下載[身分識別基準範本](template.md)。

若要加速採用這個專業領域，請選擇最密切配合您的環境且[可操作的治理旅程圖](../journeys/overview.md)。 然後修改設計，以納入您特定的公司原則決策。

> [!div class="nextstepaction"]
> [可操作的治理旅程圖](../journeys/overview.md)
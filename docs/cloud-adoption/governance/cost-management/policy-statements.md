---
title: CAF：成本管理範例原則聲明
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 1/4/2019
description: 成本管理範例原則聲明
author: BrianBlanchard
ms.openlocfilehash: 1c8b94ae5285fa26cdf9760892beaced2487af8b
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901112"
---
# <a name="cost-management-sample-policy-statements"></a>成本管理範例原則聲明

個別的雲端原則聲明是用來解決在風險評估流程中識別出之特定風險的方針。 這些聲明應該會提供風險的簡明摘要，以及如何處理它們的方案。 每個聲明定義都應該包含下列資訊：

- 業務風險：此原則將解決的風險摘要。
- 原則聲明：明確地摘要說明原則需求。
- 設計選項：IT 小組與開發人員可以在實作原則時使用之可操作的建議、規格或其他指引。

下列範例原則聲明會解決一些與成本相關的常見業務風險，並提供來做為您撰寫實際原則聲明的草稿以滿足自己的組織需求時所參考的範例。 這些範例並非禁止使用，而且可能提供數個原則選項來處理任何已識別出的單一風險。 與業務和 IT 小組密切合作，來識別適用於特定成本相關風險的最佳原則解決方案。  

## <a name="future-proofing"></a>與未來技術的兼容性

**業務風險**。 目前不保證治理小組會投資成本管理專業領域的準則。 不過，您可以預期未來將有這類投資。

**原則聲明**。 您應該將部署至雲端的所有資產都關聯至一個計費單位 (應用程式/工作負載)，並符合命名標準。 此原則將確保未來的成本管理成果將會生效。

**設計選項**。 如需建立與未來技術的兼容性基礎相關資訊，請參閱 CAF 指引隨附之[可操作的設計指南](../journeys/overview.md)中，與建立治理 MVP 相關的討論。

## <a name="budget-overruns"></a>預算超支

**業務風險：** 自助服務部署會造成超支風險。

**原則聲明：** 所有雲端部署都必須配置給已核准預算的計費單位，以及適用於預算限制的機制。

**設計選項：** 在 Azure 中，可以使用 [Azure 成本管理](/azure/cost-management/manage-budgets)來控制預算。

## <a name="underutilization"></a>使用量過低

**業務風險：** 公司已針對雲端服務進行預付，或已承諾每年會花費特定金額。 這樣會有將用不到已達成協議之金額的風險，因而導致投資損失。

**原則聲明：** 每個具有已配置雲端預算的計費單位每年都將召開會議來設定預算、每季都會調整預算，而且每月都會配置時間來檢閱已規劃與實際的支出。 每月都要與計費單位的負責人討論任何大於 20% 的偏差。 基於追蹤目的，請將所有資產都指派給一個計費單位。

**設計選項：**

- 在 Azure 中，已規劃與實際的支出均可透過 [Azure 成本管理](/azure/cost-management/quick-acm-cost-analysis)來管理。
- 有數個選項可讓計費單位用來群組資源。 在 Azure 中，應該與治理小組一同選擇[資源一致性模型](../../decision-guides/resource-consistency/overview.md)並套用至所有資產。

## <a name="overprovisioned-assets"></a>過度佈建的資產

**業務風險：** 在傳統內部部署資料中心，常見的做法是部署已額外規劃容量的資產，以便在長遠的未來中隨之增長。 相較於傳統設備，雲端可以更快速地進行調整。 雲端中的資產也會根據技術容量來計費。 對於舊的內部部署做法，會有以人為方式誇大雲端支出的風險。

**原則聲明：** 所有部署至雲端的資產都必須在計劃中註冊，此計劃可以監視使用量，並報告任何超過使用量 50% 的容量。 所有部署至雲端的資產都必須以邏輯方式加以群組或標記，如此一來，治理小組成員就能讓工作負載擁有者參與關於過度佈建資產的任何最佳化。

**設計選項：**

- 在 Azure 中，[Azure Advisor](/azure/advisor/advisor-cost-recommendations) 可以提供最佳化建議。
- 有數個選項可讓計費單位用來群組資源。 在 Azure 中，應該與治理小組一同選擇[資源一致性模型](../../decision-guides/resource-consistency/overview.md)並套用至所有資產。

## <a name="overoptimization"></a>過度最佳化

**業務風險：** 有效的成本管理實際上可能會導致新風險。 將支出最佳化，正好與系統效能相反。 降低成本時，會造成過度削減支出，而產生不良使用者體驗的風險。

**原則聲明：** 所有直接影響客戶體驗的資產都必須透過群組或標記來識別。 將影響客戶體驗的任何資產最佳化之前，雲端治理小組必須根據大於或等於 90 天的使用量趨勢來調整最佳化。 記載在將資產最佳化時所考慮的任何季節性或事件驅動的高載。

**設計選項：**

- 在 Azure 中，[Azure 監視器的深入解析功能](/azure/azure-monitor/insights/vminsights-performance)有助於分析系統使用量。
- 有數個選項可根據角色來群組和標記資源。 在 Azure 中，您應該與治理小組一同選擇[資源一致性模型](../../decision-guides/resource-consistency/overview.md)，並將此項套用至所有資產。

## <a name="next-steps"></a>後續步驟

使用本文提及的範例做為起點，以開發與您雲端採用方案保持一致的原則來解決特定的業務風險。

若要開始自行開發與成本管理相關的自訂原則聲明，請下載[成本管理範本](template.md)。

若要加速採用這個專業領域，請選擇最密切配合您的環境且[可操作的治理旅程](../journeys/overview.md)。 然後修改設計，以納入您特定的公司原則決策。

> [!div class="nextstepaction"]
> [可操作的治理旅程](../journeys/overview.md)

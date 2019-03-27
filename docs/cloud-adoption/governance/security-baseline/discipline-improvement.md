---
title: CAF：安全性基準專業領域改進
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 安全性基準專業領域改進
author: BrianBlanchard
ms.openlocfilehash: 28a971f56c9f8ada1d184bdc1cb3dbb9a17c3507
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58246449"
---
# <a name="security-baseline-discipline-improvement"></a>安全性基準專業領域改進

安全性基準專業領域著重於如何建立適當原則來保護網路、資產，以及將放在雲端提供者解決方案上的資料；其中以最後一項最為重要。 在五個雲端治理專業領域中，安全性基準包含數位資產和資料的分類。 此外也包含與資料、資產和網路的安全性相關聯的風險、商業容忍度和風險降低策略的文件說明。 從技術觀點來看，其中也牽涉到以下項目的相關決策：[加密](../../decision-guides/encryption/overview.md)、[網路需求](../../decision-guides/software-defined-network/overview.md)、[混合式身分識別策略](../../decision-guides/identity/overview.md)，以及用於開發雲端安全性基準原則的[程序](compliance-processes.md)。

本文將概述一些貴公司可參與的潛在工作，以更好的方式來開發安全性基準專業領域並使其臻至成熟。 這些工作可以細分為實作雲端解決方案的規劃、建置、採用及操作階段，接著反覆執行以允許開發[雲端治理的累加方法](../journeys/overview.md#an-incremental-approach-to-cloud-governance)。

![四個採用階段](../../_images/adoption-phases.png)

*圖 1.雲端治理累加方法的採用階段。*

沒有任何一份文件能夠滿足所有企業需求。 因此，本文將針對治理成熟流程的每個階段，概述建議的最小和潛在範例活動。 這些活動的初始目的是協助您建置[原則 MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance)，並建立累加式原則演進的架構。 您的雲端治理小組將需決定要對這些活動進行多少投資，以改進您的安全性基準治理功能。

> [!CAUTION]
> 本文中概述的最小或潛在活動都不會與特定的公司原則或第三方合規性需求保持一致。 此指導方針旨在協助促成交談，從而使這兩個需求與雲端治理模型保持一致。

## <a name="planning-and-readiness"></a>規劃和整備

這個治理成熟度階段可消彌業務成果與可操作之策略間的鴻溝。 在此程序中，領導小組會定義特定的計量、將這些計量對應至數位資產，並開始規劃整體移轉工作。

**最小的建議活動：**

- 評估您的[安全性基準工具鏈](toolchain.md)選項。
- 開發架構指導方針文件的草稿，並散發給重要的專案關係人。
- 教育並涵蓋受到開發架構指導方針影響的人員和小組。
- 將已設定優先權的安全性工作新增至您的移轉待辦項目中。

**潛在的活動：**

- 定義資料分類結構描述。
- 進行數位資產規劃程序以清查涉及您商務程序並支持營運的目前 IT 資產。
- 進行[原則檢閱](../../governance/policy-compliance/what-is-a-cloud-policy-review.md)以開始將現有的公司 IT 安全性原則現代化並定義 MVP 原則以因應已知風險。
- 檢閱您雲端平台的安全性指導方針。 針對 Azure，您可以在 [Microsoft 服務信任平台](https://www.microsoft.com/trustcenter/stp/default.aspx)中找到相關資訊。
- 判斷您的安全性基準原則是否包括[安全性開發生命週期](https://www.microsoft.com/securityengineering/sdl/)。
- 根據接下來的一到三個版本評估網路、資料與資產相關商務風險，並判定您組織對那些風險的容忍度。
- 檢閱 Microsoft 的[熱門網路安全趨勢](https://www.microsoft.com/security/operations/security-intelligence-report) \(英文\) 報告以取得目前安全性趨勢概觀。
- 考慮在您的組織中開發[安全性 DevOps](https://www.microsoft.com/en-us/securityengineering/devsecops) 角色。

<!-- "en-us" location is required for the URL above. -->

## <a name="build-and-pre-deployment"></a>建置和部署前

成功移轉環境需要一些技術性和非技術性的先決條件。 此程序著重於可繼續進行移轉的決策、整備和核心基礎結構。

**最小的建議活動：**

- 在部署前階段中推出，以實作您的[安全性基準工具鏈](toolchain.md)。
- 更新架構指導方針文件，並散發給重要的專案關係人。
- 在已設定優先權的移轉待辦項目上實作安全性工作。
- 開發教育性資料和文件、認知溝通、獎勵和其他計畫，以協助試用產品的使用者採用。

**潛在的活動：**

- 決定您組織的雲端裝載資料[加密](../../decision-guides/encryption/overview.md)策略。
- 評估您雲端部署的[身分識別](../../decision-guides/identity/overview.md)策略。 決定您的雲端式身分識別解決方案將與內部部署身分識別提供者共存或整合。
- 決定您[軟體定義網路 (SDN)](../../decision-guides/software-defined-network/overview.md) 設計的網路界限原，以確保可以獲得安全的虛擬化網路功能。
- 評估您組織的[最低權限存取權](/azure/active-directory/users-groups-roles/roles-delegate-by-task)原則，然後使用工作型角色來提供對特定資源的存取權。
- 套用安全性與監視機制到所有雲端服務與虛擬機器。
- 在可能的情況下自動化[安全性原則](../../decision-guides/policy-enforcement/overview.md)。
- 檢閱您的安全性基準原則，並判斷是否需要根據最佳做法指導方針 (如[安全性基準生命週期](https://www.microsoft.com/securityengineering/sdl/)) 修改您的方案。

## <a name="adopt-and-migrate"></a>採用和移轉

移轉是一個累加式程序，著重於在現有的數位資產中移動、測試及採用應用程式或工作負載。

**最小的建議活動：**

- 將您的[安全性基準工具鏈](toolchain.md)從部署前環境移轉至生產環境。
- 更新架構指導方針文件，並散發給重要的專案關係人。
- 開發教育性資料和文件、認知溝通、獎勵和其他計畫，以協助試用產品的使用者採用

**潛在的活動：**

- 檢閱最新的安全性基準與威脅資訊以找出任何新的商務風險。
- 判斷您組織的容忍度以處理可能會發生的新安全性威脅。
- 找出來自原則的偏差，並強制進行修正。
- 調整安全性與存取控制自動化，以確保可以獲得最大的原則合規性。  
- 驗證在建置/部署前階段期間定義的最佳做法均已正確執行。
- 檢閱您的最低權限存取權原則並調整存取控制以獲得最大的安全性。
- 針對您的工作負載測試您的安全性基準工具鏈，以找出並解決任何弱點。

## <a name="operate-and-post-implementation"></a>操作和實作後

轉換完成之後，治理和操作必須依存於應用程式或工作負載的自然生命週期。 這個治理成熟度階段著重於通常會在實作解決方案且轉換週期開始穩定後隨之而來的活動。

**最小的建議活動：**

- 驗證和/或精簡您的[安全性基準工具鏈](toolchain.md)。
- 自訂通知與報告，以在發生潛在安全性問題時接收通知。
- 精簡架構指導方針，以引導未來的採用程序。
- 定期與受影響的小組溝通並教育他們，以確保會持續遵循架構指導方針。

**潛在的活動：**

- 探索您工作負載的模式與行為，並設定您的監視與報告工具，以偵測任何異常活動、存取或資源使用狀況並通知您。
- 持續更新您的監視與報告原則，以偵測最新的弱點、漏洞與攻擊。
- 備妥適當的程序，以快速停止未經授權存取並停用可能已被攻擊者入侵的資源。
- 定期檢閱最新的安全性最佳做法，並在可能的情況下套用建議到您的安全性原則、自動化與監視功能。

## <a name="next-steps"></a>後續步驟

現在您已了解雲端安全性治理的概念，請繼續深入了解 [Microsoft 為 Azure 提供哪些安全性與最佳做法](azure-security-guidance.md)。

> [!div class="nextstepaction"]
> [了解 Azure 的安全性指導方針](azure-security-guidance.md)
> [Azure 安全性簡介](/azure/security/azure-security)
> [了解記錄、報告與監視](../../decision-guides/log-and-report/overview.md)

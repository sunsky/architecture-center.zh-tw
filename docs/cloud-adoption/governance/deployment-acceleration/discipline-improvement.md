---
title: CAF：部署加速專業領域改進
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 部署加速專業領域改進
author: alexbuckgit
ms.openlocfilehash: 98192c777d8866efb01544737e8cabea6354c4d7
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243719"
---
# <a name="deployment-acceleration-discipline-improvement"></a>部署加速專業領域改進

部署加速專業領域著重於建立原則，以確保資源會部署並以一致且可重複的方式進行設定，並且在其整個生命週期持續保有合規性。 在這五個雲端治理專業領域中，部署加速包括與下列各項有關的決策：將部署自動化、控制原始檔的部署成品、監視已部署的資源以保有預期狀態，以及稽核任何合規性問題。

本文將概述一些貴公司可參與的潛在工作，以更好的方式來開發部署加速專業領域並使其臻至成熟。 這些工作可以細分為實作雲端解決方案的規劃、建置、採用及操作階段，接著反覆執行以允許開發[雲端治理的累加方法](../journeys/overview.md#an-incremental-approach-to-cloud-governance)。

![四個採用階段](../../_images/adoption-phases.png)

*圖 1.雲端治理累加方法的採用階段。*

沒有任何一份文件能夠滿足所有企業需求。 因此，本文將針對治理成熟流程的每個階段，概述建議的最小和潛在範例活動。 這些活動的初始目的是協助您建置[原則 MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance)，並建立累加式原則演進的架構。 您的雲端治理小組將需決定要對這些活動進行多少投資，以改進您的身分識別基準治理功能。

> [!CAUTION]
> 本文中概述的最小或潛在活動都不會與特定的公司原則或第三方合規性需求保持一致。 此指導方針旨在協助促成交談，從而使這兩個需求與雲端治理模型保持一致。

## <a name="planning-and-readiness"></a>規劃和整備

這個治理成熟度階段可消彌業務成果與可操作之策略間的鴻溝。 在此程序中，領導小組會定義特定的計量、將這些計量對應至數位資產，並開始規劃整體移轉工作。

**最小的建議活動：**

- 評估您的[部署加速工具鏈](toolchain.md)選項，並實作適用於貴組織的混合式策略。
- 開發架構方針文件的草稿，並散發給重要的專案關係人。
- 教育並涵蓋受到開發架構方針影響的人員和小組。
- 訓練開發小組和 IT 人員，使其了解 DevSecOps 準則和策略，以及在部署加速專業領域中將部署完全自動化的重要性。

**潛在的活動：**

- 定義將在雲端中治理部署加速的角色和指派。

## <a name="build-and-pre-deployment"></a>建置和部署前

**最小的建議活動：**

- 對於新的雲端式應用程式，在開發流程初期引進完全自動化的部署。 此投資將改進測試流程的可靠性，並確保開發、QA 和生產環境之間的一致性。
- 使用原始檔控制平台 (例如 GitHub 或 Azure DevOps) 來儲存所有部署成品 (例如部署範本或設定指令碼)。
- 實作您的[部署加速工具鏈](toolchain.md)之前，請考慮先執行試驗測試，以確定它會盡可能地簡化您的部署。 在部署前階段期間套用來自試驗測試的意見反應，必要時可重複執行。
- 評估應用程式的邏輯與實體架構，並找機會來將應用程式資源部署自動化，或使用其他雲端式資源改進架構的某些部分。
- 更新架構方針文件，以包含部署與使用者採用方案，並散發給重要的專案關係人。
- 繼續教育受架構方針影響最大的人員與小組。

**潛在的活動：**

- 定義持續整合和持續部署 (CI/CD) 管線，透過開發、QA 及生產環境完全管理發行應用程式更新的程序。

## <a name="adopt-and-migrate"></a>採用和移轉

移轉是一個累加式程序，著重於在現有的數位資產中移動、測試及採用應用程式或工作負載。

**最小的建議活動：**

- 將您的[部署加速工具鏈](toolchain.md)從開發環境移轉至生產環境。
- 更新架構方針文件，並散發給重要的專案關係人。
- 開發教育性資料和文件、認知溝通、獎勵和其他計畫，以協助試用產品的開發人員和 IT 採用。

**潛在的活動：**

- 驗證在建置和預先部署階段期間定義的最佳做法均會正確執行。
- 確定每個應用程式或工作負載在發行之前，都會與部署加速策略保持一致。

## <a name="operate-and-post-implementation"></a>操作和實作後

轉換完成之後，治理和操作必須依存於應用程式或工作負載的自然生命週期。 這個治理成熟度階段著重於通常會在實作解決方案且轉換週期開始穩定後隨之而來的活動。

**最小的建議活動：**

- 根據對於貴組織變更身分識別需求的變更，自訂您的[部署加速工具鏈](toolchain.md)。
- 將通知和報告自動化，以警示您潛在的設定問題或惡意威脅。
- 監視和報告應用程式與資源使用量。
- 報告部署後計量，並散發給專案關係人。
- 修訂架構方針，以引導未來的採用程序。
- 繼續溝通，並定期訓練受影響的人員和小組，以確保會持續遵循架構方針。

**潛在的活動：**

- 設定預期狀態設定監視和報告工具。
- 定期檢閱設定工具和指令碼，以改進流程並找出常見的問題。
- 與開發、作業及安全性小組合作，以使 DevSecOps 做法臻至成熟，並細分導致低效率的組織定址接收器。

## <a name="next-steps"></a>後續步驟

既然您已了解雲端身分識別治理的概念，請檢查[身分識別基準工具鏈](toolchain.md)，來識別您在 Azure 平台上開發身分識別基準治理專業領域時所需的 Azure 工具和功能。

> [!div class="nextstepaction"]
> [適用於 Azure 的身分識別基準工具鏈](toolchain.md)

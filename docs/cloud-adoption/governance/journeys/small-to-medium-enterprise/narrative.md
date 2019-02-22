---
title: CAF：中小型企業 - 治理策略背後的初始敘述
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 這個敘述會針對中小型企業治理旅程建立一個使用案例。
author: BrianBlanchard
ms.openlocfilehash: 6173b01f310169017761110d6641acfa51d45df8
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901188"
---
# <a name="small-to-medium-enterprise-the-narrative-behind-the-governance-strategy"></a>中小型企業作業：治理策略背後的敘述

下列敘述描述[中小型企業治理旅程](./overview.md)的使用案例。 實作旅程之前，請務必了解此敘述中反映的假設和推論。 然後您可以讓治理策略與貴組織的旅程更一致。

## <a name="back-story"></a>背景故事

董事會今年開始計劃以多種方式為企業注入活力。 他們將推動改善客戶體驗以獲得市場佔有率的領導地位。 他們也將推動新產品和服務，將公司定位為業界的思想領導者。 他們同時還盡力減少浪費並縮減不必要的成本。 雖然令人生畏，但董事會和領導階層的行動展現出這項工作將盡可能地將資金集中投入未來發展。

過去，公司的 CIO 對於這些策略對話並沒有發言的權利。 但是，未來的願景在本質上與技術發展息息相關，因此 IT 擁有一席之地，可以幫助指導這些重大計劃。 IT 現在應該以新的方式提供。 團隊並沒有為這些變化做好準備，而且可能會遭遇學習曲線的問題。

## <a name="business-characteristics"></a>商務特性

公司具備下列商務設定檔：

- 所有的銷售與營運都位於單一國家/地區，且全球客戶的百分比低。
- 企業當作單一業務單位營運，預算會隨著銷售、行銷、營運和 IT 等職責調整。
- 企業將大多數的 IT 視為資本流失或成本中心。

## <a name="current-state"></a>目前狀態

以下是該公司的 IT 和雲端作業目前的狀態：

- IT 負責運作兩個託管的基礎結構環境。 其中一個環境含有生產資產。 另一個環境則含有災害復原和一些開發/測試資產。 這些環境是由兩個不同的提供者所託管。 IT 將這兩個資料中心分別歸類為 Prod 和 DR。
- IT 將所有使用者電子郵件帳戶移轉至 Office 365，藉此進入雲端。 此移轉已在六個月前完成。 其他一些 IT 資產已部署至雲端。
- 應用程式開發小組致力於開發/測試能力，以了解雲端原生功能。
- 商業智慧 (BI) 小組將試驗雲端中的巨量資料，以及新平台上的資料鑑藏。
- 公司有一個定義鬆散的原則，規定無法在雲端託管客戶的個人識別資訊 (PII) 和財務資料，進而限制了目前部署中的關鍵任務應用程式。
- IT 投資主要由資本支出控制 (CapEx)。 這些投資每年規劃一次。 在過去幾年中，投資僅包含基本維護需求。

## <a name="future-state"></a>未來的狀態

預計未來幾年將發生下列變化：

- CIO 將審查 PII 和財務資料的相關原則，以實現未來的狀態目標。
- 應用程式開發和 BI 團隊希望在未來的 24 個月內，根據客戶業務開發和新產品的願景，將雲端式解決方案發佈到生產環境中。
- 今年，IT 團隊會將 2,000 個 VM 移轉至雲端，來完成淘汰 DR 資料中心的災難復原工作負載。 預計這將在未來五年內節省約 2500 萬美元的成本。
    ![內部部署成本與 Azure 成本相比，未來五年的回報率為 2500 萬美元](../../../_images/governance/calculator-small-to-medium-enterprise.png)
- 公司計劃將承諾的資本支出重新定位為 IT 內的營運支出 (OpEx)，藉此改變 IT 投資的方式。 這項改變將提供更好的成本控制，並使 IT 能夠加速完成其他計劃的工作。

## <a name="next-steps"></a>後續步驟

公司已經制訂一項公司原則使治理實施具體化。 公司原則推動了許多技術決策。

> [!div class="nextstepaction"]
> [檢閱最初的公司原則](./initial-corporate-policy.md)

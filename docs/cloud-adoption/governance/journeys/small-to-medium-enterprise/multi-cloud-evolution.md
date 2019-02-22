---
title: CAF：中小型企業 – 多重雲端演進
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 說明：中小型企業 – 多重雲端演進
author: BrianBlanchard
ms.openlocfilehash: a5b09c92acc4e165590b5a35827b88b0ca099bed
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901111"
---
# <a name="small-to-medium-enterprise-multi-cloud-evolution"></a>中小型企業：多重雲端演進

本文會藉由新增多重雲端採用的控制項來發展敘述。

## <a name="evolution-of-the-narrative"></a>敘述的演進

Microsoft 意識到客戶會基於特定用途來採用多個雲端。 這個旅程圖中的虛構客戶也不例外。 與 Azure 採用旅程圖並用，該企業成功收購了一家小型但互補的企業。 該企業正在不同的雲端提供者上執行其所有 IT 作業。

本文將說明整合新組織時所產生的變化。 基於敘述的目的，我們假設這家公司已完成此客戶旅程圖中所概述的每一個治理演進。

### <a name="evolution-of-the-current-state"></a>目前狀態的演進

在此敘述的上一個階段中，公司已經開始透過 CI/CD 管線主動將生產應用程式推送至雲端。

從那時起，某些將會影響治理的事項已經改變：

- 身分識別會由 Active Directory 的內部部署執行個體來控制。 透過複寫到 Azure Active Directory 來促成混合式身分識別。
- IT 作業或雲端作業主要會由 Azure 監視器與相關的自動化來管理。
- 災害復原 / 商務持續性會由 Azure Vault 執行個體來控制。
- Azure 資訊安全中心可用來監視安全性違規和攻擊。
- Azure 資訊安全中心和 Azure 監視器可同時用來監視雲端治理。
- Azure 藍圖、Azure 原則和 Azure 管理群組可用來對原則進行合規性自動化。

### <a name="evolution-of-the-future-state"></a>未來狀態的演進

目標是盡可能將收購公司整合至現有的作業。

## <a name="evolution-of-tangible-risks"></a>實質風險的演進

**企業收購成本**：收購新企業，預計大約將在五年內獲利。 由於報酬率偏低，因此，董事會希望盡可能地控制收購成本。 這會產生使成本控制和技術整合彼此衝突的風險。

此業務風險可能會延伸出少數技術風險：

- 雲端移轉可能會產生額外的收購成本
- 無法適當治理新環境，可能導致違反原則的事件。

## <a name="evolution-of-the-policy-statements"></a>原則聲明的演進

下列原則變更有助於減緩新風險和指導實作。

1. 次要雲端中的所有資產都必須透過現有的操作管理和安全性監視工具來監視
2. 所有組織單位都必須整合到現有的識別提供者
3. 主要識別提供者應治理次要雲端中的資產驗證

## <a name="evolution-of-the-best-practices"></a>最佳做法的演進

本文的這一節將推演治理 MVP 設計，以包含新的 Azure 原則和實作 Azure 成本管理。 這兩個設計變更將共同實現新的公司原則聲明。

1. 網路連線。 此步驟由網路和 IT 安全性小組執行，並由雲端治理小組支援。 新增從 MPLS/租用線路提供者到新雲端的連線，將會整合網路。 新增路由表和防火牆設定，將控制環境之間的存取與流量。
2. 合併識別提供者。 根據次要雲端中所裝載的工作負載而定，有各種不同選項可用於合併識別提供者。 以下是一些範例：
    1. 對於使用 OAuth 2 進行驗證的應用程式，次要雲端上 Active Directory 中的使用者可能只會複製寫到現有的 Azure AD 租用戶。 這可確保所有使用者在租用戶中進行驗證。
    2. 另一個極端情況是，同盟會讓 OU 進入 Active Directory 內部部署環境，然後再到 Azure AD 執行個體。
3. 將資產新增至 Azure Site Recovery。
    1. Azure Site Recovery 是以混合式/多重雲端工具的形式從頭開始設計。
    2. 次要雲端中的 VM 可能會受到用來保護內部部署資產的相同 Azure Site Recovery 流程所保護。
4. 將資產新增至 Azure 成本管理
    1. Azure 成本管理是以多重雲端工具的形式從頭開始設計。
    2. 次要雲端中的虛擬機器可能會與某些雲端提供者的 Azure 成本管理相容。 可能需要額外成本。
5. 將資產新增至 Azure 監視器。
    1. Azure 監視器是以混合式雲端工具的形式從頭開始設計。
    2. 次要雲端中的虛擬機器可能會與 Azure 監視器代理程式相容，好讓虛擬機器包含於 Azure 監視器以進行作業監視。
6. 加強治理工具：
    1. 加強治理為雲端特有。
    2. 在治理旅程圖中所建立的公司原則不是雲端特有。 儘管實作可能因雲端而異，但原則還是能夠套用到次要提供者。

隨著多重雲端採用的成長，上述設計演進將會逐漸成熟。

## <a name="conclusion"></a>結論

這一系列敘述治理最佳做法演進的文章，都會配合著此虛構公司的體驗進行。 藉由從小規模開始 (但使用正確的基礎)，公司可以快速移動，而且還可以在對的時間套用對的治理方法。 MVP 本身不會保護客戶。 而是建立降低風險及新增保護所需的基礎。 您可以從該處套用治理層級來降低實質風險。 此處提供的確切旅程不會 100% 符合讀者的體驗。 但是，可以用此模式來累加治理方式。 建議讀者將這些最佳做法塑造成符合其獨特限制和治理需求的樣子。
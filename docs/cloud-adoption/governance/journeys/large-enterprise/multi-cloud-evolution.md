---
title: CAF：大型企業 – 多重雲端演進
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 大型企業 – 多重雲端演進
author: BrianBlanchard
ms.openlocfilehash: 5ef29aa523c04ff93b2d4f983482f94654a4a039
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900988"
---
# <a name="large-enterprise-multi-cloud-evolution"></a>大型企業：多重雲端演進

## <a name="evolution-of-the-narrative"></a>敘述的演進

Microsoft 意識到客戶會基於特定用途來採用多個雲端。 這個旅程圖中的虛構公司也不例外。 與 Azure 採用旅程圖並用，該企業成功收購了一家小型但互補的企業。 該企業正在不同的雲端提供者上執行其所有 IT 作業。

本文將說明整合新組織時所產生的變化。 基於敘述的用途，我們假設這家公司已完成此客戶旅程圖中所概述的每一個治理演進。

### <a name="evolution-of-the-current-state"></a>目前狀態的演進

在此敘述的上一個階段，隨著雲端支出成為公司正常營運費用的一部分，該公司已經開始實作成本控制和成本監視。

從那時起，某些將會影響治理的事項已經改變：

- 身分識別會由 Active Directory 的內部部署執行個體來控制。 透過複寫到 Azure Active Directory 來促成混合式身分識別。
- IT 作業或雲端作業主要會由 Azure 監視器與相關的自動化來管理。
- 災害復原 / 商務持續性會由 Azure Vault 執行個體來控制。
- Azure 資訊安全中心可用來監視安全性違規和攻擊。
- Azure 資訊安全中心和 Azure 監視器可同時用來監視雲端治理。
- Azure 藍圖、Azure 原則和管理群組可用來對原則進行合規性自動化。

### <a name="evolution-of-the-future-state"></a>未來狀態的演進

目標是盡可能將收購公司整合至現有的作業。

## <a name="evolution-of-tangible-risks"></a>實質風險的演進

**企業收購成本**：收購新企業，預計大約將在五年內獲利。 由於報酬率偏低，因此，董事會希望盡可能地控制收購成本。 這會產生使成本控制和技術整合彼此衝突的風險。

此業務風險可能會延伸出少數技術風險

- 有個雲端移轉的風險是產生額外的收購成本。
- 此外，若新環境未正確治理或導致違反原則，則會產生另一個風險。

## <a name="evolution-of-the-policy-statements"></a>原則聲明的演進

下列原則變更有助於降低新風險和指導實作。

1. 次要雲端中的所有資產都必須透過現有的操作管理和安全性監視工具來監視。
2. 所有組織單位都必須整合到現有的識別提供者。
3. 主要的識別提供者應該治理對次要雲端中資產的驗證。

## <a name="evolution-of-the-best-practices"></a>最佳做法的演進

本文的這一節將推演治理 MVP 設計，以包含新的 Azure 原則和實作 Azure 成本管理。 這兩個設計變更將共同實現新的公司原則聲明。

1. 連線網路：透過網路功能與 IT 安全性來執行 (由治理提供支援)
    1. 新增從 MPLS/租用線路提供者到新雲端的連線，將會整合網路。 新增路由表和防火牆設定，將控制環境之間的存取與流量。
2. 合併識別提供者。 根據次要雲端中所裝載的工作負載而定，有各種不同選項可用於合併識別提供者。 以下是一些範例：
    1. 對於使用 OAuth 2 進行驗證的應用程式，可能只會將次要雲端上 Active Directory 中的使用者複製寫到現有的 Azure AD 租用戶。
    2. 另一方面，這兩個內部部署識別提供者之間的同盟，可讓使用者從新的 Active Directory 網域複寫到 Azure。
3. 將資產新增至 Azure Site Recovery
    1. 一開始已從混合式/多重雲端工具建置 Azure Site Recovery。
    2. 次要雲端中的虛擬機器可能會受到用來保護內部部署資產的相同 Azure Site Recovery 流程所保護。
4. 將資產新增至 Azure 成本管理
    1. 一開始已從多重雲端工具建置 Azure 成本管理。
    2. 次要雲端中的虛擬機器可能會與某些雲端提供者的 Azure 成本管理相容。 可能需要額外成本。
5. 將資產新增至 Azure 監視器
    1. 一開始已從混合式雲端工具建置 Azure 監視器。
    2. 次要雲端中的虛擬機器可能會與 Azure 監視器代理程式相容，能夠將它們包含於 Azure 監視器以進行操作監控。
6. 加強治理工具
    1. 加強治理為雲端特有。
    2. 在治理旅程圖中所建立的公司原則不是。 儘管實作可能因雲端而異，但原則聲明還是能夠套用到次要提供者。

隨著多重雲端採用的成長，上述設計演進將會逐漸成熟。

## <a name="next-steps"></a>後續步驟

在許多大型企業中，雲端治理的專業領域可能會成為採用的阻礙物。 下一篇文章將提供一些與使治理成為小組運動密切相關的想法，可協助確保雲端中的長期成功。

> [!div class="nextstepaction"]
> [多層治理](./multiple-layers-of-governance.md)

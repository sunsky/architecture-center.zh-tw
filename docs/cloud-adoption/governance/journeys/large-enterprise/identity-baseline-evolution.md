---
title: CAF：大型企業 – 身分識別基準演進
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 大型企業 – 身分識別基準演進
author: BrianBlanchard
ms.openlocfilehash: efda14819bfbc70632dc9bb8b4c6c5aca96004c0
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900879"
---
# <a name="large-enterprise-identity-baseline-evolution"></a>大型企業：身分識別基準演進

本文藉由將身分識別基準控制新增至治理 MVP 來推演敘述。

## <a name="evolution-of-the-narrative"></a>敘述的演進

CFO 已核准將兩個資料中心移轉至雲端的商業論證。 在研究技術可行性的期間，其找到幾個障礙：

- 受保護的資料及任務關鍵性應用程式佔了兩個資料中心 25% 的工作負載。 在將目前有關 PII 和任務關鍵性應用程式的治理原則現代化之前，這兩種工作負載都無法消除。
- 在這兩個資料中心內，7% 的資產與雲端不相容。 其會先移至替代的資料中心，再終止資料中心的合約。
- 資料中心內有 15% 的資產 (750 個虛擬機器) 相依於舊式的驗證或第三方多重要素驗證。
- 連結現有資料中心與 Azure 的 VPN 連線未提供足夠的資料傳輸速度或延遲，因而無法在兩年內遷移大量資產，從而淘汰資料中心。

前兩個障礙將會一起排除。 本文會講述第三個和第四個障礙的解決方案。

### <a name="evolution-of-the-cloud-governance-team"></a>雲端治理小組的演進

雲端治理小組不斷擴大。 由於需要另外支援身分識別管理，來自身分識別基準小組的系統管理員現在會參與每週一次的會議，好讓現有的小組成員了解有何改變。

### <a name="evolution-of-the-current-state"></a>目前狀態的演進

公司已核准 IT 小組進行 CIO 和 CFO 想要將兩個資料中心淘汰的計劃。 不過，IT 有所顧慮，因為這些資料中心內有 750 個 (15%) 的資產必須移到雲端以外的地方。

### <a name="evolution-of-the-future-state"></a>未來狀態的演進

新的未來狀態計劃需要準備更強固的身分識別基準解決方案，以便遷移需要使用舊式驗證的 750 個虛擬機器。 除了這兩個資料中心外，其他資料中心內也有類似百分比的資產應該會受到這項挑戰所影響。
未來的狀態現在也需要有從雲端提供者到公司的 MPLS/專線解決方案的連線。

目前和未來狀態的變更會產生新風險，需要新的原則聲明。

## <a name="evolution-of-tangible-risks"></a>有形風險的演進

**移轉期間業務中斷**。 移轉至雲端時，會產生受控而可以管理的具時效性風險。 將老舊硬體移至世界上的其他地方，風險會更高。 為了避免中斷業務運作，其需要緩解策略。

**現有身分識別相依性**。 對現有驗證和身分識別服務的相依性可能會延遲或阻礙將某些工作負載移轉至雲端的工作。 若未能讓這兩個資料中心準時恢復上線，將會產生好幾百萬美元的資料中心租賃費用。

此業務風險可能會延伸出少數技術風險：

- 舊式驗證可能無法在雲端中使用，而限制了某些應用程式的部署。
- 目前的第三方 MFA 解決方案可能無法在雲端中使用，而限制了某些應用程式的部署。
- 不論是更換工具還是移動，都可能會產生中斷而增加成本。
- VPN 的速度和穩定性可能會妨礙移轉。
- 進入雲端的流量可能會讓全球網路的其他部分產生安全性問題。

## <a name="evolution-of-the-policy-statements"></a>原則聲明的演進

下列原則變更有助於減緩新風險和指導實作。

1. 所選擇的雲端提供者必須提供可透過舊式方法進行驗證的途徑。
2. 所選擇的雲端提供者必須提供可使用目前的第三方 MFA 解決方案進行驗證的途徑。
3. 應該在雲端提供者與公司的電信提供者之間建立高速的私人連線，以將雲端提供者連線至全球的資料中心網路。
4. 在建立好足夠的安全性需求之前，不得讓輸入的公用流量存取裝載在雲端的公司資產。 所有連接埠都要封鎖位於全域 WAN 之外的來源。

## <a name="evolution-of-the-best-practices"></a>最佳做法的演進

治理 MVP 設計會演進為在虛擬機器上包含新的 Azure 原則和 Active Directory 實作。 這兩個設計變更會共同實現新的公司原則聲明。

以下是新的最佳做法：

1. DMZ 藍圖：DMZ 的內部部署端應該設定為允許下列解決方案與內部部署的 Active Directory 伺服器進行通訊。 這個最佳做法需要讓 DMZ 跨網路界限啟用 Active Directory Domain Services。
2. Azure Resource Manager 範本：
    1. 定義 NSG 來封鎖外部流量以及將內部流量加入白名單。
    1. 根據最佳映像將兩個 AD 虛擬機器部署成具有負載平衡功能的組合。 在首次開機時，該映像會執行 PowerShell 指令碼，來加入網域並向網域服務註冊。 如需詳細資訊，請參閱[將 Active Directory Domain Services (AD DS) 擴充至 Azure](../../../../reference-architectures/identity/adds-extend-domain.md)。
3. Azure 原則：將 NSG 套用至所有資源。
4. Azure 藍圖
    1. 建立名為 `active-directory-virtual-machines` 的藍圖。
    1. 將每個 AD 範本和原則新增至藍圖中。
    1. 將藍圖發行至任何適用的管理群組中。
    1. 將藍圖套用至任何需要舊式或第三方 MFA 驗證的訂用帳戶。
    1. 在 Azure 中執行的 AD 執行個體現在會作為內部部署 AD 解決方案的延伸，而能夠與現有 MFA 工具整合，並提供宣告式的驗證，兩者皆透過現有 Active Directory 功能來達成。

## <a name="conclusion"></a>結論

將這些變更新增至治理 MVP 有助於緩解本文所述的許多風險，而讓每個雲端採用小組能夠快速地越過此一障礙。

## <a name="next-steps"></a>後續步驟

隨著雲端採用持續演進並提供額外的商業價值，風險和雲端治理需求也需要與時俱進。 以下是一些可能發生的演進。 對於這趟旅程中的虛構公司來說，下一個觸發程序是在雲端採用計劃中納入受保護的資料。 這項變更會需要額外的安全性控制。

> [!div class="nextstepaction"]
> [安全性基準演進](./security-baseline-evolution.md)

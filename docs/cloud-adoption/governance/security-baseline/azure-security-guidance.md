---
title: CAF：Azure 安全性指引
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Microsoft 提供哪些安全性指引？
author: BrianBlanchard
ms.openlocfilehash: 845b02422e2df63425e29acdfd9b43b744934c9f
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241929"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-security-guidance-does-microsoft-provide"></a>Microsoft 提供哪些安全性指引？

## <a name="security-guidance-and-tools"></a>安全性指引和工具

Microsoft 推出[服務信任平台](https://servicetrust.microsoft.com)和合規性管理員以協助處理下列事項：

- 克服合規性管理挑戰
- 滿足符合法規需求的責任
- 進行企業雲端服務使用量的自助稽核和風險評量

這些工具的設計目的是協助組織在選擇及使用 Microsoft Cloud 服務時，達成複雜的合規性義務，並改善資料保護功能。

**服務信任平台 (STP)** 提供深度資訊和工具，協助您符合使用 Microsoft Cloud 服務的需求，包括 Azure、Office 365、Dynamics 365 和 Windows。 STP 是與 Microsoft Cloud 相關的一站式安全性、法規、合規性及隱私權資訊商店。 我們在這裡發佈執行雲端服務和工具自助風險評量所需的資訊和資源。 STP 是建立來協助追蹤 Azure 內的法規合規性活動，包括：

- **合規性管理員**：合規性管理員是 Microsoft 服務信任平台中的工作流程型風險評量工具，可讓您追蹤、指派和確認與 Microsoft Cloud 服務 (例如 Office 365、Dynamics365 和 Azure) 相關的貴組織法規合規性活動。 您可以在下一節找到更多詳細資料。
- **信任文件**：目前有三種類別的指南，提供您豐富的資源以評估 Microsoft Cloud，深入了解 Microsoft 在安全性、合規性和隱私權方面的作業，以及協助您改善您的資料保護功能。 其中包含：
- **稽核報告**：稽核報告可讓您掌握 Microsoft Cloud 服務最新的隱私權、安全性和合規性相關資訊。 包括 ISO、SOC、FedRAMP 和其他稽核報告、橋接字母和獨立第三方稽核 Microsoft Cloud 服務 (例如 Azure、Office 365、Dynamics 365 等等) 的相關資料。
- **資料保護指南**：資料保護指南提供有關 Microsoft Cloud 服務如何保護您的資料，以及您如何為貴組織管理雲端資料安全性與合規性的相關資訊。 包括深入白皮書，提供 Microsoft 如何設計和操作雲端服務的詳細資料、常見問題集、年終安全性評量報告、滲透測試結果，以及可協助您進行風險評量並改善資料保護功能的指引。
- **Azure 安全性與合規性藍圖**：藍圖提供資源來協助您建置和啟動雲端驅動的應用程式，協助您遵守嚴格的規範及標準。 具有比其他任何雲端提供者更多的認證，您可以有信心地將關鍵工作負載部署至 Azure，使用包含下列項目的藍圖：

- 產業特定概觀和指引
- 客戶責任對照表
- 具有威脅模型的參考架構
- 控制實作對照表
- 自動化部署參考架構
- 隱私權資源 – 提供資料保護影響評量、資料主體要求 (DSR) 以及資料缺口通知的文件以併入您自己的權責程式，支援一般資料保護規定 (GDPR)。

- **開始使用 GDPR**：Microsoft 產品和服務可協助組織符合 GDPR 需求，同時收集或處理個人資料。 STP 的設計目的是給予您 Microsoft 服務中可用來解決 GDPR 特定需求之功能的相關資訊。 文件可協助您的 GDPR 權責和了解技術和組織措施。 提供資料保護影響評量、資料主體要求 (DSR) 以及資料缺口通知的文件以併入您自己的權責程式，支援 GDPR。
  - **資料主體要求**：GDPR 授與個人 (或資料主體) 與處理其個人資料相關的特定權限。 包括修正不正確資料、清除資料或限制其處理的權限，以及接收其資料和履行將資料傳送給另一個控制器之要求的權限。
  - **資料缺口**：GDPR 會在個人資料有缺口的事件中，要求資料控制器和處理器的通知需求。 STP 提供您 Microsoft 起先如何嘗試防止缺口、Microsoft 如何偵測缺口以及 Microsoft 如何回應缺口事件並且以資料控制器身分通知您的相關資訊。
  - **資料保護影響評量**：Microsoft 協助控制器完成 GDPR 資料保護影響評量。 GDPR 提供詳盡的案例清單，其中 DPIA 必須執行，例如針對分析和類似活動目的的自動化處理、處理大規模特殊類別個人資料，以及大規模系統化監視可公開存取的區域。
  - **其他資源**：除了以上各節中討論的工具指引，STP 也提供其他資源，包括區域合規性、安全性與相容性中心的其他資源及服務信任平台、合規性管理員和隱私權/GDPR 相關的常見問題集。
- **區域合規性**：STP 提供許多 Microsoft 線上服務的合規性文件和指引，以符合不同區域的合規性需求，包括捷克共和國、波蘭和羅馬尼亞。

## <a name="unique-intelligent-insights"></a>唯一的 Intelligent Insights

隨著安全性訊號的數量和複雜度成長，判斷這些訊號是否為可信的威脅然後再做反應，會耗用太多時間。 Microsoft 提供在雲端傳遞，無與倫比廣度的安全性智慧，協助快速偵測和修復威脅。

## <a name="azure-threat-intelligence"></a>Azure 威脅情報

使用資訊安全中心可用的威脅情報選項，IT 系統管理員可以識別對環境的安全性威脅。 例如，識別特定的電腦是否屬於殭屍網路。 如果攻擊者偷偷地安裝惡意程式碼，暗中將此電腦連接到命令和控制項，則電腦可能會成為殭屍網路中的節點。 威脅情報也可以識別來自地下通訊通道 (例如暗網) 的潛在威脅。

為了建置此威脅情報，資訊安全中心會使用來自 Microsoft 內多個來源的資料。 資訊安全中心將會使用這項資料來識別您環境的潛在威脅。 [威脅情報] 窗格是由三個主要選項所組成︰

- 偵測到的威脅類型
- 威脅來源
- 威脅情報對應

## <a name="machine-learning-in-azure-security-center"></a>Azure 資訊安全中心的機器學習

Azure 資訊安全中心深入分析豐富的資料，這些資料來自 Microsoft 和各個合作夥伴解決方案，協助您達成更高的安全性。 為了利用這項資料，公司使用資料科學與機器學習服務來進行威脅預防、偵測及最終調查。

廣義來說，Azure Machine Learning 可協助達到兩個結果：

## <a name="next-generation-detection"></a>新一代偵測

攻擊者越來越自動化及複雜。 他們也使用資料科學。 他們會對保護進行反向工程，並建置系統以支援行為的突變。 它們會將其活動偽裝成雜訊，並從錯誤快速學習。 機器學習可協助我們回應這些發展。

## <a name="simplified-security-baseline"></a>簡化的安全性基準

進行有效率的安全性決策並不容易。 需要安全性經驗和專業知識。 雖然某些大型組織的人員有這方面的專業知識，但是許多公司都沒有。 Azure Machine Learning 可讓客戶在進行安全性決策時，受益於其他組織的智慧。

## <a name="behavioral-analytics"></a>行為分析

行為分析是一種可分析及比較資料與一組已知模式的技術。 不過，這些模式並非簡單的簽章。 它們會透過已套用至大型資料集的複雜機器學習演算法來決定。 它們也能透過專業分析師仔細分析惡意行為來判定。 Azure 資訊安全中心可以使用行為分析，根據虛擬機器記錄、虛擬網路裝置記錄、網狀架構記錄、毀損傾印和其他來源的分析，來識別遭到入侵的資源。

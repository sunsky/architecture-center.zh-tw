---
title: Azure 虛擬資料中心：網路觀點
description: 了解如何在 Azure 中建置虛擬資料中心
author: tracsman
manager: rossort
tags: azure-resource-manager
ms.topic: guide
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: virtual-network
ms.date: 11/28/2018
ms.author: jonor
ms.openlocfilehash: 1f61996d231f3bf0cc2c550f4d3e119116bb7bc0
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54488642"
---
# <a name="azure-virtual-datacenter-a-network-perspective"></a>Azure 虛擬資料中心：網路觀點

## <a name="overview"></a>概觀

將內部部署應用程式移轉至 Azure 可讓組織受益於安全且具成本效益的基礎結構，而且遷移應用程式時幾乎不需要進行變更。 不過，若要讓雲端運算具有最大靈活度，企業必須改進其架構，以充分利用 Azure 功能。 

Microsoft Azure 提供超大規模的服務和基礎結構，並且具有企業級功能和可靠性。 這些服務和基礎結構會提供許多混合式連線的選擇，讓客戶可以選擇透過公用網際網路或私人 Azure ExpressRoute 連線來存取這些服務和基礎結構。 Microsoft 夥伴也提供增強的功能，方法是提供最適合在 Azure 中執行的安全性服務和虛擬設備。

客戶可以選擇透過網際網路或透過 Azure ExpressRoute (提供私人網路連線) 來存取這些雲端服務。 透過 Microsoft Azure 平台，客戶可順暢地將基礎結構延伸至雲端並建置多層式架構。 Microsoft 夥伴也提供增強的功能，方法是提供最適合在 Azure 中執行的安全性服務和虛擬設備。

## <a name="what-is-the-virtual-datacenter"></a>什麼是虛擬資料中心？

一開始，雲端基本上是代管公用端應用程式的平台。 企業開始了解雲端的價值，並開始將內部企業營運應用程式遷移至雲端。 這類應用程式帶來額外的安全性、可靠性、效能和成本考量，因此在提供雲端服務上需要額外的彈性。 此契機成就了提供此彈性的新基礎結構和網路服務，也成就了用於規模調整、災害復原和其他考量的新功能。

## <a name="what-is-a-virtual-datacenter"></a>什麼是虛擬資料中心？
雲端解決方案早先設計成在公用頻譜中裝載相對隔離的單一應用程式。 這種方法已良好運作多年。 其後，雲端解決方案的效益日漸顯著，開始有人在雲端上裝載多個大規模工作負載。 在雲端服務的整個生命週期處理一或多個區域中的安全性、可靠性、效能和部署成本考量，變得十分重要。

下列雲端部署的**紅色方塊**顯示安全性缺口的範例。 **黃色方塊**顯示工作負載的最佳化網路虛擬設備空間。

[![0]][0]

虛擬資料中心 (VDC) 是為了支援企業工作負載而必須進行調整所產生的概念，而且還需要處理在公用雲端中支援大規模應用程式時所產生的問題。

VDC 實作不只代表雲端中的應用程式工作負載，也是網路、安全性、管理和基礎結構 (例如，DNS 和目錄服務)。 有越來越多的企業工作負載移至 Azure 時，考慮支援基礎結構以及這些工作負載所在的物件就變得很重要。 謹慎考量資源建構方式可以避免激增數百個「工作負載島」，而工作負載島必須使用獨立資料流程、安全性模型和相容性挑戰進行分開管理。

VDC 概念是建議和最佳做法的集合，目的是以常見的支援函式、功能及基礎結構來實作一批不同但相關的實體。 透過 VDC 的角度來檢視工作負載，您可以了解規模經濟帶來的成本降低、透過元件和資料流程集中化而最佳化的安全性，以及更容易的操作、管理和合規性稽核。

> [!NOTE]
> 請務必了解 VDC **不**是離散 Azure 產品，而是各種功能的組合，用來達到您實際的需求。 VDC 是考慮工作負載和 Azure 使用量以最佳化您雲端中資源和能力的方式。 這是在 Azure 中建置 IT 服務的模組化方法，同時還能顧及企業的組織角色和責任。

VDC 實作可協助企業在下列情況下，將工作負載和應用程式放入 Azure：

-   裝載多個相關工作負載。
-   將工作負載從內部部署環境移轉至 Azure。
-   實作工作負載的共用或集中式安全性和存取需求。
-   適當地針對大型企業混合使用 Azure DevOps 和集中式 IT。

集中式中樞和輪輻網路拓樸，以及 Azure 服務和功能的混合使用，是 VDC 充分展現其優勢的關鍵：

* [Azure 虛擬網路][VNet]、
* [網路安全性群組][NSG]、
* [虛擬網路對等互連][VNetPeering]、
* [使用者定義路由 (UDR)][UDR]，以及
* 使用[角色型存取控制 (RBAC)][RBAC] 的 Azure 身分識別，還有選用的 [Azure 防火牆][AzFW]、[Azure DNS][DNS]、[Azure Front Door][AFD] 和 [Azure 虛擬 WAN][vWAN]。

## <a name="who-should-implement-a-virtual-datacenter"></a>哪些人應實作虛擬資料中心？

任何已決定要採用雲端的 Azure 客戶，皆可因為設定一組所有應用程式常用的資源，而受益於其所帶來的效率。 根據範圍，甚至單一應用程式也可以受益於使用用來建置 VDC 實作的模式和元件。

如果您的組織具有集中式 IT、網路、安全性和 (或) 合規性小組/部門，則實作 VDC 有助於強制執行原則點、職責區隔，並確保基礎一般元件的一致性，同時讓應用程式小組適當地具有與您需求一樣多的自由和控制。

正在嘗試 DevOps 的組織也可以利用 VDC 概念來提供數個授權部分的 Azure 資源，並確保它們擁有該群組內的整體控制 (一般訂用帳戶中的訂用帳戶或資源群組)，但網路和安全性界限會保持相容，如中樞 VNet 和資源群組中的集中式原則所定義。

## <a name="considerations-for-implementing-a-virtual-datacenter"></a>實作虛擬資料中心的考量

設計 VDC 實作時，有數個關鍵問題需要考量：

### <a name="identity-and-directory-service"></a>身分識別和目錄服務

身分識別服務和目錄服務是內部部署和雲端中所有資料中心的一個關鍵層面。 身分識別是與 VDC 實作內服務存取和授權的所有層面有關。 為了協助確保只有授權使用者和處理序才能存取您的 Azure 帳戶和資源，Azure 會使用數種類型的認證進行驗證。 其中包括密碼以存取 Azure 帳戶、密碼編譯金鑰、數位簽章和憑證。 [Azure Multi-Factor Authentication (MFA)][MFA] 是存取 Azure 服務的額外安全層。 Azure MFA 透過多種簡易的驗證選項提供強大的驗證，包括電話、簡訊或行動應用程式通知，讓客戶選擇自己喜歡的方式。

任何大型企業都需要定義身分識別管理程序，以描述其 VDC 實作內或跨 VDC 的個別身分識別、其驗證、授權、角色和權限管理。 此程序的目標應該是提高安全性與生產力，同時降低成本、停機時間和重複性手動工作。

企業/組織可能需要依需求混合使用不同企業營運 (LOB) 的服務，而且員工通常在涉及不同專案時會有不同角色。 使用 VDC 時，各具有特定角色定義的不同小組之間必須密切合作，使系統在妥善的控管下執行。 責任、存取權和權限的矩陣可能會很複雜。 VDC 中的身分識別管理會透過 [Azure Active Directory (Azure AD)][AAD] 和角色型存取控制 (RBAC) 進行實作。

目錄服務是一種共用資訊基礎結構，用以尋找、管理和組織日常項目和網路資源。 這些資源可能包含磁碟區、資料夾、檔案、印表機、使用者、群組、裝置和其他物件。 目錄伺服器會將網路上的每個資源都視為物件。 資源的相關資訊會儲存為與該資源或物件建立關聯的屬性集合。

所有 Microsoft Online 商務服務都依賴 Azure Active Directory (Azure AD) 來進行登入和其他身分識別需求。 Azure Active Directory 是全方位、高可用性的身分識別和存取管理的雲端解決方案，它結合了核心目錄服務、進階身分識別管制及應用程式存取管理。 Azure AD 可以與內部部署 Active Directory 整合，以啟用所有雲端式和本機裝載 (內部部署) 應用程式的單一登入。 內部部署 Active Directory 的使用者屬性可以自動同步至 Azure AD。

VDC 實作中的所有權限不一定要由單一全域管理員指派。 相反地，每個特定部門、使用者群組或目錄服務中的服務都可以有管理其在 VDC 實作內專屬資源所需的權限。 建構權限需要平衡。 權限太多可能會阻礙效能效率，而權限太少或鬆散可能會增加安全性風險。 Azure 角色型存取控制 (RBAC) 可以為 VDC 實作中的資源提供更細緻的存取管理，以協助解決這個問題。

#### <a name="security-infrastructure"></a>安全性基礎結構

安全性基礎結構是指在 VDC 實作的特定虛擬網路區段中區隔流量。 此基礎結構會指定在 VDC 實作中控制輸入和輸出的方式。 Azure 以多租用戶架構為基礎，可使用虛擬網路 (VNet) 隔離、存取控制清單 (ACL)、負載平衡器、IP 篩選及流量流程原則來防止部署之間的未經授權和意外流量。 網路位址轉譯 (NAT) 會區隔內部網路流量與外部流量。

Azure 網狀架構會將基礎結構資源分配給租用戶工作負載，並管理與虛擬機器 (VM) 之間的通訊。 Azure Hypervisor 會強制執行 VM 之間的記憶體和處理序區隔，並安全地將網路流量路由傳送至客體 OS 租用戶。

#### <a name="connectivity-to-the-cloud"></a>雲端的連線

VDC 實作需要與外部網路的連線，才能將服務提供給客戶、合作夥伴和 (或) 內部使用者。 這不僅需要連線至網際網路，也需連線至內部部署網路和資料中心。

客戶可使用 [Azure 防火牆][AzFW]或其他類型的虛擬網路設備 (NVA) 來控制哪些服務可與公用網際網路相互存取、使用[使用者定義的路由][UDR]來自訂路由原則，以及使用[網路安全性群組][NSG]進行網路篩選。 我們建議，所有面向網際網路的資源也都應受到 [Azure DDoS 保護標準][DDOS]的保護。

企業可能需要將其 VDC 實作連線至內部部署資料中心或其他資源。 設計有效架構時，Azure 與內部部署網路之間的此連線是重要層面。 企業有兩種不同的方式可建立此互相連線：透過網際網路和 (或) 私人直接連線的傳輸。

[**Azure 站對站 VPN**][VPN] 是內部部署網路與 VDC 實作之間透過網際網路的互相連線服務，並透過安全加密連線 (IPsec/IKE 通道) 所建立。 Azure 站對站連線具彈性且更快速建立，而且不需要任何進一步採購，因為所有連線都是透過網際網路連線。

對於大量 VPN 連線，[**Azure 虛擬 WAN**][vWAN] 可作為網路服務，透過 Azure 提供最佳且自動化的分支對分支連線。 虛擬 WAN 可讓您連線，並設定與 Azure 通訊的分支裝置。 連線和設定也可手動完成，或透過虛擬 WAN 合作夥伴使用慣用的提供者裝置來執行。 使用慣用的提供者裝置不僅方便使用，還可簡化連線和管理組態。 Azure WAN 的內建儀表板提供即時的疑難排解深入解析，可協助您節省時間，並可讓您輕鬆地檢視大規模的站對站連線。

[**ExpressRoute**][ExR] 是一種 Azure 連線服務，可讓您在 VDC 實作和任何內部部署網路之間建立私人連線。 ExpressRoute 連線不會經過公用網際網路，而且會提供更高的安全性、可靠性、速度 (最高 10 Gbps)，以及一致的延遲。 ExpressRoute 可輔助 VDC 的實作，因為 ExpressRoute 客戶可獲益於私人連線的相關合規性規則。 利用 [ExpressRoute Direct][ExRD]，具有較大頻寬需求的客戶可以 100Gbps 的傳輸速率直接連線到 Microsoft 路由器。

部署 ExpressRoute 連線通常包含加入 ExpressRoute 服務提供者。 針對需要快速啟動的客戶，一開始通常會使用站對站 VPN 建立 VDC 實作與內部部署資源之間的連線，然後在您與服務提供者的實體互相連線完成時遷移至 ExpressRoute 連線。

#### <a name="connectivity-within-the-cloud"></a>*雲端內的連線*

[VNet][VNet] 和 [VNet 對等][VNetPeering]是 VDC 實作內的基本網路連線服務。 VNet 保證 VDC 資源的自然隔離界限，而且 VNet 對等互連允許相同 Azure 區域內或甚至跨區域的不同 VNet 之間互相通訊。 VNet 內與 VNet 之間的流量控制需要符合透過存取控制清單 ([網路安全性群組][NSG])、[網路虛擬設備][NVA]和自訂路由表 ([UDR][UDR]) 指定的一組安全性規則。

## <a name="virtual-datacenter-overview"></a>虛擬資料中心概觀

### <a name="topology"></a>拓撲

_中樞和輪輻_是設計虛擬資料中心實作網路拓樸的模型。 

[![1]][1]

中樞是中央網路區域，可控制和檢查不同區域之間的輸入或輸出流量：網際網路、內部部署和輪輻。 中樞和輪輻拓撲可讓 IT 部門有效地在中央位置強制執行安全性原則。 此外也可降低設定錯誤和暴露的可能性。

中樞包含支點所使用的一般服務元件。 以下是常用中央服務的範例：

-   從不受信任的網路存取的第三方在能夠存取輪輻中的工作負載之前進行使用者驗證所需的 Windows Active Directory 基礎結構。 其中包括相關的 Active Directory 同盟服務 (AD FS)。
-   DNS 服務，用來對輪輻中的工作負載進行命名解析，以存取內部部署和網際網路上的資源 (如果未使用 [Azure DNS][DNS])。
-   公開金鑰基礎結構 (PKI)，用以實作工作負載的單一登入。
-   輪輻網路區域與網際網路之間的 TCP 和 UDP 流量的流量控制。
-   輪輻與內部部署之間的流量控制。
-   兩個輪輻之間的流量控制 (如有需要)。

VDC 透過在多個支點之間使用共用中樞基礎結構，以減少整體成本。

每個支點的角色都可以裝載不同類型的工作負載。 輪輻也提供適用的模組化方法，供相同的工作負載進行可重複的部署。 範例包括開發和測試、使用者驗收測試、生產階段前環境和生產環境。 輪輻也可區隔和啟用組織內的不同群組。 Azure DevOps 群組為範例之一。 在輪輻內部，可以使用各層之間的流量控制來部署基本工作負載或複雜的多層式工作負載。

#### <a name="subscription-limits-and-multiple-hubs"></a>訂用帳戶限制和多個中樞

在 Azure 中，每個任何類型的元件都會部署在 Azure 訂用帳戶中。 不同 Azure 訂用帳戶中的 Azure 元件隔離可以滿足不同 LOB 的需求，例如設定不同層級的存取和授權。

單一 VDC 實作可以向上延展到大量輪輻；但是，與每個 IT 系統相同，會有平台限制。 中樞部署會繫結至具有限制的特定 Azure 訂用帳戶 (例如，VNet 對等數目上限 - 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與限制][Limits])。 如果限制可能會產生問題，則將模型從單一中樞支點延伸到中樞和支點叢集，即可進一步向上延展架構。 一或多個 Azure 區域中的多個中樞可以使用 VNet 對等互連、ExpressRoute、虛擬 WAN 或站對站 VPN 互相連接。

[![2]][2]

導入多個中樞會增加系統的成本和管理工作。 此狀況只能藉由延展性 (例如系統限制或備援) 和區域複寫 (例如使用者效能或災害復原) 來調整。 在需要多個中樞的案例中，所有中樞都應該致力於提供一組相同的易操作服務。

#### <a name="interconnection-between-spokes"></a>支點之間的互相連線

在單一支點內，可能會實作複雜多層工作負載。 可以在相同的 VNet 中使用子網路 (一層一個) 實作多層設定，以及使用 NSG 篩選流程。

架構設計人員可能會想要跨多個虛擬網路部署一個多層式工作負載。 透過虛擬網路對等互連，輪輻可以連線至相同中樞或不同中樞內的其他輪輻。 此案例的常見範例是，應用程式處理伺服器位於一個輪輻 (或虛擬網路) 中。 資料庫則部署在多個輪輻 (或虛擬網路) 中。 在此情況下，可以輕易地透過虛擬網路對等互連將輪輻互相連線，而避免透過中樞傳輸。 您應仔細檢閱架構和安全性，以確定略過中樞並不會略過可能僅存在於中樞的重要安全性或稽核點。

[![3]][3]

支點也可以與作為中樞的支點互相連接。 這種方法會建立雙層階層：較高層級的支點 (層級 0) 會變成階層中較低支點 (層級 1) 的中樞。 必須實作 VDC 實作的輪輻來將流量轉送到中央中樞，如此一來，流量才能傳送到內部部署網路或公用網際網路中的目的地。 具有雙層中樞的架構引進複雜路由，以移除簡單中樞支點關聯性的優點。

雖然 Azure 允許複雜拓撲，但是 VDC 概念的其中一個核心準則是重複性和簡單性。 若要將管理投入時間降到最低，我們建議採用 VDC 參考架構這種簡單的中樞輪輻設計。

### <a name="components"></a>元件

虛擬資料中心是由四種基本元件類型組成：**基礎結構**、**周邊網路**、**工作負載**和**監視**。

每種元件類型都是由各種 Azure 功能和資源所組成。 VDC 實作是由多種元件類型以及相同元件類型之多個變化的執行個體所構成。 例如，您可能有代表不同應用程式之許多不同邏輯分隔的工作負載執行個體。 您可以使用這些不同的元件類型和執行個體來最後建置 VDC。

[![4]][4]

VDC 的上述高階概念架構顯示不同中樞輪輻拓撲區域中所使用的不同元件類型。 此圖顯示架構各種組件中的基礎結構元件。

一般較好的做法是，存取權利和權限應該以群組為基礎。 比起個別使用者，利用群組可較輕鬆地維護存取原則，因為可提供一致的方式來跨小組管理存取原則。  而且有助於減少設定錯誤。 在適當群組中指派和移除使用者有助於保持特定使用者權限的最新狀態。

每個角色群組的名稱均應有唯一的前置詞。 此前置詞有助於輕易識別哪個群組與哪個工作負載相關聯。 例如，裝載驗證服務的工作負載可能會名為 **AuthServiceNetOps**、**AuthServiceSecOps**、**AuthServiceDevOps** 和 **AuthServiceInfraOps**。 集中式角色 (或與特定服務無關的角色) 前面可能會加上 **Corp**。例如 **CorpNetOps**。

許多組織都會使用下列群組的一種變化，以提供角色的主要分析：

-   中央 IT 群組 **Corp** 具有控制基礎結構元件的擁有權。 例如網路和安全性。 群組必須具有訂用帳戶的參與者角色、中樞的控制權，以及輪輻中的網路參與者權限。 大型組織常會將這些管理職責劃分到多個小組間。 例如，網路作業 **CorpNetOps** 群組 (具有網路的獨佔焦點)，和安全性作業 **CorpSecOps** 群組 (負責防火牆和安全性原則)。 在這種特定情況下，需要建立兩個不同的群組，才能指派這些自訂角色。
-   開發測試群組 **AppDevOps** 負責部署應用程式或服務工作負載。 此群組扮演虛擬機器參與者角色以進行 IaaS 部署，或一或多個 PaaS 參與者的角色。 請參閱[適用於 Azure 資源的內建角色][Roles]。 (選擇性) 開發測試小組可能需要檢視中樞或特定輪輻內的安全性原則 (NSG) 和路由原則 (UDR)。 除了工作負載參與者角色之外，此群組也需要網路讀取者角色。
-   作業和維護群組 (**CorpInfraOps** 或 **AppInfraOps**) 負責管理生產環境中的工作負載。 此群組必須是任何生產訂用帳戶中工作負載的訂用帳戶參與者。 某些組織可能也會評估他們是否需要在生產環境和中央中樞訂用帳戶中具有訂用帳戶參與者角色的額外擴大支援小組群組。 額外的群組可修正生產環境中潛在的設定問題。

VDC 經過設計，因此，針對管理中樞的中央 IT 群組所建立的群組具有工作負載層級的對應群組。 除了管理中樞資源之外，只有中央 IT 群組能夠控制外部存取，以及訂用帳戶的最上層權限。 工作負載群組也能夠單獨控制其在中央 IT 上之 VNet 的資源和權限。

VDC 可分割，以跨不同的企業營運應用程式 (LOB) 安全地裝載多個專案。 所有專案則都需要不同的隔離環境 (開發、UAT、生產)。 其中每個環境的不同 Azure 訂用帳戶都會提供自然隔離。

[![5]][5]

上圖顯示組織的專案、使用者、群組與 Azure 元件部署所在環境之間的關聯性。

通常，在 IT 中，環境 (或層) 是部署和執行多個應用程式的系統。 大型企業使用開發環境 (一開始進行和測試變更的位置) 和生產環境 (使用者所使用的環境)。 這些環境之間通常都會分隔成數個預備環境，以允許分階段部署 (推出)、測試以及在發生問題時復原。 部署架構極大，但通常仍會遵循開始開發環境 (DEV) 和結束生產環境 (PROD) 的基本程序。

這幾種多層式環境的常見架構由 Azure DevOps (開發和測試)、UAT (預備) 和生產環境所組成。 組織可以利用單一或多個 Azure AD 租用戶，來定義對這些環境的存取權和權限。 上圖顯示使用兩個不同 Azure AD 租用戶的情況：一個用於 Azure DevOps 和 UAT，另一個則專用於生產環境。

具有不同的 Azure AD 租用戶會強制執行環境之間的區隔。 相同的使用者群組 (例如中央 IT) 需要使用不同的 URI 憑證存取不同的 Azure AD 租用戶進行驗證，以修改專案的 Azure DevOps 或生產環境的角色或權限。 具有存取不同環境的不同使用者驗證可降低可能的中斷以及人為錯誤所導致的其他問題。

#### <a name="component-type-infrastructure"></a>元件類型：基礎結構

此元件類型是大部分支援基礎結構所在的位置。 它也是集中式 IT、安全性和 (或) 相容性小組花費最多時間的位置。

[![6]][6]

基礎結構元件提供不同 VDC 實作元件的互相連線，並且存在於中樞和輪輻中。 管理和維護基礎結構元件的責任通常會指派給中央 IT 和 (或) 安全性小組。

IT 基礎結構小組的其中一個主要工作是確保整個企業的 IP 位址結構描述一致性。 指派給 VDC 實作的私人 IP 位址空間需要一致，而且不會與內部部署網路上指派的私人 IP 位址重疊。

雖然內部部署邊際路由器或 Azure 環境中的 NAT 可以避免 IP 位址衝突，但是會增加基礎結構元件的複雜性。 管理簡化是 VDC 的其中一個關鍵目標，因此使用 NAT 處理 IP 考量不是建議的解決方案。

基礎結構元件具有下列功能：

-   [**身分識別和目錄服務**][AAD]. Azure 中每種資源類型的存取權都是受控於目錄服務中所儲存的身分識別。 目錄服務不只會儲存使用者清單，也會儲存對特定 Azure 訂用帳戶中資源的存取權。 這些服務只能存在於雲端，或與 Active Directory 中所儲存的內部部署身分識別同步。
-   [**虛擬網路**][VPN]。 虛擬網路是 VDC 的其中一個主要元件，並可讓您定義 Azure 平台的流量隔離界限。 虛擬網路是由單一或多個虛擬網路區段所組成，而每個區段都有特定 IP 網路前置詞 (子網路)。 虛擬網路定義 IaaS 虛擬機器和 PaaS 服務可建立私人通訊的內部周邊區域。 在相同的訂用帳戶下，一個虛擬網路中的 VM (和 PaaS 服務) 無法與不同虛擬網路中的 VM (和 PaaS 服務) 直接通訊，即使兩個虛擬網路都是由同一位客戶所建立也是一樣。 隔離是很重要的屬性，可確保客戶 VM 和通訊仍然隱蔽於虛擬網路內。
-   [**UDR**][UDR]。 預設會根據系統路由表來路由傳送虛擬網路中的流量。 使用者定義路由是網路系統管理員可建立與一或多個子網路關聯的自訂路由表，可覆寫系統路由表的行為，以及定義虛擬網路內的通訊路徑。 UDR 的存在保證來自支點的輸出流量會傳輸到存在於中樞和支點中的特定自訂 VM 以及 (或) 網路虛擬設備和負載平衡器。
-   [**NSG**][NSG]. 網路安全性群組是安全性規則清單，而安全性規則是作為 IP 來源、IP 目的地、通訊協定、IP 來源連接埠和 IP 目的地連接埠的流量篩選。 NSG 可以套用至子網路、與 Azure VM 建立關聯的虛擬 NIC 卡，或兩者。 若要實作中樞和支點中的正確流量控制，NSG 不可或缺。 NSG 所提供的安全性層級是您所開啟之連接埠和用途的功能。 客戶應該套用具有主機型防火牆 (例如 IPtables 或 Windows 防火牆) 的其他個別 VM 篩選。
-   [**DNS**][DNS]。 在 VDC 實作的 VNet 中，資源的名稱解析是透過 DNS 所提供。 Azure 可提供 DNS 服務以供進行[公用][DNS]和[私人][PrivateDNS]名稱解析。 私人區域可在虛擬網路內及虛擬網路之間提供名稱解析。 私人區域不僅可以橫跨相同區域內的虛擬網路，也可以橫跨區域和訂用帳戶。 至於公用解析，Azure DNS 可提供 DNS 網域的主機服務，使用 Microsoft Azure 基礎結構提供名稱解析。 只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。
-   [**訂用帳戶**][SubMgmt]和[**資源群組管理**][RGMgmt]。 訂用帳戶定義自然界限，以在 Azure 中建立多個資源群組。 在名為「資源群組」的邏輯容器中，會將訂用帳戶中的資源組合在一起。 資源群組代表可組織 VDC 實作資源的邏輯群組。
-   [**RBAC**][RBAC]。 透過 RBAC，可以對應組織角色以及存取特定 Azure 資源的權利，讓您限制使用者只能使用特定子集的動作。 使用 RBAC，您可以將適當的角色指派給相關範圍內的使用者、群組和應用程式，來授與存取權。 角色指派的範圍可以是 Azure 訂用帳戶、資源群組或單一資源。 RBAC 允許繼承權限。 在父範圍指派的角色也會授與其內含子系的存取權。 RBAC 可讓您區隔職責，而僅授與使用者執行工作所需的存取權。 例如，使用 RBAC 讓一位員工管理某個訂用帳戶中的虛擬機器，而讓另一位員工管理相同訂用帳戶內的 SQL DB。
-   [**VNet 對等**][VNetPeering]。 用來建立 VDC 基礎結構的基礎功能是 VNet 對等互連，這個機制會透過 Azure 資料中心網路或使用跨區域的 Azure 全球骨幹，連接相同區域中的兩個虛擬網路 (VNet)。

#### <a name="component-type-perimeter-networks"></a>元件類型：周邊網路

[周邊網路][DMZ]元件可提供內部部署或實體資料中心網路之間的網路連線，以及任何連至或來自網際網路的連線。 它也是您網路和安全性小組可能花費最多時間的位置。

連入封包應該先流經中樞內的安全性設備，才會到達輪輻中的後端伺服器。 範例包括防火牆、IDS 和 IPS 等。 來自工作負載的網際網路繫結封包應該也會先流經周邊網路中的安全性設備，才會離開網路。 此流程的目的是為了強制執行原則，以及進行檢查和稽核。

周邊網路元件提供下列功能：

-   [虛擬網路][VNet]、[UDR][UDR] 和 [NSG][NSG]
-   [網路虛擬設備][NVA]
-   [Azure 負載平衡器][ALB]
-   [Azure 應用程式閘道][AppGW]和 [Web 應用程式防火牆 (WAF)][WAF]
-   [公用 IP][PIP]
-   [Azure Front Door][AFD]
-   [Azure 防火牆][AzFW]

通常，中央 IT 和安全性小組會負責周邊網路的需求定義和作業。

[![7]][7]

上圖顯示如何強制執行兩個具有網際網路存取的周邊網路以及一個內部部署網路，而這些都位於 DMZ 和 vWAN 中樞。 在 DMZ 中樞中，連線至網際網路的周邊網路可以使用 Web 應用程式防火牆 (WAF) 和/或 Azure 防火牆的多個伺服器陣列進行向上延展，以支援大量 LOB。 在 vWAN 中樞中，高擴充性分支對分支和分支對 Azure 連線會透過 VPN 或 ExpressRoute 來完成 (如有需要)。

[**虛擬網路**][VNet]。 中樞通常會建置於具有多個子網路的虛擬網路上，以裝載不同類型的服務，而透過 NVA、WAF 和 Azure 應用程式閘道執行個體來篩選和檢查送至或來自網際網路的流量。

[**UDR**][UDR]使用 UDR，客戶可以部署防火牆、IDS/IPS 和其他虛擬設備，並透過這些安全性設備來路由傳送網路流量，以強制執行安全性界限原則、稽核和檢查。 UDR 可以建立於中樞和輪輻中，保證流量傳輸到 VDC 實作所使用的特定自訂 VM、網路虛擬設備和負載平衡器。 若要保證支點所在 VM 產生的流量會傳輸到正確的虛擬設備，UDR 需要設定在支點的子網路中，方法是將內部負載平衡器的前端 IP 位址設定為下一個躍點。 內部負載平衡器會將內部流量分散到虛擬設備 (負載平衡器後端集區)。

[Azure 防火牆][AzFW]是受控、雲端式網路安全性服務，可以保護您的 Azure 虛擬網路資源。 它是完全具狀態的防火牆即服務，具有內建的高可用性和不受限制的雲端延展性。 您可以橫跨訂用帳戶和虛擬網路集中建立、強制執行以及記錄應用程式和網路連線原則。 Azure 防火牆會為您的虛擬網路資源提供靜態公用 IP 位址。 這可讓外部防火牆識別來自您虛擬網路的流量。 此服務與 Azure 監視器會完全整合，以進行記錄和分析。

[**網路虛擬設備**][NVA]。 在中樞內，具有網際網路存取的周邊網路通常會透過 Azure 防火牆執行個體或是防火牆伺服器陣列或 Web 應用程式防火牆 (WAF) 的伺服器陣列進行管理。

不同的 LOB 通常會使用許多 Web 應用程式。 這些應用程式通常很容易受到各種弱點和潛在攻擊的影響。 Web 應用程式防火牆是一種特殊類型的產品，用來偵測對 Web 應用程式的攻擊 (HTTP/HTTPS)，而其深入程度高於一般防火牆。 相較於傳統防火牆技術，WAF 有一組特定功能可保護內部網頁伺服器不受威脅。

Azure 防火牆或 NVA 防火牆都會使用共同管理平面，其中有一組安全性規則可保護輪輻中所裝載的工作負載，以及控制對內部部署網路的存取。 Azure 防火牆具有內建延展性，然而 NVA 防火牆可以在負載平衡器後方手動擴充。 一般而言，防火牆伺服器陣列具有比 WAF 還不特殊的軟體，但具有廣泛應用程式範圍可篩選和檢查任何類型的輸出和輸入流量。 如果使用 NVA 方法，則可從 Azure Marketplace 找到並部署這類防火牆。

使用一組 Azure 防火牆 (或 NVA) 來管理源自網際網路的流量，並使用另一組來管理源自內部部署環境的流量。 對兩者僅使用一組防火牆會造成安全性風險，因為它未提供兩組網路流量之間的安全性範疇。 使用個別的防火牆層級可降低檢查安全性規則的複雜度，並清楚哪些規則對應到哪個傳入網路要求。

建議您針對來自網際網路的流量使用一組 Azure 防火牆執行個體。 對於來自內部部署的流量則使用另一組執行個體。 對兩者僅使用一組防火牆會造成安全性風險，因為這並未提供兩組網路流量之間的安全性周邊。 使用個別的防火牆層級可降低檢查安全性規則的複雜度，並明確指出哪些規則對應到哪個傳入網路要求。

[**Azure Load Balancer**][ALB] 提供高可用性層級 4 (TCP、UDP) 服務，可將連入流量分散到負載平衡組中所定義的服務執行個體。 不論有沒有位址轉譯，從前端端點 (公用 IP 端點或私用 IP 端點) 傳送給負載平衡器的流量都會重新分散到一組後端 IP 位址集區 (範例為網路虛擬設備或 VM)。

Azure Load Balancer 也可以探查各種伺服器執行個體的健康狀態，以及在探查無法回應負載平衡器時，停止將流量傳送至狀況不良的執行個體。 在 VDC 中，外部負載平衡器會部署到中樞和輪輻中。 在中樞中，可使用負載平衡器來有效率地將流量路由至輪輻中的服務，而且在輪輻中，負載平衡器可用來管理應用程式流量。

[**Azure Front Door**][AFD] (AFD) 是 Microsoft 兼具高可用性和高擴充性的 Web 應用程式加速平台、全域 HTTP 負載平衡器、應用程式保護和內容傳遞網路。 AFD 會在 Microsoft 全球網路邊緣 100 個以上的位置執行，可讓您建置、操作及相應放大您的動態 Web 應用程式和靜態內容。 AFD 為您的應用程式提供世界級使用者效能、統一地區/戳記維護自動化、BCDR 自動化、統一用戶端/使用者資訊、快取和服務見解。 此平台可提供效能、可靠性和支援 SLA、合規性認證，以及由 Azure 原生開發、操作及支援的可稽核安全性做法。

[**應用程式閘道**][AppGW] Microsoft Azure 應用程式閘道是專用的虛擬設備，會以服務形式提供應用程式傳遞控制器 (ADC)，為您的應用程式提供各種第 7 層負載平衡功能。 它會將 CPU 密集 SSL 終止卸載至應用程式閘道，讓您最佳化 Web 伺服器陣列的產能。 它也提供其他第 7 層路由功能，包括循環配置連入流量、以 Cookie 為基礎的工作階段同質、URL 路徑型路由，以及在單一應用程式閘道背後代管多個網站的能力。 Web 應用程式防火牆 (WAF) 也是提供為應用程式閘道 WAF SKU 的一部分。 此 SKU 會保護 Web 應用程式免於遭遇常見的 Web 弱點和攻擊。 應用程式閘道可以設定為面向網際網路的閘道、內部專用閘道或兩者混合。 

[**應用程式閘道**][AppGW]是專用的虛擬設備，會以服務形式提供應用程式傳遞控制器 (ADC)，為您的應用程式提供多種第 7 層負載平衡功能。 您可以將需要大量 CPU 資源的 SSL 終止作業卸載到應用程式閘道執行個體，藉以最佳化 Web 伺服器陣列的產能。 它也提供其他第 7 層路由功能，包括下列範例： 
* 傳入流量的循環配置資源散發。 
* 以 Cookie 為基礎的工作階段同質性。 
* URL 路徑型路由。 
* 在單一應用程式閘道執行個體後方裝載多個網站的功能。 Web 應用程式防火牆 (WAF) 也會提供作為應用程式閘道 WAF SKU 的一部分。 此 SKU 會保護 Web 應用程式免於遭遇常見的 Web 弱點和攻擊。 應用程式閘道可以設定為面向網際網路的閘道、內部專用閘道或兩者混合。 

[**公用 IP**][PIP]。 您可以利用某些 Azure 功能建立服務端點與公用 IP 位址的關聯，以便從網際網路存取資源。 此端點會使用網路位址轉譯 (NAT) 將流量路由至 Azure 虛擬網路的內部位址和連接埠。 這個路徑是外部流量進入虛擬網路的主要方式。 您可以設定公用 IP 位址以決定要傳入的流量，以及該流量在虛擬網路上的轉譯方式和目的地。

[**Azure DDoS 保護標準**][DDOS]提供[基本服務][DDOS]層以外特別針對 Azure 虛擬網路資源調整的額外安全防護功能。 「DDoS 保護標準」很容易啟用，而且不需要變更應用程式。 保護原則是透過專用的流量監視和機器學習演算法進行調整。 原則會套用至與虛擬網路中部署的資源相關聯的公用 IP 位址。 例如 Azure Load Balancer、Azure 應用程式閘道和 Azure Service Fabric 執行個體。 在攻擊期間，可透過 Azure 監視器檢視取得即時遙測歷程記錄。 應用程式層保護可透過 Azure 應用程式閘道 Web 應用程式防火牆來新增。 針對 IPv4 Azure 公用 IP 位址提供保護。

#### <a name="component-type-monitoring"></a>元件類型：監視

監視元件提供來自所有其他元件類型的可見性和警示。 所有小組應該都可以存取他們可存取之元件和服務的監視。 如果您有集中式技術支援中心或作業小組，則他們需要具有這些元件所提供資料的整合式存取權。

Azure 提供不同類型的記錄和監視服務，以追蹤 Azure 裝載資源的行為。 Azure 中的工作負載控管和控制不僅以記錄資料的收集為基礎，也需仰賴依特定報告事件觸發動作的能力。

[**Azure 監視器**][Monitor]。 Azure 包含多項服務，能在監視空間內個別執行特定的角色或工作。 這些服務可共同提供一套全面性解決方案，以便收集、分析來自您的應用程式和支援這些服務之 Azure 資源的遙測，並採取行動。 它們也可以用來監視重要的內部部署資源，以提供混合式監視環境。 了解可用的工具和資料是開發完整應用程式監視策略的第一步。

Azure 中有兩種主要類型的記錄：

-   [Azure 活動記錄][ActLog] (先前稱為**作業記錄**) 可讓您深入了解對 Azure 訂用帳戶中資源所執行的作業。 這些記錄會報告訂用帳戶的控制程度事件。 每個 Azure 資源都會產生稽核記錄。

-   [Azure 監視器診斷記錄][DiagLog]是由資源產生的記錄，會提供該資源相關作業的豐富經常性資料。 這些記錄的內容會依資源類型而有所不同。

[![9]][9]

請務必要追蹤 NSG 記錄，特別是下列資訊：

-   [事件記錄檔][NSGLog]會提供哪些 NSG 規則套用到以 MAC 位址為基礎的 VM 和執行個體角色的相關資訊。
-   [計數器記錄][NSGLog]會追蹤執行每個 NSG 規則以拒絕或允許流量的次數。

所有記錄都可以儲存在 Azure 儲存體帳戶中，以進行稽核、靜態分析或備份。 當您將記錄儲存在 Azure 儲存體帳戶時，客戶可以使用不同類型的架構來擷取、準備、分析並以視覺化方式檢視這項資料，以報告雲端資源的狀態和健康情況。 

大型企業應已取得用來監視內部部署系統的標準架構。 他們可以延伸該架構，以整合雲端部署所產生的記錄。 組織可以使用 [Azure Log Analytics](/azure/log-analytics/log-analytics-queries)，將所有記錄保存在雲端中。 Log Analytics 會實作為雲端型服務。 因此，您只需對基礎結構服務進行最基本的投資，就可快速加以啟動並執行。 Log Analytics 也可以整合 System Center 元件 (例如 System Center Operations Manager)，以將現有管理投資延伸到雲端。 

Log Analytics 是一項 Azure 服務，可協助收集、相互關聯、搜尋和處理作業系統、應用程式及基礎結構雲端元件所產生的記錄和效能資料。 它可將使用整合式搜尋和自訂儀表板的即時操作深入資訊提供給客戶，以分析 VDC 實作中所有工作負載的所有記錄。

[Azure 網路監看員][NetWatch]提供了相關工具，可對 Azure 虛擬網路中的資源進行監視、診斷、檢視計量，以及啟用或停用記錄。 這是一項多元的服務，可提供下列功能和更多效用：
-    監視虛擬機器與端點之間的通訊。
-    檢視虛擬網路中的資源及其關聯性。
-    診斷進出於 VM 的網路流量篩選問題。
-    診斷來自 VM 的網路路由問題。
-    診斷來自 VM 的輸出連線。
-    擷取進出於 VM 的封包。
-    對 Azure 虛擬網路閘道和連線進行疑難排解。
-    判斷 Azure 區域和網際網路服務提供者之間的相對延遲。
-    檢視網路介面的安全性規則。
-    檢視網路計量。
-    分析網路安全性群組的輸入或輸出流量。
-    檢視網路資源的診斷記錄。

Operations Management Suite 內部的[網路效能監控][NPM]解決方案可端對端提供詳細的網路資訊。 這項資訊包括 Azure 網路與內部部署網路的單一檢視。 此解決方案具有適用於 ExpressRoute 和公用服務的特定監視器。

#### <a name="component-type-workloads"></a>元件類型：工作負載

工作負載元件是實際應用程式和服務所在的位置。 它也是在應用程式開發小組花費最多時間的位置。

工作負載有各式各樣的可能性。 以下只是一些可能的工作負載類型：

**內部 LOB 應用程式**：企業營運應用程式是對企業之進行中作業而言的重要電腦應用程式。 LOB 應用程式具有一些共同特性：

-   **互動**的特質。 輸入資料後，會傳回結果或報告。
-   **資料驅動**&mdash;具資料密集性，需頻繁存取資料庫或其他儲存體。
-   **整合**&mdash;可與組織內部或外部的其他系統整合。

**面向客戶的網站 (面向網際網路或內部)**：與網際網路互動的大部分應用程式都是網站。 Azure 提供在 IaaS VM 上或從 [Azure Web Apps][WebApps] 站台 (PaaS) 執行網站的功能。 Azure Web Apps 可與允許將 Web Apps 部署在輪輻網路區域中的 VNet 整合。 面向內部的網站不需要公開公用網際網路端點，因為可透過可路由的非網際網路私人位址，從私人的 VNet 存取資源。

**巨量資料/分析**：資料需要相應增加至大量時，資料庫可能無法適當地相應增加。 Hadoop 技術可讓系統對大量節點平行執行分散式查詢。 客戶可以選擇在 IaaS VM 或 PaaS ([HDInsight][HDI]) 中執行資料工作負載。 HDInsight 支援部署到位置型 VNet、可以部署到 VDC 支點中的叢集。

**事件和傳訊**：[Azure 事件中樞][EventHubs]是大規模的遙測擷取服務，能夠收集、轉換及儲存數百萬個事件。 這個分散式串流平台提供低延遲和可設定的保留期，讓您能夠將大量遙測資料輸入 Azure，並從多個應用程式讀取該資料。 使用事件中樞，單一串流就可以同時支援即時和批次型管線。

您可以透過 [Azure 服務匯流排][ServiceBus]在應用程式與服務之間實作高度可靠的雲端傳訊服務。 此方法可提供用戶端與伺服器之間的非同步代理傳訊、結構化的先進先出 (FIFO) 傳訊，以及發佈和訂閱功能。

[![10]][10]

### <a name="making-the-vdc-highly-available-multiple-vdcs"></a>讓 VDC 高度可用：多重 VDC

目前為止，本文的重點在於單一 VDC 的設計，並描述構成復原的基本元件和架構。 Azure 負載平衡器這類 Azure 功能、NVA、可用性設定組、擴展集以及其他機制提供給可讓您將穩固的 SLA 層級建置到生產服務的系統。

不過，由於單一 VDC 通常會在單一區域內實作，因此可能容易受到任何大型中斷 (影響整個區域的中斷) 的影響。 需要高 SLA 的客戶，必須透過在不同區域的兩個 (或更多) VDC 實作中部署相同專案來保護服務。

除了 SLA 考量外，在若干常見的案例中，部署多個 VDC 實作也可發揮效益：

-   區域性或遍及各區。
-   災害復原。
-   在資料中心之間轉向流量的機制。

#### <a name="regionalglobal-presence"></a>地區/全球支援

Azure 資料中心位在全球的許多區域。 選取多個 Azure 資料中心時，客戶需要考慮兩個相關因素：地理距離和延遲。 為了提供最理想的使用者體驗，請評估每個 VDC 實作之間的地理距離，以及每個 VDC 實作與終端使用者之間的距離。

裝載 VDC 實作的區域必須符合您組織運作所在的任何法律轄區所建立的法規需求。

#### <a name="disaster-recovery"></a>災害復原

災害復原方案的設計取決於工作負載類型，以及同步不同 VDC 實作之間工作負載狀態的能力。 在理想情況下，大部分的客戶都會想要快速的容錯移轉機制，而這可能需要在執行多個 VDC 實作的部署之間同步應用程式資料。 不過，在設計災害復原方案時，務必考慮到延遲對大部分的應用程式而言可能都很敏感，而此資料同步可能會造成延遲。

不同 VDC 實作中的應用程式同步和活動訊號監視，都需要 VDC 實作透過網路進行通訊。 不同區域中的兩個 VDC 實作可透過下列方式連線：

-   VNet 對等互連 - VNet 對等互連可以跨區域將各個中樞連線
-   若要讓每個 VDC 實作中的中樞連線到相同 ExpressRoute 線路，應使用 ExpressRoute 私人對等互連
-   透過公司骨幹所連線的多個 ExpressRoute 線路以及連線至 ExpressRoute 線路的多個 VDC 實作
-   在每個 Azure 區域中，VDC 實作中樞區域之間的站對站 VPN 連線

通常，因為透過 Microsoft 骨幹傳輸時的較高頻寬和一致延遲層級，所以 VNet 對等互連或 ExpressRoute 連線是慣用的網路連線類型。

我們建議客戶執行網路資格測試來驗證這些連線的延遲和頻寬，並根據結果決定是否適合同步或非同步資料複寫。 務必也要以最佳復原時間目標 (RTO) 的角度來衡量這些結果。

#### <a name="disaster-recovery-diverting-traffic-from-one-region-to-another"></a>災害復原：將流量從一個區域轉移到另一個區域

[Azure 流量管理員][TM]會定期檢查不同 VDC 實作中公用端點的服務健康情況，並在這些端點失敗時，使用網域名稱系統 (DNS) 自動路由至次要 VDC。 

由於是使用 DNS，因此流量管理員僅適用於 Azure 公用端點。  此服務通常會用來控制流量，或將流量轉移至良好 VDC 實作執行個體中的 Azure VM 和 Web Apps。 流量管理員即使在整個 Azure 區域失敗還是可以恢復，而且可以根據數個準則來控制不同 VDC 中服務端點的使用者流量分配。 例如，特定 VDC 實作中的服務失敗，或選取具有最低網路延遲的 VDC 實作。

### <a name="summary"></a>總結

虛擬資料中心是移轉資料中心的一種方法，可在 Azure 中建立可調整的架構，進而最大化雲端資源的使用、降低成本及簡化系統控管。 VDC 以中樞和輪輻網路拓撲為基礎，並提供中樞中的一般共用服務，以及允許支點中的特定應用程式/工作負載。 VDC 也符合公司角色的結構，其中，不同部門 (例如中央 IT、DevOps、作業和維護) 會一起工作，並執行他們的特定角色。 VDC 滿足「隨即轉移」的移轉需求，但也提供原生雲端部署的許多優點。

## <a name="references"></a>參考

本文件已討論下列功能。 遵循連結以深入了解。

| | | |
|-|-|-|
|網路功能|負載平衡|連線能力|
|[Azure 虛擬網路][VNet]</br>[網路安全性群組][NSG]</br>[NSG 記錄][NSGLog]</br>[使用者定義路由][UDR]</br>[網路虛擬設備][NVA]</br>[公用 IP 位址][PIP]</br>[Azure DDOS][DDOS]</br>[Azure 防火牆][AzFW]</br>[Azure DNS][DNS]|[Azure Front Door][AFD]</br>[Azure Load Balancer (L3) ][ALB]</br>[應用程式閘道 (L7) ][AppGW]</br>[Web 應用程式防火牆][WAF]</br>[Azure 流量管理員][TM]</br></br></br></br></br> |[VNet 對等][VNetPeering]</br>[虛擬私人網路][VPN]</br>[虛擬 WAN][vWAN]</br>[ExpressRoute][ExR]</br>[ExpressRoute Direct][ExRD]</br></br></br></br></br>
|身分識別</br>|監視</br>|最佳做法</br>|
|[Azure Active Directory][AAD]</br>[Multi-Factor Authentication][MFA]</br>[角色型存取控制][RBAC]</br>[預設 Azure AD 角色][Roles]</br></br></br> |[網路監看員][NetWatch]</br>[Azure 監視器][Monitor]</br>[活動記錄][ActLog]</br>[診斷記錄][DiagLog]</br>[Microsoft Operations Management Suite][OMS]</br>[網路效能監視器][NPM]|[周邊網路最佳做法][DMZ]</br>[訂用帳戶管理][SubMgmt]</br>[資源群組管理][RGMgmt]</br>[Azure 訂用帳戶限制][Limits] </br></br></br>|
|其他 Azure 服務|
|[Azure Web Apps][WebApps]</br>[HDInsights (Hadoop) ][HDI]</br>[事件中樞][EventHubs]</br>[服務匯流排][ServiceBus]|

## <a name="next-steps"></a>後續步驟

 - 探索 [VNet 對等][VNetPeering]，其為 VDC 中樞和支點設計的支持技術
 - 實作 [Azure AD][AAD]，以開始使用 [RBAC][RBAC] 探索
 - 開發訂用帳戶和資源管理模型與 RBAC 模型，以符合組織的結構、需求和原則。 正在規劃最重要的活動。 請盡快規劃重組、合併、新的產品線等。m <!--Image References-->
[0]: ./images/networking-redundant-equipment.png "元件重疊範例" 
[1]: ./images/networking-vdc-high-level.png "高階中樞和支點 VDC 範例"
[2]: ./images/networking-hub-spokes-cluster.png "中樞和支點叢集"
[3]: ./images/networking-spoke-to-spoke.png "支點對支點"
[4]: ./images/networking-vdc-block-level-diagram.png "VDC 的區塊層級圖"
[5]: ./images/networking-users-groups-subsciptions.png "使用者、群組、訂用帳戶和專案"
[6]: ./images/networking-infrastructure-high-level.png "高階基礎結構圖"
[7]: ./images/networking-highlevel-perimeter-networks.png "高階基礎結構圖"
[8]: ./images/networking-vnet-peering-perimeter-neworks.png "VNet 對等和周邊網路"
[9]: ./images/networking-high-level-diagram-monitoring.png "高階監視圖"
[10]: ./images/networking-high-level-workloads.png "高階工作負載圖"

<!--Link References-->
[Limits]: /azure/azure-subscription-service-limits
[Roles]: /azure/role-based-access-control/built-in-roles
[VNet]: /azure/virtual-network/virtual-networks-overview
[NSG]: /azure/virtual-network/virtual-networks-nsg
[DNS]: /azure/dns/dns-overview
[PrivateDNS]: /azure/dns/private-dns-overview
[VNetPeering]: /azure/virtual-network/virtual-network-peering-overview 
[UDR]: /azure/virtual-network/virtual-networks-udr-overview 
[RBAC]: /azure/role-based-access-control/overview
[MFA]: /azure/multi-factor-authentication/multi-factor-authentication
[AAD]: /azure/active-directory/active-directory-whatis
[VPN]: /azure/vpn-gateway/vpn-gateway-about-vpngateways 
[ExR]: /azure/expressroute/expressroute-introduction
[ExRD]: /azure/expressroute/expressroute-erdirect-about
[vWAN]: /azure/virtual-wan/virtual-wan-about
[NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[AzFW]: /azure/firewall/overview
[SubMgmt]: /azure/architecture/cloud-adoption/appendix/azure-scaffold 
[RGMgmt]: /azure/azure-resource-manager/resource-group-overview
[DMZ]: /azure/best-practices-network-security
[ALB]: /azure/load-balancer/load-balancer-overview
[DDOS]: /azure/virtual-network/ddos-protection-overview
[PIP]: /azure/virtual-network/resource-groups-networking#public-ip-address
[AFD]: /azure/frontdoor/front-door-overview
[AppGW]: /azure/application-gateway/application-gateway-introduction
[WAF]: /azure/application-gateway/application-gateway-web-application-firewall-overview
[Monitor]: /azure/monitoring-and-diagnostics/
[ActLog]: /azure/monitoring-and-diagnostics/monitoring-overview-activity-logs 
[DiagLog]: /azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs
[NSGLog]: /azure/virtual-network/virtual-network-nsg-manage-log
[OMS]: /azure/operations-management-suite/operations-management-suite-overview
[NPM]: /azure/log-analytics/log-analytics-network-performance-monitor
[NetWatch]: /azure/network-watcher/network-watcher-monitoring-overview
[WebApps]: /azure/app-service/
[HDI]: /azure/hdinsight/hdinsight-hadoop-introduction
[EventHubs]: /azure/event-hubs/event-hubs-what-is-event-hubs 
[ServiceBus]: /azure/service-bus-messaging/service-bus-messaging-overview
[TM]: /azure/traffic-manager/traffic-manager-overview
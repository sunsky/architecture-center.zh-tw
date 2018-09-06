---
title: Azure 虛擬資料中心 - 網路觀點
description: 了解如何在 Azure 中建置虛擬資料中心
author: tracsman
manager: rossort
tags: azure-resource-manager
ms.service: virtual-network
ms.date: 04/3/2018
ms.author: jonor
ms.openlocfilehash: 34fab47cef6d5a9c0130f0864e9fdef33357ba25
ms.sourcegitcommit: ae8a1de6f4af7a89a66a8339879843d945201f85
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2018
ms.locfileid: "43325473"
---
# <a name="azure-virtual-datacenter-a-network-perspective"></a>Azure 虛擬資料中心：網路觀點

## <a name="overview"></a>概觀
將內部部署應用程式移轉至 Azure，甚至不需要任何大量變更 (稱為「隨即轉移」的方法)，即可將安全且具成本效益的基礎結構的優點提供給組織。 不過，若要讓雲端運算具有最大靈活度，企業應該持續改進其架構，以充分利用 Azure 功能。 Microsoft Azure 提供超大規模的服務和基礎結構、企業級功能和可靠性，以及許多混合式連線選項。 客戶可以選擇透過網際網路或透過 Azure ExpressRoute (提供私人網路連線) 存取這些雲端服務。 Microsoft Azure 平台可讓客戶順暢地將基礎結構延伸至雲端並建置多層式架構。 此外，Microsoft 夥伴提供增強的功能，方法是提供最適合在 Azure 中執行的安全性服務和虛擬設備。

本文概述可用來解決許多客戶在考慮移至雲端時所面臨之架構規模、效能和安全性問題的模式和設計。 討論如何將不同組織 IT 角色放入系統的管理和控管，並強調安全性需求和成本最佳化。

## <a name="what-is-a-virtual-data-center"></a>什麼是虛擬資料中心？
在早期，雲端解決方案設計成在公用頻譜中裝載相對隔離的單一應用程式。 這種方法已良好運作多年。 不過，雲端解決方案的優點變得明顯，並且雲端上裝載多個大規模工作負載，而在雲端服務的整個生命週期，處理一或多個區域中的安全性、可靠性、效能和部署成本考量變得十分重要。

下列雲端部署圖所說明的範例是有關安全性漏洞 (紅色方塊) 以及跨工作負載的最佳化網路虛擬設備的空間 (黃色方塊)。

[![0]][0]

虛擬資料中心 (VDC) 的產生是基於調整以支援企業工作負載的這項必要性，以及需要處理在公用雲端中支援大規模應用程式時所產生的問題。

VDC 不只是雲端中的應用程式工作負載，也是網路、安全性、管理和基礎結構 (例如，DNS 和目錄服務)。 它通常也會將私人連線提供回內部部署網路或資料中心。 越來越多的工作負載移到 Azure 時，請務必考慮支援基礎結構以及這些工作負載所在的物件。 謹慎考量資源建構方式可以避免激增數百個「工作負載島」，而工作負載島必須使用獨立資料流程、安全性模型和相容性挑戰進行分開管理。

虛擬資料中心本質上是不同但相關實體的集合，而實體具有一般支援功能和基礎結構。 透過將工作負載檢視為整合式 VDC，您可以了解規模經濟的成本降低、透過元件和資料流程集中化的最佳安全性，以及更容易操作、管理和相容性稽核。

> [!NOTE]
> 請務必了解 VDC **不**是離散 Azure 產品，但各種功能的組合符合您的實際需求。 VDC 是考慮工作負載和 Azure 使用量以最佳化您雲端中資源和能力的方式。 因此，虛擬 DC 是在考慮組織角色和責任的情況，如何在 Azure 中建置 IT 服務的模組化方法。

VDC 可協助企業在下列情況下，將工作負載和應用程式放入 Azure：

-   裝載多個相關工作負載
-   將工作負載從內部部署環境移轉至 Azure
-   實作工作負載之間的共用或集中式安全性和存取需求
-   適當地針對大型企業混合使用 DevOps 和集中式 IT

解除鎖定 VDC 優點的重點是混合使用 Azure 功能的集中式拓撲 (中樞和支點)：[Azure VNet][VNet]、[NSG][NSG]、[VNet 對等][VNetPeering]、[使用者定義路由 (UDR)][UDR]，以及含[角色型存取控制 (RBAC)][RBAC] 的 Azure 身分識別。

## <a name="who-needs-a-virtual-data-center"></a>誰需要虛擬資料中心？
任何需要將更多工作負載移至 Azure 的 Azure 客戶，都可以受益於考慮使用常用資源。 根據範圍，甚至單一應用程式也可以受益於使用用來建置 VDC 的模式和元件。

如果您的組織具有集中式 IT、網路、安全性和 (或) 相容性小組/部門，則 VDC 有助於強制執行原則點、職責區隔，並確保基礎一般元件的一致性，同時讓應用程式小組適當地具有與您需求一樣多的自由和控制。

正在嘗試 DevOps 的組織可以利用 VDC 概念來提供數個授權部分的 Azure 資源，並確保它們擁有該群組內的整體控制 (一般訂用帳戶中的訂用帳戶或資源群組)，但網路和安全性界限會保持相容，如中樞 VNet 和資源群組中的集中式原則所定義。

## <a name="considerations-on-implementing-a-virtual-data-center"></a>實作虛擬資料中心的考量
設計 VDC 時，有數個關鍵問題需要考量：

-   身分識別和目錄服務
-   安全性基礎結構
-   雲端的連線
-   雲端內的連線

### <a name="identity-and-directory-service"></a>身分識別和目錄服務
身分識別和目錄服務是內部部署和雲端中所有資料中心的一個關鍵層面。 身分識別是與 VDC 內服務存取和授權的所有層面有關。 為了協助確保只有授權使用者和處理序才能存取您的 Azure 帳戶和資源，Azure 會使用數種類型的認證進行驗證。 其中包括密碼以存取 Azure 帳戶、密碼編譯金鑰、數位簽章和憑證。 [*Azure Multi-Factor Authentication* (MFA)][MFA] 是存取 Azure 服務的額外安全層。 Azure MFA 透過多種簡易的驗證選項提供強大的驗證，包括電話、簡訊或行動應用程式通知，讓客戶選擇自己喜歡的方式。

任何大型企業都需要定義身分識別管理程序，以描述 VDC 內或跨 VDC 的個別身分識別、其驗證、授權、角色和權限管理。 此程序的目標應該是提高安全性與生產力，同時降低成本、停機時間和重複性手動工作。

企業/組織可能需要依需求混合使用不同企業營運 (LOB) 的服務，而且員工通常在涉及不同專案時會有不同角色。 VDC 需要不同小組之間的良好合作以取得在良好控管下執行的系統，而每個小組各具有特定角色定義。 責任、存取權和權限的矩陣可能會非常複雜。 VDC 中的身分識別管理是透過 [*Azure Active Directory* (AAD)][AAD] 和角色型存取控制 (RBAC) 進行實作。

目錄服務是一種共用資訊基礎結構，用於尋找、管理和組織日常項目和網路資源。 這些資源可能包含磁碟區、資料夾、檔案、印表機、使用者、群組、裝置和其他物件。 目錄伺服器會將網路上的每個資源都視為物件。 資源的相關資訊會儲存為與該資源或物件建立關聯的屬性集合。

所有 Microsoft 線上商務服務都依賴 Azure Active Directory (AAD) 來進行登入和其他身分識別需求。 Azure Active Directory 是全方位、高可用性的身分識別和存取管理的雲端解決方案，它結合了核心目錄服務、進階身分識別管制及應用程式存取管理。 AAD 可以與內部部署 Active Directory 整合，以啟用所有雲端式和本機託管 (內部部署) 應用程式的單一登入。 內部部署 Active Directory 的使用者屬性可以自動同步至 AAD。

不需要單一全域系統管理員，即可指派 VDC 中的所有權限。 相反地，每個特定部門 (或目錄服務中的使用者或服務群組) 都可以有管理其在 VDC 內專屬資源所需的權限。 建構權限需要平衡。 權限太多可能會阻礙效能效率，而權限太少或鬆散可能會增加安全性風險。 Azure 角色型存取控制 (RBAC) 可以為 VDC 資源提供更細緻的存取管理來協助解決這個問題。

#### <a name="security-infrastructure"></a>安全性基礎結構
VDC 內容中的安全性基礎結構主要有關 VDC 特定虛擬網路區段中的流量區隔，以及如何控制整個 VDC 的輸入和輸出流程。 Azure 是根據多租用戶架構，可使用虛擬網路 (VNet) 隔離、存取控制清單 (ACL)、負載平衡器和 IP 篩選以及流量流程原則來防止部署之間的未經授權和意外流量。 網路位址轉譯 (NAT) 會區隔內部網路流量與外部流量。

Azure 網狀架構會將基礎結構資源分配給租用戶工作負載，並管理與虛擬機器 (VM) 之間的通訊。 Azure Hypervisor 會強制執行 VM 之間的記憶體和處理序區隔，並安全地將網路流量路由傳送至客體 OS 租用戶。

#### <a name="connectivity-to-the-cloud"></a>雲端的連線
VDC 需要與外部網路的連線，才能將服務提供給客戶、合作夥伴和 (或) 內部使用者。 這通常表示不只連線到網際網路，也會連線至內部部署網路和資料中心。

客戶可以建置其安全性原則，來控制可以使用網路虛擬設備從網際網路存取哪些特定 VDC 託管服務和其存取方式 (使用篩選和流量檢查)，以及自訂路由原則和網路篩選 (使用者定義路由和網路安全性群組)。

企業通常需要將 VDC 連接至內部部署資料中心或其他資源。 因此，設計有效架構時，Azure 與內部部署網路之間的連線是重要層面。 企業有兩種不同的方式可在 Azure 中建立 VDC 與內部部署之間的互相連線：透過網際網路和 (或) 私人直接連線的傳輸。

[**Azure 站對站 VPN**][VPN] 是內部部署網路與 VDC 之間透過網際網路的互相連線服務，並透過安全加密連線 (IPsec/IKE 通道) 所建立。 Azure 站對站連線具彈性且更快速建立，而且不需要任何進一步採購，因為所有連線都是透過網際網路連線。

[**ExpressRoute**][ExR] 是一種 Azure 連線服務，可讓您在 VDC 與內部部署網路之間建立私人連線。 ExpressRoute 連線不會經過公用網際網路，而且會提供更高的安全性、可靠性、速度 (最高 10 Gbps)，以及一致的延遲。 ExpressRoute 十分適合 VDC，因為 ExpressRoute 客戶可以獲得與私人連線建立關聯之相容性規則的優點。

部署 ExpressRoute 連線包含加入 ExpressRoute 服務提供者。 針對需要快速啟動的客戶，一開始通常會使用站對站 VPN 建立 VDC 與內部部署資源之間的連線，然後移轉至 ExpressRoute 連線。

#### <a name="connectivity-within-the-cloud"></a>*雲端內的連線*
[VNet][VNet] 和 [VNet 對等][VNetPeering]是 VDC 內的基本網路連線服務。 VNet 保證 VDC 資源的自然隔離界限，而且 VNet 對等互連允許相同 Azure 區域內或甚至跨區域的不同 VNet 之間互相通訊。 VNet 內與 VNet 之間的流量控制需要符合透過存取控制清單 ([網路安全性群組][NSG])、[網路虛擬設備][NVA]和自訂路由表 ([UDR][UDR]) 指定的一組安全性規則。

## <a name="virtual-data-center-overview"></a>虛擬資料中心概觀

### <a name="topology"></a>拓撲
_中樞和支點_是延伸單一 Azure 區域內虛擬資料中心的模型。

[![1]][1]

中樞是中央區域，可控制和檢查不同區域之間的輸入和 (或) 輸出流量：網際網路、內部部署和支點。 中樞和支點拓撲提供有效的方式可讓 IT 部門在中央位置強制執行安全性原則，同時減少設定錯誤和暴露的可能性。

中樞包含支點所使用的一般服務元件。 以下是一些典型常用中央服務範例：

-   取得支點中工作負載的存取之前，不受信任網路中第三方存取的使用者驗證所需的 Windows Active Directory 基礎結構 (具有相關 ADFS 服務)。
-   解決支點中工作負載命名的 DNS 服務，以存取內部部署和網際網路上的資源。
-   PKI 基礎結構，可對工作負載實作單一登入
-   支點與網際網路之間的流量控制 (TCP/UDP)
-   支點與內部部署之間的流量控制
-   如有需要，為兩個支點之間的流量控制

VDC 透過在多個支點之間使用共用中樞基礎結構，以減少整體成本。

每個支點的角色都可以裝載不同類型的工作負載。 支點也可以提供相同工作負載之可重複部署的模組化方法 (例如，開發和測試、使用者驗收測試、進入生產階段前和生產)。 支點也可以用來區隔並啟用組織 (例如，DevOps 群組) 內的不同群組。 在支點內部，可以使用各層之間的流量控制來部署基本工作負載或複雜多層工作負載。

#### <a name="subscription-limits-and-multiple-hubs"></a>訂用帳戶限制和多個中樞
在 Azure 中，每個任何類型的元件都會部署在 Azure 訂用帳戶中。 不同 Azure 訂用帳戶中的 Azure 元件隔離可以滿足不同 LOB 的需求，例如設定不同層級的存取和授權。

單一 VDC 可以向上延展到大量支點；但是，與每個 IT 系統相同，會有平台限制。 中樞部署會繫結至具有限制的特定 Azure 訂用帳戶 (例如，VNet 對等數目上限 - 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與限制][Limits])。 如果限制可能會產生問題，則將模型從單一中樞支點延伸到中樞和支點叢集，即可進一步向上延展架構。 一或多個 Azure 區域中的多個中樞可以使用 VNet 對等互連、Express Route 或站對站 VPN 互相連接。

[![2]][2]

引進多個中樞會增加系統的成本和管理工作，並且只會根據延展性進行調整 (範例：系統限制或備援) 和地區複寫 (範例：終端使用者效能或災害復原)。 在需要多個中樞的案例中，所有中樞都應該致力於提供一組相同的易操作服務。

#### <a name="interconnection-between-spokes"></a>支點之間的互相連線
在單一支點內，可能會實作複雜多層工作負載。 可以在相同的 VNet 中使用子網路 (一層一個) 實作多層設定，以及使用 NSG 篩選流程。

另一方面，架構設計人員可能想要跨多個 VNet 部署多層工作負載。 使用 VNet 對等，支點可以連接到相同中樞或不同中樞中的其他支點。 此案例的一般範例是應用程式處理伺服器位於一個支點 (VNet)，而資料庫部署於多個支點 (VNet). 在此情況下，很容易將支點與 VNet 對等互相連接，進而避免透過中樞傳送。 應該仔細檢閱架構和安全性，確保略過中樞不會略過可能只存在於中樞中的重要安全性或稽核點。

[![3]][3]

支點也可以與作為中樞的支點互相連接。 這種方法會建立雙層階層：較高層級的支點 (層級 0) 會變成階層中較低支點 (層級 1) 的中樞。 VDC 的支點需要將流量轉送到中央中樞，以連接到內部部署網路或網際網路。 具有雙層中樞的架構引進複雜路由，以移除簡單中樞支點關聯性的優點。

雖然 Azure 允許複雜拓撲，但是 VDC 概念的其中一個核心準則是重複性和簡單性。 若要將管理投入時間降到最低，簡單中樞點設計是建議的 VDC 參考架構。

### <a name="components"></a>元件
虛擬資料中心是由四種基本元件類型所構成：[基礎結構]、[周邊網路]、[工作負載] 和 [監視]。

每種元件類型都是由各種 Azure 功能和資源所組成。 VDC 是由多種元件類型以及相同元件類型之多個變化的執行個體所構成。 例如，您可能有代表不同應用程式之許多不同邏輯分隔的工作負載執行個體。 您可以使用這些不同的元件類型和執行個體來最後建置 VDC。

[![4]][4]

VDC 的上述高階架構顯示中樞支點拓撲之不同區域中所使用的不同元件類型。 此圖顯示架構各種組件中的基礎結構元件。

不錯的做法 (適用於內部部署 DC 或 VDC) 是存取權利和權限應該是以群組為基礎。 處理群組，而不是個別使用者協助一致地維護跨小組的存取原則，以及協助將設定錯誤降至最低。 在適當群組中指派和移除使用者有助於保持特定使用者權限的最新狀態。

每個角色群組的名稱都應該有唯一的前置詞，才能輕鬆地識別哪個群組與哪個工作負載建立關聯。 例如，裝載驗證服務的工作負載可能會名為 AuthServiceNetOps、AuthServiceSecOps、AuthServiceDevOps 和 AuthServiceInfraOps 的群組。 與集中式角色或未與特定服務相關的角色類似，前面可能會加上 "Corp"，例如：*CorpNetOps*。

許多組織都會使用下列群組的一種變化，以提供角色的主要分析：

-   「中央 IT 群組 (Corp)」具有控制基礎結構 (例如網路和安全性) 元件的擁有權權限，因此必須要有訂用帳戶的參與者角色 (並具有中樞的控制權) 以及支點中的網路參與者權限。 大型組織經常會在多個小組之間分割這些責任，例如網路作業 (CorpNetOps) 群組 (具有網路的獨佔焦點) 和資料安全性作業 (CorpSecOps) 群組 (負責防火牆和安全性原則)。 在這種特定情況下，需要建立兩個不同的群組，才能指派這些自訂角色。
-   「開發和測試 (AppDevOps) 群組」負責部署工作負載 (應用程式或服務)。 此群組扮演虛擬機器參與者角色來進行 IaaS 部署，以及 (或) 一或多個 PaaS 參與者的角色 (請參閱 [ Azure 角色型存取控制的內建角色][Roles])。 (選擇性) 開發與測試小組可能需要有中樞或特定支點內安全性原則 (NSG) 和路由原則 (UDR) 的可見性。 因此，除了工作負載參與者角色之外，此群組也需要網路讀取者角色。
-   「作業和維護群組 (CorpInfraOps 或 AppInfraOps)」負責管理生產環境中的工作負載。 此群組必須是任何生產訂用帳戶中工作負載的訂用帳戶參與者。 某些組織可能也會評估它們是否需要具有生產環境和中央中樞訂用帳戶中訂用帳戶參與者角色的額外擴大支援小組群組，才能修正生產環境中的潛在設定問題。

VDC 具結構性，因此，針對管理中樞的中央 IT 群組所建立的群組具有工作負載層級的對應群組。 除了管理中樞資源之外，只有中央 IT 群組才能夠控制外部存取，以及訂用帳戶的最上層權限。 不過，工作負載群組能夠單獨控制其在中央 IT 上之 VNet 的資源和權限。

需要分割 VDC，才能安全地裝載跨不同企業營運 (LOB) 的多個專案。 所有專案則都需要不同的隔離環境 (開發、UAT、生產)。 其中每個環境的不同 Azure 訂用帳戶都會提供自然隔離。

[![5]][5]

上圖顯示組織的專案、使用者、群組與 Azure 元件部署所在環境之間的關聯性。

通常，在 IT 中，環境 (或層) 是部署和執行多個應用程式的系統。 大型企業使用開發環境 (一開始進行和測試變更的位置) 和生產環境 (使用者所使用的環境)。 這些環境之間通常都會分隔成數個預備環境，以允許分階段部署 (推出)、測試以及在發生問題時復原。 部署架構極大，但通常仍會遵循開始開發環境 (DEV) 和結束生產環境 (PROD) 的基本程序。

這些多層環境類型的常見架構包含 DevOps (開發和測試)、UAT (預備) 和生產環境。 組織可以利用單一或多個 Azure AD 租用戶，來定義對這些環境的存取權和權限。 上圖顯示使用兩個不同 Azure AD 租用戶的情況：一個適用於 DevOps 和 UAT，另一個則專用於生產環境。

具有不同的 Azure AD 租用戶會強制執行環境之間的區隔。 相同的使用者群組 (例如，中央 IT) 需要使用不同的 URI 憑證存取不同的 AD 租用戶進行驗證，以修改專案之 DevOps 或生產環境的角色或權限。 具有存取不同環境的不同使用者驗證可降低可能的中斷以及人為錯誤所導致的其他問題。

#### <a name="component-type-infrastructure"></a>元件類型：基礎結構
此元件類型是大部分支援基礎結構所在的位置。 它也是集中式 IT、安全性和 (或) 相容性小組花費最多時間的位置。

[![6]][6]

基礎結構元件提供不同 VDC 元件之間的互相連線，並且存在於中樞和支點中。 管理和維護基礎結構元件的責任通常會指派給中央 IT 和 (或) 安全性小組。

IT 基礎結構小組的其中一個主要工作是確保整個企業的 IP 位址結構描述一致性。 指派給 VDC 的私人 IP 位址空間需要一致，而且不會與內部部署網路上指派的私人 IP 位址重疊。

雖然內部部署邊際路由器或 Azure 環境中的 NAT 可以避免 IP 位址衝突，但是會增加基礎結構元件的複雜性。 管理簡化是 VDC 的其中一個關鍵目標，因此使用 NAT 處理 IP 考量不是建議的解決方案。

基礎結構元件包含下列功能：

-   [**身分識別和目錄服務**][AAD]. Azure 中每種資源類型的存取權都是受控於目錄服務中所儲存的身分識別。 目錄服務不只會儲存使用者清單，也會儲存對特定 Azure 訂用帳戶中資源的存取權。 這些服務只能存在於雲端，或與 Active Directory 中所儲存的內部部署身分識別同步。
-   [**虛擬網路**][VPN]。 虛擬網路是 VDC 的其中一個主要元件，並可讓您定義 Azure 平台的流量隔離界限。 虛擬網路是由單一或多個虛擬網路區段所組成，而每個區段都有特定 IP 網路前置詞 (子網路)。 虛擬網路定義 IaaS 虛擬機器和 PaaS 服務可建立私人通訊的內部周邊區域。 在相同的訂用帳戶下，一個虛擬網路中的 VM (和 PaaS 服務) 無法與不同虛擬網路中的 VM (和 PaaS 服務) 直接通訊，即使兩個虛擬網路都是由同一位客戶所建立也是一樣。 隔離是很重要的屬性，可確保客戶 VM 和通訊仍然隱蔽於虛擬網路內。
-   [**UDR**][UDR]。 預設會根據系統路由表來路由傳送虛擬網路中的流量。 使用者定義路由是網路系統管理員可建立與一或多個子網路關聯的自訂路由表，可覆寫系統路由表的行為，以及定義虛擬網路內的通訊路徑。 UDR 的存在保證來自支點的輸出流量會傳輸到存在於中樞和支點中的特定自訂 VM 以及 (或) 網路虛擬設備和負載平衡器。
-   [**NSG**][NSG]. 網路安全性群組是安全性規則清單，而安全性規則是作為 IP 來源、IP 目的地、通訊協定、IP 來源連接埠和 IP 目的地連接埠的流量篩選。 NSG 可以套用至子網路、與 Azure VM 建立關聯的虛擬 NIC 卡，或兩者。 若要實作中樞和支點中的正確流量控制，NSG 不可或缺。 NSG 所提供的安全性層級是您所開啟之連接埠和用途的功能。 客戶應該套用具有主機型防火牆 (例如 IPtables 或 Windows 防火牆) 的其他個別 VM 篩選。
-   [**DNS**][DNS]。 在 VDC 的 VNet 中，資源的名稱解析是透過 DNS 所提供。 Azure 可提供 DNS 服務以供進行[公用][DNS] 和[私人][PrivateDNS]名稱解析。 私人區域可在虛擬網路內及虛擬網路之間提供名稱解析。 私人區域不僅可以橫跨相同區域內的虛擬網路，也可以橫跨區域和訂用帳戶。 至於公用解析，Azure DNS 可提供 DNS 網域的主機服務，使用 Microsoft Azure 基礎結構提供名稱解析。 只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。
-   [**訂用帳戶**][SubMgmt]和[**資源群組管理**][RGMgmt]。 訂用帳戶定義自然界限，以在 Azure 中建立多個資源群組。 在名為「資源群組」的邏輯容器中，會將訂用帳戶中的資源組合在一起。 資源群組代表可組織 VDC 資源的邏輯群組。
-   [**RBAC**][RBAC]。 透過 RBAC，可以對應組織角色以及存取特定 Azure 資源的權利，讓您限制使用者只能使用特定子集的動作。 使用 RBAC，您可以將適當的角色指派給相關範圍內的使用者、群組和應用程式，來授與存取權。 角色指派的範圍可以是 Azure 訂用帳戶、資源群組或單一資源。 RBAC 允許繼承權限。 在父範圍指派的角色也會授與其內含子系的存取權。 RBAC 可讓您區隔職責，而僅授與使用者執行工作所需的存取權。 例如，使用 RBAC 讓一位員工管理某個訂用帳戶中的虛擬機器，而讓另一位員工管理相同訂用帳戶內的 SQL DB。
-   [**VNet 對等**][VNetPeering]。 用來建立 VDC 基礎結構的基礎功能是 VNet 對等互連，這個機制會透過 Azure 資料中心網路或使用跨區域的 Azure 全球骨幹，連接相同區域中的兩個虛擬網路 (VNet)。

#### <a name="component-type-perimeter-networks"></a>元件類型：周邊網路
[周邊網路][DMZ]元件 (也稱為 DMZ 網路) 可讓您提供與內部部署或實體資料中心網路的網路連線，以及與網際網路之間的任何連線。 它也是您網路和安全性小組可能花費最多時間的位置。

連入封包應該先流經中樞內的安全性設備，如防火牆、IDS 和 IPS 等，才會到達支點中的後端伺服器。 來自工作負載的網際網路繫結封包應該也會流經周邊網路中的安全性設備，經過原則強制執行、檢查和稽核之後，才會離開網路。

周邊網路元件提供下列功能：

-   [虛擬網路][VNet]、[UDR][UDR]、[NSG][NSG]
-   [網路虛擬設備][NVA]
-   [負載平衡器][ALB]
-   [應用程式閘道][AppGW] / [WAF][WAF]
-   [公用 IP][PIP]

通常，中央 IT 和安全性小組會負責周邊網路的需求定義和作業。

[![7]][7]

上圖顯示如何強制執行兩個具有網際網路存取的周邊網路以及一個內部部署網路，而這些都位在中樞中。 在單一中樞中，連線至網際網路的周邊網路可以使用 Web 應用程式防火牆 (WAF) 和 (或) 防火牆的多個伺服器陣列進行向上延展，以支援大量 LOB。

[**虛擬網路**][VNet]：中樞一般是建置於具有多個子網路的 VNet，以裝載不同類型的服務，其透過 NVA、WAF 和 Azure 應用程式閘道篩選和檢查送至或來自網際網路的流量。

[**UDR**][UDR]：使用 UDR，客戶可以部署防火牆、IDS/IPS 和其他虛擬設備，並透過這些安全性設備來路由傳送網路流量，以強制執行安全性界限原則、稽核和檢查。 UDR 可以建立於中樞和支點中，保證流量傳輸到 VDC 所使用的特定自訂 VM、網路虛擬設備和負載平衡器。 若要保證支點所在 VM 產生的流量會傳輸到正確的虛擬設備，UDR 需要設定在支點的子網路中，方法是將內部負載平衡器的前端 IP 位址設定為下一個躍點。 內部負載平衡器會將內部流量分散到虛擬設備 (負載平衡器後端集區)。

[![8]][8]

[**網路虛擬設備**][NVA]：在中樞中，具有網際網路存取的周邊網路通常是透過防火牆和 (或) Web 應用程式防火牆 (WAF) 的伺服器陣列進行管理。

不同的 LOB 通常會使用許多 Web 應用程式，而且這些應用程式通常很容易受到各種弱點和潛在攻擊的攻擊。 Web 應用程式防火牆是一種特殊類型的產品，用來偵測對 Web 應用程式的攻擊 (HTTP/HTTPS)，而其深入程度高於一般防火牆。 相較於傳統防火牆技術，WAF 有一組特定功能可保護內部網頁伺服器不受威脅。

防火牆伺服器陣列是一組在相同一般管理下串聯運作的防火牆，並具有一組安全性規則來保護支點中所裝載的工作負載，以及控制對內部部署網路的存取。 防火牆伺服器陣列具有比 WAF 還不特殊的軟體，但具有廣泛應用程式範圍可篩選和檢查任何類型的輸出和輸入流量。 防火牆伺服器陣列通常是透過可在 Azure 市集中取得的網路虛擬設備 (NVA) 在 Azure 中實作。

建議將一組 NVA 用於來自網際網路的流量，並將另一組用於來自內部部署的流量。 對兩者僅使用一組 NVA 會造成安全性風險，因為它未提供兩組網路流量之間的安全性範疇。 使用個別 NVA 可降低檢查安全性規則的複雜度，並清楚哪些規則對應到哪個傳入網路要求。

大部分大型企業都會管理多個網域。 Azure DNS 可以用來裝載特定網域的 DNS 記錄。 例如，Azure 外部負載平衡器 (或 WAF) 的虛擬 IP 位址 (VIP) 可以註冊於 Azure DNS 記錄的 A 記錄中。

[**Azure Load Balancer**][ALB]：Azure Load Balancer 提供高可用性層級 4 (TCP、UDP) 服務，可將連入流量分散到負載平衡組中所定義的服務執行個體。 不論有沒有位址轉譯，從前端端點 (公用 IP 端點或私用 IP 端點) 傳送給負載平衡器的流量都會重新分散到一組後端 IP 位址集區 (範例為網路虛擬設備或 VM)。

Azure Load Balancer 也可以探查各種伺服器執行個體的健康狀態，以及在探查無法回應負載平衡器時，停止將流量傳送至狀況不良的執行個體。 在 VDC 中，我們將外部負載平衡器放在中樞 (例如，將流量平衡到 NVA) 和支點 (執行工作，例如平衡多層應用程式的不同 VM 之間的流量) 中。

[**應用程式閘道**][AppGW]：Microsoft Azure 應用程式閘道是專用的虛擬設備，會以服務形式提供應用程式傳遞控制器 (ADC)，為您的應用程式提供各種第 7 層負載平衡功能。 它會將 CPU 密集 SSL 終止卸載至應用程式閘道，讓您最佳化 Web 伺服器陣列的產能。 它也提供其他第 7 層路由功能，包括循環配置連入流量、以 Cookie 為基礎的工作階段同質、URL 路徑型路由，以及在單一應用程式閘道背後代管多個網站的能力。 Web 應用程式防火牆 (WAF) 也是提供為應用程式閘道 WAF SKU 的一部分。 此 SKU 會保護 Web 應用程式免於遭遇常見的 Web 弱點和攻擊。 應用程式閘道可以設定為面向網際網路的閘道、內部專用閘道或兩者混合。 

[**公用 IP**][PIP]：某些 Azure 功能可讓您建立服務端點與公用 IP 位址的關聯，而公用 IP 位址允許從網際網路存取資源。 此端點使用網路位址轉譯 (NAT) 將流量路由傳送至 Azure 虛擬網路的內部位址和連接埠。 這個路徑是外部流量進入虛擬網路的主要方式。 公用 IP 位址可以設定成判斷要傳入的流量，以及該流量在虛擬網路上如何轉譯及轉譯至何處。

#### <a name="component-type-monitoring"></a>元件類型：監視
監視元件提供來自所有其他元件類型的可見性和警示。 所有小組應該都可以存取他們可存取之元件和服務的監視。 如果您有集中式支援人員或作業小組，則他們需要具有這些元件所提供資料的整合式存取權。

Azure 提供不同類型的記錄和監視服務，以追蹤 Azure 託管資源的行為。 Azure 中的工作負載控管和控制不只根據收集記錄資料，也會根據依特定報告事件觸發動作的能力。

[**Azure 監視器**][Monitor] - Azure 包含多項服務，能在監視空間內個別執行特定的角色或工作。 這些服務可共同提供一套全面性解決方案，以便收集、分析來自您的應用程式和支援這些服務之 Azure 資源的遙測，並採取行動。 它們也可以用來監視重要的內部部署資源，以提供混合式監視環境。 了解可用的工具和資料是開發完整應用程式監視策略的第一步。

Azure 中有兩種主要類型的記錄：

-   [**活動記錄**][ActLog] (也稱為「作業記錄」) 可讓您了解對 Azure 訂用帳戶中資源所執行的作業。 這些記錄會報告訂用帳戶的控制程度事件。 每個 Azure 資源都會產生稽核記錄。

-   [**Azure 診斷記錄**][DiagLog]是由資源所產生的記錄，提供有關該資源之作業的豐富經常性資料。 這些記錄的內容會依資源類型而有所不同。

[![9]][9]

在 VDC 中，最為重要的是追蹤 NSG 記錄，特別是下列資訊：

-   [**事件記錄檔**][NSGLog]︰提供哪些 NSG 規則套用到以 MAC 位址為基礎的 VM 和執行個體角色的相關資訊。
-   [**計數器記錄**][NSGLog]：追蹤執行每個 NSG 規則以拒絕或允許流量的次數。

所有記錄都可以儲存在 Azure 儲存體帳戶中，以進行稽核、靜態分析或備份。 將記錄儲存在 Azure 儲存體帳戶中時，客戶就可以使用不同類型的架構來擷取、準備、分析並以視覺化方式檢視這項資料，以報告雲端資源的狀態和健康狀態。

大型企業應該已取得用來監視內部部署系統的標準架構，以及可以延伸該架構以整合雲端部署所產生的記錄。 對於想要在雲端保留所有記錄的組織而言，[Log Analytics][../log-analytics/log-analytics-overview .md] 是不錯的選擇。 因為 Log Analytics 實作為雲端型服務，所以您對基礎結構服務進行最小的投資就可以快速啟動並執行它。 Log Analytics 也可以整合 System Center 元件 (例如 System Center Operations Manager)，以將現有管理投資擴充到雲端。

Log Analytics 是一項 Azure 服務，可協助收集、相互關聯、搜尋和處理作業系統、應用程式及基礎結構雲端元件所產生的記錄和效能資料。 它可將使用整合式搜尋和自訂儀表板的即時操作深入資訊提供給客戶，以分析 VDC 中所有工作負載的所有記錄。

OMS 內部的[網路效能監控 (NPM)][NPM] 解決方案可端對端提供詳細的網路資訊，包括 Azure 網路與內部部署網路的單一檢視。 使用適用於 ExpressRoute 和公用服務的特定監控。

#### <a name="component-type-workloads"></a>元件類型：工作負載
工作負載元件是實際應用程式和服務所在的位置。 它也是在應用程式開發小組花費最多時間的位置。

工作負載可能性真的無止盡。 以下只是一些可能的工作負載類型：

**內部 LOB 應用程式**。 企業營運應用程式是對企業之進行中作業而言的重要電腦應用程式。 LOB 應用程式具有一些共同特性：

-   **互動式**。 LOB 應用程式皆為互動式本質：輸入資料，並傳回結果/報表。
-   **資料驅動**。 LOB 應用程式具資料密集性，其會頻繁存取資料庫或其他儲存體。
-   **整合式**。 LOB 應用程式可與組織內部或外部的其他系統整合。

**面向客戶的網站 (面向網際網路或內部)**。 與網際網路互動的大部分應用程式都是網站。 Azure 提供在 IaaS VM 上或從 [Azure Web Apps][WebApps] 站台 (PaaS) 執行網站的功能。 Azure Web Apps 支援整合允許將 Web Apps 部署在 VDC 支點中的 VNet。 在查看對內提供的網站時，由於有 VNET 整合，您不需要公開您應用程式的網際網路端點，但可以改為透過私人 VNet 的私人非網際網路可路由傳送位址來使用資源。

**巨量資料/分析**：資料需要向上延展至極大型磁碟區時，資料庫可能無法適當地向上延展。 Hadoop 技術可讓系統對大量節點平行執行分散式查詢。 客戶可以選擇在 IaaS VM 或 PaaS ([HDInsight][HDI]) 中執行資料工作負載。 HDInsight 支援部署到位置型 VNet、可以部署到 VDC 支點中的叢集。

**事件和傳訊**。 [Azure 事件中樞][EventHubs]是大規模的遙測擷取服務，能夠收集、轉換及儲存數百萬個事件。 這個分散式串流平台提供低延遲和可設定的保留期，讓您能夠將大量遙測資料輸入 Azure，並從多個應用程式讀取該資料。 使用事件中樞，單一串流就可以同時支援即時和批次型管線。

應用程式與服務之間的高可靠雲端傳訊服務可以透過 [Azure 服務匯流排][ServiceBus]進行實作，而 Azure 服務匯流排提供用戶端與伺服器之間的非同步代理傳訊，以及結構化先進先出 (FIFO) 傳訊和發佈/訂閱功能。

[![10]][10]

### <a name="multiple-vdc"></a>多重 VDC
目前為止，本文的重點在於單一 VDC，並描述構成復原 VDC 的基本元件和架構。 Azure 負載平衡器這類 Azure 功能、NVA、可用性設定組、擴展集以及其他機制提供給可讓您將穩固的 SLA 層級建置到生產服務的系統。

不過，單一 VDC 裝載於單一區域內，而且很容易發生可能影響該整個區域的主要服務中斷。 想要達到高 SLA 的客戶必須透過在不同區域的兩個 (或以上) VDC 中部署相同專案來保護服務。

除了 SLA 考量，有數種部署多個 VDC 的有意義常見案例：

-   地區/全球支援
-   災害復原
-   在 DC 之間轉向流量的機制

#### <a name="regionalglobal-presence"></a>地區/全球支援
Azure 資料中心位在全球的許多區域。 選取多個 Azure 資料中心時，客戶需要考慮兩個相關因素：地理距離和延遲。 客戶必須評估 VDC 之間的地理距離，以及 VDC 與終端使用者之間的距離，以提供最佳使用者體驗。

VDC 裝載所在的 Azure 區域也需要符合您組織運作所在之任何法律轄區所建立的法規需求。

#### <a name="disaster-recovery"></a>災害復原
災害復原計劃的實作是與相關的工作負載類型強烈相關，以及同步不同 VDC 之間的工作負載狀態的功能。 在理想情況下，大部分客戶都想要同步兩個不同 VDC 中執行之部署間的應用程式資料，以實作快速容錯移轉機制。 大部分的應用程式都很容易延遲，因而導致潛在的資料同步逾時和延遲。

不同 VDC 中應用程式的同步或活動訊號監視需要其間的通訊。 不同區域中的兩個 VDC 可以透過進行連線：

-   VNet 對等互連 - VNet 對等互連可以跨區域將各個中樞連線
-   VDC 中樞連接到相同 ExpressRoute 電路時的 ExpressRoute 私人對等
-   透過公司骨幹所連線的多個 ExpressRoute 電路以及連線至 ExpressRoute 電路的 VDC 網格
-   每個 Azure 區域中 VDC 中樞之間的站對站 VPN 連線

通常，因為透過 Microsoft 骨幹傳輸時的較高頻寬和一致延遲，所以 VNet 對等互連或 ExpressRoute 連線是慣用的機制。

沒有任何魔法可以驗證分散於不同區域中兩個 (以上) 不同 VDC 之間的應用程式。 客戶應該執行網路資格測試來驗證連線延遲和頻寬，並設定同步還是非同步資料複寫適合，以及您工作負載的最佳復原時間目標 (RTO)。

#### <a name="mechanism-to-divert-traffic-between-dc"></a>在 DC 之間轉向流量的機制
將來自某個 DC 的流量轉向到另一個 DC 的一個有效技術是以 DNS 為基礎。 [Azure 流量管理員][TM]使用網域名稱系統 (DNS) 機制，以將終端使用者導向特定 VDC 中的最適合公用端點。 透過探查，流量管理員會定期檢查不同 VDC 中公用端點的服務健康狀態，如果這些端點失敗，則會自動路由傳送至次要 VDC。

流量管理員作用於 Azure 公用端點上，例如，可以用來控制流量/將流量轉向到適當 VDC 中的 Azure VM 和 Web Apps。 流量管理員即使在整個 Azure 區域失敗還是可以恢復，而且可以根據數個準則來控制不同 VDC 中服務端點的使用者流量分配 (例如，特定 VDC 中服務的失敗，或選取具有用戶端最低網路延遲的 VDC)。

### <a name="conclusion"></a>結論
虛擬資料中心是將資料中心移轉至雲端的方法，而此雲端合併使用在 Azure 中建立可擴充架構的特色和功能，以最大化雲端資源使用、降低成本，並簡化系統控管。 VDC 概念是以中樞支點拓撲為基礎，並提供中樞中的一般共用服務，以及允許支點中的特定應用程式/工作負載。 VDC 符合公司角色的結構，其中，不同部門 (中央 IT、DevOps、作業和維護) 會一起工作，而且每個部門都會有特定角色和職責清單。 VDC 滿足「隨即轉移」移轉的需求，但也提供原生雲端部署的許多優點。

## <a name="references"></a>參考
本文件已討論下列功能。 請按一下連結以深入了解。

| | | |
|-|-|-|
|網路功能|負載平衡|連線能力|
|[Azure 虛擬網路][VNet]</br>[網路安全性群組][NSG]</br>[NSG 記錄][NSGLog]</br>[使用者定義路由][UDR]</br>[網路虛擬設備][NVA]</br>[公用 IP 位址][PIP]</br>[DNS]|[Azure Load Balancer (L3) ][ALB]</br>[應用程式閘道 (L7) ][AppGW]</br>[Web 應用程式防火牆][WAF]</br>[Azure 流量管理員][TM] |[VNet 對等][VNetPeering]</br>[虛擬私人網路][VPN]</br>[ExpressRoute][ExR]
|身分識別</br>|監視</br>|最佳做法</br>|
|[Azure Active Directory][AAD]</br>[Multi-Factor Authentication][MFA]</br>[角色型存取控制][RBAC]</br>[預設 AAD 角色][Roles] |[Azure 監視器][Monitor]</br>[活動記錄][ActLog]</br>[診斷記錄][DiagLog]</br>[Microsoft Operations Management Suite][OMS]</br>[網路效能監視器][NPM]|[周邊網路最佳做法][DMZ]</br>[訂用帳戶管理][SubMgmt]</br>[資源群組管理][RGMgmt]</br>[Azure 訂用帳戶限制][Limits] |
|其他 Azure 服務|
|[Azure Web Apps][WebApps]</br>[HDInsights (Hadoop) ][HDI]</br>[事件中樞][EventHubs]</br>[服務匯流排][ServiceBus]|



## <a name="next-steps"></a>後續步驟
 - 探索 [VNet 對等][VNetPeering]，其為 VDC 中樞和支點設計的支持技術
 - 實作 [AAD][AAD]，以開始使用 [RBAC][RBAC] 探索。
 - 開發訂用帳戶和資源管理模型與 RBAC 模型，以符合組織的結構、需求和原則。 正在規劃最重要的活動。 請盡快規劃重組、合併、新的產品線等。

<!--Image References-->
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
[NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[SubMgmt]: /azure/azure-resource-manager/resource-manager-azure-scaffold 
[RGMgmt]: /azure/azure-resource-manager/resource-group-overview
[DMZ]: /azure/best-practices-network-security
[ALB]: /azure/load-balancer/load-balancer-overview
[PIP]: /azure/virtual-network/resource-groups-networking#public-ip-address
[AppGW]: /azure/application-gateway/application-gateway-introduction
[WAF]: /azure/application-gateway/application-gateway-web-application-firewall-overview
[Monitor]: /azure/monitoring-and-diagnostics/
[ActLog]: /azure/monitoring-and-diagnostics/monitoring-overview-activity-logs 
[DiagLog]: /azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs
[NSGLog]: /azure/virtual-network/virtual-network-nsg-manage-log
[OMS]: /azure/operations-management-suite/operations-management-suite-overview
[NPM]: /azure/log-analytics/log-analytics-network-performance-monitor
[WebApps]: /azure/app-service/
[HDI]: /azure/hdinsight/hdinsight-hadoop-introduction
[EventHubs]: /azure/event-hubs/event-hubs-what-is-event-hubs 
[ServiceBus]: /azure/service-bus-messaging/service-bus-messaging-overview
[TM]: /azure/traffic-manager/traffic-manager-overview

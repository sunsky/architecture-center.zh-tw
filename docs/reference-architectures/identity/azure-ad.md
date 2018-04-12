---
title: 整合內部部署 AD 網域與 Azure Active Directory
description: 如何使用 Azure Active Directory 實作安全的混合式網路架構。
author: telmosampaio
pnp.series.title: Identity management
ms.date: 11/28/2016
pnp.series.next: adds-extend-domain
pnp.series.prev: ./index
cardTitle: Integrate on-premises AD with Azure AD
ms.openlocfilehash: 431de4b2e08c79f70cc9830fda8315e07bf22c64
ms.sourcegitcommit: c441fd165e6bebbbbbc19854ec6f3676be9c3b25
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2018
---
# <a name="integrate-on-premises-active-directory-domains-with-azure-active-directory"></a>整合內部部署 Active Directory 網域與 Azure Active Directory

Azure Active Directory (Azure AD) 是雲端式的多租用戶目錄和身分識別服務。 此參考架構會顯示最佳做法，供您整合內部部署 Active Directory 網域與 Azure AD 以提供雲端式身分識別驗證。 [**部署這個解決方案**。](#deploy-the-solution)

[![0]][0] 

下載這個架構的 [Visio 檔案][visio-download]。

> [!NOTE]
> 為求簡化，此圖只顯示與 Azure AD 直接相關的連線，而不顯示可能會在驗證和身分識別同盟期間發生的通訊協定相關流量。 例如，Web 應用程式可能會將網頁瀏覽器重新導向為透過 Azure AD 驗證要求。 通過驗證之後，即可將要求傳回給 Web 應用程式，其中會具有適當的身分識別資訊。
> 

此參少架構的典型使用案例包括：

* 部署在 Azure 中的 Web 應用程式，其可存取隸屬於貴組織的遠端使用者。
* 為使用者實作自助式功能，例如重設其密碼，以及委派群組管理。 請注意，這需要 Azure AD Premium 版本。
* 內部部署網路和應用程式的 Azure VNet 未使用 VPN 通道或 ExpressRoute 線路連線的架構。

> [!NOTE]
> Azure AD 目前僅支援使用者驗證。 某些應用程式和服務 (例如 SQL Server) 可能需要電腦驗證，但這並非此解決方案的適用情況。
> 

如需了解其他考量，請參閱[選擇解決方案以整合內部部署 Active Directory 與 Azure][considerations]。 

## <a name="architecture"></a>架構

此架構具有下列元件。

* **Azure AD 租用戶**。 這是貴組織所建立之 [Azure AD][azure-active-directory] 的執行個體。 它可以儲存從內部部署 Active Directory 所複製過來的物件以作為雲端應用程式的目錄服務，並且也可以提供識別服務。
* **Web 層子網路**。 此子網路會保存執行 Web 應用程式的 VM。 Azure AD 可作為此應用程式的身分識別 Broker。
* **內部部署 AD DS 伺服器**。 內部部署目錄和識別服務。 AD DS 目錄可與 Azure AD 同步，以便讓它能夠驗證內部部署使用者。
* **Azure AD Connect 同步處理伺服器**。 執行 [Azure AD Connect][azure-ad-connect] 同步處理服務的內部部署電腦。 此服務會將保留在內部部署 Active Directory 的資訊同步處理至 Azure AD。 例如，如果您佈建或取消佈建內部部署群組和使用者，這些變更會傳播至 Azure AD。 
  
  > [!NOTE]
  > 為求安全，Azure AD 會將使用者的密碼儲存為雜湊。 如果使用者需要重設密碼，這必須在內部部署環境中執行，而且必須將新的雜湊傳送至 Azure AD。 Azure AD Premium 版本有功能可自動執行這項工作，讓使用者能夠重設自己的密碼。
  > 

* **多層式架構 (N-tier) 應用程式的 VM**。 此部署包含多層式架構應用程式的基礎結構。 如需這些資源的詳細資訊，請參閱[執行多層式架構的 VM][implementing-a-multi-tier-architecture-on-Azure]。

## <a name="recommendations"></a>建議

下列建議適用於大部分的案例。 除非您有特定的需求會覆寫它們，否則請遵循下列建議。 

### <a name="azure-ad-connect-sync-service"></a>Azure AD Connect 同步處理服務

Azure AD Connect 同步處理服務可確保雲端中儲存的身分識別資訊，會與保留在內部部署環境中的資訊一致。 您可以使用 Azure AD Connect 軟體安裝此服務。 

在實作 Azure AD Connect 同步處理之前，請判斷貴組織的同步處理需求。 例如，要同步處理哪些項目、從哪個網域，以及多久進行一次。 如需詳細資訊，請參閱[判斷目錄同步處理需求][aad-sync-requirements]。

您可以在 VM 或裝載於內部部署環境的電腦上，執行 Azure AD Connect 同步處理服務。 根據 Active Directory 目錄中資訊的變動性，在首次與 Azure AD 同步處理之後，Azure AD Connect 同步處理服務的負載不會太高。 在 VM 上執行服務，可讓您在需要時輕鬆地調整伺服器。 請依照＜監視考量＞一節所述監視 VM 上的活動，以判斷是否有必要調整。

如果您的樹系中有多個內部部署網域，建議您將整個樹系的資訊儲存起來，並同步處理至單一 Azure AD 租用戶。 篩選在多個網域中出現之身分識別的資訊，讓每個身分識別只在 Azure AD 中出現一次，而不是重複出現。 重複出現會導致同步處理資料時發生不一致的情形。 如需詳細資訊，請參閱下面的＜拓撲＞一節。 

使用篩選，以便只將必要的資料儲存在 Azure AD。 例如，貴組織可能不會想要將非使用中帳戶的相關資訊儲存在 Azure AD 中。 您可以進行群組型、網域型、組織單位 (OU) 型或屬性型篩選。 您可以結合多個篩選器，以產生更複雜的規則。 例如，您可以對網域所保有、在選取的屬性中具有特定值的物件進行同步處理。 如需詳細資訊，請參閱 [Azure AD Connect 同步處理：設定篩選][aad-filtering]。

若要為 AD Connect 同步處理服務實作高可用性功能，請執行次要的預備伺服器。 如需詳細資訊，請參閱＜拓撲建議＞一節。

### <a name="security-recommendations"></a>安全性建議

**使用者密碼管理。** Azure AD Premium 版本支援密碼回寫，此功能可讓您的內部部署使用者從 Azure 入口網站內執行自助式密碼重設作業。 請在檢閱過貴組織的密碼安全性原則後，才啟用此功能。 例如，您可以限制哪些使用者可以變更其密碼，並可以量身打造密碼管理體驗。 如需詳細資訊，請參閱[自訂密碼管理以符合您的組織的需求][aad-password-management]。 

**保護可從外部存取的內部部署應用程式。** 使用 Azure AD 應用程式 Proxy 可對外部使用者提供透過 Azure AD 存取內部部署 Web 應用程式的服務，且其存取權會受到控制。 只有擁有您 Azure 目錄有效認證的使用者有權使用應用程式。 如需詳細資訊，請參閱[在 Azure 入口網站中啟用應用程式 Proxy][aad-application-proxy]一文。

**主動監視 Azure AD 中是否有可疑活動的跡象。**    請考慮使用 Azure AD Premium P2 版本，其包含 Azure AD Identity Protection。 Identity Protection 會使用調適性機器學習服務演算法和啟發學習法，來偵測異常以及可能表示身分識別已遭入侵的風險事件。 例如，它可以偵測到潛在的不尋常活動，例如異常登入活動、從不明來源或從具有可疑活動之 IP 位址進行的登入，或是從可能受感染的裝置進行的登入。 Identity Protection 會使用此資料來產生報告和警示，讓您調查這些風險事件並採取適當動作。 如需詳細資訊，請參閱 [Azure Active Directory Identity Protection][aad-identity-protection]。
  
您可以在 Azure 入口網站中使用 Azure AD 的報告功能，監視系統中所發生的安全性相關活動。 如需如何使用這些報告的詳細資訊，請參閱 [Azure Active Directory 報告指南][aad-reporting-guide]。

### <a name="topology-recommendations"></a>拓撲建議

請設定 Azure AD Connect，以實作與貴組織的需求最相符的拓撲。 Azure AD Connect 所支援的拓撲如下：

* **單一樹系、單一 Azure AD 目錄**。 在此拓撲中，Azure AD Connect 會將物件和身分識別資訊從單一內部部署樹系中的一或多個網域，同步處理至單一 Azure AD 租用戶。 這是 Azure AD Connect 的快速安裝會實作的預設拓撲。
  
  > [!NOTE]
  > 除非您要以預備模式執行伺服器，否則請勿使用多個 Azure AD Connect 同步處理伺服器將相同內部部署樹系中的不同網域連線到相同的 Azure AD 租用戶，如下所述。
  > 
  > 

* **多樹系、單一 Azure AD 目錄**。 在此拓撲中，Azure AD Connect 會將物件和身分識別資訊從多個樹系同步處理至單一 Azure AD 租用戶。 如果貴組織有多個內部部署樹系，請使用此拓撲。 您可以合併身分識別資訊，讓每個唯一的使用者只會在 Azure AD 目錄中顯示一次，即使同一名使用者存在於多個樹系亦然。 所有樹系都會使用相同的 Azure AD Connect 同步處理伺服器。 Azure AD Connect 同步處理伺服器不必屬於任何網域，但必須可從所有樹系加以連線。
  
  > [!NOTE]
  > 在此拓撲中，請勿使用不同的 Azure AD Connect 同步處理伺服器將每個內部部署樹系連線至單一 Azure AD 租用戶。 如果使用者出現在多個樹系中，這可能會導致 Azure AD 中有重複的身分識別資訊。
  > 
  > 

* **多個樹系，個別拓撲**。 此拓撲會將不同樹系的身分識別資訊合併至單一 Azure AD 租用戶，將所有樹系視為不同的實體。 如果您要合併不同組織的樹系，而且每個使用者的身分識別資訊只保留在一個樹系中，此拓撲會很實用。
  
  > [!NOTE]
  > 如果每個樹系中的全域通訊清單 (GAL) 都進行同步處理，某個樹系中的使用者可能會出現在另一個樹系中作為連絡人。 如果貴組織已使用 Forefront Identity Manager 2010 或 Microsoft Identity Manager 2016 實作 GALSync，便可能發生此情形。 在此案例中，您可以指定應該以 Mail 屬性識別使用者。 您也可以使用 *ObjectSID* 和 *msExchMasterAccountSID* 屬性比對身分。 如果您的一或多個資源樹系具有已停用的帳戶，則此拓撲很實用。
  > 
  > 

* **預備伺服器**。 在此組態中，您會以平行方式，連同第一個執行個體來執行 Azure AD Connect 同步處理伺服器的第二個執行個體。 此結構支援的案例如下：
  
  * 高可用性。
  * 測試和部署 Azure AD Connect 同步處理伺服器的新組態。
  * 引進新伺服器，並解除舊組態。 
    
    在這些案例中，第二個執行個體會以「預備模式」執行。 伺服器會記錄其資料庫中所匯入的物件和同步處理資料，但不會將資料傳遞至 Azure AD。 如果您停用預備模式，伺服器會開始將資料寫入到 Azure AD，並且還會在情況合適時，執行將密碼回寫到內部部署目錄的作業。 如需詳細資訊，請參閱 [Azure AD Connect 同步處理：作業工作和考量][aad-connect-sync-operational-tasks]。

* **多個 Azure AD 目錄**。 雖然我們會建議您為組織建立單一的 Azure AD 目錄，但有時候您可能需要將資訊分割到不同的 Azure AD 目錄。 在此情況下，請避免發生同步處理和密碼回寫問題，方法是確保內部部署樹系的每個物件只會出現在一個 Azure AD 目錄。 若要實作此案例，請為每個 Azure AD 目錄設定不同的 Azure AD Connect 同步處理伺服器，並使用篩選功能，讓每個 Azure AD Connect 同步處理伺服器在一組互斥的物件上運作。 

如需這些拓撲的詳細資訊，請參閱 [Azure AD Connect 的拓撲][aad-topologies]。

### <a name="user-authentication"></a>使用者驗證

根據預設，Azure AD Connect 同步處理伺服器會在內部部署網域與 Azure AD 之間設定密碼同步處理，而且 Azure AD 服務會假設使用者是藉由提供他們在內部部署環境所使用的相同密碼進行驗證。 對許多組織來說，這是適當的方式，但您也應該考慮到組織現有的原則和基礎結構。 例如︰

* 貴組織的安全性原則可能會禁止將密碼雜湊同步處理至雲端。
* 您可能會要求使用者在從公司網路上已加入網域的機器中存取雲端資源時，使用無縫式單一登入 (SSO)。
* 貴組織可能已部署 Active Directory 同盟服務 (AD FS) 或第三方同盟提供者。 您可以將 Azure AD 設定為使用此基礎結構，以實作驗證和 SSO，而不是使用雲端中保留的密碼資訊。

如需詳細資訊，請參閱 [Azure AD Connect 使用者登入選項][aad-user-sign-in]。

### <a name="azure-ad-application-proxy"></a>Azure AD 應用程式 Proxy 

使用 Azure AD 提供內部部署應用程式的存取權。

請使用 Azure AD 應用程式 Proxy 元件所管理的應用程式 Proxy 連接器，公開您的內部部署 Web 應用程式。 應用程式 Proxy 連接器會開啟連往 Azure AD 應用程式 Proxy 的輸出網路連線，而且遠端使用者的要求會透過此連線，從 Azure AD 往回路由傳送至 Web 應用程式。 這可讓您不再需要於內部部署防火牆開啟輸入連接埠，並減少貴組織所暴露的受攻擊面。

如需詳細資訊，請參閱[使用 Azure AD 應用程式 Proxy 發佈應用程式][aad-application-proxy]。

### <a name="object-synchronization"></a>物件同步處理 

Azure AD Connect 的預設組態會根據 [Azure AD Connect 同步處理：了解預設組態][aad-connect-sync-default-rules]一文所指定的規則，同步處理來自您本機 Active Directory 目錄的物件。 符合這些規則的物件會進行同步處理，其他所有物件則會予以忽略。 以下是某些範例規則：

* 使用者物件必須有唯一的 sourceAnchor 屬性，且必須填入 accountEnabled 屬性。
* 使用者物件必須具有 sAMAccountName 屬性，且開頭的文字不可以是「Azure AD_」或「MSOL_」。

Azure AD Connect 會對使用者、連絡人、群組、ForeignSecurityPrincipal 和電腦等物件套用數個規則。 如果您需要修改一組預設規則，請使用隨 Azure AD Connect 一起安裝的同步處理規則編輯器。 如需詳細資訊，請參閱 [Azure AD Connect 同步處理：了解預設組態][aad-connect-sync-default-rules]。

您也可以定義您自己的篩選，以限制要依網域或 OU 同步處理的物件。 或者，您也可以實作更複雜的自訂篩選，例如 [Azure AD Connect 同步處理：設定篩選][aad-filtering]所述的篩選。

### <a name="monitoring"></a>監視 

下列安裝在內部部署環境的代理程式會執行健康情況監視：

* Azure AD Connect 會安裝代理程式以擷取同步處理作業的相關資訊。 使用 Azure 入口網站中的 [Azure AD Connect Health] 刀鋒視窗，來監視其健康情況和效能。 如需詳細資訊，請參閱[使用 Azure AD Connect Health 進行同步處理][aad-health]。
* 若要從 Azure 監視 AD DS 網域和目錄的健康情況，請在位於內部部署網域內的機器上安裝 AD DS 代理程式的 Azure AD Connect Health。 使用 Azure 入口網站中的 [Azure Active Directory Connect Health] 刀鋒視窗來監視健康情況。 如需詳細資訊，請參閱[在 AD DS 使用 Azure AD Connect Health][aad-health-adds] 
* 安裝 AD FS 代理程式的 Azure AD Connect Health 來監視於內部部署環境上執行之服務的健康情況，並使用 Azure 入口網站中的 [Azure Active Directory Connect Health] 刀鋒視窗來監視 AD FS。 如需詳細資訊，請參閱[在 AD FS 使用 Azure AD Connect Health][aad-health-adfs]

如需安裝 AD Connect Health 代理程式和其需求的詳細資訊，請參閱 [Azure AD Connect Health 代理程式安裝][aad-agent-installation]。

## <a name="scalability-considerations"></a>延展性考量

Azure AD 服務會根據複本支援延展性，其具有單一主要複本可處理寫入作業，以及多個次要的唯讀複本。 Azure AD 會明確地將針對次要複本所嘗試的寫入，重新導向至主要複本，並提供最終一致性。 針對主要複本進行的所有變更都會傳播到次要複本。 此架構可順暢調整，因為針對 Azure AD 進行的大多數作業都是讀取，而不是寫入。 如需詳細資訊，請參閱 [Azure AD：異地備援、高可用性、分散式雲端目錄的幕後][aad-scalability]。

針對 Azure AD Connect 同步處理伺服器，請決定您可能要從本機目錄同步處理多少物件。 如果您的物件少於 100,000 個，您可以使用 Azure AD Connect 所提供的預設 SQL Server Express LocalDB 軟體。 如果您有大量物件，則應該安裝 SQL Server 的生產版本，並執行 Azure AD Connect 的自訂安裝，以指定其應該使用現有的 SQL Server 執行個體。

## <a name="availability-considerations"></a>可用性考量

Azure AD 服務會異地分散，並且會在分處世界各地的多個資料中心執行，並提供自動容錯移轉功能。 如果資料中心變得無法使用，Azure AD 會確保您的目錄資料可在至少兩個以上分散各區域的資料中心內供執行個體存取。

> [!NOTE]
> Azure AD Basic 和 Premium 服務的服務等級協定 (SLA) 會保證至少 99.9% 的可用性。 Azure AD 免費層則沒有 SLA。 如需詳細資訊，請參閱 [Azure Active Directory 的 SLA][sla-aad]。
> 
> 

請考慮以預備模式佈建 Azure AD Connect 同步處理伺服器的第二個執行個體以提高可用性，如＜拓撲建議＞一節所述。 

如果您未使用 Azure AD Connect 隨附的 SQL Server Express LocalDB 執行個體，請考慮使用 SQL 叢集以實現高可用性。 Azure AD Connect 不支援鏡像和 Always On 之類的解決方案。

如需實現 Azure AD Connect 同步處理伺服器高可用性的其他考量，以及要了解如何在失敗後復原，請參閱 [Azure AD Connect 同步處理：作業工作和考量 - 災害復原][aad-sync-disaster-recovery]。

## <a name="manageability-considerations"></a>管理性考量

Azure AD 的管理有兩個層面：

* 管理雲端中的 Azure AD。
* 維護 Azure AD Connect 同步處理伺服器。

Azure AD 提供了下列選項供您管理雲端中的網域和目錄： 

* **Azure Active Directory PowerShell 模組**。 如果您需要為常見的 Azure AD 管理工作 (例如，使用者管理、網域管理和設定單一登入) 編寫指令碼，請使用此[模組][aad-powershell]。
* **Azure 入口網站中的 [Azure AD 管理] 刀鋒視窗**。 此刀鋒視窗會提供互動式的目錄管理檢視，可讓您控制及設定 Azure AD 的大多數層面。 

Azure AD Connect 會安裝下列工具，以從您的內部部署機器維護 Azure AD Connect 同步處理服務：
  
* **Microsoft Azure Active Directory Connect 主控台**。 此工具可讓您修改 Azure AD 同步處理伺服器的組態、自訂進行同步處理的方式、啟用或停用預備模式，以及切換使用者登入模式。 請注意，您可以使用您的內部部署基礎結構來啟用 Active Directory FS 登入。
* **同步處理服務管理員**。 使用此工具中的 [作業] 索引標籤來管理同步處理程序，以及偵測處理程序是否有任何部分失敗。 您可以使用此工具來手動觸發同步處理。 [連接器] 索引標籤可讓您控制同步處理引擎所連結之網域的連線。
* **同步處理規則編輯器**。 使用此工具來自訂物件於內部部署目錄和 Azure AD 之間進行複製時的轉換方式。 此工具可讓您指定同步處理的其他屬性及物件，然後執行篩選以判斷哪些物件應該或不應該同步處理。 如需詳細資訊，請參閱 [Azure AD Connect 同步處理：了解預設組態][aad-connect-sync-default-rules]文件中的＜同步處理規則編輯器＞一節。

如需管理 Azure AD Connect 的詳細資訊和秘訣，請參閱 [Azure AD Connect 同步處理：變更預設組態的最佳做法][aad-sync-best-practices]。

## <a name="security-considerations"></a>安全性考量

使用條件式存取控制來拒絕非預期來源所提出的驗證要求：

- 如果使用者嘗試從非受信任的位置 (例如經過網際網路而不是受信任的網路) 連線，則觸發 [Azure Multi-Factor Authentication (MFA)][azure-multifactor-authentication]。

- 使用使用者的裝置平台類型 (iOS、Android、Windows Mobile、Windows) 來判斷應用程式和功能的存取原則。

- 記錄使用者裝置的啟用/停用狀態，並將這項資訊合併到存取原則檢查。 例如，如果使用者的電話遺失或遭竊，則應該將它記錄為停用，以防止有人使用它獲得存取權。

- 根據群組成員資格來控制使用者的資源存取權。 使用 [Azure AD 動態成員資格規則][aad-dynamic-membership-rules] 來簡化群組管理。 如需其運作方式的簡短概觀，請參閱[群組動態成員資格簡介][aad-dynamic-memberships]。

- 使用條件式存取風險原則與 Azure AD Identity Protection，以根據異常登入活動或其他事件來提供進階保護。

如需詳細資訊，請參閱 [Azure Active Directory 條件式存取][aad-conditional-access]。

## <a name="deploy-the-solution"></a>部署解決方案

GitHub 中有實作這些建議和考量的參考架構部署。 此參考架構會在 Azure 部署模擬的內部部署網路，供您進行測試和實驗。 您可以使用 Windows 或 Linux VM 部署此參考架構，請遵循下列指示： 

1. 按一下下方的按鈕：<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fidentity%2Fazure-ad%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
2. 一旦連結已在 Azure 入口網站中開啟，您必須輸入部分設定的值： 
   * **資源群組**名稱已在參數檔案中定義，因此請在文字方塊中選取 [新建] 並輸入 `ra-aad-onpremise-rg`。
   * 從 [位置] 下拉式方塊選取區域。
   * 請勿編輯 [範本的根 URI] 或 [參數根 URI] 文字方塊。
   * 在 [作業系統類型] 下拉式方塊中選取 [Windows] 或 [Linux]。
   * 檢閱條款和條件，然後按一下 [我同意上方所述的條款及條件] 核取方塊。
   * 按一下 [購買] 按鈕。
3. 等待部署完成。
4. 參數檔包含硬式編碼的系統管理員使用者名稱和密碼，強烈建議您立即在所有 VM 上變更這兩者。 按一下 Azure 入口網站中的每個 VM，然後按一下 [支援與疑難排解] 刀鋒視窗中的 [重設密碼]。 選取 [模式] 下拉式清單方塊中的 [重設密碼]，然後選取新的[使用者名稱] 和 [密碼]。 按一下 [更新] 按鈕，保存新的使用者名稱和密碼。

<!-- links -->

[implementing-a-multi-tier-architecture-on-Azure]: ../virtual-machines-windows/n-tier.md

[aad-agent-installation]: /azure/active-directory/active-directory-aadconnect-health-agent-install
[aad-application-proxy]: /azure/active-directory/active-directory-application-proxy-enable
[aad-conditional-access]: /azure/active-directory//active-directory-conditional-access
[aad-connect-sync-default-rules]: /azure/active-directory/active-directory-aadconnectsync-understanding-default-configuration
[aad-connect-sync-operational-tasks]: /azure/active-directory/active-directory-aadconnectsync-operations#staging-mode
[aad-dynamic-memberships]: https://youtu.be/Tdiz2JqCl9Q
[aad-dynamic-membership-rules]: /azure/active-directory/active-directory-accessmanagement-groups-with-advanced-rules
[aad-editions]: /azure/active-directory/active-directory-editions
[aad-filtering]: /azure/active-directory/active-directory-aadconnectsync-configure-filtering
[aad-health]: /azure/active-directory/active-directory-aadconnect-health-sync
[aad-health-adds]: /azure/active-directory/active-directory-aadconnect-health-adds
[aad-health-adfs]: /azure/active-directory/active-directory-aadconnect-health-adfs
[aad-identity-protection]: /azure/active-directory/active-directory-identityprotection
[aad-password-management]: /azure/active-directory/active-directory-passwords-customize
[aad-powershell]: https://msdn.microsoft.com/library/azure/mt757189.aspx
[aad-reporting-guide]: /azure/active-directory/active-directory-reporting-guide
[aad-scalability]: https://blogs.technet.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/
[aad-sync-best-practices]: /azure/active-directory/active-directory-aadconnectsync-best-practices-changing-default-configuration
[aad-sync-disaster-recovery]: /azure/active-directory/active-directory-aadconnectsync-operations#disaster-recovery
[aad-sync-requirements]: /azure/active-directory/active-directory-hybrid-identity-design-considerations-directory-sync-requirements
[aad-topologies]: /azure/active-directory/active-directory-aadconnect-topologies
[aad-user-sign-in]: /azure/active-directory/active-directory-aadconnect-user-signin
[azure-active-directory]: /azure/active-directory-domain-services/active-directory-ds-overview
[azure-ad-connect]: /azure/active-directory/active-directory-aadconnect
[azure-multifactor-authentication]: /azure/multi-factor-authentication/multi-factor-authentication
[considerations]: ./considerations.md
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[sla-aad]: https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/
[visio-download]: https://archcenter.blob.core.windows.net/cdn/identity-architectures.vsdx


[0]: ./images/azure-ad.png "使用 Azure Active Directory 的雲端身分識別架構"
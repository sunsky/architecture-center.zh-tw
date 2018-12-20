---
title: 將內部部署 AD FS 擴充至 Azure
titleSuffix: Azure Reference Architectures
description: 如何在 Azure 中使用 Active Directory 同盟服務授權來實作安全的混合式網路架構。
author: telmosampaio
ms.date: 11/28/2016
ms.custom: seodec18
ms.openlocfilehash: 95866961cd92f44e0925c5e47eafdc5df71652db
ms.sourcegitcommit: 88a68c7e9b6b772172b7faa4b9fd9c061a9f7e9d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53120215"
---
# <a name="extend-active-directory-federation-services-ad-fs-to-azure"></a>將 Active Directory 同盟服務 (AD FS) 擴充至 Azure

此參考架構會實作安全的混合式網路，可將您的內部部署網路擴充至 Azure，並使用 [Active Directory 同盟服務 (AD FS)][active-directory-federation-services] 來執行同盟驗證，以及授權 Azure 中執行的元件。 [**部署這個解決方案**](#deploy-the-solution)。

![使用 Active Directory 保護混合式網路架構的安全](./images/adfs.png)

下載這個架構的 [Visio 檔案][visio-download]。

AD FS 可在內部部署主控，但如果是當中有些組件實作在 Azure 中的混合式應用程式，在雲端中複寫 AD FS 可能會更有效率。

下圖顯示下列情節：

- 夥伴組織中的應用程式程式碼會存取 Azure VNet 內主控的 web 應用程式。
- 已外部註冊、且認證儲存在 Active Directory Domain Services (DS) 的使用者，可存取 Azure VNet 內主控的 web 應用程式。
- 使用授權裝置連線至 VNet 的使用者會執行 Azure VNet 內主控的 web 應用程式。

此架構的典型使用案例包括：

- 部分工作負載在內部部署中執行、部分在 Azure 中執行的混合式應用程式。
- 使用同盟授權向夥伴組織公開 web 應用程式的解決方案。
- 支援從組織防火牆外部執行網頁瀏覽器之存取的系統。
- 讓使用者能夠從授權的外部裝置 (例如遠端電腦、筆記型電腦和其他行動裝置) 連線來存取 web 應用程式的系統。

此參考架構著重於被動同盟，同盟伺服器會在當中決定如何及何時要驗證使用者。 使用者在應用程式啟動時，要提供登入資訊。 這項機制最常受網頁瀏覽器使用，且涉及將瀏覽器重新導向至使用者驗證之站台的通訊協定。 AD FS 也支援主動同盟，其中應用程式會負責提供認證，而不需要使用者互動，但這種情節是在此架構範圍之外。

如需了解其他考量，請參閱[選擇解決方案以整合內部部署 Active Directory 與 Azure][considerations]。-

## <a name="architecture"></a>架構

此架構會擴充[將 AD DS 延伸至 Azure][extending-ad-to-azure]中所述的實作。 它包含下列元件。

- **AD DS 子網路**。 AD DS 伺服器會包含在自己的子網路中，且會將網路安全性群組 (NSG) 規則作為防火牆。

- **AD DS 伺服器**。 在 Azure 中作為 VM 執行的網域控制站。 這些伺服器會提供網域內本機識別身分的驗證。

- **AD FS 子網路**。 AD FS 伺服器都位於自己的子網路內，且將 NSG 規則作為防火牆。

- **AD FS 伺服器**。 AD FS 伺服器會提供同盟授權和驗證。 在此架構中，他們會執行下列工作：

  - 接收安全性權杖，當中包含由代表夥伴使用者之夥伴同盟伺服器進行的宣告。 AD FS 會先驗證權杖為有效後，才能將宣告傳遞至 Azure 中執行的 web 應用程式來授權要求。

    在 Azure 中執行的 web 應用程式是信賴憑證者。 夥伴同盟伺服器必須發出 web 應用程式可解讀的宣告。 夥伴同盟伺服器稱為帳戶夥伴，因為它們會代表夥伴組織中的驗證帳戶提交存取要求。 AD FS 伺服器稱為資源夥伴，因為它們提供資源 (web 應用程式) 的存取權。

  - 驗證並授權來自執行網頁瀏覽器或裝置 (需要 web 應用程式的存取權) 之外部使用者的內送要求，方法是使用 AD DS 和 [Active Directory 裝置註冊服務][ADDRS]。

  AD FS 伺服器會設定為透過 Azure 負載平衡器存取的伺服器陣列。 這個實作能改善可用性和延展性。 AD FS 伺服器不會直接向網際網路公開。 所有網際網路流量都會透過 AD FS web 應用程式 proxy 伺服器和 DMZ (也稱為周邊網路) 進行篩選。

  如需有關 AD FS 運作方式的詳細資訊，請參閱 [Active Directory 同盟服務概觀][active-directory-federation-services-overview]。 此外，[Azure 中的 AD FS 部署][adfs-intro]文章中包含了詳細的逐步實作。

- **AD FS proxy 子網路**。 AD FS proxy 伺服器可以包含在自己的子網路內，且 NSG 規則會提供保護。 此子網路中的伺服器會透過一組可在 Azure 虛擬網路與網際網路之間提供防火牆的網路虛擬設備，向網路虛擬公開。

- **AD FS Web 應用程式 proxy (WAP) 伺服器**。 這些 VM 會作為 AD FS 伺服器，接收來自夥伴組織和外部裝置的內送要求。 WAP 伺服器會作為篩選條件，保護來自網際網路的直接存取 AD FS 伺服器。 如同 AD FS 伺服器，在伺服器陣列中使用負載平衡部署 WAP 伺服器，可為您提供比部署獨立伺服器的集合更高的可用性和延展性。

  > [!NOTE]
  > 如需安裝 WAP 伺服器的詳細資訊，請參閱[安裝及設定 Web 應用程式 Proxy 伺服器][install_and_configure_the_web_application_proxy_server]
  >

- **夥伴組織**。 執行 web 應用程式的夥伴組織要求存取 Azure 中執行的 web 應用程式。 夥伴組織中的同盟伺服器會在本機驗證要求，並提交安全性權杖，當中包含對 Azure 中執行的 AD FS 之宣告。 在 Azure 中的 AD FS 會驗證安全性權杖，且如果有效，就可將宣告傳遞至 Azure 中執行的 web 應用程式來授權它們。

  > [!NOTE]
  > 您也可以使用 Azure 閘道來設定 VPN 通道，為受信任的夥伴提供 AD FS 的直接存取。 從這些夥伴收到的要求不會通過 WAP 伺服器。
  >

如需與 AD FS 不相關之架構組件的詳細資訊，請參閱下列各項：

- [在 Azure 中實作安全的混合式網路架構][implementing-a-secure-hybrid-network-architecture]
- [在 Azure 中使用網際網路存取實作安全的混合式網路架構][implementing-a-secure-hybrid-network-architecture-with-internet-access]
- [在 Azure 中使用 Active Directory 身分識別來實作安全的混合式網路架構][extending-ad-to-azure]。

## <a name="recommendations"></a>建議

下列建議適用於大部分的案例。 除非您有特定的需求會覆寫它們，否則請遵循下列建議。

### <a name="vm-recommendations"></a>VM 建議

使用足夠的資源來建立 VM，以處理預期的流量。 使用內部部署主控 AD FS 的現有電腦大小作為起點。 監視資源使用率。 您可以調整 VM 大小，並在太大時相應減少。

請遵循[在 Azure 上執行 Windows VM][vm-recommendations]中所列的建議。

### <a name="networking-recommendations"></a>網路功能的建議

針對每個主控具有靜態私人 IP 位址之 AD FS 和 WAP 伺服器的 VM 設定網路介面。

請勿提供 AD FS VM 公用 IP 位址。 如需詳細資訊，請參閱安全性調整一節。

針對每個 AD FS 和 WAP VM 的網路介面，設定偏好及次要網域名稱服務 (DNS) 伺服器，來參考 Active Directory DS VM。 Active Directory DS VM 應執行 DNS。 這是讓每個 VM 加入網域的必要步驟。

### <a name="ad-fs-availability"></a>AD FS 可用性

使用至少兩部伺服器建立 AD FS 伺服器陣列，以提升服務的可用性。 在伺服器陣列中針對每個 AD FS VM 使用不同的儲存體帳戶。 這種方式有助於確保單一儲存體帳戶中的失敗不會使整個伺服器陣列無法存取。

> [!IMPORTANT]
> 建議使用[受控磁碟](/azure/storage/storage-managed-disks-overview)。 受控磁碟不需要儲存體帳戶。 您只需指定磁碟的大小和類型，並以高度可用的方式來部署它。 我們的[參考架構](/azure/architecture/reference-architectures/)目前不會部署受控磁碟，但[範本建置組塊](https://github.com/mspnp/template-building-blocks/wiki)將在第 2 版中更新，以部署受控磁碟。

建立AD FS 和 WAP VM 的個別 Azure 可用性設定組。 請確保每個集合中至少有兩個 VM。 每個可用性設定組必須至少有兩個更新網域和兩個容錯網域。

設定 AD FS VM 和 WAP VM 的負載平衡器，如下所示：

- 使用 Azure 負載平衡器來提供 WAP VM 的外部存取權，以及內部負載平衡器可在伺服器陣列中的 AD FS 伺服器之間發佈負載。
- 只會將出現在連接埠 443 (HTTPS) 上的流量傳遞至 AD FS/WAP 伺服器。
- 為負載平衡器提供靜態 IP 位址。
- 使用 HTTP 針對 `/adfs/probe` 建立健康情況探查。 如需詳細資訊，請參閱[硬體負載平衡器健康情況檢查和 Web 應用程式 Proxy / AD FS 2012 R2](https://blogs.technet.microsoft.com/applicationproxyblog/2014/10/17/hardware-load-balancer-health-checks-and-web-application-proxy-ad-fs-2012-r2/)。

  > [!NOTE]
  > AD FS 伺服器會使用伺服器名稱指示 (SNI) 通訊協定，因此使用 HTTPS 端點從負載平衡器嘗試探查就會失敗。
  >

- 將 DNS A 記錄新增到 AD FS 負載平衡器的網域。 指定負載平衡器的 IP 位址，並在網域 (例如 adfs.contoso.com) 中為其提供名稱。 這是用戶端和 WAP 伺服器用來存取 AD FS 伺服器陣列的名稱。

### <a name="ad-fs-security"></a>AD FS 安全性

防止 AD FS 伺服器直接暴露在網際網路。 AD FS 伺服器是已加入網域的電腦，具有完整權限可授與安全性權杖。 如果伺服器遭到入侵，惡意使用者可以向所有 web 應用程式以及所有受到 AD FS 保護的同盟伺服器發出完整存取權杖。 如果您系統必須處理的要求不是從受信任夥伴站台連線的外部使用者，請使用 WAP 伺服器來處理這些要求。 如需詳細資訊，請參閱[要將同盟伺服器 Proxy 放置在何處][where-to-place-an-fs-proxy]。

將 AD FS 伺服器和 WAP 伺服器與其本身的防火牆放在不同的子網路中。 您可以使用 NSG 規則來定義防火牆規則。 如果您需要更完整的保護，可以使用一組子網路和網路虛擬設備 (NVA) 來實作其他的安全性周邊，如[在 Azure 中使用網際網路存取實作安全的混合式網路架構][implementing-a-secure-hybrid-network-architecture-with-internet-access]文件中所述。 所有防火牆都應該允許連接埠 443 (HTTPS) 流量。

限制直接登入存取 AD FS 和 WAP 伺服器。 應該只有 DevOps 工作人員能夠連線。

請勿將 WAP 伺服器加入網域。

### <a name="ad-fs-installation"></a>AD FS 安裝

[部署同盟伺服器陣列][Deploying_a_federation_server_farm]文章會提供安裝和設定 AD FS 的詳細指示。 在伺服器陣列中設定第一部 AD FS 伺服器之前，請執行下列工作：

1. 取得公開受信任的憑證以執行伺服器驗證。 主體名稱必須包含用戶端用來存取同盟服務的名稱。 這可以是負載平衡器的已註冊 DNS 名稱，例如 adfs.contoso.com (基於安全性理由，請避免使用萬用字元名稱，例如 *.contoso.com)。 在所有 AD FS 伺服器 VM 上使用相同的憑證。 您可以向受信任的憑證授權單位購買憑證，但是如果貴組織是使用 Active Directory 憑證服務，您就可以建立自己的憑證。

    主體別名會由裝置註冊服務 (DRS) 用來啟用來自外部裝置的存取。 此形式應該是 enterpriseregistration.contoso.com。

    如需詳細資訊，請參閱[取得和設定 AD FS 的安全通訊端層 (SSL) 憑證][adfs_certificates]。

2. 在網域控制站上，產生金鑰發佈服務的新根金鑰。 將有效時間設為目前時間減去 10 小時 (這個設定可減少跨網域發佈及同步處理金鑰時可能會發生的延遲)。 若要支援建立用來執行 AD FS 服務的群組服務帳戶，此為必要步驟。 下列 PowerShell 命令會顯示如何執行這項操作的範例：

    ```powershell
    Add-KdsRootKey -EffectiveTime (Get-Date).AddHours(-10)
    ```

3. 將每個 AD FS 伺服器 VM 新增至網域。

> [!NOTE]
> 若要安裝 AD FS，針對網域執行主要網域控制站 (PDC) 模擬器彈性單一主機作業 (FSMO) 角色的網域控制站必須執行且從 AD FS VM 存取。 <<RBC：是否有方法可讓此較少重複？>>
>

### <a name="ad-fs-trust"></a>AD FS 信任

在 AD FS 安裝與任何夥伴組織的同盟伺服器之間建立同盟信任。 設定篩選和對應所需的任何宣告。

- 每個夥伴組織的 DevOps 人員必須新增信賴憑證者信任，才能透過 AD FS 伺服器存取 web 應用程式。
- 貴組織中的 DevOps 人員必須設定宣告提供者信任，才能讓 AD FS 伺服器信任夥伴組織所提供的宣告。
- 貴組織中的 DevOps 人員也必須設定 AD FS，才能將宣告傳遞至貴組織的 web 應用程式。

如需詳細資訊，請參閱[建立同盟信任][establishing-federation-trust]。

透過 WAP 伺服器使用預先驗證來發佈貴組織的 web 應用程式，並讓外部夥伴可使用它們。 如需詳細資訊，請參閱[使用 AD FS 預先驗證來發佈應用程式][publish_applications_using_AD_FS_preauthentication]

AD FS 支援權杖轉換和增強指定。 Azure Active Directory 不提供這項功能。 利用 AD FS，當您設定信任關係時，可以：

- 設定授權規則的宣告轉換。 例如，您可以將來自非 Microsoft 夥伴組織使用之表示法的群組安全性對應到 Active Directory DS 可在貴組織中授權的項目。
- 將宣告從一種格式轉換成另一種格式。 例如，如果您的應用程式只支援 SAML 1.1 宣告，您可以從 SAML 2.0 對應到 SAML 1.1。

### <a name="ad-fs-monitoring"></a>AD FS 監視

[適用於 Active Directory 同盟服務 2012 R2 的 Microsoft System Center Management 套件][oms-adfs-pack]會提供適用於同盟伺服器的 AD FS 部署之主動式及回應式監視。 此管理組件會監視：

- AD FS 服務在其事件記錄中記錄的事件。
- AD FS 效能計數器收集的效能資料。
- AD FS 系統和 web 應用程式 (信賴憑證者) 的整體健康情況，並提供重大問題和警告的警示。

## <a name="scalability-considerations"></a>延展性考量

下列考量摘要自[規劃 AD FS 部署][plan-your-adfs-deployment]文章，提供一個調整 AD FS 伺服器陣列大小的起始點：

- 如果您的使用者低於 1000 個，請勿建立專用的伺服器，而是改為在雲端中的每個 Active Directory DS 伺服器上安裝 AD FS。 請確定您有至少兩部 Active Directory DS 伺服器以維持可用性。 建立單一 WAP 伺服器。
- 如果您的使用者介於 1000 和 15000 個之間，請建立兩部專用的 AD FS 伺服器和兩部專用的 WAP 伺服器。
- 如果您的使用者介於 15000 與 60000 個之間，請建立三到五部專用的 AD FS 伺服器，和至少兩部專用的 WAP 伺服器。

這些考量是假設您在 Azure 中使用雙四核心 VM (標準 D4_v2 或更新版本) 的大小。

如果您是使用 Windows 內部資料庫來儲存 AD FS 組態資料，在伺服器陣列中就會限制為八個 AD FS 伺服器。 如果您預期未來會需要更多，請使用 SQL Server。 如需詳細資訊，請參閱 [AD FS 設定資料庫的角色][adfs-configuration-database]。

## <a name="availability-considerations"></a>可用性考量

您可以使用 SQL Server 或 Windows 內部資料庫來保存 AD FS 組態資訊。 Windows 內部資料庫會提供基本的備援性。 變更只會直接寫入 AD FS 叢集中的其中一個 AD FS 資料庫，而其他伺服器會使用提取複寫，讓其資料庫保持最新狀態。 使用 SQL Server，可以在使用容錯移轉叢集或鏡像時提供完整的資料庫備援性和高可用性。

## <a name="manageability-considerations"></a>管理性考量

DevOps 人員應該準備好執行下列工作：

- 管理同盟伺服器，包括管理 AD FS 伺服器陣列、管理同盟伺服器上的信任原則，和管理同盟服務所使用的憑證。
- 管理 WAP 伺服器包括管理 WAP 伺服器陣列和憑證。
- 管理 web 應用程式，包括設定信賴憑證者、驗證方法及宣告對應。
- 備份 AD FS 元件。

## <a name="security-considerations"></a>安全性考量

AD FS 會使用 HTTPS 通訊協定，因此請確定包含 web 層 VM 的子網路之 NSG 規則允許 HTTPS 要求。 可從內部部署網路、包含 web 層的子網路、商業層、資料層、私人 DMZ、公用 DMZ，以及包含 AD FS 伺服器的子網路等起始這些要求。

請考慮使用一組網路虛擬設備，可就稽核目的，記錄關於流量周遊虛擬網路邊緣的詳細資訊。

## <a name="deploy-the-solution"></a>部署解決方案

部署此參考架構的解決方案可在 [GitHub][github] 上取得。 您需要最新版的 [Azure CLI][azure-cli]，才能執行可部署解決方案的 Powershell 指令碼。 若要部署參考架構，請依照下列步驟執行：

1. 將解決方案資料夾從 [GitHub][github] 下載或複製到本機電腦。

2. 開啟 Azure CLI，並瀏覽至本機解決方案資料夾。

3. 執行以下命令：

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```

    使用您的 Azure 訂用帳戶識別碼來取代 `<subscription id>` 。

    針對 `<location>`，請指定 Azure 區域，例如 `eastus` 或 `westus`。

    `<mode>` 參數可精密控制部署，而且可以是下列其中一個值：

   - `Onpremise`：部署模擬的內部部署環境。 如果您沒有任何現有內部部署網路，或如果您需要在不變更現有內部部署網路設定的情況下測試此參考架構，可以使用此部署進行測試和實驗。
   - `Infrastructure`：部署 VNet 基礎結構和跳躍方塊。
   - `CreateVpn`：部署 Azure 虛擬網路閘道，並將其連線到模擬的內部部署網路。
   - `AzureADDS`：部署當作 Active Directory DS 伺服器的 VM、將 Active Directory 部署到這些 VM，然後在 Azure 中建立網域。
   - `AdfsVm`：部署 AD FS VM，並將它們加入 Azure 中的網域。
   - `PublicDMZ`：在 Azure 中部署公用 DMZ。
   - `ProxyVm`：部署 AD FS proxy VM，並將它們加入 Azure 中的網域。
   - `Prepare`：部署所有先前的部署。 **如果您要建置全新的部署，而且沒有任何現有內部部署基礎結構，這就是建議的選項。**
   - `Workload`：選擇性地部署 web、商務和資料層 VM 和支援的網路。 不包含在 `Prepare` 部署模式中。
   - `PrivateDMZ`：選擇性地將 Azure 中的私人 DMZ 部署在以上所部署的 `Workload`VM 前面。 不包含在 `Prepare` 部署模式中。

4. 等待部署完成。 如果您使用 `Prepare` 選項，部署就需要數小時才能完成，且完成時會出現 `Preparation is completed. Please install certificate to all AD FS and proxy VMs.` 訊息

5. 重新啟動跳躍方塊 (ra-adfs-security-rg 群組中的 ra-adfs-mgmt-vm1)，以允許其 DNS 設定生效。

6. [取得 AD FS 的 SSL 憑證][adfs_certificates] 並在 AD FS VM 上安裝這個憑證。 請注意，您可透過跳躍方塊加以連線。 IP 位址為 10.0.5.4 和 10.0.5.5。 預設使用者名稱為 contoso\testuser，密碼為 AweSome@PW。

   > [!NOTE]
   > 此時 Deploy-ReferenceArchitecture.ps1 指令碼中的註解會提供詳細指示，說明如何使用 `makecert` 命令來建立自我簽署的測試憑證和授權單位。 不過，僅執行這些步驟作為**測試**，並且請勿在生產環境中使用 makecert 所產生的憑證。

7. 執行下列 PowerShell 命令來部署 AD FS 伺服器陣列：

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Adfs
    ```

8. 在跳躍方塊中，瀏覽至 `https://adfs.contoso.com/adfs/ls/idpinitiatedsignon.htm` 來測試 AD FS 安裝 (可能會收到憑證，警告您可以忽略這項測試)。 確認出現 Contoso Corporation 登入頁面。 以 contoso\testuser 身分登入，密碼為 AweS0me@PW。

9. 在 AD FS proxy VM 上安裝 SSL 憑證。 IP 位址為 10.0.6.4 和 10.0.6.5。

10. 執行下列 PowerShell 命令來部署第一個 AD FS Proxy 伺服器：

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy1
    ```

11. 依照指令碼所顯示的指示來測試安裝第一個 Proxy 伺服器。

12. 執行下列 PowerShell 命令來部署第二個 Proxy 伺服器：

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy2
    ```

13. 依照指令碼所顯示的指示來測試完整 Proxy 設定。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure Active Directory][aad]。
- 了解 [Azure Active Directory B2C][aadb2c]。

<!-- links -->
[extending-ad-to-azure]: adds-extend-domain.md

[vm-recommendations]: ../virtual-machines-windows/single-vm.md
[implementing-a-secure-hybrid-network-architecture]: ../dmz/secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ../dmz/secure-vnet-dmz.md
[hybrid-azure-on-prem-vpn]: ../hybrid-networking/vpn.md

[azure-cli]: /azure/azure-resource-manager/xplat-cli-azure-resource-manager
[DRS]: https://technet.microsoft.com/library/dn280945.aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048.aspx
[ADDRS]: https://technet.microsoft.com/library/dn486831.aspx
[plan-your-adfs-deployment]: https://msdn.microsoft.com/library/azure/dn151324.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[adfs_certificates]: https://technet.microsoft.com/library/dn781428(v=ws.11).aspx
[create_service_account_for_adfs_farm]: https://technet.microsoft.com/library/dd807078.aspx
[adfs-configuration-database]: https://technet.microsoft.com/library/ee913581(v=ws.11).aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[active-directory-federation-services-overview]: https://technet.microsoft.com/library/hh831502(v=ws.11).aspx
[establishing-federation-trust]: https://blogs.msdn.microsoft.com/alextch/2011/06/27/establishing-federation-trust/
[Deploying_a_federation_server_farm]:  /windows-server/identity/ad-fs/deployment/deploying-a-federation-server-farm
[install_and_configure_the_web_application_proxy_server]: https://technet.microsoft.com/library/dn383662.aspx
[publish_applications_using_AD_FS_preauthentication]: https://technet.microsoft.com/library/dn383640.aspx
[managing-adfs-components]: https://technet.microsoft.com/library/cc759026.aspx
[oms-adfs-pack]: https://www.microsoft.com/download/details.aspx?id=41184
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[aad]: https://azure.microsoft.com/documentation/services/active-directory/
[aadb2c]: https://azure.microsoft.com/documentation/services/active-directory-b2c/
[adfs-intro]: /azure/active-directory/hybrid/whatis-hybrid-identity
[github]: https://github.com/mspnp/identity-reference-architectures/tree/master/adfs
[adfs_certificates]: https://technet.microsoft.com/library/dn781428(v=ws.11).aspx
[considerations]: ./considerations.md
[visio-download]: https://archcenter.blob.core.windows.net/cdn/identity-architectures.vsdx

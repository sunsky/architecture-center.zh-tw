---
title: 在 Azure 中建立 AD DS 資源樹系
description: >-
  如何在 Azure 中建立受信任的 Active Directory 網域。

  指引,vpn 閘道,expressroute,負載平衡器,虛擬網路,active directory
author: telmosampaio
ms.date: 11/28/2016
pnp.series.title: Identity management
pnp.series.prev: adds-extend-domain
pnp.series.next: adfs
cardTitle: Create an AD DS forest in Azure
ms.openlocfilehash: e32a6420821e70c84e77d2c39614f0c45efbb7e2
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="create-an-active-directory-domain-services-ad-ds-resource-forest-in-azure"></a>在 Azure 中建立 Active Directory Domain Services (AD DS) 資源樹系

此參考架構會顯示如何在 Azure 中建立受到內部部署 AD 樹系內網域信任的另一個 Active Directory 網域。 [**部署這個解決方案**。](#deploy-the-solution)

[![0]][0] 

下載這個架構的 [Visio 檔案][visio-download]。

Active Directory Domain Services (AD DS) 會以階層式結構儲存身分識別資訊。 階層式結構中的最上層節點稱為樹系。 樹系包含網域，而網域則包含其他類型的物件。 此參考架構會在 Azure 中建立與內部部署網域具有單向連出信任關係的 AD DS 樹系。 Azure 中的樹系包含不存在於內部部署的網域。 因為信任關係，才能夠信任針對內部部署網域的登入，以存取個別 Azure 網域中的資源。 

這個架構的典型用途包括維持雲端中保留之物件和身分識別的安全性隔離，以及將個別網域從內部部署移轉到雲端。 

如需了解其他考量，請參閱[選擇解決方案以整合內部部署 Active Directory 與 Azure][considerations]。 

## <a name="architecture"></a>架構

此架構具有下列元件。

* **內部部署網路**。 內部部署網路包含自己的 Active Directory 樹系和網域。
* **Active Directory 伺服器**。 這些是雲端中實作當作 VM 執行之網域服務的網域控制站。 這些伺服器會裝載包含一個或多個網域的樹系，而且與內部部署的網域不同。
* **單向信任關係**。 圖表中的範例顯示 Azure 中網域到內部部署網域的單向信任。 這種關係可讓內部部署使用者在 Azure 中存取網域內的資源，但不適用於反向。 如果雲端使用者也需要存取內部部署資源，您可以建立雙向信任。
* **Active Directory 子網路**。 AD DS 伺服器會裝載在不同的子網路中。 網路安全性群組 (NSG) 規則可保護 AD DS 伺服器，並提供防火牆以防禦來自非預期來源的流量。
* **Azure 閘道**。 Azure 閘道會提供內部部署網路與 Azure VNet 之間的連線。 這可能是 [VPN 連線][azure-vpn-gateway]或 [Azure ExpressRoute][azure-expressroute]。 如需詳細資訊，請參閱[在 Azure 中實作安全的混合式網路架構][implementing-a-secure-hybrid-network-architecture]。

## <a name="recommendations"></a>建議

如需在 Azure 中實作 Active Directory 的特定建議，請參閱下列文章：

- [將 Active Directory Domain Services (AD DS) 擴充至 Azure][adds-extend-domain]。 
- [在 Azure 虛擬機器上部署 Windows Server Active Directory 的指導方針][ad-azure-guidelines]。

### <a name="trust"></a>信任

內部部署網域包含在雲端網域中的不同樹系內。 若要在雲端驗證內部部署使用者，Azure 中的網域必須信任內部部署樹系中的登入網域。 同樣地，如果雲端為外部使用者提供登入網域，內部部署樹系也需要信任雲端網域。

您可以在樹系層級[建立樹系信任][creating-forest-trusts]，也可以在網域層級[建立外部信任][creating-external-trusts]來建立信任。 樹系層級信任會建立兩個樹系中所有網域之間的關係。 外部網域層級信任只會建立兩個指定網域之間的關係。 您僅應在不同樹系的網域之間建立外部網域層級信任。

信任可以是單向或雙向：

* 單向信任可讓一個網域或樹系 (稱為「連入」網域或樹系) 中的使用者存取另一個網域或樹系 (「連出」網域或樹系) 中保留的資源。
* 雙向信任可讓網域或樹系中的使用者存取其他網域或樹系中保留的資源。

下表摘要說明一些簡單案例的信任設定：

| 案例 | 內部部署信任 | 雲端信任 |
| --- | --- | --- |
| 內部部署使用者需要存取雲端的資源，但反之則否 |單向，連入 |單向，連出 |
| 雲端使用者需要存取內部部署的資源，但反之則否 |單向，連出 |單向，連入 |
| 雲端和內部部署的使用者需要同時存取雲端和內部部署保留的資源 |雙向，連入和連出 |雙向，連入和連出 |

## <a name="scalability-considerations"></a>延展性考量

Active Directory 會針對屬於相同網域的網域控制站自動進行調整。 要求會分散到網域內的所有控制站。 您可以新增另一個網域控制站，而且該網域控制站會自動與網域同步。 請勿將個別的負載平衡器設定為將流量導向至網域內的控制站。 請確定所有網域控制站都有足夠的記憶體和儲存體資源，可處理網域資料庫。 將所有網域控制站 VM 的大小設為相同。

## <a name="availability-considerations"></a>可用性考量

針對每個網域至少佈建兩個網域控制站。 如此可在伺服器之間啟用自動複寫。 針對當作處理每個網域之 Active Directory 伺服器的 VM，建立可用性設定組。 在此可用性設定組中至少放置兩部伺服器。

此外，如果作為彈性單一主機操作 (FSMO) 角色之伺服器的連線失敗，請考慮將每個網域中的一部或多部伺服器指定為[待命操作主機][standby-operations-masters]。

## <a name="manageability-considerations"></a>管理性考量

如需管理與監視考量的相關資訊，請參閱[將 Active Directory 擴充至 Azure][adds-extend-domain]。 
 
如需其他資訊，請參閱[監視 Active Directory][monitoring_ad]。 您可以在管理子網路中的監視伺服器上安裝工具 (例如 [Microsoft Systems Center][microsoft_systems_center]) 以協助執行這些工作。

## <a name="security-considerations"></a>安全性考量

樹系層級信任是可轉移的。 如果您在內部部署樹系與雲端樹系之間建立樹系層級信任，此信任會擴充至任一樹系中建立的其他新網域。 如果您基於安全性目的而使用網域提供區隔，請考慮僅在網域層級建立信任。 網域層級信任是不可轉移的。

如需了解 Active Directory 專屬的安全性考量，請參閱[將 Active Directory 擴充至 Azure][adds-extend-domain] 中的＜安全性考量＞一節。

## <a name="deploy-the-solution"></a>部署解決方案

部署此參考架構的解決方案可在 [GitHub][github] 上取得。 您需要最新版的 Azure CLI，才能執行可部署解決方案的 PowerShell 指令碼。 若要部署參考架構，請依照下列步驟執行：

1. 將解決方案資料夾從 [GitHub][github] 下載或複製到本機電腦。

2. 開啟 Azure CLI，並瀏覽至本機解決方案資料夾。

3. 執行以下命令：
   
    ```Powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```
   
    使用您的 Azure 訂用帳戶識別碼來取代 `<subscription id>` 。
   
    針對 `<location>`，請指定 Azure 區域，例如 `eastus` 或 `westus`。
   
    `<mode>` 參數可精密控制部署，而且可以是下列其中一個值：
   
   * `Onpremise`：部署模擬的內部部署環境。
   * `Infrastructure`：在 Azure 中部署 VNet 基礎結構和跳躍箱。
   * `CreateVpn`：部署 Azure 虛擬網路閘道，並將其連線到模擬的內部部署網路。
   * `AzureADDS`：部署當作 Active Directory DS 伺服器的 VM、將 Active Directory 部署到這些 VM，然後在 Azure 中部署網域。
   * `WebTier`：部署 Web 層 VM 和負載平衡器。
   * `Prepare`：部署所有先前的部署。 **如果您沒有現有的內部部署網路，但您想要部署上述的完整參考架構以供測試或評估之用，這是建議的選項。** 
   * `Workload`：部署商務和資料層 VM 以及負載平衡器。 請注意，這些 VM 不包含在 `Prepare` 部署中。

4. 等待部署完成。 如果您要部署 `Prepare` 部署，將需要數小時的時間。
     
5. 如果您要使用模擬的內部部署設定，請設定連入信任關係：
   
   1. 連線到跳躍箱 (<em>ra-adtrust-security-rg</em> 資源群組中的 <em>ra-adtrust-mgmt-vm1</em>)。 以 <em>testuser</em> 的身分和密碼 <em>AweS0me@PW</em> 登入。
   2. 在跳躍箱上，開啟 <em>contoso.com</em> 網域 (內部部署網域) 第一個 VM 中的 RDP 工作階段。 此 VM 的 IP 位址為 192.168.0.4。 使用者名稱為 <em>contoso\testuser</em>，且密碼為 <em>AweS0me@PW</em>。
   3. 下載 [incoming-trust.ps1][incoming-trust] 指令碼並執行，以便從 *treyresearch.com* 網域建立連入信任。

6. 如果您要使用自己的內部部署基礎結構：
   
   1. 下載 [incoming-trust.ps1][incoming-trust] 指令碼。
   2. 編輯指令碼，並將 `$TrustedDomainName` 變數的值取代為您自己網域的名稱。
   3. 執行指令碼。

7. 從跳躍箱連線至 <em>treyresearch.com</em> 網域 (雲端中的網域) 中的第一個 VM。 此 VM 的 IP 位址為 10.0.4.4。 使用者名稱為 <em>treyresearch\testuser</em>，且密碼為 <em>AweS0me@PW</em>。

8. 下載 [outgoing-trust.ps1][outgoing-trust] 指令碼並加以執行，以便從 *treyresearch.com* 網域建立連入信任。 如果您要使用自己的內部部署電腦，請先編輯指令碼。 將 `$TrustedDomainName` 變數設為您內部部署網域的名稱，並使用 `$TrustedDomainDnsIpAddresses` 變數，針對此網域指定 Active Directory DS 伺服器的 IP 位址。

9. 等待幾分鐘，讓上述步驟完成，然後連線至內部部署 VM，並執行[驗證信任][verify-a-trust]一文中所述的步驟，來判斷 *contoso.com* 和 *treyresearch.com* 網域之間的信任關係是否已正確設定。

## <a name="next-steps"></a>後續步驟

* 了解[將內部部署 AD DS 網域擴充至 Azure][adds-extend-domain] 的最佳做法
* 了解[在 Azure 中建立 AD FS 基礎結構][adfs]的最佳做法。

<!-- links -->
[adds-extend-domain]: adds-extend-domain.md
[adfs]: adfs.md

[implementing-a-secure-hybrid-network-architecture]: ../dmz/secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ../dmz/secure-vnet-dmz.md

[running-VMs-for-an-N-tier-architecture-on-Azure]: ../virtual-machines-windows/n-tier.md

[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[considerations]: ./considerations.md
[creating-external-trusts]: https://technet.microsoft.com/library/cc816837(v=ws.10).aspx
[creating-forest-trusts]: https://technet.microsoft.com/library/cc816810(v=ws.10).aspx
[github]: https://github.com/mspnp/reference-architectures/tree/master/identity/adds-forest
[incoming-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/identity/adds-forest/extensions/incoming-trust.ps1
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/identity/adds-forest/Deploy-ReferenceArchitecture.ps1
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[outgoing-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/identity/adds-forest/extensions/outgoing-trust.ps1
[verify-a-trust]: https://technet.microsoft.com/library/cc753821.aspx
[visio-download]: https://archcenter.blob.core.windows.net/cdn/identity-architectures.vsdx
[0]: ./images/adds-forest.png "使用不同的 Active Directory 網域保護混合式網路架構的安全"
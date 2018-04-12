---
title: 在 Azure 中實作安全的混合式網路架構
description: 如何在 Azure 中實作安全的混合式網路架構。
author: telmosampaio
ms.date: 11/23/2016
pnp.series.title: Network DMZ
pnp.series.prev: ./index
pnp.series.next: secure-vnet-dmz
cardTitle: DMZ between Azure and on-premises
ms.openlocfilehash: 81dea2e4439d5a01ebb88ab86dc0a59609bb7bc3
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="dmz-between-azure-and-your-on-premises-datacenter"></a>Azure 與內部部署資料中心之間的 DMZ

此參考架構顯示的安全混合式網路，可將內部部署網路擴充至 Azure。 此架構會在內部部署網路與 Azure 虛擬網路 (VNet) 之間實作 DMZ (也稱為「周邊網路」)。 DMZ 包含實作安全性功能 (例如防火牆和封包檢查) 的網路虛擬裝置 (NVA)。 VNet 的所有傳出流量會強制通過內部部署網路後再傳送至網際網路，以便進行稽核。

[![0]][0] 

下載這個架構的 [Visio 檔案][visio-download]。

此架構需要連線到內部部署資料中心，可使用 [VPN 閘道][ra-vpn]或 [ExpressRoute][ra-expressroute]連線。 此架構的典型使用案例包括：

* 部分工作負載在內部部署中執行、部分在 Azure 中執行的混合式應用程式。
* 針對從內部部署資料中心進入 Azure VNet 的流量，需要進行更精確控管的基礎結構。
* 必須稽核傳出流量的應用程式。 這通常是許多商業系統的管理需求，並且有助於防止個人資訊遭到公開洩漏。

## <a name="architecture"></a>架構

此架構由下列元件組成。

* **內部部署網路**。 在組織內實作的私人區域網路。
* **Azure 虛擬網路 (VNet)**。 VNet 會裝載在 Azure 中執行的應用程式和其他資源。
* **閘道**。 閘道會提供內部部署網路與 VNet 中路由器之間的連線。
* **網路虛擬設備 (NVA)**。 NVA 是統稱，用來描述執行以下這類工作的虛擬機器：作為防火牆來允許或拒絕存取、最佳化廣域網路 (WAN) 作業 (包括網路壓縮)、自訂路由或其他網路功能等。
* **Web 層、商務層和資料層子網路**。 裝載虛擬機器和服務的子網路，其會實作在雲端中執行的 3 層應用程式範例。 如需詳細資訊，請參閱[在 Azure 上執行適用於多層式架構的 Windows 虛擬機器][ra-n-tier]。
* **使用者定義路由 (UDR)**。 [使用者定義路由][udr-overview]會定義 Azure VNet 內 IP 流量的流程。

    > [!NOTE]
    > 根據您 VPN 連線的需求，您可以設定邊界閘道協定 (BGP) 路由，而不是使用 UDR 來實作轉送規則，透過內部部署網路將流量導回。
    > 
    > 

* **管理子網路。** 此子網路包含的虛擬機器會針對 VNet 中執行的元件實作管理和監視功能。

## <a name="recommendations"></a>建議

下列建議適用於大部分的案例。 除非您有特定的需求會覆寫它們，否則請遵循下列建議。 

### <a name="access-control-recommendations"></a>存取控制建議

使用[角色型存取控制] [rbac] (RBAC) 來管理您應用程式中的資源。 請考慮建立下列[自訂角色][rbac-custom-roles]：

- DevOps 角色，有權管理應用程式的基礎結構、部署應用程式元件，以及監視和重新啟動虛擬機器。  

- 中央 IT 管理員角色，管理和監視網路資源。

- 安全性 IT 管理員角色，管理安全的網路資源，例如 NVA。 

DevOps 與 IT 管理員角色應不可有 NVA 資源的存取權。 應僅限安全性 IT 管理員角色可存取。

### <a name="resource-group-recommendations"></a>資源群組建議

將 Azure 資源 (例如 VM、VNet 及負載平衡器) 組成資源群組，您就可以輕鬆地進行管理。 將 RBAC 角色指派給每個資源群組，以限制存取。

我們建議您建立下列資源群組：

* 資源群組中包含 VNet (但不包括虛擬機器)、NSG 和用以連線至內部部署網路的閘道資源。 將中央 IT 管理員角色指派給此資源群組。
* 資源群組中包含 NVA 的虛擬機器 (包括負載平衡器)、jumpbox 和其他管理虛擬機器，以及強制流量通過 NVA 的閘道子網路 UDR。 將安全性 IT 管理員角色指派給此資源群組。
* 針對每個應用程式層分隔資源群組，其中包含負載平衡器和虛擬機器。 請注意，此資源群組不應包含每一層的子網路。 將 DevOps 角色指派給此資源群組。

### <a name="virtual-network-gateway-recommendations"></a>虛擬網路閘道建議

內部部署流量會透過虛擬網路閘道傳遞至 VNet。 我們建議使用 [Azure VPN 閘道][guidance-vpn-gateway]或 [Azure ExpressRoute 閘道][guidance-expressroute]。

### <a name="nva-recommendations"></a>NVA 建議

NVA 提供不同的服務來管理和監視網路流量。 [Azure Marketplace][azure-marketplace-nva] 提供數個第三方廠商的 NVA 供您使用。 如果這些第三方廠商的 NVA 都不符合您的需求，您可以使用虛擬機器建立自訂的 NVA。 

例如，此參考架構的解決方案部署，會在虛擬機器上實作具有下列功能的 NVA：

* 使用 NVA 網路介面 (NIC) 上的 [IP 轉送][ip-forwarding]來路由流量。
* 流量只會在合適的狀況下允許通過 NVA。 參考架構中的每個 NVA 虛擬機器都是簡單的 Linux 路由器。 輸入流量會抵達網路介面 eth0，而輸出流量會符合自訂指令碼定義的規則，自訂指令碼是透過網路介面 eth1 分派。
* NVA 只能從管理子網路進行設定。 
* 路由至管理子網路的流量不會通過 NVA。 否則，如果 NVA 失效，將不會有任何通往管理子網路的路由可修復此問題。  
* NVA 的虛擬機器會置於負載平衡器後方的[可用性設定組][availability-set]。 閘道子網路中的 UDR 會將 NVA 要求導向負載平衡器。

加入第 7 層 NVA 來終止 NVA 層級的應用程式連線，並維持與後端層的同質性。 這可確保對稱的連線，其中來自後端層的回應流量會透過 NVA 傳回。

要考量的另一個選項是與多個 NVA 串聯連線，且讓每個 NVA 執行特殊安全性工作。 這可讓您以每個 NVA 為基礎地管理每個安全性功能。 例如，實作防火牆的 NVA 可與執行身分識別服務的 NVA 串聯在一起。 方便管理的代價是需要額外的網路躍點，而這可能會增加延遲，因此請確定此動作不會影響您的應用程式效能。


### <a name="nsg-recommendations"></a>NSG 建議

VPN 閘道會使連線所用的公用 IP 位址暴露在內部部署網路中。 我們建議您為輸入 NVA 子網路建立網路安全性群組 (NSG)，以及設定規則來封鎖所有不是源自於內部部署網路的流量。

我們也建議針對每個子網路使用 NSG 來提供第二層保護，以避免輸入的流量略過設定不正確或已停用的 NVA。 例如，參考架構中的 Web 層子網路會實作 NSG，並設定一個規則來略過不是從內部部署網路 (192.168.0.0/16) 或 VNet 接收的要求，以及設定另一個規則來忽略不是從連接埠 80 產生的所有要求。

### <a name="internet-access-recommendations"></a>網際網路存取建議

使用站對站 VPN 通道來建立[強制通道][azure-forced-tunneling]，讓所有輸出網際網路流量通過內部部署網路，並使用網路位址轉譯 (NAT) 路由至網際網路。 如此可防止儲存在您資料層的機密資訊意外洩漏，並允許檢查和稽核所有傳出流量。

> [!NOTE]
> 請勿完全封鎖來自應用程式層的網際網路流量，因為這會阻止這些層使用依賴公用 IP 位址的 Azure PaaS 服務，例如虛擬機器診斷記錄和虛擬機器擴充功能下載等功能。 Azure 診斷也需要元件可以讀取和寫入至 Azure 儲存體帳戶。
> 
> 

請確認輸出網際網路流量已正確地使用強制通道。 如果您在內部部署伺服器上使用的 VPN 連線具有[路由及遠端存取服務][routing-and-remote-access-service]，請使用 [WireShark][wireshark] 或 [Microsoft Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) 這類工具。

### <a name="management-subnet-recommendations"></a>管理子網路建議

管理子網路包含可執行管理和監視功能的 jumpbox。 僅限 Jumpbox 可執行所有安全的管理工作。
 
請勿針對 Jumpbox 建立公用 IP 位址。 相反地，建立一個透過傳入閘道存取 jumpbox 的路由。 建立 NSG 規則，讓管理子網路只回應來自許可路由的要求。

## <a name="scalability-considerations"></a>延展性考量

參考架構會使用負載平衡器，來將內部部署網路流量導向會路由流量的 NVA 裝置集區。 NVA 會放置在[可用性設定組][availability-set]中。 此設計可讓您監視一段時間的 NVA 輸送量，並新增 NVA 裝置來回應增加的負載。

標準 SKU VPN 閘道會支援高達 100 Mbps 的持續性輸送量。 高效能 SKU 提供最多 200 Mbps。 如需更高的頻寬，請考慮升級至 ExpressRoute 閘道。 ExpressRoute 提供高達 10 Gbps，並具有比 VPN 連線更低的延遲。

如需有關 Azure 閘道延展性的詳細資訊，請參閱[使用 Azure 和內部部署 VPN 實作混合式網路架構][guidance-vpn-gateway-scalability]和[使用 Azure ExpressRoute 實作混合式網路架構][guidance-expressroute-scalability]中的延展性考量區段。

## <a name="availability-considerations"></a>可用性考量

如前所述，參考架構會使用負載平衡器後方的 NVA 裝置集區。 負載平衡器會使用健康情況探查來監視每個 NVA ，然後從集區中移除任何沒有回應的 NVA。

如果您使用 Azure ExpressRoute 來提供 VNet 和內部部署網路之間的連線，請[設定 VPN 閘道來提供容錯移轉][ra-vpn-failover] (如果 ExpressRoute 連線變得無法使用)。

如需有關維護 VPN 和 ExpressRoute 連線可用性的詳細資訊，請參閱[使用 Azure 和內部部署 VPN 實作混合式網路架構][guidance-vpn-gateway-availability]和[使用 Azure ExpressRoute 實作混合式網路架構][guidance-expressroute-availability]中的可用性考量。 

## <a name="manageability-considerations"></a>管理性考量

應只有管理子網路中的 jumpbox 才能執行所有應用程式和資源的監視。 根據您的應用程式需求，您可能需要管理子網路中的其他監視資源。 如果是的話，應透過 jumpbox 來存取這些資源。

如果從內部部署網路到 Azure 的閘道連線關閉，藉由部署公用 IP 位址、將它加入 jumpbox、然後從網際網路進行遠端登入，仍可連線至 jumpbox。

NSG 規則會保護參考架構中每一層的子網路。 您可能需要建立規則來開啟連接埠 3389，以便在 Windows 虛擬機器上使用遠端桌面通訊協定 (RDP) 的存取，或開啟連接埠 22，以便在 Linux 虛擬機器上使用安全殼層 (SSH) 的存取。 其他管理和監視工具可能需要規則來開啟其他連接埠。

如果您使用 ExpressRoute 來提供您內部部署資料中心與 Azure 之間的連線，請使用 [Azure 連線工具組 (AzureCT)][azurect] 來監視連線問題並進行疑難排解。

您可以在[使用 Azure 和內部部署 VPN 實作混合式網路架構][guidance-vpn-gateway-manageability]和[使用 Azure ExpressRoute 實作混合式網路架構][guidance-expressroute-manageability]的文章中，找到特別針對監視和管理 VPN 與 ExpressRoute 連線的其他資訊。

## <a name="security-considerations"></a>安全性考量

此參考架構實作多個安全性層級。

### <a name="routing-all-on-premises-user-requests-through-the-nva"></a>透過 NVA 路由所有內部部署使用者要求
閘道子網路中的 UDR 會封鎖不是從內部部署收到的所有使用者要求。 UDR 會將允許的要求傳遞至私人 DMZ 子網路中的 NVA，而且如果 NVA 規則允許的話，這些要求會繼續傳遞至應用程式。 您可以將其他路由新增至 UDR，但請確定路由不會不小心略過 NVA，或是封鎖要傳向管理子網路的系統管理流量。

NVA 前方的負載平衡器也可以當做安全性裝置，如果流量不在負載平衡規則中開啟的連接埠上，則將其略過。 參考架構中的負載平衡器只會接聽連接埠 80 上的 HTTP 要求和連接埠 443 上的 HTTPS 要求。 記錄您新增至負載平衡器的任何其他規則，並監視流量以確保沒有安全性問題。

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a>使用 NSG 封鎖/傳遞應用程式層之間的流量
使用 NSG 限制各層之間的流量。 商務層會封鎖不是源自於 Web 層的所有流量，而資料層會封鎖所有不是源自於商務層的所有流量。 如果您需要擴充 NSG 規則以讓這些層接受更廣泛的存取，請評估這些需求的安全性風險。 每個新的輸入路徑都代表著有機會發生意外或有意的資料外洩或應用程式損害。

### <a name="devops-access"></a>DevOps 存取
使用 [RBAC][rbac] 限制 DevOps 可在每一層執行的作業。 授與權限時，請使用[最小權限的原則][security-principle-of-least-privilege]。 記錄所有的系統管理作業並定期執行稽核，以確保所有設定變更都是經過規劃的。

## <a name="solution-deployment"></a>解決方案部署

在 [GitHub][github-folder] 中有實作這些建議的參考架構部署。 您可以遵循下列指示來部署參考架構：

1. 按一下下方的按鈕：<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fdmz%2Fsecure-vnet-hybrid%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
2. 一旦連結已在 Azure 入口網站中開啟，您必須輸入部分設定的值：   
   * **資源群組**名稱已在參數檔案中定義，因此請在文字方塊中選取 [新建] 並輸入 `ra-private-dmz-rg`。
   * 從 [位置] 下拉式方塊選取區域。
   * 請勿編輯 [範本的根 URI] 或 [參數根 URI] 文字方塊。
   * 檢閱條款和條件，然後按一下 [我同意上方所述的條款及條件] 核取方塊。
   * 按一下 [購買] 按鈕。
3. 等待部署完成。
4. 參數檔案中有硬式編碼的所有 VM 的系統管理員使用者名稱和密碼，強烈建議您立即變更這兩項。 在 Azure 入口網站中選取部署中的每個虛擬機器，然後按一下 [支援與疑難排解] 刀鋒視窗中的 [重設密碼]。 選取 [模式] 下拉式清單方塊中的 [重設密碼]，然後選取新的[使用者名稱] 和 [密碼]。 按一下 [更新] 按鈕以儲存。

## <a name="next-steps"></a>後續步驟

* 了解如何[在 Azure 與網際網路之間實作 DMZ](secure-vnet-dmz.md)。
* 了解如何實作[高可用性的混合式網路架構][ra-vpn-failover]。
* 如需有關使用 Azure 管理網路安全性的詳細資訊，請參閱 [Microsoft 雲端服務和網路安全性][cloud-services-network-security]。
* 如需在 Azure 中保護資源的詳細資訊，請參閱[開始使用 Microsoft Azure 安全性][getting-started-with-azure-security]。 
* 若要深入了解如何解決 Azure 閘道連線上的安全性考量，請參閱[使用 Azure 和內部部署 VPN 實作混合式網路架構][guidance-vpn-gateway-security]和[使用 Azure ExpressRoute 實作混合式網路架構][guidance-expressroute-security]。
  > 

<!-- links -->

[availability-set]: /azure/virtual-machines/virtual-machines-windows-create-availability-set
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[azure-forced-tunneling]: https://azure.microsoft.com/en-gb/documentation/articles/vpn-gateway-forced-tunneling-rm/
[azure-marketplace-nva]: https://azuremarketplace.microsoft.com/marketplace/apps/category/networking
[cloud-services-network-security]: https://azure.microsoft.com/documentation/articles/best-practices-network-security/
[getting-started-with-azure-security]: /azure/security/azure-security-getting-started
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/dmz/secure-vnet-hybrid
[guidance-expressroute]: ../hybrid-networking/expressroute.md
[guidance-expressroute-availability]: ../hybrid-networking/expressroute.md#availability-considerations
[guidance-expressroute-manageability]: ../hybrid-networking/expressroute.md#manageability-considerations
[guidance-expressroute-security]: ../hybrid-networking/expressroute.md#security-considerations
[guidance-expressroute-scalability]: ../hybrid-networking/expressroute.md#scalability-considerations
[guidance-vpn-gateway]: ../hybrid-networking/vpn.md
[guidance-vpn-gateway-availability]: ../hybrid-networking/vpn.md#availability-considerations
[guidance-vpn-gateway-manageability]: ../hybrid-networking/vpn.md#manageability-considerations
[guidance-vpn-gateway-scalability]: ../hybrid-networking/vpn.md#scalability-considerations
[guidance-vpn-gateway-security]: ../hybrid-networking/vpn.md#security-considerations
[ip-forwarding]: /azure/virtual-network/virtual-networks-udr-overview#ip-forwarding
[ra-expressroute]: ../hybrid-networking/expressroute.md
[ra-n-tier]: ../virtual-machines-windows/n-tier.md
[ra-vpn]: ../hybrid-networking/vpn.md
[ra-vpn-failover]: ../hybrid-networking/expressroute-vpn-failover.md
[rbac]: /azure/active-directory/role-based-access-control-configure
[rbac-custom-roles]: /azure/active-directory/role-based-access-control-custom-roles
[routing-and-remote-access-service]: https://technet.microsoft.com/library/dd469790(v=ws.11).aspx
[security-principle-of-least-privilege]: https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1
[udr-overview]: /azure/virtual-network/virtual-networks-udr-overview
[visio-download]: https://archcenter.blob.core.windows.net/cdn/dmz-reference-architectures.vsdx
[wireshark]: https://www.wireshark.org/
[0]: ./images/dmz-private.png "安全混合式網路架構"

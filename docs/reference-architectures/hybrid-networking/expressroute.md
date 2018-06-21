---
title: 使用 ExpressRoute 將內部部署網路連線至 Azure
description: 如何在透過 Azure ExpressRoute 連線的 Azure 虛擬網路與內部部署網路間，實作安全的站對站網路架構。
author: telmosampaio
ms.date: 11/28/2016
pnp.series.title: Connect an on-premises network to Azure
pnp.series.next: expressroute-vpn-failover
pnp.series.prev: vpn
cardTitle: ExpressRoute
ms.openlocfilehash: ada07f399925da6da28b24260f5c73f1e106fd7d
ms.sourcegitcommit: c441fd165e6bebbbbbc19854ec6f3676be9c3b25
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2018
ms.locfileid: "30270314"
---
# <a name="connect-an-on-premises-network-to-azure-using-expressroute"></a>使用 ExpressRoute 將內部部署網路連線至 Azure

此參考架構會示範如何使用 [Azure ExpressRoute][expressroute-introduction] 將內部部署網路連線至 Azure 上的虛擬網路。 ExpressRoute 連線會透過第三方連線提供者，使用私人的專用連線。 私人連線會將內部部署網路延伸到 Azure。 [**部署這個解決方案**。](#deploy-the-solution)

![[0]][0]

下載這個架構的 [Visio 檔案][visio-download]。

## <a name="architecture"></a>架構

此架構由下列元件組成。

* **內部部署的公司網路**。 在組織內執行的私用區域網路。

* **ExpressRoute 線路**。 透過邊緣路由器聯結內部部署網路與 Azure 之連線提供者所提供的第 2 層或第 3 層線路。 此線路使用連線提供者所管理的硬體基礎結構。

* **本機邊緣路由器**。 該路由器會將內部部署網路連線至提供者管理的線路。 根據您佈建連線的方式，您可能需要提供路由器使用的公用 IP 位址。
* **Microsoft 邊緣路由器**。 使用主動-主動高可用性設定的兩個路由器。 這類路由器可讓連線提供者將他們的線路直接連線至資料中心。 根據您佈建連線的方式，您可能需要提供路由器使用的公用 IP 位址。

* **Azure 虛擬網路 (VNet)**。 每個 VNet 都位於單一 Azure 區域，而且可以裝載多個應用程式層。 應用程式層可以使用每個 VNet 中的子網路區隔。

* **Azure 公用服務**。 可以在混合式應用程式中使用的 Azure 服務。 這些服務可透過網際網路存取，但使用 ExpressRoute 線路存取這些服務可提供低延遲且更能預測的效能，因為流量不會進出網際網路。 連線會透過您組織所擁有或連線提供者提供的位址，使用[公用對等互連][expressroute-peering]來執行。

* **Office 365 服務**。 由 Microsoft 所提供可公開使用的 Office 365 應用程式和服務。 連線會透過您組織所擁有或連線提供者提供的位址，使用[Microsoft 對等互連][expressroute-peering]來執行。 您也可以透過 Microsoft 對等互連直接連線至 Microsoft CRM Online。

* **連線提供者** (未顯示)。 使用第 2 層和第 3 層連線，在您資料中心和 Azure 資料中心之間提供連線的公司。

## <a name="recommendations"></a>建議

下列建議適用於大部分的案例。 除非您有特定的需求會覆寫它們，否則請遵循下列建議。

### <a name="connectivity-providers"></a>連線提供者

根據您的所在位置，選取適當的 ExpressRoute 連線提供者。 若要取得您所在位置可用的連線提供者清單，請使用下列 Azure PowerShell 命令：

```powershell
Get-AzureRmExpressRouteServiceProvider
```

ExpressRoute 連線提供者會使用下列方法將您的資料中心連線至 Microsoft：

* **共置於雲端 Exchange**。 如果您共置於具有雲端 Exchange 的設施中，您可以訂購虛擬交叉連線，透過共置提供者的乙太網路交換而連線至 Azure。 共置提供者可以在您於共置設施中的基礎結構與 Azure 之間，提供第 2 層交叉連線或受控第 3 層交叉連線。
* **點對點乙太網路連線**。 您可以透過點對點乙太網路連結，將內部部署資料中心/辦公室連線到 Azure。 點對點乙太網路提供者可以在您的網路與 Azure 之間，提供第 2 層連線或受控第 3 層連線。
* **任意點對任意點 (IPVPN) 網路**。 您可以將您的廣域網路 (WAN) 整合至 Azure。 網際網路通訊協定虛擬私人網路 (IPVPN) 提供者 (通常是多重通訊協定標籤交換 VPN) 可在您的分公司與資料中心之間提供任意點對任意點連線。 Azure 可以相互連線到您的 WAN，看起來就像任何其他分公司一樣。 WAN 提供者通常會提供受控第 3 層連線能力。

如需連線提供者的詳細資訊，請參閱 [ExpressRoute 簡介][expressroute-introduction]。

### <a name="expressroute-circuit"></a>ExpressRoute 線路

請確認您的組織符合 [ExpressRoute 必要條件需求][expressroute-prereqs]以連線至 Azure。

如果您尚未這麼做，請將名為 `GatewaySubnet` 的子網路新增至 Azure VNet，並使用 Azure VPN 閘道服務建立 ExpressRoute 虛擬網路閘道。 如需詳細資訊，請參閱 [線路佈建和線路狀態的 ExpressRoute 工作流程][ExpressRoute-provisioning]。

建立 ExpressRoute 線路，如下所示：

1. 執行下列 PowerShell 命令：
   
    ```powershell
    New-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>> -Location <<location>> -SkuTier <<sku-tier>> -SkuFamily <<sku-family>> -ServiceProviderName <<service-provider-name>> -PeeringLocation <<peering-location>> -BandwidthInMbps <<bandwidth-in-mbps>>
    ```
2. 將新線路的 `ServiceKey` 傳送給服務提供者。

3. 等候提供者佈建線路。 若要確認線路的佈建狀態，請執行下列 PowerShell 命令：
   
    ```powershell
    Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
    ```

    當線路就緒時，在輸出的 `Service Provider` 區段中，`Provisioning state` 欄位會從 `NotProvisioned` 變更為 `Provisioned`。

    > [!NOTE]
    > 如果您使用第 3 層連線，提供者應為您設定及管理路由。 您須提供必要資訊，讓提供者實作適當的路由。
    > 
    > 

4. 如果您使用第 2 層連線：

    1. 針對您要實作的每個對等互連類型，保留兩個由有效公用 IP 位址組成的 /30 子網路。 這些 /30 子網路將用來提供線路所需的路由器 IP 位址。 如果您實作私人、公用及 Microsoft 對等互連，您必須有 6 個包含有效公用 IP 位址的 /30 子網路。     

    2. 設定 ExpressRoute 線路的路由。 針對您要設定的每個對等互連類型 (私人、公用和 Microsoft)，執行下列 PowerShell 命令。 如需詳細資訊，請參閱[建立和修改 ExpressRoute 路線的路由][configure-expressroute-routing]。
   
        ```powershell
        Set-AzureRmExpressRouteCircuitPeeringConfig -Name <<peering-name>> -Circuit <<circuit-name>> -PeeringType <<peering-type>> -PeerASN <<peer-asn>> -PrimaryPeerAddressPrefix <<primary-peer-address-prefix>> -SecondaryPeerAddressPrefix <<secondary-peer-address-prefix>> -VlanId <<vlan-id>>

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit <<circuit-name>>
        ```

    3. 保留另一個有效的公用 IP 位址集區，以便用於公用和 Microsoft 對等互連的網路位址轉譯 (NAT)。 建議您針對每個對等互連使用不同集區。 為連線提供者指定集區，讓他們可以設定這些範圍的邊界閘道通訊協定 (BGP) 公告。

5. 執行下列 PowerShell 命令，將您的私人 VNet 連結至 ExpressRoute 線路。 如需詳細資訊，請參閱[將虛擬網路連結到 ExpressRoute 線路][link-vnet-to-expressroute]。

    ```powershell
    $circuit = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
    $gw = Get-AzureRmVirtualNetworkGateway -Name <<gateway-name>> -ResourceGroupName <<resource-group>>
    New-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> -ResourceGroupName <<resource-group>> -Location <<location> -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
    ```

您可以將不同區域中的多個 VNet 連線至相同 ExpressRoute 線路，前提是所有 VNet 和 ExpressRoute 線路皆位於相同的地緣政治區域內。

### <a name="troubleshooting"></a>疑難排解 

如果先前可運作的 ExpressRoute 線路現在無法連線，且內部部署或私人 VNet 內的設定皆沒有變更，則您可能需要連絡連線提供者，並與他們一起修正問題。 您可以使用下列 Powershell 命令來確認 ExpressRoute 線路是否已佈建：

```powershell
Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

此命令的輸出會顯示數個線路屬性，包括 `ProvisioningState`、`CircuitProvisioningState` 和 `ServiceProviderProvisioningState`，如下所示。

```
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : NotProvisioned
```

在您嘗試建立新的線路之後，如果 `ProvisioningState` 未設為 `Succeeded` ，請使用下列命令移除線路，然後嘗試重新建立。

```powershell
Remove-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

如果您的提供者已佈建線路，但 `ProvisioningState` 設為 `Failed`，或 `CircuitProvisioningState` 不是 `Enabled`，請連絡您的提供者，以取得進一步協助。

## <a name="scalability-considerations"></a>延展性考量

ExpressRoute 線路會在網路間提供高頻寬路徑。 一般而言，頻寬較高，成本也會提高。 

ExpressRoute 提供兩個[價格方案][expressroute-pricing]給客戶，計量方案和無限制資料方案。 費用會因為線路頻寬不同而有所差異。 可用的頻寬也可能因為提供者不同而有所差異。 使用 `Get-AzureRmExpressRouteServiceProvider` Cmdlet 可查看您區域中可用的提供者，以及他們所提供的頻寬。
 
單一 ExpressRoute 線路可以支援特定數量的對等互連和 VNet 連結。 如需詳細資訊，請參閱 [ExpressRoute 限制](/azure/azure-subscription-service-limits)。

包含在額外費用中的 ExpressRoute 進階附加元件會提供一些額外的功能：

* 提高公開和私人對等互連的路由限制數量。 
* 提高每個 ExpressRoute 線路的 VNet 連結數目。 
* 從全球連線到服務。

如需詳細資訊，請參閱 [ExpressRoute 價格][expressroute-pricing]。 

在 ExpressRoute 線路的設計中，可允許暫時性網路的頻寬突增為所採購頻寬限制的兩倍，且無需另外付費。 能達成這項功能，是因為使用了備援連結。 不過，並非所有連線提供者都支援這項功能。 需要此功能前，請確認連線提供者已啟用此功能。

雖然有些提供者可讓您變更頻寬，但請確定您挑選的初始頻寬大於您的需求，且具有成長的空間。 如果您未來需要增加頻寬，您有兩個選項：

- 增加頻寬。 您應該盡可能避免使用此選項，並非所有提供者都可讓您隨意地增加頻寬。 但是，如果增加頻寬是必須的，請洽詢您的提供者，確認他們是否支援透過 Powershell 命令變更 ExpressRoute 頻寬屬性。 如果他們支援的話，請執行下列命令。

    ```powershell
    $ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
    $ckt.ServiceProviderProperties.BandwidthInMbps = <<bandwidth-in-mbps>>
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

    您可以在不中斷連線的情況下，增加頻寬。 降低頻寬將會導致連線中斷，因為您必須刪除線路，然後以新的設定重新建立線路。

- 變更您的價格方案及/或升級為進階版。 若要這樣做，請執行下列命令。 `Sku.Tier` 屬性可以是 `Standard` 或 `Premium`；`Sku.Name` 屬性可以是 `MeteredData` 或 `UnlimitedData`。

    ```powershell
    $ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>

    $ckt.Sku.Tier = "Premium"
    $ckt.Sku.Family = "MeteredData"
    $ckt.Sku.Name = "Premium_MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

    > [!IMPORTANT]
    > 請確定 `Sku.Name` 屬性符合 `Sku.Tier` 和 `Sku.Family`。 如果您變更系列和層次，而不是變更名稱，您的連線將會停用。
    > 
    > 

    您可以在不中斷連線的情況下升級 SKU，但您不能從無限制的價格方案切換為計量方案。 將 SKU 降級時，您的頻寬耗用量必須維持在標準 SKU 的預設限制內。

## <a name="availability-considerations"></a>可用性考量

ExpressRoute 不支援透過路由器備援通訊協定來實作高可用性，例如熱待命路由通訊協定 (HSRP) 和虛擬路由器備援通訊協定 (VRRP)。 相反地，它會針對每個對等互連使用一組備援 BGP 工作階段。 為了讓您的網路具有高可用性連線，Azure 會使用主動-主動設定，在兩個路由器上 (Microsoft 邊緣的一部分) 佈建兩個備援連接埠。

依預設，BGP 工作階段會設定 60 秒的閒置逾時值。 如果工作階段逾時 3 次 (共 180 秒)，路由器會標示為無法使用，而所有流量會被重新導向至其餘路由器。 此 180 秒的逾時可能對某些重要應用程式而言太長。 如果是這樣，您可以在內部部署路由器上將 BGP 逾時設定變更為較小的值。

根據您使用的提供者類型，以及 ExpressRoute 線路數量和您要設定的虛擬網路閘道連線數量，您可以使用不同方式來設定 Azure 連線的高可用性。 以下是可用性選項的摘要：

* 如果您使用第 2 層連線，可使用主動-主動設定將備援路由器部署在內部部署網路中。 將主要線路連線至一個路由器，然後將次要線路連線至另一個路由器。 這樣可讓您的連線兩端都有高可用性連線。 如果您需要 ExpressRoute 服務等級協定 (SLA)，這會是必要項目。 如需詳細資訊，請參閱 [Azure ExpressRoute SLA][sla-for-expressroute]。

    下圖顯示的設定中，包含與主要和次要線路連線的備援內部部署路由器。 每個線路都會處理公用對等互連和私人對等互連的流量 (如上一節中所述，每個對等互連會指定一組 /30 的位址空)。

    ![[1]][1]

* 如果您使用第 3 層連線，請確認此連線會為您提供處理可用性的備援 BGP 工作階段。

* 將 VNet 連線至不同服務提供者提供的多個 ExpressRoute 線路。 此策略會提供額外的高可用性和災害復原功能。

* 設定站對站 VPN 作為 ExpressRoute 的容錯移轉路徑。 如需此選像的詳細資訊，請參閱[使用 ExpressRoute 搭配 VPN 容錯移轉，將內部部署網路連線至 Azure][highly-available-network-architecture]。
 此選項僅適用於私人對等互連。 針對 Azure 和 Office 365 服務，網際網路是唯一的容錯移轉路徑。 

## <a name="manageability-considerations"></a>管理性考量

您可以使用 [Azure 連線工具組 (AzureCT)][azurect] 來監視您內部部署資料中心與 Azure 之間的連線。 

## <a name="security-considerations"></a>安全性考量

根據您的安全性考量和相容性需求，您可以使用不同方法來設定 Azure 連線的安全性選項。 

第 3 層中的 ExpressRoute 運作。 透過使用網路安全性裝備，限制流量只能傳送到合法的資源，就可以防止應用程式層中的威脅。 此外，使用公用對等互連的 ExpressRoute 連線只能從內部部署中起始。 這可防止惡意服務的存取，並防止內部部署資料從網際網路洩露出去。

為了盡可能提高安全性，請在內部部署網路與提供者邊緣路由器之間新增網路安全性設備。 這有助於限制未經授權的流量從 VNet 流入：

![[2]][2]

基於稽核或合規性考量，您可能需要禁止從 VNet 中執行的元件直接存取網際網路，以及實作[強制通道][forced-tuneling]。 在此情況下，網際網路流量應透過內部部署中執行的 Proxy 重新導回，如此就可經過稽核。 Proxy 可以設定為封鎖未經授權的流量送出，並篩選潛在的惡意輸入流量。

![[3]][3]

為了盡可能提高安全性，請勿啟用您虛擬機器的公用 IP 位址，並且使用 NSG，以確保這些虛擬機器不可公開存取。 虛擬機器應僅供內部 IP 位址使用。 這些位址可透過 ExpressRoute 網路設定為可存取，讓內部部署的 DevOps 人員可進行設定或維護。

如果您必須在外部網路中公開虛擬機器的管理端點，請使用 NSG 或存取控制清單，以 IP 位址或網路允許清單來限制這些連接埠的可見性。

> [!NOTE]
> 依預設，透過 Azure 入口網站部署的 Azure 虛擬機器包含提供登入存取的公用 IP 位址。  
> 
> 


## <a name="deploy-the-solution"></a>部署解決方案

**必要條件。** 您必須已經使用適當的網路設備，設定現有的內部部署基礎結構。

若要部署解決方案，請執行下列步驟。

1. 按一下下方的按鈕：<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fexpressroute%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
2. 等待此連結在 Azure 入口網站中開啟，然後按照下列步驟進行：
   * **資源群組**名稱已在參數檔案中定義，因此請在文字方塊中選取 [新建] 並輸入 `ra-hybrid-er-rg`。
   * 從 [位置] 下拉式方塊選取區域。
   * 請勿編輯 [範本的根 URI] 或 [參數根 URI] 文字方塊。
   * 檢閱條款和條件，然後按一下 [我同意上方所述的條款及條件] 核取方塊。
   * 按一下 [購買] 按鈕。
3. 等待部署完成。
4. 按一下下方的按鈕：<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fexpressroute%2Fazuredeploy-expressRouteCircuit.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
5. 等待此連結在 Azure 入口網站中開啟，然後按照下列步驟進行：
   * 選取 [資源群組] 區段中的 [使用現有的]，然後在文字方塊中輸入 `ra-hybrid-er-rg`。
   * 從 [位置] 下拉式方塊選取區域。
   * 請勿編輯 [範本的根 URI] 或 [參數根 URI] 文字方塊。
   * 檢閱條款和條件，然後按一下 [我同意上方所述的條款及條件] 核取方塊。
   * 按一下 [購買] 按鈕。
6. 等待部署完成。


<!-- links -->
[forced-tuneling]: ../dmz/secure-vnet-hybrid.md
[highly-available-network-architecture]: ./expressroute-vpn-failover.md

[expressroute-technical-overview]: /azure/expressroute/expressroute-introduction
[expressroute-prereqs]: /azure/expressroute/expressroute-prerequisites
[configure-expressroute-routing]: /azure/expressroute/expressroute-howto-routing-arm
[sla-for-expressroute]: https://azure.microsoft.com/support/legal/sla/expressroute/v1_0/
[link-vnet-to-expressroute]: /azure/expressroute/expressroute-howto-linkvnet-arm
[ExpressRoute-provisioning]: /azure/expressroute/expressroute-workflows
[expressroute-introduction]: /azure/expressroute/expressroute-introduction
[expressroute-peering]: /azure/expressroute/expressroute-circuit-peerings
[expressroute-pricing]: https://azure.microsoft.com/pricing/details/expressroute/
[expressroute-limits]: /azure/azure-subscription-service-limits#networking-limits
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-architectures.vsdx
[er-circuit-parameters]: https://github.com/mspnp/reference-architectures/tree/master/hybrid-networking/expressroute/parameters/expressRouteCircuit.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[0]: ./images/expressroute.png "使用 Azure ExpressRoute 的混合式網路架構"
[1]: ../_images/guidance-hybrid-network-expressroute/figure2.png "搭配 ExpressRoute 主要和次要線路使用備援路由器"
[2]: ../_images/guidance-hybrid-network-expressroute/figure3.png "將安全性裝置新增至內部部署網路"
[3]: ../_images/guidance-hybrid-network-expressroute/figure4.png "使用強制通道來稽核網際網路繫結流量"
[4]: ../_images/guidance-hybrid-network-expressroute/figure5.png "找出 ExpressRoute 線路的 ServiceKey"  
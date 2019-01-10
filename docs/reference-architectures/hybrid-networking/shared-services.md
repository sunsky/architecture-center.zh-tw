---
title: 實作中樞輪輻網路拓撲
titleSuffix: Azure Reference Architectures
description: 在 Azure 中實作中樞輪輻網路拓撲與共用服務。
author: telmosampaio
ms.date: 10/09/2018
ms.custom: seodec18
ms.openlocfilehash: 2ed76e467fd3f65664afa35b6247c83c3f6ce078
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54112221"
---
# <a name="implement-a-hub-spoke-network-topology-with-shared-services-in-azure"></a>在 Azure 中實作中樞輪輻網路拓撲與共用服務

此參考架構是建置在[中樞輪輻][guidance-hub-spoke]參考架構基礎上，以包含可以被所有輪輻取用之中樞中的共用服務。 作為將資料中心移轉至雲端的第一步，並且建置[虛擬資料中心]，您需要共用的首要服務是身分識別和安全性。 此參考架構說明如何從您的內部部署資料中心將 Active Directory 服務擴充至 Azure，以及如何在中樞輪輻拓撲中新增可作為防火牆的網路虛擬設備 (NVA)。  [**部署這個解決方案**](#deploy-the-solution)。

![Azure 中的共用服務拓撲](./images/shared-services.png)

*下載這個架構的 [Visio 檔案][visio-download]*

此拓撲的優點包括：

- **節省成本**：將可由多個工作負載共用的服務 (例如網路虛擬裝置 (NVA) 和 DNS 伺服器) 集中在單一位置。
- **克服訂用帳戶限制**：將不同訂用帳戶中的 VNet 對等互連到中心中樞。
- **關注點分離**：中央 IT (SecOps、InfraOps) 和工作負載 (DevOps)。

此架構的一般使用案例包括：

- 在不同環境 (例如開發、測試和生產環境) 下部署，且需要共用服務 (例如 DNS、IDS、NTP 或 AD DS) 的工作負載。 系統會將共用服務置於中樞 VNet，同時將每個環境部署至輪輻以維持隔離。
- 不需要彼此連線，但需要存取共用服務的工作負載。
- 需要集中控制安全性層面 (例如，在中樞當作 DMZ 的防火牆)，並對每個輪輻中的工作負載進行分離管理的企業。

## <a name="architecture"></a>架構

此架構由下列元件組成。

- **內部部署網路**。 在組織內執行的私用區域網路。

- **VPN 裝置**。 可對內部部署網路提供外部連線的裝置或服務。 VPN 裝置可能是硬體裝置或軟體解決方案，例如，Windows Server 2012 中的路由及遠端存取服務 (RRAS)。 如需支援的 VPN 設備清單，以及設定選取的 VPN 設備以連線至 Azure 的相關資訊，請參閱[關於站對站 VPN 閘道連線的 VPN 裝置][vpn-appliance]。

- **VPN 虛擬網路閘道或 ExpressRoute 閘道**。 虛擬網路閘道可讓 VNet 連線到與內部部署網路連線所使用的 VPN 裝置或 ExpressRoute 線路。 如需詳細資訊，請參閱[將內部部署網路連線至 Microsoft Azure 虛擬網路][connect-to-an-Azure-vnet]。

> [!NOTE]
> 此參考架構的部署指令碼會使用 VPN 閘道進行連線，並使用 Azure 中的 VNet 模擬內部部署網路。

- **中樞 VNet**。 在中樞輪輻拓撲當作中樞使用的 Azure VNet。 中樞是內部部署網路的連線中心點，也是裝載可由輪輻 VNet 中裝載的不同工作負載使用之服務的位置。

- **閘道子網路**。 虛擬網路閘道會保留在相同的子網路中。

- **共用服務子網路**。 中樞 VNet 中用來裝載可在所有輪輻間共用之服務 (例如 DNS 或 AD DS) 的子網路。

- **DMZ 子網路**。 中樞 VNet 中的子網路，用來裝載可以作為安全性設備 (例如防火牆) 的 NVA。

- **輪輻 VNet**。 在中樞輪輻拓撲中用來當作輪輻的一個或多個 Azure VNet。 輪輻可在自己的 VNet 中，用來隔離從其他輪輻分開管理的工作負載。 每個工作負載在透過 Azure 負載平衡器連線多個子網路的情況下，都可能包括多層。 如需有關應用程式基礎結構的詳細資訊，請參閱[執行 Windows VM 工作負載][windows-vm-ra]和[執行 Linux VM 工作負載][linux-vm-ra]。

- **VNet 對等互連**。 在相同的 Azure 區域中，可以使用[對等連線][vnet-peering]來連線兩個 VNet。 對等連線是 VNet 之間不可轉移的低延遲連線。 因此，一旦對等互連之後，VNet 就會使用 Azure 骨幹交換流量，而不需要使用路由器。 在中樞輪輻網路拓撲中，您可以使用 VNet 對等互連，將中樞連線至每個輪輻。

> [!NOTE]
> 本文僅涵蓋 [Resource Manager](/azure/azure-resource-manager/resource-group-overview) 部署，但是您也可以在相同的訂用帳戶中，將傳統 VNet 連線至 Resource Manager VNet。 如此一來，您的輪輻就可以裝載傳統部署，而且仍然受益於中樞中共用的服務。

## <a name="recommendations"></a>建議

適用於[中樞輪輻][guidance-hub-spoke]參考架構的所有建議也會套用至共用服務參考架構。

此外，下列建議適用於共用服務底下大部分的案例。 除非您有特定的需求會覆寫它們，否則請遵循下列建議。

### <a name="identity"></a>身分識別

大部分企業組織在其內部部署資料中心有 Active Directory 目錄服務 (ADDS) 環境。 若要將從依據 ADDS 之內部部署網路移到 Azure 的資產管理簡化，建議在 Azure 中裝載 ADDS 網域控制站。

如果您使用群組原則物件，想要分別控制 Azure 和內部部署環境，請為每個 Azure 區域使用不同的 AD 站台。 將您的網域控制站放在相依工作負載可存取的中央 VNet (中樞)。

### <a name="security"></a>安全性

當您從內部部署環境將工作負載移至 Azure 時，部分工作負載必須裝載在 VM 中。 基於相容性因素，您可能需要對周遊這些工作負載的流量強制執行限制。

您可以在 Azure 中使用網路虛擬設備 (NVA) 以裝載不同類型的安全性和效能服務。 如果您很熟悉現今一組特定的設備，建議在適用時於 Azure 中使用相同的虛擬設備。

> [!NOTE]
> 此參考架構的部署指令碼會使用已啟用 IP 轉送的 Ubuntu VM 來模擬網路虛擬設備。

## <a name="considerations"></a>考量

### <a name="overcoming-vnet-peering-limits"></a>克服 VNet 對等互連的限制

請確定您有在 Azure 中考慮[每個 VNet 的 VNet 對等互連數目限制][vnet-peering-limit]。 如果您決定您需要的輪輻比允許的限制還多，請考慮建立中樞輪輻中樞輪輻拓撲，其中輪輻的第一層也可作為中樞。 下圖顯示這個方法。

![Azure 中的中樞輪輻中樞輪輻拓撲](./images/hub-spokehub-spoke.svg)

此外，請考慮要在中樞共用哪些服務，以確保中樞可針對大量輪輻進行調整。 例如，如果您的中樞提供防火牆服務，請在新增多個輪輻時，考慮防火牆解決方案的頻寬限制。 您可以將其中部分共用服務移到中樞的第二層。

## <a name="deploy-the-solution"></a>部署解決方案

適用於此架構的部署可在 [GitHub][ref-arch-repo] 上取得。 此部署會在您的訂用帳戶中建立下列資源群組︰

- hub-adds-rg
- hub-nva-rg
- hub-vnet-rg
- onprem-vnet-rg
- spoke1-vnet-rg
- spoke2-vent-rg

範本參數檔案會參照這些名稱，因此，如果您變更這些名稱，請據以更新參數檔案。

### <a name="prerequisites"></a>必要條件

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-the-simulated-on-premises-datacenter-using-azbb"></a>使用 azbb 部署模擬的內部部署資料中心

此步驟將模擬的內部部署資料中心部署為 Azure VNet。

1. 巡覽至 GitHub 存放庫的 `hybrid-networking\shared-services-stack\` 資料夾。

2. 開啟 `onprem.json` 檔案。

3. 搜尋 `UserName`、`adminUserName`、`Password` 和 `adminPassword` 的所有執行個體。 在參數中輸入使用者名稱和密碼的值，並儲存檔案。

4. 執行以下命令：

   ```bash
   azbb -s <subscription_id> -g onprem-vnet-rg -l <location> -p onprem.json --deploy
   ```

5. 等待部署完成。 此部署會建立一個虛擬網路、一個執行 Windows 的虛擬機器，以及一個 VPN 閘道。 建立 VPN 閘道可能需要超過 40 分鐘才能完成。

### <a name="deploy-the-hub-vnet"></a>部署中樞 VNet

此步驟部署中樞 VNet 並將其連接至模擬的內部部署 VNet。

1. 開啟 `hub-vnet.json` 檔案。

2. 搜尋 `adminPassword` 並輸入參數中的使用者名稱和密碼。

3. 搜尋 `sharedKey` 的所有執行個體，並輸入共用金鑰的值。 儲存檔案。

   ```json
   "sharedKey": "abc123",
   ```

4. 執行以下命令：

   ```bash
   azbb -s <subscription_id> -g hub-vnet-rg -l <location> -p hub-vnet.json --deploy
   ```

5. 等待部署完成。 此部署會建立一個虛擬網路、一個虛擬機器、一個 VPN 閘道，以及一個與上節中建立之閘道的連線。 VPN 閘道可能需要超過 40 分鐘才能完成。

### <a name="deploy-ad-ds-in-azure"></a>在 Azure 中部署 AD DS

此步驟將在 Azure 中部署 AD DS 網域控制站。

1. 開啟 `hub-adds.json` 檔案。

2. 搜尋 `Password` 和 `adminPassword` 的所有執行個體。 在參數中輸入使用者名稱和密碼的值，並儲存檔案。

3. 執行以下命令：

   ```bash
   azbb -s <subscription_id> -g hub-adds-rg -l <location> -p hub-adds.json --deploy
   ```

此部署步驟可能需要幾分鐘的時間，因為它將兩個 VM 加入模擬內部部署資料中心託管的網域，並在其上安裝 AD DS。

### <a name="deploy-the-spoke-vnets"></a>部署輪輻 VNet

此步驟部署支點 Vnet。

1. 開啟 `spoke1.json` 檔案。

2. 搜尋 `adminPassword` 並輸入參數中的使用者名稱和密碼。

3. 執行以下命令：

   ```bash
   azbb -s <subscription_id> -g spoke1-vnet-rg -l <location> -p spoke1.json --deploy
   ```

4. 對 `spoke2.json` 檔案重複步驟 1 和 2。

5. 執行以下命令：

   ```bash
   azbb -s <subscription_id> -g spoke2-vnet-rg -l <location> -p spoke2.json --deploy
   ```

### <a name="peer-the-hub-vnet-to-the-spoke-vnets"></a>將中樞 VNet 對等到支點 Vnet

若要建立從中樞 VNet 到支點 VNet 的對等互連連線，請執行下列命令：

```bash
azbb -s <subscription_id> -g hub-vnet-rg -l <location> -p hub-vnet-peering.json --deploy
```

### <a name="deploy-the-nva"></a>部署 NVA

此步驟會在 `dmz` 子網路中部署 NVA。

1. 開啟 `hub-nva.json` 檔案。

2. 搜尋 `adminPassword` 並輸入參數中的使用者名稱和密碼。

3. 執行以下命令：

   ```bash
   azbb -s <subscription_id> -g hub-nva-rg -l <location> -p hub-nva.json --deploy
   ```

### <a name="test-connectivity"></a>測試連線能力

測試從模擬的內部部署環境到中樞 VNet 的連線。

1. 使用 Azure 入口網站尋找 `onprem-jb-rg` 資源群組中名為 `jb-vm1` 的 VM。

2. 按一下 `Connect` 以開啟 VM 的遠端桌面工作階段。 請使用您在 `onprem.json` 參數檔案中指定的密碼。

3. 在 VM 中開啟 PowerShell 主控台，並使用 `Test-NetConnection` Cmdlet 確認您可以連線至中樞 VNet 中的 jumpbox VM。

   ```powershell
   Test-NetConnection 10.0.0.68 -CommonTCPPort RDP
   ```

輸出應如下所示：

```powershell
ComputerName     : 10.0.0.68
RemoteAddress    : 10.0.0.68
RemotePort       : 3389
InterfaceAlias   : Ethernet 2
SourceAddress    : 192.168.1.000
TcpTestSucceeded : True
```

> [!NOTE]
> 根據預設，Windows Server VM 在 Azure 中不允許 ICMP 回應。 如果您想要使用 `ping` 來測試連線，您需要在 Windows 進階防火牆中為每個 VM 啟用 ICMP 流量。

重複執行相同的步驟來測試與支點 VNet 的連接性：

```powershell
Test-NetConnection 10.1.0.68 -CommonTCPPort RDP
Test-NetConnection 10.2.0.68 -CommonTCPPort RDP
```

<!-- links -->

[azure-cli-2]: /azure/install-azure-cli
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[guidance-hub-spoke]: ./hub-spoke.md
[azure-vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[best-practices-security]: /azure/best-practices-network-securit
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[guidance-expressroute]: ./expressroute.md
[guidance-vpn]: ./vpn.md
[linux-vm-ra]: ../virtual-machines-linux/index.md
[hybrid-ha]: ./expressroute-vpn-failover.md
[naming conventions]: /azure/guidance/guidance-naming-conventions
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[虛擬資料中心]: https://aka.ms/vdc
[vnet-peering]: /azure/virtual-network/virtual-network-peering-overview
[vnet-peering-limit]: /azure/azure-subscription-service-limits#networking-limits
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[windows-vm-ra]: ../virtual-machines-windows/index.md

[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-hub-spoke.vsdx
[ref-arch-repo]: https://github.com/mspnp/reference-architectures

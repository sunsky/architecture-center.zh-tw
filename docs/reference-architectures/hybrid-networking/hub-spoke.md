---
title: 在 Azure 中實作中樞輪輻網路拓撲
description: 如何在 Azure 中實作中樞輪輻網路拓撲。
author: telmosampaio
ms.date: 02/23/2018
pnp.series.title: Implement a hub-spoke network topology in Azure
pnp.series.prev: expressroute
ms.openlocfilehash: 243ad026c7c9703d9659cbef6815131fcdaa8a11
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="implement-a-hub-spoke-network-topology-in-azure"></a>在 Azure 中實作中樞輪輻網路拓撲

此參考架構會顯示如何在 Azure 中實作中樞輪輻拓撲。 「中樞」是 Azure 中的虛擬網路 (VNet)，可當作內部部署網路的連線中心點。 「輪輻」是與中樞對等互連的 VNet，可用於隔離工作負載。 流量會透過 ExpressRoute 或 VPN 閘道連線，在內部部署資料中心和中樞之間流動。  [**部署這個解決方案**](#deploy-the-solution)。

![[0]][0]

*下載這個架構的 [Visio 檔案][visio-download]*


此拓撲的優點包括：

* **節省成本**：將可由多個工作負載共用的服務 (例如網路虛擬裝置 (NVA) 和 DNS 伺服器) 集中在單一位置。
* **克服訂用帳戶限制**：將不同訂用帳戶中的 VNet 對等互連到中心中樞。
* **關注點分離**：中央 IT (SecOps、InfraOps) 和工作負載 (DevOps)。

此架構的一般使用案例包括：

* 在不同環境 (例如開發、測試和生產環境) 下部署，且需要共用服務 (例如 DNS、IDS、NTP 或 AD DS) 的工作負載。 系統會將共用服務置於中樞 VNet，同時將每個環境部署至輪輻以維持隔離。
* 不需要彼此連線，但需要存取共用服務的工作負載。
* 需要集中控制安全性層面 (例如，在中樞當作 DMZ 的防火牆)，並對每個輪輻中的工作負載進行分離管理的企業。

## <a name="architecture"></a>架構

此架構由下列元件組成。

* **內部部署網路**。 在組織內執行的私用區域網路。

* **VPN 裝置**。 可對內部部署網路提供外部連線的裝置或服務。 VPN 裝置可能是硬體裝置或軟體解決方案，例如，Windows Server 2012 中的路由及遠端存取服務 (RRAS)。 如需支援的 VPN 設備清單，以及設定選取的 VPN 設備以連線至 Azure 的相關資訊，請參閱[關於站對站 VPN 閘道連線的 VPN 裝置][vpn-appliance]。

* **VPN 虛擬網路閘道或 ExpressRoute 閘道**。 虛擬網路閘道可讓 VNet 連線到與內部部署網路連線所使用的 VPN 裝置或 ExpressRoute 線路。 如需詳細資訊，請參閱[將內部部署網路連線至 Microsoft Azure 虛擬網路][connect-to-an-Azure-vnet]。

> [!NOTE]
> 此參考架構的部署指令碼會使用 VPN 閘道進行連線，並使用 Azure 中的 VNet 模擬內部部署網路。

* **中樞 VNet**。 在中樞輪輻拓撲當作中樞使用的 Azure VNet。 中樞是內部部署網路的連線中心點，也是裝載可由輪輻 VNet 中裝載的不同工作負載使用之服務的位置。

* **閘道子網路**。 虛擬網路閘道會保留在相同的子網路中。

* **輪輻 VNet**。 在中樞輪輻拓撲中用來當作輪輻的一個或多個 Azure VNet。 輪輻可在自己的 VNet 中，用來隔離從其他輪輻分開管理的工作負載。 每個工作負載在透過 Azure 負載平衡器連線多個子網路的情況下，都可能包括多層。 如需有關應用程式基礎結構的詳細資訊，請參閱[執行 Windows VM 工作負載][windows-vm-ra]和[執行 Linux VM 工作負載][linux-vm-ra]。

* **VNet 對等互連**。 在相同的 Azure 區域中，可以使用[對等連線][vnet-peering]來連線兩個 VNet。 對等連線是 VNet 之間不可轉移的低延遲連線。 因此，一旦對等互連之後，VNet 就會使用 Azure 骨幹交換流量，而不需要使用路由器。 在中樞輪輻網路拓撲中，您可以使用 VNet 對等互連，將中樞連線至每個輪輻。

> [!NOTE]
> 本文僅涵蓋 [Resource Manager](/azure/azure-resource-manager/resource-group-overview) 部署，但是您也可以在相同的訂用帳戶中，將傳統 VNet 連線至 Resource Manager VNet。 如此一來，您的輪輻就可以裝載傳統部署，而且仍然受益於中樞中共用的服務。

## <a name="recommendations"></a>建議

下列建議適用於大部分的案例。 除非您有特定的需求會覆寫它們，否則請遵循下列建議。

### <a name="resource-groups"></a>資源群組

中樞 VNet 以及每個輪輻 VNet 都可以在不同的資源群組，甚至是不同的訂用帳戶中實作，只要這些訂用帳戶屬於相同 Azure 區域中的相同 Azure Active Directory (Azure AD) 租用戶即可。 如此可對每個工作負載進行非集中式管理，同時在中樞 VNet 中維護共用服務。

### <a name="vnet-and-gatewaysubnet"></a>VNet 和 GatewaySubnet

建立一個名為 *GatewaySubnet* 的子網路，且其位址範圍為 /27。 虛擬網路閘道需要使用這個子網路。 將 32 個位址配置給這個子網路，將有助於防止未來達到閘道大小限制。

如需有關設定閘道的詳細資訊，請根據您的連線類型，參閱下列參考架構：

- [使用 ExpressRoute 的混合式網路][guidance-expressroute]
- [使用 VPN 閘道的混合式網路][guidance-vpn]

如需高可用性，您可以使用 ExpressRoute 加上 VPN 進行容錯移轉。 請參閱[使用 ExpressRoute 搭配 VPN 容錯移轉，將內部部署網路連線至 Azure][hybrid-ha]。

如果您不需要與內部部署網路連線，則不需要閘道也可以使用中樞輪輻拓撲。 

### <a name="vnet-peering"></a>VNet 對等互連

VNet 對等互連是兩個 VNet 之間不可轉移的關聯性。 如果您需要輪輻彼此連線，請考慮在這些輪輻之間加入個別的對等連線。

不過，如果您有數個需要彼此連線的輪輻，將會因為[每個 VNet 的 VNet 對等互連數目限制][vnet-peering-limit]，而非常快速地用盡可能的對等連線。 在此案例中，請考慮使用使用者定義的路由 (UDR)，強制將目的地為輪輻的流量傳送到當作中樞 VNet 路由器的 NVA。 這可讓輪輻彼此連線。

您也可以將輪輻設定為使用中樞 VNet 閘道，與遠端網路進行通訊。 若要允許閘道流量從輪輻流向中樞，然後連線至遠端網路，您必須：

  - 將中樞中的 VNet 對等連線設定為**允許閘道傳輸**。
  - 將每個輪輻中的 VNet 對等連線設定為**使用遠端閘道**。
  - 將所有 VNet 對等連線設定為**允許轉送的流量**。

## <a name="considerations"></a>考量

### <a name="spoke-connectivity"></a>輪輻連線能力

如果您需要在輪輻之間連線，請考慮實作 NVA 以便在中樞路由傳送，並在輪輻中使用 UDR，將流量轉送到中樞。

![[2]][2]

在此案例中，您必須將對等連線設定為**允許轉送的流量**。

### <a name="overcoming-vnet-peering-limits"></a>克服 VNet 對等互連的限制

請確定您有在 Azure 中考慮[每個 VNet 的 VNet 對等互連數目限制][vnet-peering-limit]。 如果您決定您需要的輪輻比允許的限制還多，請考慮建立中樞輪輻中樞輪輻拓撲，其中輪輻的第一層也可作為中樞。 下圖顯示這個方法。

![[3]][3]

此外，請考慮要在中樞共用哪些服務，以確保中樞可針對大量輪輻進行調整。 例如，如果您的中樞提供防火牆服務，請在新增多個輪輻時，考慮防火牆解決方案的頻寬限制。 您可以將其中部分共用服務移到中樞的第二層。

## <a name="deploy-the-solution"></a>部署解決方案

適用於此架構的部署可在 [GitHub][ref-arch-repo] 上取得。 它會使用每個 VNet 中的 Ubuntu VM 測試連線。 **中樞 VNet** 的**共用服務**子網路中沒有裝載任何實際的服務。

### <a name="prerequisites"></a>先決條件

在您可以將參考架構部署到自己的訂用帳戶之前，必須執行下列步驟。

1. 複製、派生或下載適用於[參考架構][ref-arch-repo] GitHub 存放庫的 zip 檔案。

2. 確定您已在電腦上安裝 Azure CLI 2.0。 如需 CLI 安裝指示，請參閱[安裝 Azure CLI 2.0][azure-cli-2]。

3. 安裝 [Azure 建置組塊][azbb] npm 套件。

4. 從命令提示字元、bash 提示字元或 PowerShell 提示字元中，使用下列命令登入 Azure 帳戶，並遵循提示進行。

   ```bash
   az login
   ```

### <a name="deploy-the-simulated-on-premises-datacenter-using-azbb"></a>使用 azbb 部署模擬的內部部署資料中心

若要將模擬的內部部署資料中心部署為 Azure VNet，請遵循下列步驟：

1. 瀏覽至您在上述必要條件步驟中所下載存放庫的 `hybrid-networking\hub-spoke\` 資料夾。

2. 開啟 `onprem.json` 檔案，然後在第 36 和 37 行的引號之間輸入使用者名稱和密碼，如下所示，然後儲存檔案。

   ```bash
   "adminUsername": "XXX",
   "adminPassword": "YYY",
   ```

3. 在第 38 行上，針對 `osType` 輸入 `Windows` 或 `Linux`，以安裝 Windows Server 2016 Datacenter 或 Ubuntu 16.04 作為 jumpbox 的作業系統。

4. 執行 `azbb` 以部署模擬的內部部署環境，如下所示。

   ```bash
   azbb -s <subscription_id> -g onprem-vnet-rg - l <location> -p onoprem.json --deploy
   ```
   > [!NOTE]
   > 如果您決定使用不同的資源群組名稱 (非 `onprem-vnet-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。

5. 等待部署完成。 此部署會建立一個虛擬網路、一個虛擬機器，以及一個 VPN 閘道。 建立 VPN 閘道可能需要超過 40 分鐘才能完成。

### <a name="azure-hub-vnet"></a>Azure 中樞 VNet

若要部署中樞 VNet 並連線到以上所建立的模擬內部部署 VNet，請執行下列步驟。

1. 開啟 `hub-vnet.json` 檔案，然後在第 39 和 40 行的引號之間輸入使用者名稱和密碼，如下所示。

   ```bash
   "adminUsername": "XXX",
   "adminPassword": "YYY",
   ```

2. 在第 41 行上，針對 `osType` 輸入 `Windows` 或 `Linux` 以安裝 Windows Server 2016 Datacenter 或 Ubuntu 16.04 作為 jumpbox 的作業系統。

3. 在第 72 行的引號之間輸入共用金鑰，如下所示，然後儲存檔案。

   ```bash
   "sharedKey": "",
   ```

4. 執行 `azbb` 以部署模擬的內部部署環境，如下所示。

   ```bash
   azbb -s <subscription_id> -g hub-vnet-rg - l <location> -p hub-vnet.json --deploy
   ```
   > [!NOTE]
   > 如果您決定使用不同的資源群組名稱 (非 `hub-vnet-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。

5. 等待部署完成。 此部署會建立一個虛擬網路、一個虛擬機器、一個 VPN 閘道，以及一個與上節中建立之閘道的連線。 建立 VPN 閘道可能需要超過 40 分鐘才能完成。

### <a name="optional-test-connectivity-from-onprem-to-hub"></a>(選擇性) 測試從內部部署到中樞的連線

若要測試從模擬的內部部署環境到使用 Windows VM 之中樞 VNet 的連線，請執行下列步驟。

1. 從 Azure 入口網站中，瀏覽至 `onprem-jb-rg` 資源群組，然後按一下 `jb-vm1` 虛擬機器資源。

2. 在入口網站中虛擬機器刀鋒視窗的左上角，按一下 `Connect`，並遵循提示來使用遠端桌面連線至 VM。 請務必使用您在 `onprem.json` 檔案 36 和 37 行中指定的使用者名稱和密碼。

3. 在 VM 中開啟 PowerShell 主控台，並使用 `Test-NetConnection` Cmdlet 來確認您可以連線至中樞 jumpbox VM，如下所示。

   ```powershell
   Test-NetConnection 10.0.0.68 -CommonTCPPort RDP
   ```
   > [!NOTE]
   > 根據預設，Windows Server VM 在 Azure 中不允許 ICMP 回應。 如果您想要使用 `ping` 來測試連線，您需要在 Windows 進階防火牆中為每個 VM 啟用 ICMP 流量。

若要測試從模擬的內部部署環境到使用 Linux VM 之中樞 VNet 的連線，請執行下列步驟：

1. 從 Azure 入口網站中，瀏覽至 `onprem-jb-rg` 資源群組，然後按一下 `jb-vm1` 虛擬機器資源。

2. 在入口網站 VM 刀鋒視窗的左上角，按一下 `Connect`，然後複製入口網站上顯示的 `ssh` 命令。 

3. 從 Linux 提示中執行 `ssh`，使用您在上述步驟 2 中複製的資訊，連線到模擬的內部部署環境 jumpbox，如下所示。

   ```bash
   ssh <your_user>@<public_ip_address>
   ```

4. 使用您在 `onprem.json` 檔案 37 行中指定的密碼連線到 VM。

5. 使用 `ping` 命令來測試與中樞 jumpbox 的連線，如下所示。

   ```bash
   ping 10.0.0.68
   ```

### <a name="azure-spoke-vnets"></a>Azure 輪輻 VNet

若要部署輪輻 VNet，請執行下列步驟。

1. 開啟 `spoke1.json` 檔案，然後在第 47 和 48 行的引號之間輸入使用者名稱和密碼，如下所示，然後儲存檔案。

   ```bash
   "adminUsername": "XXX",
   "adminPassword": "YYY",
   ```

2. 在第 49 行上，針對 `osType` 輸入 `Windows` 或 `Linux` 以安裝 Windows Server 2016 Datacenter 或 Ubuntu 16.04 作為 jumpbox 的作業系統。

3. 執行 `azbb` 以部署第一個輪輻 VNet 環境，如下所示。

   ```bash
   azbb -s <subscription_id> -g spoke1-vnet-rg - l <location> -p spoke1.json --deploy
   ```
  
   > [!NOTE]
   > 如果您決定使用不同的資源群組名稱 (非 `spoke1-vnet-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。

4. 對 `spoke2.json` 檔案重複上述的步驟 1。

5. 執行 `azbb` 以部署第二個輪輻 VNet 環境，如下所示。

   ```bash
   azbb -s <subscription_id> -g spoke2-vnet-rg - l <location> -p spoke2.json --deploy
   ```
   > [!NOTE]
   > 如果您決定使用不同的資源群組名稱 (非 `spoke2-vnet-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。

### <a name="azure-hub-vnet-peering-to-spoke-vnets"></a>Azure 中樞 VNet 對等互連至輪輻 VNet

若要建立從中樞 VNet 到輪輻 VNet 的對等互連連線，請執行下列步驟。

1. 開啟 `hub-vnet-peering.json` 檔案並確認從第 29 行開始之每個虛擬網路對等互連的資源群組名稱和虛擬網路名稱正確無誤。

2. 執行 `azbb` 以部署第一個輪輻 VNet 環境，如下所示。

   ```bash
   azbb -s <subscription_id> -g hub-vnet-rg - l <location> -p hub-vnet-peering.json --deploy
   ```

   > [!NOTE]
   > 如果您決定使用不同的資源群組名稱 (非 `hub-vnet-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。

### <a name="test-connectivity"></a>測試連線能力

若要測試從模擬的內部部署環境到使用 Windows VM 之輪輻 VNet 的連線，請執行下列步驟。

1. 從 Azure 入口網站中，瀏覽至 `onprem-jb-rg` 資源群組，然後按一下 `jb-vm1` 虛擬機器資源。

2. 在入口網站中虛擬機器刀鋒視窗的左上角，按一下 `Connect`，並遵循提示來使用遠端桌面連線至 VM。 請務必使用您在 `onprem.json` 檔案 36 和 37 行中指定的使用者名稱和密碼。

3. 在 VM 中開啟 PowerShell 主控台，並使用 `Test-NetConnection` Cmdlet 來確認您可以連線至中樞 jumpbox VM，如下所示。

   ```powershell
   Test-NetConnection 10.1.0.68 -CommonTCPPort RDP
   Test-NetConnection 10.2.0.68 -CommonTCPPort RDP
   ```

若要測試從模擬的內部部署環境到使用 Linux VM 之輪輻 VNet 的連線，請執行下列步驟：

1. 從 Azure 入口網站中，瀏覽至 `onprem-jb-rg` 資源群組，然後按一下 `jb-vm1` 虛擬機器資源。

2. 在入口網站 VM 刀鋒視窗的左上角，按一下 `Connect`，然後複製入口網站上顯示的 `ssh` 命令。 

3. 從 Linux 提示中執行 `ssh`，使用您在上述步驟 2 中複製的資訊，連線到模擬的內部部署環境 jumpbox，如下所示。

   ```bash
   ssh <your_user>@<public_ip_address>
   ```

4. 使用您在 `onprem.json` 檔案 37 行中指定的密碼連線到 VM。

5. 使用 `ping` 命令來測試與每個輪輻中 jumpbox VM 的連線，如下所示。

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

### <a name="add-connectivity-between-spokes"></a>新增輪輻之間的連線

如果您想要允許輪輻彼此連線，您必須使用網路虛擬設備 (NVA) 作為中樞虛擬網路的路由器，並且在嘗試連線到另一個輪輻時強制執行從輪輻到路由器的流量。 若要部署基本範例 NVA 作為單一 VM，且需要使用者定義的路由器允許兩個輪輻 Vnet 連線，請執行下列步驟：

1. 開啟 `hub-nva.json` 檔案，然後在第 13 和 14 行的引號之間輸入使用者名稱和密碼，如下所示，然後儲存檔案。

   ```bash
   "adminUsername": "XXX",
   "adminPassword": "YYY",
   ```
2. 執行 `azbb` 以部署 NVA VM 和使用者定義的路由。

   ```bash
   azbb -s <subscription_id> -g hub-nva-rg - l <location> -p hub-nva.json --deploy
   ```
   > [!NOTE]
   > 如果您決定使用不同的資源群組名稱 (非 `hub-nva-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。

<!-- links -->

[azure-cli-2]: /azure/install-azure-cli
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[azure-vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[best-practices-security]: /azure/best-practices-network-securit
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[guidance-expressroute]: ./expressroute.md
[guidance-vpn]: ./vpn.md
[linux-vm-ra]: ../virtual-machines-linux/index.md
[hybrid-ha]: ./expressroute-vpn-failover.md
[naming conventions]: /azure/guidance/guidance-naming-conventions
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[vnet-peering]: /azure/virtual-network/virtual-network-peering-overview
[vnet-peering-limit]: /azure/azure-subscription-service-limits#networking-limits
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[windows-vm-ra]: ../virtual-machines-windows/index.md

[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-hub-spoke.vsdx
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[0]: ./images/hub-spoke.png "Azure 中的中樞輪輻拓撲"
[1]: ./images/hub-spoke-gateway-routing.svg "Azure 中具有可轉移路由的中樞輪輻拓撲"
[2]: ./images/hub-spoke-no-gateway-routing.svg "Azure 中具有使用 NVA 之可轉移路由的中樞輪輻拓撲"
[3]: ./images/hub-spokehub-spoke.svg "Azure 中的中樞輪輻中樞輪輻拓撲"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/

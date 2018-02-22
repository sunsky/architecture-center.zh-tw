---
title: "在 Azure 中實作中樞輪輻網路拓撲"
description: "如何在 Azure 中實作中樞輪輻網路拓撲。"
author: telmosampaio
ms.date: 02/14/2018
pnp.series.title: Implement a hub-spoke network topology in Azure
pnp.series.prev: expressroute
ms.openlocfilehash: c03ecd4ba5ddbe50cfb17e56d75c18102b751cfb
ms.sourcegitcommit: 475064f0a3c2fac23e1286ba159aaded287eec86
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2018
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

* **共用服務子網路**。 中樞 VNet 中用來裝載可在所有輪輻間共用之服務 (例如 DNS 或 AD DS) 的子網路。

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

1. 複製、派生或下載適用於 [AzureCAT 參考架構][ref-arch-repo] GitHub 存放庫的 zip 檔案。

2. 如果您想要使用 Azure CLI，請確定您已在電腦上安裝 Azure CLI 2.0。 若要安裝 CLI，請依照[安裝 Azure CLI 2.0][azure-cli-2] 中的指示進行。

3. 如果您想要使用 PowerShell，請確定您已在電腦上安裝適用於 Azure 的最新 PowerShell 模組。 若要安裝最新的 Azure PowerShell 模組，請依照[安裝適用於 Azure 的 PowerShell][azure-powershell] 中的指示進行。

4. 從命令提示字元、bash 提示字元或 PowerShell 提示字元中，使用下列其中一個命令登入 Azure 帳戶，並遵循提示進行。

  ```bash
  az login
  ```

  ```powershell
  Login-AzureRmAccount
  ```

### <a name="deploy-the-simulated-on-premises-datacenter"></a>部署模擬的內部部署資料中心

若要將模擬的內部部署資料中心部署為 Azure VNet，請執行下列步驟。

1. 瀏覽至您在上述必要條件步驟中所下載存放庫的 `hybrid-networking\hub-spoke\onprem` 資料夾。

2. 開啟 `onprem.vm.parameters.json` 檔案，然後在第 11 和 12 行的引號之間輸入使用者名稱和密碼，如下所示，然後儲存檔案。

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```

3. 執行以下的 bash 或 PowerShell 命令，將模擬的內部部署環境部署為 Azure 中的 VNet。 將這些值取代為您的訂用帳戶、資源群組名稱和 Azure 區域。

  ```bash
  sh ./onprem.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-onprem-rg \
    --location westus
  ```

  ```powershell
  ./onprem.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-onprem-rg `
    -Location westus
  ```
  > [!NOTE]
  > 如果您決定使用不同的資源群組名稱 (非 `ra-onprem-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。

4. 等待部署完成。 此部署會建立一個虛擬網路、一個執行 Ubuntu 的虛擬機器，以及一個 VPN 閘道。 建立 VPN 閘道可能需要超過 40 分鐘才能完成。

### <a name="azure-hub-vnet"></a>Azure 中樞 VNet

若要部署中樞 VNet 並連線到以上所建立的模擬內部部署 VNet，請執行下列步驟。

1. 瀏覽至您在上述必要條件步驟中所下載存放庫的 `hybrid-networking\hub-spoke\hub` 資料夾。

2. 開啟 `hub.vm.parameters.json` 檔案，然後在第 11 和 12 行的引號之間輸入使用者名稱和密碼，如下所示，然後儲存檔案。

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```

3. 開啟 `hub.gateway.parameters.json` 檔案，然後在第 23 行的引號之間輸入共用金鑰，如下所示，然後儲存檔案。 請記下此值，稍後在部署時會使用到這個值。

  ```bash
  "sharedKey": "",
  ```

4. 執行以下的 bash 或 PowerShell 命令，將模擬的內部部署環境部署為 Azure 中的 VNet。 將這些值取代為您的訂用帳戶、資源群組名稱和 Azure 區域。

  ```bash
  sh ./hub.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-hub-rg \
    --location westus
  ```

  ```powershell
  ./hub.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-hub-rg `
    -Location westus
  ```
  > [!NOTE]
  > 如果您決定使用不同的資源群組名稱 (非 `ra-hub-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。

5. 等待部署完成。 此部署會建立一個虛擬網路、一個執行 Ubuntu 的虛擬機器、一個 VPN 閘道，以及一個與上節中建立之閘道的連線。 建立 VPN 閘道可能需要超過 40 分鐘才能完成。

### <a name="connection-from-on-premises-to-the-hub"></a>從內部部署連線到中樞

若要從模擬的內部部署資料中心連線到中樞 VNet，請執行下列步驟。

1. 瀏覽至您在上述必要條件步驟中所下載存放庫的 `hybrid-networking\hub-spoke\onprem` 資料夾。

2. 開啟 `onprem.connection.parameters.json` 檔案，然後在第 9 行的引號之間輸入共用金鑰，如下所示，然後儲存檔案。 此共用金鑰值必須與您先前部署之內部部署閘道中使用的值相同。

  ```bash
  "sharedKey": "",
  ```

3. 執行以下的 bash 或 PowerShell 命令，將模擬的內部部署環境部署為 Azure 中的 VNet。 將這些值取代為您的訂用帳戶、資源群組名稱和 Azure 區域。

  ```bash
  sh ./onprem.connection.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-onprem-rg \
    --location westus
  ```

  ```powershell
  ./onprem.connection.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-onprem-rg `
    -Location westus
  ```
  > [!NOTE]
  > 如果您決定使用不同的資源群組名稱 (非 `ra-onprem-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。

4. 等待部署完成。 此部署會在用來模擬內部部署資料中心的 VNet 和中樞 VNet 之間建立一個連線。

### <a name="azure-spoke-vnets"></a>Azure 輪輻 VNet

若要部署輪輻 VNet 並連線到以上所建立的中樞 VNet，請執行下列步驟。

1. 切換至您在上述先決條件步驟中下載之存放庫的 `hybrid-networking\hub-spoke\spokes` 資料夾。

2. 開啟 `spoke1.web.parameters.json` 檔案，然後在第 53 和 54 行的引號之間輸入使用者名稱和密碼，如下所示，然後儲存檔案。

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```

3. 針對檔案 `spoke2.web.parameters.json` 重複上述步驟。

4. 執行以下的 bash 或 PowerShell 命令，以部署第一個輪輻並將其連線至中樞。 將這些值取代為您的訂用帳戶、資源群組名稱和 Azure 區域。

  ```bash
  sh ./spoke.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-spoke1-rg \
    --location westus \
    --spoke 1
  ```

  ```powershell
  ./spoke.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-spoke1-rg `
    -Location westus `
    -Spoke 1
  ```
  > [!NOTE]
  > 如果您決定使用不同的資源群組名稱 (非 `ra-spoke1-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。

5. 等待部署完成。 此部署建立一個虛擬網路、一個包含三個執行 Ubuntu 和 Apache 之虛擬機器的負載平衡器，以及一個與上節中建立之中樞 VNet 的 VNet 對等連線。 此部署可能需要超過 20 分鐘。

6. 執行以下的 bash 或 PowerShell 命令，以部署第一個輪輻並將其連線至中樞。 將這些值取代為您的訂用帳戶、資源群組名稱和 Azure 區域。

  ```bash
  sh ./spoke.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-spoke2-rg \
    --location westus \
    --spoke 2
  ```

  ```powershell
  ./spoke.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-spoke2-rg `
    -Location westus `
    -Spoke 2
  ```
  > [!NOTE]
  > 如果您決定使用不同的資源群組名稱 (非 `ra-spoke2-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。

5. 等待部署完成。 此部署建立一個虛擬網路、一個包含三個執行 Ubuntu 和 Apache 之虛擬機器的負載平衡器，以及一個與上節中建立之中樞 VNet 的 VNet 對等連線。 此部署可能需要超過 20 分鐘。

### <a name="azure-hub-vnet-peering-to-spoke-vnets"></a>Azure 中樞 VNet 對等互連至輪輻 VNet

若要部署中樞 VNet 的 VNet 對等連線，請執行下列步驟。

1. 切換至您在上述先決條件步驟中下載之存放庫的 `hybrid-networking\hub-spoke\hub` 資料夾。

2. 執行以下的 bash 或 PowerShell 命令，以部署與第一個輪輻的對等連線。 將這些值取代為您的訂用帳戶、資源群組名稱和 Azure 區域。

  ```bash
  sh ./hub.peering.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-hub-rg \
    --location westus \
    --spoke 1
  ```

  ```powershell
  ./hub.peering.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-hub-rg `
    -Location westus `
    -Spoke 1
  ```

2. 執行以下的 bash 或 PowerShell 命令，以部署與第二個輪輻的對等連線。 將這些值取代為您的訂用帳戶、資源群組名稱和 Azure 區域。

  ```bash
  sh ./hub.peering.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-hub-rg \
    --location westus \
    --spoke 2
  ```

  ```powershell
  ./hub.peering.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-hub-rg `
    -Location westus `
    -Spoke 2
  ```

### <a name="test-connectivity"></a>測試連線能力

若要確認連線至內部部署資料中心部署的中樞輪輻拓撲正常運作，請依照下列步驟進行。

1. 從 [Azure 入口網站][入口網站] 連線到您的訂用帳戶，然後瀏覽至 `ra-onprem-rg` 資源群組中的 `ra-onprem-vm1` 虛擬機器。

2. 在 `Overview` 刀鋒視窗中，記下 VM 的 `Public IP address`。

3. 使用您在部署期間指定的使用者名稱和密碼，透過 SSH 用戶端連線至您在上述步驟中記下的 IP 位址。

4. 從連線 VM 上的命令提示字元執行以下的命令，以測試內部部署 VNet 到 Spoke1 VNet 的連線。

  ```bash
  ping 10.1.1.37
  ```

<!-- links -->

[azure-cli-2]: /azure/install-azure-cli
[azure-powershell]: /powershell/azure/install-azure-ps?view=azuresmps-3.7.0
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

[visio-download]: https://archcenter.azureedge.net/cdn/hybrid-network-hub-spoke.vsdx
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[0]: ./images/hub-spoke.png "Azure 中的中樞輪輻拓撲"
[1]: ./images/hub-spoke-gateway-routing.svg "Azure 中具有可轉移路由的中樞輪輻拓撲"
[2]: ./images/hub-spoke-no-gateway-routing.svg "Azure 中具有使用 NVA 之可轉移路由的中樞輪輻拓撲"
[3]: ./images/hub-spokehub-spoke.svg "Azure 中的中樞輪輻中樞輪輻拓撲"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/

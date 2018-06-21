---
title: 使用 VPN 將內部部署網路連線至 Azure
description: 如何實作安全的站對站網路架構，並且在透過 VPN 連線的 Azure 虛擬網路與內部部署網路中使用。
author: RohitSharma-pnp
ms.date: 11/28/2016
pnp.series.title: Connect an on-premises network to Azure
pnp.series.next: expressroute
pnp.series.prev: ./index
cardTitle: VPN
ms.openlocfilehash: dafcee6607d9cc7c56c332f9ed5d9568ff70f0e7
ms.sourcegitcommit: c441fd165e6bebbbbbc19854ec6f3676be9c3b25
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2018
ms.locfileid: "30270688"
---
# <a name="connect-an-on-premises-network-to-azure-using-a-vpn-gateway"></a>使用 VPN 閘道將內部部署網路連線至 Azure

此參考架構會示範如何使用網站對網站虛擬私人網路 (VPN)，將內部部署網路擴充至 Azure。 內部部署網路和 Azure 虛擬網路 (VNet) 之間的流量會透過 IPSec VPN 通道流動。 [**部署這個解決方案**。](#deploy-the-solution)

![[0]][0]

下載這個架構的 [Visio 檔案][visio-download]。

## <a name="architecture"></a>架構 

此架構由下列元件組成。

* **內部部署網路**。 在組織內執行的私用區域網路。

* **VPN 設備**。 可對內部部署網路提供外部連線的裝置或服務。 VPN 設備可能是硬體裝置或軟體解決方案，例如，Windows Server 2012 中的路由及遠端存取服務 (RRAS)。 如需支援的 VPN 設備清單和將其設定為連線至 Azure VPN 閘道的詳細資訊，請參閱[關於站對站 VPN 閘道連線的 VPN 裝置][vpn-appliance]一文中所選取裝置的指示。

* **虛擬網路 (VNet)**。 雲端應用程式與 Azure VPN 閘道的元件存會位於相同 [VNet][azure-virtual-network]。

* **Azure VPN 閘道**。 [VPN 閘道][azure-vpn-gateway]服務可讓您透過 VPN 設備將 VNet 連線至內部部署網路。 如需詳細資訊，請參閱[將內部部署網路連線至 Microsoft Azure 虛擬網路][connect-to-an-Azure-vnet]。 VPN 閘道包含下列元素：
  
  * **虛擬網路閘道**。 為 VNet 提供虛擬 VPN 設備的資源。 負責將流量從內部部署網路由至 VNet。
  * **區域網路閘道**。 內部部署 VPN 設備的抽象概念。 從雲端應用程式流至內部部署網路的網路流量會透過此閘道路由傳送。
  * **連線**。 連線的屬性會指定連線類型 (IPSec) 以及與內部部署 VPN 設備共用以加密流量的金鑰。
  * **閘道子網路**。 虛擬網路閘道保留在自己的子網路中，其受限於下方＜建議＞一節中所述的各種需求。

* **雲端應用程式**。 在 Azure 中裝載的應用程式。 此應用程式在透過 Azure 負載平衡器連線多個子網路的情況下，可能包括多層。 如需有關應用程式基礎結構的詳細資訊，請參閱[執行 Windows VM 工作負載][windows-vm-ra]和[執行 Linux VM 工作負載][linux-vm-ra]。

* **內部負載平衡器**。 來自 VPN 閘道的網路流量會透過內部負載平衡器，路由傳送至雲端應用程式。 負載平衡器會位於應用程式的前端子網路中。

## <a name="recommendations"></a>建議

下列建議適用於大部分的案例。 除非您有特定的需求會覆寫它們，否則請遵循下列建議。

### <a name="vnet-and-gateway-subnet"></a>VNet 和閘道子網路

建立 Azure VNet，且其位址空間要夠大，以便使用所有必要資源。 如果未來可能需要其他虛擬機器，請確定 VNet 位址空間有足夠的空間可擴增。 VNet 的位址空間不得與內部部署網路重疊。 例如，上圖中 VNet 使用位址空間 10.20.0.0/16。

建立一個名為 *GatewaySubnet* 的子網路，且其位址範圍為 /27。 虛擬網路閘道需要使用這個子網路。 將 32 個位址配置給這個子網路，將有助於防止未來達到閘道大小限制。 此外，請避免將此子網路放在位址空間的中間。 較好的做法是，將閘道子網路的位址空間設定在 VNet 位址空間的上端。 圖中所示的範例是使用 10.20.255.224/27。  以下是計算 [CIDR] 的快速程序：

1. 將 VNet 位址空間中的變動位元數設為 1 (根據閘道正在使用的位元數)，然後將其他位元數設為 0。
2. 將產生的位元數轉換成十進位，並將其表示為前置長度設為閘道子網路大小的位址空間。

例如，IP 位址範圍是 10.20.0.0/16 的 VNet，套用上述步驟 1 後會變成 10.20.0b11111111.0b11100000。  將其轉換成十進位並表示為位址空間後會產生 10.20.255.224/27。 

> [!WARNING]
> 請勿將任何虛擬機器部署至閘道子網路。 此外，請勿指派 NSG 至此子網路，因為會導致閘道停止運作。
> 
> 

### <a name="virtual-network-gateway"></a>虛擬網路閘道

配置虛擬網路閘道的公用 IP 位址。

在閘道子網路中建立虛擬網路閘道，並為其指派新配置的公用 IP 位址。 使用最符合您需求且由您的 VPN 設備啟用的閘道類型：

- 如果您需要根據原則準則 (例如位址首碼)，密切地控制要求的路由方式，可建立[原則型閘道][policy-based-routing]。 原則型閘道會使用靜態路由，並只適用於站對站連線。

- 如果您使用 RRAS 連線至內部部署網路、支援多站台或跨區域連線或實作 VNet 對 VNet 連線 (包括跨越多個 Vnet 的路由)，可建立[路由型閘道][route-based-routing]。 路由型閘道會使用動態路由來指引網路之間的流量。 動態路由在網路路徑中的容錯能力比靜態路由好，因為動態路由可以嘗試替代路由。 路由型閘道也可減少額外負荷，因為當網路位址變更時，路由可能不需要以手動方式更新。

如需支援的 VPN 設備清單，請參閱[關於站對站 VPN 閘道連線的 VPN 裝置][vpn-appliances]。

> [!NOTE]
> 一旦建立閘道之後，您必須刪除並重新建立閘道才能變更閘道類型。
> 
> 

選取最符合您輸送量需求的 Azure VPN 閘道 SKU。 有三個 SKU 適用於Azure VPN 閘道，如下表所示。 

| SKU | VPN 輸送量 | IPSec 通道數上限 |
| --- | --- | --- |
| 基本 |100 Mbps |10 |
| 標準 |100 Mbps |10 |
| 高效能 |200 Mbps |30 |

> [!NOTE]
> 基本 SKU 與 Azure ExpressRoute 不相容。 建立閘道之後，您可以[變更 SKU][changing-SKUs]。
> 
> 

您須根據閘道上所佈建及可用時間量支付費用。 請參閱 [VPN 閘道價格][azure-gateway-charges]。

建立閘道子網路的路由規則，將傳入的應用程式流量從閘道引導至內部負載平衡器，而不允許將要求直接傳遞至應用程式虛擬機器。

### <a name="on-premises-network-connection"></a>內部部署網路連線

建立區域網路閘道。 指定內部部署 VPN 設備的公用 IP 位址，以及內部部署網路的位址空間。 請注意，內部部署 VPN 設備必須有可以存取的公用 IP 位址，讓 Azure VPN 閘道中的區域網路閘道進行存取。 在網路位址轉譯 (NAT) 裝置後面找不到 VPN 裝置。

建立虛擬網路閘道與區域網路閘道的站對站連線。 選取站對站 (IPSec) 連線類型，並指定共用金鑰。 Azure VPN 閘道的站對站加密是以 IPSec 通訊協定為基礎，並使用預先共用的金鑰來進行驗證。 您會在建立 Azure VPN 閘道時指定金鑰。 您必須使用相同金鑰來設定在內部部署執行的 VPN 設備。 目前不支援其他驗證機制。

請確認內部部署路由基礎結構已設定為，將原本目的地是 Azure VNet 位址的要求轉送至 VPN 裝置。

在內部部署網路中，開啟任何雲端應用程式所需的連接埠。

測試連線以確認下列事項：

* 內部部署 VPN 設備透過 Azure VPN 閘道，正確地將流量路由至雲端應用程式。
* VNet 正確地將流量路由回內部部署網路。
* 兩個方向中禁止的流量已正確地封鎖。

## <a name="scalability-considerations"></a>延展性考量

您可以從基本或標準 VPN 閘道 SKU 移到高效能 VPN SKU，以達到所限制的垂直延展性。

對於預期有大量 VPN 流量的 Vnet，請考慮將不同工作負載分散到較小的個別 VNet，以及為每一個個別 VNet 設定 VPN 閘道。

您可以水平或垂直分割 VNet。 若要以水平方式分割，請將一些虛擬機器執行個體從每個層移至新 VNet 的子網路。 結果會是每個 VNet 都具有相同的結構與功能。 若要以垂直方式分割，請重新設計每個層以將功能分成不同的邏輯區域 (例如處理訂單、發票開立和客戶帳戶管理等)。 然後每個功能區域就可以放在自己的 VNet 中。

複寫 VNet 中的內部部署 Active Directory 網域控制站，以及在 VNet 中實作 DNS，可以協助減少部分安全性相關流量與系統管理流量從內部部署流至雲端。 如需詳細資訊，請參閱[將 Active Directory Domain Services (AD DS) 擴充至 Azure][adds-extend-domain]。

## <a name="availability-considerations"></a>可用性考量

如果您需要確保內部部署網路仍然可供 Azure VPN 閘道使用，可為內部部署 VPN 閘道實作容錯移轉叢集。

如果您的組織具有多個內部部署站台，可對一個或多個 Azure Vnet 建立[多站台連線][vpn-gateway-multi-site]。 此方法需要建立動態 (路由型) 路由，因此請確定內部部署 VPN 閘道支援此功能。

如需有關服務等級協定的詳細資訊，請參閱 [VPN 閘道的 SLA][sla-for-vpn-gateway]。 

## <a name="manageability-considerations"></a>管理性考量

監視內部部署 VPN 設備中的診斷資訊。 此流程取決於 VPN 設備所提供的功能。 例如，如果您使用 Windows Server 2012 上的路由及遠端存取服務，也就是 [RRAS 記錄][rras-logging]。

請使用 [Azure VPN 閘道診斷][gateway-diagnostic-logs]來擷取連線問題的相關資訊。 這些記錄可用來追蹤資訊，例如連線要求的來源和目的地、使用的通訊協定，以及建立連線的方式 (或嘗試失敗的原因)。

在 Azure 入口網站中，使用可用的稽核記錄來監視 Azure VPN 閘道的作業記錄。 個別的記錄適用於區域網路閘道、Azure 網路閘道和連線。 此資訊可用來追蹤閘道上所做的任何變更，而且有助於了解先前可運作的閘道為什麼會停止運作。

![[2]][2]

監視連線，並追蹤連線失敗事件。 您可以使用監視套件 (例如 [Nagios][nagios]) 來擷取和提報此資訊。

## <a name="security-considerations"></a>安全性考量

針對每個 VPN 閘道產生不同的共用金鑰。 使用強式共用金鑰，以協助抵禦暴力密碼破解攻擊。

> [!NOTE]
> 目前，您無法使用 Azure Key Vault 預先共用 Azure VPN 閘道的金鑰。
> 
> 

請確定內部部署 VPN 設備使用的加密方法[與 Azure VPN 閘道相容][vpn-appliance-ipsec]。 針對原則型路由，Azure VPN 閘道支援 AES256、AES128 和 3DES 加密演算法。 路由型閘道支援 AES256 和 3DES。

如果您的內部部署 VPN 設備位在與網際網路之間有防火牆的周邊網路 (DMZ) 上，您可能必須設定[其他防火牆規則][additional-firewall-rules]，以允許站對站 VPN 連線。

如果 VNet 中的應用程式會將資料傳送至網際網路，請考慮[實作強制通道][forced-tunneling]，以透過內部部署網路來路由所有網際網路繫結流量。 此方法可讓您稽核應用程式在內部部署基礎結構中所做的傳出要求。

> [!NOTE]
> 強制通道可能會影響與 Azure 服務 (例如儲存體服務) 和 Windows 授權管理員的連線。
> 
> 


## <a name="troubleshooting"></a>疑難排解 

如需針對常見 VPN 相關錯誤進行疑難排解的一般資訊，請參閱[針對常見 VPN 相關錯誤進行移難排解][troubleshooting-vpn-errors]。

下列建議有助於判斷您的內部部署 VPN 設備是否正常運作。

- **請查看 VPN 設備產生的任何記錄，以檢查是否有錯誤或失敗。**

    這可協助您判斷 VPN 設備是否正確運作。 此資訊的位置會因為您的設備不同而有所差異。 例如，如果您在 Windows Server 2012 上使用 RRAS，您可以使用下列 PowerShell 命令來顯示 RRAS 服務的錯誤事件資訊：

    ```PowerShell
    Get-EventLog -LogName System -EntryType Error -Source RemoteAccess | Format-List -Property *
    ```

    每個項目的「訊息」屬性會提供錯誤的描述。 一些常見的範例包括：

        - Inability to connect, possibly due to an incorrect IP address specified for the Azure VPN gateway in the RRAS VPN network interface configuration.

        ```
        EventID            : 20111
        MachineName        : on-prem-vm
        Data               : {41, 3, 0, 0}
        Index              : 14231
        Category           : (0)
        CategoryNumber     : 0
        EntryType          : Error
        Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A demand dial connection to the remote
                             interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                             successfully because of the  following error: The network connection between your computer and
                             the VPN server could not be established because the remote server is not responding. This could
                             be because one of the network devices (for example, firewalls, NAT, routers, and so on) between your computer
                             and the remote server is not configured to allow VPN connections. Please contact your
                             Administrator or your service provider to determine which device may be causing the problem.
        Source             : RemoteAccess
        ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, The network connection between
                             your computer and the VPN server could not be established because the remote server is not
                             responding. This could be because one of the network devices (for example, firewalls, NAT, routers, and so on)
                             between your computer and the remote server is not configured to allow VPN connections. Please
                             contact your Administrator or your service provider to determine which device may be causing the
                             problem.}
        InstanceId         : 20111
        TimeGenerated      : 3/18/2016 1:26:02 PM
        TimeWritten        : 3/18/2016 1:26:02 PM
        UserName           :
        Site               :
        Container          :
        ```

        - The wrong shared key being specified in the RRAS VPN network interface configuration.

        ```
        EventID            : 20111
        MachineName        : on-prem-vm
        Data               : {233, 53, 0, 0}
        Index              : 14245
        Category           : (0)
        CategoryNumber     : 0
        EntryType          : Error
        Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A demand dial connection to the remote
                             interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                             successfully because of the  following error: Internet key exchange (IKE) authentication credentials are unacceptable.

        Source             : RemoteAccess
        ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, IKE authentication credentials are
                             unacceptable.
                             }
        InstanceId         : 20111
        TimeGenerated      : 3/18/2016 1:34:22 PM
        TimeWritten        : 3/18/2016 1:34:22 PM
        UserName           :
        Site               :
        Container          :
        ```

    您也可以使用下列 PowerShell 命令，取得嘗試透過 RRAS 服務連線的相關事件記錄資訊： 

    ```
    Get-EventLog -LogName Application -Source RasClient | Format-List -Property *
    ```

    在連線失敗的事件中，此記錄會包含看起來類似下列的錯誤：

    ```
    EventID            : 20227
    MachineName        : on-prem-vm
    Data               : {}
    Index              : 4203
    Category           : (0)
    CategoryNumber     : 0
    EntryType          : Error
    Message            : CoId={B4000371-A67F-452F-AA4C-3125AA9CFC78}: The user SYSTEM dialed a connection named
                         AzureGateway that has failed. The error code returned on failure is 809.
    Source             : RasClient
    ReplacementStrings : {{B4000371-A67F-452F-AA4C-3125AA9CFC78}, SYSTEM, AzureGateway, 809}
    InstanceId         : 20227
    TimeGenerated      : 3/18/2016 1:29:21 PM
    TimeWritten        : 3/18/2016 1:29:21 PM
    UserName           :
    Site               :
    Container          :
    ```

- **請檢查 VPN 閘道之間的連線和路由。**

    VPN 設備可能沒有正確地透過 Azure VPN 閘道路由流量。 請使用 [PsPing][psping] 等工具來檢查 VPN 閘道之間的連線和路由。 例如，若要測試內部部署機器是否能連線至位於 VNet 上的 Web 伺服器，請執行下列命令 (使用 Web 伺服器的位址取代 `<<web-server-address>>`)：

    ```
    PsPing -t <<web-server-address>>:80
    ```

    如果內部部署機器可以將流量路由至 Web 伺服器，您應該會看到類似下列的輸出：

    ```
    D:\PSTools>psping -t 10.20.0.5:80

    PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2014 Mark Russinovich
    Sysinternals - www.sysinternals.com

    TCP connect to 10.20.0.5:80:
    Infinite iterations (warmup 1) connecting test:
    Connecting to 10.20.0.5:80 (warmup): 6.21ms
    Connecting to 10.20.0.5:80: 3.79ms
    Connecting to 10.20.0.5:80: 3.44ms
    Connecting to 10.20.0.5:80: 4.81ms

      Sent = 3, Received = 3, Lost = 0 (0% loss),
      Minimum = 3.44ms, Maximum = 4.81ms, Average = 4.01ms
    ```

    如果內部部署機器無法與指定的目的地通訊，您會看到像這樣的訊息：

    ```
    D:\PSTools>psping -t 10.20.1.6:80

    PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2014 Mark Russinovich
    Sysinternals - www.sysinternals.com

    TCP connect to 10.20.1.6:80:
    Infinite iterations (warmup 1) connecting test:
    Connecting to 10.20.1.6:80 (warmup): This operation returned because the timeout period expired.
    Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
    Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
    Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
    Connecting to 10.20.1.6:80:
      Sent = 3, Received = 0, Lost = 3 (100% loss),
      Minimum = 0.00ms, Maximum = 0.00ms, Average = 0.00ms
    ```

- **請確認內部部署防火牆允許 VPN 流量通過且正確的連接埠已開啟。**

- **請確認內部部署 VPN 設備使用的加密方法[與 Azure VPN 閘道相容][vpn-appliance]。** 針對原則型路由，Azure VPN 閘道支援 AES256、AES128 和 3DES 加密演算法。 路由型閘道支援 AES256 和 3DES。

下列建議有助於判斷 Azure VPN 閘道是否有問題：

- **檢查 [Azure VPN 閘道診斷記錄][gateway-diagnostic-logs]，以查看是否有潛在問題。**

- **確認 Azure VPN 閘道與內部部署 VPN 設備是使用相同的共用驗證金鑰進行設定。**

    您可以使用下列 Azure CLI 命令，檢視 Azure VPN 閘道所儲存的共用金鑰：

    ```
    azure network vpn-connection shared-key show <<resource-group>> <<vpn-connection-name>>
    ```

    您可以使用適用於您內部部署 VPN 設備的命令，來顯示該設備上設定的共用金鑰。

    確認持有 Azure VPN 閘道的 GatewaySubnet 子網路沒有與 NSG 建立關聯。

    您可使用下列 Azure CLI 命令檢視子網路詳細資料：

    ```
    azure network vnet subnet show -g <<resource-group>> -e <<vnet-name>> -n GatewaySubnet
    ```

    請確定沒有名為「網路安全性群組識別碼」的資料欄位。下列範例會示範具有指派 NSG (VPN-Gateway-Group) 的 GatewaySubnet 執行個體結果。 如果您有對此 NSG 定義任何規則，此動作可能會使得閘道無法正常運作。

    ```
    C:\>azure network vnet subnet show -g profx-prod-rg -e profx-vnet -n GatewaySubnet
        info:    Executing command network vnet subnet show
        + Looking up virtual network "profx-vnet"
        + Looking up the subnet "GatewaySubnet"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/virtualNetworks/profx-vnet/subnets/GatewaySubnet
        data:    Name                            : GatewaySubnet
        data:    Provisioning state              : Succeeded
        data:    Address prefix                  : 10.20.3.0/27
        data:    Network Security Group id       : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/networkSecurityGroups/VPN-Gateway-Group
        info:    network vnet subnet show command OK
    ```

- **請確認 Azure VNet 中的虛擬機器設定為允許流量從外部 VNet 傳入。**

    如果有任何 NSG 規則與包含這些虛擬機器的子網路相關聯，請檢查這些規則。 您可使用下列 Azure CLI 命令檢視所有 NSG 規則：

    ```
    azure network nsg show -g <<resource-group>> -n <<nsg-name>>
    ```

- **請確認 Azure VPN 閘道已連線。**

    您可以使用下列 Azure PowerShell 命令來檢查 Azure VPN 連線的目前狀態。 `<<connection-name>>` 參數是連結虛擬網路閘道與區域閘道的 Azure VPN 連線名稱。

    ```
    Get-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> - ResourceGroupName <<resource-group>>
    ```

    下列程式碼片段會醒目提示閘道連線時 (第一個範例) 和中斷連線時 (第二個範例) 所產生的輸出：

    ```
    PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection -ResourceGroupName profx-prod-rg

    AuthorizationKey           :
    VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
    VirtualNetworkGateway2     :
    LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
    Peer                       :
    ConnectionType             : IPsec
    RoutingWeight              : 0
    SharedKey                  : ####################################
    ConnectionStatus           : Connected
    EgressBytesTransferred     : 55254803
    IngressBytesTransferred    : 32227221
    ProvisioningState          : Succeeded
    ...
    ```

    ```
    PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection2 -ResourceGroupName profx-prod-rg

    AuthorizationKey           :
    VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
    VirtualNetworkGateway2     :
    LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
    Peer                       :
    ConnectionType             : IPsec
    RoutingWeight              : 0
    SharedKey                  : ####################################
    ConnectionStatus           : NotConnected
    EgressBytesTransferred     : 0
    IngressBytesTransferred    : 0
    ProvisioningState          : Succeeded
    ...
    ```

下列建議有助於判斷主機虛擬機器設定、網路頻寬使用率或應用程式效能是否有問題：

- **如果子網路中的 Azure 虛擬機器上正在執行客體作業系統，請確認其中的防火牆已正確設定，可允許內部部署 IP 範圍中的許可流量。**

- **請確認流量尚未接近 Azure VPN 閘道可用的頻寬上限。**

    驗證此項目的方式取決於在內部部署的 VPN 設備。 例如，如果您在 Windows Server 2012 上使用 RRAS，您可以使用效能監視器來追蹤透過 VPN 連線收到和傳輸的資料量。 如果使用「RAS 總計」物件，請選取「接收的位元組/秒」和「傳輸的位元組/秒」計數器：

    ![[3]][3]

    您應比較使用 VPN 閘道可用頻寬的結果 (基本與標準 SKU 是 100 Mbps，而高效能 SKU 是 200 Mbps)：

    ![[4]][4]

- **請確認您已針對應用程式負載，部署正確的虛擬機器數量和大小。**

    判斷 Azure VNet 中是否有任何一部虛擬機器執行速度緩慢。 如果有，則表示虛擬機器負載可能過重、虛擬機器可能太少以至於無法處理負載，或是負載平衡器可能未正確設定。 若要判斷原因，可[擷取及分析診斷資訊][azure-vm-diagnostics]。 您可以使用 Azure 入口網站檢查結果，但也有許多第三方工具可協助您深入了解效能資料。

- **請確認應用程式正在有效率地使用雲端資源。**

    檢測每個虛擬機器上執行的應用程式程式碼，判斷應用程式是否以最佳方式在使用資源。 您可以使用 [Application Insights][application-insights] 等工具。

## <a name="deploy-the-solution"></a>部署解決方案


**必要條件。** 您必須已經使用適當的網路設備，設定現有的內部部署基礎結構。

若要部署解決方案，請執行下列步驟。

1. 按一下下方的按鈕：<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fvpn%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
2. 等待此連結在 Azure 入口網站中開啟，然後按照下列步驟進行： 
   * **資源群組**名稱已在參數檔案中定義，因此請在文字方塊中選取 [新建] 並輸入 `ra-hybrid-vpn-rg`。
   * 從 [位置] 下拉式方塊選取區域。
   * 請勿編輯 [範本的根 URI] 或 [參數根 URI] 文字方塊。
   * 檢閱條款和條件，然後按一下 [我同意上方所述的條款及條件] 核取方塊。
   * 按一下 [購買] 按鈕。
3. 等待部署完成。



<!-- links -->

[adds-extend-domain]: ../identity/adds-extend-domain.md
[expressroute]: ../hybrid-networking/expressroute.md
[windows-vm-ra]: ../virtual-machines-windows/index.md
[linux-vm-ra]: ../virtual-machines-linux/index.md

[naming conventions]: /azure/guidance/guidance-naming-conventions

[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[arm-templates]: /azure/resource-group-authoring-templates
[azure-cli]: /azure/virtual-machines-command-line-tools
[azure-portal]: /azure/azure-portal/resource-group-portal
[azure-powershell]: /azure/powershell-azure-resource-manager
[azure-virtual-network]: /azure/virtual-network/virtual-networks-overview
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[azure-vpn-gateway]: https://azure.microsoft.com/services/vpn-gateway/
[azure-gateway-charges]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[vpn-gateway-multi-site]: /azure/vpn-gateway/vpn-gateway-multi-site
[policy-based-routing]: https://en.wikipedia.org/wiki/Policy-based_routing
[route-based-routing]: https://en.wikipedia.org/wiki/Static_routing
[network-security-group]: /azure/virtual-network/virtual-networks-nsg
[sla-for-vpn-gateway]: https://azure.microsoft.com/support/legal/sla/vpn-gateway/v1_2/
[additional-firewall-rules]: https://technet.microsoft.com/library/dn786406.aspx#firewall
[nagios]: https://www.nagios.org/
[azure-vpn-gateway-diagnostics]: http://blogs.technet.com/b/keithmayer/archive/2014/12/18/diagnose-azure-virtual-network-vpn-connectivity-issues-with-powershell.aspx
[ping]: https://technet.microsoft.com/library/ff961503.aspx
[tracert]: https://technet.microsoft.com/library/ff961507.aspx
[psping]: http://technet.microsoft.com/sysinternals/jj729731.aspx
[nmap]: http://nmap.org
[changing-SKUs]: https://azure.microsoft.com/blog/azure-virtual-network-gateway-improvements/
[gateway-diagnostic-logs]: http://blogs.technet.com/b/keithmayer/archive/2015/12/07/step-by-step-capturing-azure-resource-manager-arm-vnet-gateway-diagnostic-logs.aspx
[troubleshooting-vpn-errors]: https://blogs.technet.microsoft.com/rrasblog/2009/08/12/troubleshooting-common-vpn-related-errors/
[rras-logging]: https://www.petri.com/enable-diagnostic-logging-in-windows-server-2012-r2-routing-and-remote-access
[create-on-prem-network]: https://technet.microsoft.com/library/dn786406.aspx#routing
[azure-vm-diagnostics]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[application-insights]: /azure/application-insights/app-insights-overview-usage
[forced-tunneling]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-forced-tunneling/
[vpn-appliances]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-architectures.vsdx
[vpn-appliance-ipsec]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec-parameters
<!--[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/Deploy-ReferenceArchitecture.ps1-->
<!--[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/deploy-reference-architecture.sh-->
<!--[virtualNetworkGateway-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/parameters/virtualNetworkGateway.parameters.json-->
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[0]: ./images/vpn.png "跨越內部部署和 Azure 基礎結構的混合式網路"
[2]: ../_images/guidance-hybrid-network-vpn/audit-logs.png "Azure 入口網站中的稽核記錄"
[3]: ../_images/guidance-hybrid-network-vpn/RRAS-perf-counters.png "監視 VPN 網路流量的效能計數器"
[4]: ../_images/guidance-hybrid-network-vpn/RRAS-perf-graph.png "範例 VPN 網路的效能圖表"
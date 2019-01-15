---
title: 具有 SQL Server 的 Windows 多層式架構 (N-tier) 應用程式
titleSuffix: Azure Reference Architectures
description: 在 Azure 上實作多層式架構，以取得可用性、安全性、延展性及管理功能。
author: MikeWasson
ms.date: 11/12/2018
ms.openlocfilehash: e7dbd8dd2b8e5aff8f18ff9b87fce0b76a850bce
ms.sourcegitcommit: 680c9cef945dff6fee5e66b38e24f07804510fa9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54011373"
---
# <a name="windows-n-tier-application-on-azure-with-sql-server"></a>Azure 上具有 SQL Server 的 Windows 多層式架構 (N-tier) 應用程式

此參考架構展示如何在 Windows 上使用 SQL Server 作為資料層，來部署針對多層式架構 (N-Tier) 應用程式設定的 VM 和虛擬網路。 [**部署這個解決方案**](#deploy-the-solution)。

![使用 Microsoft Azure 的多層式架構](./images/n-tier-sql-server.png)

下載這個架構的 [Visio 檔案][visio-download]。

## <a name="architecture"></a>架構

此架構具有下列元件：

- **資源群組**。 [資源群組][resource-manager-overview]可用來將資源組合在一起，讓它們可以依存留期、擁有者或其他準則管理。

- **虛擬網路 (VNet) 和子網路**。 每部 Azure VM 都會部署到可以分割成子網路的 VNet。 針對每一層建立不同的子網路。

- **應用程式閘道**。 [Azure 應用程式閘道](/azure/application-gateway/)是第 7 層負載平衡器。 在此架構中，它會將 HTTP 要求路由傳送至 Web 前端。 應用程式閘道也會提供 [Web 應用程式防火牆](/azure/application-gateway/waf-overview) (WAF)，該防火牆會保護應用程式免於常見惡意探索和弱點。

- **NSG**。 使用[網路安全性群組][nsg] (NSG) 來限制 VNet 內的網路流量。 例如，在如下所示的三層式架構中，資料庫層不接受來自 Web 前端的流量，只接受來自 Business 層和管理子網路的流量。

- **DDoS 保護**。 雖然 Azure 平台提供基本的保護，可抵禦分散式阻斷服務 (DDoS) 攻擊，但我們建議您使用 [DDoS 保護標準][ddos]，其中具有增強的 DDoS 風險降低功能。 請參閱[安全性考量](#security-considerations)。

- **虛擬機器**。 如需有關設定 VM 的建議，請參閱[在 Azure 上執行 Windows VM](./windows-vm.md) 和[在 Azure 上執行 Linux VM](./linux-vm.md)。

- **可用性集合**。 為每個層級建立[可用性設定組][azure-availability-sets]，並在每層中佈建至少兩部虛擬機器，可讓虛擬機器符合較高的[服務等級協定 (SLA)][vm-sla]。

- **負載平衡器**。 使用 [Azure Load Balancer][load-balancer] 將來自 Web 層的流量散佈到商務層，以及將來自商務層的流量散佈到 SQL Server。

- **公用 IP 位址**。 應用程式需要公用 IP 位址才能接收網際網路流量。

- **Jumpbox**。 也稱為[防禦主機]。 網路上系統管理員用來連線到其他 VM 的安全 VM。 Jumpbox 具有 NSG，只允許來自安全清單上公用 IP 位址的遠端流量。 NSG 應該允許遠端桌面 (RDP) 流量。

- **SQL Server Always On 可用性群組**。 藉由啟用複寫和容錯移轉，在資料層提供高可用性。 它會使用 Windows Server 容錯移轉叢集 (WSFC) 技術來進行容錯移轉。

- **Active Directory Domain Services (AD DS) 伺服器**。 容錯移轉叢集及其相關聯叢集角色的電腦物件，是在 Active Directory Domain Services (AD DS) 中建立。

- **雲端見證**。 容錯移轉叢集需要它一半以上的節點執行，也就是具有仲裁。 如果叢集只有兩個節點，網路磁碟分割可能會導致每個節點都認為它是主要節點。 在此情況下，您需要「見證」以打破和局，並建立仲裁。 見證是例如共用磁碟的資源，可以作為打破和局項目以建立仲裁。 雲端見證是使用 Azure Blob 儲存體的見證類型。 若要深入了解有關仲裁的概念，請參閱＜[了解叢集和集區仲裁](/windows-server/storage/storage-spaces/understand-quorum)＞。 如需雲端見證的詳細資訊，請參閱＜[為容錯移轉叢集部署雲端見證](/windows-server/failover-clustering/deploy-cloud-witness)＞。

- **Azure DNS**。 [Azure DNS][azure-dns] 是適用於 DNS 網域的主機服務。 其使用 Microsoft Azure 基礎結構來提供名稱解析。 只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。

## <a name="recommendations"></a>建議

您的需求可能和此處所述的架構不同。 請使用以下建議作為起點。

### <a name="vnet--subnets"></a>VNet / 子網路

當您建立 VNet 時，請判斷您在每個子網路中的資源需要多少個 IP 位址。 使用 [CIDR] 標記法，針對所需的 IP 位址指定子網路遮罩和夠大的 VNet 位址範圍。 使用落在標準[私人 IP 位址區塊][private-ip-space]內的位址空間，其為 10.0.0.0/8、172.16.0.0/12 及 192.168.0.0/16。

選擇不會與您的內部部署網路重疊的位址範圍，以防您稍後需要在 VNet 和您的內部部署網路之間設定閘道。 一旦建立 VNet 之後，就無法變更位址範圍。

請記得以功能和安全性需求來設計子網路。 位於相同層或角色的所有 VM 都應移入相同的子網路，它可以是安全性界限。 如需設計 VNet 和子網路的詳細資訊，請參閱[規劃和設計 Azure 虛擬網路][plan-network]。

### <a name="load-balancers"></a>負載平衡器

不要直接將 VM 公開至網際網路，而是改為每個 VM 提供私人 IP 位址。 用戶端會使用與應用程式閘道相關聯的公用 IP 位址來進行連線。

定義負載平衡器規則，以將網路流量導向至 VM。 例如，若要啟用 HTTP 流量，請將前端設定的連接埠 80 對應至後端位址集區的連接埠 80。 當用戶端將 HTTP 要求傳送到連接埠 80 時，負載平衡器會藉由使用[雜湊演算法][load-balancer-hashing] (其中包含來源 IP 位址) 來選取後端 IP 位址。 用戶端要求會散發到後端位址集區中的所有 VM。

### <a name="network-security-groups"></a>網路安全性群組

使用 NSG 規則來限制各層之間的流量。 在如上所示的三層式架構中，Web 層不會直接與資料庫層通訊。 若要強制執行此動作，資料庫層應該封鎖來自 Web 層子網路的連入流量。

1. 拒絕來自 VNet 的所有輸入流量。 (在規則中使用 `VIRTUAL_NETWORK` 標記)。
2. 允許來自 Business 層子網路的輸入流量。
3. 允許來自資料庫層子網路本身的輸入流量。 此規則允許資料庫 VM 之間的通訊，是進行資料庫複寫和容錯移轉所需的。
4. 允許來自 Jumpbox 子網路的 RDP 流量 (連接埠 3389)。 此規則讓系統管理員能夠從 Jumpbox 連線到資料庫層。

建立規則 2 &ndash; 4，其優先順序高於第一個規則，因此它們會覆寫它。

### <a name="sql-server-always-on-availability-groups"></a>SQL Server Always On 可用性群組

我們建議使用 [Always On 可用性群組][sql-alwayson]來獲得 SQL Server 高可用性。 在 Windows Server 2016 之前，Always On 可用性群組需要一個網域控制站，而且可用性群組中的所有節點都必須位於相同的 AD 網域。

其他各層會透過[可用性群組接聽程式][sql-alwayson-listeners]連線到資料庫。 此接聽程式讓 SQL 用戶端能夠在不知道 SQL Server 實體執行個體名稱的情況下連線。 存取資料庫的 VM 必須加入該網域。 用戶端 (在此案例中為另一層) 會使用 DNS，將接聽程式的虛擬網路名稱解析為 IP 位址。

設定 SQL Server Always On 可用性群組，如下：

1. 建立 Windows Server 容錯移轉叢集 (WSFC) 叢集、SQL Server Always On 可用性群組，以及主要複本。 如需詳細資訊，請參閱[開始使用 Always On 可用性群組][sql-alwayson-getting-started]。
2. 建立具有靜態私人 IP 位址的內部負載平衡器。
3. 建立可用性群組接聽程式，並將接聽程式的 DNS 名稱對應到內部負載平衡器的 IP 位址。
4. 建立 SQL Server 接聽連接埠的負載平衡器規則 (預設為 TCP 連接埠 1433)。 負載平衡器規則必須啟用「浮動 IP」，也稱為「伺服器直接回傳」。 這樣會導致 VM 直接回覆用戶端，以啟用與主要複本的直接連線。

   > [!NOTE]
   > 啟用浮動 IP 時，前端連接埠號碼必須與負載平衡器規則中的後端連接埠號碼相同。
   >

當 SQL 用戶端嘗試連線時，負載平衡器會將連線要求路由傳送到主要複本。 如果已容錯移轉至另一個複本，負載平衡器會自動將新要求路由傳送到新的主要複本。 如需詳細資訊，請參閱[設定 SQL Server Always On 可用性群組的 ILB 接聽程式][sql-alwayson-ilb]。

在容錯移轉期間，會關閉現有的用戶端連線。 當容錯移轉完成之後，會將新的連線路由傳送到新的主要複本。

如果您應用程式的讀取次數明顯比寫入多很多，您可以將某些唯讀查詢卸載至次要複本。 請參閱[使用接聽程式連接到唯讀次要複本 (唯讀路由)][sql-alwayson-read-only-routing]。

執行可用性群組的[強制手動容錯移轉][sql-alwayson-force-failover]來測試您的部署。

### <a name="jumpbox"></a>Jumpbox

不允許從公用網際網路對執行應用程式工作負載的 VM 進行 RDP 存取。 對這些 VM 所做的所有 RDP 存取都必須改為透過 Jumpbox 進行。 系統管理員會登入 Jumpbox，然後從 Jumpbox 登入其他 VM。 Jumpbox 允許來自網際網路，但只來自已知且安全 IP 位址的 RDP 流量。

Jumpbox 有最低效能需求，因此選取小的 VM 大小。 針對 Jumpbox 建立[公用 IP 位址]。 將 Jumpbox 放置於與其他 VM 相同的 VNet，但在個別的管理子網路中。

若要保護 Jumpbox，請新增 NSG 規則，只允許來自一組安全公用 IP 位址的 RDP 連線。 設定其他子網路的 NSG，允許來自管理子網路的 RDP 流量。

## <a name="scalability-considerations"></a>延展性考量

針對 Web 和 Business 層，請考慮使用[虛擬機器擴展集][vmss]，而不是將個別虛擬機器部署至可用性設定組。 擴展集可讓您輕鬆部署及管理一組完全相同的虛擬機器，並根據效能計量自動調整虛擬機器。 當 VM 上的負載增加時，就會將其他 VM 自動新增到負載平衡器。 如果您需要快速相應放大 VM 數目，或需要自動調整規模，請考慮擴展集。

有兩種基本方法可用來設定擴展集中部署的 VM：

- 部署虛擬機器之後，使用擴充功能來設定它。 透過此方法，新的 VM 執行個體可能需要較長的時間來啟動不具擴充功能的 VM。

- 利用自訂的磁碟映像來部署[受控磁碟](/azure/storage/storage-managed-disks-overview)。 這個選項可能會更快速地部署。 不過，它會要求您讓映像保持最新狀態。

如需詳細資訊，請參閱[擴展集的設計考量][vmss-design]。

> [!TIP]
> 使用任何自動調整規模解決方案時，事前也要利用生產層級的工作負載來進行測試。

每個 Azure 訂用帳戶都已經有預設限制，包括每個區域的 VM 最大數目。 您可以藉由提出支援要求來提高限制。 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束][subscription-limits]。

## <a name="availability-considerations"></a>可用性考量

如果您未使用虛擬機器擴展集，請在相同層級中將虛擬機器放置於可用性設定組中。 在可用性設定組中至少建立兩部 VM，以支援[適用於 Azure VM 的可用性 SLA][vm-sla]。 如需詳細資訊，請參閱[管理虛擬機器的可用性][availability-set]。 擴展集會自動使用*放置群組*，其可做為隱含的可用性設定組。

負載平衡器會使用[健康情況探查][health-probes]來監視 VM 執行個體的可用性。 如果探查無法在逾時期間內連線到執行個體，負載平衡器就會停止將流量傳送到該 VM。 不過，負載平衡器將會繼續探查，而且如果 VM 再次變成可用，負載平衡器就會繼續將流量傳送到該 VM。

以下是關於負載平衡器健康情況探查的建議：

- 探查可以測試 HTTP 或 TCP。 如果您的 VM 執行 HTTP 伺服器，請建立 HTTP 探查。 否則，建立 TCP 探查。
- 針對 HTTP 探查，指定 HTTP 端點的路徑。 探查會檢查來自此路徑的 HTTP 200 回應。 此路徑可以是根路徑 ("/")，或是實作一些自訂邏輯來檢查應用程式健康情況的健康情況監視端點。 端點必須允許匿名的 HTTP 要求。
- 探查會從[已知的 IP 位址][health-probe-ip] 168.63.129.16 傳送出來。 請勿在任何防火牆原則或 NSG 規則中封鎖往返此 IP 位址的流量。
- 使用[健康情況探查記錄][health-probe-log]來檢視健康情況探查的狀態。 能夠針對每個負載平衡器登入 Azure 入口網站。 記錄會寫入 Azure Blob 儲存體。 記錄會顯示多少虛擬機器因探查回應失敗而未取得網路流量。

如果您需要比[適用於 VM 的 Azure SLA][vm-sla] 所提供更高的可用性，請考慮跨兩個區域複寫應用程式，同時使用 Azure 流量管理員進行容錯移轉。 如需詳細資訊，請參閱[適用於高可用性的多重區域多層式架構 (N-tier) 應用程式][multi-dc]。

## <a name="security-considerations"></a>安全性考量

虛擬網路是 Azure 中的流量隔離界限。 某一個 VNet 中的 VM 無法直接與不同 VNet 中的 VM 通訊。 除非您建立[網路安全性群組][nsg] (NSG) 來限制流量，否則，相同 VNet 中的 VM 可以彼此通訊。 如需詳細資訊，請參閱 [Microsoft 雲端服務和網路安全性][network-security]。

**DMZ**。 請考慮新增網路虛擬設備 (NVA)，在網際網路和 Azure 虛擬網路之間建立 DMZ。 NVA 是虛擬設備的通稱，可以執行網路相關的工作，例如防火牆、封包檢查、稽核和自訂路由傳送。 如需詳細資訊，請參閱[實作 Azure 和網際網路之間的 DMZ][dmz]。

**加密**。 將機密的待用資料加密，並使用 [Azure Key Vault][azure-key-vault] 來管理資料庫加密金鑰。 Key Vault 可以在硬體安全模組 (HSM) 中儲存加密金鑰。 如需詳細資訊，請參閱[在 Azure VM 上設定 SQL Server 的 Azure Key Vault 整合][sql-keyvault]。 也建議將應用程式密碼 (例如資料庫連接字串) 儲存在金鑰保存庫中。

**DDoS 保護**。 Azure 平台預設會提供基本的 DDoS 保護。 此基本保護的目標是保護整個 Azure 基礎結構。 雖然基本 DDoS 保護會自動啟用，我們仍建議您使用 [DDoS 保護標準][ddos]。 標準保護使用彈性調整，可根據您的應用程式網路流量模式來偵測威脅。 這可讓它針對 DDoS 攻擊套用風險降低措施，這些攻擊可能因整個基礎結構的 DDoS 原則而未被察覺。 標準保護也會提供警示、遙測以及透過 Azure 監視器的分析。 如需詳細資訊，請參閱 [Azure DDoS 保護：最佳做法與參考架構][ddos-best-practices]。

## <a name="deploy-the-solution"></a>部署解決方案

此參考架構的部署可在 [GitHub][github-folder] 上取得。 整個部署可能需要長達 2 小時的時間，包括執行指令碼以設定 AD DS、Windows Server 容錯移轉叢集和 SQL Server 可用性設定組。

### <a name="prerequisites"></a>必要條件

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deployment-steps"></a>部署步驟

1. 執行下列命令以建立資源群組。

    ```azurecli
    az group create --location <location> --name <resource-group-name>
    ```

2. 執行下列命令為雲端見證建立儲存體帳戶。

    ```azurecli
    az storage account create --location <location> \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --sku Standard_LRS
    ```

3. 瀏覽至參考架構 GitHub 存放庫的 `virtual-machines\n-tier-windows` 資料夾。

4. 開啟 `n-tier-windows.json` 檔案。

5. 搜尋 "witnessStorageBlobEndPoint" 的所有執行個體，並且將預留位置文字取代為步驟 2 中儲存體帳戶的名稱。

    ```json
    "witnessStorageBlobEndPoint": "https://[replace-with-storageaccountname].blob.core.windows.net",
    ```

6. 執行下列命令以列出儲存體帳戶的帳戶金鑰。

    ```azurecli
    az storage account keys list \
      --account-name <storage-account-name> \
      --resource-group <resource-group-name>
    ```

    輸出應該看起來如下所示。 複製 `key1` 的值。

    ```json
    [
    {
        "keyName": "key1",
        "permissions": "Full",
        "value": "..."
    },
    {
        "keyName": "key2",
        "permissions": "Full",
        "value": "..."
    }
    ]
    ```

7. 在 `n-tier-windows.json` 檔案中，搜尋 "witnessStorageAccountKey" 的所有執行個體，並且貼至帳戶金鑰。

    ```json
    "witnessStorageAccountKey": "[replace-with-storagekey]"
    ```

8. 在 `n-tier-windows.json` 檔案中，搜尋 `[replace-with-password]` 和 `[replace-with-sql-password]` 的所有執行個體，並以強式密碼加以取代。 儲存檔案。

    > [!NOTE]
    > 如果您變更系統管理員使用者名稱，您也必須在 JSON 檔案中更新 `extensions` 區塊。

9. 執行下列命令來部署架構。

    ```azurecli
    azbb -s <your subscription_id> -g <resource_group_name> -l <location> -p n-tier-windows.json --deploy
    ```

如需使用 Azure 組建區塊部署此範例參考架構的詳細資訊，請瀏覽 [GitHub 存放庫][git]。

<!-- links -->
[dmz]: ../dmz/secure-vnet-dmz.md
[multi-dc]: multi-region-sql-server.md
[n-tier]: n-tier.md
[azure-availability-sets]: /azure/virtual-machines/virtual-machines-windows-manage-availability#configure-each-application-tier-into-separate-availability-sets
[azure-dns]: /azure/dns/dns-overview
[azure-key-vault]: https://azure.microsoft.com/services/key-vault
[防禦主機]: https://en.wikipedia.org/wiki/Bastion_host
[cidr]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[ddos]: /azure/virtual-network/ddos-protection-overview
[ddos-best-practices]: /azure/security/azure-ddos-best-practices
[git]: https://github.com/mspnp/template-building-blocks
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/n-tier-windows
[nsg]: /azure/virtual-network/virtual-networks-nsg
[plan-network]: /azure/virtual-network/virtual-network-vnet-plan-design-arm
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[公用 IP 位址]: /azure/virtual-network/virtual-network-ip-addresses-overview-arm
[sql-alwayson]: https://msdn.microsoft.com/library/hh510230.aspx
[sql-alwayson-force-failover]: https://msdn.microsoft.com/library/ff877957.aspx
[sql-alwayson-getting-started]: https://msdn.microsoft.com/library/gg509118.aspx
[sql-alwayson-ilb]: /azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener
[sql-alwayson-listeners]: https://msdn.microsoft.com/library/hh213417.aspx
[sql-alwayson-read-only-routing]: https://technet.microsoft.com/library/hh213417.aspx#ConnectToSecondary
[sql-keyvault]: /azure/virtual-machines/virtual-machines-windows-ps-sql-keyvault
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[vnet faq]: /azure/virtual-network/virtual-networks-faq
[wsfc-whats-new]: https://technet.microsoft.com/windows-server-docs/failover-clustering/whats-new-in-failover-clustering
[visio-download]: https://archcenter.blob.core.windows.net/cdn/vm-reference-architectures.vsdx
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[vmss]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview
[load-balancer]: /azure/load-balancer/
[load-balancer-hashing]: /azure/load-balancer/load-balancer-overview#load-balancer-features
[vmss-design]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview
[subscription-limits]: /azure/azure-subscription-service-limits
[availability-set]: /azure/virtual-machines/virtual-machines-windows-manage-availability
[health-probes]: /azure/load-balancer/load-balancer-overview#load-balancer-features
[health-probe-log]: /azure/load-balancer/load-balancer-monitor-log
[health-probe-ip]: /azure/virtual-network/virtual-networks-nsg#special-rules
[network-security]: /azure/best-practices-network-security

---
title: 具有 Apache Cassandra 的多層式架構 (N-tier) 應用程式
description: 如何在 Microsoft Azure 上執行適用於多層式架構的 Linux VM。
author: MikeWasson
ms.date: 05/03/2018
ms.openlocfilehash: fa5faeda4ef1dcae46181c0a3be8f4e139dc27d0
ms.sourcegitcommit: 25bf02e89ab4609ae1b2eb4867767678a9480402
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2018
ms.locfileid: "45584709"
---
# <a name="n-tier-application-with-apache-cassandra"></a>具有 Apache Cassandra 的多層式架構 (N-tier) 應用程式

此參考架構展示如何在 Linux 上使用 Apache Cassandra 作為資料層，來部署針對多層式架構 (N-Tier) 應用程式設定的 VM 和虛擬網路。 [**部署這個解決方案**。](#deploy-the-solution) 

![[0]][0]

下載這個架構的 [Visio 檔案][visio-download]。

## <a name="architecture"></a>架構 

此架構具有下列元件：

* **資源群組。** [資源群組][resource-manager-overview]可用來將資源組合在一起，讓它們可以依存留期、擁有者或其他準則管理。

* **虛擬網路 (VNet) 和子網路。** 每部 Azure VM 都會部署到可以分割成多個子網路的 VNet。 針對每一層建立不同的子網路。 

* **NSG。** 使用[網路安全性群組][nsg] (NSG) 來限制 VNet 內的網路流量。 例如，在如下所示的 3 層式架構中，資料庫層不接受來自 Web 前端的流量，只接受來自 Business 層和管理子網路的流量。

* **虛擬機器**。 如需有關設定 VM 的建議，請參閱[在 Azure 上執行 Windows VM](./windows-vm.md) 和[在 Azure 上執行 Linux VM](./linux-vm.md)。

* **可用性設定組。** 針對每一層建立[可用性設定組][azure-availability-sets]，並且在每一層中至少佈建兩個 VM。 這讓 VM 能夠符合適用於 VM 之較高[服務等級協定 (SLA)][vm-sla] 的資格。 

* **VM 擴展集** (未顯示)。 [VM 擴展集][vmss]是使用可用性設定組的替代方案。 擴展集可讓您輕鬆地根據預先定義的規則，手動或自動相應放大層中的 VM。

* **Azure 負載平衡器。** [負載平衡器][load-balancer]會將連入網際網路要求散發到 VM 執行個體。 使用[公用負載平衡器][load-balancer-external]，可將連入的網際網路流量散發到 Web 層，使用[內部負載平衡器][load-balancer-internal]，則可將來自 Web 層的網路流量散發到 Business 層。

* **公用 IP 位址**。 公用負載平衡器需要公用 IP 位址才能接收網際網路流量。

* **Jumpbox。** 也稱為[防禦主機]。 網路上系統管理員用來連線到其他 VM 的安全 VM。 Jumpbox 具有 NSG，能夠儘允許來自安全清單上公用 IP 位址的遠端流量。 NSG 應該允許安全殼層 (SSH) 流量。

* **Apache Cassandra 資料庫**。 藉由啟用複寫和容錯移轉，在資料層提供高可用性。

* **Azure DNS**。 [Azure DNS][azure-dns] 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構提供名稱解析。 只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。

## <a name="recommendations"></a>建議

您的需求可能和此處所述的架構不同。 請使用以下建議作為起點。 

### <a name="vnet--subnets"></a>VNet / 子網路

當您建立 VNet 時，請判斷您在每個子網路中的資源需要多少個 IP 位址。 使用 [CIDR] 標記法，針對所需的 IP 位址指定子網路遮罩和夠大的 VNet 位址範圍。 使用落在標準[私人 IP 位址區塊][private-ip-space]內的位址空間，其為 10.0.0.0/8、172.16.0.0/12 及 192.168.0.0/16。

選擇不會與您的內部部署網路重疊的位址範圍，以防您稍後需要在 VNet 和您的內部部署網路之間設定閘道。 一旦建立 VNet 之後，就無法變更位址範圍。

請記得以功能和安全性需求來設計子網路。 位於相同層或角色的所有 VM 都應移入相同的子網路，它可以是安全性界限。 如需設計 VNet 和子網路的詳細資訊，請參閱[規劃和設計 Azure 虛擬網路][plan-network]。

### <a name="load-balancers"></a>負載平衡器

不要直接將 VM 公開至網際網路，而是改為每個 VM 提供私人 IP 位址。 用戶端會使用公用負載平衡器的 IP 位址來連線。

定義負載平衡器規則，以將網路流量導向至 VM。 例如，若要啟用 HTTP 流量，請建立一個規則，將前端設定的連接埠 80 對應至後端位址集區的連接埠 80。 當用戶端將 HTTP 要求傳送到連接埠 80 時，負載平衡器會藉由使用[雜湊演算法][load-balancer-hashing] (其中包含來源 IP 位址) 來選取後端 IP 位址。 如此一來，就會將用戶端要求散發到所有 VM。

### <a name="network-security-groups"></a>網路安全性群組

使用 NSG 規則來限制各層之間的流量。 例如，在如上所示的 3 層式架構中，Web 層不會直接與資料庫層通訊。 若要強制執行此動作，資料庫層應該封鎖來自 Web 層子網路的連入流量。  

1. 拒絕來自 VNet 的所有輸入流量。 (在規則中使用 `VIRTUAL_NETWORK` 標記)。 
2. 允許來自 Business 層子網路的輸入流量。  
3. 允許來自資料庫層子網路本身的輸入流量。 此規則允許資料庫 VM 之間的通訊，是進行資料庫複寫和容錯移轉所需的。
4. 允許來自 Jumpbox 子網路的 ssh 流量 (連接埠 22)。 此規則讓系統管理員能夠從 Jumpbox 連線到資料庫層。

建立規則 2 &ndash; 4，其優先順序高於第一個規則，因此它們會覆寫它。

### <a name="cassandra"></a>Cassandra

我們建議針對生產環境使用 [DataStax Enterprise][datastax]，但這些建議適用於所有的 Cassandra 版本。 如需在 Azure 中執行 DataStax 的詳細資訊，請參閱[適用於 Azure 的DataStax Enterprise 部署指南][cassandra-in-azure]。 

將適用於 Cassandra 叢集的 VM 放置於可用性設定組，以確保會將 Cassandra 複本散發到多個容錯網域和升級網域。 如需容錯網域和升級網域的詳細資訊，請參閱[管理虛擬機器的可用性][azure-availability-sets]。 

針對每個可用性設定組設定三個錯誤網域 (最大值)，以及針對每個可用性設定組設定 18 個升級網域。 這提供仍能平均散發到容錯網域的升級網域數目上限。   

在機架感知的模式中設定節點。 在 `cassandra-rackdc.properties` 檔案中，將容錯網域對應到機架。

您不需要叢集前方的負載平衡器。 用戶端會直接連線至用戶端中的節點。

如需高可用性，在多個 Azure 區域中部署 Cassandra。 在每個區域中，會在機架感知的模式中使用錯誤和升級網域來設定節點，以便在區域內部提供復原能力。


### <a name="jumpbox"></a>Jumpbox

不允許從公用網際網路對執行應用程式工作負載的 VM 進行 ssh 存取。 對這些 VM 所做的所有 ssh 存取都必須改為透過 Jumpbox 進行。 系統管理員會登入 Jumpbox，然後從 Jumpbox 登入其他 VM。 Jumpbox 允許來自網際網路，但只來自已知且安全 IP 位址的 ssh 流量。

Jumpbox 有最低效能需求，因此選取小的 VM 大小。 針對 Jumpbox 建立[公用 IP 位址]。 將 Jumpbox 放置於與其他 VM 相同的 VNet，但在個別的管理子網路中。

若要保護 Jumpbox，請新增 NSG 規則，只允許來自一組安全公用 IP 位址的 ssh 連線。 設定其他子網路的 NSG，允許來自管理子網路的 ssh 流量。

## <a name="scalability-considerations"></a>延展性考量

[VM 擴展集][vmss]可協助您部署及管理一組完全相同的 VM。 擴展集支援根據效能計量自動調整規模。 當 VM 上的負載增加時，就會將其他 VM 自動新增到負載平衡器。 如果您需要快速相應放大 VM 數目，或需要自動調整規模，請考慮擴展集。

有兩種基本方法可用來設定擴展集中部署的 VM：

- 佈建 VM 之後，使用擴充功能來設定它。 透過此方法，新的 VM 執行個體可能需要較長的時間來啟動不具擴充功能的 VM。

- 利用自訂的磁碟映像來部署[受控磁碟](/azure/storage/storage-managed-disks-overview)。 這個選項可能會更快速地部署。 不過，它會要求您讓映像保持最新狀態。

如需其他考量，請參閱[擴展集的設計考量][vmss-design]。

> [!TIP]
> 使用任何自動調整規模解決方案時，事前也要利用生產層級的工作負載來進行測試。

每個 Azure 訂用帳戶都已經有預設限制，包括每個區域的 VM 最大數目。 您可以藉由提出支援要求來提高限制。 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束][subscription-limits]。

## <a name="availability-considerations"></a>可用性考量

如果您不是使用 VM 擴展集，請將同一層中的 VM 放入可用性設定組。 在可用性設定組中至少建立兩部 VM，以支援[適用於 Azure VM 的可用性 SLA][vm-sla]。 如需詳細資訊，請參閱[管理虛擬機器的可用性][availability-set]。 

負載平衡器會使用[健康情況探查][health-probes]來監視 VM 執行個體的可用性。 如果探查無法在逾時期間內連線到執行個體，負載平衡器就會停止將流量傳送到該 VM。 不過，負載平衡器將會繼續探查，而且如果 VM 再次變成可用，負載平衡器就會繼續將流量傳送到該 VM。

以下是關於負載平衡器健康情況探查的建議：

* 探查可以測試 HTTP 或 TCP。 如果您的 VM 執行 HTTP 伺服器，請建立 HTTP 探查。 否則，建立 TCP 探查。
* 針對 HTTP 探查，指定 HTTP 端點的路徑。 探查會檢查來自此路徑的 HTTP 200 回應。 這可以是根路徑 ("/")，或是實作一些自訂邏輯來檢查應用程式健康情況的健康情況監視端點。 端點必須允許匿名的 HTTP 要求。
* 探查會從[已知的 IP 位址][health-probe-ip] 168.63.129.16 傳送出來。 請確定您並未在任何防火牆原則或網路安全性群組 (NSG) 規則中封鎖往返此 IP 位址的流量。
* 使用[健康情況探查記錄][health-probe-log]來檢視健康情況探查的狀態。 能夠針對每個負載平衡器登入 Azure 入口網站。 記錄會寫入 Azure Blob 儲存體。 記錄會顯示後端有多少個 VM 因探查回應失敗而不會接收網路流量。

針對 Cassandra 叢集，要考慮的容錯移轉案例取決於應用程式所使用的一致性層級，以及使用的複本數目。 如需 Cassandra 中的一致性層級和使用方式，請參閱[設定資料一致性][cassandra-consistency]和 [Cassandra：有多少節點會與仲裁交談？][cassandra-consistency-usage] Cassandra 中的資料可用性取決於應用程式所使用的一致性層級和複寫機制。 如需 Cassandra 中的複寫，請參閱 [NoSQL 資料庫的資料複寫說明][cassandra-replication]。

## <a name="security-considerations"></a>安全性考量

虛擬網路是 Azure 中的流量隔離界限。 某一個 VNet 中的 VM 無法直接與不同 VNet 中的 VM 通訊。 除非您建立[網路安全性群組][nsg] (NSG) 來限制流量，否則，相同 VNet 中的 VM 可以彼此通訊。 如需詳細資訊，請參閱 [Microsoft 雲端服務和網路安全性][network-security]。

針對傳入的網際網路流量，負載平衡器規則會定義哪些流量可以連線到後端。 不過，負載平衡器規則不支援 IP 安全清單，因此，如果您想要將特定的公用 IP 位址新增至安全清單，請將 NSG 新增至子網路。

請考慮新增網路虛擬設備 (NVA)，在網際網路和 Azure 虛擬網路之間建立 DMZ。 NVA 是虛擬設備的通稱，可以執行網路相關的工作，例如防火牆、封包檢查、稽核和自訂路由傳送。 如需詳細資訊，請參閱[實作 Azure 和網際網路之間的 DMZ][dmz]。

將機密的待用資料加密，並使用 [Azure Key Vault][azure-key-vault] 來管理資料庫加密金鑰。 Key Vault 可以在硬體安全模組 (HSM) 中儲存加密金鑰。 也建議將應用程式密碼 (例如資料庫連接字串) 儲存在金鑰保存庫中。

我們建議啟用 [Azure DDoS 保護標準](/azure/virtual-network/ddos-protection-overview)，該標準為 VNet 中的資源提供額外的 DDoS 安全防護功能。 雖然基本 DDoS 保護會隨著 Azure 平台而自動啟用，但 Azure DDoS 保護標準提供了專門針對 Azure 虛擬網路資源進行調整的安全防護功能。  

## <a name="deploy-the-solution"></a>部署解決方案

此參考架構的部署可在 [GitHub][github-folder] 上取得。 

### <a name="prerequisites"></a>必要條件

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-the-solution-using-azbb"></a>使用 azbb 部署解決方案

若要以多層式架構 (N-Tier) 應用程式參考架構部署 Linux VM，請依以下步驟進行：

1. 瀏覽至您在上述必要條件步驟 1 中複製存放庫的 `virtual-machines\n-tier-linux` 資料夾。

2. 參數檔案會指定部署中每個 VM 的預設管理員使用者名稱及密碼。 您必須在部署參考架構前先變更這些內容。 開啟 `n-tier-linux.json` 檔案，並用新設定取代每個 **adminUsername** 和 **adminPassword** 欄位。   儲存檔案。

3. 使用如下所示的 **azbb** 命令列來部署參考架構。

   ```bash
   azbb -s <your subscription_id> -g <your resource_group_name> -l <azure region> -p n-tier-linux.json --deploy
   ```

如需使用 Azure 組建區塊部署此範例參考架構的詳細資訊，請瀏覽 [GitHub 存放庫][git]。

<!-- links -->
[dmz]: ../dmz/secure-vnet-dmz.md
[multi-vm]: ./multi-vm.md
[naming conventions]: /azure/guidance/guidance-naming-conventions
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[azure-administration]: /azure/automation/automation-intro
[azure-availability-sets]: /azure/virtual-machines/virtual-machines-linux-manage-availability
[azure-cli-2]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[azure-dns]: /azure/dns/dns-overview
[azure-key-vault]: https://azure.microsoft.com/services/key-vault

[防禦主機]: https://en.wikipedia.org/wiki/Bastion_host
[cassandra-in-azure]: https://academy.datastax.com/resources/deployment-guide-azure
[cassandra-consistency]: http://docs.datastax.com/en/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
[cassandra-replication]: http://www.planetcassandra.org/data-replication-in-nosql-databases-explained/
[cassandra-consistency-usage]: https://medium.com/@foundev/cassandra-how-many-nodes-are-talked-to-with-quorum-also-should-i-use-it-98074e75d7d5#.b4pb4alb2

[cidr]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[datastax]: http://www.datastax.com/products/datastax-enterprise
[git]: https://github.com/mspnp/template-building-blocks
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/n-tier-linux
[lb-external-create]: /azure/load-balancer/load-balancer-get-started-internet-portal
[lb-internal-create]: /azure/load-balancer/load-balancer-get-started-ilb-arm-portal
[load-balancer-external]: /azure/load-balancer/load-balancer-internet-overview
[load-balancer-internal]: /azure/load-balancer/load-balancer-internal-overview
[nsg]: /azure/virtual-network/virtual-networks-nsg
[nsg-rules]: /azure/azure-resource-manager/best-practices-resource-manager-security#network-security-groups
[operations-management-suite]: https://www.microsoft.com/server-cloud/operations-management-suite/overview.aspx
[plan-network]: /azure/virtual-network/virtual-network-vnet-plan-design-arm
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[公用 IP 位址]: /azure/virtual-network/virtual-network-ip-addresses-overview-arm
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[vnet faq]: /azure/virtual-network/virtual-networks-faq
[visio-download]: https://archcenter.blob.core.windows.net/cdn/vm-reference-architectures.vsdx
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[0]: ./images/n-tier-cassandra.png "使用 Microsoft Azure 的多層式架構"

[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview 
[vmss]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview
[load-balancer]: /azure/load-balancer/load-balancer-get-started-internet-arm-cli
[load-balancer-hashing]: /azure/load-balancer/load-balancer-overview#load-balancer-features
[vmss-design]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview
[subscription-limits]: /azure/azure-subscription-service-limits
[availability-set]: /azure/virtual-machines/virtual-machines-windows-manage-availability
[health-probes]: /azure/load-balancer/load-balancer-overview#load-balancer-features
[health-probe-log]: /azure/load-balancer/load-balancer-monitor-log
[health-probe-ip]: /azure/virtual-network/virtual-networks-nsg#special-rules
[network-security]: /azure/best-practices-network-security
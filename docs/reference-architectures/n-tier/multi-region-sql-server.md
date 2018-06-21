---
title: 適用於高可用性的多重區域多層式架構 (N-tier) 應用程式
description: 如何在 Azure 上的多個區域中部署 VM 以獲得高可用性和復原能力。
author: MikeWasson
ms.date: 05/03/2018
pnp.series.title: Windows VM workloads
pnp.series.prev: n-tier
ms.openlocfilehash: 48943094e7847e39b9fdc4c3f71e27f2e6e41293
ms.sourcegitcommit: a5e549c15a948f6fb5cec786dbddc8578af3be66
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2018
ms.locfileid: "33673570"
---
# <a name="multi-region-n-tier-application-for-high-availability"></a>適用於高可用性的多重區域多層式架構 (N-tier) 應用程式

此參考架構顯示一組經過證實的作法，其可在多個 Azure 區域中執行多層式架構應用程式，以實現可用性和強固的災害復原基礎結構。 

[![0]][0] 

下載這個架構的 [Visio 檔案][visio-download]。

## <a name="architecture"></a>架構 

此架構係根據[具有 SQL Server 的多層式架構 (N-tier) 應用程式](n-tier-sql-server.md)中顯示的架構而建置的。 

* **主要和次要區域**。 使用兩個區域以實現更高的可用性。 其中一個是主要區域。 另一個則用於容錯移轉。

* **Azure 流量管理員**。 [流量管理員][traffic-manager]會將連入要求路由傳送到其中一個區域。 正常作業期間，它會將要求路由傳送到主要區域。 如果該區域變得無法使用，流量管理員就會容錯移轉到次要區域。 如需詳細資訊，請參閱[流量管理員設定](#traffic-manager-configuration)。

* **資源群組**。 分別針對主要區域、次要區域及流量管理員建立[資源群組][resource groups]。 這可讓您彈性地將每個區域當作單一資源集合來管理。 例如，您可以重新部署某一個區域，而不需拿掉另一個區域。 [連結資源群組][resource-group-links]，如此一來，您就能執行查詢，以列出應用程式的所有資源。

* **VNet**。 針對每個區域建立不同的 VNet。 確定位址空間並未重疊。 

* **SQL Server Always On 可用性群組**。 如果您使用 SQL Server，我們建議使用 [SQL Always On 可用性群組][sql-always-on]來獲得高可用性。 建立單一可用性群組，以包含這兩個區域中的 SQL Server 執行個體。 

    > [!NOTE]
    > 另請考慮 [Azure SQL Database][azure-sql-db]，其可提供關聯式資料庫作為雲端服務。 使用 SQL Database 時，您不需要設定可用性群組或管理容錯移轉。  
    > 

* **VPN 閘道**。 在每個 VNet 中建立 [VPN 閘道][vpn-gateway]，並設定 [VNet 對 VNet 連線][vnet-to-vnet]，以啟用這兩個 VNet 之間的網路流量。 這對於 SQL Always On 可用性群組是必要的。

## <a name="recommendations"></a>建議

多區域架構可以提供比部署到單一區域更高的可用性。 如果區域中斷會影響主要區域，您可以使用[流量管理員][traffic-manager]容錯移轉到次要區域。 如果應用程式的個別子系統失敗，此架構也可以提供協助。

有數種一般方法可用來跨區域實現高可用性： 

* 搭配熱待命的主動/被動。 流量會傳送到其中一個區域，而另一個區域會以熱待命模式等候。 熱待命表示次要區域中隨時都已配置 VM 且正在執行中。
* 搭配冷待命的主動/被動。 流量會傳送到其中一個區域，而另一個區域會以冷待命模式等候。 冷待命表示在需要容錯移轉之前，不會在次要區域中配置 VM。 此方式的執行成本較小，但在失敗發生時通常需要較久的時間才能上線。
* 主動/主動。 兩個區域均為主動，而且要求會在它們之間負載平衡。 如果其中一個區域變成無法使用，就會將它從輪替中剔除。 

此參考架構著重於搭配熱待命的主動/被動 (使用流量管理員進行容錯移轉)。 請注意，您可以部署少量的 VM 來進行熱待命，然後視需要相應放大。

### <a name="regional-pairing"></a>區域配對

每個 Azure 區域都會與相同地理位置內的另一個區域配對。 通常會從相同區域配對選擇區域 (例如，美國東部 2 和美國中部)。 這樣做的優點包括：

* 如果發生廣泛的中斷，至少會優先復原每個配對中的一個區域。
* 已計劃的 Azure 系統更新會循序在配對區域中推出，以將可能的停機時間降到最低。
* 配對會位於相同的地理位置，以符合資料落地需求。 

不過，請確定這兩個區域均支援您應用程式所需的所有 Azure 服務 (請參閱[依區域提供的服務][services-by-region])。 如需區域配對的詳細資訊，請參閱[業務持續性和災害復原 (BCDR)：Azure 配對的區域][regional-pairs]。

### <a name="traffic-manager-configuration"></a>流量管理員設定

設定流量管理員時，請考慮下列各點：

* **路由**。 流量管理員支援數個[路由演算法][tm-routing]。 針對本文所述的案例，請使用「優先順序」路由 (先前稱為「容錯移轉」路由)。 使用此設定，流量管理員會將所有要求傳送到主要區域，除非主要地區變成無法連線。 那時，就會自動容錯移轉到次要區域。 請參閱[設定容錯移轉路由方法][tm-configure-failover]。
* **健康情況探查**。 流量管理員會使用 HTTP (或 HTTPS) [探查][tm-monitoring]來監視每個區域的可用性。 探查會針對指定的 URL 路徑檢查 HTTP 200 回應。 最佳作法是，建立端點來報告應用程式的整體健康情況，並使用此端點進行健康情況探查。 否則，探查可能會在應用程式的關鍵組件真的失敗時報告端點狀況良好。 如需詳細資訊，請參閱[健康情況端點監視模式][health-endpoint-monitoring-pattern]。   

當流量管理員容錯移轉時，用戶端會有一段時間無法連線到應用程式。 持續時間會受到下列因素影響：

* 健康情況探查必須偵測到主要區域已變成無法連線。
* DNS 伺服器必須針對 IP 位址更新快取的 DNS 記錄，這取決於 DNS 存留時間 (TTL)。 預設的 TTL 是 300 秒 (5 分鐘)，但是當您建立流量管理員設定檔時，您可以設定此值。

如需詳細資料，請參閱[關於流量管理員監視][tm-monitoring]。

如果流量管理員容錯移轉，我們建議執行手動容錯回復，而不是實作自動容錯回復。 或者，您可以建立應用程式會在區域之間來回翻轉的情況。 請確認所有的應用程式子系統狀況良好，然後再進行容錯回復。

請注意，流量管理員預設會自動容錯回復。 若要避免這個問題，請在容錯移轉事件之後，手動降低主要區域的優先順序。 例如，假設主要區域是優先順序 1，而次要為優先順序 2。 在容錯移轉之後，將主要區域設定為優先順序 3，以防止自動容錯回復。 當您準備好切換回來時，將優先順序更新為 1。

下列 [Azure CLI][install-azure-cli] 命令會更新優先順序：

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --priority 3
```    

另一種方法是暫時停用端點，直到您準備好進行容錯回復為止：

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --status Disabled
```

依據容錯移轉的原因而定，您可能需要重新部署區域內的資源。 容錯回復之前，請執行操作性整備測試。 測試應該確認如下的事項：

* VM 已正確設定 (已安裝所有必要的軟體，IIS 正在執行等設定)。
* 應用程式子系統均狀況良好。 
* 功能測試 (例如，資料庫層可從 Web 層連線)。

### <a name="configure-sql-server-always-on-availability-groups"></a>設定 SQL Server Always On 可用性群組

在 Windows Server 2016 之前，SQL Server Always On 可用性群組需要一個網域控制站，而且可用性群組中的所有節點都必須位於相同的 Active Directory (AD) 網域。 

設定可用性群組：

* 在每個區域中至少放置兩個網域控制站。
* 為每個網域控制站提供一個靜態 IP 位址。
* 建立 VNet 對 VNet 連線以啟用 VNet 之間的通訊。
* 針對每個 VNet，將網域控制站的 IP 位址 (從這兩個區域) 新增到 DNS 伺服器清單。 您可以使用下列 CLI 命令。 如需詳細資訊，請參閱[管理虛擬網路 (VNet) 所使用的 DNS 伺服器][vnet-dns]。

    ```bat
    azure network vnet set --resource-group dc01-rg --name dc01-vnet --dns-servers "10.0.0.4,10.0.0.6,172.16.0.4,172.16.0.6"
    ```

* 建立 [Windows Server 容錯移轉叢集][wsfc] (WSFC) 叢集，其中包含這兩個區域中的 SQL Server 執行個體。 
* 建立 SQL Server Always On 可用性群組，其中包含主要和次要區域中的 SQL Server 執行個體。 如需相關步驟，請參閱[將 Always On 可用性群組擴充到遠端 Azure 資料中心 (PowerShell)](https://blogs.msdn.microsoft.com/sqlcat/2014/09/22/extending-alwayson-availability-group-to-remote-azure-datacenter-powershell/) \(英文\)。

  * 將主要複本放置於主要區域。
  * 將一或多個次要複本放置於主要區域。 設定這些來使用具有自動容錯移轉的同步認可。
  * 將一或多個次要複本放置於次要區域。 基於效能考量，設定這些來使用「非同步」認可 (否則，所有的 T-SQL 交易都必須等候透過網路到次要區域的來回行程)。

    > [!NOTE]
    > 非同步認可複本不支援自動容錯移轉。
    >
    >

## <a name="availability-considerations"></a>可用性考量

使用複雜的多層式架構應用程式，您可能不需要在次要區域中複寫整個應用程式， 而是可能只需複寫支援商務持續性所需的關鍵子系統。

流量管理員可能是此系統中的失敗點。 如果流量管理員服務失敗，用戶端就無法在當機期間存取您的應用程式。 檢閱[流量管理員 SLA][tm-sla]，並判斷單獨使用流量管理員是否符合您獲得高可用性的業務需求。 如果沒有，請考慮新增另一個流量管理解決方案作為容錯回復。 如果 Azure 流量管理員服務失敗，請變更您在 DNS 中的 CNAME 記錄，以指向其他流量管理服務 (此步驟必須手動執行，而且在傳播 DNS 變更之前，將無法使用您的應用程式)。

對於 SQL Server 叢集，有兩個要考慮的容錯移轉案例：

- 主要區域中的所有 SQL Server 資料庫複本都失敗。 例如，這可能會在區域中斷期間發生。 在該情況下，您必須手動容錯移轉可用性群組，即使流量管理員會在前端自動容錯移轉。 請依照[執行 SQL Server 可用性群組的強制手動容錯移轉](https://msdn.microsoft.com/library/ff877957.aspx)，其中描述如何使用 SQL Server 2016 中的 SQL Server Management Studio、Transact-SQL 或 PowerShell 執行強制容錯移轉。

   > [!WARNING]
   > 使用強制容錯移轉，會有資料遺失的風險。 當主要區域再次上線之後，請取得資料庫的快照集，然後使用 [tablediff] 來找出差異。
   >
   >
- 流量管理員會容錯移轉到次要區域，但仍然可以使用主要的 SQL Server 資料庫複本。 例如，前端層可能失敗，但不會影響 SQL Server VM。 在該情況下，會將網際網路流量路由傳送到次要區域，而且該區域仍然可以連線到主要複本。 不過，將會增加延遲，因為 SQL Server 連線會跨區域進行。 在此情況下，您應該執行手動容錯移轉，如下所示：

   1. 將次要區域中的 SQL Server 資料庫複本暫時切換到「同步」認可。 這確保在容錯移轉期間將不會遺失資料。
   2. 容錯移轉到該複本。
   3. 當您容錯回復到主要區域時，還原非同步認可設定。

## <a name="manageability-considerations"></a>管理性考量

當您更新部署時，請一次更新一個區域，以減少因為應用程式中的不正確設定或錯誤而導致全域失敗的機會。

測試系統對於失敗的復原能力。 以下是一些要測試的常見失敗案例：

* 關閉 VM 執行個體。
* 壓力資源，例如 CPU 和記憶體。
* 中斷連線/延遲網路。
* 毀損程序。
* 憑證到期。
* 模擬硬體故障。
* 關閉網域控制站上的 DNS 服務。

測量復原時間，並確認它們符合您的業務需求。 此外，也需測試失敗模式組合。



<!-- Links -->
[hybrid-vpn]: ../hybrid-networking/vpn.md
[azure-dns]: /azure/dns/dns-overview
[azure-sla]: https://azure.microsoft.com/support/legal/sla/
[azure-sql-db]: https://azure.microsoft.com/documentation/services/sql-database/
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[install-azure-cli]: /azure/xplat-cli-install
[regional-pairs]: /azure/best-practices-availability-paired-regions
[resource groups]: /azure/azure-resource-manager/resource-group-overview
[resource-group-links]: /azure/resource-group-link-resources
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[services-by-region]: https://azure.microsoft.com/regions/#services
[sql-always-on]: https://msdn.microsoft.com/library/hh510230.aspx
[tablediff]: https://msdn.microsoft.com/library/ms162843.aspx
[tm-configure-failover]: /azure/traffic-manager/traffic-manager-configure-failover-routing-method
[tm-monitoring]: /azure/traffic-manager/traffic-manager-monitoring
[tm-routing]: /azure/traffic-manager/traffic-manager-routing-methods
[tm-sla]: https://azure.microsoft.com/support/legal/sla/traffic-manager/v1_0/
[traffic-manager]: https://azure.microsoft.com/services/traffic-manager/
[visio-download]: https://archcenter.blob.core.windows.net/cdn/vm-reference-architectures.vsdx
[vnet-dns]: /azure/virtual-network/virtual-networks-manage-dns-in-vnet
[vnet-to-vnet]: /azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps
[vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[wsfc]: https://msdn.microsoft.com/library/hh270278.aspx

[0]: ./images/multi-region-sql-server.png "適用於 Azure 多層式架構應用程式的高可用性網路架構"

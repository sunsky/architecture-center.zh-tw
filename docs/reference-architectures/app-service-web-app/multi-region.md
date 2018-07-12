---
title: 多區域 Web 應用程式
description: 在 Microsoft Azure 中執行的高可用性 Web 應用程式建議使用架構。
author: MikeWasson
ms.date: 11/23/2016
cardTitle: Run in multiple regions
ms.openlocfilehash: 00309e58c163a64f6d9796bedc19d936afcd09ab
ms.sourcegitcommit: 5d99b195388b7cabba383c49a81390ac48f86e8a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37958818"
---
# <a name="run-a-web-application-in-multiple-regions"></a>在多個區域中執行 Web 應用程式
[!INCLUDE [header](../../_includes/header.md)]

此參考架構示範如何在多個區域中執行 Azure App Service 應用程式以實現高可用性。 

![參考架構︰高可用性的 Web 應用程式](./images/multi-region-web-app-diagram.png) 

下載這個架構的 [Visio 檔案][visio-download]。

## <a name="architecture"></a>架構 

此架構是根據[增進 Web 應用程式中的延展性][guidance-web-apps-scalability] 中的架構而建置。 主要差別在於：

* **主要和次要區域**。 此架構使用兩個區域以實現更高的可用性。 應用程式會部署到每個區域。 正常作業期間，會將網路流量路由傳送到主要區域。 如果主要區域變得無法使用，就會將流量流量傳送到次要區域。 
* **Azure DNS**。 [Azure DNS][azure-dns] 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構提供名稱解析。 只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。
* **Azure 流量管理員**。 [流量管理員][traffic-manager]會將傳入要求路由傳送到主要區域。 如果應用程式執行的區域變得無法使用，流量管理員就會容錯移轉到次要區域。
* SQL Database 和 Cosmos DB 的**異地複寫**。 

多區域架構可以提供比部署到單一區域更高的可用性。 如果區域中斷會影響主要區域，您可以使用[流量管理員][traffic-manager]容錯移轉到次要區域。 如果應用程式的個別子系統失敗，此架構也可以提供協助。

有數種一般方法可用來跨區域實現高可用性： 

* 搭配熱待命的主動/被動。 流量會傳送到其中一個區域，而另一個區域會以熱待命模式等候。 熱待命表示次要區域中隨時都已配置 VM 且正在執行中。
* 搭配冷待命的主動/被動。 流量會傳送到其中一個區域，而另一個區域會以冷待命模式等候。 冷待命表示在需要容錯移轉之前，不會在次要區域中配置 VM。 此方式的執行成本較小，但在失敗發生時通常需要較久的時間才能上線。
* 主動/主動。 兩個區域均為主動，而且要求會在它們之間負載平衡。 如果其中一個區域變成無法使用，就會將它從輪替中剔除。 

此參考架構著重於搭配熱待命的主動/被動，使用流量管理員進行容錯移轉。 


## <a name="recommendations"></a>建議

您的需求可能和此處所述的架構不同。 請使用本節的建議作為起點。

### <a name="regional-pairing"></a>區域配對
每個 Azure 區域都會與相同地理位置內的另一個區域配對。 通常會從相同區域配對選擇區域 (例如，美國東部 2 和美國中部)。 這樣做的優點包括：

* 如果有大範圍中斷，至少優先復原每個配對中的一個區域。
* 已計劃的 Azure 系統更新會循序在配對區域中展開，將可能的停機時間降到最低。
* 在大部分情況下，區域配對會位於相同的地理位置，以符合資料落地需求。

不過，請確定這兩個區域均支援您應用程式所需的所有 Azure 服務。 請參閱[依區域提供的服務][services-by-region]。 如需區域配對的詳細資訊，請參閱[商務持續性和災害復原 (BCDR)：Azure 配對的區域][regional-pairs]。

### <a name="resource-groups"></a>資源群組
請考慮將主要區域、次要區域、流量管理員放在不同的[資源群組][resource groups]中。 這可讓您將部署至每個區域的資源當做單一集合來管理。

### <a name="traffic-manager-configuration"></a>流量管理員設定 

**路由**。 流量管理員支援數個[路由演算法][tm-routing]。 針對本文所述的案例，請使用「優先順序」路由 (先前稱為「容錯移轉」路由)。 使用此設定，流量管理員會將所有要求傳送到主要區域，除非該區域的端點變成無法連線。 那時，就會自動容錯移轉到次要區域。 請參閱[設定容錯移轉路由方法][tm-configure-failover]。

**健康情況探查**。 流量管理員會使用 HTTP (或 HTTPS) 探查來監視每個端點區域的可用性。 探查會提供容錯移轉到次要區域的測試結果 (通過/失敗) 給流量管理員。 運作方式是將要求傳送至指定的 URL 路徑。 如果在逾時期間內得到非 200 的回應，則探查失敗。 四次失敗的要求之後，流量管理員會將端點標示為降級，並容錯移轉至另一個端點。 如需詳細資訊，請參閱[流量管理員端點監視和容錯移轉][tm-monitoring]。

最佳做法是，建立健康情況探查端點來報告應用程式的整體健康情況，並使用此端點進行健康情況探查。 端點應該檢查關鍵性的相依性，例如 App Service 應用程式、儲存體佇列、SQL Database。 否則，探查可能會在應用程式的關鍵組件真的失敗時報告端點狀況良好。

另一方面，請勿使用健康情況探查來檢查較低優先順序的服務。 例如，如果電子郵件服務已關閉，應用程式可以切換至第二個提供者或只是稍後再傳送電子郵件。 其優先順序不足以高到要將應用程式容錯移轉。 如需詳細資訊，請參閱[健康情況端點監視控模式][health-endpoint-monitoring-pattern]。
 
### <a name="sql-database"></a>SQL Database
使用[作用中地理複寫][sql-replication] 在不同區域中建立可讀取的次要複本。 最多可以有四個可讀取的次要複本。 若您的主要資料庫失敗，或是需要離線，請容錯移轉至次要資料庫。 您可以為任何彈性資料庫集區中的任何資料庫設定作用中異地複寫。

### <a name="cosmos-db"></a>Cosmos DB
Cosmos DB 支援跨區域異地複寫。 可指定一個區域為可寫入，而其他區域為唯讀複本。

如果發生區域性中斷，您可以選取另一個區域作為寫入區域，達成容錯移轉。 用戶端 SDK 會自動傳送寫入要求到目前的寫入區域，因此您不需要在容錯移轉之後更新用戶端設定。 如需詳細資訊，請參閱[如何使用 Azure Cosmos DB 在全域散發資料][cosmosdb-geo]。

> [!NOTE]
> 所有複本皆隸屬於相同資源群組。
>
>

### <a name="storage"></a>儲存體
若是 Azure 儲存體，請使用[讀取權限異地備援儲存體 (RA-GRS)][ra-grs]。 使用 RA-GRS 儲存體，資料會複寫到次要區域。 您透過不同的端點，對次要區域中的資料有唯讀存取權。 如果發生區域性中斷或災害，Azure 儲存體團隊可能會決定執行地理容錯移轉到次要區域。 此種容錯移轉不需要客戶動作。

若是佇列儲存體，請在次要區域中建立備份佇列。 在容錯移轉期間，應用程式可以使用備份佇列，直到主要區域再次變為可使用為止。 這樣一來，應用程式仍然可以處理新的要求。

## <a name="availability-considerations"></a>可用性考量


### <a name="traffic-manager"></a>流量管理員

如果主要區域變得無法使用，流量管理員就會自動容錯移轉到次要區域。 當流量管理員容錯移轉時，用戶端會有一段時間無法連線到應用程式。 持續時間受到下列因素影響：

* 健康情況探查必須偵測到主要區域已變成無法連線。
* 網域名稱系統 (DNS) 伺服器必須針對 IP 位址更新快取的 DNS 記錄，這取決於 DNS 存留時間 (TTL)。 預設的 TTL 是 300 秒 (5 分鐘)，但是當您建立流量管理員設定檔時，您可以設定此值。

如需詳細資料，請參閱[關於流量管理員監視][tm-monitoring]。

流量管理員也可能是這套系統中的失敗點。 如果此服務失敗，用戶端就無法在當機期間存取您的應用程式。 檢閱[流量管理員服務等級協定 (SLA)][tm-sla]，判斷單獨使用流量管理員是否符合您獲得高可用性的業務需求。 如果沒有，請考慮新增另一個流量管理解決方案作為容錯回復。 如果 Azure 流量管理員服務失敗，請變更您在 DNS 中的正式名稱 (CNAME) 記錄，以指向其他流量管理服務。 此步驟必須手動執行，而且在傳播 DNS 變更之前，將無法使用您的應用程式。

### <a name="sql-database"></a>SQL Database
SQL Database 的復原點目標 (RPO) 和 預估復原時間 (ERT) 記載於[使用 Azure SQL Database 的商務持續性概觀][sql-rpo]。 

### <a name="storage"></a>儲存體
RA-GRS 儲存體提供持久儲存體，但請務必了解中斷期間可能發生的情況：

* 如果儲存體中斷，您會有一段時間不具有寫入資料的權限。 在中斷期間，您仍可讀取次要端點。
* 如果區域性中斷或災害影響了主要位置，且無法復原該處的資料，Azure 儲存體團隊可能會決定執行地理容錯移轉到次要區域。
* 資料複寫到次要區域會以非同步方式執行。 因此，執行地理容錯移轉時，如果無法從主要區域復原資料，則可能會遺失某些資料。
* 暫時性的失敗 (例如網路中斷) 不會觸發儲存體容錯移轉。 將您的應用程式設計為具有經歷暫時性失敗的彈性。 可能的緩和措施：
  
  * 從次要地區讀取。
  * 暫時切換到另一個儲存體帳戶進行新的寫入作業 (例如，將訊息排入佇列)。
  * 從次要區域將資料複製到另一個儲存體帳戶。
  * 提供的功能會減少，直到系統容錯回復。

如需詳細資訊，請參閱[如果 Azure 儲存體發生中斷怎麼辦][storage-outage]。

## <a name="manageability-considerations"></a>管理性考量

### <a name="traffic-manager"></a>流量管理員

如果流量管理員容錯移轉，我們建議執行手動容錯回復，而不是實作自動容錯回復。 或者，您可以建立應用程式會在區域之間來回翻轉的情況。 請確認所有的應用程式子系統狀況良好，然後再進行容錯回復。

請注意，流量管理員預設會自動容錯回復。 若要避免這個問題，請在容錯移轉事件之後，手動降低主要區域的優先順序。 例如，假設主要區域是優先順序 1，而次要為優先順序 2。 在容錯移轉之後，將主要區域設定為優先順序 3，以防止自動容錯回復。 當您準備好切換回來時，將優先順序更新為 1。

下列命令會更新優先順序。

**PowerShell**

```bat
$endpoint = Get-AzureRmTrafficManagerEndpoint -Name <endpoint> -ProfileName <profile> -ResourceGroupName <resource-group> -Type AzureEndpoints
$endpoint.Priority = 3
Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

如需詳細資訊，請參閱 [Azure 流量管理員 Cmdlet][tm-ps]。

**Azure 命令列介面 (CLI)**

```bat
azure network traffic-manager endpoint set --name <endpoint> --profile-name <profile> --resource-group <resource-group> --type AzureEndpoints --priority 3
```    

### <a name="sql-database"></a>SQL Database
如果主要資料庫失敗，手動容錯移轉到次要資料庫。 請參閱[還原 Azure SQL Database 或容錯移轉到次要資料庫][sql-failover]。 次要資料庫會維持唯讀狀態，直到您容錯移轉。


<!-- links -->

[azure-sql-db]: https://azure.microsoft.com/documentation/services/sql-database/
[azure-dns]: /azure/dns/dns-overview
[cosmosdb-geo]: /azure/cosmos-db/distribute-data-globally
[guidance-web-apps-scalability]: ./scalable-web-app.md
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[ra-grs]: /azure/storage/storage-redundancy#read-access-geo-redundant-storage
[regional-pairs]: /azure/best-practices-availability-paired-regions
[resource groups]: /azure/azure-resource-manager/resource-group-overview#resource-groups
[services-by-region]: https://azure.microsoft.com/regions/#services
[sql-failover]: /azure/sql-database/sql-database-disaster-recovery
[sql-replication]: /azure/sql-database/sql-database-geo-replication-overview
[sql-rpo]: /azure/sql-database/sql-database-business-continuity#sql-database-features-that-you-can-use-to-provide-business-continuity
[storage-outage]: /azure/storage/storage-disaster-recovery-guidance
[tm-configure-failover]: /azure/traffic-manager/traffic-manager-configure-failover-routing-method
[tm-monitoring]: /azure/traffic-manager/traffic-manager-monitoring
[tm-ps]: https://msdn.microsoft.com/library/mt125941.aspx
[tm-routing]: /azure/traffic-manager/traffic-manager-routing-methods
[tm-sla]: https://azure.microsoft.com/support/legal/sla/traffic-manager/v1_0/
[traffic-manager]: https://azure.microsoft.com/services/traffic-manager/
[visio-download]: https://archcenter.blob.core.windows.net/cdn/app-service-reference-architectures.vsdx

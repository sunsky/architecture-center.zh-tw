---
title: Azure 服務的復原檢查清單
titleSuffix: Azure Design Review Framework
description: 檢查清單，提供各種 Azure 服務的復原指南。
author: petertaylor9999
ms.date: 11/26/2018
ms.topic: checklist
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.custom: resiliency, checklist
ms.openlocfilehash: fbb7501a663c8b5e326b2b601685419c8e5a0806
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54486908"
---
# <a name="resiliency-checklist-for-specific-azure-services"></a>特定 Azure 服務的復原檢查清單

復原是指系統從失敗中復原並繼續運作的能力，且是[軟體品質要素](../guide/pillars.md)的其中一項。 每一種技術都有它自己的特定失敗模式，您必須在設計和實作應用程式時加以考量。 針對特定 Azure 服務使用此檢查清單來檢閱復原考量。 同時檢閱[一般復原檢查清單](./resiliency.md)。

## <a name="app-service"></a>App Service 方案

**使用標準或進階層。** 這些服務層可支援預備位置和自動化備份。 如需詳細資訊，請參閱 [Azure App Service 方案深入概觀](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview/)

**避免相應增加或相應減少。** 請改為選取在一般負載下符合您效能需求的服務層和執行個體大小，然後[相應放大](/azure/app-service-web/web-sites-scale/)執行個體來處理流量的變更。 相應增加和減少可能會觸發應用程式重新啟動。

**將組態儲存為應用程式設定。** 使用應用程式設定，將組態設定保留為應用程式設定。 在您的 Resource Manager 範本 中或使用 PowerShell 來定義設定，以便您在更可靠的自動化部署 / 更新程序中套用它們。 如需詳細資訊，請參閱[在 Azure App Service 中設定 Web 應用程式](/azure/app-service-web/web-sites-configure/)。

**為生產和測試環境建立不同的 App Service 方案。** 請勿使用生產部署上的位置進行測試。  相同 App Service 方案中的所有應用程式會共用相同的 VM 執行個體。 如果您將生產和測試部署放在相同的方案中，可能會對生產部署造成負面影響。 例如，負載測試可能會使實際作用的生產網站降級。 透過將測試部署放在不同方案內，您可以使其與生產版本隔離。

**分隔 Web 應用程式與 Web API。** 如果您的解決方案具有 Web 前端與 Web API，請考慮將它們分解成個別的 App Service 應用程式。 此設計可讓您更輕鬆地依照工作負載來分解解決方案。 您可以在個別的 App Service 方案中執行 Web 應用程式與 API，以便獨立調整其規模。 如果您一開始不需要這種程度的延展性，可以將應用程式部署到相同的方案中，之後有需要時再將它們移到別的方案。

**避免使用 App Service 備份功能來備份 Azure SQL 資料庫。** 請改用 [SQL Database 自動化備份][sql-backup]。 App Service 備份會將資料庫匯出至 SQL .bacpac 檔案，這會使用 DTU。

**部署到預備位置。** 建立預備的部署位置。 將應用程式更新部署到預備位置，並先確認部署，再將它交換至生產環境。 這可降低生產環境使用不正確更新的可能性。 也可確保所有執行個體在交換至生產環境之前就已準備就緒。 許多應用程式都有重要的暖機和冷啟動時間。 如需詳細資訊，請參閱[針對 Azure App Service 中的 Web 應用程式設定預備環境](/azure/app-service-web/web-sites-staged-publishing/)。

**建立部署位置來保存上一個已知良好 (LKG) 的部署。** 當您將更新部署到生產環境時，請將先前的生產部署移到 LKG 位置。 這可讓您更輕鬆地復原錯誤的部署。 如果您之後發現問題，您可以快速地還原至 LKG 版本。 如需詳細資訊，請參閱[基本 Web 應用程式](../reference-architectures/app-service-web-app/basic-web-app.md)。

**啟用診斷記錄**，包括應用程式記錄和 Web 伺服器記錄。 記錄作業對監視和診斷而言都很重要。 請參閱[在 Azure App Service 中針對 Web 應用程式啟用診斷記錄功能](/azure/app-service-web/web-sites-enable-diagnostic-log/)

**記錄至 Blob 儲存體。** 這可讓您更輕鬆地收集和分析資料。

**為記錄建立個別的儲存體帳戶。** 記錄和應用程式資料不可使用相同儲存體帳戶。 這有助於防止記錄作業降低應用程式效能。

**監視效能。** 使用 [New Relic](https://newrelic.com/) 或 [Application Insights](/azure/application-insights/app-insights-overview/) 等效能監視服務，來監視負載下的應用程式效能和行為。  效能監視可讓您即時深入解析應用程式。 它可讓您診斷問題並執行失敗的根本原因分析。

## <a name="application-gateway"></a>應用程式閘道

**佈建至少兩個執行個體。** 部署具有至少兩個執行個體的應用程式閘道。 單一執行個體為單一失敗點。 使用兩個或多個執行個體，可提供備援和延展性。 為了符合 [SLA](https://azure.microsoft.com/support/legal/sla/application-gateway) 的資格，您必須佈建兩個或更多中型或大型執行個體。

## <a name="cosmos-db"></a>Cosmos DB

**跨區域複寫資料庫。** Cosmos DB 可讓您將任意數目的 Azure 區域與 Cosmos DB 資料庫帳戶產生關聯。 Cosmos DB 資料庫可以有一個寫入區域和多個唯讀區域。 如果寫入區域發生失敗，您可以從另一個複本進行讀取。 用戶端 SDK 會自動處理此作業。 您也可以將寫入區域容錯移轉到另一個區域。 如需詳細資訊，請參閱[如何使用 Azure Cosmos DB 在全域散發資料](/azure/cosmos-db/distribute-data-globally)。

## <a name="event-hubs"></a>事件中樞

**使用檢查點**。  事件取用者應以預先定義的間隔將其目前位置寫入永續性儲存體。 這樣一來，如果取用者遇到錯誤 (例如，取用者毀損或主機失敗)，則新的執行個體可以從最後記錄的位置繼續讀取。 如需詳細資訊，請參閱[事件取用者](/azure/event-hubs/event-hubs-features#event-consumers)。

**處理重複的訊息。** 如果事件取用者失敗，訊息處理會從最後一個記錄檢查點繼續。 在最後一個檢查點之後處理的任何訊息會再次處理。 因此，您的訊息處理邏輯必須是等冪，或應用程式必須能夠刪除重複訊息。

**處理例外狀況。** 事件取用者通常會處理迴圈中的一批訊息。 您應該處理此處理迴圈中的例外狀況，以避免在單一訊息造成例外狀況時遺失整個批次的訊息。

**使用無效信件佇列。** 如果處理訊息導致非暫時性失敗，請將訊息放置到無效信件佇列，以便追蹤狀態。 根據這種案例，您可能會在稍後重試訊息、套用補償交易，或採取其他動作。 請注意，事件中樞沒有任何內建無效信件佇列功能。 您可以使用 Azure 佇列儲存體或服務匯流排來實作無效信件佇列，或使用 Azure Functions 或其他事件處理機制。

**藉由容錯移轉至次要事件中樞命名空間來實作災害復原。** 如需詳細資訊，請參閱 [Azure 事件中樞地理災害復原](/azure/event-hubs/event-hubs-geo-dr)。

## <a name="redis-cache"></a>Redis 快取

**設定異地複寫**。 異地複寫提供一個機制，可供連結兩個高階層 Azure Redis 快取執行個體。 寫入主要快取中的資料會複寫至次要唯讀快取。 如需詳細資訊，請參閱[如何設定 Azure Redis 快取的異地複寫](/azure/redis-cache/cache-how-to-geo-replication)

**設定資料永續性。** Redis 永續性可讓您保存儲存在 Redis 中的資料。 您也可以擷取快照和備份資料，以在硬體失敗時載入。 如需詳細資訊，請參閱 [如何設定進階 Azure Redis 快取的資料永續性](/azure/redis-cache/cache-how-to-premium-persistence)

若您將 Redis 快取當作暫存資料快取而非持續性存放區，這些建議可能不適用。

## <a name="search"></a>Search

**佈建一個以上的複本。** 使用至少兩個複本可提供讀取高可用性，或者使用三個複本可提供讀寫高可用性。

**設定多區域部署的索引子。** 如果您有多區域部署，請考慮索引連續性的選項。

- 如果資料來源已在異地複寫，您通常應將每個區域性 Azure 搜尋服務的每個索引子指向其本機資料來源複本。 不過，該方法不建議用於儲存在 Azure SQL Database 中的大型資料集。 原因是 Azure 搜尋服務無法從次要 SQL Database 複本 (只能從主要複本) 執行增量編製索引。 請改為將所有索引子指向主要複本。 在容錯移轉之後，指著位於新主要複本的 Azure 搜尋服務索引子。

- 如果資料來源並未異地複寫，請指著位於相同資料來源的多個索引子，如此一來，多個區域中的 Azure 搜尋服務就能持續且獨立地從資料來源編製索引。 如需詳細資訊，請參閱 [Azure 搜尋服務效能和最佳化考量][search-optimization]。

## <a name="service-bus"></a>服務匯流排

**將進階層使用於生產工作負載**。 [服務匯流排進階傳訊](/azure/service-bus-messaging/service-bus-premium-messaging)提供專用和保留的處理資源和記憶體容量，以支援可預測的效能和輸送量。 進階傳訊層也可讓您存取僅一開始適用於進階客戶的新功能。 您可以根據預期的工作負載決定傳訊單位數目。

**處理重複的訊息**。 如果發行者在傳送訊息後立即失敗，或遭遇網路或系統問題，則可能因錯誤而無法記錄訊息已傳遞，並可能將相同的訊息傳送到系統兩次。 服務匯流排可藉由啟用重複偵測來處理此問題。 如需詳細資訊，請參閱[重複偵測](/azure/service-bus-messaging/duplicate-detection)。

**處理例外狀況**。 發生使用者錯誤、設定錯誤或其他錯誤時，傳訊 API 會產生例外狀況。 用戶端程式碼 (傳送者和接收者) 應在其程式碼中處理這些例外狀況。 這在批次處理時尤其重要，因為例外狀況處理可用來避免遺失整個批次的訊息。 如需詳細資訊，請參閱[服務匯流排傳訊例外狀況](/azure/service-bus-messaging/service-bus-messaging-exceptions)。

**重試原則**。 服務匯流排可讓您挑選最適合您應用程式的重試原則。 預設原則是最多允許重試 9 次，並等候 30 秒，但可進一步調整。 如需詳細資訊，請參閱[重試原則 – 服務匯流排](/azure/architecture/best-practices/retry-service-specific#service-bus)。

**使用無效信件佇列**。 在多次重試之後，如果無法處理訊息或傳遞給任何接收者，該訊息就會移至無效信件佇列。 實作從無效信件佇列讀取訊息、加以檢查，然後補救問題的程序。 視案例而定，您可能會按現況重試訊息、進行變更並重試，或捨棄該訊息。 如需詳細資訊，請參閱[服務匯流排寄不出的信件佇列的概觀](/azure/service-bus-messaging/service-bus-dead-letter-queues)。

**使用異地災害復原**。 如果整個 Azure 區域或資料中心因為災害而無法使用，地理災害復原可確保資料處理會繼續在不同的區域或資料中心運作。 如需詳細資訊，請參閱 [Azure 服務匯流排地理災害復原](/azure/service-bus-messaging/service-bus-geo-dr)。

## <a name="storage"></a>儲存體

**對於應用程式資料，請使用讀取權限異地備援儲存體 (RA-GRS)。** RA-GRS 儲存體會將資料複寫到次要區域，並提供從次要區域的唯讀存取權。 如果主要區域發生儲存體中斷，應用程式可以從次要區域讀取資料。 如需詳細資訊，請參閱 [Azure 儲存體複寫](/azure/storage/storage-redundancy/)。

**若為 VM 磁碟，請使用受控磁碟。** [受控磁碟][managed-disks]可為可用性設定組中的 VM 提供更高的可靠性，因為磁碟彼此充分隔離，以避免單一失敗點。 此外，受控磁碟不受儲存體帳戶中建立之 VHD 的 IOPS 限制所約束。 如需詳細資訊，請參閱[管理 Azure 中 Windows 虛擬機器的可用性][vm-manage-availability]。

**若為佇列儲存體，請在另一個區域中建立備份佇列。** 對於佇列儲存體，唯讀複本的用途有限，因為您無法將項目排入佇列或從佇列中清除。 請改為在其他區域的儲存體帳戶中建立備份佇列。 如果發生儲存體中斷，應用程式可以使用備份佇列，直到主要區域再次變為可使用為止。 這樣一來，應用程式仍然可以處理新的要求。

## <a name="sql-database"></a>SQL Database

**使用標準或進階層。** 這些服務層提供較長的時間點還原週期 (35 天)。 如需詳細資訊，請參閱 [SQL Database 選項和效能](/azure/sql-database/sql-database-service-tiers/)。

**啟用 SQL Database 稽核。** 稽核可以用來診斷惡意攻擊或人為錯誤。 如需詳細資訊，請參閱 [開始使用 SQL 資料庫稽核](/azure/sql-database/sql-database-auditing-get-started/)。

**使用作用中異地複寫** 使用作用中異地複寫在不同區域中建立可讀取的次要複本。  如果您的主要資料庫失敗，或只需要離線，請執行手動容錯移轉至次要資料庫。  在您容錯移轉前，次要資料庫會維持唯讀狀態。  如需詳細資訊，請參閱 [SQL Database 作用中異地複寫](/azure/sql-database/sql-database-geo-replication-overview/)。

**使用分區化。** 請考慮使用分區化來水平分割資料庫。 分區化可提供容錯隔離。 如需詳細資訊，請參閱[使用 Azure SQL Database 相應放大](/azure/sql-database/sql-database-elastic-scale-introduction/)。

**使用時間點還原從人為錯誤中復原。**  時間點還原會使您的資料庫回到較早的時間點。 如需詳細資訊，請參閱[使用自動化資料庫備份來復原 Azure SQL Database][sql-restore]。

**使用異地還原從服務中斷中復原。** 異地還原可從異地備援備份還原資料庫。  如需詳細資訊，請參閱[使用自動化資料庫備份來復原 Azure SQL Database][sql-restore]。

## <a name="sql-data-warehouse"></a>SQL 資料倉儲

**請勿停用異地備份。** 根據預設，Microsoft Azure SQL 資料倉儲每 24 小時會完整備份您的資料以進行災害復原。 建議您不要關閉這項功能。 如需詳細資訊，請參閱[異地備份](/azure/sql-data-warehouse/backup-and-restore#geo-backups)。

## <a name="sql-server-running-in-a-vm"></a>在虛擬機器中執行的 SQL Server

**複寫資料庫。** 使用 SQL Server Always On 可用性群組來複寫資料庫。 如果有一個 SQL Server 執行個體失敗，可提供高可用性。 如需詳細資訊，請參閱[執行適用於多層式架構的 Windows VM](../reference-architectures/virtual-machines-windows/n-tier.md)

**備份資料庫**。 如果您已經使用 [Azure 備份](/azure/backup/)來備份您的 VM，請考慮使用 [Azure 備份來備份採用 DPM 的 SQL Server 工作負載](/azure/backup/backup-azure-backup-sql/)。 使用此方法，組織有一個備份管理員角色以及 VM 和 SQL Server 的整合復原程序。 否則，使用 [SQL Server 受控備份至 Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx)。

## <a name="traffic-manager"></a>流量管理員

**執行手動容錯回復。** 在流量管理員容錯移轉之後，執行手動容錯回復，而非自動容錯回復。 在容錯回復之前，確認所有應用程式子系統的狀況良好。  或者，您可以建立應用程式會在資料中心之間來回翻轉的情況。 如需詳細資訊，請參閱[在多個區域中執行 VM 以獲得高可用性](../reference-architectures/virtual-machines-windows/multi-region-application.md)。

**建立健康情況探查端點。** 建立可報告應用程式整體健康情況的自訂端點。 如有任何重要的路徑失敗 (不只是前端)，這可讓流量管理員進行容錯移轉。 如有任何重要的相依性處於狀況不良或無法連線，則端點應傳回 HTTP 錯誤碼。 然而，不會回報不重要服務的錯誤。 否則，健康情況探查可能觸發容錯移轉，若不需要，則會建立誤判。 如需詳細資訊，請參閱[流量管理員端點監視和容錯移轉](/azure/traffic-manager/traffic-manager-monitoring/)。

## <a name="virtual-machines"></a>虛擬機器

**避免在單一 VM 上執行生產工作負載。** 單一 VM 部署不適合進行計劃性或非計劃性維護。 請改為將多個 VM 放入一個可用性集合或 [VM 擴展集](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview/)中 (前面是負載平衡器)。

**在佈建 VM 時指定可用性設定組。** 目前沒有任何方法可在佈建 VM 之後，將 VM 新增至可用性設定組。 當您將新的 VM 新增至現有的可用性設定時，務必為此 VM 建立 NIC，然後將此 NIC 新增至負載平衡器上的後端位址集區。 否則，負載平衡器不會將網路流量傳送到該 VM。

**將每個應用程式層放在不同的可用性設定組中。** 在多層式架構應用程式中，請勿將不同層的 VM 放在相同的可用性設定組中。 可用性設定組中的 VM 會置於各個容錯網域 (FD) 與更新網域 (UD) 中。 不過，若要取得 FD 和 UD 的備援優勢，可用性設定組中的每個 VM 必須能夠處理相同的用戶端要求。

**使用 Azure Site Recovery 複寫 VM。** 使用 [Site Recovery][site-recovery] 複寫 Azure VM 時，所有 VM 磁碟會持續以非同步方式複寫至目標區域。 每隔幾分鐘就會建立一次復原點。 據此，您將獲得以分鐘為單位的復原點目標 (RPO)。 您可以依需求執行不限次數的災害復原演練，而不會影響生產應用程式或進行中的複寫。 如需詳細資訊，請參閱[執行 Azure 的災害復原演練][site-recovery-test]。

**根據效能需求選擇正確的 VM 大小。** 將現有的工作負載移至 Azure 時，從最符合您內部部署伺服器的 VM 大小開始。 然後根據 CPU、記憶體和 IOPS 測量您的實際工作負載效能，並視需要調整大小。 這有助於確保應用程式如預期般在雲端環境中運作。 此外，如果您需要多個 NIC，請留意每個大小的 NIC 限制。

**對 VHD 使用受控磁碟。** [受控磁碟][managed-disks]可為可用性設定組中的 VM 提供更高的可靠性，因為磁碟彼此充分隔離，以避免單一失敗點。 此外，受控磁碟不受儲存體帳戶中建立之 VHD 的 IOPS 限制所約束。 如需詳細資訊，請參閱[管理 Azure 中 Windows 虛擬機器的可用性][vm-manage-availability]。

**將應用程式安裝在資料磁碟上，而非作業系統磁碟上。** 否則，您可能會達到磁碟大小限制。

**使用 Azure 備份來備份 VM。** 備份可防止資料意外遺失。 如需詳細資訊，請參閱[使用復原服務保存庫來保護 Azure VM](/azure/backup/backup-azure-vms-first-look-arm/)。

**啟用診斷記錄**，包括基本健康情況計量、基礎結構記錄及[開機診斷][boot-diagnostics]。 如果您的 VM 進入無法開機的狀態，開機診斷能協助您診斷開機失敗。 如需詳細資訊，請參閱 [Azure Diagnostic Logs 概觀][diagnostics-logs]。

**使用 AzureLogCollector 擴充功能。** (僅限 Windows VM。)這項擴充功能可彙總 Azure 平台記錄，並將其上傳至 Azure 儲存體，而不需操作人員從遠端登入 VM。 如需詳細資訊，請參閱 [AzureLogCollector 擴充功能](/azure/virtual-machines/virtual-machines-windows-log-collector-extension/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="virtual-network"></a>虛擬網路

**若要允許或封鎖公用 IP 位址，請將 NSG 新增至子網路。** 封鎖來自惡意使用者的存取，或只允許有權存取應用程式的使用者存取。

**建立自訂健康情況探查。** Load Balancer 健康情況探查可以測試 HTTP 或 TCP。 如果 VM 執行 HTTP 伺服器，則 HTTP 探查是比 TCP 探查更佳的健康狀態指標。 對於 HTTP 探查，使用可報告應用程式整體健康情況 (包括所有重要相依性) 的自訂端點。 如需詳細資訊，請參閱 [Azure Load Balancer 概觀](/azure/load-balancer/load-balancer-overview/)。

**封鎖健康情況探查。** Load Balancer 健康情況探查會從已知的 IP 位址 168.63.129.16 傳送。 請勿在任何防火牆原則或網路安全性群組 (NSG) 規則中封鎖往返此 IP 的流量。 封鎖健康情況探查，可能會導致負載平衡器從循環中移除 VM。

**啟用 Azure Load Balancer 記錄功能。** 記錄會顯示後端有多少個 VM 因為探查回應失敗而不接收網路流量。 如需詳細資訊，請參閱 [Azure Load Balancer 的記錄分析](/azure/load-balancer/load-balancer-monitor-log/)。

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[diagnostics-logs]: /azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs/
[managed-disks]: /azure/storage/storage-managed-disks-overview
[search-optimization]: /azure/search/search-performance-optimization/
[site-recovery]: /azure/site-recovery/
[site-recovery-test]: /azure/site-recovery/site-recovery-test-failover-to-azure
[sql-backup]: /azure/sql-database/sql-database-automated-backups/
[sql-restore]: /azure/sql-database/sql-database-recovery-using-backups/
[vm-manage-availability]: /azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set

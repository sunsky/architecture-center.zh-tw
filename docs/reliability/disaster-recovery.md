---
title: Azure 應用程式的失敗和災害復原
description: 方法中，Azure 的災害復原的概觀
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: f00341b06b8092c798d6260317226190cbace66b
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59646518"
---
# <a name="failure-and-disaster-recovery-for-azure-applications"></a>Azure 應用程式的失敗和災害復原

*嚴重損壞修復*是還原應用程式功能的重大損失的網路喚醒的程序。

您對功能會減少災害發生期間的容忍度是業務決策，不同的應用程式的下一步。 它是可接受的某些應用程式完全無法使用或部分可用以降低的功能或延遲一段時間的處理。 其他應用程式，是無法接受任何功能會減少。

## <a name="disaster-recovery-plan"></a>災害復原計劃

啟動建立復原計劃。 已經過完整測試之後，即視為完成計劃。 包含人員、 程序和還原功能，您已定義為您的客戶服務等級協定 (SLA) 內所需的應用程式。

請考慮下列建議時建立並測試您的災害復原計劃：

- 在您的方案，包括連絡支援人員，以及向上呈報問題的程序。 這項資訊有助於避免延長的停機，因為第一次解決復原程序。
- 評估應用程式失敗的商務影響。
- 選擇關鍵任務應用程式的跨區域復原架構。
- 識別災害復原方案的特定擁有者，包括自動化和測試。
- 文件的程序，尤其是任何手動步驟。
- 最大的程序自動化。
- 定期建立所有的參考和交易式的資料，以及測試備份還原的備份策略。
- 設定適用於 Azure 的服務，供您的應用程式堆疊的警示。
- 訓練作業人員執行計劃。
- 執行常態性災害模擬來驗證和改善計劃。

如果您使用[Azure Site Recovery](/azure/site-recovery/)來複寫虛擬機器 (Vm)，建立完全自動化的復原方案容錯移轉整個應用程式。

## <a name="manual-responses"></a>手動回應

雖然自動化是理想的災害復原的一些策略將需要手動的回應。

### <a name="alerts"></a>警示

監視您的應用程式，注意可能需要主動介入的跡象。 例如，如果 Azure SQL Database 或 Azure Cosmos DB 一致地節流您的應用程式，您可能需要增加您的資料庫容量或最佳化查詢。 即使應用程式可能會以透明的方式處理節流錯誤，您的遙測仍應發出警示，讓您可以追蹤。

如需服務限制和配額臨界值，建議您設定 Azure 資源計量和診斷記錄的警示。 可能的話，請設定警示的計量，也就是較低的延遲，比診斷記錄檔。

透過 [資源健康狀態](/azure/service-health/resource-health-checks-resource-types)，Azure 會提供一些內建的健康狀態檢查，以協助您診斷 Azure 服務節流問題。

### <a name="failover"></a>容錯移轉

設定每個 Azure 應用程式和其 Azure 服務的災害復原策略。 可接受的部署策略，以支援災害復原可能因每個應用程式的所有元件所都需的 Sla。  

Azure 提供不同的功能，以便進行手動容錯移轉，例如許多 Azure 服務內 [redis 快取的異地複寫](/azure/azure-cache-for-redis/cache-how-to-geo-replication)，或適用於自動化容錯移轉，例如 [SQL 自動容錯移轉群組](/azure/sql-database/sql-database-auto-failover-group)。 例如︰

- 主要使用的虛擬機器的應用程式，您可以使用 Azure Site Recovery 針對 web 和邏輯層。 如需詳細資訊，請參閱 < [Azure 至 Azure 災害復原架構](/azure/site-recovery/azure-to-azure-architecture)。 在 Vm 上的 SQL 伺服器，請使用[SQL Server Always On 可用性群組](/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr)。
- 使用 App Service 和 Azure SQL Database 的應用程式，您可以使用較小的層 App Service 方案，在次要區域中，容錯移轉發生時的自動調整設定。 您可以使用 容錯移轉群組來放置資料庫層。

在任一案例中， [Azure 流量管理員](/azure/traffic-manager/traffic-manager-overview) 跨區域提供的自動容錯移轉的設定檔。 [負載平衡器](/azure/load-balancer/load-balancer-overview)或是 [應用程式閘道](/azure/application-gateway/overview) 應該設定次要區域中可支援在容錯移轉時更快的可用性。

### <a name="operational-readiness-testing"></a>作業整備測試

針對至次要區域的容錯移轉及容錯回復到主要區域，請執行作業整備測試。 許多 Azure 服務支援手動容錯移轉或測試災害復原演練的容錯移轉。 或者，您可以模擬中斷正在關閉或移除 Azure 服務。

## <a name="application-failure"></a>應用程式失敗

應用程式失敗是可修復或無法復原。 您可以緩和可復原的錯誤，但無法復原的錯誤將會關閉應用程式。

- 某些故障問題可以解決無障礙地自動處理錯誤及採取替代動作。 比方說，Traffic Manager 會自動處理從虛擬機器主機中基礎硬體或作業系統軟體所造成的失敗。
- 但發生一些錯誤，應用程式可以繼續處理使用者要求，以降低的功能。
- 更嚴重的服務中斷可能會使應用程式無法使用。

設計良好的系統將服務層級的責任&mdash;在設計階段和執行階段。 這種區隔可防止關閉整個應用程式相依的服務中斷。 例如，請考慮下列模組的 web 商務應用程式：

![將連結加入到圖案](./_images/disaster-recovery.png)

如果裝載訂單的資料庫關閉時，「 訂單處理服務無法處理銷售交易。 根據架構中，它可能無法提交訂單 」 和 「 訂單處理服務繼續順利執行。 不過，如果產品資料儲存在不同的位置中，產品目錄就是仍然可用，即使應用程式的其他部分可能會無法使用。

它由您決定如何應用程式將會通知使用者發生任何暫時性問題，並建置適當的通知系統。 在上述範例中，來檢視產品，並將它們新增至購物車，可能會允許應用程式。 不過，當客戶嘗試進行購買，應用程式應該通知它們，訂購功能暫時無法使用。 雖然不理想的客戶，這種方法可防止全應用程式的服務中斷。

## <a name="data-corruption-and-restoration"></a>資料損毀和還原

如果資料存放區失敗，可能有資料不一致時它再次可供使用，尤其是在已複寫資料。 了解復原時間目標 (RTO) 和復原點目標 (RPO) 的複寫的資料存放區可協助您預測的資料遺失量。

若要了解是否要以手動方式或 Microsoft 啟動跨區域容錯移轉，請檢閱 Azure 服務 Sla。 對於服務沒有 Sla 的跨區域容錯移轉，Microsoft 通常會決定何時要容錯移轉，但通常優先復原主要區域中的資料。 如果主要區域中的資料會被視為無法復原的 Microsoft 會容錯移轉到次要區域。

### <a name="restoring-data-from-backups"></a>從備份還原資料

備份可保護您免於因意外刪除或資料損毀而遺失應用程式的元件。 它們會保留從較早的時間，將它還原，您可以使用元件的功能版本。

災害復原策略不會取代備份，但應用程式資料的定期備份支援一些災害復原案例。 您的備份儲存體選擇應根據災害復原策略。

執行備份程序的頻率會決定您的 RPO。 比方說，如果您每小時執行備份，災害發生在備份之前的兩分鐘，您將會遺失的 58 分鐘資料。 您的災害復原計劃應該包含您如何解決遺失的資料。

通常會在另一個存放區中的參考資料的一個資料存放區中的資料。 例如，假設 SQL Database 具有資料行連結至 Azure 儲存體中的 blob。 如果備份未同時進行，則資料庫可能有失敗前未備份 blob 的指標。 在應用程式或災害復原計畫必須實作程序來處理復原後的這項不一致。

> [!NOTE]
> 在某些情況下，例如使用備份的 vm [Azure 備份](/azure/backup/backup-azure-vms-first-look-arm)，您可以只從相同的區域中的備份進行還原。 其他 Azure 服務，例如[redis Azure 快取](/azure/azure-cache-for-redis/cache-how-to-geo-replication)，提供異地複寫的備份，您可以使用跨區域還原服務。

### <a name="azure-storage-and-azure-sql-database"></a>Azure 儲存體和 Azure SQL Database

Azure 會自動儲存三次內的不同容錯網域的 Azure 儲存體和 SQL Database 的資料，相同的區域中。 如果您使用異地複寫，資料會在不同的區域中額外儲存三次。 不過，如果資料已損毀或刪除主要複本中 （例如，因為使用者錯誤），所做的變更會複寫到其他複本。

您有兩個選項可管理潛在資料損毀或刪除：

- **建立自訂的備份策略。** 您可以將備份儲存在 Azure 或內部部署，視您的商務需求和控管規定而定。
- **使用還原時間點選項**來復原 SQL 資料庫。

#### <a name="azure-storage-recovery"></a>Azure 儲存體復原

您可以開發自訂備份程序的 Azure 儲存體，或使用某個協力廠商備份工具。

Azure 儲存體提供資料恢復功能，透過自動化複本，但它不會防止應用程式程式碼或使用者損毀資料。 維護資料精確度之後應用程式或使用者錯誤需要更進階的技術，例如將資料複製到次要儲存體位置，使用稽核記錄。 您有幾種選項：

- [**區塊 blob。**](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) 建立每個區塊 Blob 的時間點快照集。 每個快照集，您只需支付儲存在 blob 中的差異，自先前的快照集狀態所需的儲存體。 因此我們建議您複製到另一個 blob 或甚至是另一個儲存體帳戶，是取決於原始的 blob，快照集。 這個方法可確保備份資料會受到保護，防止被意外刪除。 使用[AzCopy](/azure/storage/common/storage-use-azcopy)或是[Azure PowerShell](/azure/storage/common/storage-powershell-guide-full)將 blob 複製到另一個儲存體帳戶。

    如需詳細資訊，請參閱 [建立 Blob 的快照集](/rest/api/storageservices/creating-a-snapshot-of-a-blob)。

- [**Azure 檔案。**](/azure/storage/files/storage-files-introduction) 使用[共用快照集](/azure/storage/files/storage-snapshots-files)，AzCopy 或 PowerShell，將您的檔案複製到另一個儲存體帳戶。
- [**Azure 資料表儲存體中。**](/azure/storage/tables/table-storage-overview) 使用 AzCopy 可將資料表資料匯出到位於其他區域的其他儲存體帳戶。

#### <a name="sql-database-recovery"></a>SQL Database 復原

若要防止資料遺失，SQL Database 自動保護您的企業會執行完整資料庫備份的組合每週、 每小時、 差異資料庫備份和交易記錄備份每隔 5 到 10 分鐘的時間。 對於 Basic、 Standard 和 Premium SQL Database 層中，使用時間點還原到資料庫加入至較早的時間。 如需詳細資訊，請參閱下列文章：

- [使用自動資料庫備份復原 Azure SQL Database](/azure/sql-database/sql-database-recovery-using-backups)
- [使用 Azure SQL Database 的商務持續性概觀](/azure/sql-database/sql-database-business-continuity)

另一個選項是用於 SQL Database，其會自動將資料庫變更複寫到相同或不同 Azure 區域中的次要資料庫的作用中異地複寫。 如需詳細資訊，請參閱 <<c0> [ 建立和使用主動式異地複寫](/azure/sql-database/sql-database-active-geo-replication)。

您也可以使用較為手動的方式進行備份和還原：

- 使用**資料庫複製**命令來建立資料庫的備份副本，具有交易一致性。
- 使用 Azure SQL Database 匯入/匯出服務，可支援將資料庫匯出到 BACPAC 檔案 （包含您的資料庫結構描述和相關聯的資料壓縮檔案） 儲存在 Azure Blob 儲存體中。 若要防止全區域服務中斷，將 BACPAC 檔案複製到替代區域。

### <a name="sql-server-on-vms"></a>虛擬機器上的 SQL Server

在 Vm 上執行的 SQL Server，您有兩個選項： 傳統備份和記錄傳送。

- 使用傳統備份時，您可以還原至特定點時間，但復原程序緩慢。 還原傳統備份需要初始完整備份開始，然後套用所有增量備份。
- 您可以設定記錄傳送工作階段的記錄備份還原延遲。 這會提供一個視窗，以從主要複本上的錯誤復原。

### <a name="azure-database-for-mysql-and-azure-database-for-postgresql"></a>Azure Database for MySQL 和適用於 PostgreSQL 的 Azure 資料庫

在適用於 MySQL 的 Azure 資料庫和適用於 PostgreSQL 的 Azure 資料庫中，資料庫服務會自動將備份每隔五分鐘。 您可以使用這些自動化的備份來還原伺服器和資料庫從較早時間點的時間到新的伺服器。 如需詳細資訊，請參閱

- [如何使用 Azure 入口網站，在適用於 MySQL 的 Azure 資料庫中備份和還原伺服器](/azure/mysql/howto-restore-server-portal)
- [如何備份及還原使用 Azure 入口網站適用於 PostgreSQL 的 Azure 資料庫中的伺服器](/azure/postgresql/howto-restore-server-portal)

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Cosmos DB 會自動將固定間隔的備份。 備份會儲存在另一個儲存體服務，而且會全域複寫到防範區域性災害提供防護。 如果您不小心刪除資料庫或集合，您可以提出支援票證或連絡 Azure 支援，要求從最新的自動備份還原資料。 如需詳細資訊，請參閱 <<c0> [ 線上備份及 Azure Cosmos DB 中的隨選還原](/azure/cosmos-db/online-backup-and-restore)。

### <a name="azure-virtual-machines"></a>Azure 虛擬機器

若要防止應用程式錯誤或意外刪除的 Azure 虛擬機器，使用[Azure 備份](/azure/backup/)。 建立的備份是一致的跨多個 VM 磁碟。 此外，Azure 備份保存庫可以跨區域，以支援從地區耗損復原複寫。

## <a name="network-outage"></a>網路中斷

Azure 網路的組件無法存取時，您可能會無法存取您的應用程式或資料。 在此情況下，建議您設計災害復原策略，來執行大部分的應用程式以降低的功能。

如果降低功能不是一個選項，則剩餘的選項會為應用程式停機或容錯移轉至替代區域。

在功能會減少的案例：

- 如果您的應用程式無法存取其資料，因為 Azure 網路中斷，您可以使用快取的資料在本機執行具有降低的應用程式功能。
- 您可以將資料儲存在替代位置，直到恢復連線為止。

## <a name="dependent-service-failure"></a>相依服務失敗

針對每個相依的服務，您應該了解服務中斷，以及應用程式會回應的方式的影響。 許多服務都包含支援復原和可用性，因此獨立評估每個服務時可能會提高嚴重損壞修復計劃的功能。 例如，支援 Azure 事件中樞[容錯移轉](/azure/event-hubs/event-hubs-geo-dr#setup-and-failover-flow)次要命名空間。

## <a name="region-wide-service-disruptions"></a>全區域服務中斷

許多失敗是可管理的相同 Azure 區域內。 不過，不太可能全區域服務中斷事件時，無法使用本機備援複本的資料。 如果您已啟用異地複寫，但是有額外三份您的 blob 和資料表在不同的區域。 如果 Microsoft 宣告此區域遺失，Azure 會重新對應到次要區域的所有 DNS 項目。

> [!NOTE]
> 此程序只會發生全區域服務中斷，並不在您的控制項內。 請考慮使用 [Azure Site Recovery](/azure/site-recovery/) 來達成更好的 RPO 和 RTO。 使用 Site Recovery，您決定什麼是可接受的中斷，以及何時要容錯移轉至複寫的 Vm。

您的回應，全區域服務中斷的情況取決於您的部署和災害復原計畫。

- 成本控制策略的非關鍵性的應用程式，不需要保證的復原時間，可能會合理重新部署到不同的區域。
- 裝載於另一個區域，與已部署的角色，但不將流量分散到區域應用程式 (*主動/被動部署*)，切換到次要的託管服務，在替代區域。
- 在另一個區域中有完整規模的次要部署的應用程式 (*主動/主動部署*)，將流量路由傳送到該區域。

若要深入了解從全區域服務中斷復原，請參閱[從全區域服務中斷復原](../resiliency/recovery-loss-azure-region.md)。

### <a name="vm-recovery"></a>VM 復原

針對關鍵的應用程式，規劃復原 Vm 發生全區域服務中斷的情況。

- 使用 Azure 備份或另一種備份方法來建立跨區域備份應用程式一致的。 （在建立時必須設定的備份保存庫的複寫）。
- 使用 Site Recovery 來複寫跨區域，只要按一下應用程式容錯移轉和容錯移轉測試。
- 您可以使用流量管理員自動使用者流量容錯移轉到另一個區域。

若要進一步了解，請參閱[從全區域服務中斷的情況中，虛擬機器復原](../resiliency/recovery-loss-azure-region.md#virtual-machines)。

### <a name="storage-recovery"></a>儲存體復原

若要保護您的儲存體發生全區域服務中斷的情況：

- 使用異地備援儲存體。
- 知道您的儲存體的異地複寫的位置。 這會影響您在其中部署您需要有您的儲存體地緣性的資料的其他執行個體。
- 在容錯移轉之後會檢查資料一致性，並視需要，還原的備份。

若要進一步了解，請參閱[設計高可用性的應用程式使用 RA-GRS](/azure/storage/common/storage-designing-ha-apps-with-ragrs)。

### <a name="sql-database-and-sql-server"></a>SQL Database 和 SQL Server

Azure SQL Database 提供兩種復原：

- 若要將資料庫從另一個區域中的備份副本還原使用異地還原。 如需詳細資訊，請參閱 <<c0> [ 復原 Azure SQL database 使用自動的資料庫備份](/azure/sql-database/sql-database-recovery-using-backups)。
- 使用作用中異地複寫容錯移轉至次要資料庫。 如需詳細資訊，請參閱 <<c0> [ 建立和使用主動式異地複寫](/azure/sql-database/sql-database-active-geo-replication)。

在 Vm 上執行的 SQL Server，請參閱 <<c0> [ 在 「 Azure 虛擬機器 」 中的 SQL Server 的高可用性和災害復原](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr/)。

## <a name="service-specific-guidance"></a>服務特有的指引

下列文章描述特定 Azure 服務的災害的復原：

| 服務                       | 文章                                                                                                                                                                                       |
|-------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 適用於 MySQL 的 Azure 資料庫      | [使用「適用於 MySQL 的 Azure 資料庫」的商務持續性概觀](/azure/mysql/concepts-business-continuity)                                                  |
| 適用於 PostgreSQL 的 Azure 資料庫 | [使用「適用於 PostgreSQL 的 Azure 資料庫」的商務持續性概觀](/azure/postgresql/concepts-business-continuity)                                        |
| Azure 雲端服務          | [發生影響 Azure 雲端服務的 Azure 服務中斷事件時該怎麼辦](/azure/cloud-services/cloud-services-disaster-recovery-guidance) |
| Cosmos DB                     | [使用 Azure Cosmos DB 的高可用性](/azure/cosmos-db/high-availability)                                                                                |
| Azure 金鑰保存庫               | [Azure Key Vault 的可用性與備援](/azure/key-vault/key-vault-disaster-recovery-guidance)                                                        |
| Azure 儲存體                 | [Azure 儲存體中的災害復原和儲存體帳戶容錯移轉 (預覽)](/azure/storage/common/storage-disaster-recovery-guidance)                       |
| SQL Database                  | [還原 Azure SQL Database 或容錯移轉到次要區域](/azure/sql-database/sql-database-disaster-recovery)                                       |
| 虛擬機器              | [要執行 Azure 發生服務中斷的項目會影響 Azure 雲端](/azure/cloud-services/cloud-services-disaster-recovery-guidance)               |
| Azure 虛擬網路         | [虛擬網路 – 商務持續性](/azure/virtual-network/virtual-network-disaster-recovery-guidance)                                                  |

## <a name="next-steps"></a>後續步驟

- [從資料損毀或意外刪除復原](../resiliency/recovery-data-corruption.md)
- [從區域全面性服務中斷復原](../resiliency/recovery-loss-azure-region.md)
---
title: Azure 上 Linux 虛擬機器的 SAP S/4HANA
titleSuffix: Azure Reference Architectures
description: 在 Linux 環境中具有高可用性的 Azure 上執行 SAP S/4HANA 的經過證實做法。
author: lbrader
ms.date: 05/11/2018
ms.custom: seodec18
ms.openlocfilehash: 9eb73ddaf5b1cb815f037f46c7e187f61d126876
ms.sourcegitcommit: bb7fcffbb41e2c26a26f8781df32825eb60df70c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2018
ms.locfileid: "53644167"
---
# <a name="sap-s4hana-for-linux-virtual-machines-on-azure"></a>Azure 上 Linux 虛擬機器的 SAP S/4HANA

此參考架構會顯示一組經過證實的做法，能在 Azure 上支援災害復原的高可用性環境中執行 S/4HANA。 這個架構是以特定虛擬機器 (VM) 大小進行部署，大小可以變更以符合您的組織需求。

![Azure 上 Linux 虛擬機器的 SAP S/4HANA 參考架構](./images/sap-s4hana.png)

下載這個架構的 [Visio 檔案][visio-download]。

> [!NOTE]
> 部署此參考架構需要 SAP 產品的適當授權和其他非 Microsoft 技術。

## <a name="architecture"></a>架構

此參考架構描述企業等級、生產環境層級系統。 若要符合您的業務需求，此組態可縮減為單一虛擬機器。 但是需要下列元件：

**虛擬網路**。 [Azure 虛擬網路](/azure/virtual-network/virtual-networks-overview)服務會安全地讓 Azure 資源彼此連線。 在此架構中，虛擬網路是透過在[中樞輪輻拓撲](../hybrid-networking/hub-spoke.md)中樞中所部署的閘道，連線至內部部署環境。 輪輻是虛擬網路，用於 SAP 應用程式。

**子網路**。 虛擬網路會針對以下每個層級細分為個別[子網路](/azure/virtual-network/virtual-network-manage-subnet)：閘道、應用程式、資料庫和共用服務。

**虛擬機器**。 此架構會針對應用程式層和資料庫層使用執行 Linux 的虛擬機器，群組如下：

- **應用程式層**。 包含 Fiori 前端伺服器集區、SAP Web Dispatcher 集區、應用程式伺服器集區以及 SAP 中央服務叢集。 如需 Azure Linux 虛擬機器上中央服務的高可用性，需要高可用性網路檔案系統 (NFS) 服務。
- **NFS 叢集**。 此架構會使用在 Linux 叢集上執行的 [NFS](/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) 伺服器，以儲存在 SAP 系統之間共用的資料。 這個集中式叢集可以跨多個 SAP 系統共用。 如需 NFS 服務的高可用性，會使用適用於所選取 Linux 發佈的適當高可用性擴充。
- **SAP HANA**。 資料庫層會在叢集中使用兩個以上的 Linux 虛擬機器，以達到高可用性。 HANA 系統複寫 (HSR) 會用來複寫主要和次要 HANA 系統之間的內容。 Linux 叢集會用來偵測系統失敗，並協助自動容錯移轉。 以儲存體為基礎或以雲端為基礎的隔離機制可以用來確保失敗的系統是否已隔離或關機，以避免叢集核心分裂情況。
- **Jumpbox**。 也稱為防禦主機。 這是網路上系統管理員用來連線到其他虛擬機器的安全虛擬機器。 它可以執行 Windows 或 Linux。 當使用 HANA Cockpit 或 HANA Studio 管理工具時，使用 Windows jumpbox 以方便網頁瀏覽。

**負載平衡器**。 內建 SAP 負載平衡器和 [Azure Load Balancer](/azure/load-balancer/load-balancer-overview) 兩者都可用來達到高可用性。 會使用 Azure Load Balancer 執行個體，將流量分配到應用程式層子網路中的虛擬機器。

**可用性集合**。 所有集區和叢集 (Web Dispatcher、SAP 應用程式伺服器、中央服務、NFS 及 HANA) 的虛擬機器會分組到個別[可用性設定組](/azure/virtual-machines/windows/tutorial-availability-sets)，每個角色都會佈建至少兩部虛擬機器。 這讓虛擬機器能夠符合適用於較高[服務等級協定](https://azure.microsoft.com/support/legal/sla/virtual-machines) (SLA) 的資格。

**NIC**。 [網路介面卡](/azure/virtual-network/virtual-network-network-interface) (NIC) 會啟用虛擬網路上虛擬機器的所有通訊。

**網路安全性群組**。 若要限制虛擬網路中的傳入、傳出及內部子網路流量，請使用[網路安全性群組](/azure/virtual-network/virtual-networks-nsg) (NSG)。

**閘道**。 閘道會將您的內部部署網路擴充至 Azure 虛擬網路。 [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) 是建議的 Azure 服務，用來建立不會經過公用網際網路的私人連線，但是也可以使用[站對站](/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)連線。

**Azure 儲存體**。 若要提供虛擬機器虛擬硬碟 (VHD) 的持續性儲存體，則需要 [Azure 儲存體](/azure/storage/)。

## <a name="recommendations"></a>建議

此架構描述小型生產環境層級企業部署。 您的部署會根據您的業務需求而有所不同。 請使用以下建議作為起點。

### <a name="virtual-machines"></a>虛擬機器

在應用程式伺服器集區和叢集中，根據您的需求來調整虛擬機器數目。 [Azure 虛擬機器規劃和實作指南](/azure/virtual-machines/workloads/sap/planning-guide)包含關於在虛擬機器上執行 SAP NetWeaver 的詳細資訊，但是該資訊也適用於 SAP S/4HANA。

如需針對 Azure 虛擬機器類型和輸送量計量 (SAPS) 的 SAP 支援相關詳細資訊，請參閱 [SAP 附註 1928533](https://launchpad.support.sap.com/#/notes/1928533)。

### <a name="sap-web-dispatcher-pool"></a>SAP Web Dispatcher 集區

Web Dispatcher 元件是用來作為 SAP 應用程式伺服器之間 SAP 流量的負載平衡器。 若要達到 Web Dispatcher 元件的高可用性，使用 Azure Load Balancer 以針對平衡器後端集區中可用 Web Dispatcher 之間的 HTTP(S) 流量分配，在循環配置資源組態中實作平行 Web Dispatcher 設定。

### <a name="fiori-front-end-server"></a>Fiori 前端伺服器

Fiori 前端伺服器使用 [NetWeaver 閘道](https://help.sap.com/doc/saphelp_gateway20sp12/2.0/en-US/76/08828d832e4aa78748e9f82204a864/content.htm?no_cache=true)。 針對小型部署，它可以在 Fiori 伺服器上載入。 針對大型部署，另外有用於 NetWeaver 閘道的個別伺服器可以部署在 Fiori 前端伺服器集區前面。

### <a name="application-servers-pool"></a>應用程式伺服器集區

若要管理 ABAP 應用程式伺服器的登入群組，請使用 SMLG 交易。 它會在中央服務的訊息伺服器內使用負載平衡函式，以針對 SAPGUI 和 RFC 流量，分配 SAP 應用程式伺服器集區之間的工作負載。 與高可用性中央服務的應用程式伺服器連線是透過叢集虛擬網路名稱。 這樣可避免必須在本機容錯移轉之後變更中央服務連線的應用程式伺服器設定檔。

### <a name="sap-central-services-cluster"></a>SAP 中央服務叢集

不需要高可用性時，可以將中央服務部署至單一虛擬機器。 不過，單一虛擬機器會變成 SAP 環境的潛在單一失敗點 (SPOF)。 針對高可用性中央服務部署，會使用高可用性 NFS 叢集和高可用性中央服務叢集。

### <a name="nfs-cluster"></a>NFS 叢集

DRBD (分散複寫區塊裝置) 會用於 NFS 叢集節點之間的複寫。

### <a name="availability-sets"></a>可用性設定組

可用性設定組會將伺服器分配到不同實體基礎結構並且更新群組，以改善服務可用性。 將執行相同角色的虛擬機器放到可用性設定組，以協助防範 Azure 基礎結構維護所造成的停機時間，並符合 [SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines)。 建議您針對每個可用性設定組使用兩部以上的虛擬機器。

集合中的所有虛擬機器必須執行相同的角色。 請勿在相同的可用性設定組中混用不同角色的伺服器。 例如，不要將 ASCS 節點放置在與應用程式伺服器相同的可用性設定組。

### <a name="nics"></a>NIC

傳統內部部署 SAP 環境會為每部機器實作多個網路介面卡 (NIC)，以隔離系統管理流量與商務流量。 在 Azure 上，虛擬網路是軟體定義網路，它會透過相同的網路網狀架構傳送所有流量。 因此，不需要使用多個 NIC。 不過，如果您的組織需要隔離流量，您可以對每部虛擬機器部署多個 NIC、將每個 NIC 連線到不同的子網路，然後使用 NSG 以強制執行不同的存取控制原則。

### <a name="subnets-and-nsgs"></a>子網路和 NSG

此架構會將虛擬網路位址空間細分為子網路。 每個子網路可以與定義子網路存取原則的 NSG 相關聯。 將應用程式伺服器放在不同的子網路上，您就可以藉由管理子網路安全性原則 (而非個別伺服器)，以便更輕鬆地保護它們。

當 NSG 與子網路產生關聯時，它會套用至子網路內的所有伺服器。 如需使用 NSG 對子網路中伺服器進行更細微控制的詳細資訊，請參閱[使用網路安全性群組篩選網路流量](https://azure.microsoft.com/blog/multiple-vm-nics-and-network-virtual-appliances-in-azure/)。

另請參閱[規劃與設計 VPN 閘道](/azure/vpn-gateway/vpn-gateway-plan-design)。

### <a name="load-balancers"></a>負載平衡器

[SAP Web Dispatcher](https://help.sap.com/doc/saphelp_nw73ehp1/7.31.19/en-US/48/8fe37933114e6fe10000000a421937/frameset.htm) 會處理到 SAP 應用程式伺服器集區之 HTTP(S) 流量 (包含 Fiori 樣式應用程式) 的負載平衡。

針對透過 DIAG 或遠端函式呼叫 (RFC)，從 SAP GUI 用戶端連線 SAP 伺服器的流量，中央服務訊息伺服器會透過 SAP 應用程式伺服器[登入群組](https://wiki.scn.sap.com/wiki/display/SI/ABAP+Logon+Group+based+Load+Balancing)進行負載平衡，因此不需要額外負載平衡器。

### <a name="azure-storage"></a>Azure 儲存體

我們建議針對資料庫伺服器虛擬機器使用 Azure 進階儲存體。 進階儲存體提供一致的讀取/寫入延遲。 如需針對單一執行個體虛擬機器的作業系統磁碟和資料磁碟使用進階儲存體的詳細資訊，請參閱[虛擬機器的 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。

針對所有生產環境 SAP 系統，我們建議使用進階 [Azure 受控磁碟](/azure/storage/storage-managed-disks-overview)。 受控磁碟是用來管理磁碟的 VHD 檔案，可增加可靠性。 它們也會確保可用性設定組內的虛擬機器磁碟是各自獨立的，以避免發生單一失敗點。

針對 SAP 應用程式伺服器 (包括中央服務虛擬機器)，您可以使用 Azure 標準儲存體來降低成本，因為應用程式的執行會發生在記憶體中，並只會使用磁碟來記錄。 不過，目前標準儲存體只針對非受控儲存體經過認證。 因為應用程式伺服器未裝載任何資料，您也可以使用較小的 P4 和 P6 進階儲存體磁碟以協助降低成本。

針對備份資料存放區，我們建議您使用 Azure [非經常性存取層和/或封存存取層儲存體](/azure/storage/storage-blob-storage-tiers)。 這些儲存體層是符合成本效益的方式，用來儲存不常存取的長時間執行資料。

## <a name="performance-considerations"></a>效能考量

SAP 應用程式伺服器會執行與資料庫伺服器的持續通訊。 針對 HANA 資料庫虛擬機器，請考量啟用[寫入加速器](/azure/virtual-machines/linux/how-to-enable-write-accelerator)以改善記錄寫入延遲。 若要最佳化伺服器間的通訊，請使用[加速網路](https://azure.microsoft.com/blog/linux-and-windows-networking-performance-enhancements-accelerated-networking/)。 請注意，這些加速器僅適用於特定虛擬機器系列。

若要達到高 IOPS 和磁碟頻寬輸送量，可將儲存體磁碟區[效能最佳化](/azure/virtual-machines/linux/premium-storage-performance)的常見做法套用於 Azure 儲存體配置。 例如，將多個磁碟合併在一起來建立等量磁碟區，能夠改善 IO 效能。 在不常變更的儲存體內容上啟用讀取快取，能夠增強資料擷取的速度。 如需有關效能需求的詳細資訊，請參閱 [SAP 附註 1943937 - 硬體設定檢查工具](https://launchpad.support.sap.com/#/notes/1943937) (需要 SAP Service Marketplace 帳戶才能進行存取)。

## <a name="scalability-considerations"></a>延展性考量

在 SAP 應用程式層中，Azure 會提供各種不同的虛擬機器大小可進行相應增加或相應放大。如需完整清單，請參閱 [SAP 附註 1928533](https://launchpad.support.sap.com/#/notes/1928533) - Azure 上的 SAP 應用程式︰支援的產品和 Azure 虛擬機器類型 (需要 SAP Service Marketplace 帳戶才能進行存取)。 隨著我們持續認證更多虛擬機器類型，您可以使用相同的雲端部署相應增加或相應減少。

在資料庫層，此基礎架構會在虛擬機器上執行 HANA。 如果您的工作負載超過虛擬機器大小上限，Microsoft 也會提供適用於 SAP HANA 的 [Azure 大型執行個體](/azure/virtual-machines/workloads/sap/hana-overview-architecture)。 這些實體伺服器共同位於 Microsoft Azure 經過認證的資料中心，在此文件撰寫當下，為單一執行個體提供最多 20 TB 的記憶體容量。 多重節點組態也可具有多達 60 TB 的記憶體總容量。

## <a name="availability-considerations"></a>可用性考量

資源備援是高可用性基礎結構解決方案中的一般主題。 針對具有寬鬆 SLA 的企業，單一執行個體 Azure 虛擬機器會提供執行時間 SLA。 如需詳細資訊，請參閱 [Azure 服務等級協定](https://azure.microsoft.com/support/legal/sla/)。

在 SAP 應用程式的這個分散式安裝中，會複寫基底安裝來達到高可用性。 針對每一層的架構，高可用性設計會有所不同。

### <a name="application-tier"></a>應用程式層

- Web Dispatcher。 高可用性是以備援 Web Dispatcher 執行個體來達成。 請參閱 SAP 文件中的 [SAP Web Dispatcher](https://help.sap.com/doc/saphelp_nw70ehp2/7.02.16/en-us/48/8fe37933114e6fe10000000a421937/frameset.htm)。
- Fiori 伺服器。 藉由在伺服器集區內負載平衡流量來達到高可用性。
- 中央服務。 如需 Azure Linux 虛擬機器上中央服務的高可用性，會使用適用於所選取 Linux 發佈的適當高可用性擴充，以及高可用性 NFS 叢集會裝載 DRBD 儲存體。
- 應用程式伺服器。 藉由應用程式伺服器集區內的負載平衡流量來達到高可用性。

### <a name="database-tier"></a>資料庫層

此參考基本架構描述由兩部 Azure 虛擬機器所組成的高可用性 SAP HANA 資料庫系統。 資料庫層的原生系統複寫功能提供複寫節點之間手動或自動容錯移轉：

- 針對手動容錯移轉，部署多個 HANA 執行個體，並使用 HANA 系統複寫 (HSR)。
- 針對自動容錯移轉，同時使用適用於您的 Linux 散佈的 HSR 和 高可用性擴充 (HAE)。 Linux HAE 為 HANA 資源提供叢集服務，偵測失敗事件並且協調錯誤服務前往狀況良好節點的容錯移轉。

請參閱[於 Microsoft Azure 上執行的 SAP 認證和設定](/azure/virtual-machines/workloads/sap/sap-certifications)。

### <a name="disaster-recovery-considerations"></a>災害復原考量

每一層會使用不同的策略來提供災害復原 (DR) 保護。

- **應用程式伺服器層**。 SAP 應用程式伺服器不包含商務資料。 在 Azure 上，簡易 DR 策略是在次要區域建立 SAP 應用程式伺服器，然後將它們關機。 在主要應用程式伺服器上進行任何組態變更或核心更新時，必須將相同的變更套用到次要區域中的虛擬機器。 例如，將 SAP 核心可執行檔複製到 DR 虛擬機器。 如需將應用程式伺服器自動複寫到次要區域中，[Azure Site Recovery](/azure/site-recovery/site-recovery-overview) 是建議的解決方案。 在此文件撰寫當下，ASR 還無法支援在 Azure 虛擬機器中複寫加速網路組態設定。

- **中央服務**。 這個 SAP 應用程式堆疊的元件也不會保存商務資料。 您可以在次要區域中建置虛擬機器，來執行中央服務角色。 主要中央服務節點中同步處理的唯一內容是 /sapmnt 共用內容。 另外，如果組態變更或核心更新是在主要中央服務伺服器上，它們必須在執行中央服務之次要區域中的虛擬機器上重複執行。 若要同步處理兩部伺服器，您可以使用 Azure Site Recovery 複寫叢集節點，或者只使用定期排程備份作業將 /sapmnt 複製到 DR 端。 如需組建、複製及測試容錯移轉程序的詳細資訊，請下載 [SAP NetWeaver：建置 Hyper-V 和以 Microsoft Azure 作為基礎的災害復原解決方案](https://download.microsoft.com/download/9/5/6/956FEDC3-702D-4EFB-A7D3-2DB7505566B6/SAP%20NetWeaver%20-%20Building%20an%20Azure%20based%20Disaster%20Recovery%20Solution%20V1_5%20.docx)，並參考 4.3 節：「SAP SPOF 層 (ASCS)」。 這份文件適用於在 Windows 中執行的 NetWeaver，但是您可以為 Linux 建立同等的組態。 針對中央服務，使用 [Azure Site Recovery](/en-us/azure/site-recovery/site-recovery-overview) 以複寫叢集節點和儲存體。 針對 Linux，使用高可用性擴充建立三個節點地理叢集。

- **SAP 資料庫層**。 針對 HANA 支援複寫使用 HSR。 除了本機、兩個節點的高可用性安裝以外，HSR 還支援多層式複寫，其中位於不同 Azure 區域的第三個節點會作為外部實體而不是叢集的一部分，且登錄至叢集 HSR 配對的次要複本，作為其複寫目標。 這樣會形成複寫菊輪鍊。 對 DR 節點的容錯移轉是手動程序。

若要使用 Azure Site Recovery 自動建置原始的完整複寫生產環境網站，您必須執行自訂[部署指令碼](/azure/site-recovery/site-recovery-runbook-automation)。 Site Recovery 首先會在可用性設定組中部署虛擬機器，然後執行指令碼以新增負載平衡器之類的資源。

## <a name="manageability-considerations"></a>管理性考量

SAP HANA 具有備份功能，會使用基本 Azure 基礎結構。 若要備份在 Azure 虛擬機器上執行的 SAP HANA 資料庫，就要使用 SAP HANA 快照集和 Azure 儲存體快照集來確保備份檔案的一致性。 如需詳細資訊，請參閱 [Azure 虛擬機器上的 SAP HANA 備份指南](/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)和 [Azure 備份服務常見問題集](/azure/backup/backup-azure-backup-faq)。 只有 HANA 單一容器部署支援 Azure 儲存體快照集。

### <a name="identity-management"></a>身分識別管理

藉由在所有層級使用集中式身分識別管理系統，以控制資源的存取權：

- 透過[角色型存取控制](/azure/active-directory/role-based-access-control-what-is) (RBAC) 提供 Azure 資源的存取權。
- 透過 LDAP、Azure Active Directory、Kerberos 或其他系統，授與 Azure 虛擬機器的存取權。
- 支援透過 SAP 提供的服務在應用程式本身內存取，或者使用 [OAuth 2.0 和 Azure Active Directory](/azure/active-directory/develop/active-directory-protocols-oauth-code)。

### <a name="monitoring"></a>監視

Azure 會提供幾個函式來[監視和診斷](/azure/architecture/best-practices/monitoring)整體基礎結構。 此外，Azure 虛擬機器 (Linux 或 Windows) 的增強型監視會由 Azure Operations Management Suite (OMS) 處理。

若要提供以 SAP 作為基礎的 SAP 基礎結構之資源與服務效能監視，請使用 [Azure SAP 增強型監視](/azure/virtual-machines/workloads/sap/deployment-guide#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)延伸模組。 此延伸模組會將 Azure 監視統計資料輸入 SAP 應用程式，以進行作業系統監視和 DBA Cockpit 函式。 SAP 增強型監視是在 Azure 上執行 SAP 的必要先決條件。 如需詳細資訊，請參閱 [SAP 附註 2191498](https://launchpad.support.sap.com/#/notes/2191498) -「Linux 上的 SAP 搭配 Azure：增強型監視」。

## <a name="security-considerations"></a>安全性考量

SAP 有它自己的「使用者管理引擎 (UME)」，可控制角色型存取和 SAP 應用程式內的授權。 如需詳細資訊，請參閱 [SAP HANA 安全性 - 概觀](https://archive.sap.com/documents/docs/DOC-62943) (需要 SAP Service Marketplace 帳戶才能進行存取)。

如需額外的網路安全性，請考慮實作[網路 DMZ](/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)，它會使用網路虛擬設備，在 Web Dispatcher 的子網路和 Fiori 前端伺服器集區前面建立防火牆。

針對基礎結構安全性，資料在傳輸中和靜止時會加密。 [Azure 虛擬機器上的 SAP NetWeaver - 規劃和實作指南](/azure/virtual-machines/workloads/sap/planning-guide)的「安全性考量」一節會開始說明網路安全性並套用至 S/4HANA。 本指南也會指定您在防火牆上必須開啟才能允許應用程式通訊的網路連接埠。

如需加密 Linux IaaS 虛擬機器磁碟，您可以使用 [Azure 磁碟加密](/azure/security/azure-security-disk-encryption)。 它會使用 Linux 的 DM-Crypt 功能來提供作業系統和資料磁碟的磁碟區加密。 此解決方案也可與 Azure Key Vault 搭配使用，協助您控制及管理金鑰保存庫訂用帳戶中的磁碟加密金鑰與祕密。 虛擬機器磁碟上的所有待用資料都會在您的 Azure 儲存體中加密。

針對 SAP HANA 靜止資料加密，建議您使用 SAP HANA 原生加密技術。

> [!NOTE]
> 請勿在相同的伺服器上使用 HANA 靜止資料加密搭配 Azure 磁碟加密。 針對 Hana，僅使用 HANA 資料加密。

## <a name="communities"></a>社群

社群可以回答問題並協助您設定成功的部署。 請考慮下列：

- [在 Microsoft 平台上執行 SAP 應用程式部落格](https://blogs.msdn.microsoft.com/saponsqlserver/2017/05/04/sap-on-azure-general-update-for-customers-partners-april-2017/)
- [Azure 社群支援](https://azure.microsoft.com/support/community/)
- [SAP 社群](https://www.sap.com/community.html)
- [Stack Overflow](https://stackoverflow.com/tags/sap/)

## <a name="related-resources"></a>相關資源

您可以檢閱下列 [Azure 範例案例](/azure/architecture/example-scenario)，其中示範使用相同技術的一些特定解決方案：

- [在 Azure 上使用 Oracle Database 來執行 SAP 生產環境工作負載](/azure/architecture/example-scenario/apps/sap-production)
- [Azure 上 SAP 工作負載的開發/測試環境](/azure/architecture/example-scenario/apps/sap-dev-test)

<!-- links -->

[visio-download]: https://archcenter.blob.core.windows.net/cdn/sap-reference-architectures.vsdx

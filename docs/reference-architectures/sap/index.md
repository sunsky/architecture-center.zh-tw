---
title: "在 Azure 上部署 SAP NetWeaver 和 SAP HANA"
description: "Azure 高可用性環境中執行 SAP HANA 的經過證實做法。"
author: njray
ms.date: 06/29/2017
ms.openlocfilehash: 27a97103c0c6f305cb8e830d670c8d0ba7e22aa5
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="deploy-sap-netweaver-and-sap-hana-on-azure"></a>在 Azure 上部署 SAP NetWeaver 和 SAP HANA

此參考架構會顯示一組經過證實的做法，能在 Azure 的高可用性環境中執行 SAP HANA。 [**部署這個解決方案**。](#deploy-the-solution)

![0][0]

*下載這個架構的 [Visio 檔案][visio-download]。*

> [!NOTE]
> 部署此參考架構需要 SAP 產品的適當授權和其他非 Microsoft 技術。 如需 Microsoft 與 SAP 之間合作關係的相關資訊，請參閱 [Azure 上的 SAP HANA][sap-hana-on-azure]。

## <a name="architecture"></a>架構

此架構由下列元件組成。

- **虛擬網路 (VNet)**。 VNet 是 Azure 中邏輯上隔離網路的表示法。 此參考架構中的所有 VM 都會部署到相同的 VNet。 VNet 會再細分為子網路。 針對每一層建立不同的子網路，包括應用程式 (SAP NetWeaver)、資料庫 (SAP HANA)、管理 (jumpbox) 和 Active Directory。

- **虛擬機器 (VM)**。 這個架構的 VM 群組為數個不同層。

    - **SAP NetWeaver**。 包含 SAP ASCS、SAP Web Dispatcher 和 SAP 應用程式伺服器。 
    
    - **SAP HANA**。 此參考架構會將 SAP HANA 用於資料庫層，在單一 [GS5][vm-sizes-mem] 執行個體上執行。 SAP HANA 通過 GS5 上生產 OLAP 工作負載，或 [SAP HANA on Azure 大型執行個體][azure-large-instances]的認證。 此參考架構適用於 M 系列和 G 系列中的 Azure 虛擬機器。 如需 Azure 大型執行個體上 SAP HANA 的相關資訊，請參閱 [Azure 上的 SAP HANA (大型執行個體) 概觀和架構][azure-large-instances]。
   
    - **Jumpbox**。 也稱為防禦主機。 這是網路上系統管理員用來連線到其他 VM 的安全 VM。 
     
    - **Windows Server Active Directory (AD) 網域控制站。** 網域控制站可用來設定 Windows Server 容錯移轉叢集 (大型執行個體)。
 
- **可用性設定組**。 將 SAP Web Dispatcher、SAP 應用程式伺服器和 SAP ACSC 角色的 VM 放入個別的可用性設定組，並針對每個角色佈建至少兩個 VM。 這讓虛擬機器能夠符合較高服務等級協定 (SLA) 的資格。
    
- **NIC。** 執行 SAP NetWeaver 和 SAP HANA 的 VM 需要兩個網路介面 (NIC)。 每個 NIC 會指派給不同的子網路，以分隔不同類型的流量。 如需詳細資訊，請參閱下方的[建議](#recommendations)。

- **Windows Server 容錯移轉叢集**。 執行 SAP ACSC 的 VM 會設定為高可用性的容錯移轉叢集。 若要支援容錯移轉叢集，SIOS DataKeeper 叢集版會執行叢集共用磁碟區 (CSV) 函式，方法為複寫叢集節點所擁有的獨立磁碟。 如需詳細資訊，請參閱[在 Microsoft 平台上執行 SAP 應用程式][running-sap]。
    
- **負載平衡器。** 會使用兩個 [Azure Load Balancer][azure-lb] 執行個體。 第一個會顯示在圖表的左邊，將流量散發至 SAP Web Dispatcher。 此設定會實作 [SAP Web Dispatcher 的高可用性][sap-dispatcher-ha]中所述的平行 web 發送器選項。 第二個負載平衡器會顯示在右側，可允許 Windows Server 容錯移轉叢集中的容錯移轉，方法是將連入連線導向至作用中/狀況良好的節點。

- **VPN 閘道。** VPN 閘道會將您的內部部署網路擴充至 Azure VNet。 您也可以使用 ExpressRoute，其所使用的專用私人連線不會通過公用網際網路。 範例解決方案不會部署閘道。 如需詳細資訊，請參閱[將內部部署網路連線至 Azure][hybrid-networking]。

## <a name="recommendations"></a>建議

您的需求可能不同於這裡所述的架構。 請使用以下建議作為起點。

### <a name="load-balancers"></a>負載平衡器

[SAP Web 發送器][sap-dispatcher]會處理 HTTP(S) 到雙重堆疊伺服器 (ABAP 和 Java) 流量的負載平衡。 SAP 多年來持續提倡單一堆疊應用程式伺服器，因此現今在雙重堆疊部署模型上執行的應用程式非常稀少。 架構圖中所示的 Azure 負載平衡器會實作高可用性叢集的 SAP Web Dispatcher。

會在 SAP 內處理流向應用程式伺服器的流量負載平衡。 針對從 SAPGUI 用戶端透過 DIAG 和遠端函式呼叫 (RFC) 連線到 SAP 伺服器的流量，SCS 訊息伺服器會建立 SAP 應用程式伺服器[登入群組][logon-groups]來平衡負載。 

SMLG 是 SAP ABAP 交易，可用來管理 SAP 中央服務的登入負載平衡功能。 登入群組的後端集區具有一個以上的 ABAP 應用程式伺服器。 存取 ASCS 叢集服務的用戶端會透過前端 IP 位址連線到 Azure 負載平衡器。 ASCS 叢集虛擬網路名稱也有一個 IP 位址。 (選擇性) 此位址可與 Azure 負載平衡器上的其他 IP 位址相關聯，讓您可以從遠端管理叢集。  

### <a name="nics"></a>NIC

SAP 橫向管理函式需要在不同的 NIC 上隔離伺服器流量。 例如，商務資料應該與管理流量和備份資料傳輸區隔。 將多個 NIC 指派給不同的子網路即可進行此資料隔離。 如需詳細資訊，請參閱[建置高可用性的 SAP NetWeaver 和 SAP HANA][sap-ha] (PDF) 中的「網路」。

將管理 NIC 指派給管理子網路，並將資料通訊 NIC 指派給不同的子網路。 如需組態的詳細資訊，請參閱[建立及管理具有多個 NIC 的 Windows 虛擬機器][multiple-vm-nics]。

### <a name="azure-storage"></a>Azure 儲存體

利用所有的資料庫伺服器 VM，建議您使用 Azure 進階儲存體以獲得一致的讀取/寫入延遲。 針對 SAP 應用程式伺服器 (包括 (A)SCS 虛擬機器)，您可以使用 Azure 標準儲存體，因為應用程式的執行會發生在記憶體中，並只會使用磁碟來記錄。

為擁有最佳可靠性，建議您使用 [Azure 受控磁碟][managed-disks]。 受控磁碟可確保可用性設定組內的虛擬機器磁碟是各自獨立的，以避免發生單一失敗點。

> [!NOTE]
> 目前，此參考架構的 Resource Manager 範本並未使用受控磁碟。 我們正計劃更新範本以使用受控磁碟。

若要達到高 IOPS 和磁碟頻寬輸送量，可將儲存體磁碟區效能最佳化的常見做法套用於 Azure 儲存體配置。 例如，將多個磁碟串接在一起來建立較大的磁碟區，能夠改善 IO 效能。 在不常變更的儲存體內容上啟用讀取快取，能夠增強資料擷取的速度。 如需有關效能需求的詳細資訊，請參閱 [SAP 附註 1943937 - 硬體設定檢查工具][sap-1943937]。

針對備份資料存放區，建議使用 Azure Blob 儲存體的[非經常性存取層][cool-blob-storage]。 對於儲存較不常存取且長時間執行的資料，非經常性儲存層是符合成本效益的方式。

## <a name="scalability-considerations"></a>延展性考量

在 SAP 應用程式層中，Azure 會提供各種不同的虛擬機器大小可進行相應增加。 如需完整清單，請參閱 [SAP 附註 1928533 - Azure 上的 SAP 應用程式︰支援的產品和 Azure VM 類型][sap-1928533]。 將多個 VM 新增至可用性設定組來相應放大。

針對包含 OLTP 與 OLAP SAP 應用程式之 Azure 虛擬機器上的 SAP HANA，SAP 認證的虛擬機器大小為 GS5，並具有單一 VM 執行個體。 對於較大的工作負載，Microsoft 也會針對共置在 Microsoft Azure 認證資料中心之實體伺服器上的 SAP HANA 提供 [Azure 大型執行個體][azure-large-instances]，目前能為單一執行個體提供最多 4 TB 的記憶體容量。 多重節點組態也可具有多達 32 TB 的記憶體總容量。

## <a name="availability-considerations"></a>可用性考量

在集中式資料庫上的 SAP 應用程式的這個分散式安裝中，會複寫基底安裝來達到高可用性。 針對每一層的架構，高可用性設計會有所不同：

- **Web Dispatcher。** 透過備援 SAP Web Dispatcher 執行個體與 SAP 應用程式流量來達到高可用性。 請參閱 SAP 文件中的 [SAP Web 發送器][swd]。

- **ASCS。** 針對 Azure Windows 虛擬機器上的 ASCS 高可用性，Windows Server 容錯移轉叢集會與 SIOS DataKeeper 搭配使用，以實作叢集共用磁碟區。 如需實作詳細資料，請參閱[在 Azure 上叢集 SAP ASCS][clustering]。

- **應用程式伺服器。** 藉由應用程式伺服器集區內的負載平衡流量來達到高可用性。

- **資料庫層。** 此參考架構會部署單一 SAP HANA 資料庫執行個體。 針對高可用性，部署多個執行個體，並使用 HANA 系統複寫 (HSR) 來實作手動容錯移轉。 若要啟用自動容錯移轉，需要適用於特定 Linux 發佈的 HA 延伸模組。

### <a name="disaster-recovery-considerations"></a>災害復原考量

每一層會使用不同的策略來提供災害復原 (DR) 保護。

- **應用程式伺服器。** SAP 應用程式伺服器不包含商務資料。 在 Azure 上，簡易 DR 策略是在另一個區域建立 SAP 應用程式伺服器。 在主要應用程式伺服器上進行任何組態變更或核心更新時，必須將相同的變更複製到 DR 區域中的 VM。 例如，核心可執行檔複製到 DR VM。

- **SAP 中央服務。** 這個 SAP 應用程式堆疊的元件也不會保存商務資料。 您可以在 DR 區域中建置 VM 來執行 SCS 角色。 主要 SCS 節點中同步處理的唯一內容是 **/sapmnt** 共用內容。 此外，如果組態變更或核心更新在主要 SCS 伺服器上進行，它們就必須在 DR SCS 上重複。 若要將兩部伺服器同步，只需使用定期排程的複製作業將 **/sapmnt** 複製到 DR 端。 如需組建、複製及測試容錯移轉程序的詳細資訊，請下載 [SAP NetWeaver：建置 Hyper-V 和以 Microsoft Azure 作為基礎的災害復原解決方案][sap-netweaver-dr]，並參考「4.3. SAP SPOF layer (ASCS)。」

- **資料庫層。** 使用 HANA 支援的複寫解決方案，例如 HSR 或儲存體複寫。 

## <a name="manageability-considerations"></a>管理性考量

SAP HANA 具有備份功能，是使用基本 Azure 基礎結構。 若要備份在 Azure 虛擬機器上執行的 SAP HANA 資料庫，就要使用 SAP HANA 快照集和 Azure 儲存體快照集來確保備份檔案的一致性。 如需詳細資訊，請參閱[Azure 虛擬機器上的 SAP HANA 備份指南][hana-backup]和[Azure 備份服務常見問題集][backup-faq]。

Azure 會提供幾個函式來[監視和診斷][monitoring]整體基礎結構。 此外，Azure 虛擬機器 (Linux 或 Windows) 的增強型監視會由 Azure Operations Management Suite (OMS) 處理。

若要提供以 SAP 作為基礎的 SAP 基礎結構之資源與服務效能監視，請使用「Azure SAP 強化監視」延伸模組。 此延伸模組會將 Azure 監視統計資料輸入 SAP 應用程式，以進行作業系統監視和 DBA Cockpit 函式。 

## <a name="security-considerations"></a>安全性考量

SAP 有它自己的「使用者管理引擎 (UME)」，可控制角色型存取和 SAP 應用程式內的授權。 如需詳細資訊，請參閱 [SAP HANA 安全性 - 概觀][sap-security]。 (需要 SAP Service Marketplace 帳戶才能進行存取。)

針對基礎結構安全性，資料在傳輸中和靜止時會受到保護。 [Azure 虛擬機器 (VM) 上的 SAP NetWeaver - 規劃和實作指南][netweaver-on-azure]的「安全性考量」一節會開始說明網路安全性。 本指南也會指定您在防火牆上必須開啟才能允許應用程式通訊的網路連接埠。 

如需加密 Windows 和 Linux IaaS 虛擬機器磁碟，您可以使用 [Azure 磁碟加密][disk-encryption]。 Azure 磁碟加密使用 Windows 的 BitLocker 功能和 Linux 的 DM-Crypt 功能，為作業系統和資料磁碟提供磁碟區加密。 此解決方案也可與 Azure Key Vault 搭配使用，協助您控制及管理 Key Vault 訂用帳戶中的磁碟加密金鑰與祕密。 虛擬機器磁碟上的所有待用資料都會在您的 Azure 儲存體中加密。

針對 SAP HANA 靜止資料加密，建議您使用 SAP HANA 原生加密技術。

> [!NOTE]
> 請勿在相同的伺服器上使用 HANA 靜止資料加密搭配 Azure 磁碟加密。

請考慮使用[網路安全性群組][nsg] (NSG) 來限制 VNet 中各種子網路之間的流量。

## <a name="deploy-the-solution"></a>部署解決方案 

此參考架構的部署指令碼可在 [GitHub][github] 上取得。


### <a name="prerequisites"></a>必要條件

- 您必須能夠存取 SAP 軟體下載中心才能完成安裝。
 
- 安裝最新版的 [Azure PowerShell][azure-ps]。 

- 此部署需要 51 個核心。 在部署之前，請確認您的訂用帳戶具有足夠的配額可供 VM 核心使用。 否則，請使用 Azure 入口網站來提交支援要求以取得更多配額。
 
- 此部署會使用 GS 系列 VM。 在[這裡][region-availability]依區域檢查 GS 系列的可用性。

- 若要預估此部署的成本，請參閱 [Azure 定價計算機][azure-pricing]。 
 
此參考架構會部署下列 VM：

| 資源名稱 | VM 大小 | 目的  |
|---------------|---------|----------|
| `ra-sapApps-scs-vm1` ... `ra-sapApps-scs-vmN` | DS11v2 | SAP 中央服務 |
| `ra-sapApps-vm1` ... `ra-sapApps-vmN` | DS11v2 | SAP NetWeaver 應用程式 |
| `ra-sap-wdp-vm1` ... `ra-sap-wdp-vmN` | DS11v2 | SAP Web Dispatcher |
| `ra-sap-data-vm1` | GS5 | SAP HANA 資料庫執行個體 |
| `ra-sap-jumpbox-vm1` | DS1V2 | Jumpbox |

會部署單一的 SAP HANA 執行個體。 針對應用程式 VM，會在範本參數中指定要部署的執行個體數目。

### <a name="deploy-sap-infrastructure"></a>部署 SAP 基礎結構

您可以以累加方式或一次全部部署此架構。 第一次，建議您使用累加式部署，以便您看到每個部署步驟的執行方式。 使用下列其中一個 mode 參數來指定增量

| 模式           | 作用                                                                                                            |
|----------------|-----------------------------------------------------|
| 基礎結構 | 在 Azure 中部署網路基礎結構。        |
| 工作負載       | 將 SAP 伺服器部署至網路。             |
| 所有            | 部署所有先前的部署。              |

若要部署解決方案，請執行下列步驟：

1. 將 [GitHub 存放庫][github]下載或複製到本機電腦。

2. 開啟 PowerShell 視窗並瀏覽至 `/sap/sap-hana/` 資料夾。

3. 執行下列 PowerShell Cmdlet。 針對 `<subscription id>`，請使用您的 Azure 訂用帳戶 ID。 針對 `<location>`，請指定 Azure 區域，例如 `eastus` 或 `westus`。 針對 `<mode>`，請指定一個以上所列的模式。

    ```powershell
     .\Deploy-ReferenceArchitecture -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group> <mode>
    ```

4.  出現系統時，請登入您的 Azure 帳戶。 

部署指令碼可能需要數小時才能完成，視您選取的模式而定。

> [!WARNING]
> 參數檔案會在不同位置中包含硬式編碼的密碼 (`AweS0me@PW`)。 在部署之前，請變更這些值。
 
### <a name="configure-sap-applications-and-database"></a>設定 SAP 應用程式和資料庫

部署 SAP 基礎結構之後，請在虛擬機器上安裝及設定您的 SAP 應用程式和 HANA 資料庫，如下所示。

> [!NOTE]
> 如需 SAP 安裝指示，您必須擁有 SAP 支援入口網站的使用者名稱和密碼，才能下載 [SAP 安裝指南][sap-guide]。

1. 登入 jumpbox (`ra-sap-jumpbox-vm1`)。 您將使用 jumpbox 來登入其他 VM。 

2.  針對每個名為 `ra-sap-wdp-vm1` ... `ra-sap-wdp-vmN` 的 VM，登入 VM，然後使用 [Web Dispatcher安裝][sap-dispatcher-install] wiki 中所述的步驟來安裝和設定 SAP Web Dispatcher 執行個體。

3.  登入名為 `ra-sap-data-vm1` 的 VM。 使用 [SAP HANA 伺服器安裝與更新指南][hana-guide]來安裝和設定 SAP HANA 資料庫執行個體。

4. 針對每個名為 `ra-sapApps-scs-vm1` ... `ra-sapApps-scs-vmN` 的 VM，登入 VM，然後使用 [SAP 安裝指南][sap-guide]來安裝和設定 SAP 中央服務 (SCS)。

5.  針對每個名為 `ra-sapApps-vm1` ... `ra-sapApps-vmN` 的 VM，登入 VM，然後使用 [SAP 安裝指南][sap-guide]來安裝和設定 SAP NetWeaver 應用程式。

**_此參考架構的參與者_** &mdash; Rick Rainey、Ross Sponholtz、Ben Trinh

[azure-large-instances]: /azure/virtual-machines/workloads/sap/hana-overview-architecture
[azure-lb]: /azure/load-balancer/load-balancer-overview
[azure-pricing]: https://azure.microsoft.com/pricing/calculator/
[azure-ps]: /powershell/azure/overview
[backup-faq]: /azure/backup/backup-azure-backup-faq
[clustering]: https://blogs.msdn.microsoft.com/saponsqlserver/2015/05/20/clustering-sap-ascs-instance-using-windows-server-failover-cluster-on-microsoft-azure-with-sios-datakeeper-and-azure-internal-load-balancer/
[cool-blob-storage]: /azure/storage/storage-blob-storage-tiers
[disk-encryption]: /azure/security/azure-security-disk-encryption
[github]: https://github.com/mspnp/reference-architectures/tree/master/sap/sap-hana
[hana-backup]: /azure/virtual-machines/workloads/sap/sap-hana-backup-guide
[hana-guide]: https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.01/en-US/7eb0167eb35e4e2885415205b8383584.html
[hybrid-networking]: ../hybrid-networking/index.md
[logon-groups]: https://wiki.scn.sap.com/wiki/display/SI/ABAP+Logon+Group+based+Load+Balancing
[managed-disks]: /azure/storage/storage-managed-disks-overview
[monitoring]: /azure/architecture/best-practices/monitoring
[multiple-vm-nics]: /azure/virtual-machines/windows/multiple-nics
[netweaver-on-azure]: /azure/virtual-machines/workloads/sap/planning-guide
[nsg]: /azure/virtual-network/virtual-networks-nsg
[region-availability]: https://azure.microsoft.com/regions/services/
[running-SAP]: https://blogs.msdn.microsoft.com/saponsqlserver/2016/06/07/sap-on-sql-general-update-for-customers-partners-june-2016/
[sap-1943937]: https://launchpad.support.sap.com/#/notes/1943937
[sap-1928533]: https://launchpad.support.sap.com/#/notes/1928533
[sap-dispatcher]: https://help.sap.com/doc/saphelp_nw73ehp1/7.31.19/en-US/48/8fe37933114e6fe10000000a421937/frameset.htm
[sap-dispatcher-ha]: https://help.sap.com/doc/saphelp_nw73ehp1/7.31.19/en-US/48/9a9a6b48c673e8e10000000a42189b/frameset.htm
[sap-dispatcher-install]: https://wiki.scn.sap.com/wiki/display/SI/Web+Dispatcher+Installation
[sap-guide]: https://service.sap.com/instguides
[sap-ha]: https://support.sap.com/content/dam/SAAP/SAP_Activate/AGS_70.pdf
[sap-hana-on-azure]: https://azure.microsoft.com/services/virtual-machines/sap-hana/
[sap-netweaver-dr]: http://download.microsoft.com/download/9/5/6/956FEDC3-702D-4EFB-A7D3-2DB7505566B6/SAP%20NetWeaver%20-%20Building%20an%20Azure%20based%20Disaster%20Recovery%20Solution%20V1_5%20.docx
[sap-security]: https://archive.sap.com/documents/docs/DOC-62943
[visio-download]: https://archcenter.azureedge.net/cdn/SAP-HANA-architecture.vsdx
[vm-sizes-mem]: /azure/virtual-machines/windows/sizes-memory
[swd]: https://help.sap.com/doc/saphelp_nw70ehp2/7.02.16/en-us/48/8fe37933114e6fe10000000a421937/frameset.htm
[0]: ./images/sap-hana.png "使用 Microsoft Azure 的 SAP HANA 架構"

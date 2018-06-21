---
title: 在 Azure 上執行 Jenkins 伺服器
description: 此參考架構會顯示如何在受到單一登入 (SSO) 保護的 Azure 上，部署和操作可調整的企業級 Jenkins 伺服器。
author: njray
ms.date: 01/21/18
ms.openlocfilehash: 5f9c54e71a8750e88de1ae633ccc1316f8375d3a
ms.sourcegitcommit: 0de300b6570e9990e5c25efc060946cb9d079954
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2018
ms.locfileid: "32323919"
---
# <a name="run-a-jenkins-server-on-azure"></a>在 Azure 上執行 Jenkins 伺服器

此參考架構會顯示如何在受到單一登入 (SSO) 保護的 Azure 上，部署和操作可調整的企業級 Jenkins 伺服器。 該架構也會使用 Azure 監視器來監視 Jenkins 伺服器的狀態。 [**部署這個解決方案**。](#deploy-the-solution)

![在 Azure 上執行的 Jenkins 伺服器][0]

下載包含此架構圖表的 [Visio 檔案](https://archcenter.blob.core.windows.net/cdn/Jenkins-architecture.vsdx)。

此架構可透過 Azure 服務支援災害復原，但並未涵蓋涉及多部主機的向外延展案例，或無停機時間的高可用性。 如需各種 Azure 元件的一般深入解析 (包括在 Azure 上建置 CI/CD 管線的相關逐步教學課程)，請參閱[在 Azure 上的 Jenkins][jenkins-on-azure]。

這份文件的重點是討論支援 Jenkins 所需的核心 Azure 作業 (包括使用 Azure 儲存體來維護組建成品、SSO 所需的安全性項目、其他可整合的服務和管線的延展性)。 該架構是設計為可與現有原始檔控制存放庫搭配使用。 例如，常見案例是根據 GitHub 認可來啟動 Jenkins 作業。

## <a name="architecture"></a>架構

此架構由下列元件組成：

- **資源群組。** [資源群組][rg]可用來將 Azure 資產分組，讓它們可以受到存留期、擁有者及其他準則所管理。 使用資源群組，以群組為單位來部署和監視資源，並根據資源群組來追蹤帳單成本。 您也可以刪除整組資源，這對於測試部署非常有用。

- **Jenkins 伺服器**。 虛擬機器會部署為自動化伺服器來執行 [Jenkins][azure-market]，並充當 Jenkins 主機。 此參考架構會使用在 Azure 上的 Linux (Ubuntu 16.04 LTS) 虛擬機器上所安裝的 [Azure 上的 Jenkins 解決方案範本][solution]。 Microsoft Azure Marketplace 中提供其他 Jenkins 產品供應項目。

  > [!NOTE]
  > Nginx 已安裝在虛擬機器上用來作為 Jenkins 的反向 proxy。 您可以將 Nginx 設定為啟用對 Jenkins 伺服器的 SSL。
  > 
  > 

- **虛擬網路**。 [虛擬網路][vnet]會將 Azure 資源互相連線，並提供邏輯隔離。 在此架構中，Jenkins 伺服器會在虛擬網路中執行。

- **子網路**。 系統會將 Jenkins 伺服器隔離在[子網路][subnet]中，讓您能更輕鬆地管理和隔離網路流量，而不會影響效能。

- <strong>NSG</strong>。 使用[網路安全性群組][nsg] (NSG) 來限制從網際網路到虛擬網路子網路的網路流量。

- **受控磁碟**。 [受控磁碟][managed-disk]是用於應用程式儲存區的永續性虛擬硬碟 (VHD)，也可用來維護 Jenkins 伺服器的狀態，並提供災害復原。 資料磁碟會儲存在 Azure 儲存體中。 如需高效能，建議使用[進階儲存體][premium]。

- **Azure Blob 儲存體**。 [Windows Azure 儲存體外掛程式][configure-storage]會使用 Azure Blob 儲存體，來儲存透過其他 Jenkins 組建所建立且共用的組建成品。

- <strong>Azure Active Directory (Azure AD)</strong>。 [Azure AD][azure-ad] 支援使用者驗證，讓您可設定 SSO。 Azure AD [服務主體][service-principal]會使用[以角色為基礎的存取控制][rbac] (RBAC)，來定義工作流程中每個角色授權的原則和權限。 每個服務主體都會與 Jenkins 作業相關聯。

- **Azure Key Vault**。 為了在需要密碼時，管理用來佈建 Azure 資源的祕密和密碼編譯金鑰，此架構會使用 [Key Vault][key-vault]。 如需在管線中與應用程式相關聯的已新增說明儲存祕密，也請參閱 Jenkins 的 [Azure 認證][configure-credential]外掛程式。

- **Azure 監視服務**。 此服務會[監視][monitor]裝載 Jenkins 的 Azure 虛擬機器。 這個部署會監視虛擬機器狀態與 CPU 使用率，而且會傳送警示。

## <a name="recommendations"></a>建議

下列建議適用於大部分的案例。 除非您有特定的需求會覆寫它們，否則請遵循下列建議。

### <a name="azure-ad"></a>Azure AD

Azure 訂用帳戶的 [Azure AD][azure-ad] 租用戶可用來啟用對 Jenkins 使用者的 SSO 並設定[服務主體][service-principal]，該主體可讓 Jenkins 作業存取 Azure 資源。

Jenkins 伺服器上安裝的 Azure AD 外掛程式會實作 SSO 驗證和授權。 SSO 可讓您在登入 Jenkins 伺服器時，使用來自 Azure AD 的組織認證來進行驗證。 設定 Azure AD 外掛程式時，您可以指定使用者對 Jenkin 伺服器之授權存取權的層級。

為了提供 Jenkins 作業 Azure 資源的存取權，Azure AD 系統管理員會建立服務主體。 這些主體會授與應用程式 (在此情況下即為 Jenkins 作業) 對 Azure 資源的[經驗證、獲授權的存取權][ad-sp]。

[RBAC][rbac] 會透過其獲指派的角色，進一步為使用者或服務主體定義並控制對 Azure 資源的存取權。 內建和自訂角色皆受到系統支援。 角色也有助於保護管線，並確保系統已正確指派並授權使用者或代理程式的責任。 此外，可以將 RBAC 設定為限制存取 Azure 資產。 例如，可以將使用者限制為只能使用特定資源群組中的資產。

### <a name="storage"></a>儲存體

使用透過 Microsoft Azure Marketplace 安裝的 Jenkins [Windows Azure 儲存體外掛程式][storage-plugin]，來儲存可與其他組建和測試共用的成品組建。 必須先設定 Azure 儲存體帳戶，該 Jenkins 作業才能使用此外掛程式。

### <a name="jenkins-azure-plugins"></a>Jenkins Azure 外掛程式

在 Azure 上的 Jenkins 解決方案範本會安裝數個 Azure 外掛程式。 Azure 的 DevOps 小組會建置並維護該解決方案範本以及下列外掛程式，這些會與 Microsoft Azure Marketplace 中的其他 Jenkins 供應項目與內部部署上設定的任何 Jenkins 主機搭配使用：

-   [Azure AD 外掛程式][configure-azure-ad]可讓 Jenkins 伺服器支援對以 Azure AD 為基礎的使用者的 SSO。

-   [Azure 虛擬機器代理程式][configure-agent]外掛程式會使用 Azure Resource Manager 範本，以在 Azure 虛擬機器中建立 Jenkins 代理程式。

-   [Azure 認證][configure-credential]外掛程式可讓您將 Azure 服務主體認證儲存在 Jenkins 中。

-   [Windows Azure 儲存體外掛程式][configure-storage]會將組建成品上傳至 [Azure Blob 儲存體][blob]，或從其中下載組建相依性。

我們也建議您檢閱可與 Azure 資源搭配使用的所有可用 Azure 外掛程式的清單，該清單仍會不斷新增。 若要查看所有最新的清單，請造訪 [Jenkins 外掛程式索引][index]並搜尋 Azure。 例如，下列外掛程式可供部署：

-   [Azure 容器代理程式][container-agents]可協助您在 Jenkins 中執行容器作為代理程式。

-   [Kubernetes 連續部署](https://aka.ms/azjenkinsk8s)會將資源設定部署至 Kubernetes 叢集。

-   [Azure Container Service][acs] 會將設定部署至具 Kubernetes 的 Azure Container Service、具 Marathon 的 DC/OS 或 Docker Swarm。

-   [Azure Functions][functions] 會將您的專案部署至 Azure Functions。

-   [Azure App Service][app-service] 會部署至 Azure App Service。

## <a name="scalability-considerations"></a>延展性考量

Jenkins 的大小可以調整以支援超大型工作負載。 對於彈性組建，請勿在 Jenkins 主要伺服器上執行組建。 而是將組建工作卸載至 Jenkins 代理程式，其可以依所需彈性地相應縮小和相應放大。 請考慮適用於調整代理程式大小的兩個選項：

- 使用 [Azure 虛擬機器代理程式][vm-agent]外掛程式，以建立在 Azure 虛擬機器中執行的 Jenkins 代理程式。 此外掛程式可讓代理程式彈性地相應放大，而且可以使用不同類型的虛擬機器。 您可以從 Microsoft Azure Marketplace 中選取不同的基礎映像或使用自訂映像。 如需 Jenkins 代理程式如何調整大小的詳細資訊，請參閱 Jenkins 文件中的[調整大小的架構][scale]。

- 使用 [Azure 容器代理程式][container-agents]外掛程式，以在[具 Kubernetes 的 Azure Container Service](/azure/container-service/kubernetes/) 或 [Azure 容器執行個體](/azure/container-instances/)中執行容器作為代理程式。

虛擬機器的調整成本通常大於容器。 若要將容器用於調整大小，您的組建流程必須與容器一起搭配執行。

此外，使用 Azure 儲存體來共用組建成品，其他組建代理程式可能會在管線的下一個階段中使用該成品。

### <a name="scaling-the-jenkins-server"></a>調整 Jenkins 伺服器的大小 

您可以藉由變更虛擬機器的大小來相應增加或減少 Jenkins 伺服器虛擬機器。 [在 Azure 上的 Jenkins 解決方案範本][azure-market]依預設會指定 DS2 v2 的大小 (含兩個 CPU，7 GB)。 此大小可處理小型至中型的小組工作負載。 建立伺服器時，請透過選擇不同的選項來變更虛擬機器的大小。 

請根據預期的工作負載大小選取正確的伺服器大小。 Jenkins 社群會維護[選取項目指南][selection-guide]，以協助找出最符合您需求的設定。 Azure 提供許多[適用於 Linux 虛擬機器的大小][sizes-linux]來符合所有需求。 如需有關調整 Jenkins 主機大小的詳細資訊，請參考[最佳做法][best-practices]的 Jenkins 社群，其中也包括調整 Jenkins 主機大小的相關詳細資料。


## <a name="availability-considerations"></a>可用性考量

Jenkins 伺服器內容中的可用性表示能夠復原與您工作流程關聯的任何狀態資訊 (例如測試結果、您已建立的文件庫，或其他成品)。 若 Jenkins 伺服器當機時，必須維護重要工作流程狀態或成品以復原工作流程。 若要評估您的可用性需求，請考量以下兩個計量：

-   復原時間目標 (RTO) 會指定在沒有 Jenkins 的情況您能運行多久的時間。

-   復原點目標 (RPO) 表示當進行中的中斷影響 Jenkins 時，您可以負擔多少的資料遺失量。

實際上，RTO 和 RPO 代表備援性和備份。 可用性不是硬體復原的問題 (那是 Azure 的問題)，而是要確保您會維護 Jenkins 伺服器的狀態。 Microsoft 會提供單一虛擬機器執行個體的[服務等級協定][sla] (SLA)。 如果這個 SLA 不符合您的執行時間需求，請確定您備妥災害復原的計畫，或考慮使用[多重主要 Jenkins 伺服器][multi-master]部署 (未涵蓋在本文件中)。

請考慮使用在部署步驟 7 的災害復原[指令碼][disaster]來建立具受控磁碟的 Azure 儲存體帳戶，以儲存 Jenkins 伺服器狀態。 Jenkins 當機時，可還原至此個別儲存體帳戶中所儲存的狀態。

## <a name="security-considerations"></a>安全性考量

因為基本 Jenkins 伺服器在基本狀態時並不安全，請使用下列方法在 Jenkins 伺服器上鎖定安全性。

-   設定 Jenkins 伺服器的安全登入方式。 這個架構會使用 HTTP 並具有公用 IP，但依預設 HTTP 並不安全。 請考慮在用於安全登入的 [Nginx 伺服器上設定 HTTPS][nginx]。

    > [!NOTE]
    > 將 SSL 新增至伺服器時，請建立 Jenkins 子網路的 NSG 規則以開啟連接埠 443。 如需詳細資訊，請參閱[如何使用 Azure 入口網站開啟對虛擬機器的連接埠][port443]。
    > 

-   請確定 Jenkins 設定可防止跨站台要求偽造 (管理 Jenkins \> 設定全域安全性)。 這是 Microsoft Jenkins 伺服器的預設值。

-   透過使用[矩陣授權策略外掛程式][matrix]來設定對 Jenkins 儀表板的唯讀存取。

-   安裝 [Azure 認證][configure-credential]外掛程式，以使用 Key Vault 來處理 Azure 資產、管線中的代理程式和第三方元件中的秘密。

-   使用 RBAC 將服務主體的存取限制為執行工作所需的最低限度。 這有助於限制 Rogue 作業帶來的損毀範圍。

Jenkins 作業通常需要祕密來存取需要授權的 Azure 服務 (例如 Azure Container Service)。 使用 [Key Vault][key-vault] 與 [Azure 認證外掛程式][configure-credential]來安全地管理這些祕密。 使用 Key Vault 來儲存服務主體認證、密碼、權杖和其他秘密。

使用 [Azure 資訊安全中心][security-center]來集中檢視 Azure 資源的安全性狀態。 資訊安全中心會監視潛在的安全性問題，並提供全面性的部署安全性健康狀態。 資訊安全中心是依每個 Azure 訂用帳戶設定。 請按照 [Azure 資訊安全中心快速入門指南][quick-start]中所述的方式，來啟用安全性資料收集。 啟用資料收集時，資訊安全性中心就會自動掃描任何該訂用帳戶建立的虛擬機器。

Jenkins 伺服器有其本身的使用者管理系統，而且 Jenkins 社群會提供最佳做法[來保護 Azure 上的 Jenkins 執行個體][secure-jenkins]。 在 Azure 上的 Jenkins 的解決方案範本會實作這些最佳做法。

## <a name="manageability-considerations"></a>管理性考量

使用資源群組來組織部署的 Azure 資源。 在個別的資源群組中部署生產環境和開發/測試環境，讓您可以監視每個環境的資源，並彙總依資源群組的帳單成本。 您也可以刪除整組資源，這對於測試部署非常有用。

Azure 會提供幾個功能來[監視和診斷][monitoring-diag]整體架構。 若要監視 CPU 使用量，這個架構會部署 Azure 監視器。 例如，如果 CPU 使用量超過 80%，您可以使用 Azure 監視器來監視 CPU 使用率並傳送通知。 (高 CPU 使用量則表示您可以相應增加 Jenkins 伺服器虛擬機器的大小。)如果虛擬機器失敗或變得無法使用，您也可以通知指定的使用者。

## <a name="communities"></a>社群

社群可以回答問題並協助您設定成功的部署。 請考慮下列：

-   [Jenkins 社群部落格](https://jenkins.io/node/)
-   [Azure 論壇](https://azure.microsoft.com/support/forums/)
-   [Stack Overflow Jenkins](https://stackoverflow.com/tags/jenkins/info)

如需來自 Jenkins 社群的最佳做法，請造訪 [Jenkins 最佳做法][jenkins-best]。

## <a name="deploy-the-solution"></a>部署解決方案

若要部署這種架構，請遵循下列步驟來安裝[在 Azure 上的 Jenkins 的解決方案範本][azure-market]，然後安裝在下列步驟中設定監視和災害復原的指令碼。

### <a name="prerequisites"></a>先決條件

- 此參考架構需要 Azure 訂用帳戶。 
- 若要建立 Azure 服務主體，您必須擁有 Azure AD 租用戶的系統管理員權限，該租用戶與部署的 Jenkins 伺服器相關聯。
- 這些指示會假設 Jenkins 系統管理員也是 Azure 使用者，且至少擁有參與者權限。

### <a name="step-1-deploy-the-jenkins-server"></a>步驟 1：部署 Jenkins 伺服器

1.  在 Web 瀏覽器中開啟 [Jenkins 的 Microsoft Azure Marketplace 映像][][azure-market]，然後從頁面左側選取 [立即取得]。

2.  檢閱價格詳細資料並選取 [繼續]，然後選取 [建立] 以在 Azure 入口網站中設定 Jenkins 伺服器。

如需詳細的指示，請參閱[從 Azure 入口網站在 Azure Linux 虛擬機器上建立 Jenkins 伺服器][create-jenkins]。 對於此參考架構，要啟動伺服器並使用系統管理員登入來執行便已足夠。 然後您可以佈建它去使用各種其他服務。

### <a name="step-2-set-up-sso"></a>步驟 2：設定 SSO

此步驟是由 Jenkins 系統管理員執行，此管理員也必須有訂用帳戶 Azure AD 目錄中的使用者帳戶，而且必須獲指派參與者角色。

在 Jenkin 伺服器中從 Jenkins 更新中心使用 [Azure AD 外掛程式][configure-azure-ad]並遵循指示來設定 SSO。

### <a name="step-3-provision-jenkins-server-with-azure-vm-agent-plugin"></a>步驟 3：使用 Azure 虛擬機器代理程式的外掛程式來佈建 Jenkins 伺服器

Jenkins 系統管理員會執行此步驟以設定已安裝的 Azure 虛擬機器代理程式外掛程式。

[請遵循這些步驟來設定該外掛程式][configure-agent]。 對於設定外掛程式的服務主體之相關教學課程中，請參閱[調整 Jenkins 部署的大小以符合與 Azure 虛擬機器代理程式的需求][scale-agent]。

### <a name="step-4-provision-jenkins-server-with-azure-storage"></a>步驟 4：佈建具 Azure 儲存體的 Jenkins 伺服器

Jenkins 系統管理員會執行此步驟，該管理員會設定已安裝的 Windows Azure 儲存體外掛程式。

[請遵循這些步驟來設定該外掛程式][configure-storage]。

### <a name="step-5-provision-jenkins-server-with-azure-credential-plugin"></a>步驟 5：佈建具 Azure 認證外掛程式的 Jenkins 伺服器

Jenkins 系統管理員會執行此步驟以設定已安裝的 Azure 認證外掛程式。

[請遵循這些步驟來設定該外掛程式][configure-credential]。

### <a name="step-6-provision-jenkins-server-for-monitoring-by-the-azure-monitor-service"></a>步驟 6：佈建 Jenkins 伺服器以供 Azure 監視器服務進行監視使用

若要設定 Jenkins 伺服器的監視，請依照[在 Azure 監視器中為 Azure 服務建立度量警示][create-metric]中的指示。

### <a name="step-7-provision-jenkins-server-with-managed-disks-for-disaster-recovery"></a>步驟 7：佈建具災害復原的受控磁碟之 Jenkins 伺服器

Microsoft Jenkins 產品群組已建立災害復原指令碼，其會建置用於儲存 Jenkins 狀態的受控磁碟。 如果伺服器當機時，該指令碼可以還原至其最新狀態。

從 [GitHub][disaster] 下載並執行災害復原指令碼。

[acs]: https://aka.ms/azjenkinsacs
[ad-sp]: /azure/active-directory/develop/active-directory-integrating-applications
[app-service]: https://plugins.jenkins.io/azure-app-service
[azure-ad]: /azure/active-directory/
[azure-market]: https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview
[best-practices]: https://jenkins.io/doc/book/architecting-for-scale/
[blob]: /azure/storage/common/storage-java-jenkins-continuous-integration-solution
[configure-azure-ad]: https://plugins.jenkins.io/azure-ad
[configure-agent]: https://plugins.jenkins.io/azure-vm-agents
[configure-credential]: https://plugins.jenkins.io/azure-credentials
[configure-storage]: https://plugins.jenkins.io/windows-azure-storage
[container-agents]: https://aka.ms/azcontaineragent
[create-jenkins]: /azure/jenkins/install-jenkins-solution-template
[create-metric]: /azure/monitoring-and-diagnostics/insights-alerts-portal
[disaster]: https://github.com/Azure/jenkins/tree/master/disaster_recovery
[functions]: https://aka.ms/azjenkinsfunctions
[index]: https://plugins.jenkins.io
[jenkins-best]: https://wiki.jenkins.io/display/JENKINS/Jenkins+Best+Practices
[jenkins-on-azure]: /azure/jenkins/
[key-vault]: /azure/key-vault/
[managed-disk]: /azure/virtual-machines/linux/managed-disks-overview
[matrix]: https://plugins.jenkins.io/matrix-auth
[monitor]: /azure/monitoring-and-diagnostics/
[monitoring-diag]: /azure/architecture/best-practices/monitoring
[multi-master]: https://jenkins.io/doc/book/architecting-for-scale/
[nginx]: https://www.digitalocean.com/community/tutorials/how-to-create-an-ssl-certificate-on-nginx-for-ubuntu-14-04
[nsg]: /azure/virtual-network/virtual-networks-nsg
[quick-start]: /azure/security-center/security-center-get-started
[port443]: /azure/virtual-machines/windows/nsg-quickstart-portal
[premium]: /azure/virtual-machines/linux/premium-storage
[rbac]: /azure/active-directory/role-based-access-control-what-is
[rg]: /azure/azure-resource-manager/resource-group-overview
[scale]: https://jenkins.io/doc/book/architecting-for-scale/
[scale-agent]: /azure/jenkins/jenkins-azure-vm-agents
[selection-guide]: https://jenkins.io/doc/book/hardware-recommendations/
[service-principal]: /azure/active-directory/develop/active-directory-application-objects
[secure-jenkins]: https://jenkins.io/blog/2017/04/20/secure-jenkins-on-azure/
[security-center]: /azure/security-center/security-center-intro
[sizes-linux]: /azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json
[solution]: https://azure.microsoft.com/blog/announcing-the-solution-template-for-jenkins-on-azure/
[sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/
[storage-plugin]: https://wiki.jenkins.io/display/JENKINS/Windows+Azure+Storage+Plugin
[subnet]: /azure/virtual-network/virtual-network-manage-subnet
[vm-agent]: https://wiki.jenkins.io/display/JENKINS/Azure+VM+Agents+plugin
[vnet]: /azure/virtual-network/virtual-networks-overview
[0]: ./images/jenkins-server.png 

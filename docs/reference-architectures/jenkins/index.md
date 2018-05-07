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
---
# <a name="run-a-jenkins-server-on-azure"></a><span data-ttu-id="71444-103">在 Azure 上執行 Jenkins 伺服器</span><span class="sxs-lookup"><span data-stu-id="71444-103">Run a Jenkins server on Azure</span></span>

<span data-ttu-id="71444-104">此參考架構會顯示如何在受到單一登入 (SSO) 保護的 Azure 上，部署和操作可調整的企業級 Jenkins 伺服器。</span><span class="sxs-lookup"><span data-stu-id="71444-104">This reference architecture shows how to deploy and operate a scalable, enterprise-grade Jenkins server on Azure secured with single sign-on (SSO).</span></span> <span data-ttu-id="71444-105">該架構也會使用 Azure 監視器來監視 Jenkins 伺服器的狀態。</span><span class="sxs-lookup"><span data-stu-id="71444-105">The architecture also uses Azure Monitor to monitor the state of the Jenkins server.</span></span> [<span data-ttu-id="71444-106">**部署這個解決方案**。</span><span class="sxs-lookup"><span data-stu-id="71444-106">**Deploy this solution**.</span></span>](#deploy-the-solution)

![在 Azure 上執行的 Jenkins 伺服器][0]

<span data-ttu-id="71444-108">下載包含此架構圖表的 [Visio 檔案](https://archcenter.blob.core.windows.net/cdn/Jenkins-architecture.vsdx)。</span><span class="sxs-lookup"><span data-stu-id="71444-108">*Download a [Visio file](https://archcenter.blob.core.windows.net/cdn/Jenkins-architecture.vsdx) that contains this architecture diagram.*</span></span>

<span data-ttu-id="71444-109">此架構可透過 Azure 服務支援災害復原，但並未涵蓋涉及多部主機的向外延展案例，或無停機時間的高可用性。</span><span class="sxs-lookup"><span data-stu-id="71444-109">This architecture supports disaster recovery with Azure services but does not cover more advanced scale-out scenarios involving multiple masters or high availability (HA) with no downtime.</span></span> <span data-ttu-id="71444-110">如需各種 Azure 元件的一般深入解析 (包括在 Azure 上建置 CI/CD 管線的相關逐步教學課程)，請參閱[在 Azure 上的 Jenkins][jenkins-on-azure]。</span><span class="sxs-lookup"><span data-stu-id="71444-110">For general insights about the various Azure components, including a step-by-step tutorial about building out a CI/CD pipeline on Azure, see [Jenkins on  Azure][jenkins-on-azure].</span></span>

<span data-ttu-id="71444-111">這份文件的重點是討論支援 Jenkins 所需的核心 Azure 作業 (包括使用 Azure 儲存體來維護組建成品、SSO 所需的安全性項目、其他可整合的服務和管線的延展性)。</span><span class="sxs-lookup"><span data-stu-id="71444-111">The focus of this document is on the core Azure operations needed to support Jenkins, including the use of Azure Storage to maintain build artifacts, the security items needed for SSO, other services that can be integrated, and scalability for the pipeline.</span></span> <span data-ttu-id="71444-112">該架構是設計為可與現有原始檔控制存放庫搭配使用。</span><span class="sxs-lookup"><span data-stu-id="71444-112">The architecture is designed to work with an existing source control repository.</span></span> <span data-ttu-id="71444-113">例如，常見案例是根據 GitHub 認可來啟動 Jenkins 作業。</span><span class="sxs-lookup"><span data-stu-id="71444-113">For example, a common scenario is to start Jenkins jobs based on GitHub commits.</span></span>

## <a name="architecture"></a><span data-ttu-id="71444-114">架構</span><span class="sxs-lookup"><span data-stu-id="71444-114">Architecture</span></span>

<span data-ttu-id="71444-115">此架構由下列元件組成：</span><span class="sxs-lookup"><span data-stu-id="71444-115">The architecture consists of the following components:</span></span>

- <span data-ttu-id="71444-116">**資源群組。**</span><span class="sxs-lookup"><span data-stu-id="71444-116">**Resource group.**</span></span> <span data-ttu-id="71444-117">[資源群組][rg]可用來將 Azure 資產分組，讓它們可以受到存留期、擁有者及其他準則所管理。</span><span class="sxs-lookup"><span data-stu-id="71444-117">A [resource group][rg] is used to group Azure assets so they can be managed by lifetime, owner, and other criteria.</span></span> <span data-ttu-id="71444-118">使用資源群組，以群組為單位來部署和監視資源，並根據資源群組來追蹤帳單成本。</span><span class="sxs-lookup"><span data-stu-id="71444-118">Use resource groups to deploy and monitor Azure assets as a group and track billing costs by resource group.</span></span> <span data-ttu-id="71444-119">您也可以刪除整組資源，這對於測試部署非常有用。</span><span class="sxs-lookup"><span data-stu-id="71444-119">You can also delete resources as a set, which is very useful for test deployments.</span></span>

- <span data-ttu-id="71444-120">**Jenkins 伺服器**。</span><span class="sxs-lookup"><span data-stu-id="71444-120">**Jenkins server**.</span></span> <span data-ttu-id="71444-121">虛擬機器會部署為自動化伺服器來執行 [Jenkins][azure-market]，並充當 Jenkins 主機。</span><span class="sxs-lookup"><span data-stu-id="71444-121">A virtual machine is deployed to run [Jenkins][azure-market] as an automation server and serve as Jenkins Master.</span></span> <span data-ttu-id="71444-122">此參考架構會使用在 Azure 上的 Linux (Ubuntu 16.04 LTS) 虛擬機器上所安裝的 [Azure 上的 Jenkins 解決方案範本][solution]。</span><span class="sxs-lookup"><span data-stu-id="71444-122">This reference architecture uses the [solution template for Jenkins on Azure][solution], installed on a Linux (Ubuntu 16.04 LTS) virtual machine on Azure.</span></span> <span data-ttu-id="71444-123">Microsoft Azure Marketplace 中提供其他 Jenkins 產品供應項目。</span><span class="sxs-lookup"><span data-stu-id="71444-123">Other Jenkins offerings are available in the Azure Marketplace.</span></span>

  > [!NOTE]
  > <span data-ttu-id="71444-124">Nginx 已安裝在虛擬機器上用來作為 Jenkins 的反向 proxy。</span><span class="sxs-lookup"><span data-stu-id="71444-124">Nginx is installed on the VM to act as a reverse proxy to Jenkins.</span></span> <span data-ttu-id="71444-125">您可以將 Nginx 設定為啟用對 Jenkins 伺服器的 SSL。</span><span class="sxs-lookup"><span data-stu-id="71444-125">You can configure Nginx to enable SSL for the Jenkins server.</span></span>
  > 
  > 

- <span data-ttu-id="71444-126">**虛擬網路**。</span><span class="sxs-lookup"><span data-stu-id="71444-126">**Virtual network**.</span></span> <span data-ttu-id="71444-127">[虛擬網路][vnet]會將 Azure 資源互相連線，並提供邏輯隔離。</span><span class="sxs-lookup"><span data-stu-id="71444-127">A [virtual network][vnet] connects Azure resources to each other and provides logical isolation.</span></span> <span data-ttu-id="71444-128">在此架構中，Jenkins 伺服器會在虛擬網路中執行。</span><span class="sxs-lookup"><span data-stu-id="71444-128">In this architecture, the Jenkins server runs in a virtual network.</span></span>

- <span data-ttu-id="71444-129">**子網路**。</span><span class="sxs-lookup"><span data-stu-id="71444-129">**Subnets**.</span></span> <span data-ttu-id="71444-130">系統會將 Jenkins 伺服器隔離在[子網路][subnet]中，讓您能更輕鬆地管理和隔離網路流量，而不會影響效能。</span><span class="sxs-lookup"><span data-stu-id="71444-130">The Jenkins server is isolated in a [subnet][subnet] to make it easier to manage and segregate network traffic without impacting performance.</span></span>

- <span data-ttu-id="71444-131"><strong>NSG</strong>。</span><span class="sxs-lookup"><span data-stu-id="71444-131"><strong>NSGs</strong>.</span></span> <span data-ttu-id="71444-132">使用[網路安全性群組][nsg] (NSG) 來限制從網際網路到虛擬網路子網路的網路流量。</span><span class="sxs-lookup"><span data-stu-id="71444-132">Use [network security groups][nsg] (NSGs) to restrict network traffic from the Internet to the subnet of a virtual network.</span></span>

- <span data-ttu-id="71444-133">**受控磁碟**。</span><span class="sxs-lookup"><span data-stu-id="71444-133">**Managed disks**.</span></span> <span data-ttu-id="71444-134">[受控磁碟][managed-disk]是用於應用程式儲存區的永續性虛擬硬碟 (VHD)，也可用來維護 Jenkins 伺服器的狀態，並提供災害復原。</span><span class="sxs-lookup"><span data-stu-id="71444-134">A [managed disk][managed-disk] is a persistent virtual hard disk (VHD) used for application storage and also to maintain the state of the Jenkins server and provide disaster recovery.</span></span> <span data-ttu-id="71444-135">資料磁碟會儲存在 Azure 儲存體中。</span><span class="sxs-lookup"><span data-stu-id="71444-135">Data disks are stored in Azure Storage.</span></span> <span data-ttu-id="71444-136">如需高效能，建議使用[進階儲存體][premium]。</span><span class="sxs-lookup"><span data-stu-id="71444-136">For high performance, [premium storage][premium] is recommended.</span></span>

- <span data-ttu-id="71444-137">**Azure Blob 儲存體**。</span><span class="sxs-lookup"><span data-stu-id="71444-137">**Azure Blob Storage**.</span></span> <span data-ttu-id="71444-138">[Windows Azure 儲存體外掛程式][configure-storage]會使用 Azure Blob 儲存體，來儲存透過其他 Jenkins 組建所建立且共用的組建成品。</span><span class="sxs-lookup"><span data-stu-id="71444-138">The [Windows Azure Storage plugin][configure-storage] uses Azure Blob  Storage to store the build artifacts that are created and shared with other Jenkins builds.</span></span>

- <span data-ttu-id="71444-139"><strong>Azure Active Directory (Azure AD)</strong>。</span><span class="sxs-lookup"><span data-stu-id="71444-139"><strong>Azure Active Directory (Azure AD)</strong>.</span></span> <span data-ttu-id="71444-140">[Azure AD][azure-ad] 支援使用者驗證，讓您可設定 SSO。</span><span class="sxs-lookup"><span data-stu-id="71444-140">[Azure AD][azure-ad] supports user authentication, allowing you to set up SSO.</span></span> <span data-ttu-id="71444-141">Azure AD [服務主體][service-principal]會使用[以角色為基礎的存取控制][rbac] (RBAC)，來定義工作流程中每個角色授權的原則和權限。</span><span class="sxs-lookup"><span data-stu-id="71444-141">Azure AD [service principals][service-principal] define the policy and permissions for each role authorization in the workflow, using [role-based access control][rbac] (RBAC).</span></span> <span data-ttu-id="71444-142">每個服務主體都會與 Jenkins 作業相關聯。</span><span class="sxs-lookup"><span data-stu-id="71444-142">Each service principal is associated with a Jenkins job.</span></span>

- <span data-ttu-id="71444-143">**Azure Key Vault**。</span><span class="sxs-lookup"><span data-stu-id="71444-143">**Azure Key Vault.**</span></span> <span data-ttu-id="71444-144">為了在需要密碼時，管理用來佈建 Azure 資源的祕密和密碼編譯金鑰，此架構會使用 [Key Vault][key-vault]。</span><span class="sxs-lookup"><span data-stu-id="71444-144">To manage secrets and cryptographic keys used to provision Azure resources when secrets are required, this architecture uses [Key Vault][key-vault].</span></span> <span data-ttu-id="71444-145">如需在管線中與應用程式相關聯的已新增說明儲存祕密，也請參閱 Jenkins 的 [Azure 認證][configure-credential]外掛程式。</span><span class="sxs-lookup"><span data-stu-id="71444-145">For added help storing secrets associated with the application in the pipeline, see also the [Azure Credentials][configure-credential] plugin for Jenkins.</span></span>

- <span data-ttu-id="71444-146">**Azure 監視服務**。</span><span class="sxs-lookup"><span data-stu-id="71444-146">**Azure monitoring services**.</span></span> <span data-ttu-id="71444-147">此服務會[監視][monitor]裝載 Jenkins 的 Azure 虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="71444-147">This service [monitors][monitor] the Azure virtual machine hosting Jenkins.</span></span> <span data-ttu-id="71444-148">這個部署會監視虛擬機器狀態與 CPU 使用率，而且會傳送警示。</span><span class="sxs-lookup"><span data-stu-id="71444-148">This deployment monitors the virtual machine status and CPU utilization and sends alerts.</span></span>

## <a name="recommendations"></a><span data-ttu-id="71444-149">建議</span><span class="sxs-lookup"><span data-stu-id="71444-149">Recommendations</span></span>

<span data-ttu-id="71444-150">下列建議適用於大部分的案例。</span><span class="sxs-lookup"><span data-stu-id="71444-150">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="71444-151">除非您有特定的需求會覆寫它們，否則請遵循下列建議。</span><span class="sxs-lookup"><span data-stu-id="71444-151">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="azure-ad"></a><span data-ttu-id="71444-152">Azure AD</span><span class="sxs-lookup"><span data-stu-id="71444-152">Azure AD</span></span>

<span data-ttu-id="71444-153">Azure 訂用帳戶的 [Azure AD][azure-ad] 租用戶可用來啟用對 Jenkins 使用者的 SSO 並設定[服務主體][service-principal]，該主體可讓 Jenkins 作業存取 Azure 資源。</span><span class="sxs-lookup"><span data-stu-id="71444-153">The [Azure AD][azure-ad] tenant for your Azure subscription is used to enable SSO for Jenkins users and set up [service principals][service-principal] that enable Jenkins jobs to access Azure resources.</span></span>

<span data-ttu-id="71444-154">Jenkins 伺服器上安裝的 Azure AD 外掛程式會實作 SSO 驗證和授權。</span><span class="sxs-lookup"><span data-stu-id="71444-154">SSO authentication and authorization are implemented by the Azure AD plugin  installed on the Jenkins server.</span></span> <span data-ttu-id="71444-155">SSO 可讓您在登入 Jenkins 伺服器時，使用來自 Azure AD 的組織認證來進行驗證。</span><span class="sxs-lookup"><span data-stu-id="71444-155">SSO allows you to authenticate using your organization credentials from Azure AD when logging on to the Jenkins server.</span></span> <span data-ttu-id="71444-156">設定 Azure AD 外掛程式時，您可以指定使用者對 Jenkin 伺服器之授權存取權的層級。</span><span class="sxs-lookup"><span data-stu-id="71444-156">When configuring the Azure AD plugin, you can specify the level of a user’s authorized access to the Jenkins server.</span></span>

<span data-ttu-id="71444-157">為了提供 Jenkins 作業 Azure 資源的存取權，Azure AD 系統管理員會建立服務主體。</span><span class="sxs-lookup"><span data-stu-id="71444-157">To provide Jenkins jobs with access to Azure resources, an Azure AD administrator creates service principals.</span></span> <span data-ttu-id="71444-158">這些主體會授與應用程式 (在此情況下即為 Jenkins 作業) 對 Azure 資源的[經驗證、獲授權的存取權][ad-sp]。</span><span class="sxs-lookup"><span data-stu-id="71444-158">These grant applications—in this case, the Jenkins jobs—[authenticated, authorized access][ad-sp] to Azure resources.</span></span>

<span data-ttu-id="71444-159">[RBAC][rbac] 會透過其獲指派的角色，進一步為使用者或服務主體定義並控制對 Azure 資源的存取權。</span><span class="sxs-lookup"><span data-stu-id="71444-159">[RBAC][rbac] further defines and controls access to Azure resources for users or service principals through their assigned role.</span></span> <span data-ttu-id="71444-160">內建和自訂角色皆受到系統支援。</span><span class="sxs-lookup"><span data-stu-id="71444-160">Both built-in and custom roles are supported.</span></span> <span data-ttu-id="71444-161">角色也有助於保護管線，並確保系統已正確指派並授權使用者或代理程式的責任。</span><span class="sxs-lookup"><span data-stu-id="71444-161">Roles also help secure the pipeline and ensure that a user’s or agent’s responsibilities are assigned and authorized correctly.</span></span> <span data-ttu-id="71444-162">此外，可以將 RBAC 設定為限制存取 Azure 資產。</span><span class="sxs-lookup"><span data-stu-id="71444-162">In addition, RBAC can be set up to limit access to Azure assets.</span></span> <span data-ttu-id="71444-163">例如，可以將使用者限制為只能使用特定資源群組中的資產。</span><span class="sxs-lookup"><span data-stu-id="71444-163">For example, a user can be limited to working with only the assets in a particular resource group.</span></span>

### <a name="storage"></a><span data-ttu-id="71444-164">儲存體</span><span class="sxs-lookup"><span data-stu-id="71444-164">Storage</span></span>

<span data-ttu-id="71444-165">使用透過 Microsoft Azure Marketplace 安裝的 Jenkins [Windows Azure 儲存體外掛程式][storage-plugin]，來儲存可與其他組建和測試共用的成品組建。</span><span class="sxs-lookup"><span data-stu-id="71444-165">Use the Jenkins [Windows Azure Storage plugin][storage-plugin], which is installed from the Azure Marketplace, to store build artifacts that can be shared with other builds and tests.</span></span> <span data-ttu-id="71444-166">必須先設定 Azure 儲存體帳戶，該 Jenkins 作業才能使用此外掛程式。</span><span class="sxs-lookup"><span data-stu-id="71444-166">An Azure Storage account must be configured before this plugin can be used by the Jenkins jobs.</span></span>

### <a name="jenkins-azure-plugins"></a><span data-ttu-id="71444-167">Jenkins Azure 外掛程式</span><span class="sxs-lookup"><span data-stu-id="71444-167">Jenkins Azure plugins</span></span>

<span data-ttu-id="71444-168">在 Azure 上的 Jenkins 解決方案範本會安裝數個 Azure 外掛程式。</span><span class="sxs-lookup"><span data-stu-id="71444-168">The solution template for Jenkins on Azure installs several Azure plugins.</span></span> <span data-ttu-id="71444-169">Azure 的 DevOps 小組會建置並維護該解決方案範本以及下列外掛程式，這些會與 Microsoft Azure Marketplace 中的其他 Jenkins 供應項目與內部部署上設定的任何 Jenkins 主機搭配使用：</span><span class="sxs-lookup"><span data-stu-id="71444-169">The Azure DevOps Team builds and maintains the solution template and the following plugins, which work with other Jenkins offerings in Azure Marketplace as well as any Jenkins master set up on premises:</span></span>

-   <span data-ttu-id="71444-170">[Azure AD 外掛程式][configure-azure-ad]可讓 Jenkins 伺服器支援對以 Azure AD 為基礎的使用者的 SSO。</span><span class="sxs-lookup"><span data-stu-id="71444-170">[Azure AD plugin][configure-azure-ad] allows the Jenkins server to support SSO for users based on Azure AD.</span></span>

-   <span data-ttu-id="71444-171">[Azure 虛擬機器代理程式][configure-agent]外掛程式會使用 Azure Resource Manager 範本，以在 Azure 虛擬機器中建立 Jenkins 代理程式。</span><span class="sxs-lookup"><span data-stu-id="71444-171">[Azure VM Agents][configure-agent] plugin uses an Azure Resource Manager template to create Jenkins agents in Azure virtual machines.</span></span>

-   <span data-ttu-id="71444-172">[Azure 認證][configure-credential]外掛程式可讓您將 Azure 服務主體認證儲存在 Jenkins 中。</span><span class="sxs-lookup"><span data-stu-id="71444-172">[Azure Credentials][configure-credential] plugin allows you to store Azure service principal credentials in Jenkins.</span></span>

-   <span data-ttu-id="71444-173">[Windows Azure 儲存體外掛程式][configure-storage]會將組建成品上傳至 [Azure Blob 儲存體][blob]，或從其中下載組建相依性。</span><span class="sxs-lookup"><span data-stu-id="71444-173">[Windows Azure Storage plugin][configure-storage] uploads build artifacts to, or downloads build dependencies from, [Azure Blob storage][blob].</span></span>

<span data-ttu-id="71444-174">我們也建議您檢閱可與 Azure 資源搭配使用的所有可用 Azure 外掛程式的清單，該清單仍會不斷新增。</span><span class="sxs-lookup"><span data-stu-id="71444-174">We also recommend reviewing the growing list of all available Azure plugins that work with Azure resources.</span></span> <span data-ttu-id="71444-175">若要查看所有最新的清單，請造訪 [Jenkins 外掛程式索引][index]並搜尋 Azure。</span><span class="sxs-lookup"><span data-stu-id="71444-175">To see all the latest list, visit [Jenkins Plugin Index][index] and search for Azure.</span></span> <span data-ttu-id="71444-176">例如，下列外掛程式可供部署：</span><span class="sxs-lookup"><span data-stu-id="71444-176">For example, the following plugins are available for deployment:</span></span>

-   <span data-ttu-id="71444-177">[Azure 容器代理程式][container-agents]可協助您在 Jenkins 中執行容器作為代理程式。</span><span class="sxs-lookup"><span data-stu-id="71444-177">[Azure Container Agents][container-agents] helps you to run a container as an agent in Jenkins.</span></span>

-   <span data-ttu-id="71444-178">[Kubernetes 連續部署](https://aka.ms/azjenkinsk8s)會將資源設定部署至 Kubernetes 叢集。</span><span class="sxs-lookup"><span data-stu-id="71444-178">[Kubernetes Continuous Deploy](https://aka.ms/azjenkinsk8s) deploys resource configurations to a Kubernetes cluster.</span></span>

-   <span data-ttu-id="71444-179">[Azure Container Service][acs] 會將設定部署至具 Kubernetes 的 Azure Container Service、具 Marathon 的 DC/OS 或 Docker Swarm。</span><span class="sxs-lookup"><span data-stu-id="71444-179">[Azure Container Service][acs] deploys configurations to Azure Container Service with Kubernetes, DC/OS with Marathon, or Docker Swarm.</span></span>

-   <span data-ttu-id="71444-180">[Azure Functions][functions] 會將您的專案部署至 Azure Functions。</span><span class="sxs-lookup"><span data-stu-id="71444-180">[Azure Functions][functions] deploys your project to Azure Function.</span></span>

-   <span data-ttu-id="71444-181">[Azure App Service][app-service] 會部署至 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="71444-181">[Azure App Service][app-service] deploys to Azure App Service.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="71444-182">延展性考量</span><span class="sxs-lookup"><span data-stu-id="71444-182">Scalability considerations</span></span>

<span data-ttu-id="71444-183">Jenkins 的大小可以調整以支援超大型工作負載。</span><span class="sxs-lookup"><span data-stu-id="71444-183">Jenkins can scale to support very large workloads.</span></span> <span data-ttu-id="71444-184">對於彈性組建，請勿在 Jenkins 主要伺服器上執行組建。</span><span class="sxs-lookup"><span data-stu-id="71444-184">For elastic builds, do not run builds on the Jenkins master server.</span></span> <span data-ttu-id="71444-185">而是將組建工作卸載至 Jenkins 代理程式，其可以依所需彈性地相應縮小和相應放大。</span><span class="sxs-lookup"><span data-stu-id="71444-185">Instead, offload build tasks to Jenkins agents, which can be elastically scaled in and out as need.</span></span> <span data-ttu-id="71444-186">請考慮適用於調整代理程式大小的兩個選項：</span><span class="sxs-lookup"><span data-stu-id="71444-186">Consider two options for scaling agents:</span></span>

- <span data-ttu-id="71444-187">使用 [Azure 虛擬機器代理程式][vm-agent]外掛程式，以建立在 Azure 虛擬機器中執行的 Jenkins 代理程式。</span><span class="sxs-lookup"><span data-stu-id="71444-187">Use the [Azure VM Agents][vm-agent] plugin to create Jenkins agents that run in Azure VMs.</span></span> <span data-ttu-id="71444-188">此外掛程式可讓代理程式彈性地相應放大，而且可以使用不同類型的虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="71444-188">This plugin enables elastic scale-out for agents and can use distinct types of virtual machines.</span></span> <span data-ttu-id="71444-189">您可以從 Microsoft Azure Marketplace 中選取不同的基礎映像或使用自訂映像。</span><span class="sxs-lookup"><span data-stu-id="71444-189">You can select a different base image from Azure Marketplace or use a custom image.</span></span> <span data-ttu-id="71444-190">如需 Jenkins 代理程式如何調整大小的詳細資訊，請參閱 Jenkins 文件中的[調整大小的架構][scale]。</span><span class="sxs-lookup"><span data-stu-id="71444-190">For details about how the Jenkins agents scale, see [Architecting for Scale][scale] in the Jenkins documentation.</span></span>

- <span data-ttu-id="71444-191">使用 [Azure 容器代理程式][container-agents]外掛程式，以在[具 Kubernetes 的 Azure Container Service](/azure/container-service/kubernetes/) 或 [Azure 容器執行個體](/azure/container-instances/)中執行容器作為代理程式。</span><span class="sxs-lookup"><span data-stu-id="71444-191">Use the [Azure Container Agents][container-agents] plugin to run a container as an agent in either [Azure Container Service with Kubernetes](/azure/container-service/kubernetes/), or [Azure Container Instances](/azure/container-instances/).</span></span>

<span data-ttu-id="71444-192">虛擬機器的調整成本通常大於容器。</span><span class="sxs-lookup"><span data-stu-id="71444-192">Virtual machines generally cost more to scale than containers.</span></span> <span data-ttu-id="71444-193">若要將容器用於調整大小，您的組建流程必須與容器一起搭配執行。</span><span class="sxs-lookup"><span data-stu-id="71444-193">To use containers for scaling, however, your build process must run with containers.</span></span>

<span data-ttu-id="71444-194">此外，使用 Azure 儲存體來共用組建成品，其他組建代理程式可能會在管線的下一個階段中使用該成品。</span><span class="sxs-lookup"><span data-stu-id="71444-194">Also, use Azure Storage to share build artifacts that may be used in the next stage of the pipeline by other build agents.</span></span>

### <a name="scaling-the-jenkins-server"></a><span data-ttu-id="71444-195">調整 Jenkins 伺服器的大小</span><span class="sxs-lookup"><span data-stu-id="71444-195">Scaling the Jenkins server</span></span> 

<span data-ttu-id="71444-196">您可以藉由變更虛擬機器的大小來相應增加或減少 Jenkins 伺服器虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="71444-196">You can scale the Jenkins server VM up or down by changing the VM size.</span></span> <span data-ttu-id="71444-197">[在 Azure 上的 Jenkins 解決方案範本][azure-market]依預設會指定 DS2 v2 的大小 (含兩個 CPU，7 GB)。</span><span class="sxs-lookup"><span data-stu-id="71444-197">The [solution template for Jenkins on Azure][azure-market] specifies the DS2 v2 size (with two CPUs, 7 GB) by default.</span></span> <span data-ttu-id="71444-198">此大小可處理小型至中型的小組工作負載。</span><span class="sxs-lookup"><span data-stu-id="71444-198">This size handles a small to medium team workload.</span></span> <span data-ttu-id="71444-199">建立伺服器時，請透過選擇不同的選項來變更虛擬機器的大小。</span><span class="sxs-lookup"><span data-stu-id="71444-199">Change the VM size by choosing a different option when building out the server.</span></span> 

<span data-ttu-id="71444-200">請根據預期的工作負載大小選取正確的伺服器大小。</span><span class="sxs-lookup"><span data-stu-id="71444-200">Selecting the correct server size depends on the size of the expected workload.</span></span> <span data-ttu-id="71444-201">Jenkins 社群會維護[選取項目指南][selection-guide]，以協助找出最符合您需求的設定。</span><span class="sxs-lookup"><span data-stu-id="71444-201">The Jenkins community maintains a [selection guide][selection-guide] to help identify the configuration that best meets your requirements.</span></span> <span data-ttu-id="71444-202">Azure 提供許多[適用於 Linux 虛擬機器的大小][sizes-linux]來符合所有需求。</span><span class="sxs-lookup"><span data-stu-id="71444-202">Azure offers many [sizes for Linux VMs][sizes-linux] to meet any requirements.</span></span> <span data-ttu-id="71444-203">如需有關調整 Jenkins 主機大小的詳細資訊，請參考[最佳做法][best-practices]的 Jenkins 社群，其中也包括調整 Jenkins 主機大小的相關詳細資料。</span><span class="sxs-lookup"><span data-stu-id="71444-203">For more information about scaling the Jenkins master, refer to the Jenkins community of [best practices][best-practices], which also includes details about scaling Jenkins master.</span></span>


## <a name="availability-considerations"></a><span data-ttu-id="71444-204">可用性考量</span><span class="sxs-lookup"><span data-stu-id="71444-204">Availability considerations</span></span>

<span data-ttu-id="71444-205">Jenkins 伺服器內容中的可用性表示能夠復原與您工作流程關聯的任何狀態資訊 (例如測試結果、您已建立的文件庫，或其他成品)。</span><span class="sxs-lookup"><span data-stu-id="71444-205">Availability in the context of a Jenkins server means being able to recover any state information associated with your workflow, such as test results, libraries you have created, or other artifacts.</span></span> <span data-ttu-id="71444-206">若 Jenkins 伺服器當機時，必須維護重要工作流程狀態或成品以復原工作流程。</span><span class="sxs-lookup"><span data-stu-id="71444-206">Critical workflow state or artifacts must be maintained to recover the workflow if the Jenkins server goes down.</span></span> <span data-ttu-id="71444-207">若要評估您的可用性需求，請考量以下兩個計量：</span><span class="sxs-lookup"><span data-stu-id="71444-207">To assess your availability requirements, consider two common metrics:</span></span>

-   <span data-ttu-id="71444-208">復原時間目標 (RTO) 會指定在沒有 Jenkins 的情況您能運行多久的時間。</span><span class="sxs-lookup"><span data-stu-id="71444-208">Recovery Time Objective (RTO) specifies how long you can go without Jenkins.</span></span>

-   <span data-ttu-id="71444-209">復原點目標 (RPO) 表示當進行中的中斷影響 Jenkins 時，您可以負擔多少的資料遺失量。</span><span class="sxs-lookup"><span data-stu-id="71444-209">Recovery Point Objective (RPO) indicates how much data you can afford to lose if a disruption in service affects Jenkins.</span></span>

<span data-ttu-id="71444-210">實際上，RTO 和 RPO 代表備援性和備份。</span><span class="sxs-lookup"><span data-stu-id="71444-210">In practice, RTO and RPO imply redundancy and backup.</span></span> <span data-ttu-id="71444-211">可用性不是硬體復原的問題 (那是 Azure 的問題)，而是要確保您會維護 Jenkins 伺服器的狀態。</span><span class="sxs-lookup"><span data-stu-id="71444-211">Availability is not a question of hardware recovery—that is part of Azure—but rather ensuring you maintain the state of your Jenkins server.</span></span> <span data-ttu-id="71444-212">Microsoft 會提供單一虛擬機器執行個體的[服務等級協定][sla] (SLA)。</span><span class="sxs-lookup"><span data-stu-id="71444-212">Microsoft offers a [service level agreement][sla] (SLA) for single VM instances.</span></span> <span data-ttu-id="71444-213">如果這個 SLA 不符合您的執行時間需求，請確定您備妥災害復原的計畫，或考慮使用[多重主要 Jenkins 伺服器][multi-master]部署 (未涵蓋在本文件中)。</span><span class="sxs-lookup"><span data-stu-id="71444-213">If this SLA doesn't meet your uptime requirements, make sure you have a plan for disaster recovery, or consider using a [multi-master Jenkins server][multi-master] deployment (not covered in this document).</span></span>

<span data-ttu-id="71444-214">請考慮使用在部署步驟 7 的災害復原[指令碼][disaster]來建立具受控磁碟的 Azure 儲存體帳戶，以儲存 Jenkins 伺服器狀態。</span><span class="sxs-lookup"><span data-stu-id="71444-214">Consider using the disaster recovery [scripts][disaster] in step 7 of the deployment to create an Azure Storage account with managed disks to store the Jenkins server state.</span></span> <span data-ttu-id="71444-215">Jenkins 當機時，可還原至此個別儲存體帳戶中所儲存的狀態。</span><span class="sxs-lookup"><span data-stu-id="71444-215">If Jenkins goes down, it can be restored to the state stored in this separate storage account.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="71444-216">安全性考量</span><span class="sxs-lookup"><span data-stu-id="71444-216">Security considerations</span></span>

<span data-ttu-id="71444-217">因為基本 Jenkins 伺服器在基本狀態時並不安全，請使用下列方法在 Jenkins 伺服器上鎖定安全性。</span><span class="sxs-lookup"><span data-stu-id="71444-217">Use the following approaches to help lock down security on a basic Jenkins server, since in its basic state, it is not secure.</span></span>

-   <span data-ttu-id="71444-218">設定 Jenkins 伺服器的安全登入方式。</span><span class="sxs-lookup"><span data-stu-id="71444-218">Set up a secure way to log into the Jenkins server.</span></span> <span data-ttu-id="71444-219">這個架構會使用 HTTP 並具有公用 IP，但依預設 HTTP 並不安全。</span><span class="sxs-lookup"><span data-stu-id="71444-219">This architecture uses HTTP and has a public IP, but HTTP is not secure by default.</span></span> <span data-ttu-id="71444-220">請考慮在用於安全登入的 [Nginx 伺服器上設定 HTTPS][nginx]。</span><span class="sxs-lookup"><span data-stu-id="71444-220">Consider setting up [HTTPS on the Nginx server][nginx] being used for a secure logon.</span></span>

    > [!NOTE]
    > <span data-ttu-id="71444-221">將 SSL 新增至伺服器時，請建立 Jenkins 子網路的 NSG 規則以開啟連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="71444-221">When adding SSL to your server, create an NSG rule for the Jenkins subnet to open port 443.</span></span> <span data-ttu-id="71444-222">如需詳細資訊，請參閱[如何使用 Azure 入口網站開啟對虛擬機器的連接埠][port443]。</span><span class="sxs-lookup"><span data-stu-id="71444-222">For more information, see [How to open ports to a virtual machine with the Azure portal][port443].</span></span>
    > 

-   <span data-ttu-id="71444-223">請確定 Jenkins 設定可防止跨站台要求偽造 (管理 Jenkins \> 設定全域安全性)。</span><span class="sxs-lookup"><span data-stu-id="71444-223">Ensure that the Jenkins configuration prevents cross site request forgery (Manage Jenkins \> Configure Global Security).</span></span> <span data-ttu-id="71444-224">這是 Microsoft Jenkins 伺服器的預設值。</span><span class="sxs-lookup"><span data-stu-id="71444-224">This is the default for Microsoft Jenkins Server.</span></span>

-   <span data-ttu-id="71444-225">透過使用[矩陣授權策略外掛程式][matrix]來設定對 Jenkins 儀表板的唯讀存取。</span><span class="sxs-lookup"><span data-stu-id="71444-225">Configure read-only access to the Jenkins dashboard by using the [Matrix Authorization Strategy Plugin][matrix].</span></span>

-   <span data-ttu-id="71444-226">安裝 [Azure 認證][configure-credential]外掛程式，以使用 Key Vault 來處理 Azure 資產、管線中的代理程式和第三方元件中的秘密。</span><span class="sxs-lookup"><span data-stu-id="71444-226">Install the [Azure Credentials][configure-credential] plugin to use Key Vault to handle secrets for the Azure assets, the agents in the pipeline, and third-party components.</span></span>

-   <span data-ttu-id="71444-227">使用 RBAC 將服務主體的存取限制為執行工作所需的最低限度。</span><span class="sxs-lookup"><span data-stu-id="71444-227">Use RBAC to restrict the access of the service principal to the minimum required to run the jobs.</span></span> <span data-ttu-id="71444-228">這有助於限制 Rogue 作業帶來的損毀範圍。</span><span class="sxs-lookup"><span data-stu-id="71444-228">This helps limit the scope of damage from a rogue job.</span></span>

<span data-ttu-id="71444-229">Jenkins 作業通常需要祕密來存取需要授權的 Azure 服務 (例如 Azure Container Service)。</span><span class="sxs-lookup"><span data-stu-id="71444-229">Jenkins jobs often require secrets to access Azure services that require authorization, such as Azure Container Service.</span></span> <span data-ttu-id="71444-230">使用 [Key Vault][key-vault] 與 [Azure 認證外掛程式][configure-credential]來安全地管理這些祕密。</span><span class="sxs-lookup"><span data-stu-id="71444-230">Use [Key Vault][key-vault] along with the [Azure Credential plugin][configure-credential] to manage these secrets securely.</span></span> <span data-ttu-id="71444-231">使用 Key Vault 來儲存服務主體認證、密碼、權杖和其他秘密。</span><span class="sxs-lookup"><span data-stu-id="71444-231">Use Key Vault to store service principal credentials, passwords, tokens, and other secrets.</span></span>

<span data-ttu-id="71444-232">使用 [Azure 資訊安全中心][security-center]來集中檢視 Azure 資源的安全性狀態。</span><span class="sxs-lookup"><span data-stu-id="71444-232">To get a central view of the security state of your Azure resources, use [Azure Security Center][security-center].</span></span> <span data-ttu-id="71444-233">資訊安全中心會監視潛在的安全性問題，並提供全面性的部署安全性健康狀態。</span><span class="sxs-lookup"><span data-stu-id="71444-233">Security Center monitors potential security issues and provides a comprehensive picture of the security health of your deployment.</span></span> <span data-ttu-id="71444-234">資訊安全中心是依每個 Azure 訂用帳戶設定。</span><span class="sxs-lookup"><span data-stu-id="71444-234">Security Center is configured per Azure subscription.</span></span> <span data-ttu-id="71444-235">請按照 [Azure 資訊安全中心快速入門指南][quick-start]中所述的方式，來啟用安全性資料收集。</span><span class="sxs-lookup"><span data-stu-id="71444-235">Enable security data collection as described in the [Azure Security Center quick start guide][quick-start].</span></span> <span data-ttu-id="71444-236">啟用資料收集時，資訊安全性中心就會自動掃描任何該訂用帳戶建立的虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="71444-236">When data collection is enabled, Security Center automatically scans any virtual machines created under that subscription.</span></span>

<span data-ttu-id="71444-237">Jenkins 伺服器有其本身的使用者管理系統，而且 Jenkins 社群會提供最佳做法[來保護 Azure 上的 Jenkins 執行個體][secure-jenkins]。</span><span class="sxs-lookup"><span data-stu-id="71444-237">The Jenkins server has its own user management system, and the Jenkins community provides best practices for [securing a Jenkins instance on Azure][secure-jenkins].</span></span> <span data-ttu-id="71444-238">在 Azure 上的 Jenkins 的解決方案範本會實作這些最佳做法。</span><span class="sxs-lookup"><span data-stu-id="71444-238">The solution template for Jenkins on Azure implements these best practices.</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="71444-239">管理性考量</span><span class="sxs-lookup"><span data-stu-id="71444-239">Manageability considerations</span></span>

<span data-ttu-id="71444-240">使用資源群組來組織部署的 Azure 資源。</span><span class="sxs-lookup"><span data-stu-id="71444-240">Use resource groups to organize the Azure resources that are deployed.</span></span> <span data-ttu-id="71444-241">在個別的資源群組中部署生產環境和開發/測試環境，讓您可以監視每個環境的資源，並彙總依資源群組的帳單成本。</span><span class="sxs-lookup"><span data-stu-id="71444-241">Deploy production environments and development/test environments in separate resource groups, so that you can monitor each environment’s resources and roll up billing costs by resource group.</span></span> <span data-ttu-id="71444-242">您也可以刪除整組資源，這對於測試部署非常有用。</span><span class="sxs-lookup"><span data-stu-id="71444-242">You can also delete resources as a set, which is very useful for test deployments.</span></span>

<span data-ttu-id="71444-243">Azure 會提供幾個功能來[監視和診斷][monitoring-diag]整體架構。</span><span class="sxs-lookup"><span data-stu-id="71444-243">Azure provides several features for [monitoring and diagnostics][monitoring-diag] of the overall infrastructure.</span></span> <span data-ttu-id="71444-244">若要監視 CPU 使用量，這個架構會部署 Azure 監視器。</span><span class="sxs-lookup"><span data-stu-id="71444-244">To monitor CPU usage, this architecture deploys Azure Monitor.</span></span> <span data-ttu-id="71444-245">例如，如果 CPU 使用量超過 80%，您可以使用 Azure 監視器來監視 CPU 使用率並傳送通知。</span><span class="sxs-lookup"><span data-stu-id="71444-245">For example, you can use Azure Monitor to monitor CPU utilization, and send a notification if CPU usage exceeds 80 percent.</span></span> <span data-ttu-id="71444-246">(高 CPU 使用量則表示您可以相應增加 Jenkins 伺服器虛擬機器的大小。)如果虛擬機器失敗或變得無法使用，您也可以通知指定的使用者。</span><span class="sxs-lookup"><span data-stu-id="71444-246">(High CPU usage indicates that you might want to scale up the Jenkins server VM.) You can also notify a designated user if the VM fails or becomes unavailable.</span></span>

## <a name="communities"></a><span data-ttu-id="71444-247">社群</span><span class="sxs-lookup"><span data-stu-id="71444-247">Communities</span></span>

<span data-ttu-id="71444-248">社群可以回答問題並協助您設定成功的部署。</span><span class="sxs-lookup"><span data-stu-id="71444-248">Communities can answer questions and help you set up a successful deployment.</span></span> <span data-ttu-id="71444-249">請考慮下列：</span><span class="sxs-lookup"><span data-stu-id="71444-249">Consider the following:</span></span>

-   [<span data-ttu-id="71444-250">Jenkins 社群部落格</span><span class="sxs-lookup"><span data-stu-id="71444-250">Jenkins Community Blog</span></span>](https://jenkins.io/node/)
-   [<span data-ttu-id="71444-251">Azure 論壇</span><span class="sxs-lookup"><span data-stu-id="71444-251">Azure Forum</span></span>](https://azure.microsoft.com/support/forums/)
-   [<span data-ttu-id="71444-252">Stack Overflow Jenkins</span><span class="sxs-lookup"><span data-stu-id="71444-252">Stack Overflow Jenkins</span></span>](https://stackoverflow.com/tags/jenkins/info)

<span data-ttu-id="71444-253">如需來自 Jenkins 社群的最佳做法，請造訪 [Jenkins 最佳做法][jenkins-best]。</span><span class="sxs-lookup"><span data-stu-id="71444-253">For more best practices from the Jenkins community, visit [Jenkins best practices][jenkins-best].</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="71444-254">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="71444-254">Deploy the solution</span></span>

<span data-ttu-id="71444-255">若要部署這種架構，請遵循下列步驟來安裝[在 Azure 上的 Jenkins 的解決方案範本][azure-market]，然後安裝在下列步驟中設定監視和災害復原的指令碼。</span><span class="sxs-lookup"><span data-stu-id="71444-255">To deploy this architecture, follow the steps below to install the [solution template for Jenkins on Azure][azure-market], then install the scripts that set up monitoring and disaster recovery in the steps below.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="71444-256">先決條件</span><span class="sxs-lookup"><span data-stu-id="71444-256">Prerequisites</span></span>

- <span data-ttu-id="71444-257">此參考架構需要 Azure 訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="71444-257">This reference architecture requires an Azure subscription.</span></span> 
- <span data-ttu-id="71444-258">若要建立 Azure 服務主體，您必須擁有 Azure AD 租用戶的系統管理員權限，該租用戶與部署的 Jenkins 伺服器相關聯。</span><span class="sxs-lookup"><span data-stu-id="71444-258">To create an Azure service principal, you must have admin rights to the Azure AD tenant that is associated with the deployed Jenkins server.</span></span>
- <span data-ttu-id="71444-259">這些指示會假設 Jenkins 系統管理員也是 Azure 使用者，且至少擁有參與者權限。</span><span class="sxs-lookup"><span data-stu-id="71444-259">These instructions assume that the Jenkins administrator is also an Azure user with at least Contributor privileges.</span></span>

### <a name="step-1-deploy-the-jenkins-server"></a><span data-ttu-id="71444-260">步驟 1：部署 Jenkins 伺服器</span><span class="sxs-lookup"><span data-stu-id="71444-260">Step 1: Deploy the Jenkins server</span></span>

1.  <span data-ttu-id="71444-261">在 Web 瀏覽器中開啟 [Jenkins 的 Microsoft Azure Marketplace 映像][][azure-market]，然後從頁面左側選取 [立即取得]。</span><span class="sxs-lookup"><span data-stu-id="71444-261">Open the [Azure Marketplace image for Jenkins][azure-market] in your web browser and select **GET IT NOW** from the left side of the page.</span></span>

2.  <span data-ttu-id="71444-262">檢閱價格詳細資料並選取 [繼續]，然後選取 [建立] 以在 Azure 入口網站中設定 Jenkins 伺服器。</span><span class="sxs-lookup"><span data-stu-id="71444-262">Review the pricing details and select **Continue**, then select **Create** to configure the Jenkins server in the Azure portal.</span></span>

<span data-ttu-id="71444-263">如需詳細的指示，請參閱[從 Azure 入口網站在 Azure Linux 虛擬機器上建立 Jenkins 伺服器][create-jenkins]。</span><span class="sxs-lookup"><span data-stu-id="71444-263">For detailed instructions, see [Create a Jenkins server on an Azure Linux VM from the Azure portal][create-jenkins].</span></span> <span data-ttu-id="71444-264">對於此參考架構，要啟動伺服器並使用系統管理員登入來執行便已足夠。</span><span class="sxs-lookup"><span data-stu-id="71444-264">For this reference architecture, it is sufficient to get the server up and running with the admin logon.</span></span> <span data-ttu-id="71444-265">然後您可以佈建它去使用各種其他服務。</span><span class="sxs-lookup"><span data-stu-id="71444-265">Then you can provision it to use various other services.</span></span>

### <a name="step-2-set-up-sso"></a><span data-ttu-id="71444-266">步驟 2：設定 SSO</span><span class="sxs-lookup"><span data-stu-id="71444-266">Step 2: Set up SSO</span></span>

<span data-ttu-id="71444-267">此步驟是由 Jenkins 系統管理員執行，此管理員也必須有訂用帳戶 Azure AD 目錄中的使用者帳戶，而且必須獲指派參與者角色。</span><span class="sxs-lookup"><span data-stu-id="71444-267">The step is run by the Jenkins administrator, who must also have a user account in the subscription’s Azure AD directory and must be assigned the Contributor role.</span></span>

<span data-ttu-id="71444-268">在 Jenkin 伺服器中從 Jenkins 更新中心使用 [Azure AD 外掛程式][configure-azure-ad]並遵循指示來設定 SSO。</span><span class="sxs-lookup"><span data-stu-id="71444-268">Use the [Azure AD Plugin][configure-azure-ad] from the Jenkins Update Center in the Jenkins server and follow the instructions to set up SSO.</span></span>

### <a name="step-3-provision-jenkins-server-with-azure-vm-agent-plugin"></a><span data-ttu-id="71444-269">步驟 3：使用 Azure 虛擬機器代理程式的外掛程式來佈建 Jenkins 伺服器</span><span class="sxs-lookup"><span data-stu-id="71444-269">Step 3: Provision Jenkins server with Azure VM Agent plugin</span></span>

<span data-ttu-id="71444-270">Jenkins 系統管理員會執行此步驟以設定已安裝的 Azure 虛擬機器代理程式外掛程式。</span><span class="sxs-lookup"><span data-stu-id="71444-270">The step is run by the Jenkins administrator to set up the Azure VM Agent plugin, which is already installed.</span></span>

<span data-ttu-id="71444-271">[請遵循這些步驟來設定該外掛程式][configure-agent]。</span><span class="sxs-lookup"><span data-stu-id="71444-271">[Follow these steps to configure the plugin][configure-agent].</span></span> <span data-ttu-id="71444-272">對於設定外掛程式的服務主體之相關教學課程中，請參閱[調整 Jenkins 部署的大小以符合與 Azure 虛擬機器代理程式的需求][scale-agent]。</span><span class="sxs-lookup"><span data-stu-id="71444-272">For a tutorial about setting up service principals for the plugin, see [Scale your  Jenkins deployments to meet demand with Azure VM agents][scale-agent].</span></span>

### <a name="step-4-provision-jenkins-server-with-azure-storage"></a><span data-ttu-id="71444-273">步驟 4：佈建具 Azure 儲存體的 Jenkins 伺服器</span><span class="sxs-lookup"><span data-stu-id="71444-273">Step 4: Provision Jenkins server with Azure Storage</span></span>

<span data-ttu-id="71444-274">Jenkins 系統管理員會執行此步驟，該管理員會設定已安裝的 Windows Azure 儲存體外掛程式。</span><span class="sxs-lookup"><span data-stu-id="71444-274">The step is run by the Jenkins administrator, who sets up the Windows Azure Storage Plugin, which is already installed.</span></span>

<span data-ttu-id="71444-275">[請遵循這些步驟來設定該外掛程式][configure-storage]。</span><span class="sxs-lookup"><span data-stu-id="71444-275">[Follow these steps to configure the plugin][configure-storage].</span></span>

### <a name="step-5-provision-jenkins-server-with-azure-credential-plugin"></a><span data-ttu-id="71444-276">步驟 5：佈建具 Azure 認證外掛程式的 Jenkins 伺服器</span><span class="sxs-lookup"><span data-stu-id="71444-276">Step 5: Provision Jenkins server with Azure Credential plugin</span></span>

<span data-ttu-id="71444-277">Jenkins 系統管理員會執行此步驟以設定已安裝的 Azure 認證外掛程式。</span><span class="sxs-lookup"><span data-stu-id="71444-277">The step is run by the Jenkins administrator to set up the Azure Credential plugin, which is already installed.</span></span>

<span data-ttu-id="71444-278">[請遵循這些步驟來設定該外掛程式][configure-credential]。</span><span class="sxs-lookup"><span data-stu-id="71444-278">[Follow these steps to configure the plugin][configure-credential].</span></span>

### <a name="step-6-provision-jenkins-server-for-monitoring-by-the-azure-monitor-service"></a><span data-ttu-id="71444-279">步驟 6：佈建 Jenkins 伺服器以供 Azure 監視器服務進行監視使用</span><span class="sxs-lookup"><span data-stu-id="71444-279">Step 6: Provision Jenkins server for monitoring by the Azure Monitor Service</span></span>

<span data-ttu-id="71444-280">若要設定 Jenkins 伺服器的監視，請依照[在 Azure 監視器中為 Azure 服務建立度量警示][create-metric]中的指示。</span><span class="sxs-lookup"><span data-stu-id="71444-280">To set up monitoring for your Jenkins server, follow the instructions in [Create metric alerts in Azure Monitor for Azure services][create-metric].</span></span>

### <a name="step-7-provision-jenkins-server-with-managed-disks-for-disaster-recovery"></a><span data-ttu-id="71444-281">步驟 7：佈建具災害復原的受控磁碟之 Jenkins 伺服器</span><span class="sxs-lookup"><span data-stu-id="71444-281">Step 7: Provision Jenkins server with Managed Disks for disaster recovery</span></span>

<span data-ttu-id="71444-282">Microsoft Jenkins 產品群組已建立災害復原指令碼，其會建置用於儲存 Jenkins 狀態的受控磁碟。</span><span class="sxs-lookup"><span data-stu-id="71444-282">The Microsoft Jenkins product group has created disaster recovery scripts that build a managed disk used to save the Jenkins state.</span></span> <span data-ttu-id="71444-283">如果伺服器當機時，該指令碼可以還原至其最新狀態。</span><span class="sxs-lookup"><span data-stu-id="71444-283">If the server goes down, it can be restored to its latest state.</span></span>

<span data-ttu-id="71444-284">從 [GitHub][disaster] 下載並執行災害復原指令碼。</span><span class="sxs-lookup"><span data-stu-id="71444-284">Download and run the disaster recovery scripts from [GitHub][disaster].</span></span>

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

---
title: 容器型工作負載的 CI/CD 管線
description: 經過證明的案例，可以為使用 Jenkins、Azure Container Registry、Azure Kubernetes Service、Cosmos DB 及 Grafana 的 Node.js Web 應用程式，建置 DevOps 管線。
author: iainfoulds
ms.date: 07/05/2018
ms.openlocfilehash: dceb4ad3c34ec43a54d802772f5817cacdd3929c
ms.sourcegitcommit: 8b5fc0d0d735793b87677610b747f54301dcb014
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2018
ms.locfileid: "39334210"
---
# <a name="cicd-pipeline-for-container-based-workloads"></a><span data-ttu-id="27d33-103">容器型工作負載的 CI/CD 管線</span><span class="sxs-lookup"><span data-stu-id="27d33-103">CI/CD pipeline for container-based workloads</span></span>

<span data-ttu-id="27d33-104">此範例案例適用於想要藉由使用容器和 DevOps 工作流程讓應用程式開發現代化的企業。</span><span class="sxs-lookup"><span data-stu-id="27d33-104">This example scenario is applicable to businesses that want to modernize application development by using containers and DevOps workflows.</span></span> <span data-ttu-id="27d33-105">在此案例中，Node.js Web 應用程式是由 Jenkins 建置，並且部署至 Azure Container Registry 和 Azure Kubernetes Service。</span><span class="sxs-lookup"><span data-stu-id="27d33-105">In this scenario, a Node.js web app is built and deployed by Jenkins into an Azure Container Registry and Azure Kubernetes Service.</span></span> <span data-ttu-id="27d33-106">針對全域分散式資料庫層，使用 Azure Cosmos DB。</span><span class="sxs-lookup"><span data-stu-id="27d33-106">For a globally distributed database tier, Azure Cosmos DB is used.</span></span> <span data-ttu-id="27d33-107">為了監視應用程式效能及進行疑難排解，Azure 監視器與 Grafana 執行個體和儀表板整合。</span><span class="sxs-lookup"><span data-stu-id="27d33-107">To monitor and troubleshoot application performance, Azure Monitor integrates with a Grafana instance and dashboard.</span></span>

<span data-ttu-id="27d33-108">範例應用程式案例包括提供自動化開發環境、驗證新的程式碼認可，以及將新部署推送至預備或生產環境。</span><span class="sxs-lookup"><span data-stu-id="27d33-108">Example application scenarios include providing an automated development environment, validating new code commits, and pushing new deployments into staging or production environments.</span></span> <span data-ttu-id="27d33-109">在過去，企業必須以手動方式建置及編譯應用程式和更新，然後維護大型、整合式程式碼基底。</span><span class="sxs-lookup"><span data-stu-id="27d33-109">Traditionally, businesses had to manually build and compile applications and updates, and maintain a large, monolithic code base.</span></span> <span data-ttu-id="27d33-110">透過使用持續整合 (CI) 和持續部署 (CD) 的應用程式開發新式方法，您可以更快速地建置、測試及部署服務。</span><span class="sxs-lookup"><span data-stu-id="27d33-110">With a modern approach to application development that uses continuous integration (CI) and continuous deployment (CD), you can more quickly build, test, and deploy services.</span></span> <span data-ttu-id="27d33-111">這個新式方法可讓您更快地將應用程式和更新發行給客戶，並且以更靈活的方式回應日新月異的商業需求。</span><span class="sxs-lookup"><span data-stu-id="27d33-111">This modern approach lets you release applications and updates to your customers faster, and respond to changing business demands in a more agile manner.</span></span>

<span data-ttu-id="27d33-112">藉由使用 Azure 服務 (例如 Azure Kubernetes Service、Container Registry 和 Cosmos DB)，公司可以使用最新的應用程式開發技術和工具，來簡化實作高可用性的程序。</span><span class="sxs-lookup"><span data-stu-id="27d33-112">By using Azure services such as Azure Kubernetes Service, Container Registry, and Cosmos DB, companies can use the latest in application development techniques and tools to simplify the process of implementing high availability.</span></span>

## <a name="related-use-cases"></a><span data-ttu-id="27d33-113">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="27d33-113">Related use cases</span></span>

<span data-ttu-id="27d33-114">請針對下列使用案例考慮此案例：</span><span class="sxs-lookup"><span data-stu-id="27d33-114">Consider this scenario for the following use cases:</span></span>

* <span data-ttu-id="27d33-115">將應用程式開發做法現代化為微服務、容器型方法。</span><span class="sxs-lookup"><span data-stu-id="27d33-115">Modernizing application development practices to a microservices, container-based approach.</span></span>
* <span data-ttu-id="27d33-116">加速應用程式開發和部署生命週期。</span><span class="sxs-lookup"><span data-stu-id="27d33-116">Speeding up application development and deployment lifecycles.</span></span>
* <span data-ttu-id="27d33-117">自動化測試部署或進行驗證的接受環境。</span><span class="sxs-lookup"><span data-stu-id="27d33-117">Automating deployments to test or acceptance environments for validation.</span></span>

## <a name="architecture"></a><span data-ttu-id="27d33-118">架構</span><span class="sxs-lookup"><span data-stu-id="27d33-118">Architecture</span></span>

![DevOps 案例 (使用 Jenkins、Azure Container Registry 及 Azure Kubernetes Service) 中所牽涉到 Azure 元件的架構概觀][architecture]

<span data-ttu-id="27d33-120">此案例涵蓋 Node.js Web 應用程式和資料庫後端的 DevOps 管線。</span><span class="sxs-lookup"><span data-stu-id="27d33-120">This scenario covers a DevOps pipeline for a Node.js web application and database backend.</span></span> <span data-ttu-id="27d33-121">整個案例的資料流程如下所示：</span><span class="sxs-lookup"><span data-stu-id="27d33-121">The data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="27d33-122">開發人員會對 Node.js Web 應用程式原始程式碼進行變更。</span><span class="sxs-lookup"><span data-stu-id="27d33-122">A developer makes changes to the Node.js web application source code.</span></span>
2. <span data-ttu-id="27d33-123">程式碼變更會認可至原始檔控制存放庫，例如 GitHub。</span><span class="sxs-lookup"><span data-stu-id="27d33-123">The code change is committed to a source control respository, such as GitHub.</span></span>
3. <span data-ttu-id="27d33-124">為了開始持續整合 (CI) 程序，GitHub Webhook 會觸發 Jenkins 專案建置。</span><span class="sxs-lookup"><span data-stu-id="27d33-124">To start the continuous integration (CI) process, a GitHub webhook triggers a Jenkins project build.</span></span>
4. <span data-ttu-id="27d33-125">Jenkins 建置作業會使用 Azure Kubernetes Service 中的動態建置代理程式，來執行容器建置程序。</span><span class="sxs-lookup"><span data-stu-id="27d33-125">The Jenkins build job uses a dynamic build agent in Azure Kubernetes Service to perform a container build process.</span></span>
5. <span data-ttu-id="27d33-126">容器映像會根據原始檔控制中的程式碼建立，接著推送至 Azure Container Registry。</span><span class="sxs-lookup"><span data-stu-id="27d33-126">A container image is created from the code in source control, and is then pushed to an Azure Container Registry.</span></span>
6. <span data-ttu-id="27d33-127">透過持續部署 (CD)，Jenkins 會將此更新的容器映像部署到 Kubernetes 叢集。</span><span class="sxs-lookup"><span data-stu-id="27d33-127">Through continuous deployment (CD), Jenkins deploys this updated container image to the Kubernetes cluster.</span></span>
7. <span data-ttu-id="27d33-128">Node.js Web 應用程式會使用 Azure Cosmos DB 作為它的後端。</span><span class="sxs-lookup"><span data-stu-id="27d33-128">The Node.js web application uses Azure Cosmos DB as it's backend.</span></span> <span data-ttu-id="27d33-129">Cosmos DB 和 Azure Kubernetes Service 都會向 Azure 監視器報告計量。</span><span class="sxs-lookup"><span data-stu-id="27d33-129">Both Cosmos DB and Azure Kubernetes Service report metrics to Azure Monitor.</span></span>
8. <span data-ttu-id="27d33-130">Grafana 執行個體根據 Azure 監視器的資料，提供應用程式效能的視覺效果儀表板。</span><span class="sxs-lookup"><span data-stu-id="27d33-130">A Grafana instance provides visual dashboards of the application performance based on the data from Azure Monitor.</span></span>

### <a name="components"></a><span data-ttu-id="27d33-131">元件</span><span class="sxs-lookup"><span data-stu-id="27d33-131">Components</span></span>

* <span data-ttu-id="27d33-132">[Jenkins][jenkins] 是開放原始碼 Automation 伺服程式，可與 Azure 服務整合，以進行持續整合 (CI) 及持續部署 (CD)。</span><span class="sxs-lookup"><span data-stu-id="27d33-132">[Jenkins][jenkins] is an open-source automation server that can integrate with Azure services to enable continuous integration (CI) and continuous deployment (CD).</span></span> <span data-ttu-id="27d33-133">在此案例中，Jenkins 會根據對於原始檔控制的認可，協調新容器映像的建立，將這些映像推送至 Azure Container Registry，然後在 Azure Kubernetes Service 中更新應用程式執行個體。</span><span class="sxs-lookup"><span data-stu-id="27d33-133">In this scenario, Jenkins orchestrates the creation of new container images based on commits to source control, pushes those images to Azure Container Registry, then updates application instances in Azure Kubernetes Service.</span></span>
* <span data-ttu-id="27d33-134">[Azure Linux 虛擬機器][azurevm-docs]是用來執行 Jenkins 和 Grafana 執行個體。</span><span class="sxs-lookup"><span data-stu-id="27d33-134">[Azure Linux Virtual Machines][azurevm-docs] are used to run the Jenkins and Grafana instances.</span></span>
* <span data-ttu-id="27d33-135">[Azure Container Registry][azureacr-docs] 會儲存及管理 Azure Kubernetes Service 叢集所使用的容器映像。</span><span class="sxs-lookup"><span data-stu-id="27d33-135">[Azure Container Registry][azureacr-docs] stores and manages container images that are used by the Azure Kubernetes Service cluster.</span></span> <span data-ttu-id="27d33-136">映像會安全地儲存，並且可以由 Azure 平台複寫到其他區域來加快部署速度。</span><span class="sxs-lookup"><span data-stu-id="27d33-136">Images are securely stored, and can replicated to other regions by the Azure platform to speed up deployment times.</span></span>
* <span data-ttu-id="27d33-137">[Azure Kubernetes Service][azureaks-docs] 是受控 Kubernetes 平台，讓您不需要具備容器協調流程專業知識，也可以部署及管理容器化應用程式。</span><span class="sxs-lookup"><span data-stu-id="27d33-137">[Azure Kubernetes Service][azureaks-docs] is a managed Kubernetes platform that lets you deploy and manage containerized applications without container orchestration expertise.</span></span> <span data-ttu-id="27d33-138">以主控的 Kubernetes 服務形式，Azure 會為您處理像是健康狀態監視和維護等重要工作。</span><span class="sxs-lookup"><span data-stu-id="27d33-138">As a hosted Kubernetes service, Azure handles critical tasks like health monitoring and maintenance for you.</span></span>
* <span data-ttu-id="27d33-139">[Azure Cosmos DB] [azurecosmosdb-docs] 是全域分散式、多模型資料庫，可讓您從各種不同的資料庫和一致性模型中，選擇符合您需求的項目。</span><span class="sxs-lookup"><span data-stu-id="27d33-139">[Azure Cosmos DB][azurecosmosdb-docs] is a globally distributed, multi-model database that allows you to choose from various database and consistency models to suit your needs.</span></span> <span data-ttu-id="27d33-140">使用 Cosmos DB，您的資料可以全域複寫，不需要部署及設定叢集管理或複寫元件。</span><span class="sxs-lookup"><span data-stu-id="27d33-140">With Cosmos DB, your data can be globally replicated, and there is no cluster management or replication components to deploy and configure.</span></span>
* <span data-ttu-id="27d33-141">[Azure 監視器][azuremonitor-docs]有助於追蹤效能、維護安全性及找出趨勢。</span><span class="sxs-lookup"><span data-stu-id="27d33-141">[Azure Monitor][azuremonitor-docs] helps you track performance, maintain security, and identify trends.</span></span> <span data-ttu-id="27d33-142">監視器所取得的計量可以由其他資源和工具使用，例如 Grafana。</span><span class="sxs-lookup"><span data-stu-id="27d33-142">Metrics obtained by Monitor can be used by other resources and tools, such as Grafana.</span></span>
* <span data-ttu-id="27d33-143">[Grafana][grafana] 是開放原始碼解決方案，可以查詢、視覺化、警示及了解計量。</span><span class="sxs-lookup"><span data-stu-id="27d33-143">[Grafana][grafana] is an open-source solution to query, visualize, alert, and understand metrics.</span></span> <span data-ttu-id="27d33-144">Azure 監視器的資料來源外掛程式可讓 Grafana 建立視覺效果儀表板，來監視在 Azure Kubernetes Service 中執行且使用 Cosmos DB 的應用程式效能。</span><span class="sxs-lookup"><span data-stu-id="27d33-144">A data source plugin for Azure Monitor allows Grafana to create visual dashboards to monitor the performance of your applications running in Azure Kubernetes Service and using Cosmos DB.</span></span>

### <a name="alternatives"></a><span data-ttu-id="27d33-145">替代項目</span><span class="sxs-lookup"><span data-stu-id="27d33-145">Alternatives</span></span>

* <span data-ttu-id="27d33-146">[Visual Studio Team Services][vsts] 和 Team Foundation Server 會協助您實作任何應用程式的持續整合 (CI)、測試及部署 (CD) 管線。</span><span class="sxs-lookup"><span data-stu-id="27d33-146">[Visual Studio Team Services][vsts] and Team Foundation Server help you implement a continuous integration (CI), test, and deployment (CD) pipeline for any app.</span></span>
* <span data-ttu-id="27d33-147">如果您想要更多叢集控制權，[Kubernetes][kubernetes] 可以直接在 Azure 虛擬機器上執行，不用透過受控服務。</span><span class="sxs-lookup"><span data-stu-id="27d33-147">[Kubernetes][kubernetes] can be run directly on Azure VMs instead of via a managed service if you would like more control over the cluster.</span></span>
* <span data-ttu-id="27d33-148">[Service Fabric][service-fabric] 是另一個可以取代 AKS 的替代容器協調器。</span><span class="sxs-lookup"><span data-stu-id="27d33-148">[Service Fabric][service-fabric] is another alternate container orchestrator that can replace AKS.</span></span>

## <a name="considerations"></a><span data-ttu-id="27d33-149">考量</span><span class="sxs-lookup"><span data-stu-id="27d33-149">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="27d33-150">可用性</span><span class="sxs-lookup"><span data-stu-id="27d33-150">Availability</span></span>

<span data-ttu-id="27d33-151">為了監視您的應用程式效能並且報告問題，此案例將 Azure 監視器與 Grafana 合併到視覺效果儀表板。</span><span class="sxs-lookup"><span data-stu-id="27d33-151">To monitor your application performance and report on issues, this scenario combines Azure Monitor with Grafana for visual dashboards.</span></span> <span data-ttu-id="27d33-152">這些工具可讓您監視可能需要程式碼更新 (後續可以使用 CI/CD 管線部署) 的效能問題，並且進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="27d33-152">These tools let you monitor and troubleshoot performance issues that may require code updates, which can all then be deployed with the CI/CD pipeline.</span></span>

<span data-ttu-id="27d33-153">負載平衡器是 Azure Kubernetes Service 叢集的一部分，它會將應用程式流量散佈到執行應用程式的一或多個容器 (Pod)。</span><span class="sxs-lookup"><span data-stu-id="27d33-153">As part of the Azure Kubernetes Service cluster, a load balancer distributes application traffic to one or more containers (pods) that run your application.</span></span> <span data-ttu-id="27d33-154">在 Kubernetes 中執行容器化應用程式的這個方法，會為您的客戶提供高可用性基礎結構。</span><span class="sxs-lookup"><span data-stu-id="27d33-154">This approach to running containerized applications in Kubernetes provides a highly-available infrastructure for your customers.</span></span>

<span data-ttu-id="27d33-155">如需其他可用性主題，請參閱架構中心的[可用性檢查清單][availability]。</span><span class="sxs-lookup"><span data-stu-id="27d33-155">For other availability topics, see the [availability checklist][availability] available in the architecure center.</span></span>

### <a name="scalability"></a><span data-ttu-id="27d33-156">延展性</span><span class="sxs-lookup"><span data-stu-id="27d33-156">Scalability</span></span>

<span data-ttu-id="27d33-157">Azure Kubernetes Service 可讓您調整叢集節點數目，以符合您的應用程式需求。</span><span class="sxs-lookup"><span data-stu-id="27d33-157">Azure Kubernetes Service lets you scale the number of cluster nodes to meet the demands of your applications.</span></span> <span data-ttu-id="27d33-158">當您的應用程式增加時，您可以相應放大執行服務的 Kubernetes 節點數目。</span><span class="sxs-lookup"><span data-stu-id="27d33-158">As your application increases, you can scale out the number of Kubernetes nodes that run your service.</span></span>

<span data-ttu-id="27d33-159">應用程式資料會儲存在 Azure Cosmos DB，這是可以全域調整的全域分散式、多模型資料庫。</span><span class="sxs-lookup"><span data-stu-id="27d33-159">Application data is stored in Azure Cosmos DB, a globally distributed, multi-model database that can scale globally.</span></span> <span data-ttu-id="27d33-160">Cosmos DB 會將調整基礎結構的需求抽象化，如同使用傳統資料庫元件一般，您可以選擇全域複寫您的 Cosmos DB 以符合客戶需求。</span><span class="sxs-lookup"><span data-stu-id="27d33-160">Cosmos DB abstracts the need to scale your  infrastructure as with traditional database components, and you can choose to replicate your Cosmos DB globally to meet the demands of your customers.</span></span>

<span data-ttu-id="27d33-161">如需其他延展性主題，請參閱架構中心的[延展性檢查清單][scalability]。</span><span class="sxs-lookup"><span data-stu-id="27d33-161">For other scalability topics, see the [scalability checklist][scalability] available in the architecure center.</span></span>

### <a name="security"></a><span data-ttu-id="27d33-162">安全性</span><span class="sxs-lookup"><span data-stu-id="27d33-162">Security</span></span>

<span data-ttu-id="27d33-163">為了將攻擊量降至最低，此案例不會透過 HTTP 公開 Jenkins 虛擬機器執行個體。</span><span class="sxs-lookup"><span data-stu-id="27d33-163">To minimize the attack footprint, this scenarios does not expose the Jenkins VM instance over HTTP.</span></span> <span data-ttu-id="27d33-164">針對需要您與 Jenkins 互動的任何管理工作，您使用 SSH 通道從本機機器建立安全的遠端連線。</span><span class="sxs-lookup"><span data-stu-id="27d33-164">For any management tasks that require you to interact with Jenkins, you create a secure remote connection using an SSH tunnel from your local machine.</span></span> <span data-ttu-id="27d33-165">Jenkins 和 Grafana 虛擬機器執行個體僅允許 SSH 公開金鑰驗證。</span><span class="sxs-lookup"><span data-stu-id="27d33-165">Only SSH public key authentication is allowed for the Jenkins and Grafana VM instances.</span></span> <span data-ttu-id="27d33-166">已停用密碼型登入。</span><span class="sxs-lookup"><span data-stu-id="27d33-166">Password-based logins are disabled.</span></span> <span data-ttu-id="27d33-167">如需詳細資訊，請參閱[在 Azure 上執行 Jenkins 伺服器](../../reference-architectures/jenkins/index.md)。</span><span class="sxs-lookup"><span data-stu-id="27d33-167">For more information, see [Run a Jenkins server on Azure](../../reference-architectures/jenkins/index.md).</span></span>

<span data-ttu-id="27d33-168">針對區隔認證和權限，此案例會使用專用的 Azure Active Directory (AD) 服務主體。</span><span class="sxs-lookup"><span data-stu-id="27d33-168">For separation of credentials and permissions, this scenario uses a dedicated Azure Active Directory (AD) service principal.</span></span> <span data-ttu-id="27d33-169">此服務主體的認證會在 Jenkins 中儲存為安全認證物件，因此它們不會直接公開，在指令碼或建置管線內也看不見。</span><span class="sxs-lookup"><span data-stu-id="27d33-169">The credentials for this service principal are stored as a secure credential object in Jenkins so that they are not directly exposed and visible within scripts or the build pipeline.</span></span>

<span data-ttu-id="27d33-170">如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。</span><span class="sxs-lookup"><span data-stu-id="27d33-170">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="27d33-171">災害復原</span><span class="sxs-lookup"><span data-stu-id="27d33-171">Resiliency</span></span>

<span data-ttu-id="27d33-172">此案例會針對您的應用程式使用 Azure Kubernetes Service。</span><span class="sxs-lookup"><span data-stu-id="27d33-172">This scenario uses Azure Kubernetes Service for your application.</span></span> <span data-ttu-id="27d33-173">復原元件內建於 Kubernetes，這些元件會監視及重新啟動有問題的容器 (Pod)。</span><span class="sxs-lookup"><span data-stu-id="27d33-173">Built into Kubernetes are resiliency components that monitor and restart the containers (pods) if there is an issue.</span></span> <span data-ttu-id="27d33-174">您的應用程式與多個執行中 Kubernetes 節點合併，可以容許 Pod 或節點無法使用。</span><span class="sxs-lookup"><span data-stu-id="27d33-174">Combined with running multiple Kubernetes nodes, your application can tolerate a pod or node being unavailable.</span></span>

<span data-ttu-id="27d33-175">如需設計彈性解決方案的一般指引，請參閱[為 Azure 設計有彈性的應用程式][resiliency]。</span><span class="sxs-lookup"><span data-stu-id="27d33-175">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="27d33-176">部署案例</span><span class="sxs-lookup"><span data-stu-id="27d33-176">Deploy the scenario</span></span>

<span data-ttu-id="27d33-177">**必要條件。**</span><span class="sxs-lookup"><span data-stu-id="27d33-177">**Prerequisites.**</span></span>

* <span data-ttu-id="27d33-178">您必須具有現有的 Azure 帳戶。</span><span class="sxs-lookup"><span data-stu-id="27d33-178">You must have an existing Azure account.</span></span> <span data-ttu-id="27d33-179">如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。</span><span class="sxs-lookup"><span data-stu-id="27d33-179">If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.</span></span>
* <span data-ttu-id="27d33-180">您需要 SSH 公開金鑰組。</span><span class="sxs-lookup"><span data-stu-id="27d33-180">You need an SSH public key pair.</span></span> <span data-ttu-id="27d33-181">如需如何建立公開金鑰組的步驟，請參閱[為 Linux 虛擬機器建立和使用 SSH 金鑰組][sshkeydocs]。</span><span class="sxs-lookup"><span data-stu-id="27d33-181">For steps on how to create a public key pair, see [Create and use an SSH key pair for Linux VMs][sshkeydocs].</span></span>
* <span data-ttu-id="27d33-182">您需要 Azure Active Directory (AD) 服務主體以便驗證服務和資源。</span><span class="sxs-lookup"><span data-stu-id="27d33-182">You need an Azure Active Directory (AD) service principal for the authentication of service and resources.</span></span> <span data-ttu-id="27d33-183">您可以視需要使用 [az ad sp create-for-rbac][createsp] 來建立服務主體</span><span class="sxs-lookup"><span data-stu-id="27d33-183">If needed, you can create a service principal with [az ad sp create-for-rbac][createsp]</span></span>

    ```azurecli-interactive
    az ad sp create-for-rbac --name myDevOpsScenario
    ```

    <span data-ttu-id="27d33-184">請記下此命令輸出中的 appId 和 password。</span><span class="sxs-lookup"><span data-stu-id="27d33-184">Make a note of the *appId* and *password* in the output from this command.</span></span> <span data-ttu-id="27d33-185">當您部署案例時，將這些值提供給範本。</span><span class="sxs-lookup"><span data-stu-id="27d33-185">You provide these values to the template when you deploy the scenario.</span></span>

<span data-ttu-id="27d33-186">若要使用 Azure Resource Manager 範本部署此案例，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="27d33-186">To deploy this scenario with an Azure Resource Manager template, perform the following steps.</span></span>

1. <span data-ttu-id="27d33-187">按一下 [部署至 Azure] 按鈕：</span><span class="sxs-lookup"><span data-stu-id="27d33-187">Click the **Deploy to Azure** button:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fapps%2Fdevops-with-aks%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. <span data-ttu-id="27d33-188">等待 Azure 入口網站中的範本部署開啟，然後完成下列步驟：</span><span class="sxs-lookup"><span data-stu-id="27d33-188">Wait for the template deployment to open in the Azure portal, then complete the following steps:</span></span>
   * <span data-ttu-id="27d33-189">選擇 [新建] 資源群組，然後在文字方塊中提供名稱，例如 myAKSDevOpsScenario。</span><span class="sxs-lookup"><span data-stu-id="27d33-189">Choose to **Create new** resource group, then provide a name such as *myAKSDevOpsScenario* in the text box.</span></span>
   * <span data-ttu-id="27d33-190">從 [位置] 下拉式方塊選取區域。</span><span class="sxs-lookup"><span data-stu-id="27d33-190">Select a region from the **Location** drop-down box.</span></span>
   * <span data-ttu-id="27d33-191">輸入來自 `az ad sp create-for-rbac` 命令的服務主體應用程式識別碼和密碼。</span><span class="sxs-lookup"><span data-stu-id="27d33-191">Enter your service principal app ID and password from the `az ad sp create-for-rbac` command.</span></span>
   * <span data-ttu-id="27d33-192">提供 Jenkins 執行個體和 Grafana 主控台的使用者名稱和安全的密碼。</span><span class="sxs-lookup"><span data-stu-id="27d33-192">Provide a username and secure password for the Jenkins instance and Grafana console.</span></span>
   * <span data-ttu-id="27d33-193">提供 SSH 金鑰以保護 Linux 虛擬機器的登入。</span><span class="sxs-lookup"><span data-stu-id="27d33-193">Provide an SSH key to secure logins to the Linux VMs.</span></span>
   * <span data-ttu-id="27d33-194">檢閱條款及條件，然後按一下 [我同意上方所述的條款及條件]。</span><span class="sxs-lookup"><span data-stu-id="27d33-194">Review the terms and conditions, then check **I agree to the terms and conditions stated above**.</span></span>
   * <span data-ttu-id="27d33-195">選取 [購買] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="27d33-195">Select the **Purchase** button.</span></span>

<span data-ttu-id="27d33-196">需要 15-20 分鐘的時間才能完成部署。</span><span class="sxs-lookup"><span data-stu-id="27d33-196">It can take 15-20 minutes for the deployment to complete.</span></span>

## <a name="pricing"></a><span data-ttu-id="27d33-197">價格</span><span class="sxs-lookup"><span data-stu-id="27d33-197">Pricing</span></span>

<span data-ttu-id="27d33-198">為了探索執行此案例的成本，所有服務會在成本計算機中預先設定。</span><span class="sxs-lookup"><span data-stu-id="27d33-198">To explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="27d33-199">若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的流量。</span><span class="sxs-lookup"><span data-stu-id="27d33-199">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="27d33-200">我們根據要儲存的容器映像數目和執行應用程式的 Kubernetes 節點數目，提供了 3 個範例成本設定檔。</span><span class="sxs-lookup"><span data-stu-id="27d33-200">We have provided three sample cost profiles based on the number of container images to store and Kubernetes nodes to run your applications.</span></span>

* <span data-ttu-id="27d33-201">[小型][small-pricing]：這個設定檔適用於每月 1000 個容器建置。</span><span class="sxs-lookup"><span data-stu-id="27d33-201">[Small][small-pricing]: this correlates to 1000 container builds per month.</span></span>
* <span data-ttu-id="27d33-202">[中型][medium-pricing]：這個設定檔適用於每月 100000 個容器建置。</span><span class="sxs-lookup"><span data-stu-id="27d33-202">[Medium][medium-pricing]: this correlates to 100,000 container builds per month.</span></span>
* <span data-ttu-id="27d33-203">[大型][large-pricing]：這個設定檔適用於每月 1000000 個容器建置。</span><span class="sxs-lookup"><span data-stu-id="27d33-203">[Large][large-pricing]: this correlates to 1,000,000 container builds per month.</span></span>

## <a name="related-resources"></a><span data-ttu-id="27d33-204">相關資源</span><span class="sxs-lookup"><span data-stu-id="27d33-204">Related Resources</span></span>

<span data-ttu-id="27d33-205">此案例使用 Azure Container Registry 和 Azure Kubernetes Service 來儲存及執行容器型應用程式。</span><span class="sxs-lookup"><span data-stu-id="27d33-205">This scenario used Azure Container Registry and Azure Kubernetes Service to store and run your container-based applications.</span></span> <span data-ttu-id="27d33-206">Azure 容器執行個體也可以用來執行容器型應用程式，不需要佈建任何協調流程元件。</span><span class="sxs-lookup"><span data-stu-id="27d33-206">Azure Container Instances can also be used to run container-based applications, without having to provision any orchestration components.</span></span> <span data-ttu-id="27d33-207">如需詳細資訊，請參閱 [Azure 容器執行個體概觀][azureaci-docs]。</span><span class="sxs-lookup"><span data-stu-id="27d33-207">For more information, see [Azure Container Instances overview][azureaci-docs].</span></span>

<!-- links -->
[architecture]: ./media/devops-with-aks/architecture-devops-with-aks.png
[autoscaling]: ../../best-practices/auto-scaling.md
[availability]: ../../checklist/availability.md
[azureaci-docs]: /azure/container-instances/container-instances-overview
[azureacr-docs]: /azure/container-registry/container-registry-intro
[azurecosmosdb-docs]: /azure/cosmos-db/introduction
[azureaks-docs]: /azure/aks/intro-kubernetes
[azuremonitor-docs]: /azure/monitoring-and-diagnostics/monitoring-overview
[azurevm-docs]: /azure/virtual-machines/linux/overview
[createsp]: /cli/azure/ad/sp#az-ad-sp-create
[grafana]: https://grafana.com/
[jenkins]: https://jenkins.io/
[resiliency]: ../../resiliency/index.md
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[security]: /azure/security/
[scalability]: ../../checklist/scalability.md
[sshkeydocs]: /azure/virtual-machines/linux/mac-create-ssh-keys
[vsts]: /vsts/?view=vsts
[kubernetes]: https://kubernetes.io/
[service-fabric]: /azure/service-fabric/

[small-pricing]: https://azure.com/e/841f0a75b1ea4802ba1ac8f7918a71e7
[medium-pricing]: https://azure.com/e/eea0e6d79b4e45618a96d33383ec77ba
[large-pricing]: https://azure.com/e/3faab662c54c473da55a1e93a27e0e64

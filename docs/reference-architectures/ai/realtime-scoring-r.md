---
title: R 機器學習模型的即時評分
description: 使用在 Azure Kubernetes Service (AKS) 中執行的 Machine Learning Server 在 R 中實作即時預測服務。
author: njray
ms.date: 12/12/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai
ms.openlocfilehash: 00bea3cae0c3d2f0fea2babd7b0157382cf9890a
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58248683"
---
# <a name="real-time-scoring-of-r-machine-learning-models"></a><span data-ttu-id="a6b20-103">R 機器學習模型的即時評分</span><span class="sxs-lookup"><span data-stu-id="a6b20-103">Real-time scoring of R machine learning models</span></span>

<span data-ttu-id="a6b20-104">本參考架構說明如何使用在 Azure Kubernetes Service (AKS) 中執行的 Microsoft Machine Learning Server 在 R 中實作即時 (同步) 預測服務。</span><span class="sxs-lookup"><span data-stu-id="a6b20-104">This reference architecture shows how to implement a real-time (synchronous) prediction service in R using Microsoft Machine Learning Server running in Azure Kubernetes Service (AKS).</span></span> <span data-ttu-id="a6b20-105">此架構採通用設計，適用於任何您想要部署為即時服務、且內建於 R 中的預測性模型。</span><span class="sxs-lookup"><span data-stu-id="a6b20-105">This architecture is intended to be generic and suited for any predictive model built in R that you want to deploy as a real-time service.</span></span> <span data-ttu-id="a6b20-106">**[部署這個解決方案][github]**。</span><span class="sxs-lookup"><span data-stu-id="a6b20-106">**[Deploy this solution][github]**.</span></span>

## <a name="architecture"></a><span data-ttu-id="a6b20-107">架構</span><span class="sxs-lookup"><span data-stu-id="a6b20-107">Architecture</span></span>

![Azure 上的 R 機器學習模型的即時評分][0]

<span data-ttu-id="a6b20-109">此參考架構採用以容器為基礎的方法。</span><span class="sxs-lookup"><span data-stu-id="a6b20-109">This reference architecture takes a container-based approach.</span></span> <span data-ttu-id="a6b20-110">Docker 映像的建置包含 R，以及對新資料進行評分所需的各種成品。</span><span class="sxs-lookup"><span data-stu-id="a6b20-110">A Docker image is built containing R, as well as the various artifacts needed to score new data.</span></span> <span data-ttu-id="a6b20-111">其中包括模型物件本身和評分指令碼。</span><span class="sxs-lookup"><span data-stu-id="a6b20-111">These include the model object itself and a scoring script.</span></span> <span data-ttu-id="a6b20-112">此映像會推送至裝載在 Azure 中的 Docker 登錄，然後部署至 Kubernetes 叢集，也位於 Azure 中。</span><span class="sxs-lookup"><span data-stu-id="a6b20-112">This image is pushed to a Docker registry hosted in Azure, and then deployed to a Kubernetes cluster, also in Azure.</span></span>

<span data-ttu-id="a6b20-113">此工作流程的架構包含下列元件。</span><span class="sxs-lookup"><span data-stu-id="a6b20-113">The architecture of this workflow includes the following components.</span></span>

- <span data-ttu-id="a6b20-114">**[Azure Container Registry][acr]** 用來儲存此工作流程的映像。</span><span class="sxs-lookup"><span data-stu-id="a6b20-114">**[Azure Container Registry][acr]** is used to store the images for this workflow.</span></span> <span data-ttu-id="a6b20-115">使用 Container 登錄建立的登錄可透過標準 [Docker Registry V2 API][docker] 和用戶端來管理。</span><span class="sxs-lookup"><span data-stu-id="a6b20-115">Registries created with Container Registry can be managed via the standard [Docker Registry V2 API][docker] and client.</span></span>

- <span data-ttu-id="a6b20-116">**[Azure Kubernetes Service][aks]** 用來裝載部署和服務。</span><span class="sxs-lookup"><span data-stu-id="a6b20-116">**[Azure Kubernetes Service][aks]** is used to host the deployment and service.</span></span> <span data-ttu-id="a6b20-117">使用 AKS 建立的叢集可使用標準 [Kubernetes API][k-api] 和用戶端 (kubectl) 來管理。</span><span class="sxs-lookup"><span data-stu-id="a6b20-117">Clusters created with AKS can be managed using the standard [Kubernetes API][k-api] and client (kubectl).</span></span>

- <span data-ttu-id="a6b20-118">**[Microsoft Machine Learning Server][mmls]** 用來定義服務的 REST API，並包含[模型運算化][operationalization]。</span><span class="sxs-lookup"><span data-stu-id="a6b20-118">**[Microsoft Machine Learning Server][mmls]** is used to define the REST API for the service and includes [Model Operationalization][operationalization].</span></span> <span data-ttu-id="a6b20-119">此服務導向的 Web 伺服器程序會接聽要求，然後將其交由其他執行實際 R 程式碼的背景程序處理，以產生結果。</span><span class="sxs-lookup"><span data-stu-id="a6b20-119">This service-oriented web server process listens for requests, which are then handed off to other background processes that run the actual R code to generate the results.</span></span> <span data-ttu-id="a6b20-120">在此組態中，前述所有程序都會在單一節點上執行，並且包裝在容器中。</span><span class="sxs-lookup"><span data-stu-id="a6b20-120">All these processes run on a single node in this configuration, which is wrapped in a container.</span></span> <span data-ttu-id="a6b20-121">如需在開發或測試環境以外使用這項服務的詳細資訊，請連絡您的 Microsoft 代表。</span><span class="sxs-lookup"><span data-stu-id="a6b20-121">For details about using this service outside a dev or test environment, contact your Microsoft representative.</span></span>

## <a name="performance-considerations"></a><span data-ttu-id="a6b20-122">效能考量</span><span class="sxs-lookup"><span data-stu-id="a6b20-122">Performance considerations</span></span>

<span data-ttu-id="a6b20-123">機器學習工作負載通常需要大量計算，在進行新資料的定型和評分時都是如此。</span><span class="sxs-lookup"><span data-stu-id="a6b20-123">Machine learning workloads tend to be compute-intensive, both when training and when scoring new data.</span></span> <span data-ttu-id="a6b20-124">根據經驗法則，請不要讓每個核心執行多個評分程序。</span><span class="sxs-lookup"><span data-stu-id="a6b20-124">As a rule of thumb, try not to run more than one scoring process per core.</span></span> <span data-ttu-id="a6b20-125">Machine Learning Server 可讓您定義在每個容器中執行的 R 程序數目。</span><span class="sxs-lookup"><span data-stu-id="a6b20-125">Machine Learning Server lets you define the number of R processes running in each container.</span></span> <span data-ttu-id="a6b20-126">預設值為五個程序。</span><span class="sxs-lookup"><span data-stu-id="a6b20-126">The default is five processes.</span></span> <span data-ttu-id="a6b20-127">在建立相對較簡單的模型時 (例如，使用少量變數的線性迴歸，或小型決策樹) 時，您可以增加程序的數目。</span><span class="sxs-lookup"><span data-stu-id="a6b20-127">When creating a relatively simple model, such as a linear regression with a small number of variables, or a small decision tree, you can increase the number of processes.</span></span> <span data-ttu-id="a6b20-128">請監視叢集節點的 CPU 負載，以判斷容器數目的適當限制。</span><span class="sxs-lookup"><span data-stu-id="a6b20-128">Monitor the CPU load on your cluster nodes to determine the appropriate limit on the number of containers.</span></span>

<span data-ttu-id="a6b20-129">已啟用 GPU 功能的叢集可以加快某些類型的工作負載，特別是深度學習模型。</span><span class="sxs-lookup"><span data-stu-id="a6b20-129">A GPU-enabled cluster can speed up some types of workloads, and deep learning models in particular.</span></span> <span data-ttu-id="a6b20-130">並非所有工作負載都可使用 GPU &mdash; 只有大量使用矩陣代數的工作負載才適用。</span><span class="sxs-lookup"><span data-stu-id="a6b20-130">Not all workloads can take advantage of GPUs &mdash; only those that make heavy use of matrix algebra.</span></span> <span data-ttu-id="a6b20-131">例如，以樹狀結構為基礎的模型 (包括隨機樹系和提升模型) 通常無法獲益於 GPU。</span><span class="sxs-lookup"><span data-stu-id="a6b20-131">For example, tree-based models, including random forests and boosting models, generally derive no advantage from GPUs.</span></span>

<span data-ttu-id="a6b20-132">某些模型類型 (例如隨機樹系) 可在 CPU 上大規模平行執行。</span><span class="sxs-lookup"><span data-stu-id="a6b20-132">Some model types such as random forests are massively parallelizable on CPUs.</span></span> <span data-ttu-id="a6b20-133">在這種情況下，請將工作負載分散到多個核心，以加快單一要求的評分。</span><span class="sxs-lookup"><span data-stu-id="a6b20-133">In these cases, speed up the scoring of a single request by distributing the workload across multiple cores.</span></span> <span data-ttu-id="a6b20-134">但若這麼做，您對固定叢集大小處理多個評分要求的容量將會因此縮減。</span><span class="sxs-lookup"><span data-stu-id="a6b20-134">However, doing so reduces your capacity to handle multiple scoring requests given a fixed cluster size.</span></span>

<span data-ttu-id="a6b20-135">一般情況下，開放原始碼 R 模型會將其所有資料儲存在記憶體中，因此請確定您的節點有足夠的記憶體可容納您要同時執行的程序。</span><span class="sxs-lookup"><span data-stu-id="a6b20-135">In general, open-source R models store all their data in memory, so ensure that your nodes have enough memory to accommodate the processes you plan to run concurrently.</span></span> <span data-ttu-id="a6b20-136">如果您使用 Machine Learning Server 來配適您的模型，請使用可在磁碟上處理資料的程式庫，而不要將資料全部讀取到記憶體中。</span><span class="sxs-lookup"><span data-stu-id="a6b20-136">If you are using Machine Learning Server to fit your models, use the libraries that can process data on disk, rather than reading it all into memory.</span></span> <span data-ttu-id="a6b20-137">這有助於大幅降低記憶體需求。</span><span class="sxs-lookup"><span data-stu-id="a6b20-137">This can help reduce memory requirements significantly.</span></span> <span data-ttu-id="a6b20-138">不論您是使用 Machine Learning Server 還是開放原始碼 R，均請監視您的節點，以確定您的評分程序不會耗盡記憶體。</span><span class="sxs-lookup"><span data-stu-id="a6b20-138">Regardless of whether you use Machine Learning Server or open-source R, monitor your nodes to ensure that your scoring processes are not memory-starved.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="a6b20-139">安全性考量</span><span class="sxs-lookup"><span data-stu-id="a6b20-139">Security considerations</span></span>

### <a name="network-encryption"></a><span data-ttu-id="a6b20-140">網路加密</span><span class="sxs-lookup"><span data-stu-id="a6b20-140">Network encryption</span></span>

<span data-ttu-id="a6b20-141">在此參考架構中，會對與叢集的通訊啟用 HTTPS，並且使用來自 [Let 's Encrypt][encrypt] 的暫存憑證。</span><span class="sxs-lookup"><span data-stu-id="a6b20-141">In this reference architecture, HTTPS is enabled for communication with the cluster, and a staging certificate from [Let’s Encrypt][encrypt] is used.</span></span> <span data-ttu-id="a6b20-142">基於生產用途，請替換成適當的簽章授權單位為您提供的個人憑證。</span><span class="sxs-lookup"><span data-stu-id="a6b20-142">For production purposes, substitute your own certificate from an appropriate signing authority.</span></span>

### <a name="authentication-and-authorization"></a><span data-ttu-id="a6b20-143">驗證和授權</span><span class="sxs-lookup"><span data-stu-id="a6b20-143">Authentication and authorization</span></span>

<span data-ttu-id="a6b20-144">Machine Learning Server [模型運算化][operationalization]需要驗證評分要求。</span><span class="sxs-lookup"><span data-stu-id="a6b20-144">Machine Learning Server [Model Operationalization][operationalization] requires scoring requests to be authenticated.</span></span> <span data-ttu-id="a6b20-145">在此部署中，會使用使用者名稱和密碼。</span><span class="sxs-lookup"><span data-stu-id="a6b20-145">In this deployment, a username and password are used.</span></span> <span data-ttu-id="a6b20-146">在企業設定中，您可以使用 [Azure Active Directory][AAD] 來啟用驗證，或使用 [Azure API 管理][API]建立個別的後端。</span><span class="sxs-lookup"><span data-stu-id="a6b20-146">In an enterprise setting, you can enable authentication using [Azure Active Directory][AAD] or create a separate front end using [Azure API Management][API].</span></span>

<span data-ttu-id="a6b20-147">若要讓模型運算化與容器上的 Machine Learning Server 順利搭配運作，您必須安裝 JSON Web 權杖 (JWT) 憑證。</span><span class="sxs-lookup"><span data-stu-id="a6b20-147">For Model Operationalization to work correctly with Machine Learning Server on containers, you must install a JSON Web Token (JWT) certificate.</span></span> <span data-ttu-id="a6b20-148">此部署會使用 Microsoft 所提供的憑證。</span><span class="sxs-lookup"><span data-stu-id="a6b20-148">This deployment uses a certificate supplied by Microsoft.</span></span> <span data-ttu-id="a6b20-149">在生產環境設定中，請提供您自己的憑證。</span><span class="sxs-lookup"><span data-stu-id="a6b20-149">In a production setting, supply your own.</span></span>

<span data-ttu-id="a6b20-150">對於容器登錄與 AKS 之間的流量，請考慮啟用[角色型存取控制][rbac] (RBAC) 將存取權限僅限定於有需要的對象。</span><span class="sxs-lookup"><span data-stu-id="a6b20-150">For traffic between Container Registry and AKS, consider enabling [role-based access control][rbac] (RBAC) to limit access privileges to only those needed.</span></span>

### <a name="separate-storage"></a><span data-ttu-id="a6b20-151">區隔儲存體</span><span class="sxs-lookup"><span data-stu-id="a6b20-151">Separate storage</span></span>

<span data-ttu-id="a6b20-152">此參考架構會將應用程式 (R) 和資料 (模型物件和評分指令碼) 組合成單一映像。</span><span class="sxs-lookup"><span data-stu-id="a6b20-152">This reference architecture bundles the application (R) and the data (model object and scoring script) into a single image.</span></span> <span data-ttu-id="a6b20-153">在某些情況下，加以區隔可能會有好處。</span><span class="sxs-lookup"><span data-stu-id="a6b20-153">In some cases, it may be beneficial to separate these.</span></span> <span data-ttu-id="a6b20-154">您可以將模型資料和程式碼放入 Azure Blob 或檔案[儲存體][storage]中，並在容器初始化時加以擷取。</span><span class="sxs-lookup"><span data-stu-id="a6b20-154">You can place the model data and code into Azure blob or file [storage][storage], and retrieve them at container initialization.</span></span> <span data-ttu-id="a6b20-155">在此情況下，請確定儲存體帳戶設定為僅允許已驗證的存取，而且需要 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="a6b20-155">In this case, ensure that the storage account is set to allow authenticated access only and require HTTPS.</span></span>

## <a name="monitoring-and-logging-considerations"></a><span data-ttu-id="a6b20-156">監視和記錄的考量</span><span class="sxs-lookup"><span data-stu-id="a6b20-156">Monitoring and logging considerations</span></span>

<span data-ttu-id="a6b20-157">使用 [Kubernetes 儀表板][dashboard]監視 AKS 叢集的整體狀態。</span><span class="sxs-lookup"><span data-stu-id="a6b20-157">Use the [Kubernetes dashboard][dashboard] to monitor the overall status of your AKS cluster.</span></span> <span data-ttu-id="a6b20-158">如需詳細資訊，請在 Azure 入口網站中參閱叢集的概觀刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="a6b20-158">See the cluster’s overview blade in Azure portal for more details.</span></span> <span data-ttu-id="a6b20-159">[GitHub][github] 資源也會說明如何從 R 啟動儀表板。</span><span class="sxs-lookup"><span data-stu-id="a6b20-159">The [GitHub][github] resources also show how to bring up the dashboard from R.</span></span>

<span data-ttu-id="a6b20-160">雖然儀表板可讓您檢視叢集的整體健康情況，但追蹤個別容器的狀態也很重要。</span><span class="sxs-lookup"><span data-stu-id="a6b20-160">Although the dashboard gives you a view of the overall health of your cluster, it’s also important to track the status of individual containers.</span></span> <span data-ttu-id="a6b20-161">若要這麼做，請從 Azure 入口網站中的叢集概觀刀鋒視窗啟用 [Azure 監視器深入解析][monitor]，或參閱[適用於容器的 Azure 監視器][monitor-containers] (處於預覽狀態)。</span><span class="sxs-lookup"><span data-stu-id="a6b20-161">To do this, enable [Azure Monitor Insights][monitor] from the cluster overview blade in Azure portal, or see [Azure Monitor for containers][monitor-containers] (in preview).</span></span>

## <a name="cost-considerations"></a><span data-ttu-id="a6b20-162">成本考量</span><span class="sxs-lookup"><span data-stu-id="a6b20-162">Cost considerations</span></span>

<span data-ttu-id="a6b20-163">Machine Learning Server 是根據個別核心進行授權的，叢集中所有執行 Machine Learning Server 的核心都會計入此數中。</span><span class="sxs-lookup"><span data-stu-id="a6b20-163">Machine Learning Server is licensed on a per-core basis, and all the cores in the cluster that will run Machine Learning  Server count towards this.</span></span> <span data-ttu-id="a6b20-164">如果您是企業 Machine Learning Server 或 Microsoft SQL Server 的客戶，請連絡您的 Microsoft 代表以取得定價詳細資料。</span><span class="sxs-lookup"><span data-stu-id="a6b20-164">If you are an enterprise Machine Learning Server or Microsoft SQL Server customer, contact your Microsoft representative for pricing details.</span></span>

<span data-ttu-id="a6b20-165">Machine Learning Server 的開放原始碼替代方案是 [Plumber][plumber]，這是一個可將您的程式碼轉換為 REST API 的 R 套件。</span><span class="sxs-lookup"><span data-stu-id="a6b20-165">An open-source alternative to Machine Learning Server is [Plumber][plumber], an R package that turns your code into a REST API.</span></span> <span data-ttu-id="a6b20-166">Plumber 的功能不像 Machine Learning Server 那麼完整。</span><span class="sxs-lookup"><span data-stu-id="a6b20-166">Plumber is less fully featured than Machine Learning Server.</span></span> <span data-ttu-id="a6b20-167">例如，根據預設，它不含任何提供要求驗證的功能。</span><span class="sxs-lookup"><span data-stu-id="a6b20-167">For example, by default it doesn't include any features that provide request authentication.</span></span> <span data-ttu-id="a6b20-168">如果您使用 Plumber，建議您啟用 [Azure API 管理][API]來處理驗證詳細資料。</span><span class="sxs-lookup"><span data-stu-id="a6b20-168">If you use Plumber, it’s recommended that you enable [Azure API Management][API] to handle authentication details.</span></span>

<span data-ttu-id="a6b20-169">除了授權以外，主要的成本考量還有 Kubernetes 叢集的計算資源。</span><span class="sxs-lookup"><span data-stu-id="a6b20-169">Besides licensing, the main cost consideration is the Kubernetes cluster's compute resources.</span></span> <span data-ttu-id="a6b20-170">叢集必須夠大而足以處理尖峰時間的預期要求量，但這種方法會使資源在其他時間處於閒置狀態。</span><span class="sxs-lookup"><span data-stu-id="a6b20-170">The cluster must be large enough to handle the expected request volume at peak times, but this approach leaves resources idle at other times.</span></span> <span data-ttu-id="a6b20-171">若要限制閒置資源的影響，請透過 kubectl 工具啟用[水平自動調整程式][autoscaler]來處理叢集。</span><span class="sxs-lookup"><span data-stu-id="a6b20-171">To limit the impact of idle resources, enable the [horizontal autoscaler][autoscaler] for the cluster using the kubectl tool.</span></span> <span data-ttu-id="a6b20-172">或者，請使用[叢集自動調整程式][cluster-autoscaler]。</span><span class="sxs-lookup"><span data-stu-id="a6b20-172">Or use the AKS [cluster autoscaler][cluster-autoscaler].</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="a6b20-173">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="a6b20-173">Deploy the solution</span></span>

<span data-ttu-id="a6b20-174">此架構的參考實作可在 [GitHub][github] 上取得。</span><span class="sxs-lookup"><span data-stu-id="a6b20-174">The reference implementation of this architecture is available on [GitHub][github].</span></span> <span data-ttu-id="a6b20-175">請依照此處說明的步驟，部署簡單的預測模型作為服務。</span><span class="sxs-lookup"><span data-stu-id="a6b20-175">Follow the steps described there to deploy a simple predictive model as a service.</span></span>

<!-- links -->
[AAD]: /azure/active-directory/fundamentals/active-directory-whatis
[API]: /azure/api-management/api-management-key-concepts
[ACR]: /azure/container-registry/container-registry-intro
[AKS]: /azure/aks/intro-kubernetes
[autoscaler]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[cluster-autoscaler]: /azure/aks/autoscaler
[monitor]: /azure/monitoring/monitoring-container-insights-overview
[dashboard]: /azure/aks/kubernetes-dashboard
[docker]: https://docs.docker.com/registry/spec/api/
[encrypt]: https://letsencrypt.org/
[gitHub]: https://github.com/Azure/RealtimeRDeployment
[K-API]: https://kubernetes.io/docs/reference/
[MMLS]: /machine-learning-server/what-is-machine-learning-server
[monitor-containers]: /azure/azure-monitor/insights/container-insights-overview
[operationalization]: /machine-learning-server/what-is-operationalization
[plumber]: https://www.rplumber.io
[RBAC]: /azure/role-based-access-control/overview
[storage]: /azure/storage/common/storage-introduction
[0]: ./_images/realtime-scoring-r.png

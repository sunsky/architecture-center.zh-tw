---
title: Azure 上的電腦輔助工程服務
description: 為 Azure 上的電腦輔助工程 (CAE) 提供軟體即服務 (SaaS) 平台。
author: alexbuckgit
ms.date: 08/22/2018
ms.openlocfilehash: d17ac218052c5b98e8790f1386be035618a2d957
ms.sourcegitcommit: b2a4eb132857afa70201e28d662f18458865a48e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48818678"
---
# <a name="a-computer-aided-engineering-service-on-azure"></a><span data-ttu-id="8c88a-103">Azure 上的電腦輔助工程服務</span><span class="sxs-lookup"><span data-stu-id="8c88a-103">A computer-aided engineering service on Azure</span></span>

<span data-ttu-id="8c88a-104">此範例案例示範如何傳遞以 Azure 高效能運算 (HPC) 功能為基礎的軟體即服務 (SaaS) 平台。</span><span class="sxs-lookup"><span data-stu-id="8c88a-104">This example scenario demonstrates delivery of a software-as-a-service (SaaS) platform built on the high-performance computing (HPC) capabilities of Azure.</span></span> <span data-ttu-id="8c88a-105">此案例是以工程軟體解決方案為基礎。</span><span class="sxs-lookup"><span data-stu-id="8c88a-105">This scenario is based on an engineering software solution.</span></span> <span data-ttu-id="8c88a-106">不過，其架構與其他需要 HPC 資源的產業相關，例如影像轉譯、複雜模型化和財務風險計算。</span><span class="sxs-lookup"><span data-stu-id="8c88a-106">However, the architecture is relevant to other industries requiring HPC resources such as image rendering, complex modeling, and financial risk calculation.</span></span>

<span data-ttu-id="8c88a-107">此範例示範將電腦輔助工程 (CAE) 應用程式提供給工程公司和製造企業的工程軟體提供者。</span><span class="sxs-lookup"><span data-stu-id="8c88a-107">This example demonstrates an engineering software provider that delivers computer-aided engineering (CAE) applications to engineering firms and manufacturing enterprises.</span></span> <span data-ttu-id="8c88a-108">CAE 解決方案能賦予創新、減少開發時間，以及降低產品設計生命週期中的成本。</span><span class="sxs-lookup"><span data-stu-id="8c88a-108">CAE solutions enable innovation, reduce development times, and lower costs throughout the lifetime of a product's design.</span></span> <span data-ttu-id="8c88a-109">這些解決方案需要大量計算資源，且通常會處理很高的資料量。</span><span class="sxs-lookup"><span data-stu-id="8c88a-109">These solutions require substantial compute resources and often process high data volumes.</span></span> <span data-ttu-id="8c88a-110">內部部署 HPC 設備或高階工作站的昂貴成本，通常讓小型工程公司、企業主和學生無法接觸到這些技術。</span><span class="sxs-lookup"><span data-stu-id="8c88a-110">The high costs of an on-premises HPC appliance or high-end workstations often put these technologies out of reach for small engineering firms, entrepreneurs, and students.</span></span>

<span data-ttu-id="8c88a-111">公司想藉由建置雲端式 HPC 技術所支援的 SaaS 平台來擴充其應用程式的市場。</span><span class="sxs-lookup"><span data-stu-id="8c88a-111">The company wants to expand the market for its applications by building a SaaS platform backed by cloud-based HPC technologies.</span></span> <span data-ttu-id="8c88a-112">其客戶應該能夠視需要支付計算資源，以及存取原本負擔不起的大規模運算能力。</span><span class="sxs-lookup"><span data-stu-id="8c88a-112">Their customers should be able to pay for compute resources as needed and access massive computing power that would be unaffordable otherwise.</span></span>

<span data-ttu-id="8c88a-113">公司的目標包括：</span><span class="sxs-lookup"><span data-stu-id="8c88a-113">The company's goals include:</span></span>

* <span data-ttu-id="8c88a-114">利用 Azure 中的 HPC 功能來加速產品設計和測試程序。</span><span class="sxs-lookup"><span data-stu-id="8c88a-114">Taking advantage of HPC capabilities in Azure to accelerate the product design and testing process.</span></span>
* <span data-ttu-id="8c88a-115">使用最新的硬體創新功能來執行複雜的模擬，同時將更簡單模擬的成本降至最低。</span><span class="sxs-lookup"><span data-stu-id="8c88a-115">Using the latest hardware innovations to run complex simulations, while minimizing the costs for simpler simulations.</span></span>
* <span data-ttu-id="8c88a-116">啟用逼真的視覺效果並在網頁瀏覽器中轉譯，而不需要高階的工程工作站。</span><span class="sxs-lookup"><span data-stu-id="8c88a-116">Enabling true-to-life visualization and rendering in a web browser, without requiring a high-end engineering workstation.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="8c88a-117">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="8c88a-117">Relevant use cases</span></span>

<span data-ttu-id="8c88a-118">使用此架構的其他案例可能包括：</span><span class="sxs-lookup"><span data-stu-id="8c88a-118">Other scenarios using this architecture might include:</span></span>

* <span data-ttu-id="8c88a-119">基因體研究</span><span class="sxs-lookup"><span data-stu-id="8c88a-119">Genomics research</span></span>
* <span data-ttu-id="8c88a-120">天氣模擬</span><span class="sxs-lookup"><span data-stu-id="8c88a-120">Weather simulation</span></span>
* <span data-ttu-id="8c88a-121">計算化學應用程式</span><span class="sxs-lookup"><span data-stu-id="8c88a-121">Computational chemistry applications</span></span>

## <a name="architecture"></a><span data-ttu-id="8c88a-122">架構</span><span class="sxs-lookup"><span data-stu-id="8c88a-122">Architecture</span></span>

![啟用 HPC 功能的 SaaS 解決方案架構][architecture]

* <span data-ttu-id="8c88a-124">使用者可以使用 [Apache Guacamole 服務](https://guacamole.apache.org/)，透過具有 HTML5 型 RDP 連線的瀏覽器存取 NV 系列虛擬機器 (VM)。</span><span class="sxs-lookup"><span data-stu-id="8c88a-124">Users can access NV-series virtual machines (VMs) via a browser with an HTML5-based RDP connection using the [Apache Guacamole service](https://guacamole.apache.org/).</span></span> <span data-ttu-id="8c88a-125">這些 VM 執行個體為轉譯和共同作業工作提供強大的 GPU。</span><span class="sxs-lookup"><span data-stu-id="8c88a-125">These VM instances provide powerful GPUs for rendering and collaborative tasks.</span></span> <span data-ttu-id="8c88a-126">使用者可以編輯其設計並檢視其結果，而不需存取高階行動運算裝置或膝上型電腦。</span><span class="sxs-lookup"><span data-stu-id="8c88a-126">Users can edit their designs and view their results without needing access to high-end mobile computing devices or laptops.</span></span> <span data-ttu-id="8c88a-127">排程器會根據使用者定義的啟發學習法來啟動額外的 VM。</span><span class="sxs-lookup"><span data-stu-id="8c88a-127">The scheduler spins up additional VMs based on user-defined heuristics.</span></span>
* <span data-ttu-id="8c88a-128">從桌面 CAD 工作階段，使用者可以在可用的 HPC 叢集節點上提供工作負載以供執行。</span><span class="sxs-lookup"><span data-stu-id="8c88a-128">From a desktop CAD session, users can submit workloads for execution on available HPC cluster nodes.</span></span> <span data-ttu-id="8c88a-129">這些工作負載會執行壓力分析或計算流體力學計算等工作，因而免除專用內部部署計算叢集的需求。</span><span class="sxs-lookup"><span data-stu-id="8c88a-129">These workloads perform tasks such as stress analysis or computational fluid dynamics calculations, eliminating the need for dedicated  on-premises compute clusters.</span></span> <span data-ttu-id="8c88a-130">您可以根據以作用中使用者的計算資源需求為基礎的負載或佇列深度，將這些叢集節點設定為自動調整。</span><span class="sxs-lookup"><span data-stu-id="8c88a-130">These cluster nodes can be configured to auto-scale based on load or queue depth based on active user demand for compute resources.</span></span>
* <span data-ttu-id="8c88a-131">Azure Kubernetes Service (AKS) 用來裝載使用者可用的 Web 資源。</span><span class="sxs-lookup"><span data-stu-id="8c88a-131">Azure Kubernetes Service (AKS) is used to host the web resources available to end users.</span></span>

### <a name="components"></a><span data-ttu-id="8c88a-132">元件</span><span class="sxs-lookup"><span data-stu-id="8c88a-132">Components</span></span>

* <span data-ttu-id="8c88a-133">[H 系列虛擬機器](/azure/virtual-machines/linux/sizes-hpc)用來執行計算密集的模擬，例如分子建模和運算流體力學。</span><span class="sxs-lookup"><span data-stu-id="8c88a-133">[H-series virtual machines](/azure/virtual-machines/linux/sizes-hpc) are used to run compute-intensive simulations such as molecular modeling and computational fluid dynamics.</span></span> <span data-ttu-id="8c88a-134">此解決方案也會利用遠端直接記憶體存取 (RDMA) 連線和 InfiniBand 網路功能等技術。</span><span class="sxs-lookup"><span data-stu-id="8c88a-134">The solution also takes advantage of technologies like remote direct memory access (RDMA) connectivity and InfiniBand networking.</span></span>
* <span data-ttu-id="8c88a-135">[NV 系列虛擬機器](/azure/virtual-machines/windows/sizes-gpu)為工程師提供從標準網頁瀏覽器執行的高階工作站功能。</span><span class="sxs-lookup"><span data-stu-id="8c88a-135">[NV-series virtual machines](/azure/virtual-machines/windows/sizes-gpu) give engineers high-end workstation functionality from a standard web browser.</span></span> <span data-ttu-id="8c88a-136">這些虛擬機器採用 NVIDIA Tesla M60 GPU，其支援進階轉譯並可執行單一的精密工作負載。</span><span class="sxs-lookup"><span data-stu-id="8c88a-136">These virtual machines have NVIDIA Tesla M60 GPUs that support advanced rendering and can run single precision workloads.</span></span>
* <span data-ttu-id="8c88a-137">執行 CentOS 的[一般用途虛擬機器](/azure/virtual-machines/linux/sizes-general)可處理更傳統的工作負載，例如 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="8c88a-137">[General purpose virtual machines](/azure/virtual-machines/linux/sizes-general) running CentOS handle more traditional workloads such as web applications.</span></span>
* <span data-ttu-id="8c88a-138">[應用程式閘道](/azure/application-gateway/overview)會讓進入網頁伺服器的要求達到負載平衡。</span><span class="sxs-lookup"><span data-stu-id="8c88a-138">[Application Gateway](/azure/application-gateway/overview) load balances the requests coming into the web servers.</span></span>
* <span data-ttu-id="8c88a-139">對於不需要 HPC 或 GPU 虛擬機器高階功能的模擬，[Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) 用於以較低的成本執行可調式工作負載。</span><span class="sxs-lookup"><span data-stu-id="8c88a-139">[Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) is used to run scalable workloads at a lower cost for simulations that don't require the high end capabilities of HPC or GPU virtual machines.</span></span>
* <span data-ttu-id="8c88a-140">[Altair PBS Works Suite](https://www.pbsworks.com/PBSProduct.aspx?n=PBS-Works-Suite&c=Overview-and-Capabilities) 可協調 HPC 工作流程，進而確保有足夠的虛擬機器執行個體可處理目前的負載。</span><span class="sxs-lookup"><span data-stu-id="8c88a-140">[Altair PBS Works Suite](https://www.pbsworks.com/PBSProduct.aspx?n=PBS-Works-Suite&c=Overview-and-Capabilities) orchestrates the HPC workflow, ensuring that enough virtual machine instances are available to handle the current load.</span></span> <span data-ttu-id="8c88a-141">當需求較低而降低成本時，它也會將虛擬機器解除配置。</span><span class="sxs-lookup"><span data-stu-id="8c88a-141">It also deallocates virtual machines when demand is lower to reduce costs.</span></span>
* <span data-ttu-id="8c88a-142">[Blob 儲存體](/azure/storage/blobs/storage-blobs-introduction)可儲存支援排定工作的檔案。</span><span class="sxs-lookup"><span data-stu-id="8c88a-142">[Blob storage](/azure/storage/blobs/storage-blobs-introduction) stores files that support the scheduled jobs.</span></span> 

### <a name="alternatives"></a><span data-ttu-id="8c88a-143">替代項目</span><span class="sxs-lookup"><span data-stu-id="8c88a-143">Alternatives</span></span>

* <span data-ttu-id="8c88a-144">[Azure CycleCloud](/azure/cyclecloud/overview) 可簡化 HPC 叢集的建立、管理、操作及最佳化。</span><span class="sxs-lookup"><span data-stu-id="8c88a-144">[Azure CycleCloud](/azure/cyclecloud/overview) simplifies creating, managing, operating, and optimizing HPC clusters.</span></span> <span data-ttu-id="8c88a-145">它可提供進階原則和治理功能。</span><span class="sxs-lookup"><span data-stu-id="8c88a-145">It offers advanced policy and governance features.</span></span> <span data-ttu-id="8c88a-146">CycleCloud 支援任何作業排程器或軟體堆疊。</span><span class="sxs-lookup"><span data-stu-id="8c88a-146">CycleCloud supports any job scheduler or software stack.</span></span>
* <span data-ttu-id="8c88a-147">[HPC Pack](/azure/virtual-machines/windows/hpcpack-cluster-options) 可以建立及管理適用於 Windows Server 型工作負載的 HPC 叢集。</span><span class="sxs-lookup"><span data-stu-id="8c88a-147">[HPC Pack](/azure/virtual-machines/windows/hpcpack-cluster-options) can create and manage an Azure HPC cluster for Windows Server-based workloads.</span></span> <span data-ttu-id="8c88a-148">HPC Pack 不是 Linux 型工作負載的選項。</span><span class="sxs-lookup"><span data-stu-id="8c88a-148">HPC Pack isn't an option for Linux-based workloads.</span></span>
* <span data-ttu-id="8c88a-149">[Azure 自動化狀態設定](/azure/automation/automation-dsc-overview)提供基礎結構即程式碼方法，以定義要部署的虛擬機器和軟體。</span><span class="sxs-lookup"><span data-stu-id="8c88a-149">[Azure Automation State Configuration](/azure/automation/automation-dsc-overview) provides an infrastructure-as-code approach to defining the virtual machines and software to be deployed.</span></span> <span data-ttu-id="8c88a-150">對計算節點使用以提交至工作佇列的作業數目為基礎的自動調整規則，可以將虛擬機器部署為虛擬機器擴展集的一部分。</span><span class="sxs-lookup"><span data-stu-id="8c88a-150">Virtual machines can be deployed as part of a virtual machine scale set, with auto-scaling rules for compute nodes based on the number of jobs submitted to the job queue.</span></span> <span data-ttu-id="8c88a-151">需要新的虛擬機器時，可使用 Azure 映像庫中最新修補的映像來佈建，然後透過 PowerShell DSC 設定指令碼來安裝並設定所需的軟體。</span><span class="sxs-lookup"><span data-stu-id="8c88a-151">When a new virtual machine is needed, it is provisioned using the latest patched image from the Azure image gallery, and then the required software is installed and configured via a PowerShell DSC configuration script.</span></span>
* [<span data-ttu-id="8c88a-152">Azure Functions</span><span class="sxs-lookup"><span data-stu-id="8c88a-152">Azure Functions</span></span>](/azure/azure-functions/functions-overview)

## <a name="considerations"></a><span data-ttu-id="8c88a-153">考量</span><span class="sxs-lookup"><span data-stu-id="8c88a-153">Considerations</span></span>

* <span data-ttu-id="8c88a-154">雖然使用基礎結構即程式碼方法很適合用來管理虛擬機器的組建定義，使用指令碼可能需要很長的時間來佈建新的虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="8c88a-154">While using an infrastructure-as-code approach is a great way to manage virtual machine build definitions, it can take a long time to provision a new virtual machine using a script.</span></span> <span data-ttu-id="8c88a-155">此解決方案找到很好的折衷方式，就是使用 DSC 指令碼定期建立黃金映像，該映像接著可用來佈建新的虛擬機器，速度比使用 DSC 視需要完全 建置 VM 還要快。</span><span class="sxs-lookup"><span data-stu-id="8c88a-155">This solution found a good middle ground by using the DSC script to periodically create a golden image, which can then be used to provision a new virtual machine faster than completely building a VM on demand using DSC.</span></span> <span data-ttu-id="8c88a-156">Azure DevOps 服務或其他 CI/CD 工具可以使用 DSC 指令碼定期重新整理黃金映像。</span><span class="sxs-lookup"><span data-stu-id="8c88a-156">Azure DevOps Services or other CI/CD tooling can periodically refresh golden images using DSC scripts.</span></span>
* <span data-ttu-id="8c88a-157">透過快速提供計算資源來平衡整體解決方案成本是一項重要考量。</span><span class="sxs-lookup"><span data-stu-id="8c88a-157">Balancing overall solution costs with fast availability of compute resources is a key consideration.</span></span> <span data-ttu-id="8c88a-158">佈建一些 N 系列虛擬機器執行個體並將其置於已解除配置的狀態，可降低營運成本。</span><span class="sxs-lookup"><span data-stu-id="8c88a-158">Provisioning a pool of N-series virtual machine instances and putting them in a deallocated state lowers the operating costs.</span></span> <span data-ttu-id="8c88a-159">需要額外的虛擬機器時，重新配置現有的執行個體則需在不同的主機上開啟虛擬機器，但可免除 OS 找出並安裝 GPU 驅動程式所需的 PCI 匯流排偵測時間，因為已取消佈建而後重新佈建的虛擬機器會在重新啟動時保留適用於 GPU 的相同 PCI 匯流排。</span><span class="sxs-lookup"><span data-stu-id="8c88a-159">When an additional virtual machine is needed, reallocating an existing instance will involve powering up the virtual machine on a different host, but the PCI bus detection time required by the OS to identify and install drivers for the GPU is eliminated because a virtual machine that is deprovisioned and then reprovisioned will retain the same PCI bus for the GPU upon restart.</span></span>
* <span data-ttu-id="8c88a-160">原始架構完全仰賴 Azure 虛擬機器來執行模擬。</span><span class="sxs-lookup"><span data-stu-id="8c88a-160">The original architecture relied entirely on Azure virtual machines for running simulations.</span></span> <span data-ttu-id="8c88a-161">為了讓不需要所有虛擬機器功能的工作負載降低成本，這些工作負載已容器化並且部署至 Azure Kubernetes Service (AKS)。</span><span class="sxs-lookup"><span data-stu-id="8c88a-161">In order to reduce costs for workloads that didn't require all the capabilities of a virtual machine, these workloads were containerized and deployed to Azure Kubernetes Service (AKS).</span></span>
* <span data-ttu-id="8c88a-162">公司的員工已具有開放原始碼技術的技能。</span><span class="sxs-lookup"><span data-stu-id="8c88a-162">The company's workforce had existing skills in open-source technologies.</span></span> <span data-ttu-id="8c88a-163">他們可藉由採用 Linux 和 Kubernetes 等技術，進而利用這些技能。</span><span class="sxs-lookup"><span data-stu-id="8c88a-163">They can take advantage of these skills by building on technologies like Linux and Kubernetes.</span></span> 

## <a name="pricing"></a><span data-ttu-id="8c88a-164">價格</span><span class="sxs-lookup"><span data-stu-id="8c88a-164">Pricing</span></span>

<span data-ttu-id="8c88a-165">為了協助您探索執行此案例的成本，許多必要服務都會在[成本計算機範例][calculator]中預先設定。</span><span class="sxs-lookup"><span data-stu-id="8c88a-165">To help you explore the cost of running this scenario, many of the required services are pre-configured in a [cost calculator example][calculator].</span></span> <span data-ttu-id="8c88a-166">您解決方案的成本取決於要符合您的需求所需的服務數目和規模。</span><span class="sxs-lookup"><span data-stu-id="8c88a-166">The costs of your solution are dependent on the number and scale of services needed to meet your requirements.</span></span>

<span data-ttu-id="8c88a-167">下列考量會影響此解決方案的大部分成本：</span><span class="sxs-lookup"><span data-stu-id="8c88a-167">The following considerations will drive a substantial portion of the costs for this solution:</span></span>
* <span data-ttu-id="8c88a-168">佈建額外的執行個體時，Azure 虛擬機器成本會以線性方式增加。</span><span class="sxs-lookup"><span data-stu-id="8c88a-168">Azure virtual machine costs increase linearly as additional instances are provisioned.</span></span> <span data-ttu-id="8c88a-169">解除配置的虛擬機器只會產生儲存體成本，而不會產生計算成本。</span><span class="sxs-lookup"><span data-stu-id="8c88a-169">Virtual machines that are deallocated will only incur storage costs, and not compute costs.</span></span> <span data-ttu-id="8c88a-170">當要求很高時，即可重新配置這些已解除配置的機器。</span><span class="sxs-lookup"><span data-stu-id="8c88a-170">These deallocated machines can then be reallocated when demand is high.</span></span>
* <span data-ttu-id="8c88a-171">Azure Kubernetes 服務成本是以為了支援工作負載所選的 VM 類型為基礎。</span><span class="sxs-lookup"><span data-stu-id="8c88a-171">Azure Kubernetes Services costs are based on the VM type chosen to support the workload.</span></span> <span data-ttu-id="8c88a-172">此成本會根據叢集中的 VM 數目以線性方式增加。</span><span class="sxs-lookup"><span data-stu-id="8c88a-172">The costs will increase linearly based on the number of VMs in the cluster.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8c88a-173">後續步驟</span><span class="sxs-lookup"><span data-stu-id="8c88a-173">Next Steps</span></span>

* <span data-ttu-id="8c88a-174">閱讀 [Altair 客戶案例][source-document]。</span><span class="sxs-lookup"><span data-stu-id="8c88a-174">Read the [Altair customer story][source-document].</span></span> <span data-ttu-id="8c88a-175">此範例案例是以其架構版本為基礎。</span><span class="sxs-lookup"><span data-stu-id="8c88a-175">This example scenario is based on a version of their architecture.</span></span>
* <span data-ttu-id="8c88a-176">檢閱 Azure 中其他可用的 [Big Compute 解決方案](https://azure.microsoft.com/solutions/big-compute)。</span><span class="sxs-lookup"><span data-stu-id="8c88a-176">Review other [Big Compute solutions](https://azure.microsoft.com/solutions/big-compute) available in Azure.</span></span>

<!-- links -->
[architecture]: ./media/architecture-hpc-saas.png
[source-document]: https://customers.microsoft.com/story/altair-manufacturing-azure
[calculator]: https://azure.com/e/3cb9ccdc893f41ffbcdb00c328178ccf
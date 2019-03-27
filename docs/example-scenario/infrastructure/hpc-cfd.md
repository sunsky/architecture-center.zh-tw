---
title: 執行 CFD 模擬
titleSuffix: Azure Example Scenarios
description: 在 Azure 上執行計算流體力學 (CFD) 模擬。
author: mikewarr
ms.date: 09/20/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: fasttrack
social_image_url: /azure/architecture/example-scenario/infrastructure/media/architecture-hpc-cfd.png
ms.openlocfilehash: 6972b701a608351104c23459bc2c38029a5c8d3d
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58249583"
---
# <a name="running-computational-fluid-dynamics-cfd-simulations-on-azure"></a><span data-ttu-id="558d8-103">在 Azure 上執行計算流體力學 (CFD) 模擬</span><span class="sxs-lookup"><span data-stu-id="558d8-103">Running computational fluid dynamics (CFD) simulations on Azure</span></span>

<span data-ttu-id="558d8-104">計算流體力學 (CFD) 模擬需要大量計算時間以及特製化硬體。</span><span class="sxs-lookup"><span data-stu-id="558d8-104">Computational Fluid Dynamics (CFD) simulations require significant compute time along with specialized hardware.</span></span> <span data-ttu-id="558d8-105">當叢集使用量增加時，模擬時間和整體方格使用會隨之成長，進而導致備用容量和佇列時間很長的問題。</span><span class="sxs-lookup"><span data-stu-id="558d8-105">As cluster usage increases, simulation times and overall grid use grow, leading to issues with spare capacity and long queue times.</span></span> <span data-ttu-id="558d8-106">新增實體硬體的成本很高，而且可能與企業所經歷的使用量尖峰和低谷不相符。</span><span class="sxs-lookup"><span data-stu-id="558d8-106">Adding physical hardware can be expensive, and may not align to the usage peaks and valleys that a business goes through.</span></span> <span data-ttu-id="558d8-107">利用 Azure，可以克服其中許多挑戰，而沒有任何資本支出。</span><span class="sxs-lookup"><span data-stu-id="558d8-107">By taking advantage of Azure, many of these challenges can be overcome with no capital expenditure.</span></span>

<span data-ttu-id="558d8-108">Azure 提供您在 GPU 和 CPU 虛擬機器上執行 CFD 作業所需的硬體。</span><span class="sxs-lookup"><span data-stu-id="558d8-108">Azure provides the hardware you need to run your CFD jobs on both GPU and CPU virtual machines.</span></span> <span data-ttu-id="558d8-109">支援 RDMA (遠端直接記憶體存取) 的 VM 大小具有 FDR InfiniBand 型網路，能夠進行低延遲 MPI (訊息傳遞介面) 通訊。</span><span class="sxs-lookup"><span data-stu-id="558d8-109">RDMA (Remote Direct Memory Access) enabled VM sizes have FDR InfiniBand-based networking which allows for low latency MPI (Message Passing Interface) communication.</span></span> <span data-ttu-id="558d8-110">結合可提供企業級叢集檔案系統的 Avere vFXT，客戶即可確保 Azure 中讀取作業的最大輸送量。</span><span class="sxs-lookup"><span data-stu-id="558d8-110">Combined with the Avere vFXT, which provides an enterprise-scale clustered file system, customers can ensure maximum throughput for read operations in Azure.</span></span>

<span data-ttu-id="558d8-111">若要簡化 HPC 叢集的建立、管理和最佳化，Azure CycleCloud 可用來佈建叢集，以及協調混合式和雲端案例中的資料。</span><span class="sxs-lookup"><span data-stu-id="558d8-111">To simplify the creation, management, and optimization of HPC clusters, Azure CycleCloud can be used to provision clusters and orchestrate data in both hybrid and cloud scenarios.</span></span> <span data-ttu-id="558d8-112">CycleCloud 可藉由監視擱置工作，自動啟動隨選計算功能，讓您只需支付您所使用的項目，同時連線到您所選的工作負載排程器。</span><span class="sxs-lookup"><span data-stu-id="558d8-112">By monitoring the pending jobs, CycleCloud will automatically launch on-demand compute, where you only pay for what you use, connected to the workload scheduler of your choice.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="558d8-113">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="558d8-113">Relevant use cases</span></span>

<span data-ttu-id="558d8-114">其他 CFD 應用程式相關產業包括：</span><span class="sxs-lookup"><span data-stu-id="558d8-114">Other relevant industries for CFD applications include:</span></span>

- <span data-ttu-id="558d8-115">航空</span><span class="sxs-lookup"><span data-stu-id="558d8-115">Aeronautics</span></span>
- <span data-ttu-id="558d8-116">汽車</span><span class="sxs-lookup"><span data-stu-id="558d8-116">Automotive</span></span>
- <span data-ttu-id="558d8-117">建築物 HVAV</span><span class="sxs-lookup"><span data-stu-id="558d8-117">Building HVAC</span></span>
- <span data-ttu-id="558d8-118">石油與天然氣</span><span class="sxs-lookup"><span data-stu-id="558d8-118">Oil and gas</span></span>
- <span data-ttu-id="558d8-119">生命科學</span><span class="sxs-lookup"><span data-stu-id="558d8-119">Life sciences</span></span>

## <a name="architecture"></a><span data-ttu-id="558d8-120">架構</span><span class="sxs-lookup"><span data-stu-id="558d8-120">Architecture</span></span>

![架構圖表][architecture]

<span data-ttu-id="558d8-122">下圖顯示典型混合式設計的高階概觀，以供監視 Azure 中隨選節點的作業：</span><span class="sxs-lookup"><span data-stu-id="558d8-122">This diagram shows a high-level overview of a typical hybrid design providing job monitoring of the on-demand nodes in Azure:</span></span>

1. <span data-ttu-id="558d8-123">連線到 Azure CycleCloud 伺服器以設定叢集。</span><span class="sxs-lookup"><span data-stu-id="558d8-123">Connect to the Azure CycleCloud server to configure the cluster.</span></span>
2. <span data-ttu-id="558d8-124">將支援 RDMA 的機器使用於 MPI，以設定和建立叢集前端節點。</span><span class="sxs-lookup"><span data-stu-id="558d8-124">Configure and create the cluster head node, using RDMA enabled machines for MPI.</span></span>
3. <span data-ttu-id="558d8-125">新增並設定內部部署前端節點。</span><span class="sxs-lookup"><span data-stu-id="558d8-125">Add and configure the on-premises head node.</span></span>
4. <span data-ttu-id="558d8-126">如果資源不足，Azure CycleCloud 會相應增加 (或減少) Azure 中的計算資源。</span><span class="sxs-lookup"><span data-stu-id="558d8-126">If there are insufficient resources, Azure CycleCloud will scale up (or down) compute resources in Azure.</span></span> <span data-ttu-id="558d8-127">您可定義預先決定的限制，以防止過度配置。</span><span class="sxs-lookup"><span data-stu-id="558d8-127">A predetermined limit can be defined to prevent over allocation.</span></span>
5. <span data-ttu-id="558d8-128">配置給執行節點的工作。</span><span class="sxs-lookup"><span data-stu-id="558d8-128">Tasks allocated to the execute nodes.</span></span>
6. <span data-ttu-id="558d8-129">Azure 中從內部部署 NFS 伺服器快取的資料。</span><span class="sxs-lookup"><span data-stu-id="558d8-129">Data cached in Azure from on-premises NFS server.</span></span>
7. <span data-ttu-id="558d8-130">從 Avere vFXT for Azure 快取讀入的資料。</span><span class="sxs-lookup"><span data-stu-id="558d8-130">Data read in from the Avere vFXT for Azure cache.</span></span>
8. <span data-ttu-id="558d8-131">轉送至 Azure CycleCloud 伺服器的作業和工作資訊。</span><span class="sxs-lookup"><span data-stu-id="558d8-131">Job and task information relayed to the Azure CycleCloud server.</span></span>

### <a name="components"></a><span data-ttu-id="558d8-132">元件</span><span class="sxs-lookup"><span data-stu-id="558d8-132">Components</span></span>

- <span data-ttu-id="558d8-133">[Azure CycleCloud][cyclecloud] 是一項工具，用於建立、管理、操作及最佳化 Azure 中的 HPC 和 Big Compute 叢集。</span><span class="sxs-lookup"><span data-stu-id="558d8-133">[Azure CycleCloud][cyclecloud] a tool for creating, managing, operating, and optimizing HPC and Big Compute clusters in Azure.</span></span>
- <span data-ttu-id="558d8-134">[Azure 上的 Avere vFXT][avere] 用來提供專為雲端建置的企業級叢集檔案系統。</span><span class="sxs-lookup"><span data-stu-id="558d8-134">[Avere vFXT on Azure][avere] is used to provide an enterprise-scale clustered file system built for the cloud.</span></span>
- <span data-ttu-id="558d8-135">[Azure 虛擬機器 (Vm)][vms] 用來建立一組靜態計算執行個體。</span><span class="sxs-lookup"><span data-stu-id="558d8-135">[Azure Virtual Machines (VMs)][vms] are used to create a static set of compute instances.</span></span>
- <span data-ttu-id="558d8-136">[虛擬機器擴展集][vmss] 提供一組能夠由 Azure CycleCloud 相應增加或減少的相同 VM。</span><span class="sxs-lookup"><span data-stu-id="558d8-136">[Virtual Machine Scale Sets (virtual machine scale set)][vmss] provide a group of identical VMs capable of being scaled up or down by Azure CycleCloud.</span></span>
- <span data-ttu-id="558d8-137">[Azure 儲存體帳戶](/azure/storage/common/storage-introduction)是用於同步處理和資料保留。</span><span class="sxs-lookup"><span data-stu-id="558d8-137">[Azure Storage accounts](/azure/storage/common/storage-introduction) are used for synchronization and data retention.</span></span>
- <span data-ttu-id="558d8-138">[虛擬網路](/azure/virtual-network/virtual-networks-overview)可讓多種類型的 Azure 資源 (例如 Azure 虛擬機器 (VM)) 安全地彼此通訊，以及與網際網路和內部部署網路通訊。</span><span class="sxs-lookup"><span data-stu-id="558d8-138">[Virtual Networks](/azure/virtual-network/virtual-networks-overview) enable many types of Azure resources, such as Azure Virtual Machines (VMs), to securely communicate with each other, the internet, and on-premises networks.</span></span>

### <a name="alternatives"></a><span data-ttu-id="558d8-139">替代項目</span><span class="sxs-lookup"><span data-stu-id="558d8-139">Alternatives</span></span>

<span data-ttu-id="558d8-140">客戶也可以使用 Azure CycleCloud 完全在 Azure 中建立方格。</span><span class="sxs-lookup"><span data-stu-id="558d8-140">Customers can also use Azure CycleCloud to create a grid entirely in Azure.</span></span> <span data-ttu-id="558d8-141">在此設定中，Azure CycleCloud 伺服器是在您的 Azure 訂用帳戶中執行。</span><span class="sxs-lookup"><span data-stu-id="558d8-141">In this setup, the Azure CycleCloud server is run within your Azure subscription.</span></span>

<span data-ttu-id="558d8-142">如需不需要管理工作負載排程器的新式應用程式方法，[Azure Batch][batch] 有所幫助。</span><span class="sxs-lookup"><span data-stu-id="558d8-142">For a modern application approach where management of a workload scheduler is not needed, [Azure Batch][batch] can help.</span></span> <span data-ttu-id="558d8-143">Azure Batch 可以在雲端有效率地執行大規模的平行和高效能運算 (HPC) 應用程式。</span><span class="sxs-lookup"><span data-stu-id="558d8-143">Azure Batch can run large-scale parallel and high-performance computing (HPC) applications efficiently in the cloud.</span></span> <span data-ttu-id="558d8-144">Azure Batch 可讓您定義 Azure 計算資源，進而以平行方式或大規模執行應用程式，而不需要手動設定或管理基礎結構。</span><span class="sxs-lookup"><span data-stu-id="558d8-144">Azure Batch allows you to define the Azure compute resources to execute your applications in parallel or at scale without manually configuring or managing infrastructure.</span></span> <span data-ttu-id="558d8-145">Azure Batch 會排程需要大量計算的工作，並根據您的需求動態新增和移除計算資源。</span><span class="sxs-lookup"><span data-stu-id="558d8-145">Azure Batch schedules compute-intensive tasks and dynamically adds and removes compute resources based on your requirements.</span></span>

### <a name="scalability-and-security"></a><span data-ttu-id="558d8-146">延展性與安全性</span><span class="sxs-lookup"><span data-stu-id="558d8-146">Scalability, and Security</span></span>

<span data-ttu-id="558d8-147">您可以手動或使用自動調整功能來調整 Azure CycleCloud 上的執行節點。</span><span class="sxs-lookup"><span data-stu-id="558d8-147">Scaling the execute nodes on Azure CycleCloud can be accomplished either manually or using autoscaling.</span></span> <span data-ttu-id="558d8-148">如需詳細資訊，請參閱 [CycleCloud 自動調整][cycle-scale]。</span><span class="sxs-lookup"><span data-stu-id="558d8-148">For more information, see [CycleCloud Autoscaling][cycle-scale].</span></span>

<span data-ttu-id="558d8-149">如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。</span><span class="sxs-lookup"><span data-stu-id="558d8-149">For general guidance on designing secure solutions, see the [Azure security documentation][security].</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="558d8-150">部署案例</span><span class="sxs-lookup"><span data-stu-id="558d8-150">Deploy the scenario</span></span>

### <a name="prerequisites"></a><span data-ttu-id="558d8-151">必要條件</span><span class="sxs-lookup"><span data-stu-id="558d8-151">Prerequisites</span></span>

<span data-ttu-id="558d8-152">在部署 Resource Manager 範本之前，請遵循下列步驟：</span><span class="sxs-lookup"><span data-stu-id="558d8-152">Follow these steps before deploying the Resource Manager template:</span></span>

1. <span data-ttu-id="558d8-153">建立[服務主體][cycle-svcprin]，以便擷取 appId、displayName、名稱、密碼及租用戶。</span><span class="sxs-lookup"><span data-stu-id="558d8-153">Create a [service principal][cycle-svcprin] for retrieving the appId, displayName, name, password, and tenant.</span></span>
2. <span data-ttu-id="558d8-154">產生 [SSH 金鑰組][ cycle-ssh]，以便安全地登入 CycleCloud 伺服器。</span><span class="sxs-lookup"><span data-stu-id="558d8-154">Generate an [SSH key pair][cycle-ssh] to sign in securely to the CycleCloud server.</span></span>

    <!-- markdownlint-disable MD033 -->

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FCycleCloudCommunity%2Fcyclecloud_arm%2Fmaster%2Fazuredeploy.json" target="_blank">
        <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

    <!-- markdownlint-enable MD033 -->

3. <span data-ttu-id="558d8-155">[登入 Azure CycleCloud 伺服器][cycle-login]以設定和建立新叢集。</span><span class="sxs-lookup"><span data-stu-id="558d8-155">[Log into the CycleCloud server][cycle-login] to configure and create a new cluster.</span></span>
4. <span data-ttu-id="558d8-156">[建立叢集][cycle-create]。</span><span class="sxs-lookup"><span data-stu-id="558d8-156">[Create a cluster][cycle-create].</span></span>

<span data-ttu-id="558d8-157">Avere 快取是一個選擇性解決方案，可以大幅提高應用程式作業資料的讀取輸送量。</span><span class="sxs-lookup"><span data-stu-id="558d8-157">The Avere Cache is an optional solution that can drastically increase read throughput for the application job data.</span></span> <span data-ttu-id="558d8-158">Avere vFXT for Azure 可解決在雲端中執行這些企業 HPC 應用程式的問題，同時還能利用內部部署環境或 Azure Blob 儲存體中儲存的資料。</span><span class="sxs-lookup"><span data-stu-id="558d8-158">Avere vFXT for Azure solves the problem of running these enterprise HPC applications in the cloud while leveraging data stored on-premises or in Azure Blob storage.</span></span>

<span data-ttu-id="558d8-159">如果組織正在規劃兼具內部部署儲存體和雲端運算的混合式基礎結構，HPC 應用程式可以使用 NAS 裝置中儲存的資料「高載」至 Azure 中，並且視需要加快虛擬 CPU。</span><span class="sxs-lookup"><span data-stu-id="558d8-159">For organizations that are planning for a hybrid infrastructure with both on-premises storage and cloud computing, HPC applications can “burst” into Azure using data stored in NAS devices and spin up virtual CPUs as needed.</span></span> <span data-ttu-id="558d8-160">資料集永遠不會完全移至雲端。</span><span class="sxs-lookup"><span data-stu-id="558d8-160">The data set is never moved completely into the cloud.</span></span> <span data-ttu-id="558d8-161">在處理期間會使用 Avere 叢集來暫時快取要求的位元組。</span><span class="sxs-lookup"><span data-stu-id="558d8-161">The requested bytes are temporarily cached using an Avere cluster during processing.</span></span>

<span data-ttu-id="558d8-162">若要安裝及設定 Avere vFXT 安裝，請遵循 [Avere 安裝和設定指南][avere]。</span><span class="sxs-lookup"><span data-stu-id="558d8-162">To set up and configure an Avere vFXT installation, follow the [Avere Setup and Configuration guide][avere].</span></span>

## <a name="pricing"></a><span data-ttu-id="558d8-163">價格</span><span class="sxs-lookup"><span data-stu-id="558d8-163">Pricing</span></span>

<span data-ttu-id="558d8-164">使用 CycleCloud 伺服器執行 HPC 實作的成本會因許多因素而有所不同。</span><span class="sxs-lookup"><span data-stu-id="558d8-164">The cost of running an HPC implementation using CycleCloud server will vary depending on a number of factors.</span></span> <span data-ttu-id="558d8-165">比方說，CycleCloud 是依所用的計算時間量收費，而且主要和 CycleCloud 伺服器通常會持續配置和執行。</span><span class="sxs-lookup"><span data-stu-id="558d8-165">For example, CycleCloud is charged by the amount of compute time that is used, with the Master and CycleCloud server typically being constantly allocated and running.</span></span> <span data-ttu-id="558d8-166">執行執行節點的成本會取決於這些節點的運作時間長度以及所用的大小。</span><span class="sxs-lookup"><span data-stu-id="558d8-166">The cost of running the Execute nodes will depend on how long these are up and running as well as what size is used.</span></span> <span data-ttu-id="558d8-167">儲存體和網路功能的標準 Azure 費用也適用。</span><span class="sxs-lookup"><span data-stu-id="558d8-167">The normal Azure charges for storage and networking also apply.</span></span>

<span data-ttu-id="558d8-168">這個案例示範如何在 Azure 中執行 CFD 應用程式，因此機器需要僅適用於特定 VM 大小的 RDMA 功能。</span><span class="sxs-lookup"><span data-stu-id="558d8-168">This scenario shows how CFD applications can be run in Azure, so the machines will require RDMA functionality, which is only available on specific VM sizes.</span></span> <span data-ttu-id="558d8-169">以下是一個月每天持續配置八小時且資料輸出為 1 TB 的擴展集可能產生的成本範例。</span><span class="sxs-lookup"><span data-stu-id="558d8-169">The following are examples of costs that could be incurred for a scale set that is allocated continuously for eight hours per day for one month, with data egress of 1 TB.</span></span> <span data-ttu-id="558d8-170">其中也包含 Azure CycleCloud 伺服器和 Avere vFXT for Azure 安裝的定價：</span><span class="sxs-lookup"><span data-stu-id="558d8-170">It also includes pricing for the Azure CycleCloud server and the Avere vFXT for Azure install:</span></span>

- <span data-ttu-id="558d8-171">區域：北歐</span><span class="sxs-lookup"><span data-stu-id="558d8-171">Region: North Europe</span></span>
- <span data-ttu-id="558d8-172">Azure CycleCloud 伺服器：1 x 標準 D3 (4 個 CPU、14 GB 記憶體、標準 HDD 32 GB)</span><span class="sxs-lookup"><span data-stu-id="558d8-172">Azure CycleCloud Server: 1 x Standard D3 (4 x CPUs, 14 GB Memory, Standard HDD 32 GB)</span></span>
- <span data-ttu-id="558d8-173">Azure CycleCloud 主要伺服器：1 x 標準 D12 v (4 個 CPU、28 GB 記憶體、標準 HDD 32 GB)</span><span class="sxs-lookup"><span data-stu-id="558d8-173">Azure CycleCloud Master Server: 1 x Standard D12 v (4 x CPUs, 28 GB Memory, Standard HDD 32 GB)</span></span>
- <span data-ttu-id="558d8-174">Azure CycleCloud 節點陣列：10 x 標準 H16r (16 個 CPU、112 GB 記憶體)</span><span class="sxs-lookup"><span data-stu-id="558d8-174">Azure CycleCloud Node Array: 10 x Standard H16r (16 x CPUs, 112 GB Memory)</span></span>
- <span data-ttu-id="558d8-175">Azure 叢集上的 Avere vFXT：3 x D16s v3 (200 GB OS、進階 SSD 1-TB 資料磁碟)</span><span class="sxs-lookup"><span data-stu-id="558d8-175">Avere vFXT on Azure Cluster: 3 x D16s v3 (200 GB OS, Premium SSD 1-TB data disk)</span></span>
- <span data-ttu-id="558d8-176">資料輸出：1 TB</span><span class="sxs-lookup"><span data-stu-id="558d8-176">Data Egress: 1 TB</span></span>

<span data-ttu-id="558d8-177">針對以上所列的硬體，檢閱此[價格評估][pricing]。</span><span class="sxs-lookup"><span data-stu-id="558d8-177">Review this [price estimate][pricing] for the hardware listed above.</span></span>

## <a name="next-steps"></a><span data-ttu-id="558d8-178">後續步驟</span><span class="sxs-lookup"><span data-stu-id="558d8-178">Next Steps</span></span>

<span data-ttu-id="558d8-179">在您部署範例後，請深入了解 [Azure CycleCloud][cyclecloud]。</span><span class="sxs-lookup"><span data-stu-id="558d8-179">Once you've deployed the sample, learn more about [Azure CycleCloud][cyclecloud].</span></span>

## <a name="related-resources"></a><span data-ttu-id="558d8-180">相關資源</span><span class="sxs-lookup"><span data-stu-id="558d8-180">Related resources</span></span>

- <span data-ttu-id="558d8-181">[支援 RDMA 的機器執行個體][rdma]</span><span class="sxs-lookup"><span data-stu-id="558d8-181">[RDMA Capable Machine Instances][rdma]</span></span>
- <span data-ttu-id="558d8-182">[自訂 RDMA 執行個體 VM][rdma-custom]</span><span class="sxs-lookup"><span data-stu-id="558d8-182">[Customizing an RDMA Instance VM][rdma-custom]</span></span>

<!-- links -->
[architecture]: ./media/architecture-hpc-cfd.png
[calculator]: https://azure.com/e/
[availability]: /azure/architecture/checklist/availability
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[vmss]: /azure/virtual-machine-scale-sets/overview
[cyclecloud]: /azure/cyclecloud/
[rdma]: /azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances
[gpu]: /azure/virtual-machines/windows/sizes-gpu
[hpcsizes]: /azure/virtual-machines/windows/sizes-hpc
[vms]: /azure/virtual-machines/
[low-pri]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-low-priority
[batch]: /azure/batch/
[avere]: https://github.com/Azure/Avere/blob/master/README.md
[cycle-prereq]: /azure/cyclecloud/quickstart-install-cyclecloud#prerequisites
[cycle-svcprin]: /azure/cyclecloud/quickstart-install-cyclecloud#service-principal
[cycle-ssh]: /azure/cyclecloud/quickstart-install-cyclecloud#ssh-keypair
[cycle-login]: /azure/cyclecloud/quickstart-install-cyclecloud#log-into-the-cyclecloud-application-server
[cycle-create]: /azure/cyclecloud/quickstart-create-and-run-cluster
[rdma]: /azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances
[rdma-custom]: /azure/virtual-machines/linux/classic/rdma-cluster#customize-the-vm
[pricing]: https://azure.com/e/53030a04a2ab47a289156e2377a4247a
[cycle-scale]: /azure/cyclecloud/autoscale

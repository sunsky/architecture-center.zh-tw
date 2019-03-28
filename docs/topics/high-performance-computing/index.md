---
title: Azure 上的高效能運算 (HPC)
description: 在 Azure 上建置執行中 HPC 工作負載的指南
author: adamboeglin
ms.date: 2/4/2019
---
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD026 -->

# <a name="high-performance-computing-hpc-on-azure"></a><span data-ttu-id="068d3-103">Azure 上的高效能運算 (HPC)</span><span class="sxs-lookup"><span data-stu-id="068d3-103">High Performance Computing (HPC) on Azure</span></span>

## <a name="introduction-to-hpc"></a><span data-ttu-id="068d3-104">HPC 簡介</span><span class="sxs-lookup"><span data-stu-id="068d3-104">Introduction to HPC</span></span>

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.youtube.com/embed/rKURT32faJk]

<!-- markdownlint-enable MD034 -->

<span data-ttu-id="068d3-105">高效能運算 (HPC) (也稱為「巨量計算」) 會使用大量 CPU 或 GPU 型電腦來解決複雜的數學工作。</span><span class="sxs-lookup"><span data-stu-id="068d3-105">High Performance Computing (HPC), also called "Big Compute", uses a large number of CPU or GPU-based computers to solve complex mathematical tasks.</span></span>

<span data-ttu-id="068d3-106">許多產業都使用 HPC 來解決一些最困難的問題。</span><span class="sxs-lookup"><span data-stu-id="068d3-106">Many industries use HPC to solve some of their most difficult problems.</span></span>  <span data-ttu-id="068d3-107">這些產業包括下列工作負載：</span><span class="sxs-lookup"><span data-stu-id="068d3-107">These include workloads such as:</span></span>

- <span data-ttu-id="068d3-108">Genomics</span><span class="sxs-lookup"><span data-stu-id="068d3-108">Genomics</span></span>
- <span data-ttu-id="068d3-109">石油與天然氣模擬</span><span class="sxs-lookup"><span data-stu-id="068d3-109">Oil and gas simulations</span></span>
- <span data-ttu-id="068d3-110">財務</span><span class="sxs-lookup"><span data-stu-id="068d3-110">Finance</span></span>
- <span data-ttu-id="068d3-111">半導體設計</span><span class="sxs-lookup"><span data-stu-id="068d3-111">Semiconductor design</span></span>
- <span data-ttu-id="068d3-112">Engineering</span><span class="sxs-lookup"><span data-stu-id="068d3-112">Engineering</span></span>
- <span data-ttu-id="068d3-113">天氣模型</span><span class="sxs-lookup"><span data-stu-id="068d3-113">Weather modeling</span></span>

### <a name="how-is-hpc-different-on-the-cloud"></a><span data-ttu-id="068d3-114">HPC 在雲端上有何不同？</span><span class="sxs-lookup"><span data-stu-id="068d3-114">How is HPC different on the cloud?</span></span>

<span data-ttu-id="068d3-115">內部部署 HPC 系統與雲端中 HPC 系統之間的其中一項主要差異，就是依照需求動態新增及移除資源的能力。</span><span class="sxs-lookup"><span data-stu-id="068d3-115">One of the primary differences between an on-premise HPC system and one in the cloud is the ability for resources to dynamically be added and removed as they're needed.</span></span>  <span data-ttu-id="068d3-116">動態調整會因為瓶頸而移除計算功能，改而讓客戶能夠針對其作業需求適度調整其基礎結構的大小。</span><span class="sxs-lookup"><span data-stu-id="068d3-116">Dynamic scaling removes compute capacity as a bottleneck and instead allow customers to right size their infrastructure for the requirements of their jobs.</span></span>

<span data-ttu-id="068d3-117">下列文章提供此動態調整功能的詳細說明。</span><span class="sxs-lookup"><span data-stu-id="068d3-117">The following articles provide more detail about this dynamic scaling capability.</span></span>

- [<span data-ttu-id="068d3-118">大量計算架構樣式</span><span class="sxs-lookup"><span data-stu-id="068d3-118">Big Compute Architecture Style</span></span>](/azure/architecture/guide/architecture-styles/big-compute?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="068d3-119">自動調整最佳做法</span><span class="sxs-lookup"><span data-stu-id="068d3-119">Autoscaling best practices</span></span>](/azure/architecture/best-practices/auto-scaling?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

## <a name="implementation-checklist"></a><span data-ttu-id="068d3-120">實作檢查清單</span><span class="sxs-lookup"><span data-stu-id="068d3-120">Implementation checklist</span></span>

<span data-ttu-id="068d3-121">當您想要在 Azure 上實作自己的 HPC 解決方案時，請確定您已檢閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="068d3-121">As you're looking to implement your own HPC solution on Azure, ensure you're reviewed the following topics:</span></span>

<!-- markdownlint-disable MD032 -->

> [!div class="checklist"]
> - <span data-ttu-id="068d3-122">根據您的需求選擇適當的[架構](#infrastructure)</span><span class="sxs-lookup"><span data-stu-id="068d3-122">Choose the appropriate [architecture](#infrastructure) based on your requirements</span></span>
> - <span data-ttu-id="068d3-123">知道哪個[計算](#compute)選項適合您的工作負載</span><span class="sxs-lookup"><span data-stu-id="068d3-123">Know which [compute](#compute) options is right for your workload</span></span>
> - <span data-ttu-id="068d3-124">找出符合您需求的合適[儲存體](#storage)解決方案</span><span class="sxs-lookup"><span data-stu-id="068d3-124">Identify the right [storage](#storage) solution that meets your needs</span></span>
> - <span data-ttu-id="068d3-125">決定您要如何[管理](#management)您所有的資源</span><span class="sxs-lookup"><span data-stu-id="068d3-125">Decide how you're going to [manage](#management) all your resources</span></span>
> - <span data-ttu-id="068d3-126">針對雲端將您的[應用程式](#hpc-applications)最佳化</span><span class="sxs-lookup"><span data-stu-id="068d3-126">Optimize your [application](#hpc-applications) for the cloud</span></span>
> - <span data-ttu-id="068d3-127">[保護](#security)您的基礎結構</span><span class="sxs-lookup"><span data-stu-id="068d3-127">[Secure](#security) your Infrastructure</span></span>

<!-- markdownlint-enable MD032 -->

## <a name="infrastructure"></a><span data-ttu-id="068d3-128">基礎結構</span><span class="sxs-lookup"><span data-stu-id="068d3-128">Infrastructure</span></span>

<span data-ttu-id="068d3-129">建置 HPC 系統時需要許多基礎結構元件。</span><span class="sxs-lookup"><span data-stu-id="068d3-129">There are a number of infrastructure components necessary to build an HPC system.</span></span>  <span data-ttu-id="068d3-130">不論您選擇如何管理您的 HPC 工作負載，計算、儲存體和網路都會提供基礎元件。</span><span class="sxs-lookup"><span data-stu-id="068d3-130">Compute, Storage, and Networking provide the underlying components, no matter how you choose to manage your HPC workloads.</span></span>

### <a name="example-hpc-architectures"></a><span data-ttu-id="068d3-131">範例 HPC 架構</span><span class="sxs-lookup"><span data-stu-id="068d3-131">Example HPC architectures</span></span>

<span data-ttu-id="068d3-132">有幾個不同的方式可在 Azure 上設計及實作您的 HPC 架構。</span><span class="sxs-lookup"><span data-stu-id="068d3-132">There are a number of different ways to design and implement your HPC architecture on Azure.</span></span>  <span data-ttu-id="068d3-133">HPC 應用程式可擴展成上千個計算核心，以擴充內部部署叢集或以 100% 雲端原生解決方案的方式執行。</span><span class="sxs-lookup"><span data-stu-id="068d3-133">HPC applications can scale to thousands of compute cores, extend on-premises clusters, or run as a 100% cloud native solution.</span></span>

<span data-ttu-id="068d3-134">下列案例概述幾個常見的 HPC 解決方案建置方式。</span><span class="sxs-lookup"><span data-stu-id="068d3-134">The following scenarios outline a few of the common ways HPC solutions are built.</span></span>

<ul class="columns is-multiline has-margin-left-none has-margin-bottom-none has-padding-top-medium">
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/example-scenario/apps/hpc-saas?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="../../example-scenario/apps/media/architecture-hpc-saas.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="068d3-135">Azure 上的電腦輔助工程服務</span><span class="sxs-lookup"><span data-stu-id="068d3-135">Computer-aided engineering services on Azure</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="068d3-136">為 Azure 上的電腦輔助工程 (CAE) 提供軟體即服務 (SaaS) 平台。</span><span class="sxs-lookup"><span data-stu-id="068d3-136">Provide a software-as-a-service (SaaS) platform for computer-aided engineering (CAE) on Azure.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/example-scenario/infrastructure/hpc-cfd?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="../../example-scenario/infrastructure/media/architecture-hpc-cfd.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="068d3-137">Azure 上的計算流體力學 (CFD) 模擬</span><span class="sxs-lookup"><span data-stu-id="068d3-137">Computational fluid dynamics (CFD) simulations on Azure</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="068d3-138">在 Azure 上執行計算流體力學 (CFD) 模擬。</span><span class="sxs-lookup"><span data-stu-id="068d3-138">Execute computational fluid dynamics (CFD) simulations on Azure.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/example-scenario/infrastructure/video-rendering?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="../../example-scenario/infrastructure/media/architecture-video-rendering.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="068d3-139">Azure 上的 3D 影片轉譯</span><span class="sxs-lookup"><span data-stu-id="068d3-139">3D video rendering on Azure</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="068d3-140">使用 Azure Batch 服務在 Azure 中執行原生 HPC 工作負載</span><span class="sxs-lookup"><span data-stu-id="068d3-140">Run native HPC workloads in Azure using the Azure Batch service</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
</ul>

### <a name="compute"></a><span data-ttu-id="068d3-141">計算</span><span class="sxs-lookup"><span data-stu-id="068d3-141">Compute</span></span>

<span data-ttu-id="068d3-142">Azure 提供針對 CPU 和 GPU 密集型工作負載最佳化的各種大小。</span><span class="sxs-lookup"><span data-stu-id="068d3-142">Azure offers a range of sizes that are optimized for both CPU & GPU intensive workloads.</span></span>

#### <a name="cpu-based-virtual-machines"></a><span data-ttu-id="068d3-143">CPU 型虛擬機器</span><span class="sxs-lookup"><span data-stu-id="068d3-143">CPU-based virtual machines</span></span>

- [<span data-ttu-id="068d3-144">Linux VM</span><span class="sxs-lookup"><span data-stu-id="068d3-144">Linux VMs</span></span>](/azure/virtual-machines/linux/sizes-hpc?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- <span data-ttu-id="068d3-145">[Windows VM](/azure/virtual-machines/windows/sizes-hpc?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) 的 VM</span><span class="sxs-lookup"><span data-stu-id="068d3-145">[Windows VM's](/azure/virtual-machines/windows/sizes-hpc?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) VMs</span></span>
  
#### <a name="gpu-enabled-virtual-machines"></a><span data-ttu-id="068d3-146">已啟用 GPU 的虛擬機器</span><span class="sxs-lookup"><span data-stu-id="068d3-146">GPU-enabled virtual machines</span></span>

<span data-ttu-id="068d3-147">N 系列 VM 功能 NVIDIA GPU 是專為需要大量運算或需要大量圖形的應用程式所設計，包括人工地智慧 (AI) 學習和視覺效果。</span><span class="sxs-lookup"><span data-stu-id="068d3-147">N-series VMs feature NVIDIA GPUs designed for compute-intensive or graphics-intensive applications including artificial intelligence (AI) learning and visualization.</span></span>

- [<span data-ttu-id="068d3-148">Linux VM</span><span class="sxs-lookup"><span data-stu-id="068d3-148">Linux VMs</span></span>](/azure/virtual-machines/linux/sizes-gpu?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="068d3-149">Windows VM</span><span class="sxs-lookup"><span data-stu-id="068d3-149">Windows VMs</span></span>](/azure/virtual-machines/windows/sizes-gpu?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

### <a name="storage"></a><span data-ttu-id="068d3-150">儲存體</span><span class="sxs-lookup"><span data-stu-id="068d3-150">Storage</span></span>

<span data-ttu-id="068d3-151">大規模 Batch 和 HPC 工作負載所需要的資料儲存和存取會超過傳統雲端檔案系統的容量。</span><span class="sxs-lookup"><span data-stu-id="068d3-151">Large-scale Batch and HPC workloads have demands for data storage and access that exceed the capabilities of traditional cloud file systems.</span></span>  <span data-ttu-id="068d3-152">有數個解決方案可管理 Azure 上 HPC 應用程式的速度和容量需求</span><span class="sxs-lookup"><span data-stu-id="068d3-152">There are a number of solutions to manage both the speed and capacity needs of HPC applications on Azure</span></span>

- <span data-ttu-id="068d3-153">[Avere vFXT](https://azure.microsoft.com/services/storage/avere-vfxt/) 可供更快速、更容易取得資料儲存體，以在邊緣執行高效能運算</span><span class="sxs-lookup"><span data-stu-id="068d3-153">[Avere vFXT](https://azure.microsoft.com/services/storage/avere-vfxt/) for faster, more accessible data storage for high-performance computing at the edge</span></span>
- [<span data-ttu-id="068d3-154">BeeGFS</span><span class="sxs-lookup"><span data-stu-id="068d3-154">BeeGFS</span></span>](https://azure.microsoft.com/resources/implement-glusterfs-on-azure/)
- [<span data-ttu-id="068d3-155">儲存體最佳化的虛擬機器</span><span class="sxs-lookup"><span data-stu-id="068d3-155">Storage Optimized Virtual Machines</span></span>](/azure/virtual-machines/windows/sizes-storage?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="068d3-156">Blob、資料表和佇列儲存體</span><span class="sxs-lookup"><span data-stu-id="068d3-156">Blob, table, and queue storage</span></span>](/azure/storage/storage-introduction?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="068d3-157">Azure SMB 檔案儲存體</span><span class="sxs-lookup"><span data-stu-id="068d3-157">Azure SMB File storage</span></span>](/azure/storage/files/storage-files-introduction?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="068d3-158">Intel Cloud Edition Lustre</span><span class="sxs-lookup"><span data-stu-id="068d3-158">Intel Cloud Edition Lustre</span></span>](https://azuremarketplace.microsoft.com/marketplace/apps/intel.intel-cloud-edition-gs)

<span data-ttu-id="068d3-159">如需比較 Azure 上 Lustre、GlusterFS 和 BeeGFS 的詳細資訊，請檢閱 [Azure 電子書上的平行檔案系統](https://blogs.msdn.microsoft.com/azurecat/2018/06/11/azurecat-ebook-parallel-virtual-file-systems-on-microsoft-azure/)</span><span class="sxs-lookup"><span data-stu-id="068d3-159">For more information comparing Lustre, GlusterFS, and BeeGFS on Azure, review the [Parallel Files Systems on Azure eBook](https://blogs.msdn.microsoft.com/azurecat/2018/06/11/azurecat-ebook-parallel-virtual-file-systems-on-microsoft-azure/)</span></span>

### <a name="networking"></a><span data-ttu-id="068d3-160">網路功能</span><span class="sxs-lookup"><span data-stu-id="068d3-160">Networking</span></span>

<span data-ttu-id="068d3-161">H16r、H16mr、A8 和 A9 VM 可以連線到高輸送量後端 RDMA 網路。</span><span class="sxs-lookup"><span data-stu-id="068d3-161">H16r, H16mr, A8, and A9 VMs can connect to a high throughput back-end RDMA network.</span></span> <span data-ttu-id="068d3-162">此網路可以改善 Microsoft MPI 或 Intel MPI 下執行的緊密結合平行應用程式效能。</span><span class="sxs-lookup"><span data-stu-id="068d3-162">This network can improve the performance of tightly coupled parallel applications running under Microsoft MPI or Intel MPI.</span></span>

- [<span data-ttu-id="068d3-163">支援 RDMA 的執行個體</span><span class="sxs-lookup"><span data-stu-id="068d3-163">RDMA Capable Instances</span></span>](/azure/virtual-machines/windows/sizes-hpc?context=/azure/architecture/topics/high-performance-computing/context/hpc-context#rdma-capable-instances)
- [<span data-ttu-id="068d3-164">虛擬網路</span><span class="sxs-lookup"><span data-stu-id="068d3-164">Virtual Network</span></span>](/azure/virtual-network/virtual-networks-overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="068d3-165">ExpressRoute</span><span class="sxs-lookup"><span data-stu-id="068d3-165">ExpressRoute</span></span>](/azure/expressroute/expressroute-introduction?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

## <a name="management"></a><span data-ttu-id="068d3-166">管理性</span><span class="sxs-lookup"><span data-stu-id="068d3-166">Management</span></span>

### <a name="do-it-yourself"></a><span data-ttu-id="068d3-167">自己動手做</span><span class="sxs-lookup"><span data-stu-id="068d3-167">Do-it-yourself</span></span>

<span data-ttu-id="068d3-168">在 Azure 上從頭開始建置 HPC 系統隨可提供大量彈性，但通常需要密集維護。</span><span class="sxs-lookup"><span data-stu-id="068d3-168">Building an HPC system from scratch on Azure offers a significant amount of flexibility, but is often very maintenance intensive.</span></span>  

1. <span data-ttu-id="068d3-169">在 Azure 虛擬機器或[虛擬機器擴展集](/azure/virtual-machine-scale-sets/overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)中設定自己的叢集環境。</span><span class="sxs-lookup"><span data-stu-id="068d3-169">Set up your own cluster environment in Azure virtual machines or [virtual machine scale sets](/azure/virtual-machine-scale-sets/overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context).</span></span>
2. <span data-ttu-id="068d3-170">使用 Azure Resource Manager 範本來部署前置[工作負載管理員](#workload-managers)基礎結構和[應用程式](#hpc-applications)。</span><span class="sxs-lookup"><span data-stu-id="068d3-170">Use Azure Resource Manager templates to deploy leading [workload managers](#workload-managers), infrastructure, and [applications](#hpc-applications).</span></span>
3. <span data-ttu-id="068d3-171">選擇 HPC 和 GPU [VM 大小](#compute)，包括 MPI 或 GPU 工作負載的特製化硬體和網路連線。</span><span class="sxs-lookup"><span data-stu-id="068d3-171">Choose HPC and GPU [VM sizes](#compute) that include specialized hardware and network connections for MPI or GPU workloads.</span></span>
4. <span data-ttu-id="068d3-172">為 I/O 密集的工作負載新增[高效能儲存體](#storage)。</span><span class="sxs-lookup"><span data-stu-id="068d3-172">Add [high performance storage](#storage) for I/O-intensive workloads.</span></span>

### <a name="hybrid-and-cloud-bursting"></a><span data-ttu-id="068d3-173">混合式和雲端負載平衡</span><span class="sxs-lookup"><span data-stu-id="068d3-173">Hybrid and cloud Bursting</span></span>

<span data-ttu-id="068d3-174">如果您有想要連線到 Azure 的現有內部部署 HPC 系統，則有許多資源可協助您上手。</span><span class="sxs-lookup"><span data-stu-id="068d3-174">If you have an existing on-premise HPC system that you'd like to connect to Azure, there are a number of resources to help get you started.</span></span>

<span data-ttu-id="068d3-175">首先，檢閱文件中的[將內部部署網路連線到 Azure 的選項](/azure/architecture/reference-architectures/hybrid-networking/?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)一文。</span><span class="sxs-lookup"><span data-stu-id="068d3-175">First, review the [Options for connecting an on-premises network to Azure](/azure/architecture/reference-architectures/hybrid-networking/?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) article in the documentation.</span></span>  <span data-ttu-id="068d3-176">從那裡，您可以取得這些連線選項的資訊：</span><span class="sxs-lookup"><span data-stu-id="068d3-176">From there, you may want information on these connectivity options:</span></span>

<ul class="columns is-multiline has-margin-left-none has-margin-bottom-none has-padding-top-medium">
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/reference-architectures/hybrid-networking/vpn?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="/azure/architecture/reference-architectures/hybrid-networking/images/vpn.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="068d3-177">使用 VPN 閘道將內部部署網路連線至 Azure</span><span class="sxs-lookup"><span data-stu-id="068d3-177">Connect an on-premises network to Azure using a VPN gateway</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="068d3-178">此參考架構會示範如何使用網站對網站虛擬私人網路 (VPN)，將內部部署網路擴充至 Azure。</span><span class="sxs-lookup"><span data-stu-id="068d3-178">This reference architecture shows how to extend an on-premises network to Azure, using a site-to-site virtual private network (VPN).</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/reference-architectures/hybrid-networking/expressroute?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="/azure/architecture/reference-architectures/hybrid-networking/images/expressroute.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="068d3-179">使用 ExpressRoute 將內部部署網路連線至 Azure</span><span class="sxs-lookup"><span data-stu-id="068d3-179">Connect an on-premises network to Azure using ExpressRoute</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="068d3-180">ExpressRoute 連線會透過第三方連線提供者，使用私人的專用連線。</span><span class="sxs-lookup"><span data-stu-id="068d3-180">ExpressRoute connections use a private, dedicated connection through a third-party connectivity provider.</span></span> <span data-ttu-id="068d3-181">私人連線會將內部部署網路延伸到 Azure。</span><span class="sxs-lookup"><span data-stu-id="068d3-181">The private connection extends your on-premises network into Azure.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/reference-architectures/hybrid-networking/expressroute-vpn-failover?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="/azure/architecture/reference-architectures/hybrid-networking/images/expressroute-vpn-failover.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="068d3-182">使用 ExpressRoute 搭配 VPN 容錯移轉，將內部部署網路連線至 Azure</span><span class="sxs-lookup"><span data-stu-id="068d3-182">Connect an on-premises network to Azure using ExpressRoute with VPN failover</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="068d3-183">跨越使用 ExpressRoute 搭配 VPN 閘道容錯移轉連線的 Azure 虛擬網路與內部部署網路，實作可用性高且安全的站對站網路架構。</span><span class="sxs-lookup"><span data-stu-id="068d3-183">Implement a highly available and secure site-to-site network architecture that spans an Azure virtual network and an on-premises network connected using ExpressRoute with VPN gateway failover.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
</ul>

<span data-ttu-id="068d3-184">安全地建立網路連線後，您即可透過現有[工作負載管理員](#workload-managers)的負載平衡功能，開始隨選使用雲端計算資源。</span><span class="sxs-lookup"><span data-stu-id="068d3-184">Once network connectivity is securely established, you can start using cloud compute resources on-demand with the bursting capabilities of your existing [workload manager](#workload-managers).</span></span>

### <a name="marketplace-solutions"></a><span data-ttu-id="068d3-185">Marketplace 解決方案</span><span class="sxs-lookup"><span data-stu-id="068d3-185">Marketplace solutions</span></span>

<span data-ttu-id="068d3-186">[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) 中提供許多工作負載管理員。</span><span class="sxs-lookup"><span data-stu-id="068d3-186">There are a number of workload managers offered in the [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).</span></span>

- [<span data-ttu-id="068d3-187">RogueWave CentOS 型 HPC</span><span class="sxs-lookup"><span data-stu-id="068d3-187">RogueWave CentOS-based HPC</span></span>](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
- [<span data-ttu-id="068d3-188">適用於 HPC 的 SUSE Linux Enterprise Server</span><span class="sxs-lookup"><span data-stu-id="068d3-188">SUSE Linux Enterprise Server for HPC</span></span>](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
- [<span data-ttu-id="068d3-189">TIBCO Grid Server Engine</span><span class="sxs-lookup"><span data-stu-id="068d3-189">TIBCO Grid Server Engine</span></span>](https://azuremarketplace.microsoft.com/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
- [<span data-ttu-id="068d3-190">適用於 Windows 和 Linux 的 Azure 資料科學 VM</span><span class="sxs-lookup"><span data-stu-id="068d3-190">Azure Data Science VM for Windows and Linux</span></span>](/azure/machine-learning/data-science-virtual-machine/overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="068d3-191">D3View</span><span class="sxs-lookup"><span data-stu-id="068d3-191">D3View</span></span>](https://azuremarketplace.microsoft.com/marketplace/apps/xfinityinc.d3view-v5?tab=Overview)
- [<span data-ttu-id="068d3-192">UberCloud</span><span class="sxs-lookup"><span data-stu-id="068d3-192">UberCloud</span></span>](https://azure.microsoft.com/search/marketplace/?q=ubercloud)

### <a name="azure-batch"></a><span data-ttu-id="068d3-193">Azure Batch</span><span class="sxs-lookup"><span data-stu-id="068d3-193">Azure Batch</span></span>

<span data-ttu-id="068d3-194">[Azure Batch](/azure/batch/batch-technical-overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) 是一項平台服務，可用於在雲端有效地執行大規模的平行和高效能運算 (HPC) 應用程式。</span><span class="sxs-lookup"><span data-stu-id="068d3-194">[Azure Batch](/azure/batch/batch-technical-overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) is a platform service for running large-scale parallel and high-performance computing (HPC) applications efficiently in the cloud.</span></span> <span data-ttu-id="068d3-195">Azure Batch 可排程要在受控集區虛擬機器上執行的需要大量運算工作，而且可以調整運算資源以符合工作的需求。</span><span class="sxs-lookup"><span data-stu-id="068d3-195">Azure Batch schedules compute-intensive work to run on a managed pool of virtual machines, and can automatically scale compute resources to meet the needs of your jobs.</span></span>

<span data-ttu-id="068d3-196">SaaS 提供者或開發人員可使用 Batch SDK 和工具，將 HPC 應用程式或容器工作負載與 Azure 進行整合、將資料暫存至 Azure，並建置作業執行管線。</span><span class="sxs-lookup"><span data-stu-id="068d3-196">SaaS providers or developers can use the Batch SDKs and tools to integrate HPC applications or container workloads with Azure, stage data to Azure, and build job execution pipelines.</span></span>

### <a name="azure-cyclecloud"></a><span data-ttu-id="068d3-197">Azure CycleCloud</span><span class="sxs-lookup"><span data-stu-id="068d3-197">Azure CycleCloud</span></span>

<span data-ttu-id="068d3-198">[Azure CycleCloud](/azure/cyclecloud/?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) 在 Azure 上使用任何作業排程器 (如 Slurm、Grid Engine、HPC Pack、HTCondor、LSF、PBS Pro 或 Symphony) 管理 HPC 工作負載的最簡單方法</span><span class="sxs-lookup"><span data-stu-id="068d3-198">[Azure CycleCloud](/azure/cyclecloud/?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) Provides the simplest way to manage HPC workloads using any scheduler (like Slurm, Grid Engine, HPC Pack, HTCondor, LSF, PBS Pro, or Symphony), on Azure</span></span>

<span data-ttu-id="068d3-199">CycleCloud 可讓您：</span><span class="sxs-lookup"><span data-stu-id="068d3-199">CycleCloud allows you to:</span></span>

- <span data-ttu-id="068d3-200">部署完整叢集和其他資源，包括排程器、計算 VM、儲存體、網路和快取</span><span class="sxs-lookup"><span data-stu-id="068d3-200">Deploy full clusters and other resources, including scheduler, compute VMs, storage, networking, and cache</span></span>
- <span data-ttu-id="068d3-201">協調作業、資料和雲端工作流程</span><span class="sxs-lookup"><span data-stu-id="068d3-201">Orchestrate job, data, and cloud workflows</span></span>
- <span data-ttu-id="068d3-202">讓管理員能完全控制哪些使用者可執行作業，以及執行位置和成本</span><span class="sxs-lookup"><span data-stu-id="068d3-202">Give admins full control over which users can run jobs, as well as where and at what cost</span></span>
- <span data-ttu-id="068d3-203">透過進階原則和治理功能 (包括成本控制、Active Directory 整合、監視和報告) 自訂叢集並予以最佳化</span><span class="sxs-lookup"><span data-stu-id="068d3-203">Customize and optimize clusters through advanced policy and governance features, including cost controls, Active Directory integration, monitoring, and reporting</span></span>
- <span data-ttu-id="068d3-204">使用目前的作業排程器和應用程式，並無需修改</span><span class="sxs-lookup"><span data-stu-id="068d3-204">Use your current job scheduler and applications without modification</span></span>
- <span data-ttu-id="068d3-205">針對各種 HPC 工作負載和產業，利用內建自動調整和經過測試的參考架構</span><span class="sxs-lookup"><span data-stu-id="068d3-205">Take advantage of built-in autoscaling and battle-tested reference architectures for a wide range of HPC workloads and industries</span></span>

### <a name="workload-managers"></a><span data-ttu-id="068d3-206">工作負載管理員</span><span class="sxs-lookup"><span data-stu-id="068d3-206">Workload managers</span></span>

<span data-ttu-id="068d3-207">下列是可以在 Azure 基礎結構中執行的叢集和工作負載管理員範例。</span><span class="sxs-lookup"><span data-stu-id="068d3-207">The following are examples of cluster and workload managers that can run in Azure infrastructure.</span></span> <span data-ttu-id="068d3-208">在 Azure VM 中建立獨立叢集，或從內部部署叢集高載至 Azure VM。</span><span class="sxs-lookup"><span data-stu-id="068d3-208">Create stand-alone clusters in Azure VMs or burst to Azure VMs from an on-premises cluster.</span></span>

- [<span data-ttu-id="068d3-209">Alces Flight Compute</span><span class="sxs-lookup"><span data-stu-id="068d3-209">Alces Flight Compute</span></span>](https://azuremarketplace.microsoft.com/marketplace/apps/alces-flight-limited.alces-flight-compute-solo?tab=Overview)
- [<span data-ttu-id="068d3-210">TIBCO DataSynapse GridServer</span><span class="sxs-lookup"><span data-stu-id="068d3-210">TIBCO DataSynapse GridServer</span></span>](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/)
- [<span data-ttu-id="068d3-211">Bright Cluster Manager</span><span class="sxs-lookup"><span data-stu-id="068d3-211">Bright Cluster Manager</span></span>](http://www.brightcomputing.com/technology-partners/microsoft)
- [<span data-ttu-id="068d3-212">IBM Spectrum Symphony 和 Symphony LSF</span><span class="sxs-lookup"><span data-stu-id="068d3-212">IBM Spectrum Symphony and Symphony LSF</span></span>](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
- [<span data-ttu-id="068d3-213">PBS Pro</span><span class="sxs-lookup"><span data-stu-id="068d3-213">PBS Pro</span></span>](http://pbspro.org)
- [<span data-ttu-id="068d3-214">Altair</span><span class="sxs-lookup"><span data-stu-id="068d3-214">Altair</span></span>](http://www.altair.com/)
- [<span data-ttu-id="068d3-215">Rescale</span><span class="sxs-lookup"><span data-stu-id="068d3-215">Rescale</span></span>](https://www.rescale.com/azure/)
- [<span data-ttu-id="068d3-216">Microsoft HPC Pack</span><span class="sxs-lookup"><span data-stu-id="068d3-216">Microsoft HPC Pack</span></span>](https://technet.microsoft.com/library/mt744885.aspx)
  - [<span data-ttu-id="068d3-217">適用於 Windows 的 HPC 套件</span><span class="sxs-lookup"><span data-stu-id="068d3-217">HPC Pack for Windows</span></span>](/azure/virtual-machines/windows/hpcpack-cluster-options?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
  - [<span data-ttu-id="068d3-218">適用於 Linux 的 HPC 套件</span><span class="sxs-lookup"><span data-stu-id="068d3-218">HPC Pack for Linux</span></span>](/azure/virtual-machines/linux/hpcpack-cluster-options?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

#### <a name="containers"></a><span data-ttu-id="068d3-219">容器</span><span class="sxs-lookup"><span data-stu-id="068d3-219">Containers</span></span>

<span data-ttu-id="068d3-220">容器也可用來管理一些 HPC 工作負載。</span><span class="sxs-lookup"><span data-stu-id="068d3-220">Containers can also be used to manage some HPC workloads.</span></span>  <span data-ttu-id="068d3-221">Azure Kubernetes Service (AKS) 等服務可讓您輕鬆地在 Azure 中部署受控 Kubernetes 叢集。</span><span class="sxs-lookup"><span data-stu-id="068d3-221">Services like the Azure Kubernetes Service (AKS) makes it simple to deploy a managed Kubernetes cluster in Azure.</span></span>

- [<span data-ttu-id="068d3-222">Azure Kubernetes Service (AKS)</span><span class="sxs-lookup"><span data-stu-id="068d3-222">Azure Kubernetes Service (AKS)</span></span>](/azure/aks/intro-kubernetes?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="068d3-223">容器登錄</span><span class="sxs-lookup"><span data-stu-id="068d3-223">Container Registry</span></span>](/azure/container-registry/container-registry-intro?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

## <a name="cost-management"></a><span data-ttu-id="068d3-224">成本管理</span><span class="sxs-lookup"><span data-stu-id="068d3-224">Cost management</span></span>

<span data-ttu-id="068d3-225">您可以透過一些不同方法在 Azure 上管理您的 HPC 成本。</span><span class="sxs-lookup"><span data-stu-id="068d3-225">Managing your HPC cost on Azure can be done through a few different ways.</span></span>  <span data-ttu-id="068d3-226">確定您已檢閱 [Azure 購買選項](https://azure.microsoft.com/pricing/purchase-options/)，以尋找最適合貴組織的方法。</span><span class="sxs-lookup"><span data-stu-id="068d3-226">Ensure you've reviewed the [Azure purchasing options](https://azure.microsoft.com/pricing/purchase-options/) to find the method that works best for your organization.</span></span>

<span data-ttu-id="068d3-227">[低優先順序 VM](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-low-priority?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) 可讓您以大幅降低的成本使用我們未運用的容量。</span><span class="sxs-lookup"><span data-stu-id="068d3-227">[Low priority VMs](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-low-priority?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) allow you to take advantage of our unutilized capacity at a significant cost savings.</span></span>

## <a name="security"></a><span data-ttu-id="068d3-228">安全性</span><span class="sxs-lookup"><span data-stu-id="068d3-228">Security</span></span>

<span data-ttu-id="068d3-229">如需 Azure 上的安全性最佳做法概觀，請檢閱 [Azure 安全性文件](/azure/security/azure-security?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)。</span><span class="sxs-lookup"><span data-stu-id="068d3-229">For an overview of security best practices on Azure, review the [Azure Security Documentation](/azure/security/azure-security?context=/azure/architecture/topics/high-performance-computing/context/hpc-context).</span></span>  

<span data-ttu-id="068d3-230">除了[雲端負載平衡](#hybrid-and-cloud-bursting)區段中可用的網路組態，您可以實作中樞/輪輻組態，以隔離您的計算資源：</span><span class="sxs-lookup"><span data-stu-id="068d3-230">In addition to the network configurations available in the [Cloud Bursting](#hybrid-and-cloud-bursting) section, you may want to implement a hub/spoke configuration to isolate your compute resources:</span></span>

<ul class="columns is-multiline has-margin-left-none has-margin-bottom-none has-padding-top-medium">
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="/azure/architecture/reference-architectures/hybrid-networking/images/hub-spoke.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="068d3-231">在 Azure 中實作中樞輪輻網路拓撲</span><span class="sxs-lookup"><span data-stu-id="068d3-231">Implement a hub-spoke network topology in Azure</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="068d3-232">「中樞」是 Azure 中的虛擬網路 (VNet)，可當作內部部署網路的連線中心點。</span><span class="sxs-lookup"><span data-stu-id="068d3-232">The hub is a virtual network (VNet) in Azure that acts as a central point of connectivity to your on-premises network.</span></span> <span data-ttu-id="068d3-233">輪輻是與中樞對等的 VNet，可用於隔離工作負載。</span><span class="sxs-lookup"><span data-stu-id="068d3-233">The spokes are VNets that peer with the hub, and can be used to isolate workloads.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/reference-architectures/hybrid-networking/shared-services?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="/azure/architecture/reference-architectures/hybrid-networking/images/shared-services.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="068d3-234">在 Azure 中實作中樞輪輻網路拓撲與共用服務</span><span class="sxs-lookup"><span data-stu-id="068d3-234">Implement a hub-spoke network topology with shared services in Azure</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="068d3-235">此參考架構是建置在中樞輪輻參考架構基礎上，以包含可以被所有輪輻取用之中樞中的共用服務。</span><span class="sxs-lookup"><span data-stu-id="068d3-235">This reference architecture builds on the hub-spoke reference architecture to include shared services in the hub that can be consumed by all spokes.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
</ul>

## <a name="hpc-applications"></a><span data-ttu-id="068d3-236">HPC 應用程式</span><span class="sxs-lookup"><span data-stu-id="068d3-236">HPC applications</span></span>

<span data-ttu-id="068d3-237">在 Azure 中執行自訂或商業 HPC 應用程式。</span><span class="sxs-lookup"><span data-stu-id="068d3-237">Run custom or commercial HPC applications in Azure.</span></span> <span data-ttu-id="068d3-238">本節中的數個範例會經過基準測試，以使用其他 VM 或運算核心有效地進行擴充。</span><span class="sxs-lookup"><span data-stu-id="068d3-238">Several examples in this section are benchmarked to scale efficiently with additional VMs or compute cores.</span></span> <span data-ttu-id="068d3-239">請瀏覽 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) 以取得可立即部署的解決方案。</span><span class="sxs-lookup"><span data-stu-id="068d3-239">Visit the [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) for ready-to-deploy solutions.</span></span>

> [!NOTE]
> <span data-ttu-id="068d3-240">請向廠商確認任何商業應用程式在雲端中的執行授權或其他限制。</span><span class="sxs-lookup"><span data-stu-id="068d3-240">Check with the vendor of any commercial application for licensing or other restrictions for running in the cloud.</span></span> <span data-ttu-id="068d3-241">並非所有廠商都提供隨用隨付授權。</span><span class="sxs-lookup"><span data-stu-id="068d3-241">Not all vendors offer pay-as-you-go licensing.</span></span> <span data-ttu-id="068d3-242">您可能需要視您的解決方案，在雲端中授權伺服器，或連線至內部部署授權伺服器。</span><span class="sxs-lookup"><span data-stu-id="068d3-242">You might need a licensing server in the cloud for your solution, or connect to an on-premises license server.</span></span>

### <a name="engineering-applications"></a><span data-ttu-id="068d3-243">工程應用程式</span><span class="sxs-lookup"><span data-stu-id="068d3-243">Engineering applications</span></span>

- [<span data-ttu-id="068d3-244">Altair RADIOSS</span><span class="sxs-lookup"><span data-stu-id="068d3-244">Altair RADIOSS</span></span>](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
- [<span data-ttu-id="068d3-245">ANSYS CFD</span><span class="sxs-lookup"><span data-stu-id="068d3-245">ANSYS CFD</span></span>](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
- [<span data-ttu-id="068d3-246">MATLAB Distributed Computing Server</span><span class="sxs-lookup"><span data-stu-id="068d3-246">MATLAB Distributed Computing Server</span></span>](/azure/virtual-machines/windows/matlab-mdcs-cluster?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="068d3-247">StarCCM+</span><span class="sxs-lookup"><span data-stu-id="068d3-247">StarCCM+</span></span>](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
- [<span data-ttu-id="068d3-248">OpenFOAM</span><span class="sxs-lookup"><span data-stu-id="068d3-248">OpenFOAM</span></span>](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)

### <a name="graphics-and-rendering"></a><span data-ttu-id="068d3-249">圖形和轉譯器</span><span class="sxs-lookup"><span data-stu-id="068d3-249">Graphics and rendering</span></span>

- <span data-ttu-id="068d3-250">Azure Batch 上的 [Autodesk Maya、3ds Max 和 Arnold](/azure/batch/batch-rendering-service?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)</span><span class="sxs-lookup"><span data-stu-id="068d3-250">[Autodesk Maya, 3ds Max, and Arnold](/azure/batch/batch-rendering-service?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) on Azure Batch</span></span>

### <a name="ai-and-deep-learning"></a><span data-ttu-id="068d3-251">AI 和深入學習</span><span class="sxs-lookup"><span data-stu-id="068d3-251">AI and deep learning</span></span>

- [<span data-ttu-id="068d3-252">Microsoft 辨識工具組</span><span class="sxs-lookup"><span data-stu-id="068d3-252">Microsoft Cognitive Toolkit</span></span>](/cognitive-toolkit/cntk-on-azure)
- [<span data-ttu-id="068d3-253">深入學習 VM</span><span class="sxs-lookup"><span data-stu-id="068d3-253">Deep Learning VM</span></span>](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
- [<span data-ttu-id="068d3-254">深入學習的 Batch Shipyard 訣竅</span><span class="sxs-lookup"><span data-stu-id="068d3-254">Batch Shipyard recipes for deep learning</span></span>](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)

### <a name="mpi-providers"></a><span data-ttu-id="068d3-255">MPI 提供者</span><span class="sxs-lookup"><span data-stu-id="068d3-255">MPI Providers</span></span>

- [<span data-ttu-id="068d3-256">Microsoft MPI</span><span class="sxs-lookup"><span data-stu-id="068d3-256">Microsoft MPI</span></span>](/message-passing-interface/microsoft-mpi)

## <a name="remote-visualization"></a><span data-ttu-id="068d3-257">遠端視覺效果</span><span class="sxs-lookup"><span data-stu-id="068d3-257">Remote visualization</span></span>

<ul class="columns is-multiline has-margin-left-none has-margin-bottom-none has-padding-top-medium">
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/example-scenario/infrastructure/linux-vdi-citrix?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="../../example-scenario/infrastructure/media/azure-citrix-sample-diagram.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="068d3-258">使用 Citrix 的 Linux 虛擬桌面</span><span class="sxs-lookup"><span data-stu-id="068d3-258">Linux virtual desktops with Citrix</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="068d3-259">在 Azure 上使用 Citrix 建置適用於 Linux 桌面的 VDI 環境。</span><span class="sxs-lookup"><span data-stu-id="068d3-259">Build a VDI environment for Linux Desktops using Citrix on Azure.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
</ul>

## <a name="performance-benchmarks"></a><span data-ttu-id="068d3-260">效能評定</span><span class="sxs-lookup"><span data-stu-id="068d3-260">Performance Benchmarks</span></span>

- [<span data-ttu-id="068d3-261">計算評定</span><span class="sxs-lookup"><span data-stu-id="068d3-261">Compute benchmarks</span></span>](/azure/virtual-machines/windows/compute-benchmark-scores?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

## <a name="customer-stories"></a><span data-ttu-id="068d3-262">客戶案例</span><span class="sxs-lookup"><span data-stu-id="068d3-262">Customer stories</span></span>

<span data-ttu-id="068d3-263">有許多客戶在使用 Azure 處理其 HPC 工作負載方面非常成功。</span><span class="sxs-lookup"><span data-stu-id="068d3-263">There are a number of customers who have seen great success by using Azure for their HPC workloads.</span></span>  <span data-ttu-id="068d3-264">您可以在底下找到這些客戶的一些案例研究：</span><span class="sxs-lookup"><span data-stu-id="068d3-264">You can find a few of these customer case studies below:</span></span>

- [<span data-ttu-id="068d3-265">ANEO</span><span class="sxs-lookup"><span data-stu-id="068d3-265">ANEO</span></span>](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu)
- [<span data-ttu-id="068d3-266">AXA Global P&C</span><span class="sxs-lookup"><span data-stu-id="068d3-266">AXA Global P&C</span></span>](https://customers.microsoft.com/story/axa-global-p-and-c)
- [<span data-ttu-id="068d3-267">Axioma</span><span class="sxs-lookup"><span data-stu-id="068d3-267">Axioma</span></span>](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
- [<span data-ttu-id="068d3-268">d3View</span><span class="sxs-lookup"><span data-stu-id="068d3-268">d3View</span></span>](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
- [<span data-ttu-id="068d3-269">EFS</span><span class="sxs-lookup"><span data-stu-id="068d3-269">EFS</span></span>](https://customers.microsoft.com/story/efs-professionalservices-azure)
- [<span data-ttu-id="068d3-270">Hymans Robertson</span><span class="sxs-lookup"><span data-stu-id="068d3-270">Hymans Robertson</span></span>](https://customers.microsoft.com/story/hymans-robertson)
- [<span data-ttu-id="068d3-271">MetLife</span><span class="sxs-lookup"><span data-stu-id="068d3-271">MetLife</span></span>](https://enterprise.microsoft.com/customer-story/industries/insurance/metlife/)
- [<span data-ttu-id="068d3-272">Microsoft Research</span><span class="sxs-lookup"><span data-stu-id="068d3-272">Microsoft Research</span></span>](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
- [<span data-ttu-id="068d3-273">明德精算顧問有限公司</span><span class="sxs-lookup"><span data-stu-id="068d3-273">Milliman</span></span>](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
- [<span data-ttu-id="068d3-274">Mitsubishi UFJ Securities International</span><span class="sxs-lookup"><span data-stu-id="068d3-274">Mitsubishi UFJ Securities International</span></span>](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
- [<span data-ttu-id="068d3-275">NeuroInitiative</span><span class="sxs-lookup"><span data-stu-id="068d3-275">NeuroInitiative</span></span>](https://customers.microsoft.com/story/neuroinitiative-health-provider-azure)
- [<span data-ttu-id="068d3-276">Schlumberger</span><span class="sxs-lookup"><span data-stu-id="068d3-276">Schlumberger</span></span>](https://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
- [<span data-ttu-id="068d3-277">Towers Watson</span><span class="sxs-lookup"><span data-stu-id="068d3-277">Towers Watson</span></span>](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)

## <a name="other-important-information"></a><span data-ttu-id="068d3-278">其他重要資訊</span><span class="sxs-lookup"><span data-stu-id="068d3-278">Other important information</span></span>

- <span data-ttu-id="068d3-279">在嘗試執行大規模工作負載之前，先確定您的 [vCPU 配額](/azure/virtual-machines/linux/quotas?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)已經增加。</span><span class="sxs-lookup"><span data-stu-id="068d3-279">Ensure your [vCPU quota](/azure/virtual-machines/linux/quotas?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) has been increased before attempting to run large-scale workloads.</span></span>

## <a name="next-steps"></a><span data-ttu-id="068d3-280">後續步驟</span><span class="sxs-lookup"><span data-stu-id="068d3-280">Next steps</span></span>

<span data-ttu-id="068d3-281">如需最新公告，請參閱：</span><span class="sxs-lookup"><span data-stu-id="068d3-281">For the latest announcements, see:</span></span>

- [<span data-ttu-id="068d3-282">Microsoft HPC 和 Batch 小組部落格</span><span class="sxs-lookup"><span data-stu-id="068d3-282">Microsoft HPC and Batch team blog</span></span>](http://blogs.technet.com/b/windowshpc/)
- <span data-ttu-id="068d3-283">瀏覽 [Azure 部落格](https://azure.microsoft.com/blog/tag/hpc/)。</span><span class="sxs-lookup"><span data-stu-id="068d3-283">Visit the [Azure blog](https://azure.microsoft.com/blog/tag/hpc/).</span></span>

### <a name="microsoft-batch-examples"></a><span data-ttu-id="068d3-284">Microsoft Batch 範例</span><span class="sxs-lookup"><span data-stu-id="068d3-284">Microsoft Batch Examples</span></span>

<span data-ttu-id="068d3-285">這些教學課程將為您提供在 Microsoft Batch 上執行應用程式的詳細資訊</span><span class="sxs-lookup"><span data-stu-id="068d3-285">These tutorials will provide you with details on running applications on Microsoft Batch</span></span>

- [<span data-ttu-id="068d3-286">開始使用 Batch 進行開發</span><span class="sxs-lookup"><span data-stu-id="068d3-286">Get started developing with Batch</span></span>](/azure/batch/quick-run-dotnet?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="068d3-287">使用 Azure Batch 程式碼範例</span><span class="sxs-lookup"><span data-stu-id="068d3-287">Use Azure Batch code samples</span></span>](https://github.com/Azure/azure-batch-samples)
- [<span data-ttu-id="068d3-288">使用低優先順序的 VM 搭配 Batch</span><span class="sxs-lookup"><span data-stu-id="068d3-288">Use low-priority VMs with Batch</span></span>](/azure/batch/batch-low-pri-vms?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="068d3-289">使用 Batch Shipyard 執行容器化的 HPC 工作負載</span><span class="sxs-lookup"><span data-stu-id="068d3-289">Run containerized HPC workloads with Batch Shipyard</span></span>](https://github.com/Azure/batch-shipyard)
- [<span data-ttu-id="068d3-290">在 Batch 上執行平行的 R 工作負載</span><span class="sxs-lookup"><span data-stu-id="068d3-290">Run parallel R workloads on Batch</span></span>](https://github.com/Azure/doAzureParallel)
- [<span data-ttu-id="068d3-291">在 Batch 上執行隨選 Spark 作業</span><span class="sxs-lookup"><span data-stu-id="068d3-291">Run on-demand Spark jobs on Batch</span></span>](https://github.com/Azure/aztk)
- [<span data-ttu-id="068d3-292">在 Batch 集區中使用需要大量運算的 VM</span><span class="sxs-lookup"><span data-stu-id="068d3-292">Use compute-intensive VMs in Batch pools</span></span>](/azure/batch/batch-pool-compute-intensive-sizes?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
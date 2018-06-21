---
title: 大量計算架構樣式
description: 說明 Azure 上大量計算架構的優點、挑戰和最佳作法
author: MikeWasson
ms.openlocfilehash: b16be4133143d7d73062eeb280b44779c390f387
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
ms.locfileid: "24539780"
---
# <a name="big-compute-architecture-style"></a><span data-ttu-id="4748d-103">大量計算架構樣式</span><span class="sxs-lookup"><span data-stu-id="4748d-103">Big compute architecture style</span></span>

<span data-ttu-id="4748d-104">「大量計算」一詞描述需要大量核心 (通常數以千、百計) 的大型工作負載。</span><span class="sxs-lookup"><span data-stu-id="4748d-104">The term *big compute* describes large-scale workloads that require a large number of cores, often numbering in the hundreds or thousands.</span></span> <span data-ttu-id="4748d-105">案例包括影像轉譯、流體動態、財務風險模型、石油探勘、藥物設計、工程壓力分析，當然還有其他的。</span><span class="sxs-lookup"><span data-stu-id="4748d-105">Scenarios include image rendering, fluid dynamics, financial risk modeling, oil exploration, drug design, and engineering stress analysis, among others.</span></span>

![](./images/big-compute-logical.png)

<span data-ttu-id="4748d-106">以下是大量計算應用程式的一些 典型特性：</span><span class="sxs-lookup"><span data-stu-id="4748d-106">Here are some typical characteristics of big compute applications:</span></span>

- <span data-ttu-id="4748d-107">工作可以分割成多個離散的工作，這些工作可以同時分散到許多核心上執行。</span><span class="sxs-lookup"><span data-stu-id="4748d-107">The work can be split into discrete tasks, which can be run across many cores simultaneously.</span></span>
- <span data-ttu-id="4748d-108">每個工作有限。</span><span class="sxs-lookup"><span data-stu-id="4748d-108">Each task is finite.</span></span> <span data-ttu-id="4748d-109">它會取得一些輸入、執行一些處理、並產生輸出。</span><span class="sxs-lookup"><span data-stu-id="4748d-109">It takes some input, does some processing, and produces output.</span></span> <span data-ttu-id="4748d-110">整個應用程式會執行一段有限的時間 (幾分鐘至幾天)。</span><span class="sxs-lookup"><span data-stu-id="4748d-110">The entire application runs for a finite amount of time (minutes to days).</span></span> <span data-ttu-id="4748d-111">常見的模式是佈建一批數量龐大的核心，然後在應用程式完成後停轉至零。</span><span class="sxs-lookup"><span data-stu-id="4748d-111">A common pattern is to provision a large number of cores in a burst, and then spin down to zero once the application completes.</span></span> 
- <span data-ttu-id="4748d-112">應用程式不需要全年無休。</span><span class="sxs-lookup"><span data-stu-id="4748d-112">The application does not need to stay up 24/7.</span></span> <span data-ttu-id="4748d-113">不過，系統必須處理節點失敗或應用程式當機。</span><span class="sxs-lookup"><span data-stu-id="4748d-113">However, the system must handle node failures or application crashes.</span></span>
- <span data-ttu-id="4748d-114">某些應用程式的工作是獨立的，而且可平行執行。</span><span class="sxs-lookup"><span data-stu-id="4748d-114">For some applications, tasks are independent and can run in parallel.</span></span> <span data-ttu-id="4748d-115">其他應用程式的工作會緊密結合，這表示它們必須互動或交換中繼結果。</span><span class="sxs-lookup"><span data-stu-id="4748d-115">In other cases, tasks are tightly coupled, meaning they must interact or exchange intermediate results.</span></span> <span data-ttu-id="4748d-116">在此情況下，請考慮使用高速網路技術，例如 InfiniBand 和遠端直接記憶體存取 (RDMA)。</span><span class="sxs-lookup"><span data-stu-id="4748d-116">In that case, consider using high-speed networking technologies such as InfiniBand and remote direct memory access (RDMA).</span></span> 
- <span data-ttu-id="4748d-117">根據您的工作負載，您應使用可應付大量計算的 VM 大小 (H16r、H16mr 和 A9)。</span><span class="sxs-lookup"><span data-stu-id="4748d-117">Depending on your workload, you might use compute-intensive VM sizes (H16r, H16mr, and A9).</span></span>

## <a name="when-to-use-this-architecture"></a><span data-ttu-id="4748d-118">使用此架構的時機</span><span class="sxs-lookup"><span data-stu-id="4748d-118">When to use this architecture</span></span>

- <span data-ttu-id="4748d-119">計算密集的作業，例如模擬、數字密集運算。</span><span class="sxs-lookup"><span data-stu-id="4748d-119">Computationally intensive operations such as simulation and number crunching.</span></span>
- <span data-ttu-id="4748d-120">會密集運算、必須分散在多部電腦 (10-1000 部) 之 CPU 的模擬。</span><span class="sxs-lookup"><span data-stu-id="4748d-120">Simulations that are computationally intensive and must be split across CPUs in multiple computers (10-1000s).</span></span>
- <span data-ttu-id="4748d-121">會耗用一部電腦太多記憶體、必須分散在多部電腦的模擬。</span><span class="sxs-lookup"><span data-stu-id="4748d-121">Simulations that require too much memory for one computer, and must be split across multiple computers.</span></span>
- <span data-ttu-id="4748d-122">若在單一電腦上完成會花太多時間的長時間執行計算。</span><span class="sxs-lookup"><span data-stu-id="4748d-122">Long-running computations that would take too long to complete on a single computer.</span></span>
- <span data-ttu-id="4748d-123">必須執行 100 或 1000 次以上的小型計算，例如 Monte Carlo 模擬。</span><span class="sxs-lookup"><span data-stu-id="4748d-123">Smaller computations that must be run 100s or 1000s of times, such as Monte Carlo simulations.</span></span>

## <a name="benefits"></a><span data-ttu-id="4748d-124">優點</span><span class="sxs-lookup"><span data-stu-id="4748d-124">Benefits</span></span>

- <span data-ttu-id="4748d-125">運用 [embarrassingly parallel][embarrassingly-parallel] 處理而有高效能。</span><span class="sxs-lookup"><span data-stu-id="4748d-125">High performance with "[embarrassingly parallel][embarrassingly-parallel]" processing.</span></span>
- <span data-ttu-id="4748d-126">可以利用數百或數千部電腦核心，更快解決大型問題。</span><span class="sxs-lookup"><span data-stu-id="4748d-126">Can harness hundreds or thousands of computer cores to solve large problems faster.</span></span>
- <span data-ttu-id="4748d-127">存取專門的高效能硬體，搭配專用的高速 InfiniBand 網路。</span><span class="sxs-lookup"><span data-stu-id="4748d-127">Access to specialized high-performance hardware, with dedicated high-speed InfiniBand networks.</span></span>
- <span data-ttu-id="4748d-128">您可以視需要佈建 VM 來執行工作，然後終止它們。</span><span class="sxs-lookup"><span data-stu-id="4748d-128">You can provision VMs as needed to do work, and then tear them down.</span></span> 

## <a name="challenges"></a><span data-ttu-id="4748d-129">挑戰</span><span class="sxs-lookup"><span data-stu-id="4748d-129">Challenges</span></span>

- <span data-ttu-id="4748d-130">管理 VM 基礎結構。</span><span class="sxs-lookup"><span data-stu-id="4748d-130">Managing the VM infrastructure.</span></span>
- <span data-ttu-id="4748d-131">管理數字密集運算的磁碟區。</span><span class="sxs-lookup"><span data-stu-id="4748d-131">Managing the volume of number crunching.</span></span> 
- <span data-ttu-id="4748d-132">適時佈建數千個核心。</span><span class="sxs-lookup"><span data-stu-id="4748d-132">Provisioning thousands of cores in a timely manner.</span></span>
- <span data-ttu-id="4748d-133">針對緊密結合的工作，加入更多核心可以有些微回報。</span><span class="sxs-lookup"><span data-stu-id="4748d-133">For tightly coupled tasks, adding more cores can have diminishing returns.</span></span> <span data-ttu-id="4748d-134">您可能需要不斷實驗找出最佳的核心數目。</span><span class="sxs-lookup"><span data-stu-id="4748d-134">You may need to experiment to find the optimum number of cores.</span></span>

## <a name="big-compute-using-azure-batch"></a><span data-ttu-id="4748d-135">使用 Azure Batch 的大量計算</span><span class="sxs-lookup"><span data-stu-id="4748d-135">Big compute using Azure Batch</span></span>

<span data-ttu-id="4748d-136">[Azure Batch][batch] 是受管理服務，可用於執行大規模的高效能運算 (HPC) 應用程式。</span><span class="sxs-lookup"><span data-stu-id="4748d-136">[Azure Batch][batch] is a managed service for running large-scale high-performance computing (HPC) applications.</span></span>

<span data-ttu-id="4748d-137">使用 Azure Batch，要設定 VM 集區，並上傳應用程式和資料檔案。</span><span class="sxs-lookup"><span data-stu-id="4748d-137">Using Azure Batch, you configure a VM pool, and upload the applications and data files.</span></span> <span data-ttu-id="4748d-138">然後 Batch 服務會佈建 VM、將工作指派給 VM、執行工作，並監視進度。</span><span class="sxs-lookup"><span data-stu-id="4748d-138">Then the Batch service provisions the VMs, assign tasks to the VMs, runs the tasks, and monitors the progress.</span></span> <span data-ttu-id="4748d-139">Batch 可以自動相應放大 VM 來反應工作負載。</span><span class="sxs-lookup"><span data-stu-id="4748d-139">Batch can automatically scale out the VMs in response to the workload.</span></span> <span data-ttu-id="4748d-140">Batch 也提供作業排程。</span><span class="sxs-lookup"><span data-stu-id="4748d-140">Batch also provides job scheduling.</span></span>

![](./images/big-compute-batch.png) 

## <a name="big-compute-running-on-virtual-machines"></a><span data-ttu-id="4748d-141">虛擬機器上執行的大量計算</span><span class="sxs-lookup"><span data-stu-id="4748d-141">Big compute running on Virtual Machines</span></span>

<span data-ttu-id="4748d-142">您可以使用 [Microsoft HPC Pack][hpc-pack] 管理 VM 的叢集，排程和監控 HPC 作業。</span><span class="sxs-lookup"><span data-stu-id="4748d-142">You can use [Microsoft HPC Pack][hpc-pack] to administer a cluster of VMs, and schedule and monitor HPC jobs.</span></span> <span data-ttu-id="4748d-143">使用此方法時，您必須佈建和管理 VM 及網路基礎結構。</span><span class="sxs-lookup"><span data-stu-id="4748d-143">With this approach, you must provision and manage the VMs and network infrastructure.</span></span> <span data-ttu-id="4748d-144">如果您有現有的 HPC 工作負載，而且想要將部分或全部工作負載移到 Azure，請考慮這種方法。</span><span class="sxs-lookup"><span data-stu-id="4748d-144">Consider this approach if you have existing HPC workloads and want to move some or all it to Azure.</span></span> <span data-ttu-id="4748d-145">您可以將整個 HPC 叢集移到 Azure，或將 HPC 叢集放在內部部署，但是使用 Azure 處理暴增產能。</span><span class="sxs-lookup"><span data-stu-id="4748d-145">You can move the entire HPC cluster to Azure, or keep your HPC cluster on-premises but use Azure for burst capacity.</span></span> <span data-ttu-id="4748d-146">如需詳細資訊，請參閱[大規模運算工作負載的 Batch 和 HPC 解決方案][batch-hpc-solutions]。</span><span class="sxs-lookup"><span data-stu-id="4748d-146">For more information, see [Batch and HPC solutions for large-scale computing workloads][batch-hpc-solutions].</span></span>

### <a name="hpc-pack-deployed-to-azure"></a><span data-ttu-id="4748d-147">部署至 Azure 的 HPC Pack</span><span class="sxs-lookup"><span data-stu-id="4748d-147">HPC Pack deployed to Azure</span></span>

<span data-ttu-id="4748d-148">在此案例中，HPC 叢集整個建立在 Azure 中。</span><span class="sxs-lookup"><span data-stu-id="4748d-148">In this scenario, the HPC cluster is created entirely within Azure.</span></span>

![](./images/big-compute-iaas.png) 
 
<span data-ttu-id="4748d-149">前端節點提供叢集的管理和作業排程服務。</span><span class="sxs-lookup"><span data-stu-id="4748d-149">The head node provides management and job scheduling services to the cluster.</span></span> <span data-ttu-id="4748d-150">如為緊密結合的工作，使用 RDMA 網路在 VM 之間提供高頻寬、低延遲的通訊。</span><span class="sxs-lookup"><span data-stu-id="4748d-150">For tightly coupled tasks, use an RDMA network that provides very high bandwidth, low latency communication between VMs.</span></span> <span data-ttu-id="4748d-151">如需詳細資訊，請參閱[在 Azure 中部署 HPC Pack 2016 叢集][deploy-hpc-azure]。</span><span class="sxs-lookup"><span data-stu-id="4748d-151">For more information see [Deploy an HPC Pack 2016 cluster in Azure][deploy-hpc-azure].</span></span>

### <a name="burst-an-hpc-cluster-to-azure"></a><span data-ttu-id="4748d-152">將 HPC 叢集暴增的工作負載移至 Azure</span><span class="sxs-lookup"><span data-stu-id="4748d-152">Burst an HPC cluster to Azure</span></span>

<span data-ttu-id="4748d-153">此案例中，組織是在內部部署執行 HPC Pack，並使用 Azure VM 處理暴增產能。</span><span class="sxs-lookup"><span data-stu-id="4748d-153">In this scenario, an organization is running HPC Pack on-premises, and uses Azure VMs for burst capacity.</span></span> <span data-ttu-id="4748d-154">叢集前端節為內部部署。</span><span class="sxs-lookup"><span data-stu-id="4748d-154">The cluster head node is on-premises.</span></span> <span data-ttu-id="4748d-155">ExpressRoute 或 VPN 閘道會將您的內部部署網路連線至 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="4748d-155">ExpressRoute or VPN Gateway connects the on-premises network to the Azure VNet.</span></span>

![](./images/big-compute-hybrid.png) 


[batch]: /azure/batch/
[batch-hpc-solutions]: /azure/batch/batch-hpc-solutions
[deploy-hpc-azure]: /azure/virtual-machines/windows/hpcpack-2016-cluster
[embarrassingly-parallel]: https://en.wikipedia.org/wiki/Embarrassingly_parallel
[hpc-pack]: https://technet.microsoft.com/library/cc514029

 

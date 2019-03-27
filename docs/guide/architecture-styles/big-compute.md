---
title: 大量計算架構樣式
titleSuffix: Azure Application Architecture Guide
description: 說明 Azure 上大量計算架構的優點、挑戰和最佳做法。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19, HPC
ms.openlocfilehash: 56bd2ce010b56880e769ada4c6397391a73bbd1e
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58244619"
---
# <a name="big-compute-architecture-style"></a><span data-ttu-id="ec435-103">大量計算架構樣式</span><span class="sxs-lookup"><span data-stu-id="ec435-103">Big compute architecture style</span></span>

<span data-ttu-id="ec435-104">「大量計算」一詞描述需要大量核心 (通常數以千、百計) 的大型工作負載。</span><span class="sxs-lookup"><span data-stu-id="ec435-104">The term *big compute* describes large-scale workloads that require a large number of cores, often numbering in the hundreds or thousands.</span></span> <span data-ttu-id="ec435-105">案例包括影像轉譯、流體動態、財務風險模型、石油探勘、藥物設計、工程壓力分析，當然還有其他的。</span><span class="sxs-lookup"><span data-stu-id="ec435-105">Scenarios include image rendering, fluid dynamics, financial risk modeling, oil exploration, drug design, and engineering stress analysis, among others.</span></span>

![大量計架構樣式的邏輯圖](./images/big-compute-logical.png)

<span data-ttu-id="ec435-107">以下是大量計算應用程式的一些 典型特性：</span><span class="sxs-lookup"><span data-stu-id="ec435-107">Here are some typical characteristics of big compute applications:</span></span>

- <span data-ttu-id="ec435-108">工作可以分割成多個離散的工作，這些工作可以同時分散到許多核心上執行。</span><span class="sxs-lookup"><span data-stu-id="ec435-108">The work can be split into discrete tasks, which can be run across many cores simultaneously.</span></span>
- <span data-ttu-id="ec435-109">每個工作有限。</span><span class="sxs-lookup"><span data-stu-id="ec435-109">Each task is finite.</span></span> <span data-ttu-id="ec435-110">它會取得一些輸入、執行一些處理、並產生輸出。</span><span class="sxs-lookup"><span data-stu-id="ec435-110">It takes some input, does some processing, and produces output.</span></span> <span data-ttu-id="ec435-111">整個應用程式會執行一段有限的時間 (幾分鐘至幾天)。</span><span class="sxs-lookup"><span data-stu-id="ec435-111">The entire application runs for a finite amount of time (minutes to days).</span></span> <span data-ttu-id="ec435-112">常見的模式是佈建一批數量龐大的核心，然後在應用程式完成後停轉至零。</span><span class="sxs-lookup"><span data-stu-id="ec435-112">A common pattern is to provision a large number of cores in a burst, and then spin down to zero once the application completes.</span></span>
- <span data-ttu-id="ec435-113">應用程式不需要全年無休。</span><span class="sxs-lookup"><span data-stu-id="ec435-113">The application does not need to stay up 24/7.</span></span> <span data-ttu-id="ec435-114">不過，系統必須處理節點失敗或應用程式當機。</span><span class="sxs-lookup"><span data-stu-id="ec435-114">However, the system must handle node failures or application crashes.</span></span>
- <span data-ttu-id="ec435-115">某些應用程式的工作是獨立的，而且可平行執行。</span><span class="sxs-lookup"><span data-stu-id="ec435-115">For some applications, tasks are independent and can run in parallel.</span></span> <span data-ttu-id="ec435-116">其他應用程式的工作會緊密結合，這表示它們必須互動或交換中繼結果。</span><span class="sxs-lookup"><span data-stu-id="ec435-116">In other cases, tasks are tightly coupled, meaning they must interact or exchange intermediate results.</span></span> <span data-ttu-id="ec435-117">在此情況下，請考慮使用高速網路技術，例如 InfiniBand 和遠端直接記憶體存取 (RDMA)。</span><span class="sxs-lookup"><span data-stu-id="ec435-117">In that case, consider using high-speed networking technologies such as InfiniBand and remote direct memory access (RDMA).</span></span>
- <span data-ttu-id="ec435-118">根據您的工作負載，您應使用可應付大量計算的 VM 大小 (H16r、H16mr 和 A9)。</span><span class="sxs-lookup"><span data-stu-id="ec435-118">Depending on your workload, you might use compute-intensive VM sizes (H16r, H16mr, and A9).</span></span>

## <a name="when-to-use-this-architecture"></a><span data-ttu-id="ec435-119">使用此架構的時機</span><span class="sxs-lookup"><span data-stu-id="ec435-119">When to use this architecture</span></span>

- <span data-ttu-id="ec435-120">計算密集的作業，例如模擬、數字密集運算。</span><span class="sxs-lookup"><span data-stu-id="ec435-120">Computationally intensive operations such as simulation and number crunching.</span></span>
- <span data-ttu-id="ec435-121">會密集運算、必須分散在多部電腦 (10-1000 部) 之 CPU 的模擬。</span><span class="sxs-lookup"><span data-stu-id="ec435-121">Simulations that are computationally intensive and must be split across CPUs in multiple computers (10-1000s).</span></span>
- <span data-ttu-id="ec435-122">會耗用一部電腦太多記憶體、必須分散在多部電腦的模擬。</span><span class="sxs-lookup"><span data-stu-id="ec435-122">Simulations that require too much memory for one computer, and must be split across multiple computers.</span></span>
- <span data-ttu-id="ec435-123">若在單一電腦上完成會花太多時間的長時間執行計算。</span><span class="sxs-lookup"><span data-stu-id="ec435-123">Long-running computations that would take too long to complete on a single computer.</span></span>
- <span data-ttu-id="ec435-124">必須執行 100 或 1000 次以上的小型計算，例如 Monte Carlo 模擬。</span><span class="sxs-lookup"><span data-stu-id="ec435-124">Smaller computations that must be run 100s or 1000s of times, such as Monte Carlo simulations.</span></span>

## <a name="benefits"></a><span data-ttu-id="ec435-125">優點</span><span class="sxs-lookup"><span data-stu-id="ec435-125">Benefits</span></span>

- <span data-ttu-id="ec435-126">運用 [embarrassingly parallel][embarrassingly-parallel] 處理而有高效能。</span><span class="sxs-lookup"><span data-stu-id="ec435-126">High performance with "[embarrassingly parallel][embarrassingly-parallel]" processing.</span></span>
- <span data-ttu-id="ec435-127">可以利用數百或數千部電腦核心，更快解決大型問題。</span><span class="sxs-lookup"><span data-stu-id="ec435-127">Can harness hundreds or thousands of computer cores to solve large problems faster.</span></span>
- <span data-ttu-id="ec435-128">存取專門的高效能硬體，搭配專用的高速 InfiniBand 網路。</span><span class="sxs-lookup"><span data-stu-id="ec435-128">Access to specialized high-performance hardware, with dedicated high-speed InfiniBand networks.</span></span>
- <span data-ttu-id="ec435-129">您可以視需要佈建 VM 來執行工作，然後終止它們。</span><span class="sxs-lookup"><span data-stu-id="ec435-129">You can provision VMs as needed to do work, and then tear them down.</span></span>

## <a name="challenges"></a><span data-ttu-id="ec435-130">挑戰</span><span class="sxs-lookup"><span data-stu-id="ec435-130">Challenges</span></span>

- <span data-ttu-id="ec435-131">管理 VM 基礎結構。</span><span class="sxs-lookup"><span data-stu-id="ec435-131">Managing the VM infrastructure.</span></span>
- <span data-ttu-id="ec435-132">管理數字密集運算的磁碟區</span><span class="sxs-lookup"><span data-stu-id="ec435-132">Managing the volume of number crunching</span></span>
- <span data-ttu-id="ec435-133">適時佈建數千個核心。</span><span class="sxs-lookup"><span data-stu-id="ec435-133">Provisioning thousands of cores in a timely manner.</span></span>
- <span data-ttu-id="ec435-134">針對緊密結合的工作，加入更多核心可以有些微回報。</span><span class="sxs-lookup"><span data-stu-id="ec435-134">For tightly coupled tasks, adding more cores can have diminishing returns.</span></span> <span data-ttu-id="ec435-135">您可能需要不斷實驗找出最佳的核心數目。</span><span class="sxs-lookup"><span data-stu-id="ec435-135">You may need to experiment to find the optimum number of cores.</span></span>

## <a name="big-compute-using-azure-batch"></a><span data-ttu-id="ec435-136">使用 Azure Batch 的大量計算</span><span class="sxs-lookup"><span data-stu-id="ec435-136">Big compute using Azure Batch</span></span>

<span data-ttu-id="ec435-137">[Azure Batch][batch] 是受控服務，可用於執行大規模的高效能運算 (HPC) 應用程式。</span><span class="sxs-lookup"><span data-stu-id="ec435-137">[Azure Batch][batch] is a managed service for running large-scale high-performance computing (HPC) applications.</span></span>

<span data-ttu-id="ec435-138">使用 Azure Batch，要設定 VM 集區，並上傳應用程式和資料檔案。</span><span class="sxs-lookup"><span data-stu-id="ec435-138">Using Azure Batch, you configure a VM pool, and upload the applications and data files.</span></span> <span data-ttu-id="ec435-139">然後 Batch 服務會佈建 VM、將工作指派給 VM、執行工作，並監視進度。</span><span class="sxs-lookup"><span data-stu-id="ec435-139">Then the Batch service provisions the VMs, assign tasks to the VMs, runs the tasks, and monitors the progress.</span></span> <span data-ttu-id="ec435-140">Batch 可以自動相應放大 VM 來反應工作負載。</span><span class="sxs-lookup"><span data-stu-id="ec435-140">Batch can automatically scale out the VMs in response to the workload.</span></span> <span data-ttu-id="ec435-141">Batch 也提供作業排程。</span><span class="sxs-lookup"><span data-stu-id="ec435-141">Batch also provides job scheduling.</span></span>

![使用 Azure Batch 的大量計算圖](./images/big-compute-batch.png)

## <a name="big-compute-running-on-virtual-machines"></a><span data-ttu-id="ec435-143">虛擬機器上執行的大量計算</span><span class="sxs-lookup"><span data-stu-id="ec435-143">Big compute running on Virtual Machines</span></span>

<span data-ttu-id="ec435-144">您可以使用 [Microsoft HPC Pack][hpc-pack] 管理 VM 的叢集，排程和監控 HPC 作業。</span><span class="sxs-lookup"><span data-stu-id="ec435-144">You can use [Microsoft HPC Pack][hpc-pack] to administer a cluster of VMs, and schedule and monitor HPC jobs.</span></span> <span data-ttu-id="ec435-145">使用此方法時，您必須佈建和管理 VM 及網路基礎結構。</span><span class="sxs-lookup"><span data-stu-id="ec435-145">With this approach, you must provision and manage the VMs and network infrastructure.</span></span> <span data-ttu-id="ec435-146">如果您有現有的 HPC 工作負載，而且想要將部分或全部工作負載移到 Azure，請考慮這種方法。</span><span class="sxs-lookup"><span data-stu-id="ec435-146">Consider this approach if you have existing HPC workloads and want to move some or all it to Azure.</span></span> <span data-ttu-id="ec435-147">您可以將整個 HPC 叢集移到 Azure，或將 HPC 叢集放在內部部署，但是使用 Azure 處理暴增產能。</span><span class="sxs-lookup"><span data-stu-id="ec435-147">You can move the entire HPC cluster to Azure, or keep your HPC cluster on-premises but use Azure for burst capacity.</span></span> <span data-ttu-id="ec435-148">如需詳細資訊，請參閱[大規模運算工作負載的 Batch 和 HPC 解決方案][batch-hpc-solutions]。</span><span class="sxs-lookup"><span data-stu-id="ec435-148">For more information, see [Batch and HPC solutions for large-scale computing workloads][batch-hpc-solutions].</span></span>

### <a name="hpc-pack-deployed-to-azure"></a><span data-ttu-id="ec435-149">部署至 Azure 的 HPC Pack</span><span class="sxs-lookup"><span data-stu-id="ec435-149">HPC Pack deployed to Azure</span></span>

<span data-ttu-id="ec435-150">在此案例中，HPC 叢集整個建立在 Azure 中。</span><span class="sxs-lookup"><span data-stu-id="ec435-150">In this scenario, the HPC cluster is created entirely within Azure.</span></span>

![部署至 Azure 的 HPC Pack 圖](./images/big-compute-iaas.png)

<span data-ttu-id="ec435-152">前端節點提供叢集的管理和作業排程服務。</span><span class="sxs-lookup"><span data-stu-id="ec435-152">The head node provides management and job scheduling services to the cluster.</span></span> <span data-ttu-id="ec435-153">如為緊密結合的工作，使用 RDMA 網路在 VM 之間提供高頻寬、低延遲的通訊。</span><span class="sxs-lookup"><span data-stu-id="ec435-153">For tightly coupled tasks, use an RDMA network that provides very high bandwidth, low latency communication between VMs.</span></span> <span data-ttu-id="ec435-154">如需詳細資訊，請參閱[在 Azure 中部署 HPC Pack 2016 叢集][deploy-hpc-azure]。</span><span class="sxs-lookup"><span data-stu-id="ec435-154">For more information see [Deploy an HPC Pack 2016 cluster in Azure][deploy-hpc-azure].</span></span>

### <a name="burst-an-hpc-cluster-to-azure"></a><span data-ttu-id="ec435-155">將 HPC 叢集暴增的工作負載移至 Azure</span><span class="sxs-lookup"><span data-stu-id="ec435-155">Burst an HPC cluster to Azure</span></span>

<span data-ttu-id="ec435-156">此案例中，組織是在內部部署執行 HPC Pack，並使用 Azure VM 處理暴增產能。</span><span class="sxs-lookup"><span data-stu-id="ec435-156">In this scenario, an organization is running HPC Pack on-premises, and uses Azure VMs for burst capacity.</span></span> <span data-ttu-id="ec435-157">叢集前端節為內部部署。</span><span class="sxs-lookup"><span data-stu-id="ec435-157">The cluster head node is on-premises.</span></span> <span data-ttu-id="ec435-158">ExpressRoute 或 VPN 閘道會將您的內部部署網路連線至 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="ec435-158">ExpressRoute or VPN Gateway connects the on-premises network to the Azure VNet.</span></span>

![混合式大量計算叢集圖](./images/big-compute-hybrid.png)

<!-- links -->

[batch]: /azure/batch/
[batch-hpc-solutions]: /azure/batch/batch-hpc-solutions
[deploy-hpc-azure]: /azure/virtual-machines/windows/hpcpack-2016-cluster
[embarrassingly-parallel]: https://en.wikipedia.org/wiki/Embarrassingly_parallel
[hpc-pack]: https://technet.microsoft.com/library/cc514029

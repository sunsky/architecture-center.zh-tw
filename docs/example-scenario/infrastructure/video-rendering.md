---
title: Azure 上的 3D 影片轉譯
description: 使用 Azure Batch 服務在 Azure 中執行原生 HPC 工作負載。
author: adamboeglin
ms.date: 07/13/2018
ms.openlocfilehash: 1206fa7d931fca635118929d433abe232ec5ca9a
ms.sourcegitcommit: b2a4eb132857afa70201e28d662f18458865a48e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48818616"
---
# <a name="3d-video-rendering-on-azure"></a><span data-ttu-id="d7ee2-103">Azure 上的 3D 影片轉譯</span><span class="sxs-lookup"><span data-stu-id="d7ee2-103">3D video rendering on Azure</span></span>

<span data-ttu-id="d7ee2-104">3D 影片轉譯是耗時的程序，需要大量 CPU 時間才能完成。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-104">3D video rendering is a time consuming process that requires a significant amount of CPU time to complete.</span></span> <span data-ttu-id="d7ee2-105">在單一電腦上，從靜態資產產生影片檔案的程序可能要數小時甚至數天，取決於您要產生的影片長度和複雜度。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-105">On a single machine, the process of generating a video file from static assets can take hours or even days depending on the length and complexity of the video you are producing.</span></span> <span data-ttu-id="d7ee2-106">許多公司購買昂貴的高階桌面電腦來執行這些工作，或者投資大量的轉譯伺服器陣列以將作業投入其中。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-106">Many companies will purchase either expensive high end desktop computers to perform these tasks, or invest in large render farms that they can submit jobs to.</span></span> <span data-ttu-id="d7ee2-107">不過，利用 Azure Batch，在您需要時提供功能，不需要時則自行關閉，完全不需要資本投資。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-107">However, by taking advantage of Azure Batch, that power is available to you when you need it and shuts itself down when you don't, all without any capital investment.</span></span>

<span data-ttu-id="d7ee2-108">不論您選取 Windows Server 或 Linux 計算節點，Batch 都為您提供一致的管理體驗和作業排程。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-108">Batch gives you a consistent management experience and job scheduling, whether you select Windows Server or Linux compute nodes.</span></span> <span data-ttu-id="d7ee2-109">有了 Batch，您可以使用現有的 Windows 或 Linux 應用程式，包括 AutoDesk Maya 和 Blender，在 Azure 中執行大規模轉譯作業。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-109">With Batch, you can use your existing Windows or Linux applications, including AutoDesk Maya and Blender, to run large-scale render jobs in Azure.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="d7ee2-110">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="d7ee2-110">Relevant use cases</span></span>

<span data-ttu-id="d7ee2-111">請針對下列類似使用案例考慮此案例：</span><span class="sxs-lookup"><span data-stu-id="d7ee2-111">Consider this scenario for these similar use cases:</span></span>

* <span data-ttu-id="d7ee2-112">3D 模型化</span><span class="sxs-lookup"><span data-stu-id="d7ee2-112">3D modeling</span></span>
* <span data-ttu-id="d7ee2-113">Visual FX (VFX) 轉譯</span><span class="sxs-lookup"><span data-stu-id="d7ee2-113">Visual FX (VFX) rendering</span></span>
* <span data-ttu-id="d7ee2-114">影片轉碼</span><span class="sxs-lookup"><span data-stu-id="d7ee2-114">Video transcoding</span></span>
* <span data-ttu-id="d7ee2-115">影像處理、色彩修正及調整大小</span><span class="sxs-lookup"><span data-stu-id="d7ee2-115">Image processing, color correction, and resizing</span></span>

## <a name="architecture"></a><span data-ttu-id="d7ee2-116">架構</span><span class="sxs-lookup"><span data-stu-id="d7ee2-116">Architecture</span></span>

![使用 Azure Batch 的雲端原生 HPC 解決方案中所牽涉到的元件架構概觀][architecture]

<span data-ttu-id="d7ee2-118">此案例示範使用 Azure Batch 的工作流程。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-118">This scenario shows a workflow that uses Azure Batch.</span></span> <span data-ttu-id="d7ee2-119">資料流程如下︰</span><span class="sxs-lookup"><span data-stu-id="d7ee2-119">The data flows as follows:</span></span>

1. <span data-ttu-id="d7ee2-120">將輸入檔案和處理這些檔案的應用程式上傳到您的 Azure 儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-120">Upload input files and the applications to process those files to your Azure Storage account.</span></span>
2. <span data-ttu-id="d7ee2-121">在您的 Batch 帳戶中建立計算節點的 Batch 集區、要在集區上執行工作負載的作業，以及作業中的工作。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-121">Create a Batch pool of compute nodes in your Batch account, a job to run the workload on the pool, and tasks in the job.</span></span>
3. <span data-ttu-id="d7ee2-122">將輸入檔案和應用程式下載至 Batch。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-122">Download input files and the applications to Batch.</span></span>
4. <span data-ttu-id="d7ee2-123">監視工作執行。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-123">Monitor task execution.</span></span>
5. <span data-ttu-id="d7ee2-124">上傳工作輸出。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-124">Upload task output.</span></span>
6. <span data-ttu-id="d7ee2-125">下載輸出檔案。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-125">Download output files.</span></span>

<span data-ttu-id="d7ee2-126">若要簡化此程序，您也可以使用[適用於 Maya 和 3ds Max 的 Batch 外掛程式][batch-plugins]。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-126">To simplify this process, you could also use the [Batch Plugins for Maya and 3ds Max][batch-plugins]</span></span>

### <a name="components"></a><span data-ttu-id="d7ee2-127">元件</span><span class="sxs-lookup"><span data-stu-id="d7ee2-127">Components</span></span>

<span data-ttu-id="d7ee2-128">Azure Batch 是以下列 Azure 技術為基礎而建置的：</span><span class="sxs-lookup"><span data-stu-id="d7ee2-128">Azure Batch builds upon the following Azure technologies:</span></span>

* <span data-ttu-id="d7ee2-129">[虛擬網路](/azure/virtual-network/virtual-networks-overview)是用於前端節點和計算資源。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-129">[Virtual Networks](/azure/virtual-network/virtual-networks-overview) are used for both the head node and the compute resources.</span></span>
* <span data-ttu-id="d7ee2-130">[Azure 儲存體帳戶](/azure/storage/common/storage-introduction)是用於同步處理和資料保留。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-130">[Azure Storage accounts](/azure/storage/common/storage-introduction) are used for synchronization and data retention.</span></span>
* <span data-ttu-id="d7ee2-131">[虛擬機器擴展集][vmss]是由 CycleCloud 用於計算資源。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-131">[Virtual Machine Scale Sets][vmss] are used by CycleCloud for compute resources.</span></span>

## <a name="considerations"></a><span data-ttu-id="d7ee2-132">考量</span><span class="sxs-lookup"><span data-stu-id="d7ee2-132">Considerations</span></span>

### <a name="machine-sizes-available-for-azure-batch"></a><span data-ttu-id="d7ee2-133">Azure Batch 可用的機器大小</span><span class="sxs-lookup"><span data-stu-id="d7ee2-133">Machine Sizes available for Azure Batch</span></span>

<span data-ttu-id="d7ee2-134">大部分轉譯客戶會選擇具有高 CPU 能力的資源，而其他使用虛擬機器擴展集的工作負載可能會相異地選擇虛擬機器，並且依賴許多因素：</span><span class="sxs-lookup"><span data-stu-id="d7ee2-134">While most rendering customers will choose resources with high CPU power, other workloads using virtual machine scale sets may choose VMs differently and will depend on a number of factors:</span></span>

* <span data-ttu-id="d7ee2-135">應用程式是否執行記憶體繫結？</span><span class="sxs-lookup"><span data-stu-id="d7ee2-135">Is the application being run memory bound?</span></span>
* <span data-ttu-id="d7ee2-136">應用程式是否需要使用 GPU？</span><span class="sxs-lookup"><span data-stu-id="d7ee2-136">Does the application need to use GPUs?</span></span> 
* <span data-ttu-id="d7ee2-137">作業類型是否平行，或者針對緊密結合的工作需要 Infiniband 連線？</span><span class="sxs-lookup"><span data-stu-id="d7ee2-137">Are the job types embarrassingly parallel or require infiniband connectivity for tightly coupled jobs?</span></span>
* <span data-ttu-id="d7ee2-138">需要快速 I/O 才能存取計算節點上的儲存體。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-138">Require fast I/O to access storage on the compute Nodes.</span></span>

<span data-ttu-id="d7ee2-139">Azure 有廣範圍的虛擬機器大小，可以解決每一個上述應用程式需求，部分是專屬於 HPC，但是即使是最小的大小也可以用來提供有效方格實作：</span><span class="sxs-lookup"><span data-stu-id="d7ee2-139">Azure has a wide range of VM sizes that can address each and every one of the above application requirements, some are specific to HPC, but even the smallest sizes can be utilized to provide an effective grid implementation:</span></span>

* <span data-ttu-id="d7ee2-140">[HPC 虛擬機器大小][compute-hpc] 由於轉譯的 CPU 繫結本質，所以 Microsoft 通常會建議 Azure H 系列虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-140">[HPC VM sizes][compute-hpc] Due to the CPU bound nature of rendering, Microsoft typically suggests the Azure H-Series VMs.</span></span> <span data-ttu-id="d7ee2-141">這種類型的 VM 特別針對高階計算的需求而建置，具有 8 和 16 核心 vCPU 大小可用，並且配備 DDR4 記憶體、SSD 暫時儲存體和 Haswell E5 Intel 技術。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-141">This type of VM is built specifically for high end computational needs, they have 8 and 16 core vCPU sizes available, and features DDR4 memory, SSD temporary storage, and Haswell E5 Intel technology.</span></span>
* <span data-ttu-id="d7ee2-142">[GPU 虛擬機器大小][compute-gpu] GPU 最佳化的虛擬機器大小，為搭配單一或多個 NVIDIA GPU 提供的特製化虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-142">[GPU VM sizes][compute-gpu] GPU optimized VM sizes are specialized virtual machines available with single or multiple NVIDIA GPUs.</span></span> <span data-ttu-id="d7ee2-143">這些大小是專門針對計算密集型、圖形密集型及視覺效果的工作負載所設計。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-143">These sizes are designed for compute-intensive, graphics-intensive, and visualization workloads.</span></span>
* <span data-ttu-id="d7ee2-144">NC、NCv2、NCv3 及 ND 大小會對計算密集型和網路密集型應用程式和演算法 (包括 CUDA 和 OpenCL 型應用程式及模擬、AI 及深度學習) 進行最佳化。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-144">NC, NCv2, NCv3, and ND sizes are optimized for compute-intensive and network-intensive applications and algorithms, including CUDA and OpenCL-based applications and simulations, AI, and Deep Learning.</span></span> <span data-ttu-id="d7ee2-145">NV 大小則會針對遠端視覺效果、串流、遊戲、編碼及利用 OpenGL 和 DirectX 這類架構的 VDI 案例進行最佳化和設計。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-145">NV sizes are optimized and designed for remote visualization, streaming, gaming, encoding, and VDI scenarios utilizing frameworks such as OpenGL and DirectX.</span></span>
* <span data-ttu-id="d7ee2-146">[記憶體最佳化虛擬機器大小][compute-memory] 需要更多記憶體時，記憶體最佳化虛擬機器大小提供更高的記憶體對 CPU 比例。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-146">[Memory optimized VM sizes][compute-memory] When more memory is required, the memory optimized VM sizes offer a higher memory-to-CPU ratio.</span></span>
* <span data-ttu-id="d7ee2-147">[一般用途虛擬機器大小][compute-general] 也可使用一般用途虛擬機器大小，並提供平衡的 CPU 對記憶體比例。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-147">[General purposes VM sizes][compute-general] General-purpose VM sizes are also available and provide balanced CPU-to-memory ratio.</span></span>

### <a name="alternatives"></a><span data-ttu-id="d7ee2-148">替代項目</span><span class="sxs-lookup"><span data-stu-id="d7ee2-148">Alternatives</span></span>

<span data-ttu-id="d7ee2-149">如果您需要對於 Azure 中的轉譯環境有更多控制權，或者需要混合式實作，則 CycleCloud 計算可以協助在雲端中協調 IaaS 方格。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-149">If you require more control over your rendering environment in Azure or need a hybrid implementation, then CycleCloud computing can help orchestrate an IaaS grid in the cloud.</span></span> <span data-ttu-id="d7ee2-150">使用與 Azure Batch 相同的基礎 Azure 技術，讓建置和維護 IaaS 方格成為有效率的程序。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-150">Using the same underlying Azure technologies as Azure Batch, it makes building and maintaining an IaaS grid an efficient process.</span></span> <span data-ttu-id="d7ee2-151">若要深入了解設計原則，請使用下列連結：</span><span class="sxs-lookup"><span data-stu-id="d7ee2-151">To find out more and learn about the design principles use the following link:</span></span>

<span data-ttu-id="d7ee2-152">如需您在 Azure 中可用的所有 HPC 解決方案完整概觀，請參閱[使用 Azure 虛擬機器的 HPC、Batch 及 Big Compute 解決方案][hpc-alt-solutions]一文</span><span class="sxs-lookup"><span data-stu-id="d7ee2-152">For a complete overview of all the HPC solutions that are available to you in Azure, see the article [HPC, Batch, and Big Compute solutions using Azure VMs][hpc-alt-solutions]</span></span>

### <a name="availability"></a><span data-ttu-id="d7ee2-153">可用性</span><span class="sxs-lookup"><span data-stu-id="d7ee2-153">Availability</span></span>

<span data-ttu-id="d7ee2-154">監視 Azure Batch 元件可透過一系列服務、工具和 API 來完成。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-154">Monitoring of the Azure Batch components is available through a range of services, tools, and APIs.</span></span> <span data-ttu-id="d7ee2-155">監視會在[監視 Batch 解決方案][batch-monitor]一文中進一步討論。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-155">Monitoring is discussed further in the [Monitor Batch solutions][batch-monitor] article.</span></span>

### <a name="scalability"></a><span data-ttu-id="d7ee2-156">延展性</span><span class="sxs-lookup"><span data-stu-id="d7ee2-156">Scalability</span></span>

<span data-ttu-id="d7ee2-157">Azure Batch 帳戶內的集區可以透過手動介入來調整，或者根據 Azure Batch 計量使用公式來自動調整。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-157">Pools within an Azure Batch account can either scale through manual intervention or, by using a formula based on Azure Batch metrics, be scaled automatically.</span></span> <span data-ttu-id="d7ee2-158">如需詳細資訊，請參閱[建立自動調整公式以調整 Batch 集區中的節點][batch-scaling]一文。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-158">For more information on scalability, see the article [Create an automatic scaling formula for scaling nodes in a Batch pool][batch-scaling].</span></span>

### <a name="security"></a><span data-ttu-id="d7ee2-159">安全性</span><span class="sxs-lookup"><span data-stu-id="d7ee2-159">Security</span></span>

<span data-ttu-id="d7ee2-160">如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-160">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="d7ee2-161">災害復原</span><span class="sxs-lookup"><span data-stu-id="d7ee2-161">Resiliency</span></span>

<span data-ttu-id="d7ee2-162">雖然 Azure Batch 目前沒有任何容錯移轉功能，我們建議使用下列步驟以確保在發生非計劃性中斷時的可用性：</span><span class="sxs-lookup"><span data-stu-id="d7ee2-162">While there is currently no failover capability in Azure Batch, we recommend using the following steps to ensure availability if there is an unplanned outage:</span></span>

* <span data-ttu-id="d7ee2-163">在具有替代儲存體帳戶的替代 Azure 位置中建立 Azure Batch 帳戶</span><span class="sxs-lookup"><span data-stu-id="d7ee2-163">Create an Azure Batch account in an alternate Azure location with an alternate Storage Account</span></span>
* <span data-ttu-id="d7ee2-164">建立具有相同名稱的相同節點集區，配置 0 個節點</span><span class="sxs-lookup"><span data-stu-id="d7ee2-164">Create the same node pools with the same name, with zero nodes allocated</span></span>
* <span data-ttu-id="d7ee2-165">請確定應用程式會建立並更新到替代的儲存體帳戶</span><span class="sxs-lookup"><span data-stu-id="d7ee2-165">Ensure Applications are created and updated to the alternate Storage Account</span></span>
* <span data-ttu-id="d7ee2-166">上傳輸入檔案，並將作業提交至替代的 Azure Batch 帳戶</span><span class="sxs-lookup"><span data-stu-id="d7ee2-166">Upload input files and submit jobs to the alternate Azure Batch account</span></span>

## <a name="deploy-this-scenario"></a><span data-ttu-id="d7ee2-167">部署此案例</span><span class="sxs-lookup"><span data-stu-id="d7ee2-167">Deploy this scenario</span></span>

### <a name="creating-an-azure-batch-account-and-pools-manually"></a><span data-ttu-id="d7ee2-168">手動建立 Azure Batch 帳戶和集區</span><span class="sxs-lookup"><span data-stu-id="d7ee2-168">Creating an Azure Batch account and pools manually</span></span>

<span data-ttu-id="d7ee2-169">此案例會展示 Azure Batch Labs 作為可針對您自己的客戶開發的範例 SaaS 解決方案時，示範 Azure Batch 的運作方式：</span><span class="sxs-lookup"><span data-stu-id="d7ee2-169">This scenario demonstrates how Azure Batch works while showcasing Azure Batch Labs as an example SaaS solution that can be developed for your own customers:</span></span>

<span data-ttu-id="d7ee2-170">[Azure Batch Masterclass][batch-labs-masterclass]</span><span class="sxs-lookup"><span data-stu-id="d7ee2-170">[Azure Batch Masterclass][batch-labs-masterclass]</span></span>

### <a name="deploying-the-example-scenario-using-an-azure-resource-manager-template"></a><span data-ttu-id="d7ee2-171">使用 Azure Resource Manager 範本部署範例案例</span><span class="sxs-lookup"><span data-stu-id="d7ee2-171">Deploying the example scenario using an Azure Resource Manager template</span></span>

<span data-ttu-id="d7ee2-172">範本將會部署：</span><span class="sxs-lookup"><span data-stu-id="d7ee2-172">The template will deploy:</span></span>

* <span data-ttu-id="d7ee2-173">新的 Azure Batch 帳戶</span><span class="sxs-lookup"><span data-stu-id="d7ee2-173">A new Azure Batch account</span></span>
* <span data-ttu-id="d7ee2-174">儲存體帳戶</span><span class="sxs-lookup"><span data-stu-id="d7ee2-174">A storage account</span></span>
* <span data-ttu-id="d7ee2-175">與 Batch 帳戶相關聯的節點集區</span><span class="sxs-lookup"><span data-stu-id="d7ee2-175">A node pool associated with the batch account</span></span>
* <span data-ttu-id="d7ee2-176">節點集區會設定為使用 A2 v2 虛擬機器與 Canonical Ubuntu 映像</span><span class="sxs-lookup"><span data-stu-id="d7ee2-176">The node pool will be configured to use A2 v2 VMs with Canonical Ubuntu images</span></span>
* <span data-ttu-id="d7ee2-177">節點集區一開始包含 0 部虛擬機器，需要手動調整以新增虛擬機器</span><span class="sxs-lookup"><span data-stu-id="d7ee2-177">The node pool will contain zero VMs initially and will require you to manually scale to add VMs</span></span>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fhpc%2Fbatchcreatewithpools.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

<span data-ttu-id="d7ee2-178">[深入了解 Resource Manager 範本][azure-arm-templates]</span><span class="sxs-lookup"><span data-stu-id="d7ee2-178">[Learn more about Resource Manager templates][azure-arm-templates]</span></span>

## <a name="pricing"></a><span data-ttu-id="d7ee2-179">價格</span><span class="sxs-lookup"><span data-stu-id="d7ee2-179">Pricing</span></span>

<span data-ttu-id="d7ee2-180">使用 Azure Batch 的成本取決於用於集區的虛擬機器大小，以及這些虛擬機器配置及執行的時間長度，沒有成本與 Azure Batch 帳戶建立相關聯。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-180">The cost of using Azure Batch will depend on the VM sizes that are used for the pools and how long these VMs are allocated and running, there is no cost associated with an Azure Batch account creation.</span></span> <span data-ttu-id="d7ee2-181">儲存體和資料輸出也應該列入考量，因為這些項目會有額外成本。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-181">Storage and data egress should be taken into account as these will apply additional costs.</span></span>

<span data-ttu-id="d7ee2-182">以下是使用不同伺服器數目在 8 小時內完成作業所產生的成本範例：</span><span class="sxs-lookup"><span data-stu-id="d7ee2-182">The following are examples of costs that could be incurred for a job that completes in 8 hours using a different number of servers:</span></span>

* <span data-ttu-id="d7ee2-183">100 個高效能 CPU 虛擬機器：[成本預估][hpc-est-high]</span><span class="sxs-lookup"><span data-stu-id="d7ee2-183">100 High-Performance CPU VMs: [Cost Estimate][hpc-est-high]</span></span>

  <span data-ttu-id="d7ee2-184">100 x H16m (16 核心，225 GB RAM，進階儲存體 512 GB)，2 TB Blob 儲存體，1 TB 輸出</span><span class="sxs-lookup"><span data-stu-id="d7ee2-184">100 x H16m (16 cores, 225 GB RAM, Premium Storage 512 GB), 2 TB Blob Storage, 1-TB egress</span></span>

* <span data-ttu-id="d7ee2-185">50 個高效能 CPU 虛擬機器：[成本預估][hpc-est-med]</span><span class="sxs-lookup"><span data-stu-id="d7ee2-185">50 High-Performance CPU VMs: [Cost Estimate][hpc-est-med]</span></span>

  <span data-ttu-id="d7ee2-186">50 x H16m (16 核心，225 GB RAM，進階儲存體 512 GB)，2 TB Blob 儲存體，1 TB 輸出</span><span class="sxs-lookup"><span data-stu-id="d7ee2-186">50 x H16m (16 cores, 225 GB RAM, Premium Storage 512 GB), 2 TB Blob Storage, 1-TB egress</span></span>

* <span data-ttu-id="d7ee2-187">10 個高效能 CPU 虛擬機器：[成本預估][hpc-est-low]</span><span class="sxs-lookup"><span data-stu-id="d7ee2-187">10 High-Performance CPU VMs: [Cost Estimate][hpc-est-low]</span></span>

  <span data-ttu-id="d7ee2-188">10 x H16m (16 核心，225 GB RAM，進階儲存體 512 GB)，2 TB Blob 儲存體，1 TB 輸出</span><span class="sxs-lookup"><span data-stu-id="d7ee2-188">10 x H16m (16 cores, 225 GB RAM, Premium Storage 512 GB), 2 TB Blob Storage, 1-TB egress</span></span>

### <a name="pricing-for-low-priority-vms"></a><span data-ttu-id="d7ee2-189">低優先順序 VM 的定價</span><span class="sxs-lookup"><span data-stu-id="d7ee2-189">Pricing for low-priority VMs</span></span>

<span data-ttu-id="d7ee2-190">Azure Batch 也支援在節點集區中使用低優先順序虛擬機器，這樣也許可以提供大幅的成本節省。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-190">Azure Batch also supports the use of low-priority VMs in the node pools, which can potentially provide a substantial cost saving.</span></span> <span data-ttu-id="d7ee2-191">如需詳細資訊，包括標準虛擬機器與低優先順序虛擬機器之間的價格比較，請參閱 [Azure Batch 定價][batch-pricing]。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-191">For more information, including a price comparison between standard VMs and low-priority VMs, see [Azure Batch Pricing][batch-pricing].</span></span>

> [!NOTE] 
> <span data-ttu-id="d7ee2-192">低優先順序的 VM 只適合於某些應用程式和工作負載。</span><span class="sxs-lookup"><span data-stu-id="d7ee2-192">Low-priority VMs are only suitable for certain applications and workloads.</span></span>

## <a name="related-resources"></a><span data-ttu-id="d7ee2-193">相關資源</span><span class="sxs-lookup"><span data-stu-id="d7ee2-193">Related resources</span></span>

<span data-ttu-id="d7ee2-194">[Azure Batch 概觀][batch-overview]</span><span class="sxs-lookup"><span data-stu-id="d7ee2-194">[Azure Batch Overview][batch-overview]</span></span>

<span data-ttu-id="d7ee2-195">[Azure Batch 文件][batch-doc]</span><span class="sxs-lookup"><span data-stu-id="d7ee2-195">[Azure Batch Documentation][batch-doc]</span></span>

<span data-ttu-id="d7ee2-196">[在 Azure Batch 上使用容器][batch-containers]</span><span class="sxs-lookup"><span data-stu-id="d7ee2-196">[Using containers on Azure Batch][batch-containers]</span></span>

<!-- links -->
[architecture]: ./media/architecture-video-rendering.png
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[security]: /azure/security/
[resiliency]: /azure/architecture/resiliency/
[scalability]: /azure/architecture/checklist/scalability
[vmss]: /azure/virtual-machine-scale-sets/overview
[storage]: https://azure.microsoft.com/services/storage/
[batch]: https://azure.microsoft.com/services/batch/
[batch-arch]: https://azure.microsoft.com/solutions/architecture/big-compute-with-azure-batch/
[compute-hpc]: /azure/virtual-machines/windows/sizes-hpc
[compute-gpu]: /azure/virtual-machines/windows/sizes-gpu
[compute-compute]: /azure/virtual-machines/windows/sizes-compute
[compute-memory]: /azure/virtual-machines/windows/sizes-memory
[compute-general]: /azure/virtual-machines/windows/sizes-general
[compute-storage]: /azure/virtual-machines/windows/sizes-storage
[compute-acu]: /azure/virtual-machines/windows/acu
[compute=benchmark]: /azure/virtual-machines/windows/compute-benchmark-scores
[hpc-est-high]: https://azure.com/e/9ac25baf44ef49c3a6b156935ee9544c
[hpc-est-med]: https://azure.com/e/0286f1d6f6784310af4dcda5aec8c893
[hpc-est-low]: https://azure.com/e/e39afab4e71949f9bbabed99b428ba4a
[batch-labs-masterclass]: https://github.com/azurebigcompute/BigComputeLabs/tree/master/Azure%20Batch%20Masterclass%20Labs
[batch-scaling]: /azure/batch/batch-automatic-scaling
[hpc-alt-solutions]: /azure/virtual-machines/linux/high-performance-computing?toc=%2fazure%2fbatch%2ftoc.json
[batch-monitor]: /azure/batch/monitoring-overview
[batch-pricing]: https://azure.microsoft.com/pricing/details/batch/
[batch-doc]: /azure/batch/
[batch-overview]: https://azure.microsoft.com/services/batch/
[batch-containers]: https://github.com/Azure/batch-shipyard
[azure-arm-templates]: /azure/azure-resource-manager/resource-group-overview#template-deployment
[batch-plugins]: /azure/batch/batch-rendering-service#options-for-submitting-a-render-job
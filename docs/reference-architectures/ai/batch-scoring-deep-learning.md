---
title: 深入學習模型的 Batch 評分
titleSuffix: Azure Reference Architectures
description: 此參考架構會示範如何使用 Azure Machine Learning 將類神經樣式傳輸套用到影片。
author: jiata
ms.date: 02/06/2019
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai
ms.openlocfilehash: 85d04f179b988fd5b00b361149f2170d13608e6d
ms.sourcegitcommit: 700a4f6ce61b1ebe68e227fc57443e49282e35aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55887381"
---
# <a name="batch-scoring-on-azure-for-deep-learning-models"></a><span data-ttu-id="1d649-103">Azure 上適用於深入學習模型的批次評分</span><span class="sxs-lookup"><span data-stu-id="1d649-103">Batch scoring on Azure for deep learning models</span></span>

<span data-ttu-id="1d649-104">此參考架構會示範如何使用 Azure Machine Learning 將類神經樣式傳輸套用到影片。</span><span class="sxs-lookup"><span data-stu-id="1d649-104">This reference architecture shows how to apply neural style transfer to a video, using Azure Machine Learning.</span></span> <span data-ttu-id="1d649-105">「樣式傳輸」是一種深入學習技術，可使用另一個影像的樣式來編撰現有影像。</span><span class="sxs-lookup"><span data-stu-id="1d649-105">*Style transfer* is a deep learning technique that composes an existing image in the style of another image.</span></span> <span data-ttu-id="1d649-106">此架構可以廣泛應用到任何搭配使用深入學習的批次評分案例。</span><span class="sxs-lookup"><span data-stu-id="1d649-106">This architecture can be generalized for any scenario that uses batch scoring with deep learning.</span></span> <span data-ttu-id="1d649-107">[**部署這個解決方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="1d649-107">[**Deploy this solution**](#deploy-the-solution).</span></span>

![使用 Azure Machine Learning 的深度學習模型架構圖](./_images/aml-scoring-deep-learning.png)

<span data-ttu-id="1d649-109">**案例**：媒體組織想要讓他們的影片樣式看起來像是特定畫作。</span><span class="sxs-lookup"><span data-stu-id="1d649-109">**Scenario**: A media organization has a video whose style they want to change to look like a specific painting.</span></span> <span data-ttu-id="1d649-110">組織想要及時且自動地將此樣式套用至影片的所有畫面。</span><span class="sxs-lookup"><span data-stu-id="1d649-110">The organization wants to be able to apply this style to all frames of the video in a timely manner and in an automated fashion.</span></span> <span data-ttu-id="1d649-111">如需類神經樣式傳輸演算法的詳細背景，請參閱[使用卷積神經網路的影像樣式傳輸][image-style-transfer] (PDF)。</span><span class="sxs-lookup"><span data-stu-id="1d649-111">For more background about neural style transfer algorithms, see [Image Style Transfer Using Convolutional Neural Networks][image-style-transfer] (PDF).</span></span>

| <span data-ttu-id="1d649-112">樣式影像：</span><span class="sxs-lookup"><span data-stu-id="1d649-112">Style image:</span></span> | <span data-ttu-id="1d649-113">輸入/內容影片：</span><span class="sxs-lookup"><span data-stu-id="1d649-113">Input/content video:</span></span> | <span data-ttu-id="1d649-114">輸出影片：</span><span class="sxs-lookup"><span data-stu-id="1d649-114">Output video:</span></span> |
|--------|--------|---------|
| <img src="https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/style_image.jpg" width="300"> | <span data-ttu-id="1d649-115">[<img src="https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/input_video_image_0.jpg" width="300" height="300">](https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/input_video.mp4 "輸入影片") 按一下以檢視影片</span><span class="sxs-lookup"><span data-stu-id="1d649-115">[<img src="https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/input_video_image_0.jpg" width="300" height="300">](https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/input_video.mp4 "Input Video") *click to view video*</span></span> | <span data-ttu-id="1d649-116">[<img src="https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/output_video_image_0.jpg" width="300" height="300">](https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/output_video.mp4 "輸出影片") 按一下以檢視影片</span><span class="sxs-lookup"><span data-stu-id="1d649-116">[<img src="https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/output_video_image_0.jpg" width="300" height="300">](https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/output_video.mp4 "Output Video") *click to view video*</span></span> |

<span data-ttu-id="1d649-117">此參考架構適用於當 Azure 儲存體中有新媒體出現時就會觸發的工作負載。</span><span class="sxs-lookup"><span data-stu-id="1d649-117">This reference architecture is designed for workloads that are triggered by the presence of new media in Azure storage.</span></span>

<span data-ttu-id="1d649-118">處理程序包含下列步驟：</span><span class="sxs-lookup"><span data-stu-id="1d649-118">Processing involves the following steps:</span></span>

1. <span data-ttu-id="1d649-119">將影片檔案上傳至儲存體。</span><span class="sxs-lookup"><span data-stu-id="1d649-119">Upload a video file to storage.</span></span>
1. <span data-ttu-id="1d649-120">影片檔案會觸發邏輯應用程式，以將要求傳送至 Azure Machine Learning 管線發佈的端點。</span><span class="sxs-lookup"><span data-stu-id="1d649-120">The video file triggers a Logic App to send a request to the Azure Machine Learning pipeline published endpoint.</span></span>
1. <span data-ttu-id="1d649-121">此管線會處理影片、利用 MPI 套用樣式傳輸，以及後置處理影片。</span><span class="sxs-lookup"><span data-stu-id="1d649-121">The pipeline processes the video, applies style transfer with MPI, and postprocesses the video.</span></span>
1. <span data-ttu-id="1d649-122">一旦管線完成，輸出就會存回 blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="1d649-122">The output is saved back to blob storage once the pipeline is completed.</span></span>

## <a name="architecture"></a><span data-ttu-id="1d649-123">架構</span><span class="sxs-lookup"><span data-stu-id="1d649-123">Architecture</span></span>

<span data-ttu-id="1d649-124">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="1d649-124">This architecture consists of the following components.</span></span>

### <a name="compute"></a><span data-ttu-id="1d649-125">計算</span><span class="sxs-lookup"><span data-stu-id="1d649-125">Compute</span></span>

<span data-ttu-id="1d649-126">**[Azure Machine Learning 服務][ amls]** 會使用 Azure Machine Learning 管線，來建立可重現且易於管理的計算順序。</span><span class="sxs-lookup"><span data-stu-id="1d649-126">**[Azure Machine Learning Service][amls]** uses Azure Machine Learning Pipelines to create reproducible and easy-to-manage sequences of computation.</span></span> <span data-ttu-id="1d649-127">它也會提供名為 [Azure Machine Learning Compute][aml-compute] 的受控計算目標 (管線計算可在其上執行)，用於對機器學習模型進行訓練、部署和評分。</span><span class="sxs-lookup"><span data-stu-id="1d649-127">It also offers a managed compute target (on which a pipeline computation can run) called [Azure Machine Learning Compute][aml-compute] for training, deploying, and scoring machine learning models.</span></span> 

### <a name="storage"></a><span data-ttu-id="1d649-128">儲存體</span><span class="sxs-lookup"><span data-stu-id="1d649-128">Storage</span></span>

<span data-ttu-id="1d649-129">**[Blob 儲存體][blob-storage]** 用來儲存所有影像 (輸入影像、樣式影像與輸出影像)。</span><span class="sxs-lookup"><span data-stu-id="1d649-129">**[Blob storage][blob-storage]** is used to store all images (input images, style images, and output images).</span></span> <span data-ttu-id="1d649-130">Azure Machine Learning 服務會與 Blob 儲存體整合，以便使用者不必跨運算平台和 Blob 儲存體手動移動資料。</span><span class="sxs-lookup"><span data-stu-id="1d649-130">Azure Machine Learning Service integrates with Blob storage so that users do not have to manually move data across compute platforms and Blob storage.</span></span> <span data-ttu-id="1d649-131">對於此工作負載需要的效能，Blob 儲存體也非常符合成本效益。</span><span class="sxs-lookup"><span data-stu-id="1d649-131">Blob storage is also very cost-effective for the performance that this workload requires.</span></span>

### <a name="trigger--scheduling"></a><span data-ttu-id="1d649-132">觸發程序/排程</span><span class="sxs-lookup"><span data-stu-id="1d649-132">Trigger / scheduling</span></span>

<span data-ttu-id="1d649-133">**[Azure Logic Apps][logic-apps]** 會用來觸發工作流程。</span><span class="sxs-lookup"><span data-stu-id="1d649-133">**[Azure Logic Apps][logic-apps]** is used to trigger the workflow.</span></span> <span data-ttu-id="1d649-134">當邏輯應用程式偵測到 Blob 已新增至容器時，它會觸發 Azure Machine Learning 管線。</span><span class="sxs-lookup"><span data-stu-id="1d649-134">When the Logic App detects that a blob has been added to the container, it triggers the Azure Machine Learning Pipeline.</span></span> <span data-ttu-id="1d649-135">Logic Apps 十分適合此參考架構，因為用它來偵測 Blob 儲存體的變更相當容易，並且還提供簡單的程序來變更觸發程序。</span><span class="sxs-lookup"><span data-stu-id="1d649-135">Logic Apps is a good fit for this reference architecture because it's an easy way to detect changes to blob storage and provides an easy process for changing the trigger.</span></span>

### <a name="preprocessing-and-postprocessing-our-data"></a><span data-ttu-id="1d649-136">前置處理和後置處理我們的資料</span><span class="sxs-lookup"><span data-stu-id="1d649-136">Preprocessing and postprocessing our data</span></span>

<span data-ttu-id="1d649-137">此參考架構會使用有一隻猩猩在樹上的影片畫面。</span><span class="sxs-lookup"><span data-stu-id="1d649-137">This reference architecture uses video footage of an orangutan in a tree.</span></span> <span data-ttu-id="1d649-138">您可以從[這裡][source-video]下載畫面。</span><span class="sxs-lookup"><span data-stu-id="1d649-138">You can download the footage from [here][source-video].</span></span>

1. <span data-ttu-id="1d649-139">使用 [FFmpeg][ffmpeg] 從影片畫面擷取音訊檔案，以便稍後可以將音訊檔案拼接回輸出影片。</span><span class="sxs-lookup"><span data-stu-id="1d649-139">Use [FFmpeg][ffmpeg] to extract the audio file from the video footage, so that the audio file can be stitched back into the output video later.</span></span>
1. <span data-ttu-id="1d649-140">使用 FFmpeg 將影片分成個別的畫面。</span><span class="sxs-lookup"><span data-stu-id="1d649-140">Use FFmpeg to break the video into individual frames.</span></span> <span data-ttu-id="1d649-141">畫面會以平行方式獨立處理。</span><span class="sxs-lookup"><span data-stu-id="1d649-141">The frames will be processed independently, in parallel.</span></span>
1. <span data-ttu-id="1d649-142">此時，我們可以平行地將類神經樣式傳輸套用至每個個別畫面。</span><span class="sxs-lookup"><span data-stu-id="1d649-142">At this point, we can apply neural style transfer to each individual frame in parallel.</span></span>
1. <span data-ttu-id="1d649-143">一旦處理了每個畫面，就需要使用 FFmpeg 來重新拼接回這些畫面。</span><span class="sxs-lookup"><span data-stu-id="1d649-143">One each frame has been processed, we need to use FFmpeg to restitch the frames back together.</span></span>
1. <span data-ttu-id="1d649-144">最後我們會將音訊檔案重新附加至重新拼接的畫面。</span><span class="sxs-lookup"><span data-stu-id="1d649-144">Finally we reattach the audio file to the restitched footage.</span></span>

## <a name="performance-considerations"></a><span data-ttu-id="1d649-145">效能考量</span><span class="sxs-lookup"><span data-stu-id="1d649-145">Performance considerations</span></span>

### <a name="gpu-vs-cpu"></a><span data-ttu-id="1d649-146">GPU 與 CPU</span><span class="sxs-lookup"><span data-stu-id="1d649-146">GPU vs CPU</span></span>

<span data-ttu-id="1d649-147">針對深入學習工作負載，GPU 的效能通常遠勝於 CPU，但若只能使用 CPU，通常需要大量 CPU 才能擁有相當的效能。</span><span class="sxs-lookup"><span data-stu-id="1d649-147">For deep learning workloads, GPUs will generally out-perform CPUs by a considerable amount, to the extent that a sizeable cluster of CPUs is usually needed to get comparable performance.</span></span> <span data-ttu-id="1d649-148">雖然您在此架構中可以選擇只使用 CPU，但 GPU 將能提供成本/效能更好的設定檔。</span><span class="sxs-lookup"><span data-stu-id="1d649-148">While it's an option to use only CPUs in this architecture, GPUs will provide a much better cost/performance profile.</span></span> <span data-ttu-id="1d649-149">我們建議使用最新 [NCv3 系列]vm-sizes-gpu 的 GPU 最佳化 VM。</span><span class="sxs-lookup"><span data-stu-id="1d649-149">We recommend using the latest [NCv3 series]vm-sizes-gpu of GPU optimized VMs.</span></span>

<span data-ttu-id="1d649-150">並非所有區域都會將 GPU 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="1d649-150">GPUs are not enabled by default in all regions.</span></span> <span data-ttu-id="1d649-151">請務必選取已啟用 GPU 的區域。</span><span class="sxs-lookup"><span data-stu-id="1d649-151">Make sure to select a region with GPUs enabled.</span></span> <span data-ttu-id="1d649-152">此外，針對 GPU 最佳化的 VM，訂用帳戶的預設核心配額為零。</span><span class="sxs-lookup"><span data-stu-id="1d649-152">In addition, subscriptions have a default quota of zero cores for GPU-optimized VMs.</span></span> <span data-ttu-id="1d649-153">您可以提出支援要求來提高這個配額。</span><span class="sxs-lookup"><span data-stu-id="1d649-153">You can raise this quota by opening a support request.</span></span> <span data-ttu-id="1d649-154">請確定您訂用帳戶上的配額足以執行工作負載。</span><span class="sxs-lookup"><span data-stu-id="1d649-154">Make sure that your subscription has enough quota to run your workload.</span></span>

### <a name="parallelizing-across-vms-vs-cores"></a><span data-ttu-id="1d649-155">在 VM 與核心上平行處理</span><span class="sxs-lookup"><span data-stu-id="1d649-155">Parallelizing across VMs vs cores</span></span>

<span data-ttu-id="1d649-156">若以批次作業的形式來執行樣式傳輸程序，主要在 GPU 上執行的作業將必須在 VM 上平行進行處理。</span><span class="sxs-lookup"><span data-stu-id="1d649-156">When running a style transfer process as a batch job, the jobs that run primarily on GPUs will have to be parallelized across VMs.</span></span> <span data-ttu-id="1d649-157">這可能會有兩種方法：您可以建立較大的叢集，使用具有單一 GPU 的 VM，或建立較小的叢集，使用具有許多 GPU 的 VM。</span><span class="sxs-lookup"><span data-stu-id="1d649-157">Two approaches are possible: You can create a larger cluster using VMs that have a single GPU, or create a smaller cluster using VMs with many GPUs.</span></span>

<span data-ttu-id="1d649-158">針對此工作負載，這兩個選項會有相當的效能。</span><span class="sxs-lookup"><span data-stu-id="1d649-158">For this workload, these two options will have comparable performance.</span></span> <span data-ttu-id="1d649-159">使用較少 VM (每個 VM 有多個 GPU) 有助於減少資料移動。</span><span class="sxs-lookup"><span data-stu-id="1d649-159">Using fewer VMs with more GPUs per VM can help to reduce data movement.</span></span> <span data-ttu-id="1d649-160">不過，此功能流程上每項作業的資料量都不是非常大，因此您不會看到 Blob 儲存體有太多節流動作。</span><span class="sxs-lookup"><span data-stu-id="1d649-160">However, the data volume per job for this workload is not very big, so you won't observe much throttling by blob storage.</span></span>

### <a name="mpi-step"></a><span data-ttu-id="1d649-161">MPI 步驟</span><span class="sxs-lookup"><span data-stu-id="1d649-161">MPI step</span></span> 

<span data-ttu-id="1d649-162">在 Azure Machine Learning 中建立管線時，其中一個用來執行平行計算的步驟為 MPI 步驟。</span><span class="sxs-lookup"><span data-stu-id="1d649-162">When creating the pipeline in Azure Machine Learning, one of the steps used to perform parallel computation is the MPI step.</span></span> <span data-ttu-id="1d649-163">MPI 步驟會協助將資料平均分割在可用節點之間。</span><span class="sxs-lookup"><span data-stu-id="1d649-163">The MPI step will help split the data evenly across the available nodes.</span></span> <span data-ttu-id="1d649-164">直到準備好所要求的節點後，MPI 步驟才會執行。</span><span class="sxs-lookup"><span data-stu-id="1d649-164">The MPI step will not executed until all the requested nodes are ready.</span></span> <span data-ttu-id="1d649-165">如果某個節點失敗或失去先機 (若它是低優先順序虛擬機器)，則 MPI 步驟將必須重新執行。</span><span class="sxs-lookup"><span data-stu-id="1d649-165">Should one node fail or get pre-empted (if it is a low-priority virtual machine), the MPI step will have to be re-run.</span></span> 

## <a name="security-considerations"></a><span data-ttu-id="1d649-166">安全性考量</span><span class="sxs-lookup"><span data-stu-id="1d649-166">Security considerations</span></span>

### <a name="restricting-access-to-azure-blob-storage"></a><span data-ttu-id="1d649-167">限制 Azure Blob 儲存體的存取</span><span class="sxs-lookup"><span data-stu-id="1d649-167">Restricting access to Azure blob storage</span></span>

<span data-ttu-id="1d649-168">在此參考架構中，Azure Blob 儲存體是需要保護的主要儲存體元件。</span><span class="sxs-lookup"><span data-stu-id="1d649-168">In this reference architecture, Azure blob storage is the main storage component that needs to be protected.</span></span> <span data-ttu-id="1d649-169">GitHub 存放庫中所示的基準部署會使用儲存體帳戶金鑰來存取 Blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="1d649-169">The baseline deployment shown in the GitHub repo uses storage account keys to access the blob storage.</span></span> <span data-ttu-id="1d649-170">如需進一步的控制和保護，請考慮改為使用共用存取簽章 (SAS)。</span><span class="sxs-lookup"><span data-stu-id="1d649-170">For further control and protection, consider using a shared access signature (SAS) instead.</span></span> <span data-ttu-id="1d649-171">這會針對儲存體中的物件授與有限的存取權，而且無須對帳戶金鑰進行硬式編碼，或將它們儲存成純文字。</span><span class="sxs-lookup"><span data-stu-id="1d649-171">This grants limited access to objects in storage, without needing to hard code the account keys or save them in plaintext.</span></span> <span data-ttu-id="1d649-172">此方法特別實用，因為帳戶金鑰會以純文字形式顯示在邏輯應用程式的設計工具介面內。</span><span class="sxs-lookup"><span data-stu-id="1d649-172">This approach is especially useful because account keys are visible in plaintext inside of Logic App's designer interface.</span></span> <span data-ttu-id="1d649-173">使用 SAS 也有助於確保儲存體帳戶具有適當的控管，而且該存取權也只會授與應該擁有此權限的人員。</span><span class="sxs-lookup"><span data-stu-id="1d649-173">Using an SAS also helps to ensure that the storage account has proper governance, and that access is granted only to the people intended to have it.</span></span>

<span data-ttu-id="1d649-174">如果在具有很多敏感性資料的情況下，請確定所有儲存體金鑰都會受到保護，因為這些金鑰可授與工作負載中所有輸入和輸出資料的完整存取。</span><span class="sxs-lookup"><span data-stu-id="1d649-174">For scenarios with more sensitive data, make sure that all of your storage keys are protected, because these keys grant full access to all input and output data from the workload.</span></span>

### <a name="data-encryption-and-data-movement"></a><span data-ttu-id="1d649-175">資料加密和資料移動</span><span class="sxs-lookup"><span data-stu-id="1d649-175">Data encryption and data movement</span></span>

<span data-ttu-id="1d649-176">此參考架構會使用樣式傳輸作為批次評分程序的範例。</span><span class="sxs-lookup"><span data-stu-id="1d649-176">This reference architecture uses style transfer as an example of a batch scoring process.</span></span> <span data-ttu-id="1d649-177">針對資料敏感性更高的案例，儲存體中的資料應該使用待用加密。</span><span class="sxs-lookup"><span data-stu-id="1d649-177">For more data-sensitive scenarios, the data in storage should be encrypted at rest.</span></span> <span data-ttu-id="1d649-178">每當資料從一個位置移至下一個位置時，應使用 SSL 來保護資料轉送。</span><span class="sxs-lookup"><span data-stu-id="1d649-178">Each time data is moved from one location to the next, use SSL to secure the data transfer.</span></span> <span data-ttu-id="1d649-179">如需詳細資料，請參閱 [Azure 儲存體安全性指南][storage-security]。</span><span class="sxs-lookup"><span data-stu-id="1d649-179">For more information, see [Azure Storage security guide][storage-security].</span></span>

### <a name="securing-your-computation-in-a-virtual-network"></a><span data-ttu-id="1d649-180">保護虛擬網路中的計算</span><span class="sxs-lookup"><span data-stu-id="1d649-180">Securing your computation in a virtual network</span></span>

<span data-ttu-id="1d649-181">部署 Machine Learning 叢集時，您可以將叢集設定為在[虛擬網路][virtual-network]的子網路內佈建。</span><span class="sxs-lookup"><span data-stu-id="1d649-181">When deploying your Machine Learning compute cluster, you can configure your cluster to be provisioned inside a subnet of a [virtual network][virtual-network].</span></span> <span data-ttu-id="1d649-182">這可讓叢集中的計算節點與其他虛擬機器安全地通訊。</span><span class="sxs-lookup"><span data-stu-id="1d649-182">This allows the compute nodes in the cluster to communicate securely with other virtual machines.</span></span> 

### <a name="protecting-against-malicious-activity"></a><span data-ttu-id="1d649-183">防範惡意活動</span><span class="sxs-lookup"><span data-stu-id="1d649-183">Protecting against malicious activity</span></span>

<span data-ttu-id="1d649-184">在有多個使用者的案例中，請確定敏感性資料已受到保護，以防範惡意活動。</span><span class="sxs-lookup"><span data-stu-id="1d649-184">In scenarios where there are multiple users, make sure that sensitive data is protected against malicious activity.</span></span> <span data-ttu-id="1d649-185">如果有其他使用者取得此部署的存取權，因而能自訂輸入資料，請注意下列預防措施和考量：</span><span class="sxs-lookup"><span data-stu-id="1d649-185">If other users are given access to this deployment to customize the input data, take note of the following precautions and considerations:</span></span>

- <span data-ttu-id="1d649-186">使用 RBAC 來限制使用者只能存取所需資源。</span><span class="sxs-lookup"><span data-stu-id="1d649-186">Use RBAC to limit users' access to only the resources they need.</span></span>
- <span data-ttu-id="1d649-187">佈建兩個不同的儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="1d649-187">Provision two separate storage accounts.</span></span> <span data-ttu-id="1d649-188">將輸入和輸出資料儲存在第一個帳戶。</span><span class="sxs-lookup"><span data-stu-id="1d649-188">Store input and output data in the first account.</span></span> <span data-ttu-id="1d649-189">此帳戶可讓外部使用者存取。</span><span class="sxs-lookup"><span data-stu-id="1d649-189">External users can be given access to this account.</span></span> <span data-ttu-id="1d649-190">將可執行檔的指令碼及輸出的記錄檔放在其他帳戶。</span><span class="sxs-lookup"><span data-stu-id="1d649-190">Store executable scripts and output log files in the other account.</span></span> <span data-ttu-id="1d649-191">外部使用者不可存取此帳戶。</span><span class="sxs-lookup"><span data-stu-id="1d649-191">External users should not have access to this account.</span></span> <span data-ttu-id="1d649-192">這可確保外部使用者無法修改任何可執行檔 (插入惡意程式碼)，並且無法存取可能保存著敏感資訊的記錄檔。</span><span class="sxs-lookup"><span data-stu-id="1d649-192">This will ensure that external users cannot modify any executable files (to inject malicious code), and don't have access to logfiles, which could hold sensitive information.</span></span>
- <span data-ttu-id="1d649-193">惡意使用者可以對作業佇列發動 DDOS，或將格式有誤的有害訊息插入作業佇列中，造成系統鎖住或導致清除佇列錯誤。</span><span class="sxs-lookup"><span data-stu-id="1d649-193">Malicious users can DDOS the job queue or inject malformed poison messages in the job queue, causing the system to lock up or causing dequeuing errors.</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="1d649-194">監視和記錄</span><span class="sxs-lookup"><span data-stu-id="1d649-194">Monitoring and logging</span></span>

### <a name="monitoring-batch-jobs"></a><span data-ttu-id="1d649-195">監視 Batch 工作</span><span class="sxs-lookup"><span data-stu-id="1d649-195">Monitoring Batch jobs</span></span>

<span data-ttu-id="1d649-196">當您執行工作時，務必監視進度，並確定一切運作正常。</span><span class="sxs-lookup"><span data-stu-id="1d649-196">While running your job, it's important to monitor the progress and make sure that things are working as expected.</span></span> <span data-ttu-id="1d649-197">不過，監視整個叢集上的使用中節點可能不容易。</span><span class="sxs-lookup"><span data-stu-id="1d649-197">However, it can be a challenge to monitor across a cluster of active nodes.</span></span>

<span data-ttu-id="1d649-198">若要了解叢集的整體狀態，請移至 Azure 入口網站的 Machine Learning 刀鋒視窗，以檢查叢集中的節點狀態。</span><span class="sxs-lookup"><span data-stu-id="1d649-198">To get a sense of the overall state of the cluster, go to the Machine Learning blade of the Azure Portal to inspect the state of the nodes in the cluster.</span></span> <span data-ttu-id="1d649-199">如果節點狀態是非使用中或作業已失敗，則錯誤記錄會儲存在 Blob 儲存體中，您也可在 Azure 入口網站中存取錯誤記錄。</span><span class="sxs-lookup"><span data-stu-id="1d649-199">If a node is inactive or a job has failed, the error logs are saved to blob storage, and are also accessible in the Azure Portal.</span></span>

<span data-ttu-id="1d649-200">若要進一步擴充監視功能，您可以將記錄連線到 Application Insights，或執行不同處理程序來對叢集和其作業的狀態進行輪詢。</span><span class="sxs-lookup"><span data-stu-id="1d649-200">Monitoring can be further enriched by connecting logs to Application Insights or by running separate processes to poll for the state of the cluster and its jobs.</span></span>

### <a name="logging-with-azure-machine-learning"></a><span data-ttu-id="1d649-201">使用 Azure Machine Learning 來記錄</span><span class="sxs-lookup"><span data-stu-id="1d649-201">Logging with Azure Machine Learning</span></span>

<span data-ttu-id="1d649-202">Azure Machine Learing 會自動記錄相關 Blob 儲存體帳戶的所有 stdout/stderr。</span><span class="sxs-lookup"><span data-stu-id="1d649-202">Azure Machine Learing will automatically log all stdout/stderr to the associate blob storage account.</span></span> <span data-ttu-id="1d649-203">除非另有指定，否則您的 Azure Machine Learning 工作區會自動佈建儲存體帳戶，並將您的記錄傾印至其中。</span><span class="sxs-lookup"><span data-stu-id="1d649-203">Unless otherwise specified, your Azure Machine Learning Workspace will automatically provision a storage account and dump your logs into it.</span></span> <span data-ttu-id="1d649-204">您也可以使用儲存體總管之類的儲存體瀏覽工具，其可讓您更輕鬆地瀏覽記錄檔。</span><span class="sxs-lookup"><span data-stu-id="1d649-204">You can also use a storage navigation tool such as Storage Explorer which will provide a much easier experience for navigating log files.</span></span>

## <a name="cost-considerations"></a><span data-ttu-id="1d649-205">成本考量</span><span class="sxs-lookup"><span data-stu-id="1d649-205">Cost considerations</span></span>

<span data-ttu-id="1d649-206">相較於儲存體和排程元件，此參考架構中所使用的計算資源才是影響成本的關鍵。</span><span class="sxs-lookup"><span data-stu-id="1d649-206">Compared to the storage and scheduling components, the compute resources used in this reference architecture by far dominate in terms of costs.</span></span> <span data-ttu-id="1d649-207">其中一個最大的挑戰就是，在已啟用 GPU 的機器中，有效地平行進行叢集上的工作。</span><span class="sxs-lookup"><span data-stu-id="1d649-207">One of the main challenges is effectively parallelizing the work across a cluster of GPU-enabled machines.</span></span>

<span data-ttu-id="1d649-208">Machine Learning Compute 叢集大小可根據佇列中的作業，自動地相應增加和減少。</span><span class="sxs-lookup"><span data-stu-id="1d649-208">The Machine Learning Compute cluster size can automatically scale up and down depending on the jobs in the queue.</span></span> <span data-ttu-id="1d649-209">您可以藉由設定最小和最大節點數，以程式設計方式啟用自動調整。</span><span class="sxs-lookup"><span data-stu-id="1d649-209">You can enable auto-scale programmatically by setting the minimum and maximum nodes.</span></span>

<span data-ttu-id="1d649-210">對於不需要立即處理的工作，可設定自動調整，如此一來，預設狀態 (最小值) 就是零個節點的叢集。</span><span class="sxs-lookup"><span data-stu-id="1d649-210">For work that doesn't require immediate processing, configure auto-scale so the default state (minimum) is a cluster of zero nodes.</span></span> <span data-ttu-id="1d649-211">使用此設定時，叢集一開始會有零個節點，並只在偵測到佇列中有作業時，才會相應增加。</span><span class="sxs-lookup"><span data-stu-id="1d649-211">With this configuration, the cluster starts with zero nodes and only scales up when it detects jobs in the queue.</span></span> <span data-ttu-id="1d649-212">如果批次評分程序一天只會發生幾次 (或更少)，則此設定可省下大量成本。</span><span class="sxs-lookup"><span data-stu-id="1d649-212">If the batch scoring process only happens a few times a day or less, this setting enables significant cost savings.</span></span>

<span data-ttu-id="1d649-213">自動調整可能不適合間距太近的批次作業。</span><span class="sxs-lookup"><span data-stu-id="1d649-213">Auto-scaling may not be appropriate for batch jobs that happen too close to each other.</span></span> <span data-ttu-id="1d649-214">啟動及關閉叢集所花費的時間也會產生成本，因此如果批次工作負載會在前一個作業結束後的幾分鐘內啟動，那麼讓叢集在作業之間持續運作可能比較符合成本效益。</span><span class="sxs-lookup"><span data-stu-id="1d649-214">The time that it takes for a cluster to spin up and spin down also incur a cost, so if a batch workload begins only a few minutes after the previous job ends, it might be more cost effective to keep the cluster running between jobs.</span></span>

<span data-ttu-id="1d649-215">Machine Learning Compute 也支援低優先順序虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="1d649-215">Machine Learning Compute also supports low-priority virtual machines.</span></span> <span data-ttu-id="1d649-216">這可讓您在忽視的虛擬機器上執行計算，但注意它們可能隨時失去先機。</span><span class="sxs-lookup"><span data-stu-id="1d649-216">This allows you to run your computation on discounted virtual machines, with the caveat that they may be pre-empted at any time.</span></span> <span data-ttu-id="1d649-217">低優先順序虛擬機器非常適合於非關鍵性批次評分工作負載。</span><span class="sxs-lookup"><span data-stu-id="1d649-217">Low-priority virtual machines are ideal for non-critical batch scoring workloads.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="1d649-218">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="1d649-218">Deploy the solution</span></span>

<span data-ttu-id="1d649-219">若要部署此參考架構，請遵循 [GitHub 存放庫][deployment]中所述的步驟。</span><span class="sxs-lookup"><span data-stu-id="1d649-219">To deploy this reference architecture, follow the steps described in the [GitHub repo][deployment].</span></span>

> [!NOTE]
> <span data-ttu-id="1d649-220">您也可以使用 Azure Kubernetes Service，為深度學習模型部署批次評分架構。</span><span class="sxs-lookup"><span data-stu-id="1d649-220">You can also deploy a batch scoring architecture for deep learning models using the Azure Kubernetes Service.</span></span> <span data-ttu-id="1d649-221">請依照此 [Github][deployment2] 存放庫中所述的步驟。</span><span class="sxs-lookup"><span data-stu-id="1d649-221">Follow the steps described in this [Github repo][deployment2].</span></span>


<!-- links -->

[aml-compute]: /azure/machine-learning/service/how-to-set-up-training-targets#amlcompute
[amls]: /azure/machine-learning/service/overview-what-is-azure-ml
[azcopy]: /azure/storage/common/storage-use-azcopy-linux
[blob-storage]: /azure/storage/blobs/storage-blobs-introduction
[container-instances]: /azure/container-instances/
[container-registry]: /azure/container-registry/
[deployment]: https://github.com/Azure/Batch-Scoring-Deep-Learning-Models-With-AML
[deployment2]: https://github.com/Azure/Batch-Scoring-Deep-Learning-Models-With-AKS
[ffmpeg]: https://www.ffmpeg.org/
[image-style-transfer]: https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/Gatys_Image_Style_Transfer_CVPR_2016_paper.pdf
[logic-apps]: /azure/logic-apps/
[source-video]: https://happypathspublic.blob.core.windows.net/videos/orangutan.mp4
[storage-security]: /azure/storage/common/storage-security-guide
[vm-sizes-gpu]: /azure/virtual-machines/windows/sizes-gpu
[virtual-network]: /azure/machine-learning/service/how-to-enable-virtual-network

---
title: 在 Azure 上深度學習模型的分散式訓練
description: 此參考架構顯示如何使用 Azure Batch AI，在支援 GPU 的 VM 叢集中進行深度學習模型的分散式訓練。
author: njray
ms.date: 01/14/19
ms.custom: azcat-ai
ms.openlocfilehash: 800defeb851f5a31dc730038c3699e1a3d54b923
ms.sourcegitcommit: d5ea427c25f9f7799cc859b99f328739ca2d8c1c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2019
ms.locfileid: "54307757"
---
# <a name="distributed-training-of-deep-learning-models-on-azure"></a><span data-ttu-id="643e4-103">在 Azure 上深度學習模型的分散式訓練</span><span class="sxs-lookup"><span data-stu-id="643e4-103">Distributed training of deep learning models on Azure</span></span>

<span data-ttu-id="643e4-104">此參考架構顯示如何在支援 GPU 的 VM 叢集中進行深度學習模型的分散式訓練。</span><span class="sxs-lookup"><span data-stu-id="643e4-104">This reference architecture shows how to conduct distributed training of deep learning models across clusters of GPU-enabled VMs.</span></span> <span data-ttu-id="643e4-105">該案例是影像分類，但也可以針對其他深度學習案例產生解決方案，例如區段和物件偵測。</span><span class="sxs-lookup"><span data-stu-id="643e4-105">The scenario is image classification, but the solution can be generalized for other deep learning scenarios such as segmentation and object detection.</span></span>

<span data-ttu-id="643e4-106">此架構的參考實作可在 [GitHub][github] 上取得。</span><span class="sxs-lookup"><span data-stu-id="643e4-106">A reference implementation for this architecture is available on [GitHub][github].</span></span>

![分散式深度學習架構][0]

<span data-ttu-id="643e4-108">**案例**：影像分類是電腦視覺中廣泛應用的技術，一般處理方式是透過訓練卷積神經網路 (CNN)。</span><span class="sxs-lookup"><span data-stu-id="643e4-108">**Scenario**: Image classification is a widely applied technique in computer vision, often tackled by training a convolutional neural network (CNN).</span></span> <span data-ttu-id="643e4-109">對於具有大型資料集的特大模型，使用單一 GPU 進行訓練可能需要花費數週或數個月的時間。</span><span class="sxs-lookup"><span data-stu-id="643e4-109">For particularly large models with large datasets, the training process can take weeks or months on a single GPU.</span></span> <span data-ttu-id="643e4-110">在某些情況下，模型過大會導致 GPU 無法容納合理的批次大小。</span><span class="sxs-lookup"><span data-stu-id="643e4-110">In some situations, the models are so large that it's not possible to fit reasonable batch sizes onto the GPU.</span></span> <span data-ttu-id="643e4-111">在這些情況下，使用分散式訓練可以縮短訓練時間。</span><span class="sxs-lookup"><span data-stu-id="643e4-111">Using distributed training in these situations can shorten the training time.</span></span>

<span data-ttu-id="643e4-112">在此特定案例中，使用 [Imagenet 資料集][imagenet]和綜合資料上的 [Horovod][horovod] 來訓練 [ResNet50 CNN 模型][resnet]。</span><span class="sxs-lookup"><span data-stu-id="643e4-112">In this specific scenario, a [ResNet50 CNN model][resnet] is trained using [Horovod][horovod] on the [Imagenet dataset][imagenet] and on synthetic data.</span></span> <span data-ttu-id="643e4-113">參考實作會顯示如何使用三個最受歡迎的深度學習架構，完成這項工作：TensorFlow、Keras 和 PyTorch。</span><span class="sxs-lookup"><span data-stu-id="643e4-113">The reference implementation shows how to accomplish this task using three of the most popular deep learning frameworks: TensorFlow, Keras, and PyTorch.</span></span>

<span data-ttu-id="643e4-114">有數種方法可利用分散方式來訓練深度學習模型，包括以同步或非同步更新為基礎的資料平行和模型平行方法。</span><span class="sxs-lookup"><span data-stu-id="643e4-114">There are several ways to train a deep learning model in a distributed fashion, including data-parallel and model-parallel approaches based on synchronous or asynchronous updates.</span></span> <span data-ttu-id="643e4-115">目前最常見的案例是具有同步更新的資料平行方法。</span><span class="sxs-lookup"><span data-stu-id="643e4-115">Currently the most common scenario is data parallel with synchronous updates.</span></span> <span data-ttu-id="643e4-116">這種方法是最容易實作，並且足以滿足大多數的使用案例。</span><span class="sxs-lookup"><span data-stu-id="643e4-116">This approach is the easiest to implement and is sufficient for most use cases.</span></span>

<span data-ttu-id="643e4-117">在具有同步更新的資料平行分散式訓練中，模型會跨 *n* 個硬體裝置進行複寫。</span><span class="sxs-lookup"><span data-stu-id="643e4-117">In data-parallel distributed training with synchronous updates, the model is replicated across *n* hardware devices.</span></span> <span data-ttu-id="643e4-118">將訓練範例的迷你批次分割為 *n* 個微批次。</span><span class="sxs-lookup"><span data-stu-id="643e4-118">A mini-batch of training samples is divided into *n* micro-batches.</span></span> <span data-ttu-id="643e4-119">每個裝置會對微批次執行正向和反向推算。</span><span class="sxs-lookup"><span data-stu-id="643e4-119">Each device performs the forward and backward passes for a micro-batch.</span></span> <span data-ttu-id="643e4-120">當裝置完成流程時，會與其他裝置共用更新。</span><span class="sxs-lookup"><span data-stu-id="643e4-120">When a device finishes the process, it shares the updates with the other devices.</span></span> <span data-ttu-id="643e4-121">這些值用來計算整個迷你批次的更新權數，並且權數會在模型之間同步處理。</span><span class="sxs-lookup"><span data-stu-id="643e4-121">These values are used to calculate the updated weights of the entire mini-batch, and the weights are synchronized across the models.</span></span> <span data-ttu-id="643e4-122">在 [GitHub][github] 存放庫中將會探討此案例。</span><span class="sxs-lookup"><span data-stu-id="643e4-122">This scenario is covered in the [GitHub][github] repository.</span></span>

![資料平行分散式訓練][1]

<span data-ttu-id="643e4-124">此架構也可用於模型平行及非同步更新。</span><span class="sxs-lookup"><span data-stu-id="643e4-124">This architecture can also be used for model-parallel and asynchronous updates.</span></span> <span data-ttu-id="643e4-125">在模型平行分散式訓練中，模型會被分割到 *n* 個硬體裝置，每個裝置都包含模型的一部分。</span><span class="sxs-lookup"><span data-stu-id="643e4-125">In model-parallel distributed training, the model is divided across *n* hardware devices, with each device holding a part of the model.</span></span> <span data-ttu-id="643e4-126">在最簡單的實作中，每個裝置可能包含一個網路層，並在正向和反向推算期間在裝置之間傳遞資訊。</span><span class="sxs-lookup"><span data-stu-id="643e4-126">In the simplest implementation, each device may hold a layer of the network, and information is passed between devices during the forward and backwards pass.</span></span> <span data-ttu-id="643e4-127">較大的神經網路可以透過這種方式加以訓練，但是代價是效能低落，因為裝置會持續等待另一個裝置完成正向或反向推算。</span><span class="sxs-lookup"><span data-stu-id="643e4-127">Larger neural networks can be trained this way, but at the cost of performance, since devices are constantly waiting for each other to complete either the forward or backwards pass.</span></span> <span data-ttu-id="643e4-128">一些先進的技術嘗試使用綜合漸層技術來稍微減輕這個問題。</span><span class="sxs-lookup"><span data-stu-id="643e4-128">Some advanced techniques try to partially alleviate this issue by using synthetic gradients.</span></span>

<span data-ttu-id="643e4-129">訓練的步驟如下：</span><span class="sxs-lookup"><span data-stu-id="643e4-129">The steps for training are:</span></span>

1. <span data-ttu-id="643e4-130">建立會在叢集上執行並用於訓練模型的指令碼，然後將其傳送到檔案儲存體。</span><span class="sxs-lookup"><span data-stu-id="643e4-130">Create scripts that will run on the cluster and train your model, then transfer them to file storage.</span></span>

1. <span data-ttu-id="643e4-131">將資料寫入 Blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="643e4-131">Write the data to Blob Storage.</span></span>

1. <span data-ttu-id="643e4-132">建立 Batch AI 檔案伺服器，並將資料從 Blob 儲存體下載到伺服器上。</span><span class="sxs-lookup"><span data-stu-id="643e4-132">Create a Batch AI file server and download the data from Blob Storage onto it.</span></span>

1. <span data-ttu-id="643e4-133">為每個深度學習架構建立 Docker 容器，並將其傳輸至容器登錄 (Docker Hub)。</span><span class="sxs-lookup"><span data-stu-id="643e4-133">Create the Docker containers for each deep learning framework and transfer them to a container registry (Docker Hub).</span></span>

1. <span data-ttu-id="643e4-134">建立一個也會裝載 Batch AI 檔案伺服器的 Batch AI 集區。</span><span class="sxs-lookup"><span data-stu-id="643e4-134">Create a Batch AI pool that also mounts the Batch AI file server.</span></span>

1. <span data-ttu-id="643e4-135">提交作業。</span><span class="sxs-lookup"><span data-stu-id="643e4-135">Submit jobs.</span></span> <span data-ttu-id="643e4-136">每個作業都提取適當的 Docker 映像和指令碼。</span><span class="sxs-lookup"><span data-stu-id="643e4-136">Each pulls in the appropriate Docker image and scripts.</span></span>

1. <span data-ttu-id="643e4-137">完成作業後，請將所有結果寫入檔案儲存體。</span><span class="sxs-lookup"><span data-stu-id="643e4-137">Once the job is completed, write all the results to Files storage.</span></span>

## <a name="architecture"></a><span data-ttu-id="643e4-138">架構</span><span class="sxs-lookup"><span data-stu-id="643e4-138">Architecture</span></span>

<span data-ttu-id="643e4-139">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="643e4-139">This architecture consists of the following components.</span></span>

<span data-ttu-id="643e4-140">**[Azure Batch AI][batch-ai]** 藉由根據需求擴充和縮減資源，在此架構中扮演重要的角色。</span><span class="sxs-lookup"><span data-stu-id="643e4-140">**[Azure Batch AI][batch-ai]** plays the central role in this architecture by scaling resources up and down according to need.</span></span> <span data-ttu-id="643e4-141">Batch AI 是一項服務，可以協助佈建和管理 VM 叢集、排程作業、收集結果、調整資源、處理失敗，並建立適當的儲存體。</span><span class="sxs-lookup"><span data-stu-id="643e4-141">Batch AI is a service that helps provision and manage clusters of VMs, schedule jobs, gather results, scale resources, handle failures, and create appropriate storage.</span></span> <span data-ttu-id="643e4-142">其使用支援 GPU 的 VM 來執行深度學習工作負載。</span><span class="sxs-lookup"><span data-stu-id="643e4-142">It supports GPU-enabled VMs for deep learning workloads.</span></span> <span data-ttu-id="643e4-143">Python SDK 和命令列介面 (CLI) 可用於 Batch AI。</span><span class="sxs-lookup"><span data-stu-id="643e4-143">A Python SDK and a command-line interface (CLI) are available for Batch AI.</span></span>

> [!NOTE]
> <span data-ttu-id="643e4-144">Azure Batch AI 服務即將於 2019 年 3 月停用，目前 [Azure Machine Learning 服務][amls]中已推出其大規模訓練與評分功能。</span><span class="sxs-lookup"><span data-stu-id="643e4-144">The Azure Batch AI service is retiring March 2019, and its at-scale training and scoring capabilities are now available in [Azure Machine Learning Service][amls].</span></span> <span data-ttu-id="643e4-145">此參考架構即將更新為使用機器學習，其提供名為 [Azure Machine Learning Compute][aml-compute] 的受控計算目標，用於對機器學習模型進行訓練、部署和評分。</span><span class="sxs-lookup"><span data-stu-id="643e4-145">This reference architecture will be updated soon to use Machine Learning, which offers a managed compute target called [Azure Machine Learning Compute][aml-compute] for training, deploying, and scoring machine learning models.</span></span>

<span data-ttu-id="643e4-146">**[Blob 儲存體][ azure-blob]** 用於暫存資料。</span><span class="sxs-lookup"><span data-stu-id="643e4-146">**[Blob storage][azure-blob]** is used to stage the data.</span></span> <span data-ttu-id="643e4-147">這項資料會在訓練期間下載至 Batch AI 檔案伺服器。</span><span class="sxs-lookup"><span data-stu-id="643e4-147">This data is downloaded to a Batch AI file server during training.</span></span>

<span data-ttu-id="643e4-148">**[Azure 檔案][files]** 用於儲存指令碼、記錄和訓練的最終結果。</span><span class="sxs-lookup"><span data-stu-id="643e4-148">**[Azure Files][files]** is used to store the scripts, logs, and the final results from the training.</span></span> <span data-ttu-id="643e4-149">檔案儲存體適用於儲存記錄和指令碼，但其效能不如 Blob 儲存體，因此不應用於需要大量資料的工作。</span><span class="sxs-lookup"><span data-stu-id="643e4-149">File storage works well for storing logs and scripts, but is not as performant as Blob Storage, so it shouldn't be used for data-intensive tasks.</span></span>

<span data-ttu-id="643e4-150">**[Batch AI 檔案伺服器][batch-ai-files]** 是在此架構中用來儲存訓練資料的單一節點 NFS 共用。</span><span class="sxs-lookup"><span data-stu-id="643e4-150">**[Batch AI file server][batch-ai-files]** is a single-node NFS share used in this architecture to store the training data.</span></span> <span data-ttu-id="643e4-151">Batch AI 會建立 NFS 共用，並將其裝載在叢集上。</span><span class="sxs-lookup"><span data-stu-id="643e4-151">Batch AI creates an NFS share and mounts it on the cluster.</span></span> <span data-ttu-id="643e4-152">Batch AI 檔案伺服器可提供必要的輸送量，建議用來向叢集提供資料。</span><span class="sxs-lookup"><span data-stu-id="643e4-152">Batch AI file servers are the recommended way to serve data to the cluster with the necessary throughput.</span></span>

<span data-ttu-id="643e4-153">**[Docker 中樞][docker]** 用於儲存 Batch AI 執行訓練所需的 Docker 映像。</span><span class="sxs-lookup"><span data-stu-id="643e4-153">**[Docker Hub][docker]** is used to store the Docker image that Batch AI uses to run the training.</span></span> <span data-ttu-id="643e4-154">此架構已選擇使用 Docker 中樞，因為它容易使用，而且是 Docker 使用者的預設映像存放庫。</span><span class="sxs-lookup"><span data-stu-id="643e4-154">Docker Hub was chosen for this architecture because it's easy to use and is the default image repository for Docker users.</span></span> <span data-ttu-id="643e4-155">您也可以使用 [Azure Container Registry][acr]。</span><span class="sxs-lookup"><span data-stu-id="643e4-155">[Azure Container Registry][acr] can also be used.</span></span>

## <a name="performance-considerations"></a><span data-ttu-id="643e4-156">效能考量</span><span class="sxs-lookup"><span data-stu-id="643e4-156">Performance considerations</span></span>

<span data-ttu-id="643e4-157">Azure 提供了四種適用於訓練深度學習模型的[支援 GPU 的 VM 類型][gpu]。</span><span class="sxs-lookup"><span data-stu-id="643e4-157">Azure provides four [GPU-enabled VM types][gpu] suitable for training deep learning models.</span></span> <span data-ttu-id="643e4-158">這些 VM 類型的價格和速度，按從低至高的順序如下所示：</span><span class="sxs-lookup"><span data-stu-id="643e4-158">They range in price and speed from low to high as follows:</span></span>

| <span data-ttu-id="643e4-159">**Azure VM 系列**</span><span class="sxs-lookup"><span data-stu-id="643e4-159">**Azure VM series**</span></span> | <span data-ttu-id="643e4-160">**NVIDIA GPU**</span><span class="sxs-lookup"><span data-stu-id="643e4-160">**NVIDIA GPU**</span></span> |
|---------------------|----------------|
| <span data-ttu-id="643e4-161">NC</span><span class="sxs-lookup"><span data-stu-id="643e4-161">NC</span></span>                  | <span data-ttu-id="643e4-162">K80</span><span class="sxs-lookup"><span data-stu-id="643e4-162">K80</span></span>            |
| <span data-ttu-id="643e4-163">ND</span><span class="sxs-lookup"><span data-stu-id="643e4-163">ND</span></span>                  | <span data-ttu-id="643e4-164">P40</span><span class="sxs-lookup"><span data-stu-id="643e4-164">P40</span></span>            |
| <span data-ttu-id="643e4-165">NCv2</span><span class="sxs-lookup"><span data-stu-id="643e4-165">NCv2</span></span>                | <span data-ttu-id="643e4-166">P100</span><span class="sxs-lookup"><span data-stu-id="643e4-166">P100</span></span>           |
| <span data-ttu-id="643e4-167">NCv3</span><span class="sxs-lookup"><span data-stu-id="643e4-167">NCv3</span></span>                | <span data-ttu-id="643e4-168">V100</span><span class="sxs-lookup"><span data-stu-id="643e4-168">V100</span></span>           |

<span data-ttu-id="643e4-169">我們建議您在橫向擴展之前先縱向擴展訓練。例如，在試用 K80 叢集之前先試用單一 V100。</span><span class="sxs-lookup"><span data-stu-id="643e4-169">We recommended scaling up your training before scaling out. For example, try a single V100 before trying a cluster of K80s.</span></span>

<span data-ttu-id="643e4-170">下圖根據在 Batch AI 上使用 TensorFlow 和 Horovod 執行的[基準測試][benchmark]，顯示了不同 GPU 類型的效能差異。</span><span class="sxs-lookup"><span data-stu-id="643e4-170">The following graph shows the performance differences for different GPU types based on [benchmarking tests][benchmark] carried out using TensorFlow and Horovod on Batch AI.</span></span> <span data-ttu-id="643e4-171">該圖顯示了 32 個 GPU 叢集在使用不同 GPU 類型和 MPI 版本的各種模型的輸送量。</span><span class="sxs-lookup"><span data-stu-id="643e4-171">The graph shows throughput of 32 GPU clusters across various models, on different GPU types and MPI versions.</span></span> <span data-ttu-id="643e4-172">模型是在 TensorFlow 1.9 中實現</span><span class="sxs-lookup"><span data-stu-id="643e4-172">Models were implemented in TensorFlow 1.9</span></span>

![GPU 叢集上 TensorFlow 模型的輸送量結果][2]

<span data-ttu-id="643e4-174">上表所示的每個 VM 系列包含具有 InfiniBand 的設定。</span><span class="sxs-lookup"><span data-stu-id="643e4-174">Each VM series shown in the previous table includes a configuration with InfiniBand.</span></span> <span data-ttu-id="643e4-175">執行分散式訓練時使用 InfiniBand 設定，可以加快節點之間的通訊速度。</span><span class="sxs-lookup"><span data-stu-id="643e4-175">Use the InfiniBand configurations when you run distributed training, for faster communication between nodes.</span></span> <span data-ttu-id="643e4-176">善加利用 InfiniBand 的架構，InfiniBand 也可以提高訓練的縮放效率。</span><span class="sxs-lookup"><span data-stu-id="643e4-176">InfiniBand also increases the scaling efficiency of the training for the frameworks that can take advantage of it.</span></span> <span data-ttu-id="643e4-177">如需詳細資訊，請參閱 Infiniband 的[基準比較][benchmark]。</span><span class="sxs-lookup"><span data-stu-id="643e4-177">For details, see the Infiniband [benchmark comparison][benchmark].</span></span>

<span data-ttu-id="643e4-178">雖然 Batch AI 可以使用 [blobfuse][blobfuse] 配接器來裝載 Blob 儲存體，我們不建議以這種方式使用 Blob 儲存體進行分散式訓練，因為效能仍不足以應付所需的輸送量。</span><span class="sxs-lookup"><span data-stu-id="643e4-178">Although Batch AI can mount Blob storage using the [blobfuse][blobfuse] adapter, we don't recommend using Blob Storage this way for distributed training, because the performance isn't good enough to handle the necessary throughput.</span></span> <span data-ttu-id="643e4-179">應該如架構圖所示，將資料移動到 Batch AI 檔案伺服器。</span><span class="sxs-lookup"><span data-stu-id="643e4-179">Move the data to a Batch AI file server instead, as shown in the architecture diagram.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="643e4-180">延展性考量</span><span class="sxs-lookup"><span data-stu-id="643e4-180">Scalability considerations</span></span>

<span data-ttu-id="643e4-181">由於網路額外負荷，分散式訓練的縮放效率一律低於 100% &mdash; 在裝置之間同步處理整個模型成為一個瓶頸。</span><span class="sxs-lookup"><span data-stu-id="643e4-181">The scaling efficiency of distributed training is always less than 100 percent due to network overhead &mdash; syncing the entire model between devices becomes a bottleneck.</span></span> <span data-ttu-id="643e4-182">因此，分散式訓練最適合無法在單一 GPU 上使用合理批次大小訓練的大型模型，或者無法透過簡單、平行方法的分散式模型來解決的問題。</span><span class="sxs-lookup"><span data-stu-id="643e4-182">Therefore, distributed training is most suited for large models that cannot be trained using a reasonable batch size on a single GPU, or for problems that cannot be addressed by distributing the model in a simple, parallel way.</span></span>

<span data-ttu-id="643e4-183">不建議將分散式訓練用於執行超參數搜尋。</span><span class="sxs-lookup"><span data-stu-id="643e4-183">Distributed training is not recommended for running hyperparameter searches.</span></span> <span data-ttu-id="643e4-184">縮放效率會影響性能，並使分散式方法的效率低於單獨訓練多個模型設定的效率。</span><span class="sxs-lookup"><span data-stu-id="643e4-184">The scaling efficiency affects performance and makes a distributed approach less efficient than training multiple model configurations separately.</span></span>

<span data-ttu-id="643e4-185">增加縮放效率的方法之一是增加批次大小。</span><span class="sxs-lookup"><span data-stu-id="643e4-185">One way to increase scaling efficiency is to increase the batch size.</span></span> <span data-ttu-id="643e4-186">不過，增加批次大小必須謹慎，因為在不需要調整其他參數的情況下增加批次大小，可能會損害模型的最終效能。</span><span class="sxs-lookup"><span data-stu-id="643e4-186">That must be done carefully, however, because increasing the batch size without adjusting the other parameters can hurt the model's final performance.</span></span>

## <a name="storage-considerations"></a><span data-ttu-id="643e4-187">儲存體考量</span><span class="sxs-lookup"><span data-stu-id="643e4-187">Storage considerations</span></span>

<span data-ttu-id="643e4-188">在訓練深度學習模型時，常常被忽略的問題是資料儲存的位置。</span><span class="sxs-lookup"><span data-stu-id="643e4-188">When training deep learning models, an often-overlooked aspect is where the data is stored.</span></span> <span data-ttu-id="643e4-189">如果儲存體速度太慢，無法跟上 GPU 的需求，則訓練效能可能會降低。</span><span class="sxs-lookup"><span data-stu-id="643e4-189">If the storage is too slow to keep up with the demands of the GPUs, training performance can degrade.</span></span>

<span data-ttu-id="643e4-190">Batch AI 支援許多儲存體解決方案。</span><span class="sxs-lookup"><span data-stu-id="643e4-190">Batch AI supports many storage solutions.</span></span> <span data-ttu-id="643e4-191">此架構會使用 Batch AI 檔案伺服器，因為該伺服器會在易用性和效能之間提供最佳平衡的做法。</span><span class="sxs-lookup"><span data-stu-id="643e4-191">This architecture uses a Batch AI file server, because it provides the best tradeoff between ease of use and performance.</span></span> <span data-ttu-id="643e4-192">為了達到最佳效能，請在本機載入資料。</span><span class="sxs-lookup"><span data-stu-id="643e4-192">For best performance, load the data locally.</span></span> <span data-ttu-id="643e4-193">但是，這項作業可能會很繁瑣，因為所有節點必須從 Blob 儲存體下載資料，而下載 ImageNet 資料集時，可能需要幾個小時的時間。</span><span class="sxs-lookup"><span data-stu-id="643e4-193">However, this can be cumbersome, because all the nodes must download the data from Blob Storage, and with the ImageNet dataset, this can take hours.</span></span> <span data-ttu-id="643e4-194">[Azure 進階 Blob 儲存體][blob] (有限公開預覽) 是另一個值得考量的選項。</span><span class="sxs-lookup"><span data-stu-id="643e4-194">[Azure Premium Blob Storage][blob] (limited public preview) is another good option to consider.</span></span>

<span data-ttu-id="643e4-195">請不要將 Blob 和檔案儲存體裝載為分散式訓練的資料存放區。</span><span class="sxs-lookup"><span data-stu-id="643e4-195">Do not mount Blob and File storage as data stores for distributed training.</span></span> <span data-ttu-id="643e4-196">它們速度太慢，而且會降低訓練效能。</span><span class="sxs-lookup"><span data-stu-id="643e4-196">They are too slow and will hinder training performance.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="643e4-197">安全性考量</span><span class="sxs-lookup"><span data-stu-id="643e4-197">Security considerations</span></span>

### <a name="restrict-access-to-azure-blob-storage"></a><span data-ttu-id="643e4-198">限制對 Azure Blob 儲存體的存取</span><span class="sxs-lookup"><span data-stu-id="643e4-198">Restrict access to Azure Blob Storage</span></span>

<span data-ttu-id="643e4-199">此架構使用[儲存體帳戶金鑰][security-guide]來存取 Blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="643e4-199">This architecture uses [storage account keys][security-guide] to access the Blob storage.</span></span> <span data-ttu-id="643e4-200">如需進一步的控制和保護，請考慮改為使用共用存取簽章 (SAS)。</span><span class="sxs-lookup"><span data-stu-id="643e4-200">For further control and protection, consider using a shared access signature (SAS) instead.</span></span> <span data-ttu-id="643e4-201">這會針對儲存體中的物件授與有限的存取權，而且無須對帳戶金鑰進行硬式編碼，或將它們儲存成純文字。</span><span class="sxs-lookup"><span data-stu-id="643e4-201">This grants limited access to objects in storage, without needing to hard-code the account keys or save them in plaintext.</span></span> <span data-ttu-id="643e4-202">使用 SAS 也有助於確保儲存體帳戶具有適當的控管，而且該存取權也只會授與應該擁有此權限的人員。</span><span class="sxs-lookup"><span data-stu-id="643e4-202">Using a SAS also helps to ensure that the storage account has proper governance, and that access is granted only to the people intended to have it.</span></span>

<span data-ttu-id="643e4-203">如果在具有很多敏感性資料的情況下，請確定所有儲存體金鑰都會受到保護，因為這些金鑰可授與工作負載中所有輸入和輸出資料的完整存取。</span><span class="sxs-lookup"><span data-stu-id="643e4-203">For scenarios with more sensitive data, make sure that all of your storage keys are protected, because these keys grant full access to all input and output data from the workload.</span></span>

### <a name="encrypt-data-at-rest-and-in-motion"></a><span data-ttu-id="643e4-204">加密待用資料和運行中資料</span><span class="sxs-lookup"><span data-stu-id="643e4-204">Encrypt data at rest and in motion</span></span>

<span data-ttu-id="643e4-205">在使用機密資料的案例中，加密待用資料 &mdash; 也就是儲存體中的資料。</span><span class="sxs-lookup"><span data-stu-id="643e4-205">In scenarios that use sensitive data, encrypt the data at rest &mdash; that is, the data in storage.</span></span> <span data-ttu-id="643e4-206">每當資料從一個位置移至下一個位置時，請使用 SSL 來保護資料轉送。</span><span class="sxs-lookup"><span data-stu-id="643e4-206">Each time data moves from one location to the next, use SSL to secure the data transfer.</span></span> <span data-ttu-id="643e4-207">如需詳細資料，請參閱 [Azure 儲存體安全性指南][security-guide]。</span><span class="sxs-lookup"><span data-stu-id="643e4-207">For more information, see the [Azure Storage security guide][security-guide].</span></span>

### <a name="secure-data-in-a-virtual-network"></a><span data-ttu-id="643e4-208">保護虛擬網路中的資料</span><span class="sxs-lookup"><span data-stu-id="643e4-208">Secure data in a virtual network</span></span>

<span data-ttu-id="643e4-209">對於生產部署，請考慮將 Batch AI 叢集部署到指定的虛擬網路子網路中。</span><span class="sxs-lookup"><span data-stu-id="643e4-209">For production deployments, consider deploying the Batch AI cluster into a subnet of a virtual network that you specify.</span></span> <span data-ttu-id="643e4-210">這可讓叢集中的計算節點與其他虛擬機器 (或是內部部署網路) 安全地通訊。</span><span class="sxs-lookup"><span data-stu-id="643e4-210">This allows the compute nodes in the cluster to communicate securely with other virtual machines or with an on-premises network.</span></span> <span data-ttu-id="643e4-211">您也可以使用[服務端點][endpoints]搭配 Blob 儲存體，以允許虛擬網路的存取權限，或在虛擬網路中搭配 Batch AI 使用單一節點 NFS。</span><span class="sxs-lookup"><span data-stu-id="643e4-211">You can also use [service endpoints][endpoints] with blob storage to grant access from a virtual network or use a single-node NFS inside the virtual network with Batch AI.</span></span>

## <a name="monitoring-considerations"></a><span data-ttu-id="643e4-212">監視功能考量</span><span class="sxs-lookup"><span data-stu-id="643e4-212">Monitoring considerations</span></span>

<span data-ttu-id="643e4-213">當您執行作業時，務必監視進度，並確定一切運作正常。</span><span class="sxs-lookup"><span data-stu-id="643e4-213">While running your job, it's important to monitor the progress and make sure that things are working as expected.</span></span> <span data-ttu-id="643e4-214">不過，監視整個叢集上的使用中節點可能不容易。</span><span class="sxs-lookup"><span data-stu-id="643e4-214">However, it can be a challenge to monitor across a cluster of active nodes.</span></span>

<span data-ttu-id="643e4-215">Batch AI 檔案伺服器可以透過 Azure 入口網站或 [Azure CLI][cli] 和 Python SDK 進行管理。</span><span class="sxs-lookup"><span data-stu-id="643e4-215">The Batch AI file servers can be managed through the Azure portal or though the [Azure CLI][cli] and Python SDK.</span></span> <span data-ttu-id="643e4-216">若要大致了解叢集的整體狀態，請瀏覽至 Azure 入口網站的 **Batch AI**，以檢查叢集節點的狀態。</span><span class="sxs-lookup"><span data-stu-id="643e4-216">To get a sense of the overall state of the cluster, navigate to **Batch AI** in the Azure portal to inspect the state of the cluster nodes.</span></span> <span data-ttu-id="643e4-217">如果節點狀態是非使用中或作業失敗，則錯誤記錄會儲存在 Blob 儲存體中，您也可在 [作業] 下存取錯誤記錄。</span><span class="sxs-lookup"><span data-stu-id="643e4-217">If a node is inactive or a job fails, the error logs are saved to blob storage, and are also accessible in the Azure Portal under **Jobs**.</span></span>

<span data-ttu-id="643e4-218">若要擴充監視功能，您可以將記錄連線到 [Azure Application Insights][ai]，或執行不同處理程序來對 Batch AI 叢集和其作業的狀態進行輪詢。</span><span class="sxs-lookup"><span data-stu-id="643e4-218">Enrich monitoring by connecting logs to [Azure Application Insights][ai] or by running separate processes that poll for the state of the Batch AI cluster and its jobs.</span></span>

<span data-ttu-id="643e4-219">Batch AI 自動將所有 stdout/stderr 記錄到關聯的 Blob 儲存體帳戶中。</span><span class="sxs-lookup"><span data-stu-id="643e4-219">Batch AI automatically logs all stdout/stderr to the associate Blob storage account.</span></span> <span data-ttu-id="643e4-220">使用 [Azure 儲存體總管][storage-explorer]之類的儲存體瀏覽工具，可以更輕鬆地瀏覽記錄檔。</span><span class="sxs-lookup"><span data-stu-id="643e4-220">Use a storage navigation tool such as [Azure Storage Explorer][storage-explorer] for an easier experience when navigating log files.</span></span>

<span data-ttu-id="643e4-221">它也可將每個作業記錄進行串流處理。</span><span class="sxs-lookup"><span data-stu-id="643e4-221">It is also possible to stream the logs for each job.</span></span> <span data-ttu-id="643e4-222">如需有關這個選項的詳細資訊，請參閱 [GitHub][github] 中的開發步驟。</span><span class="sxs-lookup"><span data-stu-id="643e4-222">For details about this option, see the development steps on [GitHub][github].</span></span>

## <a name="deployment"></a><span data-ttu-id="643e4-223">部署</span><span class="sxs-lookup"><span data-stu-id="643e4-223">Deployment</span></span>

<span data-ttu-id="643e4-224">此架構的參考實作可在 [GitHub][github] 上取得。</span><span class="sxs-lookup"><span data-stu-id="643e4-224">The reference implementation of this architecture is available on [GitHub][github].</span></span> <span data-ttu-id="643e4-225">請遵循該文章所述的步驟，在支援 GPU 的 VM 叢集中進行深度學習模型的分散式訓練。</span><span class="sxs-lookup"><span data-stu-id="643e4-225">Follow the steps described there to conduct distributed training of deep learning models across clusters of GPU-enabled VMs.</span></span>

## <a name="next-steps"></a><span data-ttu-id="643e4-226">後續步驟</span><span class="sxs-lookup"><span data-stu-id="643e4-226">Next steps</span></span>

<span data-ttu-id="643e4-227">此架構的輸出是儲存至 Blob 儲存體的已訓練模型。</span><span class="sxs-lookup"><span data-stu-id="643e4-227">The output from this architecture is a trained model that is saved to blob storage.</span></span> <span data-ttu-id="643e4-228">您可以操作這個模型來進行即時評分或批次評分。</span><span class="sxs-lookup"><span data-stu-id="643e4-228">You can operationalize this model for either real-time scoring or batch scoring.</span></span> <span data-ttu-id="643e4-229">如需詳細資訊，請參閱以下參考架構：</span><span class="sxs-lookup"><span data-stu-id="643e4-229">For more information, see the following reference architectures:</span></span>

- <span data-ttu-id="643e4-230">[Azure 上的 Python Scikit-Learn 和深度學習模型的即時評分][real-time-scoring]</span><span class="sxs-lookup"><span data-stu-id="643e4-230">[Real-time scoring of Python Scikit-Learn and deep learning models on Azure][real-time-scoring]</span></span>
- <span data-ttu-id="643e4-231">[Azure 上適用於深度學習模型的批次評分][batch-scoring]</span><span class="sxs-lookup"><span data-stu-id="643e4-231">[Batch scoring on Azure for deep learning models][batch-scoring]</span></span>

[0]: ./_images/distributed_dl_architecture.png
[1]: ./_images/distributed_dl_flow.png
[2]: ./_images/distributed_dl_tests.png
[acr]: /azure/container-registry/container-registry-intro
[ai]: /azure/application-insights/app-insights-overview
[aml-compute]: /azure/machine-learning/service/how-to-set-up-training-targets#amlcompute
[amls]: /azure/machine-learning/service/overview-what-is-azure-ml
[azure-blob]: /azure/storage/blobs/storage-blobs-introduction
[batch-ai]: /azure/batch-ai/overview
[batch-ai-files]: /azure/batch-ai/resource-concepts#file-server
[batch-scoring]: /azure/architecture/reference-architectures/ai/batch-scoring-deep-learning
[benchmark]: https://github.com/msalvaris/BatchAIHorovodBenchmark
[blob]: https://azure.microsoft.com/en-gb/blog/introducing-azure-premium-blob-storage-limited-public-preview/
[blobfuse]: https://github.com/Azure/azure-storage-fuse
[cli]: https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md
[docker]: https://hub.docker.com/
[endpoints]: /azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network
[files]: /azure/storage/files/storage-files-introduction
[github]: https://github.com/Azure/DistributedDeepLearning/
[gpu]: /azure/virtual-machines/windows/sizes-gpu
[horovod]: https://github.com/uber/horovod
[imagenet]: http://www.image-net.org/
[real-time-scoring]: /azure/architecture/reference-architectures/ai/realtime-scoring-python
[resnet]: https://arxiv.org/abs/1512.03385
[security-guide]: /azure/storage/common/storage-security-guide
[storage-explorer]: /azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows
[tutorial]: https://github.com/Azure/DistributedDeepLearning
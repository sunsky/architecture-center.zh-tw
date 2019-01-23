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
# <a name="distributed-training-of-deep-learning-models-on-azure"></a>在 Azure 上深度學習模型的分散式訓練

此參考架構顯示如何在支援 GPU 的 VM 叢集中進行深度學習模型的分散式訓練。 該案例是影像分類，但也可以針對其他深度學習案例產生解決方案，例如區段和物件偵測。

此架構的參考實作可在 [GitHub][github] 上取得。

![分散式深度學習架構][0]

**案例**：影像分類是電腦視覺中廣泛應用的技術，一般處理方式是透過訓練卷積神經網路 (CNN)。 對於具有大型資料集的特大模型，使用單一 GPU 進行訓練可能需要花費數週或數個月的時間。 在某些情況下，模型過大會導致 GPU 無法容納合理的批次大小。 在這些情況下，使用分散式訓練可以縮短訓練時間。

在此特定案例中，使用 [Imagenet 資料集][imagenet]和綜合資料上的 [Horovod][horovod] 來訓練 [ResNet50 CNN 模型][resnet]。 參考實作會顯示如何使用三個最受歡迎的深度學習架構，完成這項工作：TensorFlow、Keras 和 PyTorch。

有數種方法可利用分散方式來訓練深度學習模型，包括以同步或非同步更新為基礎的資料平行和模型平行方法。 目前最常見的案例是具有同步更新的資料平行方法。 這種方法是最容易實作，並且足以滿足大多數的使用案例。

在具有同步更新的資料平行分散式訓練中，模型會跨 *n* 個硬體裝置進行複寫。 將訓練範例的迷你批次分割為 *n* 個微批次。 每個裝置會對微批次執行正向和反向推算。 當裝置完成流程時，會與其他裝置共用更新。 這些值用來計算整個迷你批次的更新權數，並且權數會在模型之間同步處理。 在 [GitHub][github] 存放庫中將會探討此案例。

![資料平行分散式訓練][1]

此架構也可用於模型平行及非同步更新。 在模型平行分散式訓練中，模型會被分割到 *n* 個硬體裝置，每個裝置都包含模型的一部分。 在最簡單的實作中，每個裝置可能包含一個網路層，並在正向和反向推算期間在裝置之間傳遞資訊。 較大的神經網路可以透過這種方式加以訓練，但是代價是效能低落，因為裝置會持續等待另一個裝置完成正向或反向推算。 一些先進的技術嘗試使用綜合漸層技術來稍微減輕這個問題。

訓練的步驟如下：

1. 建立會在叢集上執行並用於訓練模型的指令碼，然後將其傳送到檔案儲存體。

1. 將資料寫入 Blob 儲存體。

1. 建立 Batch AI 檔案伺服器，並將資料從 Blob 儲存體下載到伺服器上。

1. 為每個深度學習架構建立 Docker 容器，並將其傳輸至容器登錄 (Docker Hub)。

1. 建立一個也會裝載 Batch AI 檔案伺服器的 Batch AI 集區。

1. 提交作業。 每個作業都提取適當的 Docker 映像和指令碼。

1. 完成作業後，請將所有結果寫入檔案儲存體。

## <a name="architecture"></a>架構

此架構由下列元件組成。

**[Azure Batch AI][batch-ai]** 藉由根據需求擴充和縮減資源，在此架構中扮演重要的角色。 Batch AI 是一項服務，可以協助佈建和管理 VM 叢集、排程作業、收集結果、調整資源、處理失敗，並建立適當的儲存體。 其使用支援 GPU 的 VM 來執行深度學習工作負載。 Python SDK 和命令列介面 (CLI) 可用於 Batch AI。

> [!NOTE]
> Azure Batch AI 服務即將於 2019 年 3 月停用，目前 [Azure Machine Learning 服務][amls]中已推出其大規模訓練與評分功能。 此參考架構即將更新為使用機器學習，其提供名為 [Azure Machine Learning Compute][aml-compute] 的受控計算目標，用於對機器學習模型進行訓練、部署和評分。

**[Blob 儲存體][ azure-blob]** 用於暫存資料。 這項資料會在訓練期間下載至 Batch AI 檔案伺服器。

**[Azure 檔案][files]** 用於儲存指令碼、記錄和訓練的最終結果。 檔案儲存體適用於儲存記錄和指令碼，但其效能不如 Blob 儲存體，因此不應用於需要大量資料的工作。

**[Batch AI 檔案伺服器][batch-ai-files]** 是在此架構中用來儲存訓練資料的單一節點 NFS 共用。 Batch AI 會建立 NFS 共用，並將其裝載在叢集上。 Batch AI 檔案伺服器可提供必要的輸送量，建議用來向叢集提供資料。

**[Docker 中樞][docker]** 用於儲存 Batch AI 執行訓練所需的 Docker 映像。 此架構已選擇使用 Docker 中樞，因為它容易使用，而且是 Docker 使用者的預設映像存放庫。 您也可以使用 [Azure Container Registry][acr]。

## <a name="performance-considerations"></a>效能考量

Azure 提供了四種適用於訓練深度學習模型的[支援 GPU 的 VM 類型][gpu]。 這些 VM 類型的價格和速度，按從低至高的順序如下所示：

| **Azure VM 系列** | **NVIDIA GPU** |
|---------------------|----------------|
| NC                  | K80            |
| ND                  | P40            |
| NCv2                | P100           |
| NCv3                | V100           |

我們建議您在橫向擴展之前先縱向擴展訓練。例如，在試用 K80 叢集之前先試用單一 V100。

下圖根據在 Batch AI 上使用 TensorFlow 和 Horovod 執行的[基準測試][benchmark]，顯示了不同 GPU 類型的效能差異。 該圖顯示了 32 個 GPU 叢集在使用不同 GPU 類型和 MPI 版本的各種模型的輸送量。 模型是在 TensorFlow 1.9 中實現

![GPU 叢集上 TensorFlow 模型的輸送量結果][2]

上表所示的每個 VM 系列包含具有 InfiniBand 的設定。 執行分散式訓練時使用 InfiniBand 設定，可以加快節點之間的通訊速度。 善加利用 InfiniBand 的架構，InfiniBand 也可以提高訓練的縮放效率。 如需詳細資訊，請參閱 Infiniband 的[基準比較][benchmark]。

雖然 Batch AI 可以使用 [blobfuse][blobfuse] 配接器來裝載 Blob 儲存體，我們不建議以這種方式使用 Blob 儲存體進行分散式訓練，因為效能仍不足以應付所需的輸送量。 應該如架構圖所示，將資料移動到 Batch AI 檔案伺服器。

## <a name="scalability-considerations"></a>延展性考量

由於網路額外負荷，分散式訓練的縮放效率一律低於 100% &mdash; 在裝置之間同步處理整個模型成為一個瓶頸。 因此，分散式訓練最適合無法在單一 GPU 上使用合理批次大小訓練的大型模型，或者無法透過簡單、平行方法的分散式模型來解決的問題。

不建議將分散式訓練用於執行超參數搜尋。 縮放效率會影響性能，並使分散式方法的效率低於單獨訓練多個模型設定的效率。

增加縮放效率的方法之一是增加批次大小。 不過，增加批次大小必須謹慎，因為在不需要調整其他參數的情況下增加批次大小，可能會損害模型的最終效能。

## <a name="storage-considerations"></a>儲存體考量

在訓練深度學習模型時，常常被忽略的問題是資料儲存的位置。 如果儲存體速度太慢，無法跟上 GPU 的需求，則訓練效能可能會降低。

Batch AI 支援許多儲存體解決方案。 此架構會使用 Batch AI 檔案伺服器，因為該伺服器會在易用性和效能之間提供最佳平衡的做法。 為了達到最佳效能，請在本機載入資料。 但是，這項作業可能會很繁瑣，因為所有節點必須從 Blob 儲存體下載資料，而下載 ImageNet 資料集時，可能需要幾個小時的時間。 [Azure 進階 Blob 儲存體][blob] (有限公開預覽) 是另一個值得考量的選項。

請不要將 Blob 和檔案儲存體裝載為分散式訓練的資料存放區。 它們速度太慢，而且會降低訓練效能。

## <a name="security-considerations"></a>安全性考量

### <a name="restrict-access-to-azure-blob-storage"></a>限制對 Azure Blob 儲存體的存取

此架構使用[儲存體帳戶金鑰][security-guide]來存取 Blob 儲存體。 如需進一步的控制和保護，請考慮改為使用共用存取簽章 (SAS)。 這會針對儲存體中的物件授與有限的存取權，而且無須對帳戶金鑰進行硬式編碼，或將它們儲存成純文字。 使用 SAS 也有助於確保儲存體帳戶具有適當的控管，而且該存取權也只會授與應該擁有此權限的人員。

如果在具有很多敏感性資料的情況下，請確定所有儲存體金鑰都會受到保護，因為這些金鑰可授與工作負載中所有輸入和輸出資料的完整存取。

### <a name="encrypt-data-at-rest-and-in-motion"></a>加密待用資料和運行中資料

在使用機密資料的案例中，加密待用資料 &mdash; 也就是儲存體中的資料。 每當資料從一個位置移至下一個位置時，請使用 SSL 來保護資料轉送。 如需詳細資料，請參閱 [Azure 儲存體安全性指南][security-guide]。

### <a name="secure-data-in-a-virtual-network"></a>保護虛擬網路中的資料

對於生產部署，請考慮將 Batch AI 叢集部署到指定的虛擬網路子網路中。 這可讓叢集中的計算節點與其他虛擬機器 (或是內部部署網路) 安全地通訊。 您也可以使用[服務端點][endpoints]搭配 Blob 儲存體，以允許虛擬網路的存取權限，或在虛擬網路中搭配 Batch AI 使用單一節點 NFS。

## <a name="monitoring-considerations"></a>監視功能考量

當您執行作業時，務必監視進度，並確定一切運作正常。 不過，監視整個叢集上的使用中節點可能不容易。

Batch AI 檔案伺服器可以透過 Azure 入口網站或 [Azure CLI][cli] 和 Python SDK 進行管理。 若要大致了解叢集的整體狀態，請瀏覽至 Azure 入口網站的 **Batch AI**，以檢查叢集節點的狀態。 如果節點狀態是非使用中或作業失敗，則錯誤記錄會儲存在 Blob 儲存體中，您也可在 [作業] 下存取錯誤記錄。

若要擴充監視功能，您可以將記錄連線到 [Azure Application Insights][ai]，或執行不同處理程序來對 Batch AI 叢集和其作業的狀態進行輪詢。

Batch AI 自動將所有 stdout/stderr 記錄到關聯的 Blob 儲存體帳戶中。 使用 [Azure 儲存體總管][storage-explorer]之類的儲存體瀏覽工具，可以更輕鬆地瀏覽記錄檔。

它也可將每個作業記錄進行串流處理。 如需有關這個選項的詳細資訊，請參閱 [GitHub][github] 中的開發步驟。

## <a name="deployment"></a>部署

此架構的參考實作可在 [GitHub][github] 上取得。 請遵循該文章所述的步驟，在支援 GPU 的 VM 叢集中進行深度學習模型的分散式訓練。

## <a name="next-steps"></a>後續步驟

此架構的輸出是儲存至 Blob 儲存體的已訓練模型。 您可以操作這個模型來進行即時評分或批次評分。 如需詳細資訊，請參閱以下參考架構：

- [Azure 上的 Python Scikit-Learn 和深度學習模型的即時評分][real-time-scoring]
- [Azure 上適用於深度學習模型的批次評分][batch-scoring]

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
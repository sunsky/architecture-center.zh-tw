---
title: 深入學習模型的 Batch 評分
titleSuffix: Azure Reference Architectures
description: 此參考架構會示範如何使用 Azure Batch AI 將類神經樣式傳輸套用到影片。
author: jiata
ms.date: 10/02/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai
ms.openlocfilehash: 27975b42179e87f4520186778610159943a93090
ms.sourcegitcommit: 40f3561cc94f721eca50d33f2d75dc974cb6f92b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55147241"
---
# <a name="batch-scoring-on-azure-for-deep-learning-models"></a>Azure 上適用於深入學習模型的批次評分

此參考架構會示範如何使用 Azure Batch AI 將類神經樣式傳輸套用到影片。 「樣式傳輸」是一種深入學習技術，可使用另一個影像的樣式來編撰現有影像。 此架構可以廣泛應用到任何搭配使用深入學習的批次評分案例。 [**部署這個解決方案**](#deploy-the-solution)。

![使用 Azure Batch AI 的深度學習模型架構圖](./_images/batch-ai-deep-learning.png)

**案例**：媒體組織想要讓他們的影片樣式看起來像是特定畫作。 組織想要及時且自動地將此樣式套用至影片的所有畫面。 如需類神經樣式傳輸演算法的詳細背景，請參閱[使用卷積神經網路的影像樣式傳輸][image-style-transfer] (PDF)。

| 樣式影像： | 輸入/內容影片： | 輸出影片： |
|--------|--------|---------|
| <img src="https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/style_image.jpg" width="300"> | [<img src="https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/input_video_image_0.jpg" width="300" height="300">](https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/input_video.mp4 "輸入影片") 按一下以檢視影片 | [<img src="https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/output_video_image_0.jpg" width="300" height="300">](https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/output_video.mp4 "輸出影片") 按一下以檢視影片 |

此參考架構適用於當 Azure 儲存體中有新媒體出現時就會觸發的工作負載。 處理程序包含下列步驟：

1. 上傳選取的樣式影像 (例如梵谷的畫作) 和樣式傳輸指令碼到 Blob 儲存體。
1. 建立已準備好開始進行工作的自動調整 Batch AI 叢集。
1. 將影片檔案分割成個別畫面，並將這些畫面上傳到 Blob 儲存體。
1. 上傳所有畫面之後，將觸發程序檔案上傳至 Blob 儲存體。
1. 這個檔案會觸發邏輯應用程式，此應用程式會建立一個在 Azure 容器執行個體中執行的容器。
1. 該容器會執行指令碼來建立 Batch AI 作業。 每項作業都會將類神經樣式傳輸平行套用到 Batch AI 叢集的節點上。
1. 一旦產生影像時，系統就會將這些影像存回 Blob 儲存體。
1. 下載產生的畫面，然後將影像拼接到影片中。

## <a name="architecture"></a>架構

此架構由下列元件組成。

### <a name="compute"></a>計算

**[Azure Batch AI][batch-ai]** 會用來執行類神經樣式傳輸演算法。 Batch AI 藉由提供容器化的環境來支援深入學習工作負載，此環境位在已啟用 GPU 的 VM 上，並已針對深入學習架構進行預先設定。 Batch AI 也可以將計算叢集連線到 Blob 儲存體。

> [!NOTE]
> Azure Batch AI 服務即將於 2019 年 3 月停用，目前 [Azure Machine Learning 服務][amls]中已推出其大規模訓練與評分功能。 此參考架構即將更新為使用機器學習，其提供名為 [Azure Machine Learning Compute][aml-compute] 的受控計算目標，用於對機器學習模型進行訓練、部署和評分。

### <a name="storage"></a>儲存體

**[Blob 儲存體][blob-storage]** 會用來儲存所有影像 (輸入影像、樣式影像與輸出影像)，以及所有 Batch AI 所產生的記錄。 Blob 儲存體會透過 [blobfuse][blobfuse] 與 Batch AI 整合，blobfuse 是由 Blob 儲存體支援的開放原始碼虛擬檔案系統。 對於此工作負載需要的效能，Blob 儲存體也非常符合成本效益。

### <a name="trigger--scheduling"></a>觸發程序/排程

**[Azure Logic Apps][logic-apps]** 會用來觸發工作流程。 當邏輯應用程式偵測到 Blob 已新增至容器時，就會觸發 Batch AI 程序。 Logic Apps 十分適合此參考架構，因為用它來偵測 Blob 儲存體的變更相當容易，並且還提供簡單的程序來變更觸發程序。

**[Azure 容器執行個體][container-instances]** 會用來執行建立 Batch AI 作業的 Python 指令碼。 在 Docker 容器內執行這些指令碼相當方便，您可以視需求來執行指令碼。 我們會針對此架構使用容器執行個體，因為這可使用預先建置的邏輯應用程式連接器，讓邏輯應用程式觸發 Batch AI 作業。 容器執行個體可以快速地開始運作無狀態程序。

**[DockerHub][dockerhub]** 會用來儲存 Docker 映像，容器執行個體會使用此映像來執行作業建立程序。 此架構已選擇使用 DockerHub，因為它容易使用，而且是 Docker 使用者的預設映像存放庫。 您也可以使用 [Azure Container Registry][container-registry]。

### <a name="data-preparation"></a>資料準備

此參考架構會使用有一隻猩猩在樹上的影片畫面。 您可以從[此處][source-video]下載該畫面，然後遵循下列步驟來處理此畫面，以便用於工作流程：

1. 使用 [AzCopy][azcopy] 從公用 Blob 下載影片。
2. 使用 [FFmpeg][ffmpeg] 擷取音訊檔案，以便稍後可以將音訊檔案拼接回輸出影片。
3. 使用 FFmpeg 將影片分成個別的畫面。 畫面會以平行方式獨立處理。
4. 使用 AzCopy 將個別畫面複製到您的 Blob 容器。

在此階段，影片畫面的形式已可以用於類神經樣式傳輸。

## <a name="performance-considerations"></a>效能考量

### <a name="gpu-vs-cpu"></a>GPU 與 CPU

針對深入學習工作負載，GPU 的效能通常遠勝於 CPU，但若只能使用 CPU，通常需要大量 CPU 才能擁有相當的效能。 雖然您在此架構中可以選擇只使用 CPU，但 GPU 將能提供成本/效能更好的設定檔。 我們建議使用最新 [NCv3 系列]vm-sizes-gpu 的 GPU 最佳化 VM。

並非所有區域都會將 GPU 預設為啟用。 請務必選取已啟用 GPU 的區域。 此外，針對 GPU 最佳化的 VM，訂用帳戶的預設核心配額為零。 您可以提出支援要求來提高這個配額。 請確定您訂用帳戶上的配額足以執行工作負載。

### <a name="parallelizing-across-vms-vs-cores"></a>在 VM 與核心上平行處理

若以批次作業的形式來執行樣式傳輸程序，主要在 GPU 上執行的作業將必須在 VM 上平行進行處理。 這可能會有兩種方法：您可以建立較大的叢集，使用具有單一 GPU 的 VM，或建立較小的叢集，使用具有許多 GPU 的 VM。

針對此工作負載，這兩個選項會有相當的效能。 使用較少 VM (每個 VM 有多個 GPU) 有助於減少資料移動。 不過，此功能流程上每項作業的資料量都不是非常大，因此您不會看到 Blob 儲存體有太多節流動作。

### <a name="images-batch-size-per-batch-ai-job"></a>每個 Batch AI 作業的影像批次大小

另一個必須設定的參數是每個 Batch AI 作業要處理的影像數目。 一方面，您想要確保工作會廣泛地分配到各節點，這樣一來，如果工作失敗，您就不需要重試太多影像。 這表示有許多 Batch AI 作業，因此每個作業要處理的影像數目並不多。 但是另一方面，如果每個作業處理的影像太少，則設定/啟動時間會變得過長。 您可以將作業數目設成與叢集中的節點數目上限一樣。 如果沒有任何工作失敗，那麼這麼做的效能會最好，因為可將設定/啟動成本降到最低。 不過，如果作業失敗，則可能需要重新處理大量影像。

### <a name="file-servers"></a>檔案伺服器

使用 Batch AI 時，您可以根據您案例所需輸送量選擇多個儲存體選項。 針對輸送量需求低的工作負載，使用 Blob 儲存體 (透過 blobfuse) 應該就已足夠。 或者，Batch AI 也支援 Batch AI 檔案伺服器，此受控的單一節點 NFS 可自動裝載到叢集節點上，為作業提供集中存取的儲存體位置。 在大部分情況下，工作區中只需要一個檔案伺服器，而且您可以將訓練作業的資料分到不同目錄中。 如果單一節點 NFS 不適用於您的工作負載，Batch AI 還可支援其他儲存體選項，包括 Azure 檔案服務或自訂解決方案，例如 Gluster 或 Lustre 檔案系統。

## <a name="security-considerations"></a>安全性考量

### <a name="restricting-access-to-azure-blob-storage"></a>限制 Azure Blob 儲存體的存取

在此參考架構中，Azure Blob 儲存體是需要保護的主要儲存體元件。 GitHub 存放庫中所示的基準部署會使用儲存體帳戶金鑰來存取 Blob 儲存體。 如需進一步的控制和保護，請考慮改為使用共用存取簽章 (SAS)。 這會針對儲存體中的物件授與有限的存取權，而且無須對帳戶金鑰進行硬式編碼，或將它們儲存成純文字。 此方法特別實用，因為帳戶金鑰會以純文字形式顯示在邏輯應用程式的設計工具介面內。 使用 SAS 也有助於確保儲存體帳戶具有適當的控管，而且該存取權也只會授與應該擁有此權限的人員。

如果在具有很多敏感性資料的情況下，請確定所有儲存體金鑰都會受到保護，因為這些金鑰可授與工作負載中所有輸入和輸出資料的完整存取。

### <a name="data-encryption-and-data-movement"></a>資料加密和資料移動

此參考架構會使用樣式傳輸作為批次評分程序的範例。 針對資料敏感性更高的案例，儲存體中的資料應該使用待用加密。 每當資料從一個位置移至下一個位置時，應使用 SSL 來保護資料轉送。 如需詳細資料，請參閱 [Azure 儲存體安全性指南][storage-security]。

### <a name="securing-data-in-a-virtual-network"></a>保護虛擬網路中的資料

部署 Batch AI 叢集時，您可以將叢集設定為在虛擬網路的子網路內佈建。 這可讓叢集中的計算節點與其他虛擬機器 (或甚至是內部部署網路) 安全地通訊。 您也可以使用[服務端點][service-endpoints]搭配 Blob 儲存體以允許來自虛擬網路的存取，或使用 VNET 內的單一節點 NFS 搭配 Batch AI，以確保資料一律會受到保護。

### <a name="protecting-against-malicious-activity"></a>防範惡意活動

在有多個使用者的案例中，請確定敏感性資料已受到保護，以防範惡意活動。 如果有其他使用者取得此部署的存取權，因而能自訂輸入資料，請注意下列預防措施和考量：

- 使用 RBAC 來限制使用者只能存取所需資源。
- 佈建兩個不同的儲存體帳戶。 將輸入和輸出資料儲存在第一個帳戶。 此帳戶可讓外部使用者存取。 將可執行檔的指令碼及輸出的記錄檔放在其他帳戶。 外部使用者不可存取此帳戶。 這可確保外部使用者無法修改任何可執行檔 (插入惡意程式碼)，並且無法存取可能保存著敏感資訊的記錄檔。
- 惡意使用者可以對作業佇列發動 DDOS，或將格式有誤的有害訊息插入作業佇列中，造成系統鎖住或導致清除佇列錯誤。

## <a name="monitoring-and-logging"></a>監視和記錄

### <a name="monitoring-batch-ai-jobs"></a>監視 Batch AI 作業

當您執行工作時，務必監視進度，並確定一切運作正常。 不過，監視整個叢集上的使用中節點可能不容易。

若要了解叢集的整體狀態，請移至 Azure 入口網站的 Batch AI 刀鋒視窗，以檢查叢集中的節點狀態。 如果節點狀態是非使用中或作業已失敗，則錯誤記錄會儲存在 Blob 儲存體中，您也可在 Azure 入口網站的 [作業] 刀鋒視窗中存取錯誤記錄。

若要進一步擴充監視功能，您可以將記錄連線到 Application Insights，或執行不同處理程序來對 Batch AI 叢集和其作業的狀態進行輪詢。

### <a name="logging-in-batch-ai"></a>Batch AI 中的記錄功能

Batch AI 會自動記錄相關 Blob 儲存體帳戶的所有 stdout/stderr。 使用儲存體總管之類的儲存體瀏覽工具，可讓您更輕鬆地瀏覽記錄檔。

此參考架構的部署步驟也會示範如何設定更簡單的記錄系統，像是讓不同作業的所有記錄都儲存到您 Blob 容器中的相同目錄，如下所示。 使用這些記錄可監視每個作業和每個影像的處理時間。 如此可讓您更了解如何進一步最佳化程序。

![Azure Batch AI 記錄的螢幕擷取畫面](./_images/batch-ai-logging.png)

## <a name="cost-considerations"></a>成本考量

相較於儲存體和排程元件，此參考架構中所使用的計算資源才是影響成本的關鍵。 其中一個最大的挑戰就是，在已啟用 GPU 的機器中，有效地平行進行叢集上的工作。

Batch AI 叢集大小可根據佇列中的作業，自動地相應增加和減少。 您可以使用兩種方式中的其中一種來啟用 Batch AI 的自動調整功能。 您可以透過程式設計的方式來完成，也就是在 `.env` 檔案中進行設定 (這是[部署步驟][deployment]的一部份)，或是在建立叢集之後，直接在入口網站中變更調整公式。

對於不需要立即處理的工作，可設定自動調整公式，如此一來，預設狀態 (最小值) 就是零個節點的叢集。 使用此設定時，叢集一開始會有零個節點，並只在偵測到佇列中有作業時，才會相應增加。 如果批次評分程序一天只會發生幾次 (或更少)，則此設定可省下大量成本。

自動調整可能不適合間距太近的批次作業。 啟動及關閉叢集所花費的時間也會產生成本，因此如果批次工作負載會在前一個作業結束後的幾分鐘內啟動，那麼讓叢集在作業之間持續運作可能比較符合成本效益。

## <a name="deploy-the-solution"></a>部署解決方案

若要部署此參考架構，請遵循 [GitHub 存放庫][deployment]中所述的步驟。

<!-- links -->

[aml-compute]: /azure/machine-learning/service/how-to-set-up-training-targets#amlcompute
[amls]: /azure/machine-learning/service/overview-what-is-azure-ml
[azcopy]: /azure/storage/common/storage-use-azcopy-linux
[batch-ai]: /azure/batch-ai/
[blobfuse]: https://github.com/Azure/azure-storage-fuse
[blob-storage]: /azure/storage/blobs/storage-blobs-introduction
[container-instances]: /azure/container-instances/
[container-registry]: /azure/container-registry/
[deployment]: https://github.com/Azure/batch-scoring-for-dl-models
[dockerhub]: https://hub.docker.com/
[ffmpeg]: https://www.ffmpeg.org/
[image-style-transfer]: https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/Gatys_Image_Style_Transfer_CVPR_2016_paper.pdf
[logic-apps]: /azure/logic-apps/
[service-endpoints]: /azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network
[source-video]: https://happypathspublic.blob.core.windows.net/videos/orangutan.mp4
[storage-security]: /azure/storage/common/storage-security-guide
[vm-sizes-gpu]: /azure/virtual-machines/windows/sizes-gpu

---
title: Azure 上的 3D 影片轉譯
description: 使用 Azure Batch 服務在 Azure 中執行原生 HPC 工作負載
author: adamboeglin
ms.date: 07/13/2018
ms.openlocfilehash: 67dc8496656a75eab8f5d0ce45d00f8b1f4ea03f
ms.sourcegitcommit: 94d50043db63416c4d00cebe927a0c88f78c3219
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2018
ms.locfileid: "47428052"
---
# <a name="3d-video-rendering-on-azure"></a>Azure 上的 3D 影片轉譯

3D 轉譯是耗時的程序，需要大量 CPU 時間才能完成。  在單一電腦上，從靜態資產產生影片檔案的程序可能要數小時甚至數天，取決於您要產生的影片長度和複雜度。  許多公司購買昂貴的高階桌面電腦來執行這些工作，或者投資大量的轉譯伺服器陣列以將作業投入其中。  不過，利用 Azure Batch，在您需要時提供功能，不需要時則自行關閉，完全不需要資本投資。

不論您選取 Windows Server 或 Linux 計算節點，Batch 都為您提供一致的管理體驗和作業排程。 有了 Batch，您可以使用現有的 Windows 或 Linux 應用程式，包括 AutoDesk Maya 和 Blender，在 Azure 中執行大規模轉譯作業。

## <a name="related-use-cases"></a>相關使用案例

請針對下列類似使用案例考慮此案例：

* 3D 模型化
* Visual FX (VFX) 轉譯
* 影片轉碼
* 映像處理、色彩修正與調整大小

## <a name="architecture"></a>架構

![使用 Azure Batch 的雲端原生 HPC 解決方案中所牽涉到的元件架構概觀][architecture]

此範例案例涵蓋使用 Azure Batch 時的工作流程，資料流程如下所示：

1. 將輸入檔案和處理這些檔案的應用程式上傳到您的 Azure 儲存體帳戶
2. 在您的 Batch 帳戶中建立計算節點的 Batch 集區、要在集區上執行工作負載的作業，以及作業中的工作。
3. 將輸入檔案和應用程式下載至 Batch
4. 監視工作執行
5. 上傳工作輸出
6. 下載輸出檔案

若要簡化此程序，您也可以使用[適用於 Maya 和 3ds Max 的 Batch 外掛程式][batch-plugins]

### <a name="components"></a>元件

Azure Batch 是以下列 Azure 技術為基礎而建置的：

* [資源群組][resource-groups]是 Azure 資源的邏輯容器。
* [虛擬網路][vnet]是用於前端節點和計算資源
* [儲存體][storage]帳戶是用於同步處理和資料保留
* [虛擬機器擴展集][vmss]是由 CycleCloud 用於計算資源

## <a name="considerations"></a>考量

### <a name="machine-sizes-available-for-azure-batch"></a>Azure Batch 可用的機器大小

大部分轉譯客戶會選擇具有高 CPU 能力的資源，而其他使用虛擬機器擴展集的工作負載可能會相異地選擇虛擬機器，並且依賴許多因素：

* 應用程式是否執行記憶體繫結？
* 應用程式是否需要使用 GPU？ 
* 作業類型是否平行，或者針對緊密結合的工作需要 Infiniband 連線？
* 需要計算節點上儲存體的快速 I/O

Azure 有廣範圍的虛擬機器大小，可以解決每一個上述應用程式需求，部分是專屬於 HPC，但是即使是最小的大小也可以用來提供有效方格實作：

* [HPC 虛擬機器大小][compute-hpc] 由於轉譯的 CPU 繫結本質，所以 Microsoft 通常會建議 Azure H 系列虛擬機器。  這種類型的 VM 特別針對高階計算的需求而建置，具有 8 和 16 核心 vCPU 大小可用，並且配備 DDR4 記憶體、SSD 暫時儲存體和 Haswell E5 Intel 技術。
* [GPU 虛擬機器大小][compute-gpu] GPU 最佳化的虛擬機器大小，為搭配單一或多個 NVIDIA GPU 提供的特製化虛擬機器。 這些大小是專門針對計算密集型、圖形密集型及視覺效果的工作負載所設計。
* NC、NCv2、NCv3 及 ND 大小會對計算密集型和網路密集型應用程式和演算法 (包括 CUDA 和 OpenCL 型應用程式及模擬、AI 及深度學習) 進行最佳化。 NV 大小則會針對遠端視覺效果、串流、遊戲、編碼及利用 OpenGL 和 DirectX 這類架構的 VDI 案例進行最佳化和設計。
* [記憶體最佳化虛擬機器大小][compute-memory] 需要更多記憶體時，記憶體最佳化虛擬機器大小提供更高的記憶體對 CPU 比例。
* [一般用途虛擬機器大小][compute-general] 也可使用一般用途虛擬機器大小，並提供平衡的 CPU 對記憶體比例。

### <a name="alternatives"></a>替代項目

如果您需要對於 Azure 中的轉譯環境有更多控制權，或者需要混合式實作，則 CycleCloud 計算可以協助在雲端中協調 IaaS 方格。 使用與 Azure Batch 相同的基礎 Azure 技術，讓建置和維護 IaaS 方格成為有效率的程序。 若要深入了解設計原則，請使用下列連結：

如需您在 Azure 中可用的所有 HPC 解決方案完整概觀，請參閱[使用 Azure 虛擬機器的 HPC、Batch 及 Big Compute 解決方案][hpc-alt-solutions]一文

### <a name="availability"></a>可用性

監視 Azure Batch 元件可透過一系列服務、工具和 API 來完成。 監視會在[監視 Batch 解決方案][batch-monitor]一文中進一步討論。

### <a name="scalability"></a>延展性

Azure Batch 帳戶內的集區可以透過手動介入來調整，或者根據 Azure Batch 計量使用公式來自動調整。 如需詳細資訊，請參閱[建立自動調整公式以調整 Batch 集區中的節點][batch-scaling]一文。

### <a name="security"></a>安全性

如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。

### <a name="resiliency"></a>災害復原

雖然 Azure Batch 目前沒有任何容錯移轉功能，我們建議使用下列步驟以確保在發生非計劃性中斷時的可用性：

* 在具有替代儲存體帳戶的替代 Azure 位置中建立 Azure Batch 帳戶
* 建立具有相同名稱的相同節點集區，配置 0 個節點
* 請確定應用程式會建立並更新到替代的儲存體帳戶
* 上傳輸入檔案，並將作業提交至替代的 Azure Batch 帳戶

## <a name="deploy-this-scenario"></a>部署此案例

### <a name="creating-an-azure-batch-account-and-pools-manually"></a>手動建立 Azure Batch 帳戶和集區

此範例案例會在展示 Azure Batch Labs 作為可針對您自己的客戶開發的範例 SaaS 解決方案時，協助深入了解 Azure Batch 運作方式：

[Azure Batch Masterclass][batch-labs-masterclass]

### <a name="deploying-the-sample-scenario-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本部署範例案例

範本將會部署：

* 新的 Azure Batch 帳戶
* 儲存體帳戶
* 與 Batch 帳戶相關聯的節點集區
* 節點集區會設定為使用 A2 v2 虛擬機器與 Canonical Ubuntu 映像
* 節點集區一開始包含 0 部虛擬機器，需要手動調整以新增虛擬機器

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fhpc%2Fbatchcreatewithpools.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

[深入了解 Resource Manager 範本][azure-arm-templates]

## <a name="pricing"></a>價格

使用 Azure Batch 的成本取決於用於集區的虛擬機器大小，以及這些虛擬機器配置及執行的時間長度，沒有成本與 Azure Batch 帳戶建立相關聯。 儲存體和資料輸出也應該列入考量，因為這些項目會有額外成本。

以下是使用不同伺服器數目在 8 小時內完成作業所產生的成本範例：

* 100 個高效能 CPU 虛擬機器：[成本預估][hpc-est-high]

  100 x H16m (16 核心，225 GB RAM，進階儲存體 512 GB)，2 TB Blob 儲存體，1 TB 輸出

* 50 個高效能 CPU 虛擬機器：[成本預估][hpc-est-med]

  50 x H16m (16 核心，225 GB RAM，進階儲存體 512 GB)，2 TB Blob 儲存體，1 TB 輸出

* 10 個高效能 CPU 虛擬機器：[成本預估][hpc-est-low]
  
  10 x H16m (16 核心，225 GB RAM，進階儲存體 512 GB)，2 TB Blob 儲存體，1 TB 輸出

### <a name="low-priority-vm-pricing"></a>低優先順序虛擬機器價格

Azure Batch 也支援在節點集區中使用低優先順序虛擬機器*，這樣也許可以提供大幅的成本節省。 如需標準虛擬機器與低優先順序虛擬機器之間的價格比較，以及深入了解低優先順序虛擬機器，請參閱 [Batch 價格][batch-pricing]。

\* 請注意，只有特定應用程式和工作負載適合在低優先順序虛擬機器上執行。

## <a name="related-resources"></a>相關資源

[Azure Batch 概觀][batch-overview]

[Azure Batch 文件][batch-doc]

[在 Azure Batch 上使用容器][batch-containers]

<!-- links -->
[architecture]: ./media/native-hpc-ref-arch.png
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[security]: /azure/security/
[resiliency]: /azure/architecture/resiliency/
[scalability]: /azure/architecture/checklist/scalability
[vmss]: /azure/virtual-machine-scale-sets/overview
[vnet]: /azure/virtual-network/virtual-networks-overview
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
[batch-pricing]: https://azure.microsoft.com/en-gb/pricing/details/batch/
[batch-doc]: /azure/batch/
[batch-overview]: https://azure.microsoft.com/services/batch/
[batch-containers]: https://github.com/Azure/batch-shipyard
[azure-arm-templates]: /azure/azure-resource-manager/resource-group-overview#template-deployment
[batch-plugins]: /azure/batch/batch-rendering-service#options-for-submitting-a-render-job
---
title: 在 Azure 上執行計算流體力學 (CFD) 模擬
description: 在 Azure 上執行計算流體力學 (CFD) 模擬。
author: mikewarr
ms.date: 09/20/2018
ms.custom: fasttrack
ms.openlocfilehash: 42921122d74d07bf890f55be61b04c7e9a4f4e87
ms.sourcegitcommit: a0e8d11543751d681953717f6e78173e597ae207
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2018
ms.locfileid: "53004664"
---
# <a name="running-computational-fluid-dynamics-cfd-simulations-on-azure"></a>在 Azure 上執行計算流體力學 (CFD) 模擬

計算流體力學 (CFD) 模擬需要大量計算時間以及特製化硬體。 當叢集使用量增加時，模擬時間和整體方格使用會隨之成長，進而導致備用容量和佇列時間很長的問題。 新增實體硬體的成本很高，而且可能與企業所經歷的使用量尖峰和低谷不相符。 利用 Azure，可以克服其中許多挑戰，而沒有任何資本支出。

Azure 提供您在 GPU 和 CPU 虛擬機器上執行 CFD 作業所需的硬體。 支援 RDMA (遠端直接記憶體存取) 的 VM 大小具有 FDR InfiniBand 型網路，能夠進行低延遲 MPI (訊息傳遞介面) 通訊。 結合可提供企業級叢集檔案系統的 Avere vFXT，客戶即可確保 Azure 中讀取作業的最大輸送量。

若要簡化 HPC 叢集的建立、管理和最佳化，Azure CycleCloud 可用來佈建叢集，以及協調混合式和雲端案例中的資料。 CycleCloud 可藉由監視擱置工作，自動啟動隨選計算功能，讓您只需支付您所使用的項目，同時連線到您所選的工作負載排程器。

## <a name="relevant-use-cases"></a>相關使用案例

其他 CFD 應用程式相關產業包括：

* 航空
* 汽車
* 建築物 HVAV
* 石油與天然氣
* 生命科學

## <a name="architecture"></a>架構

![架構圖表][architecture]

下圖顯示典型混合式設計的高階概觀，以供監視 Azure 中隨選節點的作業：

1. 連線到 Azure CycleCloud 伺服器以設定叢集。
2. 將支援 RDMA 的機器使用於 MPI，以設定和建立叢集前端節點。
3. 新增並設定內部部署前端節點。
4. 如果資源不足，Azure CycleCloud 會相應增加 (或減少) Azure 中的計算資源。 您可定義預先決定的限制，以防止過度配置。
5. 配置給執行節點的工作。
6. Azure 中從內部部署 NFS 伺服器快取的資料。
7. 從 Avere vFXT for Azure 快取讀入的資料。
8. 轉送至 Azure CycleCloud 伺服器的作業和工作資訊。

### <a name="components"></a>元件

* [Azure CycleCloud][cyclecloud] 是一項工具，用於建立、管理、操作及最佳化 Azure 中的 HPC 和 Big Compute 叢集。
* [Azure 上的 Avere vFXT][avere] 用來提供專為雲端建置的企業級叢集檔案系統。
* [Azure 虛擬機器 (Vm)][vms] 用來建立一組靜態計算執行個體。
* [虛擬機器擴展集][vmss] 提供一組能夠由 Azure CycleCloud 相應增加或減少的相同 VM。
* [Azure 儲存體帳戶](/azure/storage/common/storage-introduction)是用於同步處理和資料保留。
* [虛擬網路](/azure/virtual-network/virtual-networks-overview)可讓多種類型的 Azure 資源 (例如 Azure 虛擬機器 (VM)) 安全地彼此通訊，以及與網際網路和內部部署網路通訊。

### <a name="alternatives"></a>替代項目

客戶也可以使用 Azure CycleCloud 完全在 Azure 中建立方格。 在此設定中，Azure CycleCloud 伺服器是在您的 Azure 訂用帳戶中執行。

如需不需要管理工作負載排程器的新式應用程式方法，[Azure Batch][batch] 有所幫助。 Azure Batch 可以在雲端有效率地執行大規模的平行和高效能運算 (HPC) 應用程式。 Azure Batch 可讓您定義 Azure 計算資源，進而以平行方式或大規模執行應用程式，而不需要手動設定或管理基礎結構。 Azure Batch 會排程需要大量計算的工作，並根據您的需求動態新增和移除計算資源。

### <a name="scalability-and-security"></a>延展性與安全性

您可以手動或使用自動調整功能來調整 Azure CycleCloud 上的執行節點。 如需詳細資訊，請參閱 [CycleCloud 自動調整][cycle-scale]。

如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。

## <a name="deploy-this-scenario"></a>部署此案例

在 Azure 中部署之前，需具備一些必要條件。 在部署 Resource Manager 範本之前，請遵循下列步驟：
1. 建立[服務主體][cycle-svcprin]，以便擷取 appId、displayName、名稱、密碼及租用戶。
2. 產生 [SSH 金鑰組][ cycle-ssh]，以便安全地登入 CycleCloud 伺服器。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FCycleCloudCommunity%2Fcyclecloud_arm%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

3. [登入 Azure CycleCloud 伺服器][cycle-login]以設定和建立新叢集。
4. [建立叢集][cycle-create]。

Avere 快取是一個選擇性解決方案，可以大幅提高應用程式作業資料的讀取輸送量。 Avere vFXT for Azure 可解決在雲端中執行這些企業 HPC 應用程式的問題，同時還能利用內部部署環境或 Azure Blob 儲存體中儲存的資料。

如果組織正在規劃兼具內部部署儲存體和雲端運算的混合式基礎結構，HPC 應用程式可以使用 NAS 裝置中儲存的資料「高載」至 Azure 中，並且視需要加快虛擬 CPU。 資料集永遠不會完全移至雲端。 在處理期間會使用 Avere 叢集來暫時快取要求的位元組。

若要安裝及設定 Avere vFXT 安裝，請遵循 [Avere 安裝和設定指南][avere]。

## <a name="pricing"></a>價格

使用 CycleCloud 伺服器執行 HPC 實作的成本會因許多因素而有所不同。 比方說，CycleCloud 是依所用的計算時間量收費，而且主要和 CycleCloud 伺服器通常會持續配置和執行。 執行執行節點的成本會取決於這些節點的運作時間長度以及所用的大小。 儲存體和網路功能的標準 Azure 費用也適用。

這個案例示範如何在 Azure 中執行 CFD 應用程式，因此機器需要僅適用於特定 VM 大小的 RDMA 功能。 以下是一個月每天持續配置八小時且資料輸出為 1 TB 的擴展集可能產生的成本範例。 其中也包含 Azure CycleCloud 伺服器和 Avere vFXT for Azure 安裝的定價：

* 區域：北歐
* Azure CycleCloud 伺服器：1 x 標準 D3 (4 個 CPU、14 GB 記憶體、標準 HDD 32 GB)
* Azure CycleCloud 主要伺服器：1 x 標準 D12 v (4 個 CPU、28 GB 記憶體、標準 HDD 32 GB)
* Azure CycleCloud 節點陣列：10 x 標準 H16r (16 個 CPU、112 GB 記憶體)
* Azure 叢集上的 Avere vFXT：3 x D16s v3 (200 GB OS、進階 SSD 1-TB 資料磁碟)
* 資料輸出：1 TB

針對以上所列的硬體，檢閱此[價格評估][pricing]。

## <a name="next-steps"></a>後續步驟

在您部署範例後，請深入了解 [Azure CycleCloud][cyclecloud]。

## <a name="related-resources"></a>相關資源

* [支援 RDMA 的機器執行個體][rdma]
* [自訂 RDMA 執行個體 VM][rdma-custom]

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

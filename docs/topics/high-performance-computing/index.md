---
title: Azure 上的高效能運算 (HPC)
description: 在 Azure 上建置執行中 HPC 工作負載的指南
author: adamboeglin
ms.date: 2/4/2019
---
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD026 -->

# <a name="high-performance-computing-hpc-on-azure"></a>Azure 上的高效能運算 (HPC)

## <a name="introduction-to-hpc"></a>HPC 簡介

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.youtube.com/embed/rKURT32faJk]

<!-- markdownlint-enable MD034 -->

高效能運算 (HPC) (也稱為「巨量計算」) 會使用大量 CPU 或 GPU 型電腦來解決複雜的數學工作。

許多產業都使用 HPC 來解決一些最困難的問題。  這些產業包括下列工作負載：

- Genomics
- 石油與天然氣模擬
- 財務
- 半導體設計
- Engineering
- 天氣模型

### <a name="how-is-hpc-different-on-the-cloud"></a>HPC 在雲端上有何不同？

內部部署 HPC 系統與雲端中 HPC 系統之間的其中一項主要差異，就是依照需求動態新增及移除資源的能力。  動態調整會因為瓶頸而移除計算功能，改而讓客戶能夠針對其作業需求適度調整其基礎結構的大小。

下列文章提供此動態調整功能的詳細說明。

- [大量計算架構樣式](/azure/architecture/guide/architecture-styles/big-compute?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [自動調整最佳做法](/azure/architecture/best-practices/auto-scaling?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

## <a name="implementation-checklist"></a>實作檢查清單

當您想要在 Azure 上實作自己的 HPC 解決方案時，請確定您已檢閱下列主題：

<!-- markdownlint-disable MD032 -->

> [!div class="checklist"]
> - 根據您的需求選擇適當的[架構](#infrastructure)
> - 知道哪個[計算](#compute)選項適合您的工作負載
> - 找出符合您需求的合適[儲存體](#storage)解決方案
> - 決定您要如何[管理](#management)您所有的資源
> - 針對雲端將您的[應用程式](#hpc-applications)最佳化
> - [保護](#security)您的基礎結構

<!-- markdownlint-enable MD032 -->

## <a name="infrastructure"></a>基礎結構

建置 HPC 系統時需要許多基礎結構元件。  不論您選擇如何管理您的 HPC 工作負載，計算、儲存體和網路都會提供基礎元件。

### <a name="example-hpc-architectures"></a>範例 HPC 架構

有幾個不同的方式可在 Azure 上設計及實作您的 HPC 架構。  HPC 應用程式可擴展成上千個計算核心，以擴充內部部署叢集或以 100% 雲端原生解決方案的方式執行。

下列案例概述幾個常見的 HPC 解決方案建置方式。

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
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary">Azure 上的電腦輔助工程服務</h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p>為 Azure 上的電腦輔助工程 (CAE) 提供軟體即服務 (SaaS) 平台。</p>
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
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary">Azure 上的計算流體力學 (CFD) 模擬</h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p>在 Azure 上執行計算流體力學 (CFD) 模擬。</p>
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
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary">Azure 上的 3D 影片轉譯</h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p>使用 Azure Batch 服務在 Azure 中執行原生 HPC 工作負載</p>
                    </div>
                </div>
            </article>
        </a>
    </li>
</ul>

### <a name="compute"></a>計算

Azure 提供針對 CPU 和 GPU 密集型工作負載最佳化的各種大小。

#### <a name="cpu-based-virtual-machines"></a>CPU 型虛擬機器

- [Linux VM](/azure/virtual-machines/linux/sizes-hpc?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [Windows VM](/azure/virtual-machines/windows/sizes-hpc?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) 的 VM
  
#### <a name="gpu-enabled-virtual-machines"></a>已啟用 GPU 的虛擬機器

N 系列 VM 功能 NVIDIA GPU 是專為需要大量運算或需要大量圖形的應用程式所設計，包括人工地智慧 (AI) 學習和視覺效果。

- [Linux VM](/azure/virtual-machines/linux/sizes-gpu?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [Windows VM](/azure/virtual-machines/windows/sizes-gpu?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

### <a name="storage"></a>儲存體

大規模 Batch 和 HPC 工作負載所需要的資料儲存和存取會超過傳統雲端檔案系統的容量。  有數個解決方案可管理 Azure 上 HPC 應用程式的速度和容量需求

- [Avere vFXT](https://azure.microsoft.com/services/storage/avere-vfxt/) 可供更快速、更容易取得資料儲存體，以在邊緣執行高效能運算
- [BeeGFS](https://azure.microsoft.com/resources/implement-glusterfs-on-azure/)
- [儲存體最佳化的虛擬機器](/azure/virtual-machines/windows/sizes-storage?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [Blob、資料表和佇列儲存體](/azure/storage/storage-introduction?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [Azure SMB 檔案儲存體](/azure/storage/files/storage-files-introduction?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [Intel Cloud Edition Lustre](https://azuremarketplace.microsoft.com/marketplace/apps/intel.intel-cloud-edition-gs)

如需比較 Azure 上 Lustre、GlusterFS 和 BeeGFS 的詳細資訊，請檢閱 [Azure 電子書上的平行檔案系統](https://blogs.msdn.microsoft.com/azurecat/2018/06/11/azurecat-ebook-parallel-virtual-file-systems-on-microsoft-azure/)

### <a name="networking"></a>網路功能

H16r、H16mr、A8 和 A9 VM 可以連線到高輸送量後端 RDMA 網路。 此網路可以改善 Microsoft MPI 或 Intel MPI 下執行的緊密結合平行應用程式效能。

- [支援 RDMA 的執行個體](/azure/virtual-machines/windows/sizes-hpc?context=/azure/architecture/topics/high-performance-computing/context/hpc-context#rdma-capable-instances)
- [虛擬網路](/azure/virtual-network/virtual-networks-overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [ExpressRoute](/azure/expressroute/expressroute-introduction?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

## <a name="management"></a>管理性

### <a name="do-it-yourself"></a>自己動手做

在 Azure 上從頭開始建置 HPC 系統隨可提供大量彈性，但通常需要密集維護。  

1. 在 Azure 虛擬機器或[虛擬機器擴展集](/azure/virtual-machine-scale-sets/overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)中設定自己的叢集環境。
2. 使用 Azure Resource Manager 範本來部署前置[工作負載管理員](#workload-managers)基礎結構和[應用程式](#hpc-applications)。
3. 選擇 HPC 和 GPU [VM 大小](#compute)，包括 MPI 或 GPU 工作負載的特製化硬體和網路連線。
4. 為 I/O 密集的工作負載新增[高效能儲存體](#storage)。

### <a name="hybrid-and-cloud-bursting"></a>混合式和雲端負載平衡

如果您有想要連線到 Azure 的現有內部部署 HPC 系統，則有許多資源可協助您上手。

首先，檢閱文件中的[將內部部署網路連線到 Azure 的選項](/azure/architecture/reference-architectures/hybrid-networking/?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)一文。  從那裡，您可以取得這些連線選項的資訊：

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
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary">使用 VPN 閘道將內部部署網路連線至 Azure</h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p>此參考架構會示範如何使用網站對網站虛擬私人網路 (VPN)，將內部部署網路擴充至 Azure。</p>
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
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary">使用 ExpressRoute 將內部部署網路連線至 Azure</h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p>ExpressRoute 連線會透過第三方連線提供者，使用私人的專用連線。 私人連線會將內部部署網路延伸到 Azure。</p>
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
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary">使用 ExpressRoute 搭配 VPN 容錯移轉，將內部部署網路連線至 Azure</h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p>跨越使用 ExpressRoute 搭配 VPN 閘道容錯移轉連線的 Azure 虛擬網路與內部部署網路，實作可用性高且安全的站對站網路架構。</p>
                    </div>
                </div>
            </article>
        </a>
    </li>
</ul>

安全地建立網路連線後，您即可透過現有[工作負載管理員](#workload-managers)的負載平衡功能，開始隨選使用雲端計算資源。

### <a name="marketplace-solutions"></a>Marketplace 解決方案

[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) 中提供許多工作負載管理員。

- [RogueWave CentOS 型 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
- [適用於 HPC 的 SUSE Linux Enterprise Server](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
- [TIBCO Grid Server Engine](https://azuremarketplace.microsoft.com/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
- [適用於 Windows 和 Linux 的 Azure 資料科學 VM](/azure/machine-learning/data-science-virtual-machine/overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [D3View](https://azuremarketplace.microsoft.com/marketplace/apps/xfinityinc.d3view-v5?tab=Overview)
- [UberCloud](https://azure.microsoft.com/search/marketplace/?q=ubercloud)

### <a name="azure-batch"></a>Azure Batch

[Azure Batch](/azure/batch/batch-technical-overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) 是一項平台服務，可用於在雲端有效地執行大規模的平行和高效能運算 (HPC) 應用程式。 Azure Batch 可排程要在受控集區虛擬機器上執行的需要大量運算工作，而且可以調整運算資源以符合工作的需求。

SaaS 提供者或開發人員可使用 Batch SDK 和工具，將 HPC 應用程式或容器工作負載與 Azure 進行整合、將資料暫存至 Azure，並建置作業執行管線。

### <a name="azure-cyclecloud"></a>Azure CycleCloud

[Azure CycleCloud](/azure/cyclecloud/?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) 在 Azure 上使用任何作業排程器 (如 Slurm、Grid Engine、HPC Pack、HTCondor、LSF、PBS Pro 或 Symphony) 管理 HPC 工作負載的最簡單方法

CycleCloud 可讓您：

- 部署完整叢集和其他資源，包括排程器、計算 VM、儲存體、網路和快取
- 協調作業、資料和雲端工作流程
- 讓管理員能完全控制哪些使用者可執行作業，以及執行位置和成本
- 透過進階原則和治理功能 (包括成本控制、Active Directory 整合、監視和報告) 自訂叢集並予以最佳化
- 使用目前的作業排程器和應用程式，並無需修改
- 針對各種 HPC 工作負載和產業，利用內建自動調整和經過測試的參考架構

### <a name="workload-managers"></a>工作負載管理員

下列是可以在 Azure 基礎結構中執行的叢集和工作負載管理員範例。 在 Azure VM 中建立獨立叢集，或從內部部署叢集高載至 Azure VM。

- [Alces Flight Compute](https://azuremarketplace.microsoft.com/marketplace/apps/alces-flight-limited.alces-flight-compute-solo?tab=Overview)
- [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/)
- [Bright Cluster Manager](http://www.brightcomputing.com/technology-partners/microsoft)
- [IBM Spectrum Symphony 和 Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
- [PBS Pro](http://pbspro.org)
- [Altair](http://www.altair.com/)
- [Rescale](https://www.rescale.com/azure/)
- [Microsoft HPC Pack](https://technet.microsoft.com/library/mt744885.aspx)
  - [適用於 Windows 的 HPC 套件](/azure/virtual-machines/windows/hpcpack-cluster-options?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
  - [適用於 Linux 的 HPC 套件](/azure/virtual-machines/linux/hpcpack-cluster-options?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

#### <a name="containers"></a>容器

容器也可用來管理一些 HPC 工作負載。  Azure Kubernetes Service (AKS) 等服務可讓您輕鬆地在 Azure 中部署受控 Kubernetes 叢集。

- [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [容器登錄](/azure/container-registry/container-registry-intro?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

## <a name="cost-management"></a>成本管理

您可以透過一些不同方法在 Azure 上管理您的 HPC 成本。  確定您已檢閱 [Azure 購買選項](https://azure.microsoft.com/pricing/purchase-options/)，以尋找最適合貴組織的方法。

[低優先順序 VM](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-low-priority?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) 可讓您以大幅降低的成本使用我們未運用的容量。

## <a name="security"></a>安全性

如需 Azure 上的安全性最佳做法概觀，請檢閱 [Azure 安全性文件](/azure/security/azure-security?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)。  

除了[雲端負載平衡](#hybrid-and-cloud-bursting)區段中可用的網路組態，您可以實作中樞/輪輻組態，以隔離您的計算資源：

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
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary">在 Azure 中實作中樞輪輻網路拓撲</h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p>「中樞」是 Azure 中的虛擬網路 (VNet)，可當作內部部署網路的連線中心點。 輪輻是與中樞對等的 VNet，可用於隔離工作負載。</p>
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
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary">在 Azure 中實作中樞輪輻網路拓撲與共用服務</h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p>此參考架構是建置在中樞輪輻參考架構基礎上，以包含可以被所有輪輻取用之中樞中的共用服務。</p>
                    </div>
                </div>
            </article>
        </a>
    </li>
</ul>

## <a name="hpc-applications"></a>HPC 應用程式

在 Azure 中執行自訂或商業 HPC 應用程式。 本節中的數個範例會經過基準測試，以使用其他 VM 或運算核心有效地進行擴充。 請瀏覽 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) 以取得可立即部署的解決方案。

> [!NOTE]
> 請向廠商確認任何商業應用程式在雲端中的執行授權或其他限制。 並非所有廠商都提供隨用隨付授權。 您可能需要視您的解決方案，在雲端中授權伺服器，或連線至內部部署授權伺服器。

### <a name="engineering-applications"></a>工程應用程式

- [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
- [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
- [MATLAB Distributed Computing Server](/azure/virtual-machines/windows/matlab-mdcs-cluster?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [StarCCM+](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
- [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)

### <a name="graphics-and-rendering"></a>圖形和轉譯器

- Azure Batch 上的 [Autodesk Maya、3ds Max 和 Arnold](/azure/batch/batch-rendering-service?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

### <a name="ai-and-deep-learning"></a>AI 和深入學習

- [Microsoft 辨識工具組](/cognitive-toolkit/cntk-on-azure)
- [深入學習 VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
- [深入學習的 Batch Shipyard 訣竅](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)

### <a name="mpi-providers"></a>MPI 提供者

- [Microsoft MPI](/message-passing-interface/microsoft-mpi)

## <a name="remote-visualization"></a>遠端視覺效果

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
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary">使用 Citrix 的 Linux 虛擬桌面</h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p>在 Azure 上使用 Citrix 建置適用於 Linux 桌面的 VDI 環境。</p>
                    </div>
                </div>
            </article>
        </a>
    </li>
</ul>

## <a name="performance-benchmarks"></a>效能評定

- [計算評定](/azure/virtual-machines/windows/compute-benchmark-scores?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

## <a name="customer-stories"></a>客戶案例

有許多客戶在使用 Azure 處理其 HPC 工作負載方面非常成功。  您可以在底下找到這些客戶的一些案例研究：

- [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu)
- [AXA Global P&C](https://customers.microsoft.com/story/axa-global-p-and-c)
- [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
- [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
- [EFS](https://customers.microsoft.com/story/efs-professionalservices-azure)
- [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
- [MetLife](https://enterprise.microsoft.com/customer-story/industries/insurance/metlife/)
- [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
- [明德精算顧問有限公司](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
- [Mitsubishi UFJ Securities International](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
- [NeuroInitiative](https://customers.microsoft.com/story/neuroinitiative-health-provider-azure)
- [Schlumberger](https://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
- [Towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)

## <a name="other-important-information"></a>其他重要資訊

- 在嘗試執行大規模工作負載之前，先確定您的 [vCPU 配額](/azure/virtual-machines/linux/quotas?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)已經增加。

## <a name="next-steps"></a>後續步驟

如需最新公告，請參閱：

- [Microsoft HPC 和 Batch 小組部落格](http://blogs.technet.com/b/windowshpc/)
- 瀏覽 [Azure 部落格](https://azure.microsoft.com/blog/tag/hpc/)。

### <a name="microsoft-batch-examples"></a>Microsoft Batch 範例

這些教學課程將為您提供在 Microsoft Batch 上執行應用程式的詳細資訊

- [開始使用 Batch 進行開發](/azure/batch/quick-run-dotnet?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [使用 Azure Batch 程式碼範例](https://github.com/Azure/azure-batch-samples)
- [使用低優先順序的 VM 搭配 Batch](/azure/batch/batch-low-pri-vms?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [使用 Batch Shipyard 執行容器化的 HPC 工作負載](https://github.com/Azure/batch-shipyard)
- [在 Batch 上執行平行的 R 工作負載](https://github.com/Azure/doAzureParallel)
- [在 Batch 上執行隨選 Spark 作業](https://github.com/Azure/aztk)
- [在 Batch 集區中使用需要大量運算的 VM](/azure/batch/batch-pool-compute-intensive-sizes?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
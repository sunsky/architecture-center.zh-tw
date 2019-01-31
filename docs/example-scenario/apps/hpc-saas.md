---
title: 電腦輔助工程服務
titleSuffix: Azure Example Scenarios
description: 為 Azure 上的電腦輔助工程 (CAE) 提供軟體即服務 (SaaS) 平台。
author: alexbuckgit
ms.date: 08/22/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: HPC
social_image_url: /azure/architecture/example-scenario/apps/media/architecture-hpc-saas.png
ms.openlocfilehash: bd38bd0042fceeab6efe04d7b6d1477d17ada7f5
ms.sourcegitcommit: 3b15d65e7c35a19506e562c444343f8467b6a073
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2019
ms.locfileid: "54908456"
---
# <a name="a-computer-aided-engineering-service-on-azure"></a>Azure 上的電腦輔助工程服務

此範例案例示範如何傳遞以 Azure 高效能運算 (HPC) 功能為基礎的軟體即服務 (SaaS) 平台。 此案例是以工程軟體解決方案為基礎。 不過，其架構與其他需要 HPC 資源的產業相關，例如影像轉譯、複雜模型化和財務風險計算。

此範例示範將電腦輔助工程 (CAE) 應用程式提供給工程公司和製造企業的工程軟體提供者。 CAE 解決方案能賦予創新、減少開發時間，以及降低產品設計生命週期中的成本。 這些解決方案需要大量計算資源，且通常會處理很高的資料量。 內部部署 HPC 設備或高階工作站的昂貴成本，通常讓小型工程公司、企業主和學生無法接觸到這些技術。

公司想藉由建置雲端式 HPC 技術所支援的 SaaS 平台來擴充其應用程式的市場。 其客戶應該能夠視需要支付計算資源，以及存取原本負擔不起的大規模運算能力。

公司的目標包括：

- 利用 Azure 中的 HPC 功能來加速產品設計和測試程序。
- 使用最新的硬體創新功能來執行複雜的模擬，同時將更簡單模擬的成本降至最低。
- 啟用逼真的視覺效果並在網頁瀏覽器中轉譯，而不需要高階的工程工作站。

## <a name="relevant-use-cases"></a>相關使用案例

其他相關的使用案例包括：

- 基因體研究
- 天氣模擬
- 計算化學應用程式

## <a name="architecture"></a>架構

![啟用 HPC 功能的 SaaS 解決方案架構][architecture]

- 使用者可以使用 [Apache Guacamole 服務](https://guacamole.apache.org/)，透過具有 HTML5 型 RDP 連線的瀏覽器存取 NV 系列虛擬機器 (VM)。 這些 VM 執行個體為轉譯和共同作業工作提供強大的 GPU。 使用者可以編輯其設計並檢視其結果，而不需存取高階行動運算裝置或膝上型電腦。 排程器會根據使用者定義的啟發學習法來啟動額外的 VM。
- 從桌面 CAD 工作階段，使用者可以在可用的 HPC 叢集節點上提供工作負載以供執行。 這些工作負載會執行壓力分析或計算流體力學計算等工作，因而免除專用內部部署計算叢集的需求。 您可以根據以作用中使用者的計算資源需求為基礎的負載或佇列深度，將這些叢集節點設定為自動調整。
- Azure Kubernetes Service (AKS) 用來裝載使用者可用的 Web 資源。

### <a name="components"></a>元件

- [H 系列虛擬機器](/azure/virtual-machines/linux/sizes-hpc)用來執行計算密集的模擬，例如分子建模和運算流體力學。 此解決方案也會利用遠端直接記憶體存取 (RDMA) 連線和 InfiniBand 網路功能等技術。
- [NV 系列虛擬機器](/azure/virtual-machines/windows/sizes-gpu)為工程師提供從標準網頁瀏覽器執行的高階工作站功能。 這些虛擬機器採用 NVIDIA Tesla M60 GPU，其支援進階轉譯並可執行單一的精密工作負載。
- 執行 CentOS 的[一般用途虛擬機器](/azure/virtual-machines/linux/sizes-general)可處理更傳統的工作負載，例如 Web 應用程式。
- [應用程式閘道](/azure/application-gateway/overview)會讓進入網頁伺服器的要求達到負載平衡。
- 對於不需要 HPC 或 GPU 虛擬機器高階功能的模擬，[Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) 用於以較低的成本執行可調式工作負載。
- [Altair PBS Works Suite](https://www.pbsworks.com/PBSProduct.aspx?n=PBS-Works-Suite&c=Overview-and-Capabilities) 可協調 HPC 工作流程，進而確保有足夠的虛擬機器執行個體可處理目前的負載。 當需求較低而降低成本時，它也會將虛擬機器解除配置。
- [Blob 儲存體](/azure/storage/blobs/storage-blobs-introduction)可儲存支援排定工作的檔案。

### <a name="alternatives"></a>替代項目

- [Azure CycleCloud](/azure/cyclecloud/overview) 可簡化 HPC 叢集的建立、管理、操作及最佳化。 它可提供進階原則和治理功能。 CycleCloud 支援任何作業排程器或軟體堆疊。
- [HPC Pack](/azure/virtual-machines/windows/hpcpack-cluster-options) 可以建立及管理適用於 Windows Server 型工作負載的 HPC 叢集。 HPC Pack 不是 Linux 型工作負載的選項。
- [Azure 自動化狀態設定](/azure/automation/automation-dsc-overview)提供基礎結構即程式碼方法，以定義要部署的虛擬機器和軟體。 對計算節點使用以提交至工作佇列的作業數目為基礎的自動調整規則，可以將虛擬機器部署為虛擬機器擴展集的一部分。 需要新的虛擬機器時，可使用 Azure 映像庫中最新修補的映像來佈建，然後透過 PowerShell DSC 設定指令碼來安裝並設定所需的軟體。
- [Azure Functions](/azure/azure-functions/functions-overview)

## <a name="considerations"></a>考量

- 雖然使用基礎結構即程式碼方法很適合用來管理虛擬機器的組建定義，使用指令碼可能需要很長的時間來佈建新的虛擬機器。 此解決方案找到很好的折衷方式，就是使用 DSC 指令碼定期建立黃金映像，該映像接著可用來佈建新的虛擬機器，速度比使用 DSC 視需要完全 建置 VM 還要快。 Azure DevOps 服務或其他 CI/CD 工具可以使用 DSC 指令碼定期重新整理黃金映像。
- 透過快速提供計算資源來平衡整體解決方案成本是一項重要考量。 佈建一些 N 系列虛擬機器執行個體並將其置於已解除配置的狀態，可降低營運成本。 需要額外的虛擬機器時，重新配置現有的執行個體則需在不同的主機上開啟虛擬機器，但可免除 OS 找出並安裝 GPU 驅動程式所需的 PCI 匯流排偵測時間，因為已取消佈建而後重新佈建的虛擬機器會在重新啟動時保留適用於 GPU 的相同 PCI 匯流排。
- 原始架構完全仰賴 Azure 虛擬機器來執行模擬。 為了讓不需要所有虛擬機器功能的工作負載降低成本，這些工作負載已容器化並且部署至 Azure Kubernetes Service (AKS)。
- 公司的員工已具有開放原始碼技術的技能。 他們可藉由採用 Linux 和 Kubernetes 等技術，進而利用這些技能。

## <a name="pricing"></a>價格

為了協助您探索執行此案例的成本，許多必要服務都會在[成本計算機範例][calculator]中預先設定。 您解決方案的成本取決於要符合您的需求所需的服務數目和規模。

下列考量會影響此解決方案的大部分成本：

- 佈建額外的執行個體時，Azure 虛擬機器成本會以線性方式增加。 解除配置的虛擬機器只會產生儲存體成本，而不會產生計算成本。 當要求很高時，即可重新配置這些已解除配置的機器。
- Azure Kubernetes 服務成本是以為了支援工作負載所選的 VM 類型為基礎。 此成本會根據叢集中的 VM 數目以線性方式增加。

## <a name="next-steps"></a>後續步驟

- 閱讀 [Altair 客戶案例][source-document]。 此範例案例是以其架構版本為基礎。
- 檢閱 Azure 中其他可用的 [Big Compute 解決方案](https://azure.microsoft.com/solutions/big-compute)。

<!-- links -->
[architecture]: ./media/architecture-hpc-saas.png
[source-document]: https://customers.microsoft.com/story/altair-manufacturing-azure
[calculator]: https://azure.com/e/3cb9ccdc893f41ffbcdb00c328178ccf

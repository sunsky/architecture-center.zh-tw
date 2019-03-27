---
title: 選擇 Azure 計算服務的準則
titleSuffix: Azure Application Architecture Guide
description: 比較數個軸間的 Azure 計算服務。
author: MikeWasson
ms.date: 08/08/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 2b6b9b941bf7a3c0136b71ecb65bfe4b4a59e07b
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58245599"
---
# <a name="criteria-for-choosing-an-azure-compute-service"></a>選擇 Azure 計算服務的準則

*計算*一詞是指您的應用程式執行所在運算資源的裝載模型。 下表比較數個軸間的 Azure 計算服務。 為您的應用程式選取計算選項時，請參考這些表格。

## <a name="hosting-model"></a>裝載模型

<!-- markdownlint-disable MD033 -->

| 準則 | 虛擬機器 | App Service 方案 | Service Fabric | Azure Functions | Azure Kubernetes Service | Container Instances | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| 應用程式組合 | 無從驗證 | 應用程式、容器 | 服務、來賓可執行檔、容器 | Functions | 容器 | 容器 | Scheduled jobs  |
| 密度 | 無從驗證 | 每個執行個體多個應用程式，透過 App Service 方案 | 每個 VM 多個服務 | 無伺服器 <a href="#note1"><sup>1</sup></a> | 每個節點有多個容器 |沒有專用執行個體 | 每個 VM 多個應用程式 |
| 最小節點數目 | 1 <a href="#note2"><sup>2</sup></a>  | 1 | 5 <a href="#note3"><sup>3</sup></a> | 無伺服器 <a href="#note1"><sup>1</sup></a> | 3 <a href="#note3"><sup>3</sup></a> | 沒有專用節點 | 1 <a href="#note4"><sup>4</sup></a> |
| 狀態管理 | 無狀態或可設定狀態 | 無狀態 | 無狀態或可設定狀態 | 無狀態 | 無狀態或可設定狀態 | 無狀態 | 無狀態 |
| Web 裝載 | 無從驗證 | 內建 | 無從驗證 | 不適用 | 無從驗證 | 無從驗證 | 否 |
| 可以部署到專用的 VNet 嗎？ | 支援 | 支援 <a href="#note5"><sup>5</sup></a> | 支援 | 支援 <a href="#note5"><sup>5</sup></a> | [支援](/azure/aks/networking-overview) | 不支援 | 支援 |
| 混合式連線 | 支援 | 支援 <a href="#note6"><sup>6</sup></a>  | 支援 | 支援 <a href="#node7"><sup>7</sup></a> | 支援 | 不支援 | 支援 |

注意

1. <span id="note1">如果是使用使用情況方案。如果是使用 App Service 方案，則函式會在配置給您的 App Service 方案的 VM 上執行。請參閱[選擇正確的 Azure Functions 服務方案][function-plans]。</span>
2. <span id="note2">具有兩個或更多個執行個體的較高 SLA。</span>
3. <span id="note3">對生產環境的建議。</span>
4. <span id="note4">作業完成後可相應減少為零。</span>
5. <span id="note5">需要 App Service Environment (ASE)。</span>
6. <span id="note6">使用 [Azure App Service 混合式連線][app-service-hybrid].</span>
7. <span id="note7">需要 App Service 方案。</span>

## <a name="devops"></a>DevOps

| 準則 | 虛擬機器 | App Service 方案 | Service Fabric | Azure Functions | Azure Kubernetes Service | Container Instances | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| 本機偵錯 | 無從驗證 | IIS Express、其他<a href="#note1b"><sup>1</sup></a> | 本機節點叢集 | Visual Studio 或 Azure Functions CLI | Minikube，其他 | 本機容器執行階段 | 不支援 |
| 程式設計模型 | 無從驗證 | Web 和 API 應用程式、背景工作的 WebJobs | 來賓可執行檔、服務模型、執行者模型、容器 | 具有觸發程序的函式 | 無從驗證 | 無從驗證 | 命令列應用程式 |
| 應用程式更新 | 沒有內建支援 | 部署位置 | 輪流升級 (每個服務) | 部署位置 | 輪流更新 | 不適用 |

注意

1. <span id="note1b">選項包括 IIS Express for ASP.NET 或 node.js (iisnode)；PHP Web 伺服器；Azure Toolkit for IntelliJ、Azure Toolkit for Eclipse。App Service 也支援已部署 Web 應用程式的遠端偵錯。</span>
2. <span id="note2b">請參閱 [Resource Manager 提供者、區域、API 版本及結構描述][resource-manager-supported-services]。</span>

## <a name="scalability"></a>延展性

| 準則 | 虛擬機器 | App Service 方案 | Service Fabric | Azure Functions | Azure Kubernetes Service | Container Instances | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| 自動調整 | VM 擴展集 | 內建服務 | VM 擴展集 | 內建服務 | 不支援 | 不支援 | N/A |
| 負載平衡器 | Azure Load Balancer | 整合式 | Azure Load Balancer | 整合式 | 整合式 |  沒有內建支援 | Azure Load Balancer |
| 調整限制<a href="#note1c"><sup>1</sup></a> | 平台映像：每個 VMSS 1000 個節點，自訂映像：每個 VMSS 100 個節點 | 20 個執行個體，App Service 環境 100 個 | 每個 VMSS 100 個節點 | 每個 Function 應用程式 200 個執行個體 | 每個叢集 100 個節點 (預設限制) |每個訂用帳戶 20 個容器群組 (預設限制)。 | 20 個核心限制 (預設限制)。 |

注意

1. <span id="note1c">請參閱 [Azure 訂用帳戶和服務限制、配額與限制](/azure/azure-subscription-service-limits)</span>。

## <a name="availability"></a>可用性

| 準則 | 虛擬機器 | App Service 方案 | Service Fabric | Azure Functions | Azure Kubernetes Service | Container Instances | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| SLA | [虛擬機器的 SLA][sla-vm] | [App Service 的 SLA][sla-app-service] | [Service Fabric 的 SLA][sla-sf] | [Functions 的 SLA][sla-functions] | [AKS 的 SLA][sla-acs] | [容器執行個體的 SLA](https://azure.microsoft.com/support/legal/sla/container-instances/) | [Azure Batch 的 SLA][sla-batch] |
| 多區域容錯移轉 | 流量管理員 | 流量管理員 | 流量管理員、多區域叢集 | 不支援 | 流量管理員 | 不支援 | 不支援 |

## <a name="other"></a>其他

| 準則 | 虛擬機器 | App Service 方案 | Service Fabric | Azure Functions | Azure Kubernetes Service | Container Instances | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| SSL | 已在 VM 中設定 | 支援 | 支援  | 支援 | [輸入控制器](/azure/aks/ingress) | 使用[側車](../../patterns/sidecar.md)容器 | 支援 |
| 成本 | [Windows][cost-windows-vm]、[Linux][cost-linux-vm] | [App Service 價格][cost-app-service] | [Service Fabric 價格][cost-service-fabric] | [Azure Functions 價格][cost-functions] | [AKS 定價][cost-acs] | [容器執行個體價格](https://azure.microsoft.com/pricing/details/container-instances/) | [Azure Batch 價格][cost-batch]
| 合適的架構樣式 | [多層式架構 (N-Tier)][n-tier]、[Big compute][big-compute] (HPC) | [Web 佇列背景工作角色][w-q-w], [多層式架構 (N-Tier)][n-tier] | [微服務][microservices][事件驅動架構 (EDA)][event-driven] | [微服務][microservices][事件驅動架構 (EDA)][event-driven] | [微服務][microservices][事件驅動架構 (EDA)][event-driven] | [微服務][microservices]、工作自動化、批次作業  | [Big compute][big-compute] (HPC) |

<!-- markdownlint-enable MD033 -->

[cost-linux-vm]: https://azure.microsoft.com/pricing/details/virtual-machines/linux/
[cost-windows-vm]: https://azure.microsoft.com/pricing/details/virtual-machines/windows/
[cost-app-service]: https://azure.microsoft.com/pricing/details/app-service/
[cost-service-fabric]: https://azure.microsoft.com/pricing/details/service-fabric/
[cost-functions]: https://azure.microsoft.com/pricing/details/functions/
[cost-acs]: https://azure.microsoft.com/pricing/details/kubernetes-service/
[cost-batch]: https://azure.microsoft.com/pricing/details/batch/

[function-plans]: /azure/azure-functions/functions-scale
[sla-acs]: https://azure.microsoft.com/support/legal/sla/kubernetes-service
[sla-app-service]: https://azure.microsoft.com/support/legal/sla/app-service/
[sla-batch]: https://azure.microsoft.com/support/legal/sla/batch/
[sla-functions]: https://azure.microsoft.com/support/legal/sla/functions/
[sla-sf]: https://azure.microsoft.com/support/legal/sla/service-fabric/
[sla-vm]: https://azure.microsoft.com/support/legal/sla/virtual-machines/

[resource-manager-supported-services]: /azure/azure-resource-manager/resource-manager-supported-services
[scale-acs]: /azure/container-service/kubernetes/container-service-scale#scaling-considerations

[n-tier]: ../architecture-styles/n-tier.md
[w-q-w]: ../architecture-styles/web-queue-worker.md
[microservices]: ../architecture-styles/microservices.md
[event-driven]: ../architecture-styles/event-driven.md
[big-date]: ../architecture-styles/big-data.md
[big-compute]: ../architecture-styles/big-compute.md

[app-service-hybrid]: /azure/app-service/app-service-hybrid-connections

---
title: CAF：軟體定義網路 - 僅限 PaaS
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 討論雲端式網路功能的僅限 PaaS 模型
author: rotycenh
ms.openlocfilehash: 2f3f82d781ddb6544721e82e7b7d795222a2f8ff
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241199"
---
# <a name="software-defined-networks-paas-only"></a><span data-ttu-id="65bbc-103">軟體定義網路：僅限 PaaS</span><span class="sxs-lookup"><span data-stu-id="65bbc-103">Software Defined Networks: PaaS-only</span></span>

<span data-ttu-id="65bbc-104">當您實作平台即服務 (PaaS) 資源時，部署程序會利用對於該網路的有限控制項，自動建立假設的基礎網路，包括負載平衡、連接埠封鎖，以及連線至其他 PaaS 服務。</span><span class="sxs-lookup"><span data-stu-id="65bbc-104">When you implement a platform as a service (PaaS) resource, the deployment process automatically creates an assumed underlying network with a limited number of controls over that network, including load balancing, port blocking, and connections to other PaaS services.</span></span>

<span data-ttu-id="65bbc-105">在 Azure 中，可以將數個 PaaS 資源類型[部署到](/azure/virtual-network/virtual-network-for-azure-services)或[連線到](/azure/virtual-network/virtual-network-service-endpoints-overview)虛擬網路，讓這些資源能與您現有的虛擬網路基礎結構整合。</span><span class="sxs-lookup"><span data-stu-id="65bbc-105">In Azure, several PaaS resource types can be [deployed into](/azure/virtual-network/virtual-network-for-azure-services) or [connected to](/azure/virtual-network/virtual-network-service-endpoints-overview) a virtual network, allowing these resources to integrate with your existing virtual networking infrastructure.</span></span> <span data-ttu-id="65bbc-106">不過，在許多情況下，僅限 PaaS 的網路架構只依賴 PaaS 資源原生提供的這些預設網路功能，就足以符合工作負載需求。</span><span class="sxs-lookup"><span data-stu-id="65bbc-106">However, in many cases a PaaS only networking architecture, relying only on these default networking capabilities natively provided by PaaS resources, is sufficient to meet workload requirements.</span></span>

<span data-ttu-id="65bbc-107">如果您考慮使用僅限 PaaS 的網路架構，請務必驗證符合您需求的必要假設。</span><span class="sxs-lookup"><span data-stu-id="65bbc-107">If you are considering a PaaS only networking architecture, be sure you validate that the required assumptions align with your requirements.</span></span>

## <a name="paas-only-assumptions"></a><span data-ttu-id="65bbc-108">僅限 PaaS 的假設</span><span class="sxs-lookup"><span data-stu-id="65bbc-108">PaaS-only assumptions</span></span>

<span data-ttu-id="65bbc-109">部署僅限 PaaS 網路架構的假設如下：</span><span class="sxs-lookup"><span data-stu-id="65bbc-109">Deploying a PaaS-only networking architecture assumes the following:</span></span>

- <span data-ttu-id="65bbc-110">正在部署的應用程式是獨立應用程式，或只是相依於其他 PaaS 資源。</span><span class="sxs-lookup"><span data-stu-id="65bbc-110">The application being deployed is a standalone application OR is dependent on only other PaaS resources.</span></span>
- <span data-ttu-id="65bbc-111">您的 IT 作業小組可以更新其工具、訓練和流程，以支援獨立 PaaS 應用程式的管理、設定和部署。</span><span class="sxs-lookup"><span data-stu-id="65bbc-111">Your IT operations teams can update their tools, training, and processes to support management, configuration, and deployment of standalone PaaS applications.</span></span>
- <span data-ttu-id="65bbc-112">PaaS 應用程式不屬於將要包含 IaaS 資源的更廣泛雲端移轉工作。</span><span class="sxs-lookup"><span data-stu-id="65bbc-112">The PaaS application is not part of a broader cloud migration effort that will include IaaS resources.</span></span>

<span data-ttu-id="65bbc-113">這些假設是對應於部署僅限 PaaS 網路的最小限定詞。</span><span class="sxs-lookup"><span data-stu-id="65bbc-113">These assumptions are minimum qualifiers aligned to deploying a PaaS-only network.</span></span> <span data-ttu-id="65bbc-114">雖然這個方法可能與單一應用程式部署的需求相符，但您的雲端採用小組應該檢查下列長期問題：</span><span class="sxs-lookup"><span data-stu-id="65bbc-114">While this approach may align with the requirements of a single application deployment, your Cloud Adoption Team should examine these long-term questions:</span></span>

- <span data-ttu-id="65bbc-115">此部署會擴大規模而要求存取其他非 PaaS 資源嗎？</span><span class="sxs-lookup"><span data-stu-id="65bbc-115">Will this deployment expand in scope or scale to require access to other non-PaaS resources?</span></span>
- <span data-ttu-id="65bbc-116">所規劃的其他 PaaS 部署超過目前的解決方案嗎？</span><span class="sxs-lookup"><span data-stu-id="65bbc-116">Are other PaaS deployments planned beyond the current solution?</span></span>
- <span data-ttu-id="65bbc-117">組織是否有其他未來雲端移轉的計劃？</span><span class="sxs-lookup"><span data-stu-id="65bbc-117">Does the organization have plans for other future cloud migrations?</span></span>

<span data-ttu-id="65bbc-118">這些問題的答案並不會阻止小組選擇 PaaS 僅限選項，但是應在做出最後決定前加以考量。</span><span class="sxs-lookup"><span data-stu-id="65bbc-118">The answers to these questions would not preclude a team from choosing a PaaS only option but should be considered before making a final decision.</span></span>

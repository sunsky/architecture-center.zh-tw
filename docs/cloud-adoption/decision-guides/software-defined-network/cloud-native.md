---
title: CAF：軟體定義網路 - 雲端原生
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 討論雲端原生的虛擬網路服務
author: rotycenh
ms.openlocfilehash: c6200491bc9ba35a9f00e0003e51716b58628980
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58242149"
---
# <a name="software-defined-networks-cloud-native"></a><span data-ttu-id="4b6df-103">軟體定義網路：雲端原生</span><span class="sxs-lookup"><span data-stu-id="4b6df-103">Software Defined Networks: Cloud native</span></span>

<span data-ttu-id="4b6df-104">將諸如虛擬機器之類的 IaaS 資源部署到雲端平台時，需要雲端原生虛擬網路。</span><span class="sxs-lookup"><span data-stu-id="4b6df-104">A cloud native virtual network is a required when deploying IaaS resources such as virtual machines to a cloud platform.</span></span> <span data-ttu-id="4b6df-105">從外部來源 (類似於 Web) 對虛擬網路的存取需要明確佈建。</span><span class="sxs-lookup"><span data-stu-id="4b6df-105">Access to virtual networks from external sources, similar to the web, need to be explicitly provisioned.</span></span> <span data-ttu-id="4b6df-106">這些類型的虛擬網路支援建立子網路、路由規則和虛擬防火牆，以及流量管理裝置。</span><span class="sxs-lookup"><span data-stu-id="4b6df-106">These types of virtual networks support the creation of subnets, routing rules, and virtual firewall and traffic management devices.</span></span>

<span data-ttu-id="4b6df-107">雲端原生虛擬網路並不依賴您組織的內部部署或其他非雲端資源來支援雲端裝載的工作負載。</span><span class="sxs-lookup"><span data-stu-id="4b6df-107">A cloud native virtual network has no dependencies on your organization's on-premises or other non-cloud resources to support the cloud-hosted workloads.</span></span> <span data-ttu-id="4b6df-108">所有必要的資源都會佈建在虛擬網路本身，或使用受控 PaaS 供應項目進行佈建。</span><span class="sxs-lookup"><span data-stu-id="4b6df-108">All required resources are provisioned either in the virtual network itself or by using managed PaaS offerings.</span></span>

## <a name="cloud-native-assumptions"></a><span data-ttu-id="4b6df-109">雲端原生假設事項</span><span class="sxs-lookup"><span data-stu-id="4b6df-109">Cloud native assumptions</span></span>

<span data-ttu-id="4b6df-110">部署雲端原生虛擬網路時會假設以下事項：</span><span class="sxs-lookup"><span data-stu-id="4b6df-110">Deploying a cloud native virtual network assumes the following:</span></span>

- <span data-ttu-id="4b6df-111">您對虛擬網路部署的工作負載，與只能從您內部部署網路內部存取的應用程式和密碼，兩者之間並沒有相依性。</span><span class="sxs-lookup"><span data-stu-id="4b6df-111">The workloads you deploy to the virtual network have no dependencies on applications or services that are accessible only from inside your on-premises network.</span></span> <span data-ttu-id="4b6df-112">除非它們提供可透過公用網際網路存取的端點，否則雲端平台裝載的資源無法使用內部部署內裝載的應用程式和服務。</span><span class="sxs-lookup"><span data-stu-id="4b6df-112">Unless they provide endpoints accessible over the public Internet, applications and services hosted internally on-premises are not usable by resources hosted on a cloud platform.</span></span>
- <span data-ttu-id="4b6df-113">您工作負載的身分識別管理和存取控制取決於雲端平台的身分識別服務或雲端環境中裝載的 IssS 伺服器。</span><span class="sxs-lookup"><span data-stu-id="4b6df-113">Your workload's identity management and access control depends on the cloud platform's identity services or IaaS servers hosted in your cloud environment.</span></span> <span data-ttu-id="4b6df-114">您將不需要直接連線到內部部署或其他外部位置裝載的身分識別服務。</span><span class="sxs-lookup"><span data-stu-id="4b6df-114">You will not need to directly connect to identity services hosted on-premises or other external locations.</span></span>
- <span data-ttu-id="4b6df-115">您的身分識別服務不需要支援單一登入 (SSO) 與內部部署目錄。</span><span class="sxs-lookup"><span data-stu-id="4b6df-115">Your identity services do not need to support single sign-on (SSO) with on-premises directories.</span></span>

<span data-ttu-id="4b6df-116">雲端原生虛擬網路沒有任何外部相依性。</span><span class="sxs-lookup"><span data-stu-id="4b6df-116">Cloud native virtual networks have no external dependencies.</span></span> <span data-ttu-id="4b6df-117">這讓它們能夠很容易地部署和設定，因此此架構通常是適用於實驗用途或其他較小型獨立或快速重複部署的最佳選擇。</span><span class="sxs-lookup"><span data-stu-id="4b6df-117">This makes them simple to deploy and configure, and as a result this architecture is often the best choice for experiments or other smaller self-contained or rapidly iterating deployments.</span></span>

<span data-ttu-id="4b6df-118">您的雲端採用小組在討論雲端原生虛擬網路架構時，應該考量以下其他問題：</span><span class="sxs-lookup"><span data-stu-id="4b6df-118">Additional issues your Cloud Adoption Team should consider when discussing a cloud native virtual networking architecture include:</span></span>

- <span data-ttu-id="4b6df-119">設計成在內部部署資料中心執行的現有工作負載可能需要大規模修改，才能善用雲端的功能，例如儲存體或驗證服務。</span><span class="sxs-lookup"><span data-stu-id="4b6df-119">Existing workloads designed to run in an on-premises datacenter may need extensive modification to take advantage of cloud-based functionality, such as storage or authentication services.</span></span>
- <span data-ttu-id="4b6df-120">雲端原生網路僅透過雲端平台管理工具進行管理，因此隨著時間過去，可能會導致管理和原則與您現有的 IT 標準產生出入。</span><span class="sxs-lookup"><span data-stu-id="4b6df-120">Cloud native networks are managed solely through the cloud platform management tools, and therefore may lead to management and policy divergence from your existing IT standards as time goes on.</span></span>

## <a name="learn-more"></a><span data-ttu-id="4b6df-121">深入了解</span><span class="sxs-lookup"><span data-stu-id="4b6df-121">Learn more</span></span>

<span data-ttu-id="4b6df-122">如需 Azure 平台中雲端原生虛擬網路的詳細資訊，請參閱下列各項。</span><span class="sxs-lookup"><span data-stu-id="4b6df-122">See the following for more information about cloud native virtual networking in the Azure platform.</span></span>

- <span data-ttu-id="4b6df-123">[Azure 虛擬網路：使用說明指南](/azure/virtual-network/virtual-network-vnet-plan-design-arm)。</span><span class="sxs-lookup"><span data-stu-id="4b6df-123">[Azure Virtual Network: How-to guides](/azure/virtual-network/virtual-network-vnet-plan-design-arm).</span></span> <span data-ttu-id="4b6df-124">新建立的 Azure 虛擬網路預設為雲端原生網路。</span><span class="sxs-lookup"><span data-stu-id="4b6df-124">Newly created Azure Virtual Networks are cloud-native by default.</span></span> <span data-ttu-id="4b6df-125">您可以使用這些指南，協助規劃虛擬網路的設計與部署。</span><span class="sxs-lookup"><span data-stu-id="4b6df-125">Use these guides to help plan the design and deployment of your virtual networks.</span></span>
- <span data-ttu-id="4b6df-126">[訂用帳戶限制：網路](/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)。</span><span class="sxs-lookup"><span data-stu-id="4b6df-126">[Subscription limits: Networking](/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).</span></span> <span data-ttu-id="4b6df-127">任何單一虛擬網路和連線的資源只能存在於單一訂用帳戶內，並受訂用帳戶限制約束。</span><span class="sxs-lookup"><span data-stu-id="4b6df-127">Any single virtual network and connected resources can only exist within a single subscription, and are bound by subscription limits.</span></span>

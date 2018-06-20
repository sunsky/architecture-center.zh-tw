---
title: Azure 應用程式的設計原則
description: Azure 應用程式的設計原則
author: MikeWasson
ms.openlocfilehash: 462896098c668c0775464ca498925266cd73c6e1
ms.sourcegitcommit: 26b04f138a860979aea5d253ba7fecffc654841e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36206793"
---
# <a name="design-principles-for-azure-applications"></a><span data-ttu-id="e1841-103">Azure 應用程式的設計原則</span><span class="sxs-lookup"><span data-stu-id="e1841-103">Design principles for Azure applications</span></span>

<span data-ttu-id="e1841-104">請遵循這些設計原則，讓您的應用程式更有擴充空間、可容易復原且更方便管理。</span><span class="sxs-lookup"><span data-stu-id="e1841-104">Follow these design principles to make your application more scalable, resilient, and manageable.</span></span> 

<span data-ttu-id="e1841-105">**[自我修復設計](self-healing.md)**。</span><span class="sxs-lookup"><span data-stu-id="e1841-105">**[Design for self healing](self-healing.md)**.</span></span> <span data-ttu-id="e1841-106">在分散式系統中，發生失敗在所難免。</span><span class="sxs-lookup"><span data-stu-id="e1841-106">In a distributed system, failures happen.</span></span> <span data-ttu-id="e1841-107">將您的應用程式設計為可在發生失敗時自我修復。</span><span class="sxs-lookup"><span data-stu-id="e1841-107">Design your application to be self healing when failures occur.</span></span>

<span data-ttu-id="e1841-108">**[讓各個項目都有備援](redundancy.md)**。</span><span class="sxs-lookup"><span data-stu-id="e1841-108">**[Make all things redundant](redundancy.md)**.</span></span> <span data-ttu-id="e1841-109">將備援建置到您的應用程式中，以避免發生單一失敗點。</span><span class="sxs-lookup"><span data-stu-id="e1841-109">Build redundancy into your application, to avoid having single points of failure.</span></span>
 
<span data-ttu-id="e1841-110">**[最小化協調](minimize-coordination.md)**。</span><span class="sxs-lookup"><span data-stu-id="e1841-110">**[Minimize coordination](minimize-coordination.md)**.</span></span> <span data-ttu-id="e1841-111">將來應用程式服務之間的協調最小化，以達成延展性。</span><span class="sxs-lookup"><span data-stu-id="e1841-111">Minimize coordination between application services to achieve scalability.</span></span>
 
<span data-ttu-id="e1841-112">**[相應放大的設計](scale-out.md)**。設計您的應用程式，讓它可水平調整，以依需求新增或移除新的執行個體。</span><span class="sxs-lookup"><span data-stu-id="e1841-112">**[Design to scale out](scale-out.md)**. Design your application so that it can scale horizontally, adding or removing new instances as demand requires.</span></span>

<span data-ttu-id="e1841-113">**[在限制中使用分割](partition.md)**。</span><span class="sxs-lookup"><span data-stu-id="e1841-113">**[Partition around limits](partition.md)**.</span></span> <span data-ttu-id="e1841-114">使用分割作業來解決資料庫、網路和計算限制。</span><span class="sxs-lookup"><span data-stu-id="e1841-114">Use partitioning to work around database, network, and compute limits.</span></span>

<span data-ttu-id="e1841-115">**[作業的設計](design-for-operations.md)**。</span><span class="sxs-lookup"><span data-stu-id="e1841-115">**[Design for operations](design-for-operations.md)**.</span></span> <span data-ttu-id="e1841-116">設計應用程式，讓作業小組擁有所需的工具。</span><span class="sxs-lookup"><span data-stu-id="e1841-116">Design your application so that the operations team has the tools they need.</span></span>

<span data-ttu-id="e1841-117">**[使用受管理的服務](managed-services.md)**。</span><span class="sxs-lookup"><span data-stu-id="e1841-117">**[Use managed services](managed-services.md)**.</span></span> <span data-ttu-id="e1841-118">可能的話，請使用平台即服務 (PaaS) 而非基礎結構即服務 (IaaS)。</span><span class="sxs-lookup"><span data-stu-id="e1841-118">When possible, use platform as a service (PaaS) rather than infrastructure as a service (IaaS).</span></span>

<span data-ttu-id="e1841-119">**[使用作業的最佳資料存放區](use-the-best-data-store.md)**。</span><span class="sxs-lookup"><span data-stu-id="e1841-119">**[Use the best data store for the job](use-the-best-data-store.md)**.</span></span> <span data-ttu-id="e1841-120">挑選最適合您資料的儲存體技術，以及了解使用方式。</span><span class="sxs-lookup"><span data-stu-id="e1841-120">Pick the storage technology that is the best fit for your data and how it will be used.</span></span> 
 
<span data-ttu-id="e1841-121">**[進化的設計](design-for-evolution.md)**。</span><span class="sxs-lookup"><span data-stu-id="e1841-121">**[Design for evolution](design-for-evolution.md)**.</span></span> <span data-ttu-id="e1841-122">所有成功的應用程式，在經過一段時間後都會有所變更。</span><span class="sxs-lookup"><span data-stu-id="e1841-122">All successful applications change over time.</span></span> <span data-ttu-id="e1841-123">進化的設計是連續創新的關鍵。</span><span class="sxs-lookup"><span data-stu-id="e1841-123">An evolutionary design is key for continuous innovation.</span></span>

<span data-ttu-id="e1841-124">**[針對企業的需求而建置](build-for-business.md)**。</span><span class="sxs-lookup"><span data-stu-id="e1841-124">**[Build for the needs of business](build-for-business.md)**.</span></span> <span data-ttu-id="e1841-125">每個設計決策必須對應某個業務需求。</span><span class="sxs-lookup"><span data-stu-id="e1841-125">Every design decision must be justified by a business requirement.</span></span>


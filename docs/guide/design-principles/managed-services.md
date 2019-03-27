---
title: 使用受控服務
titleSuffix: Azure Application Architecture Guide
description: 可能的話，請使用平台即服務 (PaaS) 而非基礎結構即服務 (IaaS)。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: f08914e1eacc4f02fdb16093fe590f46249e3da3
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58249533"
---
# <a name="use-managed-services"></a><span data-ttu-id="d4ea9-103">使用受控服務</span><span class="sxs-lookup"><span data-stu-id="d4ea9-103">Use managed services</span></span>

## <a name="when-possible-use-platform-as-a-service-paas-rather-than-infrastructure-as-a-service-iaas"></a><span data-ttu-id="d4ea9-104">可能的話，請使用平台即服務 (PaaS) 而非基礎結構即服務 (IaaS)</span><span class="sxs-lookup"><span data-stu-id="d4ea9-104">When possible, use platform as a service (PaaS) rather than infrastructure as a service (IaaS)</span></span>

<span data-ttu-id="d4ea9-105">IaaS 就像是有一箱零件。</span><span class="sxs-lookup"><span data-stu-id="d4ea9-105">IaaS is like having a box of parts.</span></span> <span data-ttu-id="d4ea9-106">您可以建立任何項目，但您必須自行組合。</span><span class="sxs-lookup"><span data-stu-id="d4ea9-106">You can build anything, but you have to assemble it yourself.</span></span> <span data-ttu-id="d4ea9-107">受控服務則更容易設定及管理。</span><span class="sxs-lookup"><span data-stu-id="d4ea9-107">Managed services are easier to configure and administer.</span></span> <span data-ttu-id="d4ea9-108">您不需要佈建 VM、設定 VNet、管理修補程式和更新，以及與在 VM 上執行中軟體相關聯的所有其他額外負荷。</span><span class="sxs-lookup"><span data-stu-id="d4ea9-108">You don't need to provision VMs, set up VNets, manage patches and updates, and all of the other overhead associated with running software on a VM.</span></span>

<span data-ttu-id="d4ea9-109">例如，假設您的應用程式需要訊息佇列。</span><span class="sxs-lookup"><span data-stu-id="d4ea9-109">For example, suppose your application needs a message queue.</span></span> <span data-ttu-id="d4ea9-110">您可以在 VM 上設定您自己的訊息服務，使用 RabbitMQ 之類的項目。</span><span class="sxs-lookup"><span data-stu-id="d4ea9-110">You could set up your own messaging service on a VM, using something like RabbitMQ.</span></span> <span data-ttu-id="d4ea9-111">Azure 服務匯流排已提供可靠的傳訊即服務，並且較容易設定。</span><span class="sxs-lookup"><span data-stu-id="d4ea9-111">But Azure Service Bus already provides reliable messaging as service, and it's simpler to set up.</span></span> <span data-ttu-id="d4ea9-112">只要建立服務匯流排命名空間 (這可以隨著部署指令碼的一部分完成)，然後使用用戶端 SDK 呼叫服務匯流排。</span><span class="sxs-lookup"><span data-stu-id="d4ea9-112">Just create a Service Bus namespace (which can be done as part of a deployment script) and then call Service Bus using the client SDK.</span></span>

<span data-ttu-id="d4ea9-113">當然，您的應用程式可能有使 IaaS 方法更合適的特定需求。</span><span class="sxs-lookup"><span data-stu-id="d4ea9-113">Of course, your application may have specific requirements that make an IaaS approach more suitable.</span></span> <span data-ttu-id="d4ea9-114">不過，即使您的應用程式是以 IaaS 為基礎，請尋找能自然併入受控服務的位置。</span><span class="sxs-lookup"><span data-stu-id="d4ea9-114">However, even if your application is based on IaaS, look for places where it may be natural to incorporate managed services.</span></span> <span data-ttu-id="d4ea9-115">這些包括快取、佇列和資料儲存體。</span><span class="sxs-lookup"><span data-stu-id="d4ea9-115">These include cache, queues, and data storage.</span></span>

| <span data-ttu-id="d4ea9-116">而不是執行...</span><span class="sxs-lookup"><span data-stu-id="d4ea9-116">Instead of running...</span></span> | <span data-ttu-id="d4ea9-117">考慮使用...</span><span class="sxs-lookup"><span data-stu-id="d4ea9-117">Consider using...</span></span> |
|-----------------------|-------------|
| <span data-ttu-id="d4ea9-118">Active Directory</span><span class="sxs-lookup"><span data-stu-id="d4ea9-118">Active Directory</span></span> | <span data-ttu-id="d4ea9-119">Azure Active Directory Domain Services</span><span class="sxs-lookup"><span data-stu-id="d4ea9-119">Azure Active Directory Domain Services</span></span> |
| <span data-ttu-id="d4ea9-120">Elasticsearch</span><span class="sxs-lookup"><span data-stu-id="d4ea9-120">Elasticsearch</span></span> | <span data-ttu-id="d4ea9-121">Azure 搜尋服務</span><span class="sxs-lookup"><span data-stu-id="d4ea9-121">Azure Search</span></span> |
| <span data-ttu-id="d4ea9-122">Hadoop</span><span class="sxs-lookup"><span data-stu-id="d4ea9-122">Hadoop</span></span> | <span data-ttu-id="d4ea9-123">HDInsight</span><span class="sxs-lookup"><span data-stu-id="d4ea9-123">HDInsight</span></span> |
| <span data-ttu-id="d4ea9-124">IIS</span><span class="sxs-lookup"><span data-stu-id="d4ea9-124">IIS</span></span> | <span data-ttu-id="d4ea9-125">App Service 方案</span><span class="sxs-lookup"><span data-stu-id="d4ea9-125">App Service</span></span> |
| <span data-ttu-id="d4ea9-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="d4ea9-126">MongoDB</span></span> | <span data-ttu-id="d4ea9-127">Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="d4ea9-127">Cosmos DB</span></span> |
| <span data-ttu-id="d4ea9-128">Redis</span><span class="sxs-lookup"><span data-stu-id="d4ea9-128">Redis</span></span> | <span data-ttu-id="d4ea9-129">Azure Redis 快取</span><span class="sxs-lookup"><span data-stu-id="d4ea9-129">Azure Redis Cache</span></span> |
| <span data-ttu-id="d4ea9-130">SQL Server</span><span class="sxs-lookup"><span data-stu-id="d4ea9-130">SQL Server</span></span> | <span data-ttu-id="d4ea9-131">連接字串</span><span class="sxs-lookup"><span data-stu-id="d4ea9-131">Azure SQL Database</span></span> |

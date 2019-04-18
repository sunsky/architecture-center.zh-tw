---
title: CAF：什麼是雲端資源治理？
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.date: 02/11/2019
description: 說明 Azure 上的雲端資源治理
author: petertaylor9999
ms.openlocfilehash: 0989a5aad8a6290cce07fd71771c690bbd615e0d
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068849"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-is-cloud-resource-governance"></a><span data-ttu-id="bc2d0-103">什麼是雲端資源治理？</span><span class="sxs-lookup"><span data-stu-id="bc2d0-103">What is cloud resource governance?</span></span>

<span data-ttu-id="bc2d0-104">在  [Azure 如何運作？](what-is-azure.md)、 您已了解 Azure 是伺服器的集合，以及網路代表使用者執行虛擬化的硬體和軟體的硬體。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-104">In [How does Azure work?](what-is-azure.md), you learned that Azure is a collection of servers and networking hardware running virtualized hardware and software on behalf of users.</span></span> <span data-ttu-id="bc2d0-105">Azure 可讓您組織的應用程式開發和 IT 部門，讓您輕鬆地建立、 讀取、 更新和刪除資源，視需要變得敏捷。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-105">Azure enables your organization's application development and IT departments to be agile by making it easy to create, read, update, and delete resources as needed.</span></span>

<span data-ttu-id="bc2d0-106">不過，雖然無限制地的存取資源，可以讓開發人員非常敏捷式軟體開發，它可能也會導致非預期的成本。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-106">However, while unrestricted access to resources can make developers very agile, it can also lead to unexpected costs.</span></span> <span data-ttu-id="bc2d0-107">例如，開發小組可能會核准要部署一組測試資源，但是在完成測試之後忘記刪除。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-107">For example, a development team might be approved to deploy a set of resources for testing but forget to delete them when testing is complete.</span></span> <span data-ttu-id="bc2d0-108">這些資源會繼續累算費用，即使它們不再是已核准或不必要。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-108">These resources will continue to accrue costs even though they are no longer approved or necessary.</span></span>

<span data-ttu-id="bc2d0-109">解決方法是資源的存取管理。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-109">The solution is resource access governance.</span></span> <span data-ttu-id="bc2d0-110">**控管**是進行中的程序的管理、 監視及稽核以符合組織需求的 Azure 資源的使用。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-110">**Governance** is the ongoing process of managing, monitoring, and auditing the use of Azure resources to meet the requirements of your organization.</span></span>

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ii94]

<!-- markdownlint-enable MD034 -->

<span data-ttu-id="bc2d0-111">這些需求是每個組織，唯一的因此萬用的方法，來控管不是很有幫助。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-111">These requirements are unique to each organization, so a one-size-fits-all approach to governance isn't helpful.</span></span> <span data-ttu-id="bc2d0-112">相反地，它是由設計使用 Azure 的兩個主要的控管工具及其控管模型的每個組織：**角色型存取控制 (RBAC)** 並**資源原則**。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-112">Instead, it's up to each organization to design their governance model using Azure's two primary governance tools: **role-based access control (RBAC)** and **resource policy**.</span></span>

<span data-ttu-id="bc2d0-113">RBAC 定義的角色和角色定義指派給該角色每一位使用者的功能。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-113">RBAC defines roles, and roles define the capabilities of each user assigned that role.</span></span> <span data-ttu-id="bc2d0-114">例如，**擁有者**角色可讓所有的功能 （建立、 讀取、 更新和刪除） 對於資源，而**讀取器**角色可讓讀取的功能。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-114">For example, the **owner** role allows all capabilites (create, read, update, and delete) for a resource, while the  **reader** role allows only the read capability.</span></span> <span data-ttu-id="bc2d0-115">可以使用廣泛的範圍會套用到多個資源類型或適用於少數的狹窄範圍中定義角色。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-115">Roles can be defined with a broad scope that applies to many resource types, or a narrow scope that applies to a few.</span></span>

<span data-ttu-id="bc2d0-116">資源原則會定義資源建立的規則。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-116">Resource policies define rules for resource creation.</span></span> <span data-ttu-id="bc2d0-117">例如，資源原則可以限制為特定的預先核准大小的虛擬機器的 SKU。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-117">For example, a resource policy can limit the SKU of a virtual machine to a particular pre-approved size.</span></span> <span data-ttu-id="bc2d0-118">若要建立的資源提出要求時，另一個資源的原則可能會強制使用指派的成本中心標記的應用程式。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-118">Another resource policy could enforce the application of a tag for an assigned cost center when the request is made to create the resource.</span></span>

<span data-ttu-id="bc2d0-119">在設定這些工具時，務必控管與組織的敏捷度之間取得平衡。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-119">When configuring these tools, it is important to balance governance and organizational agility.</span></span> <span data-ttu-id="bc2d0-120">更嚴格您控管的原則，敏捷式較不會是您的開發人員和 IT 工作者。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-120">The more restrictive your governance policy, the less agile your developers and IT workers will be.</span></span> <span data-ttu-id="bc2d0-121">嚴格控管的原則可能需要更多的手動步驟，例如要求開發人員在填寫表單，或將電子郵件傳送至管理小組的成員，才能以手動方式建立資源。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-121">A restrictive governance policy may require more manual steps like requiring a developer to fill out a form or send an email to a member of the governance team to manually create a resource.</span></span> <span data-ttu-id="bc2d0-122">管理小組具有有限的容量，並且可能成為瓶頸，導致 unproductively 等候其資源來建立或不必要的資源產生成本，在刪除前的開發團隊。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-122">The governance team has finite capacity and may become a bottleneck, resulting in development teams waiting unproductively for their resources to be created or unneeded resources accruing costs before they are deleted.</span></span>

## <a name="next-steps"></a><span data-ttu-id="bc2d0-123">後續步驟</span><span class="sxs-lookup"><span data-stu-id="bc2d0-123">Next steps</span></span>

<span data-ttu-id="bc2d0-124">既然您了解雲端資源管理的概念，深入了解如何在 Azure 中管理資源的存取。</span><span class="sxs-lookup"><span data-stu-id="bc2d0-124">Now that you understand the concept of cloud resource governance, learn more about how resource access is managed in Azure.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="bc2d0-125">深入了解 Azure 中的資源存取</span><span class="sxs-lookup"><span data-stu-id="bc2d0-125">Learn about resource access in Azure</span></span>](azure-resource-access.md)

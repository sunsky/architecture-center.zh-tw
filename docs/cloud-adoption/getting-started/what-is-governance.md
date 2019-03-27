---
title: CAF：什麼是雲端資源治理？
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.date: 02/11/2019
description: 說明 Azure 上的雲端資源治理
author: petertaylor9999
ms.openlocfilehash: 5d022e83a1c97a5e5af8208ec00339575bb88be1
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58242119"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-is-cloud-resource-governance"></a><span data-ttu-id="7914b-103">什麼是雲端資源治理？</span><span class="sxs-lookup"><span data-stu-id="7914b-103">What is cloud resource governance?</span></span>

<span data-ttu-id="7914b-104">在 [Azure 如何運作？](what-is-azure.md)中，您已了解 Azure 是代替使用者執行虛擬化硬體和軟體的伺服器和網路硬體的集合。</span><span class="sxs-lookup"><span data-stu-id="7914b-104">In [how does Azure work?](what-is-azure.md), you learned that Azure is a collection of servers and networking hardware running virtualized hardware and software on behalf of users.</span></span> <span data-ttu-id="7914b-105">Azure 可藉由讓建立、讀取、更新和刪除資源變得簡單，使貴組織的開發和 IT 部門更加敏捷。</span><span class="sxs-lookup"><span data-stu-id="7914b-105">Azure enables your organization's development and IT departments to be agile by making it easy to create, read, update, and delete resources as needed.</span></span>

<span data-ttu-id="7914b-106">不過，雖然為開發人員提供不受限制的資源存取可以讓他們非常敏捷，但是也會導致非預期的成本後果。</span><span class="sxs-lookup"><span data-stu-id="7914b-106">However, while giving unrestricted resource access to developers can make them very agile, it can also lead to unintended cost consequences.</span></span> <span data-ttu-id="7914b-107">例如，開發小組可能會核准要部署一組測試資源，但是在完成測試之後忘記刪除。</span><span class="sxs-lookup"><span data-stu-id="7914b-107">For example, a development team might be approved to deploy a set of resources for testing but forget to delete them when testing is complete.</span></span> <span data-ttu-id="7914b-108">這些資源將會繼續累計成本，即使已不再核准或需要使用它們。</span><span class="sxs-lookup"><span data-stu-id="7914b-108">These resources will continue to accrue costs even though their use is no longer approved or necessary.</span></span>

<span data-ttu-id="7914b-109">這個問題的解決方案是資源存取**治理**。</span><span class="sxs-lookup"><span data-stu-id="7914b-109">The solution to this problem is resource access **governance**.</span></span> <span data-ttu-id="7914b-110">治理指的是管理、監視及稽核 Azure 資源的使用符合組織目標和需求的進行中程序。</span><span class="sxs-lookup"><span data-stu-id="7914b-110">Governance refers to the ongoing process of managing, monitoring, and auditing the use of Azure resources to meet the goals and requirements of your organization.</span></span>

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ii94]

<!-- markdownlint-enable MD034 -->

<span data-ttu-id="7914b-111">這些目標和需求對於每個組織都是獨特的，所以不可能有一體適用的治理方法。</span><span class="sxs-lookup"><span data-stu-id="7914b-111">These goals and requirements are unique to each organization so it's not possible to have a one-size-fits-all approach to governance.</span></span> <span data-ttu-id="7914b-112">相反地，Azure 會實作兩個主要的治理工具，**角色型存取控制 (RBAC)** 和**資源原則**，由每個組織設計其治理模型並使用。</span><span class="sxs-lookup"><span data-stu-id="7914b-112">Rather, Azure implements two primary governance tools, **role based access control (RBAC)**, and **resource policy**, and it's up to each organization to design their governance model using them.</span></span>

<span data-ttu-id="7914b-113">RBAC 會定義角色，而角色會定義獲指派角色的使用者功能。</span><span class="sxs-lookup"><span data-stu-id="7914b-113">RBAC defines roles, and roles define the capabilities for a user that is assigned the role.</span></span> <span data-ttu-id="7914b-114">例如，**擁有者**角色可以對資源啟用所有功能 (建立、讀取、更新和刪除)，而**讀者**角色僅可啟用讀取功能。</span><span class="sxs-lookup"><span data-stu-id="7914b-114">For example, the **owner** role enables all capabilites (create, read, update, and delete) for a resource, while the  **reader** roles enables only the read capability.</span></span> <span data-ttu-id="7914b-115">角色可以定義為套用至許多資源類型的廣範圍，或者套用至少數資源的狹窄範圍。</span><span class="sxs-lookup"><span data-stu-id="7914b-115">Roles can be defined with a broad scope that applies to many resources types, or a narrow scope that applies to a few.</span></span>

<span data-ttu-id="7914b-116">資源原則會定義資源建立的規則。</span><span class="sxs-lookup"><span data-stu-id="7914b-116">Resource policies define rules for resource creation.</span></span> <span data-ttu-id="7914b-117">例如，資源原則可以將虛擬機器的 SKU 限制為特定預先核准大小。</span><span class="sxs-lookup"><span data-stu-id="7914b-117">For example, a resource policy can limit the SKU of a VM to a particular pre-appproved size.</span></span> <span data-ttu-id="7914b-118">或者，資源原則可以在要求建立資源時，強制新增成本中心的標記。</span><span class="sxs-lookup"><span data-stu-id="7914b-118">Or, a resource policy can enforce the addition of a tag with a cost center when the request is made to create the resource.</span></span>

<span data-ttu-id="7914b-119">在設定這些工具時，重要的考量是平衡治理與組織靈活度。</span><span class="sxs-lookup"><span data-stu-id="7914b-119">When configuring these tools, an important consideration is balancing governance versus organizational agility.</span></span> <span data-ttu-id="7914b-120">也就是，治理原則越嚴格，您的開發人員和 IT 工作者就會變得越不敏捷。</span><span class="sxs-lookup"><span data-stu-id="7914b-120">That is, the more restrictive your governance policy, the less agile your developers and IT workers become.</span></span> <span data-ttu-id="7914b-121">這是因為嚴格的治理原則可能需要更多手動步驟，例如需要開發人員填寫表單或傳送電子郵件給治理小組的某人，以手動建立資源。</span><span class="sxs-lookup"><span data-stu-id="7914b-121">This is because a restrictive goverance policy may require more manual steps, such as requiring a developer to fill out a form or send an email to a person on the governance team to manually create a resource.</span></span> <span data-ttu-id="7914b-122">治理小組具有有限的功能並且可能會積存，導致開發小組等候其資源建立而無效率，以及在等候不需要的資源刪除時持續累計成本。</span><span class="sxs-lookup"><span data-stu-id="7914b-122">The goverance team has finite capabilities and may become backlogged, resulting in unproductive development teams waiting for their resources to be created and unneeded resources accruing costs while they wait to be deleted.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7914b-123">後續步驟</span><span class="sxs-lookup"><span data-stu-id="7914b-123">Next steps</span></span>

<span data-ttu-id="7914b-124">既然您已了解雲端資源治理的概念，請深入了解如何在 Azure 中管理資源存取。</span><span class="sxs-lookup"><span data-stu-id="7914b-124">Now that you understand the concept of cloud resource goverance, learn more about how resource access is managed in Azure.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="7914b-125">深入了解 Azure 中的資源存取</span><span class="sxs-lookup"><span data-stu-id="7914b-125">Learn about resource access in Azure</span></span>](azure-resource-access.md)

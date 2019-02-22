---
title: CAF：訂用帳戶決策指南
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 了解在 Azure 移轉中作為核心服務的雲端平台訂用帳戶。
author: rotycenh
ms.openlocfilehash: c0781f6af25150d359395b1b80506dd0cfee8e3c
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900804"
---
# <a name="subscription-decision-guide"></a><span data-ttu-id="38337-103">訂用帳戶決策指南</span><span class="sxs-lookup"><span data-stu-id="38337-103">Subscription decision guide</span></span>

<span data-ttu-id="38337-104">所有雲端平台都是以核心擁有權模型為基礎，以便為組織提供許多帳單和資源管理選項。</span><span class="sxs-lookup"><span data-stu-id="38337-104">All cloud platforms are based on a core ownership model that provides organizations with numerous billing and resource management options.</span></span> <span data-ttu-id="38337-105">Azure 所使用的結構不同於其他雲端提供者，因為它包含適用於組織階層和已群組訂用帳戶擁有權的各種支援選項。</span><span class="sxs-lookup"><span data-stu-id="38337-105">The structure that Azure uses is different from other cloud providers because it includes various support options for organizational hierarchy and grouped subscription ownership.</span></span> <span data-ttu-id="38337-106">不論如何，通常會有一個人負責帳單，並指派另一個人作為管理資源的最上層擁有者。</span><span class="sxs-lookup"><span data-stu-id="38337-106">Regardless, there is generally one individual responsible for billing and another who is assigned as the top-level owner for managing resources.</span></span>

![繪製符合下列快速連結的訂用帳戶選項 (從最簡單到最複雜)](../../_images/discovery-guides/discovery-guide-subscriptions.png)

<span data-ttu-id="38337-108">跳至：[訂用帳戶設計和 Azure Enterprise 合約](#subscriptions-design-and-azure-enterprise-agreements) | [訂用帳戶設計模式](#subscription-design-patterns) | [管理群組](#management-groups) | [訂用帳戶層級的組織](#organization-at-the-subscription-level)</span><span class="sxs-lookup"><span data-stu-id="38337-108">Jump to: [Subscriptions design and Azure Enterprise Agreements](#subscriptions-design-and-azure-enterprise-agreements) | [Subscription design patterns](#subscription-design-patterns) | [Management groups](#management-groups) | [Organization at the subscription level](#organization-at-the-subscription-level)</span></span>

<span data-ttu-id="38337-109">訂用帳戶設計是公司在雲端採用期間，用來建立結構或組織資產的最常見策略之一。</span><span class="sxs-lookup"><span data-stu-id="38337-109">Subscription design is one of the most common strategies that companies use to establish a structure or organize assets during cloud adoption.</span></span>

<span data-ttu-id="38337-110">**訂用帳戶階層**：「訂用帳戶」是 Azure 服務 (例如，虛擬機器、SQL DB、應用程式服務或容器) 的邏輯集合。</span><span class="sxs-lookup"><span data-stu-id="38337-110">**Subscription hierarchy**: A *subscription* is a logical collection of Azure services (such as virtual machines, SQL DB, App Services, or containers).</span></span> <span data-ttu-id="38337-111">Azure 中的每個資產都會部署到單一訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="38337-111">Each asset in Azure is deployed to a single subscription.</span></span> <span data-ttu-id="38337-112">每個訂用帳戶則會由一個「帳戶」所擁有。</span><span class="sxs-lookup"><span data-stu-id="38337-112">Each subscription is then owned by one *account*.</span></span> <span data-ttu-id="38337-113">此帳戶是使用者帳戶 (或最好是服務帳戶)，可跨訂用帳戶提供帳單和系統管理存取。</span><span class="sxs-lookup"><span data-stu-id="38337-113">This account is a user account (or preferably a service account) that provides billing and administrative access across a subscription.</span></span> <span data-ttu-id="38337-114">對於已透過 Enterprise 合約 (EA) 承諾使用特定數量之 Azure 的客戶，會新增另一個稱為「部門」的控制層級。</span><span class="sxs-lookup"><span data-stu-id="38337-114">For customers who have made a commitment to use a specific amount of Azure through an Enterprise Agreement (EA), another level of control called a *department* is added.</span></span> <span data-ttu-id="38337-115">在 EA 入口網站中，可以基於帳單和管理用途，使用訂用帳戶、帳戶和部門來建立階層。</span><span class="sxs-lookup"><span data-stu-id="38337-115">In the EA portal, subscription, accounts, and departments can be used to create a hierarchy for billing and management purposes.</span></span>

<span data-ttu-id="38337-116">訂用帳戶設計的複雜度各不相同。</span><span class="sxs-lookup"><span data-stu-id="38337-116">The complexity of subscription designs varies.</span></span> <span data-ttu-id="38337-117">有關設計策略的決策具有獨特的轉折點，因為它們通常涉及商務和 IT 條件約束。</span><span class="sxs-lookup"><span data-stu-id="38337-117">Decisions regarding a design strategy have unique inflection points, as they typically involve both business and IT constraints.</span></span> <span data-ttu-id="38337-118">進行技術決策之前，IT 架構設計師和決策人員應該與商務專案關係人和雲端策略小組合作，以了解所需的雲端帳戶處理方法、您營業單位內的成本帳戶處理做法，以及適用於貴組織的全球市場需求。</span><span class="sxs-lookup"><span data-stu-id="38337-118">Before making technical decisions, IT architects and decision makers should work with the business stakeholders and the cloud strategy team to understand the desired cloud accounting approach, cost accounting practices within your business units, and global market needs for your organization.</span></span>

<span data-ttu-id="38337-119">**轉折點**：上圖的虛線會基於訂用帳戶設計，參考簡單模式和更複雜模式之間的轉折點。</span><span class="sxs-lookup"><span data-stu-id="38337-119">**Inflection point**: The dashed line in the image above references an inflection point between simple and more complex patterns for subscription design.</span></span> <span data-ttu-id="38337-120">其他以數位資產大小與 Azure 訂用帳戶限制、隔離 (Isolation) 和隔離 (Segregation) 原則，以及 IT 作業部門為依據的技術決策點，通常會對訂用帳戶設計產生重大影響。</span><span class="sxs-lookup"><span data-stu-id="38337-120">Additional technical decision points based on digital estate size versus Azure subscription limits, isolation and segregation policies, and IT operational divisions usually have a significant effect on subscription design.</span></span>

<span data-ttu-id="38337-121">**其他考量**：選取訂用帳戶設計時應注意的事項是，訂用帳戶不是群組資源或部署的唯一方式。</span><span class="sxs-lookup"><span data-stu-id="38337-121">**Other considerations**: An important thing to note when selecting a subscription design is that subscriptions aren’t the only way to group resources or deployments.</span></span> <span data-ttu-id="38337-122">訂用帳戶都是在 Azure 早期建立的，因此，它們具有與先前 Azure 解決方案 (例如 Azure Service Manager) 相關的限制。</span><span class="sxs-lookup"><span data-stu-id="38337-122">Subscriptions were created in the early days of Azure, as such they have limitations related to previous Azure solutions like Azure Service Manager.</span></span>

<span data-ttu-id="38337-123">部署結構、自動化，以及新增方法來群組資源，可能都會影響您的結構訂用帳戶設計。</span><span class="sxs-lookup"><span data-stu-id="38337-123">Deployment structure, automation, and new approaches to grouping resources can affect your structure subscription design.</span></span> <span data-ttu-id="38337-124">完成訂用帳戶設計之前，請考慮[資源一致性](../resource-consistency/overview.md)決策可能會對您的設計選擇產生何種影響。</span><span class="sxs-lookup"><span data-stu-id="38337-124">Before finalizing a subscription design, consider how [resource consistency](../resource-consistency/overview.md) decisions might influence your design choices.</span></span> <span data-ttu-id="38337-125">例如，大型跨國組織一開始可能考慮針對訂用帳戶管理使用複雜模式。</span><span class="sxs-lookup"><span data-stu-id="38337-125">For example, a large multinational organization might initially consider a complex pattern for subscription management.</span></span> <span data-ttu-id="38337-126">不過，藉由新增管理群組階層，該家公司或許能夠透過較簡單的營業單位模式來實現更大的利益。</span><span class="sxs-lookup"><span data-stu-id="38337-126">However, that same company might realize greater benefits with a simpler business unit pattern by adding a management group hierarchy.</span></span>

## <a name="subscriptions-design-and-azure-enterprise-agreements"></a><span data-ttu-id="38337-127">訂用帳戶設計和 Azure Enterprise 合約</span><span class="sxs-lookup"><span data-stu-id="38337-127">Subscriptions design and Azure Enterprise agreements</span></span>

<span data-ttu-id="38337-128">所有 Azure 訂用帳戶都會與一個帳戶相關聯，該帳戶會連線到每個訂用帳戶的帳單和最上層存取控制。</span><span class="sxs-lookup"><span data-stu-id="38337-128">All Azure subscriptions are associated with one account, which is connected to billing and top-level access control for each subscription.</span></span> <span data-ttu-id="38337-129">單一帳戶可以擁有多個訂用帳戶，而且可提供基本層級的訂用帳戶組織。</span><span class="sxs-lookup"><span data-stu-id="38337-129">A single account can own multiple subscriptions and can provide a base level of subscriptions organization.</span></span>

<span data-ttu-id="38337-130">對於小型 Azure 部署，單一訂用帳戶或小型訂用帳戶集合可能就會構成您的整個雲端資產。</span><span class="sxs-lookup"><span data-stu-id="38337-130">For small Azure deployments, a single subscription or a small collection of subscriptions may compose your entire cloud estate.</span></span> <span data-ttu-id="38337-131">不過，大型 Azure 部署可能需要跨越多個訂用帳戶來支援您的組織結構，並略過[訂用帳戶配額和限制](/azure/azure-subscription-service-limits)。</span><span class="sxs-lookup"><span data-stu-id="38337-131">However, large Azure deployments likely need to span multiple subscriptions to support your organizational structure and bypass [subscription quotas and limits](/azure/azure-subscription-service-limits).</span></span>

<span data-ttu-id="38337-132">每個 Azure Enterprise 合約都提供進一步的能力，來將訂用帳戶和帳戶組織為可反映組織優先順序的階層。</span><span class="sxs-lookup"><span data-stu-id="38337-132">Each Azure Enterprise Agreement provides a further ability to organize subscriptions, and accounts into hierarchies that reflect your organizational priorities.</span></span> <span data-ttu-id="38337-133">您的組織 Enterprise 註冊會以合約的角度，來定義公司內部的 Azure 服務形式和用途。</span><span class="sxs-lookup"><span data-stu-id="38337-133">Your organizational enterprise enrollment defines the shape and use of Azure services within your company from a contractual point of view.</span></span> <span data-ttu-id="38337-134">在每個 Enterprise 合約中，您可以進一步將環境細分成部門、帳戶和訂用帳戶，以符合組織的結構。</span><span class="sxs-lookup"><span data-stu-id="38337-134">Within each enterprise agreement, you can further subdivide the environment into departments, accounts, and subscriptions to match your organization's structure.</span></span>

![階層](../../_images/infra-subscriptions/agreement.png)

## <a name="subscription-design-patterns"></a><span data-ttu-id="38337-136">訂用帳戶設計模式</span><span class="sxs-lookup"><span data-stu-id="38337-136">Subscription design patterns</span></span>

<span data-ttu-id="38337-137">每個企業都不同。</span><span class="sxs-lookup"><span data-stu-id="38337-137">Every enterprise is different.</span></span> <span data-ttu-id="38337-138">因此，已在整個 Azure Enterprise 合約中啟用的部門/帳戶/訂用帳戶階層，能夠在組織 Azure 的方式上提供極大的彈性。</span><span class="sxs-lookup"><span data-stu-id="38337-138">Therefore, the department/account/subscription hierarchy enabled throughout an Azure Enterprise Agreement allows for significant flexibility in how Azure is organized.</span></span> <span data-ttu-id="38337-139">將貴組織的階層模型化以反映公司的帳單、資源管理和資源存取需求，是當您開始在公用雲端中作業時所做的最優先且最重要的決策。</span><span class="sxs-lookup"><span data-stu-id="38337-139">Modeling your organization's hierarchy to reflect the needs of your company for billing, resource management, and resource access is the first, and most important, decision that you make when starting in the public cloud.</span></span>

<span data-ttu-id="38337-140">下列訂用帳戶模式反映出普遍提高了訂用帳戶設計的複雜度，以支援潛在的組織優先順序：</span><span class="sxs-lookup"><span data-stu-id="38337-140">The following subscription patterns reflect a general increase in subscription design complexity to support potential organizational priorities:</span></span>

### <a name="single-subscription"></a><span data-ttu-id="38337-141">單一訂用帳戶</span><span class="sxs-lookup"><span data-stu-id="38337-141">Single subscription</span></span>

<span data-ttu-id="38337-142">如果組織需要部署少數裝載於雲端的資產，則針對每個帳戶使用單一訂用帳戶就已足夠。</span><span class="sxs-lookup"><span data-stu-id="38337-142">A single subscription per account may suffice for organizations that need to deploy a small number of cloud-hosted assets.</span></span> <span data-ttu-id="38337-143">這通常是您在開始雲端採用程序時所實作的第一個訂用帳戶模式，讓小規模的實驗性或概念證明部署能夠探索雲端平台的功能。</span><span class="sxs-lookup"><span data-stu-id="38337-143">This is often the first subscription pattern you implement when beginning your cloud adoption process, allowing small-scale experimental or proof of concept deployments to explore the capabilities of a cloud platform.</span></span>

<span data-ttu-id="38337-144">不過，對於單一訂用帳戶將支援的資源數目可能會有技術限制。</span><span class="sxs-lookup"><span data-stu-id="38337-144">However, there can be technical limitations to the number of resources that a single subscription will support.</span></span> <span data-ttu-id="38337-145">隨著您的雲端資產大小增長，您可能還想要支援組織資源，以單一訂用帳戶不支援的方式，更妥善地組織原則和存取控制。</span><span class="sxs-lookup"><span data-stu-id="38337-145">As the size of your cloud estate grows, you may likely want to also support organizing your resources to better organize policies and access control in a manner not supported with a single subscription.</span></span>

### <a name="application-category-pattern"></a><span data-ttu-id="38337-146">應用程式分類模式</span><span class="sxs-lookup"><span data-stu-id="38337-146">Application category pattern</span></span>

<span data-ttu-id="38337-147">隨著組織雲端磁碟使用量大小的增長，使用多個訂用帳戶的可能性就會提高。</span><span class="sxs-lookup"><span data-stu-id="38337-147">As the size of an organization's cloud footprint grows, the use of multiple subscriptions becomes increasingly likely.</span></span> <span data-ttu-id="38337-148">在此案例中，通常會建立訂用帳戶，以支援在商務關鍵性、合規性需求、存取控制或資料保護需求中具有基本差異的應用程式。</span><span class="sxs-lookup"><span data-stu-id="38337-148">In this scenario, subscriptions are generally created to support applications that have fundamental differences in business criticality, compliance requirements, access controls, or data protection needs.</span></span> <span data-ttu-id="38337-149">支援這些應用程式分類的訂用帳戶和帳戶，全都組織於由中央 IT 作業人員所擁有且管理的單一部門之下。</span><span class="sxs-lookup"><span data-stu-id="38337-149">The subscriptions and accounts supporting these application categories are all organized under a single department which is owned and administered by central IT operations staff.</span></span>

<span data-ttu-id="38337-150">每個組織選擇來將應用程式分類的方式各有不同，通常會根據特定的應用程式或服務，或是應用程式原型之類的訂用帳戶來分隔訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="38337-150">Each organization will choose to categorize applications differently, often separating subscriptions based on specific applications or services or along the lines of application archetypes.</span></span> <span data-ttu-id="38337-151">可能會證明此模式下之個別訂用帳戶的工作負載包括：</span><span class="sxs-lookup"><span data-stu-id="38337-151">Workloads that might justify a separate subscription under this pattern include:</span></span>

- <span data-ttu-id="38337-152">實驗性或低風險應用程式</span><span class="sxs-lookup"><span data-stu-id="38337-152">Experimental or low-risk applications</span></span>
- <span data-ttu-id="38337-153">含有受保護資料的應用程式</span><span class="sxs-lookup"><span data-stu-id="38337-153">Applications with protected data</span></span>
- <span data-ttu-id="38337-154">任務關鍵性工作負載</span><span class="sxs-lookup"><span data-stu-id="38337-154">Mission-critical workloads</span></span>
- <span data-ttu-id="38337-155">受限於法規需求 (例如 HIPAA 或 FedRAMP) 的應用程式</span><span class="sxs-lookup"><span data-stu-id="38337-155">Applications subject to regulatory requirements (such as HIPAA or FedRAMP)</span></span>
- <span data-ttu-id="38337-156">批次工作負載</span><span class="sxs-lookup"><span data-stu-id="38337-156">Batch workloads</span></span>
- <span data-ttu-id="38337-157">巨量資料工作負載，例如 Hadoop</span><span class="sxs-lookup"><span data-stu-id="38337-157">Big data workloads such as Hadoop</span></span>
- <span data-ttu-id="38337-158">已使用 Kubernetes 之類的部署協調器進行容器化的工作負載</span><span class="sxs-lookup"><span data-stu-id="38337-158">Containerized workloads using deployment orchestrators such as Kubernetes</span></span>
- <span data-ttu-id="38337-159">分析工作負載</span><span class="sxs-lookup"><span data-stu-id="38337-159">Analytics workloads</span></span>

<span data-ttu-id="38337-160">此模式支援多個負責特定工作負載的帳戶擁有者。</span><span class="sxs-lookup"><span data-stu-id="38337-160">This pattern supports multiple accounts owners responsible for specific workloads.</span></span> <span data-ttu-id="38337-161">由於它在 Enterprise 合約階層的部門層級缺乏更複雜的結構，因此，此模式不需要實作 Azure Enterprise 合約。</span><span class="sxs-lookup"><span data-stu-id="38337-161">As it lacks a more complex structure at the department level of the enterprise agreement hierarchy, this pattern does not require an Azure Enterprise Agreement to implement.</span></span>

![應用程式分類模式](../../_images/infra-subscriptions/application.png)

### <a name="functional-pattern"></a><span data-ttu-id="38337-163">功能模式</span><span class="sxs-lookup"><span data-stu-id="38337-163">Functional pattern</span></span>

<span data-ttu-id="38337-164">此模式會使用提供給 Azure Enterprise 合約客戶的企業/部門/帳戶/訂用帳戶階層，依功能 (例如，財務、業務或 IT 支援) 來組織訂用帳戶和帳戶。</span><span class="sxs-lookup"><span data-stu-id="38337-164">This pattern organizes subscriptions and accounts along functional lines, such as finance, sales, or IT support, using the Enterprise/Department/Account/subscription hierarchy provided to Azure enterprise agreement customers.</span></span>

![功能模式](../../_images/infra-subscriptions/functional.png)

### <a name="business-unit-pattern"></a><span data-ttu-id="38337-166">營業單位模式</span><span class="sxs-lookup"><span data-stu-id="38337-166">Business unit pattern</span></span>

<span data-ttu-id="38337-167">此模式會使用 Azure Enterprise 合約階層，根據損益分類、營業單位、部門、利潤中心或類似的商務結構，來群組應用程式和帳戶。</span><span class="sxs-lookup"><span data-stu-id="38337-167">This pattern groups subscriptions and accounts based on profit and loss category, business unit, division, profit center, or similar business structure using the Azure Enterprise Agreement hierarchy.</span></span>

![營業單位模式](../../_images/infra-subscriptions/business.png)

### <a name="geographic-pattern"></a><span data-ttu-id="38337-169">地理模式</span><span class="sxs-lookup"><span data-stu-id="38337-169">Geographic pattern</span></span>

<span data-ttu-id="38337-170">對於全球營運的組織，此模式會使用 Azure Enterprise 合約階層，根據地理區域來群組訂用帳戶和帳戶。</span><span class="sxs-lookup"><span data-stu-id="38337-170">For organizations with global operations, this pattern groups subscriptions and accounts based on geographic regions using the Azure Enterprise Agreement hierarchy.</span></span>

![地理模式](../../_images/infra-subscriptions/geographic.png)

### <a name="mixed-patterns"></a><span data-ttu-id="38337-172">混合模式</span><span class="sxs-lookup"><span data-stu-id="38337-172">Mixed patterns</span></span>

<span data-ttu-id="38337-173">企業/部門/帳戶/訂用帳戶階層。</span><span class="sxs-lookup"><span data-stu-id="38337-173">enterprise/department/account/subscriptions hierarchy.</span></span> <span data-ttu-id="38337-174">不過，您可以將地理區域和營業單位之類的模式相結合，以反映貴公司內更複雜的帳單與組織結構。</span><span class="sxs-lookup"><span data-stu-id="38337-174">However, you can combine patterns such as geographic region and business unit to reflect more complex billing and organizational structures within your company.</span></span> <span data-ttu-id="38337-175">此外，您的[資源一致性設計](../resource-consistency/overview.md)可以進一步擴充訂用帳戶設計的治理與組織結構。</span><span class="sxs-lookup"><span data-stu-id="38337-175">In addition, your [resource consistency design](../resource-consistency/overview.md) can further extend the governance and organizational structure of your subscription design.</span></span>

<span data-ttu-id="38337-176">管理群組 (如下一節所討論) 有助於支援更複雜的組織結構。</span><span class="sxs-lookup"><span data-stu-id="38337-176">Management groups, as discussed in the following section, can help support more complicated organizational structures.</span></span>

<span data-ttu-id="38337-177">下一節所討論的管理群組有助於支援更複雜的組織結構。</span><span class="sxs-lookup"><span data-stu-id="38337-177">Management groups, discussed in the following section, can help support more complicated organizational structures.</span></span>

## <a name="management-groups"></a><span data-ttu-id="38337-178">管理群組</span><span class="sxs-lookup"><span data-stu-id="38337-178">Management groups</span></span>

<span data-ttu-id="38337-179">除了透過 Enterprise 合約提供的部門和組織結構，[Azure 管理群組](/azure/governance/management-groups/index)還會跨多個訂用帳戶，針對組織原則、存取控制及合規性提供額外的彈性。</span><span class="sxs-lookup"><span data-stu-id="38337-179">In addition to the department and organization structure provided through Enterprise Agreements, [Azure management groups](/azure/governance/management-groups/index) offer additional flexibility for organizing policy, access control, and compliance across multiple subscriptions.</span></span> <span data-ttu-id="38337-180">管理群組的巢狀結構最多可有六個層級，讓您能夠建立與帳單階層分開的階層。</span><span class="sxs-lookup"><span data-stu-id="38337-180">Management groups can be nested up to six levels, allowing you to create a hierarchy that is separate from your billing hierarchy.</span></span> <span data-ttu-id="38337-181">這樣做只為了讓您能更有效率地管理資源。</span><span class="sxs-lookup"><span data-stu-id="38337-181">This can be solely for efficient management of resources.</span></span>

<span data-ttu-id="38337-182">管理群組可對您的帳單階層進行鏡像處理，而且企業通常會以該方式作為起點。</span><span class="sxs-lookup"><span data-stu-id="38337-182">Management groups can mirror your billing hierarchy, and often enterprises start that way.</span></span> <span data-ttu-id="38337-183">不過，管理群組的強大功能是，當相關的訂用帳戶 (無論位於帳單階層的何處&mdash;&mdash;) 群組在一起，而且需要獲派常見角色、原則及計劃時，即可使用這些管理群組來為組織建立模型。</span><span class="sxs-lookup"><span data-stu-id="38337-183">However, the power of management groups is when you use them to model your organization where related subscriptions &mdash; regardless of where they are in the billing hierarchy &mdash; are grouped together and need common roles assigned along with policies and initiatives.</span></span>

<span data-ttu-id="38337-184">範例包括：</span><span class="sxs-lookup"><span data-stu-id="38337-184">Examples include:</span></span>

- <span data-ttu-id="38337-185">生產/非生產：有些企業會建立管理群組來識別其生產和非生產訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="38337-185">Production/non-production: Some enterprises create management groups to identify their production and non-production subscriptions.</span></span> <span data-ttu-id="38337-186">管理群組可讓這些客戶更輕鬆地管理角色和原則，例如：非生產訂用帳戶可能會允許開發人員具有「參與者」存取權，但在生產訂用帳戶中，他們只具有「讀者」存取權。</span><span class="sxs-lookup"><span data-stu-id="38337-186">Management groups allow these customers to more easily manage roles and policies, for example: non-production subscription may allow developers "contributor" access, but in production, they have only "reader" access.</span></span>
- <span data-ttu-id="38337-187">內部服務/外部服務：類似於生產/非生產，企業對內部服務和提供給客戶的外部服務通常會有不同的需求、原則和角色。</span><span class="sxs-lookup"><span data-stu-id="38337-187">Internal services/external services: Much like production/non-production, enterprises often have different requirements, policies, and roles for internal services versus external customer-facing services.</span></span>

## <a name="organization-at-the-subscription-level"></a><span data-ttu-id="38337-188">訂用帳戶層級的組織</span><span class="sxs-lookup"><span data-stu-id="38337-188">Organization at the subscription level</span></span>

<span data-ttu-id="38337-189">在判斷您的部門和帳戶 (或管理群組) 時，您將需優先決定如何分配 Azure 環境以符合您的組織。</span><span class="sxs-lookup"><span data-stu-id="38337-189">When determining your departments and accounts (or management groups), you will primarily need to decide how you're going to divide your Azure environment to match your organization.</span></span> <span data-ttu-id="38337-190">但是，訂用帳戶才是真正執行作業的地方，而這些決策都將影響安全性、延展性和帳單。</span><span class="sxs-lookup"><span data-stu-id="38337-190">However, subscriptions are where the real work happens, and these decisions will impact security, scalability, and billing.</span></span>

<span data-ttu-id="38337-191">請考慮下列模式作為指南：</span><span class="sxs-lookup"><span data-stu-id="38337-191">Consider the following patterns as guides:</span></span>

- <span data-ttu-id="38337-192">**應用程式/服務**：訂用帳戶代表應用程式或服務 (應用程式的組合)。</span><span class="sxs-lookup"><span data-stu-id="38337-192">**Application/service**: Subscriptions represent an application or a service (portfolio of applications).</span></span>

- <span data-ttu-id="38337-193">**生命週期**：訂用帳戶代表服務生命週期，例如生產或開發。</span><span class="sxs-lookup"><span data-stu-id="38337-193">**Lifecycle**: Subscriptions represent a lifecycle of a service, such as production or development.</span></span>

- <span data-ttu-id="38337-194">**部門**：訂用帳戶代表組織中的部門。</span><span class="sxs-lookup"><span data-stu-id="38337-194">**Department**: Subscriptions represent departments in the organization.</span></span>

<span data-ttu-id="38337-195">前兩個模式最常使用，而且強烈建議使用這兩者。</span><span class="sxs-lookup"><span data-stu-id="38337-195">The first two patterns are the most commonly used and are both highly recommended.</span></span> <span data-ttu-id="38337-196">生命週期方法適用於大部分的組織。</span><span class="sxs-lookup"><span data-stu-id="38337-196">The lifecycle approach is appropriate for most organizations.</span></span> <span data-ttu-id="38337-197">在此情況下，一般建議是使用兩個基本訂用帳戶：生產和非生產，然後使用資源群組進一步細分環境。</span><span class="sxs-lookup"><span data-stu-id="38337-197">In this case, the general recommendation is to use two base subscriptions: production and non-production, and then use resource groups to break out the environments further.</span></span>

<span data-ttu-id="38337-198">如需如何使用 Azure 訂用帳戶和資源群組來群組和管理資源的一般描述，請參閱 [Azure 中的資源存取管理](../../getting-started/azure-resource-access.md)。</span><span class="sxs-lookup"><span data-stu-id="38337-198">For a general description of how Azure subscriptions and resource groups are used to group and manage resources, see [Resource access management in Azure](../../getting-started/azure-resource-access.md).</span></span>

## <a name="next-steps"></a><span data-ttu-id="38337-199">後續步驟</span><span class="sxs-lookup"><span data-stu-id="38337-199">Next steps</span></span>

<span data-ttu-id="38337-200">了解如何使用識別服務，在雲端中進行存取控制和管理。</span><span class="sxs-lookup"><span data-stu-id="38337-200">Learn how identity services are used for access control and management in the cloud.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="38337-201">身分識別</span><span class="sxs-lookup"><span data-stu-id="38337-201">Identity</span></span>](../identity/overview.md)

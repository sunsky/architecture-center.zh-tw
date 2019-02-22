---
title: CAF：資源一致性決策指南
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 了解規劃 Azure 移轉時的資源一致性。
author: rotycenh
ms.openlocfilehash: 8170bfd09218a451e086a57e0631b7e567eb2b82
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900864"
---
# <a name="caf-resource-consistency-decision-guide"></a><span data-ttu-id="46d29-103">CAF：資源一致性決策指南</span><span class="sxs-lookup"><span data-stu-id="46d29-103">CAF: Resource consistency decision guide</span></span>

<span data-ttu-id="46d29-104">Azure[訂用帳戶設計](../subscriptions/overview.md)可定義雲端資產的組織方式，這些雲端資產則與您組織的整體結構息息相關。</span><span class="sxs-lookup"><span data-stu-id="46d29-104">Azure [subscription design](../subscriptions/overview.md) defines how you organize your cloud assets in relation to your organization's overall structure.</span></span> <span data-ttu-id="46d29-105">此外，您現有的 IT 管理標準和組織性原則的整合，則取決於部署及組織訂用帳戶內雲端資源的方式。</span><span class="sxs-lookup"><span data-stu-id="46d29-105">In addition, integrating your existing IT management standards and your organizational policies depends on how you deploy and organize cloud resources within a subscription.</span></span>

<span data-ttu-id="46d29-106">可用來實作資源部署、分組及管理設計的工具會視雲端平台而異。</span><span class="sxs-lookup"><span data-stu-id="46d29-106">The tools available to implement your resource deployment, grouping, and management designs vary by cloud platform.</span></span> <span data-ttu-id="46d29-107">通常每個解決方案都會包含下列功能：</span><span class="sxs-lookup"><span data-stu-id="46d29-107">In general, each solution includes the following features:</span></span>

- <span data-ttu-id="46d29-108">訂用帳戶或帳戶層級以下的邏輯群組機制。</span><span class="sxs-lookup"><span data-stu-id="46d29-108">A logical grouping mechanism below the subscription or account level.</span></span>
- <span data-ttu-id="46d29-109">以程式設計方式使用 API 部署資源的功能。</span><span class="sxs-lookup"><span data-stu-id="46d29-109">The ability to deploy resources programmatically with APIs.</span></span>
- <span data-ttu-id="46d29-110">建立標準化部署的範本。</span><span class="sxs-lookup"><span data-stu-id="46d29-110">Templates for creating standardized deployments.</span></span>
- <span data-ttu-id="46d29-111">在訂用帳戶、帳戶和資源群組層級部署原則規則的功能。</span><span class="sxs-lookup"><span data-stu-id="46d29-111">The ability to deploy policy rules at the subscription, account, and resource grouping levels.</span></span>

![規劃符合下列快速連結的資源一致性選項 (從最簡單到最複雜)](../../_images/discovery-guides/discovery-guide-resource-consistency.png)

<span data-ttu-id="46d29-113">跳至：[基本群組](#basic-grouping) | [部署一致性](#deployment-consistency) | [原則一致性](#policy-consistency) | [階層式一致性](#hierarchical-consistency)  | [自動化的一致性](#automated-consistency)</span><span class="sxs-lookup"><span data-stu-id="46d29-113">Jump to: [Basic grouping](#basic-grouping) | [Deployment consistency](#deployment-consistency) | [Policy consistency](#policy-consistency) | [Hierarchical consistency](#hierarchical-consistency) | [Automated consistency](#automated-consistency)</span></span>

<span data-ttu-id="46d29-114">資源部署和群組決策主要取決於下列因素：移轉後的數位資產大小、不符合您現有訂用帳戶設計方式的業務或環境複雜度，或需要在部署資源之後持續強制執行治理。</span><span class="sxs-lookup"><span data-stu-id="46d29-114">Resource deployment and grouping decisions are primarily driven by these factors: post-migration digital estate size, business or environmental complexity that doesn't fit neatly within your existing subscription design approaches, or the need to enforce governance over time after resources have been deployed.</span></span> <span data-ttu-id="46d29-115">更進階的資源群組設計需要執行更多工作，以確保能夠準確地分組，因此這會導致在變更管理和追蹤時需要更多時間。</span><span class="sxs-lookup"><span data-stu-id="46d29-115">More advanced resource grouping designs require an increased effort to ensure accurate grouping, and this results in an increase in the time spent on change management and tracking.</span></span>

## <a name="basic-grouping"></a><span data-ttu-id="46d29-116">基本分組</span><span class="sxs-lookup"><span data-stu-id="46d29-116">Basic grouping</span></span>

<span data-ttu-id="46d29-117">在 Azure 中，[資源群組](/azure/azure-resource-manager/resource-group-overview#resource-groups)是以邏輯方式將訂用帳戶內的資源分組的核心資源組織機制。</span><span class="sxs-lookup"><span data-stu-id="46d29-117">In Azure, [resource groups](/azure/azure-resource-manager/resource-group-overview#resource-groups) are a core resource organization mechanism to logically group resources within a subscription.</span></span>

<span data-ttu-id="46d29-118">資源群組會做為具有一般生命週期或共用管理條件約束 (例如原則或以角色為基礎的存取控制 (RBAC) 要求) 的資源容器。</span><span class="sxs-lookup"><span data-stu-id="46d29-118">Resource groups act as containers for resources with a common lifecycle or shared management constraints such as policy or role-based access control (RBAC) requirements.</span></span> <span data-ttu-id="46d29-119">資源群組不能建立巢狀結構，且資源只能屬於一個資源群組。</span><span class="sxs-lookup"><span data-stu-id="46d29-119">Resource groups can't be nested, and resources can only belong to one resource group.</span></span> <span data-ttu-id="46d29-120">某些動作可套用於資源群組中的所有資源。</span><span class="sxs-lookup"><span data-stu-id="46d29-120">Some actions can act on all resources in a resource group.</span></span> <span data-ttu-id="46d29-121">例如，刪除資源群組即可移除該群組內的所有資源。</span><span class="sxs-lookup"><span data-stu-id="46d29-121">For example, deleting a resource group removes all resources within that group.</span></span> <span data-ttu-id="46d29-122">有幾種常見模式可建立資源群組，通常區分為兩種類別：</span><span class="sxs-lookup"><span data-stu-id="46d29-122">There are common patterns when creating resource groups, commonly divided into two categories:</span></span>

- <span data-ttu-id="46d29-123">傳統 IT 工作負載：最常見的分組方式為依照相同生命週期內的項目分組，例如應用程式。</span><span class="sxs-lookup"><span data-stu-id="46d29-123">Traditional IT workloads: Most often grouped by items within the same lifecycle, such as an application.</span></span> <span data-ttu-id="46d29-124">依照應用程式分組，即可進行個別應用程式管理。</span><span class="sxs-lookup"><span data-stu-id="46d29-124">Grouping by application allows for individual application management.</span></span>
- <span data-ttu-id="46d29-125">敏捷式 IT 工作負載：著重於外部客戶面向的雲端應用程式。</span><span class="sxs-lookup"><span data-stu-id="46d29-125">Agile IT workloads: Focus on external customer-facing cloud applications.</span></span> <span data-ttu-id="46d29-126">資源群組通常會反映出部署 (例如 Web 層或應用程式層) 和管理的功能層次。</span><span class="sxs-lookup"><span data-stu-id="46d29-126">These resource groups often reflect the functional layers of deployment (such as web tier or app tier) and management.</span></span>

## <a name="deployment-consistency"></a><span data-ttu-id="46d29-127">部署一致性</span><span class="sxs-lookup"><span data-stu-id="46d29-127">Deployment consistency</span></span>

<span data-ttu-id="46d29-128">大部分雲端平台皆建置在基本的資源群組機制之上，可提供系統以用於使用範本將資源部署至雲端環境。</span><span class="sxs-lookup"><span data-stu-id="46d29-128">Building on top of the base resource grouping mechanism, most cloud platforms provide a system for using templates to deploy your resources to the cloud environment.</span></span> <span data-ttu-id="46d29-129">您可以在部署工作負載時使用範本建立一致的組織和命名慣例，強制執行您資源部署和管理設計的各個層面。</span><span class="sxs-lookup"><span data-stu-id="46d29-129">You can use templates to create consistent organization and naming conventions when deploying workloads, enforcing those aspects of your resource deployment and management design.</span></span>

<span data-ttu-id="46d29-130">[Azure Resource Manager 範本](/azure/azure-resource-manager/resource-group-overview#template-deployment)可讓您使用預先決定的組態和資源群組結構，以一致的狀態重複部署資源。</span><span class="sxs-lookup"><span data-stu-id="46d29-130">[Azure Resource Manager templates](/azure/azure-resource-manager/resource-group-overview#template-deployment) allow you to repeatedly deploy your resources in a consistent state using a predetermined configuration and resource group structure.</span></span> <span data-ttu-id="46d29-131">Resource Manager 範本可協助您定義一組標準作為部署基礎。</span><span class="sxs-lookup"><span data-stu-id="46d29-131">Resource Manager templates help you define a set of standards as a basis for your deployments.</span></span>

<span data-ttu-id="46d29-132">例如，您可以使用標準範本部署，將包含兩個虛擬機器的 Web 伺服器工作負載，部署為結合負載平衡器的 Web 伺服器，以管理伺服器之間的流量。</span><span class="sxs-lookup"><span data-stu-id="46d29-132">For example, you can have a standard template for deploying a web server workload that contains two virtual machines as web servers combined with a load balancer to manage traffic between the servers.</span></span> <span data-ttu-id="46d29-133">然後只要需要新的 Web 伺服器工作負載，就可以重複使用此範本建立結構完全相同的部署，僅需變更涉及的部署名稱和 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="46d29-133">You can then reuse this template to create structurally identical deployments whenever a new web server workload is needed, only changing the deployment name and IP addresses involved.</span></span>

<span data-ttu-id="46d29-134">請注意，您也可以用程式設計方式部署這些範本，並將它們與您的 CI/CD 系統整合。</span><span class="sxs-lookup"><span data-stu-id="46d29-134">Note that you can also programmatically deploy these templates and integrate them with your CI/CD systems.</span></span>

## <a name="policy-consistency"></a><span data-ttu-id="46d29-135">原則一致性</span><span class="sxs-lookup"><span data-stu-id="46d29-135">Policy consistency</span></span>

<span data-ttu-id="46d29-136">為確保在建立資源時套用治理原則，資源群組設計的一部份會涉及使用一般組態部署資源。</span><span class="sxs-lookup"><span data-stu-id="46d29-136">To ensure that governance policies are applied when resources are created, part of resource grouping design involves using a common configuration when deploying resources.</span></span>

<span data-ttu-id="46d29-137">您可以透過結合資源群組和標準化 Resource Manager 範本，針對部署時需要哪些設定，以及要對每個資源群組或資源套用哪些 [Azure 原則](/azure/governance/policy/overview)規則等需求，強制執行適用標準。</span><span class="sxs-lookup"><span data-stu-id="46d29-137">By combining resource groups and standardized Resource Manager templates, you can enforce standards for what settings are required in a deployment and what [Azure Policy](/azure/governance/policy/overview) rules are applied to each resource group or resource.</span></span>

<span data-ttu-id="46d29-138">例如，您可能需要讓訂用帳戶內部署的所有虛擬機器皆連線到由您的中央 IT 小組所管理的一般子網路。</span><span class="sxs-lookup"><span data-stu-id="46d29-138">For example, you may have a requirement that all virtual machines deployed within your subscription connect to a common subnet managed by your central IT team.</span></span> <span data-ttu-id="46d29-139">您可以建立標準範本，以用於部署會針對工作負載建立個別資源群組，並在其中部署必要 VM 的工作負載 VM。</span><span class="sxs-lookup"><span data-stu-id="46d29-139">You can create a standard template for deploying workload VMs which would create a separate resource group for the workload and deploy the required VMs there.</span></span> <span data-ttu-id="46d29-140">此資源群組會有一項原則規則，限制只允許將資源群組內的網路介面加入至共用子網路。</span><span class="sxs-lookup"><span data-stu-id="46d29-140">This resource group would have a policy rule to only allow network interfaces within the resource group to be joined to the shared subnet.</span></span>

<span data-ttu-id="46d29-141">如需有關在雲端部署內強制執行原則決策的更深入討論，請參閱[原則強制執行](../policy-enforcement/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="46d29-141">For a more in-depth discussion of enforcing your policy decisions within a cloud deployment, see [Policy enforcement](../policy-enforcement/overview.md).</span></span>

## <a name="hierarchical-consistency"></a><span data-ttu-id="46d29-142">階層式一致性</span><span class="sxs-lookup"><span data-stu-id="46d29-142">Hierarchical consistency</span></span>

<span data-ttu-id="46d29-143">當雲端資產的大小成長時，您可能需要支援比使用 Azure Enterprise 合約的企業/部門/帳戶/訂用帳戶階層時更複雜治理要求。</span><span class="sxs-lookup"><span data-stu-id="46d29-143">As the size of your cloud estate grows, you may need to support more complicated governance requirements than can be supported using the Azure Enterprise Agreement's Enterprise/Department/Account/Subscription hierarchy.</span></span> <span data-ttu-id="46d29-144">資源群組可讓您透過在資源群組層級套用 Azure 原則規則和存取控制，以在組織內支援額外的階層層級。</span><span class="sxs-lookup"><span data-stu-id="46d29-144">Resource groups allows you to support additional levels of hierarchy within your organization, applying Azure Policy rules and access controls at a resource group level.</span></span>

<span data-ttu-id="46d29-145">[Azure 管理群組](../subscriptions/overview.md#management-groups)可在企業合約結構上重疊一個替代階層，藉此支援更複雜的組織性結構。</span><span class="sxs-lookup"><span data-stu-id="46d29-145">[Azure management groups](../subscriptions/overview.md#management-groups) can support more complicated organizational structures by overlaying an alternative hierarchy on top of your enterprise agreement's structure.</span></span> <span data-ttu-id="46d29-146">這可讓訂用帳戶和它們包含的資源支援妥善安排的存取控制和原則強制執行機制，以符合您的業務組織性需求。</span><span class="sxs-lookup"><span data-stu-id="46d29-146">This allows subscriptions, and the resources they contain, to support access control and policy enforcement mechanisms organized to match your business organizational requirements.</span></span>

## <a name="automated-consistency"></a><span data-ttu-id="46d29-147">自動化的一致性</span><span class="sxs-lookup"><span data-stu-id="46d29-147">Automated consistency</span></span>

<span data-ttu-id="46d29-148">對於大型雲端部署而言，全域治理變得更加重要，而且更複雜。</span><span class="sxs-lookup"><span data-stu-id="46d29-148">For large cloud deployments, global governance becomes both more important and more complex.</span></span> <span data-ttu-id="46d29-149">務必要自動套用和部署資源時，強制執行控管需求，以及針對現有部署更新的需求。</span><span class="sxs-lookup"><span data-stu-id="46d29-149">It is crucial to automatically apply and enforce governance requirements when deploying resources, as well as meet updated requirements for existing deployments.</span></span>

<span data-ttu-id="46d29-150">[Azure 藍圖](/azure/governance/blueprints/overview)可讓組織支援 Azure 中大型雲端資產的全域治理。</span><span class="sxs-lookup"><span data-stu-id="46d29-150">[Azure Blueprints](/azure/governance/blueprints/overview) enable organizations to support global governance of large cloud estates in Azure.</span></span> <span data-ttu-id="46d29-151">藍圖超越了標準 Azure Resource Manager 範本所提供的功能，可建立完整且能夠部署資源及套用原則規則的部署協調流程。</span><span class="sxs-lookup"><span data-stu-id="46d29-151">Blueprints move beyond the capabilities provided by standard Azure Resource Manager templates to create complete deployment orchestrations capable of deploying resources and applying policy rules.</span></span> <span data-ttu-id="46d29-152">藍圖支援下列功能：版本控制、將更新套用至使用藍圖的所有訂用帳戶，以及鎖定已部署的訂用帳戶，藉此防止未經授權建立及修改資源。</span><span class="sxs-lookup"><span data-stu-id="46d29-152">Blueprints supports versioning, the ability to make apply updates to all subscriptions where the blueprint was used, and the ability to lock down deployed subscriptions to avoid the unauthorized creation and modification of resources.</span></span>

<span data-ttu-id="46d29-153">這些部署套件可讓 IT 和開發團隊，快速部署符合變更組織性原則需求的新工作負載和網路資產。</span><span class="sxs-lookup"><span data-stu-id="46d29-153">These deployment packages allow IT and development teams to rapidly deploy new workloads and networking assets that comply with changing organizational policy requirements.</span></span> <span data-ttu-id="46d29-154">藍圖也可以整合至 CI/CD 管線中，以在更新部署時套用修改過的治理標準。</span><span class="sxs-lookup"><span data-stu-id="46d29-154">Blueprints can also be integrated into CI/CD pipelines to apply revised governance standards to deployments as they are updated.</span></span>

## <a name="next-steps"></a><span data-ttu-id="46d29-155">後續步驟</span><span class="sxs-lookup"><span data-stu-id="46d29-155">Next steps</span></span>

<span data-ttu-id="46d29-156">了解如何使用資源命名與標記進一步組織及管理雲端資源。</span><span class="sxs-lookup"><span data-stu-id="46d29-156">Learn how resource naming and tagging are used to further organize and manage your cloud resources.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="46d29-157">資源命名與標記</span><span class="sxs-lookup"><span data-stu-id="46d29-157">Resource naming and tagging</span></span>](../resource-tagging/overview.md)

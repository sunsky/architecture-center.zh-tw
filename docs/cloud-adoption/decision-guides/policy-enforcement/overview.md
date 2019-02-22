---
title: CAF：原則強制執行決策指南
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 深入了解原則強制執行訂用帳戶是 Azure 移轉中的核心設計優先順序。
author: rotycenh
ms.openlocfilehash: 372926453ee4ae0502250e9b69fe8a0ea94f0ffe
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900744"
---
# <a name="policy-enforcement-decision-guide"></a><span data-ttu-id="9677a-103">原則強制執行決策指南</span><span class="sxs-lookup"><span data-stu-id="9677a-103">Policy enforcement decision guide</span></span>

<span data-ttu-id="9677a-104">定義組織原則沒有效率，除非有方法可以在貴組織強制執行。</span><span class="sxs-lookup"><span data-stu-id="9677a-104">Defining organizational policy is not effective unless there is a way to enforce it across your organization.</span></span> <span data-ttu-id="9677a-105">規劃任何雲端移轉的關鍵層面，是判斷如何最佳地合併雲端平台提供的工具與您的現有 IT 流程，讓您的整個雲端資產原則合規性發揮到極致。</span><span class="sxs-lookup"><span data-stu-id="9677a-105">A key aspect to planning any cloud migration is determining how best to combine tools provided by the cloud platform with your existing IT processes to maximize policy compliance across your entire cloud estate.</span></span>

![繪製符合下列快速連結的原則強制執行選項 (從最簡單到最複雜)](../../_images/discovery-guides/discovery-guide-policy-enforcement.png)

<span data-ttu-id="9677a-107">跳至：[基準建議做法](#baseline-recommended-practices) | [原則合規性監視](#policy-compliance-monitoring) | [原則強制執行](#policy-enforcement) | [跨組織原則](#cross-organization-policy) | [自動化強制執行](#automated-enforcement)</span><span class="sxs-lookup"><span data-stu-id="9677a-107">Jump to: [Baseline recommended practices](#baseline-recommended-practices) | [Policy compliance monitoring](#policy-compliance-monitoring) | [Policy enforcement](#policy-enforcement) | [Cross-organization policy](#cross-organization-policy) | [Automated enforcement](#automated-enforcement)</span></span>

<span data-ttu-id="9677a-108">隨著您的雲端資產成長，您會面臨到跨更大的資源、訂用帳戶和租用戶陣列維護及強制執行原則的對應需求。</span><span class="sxs-lookup"><span data-stu-id="9677a-108">As your cloud estate grows, you will be faced with a corresponding need to maintain and enforce policy across a larger array of resources, subscriptions, and tenants.</span></span> <span data-ttu-id="9677a-109">您的資產越大，用來確保一致遵循和快速違規偵測的強制執行機制就越複雜。</span><span class="sxs-lookup"><span data-stu-id="9677a-109">The larger your estate, the more complex your enforcement mechanisms will need to be to ensure consistent adherence and fast violation detection.</span></span> <span data-ttu-id="9677a-110">資源或訂用帳戶層級的平台提供原則強制執行機制對於較小的雲端配置就已足夠，而較大的部署可能需要利用更複雜的機制，牽涉到部署標準、資源群組和組織，並且讓原則強制執行與您的記錄和報告系統整合。</span><span class="sxs-lookup"><span data-stu-id="9677a-110">Platform-provided policy enforcement mechanisms at the resource or subscription level are usually sufficient for smaller cloud deployments, while larger deployments may need to take advantage of more sophisticated mechanisms involving deployment standards, resource grouping and organization, and integrating policy enforcement with your logging and reporting systems.</span></span>

<span data-ttu-id="9677a-111">選擇原則強制執行策略複雜度時的轉捩點，主要著重在您的[訂用帳戶設計](../subscriptions/overview.md)所需的訂用帳戶或租用戶數量。</span><span class="sxs-lookup"><span data-stu-id="9677a-111">The key inflection point when choosing the complexity of your policy enforcement strategy is primarily focused on the number of subscriptions or tenants required by your [subscription design](../subscriptions/overview.md).</span></span> <span data-ttu-id="9677a-112">授與您雲端資產內各個使用者角色的控制數量，可能也會影響這些決策。</span><span class="sxs-lookup"><span data-stu-id="9677a-112">The amount of control granted to various user roles within your cloud estate might influence these decisions as well.</span></span>

## <a name="baseline-recommended-practices"></a><span data-ttu-id="9677a-113">基準建議做法</span><span class="sxs-lookup"><span data-stu-id="9677a-113">Baseline recommended practices</span></span>

<span data-ttu-id="9677a-114">針對單一訂用帳戶和簡單雲端部署，可以使用大部分雲端平台的原生功能來強制執行許多公司的原則。</span><span class="sxs-lookup"><span data-stu-id="9677a-114">For single subscription and simple cloud deployments, many corporate policies can be enforced using features that are native to most cloud platforms.</span></span> <span data-ttu-id="9677a-115">即使這個做法的部署複雜度相對較低，但是一致使用 CAF [決策指南](../overview.md)中所討論的模式，可協助建立基準層級的原則合規性。</span><span class="sxs-lookup"><span data-stu-id="9677a-115">Even at this relatively low level of deployment complexity, the consistent use of the patterns discussed throughout the CAF [decision guides](../overview.md) can help establish a baseline level of policy compliance.</span></span>

<span data-ttu-id="9677a-116">例如︰</span><span class="sxs-lookup"><span data-stu-id="9677a-116">For example:</span></span>

- <span data-ttu-id="9677a-117">[部署範本](../resource-consistency/overview.md)可以佈建具有標準化結構和設定的資源。</span><span class="sxs-lookup"><span data-stu-id="9677a-117">[Deployment templates](../resource-consistency/overview.md) can provision resources with standardized structure and configuration.</span></span>
- <span data-ttu-id="9677a-118">[標記和命名標準](../resource-tagging/overview.md)有助於組織作業，並支援會計和業務需求。</span><span class="sxs-lookup"><span data-stu-id="9677a-118">[Tagging and naming standards](../resource-tagging/overview.md) can help organize operations and support accounting and business requirements.</span></span>
- <span data-ttu-id="9677a-119">流量管理和網路限制可以透過[軟體定義網路](../software-defined-network/overview.md)實作。</span><span class="sxs-lookup"><span data-stu-id="9677a-119">Traffic management and networking restrictions can be implemented through [software defined networking](../software-defined-network/overview.md).</span></span>
- <span data-ttu-id="9677a-120">[角色型存取控制](../identity/overview.md)可以保護和隔離您的雲端資源。</span><span class="sxs-lookup"><span data-stu-id="9677a-120">[Role-based access control](../identity/overview.md) can secure and isolate your cloud resources.</span></span>

<span data-ttu-id="9677a-121">透過檢查這些指南通篇討論的標準模式應用程式如何能夠協助符合您的組織需求，開始您的雲端原則強制執行規劃。</span><span class="sxs-lookup"><span data-stu-id="9677a-121">Start your cloud policy enforcement planning by examining how the application of the standard patterns discussed throughout these guides can help meet your organizational requirements.</span></span>

## <a name="policy-compliance-monitoring"></a><span data-ttu-id="9677a-122">原則合規性監視</span><span class="sxs-lookup"><span data-stu-id="9677a-122">Policy compliance monitoring</span></span>

<span data-ttu-id="9677a-123">另一個關鍵因素 (即使是相對較小的雲端部署) 是能夠驗證雲端型應用程式和服務遵守組織原則，如果資源變得不相容，立即通知負責的合作對象。</span><span class="sxs-lookup"><span data-stu-id="9677a-123">Another key factor, even for relatively small cloud deployments, is the ability to verify that cloud-based applications and services comply with organizational policy, promptly notifying the responsible parties if a resource becomes noncompliant.</span></span> <span data-ttu-id="9677a-124">有效地[記錄和報告](../log-and-report/overview.md)您雲端工作負載的合規性狀態，是公司原則強制執行策略中的關鍵部分。</span><span class="sxs-lookup"><span data-stu-id="9677a-124">Effectively [logging and reporting](../log-and-report/overview.md) the compliance status of your cloud workloads is a critical part of a corporate policy enforcement strategy.</span></span>

<span data-ttu-id="9677a-125">隨著您的雲端資產成長，額外工具 (例如 [Azure 資訊安全中心](/azure/security-center/)) 可以提供整合的安全性和威脅偵測，協助套用集中式原則管理和警示您的內部部署和雲端資產。</span><span class="sxs-lookup"><span data-stu-id="9677a-125">As your cloud estate grows, additional tools such as [Azure Security Center](/azure/security-center/) can provide integrated security and threat detection, and help apply centralized policy management and alerting for both your on-premises and cloud assets.</span></span>

## <a name="policy-enforcement"></a><span data-ttu-id="9677a-126">強制執行原則</span><span class="sxs-lookup"><span data-stu-id="9677a-126">Policy enforcement</span></span>

<span data-ttu-id="9677a-127">您也可以在訂用帳戶層級套用組態設定和資源建立規則，協助確保原則對齊。</span><span class="sxs-lookup"><span data-stu-id="9677a-127">You can also apply configuration settings and resource creation rules at the subscription level to help ensure policy alignment.</span></span>

<span data-ttu-id="9677a-128">[Azure 原則](/azure/governance/policy/overview)是 Azure 服務，用於建立、指派和管理原則。</span><span class="sxs-lookup"><span data-stu-id="9677a-128">[Azure Policy](/azure/governance/policy/overview) is an Azure service for creating, assigning, and managing policies.</span></span> <span data-ttu-id="9677a-129">這些原則會對您的資源強制執行不同的規則和效果，讓這些資源能符合公司標準和服務等級協定的規範。</span><span class="sxs-lookup"><span data-stu-id="9677a-129">These policies enforce different rules and effects over your resources, so those resources stay compliant with your corporate standards and service level agreements.</span></span> <span data-ttu-id="9677a-130">Azure 原則會評估您的資源是否符合指派的原則。</span><span class="sxs-lookup"><span data-stu-id="9677a-130">Azure Policy evaluates your resources for noncompliance with assigned policies.</span></span> <span data-ttu-id="9677a-131">例如，您可能要限制環境中虛擬機器的 SKU 大小。</span><span class="sxs-lookup"><span data-stu-id="9677a-131">For example, you might want to limit the SKU size of virtual machines in your environment.</span></span> <span data-ttu-id="9677a-132">實作對應原則之後，會評估新資源和現有資源的合規性。</span><span class="sxs-lookup"><span data-stu-id="9677a-132">Once a corresponding policy is implemented, new and existing resources would be evaluated for compliance.</span></span> <span data-ttu-id="9677a-133">使用正確的原則，現有的資源就可以合規。</span><span class="sxs-lookup"><span data-stu-id="9677a-133">With the right policy, existing resources can be brought into compliance.</span></span>

## <a name="cross-organization-policy"></a><span data-ttu-id="9677a-134">跨組織原則</span><span class="sxs-lookup"><span data-stu-id="9677a-134">Cross-organization policy</span></span>

<span data-ttu-id="9677a-135">隨著您的雲端資產成長跨越需要強制執行的許多訂用帳戶，您必須將焦點放在整個租用戶的強制執行策略，以確保原則一致性。</span><span class="sxs-lookup"><span data-stu-id="9677a-135">As your cloud estate grows to span many subscriptions that require enforcement, you will need to focus on a tenant-wide enforcement strategy to ensure policy consistency.</span></span>

<span data-ttu-id="9677a-136">您的[訂用帳戶設計](../subscriptions/overview.md)必須考量原則，因為它與您的組織結構相關。</span><span class="sxs-lookup"><span data-stu-id="9677a-136">Your [subscription design](../subscriptions/overview.md) will need to account for policy as it relates to your organizational structure.</span></span> <span data-ttu-id="9677a-137">除了協助支援您的訂用帳戶設計內的複雜組織[Azure 管理群組](../subscriptions/overview.md#management-groups)可用來跨多個訂用帳戶指派 Azure 原則規則。</span><span class="sxs-lookup"><span data-stu-id="9677a-137">In addition to helping support complex organization within your subscription design, [Azure management groups](../subscriptions/overview.md#management-groups) can be used to assign Azure Policy rules across multiple subscriptions.</span></span>

## <a name="automated-enforcement"></a><span data-ttu-id="9677a-138">自動化強制執行</span><span class="sxs-lookup"><span data-stu-id="9677a-138">Automated enforcement</span></span>

<span data-ttu-id="9677a-139">雖然標準化部署範本在較小規模中有效率，而 [Azure 藍圖](/azure/governance/blueprints/overview)可進行 Azure 解決方案的大規模標準化佈建和部署協調流程。</span><span class="sxs-lookup"><span data-stu-id="9677a-139">While standardized deployment templates are effective at a smaller scale, [Azure Blueprints](/azure/governance/blueprints/overview) allows large-scale standardized provisioning and deployment orchestration of Azure solutions.</span></span> <span data-ttu-id="9677a-140">跨多個訂用帳戶的工作負載可以針對任何已建立的資源，以一致的原則設定進行部署。</span><span class="sxs-lookup"><span data-stu-id="9677a-140">Workloads across multiple subscriptions can be deployed with consistent policy settings for any resources created.</span></span>

<span data-ttu-id="9677a-141">針對整合雲端與內部部署資源的 IT 環境，您需要使用記錄和報告系統來提供混合式監視功能。</span><span class="sxs-lookup"><span data-stu-id="9677a-141">For IT environments integrating cloud and on-premises resources, you may need use logging and reporting systems to provide hybrid monitoring capabilities.</span></span> <span data-ttu-id="9677a-142">您的第三方或自訂作業監視系統可能會提供額外的原則強制執行功能。</span><span class="sxs-lookup"><span data-stu-id="9677a-142">Your third-party or custom operational monitoring systems may offer additional policy enforcement capabilities.</span></span> <span data-ttu-id="9677a-143">針對複雜的雲端資產，請考量如何最佳地整合這些系統與雲端資產。</span><span class="sxs-lookup"><span data-stu-id="9677a-143">For complicated cloud estates, consider how best to integrate these systems with your cloud assets.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9677a-144">後續步驟</span><span class="sxs-lookup"><span data-stu-id="9677a-144">Next steps</span></span>

<span data-ttu-id="9677a-145">深入了解如何使用資源一致性來組織及標準化雲端部署，支援訂用帳戶設計和治理目標。</span><span class="sxs-lookup"><span data-stu-id="9677a-145">Learn how resource consistency is used to organize and standardize cloud deployments in support of subscription design and governance goals.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="9677a-146">資源一致性</span><span class="sxs-lookup"><span data-stu-id="9677a-146">Resource consistency</span></span>](../resource-consistency/overview.md)

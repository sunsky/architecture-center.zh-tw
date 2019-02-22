---
title: CAF：資源一致性原則聲明範例
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 資源一致性原則聲明範例
author: BrianBlanchard
ms.openlocfilehash: 9217ae73b0edf5b40bedac1cdc961b7a34da87d1
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900836"
---
# <a name="resource-consistency-sample-policy-statements"></a><span data-ttu-id="1086f-103">資源一致性原則聲明範例</span><span class="sxs-lookup"><span data-stu-id="1086f-103">Resource Consistency sample policy statements</span></span>

<span data-ttu-id="1086f-104">每個雲端原則聲明都是一個指導方針，用來解決在風險評估流程中識別到的特定風險。</span><span class="sxs-lookup"><span data-stu-id="1086f-104">Individual cloud policy statements are guidelines for addressing specific risks identified during your risk assessment process.</span></span> <span data-ttu-id="1086f-105">這些聲明應該會提供風險的簡明摘要，以及如何處理這些風險的方案。</span><span class="sxs-lookup"><span data-stu-id="1086f-105">These statements should provide a concise summary of risks and plans to deal with them.</span></span> <span data-ttu-id="1086f-106">每個聲明定義都應該包含下列資訊：</span><span class="sxs-lookup"><span data-stu-id="1086f-106">Each statement definition should include these pieces of information:</span></span>

- <span data-ttu-id="1086f-107">技術風險 - 此原則將解決的風險摘要。</span><span class="sxs-lookup"><span data-stu-id="1086f-107">Technical risk - A summary of the risk this policy will address.</span></span>
- <span data-ttu-id="1086f-108">原則聲明 - 明確地摘要說明原則需求。</span><span class="sxs-lookup"><span data-stu-id="1086f-108">Policy statement - A clear summary explanation of the policy requirements.</span></span>
- <span data-ttu-id="1086f-109">設計選項 - IT 小組與開發人員可以在實作原則時使用的可操作建議、規格或其他指引。</span><span class="sxs-lookup"><span data-stu-id="1086f-109">Design options - Actionable recommendations, specifications, or other guidance that IT teams and developers can use when implementing the policy.</span></span>

<span data-ttu-id="1086f-110">下列範例原則聲明會解決一些與資源一致性相關的常見業務風險，並可作為您為自己組織需求撰寫原則聲明草稿時的參考範例。</span><span class="sxs-lookup"><span data-stu-id="1086f-110">The following sample policy statements address a number of common Resource Consistency-related business risks, and are provided as examples for you to reference when drafting policy statements to address your own organization's needs.</span></span> <span data-ttu-id="1086f-111">請注意，這些範例並非禁止使用，而且可能提供數個原則選項來處理任何特定的風險。</span><span class="sxs-lookup"><span data-stu-id="1086f-111">Note that these examples are not meant to be proscriptive, and there are potentially several policy options for dealing with any particular risk.</span></span> <span data-ttu-id="1086f-112">請與業務和 IT 小組密切合作，以識別適用於您獨特風險集合的最佳原則解決方案。</span><span class="sxs-lookup"><span data-stu-id="1086f-112">Work closely with business and IT teams to identify the best policy solutions for your unique set of risks.</span></span>

## <a name="tagging"></a><span data-ttu-id="1086f-113">標記</span><span class="sxs-lookup"><span data-stu-id="1086f-113">Tagging</span></span>

<span data-ttu-id="1086f-114">**技術風險**：如果沒有與已部署的資源相關聯的適當中繼資料標記，IT 作業就無法根據所需的 SLA、商務作業的重要性或營運成本，排定支援的優先順序或最佳化資源。</span><span class="sxs-lookup"><span data-stu-id="1086f-114">**Technical risk**: Without proper metadata tagging associated with deployed resources, IT Operations cannot prioritize support or optimization of resources based on required SLA, importance to business operations, or operational cost.</span></span> <span data-ttu-id="1086f-115">這可能會導致 IT 資源的錯誤配置，以及事件解決方案中的潛在延遲。</span><span class="sxs-lookup"><span data-stu-id="1086f-115">This can result in mis-allocation of IT resources and potential delays in incident resolution.</span></span>

<span data-ttu-id="1086f-116">**原則聲明**：您將實作下列原則：</span><span class="sxs-lookup"><span data-stu-id="1086f-116">**Policy statement**: The following policies will be implemented:</span></span>

- <span data-ttu-id="1086f-117">已部署的資產應使用下列值標記：成本、嚴重性、SLA 和環境。</span><span class="sxs-lookup"><span data-stu-id="1086f-117">Deployed assets should be tagged with the following values: cost, criticality, SLA, and environment.</span></span>
- <span data-ttu-id="1086f-118">治理工具必須確認與成本、重要性、SLA、應用程式及環境相關的標記。</span><span class="sxs-lookup"><span data-stu-id="1086f-118">Governance tooling must validate tagging related to cost, criticality, SLA, application, and environment.</span></span> <span data-ttu-id="1086f-119">所有值必須符合由治理小組管理的預先定義值。</span><span class="sxs-lookup"><span data-stu-id="1086f-119">All values must align to predefined values managed by the governance team.</span></span>

<span data-ttu-id="1086f-120">**潛在的設計選項**：在 Azure 中，大部分的資源類型都支援[標準名稱/值中繼資料標記](/azure/azure-resource-manager/resource-group-using-tags)。</span><span class="sxs-lookup"><span data-stu-id="1086f-120">**Potential design options**: In Azure, [standard name-value metadata tags](/azure/azure-resource-manager/resource-group-using-tags) are supported on most resource types.</span></span> <span data-ttu-id="1086f-121">[Azure 原則](/azure/governance/policy/overview)用於在資源建立期間強制執行特定標記。</span><span class="sxs-lookup"><span data-stu-id="1086f-121">[Azure Policy](/azure/governance/policy/overview) is used to enforce specific tags as part of resource creation.</span></span>

## <a name="ungoverned-subscriptions"></a><span data-ttu-id="1086f-122">未治理的訂用帳戶</span><span class="sxs-lookup"><span data-stu-id="1086f-122">Ungoverned subscriptions</span></span>

<span data-ttu-id="1086f-123">**技術風險**：任意建立訂用帳戶和管理群組，可能會導致您雲端資產中的隔離部分未正確地受限於您的治理原則。</span><span class="sxs-lookup"><span data-stu-id="1086f-123">**Technical risk**: Arbitrary creation of subscriptions and management groups can lead to isolated sections of your cloud estate that are not properly subject to your governance policies.</span></span>

<span data-ttu-id="1086f-124">**原則聲明**：為任何任務關鍵性應用程式或受保護的資料建立新的訂用帳戶或管理群組將需要經過雲端治理小組審查。</span><span class="sxs-lookup"><span data-stu-id="1086f-124">**Policy statement**: Creation of new subscriptions or management groups for any mission-critical applications or protected data will require a review from the Cloud Governance team.</span></span> <span data-ttu-id="1086f-125">核准的變更會整合到適當的藍圖指派中。</span><span class="sxs-lookup"><span data-stu-id="1086f-125">Approved changes will be integrated into a proper blueprint assignment.</span></span>

<span data-ttu-id="1086f-126">**潛在的設計選項**：將對組織的系統管理存取權 [Azure 管理群組](/azure/governance/management-groups/)鎖定為僅限核准的控管小組成員，他們會控制訂用帳戶建立和存取控制程序。</span><span class="sxs-lookup"><span data-stu-id="1086f-126">**Potential design options**: Lock down administrative access to your organizations [Azure management groups](/azure/governance/management-groups/) to only approved governance team members who will control the subscription creation and access control process.</span></span>

## <a name="manage-updates-to-virtual-machines"></a><span data-ttu-id="1086f-127">管理虛擬機器的更新</span><span class="sxs-lookup"><span data-stu-id="1086f-127">Manage updates to virtual machines</span></span>

<span data-ttu-id="1086f-128">**技術風險**：未使用最新更新和軟體修補程式的虛擬機器 (VM) 容易受到安全性或效能問題的影響，這可能會導致服務中斷。</span><span class="sxs-lookup"><span data-stu-id="1086f-128">**Technical risk**: Virtual machines (VMs) that are not up-to-date with the latest updates and software patches are vulnerable to security or performance issues, which can result in service disruptions.</span></span>

<span data-ttu-id="1086f-129">**原則聲明**：治理工具必須強制在所有已部署的 VM 上啟用自動更新。</span><span class="sxs-lookup"><span data-stu-id="1086f-129">**Policy statement**: Governance tooling must enforce that automatic updates are enabled on all deployed VMs.</span></span> <span data-ttu-id="1086f-130">必須與作業管理小組一起檢閱違規事件，並根據作業原則來進行修復。</span><span class="sxs-lookup"><span data-stu-id="1086f-130">Violations must be reviewed with operational management teams and remediated in accordance with operations policies.</span></span> <span data-ttu-id="1086f-131">不會自動更新的資產必須包含在 IT 部門所負責的處理程序中。</span><span class="sxs-lookup"><span data-stu-id="1086f-131">Assets that are not automatically updated must be included in processes owned by IT Operations.</span></span>

<span data-ttu-id="1086f-132">**潛在的設計選項**：針對 Azure 裝載的 VM，您可以使用 [Azure 自動化中的更新管理解決方案](/azure/automation/automation-update-management)提供一致的更新管理。</span><span class="sxs-lookup"><span data-stu-id="1086f-132">**Potential design options**: For Azure hosted VMs, you can provide consistent update management using the [Update Management solution in Azure Automation](/azure/automation/automation-update-management).</span></span>

## <a name="deployment-compliance"></a><span data-ttu-id="1086f-133">部署合規性</span><span class="sxs-lookup"><span data-stu-id="1086f-133">Deployment compliance</span></span>

<span data-ttu-id="1086f-134">**技術風險**：未經雲端管理小組完全審查的部署指令碼和自動化工具可能會導致資源部署違反原則。</span><span class="sxs-lookup"><span data-stu-id="1086f-134">**Technical risk**: Deployment scripts and automation tooling that is not fully vetted by the Cloud Governance team can result in resource deployments that violate policy.</span></span>

<span data-ttu-id="1086f-135">**原則聲明**：您將實作下列原則：</span><span class="sxs-lookup"><span data-stu-id="1086f-135">**Policy statement**: The following policies will be implemented:</span></span>

- <span data-ttu-id="1086f-136">部署工具必須由雲端治理小組核准，以確保能持續治理已部署的資產。</span><span class="sxs-lookup"><span data-stu-id="1086f-136">Deployment tooling must be approved by the Cloud Governance team to ensure ongoing governance of deployed assets.</span></span>
- <span data-ttu-id="1086f-137">部署指令碼必須在雲端治理小組可存取的中央存放庫中進行維護，以便定進行定期檢閱和稽核。</span><span class="sxs-lookup"><span data-stu-id="1086f-137">Deployment scripts must be maintained in central repository accessible by the Cloud Governance team for periodic review and auditing.</span></span>

<span data-ttu-id="1086f-138">**潛在的設計選項**：透過一致使用 [Azure 藍圖](/azure/governance/blueprints/)來管理自動化部署，可以實現符合貴組織治理標準和原則的 Azure 資源的一致部署。</span><span class="sxs-lookup"><span data-stu-id="1086f-138">**Potential design options**: Consistent use of [Azure Blueprints](/azure/governance/blueprints/) to manage automated deployments allows consistent deployments of Azure resources that adhere to your organization's governance standards and policies.</span></span>

## <a name="monitoring"></a><span data-ttu-id="1086f-139">監視</span><span class="sxs-lookup"><span data-stu-id="1086f-139">Monitoring</span></span>

<span data-ttu-id="1086f-140">**技術風險**：未正確地實作或不一致地檢測監視，可以避免檢測到工作負載的健康狀態問題或其他違反合規性原則的行為。</span><span class="sxs-lookup"><span data-stu-id="1086f-140">**Technical risk**: Improperly implemented or inconsistently instrumented monitoring can prevent the detection of workload health issues or other policy compliance violations.</span></span>

<span data-ttu-id="1086f-141">**原則聲明**：您將實作下列原則：</span><span class="sxs-lookup"><span data-stu-id="1086f-141">**Policy statement**: The following policies will be implemented:</span></span>

- <span data-ttu-id="1086f-142">治理工具必須確認所有與任務關鍵性應用程式或受保護資料相關的資產都會受到監視，以了解資源損耗與最佳化的情形。</span><span class="sxs-lookup"><span data-stu-id="1086f-142">Governance tooling must validate that all assets related to mission-critical applications or protected data are included in monitoring for resource depletion and optimization.</span></span>
- <span data-ttu-id="1086f-143">治理工具必須確認會針對所有任務關鍵性應用程式或受保護的資料，收集適當層級的記錄資料。</span><span class="sxs-lookup"><span data-stu-id="1086f-143">Governance tooling must validate that the appropriate level of logging data is being collected for all mission-critical applications or protected data.</span></span>

<span data-ttu-id="1086f-144">**潛在的設計選項**：[Azure 監視器](/azure/azure-monitor/overview)是 Azure 平台中的預設監視服務，在部署資源時可以透過使用 [Azure 藍圖](/azure/governance/blueprints/)來強制執行一致的監視。</span><span class="sxs-lookup"><span data-stu-id="1086f-144">**Potential design options**: [Azure Monitor](/azure/azure-monitor/overview) is the default monitoring service in the Azure platform, and consistent monitoring can be enforced through the use of [Azure Blueprints](/azure/governance/blueprints/) when deploying resources.</span></span>

## <a name="disaster-recovery"></a><span data-ttu-id="1086f-145">災害復原</span><span class="sxs-lookup"><span data-stu-id="1086f-145">Disaster recovery</span></span>

<span data-ttu-id="1086f-146">**技術風險**：資源失敗、刪除或損毀，可能導致任務關鍵性應用程式或服務中斷和敏感性資料遺失。</span><span class="sxs-lookup"><span data-stu-id="1086f-146">**Technical risk**: Resource failure, deletions, or corruption can result in disruption of mission-critical applications or services and the loss of sensitive data.</span></span>

<span data-ttu-id="1086f-147">**原則聲明**：所有任務關鍵性應用程式和受保護的資料都必須實作備份和復原解決方案，以將中斷或系統失敗的商務影響降到最低。</span><span class="sxs-lookup"><span data-stu-id="1086f-147">**Policy statement**: All mission-critical applications and protected data must have backup and recovery solutions implemented to minimize business impact of outages or system failures.</span></span>

<span data-ttu-id="1086f-148">**潛在的設計選項**：[Azure Site Recovery] 服務提供備份、復原及複寫功能，目的在將商務持續性和災害復原 (BCDR) 之使用案例中的中斷期間降到最低。</span><span class="sxs-lookup"><span data-stu-id="1086f-148">**Potential design options**: The [Azure Site Recovery] service provides backup, recovery, and replication capabilities intended to minimize outage duration in business continuity and disaster recovery (BCDR) scenarios.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1086f-149">後續步驟</span><span class="sxs-lookup"><span data-stu-id="1086f-149">Next steps</span></span>

<span data-ttu-id="1086f-150">使用本文提及的範例作為起點，以開發與您雲端採用方案保持一致的原則來解決特定業務風險。</span><span class="sxs-lookup"><span data-stu-id="1086f-150">Use the samples mentioned in this article as a starting point to develop policies that address specific business risks that align with your cloud adoption plans.</span></span>

<span data-ttu-id="1086f-151">若要開始開發與資源一致性相關的自訂原則聲明，請下載[資源一致性範本](template.md)。</span><span class="sxs-lookup"><span data-stu-id="1086f-151">To begin developing your own custom policy statements related to Resource Consistency, download the [Resource Consistency template](template.md).</span></span>

<span data-ttu-id="1086f-152">若要加速採用這個專業領域，請選擇最密切配合您環境的[可採取動作的治理旅程](../journeys/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="1086f-152">To accelerate adoption of this discipline, choose the [Actionable Governance Journey](../journeys/overview.md) that most closely aligns with your environment.</span></span> <span data-ttu-id="1086f-153">然後修改設計，以納入您特定的公司原則決策。</span><span class="sxs-lookup"><span data-stu-id="1086f-153">Then modify the design to incorporate your specific corporate policy decisions.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="1086f-154">可採取動作的治理旅程</span><span class="sxs-lookup"><span data-stu-id="1086f-154">Actionable Governance Journeys</span></span>](../journeys/overview.md)

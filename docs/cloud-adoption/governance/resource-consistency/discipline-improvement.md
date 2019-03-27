---
title: CAF：資源一致性專業領域的改進
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 資源一致性專業領域的改進
author: BrianBlanchard
ms.openlocfilehash: bc81b894d46266c52291c53dba5532ab2ab6b860
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241569"
---
# <a name="resource-consistency-discipline-improvement"></a><span data-ttu-id="427cc-103">資源一致性專業領域的改進</span><span class="sxs-lookup"><span data-stu-id="427cc-103">Resource Consistency discipline improvement</span></span>

<span data-ttu-id="427cc-104">資源一致性專業領域著重於訂定與環境、應用程式或工作負載的作業管理有關的原則。</span><span class="sxs-lookup"><span data-stu-id="427cc-104">The Resource Consistency discipline focuses on ways of establishing policies related to the operational management of an environment, application, or workload.</span></span> <span data-ttu-id="427cc-105">在雲端治理的五個專業領域中，資源一致性包含應用程式、工作負載，和資產效能監視功能。</span><span class="sxs-lookup"><span data-stu-id="427cc-105">Within the five disciplines of Cloud Governance, Resource Consistency includes monitoring of applications, workload, and asset performance.</span></span> <span data-ttu-id="427cc-106">它也包含滿足擴展需求、補救效能服務等級協定 (SLA) 違規和藉由自動補救的方式主動避免 SLA 違規所需的任務。</span><span class="sxs-lookup"><span data-stu-id="427cc-106">It also includes the tasks required to meet scale demands, remediate performance Service Level Agreement (SLA) violations, and proactively avoid SLA violations through automated remediation.</span></span>

<span data-ttu-id="427cc-107">本文將概述一些貴公司可參與的潛在工作，以更好的方式來開發部署加速專業領域並使其臻至成熟。</span><span class="sxs-lookup"><span data-stu-id="427cc-107">This article outlines some potential tasks your company can engage in to better develop and mature the Resource Consistency discipline.</span></span> <span data-ttu-id="427cc-108">這些工作可以細分為實作雲端解決方案的規劃、建置、採用及操作階段，接著反覆執行以允許開發[雲端治理的累加方法](../journeys/overview.md#an-incremental-approach-to-cloud-governance)。</span><span class="sxs-lookup"><span data-stu-id="427cc-108">These tasks can be broken down into planning, building, adopting, and operating phases of implementing a cloud solution, which are then iterated on allowing the development of an [incremental approach to cloud governance](../journeys/overview.md#an-incremental-approach-to-cloud-governance).</span></span>

![四個採用階段](../../_images/adoption-phases.png)

<span data-ttu-id="427cc-110">*圖 1.雲端治理累加方法的採用階段。*</span><span class="sxs-lookup"><span data-stu-id="427cc-110">*Figure 1. Adoption phases of the incremental approach to cloud governance.*</span></span>

<span data-ttu-id="427cc-111">沒有任何一份文件能夠滿足所有企業需求。</span><span class="sxs-lookup"><span data-stu-id="427cc-111">It's impossible for any one document to account for the requirements of all businesses.</span></span> <span data-ttu-id="427cc-112">因此，本文將針對治理成熟流程的每個階段，概述建議的最小和潛在範例活動。</span><span class="sxs-lookup"><span data-stu-id="427cc-112">As such, this article outlines suggested minimum and potential example activities for each phase of the governance maturation process.</span></span> <span data-ttu-id="427cc-113">這些活動的初始目的是協助您建置[原則 MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance)，並建立累加式原則演進的架構。</span><span class="sxs-lookup"><span data-stu-id="427cc-113">The initial objective of these activities is to help you build a [Policy MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance) and establish a framework for incremental policy evolution.</span></span> <span data-ttu-id="427cc-114">您的雲端治理小組將需決定要對這些活動進行多少投資，以改進您的資源一致性治理功能。</span><span class="sxs-lookup"><span data-stu-id="427cc-114">Your Cloud Governance team will need to decide how much to invest in these activities to improve your Resource Consistency governance capabilities.</span></span>

> [!CAUTION]
> <span data-ttu-id="427cc-115">本文中概述的最小或潛在活動都不會與特定的公司原則或協力廠商合規性需求保持一致。</span><span class="sxs-lookup"><span data-stu-id="427cc-115">Neither the minimum or potential activities outlined in this article are aligned to specific corporate policies or third party compliance requirements.</span></span> <span data-ttu-id="427cc-116">此指導方針旨在協助促成交談，從而使這兩個需求與雲端治理模型保持一致。</span><span class="sxs-lookup"><span data-stu-id="427cc-116">This guidance is designed to help facilitate the conversations that will lead to alignment of both requirements with a cloud governance model.</span></span>

## <a name="planning-and-readiness"></a><span data-ttu-id="427cc-117">規劃和整備</span><span class="sxs-lookup"><span data-stu-id="427cc-117">Planning and readiness</span></span>

<span data-ttu-id="427cc-118">這個治理成熟度階段可消彌業務成果與可操作之策略間的鴻溝。</span><span class="sxs-lookup"><span data-stu-id="427cc-118">This phase of governance maturity bridges the divide between business outcomes and actionable strategies.</span></span> <span data-ttu-id="427cc-119">在此程序中，領導小組會定義特定的計量、將這些計量對應至數位資產，並開始規劃整體移轉工作。</span><span class="sxs-lookup"><span data-stu-id="427cc-119">During this process, the leadership team defines specific metrics, maps those metrics to the digital estate, and begins planning the overall migration effort.</span></span>

<span data-ttu-id="427cc-120">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="427cc-120">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="427cc-121">評估您的[資源一致性工具鏈](toolchain.md)選項。</span><span class="sxs-lookup"><span data-stu-id="427cc-121">Evaluate your [Resource Consistency toolchain](toolchain.md) options.</span></span>
* <span data-ttu-id="427cc-122">了解雲端策略的授權需求。</span><span class="sxs-lookup"><span data-stu-id="427cc-122">Understand the licensing requirements for your cloud strategy.</span></span>
* <span data-ttu-id="427cc-123">開發架構方針文件的草稿，並散發給重要的專案關係人。</span><span class="sxs-lookup"><span data-stu-id="427cc-123">Develop a draft Architecture Guidelines document and distribute to key stakeholders.</span></span>
* <span data-ttu-id="427cc-124">熟悉您用於部署、管理及以群組監視解決方案之所有資源的資源管理員。</span><span class="sxs-lookup"><span data-stu-id="427cc-124">Become familiar with the resource manager you use to deploy, manage, and monitor all the resources for your solution as a group.</span></span>
* <span data-ttu-id="427cc-125">教育並涵蓋受到開發架構方針影響的人員和小組。</span><span class="sxs-lookup"><span data-stu-id="427cc-125">Educate and involve the people and teams affected by the development of architecture guidelines.</span></span>
* <span data-ttu-id="427cc-126">將已設定優先權的資源部署工作新增至您的移轉待辦項目中。</span><span class="sxs-lookup"><span data-stu-id="427cc-126">Add prioritized resource deployment tasks to your migration backlog.</span></span>

<span data-ttu-id="427cc-127">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="427cc-127">**Potential activities:**</span></span>

* <span data-ttu-id="427cc-128">與商務專案關係人和/或您的雲端策略小組合作，了解業務單位與整個組織內所需的雲端帳戶管理方法和成本計量作法。</span><span class="sxs-lookup"><span data-stu-id="427cc-128">Work with the business stakeholders and/or your cloud strategy team to understand the desired cloud accounting approach and cost accounting practices within your business units and organization as a whole.</span></span>
* <span data-ttu-id="427cc-129">定義[監視和原則強制執行](compliance-processes.md)需求。</span><span class="sxs-lookup"><span data-stu-id="427cc-129">Define your [monitoring and policy enforcement](compliance-processes.md) requirements.</span></span>
* <span data-ttu-id="427cc-130">檢查商業價值及中斷成本，以定義修復原則和 SLA 需求。</span><span class="sxs-lookup"><span data-stu-id="427cc-130">Examine the business value and cost of outage to define remediation policy and SLA requirements.</span></span>
* <span data-ttu-id="427cc-131">判斷您是否將為您的資源部署[簡單工作負載](./governance-simple-workload.md)或[多小組](./governance-multiple-teams.md)治理策略。</span><span class="sxs-lookup"><span data-stu-id="427cc-131">Determine whether you'll deploy a [simple workload](./governance-simple-workload.md) or [multi-team](./governance-multiple-teams.md) governance strategy for your resources.</span></span>
* <span data-ttu-id="427cc-132">判斷您的已計劃工作負載的延展性需求。</span><span class="sxs-lookup"><span data-stu-id="427cc-132">Determine scalability requirements for your planned workloads.</span></span>

## <a name="build-and-pre-deployment"></a><span data-ttu-id="427cc-133">建置和前置部署</span><span class="sxs-lookup"><span data-stu-id="427cc-133">Build and pre-deployment</span></span>

<span data-ttu-id="427cc-134">成功移轉環境需要一些技術性和非技術性的先決條件。</span><span class="sxs-lookup"><span data-stu-id="427cc-134">A number of technical and nontechnical prerequisites are required to successful migrate an environment.</span></span> <span data-ttu-id="427cc-135">此程序著重於可繼續進行移轉的決策、整備和核心基礎結構。</span><span class="sxs-lookup"><span data-stu-id="427cc-135">This process focuses on the decisions, readiness, and core infrastructure that proceeds a migration.</span></span>

<span data-ttu-id="427cc-136">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="427cc-136">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="427cc-137">在前置部署階段中推出，以實作您的[資源一致性工具鏈](toolchain.md)。</span><span class="sxs-lookup"><span data-stu-id="427cc-137">Implement your [Resource Consistency toolchain](toolchain.md) by rolling out in a pre-deployment phase.</span></span>
* <span data-ttu-id="427cc-138">更新架構方針文件，並散發給重要的專案關係人。</span><span class="sxs-lookup"><span data-stu-id="427cc-138">Update the Architecture Guidelines document and distribute to key stakeholders.</span></span>
* <span data-ttu-id="427cc-139">在已設定優先權的移轉待辦項目上實作資源部署工作。</span><span class="sxs-lookup"><span data-stu-id="427cc-139">Implement resource deployment tasks on your prioritized migration backlog.</span></span>
* <span data-ttu-id="427cc-140">開發教育性資料和文件、認知溝通、獎勵和其他計畫，以協助試用產品的使用者採用。</span><span class="sxs-lookup"><span data-stu-id="427cc-140">Develop educational materials and documentation, awareness communications, incentives, and other programs to help drive user adoption.</span></span>

<span data-ttu-id="427cc-141">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="427cc-141">**Potential activities:**</span></span>

* <span data-ttu-id="427cc-142">判斷[訂用帳戶設計策略](../../decision-guides/subscriptions/overview.md)，選擇最符合您的組織和工作負載需求的訂用帳戶模式。</span><span class="sxs-lookup"><span data-stu-id="427cc-142">Decide on a [subscription design strategy](../../decision-guides/subscriptions/overview.md), choosing the subscription patterns that best fit your organization and workload needs.</span></span>
* <span data-ttu-id="427cc-143">使用[資源一致性](../../decision-guides/resource-consistency/overview.md)策略來強制執行架構方針。</span><span class="sxs-lookup"><span data-stu-id="427cc-143">Use a [resource consistency](../../decision-guides/resource-consistency/overview.md) strategy to enforce architecture guidelines over time.</span></span>
* <span data-ttu-id="427cc-144">實作[資源命名，並為您的資源標記標準](../../decision-guides/resource-tagging/overview.md)，以符合您的組織和帳戶處理需求。</span><span class="sxs-lookup"><span data-stu-id="427cc-144">Implement [resource naming, and tagging standards](../../decision-guides/resource-tagging/overview.md) for your resources to match your organizational and accounting requirements.</span></span>
* <span data-ttu-id="427cc-145">若要建立主動式時間點治理，請在部署資源和資源群組時，使用部署範本和自動化來強制執行常見的組態和一致的群組結構。</span><span class="sxs-lookup"><span data-stu-id="427cc-145">To create proactive point-in-time governance, use deployment templates and automation to enforce common configurations and a consistent grouping structure when deploying resources and resource groups.</span></span>
* <span data-ttu-id="427cc-146">建立最低權限的權限模型，預設情況下使用者沒有任何權限。</span><span class="sxs-lookup"><span data-stu-id="427cc-146">Establish a least privilege permissions model, where users have no permissions by default.</span></span>
* <span data-ttu-id="427cc-147">判斷組織中誰擁有每個工作負載和帳戶，以及誰將需要存取以維護或修改這些資源。</span><span class="sxs-lookup"><span data-stu-id="427cc-147">Determine who in your organization owns each workload and account, and who will need to access to maintain or modify these resources.</span></span> <span data-ttu-id="427cc-148">定義符合這些需求的雲端角色和職責，並將這些角色作為存取控制的基礎。</span><span class="sxs-lookup"><span data-stu-id="427cc-148">Define cloud roles and responsibilities that match these needs and use these roles as the basis for access control.</span></span>
* <span data-ttu-id="427cc-149">定義資源間的相依性。</span><span class="sxs-lookup"><span data-stu-id="427cc-149">Define dependencies between resources.</span></span>
* <span data-ttu-id="427cc-150">實作自動資源調整以符合計劃階段中定義的資源。</span><span class="sxs-lookup"><span data-stu-id="427cc-150">Implement automated resource scaling to match requirements defined in the Plan stage.</span></span>
* <span data-ttu-id="427cc-151">執行存取性能以測量收到的服務品質。</span><span class="sxs-lookup"><span data-stu-id="427cc-151">Conduct access performance to measure the quality of services received.</span></span>
* <span data-ttu-id="427cc-152">請考慮使用組態設定和資源建立規則部署[原則](/azure/governance/policy/overview)來管理 SLA 強制。</span><span class="sxs-lookup"><span data-stu-id="427cc-152">Consider deploying [policy](/azure/governance/policy/overview) to manage SLA enforcement using configuration settings and resource creation rules.</span></span>

## <a name="adopt-and-migrate"></a><span data-ttu-id="427cc-153">採用和移轉</span><span class="sxs-lookup"><span data-stu-id="427cc-153">Adopt and migrate</span></span>

<span data-ttu-id="427cc-154">移轉是一個累加式程序，著重於在現有的數位資產中移動、測試及採用應用程式或工作負載。</span><span class="sxs-lookup"><span data-stu-id="427cc-154">Migration is an incremental process that focuses on the movement, testing, and adoption of applications or workloads in an existing digital estate.</span></span>

<span data-ttu-id="427cc-155">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="427cc-155">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="427cc-156">將您的[資源一致性工具鏈](toolchain.md)從前置部署環境移轉至生產環境。</span><span class="sxs-lookup"><span data-stu-id="427cc-156">Migrate your [Resource Consistency toolchain](toolchain.md) from pre-deployment to production.</span></span>
* <span data-ttu-id="427cc-157">更新架構方針文件，並散發給重要的專案關係人。</span><span class="sxs-lookup"><span data-stu-id="427cc-157">Update the Architecture Guidelines document and distribute to key stakeholders.</span></span>
* <span data-ttu-id="427cc-158">開發教育性資料和文件、認知溝通、獎勵和其他計畫，以協助試用產品的使用者採用。</span><span class="sxs-lookup"><span data-stu-id="427cc-158">Develop educational materials and documentation, awareness communications, incentives, and other programs to help drive user adoption.</span></span>
* <span data-ttu-id="427cc-159">移轉任何現有的自動化補救指令碼或工具，以支援定義的 SLA 需求。</span><span class="sxs-lookup"><span data-stu-id="427cc-159">Migrate any existing automated remediation scripts or tools to support defined SLA requirements.</span></span>

<span data-ttu-id="427cc-160">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="427cc-160">**Potential activities:**</span></span>

* <span data-ttu-id="427cc-161">完成並測試監視和報告資料。</span><span class="sxs-lookup"><span data-stu-id="427cc-161">Complete and test monitoring and reporting data.</span></span> <span data-ttu-id="427cc-162">使用您選擇的內部部署、雲端閘道或混合式解決方案。</span><span class="sxs-lookup"><span data-stu-id="427cc-162">with your chosen on-premises, cloud gateway, or hybrid solution.</span></span>
* <span data-ttu-id="427cc-163">判斷是否需要對 SLA 或資源管理原則進行變更。</span><span class="sxs-lookup"><span data-stu-id="427cc-163">Determine if changes need to be made to SLA or management policy for resources.</span></span>
* <span data-ttu-id="427cc-164">透過實作查詢功能來有效率地尋找跨雲端資產中的資源，從而改善作業工作。</span><span class="sxs-lookup"><span data-stu-id="427cc-164">Improve operations tasks by implementing query capabilities to efficiently find resource across your cloud estate.</span></span>
* <span data-ttu-id="427cc-165">使資源與不斷變化的業務需求和治理需求保持一致。</span><span class="sxs-lookup"><span data-stu-id="427cc-165">Align resources to changing business needs and governance requirements.</span></span>
* <span data-ttu-id="427cc-166">請確定您的虛擬機器、虛擬網路和儲存體帳戶，在每個版本中反映實際資源的存取需求，並視需要調整。</span><span class="sxs-lookup"><span data-stu-id="427cc-166">Ensure that your virtual machines, virtual networks, and storage accounts reflect actual resource access needs during each release, and adjust as necessary.</span></span>
* <span data-ttu-id="427cc-167">確認自動調整的資源符合存取需求。</span><span class="sxs-lookup"><span data-stu-id="427cc-167">Verify automated scaling of resources meets access requirements.</span></span>
* <span data-ttu-id="427cc-168">檢閱使用者對資源、資源群組和 Azure 訂用帳戶的存取權限，並視需要調整存取控制。</span><span class="sxs-lookup"><span data-stu-id="427cc-168">Review user access to resources, resource groups, and Azure subscriptions, and adjust access controls as necessary.</span></span>
* <span data-ttu-id="427cc-169">監視資源存取方案中的變更，並且在需要額外的登出時與專案關係人進行驗證。</span><span class="sxs-lookup"><span data-stu-id="427cc-169">Monitor changes in resource access plans and validate with stakeholders if additional sign-offs are needed.</span></span>
* <span data-ttu-id="427cc-170">更新架構方針文件的變更，以反映實際成本。</span><span class="sxs-lookup"><span data-stu-id="427cc-170">Update changes to the Architecture Guidelines document to reflect actual costs.</span></span>
* <span data-ttu-id="427cc-171">判斷您的組織是否要求對業務單位的損益 (P&L) 進行更清楚的財務調整。</span><span class="sxs-lookup"><span data-stu-id="427cc-171">Determine whether your organization requires clearer financial alignment to P&Ls for business units.</span></span>
* <span data-ttu-id="427cc-172">對於全球組織，請實作您的 SLA 合規性及主權需求。</span><span class="sxs-lookup"><span data-stu-id="427cc-172">For global organizations, implement your SLA compliance or sovereignty requirements.</span></span>
* <span data-ttu-id="427cc-173">對於雲端彙總，請將閘道解決方案部署到雲端提供者。</span><span class="sxs-lookup"><span data-stu-id="427cc-173">For cloud aggregation, deploy a gateway solution to a cloud provider.</span></span>
* <span data-ttu-id="427cc-174">對於不允許混合式或閘道選項的工具，請使用作業的監視工具將監視緊密結合。</span><span class="sxs-lookup"><span data-stu-id="427cc-174">For tools that don't allow for hybrid or gateway options, tightly couple monitoring with an operational monitoring tool.</span></span>

## <a name="operate-and-post-implementation"></a><span data-ttu-id="427cc-175">操作和實作後</span><span class="sxs-lookup"><span data-stu-id="427cc-175">Operate and post-implementation</span></span>

<span data-ttu-id="427cc-176">轉換完成之後，治理和操作必須依存於應用程式或工作負載的自然生命週期。</span><span class="sxs-lookup"><span data-stu-id="427cc-176">Once the transformation is complete, governance and operations must live on for the natural lifecycle of an application or workload.</span></span> <span data-ttu-id="427cc-177">這個治理成熟度階段著重於通常會在實作解決方案且轉換週期開始穩定後隨之而來的活動。</span><span class="sxs-lookup"><span data-stu-id="427cc-177">This phase of governance maturity focuses on the activities that commonly come after the solution is implemented and the transformation cycle begins to stabilize.</span></span>

<span data-ttu-id="427cc-178">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="427cc-178">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="427cc-179">根據組織不斷變化之成本管理需求的更新，自訂[資源一致性工具鏈](toolchain.md)。</span><span class="sxs-lookup"><span data-stu-id="427cc-179">Customize your [Resource Consistency toolchain](toolchain.md) based on updates to your organization’s changing Cost Management needs.</span></span>
* <span data-ttu-id="427cc-180">考慮將任何通知和報告自動化，以反映實際資源使用量。</span><span class="sxs-lookup"><span data-stu-id="427cc-180">Consider automating any notifications and reports to reflect actual resource usage.</span></span>
* <span data-ttu-id="427cc-181">精簡架構方針，以引導未來的採用程序。</span><span class="sxs-lookup"><span data-stu-id="427cc-181">Refine Architecture Guidelines to guide future adoption processes.</span></span>
* <span data-ttu-id="427cc-182">定期教育受影響的小組，以確保會持續遵循架構方針。</span><span class="sxs-lookup"><span data-stu-id="427cc-182">Educate affected teams periodically to ensure ongoing adherence to the architecture guidelines.</span></span>

<span data-ttu-id="427cc-183">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="427cc-183">**Potential activities:**</span></span>

* <span data-ttu-id="427cc-184">每季調整方案，以反映實際資源的變更。</span><span class="sxs-lookup"><span data-stu-id="427cc-184">Adjust plans quarterly to reflect changes to actual resources.</span></span>
* <span data-ttu-id="427cc-185">在未來的部署期間自動套用和強制執行治理需求。</span><span class="sxs-lookup"><span data-stu-id="427cc-185">Automatically apply and enforce governance requirements during future deployments.</span></span>
* <span data-ttu-id="427cc-186">評估未充分使用的資源，並判斷其是否值得繼續。</span><span class="sxs-lookup"><span data-stu-id="427cc-186">Evaluate underused resources and determine if they're worth continuing.</span></span>
* <span data-ttu-id="427cc-187">偵測計劃與實際資源使用量之間的不一致與異常狀況。</span><span class="sxs-lookup"><span data-stu-id="427cc-187">Detect misalignments and anomalies between planned and actual resource usage.</span></span>
* <span data-ttu-id="427cc-188">協助雲端採用小組和雲端策略小組了解並解決這些異常狀況。</span><span class="sxs-lookup"><span data-stu-id="427cc-188">Assist the cloud adoption teams and the Cloud Strategy team in understanding and resolving these anomalies.</span></span>
* <span data-ttu-id="427cc-189">判斷是否需要對計費和 SLA 的資源一致性進行變更。</span><span class="sxs-lookup"><span data-stu-id="427cc-189">Determine if changes need to be made to Resource Consistency for billing and SLAs.</span></span>
* <span data-ttu-id="427cc-190">評估記錄和監視工具，以判斷您的內部部署、雲端閘道或混合式解決方案是否需要調整。</span><span class="sxs-lookup"><span data-stu-id="427cc-190">Evaluate logging and monitoring tools to determine whether your on-premises, cloud gateway, or hybrid solution needs adjusting.</span></span>
* <span data-ttu-id="427cc-191">對於業務單位和地理位置分散的群組，請判斷您的組織是否應考慮使用其他雲端管理功能 (例如 [Azure 管理群組](/azure/governance/management-groups/)) 以進一步套用集中式原則並符合 SLA 需求。</span><span class="sxs-lookup"><span data-stu-id="427cc-191">For business units and geographically distributed groups, determine if your organization should consider using additional cloud management features (for example [Azure management groups](/azure/governance/management-groups/)) to better apply centralized policy and meet SLA requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="427cc-192">後續步驟</span><span class="sxs-lookup"><span data-stu-id="427cc-192">Next steps</span></span>

<span data-ttu-id="427cc-193">既然您已了解雲端資源治理的概念，請繼續深入了解在 Azure 中[如何管理資源存取權](azure-resource-access.md)，以準備學習如何設計[簡單工作負載](governance-simple-workload.md)和[多個小組](governance-multiple-teams.md)的治理模型。</span><span class="sxs-lookup"><span data-stu-id="427cc-193">Now that you understand the concept of cloud resource governance, move on to learn more about [how resource access is managed](azure-resource-access.md) in Azure in preparation for learning how to design a governance model for a [simple workload](governance-simple-workload.md) or for [multiple teams](governance-multiple-teams.md).</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="427cc-194">[深入了解 Azure 中的資源存取](azure-resource-access.md)
> [深入了解適用於 Azure 的 SLA](https://azure.microsoft.com/support/legal/sla/)
> [深入了解記錄、報告和監視](../../decision-guides/log-and-report/overview.md)</span><span class="sxs-lookup"><span data-stu-id="427cc-194">[Learn about resource access in Azure](azure-resource-access.md)
[Learn about SLAs for Azure](https://azure.microsoft.com/support/legal/sla/)
[Learn about logging, reporting, and monitoring](../../decision-guides/log-and-report/overview.md)</span></span>

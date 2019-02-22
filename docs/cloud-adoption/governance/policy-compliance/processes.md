---
title: CAF：監視和強制執行原則遵循
description: 您要如何確保符合已建立的原則？
author: BrianBlanchard
ms.date: 1/4/2019
ms.openlocfilehash: 9066f33c8baa183476a9632e82d6eb960d03752c
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900756"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-processes-can-help-ensure-policy-adherence"></a><span data-ttu-id="3018f-103">哪些流程可協助確保原則遵循？</span><span class="sxs-lookup"><span data-stu-id="3018f-103">What processes can help ensure policy adherence?</span></span>

<!---
I've defined policies, I've provided an architecture guide. Now how do I monitor adherence to policy? If there is a violation, how do I enforce the policy?
--->

<span data-ttu-id="3018f-104">建立您的雲端原則聲明並且草擬設計指南之後，您必須建立策略以確保您的雲端部署符合原則需求。</span><span class="sxs-lookup"><span data-stu-id="3018f-104">After establishing your cloud policy statements and drafting a design guide, you'll need to create a strategy for ensuring your cloud deployment stays in compliance with your policy requirements.</span></span> <span data-ttu-id="3018f-105">此策略必須包含您雲端治理小組的持續檢閱和通訊流程，建立當違反原則時需要動作的準則，並且定義自動化監視和合規性系統 (將會偵測違規和觸發修復動作) 的需求。</span><span class="sxs-lookup"><span data-stu-id="3018f-105">This strategy will need to encompass your Cloud Governance team's ongoing review and communication processes, establish criteria for when policy violations require action, and defining the requirements for automated monitoring and compliance systems that will detect violations and trigger remediation actions.</span></span>

<span data-ttu-id="3018f-106">請參閱[可採取動作的治理旅程](../journeys/overview.md)的公司原則區段，以取得原則遵循流程如何符合雲端治理方案的範例。</span><span class="sxs-lookup"><span data-stu-id="3018f-106">See the corporate policy sections of the [Actionable Governance Journeys](../journeys/overview.md) for examples of how policy adherence process fit into a cloud governance plan.</span></span>

## <a name="prioritize-policy-adherence-processes"></a><span data-ttu-id="3018f-107">制定原則遵循流程的優先順序</span><span class="sxs-lookup"><span data-stu-id="3018f-107">Prioritize policy adherence processes</span></span>

<span data-ttu-id="3018f-108">需要在開發流程方面投資多少，才能支援您的原則目標？</span><span class="sxs-lookup"><span data-stu-id="3018f-108">How much investment in developing processes is required to support your policy goals?</span></span> <span data-ttu-id="3018f-109">根據您的雲端部署的大小和成熟度，建立支援合規性之流程的投入量，以及與此投入量相關聯的成本可能大不相同。</span><span class="sxs-lookup"><span data-stu-id="3018f-109">Depending on the size and maturity of your cloud deployment, the effort required to establish processes that support compliance, and the costs associated with this effort, can vary widely.</span></span>

<span data-ttu-id="3018f-110">針對包含開發和測試資源的小型部署，原則需求簡單且需要少數的專用資源就能解決。</span><span class="sxs-lookup"><span data-stu-id="3018f-110">For small deployments consisting of development and test resources, policy requirements may be simple and require few dedicated resources to address.</span></span> <span data-ttu-id="3018f-111">相反地，具有高優先順序安全性和效能需求的成熟任務關鍵性雲端部署，可能需要一組人員、廣泛的內部流程以及自訂監視工具，才能支援您的原則目標。</span><span class="sxs-lookup"><span data-stu-id="3018f-111">On the other hand, a mature mission-critical cloud deployment with high-priority security and performance needs may require a team of staff, extensive internal processes, and custom monitoring tooling to support your policy goals.</span></span>

<span data-ttu-id="3018f-112">作為定義您原則遵循策略中的第一個步驟，請評估以下討論的流程可以如何支援您的原則需求。</span><span class="sxs-lookup"><span data-stu-id="3018f-112">As a first step in defining your policy adherence strategy, evaluate how the processes discussed below can support your policy requirements.</span></span> <span data-ttu-id="3018f-113">決定投資這些流程值得多少投入量，然後使用這項資訊來建立符合這些需求的實際預算和人員計劃。</span><span class="sxs-lookup"><span data-stu-id="3018f-113">Determine how much effort is worth investing in these processes, and then use this information to establish realistic budget and staffing plans to meet these needs.</span></span>

## <a name="establish-cloud-governance-team-processes"></a><span data-ttu-id="3018f-114">建立雲端治理小組流程</span><span class="sxs-lookup"><span data-stu-id="3018f-114">Establish Cloud Governance team processes</span></span>

<span data-ttu-id="3018f-115">在定義原則合規性修復的觸發程序之前，您需要建立您的小組將會使用，以及資訊如何在 IT 人員與雲端治理小組之間共用和呈報的整體流程。</span><span class="sxs-lookup"><span data-stu-id="3018f-115">Before defining triggers for policy compliance remediation, you need establish the overall processes that your team will use and how information will be shared and escalated between IT staff and the Cloud Governance team.</span></span>

### <a name="assign-cloud-governance-team-members"></a><span data-ttu-id="3018f-116">指派雲端治理小組成員</span><span class="sxs-lookup"><span data-stu-id="3018f-116">Assign Cloud Governance team members</span></span>

<span data-ttu-id="3018f-117">誰會提供原則合規性的持續指引，並且處理在部署及操作您的雲端資產時出現的原則相關問題？</span><span class="sxs-lookup"><span data-stu-id="3018f-117">Who will provide ongoing guidance on policy compliance and handle policy-related issues that emerge when deploying and operating your cloud assets?</span></span> <span data-ttu-id="3018f-118">雲端治理小組的大小和組合取決於原則需求的複雜度，以及您附加至原則合規性的預算和人員優先順序。</span><span class="sxs-lookup"><span data-stu-id="3018f-118">The size and composition of your Cloud Governance team will depend on the complexity of your policy requirements, and the budgeting and staffing priorities you've attached to policy compliance.</span></span>

<span data-ttu-id="3018f-119">選擇已定義原則聲明所涵蓋的區域中，具有專業知識的小組成員。</span><span class="sxs-lookup"><span data-stu-id="3018f-119">Choose team members that have expertise in the areas covered by your defined policy statements.</span></span> <span data-ttu-id="3018f-120">針對初始測試部署，可以限制為負責建立治理基本的少數系統管理員。</span><span class="sxs-lookup"><span data-stu-id="3018f-120">For initial test deployments this can be limited to a few system administrators responsible for establishing the basics of governance.</span></span> <span data-ttu-id="3018f-121">隨著您的部署成熟以及原則變得更加複雜且與更廣泛公司原則需求更緊密地整合，您的雲端治理小組必須變更以支援日益增加的複雜原則需求。</span><span class="sxs-lookup"><span data-stu-id="3018f-121">As your deployments mature and your policies become more complex and more integrated with your wider corporate policy requirements, your Cloud Governance team will need to change to support increasingly complicated policy requirements.</span></span>

<span data-ttu-id="3018f-122">隨著您的治理流程成熟，請定期檢閱雲端指引小組的成員資格，以確保您可以適當地解決最新的原則需求。</span><span class="sxs-lookup"><span data-stu-id="3018f-122">As your governance processes mature, review the cloud guidance team's membership regularly to ensure that you can properly address the latest policy requirements.</span></span> <span data-ttu-id="3018f-123">識別具有相關經驗或者對於特定治理領域有興趣的 IT 人員成員，並且視需要將他們永久或依臨機操作基準納入小組中。</span><span class="sxs-lookup"><span data-stu-id="3018f-123">Identify members of your IT staff with relevant experience or interest in specific areas of governance and include them in your teams on a permanent or ad-hoc basis as-needed.</span></span>

### <a name="reviews-and-policy-iteration"></a><span data-ttu-id="3018f-124">檢閱和原則反覆項目</span><span class="sxs-lookup"><span data-stu-id="3018f-124">Reviews and policy iteration</span></span>

<span data-ttu-id="3018f-125">隨著額外資源部署，雲端治理小組必須確定新工作負載或資產符合原則需求。</span><span class="sxs-lookup"><span data-stu-id="3018f-125">As additional resources are deployed, the Cloud Governance team will need to ensure that new workloads or assets comply with policy requirements.</span></span> <span data-ttu-id="3018f-126">規劃與負責部署任何新資源的小組開會，討論與設計指南保持一致。</span><span class="sxs-lookup"><span data-stu-id="3018f-126">Plan to meet with the teams responsible for deploying any new resources to discuss alignment with your design guides.</span></span>

<span data-ttu-id="3018f-127">隨著整體部署成長，定期評估新的潛在風險並且視需要更新原則聲明和設計指南。</span><span class="sxs-lookup"><span data-stu-id="3018f-127">As your overall deployment grows, evaluate new potential risks regularly and update policy statements and design guides as needed.</span></span> <span data-ttu-id="3018f-128">排程五個治理專業領域個別的定期檢閱週期，以確保原則是最新的且符合原則。</span><span class="sxs-lookup"><span data-stu-id="3018f-128">Schedule regular review cycles each of the five governance disciplines to ensure policy is up-to-date and being met.</span></span>

### <a name="education"></a><span data-ttu-id="3018f-129">教育訓練</span><span class="sxs-lookup"><span data-stu-id="3018f-129">Education</span></span>

<span data-ttu-id="3018f-130">原則合規性需要 IT 人員和開發人員了解影響他們責任領域的原則需求。</span><span class="sxs-lookup"><span data-stu-id="3018f-130">Policy compliance requires IT staff and developers to understand the policy requirements that affect their areas of responsibility.</span></span> <span data-ttu-id="3018f-131">規劃投入資源以記錄決策和需求，並且對所有相關小組教育支援您的原則需求的設計指南。</span><span class="sxs-lookup"><span data-stu-id="3018f-131">Plan to devote resources to document decisions and requirements, and educate all relevant teams on the design guides that support your policy requirements.</span></span>

<span data-ttu-id="3018f-132">因應原則變更，定期更新文件和訓練教材，並確保教育工作能夠將更新的需求和指引傳授給相關的 IT 人員。</span><span class="sxs-lookup"><span data-stu-id="3018f-132">As policy changes, regularly update documentation and training materials, and ensure education efforts communicate updated requirements and guidance to relevant IT staff.</span></span>  

### <a name="establish-escalation-paths"></a><span data-ttu-id="3018f-133">建立呈核路徑</span><span class="sxs-lookup"><span data-stu-id="3018f-133">Establish escalation paths</span></span>

<span data-ttu-id="3018f-134">如果資源不符合規範，誰會收到通知？</span><span class="sxs-lookup"><span data-stu-id="3018f-134">If a resource goes out of compliance, who gets notified?</span></span> <span data-ttu-id="3018f-135">如果 IT 人員偵測到原則合規性問題，他們要連絡誰？</span><span class="sxs-lookup"><span data-stu-id="3018f-135">If IT staff detect a policy compliance issue, who do they contact?</span></span> <span data-ttu-id="3018f-136">請確定已清楚定義雲端治理小組的呈報流程。</span><span class="sxs-lookup"><span data-stu-id="3018f-136">Make sure the escalation process to the Cloud Governance team is clearly defined.</span></span> <span data-ttu-id="3018f-137">請確定這些通訊通道會保持更新，以反映人員和組織變更。</span><span class="sxs-lookup"><span data-stu-id="3018f-137">Ensure these communication channels are kept updated to reflect staff and organization changes.</span></span>

## <a name="violation-triggers-and-actions"></a><span data-ttu-id="3018f-138">違規觸發程序和動作</span><span class="sxs-lookup"><span data-stu-id="3018f-138">Violation triggers and actions</span></span>

<span data-ttu-id="3018f-139">在您定義雲端治理小組及其流程之後，您必須明確地定義將會觸發動作的合規性違規合格項目，以及這些動作是什麼。</span><span class="sxs-lookup"><span data-stu-id="3018f-139">After defining your Cloud Governance team and its processes, you need to explicitly define what qualifies as compliance violations that will triggers actions, and what those actions should be.</span></span>

### <a name="define-triggers"></a><span data-ttu-id="3018f-140">定義觸發程序</span><span class="sxs-lookup"><span data-stu-id="3018f-140">Define triggers</span></span>

<span data-ttu-id="3018f-141">針對每個原則聲明，檢閱需求以決定何者構成原則違規。</span><span class="sxs-lookup"><span data-stu-id="3018f-141">For each of your policy statements, review requirements to determine what constitutes a policy violation.</span></span> <span data-ttu-id="3018f-142">使用您在原則定義流程中建立的資訊來產生觸發程序。</span><span class="sxs-lookup"><span data-stu-id="3018f-142">Generate your triggers using the information you've already established as part of the policy definition process.</span></span>

* <span data-ttu-id="3018f-143">風險承受度 - 根據您在[風險承受度分析](risk-tolerance.md)中建立的計量和風險指標，建立違規觸發程序。</span><span class="sxs-lookup"><span data-stu-id="3018f-143">Risk tolerance - Create violation triggers based on the metrics and risk indicators you established as part of your [risk tolerance analysis](risk-tolerance.md).</span></span>
* <span data-ttu-id="3018f-144">定義原則需求 - 原則聲明可能會提供服務等級協定 (SLA)、商務持續性和災害復原 (BRCD) 或效能需求，這些項目應該用來作為合規性觸發程序的基礎。</span><span class="sxs-lookup"><span data-stu-id="3018f-144">Defined policy requirements - Policy statements may provide Service Level Agreement (SLA), Business continuity and disaster recovery (BRCD), or performance requirements that should be used as the basis for compliance triggers.</span></span>

### <a name="define-actions"></a><span data-ttu-id="3018f-145">定義動作</span><span class="sxs-lookup"><span data-stu-id="3018f-145">Define actions</span></span>

<span data-ttu-id="3018f-146">每個違規觸發程序都應該有一個對應的動作。</span><span class="sxs-lookup"><span data-stu-id="3018f-146">Each violation trigger should have a corresponding action.</span></span> <span data-ttu-id="3018f-147">觸發的動作都應該在發生違規時，通知適當的 IT 人員或雲端治理小組成員。</span><span class="sxs-lookup"><span data-stu-id="3018f-147">Triggered actions should always notify an appropriate IT staff or Cloud Governance team member when a violation occurs.</span></span> <span data-ttu-id="3018f-148">此通知會根據偵測到違規的類型和嚴重性，導致手動檢閱合規性問題及啟動預先建立的修復流程。</span><span class="sxs-lookup"><span data-stu-id="3018f-148">This notification can lead to a manual review of the compliance issue or kickoff a pre-established remediation process depending on the type and severity of the detected violation.</span></span>

<span data-ttu-id="3018f-149">違規觸發程序和動作的一些範例：</span><span class="sxs-lookup"><span data-stu-id="3018f-149">Some examples of violation triggers and actions:</span></span>

| <span data-ttu-id="3018f-150">雲端治理專業領域</span><span class="sxs-lookup"><span data-stu-id="3018f-150">Cloud governance discipline</span></span> | <span data-ttu-id="3018f-151">範例觸發程序</span><span class="sxs-lookup"><span data-stu-id="3018f-151">Sample trigger</span></span> | <span data-ttu-id="3018f-152">範例動作</span><span class="sxs-lookup"><span data-stu-id="3018f-152">Sample action</span></span> |
|-----------------------------|----------------|---------------|
| <span data-ttu-id="3018f-153">成本管理</span><span class="sxs-lookup"><span data-stu-id="3018f-153">Cost Management</span></span> | <span data-ttu-id="3018f-154">每月雲端費用高於預期 20%。</span><span class="sxs-lookup"><span data-stu-id="3018f-154">Monthly cloud spending is more than 20% higher than expected.</span></span> | <span data-ttu-id="3018f-155">通知計費單位主管，該主管會開始檢閱資源使用量。</span><span class="sxs-lookup"><span data-stu-id="3018f-155">Notify billing unit leader who will begin a review of resource usage.</span></span> |
| <span data-ttu-id="3018f-156">安全性基準</span><span class="sxs-lookup"><span data-stu-id="3018f-156">Security Baseline</span></span> | <span data-ttu-id="3018f-157">偵測到可疑的使用者登入活動。</span><span class="sxs-lookup"><span data-stu-id="3018f-157">Detect suspicious user login activity.</span></span> | <span data-ttu-id="3018f-158">通知 IT 安全性小組，並停用可疑的使用者帳戶。</span><span class="sxs-lookup"><span data-stu-id="3018f-158">Notify IT security team and disable suspect user account.</span></span> |
| <span data-ttu-id="3018f-159">資源一致性</span><span class="sxs-lookup"><span data-stu-id="3018f-159">Resource Consistency</span></span> | <span data-ttu-id="3018f-160">工作負載的 CPU 使用率大於 90%。</span><span class="sxs-lookup"><span data-stu-id="3018f-160">CPU utilization for workload is greater than 90%.</span></span> | <span data-ttu-id="3018f-161">通知 IT 作業小組並且相應放大其他資源來處理負載。</span><span class="sxs-lookup"><span data-stu-id="3018f-161">Notify the IT Operations team and scale out additional resources to handle load.</span></span> |

## <a name="monitoring-and-compliance-automation"></a><span data-ttu-id="3018f-162">監視與合規性自動化</span><span class="sxs-lookup"><span data-stu-id="3018f-162">Monitoring and compliance automation</span></span>

<span data-ttu-id="3018f-163">在您定義合規性違規觸發程序和動作之後，您可以開始規劃如何最佳地使用記錄和報告工具及雲端平台的其他功能，協助自動化監視和原則合規性策略。</span><span class="sxs-lookup"><span data-stu-id="3018f-163">After you've defined your compliance violation triggers and actions, you can start planning how best to use the logging and reporting tools and other features of the cloud platform to help automate your monitoring and policy compliance strategy.</span></span>

<span data-ttu-id="3018f-164">請參閱 CAF [記錄和報告決策指南](../../decision-guides/log-and-report/overview.md)主題，以取得選擇部署的最佳監視模式的指引。</span><span class="sxs-lookup"><span data-stu-id="3018f-164">Consult the CAF [logging and reporting decision guide](../../decision-guides/log-and-report/overview.md) topic for guidance on choosing the best monitoring pattern for your deployment.</span></span>

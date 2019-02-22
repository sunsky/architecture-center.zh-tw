---
title: 大型企業 - 資源一致性演進
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 大型企業 - 資源一致性演進
author: BrianBlanchard
ms.openlocfilehash: 9fc6ca015310c02338463d3c8aa53ddfc74699df
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900903"
---
# <a name="large-enterprise-resource-consistency-evolution"></a><span data-ttu-id="05454-103">大型企業：資源一致性演進</span><span class="sxs-lookup"><span data-stu-id="05454-103">Large enterprise: Resource Consistency evolution</span></span>

<span data-ttu-id="05454-104">本文將藉由新增治理 MVP 的資源一致性控制項，來治理支援任務關鍵性應用程式的敘述。</span><span class="sxs-lookup"><span data-stu-id="05454-104">This article evolves the narrative by adding Resource Consistency controls to the governance MVP to support mission-critical applications.</span></span>

## <a name="evolution-of-the-narrative"></a><span data-ttu-id="05454-105">敘述的演進</span><span class="sxs-lookup"><span data-stu-id="05454-105">Evolution of the narrative</span></span>

<span data-ttu-id="05454-106">雲端採用小組已滿足移動受保護資料必須達成的所有需求。</span><span class="sxs-lookup"><span data-stu-id="05454-106">The cloud adoption teams have met all requirements to move protected data.</span></span> <span data-ttu-id="05454-107">藉由這些應用程式，SLA 就能夠對業務做出承諾，且需要 IT 作業部門的支援。</span><span class="sxs-lookup"><span data-stu-id="05454-107">With those applications come SLA commitments to the business and need for support from IT Operations.</span></span> <span data-ttu-id="05454-108">在移轉兩個資料中心的小組背後，多個應用程式開發和 BI 小組準備開始正式推出新的解決方案。</span><span class="sxs-lookup"><span data-stu-id="05454-108">Right behind the team migrating the two datacenters, multiple app dev and BI teams are ready to begin launching new solutions into production.</span></span> <span data-ttu-id="05454-109">IT 作業剛開始接觸雲端作業思維，且需要快速整合現有作業過程的方法。</span><span class="sxs-lookup"><span data-stu-id="05454-109">IT Operations is new to the thought of cloud operations and needs a way to quickly integrate existing operational processes.</span></span>

### <a name="evolution-of-current-state"></a><span data-ttu-id="05454-110">目前狀態的演進</span><span class="sxs-lookup"><span data-stu-id="05454-110">Evolution of current state</span></span>

- <span data-ttu-id="05454-111">IT 正在積極將包含受保護資料的生產工作負載轉移到 Azure 中。</span><span class="sxs-lookup"><span data-stu-id="05454-111">IT is actively moving production workloads with protected data into Azure.</span></span> <span data-ttu-id="05454-112">許多低優先順序的工作負載正在為生產流量提供服務。</span><span class="sxs-lookup"><span data-stu-id="05454-112">A number of low priority workloads are serving production traffic.</span></span> <span data-ttu-id="05454-113">IT 作業完成支援工作負載的準備後，就更能得心應手。</span><span class="sxs-lookup"><span data-stu-id="05454-113">More can be cut over, as soon as IT Operations signs off on readiness to support the workloads.</span></span>
- <span data-ttu-id="05454-114">應用程式開發小組已準備好使用生產環境流量。</span><span class="sxs-lookup"><span data-stu-id="05454-114">The application development teams are ready for production traffic.</span></span>
- <span data-ttu-id="05454-115">BI 小組已準備將預測和見解整合到為三個業務單位運作的系統中。</span><span class="sxs-lookup"><span data-stu-id="05454-115">The BI team is ready to integrate predictions and insights into the systems that run operations for the three business units.</span></span>

### <a name="evolution-of-the-future-state"></a><span data-ttu-id="05454-116">未來狀態的演進</span><span class="sxs-lookup"><span data-stu-id="05454-116">Evolution of the future state</span></span>

- <span data-ttu-id="05454-117">IT 作業剛開始接觸雲端作業思維，且需要快速整合現有作業過程的方法。</span><span class="sxs-lookup"><span data-stu-id="05454-117">IT operations is new to the thought of cloud operations and needs a way to quickly integrate existing operational processes.</span></span>

<span data-ttu-id="05454-118">目前和未來狀態的變更會產生新風險，因此需要新的原則聲明。</span><span class="sxs-lookup"><span data-stu-id="05454-118">The changes to current and future state expose new risks that will require new policy statements.</span></span>

## <a name="evolution-of-tangible-risks"></a><span data-ttu-id="05454-119">實質風險的演進</span><span class="sxs-lookup"><span data-stu-id="05454-119">Evolution of tangible risks</span></span>

<span data-ttu-id="05454-120">**業務中斷**：任何新平台都有使任務關鍵性業務程序中斷的固有風險。</span><span class="sxs-lookup"><span data-stu-id="05454-120">**Business Interruption**: There is an inherent risk of any new platform causing interruptions to mission-critical business processes.</span></span> <span data-ttu-id="05454-121">IT 作業小組和執行各種雲端採用的小組都相對缺乏雲端作業的相關經驗。</span><span class="sxs-lookup"><span data-stu-id="05454-121">The IT Operations team and the teams executing on various cloud adoptions are relatively inexperienced with cloud operations.</span></span> <span data-ttu-id="05454-122">這會增加業務中斷的風險，且必須降低風險並進行治理。</span><span class="sxs-lookup"><span data-stu-id="05454-122">This increases the risk of interruption and must be mitigated and governed.</span></span>

<span data-ttu-id="05454-123">此商務風險可能會延伸出一些技術風險：</span><span class="sxs-lookup"><span data-stu-id="05454-123">This business risk can be expanded into several technical risks:</span></span>

- <span data-ttu-id="05454-124">不當的作業程序可能導致無法快速偵測或補救的中斷情況。</span><span class="sxs-lookup"><span data-stu-id="05454-124">Misaligned operational processes might lead to outages that can’t be detected or remediated quickly.</span></span>
- <span data-ttu-id="05454-125">外部入侵或拒絕服務攻擊可能會導致業務中斷</span><span class="sxs-lookup"><span data-stu-id="05454-125">External intrusion or denial of service attacks might cause a business interruption</span></span>
- <span data-ttu-id="05454-126">無法正確探索到任務關鍵性的資產，因此無法正確運作。</span><span class="sxs-lookup"><span data-stu-id="05454-126">Mission-critical assets might not be properly discovered and therefore not properly operated.</span></span>
- <span data-ttu-id="05454-127">現有的作業管理程序可能不支援未探索到或標記錯誤的資產。</span><span class="sxs-lookup"><span data-stu-id="05454-127">Undiscovered or mislabeled assets might not be supported by existing operational management processes.</span></span>
- <span data-ttu-id="05454-128">已部署資產的設定可能不符合效能預期。</span><span class="sxs-lookup"><span data-stu-id="05454-128">Configuration of deployed assets might not meet performance expectations.</span></span>
- <span data-ttu-id="05454-129">記錄可能無法正確記載並集中，因此無法補救效能問題。</span><span class="sxs-lookup"><span data-stu-id="05454-129">Logging might not be properly recorded and centralized to allow for remediation of performance issues.</span></span>
- <span data-ttu-id="05454-130">復原原則可能會失敗，或是執行時間超出預期。</span><span class="sxs-lookup"><span data-stu-id="05454-130">Recovery policies may fail or take longer than expected.</span></span>
- <span data-ttu-id="05454-131">不一致的部署程序可能導致安全性漏洞，並造成資料外洩或中斷。</span><span class="sxs-lookup"><span data-stu-id="05454-131">Inconsistent deployment processes might result in security gaps that could lead to data leaks or interruptions.</span></span>
- <span data-ttu-id="05454-132">設定漂移或遺失修補程式可能會導致非預期的安全性漏洞，並造成資料外洩或中斷。</span><span class="sxs-lookup"><span data-stu-id="05454-132">Configuration drift or missed patches might result in unintended security gaps that could lead to data leaks or interruptions.</span></span>
- <span data-ttu-id="05454-133">設定可能不會強制執行所定義的 SLA 需求或已認可的復原需求。</span><span class="sxs-lookup"><span data-stu-id="05454-133">Configuration might not enforce the requirements of defined SLAs or committed recovery requirements.</span></span>
- <span data-ttu-id="05454-134">已部署的作業系統或應用程式可能無法符合作業系統和應用程式強化需求。</span><span class="sxs-lookup"><span data-stu-id="05454-134">Deployed operating systems or applications might not meet OS and application hardening requirements.</span></span>
- <span data-ttu-id="05454-135">有多個小組在雲端中工作時，會有不一致的風險。</span><span class="sxs-lookup"><span data-stu-id="05454-135">There is a risk of inconsistency due to multiple teams working in the cloud.</span></span>

## <a name="evolution-of-the-policy-statements"></a><span data-ttu-id="05454-136">原則聲明的演進</span><span class="sxs-lookup"><span data-stu-id="05454-136">Evolution of the policy statements</span></span>

<span data-ttu-id="05454-137">下列原則變更有助於降低新風險和指導實作。</span><span class="sxs-lookup"><span data-stu-id="05454-137">The following changes to policy will help mitigate the new risks and guide implementation.</span></span> <span data-ttu-id="05454-138">清單看起來很長，但採用這些原則可能比看起來的簡單。</span><span class="sxs-lookup"><span data-stu-id="05454-138">The list looks long, but the adoption of these policies may be easier than it would appear.</span></span>

1. <span data-ttu-id="05454-139">所有已部署的資產必須依據重要性和資料類別來分類。</span><span class="sxs-lookup"><span data-stu-id="05454-139">All deployed assets must be categorized by criticality and data classification.</span></span> <span data-ttu-id="05454-140">分類會由雲端治理小組和應用程式擁有者進行檢閱，然後再部署到雲端。</span><span class="sxs-lookup"><span data-stu-id="05454-140">Classifications are to be reviewed by the Cloud Governance team and the application owner before deployment to the cloud.</span></span>
2. <span data-ttu-id="05454-141">包含任務關鍵性應用程式的子網路必須受到防火牆解決方案的保護，以偵測入侵及回應攻擊。</span><span class="sxs-lookup"><span data-stu-id="05454-141">Subnets containing mission-critical applications must be protected by a firewall solution capable of detecting intrusions and responding to attacks.</span></span>
3. <span data-ttu-id="05454-142">治理工具必須稽核並強制執行安全性基準小組所定義的網路設定需求。</span><span class="sxs-lookup"><span data-stu-id="05454-142">Governance tooling must audit and enforce network configuration requirements defined by the Security Baseline team.</span></span>
4. <span data-ttu-id="05454-143">治理工具必須確認所有與任務關鍵性應用程式或受保護資料相關的資產都會受到監視，以了解資源損耗與最佳化的情形。</span><span class="sxs-lookup"><span data-stu-id="05454-143">Governance tooling must validate that all assets related to mission-critical applications or protected data are included in monitoring for resource depletion and optimization.</span></span>
5. <span data-ttu-id="05454-144">治理工具必須確認會針對所有任務關鍵性應用程式或受保護的資料，收集適當層級的記錄資料。</span><span class="sxs-lookup"><span data-stu-id="05454-144">Governance tooling must validate that the appropriate level of logging data is being collected for all mission-critical applications or protected data.</span></span>
6. <span data-ttu-id="05454-145">治理程序必須確認任務關鍵性應用程式和受保護資料的備份、復原和 SLA 遵循皆正確實作。</span><span class="sxs-lookup"><span data-stu-id="05454-145">Governance process must validate that backup, recovery, and SLA adherence are properly implemented for mission-critical applications and protected data.</span></span>
7. <span data-ttu-id="05454-146">治理工具必須限制只對已核准的映像進行虛擬機器部署。</span><span class="sxs-lookup"><span data-stu-id="05454-146">Governance tooling must limit virtual machine deployment to approved images only.</span></span>
8. <span data-ttu-id="05454-147">治理工具必須強制**避免**在支援任務關鍵性應用程式的所有已部署資產上進行自動更新。</span><span class="sxs-lookup"><span data-stu-id="05454-147">Governance tooling must enforce that automatic updates are **prevented** on all deployed assets that support mission-critical applications.</span></span> <span data-ttu-id="05454-148">必須與作業管理小組一起檢閱違規事件，並根據作業原則來進行修復。</span><span class="sxs-lookup"><span data-stu-id="05454-148">Violations must be reviewed with operational management teams and remediated in accordance with operations policies.</span></span> <span data-ttu-id="05454-149">不會自動更新之資產必須包含在 IT 部門所負責的處理程序中。</span><span class="sxs-lookup"><span data-stu-id="05454-149">Assets that are not automatically updated must be included in processes owned by IT operations.</span></span>
9. <span data-ttu-id="05454-150">治理工具必須確認與成本、重要性、SLA、應用程式及資料類別相關的標記。</span><span class="sxs-lookup"><span data-stu-id="05454-150">Governance tooling must validate tagging related to cost, criticality, SLA, application, and data classification.</span></span> <span data-ttu-id="05454-151">所有值必須符合由雲端治理小組管理的預先定義值。</span><span class="sxs-lookup"><span data-stu-id="05454-151">All values must align to predefined values managed by the Cloud Governance team.</span></span>
10. <span data-ttu-id="05454-152">治理程序必須包含部署期間和一般週期的稽核，以確保所有資產之間的一致性。</span><span class="sxs-lookup"><span data-stu-id="05454-152">Governance processes must include audits at the point of deployment and at regular cycles to ensure consistency across all assets.</span></span>
11. <span data-ttu-id="05454-153">安全性小組應定期檢閱可能影響雲端部署的趨勢與攻擊，以更新雲端中使用的安全性基準工具。</span><span class="sxs-lookup"><span data-stu-id="05454-153">Trends and exploits that could affect cloud deployments should be reviewed regularly by the security team to provide updates to Security Baseline tooling used in the cloud.</span></span>
12. <span data-ttu-id="05454-154">在發行到生產環境之前，必須將所有任務關鍵性應用程式和受保護資料新增至指定的作業監視解決方案。</span><span class="sxs-lookup"><span data-stu-id="05454-154">Prior to release into production, all mission-critical applications and protected data must be added to the designated operational monitoring solution.</span></span> <span data-ttu-id="05454-155">如果所選取的 IT 作業工具找不到某些資產，則這些資產就無法發行為生產用的資產。</span><span class="sxs-lookup"><span data-stu-id="05454-155">Assets that cannot be discovered by the chosen IT operations tooling cannot be released for production use.</span></span> <span data-ttu-id="05454-156">為了讓資產變為可搜尋所做的變更，也必須對相關部署程序執行，如此才能確保在未來的部署中能探索到該資產。</span><span class="sxs-lookup"><span data-stu-id="05454-156">Any changes required to make the assets discoverable must be made to the relevant deployment processes to ensure assets will be discoverable in future deployments.</span></span>
13. <span data-ttu-id="05454-157">搜尋後，作業管理小組將驗證資產規模，以驗證資產是否符合效能需求。</span><span class="sxs-lookup"><span data-stu-id="05454-157">Upon discovery, asset sizing is to be validated by operational management teams to validate that the asset meets performance requirements.</span></span>
14. <span data-ttu-id="05454-158">部署工具必須由雲端治理小組核准，以確保能持續治理已部署的資產。</span><span class="sxs-lookup"><span data-stu-id="05454-158">Deployment tooling must be approved by the Cloud Governance team to ensure ongoing governance of deployed assets.</span></span>
15. <span data-ttu-id="05454-159">部署指令碼必須在雲端治理小組可存取的中央存放庫中進行維護，以便定進行定期檢閱和稽核。</span><span class="sxs-lookup"><span data-stu-id="05454-159">Deployment scripts must be maintained in central repository accessible by the Cloud Governance team for periodic review and auditing.</span></span>
16. <span data-ttu-id="05454-160">治理檢閱程序必須確認部署的資產已根據 SLA 及復原需求進行正確設定。</span><span class="sxs-lookup"><span data-stu-id="05454-160">Governance review processes must validate that deployed assets are properly configured in alignment with SLA and recovery requirements.</span></span>

## <a name="evolution-of-the-best-practices"></a><span data-ttu-id="05454-161">最佳做法的演進</span><span class="sxs-lookup"><span data-stu-id="05454-161">Evolution of the best practices</span></span>

<span data-ttu-id="05454-162">本文的這一節將發展治理 MVP 設計，以包含新的 Azure 原則和實作 Azure 成本管理。</span><span class="sxs-lookup"><span data-stu-id="05454-162">This section of the article will evolve the governance MVP design to include new Azure policies and an implementation of Azure Cost Management.</span></span> <span data-ttu-id="05454-163">這兩個設計變更將共同實現新的公司原則聲明。</span><span class="sxs-lookup"><span data-stu-id="05454-163">Together, these two design changes will fulfill the new corporate policy statements.</span></span>

<span data-ttu-id="05454-164">根據這個虛構範例的體驗，假設受保護資料的演進已經發生。</span><span class="sxs-lookup"><span data-stu-id="05454-164">Following the experience of this fictional example, it is assumed that the Protected Data evolution has already happened.</span></span> <span data-ttu-id="05454-165">在這個最佳做法的基礎上，下列內容將新增作業監控需求，為任務關鍵型應用程式準備訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="05454-165">Building on that best practice, the following will add operational monitoring requirements, readying a subscription for mission-critical applications.</span></span>

<span data-ttu-id="05454-166">**企業 IT 訂用帳戶**：將下列內容新增到作為中樞的企業 IT 訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="05454-166">**Corporate IT Subscription**: Add the following to the Corporate IT subscription, which acts as a hub.</span></span>

1. <span data-ttu-id="05454-167">作為外部相依性，雲端作業小組需要定義作業監控工具、商務連續性/災害復原 (BCDR) 工具和自動補救工具。</span><span class="sxs-lookup"><span data-stu-id="05454-167">As an external dependency, the Cloud Operations team will need to define operational monitoring tooling, Business Continuity/Disaster Recovery (BCDR) tooling and automated remediation tooling.</span></span> <span data-ttu-id="05454-168">然後，雲端治理小組即可支援必要的探索流程。</span><span class="sxs-lookup"><span data-stu-id="05454-168">The Cloud Governance team can then support necessary discovery processes.</span></span>
    1. <span data-ttu-id="05454-169">在此使用案例中，雲端作業小組會選擇 Azure 監視器作為監視任務關鍵性應用程式的主要工具。</span><span class="sxs-lookup"><span data-stu-id="05454-169">In this use case, the Cloud Operations team chose Azure Monitor as the primary tool for monitoring mission-critical applications.</span></span>
    2. <span data-ttu-id="05454-170">該小組也選擇 Azure Site Recovery 作為主要的 BCDR 工具。</span><span class="sxs-lookup"><span data-stu-id="05454-170">The team also chose Azure Site Recovery as the primary BCDR tooling.</span></span>
2. <span data-ttu-id="05454-171">Azure Site Recovery 實作</span><span class="sxs-lookup"><span data-stu-id="05454-171">Azure Site Recovery implementation</span></span>
    1. <span data-ttu-id="05454-172">定義及部署用於備份和復原程序的 Azure Vault</span><span class="sxs-lookup"><span data-stu-id="05454-172">Define and deploy Azure Vault for backup and recovery processes</span></span>
    2. <span data-ttu-id="05454-173">建立 Azure Resource Manager 範本以在每個訂用帳戶中建立保存庫</span><span class="sxs-lookup"><span data-stu-id="05454-173">Create an Azure Resource Management template for creation of a vault in each subscription</span></span>
3. <span data-ttu-id="05454-174">Azure 監視器實作</span><span class="sxs-lookup"><span data-stu-id="05454-174">Azure Monitor implementation</span></span>
    1. <span data-ttu-id="05454-175">識別任務關鍵性的訂用帳戶後，可使用 PowerShell 建立分析工作區。</span><span class="sxs-lookup"><span data-stu-id="05454-175">Once a mission-critical subscription is identified, a log analytics workspace can be created using PowerShell.</span></span> <span data-ttu-id="05454-176">這是一個預先部署程序。</span><span class="sxs-lookup"><span data-stu-id="05454-176">This is a pre-deployment process.</span></span>

<span data-ttu-id="05454-177">**個別雲端採用訂用帳戶**：下列內容將確保監控解決方案可以探索每個訂用帳戶，並準備包含在 BCDR 做法中。</span><span class="sxs-lookup"><span data-stu-id="05454-177">**Individual cloud adoption subscription**: The following will ensure that each subscription is discoverable by the monitoring solution and ready to be included in BCDR practices.</span></span>

1. <span data-ttu-id="05454-178">任務關鍵型節點的 Azure 原則</span><span class="sxs-lookup"><span data-stu-id="05454-178">Azure Policy for mission-critical nodes</span></span>
    1. <span data-ttu-id="05454-179">僅稽核和強制使用標準角色。</span><span class="sxs-lookup"><span data-stu-id="05454-179">Audit and enforce use of standard roles only.</span></span>
    2. <span data-ttu-id="05454-180">稽核和強制執行所有儲存體帳戶的加密。</span><span class="sxs-lookup"><span data-stu-id="05454-180">Audit and enforce application of encryption for all storage accounts.</span></span>
    3. <span data-ttu-id="05454-181">稽核並強制對每個網路介面使用已核准的網路子網路和 VNet。</span><span class="sxs-lookup"><span data-stu-id="05454-181">Audit and enforce use of approved network subnet and VNet per network interface.</span></span>
    4. <span data-ttu-id="05454-182">稽核並強制執行使用者定義路由表的限制。</span><span class="sxs-lookup"><span data-stu-id="05454-182">Audit and enforce the limitation of user-defined routing tables.</span></span>
    5. <span data-ttu-id="05454-183">稽核和強制 Windows 和 Linux 虛擬機器的 Log Analytics 代理程式部署。</span><span class="sxs-lookup"><span data-stu-id="05454-183">Audit and enforce the deployment of Log Analytics agents for Windows and Linux virtual machines.</span></span>
2. <span data-ttu-id="05454-184">Azure 藍圖</span><span class="sxs-lookup"><span data-stu-id="05454-184">Azure blueprint</span></span>
    1. <span data-ttu-id="05454-185">建立名為 `mission-critical-workloads-and-protected-data` 的藍圖。</span><span class="sxs-lookup"><span data-stu-id="05454-185">Create a blueprint named `mission-critical-workloads-and-protected-data`.</span></span> <span data-ttu-id="05454-186">除了受保護的資料藍圖之外，此藍圖也將運用資產。</span><span class="sxs-lookup"><span data-stu-id="05454-186">This blueprint will apply assets in addition to the protected data blueprint.</span></span>
    2. <span data-ttu-id="05454-187">將新的 Azure 原則新增到藍圖中。</span><span class="sxs-lookup"><span data-stu-id="05454-187">Add the new Azure policies to the blueprint.</span></span>
    3. <span data-ttu-id="05454-188">將藍圖運用於預期裝載任務關鍵型應用程式的任何訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="05454-188">Apply the blueprint to any subscription that is expected to host a mission-critical application.</span></span>

## <a name="conclusion"></a><span data-ttu-id="05454-189">結論</span><span class="sxs-lookup"><span data-stu-id="05454-189">Conclusion</span></span>

<span data-ttu-id="05454-190">對治理 MVP 新增這些流程和變更，有助於降低與資源治理建立關聯的許多風險。</span><span class="sxs-lookup"><span data-stu-id="05454-190">Adding these processes and changes to the governance MVP helps mitigate many of the risks associated with resource governance.</span></span> <span data-ttu-id="05454-191">同時還會新增可加強雲端感知作業的復原、大小調整及監視的必要控制項。</span><span class="sxs-lookup"><span data-stu-id="05454-191">Together, they add the recovery, sizing, and monitoring controls necessary to empower cloud-aware operations.</span></span>

## <a name="next-steps"></a><span data-ttu-id="05454-192">後續步驟</span><span class="sxs-lookup"><span data-stu-id="05454-192">Next steps</span></span>

<span data-ttu-id="05454-193">隨著雲端採用持續演進並提供額外的商業價值，風險和雲端治理需求也需要與時俱進。</span><span class="sxs-lookup"><span data-stu-id="05454-193">As cloud adoption continues to evolve and deliver additional business value, the risks and cloud governance needs will also evolve.</span></span> <span data-ttu-id="05454-194">對此程序中的虛構公司而言，下一個觸發點是將超過 1,000 個資產部署到雲端的時候，或每月支出超過 $10,000 美元的時候。</span><span class="sxs-lookup"><span data-stu-id="05454-194">For the fictional company in this journey, the next trigger is when the scale of deployment exceeds 1,000 assets to the cloud or monthly spending exceeds $10,000 USD per month.</span></span> <span data-ttu-id="05454-195">此時，雲端治理小組會新增成本管理控制項。</span><span class="sxs-lookup"><span data-stu-id="05454-195">At this point, the Cloud Governance team adds Cost Management controls.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="05454-196">成本管理演進</span><span class="sxs-lookup"><span data-stu-id="05454-196">Cost Management evolution</span></span>](./cost-management-evolution.md)
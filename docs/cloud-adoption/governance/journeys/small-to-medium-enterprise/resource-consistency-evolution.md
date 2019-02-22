---
title: 'CAF：中小型企業 - 資源一致性演進 '
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 說明：中小型企業 - 資源一致性演進
author: BrianBlanchard
ms.openlocfilehash: 402bb3ff4182123cdc8825522f965f7cf8637980
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900951"
---
# <a name="small-to-medium-enterprise-resource-consistency-evolution"></a><span data-ttu-id="2974b-103">中小型企業：資源一致性演進</span><span class="sxs-lookup"><span data-stu-id="2974b-103">Small-to-medium enterprise: Resource Consistency evolution</span></span>

<span data-ttu-id="2974b-104">本文將從新增資源一致性控制項來支援任務關鍵性應用程式這一點上發展敘述。</span><span class="sxs-lookup"><span data-stu-id="2974b-104">This article evolves the narrative by adding Resource Consistency controls to support mission-critical apps.</span></span>

## <a name="evolution-of-the-narrative"></a><span data-ttu-id="2974b-105">敘述的演進</span><span class="sxs-lookup"><span data-stu-id="2974b-105">Evolution of the narrative</span></span>

<span data-ttu-id="2974b-106">新的客戶體驗、新的預測工具和已移轉的基礎結構會繼續進行。</span><span class="sxs-lookup"><span data-stu-id="2974b-106">New customer experiences, new prediction tools, and migrated infrastructure continue to progress.</span></span> <span data-ttu-id="2974b-107">企業現在已準備開始在產能上使用這些資產。</span><span class="sxs-lookup"><span data-stu-id="2974b-107">The business is now ready to begin using those assets in a production capacity.</span></span>

### <a name="evolution-of-the-current-state"></a><span data-ttu-id="2974b-108">目前狀態的演進</span><span class="sxs-lookup"><span data-stu-id="2974b-108">Evolution of the current state</span></span>

<span data-ttu-id="2974b-109">在此敘述的上一個階段中，應用程式開發和 BI 小組幾乎已準備好將客戶和財務資料整合到生產工作負載。</span><span class="sxs-lookup"><span data-stu-id="2974b-109">In the previous phase of this narrative, the application development and BI teams were nearly ready to integrate customer and financial data into production workloads.</span></span> <span data-ttu-id="2974b-110">IT 小組已經開始淘汰 DR 資料中心。</span><span class="sxs-lookup"><span data-stu-id="2974b-110">The IT team was in the process of retiring the DR datacenter.</span></span>

<span data-ttu-id="2974b-111">從那時起，某些將會影響治理的事項已經改變：</span><span class="sxs-lookup"><span data-stu-id="2974b-111">Since then, some things have changed that will affect governance:</span></span>

- <span data-ttu-id="2974b-112">IT 已 100% 淘汰 DR 資料中心，且進度超前。</span><span class="sxs-lookup"><span data-stu-id="2974b-112">IT has retired 100% of the DR datacenter, ahead of schedule.</span></span> <span data-ttu-id="2974b-113">在過程中，生產資料中心的大量資產會識別為雲端移轉候選項目。</span><span class="sxs-lookup"><span data-stu-id="2974b-113">In the process, a number of assets in the Production datacenter were identified as cloud migration candidates.</span></span>
- <span data-ttu-id="2974b-114">應用程式開發小組現在已準備好使用生產環境流量。</span><span class="sxs-lookup"><span data-stu-id="2974b-114">The application development teams are now ready for production traffic.</span></span>
- <span data-ttu-id="2974b-115">BI 小組已準備好將預測和深入解析回饋至生產資料中心內的作業系統。</span><span class="sxs-lookup"><span data-stu-id="2974b-115">The BI team is ready to feed predictions and insights back into operation systems in the Production datacenter.</span></span>

### <a name="evolution-of-the-future-state"></a><span data-ttu-id="2974b-116">未來狀態的演進</span><span class="sxs-lookup"><span data-stu-id="2974b-116">Evolution of the future state</span></span>

<span data-ttu-id="2974b-117">在生產業務程序中使用 Azure 部署之前，必須有成熟的雲端作業。</span><span class="sxs-lookup"><span data-stu-id="2974b-117">Before using Azure deployments in production business processes, cloud operations must mature.</span></span> <span data-ttu-id="2974b-118">必須搭配其他治理演進，才能確保資產可正確運作。</span><span class="sxs-lookup"><span data-stu-id="2974b-118">In conjunction, an additional governance evolution is required to ensure assets can be operated properly.</span></span>

<span data-ttu-id="2974b-119">目前和未來狀態的變更會產生新風險，需要新的原則聲明。</span><span class="sxs-lookup"><span data-stu-id="2974b-119">The changes to current and future state expose new risks that will require new policy statements.</span></span>

## <a name="evolution-of-tangible-risks"></a><span data-ttu-id="2974b-120">實質風險的演進</span><span class="sxs-lookup"><span data-stu-id="2974b-120">Evolution of tangible risks</span></span>

<span data-ttu-id="2974b-121">**業務中斷**：任何新平台都有使任務關鍵性業務程序中斷的固有風險。</span><span class="sxs-lookup"><span data-stu-id="2974b-121">**Business Interruption**: There is an inherent risk of any new platform causing interruptions to mission-critical business processes.</span></span> <span data-ttu-id="2974b-122">IT 營運小組和執行各種雲端採用的小組都相對缺乏雲端作業的相關經驗。</span><span class="sxs-lookup"><span data-stu-id="2974b-122">The IT Operations team and the teams executing on various cloud adoptions are relatively inexperienced with cloud operations.</span></span> <span data-ttu-id="2974b-123">這會增加業務中斷的風險，而且必須降低風險及進行治理。</span><span class="sxs-lookup"><span data-stu-id="2974b-123">This increases the risk of interruption and must be mitigated and governed.</span></span>

<span data-ttu-id="2974b-124">此業務風險會延伸成大量技術風險：</span><span class="sxs-lookup"><span data-stu-id="2974b-124">This business risk can be expanded into a number of technical risks:</span></span>

- <span data-ttu-id="2974b-125">外部入侵或拒絕服務攻擊可能會導致業務中斷</span><span class="sxs-lookup"><span data-stu-id="2974b-125">External intrusion or denial of service attacks might cause a business interruption</span></span>
- <span data-ttu-id="2974b-126">無法正確探索到任務關鍵性的資產，因此可能無法正確運作</span><span class="sxs-lookup"><span data-stu-id="2974b-126">Mission-critical assets may not be properly discovered, and therefore might not be properly operated</span></span>
- <span data-ttu-id="2974b-127">現有的作業管理程序可能不支援未探索到或標記錯誤的資產。</span><span class="sxs-lookup"><span data-stu-id="2974b-127">Undiscovered or mislabeled assets might not be supported by existing operational management processes.</span></span>
- <span data-ttu-id="2974b-128">已部署的資產設定可能不符合預期的效能</span><span class="sxs-lookup"><span data-stu-id="2974b-128">The configuration of deployed assets may not meet performance expectations</span></span>
- <span data-ttu-id="2974b-129">記錄可能無法正確記載並集中，因此無法補救效能問題。</span><span class="sxs-lookup"><span data-stu-id="2974b-129">Logging might not be properly recorded and centralized to allow for remediation of performance issues.</span></span>
- <span data-ttu-id="2974b-130">復原原則可能會失敗，或是執行時間超出預期。</span><span class="sxs-lookup"><span data-stu-id="2974b-130">Recovery policies may fail or take longer than expected.</span></span>
- <span data-ttu-id="2974b-131">不一致的部署程序可能導致安全性漏洞，並造成資料外洩或中斷。</span><span class="sxs-lookup"><span data-stu-id="2974b-131">Inconsistent deployment processes might result in security gaps that could lead to data leaks or interruptions.</span></span>
- <span data-ttu-id="2974b-132">設定漂移或遺失修補程式可能會導致非預期的安全性漏洞，並造成資料外洩或中斷。</span><span class="sxs-lookup"><span data-stu-id="2974b-132">Configuration drift or missed patches might result in unintended security gaps that could lead to data leaks or interruptions.</span></span>
- <span data-ttu-id="2974b-133">設定可能不會強制執行所定義的 SLA 需求或已認可的復原要求。</span><span class="sxs-lookup"><span data-stu-id="2974b-133">Configuration might not enforce the requirements of defined SLAs or committed recovery requirements.</span></span>
- <span data-ttu-id="2974b-134">已部署的作業系統或應用程式可能無法符合強化需求</span><span class="sxs-lookup"><span data-stu-id="2974b-134">Deployed operating systems or applications might fail to meet hardening requirements</span></span>
- <span data-ttu-id="2974b-135">有很多小組在雲端中工作時，就會有不一致的風險。</span><span class="sxs-lookup"><span data-stu-id="2974b-135">With so many teams working in the cloud, there is a risk of inconsistency.</span></span>

## <a name="evolution-of-the-policy-statements"></a><span data-ttu-id="2974b-136">原則聲明的演進</span><span class="sxs-lookup"><span data-stu-id="2974b-136">Evolution of the policy statements</span></span>

<span data-ttu-id="2974b-137">下列原則變更有助於減緩新風險和指導實作。</span><span class="sxs-lookup"><span data-stu-id="2974b-137">The following changes to policy will help mitigate the new risks and guide implementation.</span></span> <span data-ttu-id="2974b-138">清單看起來很長，但採用這些原則可能比看起來的簡單。</span><span class="sxs-lookup"><span data-stu-id="2974b-138">The list looks long, but adopting these policies may be easier than it appears.</span></span>

1. <span data-ttu-id="2974b-139">所有已部署的資產必須依據重要性和資料類別來分類。</span><span class="sxs-lookup"><span data-stu-id="2974b-139">All deployed assets must be categorized by criticality and data classification.</span></span> <span data-ttu-id="2974b-140">分類會由雲端治理小組和應用程式擁有者進行檢閱，然後再部署到雲端</span><span class="sxs-lookup"><span data-stu-id="2974b-140">Classifications are to be reviewed by the Cloud Governance team and the application owner before deployment to the cloud</span></span>
2. <span data-ttu-id="2974b-141">包含任務關鍵性應用程式的子網路必須受到防火牆解決方案的保護，以偵測入侵及回應攻擊。</span><span class="sxs-lookup"><span data-stu-id="2974b-141">Subnets containing mission-critical applications must be protected by a firewall solution capable of detecting intrusions and responding to attacks.</span></span>
3. <span data-ttu-id="2974b-142">治理工具必須稽核並強制執行安全性管理小組所定義的網路設定需求</span><span class="sxs-lookup"><span data-stu-id="2974b-142">Governance tooling must audit and enforce network configuration requirements defined by the Security Management team</span></span>
4. <span data-ttu-id="2974b-143">治理工具必須確認所有與任務關鍵性應用程式或受保護資料相關的資產都會受到監視，以了解資源損耗與最佳化的情形。</span><span class="sxs-lookup"><span data-stu-id="2974b-143">Governance tooling must validate that all assets related to mission-critical apps or protected data are included in monitoring for resource depletion and optimization.</span></span>
5. <span data-ttu-id="2974b-144">治理工具必須確認會針對所有任務關鍵性應用程式或受保護的資料，收集適當層級的記錄資料。</span><span class="sxs-lookup"><span data-stu-id="2974b-144">Governance tooling must validate that the appropriate level of logging data is being collected for all mission-critical applications or protected data.</span></span>
6. <span data-ttu-id="2974b-145">治理程序必須確認任務關鍵性應用程式和受保護資料的備份、復原和 SLA 遵循皆正確實作。</span><span class="sxs-lookup"><span data-stu-id="2974b-145">Governance process must validate that backup, recovery, and SLA adherence are properly implemented for mission-critical applications and protected data.</span></span>
7. <span data-ttu-id="2974b-146">治理工具必須限制只對已核准的映像進行虛擬機器部署。</span><span class="sxs-lookup"><span data-stu-id="2974b-146">Governance tooling must limit virtual machine deployments to approved images only.</span></span>
8. <span data-ttu-id="2974b-147">治理工具必須強制避免在支援任務關鍵性應用程式的所有已部署資產上進行自動更新。</span><span class="sxs-lookup"><span data-stu-id="2974b-147">Governance tooling must enforce that automatic updates are prevented on all deployed assets that support mission-critical applications.</span></span> <span data-ttu-id="2974b-148">必須與作業管理小組一起檢閱違規事件，並根據作業原則來進行修復。</span><span class="sxs-lookup"><span data-stu-id="2974b-148">Violations must be reviewed with operational management teams and remediated in accordance with operations policies.</span></span> <span data-ttu-id="2974b-149">不會自動更新的資產必須包含在 IT 部門所負責的處理程序中。</span><span class="sxs-lookup"><span data-stu-id="2974b-149">Assets that are not automatically updated must be included in processes owned by IT Operations.</span></span>
9. <span data-ttu-id="2974b-150">治理工具必須確認與成本、重要性、SLA、應用程式及資料類別相關的標記。</span><span class="sxs-lookup"><span data-stu-id="2974b-150">Governance tooling must validate tagging related to cost, criticality, SLA, application, and data classification.</span></span> <span data-ttu-id="2974b-151">所有值必須符合由治理小組管理的預先定義值。</span><span class="sxs-lookup"><span data-stu-id="2974b-151">All values must align to predefined values managed by the governance team.</span></span>
10. <span data-ttu-id="2974b-152">治理程序必須包含部署期間和一般週期的稽核，以確保所有資產之間的一致性。</span><span class="sxs-lookup"><span data-stu-id="2974b-152">Governance processes must include audits at the point of deployment and at regular cycles to ensure consistency across all assets.</span></span>
11. <span data-ttu-id="2974b-153">安全性小組應定期檢閱可能影響雲端部署的趨勢與攻擊，以更新雲端中使用的安全性管理工具。</span><span class="sxs-lookup"><span data-stu-id="2974b-153">Trends and exploits that could affect cloud deployments should be reviewed regularly by the Security team to provide updates to security management tooling used in the cloud.</span></span>
12. <span data-ttu-id="2974b-154">在發行到生產環境之前，必須將所有任務關鍵性應用程式和受保護的資料新增至指定的作業監視解決方案。</span><span class="sxs-lookup"><span data-stu-id="2974b-154">Before release into production, all mission-critical apps and protected data must be added to the designated operational monitoring solution.</span></span> <span data-ttu-id="2974b-155">如果所選取的 IT 作業工具找不到某些資產，則這些資產就無法發行為生產用的資產。</span><span class="sxs-lookup"><span data-stu-id="2974b-155">Assets that cannot be discovered by the chosen IT operations tooling, cannot be released for production use.</span></span> <span data-ttu-id="2974b-156">為了讓資產變得可探索所做的變更，也必須對相關部署程序執行，如此才能確保在未來的部署中能探索到該資產。</span><span class="sxs-lookup"><span data-stu-id="2974b-156">Any changes required to make the assets discoverable must be made to the relevant deployment processes to ensure assets will be discoverable in future deployments.</span></span>
13. <span data-ttu-id="2974b-157">發現資產時，作業管理小組會調整資產的大小，以確保資產符合效能需求</span><span class="sxs-lookup"><span data-stu-id="2974b-157">Upon discovery, operational management teams will size assets, to ensure that assets meet performance requirements</span></span>
14. <span data-ttu-id="2974b-158">部署工具必須由雲端治理小組核准，以確保能持續治理已部署的資產。</span><span class="sxs-lookup"><span data-stu-id="2974b-158">Deployment tooling must be approved by the Cloud Governance team to ensure ongoing governance of deployed assets.</span></span>
15. <span data-ttu-id="2974b-159">部署指令碼必須在雲端治理小組可存取的中央存放庫中進行維護，以便定進行定期檢閱和稽核。</span><span class="sxs-lookup"><span data-stu-id="2974b-159">Deployment scripts must be maintained in a central repository accessible by the Cloud Governance team for periodic review and auditing.</span></span>
16. <span data-ttu-id="2974b-160">治理檢閱程序必須確認部署的資產已根據 SLA 及復原需求進行正確的設定。</span><span class="sxs-lookup"><span data-stu-id="2974b-160">Governance review processes must validate that deployed assets are properly configured in alignment with SLA and recovery requirements.</span></span>

## <a name="evolution-of-the-best-practices"></a><span data-ttu-id="2974b-161">最佳做法的演進</span><span class="sxs-lookup"><span data-stu-id="2974b-161">Evolution of the best practices</span></span>

<span data-ttu-id="2974b-162">本文的這一節將推演治理 MVP 設計，以包含新的 Azure 原則和實作 Azure 成本管理。</span><span class="sxs-lookup"><span data-stu-id="2974b-162">This section of the article will evolve the governance MVP design to include new Azure policies and an implementation of Azure Cost Management.</span></span> <span data-ttu-id="2974b-163">這兩個設計變更將共同實現新的公司原則聲明。</span><span class="sxs-lookup"><span data-stu-id="2974b-163">Together, these two design changes will fulfill the new corporate policy statements.</span></span>

1. <span data-ttu-id="2974b-164">雲端作業小組會定義作業監視工具及自動補救工具。</span><span class="sxs-lookup"><span data-stu-id="2974b-164">The Cloud Operations team will define operational monitoring tooling and automated remediation tooling.</span></span> <span data-ttu-id="2974b-165">雲端治理小組將會支援這些探索程序。</span><span class="sxs-lookup"><span data-stu-id="2974b-165">The Cloud Governance team will support those discovery processes.</span></span> <span data-ttu-id="2974b-166">在此使用案例中，雲端作業小組會選擇 Azure 監視器作為監視任務關鍵性應用程式的主要工具。</span><span class="sxs-lookup"><span data-stu-id="2974b-166">In this use case, the Cloud Operations team chose Azure Monitor as the primary tool for monitoring mission-critical applications.</span></span>
2. <span data-ttu-id="2974b-167">在 Azure DevOps 中建立存放庫來存放所有相關的 Resource Manager 範本和指令碼式的組態，並為這些項目設定版本。</span><span class="sxs-lookup"><span data-stu-id="2974b-167">Create a repository in Azure DevOps to store and version all relevant Resource Manager templates and scripted configurations.</span></span>
3. <span data-ttu-id="2974b-168">Azure Vault 實作：</span><span class="sxs-lookup"><span data-stu-id="2974b-168">Azure Vault implementation:</span></span>
    1. <span data-ttu-id="2974b-169">定義及部署用於備份和復原程序的 Azure Vault。</span><span class="sxs-lookup"><span data-stu-id="2974b-169">Define and deploy Azure Vault for backup and recovery processes.</span></span>
    2. <span data-ttu-id="2974b-170">建立 Resource Manager 範本以在每個訂用帳戶中建立保存庫。</span><span class="sxs-lookup"><span data-stu-id="2974b-170">Create a Resource Manager template for creation of a vault in each subscription.</span></span>
4. <span data-ttu-id="2974b-171">更新所有訂用帳戶的 Azure 原則：</span><span class="sxs-lookup"><span data-stu-id="2974b-171">Update Azure Policy for all subscriptions:</span></span>
    1. <span data-ttu-id="2974b-172">在所有訂用帳戶上稽核並強制執行重要性和資料分類，以識別任何具有任務關鍵性資產的訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="2974b-172">Audit and enforce criticality and data classification across all subscriptions to identify any subscriptions with mission-critical assets.</span></span>
    2. <span data-ttu-id="2974b-173">稽核並限制使用已核准的映像。</span><span class="sxs-lookup"><span data-stu-id="2974b-173">Audit and enforce the use of approved images only.</span></span>
5. <span data-ttu-id="2974b-174">Azure 監視器實作：</span><span class="sxs-lookup"><span data-stu-id="2974b-174">Azure Monitor implementation:</span></span>
    1. <span data-ttu-id="2974b-175">找到任務關鍵性的訂用帳戶後，使用 PowerShell 建立 Azure 監視器工作區。</span><span class="sxs-lookup"><span data-stu-id="2974b-175">Once a mission-critical subscription is identified, create an Azure Monitor workspace using PowerShell.</span></span> <span data-ttu-id="2974b-176">這是一個預先部署程序。</span><span class="sxs-lookup"><span data-stu-id="2974b-176">This is a pre-deployment process.</span></span>
    2. <span data-ttu-id="2974b-177">在部署測試期間，雲端作業小組會部署所需的代理程式並測試探索。</span><span class="sxs-lookup"><span data-stu-id="2974b-177">During deployment testing, the Cloud Operations team deploys the necessary agents and tests discovery.</span></span>
6. <span data-ttu-id="2974b-178">針對包含任務關鍵性應用程式的所有訂用帳戶更新 Azure 原則。</span><span class="sxs-lookup"><span data-stu-id="2974b-178">Update Azure Policy for all subscriptions that contain mission-critical applications.</span></span>
    1. <span data-ttu-id="2974b-179">稽核並強制執行使用 NSG 連到所有 NIC 和子網路的應用程式。</span><span class="sxs-lookup"><span data-stu-id="2974b-179">Audit and enforce the application of an NSG to all NICs and subnets.</span></span> <span data-ttu-id="2974b-180">網路和 IT 安全性會定義 NSG。</span><span class="sxs-lookup"><span data-stu-id="2974b-180">Networking and IT Security define the NSG.</span></span>
    2. <span data-ttu-id="2974b-181">稽核並強制對每個網路介面使用已核准的網路子網路和 VNet。</span><span class="sxs-lookup"><span data-stu-id="2974b-181">Audit and enforce the use of approved network subnets and VNets for each network interface.</span></span>
    3. <span data-ttu-id="2974b-182">稽核並強制執行使用者定義路由表的限制。</span><span class="sxs-lookup"><span data-stu-id="2974b-182">Audit and enforce the limitation of user-defined routing tables.</span></span>
    4. <span data-ttu-id="2974b-183">稽核並強制對所有虛擬機器部署 Azure 監視器代理程式。</span><span class="sxs-lookup"><span data-stu-id="2974b-183">Audit and enforce deployment of Azure Monitor agents for all virtual machines.</span></span>
    5. <span data-ttu-id="2974b-184">稽核並強制訂用帳戶中必須有 Azure Vault。</span><span class="sxs-lookup"><span data-stu-id="2974b-184">Audit and enforce that Azure Vault exists in the subscription.</span></span>
7. <span data-ttu-id="2974b-185">防火牆組態：</span><span class="sxs-lookup"><span data-stu-id="2974b-185">Firewall configuration:</span></span>
    1. <span data-ttu-id="2974b-186">識別符合安全性需求的 Azure 防火牆設定。</span><span class="sxs-lookup"><span data-stu-id="2974b-186">Identify a configuration of Azure Firewall that meets security requirements.</span></span> <span data-ttu-id="2974b-187">或者，識別與 Azure 相容的第三方應用裝置。</span><span class="sxs-lookup"><span data-stu-id="2974b-187">Alternatively, identify a third-party appliance that is compatible with Azure.</span></span>
    2. <span data-ttu-id="2974b-188">建立 Resource Manager 範本來部署具有必要設定的防火牆。</span><span class="sxs-lookup"><span data-stu-id="2974b-188">Create a Resource Manager template to deploy the firewall with required configurations.</span></span>
8. <span data-ttu-id="2974b-189">Azure 藍圖：</span><span class="sxs-lookup"><span data-stu-id="2974b-189">Azure blueprint:</span></span>
    1. <span data-ttu-id="2974b-190">建立名為 `protected-data` 的新 Azure 藍圖。</span><span class="sxs-lookup"><span data-stu-id="2974b-190">Create a new Azure blueprint named `protected-data`.</span></span>
    2. <span data-ttu-id="2974b-191">對藍圖新增防火牆和 Azure Vault 範本。</span><span class="sxs-lookup"><span data-stu-id="2974b-191">Add the firewall and Azure Vault templates to the blueprint.</span></span>
    3. <span data-ttu-id="2974b-192">為受保護資料的訂用帳戶新增原則。</span><span class="sxs-lookup"><span data-stu-id="2974b-192">Add the new policies for protected data subscriptions.</span></span>
    4. <span data-ttu-id="2974b-193">將藍圖發佈到任何要裝載任務關鍵性應用程式的管理群組。</span><span class="sxs-lookup"><span data-stu-id="2974b-193">Publish the blueprint to any management group intended to host mission-critical applications.</span></span>
    5. <span data-ttu-id="2974b-194">對每個受影響的訂用帳戶及現有藍圖套用新的藍圖。</span><span class="sxs-lookup"><span data-stu-id="2974b-194">Apply the new blueprint to each affected subscription as well as existing blueprints.</span></span>

## <a name="conclusion"></a><span data-ttu-id="2974b-195">結論</span><span class="sxs-lookup"><span data-stu-id="2974b-195">Conclusion</span></span>

<span data-ttu-id="2974b-196">對治理 MVP 新增的這些流程和變更，有助於降低與資源治理相關聯的許多風險。</span><span class="sxs-lookup"><span data-stu-id="2974b-196">These additional processes and changes to the governance MVP help mitigate many of the risks associated with resource governance.</span></span> <span data-ttu-id="2974b-197">同時還會新增可加強雲端感知作業的復原、大小調整及監視控制項。</span><span class="sxs-lookup"><span data-stu-id="2974b-197">Together they add recovery, sizing, and monitoring controls that empower cloud-aware operations.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2974b-198">後續步驟</span><span class="sxs-lookup"><span data-stu-id="2974b-198">Next steps</span></span>

<span data-ttu-id="2974b-199">隨著雲端採用持續演進，並提供額外的商業價值，風險和雲端治理需求也需要與時俱進。</span><span class="sxs-lookup"><span data-stu-id="2974b-199">As cloud adoption continues to evolve and deliver additional business value, risks and cloud governance needs will also evolve.</span></span> <span data-ttu-id="2974b-200">對此旅程中的虛構公司而言，下一個觸發點是將超過 100 個資產部署到雲端的時候，或每月支出超過 $1,000 美元的時候。</span><span class="sxs-lookup"><span data-stu-id="2974b-200">For the fictional company in this journey, the next trigger is when the scale of deployment exceeds 100 assets to the cloud or monthly spending exceeds $1,000 per month.</span></span> <span data-ttu-id="2974b-201">此時，雲端治理小組會新增成本管理控制項。</span><span class="sxs-lookup"><span data-stu-id="2974b-201">At this point, the Cloud Governance team adds Cost Management controls.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="2974b-202">成本管理演進</span><span class="sxs-lookup"><span data-stu-id="2974b-202">Cost Management evolution</span></span>](./cost-management-evolution.md)
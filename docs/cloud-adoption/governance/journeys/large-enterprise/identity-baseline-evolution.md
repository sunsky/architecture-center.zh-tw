---
title: CAF：大型企業 – 身分識別基準演進
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 大型企業 – 身分識別基準演進
author: BrianBlanchard
ms.openlocfilehash: efda14819bfbc70632dc9bb8b4c6c5aca96004c0
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900879"
---
# <a name="large-enterprise-identity-baseline-evolution"></a><span data-ttu-id="ac8ca-103">大型企業：身分識別基準演進</span><span class="sxs-lookup"><span data-stu-id="ac8ca-103">Large enterprise: Identity Baseline evolution</span></span>

<span data-ttu-id="ac8ca-104">本文藉由將身分識別基準控制新增至治理 MVP 來推演敘述。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-104">This article evolves the narrative by adding Identity Baseline controls to the governance MVP.</span></span>

## <a name="evolution-of-the-narrative"></a><span data-ttu-id="ac8ca-105">敘述的演進</span><span class="sxs-lookup"><span data-stu-id="ac8ca-105">Evolution of the narrative</span></span>

<span data-ttu-id="ac8ca-106">CFO 已核准將兩個資料中心移轉至雲端的商業論證。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-106">The business justification for the cloud migration of the two datacenters was approved by the CFO.</span></span> <span data-ttu-id="ac8ca-107">在研究技術可行性的期間，其找到幾個障礙：</span><span class="sxs-lookup"><span data-stu-id="ac8ca-107">During the technical feasibility study, several roadblocks were discovered:</span></span>

- <span data-ttu-id="ac8ca-108">受保護的資料及任務關鍵性應用程式佔了兩個資料中心 25% 的工作負載。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-108">Protected data and mission-critical applications represent 25% of the workloads in the two datacenters.</span></span> <span data-ttu-id="ac8ca-109">在將目前有關 PII 和任務關鍵性應用程式的治理原則現代化之前，這兩種工作負載都無法消除。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-109">Neither can be eliminated until the current governance policies regarding PII and mission-critical applications have been modernized.</span></span>
- <span data-ttu-id="ac8ca-110">在這兩個資料中心內，7% 的資產與雲端不相容。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-110">7% of the assets in those datacenters are not cloud-compatible.</span></span> <span data-ttu-id="ac8ca-111">其會先移至替代的資料中心，再終止資料中心的合約。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-111">They will be moved to an alternate datacenter before termination of the datacenter contract.</span></span>
- <span data-ttu-id="ac8ca-112">資料中心內有 15% 的資產 (750 個虛擬機器) 相依於舊式的驗證或第三方多重要素驗證。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-112">15% of the assets in the datacenter (750 virtual machines) have a dependency on legacy authentication or third-party multi-factor authentication.</span></span>
- <span data-ttu-id="ac8ca-113">連結現有資料中心與 Azure 的 VPN 連線未提供足夠的資料傳輸速度或延遲，因而無法在兩年內遷移大量資產，從而淘汰資料中心。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-113">The VPN connection that connects existing datacenters and Azure does not offer sufficient data transmission speeds or latency to migrate the volume of assets within the two-year timeline to retire the datacenter.</span></span>

<span data-ttu-id="ac8ca-114">前兩個障礙將會一起排除。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-114">The first two roadblocks are being mitigated in parallel.</span></span> <span data-ttu-id="ac8ca-115">本文會講述第三個和第四個障礙的解決方案。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-115">This article will address the resolution of the third and fourth roadblocks.</span></span>

### <a name="evolution-of-the-cloud-governance-team"></a><span data-ttu-id="ac8ca-116">雲端治理小組的演進</span><span class="sxs-lookup"><span data-stu-id="ac8ca-116">Evolution of the Cloud Governance team</span></span>

<span data-ttu-id="ac8ca-117">雲端治理小組不斷擴大。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-117">The Cloud Governance team is expanding.</span></span> <span data-ttu-id="ac8ca-118">由於需要另外支援身分識別管理，來自身分識別基準小組的系統管理員現在會參與每週一次的會議，好讓現有的小組成員了解有何改變。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-118">Given the need for additional support regarding identity management, a systems administrator from the Identity Baseline team now participates in a weekly meeting to keep the existing team members aware of changes.</span></span>

### <a name="evolution-of-the-current-state"></a><span data-ttu-id="ac8ca-119">目前狀態的演進</span><span class="sxs-lookup"><span data-stu-id="ac8ca-119">Evolution of the current state</span></span>

<span data-ttu-id="ac8ca-120">公司已核准 IT 小組進行 CIO 和 CFO 想要將兩個資料中心淘汰的計劃。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-120">The IT team has approval to move forward with the CIO and CFO's plans to retire two datacenters.</span></span> <span data-ttu-id="ac8ca-121">不過，IT 有所顧慮，因為這些資料中心內有 750 個 (15%) 的資產必須移到雲端以外的地方。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-121">However, IT is concerned that 750 (15%) of the assets in those datacenters will have to be moved somewhere other than the cloud.</span></span>

### <a name="evolution-of-the-future-state"></a><span data-ttu-id="ac8ca-122">未來狀態的演進</span><span class="sxs-lookup"><span data-stu-id="ac8ca-122">Evolution of the future state</span></span>

<span data-ttu-id="ac8ca-123">新的未來狀態計劃需要準備更強固的身分識別基準解決方案，以便遷移需要使用舊式驗證的 750 個虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-123">The new future state plans require a more robust Identity Baseline solution to migrate the 750 virtual machines with legacy authentication requirements.</span></span> <span data-ttu-id="ac8ca-124">除了這兩個資料中心外，其他資料中心內也有類似百分比的資產應該會受到這項挑戰所影響。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-124">Beyond these two datacenters, similar percentages of assets in other datacenters are expected to be affected by this challenge.</span></span>
<span data-ttu-id="ac8ca-125">未來的狀態現在也需要有從雲端提供者到公司的 MPLS/專線解決方案的連線。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-125">The future state now also requires a connection from the cloud provider to the company’s MPLS/leased-line solution.</span></span>

<span data-ttu-id="ac8ca-126">目前和未來狀態的變更會產生新風險，需要新的原則聲明。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-126">The changes to current and future state expose new risks that will require new policy statements.</span></span>

## <a name="evolution-of-tangible-risks"></a><span data-ttu-id="ac8ca-127">有形風險的演進</span><span class="sxs-lookup"><span data-stu-id="ac8ca-127">Evolution of tangible risks</span></span>

<span data-ttu-id="ac8ca-128">**移轉期間業務中斷**。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-128">**Business interruption during migration**.</span></span> <span data-ttu-id="ac8ca-129">移轉至雲端時，會產生受控而可以管理的具時效性風險。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-129">Migration to the cloud creates a controlled, time-bound risk that can be managed.</span></span> <span data-ttu-id="ac8ca-130">將老舊硬體移至世界上的其他地方，風險會更高。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-130">Moving aging hardware to another part of the world is much higher risk.</span></span> <span data-ttu-id="ac8ca-131">為了避免中斷業務運作，其需要緩解策略。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-131">A mitigation strategy is needed to avoid interruptions to business operations.</span></span>

<span data-ttu-id="ac8ca-132">**現有身分識別相依性**。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-132">**Existing identity dependencies**.</span></span> <span data-ttu-id="ac8ca-133">對現有驗證和身分識別服務的相依性可能會延遲或阻礙將某些工作負載移轉至雲端的工作。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-133">Dependencies on existing authentication and identity services may delay or prevent the migration of some workloads to the cloud.</span></span> <span data-ttu-id="ac8ca-134">若未能讓這兩個資料中心準時恢復上線，將會產生好幾百萬美元的資料中心租賃費用。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-134">Failure to return the two datacenters on time will incur millions of dollars in datacenter lease fees.</span></span>

<span data-ttu-id="ac8ca-135">此業務風險可能會延伸出少數技術風險：</span><span class="sxs-lookup"><span data-stu-id="ac8ca-135">This business risk can be expanded into a few technical risks:</span></span>

- <span data-ttu-id="ac8ca-136">舊式驗證可能無法在雲端中使用，而限制了某些應用程式的部署。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-136">Legacy authentication might not be available in the cloud, limiting deployment of some applications.</span></span>
- <span data-ttu-id="ac8ca-137">目前的第三方 MFA 解決方案可能無法在雲端中使用，而限制了某些應用程式的部署。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-137">The current third-party MFA solution might not be available in the cloud, limiting deployment of some applications.</span></span>
- <span data-ttu-id="ac8ca-138">不論是更換工具還是移動，都可能會產生中斷而增加成本。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-138">Retooling or moving either could create outages and add costs.</span></span>
- <span data-ttu-id="ac8ca-139">VPN 的速度和穩定性可能會妨礙移轉。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-139">The speed and stability of the VPN might impede migration.</span></span>
- <span data-ttu-id="ac8ca-140">進入雲端的流量可能會讓全球網路的其他部分產生安全性問題。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-140">Traffic coming into the cloud could cause security issues in other parts of the global network.</span></span>

## <a name="evolution-of-the-policy-statements"></a><span data-ttu-id="ac8ca-141">原則聲明的演進</span><span class="sxs-lookup"><span data-stu-id="ac8ca-141">Evolution of the policy statements</span></span>

<span data-ttu-id="ac8ca-142">下列原則變更有助於減緩新風險和指導實作。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-142">The following changes to policy will help mitigate the new risks and guide implementation.</span></span>

1. <span data-ttu-id="ac8ca-143">所選擇的雲端提供者必須提供可透過舊式方法進行驗證的途徑。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-143">The chosen cloud provider must offer a means of authenticating via legacy methods.</span></span>
2. <span data-ttu-id="ac8ca-144">所選擇的雲端提供者必須提供可使用目前的第三方 MFA 解決方案進行驗證的途徑。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-144">The chosen cloud provider must offer a means of authentication with the current third-party MFA solution.</span></span>
3. <span data-ttu-id="ac8ca-145">應該在雲端提供者與公司的電信提供者之間建立高速的私人連線，以將雲端提供者連線至全球的資料中心網路。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-145">A high-speed private connection should be established between the cloud provider and the company’s telco provider, connecting the cloud provider to the global network of datacenters.</span></span>
4. <span data-ttu-id="ac8ca-146">在建立好足夠的安全性需求之前，不得讓輸入的公用流量存取裝載在雲端的公司資產。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-146">Until sufficient security requirements are established, no inbound public traffic may access company assets hosted in the cloud.</span></span> <span data-ttu-id="ac8ca-147">所有連接埠都要封鎖位於全域 WAN 之外的來源。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-147">All ports are blocked from any source outside of the global WAN.</span></span>

## <a name="evolution-of-the-best-practices"></a><span data-ttu-id="ac8ca-148">最佳做法的演進</span><span class="sxs-lookup"><span data-stu-id="ac8ca-148">Evolution of the best practices</span></span>

<span data-ttu-id="ac8ca-149">治理 MVP 設計會演進為在虛擬機器上包含新的 Azure 原則和 Active Directory 實作。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-149">The governance MVP design evolves to include new Azure policies and an implementation of Active Directory on a virtual machine.</span></span> <span data-ttu-id="ac8ca-150">這兩個設計變更會共同實現新的公司原則聲明。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-150">Together, these two design changes fulfill the new corporate policy statements.</span></span>

<span data-ttu-id="ac8ca-151">以下是新的最佳做法：</span><span class="sxs-lookup"><span data-stu-id="ac8ca-151">Here are the new best practices:</span></span>

1. <span data-ttu-id="ac8ca-152">DMZ 藍圖：DMZ 的內部部署端應該設定為允許下列解決方案與內部部署的 Active Directory 伺服器進行通訊。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-152">DMZ blueprint: The on-premises side of the DMZ should be configured to allow communication between the following solution and the on-premises Active Directory servers.</span></span> <span data-ttu-id="ac8ca-153">這個最佳做法需要讓 DMZ 跨網路界限啟用 Active Directory Domain Services。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-153">This best practice requires a DMZ to enable Active Directory Domain Services across network boundaries.</span></span>
2. <span data-ttu-id="ac8ca-154">Azure Resource Manager 範本：</span><span class="sxs-lookup"><span data-stu-id="ac8ca-154">Azure Resource Manager templates:</span></span>
    1. <span data-ttu-id="ac8ca-155">定義 NSG 來封鎖外部流量以及將內部流量加入白名單。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-155">Define an NSG to block external traffic and whitelist internal traffic.</span></span>
    1. <span data-ttu-id="ac8ca-156">根據最佳映像將兩個 AD 虛擬機器部署成具有負載平衡功能的組合。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-156">Deploy two AD virtual machines in a load balanced pair based on a golden image.</span></span> <span data-ttu-id="ac8ca-157">在首次開機時，該映像會執行 PowerShell 指令碼，來加入網域並向網域服務註冊。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-157">On first boot, that image runs a PowerShell script to join the domain and register with domain services.</span></span> <span data-ttu-id="ac8ca-158">如需詳細資訊，請參閱[將 Active Directory Domain Services (AD DS) 擴充至 Azure](../../../../reference-architectures/identity/adds-extend-domain.md)。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-158">For more information, see [Extend Active Directory Domain Services (AD DS) to Azure](../../../../reference-architectures/identity/adds-extend-domain.md).</span></span>
3. <span data-ttu-id="ac8ca-159">Azure 原則：將 NSG 套用至所有資源。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-159">Azure Policy: Apply the NSG to all resources.</span></span>
4. <span data-ttu-id="ac8ca-160">Azure 藍圖</span><span class="sxs-lookup"><span data-stu-id="ac8ca-160">Azure blueprint</span></span>
    1. <span data-ttu-id="ac8ca-161">建立名為 `active-directory-virtual-machines` 的藍圖。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-161">Create a blueprint named `active-directory-virtual-machines`.</span></span>
    1. <span data-ttu-id="ac8ca-162">將每個 AD 範本和原則新增至藍圖中。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-162">Add each of the AD templates and policies to the blueprint.</span></span>
    1. <span data-ttu-id="ac8ca-163">將藍圖發行至任何適用的管理群組中。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-163">Publish the blueprint to any applicable management group.</span></span>
    1. <span data-ttu-id="ac8ca-164">將藍圖套用至任何需要舊式或第三方 MFA 驗證的訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-164">Apply the blueprint to any subscription requiring legacy or third-party MFA authentication.</span></span>
    1. <span data-ttu-id="ac8ca-165">在 Azure 中執行的 AD 執行個體現在會作為內部部署 AD 解決方案的延伸，而能夠與現有 MFA 工具整合，並提供宣告式的驗證，兩者皆透過現有 Active Directory 功能來達成。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-165">The instance of AD running in Azure can now be used as an extension of the on-premises AD solution, allowing it to integrate with the existing MFA tool and provide claims-based authentication, both through existing Active Directory functionality.</span></span>

## <a name="conclusion"></a><span data-ttu-id="ac8ca-166">結論</span><span class="sxs-lookup"><span data-stu-id="ac8ca-166">Conclusion</span></span>

<span data-ttu-id="ac8ca-167">將這些變更新增至治理 MVP 有助於緩解本文所述的許多風險，而讓每個雲端採用小組能夠快速地越過此一障礙。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-167">Adding these changes to the governance MVP helps mitigate many of the risks in this article, allowing each cloud adoption team to quickly move past this roadblock.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ac8ca-168">後續步驟</span><span class="sxs-lookup"><span data-stu-id="ac8ca-168">Next steps</span></span>

<span data-ttu-id="ac8ca-169">隨著雲端採用持續演進並提供額外的商業價值，風險和雲端治理需求也需要與時俱進。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-169">As cloud adoption evolves and delivers additional business value, risks and cloud governance needs will also evolve.</span></span> <span data-ttu-id="ac8ca-170">以下是一些可能發生的演進。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-170">The following are a few evolutions that may occur.</span></span> <span data-ttu-id="ac8ca-171">對於這趟旅程中的虛構公司來說，下一個觸發程序是在雲端採用計劃中納入受保護的資料。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-171">For the fictional company in this journey, the next trigger is the inclusion of protected data in the cloud adoption plan.</span></span> <span data-ttu-id="ac8ca-172">這項變更會需要額外的安全性控制。</span><span class="sxs-lookup"><span data-stu-id="ac8ca-172">This change will require additional security controls.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="ac8ca-173">安全性基準演進</span><span class="sxs-lookup"><span data-stu-id="ac8ca-173">Security Baseline evolution</span></span>](./security-baseline-evolution.md)

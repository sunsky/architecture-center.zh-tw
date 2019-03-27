---
title: CAF：安全性基準專業領域改進
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 安全性基準專業領域改進
author: BrianBlanchard
ms.openlocfilehash: 28a971f56c9f8ada1d184bdc1cb3dbb9a17c3507
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58246449"
---
# <a name="security-baseline-discipline-improvement"></a><span data-ttu-id="ec2eb-103">安全性基準專業領域改進</span><span class="sxs-lookup"><span data-stu-id="ec2eb-103">Security Baseline discipline improvement</span></span>

<span data-ttu-id="ec2eb-104">安全性基準專業領域著重於如何建立適當原則來保護網路、資產，以及將放在雲端提供者解決方案上的資料；其中以最後一項最為重要。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-104">The Security Baseline discipline focuses on ways of establishing policies that protect the network, assets, and most importantly the data that will reside on a Cloud Provider's solution.</span></span> <span data-ttu-id="ec2eb-105">在五個雲端治理專業領域中，安全性基準包含數位資產和資料的分類。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-105">Within the five disciplines of Cloud Governance, Security Baseline includes classification of the digital estate and data.</span></span> <span data-ttu-id="ec2eb-106">此外也包含與資料、資產和網路的安全性相關聯的風險、商業容忍度和風險降低策略的文件說明。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-106">It also includes documentation of risks, business tolerance, and mitigation strategies associated with the security of the data, assets, and network.</span></span> <span data-ttu-id="ec2eb-107">從技術觀點來看，其中也牽涉到以下項目的相關決策：[加密](../../decision-guides/encryption/overview.md)、[網路需求](../../decision-guides/software-defined-network/overview.md)、[混合式身分識別策略](../../decision-guides/identity/overview.md)，以及用於開發雲端安全性基準原則的[程序](compliance-processes.md)。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-107">From a technical perspective, this also includes involvement in decisions regarding [encryption](../../decision-guides/encryption/overview.md), [network requirements](../../decision-guides/software-defined-network/overview.md), [hybrid identity strategies](../../decision-guides/identity/overview.md), and the [processes](compliance-processes.md) used to develop cloud Security Baseline policies.</span></span>

<span data-ttu-id="ec2eb-108">本文將概述一些貴公司可參與的潛在工作，以更好的方式來開發安全性基準專業領域並使其臻至成熟。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-108">This article outlines some potential tasks your company can engage in to better develop and mature the Security Baseline discipline.</span></span> <span data-ttu-id="ec2eb-109">這些工作可以細分為實作雲端解決方案的規劃、建置、採用及操作階段，接著反覆執行以允許開發[雲端治理的累加方法](../journeys/overview.md#an-incremental-approach-to-cloud-governance)。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-109">These tasks can be broken down into planning, building, adopting, and operating phases of implementing a cloud solution, which are then iterated on allowing the development of an [incremental approach to cloud governance](../journeys/overview.md#an-incremental-approach-to-cloud-governance).</span></span>

![四個採用階段](../../_images/adoption-phases.png)

<span data-ttu-id="ec2eb-111">*圖 1.雲端治理累加方法的採用階段。*</span><span class="sxs-lookup"><span data-stu-id="ec2eb-111">*Figure 1. Adoption phases of the incremental approach to cloud governance.*</span></span>

<span data-ttu-id="ec2eb-112">沒有任何一份文件能夠滿足所有企業需求。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-112">It's impossible for any one document to account for the requirements of all businesses.</span></span> <span data-ttu-id="ec2eb-113">因此，本文將針對治理成熟流程的每個階段，概述建議的最小和潛在範例活動。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-113">As such, this article outlines suggested minimum and potential example activities for each phase of the governance maturation process.</span></span> <span data-ttu-id="ec2eb-114">這些活動的初始目的是協助您建置[原則 MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance)，並建立累加式原則演進的架構。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-114">The initial objective of these activities is to help you build a [Policy MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance) and establish a framework for incremental policy evolution.</span></span> <span data-ttu-id="ec2eb-115">您的雲端治理小組將需決定要對這些活動進行多少投資，以改進您的安全性基準治理功能。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-115">Your Cloud Governance team will need to decide how much to invest in these activities to improve your Security Baseline governance capabilities.</span></span>

> [!CAUTION]
> <span data-ttu-id="ec2eb-116">本文中概述的最小或潛在活動都不會與特定的公司原則或第三方合規性需求保持一致。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-116">Neither the minimum or potential activities outlined in this article are aligned to specific corporate policies or third party compliance requirements.</span></span> <span data-ttu-id="ec2eb-117">此指導方針旨在協助促成交談，從而使這兩個需求與雲端治理模型保持一致。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-117">This guidance is designed to help facilitate the conversations that will lead to alignment of both requirements with a cloud governance model.</span></span>

## <a name="planning-and-readiness"></a><span data-ttu-id="ec2eb-118">規劃和整備</span><span class="sxs-lookup"><span data-stu-id="ec2eb-118">Planning and readiness</span></span>

<span data-ttu-id="ec2eb-119">這個治理成熟度階段可消彌業務成果與可操作之策略間的鴻溝。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-119">This phase of governance maturity bridges the divide between business outcomes and actionable strategies.</span></span> <span data-ttu-id="ec2eb-120">在此程序中，領導小組會定義特定的計量、將這些計量對應至數位資產，並開始規劃整體移轉工作。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-120">During this process, the leadership team defines specific metrics, maps those metrics to the digital estate, and begins planning the overall migration effort.</span></span>

<span data-ttu-id="ec2eb-121">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="ec2eb-121">**Minimum suggested activities:**</span></span>

- <span data-ttu-id="ec2eb-122">評估您的[安全性基準工具鏈](toolchain.md)選項。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-122">Evaluate your [Security Baseline toolchain](toolchain.md) options.</span></span>
- <span data-ttu-id="ec2eb-123">開發架構指導方針文件的草稿，並散發給重要的專案關係人。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-123">Develop a draft Architecture Guidelines document and distribute to key stakeholders.</span></span>
- <span data-ttu-id="ec2eb-124">教育並涵蓋受到開發架構指導方針影響的人員和小組。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-124">Educate and involve the people and teams affected by the development of architecture guidelines.</span></span>
- <span data-ttu-id="ec2eb-125">將已設定優先權的安全性工作新增至您的移轉待辦項目中。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-125">Add prioritized security tasks to your migration backlog.</span></span>

<span data-ttu-id="ec2eb-126">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="ec2eb-126">**Potential activities:**</span></span>

- <span data-ttu-id="ec2eb-127">定義資料分類結構描述。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-127">Define a data classification schema.</span></span>
- <span data-ttu-id="ec2eb-128">進行數位資產規劃程序以清查涉及您商務程序並支持營運的目前 IT 資產。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-128">Conduct a digital estate planning process to inventory the current IT assets powering your business processes and supporting operations.</span></span>
- <span data-ttu-id="ec2eb-129">進行[原則檢閱](../../governance/policy-compliance/what-is-a-cloud-policy-review.md)以開始將現有的公司 IT 安全性原則現代化並定義 MVP 原則以因應已知風險。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-129">Conduct a [policy review](../../governance/policy-compliance/what-is-a-cloud-policy-review.md) to begin the process of modernizing existing corporate IT security policies, and define MVP policies addressing known risks.</span></span>
- <span data-ttu-id="ec2eb-130">檢閱您雲端平台的安全性指導方針。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-130">Review your cloud platform's security guidelines.</span></span> <span data-ttu-id="ec2eb-131">針對 Azure，您可以在 [Microsoft 服務信任平台](https://www.microsoft.com/trustcenter/stp/default.aspx)中找到相關資訊。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-131">For Azure these can be found in the [Microsoft Service Trust Platform](https://www.microsoft.com/trustcenter/stp/default.aspx).</span></span>
- <span data-ttu-id="ec2eb-132">判斷您的安全性基準原則是否包括[安全性開發生命週期](https://www.microsoft.com/securityengineering/sdl/)。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-132">Determine whether your Security Baseline policy includes a [Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl/).</span></span>
- <span data-ttu-id="ec2eb-133">根據接下來的一到三個版本評估網路、資料與資產相關商務風險，並判定您組織對那些風險的容忍度。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-133">Evaluate network, data, and asset-related business risks based on the next one to three releases, and gauge your organization's tolerance for those risks.</span></span>
- <span data-ttu-id="ec2eb-134">檢閱 Microsoft 的[熱門網路安全趨勢](https://www.microsoft.com/security/operations/security-intelligence-report) \(英文\) 報告以取得目前安全性趨勢概觀。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-134">Review Microsoft's [top trends in cybersecurity](https://www.microsoft.com/security/operations/security-intelligence-report) report to get an overview of the current security landscape.</span></span>
- <span data-ttu-id="ec2eb-135">考慮在您的組織中開發[安全性 DevOps](https://www.microsoft.com/en-us/securityengineering/devsecops) 角色。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-135">Consider developing a [Security DevOps](https://www.microsoft.com/en-us/securityengineering/devsecops) role in your organization.</span></span>

<!-- "en-us" location is required for the URL above. -->

## <a name="build-and-pre-deployment"></a><span data-ttu-id="ec2eb-136">建置和部署前</span><span class="sxs-lookup"><span data-stu-id="ec2eb-136">Build and pre-deployment</span></span>

<span data-ttu-id="ec2eb-137">成功移轉環境需要一些技術性和非技術性的先決條件。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-137">A number of technical and nontechnical prerequisites are required to successful migrate an environment.</span></span> <span data-ttu-id="ec2eb-138">此程序著重於可繼續進行移轉的決策、整備和核心基礎結構。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-138">This process focuses on the decisions, readiness, and core infrastructure that proceeds a migration.</span></span>

<span data-ttu-id="ec2eb-139">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="ec2eb-139">**Minimum suggested activities:**</span></span>

- <span data-ttu-id="ec2eb-140">在部署前階段中推出，以實作您的[安全性基準工具鏈](toolchain.md)。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-140">Implement your [Security Baseline toolchain](toolchain.md) by rolling out in a pre-deployment phase.</span></span>
- <span data-ttu-id="ec2eb-141">更新架構指導方針文件，並散發給重要的專案關係人。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-141">Update the Architecture Guidelines document and distribute to key stakeholders.</span></span>
- <span data-ttu-id="ec2eb-142">在已設定優先權的移轉待辦項目上實作安全性工作。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-142">Implement security tasks on your prioritized migration backlog.</span></span>
- <span data-ttu-id="ec2eb-143">開發教育性資料和文件、認知溝通、獎勵和其他計畫，以協助試用產品的使用者採用。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-143">Develop educational materials and documentation, awareness communications, incentives, and other programs to help drive user adoption.</span></span>

<span data-ttu-id="ec2eb-144">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="ec2eb-144">**Potential activities:**</span></span>

- <span data-ttu-id="ec2eb-145">決定您組織的雲端裝載資料[加密](../../decision-guides/encryption/overview.md)策略。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-145">Determine your organization's [encryption](../../decision-guides/encryption/overview.md) strategy for cloud-hosted data.</span></span>
- <span data-ttu-id="ec2eb-146">評估您雲端部署的[身分識別](../../decision-guides/identity/overview.md)策略。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-146">Evaluate your cloud deployment's [identity](../../decision-guides/identity/overview.md) strategy.</span></span> <span data-ttu-id="ec2eb-147">決定您的雲端式身分識別解決方案將與內部部署身分識別提供者共存或整合。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-147">Determine how your cloud-based identity solution will coexist or integrate with on-premises identity providers.</span></span>
- <span data-ttu-id="ec2eb-148">決定您[軟體定義網路 (SDN)](../../decision-guides/software-defined-network/overview.md) 設計的網路界限原，以確保可以獲得安全的虛擬化網路功能。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-148">Determine network boundary policies for your [Software Defined Networking (SDN)](../../decision-guides/software-defined-network/overview.md) design to ensure secure virtualized networking capabilities.</span></span>
- <span data-ttu-id="ec2eb-149">評估您組織的[最低權限存取權](/azure/active-directory/users-groups-roles/roles-delegate-by-task)原則，然後使用工作型角色來提供對特定資源的存取權。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-149">Evaluate your organization's [least privilege access](/azure/active-directory/users-groups-roles/roles-delegate-by-task) policies, and use task-based roles to provide access to specific resources.</span></span>
- <span data-ttu-id="ec2eb-150">套用安全性與監視機制到所有雲端服務與虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-150">Apply security and monitoring mechanisms to for all cloud services and virtual machines.</span></span>
- <span data-ttu-id="ec2eb-151">在可能的情況下自動化[安全性原則](../../decision-guides/policy-enforcement/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-151">Automate [security policies](../../decision-guides/policy-enforcement/overview.md) where possible.</span></span>
- <span data-ttu-id="ec2eb-152">檢閱您的安全性基準原則，並判斷是否需要根據最佳做法指導方針 (如[安全性基準生命週期](https://www.microsoft.com/securityengineering/sdl/)) 修改您的方案。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-152">Review your Security Baseline policy and determine if you need to modify your plans according to best practices guidance such as those outlined in the [Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl/).</span></span>

## <a name="adopt-and-migrate"></a><span data-ttu-id="ec2eb-153">採用和移轉</span><span class="sxs-lookup"><span data-stu-id="ec2eb-153">Adopt and migrate</span></span>

<span data-ttu-id="ec2eb-154">移轉是一個累加式程序，著重於在現有的數位資產中移動、測試及採用應用程式或工作負載。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-154">Migration is an incremental process that focuses on the movement, testing, and adoption of applications or workloads in an existing digital estate.</span></span>

<span data-ttu-id="ec2eb-155">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="ec2eb-155">**Minimum suggested activities:**</span></span>

- <span data-ttu-id="ec2eb-156">將您的[安全性基準工具鏈](toolchain.md)從部署前環境移轉至生產環境。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-156">Migrate your [Security Baseline toolchain](toolchain.md) from pre-deployment to production.</span></span>
- <span data-ttu-id="ec2eb-157">更新架構指導方針文件，並散發給重要的專案關係人。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-157">Update the Architecture Guidelines document and distribute to key stakeholders.</span></span>
- <span data-ttu-id="ec2eb-158">開發教育性資料和文件、認知溝通、獎勵和其他計畫，以協助試用產品的使用者採用</span><span class="sxs-lookup"><span data-stu-id="ec2eb-158">Develop educational materials and documentation, awareness communications, incentives, and other programs to help drive user adoption</span></span>

<span data-ttu-id="ec2eb-159">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="ec2eb-159">**Potential activities:**</span></span>

- <span data-ttu-id="ec2eb-160">檢閱最新的安全性基準與威脅資訊以找出任何新的商務風險。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-160">Review the latest Security Baseline and threat information to identify any new business risks.</span></span>
- <span data-ttu-id="ec2eb-161">判斷您組織的容忍度以處理可能會發生的新安全性威脅。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-161">Gauge your organization's tolerance to handle new security risks that may arise.</span></span>
- <span data-ttu-id="ec2eb-162">找出來自原則的偏差，並強制進行修正。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-162">Identify deviations from policy, and enforce corrections.</span></span>
- <span data-ttu-id="ec2eb-163">調整安全性與存取控制自動化，以確保可以獲得最大的原則合規性。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-163">Adjust security and access control automation to ensure maximum policy compliance.</span></span>  
- <span data-ttu-id="ec2eb-164">驗證在建置/部署前階段期間定義的最佳做法均已正確執行。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-164">Validate that the best practices defined during the Build / Pre-deployment phases are properly executed.</span></span>
- <span data-ttu-id="ec2eb-165">檢閱您的最低權限存取權原則並調整存取控制以獲得最大的安全性。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-165">Review your least privilege access policies and adjust access controls to maximize security.</span></span>
- <span data-ttu-id="ec2eb-166">針對您的工作負載測試您的安全性基準工具鏈，以找出並解決任何弱點。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-166">Test your Security Baseline toolchain against your workloads to identify and resolve any vulnerabilities.</span></span>

## <a name="operate-and-post-implementation"></a><span data-ttu-id="ec2eb-167">操作和實作後</span><span class="sxs-lookup"><span data-stu-id="ec2eb-167">Operate and post-implementation</span></span>

<span data-ttu-id="ec2eb-168">轉換完成之後，治理和操作必須依存於應用程式或工作負載的自然生命週期。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-168">Once the transformation is complete, governance and operations must live on for the natural lifecycle of an application or workload.</span></span> <span data-ttu-id="ec2eb-169">這個治理成熟度階段著重於通常會在實作解決方案且轉換週期開始穩定後隨之而來的活動。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-169">This phase of governance maturity focuses on the activities that commonly come after the solution is implemented and the transformation cycle begins to stabilize.</span></span>

<span data-ttu-id="ec2eb-170">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="ec2eb-170">**Minimum suggested activities:**</span></span>

- <span data-ttu-id="ec2eb-171">驗證和/或精簡您的[安全性基準工具鏈](toolchain.md)。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-171">Validate and/or refine your [Security Baseline toolchain](toolchain.md).</span></span>
- <span data-ttu-id="ec2eb-172">自訂通知與報告，以在發生潛在安全性問題時接收通知。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-172">Customize notifications and reports to alert you of potential security issues.</span></span>
- <span data-ttu-id="ec2eb-173">精簡架構指導方針，以引導未來的採用程序。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-173">Refine the Architecture Guidelines to guide future adoption processes.</span></span>
- <span data-ttu-id="ec2eb-174">定期與受影響的小組溝通並教育他們，以確保會持續遵循架構指導方針。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-174">Communicate and educate the affected teams periodically to ensure ongoing adherence to architecture guidelines.</span></span>

<span data-ttu-id="ec2eb-175">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="ec2eb-175">**Potential activities:**</span></span>

- <span data-ttu-id="ec2eb-176">探索您工作負載的模式與行為，並設定您的監視與報告工具，以偵測任何異常活動、存取或資源使用狀況並通知您。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-176">Discover patterns and behavior for your workloads and configure your monitoring and reporting tools to identify and notify you of any abnormal activity, access, or resource usage.</span></span>
- <span data-ttu-id="ec2eb-177">持續更新您的監視與報告原則，以偵測最新的弱點、漏洞與攻擊。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-177">Continuously update your monitoring and reporting policies to detect the latest vulnerabilities, exploits, and attacks.</span></span>
- <span data-ttu-id="ec2eb-178">備妥適當的程序，以快速停止未經授權存取並停用可能已被攻擊者入侵的資源。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-178">Have procedures in place to quickly stop unauthorized access and disable resources that may have been compromised by an attacker.</span></span>
- <span data-ttu-id="ec2eb-179">定期檢閱最新的安全性最佳做法，並在可能的情況下套用建議到您的安全性原則、自動化與監視功能。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-179">Regularly review the latest security best practices and apply recommendations to your security policy, automation, and monitoring capabilities where possible.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ec2eb-180">後續步驟</span><span class="sxs-lookup"><span data-stu-id="ec2eb-180">Next steps</span></span>

<span data-ttu-id="ec2eb-181">現在您已了解雲端安全性治理的概念，請繼續深入了解 [Microsoft 為 Azure 提供哪些安全性與最佳做法](azure-security-guidance.md)。</span><span class="sxs-lookup"><span data-stu-id="ec2eb-181">Now that you understand the concept of cloud security governance, move on to learn more about [what security and best practices guidance Microsoft provides](azure-security-guidance.md) for Azure.</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="ec2eb-182">[了解 Azure 的安全性指導方針](azure-security-guidance.md)
> [Azure 安全性簡介](/azure/security/azure-security)
> [了解記錄、報告與監視](../../decision-guides/log-and-report/overview.md)</span><span class="sxs-lookup"><span data-stu-id="ec2eb-182">[Learn about security guidance for Azure](azure-security-guidance.md)
[Introduction to Azure Security](/azure/security/azure-security)
[Learn about logging, reporting, and monitoring](../../decision-guides/log-and-report/overview.md)</span></span>

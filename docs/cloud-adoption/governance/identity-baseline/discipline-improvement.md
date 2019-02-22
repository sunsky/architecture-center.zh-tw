---
title: CAF：身分識別基準專業領域改進
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 身分識別基準專業領域改進
author: BrianBlanchard
ms.openlocfilehash: c96a638af549782fec22b2068c9b4943df4b943a
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900750"
---
# <a name="identity-baseline-discipline-improvement"></a><span data-ttu-id="f18d4-103">身分識別基準專業領域改進</span><span class="sxs-lookup"><span data-stu-id="f18d4-103">Identity Baseline discipline improvement</span></span>

<span data-ttu-id="f18d4-104">身分識別基準專業領域著重在建立原則的方式，無論主控應用程式或工作負載的雲端提供者是誰，都確保使用者身分識別的一致性和持續性。</span><span class="sxs-lookup"><span data-stu-id="f18d4-104">The Identity Baseline discipline focuses on ways of establishing policies that ensure consistency and continuity of user identities regardless of the cloud provider that hosts the application or workload.</span></span> <span data-ttu-id="f18d4-105">在五個雲端治理的專業領域中，身分識別基準包括關於[混合式身分識別策略](../../decision-guides/identity/overview.md)的決策、身分識別存放庫的評估和延伸、實作單一登入 (相同登入)、稽核及監視未獲授權使用或惡意動作項目。</span><span class="sxs-lookup"><span data-stu-id="f18d4-105">Within the Five Disciplines of Cloud Governance, Identity Baseline includes decisions regarding the [Hybrid Identity Strategy](../../decision-guides/identity/overview.md), evaluation and extension of identity repositories, implementation of single sign-on (same sign-on), auditing and monitoring for unauthorized use or malicious actors.</span></span> <span data-ttu-id="f18d4-106">在某些情況下，也可能牽涉到現代化、合併或整合多個身分識別提供者的決策。</span><span class="sxs-lookup"><span data-stu-id="f18d4-106">In some cases, it may also involve decisions to modernize, consolidate, or integrate multiple identity providers.</span></span>

<span data-ttu-id="f18d4-107">本文將概述一些貴公司可參與的潛在工作，以更好的方式來開發身分識別基準專業領域並使其臻至成熟。</span><span class="sxs-lookup"><span data-stu-id="f18d4-107">This article outlines some potential tasks your company can engage in to better develop and mature the Identity Baseline discipline.</span></span> <span data-ttu-id="f18d4-108">這些工作可以細分為實作雲端解決方案的規劃、建置、採用及操作階段，接著反覆執行以允許開發[雲端治理的累加方法](../journeys/overview.md#an-incremental-approach-to-cloud-governance)。</span><span class="sxs-lookup"><span data-stu-id="f18d4-108">These tasks can be broken down into planning, building, adopting, and operating phases of implementing a cloud solution, which are then iterated on allowing the development of an [incremental approach to cloud governance](../journeys/overview.md#an-incremental-approach-to-cloud-governance).</span></span>

![四個採用階段](../../_images/adoption-phases.png)

<span data-ttu-id="f18d4-110">*圖 1.雲端治理累加方法的採用階段。*</span><span class="sxs-lookup"><span data-stu-id="f18d4-110">*Figure 1. Adoption phases of the incremental approach to cloud governance.*</span></span>

<span data-ttu-id="f18d4-111">沒有任何一份文件能夠滿足所有企業需求。</span><span class="sxs-lookup"><span data-stu-id="f18d4-111">It's impossible for any one document to account for the requirements of all businesses.</span></span> <span data-ttu-id="f18d4-112">因此，本文將針對治理成熟流程的每個階段，概述建議的最小和潛在範例活動。</span><span class="sxs-lookup"><span data-stu-id="f18d4-112">As such, this article outlines suggested minimum and potential example activities for each phase of the governance maturation process.</span></span> <span data-ttu-id="f18d4-113">這些活動的初始目的是協助您建置[原則 MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance)，並建立累加式原則演進的架構。</span><span class="sxs-lookup"><span data-stu-id="f18d4-113">The initial objective of these activities is to help you build a [Policy MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance) and establish a framework for incremental policy evolution.</span></span> <span data-ttu-id="f18d4-114">您的雲端治理小組將需決定要對這些活動進行多少投資，以改進您的身分識別基準治理功能。</span><span class="sxs-lookup"><span data-stu-id="f18d4-114">Your Cloud Governance team will need to decide how much to invest in these activities to improve your Identity Baseline governance capabilities.</span></span>

> [!CAUTION]
> <span data-ttu-id="f18d4-115">本文中概述的最小或潛在活動都不會與特定的公司原則或第三方合規性需求保持一致。</span><span class="sxs-lookup"><span data-stu-id="f18d4-115">Neither the minimum or potential activities outlined in this article are aligned to specific corporate policies or third party compliance requirements.</span></span> <span data-ttu-id="f18d4-116">本指引旨在協助促成交談，從而使這兩個需求與雲端治理模型保持一致。</span><span class="sxs-lookup"><span data-stu-id="f18d4-116">This guidance is designed to help facilitate the conversations that will lead to alignment of both requirements with a cloud governance model.</span></span>

## <a name="planning-and-readiness"></a><span data-ttu-id="f18d4-117">規劃和整備</span><span class="sxs-lookup"><span data-stu-id="f18d4-117">Planning and readiness</span></span>

<span data-ttu-id="f18d4-118">這個階段的治理成熟度可消彌業務成果與可操作之策略間的鴻溝。</span><span class="sxs-lookup"><span data-stu-id="f18d4-118">This phase of governance maturity bridges the divide between business outcomes and actionable strategies.</span></span> <span data-ttu-id="f18d4-119">在此流程中，領導小組會定義特定的計量、將這些計量對應至數位資產，並開始規劃整體移轉工作。</span><span class="sxs-lookup"><span data-stu-id="f18d4-119">During this process, the leadership team defines specific metrics, maps those metrics to the digital estate, and begins planning the overall migration effort.</span></span>

<span data-ttu-id="f18d4-120">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="f18d4-120">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="f18d4-121">評估您的[身分識別工具鏈](toolchain.md)選項，並實作適用於貴組織的混合式策略。</span><span class="sxs-lookup"><span data-stu-id="f18d4-121">Evaluate your [Identity toolchain](toolchain.md) options and implement a hybrid strategy that is appropriate to your organization.</span></span>
* <span data-ttu-id="f18d4-122">開發架構方針文件的草稿，並散發給重要的專案關係人。</span><span class="sxs-lookup"><span data-stu-id="f18d4-122">Develop a draft Architecture Guidelines document and distribute to key stakeholders.</span></span>
* <span data-ttu-id="f18d4-123">教育並涵蓋受到開發架構方針影響的人員和小組。</span><span class="sxs-lookup"><span data-stu-id="f18d4-123">Educate and involve the people and teams affected by the development of architecture guidelines.</span></span>

<span data-ttu-id="f18d4-124">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="f18d4-124">**Potential activities:**</span></span>

* <span data-ttu-id="f18d4-125">定義角色和指派，這些項目將會治理雲端中的身分識別和存取管理。</span><span class="sxs-lookup"><span data-stu-id="f18d4-125">Define roles and assignments that will govern identity and access management in the cloud.</span></span>
* <span data-ttu-id="f18d4-126">定義您的內部部署群組，並對應至相對應的雲端型角色。</span><span class="sxs-lookup"><span data-stu-id="f18d4-126">Define your on-premises groups and map to corresponding cloud-based roles.</span></span>
* <span data-ttu-id="f18d4-127">清查身分識別提供者 (包括自訂應用程式所使用的資料庫驅動身分識別)。</span><span class="sxs-lookup"><span data-stu-id="f18d4-127">Inventory identity providers (including database-driven identities used by custom applications).</span></span>
* <span data-ttu-id="f18d4-128">請考量合併或整合身分識別提供者 (有重複項目存在) 的選項，以簡化整體身分識別解決方案。</span><span class="sxs-lookup"><span data-stu-id="f18d4-128">Consider options for consolidation or integration of identity providers where duplication exists, to simplify the overall identity solution.</span></span>
* <span data-ttu-id="f18d4-129">評估現有身分識別提供者的混合式相容性。</span><span class="sxs-lookup"><span data-stu-id="f18d4-129">Evaluate hybrid compatibility of existing identity providers.</span></span>
* <span data-ttu-id="f18d4-130">針對非混合式相容的身分識別提供者，評估合併或取代選項。</span><span class="sxs-lookup"><span data-stu-id="f18d4-130">For identity providers that are not hybrid compatible, evaluate consolidation or replacement options.</span></span>

## <a name="build-and-pre-deployment"></a><span data-ttu-id="f18d4-131">建置和部署前</span><span class="sxs-lookup"><span data-stu-id="f18d4-131">Build and pre-deployment</span></span>

<span data-ttu-id="f18d4-132">成功移轉環境需要一些技術性和非技術性的必要條件。</span><span class="sxs-lookup"><span data-stu-id="f18d4-132">A number of technical and nontechnical prerequisites are required to successfully migrate an environment.</span></span> <span data-ttu-id="f18d4-133">此流程著重於可繼續進行移轉的決策、整備和核心基礎結構。</span><span class="sxs-lookup"><span data-stu-id="f18d4-133">This process focuses on the decisions, readiness, and core infrastructure that proceeds a migration.</span></span>

<span data-ttu-id="f18d4-134">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="f18d4-134">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="f18d4-135">在實作[身分識別工具鏈](toolchain.md)之前請考量試驗測試，確定它可以盡可能簡化使用者體驗。</span><span class="sxs-lookup"><span data-stu-id="f18d4-135">Consider a pilot test before implementing your [Identity toolchain](toolchain.md), making sure it simplifies the user experience as much as possible.</span></span>
* <span data-ttu-id="f18d4-136">將試驗測試的意見反應套用到預先部署。</span><span class="sxs-lookup"><span data-stu-id="f18d4-136">Apply feedback from pilot tests into the pre-deployment.</span></span> <span data-ttu-id="f18d4-137">重複直到可接受結果。</span><span class="sxs-lookup"><span data-stu-id="f18d4-137">Repeat until results are acceptable.</span></span>
* <span data-ttu-id="f18d4-138">更新架構方針文件，以包含部署與使用者採用方案，並散發給重要的專案關係人。</span><span class="sxs-lookup"><span data-stu-id="f18d4-138">Update the Architecture Guidelines document to include deployment and user adoption plans, and distribute to key stakeholders.</span></span>
* <span data-ttu-id="f18d4-139">請考量建立早期採用者方案，並向限量的使用者推出。</span><span class="sxs-lookup"><span data-stu-id="f18d4-139">Consider establishing an early adopter program and rolling out to a limited number of users.</span></span>
* <span data-ttu-id="f18d4-140">繼續教育受架構方針影響最大的人員與小組。</span><span class="sxs-lookup"><span data-stu-id="f18d4-140">Continue to educate the people and teams most affected by the architecture guidelines.</span></span>

<span data-ttu-id="f18d4-141">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="f18d4-141">**Potential activities:**</span></span>

* <span data-ttu-id="f18d4-142">評估您的邏輯與實體架構，並決定[混合式身分識別策略](../../decision-guides/identity/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="f18d4-142">Evaluate your logical and physical architecture and determine a [Hybrid Identity Strategy](../../decision-guides/identity/overview.md).</span></span>
* <span data-ttu-id="f18d4-143">對應身分識別存取管理原則，例如登入識別碼指派，並選擇適用於 Azure AD 的適當驗證方法。</span><span class="sxs-lookup"><span data-stu-id="f18d4-143">Map identity access management policies, such as login ID assignments, and choose the appropriate authentication method for Azure AD.</span></span>
  * <span data-ttu-id="f18d4-144">如果同盟，請啟用系統管理帳戶的租用戶限制。</span><span class="sxs-lookup"><span data-stu-id="f18d4-144">If federated, enable tenant restrictions for administrative accounts.</span></span>
* <span data-ttu-id="f18d4-145">整合您的內部部署與雲端目錄。</span><span class="sxs-lookup"><span data-stu-id="f18d4-145">Integrate your on-premises and cloud directories.</span></span>
* <span data-ttu-id="f18d4-146">請考量使用下列存取模型：</span><span class="sxs-lookup"><span data-stu-id="f18d4-146">Consider using the following access models:</span></span>
  * <span data-ttu-id="f18d4-147">[最低權限存取](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)模型</span><span class="sxs-lookup"><span data-stu-id="f18d4-147">[Least Privilege Access](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) model</span></span>
  * <span data-ttu-id="f18d4-148">[特殊權限身分識別基準](/azure/active-directory/privileged-identity-management/pim-configure)存取模型</span><span class="sxs-lookup"><span data-stu-id="f18d4-148">[Privileged Identity Baseline](/azure/active-directory/privileged-identity-management/pim-configure) access model</span></span>
* <span data-ttu-id="f18d4-149">完成所有預先整合詳細資料，然後檢閱[身分識別最佳做法](/azure/security/azure-security-identity-management-best-practices)。</span><span class="sxs-lookup"><span data-stu-id="f18d4-149">Finalize all pre-integration details and review [Identity Best Practices](/azure/security/azure-security-identity-management-best-practices).</span></span>
  * <span data-ttu-id="f18d4-150">啟用單一身分識別、單一登入 (SSO) 或無縫 SSO</span><span class="sxs-lookup"><span data-stu-id="f18d4-150">Enable single identity, single sign-on (SSO), or seamless SSO</span></span>
  * <span data-ttu-id="f18d4-151">設定適用於系統管理員的多重要素驗證 (MFA)</span><span class="sxs-lookup"><span data-stu-id="f18d4-151">Configure multi-factor authentication (MFA) for administrators</span></span>
  * <span data-ttu-id="f18d4-152">需要時合併或整合身分識別提供者</span><span class="sxs-lookup"><span data-stu-id="f18d4-152">Consolidate or integrate identity providers, where necessary</span></span>
  * <span data-ttu-id="f18d4-153">集中管理身分識別所需的實作工具</span><span class="sxs-lookup"><span data-stu-id="f18d4-153">Implement tooling necessary to centralize management of identities</span></span>
  * <span data-ttu-id="f18d4-154">啟用 Just-In-Time (JIT) 存取和角色變更警示</span><span class="sxs-lookup"><span data-stu-id="f18d4-154">Enable just-in-time (JIT) access and role change alerting</span></span>
  * <span data-ttu-id="f18d4-155">進行關鍵系統管理活動的風險分析，以便指派給內建角色</span><span class="sxs-lookup"><span data-stu-id="f18d4-155">Conduct a risk analysis of key admin activities for assigning to built-in roles</span></span>
  * <span data-ttu-id="f18d4-156">考量對所有使用者更新推出項目以進行更嚴格的驗證</span><span class="sxs-lookup"><span data-stu-id="f18d4-156">Consider an updated rollout of stronger authentication for all users</span></span>
  * <span data-ttu-id="f18d4-157">針對額外系統管理角色，啟用 JIT 的特殊權限身分識別基準 (PIM) (使用限時啟用)</span><span class="sxs-lookup"><span data-stu-id="f18d4-157">Enable Privileged Identity Baseline (PIM) for JIT (using time-limited activation) for additional administrative roles</span></span>
  * <span data-ttu-id="f18d4-158">從全域管理員帳戶分隔使用者帳戶 (確定系統管理員未在無意中開啟電子郵件或執行與其全域管理員帳戶相關聯的程式)</span><span class="sxs-lookup"><span data-stu-id="f18d4-158">Separate user accounts from Global admin accounts (to make sure that administrators do not inadvertently open emails or run programs associated with their Global admin accounts)</span></span>

## <a name="adopt-and-migrate"></a><span data-ttu-id="f18d4-159">採用和遷移</span><span class="sxs-lookup"><span data-stu-id="f18d4-159">Adopt and migrate</span></span>

<span data-ttu-id="f18d4-160">移轉是一個累加式流程，著重於在現有的數位資產中，移動、測試和採用應用程式或工作負載。</span><span class="sxs-lookup"><span data-stu-id="f18d4-160">Migration is an incremental process that focuses on the movement, testing, and adoption of applications or workloads in an existing digital estate.</span></span>

<span data-ttu-id="f18d4-161">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="f18d4-161">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="f18d4-162">將您的[身分識別工具鏈](toolchain.md)從開發環境移轉至生產環境。</span><span class="sxs-lookup"><span data-stu-id="f18d4-162">Migrate your [Identity toolchain](toolchain.md) from development to production.</span></span>
* <span data-ttu-id="f18d4-163">更新架構方針文件，並散發給重要的專案關係人。</span><span class="sxs-lookup"><span data-stu-id="f18d4-163">Update the Architecture Guidelines document and distribute to key stakeholders.</span></span>
* <span data-ttu-id="f18d4-164">開發教育性資料和文件、認知溝通、獎勵和其他計畫，以協助試用產品的使用者採用。</span><span class="sxs-lookup"><span data-stu-id="f18d4-164">Develop educational materials and documentation, awareness communications, incentives, and other programs to help drive user adoption.</span></span>

<span data-ttu-id="f18d4-165">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="f18d4-165">**Potential activities:**</span></span>

* <span data-ttu-id="f18d4-166">驗證在建置/預先部署階段期間定義的最佳做法均會正確執行。</span><span class="sxs-lookup"><span data-stu-id="f18d4-166">Validate that the best practices defined during the Build / Pre-deployment phases are properly executed.</span></span>
* <span data-ttu-id="f18d4-167">驗證和/或精簡您的[混合式身分識別策略](../../decision-guides/identity/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="f18d4-167">Validate and/or refine your [Hybrid Identity Strategy](../../decision-guides/identity/overview.md).</span></span>
* <span data-ttu-id="f18d4-168">確定每個應用程式或工作負載在發行之前，都持續與身分識別策略保持一致。</span><span class="sxs-lookup"><span data-stu-id="f18d4-168">Ensure that each application or workload continues to align with the identity strategy before release.</span></span>
* <span data-ttu-id="f18d4-169">驗證單一登入 (SSO) 和無縫 SSO 對於您的應用程式如預期般運作。</span><span class="sxs-lookup"><span data-stu-id="f18d4-169">Validate that single sign-on (SSO) and seamless SSO is working as expected for your applications.</span></span>
* <span data-ttu-id="f18d4-170">可行時減少或消除替代身分識別存放區的數量。</span><span class="sxs-lookup"><span data-stu-id="f18d4-170">Reduce or eliminate the number of alternative identity stores, when possible.</span></span>
* <span data-ttu-id="f18d4-171">審查任何應用程式內或資料庫內身分識別存放區的需求。</span><span class="sxs-lookup"><span data-stu-id="f18d4-171">Scrutinize the need for any in-app or in-database identity stores.</span></span> <span data-ttu-id="f18d4-172">落在適當身分識別提供者 (第一方或第三方) 範圍外的身分識別，可以代表應用程式和使用者的風險。</span><span class="sxs-lookup"><span data-stu-id="f18d4-172">Identities that fall outside of a proper identity provider (first-party or third-party) can represent risk to the application and the users.</span></span>
* <span data-ttu-id="f18d4-173">啟用[內部部署同盟應用程式](/azure/active-directory/active-directory-device-registration-on-premises-setup)的條件式存取。</span><span class="sxs-lookup"><span data-stu-id="f18d4-173">Enable conditional access for [on-premises federated applications](/azure/active-directory/active-directory-device-registration-on-premises-setup).</span></span>
* <span data-ttu-id="f18d4-174">跨全域區域在多個中樞 (具有區域之間的同步處理) 中散佈身分識別。</span><span class="sxs-lookup"><span data-stu-id="f18d4-174">Distribute identity across global regions in multiple hubs with synchronization between regions.</span></span>
* <span data-ttu-id="f18d4-175">建立中央角色型存取控制 (RBAC) 同盟。</span><span class="sxs-lookup"><span data-stu-id="f18d4-175">Establish central role-based access control (RBAC) federation.</span></span>

## <a name="operate-and-post-implementation"></a><span data-ttu-id="f18d4-176">操作和實作後</span><span class="sxs-lookup"><span data-stu-id="f18d4-176">Operate and post-implementation</span></span>

<span data-ttu-id="f18d4-177">轉換完成之後，治理和操作必須依存於應用程式或工作負載的自然生命週期。</span><span class="sxs-lookup"><span data-stu-id="f18d4-177">Once the transformation is complete, governance and operations must live on for the natural lifecycle of an application or workload.</span></span> <span data-ttu-id="f18d4-178">這個階段的治理成熟度著重於通常會在實作解決方案且轉換週期開始穩定後隨之而來的活動。</span><span class="sxs-lookup"><span data-stu-id="f18d4-178">This phase of governance maturity focuses on the activities that commonly come after the solution is implemented and the transformation cycle begins to stabilize.</span></span>

<span data-ttu-id="f18d4-179">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="f18d4-179">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="f18d4-180">根據貴組織變動身分識別需求的變更，自訂您的[身分識別基準工具鏈](toolchain.md)。</span><span class="sxs-lookup"><span data-stu-id="f18d4-180">Customize your [Identity Baseline toolchain](toolchain.md) based on changes to your organization’s changing identity needs.</span></span>
* <span data-ttu-id="f18d4-181">將通知和報告自動化，以警示您潛在的惡意威脅。</span><span class="sxs-lookup"><span data-stu-id="f18d4-181">Automate notifications and reports to alert you of potential malicious threats.</span></span>
* <span data-ttu-id="f18d4-182">監視和報告系統使用量和使用者採用進度。</span><span class="sxs-lookup"><span data-stu-id="f18d4-182">Monitor and report on system usage and user adoption progress.</span></span>
* <span data-ttu-id="f18d4-183">報告部署後計量，並散發給專案關係人。</span><span class="sxs-lookup"><span data-stu-id="f18d4-183">Report on post-deployment metrics and distribute to stakeholders.</span></span>
* <span data-ttu-id="f18d4-184">精簡架構方針，以引導未來的採用程序。</span><span class="sxs-lookup"><span data-stu-id="f18d4-184">Refine the Architecture Guidelines to guide future adoption processes.</span></span>
* <span data-ttu-id="f18d4-185">定期與受影響的小組通訊並且持續教育，以確保會持續遵循架構方針。</span><span class="sxs-lookup"><span data-stu-id="f18d4-185">Communicate and continually educate the affected teams on a periodic basis to ensure ongoing adherence to architecture guidelines.</span></span>

<span data-ttu-id="f18d4-186">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="f18d4-186">**Potential activities:**</span></span>

* <span data-ttu-id="f18d4-187">進行身分識別原則的定期稽核並遵循做法。</span><span class="sxs-lookup"><span data-stu-id="f18d4-187">Conduct periodic audits of identity policies and adherence practices.</span></span>
* <span data-ttu-id="f18d4-188">定期掃描惡意動作項目和資料外洩，特別是與身分識別詐騙相關的項目，例如潛在的系統管理員帳戶接管。</span><span class="sxs-lookup"><span data-stu-id="f18d4-188">Scan for malicious actors and data breaches regularly, particularly those related to identity fraud, such as potential admin account takeovers.</span></span>
* <span data-ttu-id="f18d4-189">設定監視和報告工具。</span><span class="sxs-lookup"><span data-stu-id="f18d4-189">Configure a monitoring and reporting tool.</span></span>
* <span data-ttu-id="f18d4-190">請考量更緊密地與安全性和防止詐騙系統整合。</span><span class="sxs-lookup"><span data-stu-id="f18d4-190">Consider integrating more closely with security and fraud-prevention systems.</span></span>
* <span data-ttu-id="f18d4-191">定期檢閱提升權限使用者或角色的存取權限。</span><span class="sxs-lookup"><span data-stu-id="f18d4-191">Regularly review access rights for elevated users or roles.</span></span>
  * <span data-ttu-id="f18d4-192">識別有資格啟動管理員權限的每個使用者。</span><span class="sxs-lookup"><span data-stu-id="f18d4-192">Identify every user who is eligible to activate admin privilege.</span></span>
* <span data-ttu-id="f18d4-193">檢閱上架、下架與認證更新流程。</span><span class="sxs-lookup"><span data-stu-id="f18d4-193">Review on-boarding, off-boarding, and credential update processes.</span></span>
* <span data-ttu-id="f18d4-194">調查身分識別存取管理 (IAM) 模組之間的自動化和通訊增加層級。</span><span class="sxs-lookup"><span data-stu-id="f18d4-194">Investigate increasing levels of automation and communication between identity access management (IAM) modules.</span></span>
* <span data-ttu-id="f18d4-195">請考量實作開發安全性作業 (DevSecOps) 方法。</span><span class="sxs-lookup"><span data-stu-id="f18d4-195">Consider implementing a development security operations (DevSecOps) approach.</span></span>
* <span data-ttu-id="f18d4-196">執行成本、安全性和使用者採用量測結果的影響分析。</span><span class="sxs-lookup"><span data-stu-id="f18d4-196">Carry out an impact analysis to gauge results on costs, security, and user adoption.</span></span>
* <span data-ttu-id="f18d4-197">定期產生影響報告，顯示系統所建立計量的變更，並且評估[混合式身分識別策略](../../decision-guides/identity/overview.md)的業務影響。</span><span class="sxs-lookup"><span data-stu-id="f18d4-197">Periodically produce an impact report that shows the changes in metrics created by the system and estimate the business impacts of the [Hybrid Identity Strategy](../../decision-guides/identity/overview.md).</span></span>
* <span data-ttu-id="f18d4-198">建立 [Azure 資訊安全中心](/azure/security-center/security-center-intro)所建議的整合監視。</span><span class="sxs-lookup"><span data-stu-id="f18d4-198">Establish integrated monitoring recommended by the [Azure Security Center](/azure/security-center/security-center-intro).</span></span>

## <a name="next-steps"></a><span data-ttu-id="f18d4-199">後續步驟</span><span class="sxs-lookup"><span data-stu-id="f18d4-199">Next steps</span></span>

<span data-ttu-id="f18d4-200">既然您已了解雲端身分識別治理的概念，請檢查[身分識別基準工具鏈](toolchain.md)，來識別您在 Azure 平台上開發身分識別基準治理專業領域時所需的 Azure 工具和功能。</span><span class="sxs-lookup"><span data-stu-id="f18d4-200">Now that you understand the concept of cloud identity governance, examine the [Identity Baseline toolchain](toolchain.md) to identify Azure tools and features that you'll need when developing the Identity Baseline governance discipline on the Azure platform.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="f18d4-201">適用於 Azure 的身分識別基準工具鏈</span><span class="sxs-lookup"><span data-stu-id="f18d4-201">Identity Baseline toolchain for Azure</span></span>](toolchain.md)
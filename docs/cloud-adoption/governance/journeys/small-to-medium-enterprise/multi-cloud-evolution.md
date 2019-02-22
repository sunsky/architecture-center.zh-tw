---
title: CAF：中小型企業 – 多重雲端演進
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 說明：中小型企業 – 多重雲端演進
author: BrianBlanchard
ms.openlocfilehash: a5b09c92acc4e165590b5a35827b88b0ca099bed
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901111"
---
# <a name="small-to-medium-enterprise-multi-cloud-evolution"></a><span data-ttu-id="fc3f6-103">中小型企業：多重雲端演進</span><span class="sxs-lookup"><span data-stu-id="fc3f6-103">Small-to-medium enterprise: Multi-cloud evolution</span></span>

<span data-ttu-id="fc3f6-104">本文會藉由新增多重雲端採用的控制項來發展敘述。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-104">This article evolves the narrative by adding controls for multi-cloud adoption.</span></span>

## <a name="evolution-of-the-narrative"></a><span data-ttu-id="fc3f6-105">敘述的演進</span><span class="sxs-lookup"><span data-stu-id="fc3f6-105">Evolution of the narrative</span></span>

<span data-ttu-id="fc3f6-106">Microsoft 意識到客戶會基於特定用途來採用多個雲端。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-106">Microsoft recognizes that customers are adopting multiple clouds for specific purposes.</span></span> <span data-ttu-id="fc3f6-107">這個旅程圖中的虛構客戶也不例外。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-107">The fictional customer in this journey is no exception.</span></span> <span data-ttu-id="fc3f6-108">與 Azure 採用旅程圖並用，該企業成功收購了一家小型但互補的企業。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-108">In parallel to the Azure adoption journey, the business success has led to the acquisition of a small, but complementary business.</span></span> <span data-ttu-id="fc3f6-109">該企業正在不同的雲端提供者上執行其所有 IT 作業。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-109">That business is running all of their IT operations on a different cloud provider.</span></span>

<span data-ttu-id="fc3f6-110">本文將說明整合新組織時所產生的變化。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-110">This article describes how things change when integrating the new organization.</span></span> <span data-ttu-id="fc3f6-111">基於敘述的目的，我們假設這家公司已完成此客戶旅程圖中所概述的每一個治理演進。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-111">For purposes of the narrative, we assume this company has completed each of the governance evolutions outlined in this customer journey.</span></span>

### <a name="evolution-of-the-current-state"></a><span data-ttu-id="fc3f6-112">目前狀態的演進</span><span class="sxs-lookup"><span data-stu-id="fc3f6-112">Evolution of the current state</span></span>

<span data-ttu-id="fc3f6-113">在此敘述的上一個階段中，公司已經開始透過 CI/CD 管線主動將生產應用程式推送至雲端。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-113">In the previous phase of this narrative, the company had begun actively pushing production applications to the cloud through CI/CD pipelines.</span></span>

<span data-ttu-id="fc3f6-114">從那時起，某些將會影響治理的事項已經改變：</span><span class="sxs-lookup"><span data-stu-id="fc3f6-114">Since then, some things have changed that will affect governance:</span></span>

- <span data-ttu-id="fc3f6-115">身分識別會由 Active Directory 的內部部署執行個體來控制。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-115">Identity is controlled by an on-premises instance of Active Directory.</span></span> <span data-ttu-id="fc3f6-116">透過複寫到 Azure Active Directory 來促成混合式身分識別。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-116">Hybrid identity is facilitated through replication to Azure Active Directory.</span></span>
- <span data-ttu-id="fc3f6-117">IT 作業或雲端作業主要會由 Azure 監視器與相關的自動化來管理。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-117">IT Operations or Cloud Operations are largely managed by Azure Monitor and related automations.</span></span>
- <span data-ttu-id="fc3f6-118">災害復原 / 商務持續性會由 Azure Vault 執行個體來控制。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-118">Disaster Recovery / Business Continuity is controlled by Azure Vault instances.</span></span>
- <span data-ttu-id="fc3f6-119">Azure 資訊安全中心可用來監視安全性違規和攻擊。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-119">Azure Security Center is used to monitor security violations and attacks.</span></span>
- <span data-ttu-id="fc3f6-120">Azure 資訊安全中心和 Azure 監視器可同時用來監視雲端治理。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-120">Azure Security Center and Azure Monitor are both used to monitor governance of the cloud.</span></span>
- <span data-ttu-id="fc3f6-121">Azure 藍圖、Azure 原則和 Azure 管理群組可用來對原則進行合規性自動化。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-121">Azure Blueprints, Azure Policy, and Azure management groups are used to automate compliance with policy.</span></span>

### <a name="evolution-of-the-future-state"></a><span data-ttu-id="fc3f6-122">未來狀態的演進</span><span class="sxs-lookup"><span data-stu-id="fc3f6-122">Evolution of the future state</span></span>

<span data-ttu-id="fc3f6-123">目標是盡可能將收購公司整合至現有的作業。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-123">The goal is to integrate the acquisition company into existing operations wherever possible.</span></span>

## <a name="evolution-of-tangible-risks"></a><span data-ttu-id="fc3f6-124">實質風險的演進</span><span class="sxs-lookup"><span data-stu-id="fc3f6-124">Evolution of tangible risks</span></span>

<span data-ttu-id="fc3f6-125">**企業收購成本**：收購新企業，預計大約將在五年內獲利。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-125">**Business acquisition cost**: Acquisition of the new business is slated to be profitable in approximately five years.</span></span> <span data-ttu-id="fc3f6-126">由於報酬率偏低，因此，董事會希望盡可能地控制收購成本。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-126">Because of the slow rate of return, the board wants to control acquisition costs, as much as possible.</span></span> <span data-ttu-id="fc3f6-127">這會產生使成本控制和技術整合彼此衝突的風險。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-127">There is a risk of cost control and technical integration conflicting with one another.</span></span>

<span data-ttu-id="fc3f6-128">此業務風險可能會延伸出少數技術風險：</span><span class="sxs-lookup"><span data-stu-id="fc3f6-128">This business risk can be expanded into a few technical risks:</span></span>

- <span data-ttu-id="fc3f6-129">雲端移轉可能會產生額外的收購成本</span><span class="sxs-lookup"><span data-stu-id="fc3f6-129">Cloud migration might produce additional acquisition costs</span></span>
- <span data-ttu-id="fc3f6-130">無法適當治理新環境，可能導致違反原則的事件。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-130">The new environment might not be properly governed which could result in policy violations.</span></span>

## <a name="evolution-of-the-policy-statements"></a><span data-ttu-id="fc3f6-131">原則聲明的演進</span><span class="sxs-lookup"><span data-stu-id="fc3f6-131">Evolution of the policy statements</span></span>

<span data-ttu-id="fc3f6-132">下列原則變更有助於減緩新風險和指導實作。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-132">The following changes to policy will help mitigate the new risks and guide implementation.</span></span>

1. <span data-ttu-id="fc3f6-133">次要雲端中的所有資產都必須透過現有的操作管理和安全性監視工具來監視</span><span class="sxs-lookup"><span data-stu-id="fc3f6-133">All assets in a secondary cloud must be monitored through existing operational management and security monitoring tools</span></span>
2. <span data-ttu-id="fc3f6-134">所有組織單位都必須整合到現有的識別提供者</span><span class="sxs-lookup"><span data-stu-id="fc3f6-134">All Organization Units must be integrated into the existing identity provider</span></span>
3. <span data-ttu-id="fc3f6-135">主要識別提供者應治理次要雲端中的資產驗證</span><span class="sxs-lookup"><span data-stu-id="fc3f6-135">The primary identity provider should govern authentication to assets in the secondary cloud</span></span>

## <a name="evolution-of-the-best-practices"></a><span data-ttu-id="fc3f6-136">最佳做法的演進</span><span class="sxs-lookup"><span data-stu-id="fc3f6-136">Evolution of the best practices</span></span>

<span data-ttu-id="fc3f6-137">本文的這一節將推演治理 MVP 設計，以包含新的 Azure 原則和實作 Azure 成本管理。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-137">This section of the article will evolve the governance MVP design to include new Azure policies and an implementation of Azure Cost Management.</span></span> <span data-ttu-id="fc3f6-138">這兩個設計變更將共同實現新的公司原則聲明。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-138">Together, these two design changes will fulfill the new corporate policy statements.</span></span>

1. <span data-ttu-id="fc3f6-139">網路連線。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-139">Connect the networks.</span></span> <span data-ttu-id="fc3f6-140">此步驟由網路和 IT 安全性小組執行，並由雲端治理小組支援。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-140">This step is executed by the Networking and IT Security teams, and supported by the Cloud Governance team.</span></span> <span data-ttu-id="fc3f6-141">新增從 MPLS/租用線路提供者到新雲端的連線，將會整合網路。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-141">Adding a connection from the MPLS/leased-line provider to the new cloud will integrate networks.</span></span> <span data-ttu-id="fc3f6-142">新增路由表和防火牆設定，將控制環境之間的存取與流量。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-142">Adding routing tables and firewall configurations will control access and traffic between the environments.</span></span>
2. <span data-ttu-id="fc3f6-143">合併識別提供者。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-143">Consolidate identity providers.</span></span> <span data-ttu-id="fc3f6-144">根據次要雲端中所裝載的工作負載而定，有各種不同選項可用於合併識別提供者。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-144">Depending on the workloads being hosted in the secondary cloud, there are a variety of options to identity provider consolidation.</span></span> <span data-ttu-id="fc3f6-145">以下是一些範例：</span><span class="sxs-lookup"><span data-stu-id="fc3f6-145">The following are a few examples:</span></span>
    1. <span data-ttu-id="fc3f6-146">對於使用 OAuth 2 進行驗證的應用程式，次要雲端上 Active Directory 中的使用者可能只會複製寫到現有的 Azure AD 租用戶。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-146">For applications that authenticate using OAuth 2, users from Active Directory in the secondary cloud can simply be replicated to the existing Azure AD tenant.</span></span> <span data-ttu-id="fc3f6-147">這可確保所有使用者在租用戶中進行驗證。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-147">This ensures all users can be authenticated in the tenant.</span></span>
    2. <span data-ttu-id="fc3f6-148">另一個極端情況是，同盟會讓 OU 進入 Active Directory 內部部署環境，然後再到 Azure AD 執行個體。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-148">At the other extreme, federation allows OUs to flow into Active Directory on-premises, then into the Azure AD instance.</span></span>
3. <span data-ttu-id="fc3f6-149">將資產新增至 Azure Site Recovery。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-149">Add assets to Azure Site Recovery.</span></span>
    1. <span data-ttu-id="fc3f6-150">Azure Site Recovery 是以混合式/多重雲端工具的形式從頭開始設計。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-150">Azure Site Recovery was designed from the beginning as a hybrid/multi-cloud tool.</span></span>
    2. <span data-ttu-id="fc3f6-151">次要雲端中的 VM 可能會受到用來保護內部部署資產的相同 Azure Site Recovery 流程所保護。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-151">VMs in the secondary cloud might be able to be protected by the same Azure Site Recovery processes used to protect on-premises assets.</span></span>
4. <span data-ttu-id="fc3f6-152">將資產新增至 Azure 成本管理</span><span class="sxs-lookup"><span data-stu-id="fc3f6-152">Add assets to Azure Cost Management</span></span>
    1. <span data-ttu-id="fc3f6-153">Azure 成本管理是以多重雲端工具的形式從頭開始設計。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-153">Azure Cost Management was designed from the beginning as a multi-cloud tool.</span></span>
    2. <span data-ttu-id="fc3f6-154">次要雲端中的虛擬機器可能會與某些雲端提供者的 Azure 成本管理相容。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-154">Virtual machines in the secondary cloud may be compatible with Azure Cost Management for some cloud providers.</span></span> <span data-ttu-id="fc3f6-155">可能需要額外成本。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-155">Additional costs may apply.</span></span>
5. <span data-ttu-id="fc3f6-156">將資產新增至 Azure 監視器。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-156">Add assets to Azure Monitor.</span></span>
    1. <span data-ttu-id="fc3f6-157">Azure 監視器是以混合式雲端工具的形式從頭開始設計。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-157">Azure Monitor was designed as a hybrid cloud tool from inception.</span></span>
    2. <span data-ttu-id="fc3f6-158">次要雲端中的虛擬機器可能會與 Azure 監視器代理程式相容，好讓虛擬機器包含於 Azure 監視器以進行作業監視。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-158">Virtual machines in the secondary cloud may be compatible with Azure Monitor agents, allowing them to be included in Azure Monitor for operational monitoring.</span></span>
6. <span data-ttu-id="fc3f6-159">加強治理工具：</span><span class="sxs-lookup"><span data-stu-id="fc3f6-159">Governance enforcement tools:</span></span>
    1. <span data-ttu-id="fc3f6-160">加強治理為雲端特有。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-160">Governance enforcement is cloud-specific.</span></span>
    2. <span data-ttu-id="fc3f6-161">在治理旅程圖中所建立的公司原則不是雲端特有。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-161">The corporate policies established in the governance journey are not cloud-specific.</span></span> <span data-ttu-id="fc3f6-162">儘管實作可能因雲端而異，但原則還是能夠套用到次要提供者。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-162">While the implementation may vary from cloud to cloud, the policies can be applied to the secondary provider.</span></span>

<span data-ttu-id="fc3f6-163">隨著多重雲端採用的成長，上述設計演進將會逐漸成熟。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-163">As multi-cloud adoption grows, the design evolution above will continue to mature.</span></span>

## <a name="conclusion"></a><span data-ttu-id="fc3f6-164">結論</span><span class="sxs-lookup"><span data-stu-id="fc3f6-164">Conclusion</span></span>

<span data-ttu-id="fc3f6-165">這一系列敘述治理最佳做法演進的文章，都會配合著此虛構公司的體驗進行。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-165">This series of articles outlined the evolution of governance best practices, aligned with the experiences of this fictional company.</span></span> <span data-ttu-id="fc3f6-166">藉由從小規模開始 (但使用正確的基礎)，公司可以快速移動，而且還可以在對的時間套用對的治理方法。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-166">By starting small, but with the right foundation, the company could move quickly and yet still apply the right amount of governance at the right time.</span></span> <span data-ttu-id="fc3f6-167">MVP 本身不會保護客戶。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-167">The MVP by itself did not protect the customer.</span></span> <span data-ttu-id="fc3f6-168">而是建立降低風險及新增保護所需的基礎。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-168">Instead, it created the foundation to mitigate risk and add protections.</span></span> <span data-ttu-id="fc3f6-169">您可以從該處套用治理層級來降低實質風險。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-169">From there, layers of governance were applied to mitigate tangible risks.</span></span> <span data-ttu-id="fc3f6-170">此處提供的確切旅程不會 100% 符合讀者的體驗。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-170">The exact journey presented here won't align 100% with the experiences of any reader.</span></span> <span data-ttu-id="fc3f6-171">但是，可以用此模式來累加治理方式。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-171">Rather, it serves as a pattern for incremental governance.</span></span> <span data-ttu-id="fc3f6-172">建議讀者將這些最佳做法塑造成符合其獨特限制和治理需求的樣子。</span><span class="sxs-lookup"><span data-stu-id="fc3f6-172">The reader is advised to mold these best practices to fit their own unique constraints and governance requirements.</span></span>
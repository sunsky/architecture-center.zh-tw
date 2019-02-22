---
title: CAF：大型企業 – 多重雲端演進
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 大型企業 – 多重雲端演進
author: BrianBlanchard
ms.openlocfilehash: 5ef29aa523c04ff93b2d4f983482f94654a4a039
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900988"
---
# <a name="large-enterprise-multi-cloud-evolution"></a><span data-ttu-id="1d318-103">大型企業：多重雲端演進</span><span class="sxs-lookup"><span data-stu-id="1d318-103">Large enterprise: Multi-cloud evolution</span></span>

## <a name="evolution-of-the-narrative"></a><span data-ttu-id="1d318-104">敘述的演進</span><span class="sxs-lookup"><span data-stu-id="1d318-104">Evolution of the narrative</span></span>

<span data-ttu-id="1d318-105">Microsoft 意識到客戶會基於特定用途來採用多個雲端。</span><span class="sxs-lookup"><span data-stu-id="1d318-105">Microsoft recognizes that customers are adopting multiple clouds for specific purposes.</span></span> <span data-ttu-id="1d318-106">這個旅程圖中的虛構公司也不例外。</span><span class="sxs-lookup"><span data-stu-id="1d318-106">The fictional company in this journey is no exception.</span></span> <span data-ttu-id="1d318-107">與 Azure 採用旅程圖並用，該企業成功收購了一家小型但互補的企業。</span><span class="sxs-lookup"><span data-stu-id="1d318-107">In parallel to the Azure adoption journey, the business success has led to the acquisition of a small, but complementary business.</span></span> <span data-ttu-id="1d318-108">該企業正在不同的雲端提供者上執行其所有 IT 作業。</span><span class="sxs-lookup"><span data-stu-id="1d318-108">That business is running all of their IT operations on a different cloud provider.</span></span>

<span data-ttu-id="1d318-109">本文將說明整合新組織時所產生的變化。</span><span class="sxs-lookup"><span data-stu-id="1d318-109">This article describes how things change when integrating the new organization.</span></span> <span data-ttu-id="1d318-110">基於敘述的用途，我們假設這家公司已完成此客戶旅程圖中所概述的每一個治理演進。</span><span class="sxs-lookup"><span data-stu-id="1d318-110">For purposes of the narrative, we assume this company has completed each of the governance evolutions outlined in this customer journey.</span></span>

### <a name="evolution-of-the-current-state"></a><span data-ttu-id="1d318-111">目前狀態的演進</span><span class="sxs-lookup"><span data-stu-id="1d318-111">Evolution of the current state</span></span>

<span data-ttu-id="1d318-112">在此敘述的上一個階段，隨著雲端支出成為公司正常營運費用的一部分，該公司已經開始實作成本控制和成本監視。</span><span class="sxs-lookup"><span data-stu-id="1d318-112">In the previous phase of this narrative, the company had begun to implement cost controls and cost monitoring, as cloud spending becomes part of the company's regular operational expenses.</span></span>

<span data-ttu-id="1d318-113">從那時起，某些將會影響治理的事項已經改變：</span><span class="sxs-lookup"><span data-stu-id="1d318-113">Since then, some things have changed that will affect governance:</span></span>

- <span data-ttu-id="1d318-114">身分識別會由 Active Directory 的內部部署執行個體來控制。</span><span class="sxs-lookup"><span data-stu-id="1d318-114">Identity is controlled by an on-premises instance of Active Directory.</span></span> <span data-ttu-id="1d318-115">透過複寫到 Azure Active Directory 來促成混合式身分識別。</span><span class="sxs-lookup"><span data-stu-id="1d318-115">Hybrid Identity is facilitated through replication to Azure Active Directory.</span></span>
- <span data-ttu-id="1d318-116">IT 作業或雲端作業主要會由 Azure 監視器與相關的自動化來管理。</span><span class="sxs-lookup"><span data-stu-id="1d318-116">IT Operations or Cloud Operations are largely managed by Azure Monitor and related automations.</span></span>
- <span data-ttu-id="1d318-117">災害復原 / 商務持續性會由 Azure Vault 執行個體來控制。</span><span class="sxs-lookup"><span data-stu-id="1d318-117">Disaster Recovery / Business Continuity is controlled by Azure Vault instances.</span></span>
- <span data-ttu-id="1d318-118">Azure 資訊安全中心可用來監視安全性違規和攻擊。</span><span class="sxs-lookup"><span data-stu-id="1d318-118">Azure Security Center is used to monitor security violations and attacks.</span></span>
- <span data-ttu-id="1d318-119">Azure 資訊安全中心和 Azure 監視器可同時用來監視雲端治理。</span><span class="sxs-lookup"><span data-stu-id="1d318-119">Azure Security Center and Azure Monitor are both used to monitor governance of the cloud.</span></span>
- <span data-ttu-id="1d318-120">Azure 藍圖、Azure 原則和管理群組可用來對原則進行合規性自動化。</span><span class="sxs-lookup"><span data-stu-id="1d318-120">Azure Blueprints, Azure Policy, and management groups are used to automate compliance to policy.</span></span>

### <a name="evolution-of-the-future-state"></a><span data-ttu-id="1d318-121">未來狀態的演進</span><span class="sxs-lookup"><span data-stu-id="1d318-121">Evolution of the future state</span></span>

<span data-ttu-id="1d318-122">目標是盡可能將收購公司整合至現有的作業。</span><span class="sxs-lookup"><span data-stu-id="1d318-122">The goal is to integrate the acquisition company into existing operations wherever possible.</span></span>

## <a name="evolution-of-tangible-risks"></a><span data-ttu-id="1d318-123">實質風險的演進</span><span class="sxs-lookup"><span data-stu-id="1d318-123">Evolution of tangible risks</span></span>

<span data-ttu-id="1d318-124">**企業收購成本**：收購新企業，預計大約將在五年內獲利。</span><span class="sxs-lookup"><span data-stu-id="1d318-124">**Business Acquisition Cost**: Acquisition of the new business is slated to be profitable in approximately five years.</span></span> <span data-ttu-id="1d318-125">由於報酬率偏低，因此，董事會希望盡可能地控制收購成本。</span><span class="sxs-lookup"><span data-stu-id="1d318-125">Because of the slow rate of return, the board wants to control acquisition costs, as much as possible.</span></span> <span data-ttu-id="1d318-126">這會產生使成本控制和技術整合彼此衝突的風險。</span><span class="sxs-lookup"><span data-stu-id="1d318-126">There is a risk of cost control and technical integration conflicting with one another.</span></span>

<span data-ttu-id="1d318-127">此業務風險可能會延伸出少數技術風險</span><span class="sxs-lookup"><span data-stu-id="1d318-127">This business risk can be expanded into a few technical risks</span></span>

- <span data-ttu-id="1d318-128">有個雲端移轉的風險是產生額外的收購成本。</span><span class="sxs-lookup"><span data-stu-id="1d318-128">There is risk of cloud migration producing additional acquisition costs.</span></span>
- <span data-ttu-id="1d318-129">此外，若新環境未正確治理或導致違反原則，則會產生另一個風險。</span><span class="sxs-lookup"><span data-stu-id="1d318-129">There is also a risk of the new environment not being properly governed or resulting in policy violations.</span></span>

## <a name="evolution-of-the-policy-statements"></a><span data-ttu-id="1d318-130">原則聲明的演進</span><span class="sxs-lookup"><span data-stu-id="1d318-130">Evolution of the policy statements</span></span>

<span data-ttu-id="1d318-131">下列原則變更有助於降低新風險和指導實作。</span><span class="sxs-lookup"><span data-stu-id="1d318-131">The following changes to policy will help mitigate the new risks and guide implementation.</span></span>

1. <span data-ttu-id="1d318-132">次要雲端中的所有資產都必須透過現有的操作管理和安全性監視工具來監視。</span><span class="sxs-lookup"><span data-stu-id="1d318-132">All assets in a secondary cloud must be monitored through existing operational management and security monitoring tools.</span></span>
2. <span data-ttu-id="1d318-133">所有組織單位都必須整合到現有的識別提供者。</span><span class="sxs-lookup"><span data-stu-id="1d318-133">All organizational units must be integrated into the existing identity provider.</span></span>
3. <span data-ttu-id="1d318-134">主要的識別提供者應該治理對次要雲端中資產的驗證。</span><span class="sxs-lookup"><span data-stu-id="1d318-134">The primary identity provider should govern authentication to assets in the secondary cloud.</span></span>

## <a name="evolution-of-the-best-practices"></a><span data-ttu-id="1d318-135">最佳做法的演進</span><span class="sxs-lookup"><span data-stu-id="1d318-135">Evolution of the best practices</span></span>

<span data-ttu-id="1d318-136">本文的這一節將推演治理 MVP 設計，以包含新的 Azure 原則和實作 Azure 成本管理。</span><span class="sxs-lookup"><span data-stu-id="1d318-136">This section of the article will evolve the governance MVP design to include new Azure policies and an implementation of Azure Cost Management.</span></span> <span data-ttu-id="1d318-137">這兩個設計變更將共同實現新的公司原則聲明。</span><span class="sxs-lookup"><span data-stu-id="1d318-137">Together, these two design changes will fulfill the new corporate policy statements.</span></span>

1. <span data-ttu-id="1d318-138">連線網路：透過網路功能與 IT 安全性來執行 (由治理提供支援)</span><span class="sxs-lookup"><span data-stu-id="1d318-138">Connect the networks - Executed by Networking and IT Security, supported by governance</span></span>
    1. <span data-ttu-id="1d318-139">新增從 MPLS/租用線路提供者到新雲端的連線，將會整合網路。</span><span class="sxs-lookup"><span data-stu-id="1d318-139">Adding a connection from the MPLS/Leased line provider to the new cloud will integrate networks.</span></span> <span data-ttu-id="1d318-140">新增路由表和防火牆設定，將控制環境之間的存取與流量。</span><span class="sxs-lookup"><span data-stu-id="1d318-140">Adding routing tables and firewall configurations will control access and traffic between the environments.</span></span>
2. <span data-ttu-id="1d318-141">合併識別提供者。</span><span class="sxs-lookup"><span data-stu-id="1d318-141">Consolidate Identity Providers.</span></span> <span data-ttu-id="1d318-142">根據次要雲端中所裝載的工作負載而定，有各種不同選項可用於合併識別提供者。</span><span class="sxs-lookup"><span data-stu-id="1d318-142">Depending on the workloads being hosted in the secondary cloud, there are a variety of options to identity provider consolidation.</span></span> <span data-ttu-id="1d318-143">以下是一些範例：</span><span class="sxs-lookup"><span data-stu-id="1d318-143">The following are a few examples:</span></span>
    1. <span data-ttu-id="1d318-144">對於使用 OAuth 2 進行驗證的應用程式，可能只會將次要雲端上 Active Directory 中的使用者複製寫到現有的 Azure AD 租用戶。</span><span class="sxs-lookup"><span data-stu-id="1d318-144">For applications that authenticate using OAuth 2, users in the Active Directory in the secondary cloud could simply be replicated to the existing Azure AD tenant.</span></span>
    2. <span data-ttu-id="1d318-145">另一方面，這兩個內部部署識別提供者之間的同盟，可讓使用者從新的 Active Directory 網域複寫到 Azure。</span><span class="sxs-lookup"><span data-stu-id="1d318-145">On the other extreme, federation between the two on-premises identity providers, would allow users from the new Active Directory domains to be replicated to Azure.</span></span>
3. <span data-ttu-id="1d318-146">將資產新增至 Azure Site Recovery</span><span class="sxs-lookup"><span data-stu-id="1d318-146">Add assets to Azure Site Recovery</span></span>
    1. <span data-ttu-id="1d318-147">一開始已從混合式/多重雲端工具建置 Azure Site Recovery。</span><span class="sxs-lookup"><span data-stu-id="1d318-147">Azure Site Recovery was built as a hybrid/multi-cloud tool from the beginning.</span></span>
    2. <span data-ttu-id="1d318-148">次要雲端中的虛擬機器可能會受到用來保護內部部署資產的相同 Azure Site Recovery 流程所保護。</span><span class="sxs-lookup"><span data-stu-id="1d318-148">Virtual machines in the secondary cloud might be able to be protected by the same Azure Site Recovery processes used to protect on-premises assets.</span></span>
4. <span data-ttu-id="1d318-149">將資產新增至 Azure 成本管理</span><span class="sxs-lookup"><span data-stu-id="1d318-149">Add assets to Azure Cost Management</span></span>
    1. <span data-ttu-id="1d318-150">一開始已從多重雲端工具建置 Azure 成本管理。</span><span class="sxs-lookup"><span data-stu-id="1d318-150">Azure Cost Management was built as a multi-cloud tool from the beginning.</span></span>
    2. <span data-ttu-id="1d318-151">次要雲端中的虛擬機器可能會與某些雲端提供者的 Azure 成本管理相容。</span><span class="sxs-lookup"><span data-stu-id="1d318-151">Virtual machines in the secondary cloud might be compatible with Azure Cost Management for some cloud providers.</span></span> <span data-ttu-id="1d318-152">可能需要額外成本。</span><span class="sxs-lookup"><span data-stu-id="1d318-152">Additional costs may apply.</span></span>
5. <span data-ttu-id="1d318-153">將資產新增至 Azure 監視器</span><span class="sxs-lookup"><span data-stu-id="1d318-153">Add assets to Azure Monitor</span></span>
    1. <span data-ttu-id="1d318-154">一開始已從混合式雲端工具建置 Azure 監視器。</span><span class="sxs-lookup"><span data-stu-id="1d318-154">Azure Monitor was built as a hybrid cloud tool from the beginning.</span></span>
    2. <span data-ttu-id="1d318-155">次要雲端中的虛擬機器可能會與 Azure 監視器代理程式相容，能夠將它們包含於 Azure 監視器以進行操作監控。</span><span class="sxs-lookup"><span data-stu-id="1d318-155">Virtual machines in the secondary cloud might be compatible with Azure Monitor agents, allowing them to be included in Azure Monitor for operational monitoring.</span></span>
6. <span data-ttu-id="1d318-156">加強治理工具</span><span class="sxs-lookup"><span data-stu-id="1d318-156">Governance enforcement tools</span></span>
    1. <span data-ttu-id="1d318-157">加強治理為雲端特有。</span><span class="sxs-lookup"><span data-stu-id="1d318-157">Governance enforcement is cloud-specific.</span></span>
    2. <span data-ttu-id="1d318-158">在治理旅程圖中所建立的公司原則不是。</span><span class="sxs-lookup"><span data-stu-id="1d318-158">The corporate policies established in the governance journey are not.</span></span> <span data-ttu-id="1d318-159">儘管實作可能因雲端而異，但原則聲明還是能夠套用到次要提供者。</span><span class="sxs-lookup"><span data-stu-id="1d318-159">While the implementation may vary from cloud to cloud, the policy statements can be applied to the secondary provider.</span></span>

<span data-ttu-id="1d318-160">隨著多重雲端採用的成長，上述設計演進將會逐漸成熟。</span><span class="sxs-lookup"><span data-stu-id="1d318-160">As multi-cloud adoption grows, the design evolution above will continue to mature.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1d318-161">後續步驟</span><span class="sxs-lookup"><span data-stu-id="1d318-161">Next steps</span></span>

<span data-ttu-id="1d318-162">在許多大型企業中，雲端治理的專業領域可能會成為採用的阻礙物。</span><span class="sxs-lookup"><span data-stu-id="1d318-162">In many large enterprises, the disciplines of cloud governance can be blockers to adoption.</span></span> <span data-ttu-id="1d318-163">下一篇文章將提供一些與使治理成為小組運動密切相關的想法，可協助確保雲端中的長期成功。</span><span class="sxs-lookup"><span data-stu-id="1d318-163">The next article has some closing thoughts about making governance a team sport, to help ensure long-term success in the cloud.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="1d318-164">多層治理</span><span class="sxs-lookup"><span data-stu-id="1d318-164">Multiple layers of governance</span></span>](./multiple-layers-of-governance.md)

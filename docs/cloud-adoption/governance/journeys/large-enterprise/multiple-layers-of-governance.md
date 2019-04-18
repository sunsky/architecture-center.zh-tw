---
title: CAF：大型企業 – 控管在大型企業中的多個圖層
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 大型企業 – 控管在大型企業中的多個圖層
author: BrianBlanchard
ms.openlocfilehash: 1a90f8007077df0ecefa8ec5d8c0dd6bfca9ccc7
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59641207"
---
# <a name="multiple-layers-of-governance-in-large-enterprises"></a><span data-ttu-id="1031f-103">大型企業中的多層治理</span><span class="sxs-lookup"><span data-stu-id="1031f-103">Multiple layers of governance in large enterprises</span></span>

<span data-ttu-id="1031f-104">當大型企業會需要多層治理時，必須將更高層級的複雜度納入治理 MVP 和稍後的治理演變中。</span><span class="sxs-lookup"><span data-stu-id="1031f-104">When large enterprises require multiple layers of governance, there are greater levels of complexity that must be factored into the governance MVP and later governance evolutions.</span></span>

<span data-ttu-id="1031f-105">此類複雜度的幾個常見範例包括：</span><span class="sxs-lookup"><span data-stu-id="1031f-105">A few common examples of such complexities include:</span></span>

- <span data-ttu-id="1031f-106">分散式治理功能。</span><span class="sxs-lookup"><span data-stu-id="1031f-106">Distributed governance functions.</span></span>
- <span data-ttu-id="1031f-107">公司 IT 支援營業單位 IT 組織。</span><span class="sxs-lookup"><span data-stu-id="1031f-107">Corporate IT supporting Business unit IT organizations.</span></span>
- <span data-ttu-id="1031f-108">公司 IT 支援地理位置分散的 IT 組織。</span><span class="sxs-lookup"><span data-stu-id="1031f-108">Corporate IT supporting geographically distributed IT organizations.</span></span>

<span data-ttu-id="1031f-109">這篇文章將探討巡覽此類複雜度的一些方式。</span><span class="sxs-lookup"><span data-stu-id="1031f-109">This article explores some ways to navigate this type of complexity.</span></span>

## <a name="large-enterprise-governance-is-a-team-sport"></a><span data-ttu-id="1031f-110">大型企業治理是一個小組運動</span><span class="sxs-lookup"><span data-stu-id="1031f-110">Large enterprise governance is a team sport</span></span>

<span data-ttu-id="1031f-111">大型成熟企業通常擁有專注於整個旅程中提到的專業領域的小組或員工。</span><span class="sxs-lookup"><span data-stu-id="1031f-111">Large established enterprises often have teams or employees who focus on the disciplines mentioned throughout this journey.</span></span> <span data-ttu-id="1031f-112">這個旅程示範了將治理變成小組運動的一個方式。</span><span class="sxs-lookup"><span data-stu-id="1031f-112">This journey demonstrates one approach to making governance a team sport.</span></span>

<span data-ttu-id="1031f-113">在許多大型企業中，雲端管理五個專業領域可以採用的阻礙。</span><span class="sxs-lookup"><span data-stu-id="1031f-113">In many large enterprises, the Five Disciplines of Cloud Governance can be blockers to adoption.</span></span> <span data-ttu-id="1031f-114">在整個企業開發身分識別、安全性、作業、部署和設定方面的雲端專業知識需要時間。</span><span class="sxs-lookup"><span data-stu-id="1031f-114">Developing cloud expertise in identity, security, operations, deployments, and configuration across an enterprise takes time.</span></span> <span data-ttu-id="1031f-115">全面實作 IT 治理原則和 IT 安全性可能會使創新減緩數個月甚至數年。</span><span class="sxs-lookup"><span data-stu-id="1031f-115">Holistically implementing IT governance policy and IT security can slow innovation by months or even years.</span></span> <span data-ttu-id="1031f-116">必須創新的業務與必須保護現有資源的治理之間的平衡是很微妙的。</span><span class="sxs-lookup"><span data-stu-id="1031f-116">Balancing the business need to innovate and the governance need to protect existing resources is delicate.</span></span>

<span data-ttu-id="1031f-117">雲端的固有功能可以移除創新的阻礙，但會增加風險。</span><span class="sxs-lookup"><span data-stu-id="1031f-117">The inherent capabilities of the cloud can remove blockers to innovation but increase risks.</span></span> <span data-ttu-id="1031f-118">在此治理旅程中，我們示範了範例公司建立護欄以降低風險的方式。</span><span class="sxs-lookup"><span data-stu-id="1031f-118">In this governance journey, we showed how the example company created guardrails to mitigate the risk.</span></span> <span data-ttu-id="1031f-119">雲端治理小組並非處理保護環境所需的每個專業領域，而是率領風險型方式以治理可部署的內容，而其他小組則建置必要的雲端成熟度。</span><span class="sxs-lookup"><span data-stu-id="1031f-119">Rather than tackling each of the disciplines required to protect the environment, the Cloud Governance team leads a risk-based approach to govern what could be deployed, while the other teams build the necessary cloud maturities.</span></span> <span data-ttu-id="1031f-120">最重要的是，當每個小組到達雲端成熟度時，治理會全面地適用於其解決方案。</span><span class="sxs-lookup"><span data-stu-id="1031f-120">Most importantly, as each team reaches cloud maturity, governance applies their solutions holistically.</span></span> <span data-ttu-id="1031f-121">當每個小組都成熟並增添至整體解決方案時，雲端治理小組即可開啟階段大門，讓其他創新和採用蓬勃發展。</span><span class="sxs-lookup"><span data-stu-id="1031f-121">As each team matures and adds to the overall solution, the Cloud Governance team can open stage gates, allowing additional innovation and adoption to thrive.</span></span>

<span data-ttu-id="1031f-122">此模型說明了雲端治理小組與現有企業團隊 (安全性、IT 治理、網路、身分識別等) 之間合作關係的成長。</span><span class="sxs-lookup"><span data-stu-id="1031f-122">This model illustrates the growth of a partnership between the Cloud Governance team and existing enterprise teams (Security, IT Governance, Networking, Identity, and others).</span></span> <span data-ttu-id="1031f-123">該旅程是以治理 MVP 為開始，透過治理演變，成長到全面的結束狀態。</span><span class="sxs-lookup"><span data-stu-id="1031f-123">The journey starts with the governance MVP and grows to a holistic end state through governance evolutions.</span></span>

## <a name="requirements-to-supporting-such-a-team-sport"></a><span data-ttu-id="1031f-124">支援這類小組運動的需求</span><span class="sxs-lookup"><span data-stu-id="1031f-124">Requirements to supporting such a team sport</span></span>

<span data-ttu-id="1031f-125">多層治理模型的第一個需求是了解治理階層。</span><span class="sxs-lookup"><span data-stu-id="1031f-125">The first requirement of a multi-layer governance model is to understand of the governance hierarchy.</span></span> <span data-ttu-id="1031f-126">回答下列問題可協助您了解一般的控管階層：</span><span class="sxs-lookup"><span data-stu-id="1031f-126">Answering the following questions will help you to understand the general governance hierarchy:</span></span>

- <span data-ttu-id="1031f-127">雲端帳戶處理 (雲端服務的計費) 在各營業單位之間如何配置？</span><span class="sxs-lookup"><span data-stu-id="1031f-127">How is cloud accounting (billing for cloud services) allocated across business units?</span></span>
- <span data-ttu-id="1031f-128">治理責任在公司 IT 與各個營業單位之間如何配置？</span><span class="sxs-lookup"><span data-stu-id="1031f-128">How are governance responsibilities allocated across corporate IT and each business unit?</span></span>
- <span data-ttu-id="1031f-129">各個 IT 單位管理哪些類型的環境？</span><span class="sxs-lookup"><span data-stu-id="1031f-129">What types of environments do each of those units of IT manage?</span></span>

## <a name="central-governance-of-a-distributed-governance-hierarchy"></a><span data-ttu-id="1031f-130">分散式治理階層的中央治理</span><span class="sxs-lookup"><span data-stu-id="1031f-130">Central governance of a distributed governance hierarchy</span></span>

<span data-ttu-id="1031f-131">管理群組之類的工具可讓公司 IT 建立符合治理階層的階層結構。</span><span class="sxs-lookup"><span data-stu-id="1031f-131">Tools like management groups allow corporate IT to create a hierarchy structure that matches the governance hierarchy.</span></span> <span data-ttu-id="1031f-132">Azure 藍圖之類的工具可套用至該階層的不同層級。</span><span class="sxs-lookup"><span data-stu-id="1031f-132">Tools like Azure Blueprints can apply assets to different layers of that hierarchy.</span></span> <span data-ttu-id="1031f-133">Azure 藍圖可以建立版本，並可將各版本套用至管理群組、訂用帳戶或資源群組。</span><span class="sxs-lookup"><span data-stu-id="1031f-133">Azure Blueprints can be versioned and various versions can be applied to management groups, subscriptions, or resource groups.</span></span> <span data-ttu-id="1031f-134">治理 MVP 中會詳細說明每一個概念。</span><span class="sxs-lookup"><span data-stu-id="1031f-134">Each of these concepts is described in more detail in the governance MVP.</span></span>

<span data-ttu-id="1031f-135">這些工具的重點是能夠套用至階層的多個藍圖。</span><span class="sxs-lookup"><span data-stu-id="1031f-135">The important aspect of each of these tools is the ability to apply multiple blueprints to a hierarchy.</span></span> <span data-ttu-id="1031f-136">這可讓治理成為多層式流程。</span><span class="sxs-lookup"><span data-stu-id="1031f-136">This allows governance to be a layered process.</span></span> <span data-ttu-id="1031f-137">以下是此治理之階層式應用程式的其中一個範例：</span><span class="sxs-lookup"><span data-stu-id="1031f-137">The following is one example of this hierarchical application of governance:</span></span>

- <span data-ttu-id="1031f-138">公司 IT：公司 IT 會建立一組套用至所有雲端採用的標準和原則。</span><span class="sxs-lookup"><span data-stu-id="1031f-138">Corporate IT: Corporate IT creates a set of standards and policies that apply to all cloud adoption.</span></span> <span data-ttu-id="1031f-139">這會在「基準」藍圖中具體化。</span><span class="sxs-lookup"><span data-stu-id="1031f-139">This is materialized in a "Baseline" blueprint.</span></span> <span data-ttu-id="1031f-140">然後，公司 IT 會擁有管理群組階層，確保基準版本套用至階層中的所有訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="1031f-140">Corporate IT then owns the management group hierarchy, ensuring that a version of the baseline is applied to all subscriptions in the hierarchy.</span></span>
- <span data-ttu-id="1031f-141">地區或業務單位 IT：各個 IT 小組可以藉由建立自己的藍圖，來套用額外的治理層級。</span><span class="sxs-lookup"><span data-stu-id="1031f-141">Regional or Business Unit IT: Various IT teams can apply an additional layer of governance by creating their own blueprint.</span></span> <span data-ttu-id="1031f-142">這些藍圖會建立附加的原則和標準。</span><span class="sxs-lookup"><span data-stu-id="1031f-142">Those blueprints would create additive policies and standards.</span></span> <span data-ttu-id="1031f-143">開發完成後，公司 IT 可將這些藍圖套用至管理群組階層內適用的節點。</span><span class="sxs-lookup"><span data-stu-id="1031f-143">Once developed, Corporate IT could apply those blueprints to the applicable nodes within the management group hierarchy.</span></span>
- <span data-ttu-id="1031f-144">雲端採用小組：詳細的決策和有關應用程式或工作負載的實作，可由雲端採用小組在治理需求範圍內加以進行。</span><span class="sxs-lookup"><span data-stu-id="1031f-144">Cloud adoption teams: Detailed decisions and implementation about applications or workloads can be made by the cloud adoption teams, within the context of governance requirements.</span></span> <span data-ttu-id="1031f-145">有時，小組也可以要求其他的 Azure 資源一致性範本，以加速採用工作。</span><span class="sxs-lookup"><span data-stu-id="1031f-145">At times the team can also request additional Azure Resource Consistency templates to accelerate adoption efforts.</span></span>

<span data-ttu-id="1031f-146">每個層級的治理實作詳細資料將需要每個小組之間協調。</span><span class="sxs-lookup"><span data-stu-id="1031f-146">The details regarding governance implementation at each level will require coordination between each team.</span></span> <span data-ttu-id="1031f-147">此旅程中概述的治理 MVP 和治理演變有助於使協調一致。</span><span class="sxs-lookup"><span data-stu-id="1031f-147">The governance MVP and governance evolutions outlined in this journey can aid in aligning that coordination.</span></span>

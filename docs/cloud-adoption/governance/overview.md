---
title: CAF：在 Microsoft 外，azure 中的控管
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 在 Microsoft 外，azure 中的控管
author: BrianBlanchard
ms.openlocfilehash: ce407de0daa590e767382346692c80e0a113bb3c
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068832"
---
# <a name="governance-in-the-microsoft-caf-for-azure"></a><span data-ttu-id="aefbd-103">在 Microsoft 外，azure 中的控管</span><span class="sxs-lookup"><span data-stu-id="aefbd-103">Governance in the Microsoft CAF for Azure</span></span>

<span data-ttu-id="aefbd-104">雲端為支援商務的技術建立新的架構。</span><span class="sxs-lookup"><span data-stu-id="aefbd-104">The cloud creates new paradigms regarding the technologies that support the business.</span></span> <span data-ttu-id="aefbd-105">這些新的架構也促使採用、管理和治理這些技術的方式有所改變。</span><span class="sxs-lookup"><span data-stu-id="aefbd-105">These new paradigms also cause shifts in how those technologies are adopted, managed, and governed.</span></span> <span data-ttu-id="aefbd-106">自動程序會執行一行程式碼來終結並重新建立整個資料中心時，我們必須重新思考傳統方法。</span><span class="sxs-lookup"><span data-stu-id="aefbd-106">When entire datacenters can be destroyed and recreated with one line of code executed from an unattended process, we have to rethink traditional approaches.</span></span> <span data-ttu-id="aefbd-107">對於治理也同樣如此。</span><span class="sxs-lookup"><span data-stu-id="aefbd-107">This is equally true when it comes to governance.</span></span>

<span data-ttu-id="aefbd-108">針對使用現有原則治理內部部署 IT 環境的組織而言，雲端治理應該可以補強這些原則。</span><span class="sxs-lookup"><span data-stu-id="aefbd-108">For organizations with existing policies governing on-premises IT environments, cloud governance should complement those policies.</span></span> <span data-ttu-id="aefbd-109">不過，在內部部署與雲端之間的公司原則整合層級會由於雲端治理成熟度和雲端中的數位資產而有所差異。</span><span class="sxs-lookup"><span data-stu-id="aefbd-109">However, the level of corporate policy integration between on-premises and the cloud will vary depending on cloud governance maturity and digital estate in the cloud.</span></span> <span data-ttu-id="aefbd-110">雲端資產隨時間不斷演化，雲端治理程序和原則也不斷改變。</span><span class="sxs-lookup"><span data-stu-id="aefbd-110">As the cloud estate evolves over time, so will cloud governance processes and policies.</span></span>

<span data-ttu-id="aefbd-111">本節關於 CAF 的指引有兩個目的：</span><span class="sxs-lookup"><span data-stu-id="aefbd-111">The guidance in this section of the CAF is designed for two purposes:</span></span>

* <span data-ttu-id="aefbd-112">提供客戶通常都會遇到且可採取動作的程序。</span><span class="sxs-lookup"><span data-stu-id="aefbd-112">Provide actionable customer journeys that represent common experiences that customers often encounter.</span></span> <span data-ttu-id="aefbd-113">所有這些都涵蓋商務風險、降低風險的公司原則，以及實作技術解決方案的設計指引。</span><span class="sxs-lookup"><span data-stu-id="aefbd-113">Each of these encapsulate business risks, corporate policies for risk mitigation, and design guidance to implement technical solutions.</span></span> <span data-ttu-id="aefbd-114">若有必要，設計指引可以專門針對 Azure。</span><span class="sxs-lookup"><span data-stu-id="aefbd-114">By necessity, the design guidance is specific to Azure.</span></span> <span data-ttu-id="aefbd-115">這些程序中其他所有指引可以在任何雲端均適用的方法或多雲端方法中運用。</span><span class="sxs-lookup"><span data-stu-id="aefbd-115">All other guidance in these journeys could be applied as part of a cloud-agnostic or multi-cloud approach.</span></span>
* <span data-ttu-id="aefbd-116">透過開發公司原則、處理程序和工具的詳細指引，協助讀者建立滿足各種不同業務需求的個人化治理解決方案，包括治理多個公用雲端。</span><span class="sxs-lookup"><span data-stu-id="aefbd-116">Help readers create personalized governance solutions that can meet a variety of business needs, including the governance of multiple public clouds, through detailed guidance on the development of corporate policies, processes, and tooling.</span></span>

<span data-ttu-id="aefbd-117">此內容適用於雲端治理小組。</span><span class="sxs-lookup"><span data-stu-id="aefbd-117">This content is intended for the Cloud Governance team.</span></span> <span data-ttu-id="aefbd-118">這也與開發雲端治理強大基礎的雲端架構設計師息息相關。</span><span class="sxs-lookup"><span data-stu-id="aefbd-118">It is also relevant to cloud architects that need to develop a strong foundation in cloud governance.</span></span>

## <a name="audience"></a><span data-ttu-id="aefbd-119">對象</span><span class="sxs-lookup"><span data-stu-id="aefbd-119">Audience</span></span>

<span data-ttu-id="aefbd-120">CAF 的內容會影響企業的業務、技術和文化。</span><span class="sxs-lookup"><span data-stu-id="aefbd-120">The content in the CAF affects the business, technology, and culture of enterprises.</span></span> <span data-ttu-id="aefbd-121">CAF 的這個部分將與 IT 安全性、IT 治理、財務、業務主管、網路、身分識別和雲端採用小組進行密切互動。</span><span class="sxs-lookup"><span data-stu-id="aefbd-121">This section of the CAF will interact heavily with IT Security, IT Governance, Finance, line-of-business leaders, Networking, Identity, and cloud adoption teams.</span></span> <span data-ttu-id="aefbd-122">對於這些角色有不同程度的相依性，將需要雲端架構師使用本指南才能達成。</span><span class="sxs-lookup"><span data-stu-id="aefbd-122">There are various co-dependencies on these personas which will require a facilitative approach by the Cloud Architects using this guidance.</span></span> <span data-ttu-id="aefbd-123">對這些小組的接觸可能只需要進行一次；不過，在某些情況下，與其他這些角色的互動則需要不斷進行。</span><span class="sxs-lookup"><span data-stu-id="aefbd-123">Facilitation with these teams may be a one-time effort, but in some cases, it will result in recurring interactions with these other personas.</span></span>

<span data-ttu-id="aefbd-124">雲端架構設計師可引導並促使這些對象共同合作。</span><span class="sxs-lookup"><span data-stu-id="aefbd-124">The Cloud Architect serves as the thought leader and facilitator to bring these audiences together.</span></span> <span data-ttu-id="aefbd-125">此系列指南的內容旨在協助雲端架構設計師促進與正確對象進行正確的對話，藉以推動必要的決策。</span><span class="sxs-lookup"><span data-stu-id="aefbd-125">The content in this collection of guides is designed to help the Cloud Architect facilitate the right conversation, with the right audience, to drive necessary decisions.</span></span> <span data-ttu-id="aefbd-126">由雲端推動的業務轉型必須由雲端架構設計師居中協助指導整個業務和 IT 的決策。</span><span class="sxs-lookup"><span data-stu-id="aefbd-126">Business transformation that is empowered by the cloud is dependent upon the Cloud Architect role to help guide decisions throughout the business and IT.</span></span>

<span data-ttu-id="aefbd-127">**本節中的雲端架構設計師專業化：** CAF 的每個部分代表雲端架構設計師角色的不同專業化或樣貌。</span><span class="sxs-lookup"><span data-stu-id="aefbd-127">**Cloud Architect specialization in this section:** Each section of the CAF represents a different specialization or variant of the Cloud Architect role.</span></span> <span data-ttu-id="aefbd-128">本節中的外適合雲端架構設計人員懷有熱忱的緩和或降低技術的風險。</span><span class="sxs-lookup"><span data-stu-id="aefbd-128">This section of the CAF is designed for cloud architects with a passion for mitigating or reducing technical risks.</span></span> <span data-ttu-id="aefbd-129">許多雲端服務提供者將這些專家稱為雲端監管人，我們則偏向稱為雲端守護者，或統稱為雲端治理小組。</span><span class="sxs-lookup"><span data-stu-id="aefbd-129">Many cloud providers refer to these specialists as cloud custodians, we prefer cloud guardians or, collectively, the Cloud Governance team.</span></span> <span data-ttu-id="aefbd-130">在每個可採取動作的客戶程序中，這些文章呈現雲端治理小組的組成和角色如何隨時間而改變。</span><span class="sxs-lookup"><span data-stu-id="aefbd-130">In each actionable customer journey, the articles show how the composition and role of the Cloud Governance team may change over time.</span></span>

## <a name="using-this-guide"></a><span data-ttu-id="aefbd-131">使用本指南</span><span class="sxs-lookup"><span data-stu-id="aefbd-131">Using this guide</span></span>

<span data-ttu-id="aefbd-132">對於想要完全遵循本指南的讀者，此內容將有助於訂定與雲端實作並行的強大雲端治理策略。</span><span class="sxs-lookup"><span data-stu-id="aefbd-132">For readers who wish to follow this guide from beginning to end, this content will aid in developing a robust cloud governance strategy in parallel to cloud implementation.</span></span> <span data-ttu-id="aefbd-133">本指南會引導讀者了解此類策略的理論和實作。</span><span class="sxs-lookup"><span data-stu-id="aefbd-133">The guidance walks the reader through the theory and implementation of such a strategy.</span></span>

<span data-ttu-id="aefbd-134">當機的理論和快速存取 Azure 實作課程，開始使用[概觀可操作的控管旅程](./journeys/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="aefbd-134">For a crash course on the theory and quick access to Azure implementation, get started with the [Overview of actionable governance journeys](./journeys/overview.md).</span></span> <span data-ttu-id="aefbd-135">透過此指南，讀者可以從小規模開始，並在雲端採用過程中同時發展其治理需求。</span><span class="sxs-lookup"><span data-stu-id="aefbd-135">Through this guidance, the reader can start small and evolve their governance needs in parallel with cloud adoption efforts.</span></span>

## <a name="next-steps"></a><span data-ttu-id="aefbd-136">後續步驟</span><span class="sxs-lookup"><span data-stu-id="aefbd-136">Next steps</span></span>

<span data-ttu-id="aefbd-137">檢閱可操作的控管旅程圖。</span><span class="sxs-lookup"><span data-stu-id="aefbd-137">Review the actionable governance journeys.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="aefbd-138">可採取動作的治理程序</span><span class="sxs-lookup"><span data-stu-id="aefbd-138">Actionable Governance Journeys</span></span>](./journeys/overview.md)

---
title: CAF：中小型企業 - 治理策略背後的初始敘述
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 這個敘述會針對中小型企業治理旅程建立一個使用案例。
author: BrianBlanchard
ms.openlocfilehash: 6173b01f310169017761110d6641acfa51d45df8
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901188"
---
# <a name="small-to-medium-enterprise-the-narrative-behind-the-governance-strategy"></a><span data-ttu-id="92781-103">中小型企業作業：治理策略背後的敘述</span><span class="sxs-lookup"><span data-stu-id="92781-103">Small-to-medium enterprise: The narrative behind the governance strategy</span></span>

<span data-ttu-id="92781-104">下列敘述描述[中小型企業治理旅程](./overview.md)的使用案例。</span><span class="sxs-lookup"><span data-stu-id="92781-104">The following narrative describes the use case for the [small-to-medium enterprise governance journey](./overview.md).</span></span> <span data-ttu-id="92781-105">實作旅程之前，請務必了解此敘述中反映的假設和推論。</span><span class="sxs-lookup"><span data-stu-id="92781-105">Before implementing the journey, it’s important to understand the assumptions and reasoning that are reflected in this narrative.</span></span> <span data-ttu-id="92781-106">然後您可以讓治理策略與貴組織的旅程更一致。</span><span class="sxs-lookup"><span data-stu-id="92781-106">Then you can better align the governance strategy to your own organization’s journey.</span></span>

## <a name="back-story"></a><span data-ttu-id="92781-107">背景故事</span><span class="sxs-lookup"><span data-stu-id="92781-107">Back story</span></span>

<span data-ttu-id="92781-108">董事會今年開始計劃以多種方式為企業注入活力。</span><span class="sxs-lookup"><span data-stu-id="92781-108">The board of directors started the year with plans to energize the business in several ways.</span></span> <span data-ttu-id="92781-109">他們將推動改善客戶體驗以獲得市場佔有率的領導地位。</span><span class="sxs-lookup"><span data-stu-id="92781-109">They are pushing leadership to improve customer experiences to gain market share.</span></span> <span data-ttu-id="92781-110">他們也將推動新產品和服務，將公司定位為業界的思想領導者。</span><span class="sxs-lookup"><span data-stu-id="92781-110">They are also pushing for new products and services that will position the company as a thought leader in the industry.</span></span> <span data-ttu-id="92781-111">他們同時還盡力減少浪費並縮減不必要的成本。</span><span class="sxs-lookup"><span data-stu-id="92781-111">They also initiated a parallel effort to reduce waste and cut unnecessary costs.</span></span> <span data-ttu-id="92781-112">雖然令人生畏，但董事會和領導階層的行動展現出這項工作將盡可能地將資金集中投入未來發展。</span><span class="sxs-lookup"><span data-stu-id="92781-112">Though intimidating, the actions of the board and leadership show that this effort is focusing as much capital as possible on future growth.</span></span>

<span data-ttu-id="92781-113">過去，公司的 CIO 對於這些策略對話並沒有發言的權利。</span><span class="sxs-lookup"><span data-stu-id="92781-113">In the past, the company’s CIO has been excluded from these strategic conversations.</span></span> <span data-ttu-id="92781-114">但是，未來的願景在本質上與技術發展息息相關，因此 IT 擁有一席之地，可以幫助指導這些重大計劃。</span><span class="sxs-lookup"><span data-stu-id="92781-114">However, because the future vision is intrinsically linked to technical growth, IT has a seat at the table to help guide these big plans.</span></span> <span data-ttu-id="92781-115">IT 現在應該以新的方式提供。</span><span class="sxs-lookup"><span data-stu-id="92781-115">IT is now expected to deliver in new ways.</span></span> <span data-ttu-id="92781-116">團隊並沒有為這些變化做好準備，而且可能會遭遇學習曲線的問題。</span><span class="sxs-lookup"><span data-stu-id="92781-116">The team isn’t really prepared for these changes and is likely to struggle with the learning curve.</span></span>

## <a name="business-characteristics"></a><span data-ttu-id="92781-117">商務特性</span><span class="sxs-lookup"><span data-stu-id="92781-117">Business characteristics</span></span>

<span data-ttu-id="92781-118">公司具備下列商務設定檔：</span><span class="sxs-lookup"><span data-stu-id="92781-118">The company has the following business profile:</span></span>

- <span data-ttu-id="92781-119">所有的銷售與營運都位於單一國家/地區，且全球客戶的百分比低。</span><span class="sxs-lookup"><span data-stu-id="92781-119">All sales and operations reside in a single country, with a low percentage of global customers.</span></span>
- <span data-ttu-id="92781-120">企業當作單一業務單位營運，預算會隨著銷售、行銷、營運和 IT 等職責調整。</span><span class="sxs-lookup"><span data-stu-id="92781-120">The business operates as a single business unit, with budget aligned to functions, including Sales, Marketing, Operations, and IT.</span></span>
- <span data-ttu-id="92781-121">企業將大多數的 IT 視為資本流失或成本中心。</span><span class="sxs-lookup"><span data-stu-id="92781-121">The business views most of IT as a capital drain or a cost center.</span></span>

## <a name="current-state"></a><span data-ttu-id="92781-122">目前狀態</span><span class="sxs-lookup"><span data-stu-id="92781-122">Current state</span></span>

<span data-ttu-id="92781-123">以下是該公司的 IT 和雲端作業目前的狀態：</span><span class="sxs-lookup"><span data-stu-id="92781-123">Here is the current state of the company’s IT and cloud operations:</span></span>

- <span data-ttu-id="92781-124">IT 負責運作兩個託管的基礎結構環境。</span><span class="sxs-lookup"><span data-stu-id="92781-124">IT operates two hosted infrastructure environments.</span></span> <span data-ttu-id="92781-125">其中一個環境含有生產資產。</span><span class="sxs-lookup"><span data-stu-id="92781-125">One environment contains production assets.</span></span> <span data-ttu-id="92781-126">另一個環境則含有災害復原和一些開發/測試資產。</span><span class="sxs-lookup"><span data-stu-id="92781-126">The second environment contains disaster recovery and some dev/test assets.</span></span> <span data-ttu-id="92781-127">這些環境是由兩個不同的提供者所託管。</span><span class="sxs-lookup"><span data-stu-id="92781-127">These environments are hosted by two different providers.</span></span> <span data-ttu-id="92781-128">IT 將這兩個資料中心分別歸類為 Prod 和 DR。</span><span class="sxs-lookup"><span data-stu-id="92781-128">IT refers to these two datacenters as Prod and DR respectively.</span></span>
- <span data-ttu-id="92781-129">IT 將所有使用者電子郵件帳戶移轉至 Office 365，藉此進入雲端。</span><span class="sxs-lookup"><span data-stu-id="92781-129">IT entered the cloud by migrating all end-user email accounts to Office 365.</span></span> <span data-ttu-id="92781-130">此移轉已在六個月前完成。</span><span class="sxs-lookup"><span data-stu-id="92781-130">This migration was completed six months ago.</span></span> <span data-ttu-id="92781-131">其他一些 IT 資產已部署至雲端。</span><span class="sxs-lookup"><span data-stu-id="92781-131">Few other IT assets have been deployed to the cloud.</span></span>
- <span data-ttu-id="92781-132">應用程式開發小組致力於開發/測試能力，以了解雲端原生功能。</span><span class="sxs-lookup"><span data-stu-id="92781-132">The application development teams are working in a dev/test capacity to learn about cloud native capabilities.</span></span>
- <span data-ttu-id="92781-133">商業智慧 (BI) 小組將試驗雲端中的巨量資料，以及新平台上的資料鑑藏。</span><span class="sxs-lookup"><span data-stu-id="92781-133">The business intelligence (BI) team is experimenting with big data in the cloud and curation of data on new platforms.</span></span>
- <span data-ttu-id="92781-134">公司有一個定義鬆散的原則，規定無法在雲端託管客戶的個人識別資訊 (PII) 和財務資料，進而限制了目前部署中的關鍵任務應用程式。</span><span class="sxs-lookup"><span data-stu-id="92781-134">The company has a loosely defined policy stating that customer personally identifiable information (PII) and financial data cannot be hosted in the cloud, which limits mission-critical applications in the current deployments.</span></span>
- <span data-ttu-id="92781-135">IT 投資主要由資本支出控制 (CapEx)。</span><span class="sxs-lookup"><span data-stu-id="92781-135">IT investments are controlled largely by capital expense (CapEx).</span></span> <span data-ttu-id="92781-136">這些投資每年規劃一次。</span><span class="sxs-lookup"><span data-stu-id="92781-136">Those investments are planned yearly.</span></span> <span data-ttu-id="92781-137">在過去幾年中，投資僅包含基本維護需求。</span><span class="sxs-lookup"><span data-stu-id="92781-137">In the past several years, investments have included little more than basic maintenance requirements.</span></span>

## <a name="future-state"></a><span data-ttu-id="92781-138">未來的狀態</span><span class="sxs-lookup"><span data-stu-id="92781-138">Future state</span></span>

<span data-ttu-id="92781-139">預計未來幾年將發生下列變化：</span><span class="sxs-lookup"><span data-stu-id="92781-139">The following changes are anticipated over the next several years:</span></span>

- <span data-ttu-id="92781-140">CIO 將審查 PII 和財務資料的相關原則，以實現未來的狀態目標。</span><span class="sxs-lookup"><span data-stu-id="92781-140">The CIO is reviewing the policy on PII and financial data to allow for the future state goals.</span></span>
- <span data-ttu-id="92781-141">應用程式開發和 BI 團隊希望在未來的 24 個月內，根據客戶業務開發和新產品的願景，將雲端式解決方案發佈到生產環境中。</span><span class="sxs-lookup"><span data-stu-id="92781-141">The application development and BI teams want to release cloud-based solutions to production over the next 24 months based on the vision for customer engagement and new products.</span></span>
- <span data-ttu-id="92781-142">今年，IT 團隊會將 2,000 個 VM 移轉至雲端，來完成淘汰 DR 資料中心的災難復原工作負載。</span><span class="sxs-lookup"><span data-stu-id="92781-142">This year, the IT team will finish retiring the disaster recovery workloads of the DR datacenter by migrating 2,000 VMs to the cloud.</span></span> <span data-ttu-id="92781-143">預計這將在未來五年內節省約 2500 萬美元的成本。</span><span class="sxs-lookup"><span data-stu-id="92781-143">This is expected to produce an estimated $25M USD cost savings over the next five years.</span></span>
    <span data-ttu-id="92781-144">![內部部署成本與 Azure 成本相比，未來五年的回報率為 2500 萬美元](../../../_images/governance/calculator-small-to-medium-enterprise.png)</span><span class="sxs-lookup"><span data-stu-id="92781-144">![On-premise costs versus Azure costs demonstrating a return of $25M USD over the next five years](../../../_images/governance/calculator-small-to-medium-enterprise.png)</span></span>
- <span data-ttu-id="92781-145">公司計劃將承諾的資本支出重新定位為 IT 內的營運支出 (OpEx)，藉此改變 IT 投資的方式。</span><span class="sxs-lookup"><span data-stu-id="92781-145">The company plans to change how it makes IT investments by repositioning the committed CapEx as an operational expense (OpEx) within IT.</span></span> <span data-ttu-id="92781-146">這項改變將提供更好的成本控制，並使 IT 能夠加速完成其他計劃的工作。</span><span class="sxs-lookup"><span data-stu-id="92781-146">This change will provide greater cost control and enable IT to accelerate other planned efforts.</span></span>

## <a name="next-steps"></a><span data-ttu-id="92781-147">後續步驟</span><span class="sxs-lookup"><span data-stu-id="92781-147">Next steps</span></span>

<span data-ttu-id="92781-148">公司已經制訂一項公司原則使治理實施具體化。</span><span class="sxs-lookup"><span data-stu-id="92781-148">The company has developed a corporate policy to shape the governance implementation.</span></span> <span data-ttu-id="92781-149">公司原則推動了許多技術決策。</span><span class="sxs-lookup"><span data-stu-id="92781-149">The corporate policy drives many of the technical decisions.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="92781-150">檢閱最初的公司原則</span><span class="sxs-lookup"><span data-stu-id="92781-150">Review the initial corporate policy</span></span>](./initial-corporate-policy.md)

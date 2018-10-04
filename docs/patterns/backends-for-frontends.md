---
title: 前端模式的後端
description: 建立由特定前端應用程式或介面取用的個別後端服務。
author: dragon119
ms.date: 06/23/2017
ms.openlocfilehash: a0dbc9ab58aa218f6faf40b70dad1bdc22d71458
ms.sourcegitcommit: 94d50043db63416c4d00cebe927a0c88f78c3219
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2018
ms.locfileid: "47428783"
---
# <a name="backends-for-frontends-pattern"></a><span data-ttu-id="942d9-103">前端模式的後端</span><span class="sxs-lookup"><span data-stu-id="942d9-103">Backends for Frontends pattern</span></span>

<span data-ttu-id="942d9-104">建立由特定前端應用程式或介面取用的個別後端服務。</span><span class="sxs-lookup"><span data-stu-id="942d9-104">Create separate backend services to be consumed by specific frontend applications or interfaces.</span></span> <span data-ttu-id="942d9-105">想要避免為多個介面自訂單一後端時，此模式相當有用。</span><span class="sxs-lookup"><span data-stu-id="942d9-105">This pattern is useful when you want to avoid customizing a single backend for multiple interfaces.</span></span> <span data-ttu-id="942d9-106">此模式最早是由 Sam Newman 所提及。</span><span class="sxs-lookup"><span data-stu-id="942d9-106">This pattern was first described by Sam Newman.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="942d9-107">內容和問題</span><span class="sxs-lookup"><span data-stu-id="942d9-107">Context and problem</span></span>

<span data-ttu-id="942d9-108">應用程式一開始的目標可能是桌面 Web UI。</span><span class="sxs-lookup"><span data-stu-id="942d9-108">An application may initially be targeted at a desktop web UI.</span></span> <span data-ttu-id="942d9-109">一般而言，後端服務會平行開發，提供該 UI 所需的功能。</span><span class="sxs-lookup"><span data-stu-id="942d9-109">Typically, a backend service is developed in parallel that provides the features needed for that UI.</span></span> <span data-ttu-id="942d9-110">隨著應用程式的使用者數量成長，即會開發必須與相同後端互動的行動應用程式。</span><span class="sxs-lookup"><span data-stu-id="942d9-110">As the application's user base grows, a mobile application is developed that must interact with the same backend.</span></span> <span data-ttu-id="942d9-111">後端服務會變成一般用途的後端，同時為桌面和行動裝置介面的需求提供服務。</span><span class="sxs-lookup"><span data-stu-id="942d9-111">The backend service becomes a general-purpose backend, serving the requirements of both the desktop and mobile interfaces.</span></span>

<span data-ttu-id="942d9-112">但行動裝置的功能與明顯桌面瀏覽器不同，就螢幕大小、效能與顯示限制而言。</span><span class="sxs-lookup"><span data-stu-id="942d9-112">But the capabilities of a mobile device differ significantly from a desktop browser, in terms of screen size, performance, and display limitations.</span></span> <span data-ttu-id="942d9-113">因此，行動應用程式後端的需求與桌面 Web UI 的需求不同。</span><span class="sxs-lookup"><span data-stu-id="942d9-113">As a result, the requirements for a mobile application backend differ from the desktop web UI.</span></span> 

<span data-ttu-id="942d9-114">這些差異導致後端的競爭需求。</span><span class="sxs-lookup"><span data-stu-id="942d9-114">These differences result in competing requirements for the backend.</span></span> <span data-ttu-id="942d9-115">後端需要進行定期重大變更，才能同時對桌面 Web UI 和行動應用程式提供服務。</span><span class="sxs-lookup"><span data-stu-id="942d9-115">The backend requires regular and significant changes to serve both the desktop web UI and the mobile application.</span></span> <span data-ttu-id="942d9-116">通常，個別介面小組會在每個前端上運作，導致後端變成開發程序中的瓶頸。</span><span class="sxs-lookup"><span data-stu-id="942d9-116">Often, separate interface teams work on each frontend, causing the backend to become a bottleneck in the development process.</span></span> <span data-ttu-id="942d9-117">衝突的更新需求以及需要讓服務保持對這兩個前端運作，會導致花費大量精力於單一可部署資源上。</span><span class="sxs-lookup"><span data-stu-id="942d9-117">Conflicting update requirements, and the need to keep the service working for both frontends, can result in spending a lot of effort on a single deployable resource.</span></span>

![](./_images/backend-for-frontend.png) 

<span data-ttu-id="942d9-118">因為開發活動的重點在於後端服務，可能會建立個別小組來管理和維護後端。</span><span class="sxs-lookup"><span data-stu-id="942d9-118">As the development activity focuses on the backend service, a separate team may be created to manage and maintain the backend.</span></span> <span data-ttu-id="942d9-119">最後，這會導致介面和後端開發小組之間無法連繫，在後端小組上增加負擔，以平衡不同 UI 小組之間的競爭需求。</span><span class="sxs-lookup"><span data-stu-id="942d9-119">Ultimately, this results in a disconnect between the interface and backend development teams, placing a burden on the backend team to balance the competing requirements of the different UI teams.</span></span> <span data-ttu-id="942d9-120">當一個介面小組需要對後端進行變更時，必須先向其他介面小組驗證這些變更，之後才能將變更整合到後端。</span><span class="sxs-lookup"><span data-stu-id="942d9-120">When one interface team requires changes to the backend, those changes must be validated with other interface teams before they can be integrated into the backend.</span></span> 

## <a name="solution"></a><span data-ttu-id="942d9-121">解決方法</span><span class="sxs-lookup"><span data-stu-id="942d9-121">Solution</span></span>

<span data-ttu-id="942d9-122">每個使用者介面建立一個後端。</span><span class="sxs-lookup"><span data-stu-id="942d9-122">Create one backend per user interface.</span></span> <span data-ttu-id="942d9-123">微調每個後端的行為與效能，以最符合前端環境的需求，而不需擔心影響其他前端的體驗。</span><span class="sxs-lookup"><span data-stu-id="942d9-123">Fine tune the behavior and performance of each backend to best match the needs of the frontend environment, without worrying about affecting other frontend experiences.</span></span>

![](./_images/backend-for-frontend-example.png) 

<span data-ttu-id="942d9-124">因為每個後端相對於一個特定介面，可以為該介面最佳化。</span><span class="sxs-lookup"><span data-stu-id="942d9-124">Because each backend is specific to one interface, it can be optimized for that interface.</span></span> <span data-ttu-id="942d9-125">如此一來，它會較小、較簡單且可能快於嘗試滿足所有介面需求的一般後端。</span><span class="sxs-lookup"><span data-stu-id="942d9-125">As a result, it will be smaller, less complex, and likely faster than a generic backend that tries to satisfy the requirements for all interfaces.</span></span> <span data-ttu-id="942d9-126">每個介面小組有控制自己後端的自主權，並且不依賴於集中式後端開發小組。</span><span class="sxs-lookup"><span data-stu-id="942d9-126">Each interface team has autonomy to control their own backend and doesn't rely on a centralized backend development team.</span></span> <span data-ttu-id="942d9-127">這讓介面小組對於後端的語言選擇、發佈日程、工作負載的優先順序以及功能整合有彈性。</span><span class="sxs-lookup"><span data-stu-id="942d9-127">This gives the interface team flexibility in language selection, release cadence, prioritization of workload, and feature integration in their backend.</span></span>

<span data-ttu-id="942d9-128">如需詳細資訊，請參閱[模式：前端的後端](https://samnewman.io/patterns/architectural/bff/)。</span><span class="sxs-lookup"><span data-stu-id="942d9-128">For more information, see [Pattern: Backends For Frontends](https://samnewman.io/patterns/architectural/bff/).</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="942d9-129">問題和考量</span><span class="sxs-lookup"><span data-stu-id="942d9-129">Issues and considerations</span></span>

- <span data-ttu-id="942d9-130">考慮要部署的後端數量。</span><span class="sxs-lookup"><span data-stu-id="942d9-130">Consider how many backends to deploy.</span></span>
- <span data-ttu-id="942d9-131">如果不同的介面 (例如行動用戶端) 將進行相同的要求，請考慮是否需要為每個介面實作後端，或單一後端即已足夠。</span><span class="sxs-lookup"><span data-stu-id="942d9-131">If different interfaces (such as mobile clients) will make the same requests, consider whether it is necessary to implement a backend for each interface, or if a single backend will suffice.</span></span>
- <span data-ttu-id="942d9-132">實作此模式時，服務間的程式碼很有高的可能性會重複。</span><span class="sxs-lookup"><span data-stu-id="942d9-132">Code duplication across services is highly likely when implementing this pattern.</span></span>
- <span data-ttu-id="942d9-133">以前端為焦點的後端服務應該只包含用戶端的特定邏輯和行為。</span><span class="sxs-lookup"><span data-stu-id="942d9-133">Frontend-focused backend services should only contain client-specific logic and behavior.</span></span> <span data-ttu-id="942d9-134">應該在您的應用程式中任意處管理一般商務邏輯和其他通用功能。</span><span class="sxs-lookup"><span data-stu-id="942d9-134">General business logic and other global features should be managed elsewhere in your application.</span></span>
- <span data-ttu-id="942d9-135">請思考此模式如何反映在開發小組的責任中。</span><span class="sxs-lookup"><span data-stu-id="942d9-135">Think about how this pattern might be reflected in the responsibilities of a development team.</span></span>
- <span data-ttu-id="942d9-136">考慮實作此模式需要的時間。</span><span class="sxs-lookup"><span data-stu-id="942d9-136">Consider how long it will take to implement this pattern.</span></span> <span data-ttu-id="942d9-137">在您繼續支援現有的一般後端的同時，建立新後端的投入時間是否會衍生技術債務？</span><span class="sxs-lookup"><span data-stu-id="942d9-137">Will the effort of building the new backends incur technical debt, while you continue to support the existing generic backend?</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="942d9-138">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="942d9-138">When to use this pattern</span></span>

<span data-ttu-id="942d9-139">使用此模式的時機包括：</span><span class="sxs-lookup"><span data-stu-id="942d9-139">Use this pattern when:</span></span>

- <span data-ttu-id="942d9-140">共用或一般用途的後端服務必須以大量的開發負荷加以維護。</span><span class="sxs-lookup"><span data-stu-id="942d9-140">A shared or general purpose backend service must be maintained with significant development overhead.</span></span>
- <span data-ttu-id="942d9-141">您想要針對特定用戶端介面的需求將後端最佳化。</span><span class="sxs-lookup"><span data-stu-id="942d9-141">You want to optimize the backend for the requirements of specific client interfaces.</span></span>
- <span data-ttu-id="942d9-142">自訂作業會對一般用途的後端進行，以容納多個介面。</span><span class="sxs-lookup"><span data-stu-id="942d9-142">Customizations are made to a general-purpose backend to accommodate multiple interfaces.</span></span>
- <span data-ttu-id="942d9-143">替代語言更適合用於不同的使用者介面的後端。</span><span class="sxs-lookup"><span data-stu-id="942d9-143">An alternative language is better suited for the backend of a different user interface.</span></span>

<span data-ttu-id="942d9-144">此模式可能不適合下列時機：</span><span class="sxs-lookup"><span data-stu-id="942d9-144">This pattern may not be suitable:</span></span>

- <span data-ttu-id="942d9-145">當介面對後端進行相同或類似的要求時。</span><span class="sxs-lookup"><span data-stu-id="942d9-145">When interfaces make the same or similar requests to the backend.</span></span>
- <span data-ttu-id="942d9-146">只有一個介面用來與後端互動時。</span><span class="sxs-lookup"><span data-stu-id="942d9-146">When only one interface is used to interact with the backend.</span></span>

## <a name="related-guidance"></a><span data-ttu-id="942d9-147">相關的指引</span><span class="sxs-lookup"><span data-stu-id="942d9-147">Related guidance</span></span>

- [<span data-ttu-id="942d9-148">閘道彙總模式</span><span class="sxs-lookup"><span data-stu-id="942d9-148">Gateway Aggregation pattern</span></span>](./gateway-aggregation.md)
- [<span data-ttu-id="942d9-149">閘道卸載模式</span><span class="sxs-lookup"><span data-stu-id="942d9-149">Gateway Offloading pattern</span></span>](./gateway-offloading.md)
- [<span data-ttu-id="942d9-150">閘道路由模式</span><span class="sxs-lookup"><span data-stu-id="942d9-150">Gateway Routing pattern</span></span>](./gateway-routing.md)



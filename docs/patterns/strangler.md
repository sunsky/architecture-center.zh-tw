---
title: 扼制模式
titleSuffix: Cloud Design Patterns
description: 透過將功能的特定片段逐漸取代成新的應用程式和服務，來逐步移轉舊有系統。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: f139d368c98256c0190753930983a47df81a5134
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54480543"
---
# <a name="strangler-pattern"></a><span data-ttu-id="52e5e-104">扼制模式</span><span class="sxs-lookup"><span data-stu-id="52e5e-104">Strangler pattern</span></span>

<span data-ttu-id="52e5e-105">透過將功能的特定片段逐漸取代成新的應用程式和服務，來逐步移轉舊有系統。</span><span class="sxs-lookup"><span data-stu-id="52e5e-105">Incrementally migrate a legacy system by gradually replacing specific pieces of functionality with new applications and services.</span></span> <span data-ttu-id="52e5e-106">隨著舊有系統的功能被取代，新系統最終會取代舊系統所有的功能，扼制舊系統或功能，讓您將它解除任務。</span><span class="sxs-lookup"><span data-stu-id="52e5e-106">As features from the legacy system are replaced, the new system eventually replaces all of the old system's features, strangling the old system and allowing you to decommission it.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="52e5e-107">內容和問題</span><span class="sxs-lookup"><span data-stu-id="52e5e-107">Context and problem</span></span>

<span data-ttu-id="52e5e-108">隨著系統老化，開發工具、裝載技術、甚至是建置它們的系統架構，都可能變得越來越過時。</span><span class="sxs-lookup"><span data-stu-id="52e5e-108">As systems age, the development tools, hosting technology, and even system architectures they were built on can become increasingly obsolete.</span></span> <span data-ttu-id="52e5e-109">因為新的特色與功能加入，這些應用程式的複雜性會大幅增加，使其更難以維護或加入新功能。</span><span class="sxs-lookup"><span data-stu-id="52e5e-109">As new features and functionality are added, the complexity of these applications can increase dramatically, making them harder to maintain or add new features to.</span></span>

<span data-ttu-id="52e5e-110">完全取代複雜的系統可能會是個大工程。</span><span class="sxs-lookup"><span data-stu-id="52e5e-110">Completely replacing a complex system can be a huge undertaking.</span></span> <span data-ttu-id="52e5e-111">通常，您將需要漸進式移轉到新的系統，同時保留舊系統來處理尚未移轉的功能。</span><span class="sxs-lookup"><span data-stu-id="52e5e-111">Often, you will need a gradual migration to a new system, while keeping the old system to handle features that haven't been migrated yet.</span></span> <span data-ttu-id="52e5e-112">但是，執行兩個不同版本的應用程式，表示用戶端必須知道特定功能在哪裡。</span><span class="sxs-lookup"><span data-stu-id="52e5e-112">However, running two separate versions of an application means that clients have to know where particular features are located.</span></span> <span data-ttu-id="52e5e-113">每次移轉功能或服務時，需要讓用戶端知道新的位置。</span><span class="sxs-lookup"><span data-stu-id="52e5e-113">Every time a feature or service is migrated, clients need to be updated to point to the new location.</span></span>

## <a name="solution"></a><span data-ttu-id="52e5e-114">解決方法</span><span class="sxs-lookup"><span data-stu-id="52e5e-114">Solution</span></span>

<span data-ttu-id="52e5e-115">將功能的特定片段逐漸取代成新的應用程式和服務。</span><span class="sxs-lookup"><span data-stu-id="52e5e-115">Incrementally replace specific pieces of functionality with new applications and services.</span></span> <span data-ttu-id="52e5e-116">建立外觀以攔截前往後端舊版系統的要求。</span><span class="sxs-lookup"><span data-stu-id="52e5e-116">Create a façade that intercepts requests going to the backend legacy system.</span></span> <span data-ttu-id="52e5e-117">外觀會將這些要求路由傳送至舊版應用程式或新的服務。</span><span class="sxs-lookup"><span data-stu-id="52e5e-117">The façade routes these requests either to the legacy application or the new services.</span></span> <span data-ttu-id="52e5e-118">現有功能可以逐漸移轉至新系統，取用者可以繼續使用相同的介面，不會察覺任何移轉的發生。</span><span class="sxs-lookup"><span data-stu-id="52e5e-118">Existing features can be migrated to the new system gradually, and consumers can continue using the same interface, unaware that any migration has taken place.</span></span>

![扼制模式圖](./_images/strangler.png)

<span data-ttu-id="52e5e-120">此模式有助於將移轉的風險降至最低，並將開發分散在一段時間內。</span><span class="sxs-lookup"><span data-stu-id="52e5e-120">This pattern helps to minimize risk from the migration, and spread the development effort over time.</span></span> <span data-ttu-id="52e5e-121">使用外觀安全地將使用者路由傳送至正確的應用程式，您可以任何您喜歡的步調將功能新增至新的系統，同時確保舊版應用程式持續運作。</span><span class="sxs-lookup"><span data-stu-id="52e5e-121">With the façade safely routing users to the correct application, you can add functionality to the new system at whatever pace you like, while ensuring the legacy application continues to function.</span></span> <span data-ttu-id="52e5e-122">經過一段時間，當功能都移轉到新的系統，舊系統終將被「扼制」，且不再需要它。</span><span class="sxs-lookup"><span data-stu-id="52e5e-122">Over time, as features are migrated to the new system, the legacy system is eventually "strangled" and is no longer necessary.</span></span> <span data-ttu-id="52e5e-123">完成此程序之後，可以安全地淘汰舊系統。</span><span class="sxs-lookup"><span data-stu-id="52e5e-123">Once this process is complete, the legacy system can safely be retired.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="52e5e-124">問題和考量</span><span class="sxs-lookup"><span data-stu-id="52e5e-124">Issues and considerations</span></span>

- <span data-ttu-id="52e5e-125">請考慮如何處理新舊系統可能同時使用的服務和資料存放區。</span><span class="sxs-lookup"><span data-stu-id="52e5e-125">Consider how to handle services and data stores that are potentially used by both new and legacy systems.</span></span> <span data-ttu-id="52e5e-126">確定兩者可以同時存取這些資源。</span><span class="sxs-lookup"><span data-stu-id="52e5e-126">Make sure both can access these resources side-by-side.</span></span>
- <span data-ttu-id="52e5e-127">建構新應用程式和服務的方式，使它們在未來的扼制移轉中可以輕鬆地被攔截和取代。</span><span class="sxs-lookup"><span data-stu-id="52e5e-127">Structure new applications and services in a way that they can easily be intercepted and replaced in future strangler migrations.</span></span>
- <span data-ttu-id="52e5e-128">在未來移轉完成時，扼制外觀將會消失或發展成舊版用戶端的配接器。</span><span class="sxs-lookup"><span data-stu-id="52e5e-128">At some point, when the migration is complete, the strangler façade will either go away or evolve into an adaptor for legacy clients.</span></span>
- <span data-ttu-id="52e5e-129">請確定外觀可配合移轉。</span><span class="sxs-lookup"><span data-stu-id="52e5e-129">Make sure the façade keeps up with the migration.</span></span>
- <span data-ttu-id="52e5e-130">請確定外觀就不會成為單點失敗或效能瓶頸。</span><span class="sxs-lookup"><span data-stu-id="52e5e-130">Make sure the façade doesn't become a single point of failure or a performance bottleneck.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="52e5e-131">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="52e5e-131">When to use this pattern</span></span>

<span data-ttu-id="52e5e-132">逐漸將後端應用程式移轉至新架構時，請使用此模式。</span><span class="sxs-lookup"><span data-stu-id="52e5e-132">Use this pattern when gradually migrating a back-end application to a new architecture.</span></span>

<span data-ttu-id="52e5e-133">此模式可能不適合下列時機：</span><span class="sxs-lookup"><span data-stu-id="52e5e-133">This pattern may not be suitable:</span></span>

- <span data-ttu-id="52e5e-134">當無法攔截送到後端系統的要求。</span><span class="sxs-lookup"><span data-stu-id="52e5e-134">When requests to the back-end system cannot be intercepted.</span></span>
- <span data-ttu-id="52e5e-135">整批取代的複雜度較低的小型系統。</span><span class="sxs-lookup"><span data-stu-id="52e5e-135">For smaller systems where the complexity of wholesale replacement is low.</span></span>

## <a name="related-guidance"></a><span data-ttu-id="52e5e-136">相關的指引</span><span class="sxs-lookup"><span data-stu-id="52e5e-136">Related guidance</span></span>

- <span data-ttu-id="52e5e-137">[StranglerApplication](https://www.martinfowler.com/bliki/StranglerApplication.html) 上 Martin Fowler 的部落格文章</span><span class="sxs-lookup"><span data-stu-id="52e5e-137">Martin Fowler's blog post on [StranglerApplication](https://www.martinfowler.com/bliki/StranglerApplication.html)</span></span>

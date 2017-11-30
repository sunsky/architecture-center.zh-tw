---
title: "扼制模式"
description: "透過將功能的特定片段逐漸取代成新的應用程式和服務，來逐步移轉舊有系統。"
author: dragon119
ms.date: 06/23/2017
ms.openlocfilehash: d03e8a1ef9077b6e00ea5a17423bf7e09b68111a
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="strangler-pattern"></a><span data-ttu-id="ee1f7-103">扼制模式</span><span class="sxs-lookup"><span data-stu-id="ee1f7-103">Strangler pattern</span></span>

<span data-ttu-id="ee1f7-104">透過將功能的特定片段逐漸取代成新的應用程式和服務，來逐步移轉舊有系統。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-104">Incrementally migrate a legacy system by gradually replacing specific pieces of functionality with new applications and services.</span></span> <span data-ttu-id="ee1f7-105">隨著舊有系統的功能被取代，新系統最終會取代舊系統所有的功能，扼制舊系統或功能，讓您將它解除任務。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-105">As features from the legacy system are replaced, the new system eventually replaces all of the old system's features, strangling the old system and allowing you to decommission it.</span></span> 

## <a name="context-and-problem"></a><span data-ttu-id="ee1f7-106">內容和問題</span><span class="sxs-lookup"><span data-stu-id="ee1f7-106">Context and problem</span></span>

<span data-ttu-id="ee1f7-107">隨著系統老化，開發工具、裝載技術、甚至是建置它們的系統架構，都可能變得越來越過時。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-107">As systems age, the development tools, hosting technology, and even system architectures they were built on can become increasingly obsolete.</span></span> <span data-ttu-id="ee1f7-108">因為新的特色與功能加入，這些應用程式的複雜性會大幅增加，使其更難以維護或加入新功能。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-108">As new features and functionality are added, the complexity of these applications can increase dramatically, making them harder to maintain or add new features to.</span></span>

<span data-ttu-id="ee1f7-109">完全取代複雜的系統可能會是個大工程。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-109">Completely replacing a complex system can be a huge undertaking.</span></span> <span data-ttu-id="ee1f7-110">通常，您將需要漸進式移轉到新的系統，同時保留舊系統來處理尚未移轉的功能。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-110">Often, you will need a gradual migration to a new system, while keeping the old system to handle features that haven't been migrated yet.</span></span> <span data-ttu-id="ee1f7-111">但是，執行兩個不同版本的應用程式，表示用戶端必須知道特定功能在哪裡。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-111">However, running two separate versions of an application means that clients have to know where particular features are located.</span></span> <span data-ttu-id="ee1f7-112">每次移轉功能或服務時，需要讓用戶端知道新的位置。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-112">Every time a feature or service is migrated, clients need to be updated to point to the new location.</span></span>

## <a name="solution"></a><span data-ttu-id="ee1f7-113">方案</span><span class="sxs-lookup"><span data-stu-id="ee1f7-113">Solution</span></span>

<span data-ttu-id="ee1f7-114">將功能的特定片段逐漸取代成新的應用程式和服務。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-114">Incrementally replace specific pieces of functionality with new applications and services.</span></span> <span data-ttu-id="ee1f7-115">建立 façade 來攔截送至後端舊系統的要求。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-115">Create a façade that intercepts requests going to the backend legacy system.</span></span> <span data-ttu-id="ee1f7-116">façade 會將這些要求路由傳送至舊版應用程式或新的服務。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-116">The façade routes these requests either to the legacy application or the new services.</span></span> <span data-ttu-id="ee1f7-117">現有功能可以逐漸移轉至新系統，取用者可以繼續使用相同的介面，不會察覺任何移轉的發生。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-117">Existing features can be migrated to the new system gradually, and consumers can continue using the same interface, unaware that any migration has taken place.</span></span>

![](./_images/strangler.png)  

<span data-ttu-id="ee1f7-118">此模式有助於將移轉的風險降至最低，並將開發分散在一段時間內。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-118">This pattern helps to minimize risk from the migration, and spread the development effort over time.</span></span> <span data-ttu-id="ee1f7-119">利用 façade 安全地將使用者路由至正確的應用程式，以您喜歡的步調將功能加入新的系統，同時確保舊版應用程式仍持續運作。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-119">With the façade safely routing users to the correct application, you can add functionality to the new system at whatever pace you like, while ensuring the legacy application continues to function.</span></span> <span data-ttu-id="ee1f7-120">經過一段時間，當功能都移轉到新的系統，舊系統終將被「扼制」，且不再需要它。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-120">Over time, as features are migrated to the new system, the legacy system is eventually "strangled" and is no longer necessary.</span></span> <span data-ttu-id="ee1f7-121">完成此程序之後，可以安全地淘汰舊系統。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-121">Once this process is complete, the legacy system can safely be retired.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="ee1f7-122">問題和考量</span><span class="sxs-lookup"><span data-stu-id="ee1f7-122">Issues and considerations</span></span>

- <span data-ttu-id="ee1f7-123">請考慮如何處理新舊系統可能同時使用的服務和資料存放區。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-123">Consider how to handle services and data stores that are potentially used by both new and legacy systems.</span></span> <span data-ttu-id="ee1f7-124">確定兩者可以同時存取這些資源。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-124">Make sure both can access these resources side-by-side.</span></span>
- <span data-ttu-id="ee1f7-125">建構新應用程式和服務的方式，使它們在未來的扼制移轉中可以輕鬆地被攔截和取代。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-125">Structure new applications and services in a way that they can easily be intercepted and replaced in future strangler migrations.</span></span>
- <span data-ttu-id="ee1f7-126">在某些時候，當移轉完成時，抑制 façade 將會消失或演變成舊版用戶端的配接器。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-126">At some point, when the migration is complete, the strangler façade will either go away or evolve into an adaptor for legacy clients.</span></span>
- <span data-ttu-id="ee1f7-127">請確定 façade 跟得上移轉。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-127">Make sure the façade keeps up with the migration.</span></span>
- <span data-ttu-id="ee1f7-128">請確定外觀就不會成為單點失敗或效能瓶頸。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-128">Make sure the façade doesn't become a single point of failure or a performance bottleneck.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="ee1f7-129">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="ee1f7-129">When to use this pattern</span></span>

<span data-ttu-id="ee1f7-130">逐漸將後端應用程式移轉至新架構時，請使用此模式。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-130">Use this pattern when gradually migrating a back-end application to a new architecture.</span></span>

<span data-ttu-id="ee1f7-131">此模式可能不適合下列時機：</span><span class="sxs-lookup"><span data-stu-id="ee1f7-131">This pattern may not be suitable:</span></span>

- <span data-ttu-id="ee1f7-132">當無法攔截送到後端系統的要求。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-132">When requests to the back-end system cannot be intercepted.</span></span>
- <span data-ttu-id="ee1f7-133">整批取代的複雜度較低的小型系統。</span><span class="sxs-lookup"><span data-stu-id="ee1f7-133">For smaller systems where the complexity of wholesale replacement is low.</span></span>

## <a name="related-guidance"></a><span data-ttu-id="ee1f7-134">相關的指引</span><span class="sxs-lookup"><span data-stu-id="ee1f7-134">Related guidance</span></span>

- [<span data-ttu-id="ee1f7-135">防損毀層模式</span><span class="sxs-lookup"><span data-stu-id="ee1f7-135">Anti-Corruption Layer pattern</span></span>](./anti-corruption-layer.md)
- [<span data-ttu-id="ee1f7-136">閘道路由模式</span><span class="sxs-lookup"><span data-stu-id="ee1f7-136">Gateway Routing pattern</span></span>](./gateway-routing.md)


 


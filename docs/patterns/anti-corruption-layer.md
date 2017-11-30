---
title: "防損毀層模式"
description: "在現代應用程式和舊版系統間實作外觀或配接器層。"
author: dragon119
ms.date: 06/23/2017
ms.openlocfilehash: 590d5f3676c92f5f18661360106e2b2fdd4efbe1
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="anti-corruption-layer-pattern"></a><span data-ttu-id="bef05-103">防損毀層模式</span><span class="sxs-lookup"><span data-stu-id="bef05-103">Anti-Corruption Layer pattern</span></span>

<span data-ttu-id="bef05-104">在現代應用程式和其依存的舊版系統間實作外觀或配接器層。</span><span class="sxs-lookup"><span data-stu-id="bef05-104">Implement a façade or adapter layer between a modern application and a legacy system that it depends on.</span></span> <span data-ttu-id="bef05-105">此層會轉譯現代應用程式與舊版系統之間的要求。</span><span class="sxs-lookup"><span data-stu-id="bef05-105">This layer translates requests between the modern application and the legacy system.</span></span> <span data-ttu-id="bef05-106">您可以使用此模式來確保應用程式的設計不會受限於舊版系統上的相依性。</span><span class="sxs-lookup"><span data-stu-id="bef05-106">Use this pattern to ensure that an application's design is not limited by dependencies on legacy systems.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="bef05-107">內容和問題</span><span class="sxs-lookup"><span data-stu-id="bef05-107">Context and problem</span></span>

<span data-ttu-id="bef05-108">大部分的應用程式依賴其他系統提供某些資料或功能。</span><span class="sxs-lookup"><span data-stu-id="bef05-108">Most applications rely on other systems for some data or functionality.</span></span> <span data-ttu-id="bef05-109">例如，將舊版應用程式移轉至現代系統時，仍可能需要現有的舊版資源。</span><span class="sxs-lookup"><span data-stu-id="bef05-109">For example, when a legacy application is migrated to a modern system, it may still need existing legacy resources.</span></span> <span data-ttu-id="bef05-110">新功能必須能夠呼叫舊版系統。</span><span class="sxs-lookup"><span data-stu-id="bef05-110">New features must be able to call the legacy system.</span></span> <span data-ttu-id="bef05-111">對於較大型應用程式的不同功能會隨著時間移動到現代系統的漸進式移轉尤其是如此。</span><span class="sxs-lookup"><span data-stu-id="bef05-111">This is especially true of gradual migrations, where different features of a larger application are moved to a modern system over time.</span></span>

<span data-ttu-id="bef05-112">通常這些舊版系統會為品質問題所苦，例如迂迴的資料結構描述或過時的 API。</span><span class="sxs-lookup"><span data-stu-id="bef05-112">Often these legacy systems suffer from quality issues such as convoluted data schemas or obsolete APIs.</span></span> <span data-ttu-id="bef05-113">舊版系統中所使用的功能和技術可能與更現代系統大不相同。</span><span class="sxs-lookup"><span data-stu-id="bef05-113">The features and technologies used in legacy systems can vary widely from more modern systems.</span></span> <span data-ttu-id="bef05-114">為了與舊版系統交互操作，新應用程式可能需要支援過時的基礎結構、通訊協定、資料模型、API 或是您不會放入現代應用程式的其他功能。</span><span class="sxs-lookup"><span data-stu-id="bef05-114">To interoperate with the legacy system, the new application may need to support outdated infrastructure, protocols, data models, APIs, or other features that you wouldn't otherwise put into a modern application.</span></span>

<span data-ttu-id="bef05-115">維護新版與舊版系統之間的存取權可以強制新系統遵守至少一些舊版系統的 API 或其他語意。</span><span class="sxs-lookup"><span data-stu-id="bef05-115">Maintaining access between new and legacy systems can force the new system to adhere to at least some of the legacy system's APIs or other semantics.</span></span> <span data-ttu-id="bef05-116">當這些舊版功能有品質問題時，支援它們發生「損毀」，否則可能需要全新設計的現代化應用程式。</span><span class="sxs-lookup"><span data-stu-id="bef05-116">When these legacy features have quality issues, supporting them "corrupts" what might otherwise be a cleanly designed modern application.</span></span> 

## <a name="solution"></a><span data-ttu-id="bef05-117">方案</span><span class="sxs-lookup"><span data-stu-id="bef05-117">Solution</span></span>

<span data-ttu-id="bef05-118">藉由在它們之間放置防損毀層來隔離舊版和現代系統。</span><span class="sxs-lookup"><span data-stu-id="bef05-118">Isolate the legacy and modern systems by placing an anti-corruption layer between them.</span></span> <span data-ttu-id="bef05-119">此層會轉譯兩個系統之間的通訊，讓舊版系統維持不變，同時讓現代應用程式可以避免損害其設計和技術方法。</span><span class="sxs-lookup"><span data-stu-id="bef05-119">This layer translates communications between the two systems, allowing the legacy system to remain unchanged while the modern application can avoid compromising its design and technological approach.</span></span>

![](./_images/anti-corruption-layer.png) 

<span data-ttu-id="bef05-120">現代應用程式和防損毀層之間的通訊一律會使用應用程式的資料模型和架構。</span><span class="sxs-lookup"><span data-stu-id="bef05-120">Communication between the modern application and the anti-corruption layer always uses the application's data model and architecture.</span></span> <span data-ttu-id="bef05-121">從防損毀層對舊版系統的呼叫，符合該系統的資料模型或方法。</span><span class="sxs-lookup"><span data-stu-id="bef05-121">Calls from the anti-corruption layer to the legacy system conform to that system's data model or methods.</span></span> <span data-ttu-id="bef05-122">防損毀層包含在兩個系統之間進行轉譯所需的所有邏輯。</span><span class="sxs-lookup"><span data-stu-id="bef05-122">The anti-corruption layer contains all of the logic necessary to translate between the two systems.</span></span> <span data-ttu-id="bef05-123">可以將層實作為應用程式內的元件或獨立的服務。</span><span class="sxs-lookup"><span data-stu-id="bef05-123">The layer can be implemented as a component within the application or as an independent service.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="bef05-124">問題和考量</span><span class="sxs-lookup"><span data-stu-id="bef05-124">Issues and considerations</span></span>

- <span data-ttu-id="bef05-125">防損毀層可以對兩個系統之間的呼叫新增延遲。</span><span class="sxs-lookup"><span data-stu-id="bef05-125">The anti-corruption layer may add latency to calls made between the two systems.</span></span>
- <span data-ttu-id="bef05-126">防損毀層會新增必須管理和維護的一項額外服務。</span><span class="sxs-lookup"><span data-stu-id="bef05-126">The anti-corruption layer adds an additional service that must be managed and maintained.</span></span>
- <span data-ttu-id="bef05-127">考慮您的防損毀層如何將如何調整。</span><span class="sxs-lookup"><span data-stu-id="bef05-127">Consider how your anti-corruption layer will scale.</span></span>
- <span data-ttu-id="bef05-128">考慮您是否需要多個防損毀層。</span><span class="sxs-lookup"><span data-stu-id="bef05-128">Consider whether you need more than one anti-corruption layer.</span></span> <span data-ttu-id="bef05-129">您可能想要將功能分解成使用不同的技術或語言的多個服務，或分割防損毀層可能有其他原因。</span><span class="sxs-lookup"><span data-stu-id="bef05-129">You may want to decompose functionality into multiple services using different technologies or languages, or there may be other reasons to partition the anti-corruption layer.</span></span>
- <span data-ttu-id="bef05-130">考慮關於您的其他應用程式或服務，防損毀層的管理方式。</span><span class="sxs-lookup"><span data-stu-id="bef05-130">Consider how the anti-corruption layer will be managed in relation with your other applications or services.</span></span> <span data-ttu-id="bef05-131">如何將它整合到您的監視、發行和設定程序？</span><span class="sxs-lookup"><span data-stu-id="bef05-131">How will it be integrated into your monitoring, release, and configuration processes?</span></span>
- <span data-ttu-id="bef05-132">確定會保有交易和資料的一致性，並且可加以監視。</span><span class="sxs-lookup"><span data-stu-id="bef05-132">Make sure transaction and data consistency are maintained and can be monitored.</span></span>
- <span data-ttu-id="bef05-133">考慮防損毀層是否需要處理舊版和現代系統之間的通訊，或只需處理功能子集。</span><span class="sxs-lookup"><span data-stu-id="bef05-133">Consider whether the anti-corruption layer needs to handle all communication between legacy and modern systems, or just a subset of features.</span></span> 
- <span data-ttu-id="bef05-134">考慮防損毀層會是永久，或是在移轉所有舊版之後最終會淘汰。</span><span class="sxs-lookup"><span data-stu-id="bef05-134">Consider whether the anti-corruption layer is meant to be permanent, or eventually retired once all legacy functionality has been migrated.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="bef05-135">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="bef05-135">When to use this pattern</span></span>

<span data-ttu-id="bef05-136">使用此模式的時機包括：</span><span class="sxs-lookup"><span data-stu-id="bef05-136">Use this pattern when:</span></span>

- <span data-ttu-id="bef05-137">移轉規劃分多個階段進行，但需要保有新版與舊版系統之間的整合。</span><span class="sxs-lookup"><span data-stu-id="bef05-137">A migration is planned to happen over multiple stages, but integration between new and legacy systems needs to be maintained.</span></span>
- <span data-ttu-id="bef05-138">新版與舊版系統有不同的語意，但仍然需要進行通訊。</span><span class="sxs-lookup"><span data-stu-id="bef05-138">New and legacy system have different semantics, but still need to communicate.</span></span>

<span data-ttu-id="bef05-139">如果有新版與舊版系統之間沒有顯著的語意差異，此模式可能不適用。</span><span class="sxs-lookup"><span data-stu-id="bef05-139">This pattern may not be suitable if there are no significant semantic differences between new and legacy systems.</span></span> 

## <a name="related-guidance"></a><span data-ttu-id="bef05-140">相關的指引</span><span class="sxs-lookup"><span data-stu-id="bef05-140">Related guidance</span></span>

- <span data-ttu-id="bef05-141">[Strangler 模式][strangler]</span><span class="sxs-lookup"><span data-stu-id="bef05-141">[Strangler pattern][strangler]</span></span>

[strangler]: ./strangler.md

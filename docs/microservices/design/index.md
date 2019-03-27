---
title: Azure Kubernetes Service 的微服務參考實作
description: 此參考實作會說明微服務架構的最佳做法
author: MikeWasson
ms.date: 02/26/2019
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: microservices
---

# <a name="designing-a-microservices-architecture"></a><span data-ttu-id="b3623-103">設計微服務架構</span><span class="sxs-lookup"><span data-stu-id="b3623-103">Designing a microservices architecture</span></span>

<span data-ttu-id="b3623-104">微服務已成為熱門的架構樣式，用於建置可復原、高延展性、可獨立部署，以及能夠快速發展的雲端應用程式。</span><span class="sxs-lookup"><span data-stu-id="b3623-104">Microservices have become a popular architectural style for building cloud applications that are resilient, highly scalable, independently deployable, and able to evolve quickly.</span></span> <span data-ttu-id="b3623-105">不過，微服務需要不同的方法來設計和建置應用程式，而不僅僅是現今流行的用語。</span><span class="sxs-lookup"><span data-stu-id="b3623-105">To be more than just a buzzword, however, microservices require a different approach to designing and building applications.</span></span>

<span data-ttu-id="b3623-106">在這一系列文章中，我們會探討如何在 Azure 上建置和執行微服務架構。</span><span class="sxs-lookup"><span data-stu-id="b3623-106">In this set of articles, we explore how to build and run a microservices architecture on Azure.</span></span> <span data-ttu-id="b3623-107">主題包括：</span><span class="sxs-lookup"><span data-stu-id="b3623-107">Topics include:</span></span>

- [<span data-ttu-id="b3623-108">服務間通訊</span><span class="sxs-lookup"><span data-stu-id="b3623-108">Interservice communication</span></span>](./interservice-communication.md)
- [<span data-ttu-id="b3623-109">API 設計</span><span class="sxs-lookup"><span data-stu-id="b3623-109">API design</span></span>](./api-design.md)
- [<span data-ttu-id="b3623-110">API 閘道</span><span class="sxs-lookup"><span data-stu-id="b3623-110">API gateways</span></span>](./gateway.md)
- [<span data-ttu-id="b3623-111">資料考量</span><span class="sxs-lookup"><span data-stu-id="b3623-111">Data considerations</span></span>](./data-considerations.md)
- [<span data-ttu-id="b3623-112">設計模式</span><span class="sxs-lookup"><span data-stu-id="b3623-112">Design patterns</span></span>](./patterns.md)

## <a name="prerequisites"></a><span data-ttu-id="b3623-113">必要條件</span><span class="sxs-lookup"><span data-stu-id="b3623-113">Prerequisites</span></span>

<span data-ttu-id="b3623-114">閱讀這些文章之前，您可以從下列文章著手：</span><span class="sxs-lookup"><span data-stu-id="b3623-114">Before reading these articles, you might start with the following:</span></span>

- <span data-ttu-id="b3623-115">[微服務架構簡介](../introduction.md)。</span><span class="sxs-lookup"><span data-stu-id="b3623-115">[Introduction to microservices architectures](../introduction.md).</span></span> <span data-ttu-id="b3623-116">了解微服務的優勢和挑戰，以及何時使用這種架構樣式。</span><span class="sxs-lookup"><span data-stu-id="b3623-116">Understand the benefits and challenges of microservices, and when to use this style of architecture.</span></span>
- <span data-ttu-id="b3623-117">[使用領域分析進行微服務建模](../model/domain-analysis.md)。</span><span class="sxs-lookup"><span data-stu-id="b3623-117">[Using domain analysis to model microservices](../model/domain-analysis.md).</span></span> <span data-ttu-id="b3623-118">了解領域導向方法來進行微服務建模。</span><span class="sxs-lookup"><span data-stu-id="b3623-118">Learn a domain-driven approach to modeling microservices.</span></span>

## <a name="reference-implementation"></a><span data-ttu-id="b3623-119">參考實作</span><span class="sxs-lookup"><span data-stu-id="b3623-119">Reference implementation</span></span>

<span data-ttu-id="b3623-120">為了說明微服務架構的最佳做法，我們建立了名為「無人機遞送應用程式」的參考實作。</span><span class="sxs-lookup"><span data-stu-id="b3623-120">To illustrate best practices for a microservices architecture, we created a reference implementation that we call the Drone Delivery application.</span></span> <span data-ttu-id="b3623-121">此參考實作會使用 Azure Kubernetes Service (AKS) 在 Kubernetes 上執行。</span><span class="sxs-lookup"><span data-stu-id="b3623-121">This implementation runs on Kubernetes using Azure Kubernetes Service (AKS).</span></span> <span data-ttu-id="b3623-122">您可以在 [GitHub][drone-ri] 上找到此參考實作。</span><span class="sxs-lookup"><span data-stu-id="b3623-122">You can find the reference implementation on [GitHub][drone-ri].</span></span>

![無人機遞送應用程式的架構](../images/drone-delivery.png)

## <a name="scenario"></a><span data-ttu-id="b3623-124">案例</span><span class="sxs-lookup"><span data-stu-id="b3623-124">Scenario</span></span>

<span data-ttu-id="b3623-125">Fabrikam, Inc. 正在推動無人機遞送服務。</span><span class="sxs-lookup"><span data-stu-id="b3623-125">Fabrikam, Inc. is starting a drone delivery service.</span></span> <span data-ttu-id="b3623-126">該公司經營一個無人機隊。</span><span class="sxs-lookup"><span data-stu-id="b3623-126">The company manages a fleet of drone aircraft.</span></span> <span data-ttu-id="b3623-127">企業會註冊此服務，而使用者可要求無人機收取貨物進行遞送。</span><span class="sxs-lookup"><span data-stu-id="b3623-127">Businesses register with the service, and users can request a drone to pick up goods for delivery.</span></span> <span data-ttu-id="b3623-128">當客戶排程取件後，後端系統就會指派一台無人機並將預估的遞送時間通知使用者。</span><span class="sxs-lookup"><span data-stu-id="b3623-128">When a customer schedules a pickup, a backend system assigns a drone and notifies the user with an estimated delivery time.</span></span> <span data-ttu-id="b3623-129">在遞送過程中，客戶可以使用持續更新的 ETA 來追蹤無人機的位置。</span><span class="sxs-lookup"><span data-stu-id="b3623-129">While the delivery is in progress, the customer can track the location of the drone, with a continuously updated ETA.</span></span>

<span data-ttu-id="b3623-130">此案例牽涉到相當複雜的領域。</span><span class="sxs-lookup"><span data-stu-id="b3623-130">This scenario involves a fairly complicated domain.</span></span> <span data-ttu-id="b3623-131">其中一些企業的疑慮包括無人機排程、追蹤包裹、管理使用者帳戶，以及儲存和分析歷史資料。</span><span class="sxs-lookup"><span data-stu-id="b3623-131">Some of the business concerns include scheduling drones, tracking packages, managing user accounts, and storing and analyzing historical data.</span></span> <span data-ttu-id="b3623-132">此外，Fabrikam 希望能快速上市，而後快速反覆運作，並新增各項功能。</span><span class="sxs-lookup"><span data-stu-id="b3623-132">Moreover, Fabrikam wants to get to market quickly and then iterate quickly, adding new functionality and capabilities.</span></span> <span data-ttu-id="b3623-133">此應用程式必須以最高服務等級目標 (SLO) 在雲端規模運作。</span><span class="sxs-lookup"><span data-stu-id="b3623-133">The application needs to operate at cloud scale, with a high service level objective (SLO).</span></span> <span data-ttu-id="b3623-134">Fabrikam 也預期不同的系統部分會有非常不同的資料儲存和查詢需求。</span><span class="sxs-lookup"><span data-stu-id="b3623-134">Fabrikam also expects that different parts of the system will have very different requirements for data storage and querying.</span></span> <span data-ttu-id="b3623-135">上述所有考量導致 Fabrikam 針對無人機遞送應用程式選擇微服務架構。</span><span class="sxs-lookup"><span data-stu-id="b3623-135">All of these considerations lead Fabrikam to choose a microservices architecture for the Drone Delivery application.</span></span>

> [!NOTE]
> <span data-ttu-id="b3623-136">如需選擇微服務架構或其他架構樣式的說明，請參閱 [Azure 應用程式架構指南](../../guide/index.md)。</span><span class="sxs-lookup"><span data-stu-id="b3623-136">For help in choosing between a microservices architecture and other architectural styles, see the [Azure Application Architecture Guide](../../guide/index.md).</span></span>

<span data-ttu-id="b3623-137">我們的參考實作會使用 Kubernetes 搭配 [Azure Kubernetes Service](/azure/aks/) (AKS)。</span><span class="sxs-lookup"><span data-stu-id="b3623-137">Our reference implementation uses Kubernetes with [Azure Kubernetes Service](/azure/aks/) (AKS).</span></span> <span data-ttu-id="b3623-138">不過，任何容器協調工具 (包括 [Azure Service Fabric](/azure/service-fabric/)) 都會面臨許多高層級的架構決策和挑戰。</span><span class="sxs-lookup"><span data-stu-id="b3623-138">However, many of the high-level architectural decisions and challenges will apply to any container orchestrator, including [Azure Service Fabric](/azure/service-fabric/).</span></span>

<!-- links -->

[drone-ri]: https://github.com/mspnp/microservices-reference-implementation

## <a name="next-steps"></a><span data-ttu-id="b3623-139">後續步驟</span><span class="sxs-lookup"><span data-stu-id="b3623-139">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="b3623-140">選擇計算選項</span><span class="sxs-lookup"><span data-stu-id="b3623-140">Choose a compute option</span></span>](./compute-options.md)
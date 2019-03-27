---
title: 管理與監視模式
titleSuffix: Cloud Design Patterns
description: 雲端應用程式會在遠端資料中心內執行，其中您沒有基礎結構或作業系統 (部份情況下) 的完整控制權。 比起內部部署，這可能會讓管理和監視作業變得更困難。 應用程式必須公開執行階段資訊，讓系統管理員及操作員可以使用該資訊來管理及監視系統，以及支援變更商業需求和自訂，而不需要停止或重新部署應用程式。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 587caf680a884cda208baec50ff914f6c7238b48
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58242999"
---
# <a name="management-and-monitoring-patterns"></a><span data-ttu-id="fa5e2-106">管理與監視模式</span><span class="sxs-lookup"><span data-stu-id="fa5e2-106">Management and Monitoring patterns</span></span>

<span data-ttu-id="fa5e2-107">雲端應用程式會在遠端資料中心內執行，其中您沒有基礎結構或作業系統 (部份情況下) 的完整控制權。</span><span class="sxs-lookup"><span data-stu-id="fa5e2-107">Cloud applications run in in a remote datacenter where you do not have full control of the infrastructure or, in some cases, the operating system.</span></span> <span data-ttu-id="fa5e2-108">比起內部部署，這可能會讓管理和監視作業變得更困難。</span><span class="sxs-lookup"><span data-stu-id="fa5e2-108">This can make management and monitoring more difficult than an on-premises deployment.</span></span> <span data-ttu-id="fa5e2-109">應用程式必須公開執行階段資訊，讓系統管理員及操作員可以使用該資訊來管理及監視系統，以及支援變更商業需求和自訂，而不需要停止或重新部署應用程式。</span><span class="sxs-lookup"><span data-stu-id="fa5e2-109">Applications must expose runtime information that administrators and operators can use to manage and monitor the system, as well as supporting changing business requirements and customization without requiring the application to be stopped or redeployed.</span></span>

|                              <span data-ttu-id="fa5e2-110">模式</span><span class="sxs-lookup"><span data-stu-id="fa5e2-110">Pattern</span></span>                               |                                                              <span data-ttu-id="fa5e2-111">總結</span><span class="sxs-lookup"><span data-stu-id="fa5e2-111">Summary</span></span>                                                              |
|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------|
|                   [<span data-ttu-id="fa5e2-112">外交官 (Ambassador)</span><span class="sxs-lookup"><span data-stu-id="fa5e2-112">Ambassador</span></span>](../ambassador.md)                   |                 <span data-ttu-id="fa5e2-113">建立會代表取用者服務或應用程式傳送網路要求的協助程式服務。</span><span class="sxs-lookup"><span data-stu-id="fa5e2-113">Create helper services that send network requests on behalf of a consumer service or application.</span></span>                 |
|        [<span data-ttu-id="fa5e2-114">防損毀層</span><span class="sxs-lookup"><span data-stu-id="fa5e2-114">Anti-Corruption Layer</span></span>](../anti-corruption-layer.md)        |                       <span data-ttu-id="fa5e2-115">在最新應用程式和舊系統間實作外觀或配接層。</span><span class="sxs-lookup"><span data-stu-id="fa5e2-115">Implement a façade or adapter layer between a modern application and a legacy system.</span></span>                       |
| [<span data-ttu-id="fa5e2-116">外部設定存放區</span><span class="sxs-lookup"><span data-stu-id="fa5e2-116">External Configuration Store</span></span>](../external-configuration-store.md) |                <span data-ttu-id="fa5e2-117">將設定資訊從應用程式部署套件移至集中位置。</span><span class="sxs-lookup"><span data-stu-id="fa5e2-117">Move configuration information out of the application deployment package to a centralized location.</span></span>                |
|          [<span data-ttu-id="fa5e2-118">閘道彙總</span><span class="sxs-lookup"><span data-stu-id="fa5e2-118">Gateway Aggregation</span></span>](../gateway-aggregation.md)          |                          <span data-ttu-id="fa5e2-119">您可以使用閘道來將多個個別的要求彙總成單一要求。</span><span class="sxs-lookup"><span data-stu-id="fa5e2-119">Use a gateway to aggregate multiple individual requests into a single request.</span></span>                           |
|           [<span data-ttu-id="fa5e2-120">閘道卸載</span><span class="sxs-lookup"><span data-stu-id="fa5e2-120">Gateway Offloading</span></span>](../gateway-offloading.md)           |                              <span data-ttu-id="fa5e2-121">將共用或特殊服務功能卸載至閘道 Proxy。</span><span class="sxs-lookup"><span data-stu-id="fa5e2-121">Offload shared or specialized service functionality to a gateway proxy.</span></span>                              |
|              [<span data-ttu-id="fa5e2-122">閘道路由</span><span class="sxs-lookup"><span data-stu-id="fa5e2-122">Gateway Routing</span></span>](../gateway-routing.md)              |                                   <span data-ttu-id="fa5e2-123">使用單一端點將要求路由至多個服務。</span><span class="sxs-lookup"><span data-stu-id="fa5e2-123">Route requests to multiple services using a single endpoint.</span></span>                                    |
|   [<span data-ttu-id="fa5e2-124">健康情況端點監視</span><span class="sxs-lookup"><span data-stu-id="fa5e2-124">Health Endpoint Monitoring</span></span>](../health-endpoint-monitoring.md)   |   <span data-ttu-id="fa5e2-125">實作應用程式中的功能檢查，而外部工具可透過公開的端點定期存取此應用程式。</span><span class="sxs-lookup"><span data-stu-id="fa5e2-125">Implement functional checks in an application that external tools can access through exposed endpoints at regular intervals.</span></span>    |
|                      [<span data-ttu-id="fa5e2-126">側車</span><span class="sxs-lookup"><span data-stu-id="fa5e2-126">Sidecar</span></span>](../sidecar.md)                      |         <span data-ttu-id="fa5e2-127">將應用程式的元件部署到個別的處理序或容器，以提供隔離和封裝。</span><span class="sxs-lookup"><span data-stu-id="fa5e2-127">Deploy components of an application into a separate process or container to provide isolation and encapsulation.</span></span>          |
|                    [<span data-ttu-id="fa5e2-128">Strangler</span><span class="sxs-lookup"><span data-stu-id="fa5e2-128">Strangler</span></span>](../strangler.md)                    | <span data-ttu-id="fa5e2-129">透過將功能的特定片段逐漸取代成新的應用程式和服務，來逐步移轉舊有系統。</span><span class="sxs-lookup"><span data-stu-id="fa5e2-129">Incrementally migrate a legacy system by gradually replacing specific pieces of functionality with new applications and services.</span></span> |

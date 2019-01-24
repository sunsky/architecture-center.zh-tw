---
title: 作業的設計
titleSuffix: Azure Application Architecture Guide
description: 設計應用程式，讓作業小組擁有所需的工具。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 75eaa7f8e322c66a83d2b43d180780a2fdcd745b
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54480479"
---
# <a name="design-for-operations"></a><span data-ttu-id="4c9d3-103">作業的設計</span><span class="sxs-lookup"><span data-stu-id="4c9d3-103">Design for operations</span></span>

## <a name="design-an-application-so-that-the-operations-team-has-the-tools-they-need"></a><span data-ttu-id="4c9d3-104">設計應用程式，讓作業小組擁有所需的工具</span><span class="sxs-lookup"><span data-stu-id="4c9d3-104">Design an application so that the operations team has the tools they need</span></span>

<span data-ttu-id="4c9d3-105">雲端已大幅變更作業小組的角色。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-105">The cloud has dramatically changed the role of the operations team.</span></span> <span data-ttu-id="4c9d3-106">他們不再負責管理主控此應用程式的硬體及基礎結構。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-106">They are no longer responsible for managing the hardware and infrastructure that hosts the application.</span></span>  <span data-ttu-id="4c9d3-107">話雖如此，作業仍在執行成功的雲端應用程式中佔很重要的一部分。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-107">That said, operations is still a critical part of running a successful cloud application.</span></span> <span data-ttu-id="4c9d3-108">作業小組的一些重要功能包括：</span><span class="sxs-lookup"><span data-stu-id="4c9d3-108">Some of the important functions of the operations team include:</span></span>

- <span data-ttu-id="4c9d3-109">部署</span><span class="sxs-lookup"><span data-stu-id="4c9d3-109">Deployment</span></span>
- <span data-ttu-id="4c9d3-110">監視</span><span class="sxs-lookup"><span data-stu-id="4c9d3-110">Monitoring</span></span>
- <span data-ttu-id="4c9d3-111">升級</span><span class="sxs-lookup"><span data-stu-id="4c9d3-111">Escalation</span></span>
- <span data-ttu-id="4c9d3-112">事件回應</span><span class="sxs-lookup"><span data-stu-id="4c9d3-112">Incident response</span></span>
- <span data-ttu-id="4c9d3-113">安全性稽核</span><span class="sxs-lookup"><span data-stu-id="4c9d3-113">Security auditing</span></span>

<span data-ttu-id="4c9d3-114">健全的記錄與追蹤在雲端應用程式中特別重要。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-114">Robust logging and tracing are particularly important in cloud applications.</span></span> <span data-ttu-id="4c9d3-115">讓作業小組參與設計與規劃，以確保應用程式可提供讓他們成功所需的資料與見解。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-115">Involve the operations team in design and planning, to ensure the application gives them the data and insight thay need to be successful.</span></span>  <!-- to do: Link to DevOps checklist -->

## <a name="recommendations"></a><span data-ttu-id="4c9d3-116">建議</span><span class="sxs-lookup"><span data-stu-id="4c9d3-116">Recommendations</span></span>

<span data-ttu-id="4c9d3-117">**讓各個項目可觀察**。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-117">**Make all things observable**.</span></span> <span data-ttu-id="4c9d3-118">一旦解決方案部署且執行中，記錄檔和追蹤是您了解系統的主要解析方式。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-118">Once a solution is deployed and running, logs and traces are your primary insight into the system.</span></span> <span data-ttu-id="4c9d3-119">*追蹤*會記錄經過系統的路徑，並可用於找出瓶頸、效能問題和失敗點。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-119">*Tracing* records a path through the system, and is useful to pinpoint bottlenecks, performance issues, and failure points.</span></span> <span data-ttu-id="4c9d3-120">*記錄*會擷取個別事件，例如應用程式狀態變更、錯誤和例外狀況。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-120">*Logging* captures individual events such as application state changes, errors, and exceptions.</span></span> <span data-ttu-id="4c9d3-121">在生產環境記錄，否則當您最需要它時，可能會錯過解析。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-121">Log in production, or else you lose insight at the very times when you need it the most.</span></span>

<span data-ttu-id="4c9d3-122">**檢測監視**。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-122">**Instrument for monitoring**.</span></span> <span data-ttu-id="4c9d3-123">監視提供對於應用程式執行情況的解析 (良好或狀況不佳)，就可用性、效能和系統健康情況而言。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-123">Monitoring gives insight into how well (or poorly) an application is performing, in terms of availability, performance, and system health.</span></span> <span data-ttu-id="4c9d3-124">例如，監視可讓您知道您是否符合 SLA。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-124">For example, monitoring tells you whether you are meeting your SLA.</span></span> <span data-ttu-id="4c9d3-125">監視會在系統正常運作期間進行。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-125">Monitoring happens during the normal operation of the system.</span></span> <span data-ttu-id="4c9d3-126">它應該盡可能接近即時，使得作業人員可以快速反應問題。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-126">It should be as close to real-time as possible, so that the operations staff can react to issues quickly.</span></span> <span data-ttu-id="4c9d3-127">在理想情況下，監視可以協助在問題導致嚴重失敗之前避開問題。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-127">Ideally, monitoring can help avert problems before they lead to a critical failure.</span></span> <span data-ttu-id="4c9d3-128">如需詳細資訊，請參閱[監視和診斷][monitoring]。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-128">For more information, see [Monitoring and diagnostics][monitoring].</span></span>

<span data-ttu-id="4c9d3-129">**檢測根本原因分析**。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-129">**Instrument for root cause analysis**.</span></span> <span data-ttu-id="4c9d3-130">根本原因分析是尋找失敗的根本原因的程序。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-130">Root cause analysis is the process of finding the underlying cause of failures.</span></span> <span data-ttu-id="4c9d3-131">分析發生在失敗已發生之後。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-131">It occurs after a failure has already happened.</span></span>

<span data-ttu-id="4c9d3-132">**使用分散式追蹤**。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-132">**Use distributed tracing**.</span></span> <span data-ttu-id="4c9d3-133">使用針對並行、非同步和雲端規模設計的分散式追蹤系統。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-133">Use a distributed tracing system that is designed for concurrency, asynchrony, and cloud scale.</span></span> <span data-ttu-id="4c9d3-134">追蹤應包含流過服務界限的相互關聯識別碼。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-134">Traces should include a correlation ID that flows across service boundaries.</span></span> <span data-ttu-id="4c9d3-135">單一作業可能牽涉到對多個應用程式服務的呼叫。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-135">A single operation may involve calls to multiple application services.</span></span> <span data-ttu-id="4c9d3-136">如果作業失敗，相互關聯識別碼可協助找出失敗的原因。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-136">If an operation fails, the correlation ID helps to pinpoint the cause of the failure.</span></span>

<span data-ttu-id="4c9d3-137">**將記錄檔和計量標準化**。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-137">**Standardize logs and metrics**.</span></span> <span data-ttu-id="4c9d3-138">作業小組必須從您的解決方案中的各種服務之間彙總記錄檔。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-138">The operations team will need to aggregate logs from across the various services in your solution.</span></span> <span data-ttu-id="4c9d3-139">如果每個服務使用自己的記錄格式，要從中取得有用的資訊會變得困難或不可行。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-139">If every service uses its own logging format, it becomes difficult or impossible to get useful information from them.</span></span> <span data-ttu-id="4c9d3-140">定義通用的結構描述，其中包含欄位如相互關聯識別碼、事件名稱、寄件者的 IP 位址等。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-140">Define a common schema that includes fields such as correlation ID, event name, IP address of the sender, and so forth.</span></span> <span data-ttu-id="4c9d3-141">個別服務可以衍生自訂結構描述，其會繼承基底結構描述，並包含額外的欄位。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-141">Individual services can derive custom schemas that inherit the base schema, and contain additional fields.</span></span>

<span data-ttu-id="4c9d3-142">**自動化管理工作**，包括佈建、部署和監視。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-142">**Automate management tasks**, including provisioning, deployment, and monitoring.</span></span> <span data-ttu-id="4c9d3-143">將工作自動化使得工作可重複，且較不容易出現人為錯誤。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-143">Automating a task makes it repeatable and less prone to human errors.</span></span>

<span data-ttu-id="4c9d3-144">**將設定視為程式碼**。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-144">**Treat configuration as code**.</span></span> <span data-ttu-id="4c9d3-145">將設定檔簽入版本控制系統，以便您可以追蹤和設定變更的版本，並在必要時復原。</span><span class="sxs-lookup"><span data-stu-id="4c9d3-145">Check configuration files into a version control system, so that you can track and version your changes, and roll back if needed.</span></span>

<!-- links -->

[monitoring]: ../../best-practices/monitoring.md

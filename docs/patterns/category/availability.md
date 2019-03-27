---
title: 可用性模式
titleSuffix: Cloud Design Patterns
description: 可用性定義的是系統運作且工作中的時間比例。 它將會受到系統錯誤、基礎結構問題、惡意攻擊和負載系統的影響。 通常以運作時間百分比加以測量。 雲端應用程式一般會提供使用者服務等級協定 (SLA)，這表示必須以可充分提高可用性的方式設計應用程式並且實作。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 897bc3c87beb23770220ef0fc3c5c4394d192f2a
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243039"
---
# <a name="availability-patterns"></a><span data-ttu-id="5a446-107">可用性模式</span><span class="sxs-lookup"><span data-stu-id="5a446-107">Availability patterns</span></span>

[!INCLUDE [header](../../_includes/header.md)]

<span data-ttu-id="5a446-108">可用性定義的是系統運作且工作中的時間比例。</span><span class="sxs-lookup"><span data-stu-id="5a446-108">Availability defines the proportion of time that the system is functional and working.</span></span> <span data-ttu-id="5a446-109">它將會受到系統錯誤、基礎結構問題、惡意攻擊和負載系統的影響。</span><span class="sxs-lookup"><span data-stu-id="5a446-109">It will be affected by system errors, infrastructure problems, malicious attacks, and system load.</span></span> <span data-ttu-id="5a446-110">通常以運作時間百分比加以測量。</span><span class="sxs-lookup"><span data-stu-id="5a446-110">It is usually measured as a percentage of uptime.</span></span> <span data-ttu-id="5a446-111">雲端應用程式一般會提供使用者服務等級協定 (SLA)，這表示必須以可充分提高可用性的方式設計應用程式並且實作。</span><span class="sxs-lookup"><span data-stu-id="5a446-111">Cloud applications typically provide users with a service level agreement (SLA), which means that applications must be designed and implemented in a way that maximizes availability.</span></span>

|                            <span data-ttu-id="5a446-112">模式</span><span class="sxs-lookup"><span data-stu-id="5a446-112">Pattern</span></span>                             |                                                           <span data-ttu-id="5a446-113">總結</span><span class="sxs-lookup"><span data-stu-id="5a446-113">Summary</span></span>                                                            |
|----------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| [<span data-ttu-id="5a446-114">健康情況端點監視</span><span class="sxs-lookup"><span data-stu-id="5a446-114">Health Endpoint Monitoring</span></span>](../health-endpoint-monitoring.md) | <span data-ttu-id="5a446-115">實作應用程式中的功能檢查，而外部工具可透過公開的端點定期存取此應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a446-115">Implement functional checks in an application that external tools can access through exposed endpoints at regular intervals.</span></span> |
|  [<span data-ttu-id="5a446-116">佇列型負載調節</span><span class="sxs-lookup"><span data-stu-id="5a446-116">Queue-Based Load Leveling</span></span>](../queue-based-load-leveling.md)  | <span data-ttu-id="5a446-117">使用佇列來作為工作與其所叫用服務之間的緩衝區，以使間歇性的繁重負載順暢。</span><span class="sxs-lookup"><span data-stu-id="5a446-117">Use a queue that acts as a buffer between a task and a service that it invokes in order to smooth intermittent heavy loads.</span></span>  |
|                 [<span data-ttu-id="5a446-118">節流</span><span class="sxs-lookup"><span data-stu-id="5a446-118">Throttling</span></span>](../throttling.md)                 |   <span data-ttu-id="5a446-119">控制應用程式執行個體、個別租用戶或整個服務所使用的資源耗用量。</span><span class="sxs-lookup"><span data-stu-id="5a446-119">Control the consumption of resources used by an instance of an application, an individual tenant, or an entire service.</span></span>    |

---
title: 訊息模式
description: 雲端應用程式的分散式本質需要可連接元件和服務的傳訊基礎結構，最理想的情況是使用鬆散耦合的方式來達到延展性最大化。 已廣泛使用的非同步傳訊提供許多優點，但也帶來傳訊的順序安排、有害訊息的管理和冪等性 (idempotency) 等挑戰。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
ms.openlocfilehash: 8bf37903df3a6eb23f1581e0405358a7aee61f79
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="messaging-patterns"></a><span data-ttu-id="3ec44-105">訊息模式</span><span class="sxs-lookup"><span data-stu-id="3ec44-105">Messaging patterns</span></span>

[!INCLUDE [header](../../_includes/header.md)]

<span data-ttu-id="3ec44-106">雲端應用程式的分散式本質需要可連接元件和服務的傳訊基礎結構，最理想的情況是使用鬆散耦合的方式來達到延展性最大化。</span><span class="sxs-lookup"><span data-stu-id="3ec44-106">The distributed nature of cloud applications requires a messaging infrastructure that connects the components and services, ideally in a loosely coupled manner in order to maximize scalability.</span></span> <span data-ttu-id="3ec44-107">已廣泛使用的非同步傳訊提供許多優點，但也帶來傳訊的順序安排、有害訊息的管理和冪等性 (idempotency) 等挑戰。</span><span class="sxs-lookup"><span data-stu-id="3ec44-107">Asynchronous messaging is widely used, and provides many benefits, but also brings challenges such as the ordering of messages, poison message management, idempotency, and more.</span></span>


|                            <span data-ttu-id="3ec44-108">模式</span><span class="sxs-lookup"><span data-stu-id="3ec44-108">Pattern</span></span>                             |                                                                        <span data-ttu-id="3ec44-109">總結</span><span class="sxs-lookup"><span data-stu-id="3ec44-109">Summary</span></span>                                                                         |
|----------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
|        [<span data-ttu-id="3ec44-110">競爭取用者</span><span class="sxs-lookup"><span data-stu-id="3ec44-110">Competing Consumers</span></span>](../competing-consumers.md)        |                            <span data-ttu-id="3ec44-111">讓多個並行取用者處理在相同的傳訊通道上接收的訊息。</span><span class="sxs-lookup"><span data-stu-id="3ec44-111">Enable multiple concurrent consumers to process messages received on the same messaging channel.</span></span>                            |
|          [<span data-ttu-id="3ec44-112">管道與篩選器</span><span class="sxs-lookup"><span data-stu-id="3ec44-112">Pipes and Filters</span></span>](../pipes-and-filters.md)          |                       <span data-ttu-id="3ec44-113">將執行複雜處理程序的工作，細分成一系列可重複使用的個別元素。</span><span class="sxs-lookup"><span data-stu-id="3ec44-113">Break down a task that performs complex processing into a series of separate elements that can be reused.</span></span>                        |
|             [<span data-ttu-id="3ec44-114">優先順序佇列</span><span class="sxs-lookup"><span data-stu-id="3ec44-114">Priority Queue</span></span>](../priority-queue.md)             | <span data-ttu-id="3ec44-115">針對傳送給服務的要求排列優先順序，讓高優先順序要求的接收和處理順序在低優先順序要求之前。</span><span class="sxs-lookup"><span data-stu-id="3ec44-115">Prioritize requests sent to services so that requests with a higher priority are received and processed more quickly than those with a lower priority.</span></span> |
|  [<span data-ttu-id="3ec44-116">佇列型負載調節</span><span class="sxs-lookup"><span data-stu-id="3ec44-116">Queue-Based Load Leveling</span></span>](../queue-based-load-leveling.md)  |              <span data-ttu-id="3ec44-117">使用佇列來作為工作與其所叫用服務之間的緩衝區，以使間歇性的繁重負載順暢。</span><span class="sxs-lookup"><span data-stu-id="3ec44-117">Use a queue that acts as a buffer between a task and a service that it invokes in order to smooth intermittent heavy loads.</span></span>               |
| [<span data-ttu-id="3ec44-118">排程器代理程式監督員</span><span class="sxs-lookup"><span data-stu-id="3ec44-118">Scheduler Agent Supervisor</span></span>](../scheduler-agent-supervisor.md) |                              <span data-ttu-id="3ec44-119">在一組分散式服務和其他遠端資源中協調一組動作。</span><span class="sxs-lookup"><span data-stu-id="3ec44-119">Coordinate a set of actions across a distributed set of services and other remote resources.</span></span>                              |


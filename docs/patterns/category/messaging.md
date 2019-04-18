---
title: 訊息模式
titleSuffix: Cloud Design Patterns
description: 雲端應用程式的分散式本質需要可連接元件和服務的傳訊基礎結構，最理想的情況是使用鬆散耦合的方式來達到延展性最大化。 已廣泛使用的非同步傳訊提供許多優點，但也帶來傳訊的順序安排、有害訊息的管理和冪等性 (idempotency) 等挑戰。
keywords: 設計模式
author: dragon119
ms.date: 03/01/2018
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: f838288e10acf9af5776c93972028b6b878bd7b0
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640663"
---
# <a name="messaging-patterns"></a><span data-ttu-id="0860b-105">訊息模式</span><span class="sxs-lookup"><span data-stu-id="0860b-105">Messaging patterns</span></span>

<span data-ttu-id="0860b-106">雲端應用程式的分散式本質需要可連接元件和服務的傳訊基礎結構，最理想的情況是使用鬆散耦合的方式來達到延展性最大化。</span><span class="sxs-lookup"><span data-stu-id="0860b-106">The distributed nature of cloud applications requires a messaging infrastructure that connects the components and services, ideally in a loosely coupled manner in order to maximize scalability.</span></span> <span data-ttu-id="0860b-107">已廣泛使用的非同步傳訊提供許多優點，但也帶來傳訊的順序安排、有害訊息的管理和冪等性 (idempotency) 等挑戰。</span><span class="sxs-lookup"><span data-stu-id="0860b-107">Asynchronous messaging is widely used, and provides many benefits, but also brings challenges such as the ordering of messages, poison message management, idempotency, and more.</span></span>

| <span data-ttu-id="0860b-108">模式</span><span class="sxs-lookup"><span data-stu-id="0860b-108">Pattern</span></span> | <span data-ttu-id="0860b-109">總結</span><span class="sxs-lookup"><span data-stu-id="0860b-109">Summary</span></span> |
| ------- | ------- |
| [<span data-ttu-id="0860b-110">提領票證</span><span class="sxs-lookup"><span data-stu-id="0860b-110">Claim Check</span></span>](../claim-check.md) | <span data-ttu-id="0860b-111">將大型訊息分割成提領票證與承載，以免癱瘓訊息匯流排。</span><span class="sxs-lookup"><span data-stu-id="0860b-111">Split a large message into a claim check and a payload to avoid overwhelming a message bus.</span></span> |
| [<span data-ttu-id="0860b-112">競爭取用者</span><span class="sxs-lookup"><span data-stu-id="0860b-112">Competing Consumers</span></span>](../competing-consumers.md) | <span data-ttu-id="0860b-113">讓多個並行取用者處理在相同的傳訊通道上接收的訊息。</span><span class="sxs-lookup"><span data-stu-id="0860b-113">Enable multiple concurrent consumers to process messages received on the same messaging channel.</span></span> |
| [<span data-ttu-id="0860b-114">管道與篩選器</span><span class="sxs-lookup"><span data-stu-id="0860b-114">Pipes and Filters</span></span>](../pipes-and-filters.md) | <span data-ttu-id="0860b-115">將執行複雜處理程序的工作，細分成一系列可重複使用的個別元素。</span><span class="sxs-lookup"><span data-stu-id="0860b-115">Break down a task that performs complex processing into a series of separate elements that can be reused.</span></span> |
| [<span data-ttu-id="0860b-116">優先順序佇列</span><span class="sxs-lookup"><span data-stu-id="0860b-116">Priority Queue</span></span>](../priority-queue.md) | <span data-ttu-id="0860b-117">針對傳送給服務的要求排列優先順序，讓高優先順序要求的接收和處理順序在低優先順序要求之前。</span><span class="sxs-lookup"><span data-stu-id="0860b-117">Prioritize requests sent to services so that requests with a higher priority are received and processed more quickly than those with a lower priority.</span></span> |
| [<span data-ttu-id="0860b-118">Publisher-Subscriber</span><span class="sxs-lookup"><span data-stu-id="0860b-118">Publisher-Subscriber</span></span>](../publisher-subscriber.md) | <span data-ttu-id="0860b-119">讓應用程式而不需要結合的寄件者到接收者以非同步方式宣告的事件至多個想要的取用者。</span><span class="sxs-lookup"><span data-stu-id="0860b-119">Enable an application to announce events to multiple interested consumers asynchronously, without coupling the senders to the receivers.</span></span> |
| [<span data-ttu-id="0860b-120">佇列型負載調節</span><span class="sxs-lookup"><span data-stu-id="0860b-120">Queue-Based Load Leveling</span></span>](../queue-based-load-leveling.md) | <span data-ttu-id="0860b-121">使用佇列來作為工作與其所叫用服務之間的緩衝區，以使間歇性的繁重負載順暢。</span><span class="sxs-lookup"><span data-stu-id="0860b-121">Use a queue that acts as a buffer between a task and a service that it invokes in order to smooth intermittent heavy loads.</span></span> |
| [<span data-ttu-id="0860b-122">排程器代理程式監督員</span><span class="sxs-lookup"><span data-stu-id="0860b-122">Scheduler Agent Supervisor</span></span>](../scheduler-agent-supervisor.md) | <span data-ttu-id="0860b-123">在一組分散式服務和其他遠端資源中協調一組動作。</span><span class="sxs-lookup"><span data-stu-id="0860b-123">Coordinate a set of actions across a distributed set of services and other remote resources.</span></span> |

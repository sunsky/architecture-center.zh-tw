---
title: "閘道路由模式"
description: "使用單一端點將要求路由至多個服務。"
author: dragon119
ms.date: 06/23/2017
ms.openlocfilehash: 53239b23cfd98fad1edc38ca37c2274d5a9d7a0f
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="gateway-routing-pattern"></a><span data-ttu-id="95eb8-103">閘道路由模式</span><span class="sxs-lookup"><span data-stu-id="95eb8-103">Gateway Routing pattern</span></span>

<span data-ttu-id="95eb8-104">使用單一端點將要求路由至多個服務。</span><span class="sxs-lookup"><span data-stu-id="95eb8-104">Route requests to multiple services using a single endpoint.</span></span> <span data-ttu-id="95eb8-105">當您想要在單一端點公開多個服務，並根據要求路由至適當的服務，此模式相當有用。</span><span class="sxs-lookup"><span data-stu-id="95eb8-105">This pattern is useful when you wish to expose multiple services on a single endpoint and route to the appropriate service based on the request.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="95eb8-106">內容和問題</span><span class="sxs-lookup"><span data-stu-id="95eb8-106">Context and problem</span></span>

<span data-ttu-id="95eb8-107">當用戶端需要使用多個服務時，為每個服務端點設定個別的端點，以及讓用戶端管理每個端點會是一項挑戰。</span><span class="sxs-lookup"><span data-stu-id="95eb8-107">When a client needs to consume multiple services, setting up a separate endpoint for each service and having the client manage each endpoint can be challenging.</span></span> <span data-ttu-id="95eb8-108">例如，電子商務應用程式可能會提供服務，例如搜尋、評論、購物車、結帳和訂單記錄。</span><span class="sxs-lookup"><span data-stu-id="95eb8-108">For example, an e-commerce application might provide services such as search, reviews, cart, checkout, and order history.</span></span> <span data-ttu-id="95eb8-109">每個服務都有用戶端必須與其互動的不同 API，而用戶端必須了解每個端點，才能連接到服務。</span><span class="sxs-lookup"><span data-stu-id="95eb8-109">Each service has a different API that the client must interact with, and the client must know about each endpoint in order to connect to the services.</span></span> <span data-ttu-id="95eb8-110">如果 API 變更，則必須一併更新用戶端。</span><span class="sxs-lookup"><span data-stu-id="95eb8-110">If an API changes, the client must be updated as well.</span></span> <span data-ttu-id="95eb8-111">如果您將服務重構為兩個或多個個別的服務，則必須變更服務和用戶端中的程式碼。</span><span class="sxs-lookup"><span data-stu-id="95eb8-111">If you refactor a service into two or more separate services, the code must change in both the service and the client.</span></span>

## <a name="solution"></a><span data-ttu-id="95eb8-112">方案</span><span class="sxs-lookup"><span data-stu-id="95eb8-112">Solution</span></span>

<span data-ttu-id="95eb8-113">將閘道放置在一組應用程式、服務或部署的前方。</span><span class="sxs-lookup"><span data-stu-id="95eb8-113">Place a gateway in front of a set of applications, services, or deployments.</span></span> <span data-ttu-id="95eb8-114">使用應用程式第 7 層路由來將要求路由至適當的執行個體。</span><span class="sxs-lookup"><span data-stu-id="95eb8-114">Use application Layer 7 routing to route the request to the appropriate instances.</span></span>

<span data-ttu-id="95eb8-115">利用此模式，用戶端應用程式只需要知道單一端點並與其進行通訊。</span><span class="sxs-lookup"><span data-stu-id="95eb8-115">With this pattern, the client application only needs to know about and communicate with a single endpoint.</span></span> <span data-ttu-id="95eb8-116">如果服務已彙總或分解，則不一定需要更新用戶端。</span><span class="sxs-lookup"><span data-stu-id="95eb8-116">If a service is consolidated or decomposed, the client does not necessarily require updating.</span></span> <span data-ttu-id="95eb8-117">它可以繼續對閘道進行要求，並且只有路由會變更。</span><span class="sxs-lookup"><span data-stu-id="95eb8-117">It can continue making requests to the gateway, and only the routing changes.</span></span>

<span data-ttu-id="95eb8-118">閘道也可讓您從用戶端擷取後端服務，使得在閘道後的後端服務中啟用變更的同時，保持用戶端呼叫簡易。</span><span class="sxs-lookup"><span data-stu-id="95eb8-118">A gateway also lets you abstract backend services from the clients, allowing you to keep client calls simple while enabling changes in the backend services behind the gateway.</span></span> <span data-ttu-id="95eb8-119">您可以將用戶端呼叫路由至需要處理預期的用戶端行為的任何服務，讓您新增、分割和重新組織閘道後的服務，而不需要變更用戶端。</span><span class="sxs-lookup"><span data-stu-id="95eb8-119">Client calls can be routed to whatever service or services need to handle the expected client behavior, allowing you to add, split, and reorganize services behind the gateway without changing the client.</span></span>

![](./_images/gateway-routing.png)
 
<span data-ttu-id="95eb8-120">透過讓您管理將更新發佈給使用者的方式，此模式也有助於部署。</span><span class="sxs-lookup"><span data-stu-id="95eb8-120">This pattern can also help with deployment, by allowing you to manage how updates are rolled out to users.</span></span> <span data-ttu-id="95eb8-121">部署您的服務的新版本時，可以將它與現有版本平行部署。</span><span class="sxs-lookup"><span data-stu-id="95eb8-121">When a new version of your service is deployed, it can be deployed in parallel with the existing version.</span></span> <span data-ttu-id="95eb8-122">路由可讓您控制將呈現給用戶端哪個版本的服務，讓您彈性地使用各種版本策略，無論是更新的累加、平行或完全首度推出。</span><span class="sxs-lookup"><span data-stu-id="95eb8-122">Routing let you control what version of the service is presented to the clients, giving you the flexibility to use various release strategies, whether incremental, parallel, or complete rollouts of updates.</span></span> <span data-ttu-id="95eb8-123">在新服務部署後探索到的任何問題可以藉由在閘道上進行設定變更來快速還原，而不會影響用戶端。</span><span class="sxs-lookup"><span data-stu-id="95eb8-123">Any issues discovered after the new service is deployed can be quickly reverted by making a configuration change at the gateway, without affecting clients.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="95eb8-124">問題和考量</span><span class="sxs-lookup"><span data-stu-id="95eb8-124">Issues and considerations</span></span>

- <span data-ttu-id="95eb8-125">閘道服務可能會導致單一失敗點。</span><span class="sxs-lookup"><span data-stu-id="95eb8-125">The gateway service may introduce a single point of failure.</span></span> <span data-ttu-id="95eb8-126">請確定已適當地設計，以符合您的可用性需求。</span><span class="sxs-lookup"><span data-stu-id="95eb8-126">Ensure it is properly designed to meet your availability requirements.</span></span> <span data-ttu-id="95eb8-127">實作時考慮復原和容錯功能。</span><span class="sxs-lookup"><span data-stu-id="95eb8-127">Consider resiliency and fault tolerance capabilities when implementing.</span></span>
- <span data-ttu-id="95eb8-128">閘道服務可能會造成瓶頸。</span><span class="sxs-lookup"><span data-stu-id="95eb8-128">The gateway service may introduce a bottleneck.</span></span> <span data-ttu-id="95eb8-129">請確定閘道具有足夠效能可處理負載，而且可以輕鬆配合您的成長預期進行調整。</span><span class="sxs-lookup"><span data-stu-id="95eb8-129">Ensure the gateway has adequate performance to handle load and can easily scale in line with your growth expectations.</span></span>
- <span data-ttu-id="95eb8-130">對閘道執行負載測試，以確保不會導致一連串的服務失敗。</span><span class="sxs-lookup"><span data-stu-id="95eb8-130">Perform load testing against the gateway to ensure you don't introduce cascading failures for services.</span></span>
- <span data-ttu-id="95eb8-131">閘道路由是等級 7。</span><span class="sxs-lookup"><span data-stu-id="95eb8-131">Gateway routing is level 7.</span></span> <span data-ttu-id="95eb8-132">它可以基於 IP、連接埠、標頭或 URL。</span><span class="sxs-lookup"><span data-stu-id="95eb8-132">It can be based on IP, port, header, or URL.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="95eb8-133">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="95eb8-133">When to use this pattern</span></span>

<span data-ttu-id="95eb8-134">使用此模式的時機包括：</span><span class="sxs-lookup"><span data-stu-id="95eb8-134">Use this pattern when:</span></span>

- <span data-ttu-id="95eb8-135">用戶端必須使用可在閘道後存取的多個服務。</span><span class="sxs-lookup"><span data-stu-id="95eb8-135">A client needs to consume multiple services that can be accessed behind a gateway.</span></span>
- <span data-ttu-id="95eb8-136">您想要使用單一端點來簡化用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="95eb8-136">You wish to simplify client applications by using a single endpoint.</span></span>
- <span data-ttu-id="95eb8-137">您需要將要求從外部可定址端點路由至內部虛擬端點，例如對叢集虛擬 IP 位址公開 VM 上的連接埠。</span><span class="sxs-lookup"><span data-stu-id="95eb8-137">You need to route requests from externally addressable endpoints to internal virtual endpoints, such as exposing ports on a VM to cluster virtual IP addresses.</span></span>

<span data-ttu-id="95eb8-138">當您有只使用一或兩個服務的簡單應用程式時，此模式可能不適合。</span><span class="sxs-lookup"><span data-stu-id="95eb8-138">This pattern may not be suitable when you have a simple application that uses only one or two services.</span></span>

## <a name="example"></a><span data-ttu-id="95eb8-139">範例</span><span class="sxs-lookup"><span data-stu-id="95eb8-139">Example</span></span>

<span data-ttu-id="95eb8-140">使用 Nginx 做為路由器，以下是伺服器的簡單設定檔範例，該伺服器會將位於不同虛擬目錄上應用程式的要求路由到後端不同電腦。</span><span class="sxs-lookup"><span data-stu-id="95eb8-140">Using Nginx as the router, the following is a simple example configuration file for a server that routes requests for applications residing on different virtual directories to different machines at the back end.</span></span>

```
server {
    listen 80;
    server_name domain.com;

    location /app1 {
        proxy_pass http://10.0.3.10:80;
    }

    location /app2 {
        proxy_pass http://10.0.3.20:80;
    }

    location /app3 {
        proxy_pass http://10.0.3.30:80;
    }
}
```

## <a name="related-guidance"></a><span data-ttu-id="95eb8-141">相關的指引</span><span class="sxs-lookup"><span data-stu-id="95eb8-141">Related guidance</span></span>

- [<span data-ttu-id="95eb8-142">前端模式的範例</span><span class="sxs-lookup"><span data-stu-id="95eb8-142">Backends for Frontends pattern</span></span>](./backends-for-frontends.md)
- [<span data-ttu-id="95eb8-143">閘道彙總模式</span><span class="sxs-lookup"><span data-stu-id="95eb8-143">Gateway Aggregation pattern</span></span>](./gateway-aggregation.md)
- [<span data-ttu-id="95eb8-144">閘道卸載模式</span><span class="sxs-lookup"><span data-stu-id="95eb8-144">Gateway Offloading pattern</span></span>](./gateway-offloading.md)




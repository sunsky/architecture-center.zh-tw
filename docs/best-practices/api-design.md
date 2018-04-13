---
title: API 設計指引
description: 說明如何建立設計完善之 Web API 的指引。
author: dragon119
ms.date: 01/12/2018
pnp.series.title: Best Practices
ms.openlocfilehash: a8c4a81835ebd3ebdba2fd2cec624a9a9d5646f5
ms.sourcegitcommit: ea7108f71dab09175ff69322874d1bcba800a37a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="api-design"></a><span data-ttu-id="4afdf-103">API 設計</span><span class="sxs-lookup"><span data-stu-id="4afdf-103">API design</span></span>

<span data-ttu-id="4afdf-104">大多數現代的 Web 應用程式，都會公開用戶端可用來與應用程式互動的 API。</span><span class="sxs-lookup"><span data-stu-id="4afdf-104">Most modern web applications expose APIs that clients can use to interact with the application.</span></span> <span data-ttu-id="4afdf-105">設計良好的 Web API 應以支援下列特性為目標：</span><span class="sxs-lookup"><span data-stu-id="4afdf-105">A well-designed web API should aim to support:</span></span>

* <span data-ttu-id="4afdf-106">**平台獨立性**。</span><span class="sxs-lookup"><span data-stu-id="4afdf-106">**Platform independence**.</span></span> <span data-ttu-id="4afdf-107">不論 API 是如何在內部實作，任何用戶端都應該能夠呼叫 API。</span><span class="sxs-lookup"><span data-stu-id="4afdf-107">Any client should be able to call the API, regardless of how the API is implemented internally.</span></span> <span data-ttu-id="4afdf-108">這需要使用標準通訊協定，並擁有讓用戶端和 Web 服務可以同意交換資料格式的機制。</span><span class="sxs-lookup"><span data-stu-id="4afdf-108">This requires using standard protocols, and having a mechanism whereby the client and the web service can agree on the format of the data to exchange.</span></span>

* <span data-ttu-id="4afdf-109">**服務演化**。</span><span class="sxs-lookup"><span data-stu-id="4afdf-109">**Service evolution**.</span></span> <span data-ttu-id="4afdf-110">Web API 應該要能獨立於用戶端應用程式之外而演化及新增功能。</span><span class="sxs-lookup"><span data-stu-id="4afdf-110">The web API should be able to evolve and add functionality independently from client applications.</span></span> <span data-ttu-id="4afdf-111">隨著 API 的發展，現有的用戶端應用程式即使不進行修改，也應該能夠繼續運作。</span><span class="sxs-lookup"><span data-stu-id="4afdf-111">As the API evolves, existing client applications should continue to function without modification.</span></span> <span data-ttu-id="4afdf-112">所有功能應該要可供探索，讓用戶端應用程式得以充分運用。</span><span class="sxs-lookup"><span data-stu-id="4afdf-112">All functionality should be discoverable, so that client applications can fully utilize it.</span></span>

<span data-ttu-id="4afdf-113">本指引描述了設計 Web API 時應考量的問題。</span><span class="sxs-lookup"><span data-stu-id="4afdf-113">This guidance describes issues that you should consider when designing a web API.</span></span>

## <a name="introduction-to-rest"></a><span data-ttu-id="4afdf-114">REST 簡介</span><span class="sxs-lookup"><span data-stu-id="4afdf-114">Introduction to REST</span></span>

<span data-ttu-id="4afdf-115">在 2000 年時，Roy Fielding 提出了以具象狀態傳輸 (REST) 的架構來設計 Web 服務。</span><span class="sxs-lookup"><span data-stu-id="4afdf-115">In 2000, Roy Fielding proposed Representational State Transfer (REST) as an architectural approach to designing web services.</span></span> <span data-ttu-id="4afdf-116">REST 是建置以超媒體為基礎之分散式系統的架構樣式。</span><span class="sxs-lookup"><span data-stu-id="4afdf-116">REST is an architectural style for building distributed systems based on hypermedia.</span></span> <span data-ttu-id="4afdf-117">REST 獨立於任何基礎通訊協定之外，因此不一定要與 HTTP 連結。</span><span class="sxs-lookup"><span data-stu-id="4afdf-117">REST is independent of any underlying protocol and is not necessarily tied to HTTP.</span></span> <span data-ttu-id="4afdf-118">不過，最常見的 REST 實作會使用 HTTP 作為應用程式通訊協定，且本指南著重於針對 HTTP 來設計 REST API。</span><span class="sxs-lookup"><span data-stu-id="4afdf-118">However, most common REST implementations use HTTP as the application protocol, and this guide focuses on designing REST APIs for HTTP.</span></span>

<span data-ttu-id="4afdf-119">REST 比起 HTTP 的主要優點是前者使用開放標準，因此不會讓 API 或用戶端應用程式的實作受到任何特定實作的約束。</span><span class="sxs-lookup"><span data-stu-id="4afdf-119">A primary advantage of REST over HTTP is that it uses open standards, and does not bind the implementation of the API or the client applications any specific implementation.</span></span> <span data-ttu-id="4afdf-120">例如，開發人員能以 ASP.NET 來撰寫 REST Web 服務，且用戶端應用程式可使用任何語言或工具組，只要能產生 HTTP 要求和剖析 HTTP 回應即可。</span><span class="sxs-lookup"><span data-stu-id="4afdf-120">For example, a REST web service could be written in ASP.NET, and client applications can use any language or toolset that can generate HTTP requests and parse HTTP responses.</span></span>

<span data-ttu-id="4afdf-121">以下是一些使用 HTTP 設計 REST 式 API 的主要原則：</span><span class="sxs-lookup"><span data-stu-id="4afdf-121">Here are some of the main design principles of RESTful APIs using HTTP:</span></span>

- <span data-ttu-id="4afdf-122">REST API 是依照「資源」來設計的，而資源是指可由用戶端存取、任何類型的物件、資料或服務。</span><span class="sxs-lookup"><span data-stu-id="4afdf-122">REST APIs are designed around *resources*, which are any kind of object, data, or service that can be accessed by the client.</span></span> 

- <span data-ttu-id="4afdf-123">資源具有「識別碼」，這是可唯一識別該資源的 URI。</span><span class="sxs-lookup"><span data-stu-id="4afdf-123">A resource has an *identifier*, which is a URI that uniquely identifies that resource.</span></span> <span data-ttu-id="4afdf-124">例如，特定客戶訂單的 URI 可能會是：</span><span class="sxs-lookup"><span data-stu-id="4afdf-124">For example, the URI for a particular customer order might be:</span></span> 
 
    ```http
    http://adventure-works.com/orders/1
    ```
 
- <span data-ttu-id="4afdf-125">用戶端會透過交換資源的「表示法」與服務進行互動。</span><span class="sxs-lookup"><span data-stu-id="4afdf-125">Clients interact with a service by exchanging *representations* of resources.</span></span> <span data-ttu-id="4afdf-126">許多 Web API 會使用 JSON 作為交換格式。</span><span class="sxs-lookup"><span data-stu-id="4afdf-126">Many web APIs use JSON as the exchange format.</span></span> <span data-ttu-id="4afdf-127">例如，對上面所列 URI 的 GET 要求，可能會傳回此回應本文：</span><span class="sxs-lookup"><span data-stu-id="4afdf-127">For example, a GET request to the URI listed above might return this response body:</span></span>

    ```json
    {"orderId":1,"orderValue":99.90,"productId":1,"quantity":1}
    ```

- <span data-ttu-id="4afdf-128">REST API 會使用統一的介面，有助於讓用戶端與服務實作分離。</span><span class="sxs-lookup"><span data-stu-id="4afdf-128">REST APIs use a uniform interface, which helps to decouple the client and service implementations.</span></span> <span data-ttu-id="4afdf-129">對於建置在 HTTP 上的 REST API，統一的介面包括使用標準 HTTP 指令動詞在資源上執行作業。</span><span class="sxs-lookup"><span data-stu-id="4afdf-129">For REST APIs built on HTTP, the uniform interface includes using standard HTTP verbs to perform operations on resources.</span></span> <span data-ttu-id="4afdf-130">最常見的作業是 GET、POST、PUT、PATCH 和 DELETE。</span><span class="sxs-lookup"><span data-stu-id="4afdf-130">The most common operations are GET, POST, PUT, PATCH, and DELETE.</span></span> 

- <span data-ttu-id="4afdf-131">REST API 會使用無狀態要求模式。</span><span class="sxs-lookup"><span data-stu-id="4afdf-131">REST APIs use a stateless request model.</span></span> <span data-ttu-id="4afdf-132">HTTP 要求應該是獨立的，而且可能會以任何順序發生，因此保留多個要求之間的暫時性狀態資訊不是恰當的做法。</span><span class="sxs-lookup"><span data-stu-id="4afdf-132">HTTP requests should be independent and may occur in any order, so keeping transient state information between requests is not feasible.</span></span> <span data-ttu-id="4afdf-133">唯一可儲存資訊的場所是資源本身，而且每個要求都應該是不可部分完成的作業。</span><span class="sxs-lookup"><span data-stu-id="4afdf-133">The only place where information is stored is in the resources themselves, and each request should be an atomic operation.</span></span> <span data-ttu-id="4afdf-134">這個限制式可讓 Web 服務具有高度可調整性，因為在用戶端與特定伺服器之間不需要保留任何的親和性。</span><span class="sxs-lookup"><span data-stu-id="4afdf-134">This constraint enables web services to be highly scalable, because there is no need to retain any affinity between clients and specific servers.</span></span> <span data-ttu-id="4afdf-135">任何伺服器都可以處理來自任何用戶端的任何要求。</span><span class="sxs-lookup"><span data-stu-id="4afdf-135">Any server can handle any request from any client.</span></span> <span data-ttu-id="4afdf-136">話雖如此，其他因素可能會限制延展性。</span><span class="sxs-lookup"><span data-stu-id="4afdf-136">That said, other factors can limit scalability.</span></span> <span data-ttu-id="4afdf-137">例如，許多 Web 服務都會寫入可能難以相應放大的後端資料存放區。([資料分割](./data-partitioning.md)一文描述了相應放大資料存放區的策略。)</span><span class="sxs-lookup"><span data-stu-id="4afdf-137">For example, many web services write to a backend data store, which may be hard to scale out. (The article [Data Partitioning](./data-partitioning.md) describes strategies to scale out a data store.)</span></span>

- <span data-ttu-id="4afdf-138">REST API 是由表示法中包含的超媒體所推動的。</span><span class="sxs-lookup"><span data-stu-id="4afdf-138">REST APIs are driven by hypermedia links that are contained in the representation.</span></span> <span data-ttu-id="4afdf-139">例如，以下顯示訂單的 JSON 表示法。</span><span class="sxs-lookup"><span data-stu-id="4afdf-139">For example, the following shows a JSON representation of an order.</span></span> <span data-ttu-id="4afdf-140">其中所包含的連結，可取得或更新與訂單相關的客戶。</span><span class="sxs-lookup"><span data-stu-id="4afdf-140">It contains links to get or update the customer associated with the order.</span></span> 
 
    ```json
    {
        "orderID":3,
        "productID":2,
        "quantity":4,
        "orderValue":16.60,
        "links": [
            {"rel":"product","href":"http://adventure-works.com/customers/3", "action":"GET" },
            {"rel":"product","href":"http://adventure-works.com/customers/3", "action":"PUT" } 
        ]
    } 
    ```


<span data-ttu-id="4afdf-141">在 2008 年時，Leonard Richardson 針對 Web API 提出了下列[成熟度模型](https://martinfowler.com/articles/richardsonMaturityModel.html)：</span><span class="sxs-lookup"><span data-stu-id="4afdf-141">In 2008, Leonard Richardson proposed the following [maturity model](https://martinfowler.com/articles/richardsonMaturityModel.html) for web APIs:</span></span>

- <span data-ttu-id="4afdf-142">等級 0：定義一個 URI，而所有的作業對此 URI 都是 POST 要求。</span><span class="sxs-lookup"><span data-stu-id="4afdf-142">Level 0: Define one URI, and all operations are POST requests to this URI.</span></span>
- <span data-ttu-id="4afdf-143">等級 1：針對個別資源建立不同的 URI。</span><span class="sxs-lookup"><span data-stu-id="4afdf-143">Level 1: Create separate URIs for individual resources.</span></span>
- <span data-ttu-id="4afdf-144">等級 2：使用 HTTP 方法來定義資源上的作業。</span><span class="sxs-lookup"><span data-stu-id="4afdf-144">Level 2: Use HTTP methods to define operations on resources.</span></span>
- <span data-ttu-id="4afdf-145">等級 3：使用超媒體 (HATEOAS，如下所述)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-145">Level 3: Use hypermedia (HATEOAS, described below).</span></span>

<span data-ttu-id="4afdf-146">根據 Fielding 的定義，層級 3 代表真正的 REST 式 API。</span><span class="sxs-lookup"><span data-stu-id="4afdf-146">Level 3 corresponds to a truly RESTful API according to Fielding's definition.</span></span> <span data-ttu-id="4afdf-147">在實務上，許多已發行的 Web API 是落在等級 2 附近。</span><span class="sxs-lookup"><span data-stu-id="4afdf-147">In practice, many  published web APIs fall somewhere around level 2.</span></span>  

## <a name="organize-the-api-around-resources"></a><span data-ttu-id="4afdf-148">依照資源來組織 API</span><span class="sxs-lookup"><span data-stu-id="4afdf-148">Organize the API around resources</span></span>

<span data-ttu-id="4afdf-149">將焦點放在 Web API 公開商業實體。</span><span class="sxs-lookup"><span data-stu-id="4afdf-149">Focus on the business entities that the web API exposes.</span></span> <span data-ttu-id="4afdf-150">例如，在電子商務系統中，主要實體可能是客戶和訂單。</span><span class="sxs-lookup"><span data-stu-id="4afdf-150">For example, in an e-commerce system, the primary entities might be customers and orders.</span></span> <span data-ttu-id="4afdf-151">可藉由傳送包含訂單資訊的 HTTP POST 要求來建立訂單。</span><span class="sxs-lookup"><span data-stu-id="4afdf-151">Creating an order can be achieved by sending an HTTP POST request that contains the order information.</span></span> <span data-ttu-id="4afdf-152">HTTP 回應會指出訂購成功與否。</span><span class="sxs-lookup"><span data-stu-id="4afdf-152">The HTTP response indicates whether the order was placed successfully or not.</span></span> <span data-ttu-id="4afdf-153">如果可能的話，資源 URI 應該要依據名詞 (資源) 而不是動詞 (在資源上的作業)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-153">When possible, resource URIs should be based on nouns (the resource) and not verbs (the operations on the resource).</span></span> 

```HTTP
http://adventure-works.com/orders // Good

http://adventure-works.com/create-order // Avoid
```

<span data-ttu-id="4afdf-154">資源不一定要依據單一實體資料項目。</span><span class="sxs-lookup"><span data-stu-id="4afdf-154">A resource does not have to be based on a single physical data item.</span></span> <span data-ttu-id="4afdf-155">例如，訂單資源可能會以關聯式資料庫中數個資料表的形式在內部實作，但以單一實體的形式呈現給用戶端。</span><span class="sxs-lookup"><span data-stu-id="4afdf-155">For example, an order resource might be implemented internally as several tables in a relational database, but presented to the client as a single entity.</span></span> <span data-ttu-id="4afdf-156">請避免建立只是反映資料庫內部結構的 API。</span><span class="sxs-lookup"><span data-stu-id="4afdf-156">Avoid creating APIs that simply mirror the internal structure of a database.</span></span> <span data-ttu-id="4afdf-157">REST 的目的在於將實體，以及應用程式能在這些實體上所執行的作業加以模型化。</span><span class="sxs-lookup"><span data-stu-id="4afdf-157">The purpose of REST is to model entities and the operations that an application can perform on those entities.</span></span> <span data-ttu-id="4afdf-158">用戶端不應向內部實作公開。</span><span class="sxs-lookup"><span data-stu-id="4afdf-158">A client should not be exposed to the internal implementation.</span></span>

<span data-ttu-id="4afdf-159">實體通常會分組成集合 (訂單、客戶)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-159">Entities are often grouped together into collections (orders, customers).</span></span> <span data-ttu-id="4afdf-160">集合與集合內的項目是不同的資源，而應具有自己的 URI。</span><span class="sxs-lookup"><span data-stu-id="4afdf-160">A collection is a separate resource from the item within the collection, and should have its own URI.</span></span> <span data-ttu-id="4afdf-161">例如，下列 URI 可能代表訂單的集合：</span><span class="sxs-lookup"><span data-stu-id="4afdf-161">For example, the following URI might represent the collection of orders:</span></span> 

```HTTP
http://adventure-works.com/orders
```

<span data-ttu-id="4afdf-162">傳送對集合 URI 的 HTTP GET 要求，可擷取集合中的項目清單。</span><span class="sxs-lookup"><span data-stu-id="4afdf-162">Sending an HTTP GET request to the collection URI retrieves a list of items in the collection.</span></span> <span data-ttu-id="4afdf-163">集合中的每個項目也會有自己的唯一 URI。</span><span class="sxs-lookup"><span data-stu-id="4afdf-163">Each item in the collection also has its own unique URI.</span></span> <span data-ttu-id="4afdf-164">對項目 URI 的 HTTP GET 要求，會傳回該項目的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="4afdf-164">An HTTP GET request to the item's URI returns the details of that item.</span></span> 

<span data-ttu-id="4afdf-165">在 URI 中請採用一致的命名慣例。</span><span class="sxs-lookup"><span data-stu-id="4afdf-165">Adopt a consistent naming convention in URIs.</span></span> <span data-ttu-id="4afdf-166">一般而言，在參考集合的 URI 中使用複數名詞比較有效益。</span><span class="sxs-lookup"><span data-stu-id="4afdf-166">In general, it helps to use plural nouns for URIs that reference collections.</span></span> <span data-ttu-id="4afdf-167">將集合的 URI 和項目組織成階層是很好的做法。</span><span class="sxs-lookup"><span data-stu-id="4afdf-167">It's a good practice to organize URIs for collections and items into a hierarchy.</span></span> <span data-ttu-id="4afdf-168">例如，`/customers` 是客戶集合的路徑，而 `/customers/5` 則是其 ID 等於 5 的客戶路徑。</span><span class="sxs-lookup"><span data-stu-id="4afdf-168">For example, `/customers` is the path to the customers collection, and `/customers/5` is the path to the customer with ID equal to 5.</span></span> <span data-ttu-id="4afdf-169">這個方法有助於維持 Web API 的直覺性。</span><span class="sxs-lookup"><span data-stu-id="4afdf-169">This approach helps to keep the web API intuitive.</span></span> <span data-ttu-id="4afdf-170">此外，許多 Web API 架構可根據參數化的 URI 路徑來路由傳送要求，因此您可以定義路徑 `/customers/{id}` 的路由。</span><span class="sxs-lookup"><span data-stu-id="4afdf-170">Also, many web API frameworks can route requests based on parameterized URI paths, so you could define a route for the path `/customers/{id}`.</span></span>

<span data-ttu-id="4afdf-171">也請將不同類型之資源間的關聯性，以及公開這些關聯的方式納入考量。</span><span class="sxs-lookup"><span data-stu-id="4afdf-171">Also consider the relationships between different types of resources and how you might expose these associations.</span></span> <span data-ttu-id="4afdf-172">例如，`/customers/5/orders` 可能代表客戶 5 的所有訂單。</span><span class="sxs-lookup"><span data-stu-id="4afdf-172">For example, the `/customers/5/orders` might represent all of the orders for customer 5.</span></span> <span data-ttu-id="4afdf-173">您也可以嘗試從另一個方向切入；從訂單倒推具有如 `/orders/99/customer` URI 的客戶，來表示其關聯性。</span><span class="sxs-lookup"><span data-stu-id="4afdf-173">You could also go in the other direction, and represent the association from an order back to a customer with a URI such as `/orders/99/customer`.</span></span> <span data-ttu-id="4afdf-174">不過，過度擴充此模型可能會導致難以實作。</span><span class="sxs-lookup"><span data-stu-id="4afdf-174">However, extending this model too far can become cumbersome to implement.</span></span> <span data-ttu-id="4afdf-175">比較好的解決方案，是在 HTTP 回應訊息本文中提供相關資源的可瀏覽連結。</span><span class="sxs-lookup"><span data-stu-id="4afdf-175">A better solution is to provide navigable links to associated resources in the body of the HTTP response message.</span></span> <span data-ttu-id="4afdf-176">稍後在[使用 HATEOAS 方法來啟用相關資源導覽](#using-the-hateoas-approach-to-enable-navigation-to-related-resources)一節將有這項機制的詳細描述。</span><span class="sxs-lookup"><span data-stu-id="4afdf-176">This mechanism is described in more detail in the section [Using the HATEOAS Approach to Enable Navigation To Related Resources later](#using-the-hateoas-approach-to-enable-navigation-to-related-resources).</span></span>

<span data-ttu-id="4afdf-177">在更複雜的系統中，能提供可讓用戶端導覽數個層級關聯性 (例如 `/customers/1/orders/99/products`) 的 URI 會很吸引人。</span><span class="sxs-lookup"><span data-stu-id="4afdf-177">In more complex systems, it can be tempting to provide URIs that enable a client to navigate through several levels of relationships, such as `/customers/1/orders/99/products`.</span></span> <span data-ttu-id="4afdf-178">不過，如果資源之間的關聯性在未來發生變更，這種程度的複雜度不僅難以維持，也缺乏彈性。</span><span class="sxs-lookup"><span data-stu-id="4afdf-178">However, this level of complexity can be difficult to maintain and is inflexible if the relationships between resources change in the future.</span></span> <span data-ttu-id="4afdf-179">所以，請試著讓 URI 保持相對單純。</span><span class="sxs-lookup"><span data-stu-id="4afdf-179">Instead, try to keep URIs relatively simple.</span></span> <span data-ttu-id="4afdf-180">一旦應用程式擁有資源的參考，就應該可以使用這個參考來尋找與該資源相關的項目。</span><span class="sxs-lookup"><span data-stu-id="4afdf-180">Once an application has a reference to a resource, it should be possible to use this reference to find items related to that resource.</span></span> <span data-ttu-id="4afdf-181">您可用 URI `/customers/1/orders` 來取代上述查詢，找出客戶 1 的所有訂單，然後用 `/orders/99/products` 來尋找此順序中的產品。</span><span class="sxs-lookup"><span data-stu-id="4afdf-181">The preceding query can be replaced with the URI `/customers/1/orders` to find all the orders for customer 1, and then `/orders/99/products` to find the products in this order.</span></span>

> [!TIP]
> <span data-ttu-id="4afdf-182">請避免要求比 collection/item/collection 更複雜的資源 URI。</span><span class="sxs-lookup"><span data-stu-id="4afdf-182">Avoid requiring resource URIs more complex than *collection/item/collection*.</span></span>

<span data-ttu-id="4afdf-183">另一個因素是所有的 Web 要求，都會造成網頁伺服器的負載。</span><span class="sxs-lookup"><span data-stu-id="4afdf-183">Another factor is that all web requests impose a load on the web server.</span></span> <span data-ttu-id="4afdf-184">要求愈多，負載也愈大。</span><span class="sxs-lookup"><span data-stu-id="4afdf-184">The more requests, the bigger the load.</span></span> <span data-ttu-id="4afdf-185">因此，請試著避免會公開大量小型資源的「多話」Web API。</span><span class="sxs-lookup"><span data-stu-id="4afdf-185">Therefore, try to avoid "chatty" web APIs that expose a large number of small resources.</span></span> <span data-ttu-id="4afdf-186">這類 API 可能會要求用戶端應用程式傳送多個要求來尋找需要的所有資料。</span><span class="sxs-lookup"><span data-stu-id="4afdf-186">Such an API may require a client application to send multiple requests to find all of the data that it requires.</span></span> <span data-ttu-id="4afdf-187">反之，建議您將資料反正規化並將相關資訊結合在一起，變成發出單一要求即可予以擷取的大型資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-187">Instead, you might want to denormalize the data and combine related information into bigger resources that can be retrieved with a single request.</span></span> <span data-ttu-id="4afdf-188">不過，您需要設法平衡使用此方法在擷取資料時所造成的額外負荷 (這是用戶端不需要的)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-188">However, you need to balance this approach against the overhead of fetching data that the client doesn't need.</span></span> <span data-ttu-id="4afdf-189">擷取大型物件可能會讓要求的延遲時間變長，並引發額外的頻寬成本。</span><span class="sxs-lookup"><span data-stu-id="4afdf-189">Retrieving large objects can increase the latency of a request and incur additional bandwidth costs.</span></span> <span data-ttu-id="4afdf-190">如需有關這些效能反模式的詳細資訊，請參閱[多話的 I/O](../antipatterns/chatty-io/index.md) 和[沒有直接關聯的擷取](../antipatterns/extraneous-fetching/index.md)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-190">For more information about these performance antipatterns, see [Chatty I/O](../antipatterns/chatty-io/index.md) and [Extraneous Fetching](../antipatterns/extraneous-fetching/index.md).</span></span>

<span data-ttu-id="4afdf-191">請避免讓 Web API 與基礎資料來源產生相依性。</span><span class="sxs-lookup"><span data-stu-id="4afdf-191">Avoid introducing dependencies between the web API and the underlying data sources.</span></span> <span data-ttu-id="4afdf-192">例如，若資料儲存在關聯式資料庫中，Web API 就不需要將每個資料表公開為資源集合。</span><span class="sxs-lookup"><span data-stu-id="4afdf-192">For example, if your data is stored in a relational database, the web API doesn't need to expose each table as a collection of resources.</span></span> <span data-ttu-id="4afdf-193">事實上，這可能是不良的設計。</span><span class="sxs-lookup"><span data-stu-id="4afdf-193">In fact, that's probably a poor design.</span></span> <span data-ttu-id="4afdf-194">相反地，請將 Web API 想像成資料庫的抽象概念。</span><span class="sxs-lookup"><span data-stu-id="4afdf-194">Instead, think of the web API as an abstraction of the database.</span></span> <span data-ttu-id="4afdf-195">如有必要，請在資料庫與 Web API 之間引入對應層。</span><span class="sxs-lookup"><span data-stu-id="4afdf-195">If necessary, introduce a mapping layer between the database and the web API.</span></span> <span data-ttu-id="4afdf-196">這樣一來，用戶端應用程式就會與基礎資料庫結構描述的變更隔離。</span><span class="sxs-lookup"><span data-stu-id="4afdf-196">That way, client applications are isolated from changes to the underlying database scheme.</span></span>

<span data-ttu-id="4afdf-197">最後，您不一定能將 Web API 實作的每個作業對應到特定資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-197">Finally, it might not be possible to map every operation implemented by a web API to a specific resource.</span></span> <span data-ttu-id="4afdf-198">您可以透過能叫用某項功能，並以 HTTP 回應訊息形式傳回結果的 HTTP 要求來處理這類「非資源」案例。</span><span class="sxs-lookup"><span data-stu-id="4afdf-198">You can handle such *non-resource* scenarios through HTTP requests that invoke a function and return the results as an HTTP response message.</span></span> <span data-ttu-id="4afdf-199">例如，實作簡單計算機作業 (如加法和減法) 的 Web API 可以提供將這些作業公開為虛擬資源，並利用查詢字串來指定所需參數的 URI。</span><span class="sxs-lookup"><span data-stu-id="4afdf-199">For example, a web API that implements simple calculator operations such as add and subtract could provide URIs that expose these operations as pseudo resources and use the query string to specify the parameters required.</span></span> <span data-ttu-id="4afdf-200">例如對 URI /add?operand1=99&operand2=1 的 GET 要求，會傳回本文中包含值 100 的回應訊息。</span><span class="sxs-lookup"><span data-stu-id="4afdf-200">For example a GET request to the URI */add?operand1=99&operand2=1* would return a response message with the body containing the value 100.</span></span> <span data-ttu-id="4afdf-201">儘管如此，請謹慎使用這些形式的 URI。</span><span class="sxs-lookup"><span data-stu-id="4afdf-201">However, only use these forms of URIs sparingly.</span></span>

## <a name="define-operations-in-terms-of-http-methods"></a><span data-ttu-id="4afdf-202">以 HTTP 方法定義作業</span><span class="sxs-lookup"><span data-stu-id="4afdf-202">Define operations in terms of HTTP methods</span></span>

<span data-ttu-id="4afdf-203">HTTP 通訊協定能定義數個將語意意義指派給要求的方法。</span><span class="sxs-lookup"><span data-stu-id="4afdf-203">The HTTP protocol defines a number of methods that assign semantic meaning to a request.</span></span> <span data-ttu-id="4afdf-204">大多數符合 REST 限制之 Web API 使用的常見 HTTP 方法包括︰</span><span class="sxs-lookup"><span data-stu-id="4afdf-204">The common HTTP methods used by most RESTful web APIs are:</span></span>

* <span data-ttu-id="4afdf-205">**GET**：在指定的 URI 擷取資源的表示法。</span><span class="sxs-lookup"><span data-stu-id="4afdf-205">**GET** retrieves a representation of the resource at the specified URI.</span></span> <span data-ttu-id="4afdf-206">回應訊息的本文包含要求之資源的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="4afdf-206">The body of the response message contains the details of the requested resource.</span></span>
* <span data-ttu-id="4afdf-207">**POST**：在指定的 URI 建立新資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-207">**POST** creates a new resource at the specified URI.</span></span> <span data-ttu-id="4afdf-208">要求訊息的本文提供新資源的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="4afdf-208">The body of the request message provides the details of the new resource.</span></span> <span data-ttu-id="4afdf-209">請注意，POST 也能用來觸發實際上不會建立資源的作業。</span><span class="sxs-lookup"><span data-stu-id="4afdf-209">Note that POST can also be used to trigger operations that don't actually create resources.</span></span>
* <span data-ttu-id="4afdf-210">**PUT**：在指定的 URI 建立或取代資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-210">**PUT** either creates or replaces the resource at the specified URI.</span></span> <span data-ttu-id="4afdf-211">要求訊息的本文會指定要建立或更新的資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-211">The body of the request message specifies the resource to be created or updated.</span></span>
* <span data-ttu-id="4afdf-212">**PATCH**：會執行資源的部分更新。</span><span class="sxs-lookup"><span data-stu-id="4afdf-212">**PATCH** performs a partial update of a resource.</span></span> <span data-ttu-id="4afdf-213">要求本文會指定要套用到資源的變更集。</span><span class="sxs-lookup"><span data-stu-id="4afdf-213">The request body specifies the set of changes to apply to the resource.</span></span>
* <span data-ttu-id="4afdf-214">**DELETE**：會在指定的 URI 移除資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-214">**DELETE** removes the resource at the specified URI.</span></span>

<span data-ttu-id="4afdf-215">特定要求的效果應取決於資源是集合還是個別項目。</span><span class="sxs-lookup"><span data-stu-id="4afdf-215">The effect of a specific request should depend on whether the resource is a collection or an individual item.</span></span> <span data-ttu-id="4afdf-216">下表摘要說明的常見慣例是大部分使用電子商務範例之符合 REST 限制實作所採用的慣例。</span><span class="sxs-lookup"><span data-stu-id="4afdf-216">The following table summarizes the common conventions adopted by most RESTful implementations using the ecommerce example.</span></span> <span data-ttu-id="4afdf-217">請注意，這些要求中並非所有要求都可以實作，須視特定案例的情況而定。</span><span class="sxs-lookup"><span data-stu-id="4afdf-217">Note that not all of these requests might be implemented; it depends on the specific scenario.</span></span>

| <span data-ttu-id="4afdf-218">**Resource**</span><span class="sxs-lookup"><span data-stu-id="4afdf-218">**Resource**</span></span> | <span data-ttu-id="4afdf-219">**POST**</span><span class="sxs-lookup"><span data-stu-id="4afdf-219">**POST**</span></span> | <span data-ttu-id="4afdf-220">**GET**</span><span class="sxs-lookup"><span data-stu-id="4afdf-220">**GET**</span></span> | <span data-ttu-id="4afdf-221">**PUT**</span><span class="sxs-lookup"><span data-stu-id="4afdf-221">**PUT**</span></span> | <span data-ttu-id="4afdf-222">**DELETE**</span><span class="sxs-lookup"><span data-stu-id="4afdf-222">**DELETE**</span></span> |
| --- | --- | --- | --- | --- |
| <span data-ttu-id="4afdf-223">/customers</span><span class="sxs-lookup"><span data-stu-id="4afdf-223">/customers</span></span> |<span data-ttu-id="4afdf-224">建立新客戶</span><span class="sxs-lookup"><span data-stu-id="4afdf-224">Create a new customer</span></span> |<span data-ttu-id="4afdf-225">擷取所有客戶</span><span class="sxs-lookup"><span data-stu-id="4afdf-225">Retrieve all customers</span></span> |<span data-ttu-id="4afdf-226">大量更新客戶</span><span class="sxs-lookup"><span data-stu-id="4afdf-226">Bulk update of customers</span></span> |<span data-ttu-id="4afdf-227">移除所有客戶</span><span class="sxs-lookup"><span data-stu-id="4afdf-227">Remove all customers</span></span> |
| <span data-ttu-id="4afdf-228">/customers/1</span><span class="sxs-lookup"><span data-stu-id="4afdf-228">/customers/1</span></span> |<span data-ttu-id="4afdf-229">Error</span><span class="sxs-lookup"><span data-stu-id="4afdf-229">Error</span></span> |<span data-ttu-id="4afdf-230">擷取客戶 1 的詳細資料</span><span class="sxs-lookup"><span data-stu-id="4afdf-230">Retrieve the details for customer 1</span></span> |<span data-ttu-id="4afdf-231">更新客戶 1 的詳細資料 (若有的話)</span><span class="sxs-lookup"><span data-stu-id="4afdf-231">Update the details of customer 1 if it exists</span></span> |<span data-ttu-id="4afdf-232">移除客戶 1</span><span class="sxs-lookup"><span data-stu-id="4afdf-232">Remove customer 1</span></span> |
| <span data-ttu-id="4afdf-233">/customers/1/orders</span><span class="sxs-lookup"><span data-stu-id="4afdf-233">/customers/1/orders</span></span> |<span data-ttu-id="4afdf-234">為客戶 1 建立新訂單</span><span class="sxs-lookup"><span data-stu-id="4afdf-234">Create a new order for customer 1</span></span> |<span data-ttu-id="4afdf-235">擷取客戶 1 的所有訂單</span><span class="sxs-lookup"><span data-stu-id="4afdf-235">Retrieve all orders for customer 1</span></span> |<span data-ttu-id="4afdf-236">大量更新客戶 1 的訂單</span><span class="sxs-lookup"><span data-stu-id="4afdf-236">Bulk update of orders for customer 1</span></span> |<span data-ttu-id="4afdf-237">移除客戶 1 的所有訂單</span><span class="sxs-lookup"><span data-stu-id="4afdf-237">Remove all orders for customer 1</span></span> |

<span data-ttu-id="4afdf-238">POST、PUT 和 PATCH 之間的差異可能會令人混淆。</span><span class="sxs-lookup"><span data-stu-id="4afdf-238">The differences between POST, PUT, and PATCH can be confusing.</span></span>

- <span data-ttu-id="4afdf-239">POST 要求會建立資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-239">A POST request creates a resource.</span></span> <span data-ttu-id="4afdf-240">伺服器會指派新資源的 URI，並將該 URI 傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="4afdf-240">The server assigns a URI for the new resource, and returns that URI to the client.</span></span> <span data-ttu-id="4afdf-241">在 REST 模型中，您會經常將 POST 要求套用到集合。</span><span class="sxs-lookup"><span data-stu-id="4afdf-241">In the REST model, you frequently apply POST requests to collections.</span></span> <span data-ttu-id="4afdf-242">新資源會新增到集合中。</span><span class="sxs-lookup"><span data-stu-id="4afdf-242">The new resource is added to the collection.</span></span> <span data-ttu-id="4afdf-243">POST 要求也可用來提交資料以處理現有的資源，而不建立任何新資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-243">A POST request can also be used to submit data for processing to an existing resource, without any new resource being created.</span></span>

- <span data-ttu-id="4afdf-244">PUT 要求會建立資源「或」更新現有的資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-244">A PUT request creates a resource *or* updates an existing resource.</span></span> <span data-ttu-id="4afdf-245">用戶端會指定資源的 URI。</span><span class="sxs-lookup"><span data-stu-id="4afdf-245">The client specifies the URI for the resource.</span></span> <span data-ttu-id="4afdf-246">要求本文會包含資源的完整表示法。</span><span class="sxs-lookup"><span data-stu-id="4afdf-246">The request body contains a complete representation of the resource.</span></span> <span data-ttu-id="4afdf-247">若具有此 URI 的資源已經存在，則會取代此資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-247">If a resource with this URI already exists, it is replaced.</span></span> <span data-ttu-id="4afdf-248">否則會建立新的資源 (若伺服器支援此動作)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-248">Otherwise a new resource is created, if the server supports doing so.</span></span> <span data-ttu-id="4afdf-249">PUT 要求最常套用到是個別項目的資源 (例如特定的客戶)，而非集合的資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-249">PUT requests are most frequently applied to resources that are individual items, such as a specific customer, rather than collections.</span></span> <span data-ttu-id="4afdf-250">伺服器可能會支援透過 PUT 更新資源，但不支援透過 PUT 建立資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-250">A server might support updates but not creation via PUT.</span></span> <span data-ttu-id="4afdf-251">是否支援透過 PUT 建立資源，取決於資源存在之前，用戶端是否可以有意義地將 URI 指派給資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-251">Whether to support creation via PUT depends on whether the client can meaningfully assign a URI to a resource before it exists.</span></span> <span data-ttu-id="4afdf-252">若否，請使用 POST 來建立資源並使用 PUT 或 PATCH 來更新資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-252">If not, then use POST to create resources and PUT or PATCH to update.</span></span>

- <span data-ttu-id="4afdf-253">PATCH 要求會針對現有的資源執行「部分更新」。</span><span class="sxs-lookup"><span data-stu-id="4afdf-253">A PATCH request performs a *partial update* to an existing resource.</span></span> <span data-ttu-id="4afdf-254">用戶端會指定資源的 URI。</span><span class="sxs-lookup"><span data-stu-id="4afdf-254">The client specifies the URI for the resource.</span></span> <span data-ttu-id="4afdf-255">要求本文會指定要套用到資源的「變更」集。</span><span class="sxs-lookup"><span data-stu-id="4afdf-255">The request body specifies a set of *changes* to apply to the resource.</span></span> <span data-ttu-id="4afdf-256">這可能比使用 PUT 更有效率，因為用戶端只會傳送變更，而不會傳送整個資源的表示法。</span><span class="sxs-lookup"><span data-stu-id="4afdf-256">This can be more efficient than using PUT, because the client only sends the changes, not the entire representation of the resource.</span></span> <span data-ttu-id="4afdf-257">技術上 PATCH 也可以建立新的資源 (透過指定一組「null」資源的更新，若伺服器支援此動作)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-257">Technically PATCH can also create a new resource (by specifying a set of updates to a "null" resource), if the server supports this.</span></span> 

<span data-ttu-id="4afdf-258">PUT 要求必須具有等冪性。</span><span class="sxs-lookup"><span data-stu-id="4afdf-258">PUT requests must be idempotent.</span></span> <span data-ttu-id="4afdf-259">若用戶端多次送出相同的 PUT 要求，結果應該永遠保持不變 (使用相同的值會修改相同的資源)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-259">If a client submits the same PUT request multiple times, the results should always be the same (the same resource will be modified with the same values).</span></span> <span data-ttu-id="4afdf-260">不保證 POST 和 PATCH 要求都具有冪等性。</span><span class="sxs-lookup"><span data-stu-id="4afdf-260">POST and PATCH requests are not guaranteed to be idempotent.</span></span>

## <a name="conform-to-http-semantics"></a><span data-ttu-id="4afdf-261">符合 HTTP 語意</span><span class="sxs-lookup"><span data-stu-id="4afdf-261">Conform to HTTP semantics</span></span>

<span data-ttu-id="4afdf-262">本節會描述一些要設計符合 HTTP 規格的 API 時，一般的考量事項。</span><span class="sxs-lookup"><span data-stu-id="4afdf-262">This section describes some typical considerations for designing an API that conforms to the HTTP specification.</span></span> <span data-ttu-id="4afdf-263">不過，恕無法涵蓋每種可能的細節或案例。</span><span class="sxs-lookup"><span data-stu-id="4afdf-263">However, it doesn't cover every possible detail or scenario.</span></span> <span data-ttu-id="4afdf-264">若有疑問，請參閱 HTTP 規格。</span><span class="sxs-lookup"><span data-stu-id="4afdf-264">When in doubt, consult the HTTP specifications.</span></span>

### <a name="media-types"></a><span data-ttu-id="4afdf-265">媒體類型</span><span class="sxs-lookup"><span data-stu-id="4afdf-265">Media types</span></span>

<span data-ttu-id="4afdf-266">如先前所述，用戶端和伺服器會交換資源的表示法。</span><span class="sxs-lookup"><span data-stu-id="4afdf-266">As mentioned earlier, clients and servers exchange representations of resources.</span></span> <span data-ttu-id="4afdf-267">例如，在 POST 要求中，要求本文會包含所要建立資源的表示法。</span><span class="sxs-lookup"><span data-stu-id="4afdf-267">For example, in a POST request, the request body contains a representation of the resource to create.</span></span> <span data-ttu-id="4afdf-268">在 GET 要求中，回應本文會包含已擷取資源的表示法。</span><span class="sxs-lookup"><span data-stu-id="4afdf-268">In a GET request, the response body contains a representation of the fetched resource.</span></span>

<span data-ttu-id="4afdf-269">在 HTTP 通訊協定中，會使用「媒體類型」(也稱為 MIME 類型) 來指定格式。</span><span class="sxs-lookup"><span data-stu-id="4afdf-269">In the HTTP protocol, formats are specified through the use of *media types*, also called MIME types.</span></span> <span data-ttu-id="4afdf-270">對於非二進位資料，大部分 Web API 都支援 JSON (媒體類型 = application/json)，並可能支援 XML (媒體類型 = application/xml)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-270">For non-binary data, most web APIs support JSON (media type = application/json) and possibly XML (media type = application/xml).</span></span> 

<span data-ttu-id="4afdf-271">要求或回應中的 Content-Type 標頭會指定表示法的格式。</span><span class="sxs-lookup"><span data-stu-id="4afdf-271">The Content-Type header in a request or response specifies the format of the representation.</span></span> <span data-ttu-id="4afdf-272">以下是包括 JSON 資料的 POST 要求範例：</span><span class="sxs-lookup"><span data-stu-id="4afdf-272">Here is an example of a POST request that includes JSON data:</span></span>

```HTTP
POST http://adventure-works.com/orders HTTP/1.1
Content-Type: application/json; charset=utf-8
Content-Length: 57

{"Id":1,"Name":"Gizmo","Category":"Widgets","Price":1.99}
```

<span data-ttu-id="4afdf-273">若伺服器不支援該媒體類型，應會傳回 HTTP 狀態碼 415 (不支援的媒體類型)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-273">If the server doesn't support the media type, it should return HTTP status code 415 (Unsupported Media Type).</span></span>

<span data-ttu-id="4afdf-274">用戶端要求可能會包含 Accept 標頭，而在回應訊息中，該標頭包含用戶端會從伺服器接受的媒體類型清單。</span><span class="sxs-lookup"><span data-stu-id="4afdf-274">A client request can include an Accept header that contains a list of media types the client will accept from the server in the response message.</span></span> <span data-ttu-id="4afdf-275">例如︰</span><span class="sxs-lookup"><span data-stu-id="4afdf-275">For example:</span></span>

```HTTP
GET http://adventure-works.com/orders/2 HTTP/1.1
Accept: application/json
```

<span data-ttu-id="4afdf-276">若伺服器不符合任何所列的媒體類型，應會傳回 HTTP 狀態碼 406 (無法接受)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-276">If the server cannot match any of the media type(s) listed, it should return HTTP status code 406 (Not Acceptable).</span></span> 

### <a name="get-methods"></a><span data-ttu-id="4afdf-277">GET 方法</span><span class="sxs-lookup"><span data-stu-id="4afdf-277">GET methods</span></span>

<span data-ttu-id="4afdf-278">成功的 GET 方法通常會傳回 HTTP 狀態碼 200 (確定)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-278">A successful GET method typically returns HTTP status code 200 (OK).</span></span> <span data-ttu-id="4afdf-279">若找不到該資源，此方法應會傳回 404 (找不到)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-279">If the resource cannot be found, the method should return 404 (Not Found).</span></span>

### <a name="post-methods"></a><span data-ttu-id="4afdf-280">POST 方法</span><span class="sxs-lookup"><span data-stu-id="4afdf-280">POST methods</span></span>

<span data-ttu-id="4afdf-281">若 POST 方法建立了新資源，就會傳回 HTTP 狀態碼 201 (已建立)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-281">If a POST method creates a new resource, it returns HTTP status code 201 (Created).</span></span> <span data-ttu-id="4afdf-282">新資源的 URI 會包含在回應的 Location 標頭中。</span><span class="sxs-lookup"><span data-stu-id="4afdf-282">The URI of the new resource is included in the Location header of the response.</span></span> <span data-ttu-id="4afdf-283">回應本文會包含資源的表示法。</span><span class="sxs-lookup"><span data-stu-id="4afdf-283">The response body contains a representation of the resource.</span></span>

<span data-ttu-id="4afdf-284">若該方法進行了一些處理，但未建立新資源，則方法可能會傳回 HTTP 狀態碼 200，並在回應本文中包含作業的結果。</span><span class="sxs-lookup"><span data-stu-id="4afdf-284">If the method does some processing but does not create a new resource, the method can return HTTP status code 200 and include the result of the operation in the response body.</span></span> <span data-ttu-id="4afdf-285">或者，若沒有可傳回的結果，該方法可能會傳回 HTTP 狀態碼 204 (沒有內容) 而不傳回回應本文。</span><span class="sxs-lookup"><span data-stu-id="4afdf-285">Alternatively, if there is no result to return, the method can return HTTP status code 204 (No Content) with no response body.</span></span>

<span data-ttu-id="4afdf-286">若用戶端在要求中放入無效的資料，伺服器應會傳回 HTTP 狀態碼 400 (錯誤的要求)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-286">If the client puts invalid data into the request, the server should return HTTP status code 400 (Bad Request).</span></span> <span data-ttu-id="4afdf-287">回應本文可能會包含關於錯誤的其他資訊，或可提供更多詳細資料的 URI 連結。</span><span class="sxs-lookup"><span data-stu-id="4afdf-287">The response body can contain additional information about the error or a link to a URI that provides more details.</span></span>

### <a name="put-methods"></a><span data-ttu-id="4afdf-288">PUT 方法</span><span class="sxs-lookup"><span data-stu-id="4afdf-288">PUT methods</span></span>

<span data-ttu-id="4afdf-289">若 PUT 方法建立了新資源，就會傳回 HTTP 狀態碼 201 (已建立)，如同 POST 方法一樣。</span><span class="sxs-lookup"><span data-stu-id="4afdf-289">If a PUT method creates a new resource, it returns HTTP status code 201 (Created), as with a POST method.</span></span> <span data-ttu-id="4afdf-290">若該方法更新了現有資源，就會傳回 200 (確定) 或 204 (沒有內容)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-290">If the method updates an existing resource, it returns either 200 (OK) or 204 (No Content).</span></span> <span data-ttu-id="4afdf-291">在某些情況下，可能會無法更新現有的資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-291">In some cases, it might not be possible to update an existing resource.</span></span> <span data-ttu-id="4afdf-292">在此情況下，請考慮傳回 HTTP 狀態碼 409 (衝突)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-292">In that case, consider returning HTTP status code 409 (Conflict).</span></span> 

<span data-ttu-id="4afdf-293">請考慮實作可批次更新集合中多個資源的大量 HTTP PUT 作業。</span><span class="sxs-lookup"><span data-stu-id="4afdf-293">Consider implementing bulk HTTP PUT operations that can batch updates to multiple resources in a collection.</span></span> <span data-ttu-id="4afdf-294">PUT 要求應指定集合的 URI，而要求本文應指定要修改之資源的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="4afdf-294">The PUT request should specify the URI of the collection, and the request body should specify the details of the resources to be modified.</span></span> <span data-ttu-id="4afdf-295">這個方法有助於減少多對話的情況及提升效能。</span><span class="sxs-lookup"><span data-stu-id="4afdf-295">This approach can help to reduce chattiness and improve performance.</span></span>

### <a name="patch-methods"></a><span data-ttu-id="4afdf-296">PATCH 方法</span><span class="sxs-lookup"><span data-stu-id="4afdf-296">PATCH methods</span></span>

<span data-ttu-id="4afdf-297">用戶端會使用 PATCH 要求，以「修補程式文件」的格式將一組更新傳送給現有資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-297">With a PATCH request, the client sends a set of updates to an existing resource, in the form of a *patch document*.</span></span> <span data-ttu-id="4afdf-298">伺服器會處理修補程式文件以執行更新。</span><span class="sxs-lookup"><span data-stu-id="4afdf-298">The server processes the patch document to perform the update.</span></span> <span data-ttu-id="4afdf-299">修補程式文件並不會描述整個資源，只會描述要套用的一組變更。</span><span class="sxs-lookup"><span data-stu-id="4afdf-299">The patch document doesn't describe the whole resource, only a set of changes to apply.</span></span> <span data-ttu-id="4afdf-300">PATCH 方法的規格 ([RFC 5789](https://tools.ietf.org/html/rfc5789)) 不會針對修補程式文件定義特定格式。</span><span class="sxs-lookup"><span data-stu-id="4afdf-300">The specification for the PATCH method ([RFC 5789](https://tools.ietf.org/html/rfc5789)) doesn't define a particular format for patch documents.</span></span> <span data-ttu-id="4afdf-301">格式必須從要求中的媒體類型來推斷。</span><span class="sxs-lookup"><span data-stu-id="4afdf-301">The format must be inferred from the media type in the request.</span></span>

<span data-ttu-id="4afdf-302">JSON 可能是 Web API 最常見的資料格式。</span><span class="sxs-lookup"><span data-stu-id="4afdf-302">JSON is probably the most common data format for web APIs.</span></span> <span data-ttu-id="4afdf-303">有兩種以 JSON 為基礎的主要修補程式格式，稱為「JSON 修補」和「JSON 合併修補」。</span><span class="sxs-lookup"><span data-stu-id="4afdf-303">There are two main JSON-based patch formats, called *JSON patch* and *JSON merge patch*.</span></span>

<span data-ttu-id="4afdf-304">JSON 合併修補稍微簡單些。</span><span class="sxs-lookup"><span data-stu-id="4afdf-304">JSON merge patch is somewhat simpler.</span></span> <span data-ttu-id="4afdf-305">修補程式文件與原始的 JSON 資源具有相同的結構，但只包含了應變更或新增的欄位子集。</span><span class="sxs-lookup"><span data-stu-id="4afdf-305">The patch document has the same structure as the original JSON resource, but includes just the subset of fields that should be changed or added.</span></span> <span data-ttu-id="4afdf-306">此外，您可在修補程式文件中將欄位值指定為 `null` 來刪除欄位。</span><span class="sxs-lookup"><span data-stu-id="4afdf-306">In addition, a field can be deleted by specifying `null` for the field value in the patch document.</span></span> <span data-ttu-id="4afdf-307">(這表示若原始的資源可以有明確的 null 值，合併修補程式就不適合。)</span><span class="sxs-lookup"><span data-stu-id="4afdf-307">(That means merge patch is not suitable if the original resource can have explicit null values.)</span></span>

<span data-ttu-id="4afdf-308">例如，假設原始的資源具有下列 JSON 表示法：</span><span class="sxs-lookup"><span data-stu-id="4afdf-308">For example, suppose the original resource has the following JSON representation:</span></span>

```json
{ 
    "name":"gizmo",
    "category":"widgets",
    "color":"blue",
    "price":10
}
```

<span data-ttu-id="4afdf-309">以下是此資源可能的 JSON 合併修補程式：</span><span class="sxs-lookup"><span data-stu-id="4afdf-309">Here is a possible JSON merge patch for this resource:</span></span>

```json
{ 
    "price":12,
    "color":null,
    "size":"small"
}
```

<span data-ttu-id="4afdf-310">這會要求伺服器更新「價格」、刪除「色彩」，然後新增「大小」。</span><span class="sxs-lookup"><span data-stu-id="4afdf-310">This tells the server to update "price", delete "color", and add "size".</span></span> <span data-ttu-id="4afdf-311">「名稱」和「類別」則不會受到修改。</span><span class="sxs-lookup"><span data-stu-id="4afdf-311">"Name" and "category" are not modified.</span></span> <span data-ttu-id="4afdf-312">如需 JSON 合併修補程式的確切詳情，請參閱 [RFC 7396](https://tools.ietf.org/html/rfc7396)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-312">For the exact details of JSON merge patch, see [RFC 7396](https://tools.ietf.org/html/rfc7396).</span></span> <span data-ttu-id="4afdf-313">JSON 合併修補程式的媒體類型為「application/merge-patch+json」。</span><span class="sxs-lookup"><span data-stu-id="4afdf-313">The media type for JSON merge patch is "application/merge-patch+json".</span></span>

<span data-ttu-id="4afdf-314">若原始的資源可以包含明確的 null 值，合併修補程式就不適合，因為 `null` 在修補程式文件中有特殊意義。</span><span class="sxs-lookup"><span data-stu-id="4afdf-314">Merge patch is not suitable if the original resource can contain explicit null values, due to the special meaning of `null` in the patch document.</span></span> <span data-ttu-id="4afdf-315">此外，修補程式文件也不會指定伺服器應套用更新的順序。</span><span class="sxs-lookup"><span data-stu-id="4afdf-315">Also, the patch document doesn't specify the order that the server should apply the updates.</span></span> <span data-ttu-id="4afdf-316">這可能很重要，也可能不重要，端視資料和網域而定。</span><span class="sxs-lookup"><span data-stu-id="4afdf-316">That may or may not matter, depending on the data and the domain.</span></span> <span data-ttu-id="4afdf-317">以 [RFC 6902](https://tools.ietf.org/html/rfc6902) 定義的 JSON 修補程式更加有彈性。</span><span class="sxs-lookup"><span data-stu-id="4afdf-317">JSON patch, defined in [RFC 6902](https://tools.ietf.org/html/rfc6902), is more flexible.</span></span> <span data-ttu-id="4afdf-318">它會將變更指定為要套用的作業序列。</span><span class="sxs-lookup"><span data-stu-id="4afdf-318">It specifies the changes as a sequence of operations to apply.</span></span> <span data-ttu-id="4afdf-319">這些作業包括新增、移除、取代、複製及測試 (用來驗證值)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-319">Operations include add, remove, replace, copy, and test (to validate values).</span></span> <span data-ttu-id="4afdf-320">JSON 修補程式的媒體類型為「application/json-patch+json」。</span><span class="sxs-lookup"><span data-stu-id="4afdf-320">The media type for JSON patch is "application/json-patch+json".</span></span>

<span data-ttu-id="4afdf-321">以下是一些處理 PATCH 要求時可能會碰到的一般錯誤狀況，以及適用的 HTTP 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="4afdf-321">Here are some typical error conditions that might be encountered when processing a PATCH request, along with the appropriate HTTP status code.</span></span>

| <span data-ttu-id="4afdf-322">錯誤狀況</span><span class="sxs-lookup"><span data-stu-id="4afdf-322">Error condition</span></span> | <span data-ttu-id="4afdf-323">HTTP 狀態碼</span><span class="sxs-lookup"><span data-stu-id="4afdf-323">HTTP status code</span></span> |
|-----------|------------|
| <span data-ttu-id="4afdf-324">不支援該修補程式文件格式。</span><span class="sxs-lookup"><span data-stu-id="4afdf-324">The patch document format isn't supported.</span></span> | <span data-ttu-id="4afdf-325">415 (不支援的媒體類型)</span><span class="sxs-lookup"><span data-stu-id="4afdf-325">415 (Unsupported Media Type)</span></span> |
| <span data-ttu-id="4afdf-326">修補程式文件格式不正確。</span><span class="sxs-lookup"><span data-stu-id="4afdf-326">Malformed patch document.</span></span> | <span data-ttu-id="4afdf-327">400 (錯誤的要求)</span><span class="sxs-lookup"><span data-stu-id="4afdf-327">400 (Bad Request)</span></span> |
| <span data-ttu-id="4afdf-328">修補程式文件有效，但在資源目前的狀態下，無法將變更套用到該資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-328">The patch document is valid, but the changes can't be applied to the resource in its current state.</span></span> | <span data-ttu-id="4afdf-329">409 (衝突)</span><span class="sxs-lookup"><span data-stu-id="4afdf-329">409 (Conflict)</span></span>

### <a name="delete-methods"></a><span data-ttu-id="4afdf-330">DELETE 方法</span><span class="sxs-lookup"><span data-stu-id="4afdf-330">DELETE methods</span></span>

<span data-ttu-id="4afdf-331">若刪除作業成功，網頁伺服器應會回應 HTTP 狀態碼 204，表示已成功處理程序，但回應本文不會包含進一步的資訊。</span><span class="sxs-lookup"><span data-stu-id="4afdf-331">If the delete operation is successful, the web server should respond with HTTP status code 204, indicating that the process has been successfully handled, but that the response body contains no further information.</span></span> <span data-ttu-id="4afdf-332">若該資源不存在，網頁伺服器可能會傳回 HTTP 404 (找不到)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-332">If the resource doesn't exist, the web server can return HTTP 404 (Not Found).</span></span>

### <a name="asynchronous-operations"></a><span data-ttu-id="4afdf-333">非同步作業</span><span class="sxs-lookup"><span data-stu-id="4afdf-333">Asynchronous operations</span></span>

<span data-ttu-id="4afdf-334">有時 POST、PUT、PATCH 或 DELETE 作業可能會需要一些時間才能完成處理。</span><span class="sxs-lookup"><span data-stu-id="4afdf-334">Sometimes a POST, PUT, PATCH, or DELETE operation might require processing that takes awhile to complete.</span></span> <span data-ttu-id="4afdf-335">若您將回應傳送給用戶端之前要等候作業完成，可能會導致無法接受的延遲。</span><span class="sxs-lookup"><span data-stu-id="4afdf-335">If you wait for completion before sending a response to the client, it may cause unacceptable latency.</span></span> <span data-ttu-id="4afdf-336">在此情況下，請考慮讓作業非同步。</span><span class="sxs-lookup"><span data-stu-id="4afdf-336">If so, consider making the operation asynchronous.</span></span> <span data-ttu-id="4afdf-337">傳回 HTTP 狀態碼 202 (已接受)，來表示已接受要求，但尚未完成處理。</span><span class="sxs-lookup"><span data-stu-id="4afdf-337">Return HTTP status code 202 (Accepted) to indicate the request was accepted for processing but is not completed.</span></span> 

<span data-ttu-id="4afdf-338">您應該公開會傳回非同步要求狀態的端點，讓用戶端可以透過輪詢狀態端點來監視狀態。</span><span class="sxs-lookup"><span data-stu-id="4afdf-338">You should expose an endpoint that returns the status of an asynchronous request, so the client can monitor the status by polling the status endpoint.</span></span> <span data-ttu-id="4afdf-339">請在 202 回應的 Location 標頭中包含狀態端點的 URI。</span><span class="sxs-lookup"><span data-stu-id="4afdf-339">Include the URI of the status endpoint in the Location header of the 202 response.</span></span> <span data-ttu-id="4afdf-340">例如︰</span><span class="sxs-lookup"><span data-stu-id="4afdf-340">For example:</span></span>

```http
HTTP/1.1 202 Accepted
Location: /api/status/12345
```

<span data-ttu-id="4afdf-341">若用戶端將 GET 要求傳送到此端點，回應中應包含要求的目前狀態。</span><span class="sxs-lookup"><span data-stu-id="4afdf-341">If the client sends a GET request to this endpoint, the response should contain the current status of the request.</span></span> <span data-ttu-id="4afdf-342">或者，您也可以在回應中包含預估的完成時間，或用以取消作業的連結。</span><span class="sxs-lookup"><span data-stu-id="4afdf-342">Optionally, it could also include an estimated time to completion or a link to cancel the operation.</span></span> 

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "status":"In progress",
    "link": { "rel":"cancel", "method":"delete", "href":"/api/status/12345"
}
```

<span data-ttu-id="4afdf-343">若非同步作業建立了新資源，在作業完成後狀態端點應會傳回狀態碼 303 (請參閱「其他」)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-343">If the asynchronous operation creates a new resource, the status endpoint should return status code 303 (See Other) after the operation completes.</span></span> <span data-ttu-id="4afdf-344">請在 303 回應中，包含可提供新資源 URI 的 Location 標頭：</span><span class="sxs-lookup"><span data-stu-id="4afdf-344">In the 303 response, include a Location header that gives the URI of the new resource:</span></span>

```http
HTTP/1.1 303 See Other
Location: /api/orders/12345
```

<span data-ttu-id="4afdf-345">如需詳細資訊，請參閱 [REST 中的非同步作業](https://www.adayinthelifeof.nl/2011/06/02/asynchronous-operations-in-rest/)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-345">For more information, see [Asynchronous operations in REST](https://www.adayinthelifeof.nl/2011/06/02/asynchronous-operations-in-rest/).</span></span>

## <a name="filter-and-paginate-data"></a><span data-ttu-id="4afdf-346">將資料篩選和分頁</span><span class="sxs-lookup"><span data-stu-id="4afdf-346">Filter and paginate data</span></span>

<span data-ttu-id="4afdf-347">透過單一 URI 公開資源集合，可能會導致應用程式在只需要資訊的子集時反而擷取大量資料。</span><span class="sxs-lookup"><span data-stu-id="4afdf-347">Exposing a collection of resources through a single URI can lead to applications fetching large amounts of data when only a subset of the information is required.</span></span> <span data-ttu-id="4afdf-348">例如，假設用戶端應用程式需要找出具有特定成本值的所有訂單。</span><span class="sxs-lookup"><span data-stu-id="4afdf-348">For example, suppose a client application needs to find all orders with a cost over a specific value.</span></span> <span data-ttu-id="4afdf-349">它可能會從 /orders URI 擷取所有的訂單，然後在用戶端上篩選這些訂單。</span><span class="sxs-lookup"><span data-stu-id="4afdf-349">It might retrieve all orders from the */orders* URI and then filter these orders on the client side.</span></span> <span data-ttu-id="4afdf-350">此程序顯然效率極低，</span><span class="sxs-lookup"><span data-stu-id="4afdf-350">Clearly this process is highly inefficient.</span></span> <span data-ttu-id="4afdf-351">會浪費裝載 Web API 之伺服器的網路頻寬和處理能力。</span><span class="sxs-lookup"><span data-stu-id="4afdf-351">It wastes network bandwidth and processing power on the server hosting the web API.</span></span>

<span data-ttu-id="4afdf-352">反之，API 可在 URI 的查詢字串中傳遞篩選條件 (例如 /orders?minCost=n)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-352">Instead, the API can allow passing a filter in the query string of the URI, such as */orders?minCost=n*.</span></span> <span data-ttu-id="4afdf-353">接著 Web API 會負責剖析和處理查詢字串中的 `minCost` 參數，並在伺服器端傳回篩選的結果。</span><span class="sxs-lookup"><span data-stu-id="4afdf-353">The web API is then responsible for parsing and handling the `minCost` parameter in the query string and returning the filtered results on the sever side.</span></span> 

<span data-ttu-id="4afdf-354">針對集合資源的 GET 要求有可能會傳回大量的項目。</span><span class="sxs-lookup"><span data-stu-id="4afdf-354">GET requests over collection resources can potentially return a large number of items.</span></span> <span data-ttu-id="4afdf-355">您應該設計 Web API 以限制任何單一要求所傳回的資料量。</span><span class="sxs-lookup"><span data-stu-id="4afdf-355">You should design a web API to limit the amount of data returned by any single request.</span></span> <span data-ttu-id="4afdf-356">請考慮提供可指定要擷取的項目上限的查詢字串，以及提供集合中的起始位移。</span><span class="sxs-lookup"><span data-stu-id="4afdf-356">Consider supporting query strings that specify the maximum number of items to retrieve and a starting offset into the collection.</span></span> <span data-ttu-id="4afdf-357">例如︰</span><span class="sxs-lookup"><span data-stu-id="4afdf-357">For example:</span></span>

```
/orders?limit=25&offset=50
```

<span data-ttu-id="4afdf-358">也請考慮設定要傳回的項目上限，以協助避免拒絕服務的攻擊。</span><span class="sxs-lookup"><span data-stu-id="4afdf-358">Also consider imposing an upper limit on the number of items returned, to help prevent Denial of Service attacks.</span></span> <span data-ttu-id="4afdf-359">為了協助用戶端應用程式，傳回已分頁資料的 GET 要求也應該包含某種形式的中繼資料，以便指出集合中的可用資源總數。</span><span class="sxs-lookup"><span data-stu-id="4afdf-359">To assist client applications, GET requests that return paginated data should also include some form of metadata that indicate the total number of resources available in the collection.</span></span> <span data-ttu-id="4afdf-360">您也可以考慮其他智慧型分頁策略。如需詳細資訊，請參閱 [API 設計注意事項：智慧型分頁](http://bizcoder.com/api-design-notes-smart-paging)</span><span class="sxs-lookup"><span data-stu-id="4afdf-360">You might also consider other intelligent paging strategies; for more information, see [API Design Notes: Smart Paging](http://bizcoder.com/api-design-notes-smart-paging)</span></span>

<span data-ttu-id="4afdf-361">您可以提供將欄位名稱當作值的排序參數 (例如 /orders?sort=ProductID)，來使用類似的策略在擷取資料時予以排序。</span><span class="sxs-lookup"><span data-stu-id="4afdf-361">You can use a similar strategy to sort data as it is fetched, by providing a sort parameter that takes a field name as the value, such as */orders?sort=ProductID*.</span></span> <span data-ttu-id="4afdf-362">不過，這種方法可能會對快取造成不良影響 (因為查詢字串參數會構成部分資源識別碼，而許多快取實作會將該識別碼當做索引鍵來快取資料)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-362">However, this approach can have a negative effect on caching, because query string parameters form part of the resource identifier used by many cache implementations as the key to cached data.</span></span>

<span data-ttu-id="4afdf-363">若每個項目都包含大量的資料，您可以延伸這個方法來限制傳回的欄位。</span><span class="sxs-lookup"><span data-stu-id="4afdf-363">You can extend this approach to limit the fields returned for each item, if each item contains a large amount of data.</span></span> <span data-ttu-id="4afdf-364">例如，您可以使用接受以逗號分隔清單之欄位的查詢字串參數 (如 /orders?fields=ProductID,Quantity)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-364">For example, you could use a query string parameter that accepts a comma-delimited list of fields, such as */orders?fields=ProductID,Quantity*.</span></span> 

<span data-ttu-id="4afdf-365">為查詢字串中所有選擇性參數提供有意義的預設值。</span><span class="sxs-lookup"><span data-stu-id="4afdf-365">Give all optional parameters in query strings meaningful defaults.</span></span> <span data-ttu-id="4afdf-366">例如，如果您實作分頁，可以將 `limit` 參數設為 10，並將 `offset` 參數設為 0；如果您實作排序，可以將排序參數設定為資源的索引鍵；如果您支援投射，可以將 `fields` 參數設定為資源中的所有欄位。</span><span class="sxs-lookup"><span data-stu-id="4afdf-366">For example, set the `limit` parameter to 10 and the `offset` parameter to 0 if you implement pagination, set the sort parameter to the key of the resource if you implement ordering, and set the `fields` parameter to all fields in the resource if you support projections.</span></span>

## <a name="support-partial-responses-for-large-binary-resources"></a><span data-ttu-id="4afdf-367">支援對大型二進位資源的部分回應</span><span class="sxs-lookup"><span data-stu-id="4afdf-367">Support partial responses for large binary resources</span></span>

<span data-ttu-id="4afdf-368">資源可能會包含大型的二進位欄位，例如檔案或影像。</span><span class="sxs-lookup"><span data-stu-id="4afdf-368">A resource may contain large binary fields, such as files or images.</span></span> <span data-ttu-id="4afdf-369">若要克服不可靠和間歇性連線所造成的問題，以及改善回應時間，請考慮以區塊形式擷取這類資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-369">To overcome problems caused by unreliable and intermittent connections and to improve response times, consider enabling such resources to be retrieved in chunks.</span></span> <span data-ttu-id="4afdf-370">若要這樣做，Web API 應支援大型資源 GET 要求的 Accept-Ranges 標頭。</span><span class="sxs-lookup"><span data-stu-id="4afdf-370">To do this, the web API should support the Accept-Ranges header for GET requests for large resources.</span></span> <span data-ttu-id="4afdf-371">此標頭表示 GET 作業支援部分的要求。</span><span class="sxs-lookup"><span data-stu-id="4afdf-371">This header indicates that the GET operation supports partial requests.</span></span> <span data-ttu-id="4afdf-372">用戶端應用程式能以位元組的範圍加以指定，送出會傳回資源子集的 GET 要求。</span><span class="sxs-lookup"><span data-stu-id="4afdf-372">The client application can submit GET requests that return a subset of a resource, specified as a range of bytes.</span></span> 

<span data-ttu-id="4afdf-373">此外，也請考慮實作這些資源的 HTTP HEAD 要求。</span><span class="sxs-lookup"><span data-stu-id="4afdf-373">Also, consider implementing HTTP HEAD requests for these resources.</span></span> <span data-ttu-id="4afdf-374">HEAD 要求與 GET 要求相似，不過前者只會傳回描述資源的 HTTP 標頭和空白的訊息本文。</span><span class="sxs-lookup"><span data-stu-id="4afdf-374">A HEAD request is similar to a GET request, except that it only returns the HTTP headers that describe the resource, with an empty message body.</span></span> <span data-ttu-id="4afdf-375">用戶端應用程式可以發出 HEAD 要求，以判斷是否要使用部分 GET 要求擷取資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-375">A client application can issue a HEAD request to determine whether to fetch a resource by using partial GET requests.</span></span> <span data-ttu-id="4afdf-376">例如︰</span><span class="sxs-lookup"><span data-stu-id="4afdf-376">For example:</span></span>

```HTTP
HEAD http://adventure-works.com/products/10?fields=productImage HTTP/1.1
```

<span data-ttu-id="4afdf-377">以下是回應訊息的範例：</span><span class="sxs-lookup"><span data-stu-id="4afdf-377">Here is an example response message:</span></span> 

```HTTP
HTTP/1.1 200 OK

Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 4580
```

<span data-ttu-id="4afdf-378">Content-Length 標頭會提供資源總大小，而 Accept-Ranges 標頭則會指出對應的 GET 作業支援部分結果。</span><span class="sxs-lookup"><span data-stu-id="4afdf-378">The Content-Length header gives the total size of the resource, and the Accept-Ranges header indicates that the corresponding GET operation supports partial results.</span></span> <span data-ttu-id="4afdf-379">用戶端應用程式可以使用這些資訊，以較小的區塊來擷取影像。</span><span class="sxs-lookup"><span data-stu-id="4afdf-379">The client application can use this information to retrieve the image in smaller chunks.</span></span> <span data-ttu-id="4afdf-380">第一個要求會使用 Range 標頭擷取前 2500 個位元組：</span><span class="sxs-lookup"><span data-stu-id="4afdf-380">The first request fetches the first 2500 bytes by using the Range header:</span></span>

```HTTP
GET http://adventure-works.com/products/10?fields=productImage HTTP/1.1
Range: bytes=0-2499
```

<span data-ttu-id="4afdf-381">回應訊息會藉由傳回 HTTP 狀態碼 206 來指出這是部分回應。</span><span class="sxs-lookup"><span data-stu-id="4afdf-381">The response message indicates that this is a partial response by returning HTTP status code 206.</span></span> <span data-ttu-id="4afdf-382">Content-Length 標頭能指出訊息本文傳回的實際位元組數目 (不是資源的大小)，而 Content-Range 標頭能指出這是資源的哪個部分 (4580 個位元組中的第 0-2499 個位元組)：</span><span class="sxs-lookup"><span data-stu-id="4afdf-382">The Content-Length header specifies the actual number of bytes returned in the message body (not the size of the resource), and the Content-Range header indicates which part of the resource this is (bytes 0-2499 out of 4580):</span></span>

```HTTP
HTTP/1.1 206 Partial Content

Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 2500
Content-Range: bytes 0-2499/4580

[...]
```

<span data-ttu-id="4afdf-383">來自用戶端應用程式的後續要求，可以擷取資源的其餘部分。</span><span class="sxs-lookup"><span data-stu-id="4afdf-383">A subsequent request from the client application can retrieve the remainder of the resource.</span></span>

## <a name="use-hateoas-to-enable-navigation-to-related-resources"></a><span data-ttu-id="4afdf-384">使用 HATEOAS 方法來啟用相關資源的導覽</span><span class="sxs-lookup"><span data-stu-id="4afdf-384">Use HATEOAS to enable navigation to related resources</span></span>

<span data-ttu-id="4afdf-385">REST 背後的其中一個主要動機，是它應該可以在不需要事先知道 URI 配置的情況下瀏覽整組資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-385">One of the primary motivations behind REST is that it should be possible to navigate the entire set of resources without requiring prior knowledge of the URI scheme.</span></span> <span data-ttu-id="4afdf-386">每個 HTTP GET 要求都應傳回讓您能透過回應包含之超連結尋找與要求物件直接相關之資源的資訊，您也應提供它描述每個資源上可供使用之作業的資訊。</span><span class="sxs-lookup"><span data-stu-id="4afdf-386">Each HTTP GET request should return the information necessary to find the resources related directly to the requested object through hyperlinks included in the response, and it should also be provided with information that describes the operations available on each of these resources.</span></span> <span data-ttu-id="4afdf-387">此原則就是所謂的 HATEOAS (Hypertext as the Engine of Application State)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-387">This principle is known as HATEOAS, or Hypertext as the Engine of Application State.</span></span> <span data-ttu-id="4afdf-388">系統實際上是有限的狀態機器，而每個要求的回應都包含從某個狀態轉換為另一個狀態所需的資訊。其他任何資訊都不是必要資訊。</span><span class="sxs-lookup"><span data-stu-id="4afdf-388">The system is effectively a finite state machine, and the response to each request contains the information necessary to move from one state to another; no other information should be necessary.</span></span>

> [!NOTE]
> <span data-ttu-id="4afdf-389">目前還沒有定義該如何為 HATEOAS 原則建立模型的標準或規格。</span><span class="sxs-lookup"><span data-stu-id="4afdf-389">Currently there are no standards or specifications that define how to model the HATEOAS principle.</span></span> <span data-ttu-id="4afdf-390">本節所示的範例將說明一個可行的解決方案。</span><span class="sxs-lookup"><span data-stu-id="4afdf-390">The examples shown in this section illustrate one possible solution.</span></span>
>
>

<span data-ttu-id="4afdf-391">例如，為了處理訂單與客戶之間的關聯性，訂單的表示法可能會包含用以識別訂單客戶可用作業的連結。</span><span class="sxs-lookup"><span data-stu-id="4afdf-391">For example, to handle the relationship between an order and a customer, the representation of an order could include links that identify the available operations for the customer of the order.</span></span> <span data-ttu-id="4afdf-392">以下是可能的表示法：</span><span class="sxs-lookup"><span data-stu-id="4afdf-392">Here is a possible representation:</span></span> 

```json
{
  "orderID":3,
  "productID":2,
  "quantity":4,
  "orderValue":16.60,
  "links":[
    {
      "rel":"customer",
      "href":"http://adventure-works.com/customers/3", 
      "action":"GET",
      "types":["text/xml","application/json"] 
    },
    {
      "rel":"customer",
      "href":"http://adventure-works.com/customers/3", 
      "action":"PUT",
      "types":["application/x-www-form-urlencoded"]
    },
    {
      "rel":"customer",
      "href":"http://adventure-works.com/customers/3",
      "action":"DELETE",
      "types":[]
    },
    {
      "rel":"self",
      "href":"http://adventure-works.com/orders/3", 
      "action":"GET",
      "types":["text/xml","application/json"]
    },
    {
      "rel":"self",
      "href":"http://adventure-works.com/orders/3", 
      "action":"PUT",
      "types":["application/x-www-form-urlencoded"]
    },
    {
      "rel":"self",
      "href":"http://adventure-works.com/orders/3", 
      "action":"DELETE",
      "types":[]
    }]
}
```

<span data-ttu-id="4afdf-393">在此範例中，`links` 陣列有一組連結。</span><span class="sxs-lookup"><span data-stu-id="4afdf-393">In this example, the `links` array has a set of links.</span></span> <span data-ttu-id="4afdf-394">每個連結都代表相關實體上的作業。</span><span class="sxs-lookup"><span data-stu-id="4afdf-394">Each link represents an operation on a related entity.</span></span> <span data-ttu-id="4afdf-395">每個連結的資料都包含關聯性 (「客戶」)、URI (`http://adventure-works.com/customers/3`)、HTTP 方法，以及支援的 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="4afdf-395">The data for each link includes the relationship ("customer"), the URI (`http://adventure-works.com/customers/3`), the HTTP method, and the supported MIME types.</span></span> <span data-ttu-id="4afdf-396">這是用戶端應用程式要能夠叫用作業需要的所有資訊。</span><span class="sxs-lookup"><span data-stu-id="4afdf-396">This is all the information that a client application needs to be able to invoke the operation.</span></span> 

<span data-ttu-id="4afdf-397">`links` 陣列也會包含關於已擷取資源本身的自我參考資訊。</span><span class="sxs-lookup"><span data-stu-id="4afdf-397">The `links` array also includes self-referencing information about the resource itself that has been retrieved.</span></span> <span data-ttu-id="4afdf-398">這些項目具有自我關聯性。</span><span class="sxs-lookup"><span data-stu-id="4afdf-398">These have the relationship *self*.</span></span>

<span data-ttu-id="4afdf-399">傳回的連結集可能會變更，視資源的狀態而定。</span><span class="sxs-lookup"><span data-stu-id="4afdf-399">The set of links that are returned may change, depending on the state of the resource.</span></span> <span data-ttu-id="4afdf-400">這就是為何我們將超文字稱為「應用程式狀態的引擎」。</span><span class="sxs-lookup"><span data-stu-id="4afdf-400">This is what is meant by hypertext being the "engine of application state."</span></span>

## <a name="versioning-a-restful-web-api"></a><span data-ttu-id="4afdf-401">符合 REST 限制的 Web API 版本控制</span><span class="sxs-lookup"><span data-stu-id="4afdf-401">Versioning a RESTful web API</span></span>

<span data-ttu-id="4afdf-402">Web API 維持靜態的可能性極低。</span><span class="sxs-lookup"><span data-stu-id="4afdf-402">It is highly unlikely that a web API will remain static.</span></span> <span data-ttu-id="4afdf-403">隨著商務需求變更，我們可能會加入新資源集合、資源之間的關聯性可能會改變，也會修改資源中的資料結構。</span><span class="sxs-lookup"><span data-stu-id="4afdf-403">As business requirements change new collections of resources may be added, the relationships between resources might change, and the structure of the data in resources might be amended.</span></span> <span data-ttu-id="4afdf-404">雖然更新 Web API 來處理新的或不同的需求是相對簡單的程序，不過您必須將這類變更對取用 Web API 之用戶端應用程式所造成的效果納入考量。</span><span class="sxs-lookup"><span data-stu-id="4afdf-404">While updating a web API to handle new or differing requirements is a relatively straightforward process, you must consider the effects that such changes will have on client applications consuming the web API.</span></span> <span data-ttu-id="4afdf-405">問題在於雖然設計和實作 Web API 的開發人員擁有該 API 完整的控制能力，不過對於從遠端作業之第三方組織所建置的用戶端應用程式，開發人員並沒有相同程度的控制能力。</span><span class="sxs-lookup"><span data-stu-id="4afdf-405">The issue is that although the developer designing and implementing a web API has full control over that API, the developer does not have the same degree of control over client applications which may be built by third party organizations operating remotely.</span></span> <span data-ttu-id="4afdf-406">主要的要務是讓現有用戶端應用程式在未變更的情況下繼續運作，同時允許新用戶端應用程式充分運用新功能和資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-406">The primary imperative is to enable existing client applications to continue functioning unchanged while allowing new client applications to take advantage of new features and resources.</span></span>

<span data-ttu-id="4afdf-407">版本控制可讓 Web API 指定其公開的功能和資源，而用戶端應用程式則可以提交導向特定版本之功能或資源的要求。</span><span class="sxs-lookup"><span data-stu-id="4afdf-407">Versioning enables a web API to indicate the features and resources that it exposes, and a client application can submit requests that are directed to a specific version of a feature or resource.</span></span> <span data-ttu-id="4afdf-408">下列小節描述幾個不同的方法，每個方法都有其優點和缺點。</span><span class="sxs-lookup"><span data-stu-id="4afdf-408">The following sections describe several different approaches, each of which has its own benefits and trade-offs.</span></span>

### <a name="no-versioning"></a><span data-ttu-id="4afdf-409">無版本控制</span><span class="sxs-lookup"><span data-stu-id="4afdf-409">No versioning</span></span>
<span data-ttu-id="4afdf-410">這是最簡單的方法，而且也是某些內部 API 可接收的方法。</span><span class="sxs-lookup"><span data-stu-id="4afdf-410">This is the simplest approach, and may be acceptable for some internal APIs.</span></span> <span data-ttu-id="4afdf-411">重大變更可能會以新資源或新連結來呈現。</span><span class="sxs-lookup"><span data-stu-id="4afdf-411">Big changes could be represented as new resources or new links.</span></span>  <span data-ttu-id="4afdf-412">將內容加入現有資源可能不會成為重大變更，因為未預期要查看此內容的用戶端應用程式會直接忽略。</span><span class="sxs-lookup"><span data-stu-id="4afdf-412">Adding content to existing resources might not present a breaking change as client applications that are not expecting to see this content will simply ignore it.</span></span>

<span data-ttu-id="4afdf-413">例如，傳送給 URI *http://adventure-works.com/customers/3* 的要求應該會傳回單一客戶的詳細資料，其中包含用戶端應用程式所預期的 `id`、`name` 和 `address` 欄位：</span><span class="sxs-lookup"><span data-stu-id="4afdf-413">For example, a request to the URI *http://adventure-works.com/customers/3* should return the details of a single customer containing `id`, `name`, and `address` fields expected by the client application:</span></span>

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

> [!NOTE]
> <span data-ttu-id="4afdf-414">為求簡單，本節所示的範例回應不包含 HATEOAS 連結。</span><span class="sxs-lookup"><span data-stu-id="4afdf-414">For simplicity, the example responses shown in this section do not include HATEOAS links.</span></span>
>
>

<span data-ttu-id="4afdf-415">如果您將 `DateCreated` 欄位加入客戶資源的結構描述，回應看起來會像這樣：</span><span class="sxs-lookup"><span data-stu-id="4afdf-415">If the `DateCreated` field is added to the schema of the customer resource, then the response would look like this:</span></span>

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":"1 Microsoft Way Redmond WA 98053"}
```

<span data-ttu-id="4afdf-416">如果現有用戶端應用程式能略過無法辨認的欄位，它們可能會繼續正常運作，而新的用戶端應用程式可以在經過設計後處理這個新欄位。</span><span class="sxs-lookup"><span data-stu-id="4afdf-416">Existing client applications might continue functioning correctly if they are capable of ignoring unrecognized fields, while new client applications can be designed to handle this new field.</span></span> <span data-ttu-id="4afdf-417">不過，如果資源的結構描述發生更巨大的變動 (如移除或重新命名欄位)，或資源之間的關聯性改變，這些變更可能會構成阻礙現有用戶端應用程式正常運作的重大變更。</span><span class="sxs-lookup"><span data-stu-id="4afdf-417">However, if more radical changes to the schema of resources occur (such as removing or renaming fields) or the relationships between resources change then these may constitute breaking changes that prevent existing client applications from functioning correctly.</span></span> <span data-ttu-id="4afdf-418">在這些情況下，您應該考慮下列其中一個方法。</span><span class="sxs-lookup"><span data-stu-id="4afdf-418">In these situations you should consider one of the following approaches.</span></span>

### <a name="uri-versioning"></a><span data-ttu-id="4afdf-419">URI 版本控制</span><span class="sxs-lookup"><span data-stu-id="4afdf-419">URI versioning</span></span>
<span data-ttu-id="4afdf-420">每次修改 Web API 或變更資源的結構描述時，您會在每個資源的 URI 加入版本號碼。</span><span class="sxs-lookup"><span data-stu-id="4afdf-420">Each time you modify the web API or change the schema of resources, you add a version number to the URI for each resource.</span></span> <span data-ttu-id="4afdf-421">早已存在的 URI 應維持先前的運作，傳回符合原始結構描述的資源。</span><span class="sxs-lookup"><span data-stu-id="4afdf-421">The previously existing URIs should continue to operate as before, returning resources that conform to their original schema.</span></span>

<span data-ttu-id="4afdf-422">延伸上述範例，如果將 `address` 欄位重建為包含位址之每個構成組件的子欄位 (如 `streetAddress`、`city`、`state` 及 `zipCode`)，您可以透過包含版本號碼的 URI (如 http://adventure-works.com/v2/customers/3:) 公開這個版本的資源：</span><span class="sxs-lookup"><span data-stu-id="4afdf-422">Extending the previous example, if the `address` field is restructured into sub-fields containing each constituent part of the address (such as `streetAddress`, `city`, `state`, and `zipCode`), this version of the resource could be exposed through a URI containing a version number, such as http://adventure-works.com/v2/customers/3:</span></span>

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":{"streetAddress":"1 Microsoft Way","city":"Redmond","state":"WA","zipCode":98053}}
```

<span data-ttu-id="4afdf-423">這個版本控制機制非常簡單，但需仰賴伺服器將要求路由傳送到適當端點。</span><span class="sxs-lookup"><span data-stu-id="4afdf-423">This versioning mechanism is very simple but depends on the server routing the request to the appropriate endpoint.</span></span> <span data-ttu-id="4afdf-424">不過，在經過數個反覆項目後當 Web API 成熟時，它會變得難以揮灑，因此伺服器必須支援許多不同的版本。</span><span class="sxs-lookup"><span data-stu-id="4afdf-424">However, it can become unwieldy as the web API matures through several iterations and the server has to support a number of different versions.</span></span> <span data-ttu-id="4afdf-425">此外，從純化論者的觀點來看，在所有情況下用戶端應用程式都在擷取相同的資料 (客戶 3)，所以 URI 不應該因版本而有所不同。</span><span class="sxs-lookup"><span data-stu-id="4afdf-425">Also, from a purist’s point of view, in all cases the client applications are fetching the same data (customer 3), so the URI should not really be different depending on the version.</span></span> <span data-ttu-id="4afdf-426">此配置也會讓 HATEOAS 的實作變得更複雜，因為所有連結都需要在它們的 URI 中包含版本號碼。</span><span class="sxs-lookup"><span data-stu-id="4afdf-426">This scheme also complicates implementation of HATEOAS as all links will need to include the version number in their URIs.</span></span>

### <a name="query-string-versioning"></a><span data-ttu-id="4afdf-427">查詢字串版本控制</span><span class="sxs-lookup"><span data-stu-id="4afdf-427">Query string versioning</span></span>
<span data-ttu-id="4afdf-428">與其提供多個 URI，您可以在附加至 HTTP 要求的查詢字串中，藉由使用參數來指定資源的版本，如 *http://adventure-works.com/customers/3?version=2*。</span><span class="sxs-lookup"><span data-stu-id="4afdf-428">Rather than providing multiple URIs, you can specify the version of the resource by using a parameter within the query string appended to the HTTP request, such as *http://adventure-works.com/customers/3?version=2*.</span></span> <span data-ttu-id="4afdf-429">如果較舊的用戶端應用程式省略版本參數，它應該預設成有意義的值 (如 1)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-429">The version parameter should default to a meaningful value such as 1 if it is omitted by older client applications.</span></span>

<span data-ttu-id="4afdf-430">這個方法具有語意上的優點，因為您總是從相同的 URI 擷取相同的資源，不過這還是要取決於處理要求以剖析查詢字串，然後回傳適當 HTTP 回應的程式碼。</span><span class="sxs-lookup"><span data-stu-id="4afdf-430">This approach has the semantic advantage that the same resource is always retrieved from the same URI, but it depends on the code that handles the request to parse the query string and send back the appropriate HTTP response.</span></span> <span data-ttu-id="4afdf-431">這個方法也需要面臨與實作 HATEOAS 同等複雜的 URI 版本控制機制。</span><span class="sxs-lookup"><span data-stu-id="4afdf-431">This approach also suffers from the same complications for implementing HATEOAS as the URI versioning mechanism.</span></span>

> [!NOTE]
> <span data-ttu-id="4afdf-432">某些較舊的網頁瀏覽器和 Web Proxy 不會快取在 URI 中包含查詢字串的要求回應。</span><span class="sxs-lookup"><span data-stu-id="4afdf-432">Some older web browsers and web proxies will not cache responses for requests that include a query string in the URI.</span></span> <span data-ttu-id="4afdf-433">對於使用 Web API 且從這類網頁瀏覽器內部執行的 Web 應用程式，這會對應用程式的效能造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="4afdf-433">This can have an adverse impact on performance for web applications that use a web API and that run from within such a web browser.</span></span>
>
>

### <a name="header-versioning"></a><span data-ttu-id="4afdf-434">標頭版本控制</span><span class="sxs-lookup"><span data-stu-id="4afdf-434">Header versioning</span></span>
<span data-ttu-id="4afdf-435">與其以查詢字串參數的形式附加版本號碼，您可以實作指出資源版本的自訂標頭。</span><span class="sxs-lookup"><span data-stu-id="4afdf-435">Rather than appending the version number as a query string parameter, you could implement a custom header that indicates the version of the resource.</span></span> <span data-ttu-id="4afdf-436">這個方法需要用戶端應用程式將適當標頭加入任何要求中，不過如果省略版本標頭，處理用戶端要求的程式碼可以使用預設值 (第 1 版)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-436">This approach requires that the client application adds the appropriate header to any requests, although the code handling the client request could use a default value (version 1) if the version header is omitted.</span></span> <span data-ttu-id="4afdf-437">下列範例使用名為 Custom-Header的自訂標頭。</span><span class="sxs-lookup"><span data-stu-id="4afdf-437">The following examples utilize a custom header named *Custom-Header*.</span></span> <span data-ttu-id="4afdf-438">此標頭的值指出 Web API 的版本。</span><span class="sxs-lookup"><span data-stu-id="4afdf-438">The value of this header indicates the version of web API.</span></span>

<span data-ttu-id="4afdf-439">第 1 版：</span><span class="sxs-lookup"><span data-stu-id="4afdf-439">Version 1:</span></span>

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
Custom-Header: api-version=1
```

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

<span data-ttu-id="4afdf-440">第 2 版：</span><span class="sxs-lookup"><span data-stu-id="4afdf-440">Version 2:</span></span>

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
Custom-Header: api-version=2
```

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":{"streetAddress":"1 Microsoft Way","city":"Redmond","state":"WA","zipCode":98053}}
```

<span data-ttu-id="4afdf-441">請注意，如同先前的兩個方法，實作 HATEOAS 需要將適當的自訂標頭加入任何連結中。</span><span class="sxs-lookup"><span data-stu-id="4afdf-441">Note that as with the previous two approaches, implementing HATEOAS requires including the appropriate custom header in any links.</span></span>

### <a name="media-type-versioning"></a><span data-ttu-id="4afdf-442">媒體類型版本控制</span><span class="sxs-lookup"><span data-stu-id="4afdf-442">Media type versioning</span></span>
<span data-ttu-id="4afdf-443">當用戶端應用程式將 HTTP GET 要求傳送至 Web 伺服器時，它應該會使用 Accept 標頭來規定可處理的內容格式 (如本指引稍早所述)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-443">When a client application sends an HTTP GET request to a web server it should stipulate the format of the content that it can handle by using an Accept header, as described earlier in this guidance.</span></span> <span data-ttu-id="4afdf-444">Accept 標頭的目的經常是讓用戶端應用程式指定回應本文應該是 XML、JSON 或其他某些用戶端可剖析的通用格式。</span><span class="sxs-lookup"><span data-stu-id="4afdf-444">Frequently the purpose of the *Accept* header is to allow the client application to specify whether the body of the response should be XML, JSON, or some other common format that the client can parse.</span></span> <span data-ttu-id="4afdf-445">不過，您也可以定義自訂媒體類型，加入讓用戶端應用程式指定預期之資源版本的資訊。</span><span class="sxs-lookup"><span data-stu-id="4afdf-445">However, it is possible to define custom media types that include information enabling the client application to indicate which version of a resource it is expecting.</span></span> <span data-ttu-id="4afdf-446">下列範例顯示將 Accept 標頭指定為 application/vnd.adventure-works.v1+json 值的要求。</span><span class="sxs-lookup"><span data-stu-id="4afdf-446">The following example shows a request that specifies an *Accept* header with the value *application/vnd.adventure-works.v1+json*.</span></span> <span data-ttu-id="4afdf-447">Vnd.adventure works.v1 元素指示 Web 伺服器傳回第 1 版的資源，而 json 元素指定回應本文的格式應該是 JSON：</span><span class="sxs-lookup"><span data-stu-id="4afdf-447">The *vnd.adventure-works.v1* element indicates to the web server that it should return version 1 of the resource, while the *json* element specifies that the format of the response body should be JSON:</span></span>

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
Accept: application/vnd.adventure-works.v1+json
```

<span data-ttu-id="4afdf-448">處理要求的程式碼會負責處理 Accept 標頭且盡可能遵循 (用戶端應用程式可能會在 Accept 標頭中指定多個格式。不論如何，Web 伺服器可以選擇最合適的回應本文格式)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-448">The code handling the request is responsible for processing the *Accept* header and honoring it as far as possible (the client application may specify multiple formats in the *Accept* header, in which case the web server can choose the most appropriate format for the response body).</span></span> <span data-ttu-id="4afdf-449">Web 伺服器會使用 Content-Type 標頭確認回應本文中的資料格式：</span><span class="sxs-lookup"><span data-stu-id="4afdf-449">The web server confirms the format of the data in the response body by using the Content-Type header:</span></span>

```HTTP
HTTP/1.1 200 OK
Content-Type: application/vnd.adventure-works.v1+json; charset=utf-8

{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

<span data-ttu-id="4afdf-450">如果 Accept 標頭未指定任何已知的媒體類型，Web 伺服器可能會產生 HTTP 406 (無法接受) 回應訊息，或以預設媒體類型傳回訊息。</span><span class="sxs-lookup"><span data-stu-id="4afdf-450">If the Accept header does not specify any known media types, the web server could generate an HTTP 406 (Not Acceptable) response message or return a message with a default media type.</span></span>

<span data-ttu-id="4afdf-451">這個方法可以說是最純粹的版本控制機制，自然也符合 HATEOAS 的本質，也就是在資源連結中包含 MIME 類型的相關資料。</span><span class="sxs-lookup"><span data-stu-id="4afdf-451">This approach is arguably the purest of the versioning mechanisms and lends itself naturally to HATEOAS, which can include the MIME type of related data in resource links.</span></span>

> [!NOTE]
> <span data-ttu-id="4afdf-452">在選擇版本控制策略時，您也應該將對效能的潛在影響 (特別是 Web 伺服器上的快取) 納入考量。</span><span class="sxs-lookup"><span data-stu-id="4afdf-452">When you select a versioning strategy, you should also consider the implications on performance, especially caching on the web server.</span></span> <span data-ttu-id="4afdf-453">有鑑於同一個 URI/查詢字串組合每次都會參考相同的資料，因此 URI 版本控制和查詢字串版本控制等配置具備易於快取的特性。</span><span class="sxs-lookup"><span data-stu-id="4afdf-453">The URI versioning and Query String versioning schemes are cache-friendly inasmuch as the same URI/query string combination refers to the same data each time.</span></span>
>
> <span data-ttu-id="4afdf-454">標頭版本控制和媒體類型版本控制等機制，通常需要額外的邏輯來檢查自訂標頭或 Accept 標頭中的值。</span><span class="sxs-lookup"><span data-stu-id="4afdf-454">The Header versioning and Media Type versioning mechanisms typically require additional logic to examine the values in the custom header or the Accept header.</span></span> <span data-ttu-id="4afdf-455">在大規模的環境中，使用不同版本之 Web API 的眾多用戶端可能會導致伺服器端快取出現大量的重複資料。</span><span class="sxs-lookup"><span data-stu-id="4afdf-455">In a large-scale environment, many clients using different versions of a web API can result in a significant amount of duplicated data in a server-side cache.</span></span> <span data-ttu-id="4afdf-456">如果用戶端應用程式透過實作快取的 Proxy 與 Web 伺服器通訊，而且只有在快取中目前沒有要求之資料的副本時才會將要求轉送到 Web 伺服器，這個問題可能會變得更嚴重。</span><span class="sxs-lookup"><span data-stu-id="4afdf-456">This issue can become acute if a client application communicates with a web server through a proxy that implements caching, and that only forwards a request to the web server if it does not currently hold a copy of the requested data in its cache.</span></span>
>
>

## <a name="open-api-initiative"></a><span data-ttu-id="4afdf-457">Open API Initiative</span><span class="sxs-lookup"><span data-stu-id="4afdf-457">Open API Initiative</span></span>
<span data-ttu-id="4afdf-458">[Open API Initiative](https://www.openapis.org/) 是由產業協會所建立，用來將各廠商間的 REST API 描述標準化。</span><span class="sxs-lookup"><span data-stu-id="4afdf-458">The [Open API Initiative](https://www.openapis.org/) was created by an industry consortium to standardize REST API descriptions across vendors.</span></span> <span data-ttu-id="4afdf-459">隨著此計畫，Swagger 2.0 規格已重新命名為 OpenAPI 規格 (OAS)，並由 Open API 計畫管理。</span><span class="sxs-lookup"><span data-stu-id="4afdf-459">As part of this initiative, the Swagger 2.0 specification was renamed the OpenAPI Specification (OAS) and brought under the Open API Initiative.</span></span>

<span data-ttu-id="4afdf-460">您可以為您的 Web API 採用 OpenAPI。</span><span class="sxs-lookup"><span data-stu-id="4afdf-460">You may want to adopt OpenAPI for your web APIs.</span></span> <span data-ttu-id="4afdf-461">考慮事項：</span><span class="sxs-lookup"><span data-stu-id="4afdf-461">Some points to consider:</span></span>

- <span data-ttu-id="4afdf-462">OpenAPI 規格隨附一組對於應如何設計 REST API 已有定見的指導方針。</span><span class="sxs-lookup"><span data-stu-id="4afdf-462">The OpenAPI Specification comes with a set of opinionated guidelines on how a REST API should be designed.</span></span> <span data-ttu-id="4afdf-463">該指引對於互通性會有好處，但在設計您的 API 時需要更小心，才能符合規格。</span><span class="sxs-lookup"><span data-stu-id="4afdf-463">That has advantages for interoperability, but requires more care when designing your API to conform to the specification.</span></span>
- <span data-ttu-id="4afdf-464">OpenAPI 會將合約優先方法升階，而不是將實作優先方法升階。</span><span class="sxs-lookup"><span data-stu-id="4afdf-464">OpenAPI promotes a contract-first approach, rather than an implementation-first approach.</span></span> <span data-ttu-id="4afdf-465">合約優先表示您會先設計 API 合約 (介面)，然後編寫實作合約的程式碼。</span><span class="sxs-lookup"><span data-stu-id="4afdf-465">Contract-first means you design the API contract (the interface) first and then write code that implements the contract.</span></span> 
- <span data-ttu-id="4afdf-466">Swagger 等工具可以從 API 合約產生用戶端程式庫或文件集。</span><span class="sxs-lookup"><span data-stu-id="4afdf-466">Tools like Swagger can generate client libraries or documentation from API contracts.</span></span> <span data-ttu-id="4afdf-467">例如，請參閱[使用 Swagger 的 ASP.NET Web API 說明頁面](/aspnet/core/tutorials/web-api-help-pages-using-swagger)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-467">For example, see [ASP.NET Web API Help Pages using Swagger](/aspnet/core/tutorials/web-api-help-pages-using-swagger).</span></span>

## <a name="more-information"></a><span data-ttu-id="4afdf-468">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="4afdf-468">More information</span></span>
* <span data-ttu-id="4afdf-469">[Microsoft REST API 指導方針](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-469">[Microsoft REST API Guidelines](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md).</span></span> <span data-ttu-id="4afdf-470">設計公用 REST API 的詳細建議。</span><span class="sxs-lookup"><span data-stu-id="4afdf-470">Detailed recommendations for designing public REST APIs.</span></span>
* <span data-ttu-id="4afdf-471">[REST 逐步指南](http://restcookbook.com/)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-471">[The REST Cookbook](http://restcookbook.com/).</span></span> <span data-ttu-id="4afdf-472">建置 REST 式 API 的簡介。</span><span class="sxs-lookup"><span data-stu-id="4afdf-472">Introduction to building RESTful APIs.</span></span>
* <span data-ttu-id="4afdf-473">[Web API 檢查清單](https://mathieu.fenniak.net/the-api-checklist/)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-473">[Web API Checklist](https://mathieu.fenniak.net/the-api-checklist/).</span></span> <span data-ttu-id="4afdf-474">在設計及實作 Web API 時應納入考量的實用項目清單。</span><span class="sxs-lookup"><span data-stu-id="4afdf-474">A useful list of items to consider when designing and implementing a web API.</span></span>
* <span data-ttu-id="4afdf-475">[Open API Initiative](https://www.openapis.org/)。</span><span class="sxs-lookup"><span data-stu-id="4afdf-475">[Open API Initiative](https://www.openapis.org/).</span></span> <span data-ttu-id="4afdf-476">Open API 的文件和實作詳細資料。</span><span class="sxs-lookup"><span data-stu-id="4afdf-476">Documentation and implementation details on Open API.</span></span>

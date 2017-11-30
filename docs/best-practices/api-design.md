---
title: "API 設計指引"
description: "說明如何建立設計完善之 API 的指引。"
author: dragon119
ms.date: 07/13/2016
pnp.series.title: Best Practices
ms.openlocfilehash: 3ffadce1b0c4a4da808e52d61cff0b7f0b27de11
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="api-design"></a><span data-ttu-id="0459a-103">API 設計</span><span class="sxs-lookup"><span data-stu-id="0459a-103">API design</span></span>
[!INCLUDE [header](../_includes/header.md)]

<span data-ttu-id="0459a-104">許多現代化的 Web 型解決方案利用 Web 伺服器代管的 Web 服務來提供遠端用戶端應用程式所需的功能。</span><span class="sxs-lookup"><span data-stu-id="0459a-104">Many modern web-based solutions make the use of web services, hosted by web servers, to provide functionality for remote client applications.</span></span> <span data-ttu-id="0459a-105">Web 服務開放的作業構成 Web API。</span><span class="sxs-lookup"><span data-stu-id="0459a-105">The operations that a web service exposes constitute a web API.</span></span> <span data-ttu-id="0459a-106">設計良好的 Web API 應以支援下列特性為目標：</span><span class="sxs-lookup"><span data-stu-id="0459a-106">A well-designed web API should aim to support:</span></span>

* <span data-ttu-id="0459a-107">**平台獨立性**。</span><span class="sxs-lookup"><span data-stu-id="0459a-107">**Platform independence**.</span></span> <span data-ttu-id="0459a-108">用戶端應用程式應該要能夠利用 Web 服務提供的 API，而不受 API 公開之資料或作業的實際實作方法所限制。</span><span class="sxs-lookup"><span data-stu-id="0459a-108">Client applications should be able to utilize the API that the web service provides without requiring how the data or operations that API exposes are physically implemented.</span></span> <span data-ttu-id="0459a-109">若要達到這個目標，API 必須遵守促使用戶端應用程式和 Web 服務就使用之資料格式，以及用戶端應用程式和 Web 服務兩者交換之資料格式等取得共識的通用標準。</span><span class="sxs-lookup"><span data-stu-id="0459a-109">This requires that the API abides by common standards that enable a client application and web service to agree on which data formats to use, and the structure of the data that is exchanged between client applications and the web service.</span></span>
* <span data-ttu-id="0459a-110">**服務演化**。</span><span class="sxs-lookup"><span data-stu-id="0459a-110">**Service evolution**.</span></span> <span data-ttu-id="0459a-111">Web 服務應該要能獨立於用戶端應用程式之外演化及新增 (或移除) 功能。</span><span class="sxs-lookup"><span data-stu-id="0459a-111">The web service should be able to evolve and add (or remove) functionality independently from client applications.</span></span> <span data-ttu-id="0459a-112">當 Web 服務提供的功能變更時，現有用戶端應用程式應該要能不經修改即可繼續運作。</span><span class="sxs-lookup"><span data-stu-id="0459a-112">Existing client applications should be able to continue to operate unmodified as the features provided by the web service change.</span></span> <span data-ttu-id="0459a-113">所有功能也應該要可供探索，讓用戶端應用程式得以充分運用。</span><span class="sxs-lookup"><span data-stu-id="0459a-113">All functionality should also be discoverable, so that client applications can fully utilize it.</span></span>

<span data-ttu-id="0459a-114">本指引的目的在於描述設計 Web API 時應考量的議題。</span><span class="sxs-lookup"><span data-stu-id="0459a-114">The purpose of this guidance is to describe the issues that you should consider when designing a web API.</span></span>

## <a name="introduction-to-representational-state-transfer-rest"></a><span data-ttu-id="0459a-115">具象狀態傳輸 (REST) 簡介</span><span class="sxs-lookup"><span data-stu-id="0459a-115">Introduction to Representational State Transfer (REST)</span></span>
<span data-ttu-id="0459a-116">在 2000 年，Roy Fielding 在他的論文中提出建構 Web 服務所公開之作業的替代架構方法：REST。</span><span class="sxs-lookup"><span data-stu-id="0459a-116">In his dissertation in 2000, Roy Fielding proposed an alternative architectural approach to structuring the operations exposed by web services; REST.</span></span> <span data-ttu-id="0459a-117">REST 是建置以超媒體為基礎之分散式系統的架構樣式。</span><span class="sxs-lookup"><span data-stu-id="0459a-117">REST is an architectural style for building distributed systems based on hypermedia.</span></span> <span data-ttu-id="0459a-118">REST 模型的主要優點是基於開放標準，因此能避免模型實作或存取模型的用戶端應用程式受到任何特定實作約束。</span><span class="sxs-lookup"><span data-stu-id="0459a-118">A primary advantage of the REST model is that it is based on open standards and does not bind the implementation of the model or the client applications that access it to any specific implementation.</span></span> <span data-ttu-id="0459a-119">比方說，您可以使用 Microsoft ASP.NET Web API 來實作 REST Web 服務，以及使用任何能產生 HTTP 要求及剖析 HTTP 回應的語言和工具組來開發用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="0459a-119">For example, a REST web service could be implemented by using the Microsoft ASP.NET Web API, and client applications could be developed by using any language and toolset that can generate HTTP requests and parse HTTP responses.</span></span>

> [!NOTE]
> <span data-ttu-id="0459a-120">REST 實際上獨立於任何基礎通訊協定之外，因此不一定要與 HTTP 連結。</span><span class="sxs-lookup"><span data-stu-id="0459a-120">REST is actually independent of any underlying protocol and is not necessarily tied to HTTP.</span></span> <span data-ttu-id="0459a-121">不過在以 REST 為基礎的系統實作中，最常見的實作是將 HTTP 當做傳送及接收要求的應用程式通訊協定。</span><span class="sxs-lookup"><span data-stu-id="0459a-121">However, most common implementations of systems that are based on REST utilize HTTP as the application protocol for sending and receiving requests.</span></span> <span data-ttu-id="0459a-122">本文件的重點，在於將 REST 原則對應到專門使用 HTTP 來運作的系統。</span><span class="sxs-lookup"><span data-stu-id="0459a-122">This document focuses on mapping REST principles to systems designed to operate using HTTP.</span></span>
>
>

<span data-ttu-id="0459a-123">REST 模型使用瀏覽配置來透過網路呈現物件和服務 (稱為「資源」)。</span><span class="sxs-lookup"><span data-stu-id="0459a-123">The REST model uses a navigational scheme to represent objects and services over a network (referred to as *resources*).</span></span> <span data-ttu-id="0459a-124">一般來說，有許多實作 REST 系統會使用 HTTP 通訊協定來傳送這些資源的存取要求。</span><span class="sxs-lookup"><span data-stu-id="0459a-124">Many systems that implement REST typically use the HTTP protocol to transmit requests to access these resources.</span></span> <span data-ttu-id="0459a-125">在這些系統中，用戶端應用程式會以識別資源之 URI 的形式提交要求，以及指出要在該資源上執行作業的 HTTP 方法 (最常見的方法包括 GET、POST、PUT 或 DELETE)。</span><span class="sxs-lookup"><span data-stu-id="0459a-125">In these systems, a client application submits a request in the form of a URI that identifies a resource, and an HTTP method (the most common being GET, POST, PUT, or DELETE) that indicates the operation to be performed on that resource.</span></span>  <span data-ttu-id="0459a-126">HTTP 要求的本文包含執行作業所需的資料。</span><span class="sxs-lookup"><span data-stu-id="0459a-126">The body of the HTTP request contains the data required to perform the operation.</span></span> <span data-ttu-id="0459a-127">您應了解的重點是 REST 能定義無狀態的要求模型。</span><span class="sxs-lookup"><span data-stu-id="0459a-127">The important point to understand is that REST defines a stateless request model.</span></span> <span data-ttu-id="0459a-128">HTTP 要求應該是獨立的，而且可能會以任何順序發生，因此嘗試保留多個要求之間的暫時性狀態資訊不是恰當的做法。</span><span class="sxs-lookup"><span data-stu-id="0459a-128">HTTP requests should be independent and may occur in any order, so attempting to retain transient state information between requests is not feasible.</span></span>  <span data-ttu-id="0459a-129">唯一可儲存資訊的場所是資源本身，而且每個要求都應該是不可部分完成的作業。</span><span class="sxs-lookup"><span data-stu-id="0459a-129">The only place where information is stored is in the resources themselves, and each request should be an atomic operation.</span></span> <span data-ttu-id="0459a-130">實際上，REST 模型會實作有限狀態機器，讓要求將資源從定義完善的非暫時性狀態轉換成其他狀態。</span><span class="sxs-lookup"><span data-stu-id="0459a-130">Effectively, a REST model implements a finite state machine where a request transitions a resource from one well-defined non-transient state to another.</span></span>

> [!NOTE]
> <span data-ttu-id="0459a-131">REST 模型中個別要求的無狀態性質，讓遵循這些原則建構的系統具備高度擴充性。</span><span class="sxs-lookup"><span data-stu-id="0459a-131">The stateless nature of individual requests in the REST model enables a system constructed by following these principles to be highly scalable.</span></span> <span data-ttu-id="0459a-132">針對提出一系列要求的用戶端應用程式和處理這些要求的特定 Web 伺服器，您不需要保留兩者之間的同質性。</span><span class="sxs-lookup"><span data-stu-id="0459a-132">There is no need to retain any affinity between a client application making a series of requests and the specific web servers handling those requests.</span></span>
>
>

<span data-ttu-id="0459a-133">實作有效 REST 模型的另一個重點，是了解模型提供存取能力之各種資源間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="0459a-133">Another crucial point in implementing an effective REST model is to understand the relationships between the various resources to which the model provides access.</span></span> <span data-ttu-id="0459a-134">這些資源通常會組織成集合和關聯性。</span><span class="sxs-lookup"><span data-stu-id="0459a-134">These resources are typically organized as collections and relationships.</span></span> <span data-ttu-id="0459a-135">例如，假設電子商務系統的快速分析顯示有兩個用戶端應用程式可能會感興趣的集合：訂單和客戶。</span><span class="sxs-lookup"><span data-stu-id="0459a-135">For example, suppose that a quick analysis of an ecommerce system shows that there are two collections in which client applications are likely to be interested: orders and customers.</span></span> <span data-ttu-id="0459a-136">每張訂單和每位客戶都應該擁有自己的唯一索引鍵，以供識別之用。</span><span class="sxs-lookup"><span data-stu-id="0459a-136">Each order and customer should have its own unique key for identification purposes.</span></span> <span data-ttu-id="0459a-137">存取訂單集合的 URI 可能就只是像 /orders 一樣簡單；同樣地，擷取所有客戶的 URI 可能是 /customers。</span><span class="sxs-lookup"><span data-stu-id="0459a-137">The URI to access the collection of orders could be something as simple as */orders*, and similarly the URI for retrieving all customers could be */customers*.</span></span> <span data-ttu-id="0459a-138">向 /orders URI 發出 HTTP GET 要求，應該會傳回以 HTTP 回應編碼且呈現集合中所有訂單的清單：</span><span class="sxs-lookup"><span data-stu-id="0459a-138">Issuing an HTTP GET request to the */orders* URI should return a list representing all orders in the collection encoded as an HTTP response:</span></span>

```HTTP
GET http://adventure-works.com/orders HTTP/1.1
...
```

<span data-ttu-id="0459a-139">以下所示的回應，會將訂單編碼成 JSON 清單結構。</span><span class="sxs-lookup"><span data-stu-id="0459a-139">The response shown below encodes the orders as a JSON list structure:</span></span>

```HTTP
HTTP/1.1 200 OK
...
Date: Fri, 22 Aug 2014 08:49:02 GMT
Content-Length: ...
[{"orderId":1,"orderValue":99.90,"productId":1,"quantity":1},{"orderId":2,"orderValue":10.00,"productId":4,"quantity":2},{"orderId":3,"orderValue":16.60,"productId":2,"quantity":4},{"orderId":4,"orderValue":25.90,"productId":3,"quantity":1},{"orderId":5,"orderValue":99.90,"productId":1,"quantity":1}]
```
<span data-ttu-id="0459a-140">若要擷取個別訂單，您需要指定 orders 資源中訂單的識別碼 (如 /orders/2)：</span><span class="sxs-lookup"><span data-stu-id="0459a-140">To fetch an individual order requires specifying the identifier for the order from the *orders* resource, such as */orders/2*:</span></span>

```HTTP
GET http://adventure-works.com/orders/2 HTTP/1.1
...
```

```HTTP
HTTP/1.1 200 OK
...
Date: Fri, 22 Aug 2014 08:49:02 GMT
Content-Length: ...
{"orderId":2,"orderValue":10.00,"productId":4,"quantity":2}
```

> [!NOTE]
> <span data-ttu-id="0459a-141">為了簡單起見，這些範例顯示回應中的資訊，是以 JSON 文字資料的形式傳回。</span><span class="sxs-lookup"><span data-stu-id="0459a-141">For simplicity, these examples show the information in responses being returned as JSON text data.</span></span> <span data-ttu-id="0459a-142">然而，限制資源不能包含其他 HTTP 支援的資料類型 (如二進位或加密資訊) 並不合理；HTTP 回應中的 content-type 應該要指定類型。</span><span class="sxs-lookup"><span data-stu-id="0459a-142">However, there is no reason why resources should not contain any other type of data supported by HTTP, such as binary or encrypted information; the content-type in the HTTP response should specify the type.</span></span> <span data-ttu-id="0459a-143">此外，REST 模型也可以傳回不同格式 (如 XML 或 JSON) 的相同資料。</span><span class="sxs-lookup"><span data-stu-id="0459a-143">Also, a REST model may be able to return the same data in different formats, such as XML or JSON.</span></span> <span data-ttu-id="0459a-144">在此情況下，Web 服務應該要能夠與提出要求的用戶端交涉內容。</span><span class="sxs-lookup"><span data-stu-id="0459a-144">In this case, the web service should be able to perform content negotiation with the client making the request.</span></span> <span data-ttu-id="0459a-145">此要求包含 Accept 標頭，其指定用戶端慣用的接收格式，而 Web 服務應盡可能嘗試遵守該格式。</span><span class="sxs-lookup"><span data-stu-id="0459a-145">The request can include an *Accept* header which specifies the preferred format that the client would like to receive and the web service should attempt to honor this format if at all possible.</span></span>
>
>

<span data-ttu-id="0459a-146">請注意，來自 REST 要求的回應會使用標準 HTTP 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="0459a-146">Notice that the response from a REST request makes use of the standard HTTP status codes.</span></span> <span data-ttu-id="0459a-147">例如，傳回有效資料的要求應包含 HTTP 回應碼 200 (良好)，而找不到或無法刪除指定資源的要求應傳回包含 HTTP 狀態碼 404 (找不到) 的回應。</span><span class="sxs-lookup"><span data-stu-id="0459a-147">For example, a request that returns valid data should include the HTTP response code 200 (OK), while a request that fails to find or delete a specified resource should return a response that includes the HTTP status code 404 (Not Found).</span></span>

## <a name="design-and-structure-of-a-restful-web-api"></a><span data-ttu-id="0459a-148">符合 REST 限制之 Web API 的設計和結構</span><span class="sxs-lookup"><span data-stu-id="0459a-148">Design and structure of a RESTful web API</span></span>
<span data-ttu-id="0459a-149">設計成功 Web API 的關鍵是簡單和一致性。</span><span class="sxs-lookup"><span data-stu-id="0459a-149">The keys to designing a successful web API are simplicity and consistency.</span></span> <span data-ttu-id="0459a-150">具備這兩項要素的 Web API 可讓您輕鬆地建置需要取用 API 的用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="0459a-150">A Web API that exhibits these two factors makes it easier to build client applications that need to consume the API.</span></span>

<span data-ttu-id="0459a-151">符合 REST 限制的 Web API 著重於公開一組連接的資源，並提供讓應用程式操作這些資源及輕易地在之間瀏覽的核心作業。</span><span class="sxs-lookup"><span data-stu-id="0459a-151">A RESTful web API is focused on exposing a set of connected resources, and providing the core operations that enable an application to manipulate these resources and easily navigate between them.</span></span> <span data-ttu-id="0459a-152">基於這個理由，構成典型符合 REST 限制之 Web API 的 URI 應該以 Web API 公開的資料為導向，並使用 HTTP 所提供的機制來操作這些資料。</span><span class="sxs-lookup"><span data-stu-id="0459a-152">For this reason, the URIs that constitute a typical RESTful web API should be oriented towards the data that it exposes, and use the facilities provided by HTTP to operate on this data.</span></span> <span data-ttu-id="0459a-153">這個方法需要的心態與設計一組物件導向 API 之類別時普遍抱持的心態不同，因為後者較傾向受物件和類別的行為所驅策。</span><span class="sxs-lookup"><span data-stu-id="0459a-153">This approach requires a different mindset from that typically employed when designing a set of classes in an object-oriented API which tends to be more motivated by the behavior of objects and classes.</span></span> <span data-ttu-id="0459a-154">此外，符合 REST 限制的 Web API 應該沒有狀態，因此不需要仰賴特定序列叫用的作業。</span><span class="sxs-lookup"><span data-stu-id="0459a-154">Additionally, a RESTful web API should be stateless and not depend on operations being invoked in a particular sequence.</span></span> <span data-ttu-id="0459a-155">以下各節摘要說明在設計符合 REST 限制之 Web API 時應考量的重點。</span><span class="sxs-lookup"><span data-stu-id="0459a-155">The following sections summarize the points you should consider when designing a RESTful web API.</span></span>

### <a name="organizing-the-web-api-around-resources"></a><span data-ttu-id="0459a-156">將 Web API 排列在資源周圍</span><span class="sxs-lookup"><span data-stu-id="0459a-156">Organizing the web API around resources</span></span>
> [!TIP]
> <span data-ttu-id="0459a-157">REST Web 服務所公開的 URI 應基於名詞 (Web API 提供存取能力的資料)，而不是動詞 (應用程式可以如何使用資料)。</span><span class="sxs-lookup"><span data-stu-id="0459a-157">The URIs exposed by a REST web service should be based on nouns (the data to which the web API provides access) and not verbs (what an application can do with the data).</span></span>
>
>

<span data-ttu-id="0459a-158">將焦點放在 Web API 公開商業實體。</span><span class="sxs-lookup"><span data-stu-id="0459a-158">Focus on the business entities that the web API exposes.</span></span> <span data-ttu-id="0459a-159">例如，對於前述專為支援電子商務系統而設計的 Web API，主要實體是客戶和訂單。</span><span class="sxs-lookup"><span data-stu-id="0459a-159">For example, in a web API designed to support the ecommerce system described earlier, the primary entities are customers and orders.</span></span> <span data-ttu-id="0459a-160">諸如下訂單這類的程序，您可以提供能提取訂單資訊，並將其加入客戶之訂單清單的 HTTP POST 作業來達成。</span><span class="sxs-lookup"><span data-stu-id="0459a-160">Processes such as the act of placing an order can be achieved by providing an HTTP POST operation that takes the order information and adds it to the list of orders for the customer.</span></span> <span data-ttu-id="0459a-161">就內部而言，這項 POST 作業可以執行如檢查存貨量及向客戶收費等工作。</span><span class="sxs-lookup"><span data-stu-id="0459a-161">Internally, this POST operation can perform tasks such as checking stock levels, and billing the customer.</span></span> <span data-ttu-id="0459a-162">HTTP 回應可以指出訂購成功與否。</span><span class="sxs-lookup"><span data-stu-id="0459a-162">The HTTP response can indicate whether the order was placed successfully or not.</span></span> <span data-ttu-id="0459a-163">另請注意，資源不一定要依據一個實體資料項目。</span><span class="sxs-lookup"><span data-stu-id="0459a-163">Also note that a resource does not have to be based on a single physical data item.</span></span> <span data-ttu-id="0459a-164">舉例來說，藉由使用自分散在關聯式資料庫多個資料表中許多資料列彙總而來的資訊，您可以將訂單資源實作在內部，但以單一實體的形式呈現給用戶端。</span><span class="sxs-lookup"><span data-stu-id="0459a-164">As an example, an order resource might be implemented internally by using information aggregated from many rows spread across several tables in a relational database but presented to the client as a single entity.</span></span>

> [!TIP]
> <span data-ttu-id="0459a-165">請避免設計會鏡像處理或需仰賴自己公開之資料內部結構的 REST 介面。</span><span class="sxs-lookup"><span data-stu-id="0459a-165">Avoid designing a REST interface that mirrors or depends on the internal structure of the data that it exposes.</span></span> <span data-ttu-id="0459a-166">REST 不僅僅是針對關聯式資料庫中個別資料表執行簡單的 CRUD (建立、擷取、更新、刪除) 作業。</span><span class="sxs-lookup"><span data-stu-id="0459a-166">REST is about more than implementing simple CRUD (Create, Retrieve, Update, Delete) operations over separate tables in a relational database.</span></span> <span data-ttu-id="0459a-167">REST 的目的是將商業實體和應用程式可針對商業實體執行的作業對應到這些實體的實際實作，但避免向用戶端公開這些實際詳細資料。</span><span class="sxs-lookup"><span data-stu-id="0459a-167">The purpose of REST is to map business entities and the operations that an application can perform on these entities to the physical implementation of these entities, but a client should not be exposed to these physical details.</span></span>
>
>

<span data-ttu-id="0459a-168">個別的商業實體很少以隔離狀態存在 (雖然可能有某些單一物件存在)，反之，它們傾向於集結成集合。</span><span class="sxs-lookup"><span data-stu-id="0459a-168">Individual business entities rarely exist in isolation (although some singleton objects may exist), but instead tend to be grouped together into collections.</span></span> <span data-ttu-id="0459a-169">就 REST來說，每個實體和每個集合都是資源。</span><span class="sxs-lookup"><span data-stu-id="0459a-169">In REST terms, each entity and each collection are resources.</span></span> <span data-ttu-id="0459a-170">在符合 REST 限制的 Web API 中，每個集合在 Web 服務中都有自己的 URI，而透過 URI 針對集合執行 HTTP GET 能擷取該集合中的項目清單。</span><span class="sxs-lookup"><span data-stu-id="0459a-170">In a RESTful web API, each collection has its own URI within the web service, and performing an HTTP GET request over a URI for a collection retrieves a list of items in that collection.</span></span> <span data-ttu-id="0459a-171">每個個別的項目也有自己的 URI，應用程式可以使用該 URI 來提交另一個 HTTP GET 要求，以便擷取該項目的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="0459a-171">Each individual item also has its own URI, and an application can submit another HTTP GET request using that URI to retrieve the details of that item.</span></span> <span data-ttu-id="0459a-172">您應該以階層方式組織集合和項目的 URI。</span><span class="sxs-lookup"><span data-stu-id="0459a-172">You should organize the URIs for collections and items in a hierarchical manner.</span></span> <span data-ttu-id="0459a-173">在電子商務系統中，URI /customers 代表客戶的集合，而 /customers/5 能擷取該集合中的識別碼為 5 之單一客戶的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="0459a-173">In the ecommerce system, the URI */customers* denotes the customer’s collection, and */customers/5* retrieves the details for the single customer with the ID 5 from this collection.</span></span> <span data-ttu-id="0459a-174">這個方法有助於維持 Web API 的直覺性。</span><span class="sxs-lookup"><span data-stu-id="0459a-174">This approach helps to keep the web API intuitive.</span></span>

> [!TIP]
> <span data-ttu-id="0459a-175">在 URI 中採用一致的命名慣例；一般而言，在參考集合的 URI 中使用複數名詞比較有效益。</span><span class="sxs-lookup"><span data-stu-id="0459a-175">Adopt a consistent naming convention in URIs; in general it helps to use plural nouns for URIs that reference collections.</span></span>
>
>

<span data-ttu-id="0459a-176">您也需要將不同類型之資源間的關聯性和公開這些關聯的方式納入考量。</span><span class="sxs-lookup"><span data-stu-id="0459a-176">You also need to consider the relationships between different types of resources and how you might expose these associations.</span></span> <span data-ttu-id="0459a-177">例如，客戶可能不會下訂單或下了多張訂單。</span><span class="sxs-lookup"><span data-stu-id="0459a-177">For example, customers may place zero or more orders.</span></span> <span data-ttu-id="0459a-178">若要以自然的方式來呈現此關聯性，便是透過如 /customers/5/orders 之類的 URI 來尋找客戶 5 的所有訂單。</span><span class="sxs-lookup"><span data-stu-id="0459a-178">A natural way to represent this relationship would be through a URI such as */customers/5/orders* to find all the orders for customer 5.</span></span> <span data-ttu-id="0459a-179">您也可以考慮透過 /orders/99/customer 之類的 URI 來尋找訂單 99 的客戶，呈現從訂單追溯回特定客戶的關聯，不過過度擴充該模型可能因為太過繁瑣而難以實作。</span><span class="sxs-lookup"><span data-stu-id="0459a-179">You might also consider representing the association from an order back to a specific customer through a URI such as */orders/99/customer* to find the customer for order 99, but extending this model too far can become cumbersome to implement.</span></span> <span data-ttu-id="0459a-180">比較好的解決方案，是在查詢訂單時於傳回的 HTTP 回應訊息本文中提供相關資源 (如客戶) 的可瀏覽連結。</span><span class="sxs-lookup"><span data-stu-id="0459a-180">A better solution is to provide navigable links to associated resources, such as the customer, in the body of the HTTP response message returned when the order is queried.</span></span> <span data-ttu-id="0459a-181">本指引稍後的「使用 HATEOAS 方法來啟用相關資源導覽」一節將有這項機制的詳細描述。</span><span class="sxs-lookup"><span data-stu-id="0459a-181">This mechanism is described in more detail in the section Using the HATEOAS Approach to Enable Navigation To Related Resources later in this guidance.</span></span>

<span data-ttu-id="0459a-182">在較複雜的系統中可能有許多不同類型的實體，而提供讓用戶端應用程式瀏覽多層級關聯性的 URI 將會是很誘人的做法。例如，瀏覽 /customers/1/orders/99/products 可取得客戶 1 之訂單 99 中的產品清單。</span><span class="sxs-lookup"><span data-stu-id="0459a-182">In more complex systems there may be many more types of entity, and it can be tempting to provide URIs that enable a client application to navigate through several levels of relationships, such as */customers/1/orders/99/products* to obtain the list of products in order 99 placed by customer 1.</span></span> <span data-ttu-id="0459a-183">不過，如果資源之間的關聯性在未來發生變更，這種程度的複雜度不僅難以維持，也缺乏彈性。</span><span class="sxs-lookup"><span data-stu-id="0459a-183">However, this level of complexity can be difficult to maintain and is inflexible if the relationships between resources change in the future.</span></span> <span data-ttu-id="0459a-184">相反地，您應該尋求將 URL 保持在相對簡單的情況下。</span><span class="sxs-lookup"><span data-stu-id="0459a-184">Rather, you should seek to keep URIs relatively simple.</span></span> <span data-ttu-id="0459a-185">請記住，一旦應用程式擁有資源的參考，它就應該可以使用這個參考來尋找與該資源相關的項目。</span><span class="sxs-lookup"><span data-stu-id="0459a-185">Bear in mind that once an application has a reference to a resource, it should be possible to use this reference to find items related to that resource.</span></span> <span data-ttu-id="0459a-186">您可以將前述查詢取代為 URI /customers/1/orders 來尋找客戶 1 的所有訂單，然後查詢 URI /orders/99/products 來尋找此訂單中的產品 (假設訂單 99 是由客戶 1 下的)。</span><span class="sxs-lookup"><span data-stu-id="0459a-186">The preceding query can be replaced with the URI */customers/1/orders* to find all the orders for customer 1, and then query the URI */orders/99/products* to find the products in this order (assuming order 99 was placed by customer 1).</span></span>

> [!TIP]
> <span data-ttu-id="0459a-187">請避免要求比 collection/item/collection 更複雜的資源 URI。</span><span class="sxs-lookup"><span data-stu-id="0459a-187">Avoid requiring resource URIs more complex than *collection/item/collection*.</span></span>
>
>

<span data-ttu-id="0459a-188">另一個要考量的重點是所有 Web 要求都會造成 Web 伺服器的負載，而要求數目越大，負載也就越沈重。</span><span class="sxs-lookup"><span data-stu-id="0459a-188">Another point to consider is that all web requests impose a load on the web server, and the greater the number of requests the bigger the load.</span></span> <span data-ttu-id="0459a-189">您應該嘗試定義資源，以避免「多話」的 Web API 公開大量小型資源。</span><span class="sxs-lookup"><span data-stu-id="0459a-189">You should attempt to define your resources to avoid “chatty” web APIs that expose a large number of small resources.</span></span> <span data-ttu-id="0459a-190">這類 API 可能會要求用戶端應用程式提交多個要求來尋找所需的所有資料。</span><span class="sxs-lookup"><span data-stu-id="0459a-190">Such an API may require a client application to submit multiple requests to find all the data that it requires.</span></span> <span data-ttu-id="0459a-191">將資料反正規化並把相關資訊結合在一起，變成發出單一要求即可予以擷取的大型資源將能提高效益。</span><span class="sxs-lookup"><span data-stu-id="0459a-191">It may be beneficial to denormalize data and combine related information together into bigger resources that can be retrieved by issuing a single request.</span></span> <span data-ttu-id="0459a-192">不過，您需要平衡這個方法在擷取用戶端非經常性需要之資料時的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="0459a-192">However, you need to balance this approach against the overhead of fetching data that might not be frequently required by the client.</span></span> <span data-ttu-id="0459a-193">擷取大型物件可能會增加要求的延遲時間，而且如果額外資料並非經常使用的資料，引發的額外頻寬成本並不會產生太高的效益。</span><span class="sxs-lookup"><span data-stu-id="0459a-193">Retrieving large objects can increase the latency of a request and incur additional bandwidth costs for little advantage if the additional data is not often used.</span></span>

<span data-ttu-id="0459a-194">請避免在 Web API 與基礎資料來源之結構、類型或位置之間導入相依性。</span><span class="sxs-lookup"><span data-stu-id="0459a-194">Avoid introducing dependencies between the web API to the structure, type, or location of the underlying data sources.</span></span> <span data-ttu-id="0459a-195">比方說，如果資料位於關聯式資料庫中，Web API 就不需要將每個資料表公開為資源集合。</span><span class="sxs-lookup"><span data-stu-id="0459a-195">For example, if your data is located in a relational database, the web API does not need to expose each table as a collection of resources.</span></span> <span data-ttu-id="0459a-196">您可以將 Web API 視為資料庫的抽象，如有必要，可以在資料庫和 Web API 之間導入對應層。</span><span class="sxs-lookup"><span data-stu-id="0459a-196">Think of the web API as an abstraction of the database, and if necessary introduce a mapping layer between the database and the web API.</span></span> <span data-ttu-id="0459a-197">如此一來，當資料庫的設計或實作改變時 (例如，從含有正規化資料表之集合的關聯式資料庫轉變成文件資料庫等反正規化 NoSQL 儲存系統)，用戶端應用程式將能隔絕這些變更。</span><span class="sxs-lookup"><span data-stu-id="0459a-197">In this way, if the design or implementation of the database changes (for example, you move from a relational database containing a collection of normalized tables to a denormalized NoSQL storage system such as a document database) client applications are insulated from these changes.</span></span>

> [!TIP]
> <span data-ttu-id="0459a-198">支持 Web API 的資料來源不一定非得是資料存放區，它可以是另一個服務或企業營運系統應用程式，或甚至是在組織內部執行的內部部署舊版應用程式。</span><span class="sxs-lookup"><span data-stu-id="0459a-198">The source of the data that underpins a web API does not have to be a data store; it could be another service or line-of-business application or even a legacy application running on-premises within an organization.</span></span>
>
>

<span data-ttu-id="0459a-199">最後，您不一定能將 Web API 實作的每個作業對應到特定資源。</span><span class="sxs-lookup"><span data-stu-id="0459a-199">Finally, it might not be possible to map every operation implemented by a web API to a specific resource.</span></span> <span data-ttu-id="0459a-200">您可以透過能叫用某項功能，並以 HTTP 回應訊息形式傳回結果的 HTTP GET 要求來處理這類「非資源」案例。</span><span class="sxs-lookup"><span data-stu-id="0459a-200">You can handle such *non-resource* scenarios through HTTP GET requests that invoke a piece of functionality and return the results as an HTTP response message.</span></span> <span data-ttu-id="0459a-201">實作簡單計算機樣式作業 (如加法和減法) 的 Web API 可以提供將這些作業公開為虛擬資源，並利用查詢字串來指定所需參數的 URI。</span><span class="sxs-lookup"><span data-stu-id="0459a-201">A web API that implements simple calculator-style operations such as add and subtract could provide URIs that expose these operations as pseudo resources and utilize the query string to specify the parameters required.</span></span> <span data-ttu-id="0459a-202">例如，URI /add?operand1=99&operand2=1 的 GET 要求可以傳回本文包含值 100 的回應訊息，而 URI /subtract?operand1=50&operand2=20 的 GET 要求則可以傳回本文包含值 30 的回應訊息。</span><span class="sxs-lookup"><span data-stu-id="0459a-202">For example a GET request to the URI */add?operand1=99&operand2=1* could return a response message with the body containing the value 100, and GET request to the URI */subtract?operand1=50&operand2=20* could return a response message with the body containing the value 30.</span></span> <span data-ttu-id="0459a-203">儘管如此，請謹慎使用這些形式的 URI。</span><span class="sxs-lookup"><span data-stu-id="0459a-203">However, only use these forms of URIs sparingly.</span></span>

### <a name="defining-operations-in-terms-of-http-methods"></a><span data-ttu-id="0459a-204">以 HTTP 方法定義作業</span><span class="sxs-lookup"><span data-stu-id="0459a-204">Defining operations in terms of HTTP methods</span></span>
<span data-ttu-id="0459a-205">HTTP 通訊協定能定義數個將語意意義指派給要求的方法。</span><span class="sxs-lookup"><span data-stu-id="0459a-205">The HTTP protocol defines a number of methods that assign semantic meaning to a request.</span></span> <span data-ttu-id="0459a-206">大多數符合 REST 限制之 Web API 使用的常見 HTTP 方法包括︰</span><span class="sxs-lookup"><span data-stu-id="0459a-206">The common HTTP methods used by most RESTful web APIs are:</span></span>

* <span data-ttu-id="0459a-207">**GET**：在指定的 URI 擷取一份資源。</span><span class="sxs-lookup"><span data-stu-id="0459a-207">**GET**, to retrieve a copy of the resource at the specified URI.</span></span> <span data-ttu-id="0459a-208">回應訊息的本文包含要求之資源的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="0459a-208">The body of the response message contains the details of the requested resource.</span></span>
* <span data-ttu-id="0459a-209">**POST**：在指定的 URI 建立新資源。</span><span class="sxs-lookup"><span data-stu-id="0459a-209">**POST**, to create a new resource at the specified URI.</span></span> <span data-ttu-id="0459a-210">要求訊息的本文提供新資源的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="0459a-210">The body of the request message provides the details of the new resource.</span></span> <span data-ttu-id="0459a-211">請注意，POST 也能用來觸發實際上不會建立資源的作業。</span><span class="sxs-lookup"><span data-stu-id="0459a-211">Note that POST can also be used to trigger operations that don't actually create resources.</span></span>
* <span data-ttu-id="0459a-212">**PUT**：取代或更新指定之 URI 的資源。</span><span class="sxs-lookup"><span data-stu-id="0459a-212">**PUT**, to replace or update the resource at the specified URI.</span></span> <span data-ttu-id="0459a-213">要求訊息的本文能會指定要修改的資源及要套用的值。</span><span class="sxs-lookup"><span data-stu-id="0459a-213">The body of the request message specifies the resource to be modified and the values to be applied.</span></span>
* <span data-ttu-id="0459a-214">**DELETE**：移除指定之 URI 的資源。</span><span class="sxs-lookup"><span data-stu-id="0459a-214">**DELETE**, to remove the resource at the specified URI.</span></span>

> [!NOTE]
> <span data-ttu-id="0459a-215">HTTP 通訊協定也能定義其他較不常用的方法。例如用來向資源要求選擇性更新的 PATCH、用來要求資源描述的 HEAD、讓用戶端資訊取得伺服器支援之通訊選項相關資訊的 OPTIONS，以及讓用戶端要求可供測試和診斷之用之資訊的 TRACE。</span><span class="sxs-lookup"><span data-stu-id="0459a-215">The HTTP protocol also defines other less commonly-used methods, such as PATCH which is used to request selective updates to a resource, HEAD which is used to request a description of a resource, OPTIONS which enables a client information to obtain information about the communication options supported by the server, and TRACE which allows a client to request information that it can use for testing and diagnostics purposes.</span></span>
>
>

<span data-ttu-id="0459a-216">特定要求的效果應取決於要套用的資源是集合或個別項目。</span><span class="sxs-lookup"><span data-stu-id="0459a-216">The effect of a specific request should depend on whether the resource to which it is applied is a collection or an individual item.</span></span> <span data-ttu-id="0459a-217">下表摘要說明的常見慣例是大部分使用電子商務範例之符合 REST 限制實作所採用的慣例。</span><span class="sxs-lookup"><span data-stu-id="0459a-217">The following table summarizes the common conventions adopted by most RESTful implementations using the ecommerce example.</span></span> <span data-ttu-id="0459a-218">請注意，這些要求中並非所有要求都可以實作，須視特定案例的情況而定。</span><span class="sxs-lookup"><span data-stu-id="0459a-218">Note that not all of these requests might be implemented; it depends on the specific scenario.</span></span>

| <span data-ttu-id="0459a-219">**Resource**</span><span class="sxs-lookup"><span data-stu-id="0459a-219">**Resource**</span></span> | <span data-ttu-id="0459a-220">**POST**</span><span class="sxs-lookup"><span data-stu-id="0459a-220">**POST**</span></span> | <span data-ttu-id="0459a-221">**GET**</span><span class="sxs-lookup"><span data-stu-id="0459a-221">**GET**</span></span> | <span data-ttu-id="0459a-222">**PUT**</span><span class="sxs-lookup"><span data-stu-id="0459a-222">**PUT**</span></span> | <span data-ttu-id="0459a-223">**DELETE**</span><span class="sxs-lookup"><span data-stu-id="0459a-223">**DELETE**</span></span> |
| --- | --- | --- | --- | --- |
| <span data-ttu-id="0459a-224">/customers</span><span class="sxs-lookup"><span data-stu-id="0459a-224">/customers</span></span> |<span data-ttu-id="0459a-225">建立新客戶</span><span class="sxs-lookup"><span data-stu-id="0459a-225">Create a new customer</span></span> |<span data-ttu-id="0459a-226">擷取所有客戶</span><span class="sxs-lookup"><span data-stu-id="0459a-226">Retrieve all customers</span></span> |<span data-ttu-id="0459a-227">大量更新客戶 (若實作的話)</span><span class="sxs-lookup"><span data-stu-id="0459a-227">Bulk update of customers (*if implemented*)</span></span> |<span data-ttu-id="0459a-228">移除所有客戶</span><span class="sxs-lookup"><span data-stu-id="0459a-228">Remove all customers</span></span> |
| <span data-ttu-id="0459a-229">/customers/1</span><span class="sxs-lookup"><span data-stu-id="0459a-229">/customers/1</span></span> |<span data-ttu-id="0459a-230">錯誤</span><span class="sxs-lookup"><span data-stu-id="0459a-230">Error</span></span> |<span data-ttu-id="0459a-231">擷取客戶 1 的詳細資料</span><span class="sxs-lookup"><span data-stu-id="0459a-231">Retrieve the details for customer 1</span></span> |<span data-ttu-id="0459a-232">更新客戶 1 的詳細資料 (若有的話)，否則傳回錯誤</span><span class="sxs-lookup"><span data-stu-id="0459a-232">Update the details of customer 1 if it exists, otherwise return an error</span></span> |<span data-ttu-id="0459a-233">移除客戶 1</span><span class="sxs-lookup"><span data-stu-id="0459a-233">Remove customer 1</span></span> |
| <span data-ttu-id="0459a-234">/customers/1/orders</span><span class="sxs-lookup"><span data-stu-id="0459a-234">/customers/1/orders</span></span> |<span data-ttu-id="0459a-235">為客戶 1 建立新訂單</span><span class="sxs-lookup"><span data-stu-id="0459a-235">Create a new order for customer 1</span></span> |<span data-ttu-id="0459a-236">擷取客戶 1 的所有訂單</span><span class="sxs-lookup"><span data-stu-id="0459a-236">Retrieve all orders for customer 1</span></span> |<span data-ttu-id="0459a-237">大量更新客戶 1 的訂單 (若實作的話)</span><span class="sxs-lookup"><span data-stu-id="0459a-237">Bulk update of orders for customer 1 (*if implemented*)</span></span> |<span data-ttu-id="0459a-238">移除客戶 1 的所有訂單 (若實作的話)</span><span class="sxs-lookup"><span data-stu-id="0459a-238">Remove all orders for customer 1(*if implemented*)</span></span> |

<span data-ttu-id="0459a-239">GET 和 DELETE 要求的用途相對地較清楚明瞭，但是 POST 和 PUT 要求用途和效果則有造成混淆的範圍。</span><span class="sxs-lookup"><span data-stu-id="0459a-239">The purpose of GET and DELETE requests are relatively straightforward, but there is scope for confusion concerning the purpose and effects of POST and PUT requests.</span></span>

<span data-ttu-id="0459a-240">POST 要求應利用要求本文提供的資料建立新資源。</span><span class="sxs-lookup"><span data-stu-id="0459a-240">A POST request should create a new resource with data provided in the body of the request.</span></span> <span data-ttu-id="0459a-241">在 REST 模型中，您經常會將 POST 要求套用至集合形式的資源；新資源會加入集合中。</span><span class="sxs-lookup"><span data-stu-id="0459a-241">In the REST model, you frequently apply POST requests to resources that are collections; the new resource is added to the collection.</span></span>

> [!NOTE]
> <span data-ttu-id="0459a-242">您也可以定義觸發某些功能 (不一定是傳回資料) 的 POST 要求，而這些類型的要求可以套用至集合。</span><span class="sxs-lookup"><span data-stu-id="0459a-242">You can also define POST requests that trigger some functionality (and that don't necessarily return data), and these types of request can be applied to collections.</span></span> <span data-ttu-id="0459a-243">比方說，您可以使用 POST 要求將時程表傳遞給薪資處理服務，然後將計算出來稅金當做回應。</span><span class="sxs-lookup"><span data-stu-id="0459a-243">For example you could use a POST request to pass a timesheet to a payroll processing service and get the calculated taxes back as a response.</span></span>
>
>

<span data-ttu-id="0459a-244">PUT 要求的目的在於修改現有資源。</span><span class="sxs-lookup"><span data-stu-id="0459a-244">A PUT request is intended to modify an existing resource.</span></span> <span data-ttu-id="0459a-245">如果指定的資源不存在，PUT 要求可能會傳回錯誤 (在某些情況下，它可能會實際建立資源)。</span><span class="sxs-lookup"><span data-stu-id="0459a-245">If the specified resource does not exist, the PUT request could return an error (in some cases, it might actually create the resource).</span></span> <span data-ttu-id="0459a-246">PUT 要求最常套用至個別項目形式的資源 (如特定客戶或訂單)，雖然您可以將它們套用至集合，不過這是比較不常實作的案例。</span><span class="sxs-lookup"><span data-stu-id="0459a-246">PUT requests are most frequently applied to resources that are individual items (such as a specific customer or order), although they can be applied to collections, although this is less-commonly implemented.</span></span> <span data-ttu-id="0459a-247">請注意，PUT 要求具有等冪性，POST 要求則否。如果應用程式提交相同的 PUT 要求多次，結果應該會保持不變 (使用相同的值修改相同的資源)，但如果應用程式重複相同的 POST 要求，結果會是建立多個資源。</span><span class="sxs-lookup"><span data-stu-id="0459a-247">Note that PUT requests are idempotent whereas POST requests are not; if an application submits the same PUT request multiple times the results should always be the same (the same resource will be modified with the same values), but if an application repeats the same POST request the result will be the creation of multiple resources.</span></span>

> [!NOTE]
> <span data-ttu-id="0459a-248">嚴格來說，HTTP PUT 要求會以在要求本文中指定的資源取代現有資源。</span><span class="sxs-lookup"><span data-stu-id="0459a-248">Strictly speaking, an HTTP PUT request replaces an existing resource with the resource specified in the body of the request.</span></span> <span data-ttu-id="0459a-249">如果您的用意是要修改資源中選取範圍內的屬性，但保留其他屬性不變，這時應使用 HTTP PATCH 要求來實作。</span><span class="sxs-lookup"><span data-stu-id="0459a-249">If the intention is to modify a selection of properties in a resource but leave other properties unchanged, then this should be implemented by using an HTTP PATCH request.</span></span> <span data-ttu-id="0459a-250">不過，許多符合 REST 限制的實作放寬這項規則，均使用 PUT 來滿足這兩種情況的需求。</span><span class="sxs-lookup"><span data-stu-id="0459a-250">However, many RESTful implementations relax this rule and use PUT for both situations.</span></span>
>
>

### <a name="processing-http-requests"></a><span data-ttu-id="0459a-251">處理 HTTP 要求</span><span class="sxs-lookup"><span data-stu-id="0459a-251">Processing HTTP requests</span></span>
<span data-ttu-id="0459a-252">用戶端應用程式加入許多 HTTP 要求中的資料，以及來自 Web 伺服器的對應回應訊息，都能以各種不同的格式 (或媒體類型) 來呈現。</span><span class="sxs-lookup"><span data-stu-id="0459a-252">The data included by a client application in many HTTP requests, and the corresponding response messages from the web server, could be presented in a variety of formats (or media types).</span></span> <span data-ttu-id="0459a-253">例如，指定客戶或訂單詳細資料的資料可能是 XML、JSON 或其他編碼及壓縮格式。</span><span class="sxs-lookup"><span data-stu-id="0459a-253">For example, the data that specifies the details for a customer or order could be provided as XML, JSON, or some other encoded and compressed format.</span></span> <span data-ttu-id="0459a-254">符合 REST 限制的 Web API 應支援提交要求之用戶端應用程式所要求的各種媒體類型。</span><span class="sxs-lookup"><span data-stu-id="0459a-254">A RESTful web API should support different media types as requested by the client application that submits a request.</span></span>

<span data-ttu-id="0459a-255">當用戶端應用程式傳送會在訊息本文中傳回資料的要求時，它能在要求的 Accept 標頭中指定可以處理的媒體類型。</span><span class="sxs-lookup"><span data-stu-id="0459a-255">When a client application sends a request that returns data in the body of a message, it can specify the media types it can handle in the Accept header of the request.</span></span> <span data-ttu-id="0459a-256">下列程式碼說明可擷取訂單 2 的詳細資料，並要求以 JSON 傳回結果的 HTTP GET 要求 (用戶端仍應該檢查回應中的資料媒體類型，以確認傳回的資料格式)：</span><span class="sxs-lookup"><span data-stu-id="0459a-256">The following code illustrates an HTTP GET request that retrieves the details of order 2 and requests the result to be returned as JSON (the client should still examine the media type of the data in the response to verify the format of the data returned):</span></span>

```HTTP
GET http://adventure-works.com/orders/2 HTTP/1.1
...
Accept: application/json
...
```

<span data-ttu-id="0459a-257">如果 Web 伺服器支援此媒體類型，它可以回覆含 Content-Type 標頭的回應，並在其中指定訊息本文中的資料格式：</span><span class="sxs-lookup"><span data-stu-id="0459a-257">If the web server supports this media type, it can reply with a response that includes Content-Type header that specifies the format of the data in the body of the message:</span></span>

> [!NOTE]
> <span data-ttu-id="0459a-258">為了取得最大互通性，Accept 和 Content-Type 標頭中所參考媒體類型應辨識為 MIME 類型，而非某些自訂媒體類型。</span><span class="sxs-lookup"><span data-stu-id="0459a-258">For maximum interoperability, the media types referenced in the Accept and Content-Type headers should be recognized MIME types rather than some custom media type.</span></span>
>
>

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
{"orderID":2,"productID":4,"quantity":2,"orderValue":10.00}
```

<span data-ttu-id="0459a-259">如果 Web 伺服器不支援要求的媒體類型，它可以利用不同的格式來傳送資料。</span><span class="sxs-lookup"><span data-stu-id="0459a-259">If the web server does not support the requested media type, it can send the data in a different format.</span></span> <span data-ttu-id="0459a-260">在所有情況下，它必須在 Content-Type 標頭中指定媒體類型 (例如 application/json)。</span><span class="sxs-lookup"><span data-stu-id="0459a-260">IN all cases it must specify the media type (such as *application/json*) in the Content-Type header.</span></span> <span data-ttu-id="0459a-261">用戶端應用程式必須負責剖析回應訊息，並適當地解譯訊息本文中的結果。</span><span class="sxs-lookup"><span data-stu-id="0459a-261">It is the responsibility of the client application to parse the response message and interpret the results in the message body appropriately.</span></span>

<span data-ttu-id="0459a-262">請注意，在此範例中 Web 伺服器成功擷取要求的資料，並藉由在回應標頭中傳回狀態碼 200 來表示成功。</span><span class="sxs-lookup"><span data-stu-id="0459a-262">Note that in this example, the web server successfully retrieves the requested data and indicates success by passing back a status code of 200 in the response header.</span></span> <span data-ttu-id="0459a-263">如果找不到相符的資料，它應改為傳回狀態碼 404 (找不到)，而回應訊息的本文可以包含其他資訊。</span><span class="sxs-lookup"><span data-stu-id="0459a-263">If no matching data is found, it should instead return a status code of 404 (not found) and the body of the response message can contain additional information.</span></span> <span data-ttu-id="0459a-264">這些資訊的格式是由 Content-Type 標頭指定，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="0459a-264">The format of this information is specified by the Content-Type header, as shown in the following example:</span></span>

```HTTP
GET http://adventure-works.com/orders/222 HTTP/1.1
...
Accept: application/json
...
```

<span data-ttu-id="0459a-265">訂單 222 不存在，所以回應訊息看起來像：</span><span class="sxs-lookup"><span data-stu-id="0459a-265">Order 222 does not exist, so the response message looks like this:</span></span>

```HTTP
HTTP/1.1 404 Not Found
...
Content-Type: application/json; charset=utf-8
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
{"message":"No such order"}
```

<span data-ttu-id="0459a-266">當應用程式傳送 HTTP PUT 要求來更新資源時，它會指定資源的 URI，並在要求訊息的本文中提供要修改的資料。</span><span class="sxs-lookup"><span data-stu-id="0459a-266">When an application sends an HTTP PUT request to update a resource, it specifies the URI of the resource and provides the data to be modified in the body of the request message.</span></span> <span data-ttu-id="0459a-267">它也可以使用 Content-Type 標頭來指定這些資料的格式。</span><span class="sxs-lookup"><span data-stu-id="0459a-267">It should also specify the format of this data by using the Content-Type header.</span></span> <span data-ttu-id="0459a-268">以文字為基礎的資訊常會使用 application/x-www-form-urlencoded 格式，其中包括一組以 & 字元分隔的名稱/值對。</span><span class="sxs-lookup"><span data-stu-id="0459a-268">A common format used for text-based information is *application/x-www-form-urlencoded*, which comprises a set of name/value pairs separated by the & character.</span></span> <span data-ttu-id="0459a-269">下一個範例顯示修改訂單 1 之資訊的 HTTP PUT 要求：</span><span class="sxs-lookup"><span data-stu-id="0459a-269">The next example shows an HTTP PUT request that modifies the information in order 1:</span></span>

```HTTP
PUT http://adventure-works.com/orders/1 HTTP/1.1
...
Content-Type: application/x-www-form-urlencoded
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
ProductID=3&Quantity=5&OrderValue=250
```

<span data-ttu-id="0459a-270">如果修改成功，在理想的情況下它會回應 HTTP 204 狀態碼，表示已成功處理程序，但回應本文不會包含進一步的資訊。</span><span class="sxs-lookup"><span data-stu-id="0459a-270">If the modification is successful, it should ideally respond with an HTTP 204 status code, indicating that the process has been successfully handled, but that the response body contains no further information.</span></span> <span data-ttu-id="0459a-271">回應中的 Location 標頭包含新更新之資源的 URI：</span><span class="sxs-lookup"><span data-stu-id="0459a-271">The Location header in the response contains the URI of the newly updated resource:</span></span>

```HTTP
HTTP/1.1 204 No Content
...
Location: http://adventure-works.com/orders/1
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
```

> [!TIP]
> <span data-ttu-id="0459a-272">如果 HTTP PUT 要求訊息中的資料包含日期和時間資訊，請確認 Web 服務接受遵循 ISO 8601 標準格式化的日期和時間。</span><span class="sxs-lookup"><span data-stu-id="0459a-272">If the data in an HTTP PUT request message includes date and time information, make sure that your web service accepts dates and times formatted following the ISO 8601 standard.</span></span>
>
>

<span data-ttu-id="0459a-273">如果要更新的資源不存在，Web 伺服器可以回覆「找不到」回應，如先前所述。</span><span class="sxs-lookup"><span data-stu-id="0459a-273">If the resource to be updated does not exist, the web server can respond with a Not Found response as described earlier.</span></span> <span data-ttu-id="0459a-274">或者，如果伺服器確實自行建立物件，它會傳回狀態碼 HTTP 200 (良好) 或 HTTP 201 (已建立)，而回應本文可以包含新資源的資料。</span><span class="sxs-lookup"><span data-stu-id="0459a-274">Alternatively, if the server actually creates the object itself it could return the status codes HTTP 200 (OK) or HTTP 201 (Created) and the response body could contain the data for the new resource.</span></span> <span data-ttu-id="0459a-275">如果要求的 Content-Type 標頭指定了 Web 伺服器無法處理的資料格式，它應該會回應 HTTP 狀態碼為 415 (不支援的媒體類型)。</span><span class="sxs-lookup"><span data-stu-id="0459a-275">If the Content-Type header of the request specifies a data format that the web server cannot handle, it should respond with HTTP status code 415 (Unsupported Media Type).</span></span>

> [!TIP]
> <span data-ttu-id="0459a-276">請考慮實作可批次更新集合中多個資源的大量 HTTP PUT 作業。</span><span class="sxs-lookup"><span data-stu-id="0459a-276">Consider implementing bulk HTTP PUT operations that can batch updates to multiple resources in a collection.</span></span> <span data-ttu-id="0459a-277">PUT 要求應指定集合的 URI，而要求本文應指定要修改之資源的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="0459a-277">The PUT request should specify the URI of the collection, and the request body should specify the details of the resources to be modified.</span></span> <span data-ttu-id="0459a-278">這個方法有助於減少多對話的情況及提升效能。</span><span class="sxs-lookup"><span data-stu-id="0459a-278">This approach can help to reduce chattiness and improve performance.</span></span>
>
>

<span data-ttu-id="0459a-279">建立新資源之 HTTP POST 要求的格式與 PUT 要求的格式相似，其訊息本文包含要加入之新資源的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="0459a-279">The format of an HTTP POST requests that create new resources are similar to those of PUT requests; the message body contains the details of the new resource to be added.</span></span> <span data-ttu-id="0459a-280">不過，URI 通常會指定應加入資源的集合。</span><span class="sxs-lookup"><span data-stu-id="0459a-280">However, the URI typically specifies the collection to which the resource should be added.</span></span> <span data-ttu-id="0459a-281">下列範例會建立新訂單並加入訂單集合：</span><span class="sxs-lookup"><span data-stu-id="0459a-281">The following example creates a new order and adds it to the orders collection:</span></span>

```HTTP
POST http://adventure-works.com/orders HTTP/1.1
...
Content-Type: application/x-www-form-urlencoded
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
productID=5&quantity=15&orderValue=400
```

<span data-ttu-id="0459a-282">如果要求成功，Web 伺服器應該會回應含 HTTP 狀態碼 201 (已建立) 的訊息代碼。</span><span class="sxs-lookup"><span data-stu-id="0459a-282">If the request is successful, the web server should respond with a message code with HTTP status code 201 (Created).</span></span> <span data-ttu-id="0459a-283">Location 標頭應包含新建立之資源的 URI，且回應本文應包含一份新資源。Content-type 標頭能指定這些資料的格式：</span><span class="sxs-lookup"><span data-stu-id="0459a-283">The Location header should contain the URI of the newly created resource, and the body of the response should contain a copy of the new resource; the Content-Type header specifies the format of this data:</span></span>

```HTTP
HTTP/1.1 201 Created
...
Content-Type: application/json; charset=utf-8
Location: http://adventure-works.com/orders/99
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
{"orderID":99,"productID":5,"quantity":15,"orderValue":400}
```

> [!TIP]
> <span data-ttu-id="0459a-284">如果 PUT 或 POST 要求所提供的資料無效，Web 伺服器應回應含 HTTP 狀態碼 400 (不正確的要求) 的訊息。</span><span class="sxs-lookup"><span data-stu-id="0459a-284">If the data provided by a PUT or POST request is invalid, the web server should respond with a message with HTTP status code 400 (Bad Request).</span></span> <span data-ttu-id="0459a-285">此訊息的本文可以包含有關要求之問題的其他資訊和預期的格式，也可以包含提供更多詳細資料的 URL 連結。</span><span class="sxs-lookup"><span data-stu-id="0459a-285">The body of this message can contain additional information about the problem with the request and the formats expected, or it can contain a link to a URL that provides more details.</span></span>
>
>

<span data-ttu-id="0459a-286">若要移除資源，HTTP DELETE 要求恰恰能提供要刪除之資源的 URI。</span><span class="sxs-lookup"><span data-stu-id="0459a-286">To remove a resource, an HTTP DELETE request simply provides the URI of the resource to be deleted.</span></span> <span data-ttu-id="0459a-287">下列範例會嘗試移除訂單 99：</span><span class="sxs-lookup"><span data-stu-id="0459a-287">The following example attempts to remove order 99:</span></span>

```HTTP
DELETE http://adventure-works.com/orders/99 HTTP/1.1
...
```

<span data-ttu-id="0459a-288">如果刪除作業成功，Web 伺服器應回應 HTTP 狀態碼 204，指出已成功處理程序，但回應本文不包含進一步的資訊 (這個回應與成功 PUT 作業傳回的回應相同，只不過沒有 Location 標頭，因為資源已不存在)。如果以非同步方式執行刪除作業，DELETE 要求也可能會傳回 HTTP 狀態碼 200 (良好) 或 202 (已接受)。</span><span class="sxs-lookup"><span data-stu-id="0459a-288">If the delete operation is successful, the web server should respond with HTTP status code 204, indicating that the process has been successfully handled, but that the response body contains no further information (this is the same response returned by a successful PUT operation, but without a Location header as the resource no longer exists.) It is also possible for a DELETE request to return HTTP status code 200 (OK) or 202 (Accepted) if the deletion is performed asynchronously.</span></span>

```HTTP
HTTP/1.1 204 No Content
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
```

<span data-ttu-id="0459a-289">如果找不到資源，Web 伺服器應改為傳回 404 (找不到) 訊息。</span><span class="sxs-lookup"><span data-stu-id="0459a-289">If the resource is not found, the web server should return a 404 (Not Found) message instead.</span></span>

> [!TIP]
> <span data-ttu-id="0459a-290">如果您需要刪除集合中的所有資源，可以針對集合的 URI 指定 HTTP DELETE 要求，避免強迫應用程式輪流移除集合中的每個資源。</span><span class="sxs-lookup"><span data-stu-id="0459a-290">If all the resources in a collection need to be deleted, enable an HTTP DELETE request to be specified for the URI of the collection rather than forcing an application to remove each resource in turn from the collection.</span></span>
>
>

### <a name="filtering-and-paginating-data"></a><span data-ttu-id="0459a-291">篩選及為資料分頁</span><span class="sxs-lookup"><span data-stu-id="0459a-291">Filtering and paginating data</span></span>
<span data-ttu-id="0459a-292">URI 應該儘可能保持簡單又直覺。</span><span class="sxs-lookup"><span data-stu-id="0459a-292">You should endeavor to keep the URIs simple and intuitive.</span></span> <span data-ttu-id="0459a-293">透過單一 URI 公開資源集合能帶來這方面的優點，不過可能會導致應用程式在只需要資訊的子集時擷取大量資料。</span><span class="sxs-lookup"><span data-stu-id="0459a-293">Exposing a collection of resources through a single URI assists in this respect, but it can lead to applications fetching large amounts of data when only a subset of the information is required.</span></span> <span data-ttu-id="0459a-294">產生大量流量不僅會影響 Web 伺服器的效能和延展性，還會對要求資料之用戶端應用程式回應能力造成不良影響。</span><span class="sxs-lookup"><span data-stu-id="0459a-294">Generating a large volume of traffic impacts not only the performance and scalability of the web server but also adversely affect the responsiveness of client applications requesting the data.</span></span>

<span data-ttu-id="0459a-295">例如，如果訂單包含針對訂單支付的價格，需要擷取所有含價格 (非特定價值) 之訂單的用戶端應用程式可能需要從 /orders URI 擷取所有訂單，然後在本機篩選這些訂單。</span><span class="sxs-lookup"><span data-stu-id="0459a-295">For example, if orders contain the price paid for the order, a client application that needs to retrieve all orders that have a cost over a specific value might need to retrieve all orders from the */orders* URI and then filter these orders locally.</span></span> <span data-ttu-id="0459a-296">顯然地，這個程序的效率不佳，它浪費裝載 Web API 之伺服器的網路頻寬和處理能力。</span><span class="sxs-lookup"><span data-stu-id="0459a-296">Clearly this process is highly inefficient; it wastes network bandwidth and processing power on the server hosting the web API.</span></span>

<span data-ttu-id="0459a-297">提供 */orders/ordervalue_greater_than_n* (其中 *n* 代表訂單價格) 之類的 URI 配置可能是其中一個解決方案，但除了有限數目的價格以外，這個方法並不實用。</span><span class="sxs-lookup"><span data-stu-id="0459a-297">One solution may be to provide a URI scheme such as */orders/ordervalue_greater_than_n* where *n* is the order price, but for all but a limited number of prices such an approach is impractical.</span></span> <span data-ttu-id="0459a-298">此外，如果您需要依據其他準則查詢訂單，可能會面臨提供一長串的 URI 和可能非直覺式名稱的窘境。</span><span class="sxs-lookup"><span data-stu-id="0459a-298">Additionally, if you need to query orders based on other criteria, you can end up being faced with providing with a long list of URIs with possibly non-intuitive names.</span></span>

<span data-ttu-id="0459a-299">較好的資料篩選策略是以查詢字串形式提供要傳遞到 Web API 的篩選準則，如 /orders?ordervaluethreshold=n。</span><span class="sxs-lookup"><span data-stu-id="0459a-299">A better strategy to filtering data is to provide the filter criteria in the query string that is passed to the web API, such as */orders?ordervaluethreshold=n*.</span></span> <span data-ttu-id="0459a-300">在此範例中，Web API 中對應的作業會負責剖析和處理 `ordervaluethreshold` 查詢字串中的參數，並在 HTTP 回應中傳回篩選結果。</span><span class="sxs-lookup"><span data-stu-id="0459a-300">In this example, the corresponding operation in the web API is responsible for parsing and handling the `ordervaluethreshold` parameter in the query string and returning the filtered results in the HTTP response.</span></span>

<span data-ttu-id="0459a-301">某些針對集合資源的簡單 HTTP GET 要求可能會傳回大量的項目。</span><span class="sxs-lookup"><span data-stu-id="0459a-301">Some simple HTTP GET requests over collection resources could potentially return a large number of items.</span></span> <span data-ttu-id="0459a-302">為了對抗發生這種情況的可能性，您應該設計 Web API 以限制任何單一要求所傳回的資料量。</span><span class="sxs-lookup"><span data-stu-id="0459a-302">To combat the possibility of this occurring you should design the web API to limit the amount of data returned by any single request.</span></span> <span data-ttu-id="0459a-303">藉由支援讓使用者指定要擷取之項目數目上限的查詢字串 (其本身可能需要受到上界限制，以防止阻絕服務攻擊)，以及插入集合的起始位移，您可以達到此目的。</span><span class="sxs-lookup"><span data-stu-id="0459a-303">You can achieve this by supporting query strings that enable the user to specify the maximum number of items to be retrieved (which could itself be subject to an upperbound limit to help prevent Denial of Service attacks), and a starting offset into the collection.</span></span> <span data-ttu-id="0459a-304">例如，URI /orders?limit=25&offset=50 中的查詢字串應從訂單集合中找到的第 50 張訂單開始擷取 25 張訂單。</span><span class="sxs-lookup"><span data-stu-id="0459a-304">For example, the query string in the URI */orders?limit=25&offset=50* should retrieve 25 orders starting with the 50th order found in the orders collection.</span></span> <span data-ttu-id="0459a-305">如同篩選資料，在 Web API 中實作 GET 要求的作業需負責剖析和處理查詢字串中的 `limit` 和 `offset` 參數。</span><span class="sxs-lookup"><span data-stu-id="0459a-305">As with filtering data, the operation that implements the GET request in the web API is responsible for parsing and handling the `limit` and `offset` parameters in the query string.</span></span> <span data-ttu-id="0459a-306">為了協助用戶端應用程式，傳回已分頁資料的 GET 要求也應該包含某種形式的中繼資料，以便指出集合中的可用資源總數。</span><span class="sxs-lookup"><span data-stu-id="0459a-306">To assist client applications, GET requests that return paginated data should also include some form of metadata that indicate the total number of resources available in the collection.</span></span> <span data-ttu-id="0459a-307">您也可以考慮其他智慧型分頁策略。如需詳細資訊，請參閱 [API 設計注意事項：智慧型分頁](http://bizcoder.com/api-design-notes-smart-paging)</span><span class="sxs-lookup"><span data-stu-id="0459a-307">You might also consider other intelligent paging strategies; for more information, see [API Design Notes: Smart Paging](http://bizcoder.com/api-design-notes-smart-paging)</span></span>

<span data-ttu-id="0459a-308">您可以遵循類似的策略在擷取資料時予以排序；您可以提供將欄位名稱當做值的排序參數 (如 /orders?sort=ProductID)。</span><span class="sxs-lookup"><span data-stu-id="0459a-308">You can follow a similar strategy for sorting data as it is fetched; you could provide a sort parameter that takes a field name as the value, such as */orders?sort=ProductID*.</span></span> <span data-ttu-id="0459a-309">不過請注意，這種方法可能會對快取造成不良影響 (查詢字串參數會構成部分資源識別碼，而許多快取實作會將該識別碼當做索引鍵來快取資料)。</span><span class="sxs-lookup"><span data-stu-id="0459a-309">However, note that this approach can have a deleterious effect on caching (query string parameters form part of the resource identifier used by many cache implementations as the key to cached data).</span></span>

<span data-ttu-id="0459a-310">如果單一資源項目包含大量資料，您可以延伸這個方法來限制 (投射) 傳回的欄位。</span><span class="sxs-lookup"><span data-stu-id="0459a-310">You can extend this approach to limit (project) the fields returned if a single resource item contains a large amount of data.</span></span> <span data-ttu-id="0459a-311">例如，您可以使用接受以逗號分隔清單之欄位的查詢字串參數 (如 /orders?fields=ProductID,Quantity)。</span><span class="sxs-lookup"><span data-stu-id="0459a-311">For example, you could use a query string parameter that accepts a comma-delimited list of fields, such as */orders?fields=ProductID,Quantity*.</span></span>

> [!TIP]
> <span data-ttu-id="0459a-312">為查詢字串中所有選擇性參數提供有意義的預設值。</span><span class="sxs-lookup"><span data-stu-id="0459a-312">Give all optional parameters in query strings meaningful defaults.</span></span> <span data-ttu-id="0459a-313">例如，如果您實作分頁，可以將 `limit` 參數設為 10，並將 `offset` 參數設為 0；如果您實作排序，可以將排序參數設定為資源的索引鍵；如果您支援投射，可以將 `fields` 參數設定為資源中的所有欄位。</span><span class="sxs-lookup"><span data-stu-id="0459a-313">For example, set the `limit` parameter to 10 and the `offset` parameter to 0 if you implement pagination, set the sort parameter to the key of the resource if you implement ordering, and set the `fields` parameter to all fields in the resource if you support projections.</span></span>
>
>

### <a name="handling-large-binary-resources"></a><span data-ttu-id="0459a-314">處理大型二進位資源</span><span class="sxs-lookup"><span data-stu-id="0459a-314">Handling large binary resources</span></span>
<span data-ttu-id="0459a-315">單一資源可能包含大型的二進位欄位，如檔案或影像。</span><span class="sxs-lookup"><span data-stu-id="0459a-315">A single resource may contain large binary fields, such as files or images.</span></span> <span data-ttu-id="0459a-316">若要克服傳輸問題造成的不可靠和間歇性連線，以及改善回應時間，請考慮提供讓用戶端應用程式以區塊形式擷取這類資源的作業。</span><span class="sxs-lookup"><span data-stu-id="0459a-316">To overcome the transmission problems caused by unreliable and intermittent connections and to improve response times, consider providing operations that enable such resources to be retrieved in chunks by the client application.</span></span> <span data-ttu-id="0459a-317">若要這樣做，Web API 應支援大型資源之 GET 要求的 Accept-Ranges 標頭，最理想的情況是針對這些資源實作 HTTP HEAD 要求。</span><span class="sxs-lookup"><span data-stu-id="0459a-317">To do this, the web API should support the Accept-Ranges header for GET requests for large resources, and ideally implement HTTP HEAD requests for these resources.</span></span> <span data-ttu-id="0459a-318">Accept-Ranges 標頭表示 GET 作業支援部分結果，而且用戶端應用程式可提交傳回以位元組範圍指定之資源子集的 GET 要求。</span><span class="sxs-lookup"><span data-stu-id="0459a-318">The Accept-Ranges header indicates that the GET operation supports partial results, and that a client application can submit GET requests that return a subset of a resource specified as a range of bytes.</span></span> <span data-ttu-id="0459a-319">HEAD 要求與 GET 要求相似，不過它只會傳回描述資源的標頭和空白的訊息本文。</span><span class="sxs-lookup"><span data-stu-id="0459a-319">A HEAD request is similar to a GET request except that it only returns a header that describes the resource and an empty message body.</span></span> <span data-ttu-id="0459a-320">用戶端應用程式可以發出 HEAD 要求，以判斷是否要使用部分 GET 要求擷取資源。</span><span class="sxs-lookup"><span data-stu-id="0459a-320">A client application can issue a HEAD request to determine whether to fetch a resource by using partial GET requests.</span></span> <span data-ttu-id="0459a-321">下列範例顯示取得產品影像之相關資訊的 HEAD 要求：</span><span class="sxs-lookup"><span data-stu-id="0459a-321">The following example shows a HEAD request that obtains information about a product image:</span></span>

```HTTP
HEAD http://adventure-works.com/products/10?fields=productImage HTTP/1.1
...
```

<span data-ttu-id="0459a-322">回應訊息含有標頭，其中包括資源大小 (4580 個位元組)，以及對應 GET 作業支援部分結果的 Accept-Ranges 標頭：</span><span class="sxs-lookup"><span data-stu-id="0459a-322">The response message contains a header that includes the size of the resource (4580 bytes), and the Accept-Ranges header that the corresponding GET operation supports partial results:</span></span>

```HTTP
HTTP/1.1 200 OK
...
Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 4580
...
```

<span data-ttu-id="0459a-323">用戶端應用程式可以使用這些資訊來建構一系列的 GET 作業，以便利用較小的區塊擷取影像。</span><span class="sxs-lookup"><span data-stu-id="0459a-323">The client application can use this information to construct a series of GET operations to retrieve the image in smaller chunks.</span></span> <span data-ttu-id="0459a-324">第一個要求會使用 Range 標頭擷取前 2500 個位元組：</span><span class="sxs-lookup"><span data-stu-id="0459a-324">The first request fetches the first 2500 bytes by using the Range header:</span></span>

```HTTP
GET http://adventure-works.com/products/10?fields=productImage HTTP/1.1
Range: bytes=0-2499
...
```

<span data-ttu-id="0459a-325">回應訊息會藉由傳回 HTTP 狀態碼 206 來指出這是部分回應。</span><span class="sxs-lookup"><span data-stu-id="0459a-325">The response message indicates that this is a partial response by returning HTTP status code 206.</span></span> <span data-ttu-id="0459a-326">Content-Length 標頭能指出訊息本文傳回的實際位元組數目 (不是資源的大小)，而 Content-Range 標頭能指出這是資源的哪個部分 (4580 個位元組中的第 0-2499 個位元組)：</span><span class="sxs-lookup"><span data-stu-id="0459a-326">The Content-Length header specifies the actual number of bytes returned in the message body (not the size of the resource), and the Content-Range header indicates which part of the resource this is (bytes 0-2499 out of 4580):</span></span>

```HTTP
HTTP/1.1 206 Partial Content
...
Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 2500
Content-Range: bytes 0-2499/4580
...
_{binary data not shown}_
```

<span data-ttu-id="0459a-327">用戶端應用程式的後續要求可以使用適當的 Range 標頭來擷取資源的其餘部分：</span><span class="sxs-lookup"><span data-stu-id="0459a-327">A subsequent request from the client application can retrieve the remainder of the resource by using an appropriate Range header:</span></span>

```HTTP
GET http://adventure-works.com/products/10?fields=productImage HTTP/1.1
Range: bytes=2500-
...
```

<span data-ttu-id="0459a-328">對應的結果訊息看起來應該像這樣：</span><span class="sxs-lookup"><span data-stu-id="0459a-328">The corresponding result message should look like this:</span></span>

```HTTP
HTTP/1.1 206 Partial Content
...
Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 2080
Content-Range: bytes 2500-4580/4580
...
```

## <a name="using-the-hateoas-approach-to-enable-navigation-to-related-resources"></a><span data-ttu-id="0459a-329">使用 HATEOAS 方法來啟用相關資源導覽</span><span class="sxs-lookup"><span data-stu-id="0459a-329">Using the HATEOAS approach to enable navigation to related resources</span></span>
<span data-ttu-id="0459a-330">REST 背後的其中一個主要動機，是它應該可以在不需要事先知道 URI 配置的情況下瀏覽整組資源。</span><span class="sxs-lookup"><span data-stu-id="0459a-330">One of the primary motivations behind REST is that it should be possible to navigate the entire set of resources without requiring prior knowledge of the URI scheme.</span></span> <span data-ttu-id="0459a-331">每個 HTTP GET 要求都應傳回讓您能透過回應包含之超連結尋找與要求物件直接相關之資源的資訊，您也應提供它描述每個資源上可供使用之作業的資訊。</span><span class="sxs-lookup"><span data-stu-id="0459a-331">Each HTTP GET request should return the information necessary to find the resources related directly to the requested object through hyperlinks included in the response, and it should also be provided with information that describes the operations available on each of these resources.</span></span> <span data-ttu-id="0459a-332">此原則就是所謂的 HATEOAS (Hypertext as the Engine of Application State)。</span><span class="sxs-lookup"><span data-stu-id="0459a-332">This principle is known as HATEOAS, or Hypertext as the Engine of Application State.</span></span> <span data-ttu-id="0459a-333">系統實際上是有限的狀態機器，而每個要求的回應都包含從某個狀態轉換為另一個狀態所需的資訊。其他任何資訊都不是必要資訊。</span><span class="sxs-lookup"><span data-stu-id="0459a-333">The system is effectively a finite state machine, and the response to each request contains the information necessary to move from one state to another; no other information should be necessary.</span></span>

> [!NOTE]
> <span data-ttu-id="0459a-334">目前還沒有定義該如何為 HATEOAS 原則建立模型的標準或規格。</span><span class="sxs-lookup"><span data-stu-id="0459a-334">Currently there are no standards or specifications that define how to model the HATEOAS principle.</span></span> <span data-ttu-id="0459a-335">本節所示的範例將說明一個可行的解決方案。</span><span class="sxs-lookup"><span data-stu-id="0459a-335">The examples shown in this section illustrate one possible solution.</span></span>
>
>

<span data-ttu-id="0459a-336">舉例來說，若要處理客戶和訂單之間的關聯性，針對特定訂單傳回之回應中的資料應包含超連結格式的 URI，以便識別下訂單的客戶，以及可針對該客戶執行的作業。</span><span class="sxs-lookup"><span data-stu-id="0459a-336">As an example, to handle the relationship between customers and orders, the data returned in the response for a specific order should contain URIs in the form of a hyperlink identifying the customer that placed the order, and the operations that can be performed on that customer.</span></span>

```HTTP
GET http://adventure-works.com/orders/3 HTTP/1.1
Accept: application/json
...
```

<span data-ttu-id="0459a-337">回應訊息的本文包含 `links` 陣列 (程式碼範例中反白顯示的部分)，指定關聯性的本質 (Customer)、客戶的 URI (http://adventure-works.com/customers/3)、如何擷取此客戶的詳細資料 (GET)，以及 Web 伺服器支援來擷取這些資訊的 MIME 類型 (text/xml 和 application/json)。</span><span class="sxs-lookup"><span data-stu-id="0459a-337">The body of the response message contains a `links` array (highlighted in the code example) that specifies the nature of the relationship (*Customer*), the URI of the customer (*http://adventure-works.com/customers/3*), how to retrieve the details of this customer (*GET*), and the MIME types that the web server supports for retrieving this information (*text/xml* and *application/json*).</span></span> <span data-ttu-id="0459a-338">這是用戶端應用程式要能夠擷取客戶詳細資料所需的所有資訊。</span><span class="sxs-lookup"><span data-stu-id="0459a-338">This is all the information that a client application needs to be able to fetch the details of the customer.</span></span> <span data-ttu-id="0459a-339">此外，連結陣列也包含其他可執行之作業的連結，如 PUT (修改客戶，以及 Web 伺服器預期用戶端提供的格式) 和 DELETE。</span><span class="sxs-lookup"><span data-stu-id="0459a-339">Additionally, the Links array also includes links for the other operations that can be performed, such as PUT (to modify the customer, together with the format that the web server expects the client to provide), and DELETE.</span></span>

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"orderID":3,"productID":2,"quantity":4,"orderValue":16.60,"links":[(some links omitted){"rel":"customer","href":" http://adventure-works.com/customers/3", "action":"GET","types":["text/xml","application/json"]},{"rel":"
customer","href":" http://adventure-works.com /customers/3", "action":"PUT","types":["application/x-www-form-urlencoded"]},{"rel":"customer","href":" http://adventure-works.com /customers/3","action":"DELETE","types":[]}]}
```

<span data-ttu-id="0459a-340">基於完整性考量，連結陣列也應包含有關已擷取之資源的自我參考資訊。</span><span class="sxs-lookup"><span data-stu-id="0459a-340">For completeness, the Links array should also include self-referencing information pertaining to the resource that has been retrieved.</span></span> <span data-ttu-id="0459a-341">先前的範例省略了這些連結，不過我們在下列程式碼中反白顯示。</span><span class="sxs-lookup"><span data-stu-id="0459a-341">These links have been omitted from the previous example, but are highlighted in the following code.</span></span> <span data-ttu-id="0459a-342">請注意，在這些連結中，我們使用關聯性 self 來指出這是作業所傳回之資源的參考：</span><span class="sxs-lookup"><span data-stu-id="0459a-342">Notice that in these links, the relationship *self* has been used to indicate that this is a reference to the resource being returned by the operation:</span></span>

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"orderID":3,"productID":2,"quantity":4,"orderValue":16.60,"links":[{"rel":"self","href":" http://adventure-works.com/orders/3", "action":"GET","types":["text/xml","application/json"]},{"rel":" self","href":" http://adventure-works.com /orders/3", "action":"PUT","types":["application/x-www-form-urlencoded"]},{"rel":"self","href":" http://adventure-works.com /orders/3", "action":"DELETE","types":[]},{"rel":"customer",
"href":" http://adventure-works.com /customers/3", "action":"GET","types":["text/xml","application/json"]},{"rel":" customer" (customer links omitted)}]}
```

<span data-ttu-id="0459a-343">這個方法若要生效，您必須備妥用戶端應用程式，使其能擷取及剖析這些額外資訊。</span><span class="sxs-lookup"><span data-stu-id="0459a-343">For this approach to be effective, client applications must be prepared to retrieve and parse this additional information.</span></span>

## <a name="versioning-a-restful-web-api"></a><span data-ttu-id="0459a-344">符合 REST 限制的 Web API 版本控制</span><span class="sxs-lookup"><span data-stu-id="0459a-344">Versioning a RESTful web API</span></span>
<span data-ttu-id="0459a-345">即使是在所有最簡單的情況下，Web API 依然不太可能維持不變。</span><span class="sxs-lookup"><span data-stu-id="0459a-345">It is highly unlikely that in all but the simplest of situations that a web API will remain static.</span></span> <span data-ttu-id="0459a-346">隨著商務需求變更，我們可能會加入新資源集合、資源之間的關聯性可能會改變，也會修改資源中的資料結構。</span><span class="sxs-lookup"><span data-stu-id="0459a-346">As business requirements change new collections of resources may be added, the relationships between resources might change, and the structure of the data in resources might be amended.</span></span> <span data-ttu-id="0459a-347">雖然更新 Web API 來處理新的或不同的需求是相對簡單的程序，不過您必須將這類變更對取用 Web API 之用戶端應用程式所造成的效果納入考量。</span><span class="sxs-lookup"><span data-stu-id="0459a-347">While updating a web API to handle new or differing requirements is a relatively straightforward process, you must consider the effects that such changes will have on client applications consuming the web API.</span></span> <span data-ttu-id="0459a-348">問題在於雖然設計和實作 Web API 的開發人員擁有該 API 完整的控制能力，不過對於從遠端作業之第三方組織所建置的用戶端應用程式，開發人員並沒有相同程度的控制能力。</span><span class="sxs-lookup"><span data-stu-id="0459a-348">The issue is that although the developer designing and implementing a web API has full control over that API, the developer does not have the same degree of control over client applications which may be built by third party organizations operating remotely.</span></span> <span data-ttu-id="0459a-349">主要的要務是讓現有用戶端應用程式在未變更的情況下繼續運作，同時允許新用戶端應用程式充分運用新功能和資源。</span><span class="sxs-lookup"><span data-stu-id="0459a-349">The primary imperative is to enable existing client applications to continue functioning unchanged while allowing new client applications to take advantage of new features and resources.</span></span>

<span data-ttu-id="0459a-350">版本控制可讓 Web API 指定其公開的功能和資源，而用戶端應用程式則可以提交導向特定版本之功能或資源的要求。</span><span class="sxs-lookup"><span data-stu-id="0459a-350">Versioning enables a web API to indicate the features and resources that it exposes, and a client application can submit requests that are directed to a specific version of a feature or resource.</span></span> <span data-ttu-id="0459a-351">下列小節描述幾個不同的方法，每個方法都有其優點和缺點。</span><span class="sxs-lookup"><span data-stu-id="0459a-351">The following sections describe several different approaches, each of which has its own benefits and trade-offs.</span></span>

### <a name="no-versioning"></a><span data-ttu-id="0459a-352">無版本控制</span><span class="sxs-lookup"><span data-stu-id="0459a-352">No versioning</span></span>
<span data-ttu-id="0459a-353">這是最簡單的方法，而且也是某些內部 API 可接收的方法。</span><span class="sxs-lookup"><span data-stu-id="0459a-353">This is the simplest approach, and may be acceptable for some internal APIs.</span></span> <span data-ttu-id="0459a-354">重大變更可能會以新資源或新連結來呈現。</span><span class="sxs-lookup"><span data-stu-id="0459a-354">Big changes could be represented as new resources or new links.</span></span>  <span data-ttu-id="0459a-355">將內容加入現有資源可能不會成為重大變更，因為未預期要查看此內容的用戶端應用程式會直接忽略。</span><span class="sxs-lookup"><span data-stu-id="0459a-355">Adding content to existing resources might not present a breaking change as client applications that are not expecting to see this content will simply ignore it.</span></span>

<span data-ttu-id="0459a-356">例如，傳送給 URI *http://adventure-works.com/customers/3* 的要求應該會傳回單一客戶的詳細資料，其中包含用戶端應用程式所預期的 `id`、`name` 和 `address` 欄位：</span><span class="sxs-lookup"><span data-stu-id="0459a-356">For example, a request to the URI *http://adventure-works.com/customers/3* should return the details of a single customer containing `id`, `name`, and `address` fields expected by the client application:</span></span>

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

> [!NOTE]
> <span data-ttu-id="0459a-357">為了簡化及避免困擾，本節所示的範例回應不包含 HATEOAS 連結。</span><span class="sxs-lookup"><span data-stu-id="0459a-357">For the purposes of simplicity and clarity, the example responses shown in this section do not include HATEOAS links.</span></span>
>
>

<span data-ttu-id="0459a-358">如果您將 `DateCreated` 欄位加入客戶資源的結構描述，回應看起來會像這樣：</span><span class="sxs-lookup"><span data-stu-id="0459a-358">If the `DateCreated` field is added to the schema of the customer resource, then the response would look like this:</span></span>

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":"1 Microsoft Way Redmond WA 98053"}
```

<span data-ttu-id="0459a-359">如果現有用戶端應用程式能略過無法辨認的欄位，它們可能會繼續正常運作，而新的用戶端應用程式可以在經過設計後處理這個新欄位。</span><span class="sxs-lookup"><span data-stu-id="0459a-359">Existing client applications might continue functioning correctly if they are capable of ignoring unrecognized fields, while new client applications can be designed to handle this new field.</span></span> <span data-ttu-id="0459a-360">不過，如果資源的結構描述發生更巨大的變動 (如移除或重新命名欄位)，或資源之間的關聯性改變，這些變更可能會構成阻礙現有用戶端應用程式正常運作的重大變更。</span><span class="sxs-lookup"><span data-stu-id="0459a-360">However, if more radical changes to the schema of resources occur (such as removing or renaming fields) or the relationships between resources change then these may constitute breaking changes that prevent existing client applications from functioning correctly.</span></span> <span data-ttu-id="0459a-361">在這些情況下，您應該考慮下列其中一個方法。</span><span class="sxs-lookup"><span data-stu-id="0459a-361">In these situations you should consider one of the following approaches.</span></span>

### <a name="uri-versioning"></a><span data-ttu-id="0459a-362">URI 版本控制</span><span class="sxs-lookup"><span data-stu-id="0459a-362">URI versioning</span></span>
<span data-ttu-id="0459a-363">每次修改 Web API 或變更資源的結構描述時，您會在每個資源的 URI 加入版本號碼。</span><span class="sxs-lookup"><span data-stu-id="0459a-363">Each time you modify the web API or change the schema of resources, you add a version number to the URI for each resource.</span></span> <span data-ttu-id="0459a-364">早已存在的 URI 應維持先前的運作，傳回符合原始結構描述的資源。</span><span class="sxs-lookup"><span data-stu-id="0459a-364">The previously existing URIs should continue to operate as before, returning resources that conform to their original schema.</span></span>

<span data-ttu-id="0459a-365">延伸上述範例，如果將 `address` 欄位重建為包含位址之每個構成組件的子欄位 (如 `streetAddress`、`city`、`state` 及 `zipCode`)，您可以透過包含版本號碼的 URI (如 http://adventure-works.com/v2/customers/3) 公開這個版本的資源：</span><span class="sxs-lookup"><span data-stu-id="0459a-365">Extending the previous example, if the `address` field is restructured into sub-fields containing each constituent part of the address (such as `streetAddress`, `city`, `state`, and `zipCode`), this version of the resource could be exposed through a URI containing a version number, such as http://adventure-works.com/v2/customers/3:</span></span>

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":{"streetAddress":"1 Microsoft Way","city":"Redmond","state":"WA","zipCode":98053}}
```

<span data-ttu-id="0459a-366">這個版本控制機制非常簡單，但需仰賴伺服器將要求路由傳送到適當端點。</span><span class="sxs-lookup"><span data-stu-id="0459a-366">This versioning mechanism is very simple but depends on the server routing the request to the appropriate endpoint.</span></span> <span data-ttu-id="0459a-367">不過，在經過數個反覆項目後當 Web API 成熟時，它會變得難以揮灑，因此伺服器必須支援許多不同的版本。</span><span class="sxs-lookup"><span data-stu-id="0459a-367">However, it can become unwieldy as the web API matures through several iterations and the server has to support a number of different versions.</span></span> <span data-ttu-id="0459a-368">此外，從純化論者的觀點來看，在所有情況下用戶端應用程式都在擷取相同的資料 (客戶 3)，所以 URI 不應該因版本而有所不同。</span><span class="sxs-lookup"><span data-stu-id="0459a-368">Also, from a purist’s point of view, in all cases the client applications are fetching the same data (customer 3), so the URI should not really be different depending on the version.</span></span> <span data-ttu-id="0459a-369">此配置也會讓 HATEOAS 的實作變得更複雜，因為所有連結都需要在它們的 URI 中包含版本號碼。</span><span class="sxs-lookup"><span data-stu-id="0459a-369">This scheme also complicates implementation of HATEOAS as all links will need to include the version number in their URIs.</span></span>

### <a name="query-string-versioning"></a><span data-ttu-id="0459a-370">查詢字串版本控制</span><span class="sxs-lookup"><span data-stu-id="0459a-370">Query string versioning</span></span>
<span data-ttu-id="0459a-371">與其提供多個 URI，您可以在附加至 HTTP 要求的查詢字串中，藉由使用參數來指定資源的版本，如 *http://adventure-works.com/customers/3?version=2*。</span><span class="sxs-lookup"><span data-stu-id="0459a-371">Rather than providing multiple URIs, you can specify the version of the resource by using a parameter within the query string appended to the HTTP request, such as *http://adventure-works.com/customers/3?version=2*.</span></span> <span data-ttu-id="0459a-372">如果較舊的用戶端應用程式省略版本參數，它應該預設成有意義的值 (如 1)。</span><span class="sxs-lookup"><span data-stu-id="0459a-372">The version parameter should default to a meaningful value such as 1 if it is omitted by older client applications.</span></span>

<span data-ttu-id="0459a-373">這個方法具有語意上的優點，因為您總是從相同的 URI 擷取相同的資源，不過這還是要取決於處理要求以剖析查詢字串，然後回傳適當 HTTP 回應的程式碼。</span><span class="sxs-lookup"><span data-stu-id="0459a-373">This approach has the semantic advantage that the same resource is always retrieved from the same URI, but it depends on the code that handles the request to parse the query string and send back the appropriate HTTP response.</span></span> <span data-ttu-id="0459a-374">這個方法也需要面臨與實作 HATEOAS 同等複雜的 URI 版本控制機制。</span><span class="sxs-lookup"><span data-stu-id="0459a-374">This approach also suffers from the same complications for implementing HATEOAS as the URI versioning mechanism.</span></span>

> [!NOTE]
> <span data-ttu-id="0459a-375">某些較舊的網頁瀏覽器和 Web Proxy 不會快取在 URL 中包含查詢字串的要求回應。</span><span class="sxs-lookup"><span data-stu-id="0459a-375">Some older web browsers and web proxies will not cache responses for requests that include a query string in the URL.</span></span> <span data-ttu-id="0459a-376">對於使用 Web API 且從這類網頁瀏覽器內部執行的 Web 應用程式，這會對應用程式的效能造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="0459a-376">This can have an adverse impact on performance for web applications that use a web API and that run from within such a web browser.</span></span>
>
>

### <a name="header-versioning"></a><span data-ttu-id="0459a-377">標頭版本控制</span><span class="sxs-lookup"><span data-stu-id="0459a-377">Header versioning</span></span>
<span data-ttu-id="0459a-378">與其以查詢字串參數的形式附加版本號碼，您可以實作指出資源版本的自訂標頭。</span><span class="sxs-lookup"><span data-stu-id="0459a-378">Rather than appending the version number as a query string parameter, you could implement a custom header that indicates the version of the resource.</span></span> <span data-ttu-id="0459a-379">這個方法需要用戶端應用程式將適當標頭加入任何要求中，不過如果省略版本標頭，處理用戶端要求的程式碼可以使用預設值 (第 1 版)。</span><span class="sxs-lookup"><span data-stu-id="0459a-379">This approach requires that the client application adds the appropriate header to any requests, although the code handling the client request could use a default value (version 1) if the version header is omitted.</span></span> <span data-ttu-id="0459a-380">下列範例使用名為 Custom-Header的自訂標頭。</span><span class="sxs-lookup"><span data-stu-id="0459a-380">The following examples utilize a custom header named *Custom-Header*.</span></span> <span data-ttu-id="0459a-381">此標頭的值指出 Web API 的版本。</span><span class="sxs-lookup"><span data-stu-id="0459a-381">The value of this header indicates the version of web API.</span></span>

<span data-ttu-id="0459a-382">第 1 版：</span><span class="sxs-lookup"><span data-stu-id="0459a-382">Version 1:</span></span>

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
...
Custom-Header: api-version=1
...
```

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

<span data-ttu-id="0459a-383">第 2 版：</span><span class="sxs-lookup"><span data-stu-id="0459a-383">Version 2:</span></span>

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
...
Custom-Header: api-version=2
...
```

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":{"streetAddress":"1 Microsoft Way","city":"Redmond","state":"WA","zipCode":98053}}
```

<span data-ttu-id="0459a-384">請注意，如同先前的兩個方法，實作 HATEOAS 需要將適當的自訂標頭加入任何連結中。</span><span class="sxs-lookup"><span data-stu-id="0459a-384">Note that as with the previous two approaches, implementing HATEOAS requires including the appropriate custom header in any links.</span></span>

### <a name="media-type-versioning"></a><span data-ttu-id="0459a-385">媒體類型版本控制</span><span class="sxs-lookup"><span data-stu-id="0459a-385">Media type versioning</span></span>
<span data-ttu-id="0459a-386">當用戶端應用程式將 HTTP GET 要求傳送至 Web 伺服器時，它應該會使用 Accept 標頭來規定可處理的內容格式 (如本指引稍早所述)。</span><span class="sxs-lookup"><span data-stu-id="0459a-386">When a client application sends an HTTP GET request to a web server it should stipulate the format of the content that it can handle by using an Accept header, as described earlier in this guidance.</span></span> <span data-ttu-id="0459a-387">Accept 標頭的目的經常是讓用戶端應用程式指定回應本文應該是 XML、JSON 或其他某些用戶端可剖析的通用格式。</span><span class="sxs-lookup"><span data-stu-id="0459a-387">Frequently the purpose of the *Accept* header is to allow the client application to specify whether the body of the response should be XML, JSON, or some other common format that the client can parse.</span></span> <span data-ttu-id="0459a-388">不過，您也可以定義自訂媒體類型，加入讓用戶端應用程式指定預期之資源版本的資訊。</span><span class="sxs-lookup"><span data-stu-id="0459a-388">However, it is possible to define custom media types that include information enabling the client application to indicate which version of a resource it is expecting.</span></span> <span data-ttu-id="0459a-389">下列範例顯示將 Accept 標頭指定為 application/vnd.adventure-works.v1+json 值的要求。</span><span class="sxs-lookup"><span data-stu-id="0459a-389">The following example shows a request that specifies an *Accept* header with the value *application/vnd.adventure-works.v1+json*.</span></span> <span data-ttu-id="0459a-390">Vnd.adventure works.v1 元素指示 Web 伺服器傳回第 1 版的資源，而 json 元素指定回應本文的格式應該是 JSON：</span><span class="sxs-lookup"><span data-stu-id="0459a-390">The *vnd.adventure-works.v1* element indicates to the web server that it should return version 1 of the resource, while the *json* element specifies that the format of the response body should be JSON:</span></span>

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
...
Accept: application/vnd.adventure-works.v1+json
...
```

<span data-ttu-id="0459a-391">處理要求的程式碼會負責處理 Accept 標頭且盡可能遵循 (用戶端應用程式可能會在 Accept 標頭中指定多個格式。不論如何，Web 伺服器可以選擇最合適的回應本文格式)。</span><span class="sxs-lookup"><span data-stu-id="0459a-391">The code handling the request is responsible for processing the *Accept* header and honoring it as far as possible (the client application may specify multiple formats in the *Accept* header, in which case the web server can choose the most appropriate format for the response body).</span></span> <span data-ttu-id="0459a-392">Web 伺服器會使用 Content-Type 標頭確認回應本文中的資料格式：</span><span class="sxs-lookup"><span data-stu-id="0459a-392">The web server confirms the format of the data in the response body by using the Content-Type header:</span></span>

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/vnd.adventure-works.v1+json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

<span data-ttu-id="0459a-393">如果 Accept 標頭未指定任何已知的媒體類型，Web 伺服器可能會產生 HTTP 406 (無法接受) 回應訊息，或以預設媒體類型傳回訊息。</span><span class="sxs-lookup"><span data-stu-id="0459a-393">If the Accept header does not specify any known media types, the web server could generate an HTTP 406 (Not Acceptable) response message or return a message with a default media type.</span></span>

<span data-ttu-id="0459a-394">這個方法可以說是最純粹的版本控制機制，自然也符合 HATEOAS 的本質，也就是在資源連結中包含 MIME 類型的相關資料。</span><span class="sxs-lookup"><span data-stu-id="0459a-394">This approach is arguably the purest of the versioning mechanisms and lends itself naturally to HATEOAS, which can include the MIME type of related data in resource links.</span></span>

> [!NOTE]
> <span data-ttu-id="0459a-395">在選擇版本控制策略時，您也應該將對效能的潛在影響 (特別是 Web 伺服器上的快取) 納入考量。</span><span class="sxs-lookup"><span data-stu-id="0459a-395">When you select a versioning strategy, you should also consider the implications on performance, especially caching on the web server.</span></span> <span data-ttu-id="0459a-396">有鑑於同一個 URI/查詢字串組合每次都會參考相同的資料，因此 URI 版本控制和查詢字串版本控制等配置具備易於快取的特性。</span><span class="sxs-lookup"><span data-stu-id="0459a-396">The URI versioning and Query String versioning schemes are cache-friendly inasmuch as the same URI/query string combination refers to the same data each time.</span></span>
>
> <span data-ttu-id="0459a-397">標頭版本控制和媒體類型版本控制等機制，通常需要額外的邏輯來檢查自訂標頭或 Accept 標頭中的值。</span><span class="sxs-lookup"><span data-stu-id="0459a-397">The Header versioning and Media Type versioning mechanisms typically require additional logic to examine the values in the custom header or the Accept header.</span></span> <span data-ttu-id="0459a-398">在大規模的環境中，使用不同版本之 Web API 的眾多用戶端可能會導致伺服器端快取出現大量的重複資料。</span><span class="sxs-lookup"><span data-stu-id="0459a-398">In a large-scale environment, many clients using different versions of a web API can result in a significant amount of duplicated data in a server-side cache.</span></span> <span data-ttu-id="0459a-399">如果用戶端應用程式透過實作快取的 Proxy 與 Web 伺服器通訊，而且只有在快取中目前沒有要求之資料的副本時才會將要求轉送到 Web 伺服器，這個問題可能會變得更嚴重。</span><span class="sxs-lookup"><span data-stu-id="0459a-399">This issue can become acute if a client application communicates with a web server through a proxy that implements caching, and that only forwards a request to the web server if it does not currently hold a copy of the requested data in its cache.</span></span>
>
>

## <a name="open-api-initiative"></a><span data-ttu-id="0459a-400">Open API Initiative</span><span class="sxs-lookup"><span data-stu-id="0459a-400">Open API Initiative</span></span>
<span data-ttu-id="0459a-401">[Open API Initiative](https://www.openapis.org/) 是由產業協會所建立，用來將各廠商間的 REST API 描述標準化。</span><span class="sxs-lookup"><span data-stu-id="0459a-401">The [Open API Initiative](https://www.openapis.org/) was created by an industry consortium to standardize REST API descriptions across vendors.</span></span> <span data-ttu-id="0459a-402">隨著此計畫，Swagger 2.0 規格已重新命名為 OpenAPI 規格 (OAS)，並由 Open API 計畫管理。</span><span class="sxs-lookup"><span data-stu-id="0459a-402">As part of this initiative, the Swagger 2.0 specification was renamed the OpenAPI Specification (OAS) and brought under the Open API Initiative.</span></span>

<span data-ttu-id="0459a-403">您可以為您的 Web API 採用 OpenAPI。</span><span class="sxs-lookup"><span data-stu-id="0459a-403">You may want to adopt OpenAPI for your web APIs.</span></span> <span data-ttu-id="0459a-404">考慮事項：</span><span class="sxs-lookup"><span data-stu-id="0459a-404">Some points to consider:</span></span>

- <span data-ttu-id="0459a-405">OpenAPI 規格隨附一組有關應如何設計 REST API 的固持己見指引。</span><span class="sxs-lookup"><span data-stu-id="0459a-405">The OpenAPI Specification comes with with a set of opinionated guidelines on how a REST API should be designed.</span></span> <span data-ttu-id="0459a-406">該指引對於互通性會有好處，但在設計您的 API 時需要更小心，才能符合規格。</span><span class="sxs-lookup"><span data-stu-id="0459a-406">That has advantages for interoperability, but requires more care when designing your API to conform to the specification.</span></span>
- <span data-ttu-id="0459a-407">OpenAPI 會將合約優先方法升階，而不是將實作優先方法升階。</span><span class="sxs-lookup"><span data-stu-id="0459a-407">OpenAPI promotes a contract-first approach, rather than an implementation-first approach.</span></span> <span data-ttu-id="0459a-408">合約優先表示您會先設計 API 合約 (介面)，然後編寫實作合約的程式碼。</span><span class="sxs-lookup"><span data-stu-id="0459a-408">Contract-first means you design the API contract (the interface) first and then write code that implements the contract.</span></span> 
- <span data-ttu-id="0459a-409">Swagger 等工具可以從 API 合約產生用戶端程式庫或文件集。</span><span class="sxs-lookup"><span data-stu-id="0459a-409">Tools like Swagger can generate client libraries or documentation from API contracts.</span></span> <span data-ttu-id="0459a-410">例如，請參閱[使用 Swagger 的 ASP.NET Web API 說明頁面](/aspnet/core/tutorials/web-api-help-pages-using-swagger)。</span><span class="sxs-lookup"><span data-stu-id="0459a-410">For example, see [ASP.NET Web API Help Pages using Swagger](/aspnet/core/tutorials/web-api-help-pages-using-swagger).</span></span>

## <a name="more-information"></a><span data-ttu-id="0459a-411">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="0459a-411">More information</span></span>
* <span data-ttu-id="0459a-412">[Microsoft REST API 指引](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md)包含設計公用 REST API 的詳細建議。</span><span class="sxs-lookup"><span data-stu-id="0459a-412">The [Microsoft REST API Guidelines](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md) contain detailed recommendations for designing public REST APIs.</span></span>
* <span data-ttu-id="0459a-413">[RESTful Cookbook](http://restcookbook.com/) (英文) 含有建置符合 REST 限制之 Web API 的簡介。</span><span class="sxs-lookup"><span data-stu-id="0459a-413">The [RESTful Cookbook](http://restcookbook.com/) contains an introduction to building RESTful APIs.</span></span>
* <span data-ttu-id="0459a-414">[Web API 檢查清單](https://mathieu.fenniak.net/the-api-checklist/)含有在設計及實作 Web API 時應納入考量的實用項目清單。</span><span class="sxs-lookup"><span data-stu-id="0459a-414">The [Web API Checklist](https://mathieu.fenniak.net/the-api-checklist/) contains a useful list of items to consider when designing and implementing a web API.</span></span>
* <span data-ttu-id="0459a-415">[Open API Initiative](https://www.openapis.org/) 網站包含 Open API 的所有相關文件和實作詳細資料。</span><span class="sxs-lookup"><span data-stu-id="0459a-415">The [Open API Initiative](https://www.openapis.org/) site, contains all related documentation and implementation details on Open API.</span></span>

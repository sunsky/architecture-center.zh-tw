---
title: API 實作指引
titleSuffix: Best practices for cloud applications
description: 如何實作 API 的指示。
author: dragon119
ms.date: 07/13/2016
ms.topic: best-practice
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: aa69746b59ddfff02381dd811caa9a7aa62d8b7b
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54484800"
---
# <a name="api-implementation"></a><span data-ttu-id="46ec7-103">API 實作</span><span class="sxs-lookup"><span data-stu-id="46ec7-103">API implementation</span></span>

<span data-ttu-id="46ec7-104">仔細設計的 RESTful Web API 可定義資源、關係以及用戶端應用程式可存取的導覽配置。</span><span class="sxs-lookup"><span data-stu-id="46ec7-104">A carefully-designed RESTful web API defines the resources, relationships, and navigation schemes that are accessible to client applications.</span></span> <span data-ttu-id="46ec7-105">當您實作和部署 Web API 時，您應該考慮裝載 Web API 之環境的實際需求，以及 Web API 的建構方式 (而非資料的邏輯結構)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-105">When you implement and deploy a web API, you should consider the physical requirements of the environment hosting the web API and the way in which the web API is constructed rather than the logical structure of the data.</span></span> <span data-ttu-id="46ec7-106">本指引著重於實作 Web API 和加以發佈以供用戶端應用程式使用的最佳作法。</span><span class="sxs-lookup"><span data-stu-id="46ec7-106">This guidance focusses on best practices for implementing a web API and publishing it to make it available to client applications.</span></span> <span data-ttu-id="46ec7-107">如需 Web API 設計的詳細資訊，請參閱 [API 設計指引](/azure/architecture/best-practices/api-design)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-107">For detailed information about web API design, see [API Design Guidance](/azure/architecture/best-practices/api-design).</span></span>

## <a name="processing-requests"></a><span data-ttu-id="46ec7-108">處理要求</span><span class="sxs-lookup"><span data-stu-id="46ec7-108">Processing requests</span></span>

<span data-ttu-id="46ec7-109">當您實作程式碼來處理要求時，請考慮下列幾點。</span><span class="sxs-lookup"><span data-stu-id="46ec7-109">Consider the following points when you implement the code to handle requests.</span></span>

### <a name="get-put-delete-head-and-patch-actions-should-be-idempotent"></a><span data-ttu-id="46ec7-110">GET、PUT、DELETE、HEAD 和 PATCH 動作應該是等冪的</span><span class="sxs-lookup"><span data-stu-id="46ec7-110">GET, PUT, DELETE, HEAD, and PATCH actions should be idempotent</span></span>

<span data-ttu-id="46ec7-111">實作這些要求的程式碼應該不會造成任何副作用。</span><span class="sxs-lookup"><span data-stu-id="46ec7-111">The code that implements these requests should not impose any side-effects.</span></span> <span data-ttu-id="46ec7-112">對相同資源重複提出的相同要求應該會導致相同的狀態。</span><span class="sxs-lookup"><span data-stu-id="46ec7-112">The same request repeated over the same resource should result in the same state.</span></span> <span data-ttu-id="46ec7-113">例如，將多個 DELETE 要求傳送至相同的 URI 應該有相同的效果，雖然回應訊息中的 HTTP 狀態碼可能不同。</span><span class="sxs-lookup"><span data-stu-id="46ec7-113">For example, sending multiple DELETE requests to the same URI should have the same effect, although the HTTP status code in the response messages may be different.</span></span> <span data-ttu-id="46ec7-114">第一個 DELETE 要求可能會傳回狀態碼 204 (沒有內容)，而後續的 DELETE 要求可能會傳回狀態碼 404 (找不到)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-114">The first DELETE request might return status code 204 (No Content), while a subsequent DELETE request might return status code 404 (Not Found).</span></span>

> [!NOTE]
> <span data-ttu-id="46ec7-115">Jonathan Oliver 部落格上的 [冪等模式](https://blog.jonathanoliver.com/idempotency-patterns/) 一文提供冪等概觀，以及其與資料管理作業有何相關。</span><span class="sxs-lookup"><span data-stu-id="46ec7-115">The article [Idempotency Patterns](https://blog.jonathanoliver.com/idempotency-patterns/) on Jonathan Oliver’s blog provides an overview of idempotency and how it relates to data management operations.</span></span>

### <a name="post-actions-that-create-new-resources-should-not-have-unrelated-side-effects"></a><span data-ttu-id="46ec7-116">建立新資源的 POST 動作不應有無關的副作用</span><span class="sxs-lookup"><span data-stu-id="46ec7-116">POST actions that create new resources should not have unrelated side-effects</span></span>

<span data-ttu-id="46ec7-117">如果 POST 要求旨在建立新資源，則應將要求的效果限制於新資源 (如果涉及某種形式的連結，則可能是任何直接相關的資源)。例如，在電子商務系統中，為客戶建立新訂單的 POST 要求也可能會修改庫存水準並產生帳單資訊，但不得修改與訂單不直接相關的資訊，或對系統的整體狀態有任何其他副作用。</span><span class="sxs-lookup"><span data-stu-id="46ec7-117">If a POST request is intended to create a new resource, the effects of the request should be limited to the new resource (and possibly any directly related resources if there is some sort of linkage involved) For example, in an ecommerce system, a POST request that creates a new order for a customer might also amend inventory levels and generate billing information, but it should not modify information not directly related to the order or have any other side-effects on the overall state of the system.</span></span>

### <a name="avoid-implementing-chatty-post-put-and-delete-operations"></a><span data-ttu-id="46ec7-118">避免實作繁複的 POST、PUT 和 DELETE 作業</span><span class="sxs-lookup"><span data-stu-id="46ec7-118">Avoid implementing chatty POST, PUT, and DELETE operations</span></span>

<span data-ttu-id="46ec7-119">支援對資源集合的 POST、PUT 和 DELETE 要求。</span><span class="sxs-lookup"><span data-stu-id="46ec7-119">Support POST, PUT and DELETE requests over resource collections.</span></span> <span data-ttu-id="46ec7-120">POST 要求可以包含多項新資源的詳細資料，並將它們全部加入相同的集合中，PUT 要求可以取代集合中的整組資源，而 DELETE 要求可以移除整個集合。</span><span class="sxs-lookup"><span data-stu-id="46ec7-120">A POST request can contain the details for multiple new resources and add them all to the same collection, a PUT request can replace the entire set of resources in a collection, and a DELETE request can remove an entire collection.</span></span>

<span data-ttu-id="46ec7-121">ASP.NET Web API 2 內含的 OData 支援可提供批次要求的功能。</span><span class="sxs-lookup"><span data-stu-id="46ec7-121">The OData support included in ASP.NET Web API 2 provides the ability to batch requests.</span></span> <span data-ttu-id="46ec7-122">用戶端應用程式可以封裝數個 Web API 要求並將它們傳送至單一 HTTP 要求中的伺服器，以及接收包含各要求之回覆的單一 HTTP 回應。</span><span class="sxs-lookup"><span data-stu-id="46ec7-122">A client application can package up several web API requests and send them to the server in a single HTTP request, and receive a single HTTP response that contains the replies to each request.</span></span> <span data-ttu-id="46ec7-123">如需詳細資訊，請參閱 [Web API 和 Web API OData 中的 Batch 支援簡介](https://blogs.msdn.microsoft.com/webdev/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata/)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-123">For more information, [Introducing Batch Support in Web API and Web API OData](https://blogs.msdn.microsoft.com/webdev/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata/).</span></span>

### <a name="follow-the-http-specification-when-sending-a-response"></a><span data-ttu-id="46ec7-124">傳送回應時請遵循 HTTP 規格</span><span class="sxs-lookup"><span data-stu-id="46ec7-124">Follow the HTTP specification when sending a response</span></span>

<span data-ttu-id="46ec7-125">Web API 必須傳回包含下列資訊的訊息：正確 HTTP 狀態碼可讓用戶端決定如何處理結果、適當的 HTTP 標頭可讓用戶端了解結果的本質，而適當格式化的內文可讓用戶端剖析結果。</span><span class="sxs-lookup"><span data-stu-id="46ec7-125">A web API must return messages that contain the correct HTTP status code to enable the client to determine how to handle the result, the appropriate HTTP headers so that the client understands the nature of the result, and a suitably formatted body to enable the client to parse the result.</span></span>

<span data-ttu-id="46ec7-126">例如，POST 作業應傳回狀態碼 201 (已建立)，而回應訊息的 Location 標頭中應包含新建資源的 URI。</span><span class="sxs-lookup"><span data-stu-id="46ec7-126">For example, a POST operation should return status code 201 (Created) and the response message should include the URI of the newly created resource in the Location header of the response message.</span></span>

### <a name="support-content-negotiation"></a><span data-ttu-id="46ec7-127">支援內容交涉</span><span class="sxs-lookup"><span data-stu-id="46ec7-127">Support content negotiation</span></span>

<span data-ttu-id="46ec7-128">回應訊息的內文可能包含各種格式的資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-128">The body of a response message may contain data in a variety of formats.</span></span> <span data-ttu-id="46ec7-129">例如，HTTP GET 要求可傳回 JSON 或 XML 格式的資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-129">For example, an HTTP GET request could return data in JSON, or XML format.</span></span> <span data-ttu-id="46ec7-130">當用戶端提交要求時，該要求可包含 Accept 標頭以指定可處理的資料格式。</span><span class="sxs-lookup"><span data-stu-id="46ec7-130">When the client submits a request, it can include an Accept header that specifies the data formats that it can handle.</span></span> <span data-ttu-id="46ec7-131">這些格式會被指定為媒體類型。</span><span class="sxs-lookup"><span data-stu-id="46ec7-131">These formats are specified as media types.</span></span> <span data-ttu-id="46ec7-132">例如，發出 GET 要求以擷取映像的用戶端可以指定 Accept 標頭，其中列出用戶端可處理的媒體類型，例如 `image/jpeg, image/gif, image/png`。</span><span class="sxs-lookup"><span data-stu-id="46ec7-132">For example, a client that issues a GET request that retrieves an image can specify an Accept header that lists the media types that the client can handle, such as `image/jpeg, image/gif, image/png`.</span></span> <span data-ttu-id="46ec7-133">當 Web API 傳回結果時，它應該使用其中一種媒體類型來格式化資料，並在回應的 Content-Type 標頭中指定格式。</span><span class="sxs-lookup"><span data-stu-id="46ec7-133">When the web API returns the result, it should format the data by using one of these media types and specify the format in the Content-Type header of the response.</span></span>

<span data-ttu-id="46ec7-134">如果用戶端未指定 Accept 標頭，則對回應內文使用合理的預設格式。</span><span class="sxs-lookup"><span data-stu-id="46ec7-134">If the client does not specify an Accept header, then use a sensible default format for the response body.</span></span> <span data-ttu-id="46ec7-135">例如，對於以文字為基礎的資料，ASP.NET Web API 架構會預設為 JSON。</span><span class="sxs-lookup"><span data-stu-id="46ec7-135">As an example, the ASP.NET Web API framework defaults to JSON for text-based data.</span></span>

### <a name="provide-links-to-support-hateoas-style-navigation-and-discovery-of-resources"></a><span data-ttu-id="46ec7-136">提供連結，以支援資源的 HATEOAS 式導覽和探索</span><span class="sxs-lookup"><span data-stu-id="46ec7-136">Provide links to support HATEOAS-style navigation and discovery of resources</span></span>

<span data-ttu-id="46ec7-137">HATEOAS 方法如何讓用戶端能夠從初始的起點瀏覽及探索資源。</span><span class="sxs-lookup"><span data-stu-id="46ec7-137">The HATEOAS approach enables a client to navigate and discover resources from an initial starting point.</span></span> <span data-ttu-id="46ec7-138">使用包含 URI 的連結即可達到此目的；當用戶端發出 HTTP GET 要求來取得資源時，回應應包含可讓用戶端應用程式快速找出任何直接相關資源的 URI。</span><span class="sxs-lookup"><span data-stu-id="46ec7-138">This is achieved by using links containing URIs; when a client issues an HTTP GET request to obtain a resource, the response should contain URIs that enable a client application to quickly locate any directly related resources.</span></span> <span data-ttu-id="46ec7-139">例如，在支援電子商務解決方案的 Web API 中，客戶可能已下了許多訂單。</span><span class="sxs-lookup"><span data-stu-id="46ec7-139">For example, in a web API that supports an e-commerce solution, a customer may have placed many orders.</span></span> <span data-ttu-id="46ec7-140">當用戶端應用程式擷取客戶的詳細資料時，回應應包含相關連結，讓用戶端應用程式傳送可擷取這些訂單的 HTTP GET 要求。</span><span class="sxs-lookup"><span data-stu-id="46ec7-140">When a client application retrieves the details for a customer, the response should include links that enable the client application to send HTTP GET requests that can retrieve these orders.</span></span> <span data-ttu-id="46ec7-141">此外，HATEOAS 式連結應描述每個連結資源支援的其他作業 (POST、PUT、DELETE 等等)，以及用來執行每個要求的對應 URI。</span><span class="sxs-lookup"><span data-stu-id="46ec7-141">Additionally, HATEOAS-style links should describe the other operations (POST, PUT, DELETE, and so on) that each linked resource supports together with the corresponding URI to perform each request.</span></span> <span data-ttu-id="46ec7-142">這種方法會在 [API 設計][api-design]中詳加說明。</span><span class="sxs-lookup"><span data-stu-id="46ec7-142">This approach is described in more detail in [API Design][api-design].</span></span>

<span data-ttu-id="46ec7-143">目前沒有標準可掌管 HATEOAS 的實作，但下列範例說明一個可能的方法。</span><span class="sxs-lookup"><span data-stu-id="46ec7-143">Currently there are no standards that govern the implementation of HATEOAS, but the following example illustrates one possible approach.</span></span> <span data-ttu-id="46ec7-144">在此範例中，尋找客戶詳細資料的 HTTP GET 要求會傳回包含 HATEOAS 連結 (用以參考該客戶的訂單) 的回應：</span><span class="sxs-lookup"><span data-stu-id="46ec7-144">In this example, an HTTP GET request that finds the details for a customer returns a response that include HATEOAS links that reference the orders for that customer:</span></span>

```HTTP
GET https://adventure-works.com/customers/2 HTTP/1.1
Accept: text/json
...
```

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"CustomerID":2,"CustomerName":"Bert","Links":[
    {"rel":"self",
    "href":"https://adventure-works.com/customers/2",
    "action":"GET",
    "types":["text/xml","application/json"]},
    {"rel":"self",
    "href":"https://adventure-works.com/customers/2",
    "action":"PUT",
    "types":["application/x-www-form-urlencoded"]},
    {"rel":"self",
    "href":"https://adventure-works.com/customers/2",
    "action":"DELETE",
    "types":[]},
    {"rel":"orders",
    "href":"https://adventure-works.com/customers/2/orders",
    "action":"GET",
    "types":["text/xml","application/json"]},
    {"rel":"orders",
    "href":"https://adventure-works.com/customers/2/orders",
    "action":"POST",
    "types":["application/x-www-form-urlencoded"]}
]}
```

<span data-ttu-id="46ec7-145">在此範例中，客戶資料是由下列程式碼片段中顯示的 `Customer` 類別表示。</span><span class="sxs-lookup"><span data-stu-id="46ec7-145">In this example, the customer data is represented by the `Customer` class shown in the following code snippet.</span></span> <span data-ttu-id="46ec7-146">HATEOAS 連結會保留在 `Links` 集合屬性中：</span><span class="sxs-lookup"><span data-stu-id="46ec7-146">The HATEOAS links are held in the `Links` collection property:</span></span>

```csharp
public class Customer
{
    public int CustomerID { get; set; }
    public string CustomerName { get; set; }
    public List<Link> Links { get; set; }
    ...
}

public class Link
{
    public string Rel { get; set; }
    public string Href { get; set; }
    public string Action { get; set; }
    public string [] Types { get; set; }
}
```

<span data-ttu-id="46ec7-147">HTTP GET 作業會擷取儲存體中的客戶資料並建構 `Customer` 物件，然後再填入 `Links` 集合。</span><span class="sxs-lookup"><span data-stu-id="46ec7-147">The HTTP GET operation retrieves the customer data from storage and constructs a `Customer` object, and then populates the `Links` collection.</span></span> <span data-ttu-id="46ec7-148">結果會格式化為 JSON 回應訊息。</span><span class="sxs-lookup"><span data-stu-id="46ec7-148">The result is formatted as a JSON response message.</span></span> <span data-ttu-id="46ec7-149">每個連結都包含下列欄位：</span><span class="sxs-lookup"><span data-stu-id="46ec7-149">Each link comprises the following fields:</span></span>

- <span data-ttu-id="46ec7-150">要傳回的物件與連結所描述的物件之間的關係。</span><span class="sxs-lookup"><span data-stu-id="46ec7-150">The relationship between the object being returned and the object described by the link.</span></span> <span data-ttu-id="46ec7-151">在此情況下，`self` 表示連結是物件本身的參考 (類似於許多物件導向語言中的 `this` 指標)，而 `orders` 是包含相關訂單資訊的集合名稱。</span><span class="sxs-lookup"><span data-stu-id="46ec7-151">In this case `self` indicates that the link is a reference back to the object itself (similar to a `this` pointer in many object-oriented languages), and `orders` is the name of a collection containing the related order information.</span></span>
- <span data-ttu-id="46ec7-152">連結所描述物件的超連結 (`Href`) (採用 URI 形式)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-152">The hyperlink (`Href`) for the object being described by the link in the form of a URI.</span></span>
- <span data-ttu-id="46ec7-153">可傳送至此 URI 之 HTTP 要求的類型 (`Action`)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-153">The type of HTTP request (`Action`) that can be sent to this URI.</span></span>
- <span data-ttu-id="46ec7-154">視要求的類型而定，應在 HTTP 要求中提供或可在回應中傳回之任何資料的格式 (`Types`)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-154">The format of any data (`Types`) that should be provided in the HTTP request or that can be returned in the response, depending on the type of the request.</span></span>

<span data-ttu-id="46ec7-155">範例 HTTP 回應中所示的 HATEOAS 連結表示用戶端應用程式可以執行下列作業：</span><span class="sxs-lookup"><span data-stu-id="46ec7-155">The HATEOAS links shown in the example HTTP response indicate that a client application can perform the following operations:</span></span>

- <span data-ttu-id="46ec7-156">對 URI `https://adventure-works.com/customers/2` 的 HTTP GET 要求，用以 (再次) 擷取客戶的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-156">An HTTP GET request to the URI `https://adventure-works.com/customers/2` to fetch the details of the customer (again).</span></span> <span data-ttu-id="46ec7-157">此資料可以 XML 或 JSON 格式傳回。</span><span class="sxs-lookup"><span data-stu-id="46ec7-157">The data can be returned as XML or JSON.</span></span>
- <span data-ttu-id="46ec7-158">對 URI `https://adventure-works.com/customers/2` 的 HTTP PUT 要求，用以修改客戶的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-158">An HTTP PUT request to the URI `https://adventure-works.com/customers/2` to modify the details of the customer.</span></span> <span data-ttu-id="46ec7-159">新資料必須在要求訊息中以 x-www-form-urlencoded 格式提供。</span><span class="sxs-lookup"><span data-stu-id="46ec7-159">The new data must be provided in the request message in x-www-form-urlencoded format.</span></span>
- <span data-ttu-id="46ec7-160">對 URI `https://adventure-works.com/customers/2` 的 HTTP DELETE 要求，用以刪除客戶。</span><span class="sxs-lookup"><span data-stu-id="46ec7-160">An HTTP DELETE request to the URI `https://adventure-works.com/customers/2` to delete the customer.</span></span> <span data-ttu-id="46ec7-161">此要求並不預期有任何其他資訊或在回應訊息內文中傳回資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-161">The request does not expect any additional information or return data in the response message body.</span></span>
- <span data-ttu-id="46ec7-162">對 URI `https://adventure-works.com/customers/2/orders` 的 HTTP GET 要求，用以尋找客戶的所有訂單。</span><span class="sxs-lookup"><span data-stu-id="46ec7-162">An HTTP GET request to the URI `https://adventure-works.com/customers/2/orders` to find all the orders for the customer.</span></span> <span data-ttu-id="46ec7-163">此資料可以 XML 或 JSON 格式傳回。</span><span class="sxs-lookup"><span data-stu-id="46ec7-163">The data can be returned as XML or JSON.</span></span>
- <span data-ttu-id="46ec7-164">對 URI `https://adventure-works.com/customers/2/orders` 的 HTTP PUT 要求，用以建立此客戶的新訂單。</span><span class="sxs-lookup"><span data-stu-id="46ec7-164">An HTTP PUT request to the URI `https://adventure-works.com/customers/2/orders` to create a new order for this customer.</span></span> <span data-ttu-id="46ec7-165">此資料必須在要求訊息中以 x-www-form-urlencoded 格式提供。</span><span class="sxs-lookup"><span data-stu-id="46ec7-165">The data must be provided in the request message in x-www-form-urlencoded format.</span></span>

## <a name="handling-exceptions"></a><span data-ttu-id="46ec7-166">處理例外狀況</span><span class="sxs-lookup"><span data-stu-id="46ec7-166">Handling exceptions</span></span>

<span data-ttu-id="46ec7-167">如果作業會擲回無法攔截的例外狀況，請考慮下列各點。</span><span class="sxs-lookup"><span data-stu-id="46ec7-167">Consider the following points if an operation throws an uncaught exception.</span></span>

### <a name="capture-exceptions-and-return-a-meaningful-response-to-clients"></a><span data-ttu-id="46ec7-168">擷取例外狀況，並將有意義的回應傳回給用戶端</span><span class="sxs-lookup"><span data-stu-id="46ec7-168">Capture exceptions and return a meaningful response to clients</span></span>

<span data-ttu-id="46ec7-169">實作 HTTP 作業的程式碼應提供完善的例外狀況處理，而不是讓未攔截到的例外狀況傳播到架構。</span><span class="sxs-lookup"><span data-stu-id="46ec7-169">The code that implements an HTTP operation should provide comprehensive exception handling rather than letting uncaught exceptions propagate to the framework.</span></span> <span data-ttu-id="46ec7-170">如果例外狀況使作業無法成功完成，可以在回應訊息中傳回例外狀況，但應包含造成例外狀況之錯誤的有意義說明。</span><span class="sxs-lookup"><span data-stu-id="46ec7-170">If an exception makes it impossible to complete the operation successfully, the exception can be passed back in the response message, but it should include a meaningful description of the error that caused the exception.</span></span> <span data-ttu-id="46ec7-171">例外狀況也應包含適當的 HTTP 狀態碼，而不只是對每種狀況傳回狀態碼 500。</span><span class="sxs-lookup"><span data-stu-id="46ec7-171">The exception should also include the appropriate HTTP status code rather than simply returning status code 500 for every situation.</span></span> <span data-ttu-id="46ec7-172">例如，如果使用者要求造成違反條件約束的資料庫更新 (例如嘗試刪除有未處理訂單的客戶)，您應傳回狀態碼 409 (衝突) 以及可指出衝突原因的訊息內本。</span><span class="sxs-lookup"><span data-stu-id="46ec7-172">For example, if a user request causes a database update that violates a constraint (such as attempting to delete a customer that has outstanding orders), you should return status code 409 (Conflict) and a message body indicating the reason for the conflict.</span></span> <span data-ttu-id="46ec7-173">如果某些其他條件呈現要求無法達成，您可以傳回狀態碼 400 (不正確的要求)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-173">If some other condition renders the request unachievable, you can return status code 400 (Bad Request).</span></span> <span data-ttu-id="46ec7-174">您可以在 W3C 網站上的 [狀態碼定義](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 頁面找到 HTTP 狀態碼的完整清單。</span><span class="sxs-lookup"><span data-stu-id="46ec7-174">You can find a full list of HTTP status codes on the [Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) page on the W3C website.</span></span>

<span data-ttu-id="46ec7-175">此程式碼範例會設陷不同的條件，並傳回適當的回應。</span><span class="sxs-lookup"><span data-stu-id="46ec7-175">The code example traps different conditions and returns an appropriate response.</span></span>

```csharp
[HttpDelete]
[Route("customers/{id:int}")]
public IHttpActionResult DeleteCustomer(int id)
{
    try
    {
        // Find the customer to be deleted in the repository
        var customerToDelete = repository.GetCustomer(id);

        // If there is no such customer, return an error response
        // with status code 404 (Not Found)
        if (customerToDelete == null)
        {
                return NotFound();
        }

        // Remove the customer from the repository
        // The DeleteCustomer method returns true if the customer
        // was successfully deleted
        if (repository.DeleteCustomer(id))
        {
            // Return a response message with status code 204 (No Content)
            // To indicate that the operation was successful
            return StatusCode(HttpStatusCode.NoContent);
        }
        else
        {
            // Otherwise return a 400 (Bad Request) error response
            return BadRequest(Strings.CustomerNotDeleted);
        }
    }
    catch
    {
        // If an uncaught exception occurs, return an error response
        // with status code 500 (Internal Server Error)
        return InternalServerError();
    }
}
```

> [!TIP]
> <span data-ttu-id="46ec7-176">請勿包含對嘗試侵入您的 API 攻擊者可能有幫助的資訊。</span><span class="sxs-lookup"><span data-stu-id="46ec7-176">Do not include information that could be useful to an attacker attempting to penetrate your API.</span></span>
  
<span data-ttu-id="46ec7-177">許多 Web 伺服器會在連上 Web API 之前，自行設陷錯誤條件。</span><span class="sxs-lookup"><span data-stu-id="46ec7-177">Many web servers trap error conditions themselves before they reach the web API.</span></span> <span data-ttu-id="46ec7-178">例如，如果您設定網站的驗證，而使用者無法提供正確的驗證資訊，Web 伺服器應該以狀態碼 401 (未經授權) 回應。</span><span class="sxs-lookup"><span data-stu-id="46ec7-178">For example, if you configure authentication for a web site and the user fails to provide the correct authentication information, the web server should respond with status code 401 (Unauthorized).</span></span> <span data-ttu-id="46ec7-179">用戶端經過驗證後，您的程式碼可以執行自己的檢查，確認用戶端應能夠存取所要求的資源。</span><span class="sxs-lookup"><span data-stu-id="46ec7-179">Once a client has been authenticated, your code can perform its own checks to verify that the client should be able access the requested resource.</span></span> <span data-ttu-id="46ec7-180">如果此授權失敗，您應該傳回狀態碼 403 (禁止)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-180">If this authorization fails, you should return status code 403 (Forbidden).</span></span>

### <a name="handle-exceptions-consistently-and-log-information-about-errors"></a><span data-ttu-id="46ec7-181">以一致的方式處理例外狀況並記錄有關錯誤的資訊</span><span class="sxs-lookup"><span data-stu-id="46ec7-181">Handle exceptions consistently and log information about errors</span></span>

<span data-ttu-id="46ec7-182">若要以一致的方式處理例外狀況，請考慮在整個 Web API 實作全域錯誤處理策略。</span><span class="sxs-lookup"><span data-stu-id="46ec7-182">To handle exceptions in a consistent manner, consider implementing a global error handling strategy across the entire web API.</span></span> <span data-ttu-id="46ec7-183">您也應該併入錯誤記錄，以擷取每種例外狀況的完整詳細資料；此錯誤記錄檔可包含詳細的資訊 (只要用戶端無法透過 Web 存取即可)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-183">You should also incorporate error logging which captures the full details of each exception; this error log can contain detailed information as long as it is not made accessible over the web to clients.</span></span>

### <a name="distinguish-between-client-side-errors-and-server-side-errors"></a><span data-ttu-id="46ec7-184">區分用戶端錯誤與伺服器端錯誤</span><span class="sxs-lookup"><span data-stu-id="46ec7-184">Distinguish between client-side errors and server-side errors</span></span>

<span data-ttu-id="46ec7-185">HTTP 通訊協定會區分用戶端應用程式 (HTTP 4xx 狀態碼) 所引起的錯誤，與伺服器災難所造成的錯誤 (HTTP 5xx 狀態碼)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-185">The HTTP protocol distinguishes between errors that occur due to the client application (the HTTP 4xx status codes), and errors that are caused by a mishap on the server (the HTTP 5xx status codes).</span></span> <span data-ttu-id="46ec7-186">請務必遵守任何錯誤回應訊息中的這個慣例。</span><span class="sxs-lookup"><span data-stu-id="46ec7-186">Make sure that you respect this convention in any error response messages.</span></span>

## <a name="optimizing-client-side-data-access"></a><span data-ttu-id="46ec7-187">將用戶端資料存取最佳化</span><span class="sxs-lookup"><span data-stu-id="46ec7-187">Optimizing client-side data access</span></span>

<span data-ttu-id="46ec7-188">在涉及 Web 伺服器和用戶端應用程式的分散式環境中，網路是其中一個主要憂心來源。</span><span class="sxs-lookup"><span data-stu-id="46ec7-188">In a distributed environment such as that involving a web server and client applications, one of the primary sources of concern is the network.</span></span> <span data-ttu-id="46ec7-189">這可能會是相當大的瓶頸，特別是在用戶端應用程式經常傳送要求或接收資料時。</span><span class="sxs-lookup"><span data-stu-id="46ec7-189">This can act as a considerable bottleneck, especially if a client application is frequently sending requests or receiving data.</span></span> <span data-ttu-id="46ec7-190">因此，您的目標應該在將網路的傳輸流量降到最低。</span><span class="sxs-lookup"><span data-stu-id="46ec7-190">Therefore you should aim to minimize the amount of traffic that flows across the network.</span></span> <span data-ttu-id="46ec7-191">當您實作程式碼來擷取和維護資料時，請考慮下列幾點：</span><span class="sxs-lookup"><span data-stu-id="46ec7-191">Consider the following points when you implement the code to retrieve and maintain data:</span></span>

### <a name="support-client-side-caching"></a><span data-ttu-id="46ec7-192">支援用戶端快取</span><span class="sxs-lookup"><span data-stu-id="46ec7-192">Support client-side caching</span></span>

<span data-ttu-id="46ec7-193">HTTP 1.1 通訊協定支援在用戶端和中繼伺服器中的快取，要求會利用 Cache-Control 標頭透過它路由傳送。</span><span class="sxs-lookup"><span data-stu-id="46ec7-193">The HTTP 1.1 protocol supports caching in clients and intermediate servers through which a request is routed by the use of the Cache-Control header.</span></span> <span data-ttu-id="46ec7-194">當用戶端應用程式將 HTTP GET 要求傳送至 Web API，回應可以包含 Cache-Control 標頭，指出此用戶端或用以路由傳送此要求的中繼伺服器是否可以安全地快取回應內文中的資料，以及它多久後會到期並視為過期。</span><span class="sxs-lookup"><span data-stu-id="46ec7-194">When a client application sends an HTTP GET request to the web API, the response can include a Cache-Control header that indicates whether the data in the body of the response can be safely cached by the client or an intermediate server through which the request has been routed, and for how long before it should expire and be considered out-of-date.</span></span> <span data-ttu-id="46ec7-195">下列範例會顯示 HTTP GET 要求和包含 Cache-Control 標頭的對應回應：</span><span class="sxs-lookup"><span data-stu-id="46ec7-195">The following example shows an HTTP GET request and the corresponding response that includes a Cache-Control header:</span></span>

```HTTP
GET https://adventure-works.com/orders/2 HTTP/1.1
```

```HTTP
HTTP/1.1 200 OK
...
Cache-Control: max-age=600, private
Content-Type: text/json; charset=utf-8
Content-Length: ...
{"orderID":2,"productID":4,"quantity":2,"orderValue":10.00}
```

<span data-ttu-id="46ec7-196">在此範例中，Cache-Control 指定傳回的資料應在 600 秒後過期，僅適用於單一用戶端且不得儲存在其他用戶端所使用的共用快取中 (它是 private)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-196">In this example, the Cache-Control header specifies that the data returned should be expired after 600 seconds, and is only suitable for a single client and must not be stored in a shared cache used by other clients (it is *private*).</span></span> <span data-ttu-id="46ec7-197">Cache-Control 標頭可以指定 public 而非 private，在此情況下資料可以儲存在共用快取中，也可以指定 no-store，在此情況下資料必須**不是**由用戶端快取。</span><span class="sxs-lookup"><span data-stu-id="46ec7-197">The Cache-Control header could specify *public* rather than *private* in which case the data can be stored in a shared cache, or it could specify *no-store* in which case the data must **not** be cached by the client.</span></span> <span data-ttu-id="46ec7-198">下列程式碼範例示範如何在回應訊息中建構 Cache-Control 標頭：</span><span class="sxs-lookup"><span data-stu-id="46ec7-198">The following code example shows how to construct a Cache-Control header in a response message:</span></span>

```csharp
public class OrdersController : ApiController
{
    ...
    [Route("api/orders/{id:int:min(0)}")]
    [HttpGet]
    public IHttpActionResult FindOrderByID(int id)
    {
        // Find the matching order
        Order order = ...;
        ...
        // Create a Cache-Control header for the response
        var cacheControlHeader = new CacheControlHeaderValue();
        cacheControlHeader.Private = true;
        cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);
        ...

        // Return a response message containing the order and the cache control header
        OkResultWithCaching<Order> response = new OkResultWithCaching<Order>(order, this)
        {
            CacheControlHeader = cacheControlHeader
        };
        return response;
    }
    ...
}
```

<span data-ttu-id="46ec7-199">此程式碼會利用名為 `OkResultWithCaching` 的自訂 `IHttpActionResult` 類別。</span><span class="sxs-lookup"><span data-stu-id="46ec7-199">This code makes use of a custom `IHttpActionResult` class named `OkResultWithCaching`.</span></span> <span data-ttu-id="46ec7-200">這個類別可讓控制器設定快取標頭內容：</span><span class="sxs-lookup"><span data-stu-id="46ec7-200">This class enables the controller to set the cache header contents:</span></span>

```csharp
public class OkResultWithCaching<T> : OkNegotiatedContentResult<T>
{
    public OkResultWithCaching(T content, ApiController controller)
        : base(content, controller) { }

    public OkResultWithCaching(T content, IContentNegotiator contentNegotiator, HttpRequestMessage request, IEnumerable<MediaTypeFormatter> formatters)
        : base(content, contentNegotiator, request, formatters) { }

    public CacheControlHeaderValue CacheControlHeader { get; set; }
    public EntityTagHeaderValue ETag { get; set; }

    public override async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
    {
        HttpResponseMessage response;
        try
        {
            response = await base.ExecuteAsync(cancellationToken);
            response.Headers.CacheControl = this.CacheControlHeader;
            response.Headers.ETag = ETag;
        }
        catch (OperationCanceledException)
        {
            response = new HttpResponseMessage(HttpStatusCode.Conflict) {ReasonPhrase = "Operation was cancelled"};
        }
        return response;
    }
}
```

> [!NOTE]
> <span data-ttu-id="46ec7-201">HTTP 通訊協定也會定義 Cache-Control 標頭的 *no-cache* 指示詞。</span><span class="sxs-lookup"><span data-stu-id="46ec7-201">The HTTP protocol also defines the *no-cache* directive for the Cache-Control header.</span></span> <span data-ttu-id="46ec7-202">令人不解的是，此指示詞並非表示「不快取 」，而是「在傳回快取的資訊前使用伺服器進行重新驗證」；此資料仍會快取，但每次用來確保其仍為當前資料時會進行檢查。</span><span class="sxs-lookup"><span data-stu-id="46ec7-202">Rather confusingly, this directive does not mean "do not cache" but rather "revalidate the cached information with the server before returning it"; the data can still be cached, but it is checked each time it is used to ensure that it is still current.</span></span>

<span data-ttu-id="46ec7-203">快取管理是用戶端應用程式或中繼伺服器的責任，但若已正確實作，則可藉由移除提取最近擷取之資料的需求來節省頻寬及改善效能。</span><span class="sxs-lookup"><span data-stu-id="46ec7-203">Cache management is the responsibility of the client application or intermediate server, but if properly implemented it can save bandwidth and improve performance by removing the need to fetch data that has already been recently retrieved.</span></span>

<span data-ttu-id="46ec7-204">Cache-Control 標頭中的 max-age 值只是指南，但不保證對應的資料在指定的時間內不會變更。</span><span class="sxs-lookup"><span data-stu-id="46ec7-204">The *max-age* value in the Cache-Control header is only a guide and not a guarantee that the corresponding data won't change during the specified time.</span></span> <span data-ttu-id="46ec7-205">Web API 應該根據預期的資料變動性，將 max-age 設定為適當的值。</span><span class="sxs-lookup"><span data-stu-id="46ec7-205">The web API should set the max-age to a suitable value depending on the expected volatility of the data.</span></span> <span data-ttu-id="46ec7-206">在此期間過期時，用戶端應捨棄快取中的物件。</span><span class="sxs-lookup"><span data-stu-id="46ec7-206">When this period expires, the client should discard the object from the cache.</span></span>

> [!NOTE]
> <span data-ttu-id="46ec7-207">大部分的新式網頁瀏覽器都支援用戶端快取，其做法是將適當的 cache-control 標頭加入至要求，並檢查結果的標頭。</span><span class="sxs-lookup"><span data-stu-id="46ec7-207">Most modern web browsers support client-side caching by adding the appropriate cache-control headers to requests and examining the headers of the results, as described.</span></span> <span data-ttu-id="46ec7-208">不過，有些較舊的瀏覽器不會快取從包含查詢字串的 URL 傳回的值。</span><span class="sxs-lookup"><span data-stu-id="46ec7-208">However, some older browsers will not cache the values returned from a URL that includes a query string.</span></span> <span data-ttu-id="46ec7-209">對於根據此處討論的通訊協定，實作自己的快取管理策略的自訂用戶端應用程式而言，這通常不是問題。</span><span class="sxs-lookup"><span data-stu-id="46ec7-209">This is not usually an issue for custom client applications which implement their own cache management strategy based on the protocol discussed here.</span></span>
>
> <span data-ttu-id="46ec7-210">某些較舊的 Proxy 會出現相同的行為，而且可能不會根據包含查詢字串的 URL 快取要求。</span><span class="sxs-lookup"><span data-stu-id="46ec7-210">Some older proxies exhibit the same behavior and might not cache requests based on URLs with query strings.</span></span> <span data-ttu-id="46ec7-211">對於透過這類 Proxy 連接到 Web 伺服器的自訂用戶端應用程式而言，這可能會是個問題。</span><span class="sxs-lookup"><span data-stu-id="46ec7-211">This could be an issue for custom client applications that connect to a web server through such a proxy.</span></span>

### <a name="provide-etags-to-optimize-query-processing"></a><span data-ttu-id="46ec7-212">提供 Etag 來最佳化查詢處理</span><span class="sxs-lookup"><span data-stu-id="46ec7-212">Provide ETags to optimize query processing</span></span>

<span data-ttu-id="46ec7-213">當用戶端應用程式擷取物件時，回應訊息也可以包含 ETag (實體標記)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-213">When a client application retrieves an object, the response message can also include an *ETag* (Entity Tag).</span></span> <span data-ttu-id="46ec7-214">ETag 是不透明的字串，可指出資源的版本；每次資源變更時，也會修改 Etag。</span><span class="sxs-lookup"><span data-stu-id="46ec7-214">An ETag is an opaque string that indicates the version of a resource; each time a resource changes the Etag is also modified.</span></span> <span data-ttu-id="46ec7-215">用戶端應用程式應將此 ETag 當作資料的一部分快取。</span><span class="sxs-lookup"><span data-stu-id="46ec7-215">This ETag should be cached as part of the data by the client application.</span></span> <span data-ttu-id="46ec7-216">下列程式碼範例示範如何加入 ETag 作為 HTTP GET 要求之回應的一部分。</span><span class="sxs-lookup"><span data-stu-id="46ec7-216">The following code example shows how to add an ETag as part of the response to an HTTP GET request.</span></span> <span data-ttu-id="46ec7-217">此程式碼使用物件的 `GetHashCode` 方法來產生數值，以識別此物件 (您可以視需要覆寫此方法，並使用諸如 MD5 的演算法來產生自己的雜湊)：</span><span class="sxs-lookup"><span data-stu-id="46ec7-217">This code uses the `GetHashCode` method of an object to generate a numeric value that identifies the object (you can override this method if necessary and generate your own hash using an algorithm such as MD5) :</span></span>

```csharp
public class OrdersController : ApiController
{
    ...
    public IHttpActionResult FindOrderByID(int id)
    {
        // Find the matching order
        Order order = ...;
        ...

        var hashedOrder = order.GetHashCode();
        string hashedOrderEtag = $"\"{hashedOrder}\"";
        var eTag = new EntityTagHeaderValue(hashedOrderEtag);

        // Return a response message containing the order and the cache control header
        OkResultWithCaching<Order> response = new OkResultWithCaching<Order>(order, this)
        {
            ...,
            ETag = eTag
        };
        return response;
    }
    ...
}
```

<span data-ttu-id="46ec7-218">Web API 所張貼的回應訊息看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="46ec7-218">The response message posted by the web API looks like this:</span></span>

```HTTP
HTTP/1.1 200 OK
...
Cache-Control: max-age=600, private
Content-Type: text/json; charset=utf-8
ETag: "2147483648"
Content-Length: ...
{"orderID":2,"productID":4,"quantity":2,"orderValue":10.00}
```

> [!TIP]
> <span data-ttu-id="46ec7-219">基於安全性理由，不允許快取機密資料或透過已驗證 (HTTPS) 連線傳回的資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-219">For security reasons, do not allow sensitive data or data returned over an authenticated (HTTPS) connection to be cached.</span></span>

<span data-ttu-id="46ec7-220">用戶端應用程式可以發出後續的 GET 要求以便隨時擷取相同的資源，而如果資源已變更 (有不同的 ETag)，則應捨棄快取的版本並在快取中加入新的版本。</span><span class="sxs-lookup"><span data-stu-id="46ec7-220">A client application can issue a subsequent GET request to retrieve the same resource at any time, and if the resource has changed (it has a different ETag) the cached version should be discarded and the new version added to the cache.</span></span> <span data-ttu-id="46ec7-221">如果資源很大且需要大量頻寬才能傳送回用戶端，提取相同資料的重複要求可能沒有效率。</span><span class="sxs-lookup"><span data-stu-id="46ec7-221">If a resource is large and requires a significant amount of bandwidth to transmit back to the client, repeated requests to fetch the same data can become inefficient.</span></span> <span data-ttu-id="46ec7-222">為了克服這點，HTTP 通訊協定定義了下列程序，以供最佳化您應在 Web API 中支援的 GET 要求：</span><span class="sxs-lookup"><span data-stu-id="46ec7-222">To combat this, the HTTP protocol defines the following process for optimizing GET requests that you should support in a web API:</span></span>

- <span data-ttu-id="46ec7-223">用戶端會建構一個 GET 要求，其中包含 If-None-Match HTTP 標頭中參考之資源的目前快取版本的 ETag：</span><span class="sxs-lookup"><span data-stu-id="46ec7-223">The client constructs a GET request containing the ETag for the currently cached version of the resource referenced in an If-None-Match HTTP header:</span></span>

    ```HTTP
    GET https://adventure-works.com/orders/2 HTTP/1.1
    If-None-Match: "2147483648"
    ```

- <span data-ttu-id="46ec7-224">Web API 中的 GET 作業可取得所要求資料的目前 ETag (上述範例中的 order 2)，並將它與 If-None-Match 標頭中的值比較。</span><span class="sxs-lookup"><span data-stu-id="46ec7-224">The GET operation in the web API obtains the current ETag for the requested data (order 2 in the above example), and compares it to the value in the If-None-Match header.</span></span>

- <span data-ttu-id="46ec7-225">如果所要求資料的目前 ETag 符合要求所提供的 ETag，則資源尚未變更，且 Web API 應傳回具有空白訊息內文和狀態碼 304 (未修改) 的 HTTP 回應。</span><span class="sxs-lookup"><span data-stu-id="46ec7-225">If the current ETag for the requested data matches the ETag provided by the request, the resource has not changed and the web API should return an HTTP response with an empty message body and a status code of 304 (Not Modified).</span></span>

- <span data-ttu-id="46ec7-226">如果所要求資料的目前 ETag 不符合要求所提供的 ETag，則資料已經變更，且 Web API 應傳回訊息內文有新資料且狀態碼為 200 (確定) 的 HTTP 回應。</span><span class="sxs-lookup"><span data-stu-id="46ec7-226">If the current ETag for the requested data does not match the ETag provided by the request, then the data has changed and the web API should return an HTTP response with the new data in the message body and a status code of 200 (OK).</span></span>

- <span data-ttu-id="46ec7-227">如果要求的資料已不存在，則 Web API 應傳回具有狀態碼 404 (找不到) 的 HTTP 回應。</span><span class="sxs-lookup"><span data-stu-id="46ec7-227">If the requested data no longer exists then the web API should return an HTTP response with the status code of 404 (Not Found).</span></span>

- <span data-ttu-id="46ec7-228">用戶端會使用狀態碼來維護快取。</span><span class="sxs-lookup"><span data-stu-id="46ec7-228">The client uses the status code to maintain the cache.</span></span> <span data-ttu-id="46ec7-229">如果資料尚未變更 (狀態碼 304)，則物件可維持快取狀態，而用戶端應用程式應繼續使用此版本的物件。</span><span class="sxs-lookup"><span data-stu-id="46ec7-229">If the data has not changed (status code 304) then the object can remain cached and the client application should continue to use this version of the object.</span></span> <span data-ttu-id="46ec7-230">如果資料已經變更 (狀態碼 200)，則應捨棄快取的物件並插入新物件。</span><span class="sxs-lookup"><span data-stu-id="46ec7-230">If the data has changed (status code 200) then the cached object should be discarded and the new one inserted.</span></span> <span data-ttu-id="46ec7-231">如果資料已無法使用 (狀態碼 404)，則應從快取中移除此物件。</span><span class="sxs-lookup"><span data-stu-id="46ec7-231">If the data is no longer available (status code 404) then the object should be removed from the cache.</span></span>

> [!NOTE]
> <span data-ttu-id="46ec7-232">如果回應標頭包含 Cache-Control 標頭 no-store，則應一律從快取中移除此物件 (不論 HTTP 狀態碼為何)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-232">If the response header contains the Cache-Control header no-store then the object should always be removed from the cache regardless of the HTTP status code.</span></span>

<span data-ttu-id="46ec7-233">以下程式碼顯示擴充為支援 If-None-Match 標頭的 `FindOrderByID` 方法。</span><span class="sxs-lookup"><span data-stu-id="46ec7-233">The code below shows the `FindOrderByID` method extended to support the If-None-Match header.</span></span> <span data-ttu-id="46ec7-234">請注意，如果省略 If-None-Match 標頭，則一律會擷取指定的訂單：</span><span class="sxs-lookup"><span data-stu-id="46ec7-234">Notice that if the If-None-Match header is omitted, the specified order is always retrieved:</span></span>

```csharp
public class OrdersController : ApiController
{
    [Route("api/orders/{id:int:min(0)}")]
    [HttpGet]
    public IHttpActionResult FindOrderByID(int id)
    {
        try
        {
            // Find the matching order
            Order order = ...;

            // If there is no such order then return NotFound
            if (order == null)
            {
                return NotFound();
            }

            // Generate the ETag for the order
            var hashedOrder = order.GetHashCode();
            string hashedOrderEtag = $"\"{hashedOrder}\"";

            // Create the Cache-Control and ETag headers for the response
            IHttpActionResult response;
            var cacheControlHeader = new CacheControlHeaderValue();
            cacheControlHeader.Public = true;
            cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);
            var eTag = new EntityTagHeaderValue(hashedOrderEtag);

            // Retrieve the If-None-Match header from the request (if it exists)
            var nonMatchEtags = Request.Headers.IfNoneMatch;

            // If there is an ETag in the If-None-Match header and
            // this ETag matches that of the order just retrieved,
            // then create a Not Modified response message
            if (nonMatchEtags.Count > 0 &&
                String.CompareOrdinal(nonMatchEtags.First().Tag, hashedOrderEtag) == 0)
            {
                response = new EmptyResultWithCaching()
                {
                    StatusCode = HttpStatusCode.NotModified,
                    CacheControlHeader = cacheControlHeader,
                    ETag = eTag
                };
            }
            // Otherwise create a response message that contains the order details
            else
            {
                response = new OkResultWithCaching<Order>(order, this)
                {
                    CacheControlHeader = cacheControlHeader,
                    ETag = eTag
                };
            }

            return response;
        }
        catch
        {
            return InternalServerError();
        }
    }
...
}
```

<span data-ttu-id="46ec7-235">此範例併入名為 `EmptyResultWithCaching` 的其他自訂 `IHttpActionResult` 類別。</span><span class="sxs-lookup"><span data-stu-id="46ec7-235">This example incorporates an additional custom `IHttpActionResult` class named `EmptyResultWithCaching`.</span></span> <span data-ttu-id="46ec7-236">這個類別只是做為以 `HttpResponseMessage` 物件 (不包含回應主體) 為主的包裝函式：</span><span class="sxs-lookup"><span data-stu-id="46ec7-236">This class simply acts as a wrapper around an `HttpResponseMessage` object that does not contain a response body:</span></span>

```csharp
public class EmptyResultWithCaching : IHttpActionResult
{
    public CacheControlHeaderValue CacheControlHeader { get; set; }
    public EntityTagHeaderValue ETag { get; set; }
    public HttpStatusCode StatusCode { get; set; }
    public Uri Location { get; set; }

    public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
    {
        HttpResponseMessage response = new HttpResponseMessage(StatusCode);
        response.Headers.CacheControl = this.CacheControlHeader;
        response.Headers.ETag = this.ETag;
        response.Headers.Location = this.Location;
        return response;
    }
}
```

> [!TIP]
> <span data-ttu-id="46ec7-237">在此範例中，雜湊從基礎資料來源擷取的資料，可以產生資料的 ETag。</span><span class="sxs-lookup"><span data-stu-id="46ec7-237">In this example, the ETag for the data is generated by hashing the data retrieved from the underlying data source.</span></span> <span data-ttu-id="46ec7-238">如果以其他方式計算 ETag，則可進一步最佳化程序，而如果資料已經變更，只需從資料來源提取。</span><span class="sxs-lookup"><span data-stu-id="46ec7-238">If the ETag can be computed in some other way, then the process can be optimized further and the data only needs to be fetched from the data source if it has changed.</span></span> <span data-ttu-id="46ec7-239">如果是大型資料或存取資料來源可能導致明顯延遲 (例如，如果資料來源是遠端資料庫)，這個方法特別有用。</span><span class="sxs-lookup"><span data-stu-id="46ec7-239">This approach is especially useful if the data is large or accessing the data source can result in significant latency (for example, if the data source is a remote database).</span></span>

### <a name="use-etags-to-support-optimistic-concurrency"></a><span data-ttu-id="46ec7-240">使用 Etag 支援開放式並行存取</span><span class="sxs-lookup"><span data-stu-id="46ec7-240">Use ETags to Support Optimistic Concurrency</span></span>

<span data-ttu-id="46ec7-241">為了能夠進行先前快取資料的更新，HTTP 通訊協定支援開放式並行存取策略。</span><span class="sxs-lookup"><span data-stu-id="46ec7-241">To enable updates over previously cached data, the HTTP protocol supports an optimistic concurrency strategy.</span></span> <span data-ttu-id="46ec7-242">提取及快取資源之後，如果用戶端應用程式接著傳送 PUT 或 DELETE 要求來變更或移除資源，則應包含在參考 ETag 的 If-match 標頭中。</span><span class="sxs-lookup"><span data-stu-id="46ec7-242">If, after fetching and caching a resource, the client application subsequently sends a PUT or DELETE request to change or remove the resource, it should include in If-Match header that references the ETag.</span></span> <span data-ttu-id="46ec7-243">Web API 可接著使用此資訊來判斷資源在擷取後是否遭到另一位使用者變更，並將適當的回應傳送回用戶端應用程式，如下所示：</span><span class="sxs-lookup"><span data-stu-id="46ec7-243">The web API can then use this information to determine whether the resource has already been changed by another user since it was retrieved and send an appropriate response back to the client application as follows:</span></span>

- <span data-ttu-id="46ec7-244">用戶端會建構一個 PUT 要求，其中包含資源的新詳細資料及 If-Match HTTP 標頭中參考之資源的目前快取版本的 ETag：</span><span class="sxs-lookup"><span data-stu-id="46ec7-244">The client constructs a PUT request containing the new details for the resource and the ETag for the currently cached version of the resource referenced in an If-Match HTTP header.</span></span> <span data-ttu-id="46ec7-245">下列範例顯示更新訂單的 PUT 要求：</span><span class="sxs-lookup"><span data-stu-id="46ec7-245">The following example shows a PUT request that updates an order:</span></span>

    ```HTTP
    PUT https://adventure-works.com/orders/1 HTTP/1.1
    If-Match: "2282343857"
    Content-Type: application/x-www-form-urlencoded
    Content-Length: ...
    productID=3&quantity=5&orderValue=250
    ```

- <span data-ttu-id="46ec7-246">Web API 中的 PUT 作業可取得所要求資料的目前 ETag (上述範例中的 order 1)，並將它與 If-Match 標頭中的值比較。</span><span class="sxs-lookup"><span data-stu-id="46ec7-246">The PUT operation in the web API obtains the current ETag for the requested data (order 1 in the above example), and compares it to the value in the If-Match header.</span></span>

- <span data-ttu-id="46ec7-247">如果所要求資料的目前 ETag 符合要求所提供的 ETag，則資源尚未變更，且 Web API 應執行更新、傳回具有 HTTP 狀態碼 204 (沒有內容) 的訊息 (如果不成功的話)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-247">If the current ETag for the requested data matches the ETag provided by the request, the resource has not changed and the web API should perform the update, returning a message with HTTP status code 204 (No Content) if it is successful.</span></span> <span data-ttu-id="46ec7-248">對於更新的資源版本，回應可以包含 Cache-Control 和 ETag 標頭。</span><span class="sxs-lookup"><span data-stu-id="46ec7-248">The response can include Cache-Control and ETag headers for the updated version of the resource.</span></span> <span data-ttu-id="46ec7-249">回應應一率包含 Location 標頭，以參考最近更新的資源 URI。</span><span class="sxs-lookup"><span data-stu-id="46ec7-249">The response should always include the Location header that references the URI of the newly updated resource.</span></span>

- <span data-ttu-id="46ec7-250">如果所要求資料的目前 ETag 不符合要求所提供的 ETag，則資料在提取後遭到另一位使用者變更，且 Web API 應傳回具有空白訊息內文和狀態碼 412 (預先指定的條件失敗) 的 HTTP 回應。</span><span class="sxs-lookup"><span data-stu-id="46ec7-250">If the current ETag for the requested data does not match the ETag provided by the request, then the data has been changed by another user since it was fetched and the web API should return an HTTP response with an empty message body and a status code of 412 (Precondition Failed).</span></span>

- <span data-ttu-id="46ec7-251">如果即將更新的資源已不存在，則 Web API 應傳回具有狀態碼 404 (找不到) 的 HTTP 回應。</span><span class="sxs-lookup"><span data-stu-id="46ec7-251">If the resource to be updated no longer exists then the web API should return an HTTP response with the status code of 404 (Not Found).</span></span>

- <span data-ttu-id="46ec7-252">用戶端會使用狀態碼和回應標投來維護快取。</span><span class="sxs-lookup"><span data-stu-id="46ec7-252">The client uses the status code and response headers to maintain the cache.</span></span> <span data-ttu-id="46ec7-253">如果資料已更新 (狀態碼 204)，則物件可維持快取狀態 (只要 Cache-Control 標頭未指定 no-store)，但應更新 ETag。</span><span class="sxs-lookup"><span data-stu-id="46ec7-253">If the data has been updated (status code 204) then the object can remain cached (as long as the Cache-Control header does not specify no-store) but the ETag should be updated.</span></span> <span data-ttu-id="46ec7-254">如果資料由另一位使用者變更 (狀態碼 412) 或找不到 (狀態碼 404)，則應捨棄快取的物件。</span><span class="sxs-lookup"><span data-stu-id="46ec7-254">If the data was changed by another user changed (status code 412) or not found (status code 404) then the cached object should be discarded.</span></span>

<span data-ttu-id="46ec7-255">下一個程式碼範例顯示 Orders 控制器的 PUT 作業實作：</span><span class="sxs-lookup"><span data-stu-id="46ec7-255">The next code example shows an implementation of the PUT operation for the Orders controller:</span></span>

```csharp
public class OrdersController : ApiController
{
    [HttpPut]
    [Route("api/orders/{id:int}")]
    public IHttpActionResult UpdateExistingOrder(int id, DTOOrder order)
    {
        try
        {
            var baseUri = Constants.GetUriFromConfig();
            var orderToUpdate = this.ordersRepository.GetOrder(id);
            if (orderToUpdate == null)
            {
                return NotFound();
            }

            var hashedOrder = orderToUpdate.GetHashCode();
            string hashedOrderEtag = $"\"{hashedOrder}\"";

            // Retrieve the If-Match header from the request (if it exists)
            var matchEtags = Request.Headers.IfMatch;

            // If there is an Etag in the If-Match header and
            // this etag matches that of the order just retrieved,
            // or if there is no etag, then update the Order
            if (((matchEtags.Count > 0 &&
                String.CompareOrdinal(matchEtags.First().Tag, hashedOrderEtag) == 0)) ||
                matchEtags.Count == 0)
            {
                // Modify the order
                orderToUpdate.OrderValue = order.OrderValue;
                orderToUpdate.ProductID = order.ProductID;
                orderToUpdate.Quantity = order.Quantity;

                // Save the order back to the data store
                // ...

                // Create the No Content response with Cache-Control, ETag, and Location headers
                var cacheControlHeader = new CacheControlHeaderValue();
                cacheControlHeader.Private = true;
                cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);

                hashedOrder = order.GetHashCode();
                hashedOrderEtag = $"\"{hashedOrder}\"";
                var eTag = new EntityTagHeaderValue(hashedOrderEtag);

                var location = new Uri($"{baseUri}/{Constants.ORDERS}/{id}");
                var response = new EmptyResultWithCaching()
                {
                    StatusCode = HttpStatusCode.NoContent,
                    CacheControlHeader = cacheControlHeader,
                    ETag = eTag,
                    Location = location
                };

                return response;
            }

            // Otherwise return a Precondition Failed response
            return StatusCode(HttpStatusCode.PreconditionFailed);
        }
        catch
        {
            return InternalServerError();
        }
    }
    ...
}
```

> [!TIP]
> <span data-ttu-id="46ec7-256">使用 If-Match 標頭完全是選擇性的，而如果省略，Web API 將永遠嘗試更新指定的訂單，可能會盲目地覆寫其他使用者所做的更新。</span><span class="sxs-lookup"><span data-stu-id="46ec7-256">Use of the If-Match header is entirely optional, and if it is omitted the web API will always attempt to update the specified order, possibly blindly overwriting an update made by another user.</span></span> <span data-ttu-id="46ec7-257">若要避免遺失更新所衍生的問題，一定要提供 If-match 標頭。</span><span class="sxs-lookup"><span data-stu-id="46ec7-257">To avoid problems due to lost updates, always provide an If-Match header.</span></span>

## <a name="handling-large-requests-and-responses"></a><span data-ttu-id="46ec7-258">處理大量要求和回應</span><span class="sxs-lookup"><span data-stu-id="46ec7-258">Handling large requests and responses</span></span>

<span data-ttu-id="46ec7-259">有時候用戶端應用程式可能需要發出要求，以傳送或接收大小可能數 MB (或更大) 的資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-259">There may be occasions when a client application needs to issue requests that send or receive data that may be several megabytes (or bigger) in size.</span></span> <span data-ttu-id="46ec7-260">等候這個數量的資料進行傳輸，可能會導致用戶端應用程式沒有回應。</span><span class="sxs-lookup"><span data-stu-id="46ec7-260">Waiting while this amount of data is transmitted could cause the client application to become unresponsive.</span></span> <span data-ttu-id="46ec7-261">當您需要處理包含大量資料的要求時，請考慮下列幾點：</span><span class="sxs-lookup"><span data-stu-id="46ec7-261">Consider the following points when you need to handle requests that include significant amounts of data:</span></span>

### <a name="optimize-requests-and-responses-that-involve-large-objects"></a><span data-ttu-id="46ec7-262">最佳化涉及大型物件的要求和回應</span><span class="sxs-lookup"><span data-stu-id="46ec7-262">Optimize requests and responses that involve large objects</span></span>

<span data-ttu-id="46ec7-263">某些資源可能是大型物件，或包含大型欄位，例如圖形影像或其他類型的二進位資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-263">Some resources may be large objects or include large fields, such as graphics images or other types of binary data.</span></span> <span data-ttu-id="46ec7-264">Web API 應支援串流處理，這些資源才能進行最佳化的上傳和下載。</span><span class="sxs-lookup"><span data-stu-id="46ec7-264">A web API should support streaming to enable optimized uploading and downloading of these resources.</span></span>

<span data-ttu-id="46ec7-265">HTTP 通訊協定提供區塊傳輸編碼機制，以將大型資料物件串流處理回到用戶端。</span><span class="sxs-lookup"><span data-stu-id="46ec7-265">The HTTP protocol provides the chunked transfer encoding mechanism to stream large data objects back to a client.</span></span> <span data-ttu-id="46ec7-266">當用戶端傳送大型物件的 HTTP GET 要求時，Web API 可透過 HTTP 連接以分次「區塊」回覆。</span><span class="sxs-lookup"><span data-stu-id="46ec7-266">When the client sends an HTTP GET request for a large object, the web API can send the reply back in piecemeal *chunks* over an HTTP connection.</span></span> <span data-ttu-id="46ec7-267">最初可能不知道回覆中的資料長度 (可能已產生)，所以裝載 Web API 的伺服器應傳送回應訊息，其中每個區塊指定 Transfer-Encoding:Chunked 標頭，而不是 Content-Length 標頭。</span><span class="sxs-lookup"><span data-stu-id="46ec7-267">The length of the data in the reply may not be known initially (it might be generated), so the server hosting the web API should send a response message with each chunk that specifies the Transfer-Encoding: Chunked header rather than a Content-Length header.</span></span> <span data-ttu-id="46ec7-268">用戶端應用程式可依序接收每個區塊，以建置完整的回應。</span><span class="sxs-lookup"><span data-stu-id="46ec7-268">The client application can receive each chunk in turn to build up the complete response.</span></span> <span data-ttu-id="46ec7-269">伺服器送回大小為零的最後一個區塊時，資料傳輸便完成。</span><span class="sxs-lookup"><span data-stu-id="46ec7-269">The data transfer completes when the server sends back a final chunk with zero size.</span></span>

<span data-ttu-id="46ec7-270">單一要求理論上可能會導致會耗用相當多資源的大量物件。</span><span class="sxs-lookup"><span data-stu-id="46ec7-270">A single request could conceivably result in a massive object that consumes considerable resources.</span></span> <span data-ttu-id="46ec7-271">在串流處理過程中，如果 Web API 判定要求中的資料量超過一些可接受的界限，則可以中止作業並傳回具有狀態碼 413 (要求實體太大) 的回應訊息。</span><span class="sxs-lookup"><span data-stu-id="46ec7-271">If, during the streaming process, the web API determines that the amount of data in a request has exceeded some acceptable bounds, it can abort the operation and return a response message with status code 413 (Request Entity Too Large).</span></span>

<span data-ttu-id="46ec7-272">您可以使用 HTTP 壓縮，將透過網路傳輸的大型物件最小化。</span><span class="sxs-lookup"><span data-stu-id="46ec7-272">You can minimize the size of large objects transmitted over the network by using HTTP compression.</span></span> <span data-ttu-id="46ec7-273">此方法有助於減少網路流量和相關聯的網路延遲，但代價是需要在用戶端和裝載 Web API 的伺服器上進行其他處理。</span><span class="sxs-lookup"><span data-stu-id="46ec7-273">This approach helps to reduce the amount of network traffic and the associated network latency, but at the cost of requiring additional processing at the client and the server hosting the web API.</span></span> <span data-ttu-id="46ec7-274">例如，預計收到壓縮資料的用戶端應用程式可以包含 Accept-Encoding: gzip 要求標頭 (也可以指定其他資料壓縮演算法)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-274">For example, a client application that expects to receive compressed data can include an Accept-Encoding: gzip request header (other data compression algorithms can also be specified).</span></span> <span data-ttu-id="46ec7-275">如果伺服器支援壓縮，則應以訊息內文中以 gzip 格式保留的內容和 Content-Encoding: gzip 回應標頭回應。</span><span class="sxs-lookup"><span data-stu-id="46ec7-275">If the server supports compression it should respond with the content held in gzip format in the message body and the Content-Encoding: gzip response header.</span></span>

<span data-ttu-id="46ec7-276">您可以結合編碼壓縮與串流；在串流前先壓縮資料，並在訊息標頭中指定 gzip 內容編碼和區塊傳輸編碼。</span><span class="sxs-lookup"><span data-stu-id="46ec7-276">You can combine encoded compression with streaming; compress the data first before streaming it, and specify the gzip content encoding and chunked transfer encoding in the message headers.</span></span> <span data-ttu-id="46ec7-277">另請注意，可將某些 Web 伺服器 (例如 Internet Information Server) 設定為自動壓縮 HTTP 回應 (不論 Web API 是否壓縮資料)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-277">Also note that some web servers (such as Internet Information Server) can be configured to automatically compress HTTP responses regardless of whether the web API compresses the data or not.</span></span>

### <a name="implement-partial-responses-for-clients-that-do-not-support-asynchronous-operations"></a><span data-ttu-id="46ec7-278">針對不支援非同步作業的用戶端實作部份回應</span><span class="sxs-lookup"><span data-stu-id="46ec7-278">Implement partial responses for clients that do not support asynchronous operations</span></span>

<span data-ttu-id="46ec7-279">用戶端應用程式可以明確要求區塊中大型物件的資料 (也稱為部份回應)，作為非同步串流的替代方式。</span><span class="sxs-lookup"><span data-stu-id="46ec7-279">As an alternative to asynchronous streaming, a client application can explicitly request data for large objects in chunks, known as partial responses.</span></span> <span data-ttu-id="46ec7-280">用戶端應用程式會傳送 HTTP HEAD 要求，以取得物件的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="46ec7-280">The client application sends an HTTP HEAD request to obtain information about the object.</span></span> <span data-ttu-id="46ec7-281">如果 Web API 支援部份回應，則應以包含 Accept-Ranges 標頭和 Content-Length 標頭 (指出物件的總大小) 的回應訊息來回應 HEAD 要求 (但訊息內容應該是空的)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-281">If the web API supports partial responses if should respond to the HEAD request with a response message that contains an Accept-Ranges header and a Content-Length header that indicates the total size of the object, but the body of the message should be empty.</span></span> <span data-ttu-id="46ec7-282">用戶端應用程式可以使用此資訊來建構一系列的 GET 要求，以指定要接收的位元組範圍。</span><span class="sxs-lookup"><span data-stu-id="46ec7-282">The client application can use this information to construct a series of GET requests that specify a range of bytes to receive.</span></span> <span data-ttu-id="46ec7-283">Web API 應傳回具有 HTTP 狀態 206 (部份內容)、Content-Length 標頭 (指定回應訊息內容中包含的實際資料量) 和 Content-Range 標頭 (指出此資料代表物件的哪一個部分 (例如 4000 到 8000 個位元組)) 的回應訊息。</span><span class="sxs-lookup"><span data-stu-id="46ec7-283">The web API should return a response message with HTTP status 206 (Partial Content), a Content-Length header that specifies the actual amount of data included in the body of the response message, and a Content-Range header that indicates which part (such as bytes 4000 to 8000) of the object this data represents.</span></span>

<span data-ttu-id="46ec7-284">HTTP HEAD 要求和部份回應會在 [API 設計][api-design]中詳加說明。</span><span class="sxs-lookup"><span data-stu-id="46ec7-284">HTTP HEAD requests and partial responses are described in more detail in [API Design][api-design].</span></span>

### <a name="avoid-sending-unnecessary-100-continue-status-messages-in-client-applications"></a><span data-ttu-id="46ec7-285">避免在用戶端應用程式中傳送不必要的 100-Continue 狀態訊息</span><span class="sxs-lookup"><span data-stu-id="46ec7-285">Avoid sending unnecessary 100-Continue status messages in client applications</span></span>

<span data-ttu-id="46ec7-286">即將將大量資料傳送到伺服器的用戶端應用程式可能會先判斷伺服器是否確實願意接受要求。</span><span class="sxs-lookup"><span data-stu-id="46ec7-286">A client application that is about to send a large amount of data to a server may determine first whether the server is actually willing to accept the request.</span></span> <span data-ttu-id="46ec7-287">在傳送資料前，用戶端應用程式可以提交具有 Expect:100-Continue 標頭、Content-Length 標頭 (指出資料大小)，但訊息內文空白的 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="46ec7-287">Prior to sending the data, the client application can submit an HTTP request with an Expect: 100-Continue header, a Content-Length header that indicates the size of the data, but an empty message body.</span></span> <span data-ttu-id="46ec7-288">如果伺服器願意處理要求，則應以指定 HTTP 狀態 100 (繼續) 的訊息回應。</span><span class="sxs-lookup"><span data-stu-id="46ec7-288">If the server is willing to handle the request, it should respond with a message that specifies the HTTP status 100 (Continue).</span></span> <span data-ttu-id="46ec7-289">用戶端應用程式可以接著繼續執行並傳送完整的要求 (包括訊息內文中的資料)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-289">The client application can then proceed and send the complete request including the data in the message body.</span></span>

<span data-ttu-id="46ec7-290">如果您使用 IIS 裝載服務，則 HTTP.sys 驅動程式會先自動偵測並處理 Expect:100-Continue 標頭，然後再將要求傳遞至 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="46ec7-290">If you are hosting a service by using IIS, the HTTP.sys driver automatically detects and handles Expect: 100-Continue headers before passing requests to your web application.</span></span> <span data-ttu-id="46ec7-291">這表示您不太可能在您的應用程式程式碼中看見這些標頭，而且您可以假設 IIS 已經篩選它認為不合適或太大的訊息。</span><span class="sxs-lookup"><span data-stu-id="46ec7-291">This means that you are unlikely to see these headers in your application code, and you can assume that IIS has already filtered any messages that it deems to be unfit or too large.</span></span>

<span data-ttu-id="46ec7-292">如果您要使用 .NET Framework 建置用戶端應用程式，則所有 POST 和 PUT 訊息都會先傳送預設具有 Expect:100-Continue 標頭的訊息。</span><span class="sxs-lookup"><span data-stu-id="46ec7-292">If you are building client applications by using the .NET Framework, then all POST and PUT messages will first send messages with Expect: 100-Continue headers by default.</span></span> <span data-ttu-id="46ec7-293">在伺服器端，此程序是由 .NET Framework 直接處理。</span><span class="sxs-lookup"><span data-stu-id="46ec7-293">As with the server-side, the process is handled transparently by the .NET Framework.</span></span> <span data-ttu-id="46ec7-294">不過，此程序會導致每個 POST 和 PUT 要求引起兩次伺服器來回行程 (即使是小型要求)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-294">However, this process results in each POST and PUT request causing two round-trips to the server, even for small requests.</span></span> <span data-ttu-id="46ec7-295">如果您的應用程式並未傳送具有大量資料的要求，您可以使用 `ServicePointManager` 類別在用戶端應用程式中建立 `ServicePoint` 物件，進而停用這項功能。</span><span class="sxs-lookup"><span data-stu-id="46ec7-295">If your application is not sending requests with large amounts of data, you can disable this feature by using the `ServicePointManager` class to create `ServicePoint` objects in the client application.</span></span> <span data-ttu-id="46ec7-296">`ServicePoint` 物件會根據 URI 的配置和主機片段 (用以識別伺服器上的資源)，處理用戶端對伺服器的連線。</span><span class="sxs-lookup"><span data-stu-id="46ec7-296">A `ServicePoint` object handles the connections that the client makes to a server based on the scheme and host fragments of URIs that identify resources on the server.</span></span> <span data-ttu-id="46ec7-297">然後您可以將 `ServicePoint` 物件的 `Expect100Continue` 屬性設定為 false。</span><span class="sxs-lookup"><span data-stu-id="46ec7-297">You can then set the `Expect100Continue` property of the `ServicePoint` object to false.</span></span> <span data-ttu-id="46ec7-298">將會傳送用戶端透過 URI (其符合 `ServicePoint` 物件的配置和主機片段) 進行的所有後續 POST 和 PUT 要求，但不需 Expect:100-Continue 標頭。</span><span class="sxs-lookup"><span data-stu-id="46ec7-298">All subsequent POST and PUT requests made by the client through a URI that matches the scheme and host fragments of the `ServicePoint` object will be sent without Expect: 100-Continue headers.</span></span> <span data-ttu-id="46ec7-299">下列程式碼示範如何設定 `ServicePoint` 物件，用來設定所有傳送至具有 `http` 配置和 `www.contoso.com` 主機之 URI 的要求。</span><span class="sxs-lookup"><span data-stu-id="46ec7-299">The following code shows how to configure a `ServicePoint` object that configures all requests sent to URIs with a scheme of `http` and a host of `www.contoso.com`.</span></span>

```csharp
Uri uri = new Uri("https://www.contoso.com/");
ServicePoint sp = ServicePointManager.FindServicePoint(uri);
sp.Expect100Continue = false;
```

<span data-ttu-id="46ec7-300">您也可以設定 `ServicePointManager` 類別的靜態 `Expect100Continue` 屬性，以針對所有後續建立的 [ServicePoint]](/dotnet/api/system.net.servicepoint) 物件指定此屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="46ec7-300">You can also set the static `Expect100Continue` property of the `ServicePointManager` class to specify the default value of this property for all subsequently created [ServicePoint]](/dotnet/api/system.net.servicepoint) objects.</span></span>

### <a name="support-pagination-for-requests-that-may-return-large-numbers-of-objects"></a><span data-ttu-id="46ec7-301">支援對可能傳回大量物件的要求進行分頁</span><span class="sxs-lookup"><span data-stu-id="46ec7-301">Support pagination for requests that may return large numbers of objects</span></span>

<span data-ttu-id="46ec7-302">如果集合包含大量資源，則對相對應的 URI 發出 GET 要求可能導致在裝載 Web API 的伺服器上進行大量處理而影響效能，並產生會導致延遲增加的大量網路流量。</span><span class="sxs-lookup"><span data-stu-id="46ec7-302">If a collection contains a large number of resources, issuing a GET request to the corresponding URI could result in significant processing on the server hosting the web API affecting performance, and generate a significant amount of network traffic resulting in increased latency.</span></span>

<span data-ttu-id="46ec7-303">若要處理這些情況，Web API 應支援查詢字串，讓用戶端應用程式能在更易於管理的離散區塊 (或頁面) 中修改要求或提取資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-303">To handle these cases, the web API should support query strings that enable the client application to refine requests or fetch data in more manageable, discrete blocks (or pages).</span></span> <span data-ttu-id="46ec7-304">下方程式碼會顯示 `Orders` 控制器中的 `GetAllOrders` 方法。</span><span class="sxs-lookup"><span data-stu-id="46ec7-304">The code below shows the `GetAllOrders` method in the `Orders` controller.</span></span> <span data-ttu-id="46ec7-305">此方法會擷取訂單的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-305">This method retrieves the details of orders.</span></span> <span data-ttu-id="46ec7-306">如果此方法不受限制，它可能會傳回大量的資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-306">If this method was unconstrained, it could conceivably return a large amount of data.</span></span> <span data-ttu-id="46ec7-307">`limit` 和 `offset` 參數主要用來將資料量縮小為較小的子集，在此例中預設只有前 10 張訂單：</span><span class="sxs-lookup"><span data-stu-id="46ec7-307">The `limit` and `offset` parameters are intended to reduce the volume of data to a smaller subset, in this case only the first 10 orders by default:</span></span>

```csharp
public class OrdersController : ApiController
{
    ...
    [Route("api/orders")]
    [HttpGet]
    public IEnumerable<Order> GetAllOrders(int limit=10, int offset=0)
    {
        // Find the number of orders specified by the limit parameter
        // starting with the order specified by the offset parameter
        var orders = ...
        return orders;
    }
    ...
}
```

<span data-ttu-id="46ec7-308">用戶端應用程式可以發出要求，使用 URI `https://www.adventure-works.com/api/orders?limit=30&offset=50` 來擷取從位移 50 開始的 30 張訂單。</span><span class="sxs-lookup"><span data-stu-id="46ec7-308">A client application can issue a request to retrieve 30 orders starting at offset 50 by using the URI `https://www.adventure-works.com/api/orders?limit=30&offset=50`.</span></span>

> [!TIP]
> <span data-ttu-id="46ec7-309">避免讓用戶端應用程式指定會導致長度超過 2000 個字元的 URI 的查詢字串。</span><span class="sxs-lookup"><span data-stu-id="46ec7-309">Avoid enabling client applications to specify query strings that result in a URI that is more than 2000 characters long.</span></span> <span data-ttu-id="46ec7-310">許多 Web 用戶端和伺服器都無法處理此種長度的 URI。</span><span class="sxs-lookup"><span data-stu-id="46ec7-310">Many web clients and servers cannot handle URIs that are this long.</span></span>

## <a name="maintaining-responsiveness-scalability-and-availability"></a><span data-ttu-id="46ec7-311">維護回應性、延展性和可用性</span><span class="sxs-lookup"><span data-stu-id="46ec7-311">Maintaining responsiveness, scalability, and availability</span></span>

<span data-ttu-id="46ec7-312">在世界各地執行的許多用戶端應用程式可能會利用相同的 Web API。</span><span class="sxs-lookup"><span data-stu-id="46ec7-312">The same web API might be utilized by many client applications running anywhere in the world.</span></span> <span data-ttu-id="46ec7-313">請務必確保實作 Web API 來維護沈重負載下的回應性，可以延展來支援非常不同的工作負載，以及保證執行業務關鍵作業的用戶端的可用性。</span><span class="sxs-lookup"><span data-stu-id="46ec7-313">It is important to ensure that the web API is implemented to maintain responsiveness under a heavy load, to be scalable to support a highly varying workload, and to guarantee availability for clients that perform business-critical operations.</span></span> <span data-ttu-id="46ec7-314">當您決定如何儲存這些需求時，請考慮下列幾點：</span><span class="sxs-lookup"><span data-stu-id="46ec7-314">Consider the following points when determining how to meet these requirements:</span></span>

### <a name="provide-asynchronous-support-for-long-running-requests"></a><span data-ttu-id="46ec7-315">長時間執行的要求提供非同步支援</span><span class="sxs-lookup"><span data-stu-id="46ec7-315">Provide asynchronous support for long-running requests</span></span>

<span data-ttu-id="46ec7-316">執行可能需花很長時間處理的要求時，不應封鎖送出此要求的用戶端。</span><span class="sxs-lookup"><span data-stu-id="46ec7-316">A request that might take a long time to process should be performed without blocking the client that submitted the request.</span></span> <span data-ttu-id="46ec7-317">Web API 可以執行一些初始檢查來驗證要求、起始個別工作來執行工作，然後傳回具有 HTTP 狀態碼 202 (已接受) 的回應訊息。</span><span class="sxs-lookup"><span data-stu-id="46ec7-317">The web API can perform some initial checking to validate the request, initiate a separate task to perform the work, and then return a response message with HTTP code 202 (Accepted).</span></span> <span data-ttu-id="46ec7-318">可隨著 Web API 處理非同步執行工作，或可以將工作卸載至背景工作。</span><span class="sxs-lookup"><span data-stu-id="46ec7-318">The task could run asynchronously as part of the web API processing, or it could be offloaded to a background task.</span></span>

<span data-ttu-id="46ec7-319">Web API 也應提供一種機制，將處理的結果傳回給用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="46ec7-319">The web API should also provide a mechanism to return the results of the processing to the client application.</span></span> <span data-ttu-id="46ec7-320">提供輪詢機制，以供用戶端應用程式定期查詢處理是否已完成並取得結果，或讓 Web API 在作業完成時傳送通知，即可達到此目的。</span><span class="sxs-lookup"><span data-stu-id="46ec7-320">You can achieve this by providing a polling mechanism for client applications to periodically query whether the processing has finished and obtain the result, or enabling the web API to send a notification when the operation has completed.</span></span>

<span data-ttu-id="46ec7-321">使用下列方法來提供當作虛擬資源的「輪詢」URI，即可實作簡單的輪詢機制：</span><span class="sxs-lookup"><span data-stu-id="46ec7-321">You can implement a simple polling mechanism by providing a *polling* URI that acts as a virtual resource using the following approach:</span></span>

1. <span data-ttu-id="46ec7-322">用戶端應用程式會將初始要求傳送至 Web API。</span><span class="sxs-lookup"><span data-stu-id="46ec7-322">The client application sends the initial request to the web API.</span></span>
2. <span data-ttu-id="46ec7-323">Web API 會將要求相關資訊儲存在資料表儲存體或 Microsoft Azure 快取中保存的資料表中，並為此項目產生唯一索引鍵 (可能是 GUID 格式)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-323">The web API stores information about the request in a table held in table storage or Microsoft Azure Cache, and generates a unique key for this entry, possibly in the form of a GUID.</span></span>
3. <span data-ttu-id="46ec7-324">Web API 會將此處理當作個別的工作起始。</span><span class="sxs-lookup"><span data-stu-id="46ec7-324">The web API initiates the processing as a separate task.</span></span> <span data-ttu-id="46ec7-325">Web API 會在資料表中將工作狀態記錄為「執行中」。</span><span class="sxs-lookup"><span data-stu-id="46ec7-325">The web API records the state of the task in the table as *Running*.</span></span>
4. <span data-ttu-id="46ec7-326">Web API 會傳回具有 HTTP 狀態碼 202 (已接受) 的回應訊息，以及訊息內文中資料表項目的 GUID。</span><span class="sxs-lookup"><span data-stu-id="46ec7-326">The web API returns a response message with HTTP status code 202 (Accepted), and the GUID of the table entry in the body of the message.</span></span>
5. <span data-ttu-id="46ec7-327">當工作完成時，Web API 會將結果儲存在資料表中，並將工作狀態設定為「完成」。</span><span class="sxs-lookup"><span data-stu-id="46ec7-327">When the task has completed, the web API stores the results in the table, and sets the state of the task to *Complete*.</span></span> <span data-ttu-id="46ec7-328">請注意，如果工作失敗，Web API 也可以儲存失敗相關資訊，並將狀態設定為「失敗」。</span><span class="sxs-lookup"><span data-stu-id="46ec7-328">Note that if the task fails, the web API could also store information about the failure and set the status to *Failed*.</span></span>
6. <span data-ttu-id="46ec7-329">當工作正在執行時，用戶端可以繼續執行自己的處理。</span><span class="sxs-lookup"><span data-stu-id="46ec7-329">While the task is running, the client can continue performing its own processing.</span></span> <span data-ttu-id="46ec7-330">它可以定期將要求傳送至 URI /polling/{guid}，其中 {guid} 是 Web API 在 202 回應訊息中傳回的 GUID。</span><span class="sxs-lookup"><span data-stu-id="46ec7-330">It can periodically send a request to the URI */polling/{guid}* where *{guid}* is the GUID returned in the 202 response message by the web API.</span></span>
7. <span data-ttu-id="46ec7-331">位於 /polling/{guid} URI 的 Web API 會查詢資料表中對應工作的狀態，並傳回具有 HTTP 狀態碼 200 (確定) 的回應訊息，其中包含此狀態 (「執行中」、「完成」或「失敗」)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-331">The web API at the */polling/{guid}* URI queries the state of the corresponding task in the table and returns a response message with HTTP status code 200 (OK) containing this state (*Running*, *Complete*, or *Failed*).</span></span> <span data-ttu-id="46ec7-332">如果工作已完成或失敗，回應訊息也可以包含處理的結果或任何有關失敗原因的可用資訊。</span><span class="sxs-lookup"><span data-stu-id="46ec7-332">If the task has completed or failed, the response message can also include the results of the processing or any information available about the reason for the failure.</span></span>

<span data-ttu-id="46ec7-333">用於實作通知的選項包括：</span><span class="sxs-lookup"><span data-stu-id="46ec7-333">Options for implementing notifications include:</span></span>

- <span data-ttu-id="46ec7-334">使用 Azure 通知中樞，將非同步回應推送至用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="46ec7-334">Using an Azure Notification Hub to push asynchronous responses to client applications.</span></span> <span data-ttu-id="46ec7-335">如需詳細資訊，請參閱 [Azure 通知中樞通知使用者](/azure/notification-hubs/notification-hubs-aspnet-backend-windows-dotnet-wns-notification/)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-335">For more information, see [Azure Notification Hubs Notify Users](/azure/notification-hubs/notification-hubs-aspnet-backend-windows-dotnet-wns-notification/).</span></span>
- <span data-ttu-id="46ec7-336">使用 Comet 模型來保持用戶端與裝載 Web API 的伺服器之間的持續性網路連線，並使用此連線將訊息從伺服器推送回到用戶端。</span><span class="sxs-lookup"><span data-stu-id="46ec7-336">Using the Comet model to retain a persistent network connection between the client and the server hosting the web API, and using this connection to push messages from the server back to the client.</span></span> <span data-ttu-id="46ec7-337">MSDN 雜誌文章 [在 Microsoft .NET Framework 中建置簡單的 Comet 應用程式](https://msdn.microsoft.com/magazine/jj891053.aspx) 會舉例說明解決方案。</span><span class="sxs-lookup"><span data-stu-id="46ec7-337">The MSDN magazine article [Building a Simple Comet Application in the Microsoft .NET Framework](https://msdn.microsoft.com/magazine/jj891053.aspx) describes an example solution.</span></span>
- <span data-ttu-id="46ec7-338">使用 SignalR 並透過持續性網路連線，即時將資料從 Web 伺服器推送到用戶端。</span><span class="sxs-lookup"><span data-stu-id="46ec7-338">Using SignalR to push data in real-time from the web server to the client over a persistent network connection.</span></span> <span data-ttu-id="46ec7-339">SignalR 可以 NuGet 封裝形式使用於 ASP.NET Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="46ec7-339">SignalR is available for ASP.NET web applications as a NuGet package.</span></span> <span data-ttu-id="46ec7-340">您可以在 [ASP.NET SignalR](https://www.asp.net/signalr) 網站上找到更多資訊。</span><span class="sxs-lookup"><span data-stu-id="46ec7-340">You can find more information on the [ASP.NET SignalR](https://www.asp.net/signalr) website.</span></span>

### <a name="ensure-that-each-request-is-stateless"></a><span data-ttu-id="46ec7-341">確保每個要求都是無狀態</span><span class="sxs-lookup"><span data-stu-id="46ec7-341">Ensure that each request is stateless</span></span>

<span data-ttu-id="46ec7-342">每個要求都應該被視為不可部分完成。</span><span class="sxs-lookup"><span data-stu-id="46ec7-342">Each request should be considered atomic.</span></span> <span data-ttu-id="46ec7-343">在用戶端應用程式所提出的某一個要求與同一個用戶端所提交的任何後續要求之間，應沒有任何相依性。</span><span class="sxs-lookup"><span data-stu-id="46ec7-343">There should be no dependencies between one request made by a client application and any subsequent requests submitted by the same client.</span></span> <span data-ttu-id="46ec7-344">此方法有助於進行延展；Web 服務的執行個體可以部署在許多伺服器上。</span><span class="sxs-lookup"><span data-stu-id="46ec7-344">This approach assists in scalability; instances of the web service can be deployed on a number of servers.</span></span> <span data-ttu-id="46ec7-345">用戶端要求可在任何這些執行個體上進行導向，而結果應一律相同。</span><span class="sxs-lookup"><span data-stu-id="46ec7-345">Client requests can be directed at any of these instances and the results should always be the same.</span></span> <span data-ttu-id="46ec7-346">基於類似的理由，這也可改善可用性；如果 Web 伺服器失敗，可將要求路由傳送到另一個執行個體 (藉由使用 Azure 流量管理員)，然而伺服器會重新啟動，但不會對用戶端應用程式造成不良的影響。</span><span class="sxs-lookup"><span data-stu-id="46ec7-346">It also improves availability for a similar reason; if a web server fails requests can be routed to another instance (by using Azure Traffic Manager) while the server is restarted with no ill effects on client applications.</span></span>

### <a name="track-clients-and-implement-throttling-to-reduce-the-chances-of-dos-attacks"></a><span data-ttu-id="46ec7-347">追蹤用戶端並實作節流來降低 DOS 攻擊的機會</span><span class="sxs-lookup"><span data-stu-id="46ec7-347">Track clients and implement throttling to reduce the chances of DOS attacks</span></span>

<span data-ttu-id="46ec7-348">如果特定用戶端在指定的期間內提出大量要求，則可能會獨佔服務並會影響其他用戶端的效能。</span><span class="sxs-lookup"><span data-stu-id="46ec7-348">If a specific client makes a large number of requests within a given period of time it might monopolize the service and affect the performance of other clients.</span></span> <span data-ttu-id="46ec7-349">若要緩和這個問題，Web API 可以追蹤所有連入要求的 IP 位址或藉由記錄每個已驗證的存取，以監視來自用戶端應用程式的呼叫。</span><span class="sxs-lookup"><span data-stu-id="46ec7-349">To mitigate this issue, a web API can monitor calls from client applications either by tracking the IP address of all incoming requests or by logging each authenticated access.</span></span> <span data-ttu-id="46ec7-350">您可以使用此資訊來限制資源存取。</span><span class="sxs-lookup"><span data-stu-id="46ec7-350">You can use this information to limit resource access.</span></span> <span data-ttu-id="46ec7-351">如果用戶端超過定義的限制，Web API 可以傳回具有狀態碼 503 (無法使用服務) 的回應訊息，並包含 Retry-After 標頭，以指定用戶端何時可以傳送下一個要求而不會遭到拒絕。</span><span class="sxs-lookup"><span data-stu-id="46ec7-351">If a client exceeds a defined limit, the web API can return a response message with status 503 (Service Unavailable) and include a Retry-After header that specifies when the client can send the next request without it being declined.</span></span> <span data-ttu-id="46ec7-352">此策略有助於減少來自一組癱瘓系統之用戶端的阻斷服務 (DOS) 攻擊機會。</span><span class="sxs-lookup"><span data-stu-id="46ec7-352">This strategy can help to reduce the chances of a Denial Of Service (DOS) attack from a set of clients stalling the system.</span></span>

### <a name="manage-persistent-http-connections-carefully"></a><span data-ttu-id="46ec7-353">請小心管理持續性 HTTP 連線</span><span class="sxs-lookup"><span data-stu-id="46ec7-353">Manage persistent HTTP connections carefully</span></span>

<span data-ttu-id="46ec7-354">HTTP 通訊協定支援可用的持續性 HTTP 連線。</span><span class="sxs-lookup"><span data-stu-id="46ec7-354">The HTTP protocol supports persistent HTTP connections where they are available.</span></span> <span data-ttu-id="46ec7-355">HTTP 1.0 規格加入了 Connection:Keep-Alive 標頭，可讓用戶端應用程式向伺服器表示它可以使用相同的連線來傳送後續的要求，而不用開啟新的連線。</span><span class="sxs-lookup"><span data-stu-id="46ec7-355">The HTTP 1.0 specificiation added the Connection:Keep-Alive header that enables a client application to indicate to the server that it can use the same connection to send subsequent requests rather than opening new ones.</span></span> <span data-ttu-id="46ec7-356">如果用戶端未在主機定義的期間內重複使用連線，連線就會自動關閉。</span><span class="sxs-lookup"><span data-stu-id="46ec7-356">The connection closes automatically if the client does not reuse the connection within a period defined by the host.</span></span> <span data-ttu-id="46ec7-357">此行為是 HTTP 1.1 中 Azure 服務所使用的預設值，因此不需要在訊息中包含 Keep-alive 標頭。</span><span class="sxs-lookup"><span data-stu-id="46ec7-357">This behavior is the default in HTTP 1.1 as used by Azure services, so there is no need to include Keep-Alive headers in messages.</span></span>

<span data-ttu-id="46ec7-358">讓連接保持開啟可減少延遲和網路壅塞，有助於改善回應能力，但由於讓不必要的連接保持開啟過久，導致其他並行用戶端無法連接，可能不利於延展性。</span><span class="sxs-lookup"><span data-stu-id="46ec7-358">Keeping a connection open can help to improve responsiveness by reducing latency and network congestion, but it can be detrimental to scalability by keeping unnecessary connections open for longer than required, limiting the ability of other concurrent clients to connect.</span></span> <span data-ttu-id="46ec7-359">如果用戶端應用程式是在行動裝置上執行，這也可能影響電池壽命；如果應用程式只偶而向伺服器發出要求，則讓連接保持開啟可能會導致電池更快耗盡。</span><span class="sxs-lookup"><span data-stu-id="46ec7-359">It can also affect battery life if the client application is running on a mobile device; if the application only makes occasional requests to the server, maintaining an open connection can cause the battery to drain more quickly.</span></span> <span data-ttu-id="46ec7-360">若要使用 HTTP 1.1 確保連線不會持續，用戶端可以在訊息中包含 Connection:Close 標頭以覆寫預設行為。</span><span class="sxs-lookup"><span data-stu-id="46ec7-360">To ensure that a connection is not made persistent with HTTP 1.1, the client can include a Connection:Close header with messages to override the default behavior.</span></span> <span data-ttu-id="46ec7-361">同樣地，如果伺服器正在處理非常大量的用戶端，則可以在回應訊息中包含應關閉連線並儲存伺服器資源的 Connection:Close 標頭。</span><span class="sxs-lookup"><span data-stu-id="46ec7-361">Similarly, if a server is handling a very large number of clients it can include a Connection:Close header in response messages which should close the connection and save server resources.</span></span>

> [!NOTE]
> <span data-ttu-id="46ec7-362">持續性 HTTP 連線純粹是選擇性功能，可減少與重複建立通訊通道相關聯的網路負荷。</span><span class="sxs-lookup"><span data-stu-id="46ec7-362">Persistent HTTP connections are a purely optional feature to reduce the network overhead associated with repeatedly establishing a communications channel.</span></span> <span data-ttu-id="46ec7-363">Web API 和用戶端應用程式都不應該依賴持續性 HTTP 連線才可使用。</span><span class="sxs-lookup"><span data-stu-id="46ec7-363">Neither the web API nor the client application should depend on a persistent HTTP connection being available.</span></span> <span data-ttu-id="46ec7-364">請勿使用持續性 HTTP 連線來實作 Comet 式通知系統；而是應該利用 TCP 層的通訊端 (或可用的 Websocket)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-364">Do not use persistent HTTP connections to implement Comet-style notification systems; instead you should utilize sockets (or websockets if available) at the TCP layer.</span></span> <span data-ttu-id="46ec7-365">最後請注意，如果用戶端應用程式透過 Proxy 與伺服器進行通訊，則 Keep-Alive 標頭的使用受限；只有與用戶端和 Proxy 的連線具持續性。</span><span class="sxs-lookup"><span data-stu-id="46ec7-365">Finally, note Keep-Alive headers are of limited use if a client application communicates with a server via a proxy; only the connection with the client and the proxy will be persistent.</span></span>

## <a name="publishing-and-managing-a-web-api"></a><span data-ttu-id="46ec7-366">發行和管理 Web API</span><span class="sxs-lookup"><span data-stu-id="46ec7-366">Publishing and managing a web API</span></span>

<span data-ttu-id="46ec7-367">若要讓 Web API 可供用戶端應用程式使用，Web API 必須部署至主機環境。</span><span class="sxs-lookup"><span data-stu-id="46ec7-367">To make a web API available for client applications, the web API must be deployed to a host environment.</span></span> <span data-ttu-id="46ec7-368">此環境通常是 Web 伺服器，然而也可能是其他類型的主機程序。</span><span class="sxs-lookup"><span data-stu-id="46ec7-368">This environment is typically a web server, although it may be some other type of host process.</span></span> <span data-ttu-id="46ec7-369">發佈 Web API 時，您應該考慮下列幾點：</span><span class="sxs-lookup"><span data-stu-id="46ec7-369">You should consider the following points when publishing a web API:</span></span>

- <span data-ttu-id="46ec7-370">所有要求都必須進行驗證和授權，而且必須強制執行適當層級的存取控制。</span><span class="sxs-lookup"><span data-stu-id="46ec7-370">All requests must be authenticated and authorized, and the appropriate level of access control must be enforced.</span></span>
- <span data-ttu-id="46ec7-371">商業 Web API 可能會受制於各種有關回應時間的品質保證。</span><span class="sxs-lookup"><span data-stu-id="46ec7-371">A commercial web API might be subject to various quality guarantees concerning response times.</span></span> <span data-ttu-id="46ec7-372">如果負載可隨著時間大幅變化，請務必確保可延伸主機環境。</span><span class="sxs-lookup"><span data-stu-id="46ec7-372">It is important to ensure that host environment is scalable if the load can vary significantly over time.</span></span>
- <span data-ttu-id="46ec7-373">有可能需要為了獲利目的而計量要求。</span><span class="sxs-lookup"><span data-stu-id="46ec7-373">It may be necessary to meter requests for monetization purposes.</span></span>
- <span data-ttu-id="46ec7-374">有可能需要規範傳送至 Web API 的流量流程，並且對已用盡配額的特定用戶端實作節流。</span><span class="sxs-lookup"><span data-stu-id="46ec7-374">It might be necessary to regulate the flow of traffic to the web API, and implement throttling for specific clients that have exhausted their quotas.</span></span>
- <span data-ttu-id="46ec7-375">法規需求可能會託管所有要求和回應的記錄與稽核。</span><span class="sxs-lookup"><span data-stu-id="46ec7-375">Regulatory requirements might mandate logging and auditing of all requests and responses.</span></span>
- <span data-ttu-id="46ec7-376">為了確保可用性，有可能需要監視裝載 Web API 的伺服器健全狀況，並在必要時重新啟動。</span><span class="sxs-lookup"><span data-stu-id="46ec7-376">To ensure availability, it may be necessary to monitor the health of the server hosting the web API and restart it if necessary.</span></span>

<span data-ttu-id="46ec7-377">讓這些問題能與 Web API 實作的相關技術問題脫鉤，很有助益。</span><span class="sxs-lookup"><span data-stu-id="46ec7-377">It is useful to be able to decouple these issues from the technical issues concerning the implementation of the web API.</span></span> <span data-ttu-id="46ec7-378">基於這個理由，請考慮建立 [façade](https://en.wikipedia.org/wiki/Facade_pattern)，以作為個別的程序來執行，然後讓其將要求路由到 Web API。</span><span class="sxs-lookup"><span data-stu-id="46ec7-378">For this reason, consider creating a [façade](https://en.wikipedia.org/wiki/Facade_pattern), running as a separate process and that routes requests to the web API.</span></span> <span data-ttu-id="46ec7-379">façade 可以提供管理作業，並將經過驗證的要求轉送給 Web API。</span><span class="sxs-lookup"><span data-stu-id="46ec7-379">The façade can provide the management operations and forward validated requests to the web API.</span></span> <span data-ttu-id="46ec7-380">使用 façade 也會帶來許多功能上的優點，包括：</span><span class="sxs-lookup"><span data-stu-id="46ec7-380">Using a façade can also bring many functional advantages, including:</span></span>

- <span data-ttu-id="46ec7-381">作為多個 Web API 的整合點。</span><span class="sxs-lookup"><span data-stu-id="46ec7-381">Acting as an integration point for multiple web APIs.</span></span>
- <span data-ttu-id="46ec7-382">針對使用各種技術建置的用戶端，轉換訊息和轉譯通訊協定。</span><span class="sxs-lookup"><span data-stu-id="46ec7-382">Transforming messages and translating communications protocols for clients built by using varying technologies.</span></span>
- <span data-ttu-id="46ec7-383">快取要求和回應，以減少裝載 Web API 之伺服器的負載。</span><span class="sxs-lookup"><span data-stu-id="46ec7-383">Caching requests and responses to reduce load on the server hosting the web API.</span></span>

## <a name="testing-a-web-api"></a><span data-ttu-id="46ec7-384">測試 Web API</span><span class="sxs-lookup"><span data-stu-id="46ec7-384">Testing a web API</span></span>

<span data-ttu-id="46ec7-385">Web API 應與軟體的任何其他部分一樣徹底進行測試。</span><span class="sxs-lookup"><span data-stu-id="46ec7-385">A web API should be tested as thoroughly as any other piece of software.</span></span> <span data-ttu-id="46ec7-386">您應該考慮建立單元測試來驗證功能。</span><span class="sxs-lookup"><span data-stu-id="46ec7-386">You should consider creating unit tests to validate the functionality.</span></span>

<span data-ttu-id="46ec7-387">Web API 的本質帶來自己額外的需求，以便確認運作正常。</span><span class="sxs-lookup"><span data-stu-id="46ec7-387">The nature of a web API brings its own additional requirements to verify that it operates correctly.</span></span> <span data-ttu-id="46ec7-388">您應該特別注意下列層面：</span><span class="sxs-lookup"><span data-stu-id="46ec7-388">You should pay particular attention to the following aspects:</span></span>

- <span data-ttu-id="46ec7-389">測試所有路由以確認它們叫用正確的作業。</span><span class="sxs-lookup"><span data-stu-id="46ec7-389">Test all routes to verify that they invoke the correct operations.</span></span> <span data-ttu-id="46ec7-390">請特別留意意外傳回的 HTTP 狀態碼 405 (不允許的方法)，因為這可能表示路由與可分派到該路由的 HTTP 方法 (GET、POST、PUT、DELETE) 不相符。</span><span class="sxs-lookup"><span data-stu-id="46ec7-390">Be especially aware of HTTP status code 405 (Method Not Allowed) being returned unexpectedly as this can indicate a mismatch between a route and the HTTP methods (GET, POST, PUT, DELETE) that can be dispatched to that route.</span></span>

    <span data-ttu-id="46ec7-391">將 HTTP 要求傳送到不提供支援的路由，例如將 POST 要求提交至特定資源 (POST 要求只能傳送至資源集合)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-391">Send HTTP requests to routes that do not support them, such as submitting a POST request to a specific resource (POST requests should only be sent to resource collections).</span></span> <span data-ttu-id="46ec7-392">在這些情況下，唯一有效的回應「應該」是狀態碼 405 (不允許)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-392">In these cases, the only valid response *should* be status code 405 (Not Allowed).</span></span>

- <span data-ttu-id="46ec7-393">確認所有路由都得到適當保護，而且都遵守適當的驗證和授權檢查。</span><span class="sxs-lookup"><span data-stu-id="46ec7-393">Verify that all routes are protected properly and are subject to the appropriate authentication and authorization checks.</span></span>

  > [!NOTE]
  > <span data-ttu-id="46ec7-394">某些安全性層面 (例如使用者驗證) 大都可能是主機環境 (而不是 Web API) 的責任，但仍必須將安全性測試納入部署程序中。</span><span class="sxs-lookup"><span data-stu-id="46ec7-394">Some aspects of security such as user authentication are most likely to be the responsibility of the host environment rather than the web API, but it is still necessary to include security tests as part of the deployment process.</span></span>
  >
  >

- <span data-ttu-id="46ec7-395">測試每項作業執行的例外狀況處理，並確認已將適當且有意義的 HTTP 回應傳遞回用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="46ec7-395">Test the exception handling performed by each operation and verify that an appropriate and meaningful HTTP response is passed back to the client application.</span></span>

- <span data-ttu-id="46ec7-396">驗證要求和回應訊息的格式是否正確。</span><span class="sxs-lookup"><span data-stu-id="46ec7-396">Verify that request and response messages are well-formed.</span></span> <span data-ttu-id="46ec7-397">例如，如果 HTTP POST 要求包含新資源的資料 (採用 x-www-form-urlencoded 格式)，請確認對應的作業正確剖析資料、建立資源，並傳回包含新資源詳細資料的回應 (包括正確的 Location 標頭)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-397">For example, if an HTTP POST request contains the data for a new resource in x-www-form-urlencoded format, confirm that the corresponding operation correctly parses the data, creates the resources, and returns a response containing the details of the new resource, including the correct Location header.</span></span>

- <span data-ttu-id="46ec7-398">驗證回應訊息中的所有連結和 URI。</span><span class="sxs-lookup"><span data-stu-id="46ec7-398">Verify all links and URIs in response messages.</span></span> <span data-ttu-id="46ec7-399">例如，HTTP POST 訊息應該會傳回新建資源的 URI。</span><span class="sxs-lookup"><span data-stu-id="46ec7-399">For example, an HTTP POST message should return the URI of the newly-created resource.</span></span> <span data-ttu-id="46ec7-400">所有 HATEOAS 連結都應該有效。</span><span class="sxs-lookup"><span data-stu-id="46ec7-400">All HATEOAS links should be valid.</span></span>

- <span data-ttu-id="46ec7-401">確保每項作業都會針對不同的輸入組合傳回正確的狀態碼。</span><span class="sxs-lookup"><span data-stu-id="46ec7-401">Ensure that each operation returns the correct status codes for different combinations of input.</span></span> <span data-ttu-id="46ec7-402">例如︰</span><span class="sxs-lookup"><span data-stu-id="46ec7-402">For example:</span></span>

  - <span data-ttu-id="46ec7-403">如果查詢成功，則應傳回狀態碼 200 (確定)</span><span class="sxs-lookup"><span data-stu-id="46ec7-403">If a query is successful, it should return status code 200 (OK)</span></span>
  - <span data-ttu-id="46ec7-404">如果找不到資源，作業應傳回 HTTP 狀態碼 404 (找不到)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-404">If a resource is not found, the operation should return HTTP status code 404 (Not Found).</span></span>
  - <span data-ttu-id="46ec7-405">如果用戶端傳送可成功刪除資源的要求，狀態碼應該是 204 (沒有內容)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-405">If the client sends a request that successfully deletes a resource, the status code should be 204 (No Content).</span></span>
  - <span data-ttu-id="46ec7-406">如果用戶端傳送可建立新資源的要求，狀態碼應該是 201 (已建立)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-406">If the client sends a request that creates a new resource, the status code should be 201 (Created).</span></span>

<span data-ttu-id="46ec7-407">請提防 5xx 範圍中的非預期回應狀態碼。</span><span class="sxs-lookup"><span data-stu-id="46ec7-407">Watch out for unexpected response status codes in the 5xx range.</span></span> <span data-ttu-id="46ec7-408">這些訊息通常是主機伺服器的回報，指出表示它無法履行某個有效的要求。</span><span class="sxs-lookup"><span data-stu-id="46ec7-408">These messages are usually reported by the host server to indicate that it was unable to fulfill a valid request.</span></span>

- <span data-ttu-id="46ec7-409">測試用戶端應用程式可以指定的不同要求標頭組合，並確保 Web API 在回應訊息中傳回預期的資訊。</span><span class="sxs-lookup"><span data-stu-id="46ec7-409">Test the different request header combinations that a client application can specify and ensure that the web API returns the expected information in response messages.</span></span>

- <span data-ttu-id="46ec7-410">測試查詢字串。</span><span class="sxs-lookup"><span data-stu-id="46ec7-410">Test query strings.</span></span> <span data-ttu-id="46ec7-411">如果作業可採用選擇性參數 (例如分頁要求)，請測試不同組合和順序的參數。</span><span class="sxs-lookup"><span data-stu-id="46ec7-411">If an operation can take optional parameters (such as pagination requests), test the different combinations and order of parameters.</span></span>

- <span data-ttu-id="46ec7-412">確認非同步作業順利完成。</span><span class="sxs-lookup"><span data-stu-id="46ec7-412">Verify that asynchronous operations complete successfully.</span></span> <span data-ttu-id="46ec7-413">如果 Web API 支援傳回大型二進位物件 (例如視訊或音訊) 之要求的串流處理，請確保在串流處理資料時不會封鎖用戶端要求。</span><span class="sxs-lookup"><span data-stu-id="46ec7-413">If the web API supports streaming for requests that return large binary objects (such as video or audio), ensure that client requests are not blocked while the data is streamed.</span></span> <span data-ttu-id="46ec7-414">如果 Web API 對長時間執行的資料修改作業進行輪詢，請確認作業在進行時正確回報其狀態。</span><span class="sxs-lookup"><span data-stu-id="46ec7-414">If the web API implements polling for long-running data modification operations, verify that that the operations report their status correctly as they proceed.</span></span>

<span data-ttu-id="46ec7-415">您也應該建立和執行效能測試，檢查 Web API 在受限的情況下是否運作順利。</span><span class="sxs-lookup"><span data-stu-id="46ec7-415">You should also create and run performance tests to check that the web API operates satisfactorily under duress.</span></span> <span data-ttu-id="46ec7-416">您可以使用 Visual Studio Ultimate，建置 Web 效能和負載測試專案。</span><span class="sxs-lookup"><span data-stu-id="46ec7-416">You can build a web performance and load test project by using Visual Studio Ultimate.</span></span> <span data-ttu-id="46ec7-417">如需詳細資訊，請參閱[在應用程式發行前執行效能測試](https://msdn.microsoft.com/library/dn250793.aspx)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-417">For more information, see [Run performance tests on an application before a release](https://msdn.microsoft.com/library/dn250793.aspx).</span></span>

## <a name="using-azure-api-management"></a><span data-ttu-id="46ec7-418">使用 Azure API 管理</span><span class="sxs-lookup"><span data-stu-id="46ec7-418">Using Azure API Management</span></span>

<span data-ttu-id="46ec7-419">在 Azure 上，請考慮使用 [Azue API 管理](/azure/api-management//services/api-management/)來發行和管理 Web API。</span><span class="sxs-lookup"><span data-stu-id="46ec7-419">On Azure, consider using [Azue API Management](/azure/api-management//services/api-management/) to publish and manage a web API.</span></span> <span data-ttu-id="46ec7-420">藉由使用此功能，您可以針對一個或多個 Web API 產生作為 façade 的服務。</span><span class="sxs-lookup"><span data-stu-id="46ec7-420">Using this facility, you can generate a service that acts as a façade for one or more web APIs.</span></span> <span data-ttu-id="46ec7-421">此服務本身是可使用 Azure 管理入口網站建立及設定的可延伸 Web 服務。</span><span class="sxs-lookup"><span data-stu-id="46ec7-421">The service is itself a scalable web service that you can create and configure by using the Azure Management portal.</span></span> <span data-ttu-id="46ec7-422">您可以使用這項服務來發佈和管理 Web API，如下所示：</span><span class="sxs-lookup"><span data-stu-id="46ec7-422">You can use this service to publish and manage a web API as follows:</span></span>

1. <span data-ttu-id="46ec7-423">將 Web API 部署到網站、Azure 雲端服務或 Azure 虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="46ec7-423">Deploy the web API to a website, Azure cloud service, or Azure virtual machine.</span></span>

2. <span data-ttu-id="46ec7-424">將 API 管理服務連接到 Web API。</span><span class="sxs-lookup"><span data-stu-id="46ec7-424">Connect the API management service to the web API.</span></span> <span data-ttu-id="46ec7-425">傳送至管理 API 之 URL 的要求會對應至 Web API 中的 URI。</span><span class="sxs-lookup"><span data-stu-id="46ec7-425">Requests sent to the URL of the management API are mapped to URIs in the web API.</span></span> <span data-ttu-id="46ec7-426">相同的 API 管理服務可將要求路由傳送到多個 Web API。</span><span class="sxs-lookup"><span data-stu-id="46ec7-426">The same API management service can route requests to more than one web API.</span></span> <span data-ttu-id="46ec7-427">這可讓您將多個 Web API 彙總成單一管理服務。</span><span class="sxs-lookup"><span data-stu-id="46ec7-427">This enables you to aggregate multiple web APIs into a single management service.</span></span> <span data-ttu-id="46ec7-428">同樣地，如果您需要限制或分割不同應用程式可用的功能，可以從一個以上的 API 管理服務參考相同的 Web API。</span><span class="sxs-lookup"><span data-stu-id="46ec7-428">Similarly, the same web API can be referenced from more than one API management service if you need to restrict or partition the functionality available to different applications.</span></span>

     > [!NOTE]
     > <span data-ttu-id="46ec7-429">HATEOAS 連結中產生成為 HTTP GET 要求的回應一部分的 URI，應參考 API 管理服務 (而非裝載 Web API 的 Web 伺服器) 的 URL。</span><span class="sxs-lookup"><span data-stu-id="46ec7-429">The URIs in HATEOAS links generated as part of the response for HTTP GET requests should reference the URL of the API management service and not the web server hosting the web API.</span></span>

3. <span data-ttu-id="46ec7-430">對每個 Web API，指定 Web API 公開的 HTTP 作業，以及作業可作為輸入使用的任何選擇性參數。</span><span class="sxs-lookup"><span data-stu-id="46ec7-430">For each web API, specify the HTTP operations that the web API exposes together with any optional parameters that an operation can take as input.</span></span> <span data-ttu-id="46ec7-431">您也可以設定 API 管理服務是否應快取從 Web API 收到的回應，以最佳化相同資料的重複要求。</span><span class="sxs-lookup"><span data-stu-id="46ec7-431">You can also configure whether the API management service should cache the response received from the web API to optimize repeated requests for the same data.</span></span> <span data-ttu-id="46ec7-432">記錄每項作業可產生之 HTTP 回應的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-432">Record the details of the HTTP responses that each operation can generate.</span></span> <span data-ttu-id="46ec7-433">此資訊用來產生開發人員適用的文件，因此一定要精確且完整。</span><span class="sxs-lookup"><span data-stu-id="46ec7-433">This information is used to generate documentation for developers, so it is important that it is accurate and complete.</span></span>

    <span data-ttu-id="46ec7-434">您可以使用 Azure 管理入口網站提供的精靈手動定義作業，也可以從含有 WADL 或 Swagger 格式之定義的檔案匯入它們。</span><span class="sxs-lookup"><span data-stu-id="46ec7-434">You can either define operations manually using the wizards provided by the Azure Management portal, or you can import them from a file containing the definitions in WADL or Swagger format.</span></span>

4. <span data-ttu-id="46ec7-435">設定 API 管理服務與裝載 Web API 的 Web 伺服器之間通訊的安全性設定。</span><span class="sxs-lookup"><span data-stu-id="46ec7-435">Configure the security settings for communications between the API management service and the web server hosting the web API.</span></span> <span data-ttu-id="46ec7-436">API 管理服務目前支援使用憑證的基本驗證和相互驗證，以及 OAuth 2.0 使用者授權。</span><span class="sxs-lookup"><span data-stu-id="46ec7-436">The API management service currently supports Basic authentication and mutual authentication using certificates, and OAuth 2.0 user authorization.</span></span>

5. <span data-ttu-id="46ec7-437">建立產品。</span><span class="sxs-lookup"><span data-stu-id="46ec7-437">Create a product.</span></span> <span data-ttu-id="46ec7-438">產品是發佈的單位；您會將先前連接的 Web API 加入至產品的管理服務。</span><span class="sxs-lookup"><span data-stu-id="46ec7-438">A product is the unit of publication; you add the web APIs that you previously connected to the management service to the product.</span></span> <span data-ttu-id="46ec7-439">發佈產品時，Web API 即可供開發人員使用。</span><span class="sxs-lookup"><span data-stu-id="46ec7-439">When the product is published, the web APIs become available to developers.</span></span>

    > [!NOTE]
    > <span data-ttu-id="46ec7-440">在發佈產品之前，您也可以定義可存取產品的使用者群組，並將使用者加入至這些群組。</span><span class="sxs-lookup"><span data-stu-id="46ec7-440">Prior to publishing a product, you can also define user-groups that can access the product and add users to these groups.</span></span> <span data-ttu-id="46ec7-441">這可讓您控制可使用 Web API 的開發人員和應用程式。</span><span class="sxs-lookup"><span data-stu-id="46ec7-441">This gives you control over the developers and applications that can use the web API.</span></span> <span data-ttu-id="46ec7-442">如果 Web API 需獲得核准，開發人員必須先將要求傳送給產品系統管理員，才能夠加以存取。</span><span class="sxs-lookup"><span data-stu-id="46ec7-442">If a web API is subject to approval, prior to being able to access it a developer must send a request to the product administrator.</span></span> <span data-ttu-id="46ec7-443">系統管理員可以授與或拒絕程式開發人員的存取。</span><span class="sxs-lookup"><span data-stu-id="46ec7-443">The administrator can grant or deny access to the developer.</span></span> <span data-ttu-id="46ec7-444">如果情況變更，也可能會封鎖現有的開發人員。</span><span class="sxs-lookup"><span data-stu-id="46ec7-444">Existing developers can also be blocked if circumstances change.</span></span>

6. <span data-ttu-id="46ec7-445">設定每個 Web API 的原則。</span><span class="sxs-lookup"><span data-stu-id="46ec7-445">Configure policies for each web API.</span></span> <span data-ttu-id="46ec7-446">原則可管理許多層面，例如是否應該允許跨網域呼叫、如何驗證用戶端、是否以透明方式在 XML 與 JSON 資料格式間轉換、是否限制來自指定 IP 範圍的呼叫、使用量配額，以及是否限制呼叫率。</span><span class="sxs-lookup"><span data-stu-id="46ec7-446">Policies govern aspects such as whether cross-domain calls should be allowed, how to authenticate clients, whether to convert between XML and JSON data formats transparently, whether to restrict calls from a given IP range, usage quotas, and whether to limit the call rate.</span></span> <span data-ttu-id="46ec7-447">原則可以全面套用於整個產品、針對產品中的單一 Web API 套用，或針對 Web API 中的個別作業套用。</span><span class="sxs-lookup"><span data-stu-id="46ec7-447">Policies can be applied globally across the entire product, for a single web API in a product, or for individual operations in a web API.</span></span>

<span data-ttu-id="46ec7-448">如需詳細資訊，請參閱 [API 管理文件](/azure/api-management/)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-448">For more information, see the [API Management Documentation](/azure/api-management/).</span></span>

> [!TIP]
> <span data-ttu-id="46ec7-449">Azure 提供了 Azure 流量管理員，可讓您實作容錯移轉和負載平衡，並且讓網站上裝載於不同地理位置的多個執行個體減少延遲。</span><span class="sxs-lookup"><span data-stu-id="46ec7-449">Azure provides the Azure Traffic Manager which enables you to implement failover and load-balancing, and reduce latency across multiple instances of a web site hosted in different geographic locations.</span></span> <span data-ttu-id="46ec7-450">您可以使用 Azure 流量管理員搭配 API 管理服務；API 管理服務可以透過 Azure 流量管理員，將要求路由傳送至網站的執行個體。</span><span class="sxs-lookup"><span data-stu-id="46ec7-450">You can use Azure Traffic Manager in conjunction with the API Management Service; the API Management Service can route requests to instances of a web site through Azure Traffic Manager.</span></span> <span data-ttu-id="46ec7-451">如需詳細資訊，請參閱[流量管理員路由方法](/azure/traffic-manager/traffic-manager-routing-methods/)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-451">For more information, see [Traffic Manager routing Methods](/azure/traffic-manager/traffic-manager-routing-methods/).</span></span>
>
> <span data-ttu-id="46ec7-452">在此結構中，如果您使用您網站的自訂 DNS 名稱，則應該為每個網站設定適當的 CNAME 記錄，以指向 Azure 流量管理員網站的 DNS 名稱。</span><span class="sxs-lookup"><span data-stu-id="46ec7-452">In this structure, if you are using custom DNS names for your web sites, you should configure the appropriate CNAME record for each web site to point to the DNS name of the Azure Traffic Manager web site.</span></span>

## <a name="supporting-client-side-developers"></a><span data-ttu-id="46ec7-453">支援用戶端開發人員</span><span class="sxs-lookup"><span data-stu-id="46ec7-453">Supporting client-side developers</span></span>

<span data-ttu-id="46ec7-454">建構用戶端應用程式的開發人員通常需要有關如何存取 Web API 的資訊，以及有關參數、資料類型、傳回型別和傳回碼 (描述 Web 服務與用戶端應用程式間的不同要求和回應) 的文件。</span><span class="sxs-lookup"><span data-stu-id="46ec7-454">Developers constructing client applications typically require information on how to access the web API, and documentation concerning the parameters, data types, return types, and return codes that describe the different requests and responses between the web service and the client application.</span></span>

### <a name="document-the-rest-operations-for-a-web-api"></a><span data-ttu-id="46ec7-455">記載 Web API 的 REST 作業</span><span class="sxs-lookup"><span data-stu-id="46ec7-455">Document the REST operations for a web API</span></span>

<span data-ttu-id="46ec7-456">Azure API 管理服務包括可描述 Web API 所公開之 REST 作業的開發人員入口網站。</span><span class="sxs-lookup"><span data-stu-id="46ec7-456">The Azure API Management Service includes a developer portal that describes the REST operations exposed by a web API.</span></span> <span data-ttu-id="46ec7-457">產品發佈後就會顯示在此入口網站上。</span><span class="sxs-lookup"><span data-stu-id="46ec7-457">When a product has been published it appears on this portal.</span></span> <span data-ttu-id="46ec7-458">開發人員可以使用此入口網站來註冊存取權；系統管理員即可核准或拒絕此要求。</span><span class="sxs-lookup"><span data-stu-id="46ec7-458">Developers can use this portal to sign up for access; the administrator can then approve or deny the request.</span></span> <span data-ttu-id="46ec7-459">如果開發人員獲得核准，就會指派訂用帳戶金鑰給他們，以便用來驗證來自他們所開發的用戶端應用程式呼叫。</span><span class="sxs-lookup"><span data-stu-id="46ec7-459">If the developer is approved, they are assigned a subscription key that is used to authenticate calls from the client applications that they develop.</span></span> <span data-ttu-id="46ec7-460">此金鑰必須隨著每個 Web API 呼叫提供，否則會遭到拒絕。</span><span class="sxs-lookup"><span data-stu-id="46ec7-460">This key must be provided with each web API call otherwise it will be rejected.</span></span>

<span data-ttu-id="46ec7-461">此入口網站也提供：</span><span class="sxs-lookup"><span data-stu-id="46ec7-461">This portal also provides:</span></span>

- <span data-ttu-id="46ec7-462">產品文件，其中列出所公開的作業、必要的參數，以及可以傳回的各種回應。</span><span class="sxs-lookup"><span data-stu-id="46ec7-462">Documentation for the product, listing the operations that it exposes, the parameters required, and the different responses that can be returned.</span></span> <span data-ttu-id="46ec7-463">請注意，此資訊是根據「使用 Microsoft Azure API 管理服務發佈 Web API」一節的清單中步驟 3 所提供的詳細資料產生。</span><span class="sxs-lookup"><span data-stu-id="46ec7-463">Note that this information is generated from the details provided in step 3 in the list in the Publishing a web API by using the Microsoft Azure API Management Service section.</span></span>
- <span data-ttu-id="46ec7-464">程式碼片段，示範如何以數種語言 (包括 JavaScript、C#、Java、Ruby、Python 和 PHP) 叫用作業。</span><span class="sxs-lookup"><span data-stu-id="46ec7-464">Code snippets that show how to invoke operations from several languages, including JavaScript, C#, Java, Ruby, Python, and PHP.</span></span>
- <span data-ttu-id="46ec7-465">開發人員的主控台，讓開發人員傳送 HTTP 要求，以在產品中測試每項作業並檢視結果。</span><span class="sxs-lookup"><span data-stu-id="46ec7-465">A developers' console that enables a developer to send an HTTP request to test each operation in the product and view the results.</span></span>
- <span data-ttu-id="46ec7-466">一個頁面，以供開發人員回報任何找到的問題。</span><span class="sxs-lookup"><span data-stu-id="46ec7-466">A page where the developer can report any issues or problems found.</span></span>

<span data-ttu-id="46ec7-467">Azure 管理入口網站可讓您自訂開發人員入口網站來變更樣式和版面配置，以符合貴組織的商標。</span><span class="sxs-lookup"><span data-stu-id="46ec7-467">The Azure Management portal enables you to customize the developer portal to change the styling and layout to match the branding of your organization.</span></span>

### <a name="implement-a-client-sdk"></a><span data-ttu-id="46ec7-468">實作用戶端 SDK</span><span class="sxs-lookup"><span data-stu-id="46ec7-468">Implement a client SDK</span></span>

<span data-ttu-id="46ec7-469">建置可叫用 REST 要求以存取 Web API 的用戶端應用程式時，需要撰寫大量的程式碼來建構每個要求並進行適當格式化、將要求傳送至裝載 Web 服務的伺服器，以及剖析回應來判斷要求成功或失敗並擷取傳回的任何資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-469">Building a client application that invokes REST requests to access a web API requires writing a significant amount of code to construct each request and format it appropriately, send the request to the server hosting the web service, and parse the response to work out whether the request succeeded or failed and extract any data returned.</span></span> <span data-ttu-id="46ec7-470">若要使用戶端應用程式免除這些顧慮，您可以提供 SDK，以供包裝 REST 介面以及在一組功能更強的方法中摘錄這些低階詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="46ec7-470">To insulate the client application from these concerns, you can provide an SDK that wraps the REST interface and abstracts these low-level details inside a more functional set of methods.</span></span> <span data-ttu-id="46ec7-471">用戶端應用程式會使用這些方法，以透明方式將呼叫轉換成 REST 要求，然後將回應轉換回方法傳回值。</span><span class="sxs-lookup"><span data-stu-id="46ec7-471">A client application uses these methods, which transparently convert calls into REST requests and then convert the responses back into method return values.</span></span> <span data-ttu-id="46ec7-472">這是由許多服務實作的通用技術，包括 Azure SDK。</span><span class="sxs-lookup"><span data-stu-id="46ec7-472">This is a common technique that is implemented by many services, including the Azure SDK.</span></span>

<span data-ttu-id="46ec7-473">建立用戶端 SDK 是相當重要的工作，因為這項作業必須以一致方式實作並小心進行測試。</span><span class="sxs-lookup"><span data-stu-id="46ec7-473">Creating a client-side SDK is a considerable undertaking as it has to be implemented consistently and tested carefully.</span></span> <span data-ttu-id="46ec7-474">不過，此程序大都可以機械方式進行，而且許多廠商都提供可自動執行許多工作的工具。</span><span class="sxs-lookup"><span data-stu-id="46ec7-474">However, much of this process can be made mechanical, and many vendors supply tools that can automate many of these tasks.</span></span>

## <a name="monitoring-a-web-api"></a><span data-ttu-id="46ec7-475">監控 Web API</span><span class="sxs-lookup"><span data-stu-id="46ec7-475">Monitoring a web API</span></span>

<span data-ttu-id="46ec7-476">視您發佈和部署 Web API 的方式而定，您可以直接監控 Web API，也可以藉由分析通過 API 管理服務的流量來收集使用量和健全狀況資訊。</span><span class="sxs-lookup"><span data-stu-id="46ec7-476">Depending on how you have published and deployed your web API you can monitor the web API directly, or you can gather usage and health information by analyzing the traffic that passes through the API Management service.</span></span>

### <a name="monitoring-a-web-api-directly"></a><span data-ttu-id="46ec7-477">直接監控 Web API</span><span class="sxs-lookup"><span data-stu-id="46ec7-477">Monitoring a web API directly</span></span>

<span data-ttu-id="46ec7-478">如果您已使用 ASP.NET Web API 範本 (以 Azure 雲端服務中的 Web API 專案或 Web 角色) 和 Visual Studio 2013 實作您的 Web API，您可以使用 ASP.NET Application Insights 來收集可用性、效能和使用量資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-478">If you have implemented your web API by using the ASP.NET Web API template (either as a Web API project or as a Web role in an Azure cloud service) and Visual Studio 2013, you can gather availability, performance, and usage data by using ASP.NET Application Insights.</span></span> <span data-ttu-id="46ec7-479">Application Insights 是在 Web API 部署到雲端時，會以透明方式追蹤及記錄要求和回應相關資訊的封裝；安裝並設定此封裝後，您不需要在 Web API 中修改任何程式碼即可加以使用。</span><span class="sxs-lookup"><span data-stu-id="46ec7-479">Application Insights is a package that transparently tracks and records information about requests and responses when the web API is deployed to the cloud; once the package is installed and configured, you don't need to amend any code in your web API to use it.</span></span> <span data-ttu-id="46ec7-480">當您將 Web API 部署至 Azure 網站時，系統會檢查所有流量並收集下列統計資料：</span><span class="sxs-lookup"><span data-stu-id="46ec7-480">When you deploy the web API to an Azure web site, all traffic is examined and the following statistics are gathered:</span></span>

- <span data-ttu-id="46ec7-481">伺服器回應時間。</span><span class="sxs-lookup"><span data-stu-id="46ec7-481">Server response time.</span></span>
- <span data-ttu-id="46ec7-482">伺服器要求的數目及每個要求的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-482">Number of server requests and the details of each request.</span></span>
- <span data-ttu-id="46ec7-483">從平均回應時間角度來看的最慢幾項要求。</span><span class="sxs-lookup"><span data-stu-id="46ec7-483">The top slowest requests in terms of average response time.</span></span>
- <span data-ttu-id="46ec7-484">任何失敗要求的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-484">The details of any failed requests.</span></span>
- <span data-ttu-id="46ec7-485">由不同的瀏覽器和使用者代理程式起始的工作階段數目。</span><span class="sxs-lookup"><span data-stu-id="46ec7-485">The number of sessions initiated by different browsers and user agents.</span></span>
- <span data-ttu-id="46ec7-486">最常檢視的頁面 (主要適用於 Web 應用程式，而非 Web API)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-486">The most frequently viewed pages (primarily useful for web applications rather than web APIs).</span></span>
- <span data-ttu-id="46ec7-487">存取 Web API 的不同使用者角色。</span><span class="sxs-lookup"><span data-stu-id="46ec7-487">The different user roles accessing the web API.</span></span>

<span data-ttu-id="46ec7-488">您可以從 Azure 管理入口網站即時檢視此資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-488">You can view this data in real time from the Azure Management portal.</span></span> <span data-ttu-id="46ec7-489">您也可以建立用以監控 Web API 健全狀況的 Web 測試。</span><span class="sxs-lookup"><span data-stu-id="46ec7-489">You can also create webtests that monitor the health of the web API.</span></span> <span data-ttu-id="46ec7-490">Web 測試會傳送定期要求至 Web API 中指定的 URI，並擷取回應。</span><span class="sxs-lookup"><span data-stu-id="46ec7-490">A webtest sends a periodic request to a specified URI in the web API and captures the response.</span></span> <span data-ttu-id="46ec7-491">您可以指定成功回應 (例如 HTTP 狀態碼 200) 的定義，而如果要求未傳回此回應，您可以安排要傳送給系統管理員的警示。</span><span class="sxs-lookup"><span data-stu-id="46ec7-491">You can specify the definition of a successful response (such as HTTP status code 200), and if the request does not return this response you can arrange for an alert to be sent to an administrator.</span></span> <span data-ttu-id="46ec7-492">必要時，系統管理員可以重新啟動裝載 Web API 的伺服器 (如果失敗的話)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-492">If necessary, the administrator can restart the server hosting the web API if it has failed.</span></span>

<span data-ttu-id="46ec7-493">如需詳細資訊，請參閱 [Application Insights - ASP.NET 入門](/azure/application-insights/app-insights-asp-net/)。</span><span class="sxs-lookup"><span data-stu-id="46ec7-493">For more information, see [Application Insights - Get started with ASP.NET](/azure/application-insights/app-insights-asp-net/).</span></span>

### <a name="monitoring-a-web-api-through-the-api-management-service"></a><span data-ttu-id="46ec7-494">透過 API 管理服務監控 Web API</span><span class="sxs-lookup"><span data-stu-id="46ec7-494">Monitoring a web API through the API Management Service</span></span>

<span data-ttu-id="46ec7-495">如果您已使用 API 管理服務發佈您的 Web API，則 Azure 管理入口網站上的 API 管理頁面會包含可讓您檢視服務整體效能的儀表板。</span><span class="sxs-lookup"><span data-stu-id="46ec7-495">If you have published your web API by using the API Management service, the API Management page on the Azure Management portal contains a dashboard that enables you to view the overall performance of the service.</span></span> <span data-ttu-id="46ec7-496">[分析] 頁面可讓您向下鑽研產品使用方式的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-496">The Analytics page enables you to drill down into the details of how the product is being used.</span></span> <span data-ttu-id="46ec7-497">本頁面包含下列索引標籤：</span><span class="sxs-lookup"><span data-stu-id="46ec7-497">This page contains the following tabs:</span></span>

- <span data-ttu-id="46ec7-498">**使用量**。</span><span class="sxs-lookup"><span data-stu-id="46ec7-498">**Usage**.</span></span> <span data-ttu-id="46ec7-499">此索引標籤提供已進行的 API 呼叫數目以及用來處理這些呼叫的頻寬相關資訊。</span><span class="sxs-lookup"><span data-stu-id="46ec7-499">This tab provides information about the number of API calls made and the bandwidth used to handle these calls over time.</span></span> <span data-ttu-id="46ec7-500">您可以依照產品、API 和作業篩選使用量詳細資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-500">You can filter usage details by product, API, and operation.</span></span>
- <span data-ttu-id="46ec7-501">**健全狀況**。</span><span class="sxs-lookup"><span data-stu-id="46ec7-501">**Health**.</span></span> <span data-ttu-id="46ec7-502">此索引標籤可讓您檢視 API 要求的結果 (傳回的 HTTP 狀態碼)、快取原則成效、API 回應時間和服務回應時間。</span><span class="sxs-lookup"><span data-stu-id="46ec7-502">This tab enables you to view the outcome of API requests (the HTTP status codes returned), the effectiveness of the caching policy, the API response time, and the service response time.</span></span> <span data-ttu-id="46ec7-503">同樣地，您可以依照產品、API 和作業篩選健全狀況資料。</span><span class="sxs-lookup"><span data-stu-id="46ec7-503">Again, you can filter health data by product, API, and operation.</span></span>
- <span data-ttu-id="46ec7-504">**活動**。</span><span class="sxs-lookup"><span data-stu-id="46ec7-504">**Activity**.</span></span> <span data-ttu-id="46ec7-505">此索引標籤提供成功呼叫、失敗呼叫、封鎖呼叫數目、平均回應時間以及各產品、Web API 和作業之回應時間的文字摘要。</span><span class="sxs-lookup"><span data-stu-id="46ec7-505">This tab provides a text summary of the numbers of successful calls, failed calls, blocked calls, average response time, and response times for each product, web API, and operation.</span></span> <span data-ttu-id="46ec7-506">本頁面也會列出每位開發人員所進行的呼叫數目。</span><span class="sxs-lookup"><span data-stu-id="46ec7-506">This page also lists the number of calls made by each developer.</span></span>
- <span data-ttu-id="46ec7-507">**速覽**。</span><span class="sxs-lookup"><span data-stu-id="46ec7-507">**At a glance**.</span></span> <span data-ttu-id="46ec7-508">此索引標籤會顯示效能資料的摘要，包括負責進行大部分 API 呼叫的開發人員，以及收到這些呼叫的產品、Web API 和作業。</span><span class="sxs-lookup"><span data-stu-id="46ec7-508">This tab displays a summary of the performance data, including the developers responsible for making the most API calls, and the products, web APIs, and operations that received these calls.</span></span>

<span data-ttu-id="46ec7-509">您可以使用這項資訊來判斷是否特定 Web API 或作業造成瓶頸，以及視需要調整主機環境並加入更多伺服器。</span><span class="sxs-lookup"><span data-stu-id="46ec7-509">You can use this information to determine whether a particular web API or operation is causing a bottleneck, and if necessary scale the host environment and add more servers.</span></span> <span data-ttu-id="46ec7-510">您也可以確定一或多個應用程式是否使用不相稱的資源量，並套用適當的原則來設定配額及限制呼叫率。</span><span class="sxs-lookup"><span data-stu-id="46ec7-510">You can also ascertain whether one or more applications are using a disproportionate volume of resources and apply the appropriate policies to set quotas and limit call rates.</span></span>

> [!NOTE]
> <span data-ttu-id="46ec7-511">您可以變更已發佈產品的詳細資料，而您所做的變更會立即套用。</span><span class="sxs-lookup"><span data-stu-id="46ec7-511">You can change the details for a published product, and the changes are applied immediately.</span></span> <span data-ttu-id="46ec7-512">例如，您可以在 Web API 中新增或移除作業，而不需要重新發佈含有 Web API 的產品。</span><span class="sxs-lookup"><span data-stu-id="46ec7-512">For example, you can add or remove an operation from a web API without requiring that you republish the product that contains the web API.</span></span>

## <a name="more-information"></a><span data-ttu-id="46ec7-513">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="46ec7-513">More information</span></span>

- <span data-ttu-id="46ec7-514">[ASP.NET Web API OData](https://www.asp.net/web-api/overview/odata-support-in-aspnet-web-api) 包含有關使用 ASP.NET 實作 OData Web API 的範例以及進一步資訊。</span><span class="sxs-lookup"><span data-stu-id="46ec7-514">[ASP.NET Web API OData](https://www.asp.net/web-api/overview/odata-support-in-aspnet-web-api) contains examples and further information on implementing an OData web API by using ASP.NET.</span></span>
- <span data-ttu-id="46ec7-515">[Web API 和 Web API OData 中的批次支援簡介](https://blogs.msdn.microsoft.com/webdev/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata/)說明如何使用 OData 在 Web API 中實作批次作業。</span><span class="sxs-lookup"><span data-stu-id="46ec7-515">[Introducing batch support in Web API and Web API OData](https://blogs.msdn.microsoft.com/webdev/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata/) describes how to implement batch operations in a web API by using OData.</span></span>
- <span data-ttu-id="46ec7-516">Jonathan Oliver 部落格上的[冪等模式](https://blog.jonathanoliver.com/idempotency-patterns/)提供冪等概觀，以及其與資料管理作業有何相關。</span><span class="sxs-lookup"><span data-stu-id="46ec7-516">[Idempotency patterns](https://blog.jonathanoliver.com/idempotency-patterns/) on Jonathan Oliver’s blog provides an overview of idempotency and how it relates to data management operations.</span></span>
- <span data-ttu-id="46ec7-517">W3C 網站上的[狀態碼定義](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)包含 HTTP 狀態碼的完整清單及其說明。</span><span class="sxs-lookup"><span data-stu-id="46ec7-517">[Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) on the W3C website contains a full list of HTTP status codes and their descriptions.</span></span>
- <span data-ttu-id="46ec7-518">[使用 WebJob 執行背景工作](/azure/app-service-web/web-sites-create-web-jobs/)提供有關使用 WebJob 執行背景作業的資訊和範例。</span><span class="sxs-lookup"><span data-stu-id="46ec7-518">[Run background tasks with WebJobs](/azure/app-service-web/web-sites-create-web-jobs/) provides information and examples on using WebJobs to perform background operations.</span></span>
- <span data-ttu-id="46ec7-519">[Azure 通知中樞通知使用者](/azure/notification-hubs/notification-hubs-aspnet-backend-windows-dotnet-wns-notification/)顯示如何使用 Azure 通知中樞將非同步回應推送至用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="46ec7-519">[Azure Notification Hubs notify users](/azure/notification-hubs/notification-hubs-aspnet-backend-windows-dotnet-wns-notification/) shows how to use an Azure Notification Hub to push asynchronous responses to client applications.</span></span>
- <span data-ttu-id="46ec7-520">[API 管理](https://azure.microsoft.com/services/api-management/)說明如何發佈產品，以提供受控制且安全的 Web API 存取。</span><span class="sxs-lookup"><span data-stu-id="46ec7-520">[API Management](https://azure.microsoft.com/services/api-management/) describes how to publish a product that provides controlled and secure access to a web API.</span></span>
- <span data-ttu-id="46ec7-521">[Azure APIM REST API 參考](https://msdn.microsoft.com/library/azure/dn776326.aspx)說明如何使用 API 管理 REST API 來建置自訂管理應用程式。</span><span class="sxs-lookup"><span data-stu-id="46ec7-521">[Azure API Management REST API reference](https://msdn.microsoft.com/library/azure/dn776326.aspx) describes how to use the API Management REST API to build custom management applications.</span></span>
- <span data-ttu-id="46ec7-522">[流量管理員路由方法](/azure/traffic-manager/traffic-manager-routing-methods/)摘要說明 Azure 流量管理員如何用來平衡裝載 Web API 的網站上多個執行個體的要求負載。</span><span class="sxs-lookup"><span data-stu-id="46ec7-522">[Traffic Manager routing methods](/azure/traffic-manager/traffic-manager-routing-methods/) summarizes how Azure Traffic Manager can be used to load-balance requests across multiple instances of a website hosting a web API.</span></span>
- <span data-ttu-id="46ec7-523">[Application Insights - 開始使用 ASP.NET](/azure/application-insights/app-insights-asp-net/)提供有關在 ASP.NET Web API 專案中安裝和設定 Application Insights 的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="46ec7-523">[Application Insights - Get started with ASP.NET](/azure/application-insights/app-insights-asp-net/) provides detailed information on installing and configuring Application Insights in an ASP.NET Web API project.</span></span>

<!-- links -->

[api-design]: ./api-design.md

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
ms.openlocfilehash: dcbfa528a4fdd640b08b42904ceadb7a802b806e
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640918"
---
# <a name="api-implementation"></a>API 實作

仔細設計的 RESTful Web API 可定義資源、關係以及用戶端應用程式可存取的導覽配置。 當您實作和部署 Web API 時，您應該考慮裝載 Web API 之環境的實際需求，以及 Web API 的建構方式 (而非資料的邏輯結構)。 本指引著重於實作 Web API 和加以發佈以供用戶端應用程式使用的最佳作法。 如需 Web API 設計的詳細資訊，請參閱 [API 設計指引](/azure/architecture/best-practices/api-design)。

## <a name="processing-requests"></a>處理要求

當您實作程式碼來處理要求時，請考慮下列幾點。

### <a name="get-put-delete-head-and-patch-actions-should-be-idempotent"></a>GET、PUT、DELETE、HEAD 和 PATCH 動作應該是等冪的

實作這些要求的程式碼應該不會造成任何副作用。 對相同資源重複提出的相同要求應該會導致相同的狀態。 例如，將多個 DELETE 要求傳送至相同的 URI 應該有相同的效果，雖然回應訊息中的 HTTP 狀態碼可能不同。 第一個 DELETE 要求可能會傳回狀態碼 204 (沒有內容)，而後續的 DELETE 要求可能會傳回狀態碼 404 (找不到)。

> [!NOTE]
> Jonathan Oliver 部落格上的 [冪等模式](https://blog.jonathanoliver.com/idempotency-patterns/) 一文提供冪等概觀，以及其與資料管理作業有何相關。

### <a name="post-actions-that-create-new-resources-should-not-have-unrelated-side-effects"></a>建立新資源的 POST 動作不應有無關的副作用

如果 POST 要求旨在建立新資源，則應將要求的效果限制於新資源 (如果涉及某種形式的連結，則可能是任何直接相關的資源)。例如，在電子商務系統中，為客戶建立新訂單的 POST 要求也可能會修改庫存水準並產生帳單資訊，但不得修改與訂單不直接相關的資訊，或對系統的整體狀態有任何其他副作用。

### <a name="avoid-implementing-chatty-post-put-and-delete-operations"></a>避免實作繁複的 POST、PUT 和 DELETE 作業

支援對資源集合的 POST、PUT 和 DELETE 要求。 POST 要求可以包含多項新資源的詳細資料，並將它們全部加入相同的集合中，PUT 要求可以取代集合中的整組資源，而 DELETE 要求可以移除整個集合。

ASP.NET Web API 2 內含的 OData 支援可提供批次要求的功能。 用戶端應用程式可以封裝數個 Web API 要求並將它們傳送至單一 HTTP 要求中的伺服器，以及接收包含各要求之回覆的單一 HTTP 回應。 如需詳細資訊，請參閱 [Web API 和 Web API OData 中的 Batch 支援簡介](https://blogs.msdn.microsoft.com/webdev/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata/)。

### <a name="follow-the-http-specification-when-sending-a-response"></a>傳送回應時請遵循 HTTP 規格

Web API 必須傳回包含下列資訊的訊息：正確 HTTP 狀態碼可讓用戶端決定如何處理結果、適當的 HTTP 標頭可讓用戶端了解結果的本質，而適當格式化的內文可讓用戶端剖析結果。

例如，POST 作業應傳回狀態碼 201 (已建立)，而回應訊息的 Location 標頭中應包含新建資源的 URI。

### <a name="support-content-negotiation"></a>支援內容交涉

回應訊息的內文可能包含各種格式的資料。 例如，HTTP GET 要求可傳回 JSON 或 XML 格式的資料。 當用戶端提交要求時，該要求可包含 Accept 標頭以指定可處理的資料格式。 這些格式會被指定為媒體類型。 例如，發出 GET 要求以擷取映像的用戶端可以指定 Accept 標頭，其中列出用戶端可處理的媒體類型，例如 `image/jpeg, image/gif, image/png`。 當 Web API 傳回結果時，它應該使用其中一種媒體類型來格式化資料，並在回應的 Content-Type 標頭中指定格式。

如果用戶端未指定 Accept 標頭，則對回應內文使用合理的預設格式。 例如，對於以文字為基礎的資料，ASP.NET Web API 架構會預設為 JSON。

### <a name="provide-links-to-support-hateoas-style-navigation-and-discovery-of-resources"></a>提供連結，以支援資源的 HATEOAS 式導覽和探索

HATEOAS 方法如何讓用戶端能夠從初始的起點瀏覽及探索資源。 使用包含 URI 的連結即可達到此目的；當用戶端發出 HTTP GET 要求來取得資源時，回應應包含可讓用戶端應用程式快速找出任何直接相關資源的 URI。 例如，在支援電子商務解決方案的 Web API 中，客戶可能已下了許多訂單。 當用戶端應用程式擷取客戶的詳細資料時，回應應包含相關連結，讓用戶端應用程式傳送可擷取這些訂單的 HTTP GET 要求。 此外，HATEOAS 式連結應描述每個連結資源支援的其他作業 (POST、PUT、DELETE 等等)，以及用來執行每個要求的對應 URI。 這種方法會在 [API 設計][api-design]中詳加說明。

目前沒有標準可掌管 HATEOAS 的實作，但下列範例說明一個可能的方法。 在此範例中，尋找客戶詳細資料的 HTTP GET 要求會傳回包含 HATEOAS 連結 (用以參考該客戶的訂單) 的回應：

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

在此範例中，客戶資料是由下列程式碼片段中顯示的 `Customer` 類別表示。 HATEOAS 連結會保留在 `Links` 集合屬性中：

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

HTTP GET 作業會擷取儲存體中的客戶資料並建構 `Customer` 物件，然後再填入 `Links` 集合。 結果會格式化為 JSON 回應訊息。 每個連結都包含下列欄位：

- 要傳回的物件與連結所描述的物件之間的關係。 在此情況下，`self` 表示連結是物件本身的參考 (類似於許多物件導向語言中的 `this` 指標)，而 `orders` 是包含相關訂單資訊的集合名稱。
- 連結所描述物件的超連結 (`Href`) (採用 URI 形式)。
- 可傳送至此 URI 之 HTTP 要求的類型 (`Action`)。
- 視要求的類型而定，應在 HTTP 要求中提供或可在回應中傳回之任何資料的格式 (`Types`)。

範例 HTTP 回應中所示的 HATEOAS 連結表示用戶端應用程式可以執行下列作業：

- 對 URI `https://adventure-works.com/customers/2` 的 HTTP GET 要求，用以 (再次) 擷取客戶的詳細資料。 此資料可以 XML 或 JSON 格式傳回。
- 對 URI `https://adventure-works.com/customers/2` 的 HTTP PUT 要求，用以修改客戶的詳細資料。 新資料必須在要求訊息中以 x-www-form-urlencoded 格式提供。
- 對 URI `https://adventure-works.com/customers/2` 的 HTTP DELETE 要求，用以刪除客戶。 此要求並不預期有任何其他資訊或在回應訊息內文中傳回資料。
- 對 URI `https://adventure-works.com/customers/2/orders` 的 HTTP GET 要求，用以尋找客戶的所有訂單。 此資料可以 XML 或 JSON 格式傳回。
- 對 URI `https://adventure-works.com/customers/2/orders` 的 HTTP PUT 要求，用以建立此客戶的新訂單。 此資料必須在要求訊息中以 x-www-form-urlencoded 格式提供。

## <a name="handling-exceptions"></a>處理例外狀況

如果作業會擲回無法攔截的例外狀況，請考慮下列各點。

### <a name="capture-exceptions-and-return-a-meaningful-response-to-clients"></a>擷取例外狀況，並將有意義的回應傳回給用戶端

實作 HTTP 作業的程式碼應提供完善的例外狀況處理，而不是讓未攔截到的例外狀況傳播到架構。 如果例外狀況使作業無法成功完成，可以在回應訊息中傳回例外狀況，但應包含造成例外狀況之錯誤的有意義說明。 例外狀況也應包含適當的 HTTP 狀態碼，而不只是對每種狀況傳回狀態碼 500。 例如，如果使用者要求造成違反條件約束的資料庫更新 (例如嘗試刪除有未處理訂單的客戶)，您應傳回狀態碼 409 (衝突) 以及可指出衝突原因的訊息內本。 如果某些其他條件呈現要求無法達成，您可以傳回狀態碼 400 (不正確的要求)。 您可以在 W3C 網站上的 [狀態碼定義](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 頁面找到 HTTP 狀態碼的完整清單。

此程式碼範例會設陷不同的條件，並傳回適當的回應。

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
> 請勿包含對嘗試侵入您的 API 攻擊者可能有幫助的資訊。
  
許多 Web 伺服器會在連上 Web API 之前，自行設陷錯誤條件。 例如，如果您設定網站的驗證，而使用者無法提供正確的驗證資訊，Web 伺服器應該以狀態碼 401 (未經授權) 回應。 用戶端經過驗證後，您的程式碼可以執行自己的檢查，確認用戶端應能夠存取所要求的資源。 如果此授權失敗，您應該傳回狀態碼 403 (禁止)。

### <a name="handle-exceptions-consistently-and-log-information-about-errors"></a>以一致的方式處理例外狀況並記錄有關錯誤的資訊

若要以一致的方式處理例外狀況，請考慮在整個 Web API 實作全域錯誤處理策略。 您也應該併入錯誤記錄，以擷取每種例外狀況的完整詳細資料；此錯誤記錄檔可包含詳細的資訊 (只要用戶端無法透過 Web 存取即可)。

### <a name="distinguish-between-client-side-errors-and-server-side-errors"></a>區分用戶端錯誤與伺服器端錯誤

HTTP 通訊協定會區分用戶端應用程式 (HTTP 4xx 狀態碼) 所引起的錯誤，與伺服器災難所造成的錯誤 (HTTP 5xx 狀態碼)。 請務必遵守任何錯誤回應訊息中的這個慣例。

## <a name="optimizing-client-side-data-access"></a>將用戶端資料存取最佳化

在涉及 Web 伺服器和用戶端應用程式的分散式環境中，網路是其中一個主要憂心來源。 這可能會是相當大的瓶頸，特別是在用戶端應用程式經常傳送要求或接收資料時。 因此，您的目標應該在將網路的傳輸流量降到最低。 當您實作程式碼來擷取和維護資料時，請考慮下列幾點：

### <a name="support-client-side-caching"></a>支援用戶端快取

HTTP 1.1 通訊協定支援在用戶端和中繼伺服器中的快取，要求會利用 Cache-Control 標頭透過它路由傳送。 當用戶端應用程式將 HTTP GET 要求傳送至 Web API，回應可以包含 Cache-Control 標頭，指出此用戶端或用以路由傳送此要求的中繼伺服器是否可以安全地快取回應內文中的資料，以及它多久後會到期並視為過期。 下列範例會顯示 HTTP GET 要求和包含 Cache-Control 標頭的對應回應：

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

在此範例中，Cache-Control 指定傳回的資料應在 600 秒後過期，僅適用於單一用戶端且不得儲存在其他用戶端所使用的共用快取中 (它是 private)。 Cache-Control 標頭可以指定 public 而非 private，在此情況下資料可以儲存在共用快取中，也可以指定 no-store，在此情況下資料必須**不是**由用戶端快取。 下列程式碼範例示範如何在回應訊息中建構 Cache-Control 標頭：

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

此程式碼會利用名為 `OkResultWithCaching` 的自訂 `IHttpActionResult` 類別。 這個類別可讓控制器設定快取標頭內容：

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
> HTTP 通訊協定也會定義 Cache-Control 標頭的 *no-cache* 指示詞。 令人不解的是，此指示詞並非表示「不快取 」，而是「在傳回快取的資訊前使用伺服器進行重新驗證」；此資料仍會快取，但每次用來確保其仍為當前資料時會進行檢查。

快取管理是用戶端應用程式或中繼伺服器的責任，但若已正確實作，則可藉由移除提取最近擷取之資料的需求來節省頻寬及改善效能。

Cache-Control 標頭中的 max-age 值只是指南，但不保證對應的資料在指定的時間內不會變更。 Web API 應該根據預期的資料變動性，將 max-age 設定為適當的值。 在此期間過期時，用戶端應捨棄快取中的物件。

> [!NOTE]
> 大部分的新式網頁瀏覽器都支援用戶端快取，其做法是將適當的 cache-control 標頭加入至要求，並檢查結果的標頭。 不過，有些較舊的瀏覽器不會快取從包含查詢字串的 URL 傳回的值。 對於根據此處討論的通訊協定，實作自己的快取管理策略的自訂用戶端應用程式而言，這通常不是問題。
>
> 某些較舊的 Proxy 會出現相同的行為，而且可能不會根據包含查詢字串的 URL 快取要求。 對於透過這類 Proxy 連接到 Web 伺服器的自訂用戶端應用程式而言，這可能會是個問題。

### <a name="provide-etags-to-optimize-query-processing"></a>提供 Etag 來最佳化查詢處理

當用戶端應用程式擷取物件時，回應訊息也可以包含 ETag (實體標記)。 ETag 是不透明的字串，可指出資源的版本；每次資源變更時，也會修改 Etag。 用戶端應用程式應將此 ETag 當作資料的一部分快取。 下列程式碼範例示範如何加入 ETag 作為 HTTP GET 要求之回應的一部分。 此程式碼使用物件的 `GetHashCode` 方法來產生數值，以識別此物件 (您可以視需要覆寫此方法，並使用諸如 MD5 的演算法來產生自己的雜湊)：

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

Web API 所張貼的回應訊息看起來像這樣：

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
> 基於安全性理由，不允許快取機密資料或透過已驗證 (HTTPS) 連線傳回的資料。

用戶端應用程式可以發出後續的 GET 要求以便隨時擷取相同的資源，而如果資源已變更 (有不同的 ETag)，則應捨棄快取的版本並在快取中加入新的版本。 如果資源很大且需要大量頻寬才能傳送回用戶端，提取相同資料的重複要求可能沒有效率。 為了克服這點，HTTP 通訊協定定義了下列程序，以供最佳化您應在 Web API 中支援的 GET 要求：

- 用戶端會建構一個 GET 要求，其中包含 If-None-Match HTTP 標頭中參考之資源的目前快取版本的 ETag：

    ```HTTP
    GET https://adventure-works.com/orders/2 HTTP/1.1
    If-None-Match: "2147483648"
    ```

- Web API 中的 GET 作業可取得所要求資料的目前 ETag (上述範例中的 order 2)，並將它與 If-None-Match 標頭中的值比較。

- 如果所要求資料的目前 ETag 符合要求所提供的 ETag，則資源尚未變更，且 Web API 應傳回具有空白訊息內文和狀態碼 304 (未修改) 的 HTTP 回應。

- 如果所要求資料的目前 ETag 不符合要求所提供的 ETag，則資料已經變更，且 Web API 應傳回訊息內文有新資料且狀態碼為 200 (確定) 的 HTTP 回應。

- 如果要求的資料已不存在，則 Web API 應傳回具有狀態碼 404 (找不到) 的 HTTP 回應。

- 用戶端會使用狀態碼來維護快取。 如果資料尚未變更 (狀態碼 304)，則物件可維持快取狀態，而用戶端應用程式應繼續使用此版本的物件。 如果資料已經變更 (狀態碼 200)，則應捨棄快取的物件並插入新物件。 如果資料已無法使用 (狀態碼 404)，則應從快取中移除此物件。

> [!NOTE]
> 如果回應標頭包含 Cache-Control 標頭 no-store，則應一律從快取中移除此物件 (不論 HTTP 狀態碼為何)。

以下程式碼顯示擴充為支援 If-None-Match 標頭的 `FindOrderByID` 方法。 請注意，如果省略 If-None-Match 標頭，則一律會擷取指定的訂單：

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

此範例併入名為 `EmptyResultWithCaching` 的其他自訂 `IHttpActionResult` 類別。 這個類別只是做為以 `HttpResponseMessage` 物件 (不包含回應主體) 為主的包裝函式：

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
> 在此範例中，雜湊從基礎資料來源擷取的資料，可以產生資料的 ETag。 如果以其他方式計算 ETag，則可進一步最佳化程序，而如果資料已經變更，只需從資料來源提取。 如果是大型資料或存取資料來源可能導致明顯延遲 (例如，如果資料來源是遠端資料庫)，這個方法特別有用。

### <a name="use-etags-to-support-optimistic-concurrency"></a>使用 Etag 支援開放式並行存取

為了能夠進行先前快取資料的更新，HTTP 通訊協定支援開放式並行存取策略。 提取及快取資源之後，如果用戶端應用程式接著傳送 PUT 或 DELETE 要求來變更或移除資源，則應包含在參考 ETag 的 If-match 標頭中。 Web API 可接著使用此資訊來判斷資源在擷取後是否遭到另一位使用者變更，並將適當的回應傳送回用戶端應用程式，如下所示：

- 用戶端會建構一個 PUT 要求，其中包含資源的新詳細資料及 If-Match HTTP 標頭中參考之資源的目前快取版本的 ETag： 下列範例顯示更新訂單的 PUT 要求：

    ```HTTP
    PUT https://adventure-works.com/orders/1 HTTP/1.1
    If-Match: "2282343857"
    Content-Type: application/x-www-form-urlencoded
    Content-Length: ...
    productID=3&quantity=5&orderValue=250
    ```

- Web API 中的 PUT 作業可取得所要求資料的目前 ETag (上述範例中的 order 1)，並將它與 If-Match 標頭中的值比較。

- 如果所要求資料的目前 ETag 符合要求所提供的 ETag，則資源尚未變更，且 Web API 應執行更新、傳回具有 HTTP 狀態碼 204 (沒有內容) 的訊息 (如果不成功的話)。 對於更新的資源版本，回應可以包含 Cache-Control 和 ETag 標頭。 回應應一率包含 Location 標頭，以參考最近更新的資源 URI。

- 如果所要求資料的目前 ETag 不符合要求所提供的 ETag，則資料在提取後遭到另一位使用者變更，且 Web API 應傳回具有空白訊息內文和狀態碼 412 (預先指定的條件失敗) 的 HTTP 回應。

- 如果即將更新的資源已不存在，則 Web API 應傳回具有狀態碼 404 (找不到) 的 HTTP 回應。

- 用戶端會使用狀態碼和回應標投來維護快取。 如果資料已更新 (狀態碼 204)，則物件可維持快取狀態 (只要 Cache-Control 標頭未指定 no-store)，但應更新 ETag。 如果資料由另一位使用者變更 (狀態碼 412) 或找不到 (狀態碼 404)，則應捨棄快取的物件。

下一個程式碼範例顯示 Orders 控制器的 PUT 作業實作：

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
> 使用 If-Match 標頭完全是選擇性的，而如果省略，Web API 將永遠嘗試更新指定的訂單，可能會盲目地覆寫其他使用者所做的更新。 若要避免遺失更新所衍生的問題，一定要提供 If-match 標頭。

## <a name="handling-large-requests-and-responses"></a>處理大量要求和回應

有時候用戶端應用程式可能需要發出要求，以傳送或接收大小可能數 MB (或更大) 的資料。 等候這個數量的資料進行傳輸，可能會導致用戶端應用程式沒有回應。 當您需要處理包含大量資料的要求時，請考慮下列幾點：

### <a name="optimize-requests-and-responses-that-involve-large-objects"></a>最佳化涉及大型物件的要求和回應

某些資源可能是大型物件，或包含大型欄位，例如圖形影像或其他類型的二進位資料。 Web API 應支援串流處理，這些資源才能進行最佳化的上傳和下載。

HTTP 通訊協定提供區塊傳輸編碼機制，以將大型資料物件串流處理回到用戶端。 當用戶端傳送大型物件的 HTTP GET 要求時，Web API 可透過 HTTP 連接以分次「區塊」回覆。 最初可能不知道回覆中的資料長度 (可能已產生)，所以裝載 Web API 的伺服器應傳送回應訊息，其中每個區塊指定 Transfer-Encoding:Chunked 標頭，而不是 Content-Length 標頭。 用戶端應用程式可依序接收每個區塊，以建置完整的回應。 伺服器送回大小為零的最後一個區塊時，資料傳輸便完成。

單一要求理論上可能會導致會耗用相當多資源的大量物件。 在串流處理過程中，如果 Web API 判定要求中的資料量超過一些可接受的界限，則可以中止作業並傳回具有狀態碼 413 (要求實體太大) 的回應訊息。

您可以使用 HTTP 壓縮，將透過網路傳輸的大型物件最小化。 此方法有助於減少網路流量和相關聯的網路延遲，但代價是需要在用戶端和裝載 Web API 的伺服器上進行其他處理。 例如，預計收到壓縮資料的用戶端應用程式可以包含 Accept-Encoding: gzip 要求標頭 (也可以指定其他資料壓縮演算法)。 如果伺服器支援壓縮，則應以訊息內文中以 gzip 格式保留的內容和 Content-Encoding: gzip 回應標頭回應。

您可以結合編碼壓縮與串流；在串流前先壓縮資料，並在訊息標頭中指定 gzip 內容編碼和區塊傳輸編碼。 另請注意，可將某些 Web 伺服器 (例如 Internet Information Server) 設定為自動壓縮 HTTP 回應 (不論 Web API 是否壓縮資料)。

### <a name="implement-partial-responses-for-clients-that-do-not-support-asynchronous-operations"></a>針對不支援非同步作業的用戶端實作部份回應

用戶端應用程式可以明確要求區塊中大型物件的資料 (也稱為部份回應)，作為非同步串流的替代方式。 用戶端應用程式會傳送 HTTP HEAD 要求，以取得物件的相關資訊。 如果 Web API 支援部份回應，則應以包含 Accept-Ranges 標頭和 Content-Length 標頭 (指出物件的總大小) 的回應訊息來回應 HEAD 要求 (但訊息內容應該是空的)。 用戶端應用程式可以使用此資訊來建構一系列的 GET 要求，以指定要接收的位元組範圍。 Web API 應傳回具有 HTTP 狀態 206 (部份內容)、Content-Length 標頭 (指定回應訊息內容中包含的實際資料量) 和 Content-Range 標頭 (指出此資料代表物件的哪一個部分 (例如 4000 到 8000 個位元組)) 的回應訊息。

HTTP HEAD 要求和部份回應會在 [API 設計][api-design]中詳加說明。

### <a name="avoid-sending-unnecessary-100-continue-status-messages-in-client-applications"></a>避免在用戶端應用程式中傳送不必要的 100-Continue 狀態訊息

即將將大量資料傳送到伺服器的用戶端應用程式可能會先判斷伺服器是否確實願意接受要求。 在傳送資料前，用戶端應用程式可以提交具有 Expect:100-Continue 標頭、Content-Length 標頭 (指出資料大小)，但訊息內文空白的 HTTP 要求。 如果伺服器願意處理要求，則應以指定 HTTP 狀態 100 (繼續) 的訊息回應。 用戶端應用程式可以接著繼續執行並傳送完整的要求 (包括訊息內文中的資料)。

如果您使用 IIS 裝載服務，則 HTTP.sys 驅動程式會先自動偵測並處理 Expect:100-Continue 標頭，然後再將要求傳遞至 Web 應用程式。 這表示您不太可能在您的應用程式程式碼中看見這些標頭，而且您可以假設 IIS 已經篩選它認為不合適或太大的訊息。

如果您要使用 .NET Framework 建置用戶端應用程式，則所有 POST 和 PUT 訊息都會先傳送預設具有 Expect:100-Continue 標頭的訊息。 在伺服器端，此程序是由 .NET Framework 直接處理。 不過，此程序會導致每個 POST 和 PUT 要求引起兩次伺服器來回行程 (即使是小型要求)。 如果您的應用程式並未傳送具有大量資料的要求，您可以使用 `ServicePointManager` 類別在用戶端應用程式中建立 `ServicePoint` 物件，進而停用這項功能。 `ServicePoint` 物件會根據 URI 的配置和主機片段 (用以識別伺服器上的資源)，處理用戶端對伺服器的連線。 然後您可以將 `ServicePoint` 物件的 `Expect100Continue` 屬性設定為 false。 將會傳送用戶端透過 URI (其符合 `ServicePoint` 物件的配置和主機片段) 進行的所有後續 POST 和 PUT 要求，但不需 Expect:100-Continue 標頭。 下列程式碼示範如何設定 `ServicePoint` 物件，用來設定所有傳送至具有 `http` 配置和 `www.contoso.com` 主機之 URI 的要求。

```csharp
Uri uri = new Uri("https://www.contoso.com/");
ServicePoint sp = ServicePointManager.FindServicePoint(uri);
sp.Expect100Continue = false;
```

您也可以設定靜態`Expect100Continue`的屬性`ServicePointManager`類別，以指定此屬性的預設值，針對所有後續建立[ServicePoint](/dotnet/api/system.net.servicepoint)物件。

### <a name="support-pagination-for-requests-that-may-return-large-numbers-of-objects"></a>支援對可能傳回大量物件的要求進行分頁

如果集合包含大量資源，則對相對應的 URI 發出 GET 要求可能導致在裝載 Web API 的伺服器上進行大量處理而影響效能，並產生會導致延遲增加的大量網路流量。

若要處理這些情況，Web API 應支援查詢字串，讓用戶端應用程式能在更易於管理的離散區塊 (或頁面) 中修改要求或提取資料。 下方程式碼會顯示 `Orders` 控制器中的 `GetAllOrders` 方法。 此方法會擷取訂單的詳細資料。 如果此方法不受限制，它可能會傳回大量的資料。 `limit` 和 `offset` 參數主要用來將資料量縮小為較小的子集，在此例中預設只有前 10 張訂單：

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

用戶端應用程式可以發出要求，使用 URI `https://www.adventure-works.com/api/orders?limit=30&offset=50` 來擷取從位移 50 開始的 30 張訂單。

> [!TIP]
> 避免讓用戶端應用程式指定會導致長度超過 2000 個字元的 URI 的查詢字串。 許多 Web 用戶端和伺服器都無法處理此種長度的 URI。

## <a name="maintaining-responsiveness-scalability-and-availability"></a>維護回應性、延展性和可用性

在世界各地執行的許多用戶端應用程式可能會利用相同的 Web API。 請務必確保實作 Web API 來維護沈重負載下的回應性，可以延展來支援非常不同的工作負載，以及保證執行業務關鍵作業的用戶端的可用性。 當您決定如何儲存這些需求時，請考慮下列幾點：

### <a name="provide-asynchronous-support-for-long-running-requests"></a>長時間執行的要求提供非同步支援

執行可能需花很長時間處理的要求時，不應封鎖送出此要求的用戶端。 Web API 可以執行一些初始檢查來驗證要求、起始個別工作來執行工作，然後傳回具有 HTTP 狀態碼 202 (已接受) 的回應訊息。 可隨著 Web API 處理非同步執行工作，或可以將工作卸載至背景工作。

Web API 也應提供一種機制，將處理的結果傳回給用戶端應用程式。 提供輪詢機制，以供用戶端應用程式定期查詢處理是否已完成並取得結果，或讓 Web API 在作業完成時傳送通知，即可達到此目的。

使用下列方法來提供當作虛擬資源的「輪詢」URI，即可實作簡單的輪詢機制：

1. 用戶端應用程式會將初始要求傳送至 Web API。
2. Web API 會將要求相關資訊儲存在資料表儲存體或 Microsoft Azure 快取中保存的資料表中，並為此項目產生唯一索引鍵 (可能是 GUID 格式)。
3. Web API 會將此處理當作個別的工作起始。 Web API 會在資料表中將工作狀態記錄為「執行中」。
4. Web API 會傳回具有 HTTP 狀態碼 202 (已接受) 的回應訊息，以及訊息內文中資料表項目的 GUID。
5. 當工作完成時，Web API 會將結果儲存在資料表中，並將工作狀態設定為「完成」。 請注意，如果工作失敗，Web API 也可以儲存失敗相關資訊，並將狀態設定為「失敗」。
6. 當工作正在執行時，用戶端可以繼續執行自己的處理。 它可以定期將要求傳送至 URI /polling/{guid}，其中 {guid} 是 Web API 在 202 回應訊息中傳回的 GUID。
7. 位於 /polling/{guid} URI 的 Web API 會查詢資料表中對應工作的狀態，並傳回具有 HTTP 狀態碼 200 (確定) 的回應訊息，其中包含此狀態 (「執行中」、「完成」或「失敗」)。 如果工作已完成或失敗，回應訊息也可以包含處理的結果或任何有關失敗原因的可用資訊。

用於實作通知的選項包括：

- 使用 Azure 通知中樞，將非同步回應推送至用戶端應用程式。 如需詳細資訊，請參閱 [Azure 通知中樞通知使用者](/azure/notification-hubs/notification-hubs-aspnet-backend-windows-dotnet-wns-notification/)。
- 使用 Comet 模型來保持用戶端與裝載 Web API 的伺服器之間的持續性網路連線，並使用此連線將訊息從伺服器推送回到用戶端。 MSDN 雜誌文章 [在 Microsoft .NET Framework 中建置簡單的 Comet 應用程式](https://msdn.microsoft.com/magazine/jj891053.aspx) 會舉例說明解決方案。
- 使用 SignalR 並透過持續性網路連線，即時將資料從 Web 伺服器推送到用戶端。 SignalR 可以 NuGet 封裝形式使用於 ASP.NET Web 應用程式。 您可以在 [ASP.NET SignalR](https://www.asp.net/signalr) 網站上找到更多資訊。

### <a name="ensure-that-each-request-is-stateless"></a>確保每個要求都是無狀態

每個要求都應該被視為不可部分完成。 在用戶端應用程式所提出的某一個要求與同一個用戶端所提交的任何後續要求之間，應沒有任何相依性。 此方法有助於進行延展；Web 服務的執行個體可以部署在許多伺服器上。 用戶端要求可在任何這些執行個體上進行導向，而結果應一律相同。 基於類似的理由，這也可改善可用性；如果 Web 伺服器失敗，可將要求路由傳送到另一個執行個體 (藉由使用 Azure 流量管理員)，然而伺服器會重新啟動，但不會對用戶端應用程式造成不良的影響。

### <a name="track-clients-and-implement-throttling-to-reduce-the-chances-of-dos-attacks"></a>追蹤用戶端並實作節流來降低 DOS 攻擊的機會

如果特定用戶端在指定的期間內提出大量要求，則可能會獨佔服務並會影響其他用戶端的效能。 若要緩和這個問題，Web API 可以追蹤所有連入要求的 IP 位址或藉由記錄每個已驗證的存取，以監視來自用戶端應用程式的呼叫。 您可以使用此資訊來限制資源存取。 如果用戶端超過定義的限制，Web API 可以傳回具有狀態碼 503 (無法使用服務) 的回應訊息，並包含 Retry-After 標頭，以指定用戶端何時可以傳送下一個要求而不會遭到拒絕。 此策略有助於減少來自一組癱瘓系統之用戶端的阻斷服務 (DOS) 攻擊機會。

### <a name="manage-persistent-http-connections-carefully"></a>請小心管理持續性 HTTP 連線

HTTP 通訊協定支援可用的持續性 HTTP 連線。 HTTP 1.0 規格加入了 Connection:Keep-Alive 標頭，可讓用戶端應用程式向伺服器表示它可以使用相同的連線來傳送後續的要求，而不用開啟新的連線。 如果用戶端未在主機定義的期間內重複使用連線，連線就會自動關閉。 此行為是 HTTP 1.1 中 Azure 服務所使用的預設值，因此不需要在訊息中包含 Keep-alive 標頭。

讓連接保持開啟可減少延遲和網路壅塞，有助於改善回應能力，但由於讓不必要的連接保持開啟過久，導致其他並行用戶端無法連接，可能不利於延展性。 如果用戶端應用程式是在行動裝置上執行，這也可能影響電池壽命；如果應用程式只偶而向伺服器發出要求，則讓連接保持開啟可能會導致電池更快耗盡。 若要确保不使用 HTTP 1.1 建立永久连接，客户端可以在消息中包括 Connection:Close 标头以覆盖默认行为。 同樣地，如果伺服器正在處理非常大量的用戶端，則可以在回應訊息中包含應關閉連線並儲存伺服器資源的 Connection:Close 標頭。

> [!NOTE]
> 持續性 HTTP 連線純粹是選擇性功能，可減少與重複建立通訊通道相關聯的網路負荷。 Web API 和用戶端應用程式都不應該依賴持續性 HTTP 連線才可使用。 請勿使用持續性 HTTP 連線來實作 Comet 式通知系統；而是應該利用 TCP 層的通訊端 (或可用的 Websocket)。 最後請注意，如果用戶端應用程式透過 Proxy 與伺服器進行通訊，則 Keep-Alive 標頭的使用受限；只有與用戶端和 Proxy 的連線具持續性。

## <a name="publishing-and-managing-a-web-api"></a>發行和管理 Web API

若要讓 Web API 可供用戶端應用程式使用，Web API 必須部署至主機環境。 此環境通常是 Web 伺服器，然而也可能是其他類型的主機程序。 發佈 Web API 時，您應該考慮下列幾點：

- 所有要求都必須進行驗證和授權，而且必須強制執行適當層級的存取控制。
- 商業 Web API 可能會受制於各種有關回應時間的品質保證。 如果負載可隨著時間大幅變化，請務必確保可延伸主機環境。
- 有可能需要為了獲利目的而計量要求。
- 有可能需要規範傳送至 Web API 的流量流程，並且對已用盡配額的特定用戶端實作節流。
- 法規需求可能會託管所有要求和回應的記錄與稽核。
- 為了確保可用性，有可能需要監視裝載 Web API 的伺服器健全狀況，並在必要時重新啟動。

讓這些問題能與 Web API 實作的相關技術問題脫鉤，很有助益。 基於這個理由，請考慮建立 [façade](https://en.wikipedia.org/wiki/Facade_pattern)，以作為個別的程序來執行，然後讓其將要求路由到 Web API。 façade 可以提供管理作業，並將經過驗證的要求轉送給 Web API。 使用 façade 还可以带来很多功能优势，包括：

- 作為多個 Web API 的整合點。
- 針對使用各種技術建置的用戶端，轉換訊息和轉譯通訊協定。
- 快取要求和回應，以減少裝載 Web API 之伺服器的負載。

## <a name="testing-a-web-api"></a>測試 Web API

Web API 應與軟體的任何其他部分一樣徹底進行測試。 您應該考慮建立單元測試來驗證功能。

Web API 的本質帶來自己額外的需求，以便確認運作正常。 您應該特別注意下列層面：

- 測試所有路由以確認它們叫用正確的作業。 請特別留意意外傳回的 HTTP 狀態碼 405 (不允許的方法)，因為這可能表示路由與可分派到該路由的 HTTP 方法 (GET、POST、PUT、DELETE) 不相符。

    將 HTTP 要求傳送到不提供支援的路由，例如將 POST 要求提交至特定資源 (POST 要求只能傳送至資源集合)。 在這些情況下，唯一有效的回應「應該」是狀態碼 405 (不允許)。

- 確認所有路由都得到適當保護，而且都遵守適當的驗證和授權檢查。

  > [!NOTE]
  > 某些安全性層面 (例如使用者驗證) 大都可能是主機環境 (而不是 Web API) 的責任，但仍必須將安全性測試納入部署程序中。
  >
  >

- 測試每項作業執行的例外狀況處理，並確認已將適當且有意義的 HTTP 回應傳遞回用戶端應用程式。

- 驗證要求和回應訊息的格式是否正確。 例如，如果 HTTP POST 要求包含新資源的資料 (採用 x-www-form-urlencoded 格式)，請確認對應的作業正確剖析資料、建立資源，並傳回包含新資源詳細資料的回應 (包括正確的 Location 標頭)。

- 驗證回應訊息中的所有連結和 URI。 例如，HTTP POST 訊息應該會傳回新建資源的 URI。 所有 HATEOAS 链接都应有效。

- 确保每个操作针对不同输入组合返回正确的状态代码。 例如︰

  - 如果查詢成功，則應傳回狀態碼 200 (確定)
  - 如果找不到資源，作業應傳回 HTTP 狀態碼 404 (找不到)。
  - 如果用戶端傳送可成功刪除資源的要求，狀態碼應該是 204 (沒有內容)。
  - 如果用戶端傳送可建立新資源的要求，狀態碼應該是 201 (已建立)。

請提防 5xx 範圍中的非預期回應狀態碼。 這些訊息通常是主機伺服器的回報，指出表示它無法履行某個有效的要求。

- 測試用戶端應用程式可以指定的不同要求標頭組合，並確保 Web API 在回應訊息中傳回預期的資訊。

- 測試查詢字串。 如果作業可採用選擇性參數 (例如分頁要求)，請測試不同組合和順序的參數。

- 確認非同步作業順利完成。 如果 Web API 支援傳回大型二進位物件 (例如視訊或音訊) 之要求的串流處理，請確保在串流處理資料時不會封鎖用戶端要求。 如果 web API 實作輪詢長時間執行資料修改作業，請確認作業報告其狀態正常進行。

您也應該建立和執行效能測試，檢查 Web API 在受限的情況下是否運作順利。 您可以使用 Visual Studio Ultimate，建置 Web 效能和負載測試專案。 如需詳細資訊，請參閱[在應用程式發行前執行效能測試](https://msdn.microsoft.com/library/dn250793.aspx)。

## <a name="using-azure-api-management"></a>使用 Azure API 管理

在 Azure 上，請考慮使用 [Azue API 管理](/azure/api-management//services/api-management/)來發行和管理 Web API。 藉由使用此功能，您可以針對一個或多個 Web API 產生作為 façade 的服務。 此服務本身是可使用 Azure 管理入口網站建立及設定的可延伸 Web 服務。 您可以使用這項服務來發佈和管理 Web API，如下所示：

1. 將 Web API 部署到網站、Azure 雲端服務或 Azure 虛擬機器。

2. 將 API 管理服務連接到 Web API。 傳送至管理 API 之 URL 的要求會對應至 Web API 中的 URI。 相同的 API 管理服務可將要求路由傳送到多個 Web API。 這可讓您將多個 Web API 彙總成單一管理服務。 同樣地，如果您需要限制或分割不同應用程式可用的功能，可以從一個以上的 API 管理服務參考相同的 Web API。

     > [!NOTE]
     > HATEOAS 連結中產生成為 HTTP GET 要求的回應一部分的 URI，應參考 API 管理服務 (而非裝載 Web API 的 Web 伺服器) 的 URL。

3. 對每個 Web API，指定 Web API 公開的 HTTP 作業，以及作業可作為輸入使用的任何選擇性參數。 您也可以設定 API 管理服務是否應快取從 Web API 收到的回應，以最佳化相同資料的重複要求。 記錄每項作業可產生之 HTTP 回應的詳細資料。 此資訊用來產生開發人員適用的文件，因此一定要精確且完整。

    您可以使用 Azure 管理入口網站提供的精靈手動定義作業，也可以從含有 WADL 或 Swagger 格式之定義的檔案匯入它們。

4. 設定 API 管理服務與裝載 Web API 的 Web 伺服器之間通訊的安全性設定。 API 管理服務目前支援使用憑證的基本驗證和相互驗證，以及 OAuth 2.0 使用者授權。

5. 建立產品。 產品是發佈的單位；您會將先前連接的 Web API 加入至產品的管理服務。 發佈產品時，Web API 即可供開發人員使用。

    > [!NOTE]
    > 在發佈產品之前，您也可以定義可存取產品的使用者群組，並將使用者加入至這些群組。 這可讓您控制可使用 Web API 的開發人員和應用程式。 如果 Web API 需獲得核准，開發人員必須先將要求傳送給產品系統管理員，才能夠加以存取。 系統管理員可以授與或拒絕程式開發人員的存取。 如果情況變更，也可能會封鎖現有的開發人員。

6. 設定每個 Web API 的原則。 原則可管理許多層面，例如是否應該允許跨網域呼叫、如何驗證用戶端、是否以透明方式在 XML 與 JSON 資料格式間轉換、是否限制來自指定 IP 範圍的呼叫、使用量配額，以及是否限制呼叫率。 原則可以全面套用於整個產品、針對產品中的單一 Web API 套用，或針對 Web API 中的個別作業套用。

如需詳細資訊，請參閱 [API 管理文件](/azure/api-management/)。

> [!TIP]
> Azure 提供了 Azure 流量管理員，可讓您實作容錯移轉和負載平衡，並且讓網站上裝載於不同地理位置的多個執行個體減少延遲。 您可以使用 Azure 流量管理員搭配 API 管理服務；API 管理服務可以透過 Azure 流量管理員，將要求路由傳送至網站的執行個體。 如需詳細資訊，請參閱[流量管理員路由方法](/azure/traffic-manager/traffic-manager-routing-methods/)。
>
> 在此結構中，如果您使用您網站的自訂 DNS 名稱，則應該為每個網站設定適當的 CNAME 記錄，以指向 Azure 流量管理員網站的 DNS 名稱。

## <a name="supporting-client-side-developers"></a>支援用戶端開發人員

建構用戶端應用程式的開發人員通常需要有關如何存取 Web API 的資訊，以及有關參數、資料類型、傳回型別和傳回碼 (描述 Web 服務與用戶端應用程式間的不同要求和回應) 的文件。

### <a name="document-the-rest-operations-for-a-web-api"></a>記載 Web API 的 REST 作業

Azure API 管理服務包括可描述 Web API 所公開之 REST 作業的開發人員入口網站。 產品發佈後就會顯示在此入口網站上。 開發人員可以使用此入口網站來註冊存取權；系統管理員即可核准或拒絕此要求。 如果開發人員獲得核准，就會指派訂用帳戶金鑰給他們，以便用來驗證來自他們所開發的用戶端應用程式呼叫。 此金鑰必須隨著每個 Web API 呼叫提供，否則會遭到拒絕。

此入口網站也提供：

- 產品文件，其中列出所公開的作業、必要的參數，以及可以傳回的各種回應。 請注意，此資訊是根據「使用 Microsoft Azure API 管理服務發佈 Web API」一節的清單中步驟 3 所提供的詳細資料產生。
- 程式碼片段，示範如何以數種語言 (包括 JavaScript、C#、Java、Ruby、Python 和 PHP) 叫用作業。
- 開發人員的主控台，讓開發人員傳送 HTTP 要求，以在產品中測試每項作業並檢視結果。
- 一個頁面，以供開發人員回報任何找到的問題。

Azure 管理入口網站可讓您自訂開發人員入口網站來變更樣式和版面配置，以符合貴組織的商標。

### <a name="implement-a-client-sdk"></a>實作用戶端 SDK

建置可叫用 REST 要求以存取 Web API 的用戶端應用程式時，需要撰寫大量的程式碼來建構每個要求並進行適當格式化、將要求傳送至裝載 Web 服務的伺服器，以及剖析回應來判斷要求成功或失敗並擷取傳回的任何資料。 若要使用戶端應用程式免除這些顧慮，您可以提供 SDK，以供包裝 REST 介面以及在一組功能更強的方法中摘錄這些低階詳細資訊。 用戶端應用程式會使用這些方法，以透明方式將呼叫轉換成 REST 要求，然後將回應轉換回方法傳回值。 這是由許多服務實作的通用技術，包括 Azure SDK。

建立用戶端 SDK 是相當重要的工作，因為這項作業必須以一致方式實作並小心進行測試。 不過，此程序大都可以機械方式進行，而且許多廠商都提供可自動執行許多工作的工具。

## <a name="monitoring-a-web-api"></a>監控 Web API

視您發佈和部署 Web API 的方式而定，您可以直接監控 Web API，也可以藉由分析通過 API 管理服務的流量來收集使用量和健全狀況資訊。

### <a name="monitoring-a-web-api-directly"></a>直接監控 Web API

如果您已使用 ASP.NET Web API 範本 (以 Azure 雲端服務中的 Web API 專案或 Web 角色) 和 Visual Studio 2013 實作您的 Web API，您可以使用 ASP.NET Application Insights 來收集可用性、效能和使用量資料。 Application Insights 是在 Web API 部署到雲端時，會以透明方式追蹤及記錄要求和回應相關資訊的封裝；安裝並設定此封裝後，您不需要在 Web API 中修改任何程式碼即可加以使用。 當您將 Web API 部署至 Azure 網站時，系統會檢查所有流量並收集下列統計資料：

- 伺服器回應時間。
- 伺服器要求的數目及每個要求的詳細資料。
- 從平均回應時間角度來看的最慢幾項要求。
- 任何失敗要求的詳細資料。
- 由不同的瀏覽器和使用者代理程式起始的工作階段數目。
- 最常檢視的頁面 (主要適用於 Web 應用程式，而非 Web API)。
- 存取 Web API 的不同使用者角色。

您可以從 Azure 管理入口網站即時檢視此資料。 您也可以建立用以監控 Web API 健全狀況的 Web 測試。 Web 測試會傳送定期要求至 Web API 中指定的 URI，並擷取回應。 您可以指定成功回應 (例如 HTTP 狀態碼 200) 的定義，而如果要求未傳回此回應，您可以安排要傳送給系統管理員的警示。 必要時，系統管理員可以重新啟動裝載 Web API 的伺服器 (如果失敗的話)。

如需詳細資訊，請參閱 [Application Insights - ASP.NET 入門](/azure/application-insights/app-insights-asp-net/)。

### <a name="monitoring-a-web-api-through-the-api-management-service"></a>透過 API 管理服務監控 Web API

如果您已使用 API 管理服務發佈您的 Web API，則 Azure 管理入口網站上的 API 管理頁面會包含可讓您檢視服務整體效能的儀表板。 [分析] 頁面可讓您向下鑽研產品使用方式的詳細資料。 本頁面包含下列索引標籤：

- **使用量**。 此索引標籤提供已進行的 API 呼叫數目以及用來處理這些呼叫的頻寬相關資訊。 您可以依照產品、API 和作業篩選使用量詳細資料。
- **健全狀況**。 此索引標籤可讓您檢視 API 要求的結果 (傳回的 HTTP 狀態碼)、快取原則成效、API 回應時間和服務回應時間。 同樣地，您可以依照產品、API 和作業篩選健全狀況資料。
- **活動**。 此索引標籤提供成功呼叫、失敗呼叫、封鎖呼叫數目、平均回應時間以及各產品、Web API 和作業之回應時間的文字摘要。 本頁面也會列出每位開發人員所進行的呼叫數目。
- **速覽**。 此索引標籤會顯示效能資料的摘要，包括負責進行大部分 API 呼叫的開發人員，以及收到這些呼叫的產品、Web API 和作業。

您可以使用這項資訊來判斷是否特定 Web API 或作業造成瓶頸，以及視需要調整主機環境並加入更多伺服器。 您也可以確定一或多個應用程式是否使用不相稱的資源量，並套用適當的原則來設定配額及限制呼叫率。

> [!NOTE]
> 您可以變更已發佈產品的詳細資料，而您所做的變更會立即套用。 例如，您可以在 Web API 中新增或移除作業，而不需要重新發佈含有 Web API 的產品。

## <a name="more-information"></a>詳細資訊

- [ASP.NET Web API OData](https://www.asp.net/web-api/overview/odata-support-in-aspnet-web-api) 包含有關使用 ASP.NET 實作 OData Web API 的範例以及進一步資訊。
- [Web API 和 Web API OData 中的批次支援簡介](https://blogs.msdn.microsoft.com/webdev/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata/)說明如何使用 OData 在 Web API 中實作批次作業。
- Jonathan Oliver 部落格上的[冪等模式](https://blog.jonathanoliver.com/idempotency-patterns/)提供冪等概觀，以及其與資料管理作業有何相關。
- W3C 網站上的[狀態碼定義](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)包含 HTTP 狀態碼的完整清單及其說明。
- [使用 WebJob 執行背景工作](/azure/app-service-web/web-sites-create-web-jobs/)提供有關使用 WebJob 執行背景作業的資訊和範例。
- [Azure 通知中樞通知使用者](/azure/notification-hubs/notification-hubs-aspnet-backend-windows-dotnet-wns-notification/)顯示如何使用 Azure 通知中樞將非同步回應推送至用戶端應用程式。
- [API 管理](https://azure.microsoft.com/services/api-management/)說明如何發佈產品，以提供受控制且安全的 Web API 存取。
- [Azure APIM REST API 參考](https://msdn.microsoft.com/library/azure/dn776326.aspx)說明如何使用 API 管理 REST API 來建置自訂管理應用程式。
- [流量管理員路由方法](/azure/traffic-manager/traffic-manager-routing-methods/)摘要說明 Azure 流量管理員如何用來平衡裝載 Web API 的網站上多個執行個體的要求負載。
- [Application Insights - 開始使用 ASP.NET](/azure/application-insights/app-insights-asp-net/)提供有關在 ASP.NET Web API 專案中安裝和設定 Application Insights 的詳細資訊。

<!-- links -->

[api-design]: ./api-design.md

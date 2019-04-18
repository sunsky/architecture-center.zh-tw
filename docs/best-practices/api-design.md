---
title: API 設計指引
titleSuffix: Best practices for cloud applications
description: 說明如何建立完善 Web API 的指引。
author: dragon119
ms.date: 01/12/2018
ms.topic: best-practice
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 06090b0862a7c737d9ee93512f851d3fcf2e2d9f
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640867"
---
# <a name="api-design"></a>API 設計

大多數現代的 Web 應用程式，都會公開用戶端可用來與應用程式互動的 API。 設計良好的 Web API 應以支援下列特性為目標：

- **平台獨立性**。 不論 API 是如何在內部實作，任何用戶端都應該能夠呼叫 API。 這需要使用標準通訊協定，並擁有讓用戶端和 Web 服務可以同意交換資料格式的機制。

- **服務演化**。 Web API 應該要能獨立於用戶端應用程式之外而演化及新增功能。 隨著 API 的發展，現有的用戶端應用程式即使不進行修改，也應該能夠繼續運作。 所有功能應該要可供探索，讓用戶端應用程式得以充分運用。

本指引描述了設計 Web API 時應考量的問題。

## <a name="introduction-to-rest"></a>REST 簡介

在 2000 年時，Roy Fielding 提出了以具象狀態傳輸 (REST) 的架構來設計 Web 服務。 REST 是建置以超媒體為基礎之分散式系統的架構樣式。 REST 獨立於任何基礎通訊協定之外，因此不一定要與 HTTP 連結。 不過，最常見的 REST 實作會使用 HTTP 作為應用程式通訊協定，且本指南著重於針對 HTTP 來設計 REST API。

REST 比起 HTTP 的主要優點是前者使用開放標準，因此不會讓 API 或用戶端應用程式的實作受到任何特定實作的約束。 例如，開發人員能以 ASP.NET 來撰寫 REST Web 服務，且用戶端應用程式可使用任何語言或工具組，只要能產生 HTTP 要求和剖析 HTTP 回應即可。

以下是一些使用 HTTP 設計 REST 式 API 的主要原則：

- REST API 是依照「資源」來設計的，而資源是指可由用戶端存取、任何類型的物件、資料或服務。

- 資源具有「識別碼」，這是可唯一識別該資源的 URI。 例如，特定客戶訂單的 URI 可能會是：

    ```HTTP
    https://adventure-works.com/orders/1
    ```

- 用戶端會透過交換資源的「表示法」與服務進行互動。 許多 Web API 會使用 JSON 作為交換格式。 例如，對上面所列 URI 的 GET 要求，可能會傳回此回應本文：

    ```json
    {"orderId":1,"orderValue":99.90,"productId":1,"quantity":1}
    ```

- REST API 會使用統一的介面，有助於讓用戶端與服務實作分離。 對於建置在 HTTP 上的 REST API，統一的介面包括使用標準 HTTP 指令動詞在資源上執行作業。 最常見的作業是 GET、POST、PUT、PATCH 和 DELETE。

- REST API 會使用無狀態要求模式。 HTTP 要求應該是獨立的，而且可能會以任何順序發生，因此保留多個要求之間的暫時性狀態資訊不是恰當的做法。 唯一可儲存資訊的場所是資源本身，而且每個要求都應該是不可部分完成的作業。 這個限制式可讓 Web 服務具有高度可調整性，因為在用戶端與特定伺服器之間不需要保留任何的親和性。 任何伺服器都可以處理來自任何用戶端的任何要求。 話雖如此，其他因素可能會限制延展性。 例如，許多 Web 服務都會寫入可能難以相應放大的後端資料存放區。([資料分割](./data-partitioning.md)一文描述了相應放大資料存放區的策略。)

- REST API 是由表示法中包含的超媒體所推動的。 例如，以下顯示訂單的 JSON 表示法。 其中所包含的連結，可取得或更新與訂單相關的客戶。

    ```json
    {
        "orderID":3,
        "productID":2,
        "quantity":4,
        "orderValue":16.60,
        "links": [
            {"rel":"product","href":"https://adventure-works.com/customers/3", "action":"GET" },
            {"rel":"product","href":"https://adventure-works.com/customers/3", "action":"PUT" }
        ]
    }
    ```

在 2008 年時，Leonard Richardson 針對 Web API 提出了下列[成熟度模型](https://martinfowler.com/articles/richardsonMaturityModel.html)：

- 等級 0：定義一個 URI，而所有的作業對此 URI 都是 POST 要求。
- 等級 1：針對個別資源建立不同的 URI。
- 等級 2：使用 HTTP 方法來定義資源上的作業。
- 等級 3：使用超媒體 (HATEOAS，如下所述)。

根據 Fielding 的定義，層級 3 代表真正的 REST 式 API。 在實務上，許多已發行的 Web API 是落在等級 2 附近。

## <a name="organize-the-api-around-resources"></a>依照資源來組織 API

將焦點放在 Web API 公開商業實體。 例如，在電子商務系統中，主要實體可能是客戶和訂單。 可藉由傳送包含訂單資訊的 HTTP POST 要求來建立訂單。 HTTP 回應會指出訂購成功與否。 如果可能的話，資源 URI 應該要依據名詞 (資源) 而不是動詞 (在資源上的作業)。

```HTTP
https://adventure-works.com/orders // Good

https://adventure-works.com/create-order // Avoid
```

資源不一定要依據單一實體資料項目。 例如，訂單資源可能會以關聯式資料庫中數個資料表的形式在內部實作，但以單一實體的形式呈現給用戶端。 請避免建立只是反映資料庫內部結構的 API。 REST 的目的在於將實體，以及應用程式能在這些實體上所執行的作業加以模型化。 用戶端不應向內部實作公開。

實體通常會分組成集合 (訂單、客戶)。 集合與集合內的項目是不同的資源，而應具有自己的 URI。 例如，下列 URI 可能代表訂單的集合：

```HTTP
https://adventure-works.com/orders
```

傳送對集合 URI 的 HTTP GET 要求，可擷取集合中的項目清單。 集合中的每個項目也會有自己的唯一 URI。 對項目 URI 的 HTTP GET 要求，會傳回該項目的詳細資料。

在 URI 中請採用一致的命名慣例。 一般而言，在參考集合的 URI 中使用複數名詞比較有效益。 將集合的 URI 和項目組織成階層是很好的做法。 例如，`/customers` 是客戶集合的路徑，而 `/customers/5` 則是其 ID 等於 5 的客戶路徑。 這個方法有助於維持 Web API 的直覺性。 此外，許多 Web API 架構可根據參數化的 URI 路徑來路由傳送要求，因此您可以定義路徑 `/customers/{id}` 的路由。

也請將不同類型之資源間的關聯性，以及公開這些關聯的方式納入考量。 例如，`/customers/5/orders` 可能代表客戶 5 的所有訂單。 您也可以嘗試從另一個方向切入；從訂單倒推具有如 `/orders/99/customer` URI 的客戶，來表示其關聯性。 不過，過度擴充此模型可能會導致難以實作。 比較好的解決方案，是在 HTTP 回應訊息本文中提供相關資源的可瀏覽連結。 這項機制描述一節中詳細[來啟用相關資源導覽使用 HATEOAS](#use-hateoas-to-enable-navigation-to-related-resources)。

在更複雜的系統中，能提供可讓用戶端導覽數個層級關聯性 (例如 `/customers/1/orders/99/products`) 的 URI 會很吸引人。 不過，如果資源之間的關聯性在未來發生變更，這種程度的複雜度不僅難以維持，也缺乏彈性。 所以，請試著讓 URI 保持相對單純。 一旦應用程式擁有資源的參考，就應該可以使用這個參考來尋找與該資源相關的項目。 您可用 URI `/customers/1/orders` 來取代上述查詢，找出客戶 1 的所有訂單，然後用 `/orders/99/products` 來尋找此順序中的產品。

> [!TIP]
> 請避免要求比 collection/item/collection 更複雜的資源 URI。

另一個因素是所有的 Web 要求，都會造成網頁伺服器的負載。 要求愈多，負載也愈大。 因此，請試著避免會公開大量小型資源的「多話」Web API。 這類 API 可能會要求用戶端應用程式傳送多個要求來尋找需要的所有資料。 反之，建議您將資料反正規化並將相關資訊結合在一起，變成發出單一要求即可予以擷取的大型資源。 不過，您需要設法平衡使用此方法在擷取資料時所造成的額外負荷 (這是用戶端不需要的)。 擷取大型物件可能會讓要求的延遲時間變長，並引發額外的頻寬成本。 如需有關這些效能反模式的詳細資訊，請參閱[多話的 I/O](../antipatterns/chatty-io/index.md) 和[沒有直接關聯的擷取](../antipatterns/extraneous-fetching/index.md)。

請避免讓 Web API 與基礎資料來源產生相依性。 例如，若資料儲存在關聯式資料庫中，Web API 就不需要將每個資料表公開為資源集合。 事實上，這可能是不良的設計。 相反地，請將 Web API 想像成資料庫的抽象概念。 如有必要，請在資料庫與 Web API 之間引入對應層。 這樣一來，用戶端應用程式就會與基礎資料庫結構描述的變更隔離。

最後，您不一定能將 Web API 實作的每個作業對應到特定資源。 您可以透過能叫用某項功能，並以 HTTP 回應訊息形式傳回結果的 HTTP 要求來處理這類「非資源」案例。 例如，實作簡單計算機作業 (如加法和減法) 的 Web API 可以提供將這些作業公開為虛擬資源，並利用查詢字串來指定所需參數的 URI。 例如對 URI /add?operand1=99&operand2=1 的 GET 要求，會傳回本文中包含值 100 的回應訊息。 儘管如此，請謹慎使用這些形式的 URI。

## <a name="define-operations-in-terms-of-http-methods"></a>以 HTTP 方法定義作業

HTTP 通訊協定能定義數個將語意意義指派給要求的方法。 大多數符合 REST 限制之 Web API 使用的常見 HTTP 方法包括︰

- **GET**：在指定的 URI 擷取資源的表示法。 回應訊息的本文包含要求之資源的詳細資料。
- **POST**：在指定的 URI 建立新資源。 要求訊息的本文提供新資源的詳細資料。 請注意，POST 也能用來觸發實際上不會建立資源的作業。
- **PUT**：在指定的 URI 建立或取代資源。 要求訊息的本文會指定要建立或更新的資源。
- **PATCH**：會執行資源的部分更新。 要求本文會指定要套用到資源的變更集。
- **DELETE**：會在指定的 URI 移除資源。

特定要求的效果應取決於資源是集合還是個別項目。 下表摘要說明的常見慣例是大部分使用電子商務範例之符合 REST 限制實作所採用的慣例。 請注意，這些要求中並非所有要求都可以實作，須視特定案例的情況而定。

| **Resource** | **POST** | **GET** | **PUT** | **DELETE** |
| --- | --- | --- | --- | --- |
| /customers |建立新客戶 |擷取所有客戶 |大量更新客戶 |移除所有客戶 |
| /customers/1 |Error |擷取客戶 1 的詳細資料 |更新客戶 1 的詳細資料 (若有的話) |移除客戶 1 |
| /customers/1/orders |為客戶 1 建立新訂單 |擷取客戶 1 的所有訂單 |大量更新客戶 1 的訂單 |移除客戶 1 的所有訂單 |

POST、PUT 和 PATCH 之間的差異可能會令人混淆。

- POST 要求會建立資源。 伺服器會指派新資源的 URI，並將該 URI 傳回給用戶端。 在 REST 模型中，您會經常將 POST 要求套用到集合。 新資源會新增到集合中。 POST 要求也可用來提交資料以處理現有的資源，而不建立任何新資源。

- PUT 要求會建立資源「或」更新現有的資源。 用戶端會指定資源的 URI。 要求本文會包含資源的完整表示法。 若具有此 URI 的資源已經存在，則會取代此資源。 否則會建立新的資源 (若伺服器支援此動作)。 PUT 要求最常套用到是個別項目的資源 (例如特定的客戶)，而非集合的資源。 伺服器可能會支援透過 PUT 更新資源，但不支援透過 PUT 建立資源。 是否支援透過 PUT 建立資源，取決於資源存在之前，用戶端是否可以有意義地將 URI 指派給資源。 若否，請使用 POST 來建立資源並使用 PUT 或 PATCH 來更新資源。

- PATCH 要求會針對現有的資源執行「部分更新」。 用戶端會指定資源的 URI。 要求本文會指定要套用到資源的「變更」集。 這可能比使用 PUT 更有效率，因為用戶端只會傳送變更，而不會傳送整個資源的表示法。 技術上 PATCH 也可以建立新的資源 (透過指定一組「null」資源的更新，若伺服器支援此動作)。

PUT 要求必須具有等冪性。 若用戶端多次送出相同的 PUT 要求，結果應該永遠保持不變 (使用相同的值會修改相同的資源)。 不保證 POST 和 PATCH 要求都具有冪等性。

## <a name="conform-to-http-semantics"></a>符合 HTTP 語意

本節會描述一些要設計符合 HTTP 規格的 API 時，一般的考量事項。 不過，恕無法涵蓋每種可能的細節或案例。 若有疑問，請參閱 HTTP 規格。

### <a name="media-types"></a>媒體類型

如先前所述，用戶端和伺服器會交換資源的表示法。 例如，在 POST 要求中，要求本文會包含所要建立資源的表示法。 在 GET 要求中，回應本文會包含已擷取資源的表示法。

在 HTTP 通訊協定中，會使用「媒體類型」(也稱為 MIME 類型) 來指定格式。 對於非二進位資料，大部分 Web API 都支援 JSON (媒體類型 = application/json)，並可能支援 XML (媒體類型 = application/xml)。

要求或回應中的 Content-Type 標頭會指定表示法的格式。 以下是包括 JSON 資料的 POST 要求範例：

```HTTP
POST https://adventure-works.com/orders HTTP/1.1
Content-Type: application/json; charset=utf-8
Content-Length: 57

{"Id":1,"Name":"Gizmo","Category":"Widgets","Price":1.99}
```

若伺服器不支援該媒體類型，應會傳回 HTTP 狀態碼 415 (不支援的媒體類型)。

用戶端要求可能會包含 Accept 標頭，而在回應訊息中，該標頭包含用戶端會從伺服器接受的媒體類型清單。 例如︰

```HTTP
GET https://adventure-works.com/orders/2 HTTP/1.1
Accept: application/json
```

若伺服器不符合任何所列的媒體類型，應會傳回 HTTP 狀態碼 406 (無法接受)。

### <a name="get-methods"></a>GET 方法

成功的 GET 方法通常會傳回 HTTP 狀態碼 200 (確定)。 若找不到該資源，此方法應會傳回 404 (找不到)。

### <a name="post-methods"></a>POST 方法

若 POST 方法建立了新資源，就會傳回 HTTP 狀態碼 201 (已建立)。 新資源的 URI 會包含在回應的 Location 標頭中。 回應本文會包含資源的表示法。

若該方法進行了一些處理，但未建立新資源，則方法可能會傳回 HTTP 狀態碼 200，並在回應本文中包含作業的結果。 或者，若沒有可傳回的結果，該方法可能會傳回 HTTP 狀態碼 204 (沒有內容) 而不傳回回應本文。

若用戶端在要求中放入無效的資料，伺服器應會傳回 HTTP 狀態碼 400 (錯誤的要求)。 回應本文可能會包含關於錯誤的其他資訊，或可提供更多詳細資料的 URI 連結。

### <a name="put-methods"></a>PUT 方法

若 PUT 方法建立了新資源，就會傳回 HTTP 狀態碼 201 (已建立)，如同 POST 方法一樣。 若該方法更新了現有資源，就會傳回 200 (確定) 或 204 (沒有內容)。 在某些情況下，可能會無法更新現有的資源。 在此情況下，請考慮傳回 HTTP 狀態碼 409 (衝突)。

請考慮實作可批次更新集合中多個資源的大量 HTTP PUT 作業。 PUT 要求應指定集合的 URI，而要求本文應指定要修改之資源的詳細資料。 這個方法有助於減少多對話的情況及提升效能。

### <a name="patch-methods"></a>PATCH 方法

用戶端會使用 PATCH 要求，以「修補程式文件」的格式將一組更新傳送給現有資源。 伺服器會處理修補程式文件以執行更新。 修補程式文件並不會描述整個資源，只會描述要套用的一組變更。 PATCH 方法的規格 ([RFC 5789](https://tools.ietf.org/html/rfc5789)) 不會針對修補程式文件定義特定格式。 格式必須從要求中的媒體類型來推斷。

JSON 可能是 Web API 最常見的資料格式。 有兩種以 JSON 為基礎的主要修補程式格式，稱為「JSON 修補」和「JSON 合併修補」。

JSON 合併修補稍微簡單些。 修補程式文件與原始的 JSON 資源具有相同的結構，但只包含了應變更或新增的欄位子集。 此外，您可在修補程式文件中將欄位值指定為 `null` 來刪除欄位。 (這表示若原始的資源可以有明確的 null 值，合併修補程式就不適合。)

例如，假設原始的資源具有下列 JSON 表示法：

```json
{
    "name":"gizmo",
    "category":"widgets",
    "color":"blue",
    "price":10
}
```

以下是此資源可能的 JSON 合併修補程式：

```json
{
    "price":12,
    "color":null,
    "size":"small"
}
```

這會要求伺服器更新 `price`、刪除 `color`，以及新增 `size` &mdash; 不會修改 `name` 和 `category`。 如需 JSON 合併修補程式的確切詳情，請參閱 [RFC 7396](https://tools.ietf.org/html/rfc7396)。 JSON 合併修補程式的媒體類型為 `application/merge-patch+json`。

若原始的資源可以包含明確的 null 值，合併修補程式就不適合，因為 `null` 在修補程式文件中有特殊意義。 此外，修補程式文件也不會指定伺服器應套用更新的順序。 這可能很重要，也可能不重要，端視資料和網域而定。 以 [RFC 6902](https://tools.ietf.org/html/rfc6902) 定義的 JSON 修補程式更加有彈性。 它會將變更指定為要套用的作業序列。 這些作業包括新增、移除、取代、複製及測試 (用來驗證值)。 JSON 修補程式的媒體類型為 `application/json-patch+json`。

以下是一些處理 PATCH 要求時可能會碰到的一般錯誤狀況，以及適用的 HTTP 狀態碼。

| 錯誤狀況 | HTTP 状态代码 |
|-----------|------------|
| 不支援該修補程式文件格式。 | 415 (不支援的媒體類型) |
| 修補程式文件格式不正確。 | 400 (錯誤的要求) |
| 修補程式文件有效，但在資源目前的狀態下，無法將變更套用到該資源。 | 409 (衝突)

### <a name="delete-methods"></a>DELETE 方法

若刪除作業成功，網頁伺服器應會回應 HTTP 狀態碼 204，表示已成功處理程序，但回應本文不會包含進一步的資訊。 若該資源不存在，網頁伺服器可能會傳回 HTTP 404 (找不到)。

### <a name="asynchronous-operations"></a>非同步作業

有時 POST、PUT、PATCH 或 DELETE 作業可能會需要一些時間才能完成處理。 若您將回應傳送給用戶端之前要等候作業完成，可能會導致無法接受的延遲。 在此情況下，請考慮讓作業非同步。 傳回 HTTP 狀態碼 202 (已接受)，來表示已接受要求，但尚未完成處理。

您應該公開會傳回非同步要求狀態的端點，讓用戶端可以透過輪詢狀態端點來監視狀態。 請在 202 回應的 Location 標頭中包含狀態端點的 URI。 例如︰

```HTTP
HTTP/1.1 202 Accepted
Location: /api/status/12345
```

若用戶端將 GET 要求傳送到此端點，回應中應包含要求的目前狀態。 或者，您也可以在回應中包含預估的完成時間，或用以取消作業的連結。

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "status":"In progress",
    "link": { "rel":"cancel", "method":"delete", "href":"/api/status/12345" }
}
```

若非同步作業建立了新資源，在作業完成後狀態端點應會傳回狀態碼 303 (請參閱「其他」)。 請在 303 回應中，包含可提供新資源 URI 的 Location 標頭：

```HTTP
HTTP/1.1 303 See Other
Location: /api/orders/12345
```

如需詳細資訊，請參閱 [REST 中的非同步作業](https://www.adayinthelifeof.nl/2011/06/02/asynchronous-operations-in-rest/)。

## <a name="filter-and-paginate-data"></a>將資料篩選和分頁

透過單一 URI 公開資源集合，可能會導致應用程式在只需要資訊的子集時反而擷取大量資料。 例如，假設用戶端應用程式需要找出具有特定成本值的所有訂單。 它可能會從 /orders URI 擷取所有的訂單，然後在用戶端上篩選這些訂單。 此程序顯然效率極低， 會浪費裝載 Web API 之伺服器的網路頻寬和處理能力。

反之，API 可在 URI 的查詢字串中傳遞篩選條件 (例如 /orders?minCost=n)。 接著 Web API 會負責剖析和處理查詢字串中的 `minCost` 參數，並在伺服器端傳回篩選的結果。

針對集合資源的 GET 要求有可能會傳回大量的項目。 您應該設計 Web API 以限制任何單一要求所傳回的資料量。 請考慮提供可指定要擷取的項目上限的查詢字串，以及提供集合中的起始位移。 例如︰

```HTTP
/orders?limit=25&offset=50
```

也請考慮設定要傳回的項目上限，以協助避免拒絕服務的攻擊。 為了協助用戶端應用程式，傳回已分頁資料的 GET 要求也應該包含某種形式的中繼資料，以便指出集合中的可用資源總數。

您可以提供將欄位名稱當作值的排序參數 (例如 /orders?sort=ProductID)，來使用類似的策略在擷取資料時予以排序。 不過，這種方法可能會對快取造成不良影響 (因為查詢字串參數會構成部分資源識別碼，而許多快取實作會將該識別碼當做索引鍵來快取資料)。

若每個項目都包含大量的資料，您可以延伸這個方法來限制傳回的欄位。 例如，您可以使用接受以逗號分隔清單之欄位的查詢字串參數 (如 /orders?fields=ProductID,Quantity)。

為查詢字串中所有選擇性參數提供有意義的預設值。 例如，如果您實作分頁，可以將 `limit` 參數設為 10，並將 `offset` 參數設為 0；如果您實作排序，可以將排序參數設定為資源的索引鍵；如果您支援投射，可以將 `fields` 參數設定為資源中的所有欄位。

## <a name="support-partial-responses-for-large-binary-resources"></a>支援對大型二進位資源的部分回應

資源可能會包含大型的二進位欄位，例如檔案或影像。 若要克服不可靠和間歇性連線所造成的問題，以及改善回應時間，請考慮以區塊形式擷取這類資源。 若要這樣做，Web API 應支援大型資源 GET 要求的 Accept-Ranges 標頭。 此標頭表示 GET 作業支援部分的要求。 用戶端應用程式能以位元組的範圍加以指定，送出會傳回資源子集的 GET 要求。

此外，也請考慮實作這些資源的 HTTP HEAD 要求。 HEAD 要求與 GET 要求相似，不過前者只會傳回描述資源的 HTTP 標頭和空白的訊息本文。 用戶端應用程式可以發出 HEAD 要求，以判斷是否要使用部分 GET 要求擷取資源。 例如︰

```HTTP
HEAD https://adventure-works.com/products/10?fields=productImage HTTP/1.1
```

以下是回應訊息的範例：

```HTTP
HTTP/1.1 200 OK

Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 4580
```

Content-Length 標頭會提供資源總大小，而 Accept-Ranges 標頭則會指出對應的 GET 作業支援部分結果。 用戶端應用程式可以使用這些資訊，以較小的區塊來擷取影像。 第一個要求會使用 Range 標頭擷取前 2500 個位元組：

```HTTP
GET https://adventure-works.com/products/10?fields=productImage HTTP/1.1
Range: bytes=0-2499
```

回應訊息會藉由傳回 HTTP 狀態碼 206 來指出這是部分回應。 Content-Length 標頭能指出訊息本文傳回的實際位元組數目 (不是資源的大小)，而 Content-Range 標頭能指出這是資源的哪個部分 (4580 個位元組中的第 0-2499 個位元組)：

```HTTP
HTTP/1.1 206 Partial Content

Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 2500
Content-Range: bytes 0-2499/4580

[...]
```

來自用戶端應用程式的後續要求，可以擷取資源的其餘部分。

## <a name="use-hateoas-to-enable-navigation-to-related-resources"></a>使用 HATEOAS 方法來啟用相關資源的導覽

REST 背後的其中一個主要動機，是它應該可以在不需要事先知道 URI 配置的情況下瀏覽整組資源。 每個 HTTP GET 要求都應傳回讓您能透過回應包含之超連結尋找與要求物件直接相關之資源的資訊，您也應提供它描述每個資源上可供使用之作業的資訊。 此原則就是所謂的 HATEOAS (Hypertext as the Engine of Application State)。 系統實際上是有限的狀態機器，而每個要求的回應都包含從某個狀態轉換為另一個狀態所需的資訊。其他任何資訊都不是必要資訊。

> [!NOTE]
> 目前還沒有定義該如何為 HATEOAS 原則建立模型的標準或規格。 本節所示的範例將說明一個可行的解決方案。

例如，為了處理訂單與客戶之間的關聯性，訂單的表示法可能會包含用以識別訂單客戶可用作業的連結。 以下是可能的表示法：

```json
{
  "orderID":3,
  "productID":2,
  "quantity":4,
  "orderValue":16.60,
  "links":[
    {
      "rel":"customer",
      "href":"https://adventure-works.com/customers/3",
      "action":"GET",
      "types":["text/xml","application/json"]
    },
    {
      "rel":"customer",
      "href":"https://adventure-works.com/customers/3",
      "action":"PUT",
      "types":["application/x-www-form-urlencoded"]
    },
    {
      "rel":"customer",
      "href":"https://adventure-works.com/customers/3",
      "action":"DELETE",
      "types":[]
    },
    {
      "rel":"self",
      "href":"https://adventure-works.com/orders/3",
      "action":"GET",
      "types":["text/xml","application/json"]
    },
    {
      "rel":"self",
      "href":"https://adventure-works.com/orders/3",
      "action":"PUT",
      "types":["application/x-www-form-urlencoded"]
    },
    {
      "rel":"self",
      "href":"https://adventure-works.com/orders/3",
      "action":"DELETE",
      "types":[]
    }]
}
```

在此範例中，`links` 陣列有一組連結。 每個連結都代表相關實體上的作業。 每個連結的資料都包含關聯性 (「客戶」)、URI (`https://adventure-works.com/customers/3`)、HTTP 方法，以及支援的 MIME 類型。 這是用戶端應用程式要能夠叫用作業需要的所有資訊。

`links` 陣列也會包含關於已擷取資源本身的自我參考資訊。 這些項目具有自我關聯性。

傳回的連結集可能會變更，視資源的狀態而定。 這就是為何我們將超文字稱為「應用程式狀態的引擎」。

## <a name="versioning-a-restful-web-api"></a>符合 REST 限制的 Web API 版本控制

Web API 維持靜態的可能性極低。 隨著商務需求變更，我們可能會加入新資源集合、資源之間的關聯性可能會改變，也會修改資源中的資料結構。 雖然更新 Web API 來處理新的或不同的需求是相對簡單的程序，不過您必須將這類變更對取用 Web API 之用戶端應用程式所造成的效果納入考量。 問題在於雖然設計和實作 Web API 的開發人員擁有該 API 完整的控制能力，不過對於從遠端作業之第三方組織所建置的用戶端應用程式，開發人員並沒有相同程度的控制能力。 主要的要務是讓現有用戶端應用程式在未變更的情況下繼續運作，同時允許新用戶端應用程式充分運用新功能和資源。

版本控制可讓 Web API 指定其公開的功能和資源，而用戶端應用程式則可以提交導向特定版本之功能或資源的要求。 下列小節描述幾個不同的方法，每個方法都有其優點和缺點。

### <a name="no-versioning"></a>無版本控制

這是最簡單的方法，而且也是某些內部 API 可接收的方法。 重大變更可能會以新資源或新連結來呈現。 將內容加入現有資源可能不會成為重大變更，因為未預期要查看此內容的用戶端應用程式會直接忽略。

例如，傳送給 URI `https://adventure-works.com/customers/3` 的要求應該會傳回單一客戶的詳細資料，其中包含用戶端應用程式所預期的 `id`、`name` 和 `address` 欄位：

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

> [!NOTE]
> 為求簡單，本節所示的範例回應不包含 HATEOAS 連結。

如果您將 `DateCreated` 欄位加入客戶資源的結構描述，回應看起來會像這樣：

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":"1 Microsoft Way Redmond WA 98053"}
```

如果現有用戶端應用程式能略過無法辨認的欄位，它們可能會繼續正常運作，而新的用戶端應用程式可以在經過設計後處理這個新欄位。 不過，如果資源的結構描述發生更巨大的變動 (如移除或重新命名欄位)，或資源之間的關聯性改變，這些變更可能會構成阻礙現有用戶端應用程式正常運作的重大變更。 在這些情況下，您應該考慮下列其中一個方法。

### <a name="uri-versioning"></a>URI 版本控制

每次修改 Web API 或變更資源的結構描述時，您會在每個資源的 URI 加入版本號碼。 早已存在的 URI 應維持先前的運作，傳回符合原始結構描述的資源。

延伸上述範例，如果將 `address` 欄位重建為包含位址之每個構成組件的子欄位 (如 `streetAddress`、`city`、`state` 及 `zipCode`)，您可以透過包含版本號碼的 URI (如 `https://adventure-works.com/v2/customers/3`) 公開這個版本的資源：

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":{"streetAddress":"1 Microsoft Way","city":"Redmond","state":"WA","zipCode":98053}}
```

這個版本控制機制非常簡單，但需仰賴伺服器將要求路由傳送到適當端點。 不過，在經過數個反覆項目後當 Web API 成熟時，它會變得難以揮灑，因此伺服器必須支援許多不同的版本。 此外，從純化論者的觀點來看，在所有情況下用戶端應用程式都在擷取相同的資料 (客戶 3)，所以 URI 不應該因版本而有所不同。 此配置也會讓 HATEOAS 的實作變得更複雜，因為所有連結都需要在它們的 URI 中包含版本號碼。

### <a name="query-string-versioning"></a>查詢字串版本控制

與其提供多個 URI，您可以在附加至 HTTP 要求的查詢字串中，藉由使用參數來指定資源的版本，如 `https://adventure-works.com/customers/3?version=2`。 如果較舊的用戶端應用程式省略版本參數，它應該預設成有意義的值 (如 1)。

這個方法具有語意上的優點，因為您總是從相同的 URI 擷取相同的資源，不過這還是要取決於處理要求以剖析查詢字串，然後回傳適當 HTTP 回應的程式碼。 這個方法也需要面臨與實作 HATEOAS 同等複雜的 URI 版本控制機制。

> [!NOTE]
> 某些較舊的網頁瀏覽器和 Web Proxy 不會快取在 URI 中包含查詢字串的要求回應。 這可能會降低使用 web API，並可在這類網頁瀏覽器內執行的 web 應用程式的效能。

### <a name="header-versioning"></a>標頭版本控制

與其以查詢字串參數的形式附加版本號碼，您可以實作指出資源版本的自訂標頭。 這個方法需要用戶端應用程式將適當標頭加入任何要求中，不過如果省略版本標頭，處理用戶端要求的程式碼可以使用預設值 (第 1 版)。 下列範例使用名為 *Custom-Header* 的自訂標頭。 此標頭的值指出 Web API 的版本。

第 1 版：

```HTTP
GET https://adventure-works.com/customers/3 HTTP/1.1
Custom-Header: api-version=1
```

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

第 2 版：

```HTTP
GET https://adventure-works.com/customers/3 HTTP/1.1
Custom-Header: api-version=2
```

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":{"streetAddress":"1 Microsoft Way","city":"Redmond","state":"WA","zipCode":98053}}
```

請注意，如同先前的兩個方法，實作 HATEOAS 需要將適當的自訂標頭加入任何連結中。

### <a name="media-type-versioning"></a>媒體類型版本控制

當用戶端應用程式將 HTTP GET 要求傳送至 Web 伺服器時，它應該會使用 Accept 標頭來規定可處理的內容格式 (如本指引稍早所述)。 Accept 標頭的目的經常是讓用戶端應用程式指定回應本文應該是 XML、JSON 或其他某些用戶端可剖析的通用格式。 不過，您也可以定義自訂媒體類型，加入讓用戶端應用程式指定預期之資源版本的資訊。 下列範例顯示將 Accept 標頭指定為 application/vnd.adventure-works.v1+json 值的要求。 Vnd.adventure works.v1 元素指示 Web 伺服器傳回第 1 版的資源，而 json 元素指定回應本文的格式應該是 JSON：

```HTTP
GET https://adventure-works.com/customers/3 HTTP/1.1
Accept: application/vnd.adventure-works.v1+json
```

處理要求的程式碼會負責處理 Accept 標頭且盡可能遵循 (用戶端應用程式可能會在 Accept 標頭中指定多個格式。不論如何，Web 伺服器可以選擇最合適的回應本文格式)。 Web 伺服器會使用 Content-Type 標頭確認回應本文中的資料格式：

```HTTP
HTTP/1.1 200 OK
Content-Type: application/vnd.adventure-works.v1+json; charset=utf-8

{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

如果 Accept 標頭未指定任何已知的媒體類型，Web 伺服器可能會產生 HTTP 406 (無法接受) 回應訊息，或以預設媒體類型傳回訊息。

這個方法可以說是最純粹的版本控制機制，自然也符合 HATEOAS 的本質，也就是在資源連結中包含 MIME 類型的相關資料。

> [!NOTE]
> 在選擇版本控制策略時，您也應該將對效能的潛在影響 (特別是 Web 伺服器上的快取) 納入考量。 有鑑於同一個 URI/查詢字串組合每次都會參考相同的資料，因此 URI 版本控制和查詢字串版本控制等配置具備易於快取的特性。
>
> 標頭版本控制和媒體類型版本控制等機制，通常需要額外的邏輯來檢查自訂標頭或 Accept 標頭中的值。 在大規模的環境中，使用不同版本之 Web API 的眾多用戶端可能會導致伺服器端快取出現大量的重複資料。 如果用戶端應用程式透過實作快取的 Proxy 與 Web 伺服器通訊，而且只有在快取中目前沒有要求之資料的副本時才會將要求轉送到 Web 伺服器，這個問題可能會變得更嚴重。

## <a name="open-api-initiative"></a>Open API Initiative

[Open API Initiative](https://www.openapis.org/) 是由產業協會所建立，用來將各廠商間的 REST API 描述標準化。 隨著此計畫，Swagger 2.0 規格已重新命名為 OpenAPI 規格 (OAS)，並由 Open API 計畫管理。

您可以為您的 Web API 採用 OpenAPI。 考慮事項：

- OpenAPI 規格隨附一組對於應如何設計 REST API 已有定見的指導方針。 該指引對於互通性會有好處，但在設計您的 API 時需要更小心，才能符合規格。

- OpenAPI 會將合約優先方法升階，而不是將實作優先方法升階。 合約優先表示您會先設計 API 合約 (介面)，然後編寫實作合約的程式碼。

- Swagger 等工具可以從 API 合約產生用戶端程式庫或文件集。 例如，請參閱[使用 Swagger 的 ASP.NET Web API 說明頁面](/aspnet/core/tutorials/web-api-help-pages-using-swagger)。

## <a name="more-information"></a>詳細資訊

- [Microsoft REST API 指導方針](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md)。 設計公用 REST API 的詳細建議。

- [Web API 檢查清單](https://mathieu.fenniak.net/the-api-checklist/)。 在設計及實作 Web API 時應納入考量的實用項目清單。

- [Open API Initiative](https://www.openapis.org/)。 Open API 的文件和實作詳細資料。

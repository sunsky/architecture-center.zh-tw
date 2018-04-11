---
title: API 設計
description: 設計微服務的 API
author: MikeWasson
ms.date: 12/08/2017
ms.openlocfilehash: d85407f3092ddb5f77aacfea8def2784c4741eb9
ms.sourcegitcommit: 786bafefc731245414c3c1510fc21027afe303dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2017
---
# <a name="designing-microservices-api-design"></a>設計微服務：API 設計

微服務架構中良好的 API 設計很重要，因為服務之間的所有資料交換都會透過訊息或 API 進行。 API 必須很有效率才可避免建立[多對話 I/O](../antipatterns/chatty-io/index.md)。 因為服務是由獨立運作的小組所設計，所以 API 必須具備妥善定義的語意和版本控制配置，這樣更新時才不會中斷其他服務。

![](./images/api-design.png)

請務必區分兩種 API 類型：

- 用戶端應用程式呼叫的公用 API。 
- 服務間通訊所使用的後端 API。

這兩個使用案例有稍微不同的需求。 公用 API 必須與用戶端應用程式相容 (通常是指瀏覽器應用程式或原生行動裝置應用程式)。 大部分的情況下，這表示公用 API 會使用 REST over HTTP。 不過，對於後端 API，您需要將網路效能納入考量。 視您服務的精細度而定，服務間通訊可能會導致大量網路流量。 服務可能會快速地變成 I/O 密集型 (I/O Bound)。 基於這個理由，諸如序列化速度和承載大小等考量變得更加重要。 可取代 REST over HTTP 的一些常用方案包括 gRPC、Apache Avro 和 Apache Thrift。 這些通訊協定可支援二進位序列化，且通常比 HTTP 更有效率。

## <a name="considerations"></a>注意事項

以下是在選擇如何實作 API 時所要思考的一些事項。

**REST 與 RPC**。 請考慮使用 REST 樣式介面與 RPC 樣式介面之間的利弊。

- REST 可塑造資源，這可以是表達網域模型的自然方式。 它會根據 HTTP 指令動詞來定義統一介面，進而提升進化能力。 就冪等性、副作用和回應碼而論，它都有妥善定義的語意。 而且會強制執行無狀態通訊，進而改善延展性。 

- RPC 更加以作業或命令為導向。 因為 RPC 介面看似區域方法呼叫，所以可能會導致您設計過多對話的 API。 不過，這不表示 RPC 必須是多對話。 這只是表示您在設計介面時必須小心翼翼。

若為 RESTful 介面，最常見的選擇是使用 JSON 的 REST over HTTP。 若為 RPC 樣式介面，則有數個常用的架構，包括 gRPC、Apache Avro 和 Apache Thrift。

**效率**。 請從速度、記憶體和承載大小方面來考慮效率。 通常以 gRPC 為基礎的介面比 REST over HTTP 快速。

**介面定義語言 (IDL)**。 IDL 會用來定義 API 的方法、參數和傳回值。 IDL 可用來產生用戶端程式碼、序列化程式碼和 API 文件。 IDL 也可供 API 測試工具 (例如 Postman) 使用。 諸如 gRPC、Avro 和 Thrift 等架構會定義自己的 IDL 規格。 REST over HTTP 沒有標準的 IDL 格式，但 OpenAPI 是常見的選擇 (先前為 Swagger)。 您不使用正式定義語言，也可以建立 HTTP REST API，但之後會喪失程式碼產生和測試的優勢。

**序列化**。 如何透過連線將物件序列化？ 您可以選擇以文字為基礎的格式 (主要是 JSON) 和二進位格式 (例如通訊協定緩衝區)。 二進位格式通常比以文字為基礎的格式快速。 不過，JSON 有互通性方面的優勢，因為大部分語言和架構都支援 JSON 序列化。 有些序列化格式需要固定的結構描述，而有些序列化格式需要編譯結構描述定義檔。 在此情況下，您必須將這個步驟併入您的建置程序中。 

**架構和語言支援**。 幾乎每個架構和語言都支援 HTTP。 gRPC、Avro 和 Thrift 全都有 C++、C#、Java 和 Python 的程式庫。 Thrift 和 gRPC 也支援 Go。 

**相容性和互通性**。 如果您選擇 gRPC 之類的通訊協定，您可能需要公用 API 與後端之間的通訊協定轉譯層。 [閘道](./gateway.md)可以執行該項功能。 如果您使用服務網格，請考慮有哪些通訊協定與服務網格相容。 例如，linkerd 有 HTTP、Thrift 和 gRPC 的內建支援。 

我們的基準建議是選擇 REST over HTTP，除非您需要二進位通訊協定的效能優勢。 REST over HTTP 不需要任何特殊程式庫。 其產生的結合程度最低，因為呼叫端不需要用戶端虛設常式來與服務進行通訊。 有豐富的工具生態系統，可支援 RESTful HTTP 端點的結構描述定義、測試及監視。 最後，HTTP 與瀏覽器用戶端相容，因此您不需要用戶端與後端之間的通訊協定轉譯層。 

不過，如果您選擇 REST over HTTP，您應在開發程序初期進行效能和載入測試，以驗證該項目在您案例中的效能是否夠好。

## <a name="restful-api-design"></a>RESTful API 設計

有許多資源可用來設計 RESTful API。 以下是一些您會覺得有用的資源：

- [API 設計](../best-practices/api-design.md) 

- [API 實作](../best-practices/api-implementation.md) 

- [Microsoft REST API 指引](https://github.com/Microsoft/api-guidelines)

以下是一些要記住的特定考量。

- 請留意流失內部實作詳細資料或只是鏡映內部資料庫結構描述的 API。 API 應塑造網域。 這是服務之間的合約，而且最理想的狀況是只在新增功能時變更，而不只是因為您重構部分程式碼或正規化資料庫資料表而進行變更。 

- 不同類型的用戶端 (例如行動裝置應用程式和桌面網頁瀏覽器) 可能需要不同的承載大小或互動模式。 請考慮使用 [Backend for Frontend (BFF) 模式](../patterns/backends-for-frontends.md)來為每個用戶端建立個別的後端，以公開該用戶端的最佳介面。

- 對於有副作用的作業，請考慮讓其具有冪等性並當作 PUT 方法加以實作。 這麼做就能夠安全重試，並可改善恢復功能。 [擷取與工作流程](./ingestion-workflow.md#idempotent-vs-non-idempotent-operations)和[服務間通訊](./interservice-communication.md)章節會更詳細討論這個問題。

- HTTP 方法可以有非同步語意，其中方法會立即傳回回應，但服務不會同步完成此作業。 在此情況下，方法應傳回 [HTTP 202](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 回應碼，表示已接受要求進行處理，但處理尚未完成。

## <a name="mapping-rest-to-ddd-patterns"></a>將 REST 對應至 DDD 模式

實體、彙總及值物件等模式是設計用來對網域模型中的物件加上特定條件約束。 在許多 DDD 討論中，模式是使用物件導向 (OO) 語言概念 (像是建構函式或屬性 getter 和 setter) 來塑型。 例如，「值物件」應不可變。 在 OO 程式設計語言中，您就會在建構函式中指派值並且讓屬性變成唯讀，以強制執行這點：

```ts
export class Location {
    readonly latitude: number;
    readonly longitude: number;

    constructor(latitude: number, longitude: number) {
        if (latitude < -90 || latitude > 90) {
            throw new RangeError('latitude must be between -90 and 90');
        }
        if (longitude < -180 || longitude > 180) {
            throw new RangeError('longitude must be between -180 and 180');
        }
        this.latitude = latitude;
        this.longitude = longitude;
    }
}
```

在建置傳統的單體式應用程式時，這類編碼實務特別重要。 以大型程式碼為基底，許多子系統可能會使用 `Location` 物件，所以物件務必強制執行正確的行為。 

另一個範例是存放庫模式，可確保應用程式的其他部分不會直接讀取或寫入資料存放區：

![](./images/repository.png)

不過，在微服務架構中，服務不會共用相同的程式碼基底，也不會共用資料存放區。 相反地，服務會透過 API 通訊。 舉例來說，排程器服務向無人機服務要求無人機的相關資訊。 無人機服務有自己的無人機內部模型 (透過程式碼表示)。 但排程器不會看到該模型。 相反地，排程器會取回無人機實體的「表示法」&mdash;，或許是 HTTP 回應中的 JSON 物件。

![](./images/ddd-rest.png)

排程器服務無法修改無人機服務的內部模型，或寫入無人機服務的資料存放區。 這表示實作無人機服務的程式碼具有的公開介面區較小 (相較於傳統單體中的程式碼)。 如果無人機服務定義了「位置」類別，該類別的範圍會受到限制 &mdash; 沒有其他服務會直接取用該類別。 

基於這些原因，此指引並不注重編碼實務，因為編碼實務與戰略性 DDD 模式相關。 但結果是您也可以透過 REST API 塑造許多 DDD 模式。 

例如︰

- 彙總會自然對應至 REST 中的「資源」。 例如，傳遞彙總會經由傳遞 API 以資源形式公開。

- 彙總是一致性界限。 彙總上的作業絕不會讓任何一個彙總處於不一致的狀態。 因此，您應該避免建立允許用戶端操控彙總內部狀態的 API。 反而要偏愛以資源形式公開彙總的粗糙 API。

- 實體具有唯一的身分識別。 在 REST 中，資源有 URL 形式的唯一身分識別。 建立可對應至實體網域身分識別的資源 URL。 對用戶端而言，從 URL 到網域身分識別的對應可能不透明。

- 從根實體瀏覽，即可觸達彙總的子實體。 如果您遵循 [HATEOAS](https://en.wikipedia.org/wiki/HATEOAS) 原則，可以透過父實體表示法中的連結來觸達子實體。 

- 因為值物件不可變，所以藉由取代整個值物件來執行更新。 在 REST 中，透過 PUT 或 PATCH 要求來實作更新。 

- 存放庫可讓用戶端查詢、新增或移除集合中的物件，以及擷取基礎資料存放區的詳細資料。 在 REST 中，集合可以是與眾不同的資源，具有可供查詢集合或將實體新增至集合的方法。

當您設計 API 時，請思考 API 如何表示網域模型 (而不只是模型內的資料)，但也要考量資料的商務作業和條件約束。

| DDD 概念 | REST 對等項目 | 範例 | 
|-------------|-----------------|---------|
| 彙總 | 資源 | `{ "1":1234, "status":"pending"... }` | 
| 身分識別 | URL | `http://delivery-service/deliveries/1` |
| 子實體 | 連結 | `{ "href": "/deliveries/1/confirmation" }` |
| 更新值物件 | PUT 或 PATCH | `PUT http://delivery-service/deliveries/1/dropoff` |
| 存放庫 | 集合 | `http://delivery-service/deliveries?status=pending` |


## <a name="api-versioning"></a>API 版本控制

API 是服務與該服務用戶端或取用者之間的合約。 如果 API 變更，則有可能中斷相依於 API 的用戶端 (不論是外部用戶端或其他微服務)。 因此，最好將 API 變更數目降至最低。 通常，基礎實作中的變更不需要任何 API 變更。 不過，實際上在某個時間點，您會想要新增需要變更現有 API 的新功能。

可能的話，讓 API 變更具有回溯相容性。 例如，避免從模型中移除欄位，因為這可能會使預期有該欄位的用戶端中斷。 新增欄位不會中斷相容性，因為用戶端應忽略回應中他們所不了解的欄位。 不過，服務必須處理要求中較舊用戶端略過新欄位的情況。 

在您的 API 合約中支援版本控制。 如果您引入重大 API 變更，請引入新的 API 版本。 繼續支援前一個版本，並且讓用戶端選取要呼叫哪個版本。 有好幾種方法可執行這項操作。 其中一種方法是只要在相同服務中公開兩個版本。 另一個選項是並排執行服務的兩個版本，並根據 HTTP 路由規則，將要求路由至其中一個或另一個版本。 

![](./images/versioning1.svg)

支援多個版本會產生開發人員時間、測試和操作額外負荷方面的成本。 因此，最好盡快淘汰舊的版本。 若為內部 API，擁有該 API 的小組可與其他小組合作，協助他們遷移到新的版本。 這時擁有跨小組控管程序很有用。 若為外部 (公用) API，可能更加難以淘汰 API 版本，特別是在 API 由第三方或原生用戶端應用程式取用時。 

當服務實作變更時，以版本標記變更很實用。 針對錯誤進行疑難排解時，版本會提供重要資訊。 這對於根本原因分析很有幫助，可精確得知已呼叫哪個服務版本。 請考慮對服務版本使用[語意版本設定](https://semver.org/)。 語意版本設定使用 *MAJOR.MINOR.PATCH* 格式。 不過，用戶端只能依照主要版本號碼選取 API，或如果次要版本之間有重大 (但不間斷) 變更，則可能依照次要版本選取 API。 換句話說，用戶端在第 1 版和第 2 版 API 之間選取很合理，但選取 2.1.3 版就不合理。 如果您允許此層級的精細度，您可能有必須支援版本擴散的風險。 

如需 API 版本控制的進一步討論，請參閱 [RESTful Web API 版本控制](../best-practices/api-design.md#versioning-a-restful-web-api)。

> [!div class="nextstepaction"]
> [擷取與工作流程](./ingestion-workflow.md)
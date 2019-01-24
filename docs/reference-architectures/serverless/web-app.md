---
title: 無伺服器 Web 應用程式
titleSuffix: Azure Reference Architectures
description: 無伺服器 Web 應用程式和 Web API 的建議架構。
author: MikeWasson
ms.date: 10/16/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18, serverless
ms.openlocfilehash: 60af3df5bbb75d97d6ba797874c8b37319b2fad5
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54487384"
---
# <a name="serverless-web-application-on-azure"></a>Azure 上的無伺服器 Web 應用程式

此參考架構顯示[無伺服器](https://azure.microsoft.com/solutions/serverless/) Web 應用程式。 應用程式會為 Azure Blob 儲存體的靜態內容提供服務，並且使用 Azure Functions 來實作 API。 API 會從 Cosmos DB 讀取資料，並且將結果傳回至 Web 應用程式。 此架構的參考實作可在 [GitHub][github] 上取得。

![無伺服器 Web 應用程式的參考架構](./_images/serverless-web-app.png)

無伺服器一詞有兩個不同但相關的意思：

- **後端即服務** (BaaS)。 後端雲端服務，例如資料庫和儲存體，提供 API 讓用戶端應用程式直接連線到這些服務。
- **函式即服務** (FaaS)。 在此模型中，"function" 是部署至雲端的一段程式碼，在裝載環境內執行，將執行程式碼的伺服器完全摘要出來。

這兩個定義在概念上的共通之處，就是開發人員和 DevOps 人員不需要部署、設定或管理伺服器。 此參考架構著重於使用 Azure Functions 的 FaaS，雖然為 Azure Blob 儲存體的 Web 內容提供服務是 BaaS 的範例。 FaaS 的一些重要特性是：

1. 計算資源會視平台的需要，動態配置。
1. 以耗用量為基礎的定價：您只需支付用來執行程式碼的計算資源。
1. 計算資源會根據流量進行隨需調整，開發人員不需要進行任何設定。

函式會在外部觸發程序發生時執行，例如 HTTP 要求或訊息抵達佇列時。 這樣可以自然地為無伺服器架構提供[事件驅動架構樣式][event-driven]。 若要協調架構中元件之間的工作，請考慮使用訊息代理程式或 pub/sub 模式。 如需在 Azure 中傳訊技術之間做選擇的協助，請參閱[在傳遞訊息的 Azure 服務之間進行選擇][azure-messaging]。

## <a name="architecture"></a>架構

此架構由下列元件組成：

**Blob 儲存體**。 靜態 Web 內容 (例如 HTML、CSS 和 JavaScript 檔案) 會儲存在 Azure Blob 儲存體中，並且使用[靜態網站代管][static-hosting]來為用戶端提供服務。 透過 JavaScript 程式碼發生的所有動態互動，都會對後端 API 進行呼叫。 沒有伺服器端程式碼可用來轉譯網頁。 靜態網站代管支援索引文件和自訂 404 錯誤頁面。

> [!NOTE]
> 靜態網站代管目前處於[預覽][static-hosting-preview]狀態。

**CDN**。 使用 [Azure 內容傳遞網路][cdn] (CDN) 快取內容，以降低延遲而且更快速傳遞內容，以及佈建 HTTPS 端點。

**函式應用程式**。 [Azure Functions][functions] 是無伺服器計算選項。 它會使用事件驅動的模型，在該模型中一段程式碼 ("function") 是由觸發程序叫用。 在此架構中，當用戶端提出 HTTP 要求時，會叫用函式。 要求一律會透過 API 閘道路由傳送，如下所述。

**API 管理**。 [API 管理][apim]提供位於 HTTP 函式前方的 API 閘道。 您可以使用 API 管理來發佈和管理由用戶端應用程式使用的 API。 使用閘道有助於讓前端應用程式與後端 API 分離。 例如，API 管理可以重新撰寫 URL、在要求到達後端之前轉換要求、設定要求或回應標頭等等。

API 管理也可用來實作跨領域考量，例如：

- 強制採用使用量配額和頻率限制
- 驗證 OAuth 權杖以進行驗證
- 啟用跨原始來源要求 (CORS)
- 快取回應
- 監視和記錄要求

如果您不需要 API 管理所提供的所有功能，另一個選項是使用[函式 Proxy][functions-proxy]。 Azure Functions 的這項功能可讓您藉由建立至後端函式的路由，為多個函式應用程式定義單一 API 介面。 函式 Proxy 也可以在 HTTP 要求和回應上執行有限的轉換。 不過，它們不提供與 API 管理相同的豐富原則型功能。

**Cosmos DB**。 [Cosmos DB][cosmosdb] 是多模型資料庫服務。 針對此案例，函式應用程式會擷取 Cosmos DB 的文件以回應用戶端的 HTTP GET 要求。

**Azure Active Directory** (Azure AD)。 使用者藉由使用其 Azure AD 認證來登入 Web 應用程式。 Azure AD 會傳回 API 的存取權杖，Web 應用程式會使用該權杖來驗證 API 要求 (請參閱[驗證](#authentication))。

**Azure 監視器**。 [監視器][monitor]會收集解決方案中部署的 Azure 服務相關效能計量。 透過儀表板中的資料視覺化，您可以看到解決方案的健康情況。 也會收集應用程式記錄。

**Azure Pipelines**。 [Pipelines][pipelines] 是持續整合 (CI) 和持續傳遞 (CD) 服務，它會建置、測試及部署應用程式。

## <a name="recommendations"></a>建議

### <a name="function-app-plans"></a>函式應用程式方案

Azure Functions 支援兩種裝載模型。 使用**使用情況方案**，會在程式碼執行時自動配置計算能力。  使用 **App Service** 方案，就會為您的程式碼配置一組 VM。 App Service 方案會定義 VM 數目和 VM 大小。

請注意，根據上述的定義，App Service 方案並不是嚴格的*無伺服器*。 程式設計模型相同，不過，相同函式程式碼可以在使用情況方案和 App Service 方案中執行。

以下是選擇要使用哪個類型方案時要考慮的一些因素：

- **冷啟動**。 使用使用情況方案，最近尚未叫用的函式將會在下次執行時發生一些額外的延遲。 這個額外的延遲是因為配置以及準備執行階段環境。 通常是數秒鐘，但是會因為一些因素而異，包括需要載入的相依性數目。 如需詳細資訊，請參閱[了解無伺服器冷啟動][functions-cold-start]。 冷啟動通常更關注於互動式工作負載 (HTTP 觸發程序) 而非非同步訊息驅動工作負載 (佇列或事件中樞觸發程序)，因為使用者會直接觀察到額外的延遲。
- **逾時期間**。  在使用情況方案中，函式執行會在一段[可設定][functions-timeout]的時間之後逾時 (最多 10 分鐘)
- **虛擬網路隔離**。 使用 App Service 方案可讓函式在 [App Service 環境][ase]內執行，這是專用和隔離的裝載環境。
- **定價模型**。 使用情況方案是依據執行次數和資源使用情況 (記憶體 &times; 執行時間) 來計費。 App Service 方案是每小時依據 VM 執行個體 SKU 來計費。 通常，使用情況方案比 App Service 方案便宜，因為您只需支付您所使用的計算資源。 如果您的流量有尖峰與低谷，更是如此。 不過，如果應用程式有常態的大量輸送量，App Service 方案可能會比使用情況方案還要划算。
- **調整大小**。 使用情況模型的一大優點是，它會根據連入流量視需要動態調整。 雖然這個調整相當快速，還是有緩慢的期間。 對於某些工作負載，您可以故意過度佈建 VM，以便處理暴增的流量而不會有緩慢的時間。 在此情況下，請考慮 App Service 方案。

### <a name="function-app-boundaries"></a>函式應用程式界限

函式應用程式可主控一或多個函式的執行。 您可以使用函式應用程式將數個函式群組在一起，作為邏輯單元。 在函式應用程式中，函式會共用相同的應用程式設定、裝載方案，以及部署生命週期。 每個函式應用程式都有自己的主機名稱。

使用函式應用程式將共用相同生命週期和設定的函式群組在一起。 不會共用相同生命週期的函式應該裝載在不同的函式應用程式中。

請考慮採用微服務方法，其中每個函式應用程式代表一個微服務，可能包含數個相關的函式。 在微服務架構中，服務應該具有鬆散結合和高度功能一致性的特性。 若您可以變更一項服務，而不需要同時更新其他服務，就表示是鬆散結合。 緊密表示服務具有單一、明確定義的用途。 如需有關這些概念的詳細討論，請參閱[設計微服務：領域分析][microservices-domain-analysis]。

### <a name="function-bindings"></a>Function 繫結

可行時使用 Functions [繫結][functions-bindings]。 繫結會提供宣告式方式，將您的程式碼連線至資料，並與其他 Azure 服務整合。 輸入繫結會從外部資料來源填入輸入參數。 輸出繫結會將函式的傳回值傳送至資料接收器，例如佇列或資料庫。

例如，參考實作中的 `GetStatus` 函式會使用 Cosmos DB [輸入繫結][cosmosdb-input-binding]。 這個繫結是設定為在 Cosmos DB 中查詢文件，使用從 HTTP 要求中查詢字串採用的查詢參數。 如果找到文件，則將它傳遞至函式作為參數。

```csharp
[FunctionName("GetStatusFunction")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "%COSMOSDB_DATABASE_NAME%",
        collectionName: "%COSMOSDB_DATABASE_COL%",
        ConnectionStringSetting = "COSMOSDB_CONNECTION_STRING",
        Id = "{Query.deviceId}",
        PartitionKey = "{Query.deviceId}")] dynamic deviceStatus,
    ILogger log)
{
    ...
}
```

藉由使用繫結，您不需要撰寫直接與服務交談的程式碼，如此可簡化函式程式碼，同時也能將資料來源或接收器的詳細資料摘要出來。 不過，在某些情況下，您可能需要比繫結所提供更複雜的邏輯。 在此情況下，請直接使用 Azure 用戶端 SDK。

## <a name="scalability-considerations"></a>延展性考量

**Functions**。 針對使用情況方案，HTTP 觸發程序是根據流量進行調整。 並行函式執行個體的數目有限制，但是每個執行個體一次可以處理一個以上的要求。 針對 App Service 方案，HTTP 觸發程序是根據 VM 執行個體的數目進行調整，該數目可以是固定值，或是根據一組自動調整規則來自動調整。 如需詳細資訊，請參閱 [Azure Functions 規模調整和裝載][functions-scale]。

**Cosmos DB**。 Cosmos DB 的輸送量容量會以[要求單位][ru] (RU) 來測量。 1 RU 的輸送量會對應至需要 GET 1KB 文件的輸送量。 若要將 Cosmos DB 容器調整超過 10,000 RU，建立容器時您必須指定[分割區索引鍵][partition-key]，並在您建立的每份文件中包含分割區索引鍵。 如需有關分割區索引鍵的詳細資訊，請參閱[在 Azure Cosmos DB 中進行資料分割和調整][cosmosdb-scale]。

**API 管理**。 API 管理可以相應放大，並支援規則型自動調整。 請注意，調整程序需要至少 20 分鐘的時間。 如果您的流量暴增，您應該針對您預期的最大暴增流量進行佈建。 不過，自動調整對於處理每小時或每日流量變化相當有用。 如需詳細資訊，請參閱[自動調整 Azure API 管理執行個體][apim-scale]。

## <a name="disaster-recovery-considerations"></a>災害復原考量

這裡示範的部署是位於單一 Azure 區域中。 如需更有彈性的災害復原方法，請利用各種服務中的地區散發功能：

- API 管理支援多區域部署，可用來在任意數量的 Azure 區域之間發佈單一 API 管理執行個體。 如需詳細資訊，請參閱[如何將 Azure API 管理服務執行個體部署到多個 Azure 區域][api-geo]。

- 使用[流量管理員][tm]以將 HTTP 要求路由傳送到主要區域。 如果在該區域中執行的函式應用程式變得無法使用，流量管理員可以容錯移轉到次要區域。

- Cosmos DB 支援[多重主要區域][cosmosdb-geo]，可讓您寫入至新增到 Cosmos DB 帳戶的任何區域。 如果您未啟用多重主要，您仍然可以容錯移轉主要寫入區域。 Cosmos DB 用戶端 SDK 與 Azure Function 繫結會自動處理容錯移轉，因此您不需要更新任何應用程式組態設定。

## <a name="security-considerations"></a>安全性考量

### <a name="authentication"></a>驗證

參考實作中的 `GetStatus` API 會使用 Azure AD 來驗證要求。 Azure AD 支援 Open ID Connect 通訊協定，該通訊協定是以 OAuth 2 通訊協定為基礎所建置的驗證通訊協定。

在此架構中，用戶端應用程式是在瀏覽器中執行的單頁應用程式 (SPA)。 這種類型的用戶端應用程式無法隱藏用戶端密碼或授權碼，因此隱含授與流程是適當的。 (請參閱[應該使用何種 OAuth 2.0 流程？][oauth-flow])。 以下是整體流程︰

1. 使用者在 Web 應用程式中按一下 [登入] 連結。
1. 瀏覽器重新導向至 Azure AD 登入頁面。
1. 使用者登入。
1. Azure AD 會重新導向回用戶端應用程式，在 URL 片段中包含存取權杖。
1. 當 Web 應用程式呼叫 API 時，它會在驗證標頭中包含存取權杖。 應用程式識別碼會作為存取權杖中的對象 ('aud') 宣告進行傳送。
1. 後端 API 會驗證存取權杖。

若要設定驗證：

- 在您的 Azure AD 租用戶中註冊應用程式。 這樣會產生應用程式識別碼，用戶端將其與登入 URL 包含在一起。

- 在函式應用程式內啟用 Azure AD 驗證。 如需詳細資訊，請參閱 [Azure App Service 中的驗證與授權][app-service-auth]。

- 藉由驗證存取權杖，將 [validate-jwt 原則][apim-validate-jwt]新增至 API 管理以預先授權要求。

如需詳細資訊，請參閱 [GitHub 讀我檔案][readme]。

建議您在 Azure AD 中，為用戶端應用程式和後端 API 建立個別的應用程式。 授予用戶端應用程式權限來呼叫 API。 這種方法可讓您彈性地定義多個 API 和用戶端，並控制每個 API 和用戶端的權限。

在 API 中，使用[範圍][scopes]來為應用程式提供對使用者要求權限的精確控制。 例如，API 可能包含 `Read` 和 `Write` 範圍，而特定用戶端應用程式可能會要求使用者僅授權 `Read` 權限。

### <a name="authorization"></a>Authorization

在許多應用程式中，後端 API 必須檢查使用者是否具有執行指定動作的權限。 建議使用[宣告型授權][claims]，其中使用者的相關資訊是由識別提供者 (在此案例中是 Azure AD) 傳送，用來進行授權決策。

某些宣告是在 Azure AD 傳回給用戶端的識別碼權杖內提供。 您可以藉由檢查要求中的 X-MS-CLIENT-PRINCIPAL 標頭，從函式應用程式內取得這些宣告。 針對其他宣告，請使用 [Microsoft Graph][graph] 來查詢 Azure AD (登入時需要使用者同意)。

例如，當您在 Azure AD 中註冊應用程式時，可以在應用程式的註冊資訊清單中定義一組應用程式角色。 當使用者登入應用程式時，Azure AD 會針對已授與使用者的每個角色 (包括透過群組成員資格繼承的角色) 包含「角色」宣告。

在參考實作中，函式會檢查已驗證使用者是否為 `GetStatus` 應用程式角色的成員。 如果不是，則函式會傳回 HTTP 未經授權 (401) 回應。

```csharp
[FunctionName("GetStatusFunction")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "%COSMOSDB_DATABASE_NAME%",
        collectionName: "%COSMOSDB_DATABASE_COL%",
        ConnectionStringSetting = "COSMOSDB_CONNECTION_STRING",
        Id = "{Query.deviceId}",
        PartitionKey = "{Query.deviceId}")] dynamic deviceStatus,
    ILogger log)
{
    log.LogInformation("Processing GetStatus request.");

    return req.HandleIfAuthorizedForRoles(new[] { GetDeviceStatusRoleName },
        async () =>
        {
            string deviceId = req.Query["deviceId"];
            if (deviceId == null)
            {
                return new BadRequestObjectResult("Missing DeviceId");
            }

            return await Task.FromResult<IActionResult>(deviceStatus != null
                    ? (ActionResult)new OkObjectResult(deviceStatus)
                    : new NotFoundResult());
        },
        log);
}
```

在此程式碼範例中，`HandleIfAuthorizedForRoles` 是擴充方法，會檢查角色宣告，並且在找不到宣告時傳回 HTTP 401。 您可以在[這裡][HttpRequestAuthorizationExtensions]找到原始程式碼。 請注意，`HandleIfAuthorizedForRoles` 會採用 `ILogger` 參數。 您應該記錄未經授權的要求，以便擁有稽核記錄，並且可以視需要診斷問題。 在此同時，請避免洩漏 HTTP 401 回應內的任何詳細資訊。

### <a name="cors"></a>CORS

在此參考架構中，Web 應用程式和 API 不會共用相同的來源。 這表示當應用程式呼叫 API 時，它是跨原始來源要求。 瀏覽器安全性可防止網頁對另一個網域提出 AJAX 要求。 這項限制也稱為同源原則，可防止惡意網站從另一個網站讀取敏感性資料。 若要啟用跨原始來源要求，請將跨原始來源資源共用 (CORS) [原則][cors-policy] 新增至 API 管理閘道：

```xml
<cors allow-credentials="true">
    <allowed-origins>
        <origin>[Website URL]</origin>
    </allowed-origins>
    <allowed-methods>
        <method>GET</method>
    </allowed-methods>
    <allowed-headers>
        <header>*</header>
    </allowed-headers>
</cors>
```

在此範例中，**allow-credentials** 屬性是 **true**。 這樣會授權瀏覽器以傳送具有要求的認證 (包括 Cookie)。 否則，瀏覽器預設不會傳送具有跨原始來源要求的認證。

> [!NOTE]
> 將 **allow-credentials** 設為 **true** 時請小心謹慎，因為這樣表示網站可以在使用者不知情的情況下，代表使用者將使用者的認證傳送至您的 API。 您必須信任允許的來源。

### <a name="enforce-https"></a>強制使用 HTTPS

若要取得最大安全性，在整個要求管線中都必須使用 HTTPS：

- **CDN**。 Azure CDN 預設支援 `*.azureedge.net` 子網域上的 HTTPS。 若要在 CDN 中針對自訂網域名稱啟用 HTTPS，請參閱[教學課程：在 Azure CDN 自訂網域上設定 HTTPS][cdn-https]。

- **靜態網站代管**。 在儲存體帳戶上啟用 [[需要安全傳輸][storage-https]] 選項。 啟用此選項時，儲存體帳戶只允許來自安全 HTTPS 連線的要求。

- **API 管理**。 將 API 設為僅使用 HTTPS 通訊協定。 您可以在 Azure 入口網站中或透過 Resource Manager 範本進行設定：

    ```json
    {
        "apiVersion": "2018-01-01",
        "type": "apis",
        "name": "dronedeliveryapi",
        "dependsOn": [
            "[concat('Microsoft.ApiManagement/service/', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "displayName": "Drone Delivery API",
            "description": "Drone Delivery API",
            "path": "api",
            "protocols": [ "HTTPS" ]
        },
        ...
    }
    ```

- **Azure Functions**。 啟用 [[僅限 HTTPS][functions-https]] 設定。

### <a name="lock-down-the-function-app"></a>鎖定函式應用程式

對函式的所有呼叫都應該經過 API 閘道。 您可以如下所示地達成此目的︰

- 設定函式應用程式以要求函式金鑰。 API 管理閘道會在呼叫函式應用程式時，包含函式金鑰。 這樣可防止用戶端略過閘道而直接呼叫函式。

- API 管理閘道具有[靜態 IP 位址][apim-ip]。 限制 Azure Function，僅允許來自該靜態 IP 位址的呼叫。 如需詳細資訊，請參閱 [Azure App Service 靜態 IP 限制][app-service-ip-restrictions]。 (這項功能僅適用於標準層服務。)

### <a name="protect-application-secrets"></a>保護應用程式祕密

請勿將應用程式祕密 (例如資料庫認證) 存放在您的程式碼或組態檔中。 而是使用應用程式設定，該設定以加密方式儲存在 Azure 中。 如需詳細資訊，請參閱 [Azure App Service 和 Azure Functions 中的安全性][app-service-security]。

或者，您可以將應用程式祕密儲存在 Key Vault 中。 這樣可讓您集中儲存祕密、控制其散佈，及監視祕密如何及何時遭到存取。 如需詳細資訊，請參閱[設定 Azure Web 應用程式以從 Key Vault 讀取祕密][key-vault-web-app]。 不過請注意，Functions 觸發程序和繫結會從應用程式設定載入其組態設定。 沒有內建方法可用來設定觸發程序和繫結使用 Key Vault 祕密。

## <a name="devops-considerations"></a>DevOps 考量

### <a name="deployment"></a>部署

若要部署函式應用程式，我們建議您使用[封裝檔案][functions-run-from-package] (「從套件執行」)。 使用此方法，您將 zip 檔案上傳至 Blob 儲存體容器，且 Functions 執行階段會將 zip 檔案裝載為唯讀檔案系統。 這是不可部分完成的作業，可以減少部署失敗而使應用程式處於不一致狀態的情況。 它也可以提升冷啟動時間，特別是針對 Node.js 應用程式，因為所有的檔案都是一次交換。

### <a name="api-versioning"></a>API 版本控制

API 是服務與用戶端之間的合約。 在此架構中，API 合約會在 API 管理層加以定義。 「API 管理」支援兩個截然不同但互補的[版本控制概念][apim-versioning]：

- *版本*可讓 API 取用者根據其需求選擇 API 版本，例如，v1 和 v2。

- *修訂*允許 API 管理員在 API 中進行非中斷變更，並部署這些變更以及變更記錄，以便告知 API 取用者所做變更。

如果您在 API 中進行重大變更，請在 API 管理中發行新版本。 在個別函式應用程式中，並排部署新版本與原始版本。 這樣可讓您將現有的用戶端遷移至新的 API，而不會中斷用戶端應用程式。 最後，您可以取代舊版。 APIM 支援數個[版本控制配置][apim-versioning-schemes]：URL 路徑、HTTP 標頭或查詢字串。 如需 API 版本控制的詳細資訊，請參閱 [RESTful Web API 版本控制][api-versioning]。

如需不中斷 API 變更的更新，請將新版本部署到相同函式應用程式中的預備位置。 確認部署成功，然後將預備版本切換為生產環境版本。 在 API 管理中發佈修訂版。

## <a name="deploy-the-solution"></a>部署解決方案

若要部署此參考架構，請檢視 [GitHub 讀我檔案][readme]。

<!-- links -->

[api-versioning]: ../../best-practices/api-design.md#versioning-a-restful-web-api
[apim]: /azure/api-management/api-management-key-concepts
[apim-ip]: /azure/api-management/api-management-faq#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules
[api-geo]: /azure/api-management/api-management-howto-deploy-multi-region
[apim-scale]: /azure/api-management/api-management-howto-autoscale
[apim-validate-jwt]: /azure/api-management/api-management-access-restriction-policies#ValidateJWT
[apim-versioning]: /azure/api-management/api-management-get-started-publish-versions
[apim-versioning-schemes]: /azure/api-management/api-management-get-started-publish-versions#choose-a-versioning-scheme
[app-service-auth]: /azure/app-service/app-service-authentication-overview
[app-service-ip-restrictions]: /azure/app-service/app-service-ip-restrictions
[app-service-security]: /azure/app-service/app-service-security
[ase]: /azure/app-service/environment/intro
[azure-messaging]: /azure/event-grid/compare-messaging-services
[claims]: https://en.wikipedia.org/wiki/Claims-based_identity
[cdn]: https://azure.microsoft.com/services/cdn/
[cdn-https]: /azure/cdn/cdn-custom-ssl
[cors-policy]: /azure/api-management/api-management-cross-domain-policies
[cosmosdb]: /azure/cosmos-db/introduction
[cosmosdb-geo]: /azure/cosmos-db/distribute-data-globally
[cosmosdb-input-binding]: /azure/azure-functions/functions-bindings-cosmosdb-v2#input
[cosmosdb-scale]: /azure/cosmos-db/partition-data
[event-driven]: ../../guide/architecture-styles/event-driven.md
[functions]: /azure/azure-functions/functions-overview
[functions-bindings]: /azure/azure-functions/functions-triggers-bindings
[functions-cold-start]: https://blogs.msdn.microsoft.com/appserviceteam/2018/02/07/understanding-serverless-cold-start/
[functions-https]: /azure/app-service/app-service-web-tutorial-custom-ssl#enforce-https
[functions-proxy]: /azure/azure-functions/functions-proxies
[functions-run-from-package]: /azure/azure-functions/run-functions-from-deployment-package
[functions-scale]: /azure/azure-functions/functions-scale
[functions-timeout]: /azure/azure-functions/functions-scale#consumption-plan
[functions-zip-deploy]: /azure/azure-functions/deployment-zip-push
[graph]: https://developer.microsoft.com/graph/docs/concepts/overview
[key-vault-web-app]: /azure/key-vault/tutorial-web-application-keyvault
[microservices-domain-analysis]: ../../microservices/domain-analysis.md
[monitor]: /azure/azure-monitor/overview
[oauth-flow]: https://auth0.com/docs/api-auth/which-oauth-flow-to-use
[partition-key]: /azure/cosmos-db/partition-data
[pipelines]: /azure/devops/pipelines/index
[ru]: /azure/cosmos-db/request-units
[scopes]: /azure/active-directory/develop/v2-permissions-and-consent
[static-hosting]: /azure/storage/blobs/storage-blob-static-website
[static-hosting-preview]: https://azure.microsoft.com/blog/azure-storage-static-web-hosting-public-preview/
[storage-https]: /azure/storage/common/storage-require-secure-transfer
[tm]: /azure/traffic-manager/traffic-manager-overview

[github]: https://github.com/mspnp/serverless-reference-implementation
[HttpRequestAuthorizationExtensions]: https://github.com/mspnp/serverless-reference-implementation/blob/master/src/DroneStatus/dotnet/DroneStatusFunctionApp/HttpRequestAuthorizationExtensions.cs
[readme]: https://github.com/mspnp/serverless-reference-implementation/blob/master/README.md

---
title: 保護多租用戶應用程式中的後端 Web API
description: 如何保護後端 Web API。
author: MikeWasson
ms.date: 07/21/2017
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
pnp.series.title: Manage Identity in Multitenant Applications
pnp.series.prev: authorize
pnp.series.next: token-cache
ms.openlocfilehash: fd0ac254604470ba51ea00537490cfb22b224e80
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640170"
---
# <a name="secure-a-backend-web-api"></a>保護後端 Web API

[![GitHub](../_images/github.png) 程式碼範例][sample application]

[Tailspin 問卷] 應用程式使用後端 Web API 來管理問卷的 CRUD 作業。 例如，當使用者按一下 [我的問卷調查] 時，Web 應用程式會傳送 HTTP 要求到 Web API：

```http
GET /users/{userId}/surveys
```

Web API 會傳回 JSON 物件：

```http
{
  "Published":[],
  "Own":[
    {"Id":1,"Title":"Survey 1"},
    {"Id":3,"Title":"Survey 3"},
    ],
  "Contribute": [{"Id":8,"Title":"My survey"}]
}
```

Web API 不允許匿名要求，因此 Web 應用程式必須使用 OAuth 2 持有人權杖來驗證自己。

> [!NOTE]
> 這是伺服器對伺服器案例。 應用程式不會從瀏覽器用戶端對 API 進行任何 AJAX 呼叫。

您可以採取兩種主要方式：

* 委派的使用者識別。 Web 應用程式會驗證使用者的身分識別。
* 應用程式識別碼。 Web 應用程式會驗證其用戶端識別碼，使用 OAuth 2 用戶端認證流程中。

Tailspin 應用程式實作委派的使用者識別。 以下是兩者的主要差異：

**委派的使用者識別：**

* 傳送給 Web API 的持有人權杖包含了使用者識別。
* Web API 會根據使用者識別進行授權決策。
* 如果未授權使用者執行動作，Web 應用程式需要處理 Web API 的 403 (禁止) 錯誤。
* 一般而言，Web 應用程式仍會做出某些影響 UI的授權決策，例如顯示或隱藏 UI 項目。
* Web API 可能可以由不受信任的用戶端所使用，例如 JavaScript 應用程式或原生用戶端應用程式。

**應用程式識別碼：**

* Web API 不會取得使用者的相關資訊。
* Web API 不能執行任何根據使用者識別的授權。 所有的授權決策都是由 Web 應用程式所進行。  
* Web API 不能由未受信任的用戶端 (JavaScript 或原生用戶端應用程式) 使用。
* 實作這種方法可能較為容易，因為 Web API 中沒有授權邏輯。

這兩種方法中，Web 應用程式必須取得存取權杖，也就是呼叫 Web API 所需的認證。

* 如果是委派的使用者識別，權杖必須來自可代表使用者發行權杖的 IDP。
* 如果是用戶端認證，應用程式可能會從 IDP 取得權杖，或主控自己的權杖伺服器。 (但請勿重頭開始撰寫權杖伺服器，請使用經完整測試的架構，例如 [IdentityServer4]。)如果您使用 Azure AD 進行驗證，強烈建議從 Azure AD 取得存取權杖，即使是使用用戶端認證流程也一樣。

本文的其餘部分假設應用程式透過 Azure AD 進行驗證。

![取得存取權杖](./images/access-token.png)

## <a name="register-the-web-api-in-azure-ad"></a>在 Azure AD 中註冊 Web API

為了讓 Azure AD 發行 Web API 的持有人權杖，您需要設定 Azure AD。

1. 在 Azure AD 中註冊 Web API。

2. 新增 Web 應用程式的用戶端識別碼至 `knownClientApplications` 屬性中的 Web API 應用程式資訊清單。 請參閱 [更新應用程式資訊清單]。

3. 提供 Web 應用程式權限來呼叫 Web API。 在 Azure 管理入口網站中，您可以設定兩種類型的權限：適用於應用程式識別 (用戶端認證流程) 的「應用程式權限」或適用於委派之使用者識別的「委派權限」。

   ![委派的權限](./images/delegated-permissions.png)

## <a name="getting-an-access-token"></a>取得存取權杖

呼叫 Web API 之前，Web 應用程式會從 Azure AD 取得存取權杖。 在 .NET 應用程式中，使用[適用於 .NET 的 Azure AD 驗證程式庫 (ADAL)][ADAL]。

在 OAuth 2 授權程式碼流程中，應用程式會交換存取權杖的授權碼。 下列程式碼會使用 ADAL 來取得存取權杖。 `AuthorizationCodeReceived` 事件期間會呼叫這個程式碼。

```csharp
// The OpenID Connect middleware sends this event when it gets the authorization code.
public override async Task AuthorizationCodeReceived(AuthorizationCodeReceivedContext context)
{
    string authorizationCode = context.ProtocolMessage.Code;
    string authority = "https://login.microsoftonline.com/" + tenantID
    string resourceID = "https://tailspin.onmicrosoft.com/surveys.webapi" // App ID URI
    ClientCredential credential = new ClientCredential(clientId, clientSecret);

    AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
    AuthenticationResult authResult = await authContext.AcquireTokenByAuthorizationCodeAsync(
        authorizationCode, new Uri(redirectUri), credential, resourceID);

    // If successful, the token is in authResult.AccessToken
}
```

以下是各種所需的參數：

* `authority`。 衍生自已登入使用者的租用戶識別碼。 (非 SaaS 提供者的租用戶識別碼)  
* `authorizationCode`。 您從 IDP 取得的驗證程式碼。
* `clientId`。 Web 應用程式的用戶端識別碼。
* `clientSecret`。 Web 應用程式的用戶端密碼。
* `redirectUri` 。 重新導向 URI 設定 OpenID Connect。 這是 IDP 使用權杖進行回呼的位置。
* `resourceID`。 Web API 的應用程式識別碼 URI，當您在 Azure AD 中註冊 Web API 時所建立
* `tokenCache`。 快取存取權杖的物件。 請參閱 [權杖快取]。

如果 `AcquireTokenByAuthorizationCodeAsync` 成功，ADAL 會快取權杖。 稍後，您可以藉由呼叫 AcquireTokenSilentAsync 從快取取得權杖：

```csharp
AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
var result = await authContext.AcquireTokenSilentAsync(resourceID, credential, new UserIdentifier(userId, UserIdentifierType.UniqueId));
```

其中的 `userId` 是使用者的物件識別碼，可在 `http://schemas.microsoft.com/identity/claims/objectidentifier` 宣告中找到。

## <a name="using-the-access-token-to-call-the-web-api"></a>使用存取權杖呼叫 Web API

一旦您擁有權杖，請在 HTTP 要求的授權標頭中將它傳送至 Web API。

```http
Authorization: Bearer xxxxxxxxxx
```

下列的 Surveys 應用程式的擴充方法，會使用 **HttpClient** 類別在 HTTP 要求上設定授權標頭。

```csharp
public static async Task<HttpResponseMessage> SendRequestWithBearerTokenAsync(this HttpClient httpClient, HttpMethod method, string path, object requestBody, string accessToken, CancellationToken ct)
{
    var request = new HttpRequestMessage(method, path);
    if (requestBody != null)
    {
        var json = JsonConvert.SerializeObject(requestBody, Formatting.None);
        var content = new StringContent(json, Encoding.UTF8, "application/json");
        request.Content = content;
    }

    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
    request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await httpClient.SendAsync(request, ct);
    return response;
}
```

## <a name="authenticating-in-the-web-api"></a>在 Web API 中進行驗證

Web API 必須驗證持有人權杖。 在 ASP.NET Core 中，您可以使用 [Microsoft.AspNet.Authentication.JwtBearer][JwtBearer] 套件。 此封裝提供中介軟體，讓應用程式能接收 OpenID Connect 持有人權杖。

在您的 Web API `Startup` 類別中註冊中介軟體

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ApplicationDbContext dbContext, ILoggerFactory loggerFactory)
{
    // ...

    app.UseJwtBearerAuthentication(new JwtBearerOptions {
        Audience = configOptions.AzureAd.WebApiResourceId,
        Authority = Constants.AuthEndpointPrefix,
        TokenValidationParameters = new TokenValidationParameters {
            ValidateIssuer = false
        },
        Events= new SurveysJwtBearerEvents(loggerFactory.CreateLogger<SurveysJwtBearerEvents>())
    });

    // ...
}
```

* **對象**。 針對 Web API 將此選項設定為當您在 Azure AD 中註冊 Web API 時建立的應用程式識別碼 URL。
* **授權單位**。 對於多租用戶應用程式，將此選項設定為 `https://login.microsoftonline.com/common/`。
* **TokenValidationParameters**。 對於多租用戶應用程式，請將 **ValidateIssuer** 設為 false。 這表示應用程式會驗證簽發者。
* **Events** 是衍生自 **JwtBearerEvents** 的類別。

### <a name="issuer-validation"></a>簽發者驗證

驗證 **JwtBearerEvents.TokenValidated** 事件中的權杖簽發者。 簽發者會在 "iss" 宣告中傳送。

在 Surveys 應用程式中，Web API 不會處理 [租用戶註冊]。 因此，它只會檢查簽發者是否已經在應用程式資料庫中。 如果沒有，則會擲回例外狀況，這會導致驗證失敗。

```csharp
public override async Task TokenValidated(TokenValidatedContext context)
{
    var principal = context.Ticket.Principal;
    var tenantManager = context.HttpContext.RequestServices.GetService<TenantManager>();
    var userManager = context.HttpContext.RequestServices.GetService<UserManager>();
    var issuerValue = principal.GetIssuerValue();
    var tenant = await tenantManager.FindByIssuerValueAsync(issuerValue);

    if (tenant == null)
    {
        // The caller was not from a trusted issuer. Throw to block the authentication flow.
        throw new SecurityTokenValidationException();
    }

    var identity = principal.Identities.First();

    // Add new claim for survey_userid
    var registeredUser = await userManager.FindByObjectIdentifier(principal.GetObjectIdentifierValue());
    identity.AddClaim(new Claim(SurveyClaimTypes.SurveyUserIdClaimType, registeredUser.Id.ToString()));
    identity.AddClaim(new Claim(SurveyClaimTypes.SurveyTenantIdClaimType, registeredUser.TenantId.ToString()));

    // Add new claim for Email
    var email = principal.FindFirst(ClaimTypes.Upn)?.Value;
    if (!string.IsNullOrWhiteSpace(email))
    {
        identity.AddClaim(new Claim(ClaimTypes.Email, email));
    }
}
```

如本範例所示，您也可以使用 **TokenValidated** 事件來修改宣告。 請記住，宣告直接來自 Azure AD。 如果 Web 應用程式修改它所取得的宣告，這些變更將不會顯示在 Web API 接收的持有人權杖中。 如需詳細資訊，請參閱[宣告轉換][claims-transformation]。

## <a name="authorization"></a>授權

如需授權的一般討論，請參閱[角色和資源型授權][Authorization]。

JwtBearer 中介軟體會處理授權回應。 例如，若要限制控制器動作，以已驗證的使用者，請使用 **[Authorize]** 屬性，並指定**JwtBearerDefaults.AuthenticationScheme**做為驗證配置：

```csharp
[Authorize(ActiveAuthenticationSchemes = JwtBearerDefaults.AuthenticationScheme)]
```

如果使用者未經過驗證，這樣就會傳回 401 狀態碼。

若要依授權原則來限制控制器動作，請在 **[Authorize]** 屬性中指定原則名稱：

```csharp
[Authorize(Policy = PolicyNames.RequireSurveyCreator)]
```

如果使用者未經過驗證，這樣就會傳回 401 狀態碼，如果使用者已經過驗證但未授權則會傳回 403 狀態碼。 啟動時註冊原則：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthorization(options =>
    {
        options.AddPolicy(PolicyNames.RequireSurveyCreator,
            policy =>
            {
                policy.AddRequirements(new SurveyCreatorRequirement());
                policy.RequireAuthenticatedUser(); // Adds DenyAnonymousAuthorizationRequirement
                policy.AddAuthenticationSchemes(JwtBearerDefaults.AuthenticationScheme);
            });
        options.AddPolicy(PolicyNames.RequireSurveyAdmin,
            policy =>
            {
                policy.AddRequirements(new SurveyAdminRequirement());
                policy.RequireAuthenticatedUser(); // Adds DenyAnonymousAuthorizationRequirement
                policy.AddAuthenticationSchemes(JwtBearerDefaults.AuthenticationScheme);
            });
    });

    // ...
}
```

[**下一主題**][token cache]

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[JwtBearer]: https://www.nuget.org/packages/Microsoft.AspNet.Authentication.JwtBearer

[Tailspin 問卷]: tailspin.md
[IdentityServer4]: https://github.com/IdentityServer/IdentityServer4
[更新應用程式資訊清單]: ./run-the-app.md#update-the-application-manifests
[權杖快取]: token-cache.md
[租用戶註冊]: signup.md
[claims-transformation]: claims.md#claims-transformations
[Authorization]: authorize.md
[sample application]: https://github.com/mspnp/multitenant-saas-guidance
[token cache]: token-cache.md

---
title: 多組織用戶共享應用程式中的授權
description: 如何在多組織用戶共享應用程式中執行授權
author: MikeWasson
ms:date: 07/21/2017
pnp.series.title: Manage Identity in Multitenant Applications
pnp.series.prev: app-roles
pnp.series.next: web-api
ms.openlocfilehash: 321dc52a3e6f803a032288c2341e490cdba8c20a
ms.sourcegitcommit: 9a2d56ac7927f0a2bbfee07198d43d9c5cb85755
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2018
ms.locfileid: "36327648"
---
# <a name="role-based-and-resource-based-authorization"></a>角色和資源型授權

[![GitHub](../_images/github.png) 程式碼範例][sample application]

我們的 [參考實作] 是 ASP.NET Core 應用程式。 本文章中，我們將探討進行授權的兩個一般方法，使用 ASP.NET Core 提供的授權 API。

* **Role-based authorization**。 根據指派給使用者的角色授權動作。 例如，某些動作需要系統管理員角色。
* **以資源為基礎的授權**。 根據特定的資源授權動作。 例如，每個資源都有一個擁有者。 擁有者可以刪除該資源；其他使用者則不行。

一般的應用程式會採用兩種的混合。 例如，若要刪除資源，使用者必須是資源擁有者*或*系統管理員。

## <a name="role-based-authorization"></a>以角色為基礎的授權
[Tailspin Surveys][Tailspin] 應用程式會定義下列角色：

* 系統管理員。 可在所有屬於該租用戶的問卷上執行所有 CRUD 作業。
* 建立者。 可以建立新的問卷
* 讀取者。 可以讀取任何屬於該租用戶的問卷

套用至應用程式*使用者*的規則。 在 Surveys 應用程式中，使用者可能是系統管理員、建立者或讀取者其中之一。

如需如何定義及管理角色的討論，請參閱 [應用程式角色]。

不論您如何管理角色，您的授權程式碼看起來都會很相似。 ASP.NET Core 有一個稱為[授權原則][policies]的抽象概念。 使用此功能，您會使用程式碼定義授權原則，並將那些原則套用至控制器動作。 該原則已從控制器分離出來。

### <a name="create-policies"></a>建立原則
若要定義原則，請先建立實作 `IAuthorizationRequirement`的類別。 從 `AuthorizationHandler`衍生是最簡單的方式。 在 `Handle` 方法中，檢查相關的宣告。

以下是來自 Tailspin Surveys 應用程式的範例：

```csharp
public class SurveyCreatorRequirement : AuthorizationHandler<SurveyCreatorRequirement>, IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, SurveyCreatorRequirement requirement)
    {
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin) || 
            context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            context.Succeed(requirement);
        }
        return Task.FromResult(0);
    }
}
```

這個類別會定義使用者建立新問卷的需求。 使用者必須是 SurveyAdmin 或 SurveyCreator 角色。

在您的啟動類別中，定義包含一或多個需求的具名原則。 如果有多個需求，使用者必須符合*每個*需求才能獲得授權。 下列程式碼定義兩個原則：

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy(PolicyNames.RequireSurveyCreator,
        policy =>
        {
            policy.AddRequirements(new SurveyCreatorRequirement());
            policy.RequireAuthenticatedUser(); // Adds DenyAnonymousAuthorizationRequirement 
            // By adding the CookieAuthenticationDefaults.AuthenticationScheme, if an authenticated
            // user is not in the appropriate role, they will be redirected to a "forbidden" page.
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });

    options.AddPolicy(PolicyNames.RequireSurveyAdmin,
        policy =>
        {
            policy.AddRequirements(new SurveyAdminRequirement());
            policy.RequireAuthenticatedUser();  
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });
});
```

此程式碼也會設定驗證配置，它會告訴 ASP.NET 如果授權失敗時應執行哪一個驗證中介軟體。 在此情況下，我們指定 Cookie 驗證中介軟體，因為 cookie 驗證中介軟體可將使用者重新導向至「禁止」頁面。 [禁止] 頁面的位置會是在 Cookie 中介軟體的 `AccessDeniedPath` 選項中設定，請參閱[設定驗證中介軟體]。

### <a name="authorize-controller-actions"></a>授權控制器動作
最後，若要在 MVC 控制器中授權動作，請在 `Authorize` 屬性中設定原則：

```csharp
[Authorize(Policy = PolicyNames.RequireSurveyCreator)]
public IActionResult Create()
{
    var survey = new SurveyDTO();
    return View(survey);
}
```

在舊版的 ASP.NET 中，您會在該屬性上設定 **Roles** 屬性：

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]
```

ASP.NET Core 中仍支援這樣設定，但相較於授權原則它有一些缺點：

* 它會假設訂定的宣告類型。 原則可以檢查任何宣告類型。 角色只是宣告的一種類型。
* 角色名稱是以硬式編碼寫在屬性中。 若使用原則，授權原則會全部在同一個位置，讓更新或甚至從組態設定載入變得更容易。
* 原則可支援簡單的角色成員資格無法表示的更複雜授權決策 (例如，年齡 >= 21)。

## <a name="resource-based-authorization"></a>以資源為基礎的授權。
*以資源為基礎的授權*發生在授權取決於將受作業影響的特定資源的情況下。 在 Tailspin Surveys 應用程式中，每個問卷都有一個擁有者，以及零至多個參與者。

* 擁有者可以讀取、更新、刪除、發佈及取消發佈問卷。
* 擁有者可將參與者指派至問卷。
* 參與者可以讀取及更新問卷。

請留意，「擁有者」和「參與者」不是應用程式角色；它們按每個問卷儲存在應用程式資料庫中。 例如，若要查看使用者是否可以刪除問卷，應用程式會檢查該使用者是否為問卷的擁有者。

在 ASP.NET Core 中，透過 **AuthorizationHandler** 衍生並覆寫 **Handle** 方法來實作以資源為基礎的授權。

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
    protected override void HandleRequirementAsync(AuthorizationHandlerContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

請注意，此類別是 Survey 物件的強型別。  在啟動時註冊 DI 的類別：

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

若要執行授權檢查，請使用 **IAuthorizationService** 介面，您可以將它插入您的控制器。 下列程式碼會檢查使用者是否可以讀取問卷：

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return StatusCode(403);
}
```

因為我們傳入 `Survey` 物件，此呼叫將會叫用 `SurveyAuthorizationHandler`。

在您的授權程式碼中，理想的方法是彙總所有使用者以角色為基礎和以資源為基礎的權限，然後針對要執行的作業查看彙總集合。
以下是來自 Surveys 應用程式的範例。 該應用程式定義數個權限類型：

* Admin
* 參與者
* [建立者]
* 擁有者
* 讀取者

該應用程式也定義一組可能對問卷進行的作業：

* 建立
* 讀取
* 更新
* 刪除
* 發佈
* Unpublsh

下列程式碼會針對特定使用者和問卷建立權限清單。 請注意，此程式碼會同時察看使用者的應用程式角色，以及問卷中的 owner/contributor 欄位。

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, OperationAuthorizationRequirement requirement, Survey resource)
    {
        var permissions = new List<UserPermissionType>();
        int surveyTenantId = context.User.GetSurveyTenantIdValue();
        int userId = context.User.GetSurveyUserIdValue();
        string user = context.User.GetUserName();

        if (resource.TenantId == surveyTenantId)
        {
            // Admin can do anything, as long as the resource belongs to the admin's tenant.
            if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin))
            {
                context.Succeed(requirement);
                return Task.FromResult(0);
            }

            if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
            {
                permissions.Add(UserPermissionType.Creator);
            }
            else
            {
                permissions.Add(UserPermissionType.Reader);
            }

            if (resource.OwnerId == userId)
            {
                permissions.Add(UserPermissionType.Owner);
            }
        }
        if (resource.Contributors != null && resource.Contributors.Any(x => x.UserId == userId))
        {
            permissions.Add(UserPermissionType.Contributor);
        }

        if (ValidateUserPermissions[requirement](permissions))
        {
            context.Succeed(requirement);
        }
        return Task.FromResult(0);
    }
}
```

在多租用戶應用程式中，您必須確定該權限不會「洩漏」給另一個租用戶的資料。 在 Surveys 應用程式中，跨租用戶間允許參與者權限 &mdash; 您可以將來自另一個租用戶的人員指派為參與者。 其他權限類型會限制在屬於該使用者租用戶的資源。 若要強制這項需求，程式碼會在授與權限之前檢查租用戶識別碼。 (建立問卷時所指派的 `TenantId` 欄位。)

下一步是針對作業 (讀取、更新、刪除等) 檢查權限。 Surveys 應用程式使用函式的查閱資料表來實作此步驟：

```csharp
static readonly Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>> ValidateUserPermissions
    = new Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>>

    {
        { Operations.Create, x => x.Contains(UserPermissionType.Creator) },

        { Operations.Read, x => x.Contains(UserPermissionType.Creator) ||
                                x.Contains(UserPermissionType.Reader) ||
                                x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Update, x => x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Delete, x => x.Contains(UserPermissionType.Owner) },

        { Operations.Publish, x => x.Contains(UserPermissionType.Owner) },

        { Operations.UnPublish, x => x.Contains(UserPermissionType.Owner) }
    };
```

[**下一主題**][web-api]

<!-- Links -->
[Tailspin]: tailspin.md

[應用程式角色]: app-roles.md
[policies]: /aspnet/core/security/authorization/policies
[參考實作]: tailspin.md
[設定驗證中介軟體]: authenticate.md#configure-the-auth-middleware
[sample application]: https://github.com/mspnp/multitenant-saas-guidance
[web-api]: web-api.md

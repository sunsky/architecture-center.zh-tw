---
title: 多組織用戶共享應用程式中的授權
description: 如何在多組織用戶共享應用程式中執行授權
author: MikeWasson
ms.date: 07/21/2017
pnp.series.title: Manage Identity in Multitenant Applications
pnp.series.prev: app-roles
pnp.series.next: web-api
ms.openlocfilehash: bbf702fe6651625a1aeceff7e4e321dd08c38544
ms.sourcegitcommit: e7e0e0282fa93f0063da3b57128ade395a9c1ef9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52902488"
---
# <a name="role-based-and-resource-based-authorization"></a><span data-ttu-id="d2cf7-103">角色和資源型授權</span><span class="sxs-lookup"><span data-stu-id="d2cf7-103">Role-based and resource-based authorization</span></span>

<span data-ttu-id="d2cf7-104">[![GitHub](../_images/github.png) 程式碼範例][sample application]</span><span class="sxs-lookup"><span data-stu-id="d2cf7-104">[![GitHub](../_images/github.png) Sample code][sample application]</span></span>

<span data-ttu-id="d2cf7-105">我們的 [參考實作] 是 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-105">Our [reference implementation] is an ASP.NET Core application.</span></span> <span data-ttu-id="d2cf7-106">本文章中，我們將探討進行授權的兩個一般方法，使用 ASP.NET Core 提供的授權 API。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-106">In this article we'll look at two general approaches to authorization, using the authorization APIs provided in ASP.NET Core.</span></span>

* <span data-ttu-id="d2cf7-107">**Role-based authorization**。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-107">**Role-based authorization**.</span></span> <span data-ttu-id="d2cf7-108">根據指派給使用者的角色授權動作。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-108">Authorizing an action based on the roles assigned to a user.</span></span> <span data-ttu-id="d2cf7-109">例如，某些動作需要系統管理員角色。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-109">For example, some actions require an administrator role.</span></span>
* <span data-ttu-id="d2cf7-110">**以資源為基礎的授權**。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-110">**Resource-based authorization**.</span></span> <span data-ttu-id="d2cf7-111">根據特定的資源授權動作。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-111">Authorizing an action based on a particular resource.</span></span> <span data-ttu-id="d2cf7-112">例如，每個資源都有一個擁有者。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-112">For example, every resource has an owner.</span></span> <span data-ttu-id="d2cf7-113">擁有者可以刪除該資源；其他使用者則不行。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-113">The owner can delete the resource; other users cannot.</span></span>

<span data-ttu-id="d2cf7-114">一般的應用程式會採用兩種的混合。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-114">A typical app will employ a mix of both.</span></span> <span data-ttu-id="d2cf7-115">例如，若要刪除資源，使用者必須是資源擁有者「或」  系統管理員。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-115">For example, to delete a resource, the user must be the resource owner *or* an admin.</span></span>

## <a name="role-based-authorization"></a><span data-ttu-id="d2cf7-116">以角色為基礎的授權</span><span class="sxs-lookup"><span data-stu-id="d2cf7-116">Role-Based Authorization</span></span>
<span data-ttu-id="d2cf7-117">[Tailspin Surveys][Tailspin] 應用程式會定義下列角色：</span><span class="sxs-lookup"><span data-stu-id="d2cf7-117">The [Tailspin Surveys][Tailspin] application defines the following roles:</span></span>

* <span data-ttu-id="d2cf7-118">系統管理員。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-118">Administrator.</span></span> <span data-ttu-id="d2cf7-119">可在所有屬於該租用戶的問卷上執行所有 CRUD 作業。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-119">Can perform all CRUD operations on any survey that belongs to that tenant.</span></span>
* <span data-ttu-id="d2cf7-120">建立者。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-120">Creator.</span></span> <span data-ttu-id="d2cf7-121">可以建立新的問卷</span><span class="sxs-lookup"><span data-stu-id="d2cf7-121">Can create new surveys</span></span>
* <span data-ttu-id="d2cf7-122">讀取者。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-122">Reader.</span></span> <span data-ttu-id="d2cf7-123">可以讀取任何屬於該租用戶的問卷</span><span class="sxs-lookup"><span data-stu-id="d2cf7-123">Can read any surveys that belong to that tenant</span></span>

<span data-ttu-id="d2cf7-124">套用至應用程式「使用者」  的規則。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-124">Roles apply to *users* of the application.</span></span> <span data-ttu-id="d2cf7-125">在 Surveys 應用程式中，使用者可能是系統管理員、建立者或讀取者其中之一。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-125">In the Surveys application, a user is either an administrator, creator, or reader.</span></span>

<span data-ttu-id="d2cf7-126">如需如何定義及管理角色的討論，請參閱 [應用程式角色]。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-126">For a discussion of how to define and manage roles, see [Application roles].</span></span>

<span data-ttu-id="d2cf7-127">不論您如何管理角色，您的授權程式碼看起來都會很相似。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-127">Regardless of how you manage the roles, your authorization code will look similar.</span></span> <span data-ttu-id="d2cf7-128">ASP.NET Core 有一個稱為[授權原則][policies]的抽象概念。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-128">ASP.NET Core has an abstraction called [authorization policies][policies].</span></span> <span data-ttu-id="d2cf7-129">使用此功能，您會使用程式碼定義授權原則，並將那些原則套用至控制器動作。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-129">With this feature, you define authorization policies in code, and then apply those policies to controller actions.</span></span> <span data-ttu-id="d2cf7-130">該原則已從控制器分離出來。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-130">The policy is decoupled from the controller.</span></span>

### <a name="create-policies"></a><span data-ttu-id="d2cf7-131">建立原則</span><span class="sxs-lookup"><span data-stu-id="d2cf7-131">Create policies</span></span>
<span data-ttu-id="d2cf7-132">若要定義原則，請先建立實作 `IAuthorizationRequirement`的類別。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-132">To define a policy, first create a class that implements `IAuthorizationRequirement`.</span></span> <span data-ttu-id="d2cf7-133">從 `AuthorizationHandler`衍生是最簡單的方式。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-133">It's easiest to derive from `AuthorizationHandler`.</span></span> <span data-ttu-id="d2cf7-134">在 `Handle` 方法中，檢查相關的宣告。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-134">In the `Handle` method, examine the relevant claim(s).</span></span>

<span data-ttu-id="d2cf7-135">以下是來自 Tailspin Surveys 應用程式的範例：</span><span class="sxs-lookup"><span data-stu-id="d2cf7-135">Here is an example from the Tailspin Surveys application:</span></span>

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

<span data-ttu-id="d2cf7-136">這個類別會定義使用者建立新問卷的需求。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-136">This class defines the requirement for a user to create a new survey.</span></span> <span data-ttu-id="d2cf7-137">使用者必須是 SurveyAdmin 或 SurveyCreator 角色。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-137">The user must be in the SurveyAdmin or SurveyCreator role.</span></span>

<span data-ttu-id="d2cf7-138">在您的啟動類別中，定義包含一或多個需求的具名原則。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-138">In your startup class, define a named policy that includes one or more requirements.</span></span> <span data-ttu-id="d2cf7-139">如果有多個需求，使用者必須符合「每個」  需求才能獲得授權。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-139">If there are multiple requirements, the user must meet *every* requirement to be authorized.</span></span> <span data-ttu-id="d2cf7-140">下列程式碼定義兩個原則：</span><span class="sxs-lookup"><span data-stu-id="d2cf7-140">The following code defines two policies:</span></span>

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

<span data-ttu-id="d2cf7-141">此程式碼也會設定驗證配置，它會告訴 ASP.NET 如果授權失敗時應執行哪一個驗證中介軟體。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-141">This code also sets the authentication scheme, which tells ASP.NET which authentication middleware should run if authorization fails.</span></span> <span data-ttu-id="d2cf7-142">在此情況下，我們指定 Cookie 驗證中介軟體，因為 cookie 驗證中介軟體可將使用者重新導向至「禁止」頁面。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-142">In this case, we specify the cookie authentication middleware, because the cookie authentication middleware can redirect the user to a "Forbidden" page.</span></span> <span data-ttu-id="d2cf7-143">[禁止] 頁面的位置會是在 Cookie 中介軟體的 `AccessDeniedPath` 選項中設定，請參閱[設定驗證中介軟體]。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-143">The location of the Forbidden page is set in the `AccessDeniedPath` option for the cookie middleware; see [Configuring the authentication middleware].</span></span>

### <a name="authorize-controller-actions"></a><span data-ttu-id="d2cf7-144">授權控制器動作</span><span class="sxs-lookup"><span data-stu-id="d2cf7-144">Authorize controller actions</span></span>
<span data-ttu-id="d2cf7-145">最後，若要在 MVC 控制器中授權動作，請在 `Authorize` 屬性中設定原則：</span><span class="sxs-lookup"><span data-stu-id="d2cf7-145">Finally, to authorize an action in an MVC controller, set the policy in the `Authorize` attribute:</span></span>

```csharp
[Authorize(Policy = PolicyNames.RequireSurveyCreator)]
public IActionResult Create()
{
    var survey = new SurveyDTO();
    return View(survey);
}
```

<span data-ttu-id="d2cf7-146">在舊版的 ASP.NET 中，您會在該屬性上設定 **Roles** 屬性：</span><span class="sxs-lookup"><span data-stu-id="d2cf7-146">In earlier versions of ASP.NET, you would set the **Roles** property on the attribute:</span></span>

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]
```

<span data-ttu-id="d2cf7-147">ASP.NET Core 中仍支援這樣設定，但相較於授權原則它有一些缺點：</span><span class="sxs-lookup"><span data-stu-id="d2cf7-147">This is still supported in ASP.NET Core, but it has some drawbacks compared with authorization policies:</span></span>

* <span data-ttu-id="d2cf7-148">它會假設訂定的宣告類型。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-148">It assumes a particular claim type.</span></span> <span data-ttu-id="d2cf7-149">原則可以檢查任何宣告類型。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-149">Policies can check for any claim type.</span></span> <span data-ttu-id="d2cf7-150">角色只是宣告的一種類型。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-150">Roles are just a type of claim.</span></span>
* <span data-ttu-id="d2cf7-151">角色名稱是以硬式編碼寫在屬性中。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-151">The role name is hard-coded into the attribute.</span></span> <span data-ttu-id="d2cf7-152">若使用原則，授權原則會全部在同一個位置，讓更新或甚至從組態設定載入變得更容易。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-152">With policies, the authorization logic is all in one place, making it easier to update or even load from configuration settings.</span></span>
* <span data-ttu-id="d2cf7-153">原則可支援簡單的角色成員資格無法表示的更複雜授權決策 (例如，年齡 >= 21)。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-153">Policies enable more complex authorization decisions (e.g., age >= 21) that can't be expressed by simple role membership.</span></span>

## <a name="resource-based-authorization"></a><span data-ttu-id="d2cf7-154">以資源為基礎的授權。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-154">Resource based authorization</span></span>
<span data-ttu-id="d2cf7-155">*以資源為基礎的授權。* 。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-155">*Resource based authorization* occurs whenever the authorization depends on a specific resource that will be affected by an operation.</span></span> <span data-ttu-id="d2cf7-156">在 Tailspin Surveys 應用程式中，每個問卷都有一個擁有者，以及零至多個參與者。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-156">In the Tailspin Surveys application, every survey has an owner and zero-to-many contributors.</span></span>

* <span data-ttu-id="d2cf7-157">擁有者可以讀取、更新、刪除、發佈及取消發佈問卷。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-157">The owner can read, update, delete, publish, and unpublish the survey.</span></span>
* <span data-ttu-id="d2cf7-158">擁有者可將參與者指派至問卷。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-158">The owner can assign contributors to the survey.</span></span>
* <span data-ttu-id="d2cf7-159">參與者可以讀取及更新問卷。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-159">Contributors can read and update the survey.</span></span>

<span data-ttu-id="d2cf7-160">請留意，「擁有者」和「參與者」不是應用程式角色；它們按每個問卷儲存在應用程式資料庫中。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-160">Note that "owner" and "contributor" are not application roles; they are stored per survey, in the application database.</span></span> <span data-ttu-id="d2cf7-161">例如，若要查看使用者是否可以刪除問卷，應用程式會檢查該使用者是否為問卷的擁有者。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-161">To check whether a user can delete a survey, for example, the app checks whether the user is the owner for that survey.</span></span>

<span data-ttu-id="d2cf7-162">在 ASP.NET Core 中，透過 **AuthorizationHandler** 衍生並覆寫 **Handle** 方法來實作以資源為基礎的授權。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-162">In ASP.NET Core, implement resource-based authorization by deriving from **AuthorizationHandler** and overriding the **Handle** method.</span></span>

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
    protected override void HandleRequirementAsync(AuthorizationHandlerContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

<span data-ttu-id="d2cf7-163">請注意，此類別是 Survey 物件的強型別。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-163">Notice that this class is strongly typed for Survey objects.</span></span>  <span data-ttu-id="d2cf7-164">在啟動時註冊 DI 的類別：</span><span class="sxs-lookup"><span data-stu-id="d2cf7-164">Register the class for DI on startup:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

<span data-ttu-id="d2cf7-165">若要執行授權檢查，請使用 **IAuthorizationService** 介面，您可以將它插入您的控制器。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-165">To perform authorization checks, use the **IAuthorizationService** interface, which you can inject into your controllers.</span></span> <span data-ttu-id="d2cf7-166">下列程式碼會檢查使用者是否可以讀取問卷：</span><span class="sxs-lookup"><span data-stu-id="d2cf7-166">The following code checks whether a user can read a survey:</span></span>

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return StatusCode(403);
}
```

<span data-ttu-id="d2cf7-167">因為我們傳入 `Survey` 物件，此呼叫將會叫用 `SurveyAuthorizationHandler`。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-167">Because we pass in a `Survey` object, this call will invoke the `SurveyAuthorizationHandler`.</span></span>

<span data-ttu-id="d2cf7-168">在您的授權程式碼中，理想的方法是彙總所有使用者以角色為基礎和以資源為基礎的權限，然後針對要執行的作業查看彙總集合。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-168">In your authorization code, a good approach is to aggregate all of the user's role-based and resource-based permissions, then check the aggregate set against the desired operation.</span></span>
<span data-ttu-id="d2cf7-169">以下是來自 Surveys 應用程式的範例。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-169">Here is an example from the Surveys app.</span></span> <span data-ttu-id="d2cf7-170">該應用程式定義數個權限類型：</span><span class="sxs-lookup"><span data-stu-id="d2cf7-170">The application defines several permission types:</span></span>

* <span data-ttu-id="d2cf7-171">Admin</span><span class="sxs-lookup"><span data-stu-id="d2cf7-171">Admin</span></span>
* <span data-ttu-id="d2cf7-172">參與者</span><span class="sxs-lookup"><span data-stu-id="d2cf7-172">Contributor</span></span>
* <span data-ttu-id="d2cf7-173">[建立者]</span><span class="sxs-lookup"><span data-stu-id="d2cf7-173">Creator</span></span>
* <span data-ttu-id="d2cf7-174">擁有者</span><span class="sxs-lookup"><span data-stu-id="d2cf7-174">Owner</span></span>
* <span data-ttu-id="d2cf7-175">讀取者</span><span class="sxs-lookup"><span data-stu-id="d2cf7-175">Reader</span></span>

<span data-ttu-id="d2cf7-176">該應用程式也定義一組可能對問卷進行的作業：</span><span class="sxs-lookup"><span data-stu-id="d2cf7-176">The application also defines a set of possible operations on surveys:</span></span>

* <span data-ttu-id="d2cf7-177">建立</span><span class="sxs-lookup"><span data-stu-id="d2cf7-177">Create</span></span>
* <span data-ttu-id="d2cf7-178">讀取</span><span class="sxs-lookup"><span data-stu-id="d2cf7-178">Read</span></span>
* <span data-ttu-id="d2cf7-179">更新</span><span class="sxs-lookup"><span data-stu-id="d2cf7-179">Update</span></span>
* <span data-ttu-id="d2cf7-180">刪除</span><span class="sxs-lookup"><span data-stu-id="d2cf7-180">Delete</span></span>
* <span data-ttu-id="d2cf7-181">發佈</span><span class="sxs-lookup"><span data-stu-id="d2cf7-181">Publish</span></span>
* <span data-ttu-id="d2cf7-182">Unpublsh</span><span class="sxs-lookup"><span data-stu-id="d2cf7-182">Unpublsh</span></span>

<span data-ttu-id="d2cf7-183">下列程式碼會針對特定使用者和問卷建立權限清單。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-183">The following code creates a list of permissions for a particular user and survey.</span></span> <span data-ttu-id="d2cf7-184">請注意，此程式碼會同時察看使用者的應用程式角色，以及問卷中的 owner/contributor 欄位。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-184">Notice that this code looks at both the user's app roles, and the owner/contributor fields in the survey.</span></span>

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

<span data-ttu-id="d2cf7-185">在多租用戶應用程式中，您必須確定該權限不會「洩漏」給另一個租用戶的資料。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-185">In a multi-tenant application, you must ensure that permissions don't "leak" to another tenant's data.</span></span> <span data-ttu-id="d2cf7-186">在 Surveys 應用程式中，跨租用戶間允許參與者權限 &mdash; 您可以將來自另一個租用戶的人員指派為參與者。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-186">In the Surveys app, the Contributor permission is allowed across tenants &mdash; you can assign someone from another tenant as a contributor.</span></span> <span data-ttu-id="d2cf7-187">其他權限類型會限制在屬於該使用者租用戶的資源。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-187">The other permission types are restricted to resources that belong to that user's tenant.</span></span> <span data-ttu-id="d2cf7-188">若要強制這項需求，程式碼會在授與權限之前檢查租用戶識別碼。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-188">To enforce this requirement, the code checks the tenant ID before granting the permission.</span></span> <span data-ttu-id="d2cf7-189">(建立問卷時所指派的 `TenantId` 欄位。)</span><span class="sxs-lookup"><span data-stu-id="d2cf7-189">(The `TenantId` field as assigned when the survey is created.)</span></span>

<span data-ttu-id="d2cf7-190">下一步是針對作業 (讀取、更新、刪除等) 檢查權限。</span><span class="sxs-lookup"><span data-stu-id="d2cf7-190">The next step is to check the operation (read, update, delete, etc) against the permissions.</span></span> <span data-ttu-id="d2cf7-191">Surveys 應用程式使用函式的查閱資料表來實作此步驟：</span><span class="sxs-lookup"><span data-stu-id="d2cf7-191">The Surveys app implements this step by using a lookup table of functions:</span></span>

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

<span data-ttu-id="d2cf7-192">[**下一主題**][web-api]</span><span class="sxs-lookup"><span data-stu-id="d2cf7-192">[**Next**][web-api]</span></span>

<!-- Links -->
[Tailspin]: tailspin.md

[應用程式角色]: app-roles.md
[Application roles]: app-roles.md
[policies]: /aspnet/core/security/authorization/policies
[參考實作]: tailspin.md
[reference implementation]: tailspin.md
[設定驗證中介軟體]: authenticate.md#configure-the-auth-middleware
[Configuring the authentication middleware]: authenticate.md#configure-the-auth-middleware
[sample application]: https://github.com/mspnp/multitenant-saas-guidance
[web-api]: web-api.md

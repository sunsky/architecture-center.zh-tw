---
title: 有關 Tailspin Surveys 應用程式
description: Tailspin Surveys 應用程式概觀
author: MikeWasson
ms.date: 07/21/2017
pnp.series.title: Manage Identity in Multitenant Applications
pnp.series.prev: index
pnp.series.next: authenticate
ms.openlocfilehash: a1c357bd1b5306d1255c66aaea96d86be55e7b77
ms.sourcegitcommit: e7e0e0282fa93f0063da3b57128ade395a9c1ef9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52902063"
---
# <a name="the-tailspin-scenario"></a><span data-ttu-id="508ad-103">Tailspin 情節</span><span class="sxs-lookup"><span data-stu-id="508ad-103">The Tailspin scenario</span></span>

<span data-ttu-id="508ad-104">[![GitHub](../_images/github.png) 程式碼範例][sample application]</span><span class="sxs-lookup"><span data-stu-id="508ad-104">[![GitHub](../_images/github.png) Sample code][sample application]</span></span>

<span data-ttu-id="508ad-105">Tailspin 是虛構的公司，他們正在開發名為 Surveys 的 SaaS 應用程式。</span><span class="sxs-lookup"><span data-stu-id="508ad-105">Tailspin is a fictitious company that is developing a SaaS application named Surveys.</span></span> <span data-ttu-id="508ad-106">此應用程式可讓組織建立並發佈線上問卷。</span><span class="sxs-lookup"><span data-stu-id="508ad-106">This application enables organizations to create and publish online surveys.</span></span>

* <span data-ttu-id="508ad-107">組織可以註冊該應用程式。</span><span class="sxs-lookup"><span data-stu-id="508ad-107">An organization can sign up for the application.</span></span>
* <span data-ttu-id="508ad-108">組織註冊之後，使用者可以使用他們組織的認證登入應用程式。</span><span class="sxs-lookup"><span data-stu-id="508ad-108">After the organization is signed up, users can sign into the application with their organizational credentials.</span></span>
* <span data-ttu-id="508ad-109">使用者可以建立、編輯和發佈問卷。</span><span class="sxs-lookup"><span data-stu-id="508ad-109">Users can create, edit, and publish surveys.</span></span>

> [!NOTE]
> <span data-ttu-id="508ad-110">若要開始使用應用程式，請參閱[執行 Surveys 應用程式]。</span><span class="sxs-lookup"><span data-stu-id="508ad-110">To get started with the application, see [Run the Surveys application].</span></span>
> 
> 

## <a name="users-can-create-edit-and-view-surveys"></a><span data-ttu-id="508ad-111">使用者可以建立、編輯和檢視問卷</span><span class="sxs-lookup"><span data-stu-id="508ad-111">Users can create, edit, and view surveys</span></span>
<span data-ttu-id="508ad-112">驗證的使用者可以檢視他或她所建立或具有參與者權限的所有調查，以及建立新的調查。</span><span class="sxs-lookup"><span data-stu-id="508ad-112">An authenticated user can view all the surveys that he or she has created or has contributor rights to, and create new surveys.</span></span> <span data-ttu-id="508ad-113">請注意，該使用者是使用他的組織身分識別 `bob@contoso.com`登入。</span><span class="sxs-lookup"><span data-stu-id="508ad-113">Notice that the user is signed in with his organizational identity, `bob@contoso.com`.</span></span>

![Surveys 應用程式](./images/surveys-screenshot.png)

<span data-ttu-id="508ad-115">此螢幕擷取畫面顯示 [Edit Survey] (編輯問卷) 頁面：</span><span class="sxs-lookup"><span data-stu-id="508ad-115">This screenshot shows the Edit Survey page:</span></span>

![編輯問卷](./images/edit-survey.png)

<span data-ttu-id="508ad-117">使用者也可以檢視相同租用戶中的其他使用者所建立的問卷。</span><span class="sxs-lookup"><span data-stu-id="508ad-117">Users can also view any surveys created by other users within the same tenant.</span></span>

![租用戶問卷](./images/tenant-surveys.png)

## <a name="survey-owners-can-invite-contributors"></a><span data-ttu-id="508ad-119">調查擁有者可以邀請參與者</span><span class="sxs-lookup"><span data-stu-id="508ad-119">Survey owners can invite contributors</span></span>
<span data-ttu-id="508ad-120">當使用者建立問卷時，他或她可以邀請其他人參與該問卷。</span><span class="sxs-lookup"><span data-stu-id="508ad-120">When a user creates a survey, he or she can invite other people to be contributors on the survey.</span></span> <span data-ttu-id="508ad-121">參與者可以編輯問卷，但不能刪除或發佈問卷。</span><span class="sxs-lookup"><span data-stu-id="508ad-121">Contributors can edit the survey, but cannot delete or publish it.</span></span>  

![加入參與者](./images/add-contributor.png)

<span data-ttu-id="508ad-123">使用者可以從其他租用戶加入參與者，因此能夠跨租用戶共用資源。</span><span class="sxs-lookup"><span data-stu-id="508ad-123">A user can add contributors from other tenants, which enables cross-tenant sharing of resources.</span></span> <span data-ttu-id="508ad-124">在此螢幕擷取畫面中，Bob (`bob@contoso.com`) 正在將 Alice (`alice@fabrikam.com`) 新增為 Bob 所建立的問卷調查的參與者。</span><span class="sxs-lookup"><span data-stu-id="508ad-124">In this screenshot, Bob (`bob@contoso.com`) is adding Alice (`alice@fabrikam.com`) as a contributor to a survey that Bob created.</span></span>

<span data-ttu-id="508ad-125">當 Alice 登入時，她會看見該問卷列在 [Surveys I can contribute to] (我可以參與的問卷) 之下。</span><span class="sxs-lookup"><span data-stu-id="508ad-125">When Alice logs in, she sees the survey listed under "Surveys I can contribute to".</span></span>

![問卷參與者](./images/contributor.png)

<span data-ttu-id="508ad-127">請留意，Alice 是用自己的租用戶登入，而不是以 Contoso 租用戶之來賓的身分登入。</span><span class="sxs-lookup"><span data-stu-id="508ad-127">Note that Alice signs into her own tenant, not as a guest of the Contoso tenant.</span></span> <span data-ttu-id="508ad-128">Alice 只針對該調查擁有參與者權限 &mdash; 她無法檢視來自 Contoso 租用戶的其他問卷。</span><span class="sxs-lookup"><span data-stu-id="508ad-128">Alice has contributor permissions only for that survey &mdash; she cannot view other surveys from the Contoso tenant.</span></span>

## <a name="architecture"></a><span data-ttu-id="508ad-129">架構</span><span class="sxs-lookup"><span data-stu-id="508ad-129">Architecture</span></span>
<span data-ttu-id="508ad-130">Surveys 應用程式由 Web 前端和 Web API 後端組成。</span><span class="sxs-lookup"><span data-stu-id="508ad-130">The Surveys application consists of a web front end and a web API backend.</span></span> <span data-ttu-id="508ad-131">兩者都使用 [ASP.NET Core] 實作。</span><span class="sxs-lookup"><span data-stu-id="508ad-131">Both are implemented using [ASP.NET Core].</span></span>

<span data-ttu-id="508ad-132">該 Web 應用程式使用 Azure Active Directory (Azure AD) 來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="508ad-132">The web application uses Azure Active Directory (Azure AD) to authenticate users.</span></span> <span data-ttu-id="508ad-133">該 Web 應用程式也會呼叫 Azure AD 來取得 Web API 的 OAuth 2 存取權杖。</span><span class="sxs-lookup"><span data-stu-id="508ad-133">The web application also calls Azure AD to get OAuth 2 access tokens for the Web API.</span></span> <span data-ttu-id="508ad-134">取存取權杖快取在 Azure Redis Cache 中。</span><span class="sxs-lookup"><span data-stu-id="508ad-134">Access tokens are cached in Azure Redis Cache.</span></span> <span data-ttu-id="508ad-135">快取可讓多個執行個體共用相同的權杖快取 (例如，在伺服器陣列中)。</span><span class="sxs-lookup"><span data-stu-id="508ad-135">The cache enables multiple instances to share the same token cache (e.g., in a server farm).</span></span>

![架構](./images/architecture.png)

<span data-ttu-id="508ad-137">[**下一主題**][authentication]</span><span class="sxs-lookup"><span data-stu-id="508ad-137">[**Next**][authentication]</span></span>

<!-- Links -->

[authentication]: authenticate.md

[執行 Surveys 應用程式]: ./run-the-app.md
[Run the Surveys application]: ./run-the-app.md
[ASP.NET Core]: /aspnet/core
[sample application]: https://github.com/mspnp/multitenant-saas-guidance

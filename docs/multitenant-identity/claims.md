---
title: "在多租用戶應用程式中使用宣告式身分識別"
description: "如何使用宣告進行簽發者驗證及授權"
author: MikeWasson
ms:date: 07/21/2017
pnp.series.title: Manage Identity in Multitenant Applications
pnp.series.prev: authenticate
pnp.series.next: signup
ms.openlocfilehash: 61788d9759715b21ef1bdda59c5b54d923fd8f62
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="work-with-claims-based-identities"></a><span data-ttu-id="548ed-103">使用宣告式身分識別</span><span class="sxs-lookup"><span data-stu-id="548ed-103">Work with claims-based identities</span></span>

<span data-ttu-id="548ed-104">[![GitHub](../_images/github.png) 範例程式碼][sample application]</span><span class="sxs-lookup"><span data-stu-id="548ed-104">[![GitHub](../_images/github.png) Sample code][sample application]</span></span>

## <a name="claims-in-azure-ad"></a><span data-ttu-id="548ed-105">在 Azure AD 中的宣告</span><span class="sxs-lookup"><span data-stu-id="548ed-105">Claims in Azure AD</span></span>
<span data-ttu-id="548ed-106">當使用者登入時，Azure AD 會傳送識別碼權杖，其中包含一組使用者的相關宣告。</span><span class="sxs-lookup"><span data-stu-id="548ed-106">When a user signs in, Azure AD sends an ID token that contains a set of claims about the user.</span></span> <span data-ttu-id="548ed-107">宣告就是以索引鍵/值組表示的資訊片段。</span><span class="sxs-lookup"><span data-stu-id="548ed-107">A claim is simply a piece of information, expressed as a key/value pair.</span></span> <span data-ttu-id="548ed-108">例如：`email`=`bob@contoso.com`。</span><span class="sxs-lookup"><span data-stu-id="548ed-108">For example, `email`=`bob@contoso.com`.</span></span>  <span data-ttu-id="548ed-109">宣告具有簽發者 &mdash; 在此情況下為 Azure AD &mdash; 它是驗證使用者並建立宣告的實體。</span><span class="sxs-lookup"><span data-stu-id="548ed-109">Claims have an issuer &mdash; in this case, Azure AD &mdash; which is the entity that authenticates the user and creates the claims.</span></span> <span data-ttu-id="548ed-110">因為您信任簽發者，您會信任此宣告。</span><span class="sxs-lookup"><span data-stu-id="548ed-110">You trust the claims because you trust the issuer.</span></span> <span data-ttu-id="548ed-111">(相反地，如果您不信任簽發者，請不要信任該宣告！)</span><span class="sxs-lookup"><span data-stu-id="548ed-111">(Conversely, if you don't trust the issuer, don't trust the claims!)</span></span>

<span data-ttu-id="548ed-112">概括而言：</span><span class="sxs-lookup"><span data-stu-id="548ed-112">At a high level:</span></span>

1. <span data-ttu-id="548ed-113">使用者驗證。</span><span class="sxs-lookup"><span data-stu-id="548ed-113">The user authenticates.</span></span>
2. <span data-ttu-id="548ed-114">IDP 傳送一組宣告。</span><span class="sxs-lookup"><span data-stu-id="548ed-114">The IDP sends a set of claims.</span></span>
3. <span data-ttu-id="548ed-115">應用程式標準化或加強宣告 (選擇性)。</span><span class="sxs-lookup"><span data-stu-id="548ed-115">The app normalizes or augments the claims (optional).</span></span>
4. <span data-ttu-id="548ed-116">應用程式使用宣告來做出授權決策。</span><span class="sxs-lookup"><span data-stu-id="548ed-116">The app uses the claims to make authorization decisions.</span></span>

<span data-ttu-id="548ed-117">在 OpenID Connect 中，您取得的一組宣告是由驗證要求的 [範圍參數] 所控制。</span><span class="sxs-lookup"><span data-stu-id="548ed-117">In OpenID Connect, the set of claims that you get is controlled by the [scope parameter] of the authentication request.</span></span> <span data-ttu-id="548ed-118">不過，Azure AD 會透過 OpenID Connect 發出一組有限的宣告；請參閱 [支援的權杖和宣告類型]。</span><span class="sxs-lookup"><span data-stu-id="548ed-118">However, Azure AD issues a limited set of claims through OpenID Connect; see [Supported Token and Claim Types].</span></span> <span data-ttu-id="548ed-119">如果您想要使用者的詳細資訊，您必須使用 Azure AD Graph API。</span><span class="sxs-lookup"><span data-stu-id="548ed-119">If you want more information about the user, you'll need to use the Azure AD Graph API.</span></span>

<span data-ttu-id="548ed-120">以下是應用程式通常可能會關注的一些 AAD 宣告：</span><span class="sxs-lookup"><span data-stu-id="548ed-120">Here are some of the claims from AAD that an app might typically care about:</span></span>

| <span data-ttu-id="548ed-121">識別碼權杖中的宣告類型</span><span class="sxs-lookup"><span data-stu-id="548ed-121">Claim type in ID token</span></span> | <span data-ttu-id="548ed-122">說明</span><span class="sxs-lookup"><span data-stu-id="548ed-122">Description</span></span> |
| --- | --- |
| <span data-ttu-id="548ed-123">aud</span><span class="sxs-lookup"><span data-stu-id="548ed-123">aud</span></span> |<span data-ttu-id="548ed-124">權杖發行的對象。</span><span class="sxs-lookup"><span data-stu-id="548ed-124">Who the token was issued for.</span></span> <span data-ttu-id="548ed-125">這會是應用程式的用戶端識別碼。</span><span class="sxs-lookup"><span data-stu-id="548ed-125">This will be the application's client ID.</span></span> <span data-ttu-id="548ed-126">一般而言，您應該不需要擔心這個宣告，因為中介軟體會自動驗證。</span><span class="sxs-lookup"><span data-stu-id="548ed-126">Generally, you shouldn't need to worry about this claim, because the middleware automatically validates it.</span></span> <span data-ttu-id="548ed-127">範例：`"91464657-d17a-4327-91f3-2ed99386406f"`</span><span class="sxs-lookup"><span data-stu-id="548ed-127">Example:  `"91464657-d17a-4327-91f3-2ed99386406f"`</span></span> |
| <span data-ttu-id="548ed-128">groups</span><span class="sxs-lookup"><span data-stu-id="548ed-128">groups</span></span> |<span data-ttu-id="548ed-129">AAD 群組清單，使用者為其中的成員。</span><span class="sxs-lookup"><span data-stu-id="548ed-129">A list of AAD groups of which the user is a member.</span></span> <span data-ttu-id="548ed-130">範例： `["93e8f556-8661-4955-87b6-890bc043c30f", "fc781505-18ef-4a31-a7d5-7d931d7b857e"]`</span><span class="sxs-lookup"><span data-stu-id="548ed-130">Example: `["93e8f556-8661-4955-87b6-890bc043c30f", "fc781505-18ef-4a31-a7d5-7d931d7b857e"]`</span></span> |
| <span data-ttu-id="548ed-131">iss</span><span class="sxs-lookup"><span data-stu-id="548ed-131">iss</span></span> |<span data-ttu-id="548ed-132">OIDC 權杖的 [簽發者] 。</span><span class="sxs-lookup"><span data-stu-id="548ed-132">The [issuer] of the OIDC token.</span></span> <span data-ttu-id="548ed-133">範例： `https://sts.windows.net/b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4/`</span><span class="sxs-lookup"><span data-stu-id="548ed-133">Example: `https://sts.windows.net/b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4/`</span></span> |
| <span data-ttu-id="548ed-134">名稱</span><span class="sxs-lookup"><span data-stu-id="548ed-134">name</span></span> |<span data-ttu-id="548ed-135">使用者的顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="548ed-135">The user's display name.</span></span> <span data-ttu-id="548ed-136">範例： `"Alice A."`</span><span class="sxs-lookup"><span data-stu-id="548ed-136">Example: `"Alice A."`</span></span> |
| <span data-ttu-id="548ed-137">oid</span><span class="sxs-lookup"><span data-stu-id="548ed-137">oid</span></span> |<span data-ttu-id="548ed-138">AAD 中使用者的物件識別碼。</span><span class="sxs-lookup"><span data-stu-id="548ed-138">The object identifier for the user in AAD.</span></span> <span data-ttu-id="548ed-139">這個值是使用者的識別碼，不可變且無法重複使用。</span><span class="sxs-lookup"><span data-stu-id="548ed-139">This value is the immutable and non-reusable identifier of the user.</span></span> <span data-ttu-id="548ed-140">使用這個值，而非電子郵件，來做為唯一的使用者識別碼；電子郵件地址可以變更。</span><span class="sxs-lookup"><span data-stu-id="548ed-140">Use this value, not email, as a unique identifier for users; email addresses can change.</span></span> <span data-ttu-id="548ed-141">如果您在應用程式中使用 Azure AD Graph API，物件識別碼是用於查詢設定檔資訊的值。</span><span class="sxs-lookup"><span data-stu-id="548ed-141">If you use the Azure AD Graph API in your app, object ID is that value used to query profile information.</span></span> <span data-ttu-id="548ed-142">範例： `"59f9d2dc-995a-4ddf-915e-b3bb314a7fa4"`</span><span class="sxs-lookup"><span data-stu-id="548ed-142">Example: `"59f9d2dc-995a-4ddf-915e-b3bb314a7fa4"`</span></span> |
| <span data-ttu-id="548ed-143">角色</span><span class="sxs-lookup"><span data-stu-id="548ed-143">roles</span></span> |<span data-ttu-id="548ed-144">使用者的應用程式角色清單。</span><span class="sxs-lookup"><span data-stu-id="548ed-144">A list of app roles for the user.</span></span>    <span data-ttu-id="548ed-145">範例： `["SurveyCreator"]`</span><span class="sxs-lookup"><span data-stu-id="548ed-145">Example: `["SurveyCreator"]`</span></span> |
| <span data-ttu-id="548ed-146">tid</span><span class="sxs-lookup"><span data-stu-id="548ed-146">tid</span></span> |<span data-ttu-id="548ed-147">租用戶識別碼。</span><span class="sxs-lookup"><span data-stu-id="548ed-147">Tenant ID.</span></span> <span data-ttu-id="548ed-148">這個值是 Azure AD 中租用戶的唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="548ed-148">This value is a unique identifier for the tenant in Azure AD.</span></span> <span data-ttu-id="548ed-149">範例： `"b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4"`</span><span class="sxs-lookup"><span data-stu-id="548ed-149">Example: `"b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4"`</span></span> |
| <span data-ttu-id="548ed-150">unique_name</span><span class="sxs-lookup"><span data-stu-id="548ed-150">unique_name</span></span> |<span data-ttu-id="548ed-151">人類可讀的使用者顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="548ed-151">A human readable display name of the user.</span></span> <span data-ttu-id="548ed-152">範例： `"alice@contoso.com"`</span><span class="sxs-lookup"><span data-stu-id="548ed-152">Example: `"alice@contoso.com"`</span></span> |
| <span data-ttu-id="548ed-153">upn</span><span class="sxs-lookup"><span data-stu-id="548ed-153">upn</span></span> |<span data-ttu-id="548ed-154">使用者主體名稱。</span><span class="sxs-lookup"><span data-stu-id="548ed-154">User principal name.</span></span> <span data-ttu-id="548ed-155">範例： `"alice@contoso.com"`</span><span class="sxs-lookup"><span data-stu-id="548ed-155">Example: `"alice@contoso.com"`</span></span> |

<span data-ttu-id="548ed-156">下表列出識別碼權杖中出現的宣告類型。</span><span class="sxs-lookup"><span data-stu-id="548ed-156">This table lists the claim types as they appear in the ID token.</span></span> <span data-ttu-id="548ed-157">在 ASP.NET Core 中，OpenID Connect 中介軟體會在填入使用者主體的宣告集合時，轉換部分宣告類型：</span><span class="sxs-lookup"><span data-stu-id="548ed-157">In ASP.NET Core, the OpenID Connect middleware converts some of the claim types when it populates the Claims collection for the user principal:</span></span>

* <span data-ttu-id="548ed-158">oid > `http://schemas.microsoft.com/identity/claims/objectidentifier`</span><span class="sxs-lookup"><span data-stu-id="548ed-158">oid > `http://schemas.microsoft.com/identity/claims/objectidentifier`</span></span>
* <span data-ttu-id="548ed-159">tid > `http://schemas.microsoft.com/identity/claims/tenantid`</span><span class="sxs-lookup"><span data-stu-id="548ed-159">tid > `http://schemas.microsoft.com/identity/claims/tenantid`</span></span>
* <span data-ttu-id="548ed-160">unique_name > `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`</span><span class="sxs-lookup"><span data-stu-id="548ed-160">unique_name > `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`</span></span>
* <span data-ttu-id="548ed-161">upn > `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`</span><span class="sxs-lookup"><span data-stu-id="548ed-161">upn > `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`</span></span>

## <a name="claims-transformations"></a><span data-ttu-id="548ed-162">宣告轉換</span><span class="sxs-lookup"><span data-stu-id="548ed-162">Claims transformations</span></span>
<span data-ttu-id="548ed-163">在驗證流程期間，您可能想要修改您從 IDP 取得的宣告。</span><span class="sxs-lookup"><span data-stu-id="548ed-163">During the authentication flow, you might want to modify the claims that you get from the IDP.</span></span> <span data-ttu-id="548ed-164">在 ASP.NET Core 中，您可以從 OpenID Connect 的中介軟體的 **AuthenticationValidated** 事件內執行宣告轉換。</span><span class="sxs-lookup"><span data-stu-id="548ed-164">In ASP.NET Core, you can perform claims transformation inside of the **AuthenticationValidated** event from the OpenID Connect middleware.</span></span> <span data-ttu-id="548ed-165">(請參閱[驗證事件]。)</span><span class="sxs-lookup"><span data-stu-id="548ed-165">(See [Authentication events].)</span></span>

<span data-ttu-id="548ed-166">您在 **AuthenticationValidated** 之中新增的任何宣告，會儲存在工作階段驗證 Cookie 中。</span><span class="sxs-lookup"><span data-stu-id="548ed-166">Any claims that you add during **AuthenticationValidated** are stored in the session authentication cookie.</span></span> <span data-ttu-id="548ed-167">它們不會推送回 Azure AD。</span><span class="sxs-lookup"><span data-stu-id="548ed-167">They don't get pushed back to Azure AD.</span></span>

<span data-ttu-id="548ed-168">以下是一些宣告轉換的範例：</span><span class="sxs-lookup"><span data-stu-id="548ed-168">Here are some examples of claims transformation:</span></span>

* <span data-ttu-id="548ed-169">**宣告標準化**，或讓宣告在使用者之間一致。</span><span class="sxs-lookup"><span data-stu-id="548ed-169">**Claims normalization**, or making claims consistent across users.</span></span> <span data-ttu-id="548ed-170">如果您正從多個 IDP 取得宣告，這特別有關，其中可能會針對類似的資訊使用不同的宣告類型。</span><span class="sxs-lookup"><span data-stu-id="548ed-170">This is particularly relevant if you are getting claims from multiple IDPs, which might use different claim types for similar information.</span></span>
  <span data-ttu-id="548ed-171">例如，Azure AD 會傳送「upn」宣告，其中包含使用者的電子郵件。</span><span class="sxs-lookup"><span data-stu-id="548ed-171">For example, Azure AD sends a "upn" claim that contains the user's email.</span></span> <span data-ttu-id="548ed-172">其他 IDP 可能會傳送「email」宣告。</span><span class="sxs-lookup"><span data-stu-id="548ed-172">Other IDPs might send an "email" claim.</span></span> <span data-ttu-id="548ed-173">下列程式碼會將「upn」宣告轉換為「email」宣告：</span><span class="sxs-lookup"><span data-stu-id="548ed-173">The following code converts the "upn" claim into an "email" claim:</span></span>
  
  ```csharp
  var email = principal.FindFirst(ClaimTypes.Upn)?.Value;
  if (!string.IsNullOrWhiteSpace(email))
  {
      identity.AddClaim(new Claim(ClaimTypes.Email, email));
  }
  ```
* <span data-ttu-id="548ed-174">針對不存在的宣告新增**預設宣告值** &mdash; 例如，將使用者指派到預設角色。</span><span class="sxs-lookup"><span data-stu-id="548ed-174">Add **default claim values** for claims that aren't present &mdash; for example, assigning a user to a default role.</span></span> <span data-ttu-id="548ed-175">在某些情況下，這可以簡化授權邏輯。</span><span class="sxs-lookup"><span data-stu-id="548ed-175">In some cases this can simplify authorization logic.</span></span>
* <span data-ttu-id="548ed-176">使用關於使用者的應用程式特定資訊，新增 **自訂宣告類型** 。</span><span class="sxs-lookup"><span data-stu-id="548ed-176">Add **custom claim types** with application-specific information about the user.</span></span> <span data-ttu-id="548ed-177">例如，您可能會在資料庫中儲存關於使用者的某些資訊。</span><span class="sxs-lookup"><span data-stu-id="548ed-177">For example, you might store some information about the user in a database.</span></span> <span data-ttu-id="548ed-178">您可以搭配這項資訊，將自訂宣告新增至驗證票證。</span><span class="sxs-lookup"><span data-stu-id="548ed-178">You could add a custom claim with this information to the authentication ticket.</span></span> <span data-ttu-id="548ed-179">宣告會儲存在 Cookie 中，所以您只需在每個登入工作階段，從資料庫中取得一次。</span><span class="sxs-lookup"><span data-stu-id="548ed-179">The claim is stored in a cookie, so you only need to get it from the database once per login session.</span></span> <span data-ttu-id="548ed-180">另一方面，您也會想要避免建立過大的 Cookie，所以您必須考量 Cookie 大小與資料庫查閱之間的取捨。</span><span class="sxs-lookup"><span data-stu-id="548ed-180">On the other hand, you also want to avoid creating excessively large cookies, so you need to consider the trade-off between cookie size versus database lookups.</span></span>   

<span data-ttu-id="548ed-181">驗證流程完成之後，宣告即可在 `HttpContext.User`中提供。</span><span class="sxs-lookup"><span data-stu-id="548ed-181">After the authentication flow is complete, the claims are available in `HttpContext.User`.</span></span> <span data-ttu-id="548ed-182">此時，您應該將它們視為唯讀的集合 &mdash; 例如，使用它們來做出授權決策。</span><span class="sxs-lookup"><span data-stu-id="548ed-182">At that point, you should treat them as a read-only collection &mdash; e.g., use them to make authorization decisions.</span></span>

## <a name="issuer-validation"></a><span data-ttu-id="548ed-183">簽發者驗證</span><span class="sxs-lookup"><span data-stu-id="548ed-183">Issuer validation</span></span>
<span data-ttu-id="548ed-184">在 OpenID Connect 中，簽發者宣告 ("iss") 會識別發行識別碼權杖的 IDP。</span><span class="sxs-lookup"><span data-stu-id="548ed-184">In OpenID Connect, the issuer claim ("iss") identifies the IDP that issued the ID token.</span></span> <span data-ttu-id="548ed-185">OIDC 驗證流程的其中一部份，是驗證簽發者宣告是否符合實際的簽發者。</span><span class="sxs-lookup"><span data-stu-id="548ed-185">Part of the OIDC authentication flow is to verify that the issuer claim matches the actual issuer.</span></span> <span data-ttu-id="548ed-186">OIDC 中介軟體會為您處理這部分。</span><span class="sxs-lookup"><span data-stu-id="548ed-186">The OIDC middleware handles this for you.</span></span>

<span data-ttu-id="548ed-187">在 Azure AD 中，每個 AD 租用戶的簽發者值是唯一的 (`https://sts.windows.net/<tenantID>`)。</span><span class="sxs-lookup"><span data-stu-id="548ed-187">In Azure AD, the issuer value is unique per AD tenant (`https://sts.windows.net/<tenantID>`).</span></span> <span data-ttu-id="548ed-188">因此，應用程式應該執行額外的檢查，確保簽發者代表的租用戶允許登入應用程式。</span><span class="sxs-lookup"><span data-stu-id="548ed-188">Therefore, an application should do an additional check, to make sure the issuer represents a tenant that is allowed to sign in to the app.</span></span>

<span data-ttu-id="548ed-189">針對單一租用戶應用程式，您可以只檢查簽發者是否為您擁有的租用戶。</span><span class="sxs-lookup"><span data-stu-id="548ed-189">For a single-tenant application, you can just check that the issuer is your own tenant.</span></span> <span data-ttu-id="548ed-190">實際上，OIDC 中介軟體預設會自動執行此動作。</span><span class="sxs-lookup"><span data-stu-id="548ed-190">In fact, the OIDC middleware does this automatically by default.</span></span> <span data-ttu-id="548ed-191">在多租用戶應用程式中，您需要允許多個簽發者，對應到不同的租用戶。</span><span class="sxs-lookup"><span data-stu-id="548ed-191">In a multi-tenant app, you need to allow for multiple issuers, corresponding to the different tenants.</span></span> <span data-ttu-id="548ed-192">以下是常用的方法：</span><span class="sxs-lookup"><span data-stu-id="548ed-192">Here is a general approach to use:</span></span>

* <span data-ttu-id="548ed-193">在 OIDC 中介軟體選項中，將 **ValidateIssuer** 設為 false。</span><span class="sxs-lookup"><span data-stu-id="548ed-193">In the OIDC middleware options, set **ValidateIssuer** to false.</span></span> <span data-ttu-id="548ed-194">這會關閉自動檢查。</span><span class="sxs-lookup"><span data-stu-id="548ed-194">This turns off the automatic check.</span></span>
* <span data-ttu-id="548ed-195">當租用戶註冊時，會將租用戶及簽發者儲存到您的使用者 DB 中。</span><span class="sxs-lookup"><span data-stu-id="548ed-195">When a tenant signs up, store the tenant and the issuer in your user DB.</span></span>
* <span data-ttu-id="548ed-196">每當使用者登入時，便會查閱資料庫中的簽發者。</span><span class="sxs-lookup"><span data-stu-id="548ed-196">Whenever a user signs in, look up the issuer in the database.</span></span> <span data-ttu-id="548ed-197">如果找不到簽發者，則表示該租用戶尚未註冊。</span><span class="sxs-lookup"><span data-stu-id="548ed-197">If the issuer isn't found, it means that tenant hasn't signed up.</span></span> <span data-ttu-id="548ed-198">您可以將他們重新導向至註冊頁面。</span><span class="sxs-lookup"><span data-stu-id="548ed-198">You can redirect them to a sign up page.</span></span>
* <span data-ttu-id="548ed-199">您也可以將特定租用戶設為黑名單；例如，沒有為他們的訂用帳戶付費的客戶。</span><span class="sxs-lookup"><span data-stu-id="548ed-199">You could also blacklist certain tenants; for example, for customers that didn't pay their subscription.</span></span>

<span data-ttu-id="548ed-200">如需更詳細的討論，請參閱[多租用戶應用程式中的租用戶註冊和登入][signup]。</span><span class="sxs-lookup"><span data-stu-id="548ed-200">For a more detailed discussion, see [Sign-up and tenant onboarding in a multitenant application][signup].</span></span>

## <a name="using-claims-for-authorization"></a><span data-ttu-id="548ed-201">使用宣告進行授權</span><span class="sxs-lookup"><span data-stu-id="548ed-201">Using claims for authorization</span></span>
<span data-ttu-id="548ed-202">利用宣告，使用者的身分識別不再是整合型實體。</span><span class="sxs-lookup"><span data-stu-id="548ed-202">With claims, a user's identity is no longer a monolithic entity.</span></span> <span data-ttu-id="548ed-203">例如，使用者可能有電子郵件地址、電話號碼、生日、性別等等。可能使用者的 IDP 會儲存所有這類資訊。</span><span class="sxs-lookup"><span data-stu-id="548ed-203">For example, a user might have an email address, phone number, birthday, gender, etc. Maybe the user's IDP stores all of this information.</span></span> <span data-ttu-id="548ed-204">但當您驗證使用者時，您通常會取得這些資訊的子集做為宣告。</span><span class="sxs-lookup"><span data-stu-id="548ed-204">But when you authenticate the user, you'll typically get a subset of these as claims.</span></span> <span data-ttu-id="548ed-205">在此模型中，使用者的身分識別只是宣告的組合。</span><span class="sxs-lookup"><span data-stu-id="548ed-205">In this model, the user's identity is simply a bundle of claims.</span></span> <span data-ttu-id="548ed-206">當您進行有關使用者的授權決策時，您會尋找特定的宣告集。</span><span class="sxs-lookup"><span data-stu-id="548ed-206">When you make authorization decisions about a user, you will look for particular sets of claims.</span></span> <span data-ttu-id="548ed-207">換句話說，問題「使用者 X 可以執行動作 Y 嗎」最後會變成「使用者 X 是否有宣告 Z」。</span><span class="sxs-lookup"><span data-stu-id="548ed-207">In other words, the question "Can user X perform action Y" ultimately becomes "Does user X have claim Z".</span></span>

<span data-ttu-id="548ed-208">以下是一些檢查宣告的基本模式。</span><span class="sxs-lookup"><span data-stu-id="548ed-208">Here are some basic patterns for checking claims.</span></span>

* <span data-ttu-id="548ed-209">若要檢查使用者是否有含有特定值的特定宣告：</span><span class="sxs-lookup"><span data-stu-id="548ed-209">To check that the user has a particular claim with a particular value:</span></span>
  
   ```csharp
   if (User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
   ```
   <span data-ttu-id="548ed-210">此程式碼會檢查使用者是否有含有「Admin」的角色宣告。</span><span class="sxs-lookup"><span data-stu-id="548ed-210">This code checks whether the user has a Role claim with the value "Admin".</span></span> <span data-ttu-id="548ed-211">它會正確地處理使用者沒有角色宣告，或有多個角色宣告的情況。</span><span class="sxs-lookup"><span data-stu-id="548ed-211">It correctly handles the case where the user has no Role claim or multiple Role claims.</span></span>
  
   <span data-ttu-id="548ed-212">**ClaimTypes** 類別定義常用宣告類型的常數。</span><span class="sxs-lookup"><span data-stu-id="548ed-212">The **ClaimTypes** class defines constants for commonly-used claim types.</span></span> <span data-ttu-id="548ed-213">不過，您可以使用任何字串值做為宣告類型。</span><span class="sxs-lookup"><span data-stu-id="548ed-213">However, you can use any string value for the claim type.</span></span>
* <span data-ttu-id="548ed-214">當您預期最多只有一個值時，若要取得宣告類型的單一值：</span><span class="sxs-lookup"><span data-stu-id="548ed-214">To get a single value for a claim type, when you expect there to be at most one value:</span></span>
  
  ```csharp
  string email = User.FindFirst(ClaimTypes.Email)?.Value;
  ```
* <span data-ttu-id="548ed-215">取得宣告類型的所有值：</span><span class="sxs-lookup"><span data-stu-id="548ed-215">To get all the values for a claim type:</span></span>
  
  ```csharp
  IEnumerable<Claim> groups = User.FindAll("groups");
  ```

<span data-ttu-id="548ed-216">如需詳細資訊，請參閱[多租用戶應用程式中以角色和資源為基礎的授權][authorization]。</span><span class="sxs-lookup"><span data-stu-id="548ed-216">For more information, see [Role-based and resource-based authorization in multitenant applications][authorization].</span></span>

<span data-ttu-id="548ed-217">[**下一步**][signup]</span><span class="sxs-lookup"><span data-stu-id="548ed-217">[**Next**][signup]</span></span>


<!-- Links -->

[範圍參數]: http://nat.sakimura.org/2012/01/26/scopes-and-claims-in-openid-connect/
[支援的權杖和宣告類型]: /azure/active-directory/active-directory-token-and-claims/
[簽發者]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
[驗證事件]: authenticate.md#authentication-events
[signup]: signup.md
[Claims-Based Authorization]: /aspnet/core/security/authorization/claims
[sample application]: https://github.com/mspnp/multitenant-saas-guidance
[authorization]: authorize.md

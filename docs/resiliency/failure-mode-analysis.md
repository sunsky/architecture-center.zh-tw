---
title: 失敗模式分析
description: 針對以 Azure 為基礎的雲端解決方案執行失敗模式分析的指導方針。
author: MikeWasson
ms.date: 03/24/2017
ms.custom: resiliency
pnp.series.title: Design for Resiliency
ms.openlocfilehash: 6598644828dffb68f01c2d0a2ce9fbdda932168a
ms.sourcegitcommit: 94d50043db63416c4d00cebe927a0c88f78c3219
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2018
ms.locfileid: "47429533"
---
# <a name="failure-mode-analysis"></a><span data-ttu-id="ed145-103">失敗模式分析</span><span class="sxs-lookup"><span data-stu-id="ed145-103">Failure mode analysis</span></span>
[!INCLUDE [header](../_includes/header.md)]

<span data-ttu-id="ed145-104">失敗模式分析 (FMA) 是一項程序，可藉由識別系統中可能的失敗點，在系統中建置復原功能。</span><span class="sxs-lookup"><span data-stu-id="ed145-104">Failure mode analysis (FMA) is a process for building resiliency into a system, by identifying possible failure points in the system.</span></span> <span data-ttu-id="ed145-105">FMA 應該加入成為架構和設計階段的一部分，這樣您才能在系統中從頭建置失敗復原。</span><span class="sxs-lookup"><span data-stu-id="ed145-105">The FMA should be part of the architecture and design phases, so that you can build failure recovery into the system from the beginning.</span></span>

<span data-ttu-id="ed145-106">以下是用來進行 FMA 的一般程序：</span><span class="sxs-lookup"><span data-stu-id="ed145-106">Here is the general process to conduct an FMA:</span></span>

1. <span data-ttu-id="ed145-107">識別系統中的所有元件。</span><span class="sxs-lookup"><span data-stu-id="ed145-107">Identify all of the components in the system.</span></span> <span data-ttu-id="ed145-108">包含外部相依性，例如，作為身分識別提供者、第三方服務等等。</span><span class="sxs-lookup"><span data-stu-id="ed145-108">Include external dependencies, such as as identity providers, third-party services, and so on.</span></span>   
2. <span data-ttu-id="ed145-109">識別每個元件可能發生的潛在失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-109">For each component, identify potential failures that could occur.</span></span> <span data-ttu-id="ed145-110">單一元件可能會有多個失敗模式。</span><span class="sxs-lookup"><span data-stu-id="ed145-110">A single component may have more than one failure mode.</span></span> <span data-ttu-id="ed145-111">例如，您應該分開考慮讀取失敗和寫入失敗，因為其影響和可能的補救措施會不同。</span><span class="sxs-lookup"><span data-stu-id="ed145-111">For example, you should consider read failures and write failures separately, because the impact and possible mitigations will be different.</span></span>
3. <span data-ttu-id="ed145-112">根據每個失敗模式的整體風險來為這些模式訂定評等。</span><span class="sxs-lookup"><span data-stu-id="ed145-112">Rate each failure mode according to its overall risk.</span></span> <span data-ttu-id="ed145-113">請考慮下列因素：</span><span class="sxs-lookup"><span data-stu-id="ed145-113">Consider these factors:</span></span>  

   * <span data-ttu-id="ed145-114">失敗的可能性有多高。</span><span class="sxs-lookup"><span data-stu-id="ed145-114">What is the likelihood of the failure.</span></span> <span data-ttu-id="ed145-115">相對常見嗎？</span><span class="sxs-lookup"><span data-stu-id="ed145-115">Is it relatively common?</span></span> <span data-ttu-id="ed145-116">極為罕見嗎？</span><span class="sxs-lookup"><span data-stu-id="ed145-116">Extrememly rare?</span></span> <span data-ttu-id="ed145-117">您不需要確切的數字；我們的目的是為了協助排列優先順序。</span><span class="sxs-lookup"><span data-stu-id="ed145-117">You don't need exact numbers; the purpose is to help rank the priority.</span></span>
   * <span data-ttu-id="ed145-118">對應用程式的影響為何 (就可用性、資料遺失、貨幣成本和業務中斷等方面來說)？</span><span class="sxs-lookup"><span data-stu-id="ed145-118">What is the impact on the application, in terms of availability, data loss, monetary cost, and business disruption?</span></span>
4. <span data-ttu-id="ed145-119">為每個失敗模式決定應用程式的回應和復原方式。</span><span class="sxs-lookup"><span data-stu-id="ed145-119">For each failure mode, determine how the application will respond and recover.</span></span> <span data-ttu-id="ed145-120">考慮成本和應用程式複雜性的取捨。</span><span class="sxs-lookup"><span data-stu-id="ed145-120">Consider tradeoffs in cost and application complexity.</span></span>   

<span data-ttu-id="ed145-121">作為 FMA 程序的起點，本文包含可能之失敗模式及其補救措施的目錄。</span><span class="sxs-lookup"><span data-stu-id="ed145-121">As a starting point for your FMA process, this article contains a catalog of potential failure modes and their mitigations.</span></span> <span data-ttu-id="ed145-122">此目錄會依技術或 Azure 服務，再加上應用程式層級設計的一般類別來分類。</span><span class="sxs-lookup"><span data-stu-id="ed145-122">The catalog is organized by technology or Azure service, plus a general category for application-level design.</span></span> <span data-ttu-id="ed145-123">此目錄並不完整，但會涵蓋許多核心的 Azure 服務。</span><span class="sxs-lookup"><span data-stu-id="ed145-123">The catalog is not exhaustive, but covers many of the core Azure services.</span></span>

## <a name="app-service"></a><span data-ttu-id="ed145-124">App Service 方案</span><span class="sxs-lookup"><span data-stu-id="ed145-124">App Service</span></span>
### <a name="app-service-app-shuts-down"></a><span data-ttu-id="ed145-125">App Service 應用程式關閉。</span><span class="sxs-lookup"><span data-stu-id="ed145-125">App Service app shuts down.</span></span>
<span data-ttu-id="ed145-126">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-126">**Detection**.</span></span> <span data-ttu-id="ed145-127">可能的原因：</span><span class="sxs-lookup"><span data-stu-id="ed145-127">Possible causes:</span></span>

* <span data-ttu-id="ed145-128">預期性關閉</span><span class="sxs-lookup"><span data-stu-id="ed145-128">Expected shutdown</span></span>

  * <span data-ttu-id="ed145-129">操作員關閉應用程式；例如，使用 Azure 入口網站。</span><span class="sxs-lookup"><span data-stu-id="ed145-129">An operator shuts down the application; for example, using the Azure portal.</span></span>
  * <span data-ttu-id="ed145-130">應用程式因為閒置而卸載。</span><span class="sxs-lookup"><span data-stu-id="ed145-130">The app was unloaded because it was idle.</span></span> <span data-ttu-id="ed145-131">(當 `Always On` 設定停用時才會發生)。</span><span class="sxs-lookup"><span data-stu-id="ed145-131">(Only if the `Always On` setting is disabled.)</span></span>
* <span data-ttu-id="ed145-132">非預期性關閉</span><span class="sxs-lookup"><span data-stu-id="ed145-132">Unexpected shutdown</span></span>

  * <span data-ttu-id="ed145-133">應用程式當機。</span><span class="sxs-lookup"><span data-stu-id="ed145-133">The app crashes.</span></span>
  * <span data-ttu-id="ed145-134">App Service VM 執行個體變得無法使用。</span><span class="sxs-lookup"><span data-stu-id="ed145-134">An App Service VM instance becomes unavailable.</span></span>

<span data-ttu-id="ed145-135">Application_End 記錄會捕捉到應用程式網域關閉 (軟程序當機)，而且也只有此記錄會捕捉到應用程式網域關閉。</span><span class="sxs-lookup"><span data-stu-id="ed145-135">Application_End logging will catch the app domain shutdown (soft process crash) and is the only way to catch the application domain shutdowns.</span></span>

<span data-ttu-id="ed145-136">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-136">**Recovery**</span></span>

* <span data-ttu-id="ed145-137">如果是預期性關閉，請使用應用程式的關閉事件來正常關閉。</span><span class="sxs-lookup"><span data-stu-id="ed145-137">If the shutdown was expected, use the application's shutdown event to shut down gracefully.</span></span> <span data-ttu-id="ed145-138">例如，在 ASP.NET 中使用 `Application_End` 方法。</span><span class="sxs-lookup"><span data-stu-id="ed145-138">For example, in ASP.NET, use the `Application_End` method.</span></span>
* <span data-ttu-id="ed145-139">如果應用程式已於閒置時卸載，它會在下一個要求到來時自動重新啟動。</span><span class="sxs-lookup"><span data-stu-id="ed145-139">If the application was unloaded while idle, it is automatically restarted on the next request.</span></span> <span data-ttu-id="ed145-140">不過，您會產生「冷啟動」成本。</span><span class="sxs-lookup"><span data-stu-id="ed145-140">However, you will incur the "cold start" cost.</span></span>
* <span data-ttu-id="ed145-141">若要防止應用程式在閒置時卸載，請在 Web 應用程式中啟用 `Always On` 設定。</span><span class="sxs-lookup"><span data-stu-id="ed145-141">To prevent the application from being unloaded while idle, enable the `Always On` setting in the web app.</span></span> <span data-ttu-id="ed145-142">請參閱[在 Azure App Service 中設定 Web 應用程式][app-service-configure]。</span><span class="sxs-lookup"><span data-stu-id="ed145-142">See [Configure web apps in Azure App Service][app-service-configure].</span></span>
* <span data-ttu-id="ed145-143">若要防止操作員關閉應用程式，請以 `ReadOnly` 層級設定資源鎖定。</span><span class="sxs-lookup"><span data-stu-id="ed145-143">To prevent an operator from shutting down the app, set a resource lock with `ReadOnly` level.</span></span> <span data-ttu-id="ed145-144">請參閱[使用 Azure Resource Manager 鎖定資源][rm-locks]。</span><span class="sxs-lookup"><span data-stu-id="ed145-144">See [Lock resources with Azure Resource Manager][rm-locks].</span></span>
* <span data-ttu-id="ed145-145">如果應用程式當機或 App Service VM 變得無法使用，App Service 會自動重新啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="ed145-145">If the app crashes or an App Service VM becomes unavailable, App Service automatically restarts the app.</span></span>

<span data-ttu-id="ed145-146">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-146">**Diagnostics**.</span></span> <span data-ttu-id="ed145-147">應用程式記錄和 Web 伺服器記錄。</span><span class="sxs-lookup"><span data-stu-id="ed145-147">Application logs and web server logs.</span></span> <span data-ttu-id="ed145-148">請參閱[在 Azure App Service 中針對 Web 應用程式啟用診斷記錄功能][app-service-logging]。</span><span class="sxs-lookup"><span data-stu-id="ed145-148">See [Enable diagnostics logging for web apps in Azure App Service][app-service-logging].</span></span>

### <a name="a-particular-user-repeatedly-makes-bad-requests-or-overloads-the-system"></a><span data-ttu-id="ed145-149">特定使用者重複提出不正確的要求，或讓多載系統。</span><span class="sxs-lookup"><span data-stu-id="ed145-149">A particular user repeatedly makes bad requests or overloads the system.</span></span>
<span data-ttu-id="ed145-150">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-150">**Detection**.</span></span> <span data-ttu-id="ed145-151">驗證使用者和在應用程式記錄中納入使用者識別碼。</span><span class="sxs-lookup"><span data-stu-id="ed145-151">Authenticate users and include user ID in application logs.</span></span>

<span data-ttu-id="ed145-152">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-152">**Recovery**</span></span>

* <span data-ttu-id="ed145-153">使用 [Azure API 管理][api-management] 對該使用者的要求進行節流處理。</span><span class="sxs-lookup"><span data-stu-id="ed145-153">Use [Azure API Management][api-management] to throttle requests from the user.</span></span> <span data-ttu-id="ed145-154">請參閱[以 Azure API 管理進行進階要求節流][api-management-throttling]</span><span class="sxs-lookup"><span data-stu-id="ed145-154">See [Advanced request throttling with Azure API Management][api-management-throttling]</span></span>
* <span data-ttu-id="ed145-155">封鎖該使用者。</span><span class="sxs-lookup"><span data-stu-id="ed145-155">Block the user.</span></span>

<span data-ttu-id="ed145-156">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-156">**Diagnostics**.</span></span> <span data-ttu-id="ed145-157">記錄所有驗證要求。</span><span class="sxs-lookup"><span data-stu-id="ed145-157">Log all authentication requests.</span></span>

### <a name="a-bad-update-was-deployed"></a><span data-ttu-id="ed145-158">所部署的更新不正確。</span><span class="sxs-lookup"><span data-stu-id="ed145-158">A bad update was deployed.</span></span>
<span data-ttu-id="ed145-159">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-159">**Detection**.</span></span> <span data-ttu-id="ed145-160">透過 Azure 入口網站監視應用程式健康情況 (請參閱[監視 Azure Web 應用程式效能][app-insights-web-apps]) 或實作[健康情況端點監視模式][health-endpoint-monitoring-pattern]。</span><span class="sxs-lookup"><span data-stu-id="ed145-160">Monitor the application health through the Azure Portal (see [Monitor Azure web app performance][app-insights-web-apps]) or implement the [health endpoint monitoring pattern][health-endpoint-monitoring-pattern].</span></span>

<span data-ttu-id="ed145-161">**復原**。</span><span class="sxs-lookup"><span data-stu-id="ed145-161">**Recovery**.</span></span> <span data-ttu-id="ed145-162">使用多個[部署位置][app-service-slots]並復原為已知正確的最後一個部署。</span><span class="sxs-lookup"><span data-stu-id="ed145-162">Use multiple [deployment slots][app-service-slots] and roll back to the last-known-good deployment.</span></span> <span data-ttu-id="ed145-163">如需詳細資訊，請參閱[基本 Web 應用程式][ra-web-apps-basic]。</span><span class="sxs-lookup"><span data-stu-id="ed145-163">For more information, see [Basic web application][ra-web-apps-basic].</span></span>

## <a name="azure-active-directory"></a><span data-ttu-id="ed145-164">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="ed145-164">Azure Active Directory</span></span>
### <a name="openid-connect-oidc-authentication-fails"></a><span data-ttu-id="ed145-165">OpenID Connect (OIDC) 驗證失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-165">OpenID Connect (OIDC) authentication fails.</span></span>
<span data-ttu-id="ed145-166">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-166">**Detection**.</span></span> <span data-ttu-id="ed145-167">可能的失敗模式包括：</span><span class="sxs-lookup"><span data-stu-id="ed145-167">Possible failure modes include:</span></span>

1. <span data-ttu-id="ed145-168">Azure AD 無法使用，或由於網路問題而無法連線。</span><span class="sxs-lookup"><span data-stu-id="ed145-168">Azure AD is not available, or cannot be reached due to a network problem.</span></span> <span data-ttu-id="ed145-169">重新導向至驗證端點時失敗，而且 OIDC 中介軟體擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ed145-169">Redirection to the authentication endpoint fails, and the OIDC middleware throws an exception.</span></span>
2. <span data-ttu-id="ed145-170">Azure AD 租用戶不存在。</span><span class="sxs-lookup"><span data-stu-id="ed145-170">Azure AD tenant does not exist.</span></span> <span data-ttu-id="ed145-171">重新導向至驗證端點時傳回 HTTP 錯誤碼，而且 OIDC 中介軟體擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ed145-171">Redirection to the authentication endpoint returns an HTTP error code, and the OIDC middleware throws an exception.</span></span>
3. <span data-ttu-id="ed145-172">使用者無法通過驗證。</span><span class="sxs-lookup"><span data-stu-id="ed145-172">User cannot authenticate.</span></span> <span data-ttu-id="ed145-173">不需要偵測策略；Azure AD 會處理登入失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-173">No detection strategy is necessary; Azure AD handles login failures.</span></span>

<span data-ttu-id="ed145-174">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-174">**Recovery**</span></span>

1. <span data-ttu-id="ed145-175">從中介軟體捕捉未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ed145-175">Catch unhandled exceptions from the middleware.</span></span>
2. <span data-ttu-id="ed145-176">處理 `AuthenticationFailed` 事件。</span><span class="sxs-lookup"><span data-stu-id="ed145-176">Handle `AuthenticationFailed` events.</span></span>
3. <span data-ttu-id="ed145-177">將使用者重新導向到錯誤頁面。</span><span class="sxs-lookup"><span data-stu-id="ed145-177">Redirect the user to an error page.</span></span>
4. <span data-ttu-id="ed145-178">使用者進行重試。</span><span class="sxs-lookup"><span data-stu-id="ed145-178">User retries.</span></span>

## <a name="azure-search"></a><span data-ttu-id="ed145-179">Azure 搜尋服務</span><span class="sxs-lookup"><span data-stu-id="ed145-179">Azure Search</span></span>
### <a name="writing-data-to-azure-search-fails"></a><span data-ttu-id="ed145-180">將資料寫入 Azure 搜尋服務時失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-180">Writing data to Azure Search fails.</span></span>
<span data-ttu-id="ed145-181">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-181">**Detection**.</span></span> <span data-ttu-id="ed145-182">捕捉 `Microsoft.Rest.Azure.CloudException` 錯誤。</span><span class="sxs-lookup"><span data-stu-id="ed145-182">Catch `Microsoft.Rest.Azure.CloudException` errors.</span></span>

<span data-ttu-id="ed145-183">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-183">**Recovery**</span></span>

<span data-ttu-id="ed145-184">[搜尋服務 .NET SDK][search-sdk] 會在暫時性失敗後自動重試。</span><span class="sxs-lookup"><span data-stu-id="ed145-184">The [Search .NET SDK][search-sdk] automatically retries after transient failures.</span></span> <span data-ttu-id="ed145-185">您應該將用戶端 SDK 擲回的任何例外狀況都視為非暫時性錯誤。</span><span class="sxs-lookup"><span data-stu-id="ed145-185">Any exceptions thrown by the client SDK should be treated as non-transient errors.</span></span>

<span data-ttu-id="ed145-186">預設的重試原則會使用指數輪詢。</span><span class="sxs-lookup"><span data-stu-id="ed145-186">The default retry policy uses exponential back-off.</span></span> <span data-ttu-id="ed145-187">若要使用不同的重試原則，請在 `SearchIndexClient` 或 `SearchServiceClient` 類別上呼叫 `SetRetryPolicy`。</span><span class="sxs-lookup"><span data-stu-id="ed145-187">To use a different retry policy, call `SetRetryPolicy` on the `SearchIndexClient` or `SearchServiceClient` class.</span></span> <span data-ttu-id="ed145-188">如需詳細資訊，請參閱[自動重試][auto-rest-client-retry]。</span><span class="sxs-lookup"><span data-stu-id="ed145-188">For more information, see [Automatic Retries][auto-rest-client-retry].</span></span>

<span data-ttu-id="ed145-189">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-189">**Diagnostics**.</span></span> <span data-ttu-id="ed145-190">使用[搜尋服務流量分析][search-analytics]。</span><span class="sxs-lookup"><span data-stu-id="ed145-190">Use [Search Traffic Analytics][search-analytics].</span></span>

### <a name="reading-data-from-azure-search-fails"></a><span data-ttu-id="ed145-191">從 Azure 搜尋服務讀取資料時失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-191">Reading data from Azure Search fails.</span></span>
<span data-ttu-id="ed145-192">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-192">**Detection**.</span></span> <span data-ttu-id="ed145-193">捕捉 `Microsoft.Rest.Azure.CloudException` 錯誤。</span><span class="sxs-lookup"><span data-stu-id="ed145-193">Catch `Microsoft.Rest.Azure.CloudException` errors.</span></span>

<span data-ttu-id="ed145-194">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-194">**Recovery**</span></span>

<span data-ttu-id="ed145-195">[搜尋服務 .NET SDK][search-sdk] 會在暫時性失敗後自動重試。</span><span class="sxs-lookup"><span data-stu-id="ed145-195">The [Search .NET SDK][search-sdk]  automatically retries after transient failures.</span></span> <span data-ttu-id="ed145-196">您應該將用戶端 SDK 擲回的任何例外狀況都視為非暫時性錯誤。</span><span class="sxs-lookup"><span data-stu-id="ed145-196">Any exceptions thrown by the client SDK should be treated as non-transient errors.</span></span>

<span data-ttu-id="ed145-197">預設的重試原則會使用指數輪詢。</span><span class="sxs-lookup"><span data-stu-id="ed145-197">The default retry policy uses exponential back-off.</span></span> <span data-ttu-id="ed145-198">若要使用不同的重試原則，請在 `SearchIndexClient` 或 `SearchServiceClient` 類別上呼叫 `SetRetryPolicy`。</span><span class="sxs-lookup"><span data-stu-id="ed145-198">To use a different retry policy, call `SetRetryPolicy` on the `SearchIndexClient` or `SearchServiceClient` class.</span></span> <span data-ttu-id="ed145-199">如需詳細資訊，請參閱[自動重試][auto-rest-client-retry]。</span><span class="sxs-lookup"><span data-stu-id="ed145-199">For more information, see [Automatic Retries][auto-rest-client-retry].</span></span>

<span data-ttu-id="ed145-200">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-200">**Diagnostics**.</span></span> <span data-ttu-id="ed145-201">使用[搜尋服務流量分析][search-analytics]。</span><span class="sxs-lookup"><span data-stu-id="ed145-201">Use [Search Traffic Analytics][search-analytics].</span></span>

## <a name="cassandra"></a><span data-ttu-id="ed145-202">Cassandra</span><span class="sxs-lookup"><span data-stu-id="ed145-202">Cassandra</span></span>
### <a name="reading-or-writing-to-a-node-fails"></a><span data-ttu-id="ed145-203">讀取或寫入至節點時失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-203">Reading or writing to a node fails.</span></span>
<span data-ttu-id="ed145-204">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-204">**Detection**.</span></span> <span data-ttu-id="ed145-205">捕捉例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ed145-205">Catch the exception.</span></span> <span data-ttu-id="ed145-206">若為 .NET 用戶端，這通常會是 `System.Web.HttpException`。</span><span class="sxs-lookup"><span data-stu-id="ed145-206">For .NET clients, this will typically be `System.Web.HttpException`.</span></span> <span data-ttu-id="ed145-207">其他用戶端可能有其他的例外狀況類型。</span><span class="sxs-lookup"><span data-stu-id="ed145-207">Other client may have other exception types.</span></span>  <span data-ttu-id="ed145-208">如需詳細資訊，請參閱[正確處理 Cassandra 錯誤](https://www.datastax.com/dev/blog/cassandra-error-handling-done-right)。</span><span class="sxs-lookup"><span data-stu-id="ed145-208">For more information, see [Cassandra error handling done right](https://www.datastax.com/dev/blog/cassandra-error-handling-done-right).</span></span>

<span data-ttu-id="ed145-209">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-209">**Recovery**</span></span>

* <span data-ttu-id="ed145-210">每個 [Cassandra 用戶端](https://wiki.apache.org/cassandra/ClientOptions)都有自己的重試原則和功能。</span><span class="sxs-lookup"><span data-stu-id="ed145-210">Each [Cassandra client](https://wiki.apache.org/cassandra/ClientOptions) has its own retry policies and capabilities.</span></span> <span data-ttu-id="ed145-211">如需詳細資訊，請參閱[正確處理 Cassandra 錯誤][cassandra-error-handling]。</span><span class="sxs-lookup"><span data-stu-id="ed145-211">For more information, see [Cassandra error handling done right][cassandra-error-handling].</span></span>
* <span data-ttu-id="ed145-212">使用機架感知部署，並將資料節點分散於所有容錯網域。</span><span class="sxs-lookup"><span data-stu-id="ed145-212">Use a rack-aware deployment, with data nodes distributed across the fault domains.</span></span>
* <span data-ttu-id="ed145-213">利用本機仲裁一致性部署至多個區域。</span><span class="sxs-lookup"><span data-stu-id="ed145-213">Deploy to multiple regions with local quorum consistency.</span></span> <span data-ttu-id="ed145-214">如果發生非暫時性失敗，則容錯移轉至其他區域。</span><span class="sxs-lookup"><span data-stu-id="ed145-214">If a non-transient failure occurs, fail over to another region.</span></span>

<span data-ttu-id="ed145-215">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-215">**Diagnostics**.</span></span> <span data-ttu-id="ed145-216">應用程式記錄檔</span><span class="sxs-lookup"><span data-stu-id="ed145-216">Application logs</span></span>

## <a name="cloud-service"></a><span data-ttu-id="ed145-217">服務雲端</span><span class="sxs-lookup"><span data-stu-id="ed145-217">Cloud Service</span></span>
### <a name="web-or-worker-roles-are-unexpectedlybeing-shut-down"></a><span data-ttu-id="ed145-218">Web 角色或背景工作角色遭到意外關閉。</span><span class="sxs-lookup"><span data-stu-id="ed145-218">Web or worker roles are unexpectedly being shut down.</span></span>
<span data-ttu-id="ed145-219">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-219">**Detection**.</span></span> <span data-ttu-id="ed145-220">系統引發了 [RoleEnvironment.Stopping][RoleEnvironment.Stopping] 事件。</span><span class="sxs-lookup"><span data-stu-id="ed145-220">The [RoleEnvironment.Stopping][RoleEnvironment.Stopping] event is fired.</span></span>

<span data-ttu-id="ed145-221"><strong>復原</strong>。</span><span class="sxs-lookup"><span data-stu-id="ed145-221"><strong>Recovery</strong>.</span></span> <span data-ttu-id="ed145-222">覆寫 [RoleEntryPoint.OnStop][RoleEntryPoint.OnStop] 方法以便正常地清除。</span><span class="sxs-lookup"><span data-stu-id="ed145-222">Override the [RoleEntryPoint.OnStop][RoleEntryPoint.OnStop] method to gracefully clean up.</span></span> <span data-ttu-id="ed145-223">如需詳細資訊，請參閱 [Azure OnStop 事件的正確處理方式][onstop-events] (部落格)。</span><span class="sxs-lookup"><span data-stu-id="ed145-223">For more information, see [The Right Way to Handle Azure OnStop Events][onstop-events] (blog).</span></span>

## <a name="cosmos-db"></a><span data-ttu-id="ed145-224">Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="ed145-224">Cosmos DB</span></span> 
### <a name="reading-data-fails"></a><span data-ttu-id="ed145-225">讀取資料時失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-225">Reading data fails.</span></span>
<span data-ttu-id="ed145-226">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-226">**Detection**.</span></span> <span data-ttu-id="ed145-227">捕捉 `System.Net.Http.HttpRequestException` 或 `Microsoft.Azure.Documents.DocumentClientException`。</span><span class="sxs-lookup"><span data-stu-id="ed145-227">Catch `System.Net.Http.HttpRequestException` or `Microsoft.Azure.Documents.DocumentClientException`.</span></span>

<span data-ttu-id="ed145-228">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-228">**Recovery**</span></span>

* <span data-ttu-id="ed145-229">SDK 會自動重試失敗的嘗試。</span><span class="sxs-lookup"><span data-stu-id="ed145-229">The SDK automatically retries failed attempts.</span></span> <span data-ttu-id="ed145-230">若要設定重試次數和等待時間上限，請設定 `ConnectionPolicy.RetryOptions`。</span><span class="sxs-lookup"><span data-stu-id="ed145-230">To set the number of retries and the maximum wait time, configure `ConnectionPolicy.RetryOptions`.</span></span> <span data-ttu-id="ed145-231">用戶端引發的例外狀況可能超出重試原則，或不是暫時性的錯誤。</span><span class="sxs-lookup"><span data-stu-id="ed145-231">Exceptions that the client raises are either beyond the retry policy or are not transient errors.</span></span>
* <span data-ttu-id="ed145-232">如果 Cosmos DB 將用戶端節流，它會傳回 HTTP 429 錯誤。</span><span class="sxs-lookup"><span data-stu-id="ed145-232">If Cosmos DB throttles the client, it returns an HTTP 429 error.</span></span> <span data-ttu-id="ed145-233">檢查 `DocumentClientException` 中的狀態碼。</span><span class="sxs-lookup"><span data-stu-id="ed145-233">Check the status code in the `DocumentClientException`.</span></span> <span data-ttu-id="ed145-234">如果您一直收到 429 錯誤，請考慮增加集合的輸送量值。</span><span class="sxs-lookup"><span data-stu-id="ed145-234">If you are getting error 429 consistently, consider increasing the throughput value of the collection.</span></span>
    * <span data-ttu-id="ed145-235">如果您使用 MongoDB API，節流時服務會傳回錯誤碼 16500。</span><span class="sxs-lookup"><span data-stu-id="ed145-235">If you are using the MongoDB API, the service returns error code 16500 when throttling.</span></span>
* <span data-ttu-id="ed145-236">跨兩個以上的區域複寫 Cosmos DB 資料庫。</span><span class="sxs-lookup"><span data-stu-id="ed145-236">Replicate the Cosmos DB database across two or more regions.</span></span> <span data-ttu-id="ed145-237">所有複本都要可供讀取。</span><span class="sxs-lookup"><span data-stu-id="ed145-237">All replicas are readable.</span></span> <span data-ttu-id="ed145-238">使用用戶端 SDK 指定 `PreferredLocations` 參數。</span><span class="sxs-lookup"><span data-stu-id="ed145-238">Using the client SDKs, specify the `PreferredLocations` parameter.</span></span> <span data-ttu-id="ed145-239">這是 Azure 區域的排序清單。</span><span class="sxs-lookup"><span data-stu-id="ed145-239">This is an ordered list of Azure regions.</span></span> <span data-ttu-id="ed145-240">所有讀取都會傳送到清單中的第一個可用區域。</span><span class="sxs-lookup"><span data-stu-id="ed145-240">All reads will be sent to the first available region in the list.</span></span> <span data-ttu-id="ed145-241">如果要求失敗，用戶端會依序嘗試清單中的其他區域。</span><span class="sxs-lookup"><span data-stu-id="ed145-241">If the request fails, the client will try the other regions in the list, in order.</span></span> <span data-ttu-id="ed145-242">如需詳細資訊，請參閱[如何使用 SQL API 來設定 Azure Cosmos DB 全域散發][cosmosdb-multi-region]。</span><span class="sxs-lookup"><span data-stu-id="ed145-242">For more information, see [How to setup Azure Cosmos DB global distribution using the SQL API][cosmosdb-multi-region].</span></span>

<span data-ttu-id="ed145-243">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-243">**Diagnostics**.</span></span> <span data-ttu-id="ed145-244">記錄用戶端上的所有錯誤。</span><span class="sxs-lookup"><span data-stu-id="ed145-244">Log all errors on the client side.</span></span>

### <a name="writing-data-fails"></a><span data-ttu-id="ed145-245">寫入資料時失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-245">Writing data fails.</span></span>
<span data-ttu-id="ed145-246">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-246">**Detection**.</span></span> <span data-ttu-id="ed145-247">捕捉 `System.Net.Http.HttpRequestException` 或 `Microsoft.Azure.Documents.DocumentClientException`。</span><span class="sxs-lookup"><span data-stu-id="ed145-247">Catch `System.Net.Http.HttpRequestException` or `Microsoft.Azure.Documents.DocumentClientException`.</span></span>

<span data-ttu-id="ed145-248">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-248">**Recovery**</span></span>

* <span data-ttu-id="ed145-249">SDK 會自動重試失敗的嘗試。</span><span class="sxs-lookup"><span data-stu-id="ed145-249">The SDK automatically retries failed attempts.</span></span> <span data-ttu-id="ed145-250">若要設定重試次數和等待時間上限，請設定 `ConnectionPolicy.RetryOptions`。</span><span class="sxs-lookup"><span data-stu-id="ed145-250">To set the number of retries and the maximum wait time, configure `ConnectionPolicy.RetryOptions`.</span></span> <span data-ttu-id="ed145-251">用戶端引發的例外狀況可能超出重試原則，或不是暫時性的錯誤。</span><span class="sxs-lookup"><span data-stu-id="ed145-251">Exceptions that the client raises are either beyond the retry policy or are not transient errors.</span></span>
* <span data-ttu-id="ed145-252">如果 Cosmos DB 將用戶端節流，它會傳回 HTTP 429 錯誤。</span><span class="sxs-lookup"><span data-stu-id="ed145-252">If Cosmos DB throttles the client, it returns an HTTP 429 error.</span></span> <span data-ttu-id="ed145-253">檢查 `DocumentClientException` 中的狀態碼。</span><span class="sxs-lookup"><span data-stu-id="ed145-253">Check the status code in the `DocumentClientException`.</span></span> <span data-ttu-id="ed145-254">如果您一直收到 429 錯誤，請考慮增加集合的輸送量值。</span><span class="sxs-lookup"><span data-stu-id="ed145-254">If you are getting error 429 consistently, consider increasing the throughput value of the collection.</span></span>
* <span data-ttu-id="ed145-255">跨兩個以上的區域複寫 Cosmos DB 資料庫。</span><span class="sxs-lookup"><span data-stu-id="ed145-255">Replicate the Cosmos DB database across two or more regions.</span></span> <span data-ttu-id="ed145-256">如果主要區域失敗，系統會將另一個區域升階以便寫入。</span><span class="sxs-lookup"><span data-stu-id="ed145-256">If the primary region fails, another region will be promoted to write.</span></span> <span data-ttu-id="ed145-257">您也可以手動觸發容錯移轉。</span><span class="sxs-lookup"><span data-stu-id="ed145-257">You can also trigger a failover manually.</span></span> <span data-ttu-id="ed145-258">SDK 會進行自動探索及路由，讓應用程式程式碼可以在容錯移轉後繼續運作。</span><span class="sxs-lookup"><span data-stu-id="ed145-258">The SDK does automatic discovery and routing, so application code continues to work after a failover.</span></span> <span data-ttu-id="ed145-259">在容錯移轉期間 (通常是幾分鐘)，SDK 會尋找新的寫入區域，因此寫入作業的延遲會比較高。</span><span class="sxs-lookup"><span data-stu-id="ed145-259">During the failover period (typically minutes), write operations will have higher latency, as the SDK finds the new write region.</span></span>
  <span data-ttu-id="ed145-260">如需詳細資訊，請參閱[如何使用 SQL API 來設定 Azure Cosmos DB 全域散發][cosmosdb-multi-region]。</span><span class="sxs-lookup"><span data-stu-id="ed145-260">For more information, see [How to setup Azure Cosmos DB global distribution using the SQL API][cosmosdb-multi-region].</span></span>
* <span data-ttu-id="ed145-261">將文件保存至備份佇列，並於稍後處理佇列，以作為後援手段。</span><span class="sxs-lookup"><span data-stu-id="ed145-261">As a fallback, persist the document to a backup queue, and process the queue later.</span></span>

<span data-ttu-id="ed145-262">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-262">**Diagnostics**.</span></span> <span data-ttu-id="ed145-263">記錄用戶端上的所有錯誤。</span><span class="sxs-lookup"><span data-stu-id="ed145-263">Log all errors on the client side.</span></span>

## <a name="elasticsearch"></a><span data-ttu-id="ed145-264">Elasticsearch</span><span class="sxs-lookup"><span data-stu-id="ed145-264">Elasticsearch</span></span>
### <a name="reading-data-from-elasticsearch-fails"></a><span data-ttu-id="ed145-265">從 Elasticsearch 讀取資料時失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-265">Reading data from Elasticsearch fails.</span></span>
<span data-ttu-id="ed145-266">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-266">**Detection**.</span></span> <span data-ttu-id="ed145-267">針對正在使用的特定 [Elasticsearch 用戶端][elasticsearch-client] 捕捉到適當的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ed145-267">Catch the appropriate exception for the particular [Elasticsearch client][elasticsearch-client] being used.</span></span>

<span data-ttu-id="ed145-268">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-268">**Recovery**</span></span>

* <span data-ttu-id="ed145-269">使用重試機制。</span><span class="sxs-lookup"><span data-stu-id="ed145-269">Use a retry mechanism.</span></span> <span data-ttu-id="ed145-270">每個用戶端都有自己的重試原則。</span><span class="sxs-lookup"><span data-stu-id="ed145-270">Each client has its own retry policies.</span></span>
* <span data-ttu-id="ed145-271">請部署多個 Elasticsearch 節點，並使用複寫功能以獲得高可用性。</span><span class="sxs-lookup"><span data-stu-id="ed145-271">Deploy multiple Elasticsearch nodes and use replication for high availability.</span></span>

<span data-ttu-id="ed145-272">如需詳細資訊，請參閱[在 Azure 上執行 Elasticsearch][elasticsearch-azure]。</span><span class="sxs-lookup"><span data-stu-id="ed145-272">For more information, see [Running Elasticsearch on Azure][elasticsearch-azure].</span></span>

<span data-ttu-id="ed145-273">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-273">**Diagnostics**.</span></span> <span data-ttu-id="ed145-274">您可以使用 Elasticsearch 的監視工具，或在用戶端上使用承載記錄所有錯誤。</span><span class="sxs-lookup"><span data-stu-id="ed145-274">You can use monitoring tools for Elasticsearch, or log all errors on the client side with the payload.</span></span> <span data-ttu-id="ed145-275">請參閱[在 Azure 上執行 Elasticsearch][elasticsearch-azure] 中的＜監視＞一節。</span><span class="sxs-lookup"><span data-stu-id="ed145-275">See the 'Monitoring' section in [Running Elasticsearch on Azure][elasticsearch-azure].</span></span>

### <a name="writing-data-to-elasticsearch-fails"></a><span data-ttu-id="ed145-276">將資料寫入 Elasticsearch 時失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-276">Writing data to Elasticsearch fails.</span></span>
<span data-ttu-id="ed145-277">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-277">**Detection**.</span></span> <span data-ttu-id="ed145-278">針對正在使用的特定 [Elasticsearch 用戶端][elasticsearch-client] 捕捉到適當的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ed145-278">Catch the appropriate exception for the particular [Elasticsearch client][elasticsearch-client] being used.</span></span>  

<span data-ttu-id="ed145-279">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-279">**Recovery**</span></span>

* <span data-ttu-id="ed145-280">使用重試機制。</span><span class="sxs-lookup"><span data-stu-id="ed145-280">Use a retry mechanism.</span></span> <span data-ttu-id="ed145-281">每個用戶端都有自己的重試原則。</span><span class="sxs-lookup"><span data-stu-id="ed145-281">Each client has its own retry policies.</span></span>
* <span data-ttu-id="ed145-282">如果應用程式可以容忍一致性層級降低，請考慮使用 `quorum` 的 `write_consistency` 設定來寫入。</span><span class="sxs-lookup"><span data-stu-id="ed145-282">If the application can tolerate a reduced consistency level, consider writing with `write_consistency` setting of `quorum`.</span></span>

<span data-ttu-id="ed145-283">如需詳細資訊，請參閱[在 Azure 上執行 Elasticsearch][elasticsearch-azure]。</span><span class="sxs-lookup"><span data-stu-id="ed145-283">For more information, see [Running Elasticsearch on Azure][elasticsearch-azure].</span></span>

<span data-ttu-id="ed145-284">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-284">**Diagnostics**.</span></span> <span data-ttu-id="ed145-285">您可以使用 Elasticsearch 的監視工具，或在用戶端上使用承載記錄所有錯誤。</span><span class="sxs-lookup"><span data-stu-id="ed145-285">You can use monitoring tools for Elasticsearch, or log all errors on the client side with the payload.</span></span> <span data-ttu-id="ed145-286">請參閱[在 Azure 上執行 Elasticsearch][elasticsearch-azure] 中的＜監視＞一節。</span><span class="sxs-lookup"><span data-stu-id="ed145-286">See the 'Monitoring' section in [Running Elasticsearch on Azure][elasticsearch-azure].</span></span>

## <a name="queue-storage"></a><span data-ttu-id="ed145-287">佇列儲存體</span><span class="sxs-lookup"><span data-stu-id="ed145-287">Queue storage</span></span>
### <a name="writing-a-message-to-azure-queue-storage-fails-consistently"></a><span data-ttu-id="ed145-288">將訊息寫入 Azure 佇列儲存體時一直失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-288">Writing a message to Azure Queue storage fails consistently.</span></span>
<span data-ttu-id="ed145-289">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-289">**Detection**.</span></span> <span data-ttu-id="ed145-290">嘗試 N 次重試之後，寫入作業仍舊失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-290">After *N* retry attempts, the write operation still fails.</span></span>

<span data-ttu-id="ed145-291">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-291">**Recovery**</span></span>

* <span data-ttu-id="ed145-292">將資料儲存在本機快取，並於之後服務恢復可用時將寫入轉送至儲存體。</span><span class="sxs-lookup"><span data-stu-id="ed145-292">Store the data in a local cache, and forward the writes to storage later, when the service becomes available.</span></span>
* <span data-ttu-id="ed145-293">建立次要佇列，並在主要佇列無法使用時寫入至該佇列。</span><span class="sxs-lookup"><span data-stu-id="ed145-293">Create a secondary queue, and write to that queue if the primary queue is unavailable.</span></span>

<span data-ttu-id="ed145-294">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-294">**Diagnostics**.</span></span> <span data-ttu-id="ed145-295">使用[儲存體度量][storage-metrics]。</span><span class="sxs-lookup"><span data-stu-id="ed145-295">Use [storage metrics][storage-metrics].</span></span>

### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a><span data-ttu-id="ed145-296">應用程式無法處理來自該佇列的特定訊息。</span><span class="sxs-lookup"><span data-stu-id="ed145-296">The application cannot process a particular message from the queue.</span></span>
<span data-ttu-id="ed145-297">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-297">**Detection**.</span></span> <span data-ttu-id="ed145-298">應用程式所特有。</span><span class="sxs-lookup"><span data-stu-id="ed145-298">Application specific.</span></span> <span data-ttu-id="ed145-299">例如，訊息包含無效資料，或是商務邏輯因故失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-299">For example, the message contains invalid data, or the business logic fails for some reason.</span></span>

<span data-ttu-id="ed145-300">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-300">**Recovery**</span></span>

<span data-ttu-id="ed145-301">將訊息移至其他佇列。</span><span class="sxs-lookup"><span data-stu-id="ed145-301">Move the message to a separate queue.</span></span> <span data-ttu-id="ed145-302">執行其他處理序以檢查該佇列中的訊息。</span><span class="sxs-lookup"><span data-stu-id="ed145-302">Run a separate process to examine the messages in that queue.</span></span>

<span data-ttu-id="ed145-303">請考慮使用 Azure 服務匯流排傳訊佇列，其可提供[無效信件佇列][sb-dead-letter-queue]以便用於此目的。</span><span class="sxs-lookup"><span data-stu-id="ed145-303">Consider using Azure Service Bus Messaging queues, which provides a [dead-letter queue][sb-dead-letter-queue] functionality for this purpose.</span></span>

> [!NOTE]
> <span data-ttu-id="ed145-304">如果您搭配使用儲存體佇列和 WebJobs，WebJobs SDK 會提供內建的有害訊息處理功能。</span><span class="sxs-lookup"><span data-stu-id="ed145-304">If you are using Storage queues with WebJobs, the WebJobs SDK provides built-in poison message handling.</span></span> <span data-ttu-id="ed145-305">請參閱[如何透過 WebJob SDK 使用 Azure 佇列儲存體 (英文)][sb-poison-message]。</span><span class="sxs-lookup"><span data-stu-id="ed145-305">See [How to use Azure queue storage with the WebJobs SDK][sb-poison-message].</span></span>

<span data-ttu-id="ed145-306">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-306">**Diagnostics**.</span></span> <span data-ttu-id="ed145-307">使用應用程式記錄。</span><span class="sxs-lookup"><span data-stu-id="ed145-307">Use application logging.</span></span>

## <a name="redis-cache"></a><span data-ttu-id="ed145-308">Redis 快取</span><span class="sxs-lookup"><span data-stu-id="ed145-308">Redis Cache</span></span>
### <a name="reading-from-the-cache-fails"></a><span data-ttu-id="ed145-309">讀取快取時失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-309">Reading from the cache fails.</span></span>
<span data-ttu-id="ed145-310">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-310">**Detection**.</span></span> <span data-ttu-id="ed145-311">捕捉 `StackExchange.Redis.RedisConnectionException`。</span><span class="sxs-lookup"><span data-stu-id="ed145-311">Catch `StackExchange.Redis.RedisConnectionException`.</span></span>

<span data-ttu-id="ed145-312">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-312">**Recovery**</span></span>

1. <span data-ttu-id="ed145-313">重試暫時性失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-313">Retry on transient failures.</span></span> <span data-ttu-id="ed145-314">Azure Redis 快取支援內建重試功能。請參閱 [Redis 快取重試指引][redis-retry]。</span><span class="sxs-lookup"><span data-stu-id="ed145-314">Azure Redis cache supports built-in retry through See [Redis Cache retry guidelines][redis-retry].</span></span>
2. <span data-ttu-id="ed145-315">將非暫時性失敗視為快取遺漏，並切換回原始資料來源。</span><span class="sxs-lookup"><span data-stu-id="ed145-315">Treat non-transient failures as a cache miss, and fall back to the original data source.</span></span>

<span data-ttu-id="ed145-316">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-316">**Diagnostics**.</span></span> <span data-ttu-id="ed145-317">使用 [Redis 快取診斷][redis-monitor]。</span><span class="sxs-lookup"><span data-stu-id="ed145-317">Use [Redis Cache diagnostics][redis-monitor].</span></span>

### <a name="writing-to-the-cache-fails"></a><span data-ttu-id="ed145-318">寫入至快取時失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-318">Writing to the cache fails.</span></span>
<span data-ttu-id="ed145-319">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-319">**Detection**.</span></span> <span data-ttu-id="ed145-320">捕捉 `StackExchange.Redis.RedisConnectionException`。</span><span class="sxs-lookup"><span data-stu-id="ed145-320">Catch `StackExchange.Redis.RedisConnectionException`.</span></span>

<span data-ttu-id="ed145-321">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-321">**Recovery**</span></span>

1. <span data-ttu-id="ed145-322">重試暫時性失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-322">Retry on transient failures.</span></span> <span data-ttu-id="ed145-323">Azure Redis 快取支援內建重試功能。請參閱 [Redis 快取重試指引][redis-retry]。</span><span class="sxs-lookup"><span data-stu-id="ed145-323">Azure Redis cache supports built-in retry through See [Redis Cache retry guidelines][redis-retry].</span></span>
2. <span data-ttu-id="ed145-324">如果是非暫時性錯誤，請予以忽略，並於稍後讓其他交易寫入至快取。</span><span class="sxs-lookup"><span data-stu-id="ed145-324">If the error is non-transient, ignore it and let other transactions write to the cache later.</span></span>

<span data-ttu-id="ed145-325">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-325">**Diagnostics**.</span></span> <span data-ttu-id="ed145-326">使用 [Redis 快取診斷][redis-monitor]。</span><span class="sxs-lookup"><span data-stu-id="ed145-326">Use [Redis Cache diagnostics][redis-monitor].</span></span>

## <a name="sql-database"></a><span data-ttu-id="ed145-327">SQL Database</span><span class="sxs-lookup"><span data-stu-id="ed145-327">SQL Database</span></span>
### <a name="cannot-connect-to-the-database-in-the-primary-region"></a><span data-ttu-id="ed145-328">無法連線到主要區域中的資料庫。</span><span class="sxs-lookup"><span data-stu-id="ed145-328">Cannot connect to the database in the primary region.</span></span>
<span data-ttu-id="ed145-329">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-329">**Detection**.</span></span> <span data-ttu-id="ed145-330">連線失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-330">Connection fails.</span></span>

<span data-ttu-id="ed145-331">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-331">**Recovery**</span></span>

<span data-ttu-id="ed145-332">必要條件：資料庫必須設定作用中異地複寫。</span><span class="sxs-lookup"><span data-stu-id="ed145-332">Prerequisite: The database must be configured for active geo-replication.</span></span> <span data-ttu-id="ed145-333">請參閱 [SQL Database 作用中異地複寫][sql-db-replication]。</span><span class="sxs-lookup"><span data-stu-id="ed145-333">See [SQL Database Active Geo-Replication][sql-db-replication].</span></span>

* <span data-ttu-id="ed145-334">若為查詢，讀取次要複本。</span><span class="sxs-lookup"><span data-stu-id="ed145-334">For queries, read from a secondary replica.</span></span>
* <span data-ttu-id="ed145-335">若為插入和更新，手動容錯移轉至次要複本。</span><span class="sxs-lookup"><span data-stu-id="ed145-335">For inserts and updates, manually fail over to a secondary replica.</span></span> <span data-ttu-id="ed145-336">請參閱[為 Azure SQL Database 起始計劃性或非計劃性容錯移轉][sql-db-failover]。</span><span class="sxs-lookup"><span data-stu-id="ed145-336">See [Initiate a planned or unplanned failover for Azure SQL Database][sql-db-failover].</span></span>

<span data-ttu-id="ed145-337">複本會使用不同的連接字串，因此您必須更新應用程式中的連接字串。</span><span class="sxs-lookup"><span data-stu-id="ed145-337">The replica uses a different connection string, so you will need to update the connection string in your application.</span></span>

### <a name="client-runs-out-of-connections-in-the-connection-pool"></a><span data-ttu-id="ed145-338">用戶端用盡連線集區中的連線。</span><span class="sxs-lookup"><span data-stu-id="ed145-338">Client runs out of connections in the connection pool.</span></span>
<span data-ttu-id="ed145-339">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-339">**Detection**.</span></span> <span data-ttu-id="ed145-340">捕捉 `System.InvalidOperationException` 錯誤。</span><span class="sxs-lookup"><span data-stu-id="ed145-340">Catch `System.InvalidOperationException` errors.</span></span>

<span data-ttu-id="ed145-341">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-341">**Recovery**</span></span>

* <span data-ttu-id="ed145-342">重試作業。</span><span class="sxs-lookup"><span data-stu-id="ed145-342">Retry the operation.</span></span>
* <span data-ttu-id="ed145-343">作為風險降低計畫，請隔離每個使用案例的連線集區，不要讓一個使用案例就能支配所有連線。</span><span class="sxs-lookup"><span data-stu-id="ed145-343">As a mitigation plan, isolate the connection pools for each use case, so that one use case can't dominate all the connections.</span></span>
* <span data-ttu-id="ed145-344">增加連線集區上限。</span><span class="sxs-lookup"><span data-stu-id="ed145-344">Increase the maximum connection pools.</span></span>

<span data-ttu-id="ed145-345">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-345">**Diagnostics**.</span></span> <span data-ttu-id="ed145-346">應用程式記錄。</span><span class="sxs-lookup"><span data-stu-id="ed145-346">Application logs.</span></span>

### <a name="database-connection-limit-is-reached"></a><span data-ttu-id="ed145-347">達到資料庫連線限制。</span><span class="sxs-lookup"><span data-stu-id="ed145-347">Database connection limit is reached.</span></span>
<span data-ttu-id="ed145-348">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-348">**Detection**.</span></span> <span data-ttu-id="ed145-349">Azure SQL Database 會限制並行之工作者、登入和工作階段的數目。</span><span class="sxs-lookup"><span data-stu-id="ed145-349">Azure SQL Database limits the number of concurrent workers, logins, and sessions.</span></span> <span data-ttu-id="ed145-350">這些限制取決於服務層。</span><span class="sxs-lookup"><span data-stu-id="ed145-350">The limits depend on the service tier.</span></span> <span data-ttu-id="ed145-351">如需詳細資訊，請參閱 [Azure SQL Database 資源限制][sql-db-limits]。</span><span class="sxs-lookup"><span data-stu-id="ed145-351">For more information, see [Azure SQL Database resource limits][sql-db-limits].</span></span>

<span data-ttu-id="ed145-352">若要偵測這些錯誤，請捕捉 `System.Data.SqlClient.SqlException` 並檢查 SQL 錯誤碼的 `SqlException.Number` 值。</span><span class="sxs-lookup"><span data-stu-id="ed145-352">To detect these errors, catch `System.Data.SqlClient.SqlException` and check the value of `SqlException.Number` for the SQL error code.</span></span> <span data-ttu-id="ed145-353">如需相關錯誤碼的清單，請參閱 [SQL Database 用戶端應用程式的 SQL 錯誤碼：資料庫連線錯誤和其他問題][sql-db-errors]。</span><span class="sxs-lookup"><span data-stu-id="ed145-353">For a list of relevant error codes, see [SQL error codes for SQL Database client applications: Database connection error and other issues][sql-db-errors].</span></span>

<span data-ttu-id="ed145-354">**復原**。</span><span class="sxs-lookup"><span data-stu-id="ed145-354">**Recovery**.</span></span> <span data-ttu-id="ed145-355">這些錯誤應該是暫時性的，因此重試後或許就能解決問題。</span><span class="sxs-lookup"><span data-stu-id="ed145-355">These errors are considered transient, so retrying may resolve the issue.</span></span> <span data-ttu-id="ed145-356">如果您一直遇到這些錯誤，請考慮調整資料庫。</span><span class="sxs-lookup"><span data-stu-id="ed145-356">If you consistently hit these errors, consider scaling the database.</span></span>

<span data-ttu-id="ed145-357">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-357">**Diagnostics**.</span></span> <span data-ttu-id="ed145-358">- [sys.event_log][sys.event_log] 查詢會傳回成功的資料庫連線、連線失敗和死結。</span><span class="sxs-lookup"><span data-stu-id="ed145-358">- The [sys.event_log][sys.event_log] query returns successful database connections, connection failures, and deadlocks.</span></span>

* <span data-ttu-id="ed145-359">為失敗的連線建立[警示規則][azure-alerts]。</span><span class="sxs-lookup"><span data-stu-id="ed145-359">Create an [alert rule][azure-alerts] for failed connections.</span></span>
* <span data-ttu-id="ed145-360">啟用 [SQL Database 稽核][sql-db-audit]，並檢查失敗的登入。</span><span class="sxs-lookup"><span data-stu-id="ed145-360">Enable [SQL Database auditing][sql-db-audit] and check for failed logins.</span></span>

## <a name="service-bus-messaging"></a><span data-ttu-id="ed145-361">服務匯流排傳訊</span><span class="sxs-lookup"><span data-stu-id="ed145-361">Service Bus Messaging</span></span>
### <a name="reading-a-message-from-a-service-bus-queue-fails"></a><span data-ttu-id="ed145-362">從服務匯流排佇列讀取訊息時失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-362">Reading a message from a Service Bus queue fails.</span></span>
<span data-ttu-id="ed145-363">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-363">**Detection**.</span></span> <span data-ttu-id="ed145-364">從用戶端 SDK 捕捉例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ed145-364">Catch exceptions from the client SDK.</span></span> <span data-ttu-id="ed145-365">服務匯流排例外狀況的基底類別是 [MessagingException][sb-messagingexception-class]。</span><span class="sxs-lookup"><span data-stu-id="ed145-365">The base class for Service Bus exceptions is [MessagingException][sb-messagingexception-class].</span></span> <span data-ttu-id="ed145-366">如果是暫時性錯誤，`IsTransient` 屬性會是 true。</span><span class="sxs-lookup"><span data-stu-id="ed145-366">If the error is transient, the `IsTransient` property is true.</span></span>

<span data-ttu-id="ed145-367">如需詳細資訊，請參閱[服務匯流排傳訊例外狀況][sb-messaging-exceptions]。</span><span class="sxs-lookup"><span data-stu-id="ed145-367">For more information, see [Service Bus messaging exceptions][sb-messaging-exceptions].</span></span>

<span data-ttu-id="ed145-368">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-368">**Recovery**</span></span>

1. <span data-ttu-id="ed145-369">重試暫時性失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-369">Retry on transient failures.</span></span> <span data-ttu-id="ed145-370">請參閱[服務匯流排重試指引][sb-retry]。</span><span class="sxs-lookup"><span data-stu-id="ed145-370">See [Service Bus retry guidelines][sb-retry].</span></span>
2. <span data-ttu-id="ed145-371">無法傳遞給任何接收者的訊息會放在「無效信件佇列」。</span><span class="sxs-lookup"><span data-stu-id="ed145-371">Messages that cannot be delivered to any receiver are placed in a *dead-letter queue*.</span></span> <span data-ttu-id="ed145-372">您可以使用此佇列來查看有哪些訊息無法接收。</span><span class="sxs-lookup"><span data-stu-id="ed145-372">Use this queue to see which messages could not be received.</span></span> <span data-ttu-id="ed145-373">無效信件佇列不會自動清除。</span><span class="sxs-lookup"><span data-stu-id="ed145-373">There is no automatic cleanup of the dead-letter queue.</span></span> <span data-ttu-id="ed145-374">訊息會一直保留在此，直到您明確地擷取走。</span><span class="sxs-lookup"><span data-stu-id="ed145-374">Messages remain there until you explicitly retrieve them.</span></span> <span data-ttu-id="ed145-375">請參閱[服務匯流排寄不出的信件佇列的概觀][sb-dead-letter-queue]。</span><span class="sxs-lookup"><span data-stu-id="ed145-375">See [Overview of Service Bus dead-letter queues][sb-dead-letter-queue].</span></span>

### <a name="writing-a-message-to-a-service-bus-queue-fails"></a><span data-ttu-id="ed145-376">將訊息寫入服務匯流排佇列時失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-376">Writing a message to a Service Bus queue fails.</span></span>
<span data-ttu-id="ed145-377">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-377">**Detection**.</span></span> <span data-ttu-id="ed145-378">從用戶端 SDK 捕捉例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ed145-378">Catch exceptions from the client SDK.</span></span> <span data-ttu-id="ed145-379">服務匯流排例外狀況的基底類別是 [MessagingException][sb-messagingexception-class]。</span><span class="sxs-lookup"><span data-stu-id="ed145-379">The base class for Service Bus exceptions is [MessagingException][sb-messagingexception-class].</span></span> <span data-ttu-id="ed145-380">如果是暫時性錯誤，`IsTransient` 屬性會是 true。</span><span class="sxs-lookup"><span data-stu-id="ed145-380">If the error is transient, the `IsTransient` property is true.</span></span>

<span data-ttu-id="ed145-381">如需詳細資訊，請參閱[服務匯流排傳訊例外狀況][sb-messaging-exceptions]。</span><span class="sxs-lookup"><span data-stu-id="ed145-381">For more information, see [Service Bus messaging exceptions][sb-messaging-exceptions].</span></span>

<span data-ttu-id="ed145-382">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-382">**Recovery**</span></span>

1. <span data-ttu-id="ed145-383">服務匯流排用戶端會在發生暫時性錯誤後自動重試。</span><span class="sxs-lookup"><span data-stu-id="ed145-383">The Service Bus client automatically retries after transient errors.</span></span> <span data-ttu-id="ed145-384">根據預設，它會使用指數輪詢。</span><span class="sxs-lookup"><span data-stu-id="ed145-384">By default, it uses exponential back-off.</span></span> <span data-ttu-id="ed145-385">在達到重試次數上限或最大逾時期限後，用戶端會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ed145-385">After the maximum retry count or maximum timeout period, the client throws an exception.</span></span> <span data-ttu-id="ed145-386">如需詳細資訊，請參閱[服務匯流排重試指引][sb-retry]。</span><span class="sxs-lookup"><span data-stu-id="ed145-386">For more information, see [Service Bus retry guidelines][sb-retry].</span></span>
2. <span data-ttu-id="ed145-387">如果超過佇列配額，用戶端就會擲回 [QuotaExceededException][QuotaExceededException]。</span><span class="sxs-lookup"><span data-stu-id="ed145-387">If the queue quota is exceeded, the client throws [QuotaExceededException][QuotaExceededException].</span></span> <span data-ttu-id="ed145-388">例外狀況訊息會提供更多詳細資料。</span><span class="sxs-lookup"><span data-stu-id="ed145-388">The exception message gives more details.</span></span> <span data-ttu-id="ed145-389">重試前請先清空佇列中的某些訊息，並考慮使用斷路器模式，以免在超出配額時不斷重試。</span><span class="sxs-lookup"><span data-stu-id="ed145-389">Drain some messages from the queue before retrying, and consider using the Circuit Breaker pattern to avoid continued retries while the quota is exceeded.</span></span> <span data-ttu-id="ed145-390">此外，請確定 [BrokeredMessage.TimeToLive] 屬性未設得太高。</span><span class="sxs-lookup"><span data-stu-id="ed145-390">Also, make sure the [BrokeredMessage.TimeToLive] property is not set too high.</span></span>
3. <span data-ttu-id="ed145-391">區域的復原能力可使用[分割的佇列或主題][sb-partition]來加以改善。</span><span class="sxs-lookup"><span data-stu-id="ed145-391">Within a region, resiliency can be improved by using [partitioned queues or topics][sb-partition].</span></span> <span data-ttu-id="ed145-392">非分割的佇列或主題會指派給一個訊息存放區。</span><span class="sxs-lookup"><span data-stu-id="ed145-392">A non-partitioned queue or topic is assigned to one messaging store.</span></span> <span data-ttu-id="ed145-393">如果此訊息存放區無法使用，該佇列或主題上的所有作業都會失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-393">If this messaging store is unavailable, all operations on that queue or topic will fail.</span></span> <span data-ttu-id="ed145-394">分割的佇列或主題會分割到多個訊息存放區。</span><span class="sxs-lookup"><span data-stu-id="ed145-394">A partitioned queue or topic is partitioned across multiple messaging stores.</span></span>
4. <span data-ttu-id="ed145-395">若要提升復原能力，請在不同區域建立兩個服務匯流排命名空間，並複寫訊息。</span><span class="sxs-lookup"><span data-stu-id="ed145-395">For additional resiliency, create two Service Bus namespaces in different regions, and replicate the messages.</span></span> <span data-ttu-id="ed145-396">使用作用中複寫或被動複寫。</span><span class="sxs-lookup"><span data-stu-id="ed145-396">You can use either active replication or passive replication.</span></span>

   * <span data-ttu-id="ed145-397">作用中複寫：用戶端會將每個訊息傳送給這兩個佇列。</span><span class="sxs-lookup"><span data-stu-id="ed145-397">Active replication: The client sends every message to both queues.</span></span> <span data-ttu-id="ed145-398">收件者會接聽這兩個佇列。</span><span class="sxs-lookup"><span data-stu-id="ed145-398">The receiver listens on both queues.</span></span> <span data-ttu-id="ed145-399">使用唯一識別碼標記訊息，讓用戶端可以捨棄重複的訊息。</span><span class="sxs-lookup"><span data-stu-id="ed145-399">Tag messages with a unique identifier, so the client can discard duplicate messages.</span></span>
   * <span data-ttu-id="ed145-400">被動複寫：用戶端會將訊息傳送給一個佇列。</span><span class="sxs-lookup"><span data-stu-id="ed145-400">Passive replication: The client sends the message to one queue.</span></span> <span data-ttu-id="ed145-401">如果發生錯誤，用戶端會退為傳送給另一個佇列。</span><span class="sxs-lookup"><span data-stu-id="ed145-401">If there is an error, the client falls back to the other queue.</span></span> <span data-ttu-id="ed145-402">收件者會接聽這兩個佇列。</span><span class="sxs-lookup"><span data-stu-id="ed145-402">The receiver listens on both queues.</span></span> <span data-ttu-id="ed145-403">這種方式可減少所傳送的重複訊息數目。</span><span class="sxs-lookup"><span data-stu-id="ed145-403">This approach reduces the number of duplicate messages that are sent.</span></span> <span data-ttu-id="ed145-404">不過，接收者仍必須處理重複的訊息。</span><span class="sxs-lookup"><span data-stu-id="ed145-404">However, the receiver must still handle duplicate messages.</span></span>

     <span data-ttu-id="ed145-405">如需詳細資訊，請參閱 [GeoReplication sample][sb-georeplication-sample] 和[將應用程式與服務匯流排中斷和災難隔絕的最佳做法 (英文)](/azure/service-bus-messaging/service-bus-outages-disasters/)。</span><span class="sxs-lookup"><span data-stu-id="ed145-405">For more information, see [GeoReplication sample][sb-georeplication-sample] and [Best practices for insulating applications against Service Bus outages and disasters](/azure/service-bus-messaging/service-bus-outages-disasters/).</span></span>

### <a name="duplicate-message"></a><span data-ttu-id="ed145-406">重複的訊息。</span><span class="sxs-lookup"><span data-stu-id="ed145-406">Duplicate message.</span></span>
<span data-ttu-id="ed145-407">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-407">**Detection**.</span></span> <span data-ttu-id="ed145-408">檢查訊息的 `MessageId` 和 `DeliveryCount` 屬性。</span><span class="sxs-lookup"><span data-stu-id="ed145-408">Examine the `MessageId` and `DeliveryCount` properties of the message.</span></span>

<span data-ttu-id="ed145-409">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-409">**Recovery**</span></span>

* <span data-ttu-id="ed145-410">可能的話，請將訊息處理作業設計為等冪形式。</span><span class="sxs-lookup"><span data-stu-id="ed145-410">If possible, design your message processing operations to be idempotent.</span></span> <span data-ttu-id="ed145-411">否則，請儲存已經處理之訊息的訊息識別碼，然後於檢查識別碼後再處理訊息。</span><span class="sxs-lookup"><span data-stu-id="ed145-411">Otherwise, store message IDs of messages that are already processed, and check the ID before processing a message.</span></span>
* <span data-ttu-id="ed145-412">藉由建立佇列並將 `RequiresDuplicateDetection` 設為 true，以啟用重複偵測。</span><span class="sxs-lookup"><span data-stu-id="ed145-412">Enable duplicate detection, by creating the queue with `RequiresDuplicateDetection` set to true.</span></span> <span data-ttu-id="ed145-413">使用此設定時，只要訊息在傳送時所使用的 `MessageId` 和先前的訊息相同，服務匯流排就會自動刪除該訊息。</span><span class="sxs-lookup"><span data-stu-id="ed145-413">With this setting, Service Bus automatically deletes any message that is sent with the same `MessageId` as a previous message.</span></span>  <span data-ttu-id="ed145-414">請注意：</span><span class="sxs-lookup"><span data-stu-id="ed145-414">Note the following:</span></span>

  * <span data-ttu-id="ed145-415">此設定可防止佇列中放入重複的訊息。</span><span class="sxs-lookup"><span data-stu-id="ed145-415">This setting prevents duplicate messages from being put into the queue.</span></span> <span data-ttu-id="ed145-416">它不會防止接收者多次處理相同的訊息。</span><span class="sxs-lookup"><span data-stu-id="ed145-416">It doesn't prevent a receiver from processing the same message more than once.</span></span>
  * <span data-ttu-id="ed145-417">重複偵測有時間範圍。</span><span class="sxs-lookup"><span data-stu-id="ed145-417">Duplicate detection has a time window.</span></span> <span data-ttu-id="ed145-418">如果重複項目不是在此時間範圍內傳送，系統就不會偵測到。</span><span class="sxs-lookup"><span data-stu-id="ed145-418">If a duplicate is sent beyond this window, it won't be detected.</span></span>

<span data-ttu-id="ed145-419">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-419">**Diagnostics**.</span></span> <span data-ttu-id="ed145-420">記錄重複的訊息。</span><span class="sxs-lookup"><span data-stu-id="ed145-420">Log duplicated messages.</span></span>

### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a><span data-ttu-id="ed145-421">應用程式無法處理來自該佇列的特定訊息。</span><span class="sxs-lookup"><span data-stu-id="ed145-421">The application cannot process a particular message from the queue.</span></span>
<span data-ttu-id="ed145-422">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-422">**Detection**.</span></span> <span data-ttu-id="ed145-423">應用程式所特有。</span><span class="sxs-lookup"><span data-stu-id="ed145-423">Application specific.</span></span> <span data-ttu-id="ed145-424">例如，訊息包含無效資料，或是商務邏輯因故失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-424">For example, the message contains invalid data, or the business logic fails for some reason.</span></span>

<span data-ttu-id="ed145-425">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-425">**Recovery**</span></span>

<span data-ttu-id="ed145-426">可考慮使用的失敗模式有兩個。</span><span class="sxs-lookup"><span data-stu-id="ed145-426">There are two failure modes to consider.</span></span>

* <span data-ttu-id="ed145-427">接收者偵測失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-427">The receiver detects the failure.</span></span> <span data-ttu-id="ed145-428">在此案例中，將訊息移至無效信件佇列。</span><span class="sxs-lookup"><span data-stu-id="ed145-428">In this case, move the message to the dead-letter queue.</span></span> <span data-ttu-id="ed145-429">之後，執行其他處理序以檢查無效信件佇列中的訊息。</span><span class="sxs-lookup"><span data-stu-id="ed145-429">Later, run a separate process to examine the messages in the dead-letter queue.</span></span>
* <span data-ttu-id="ed145-430">接收者在訊息處理過程中失敗，例如，由於發生未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ed145-430">The receiver fails in the middle of processing the message &mdash; for example, due to an unhandled exception.</span></span> <span data-ttu-id="ed145-431">若要處理這種情況，請使用 `PeekLock` 模式。</span><span class="sxs-lookup"><span data-stu-id="ed145-431">To handle this case, use `PeekLock` mode.</span></span> <span data-ttu-id="ed145-432">在此模式中，如果鎖定到期，訊息會恢復可供其他接收者接收。</span><span class="sxs-lookup"><span data-stu-id="ed145-432">In this mode, if the lock expires, the message becomes available to other receivers.</span></span> <span data-ttu-id="ed145-433">如果訊息超出最大傳遞計數或存留時間，訊息會自動移至無效信件佇列。</span><span class="sxs-lookup"><span data-stu-id="ed145-433">If the message exceeds the maximum delivery count or the time-to-live, the message is automatically moved to the dead-letter queue.</span></span>

<span data-ttu-id="ed145-434">如需詳細資訊，請參閱[服務匯流排寄不出的信件佇列的概觀][sb-dead-letter-queue]。</span><span class="sxs-lookup"><span data-stu-id="ed145-434">For more information, see [Overview of Service Bus dead-letter queues][sb-dead-letter-queue].</span></span>

<span data-ttu-id="ed145-435">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-435">**Diagnostics**.</span></span> <span data-ttu-id="ed145-436">每當應用程式將訊息移至無效信件佇列，就會在應用程式記錄寫入事件。</span><span class="sxs-lookup"><span data-stu-id="ed145-436">Whenever the application moves a message to the dead-letter queue, write an event to the application logs.</span></span>

## <a name="service-fabric"></a><span data-ttu-id="ed145-437">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="ed145-437">Service Fabric</span></span>
### <a name="a-request-to-a-service-fails"></a><span data-ttu-id="ed145-438">要求使用某項服務時失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-438">A request to a service fails.</span></span>
<span data-ttu-id="ed145-439">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-439">**Detection**.</span></span> <span data-ttu-id="ed145-440">服務會傳回錯誤。</span><span class="sxs-lookup"><span data-stu-id="ed145-440">The service returns an error.</span></span>

<span data-ttu-id="ed145-441">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-441">**Recovery**</span></span>

* <span data-ttu-id="ed145-442">再次尋找 Proxy (`ServiceProxy` 或 `ActorProxy`)，然後再次呼叫服務/動作項目方法。</span><span class="sxs-lookup"><span data-stu-id="ed145-442">Locate a proxy again (`ServiceProxy` or `ActorProxy`) and call the service/actor method again.</span></span>
* <span data-ttu-id="ed145-443">**具狀態服務**。</span><span class="sxs-lookup"><span data-stu-id="ed145-443">**Stateful service**.</span></span> <span data-ttu-id="ed145-444">將可靠集合上的作業包裝在交易中。</span><span class="sxs-lookup"><span data-stu-id="ed145-444">Wrap operations on reliable collections in a transaction.</span></span> <span data-ttu-id="ed145-445">如果發生錯誤，便會復原交易。</span><span class="sxs-lookup"><span data-stu-id="ed145-445">If there is an error, the transaction will be rolled back.</span></span> <span data-ttu-id="ed145-446">從佇列提取出的要求會再次進行處理。</span><span class="sxs-lookup"><span data-stu-id="ed145-446">The request, if pulled from a queue, will be processed again.</span></span>
* <span data-ttu-id="ed145-447">**無狀態服務**。</span><span class="sxs-lookup"><span data-stu-id="ed145-447">**Stateless service**.</span></span> <span data-ttu-id="ed145-448">如果服務將資料保存到外部存放區，所有作業都必須具有等冪性。</span><span class="sxs-lookup"><span data-stu-id="ed145-448">If the service persists data to an external store, all operations need to be idempotent.</span></span>

<span data-ttu-id="ed145-449">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-449">**Diagnostics**.</span></span> <span data-ttu-id="ed145-450">應用程式記錄</span><span class="sxs-lookup"><span data-stu-id="ed145-450">Application log</span></span>

### <a name="service-fabric-node-is-shut-down"></a><span data-ttu-id="ed145-451">Service Fabric 節點關閉。</span><span class="sxs-lookup"><span data-stu-id="ed145-451">Service Fabric node is shut down.</span></span>
<span data-ttu-id="ed145-452">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-452">**Detection**.</span></span> <span data-ttu-id="ed145-453">系統將取消權杖傳遞給服務的 `RunAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="ed145-453">A cancellation token is passed to the service's `RunAsync` method.</span></span> <span data-ttu-id="ed145-454">Service Fabric 在關閉節點前取消工作。</span><span class="sxs-lookup"><span data-stu-id="ed145-454">Service Fabric cancels the task before shutting down the node.</span></span>

<span data-ttu-id="ed145-455">**復原**。</span><span class="sxs-lookup"><span data-stu-id="ed145-455">**Recovery**.</span></span> <span data-ttu-id="ed145-456">您可以使用取消權杖來偵測關閉情形。</span><span class="sxs-lookup"><span data-stu-id="ed145-456">Use the cancellation token to detect shutdown.</span></span> <span data-ttu-id="ed145-457">當 Service Fabric 要求取消時，請盡快完成任何工作並結束  `RunAsync`。</span><span class="sxs-lookup"><span data-stu-id="ed145-457">When Service Fabric requests cancellation, finish any work and exit `RunAsync` as quickly as possible.</span></span>

<span data-ttu-id="ed145-458">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-458">**Diagnostics**.</span></span> <span data-ttu-id="ed145-459">應用程式記錄檔</span><span class="sxs-lookup"><span data-stu-id="ed145-459">Application logs</span></span>

## <a name="storage"></a><span data-ttu-id="ed145-460">儲存體</span><span class="sxs-lookup"><span data-stu-id="ed145-460">Storage</span></span>
### <a name="writing-data-to-azure-storage-fails"></a><span data-ttu-id="ed145-461">將資料寫入 Azure 儲存體時失敗</span><span class="sxs-lookup"><span data-stu-id="ed145-461">Writing data to Azure Storage fails</span></span>
<span data-ttu-id="ed145-462">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-462">**Detection**.</span></span> <span data-ttu-id="ed145-463">用戶端會在寫入時收到錯誤。</span><span class="sxs-lookup"><span data-stu-id="ed145-463">The client receives errors when writing.</span></span>

<span data-ttu-id="ed145-464">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-464">**Recovery**</span></span>

1. <span data-ttu-id="ed145-465">重試作業以從暫時性失敗中復原。</span><span class="sxs-lookup"><span data-stu-id="ed145-465">Retry the operation, to recover from transient failures.</span></span> <span data-ttu-id="ed145-466">用戶端 SDK 中的[重試原則][Storage.RetryPolicies]會自動處理此作業。</span><span class="sxs-lookup"><span data-stu-id="ed145-466">The [retry policy][Storage.RetryPolicies] in the client SDK handles this automatically.</span></span>
2. <span data-ttu-id="ed145-467">實作斷路器模式，以避免過量儲存。</span><span class="sxs-lookup"><span data-stu-id="ed145-467">Implement the Circuit Breaker pattern to avoid overwhelming storage.</span></span>
3. <span data-ttu-id="ed145-468">如果嘗試 N 次重試都失敗，請執行正常遞補。</span><span class="sxs-lookup"><span data-stu-id="ed145-468">If N retry attempts fail, perform a graceful fallback.</span></span> <span data-ttu-id="ed145-469">例如︰</span><span class="sxs-lookup"><span data-stu-id="ed145-469">For example:</span></span>

   * <span data-ttu-id="ed145-470">將資料儲存在本機快取，並於之後服務恢復可用時將寫入轉送至儲存體。</span><span class="sxs-lookup"><span data-stu-id="ed145-470">Store the data in a local cache, and forward the writes to storage later, when the service becomes available.</span></span>
   * <span data-ttu-id="ed145-471">如果寫入動作位於交易式範圍中，則補償交易。</span><span class="sxs-lookup"><span data-stu-id="ed145-471">If the write action was in a transactional scope, compensate the transaction.</span></span>

<span data-ttu-id="ed145-472">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-472">**Diagnostics**.</span></span> <span data-ttu-id="ed145-473">使用[儲存體度量][storage-metrics]。</span><span class="sxs-lookup"><span data-stu-id="ed145-473">Use [storage metrics][storage-metrics].</span></span>

### <a name="reading-data-from-azure-storage-fails"></a><span data-ttu-id="ed145-474">從 Azure 儲存體讀取資料時失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-474">Reading data from Azure Storage fails.</span></span>
<span data-ttu-id="ed145-475">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-475">**Detection**.</span></span> <span data-ttu-id="ed145-476">用戶端會在讀取時收到錯誤。</span><span class="sxs-lookup"><span data-stu-id="ed145-476">The client receives errors when reading.</span></span>

<span data-ttu-id="ed145-477">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-477">**Recovery**</span></span>

1. <span data-ttu-id="ed145-478">重試作業以從暫時性失敗中復原。</span><span class="sxs-lookup"><span data-stu-id="ed145-478">Retry the operation, to recover from transient failures.</span></span> <span data-ttu-id="ed145-479">用戶端 SDK 中的[重試原則][Storage.RetryPolicies]會自動處理此作業。</span><span class="sxs-lookup"><span data-stu-id="ed145-479">The [retry policy][Storage.RetryPolicies] in the client SDK handles this automatically.</span></span>
2. <span data-ttu-id="ed145-480">對於 RA-GRS 儲存體，如果讀取主要端點時失敗，請試著讀取次要端點。</span><span class="sxs-lookup"><span data-stu-id="ed145-480">For RA-GRS storage, if reading from the primary endpoint fails, try reading from the secondary endpoint.</span></span> <span data-ttu-id="ed145-481">用戶端 SDK 會自動處理此作業。</span><span class="sxs-lookup"><span data-stu-id="ed145-481">The client SDK can handle this automatically.</span></span> <span data-ttu-id="ed145-482">請參閱 [Azure 儲存體複寫][storage-replication]。</span><span class="sxs-lookup"><span data-stu-id="ed145-482">See [Azure Storage replication][storage-replication].</span></span>
3. <span data-ttu-id="ed145-483">如果嘗試 N 次重試都失敗，請採取遞補動作以正常地降級。</span><span class="sxs-lookup"><span data-stu-id="ed145-483">If *N* retry attempts fail, take a fallback action to degrade gracefully.</span></span> <span data-ttu-id="ed145-484">例如，如果無法從儲存體擷取產品映像，則顯示一般的預留位置影像。</span><span class="sxs-lookup"><span data-stu-id="ed145-484">For example, if a product image can't be retrieved from storage, show a generic placeholder image.</span></span>

<span data-ttu-id="ed145-485">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-485">**Diagnostics**.</span></span> <span data-ttu-id="ed145-486">使用[儲存體度量][storage-metrics]。</span><span class="sxs-lookup"><span data-stu-id="ed145-486">Use [storage metrics][storage-metrics].</span></span>

## <a name="virtual-machine"></a><span data-ttu-id="ed145-487">虛擬機器</span><span class="sxs-lookup"><span data-stu-id="ed145-487">Virtual Machine</span></span>
### <a name="connection-to-a-backend-vm-fails"></a><span data-ttu-id="ed145-488">連線至後端 VM 時失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-488">Connection to a backend VM fails.</span></span>
<span data-ttu-id="ed145-489">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-489">**Detection**.</span></span> <span data-ttu-id="ed145-490">網路連線錯誤。</span><span class="sxs-lookup"><span data-stu-id="ed145-490">Network connection errors.</span></span>

<span data-ttu-id="ed145-491">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-491">**Recovery**</span></span>

* <span data-ttu-id="ed145-492">於負載平衡器後方，在可用性設定組內部署至少兩個後端 VM。</span><span class="sxs-lookup"><span data-stu-id="ed145-492">Deploy at least two backend VMs in an availability set, behind a load balancer.</span></span>
* <span data-ttu-id="ed145-493">如果是暫時性的連線錯誤，有時 TCP 會成功地重試傳送訊息。</span><span class="sxs-lookup"><span data-stu-id="ed145-493">If the connection error is transient, sometimes TCP will successfully retry sending the message.</span></span>
* <span data-ttu-id="ed145-494">在應用程式中實作重試原則。</span><span class="sxs-lookup"><span data-stu-id="ed145-494">Implement a retry policy in the application.</span></span>
* <span data-ttu-id="ed145-495">若為持續性錯誤或非暫時性的錯誤，請實作[斷路器][circuit-breaker]模式。</span><span class="sxs-lookup"><span data-stu-id="ed145-495">For persistent or non-transient errors, implement the [Circuit Breaker][circuit-breaker] pattern.</span></span>
* <span data-ttu-id="ed145-496">如果呼叫端 VM 超出其網路輸出限制，則輸出佇列會滿溢。</span><span class="sxs-lookup"><span data-stu-id="ed145-496">If the calling VM exceeds its network egress limit, the outbound queue will fill up.</span></span> <span data-ttu-id="ed145-497">如果輸出佇列一直滿溢，請考慮相應放大。</span><span class="sxs-lookup"><span data-stu-id="ed145-497">If the outbound queue is consistently full, consider scaling out.</span></span>

<span data-ttu-id="ed145-498">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-498">**Diagnostics**.</span></span> <span data-ttu-id="ed145-499">記錄服務界限上的事件。</span><span class="sxs-lookup"><span data-stu-id="ed145-499">Log events at service boundaries.</span></span>

### <a name="vm-instance-becomes-unavailable-or-unhealthy"></a><span data-ttu-id="ed145-500">VM 執行個體變得無法使用或狀況不良。</span><span class="sxs-lookup"><span data-stu-id="ed145-500">VM instance becomes unavailable or unhealthy.</span></span>
<span data-ttu-id="ed145-501">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-501">**Detection**.</span></span> <span data-ttu-id="ed145-502">設定負載平衡器[健康情況探查][lb-probe]，以指出 VM 執行個體是否狀況良好。</span><span class="sxs-lookup"><span data-stu-id="ed145-502">Configure a Load Balancer [health probe][lb-probe] that signals whether the VM instance is healthy.</span></span> <span data-ttu-id="ed145-503">探查應該檢查重大功能是否會正確回應。</span><span class="sxs-lookup"><span data-stu-id="ed145-503">The probe should check whether critical functions are responding correctly.</span></span>

<span data-ttu-id="ed145-504">**復原**。</span><span class="sxs-lookup"><span data-stu-id="ed145-504">**Recovery**.</span></span> <span data-ttu-id="ed145-505">對每個應用程式層，在相同的可用性設定組內放入多個 VM 執行個體，並在 VM 前放置負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="ed145-505">For each application tier, put multiple VM instances into the same availability set, and place a load balancer in front of the VMs.</span></span> <span data-ttu-id="ed145-506">如果健康情況探查失敗，負載平衡器會停止傳送新的連線至狀況不良的執行個體。</span><span class="sxs-lookup"><span data-stu-id="ed145-506">If the health probe fails, the Load Balancer stops sending new connections to the unhealthy instance.</span></span>

<span data-ttu-id="ed145-507">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-507">**Diagnostics**.</span></span> <span data-ttu-id="ed145-508">- 使用負載平衡器[記錄分析][lb-monitor]。</span><span class="sxs-lookup"><span data-stu-id="ed145-508">- Use Load Balancer [log analytics][lb-monitor].</span></span>

* <span data-ttu-id="ed145-509">設定您的監視系統來監視所有的健康情況監視端點。</span><span class="sxs-lookup"><span data-stu-id="ed145-509">Configure your monitoring system to monitor all of the health monitoring endpoints.</span></span>

### <a name="operator-accidentally-shuts-down-a-vm"></a><span data-ttu-id="ed145-510">操作員不小心關閉 VM。</span><span class="sxs-lookup"><span data-stu-id="ed145-510">Operator accidentally shuts down a VM.</span></span>
<span data-ttu-id="ed145-511">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-511">**Detection**.</span></span> <span data-ttu-id="ed145-512">N/A</span><span class="sxs-lookup"><span data-stu-id="ed145-512">N/A</span></span>

<span data-ttu-id="ed145-513">**復原**。</span><span class="sxs-lookup"><span data-stu-id="ed145-513">**Recovery**.</span></span> <span data-ttu-id="ed145-514">使用 `ReadOnly` 層級設定資源鎖定。</span><span class="sxs-lookup"><span data-stu-id="ed145-514">Set a resource lock with `ReadOnly` level.</span></span> <span data-ttu-id="ed145-515">請參閱[使用 Azure Resource Manager 鎖定資源][rm-locks]。</span><span class="sxs-lookup"><span data-stu-id="ed145-515">See [Lock resources with Azure Resource Manager][rm-locks].</span></span>

<span data-ttu-id="ed145-516">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-516">**Diagnostics**.</span></span> <span data-ttu-id="ed145-517">使用 [Azure 活動記錄][azure-activity-logs]。</span><span class="sxs-lookup"><span data-stu-id="ed145-517">Use [Azure Activity Logs][azure-activity-logs].</span></span>

## <a name="webjobs"></a><span data-ttu-id="ed145-518">WebJobs</span><span class="sxs-lookup"><span data-stu-id="ed145-518">WebJobs</span></span>
### <a name="continuous-job-stops-running-when-the-scm-host-is-idle"></a><span data-ttu-id="ed145-519">SCM 主機閒置時，連續作業停止執行。</span><span class="sxs-lookup"><span data-stu-id="ed145-519">Continuous job stops running when the SCM host is idle.</span></span>
<span data-ttu-id="ed145-520">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-520">**Detection**.</span></span> <span data-ttu-id="ed145-521">將取消權杖傳遞給 WebJob 函式。</span><span class="sxs-lookup"><span data-stu-id="ed145-521">Pass a cancellation token to the WebJob function.</span></span> <span data-ttu-id="ed145-522">如需詳細資訊，請參閱[正常關機][web-jobs-shutdown]。</span><span class="sxs-lookup"><span data-stu-id="ed145-522">For more information, see [Graceful shutdown][web-jobs-shutdown].</span></span>

<span data-ttu-id="ed145-523">**復原**。</span><span class="sxs-lookup"><span data-stu-id="ed145-523">**Recovery**.</span></span> <span data-ttu-id="ed145-524">在 Web 應用程式中啟用 `Always On` 設定。</span><span class="sxs-lookup"><span data-stu-id="ed145-524">Enable the `Always On` setting in the web app.</span></span> <span data-ttu-id="ed145-525">如需詳細資訊，請參閱[使用 WebJobs 執行背景工作][web-jobs]。</span><span class="sxs-lookup"><span data-stu-id="ed145-525">For more information, see [Run Background tasks with WebJobs][web-jobs].</span></span>

## <a name="application-design"></a><span data-ttu-id="ed145-526">應用程式設計</span><span class="sxs-lookup"><span data-stu-id="ed145-526">Application design</span></span>
### <a name="application-cant-handle-a-spike-in-incoming-requests"></a><span data-ttu-id="ed145-527">應用程式無法處理內送要求突然增加。</span><span class="sxs-lookup"><span data-stu-id="ed145-527">Application can't handle a spike in incoming requests.</span></span>
<span data-ttu-id="ed145-528">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-528">**Detection**.</span></span> <span data-ttu-id="ed145-529">視應用程式而定。</span><span class="sxs-lookup"><span data-stu-id="ed145-529">Depends on the application.</span></span> <span data-ttu-id="ed145-530">一般徵兆：</span><span class="sxs-lookup"><span data-stu-id="ed145-530">Typical symptoms:</span></span>

* <span data-ttu-id="ed145-531">網站開始傳回 HTTP 5xx 錯誤碼。</span><span class="sxs-lookup"><span data-stu-id="ed145-531">The website starts returning HTTP 5xx error codes.</span></span>
* <span data-ttu-id="ed145-532">相依服務 (例如資料庫或儲存體) 開始對要求進行節流。</span><span class="sxs-lookup"><span data-stu-id="ed145-532">Dependent services, such as database or storage, start to throttle requests.</span></span> <span data-ttu-id="ed145-533">根據服務尋找 HTTP 錯誤，例如 HTTP 429 (太多要求)。</span><span class="sxs-lookup"><span data-stu-id="ed145-533">Look for HTTP errors such as HTTP 429 (Too Many Requests), depending on the service.</span></span>
* <span data-ttu-id="ed145-534">HTTP 佇列長度增加。</span><span class="sxs-lookup"><span data-stu-id="ed145-534">HTTP queue length grows.</span></span>

<span data-ttu-id="ed145-535">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-535">**Recovery**</span></span>

* <span data-ttu-id="ed145-536">相應放大以處理增加的負載。</span><span class="sxs-lookup"><span data-stu-id="ed145-536">Scale out to handle increased load.</span></span>
* <span data-ttu-id="ed145-537">降低失敗風險，以免連鎖性失敗中斷整個應用程式。</span><span class="sxs-lookup"><span data-stu-id="ed145-537">Mitigate failures to avoid having cascading failures disrupt the entire application.</span></span> <span data-ttu-id="ed145-538">風險降低策略包括：</span><span class="sxs-lookup"><span data-stu-id="ed145-538">Mitigation strategies include:</span></span>

  * <span data-ttu-id="ed145-539">實作[節流模式][throttling-pattern]以免壓垮後端系統。</span><span class="sxs-lookup"><span data-stu-id="ed145-539">Implement the [Throttling Pattern][throttling-pattern] to avoid overwhelming backend systems.</span></span>
  * <span data-ttu-id="ed145-540">使用[佇列型負載調節][queue-based-load-leveling]為要求提供緩衝，並以適當步調處理這些要求。</span><span class="sxs-lookup"><span data-stu-id="ed145-540">Use [queue-based load leveling][queue-based-load-leveling] to buffer requests and process them at an appropriate pace.</span></span>
  * <span data-ttu-id="ed145-541">設定特定用戶端的優先順序。</span><span class="sxs-lookup"><span data-stu-id="ed145-541">Prioritize certain clients.</span></span> <span data-ttu-id="ed145-542">例如，如果應用程式有免費層與付費層，對免費層的客戶節流，但不要對付費客戶節流。</span><span class="sxs-lookup"><span data-stu-id="ed145-542">For example, if the application has free and paid tiers, throttle customers on the free tier, but not paid customers.</span></span> <span data-ttu-id="ed145-543">請參閱[優先順序佇列模式][priority-queue-pattern]。</span><span class="sxs-lookup"><span data-stu-id="ed145-543">See [Priority queue pattern][priority-queue-pattern].</span></span>

<span data-ttu-id="ed145-544">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-544">**Diagnostics**.</span></span> <span data-ttu-id="ed145-545">使用 [App Service 診斷記錄][app-service-logging]。</span><span class="sxs-lookup"><span data-stu-id="ed145-545">Use [App Service diagnostic logging][app-service-logging].</span></span> <span data-ttu-id="ed145-546">使用 [Azure Log Analytics][azure-log-analytics]、[Application Insights][app-insights] 或 [New Relic][new-relic] 等服務，以協助了解診斷記錄。</span><span class="sxs-lookup"><span data-stu-id="ed145-546">Use a service such as [Azure Log Analytics][azure-log-analytics], [Application Insights][app-insights], or [New Relic][new-relic] to help understand the diagnostic logs.</span></span>

### <a name="one-of-the-operations-in-a-workflow-or-distributed-transaction-fails"></a><span data-ttu-id="ed145-547">工作流程或分散式交易中的其中一個作業失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-547">One of the operations in a workflow or distributed transaction fails.</span></span>
<span data-ttu-id="ed145-548">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-548">**Detection**.</span></span> <span data-ttu-id="ed145-549">嘗試 N 次重試之後，作業仍失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-549">After *N* retry attempts, it still fails.</span></span>

<span data-ttu-id="ed145-550">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-550">**Recovery**</span></span>

* <span data-ttu-id="ed145-551">作為風險降低計畫，請實作[排程器代理程式監督員][scheduler-agent-supervisor]模式以管理整個工作流程。</span><span class="sxs-lookup"><span data-stu-id="ed145-551">As a mitigation plan, implement the [Scheduler Agent Supervisor][scheduler-agent-supervisor] pattern to manage the entire workflow.</span></span>
* <span data-ttu-id="ed145-552">請不要在逾時的時候重試。</span><span class="sxs-lookup"><span data-stu-id="ed145-552">Don't retry on timeouts.</span></span> <span data-ttu-id="ed145-553">此錯誤的成功率很低。</span><span class="sxs-lookup"><span data-stu-id="ed145-553">There is a low success rate for this error.</span></span>
* <span data-ttu-id="ed145-554">將工作排入佇列，以便稍後重試。</span><span class="sxs-lookup"><span data-stu-id="ed145-554">Queue work, in order to retry later.</span></span>

<span data-ttu-id="ed145-555">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-555">**Diagnostics**.</span></span> <span data-ttu-id="ed145-556">記錄所有作業 (成功與失敗)，包括補償動作。</span><span class="sxs-lookup"><span data-stu-id="ed145-556">Log all operations (successful and failed), including compensating actions.</span></span> <span data-ttu-id="ed145-557">使用相互關聯識別碼，以便追蹤相同交易內的所有作業。</span><span class="sxs-lookup"><span data-stu-id="ed145-557">Use correlation IDs, so that you can track all operations within the same transaction.</span></span>

### <a name="a-call-to-a-remote-service-fails"></a><span data-ttu-id="ed145-558">遠端服務的呼叫失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-558">A call to a remote service fails.</span></span>
<span data-ttu-id="ed145-559">**偵測**。</span><span class="sxs-lookup"><span data-stu-id="ed145-559">**Detection**.</span></span> <span data-ttu-id="ed145-560">HTTP 錯誤碼。</span><span class="sxs-lookup"><span data-stu-id="ed145-560">HTTP error code.</span></span>

<span data-ttu-id="ed145-561">**復原**</span><span class="sxs-lookup"><span data-stu-id="ed145-561">**Recovery**</span></span>

1. <span data-ttu-id="ed145-562">重試暫時性失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-562">Retry on transient failures.</span></span>
2. <span data-ttu-id="ed145-563">如果呼叫在嘗試 N 次之後失敗，請採取遞補動作。</span><span class="sxs-lookup"><span data-stu-id="ed145-563">If the call fails after *N* attempts, take a fallback action.</span></span> <span data-ttu-id="ed145-564">(應用程式所特有)。</span><span class="sxs-lookup"><span data-stu-id="ed145-564">(Application specific.)</span></span>
3. <span data-ttu-id="ed145-565">實作[斷路器模式][circuit-breaker]，以免發生連鎖性失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-565">Implement the [Circuit Breaker pattern][circuit-breaker] to avoid cascading failures.</span></span>

<span data-ttu-id="ed145-566">**診斷**。</span><span class="sxs-lookup"><span data-stu-id="ed145-566">**Diagnostics**.</span></span> <span data-ttu-id="ed145-567">記錄所有遠端呼叫的失敗。</span><span class="sxs-lookup"><span data-stu-id="ed145-567">Log all remote call failures.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ed145-568">後續步驟</span><span class="sxs-lookup"><span data-stu-id="ed145-568">Next steps</span></span>
<span data-ttu-id="ed145-569">如需 FMA 程序的詳細資訊，請參閱[雲端服務原本就有的復原功能設計][resilience-by-design-pdf] (PDF 下載)。</span><span class="sxs-lookup"><span data-stu-id="ed145-569">For more information about the FMA process, see [Resilience by design for cloud services][resilience-by-design-pdf] (PDF download).</span></span>

<!-- links -->

[api-management]: https://azure.microsoft.com/documentation/services/api-management/
[api-management-throttling]: /azure/api-management/api-management-sample-flexible-throttling/
[app-insights]: /azure/application-insights/app-insights-overview/
[app-insights-web-apps]: /azure/application-insights/app-insights-azure-web-apps/
[app-service-configure]: /azure/app-service-web/web-sites-configure/
[app-service-logging]: /azure/app-service-web/web-sites-enable-diagnostic-log/
[app-service-slots]: /azure/app-service-web/web-sites-staged-publishing/
[auto-rest-client-retry]: https://github.com/Azure/autorest/tree/master/docs
[azure-activity-logs]: /azure/monitoring-and-diagnostics/monitoring-overview-activity-logs/
[azure-alerts]: /azure/monitoring-and-diagnostics/insights-alerts-portal/
[azure-log-analytics]: /azure/log-analytics/log-analytics-overview/
[BrokeredMessage.TimeToLive]: https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
[cassandra-error-handling]: https://www.datastax.com/dev/blog/cassandra-error-handling-done-right
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[cosmosdb-multi-region]: /azure/cosmos-db/tutorial-global-distribution-sql-api
[elasticsearch-azure]: ../elasticsearch/index.md
[elasticsearch-client]: https://www.elastic.co/guide/en/elasticsearch/client/index.html
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[onstop-events]: https://azure.microsoft.com/blog/the-right-way-to-handle-azure-onstop-events/
[lb-monitor]: /azure/load-balancer/load-balancer-monitor-log/
[lb-probe]: /azure/load-balancer/load-balancer-custom-probe-overview/#learn-about-the-types-of-probes
[new-relic]: https://newrelic.com/
[priority-queue-pattern]: https://msdn.microsoft.com/library/dn589794.aspx
[queue-based-load-leveling]: https://msdn.microsoft.com/library/dn589783.aspx
[QuotaExceededException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx
[ra-web-apps-basic]: ../reference-architectures/app-service-web-app/basic-web-app.md
[redis-monitor]: /azure/redis-cache/cache-how-to-monitor/
[redis-retry]: ../best-practices/retry-service-specific.md#azure-redis-cache
[resilience-by-design-pdf]: https://download.microsoft.com/download/D/8/C/D8C599A4-4E8A-49BF-80EE-FE35F49B914D/Resilience_by_Design_for_Cloud_Services_White_Paper.pdf
[RoleEntryPoint.OnStop]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[RoleEnvironment.Stopping]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx
[rm-locks]: /azure/azure-resource-manager/resource-group-lock-resources/
[sb-dead-letter-queue]: /azure/service-bus-messaging/service-bus-dead-letter-queues/
[sb-georeplication-sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/GeoReplication
[sb-messagingexception-class]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
[sb-messaging-exceptions]: /azure/service-bus-messaging/service-bus-messaging-exceptions/
[sb-outages]: /azure/service-bus-messaging/service-bus-outages-disasters/#protecting-queues-and-topics-against-datacenter-outages-or-disasters
[sb-partition]: /azure/service-bus-messaging/service-bus-partitioning/
[sb-poison-message]: /azure/app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to/#poison
[sb-retry]: ../best-practices/retry-service-specific.md#service-bus
[search-sdk]: https://msdn.microsoft.com/library/dn951165.aspx
[scheduler-agent-supervisor]: https://msdn.microsoft.com/library/dn589780.aspx
[search-analytics]: /azure/search/search-traffic-analytics/
[sql-db-audit]: /azure/sql-database/sql-database-auditing-get-started/
[sql-db-errors]: /azure/sql-database/sql-database-develop-error-messages/#resource-governance-errors
[sql-db-failover]: /azure/sql-database/sql-database-geo-replication-failover-portal/
[sql-db-limits]: /azure/sql-database/sql-database-resource-limits/
[sql-db-replication]: /azure/sql-database/sql-database-geo-replication-overview/
[storage-metrics]: https://msdn.microsoft.com/library/dn782843.aspx
[storage-replication]: /azure/storage/storage-redundancy/
[Storage.RetryPolicies]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.aspx
[sys.event_log]: https://msdn.microsoft.com/library/dn270018.aspx
[throttling-pattern]: https://msdn.microsoft.com/library/dn589798.aspx
[web-jobs]: /azure/app-service-web/web-sites-create-web-jobs/
[web-jobs-shutdown]: /azure/app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to/#graceful

---
title: 簡單的企業整合架構模式 - Azure Integration Services
description: 此架構參考示範如何使用 Azure Logic Apps 和 Azure API 管理來實作簡單的企業整合模式
services: logic-apps
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/25/2018
ms.openlocfilehash: 15349aad4e7bc1a13337259fc933e1e11e252747
ms.sourcegitcommit: 2ae794de13c45cf24ad60d4f4dbb193c25944eff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50003577"
---
# <a name="simple-enterprise-integration"></a><span data-ttu-id="5c5f5-103">簡單的企業整合</span><span class="sxs-lookup"><span data-stu-id="5c5f5-103">Simple enterprise integration</span></span>

<span data-ttu-id="5c5f5-104">此參考架構使用 [Azure Integration Services][integration-services] 協調企業後端系統的呼叫。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-104">This reference architecture uses [Azure Integration Services][integration-services] to orchestrate calls to enterprise backend systems.</span></span> <span data-ttu-id="5c5f5-105">後端系統可能包括軟體即服務 (SaaS) 系統、Azure 服務，以及企業中的現有 Web 服務。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-105">The backend systems may include software as a service (SaaS) systems, Azure services, and existing web services in your enterprise.</span></span>

<span data-ttu-id="5c5f5-106">Azure Integration Services 是整合應用程式和資料的服務集合。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-106">Azure Integration Services is a collection of services for integrating applications and data.</span></span> <span data-ttu-id="5c5f5-107">此架構使用其中兩個服務：[Logic Apps][logic-apps] 用來協調工作流程，[API 管理][apim]用來建立 API 目錄。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-107">This architecture uses two of those services: [Logic Apps][logic-apps] to orchestrate workflows, and [API Management][apim] to create catalogs of APIs.</span></span>

![架構圖 - 簡單的企業整合](./_images/simple-enterprise-integration.png)

## <a name="architecture"></a><span data-ttu-id="5c5f5-109">架構</span><span class="sxs-lookup"><span data-stu-id="5c5f5-109">Architecture</span></span>

<span data-ttu-id="5c5f5-110">此架構具有下列元件：</span><span class="sxs-lookup"><span data-stu-id="5c5f5-110">The architecture has the following components:</span></span>

- <span data-ttu-id="5c5f5-111">**後端系統**。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-111">**Backend systems**.</span></span> <span data-ttu-id="5c5f5-112">圖表右側是企業已部署或依賴的各種後端系統。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-112">On the right-hand side of the diagram are the various backend systems that the enterprise has deployed or relies on.</span></span> <span data-ttu-id="5c5f5-113">這些可能包括 SaaS 系統、其他 Azure 服務，或者會公開 REST 或 SOAP 端點的 Web 服務。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-113">These might include SaaS systems, other Azure services, or web services that expose REST or SOAP endpoints.</span></span>

- <span data-ttu-id="5c5f5-114">**Azure Logic Apps**。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-114">**Azure Logic Apps**.</span></span> <span data-ttu-id="5c5f5-115">[Logic Apps][logic-apps] 是一個無伺服器平台，用於建置可整合應用程式、資料和服務的企業工作流程。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-115">[Logic Apps][logic-apps] is a serverless platform for building enterprise workflows that integrate applications, data, and services.</span></span> <span data-ttu-id="5c5f5-116">在此架構中，邏輯應用程式會由 HTTP 要求觸發。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-116">In this architecture, the logic apps are triggered by HTTP requests.</span></span> <span data-ttu-id="5c5f5-117">您也可以為更複雜的協調流程巢狀處理工作流程。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-117">You can also nest workflows for more complex orchestration.</span></span> <span data-ttu-id="5c5f5-118">Logic Apps 會使用[連接器][logic-apps-connectors]來整合常用的服務。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-118">Logic Apps uses [connectors][logic-apps-connectors] to integrate with commonly used services.</span></span> <span data-ttu-id="5c5f5-119">Logic Apps 提供數百個連接器，且您可以建立自訂連接器。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-119">Logic Apps offers hundreds of connectors, and you can create custom connectors.</span></span>

- <span data-ttu-id="5c5f5-120">**Azure API 管理**。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-120">**Azure API Management**.</span></span> <span data-ttu-id="5c5f5-121">[API 管理][apim]是受控服務，用於發佈 HTTP API 的目錄，以促進重複使用及可搜尋性。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-121">[API Management][apim] is a managed service for publishing catalogs of HTTP APIs, to promote re-use and discoverability.</span></span> <span data-ttu-id="5c5f5-122">API 管理由兩個相關元件組成：</span><span class="sxs-lookup"><span data-stu-id="5c5f5-122">API Management consists of two related components:</span></span>

    - <span data-ttu-id="5c5f5-123">**API 閘道**。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-123">**API gateway**.</span></span> <span data-ttu-id="5c5f5-124">API 閘道可接受 HTTP 呼叫，並將它們路由傳送至後端。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-124">The API gateway accepts HTTP calls and routes them to the backend.</span></span> 

    - <span data-ttu-id="5c5f5-125">**開發人員入口網站**。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-125">**Developer portal**.</span></span> <span data-ttu-id="5c5f5-126">Azure API 管理的每個執行個體都提供[開發人員入口網站][apim-dev-portal]的存取權。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-126">Each instance of Azure API Management provides access to a [developer portal][apim-dev-portal].</span></span> <span data-ttu-id="5c5f5-127">此入口網站可讓開發人員存取呼叫 API 的文件和程式碼範例。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-127">This portal gives your developers access to documentation and code samples for calling the APIs.</span></span> <span data-ttu-id="5c5f5-128">您也可以在開發人員入口網站中測試 API。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-128">You can also test APIs in the developer portal.</span></span>

    <span data-ttu-id="5c5f5-129">在此架構中，複合 API 是藉由[匯入邏輯應用程式][apim-logic-app]為 API 所建立。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-129">In this architecture, composite APIs are built by [importing logic apps][apim-logic-app] as APIs.</span></span> <span data-ttu-id="5c5f5-130">您也可以藉由[匯入 OpenAPI][apim-openapi] (Swagger) 規格或從 WSDL 規格[匯入 SOAP API][apim-soap] 來匯入現有的 Web 服務。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-130">You can also import existing web services by [importing OpenAPI][apim-openapi] (Swagger) specifications or [importing SOAP APIs][apim-soap] from WSDL specifications.</span></span> 

    <span data-ttu-id="5c5f5-131">API 閘道有助於分離前端用戶端與後端。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-131">The API gateway helps to decouple front-end clients from the back end.</span></span> <span data-ttu-id="5c5f5-132">比方說，它可以重新撰寫 URL，或在其到達後端前轉換要求。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-132">For example, it can rewrite URLs, or transform requests before they reach the backend.</span></span> <span data-ttu-id="5c5f5-133">它也可處理許多跨領域問題，例如驗證、跨原始來源資源共用 (CORS) 支援，以及回應快取。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-133">It also handles many cross-cutting concerns such as authentication, cross-origin resource sharing (CORS) support, and response caching.</span></span>

- <span data-ttu-id="5c5f5-134">**Azure DNS**。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-134">**Azure DNS**.</span></span> <span data-ttu-id="5c5f5-135">[Azure DNS][dns] 是適用於 DNS 網域的主機服務。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-135">[Azure DNS][dns] is a hosting service for DNS domains.</span></span> <span data-ttu-id="5c5f5-136">Azure DNS 採用 Microsoft Azure 基礎結構來提供名稱解析。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-136">Azure DNS provides name resolution by using the Microsoft Azure infrastructure.</span></span> <span data-ttu-id="5c5f5-137">只要將您的網域裝載於 Azure，就可以使用您用於其他 Azure 服務的相同認證、API、工具和計費方式來管理 DNS 記錄。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-137">By hosting your domains in Azure, you can manage your DNS records by using the same credentials, APIs, tools, and billing that you use for your other Azure services.</span></span> <span data-ttu-id="5c5f5-138">若要使用自訂網域名稱 (例如 contoso.com)，請建立將自訂網域名稱對應至 IP 位址的 DNS 記錄。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-138">To use a custom domain name, such as contoso.com, create DNS records that map the custom domain name to the IP address.</span></span> <span data-ttu-id="5c5f5-139">如需詳細資訊，請參閱[在 API 管理中設定自訂網域名稱][apim-domain]。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-139">For more information, see [Configure a custom domain name in API Management][apim-domain].</span></span>

- <span data-ttu-id="5c5f5-140">**Azure Active Directory (Azure AD)**。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-140">**Azure Active Directory (Azure AD)**.</span></span> <span data-ttu-id="5c5f5-141">使用 [Azure AD][aad] 來驗證呼叫 API 閘道的用戶端。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-141">Use [Azure AD][aad] to authenticate clients that call the API gateway.</span></span> <span data-ttu-id="5c5f5-142">Azure AD 可支援 OpenID Connect (OIDC) 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-142">Azure AD supports the OpenID Connect (OIDC) protocol.</span></span> <span data-ttu-id="5c5f5-143">用戶端從 Azure AD 取得存取權杖，API 閘道會[驗證權杖][apim-jwt]以授權要求。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-143">Clients obtain an access token from Azure AD, and API Gateway [validates the token][apim-jwt] to authorize the request.</span></span> <span data-ttu-id="5c5f5-144">當使用 API 管理的標準或進階層，Azure AD 也可以保護開發人員入口網站的存取。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-144">When using the Standard or Premium tier of API Management, Azure AD can also secure access to the developer portal.</span></span>

## <a name="recommendations"></a><span data-ttu-id="5c5f5-145">建議</span><span class="sxs-lookup"><span data-stu-id="5c5f5-145">Recommendations</span></span>

<span data-ttu-id="5c5f5-146">您的特定需求可能與此處所示的一般架構不同。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-146">Your specific requirements might differ from the generic architecture shown here.</span></span> <span data-ttu-id="5c5f5-147">以本節的建議作為起點。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-147">Use the recommendations in this section as a starting point.</span></span>

### <a name="api-management"></a><span data-ttu-id="5c5f5-148">API 管理</span><span class="sxs-lookup"><span data-stu-id="5c5f5-148">API Management</span></span>

<span data-ttu-id="5c5f5-149">使用 API 管理的基本、標準或進階層。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-149">Use the API Management Basic, Standard, or Premium tiers.</span></span> <span data-ttu-id="5c5f5-150">這些層會提供生產環境服務等級協定 (SLA)，並支援在 Azure 區域內相應放大規模。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-150">These tiers offer a production service level agreement (SLA) and support scale out within the Azure region.</span></span> <span data-ttu-id="5c5f5-151">API 管理的輸送量容量以*單位*進行測量。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-151">Throughput capacity for API Management is measured in *units*.</span></span> <span data-ttu-id="5c5f5-152">每個定價層都有相應放大規模的上限。進階層也支援跨多個 Azure 區域相應放大規模。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-152">Each pricing tier has a maximum scale out. The Premium tier also supports scale out across multiple Azure regions.</span></span> <span data-ttu-id="5c5f5-153">根據您的功能集和必要輸送量的層級，選擇您的階層。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-153">Choose your tier based on your feature set and the level of required throughput.</span></span> <span data-ttu-id="5c5f5-154">如需詳細資訊，請參閱 [API 管理價格][apim-pricing]與 [Azure API 管理執行個體的容量][apim-capacity]。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-154">For more information, see [API Management pricing][apim-pricing] and [Capacity of an Azure API Management instance][apim-capacity].</span></span>

<span data-ttu-id="5c5f5-155">每個 Azure API 管理執行個體都有預設網域名稱，也就是 `azure-api.net` &mdash 的子網域，例如 `contoso.azure-api.net`。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-155">Each Azure API Management instance has a default domain name, which is a subdomain of `azure-api.net` &mdash for example, `contoso.azure-api.net`.</span></span> <span data-ttu-id="5c5f5-156">請考慮為您的組織設定[自訂網域][apim-domain]。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-156">Consider configuring a [custom domain][apim-domain] for your organization.</span></span>

### <a name="logic-apps"></a><span data-ttu-id="5c5f5-157">Logic Apps</span><span class="sxs-lookup"><span data-stu-id="5c5f5-157">Logic Apps</span></span> 

<span data-ttu-id="5c5f5-158">Logic Apps 在不需要低延遲的情況下運作得最好。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-158">Logic Apps works best in scenarios that don't require low latency.</span></span> <span data-ttu-id="5c5f5-159">例如，Logic Apps 最適合非同步或執行時間稍長的 API 呼叫。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-159">For example, Logic Apps works best for asynchronous or semi long-running API calls.</span></span> <span data-ttu-id="5c5f5-160">如果需要低延遲 (例如，會封鎖使用者介面的呼叫)，請使用不同的技術來實作您的 API 或作業。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-160">If low latency is required, for example, a call that blocks a user interface, implement your API or operation by using a different technology.</span></span> <span data-ttu-id="5c5f5-161">例如，使用 Azure Functions，或者以 App Service 來部署的 Web API。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-161">For example, use Azure Functions or a Web API that you deploy by using Azure App Service.</span></span> <span data-ttu-id="5c5f5-162">使用 API 管理讓您的 API 朝向您的 API 取用者。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-162">Use API Management to front the API to your API consumers.</span></span>

### <a name="region"></a><span data-ttu-id="5c5f5-163">區域</span><span class="sxs-lookup"><span data-stu-id="5c5f5-163">Region</span></span>

<span data-ttu-id="5c5f5-164">若要將網路延遲降到最低，將「API 管理」和 Logic Apps 放在相同區域中。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-164">To minimize network latency, put API Management and Logic Apps in the same region.</span></span> <span data-ttu-id="5c5f5-165">通常會選擇最接近使用者的區域 (或最接近後端服務)。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-165">In general, choose the region that's closest to your users (or closest to your backend services).</span></span>

<span data-ttu-id="5c5f5-166">資源群組也有區域。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-166">The resource group also has a region.</span></span> <span data-ttu-id="5c5f5-167">此區域會指定部署中繼資料的儲存位置，以及部署範本的執行位置。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-167">This region specifies where to store deployment metadata and where to execute the deployment template.</span></span> <span data-ttu-id="5c5f5-168">若要改善部署期間的可用性，請將資源群組和資源放在相同區域。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-168">To improve availability during deployment, put the resource group and resources in the same region.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="5c5f5-169">延展性考量</span><span class="sxs-lookup"><span data-stu-id="5c5f5-169">Scalability considerations</span></span>

<span data-ttu-id="5c5f5-170">若要提升 API 管理的延展性，請視需要新增[快取原則][apim-caching]。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-170">To increase the scalability of API Management, add [caching policies][apim-caching] where appropriate.</span></span> <span data-ttu-id="5c5f5-171">快取也有助於降低後端服務的負載。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-171">Caching also helps reduce the load on back-end services.</span></span>

<span data-ttu-id="5c5f5-172">若要提供更大的容量，您可以在 Azure 區域中相應放大 Azure API 管理的基本、標準及進階層。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-172">To offer greater capacity, you can scale out Azure API Management Basic, Standard, and Premium tiers in an Azure region.</span></span> <span data-ttu-id="5c5f5-173">若要分析服務的使用情況，請在 [計量] 功能表上選取 [容量計量] 選項，然後視需要相應增加或相應減少規模。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-173">To analyze the usage for your service, on the **Metrics** menu, select the **Capacity Metric** option and then scale up or scale down as appropriate.</span></span> <span data-ttu-id="5c5f5-174">升級或調整程序需要 15 到 45 分鐘才會生效。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-174">The upgrade or scale process can take from 15 to 45 minutes to apply.</span></span>

<span data-ttu-id="5c5f5-175">調整「API 管理」服務規模的建議事項：</span><span class="sxs-lookup"><span data-stu-id="5c5f5-175">Recommendations for scaling an API Management service:</span></span>

- <span data-ttu-id="5c5f5-176">請在調整時考慮流量模式。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-176">Consider traffic patterns when scaling.</span></span> <span data-ttu-id="5c5f5-177">流量模式變動較大的客戶需要更多容量。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-177">Customers with more volatile traffic patterns need more capacity.</span></span>

- <span data-ttu-id="5c5f5-178">容量如果一直大於 66%，可能代表需要相應增加規模。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-178">Consistent capacity that's greater than 66% might indicate a need to scale up.</span></span>

- <span data-ttu-id="5c5f5-179">容量如果一直低於 20%，則可能代表需要相應減少規模。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-179">Consistent capacity that's under 20% might indicate an opportunity to scale down.</span></span>

- <span data-ttu-id="5c5f5-180">在生產環境中啟用負載前，一律先以代表性負載進行 API 管理服務負載測試。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-180">Before you enable the load in production, always load-test your API Management service with a representative load.</span></span>

<span data-ttu-id="5c5f5-181">使用進階層，您可以跨多個 Azure 區域調整 API 管理執行個體。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-181">With the Premium tier, you can scale an API Management instance across multiple Azure regions.</span></span> <span data-ttu-id="5c5f5-182">這讓 API 管理可適用於更高的 SLA，並讓您在使用者附近多個區域中佈建服務。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-182">This makes API Management eligible for a higher SLA, and lets you provision services near users in multiple regions.</span></span>


<span data-ttu-id="5c5f5-183">Logic Apps 無伺服器模型代表系統管理員不需要針對服務延展性進行規劃。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-183">The Logic Apps serverless model means administrators don't have to plan for service scalability.</span></span> <span data-ttu-id="5c5f5-184">服務會自動調整規模以滿足需求。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-184">The service automatically scales to meet demand.</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="5c5f5-185">可用性考量</span><span class="sxs-lookup"><span data-stu-id="5c5f5-185">Availability considerations</span></span>

<span data-ttu-id="5c5f5-186">檢閱每個服務的 SLA：</span><span class="sxs-lookup"><span data-stu-id="5c5f5-186">Review the SLA for each service:</span></span>

- <span data-ttu-id="5c5f5-187">[API 管理 SLA][apim-sla]</span><span class="sxs-lookup"><span data-stu-id="5c5f5-187">[API Management SLA][apim-sla]</span></span>
- <span data-ttu-id="5c5f5-188">[Logic Apps SLA][logic-apps-sla]</span><span class="sxs-lookup"><span data-stu-id="5c5f5-188">[Logic Apps SLA][logic-apps-sla]</span></span>

<span data-ttu-id="5c5f5-189">如果使用進階層跨二或多個區域部署 API 管理，即適用於較高的 SLA。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-189">If deploy API Management across two or more regions with Premium tier, it is eligible for a higher SLA.</span></span> <span data-ttu-id="5c5f5-190">請參閱 [API 管理定價][apim-pricing]。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-190">See [API Management pricing][apim-pricing].</span></span>

### <a name="backups"></a><span data-ttu-id="5c5f5-191">備份</span><span class="sxs-lookup"><span data-stu-id="5c5f5-191">Backups</span></span>

<span data-ttu-id="5c5f5-192">定期[備份][apim-backup] API 管理設定。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-192">Regularly [back up][apim-backup] your API Management configuration.</span></span> <span data-ttu-id="5c5f5-193">將備份檔案儲存在不同於服務所部署區域的位置或 Azure 區域中。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-193">Store your backup files in a location or Azure region that differs from the region where the service is deployed.</span></span> <span data-ttu-id="5c5f5-194">根據 [RTO][rto] 選擇災害復原策略：</span><span class="sxs-lookup"><span data-stu-id="5c5f5-194">Based on your [RTO][rto], choose a disaster recovery strategy:</span></span>

* <span data-ttu-id="5c5f5-195">在災害復原事件中，佈建新的 API 管理執行個體、將備份還原至新的執行個體，以及重新指向 DNS 記錄。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-195">In a disaster recovery event, provision a new API Management instance, restore the backup to the new instance, and repoint the DNS records.</span></span>

* <span data-ttu-id="5c5f5-196">將 API 管理服務的被動執行個體保留在其他 Azure 區域中。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-196">Keep a passive instance of the API Management service in another Azure region.</span></span> <span data-ttu-id="5c5f5-197">定期將備份還原到該執行個體，以讓它與作用中服務保持同步。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-197">Regularly restore backups to that instance, to keep it in sync with the active service.</span></span> <span data-ttu-id="5c5f5-198">若要在災害復原事件中還原服務，您只需重新指向 DNS 記錄。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-198">To restore the service during a disaster recovery event, you need only repoint the DNS records.</span></span> <span data-ttu-id="5c5f5-199">這種方法會產生額外成本，因為您需要支付被動的執行個體，但可以縮短復原時間。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-199">This approach incurs additional cost because you are paying for the passive instance, but reduces the time to recover.</span></span> 

<span data-ttu-id="5c5f5-200">對於邏輯應用程式，我們建議使用組態即程式碼的方法來備份及還原。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-200">For logic apps, we recommend a configuration-as-code approach to backup and restoring.</span></span> <span data-ttu-id="5c5f5-201">因為邏輯應用程式無伺服器，您可以從 Azure Resource Manager 範本快速進行重新建立。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-201">Because logic apps are serverless, you can quickly recreate them from Azure Resource Manager templates.</span></span> <span data-ttu-id="5c5f5-202">將範本儲存在原始檔控制中，整合範本與您的持續整合/持續部署 (CI/CD) 程序。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-202">Save the templates in source control, integrate the templates with your continuous integration/continuous deployment (CI/CD) process.</span></span> <span data-ttu-id="5c5f5-203">在災害復原事件中，將範本部署到新的區域。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-203">In a disaster recovery event, deploy the template to a new region.</span></span>

<span data-ttu-id="5c5f5-204">如果您將邏輯應用程式部署至不同的區域，請更新 API 管理中的組態。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-204">If you deploy a logic app to a different region, update the configuration in API Management.</span></span> <span data-ttu-id="5c5f5-205">您可以使用基本 PowerShell 指令碼來更新 API 的**後端**屬性。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-205">You can update the API's **Backend** property by using a basic PowerShell script.</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="5c5f5-206">管理性考量</span><span class="sxs-lookup"><span data-stu-id="5c5f5-206">Manageability considerations</span></span>

<span data-ttu-id="5c5f5-207">針對生產、部署和測試環境，各別建立資源群組。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-207">Create separate resource groups for production, development, and test environments.</span></span> <span data-ttu-id="5c5f5-208">個別的資源群組可讓您更輕鬆地管理部署、刪除測試部署及指派存取權限。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-208">Separate resource groups make it easier to manage deployments, delete test deployments, and assign access rights.</span></span>

<span data-ttu-id="5c5f5-209">當您將資源指派給資源群組時，請考慮下列因素：</span><span class="sxs-lookup"><span data-stu-id="5c5f5-209">When you assign resources to resource groups, consider these factors:</span></span>

* <span data-ttu-id="5c5f5-210">**生命週期**。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-210">**Lifecycle**.</span></span> <span data-ttu-id="5c5f5-211">一般來說，會將具有相同生命週期的資源放在同一個資源群組中。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-211">In general, put resources that have the same lifecycle in the same resource group.</span></span>

* <span data-ttu-id="5c5f5-212">**存取**。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-212">**Access**.</span></span> <span data-ttu-id="5c5f5-213">若要將存取原則套用至群組中的資源，您可以使用[角色型存取控制][rbac] (RBAC)。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-213">To apply access policies to the resources in a group, you can use [role-based access control][rbac] (RBAC).</span></span>

* <span data-ttu-id="5c5f5-214">**計費**。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-214">**Billing**.</span></span> <span data-ttu-id="5c5f5-215">您可以檢視資源群組的彙總成本。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-215">You can view rollup costs for the resource group.</span></span>

* <span data-ttu-id="5c5f5-216">**適用於 API 管理的定價層**。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-216">**Pricing tier for API Management**.</span></span> <span data-ttu-id="5c5f5-217">使用開發與測試環境的開發人員層。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-217">Use the Developer tier for development and test environments.</span></span> <span data-ttu-id="5c5f5-218">若要將成本降到最低，請部署生產環境的複本、執行測試，然後關閉。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-218">To minimize costs during preproduction, deploy a replica of your production environment, run your tests, and then shut down.</span></span>

### <a name="deployment"></a><span data-ttu-id="5c5f5-219">部署</span><span class="sxs-lookup"><span data-stu-id="5c5f5-219">Deployment</span></span>

<span data-ttu-id="5c5f5-220">使用 [Azure Resource Manager 範本][arm]來部署 Azure 資源。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-220">Use [Azure Resource Manager templates][arm] to deploy the Azure resources.</span></span> <span data-ttu-id="5c5f5-221">範本可讓您使用 PowerShell 或 Azure CLI，更輕鬆地自動執行部署。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-221">Templates make it easier to automate deployments using PowerShell or the Azure CLI.</span></span>

<span data-ttu-id="5c5f5-222">將 Azure API 管理及任何個別的邏輯應用程式放在其自己的個別 Resource Manager 範本中。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-222">Put API Management and any individual logic apps in their own separate Resource Manager templates.</span></span> <span data-ttu-id="5c5f5-223">使用不同的範本時，可以將資源儲存於原始檔控制系統中。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-223">By using separate templates, you can store the resources in source control systems.</span></span> <span data-ttu-id="5c5f5-224">您接著可以一起部署這些範本，或者在持續整合/持續部署 (CI/CD) 流程中個別部署這些範本。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-224">You can then deploy these templates together or individually as part of a continuous integration/continuous deployment (CI/CD) process.</span></span>

### <a name="versions"></a><span data-ttu-id="5c5f5-225">版本</span><span class="sxs-lookup"><span data-stu-id="5c5f5-225">Versions</span></span>

<span data-ttu-id="5c5f5-226">每次您變更邏輯應用程式的組態或透過 Resource Manager 範本部署更新時，Azure 都會保留該版本的複本，及保留所有具有執行歷程記錄的版本。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-226">Each time you change a logic app's configuration or deploy an update through a Resource Manager template, Azure keeps a copy of that version and keeps all versions that have a run history.</span></span> <span data-ttu-id="5c5f5-227">您可以使用這些版本來追蹤歷程記錄變更，或將某個版本升階成邏輯應用程式的目前組態。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-227">You can use these versions to track historical changes or promote a version as the logic app's current configuration.</span></span> <span data-ttu-id="5c5f5-228">例如，您可以將邏輯應用程式復原至先前的版本。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-228">For example, you can roll back a logic app to a previous version.</span></span>

<span data-ttu-id="5c5f5-229">「API 管理」支援兩個截然不同但互補的版本設定概念：</span><span class="sxs-lookup"><span data-stu-id="5c5f5-229">API Management supports two distinct but complementary versioning concepts:</span></span>

* <span data-ttu-id="5c5f5-230">*版本*可讓 API 取用者根據其需求選擇 API 版本，例如 v1、v2、搶鮮版 (Beta) 或生產環境版。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-230">*Versions* allow API consumers to choose an API version based on their needs, for example, v1, v2, beta, or production.</span></span>

* <span data-ttu-id="5c5f5-231">*修訂*允許 API 管理員在 API 中進行非中斷變更，並部署這些變更以及變更記錄，以便告知 API 取用者所做變更。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-231">*Revisions* allow API administrators to make non-breaking changes in an API and deploy those changes, along with a change log to inform API consumers about the changes.</span></span>

<span data-ttu-id="5c5f5-232">您可以使用 Resource Manager 範本，在開發環境中進行修訂，然後在其他環境中部署該變更。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-232">You can make a revision in a development environment and deploy that change in other environments by using Resource Manager templates.</span></span> <span data-ttu-id="5c5f5-233">如需詳細資訊，請參閱[發佈多個 API 版本][apim-versions]</span><span class="sxs-lookup"><span data-stu-id="5c5f5-233">For more information, see [Publish multiple versions of your API][apim-versions]</span></span>

<span data-ttu-id="5c5f5-234">您也可以在變更之前使用修訂測試目前使用者能夠存取的 API。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-234">You can also use revisions to test an API before making the changes current and accessible to users.</span></span> <span data-ttu-id="5c5f5-235">不過，這個方法不建議用於負載測試或整合測試。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-235">However, this method isn't recommended for load testing or integration testing.</span></span> <span data-ttu-id="5c5f5-236">請改用個別的測試或生產階段前環境。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-236">Instead, use separate test or preproduction environments.</span></span>

## <a name="diagnostics-and-monitoring"></a><span data-ttu-id="5c5f5-237">診斷和監控</span><span class="sxs-lookup"><span data-stu-id="5c5f5-237">Diagnostics and monitoring</span></span>

<span data-ttu-id="5c5f5-238">使用 [Azure 監視器][monitor]在 API 管理和 Logic Apps 中進行作業監視。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-238">Use [Azure Monitor][monitor] for operational monitoring in both API Management and Logic Apps.</span></span> <span data-ttu-id="5c5f5-239">Azure 監視器預設會啟用，並且會根據針對每個服務所設定的計量來提供資訊。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-239">Azure Monitor provides information based on the metrics configured for each service and is enabled by default.</span></span> <span data-ttu-id="5c5f5-240">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="5c5f5-240">For more information, see:</span></span>

- <span data-ttu-id="5c5f5-241">[監視已發佈的 API][apim-monitor]</span><span class="sxs-lookup"><span data-stu-id="5c5f5-241">[Monitor published APIs][apim-monitor]</span></span>
- <span data-ttu-id="5c5f5-242">[監視狀態、設定診斷記錄，以及開啟 Azure Logic Apps 的警示][logic-apps-monitor]</span><span class="sxs-lookup"><span data-stu-id="5c5f5-242">[Monitor status, set up diagnostics logging, and turn on alerts for Azure Logic Apps][logic-apps-monitor]</span></span>

<span data-ttu-id="5c5f5-243">每個服務也具有下列選項：</span><span class="sxs-lookup"><span data-stu-id="5c5f5-243">Each service also has these options:</span></span>

* <span data-ttu-id="5c5f5-244">如需進行更深入的分析並顯示在儀表板上，請將 Logic Apps 記錄傳送至 [Azure Log Analytics][logic-apps-log-analytics]。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-244">For deeper analysis and dashboarding, send Logic Apps logs to [Azure Log Analytics][logic-apps-log-analytics].</span></span>

* <span data-ttu-id="5c5f5-245">若要進行 DevOps 監視，您可以針對 API 管理設定 Azure Application Insights。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-245">For DevOps monitoring, configure Azure Application Insights for API Management.</span></span>

* <span data-ttu-id="5c5f5-246">API 管理支援[適用於自訂 API 分析的 Power BI 解決方案範本][apim-pbi] \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-246">API Management supports the [Power BI solution template for custom API analytics][apim-pbi].</span></span> <span data-ttu-id="5c5f5-247">您可以使用此解決方案範本來建立自己的分析解決方案。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-247">You can use this solution template for creating your own analytics solution.</span></span> <span data-ttu-id="5c5f5-248">若為商務使用者，Power BI 中有可用的報告。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-248">For business users, Power BI makes reports available.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="5c5f5-249">安全性考量</span><span class="sxs-lookup"><span data-stu-id="5c5f5-249">Security considerations</span></span>

<span data-ttu-id="5c5f5-250">雖然這份清單並未完整地說明所有的安全性最佳做法，以下安全性考量專門用於此架構：</span><span class="sxs-lookup"><span data-stu-id="5c5f5-250">Although this list doesn't completely describe all security best practices, here are some security considerations that apply specifically to this architecture:</span></span>

* <span data-ttu-id="5c5f5-251">Azure API 管理服務有固定的公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-251">The Azure API Management service has a fixed public IP address.</span></span> <span data-ttu-id="5c5f5-252">將呼叫 Logic Apps 端點的存取權限制成僅供 API 管理的 IP 位址使用。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-252">Restrict access for calling Logic Apps endpoints to only the IP address of API Management.</span></span> <span data-ttu-id="5c5f5-253">如需詳細資訊，請參閱[限制連入 IP 位址][logic-apps-restrict-ip]。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-253">For more information, see [Restrict incoming IP addresses][logic-apps-restrict-ip].</span></span>

* <span data-ttu-id="5c5f5-254">若要確保使用者具有適當的存取層級，請使用角色型存取控制 (RBAC)。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-254">To make sure users have appropriate access levels, use role-based access control (RBAC).</span></span>

* <span data-ttu-id="5c5f5-255">使用 OAuth 或 Open ID Connect 來保護 API 管理中的公用 API 端點。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-255">Secure public API endpoints in API Management by using OAuth or OpenID Connect.</span></span> <span data-ttu-id="5c5f5-256">若要保護公用 API 端點，請設定識別提供者，並新增 JSON Web 權杖 (JWT) 驗證原則。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-256">To secure public API endpoints, configure an identity provider, and add a JSON Web Token (JWT) validation policy.</span></span> <span data-ttu-id="5c5f5-257">如需詳細資訊，請參閱[使用 OAuth 2.0 搭配 Azure Active Directory 與 API 管理來保護 API][apim-oauth]。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-257">For more information, see [Protect an API by using OAuth 2.0 with Azure Active Directory and API Management][apim-oauth].</span></span>

* <span data-ttu-id="5c5f5-258">使用相互憑證，從 API 管理連線至後端服務。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-258">Connect to back-end services from API Management by using mutual certificates.</span></span>

* <span data-ttu-id="5c5f5-259">在 API 管理 API 上強制使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-259">Enforce HTTPS on the API Management APIs.</span></span>

### <a name="storing-secrets"></a><span data-ttu-id="5c5f5-260">儲存祕密</span><span class="sxs-lookup"><span data-stu-id="5c5f5-260">Storing secrets</span></span>

<span data-ttu-id="5c5f5-261">永遠不要將密碼、存取金鑰或連接字串簽入原始檔控制。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-261">Never check passwords, access keys, or connection strings into source control.</span></span> <span data-ttu-id="5c5f5-262">如果需要這些值，請使用適當的技術來保護和部署這些值。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-262">If these values are required, secure and deploy these values by using the appropriate techniques.</span></span> 

<span data-ttu-id="5c5f5-263">如果邏輯應用程式需要您無法在連接器內建立的任何敏感性值，請將這些值儲存在 Azure Key Vault 中，然後從 Resource Manager 範本參考這些值。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-263">If a logic app requires any sensitive values that you can't create within a connector, store those values in Azure Key Vault and reference them from a Resource Manager template.</span></span> <span data-ttu-id="5c5f5-264">針對每個環境使用部署範本參數和參數檔。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-264">Use deployment template parameters and parameter files for each environment.</span></span> <span data-ttu-id="5c5f5-265">如需詳細資訊，請參閱[保護工作流程中的參數和輸入][logic-apps-secure]。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-265">For more information, see [Secure parameters and inputs within a workflow][logic-apps-secure].</span></span>

<span data-ttu-id="5c5f5-266">API 管理中會使用名為「具名值」或「屬性」的物件來管理祕密。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-266">API Management manages secrets by using objects called *named values* or *properties*.</span></span> <span data-ttu-id="5c5f5-267">這些物件能安全地儲存可透過 API 管理原則存取的值。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-267">These objects securely store values that you can access through API Management policies.</span></span> <span data-ttu-id="5c5f5-268">如需詳細資訊，請參閱[如何使用 Azure API 管理原則中的具名值][apim-properties]。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-268">For more information, see [How to use Named Values in Azure API Management policies][apim-properties].</span></span>

## <a name="cost-considerations"></a><span data-ttu-id="5c5f5-269">成本考量</span><span class="sxs-lookup"><span data-stu-id="5c5f5-269">Cost considerations</span></span>

<span data-ttu-id="5c5f5-270">我們會向您收取所有執行中「API 管理」執行個體的費用。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-270">You are charged for all API Management instances when they are running.</span></span> <span data-ttu-id="5c5f5-271">如果您已相應增加，且並非隨時需要該層級的效能，請以手動方式相應減少或設定[自動調整][apim-autoscale]。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-271">If you have scaled up and don't need that level of performance all the time, manually scale down or configure [autoscaling][apim-autoscale].</span></span>

<span data-ttu-id="5c5f5-272">Logic Apps 會使用[無伺服器](/azure/logic-apps/logic-apps-serverless-overview)模型。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-272">Logic Apps uses a [serverless](/azure/logic-apps/logic-apps-serverless-overview) model.</span></span> <span data-ttu-id="5c5f5-273">計費的計算方式是以動作和連接器執行為基礎。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-273">Billing is calculated based on action and connector execution.</span></span> <span data-ttu-id="5c5f5-274">如需詳細資訊，請參閱 [Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps/)。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-274">For more information, see [Logic Apps pricing](https://azure.microsoft.com/pricing/details/logic-apps/).</span></span> <span data-ttu-id="5c5f5-275">目前沒有任何針對 Logic Apps 的階層考量。</span><span class="sxs-lookup"><span data-stu-id="5c5f5-275">Currently, there are no tier considerations for Logic Apps.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5c5f5-276">後續步驟</span><span class="sxs-lookup"><span data-stu-id="5c5f5-276">Next steps</span></span>

* <span data-ttu-id="5c5f5-277">了解[搭配佇列和事件的企業整合](/azure/logic-apps/logic-apps-architectures-enterprise-integration-with-queues-events)</span><span class="sxs-lookup"><span data-stu-id="5c5f5-277">Learn about [enterprise integration with queues and events](/azure/logic-apps/logic-apps-architectures-enterprise-integration-with-queues-events)</span></span>

<!-- links -->

[aad]: /azure/active-directory
[apim]: /azure/api-management
[apim-autoscale]: /azure/api-management/api-management-howto-autoscale
[apim-backup]: /azure/api-management/api-management-howto-disaster-recovery-backup-restore
[apim-caching]: /azure/api-management/api-management-howto-cache
[apim-capacity]: /azure/api-management/api-management-capacity
[apim-dev-portal]: /azure/api-management/api-management-key-concepts#a-namedeveloper-portal-a-developer-portal
[apim-domain]: /azure/api-management/configure-custom-domain
[apim-jwt]: /azure/api-management/policies/authorize-request-based-on-jwt-claims
[apim-logic-app]: /azure/api-management/import-logic-app-as-api
[apim-monitor]: /azure/api-management/api-management-howto-use-azure-monitor
[apim-oauth]: /azure/api-management/api-management-howto-protect-backend-with-aad
[apim-openapi]: /azure/api-management/import-api-from-oas
[apim-pbi]: http://aka.ms/apimpbi
[apim-pricing]: https://azure.microsoft.com/pricing/details/api-management/
[apim-properties]: /azure/api-management/api-management-howto-properties
[apim-sla]: https://azure.microsoft.com/support/legal/sla/api-management/
[apim-soap]: /azure/api-management/import-soap-api
[apim-versions]: /azure/api-management/api-management-get-started-publish-versions
[arm]: /azure/azure-resource-manager/resource-group-authoring-templates
[dns]: /azure/dns/
[integration-services]: https://azure.microsoft.com/product-categories/integration/
[logic-apps]: /azure/logic-apps/logic-apps-overview
[logic-apps-connectors]: /azure/connectors/apis-list
[logic-apps-log-analytics]: /azure/logic-apps/logic-apps-monitor-your-logic-apps-oms
[logic-apps-monitor]: /azure/logic-apps/logic-apps-monitor-your-logic-apps
[logic-apps-restrict-ip]: /azure/logic-apps/logic-apps-securing-a-logic-app#restrict-incoming-ip-addresses
[logic-apps-secure]: /azure/logic-apps/logic-apps-securing-a-logic-app#secure-parameters-and-inputs-within-a-workflow
[logic-apps-sla]: https://azure.microsoft.com/support/legal/sla/logic-apps
[monitor]: /azure/azure-monitor/overview
[rbac]: /azure/role-based-access-control/overview
[rto]: ../../resiliency/index.md#rto-and-rpo
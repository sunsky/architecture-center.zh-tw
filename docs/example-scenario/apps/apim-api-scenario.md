---
title: 將舊版 Web 應用程式移轉至 Azure 上的 API 型架構
description: 使用 Azure API 管理讓舊版 Web 應用程式變成現代化。
author: begim
ms.date: 09/13/2018
ms.openlocfilehash: 1aa7ea6dc895146e13677dd9867fb2530f0a8f04
ms.sourcegitcommit: 62945777e519d650159f0f963a2489b6bb6ce094
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2018
ms.locfileid: "48876780"
---
# <a name="migrating-a-legacy-web-application-to-an-api-based-architecture-on-azure"></a><span data-ttu-id="f2df9-103">將舊版 Web 應用程式移轉至 Azure 上的 API 型架構</span><span class="sxs-lookup"><span data-stu-id="f2df9-103">Migrating a legacy web application to an API-based architecture on Azure</span></span>

<span data-ttu-id="f2df9-104">旅遊產業的電子商務公司要將其舊版瀏覽器型軟體堆疊現代化。</span><span class="sxs-lookup"><span data-stu-id="f2df9-104">An e-commerce company in the travel industry is modernizing their legacy browser-based software stack.</span></span> <span data-ttu-id="f2df9-105">雖然其現有的堆疊大都為整合型，但有些 [SOAP 型 HTTP 服務][soap]從最近的專案中出現。</span><span class="sxs-lookup"><span data-stu-id="f2df9-105">While their existing stack is mostly monolithic, some [SOAP-based HTTP services][soap] exist from a recent project.</span></span> <span data-ttu-id="f2df9-106">他們正考慮建立額外的收益來源，讓一些已開發的內部智慧財產能夠賺錢。</span><span class="sxs-lookup"><span data-stu-id="f2df9-106">They are considering the creation of additional revenue streams to monetize some of the internal intellectual property that's been developed.</span></span>

<span data-ttu-id="f2df9-107">專案的目標包括處理技術負債、改善持續維護，以及減少迴歸錯誤來加快功能開發速度。</span><span class="sxs-lookup"><span data-stu-id="f2df9-107">Goals for the project include addressing technical debt, improving ongoing maintenance, and accelerating feature development with fewer regression bugs.</span></span> <span data-ttu-id="f2df9-108">專案會使用反覆程序來避免風險，其中有些步驟會平行執行：</span><span class="sxs-lookup"><span data-stu-id="f2df9-108">The project will use an iterative process to avoid risk, with some steps performed in parallel:</span></span>

* <span data-ttu-id="f2df9-109">應用程式後端是由 VM 上裝載的關聯式資料庫所組成，而開發小組會將它現代化。</span><span class="sxs-lookup"><span data-stu-id="f2df9-109">The development team will modernize the application back end, which is composed of relational databases hosted on VMs.</span></span>
* <span data-ttu-id="f2df9-110">內部開發小組會撰寫新的商務功能，該功能會透過新的 HTTP API 公開。</span><span class="sxs-lookup"><span data-stu-id="f2df9-110">The in-house development team will write new business functionality, which will be exposed over new HTTP APIs.</span></span>
* <span data-ttu-id="f2df9-111">合約開發小組會建置新的瀏覽器型 UI，並將其裝載在 Azure 中。</span><span class="sxs-lookup"><span data-stu-id="f2df9-111">A contract development team will build a new browser-based UI, which will be hosted in Azure.</span></span>

<span data-ttu-id="f2df9-112">新的應用程式功能會分階段提供。</span><span class="sxs-lookup"><span data-stu-id="f2df9-112">New application features will be delivered in stages.</span></span> <span data-ttu-id="f2df9-113">它們會「逐漸取代」現今支援其電子商務的現有瀏覽器型用戶端-伺服器 UI 功能 (裝載於內部部署環境)。</span><span class="sxs-lookup"><span data-stu-id="f2df9-113">They will *gradually replace* the existing browser-based client-server UI functionality (hosted on-premises) that powers their e-commerce business today.</span></span>

<span data-ttu-id="f2df9-114">管理小組不想進行多餘的現代化。</span><span class="sxs-lookup"><span data-stu-id="f2df9-114">The management team does not want to modernize unnecessarily.</span></span> <span data-ttu-id="f2df9-115">他們也想要控管範圍與成本。</span><span class="sxs-lookup"><span data-stu-id="f2df9-115">They also want to maintain control of scope and costs.</span></span> <span data-ttu-id="f2df9-116">為了達到目的，他們決定要保留其現有的 SOAP HTTP 服務。</span><span class="sxs-lookup"><span data-stu-id="f2df9-116">To do this, they have decided to preserve their existing SOAP HTTP services.</span></span> <span data-ttu-id="f2df9-117">他們也想要將現有 UI 的變更降至最低。</span><span class="sxs-lookup"><span data-stu-id="f2df9-117">They also intend to minimize changes to the existing UI.</span></span> <span data-ttu-id="f2df9-118">[Azure API 管理 (APIM)][apim] 可用於解決許多專案的需求和限制。</span><span class="sxs-lookup"><span data-stu-id="f2df9-118">[Azure API Management (APIM)][apim] can be utilized to address many of the project's requirements and constraints.</span></span>

## <a name="architecture"></a><span data-ttu-id="f2df9-119">架構</span><span class="sxs-lookup"><span data-stu-id="f2df9-119">Architecture</span></span>

![架構圖][architecture]

<span data-ttu-id="f2df9-121">新的 UI 會在 Azure 上當作平台即服務 (PaaS) 應用程式裝載，並且會相依於現有和新的 HTTP API。</span><span class="sxs-lookup"><span data-stu-id="f2df9-121">The new UI will be hosted as a platform as a service (PaaS) application on Azure, and will depend on both existing and new HTTP APIs.</span></span> <span data-ttu-id="f2df9-122">這些 API 會隨附一組設計更完善的介面，以達到更佳的效能、更容易整合，以及具備未來擴充性。</span><span class="sxs-lookup"><span data-stu-id="f2df9-122">These APIs will ship with a better-designed set of interfaces enabling better performance, easier integration, and future extensibility.</span></span>

### <a name="components-and-security"></a><span data-ttu-id="f2df9-123">元件與安全性</span><span class="sxs-lookup"><span data-stu-id="f2df9-123">Components and Security</span></span>

1. <span data-ttu-id="f2df9-124">現有的內部部署 Web 應用程式會繼續直接取用現有的內部部署 Web 服務。</span><span class="sxs-lookup"><span data-stu-id="f2df9-124">The existing on-premises web application will continue to directly consume the existing on-premises web services.</span></span>
2. <span data-ttu-id="f2df9-125">從現有的 Web 應用程式呼叫現有的 HTTP 服務將維持不變。</span><span class="sxs-lookup"><span data-stu-id="f2df9-125">Calls from the existing web app to the existing HTTP services will remain unchanged.</span></span> <span data-ttu-id="f2df9-126">這些呼叫位於公司網路內部。</span><span class="sxs-lookup"><span data-stu-id="f2df9-126">These calls are internal to the corporate network.</span></span>
3. <span data-ttu-id="f2df9-127">從 Azure 對現有內部服務進行傳入呼叫：</span><span class="sxs-lookup"><span data-stu-id="f2df9-127">Inbound calls are made from Azure to the existing internal services:</span></span>
    * <span data-ttu-id="f2df9-128">安全性小組會[使用安全傳輸 (HTTPS/SSL)][apim-ssl]，允許來自 APIM 執行個體的流量通過公司防火牆，到達現有的內部部署服務。</span><span class="sxs-lookup"><span data-stu-id="f2df9-128">The security team allows traffic from the APIM instance to pass through the corporate firewall to the existing on-premises services [using secure transport (HTTPS/SSL)][apim-ssl].</span></span>
    * <span data-ttu-id="f2df9-129">營運小組只允許從 APIM 執行個體對服務進行傳入呼叫。</span><span class="sxs-lookup"><span data-stu-id="f2df9-129">The operations team will allow inbound calls to the services only from the APIM instance.</span></span> <span data-ttu-id="f2df9-130">[將公司網路周邊的 APIM 執行個體 IP 位址列入白名單][apim-whitelist-ip]，以滿足此需求。</span><span class="sxs-lookup"><span data-stu-id="f2df9-130">This requirement is met by [white-listing the IP address of the APIM instance][apim-whitelist-ip] within the corporate network perimeter.</span></span>
    * <span data-ttu-id="f2df9-131">新的模組會設定到內部部署 HTTP 服務要求管線中 (**只**對源自外部的連線採取行動)，該管線會驗證 [APIM 將要提供的憑證][apim-mutualcert-auth]。</span><span class="sxs-lookup"><span data-stu-id="f2df9-131">A new module is configured into the on-premises HTTP services request pipeline (to act upon **only** those connections originating externally), which will validate [a certificate which APIM will provide][apim-mutualcert-auth].</span></span>
1. <span data-ttu-id="f2df9-132">新的 API：</span><span class="sxs-lookup"><span data-stu-id="f2df9-132">The new API:</span></span>
    * <span data-ttu-id="f2df9-133">只會透過 APIM 執行個體呈現，以提供 API 外觀。</span><span class="sxs-lookup"><span data-stu-id="f2df9-133">Is surfaced only through the APIM instance, which will provide the API facade.</span></span> <span data-ttu-id="f2df9-134">新的 API 無法直接存取。</span><span class="sxs-lookup"><span data-stu-id="f2df9-134">The new API won't be accessed directly.</span></span>
    * <span data-ttu-id="f2df9-135">會開發並發佈為 [Azure PaaS Web API 應用程式][azure-api-apps]。</span><span class="sxs-lookup"><span data-stu-id="f2df9-135">Is developed and published as an [Azure PaaS Web API App][azure-api-apps].</span></span>
    * <span data-ttu-id="f2df9-136">會列入白名單 (透過 [Web 應用程式設定][azure-appservice-ip-restrict])，只接受 [APIM VIP][apim-faq-vip]。</span><span class="sxs-lookup"><span data-stu-id="f2df9-136">Is white-listed (via [Web App settings][azure-appservice-ip-restrict]) to accept only the [APIM VIP][apim-faq-vip].</span></span>
    * <span data-ttu-id="f2df9-137">會裝載於已開啟安全傳輸/SSL 的 Azure Web Apps 中。</span><span class="sxs-lookup"><span data-stu-id="f2df9-137">Is hosted in Azure Web Apps with Secure Transport/SSL turned on.</span></span>
    * <span data-ttu-id="f2df9-138">已啟用[由 Azure App Service 所提供][azure-appservice-auth] (使用 Azure Active Directory 和 OAuth2) 的授權。</span><span class="sxs-lookup"><span data-stu-id="f2df9-138">Has authorization enabled, [provided by the Azure App Service][azure-appservice-auth] using Azure Active Directory and OAuth2.</span></span>
2. <span data-ttu-id="f2df9-139">新的瀏覽器型 Web 應用程式會相依於現有 HTTP API 和新 API **兩者**的 Azure API 管理執行個體。</span><span class="sxs-lookup"><span data-stu-id="f2df9-139">The new browser-based web application will depend on the Azure API Management instance for **both** the existing HTTP API and the new API.</span></span>

<span data-ttu-id="f2df9-140">APIM 執行個體將會設定為將舊版 HTTP 服務對應至新的 API 合約。</span><span class="sxs-lookup"><span data-stu-id="f2df9-140">The APIM instance will be configured to map the legacy HTTP services to a new API contract.</span></span> <span data-ttu-id="f2df9-141">如此一來，新的 Web UI 就不會察覺一組舊版服務 API 與新 API 的整合。</span><span class="sxs-lookup"><span data-stu-id="f2df9-141">By doing this, the new Web UI is unaware of the integration with a set of legacy services/APIs and new APIs.</span></span> <span data-ttu-id="f2df9-142">專案小組未來會逐漸將功能移植至新的 API，並且淘汰原始服務。</span><span class="sxs-lookup"><span data-stu-id="f2df9-142">In the future, the project team will gradually port functionality to the new APIs and retire the original services.</span></span> <span data-ttu-id="f2df9-143">這些變更會在 APIM 設定中處理，讓前端 UI 不受影響，並且避免重新開發工作。</span><span class="sxs-lookup"><span data-stu-id="f2df9-143">These changes will be handled within APIM configuration, leaving the front-end UI unaffected and avoiding redevelopment work.</span></span>

### <a name="alternatives"></a><span data-ttu-id="f2df9-144">替代項目</span><span class="sxs-lookup"><span data-stu-id="f2df9-144">Alternatives</span></span>

* <span data-ttu-id="f2df9-145">如果組織曾規劃將其基礎結構完全移到 Azure (包括裝載舊版應用程式的 VM)，則 APIM 仍是一個絕佳選項，因為它可作為任何可定址 HTTP 端點的外觀。</span><span class="sxs-lookup"><span data-stu-id="f2df9-145">If the organization was planning to move their infrastructure entirely to Azure, including the VMs hosting the legacy applications, then APIM would still be a great option since it can act as a facade for any addressable HTTP endpoint.</span></span>
* <span data-ttu-id="f2df9-146">如果客戶已決定將現有的端點保持私密，而不要公開它們，其 API 管理執行個體就可以連結到 [Azure 虛擬網路 (VNet)][azure-vnet]：</span><span class="sxs-lookup"><span data-stu-id="f2df9-146">If the customer had decided to keep the existing endpoints private and not expose them publicly, their API Management instance could be linked to an [Azure Virtual Network (VNet)][azure-vnet]:</span></span>
  * <span data-ttu-id="f2df9-147">在連結到已部署 Azure 虛擬網路的 [Azure 隨即轉移案例][azure-vm-lift-shift]中，客戶可以透過私人 IP 位址直接定址後端服務。</span><span class="sxs-lookup"><span data-stu-id="f2df9-147">In an [Azure lift and shift scenario][azure-vm-lift-shift] linked to their deployed Azure Virtual Network, the customer could directly address the back-end service through private IP addresses.</span></span>
  * <span data-ttu-id="f2df9-148">在內部部署案例中，API 管理執行個體可以透過 [Azure VPN 閘道與站對站 IPSec VPN 連線][azure-vpn]或 [ExpressRoute][azure-er] 私下連回內部服務，讓此案例成為[混合式 Azure 和內部部署案例][azure-hybrid]。</span><span class="sxs-lookup"><span data-stu-id="f2df9-148">In the on-premises scenario, the API Management instance could reach back to the internal service privately via an [Azure VPN gateway and site-to-site IPSec VPN connection][azure-vpn] or [ExpressRoute][azure-er] making this a [hybrid Azure and on-premises scenario][azure-hybrid].</span></span>
* <span data-ttu-id="f2df9-149">以內部模式部署 API 管理執行個體，即可讓 API 管理執行個體保持私密。</span><span class="sxs-lookup"><span data-stu-id="f2df9-149">The API Management instance can be kept private by deploying the API Management instance in Internal mode.</span></span> <span data-ttu-id="f2df9-150">此部署可接著搭配 [Azure 應用程式閘道][ azure-appgw]使用，以啟用某些 API 的公用存取，而其他 API 則保持內部狀態。</span><span class="sxs-lookup"><span data-stu-id="f2df9-150">The deployment could then be used with an [Azure Application Gateway][azure-appgw] to enable public access for some APIs while others remain internal.</span></span> <span data-ttu-id="f2df9-151">如需詳細資訊，請參閱[將內部模式的 APIM 連線至 VNET][apim-vnet-internal]。</span><span class="sxs-lookup"><span data-stu-id="f2df9-151">For more information, see [Connecting APIM in internal mode to a VNET][apim-vnet-internal].</span></span>

> [!NOTE]
> <span data-ttu-id="f2df9-152">如需將 API 管理連線至 VNET 的一般資訊，[請參閱這裡][apim-vnet]。</span><span class="sxs-lookup"><span data-stu-id="f2df9-152">For general information on connecting API Management to a VNET, [see here][apim-vnet].</span></span>

### <a name="availability-and-scalability"></a><span data-ttu-id="f2df9-153">可用性和延展性</span><span class="sxs-lookup"><span data-stu-id="f2df9-153">Availability and scalability</span></span>

* <span data-ttu-id="f2df9-154">選擇定價層，然後新增單位，即可將 Azure API 管理[相應放大][apim-scaleout]。</span><span class="sxs-lookup"><span data-stu-id="f2df9-154">Azure API Management can be [scaled out][apim-scaleout] by choosing a pricing tier and then adding units.</span></span>
* <span data-ttu-id="f2df9-155">[使用自動調整][apim-autoscale]，調整也會自動發生。</span><span class="sxs-lookup"><span data-stu-id="f2df9-155">Scaling also happen [automatically with auto scaling][apim-autoscale].</span></span>
* <span data-ttu-id="f2df9-156">[部署於多個區域][apim-multi-regions]就會啟用容錯移轉選項並可在[進階層][apim-pricing]中完成。</span><span class="sxs-lookup"><span data-stu-id="f2df9-156">[Deploying across multiple regions][apim-multi-regions] will enable fail over options and can be done in the [Premium tier][apim-pricing].</span></span>
* <span data-ttu-id="f2df9-157">請考慮[與 Azure Application Insights 整合][azure-apim-ai]，這也會透過 [Azure 監視器][azure-mon]呈現計量以供監視。</span><span class="sxs-lookup"><span data-stu-id="f2df9-157">Consider [Integrating with Azure Application Insights][azure-apim-ai], which also surfaces metrics through [Azure Monitor][azure-mon] for monitoring.</span></span>

## <a name="deployment"></a><span data-ttu-id="f2df9-158">部署</span><span class="sxs-lookup"><span data-stu-id="f2df9-158">Deployment</span></span>

<span data-ttu-id="f2df9-159">若要開始進行，[請在入口網站中建立 Azure API 管理執行個體][apim-create]。</span><span class="sxs-lookup"><span data-stu-id="f2df9-159">To get started, [create an Azure API Management instance in the portal.][apim-create]</span></span>

<span data-ttu-id="f2df9-160">或者，可以選擇符合特定使用案例的現有 Azure Resource Manager [快速入門範本][azure-quickstart-templates-apim]。</span><span class="sxs-lookup"><span data-stu-id="f2df9-160">Alternatively, you can choose from an existing Azure Resource Manager [quickstart template][azure-quickstart-templates-apim] that aligns to your specific use case.</span></span>

## <a name="pricing"></a><span data-ttu-id="f2df9-161">價格</span><span class="sxs-lookup"><span data-stu-id="f2df9-161">Pricing</span></span>

<span data-ttu-id="f2df9-162">API 管理以四種階層提供：開發人員、基本、標準和進階。</span><span class="sxs-lookup"><span data-stu-id="f2df9-162">API Management is offered in four tiers: developer, basic, standard, and premium.</span></span> <span data-ttu-id="f2df9-163">您可以在 [Azure API 管理定價指引][apim-pricing]找到這些階層差異的詳細指引。</span><span class="sxs-lookup"><span data-stu-id="f2df9-163">You can find detailed guidance on the difference in these tiers at the [Azure API Management pricing guidance here.][apim-pricing]</span></span>

<span data-ttu-id="f2df9-164">客戶可藉由新增和移除單位來調整 API 管理。</span><span class="sxs-lookup"><span data-stu-id="f2df9-164">Customers can scale API Management by adding and removing units.</span></span> <span data-ttu-id="f2df9-165">每個單位的容量依其階層而定。</span><span class="sxs-lookup"><span data-stu-id="f2df9-165">Each unit has capacity that depends on its tier.</span></span>

> [!NOTE]
> <span data-ttu-id="f2df9-166">開發人員階層可用於 API 管理功能的評估。</span><span class="sxs-lookup"><span data-stu-id="f2df9-166">The Developer tier can be used for evaluation of the API Management features.</span></span> <span data-ttu-id="f2df9-167">開發人員階層不得用於生產環境。</span><span class="sxs-lookup"><span data-stu-id="f2df9-167">The Developer tier should not be used for production.</span></span>

<span data-ttu-id="f2df9-168">若要檢視預估的成本並自訂部署需求，您可以在 [Azue 定價計算機][pricing-calculator]中修改縮放單位和 App Service 執行個體的數目。</span><span class="sxs-lookup"><span data-stu-id="f2df9-168">To view projected costs and customize to your deployment needs, you can modify the number of scale units and App Service instances in the [Azue Pricing Calculator][pricing-calculator].</span></span>

## <a name="related-resources"></a><span data-ttu-id="f2df9-169">相關資源</span><span class="sxs-lookup"><span data-stu-id="f2df9-169">Related resources</span></span>

<span data-ttu-id="f2df9-170">請參閱廣泛的 Azure API 管理[文件和參考文章][apim]。</span><span class="sxs-lookup"><span data-stu-id="f2df9-170">Check out the extensive Azure API Management [documentation and reference articles.][apim]</span></span>

<!-- links -->
[architecture]: ./media/architecture-apim-api-scenario.png
[apim-create]: /azure/api-management/get-started-create-service-instance
[apim-git]: /azure/api-management/api-management-configuration-repository-git
[apim-multi-regions]: /azure/api-management/api-management-howto-deploy-multi-region
[apim-autoscale]: /azure/api-management/api-management-howto-autoscale
[apim-scaleout]: /azure/api-management/upgrade-and-scale
[azure-apim-ai]: /azure/api-management/api-management-howto-app-insights
[azure-ai]: /azure/application-insights/
[azure-mon]: /azure/monitoring-and-diagnostics/monitoring-overview
[azure-appgw]: /azure/application-gateway/application-gateway-introduction
[apim-vnet-internal]: /azure/api-management/api-management-howto-integrate-internal-vnet-appgateway
[apim-vnet]: /azure/api-management/api-management-using-with-vnet
[azure-hybrid]: /azure/architecture/reference-architectures/hybrid-networking/
[azure-er]: /azure/expressroute/expressroute-introduction
[azure-vpn]: /azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[azure-vnet]: /azure/virtual-network/virtual-networks-overview
[azure-appservice-auth]: /azure/app-service/app-service-authentication-overview#identity-providers
[apim-faq-vip]: /azure/api-management/api-management-faq#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules
[azure-appservice-ip-restrict]: /azure/app-service/app-service-ip-restrictions
[azure-api-apps]: /azure/app-service/
[apim-ssl]: /azure/api-management/api-management-howto-manage-protocols-ciphers
[apim-mutualcert-auth]: /azure/api-management/api-management-howto-mutual-certificates
[apim-whitelist-ip]: /azure/api-management/api-management-faq#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules
[anti-corruption-layer-pattern]: /azure/architecture/patterns/anti-corruption-layer
[apim]: /azure/api-management/api-management-key-concepts
[apim-api-design-guidance]: /azure/architecture/best-practices/api-design
[visualstudio-youtube-solid-design]: https://youtu.be/agkWYPUcLpg
[azure-vm-lift-shift]: https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/
[standard-pricing-calc]: https://azure.com/e/
[premium-pricing-calc]: https://azure.com/e/
[apim-pricing]: https://azure.microsoft.com/pricing/details/api-management/
[azure-quickstart-templates-apim]: https://azure.microsoft.com/resources/templates/?term=API+Management&pageNumber=1
[soap]: https://en.wikipedia.org/wiki/SOAP
[pricing-calculator]: https://azure.com/e/0e916a861fac464db61342d378cc0bd6

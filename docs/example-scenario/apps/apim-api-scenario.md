---
title: 將舊版 Web 應用程式移轉至 Azure 上的 API 型架構
description: 使用 Azure API 管理讓舊版 Web 應用程式變成現代化。
author: begim
ms.date: 09/13/2018
ms.openlocfilehash: f468b3c6dc1c58e03555613b152882316ae2a017
ms.sourcegitcommit: 0a31fad9b68d54e2858314ca5fe6cba6c6b95ae4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51610578"
---
# <a name="migrating-a-legacy-web-application-to-an-api-based-architecture-on-azure"></a>將舊版 Web 應用程式移轉至 Azure 上的 API 型架構

旅遊產業的電子商務公司要將其舊版瀏覽器型軟體堆疊現代化。 雖然其現有的堆疊大都為整合型，但有些 [SOAP 型 HTTP 服務][soap]從最近的專案中出現。 他們正考慮建立額外的收益來源，讓一些已開發的內部智慧財產能夠賺錢。

專案的目標包括處理技術負債、改善持續維護，以及減少迴歸錯誤來加快功能開發速度。 專案會使用反覆程序來避免風險，其中有些步驟會平行執行：

* 應用程式後端是由 VM 上裝載的關聯式資料庫所組成，而開發小組會將它現代化。
* 內部開發小組會撰寫新的商務功能，該功能會透過新的 HTTP API 公開。
* 合約開發小組會建置新的瀏覽器型 UI，並將其裝載在 Azure 中。

新的應用程式功能會分階段提供。 這些功能會逐漸取代現今支援其電子商務的現有瀏覽器型用戶端-伺服器 UI 功能 (裝載於內部部署環境)。

管理小組不想進行多餘的現代化。 他們也想要控管範圍與成本。 為了達到目的，他們決定要保留其現有的 SOAP HTTP 服務。 他們也想要將現有 UI 的變更降至最低。 [Azure API 管理 (APIM)][apim] 可用於解決許多專案的需求和限制。

## <a name="architecture"></a>架構

![架構圖表][architecture]

新的 UI 會在 Azure 上當作平台即服務 (PaaS) 應用程式裝載，並且會相依於現有和新的 HTTP API。 這些 API 會隨附一組設計更完善的介面，以達到更佳的效能、更容易整合，以及具備未來擴充性。

### <a name="components-and-security"></a>元件與安全性

1. 現有的內部部署 Web 應用程式會繼續直接取用現有的內部部署 Web 服務。
2. 從現有的 Web 應用程式呼叫現有的 HTTP 服務將維持不變。 這些呼叫位於公司網路內部。
3. 從 Azure 對現有內部服務進行傳入呼叫：
    * 安全性小組會[使用安全傳輸 (HTTPS/SSL)][apim-ssl]，允許來自 APIM 執行個體的流量通過公司防火牆，到達現有的內部部署服務。
    * 營運小組只允許從 APIM 執行個體對服務進行傳入呼叫。 [將公司網路周邊的 APIM 執行個體 IP 位址列入白名單][apim-whitelist-ip]，以滿足此需求。
    * 新的模組會設定到內部部署 HTTP 服務要求管線中 (**只**對源自外部的連線採取行動)，該管線會驗證 [APIM 將要提供的憑證][apim-mutualcert-auth]。
1. 新的 API：
    * 只會透過 APIM 執行個體呈現，以提供 API 外觀。 新的 API 無法直接存取。
    * 會開發並發佈為 [Azure PaaS Web API 應用程式][azure-api-apps]。
    * 會列入白名單 (透過 [Web 應用程式設定][azure-appservice-ip-restrict])，只接受 [APIM VIP][apim-faq-vip]。
    * 會裝載於已開啟安全傳輸/SSL 的 Azure Web Apps 中。
    * 已啟用[由 Azure App Service 所提供][azure-appservice-auth] (使用 Azure Active Directory 和 OAuth2) 的授權。
2. 新的瀏覽器型 Web 應用程式會相依於現有 HTTP API 和新 API **兩者**的 Azure API 管理執行個體。

APIM 執行個體將會設定為將舊版 HTTP 服務對應至新的 API 合約。 如此一來，新的 Web UI 就不會察覺一組舊版服務 API 與新 API 的整合。 專案小組未來會逐漸將功能移植至新的 API，並且淘汰原始服務。 這些變更會在 APIM 設定中處理，讓前端 UI 不受影響，並且避免重新開發工作。

### <a name="alternatives"></a>替代項目

* 如果組織曾規劃將其基礎結構完全移到 Azure (包括裝載舊版應用程式的 VM)，則 APIM 仍是一個絕佳選項，因為它可作為任何可定址 HTTP 端點的外觀。
* 如果客戶已決定將現有的端點保持私密，而不要公開它們，其 API 管理執行個體就可以連結到 [Azure 虛擬網路 (VNet)][azure-vnet]：
  * 在連結到已部署 Azure 虛擬網路的 [Azure 隨即轉移案例][azure-vm-lift-shift]中，客戶可以透過私人 IP 位址直接定址後端服務。
  * 在內部部署案例中，API 管理執行個體可以透過 [Azure VPN 閘道與站對站 IPSec VPN 連線][azure-vpn]或 [ExpressRoute][azure-er] 私下連回內部服務，讓此案例成為[混合式 Azure 和內部部署案例][azure-hybrid]。
* 以內部模式部署 API 管理執行個體，即可讓 API 管理執行個體保持私密。 此部署可接著搭配 [Azure 應用程式閘道][ azure-appgw]使用，以啟用某些 API 的公用存取，而其他 API 則保持內部狀態。 如需詳細資訊，請參閱[將內部模式的 APIM 連線至 VNET][apim-vnet-internal]。

> [!NOTE]
> 如需將 API 管理連線至 VNET 的一般資訊，[請參閱這裡][apim-vnet]。

### <a name="availability-and-scalability"></a>可用性和延展性

* 選擇定價層，然後新增單位，即可將 Azure API 管理[相應放大][apim-scaleout]。
* [使用自動調整][apim-autoscale]，調整也會自動發生。
* [部署於多個區域][apim-multi-regions]就會啟用容錯移轉選項並可在[進階層][apim-pricing]中完成。
* 請考慮[與 Azure Application Insights 整合][azure-apim-ai]，這也會透過 [Azure 監視器][azure-mon]呈現計量以供監視。

## <a name="deployment"></a>部署

若要開始進行，[請在入口網站中建立 Azure API 管理執行個體][apim-create]。

或者，可以選擇符合特定使用案例的現有 Azure Resource Manager [快速入門範本][azure-quickstart-templates-apim]。

## <a name="pricing"></a>價格

API 管理以四種階層提供：開發人員、基本、標準和進階。 您可以在 [Azure API 管理定價指引][apim-pricing]找到這些階層差異的詳細指引。

客戶可藉由新增和移除單位來調整 API 管理。 每個單位的容量依其階層而定。

> [!NOTE]
> 開發人員階層可用於 API 管理功能的評估。 開發人員階層不得用於生產環境。

若要檢視預估的成本並自訂部署需求，您可以在 [Azure 定價計算機][pricing-calculator]中修改縮放單位和 App Service 執行個體的數目。

## <a name="related-resources"></a>相關資源

請檢閱廣泛的 Azure API 管理[文件和參考文章][apim]。


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

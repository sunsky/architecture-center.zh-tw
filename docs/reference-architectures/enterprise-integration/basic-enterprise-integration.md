---
title: 使用 Azure 的基本企業整合
titleSuffix: Azure Reference Architectures
description: 使用 Azure Logic Apps 和 Azure APIM 來實作簡單企業整合模式的建議架構。
services: logic-apps
author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: reference-architecture
ms.date: 12/03/2018
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: integration-services
ms.openlocfilehash: 76e422ead7e53c582a9d64ab1da643c3990749d6
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58242959"
---
# <a name="basic-enterprise-integration-on-azure"></a>Azure 上的基本企業整合

此參考架構使用 [Azure Integration Services][integration-services] 協調企業後端系統的呼叫。 後端系統可能包括軟體即服務 (SaaS) 系統、Azure 服務，以及企業中的現有 Web 服務。

Azure Integration Services 是整合應用程式和資料的服務集合。 此架構會使用這些服務的其中兩個：使用 [Logic Apps][logic-apps] 來協調工作流程，使用 [APIM][apim] 來建立 API 目錄。 此架構足夠供基本整合案例使用，其中對後端服務的同步呼叫會觸發工作流程。 使用[佇列和事件](./queues-events.md)的更複雜架構建立在此基本架構上。

![架構圖 - 簡單的企業整合](./_images/simple-enterprise-integration.png)

## <a name="architecture"></a>架構

此架構具有下列元件：

- **後端系統**。 圖表右側是企業已部署或依賴的各種後端系統。 這些可能包括 SaaS 系統、其他 Azure 服務，或者會公開 REST 或 SOAP 端點的 Web 服務。

- **Azure Logic Apps**。 [Logic Apps][logic-apps] 是一個無伺服器平台，用於建置可整合應用程式、資料和服務的企業工作流程。 在此架構中，邏輯應用程式會由 HTTP 要求觸發。 您也可以為更複雜的協調流程巢狀處理工作流程。 Logic Apps 會使用[連接器][logic-apps-connectors]來整合常用的服務。 Logic Apps 提供數百個連接器，且您可以建立自訂連接器。

- **Azure API 管理**。 [API 管理][apim]是受控服務，用於發佈 HTTP API 的目錄，以促進重複使用及可搜尋性。 API 管理由兩個相關元件組成：

  - **API 閘道**。 API 閘道可接受 HTTP 呼叫，並將它們路由傳送至後端。

  - **開發人員入口網站**。 Azure API 管理的每個執行個體都提供[開發人員入口網站][apim-dev-portal]的存取權。 此入口網站可讓開發人員存取呼叫 API 的文件和程式碼範例。 您也可以在開發人員入口網站中測試 API。

  在此架構中，複合 API 是藉由[匯入邏輯應用程式][apim-logic-app]為 API 所建立。 您也可以藉由[匯入 OpenAPI][apim-openapi] (Swagger) 規格或從 WSDL 規格[匯入 SOAP API][apim-soap] 來匯入現有的 Web 服務。

  API 閘道有助於分離前端用戶端與後端。 比方說，它可以重新撰寫 URL，或在其到達後端前轉換要求。 它也可處理許多跨領域問題，例如驗證、跨原始來源資源共用 (CORS) 支援，以及回應快取。

- **Azure DNS**。 [Azure DNS][dns] 是適用於 DNS 網域的主機服務。 Azure DNS 採用 Microsoft Azure 基礎結構來提供名稱解析。 只要將您的網域裝載於 Azure，就可以使用您用於其他 Azure 服務的相同認證、API、工具和計費方式來管理 DNS 記錄。 若要使用自訂網域名稱 (例如 contoso.com)，請建立將自訂網域名稱對應至 IP 位址的 DNS 記錄。 如需詳細資訊，請參閱[在 API 管理中設定自訂網域名稱][apim-domain]。

- **Azure Active Directory (Azure AD)**。 使用 [Azure AD][aad] 來驗證呼叫 API 閘道的用戶端。 Azure AD 可支援 OpenID Connect (OIDC) 通訊協定。 用戶端從 Azure AD 取得存取權杖，API 閘道會[驗證權杖][apim-jwt]以授權要求。 當使用 API 管理的標準或進階層，Azure AD 也可以保護開發人員入口網站的存取。

## <a name="recommendations"></a>建議

您的特定需求可能與此處所示的一般架構不同。 以本節的建議作為起點。

### <a name="api-management"></a>API 管理

使用 API 管理的基本、標準或進階層。 這些層會提供生產環境服務等級協定 (SLA)，並支援在 Azure 區域內相應放大規模。 API 管理的輸送量容量以*單位*進行測量。 每個定價層都有相應放大規模的上限。進階層也支援跨多個 Azure 區域相應放大規模。 根據您的功能集和必要輸送量的層級，選擇您的階層。 如需詳細資訊，請參閱 [API 管理價格][apim-pricing]與 [Azure API 管理執行個體的容量][apim-capacity]。

每個 Azure API 管理執行個體都有預設網域名稱，也就是 `azure-api.net` &mdash 的子網域，例如 `contoso.azure-api.net`。 請考慮為您的組織設定[自訂網域][apim-domain]。

### <a name="logic-apps"></a>Logic Apps

Logic Apps 最適合在回應不需要低延遲的案例中運作，例如非同步或半長時間執行的 API 呼叫。 如果需要低延遲 (例如，會封鎖使用者介面的呼叫)，請使用不同的技術。 例如，使用 Azure Functions 或部署至 Azure App Service 的 Web API。 使用 API 管理讓您的 API 朝向您的 API 取用者。

### <a name="region"></a>區域

若要將網路延遲降到最低，將「API 管理」和 Logic Apps 放在相同區域中。 通常會選擇最接近使用者的區域 (或最接近後端服務)。

資源群組也有區域。 此區域會指定部署中繼資料的儲存位置，以及部署範本的執行位置。 若要改善部署期間的可用性，請將資源群組和資源放在相同區域。

## <a name="scalability-considerations"></a>延展性考量

若要提升 API 管理的延展性，請視需要新增[快取原則][apim-caching]。 快取也有助於降低後端服務的負載。

若要提供更大的容量，您可以在 Azure 區域中相應放大 Azure API 管理的基本、標準及進階層。 若要分析服務的使用情況，請在 [計量] 功能表上選取 [容量計量] 選項，然後視需要相應增加或相應減少規模。 升級或調整程序需要 15 到 45 分鐘才會生效。

調整「API 管理」服務規模的建議事項：

- 請在調整時考慮流量模式。 流量模式變動較大的客戶需要更多容量。

- 容量如果一直大於 66%，可能代表需要相應增加規模。

- 容量如果一直低於 20%，則可能代表需要相應減少規模。

- 在生產環境中啟用負載前，一律先以代表性負載進行 API 管理服務負載測試。

使用進階層，您可以跨多個 Azure 區域調整 API 管理執行個體。 這讓 API 管理可適用於更高的 SLA，並讓您在使用者附近多個區域中佈建服務。

Logic Apps 無伺服器模型代表系統管理員不需要針對服務延展性進行規劃。 服務會自動調整規模以滿足需求。

## <a name="availability-considerations"></a>可用性考量

檢閱每個服務的 SLA：

- [API 管理 SLA][apim-sla]
- [Logic Apps SLA][logic-apps-sla]

如果使用進階層跨二或多個區域部署 API 管理，即適用於較高的 SLA。 請參閱 [API 管理定價][apim-pricing]。

### <a name="backups"></a>備份

定期[備份][apim-backup] API 管理設定。 將備份檔案儲存在不同於服務所部署區域的位置或 Azure 區域中。 根據 [RTO][rto] 選擇災害復原策略：

- 在災害復原事件中，佈建新的 API 管理執行個體、將備份還原至新的執行個體，以及重新指向 DNS 記錄。

- 將 API 管理服務的被動執行個體保留在其他 Azure 區域中。 定期將備份還原到該執行個體，以讓它與作用中服務保持同步。 若要在災害復原事件中還原服務，您只需重新指向 DNS 記錄。 這種方法會產生額外成本，因為您需要支付被動的執行個體，但可以縮短復原時間。

對於邏輯應用程式，我們建議使用組態即程式碼的方法來備份及還原。 因為邏輯應用程式無伺服器，您可以從 Azure Resource Manager 範本快速進行重新建立。 將範本儲存在原始檔控制中，整合範本與您的持續整合/持續部署 (CI/CD) 程序。 在災害復原事件中，將範本部署到新的區域。

如果您將邏輯應用程式部署至不同的區域，請更新 API 管理中的組態。 您可以使用基本 PowerShell 指令碼來更新 API 的**後端**屬性。

## <a name="manageability-considerations"></a>管理性考量

針對生產、部署和測試環境，各別建立資源群組。 個別的資源群組可讓您更輕鬆地管理部署、刪除測試部署及指派存取權限。

當您將資源指派給資源群組時，請考慮下列因素：

- **生命週期**。 一般來說，會將具有相同生命週期的資源放在同一個資源群組中。

- **存取**。 若要將存取原則套用至群組中的資源，您可以使用[角色型存取控制][rbac] (RBAC)。

- **計費**。 您可以檢視資源群組的彙總成本。

- **適用於 API 管理的定價層**。 使用開發與測試環境的開發人員層。 若要將成本降到最低，請部署生產環境的複本、執行測試，然後關閉。

### <a name="deployment"></a>部署

使用 [Azure Resource Manager 範本][arm]來部署 Azure 資源。 範本可讓您使用 PowerShell 或 Azure CLI，更輕鬆地自動執行部署。

將 Azure API 管理及任何個別的邏輯應用程式放在其自己的個別 Resource Manager 範本中。 使用不同的範本時，可以將資源儲存於原始檔控制系統中。 您可以一起部署範本，或者在 CI/CD 流程中個別部署。

### <a name="versions"></a>版本

每次您變更邏輯應用程式的組態或透過 Resource Manager 範本部署更新時，Azure 都會保留該版本的複本，及保留所有具有執行歷程記錄的版本。 您可以使用這些版本來追蹤歷程記錄變更，或將某個版本升階成邏輯應用程式的目前組態。 例如，您可以將邏輯應用程式復原至先前的版本。

「API 管理」支援兩個截然不同但互補的版本設定概念：

- *版本*可讓 API 取用者根據其需求選擇 API 版本，例如 v1、v2、搶鮮版 (Beta) 或生產環境版。

- *修訂*允許 API 管理員在 API 中進行非中斷變更，並部署這些變更以及變更記錄，以便告知 API 取用者所做變更。

您可以使用 Resource Manager 範本，在開發環境中進行修訂，然後在其他環境中部署該變更。 如需詳細資訊，請參閱[發佈多個 API 版本][apim-versions]

您也可以在變更之前使用修訂測試目前使用者能夠存取的 API。 不過，這個方法不建議用於負載測試或整合測試。 請改用個別的測試或生產階段前環境。

## <a name="diagnostics-and-monitoring"></a>診斷和監控

使用 [Azure 監視器][monitor]在 API 管理和 Logic Apps 中進行作業監視。 Azure 監視器預設會啟用，並且會根據針對每個服務所設定的計量來提供資訊。 如需詳細資訊，請參閱

- [監視已發佈的 API][apim-monitor]
- [監視狀態、設定診斷記錄，以及開啟 Azure Logic Apps 的警示][logic-apps-monitor]

每個服務也具有下列選項：

- 如需進行更深入的分析並顯示在儀表板上，請將 Logic Apps 記錄傳送至 [Azure Log Analytics][logic-apps-log-analytics]。

- 若要進行 DevOps 監視，您可以針對 API 管理設定 Azure Application Insights。

- API 管理支援[適用於自訂 API 分析的 Power BI 解決方案範本][apim-pbi] \(英文\)。 您可以使用此解決方案範本來建立自己的分析解決方案。 若為商務使用者，Power BI 中有可用的報告。

## <a name="security-considerations"></a>安全性考量

雖然這份清單並未完整地說明所有的安全性最佳做法，以下安全性考量專門用於此架構：

- Azure API 管理服務有固定的公用 IP 位址。 將呼叫 Logic Apps 端點的存取權限制成僅供 API 管理的 IP 位址使用。 如需詳細資訊，請參閱[限制連入 IP 位址][logic-apps-restrict-ip]。

- 若要確保使用者具有適當的存取層級，請使用角色型存取控制 (RBAC)。

- 使用 OAuth 或 Open ID Connect 來保護 API 管理中的公用 API 端點。 若要保護公用 API 端點，請設定識別提供者，並新增 JSON Web 權杖 (JWT) 驗證原則。 如需詳細資訊，請參閱[使用 OAuth 2.0 搭配 Azure Active Directory 與 API 管理來保護 API][apim-oauth]。

- 使用相互憑證，從 API 管理連線至後端服務。

- 在 API 管理 API 上強制使用 HTTPS。

### <a name="storing-secrets"></a>儲存祕密

永遠不要將密碼、存取金鑰或連接字串簽入原始檔控制。 如果需要這些值，請使用適當的技術來保護和部署這些值。

如果邏輯應用程式需要您無法在連接器內建立的任何敏感性值，請將這些值儲存在 Azure Key Vault 中，然後從 Resource Manager 範本參考這些值。 針對每個環境使用部署範本參數和參數檔。 如需詳細資訊，請參閱[保護工作流程中的參數和輸入][logic-apps-secure]。

API 管理中會使用名為「具名值」或「屬性」的物件來管理祕密。 這些物件能安全地儲存可透過 API 管理原則存取的值。 如需詳細資訊，請參閱[如何使用 Azure API 管理原則中的具名值][apim-properties]。

## <a name="cost-considerations"></a>成本考量

我們會向您收取所有執行中「API 管理」執行個體的費用。 如果您已相應增加，且並非隨時需要該層級的效能，請以手動方式相應減少或設定[自動調整][apim-autoscale]。

Logic Apps 會使用[無伺服器](/azure/logic-apps/logic-apps-serverless-overview)模型。 計費的計算方式是以動作和連接器執行為基礎。 如需詳細資訊，請參閱 [Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps/)。 目前沒有任何針對 Logic Apps 的階層考量。

## <a name="next-steps"></a>後續步驟

為了提升可靠性和延展性，使用訊息佇列和事件來分離後端系統。 此模式會顯示在此系列的下一個參考架構中：[使用訊息佇列和事件的企業整合](./queues-events.md)。

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
[apim-pbi]: https://aka.ms/apimpbi
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
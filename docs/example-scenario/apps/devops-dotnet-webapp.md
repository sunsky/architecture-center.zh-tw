---
title: 使用 VSTS 的 CI/CD 管線
description: 將 .NET 應用程式建置和發行至 Azure Web Apps 的範例
author: christianreddington
ms.date: 07/11/18
ms.openlocfilehash: ae4ac5fc02cc841fc39b3cbef46124fe9da75e9b
ms.sourcegitcommit: 71cbef121c40ef36e2d6e3a088cb85c4260599b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/14/2018
ms.locfileid: "39061009"
---
# <a name="cicd-pipeline-with-vsts"></a>使用 VSTS 的 CI/CD 管線

DevOps 是開發、品質保證和 IT 作業的整合。 DevOps 需要統一的文化特性，以及一組功能強大的程序來提供軟體。

此範例案例示範開發小組可如何使用 Visual Studio Team Services 將 .NET 兩層式 Web 應用程式部署至 Azure App Service。 Web 應用程式是依據下游的 Azure 平台即服務 (PaaS) 服務。 這份文件也將點出您在使用 Azure 平台即服務 (PaaS) 設計這類案例時，所應該進行的一些考量。

使用持續整合 (CI) 及持續部署 (CD) 對應用程式開發採用新式方法，可協助您透過穩固的建置、測試、部署和監視服務，加速傳遞價值給您的使用者。 除了 App Service 之外，組織使用如 Visual Studio Team Services 的平台，可確保他們保持專注於案例開發，而不是管理可將它啟用的基礎結構。

## <a name="related-use-cases"></a>相關使用案例

請針對下列使用案例考慮 DevOps：

* 加速應用程式開發和部署生命週期
* 將品質與一致性建置到自動化的組建與發行程序

## <a name="architecture"></a>架構

![DevOps 案例 (使用 Visual Studio Team Services 及 App Service) 中所牽涉到 Azure 元件的架構概觀][architecture]

此案例中會討論 DevOps 管線，適用於使用 Visual Studio Team Services (VSTS) 的 .NET Web 應用程式。 整個案例的資料流程如下所示：

1. 變更應用程式原始程式碼。
2. 認可應用程式的程式碼與 Web Apps 的 web.config 檔案。
3. 持續整合會觸發應用程式組建與單元測試。
4. 持續部署觸發程序會協調應用程式構件的「部署與環境專屬的參數化組態值」。
5. 部署至 Azure App Service。
6. Azure Application Insights 會收集與分析健康情況、效能及使用方式資料。
7. 檢閱健康情況、效能及使用方式資訊。

### <a name="components"></a>元件

* [資源群組][resource-groups]是適用於 Azure 資源的邏輯容器，還會針對管理平面提供存取控制邊界，將資源群組視為表示「部署的單位」。
* [Visual Studio Team Services (VSTS)][vsts] 是一項服務，可讓您端對端管理您的開發生命週期；從規劃和專案管理、程式碼管理到建置及發行。
* [Azure Web Apps][web-apps] 是用來裝載 Web 應用程式、REST API 和行動後端的平台即服務 (PaaS)。 雖然本文著重於 .NET，但也支援數個其他開發平台選項。
* [Application Insights][application-insights] 是多個平台上的 Web 開發人員所適用的第一方、可延伸「應用程式效能管理」(APM) 服務。

### <a name="alternative-devops-tooling-options"></a>替代的 DevOps 工具選項

本文著重於 Visual Studio Team Services，但 [Team Foundation Server][team-foundation-server] 可用來替代內部部署。 或者，您可能也會發現有一系列的技術會共同用於運用 [Jenkins][jenkins-on-azure] 的開放原始碼開發管線。

就基礎結構即程式碼的觀點而言，會包含 [Azure Resource Manager (ARM) 範本][arm-templates]作為 Azure DevOps 專案，但如果您在這裡有投資，則可以考慮 [Terraform][terraform] 或 [Chef][chef]。 如果您偏好基礎結構即服務 (IaaS) 為基礎的部署，且需要組態管理，則可考慮 [Azure Desired State Configuration][desired-state-configuration]、[Ansible][ansible] 或 [Chef][chef]。

### <a name="alternatives-to-web-app-hosting"></a>Web 應用程式裝載的替代項目

Azure Web Apps 中裝載的替代方案：

* [VM][compare-vm-hosting] - 適用於需要高程度控制的工作負載，或依賴無法使用 Web Apps 的作業系統元件 / 服務 (例如 Windows GAC 或 COM) 的工作負載
* [容器裝載][azure-containers] - 其中有作業系統相依性和裝載可攜性，或是裝載密度還有需求。
* [Service Fabric][service-fabric] - 如果工作負載架構著重於建置分散式元件，可受益於在具有高度控制權的叢集之間部署及執行，則適用這個選項。 Service Fabric 也可用來裝載容器。
* [無伺服器 Azure 函式][azure-functions] - 如果工作負載架構著重於細微的分散式元件，需要最低相依性，其中個別元件只需要隨需執行 (不連續)，且不需要元件的協調流程，則適用這個選項。

### <a name="devops"></a>DevOps

**[持續整合 (CI)][continuous-integration]** 的目標應該是展示穩定的建置，透過多個個別開發人員或小組，持續對共用程式碼基底進行小型、頻繁的變更。
在持續整合管線過程中，您應該；

* 經常簽入少量的程式碼 (避免批次處理較大或較複雜的變更，因為這些可能較難以成功合併)
* 利用足夠的程式碼涵蓋範圍進行應用程式元件的單元測試 (包括不滿意的路徑)
* 確保針對共用的主要 (或主幹) 分支執行建置。 此分支應該很穩定，且保持為「部署就緒」。 不完整或工作進行中的變更應該隔離在個別的分支中，並且經常進行「正向整合」的合併，以避免日後發生衝突。

**[持續傳遞 (CD)][continuous-delivery]** 的目標應該不僅要展示穩定的組建，還要展示穩定的部署。 這會使 CD 較難以實現，需要環境特定的設定，且正確設定這些值的機制。

此外，還需要足夠的整合測試涵蓋範圍，以確保已設定各種元件，以及端對端正確運作。

這可能也需要設定及重設環境特定的資料，以及管理資料庫結構描述版本。

持續傳遞也可能會延伸至負載測試和使用者驗收測試環境。

持續傳遞受惠於連續監視，理想情況為所有環境。
藉由編寫建立和組態指令碼，或是裝載基礎結構 (這可讓雲端式工作負載事半功倍，請參閱 Azure 基礎結構即程式碼) - 這也稱為 ["infrastructure-as-code"][infra-as-code]，能讓您在各環境之間部署及整合測試更輕鬆獲得一致性和可靠性。

* 在專案生命週期中盡早開始持續傳遞。 您越晚離開它，就會越困難。
* 應該提供與專案功能相同的優先順序給整合及單元測試
* 使用環境無從驗證的部署套件，及透過發行程序管理環境特定的設定。
* 在發行程序期間，保護發行管理工具內的敏感組態，或向外硬體安全模組 (HSM) 或 [Key Vault][azure-key-vault]。 請勿在原始檔控制中儲存機密組態。

**持續學習** - CD 環境最有效的監視是由應用程式效能監視工具 (簡稱 APM) 所提供，例如 Microsoft 的 [Application Insights][application-insights]。 若要了解錯誤、負載下的效能，請務必對應用程式工作負載監視具有足夠的深入解析。 [App Insights 可以整合到 VSTS，以啟用持續監視 CD 管線][app-insights-cd-monitoring]。 這可用來啟用自動進展至下一個階段，如果偵測到警示，不需要人為介入或復原。

## <a name="considerations"></a>考量

### <a name="availability"></a>可用性

建置您的雲端應用程式時，請考量利用[獲得可用性的典型設計模式][design-patterns-availability]。

在適當的 [App Service Web 應用程式參考架構][app-service-reference-architecture]中檢閱可用性考量

如需其他可用性主題，請參閱 Azure Architecture Center 中的[可用性檢查清單][availability]。

### <a name="scalability"></a>延展性

建置雲端應用程式時，請了解[獲得延展性的典型設計模式][design-patterns-scalability]。

在適當的 [App Service Web 應用程式參考架構][app-service-reference-architecture]中檢閱延展性考量

如需其他延展性主題，請參閱 Azure Architecture Center 中的[延展性檢查清單][scalability]。

### <a name="security"></a>安全性

請在適用時考量利用[獲得安全性的典型設計模式][design-patterns-security]。

在適當的 [App Service Web 應用程式參考架構][app-service-reference-architecture]中檢閱安全性考量。

如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。

### <a name="resiliency"></a>災害復原

檢閱[獲得復原的典型設計模式][design-patterns-resiliency]，並考量在適當時實作。

您可以在架構中心上找到一些 [App Service 的復原建議做法][resiliency-app-service]。

如需設計彈性解決方案的一般指引，請參閱[為 Azure 設計有彈性的應用程式][resiliency]。

## <a name="deploy-the-scenario"></a>部署案例

### <a name="prerequisites"></a>必要條件

* 您必須具有現有的 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [[免費帳戶]][azure-free-account]。
* 您必須具有現有的 Visual Studio Team Services (VSTS) 帳戶。 深入了解關於[建立 Visual Studio Team Services (VSTS) 帳戶][vsts-account-create]的詳細資料。

### <a name="walk-through"></a>逐步解說

在此案例中，您將使用 Azure DevOps 專案來建立您的 CI/CD 管線。

DevOps 專案會為您部署 App Service 方案、App Service 和 App Insights 資源，並為您設定 Visual Studio Team Services 專案。

一旦您具有 DevOps 專案並完成建置後，請檢閱相關聯的程式碼變更、工作項目和測試結果。 您會發現並不會顯示測試結果，因為程式碼不包含任何要執行的測試。

檢閱發行定義。 請注意，發行管線已設定，將我們的應用程式發行至 Dev。 觀察 [置放] 組建成品中有一組**持續部署觸發程序**，並且會自動發行至開發環境。 在持續部署程序中，您可能會看到跨多個環境的版本範圍。 發行可以跨兩個基礎結構 (使用如基礎結構即程式碼的技術)，還可部署所需的應用程式套件以及任何組態後置工作。

**其他考量。**

* 請考慮利用其中一個 VSTS 市集中提供的 [Token 化工作][ vsts-tokenization]。
* 請考慮使用[部署：Azure Key Vault][download-keyvault-secrets] VSTS 工作，從 Azure key Vault 將祕密下載到您的發行。 接著，您可以使用這些祕密作為發行定義中的變數，且不應將它們儲存在原始檔控制中。
* 請考慮在您的發行定義中使用[發行變數][vsts-release-variables]，以進行您環境的組態變更。 發行變數範圍可以設定為整個發行或指定的環境。 如果使用祕密資訊的變數，請確定您選取「掛鎖」圖示。
* 請考慮在發行管線中使用[部署閘道][vsts-deployment-gates]。 這可讓您運用與外部系統 (例如事件管理或其他定製的系統) 關聯的監視資料，以判斷是否應該升級發行。
* 在需要手動介入的發行管線中，請考慮使用[核准][vsts-approvals]功能。
* 請考慮盡早在您的發行管線中使用 [Application Insights][application-insights] 和其他監視工具。 大部分的組織只開始在其生產環境中進行監視，但您可提前在程序中找出潛在錯誤，並避免在生產環境中對您的使用者造成影響。

## <a name="pricing"></a>價格

Visual Studio Team Services 成本計算方式取除了所需的並行組建/版次，以及測試使用者的數目等因素之外，決於貴組織中需要存取的使用者數目。 [VSTS 定價頁面][vsts-pricing-page]中提供進一步的詳細說明。

* [Visual Studio Team Services (VSTS)][vsts-pricing-calculator] 是一項服務，可讓您管理開發生命週期，並且依每位使用者每月的使用量收取費用。 除了任何額外的測試使用者，或使用者的基本授權之外，根據並行管線，可能還有額外的費用。

## <a name="related-resources"></a>相關資源

* [什麼是 DevOps？][devops-whatis]
* [Microsoft 中的 DevOps - 我們使用 Visual Studio Team Services 的方式][devops-microsoft]
* [逐步教學課程︰使用 Visual Studio Team Services 的 DevOps][devops-with-vsts]
* [使用 Azure DevOps Project 建立適用於 .NET 的 CI/CD 管線][devops-project-create]

<!-- links -->
[ansible]: /azure/ansible/
[application-insights]: /azure/application-insights/app-insights-overview
[app-service-reference-architecture]: /azure/architecture/reference-architectures/app-service-web-app/
[azure-free-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[arm-templates]: /azure/azure-resource-manager/resource-group-overview#template-deployment
[architecture]: ./media/devops-dotnet-webapp/architecture-devops-dotnet-webapp.png
[availability]: /azure/architecture/checklist/availability
[chef]: /azure/chef/
[design-patterns-availability]: /azure/architecture/patterns/category/availability
[design-patterns-resiliency]: /azure/architecture/patterns/category/resiliency
[design-patterns-scalability]: /azure/architecture/patterns/category/performance-scalability
[design-patterns-security]: /azure/architecture/patterns/category/security
[desired-state-configuration]: /azure/automation/automation-dsc-overview
[devops-microsoft]: /azure/devops/devops-at-microsoft/
[devops-with-vsts]: https://almvm.azurewebsites.net/labs/vsts/
[application-insights]: https://azure.microsoft.com/en-gb/services/application-insights/
[cloud-based-load-testing]: https://visualstudio.microsoft.com/team-services/cloud-load-testing/
[cloud-based-load-testing-on-premises]: /vsts/test/load-test/clt-with-private-machines?view=vsts
[jenkins-on-azure]: /azure/jenkins/
[devops-whatis]: /azure/devops/what-is-devops
[download-keyvault-secrets]: /vsts/pipelines/tasks/deploy/azure-key-vault?view=vsts
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[resiliency-app-service]: /azure/architecture/checklist/resiliency-per-service#app-service
[resiliency]: /azure/architecture/checklist/resiliency
[scalability]: /azure/architecture/checklist/scalability
[vsts]: /vsts/?view=vsts#pivot=services
[continuous-integration]: /azure/devops/what-is-continuous-integration
[continuous-delivery]: /azure/devops/what-is-continuous-delivery
[web-apps]: /azure/app-service/app-service-web-overview
[terraform]: /azure/terraform/
[vsts-account-create]: /vsts/organizations/accounts/create-account-msa-or-work-student?view=vsts
[vsts-approvals]: /vsts/pipelines/release/approvals/approvals?view=vsts
[devops-project]: https://portal.azure.com/?feature.customportal=false#create/Microsoft.AzureProject
[vsts-deployment-gates]: /vsts/pipelines/release/approvals/gates?view=vsts
[vsts-pricing-calculator]: https://azure.com/e/498aa024454445a8a352e75724f900b1
[vsts-pricing-page]: https://azure.microsoft.com/en-us/pricing/details/visual-studio-team-services/
[vsts-release-variables]: /vsts/pipelines/release/variables?view=vsts&tabs=batch
[vsts-tokenization]: https://marketplace.visualstudio.com/search?term=token&target=VSTS&category=All%20categories&sortBy=Relevance
[azure-key-vault]: /azure/key-vault/key-vault-overview
[infra-as-code]: https://blogs.msdn.microsoft.com/mvpawardprogram/2018/02/13/infrastructure-as-code/
[team-foundation-server]: https://visualstudio.microsoft.com/tfs/
[infra-as-code]: https://blogs.msdn.microsoft.com/mvpawardprogram/2018/02/13/infrastructure-as-code/
[service-fabric]:/azure/service-fabric/
[azure-functions]:/azure/azure-functions/
[azure-containers]:https://azure.microsoft.com/en-us/overview/containers/
[compare-vm-hosting]:/azure/app-service/choose-web-site-cloud-service-vm
[app-insights-cd-monitoring]:/azure/application-insights/app-insights-vsts-continuous-monitoring
[azure-region-pair-bcdr]:/azure/best-practices-availability-paired-regions
[devops-project-create]: /vsts/pipelines/apps/cd/azure/azure-devops-project-aspnetcore?view=vsts
[security]: /azure/security/
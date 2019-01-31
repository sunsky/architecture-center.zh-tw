---
title: 使用 Azure DevOps 設計 CI/CD 管線
titleSuffix: Azure Example Scenarios
description: 使用 Azure DevOps 建置 .NET 應用程式並且發行至 Azure Web Apps。
author: christianreddington
ms.date: 12/06/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: fasttrack, seodec18
social_image_url: /azure/architecture/example-scenario/apps/media/architecture-devops-dotnet-webapp.svg
ms.openlocfilehash: 0d6ac13fb357657a2ec5e6284abadb46d6926907
ms.sourcegitcommit: 3b15d65e7c35a19506e562c444343f8467b6a073
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2019
ms.locfileid: "54908489"
---
# <a name="design-a-cicd-pipeline-using-azure-devops"></a>使用 Azure DevOps 設計 CI/CD 管線

本案例提供建置持續整合 (CI) 和持續部署 (CD) 管線的架構和設計指導方針。 在此範例中，CI/CD 管線會將兩層式 .NET Web 應用程式部署至 Azure App Service。

遷移至新式 CI/CD 程序可為應用程式的建置、部署、測試及監視帶來許多好處。 藉由使用 Azure DevOps 搭配 App Service 等其他服務，組織即可專注於其應用程式的開發，而不是支援基礎結構的管理。

## <a name="relevant-use-cases"></a>相關使用案例

您可考慮使用 Azure DevOps 與 CI/CD 程序來達到下列效果：

- 加速應用程式開發和部署生命週期
- 將品質與一致性建置到自動化的組建與發行程序
- 增加應用程式穩定性和執行時間

## <a name="architecture"></a>架構

![DevOps 案例 (使用 Azure DevOps 和 Azure App Service) 中所牽涉到 Azure 元件的架構圖][architecture]

整個案例的資料流程如下所示：

1. 開發人員變更應用程式的原始程式碼。
2. 將包括 web.config 檔的應用程式程式碼認可到 Azure Repos 中的原始程式碼存放庫。
3. 持續整合使用 Azure Test Plans 觸發應用程式組建與單元測試。
4. Azure Pipelines 中的持續部署會觸發程式構件的自動部署，並且「以環境專屬的組態值」來進行部署。
5. 構件會部署至 Azure App Service。
6. Azure Application Insights 會收集與分析健康情況、效能及使用方式資料。
7. 開發人員會監視及管理健康情況、效能和使用量資訊。
8. 待辦項目資訊可用來排定新功能和錯誤修正的優先順序 (使用 Azure Boards)。

### <a name="components"></a>元件

- [Azure DevOps][vsts] 是一項服務，可讓您管理端對端開發生命週期 &mdash; 從規劃和專案管理、程式碼管理，一直到建置及發行。

- [Azure Web Apps][web-apps] 是用來裝載 Web 應用程式、REST API 和行動後端的 PaaS 服務。 雖然本文著重於 .NET，但也支援數個其他開發平台選項。

- [Application Insights][application-insights] 是多個平台上的 Web 開發人員所適用的第一方、可延伸「應用程式效能管理」(APM) 服務。

### <a name="alternatives"></a>替代項目

雖然本文著重於 Azure DevOps，但 [Azure DevOps Server][azure-devops-server] (先前稱為 Team Foundation Server) 可用來替代內部部署。 或者，您也可以將一組技術運用於使用 [Jenkins][jenkins-on-azure] 的開放原始碼開發管線。

就基礎結構即程式碼的觀點而言，[Resource Manager 範本][arm-templates]已作為 Azure DevOps 專案的一部份，但您可以考慮其他管理技術，例如 [Terraform][terraform] 或 [Chef][chef]。 如果您偏好基礎結構即服務 (IaaS) 為基礎的部署，且需要組態管理，則可考慮 [Azure Automation State Configuration][desired-state-configuration]、[Ansible][ansible] 或 [Chef][chef]。

您可以考慮在 Azure Web Apps 中裝載這些替代項目：

- [Azure 虛擬機器][compare-vm-hosting]會處理需要高程度控制的工作負載，或依賴無法使用 Web Apps 的作業系統元件和服務 (例如 Windows GAC 或 COM) 的工作負載。

- 如果工作負載架構著重於建置分散式元件，可受益於在具有高度控制權的叢集之間部署及執行，則適用 [Service Fabric][service-fabric]。 Service Fabric 也可用來裝載容器。

- 如果工作負載架構著重於細微的分散式元件，需要最低相依性，其中個別元件只需要隨需執行 (不連續)，且不需要元件的協調流程，則可使用 [Azure Functions][azure-functions] 提供的有效無伺服器方法。

在選擇正確的移轉路徑時，此[適用於 Azure 計算服務的決策樹](/azure/architecture/guide/technology-choices/compute-decision-tree)可能有所幫助。

## <a name="management-and-security-considerations"></a>管理和安全性考量

- 請考慮利用其中一個 VSTS 市集中提供的 [Token 化工作][vsts-tokenization]。

- [Azure Key Vault][download-keyvault-secrets] 工作可從 Azure key Vault 將祕密下載到您的發行。 接著，您可以使用這些祕密作為發行定義中的變數，以避免將它們儲存在原始檔控制中。

- 在您的發行定義中使用[發行變數][vsts-release-variables]，以進行您環境的組態變更。 發行變數範圍可以設定為整個發行或指定的環境。 使用祕密資訊的變數時，請確定您選取「掛鎖」圖示。

- 您應在發行管線中使用[部署閘道][vsts-deployment-gates]。 這可讓您運用與外部系統 (例如事件管理或其他定製的系統) 關聯的監視資料，以判斷是否應該升級發行。

- 在需要手動介入的發行管線中，請使用[核准][vsts-approvals]功能。

- 請考慮盡早在您的發行管線中使用 [Application Insights][application-insights] 和其他監視工具。 許多組織只在其生產環境中啟用監視。 藉由監視您的其他環境，您可以在開發程序初期找出錯誤，以避免在生產環境中發生問題。

## <a name="deploy-the-scenario"></a>部署案例

### <a name="prerequisites"></a>必要條件

- 您必須具有現有的 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

- 您必須註冊 Azure DevOps 組織。 如需詳細資訊，請參閱[快速入門：建立您的組織][vsts-account-create]。

### <a name="walk-through"></a>逐步解說

[Azure DevOps 專案](/azure/devops-project/azure-devops-project-github)會為您部署 App Service 方案、App Service 和 App Insights 資源，並為您設定 Azure DevOps 專案。

一旦您部署 Azure DevOps 專案並完成建置後，請檢閱相關聯的程式碼變更、工作項目和測試結果。 您會發現並不會顯示測試結果，因為程式碼不包含任何要執行的測試。

專案會建立發行管線和持續部署觸發程序，並將我們的應用程式部署到開發環境。 在持續部署過程中，您可能會看到跨多個環境的版本。 發行可以跨兩個基礎結構 (使用如基礎結構即程式碼的技術)，還可部署所需的應用程式套件以及任何設定後工作。

## <a name="pricing"></a>價格

Azure DevOps 成本取決於貴組織中需要存取的使用者數目，以及所需的並行組建/版次數目和測試使用者數目等其他因素。 如需詳細資訊，請參閱 [Azure DevOps 定價][vsts-pricing-page]。

此[定價計算機][vsts-pricing-calculator]可為 20 位使用者提供執行 Azure DevOps 的估計。

Azure DevOps 會按每個使用者的每月使用量來計費。 除了任何額外的測試使用者，或使用者的基本授權之外，根據並行管線，可能還有額外的費用。

## <a name="related-resources"></a>相關資源

請檢閱下列資源來深入了解 CI/CD 及 Azure DevOps：

- [什麼是 DevOps？][devops-whatis]
- [Microsoft 的 DevOps - 如何使用 Azure DevOps][devops-microsoft]
- [逐步教學課程：使用 Azure DevOps 進行 DevOps][devops-with-vsts]
- [DevOps 檢查清單][devops-checklist]
- [使用 Azure DevOps Project 建立適用於 .NET 的 CI/CD 管線][devops-project-create]

<!-- links -->

[ansible]: /azure/ansible/
[application-insights]: /azure/application-insights/app-insights-overview
[app-service-reference-architecture]: ../../reference-architectures/app-service-web-app/basic-web-app.md
[arm-templates]: /azure/azure-resource-manager/resource-group-overview#template-deployment
[architecture]: ./media/architecture-devops-dotnet-webapp.svg
[chef]: /azure/chef/
[design-patterns-availability]: /azure/architecture/patterns/category/availability
[design-patterns-resiliency]: /azure/architecture/patterns/category/resiliency
[design-patterns-scalability]: /azure/architecture/patterns/category/performance-scalability
[design-patterns-security]: /azure/architecture/patterns/category/security
[desired-state-configuration]: /azure/automation/automation-dsc-overview
[devops-microsoft]: /azure/devops/devops-at-microsoft/
[devops-with-vsts]: https://almvm.azurewebsites.net/labs/vsts/
[devops-checklist]: /azure/architecture/checklist/dev-ops
[application-insights]: https://azure.microsoft.com/services/application-insights/
[cloud-based-load-testing]: https://visualstudio.microsoft.com/team-services/cloud-load-testing/
[cloud-based-load-testing-on-premises]: /vsts/test/load-test/clt-with-private-machines?view=vsts
[jenkins-on-azure]: /azure/jenkins/
[devops-whatis]: /azure/devops/what-is-devops
[download-keyvault-secrets]: /vsts/pipelines/tasks/deploy/azure-key-vault?view=vsts
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[resiliency-app-service]: /azure/architecture/checklist/resiliency-per-service#app-service
[vsts]: /vsts/?view=vsts#pivot=services
[continuous-integration]: /azure/devops/what-is-continuous-integration
[continuous-delivery]: /azure/devops/what-is-continuous-delivery
[web-apps]: /azure/app-service/app-service-web-overview
[vsts-account-create]: /azure/devops/organizations/accounts/create-organization-msa-or-work-student?view=vsts
[vsts-approvals]: /vsts/pipelines/release/approvals/approvals?view=vsts
[devops-project]: https://portal.azure.com/?feature.customportal=false#create/Microsoft.AzureProject
[vsts-deployment-gates]: /vsts/pipelines/release/approvals/gates?view=vsts
[vsts-pricing-calculator]: https://azure.com/e/498aa024454445a8a352e75724f900b1
[vsts-pricing-page]: https://azure.microsoft.com/pricing/details/visual-studio-team-services/
[vsts-release-variables]: /vsts/pipelines/release/variables?view=vsts&tabs=batch
[vsts-tokenization]: https://marketplace.visualstudio.com/search?term=token&target=VSTS&category=All%20categories&sortBy=Relevance
[azure-key-vault]: /azure/key-vault/key-vault-overview
[infra-as-code]: https://blogs.msdn.microsoft.com/mvpawardprogram/2018/02/13/infrastructure-as-code/
[azure-devops-server]: https://visualstudio.microsoft.com/tfs/
[infra-as-code]: https://blogs.msdn.microsoft.com/mvpawardprogram/2018/02/13/infrastructure-as-code/
[service-fabric]: /azure/service-fabric/
[azure-functions]: /azure/azure-functions/
[azure-containers]: https://azure.microsoft.com/overview/containers/
[compare-vm-hosting]: /azure/app-service/choose-web-site-cloud-service-vm
[app-insights-cd-monitoring]: /azure/application-insights/app-insights-vsts-continuous-monitoring
[azure-region-pair-bcdr]: /azure/best-practices-availability-paired-regions
[devops-project-create]: /azure/devops-project/azure-devops-project-aspnet-core
[terraform]: /azure/terraform/

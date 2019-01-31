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
# <a name="design-a-cicd-pipeline-using-azure-devops"></a><span data-ttu-id="e71aa-103">使用 Azure DevOps 設計 CI/CD 管線</span><span class="sxs-lookup"><span data-stu-id="e71aa-103">Design a CI/CD pipeline using Azure DevOps</span></span>

<span data-ttu-id="e71aa-104">本案例提供建置持續整合 (CI) 和持續部署 (CD) 管線的架構和設計指導方針。</span><span class="sxs-lookup"><span data-stu-id="e71aa-104">This scenario provides architecture and design guidance for building a continuous integration (CI) and continuous deployment (CD) pipeline.</span></span> <span data-ttu-id="e71aa-105">在此範例中，CI/CD 管線會將兩層式 .NET Web 應用程式部署至 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="e71aa-105">In this example, the CI/CD pipeline deploys a two-tier .NET web application to the Azure App Service.</span></span>

<span data-ttu-id="e71aa-106">遷移至新式 CI/CD 程序可為應用程式的建置、部署、測試及監視帶來許多好處。</span><span class="sxs-lookup"><span data-stu-id="e71aa-106">Migrating to modern CI/CD processes provides many benefits for application builds, deployments, testing, and monitoring.</span></span> <span data-ttu-id="e71aa-107">藉由使用 Azure DevOps 搭配 App Service 等其他服務，組織即可專注於其應用程式的開發，而不是支援基礎結構的管理。</span><span class="sxs-lookup"><span data-stu-id="e71aa-107">By utilizing Azure DevOps along with other services such as App Service, organizations can focus on the development of their apps rather than the management of the supporting infrastructure.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="e71aa-108">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="e71aa-108">Relevant use cases</span></span>

<span data-ttu-id="e71aa-109">您可考慮使用 Azure DevOps 與 CI/CD 程序來達到下列效果：</span><span class="sxs-lookup"><span data-stu-id="e71aa-109">Consider Azure DevOps and CI/CD processes for:</span></span>

- <span data-ttu-id="e71aa-110">加速應用程式開發和部署生命週期</span><span class="sxs-lookup"><span data-stu-id="e71aa-110">Accelerating application development and development life cycles</span></span>
- <span data-ttu-id="e71aa-111">將品質與一致性建置到自動化的組建與發行程序</span><span class="sxs-lookup"><span data-stu-id="e71aa-111">Building quality and consistency into an automated build and release process</span></span>
- <span data-ttu-id="e71aa-112">增加應用程式穩定性和執行時間</span><span class="sxs-lookup"><span data-stu-id="e71aa-112">Increasing application stability and uptime</span></span>

## <a name="architecture"></a><span data-ttu-id="e71aa-113">架構</span><span class="sxs-lookup"><span data-stu-id="e71aa-113">Architecture</span></span>

![DevOps 案例 (使用 Azure DevOps 和 Azure App Service) 中所牽涉到 Azure 元件的架構圖][architecture]

<span data-ttu-id="e71aa-115">整個案例的資料流程如下所示：</span><span class="sxs-lookup"><span data-stu-id="e71aa-115">The data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="e71aa-116">開發人員變更應用程式的原始程式碼。</span><span class="sxs-lookup"><span data-stu-id="e71aa-116">A developer changes application source code.</span></span>
2. <span data-ttu-id="e71aa-117">將包括 web.config 檔的應用程式程式碼認可到 Azure Repos 中的原始程式碼存放庫。</span><span class="sxs-lookup"><span data-stu-id="e71aa-117">Application code including the web.config file is committed to the source code repository in Azure Repos.</span></span>
3. <span data-ttu-id="e71aa-118">持續整合使用 Azure Test Plans 觸發應用程式組建與單元測試。</span><span class="sxs-lookup"><span data-stu-id="e71aa-118">Continuous integration triggers application build and unit tests using Azure Test Plans.</span></span>
4. <span data-ttu-id="e71aa-119">Azure Pipelines 中的持續部署會觸發程式構件的自動部署，並且「以環境專屬的組態值」來進行部署。</span><span class="sxs-lookup"><span data-stu-id="e71aa-119">Continuous deployment within Azure Pipelines triggers an automated deployment of application artifacts *with environment-specific configuration values*.</span></span>
5. <span data-ttu-id="e71aa-120">構件會部署至 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="e71aa-120">The artifacts are deployed to Azure App Service.</span></span>
6. <span data-ttu-id="e71aa-121">Azure Application Insights 會收集與分析健康情況、效能及使用方式資料。</span><span class="sxs-lookup"><span data-stu-id="e71aa-121">Azure Application Insights collects and analyzes health, performance, and usage data.</span></span>
7. <span data-ttu-id="e71aa-122">開發人員會監視及管理健康情況、效能和使用量資訊。</span><span class="sxs-lookup"><span data-stu-id="e71aa-122">Developers monitor and mange health, performance, and usage information.</span></span>
8. <span data-ttu-id="e71aa-123">待辦項目資訊可用來排定新功能和錯誤修正的優先順序 (使用 Azure Boards)。</span><span class="sxs-lookup"><span data-stu-id="e71aa-123">Backlog information is used to prioritize new features and bug fixes using Azure Boards.</span></span>

### <a name="components"></a><span data-ttu-id="e71aa-124">元件</span><span class="sxs-lookup"><span data-stu-id="e71aa-124">Components</span></span>

- <span data-ttu-id="e71aa-125">[Azure DevOps][vsts] 是一項服務，可讓您管理端對端開發生命週期 &mdash; 從規劃和專案管理、程式碼管理，一直到建置及發行。</span><span class="sxs-lookup"><span data-stu-id="e71aa-125">[Azure DevOps][vsts] is a service for managing your development life cycle end-to-end &mdash; from planning and project management, to code management, and continuing to build and release.</span></span>

- <span data-ttu-id="e71aa-126">[Azure Web Apps][web-apps] 是用來裝載 Web 應用程式、REST API 和行動後端的 PaaS 服務。</span><span class="sxs-lookup"><span data-stu-id="e71aa-126">[Azure Web Apps][web-apps] is a PaaS service for hosting web applications, REST APIs, and mobile back ends.</span></span> <span data-ttu-id="e71aa-127">雖然本文著重於 .NET，但也支援數個其他開發平台選項。</span><span class="sxs-lookup"><span data-stu-id="e71aa-127">While this article focuses on .NET, there are several additional development platform options supported.</span></span>

- <span data-ttu-id="e71aa-128">[Application Insights][application-insights] 是多個平台上的 Web 開發人員所適用的第一方、可延伸「應用程式效能管理」(APM) 服務。</span><span class="sxs-lookup"><span data-stu-id="e71aa-128">[Application Insights][application-insights] is a first-party, extensible Application Performance Management (APM) service for web developers on multiple platforms.</span></span>

### <a name="alternatives"></a><span data-ttu-id="e71aa-129">替代項目</span><span class="sxs-lookup"><span data-stu-id="e71aa-129">Alternatives</span></span>

<span data-ttu-id="e71aa-130">雖然本文著重於 Azure DevOps，但 [Azure DevOps Server][azure-devops-server] (先前稱為 Team Foundation Server) 可用來替代內部部署。</span><span class="sxs-lookup"><span data-stu-id="e71aa-130">While this article focuses on Azure DevOps, [Azure DevOps Server][azure-devops-server] (previously known as Team Foundation Server) could be used as an on-premises substitute.</span></span> <span data-ttu-id="e71aa-131">或者，您也可以將一組技術運用於使用 [Jenkins][jenkins-on-azure] 的開放原始碼開發管線。</span><span class="sxs-lookup"><span data-stu-id="e71aa-131">Alternatively, you could also use a set of technologies for an open-source development pipeline using [Jenkins][jenkins-on-azure].</span></span>

<span data-ttu-id="e71aa-132">就基礎結構即程式碼的觀點而言，[Resource Manager 範本][arm-templates]已作為 Azure DevOps 專案的一部份，但您可以考慮其他管理技術，例如 [Terraform][terraform] 或 [Chef][chef]。</span><span class="sxs-lookup"><span data-stu-id="e71aa-132">From an infrastructure-as-code perspective, [Resource Manager templates][arm-templates] were used as part of the Azure DevOps project, but you could consider other management technologies such as [Terraform][terraform] or [Chef][chef].</span></span> <span data-ttu-id="e71aa-133">如果您偏好基礎結構即服務 (IaaS) 為基礎的部署，且需要組態管理，則可考慮 [Azure Automation State Configuration][desired-state-configuration]、[Ansible][ansible] 或 [Chef][chef]。</span><span class="sxs-lookup"><span data-stu-id="e71aa-133">If you prefer an infrastructure-as-a-service (IaaS)-based deployment and require configuration management, you could consider either [Azure Automation State Configuration][desired-state-configuration], [Ansible][ansible], or [Chef][chef].</span></span>

<span data-ttu-id="e71aa-134">您可以考慮在 Azure Web Apps 中裝載這些替代項目：</span><span class="sxs-lookup"><span data-stu-id="e71aa-134">You could consider these alternatives to hosting in Azure Web Apps:</span></span>

- <span data-ttu-id="e71aa-135">[Azure 虛擬機器][compare-vm-hosting]會處理需要高程度控制的工作負載，或依賴無法使用 Web Apps 的作業系統元件和服務 (例如 Windows GAC 或 COM) 的工作負載。</span><span class="sxs-lookup"><span data-stu-id="e71aa-135">[Azure Virtual Machines][compare-vm-hosting] handles workloads that require a high degree of control, or depend on OS components and services that are not possible with Web Apps (for example, the Windows GAC, or COM).</span></span>

- <span data-ttu-id="e71aa-136">如果工作負載架構著重於建置分散式元件，可受益於在具有高度控制權的叢集之間部署及執行，則適用 [Service Fabric][service-fabric]。</span><span class="sxs-lookup"><span data-stu-id="e71aa-136">[Service Fabric][service-fabric] is a good option if the workload architecture is focused around distributed components that benefit from being deployed and run across a cluster with a high degree of control.</span></span> <span data-ttu-id="e71aa-137">Service Fabric 也可用來裝載容器。</span><span class="sxs-lookup"><span data-stu-id="e71aa-137">Service Fabric can also be used to host containers.</span></span>

- <span data-ttu-id="e71aa-138">如果工作負載架構著重於細微的分散式元件，需要最低相依性，其中個別元件只需要隨需執行 (不連續)，且不需要元件的協調流程，則可使用 [Azure Functions][azure-functions] 提供的有效無伺服器方法。</span><span class="sxs-lookup"><span data-stu-id="e71aa-138">[Azure Functions][azure-functions] provides an effective serverless approach if the workload architecture is centered around fine grained distributed components, requiring minimal dependencies, where individual components are only required to run on demand (not continuously) and orchestration of components is not required.</span></span>

<span data-ttu-id="e71aa-139">在選擇正確的移轉路徑時，此[適用於 Azure 計算服務的決策樹](/azure/architecture/guide/technology-choices/compute-decision-tree)可能有所幫助。</span><span class="sxs-lookup"><span data-stu-id="e71aa-139">This [decision tree for Azure compute services](/azure/architecture/guide/technology-choices/compute-decision-tree) may help when choosing the right path to take for a migration.</span></span>

## <a name="management-and-security-considerations"></a><span data-ttu-id="e71aa-140">管理和安全性考量</span><span class="sxs-lookup"><span data-stu-id="e71aa-140">Management and Security Considerations</span></span>

- <span data-ttu-id="e71aa-141">請考慮利用其中一個 VSTS 市集中提供的 [Token 化工作][vsts-tokenization]。</span><span class="sxs-lookup"><span data-stu-id="e71aa-141">Consider leveraging one of the [tokenization tasks][vsts-tokenization] available in the VSTS marketplace.</span></span>

- <span data-ttu-id="e71aa-142">[Azure Key Vault][download-keyvault-secrets] 工作可從 Azure key Vault 將祕密下載到您的發行。</span><span class="sxs-lookup"><span data-stu-id="e71aa-142">[Azure Key Vault][download-keyvault-secrets] tasks can download secrets from an Azure Key Vault into your release.</span></span> <span data-ttu-id="e71aa-143">接著，您可以使用這些祕密作為發行定義中的變數，以避免將它們儲存在原始檔控制中。</span><span class="sxs-lookup"><span data-stu-id="e71aa-143">You can then use those secrets as variables in your release definition, which avoids storing them in source control.</span></span>

- <span data-ttu-id="e71aa-144">在您的發行定義中使用[發行變數][vsts-release-variables]，以進行您環境的組態變更。</span><span class="sxs-lookup"><span data-stu-id="e71aa-144">Use [release variables][vsts-release-variables] in your release definitions to drive configuration changes of your environments.</span></span> <span data-ttu-id="e71aa-145">發行變數範圍可以設定為整個發行或指定的環境。</span><span class="sxs-lookup"><span data-stu-id="e71aa-145">Release variables can be scoped to an entire release or a given environment.</span></span> <span data-ttu-id="e71aa-146">使用祕密資訊的變數時，請確定您選取「掛鎖」圖示。</span><span class="sxs-lookup"><span data-stu-id="e71aa-146">When using variables for secret information, ensure that you select the padlock icon.</span></span>

- <span data-ttu-id="e71aa-147">您應在發行管線中使用[部署閘道][vsts-deployment-gates]。</span><span class="sxs-lookup"><span data-stu-id="e71aa-147">[Deployment gates][vsts-deployment-gates] should be used in your release pipeline.</span></span> <span data-ttu-id="e71aa-148">這可讓您運用與外部系統 (例如事件管理或其他定製的系統) 關聯的監視資料，以判斷是否應該升級發行。</span><span class="sxs-lookup"><span data-stu-id="e71aa-148">This lets you leverage monitoring data in association with external systems (for example, incident management or additional bespoke systems) to determine whether a release should be promoted.</span></span>

- <span data-ttu-id="e71aa-149">在需要手動介入的發行管線中，請使用[核准][vsts-approvals]功能。</span><span class="sxs-lookup"><span data-stu-id="e71aa-149">Where manual intervention in a release pipeline is required, use the [approvals][vsts-approvals] functionality.</span></span>

- <span data-ttu-id="e71aa-150">請考慮盡早在您的發行管線中使用 [Application Insights][application-insights] 和其他監視工具。</span><span class="sxs-lookup"><span data-stu-id="e71aa-150">Consider using [Application Insights][application-insights] and additional monitoring tools as early as possible in your release pipeline.</span></span> <span data-ttu-id="e71aa-151">許多組織只在其生產環境中啟用監視。</span><span class="sxs-lookup"><span data-stu-id="e71aa-151">Many organizations only begin monitoring in their production environment.</span></span> <span data-ttu-id="e71aa-152">藉由監視您的其他環境，您可以在開發程序初期找出錯誤，以避免在生產環境中發生問題。</span><span class="sxs-lookup"><span data-stu-id="e71aa-152">By monitoring your other environments, you can identify bugs earlier in the development process and avoid issues in your production environment.</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="e71aa-153">部署案例</span><span class="sxs-lookup"><span data-stu-id="e71aa-153">Deploy the scenario</span></span>

### <a name="prerequisites"></a><span data-ttu-id="e71aa-154">必要條件</span><span class="sxs-lookup"><span data-stu-id="e71aa-154">Prerequisites</span></span>

- <span data-ttu-id="e71aa-155">您必須具有現有的 Azure 帳戶。</span><span class="sxs-lookup"><span data-stu-id="e71aa-155">You must have an existing Azure account.</span></span> <span data-ttu-id="e71aa-156">如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。</span><span class="sxs-lookup"><span data-stu-id="e71aa-156">If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.</span></span>

- <span data-ttu-id="e71aa-157">您必須註冊 Azure DevOps 組織。</span><span class="sxs-lookup"><span data-stu-id="e71aa-157">You must sign up for an Azure DevOps organization.</span></span> <span data-ttu-id="e71aa-158">如需詳細資訊，請參閱[快速入門：建立您的組織][vsts-account-create]。</span><span class="sxs-lookup"><span data-stu-id="e71aa-158">For more information, see [Quickstart: Create your organization][vsts-account-create].</span></span>

### <a name="walk-through"></a><span data-ttu-id="e71aa-159">逐步解說</span><span class="sxs-lookup"><span data-stu-id="e71aa-159">Walk-through</span></span>

<span data-ttu-id="e71aa-160">[Azure DevOps 專案](/azure/devops-project/azure-devops-project-github)會為您部署 App Service 方案、App Service 和 App Insights 資源，並為您設定 Azure DevOps 專案。</span><span class="sxs-lookup"><span data-stu-id="e71aa-160">The [Azure DevOps project](/azure/devops-project/azure-devops-project-github) will deploy an App Service Plan, App Service, and an App Insights resource for you, as well as configure the Azure DevOps project for you.</span></span>

<span data-ttu-id="e71aa-161">一旦您部署 Azure DevOps 專案並完成建置後，請檢閱相關聯的程式碼變更、工作項目和測試結果。</span><span class="sxs-lookup"><span data-stu-id="e71aa-161">Once you've deployed the Azure DevOps project and the build is completed, review the associated code changes, work items, and test results.</span></span> <span data-ttu-id="e71aa-162">您會發現並不會顯示測試結果，因為程式碼不包含任何要執行的測試。</span><span class="sxs-lookup"><span data-stu-id="e71aa-162">You will notice that no test results are displayed, because the code does not contain any tests to run.</span></span>

<span data-ttu-id="e71aa-163">專案會建立發行管線和持續部署觸發程序，並將我們的應用程式部署到開發環境。</span><span class="sxs-lookup"><span data-stu-id="e71aa-163">The project creates a release pipeline and continuous deployment trigger, deploying our application into the Dev environment.</span></span> <span data-ttu-id="e71aa-164">在持續部署過程中，您可能會看到跨多個環境的版本。</span><span class="sxs-lookup"><span data-stu-id="e71aa-164">As part of a continuous deployment process, you may see releases that span multiple environments.</span></span> <span data-ttu-id="e71aa-165">發行可以跨兩個基礎結構 (使用如基礎結構即程式碼的技術)，還可部署所需的應用程式套件以及任何設定後工作。</span><span class="sxs-lookup"><span data-stu-id="e71aa-165">A release can span both infrastructure (using techniques such as infrastructure-as-code), and can also deploy the application packages required along with any post-configuration tasks.</span></span>

## <a name="pricing"></a><span data-ttu-id="e71aa-166">價格</span><span class="sxs-lookup"><span data-stu-id="e71aa-166">Pricing</span></span>

<span data-ttu-id="e71aa-167">Azure DevOps 成本取決於貴組織中需要存取的使用者數目，以及所需的並行組建/版次數目和測試使用者數目等其他因素。</span><span class="sxs-lookup"><span data-stu-id="e71aa-167">Azure DevOps costs depend on the number of users in your organization that require access, along with other factors like the number of concurrent build/releases required and number of test users.</span></span> <span data-ttu-id="e71aa-168">如需詳細資訊，請參閱 [Azure DevOps 定價][vsts-pricing-page]。</span><span class="sxs-lookup"><span data-stu-id="e71aa-168">For more information, see [Azure DevOps pricing][vsts-pricing-page].</span></span>

<span data-ttu-id="e71aa-169">此[定價計算機][vsts-pricing-calculator]可為 20 位使用者提供執行 Azure DevOps 的估計。</span><span class="sxs-lookup"><span data-stu-id="e71aa-169">This [pricing calculator][vsts-pricing-calculator] provides an estimate for running Azure DevOps with 20 users.</span></span>

<span data-ttu-id="e71aa-170">Azure DevOps 會按每個使用者的每月使用量來計費。</span><span class="sxs-lookup"><span data-stu-id="e71aa-170">Azure DevOps is billed on a per-user per-month basis.</span></span> <span data-ttu-id="e71aa-171">除了任何額外的測試使用者，或使用者的基本授權之外，根據並行管線，可能還有額外的費用。</span><span class="sxs-lookup"><span data-stu-id="e71aa-171">There may be additional charges dependent upon concurrent pipelines needed, in addition to any additional test users or user basic licenses.</span></span>

## <a name="related-resources"></a><span data-ttu-id="e71aa-172">相關資源</span><span class="sxs-lookup"><span data-stu-id="e71aa-172">Related resources</span></span>

<span data-ttu-id="e71aa-173">請檢閱下列資源來深入了解 CI/CD 及 Azure DevOps：</span><span class="sxs-lookup"><span data-stu-id="e71aa-173">Review the following resources to learn more about CI/CD and Azure DevOps:</span></span>

- <span data-ttu-id="e71aa-174">[什麼是 DevOps？][devops-whatis]</span><span class="sxs-lookup"><span data-stu-id="e71aa-174">[What is DevOps?][devops-whatis]</span></span>
- <span data-ttu-id="e71aa-175">[Microsoft 的 DevOps - 如何使用 Azure DevOps][devops-microsoft]</span><span class="sxs-lookup"><span data-stu-id="e71aa-175">[DevOps at Microsoft - How we work with Azure DevOps][devops-microsoft]</span></span>
- <span data-ttu-id="e71aa-176">[逐步教學課程：使用 Azure DevOps 進行 DevOps][devops-with-vsts]</span><span class="sxs-lookup"><span data-stu-id="e71aa-176">[Step-by-step Tutorials: DevOps with Azure DevOps][devops-with-vsts]</span></span>
- <span data-ttu-id="e71aa-177">[DevOps 檢查清單][devops-checklist]</span><span class="sxs-lookup"><span data-stu-id="e71aa-177">[Devops Checklist][devops-checklist]</span></span>
- <span data-ttu-id="e71aa-178">[使用 Azure DevOps Project 建立適用於 .NET 的 CI/CD 管線][devops-project-create]</span><span class="sxs-lookup"><span data-stu-id="e71aa-178">[Create a CI/CD pipeline for .NET with the Azure DevOps project][devops-project-create]</span></span>

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

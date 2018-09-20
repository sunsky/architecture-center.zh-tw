---
title: 使用 VSTS 的 CI/CD 管線
description: 將 .NET 應用程式建置和發行至 Azure Web Apps 的範例
author: christianreddington
ms.date: 07/11/18
ms.openlocfilehash: aea757087f4a505a8c52658abe1841c5455977cc
ms.sourcegitcommit: c49aeef818d7dfe271bc4128b230cfc676f05230
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2018
ms.locfileid: "44389261"
---
# <a name="cicd-pipeline-with-vsts"></a><span data-ttu-id="bd4f7-103">使用 VSTS 的 CI/CD 管線</span><span class="sxs-lookup"><span data-stu-id="bd4f7-103">CI/CD pipeline with VSTS</span></span>

<span data-ttu-id="bd4f7-104">DevOps 是開發、品質保證和 IT 作業的整合。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-104">DevOps is the integration of development, quality assurance, and IT operations.</span></span> <span data-ttu-id="bd4f7-105">DevOps 需要統一的文化特性，以及一組功能強大的程序來提供軟體。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-105">DevOps requires both unified culture and a strong set of processes for delivering software.</span></span>

<span data-ttu-id="bd4f7-106">此範例案例示範開發小組可如何使用 Visual Studio Team Services 將 .NET 兩層式 Web 應用程式部署至 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-106">This example scenario demonstrates how development teams can use Visual Studio Team Services to deploy a .NET two-tier web application to Azure App Service.</span></span> <span data-ttu-id="bd4f7-107">Web 應用程式是依據下游的 Azure 平台即服務 (PaaS) 服務。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-107">The Web Application depends on downstream Azure Platform as a Service (PaaS) services.</span></span> <span data-ttu-id="bd4f7-108">這份文件也將點出您在使用 Azure 平台即服務 (PaaS) 設計這類案例時，所應該進行的一些考量。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-108">This document also points out some considerations that you should make when designing such a scenario using Azure Platform as a Service (PaaS).</span></span>

<span data-ttu-id="bd4f7-109">使用持續整合 (CI) 及持續部署 (CD) 對應用程式開發採用新式方法，可協助您透過穩固的建置、測試、部署和監視服務，加速傳遞價值給您的使用者。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-109">Adopting a modern approach to application development using Continuous Integration (CI) and Continuous Deployment (CD), helps you to accelerate the delivery of value to your users through a robust build, test, deployment, and monitoring service.</span></span> <span data-ttu-id="bd4f7-110">除了 App Service 之外，組織使用如 Visual Studio Team Services 的平台，可確保他們保持專注於案例開發，而不是管理可將它啟用的基礎結構。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-110">By using a platform such as Visual Studio Team Services in addition to Azure services such as App Service, organizations can ensure they remain focused on the development of their scenario, rather than the management of the infrastructure to enable it.</span></span>

## <a name="related-use-cases"></a><span data-ttu-id="bd4f7-111">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="bd4f7-111">Related use cases</span></span>

<span data-ttu-id="bd4f7-112">請針對下列使用案例考慮 DevOps：</span><span class="sxs-lookup"><span data-stu-id="bd4f7-112">Consider DevOps for the following use cases:</span></span>

* <span data-ttu-id="bd4f7-113">加速應用程式開發和部署生命週期</span><span class="sxs-lookup"><span data-stu-id="bd4f7-113">Speeding up application development and development life cycles</span></span>
* <span data-ttu-id="bd4f7-114">將品質與一致性建置到自動化的組建與發行程序</span><span class="sxs-lookup"><span data-stu-id="bd4f7-114">Building quality and consistency into an automated build and release process</span></span>

## <a name="architecture"></a><span data-ttu-id="bd4f7-115">架構</span><span class="sxs-lookup"><span data-stu-id="bd4f7-115">Architecture</span></span>

![DevOps 案例 (使用 Visual Studio Team Services 及 App Service) 中所牽涉到 Azure 元件的架構概觀][architecture]

<span data-ttu-id="bd4f7-117">此案例中會討論 DevOps 管線，適用於使用 Visual Studio Team Services (VSTS) 的 .NET Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-117">This scenario covers a DevOps pipeline for a .NET web application using Visual Studio Team Services (VSTS).</span></span> <span data-ttu-id="bd4f7-118">整個案例的資料流程如下所示：</span><span class="sxs-lookup"><span data-stu-id="bd4f7-118">The data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="bd4f7-119">變更應用程式原始程式碼。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-119">Change application source code.</span></span>
2. <span data-ttu-id="bd4f7-120">認可應用程式的程式碼與 Web Apps 的 web.config 檔案。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-120">Commit application code and Web Apps web.config file.</span></span>
3. <span data-ttu-id="bd4f7-121">持續整合會觸發應用程式組建與單元測試。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-121">Continuous integration triggers application build and unit tests.</span></span>
4. <span data-ttu-id="bd4f7-122">持續部署觸發程序會協調應用程式構件的「部署與環境專屬的參數化組態值」。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-122">Continuous deployment trigger orchestrates deployment of application artifacts *with environment-specific parameterized configuration values*.</span></span>
5. <span data-ttu-id="bd4f7-123">部署至 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-123">Deployment to Azure App Service.</span></span>
6. <span data-ttu-id="bd4f7-124">Azure Application Insights 會收集與分析健康情況、效能及使用方式資料。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-124">Azure Application Insights collects and analyzes health, performance, and usage data.</span></span>
7. <span data-ttu-id="bd4f7-125">檢閱健康情況、效能及使用方式資訊。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-125">Review health, performance, and usage information.</span></span>

### <a name="components"></a><span data-ttu-id="bd4f7-126">元件</span><span class="sxs-lookup"><span data-stu-id="bd4f7-126">Components</span></span>

* <span data-ttu-id="bd4f7-127">[資源群組][resource-groups]是適用於 Azure 資源的邏輯容器，還會針對管理平面提供存取控制邊界，將資源群組視為表示「部署的單位」。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-127">[Resource Groups][resource-groups] are a logical container for Azure resources and also provide an access control boundary for the management plane - think of a Resource Group as representing a "unit of deployment".</span></span>
* <span data-ttu-id="bd4f7-128">[Visual Studio Team Services (VSTS)][vsts] 是一項服務，可讓您端對端管理您的開發生命週期；從規劃和專案管理、程式碼管理到建置及發行。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-128">[Visual Studio Team Services (VSTS)][vsts] is a service that enables you to manage your development life cycle end-to-end; from planning and project management, to code management, through to build and release.</span></span>
* <span data-ttu-id="bd4f7-129">[Azure Web Apps][web-apps] 是用來裝載 Web 應用程式、REST API 和行動後端的平台即服務 (PaaS)。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-129">[Azure Web Apps][web-apps] is a Platform as a Service (PaaS) service for hosting web applications, REST APIs, and mobile backends.</span></span> <span data-ttu-id="bd4f7-130">雖然本文著重於 .NET，但也支援數個其他開發平台選項。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-130">While this article focuses on .NET, there are several additional development platform options supported.</span></span>
* <span data-ttu-id="bd4f7-131">[Application Insights][application-insights] 是多個平台上的 Web 開發人員所適用的第一方、可延伸「應用程式效能管理」(APM) 服務。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-131">[Application Insights][application-insights] is a first-party, extensible Application Performance Management (APM) service for web developers on multiple platforms.</span></span>

### <a name="alternative-devops-tooling-options"></a><span data-ttu-id="bd4f7-132">替代的 DevOps 工具選項</span><span class="sxs-lookup"><span data-stu-id="bd4f7-132">Alternative DevOps tooling options</span></span>

<span data-ttu-id="bd4f7-133">本文著重於 Visual Studio Team Services，但 [Team Foundation Server][team-foundation-server] 可用來替代內部部署。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-133">Whilst this article focuses on Visual Studio Team Services, [Team Foundation Server][team-foundation-server] could be used as on premises substitute.</span></span> <span data-ttu-id="bd4f7-134">或者，您可能也會發現有一系列的技術會共同用於運用 [Jenkins][jenkins-on-azure] 的開放原始碼開發管線。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-134">Alternatively, you may also find a collection of technologies being used together for an Open Source development pipeline leveraging [Jenkins][jenkins-on-azure].</span></span>

<span data-ttu-id="bd4f7-135">就基礎結構即程式碼的觀點而言，會包含 [Azure Resource Manager 範本][arm-templates]作為 Azure DevOps 專案，但如果您在這裡有投資，則可以考慮 [Terraform][terraform] 或 [Chef][chef]。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-135">From an Infrastructure as Code perspective, [Azure Resource Manager Templates][arm-templates] are included as part of the Azure DevOps project, but you could consider [Terraform][terraform] or [Chef][chef] if you have investments here.</span></span> <span data-ttu-id="bd4f7-136">如果您偏好基礎結構即服務 (IaaS) 為基礎的部署，且需要組態管理，則可考慮 [Azure Automation State Configuration][desired-state-configuration]、[Ansible][ansible] 或 [Chef][chef]。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-136">If you prefer an Infrastructure as a Service (IaaS) based deployment and require configuration management, then you could consider either [Azure Automation State Configuration][desired-state-configuration], [Ansible][ansible] or [Chef][chef].</span></span>

### <a name="alternatives-to-web-app-hosting"></a><span data-ttu-id="bd4f7-137">Web 應用程式裝載的替代項目</span><span class="sxs-lookup"><span data-stu-id="bd4f7-137">Alternatives to Web App Hosting</span></span>

<span data-ttu-id="bd4f7-138">Azure Web Apps 中裝載的替代方案：</span><span class="sxs-lookup"><span data-stu-id="bd4f7-138">Alternatives to hosting in Azure Web Apps:</span></span>

* <span data-ttu-id="bd4f7-139">[VM][compare-vm-hosting] - 適用於需要高程度控制的工作負載，或依賴無法使用 Web Apps 的作業系統元件 / 服務 (例如 Windows GAC 或 COM) 的工作負載</span><span class="sxs-lookup"><span data-stu-id="bd4f7-139">[VM][compare-vm-hosting] - For workloads that require a high degree of control, or depend on OS components / services that are not possible with Web Apps (for example, the Windows GAC, or COM)</span></span>
* <span data-ttu-id="bd4f7-140">[容器裝載][azure-containers] - 其中有作業系統相依性和裝載可攜性，或是裝載密度還有需求。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-140">[Container Hosting][azure-containers] - Where there are OS dependencies and hosting portability, or hosting density, are also requirements.</span></span>
* <span data-ttu-id="bd4f7-141">[Service Fabric][service-fabric] - 如果工作負載架構著重於建置分散式元件，可受益於在具有高度控制權的叢集之間部署及執行，則適用這個選項。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-141">[Service Fabric][service-fabric] - A good option if the workload architecture is focused around distributed components that benefit from being deployed and run across a cluster with a high degree of control.</span></span> <span data-ttu-id="bd4f7-142">Service Fabric 也可用來裝載容器。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-142">Service Fabric can also be used to host containers.</span></span>
* <span data-ttu-id="bd4f7-143">[無伺服器 Azure 函式][azure-functions] - 如果工作負載架構著重於細微的分散式元件，需要最低相依性，其中個別元件只需要隨需執行 (不連續)，且不需要元件的協調流程，則適用這個選項。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-143">[Serverless Azure functions][azure-functions] - A good option if the workload architecture is centered around fine grained distributed components, requiring minimal dependencies, where individual components are only required to run on demand (not continuously) and orchestration of components is not required.</span></span>

### <a name="devops"></a><span data-ttu-id="bd4f7-144">DevOps</span><span class="sxs-lookup"><span data-stu-id="bd4f7-144">DevOps</span></span>

<span data-ttu-id="bd4f7-145">**[持續整合 (CI)][continuous-integration]** 的目標應該是展示穩定的建置，透過多個個別開發人員或小組，持續對共用程式碼基底進行小型、頻繁的變更。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-145">**[Continuous Integration (CI)][continuous-integration]** should aim to demonstrate a stable build, with more than one individual developer or team continuously committing small, frequent changes to the shared codebase.</span></span>
<span data-ttu-id="bd4f7-146">在持續整合管線過程中，您應該；</span><span class="sxs-lookup"><span data-stu-id="bd4f7-146">As part of your Continuous Integration pipeline you should;</span></span>

* <span data-ttu-id="bd4f7-147">經常簽入少量的程式碼 (避免批次處理較大或較複雜的變更，因為這些可能較難以成功合併)</span><span class="sxs-lookup"><span data-stu-id="bd4f7-147">Check in small amounts of code frequently (avoid batching up larger or more complex changes as these can be harder to merge successfully)</span></span>
* <span data-ttu-id="bd4f7-148">利用足夠的程式碼涵蓋範圍進行應用程式元件的單元測試 (包括不滿意的路徑)</span><span class="sxs-lookup"><span data-stu-id="bd4f7-148">Unit Test the components of your application with sufficient code coverage (including the unhappy paths)</span></span>
* <span data-ttu-id="bd4f7-149">確保針對共用的主要 (或主幹) 分支執行建置。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-149">Ensuring the build is run against the shared, master (or trunk) branch.</span></span> <span data-ttu-id="bd4f7-150">此分支應該很穩定，且保持為「部署就緒」。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-150">This branch should be stable and maintained as "deployment ready".</span></span> <span data-ttu-id="bd4f7-151">不完整或工作進行中的變更應該隔離在個別的分支中，並且經常進行「正向整合」的合併，以避免日後發生衝突。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-151">Incomplete or work-in-progress changes should be isolated in a separate branch with frequent 'forward integration' merges to avoid conflicts later.</span></span>

<span data-ttu-id="bd4f7-152">**[持續傳遞 (CD)][continuous-delivery]** 的目標應該不僅要展示穩定的組建，還要展示穩定的部署。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-152">**[Continuous Delivery (CD)][continuous-delivery]** should aim to demonstrate not only a stable build but a stable deployment.</span></span> <span data-ttu-id="bd4f7-153">這會使 CD 較難以實現，需要環境特定的設定，且正確設定這些值的機制。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-153">This makes realizing CD a little more difficult, environment-specific configuration is required and a mechanism for setting those values correctly.</span></span>

<span data-ttu-id="bd4f7-154">此外，還需要足夠的整合測試涵蓋範圍，以確保已設定各種元件，以及端對端正確運作。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-154">In addition, sufficient coverage of Integration Testing is required to ensure that the various components are configured and working correctly end-to-end.</span></span>

<span data-ttu-id="bd4f7-155">這可能也需要設定及重設環境特定的資料，以及管理資料庫結構描述版本。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-155">This may also require setting up and resetting environment-specific data and managing database schema versions.</span></span>

<span data-ttu-id="bd4f7-156">持續傳遞也可能會延伸至負載測試和使用者驗收測試環境。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-156">Continuous Delivery may also extend to Load Testing and User Acceptance Testing Environments.</span></span>

<span data-ttu-id="bd4f7-157">持續傳遞受惠於連續監視，理想情況為所有環境。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-157">Continuous Delivery benefits from continuous Monitoring, ideally across all environments.</span></span>
<span data-ttu-id="bd4f7-158">藉由編寫建立和組態指令碼，或是裝載基礎結構 (這可讓雲端式工作負載事半功倍，請參閱 Azure 基礎結構即程式碼) - 這也稱為 ["infrastructure-as-code"][infra-as-code]，能讓您在各環境之間部署及整合測試更輕鬆獲得一致性和可靠性。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-158">The consistency and reliability of deployments and integration testing across environments is made easier by scripting the creation and configuration or the hosting infrastructure (something that is considerably easier for Cloud-based workloads, see Azure infrastructure as code) - this is also known as ["infrastructure-as-code"][infra-as-code].</span></span>

* <span data-ttu-id="bd4f7-159">在專案生命週期中盡早開始持續傳遞。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-159">Start Continuous Delivery as early as possible in the project life-cycle.</span></span> <span data-ttu-id="bd4f7-160">您越晚離開它，就會越困難。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-160">The later you leave it, the more difficult it will be.</span></span>
* <span data-ttu-id="bd4f7-161">應該提供與專案功能相同的優先順序給整合及單元測試</span><span class="sxs-lookup"><span data-stu-id="bd4f7-161">Integration & unit tests should be given the same priority as the project features</span></span>
* <span data-ttu-id="bd4f7-162">使用環境無從驗證的部署套件，及透過發行程序管理環境特定的設定。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-162">Use environment agnostic deployment packages and manage environment-specific configuration through the release process.</span></span>
* <span data-ttu-id="bd4f7-163">在發行程序期間，保護發行管理工具內的敏感組態，或向外硬體安全模組 (HSM) 或 [Key Vault][azure-key-vault]。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-163">Protect sensitive configuration within the release management tooling or by calling out to a Hardware-security-module (HSM), or [Key Vault][azure-key-vault], during the release process.</span></span> <span data-ttu-id="bd4f7-164">請勿在原始檔控制中儲存機密組態。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-164">Do not store sensitive configuration within source control.</span></span>

<span data-ttu-id="bd4f7-165">**持續學習** - CD 環境最有效的監視是由應用程式效能監視工具 (簡稱 APM) 所提供，例如 Microsoft 的 [Application Insights][application-insights]。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-165">**Continuous Learning** - The most effective monitoring of a CD environment is provided by Application-Performance-Monitoring tools (APM for short), for example Microsoft's [Application Insights][application-insights].</span></span> <span data-ttu-id="bd4f7-166">若要了解錯誤、負載下的效能，請務必對應用程式工作負載監視具有足夠的深入解析。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-166">Sufficient depth of monitoring for an application workload is critical to understand bugs, performance under load.</span></span> <span data-ttu-id="bd4f7-167">[App Insights 可以整合到 VSTS，以啟用持續監視 CD 管線][app-insights-cd-monitoring]。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-167">[App Insights can be integrated into VSTS to enable continuous monitoring of the CD pipeline][app-insights-cd-monitoring].</span></span> <span data-ttu-id="bd4f7-168">這可用來啟用自動進展至下一個階段，如果偵測到警示，不需要人為介入或復原。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-168">This could be used to enable automatic progression to the next stage, without human intervention, or rollback if an alert is detected.</span></span>

## <a name="considerations"></a><span data-ttu-id="bd4f7-169">考量</span><span class="sxs-lookup"><span data-stu-id="bd4f7-169">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="bd4f7-170">可用性</span><span class="sxs-lookup"><span data-stu-id="bd4f7-170">Availability</span></span>

<span data-ttu-id="bd4f7-171">建置您的雲端應用程式時，請考量利用[獲得可用性的典型設計模式][design-patterns-availability]。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-171">Consider leveraging the [typical design patterns for availability][design-patterns-availability] when building your cloud application.</span></span>

<span data-ttu-id="bd4f7-172">在適當的 [App Service Web 應用程式參考架構][app-service-reference-architecture]中檢閱可用性考量</span><span class="sxs-lookup"><span data-stu-id="bd4f7-172">Review the availability considerations in the appropriate [App Service web application reference architecture][app-service-reference-architecture]</span></span>

<span data-ttu-id="bd4f7-173">如需其他可用性主題，請參閱 Azure Architecture Center 中的[可用性檢查清單][availability]。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-173">For other availability topics, see the [availability checklist][availability] in the Azure Architecture Center.</span></span>

### <a name="scalability"></a><span data-ttu-id="bd4f7-174">延展性</span><span class="sxs-lookup"><span data-stu-id="bd4f7-174">Scalability</span></span>

<span data-ttu-id="bd4f7-175">建置雲端應用程式時，請了解[獲得延展性的典型設計模式][design-patterns-scalability]。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-175">When building a cloud application be aware of the [typical design patterns for scalability][design-patterns-scalability].</span></span>

<span data-ttu-id="bd4f7-176">在適當的 [App Service Web 應用程式參考架構][app-service-reference-architecture]中檢閱延展性考量</span><span class="sxs-lookup"><span data-stu-id="bd4f7-176">Review the scalability considerations in the appropriate [App Service web application reference architecture][app-service-reference-architecture]</span></span>

<span data-ttu-id="bd4f7-177">如需其他延展性主題，請參閱 Azure Architecture Center 中的[延展性檢查清單][scalability]。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-177">For other scalability topics, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="bd4f7-178">安全性</span><span class="sxs-lookup"><span data-stu-id="bd4f7-178">Security</span></span>

<span data-ttu-id="bd4f7-179">請在適用時考量利用[獲得安全性的典型設計模式][design-patterns-security]。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-179">Consider leveraging the [typical design patterns for security][design-patterns-security] where appropriate.</span></span>

<span data-ttu-id="bd4f7-180">在適當的 [App Service Web 應用程式參考架構][app-service-reference-architecture]中檢閱安全性考量。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-180">Review the security considerations in the appropriate [App Service web application reference architecture][app-service-reference-architecture].</span></span>

<span data-ttu-id="bd4f7-181">如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-181">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="bd4f7-182">災害復原</span><span class="sxs-lookup"><span data-stu-id="bd4f7-182">Resiliency</span></span>

<span data-ttu-id="bd4f7-183">檢閱[獲得復原的典型設計模式][design-patterns-resiliency]，並考量在適當時實作。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-183">Review the [typical design patterns for resiliency][design-patterns-resiliency] and consider implementing these where appropriate.</span></span>

<span data-ttu-id="bd4f7-184">您可以在 Azure 架構中心上找到一些 [App Service 的建議做法][resiliency-app-service]。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-184">You can find a number of [recommended practices for App Service][resiliency-app-service] in the Azure Architecture Center.</span></span>

<span data-ttu-id="bd4f7-185">如需設計彈性解決方案的一般指引，請參閱[為 Azure 設計有彈性的應用程式][resiliency]。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-185">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="bd4f7-186">部署案例</span><span class="sxs-lookup"><span data-stu-id="bd4f7-186">Deploy the scenario</span></span>

### <a name="prerequisites"></a><span data-ttu-id="bd4f7-187">必要條件</span><span class="sxs-lookup"><span data-stu-id="bd4f7-187">Prerequisites</span></span>

* <span data-ttu-id="bd4f7-188">您必須具有現有的 Azure 帳戶。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-188">You must have an existing Azure account.</span></span> <span data-ttu-id="bd4f7-189">如果您沒有 Azure 訂用帳戶，請在開始前建立 [[免費帳戶]][azure-free-account]。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-189">If you don't have an Azure subscription, create a [free account][azure-free-account] before you begin.</span></span>
* <span data-ttu-id="bd4f7-190">您必須具有現有的 Visual Studio Team Services (VSTS) 帳戶。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-190">You must have an existing Visual Studio Team Services (VSTS) account.</span></span> <span data-ttu-id="bd4f7-191">深入了解關於[建立 Visual Studio Team Services (VSTS) 帳戶][vsts-account-create]的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-191">Find out more details about [creating a Visual Studio Team Services (VSTS) account][vsts-account-create].</span></span>

### <a name="walk-through"></a><span data-ttu-id="bd4f7-192">逐步解說</span><span class="sxs-lookup"><span data-stu-id="bd4f7-192">Walk through</span></span>

<span data-ttu-id="bd4f7-193">在此案例中，您將使用 Azure DevOps 專案來建立您的 CI/CD 管線。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-193">In this scenario, you'll use the Azure DevOps Project to create your CI/CD pipeline.</span></span>

<span data-ttu-id="bd4f7-194">DevOps 專案會為您部署 App Service 方案、App Service 和 App Insights 資源，並為您設定 Visual Studio Team Services 專案。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-194">The DevOps project will deploy an App Service Plan, App Service, and an App Insights resource for you, as well as configure the Visual Studio Team Services Project for you.</span></span>

<span data-ttu-id="bd4f7-195">一旦您部署 DevOps 專案並完成建置後，請檢閱相關聯的程式碼變更、工作項目和測試結果。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-195">Once you've deployed the DevOps project and the build is completed, review the associated code changes, work items, and test results.</span></span> <span data-ttu-id="bd4f7-196">您會發現並不會顯示測試結果，因為程式碼不包含任何要執行的測試。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-196">You will notice no test results are displayed, as the code does not contain any tests to run.</span></span>

<span data-ttu-id="bd4f7-197">檢閱發行定義。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-197">Review the Release definitions.</span></span> <span data-ttu-id="bd4f7-198">請注意，發行管線已設定，將我們的應用程式發行至 Dev。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-198">Notice that a release pipeline has been setup, releasing our application into Dev.</span></span> <span data-ttu-id="bd4f7-199">觀察 [置放] 組建成品中有一組**持續部署觸發程序**，並且會自動發行至開發環境。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-199">Observe that there is a **continuous deployment trigger** set from the **Drop** build artifact, with automatic releases into the Dev environments.</span></span> <span data-ttu-id="bd4f7-200">在持續部署程序中，您可能會看到跨多個環境的版本範圍。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-200">As part of a Continuous Deployment process, you may see releases span across multiple environments.</span></span> <span data-ttu-id="bd4f7-201">發行可以跨兩個基礎結構 (使用如基礎結構即程式碼的技術)，還可部署所需的應用程式套件以及任何組態後置工作。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-201">A release can span both infrastructure (using techniques such as Infrastructure as Code), and also deploy the application packages required as well as any post-configuration tasks.</span></span>

<span data-ttu-id="bd4f7-202">**其他考量。**</span><span class="sxs-lookup"><span data-stu-id="bd4f7-202">**Additional Considerations.**</span></span>

* <span data-ttu-id="bd4f7-203">請考慮利用其中一個 VSTS 市集中提供的 [Token 化工作][ vsts-tokenization]。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-203">Consider leveraging one of the [tokenization tasks][vsts-tokenization] that are available in the VSTS marketplace.</span></span>
* <span data-ttu-id="bd4f7-204">請考慮使用[部署：Azure Key Vault][download-keyvault-secrets] VSTS 工作，從 Azure key Vault 將祕密下載到您的發行。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-204">Consider using the [Deploy: Azure Key Vault][download-keyvault-secrets] VSTS task to download secrets from an Azure KeyVault into your release.</span></span> <span data-ttu-id="bd4f7-205">接著，您可以使用這些祕密作為發行定義中的變數，且不應將它們儲存在原始檔控制中。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-205">You can then use those secrets as variables as part of your release definition, and should not be storing them in source control.</span></span>
* <span data-ttu-id="bd4f7-206">請考慮在您的發行定義中使用[發行變數][vsts-release-variables]，以進行您環境的組態變更。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-206">Consider using [release variables][vsts-release-variables] in your release definitions to drive configuration changes of your environments.</span></span> <span data-ttu-id="bd4f7-207">發行變數範圍可以設定為整個發行或指定的環境。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-207">Release variables can be scoped to an entire release or a given environment.</span></span> <span data-ttu-id="bd4f7-208">如果使用祕密資訊的變數，請確定您選取「掛鎖」圖示。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-208">If using variables for secret information, ensure that you select the padlock icon.</span></span>
* <span data-ttu-id="bd4f7-209">請考慮在發行管線中使用[部署閘道][vsts-deployment-gates]。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-209">Consider using [deployment gates][vsts-deployment-gates] in your release pipeline.</span></span> <span data-ttu-id="bd4f7-210">這可讓您運用與外部系統 (例如事件管理或其他定製的系統) 關聯的監視資料，以判斷是否應該升級發行。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-210">This allows you to leverage monitoring data in association with external systems (for example, incident management or additional bespoke systems) to determine whether a release should be promoted.</span></span>
* <span data-ttu-id="bd4f7-211">在需要手動介入的發行管線中，請考慮使用[核准][vsts-approvals]功能。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-211">Where manual intervention in a release pipeline is required, consider using the [approvals][vsts-approvals] functionality.</span></span>
* <span data-ttu-id="bd4f7-212">請考慮盡早在您的發行管線中使用 [Application Insights][application-insights] 和其他監視工具。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-212">Consider using [Application Insights][application-insights] and additional monitoring tooling as early as possible in your release pipeline.</span></span> <span data-ttu-id="bd4f7-213">大部分的組織只開始在其生產環境中進行監視，但您可提前在程序中找出潛在錯誤，並避免在生產環境中對您的使用者造成影響。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-213">Most organizations only begin monitoring in their production environment, though you could identify potential bugs earlier in the process and prevent impact to your users in production.</span></span>

## <a name="pricing"></a><span data-ttu-id="bd4f7-214">價格</span><span class="sxs-lookup"><span data-stu-id="bd4f7-214">Pricing</span></span>

<span data-ttu-id="bd4f7-215">Visual Studio Team Services 成本計算方式取除了所需的並行組建/版次，以及測試使用者的數目等因素之外，決於貴組織中需要存取的使用者數目。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-215">Your Visual Studio Team Services costing will depend upon the number of users in your organization that require access, in addition to factors such as the number of concurrent build/releases required, and number of test users.</span></span> <span data-ttu-id="bd4f7-216">[VSTS 定價頁面][vsts-pricing-page]中提供進一步的詳細說明。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-216">These are detailed further on the [VSTS pricing page][vsts-pricing-page].</span></span>

* <span data-ttu-id="bd4f7-217">[Visual Studio Team Services (VSTS)][vsts-pricing-calculator] 是一項服務，可讓您管理開發生命週期，並且依每位使用者每月的使用量收取費用。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-217">[Visual Studio Team Services (VSTS)][vsts-pricing-calculator] is a service that enables you to manage your development life cycle and is paid for on a per user, per month basis.</span></span> <span data-ttu-id="bd4f7-218">除了任何額外的測試使用者，或使用者的基本授權之外，根據並行管線，可能還有額外的費用。</span><span class="sxs-lookup"><span data-stu-id="bd4f7-218">There may be additional charges dependent upon concurrent pipelines needed, in addition to any additional test users, or user basic licenses.</span></span>

## <a name="related-resources"></a><span data-ttu-id="bd4f7-219">相關資源</span><span class="sxs-lookup"><span data-stu-id="bd4f7-219">Related Resources</span></span>

* <span data-ttu-id="bd4f7-220">[什麼是 DevOps？][devops-whatis]</span><span class="sxs-lookup"><span data-stu-id="bd4f7-220">[What is DevOps?][devops-whatis]</span></span>
* <span data-ttu-id="bd4f7-221">[Microsoft 中的 DevOps - 我們使用 Visual Studio Team Services 的方式][devops-microsoft]</span><span class="sxs-lookup"><span data-stu-id="bd4f7-221">[DevOps at Microsoft - How we work with Visual Studio Team Services][devops-microsoft]</span></span>
* <span data-ttu-id="bd4f7-222">[逐步教學課程︰使用 Visual Studio Team Services 的 DevOps][devops-with-vsts]</span><span class="sxs-lookup"><span data-stu-id="bd4f7-222">[Step-by-step Tutorials: DevOps with Visual Studio Team Services][devops-with-vsts]</span></span>
* <span data-ttu-id="bd4f7-223">[使用 Azure DevOps Project 建立適用於 .NET 的 CI/CD 管線][devops-project-create]</span><span class="sxs-lookup"><span data-stu-id="bd4f7-223">[Create a CI/CD pipeline for .NET with the Azure DevOps project][devops-project-create]</span></span>

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
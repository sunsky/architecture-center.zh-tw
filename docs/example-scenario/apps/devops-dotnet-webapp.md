---
title: 利用 Azure DevOps 的 CI/CD 管線
description: 使用 Azure DevOps 建置 .NET 應用程式並且發行至 Azure Web Apps。
author: christianreddington
ms.date: 07/11/18
ms.openlocfilehash: 97f16b2d3d9c15bc6f5db6fad4c9d8097243ad3d
ms.sourcegitcommit: 0a31fad9b68d54e2858314ca5fe6cba6c6b95ae4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51610782"
---
# <a name="cicd-pipeline-with-azure-devops"></a><span data-ttu-id="1c3b4-103">利用 Azure DevOps 的 CI/CD 管線</span><span class="sxs-lookup"><span data-stu-id="1c3b4-103">CI/CD pipeline with Azure DevOps</span></span>

<span data-ttu-id="1c3b4-104">DevOps 是開發、品質保證和 IT 作業的整合。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-104">DevOps is the integration of development, quality assurance, and IT operations.</span></span> <span data-ttu-id="1c3b4-105">DevOps 需要統一的文化特性，以及一組功能強大的程序來提供軟體。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-105">DevOps requires both unified culture and a strong set of processes for delivering software.</span></span>

<span data-ttu-id="1c3b4-106">此範例案例示範開發小組可如何使用 Azure DevOps 將 .NET 兩層式 Web 應用程式部署至 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-106">This example scenario demonstrates how development teams can use Azure DevOps to deploy a .NET two-tier web application to Azure App Service.</span></span> <span data-ttu-id="1c3b4-107">Web 應用程式是依據下游的 Azure 平台即服務 (PaaS) 服務。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-107">The Web Application depends on downstream Azure platform as a service (PaaS) services.</span></span> <span data-ttu-id="1c3b4-108">這份文件也將點出您在使用 Azure PaaS 設計這類案例時，所應該進行的一些考量。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-108">This document also points out some considerations that you should make when designing such a scenario using Azure PaaS.</span></span>

<span data-ttu-id="1c3b4-109">使用持續整合及持續部署 (CI/CD) 對應用程式開發採用新式方法，可協助您透過穩固的建置、測試、部署和監視服務，加速傳遞價值給您的使用者。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-109">Adopting a modern approach to application development using Continuous Integration and Continuous Deployment (CI/CD), helps you to accelerate the delivery of value to your users through a robust build, test, deployment, and monitoring service.</span></span> <span data-ttu-id="1c3b4-110">使用 Azure DevOps 等平台搭配 App Service 等 Azure 服務，組織即可專注於其案例的開發，而不是支援基礎結構的管理。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-110">By using a platform such as Azure DevOps along with Azure services such as App Service, organizations can focus on the development of their scenario rather than the management of the supporting infrastructure.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="1c3b4-111">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="1c3b4-111">Relevant use cases</span></span>

<span data-ttu-id="1c3b4-112">請針對下列使用案例考慮 DevOps：</span><span class="sxs-lookup"><span data-stu-id="1c3b4-112">Consider DevOps for the following use cases:</span></span>

* <span data-ttu-id="1c3b4-113">加速應用程式開發和部署生命週期</span><span class="sxs-lookup"><span data-stu-id="1c3b4-113">Accelerating application development and development life cycles</span></span>
* <span data-ttu-id="1c3b4-114">將品質與一致性建置到自動化的組建與發行程序</span><span class="sxs-lookup"><span data-stu-id="1c3b4-114">Building quality and consistency into an automated build and release process</span></span>

## <a name="architecture"></a><span data-ttu-id="1c3b4-115">架構</span><span class="sxs-lookup"><span data-stu-id="1c3b4-115">Architecture</span></span>

![DevOps 案例 (使用 Azure DevOps 和 Azure App Service) 中所牽涉到 Azure 元件的架構概觀][architecture]

<span data-ttu-id="1c3b4-117">此案例中討論的 CI/CD 管線適用於使用 Azure DevOps 的 .NET Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-117">This scenario covers a CI/CD pipeline for a .NET web application using Azure DevOps.</span></span> <span data-ttu-id="1c3b4-118">整個案例的資料流程如下所示：</span><span class="sxs-lookup"><span data-stu-id="1c3b4-118">The data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="1c3b4-119">變更應用程式原始程式碼。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-119">Change application source code.</span></span>
2. <span data-ttu-id="1c3b4-120">認可應用程式的程式碼與 Web Apps 的 web.config 檔案。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-120">Commit application code and Web Apps web.config file.</span></span>
3. <span data-ttu-id="1c3b4-121">持續整合會觸發應用程式組建與單元測試。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-121">Continuous integration triggers application build and unit tests.</span></span>
4. <span data-ttu-id="1c3b4-122">持續部署觸發程序會協調應用程式構件的「部署與環境專屬的參數化組態值」。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-122">Continuous deployment trigger orchestrates deployment of application artifacts *with environment-specific parameterized configuration values*.</span></span>
5. <span data-ttu-id="1c3b4-123">部署至 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-123">Deployment to Azure App Service.</span></span>
6. <span data-ttu-id="1c3b4-124">Azure Application Insights 會收集與分析健康情況、效能及使用方式資料。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-124">Azure Application Insights collects and analyzes health, performance, and usage data.</span></span>
7. <span data-ttu-id="1c3b4-125">檢閱健康情況、效能及使用方式資訊。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-125">Review health, performance, and usage information.</span></span>

### <a name="components"></a><span data-ttu-id="1c3b4-126">元件</span><span class="sxs-lookup"><span data-stu-id="1c3b4-126">Components</span></span>

* <span data-ttu-id="1c3b4-127">[Azure DevOps][vsts] 是一項服務，可讓您管理端對端開發生命週期 &mdash; 從規劃和專案管理、程式碼管理，一直到建置及發行。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-127">[Azure DevOps][vsts] is a service for managing your development life cycle end-to-end &mdash; from planning and project management, to code management, and continuing to build and release.</span></span>
* <span data-ttu-id="1c3b4-128">[Azure Web Apps][web-apps] 是用來裝載 Web 應用程式、REST API 和行動後端的 PaaS 服務。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-128">[Azure Web Apps][web-apps] is a PaaS service for hosting web applications, REST APIs, and mobile back ends.</span></span> <span data-ttu-id="1c3b4-129">雖然本文著重於 .NET，但也支援數個其他開發平台選項。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-129">While this article focuses on .NET, there are several additional development platform options supported.</span></span>
* <span data-ttu-id="1c3b4-130">[Application Insights][application-insights] 是多個平台上的 Web 開發人員所適用的第一方、可延伸「應用程式效能管理」(APM) 服務。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-130">[Application Insights][application-insights] is a first-party, extensible Application Performance Management (APM) service for web developers on multiple platforms.</span></span>

### <a name="alternative-devops-tooling-options"></a><span data-ttu-id="1c3b4-131">替代的 DevOps 工具選項</span><span class="sxs-lookup"><span data-stu-id="1c3b4-131">Alternative DevOps tooling options</span></span>

<span data-ttu-id="1c3b4-132">本文著重於 Azure DevOps，但 [Team Foundation Server][team-foundation-server] 可用來替代內部部署。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-132">While this article focuses on Azure DevOps, [Team Foundation Server][team-foundation-server] could be used as on-premises substitute.</span></span> <span data-ttu-id="1c3b4-133">或者，您也可以將一組技術運用於使用 [Jenkins][jenkins-on-azure] 的開放原始碼開發管線。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-133">Alternatively, you could also use a set of technologies for an open source development pipeline using [Jenkins][jenkins-on-azure].</span></span>

<span data-ttu-id="1c3b4-134">就基礎結構即程式碼的觀點而言，會包含 [Azure Resource Manager 範本][arm-templates]作為 Azure DevOps 專案，則可以考慮 [Terraform][terraform] 或 [Chef][chef]。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-134">From an infrastructure-as-code perspective, [Azure Resource Manager Templates][arm-templates] are included as part of the Azure DevOps project, but you could consider [Terraform][terraform] or [Chef][chef].</span></span> <span data-ttu-id="1c3b4-135">如果您偏好基礎結構即服務 (IaaS) 為基礎的部署，且需要組態管理，則可考慮 [Azure Automation State Configuration][desired-state-configuration]、[Ansible][ansible] 或 [Chef][chef]。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-135">If you prefer an infrastructure-as-a-service (IaaS)-based deployment and require configuration management, you could consider either [Azure Automation State Configuration][desired-state-configuration], [Ansible][ansible], or [Chef][chef].</span></span>

### <a name="alternatives-to-azure-web-apps"></a><span data-ttu-id="1c3b4-136">Azure Web Apps 的替代項目</span><span class="sxs-lookup"><span data-stu-id="1c3b4-136">Alternatives to Azure Web Apps</span></span>

<span data-ttu-id="1c3b4-137">您可以考慮在 Azure Web Apps 中裝載這些替代項目：</span><span class="sxs-lookup"><span data-stu-id="1c3b4-137">You could consider these alternatives to hosting in Azure Web Apps:</span></span>

* <span data-ttu-id="1c3b4-138">[Azure 虛擬機器][compare-vm-hosting]&mdash; - 適用於需要高程度控制的工作負載，或依賴無法使用 Web Apps 的作業系統元件和服務 (例如 Windows GAC 或 COM) 的工作負載。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-138">[Azure Virtual Machines][compare-vm-hosting] &mdash; For workloads that require a high degree of control, or depend on OS components and services that are not possible with Web Apps (for example, the Windows GAC, or COM).</span></span>
* <span data-ttu-id="1c3b4-139">[Service Fabric][service-fabric] &mdash; 如果工作負載架構著重於建置分散式元件，可受益於在具有高度控制權的叢集之間部署及執行，則適用這個選項。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-139">[Service Fabric][service-fabric] &mdash; a good option if the workload architecture is focused around distributed components that benefit from being deployed and run across a cluster with a high degree of control.</span></span> <span data-ttu-id="1c3b4-140">Service Fabric 也可用來裝載容器。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-140">Service Fabric can also be used to host containers.</span></span>
* <span data-ttu-id="1c3b4-141">[Azure Functions][azure-functions] - 如果工作負載架構著重於細微的分散式元件，需要最低相依性，其中個別元件只需要隨需執行 (不連續)，且不需要元件的協調流程，則可使用此有效的無伺服器方法。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-141">[Azure Functions][azure-functions] - an effective serverless approach if the workload architecture is centered around fine grained distributed components, requiring minimal dependencies, where individual components are only required to run on demand (not continuously) and orchestration of components is not required.</span></span>

<span data-ttu-id="1c3b4-142">在選擇正確的移轉路徑時，此[決策樹](/azure/architecture/guide/technology-choices/compute-decision-tree)可能有所幫助。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-142">This [decision tree](/azure/architecture/guide/technology-choices/compute-decision-tree) may help when choosing the right path to take for a migration.</span></span>

### <a name="devops"></a><span data-ttu-id="1c3b4-143">DevOps</span><span class="sxs-lookup"><span data-stu-id="1c3b4-143">DevOps</span></span>

<span data-ttu-id="1c3b4-144">**[持續整合 (CI)][continuous-integration]** 會透過多個開發人員持續對共用程式碼基底進行小型、頻繁的變更，以維護穩定的組建。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-144">**[Continuous Integration (CI)][continuous-integration]** maintains a stable build, with multiple developers regularly committing small, frequent changes to the shared codebase.</span></span> <span data-ttu-id="1c3b4-145">在持續整合管線過程中，您應該：</span><span class="sxs-lookup"><span data-stu-id="1c3b4-145">As part of your continuous integration pipeline, you should:</span></span>
* <span data-ttu-id="1c3b4-146">經常認可較小的程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-146">Frequently commit smaller code changes.</span></span> <span data-ttu-id="1c3b4-147">避免批次處理可能難以功合併的較大或較複雜變更。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-147">Avoid batching up larger or more complex changes that may be more difficult to merge successfully.</span></span>
* <span data-ttu-id="1c3b4-148">利用足夠的程式碼涵蓋範圍，進行應用程式元件的單元測試，包括測試不滿意的路徑。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-148">Conduct unit testing of your application components with sufficient code coverage, including testing the unhappy paths.</span></span>
* <span data-ttu-id="1c3b4-149">確保針對共用的主要 (或主幹) 分支執行建置。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-149">Ensure the build is run against the shared master (or trunk) branch.</span></span> <span data-ttu-id="1c3b4-150">此分支應該很穩定，且保持為「部署就緒」。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-150">This branch should be stable and maintained as "deployment ready".</span></span> <span data-ttu-id="1c3b4-151">不完整或工作進行中的變更應該隔離在個別的分支中，並且經常進行「正向整合」的合併，以避免日後發生衝突。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-151">Incomplete or work-in-progress changes should be isolated in a separate branch with frequent "forward integration" merges to avoid conflicts later.</span></span>

<span data-ttu-id="1c3b4-152">**[持續傳遞 (CD)][continuous-delivery]** 不僅會展示穩定的組建，還會展示穩定的部署。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-152">**[Continuous Delivery (CD)][continuous-delivery]** demonstrates not just a stable build but a stable deployment.</span></span> <span data-ttu-id="1c3b4-153">這會使 CD 較難以實現，需要環境特定的設定，且正確設定這些值的機制。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-153">This makes realizing CD a little more difficult, requiring environment-specific configuration and a mechanism for setting those values correctly.</span></span> <span data-ttu-id="1c3b4-154">其他 CD 考量包括下列各項：</span><span class="sxs-lookup"><span data-stu-id="1c3b4-154">Other CD considerations include the following:</span></span>
* <span data-ttu-id="1c3b4-155">需要足夠的整合測試涵蓋範圍，以驗證是否已設定各種元件，以及端對端是否正確運作。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-155">Sufficient integration testing coverage is required to validate that the various components are configured and working correctly end-to-end.</span></span>
* <span data-ttu-id="1c3b4-156">CD 可能也需要設定及重設環境特定的資料，以及管理資料庫結構描述版本。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-156">CD may also require setting up and resetting environment-specific data and managing database schema versions.</span></span>
* <span data-ttu-id="1c3b4-157">持續傳遞也應該延伸至負載測試和使用者驗收測試環境。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-157">Continuous delivery should also extend to load testing and user acceptance testing environments.</span></span>
* <span data-ttu-id="1c3b4-158">持續傳遞受惠於連續監視，理想情況為所有環境。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-158">Continuous delivery benefits from continuous monitoring, ideally across all environments.</span></span>
* <span data-ttu-id="1c3b4-159">藉由編寫裝載基礎結構的建立和設定指令碼，讓您在各環境間的部署及整合測試更輕鬆獲得一致性和可靠性。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-159">The consistency and reliability of deployments and integration testing across environments is made easier by scripting the creation and configuration of the hosting infrastructure.</span></span> <span data-ttu-id="1c3b4-160">這對雲端式工作負載而言簡單許多。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-160">This is considerably easier for cloud-based workloads.</span></span> <span data-ttu-id="1c3b4-161">如需詳細資訊，請參閱[基礎結構即程式碼][infra-as-code]。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-161">For more information, see [Infrastructure as Code][infra-as-code].</span></span>
* <span data-ttu-id="1c3b4-162">在專案生命週期中盡早開始持續傳遞。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-162">Begin continuous delivery as early as possible in the project lifecycle.</span></span> <span data-ttu-id="1c3b4-163">越晚開始，就越難以合併。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-163">The later you begin, the more difficult it will be to incorporate.</span></span>
* <span data-ttu-id="1c3b4-164">應該提供與應用程式功能相同的優先順序給整合及單元測試。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-164">Integration and unit tests should be given the same priority as application features.</span></span>
* <span data-ttu-id="1c3b4-165">使用環境無從驗證的部署套件，及透過發行程序管理環境特定的設定。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-165">Use environment-agnostic deployment packages and manage environment-specific configuration via the release process.</span></span>
* <span data-ttu-id="1c3b4-166">在發行過程中，使用發行管理工具，或向外呼叫硬體安全模組 (HSM) 或 [Azure Key Vault][azure-key-vault]，以保護敏感性組態。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-166">Protect sensitive configuration using the release management tooling, or by calling out to a Hardware-security-module (HSM) or [Azure Key Vault][azure-key-vault] during the release process.</span></span> <span data-ttu-id="1c3b4-167">請勿在原始檔控制中儲存機密組態。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-167">Do not store sensitive configuration within source control.</span></span>

<span data-ttu-id="1c3b4-168">**持續學習**。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-168">**Continuous Learning**.</span></span> <span data-ttu-id="1c3b4-169">CD 環境最有效的監視是由應用程式效能監視 (APM) 工具所提供，例如 [Application Insights][application-insights]。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-169">The most effective monitoring of a CD environment is provided by application performance monitoring (APM) tools such as [Application Insights][application-insights].</span></span> <span data-ttu-id="1c3b4-170">若要了解錯誤或輕載效能，請務必對應用程式工作負載監視具有足夠的深入解析。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-170">Sufficient depth of monitoring for an application workload is critical to understand bugs or performance under load.</span></span> <span data-ttu-id="1c3b4-171">Application Insights 可以整合到 VSTS，以啟用[持續監視 CD 管線][app-insights-cd-monitoring]。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-171">Application Insights can be integrated into VSTS to enable [continuous monitoring of the CD pipeline][app-insights-cd-monitoring].</span></span> <span data-ttu-id="1c3b4-172">這可用來啟用自動進展至下一個階段，如果偵測到警示，不需要人為介入或復原。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-172">This could be used to enable automatic progression to the next stage, without human intervention, or rollback if an alert is detected.</span></span>

## <a name="considerations"></a><span data-ttu-id="1c3b4-173">考量</span><span class="sxs-lookup"><span data-stu-id="1c3b4-173">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="1c3b4-174">可用性</span><span class="sxs-lookup"><span data-stu-id="1c3b4-174">Availability</span></span>

<span data-ttu-id="1c3b4-175">建置您的雲端應用程式時，請考量利用[獲得可用性的典型設計模式][design-patterns-availability]。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-175">Consider leveraging the [typical design patterns for availability][design-patterns-availability] when building your cloud application.</span></span>

<span data-ttu-id="1c3b4-176">在適當的 [App Service Web 應用程式參考架構][app-service-reference-architecture]中檢閱可用性考量</span><span class="sxs-lookup"><span data-stu-id="1c3b4-176">Review the availability considerations in the appropriate [App Service web application reference architecture][app-service-reference-architecture]</span></span>

<span data-ttu-id="1c3b4-177">如需其他可用性主題，請參閱 Azure Architecture Center 中的[可用性檢查清單][availability]。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-177">For other availability topics, see the [availability checklist][availability] in the Azure Architecture Center.</span></span>

### <a name="scalability"></a><span data-ttu-id="1c3b4-178">延展性</span><span class="sxs-lookup"><span data-stu-id="1c3b4-178">Scalability</span></span>

<span data-ttu-id="1c3b4-179">建置雲端應用程式時，請了解[獲得延展性的典型設計模式][design-patterns-scalability]。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-179">When building a cloud application be aware of the [typical design patterns for scalability][design-patterns-scalability].</span></span>

<span data-ttu-id="1c3b4-180">在適當的 [App Service Web 應用程式參考架構][app-service-reference-architecture]中檢閱延展性考量</span><span class="sxs-lookup"><span data-stu-id="1c3b4-180">Review the scalability considerations in the appropriate [App Service web application reference architecture][app-service-reference-architecture]</span></span>

<span data-ttu-id="1c3b4-181">如需其他延展性主題，請參閱 Azure Architecture Center 中的[延展性檢查清單][scalability]。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-181">For other scalability topics, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="1c3b4-182">安全性</span><span class="sxs-lookup"><span data-stu-id="1c3b4-182">Security</span></span>

<span data-ttu-id="1c3b4-183">請在適用時考量利用[獲得安全性的典型設計模式][design-patterns-security]。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-183">Consider leveraging the [typical design patterns for security][design-patterns-security] where appropriate.</span></span>

<span data-ttu-id="1c3b4-184">在適當的 [App Service Web 應用程式參考架構][app-service-reference-architecture]中檢閱安全性考量。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-184">Review the security considerations in the appropriate [App Service web application reference architecture][app-service-reference-architecture].</span></span>

<span data-ttu-id="1c3b4-185">如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-185">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="1c3b4-186">災害復原</span><span class="sxs-lookup"><span data-stu-id="1c3b4-186">Resiliency</span></span>

<span data-ttu-id="1c3b4-187">考量在適當時實作[復原的典型設計模式][design-patterns-resiliency]。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-187">Consider implementing the [typical design patterns for resiliency][design-patterns-resiliency] where appropriate.</span></span>

<span data-ttu-id="1c3b4-188">您可以在 Azure 架構中心上找到一些 [App Service 的建議做法][resiliency-app-service]。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-188">You can find a number of [recommended practices for App Service][resiliency-app-service] in the Azure Architecture Center.</span></span>

<span data-ttu-id="1c3b4-189">如需設計彈性解決方案的一般指引，請參閱[為 Azure 設計有彈性的應用程式][resiliency]。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-189">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="1c3b4-190">部署案例</span><span class="sxs-lookup"><span data-stu-id="1c3b4-190">Deploy the scenario</span></span>

### <a name="prerequisites"></a><span data-ttu-id="1c3b4-191">必要條件</span><span class="sxs-lookup"><span data-stu-id="1c3b4-191">Prerequisites</span></span>

* <span data-ttu-id="1c3b4-192">您必須具有現有的 Azure 帳戶。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-192">You must have an existing Azure account.</span></span> <span data-ttu-id="1c3b4-193">如果您沒有 Azure 訂用帳戶，請在開始前建立 [[免費帳戶]][azure-free-account]。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-193">If you don't have an Azure subscription, create a [free account][azure-free-account] before you begin.</span></span>
* <span data-ttu-id="1c3b4-194">您必須註冊 Azure DevOps 組織。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-194">You must sign up for an Azure DevOps organization.</span></span> <span data-ttu-id="1c3b4-195">如需詳細資訊，請參閱[快速入門：建立您的組織][vsts-account-create]。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-195">For more information, see [Quickstart: Create your organization][vsts-account-create].</span></span>

### <a name="walk-through"></a><span data-ttu-id="1c3b4-196">逐步解說</span><span class="sxs-lookup"><span data-stu-id="1c3b4-196">Walk-through</span></span>

<span data-ttu-id="1c3b4-197">在此案例中，您將使用 Azure DevOps 專案來建立您的 CI/CD 管線。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-197">In this scenario, you'll use the Azure DevOps project to create your CI/CD pipeline.</span></span>

<span data-ttu-id="1c3b4-198">Azure DevOps 專案會為您部署 App Service 方案、App Service 和 App Insights 資源，並為您設定 Azure DevOps 專案。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-198">The Azure DevOps project will deploy an App Service Plan, App Service, and an App Insights resource for you, as well as configure the Azure DevOps project for you.</span></span>

<span data-ttu-id="1c3b4-199">一旦您部署 Azure DevOps 專案並完成建置後，請檢閱相關聯的程式碼變更、工作項目和測試結果。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-199">Once you've deployed the Azure DevOps project and the build is completed, review the associated code changes, work items, and test results.</span></span> <span data-ttu-id="1c3b4-200">您會發現並不會顯示測試結果，因為程式碼不包含任何要執行的測試。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-200">You will notice that no test results are displayed, because the code does not contain any tests to run.</span></span>

<span data-ttu-id="1c3b4-201">檢閱發行定義。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-201">Review the release definitions.</span></span> <span data-ttu-id="1c3b4-202">請注意，發行管線已設定，將我們的應用程式發行至開發環境。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-202">Notice that a release pipeline has been set up, releasing our application into the Dev environment.</span></span> <span data-ttu-id="1c3b4-203">觀察 [置放] 組建成品中有一組**持續部署觸發程序**，並且會自動發行至開發環境。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-203">Observe that there is a **continuous deployment trigger** set from the **Drop** build artifact, with automatic releases into the Dev environment.</span></span> <span data-ttu-id="1c3b4-204">在持續部署過程中，您可能會看到跨多個環境的版本。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-204">As part of a continuous deployment process, you may see releases that span multiple environments.</span></span> <span data-ttu-id="1c3b4-205">發行可以跨兩個基礎結構 (使用如基礎結構即程式碼的技術)，還可部署所需的應用程式套件以及任何設定後工作。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-205">A release can span both infrastructure (using techniques such as infrastructure-as-code), and can also deploy the application packages required along with any post-configuration tasks.</span></span>

## <a name="additional-considerations"></a><span data-ttu-id="1c3b4-206">其他考量</span><span class="sxs-lookup"><span data-stu-id="1c3b4-206">Additional considerations</span></span>

* <span data-ttu-id="1c3b4-207">請考慮利用其中一個 VSTS 市集中提供的 [Token 化工作][vsts-tokenization]。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-207">Consider leveraging one of the [tokenization tasks][vsts-tokenization] available in the VSTS marketplace.</span></span>
* <span data-ttu-id="1c3b4-208">請考慮使用[部署：Azure Key Vault][download-keyvault-secrets] VSTS 工作，從 Azure key Vault 將祕密下載到您的發行。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-208">Consider using the [Deploy: Azure Key Vault][download-keyvault-secrets] VSTS task to download secrets from an Azure Key Vault into your release.</span></span> <span data-ttu-id="1c3b4-209">接著，您可以使用這些祕密作為發行定義中的變數，即可避免將它們儲存在原始檔控制中。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-209">You can then use those secrets as variables in your release definition, so you can avoid storing them in source control.</span></span>
* <span data-ttu-id="1c3b4-210">請考慮在您的發行定義中使用[發行變數][vsts-release-variables]，以進行您環境的組態變更。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-210">Consider using [release variables][vsts-release-variables] in your release definitions to drive configuration changes of your environments.</span></span> <span data-ttu-id="1c3b4-211">發行變數範圍可以設定為整個發行或指定的環境。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-211">Release variables can be scoped to an entire release or a given environment.</span></span> <span data-ttu-id="1c3b4-212">使用祕密資訊的變數時，請確定您選取「掛鎖」圖示。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-212">When using variables for secret information, ensure that you select the padlock icon.</span></span>
* <span data-ttu-id="1c3b4-213">請考慮在發行管線中使用[部署閘道][vsts-deployment-gates]。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-213">Consider using [deployment gates][vsts-deployment-gates] in your release pipeline.</span></span> <span data-ttu-id="1c3b4-214">這可讓您運用與外部系統 (例如事件管理或其他定製的系統) 關聯的監視資料，以判斷是否應該升級發行。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-214">This lets you leverage monitoring data in association with external systems (for example, incident management or additional bespoke systems) to determine whether a release should be promoted.</span></span>
* <span data-ttu-id="1c3b4-215">在需要手動介入的發行管線中，請考慮使用[核准][vsts-approvals]功能。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-215">Where manual intervention in a release pipeline is required, consider using the [approvals][vsts-approvals] functionality.</span></span>
* <span data-ttu-id="1c3b4-216">請考慮盡早在您的發行管線中使用 [Application Insights][application-insights] 和其他監視工具。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-216">Consider using [Application Insights][application-insights] and additional monitoring tools as early as possible in your release pipeline.</span></span> <span data-ttu-id="1c3b4-217">許多組織只會開始監視其生產環境；您可藉由監視您的其他環境，以找出開發程序中稍早的錯誤，並且避免生產環境中的問題。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-217">Many organizations only begin monitoring in their production environment; by monitoring your other environments, you can identify bugs earlier in the development process and avoid issues in your production environment.</span></span>

## <a name="pricing"></a><span data-ttu-id="1c3b4-218">價格</span><span class="sxs-lookup"><span data-stu-id="1c3b4-218">Pricing</span></span>

<span data-ttu-id="1c3b4-219">Azure DevOps 成本取決於貴組織中需要存取的使用者數目，以及所需的並行組建/版次數目和測試使用者數目等其他因素。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-219">Azure DevOps costs depend on the number of users in your organization that require access, along with other factors like the number of concurrent build/releases required and number of test users.</span></span> <span data-ttu-id="1c3b4-220">如需詳細資訊，請參閱 [Azure DevOps 定價][vsts-pricing-page]。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-220">For more information, see [Azure DevOps pricing][vsts-pricing-page].</span></span>

* <span data-ttu-id="1c3b4-221">[Azure DevOps][vsts-pricing-calculator] 是一項服務，可讓您管理開發生命週期。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-221">[Azure DevOps][vsts-pricing-calculator] is a service that enables you to manage your development life cycle.</span></span> <span data-ttu-id="1c3b4-222">它是依每個月的每位使用者付費。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-222">It is paid for on a per-user per-month basis.</span></span> <span data-ttu-id="1c3b4-223">除了任何額外的測試使用者，或使用者的基本授權之外，根據並行管線，可能還有額外的費用。</span><span class="sxs-lookup"><span data-stu-id="1c3b4-223">There may be additional charges dependent upon concurrent pipelines needed, in addition to any additional test users or user basic licenses.</span></span>

## <a name="related-resources"></a><span data-ttu-id="1c3b4-224">相關資源</span><span class="sxs-lookup"><span data-stu-id="1c3b4-224">Related resources</span></span>

* <span data-ttu-id="1c3b4-225">[什麼是 DevOps？][devops-whatis]</span><span class="sxs-lookup"><span data-stu-id="1c3b4-225">[What is DevOps?][devops-whatis]</span></span>
* <span data-ttu-id="1c3b4-226">[Microsoft 的 DevOps - 如何使用 Azure DevOps][devops-microsoft]</span><span class="sxs-lookup"><span data-stu-id="1c3b4-226">[DevOps at Microsoft - How we work with Azure DevOps][devops-microsoft]</span></span>
* <span data-ttu-id="1c3b4-227">[逐步教學課程︰使用 Azure DevOps 的 DevOps][devops-with-vsts]</span><span class="sxs-lookup"><span data-stu-id="1c3b4-227">[Step-by-step Tutorials: DevOps with Azure DevOps][devops-with-vsts]</span></span>
* <span data-ttu-id="1c3b4-228">[DevOps 檢查清單][devops-checklist]</span><span class="sxs-lookup"><span data-stu-id="1c3b4-228">[Devops Checklist][devops-checklist]</span></span>
* <span data-ttu-id="1c3b4-229">[使用 Azure DevOps Project 建立適用於 .NET 的 CI/CD 管線][devops-project-create]</span><span class="sxs-lookup"><span data-stu-id="1c3b4-229">[Create a CI/CD pipeline for .NET with the Azure DevOps project][devops-project-create]</span></span>

<!-- links -->
[ansible]: /azure/ansible/
[application-insights]: /azure/application-insights/app-insights-overview
[app-service-reference-architecture]: ../../reference-architectures/app-service-web-app/basic-web-app.md
[azure-free-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[arm-templates]: /azure/azure-resource-manager/resource-group-overview#template-deployment
[architecture]: ./media/architecture-devops-dotnet-webapp.png
[availability]: /azure/architecture/checklist/availability
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
[resiliency]: /azure/architecture/checklist/resiliency
[scalability]: /azure/architecture/checklist/scalability
[vsts]: /vsts/?view=vsts#pivot=services
[continuous-integration]: /azure/devops/what-is-continuous-integration
[continuous-delivery]: /azure/devops/what-is-continuous-delivery
[web-apps]: /azure/app-service/app-service-web-overview
[terraform]: /azure/terraform/
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
[team-foundation-server]: https://visualstudio.microsoft.com/tfs/
[infra-as-code]: https://blogs.msdn.microsoft.com/mvpawardprogram/2018/02/13/infrastructure-as-code/
[service-fabric]: /azure/service-fabric/
[azure-functions]: /azure/azure-functions/
[azure-containers]: https://azure.microsoft.com/overview/containers/
[compare-vm-hosting]: /azure/app-service/choose-web-site-cloud-service-vm
[app-insights-cd-monitoring]: /azure/application-insights/app-insights-vsts-continuous-monitoring
[azure-region-pair-bcdr]: /azure/best-practices-availability-paired-regions
[devops-project-create]: /azure/devops-project/azure-devops-project-aspnet-core
[security]: /azure/security/
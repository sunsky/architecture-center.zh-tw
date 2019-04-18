---
title: 部署 Azure 應用程式的恢復功能和可用性
description: 在 Azure 中建立可靠的部署技術的概觀
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: d8df3fe1c3353c60462959a625ba13ae47b96cf8
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59646515"
---
# <a name="deploying-azure-applications-for-resiliency-and-availability"></a><span data-ttu-id="13f4f-103">部署 Azure 應用程式的恢復功能和可用性</span><span class="sxs-lookup"><span data-stu-id="13f4f-103">Deploying Azure applications for resiliency and availability</span></span>

<span data-ttu-id="13f4f-104">當您佈建，並更新 Azure 資源、 應用程式程式碼和組態設定，重複且可預測的程序將協助您避免錯誤和停機時間。</span><span class="sxs-lookup"><span data-stu-id="13f4f-104">As you provision and update Azure resources, application code, and configuration settings, a repeatable and predictable process will help you avoid errors and downtime.</span></span> <span data-ttu-id="13f4f-105">我們建議您可以視需要執行，並重新執行，如果發生失敗的部署自動化的程序。</span><span class="sxs-lookup"><span data-stu-id="13f4f-105">We recommend automated processes for deployment that you can run on demand and rerun if something fails.</span></span> <span data-ttu-id="13f4f-106">您的部署程序執行個體之後，處理程序的文件可以讓它們保持如此。</span><span class="sxs-lookup"><span data-stu-id="13f4f-106">After your deployment processes are running smoothly, process documentation can keep them that way.</span></span>

## <a name="automate-processes"></a><span data-ttu-id="13f4f-107">自動化程序</span><span class="sxs-lookup"><span data-stu-id="13f4f-107">Automate processes</span></span>

<span data-ttu-id="13f4f-108">若要啟用隨選資源、 快速部署解決方案、 減少人為錯誤，並產生一致且可重複的結果，請務必以自動化部署和更新。</span><span class="sxs-lookup"><span data-stu-id="13f4f-108">To activate resources on demand, deploy solutions rapidly, minimize human error, and produce consistent and repeatable results, be sure to automate deployments and updates.</span></span>

### <a name="automate-as-many-processes-as-possible"></a><span data-ttu-id="13f4f-109">盡可能自動化許多程序</span><span class="sxs-lookup"><span data-stu-id="13f4f-109">Automate as many processes as possible</span></span>

<span data-ttu-id="13f4f-110">最可靠的部署程序自動化及*等冪*&mdash;也就是重複產生相同的結果。</span><span class="sxs-lookup"><span data-stu-id="13f4f-110">The most reliable deployment processes are automated and *idempotent* &mdash; that is, repeatable to produce the same results.</span></span>

- <span data-ttu-id="13f4f-111">若要自動化佈建 Azure 資源，您可以使用[Terraform](/azure/virtual-machines/windows/infrastructure-automation#terraform)， [Ansible](/azure/virtual-machines/windows/infrastructure-automation#ansible)， [Chef](/azure/virtual-machines/windows/infrastructure-automation#chef)， [Puppet](/azure/virtual-machines/windows/infrastructure-automation#puppet)， [AzurePowerShell](/powershell/azure/overview)， [Azure 命令列介面 (CLI)](/cli/azure)，或 [Azure Resource Manager 範本](/azure/azure-resource-manager/resource-group-overview#template-deployment)。</span><span class="sxs-lookup"><span data-stu-id="13f4f-111">To automate provisioning of Azure resources, you can use [Terraform](/azure/virtual-machines/windows/infrastructure-automation#terraform),   [Ansible](/azure/virtual-machines/windows/infrastructure-automation#ansible), [Chef](/azure/virtual-machines/windows/infrastructure-automation#chef), [Puppet](/azure/virtual-machines/windows/infrastructure-automation#puppet),   [Azure PowerShell](/powershell/azure/overview), [Azure command-line interface (CLI)](/cli/azure), or [Azure Resource Manager templates](/azure/azure-resource-manager/resource-group-overview#template-deployment).</span></span>
- <span data-ttu-id="13f4f-112">若要設定的 Vm，您可以使用 [為使用 cloud-init](/azure/virtual-machines/windows/infrastructure-automation#cloud-init) （適用於 Linux Vm) 或 [Azure 自動化狀態設定](/azure/automation/automation-dsc-overview)(DSC)。</span><span class="sxs-lookup"><span data-stu-id="13f4f-112">To configure VMs, you can use [cloud-init](/azure/virtual-machines/windows/infrastructure-automation#cloud-init) (for Linux VMs) or [Azure Automation State Configuration](/azure/automation/automation-dsc-overview) (DSC).</span></span>
- <span data-ttu-id="13f4f-113">若要自動化的應用程式部署，您可以使用 [Azure DevOps 服務](/azure/virtual-machines/windows/infrastructure-automation#azure-devops-services)， [Jenkins](/azure/virtual-machines/windows/infrastructure-automation#jenkins)，或其他 CI/CD 的解決方案。</span><span class="sxs-lookup"><span data-stu-id="13f4f-113">To automate application deployment, you can use [Azure DevOps Services](/azure/virtual-machines/windows/infrastructure-automation#azure-devops-services), [Jenkins](/azure/virtual-machines/windows/infrastructure-automation#jenkins), or other CI/CD solutions.</span></span>

<span data-ttu-id="13f4f-114">最佳做法是建立快速存取，使用的參數說明和指令碼使用範例所述的分類的自動化指令碼的存放庫。</span><span class="sxs-lookup"><span data-stu-id="13f4f-114">As a best practice, create a repository of categorized automation scripts for quick access, documented with explanations of parameters and examples of script use.</span></span> <span data-ttu-id="13f4f-115">讓這份文件與您的 Azure 部署保持同步，並指定主要人員來管理存放庫。</span><span class="sxs-lookup"><span data-stu-id="13f4f-115">Keep this documentation in sync with your Azure deployments, and designate a primary person to manage the repository.</span></span>

<span data-ttu-id="13f4f-116">自動化指令碼也可以啟用災害復原的隨選資源。</span><span class="sxs-lookup"><span data-stu-id="13f4f-116">Automation scripts can also activate resources on demand for disaster recovery.</span></span>

### <a name="use-code-to-provision-and-configure-infrastructure"></a><span data-ttu-id="13f4f-117">使用程式碼來佈建和設定基礎結構</span><span class="sxs-lookup"><span data-stu-id="13f4f-117">Use code to provision and configure infrastructure</span></span>

<span data-ttu-id="13f4f-118">這個練習中，呼叫*基礎結構即程式碼，* 可能會使用宣告式方法或命令式方法 （或兩者的組合）。</span><span class="sxs-lookup"><span data-stu-id="13f4f-118">This practice, called *infrastructure as code,* may use a declarative approach or an imperative approach (or a combination of both).</span></span>

- <span data-ttu-id="13f4f-119">[Resource Manager 範本](/azure/azure-resource-manager/resource-group-overview#template-deployment)是宣告式方法的範例。</span><span class="sxs-lookup"><span data-stu-id="13f4f-119">[Resource Manager templates](/azure/azure-resource-manager/resource-group-overview#template-deployment) are an example of a declarative approach.</span></span>
- <span data-ttu-id="13f4f-120">[PowerShell](/powershell/azure/overview)指令碼是命令式方法的範例。</span><span class="sxs-lookup"><span data-stu-id="13f4f-120">[PowerShell](/powershell/azure/overview) scripts are an example of an imperative approach.</span></span>

### <a name="practice-immutable-infrastructure"></a><span data-ttu-id="13f4f-121">做法是不可變的基礎結構</span><span class="sxs-lookup"><span data-stu-id="13f4f-121">Practice immutable infrastructure</span></span>

<span data-ttu-id="13f4f-122">換句話說，不會修改基礎結構部署到生產環境之後。</span><span class="sxs-lookup"><span data-stu-id="13f4f-122">In other words, don't modify infrastructure after it's deployed to production.</span></span> <span data-ttu-id="13f4f-123">已套用臨機操作變更之後，您可能不知道到底什麼有所變更，因此它可能難以進行疑難排解系統。</span><span class="sxs-lookup"><span data-stu-id="13f4f-123">After ad hoc changes have been applied, you might not know exactly what has changed, so it can be difficult to troubleshoot the system.</span></span>

### <a name="automate-and-test-deployment-and-maintenance-tasks"></a><span data-ttu-id="13f4f-124">自動化和測試部署和維護工作</span><span class="sxs-lookup"><span data-stu-id="13f4f-124">Automate and test deployment and maintenance tasks</span></span>

<span data-ttu-id="13f4f-125">分散式應用程式由多個必須相互合作的部分所組成。</span><span class="sxs-lookup"><span data-stu-id="13f4f-125">Distributed applications consist of multiple parts that must work together.</span></span> <span data-ttu-id="13f4f-126">部署應該利用經實證的機制，例如指令碼，可以更新和驗證設定和部署程序自動化。</span><span class="sxs-lookup"><span data-stu-id="13f4f-126">Deployment should take advantage of proven mechanisms, such as scripts, that can update and validate configuration and automate the deployment process.</span></span> <span data-ttu-id="13f4f-127">測試所有的處理程序完全以確保錯誤不會造成額外的停機時間。</span><span class="sxs-lookup"><span data-stu-id="13f4f-127">Test all processes fully to ensure that errors don't cause additional downtime.</span></span>

### <a name="implement-deployment-security-measures"></a><span data-ttu-id="13f4f-128">實作部署的安全性措施</span><span class="sxs-lookup"><span data-stu-id="13f4f-128">Implement deployment security measures</span></span>

<span data-ttu-id="13f4f-129">所有的部署工具必須納入要保護部署的應用程式的安全性限制。</span><span class="sxs-lookup"><span data-stu-id="13f4f-129">All deployment tools must incorporate security restrictions to protect the deployed application.</span></span> <span data-ttu-id="13f4f-130">定義和強制執行部署原則時，仔細和人為介入的需求降到最低。</span><span class="sxs-lookup"><span data-stu-id="13f4f-130">Define and enforce deployment policies carefully, and minimize the need for human intervention.</span></span>

## <a name="deploy-to-multiple-regions-and-instances"></a><span data-ttu-id="13f4f-131">將部署到多個區域和執行個體</span><span class="sxs-lookup"><span data-stu-id="13f4f-131">Deploy to multiple regions and instances</span></span>

<span data-ttu-id="13f4f-132">相依服務的單一執行個體的應用程式會建立單一失敗點。</span><span class="sxs-lookup"><span data-stu-id="13f4f-132">An application that depends on a single instance of a service creates a single point of failure.</span></span> <span data-ttu-id="13f4f-133">若要改善復原能力和延展性，佈建多個執行個體。</span><span class="sxs-lookup"><span data-stu-id="13f4f-133">To improve resiliency and scalability, provision multiple instances.</span></span>

- <span data-ttu-id="13f4f-134">對於 [Azure App Service](/azure/app-service/app-service-value-prop-what-is/)，請選擇可提供多個執行個體的 [App Service 方案](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview/)。</span><span class="sxs-lookup"><span data-stu-id="13f4f-134">For [Azure App Service](/azure/app-service/app-service-value-prop-what-is/), select an [App Service plan](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview/) that offers multiple instances.</span></span>
- <span data-ttu-id="13f4f-135">針對[Azure 雲端服務](/azure/cloud-services/cloud-services-choose-me)，設定每個角色來使用[多個執行個體](/azure/cloud-services/cloud-services-choose-me/#scaling-and-management)。</span><span class="sxs-lookup"><span data-stu-id="13f4f-135">For [Azure Cloud Services](/azure/cloud-services/cloud-services-choose-me), configure each of your roles to use [multiple instances](/azure/cloud-services/cloud-services-choose-me/#scaling-and-management).</span></span>
- <span data-ttu-id="13f4f-136">針對[Azure 虛擬機器](/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，請確定您的架構包含多個 VM，且每個 VM 會納入[可用性設定組](/azure/virtual-machines/virtual-machines-windows-manage-availability/)。</span><span class="sxs-lookup"><span data-stu-id="13f4f-136">For [Azure Virtual Machines](/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), ensure that your architecture includes more than one VM and that each VM is included in an [availability set](/azure/virtual-machines/virtual-machines-windows-manage-availability/).</span></span>

### <a name="consider-deploying-across-multiple-regions"></a><span data-ttu-id="13f4f-137">請考慮在多個區域部署</span><span class="sxs-lookup"><span data-stu-id="13f4f-137">Consider deploying across multiple regions</span></span>

<span data-ttu-id="13f4f-138">我們建議您部署所有但最不重要的應用程式與跨多個區域的應用程式服務。</span><span class="sxs-lookup"><span data-stu-id="13f4f-138">We recommend deploying all but the least critical applications and application services across multiple regions.</span></span> <span data-ttu-id="13f4f-139">如果您的應用程式部署到單一區域，這類罕見事件中的整個區域變成無法使用，應用程式也會無法使用。</span><span class="sxs-lookup"><span data-stu-id="13f4f-139">If your application is deployed to a single region, in the rare event that the entire region becomes unavailable, the application will also be unavailable.</span></span>

<span data-ttu-id="13f4f-140">如果您選擇要在單一區域部署，請考慮準備重新部署到次要區域，以非預期的失敗回應。</span><span class="sxs-lookup"><span data-stu-id="13f4f-140">If you choose to deploy to a single region, consider preparing to redeploy to a secondary region as a response to an unexpected failure.</span></span>

### <a name="redeploy-to-a-secondary-region"></a><span data-ttu-id="13f4f-141">重新部署到次要區域</span><span class="sxs-lookup"><span data-stu-id="13f4f-141">Redeploy to a secondary region</span></span>

<span data-ttu-id="13f4f-142">如果您沒有複寫的單一、 主要區域中執行應用程式和資料庫，您的復原策略可能要重新部署到另一個區域。</span><span class="sxs-lookup"><span data-stu-id="13f4f-142">If you run applications and databases in a single, primary region with no replication, your recovery strategy might be to redeploy to another region.</span></span> <span data-ttu-id="13f4f-143">此解決方案是經濟實惠，但最適合可容許較長的復原時間的非關鍵性應用程式。</span><span class="sxs-lookup"><span data-stu-id="13f4f-143">This solution is affordable but most appropriate for non-critical applications that can tolerate longer recovery times.</span></span>

<span data-ttu-id="13f4f-144">如果您選擇此策略時，請盡量將重新部署程序自動化，並將重新部署案例包含在災害回應測試。</span><span class="sxs-lookup"><span data-stu-id="13f4f-144">If you choose this strategy, automate the redeployment process as much as possible and include redeployment scenarios in your disaster response testing.</span></span>

<span data-ttu-id="13f4f-145">若要自動化您的重新部署程序，請考慮使用[Azure Site Recovery](/azure/site-recovery/)。</span><span class="sxs-lookup"><span data-stu-id="13f4f-145">To automate your redeployment process, consider using [Azure Site Recovery](/azure/site-recovery/).</span></span>

## <a name="use-staging-and-production-environments"></a><span data-ttu-id="13f4f-146">使用預備與生產環境</span><span class="sxs-lookup"><span data-stu-id="13f4f-146">Use staging and production environments</span></span>

<span data-ttu-id="13f4f-147">與充分利用預備與生產環境，您可以將更新推送至生產環境中，以高度控制的方式，並從非預期的部署問題的中斷最小化。</span><span class="sxs-lookup"><span data-stu-id="13f4f-147">With good use of staging and production environments, you can push updates to the production environment in a highly controlled way and minimize disruption from unanticipated deployment issues.</span></span>

- <span data-ttu-id="13f4f-148">[*藍綠部署*](https://martinfowler.com/bliki/BlueGreenDeployment.html) 牽涉到將更新部署到生產環境分開的即時應用程式。</span><span class="sxs-lookup"><span data-stu-id="13f4f-148">[*Blue-green deployment*](https://martinfowler.com/bliki/BlueGreenDeployment.html) involves deploying an update into a production environment that's separate from the live application.</span></span> <span data-ttu-id="13f4f-149">驗證部署之後，將流量路由切換為更新的版本。</span><span class="sxs-lookup"><span data-stu-id="13f4f-149">After you validate the deployment, switch the traffic routing to the updated version.</span></span> <span data-ttu-id="13f4f-150">這是使用其中一種方式[預備位置](/azure/app-service/web-sites-staged-publishing)預備之前將它移至生產環境部署至 Azure App Service 中使用。</span><span class="sxs-lookup"><span data-stu-id="13f4f-150">One way to do this is to use the [staging slots](/azure/app-service/web-sites-staged-publishing) available in Azure App Service to stage a deployment before moving it to production.</span></span>
- <span data-ttu-id="13f4f-151">[*Canary 版本*](https://martinfowler.com/bliki/CanaryRelease.html) 類似於藍綠部署。</span><span class="sxs-lookup"><span data-stu-id="13f4f-151">[*Canary releases*](https://martinfowler.com/bliki/CanaryRelease.html) are similar to blue-green deployments.</span></span> <span data-ttu-id="13f4f-152">而不是所有流量都切換到更新的應用程式，您可以將一小部分的流量路由到新的部署。</span><span class="sxs-lookup"><span data-stu-id="13f4f-152">Instead of switching all traffic to the updated application, you route only a small portion of the traffic to the new deployment.</span></span> <span data-ttu-id="13f4f-153">如果發生問題時，還原成舊的部署。</span><span class="sxs-lookup"><span data-stu-id="13f4f-153">If there's a problem, revert to the old deployment.</span></span> <span data-ttu-id="13f4f-154">如果沒有，逐漸將更多的流量路由至新的版本。</span><span class="sxs-lookup"><span data-stu-id="13f4f-154">If not, gradually route more traffic to the new version.</span></span> <span data-ttu-id="13f4f-155">如果您使用 Azure App Service，您可以使用在實際執行功能測試管理 canary 版本。</span><span class="sxs-lookup"><span data-stu-id="13f4f-155">If you're using Azure App Service, you can use the Testing in production feature to manage a canary release.</span></span>

## <a name="create-a-rollback-plan-for-deployment-and-updates"></a><span data-ttu-id="13f4f-156">建立復原計劃部署和更新</span><span class="sxs-lookup"><span data-stu-id="13f4f-156">Create a rollback plan for deployment and updates</span></span>

<span data-ttu-id="13f4f-157">如果部署失敗，可能無法使用您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="13f4f-157">If a deployment fails, your application could become unavailable.</span></span> <span data-ttu-id="13f4f-158">若要減少停機時間，設計回復程序，回到最後已知的良好版本。</span><span class="sxs-lookup"><span data-stu-id="13f4f-158">To minimize downtime, design a rollback process to go back to a last-known good version.</span></span> <span data-ttu-id="13f4f-159">包含的變更回復到資料庫和應用程式所需的任何其他服務的策略。</span><span class="sxs-lookup"><span data-stu-id="13f4f-159">Include a strategy to roll back changes to databases and any other services your app depends on.</span></span>

<span data-ttu-id="13f4f-160">如果您使用 Azure App Service，您可以設定最後一個已知良好的網站位置，並使用它來從 web 或 API 應用程式部署復原。</span><span class="sxs-lookup"><span data-stu-id="13f4f-160">If you're using Azure App Service, you can set up a last-known good site slot and use it to roll back from a web or API app deployment.</span></span>

## <a name="log-and-audit-deployments"></a><span data-ttu-id="13f4f-161">記錄和稽核的部署</span><span class="sxs-lookup"><span data-stu-id="13f4f-161">Log and audit deployments</span></span>

<span data-ttu-id="13f4f-162">若要擷取做為盡可能的更特定的版本資訊，請實作強固的記錄策略。</span><span class="sxs-lookup"><span data-stu-id="13f4f-162">To capture as much version-specific information as possible, implement a robust logging strategy.</span></span> <span data-ttu-id="13f4f-163">如果您使用分段的部署技術，將會執行您的應用程式的多個版本，在生產環境中。</span><span class="sxs-lookup"><span data-stu-id="13f4f-163">If you use staged deployment techniques, more than one version of your application will be running in production.</span></span> <span data-ttu-id="13f4f-164">如果發生問題，判斷哪一個版本造成它。</span><span class="sxs-lookup"><span data-stu-id="13f4f-164">If a problem occurs, determine which version is causing it.</span></span>

## <a name="document-release-processes"></a><span data-ttu-id="13f4f-165">文件發行程序</span><span class="sxs-lookup"><span data-stu-id="13f4f-165">Document release processes</span></span>

<span data-ttu-id="13f4f-166">沒有詳細的發行處理程序的文件，操作員可能會部署不正確的更新，或可能不正確地設定您的應用程式的設定。</span><span class="sxs-lookup"><span data-stu-id="13f4f-166">Without detailed release process documentation, an operator might deploy a bad update or might improperly configure settings for your application.</span></span> <span data-ttu-id="13f4f-167">明確定義和記載您的發行程序，並確保它可供整個營運小組使用。</span><span class="sxs-lookup"><span data-stu-id="13f4f-167">Clearly define and document your release process, and ensure that it's available to the entire operations team.</span></span>

## <a name="next-steps"></a><span data-ttu-id="13f4f-168">後續步驟</span><span class="sxs-lookup"><span data-stu-id="13f4f-168">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="13f4f-169">可靠性監視器應用程式健全狀況</span><span class="sxs-lookup"><span data-stu-id="13f4f-169">Monitor application health for reliability</span></span>](./monitoring.md)

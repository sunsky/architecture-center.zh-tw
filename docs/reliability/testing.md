---
title: 測試 Azure 應用程式的恢復功能和可用性
description: 若要在 Azure 中測試應用程式的可靠性方法
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: cf33a859540810279b1cb85be5a6fb2ebe4500dc
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59646514"
---
# <a name="testing-azure-applications-for-resiliency-and-availability"></a><span data-ttu-id="d4f76-103">測試 Azure 應用程式的恢復功能和可用性</span><span class="sxs-lookup"><span data-stu-id="d4f76-103">Testing Azure applications for resiliency and availability</span></span>

<span data-ttu-id="d4f76-104">若要測試復原，您應該確認端對端工作負載間歇性失敗的情況下執行的方式。</span><span class="sxs-lookup"><span data-stu-id="d4f76-104">To test resiliency, you should verify how the end-to-end workload performs under intermittent failure conditions.</span></span>

<span data-ttu-id="d4f76-105">使用綜合與真實的使用者資料的生產環境中執行測試。</span><span class="sxs-lookup"><span data-stu-id="d4f76-105">Run tests in production using both synthetic and real user data.</span></span> <span data-ttu-id="d4f76-106">測試和生產環境很少完全相同，因此請務必驗證您的應用程式在生產環境中使用[藍綠](https://martinfowler.com/bliki/BlueGreenDeployment.html)或是[canary 部署](https://martinfowler.com/bliki/CanaryRelease.html)。</span><span class="sxs-lookup"><span data-stu-id="d4f76-106">Test and production are rarely identical, so it's important to validate your application in production using a [blue-green](https://martinfowler.com/bliki/BlueGreenDeployment.html) or [canary deployment](https://martinfowler.com/bliki/CanaryRelease.html).</span></span> <span data-ttu-id="d4f76-107">如此一來，如此便能確定，它會如預期般運作完整部署時，正在測試真實狀況下，應用程式。</span><span class="sxs-lookup"><span data-stu-id="d4f76-107">This way, you're testing the application under real conditions, so you can be sure that it will function as expected when fully deployed.</span></span>

<span data-ttu-id="d4f76-108">測試計劃的一部分，包括：</span><span class="sxs-lookup"><span data-stu-id="d4f76-108">As part of your test plan, include:</span></span>

- <span data-ttu-id="d4f76-109">自動化部署前測試</span><span class="sxs-lookup"><span data-stu-id="d4f76-109">Automated predeployment testing</span></span>
- <span data-ttu-id="d4f76-110">錯誤插入測試</span><span class="sxs-lookup"><span data-stu-id="d4f76-110">Fault injection testing</span></span>
- <span data-ttu-id="d4f76-111">尖峰負載測試</span><span class="sxs-lookup"><span data-stu-id="d4f76-111">Peak load testing</span></span>
- <span data-ttu-id="d4f76-112">災害復原測試</span><span class="sxs-lookup"><span data-stu-id="d4f76-112">Disaster recovery testing</span></span>
- <span data-ttu-id="d4f76-113">第三方服務測試</span><span class="sxs-lookup"><span data-stu-id="d4f76-113">Third-party service testing</span></span>

<span data-ttu-id="d4f76-114">測試是反覆的程序。</span><span class="sxs-lookup"><span data-stu-id="d4f76-114">Testing is an iterative process.</span></span> <span data-ttu-id="d4f76-115">測試應用程式、測量結果、分析及解決所發生的任何失敗，並重複此程序。</span><span class="sxs-lookup"><span data-stu-id="d4f76-115">Test the application, measure the outcome, analyze and address any failures that result, and repeat the process.</span></span>

## <a name="perform-fault-injection-testing"></a><span data-ttu-id="d4f76-116">執行錯誤插入測試</span><span class="sxs-lookup"><span data-stu-id="d4f76-116">Perform fault injection testing</span></span>

<span data-ttu-id="d4f76-117">針對錯誤插入測試，檢查系統的復原期間失敗，方法為觸發實際的失敗或加以模擬。</span><span class="sxs-lookup"><span data-stu-id="d4f76-117">For fault injection testing, check the resiliency of the system during failures, either by triggering actual failures or by simulating them.</span></span> <span data-ttu-id="d4f76-118">以下是一些引發失敗的策略：</span><span class="sxs-lookup"><span data-stu-id="d4f76-118">Here are some strategies to induce failures:</span></span>

- <span data-ttu-id="d4f76-119">關閉虛擬機器 (VM) 執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4f76-119">Shut down virtual machine (VM) instances.</span></span>
- <span data-ttu-id="d4f76-120">毀損程序。</span><span class="sxs-lookup"><span data-stu-id="d4f76-120">Crash processes.</span></span>
- <span data-ttu-id="d4f76-121">憑證到期。</span><span class="sxs-lookup"><span data-stu-id="d4f76-121">Expire certificates.</span></span>
- <span data-ttu-id="d4f76-122">變更存取金鑰。</span><span class="sxs-lookup"><span data-stu-id="d4f76-122">Change access keys.</span></span>
- <span data-ttu-id="d4f76-123">關閉網域控制站上的 DNS 服務。</span><span class="sxs-lookup"><span data-stu-id="d4f76-123">Shut down the DNS service on domain controllers.</span></span>
- <span data-ttu-id="d4f76-124">限制可用的系統資源，例如 RAM 或執行緒的數目。</span><span class="sxs-lookup"><span data-stu-id="d4f76-124">Limit available system resources, such as RAM or number of threads.</span></span>
- <span data-ttu-id="d4f76-125">取消掛接磁碟。</span><span class="sxs-lookup"><span data-stu-id="d4f76-125">Unmount disks.</span></span>
- <span data-ttu-id="d4f76-126">重新部署 VM。</span><span class="sxs-lookup"><span data-stu-id="d4f76-126">Redeploy a VM.</span></span>

<span data-ttu-id="d4f76-127">測試計劃應該納入設計階段中，除了常見的失敗案例期間識別可能的失敗點：</span><span class="sxs-lookup"><span data-stu-id="d4f76-127">Your test plan should incorporate possible failure points identified during the design phase, in addition to common failure scenarios:</span></span>

- <span data-ttu-id="d4f76-128">生產環境盡可能接近的環境中測試您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="d4f76-128">Test your application in an environment as close to production as possible.</span></span>
- <span data-ttu-id="d4f76-129">組合中的測試失敗。</span><span class="sxs-lookup"><span data-stu-id="d4f76-129">Test failures in combination.</span></span>
- <span data-ttu-id="d4f76-130">測量復原時間，而且確定符合您的業務需求。</span><span class="sxs-lookup"><span data-stu-id="d4f76-130">Measure the recovery times, and be sure that your business requirements are met.</span></span>
- <span data-ttu-id="d4f76-131">確認失敗不會重疊，而且會以隔離的方式處理。</span><span class="sxs-lookup"><span data-stu-id="d4f76-131">Verify that failures don't cascade and are handled in an isolated way.</span></span>

<span data-ttu-id="d4f76-132">如需詳細的失敗案例的詳細資訊，請參閱[的 Azure 應用程式失敗和災害復原](./disaster-recovery.md)。</span><span class="sxs-lookup"><span data-stu-id="d4f76-132">For more information about failure scenarios, see [Failure and disaster recovery for Azure applications](./disaster-recovery.md).</span></span>

## <a name="test-under-peak-loads"></a><span data-ttu-id="d4f76-133">在尖峰負載下測試</span><span class="sxs-lookup"><span data-stu-id="d4f76-133">Test under peak loads</span></span>

<span data-ttu-id="d4f76-134">負載測試，請務必找出負載，例如後端資料庫遭灌爆，才會發生的錯誤或服務節流。</span><span class="sxs-lookup"><span data-stu-id="d4f76-134">Load testing is crucial for identifying failures that only happen under load, such as the back-end database being overwhelmed or service throttling.</span></span> <span data-ttu-id="d4f76-135">測試尖峰負載的預期的尖峰負載，使用的生產資料或綜合資料，最接近生產資料，盡可能增加。</span><span class="sxs-lookup"><span data-stu-id="d4f76-135">Test for peak load and anticipated increase in peak load, using production data or synthetic data that is as close to production data as possible.</span></span> <span data-ttu-id="d4f76-136">若要查看應用程式在真實世界的情況下的運作方式，是您的目標。</span><span class="sxs-lookup"><span data-stu-id="d4f76-136">Your goal is to see how the application behaves under real-world conditions.</span></span>

## <a name="conduct-disaster-recovery-drills"></a><span data-ttu-id="d4f76-137">進行災害復原演練</span><span class="sxs-lookup"><span data-stu-id="d4f76-137">Conduct disaster recovery drills</span></span>

<span data-ttu-id="d4f76-138">開始使用理想的災害復原方案，並定期進行測試以確定它運作。</span><span class="sxs-lookup"><span data-stu-id="d4f76-138">Start with a good disaster recovery plan, and test it periodically to make sure it works.</span></span>

<span data-ttu-id="d4f76-139">適用於 Azure 虛擬機器中，您可以使用[Azure Site Recovery](/azure/site-recovery/azure-to-azure-quickstart/)複寫，並執行[災害復原演練，又](/azure/site-recovery/azure-to-azure-tutorial-dr-drill/)而不會影響生產應用程式或進行中的複寫。</span><span class="sxs-lookup"><span data-stu-id="d4f76-139">For Azure Virtual Machines, you can use [Azure Site Recovery](/azure/site-recovery/azure-to-azure-quickstart/) to replicate and perform [disaster recovery drills](/azure/site-recovery/azure-to-azure-tutorial-dr-drill/) without impacting production applications or ongoing replication.</span></span>

### <a name="failover-and-failback-testing"></a><span data-ttu-id="d4f76-140">容錯移轉和容錯回復測試</span><span class="sxs-lookup"><span data-stu-id="d4f76-140">Failover and failback testing</span></span>

<span data-ttu-id="d4f76-141">測試容錯移轉和容錯回復到驗證，您的應用程式相依的服務恢復運作已同步處理的方式在災害復原期間。</span><span class="sxs-lookup"><span data-stu-id="d4f76-141">Test failover and failback to verify that your application's dependent services come back up in a synchronized manner during disaster recovery.</span></span> <span data-ttu-id="d4f76-142">系統與作業的變更可能會影響容錯移轉和容錯回復的函式，但主要系統失敗或變得多載之前，可能無法偵測影響。</span><span class="sxs-lookup"><span data-stu-id="d4f76-142">Changes to systems and operations may affect failover and failback functions, but the impact may not be detected until the main system fails or becomes overloaded.</span></span> <span data-ttu-id="d4f76-143">測試容錯移轉功能*之前*它們必須彌補即時問題。</span><span class="sxs-lookup"><span data-stu-id="d4f76-143">Test failover capabilities *before* they are required to compensate for a live problem.</span></span> <span data-ttu-id="d4f76-144">也請務必依存的服務容錯移轉和容錯回復在正確的順序。</span><span class="sxs-lookup"><span data-stu-id="d4f76-144">Also be sure that dependent services fail over and fail back in the correct order.</span></span>

<span data-ttu-id="d4f76-145">如果您使用[Azure Site Recovery](/azure/site-recovery/)來複寫 Vm，執行災害復原演練定期執行測試容錯移轉來驗證複寫策略。</span><span class="sxs-lookup"><span data-stu-id="d4f76-145">If you are using [Azure Site Recovery](/azure/site-recovery/) to replicate VMs, run disaster recovery drills periodically by doing test failovers to validate your replication strategy.</span></span> <span data-ttu-id="d4f76-146">測試容錯移轉不會影響進行中的 VM 複寫或生產環境。</span><span class="sxs-lookup"><span data-stu-id="d4f76-146">A test failover does not affect the ongoing VM replication or your production environment.</span></span> <span data-ttu-id="d4f76-147">如需詳細資訊，請參閱 <<c0> [ 執行至 Azure 的災害復原演練](/azure/site-recovery/site-recovery-test-failover-to-azure)。</span><span class="sxs-lookup"><span data-stu-id="d4f76-147">For more information, see [Run a disaster recovery drill to Azure](/azure/site-recovery/site-recovery-test-failover-to-azure).</span></span>

### <a name="simulation-testing"></a><span data-ttu-id="d4f76-148">模擬測試</span><span class="sxs-lookup"><span data-stu-id="d4f76-148">Simulation testing</span></span>

<span data-ttu-id="d4f76-149">模擬測試包括建立小型的真實情況。</span><span class="sxs-lookup"><span data-stu-id="d4f76-149">Simulation testing involves creating small, real-life situations.</span></span> <span data-ttu-id="d4f76-150">模擬在復原計劃中示範解決方案的效率，並反白顯示未適當地解決任何問題。</span><span class="sxs-lookup"><span data-stu-id="d4f76-150">Simulations demonstrate the effectiveness of the solutions in the recovery plan and highlight any issues that weren't adequately addressed.</span></span>

<span data-ttu-id="d4f76-151">當您執行模擬測試，請依照下列最佳做法：</span><span class="sxs-lookup"><span data-stu-id="d4f76-151">As you perform simulation testing, follow best practices:</span></span>

- <span data-ttu-id="d4f76-152">進行模擬，以不干擾實際業務，但覺得的真實情況的方式。</span><span class="sxs-lookup"><span data-stu-id="d4f76-152">Conduct simulations in a manner that doesn't disrupt actual business but feels like a real situation.</span></span>
- <span data-ttu-id="d4f76-153">請確定完全控制模擬的案例。</span><span class="sxs-lookup"><span data-stu-id="d4f76-153">Make sure that simulated scenarios are completely controllable.</span></span> <span data-ttu-id="d4f76-154">如果復原計畫似乎失敗，您可以回到正常還原這種情況，而不會造成損毀的情況。</span><span class="sxs-lookup"><span data-stu-id="d4f76-154">If the recovery plan seems to be failing, you can restore the situation back to normal without causing damage.</span></span>
- <span data-ttu-id="d4f76-155">通知管理何時及如何將執行模擬演習。</span><span class="sxs-lookup"><span data-stu-id="d4f76-155">Inform management about when and how the simulation exercises will be conducted.</span></span> <span data-ttu-id="d4f76-156">您的計劃應詳述時間範圍和模擬期間受到影響的資源。</span><span class="sxs-lookup"><span data-stu-id="d4f76-156">Your plan should detail the time frame and the resources affected during the simulation.</span></span>

## <a name="test-health-probes-for-load-balancers-and-traffic-managers"></a><span data-ttu-id="d4f76-157">測試負載平衡器和流量管理員的健康情況探查</span><span class="sxs-lookup"><span data-stu-id="d4f76-157">Test health probes for load balancers and traffic managers</span></span>

<span data-ttu-id="d4f76-158">設定並測試您的負載平衡器和流量管理員的健康情況探查。</span><span class="sxs-lookup"><span data-stu-id="d4f76-158">Configure and test health probes for your load balancers and traffic managers.</span></span> <span data-ttu-id="d4f76-159">請確定您的健康情況端點檢查系統的重要部分，並適當地回應。</span><span class="sxs-lookup"><span data-stu-id="d4f76-159">Ensure that your health endpoint checks the critical parts of the system and responds appropriately.</span></span>

- <span data-ttu-id="d4f76-160">針對[Azure 流量管理員](/azure/traffic-manager/traffic-manager-overview/)，健康情況探查可決定是否要容錯移轉至另一個區域。</span><span class="sxs-lookup"><span data-stu-id="d4f76-160">For [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview/), the health probe determines whether to fail over to another region.</span></span> <span data-ttu-id="d4f76-161">健康情況端點應該檢查關鍵性的相依性會部署在相同區域內。</span><span class="sxs-lookup"><span data-stu-id="d4f76-161">Your health endpoint should check any critical dependencies that are deployed within the same region.</span></span>
- <span data-ttu-id="d4f76-162">針對[Azure Load Balancer](/azure/load-balancer/load-balancer-overview/)，健康情況探查可決定是否要從循環中移除 VM。</span><span class="sxs-lookup"><span data-stu-id="d4f76-162">For [Azure Load Balancer](/azure/load-balancer/load-balancer-overview/), the health probe determines whether to remove a VM from rotation.</span></span> <span data-ttu-id="d4f76-163">健康情況端點應該報告 VM 健康的情況。</span><span class="sxs-lookup"><span data-stu-id="d4f76-163">The health endpoint should report the health of the VM.</span></span> <span data-ttu-id="d4f76-164">不包含其他層或外部服務。</span><span class="sxs-lookup"><span data-stu-id="d4f76-164">Don't include other tiers or external services.</span></span> <span data-ttu-id="d4f76-165">否則，在 VM 外部發生的失敗，可能會導致負載平衡器從循環中移除 VM。</span><span class="sxs-lookup"><span data-stu-id="d4f76-165">Otherwise, a failure that occurs outside the VM will cause the load balancer to remove the VM from rotation.</span></span>

<span data-ttu-id="d4f76-166">如需在應用程式中實作健康情況監視的指引，請參閱[健康情況端點監視模式](../patterns/health-endpoint-monitoring.md)。</span><span class="sxs-lookup"><span data-stu-id="d4f76-166">For guidance on implementing health monitoring in your application, see [Health Endpoint Monitoring pattern](../patterns/health-endpoint-monitoring.md).</span></span>

## <a name="test-monitoring-systems"></a><span data-ttu-id="d4f76-167">監視系統的測試</span><span class="sxs-lookup"><span data-stu-id="d4f76-167">Test monitoring systems</span></span>

<span data-ttu-id="d4f76-168">包含監視您的測試計劃中的系統。</span><span class="sxs-lookup"><span data-stu-id="d4f76-168">Include monitoring systems in your test plan.</span></span> <span data-ttu-id="d4f76-169">自動容錯移轉和容錯回復的系統而定的監視和檢測的正確運作。</span><span class="sxs-lookup"><span data-stu-id="d4f76-169">Automated failover and failback systems depend on the correct functioning of monitoring and instrumentation.</span></span> <span data-ttu-id="d4f76-170">儀表板，以視覺化方式檢視系統健全狀況和操作員的警示也會視需要精確的監視和檢測。</span><span class="sxs-lookup"><span data-stu-id="d4f76-170">Dashboards to visualize system health and operator alerts also depend on having accurate monitoring and instrumentation.</span></span> <span data-ttu-id="d4f76-171">如果這些項目失敗，遺漏重要資訊或回報不正確的資料，操作人員可能不知道系統是狀況不良還是失敗。</span><span class="sxs-lookup"><span data-stu-id="d4f76-171">If these elements fail, miss critical information, or report inaccurate data, an operator might not realize that the system is unhealthy or failing.</span></span>

## <a name="include-third-party-services-in-test-scenarios"></a><span data-ttu-id="d4f76-172">包含測試案例中的第三方服務</span><span class="sxs-lookup"><span data-stu-id="d4f76-172">Include third-party services in test scenarios</span></span>

<span data-ttu-id="d4f76-173">如果您的應用程式會對第三方服務中的相依性，識別，以及如何這些服務可能會失敗並造成何種影響這些失敗，會對您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="d4f76-173">If your application has dependencies on third-party services, identify where and how these services can fail and what effect those failures will have on your application.</span></span> <span data-ttu-id="d4f76-174">請注意服務等級協定 (SLA) 的第三方服務，並將效果可能會對您的災害復原計劃。</span><span class="sxs-lookup"><span data-stu-id="d4f76-174">Keep in mind the service-level agreement (SLA) for the third-party service and the effect it might have on your disaster recovery plan.</span></span>

<span data-ttu-id="d4f76-175">第三方服務可能會提供監視和診斷功能，所以一定要記錄您引動過程，其中，並將它們相互關聯與您的應用程式健全狀況和診斷記錄使用的唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="d4f76-175">A third-party service might not provide monitoring and diagnostics capabilities, so it's important to log your invocations of them and to correlate them with your application's health and diagnostic logging using a unique identifier.</span></span> <span data-ttu-id="d4f76-176">如需有關監視和診斷的已經實證做法的詳細資訊，請參閱[監視和診斷指引](../best-practices/monitoring.md)。</span><span class="sxs-lookup"><span data-stu-id="d4f76-176">For more information on proven practices for monitoring and diagnostics, see [Monitoring and diagnostics guidance](../best-practices/monitoring.md).</span></span>

## <a name="next-steps"></a><span data-ttu-id="d4f76-177">後續步驟</span><span class="sxs-lookup"><span data-stu-id="d4f76-177">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="d4f76-178">部署適用於復原能力和可用性</span><span class="sxs-lookup"><span data-stu-id="d4f76-178">Deploy for resiliency and availability</span></span>](./deploy.md)

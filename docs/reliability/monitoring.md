---
title: 在 Azure 中的可靠性監視應用程式的健全狀況
description: 如何使用監視來改善應用程式在 Azure 中的可靠性
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: 46f9f3fb623a2facf4b9f9c6ec57376ae59e41dc
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59646521"
---
# <a name="monitoring-application-health-for-reliability-in-azure"></a><span data-ttu-id="dbfbd-103">在 Azure 中的可靠性監視應用程式的健全狀況</span><span class="sxs-lookup"><span data-stu-id="dbfbd-103">Monitoring application health for reliability in Azure</span></span>

<span data-ttu-id="dbfbd-104">監視和診斷對於復原非常重要。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-104">Monitoring and diagnostics are crucial for resiliency.</span></span> <span data-ttu-id="dbfbd-105">如果發生失敗，您需要知道*所*失敗，它*時*失敗&mdash;和*為什麼*。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-105">If something fails, you need to know *that* it failed, *when* it failed &mdash; and *why*.</span></span>

<span data-ttu-id="dbfbd-106">*監視*不是相同*失敗偵測*。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-106">*Monitoring* is not the same as *failure detection*.</span></span> <span data-ttu-id="dbfbd-107">例如，您的應用程式可能會偵測到暫時性錯誤然後重試，避免停機時間。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-107">For example, your application might detect a transient error and retry, avoiding downtime.</span></span> <span data-ttu-id="dbfbd-108">但是，好讓您可以監視錯誤率，以取得整體的應用程式健全狀況，它應該也記錄重試作業。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-108">But it should also log the retry operation so that you can monitor the error rate to get an overall picture of application health.</span></span>

<span data-ttu-id="dbfbd-109">監視和診斷程序視為具有四個不同階段的管線：檢測、 集合和儲存體、 分析、 診斷和視覺效果及警示。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-109">Think of the monitoring and diagnostics process as a pipeline with four distinct stages: Instrumentation, collection and storage, analysis and diagnosis, and visualization and alerts.</span></span>

## <a name="instrumentation"></a><span data-ttu-id="dbfbd-110">檢測</span><span class="sxs-lookup"><span data-stu-id="dbfbd-110">Instrumentation</span></span>

<span data-ttu-id="dbfbd-111">檢測是索引鍵，因此不適合直接，監視您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-111">It's not practical to monitor your application directly, so instrumentation is key.</span></span> <span data-ttu-id="dbfbd-112">大規模的分散式的系統可能會多上執行的虛擬機器 (Vm)，會被加入和移除一段時間。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-112">A large-scale distributed system might run on dozens of virtual machines (VMs), which are added and removed over time.</span></span> <span data-ttu-id="dbfbd-113">同樣地，雲端應用程式可能會使用幾個資料存放區，並以單一使用者動作可能會跨越多個子系統。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-113">Likewise, a cloud application might use a number of data stores and a single user action might span multiple subsystems.</span></span>

<span data-ttu-id="dbfbd-114">提供豐富的檢測：</span><span class="sxs-lookup"><span data-stu-id="dbfbd-114">Provide rich instrumentation:</span></span>

- <span data-ttu-id="dbfbd-115">針對失敗的可能但尚未發生，請提供足夠的資料來判斷其原因，緩和情況，並確保系統仍然可用。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-115">For failures that are likely but have not yet occurred, provide enough data to determine the cause, mitigate the situation, and ensure that the system remains available.</span></span>
- <span data-ttu-id="dbfbd-116">針對已發生的失敗，應用程式應該將適當的錯誤訊息傳回給使用者，但應該嘗試繼續執行，但是有減少的功能。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-116">For failures that have already occurred, the application should return an appropriate error message to the user but should attempt to continue running, albeit with reduced functionality.</span></span>

<span data-ttu-id="dbfbd-117">監視系統應該擷取完整的詳細資料，以便可以有效率地還原應用程式，並視需要設計人員和開發人員可以修改系統，以防的重複發生這種情況。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-117">Monitoring systems should capture comprehensive details so that applications can be restored efficiently and, if necessary, designers and developers can modify the system to prevent the situation from recurring.</span></span>

<span data-ttu-id="dbfbd-118">監視未經處理的資料可以來自各種來源，包括：</span><span class="sxs-lookup"><span data-stu-id="dbfbd-118">The raw data for monitoring can come from a variety of sources, including:</span></span>

- <span data-ttu-id="dbfbd-119">應用程式記錄檔，例如所產生[Azure Application Insights](/azure/azure-monitor/app/app-insights-overview)服務。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-119">Application logs, such as those produced by the [Azure Application Insights](/azure/azure-monitor/app/app-insights-overview) service.</span></span>
- <span data-ttu-id="dbfbd-120">作業系統效能標準收集[Azure 監視代理程式](/azure/azure-monitor/platform/agents-overview)。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-120">Operating system performance metrics collected by [Azure monitoring agents](/azure/azure-monitor/platform/agents-overview).</span></span>
- <span data-ttu-id="dbfbd-121">[Azure 資源](/azure/azure-monitor/platform/metrics-supported)，包括標準 Azure 監視器所收集。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-121">[Azure resources](/azure/azure-monitor/platform/metrics-supported), including metrics collected by Azure Monitor.</span></span>
- <span data-ttu-id="dbfbd-122">[Azure 服務健康情況](/azure/service-health/service-health-overview)，提供可協助您追蹤作用中的事件儀表板。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-122">[Azure Service Health](/azure/service-health/service-health-overview), which offers a dashboard to help you track active events.</span></span>
- <span data-ttu-id="dbfbd-123">[Azure AD 記錄檔](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) 內建於 Azure 平台。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-123">[Azure AD logs](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) built into the Azure platform.</span></span>

<span data-ttu-id="dbfbd-124">大部分的 Azure 服務有計量和診斷工具，您可以設定來分析及判斷問題的原因。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-124">Most Azure services have metrics and diagnostics that you can configure to analyze and determine the cause of problems.</span></span> <span data-ttu-id="dbfbd-125">若要進一步了解，請參閱[Azure 監視器所收集的監視資料](/azure/azure-monitor/platform/data-collection)。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-125">To learn more, see [Monitoring data collected by Azure Monitor](/azure/azure-monitor/platform/data-collection).</span></span>

## <a name="collection-and-storage"></a><span data-ttu-id="dbfbd-126">收集和儲存體</span><span class="sxs-lookup"><span data-stu-id="dbfbd-126">Collection and storage</span></span>

<span data-ttu-id="dbfbd-127">原始檢測資料可以保留在各種位置和格式，包括：</span><span class="sxs-lookup"><span data-stu-id="dbfbd-127">Raw instrumentation data can be held in various locations and formats, including:</span></span>

- <span data-ttu-id="dbfbd-128">應用程式追蹤記錄</span><span class="sxs-lookup"><span data-stu-id="dbfbd-128">Application trace logs</span></span>
- <span data-ttu-id="dbfbd-129">IIS 記錄</span><span class="sxs-lookup"><span data-stu-id="dbfbd-129">IIS logs</span></span>
- <span data-ttu-id="dbfbd-130">效能計數器</span><span class="sxs-lookup"><span data-stu-id="dbfbd-130">Performance counters</span></span>

<span data-ttu-id="dbfbd-131">收集、 彙總，並放在 Azure 中，例如 Application Insights、 Azure 監視器計量、 服務健康狀態、 儲存體帳戶和 Azure Log Analytics 的可靠的資料存放區在這些不同的來源。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-131">These disparate sources are collected, consolidated, and placed in reliable data stores in Azure, such as Application Insights, Azure Monitor metrics, Service Health, storage accounts, and Azure Log Analytics.</span></span>

## <a name="analysis-and-diagnosis"></a><span data-ttu-id="dbfbd-132">分析及診斷</span><span class="sxs-lookup"><span data-stu-id="dbfbd-132">Analysis and diagnosis</span></span>

<span data-ttu-id="dbfbd-133">分析資料合併在針對問題進行疑難排解，並取得應用程式健全狀況的整體檢視這些資料存放區中。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-133">Analyze data consolidated in these data stores to troubleshoot issues and gain an overall view of application health.</span></span> <span data-ttu-id="dbfbd-134">一般而言，您可以[搜尋和分析](/azure/azure-monitor/log-query/log-query-overview)Application Insights 和 Log Analytics 中使用 Kusto 查詢或使用預先設定的檢視圖形中的資料 [管理解決方案](/azure/azure-monitor/insights/solutions-inventory)。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-134">Generally, you can [search for and analyze](/azure/azure-monitor/log-query/log-query-overview) the data in Application Insights and Log Analytics using Kusto queries or view preconfigured graphs using [management solutions](/azure/azure-monitor/insights/solutions-inventory).</span></span> <span data-ttu-id="dbfbd-135">或使用 Azure Advisor 來檢視有關建議著重[恢復](/azure/advisor/advisor-high-availability-recommendations)並[效能](/azure/advisor/advisor-performance-recommendations)。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-135">Or use Azure Advisor to view recommendations with a focus on [resiliency](/azure/advisor/advisor-high-availability-recommendations) and [performance](/azure/advisor/advisor-performance-recommendations).</span></span>

## <a name="visualization-and-alerts"></a><span data-ttu-id="dbfbd-136">視覺效果和警示</span><span class="sxs-lookup"><span data-stu-id="dbfbd-136">Visualization and alerts</span></span>

<span data-ttu-id="dbfbd-137">呈現遙測資料格式，可讓您更容易注意到問題和趨勢，例如儀表板或電子郵件警示操作員。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-137">Present telemetry data in a format that makes it easy for an operator to notice problems or trends quickly, such as a dashboard or email alert.</span></span>

<span data-ttu-id="dbfbd-138">取得所使用的應用程式狀態的完整堆疊檢視 [Azure 儀表板](/azure/azure-portal/azure-portal-dashboards)建立監視圖形從 Application Insights，Log Analytics、 Azure 監視器計量和服務健全狀況的彙總的檢視。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-138">Get a full-stack view of application state by using [Azure dashboards](/azure/azure-portal/azure-portal-dashboards) to create a consolidated view of monitoring graphs from Application Insights, Log Analytics, Azure Monitor metrics, and Service Health.</span></span> <span data-ttu-id="dbfbd-139">並用 [Azure 監視器警示](/azure/azure-monitor/platform/alerts-overview)建立服務健康狀態，資源健康狀態，Azure 監視器計量通知記錄在 Log Analytics 和 Application Insights。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-139">And use [Azure Monitor alerts](/azure/azure-monitor/platform/alerts-overview) to create notifications on Service Health, resource health, Azure Monitor metrics, logs in Log Analytics, and Application Insights.</span></span>

<span data-ttu-id="dbfbd-140">如需有關監視和診斷的詳細資訊，請參閱 <<c0> [ 監視和診斷](../best-practices/monitoring.md)。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-140">For more information about monitoring and diagnostics, see [Monitoring and diagnostics](../best-practices/monitoring.md).</span></span>

## <a name="health-probes-and-check-functions"></a><span data-ttu-id="dbfbd-141">健康情況探查和檢查函式</span><span class="sxs-lookup"><span data-stu-id="dbfbd-141">Health probes and check functions</span></span>

<span data-ttu-id="dbfbd-142">應用程式的效能與健康情況可能會降低經過一段時間，並降低應用程式失敗之前，可能無法明顯。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-142">The health and performance of an application can degrade over time, and degradation might not be noticeable until the application fails.</span></span>

<span data-ttu-id="dbfbd-143">實作探查或檢查函式，並執行定期從外部應用程式。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-143">Implement probes or check functions, and run them regularly from outside the application.</span></span> <span data-ttu-id="dbfbd-144">這些檢查可以非常簡單，做為測量回應時間在整個應用程式、 應用程式的個別部分、 應用程式所使用的特定服務或個別的元件。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-144">These checks can be as simple as measuring response time for the application as a whole, for individual parts of the application, for specific services that the application uses, or for separate components.</span></span>

<span data-ttu-id="dbfbd-145">檢查函數可以執行程序，以確保它們會產生有效的結果、 測量延遲及檢查可用性，並從系統擷取資訊。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-145">Check functions can run processes to ensure that they produce valid results, measure latency and check availability, and extract information from the system.</span></span>

## <a name="long-running-workflow-failures"></a><span data-ttu-id="dbfbd-146">長時間執行工作流程失敗</span><span class="sxs-lookup"><span data-stu-id="dbfbd-146">Long-running workflow failures</span></span>

<span data-ttu-id="dbfbd-147">長時間執行的工作流程通常包含多個步驟，其中每一個都應該獨立。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-147">Long-running workflows often include multiple steps, each of which should be independent.</span></span>

<span data-ttu-id="dbfbd-148">追蹤長時間執行的程序，將整個工作流程必須回復或多個補償交易，就必須執行的可能性降至最低的進度。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-148">Track the progress of long-running processes to minimize the likelihood that the entire workflow will need to be rolled back or that multiple compensating transactions will need to be executed.</span></span>

>[!TIP]
> <span data-ttu-id="dbfbd-149">監視和管理這類實作模式的長時間執行工作流程的進度[排程器代理程式監督員](../patterns/scheduler-agent-supervisor.md)。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-149">Monitor and manage the progress of long-running workflows by implementing a pattern such as [Scheduler Agent Supervisor](../patterns/scheduler-agent-supervisor.md).</span></span>

## <a name="application-logs"></a><span data-ttu-id="dbfbd-150">應用程式記錄</span><span class="sxs-lookup"><span data-stu-id="dbfbd-150">Application logs</span></span>

<span data-ttu-id="dbfbd-151">應用程式記錄是診斷資料的重要來源。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-151">Application logs are an important source of diagnostics data.</span></span> <span data-ttu-id="dbfbd-152">若要深入了解當您在需要時最，請遵循應用程式記錄的最佳作法。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-152">To gain insight when you need it most, follow best practices for application logging.</span></span>

### <a name="log-data-in-the-production-environment"></a><span data-ttu-id="dbfbd-153">在生產環境中的記錄資料</span><span class="sxs-lookup"><span data-stu-id="dbfbd-153">Log data in the production environment</span></span>

<span data-ttu-id="dbfbd-154">雖然應用程式正在執行生產環境中，因此您有足夠的資訊來診斷問題，在生產環境狀態的原因，請擷取強固的遙測資料。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-154">Capture robust telemetry data while the application is running in the production environment, so you have sufficient information to diagnose the cause of issues in the production state.</span></span>

### <a name="log-events-at-service-boundaries"></a><span data-ttu-id="dbfbd-155">在服務界限的記錄檔事件</span><span class="sxs-lookup"><span data-stu-id="dbfbd-155">Log events at service boundaries</span></span>

<span data-ttu-id="dbfbd-156">包含流過服務界限的相互關聯識別碼。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-156">Include a correlation ID that flows across service boundaries.</span></span> <span data-ttu-id="dbfbd-157">如果交易流經多個服務，其中會失敗，相互關聯識別碼可協助您追蹤您的應用程式和交易失敗的原因會指出要求。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-157">If a transaction flows through multiple services and one of them fails, the correlation ID helps you track requests across your application and pinpoints why the transaction failed.</span></span>

### <a name="use-semantic-structured-logging"></a><span data-ttu-id="dbfbd-158">使用語意 （結構化） 的記錄</span><span class="sxs-lookup"><span data-stu-id="dbfbd-158">Use semantic (structured) logging</span></span>

<span data-ttu-id="dbfbd-159">使用結構化記錄檔，就將自動化的耗用量及分析記錄資料，也就是特別重要雲端規模的工作變得更容易。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-159">With structured logs, it's easier to automate the consumption and analysis of the log data, which is especially important at cloud scale.</span></span> <span data-ttu-id="dbfbd-160">一般而言，我們建議將 Azure 資源計量和診斷資料儲存在 Log Analytics 工作區中，而不儲存體帳戶中。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-160">Generally, we recommend storing Azure resources metrics and diagnostics data in a Log Analytics workspace rather than in a storage account.</span></span> <span data-ttu-id="dbfbd-161">如此一來，您可以使用 Kusto 查詢，以取得您想要快速地結構化格式的資料。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-161">This way, you can use Kusto queries to obtain the data you want quickly and in a structured format.</span></span> <span data-ttu-id="dbfbd-162">您也可以使用 Azure 監視器 Api 和 Azure Log Analytics Api。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-162">You can also use Azure Monitor APIs and Azure Log Analytics APIs.</span></span>

### <a name="use-asynchronous-logging"></a><span data-ttu-id="dbfbd-163">使用非同步記錄</span><span class="sxs-lookup"><span data-stu-id="dbfbd-163">Use asynchronous logging</span></span>

<span data-ttu-id="dbfbd-164">同步記錄作業有時候會封鎖應用程式程式碼，導致備份記錄檔會寫入的要求。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-164">Synchronous logging operations sometimes block your application code, causing requests to back up as logs are written.</span></span> <span data-ttu-id="dbfbd-165">使用非同步記錄，以保留應用程式記錄期間的可用性。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-165">Use asynchronous logging to preserve availability during application logging.</span></span>

### <a name="separate-application-logging-from-auditing"></a><span data-ttu-id="dbfbd-166">個別的應用程式記錄從稽核</span><span class="sxs-lookup"><span data-stu-id="dbfbd-166">Separate application logging from auditing</span></span>

<span data-ttu-id="dbfbd-167">稽核記錄通常會維護的合規性或法規的需求，而且必須是完整。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-167">Audit records are commonly maintained for compliance or regulatory requirements and must be complete.</span></span> <span data-ttu-id="dbfbd-168">若要避免已卸除的交易，維護稽核記錄檔與診斷記錄檔分開。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-168">To avoid dropped transactions, maintain audit logs separately from diagnostic logs.</span></span>

## <a name="remote-call-statistics"></a><span data-ttu-id="dbfbd-169">遠端呼叫統計資料</span><span class="sxs-lookup"><span data-stu-id="dbfbd-169">Remote call statistics</span></span>

<span data-ttu-id="dbfbd-170">追蹤和報告遠端呼叫即時的統計資料，並提供簡單的方式，來檢閱這項資訊，讓作業小組擁有的即時檢視您的應用程式的健全狀況。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-170">Track and report remote call statistics in real time and provide an easy way to review this information, so the operations team has an instantaneous view into the health of your application.</span></span> <span data-ttu-id="dbfbd-171">摘要說明遠端呼叫計量，例如延遲、 輸送量和 99 和 95 百分位數中的錯誤。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-171">Summarize remote call metrics, such as latency, throughput, and errors in the 99 and 95 percentiles.</span></span>

## <a name="transient-exceptions-and-retries"></a><span data-ttu-id="dbfbd-172">暫時性例外狀況和重試次數</span><span class="sxs-lookup"><span data-stu-id="dbfbd-172">Transient exceptions and retries</span></span>

<span data-ttu-id="dbfbd-173">追蹤一段時間，以發掘問題或失敗，在您的應用程式重試邏輯的暫時性例外狀況和重試次數的數目。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-173">Track the number of transient exceptions and retries over time to uncover issues or failures in your application's retry logic.</span></span> <span data-ttu-id="dbfbd-174">服務有問題，而且可能會失敗，可能表示遞增一段時間的例外狀況的趨勢。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-174">A trend of increasing exceptions over time may indicate that the service is having an issue and may fail.</span></span> <span data-ttu-id="dbfbd-175">如需詳細資訊，請參閱[重試服務的特定指引](../best-practices/retry-service-specific.md)。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-175">For more information, see [Retry service specific guidance](../best-practices/retry-service-specific.md).</span></span>

## <a name="early-warning-system"></a><span data-ttu-id="dbfbd-176">早期警告系統，</span><span class="sxs-lookup"><span data-stu-id="dbfbd-176">Early warning system</span></span>

<span data-ttu-id="dbfbd-177">監視您的應用程式的可能需要主動介入的跡象。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-177">Monitor your application for warning signs that might require proactive intervention.</span></span> <span data-ttu-id="dbfbd-178">評估應用程式和其相依性的整體健全狀況的工具可協助您快速識別當系統或其元件突然變成無法使用。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-178">Tools that assess the overall health of the application and its dependencies help you to recognize quickly when a system or its components suddenly become unavailable.</span></span> <span data-ttu-id="dbfbd-179">您可以使用它們來實作早期警告系統。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-179">Use them to implement an early warning system.</span></span>

1. <span data-ttu-id="dbfbd-180">識別您的應用程式的健全狀況，例如暫時性例外狀況和遠端呼叫延遲的關鍵效能指標。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-180">Identify the key performance indicators of your application's health, such as transient exceptions and remote call latency.</span></span>
1. <span data-ttu-id="dbfbd-181">在問題嚴重並需要復原回應之前，請找出問題的層級設定的臨界值。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-181">Set thresholds at levels that identify issues before they become critical and require a recovery response.</span></span>
1. <span data-ttu-id="dbfbd-182">達到臨界值時，傳送警示給營運小組。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-182">Send an alert to operations when the threshold value is reached.</span></span>

<span data-ttu-id="dbfbd-183">請考慮[Microsoft System Center 2016](https://www.microsoft.com/cloud-platform/system-center)或第三方工具，來提供監視功能。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-183">Consider [Microsoft System Center 2016](https://www.microsoft.com/cloud-platform/system-center) or third-party tools to provide monitoring capabilities.</span></span> <span data-ttu-id="dbfbd-184">大部分監視解決方案會追蹤關鍵效能計數器和服務可用性。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-184">Most monitoring solutions track key performance counters and service availability.</span></span> <span data-ttu-id="dbfbd-185">[Azure 資源健康狀態](/azure/service-health/resource-health-checks-resource-types)提供一些內建的健康狀態檢查，有助於診斷的 Azure 服務的節流。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-185">[Azure resource health](/azure/service-health/resource-health-checks-resource-types) provides some built-in health status checks, which can help diagnose throttling of Azure services.</span></span>

## <a name="subscription-and-service-limitations"></a><span data-ttu-id="dbfbd-186">訂用帳戶和服務限制</span><span class="sxs-lookup"><span data-stu-id="dbfbd-186">Subscription and service limitations</span></span>

<span data-ttu-id="dbfbd-187">Azure 訂用帳戶會限制對特定資源類型，例如資源群組、 核心和儲存體帳戶的數目。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-187">Azure subscriptions have limits on certain resource types, such as number of resource groups, cores, and storage accounts.</span></span> <span data-ttu-id="dbfbd-188">若要確保您的應用程式不會執行對 Azure 訂用帳戶限制，請建立輪詢服務接近其限制和配額的警示。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-188">To ensure that your application doesn't run up against Azure subscription limits, create alerts that poll for services nearing their limits and quotas.</span></span>

<span data-ttu-id="dbfbd-189">解決下列訂用帳戶限制的警示。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-189">Address the following subscription limits with alerts.</span></span>

### <a name="individual-services"></a><span data-ttu-id="dbfbd-190">個別服務</span><span class="sxs-lookup"><span data-stu-id="dbfbd-190">Individual services</span></span>

<span data-ttu-id="dbfbd-191">個別的 Azure 服務都有儲存體、 輸送量、 連接數目、 每秒的要求以及其他計量的耗用量限制。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-191">Individual Azure services have consumption limits on storage, throughput, number of connections, requests per second, and other metrics.</span></span> <span data-ttu-id="dbfbd-192">如果它嘗試使用超出這些限制，導致服務節流和可能停機的資源，您的應用程式將會失敗。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-192">Your application will fail if it attempts to use resources beyond these limits, resulting in service throttling and possible downtime.</span></span>

<span data-ttu-id="dbfbd-193">根據特定的服務和應用程式的需求，您可以經常保持在這些限制由相應增加 （例如，選擇另一個定價層，） 或相應放大 （新增新的執行個體）。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-193">Depending on the specific service and your application requirements, you can often stay under these limits by scaling up (choosing another pricing tier, for example) or scaling out (adding new instances).</span></span>

### <a name="azure-storage-scalability-and-performance-targets"></a><span data-ttu-id="dbfbd-194">Azure 儲存體延展性和效能目標</span><span class="sxs-lookup"><span data-stu-id="dbfbd-194">Azure storage scalability and performance targets</span></span>

<span data-ttu-id="dbfbd-195">Azure 可讓每個訂用帳戶的儲存體帳戶的數目上限。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-195">Azure allows a maximum number of storage accounts per subscription.</span></span> <span data-ttu-id="dbfbd-196">如果您的應用程式需要比您訂用帳戶中目前可用的儲存體帳戶，請使用額外的儲存體帳戶建立新的訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-196">If your application requires more storage accounts than are currently available in your subscription, create a new subscription with additional storage accounts.</span></span> <span data-ttu-id="dbfbd-197">如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](/azure/azure-subscription-service-limits/#storage-limits)。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-197">For more information, see [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits/#storage-limits).</span></span>

<span data-ttu-id="dbfbd-198">如果您超過 Azure 儲存體延展性和效能目標，您的應用程式就會發生儲存體節流。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-198">If you exceed Azure storage scalability and performance targets, your application will experience storage throttling.</span></span> <span data-ttu-id="dbfbd-199">如需詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](/azure/storage/storage-scalability-targets/)。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-199">For more information, see [Azure Storage scalability and performance targets](/azure/storage/storage-scalability-targets/).</span></span>

### <a name="scalability-targets-for-virtual-machine-disks"></a><span data-ttu-id="dbfbd-200">虛擬機器磁碟的延展性目標</span><span class="sxs-lookup"><span data-stu-id="dbfbd-200">Scalability targets for virtual machine disks</span></span>

<span data-ttu-id="dbfbd-201">Azure 基礎結構即服務 (IaaS) VM 支援附加資料磁碟數目，取決於許多因素，包括 VM 大小和儲存體帳戶類型。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-201">An Azure infrastructure as a service (IaaS) VM supports attaching a number of data disks, depending on several factors, including the VM size and the type of storage account.</span></span> <span data-ttu-id="dbfbd-202">如果您的應用程式超過虛擬機器磁碟的延展性目標，請佈建額外的儲存體帳戶並在其中建立虛擬機器磁碟。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-202">If your application exceeds the scalability targets for virtual machine disks, provision additional storage accounts and create the virtual machine disks there.</span></span> <span data-ttu-id="dbfbd-203">如需詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](/azure/storage/storage-scalability-targets/#scalability-targets-for-virtual-machine-disks)。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-203">For more information, see [Azure Storage scalability and performance targets](/azure/storage/storage-scalability-targets/#scalability-targets-for-virtual-machine-disks).</span></span>

### <a name="vm-size"></a><span data-ttu-id="dbfbd-204">VM 大小</span><span class="sxs-lookup"><span data-stu-id="dbfbd-204">VM size</span></span>

<span data-ttu-id="dbfbd-205">如果實際的 CPU、 記憶體、 磁碟和 Vm 的 I/O 處理的 VM 大小的限制，請在您的應用程式可能會遇到容量問題。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-205">If the actual CPU, memory, disk, and I/O of your VMs approach the limits of the VM size, your application may experience capacity issues.</span></span> <span data-ttu-id="dbfbd-206">若要更正問題，請增加 VM 大小。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-206">To correct the issues, increase the VM size.</span></span> <span data-ttu-id="dbfbd-207">VM 大小所述[在 Azure 中的虛擬機器的大小](/azure/virtual-machines/virtual-machines-windows-sizes/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-207">VM sizes are described in [Sizes for virtual machines in Azure](/azure/virtual-machines/virtual-machines-windows-sizes/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).</span></span>

<span data-ttu-id="dbfbd-208">如果您的工作負載隨著波動，請考慮使用虛擬機器擴展集自動調整 VM 執行個體數目。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-208">If your workload fluctuates over time, consider using virtual machine scale sets to automatically scale the number of VM instances.</span></span> <span data-ttu-id="dbfbd-209">否則，您必須以手動增加或減少 Vm 數目。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-209">Otherwise, you need to manually increase or decrease the number of VMs.</span></span> <span data-ttu-id="dbfbd-210">如需詳細資訊，請參閱 <<c0> [ 虛擬機器擴展集概觀](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview/)。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-210">For more information, see the [virtual machine scale sets overview](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview/).</span></span>

### <a name="azure-sql-database"></a><span data-ttu-id="dbfbd-211">連接字串</span><span class="sxs-lookup"><span data-stu-id="dbfbd-211">Azure SQL Database</span></span>

<span data-ttu-id="dbfbd-212">如果您的 Azure SQL Database 層不適合用來處理您的應用程式資料庫交易單位 (DTU) 需求，您的資料使用會受到節流控制。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-212">If your Azure SQL Database tier isn't adequate to handle your application's Database Transaction Unit (DTU) requirements, your data use will be throttled.</span></span> <span data-ttu-id="dbfbd-213">如需有關如何選取正確的服務方案的詳細資訊，請參閱 <<c0> [ 購買模型的 Azure SQL Database](/azure/sql-database/sql-database-service-tiers/)。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-213">For more information on selecting the correct service plan, see [Azure SQL Database purchasing models](/azure/sql-database/sql-database-service-tiers/).</span></span>

## <a name="third-party-services"></a><span data-ttu-id="dbfbd-214">協力廠商服務</span><span class="sxs-lookup"><span data-stu-id="dbfbd-214">Third-party services</span></span>

<span data-ttu-id="dbfbd-215">如果您的應用程式會對第三方服務中的相依性，找出如何這些服務可能會失敗，並影響失敗會有在您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-215">If your application has dependencies on third-party services, identify how these services can fail and what effect failures will have on your application.</span></span>

<span data-ttu-id="dbfbd-216">第三方服務可能不會包含監視和診斷。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-216">A third-party service might not include monitoring and diagnostics.</span></span> <span data-ttu-id="dbfbd-217">記錄這些服務的呼叫，並將它們與您的應用程式健康情況和使用的唯一識別碼的診斷記錄相互關聯。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-217">Log calls to these services and correlate them with your application's health and diagnostic logging using a unique identifier.</span></span> <span data-ttu-id="dbfbd-218">如需有關監視和診斷的已經實證做法的詳細資訊，請參閱[監視和診斷指引](../best-practices/monitoring.md)。</span><span class="sxs-lookup"><span data-stu-id="dbfbd-218">For more information on proven practices for monitoring and diagnostics, see [Monitoring and diagnostics guidance](../best-practices/monitoring.md).</span></span>

## <a name="next-steps"></a><span data-ttu-id="dbfbd-219">後續步驟</span><span class="sxs-lookup"><span data-stu-id="dbfbd-219">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="dbfbd-220">災害復原計劃</span><span class="sxs-lookup"><span data-stu-id="dbfbd-220">Plan for disaster recovery</span></span>](./disaster-recovery.md)

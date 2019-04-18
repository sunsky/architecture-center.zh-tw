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
# <a name="monitoring-application-health-for-reliability-in-azure"></a>在 Azure 中的可靠性監視應用程式的健全狀況

監視和診斷對於復原非常重要。 如果發生失敗，您需要知道*所*失敗，它*時*失敗&mdash;和*為什麼*。

*監視*不是相同*失敗偵測*。 例如，您的應用程式可能會偵測到暫時性錯誤然後重試，避免停機時間。 但是，好讓您可以監視錯誤率，以取得整體的應用程式健全狀況，它應該也記錄重試作業。

監視和診斷程序視為具有四個不同階段的管線：檢測、 集合和儲存體、 分析、 診斷和視覺效果及警示。

## <a name="instrumentation"></a>檢測

檢測是索引鍵，因此不適合直接，監視您的應用程式。 大規模的分散式的系統可能會多上執行的虛擬機器 (Vm)，會被加入和移除一段時間。 同樣地，雲端應用程式可能會使用幾個資料存放區，並以單一使用者動作可能會跨越多個子系統。

提供豐富的檢測：

- 針對失敗的可能但尚未發生，請提供足夠的資料來判斷其原因，緩和情況，並確保系統仍然可用。
- 針對已發生的失敗，應用程式應該將適當的錯誤訊息傳回給使用者，但應該嘗試繼續執行，但是有減少的功能。

監視系統應該擷取完整的詳細資料，以便可以有效率地還原應用程式，並視需要設計人員和開發人員可以修改系統，以防的重複發生這種情況。

監視未經處理的資料可以來自各種來源，包括：

- 應用程式記錄檔，例如所產生[Azure Application Insights](/azure/azure-monitor/app/app-insights-overview)服務。
- 作業系統效能標準收集[Azure 監視代理程式](/azure/azure-monitor/platform/agents-overview)。
- [Azure 資源](/azure/azure-monitor/platform/metrics-supported)，包括標準 Azure 監視器所收集。
- [Azure 服務健康情況](/azure/service-health/service-health-overview)，提供可協助您追蹤作用中的事件儀表板。
- [Azure AD 記錄檔](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) 內建於 Azure 平台。

大部分的 Azure 服務有計量和診斷工具，您可以設定來分析及判斷問題的原因。 若要進一步了解，請參閱[Azure 監視器所收集的監視資料](/azure/azure-monitor/platform/data-collection)。

## <a name="collection-and-storage"></a>收集和儲存體

原始檢測資料可以保留在各種位置和格式，包括：

- 應用程式追蹤記錄
- IIS 記錄
- 效能計數器

收集、 彙總，並放在 Azure 中，例如 Application Insights、 Azure 監視器計量、 服務健康狀態、 儲存體帳戶和 Azure Log Analytics 的可靠的資料存放區在這些不同的來源。

## <a name="analysis-and-diagnosis"></a>分析及診斷

分析資料合併在針對問題進行疑難排解，並取得應用程式健全狀況的整體檢視這些資料存放區中。 一般而言，您可以[搜尋和分析](/azure/azure-monitor/log-query/log-query-overview)Application Insights 和 Log Analytics 中使用 Kusto 查詢或使用預先設定的檢視圖形中的資料 [管理解決方案](/azure/azure-monitor/insights/solutions-inventory)。 或使用 Azure Advisor 來檢視有關建議著重[恢復](/azure/advisor/advisor-high-availability-recommendations)並[效能](/azure/advisor/advisor-performance-recommendations)。

## <a name="visualization-and-alerts"></a>視覺效果和警示

呈現遙測資料格式，可讓您更容易注意到問題和趨勢，例如儀表板或電子郵件警示操作員。

取得所使用的應用程式狀態的完整堆疊檢視 [Azure 儀表板](/azure/azure-portal/azure-portal-dashboards)建立監視圖形從 Application Insights，Log Analytics、 Azure 監視器計量和服務健全狀況的彙總的檢視。 並用 [Azure 監視器警示](/azure/azure-monitor/platform/alerts-overview)建立服務健康狀態，資源健康狀態，Azure 監視器計量通知記錄在 Log Analytics 和 Application Insights。

如需有關監視和診斷的詳細資訊，請參閱 <<c0> [ 監視和診斷](../best-practices/monitoring.md)。

## <a name="health-probes-and-check-functions"></a>健康情況探查和檢查函式

應用程式的效能與健康情況可能會降低經過一段時間，並降低應用程式失敗之前，可能無法明顯。

實作探查或檢查函式，並執行定期從外部應用程式。 這些檢查可以非常簡單，做為測量回應時間在整個應用程式、 應用程式的個別部分、 應用程式所使用的特定服務或個別的元件。

檢查函數可以執行程序，以確保它們會產生有效的結果、 測量延遲及檢查可用性，並從系統擷取資訊。

## <a name="long-running-workflow-failures"></a>長時間執行工作流程失敗

長時間執行的工作流程通常包含多個步驟，其中每一個都應該獨立。

追蹤長時間執行的程序，將整個工作流程必須回復或多個補償交易，就必須執行的可能性降至最低的進度。

>[!TIP]
> 監視和管理這類實作模式的長時間執行工作流程的進度[排程器代理程式監督員](../patterns/scheduler-agent-supervisor.md)。

## <a name="application-logs"></a>應用程式記錄

應用程式記錄是診斷資料的重要來源。 若要深入了解當您在需要時最，請遵循應用程式記錄的最佳作法。

### <a name="log-data-in-the-production-environment"></a>在生產環境中的記錄資料

雖然應用程式正在執行生產環境中，因此您有足夠的資訊來診斷問題，在生產環境狀態的原因，請擷取強固的遙測資料。

### <a name="log-events-at-service-boundaries"></a>在服務界限的記錄檔事件

包含流過服務界限的相互關聯識別碼。 如果交易流經多個服務，其中會失敗，相互關聯識別碼可協助您追蹤您的應用程式和交易失敗的原因會指出要求。

### <a name="use-semantic-structured-logging"></a>使用語意 （結構化） 的記錄

使用結構化記錄檔，就將自動化的耗用量及分析記錄資料，也就是特別重要雲端規模的工作變得更容易。 一般而言，我們建議將 Azure 資源計量和診斷資料儲存在 Log Analytics 工作區中，而不儲存體帳戶中。 如此一來，您可以使用 Kusto 查詢，以取得您想要快速地結構化格式的資料。 您也可以使用 Azure 監視器 Api 和 Azure Log Analytics Api。

### <a name="use-asynchronous-logging"></a>使用非同步記錄

同步記錄作業有時候會封鎖應用程式程式碼，導致備份記錄檔會寫入的要求。 使用非同步記錄，以保留應用程式記錄期間的可用性。

### <a name="separate-application-logging-from-auditing"></a>個別的應用程式記錄從稽核

稽核記錄通常會維護的合規性或法規的需求，而且必須是完整。 若要避免已卸除的交易，維護稽核記錄檔與診斷記錄檔分開。

## <a name="remote-call-statistics"></a>遠端呼叫統計資料

追蹤和報告遠端呼叫即時的統計資料，並提供簡單的方式，來檢閱這項資訊，讓作業小組擁有的即時檢視您的應用程式的健全狀況。 摘要說明遠端呼叫計量，例如延遲、 輸送量和 99 和 95 百分位數中的錯誤。

## <a name="transient-exceptions-and-retries"></a>暫時性例外狀況和重試次數

追蹤一段時間，以發掘問題或失敗，在您的應用程式重試邏輯的暫時性例外狀況和重試次數的數目。 服務有問題，而且可能會失敗，可能表示遞增一段時間的例外狀況的趨勢。 如需詳細資訊，請參閱[重試服務的特定指引](../best-practices/retry-service-specific.md)。

## <a name="early-warning-system"></a>早期警告系統，

監視您的應用程式的可能需要主動介入的跡象。 評估應用程式和其相依性的整體健全狀況的工具可協助您快速識別當系統或其元件突然變成無法使用。 您可以使用它們來實作早期警告系統。

1. 識別您的應用程式的健全狀況，例如暫時性例外狀況和遠端呼叫延遲的關鍵效能指標。
1. 在問題嚴重並需要復原回應之前，請找出問題的層級設定的臨界值。
1. 達到臨界值時，傳送警示給營運小組。

請考慮[Microsoft System Center 2016](https://www.microsoft.com/cloud-platform/system-center)或第三方工具，來提供監視功能。 大部分監視解決方案會追蹤關鍵效能計數器和服務可用性。 [Azure 資源健康狀態](/azure/service-health/resource-health-checks-resource-types)提供一些內建的健康狀態檢查，有助於診斷的 Azure 服務的節流。

## <a name="subscription-and-service-limitations"></a>訂用帳戶和服務限制

Azure 訂用帳戶會限制對特定資源類型，例如資源群組、 核心和儲存體帳戶的數目。 若要確保您的應用程式不會執行對 Azure 訂用帳戶限制，請建立輪詢服務接近其限制和配額的警示。

解決下列訂用帳戶限制的警示。

### <a name="individual-services"></a>個別服務

個別的 Azure 服務都有儲存體、 輸送量、 連接數目、 每秒的要求以及其他計量的耗用量限制。 如果它嘗試使用超出這些限制，導致服務節流和可能停機的資源，您的應用程式將會失敗。

根據特定的服務和應用程式的需求，您可以經常保持在這些限制由相應增加 （例如，選擇另一個定價層，） 或相應放大 （新增新的執行個體）。

### <a name="azure-storage-scalability-and-performance-targets"></a>Azure 儲存體延展性和效能目標

Azure 可讓每個訂用帳戶的儲存體帳戶的數目上限。 如果您的應用程式需要比您訂用帳戶中目前可用的儲存體帳戶，請使用額外的儲存體帳戶建立新的訂用帳戶。 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](/azure/azure-subscription-service-limits/#storage-limits)。

如果您超過 Azure 儲存體延展性和效能目標，您的應用程式就會發生儲存體節流。 如需詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](/azure/storage/storage-scalability-targets/)。

### <a name="scalability-targets-for-virtual-machine-disks"></a>虛擬機器磁碟的延展性目標

Azure 基礎結構即服務 (IaaS) VM 支援附加資料磁碟數目，取決於許多因素，包括 VM 大小和儲存體帳戶類型。 如果您的應用程式超過虛擬機器磁碟的延展性目標，請佈建額外的儲存體帳戶並在其中建立虛擬機器磁碟。 如需詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](/azure/storage/storage-scalability-targets/#scalability-targets-for-virtual-machine-disks)。

### <a name="vm-size"></a>VM 大小

如果實際的 CPU、 記憶體、 磁碟和 Vm 的 I/O 處理的 VM 大小的限制，請在您的應用程式可能會遇到容量問題。 若要更正問題，請增加 VM 大小。 VM 大小所述[在 Azure 中的虛擬機器的大小](/azure/virtual-machines/virtual-machines-windows-sizes/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

如果您的工作負載隨著波動，請考慮使用虛擬機器擴展集自動調整 VM 執行個體數目。 否則，您必須以手動增加或減少 Vm 數目。 如需詳細資訊，請參閱 <<c0> [ 虛擬機器擴展集概觀](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview/)。

### <a name="azure-sql-database"></a>連接字串

如果您的 Azure SQL Database 層不適合用來處理您的應用程式資料庫交易單位 (DTU) 需求，您的資料使用會受到節流控制。 如需有關如何選取正確的服務方案的詳細資訊，請參閱 <<c0> [ 購買模型的 Azure SQL Database](/azure/sql-database/sql-database-service-tiers/)。

## <a name="third-party-services"></a>協力廠商服務

如果您的應用程式會對第三方服務中的相依性，找出如何這些服務可能會失敗，並影響失敗會有在您的應用程式。

第三方服務可能不會包含監視和診斷。 記錄這些服務的呼叫，並將它們與您的應用程式健康情況和使用的唯一識別碼的診斷記錄相互關聯。 如需有關監視和診斷的已經實證做法的詳細資訊，請參閱[監視和診斷指引](../best-practices/monitoring.md)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [災害復原計劃](./disaster-recovery.md)

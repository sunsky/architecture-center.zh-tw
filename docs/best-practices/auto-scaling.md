---
title: 自動調整指引
titleSuffix: Best practices for cloud applications
description: 如何自動調整以動態方式配置應用程式所需資源的指引。
author: dragon119
ms.date: 05/17/2017
ms.topic: best-practice
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 8b1d614460b8c0bd9655d7b75cd971767e34ee3f
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54483610"
---
# <a name="autoscaling"></a>自動調整

自動調整是以動態方式配置資源以符合效能需求的程序。 隨著工作量的成長，應用程式可能需要額外的資源來保有所需的效能層級及滿足服務等級協定 (SLA)。 當需要降低而不再需要其他資源，可以將它們解除配置以將成本降至最低。

自動調整會充分利用雲端裝載環境的彈性，以降低管理負擔。 它可減少操作員持續監視系統效能的需求，並做出有關新增或移除資源的決策。

應用程式有兩種主要方式可調整：

- **垂直縮放比例**，也稱為相應增加和相應減少，表示變更資源的容量。 例如，您可以將應用程式移動為較大的 VM 大小。 垂直調整通常要求在重新部署時系統必須暫時無法使用。 因此，將垂直調整自動化較不常見。

- **水平調整**，也稱為向外和向內調整，表示新增或移除資源的執行個體。 在佈建這些新資源時，應用程式會繼續執行而不中斷。 佈建程序完成時，解決方案即會部署在這些額外的資源上。 如果要求降低，則可以完全關閉及取消配置這些額外的資源。

許多雲端系統 (包括 Microsoft Azure) 都支援自動的垂直調整。 本文的其餘部分著重於水平調整。

> [!NOTE]
> 自動調整大多適用於計算資源。 雖然可以水平調整資料庫或訊息佇列，這通常牽涉到一般不會自動化的[資料分割](./data-partitioning.md)。

## <a name="overview"></a>概觀

自動調整策略通常牽涉到下列部分：

- 檢測和監視應用程式、服務和基礎結構層級上的系統。 這些系統會擷取關鍵計量，例如回應時間、佇列長度、CPU 使用率及記憶體使用量。
- 針對預先定義的臨界值或排程評估這些計量，並決定是否要調整的決策制訂邏輯。
- 調整系統的元件。
- 測試、監視和微調自動調整策略，以確保它能正常運作。

Azure 提供可處理常見情節的內建自動調整機制。 如果特定服務或技術並沒有內建的自動調整功能，或如果您有功能之外的特定自動調整需求，則可以考慮自訂實作。 自訂實作會收集作業和系統計量、分析計量資訊，然後據以調整資源。

## <a name="configure-autoscaling-for-an-azure-solution"></a>為 Azure 解決方案設定自動調整

Azure 針對多數運算選項提供內建的自動調整。

- **虛擬機器**透過使用 [VM 擴展集][vm-scale-sets] (以群組的形式來管理一組 Azure 虛擬機器的方法) 來支援自動調整。 請參閱[如何使用自動調整與虛擬機器擴展集][vm-scale-sets-autoscale]。

- **Service Fabric** 也透過 VM 擴展集來支援自動調整。 Service Fabric 叢集中的每個節點類型都會設定為個別的 VM 擴展集。 這樣一來，每個節點類型可獨立相應縮小或相應放大。 請參閱[使用自動調整規則相應縮小或放大 Service Fabric 叢集][service-fabric-autoscale]。

- **Azure App Service** 具有內建的自動調整。 自動調整設定會套用到 App Service 內的所有應用程式。 請參閱[手動或自動調整執行個體計數][app-service-autoscale]。

- **Azure 雲端服務**在角色層級有內建的自動調整。 請參閱[如何在入口網站中設定雲端服務的自動調整][cloud-services-autoscale]。

這些計算選項全都使用 [Azure 監視器自動調整][monitoring]來提供一組常用的自動調整功能。

- **Azure Functions** 與先前的計算選項不同，因為您不需要設定任何自動調整規則。 相反地，Azure Functions 會自動配置您的程式碼時執行的運算能力，視需要向外擴充以處理負載。 如需詳細資訊，請參閱[選擇正確的 Azure Functions 裝載方案][functions-scale]。

最後，自訂的自動調整解決方案有時會很實用。 例如，您可以使用 Azure 診斷和以應用程式為基礎的計量，以及自訂程式碼來監視和匯出應用程式計量。 然後您可以根據這些計量定義自訂規則，並使用 Resource Manager REST API 來觸發自動調整。 不過，自訂解決方案並不容易實作，只應在前述方法都無法滿足您的需求時才加以考慮。

如果平台的內建自動調整功能可符合您的需求，就使用它們。 如果沒有，請仔細考量您是否需要更複雜的縮放功能。 需求的部分範例包括更細微的控制程度、偵測觸發事件進行調整的其他方式、跨訂用帳戶進行調整，以及調整其他資源類型。

## <a name="use-azure-monitor-autoscale"></a>使用 Azure 監視器自動調整

[Azure 監視器自動調整][monitoring]為 VM 擴展集、Azure App Service，以及 Azure 雲端服務提供一組常用的自動調整功能。 調整可以根據排程執行或根據執行階段計量 (例如 CPU 或記憶體使用量) 執行。 範例：

- 在工作日相應放大為 10 個執行個體，以及在星期六和星期日相應縮小為 4 個執行個體。
- 如果平均 CPU 使用量超過 70%，則相應放大一個執行個體，而如果 CPU 使用量低於 50%，則相應縮小一個執行個體。
- 如果佇列中的訊息數目超過某個臨界值，則相應放大一個執行個體。

如需內建計量的清單，請參閱 [Azure 監視器的自動調整常見計量][autoscale-metrics]。 您也可以使用 Application Insights 來實作自訂的計量。

您可以使用 PowerShell、Azure CLI、Azure Resource Manager 範本或 Azure 入口網站來設定自動調整。 如需更詳細的控制，請使用 [Azure Resource Manager REST API](https://msdn.microsoft.com//library/azure/dn790568.aspx)。 [Azure 監視服務管理程式庫](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring)和 [Microsoft Insights 程式庫](https://www.nuget.org/packages/Microsoft.Azure.Insights/) (預覽版) 是可讓您從不同的資源收集計量以及利用 REST API 執行自動調整的 SDK。 對於不支援 Azure Resource Manager 的資源，或是當您使用 Azure 雲端服務時，都可以使用服務管理 REST API 進行自動調整。 在所有其他情況下，請使用 Azure Resource Manager。

使用 Azure 自動調整時，請考慮下列幾點：

- 請考慮您是否可以善加預測應用程式負載，以便只依賴排程自動調整，新增和移除執行個體以滿足要求的預期尖峰期。 如果不可行，請使用基於執行階段計量的反應式自動調整，以便處理無法預期的需求變更。 一般而言，您可以結合這些方法。 例如，建立會根據應用程式何時最忙碌的排程新增資源的策略。 這有助於確保容量在需要時可供使用，不會因啟動新執行個體發生任何延遲。 針對每個排程的規則，定義在該期間內允許被動式自動調整的計量，以確保應用程式能夠處理持續但無法預期的要求尖峰期。

- 通常很難了解計量和容量需求之間的關聯性，尤其是在最初部署應用程式的時候。 一開始多佈建一些額外的容量，然後加以監視並調整自動調整規則，使容量更接近實際負載。

- 設定自動調整規則，然後監視經過一段時間的應用程式效能。 如有必要，使用此監視結果來調整系統的調整方式。 不過，請記住，自動調整不是即時生效的程序 它需要時間來回應計量，例如平均 CPU 使用率超過 (或低於) 指定的臨界值。

- 根據測量的觸發程序屬性 (例如 CPU 使用量或佇列長度) 使用偵測機制的自動調整規則，會使用經過一段時間的彙總值 (而不是瞬間值) 來觸發自動調整動作。 根據預設，彙總是值的平均。 這可防止系統反應太快，或造成快速震盪。 這也可以讓自動啟動的新執行個體順利進入執行模式，避免新執行個體正在啟動時，又發生其他自動調整動作。 若是 Azure 雲端服務和 Azure 虛擬機器，彙總的預設期間為 45 分鐘，因此，計量需要經過這段時間後再觸發自動調整以回應尖峰需求量。 您可以使用 SDK 來變更彙總期間，但請注意，若其間低於 25 分鐘，可能會造成無法預期的結果。 若是 Web Apps，平均期間較短，允許在平均觸發量值變更約五分鐘後提供新執行個體。

- 如果您使用 SDK 設定自動調整，而不是使用入口網站，您可以指定更詳細的作用中規則排程期間。 您也可以建立您自己的度量，並在自動調整規則中與現有度量一起使用。 例如，您可能想要使用替代計數器 (例如，每秒要求數或平均記憶體可用性)，或使用自訂計數器來測量特定商務程序。

- 自動調整 Service Fabric 時，節點類型是由後端的 VM 擴展集建立，所以您必須為每個節點類型設定自動調整規則。 請先考慮一定要有的節點數目，再設定自動調整規模。 主要節點類型一定要有的節點數目下限是由您已選擇的可靠性層級決定。 如需詳細資訊，請參閱[使用自動調整規則相應縮小或放大 Service Fabric 叢集](/azure/service-fabric/service-fabric-cluster-scale-up-down)。

- 您可以使用入口網站，將 SQL Database 執行個體和佇列之類的資源連結至雲端服務執行個體。 這可讓您更輕鬆地存取每個連結資源的個別手動和自動調整設定選項。 如需詳細資訊，請參閱[操作說明：將資源連結至雲端服務](/azure/cloud-services/cloud-services-how-to-manage)。

- 當您設定多個原則和規則時，它們有可能互相衝突。 自動調整會使用下列衝突解決規則，來確保一定有足量的執行中執行個體：
  - 相應放大作業一律優先於相應縮小作業。
  - 當相應放大作業發生衝突時，起始執行個體數目增幅最多的規則優先。
  - 當相應縮小作業發生衝突時，起始執行個體數目降幅最少的規則優先。

- 在 App Service 環境中，任何背景工作集區或前端計量均可用來定義自動調整規則。 如需詳細資訊，請參閱[自動調整和 App Service 環境](/azure/app-service/app-service-environment-auto-scale)。

## <a name="application-design-considerations"></a>應用程式設計考量

自動調整不是立即見效的解決方案。 只是將資源加入至系統或執行更多處理程序執行個體，並不保證能改善系統效能。 設計自動調整策略時，請考慮下列幾點：

- 系統必須設計為可以水平調整。 不要假設執行個體具有同質性；不要設計出程式碼一律在特定的處理程序執行個體中執行的解決方案。 水平調整雲端服務或網站時，不要假設一系列來自相同來源的要求一律會路由傳送至相同的執行個體。 基於相同理由，請將服務設計成無狀態，以避免需將一連串來自應用程式的要求一律路由傳送至相同的服務執行個體。 在設計從佇列讀取訊息並處理它們的服務時，不要假設哪個服務的執行個體會處理特定訊息。 自動調整會在佇列長度增長時，啟動其他的服務執行個體。 [競爭取用者模式](../patterns/competing-consumers.md)說明如何解決這種情況。

- 如果解決方案會實作長時間執行的工作，請將此工作設計為同時支援相應縮小和相應放大。 如果不小心，這類工作會在系統相應放大時阻止處理程序執行個體完全關閉，或者，如果處理程序被強制終止，則可能會遺失資料。 理想的情況是，重整長時間執行工作並分解處理，讓其以較小且不連續的區塊執行。 [管道與篩選模式](../patterns/pipes-and-filters.md)提供如何達成的範例。

- 或者，您可以實作檢查點機制，定期記錄工作的狀態資訊，並將此狀態儲存在執行工作之任何處理程序執行個體可以存取的耐久性儲存體中。 如此一來，如果處理程序關閉，它所執行的工作可以使用另一個執行個體，從最後一個檢查點繼續進行。

- 當背景工作在不同的計算執行個體上執行 (例如已裝載雲端服務之應用程式的背景工作角色)，您可能需要使用不同的調整原則來調整應用程式的不同部分。 例如，您可能需要部署其他使用者介面 (UI) 來計算執行個體，而不增加背景計算執行個體數目，或是相反。 如果您提供不同層級的服務 (例如基本和進階服務套件) 時，相較於基本服務套件，您可能需要更積極相應放大進階服務套件的計算資源，才能符合 SLA。

- 請考慮使用 UI 和背景計算執行個體通訊的佇列長度，作為自動調整策略的準則。 這是不平衡狀態或目前負載和背景工作處理能力之間差異的最佳指標。

- 如果您的自動調整策略是根據測量商務程序 (例如每小時訂單數或複雜交易的平均執行時間) 的計數器，請確定您完全了解這些計數器類型的結果與實際計算容量需求之間的關係。 您可能需要調整多個元件或計算單位，以因應商務程序計數器的變更。

- 若要防止系統過度相應放大，並避免執行數千個執行個體的相關聯成本，請考慮限制可以自動加入的執行個體數目上限。 大部分動調整機制可讓您指定規則的執行個體數目上下限。 此外，如果部署的執行個體數目已達上限而系統仍然超載，請考慮適當地降低系統提供的功能。

- 請記住，自動調整可能不是處理工作負載中突發高載狀況的最適當機制。 佈建並啟動新服務執行個體或將資源新增到系統都需要花時間，而當這些額外資源可供使用時，尖峰需求可能已過。 在這種情況下，節流服務可能比較適合。 如需詳細資訊，請參閱[節流模式](../patterns/throttling.md)。

- 相反地，如果您需要在交易量快速波動時足以處理所有要求的能力，而且成本不是主要考量因素，那麼請考慮使用積極的自動調整策略，以更快速啟動額外的執行個體。 您也可以使用排程原則，在最大負載來臨前預先啟動足量的執行個體。

- 自動調整機制應該監視自動調整程序，並記錄每個自動調整事件的詳細資料 (觸發的事件、加入或移除哪些資源，以及發生時間)。 如果您建立自訂自動調整機制，請確定它包含這項功能。 分析資訊，以協助測量自動調整策略的有效性，並視需要加以微調。 您可以在使用模式變得更明顯時短時間內進行調整，以及在業務量拓展或對應用程式的需求演變時長期進行調整。 如果應用程式達到定義的自動調整上限，機制也可以警告操作員，讓操作員視需要手動啟動其他資源。 請注意，在這種情況下，操作員可能也需在工作負載減輕後，手動移除這些資源。

## <a name="related-patterns-and-guidance"></a>相關的模式和指導方針

實作自動調整時，下列模式和指引也可能與您的案例相關：

- [節流模式](../patterns/throttling.md)。 此模式說明當需求增加而對資源產生極大負載時，應用程式如何繼續運作並符合 SLA。 節流可以搭配自動調整，用來避免系統相應縮小時超過負荷。

- [競爭取用者模式](../patterns/competing-consumers.md)。 這個模式說明如何實作服務執行個體集區，以便處理來自任何應用程式執行個體的訊息。 自動調整可用來啟動和停止服務執行個體，以符合預期的工作負載。 此方法可讓系統同時處理多個訊息，以達到最佳輸送量、增進延展性及可用性，以及平衡工作負載。

- [監視和診斷](./monitoring.md)。 檢測和遙測對於收集資訊以進行自動調整程序相當重要。

<!-- links -->

[monitoring]: /azure/monitoring-and-diagnostics/monitoring-overview-autoscale
[app-service-autoscale]: /azure/monitoring-and-diagnostics/insights-how-to-scale?toc=%2fazure%2fapp-service-web%2ftoc.json#scaling-based-on-a-pre-set-metric
[app-service-plan]: /azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview
[autoscale-metrics]: /azure/monitoring-and-diagnostics/insights-autoscale-common-metrics
[cloud-services-autoscale]: /azure/cloud-services/cloud-services-how-to-scale-portal
[functions-scale]: /azure/azure-functions/functions-scale
[link-resource-to-cloud-service]: /azure/cloud-services/cloud-services-how-to-manage#how-to-link-a-resource-to-a-cloud-service
[service-fabric-autoscale]: /azure/service-fabric/service-fabric-cluster-scale-up-down
[vm-scale-sets]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview
[vm-scale-sets-autoscale]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview

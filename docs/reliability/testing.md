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
# <a name="testing-azure-applications-for-resiliency-and-availability"></a>測試 Azure 應用程式的恢復功能和可用性

若要測試復原，您應該確認端對端工作負載間歇性失敗的情況下執行的方式。

使用綜合與真實的使用者資料的生產環境中執行測試。 測試和生產環境很少完全相同，因此請務必驗證您的應用程式在生產環境中使用[藍綠](https://martinfowler.com/bliki/BlueGreenDeployment.html)或是[canary 部署](https://martinfowler.com/bliki/CanaryRelease.html)。 如此一來，如此便能確定，它會如預期般運作完整部署時，正在測試真實狀況下，應用程式。

測試計劃的一部分，包括：

- 自動化部署前測試
- 錯誤插入測試
- 尖峰負載測試
- 災害復原測試
- 第三方服務測試

測試是反覆的程序。 測試應用程式、測量結果、分析及解決所發生的任何失敗，並重複此程序。

## <a name="perform-fault-injection-testing"></a>執行錯誤插入測試

針對錯誤插入測試，檢查系統的復原期間失敗，方法為觸發實際的失敗或加以模擬。 以下是一些引發失敗的策略：

- 關閉虛擬機器 (VM) 執行個體。
- 毀損程序。
- 憑證到期。
- 變更存取金鑰。
- 關閉網域控制站上的 DNS 服務。
- 限制可用的系統資源，例如 RAM 或執行緒的數目。
- 取消掛接磁碟。
- 重新部署 VM。

測試計劃應該納入設計階段中，除了常見的失敗案例期間識別可能的失敗點：

- 生產環境盡可能接近的環境中測試您的應用程式。
- 組合中的測試失敗。
- 測量復原時間，而且確定符合您的業務需求。
- 確認失敗不會重疊，而且會以隔離的方式處理。

如需詳細的失敗案例的詳細資訊，請參閱[的 Azure 應用程式失敗和災害復原](./disaster-recovery.md)。

## <a name="test-under-peak-loads"></a>在尖峰負載下測試

負載測試，請務必找出負載，例如後端資料庫遭灌爆，才會發生的錯誤或服務節流。 測試尖峰負載的預期的尖峰負載，使用的生產資料或綜合資料，最接近生產資料，盡可能增加。 若要查看應用程式在真實世界的情況下的運作方式，是您的目標。

## <a name="conduct-disaster-recovery-drills"></a>進行災害復原演練

開始使用理想的災害復原方案，並定期進行測試以確定它運作。

適用於 Azure 虛擬機器中，您可以使用[Azure Site Recovery](/azure/site-recovery/azure-to-azure-quickstart/)複寫，並執行[災害復原演練，又](/azure/site-recovery/azure-to-azure-tutorial-dr-drill/)而不會影響生產應用程式或進行中的複寫。

### <a name="failover-and-failback-testing"></a>容錯移轉和容錯回復測試

測試容錯移轉和容錯回復到驗證，您的應用程式相依的服務恢復運作已同步處理的方式在災害復原期間。 系統與作業的變更可能會影響容錯移轉和容錯回復的函式，但主要系統失敗或變得多載之前，可能無法偵測影響。 測試容錯移轉功能*之前*它們必須彌補即時問題。 也請務必依存的服務容錯移轉和容錯回復在正確的順序。

如果您使用[Azure Site Recovery](/azure/site-recovery/)來複寫 Vm，執行災害復原演練定期執行測試容錯移轉來驗證複寫策略。 測試容錯移轉不會影響進行中的 VM 複寫或生產環境。 如需詳細資訊，請參閱 <<c0> [ 執行至 Azure 的災害復原演練](/azure/site-recovery/site-recovery-test-failover-to-azure)。

### <a name="simulation-testing"></a>模擬測試

模擬測試包括建立小型的真實情況。 模擬在復原計劃中示範解決方案的效率，並反白顯示未適當地解決任何問題。

當您執行模擬測試，請依照下列最佳做法：

- 進行模擬，以不干擾實際業務，但覺得的真實情況的方式。
- 請確定完全控制模擬的案例。 如果復原計畫似乎失敗，您可以回到正常還原這種情況，而不會造成損毀的情況。
- 通知管理何時及如何將執行模擬演習。 您的計劃應詳述時間範圍和模擬期間受到影響的資源。

## <a name="test-health-probes-for-load-balancers-and-traffic-managers"></a>測試負載平衡器和流量管理員的健康情況探查

設定並測試您的負載平衡器和流量管理員的健康情況探查。 請確定您的健康情況端點檢查系統的重要部分，並適當地回應。

- 針對[Azure 流量管理員](/azure/traffic-manager/traffic-manager-overview/)，健康情況探查可決定是否要容錯移轉至另一個區域。 健康情況端點應該檢查關鍵性的相依性會部署在相同區域內。
- 針對[Azure Load Balancer](/azure/load-balancer/load-balancer-overview/)，健康情況探查可決定是否要從循環中移除 VM。 健康情況端點應該報告 VM 健康的情況。 不包含其他層或外部服務。 否則，在 VM 外部發生的失敗，可能會導致負載平衡器從循環中移除 VM。

如需在應用程式中實作健康情況監視的指引，請參閱[健康情況端點監視模式](../patterns/health-endpoint-monitoring.md)。

## <a name="test-monitoring-systems"></a>監視系統的測試

包含監視您的測試計劃中的系統。 自動容錯移轉和容錯回復的系統而定的監視和檢測的正確運作。 儀表板，以視覺化方式檢視系統健全狀況和操作員的警示也會視需要精確的監視和檢測。 如果這些項目失敗，遺漏重要資訊或回報不正確的資料，操作人員可能不知道系統是狀況不良還是失敗。

## <a name="include-third-party-services-in-test-scenarios"></a>包含測試案例中的第三方服務

如果您的應用程式會對第三方服務中的相依性，識別，以及如何這些服務可能會失敗並造成何種影響這些失敗，會對您的應用程式。 請注意服務等級協定 (SLA) 的第三方服務，並將效果可能會對您的災害復原計劃。

第三方服務可能會提供監視和診斷功能，所以一定要記錄您引動過程，其中，並將它們相互關聯與您的應用程式健全狀況和診斷記錄使用的唯一識別碼。 如需有關監視和診斷的已經實證做法的詳細資訊，請參閱[監視和診斷指引](../best-practices/monitoring.md)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [部署適用於復原能力和可用性](./deploy.md)

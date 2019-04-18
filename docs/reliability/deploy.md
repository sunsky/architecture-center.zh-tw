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
# <a name="deploying-azure-applications-for-resiliency-and-availability"></a>部署 Azure 應用程式的恢復功能和可用性

當您佈建，並更新 Azure 資源、 應用程式程式碼和組態設定，重複且可預測的程序將協助您避免錯誤和停機時間。 我們建議您可以視需要執行，並重新執行，如果發生失敗的部署自動化的程序。 您的部署程序執行個體之後，處理程序的文件可以讓它們保持如此。

## <a name="automate-processes"></a>自動化程序

若要啟用隨選資源、 快速部署解決方案、 減少人為錯誤，並產生一致且可重複的結果，請務必以自動化部署和更新。

### <a name="automate-as-many-processes-as-possible"></a>盡可能自動化許多程序

最可靠的部署程序自動化及*等冪*&mdash;也就是重複產生相同的結果。

- 若要自動化佈建 Azure 資源，您可以使用[Terraform](/azure/virtual-machines/windows/infrastructure-automation#terraform)， [Ansible](/azure/virtual-machines/windows/infrastructure-automation#ansible)， [Chef](/azure/virtual-machines/windows/infrastructure-automation#chef)， [Puppet](/azure/virtual-machines/windows/infrastructure-automation#puppet)， [AzurePowerShell](/powershell/azure/overview)， [Azure 命令列介面 (CLI)](/cli/azure)，或 [Azure Resource Manager 範本](/azure/azure-resource-manager/resource-group-overview#template-deployment)。
- 若要設定的 Vm，您可以使用 [為使用 cloud-init](/azure/virtual-machines/windows/infrastructure-automation#cloud-init) （適用於 Linux Vm) 或 [Azure 自動化狀態設定](/azure/automation/automation-dsc-overview)(DSC)。
- 若要自動化的應用程式部署，您可以使用 [Azure DevOps 服務](/azure/virtual-machines/windows/infrastructure-automation#azure-devops-services)， [Jenkins](/azure/virtual-machines/windows/infrastructure-automation#jenkins)，或其他 CI/CD 的解決方案。

最佳做法是建立快速存取，使用的參數說明和指令碼使用範例所述的分類的自動化指令碼的存放庫。 讓這份文件與您的 Azure 部署保持同步，並指定主要人員來管理存放庫。

自動化指令碼也可以啟用災害復原的隨選資源。

### <a name="use-code-to-provision-and-configure-infrastructure"></a>使用程式碼來佈建和設定基礎結構

這個練習中，呼叫*基礎結構即程式碼，* 可能會使用宣告式方法或命令式方法 （或兩者的組合）。

- [Resource Manager 範本](/azure/azure-resource-manager/resource-group-overview#template-deployment)是宣告式方法的範例。
- [PowerShell](/powershell/azure/overview)指令碼是命令式方法的範例。

### <a name="practice-immutable-infrastructure"></a>做法是不可變的基礎結構

換句話說，不會修改基礎結構部署到生產環境之後。 已套用臨機操作變更之後，您可能不知道到底什麼有所變更，因此它可能難以進行疑難排解系統。

### <a name="automate-and-test-deployment-and-maintenance-tasks"></a>自動化和測試部署和維護工作

分散式應用程式由多個必須相互合作的部分所組成。 部署應該利用經實證的機制，例如指令碼，可以更新和驗證設定和部署程序自動化。 測試所有的處理程序完全以確保錯誤不會造成額外的停機時間。

### <a name="implement-deployment-security-measures"></a>實作部署的安全性措施

所有的部署工具必須納入要保護部署的應用程式的安全性限制。 定義和強制執行部署原則時，仔細和人為介入的需求降到最低。

## <a name="deploy-to-multiple-regions-and-instances"></a>將部署到多個區域和執行個體

相依服務的單一執行個體的應用程式會建立單一失敗點。 若要改善復原能力和延展性，佈建多個執行個體。

- 對於 [Azure App Service](/azure/app-service/app-service-value-prop-what-is/)，請選擇可提供多個執行個體的 [App Service 方案](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview/)。
- 針對[Azure 雲端服務](/azure/cloud-services/cloud-services-choose-me)，設定每個角色來使用[多個執行個體](/azure/cloud-services/cloud-services-choose-me/#scaling-and-management)。
- 針對[Azure 虛擬機器](/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，請確定您的架構包含多個 VM，且每個 VM 會納入[可用性設定組](/azure/virtual-machines/virtual-machines-windows-manage-availability/)。

### <a name="consider-deploying-across-multiple-regions"></a>請考慮在多個區域部署

我們建議您部署所有但最不重要的應用程式與跨多個區域的應用程式服務。 如果您的應用程式部署到單一區域，這類罕見事件中的整個區域變成無法使用，應用程式也會無法使用。

如果您選擇要在單一區域部署，請考慮準備重新部署到次要區域，以非預期的失敗回應。

### <a name="redeploy-to-a-secondary-region"></a>重新部署到次要區域

如果您沒有複寫的單一、 主要區域中執行應用程式和資料庫，您的復原策略可能要重新部署到另一個區域。 此解決方案是經濟實惠，但最適合可容許較長的復原時間的非關鍵性應用程式。

如果您選擇此策略時，請盡量將重新部署程序自動化，並將重新部署案例包含在災害回應測試。

若要自動化您的重新部署程序，請考慮使用[Azure Site Recovery](/azure/site-recovery/)。

## <a name="use-staging-and-production-environments"></a>使用預備與生產環境

與充分利用預備與生產環境，您可以將更新推送至生產環境中，以高度控制的方式，並從非預期的部署問題的中斷最小化。

- [*藍綠部署*](https://martinfowler.com/bliki/BlueGreenDeployment.html) 牽涉到將更新部署到生產環境分開的即時應用程式。 驗證部署之後，將流量路由切換為更新的版本。 這是使用其中一種方式[預備位置](/azure/app-service/web-sites-staged-publishing)預備之前將它移至生產環境部署至 Azure App Service 中使用。
- [*Canary 版本*](https://martinfowler.com/bliki/CanaryRelease.html) 類似於藍綠部署。 而不是所有流量都切換到更新的應用程式，您可以將一小部分的流量路由到新的部署。 如果發生問題時，還原成舊的部署。 如果沒有，逐漸將更多的流量路由至新的版本。 如果您使用 Azure App Service，您可以使用在實際執行功能測試管理 canary 版本。

## <a name="create-a-rollback-plan-for-deployment-and-updates"></a>建立復原計劃部署和更新

如果部署失敗，可能無法使用您的應用程式。 若要減少停機時間，設計回復程序，回到最後已知的良好版本。 包含的變更回復到資料庫和應用程式所需的任何其他服務的策略。

如果您使用 Azure App Service，您可以設定最後一個已知良好的網站位置，並使用它來從 web 或 API 應用程式部署復原。

## <a name="log-and-audit-deployments"></a>記錄和稽核的部署

若要擷取做為盡可能的更特定的版本資訊，請實作強固的記錄策略。 如果您使用分段的部署技術，將會執行您的應用程式的多個版本，在生產環境中。 如果發生問題，判斷哪一個版本造成它。

## <a name="document-release-processes"></a>文件發行程序

沒有詳細的發行處理程序的文件，操作員可能會部署不正確的更新，或可能不正確地設定您的應用程式的設定。 明確定義和記載您的發行程序，並確保它可供整個營運小組使用。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [可靠性監視器應用程式健全狀況](./monitoring.md)

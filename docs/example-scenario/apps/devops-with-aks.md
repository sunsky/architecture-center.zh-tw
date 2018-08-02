---
title: 容器型工作負載的 CI/CD 管線
description: 經過證明的案例，可以為使用 Jenkins、Azure Container Registry、Azure Kubernetes Service、Cosmos DB 及 Grafana 的 Node.js Web 應用程式，建置 DevOps 管線。
author: iainfoulds
ms.date: 07/05/2018
ms.openlocfilehash: dceb4ad3c34ec43a54d802772f5817cacdd3929c
ms.sourcegitcommit: 8b5fc0d0d735793b87677610b747f54301dcb014
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2018
ms.locfileid: "39334210"
---
# <a name="cicd-pipeline-for-container-based-workloads"></a>容器型工作負載的 CI/CD 管線

此範例案例適用於想要藉由使用容器和 DevOps 工作流程讓應用程式開發現代化的企業。 在此案例中，Node.js Web 應用程式是由 Jenkins 建置，並且部署至 Azure Container Registry 和 Azure Kubernetes Service。 針對全域分散式資料庫層，使用 Azure Cosmos DB。 為了監視應用程式效能及進行疑難排解，Azure 監視器與 Grafana 執行個體和儀表板整合。

範例應用程式案例包括提供自動化開發環境、驗證新的程式碼認可，以及將新部署推送至預備或生產環境。 在過去，企業必須以手動方式建置及編譯應用程式和更新，然後維護大型、整合式程式碼基底。 透過使用持續整合 (CI) 和持續部署 (CD) 的應用程式開發新式方法，您可以更快速地建置、測試及部署服務。 這個新式方法可讓您更快地將應用程式和更新發行給客戶，並且以更靈活的方式回應日新月異的商業需求。

藉由使用 Azure 服務 (例如 Azure Kubernetes Service、Container Registry 和 Cosmos DB)，公司可以使用最新的應用程式開發技術和工具，來簡化實作高可用性的程序。

## <a name="related-use-cases"></a>相關使用案例

請針對下列使用案例考慮此案例：

* 將應用程式開發做法現代化為微服務、容器型方法。
* 加速應用程式開發和部署生命週期。
* 自動化測試部署或進行驗證的接受環境。

## <a name="architecture"></a>架構

![DevOps 案例 (使用 Jenkins、Azure Container Registry 及 Azure Kubernetes Service) 中所牽涉到 Azure 元件的架構概觀][architecture]

此案例涵蓋 Node.js Web 應用程式和資料庫後端的 DevOps 管線。 整個案例的資料流程如下所示：

1. 開發人員會對 Node.js Web 應用程式原始程式碼進行變更。
2. 程式碼變更會認可至原始檔控制存放庫，例如 GitHub。
3. 為了開始持續整合 (CI) 程序，GitHub Webhook 會觸發 Jenkins 專案建置。
4. Jenkins 建置作業會使用 Azure Kubernetes Service 中的動態建置代理程式，來執行容器建置程序。
5. 容器映像會根據原始檔控制中的程式碼建立，接著推送至 Azure Container Registry。
6. 透過持續部署 (CD)，Jenkins 會將此更新的容器映像部署到 Kubernetes 叢集。
7. Node.js Web 應用程式會使用 Azure Cosmos DB 作為它的後端。 Cosmos DB 和 Azure Kubernetes Service 都會向 Azure 監視器報告計量。
8. Grafana 執行個體根據 Azure 監視器的資料，提供應用程式效能的視覺效果儀表板。

### <a name="components"></a>元件

* [Jenkins][jenkins] 是開放原始碼 Automation 伺服程式，可與 Azure 服務整合，以進行持續整合 (CI) 及持續部署 (CD)。 在此案例中，Jenkins 會根據對於原始檔控制的認可，協調新容器映像的建立，將這些映像推送至 Azure Container Registry，然後在 Azure Kubernetes Service 中更新應用程式執行個體。
* [Azure Linux 虛擬機器][azurevm-docs]是用來執行 Jenkins 和 Grafana 執行個體。
* [Azure Container Registry][azureacr-docs] 會儲存及管理 Azure Kubernetes Service 叢集所使用的容器映像。 映像會安全地儲存，並且可以由 Azure 平台複寫到其他區域來加快部署速度。
* [Azure Kubernetes Service][azureaks-docs] 是受控 Kubernetes 平台，讓您不需要具備容器協調流程專業知識，也可以部署及管理容器化應用程式。 以主控的 Kubernetes 服務形式，Azure 會為您處理像是健康狀態監視和維護等重要工作。
* [Azure Cosmos DB] [azurecosmosdb-docs] 是全域分散式、多模型資料庫，可讓您從各種不同的資料庫和一致性模型中，選擇符合您需求的項目。 使用 Cosmos DB，您的資料可以全域複寫，不需要部署及設定叢集管理或複寫元件。
* [Azure 監視器][azuremonitor-docs]有助於追蹤效能、維護安全性及找出趨勢。 監視器所取得的計量可以由其他資源和工具使用，例如 Grafana。
* [Grafana][grafana] 是開放原始碼解決方案，可以查詢、視覺化、警示及了解計量。 Azure 監視器的資料來源外掛程式可讓 Grafana 建立視覺效果儀表板，來監視在 Azure Kubernetes Service 中執行且使用 Cosmos DB 的應用程式效能。

### <a name="alternatives"></a>替代項目

* [Visual Studio Team Services][vsts] 和 Team Foundation Server 會協助您實作任何應用程式的持續整合 (CI)、測試及部署 (CD) 管線。
* 如果您想要更多叢集控制權，[Kubernetes][kubernetes] 可以直接在 Azure 虛擬機器上執行，不用透過受控服務。
* [Service Fabric][service-fabric] 是另一個可以取代 AKS 的替代容器協調器。

## <a name="considerations"></a>考量

### <a name="availability"></a>可用性

為了監視您的應用程式效能並且報告問題，此案例將 Azure 監視器與 Grafana 合併到視覺效果儀表板。 這些工具可讓您監視可能需要程式碼更新 (後續可以使用 CI/CD 管線部署) 的效能問題，並且進行疑難排解。

負載平衡器是 Azure Kubernetes Service 叢集的一部分，它會將應用程式流量散佈到執行應用程式的一或多個容器 (Pod)。 在 Kubernetes 中執行容器化應用程式的這個方法，會為您的客戶提供高可用性基礎結構。

如需其他可用性主題，請參閱架構中心的[可用性檢查清單][availability]。

### <a name="scalability"></a>延展性

Azure Kubernetes Service 可讓您調整叢集節點數目，以符合您的應用程式需求。 當您的應用程式增加時，您可以相應放大執行服務的 Kubernetes 節點數目。

應用程式資料會儲存在 Azure Cosmos DB，這是可以全域調整的全域分散式、多模型資料庫。 Cosmos DB 會將調整基礎結構的需求抽象化，如同使用傳統資料庫元件一般，您可以選擇全域複寫您的 Cosmos DB 以符合客戶需求。

如需其他延展性主題，請參閱架構中心的[延展性檢查清單][scalability]。

### <a name="security"></a>安全性

為了將攻擊量降至最低，此案例不會透過 HTTP 公開 Jenkins 虛擬機器執行個體。 針對需要您與 Jenkins 互動的任何管理工作，您使用 SSH 通道從本機機器建立安全的遠端連線。 Jenkins 和 Grafana 虛擬機器執行個體僅允許 SSH 公開金鑰驗證。 已停用密碼型登入。 如需詳細資訊，請參閱[在 Azure 上執行 Jenkins 伺服器](../../reference-architectures/jenkins/index.md)。

針對區隔認證和權限，此案例會使用專用的 Azure Active Directory (AD) 服務主體。 此服務主體的認證會在 Jenkins 中儲存為安全認證物件，因此它們不會直接公開，在指令碼或建置管線內也看不見。

如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。

### <a name="resiliency"></a>災害復原

此案例會針對您的應用程式使用 Azure Kubernetes Service。 復原元件內建於 Kubernetes，這些元件會監視及重新啟動有問題的容器 (Pod)。 您的應用程式與多個執行中 Kubernetes 節點合併，可以容許 Pod 或節點無法使用。

如需設計彈性解決方案的一般指引，請參閱[為 Azure 設計有彈性的應用程式][resiliency]。

## <a name="deploy-the-scenario"></a>部署案例

**必要條件。**

* 您必須具有現有的 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。
* 您需要 SSH 公開金鑰組。 如需如何建立公開金鑰組的步驟，請參閱[為 Linux 虛擬機器建立和使用 SSH 金鑰組][sshkeydocs]。
* 您需要 Azure Active Directory (AD) 服務主體以便驗證服務和資源。 您可以視需要使用 [az ad sp create-for-rbac][createsp] 來建立服務主體

    ```azurecli-interactive
    az ad sp create-for-rbac --name myDevOpsScenario
    ```

    請記下此命令輸出中的 appId 和 password。 當您部署案例時，將這些值提供給範本。

若要使用 Azure Resource Manager 範本部署此案例，請執行下列步驟。

1. 按一下 [部署至 Azure] 按鈕：<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fapps%2Fdevops-with-aks%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. 等待 Azure 入口網站中的範本部署開啟，然後完成下列步驟：
   * 選擇 [新建] 資源群組，然後在文字方塊中提供名稱，例如 myAKSDevOpsScenario。
   * 從 [位置] 下拉式方塊選取區域。
   * 輸入來自 `az ad sp create-for-rbac` 命令的服務主體應用程式識別碼和密碼。
   * 提供 Jenkins 執行個體和 Grafana 主控台的使用者名稱和安全的密碼。
   * 提供 SSH 金鑰以保護 Linux 虛擬機器的登入。
   * 檢閱條款及條件，然後按一下 [我同意上方所述的條款及條件]。
   * 選取 [購買] 按鈕。

需要 15-20 分鐘的時間才能完成部署。

## <a name="pricing"></a>價格

為了探索執行此案例的成本，所有服務會在成本計算機中預先設定。 若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的流量。

我們根據要儲存的容器映像數目和執行應用程式的 Kubernetes 節點數目，提供了 3 個範例成本設定檔。

* [小型][small-pricing]：這個設定檔適用於每月 1000 個容器建置。
* [中型][medium-pricing]：這個設定檔適用於每月 100000 個容器建置。
* [大型][large-pricing]：這個設定檔適用於每月 1000000 個容器建置。

## <a name="related-resources"></a>相關資源

此案例使用 Azure Container Registry 和 Azure Kubernetes Service 來儲存及執行容器型應用程式。 Azure 容器執行個體也可以用來執行容器型應用程式，不需要佈建任何協調流程元件。 如需詳細資訊，請參閱 [Azure 容器執行個體概觀][azureaci-docs]。

<!-- links -->
[architecture]: ./media/devops-with-aks/architecture-devops-with-aks.png
[autoscaling]: ../../best-practices/auto-scaling.md
[availability]: ../../checklist/availability.md
[azureaci-docs]: /azure/container-instances/container-instances-overview
[azureacr-docs]: /azure/container-registry/container-registry-intro
[azurecosmosdb-docs]: /azure/cosmos-db/introduction
[azureaks-docs]: /azure/aks/intro-kubernetes
[azuremonitor-docs]: /azure/monitoring-and-diagnostics/monitoring-overview
[azurevm-docs]: /azure/virtual-machines/linux/overview
[createsp]: /cli/azure/ad/sp#az-ad-sp-create
[grafana]: https://grafana.com/
[jenkins]: https://jenkins.io/
[resiliency]: ../../resiliency/index.md
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[security]: /azure/security/
[scalability]: ../../checklist/scalability.md
[sshkeydocs]: /azure/virtual-machines/linux/mac-create-ssh-keys
[vsts]: /vsts/?view=vsts
[kubernetes]: https://kubernetes.io/
[service-fabric]: /azure/service-fabric/

[small-pricing]: https://azure.com/e/841f0a75b1ea4802ba1ac8f7918a71e7
[medium-pricing]: https://azure.com/e/eea0e6d79b4e45618a96d33383ec77ba
[large-pricing]: https://azure.com/e/3faab662c54c473da55a1e93a27e0e64

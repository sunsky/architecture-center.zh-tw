---
title: Python 模型的即時評分
titleSuffix: Azure Reference Architectures
description: 此參考架構會示範如何在 Azure 上將 Python 模型部署為 Web 服務，以進行即時預測。
author: njray
ms.date: 11/09/2018
ms.custom: azcat-ai
ms.openlocfilehash: e2312d1d1d2444f9915f4e6aa067c1487e096d3e
ms.sourcegitcommit: 88a68c7e9b6b772172b7faa4b9fd9c061a9f7e9d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53120351"
---
# <a name="real-time-scoring-of-python-scikit-learn-and-deep-learning-models-on-azure"></a>Azure 上的 Python Scikit-Learn 和深度學習模型的即時評分

此參考架構會示範如何將 Python 模型部署為 Web 服務，以進行即時預測。 涵蓋兩個案例：部署一般的 Python 模型，以及部署深度學習模型的特定需求。 兩個案例皆使用顯示的架構。

此架構的兩個參考實作可在 GitHub 上取得，一個用於[一般 Python 模型][github-python]，另一個用於[深度學習模型][github-dl]。

![Azure 上 Python 模型的即時評分架構圖](./_images/python-model-architecture.png)

## <a name="scenarios"></a>案例

參考實作會使用此架構示範兩個案例。

**案例 1：常見問題集比對**。 此案例會示範如何將常見問題 (常見問題集) 比對模型部署為 Web 服務，以提供使用者問題的預測。 此案例中，架構圖中的「輸入資料」是指包含使用者問題的文字字串，用來與常見問題集清單進行比對。 此案例設計用於 Python 的 [scikit-learn][scikit] 機器學習程式庫，但可以一般化為使用 Python 模型進行即時預測的任何案例。

此案例使用 Stack Overflow 問題資料的子集，其中包括標記為 JavaScript 的原始問題、其重複的問題，以及答案。 它會訓練 scikit-learn 管線，以預測重複問題與每個原始問題的比對符合機率。 這些預測使用 REST API 端點即時進行。

此架構的應用程式流程如下所示：

1. 用戶端會傳送 HTTP POST 要求與編碼的問題資料。

2. Flask 應用程式會擷取要求中的問題。

3. 問題會傳送至 scikit-learn 管線模型，以進行特徵化和評分。

4. 比對符合的常見問題及其評分會輸送到 JSON 物件，並傳回給用戶端。

以下是取用結果的範例應用程式螢幕擷取畫面：

![應用程式範例的螢幕擷取畫面](./_images/python-faq-matches.png)

**案例 2：影像分類**。 此案例會示範如何將卷積神經網路 (CNN) 模型部署為 Web 服務，以提供影像上的預測。 此案例中，架構圖中的「輸入資料」是指影像檔案。 CNN 可有效處理電腦視覺等工作，例如影像分類和物件偵測。 此案例設計用於 TensorFlow、Keras (具有 TensorFlow 後端)，以及 PyTorch 等架構。 不過，它可以一般化為使用深度學習模型進行即時預測的任何案例。

此案例使用在 ImageNet-1K (1,000 個類別) 資料集上定型的 ResNet-152 預先定型模型，來預測影像所屬類別 (請參閱下圖)。 這些預測使用 REST API 端點即時進行。

![預測的範例](./_images/python-example-predictions.png)

深度學習模型的應用程式流程如下所示：

1. 用戶端會傳送 HTTP POST 要求與編碼的影像資料。

2. Flask 應用程式會擷取要求中的影像。

3. 影像已前置處理並傳送至模型進行評分。

4. 評分結果會輸送到 JSON 物件，並傳回給用戶端。

## <a name="architecture"></a>架構

此架構由下列元件組成。

**[虛擬機器][vm]** (VM)。 虛擬機器會顯示為可傳送 HTTP 要求的裝置 (&mdash;本機或雲端&mdash;) 範例。

**[Azure Kubernetes Service][aks]** (AKS) 用於在 Kubernetes 叢集上部署應用程式。 AKS 可簡化 Kubernetes 的部署與作業。 可以針對一般 Python 模型使用僅限 CPU 的虛擬機器，或針對深度學習模型使用已啟用 GPU 的虛擬機器來設定叢集。

**[負載平衡器][lb]**。 由 AKS 佈建的負載平衡器是用於向外部公開服務。 負載平衡器的流量會導向至後端 Pod。

**[Docker 中樞][docker]** 用來儲存部署在 Kubernetes 叢集上的 Docker 映像。 此架構已選擇使用 Docker 中樞，因為它容易使用，而且是 Docker 使用者的預設映像存放庫。 [Azure Container Registry][acr] 也可用於此架構。

## <a name="performance-considerations"></a>效能考量

對於即時評分架構，輸送量效能會成為主要的考量。 對於一般的 Python 模型，普遍認為 CPU 足以處理工作負載。

不過，對於深度學習工作負載，當速度造成瓶頸時，相較於 CPU，GPU 通常可提供更佳的[效能][gpus-vs-cpus]。 若要使用 CPU 達到 GPU 效能，通常需要具有大量 CPU 的叢集。

您可以在任何情況下針對此架構使用 CPU，但針對深度學習模型，相較於成本相近的 CPU 叢集，GPU 明顯可提供更高的輸送量值。 AKS 可支援使用 GPU，這是將 AKS 用於此架構的優點之一。 此外，深度學習部署通常會使用具有大量參數的模型。 使用 GPU 可避免模型與 Web 服務之間的資源競爭，這對於僅限 CPU 的部署是一大問題。

## <a name="scalability-considerations"></a>延展性考量

對於一般 Python 模型，其使用僅限 CPU 的虛擬機器佈建 AKS 叢集，在[相應放大 Pod 數目][manually-scale-pods]時需多加注意。 目標是要充分利用叢集。 縮放取決於針對 Pod 定義的 CPU 要求和限制。 Kubernetes 也支援 Pod 的[自動調整][autoscale-pods]，可根據 CPU 使用率或其他選取的計量來調整部署中的 Pod 數目。 [叢集自動調整程式][autoscaler] (預覽) 可根據擱置中的 Pod 來縮放代理程式節點。

對於深度學習案例，其使用已啟用 GPU 的虛擬機器，Pod 上的資源限制為一個 GPU 需指派給一個 Pod。 根據使用的虛擬機器類型，您必須[調整叢集的節點][scale-cluster]以滿足服務需求。 您可以使用 Azure CLI 和 kubectl 來輕鬆完成。

## <a name="monitoring-and-logging-considerations"></a>監視和記錄的考量

### <a name="aks-monitoring"></a>AKS 監視

如需 AKS 效能的可見性，請使用[適用於容器的 Azure 監視器][monitor-containers]功能。 它可透過計量 API 從 Kubernetes 中提供的控制器、節點與容器收集記憶體與處理器計量。

在部署您的應用程式時，監視 AKS 叢集，確定叢集正常運作、所有節點功能正常，且所有 Pod 都在執行。 雖然您可以使用 [kubectl][kubectl] 命令列工具來擷取 Pod 狀態，Kubernetes 也包含 Web 儀表板，可對叢集狀態和管理進行基本監視。

![Kubernetes 儀表板的螢幕擷取畫面](./_images/python-kubernetes-dashboard.png)

若要查看叢集和節點的整體狀態，請前往 Kubernetes 儀表板的**節點**區段。 如果節點處於非使用中或已失敗，您可以從該頁面顯示錯誤記錄檔。 同樣地，請前往 **Pod** 和**部署**區段，以取得 Pod 數目和部署狀態的相關資訊。

### <a name="aks-logs"></a>AKS 記錄

AKS 會自動將所有 stdout/stderr 記錄到叢集中的 Pod 記錄檔。 使用 kubectl 來查看，以及節點層級事件和記錄。 如需詳細資料，請參閱部署步驟。

透過適用於 Linux 的 Log Analytics 代理程式容器化版本，使用[適用於容器的 Azure 監視器][monitor-containers]來收集計量和記錄，該代理程式會儲存在您的 Log Analytics 工作區。

## <a name="security-considerations"></a>安全性考量

使用 [Azure 資訊安全中心][security-center]來集中檢視 Azure 資源的安全性狀態。 資訊安全中心會監視潛在的安全性問題，並提供全面性的部署安全性健康情況，儘管它不會監視 AKS 代理程式節點。 資訊安全中心是依每個 Azure 訂用帳戶設定。 啟用安全性資料收集，如[將 Azure 訂用帳戶上架到資訊安全中心的標準層][get-started]中所述。 啟用資料收集時，資訊安全性中心就會自動掃描任何該訂用帳戶建立的 VM。

**作業**。 若要使用 Azure Active Directory (Azure AD) 驗證權杖登入 AKS 叢集，請設定 AKS 以將 Azure AD 用於[使用者驗證][aad-auth]。 叢集系統管理員也能夠根據使用者身分識別或目錄群組成員資格，設定 Kubernetes 角色型存取控制 (RBAC)。

使用 [RBAC][rbac] 來控制您所部署之 Azure 資源的存取權。 RBAC 可讓您指派授權角色給您 DevOps 小組的成員。 使用者可以指派多個角色，且您可以針對更詳細的[權限]建立自訂角色。

**HTTPS**。 作為最佳安全性做法，應用程式應強制執行 HTTPS 並重新導向 HTTP 要求。 使用[輸入控制器][ingress-controller]以部署可終止 SSL 並重新導向 HTTP 要求的反向 Proxy。 如需詳細資訊，請參閱[在 Azure Kubernetes Service (AKS) 上建立 HTTPS 輸入控制器][https-ingress]。

**驗證**。 此解決方案並不會限制端點的存取。 若要在企業設定中部署架構，需透過 API 金鑰保護端點，並將某種形式的使用者驗證新增至用戶端應用程式。

**容器登錄**。 此解決方案會使用公用登錄來儲存 Docker 映像。 應用程式相依的程式碼以及模型都包含在此映像中。 企業應用程式應使用私人登錄，有助於防止惡意程式碼執行，並協助將資訊保存在容器內而不會遭到入侵。

**DDoS 保護**。 考慮啟用 [DDoS 保護標準][ddos]。 雖然基本 DDoS 保護會隨著 Azure 平台而啟用，但 Azure DDoS 保護標準提供了專門針對 Azure 虛擬網路資源進行調整的安全防護功能。

**記錄**。 在儲存記錄資料前請使用最佳做法，例如清除使用者密碼以及其他可能用來認可安全性詐騙的資訊。

## <a name="deployment"></a>部署

若要部署此參考架構，請遵循 GitHub 存放庫中所述步驟：

- [一般的 Python 模型][github-python]
- [深度學習模型][github-dl]

<!-- links -->

[aad-auth]: /azure/aks/aad-integration
[acr]: /azure/container-registry/
[something]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/
[aks]: /azure/aks/intro-kubernetes
[autoscaler]: /azure/aks/autoscaler
[autoscale-pods]: /azure/aks/tutorial-kubernetes-scale#autoscale-pods
[azcopy]: /azure/storage/common/storage-use-azcopy-linux
[ddos]: /azure/virtual-network/ddos-protection-overview
[docker]: https://hub.docker.com/
[get-started]: /azure/security-center/security-center-get-started
[github-python]: https://github.com/Azure/MLAKSDeployment
[github-dl]: https://github.com/Microsoft/AKSDeploymentTutorial
[gpus-vs-cpus]: https://azure.microsoft.com/en-us/blog/gpus-vs-cpus-for-deployment-of-deep-learning-models/
[https-ingress]: /azure/aks/ingress-tls
[ingress-controller]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[kubectl]: https://kubernetes.io/docs/tasks/tools/install-kubectl/
[lb]: /azure/load-balancer/load-balancer-overview
[manually-scale-pods]: /azure/aks/tutorial-kubernetes-scale#manually-scale-pods
[monitor-containers]: /azure/monitoring/monitoring-container-insights-overview
[權限]: /azure/aks/concepts-identity
[rbac]: /azure/active-directory/role-based-access-control-what-is
[scale-cluster]: /azure/aks/scale-cluster
[scikit]: https://pypi.org/project/scikit-learn/
[security-center]: /azure/security-center/security-center-intro
[vm]: /azure/virtual-machines/

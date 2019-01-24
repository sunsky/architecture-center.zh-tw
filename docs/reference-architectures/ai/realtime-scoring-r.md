---
title: R 機器學習模型的即時評分
description: 使用在 Azure Kubernetes Service (AKS) 中執行的 Machine Learning Server 在 R 中實作即時預測服務。
author: njray
ms.date: 12/12/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai
ms.openlocfilehash: 00bea3cae0c3d2f0fea2babd7b0157382cf9890a
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54487163"
---
# <a name="real-time-scoring-of-r-machine-learning-models"></a>R 機器學習模型的即時評分

本參考架構說明如何使用在 Azure Kubernetes Service (AKS) 中執行的 Microsoft Machine Learning Server 在 R 中實作即時 (同步) 預測服務。 此架構採通用設計，適用於任何您想要部署為即時服務、且內建於 R 中的預測性模型。 **[部署這個解決方案][github]**。

## <a name="architecture"></a>架構

![Azure 上的 R 機器學習模型的即時評分][0]

此參考架構採用以容器為基礎的方法。 Docker 映像的建置包含 R，以及對新資料進行評分所需的各種成品。 其中包括模型物件本身和評分指令碼。 此映像會推送至裝載在 Azure 中的 Docker 登錄，然後部署至 Kubernetes 叢集，也位於 Azure 中。

此工作流程的架構包含下列元件。

- **[Azure Container Registry][acr]** 用來儲存此工作流程的映像。 使用 Container 登錄建立的登錄可透過標準 [Docker Registry V2 API][docker] 和用戶端來管理。

- **[Azure Kubernetes Service][aks]** 用來裝載部署和服務。 使用 AKS 建立的叢集可使用標準 [Kubernetes API][k-api] 和用戶端 (kubectl) 來管理。

- **[Microsoft Machine Learning Server][mmls]** 用來定義服務的 REST API，並包含[模型運算化][operationalization]。 此服務導向的 Web 伺服器程序會接聽要求，然後將其交由其他執行實際 R 程式碼的背景程序處理，以產生結果。 在此組態中，前述所有程序都會在單一節點上執行，並且包裝在容器中。 如需在開發或測試環境以外使用這項服務的詳細資訊，請連絡您的 Microsoft 代表。

## <a name="performance-considerations"></a>效能考量

機器學習工作負載通常需要大量計算，在進行新資料的定型和評分時都是如此。 根據經驗法則，請不要讓每個核心執行多個評分程序。 Machine Learning Server 可讓您定義在每個容器中執行的 R 程序數目。 預設值為五個程序。 在建立相對較簡單的模型時 (例如，使用少量變數的線性迴歸，或小型決策樹) 時，您可以增加程序的數目。 請監視叢集節點的 CPU 負載，以判斷容器數目的適當限制。

已啟用 GPU 功能的叢集可以加快某些類型的工作負載，特別是深度學習模型。 並非所有工作負載都可使用 GPU &mdash; 只有大量使用矩陣代數的工作負載才適用。 例如，以樹狀結構為基礎的模型 (包括隨機樹系和提升模型) 通常無法獲益於 GPU。

某些模型類型 (例如隨機樹系) 可在 CPU 上大規模平行執行。 在這種情況下，請將工作負載分散到多個核心，以加快單一要求的評分。 但若這麼做，您對固定叢集大小處理多個評分要求的容量將會因此縮減。

一般情況下，開放原始碼 R 模型會將其所有資料儲存在記憶體中，因此請確定您的節點有足夠的記憶體可容納您要同時執行的程序。 如果您使用 Machine Learning Server 來配適您的模型，請使用可在磁碟上處理資料的程式庫，而不要將資料全部讀取到記憶體中。 這有助於大幅降低記憶體需求。 不論您是使用 Machine Learning Server 還是開放原始碼 R，均請監視您的節點，以確定您的評分程序不會耗盡記憶體。

## <a name="security-considerations"></a>安全性考量

### <a name="network-encryption"></a>網路加密

在此參考架構中，會對與叢集的通訊啟用 HTTPS，並且使用來自 [Let 's Encrypt][encrypt] 的暫存憑證。 基於生產用途，請替換成適當的簽章授權單位為您提供的個人憑證。

### <a name="authentication-and-authorization"></a>驗證和授權

Machine Learning Server [模型運算化][operationalization]需要驗證評分要求。 在此部署中，會使用使用者名稱和密碼。 在企業設定中，您可以使用 [Azure Active Directory][AAD] 來啟用驗證，或使用 [Azure API 管理][API]建立個別的後端。

若要讓模型運算化與容器上的 Machine Learning Server 順利搭配運作，您必須安裝 JSON Web 權杖 (JWT) 憑證。 此部署會使用 Microsoft 所提供的憑證。 在生產環境設定中，請提供您自己的憑證。

對於容器登錄與 AKS 之間的流量，請考慮啟用[角色型存取控制][rbac] (RBAC) 將存取權限僅限定於有需要的對象。

### <a name="separate-storage"></a>區隔儲存體

此參考架構會將應用程式 (R) 和資料 (模型物件和評分指令碼) 組合成單一映像。 在某些情況下，加以區隔可能會有好處。 您可以將模型資料和程式碼放入 Azure Blob 或檔案[儲存體][storage]中，並在容器初始化時加以擷取。 在此情況下，請確定儲存體帳戶設定為僅允許已驗證的存取，而且需要 HTTPS。

## <a name="monitoring-and-logging-considerations"></a>監視和記錄的考量

使用 [Kubernetes 儀表板][dashboard]監視 AKS 叢集的整體狀態。 如需詳細資訊，請在 Azure 入口網站中參閱叢集的概觀刀鋒視窗。 [GitHub][github] 資源也會說明如何從 R 啟動儀表板。

雖然儀表板可讓您檢視叢集的整體健康情況，但追蹤個別容器的狀態也很重要。 若要這麼做，請從 Azure 入口網站中的叢集概觀刀鋒視窗啟用 [Azure 監視器深入解析][monitor]，或參閱[適用於容器的 Azure 監視器][monitor-containers] (處於預覽狀態)。

## <a name="cost-considerations"></a>成本考量

Machine Learning Server 是根據個別核心進行授權的，叢集中所有執行 Machine Learning Server 的核心都會計入此數中。 如果您是企業 Machine Learning Server 或 Microsoft SQL Server 的客戶，請連絡您的 Microsoft 代表以取得定價詳細資料。

Machine Learning Server 的開放原始碼替代方案是 [Plumber][plumber]，這是一個可將您的程式碼轉換為 REST API 的 R 套件。 Plumber 的功能不像 Machine Learning Server 那麼完整。 例如，根據預設，它不含任何提供要求驗證的功能。 如果您使用 Plumber，建議您啟用 [Azure API 管理][API]來處理驗證詳細資料。

除了授權以外，主要的成本考量還有 Kubernetes 叢集的計算資源。 叢集必須夠大而足以處理尖峰時間的預期要求量，但這種方法會使資源在其他時間處於閒置狀態。 若要限制閒置資源的影響，請透過 kubectl 工具啟用[水平自動調整程式][autoscaler]來處理叢集。 或者，請使用[叢集自動調整程式][cluster-autoscaler]。

## <a name="deploy-the-solution"></a>部署解決方案

此架構的參考實作可在 [GitHub][github] 上取得。 請依照此處說明的步驟，部署簡單的預測模型作為服務。

<!-- links -->
[AAD]: /azure/active-directory/fundamentals/active-directory-whatis
[API]: /azure/api-management/api-management-key-concepts
[ACR]: /azure/container-registry/container-registry-intro
[AKS]: /azure/aks/intro-kubernetes
[autoscaler]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[cluster-autoscaler]: /azure/aks/autoscaler
[monitor]: /azure/monitoring/monitoring-container-insights-overview
[dashboard]: /azure/aks/kubernetes-dashboard
[docker]: https://docs.docker.com/registry/spec/api/
[encrypt]: https://letsencrypt.org/
[gitHub]: https://github.com/Azure/RealtimeRDeployment
[K-API]: https://kubernetes.io/docs/reference/
[MMLS]: /machine-learning-server/what-is-machine-learning-server
[monitor-containers]: /azure/azure-monitor/insights/container-insights-overview
[operationalization]: /machine-learning-server/what-is-operationalization
[plumber]: https://www.rplumber.io
[RBAC]: /azure/role-based-access-control/overview
[storage]: /azure/storage/common/storage-introduction
[0]: ./_images/realtime-scoring-r.png

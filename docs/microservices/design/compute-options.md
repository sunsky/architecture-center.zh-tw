# <a name="choosing-a-compute-option-for-microservices"></a>選擇計算選項適用於微服務

*計算*一詞是指您的應用程式執行所在運算資源的裝載模型。 應用在微服務架構方面，有兩種非常熱門的方法：

- 服務協調器，可管理專用節點 (VM) 上所執行的服務。
- 使用「函式即服務」(FaaS) 的無伺服器架構。

雖然這兩種方法並不是唯一的選項，卻是已獲證實可行的微服務建置方法。 應用程式可同時包含這兩種方法。

## <a name="service-orchestrators"></a>服務協調器

協調器負責處理與一組服務的部署及管理有關的工作。 這些工作包括在節點上放置服務、監視服務的健康情況、重新啟動狀況不良的服務、在服務執行個體間負載平衡網路流量、服務探索、調整服務執行個體數目，以及套用組態更新。 熱門協調器包括 Kubernetes、Service Fabric、DC/OS 和 Docker Swarm。

在 Azure 平台上，請考慮下列選項：

- [Azure Kubernetes Service](/azure/aks/) (AKS) 是受控 Kubernetes 服務。 AKS 會佈建 Kubernetes 並公開 Kubernetes API 端點，但會裝載和管理 Kubernetes 控制平面，以便執行自動升級、自動修補、自動調整和其他管理工作。 您可以將 AKS 視為「Kubernetes API 即服務」。

- [Service Fabric](/azure/service-fabric/) 是一個分散式系統平台，可讓您封裝、部署及管理微服務。 您可以將微服務部署至 Service Fabric，以作為容器、二進位可執行檔或作為 [Reliable Services](/azure/service-fabric/service-fabric-reliable-services-introduction)。 使用 Reliable Services 程式設計模型，服務便可直接使用 Service Fabric 程式設計 API 來查詢系統、回報健康情況、接收有關組態和程式碼變更的通知，並探索其他服務。 與 Service Fabric 的主要差異在於，其非常著重在使用 [Reliable Collections](/azure/service-fabric/service-fabric-reliable-services-reliable-collections) 來建置具狀態服務。

- 其他選項，例如 Docker Enterprise Edition 和 Mesosphere DC/OS 可以在 Azure 上執行 IaaS 環境中。 您可以找到部署範本[Azure Marketplace](https://azuremarketplace.microsoft.com)。

## <a name="containers"></a>容器

有時候人們會將容器和微服務看作是一樣的東西。 雖然事實並非如此 (不需要容器就能建置微服務)，但容器的確具有和微服務極其相關的一些優點，例如：

- **可攜性**。 容器映像是不需要安裝程式庫或其他相依性就能執行的獨立套件。 這項特性使其部署作業變得簡單。 容器可以快速加以啟動和停止，因此您可以啟動新的執行個體，以處理更多負載或是從節點失敗中復原。

- **密度**. 相較於執行虛擬機器，容器因為會共用作業系統資源，因此所需資源不多。 這項特性讓我們可以將多個容器封裝至單一節點，而這個能力在應用程式包含許多小型服務時會特別有用。

- **資源隔離**. 您可以限制容器可以使用的記憶體和 CPU 數量，這可協助確保失控程序不會耗盡主機資源。 如需詳細資訊，請參閱[隔艙模式](../../patterns/bulkhead.md)。

## <a name="serverless-functions-as-a-service"></a>無伺服器 (函式即服務)

使用[無伺服器](https://azure.microsoft.com/solutions/serverless/)架構時，您不需管理 VM 或虛擬網路基礎結構。 相反地，您會部署程式碼，而主機服務則負責將該程式碼放到 VM 並加以執行。 這種方法往往會偏好使用以事件型觸發程序來進行協調的小型函式。 例如，放到佇列的訊息可能會觸發函式，以讀取佇列並處理訊息。

[Azure Functions](/azure/azure-functions/)是無伺服器計算服務，可支援各種函式觸發程序，包括 HTTP 要求、 服務匯流排佇列和事件中樞事件。 如需完整清單，請參閱 < [Azure Functions 觸發程序和繫結概念](/azure/azure-functions/functions-triggers-bindings)。 也請考慮[Azure Event Grid](/azure/event-grid/)，這是在 Azure 中的受控的事件路由服務。

<!-- markdownlint-disable MD026 -->

## <a name="orchestrator-or-serverless"></a>該選擇協調器還是無伺服器？

<!-- markdownlint-enable MD026 -->

以下是在協調器方法與無伺服器方法之間做選擇時所要考慮的部分因素。

**管理性**。無伺服器應用程式管理容易，因為該平台會為您管理所有的計算資源。 雖然協調器會抽走某些叢集管理和設定工作，但它不會完全掩蓋掉基礎 VM。 使用協調器時，您必須考慮到負載平衡、CPU、記憶體使用量和網路等問題。

**彈性和控制**。 協調器可讓您極大程度地控制服務和叢集的設定及管理功能。 代價是複雜性會提高。 若使用無伺服器架構，您則要放棄某種程度的控制能力，因為這些詳細資料會被抽離。

**可攜性**。 此處所列的所有協調器 (Kubernetes、DC/OS、Docker Swarm 和 Service Fabric) 可在內部部署環境執行，也可以在多個公用雲端中執行。

**應用程式整合**. 使用無伺服器架構來建置複雜的應用程式並不是件容易的事。 在 Azure 中進行此作業的其中一個方法是使用 [Azure Logic Apps](/azure/logic-apps/) 來協調一組 Azure Functions。 如需此方法的範例，請參閱[建立與 Azure Logic Apps 整合的函式](/azure/azure-functions/functions-twitter-email)。

**成本**。 使用協調器時，您需要為叢集中正在執行的 VM 支付費用。 使用無伺服器應用程式時，則只需為實際耗用的計算資源支付費用。 在這兩種情況中，您都需要考量任何其他服務 (例如，儲存體、資料庫和傳訊服務) 的成本。

**延展性**。 Azure Functions 會根據內送事件數量自動進行調整，以符合需求。 使用協調器時，您可以透過增加叢集中執行的服務執行個體數目，來進行相應放大。 您也可以透過對叢集新增額外的 VM 來進行調整。

我們的參考實作主要是使用 Kubernetes，但我們也的確有對某項服務 (也就是遞送記錄服務) 使用 Azure Functions。 Azure Functions 非常適合用於此特定服務，因為它是事件驅動的工作負載。 藉由使用事件中樞觸發程序來叫用函式，服務所需的程式碼數量已降至最低。 此外，遞送記錄服務並非主要工作流程的一部分，因此在 Kubernetes 叢集之外執行此服務並不會影響使用者所起始之作業的端對端延遲。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [服務間通訊](./interservice-communication.md)

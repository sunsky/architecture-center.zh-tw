---
title: "在 Azure 上使用 Kubernetes 設計、建置及操作微服務"
description: "在 Azure 上設計、建置及操作微服務"
author: MikeWasson
ms.date: 12/08/2017
ms.openlocfilehash: 857e91a8eeefec18b459f2e66fde9a4f8bbe7b21
ms.sourcegitcommit: 744ad1381e01bbda6a1a7eff4b25e1a337385553
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2018
---
# <a name="designing-building-and-operating-microservices-on-azure"></a>在 Azure 上設計、建置及操作微服務

![](./images/drone.svg)

微服務已成為熱門的架構樣式，用於建置可復原、高延展性、可獨立部署，以及能夠快速發展的雲端應用程式。 不過，微服務需要不同的方法來設計和建置應用程式，而不僅僅是現今流行的用語。 

在這一系列文章中，我們會探討如何在 Azure 上建置和執行微服務架構。 主題包括：

- 使用網域導向設計 (DDD) 來設計微服務架構。 
- 選擇適合計算、儲存體、傳訊和其他設計元素的 Azure 技術。
- 了解微服務設計模式。
- 針對復原功能、延展性和效能進行設計。
- 建立 CI/CD 管線。


我們始終將焦點放在端對端案例：可讓客戶排程要透過無人機取件和遞送包裹的無人機遞送服務。 您可以在 GitHub 上尋找適用於參考實作的程式碼。

[![GitHub](../_images/github.png) 參考實作][drone-ri]

但首先，讓我們從基本概念開始。 何謂微服務，以及採用微服務架構的優點為何？

## <a name="why-build-microservices"></a>為何要建置微服務？

在微服務架構中，應用程式是由數個小型、獨立的服務所組成。 以下是微服務的一些定義特性：

- 每個微服務服務會實作單一商務功能。
- 微服務夠小，只要一小組開發人員即可進行撰寫和維護。
- 微服務會在個別的程序中執行，並透過定義完善的 API 或傳訊模式進行通訊。 
- 微服務不會共用資料存放區或資料結構描述。 每個微服務負責維護自己的資料。 
- 微服務具有不同的程式碼基底，而且不會共用原始程式碼。 不過會使用共同的公用程式庫。
- 每個微服務都可獨立於其他服務進行部署及更新。 

正確完成時，微服務可以提供許多實用優勢：

- **靈活度。** 因為微服務為獨自部署，所以可更輕鬆地管理錯誤 (bug) 修正和功能版本。 您可以逕自更新服務，而不必重新部署整個應用程式，並於發生錯誤時復原更新。 在許多傳統應用程式中，如果在應用程式的一個組件中發現錯誤 (bug)，應用程式可能會封鎖整個發行程序；因此，新功能可能延誤，以等候錯誤修正進行整合、測試和發佈。  

- **小型程式碼、小型團隊。** 微服務應小到讓單一功能小組可加以建置、測試及部署。 小型程式碼基底比較容易了解。 在大型單體式應用程式中，程式碼相依性有隨著時間變得紊亂的傾向，以致新增功能時需要碰觸許多位置的程式碼。 微服務架構不會共用程式碼或資料存放區，因而降低相依性，並可讓您更輕鬆地新增功能。 小型團隊規模也可提升更大的靈活度。 「兩個披薩規則」表示小組應該夠小，只要兩個比薩就可以餵飽小組。 很明顯地，這不是精確的計量且視小組的胃口而定！ 但重點是大型群組的生產力可能較低，因為通訊速度較慢、管理額外負荷會增加，而靈活度會降低。  

- **混用技術**。 小組可以視情況使用混合的技術堆疊，以挑選出最適合其服務使用的技術。 

- **復原功能**。 如果有個別微服務無法使用，只要有任何上游微服務是設計用來正確地處理錯誤 (例如，藉由實作線路中斷)，就不會中斷整個應用程式。

- **延展性**。 微服務架構可讓每個微服務獨立於其他服務進行調整。 這可讓您相應放大需要更多資源的子系統，而不需相應放大整個應用程式。 如果您在容器內部署服務，您也可以將大量微服務封裝到單一主機，以達到更有效的資源使用率。

- **資料隔離**。 您可更輕鬆地執行結構描述更新，因為只有單一微服務受到影響。 在單體式應用程式中，結構描述更新可能變得非常具挑戰性，因為應用程式的不同組件可能會觸碰相同的資料，以致任何結構描述變化都有風險。
 
## <a name="no-free-lunch"></a>天下沒有白吃的午餐

這些優勢並非免費提供。 這一系列的文章主要為了解決建置可復原、延展性且可管理之微服務時的一些挑戰。

- **服務界限**。 當您建置微服務時，您必須仔細思考如何劃出服務之間的界限。 在生產環境中建置及部署服務後，可能難以跨越這些界限進行重構。 設計微服務架構時，選擇適當的服務界限是最大挑戰之一。 每個服務應該多大？ 何時應跨越數個服務建構功能，以及何時應將它保存在相同的服務中？ 在本指南中，我們會說明使用網域導向設計來來尋找服務界限的方法。 首先，使用[網域分析](./domain-analysis.md)來尋找限定內容，然後根據功能性和非功能性需求套用一組[戰略性 DDD 模式](./microservice-boundaries.md)。 

- **資料一致性與完整性**。 微服務的基本原則是每個服務管理自己的資料。 這可讓服務保持低耦合狀態，但可能造成資料完整性或備援挑戰。 我們會在[資料考量](./data-considerations.md)中探討其中一些問題。

- **網路壅塞與延遲**。 使用許多小型且細微的服務可能會導致服務之間需要更多通訊以及較冗長的端對端延遲。 [服務間通訊](./interservice-communication.md)描述服務之間傳訊的考量。 同步和非同步通訊在微服務架構中都佔有一席之地。 完善的 [API 設計](./api-design.md)很重要，使服務保持鬆散耦合狀態，並可獨立部署及更新。
 
- **複雜度**。 微服務應用程式有較多的變動組件。 每項服務都可能很簡單，但服務必須以整體方式一起運作。 單一使用者作業可能牽涉到多項服務。 在[擷取與工作流程](./ingestion-workflow.md)一章中，我們會檢查一些有關在高輸送量上內嵌要求、協調工作流程以及處理失敗的問題。 

- **用戶端與應用程式之間的通訊。**  當您將應用程式分解成許多小型服務時，用戶端應如何與這些服務通訊？ 用戶端應直接呼叫每個個別服務，還是透過 [API 閘道](./gateway.md)路由傳送要求？

- **監視**。 監視分散式應用程式可能比監視單體式應用程式更加困難，因為您必須讓多個服務的遙測相互關聯。 [記錄和監視](./logging-monitoring.md)可解決這些疑慮。

- **持續整合和傳遞 (CI/CD)**。 靈活度是微服務的主要目標之一。 若要達成此目標，您必須有自動化且強固的 [CI/CD](./ci-cd.md)，以便快速且可靠地將個別服務部署至測試和生產環境。

## <a name="the-drone-delivery-application"></a>無人機遞送應用程式

為了探討這些問題，以及說明微服務架構的一些最佳做法，我們建立了名為「無人機遞送應用程式」的參考實作。 您可以在 [GitHub][drone-ri] 上找到此參考實作。

Fabrikam, Inc. 正在推動無人機遞送服務。 該公司經營一個無人機隊。 企業會註冊此服務，而使用者可要求無人機收取貨物進行遞送。 當客戶排程取件後，後端系統就會指派一台無人機並將預估的遞送時間通知使用者。 在遞送過程中，客戶可以使用持續更新的 ETA 來追蹤無人機的位置。

此案例牽涉到相當複雜的領域。 其中一些企業的疑慮包括無人機排程、追蹤包裹、管理使用者帳戶，以及儲存和分析歷史資料。 此外，Fabrikam 希望能快速上市，而後快速反覆運作，並新增各項功能。 此應用程式必須以最高服務等級目標 (SLO) 在雲端規模運作。 Fabrikam 也預期不同的系統部分會有非常不同的資料儲存和查詢需求。 上述所有考量導致 Fabrikam 針對無人機遞送應用程式選擇微服務架構。

> [!NOTE]
> 如需選擇微服務架構或其他架構樣式的說明，請參閱 [Azure 應用程式架構指南](../guide/index.md)。

我們的參考實作會使用 Kubernetes 搭配 [Azure Container Service (ACS)](/azure/container-service/kubernetes/)。 不過，任何容器協調工具 (包括 [Azure Service Fabric](/azure/service-fabric/)) 都會面臨許多高層級的架構決策和挑戰。 

> [!div class="nextstepaction"]
> [網域分析](./domain-analysis.md)


<!-- links -->

[drone-ri]: https://github.com/mspnp/microservices-reference-implementation
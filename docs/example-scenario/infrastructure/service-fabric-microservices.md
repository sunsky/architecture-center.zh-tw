---
title: 使用 Service Fabric 來分解應用程式
titleSuffix: Azure Example Scenarios
description: 將大型整合型應用程式分解成微服務。
author: timomta
ms.date: 09/20/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: fasttrack
social_image_url: /azure/architecture/example-scenario/infrastructure/media/architecture-service-fabric-complete.png
ms.openlocfilehash: 67610f016321623cbffb0759cc9e75d2352b60bd
ms.sourcegitcommit: 3b15d65e7c35a19506e562c444343f8467b6a073
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2019
ms.locfileid: "54907894"
---
# <a name="using-service-fabric-to-decompose-monolithic-applications"></a>使用 Service Fabric 來分解整合型應用程式

在此範例案例中，我們將逐步說明如何使用 [Service Fabric](/azure/service-fabric/service-fabric-overview) 作為平台來分解笨重的整合型應用程式。 在這裡，我們考慮採用反覆性方法將 IIS/ASP.NET 網站分解為由多個可管理的微服務所組成的應用程式。

從整合型架構移至微服務架構，具有下列優點：

- 您可以變更一個小型、可理解的程式碼單元，並且只部署該單元。
- 每個程式碼單元都只需要幾分鐘或更少的時間來部署。
- 若該小型單元中發生錯誤，只有該單元會停止運作，而不是整個應用程式都停止運作。
- 小型的程式碼單元可以輕鬆地以分散式方式散發給多個開發團隊。
- 新的開發人員可以快速且輕鬆地掌握每個單元的分散式功能。

我們在此範例中使用伺服器陣列上的大型 IIS 應用程式，但反覆分解並裝載的概念可用於任何類型的大型應用程式。 雖然此解決方案使用 Windows，但 Service Fabric 也可以在 Linux 上執行。 它可以在內部部署環境、雲端或您選擇之雲端提供者中的 VM 節點上執行。

## <a name="relevant-use-cases"></a>相關使用案例

此案例與使用大型整合型 Web 應用程式並發生下列問題的組織有關：

- 小型程式碼變更發生錯誤，導致整個網站停擺。
- 由於必須更新整個網站，所以發行需要數天才完成。
- 由於程式碼基底很複雜，以致單一個人需要了解的內容遠大於他所能處理的上限，造成開發人員或團隊難以快速上手。

## <a name="architecture"></a>架構

使用 Service Fabric 作為裝載平台時，我們可以將大型 IIS 網站轉換為微服務集合，如下所示：

![架構圖表](./media/architecture-service-fabric-complete.png)

在上圖中，我們將大型 IIS 應用程式的所有元件分解為：

- 一個路由或閘道服務，可接受連入瀏覽器要求、剖析它們以判斷應該將它們交由哪個服務處理，然後將要求轉送到該服務。
- 四個 ASP.NET Core 應用程式，這些應用程式先前是在單一 IIS 網站下以 ASP.NET 應用程式形式執行的虛擬目錄。 應用程式被分解為其自己的獨立微服務。 效果是您可以獨立變更它們、進行版本設定並升級。 在此範例中，我們使用 .Net Core 與 ASP.NET Core 重寫了每個應用程式。 這些是撰寫為 [Reliable Services](/azure/service-fabric/service-fabric-reliable-services-introduction)，因此它們能以原生方式存取完整的 Service Fabric 平台功能與優點 (通訊服務、健康情況報告與通知等)。
- 有一個名為「索引服務」的 Windows 服務會被放置在 Windows 容器中，因此它再也不會直接變更底層伺服器的登錄，但是會以自封式方式執行並隨著其所有相依性以單一單元方式部署。
- 一個封存服務，它只是可根據排程執行並為網站執行一些其他工作的可執行檔。 它是以獨立可執行檔方式直接裝載，因為我們經判斷後發現它會在不經修改的情況下執行它必須執行的工作，而且變更它並沒有投資效益存在。

## <a name="considerations"></a>考量

第一個挑戰是開始找出可從整體型轉換為微服務以供該整體呼叫的小型程式碼部分。 反覆經過一段時間，整體會細分為這些開發人員可以輕鬆地了解、 變更並在低風險情況下快速部署的微服務集合。

Service Fabric 被選擇的原因是它能夠支援在其各種型態中執行所有微服務。 例如，您可能會有獨立可執行檔、新小型網站、新小型 API 與集中式服務等的混合式環境。Service Fabric 可以將這些所有服務類型合併到單一叢集。

為達到此最終結果 (亦即分解的應用程式)，我們使用反覆性方法。 我們從伺服器陣列上的大型 IIS/ASP.NET 網站開始。 該伺服器陣列的單一節點如下圖所示。 它包含具有數個虛擬目錄、網站呼叫的額外 Windows 服務以及會執行一些定期網站封存維護之可執行檔的原始網站。

![整合型架構圖](./media/architecture-service-fabric-monolith.png)

在第一個部署反覆項目上，IIS 網站與其虛擬目錄會放置在 [Windows 容器](/azure/service-fabric/service-fabric-containers-overview)中。 這樣做可讓網站維持運作，但不會緊密繫結到底層伺服器節點 OS。 容器是由底層 Service Fabric 節點執行並協調，但節點不需要擁有網站所依存的任何狀態 (登錄項目與檔案等)。 所有那些項目都在容器中。 我們也因為相同的原因將索引服務放置在 Windows 容器中。 您能以獨立方式部署、進行版本設定及調整容器規模。 最後，我們在一個簡單的[獨立可執行檔](/azure/service-fabric/service-fabric-guest-executables-introduction)中裝載封存服務，因為它是沒有任何特殊需求的自封式 .exe。

下圖顯示您的大型網站現在如何分解為獨立單元並在時間允許的情況下進一步分解。

![顯示部分分解的架構圖](./media/architecture-service-fabric-midway.png)

進一步的開發著重在分解上圖所述的單一大型預設網站容器。 每個虛擬目錄 ASP.NET 應用程式都會從容器移除 (一次一個) 並移植到 ASP.NET Core [可靠服務](/azure/service-fabric/service-fabric-reliable-services-introduction)。

一旦每個虛擬目錄都分解之後，會以 ASP.NET Core 可靠服務方式撰寫預設網站，此網站可接受連入瀏覽器要求並將其路由傳送到正確的 ASP.NET 應用程式。

### <a name="availability-scalability-and-security"></a>可用性、延展性與安全性

Service Fabric [能支援各種微服務](/azure/service-fabric/service-fabric-choose-framework)，同時快又簡單地將它們之間的呼叫維持在相同叢集。 Service Fabric 是[具容錯能力](/azure/service-fabric/service-fabric-availability-services)的自我治癒式叢集，可以執行容器、可執行檔，甚至有原生 API 可在其中直接撰寫微服務 (上面所述的 'Reliable Services')。 平台會實作滾動式升級並為每個微服務進行版本設定。 您可以告訴平台跨 Service Fabric 叢集執行更多或更少的任何給定微服務，以只[相應](/azure/service-fabric/service-fabric-concepts-scalability) 縮小或放大您需要的微服務。

Service Fabric 是建置在虛擬 (或實體) 節點且具有網路、儲存體與作業系統之基礎結構上的叢集。 因此，它有一組系統管理、維護與監視工作。

您也想要考慮叢集的治理與控制。 就像您不想讓人任意部署資料庫到您的生產資料庫伺服器一樣，您也不想讓人在未經監督的情況下部署應用程式到 Service Fabric 叢集。

Service Fabric 能夠裝載許多不同的[應用程式案例](/azure/service-fabric/service-fabric-application-scenarios)，因此請花時間看看哪個適用於您的情況。

## <a name="pricing"></a>價格

針對裝載在 Azure 中的 Service Fabric 叢集，影響成本最大的因素是您叢集中的節點數目與大小。 Azure 允許快速並簡單地建立由您所指定底層節點大小組成的叢集，但計算費用是以節點大小乘以節點數目為基礎。

其他對成本影響較小的元件是每個節點是虛擬磁碟的儲存體費用與從 Azure 網路連入的 IO 費用 (例如，從 Azure 到使用者瀏覽器的網路流量)。

為取得成本概念，我們使用一些叢集大小、網路與儲存體的預設值建立了一個範例：看看[定價計算機](https://azure.com/e/52dea096e5844d5495a7b22a9b2ccdde)。 您可以根據您的情況更新此預設計算機中的值。

## <a name="next-steps"></a>後續步驟

花一些時間閱讀 Service Fabric [文件](/azure/service-fabric/service-fabric-overview)並檢閱不同的[應用程式案例](/azure/service-fabric/service-fabric-application-scenarios)以便熟悉整個概念。 文件將告訴您叢集由什麼組成、叢集可以在哪些項目上執行、軟體架構與維護作業。

若要查看現有 .NET 應用程式的 Service Fabric 示範，請部署 Service Fabric [快速入門](/azure/service-fabric/service-fabric-quickstart-dotnet)。

從您目前應用程式的觀點，開始思考它的不同功能。 選擇其中一個並思考您可以如何只將該功能從整體獨立出來。 一次只將它視為一個獨立的可了解部分。

## <a name="related-resources"></a>相關資源

- [在 Azure 上建置微服務](/azure/architecture/microservices)
- [Service Fabric 概觀](/azure/service-fabric/service-fabric-overview)
- [Service Fabric 程式設計模型](/azure/service-fabric/service-fabric-choose-framework)
- [Service Fabric 可用性](/azure/service-fabric/service-fabric-availability-services)
- [調整 Service Fabric 規模](/azure/service-fabric/service-fabric-concepts-scalability)
- [在 Service Fabric 中裝載容器](/azure/service-fabric/service-fabric-containers-overview)
- [在 Service Fabric 中裝載獨立可執行檔](/azure/service-fabric/service-fabric-guest-executables-introduction)
- [Service Fabric 原生的 Reliable Services](/azure/service-fabric/service-fabric-reliable-services-introduction)
- [Service Fabric 應用程式案例](/azure/service-fabric/service-fabric-application-scenarios)
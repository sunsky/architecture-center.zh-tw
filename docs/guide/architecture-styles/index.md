---
title: 架構樣式
titleSuffix: Azure Application Architecture Guide
description: 雲端應用程式的一般架構樣式。
layout: LandingPage
ms.topic: landing-page
ms.service: architecture-center
ms.subservice: reference-architecture
ms.date: 08/30/2018
ms.openlocfilehash: 6895ffc9c73dac29c27e7d8df68550c94f68b10a
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58346530"
---
# <a name="architecture-styles"></a>架構樣式

*架構樣式*是一系列共用特定特性的架構。 例如，[多層式架構 (N-tier)][n-tier] 是一般架構樣式。 最近，[微服務架構][microservices]已開始受到青睞。 架構樣式不需要使用特定技術，但是有些技術非常適合特定架構。 例如，容器的本質很適合微服務。

我們已找出一組可經常在雲端應用程式中發現的架構樣式。 每個樣式的文章包括：

- 樣式的描述和邏輯圖表。
- 選擇此樣式的時機建議。
- 優點、挑戰和最佳作法。
- 使用相關 Azure 服務的建議部署。

## <a name="a-quick-tour-of-the-styles"></a>樣式快速導覽

本節會針對我們已識別的架構樣式提供快速導覽，以及一些使用方式上的高層級考量。 在連結的主題中閱讀更多詳細資料。

### <a name="n-tier"></a>多層式架構 (N-tier)

<!-- markdownlint-disable MD033 -->

<img src="./images/n-tier-sketch.svg" style="float:left; margin-top:6px;"/>

<!-- markdownlint-enable MD033 -->

**[多層式架構 (N-Tier)][n-tier]** 是企業應用程式的傳統架構。 相依性是透過將應用程式分「層」來進行管理，這些層會執行展示、商務邏輯和資料存取等邏輯函式。 一個層只能呼叫位於該層下方的層。 不過，此水平分層可能是負擔。 您可能很難在沒有碰觸到應用程式其餘部分的情況下，將變更引入應用程式的一個部分。 這讓經常性更新變成一個挑戰，進而對新增功能的速度造成限制。

多層式架構 (N-Tier) 相當適合用來移轉已使用分層架構的現有應用程式。 因此，多層式架構 (N-Tier) 最常出現在在基礎結構即服務 (IaaS) 方案，或混合使用 IaaS 和受控服務的應用程式中。

### <a name="web-queue-worker"></a>Web 佇列背景工作角色 (Web-Queue-Worker)

<!-- markdownlint-disable MD033 -->

<img src="./images/web-queue-worker-sketch.svg" style="float:left; margin-top:6px;"/>

<!-- markdownlint-enable MD033 -->

如需單純的 PaaS 解決方案，請考慮使用 **[Web 佇列背景工作角色](./web-queue-worker.md)** 架構。 在此樣式中，應用程式會有 Web 前端來處理 HTTP 要求，以及有後端背景工作角色來執行耗用大量 CPU 的工作或長時間執行作業。 前端會透過非同步的訊息佇列與背景工作角色通訊。

Web 佇列背景工作角色適用於具有一些耗用大量資源的工作，但相對簡單的網域。 如同多層式架構 (N-tier)，該架構相當容易理解。 使用受控服務可簡化部署和作業。 但搭配複雜的網域，就很難管理相依性。 前端與背景工作角色很容易就會成為難以維護及更新的大型整合元件。 如同多層式架構 (N-tier)，這會減少更新頻率並限制創新。

### <a name="microservices"></a>微服務

<!-- markdownlint-disable MD033 -->

<img src="./images/microservices-sketch.svg" style="float:left; margin-top:6px;"/>

<!-- markdownlint-enable MD033 -->

如果您的應用程式有較複雜的網域，請考慮將移至 **[微服務][microservices]** 架構。 微服務應用程式是由許多小型且獨立的服務所組成。 每個服務會實作單一商務功能。 服務相依性低，透過 API 合約進行通訊。

每個服務都可由小型的重點開發團隊來建置。 無須小組間的大量協調就可以部署個別服務，並且可頻繁地進行更新。 比起多層式架構 (N-tier) 或 Web 佇列背景工作角色架構，微服務架構的建立和管理是較為複雜的。 它需要成熟的開發和 DevOps 文化特性。 但是，此樣式可帶來更快的發行速度、更迅速的創新和更容易復原的架構。

### <a name="cqrs"></a>CQRS

<!-- markdownlint-disable MD033 -->

<img src="./images/cqrs-sketch.svg" style="float:left; margin-top:6px;"/>

<!-- markdownlint-enable MD033 -->

**[CQRS](./cqrs.md)** (命令和查詢責任隔離) 樣式會將讀取和寫入作業分為不同模型。 這會隔離系統的組件，並從讀取資料的組件中更新資料。 此外，讀取可以針對具體化檢視執行，實際地與寫入資料庫分開。 這讓您可個別調整讀取和寫入工作負載的規模，以及針對查詢最佳化具體化檢視。

CQRS 最適合套用至較大架構的子系統。 一般而言，您不應強制它橫跨整個應用程式，因為這只會產生不必要的複雜性。 請考慮使用共同作業網域，其中有許多使用者可存取相同的資料。

### <a name="event-driven-architecture"></a>事件驅動架構

<!-- markdownlint-disable MD033 -->

<img src="./images/event-driven-sketch.svg" style="float:left; margin-top:6px;"/>

**[事件驅動的架構](./event-driven.md)** 會使用發行-訂閱 (pub sub) 模型，其中產生者發行事件，而取用者訂閱這些事件。 產生者與取用者彼此獨立，且取用者間也彼此獨立。

對於擷取和處理大量資料，且延遲非常低的應用程式 (如 IoT 解決方案)，請考慮使用事件驅動架構。 當不同子系統必須在相同事件資料上執行不同類型的處理程序時，此樣式也十分適用。

<br />

<!-- markdownlint-enable MD033 -->

### <a name="big-data-big-compute"></a>巨量資料與大量計算

對符合某些特定設定檔的工作負載而言， **[巨量資料](./big-data.md)** 與 **[大量計算](./big-compute.md)** 是其專屬架構樣式。 巨量資料會將非常大的資料集分割成區塊，並在整個集合上執行平行處理，以便進行分析和報告。 大量計算 (也稱為高效能運算 (HPC)) 會在大量核心 (數千個) 間執行平行計算。 網域包含模擬、模型和 3-D 轉譯。

## <a name="architecture-styles-as-constraints"></a>作為限制的架構樣式

架構樣式會在設計置入限制，包括一組可以顯示的元素和這些元素間的允許關聯性。 透過在廣泛的選擇中加上限制，這些限制可引導架構的「形狀」。 當架構符合特定樣式的限制時，某些理想的屬性就會出現。

例如，微服務中的限制包括：

- 服務代表單一責任。
- 每個服務彼此獨立。
- 服務所擁有的資料只專屬於該服務。 服務不會共用資料。

藉由遵循這些限制，會出現的是系統中的服務可以各自獨立部署、錯務可被隔離、允許頻繁更新，以及可輕鬆將新技術引入應用程式。

選擇架構樣式之前，請確定您了解該樣式的基礎原則和限制。 否則，您可能表面上會得到符合樣式的設計，但並沒有充分運用該樣式。 實用也是很重要的。 有時候比起堅持架構單純性，放鬆限制會比較好。

下表摘要說明每種樣式管理相依性的方式，以及每種樣式適用的最佳網域類型。

| 架構樣式 | 相依性管理 | 網域類型 |
|--------------------|------------------------|-------------|
| 多層式架構 (N-tier) | 依據子網路分割的水平分層 | 傳統企業網域。 更新的頻率低。 |
| Web 佇列背景工作 | 前端和後端作業，透過非同步傳訊區分。 | 具有一些資源密集工作且相對簡單的網域。 |
| 微服務 | 以垂直方式 (功能性) 區分服務，服務彼此透過 API 呼叫。 | 複雜的網域。 頻繁更新。 |
| CQRS | 讀取/寫入隔離。 結構描述和規模調整會分別最佳化。 | 共同作業的網域，其中許多使用者會存取相同的資料。 |
| 事件驅動架構。 | 生產者/取用者。 每個子系統的獨立檢視。 | IoT 和即時系統 |
| 巨量資料 | 將大型資料集分割成小區塊。 在本機資料集上平行處理。 | 批次和即時資料分析。 使用 ML 的預測分析。 |
| 大量計算| 資料會配置到數千個核心。 | 計算密集型網域 (例如模擬)。 |

## <a name="consider-challenges-and-benefits"></a>挑戰和優點考量

限制也會產生挑戰，因此採用這些樣式時，請務必了解利弊得失。 針對*此子網域和繫結的內容*，請考慮優點多於挑戰的架構樣式。

以下是一些選取架構樣式時要考量的挑戰類型：

- **複雜度**。 架構樣式的複雜度適合您的網域嗎？ 或是反過來，該樣式對您的網域而言太簡化了嗎？ 在此情況下，您最後可能會得到「[大泥球 (big ball of mud)][ball-of-mud]」，因為架構不會協助您清楚地管理相依性。

- **非同步傳訊和最終一致性**。 非同步傳訊可以用來分離服務，並增加可靠性 (因為可重試訊息) 和延展性。 不過，這也會產生一律一次 (always-once) 語意以及最終一致性等挑戰。

- **服務間通訊**。 當您將應用程式分解成個別服務時，會發生的風險是，服務間的通訊會造成無法接受的延遲或產生網路壅塞 (例如使用微服務架構時)。

- **可管理性**。 要管理應用程式、監視器或部署更新等作業會有多困難呢？

[ball-of-mud]: https://en.wikipedia.org/wiki/Big_ball_of_mud
[microservices]: ./microservices.md
[n-tier]: ./n-tier.md

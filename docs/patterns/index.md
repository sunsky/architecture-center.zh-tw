---
title: "雲端設計模式"
description: "Microsoft Azure 的雲端設計模式"
keywords: Azure
ms.openlocfilehash: bf9fb2555f5c80cab9e4616ba52155bf1284d26f
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="cloud-design-patterns"></a>雲端設計模式

這些設計模式有助於在雲端中建置可靠、可擴充且安全的應用程式。

每個模式都會說明模式處理的問題、套用模式的考量，以及以 Microsoft Azure 為基礎的範例。 大部分的模式會包括程式碼範例或程式碼片段，示範如何在 Azure 上實作模式。 無論如何，大部分的模式都適用於任一分散式系統 (無論是裝載於 Azure 或其他雲端平台上)。

## <a name="challenges-in-cloud-development"></a>雲端開發中的挑戰

<table>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="./category/availability.md"><img src="_images/category/availability.svg" alt="Availability" /></a></td>
    <td>
        <h3><a href="./category/availability.md">可用性</a></h3>
        <p>可用性是系統執行功能及運作的時間比例，通常是以執行時間的百分比表示。 可能會受到系統錯誤、基礎結構問題、惡意攻擊和負載系統的影響。  雲端應用程式一般會提供使用者服務等級協定 (SLA)，因此應用程式必須設計為可充分提高可用性。</p>
    </td>
</tr>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="./category/data-management.md"><img src="_images/category/data-management.svg" alt="Data Management" /></a></td>
    <td>
        <h3><a href="./category/data-management.md">資料管理</a></h3>
        <p>資料管理是雲端應用程式的關鍵元素，並且會影響多數的品質屬性。 因為效能、延展性或可用性之類的原因，資料通常裝載在不同位置以及在多個伺服器上，而這可能出現一些挑戰。 例如，必須維持資料的一致性，並且通常需要同步處理跨不同位置的資料。</p>
    </td>
</tr>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="./category/design-implementation.md"><img src="_images/category/design-implementation.svg" alt="Design and Implementation" /></a></td>
    <td>
        <h3><a href="./category/design-implementation.md">設計與實作</a></h3>
        <p>良好的設計會包含一些要素，例如元件設計和部署中的一致性及連貫性、用於簡化管理及開發的可維護性，以及可讓元件和子系統在其他應用程式和其他案例中使用的重複使用性。 設計和實作階段所做的決策，會對雲端上裝載的應用程式和服務在品質和擁有權總成本上產生重大影響。</p>
    </td>
</tr>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="./category/messaging.md"><img src="_images/category/messaging.svg" alt="Messaging" /></a></td>
    <td>
        <h3><a href="./category/messaging.md">傳訊</a></h3>
        <p>雲端應用程式的分散式本質需要可連接元件和服務的傳訊基礎結構，最理想的情況是使用鬆散耦合的方式來達到延展性最大化。 已廣泛使用的非同步傳訊提供許多優點，但也帶來傳訊的順序安排、有害訊息的管理和冪等性 (idempotency) 等挑戰</p>
    </td>
</tr>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="./category/management-monitoring.md"><img src="_images/category/management-monitoring.svg" alt="Management and Monitoring" /></a></td>
    <td>
        <h3><a href="./category/management-monitoring.md">管理與監視</a></h3>
        <p>雲端應用程式會在遠端資料中心內執行，其中您沒有基礎結構或作業系統 (部份情況下) 的完整控制權。 比起內部部署，這可能會讓管理和監視作業變得更困難。 應用程式必須公開執行階段資訊，讓系統管理員及操作員可以使用該資訊來管理及監視系統，以及支援變更商業需求和自訂，而不需要停止或重新部署應用程式。</p>
    </td>
</tr>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="./category/performance-scalability.md"><img src="_images/category/performance-scalability.svg" alt="Performance and Scalability" /></a></td>
    <td>
        <h3><a href="./category/performance-scalability.md">效能和延展性</a></h3>
        <p>效能是指系統在指定時間間隔內執行任何動作的回應能力，而延展性則是系統在不影響效能情況下處理負載增量的能力，或是系統處理可用資源快速增加的能力。 雲端應用程式通常會遇到變動的工作負載和活動尖峰。 要預測這些問題 (尤其是在多租用戶案例中) 幾乎不可能。 相反地，應用程式應要能夠在限制內相應放大，來符合尖峰需求，並且在需求減少時相應縮小。 延展性不只要考量運算執行個體，還有其他資料儲存體和傳訊基礎結構等項目。</p>
    </td>
</tr>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="./category/resiliency.md"><img src="_images/category/resiliency.svg" alt="Resiliency" /></a></td>
    <td>
        <h3><a href="./category/resiliency.md">復原</a></h3>
        <p>復原是指系統正常處理並從失敗中復原的能力。 雲端裝載的本質，像是其中的應用程式通常是多租用戶、使用共用平台服務、爭用資源和頻寬、透過網際網路通訊及採用商用硬體，這表示發生暫時性和更多永久性錯誤的可能性會同時增加。 必須能偵測失敗並快速而有效地復原，才能保有復原功能。</p>
    </td>
</tr>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="./category/security.md"><img src="_images/category/security.svg" alt="Security" /></a></td>
    <td>
        <h3><a href="./category/security.md">安全性</a></h3>
        <p>安全性是指系統有能力防止正確用法以外的惡意或意外動作，並且防止洩漏或遺失資訊。 雲端應用程式皆暴露在受信任內部部署範圍外的網際網路上，且開放給大眾使用，也可能服務不受信任的使用者。 應用程式的設計和部署方式應要能保護應用程式不受到惡意攻擊、僅限核准的使用者可存取應用程式，以及保護敏感性資料。</p>
    </td>
</tr>
</table>

## <a name="catalog-of-patterns"></a>模式的目錄

| 模式 | 摘要 |
| ------- | ------- |
| [外交官 (Ambassador)](./ambassador.md) | 建立會代表取用者服務或應用程式傳送網路要求的協助程式服務。 |
| [防損毀層](./anti-corruption-layer.md) | 在最新應用程式和舊系統間實作外觀或配接層。 |
| [前端的後端](./backends-for-frontends.md) | 建立由特定前端應用程式或介面取用的個別後端服務。 |
| [隔艙](./bulkhead.md) | 將應用程式的元素隔離到集區中，以便其中一個元素失敗時，其他元素可以繼續運作。 |
| [另行快取](./cache-aside.md) | 依需要從資料存放區將資料載入快取中 |
| [斷路器](./circuit-breaker.md) | 在連線到遠端服務或資源時，處理可能需要不同時間來修復的錯誤。 |
| [CQRS](./cqrs.md) | 如果作業讀取的資料來自使用個別介面更新資料的作業，則隔離該作業。 |
| [補償交易](./compensating-transaction.md) | 復原由一系列步驟執行的工作，這些步驟共同定義結果一致的作業。 |
| [競爭取用者](./competing-consumers.md) | 讓多個並行取用者處理在相同的傳訊通道上接收的訊息。 |
| [計算資源彙總](./compute-resource-consolidation.md) | 將多個工作或作業合併為單一計算單位 |
| [事件來源](./event-sourcing.md) | 使用僅附加的存放區記錄完整系列的事件，其描述對網域中的資料採取的動作。 |
| [外部設定存放區](./external-configuration-store.md) | 將設定資訊從應用程式部署套件移至集中位置。 |
| [同盟身分識別](./federated-identity.md) | 將驗證委派給外部身分識別提供者。 |
| [閘道管理員](./gatekeeper.md) | 可保護應用程式和服務，方法是使用專用的主機執行個體，其會作為用戶端和應用程式或服務之間的代理程式、會驗證和處理要求，並在兩者之間傳遞要求和資料。 |
| [閘道彙總](./gateway-aggregation.md) | 您可以使用閘道來將多個個別的要求彙總成單一要求。 |
| [閘道卸載](./gateway-offloading.md) | 將共用或特殊服務功能卸載至閘道 Proxy。 |
| [閘道路由](./gateway-routing.md) | 使用單一端點將要求路由至多個服務。 |
| [健康情況端點監視](./health-endpoint-monitoring.md) | 實作應用程式中的功能檢查，而外部工具可透過公開的端點定期存取此應用程式。 |
| [索引資料表](./index-table.md) | 針對資料存放區中查詢經常參考的欄位建立索引。 |
| [選出領導者](./leader-election.md) | 選取一個執行個體作為領導者，負責管理其他執行個體，協調分散式應用程式中共同作業工作執行個體集合執行的動作。 |
| [具體化檢視模式](./materialized-view.md) | 當資料格式對必要的查詢作業而言不理想時，對一或多個資料存放區中的資料產生預先填入的檢視。 |
| [管道與篩選器](./pipes-and-filters.md) | 將執行複雜處理程序的工作，細分成一系列可重複使用的個別項目。 |
| [優先順序佇列](./priority-queue.md) | 針對傳送給服務的要求排列優先順序，讓高優先順序要求的接收和處理順序在低優先順序要求之前。 |
| [佇列型負載調節](./queue-based-load-leveling.md) | 使用佇列來作為工作與其所叫用服務之間的緩衝區，以使間歇性的繁重負載順暢。 |
| [重試](./retry.md) | 讓應用程式可以在嘗試連線到服務或網路資源時，藉由明確地重試先前失敗的作業，處理預期的暫時性失敗。 |
| [排程器代理程式監督員](./scheduler-agent-supervisor.md) | 在一組分散的服務和其他遠端資源中協調一組動作。 |
| [分區化](./sharding.md) | 將資料存放區分割為一組水平分割或分區。 |
| [側車](./sidecar.md) | 將應用程式的元件部署到個別的處理序或容器，以提供隔離和封裝。 |
| [靜態內容裝載](./static-content-hosting.md) | 將靜態內容部署到可以直接將其交付給用戶端的雲端儲存體服務。 |
| [Strangler](./strangler.md) | 透過將功能的特定片段逐漸取代成新的應用程式和服務，來逐步移轉舊有系統。 |
| [節流](./throttling.md) | 控制應用程式執行個體、個別租用戶或整個服務所使用的資源耗用量。 |
| [Valet 金鑰](./valet-key.md) | 使用可提供用戶端對特定資源或服務受限制的直接存取的權杖或金鑰。 |
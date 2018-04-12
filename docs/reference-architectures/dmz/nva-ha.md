---
title: 部署高可用性的網路虛擬設備
description: 如何部署高可用性的虛擬設備。
author: telmosampaio
ms.date: 12/06/2016
pnp.series.title: Network DMZ
pnp.series.prev: secure-vnet-dmz
cardTitle: Deploy highly available network virtual appliances
ms.openlocfilehash: fe279eea3f9cb024d6c6c14943013b9b9a87bc9c
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-highly-available-network-virtual-appliances"></a>部署高可用性的網路虛擬設備

本文會示範如何在 Azure 中部署一組網路虛擬設備 (NVA) 以取得高可用性。 NVA 通常是用來控制從周邊網路 (也稱為 DMZ) 流至其他網路或子網路之網路流量的流動。 若要了解如何在 Azure 中實作 DMZ，請參閱[Microsoft 雲端服務和網路安全性][cloud-security]。 本文包含「僅輸入」、「僅輸出」，以及「輸入和輸出」的範例架構。 

<strong>必要條件：</strong>本文假設您對 Azure 網路、[Azure 負載平衡器][lb-overview]，以及[使用者定義的路由][udr-overview] (UDR) 已具有基本的了解。 


## <a name="architecture-diagrams"></a>架構圖

NVA 可以部署到許多不同架構的 DMZ 中。 例如，下圖說明針對輸入使用[單一 NVA][nva-scenario] 的方式。 

![[0]][0]

在這種架構中，NVA 會檢查所有輸入和輸出的網路流量，並僅傳遞符合網路安全性規則的流量，藉此提供安全的網路界限。 不過，由於所有網路流量都必須通過 NVA，這也代表 NVA 會成為網路中的單一失敗點。 如果 NVA 失敗，網路流量就沒有其他路徑，導致所有後端子網路都無法使用。

若要讓 NVA 具高可用性，請在可用性設定組中部署多個 NVA。    

下列架構描述高可用性 NVA 所需的資源和設定：

| 方案 | 優點 | 考量 |
| --- | --- | --- |
| [具第 7 層 NVA 的輸入][ingress-with-layer-7] |所有 NVA 節點都是作用中狀態 |需要可以終止連線並使用 SNAT 的 NVA</br> 需要另外一組 NVA 以供來自網際網路與 Azure 流量使用 </br> 僅能用於源自於 Azure 外部的流量 |
| [第 7 層 NVA 的輸出][egress-with-layer-7] |所有 NVA 節點都是作用中狀態 | 需要可以終止連線並實作來源網路位址轉譯 (SNAT) 的 NVA
| [具第 7 層 NVA 的輸入-輸出][ingress-egress-with-layer-7] |所有節點都是作用中狀態<br/>能夠處理來自 Azure 中的流量 |需要可以終止連線並使用 SNAT 的 NVA<br/>需要另外一組 NVA 以供來自網際網路與 Azure 流量使用 |
| [PIP-UDR 切換][pip-udr-switch] |針對所有流量使用一組 NVA<br/>可以處理所有流量 (連接埠規則沒有限制) |主動-被動<br/>需要容錯移轉程序 |

## <a name="ingress-with-layer-7-nvas"></a>具第 7 層 NVA 的輸入

下圖所示範的高可用性架構會在面向網際網路的負載平衡器後方實作輸入 DMZ。 此架構設計成可針對第 7 層流量 (例如 HTTP 或 HTTPS) 提供與 Azure 工作負載的連線：

![[1]][1]

這個架構的優點是所有 NVA 都處於作用中狀態，而且如果其中一個失敗，負載平衡器就會將網路流量導向其他 NVA。 兩個 NVA 都會將流量路由傳送到內部負載平衡器，因此只要有一個 NVA 處於作用中狀態，流量就會繼續流動。 需要這些 NVA 以終止適用於 Web 層 VM 的 SSL 流量。 您無法擴充這些 NVA 以處理內部部署流量，因為內部部署流量需要另一組具有個別網路路由的專用 NVA。

> [!NOTE]
> 此架構用於 [Azure 與內部部署資料中心之間的 DMZ][dmz-on-prem] 參考架構，以及 [Azure 與網際網路之間的 DMZ][dmz-internet] 參考架構。 這兩個參考架構皆包含您可以使用的部署解決方案。 如需詳細資訊，請遵循上述連結。

## <a name="egress-with-layer-7-nvas"></a>具第 7 層 NVA 的輸出

您可以擴充上述的架構，以針對源自 Azure 工作負載的要求提供輸出 DMZ。 下方架構旨在能於 DMZ 中為第 7 層的流量 (如 HTTP 或 HTTPS) 提供 NVA 的高可用性：

![[2]][2]

在此架構中，源自 Azure 中的所有流量都會路由傳送至內部負載平衡器。 負載平衡器會在一組 NVA 之間散發連出要求。 這些 NVA 會使用個別的公用 IP 位址，將流量導向網際網路。

> [!NOTE]
> 此架構用於 [Azure 與內部部署資料中心之間的 DMZ][dmz-on-prem] 參考架構，以及 [Azure 與網際網路之間的 DMZ][dmz-internet] 參考架構。 這兩個參考架構皆包含您可以使用的部署解決方案。 如需詳細資訊，請遵循上述連結。

## <a name="ingress-egress-with-layer-7-nvas"></a>具第 7 層 NVA 的輸入-輸出

在先前的兩個架構中，針對輸入和輸出皆具有個別的 DMZ。 下列架構會示範如何建立可同時用於針對第 7 層流量 (例如 HTTP 或 HTTPS) 之輸入和輸出的 DMZ： 

![[4]][4]

在此架構中，NVA 會處理來自應用程式閘道的連入要求。 NVA 也會處理來自負載平衡器後端集區中之工作負載 VM 的連出要求。 因為連入流量會透過應用程式閘道進行路由傳送，而連出流量則會透過負載平衡器進行路由傳送，因此 NVA 會負責維護工作階段親和性。 亦即，應用程式閘道會維護傳入和傳出要求的對應，使它可以將正確的回應轉送到原始要求者。 不過，內部負載平衡器並無法存取應用程式閘道對應，並會使用自己的邏輯來將回應傳送到 NVA。 負載平衡器有可能會將回應傳送給從未接收到來自應用程式閘道之要求的 NVA。 在此情況下，NVA 必須彼此進行通訊並傳輸回應，使正確的 NVA 可以將回應轉送至應用程式閘道。

> [!NOTE]
> 您也可以透過確保 NVA 執行輸入來源網路位址轉譯 (SNAT)，來解決非對稱的路由問題。 這會將要求者的原始來源 IP 取代為 NVA 用於輸入流量的其中一個 IP 位址。 如此可確保您能夠一次使用多個 NVA，同時保留路由對稱性。

## <a name="pip-udr-switch-with-layer-4-nvas"></a>具第 4 層 NVA 的 PIP-UDR 切換

下列架構示範具有一個主動 NVA 和一個被動 NVA 的架構。 此架構可同時處理第 4 層流量的輸入和輸出： 

![[3]][3]

此架構與本文中所討論的第一個架構類似。 該架構包含會接受和篩選第 4 層連入要求的單一 NVA。 此架構會新增另一個被動 NVA，以提供高可用性。 如果主動 NVA 失敗，被動 NVA 就會變成主動狀態，且 UDR 與 PIP 會變更為指向新主動 NVA 的 NIC。 您可以手動完成 UDR 與 PIP 的變更，也可以使用自動化程序來完成。 自動化程序通常是在 Azure 中執行的精靈或其他監控服務。 它會查詢主動 NVA 上的健康情況探查，並在偵測到 NVA 失敗時，執行 UDR 與 PIP 的切換。 

上圖顯示提供高可用性精靈的 [ZooKeeper][zookeeper] 叢集範例。 在 ZooKeeper 叢集內，節點仲裁會選出一個前置節點。 如果前置節點失敗，剩餘的節點會再次選出一個新的前置節點。 針對這個架構，前置節點會執行在 NVA 上查詢健康情況端點的精靈。 如果 NVA 無法回應健康情況探查，精靈就會啟動被動 NVA。 接著，精靈會呼叫 Azure REST API，以將 PIP 從失敗的 NVA 移除，然後將其附加至新的主動 NVA。 之後，精靈會將 UDR 修改為指向新主動 NVA 的內部 IP 位址。

> [!NOTE]
> 請不要將 ZooKeeper 節點包含在僅能使用包含 NVA 的路由進行存取的子網路中。 否則，如果 NVA 失敗，就無法存取 ZooKeeper 節點。 如果精靈因為任何原因而失敗，您將無法存取任何 ZooKeeper 節點來診斷問題。 

<!--### Solution Deployment-->

<!-- instructions for deploying this solution here --> 

## <a name="next-steps"></a>後續步驟
* 了解如何使用第 7 層 NVA [在 Azure 與內部部署資料中心之間實作 DMZ][dmz-on-prem]。
* 了解如何使用第 7 層 NVA [在 Azure 與網際網路之間實作 DMZ][dmz-internet]。

<!-- links -->
[cloud-security]: /azure/best-practices-network-security
[dmz-on-prem]: ./secure-vnet-hybrid.md
[dmz-internet]: ./secure-vnet-dmz.md
[egress-with-layer-7]: #egress-with-layer-7-nvas
[ingress-with-layer-7]: #ingress-with-layer-7-nvas
[ingress-egress-with-layer-7]: #ingress-egress-with-layer-7-nvas
[lb-overview]: /azure/load-balancer/load-balancer-overview/
[nva-scenario]: /azure/virtual-network/virtual-network-scenario-udr-gw-nva/
[pip-udr-switch]: #pip-udr-switch-with-layer-4-nvas
[udr-overview]: /azure/virtual-network/virtual-networks-udr-overview/
[zookeeper]: https://zookeeper.apache.org/

<!-- images -->
[0]: ./images/nva-ha/single-nva.png "單一 NVA 架構"
[1]: ./images/nva-ha/l7-ingress.png "第 7 層輸入"
[2]: ./images/nva-ha/l7-ingress-egress.png "第 7 層輸出"
[3]: ./images/nva-ha/active-passive.png "主動-被動叢集"
[4]: ./images/nva-ha/l7-ingress-egress-ag.png

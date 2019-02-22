---
title: 針對 HA/DR 所建置的多層式 Web 應用程式
titleSuffix: Azure Example Scenarios
description: 使用 Azure 虛擬機器、可用性集合、可用性區域、Azure Site Recovery 和 Azure 流量管理員，在 Azure 上建立為高可用性和災害復原而建置的多層式 Web 應用程式。
author: sujayt
ms.date: 11/16/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: product-team
social_image_url: /azure/architecture/example-scenario/infrastructure/media/arhitecture-disaster-recovery-multi-tier-app.png
ms.openlocfilehash: 1f82f0bf5421bb251bda2eb60349cc74014fd454
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55898097"
---
# <a name="multitier-web-application-built-for-high-availability-and-disaster-recovery-on-azure"></a>在 Azure 上為高可用性和災害復原而建置的多層式 Web 應用程式

任何產業只要需要部署為高可用性和災害復原而建置的彈性多層式應用程式，皆適用此範例案例。 在此案例中，應用程式包含三個層級。

- Web 層：包含使用者介面的最上層。 這一層會剖析使用者互動，並將動作傳至下一層進行處理。
- 商務層：處理使用者互動，並擬定關於後續步驟的邏輯決策。 這一層會連接 Web 層與資料層。
- 資料層：儲存應用程式資料。 通常會使用資料庫、物件儲存體或檔案儲存體。

常見的應用程式案例包括任何在 Windows 或 Linux 上執行的任務關鍵性應用程式。 這可以是現成可用的應用程式 (例如 SAP 和 SharePoint) 或自訂的企業營運應用程式。

## <a name="relevant-use-cases"></a>相關使用案例

其他相關的使用案例包括：

- 部署高復原能力的應用程式，例如 SAP 和 SharePoint
- 設計企業營運應用程式的商務持續性和災害復原計劃
- 設定災害復原和執行合規性的相關演練

## <a name="architecture"></a>架構

此案例將示範使用 ASP.NET 和 Microsoft SQL Server 的多層式應用程式。 在[支援可用性區域的 Azure 區域](/azure/availability-zones/az-overview#regions-that-support-availability-zones)中，您可以在來源區域中跨可用性區域部署虛擬機器 (VM)，並將 VM 複寫至用於災害復原的目標區域。 在不支援可用性區域的 Azure 區域中，您可以在可用性設定組內部署 VM，並將 VM 複寫至目標區域。

![高復原能力的多層式 Web 應用程式的架構概觀][architecture]

- 在支援區域的區域中，將 VM 分散到兩個可用性區域的每一層中。 在其他區域中，則將 VM 部署在一個可用性設定組中的每一層。
- 您可以將資料庫層設定為使用 Always On 可用性群組。 使用此 SQL Server 設定時，會使用最多八個次要資料庫來設定叢集內的一個主要資料庫。 如果主要資料庫發生問題，叢集就會容錯移轉至其中一個次要資料庫，讓應用程式能夠繼續使用。 如需詳細資訊，請參閱[適用於 SQL Server 的 Always On 可用性群組概觀][docs-sql-always-on]。
- 針對災害復原案例，您可以進行相關設定，以對用於災害復原的目標區域執行 SQL Always On 非同步原生複寫。 如果資料變更率在 Azure Site Recovery 支援的限制內，您也可以設定對目標區域的 Azure Site Recovery 複寫。
- 使用者會透過流量管理員端點來存取前端 ASP.NET Web 層。
- 流量管理員會將流量重新導向至主要來源區域中的主要公用 IP 端點。
- 此公用 IP 會透過公用負載平衡器將呼叫重新導向至其中一個 Web 層 VM 執行個體。 所有的 Web 層 VM 執行個體會位於一個子網路中。
- 來自 Web 層 VM 的每個呼叫，都會透過內部負載平衡器路由至商務層中的其中一個 VM 執行個體，以進行處理。 所有的商務層 VM 會位於一個不同的子網路中。
- 作業會在商務層中進行處理，而 ASP.NET 應用程式會透過 Azure 內部負載平衡器連線到後端層中的 Microsoft SQL Server 叢集。 這些後端 SQL Server 執行個體會位於一個不同的子網路中。
- 流量管理員的次要端點會在用於災害復原的目標區域中設定為公用 IP。
- 如果主要區域的運作中斷，您只要叫用 Azure Site Recovery 容錯移轉，應用程式在目標區域中即會成為使用中狀態。
- 流量管理員端點會將用戶端流量自動重新導向至目標區域中的公用 IP。

### <a name="components"></a>元件

- [可用性設定組][docs-availability-sets]可確保您在 Azure 上部署的 VM 會分散到叢集中多個各自獨立的硬體節點。 當 Azure 內發生硬體或軟體故障時，您的 VM 將只有一部分會受到影響，且您整體的解決方案仍可供使用且正常運作。
- [可用性區域][docs-availability-zones]可讓您的應用程式和資料不因資料中心失敗而受影響。 可用性區域是 Azure 區域內個別的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。
- [Azure Site Recovery (ASR)][docs-azure-site-recovery] 可讓您將 VM 複寫到另一個 Azure 區域，以符合商務持續性和災害復原的需求。 您可以進行定期的災害復原演練，以確實符合合規需求。 會以指定的設定將 VM 複寫到選取的區域，以便您可以在來源區域的中斷事件中復原應用程式。
- [Azure 流量管理員][docs-traffic-manager]是 DNS 型流量負載平衡器，可跨全球的 Azure 區域將流量最適當地分散至服務，同時提供高可用性和回應性。
- [Azure Load Balancer][docs-load-balancer] 會根據規則和健康情況探查來散發輸入流量。 對於所有 TCP 和 UDP 應用程式，負載平衡器可提供低延遲和高輸送量，且最多可相應增加為數百萬個流程。 此案例會使用公用負載平衡器，將傳入的用戶端流量散發至 Web 層。 此案例會使用內部負載平衡器，從商務層將流量散發到後端 SQL Server 叢集。

### <a name="alternatives"></a>替代項目

- 您可以將 Windows 取代為其他作業系統，因為基礎結構中沒有任何項目依存於作業系統。
- [適用於 Linux 的 SQL Server][docs-sql-server-linux] 可取代後端資料存放區。
- 資料庫可以取代為任何可用的標準資料庫應用程式。

## <a name="other-considerations"></a>其他考量

### <a name="scalability"></a>延展性

您可以根據自己的調整需求，在各層中新增或移除 VM。 由於此案例使用負載平衡器，因此您可以在一層中新增更多 VM，而不會影響應用程式的運作時間。

如需其他延展性主題，請參閱 Azure Architecture Center 中的[延展性檢查清單][scalability]。

### <a name="security"></a>安全性

所有流入前端應用程式層的虛擬網路流量都受到網路安全性群組的保護。 規則會限制流量，以便只有前端應用程式層 VM 執行個體可以存取後端資料庫層。 不允許從商務層或資料庫層輸出網際網路流量。 若要降低攻擊使用量，請勿開啟任何直接的遠端管理連接埠。 如需詳細資訊，請參閱 [Azure 網路安全性群組][docs-nsg]。

如需設計安全案例的一般指引，請參閱 [Azure 安全性文件][security]。

## <a name="pricing"></a>價格

使用 Azure Site Recovery 設定 Azure VM 的災害復原，將會持續產生下列費用。

- 每一 VM 的 Azure Site Recovery 授權成本。
- 從來源 VM 磁碟將資料變更複寫到另一個 Azure 區域的網路輸出費用。 Azure Site Recovery 會使用內建的壓縮功能，資料傳輸需求約可減少 50%。
- 復原網站的儲存體成本。 此成本通常等同於來源區域儲存體再加上維護復原的快照集作為復原點所需的任何額外的儲存體。

我們提供了[範例成本計算機][calculator]，可用來對使用六個虛擬機器的三層式應用程式設定災害復原。 所有服務都會在成本計算機中預先設定。 若要查看價格如何隨著您的特定使用案例而變更，請變更適當的變數，以估計成本。

<!-- links -->
[architecture]: ./media/arhitecture-disaster-recovery-multi-tier-app.png
[autoscaling]: /azure/architecture/best-practices/auto-scaling
[availability]: ../../checklist/availability.md
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[docs-availability-zones]: /azure/availability-zones/az-overview
[docs-load-balancer]: /azure/load-balancer/load-balancer-overview
[docs-nsg]: /azure/virtual-network/security-overview
[docs-vmss]: /azure/virtual-machine-scale-sets/overview
[docs-sql-always-on]: /sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server
[docs-vmss-autoscale]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview
[docs-vnet]: /azure/virtual-network/virtual-networks-overview
[docs-sql-server-linux]: /sql/linux/sql-server-linux-overview?view=sql-server-linux-2017
[docs-traffic-manager]: /azure/traffic-manager/
[docs-azure-site-recovery]: /azure/site-recovery/azure-to-azure-quickstart/
[docs-availability-sets]: /azure/virtual-machines/windows/manage-availability/
[calculator]: https://azure.com/e/6835332265044d6d931d68c917979e6d/

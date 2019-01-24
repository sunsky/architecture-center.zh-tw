---
title: 使用 Windows VM 建置安全的 Web 應用程式
titleSuffix: Azure Example Scenarios
description: 透過 Azure 上使用擴展集、應用程式閘道和負載平衡器的 Windows Server，建置安全、多層式 Web 應用程式。
author: iainfoulds
ms.date: 12/06/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: seodec18, Windows
ms.openlocfilehash: 12c7b4749507d4b96e5ce43f98739885c8133e7e
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54485531"
---
# <a name="building-secure-web-applications-with-windows-virtual-machines-on-azure"></a>使用 Azure 上的 Windows 虛擬機器建置安全的 Web 應用程式

此案例提供在 Microsoft Azure 上執行安全、多層式 Web 應用程式的架構和設計指引。 在此範例中，ASP.NET 應用程式會使用虛擬機器安全地連線到受保護的後端 Microsoft SQL Server 叢集。

傳統上，組織必須維護舊版內部部署應用程式和服務，才能提供安全的基礎結構。 藉由在 Azure 中安全地部署這些 Windows Server 應用程式，組織可以將其部署現代化，從而降低內部部署成本和管理額外負荷。

## <a name="relevant-use-cases"></a>相關使用案例

可能適用此案例的一些範例：

- 在安全的雲端環境中將應用程式部署現代化。
- 減少舊版內部部署應用程式和服務的管理額外負荷。
- 利用新的應用程式平台來改善病患的醫療保健與體驗。

## <a name="architecture"></a>架構

![受規範產業的多層式 Windows Server 應用程式相關 Azure 元件的架構概觀][architecture]

此案例顯示前端 Web 應用程式如何連線至後端資料庫 (這兩者皆在 Windows Server 2016 上執行)。 整個案例的資料流程如下所示：

1. 使用者會透過 Azure 應用程式閘道存取前端 ASP.NET 應用程式。
2. 應用程式閘道會將流量散發至 Azure 虛擬機器擴展集內的 VM 執行個體。
3. 應用程式會透過 Azure 負載平衡器連線到後端層中的 Microsoft SQL Server 叢集。 這些後端 SQL Server 執行個體位於不同的 Azure 虛擬網路，受到限制流量的網路安全性群組規則保護。
4. 負載平衡器會將 SQL Server 流量散發到另一個虛擬機器擴展集中的 VM 執行個體。
5. Azure Blob 儲存體會在後端層中作為 SQL Server 叢集的[雲端見證][cloud-witness]。 已利用 Azure 儲存體的 VNet 服務端點啟用 VNet 內的連線。

### <a name="components"></a>元件

- [Azure 應用程式閘道][appgateway-docs]是第 7 層 web 流量負載平衡器，會感知應用程式且可以根據特定的路由規則來散發流量。 應用程式閘道也可以處理 SSL 卸載，以獲得改善的 Web 伺服器效能。
- [Azure 虛擬網路][vnet-docs]可讓 VM 等資源安全地互相通訊，以及與網際網路和內部部署網路通訊。 虛擬網路會提供隔離與分割、篩選與路由流量，並允許位置之間的連線。 此案例中會使用與適當 NSG 結合的兩個虛擬網路，來提供[周邊網路][ dmz] (DMZ) 和應用程式元件隔離。 虛擬網路對等互連會將兩個網路連線在一起。
- [Azure 虛擬機器擴展集][scaleset-docs]可讓您建立和管理一組負載平衡的相同 VM。 VM 執行個體的數目可以自動增加或減少，以因應需求或已定義的排程。 此案例中會使用兩個不同的虛擬機器擴展集 - 一個用於前端 ASP.NET 應用程式執行個體，另一個則用於後端 SQL Server 叢集 VM 執行個體。 PowerShell 預期的狀態設定 (DSC) 或 Azure 自訂指令碼擴充功能可用來佈建使用所需軟體和組態設定的 VM 執行個體。
- [Azure 網路安全性群組 (NSG)][nsg-docs] 包含一些安全性規則，可根據來源或目的地 IP 位址、連接埠和通訊協定允許或拒絕輸入或輸出網路流量。 此案例中的虛擬網路會受到網路安全性群組規則保護，這些規則會限制應用程式元件之間的流量。
- [Azure 負載平衡器][loadbalancer-docs]會根據規則和健康情況探查來散發輸入流量。 對於所有 TCP 和 UDP 應用程式，負載平衡器可提供低延遲和高輸送量，且最多可相應增加為數百萬個流程。 此案例中會使用內部負載平衡器，從前端應用程式層將流量散發到後端 SQL Server 叢集。
- [Azure Blob 儲存體][cloudwitness-docs]會作為 SQL Server 叢集的雲端見證。 此見證用於需要額外投票來決定仲裁的叢集作業及決策。 使用雲端見證不需要額外的 VM 來作為傳統的檔案共用見證。

### <a name="alternatives"></a>替代項目

- 您可以交換使用 Linux 和 Windows，因為基礎結構並不相依於作業系統。

- [適用於 Linux 的 SQL Server][sql-linux] 可取代後端資料存放區。

- [Cosmos DB](/azure/cosmos-db/introduction) 是資料存放區的替代項目。

## <a name="considerations"></a>考量

### <a name="availability"></a>可用性

此案例中的 VM 執行個體會部署在[可用性區域](/azure/availability-zones/az-overview)之間。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 每個已啟用的區域最少有三個可用性區域。 這個跨區域 VM 執行個體的分佈能為應用程式層提供高可用性。

您可以將資料庫層設定為使用 Always On 可用性群組。 使用此 SQL Server 設定時，會使用最多八個次要資料庫來設定叢集內的一個主要資料庫。 如果主要資料庫發生問題，叢集就會容錯移轉至其中一個次要資料庫，讓應用程式能夠繼續使用。 如需詳細資訊，請參閱[適用於 SQL Server 的 Always On 可用性群組概觀][sqlalwayson-docs]。

如需詳細的可用性指引，請參閱 Azure 架構中心內的[可用性檢查清單][availability]。

### <a name="scalability"></a>延展性

此案例會使用前端和後端元件的虛擬機器擴展集。 使用擴展集時，執行前端應用程式層的 VM 執行個體數目可以視回應客戶需求，或根據定義的排程來自動調整。 如需詳細資訊，請參閱[使用虛擬機器擴展集自動調整概觀][vmssautoscale-docs]。

如需其他延展性主題，請參閱 Azure Architecture Center 中的[延展性檢查清單][scalability]。

### <a name="security"></a>安全性

所有流入前端應用程式層的虛擬網路流量都受到網路安全性群組的保護。 規則會限制流量，以便只有前端應用程式層 VM 執行個體可以存取後端資料庫層。 不允許資料庫層的輸出網際網路流量。 若要降低攻擊使用量，請勿開啟任何直接的遠端管理連接埠。 如需詳細資訊，請參閱 [Azure 網路安全性群組][nsg-docs]。

若要檢視部署支付卡產業資料安全性標準 (PCI DSS 3.2) 的指導方針[符合規範的基礎結構][pci-dss]。 如需設計安全案例的一般指引，請參閱 [Azure 安全性文件][security]。

### <a name="resiliency"></a>災害復原

此案例結合使用「可用性區域」和「虛擬機器擴展集」，會使用 Azure 應用程式閘道和負載平衡器。 這兩個網路元件會將流量散發至已連線的 VM 執行個體，並包含健康情況探查，可確保流量只會散發到狀況良好的 VM。 在主動被動組態中，會設定兩個應用程式閘道執行個體，並使用區域備援負載平衡器。 此設定會讓網路資源和應用程式有彈性地處理問題，否則會中斷流量並影響使用者存取。

如需設計彈性案例的一般指引，請參閱[為 Azure 設計有彈性的應用程式][resiliency]。

## <a name="deploy-the-scenario"></a>部署案例

### <a name="prerequisites"></a>必要條件

- 您必須具有現有的 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

- 若要將 SQL Server 叢集部署到後端擴展集，您需要 Azure Active Directory (AD) Domain Services 中的網域。

### <a name="deploy-the-components"></a>部署元件

若要使用 Azure Resource Manager 範本部署此案例的核心基礎結構，請執行下列步驟。

<!-- markdownlint-disable MD033 -->

1. 選取 [部署至 Azure] 按鈕：<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Finfrastructure%2Fregulated-multitier-app.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. 等待 Azure 入口網站中的範本部署開啟，然後完成下列步驟：
   - 選擇 [新建] 資源群組，然後在文字方塊中提供名稱，例如 myWindowsscenario。
   - 從 [位置] 下拉式方塊選取區域。
   - 提供虛擬機器擴展集執行個體的使用者名稱和安全的密碼。
   - 檢閱條款及條件，然後按一下 [我同意上方所述的條款及條件]。
   - 選取 [購買] 按鈕。

<!-- markdownlint-enable MD033 -->

需要 15-20 分鐘的時間才能完成部署。

## <a name="pricing"></a>價格

為了探索執行此案例的成本，所有服務會在成本計算機中預先設定。 若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的流量。

我們根據執行應用程式的擴展集 VM 執行個體數目，提供了 3 個範例成本設定檔。

- [小型][small-pricing]：這個定價範例與兩個前端和兩個後端 VM 執行個體相互關聯。
- [中型][medium-pricing]：這個定價範例與 20 個前端和 5 個後端 VM 執行個體相互關聯。
- [大型][large-pricing]：這個定價範例與 100 個前端和 10 個後端 VM 執行個體相互關聯。

## <a name="related-resources"></a>相關資源

此案例中使用執行 Microsoft SQL Server 叢集的後端虛擬機器擴展集。 Cosmos DB 也可用來作為應用程式資料的可調整及安全資料庫層。 [Azure 虛擬網路服務][vnetendpoint-docs]端點可讓您將重要的 Azure 服務資源只放到您的虛擬網路保護。 在此案例中，VNet 端點可讓您保護前端應用程式層與 Cosmos DB 之間的流量。 如需詳細資訊，請參閱 [Azure Cosmos DB 概觀](/azure/cosmos-db/introduction)。

如需詳細的實作指南，請檢閱[使用 SQL Server 的多層式架構 (N-Tier) 應用程式參考架構][ntiersql-ra]。

<!-- links -->
[appgateway-docs]: /azure/application-gateway/overview
[architecture]: ./media/architecture-regulated-multitier-app.png
[autoscaling]: /azure/architecture/best-practices/auto-scaling
[availability]: ../../checklist/availability.md
[cloudwitness-docs]: /windows-server/failover-clustering/deploy-cloud-witness
[loadbalancer-docs]: /azure/load-balancer/load-balancer-overview
[nsg-docs]: /azure/virtual-network/security-overview
[ntiersql-ra]: /azure/architecture/reference-architectures/n-tier/n-tier-sql-server
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[scaleset-docs]: /azure/virtual-machine-scale-sets/overview
[sqlalwayson-docs]: /sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server
[vmssautoscale-docs]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview
[vnet-docs]: /azure/virtual-network/virtual-networks-overview
[vnetendpoint-docs]: /azure/virtual-network/virtual-network-service-endpoints-overview
[pci-dss]: /azure/security/blueprints/pcidss-iaaswa-overview
[dmz]: /azure/virtual-network/virtual-networks-dmz-nsg
[sql-linux]: /sql/linux/sql-server-linux-overview?view=sql-server-linux-2017
[cloud-witness]: /windows-server/failover-clustering/deploy-cloud-witness
[small-pricing]: https://azure.com/e/711bbfcbbc884ef8aa91cdf0f2caff72
[medium-pricing]: https://azure.com/e/b622d82d79b34b8398c4bce35477856f
[large-pricing]: https://azure.com/e/1d99d8b92f90496787abecffa1473a93

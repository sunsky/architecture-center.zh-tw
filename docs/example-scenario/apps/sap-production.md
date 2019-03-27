---
title: 使用 Oracle Database 來執行 SAP 生產環境工作負載
titleSuffix: Azure Example Scenarios
description: 在 Azure 中使用 Oracle Database 來執行 SAP 生產環境部署。
author: DharmeshBhagat
ms.date: 09/12/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: fasttrack, SAP, Windows, Linux
social_image_url: /azure/architecture/example-scenario/apps/media/architecture-sap-production.png
ms.openlocfilehash: a80d414f53cca474af587fce7c67d734eb223841
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58249593"
---
# <a name="running-sap-production-workloads-using-an-oracle-database-on-azure"></a>在 Azure 上使用 Oracle Database 來執行 SAP 生產環境工作負載

SAP 系統用來執行任務關鍵性商務應用程式。 任何中斷情況都會中斷重要程序，以致費用增加或喪失營收。 發生失敗時，必須有高用性和復原性的 SAP 基礎結構，才能避免這些結果。

建置高可用性的 SAP 環境時，必須排除系統架構和程序中的單一失敗點。 單一失敗點可能由網站失敗、系統元件錯誤，甚至是人為錯誤所造成。

此範例案例示範 Azure 中 Windows 或 Linux 虛擬機器 (VM) 上的 SAP 部署，以及高可用性 (HA) 的 Oracle 資料庫。 針對您的 SAP 部署，您可以根據您的需求使用不同大小的 VM。

## <a name="relevant-use-cases"></a>相關使用案例

其他相關的使用案例包括：

- 在 SAP 上執行的任務關鍵性工作負載。
- 非關鍵性 SAP 工作負載。
- 適用於 SAP 的測試環境，可模擬高可用性的環境。

## <a name="architecture"></a>架構

![Azure 中 SAP 生產環境的架構概觀][architecture]

此範例包含的高可用性組態適用於 Oracle 資料庫、SAP 中央服務，以及在不同虛擬機器上執行的多部 SAP 應用程式伺服器。 基於安全性考量，Azure 網路會使用[中樞輪輻拓撲](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)。 整個解決方案的資料流程如下所示：

1. 使用者可透過 SAP 使用者介面、網頁瀏覽器或其他用戶端工具 (例如 Microsoft Excel) 存取 SAP 系統。 ExpressRoute 連線可供從組織的內部部署網路，存取在 Azure 中執行的資源。
2. Azure 中的 ExpressRoute 會在 ExpressRoute 虛擬網路 (VNet) 閘道終止。 網路流量會透過在中樞 VNet 中建立的 ExpressRoute 閘道，路由傳送至閘道子網路。
3. 中樞 VNet 會對等互連至輪輻 VNet。 應用程式層子網路會裝載在可用性設定組中執行 SAP 的虛擬機器。
4. 身分識別管理伺服器會提供解決方案的驗證服務。
5. 系統管理員會使用 Jumpbox 來安全地管理在 Azure 中部署的資源。

### <a name="components"></a>元件

- 此案例中的[虛擬網路](/azure/virtual-network/virtual-networks-overview)用於在 Azure 中建立虛擬中樞輪輻拓撲。

- [虛擬機器](/azure/virtual-machines/windows/overview)會為每一層的解決方案提供計算資源。 每個虛擬機器叢集都會設定為[可用性設定組](/azure/virtual-machines/windows/regions-and-availability#availability-sets)。

- [ExpressRoute](/azure/expressroute/expressroute-introduction) 可透過連線提供者所建立的私人連線，將內部部署網路延伸至 Microsoft 雲端。

- [網路安全性群組 (NSG)](/azure/virtual-network/security-overview) 可將網路存取權限制為虛擬網路中的資源。 NSG 包含一些安全性規則，可根據來源或目的地 IP 位址、連接埠和通訊協定允許或拒絕網路流量。

- [資源群組](/azure/azure-resource-manager/resource-group-overview#resource-groups)可作為 Azure 資源的邏輯容器。

### <a name="alternatives"></a>替代項目

SAP 針對 Azure 環境中不同的作業系統、資料庫管理系統和 VM 類型組合，提供有彈性的選項。 如需完整清單，請參閱 [SAP 附註 1928533](https://launchpad.support.sap.com/#/notes/1928533)：「Azure 上的 SAP 應用程式︰支援的產品和 Azure VM 類型」。

## <a name="considerations"></a>考量

- 已針對在 Azure 中建置高可用性 SAP 環境定義建議做法。 如需詳細資訊，請參閱 [SAP NetWeaver 的 Azure 虛擬機器的高可用性架構和案例](/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)。 也請參閱 [Azure VM 上 SAP 應用程式的高可用性](/azure/virtual-machines/workloads/sap/high-availability-guide)。

- Oracle 資料庫也有適用於 Azure 的建議做法。 如需詳細資訊，請參閱[在 Azure 中設計和實作 Oracle 資料庫](/azure/virtual-machines/workloads/oracle/oracle-design)。

- Oracle 資料保護用於消除關鍵任務 Oracle 資料庫的單一失敗點。 如需詳細資訊，請參閱[在 Azure 中的 Linux 虛擬機器上實作 Oracle Data Guard](/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)。

- Microsoft Azure 提供的基礎結構服務可用於部署 SAP 產品與 Oracle 資料庫。 如需詳細資訊，請參閱[在 Azure 上針對 SAP 工作負載部署 Oracle DBMS](/azure/virtual-machines/workloads/sap/dbms_guide_oracle)。

## <a name="pricing"></a>價格

為了協助您探索執行此案例的成本，所有服務都會在以下成本計算機範例中預先設定。 若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的流量。

我們根據您預期接收的流量，提供了四個範例成本設定檔：

|大小|SAP|DB VM 類型|DB 儲存體|(A)SCS VM|(A)SCS 儲存體|應用程式 VM 類型|應用程式儲存體|Azure 價格計算機|
|----|----|-------|-------|-----|---|---|--------|---------------|
|小型|30000|DS13_v2|4xP20、1xP20|DS11_v2|1x P10|DS13_v2|1x P10|[小型](https://azure.com/e/45880ba0bfdf47d497851a7cf2650c7c)|
|中|70000|DS14_v2|6xP20、1xP20|DS11_v2|1x P10|4x DS13_v2|1x P10|[中型](https://azure.com/e/9a523f79591347ca9a48c3aaa1406f8a)|
大型|180000|E32s_v3|5xP30、1xP20|DS11_v2|1x P10|6x DS14_v2|1x P10|[大型](https://azure.com/e/f70fccf571e948c4b37d4fecc07cbf42)|
超大型|250000|M64s|6xP30、1xP30|DS11_v2|1x P10|10x DS14_v2|1x P10|[超大型](https://azure.com/e/58c636922cf94faf9650f583ff35e97b)|

> [!NOTE]
> 此定價是一份指南，只會指出 VM 和儲存體成本。 它會排除網路、備份儲存體和資料輸入/輸出費用。

- [小型](https://azure.com/e/45880ba0bfdf47d497851a7cf2650c7c)：小型系統由 VM 類型 DS13_v2 的資料庫伺服器所構成，具有 8 個 vCPU、56 GB RAM 和 112 GB 暫存儲存體，另外還有五個 512 GB 的進階儲存體磁碟。 使用 DS11_v2 VM 類型的 SAP Central Instance 伺服器具有 2 個 vCPU、14 GB RAM 和 28 GB 暫存儲存體。 一部 VM 類型 DS13_v2 的 SAP 應用程式伺服器，具有 8 個 vCPU、56 GB RAM 和 400 GB 暫存儲存體，另外還有一個 128 GB 的進階儲存體磁碟。

- [中型](https://azure.com/e/9a523f79591347ca9a48c3aaa1406f8a)：中型系統由 VM 類型 DS14_v2 的資料庫伺服器所構成，具有 16 個 vCPU、112 GB RAM 和 800 GB 暫存儲存體，另外還有七個 512 GB 的進階儲存體磁碟。 使用 DS11_v2 VM 類型的 SAP Central Instance 伺服器具有 2 個 vCPU、14 GB RAM 和 28 GB 暫存儲存體。 四部 VM 類型 DS13_v2 的 SAP 應用程式伺服器，具有 8 個 vCPU、56 GB RAM 和 400 GB 暫存儲存體，另外還有一個 128 GB 的進階儲存體磁碟。

- [大型](https://azure.com/e/f70fccf571e948c4b37d4fecc07cbf42)：大型系統由 VM 類型 E32s_v3 的資料庫伺服器所構成，具有 32 個 vCPU、256 GB RAM 和 800 GB 暫存儲存體，另外還有三個 512 GB 和一個 128 GB 的進階儲存體磁碟。 使用 DS11_v2 VM 類型的 SAP Central Instance 伺服器具有 2 個 vCPU、14 GB RAM 和 28 GB 暫存儲存體。 六部 VM 類型 DS14_v2 的 SAP 應用程式伺服器，具有 16 個 vCPU、112 GB RAM 和 224 GB 暫存儲存體，另外還有六個 128 GB 的進階儲存體磁碟。

- [超大型](https://azure.com/e/58c636922cf94faf9650f583ff35e97b)：超大型系統由 M64s VM 類型的資料庫伺服器所構成，具有 64 個 vCPU、1024 GB RAM 和 2000 GB 暫存儲存體，另外還有七個 1024 GB 的進階儲存體磁碟。 使用 DS11_v2 VM 類型的 SAP Central Instance 伺服器具有 2 個 vCPU、14 GB RAM 和 28 GB 暫存儲存體。 十部 VM 類型 DS14_v2 的 SAP 應用程式伺服器，具有 16 個 vCPU、112 GB RAM 和 224 GB 暫存儲存體，另外還有十個 128 GB 的進階儲存體磁碟。

## <a name="deployment"></a>部署

使用下列連結來部署此案例的基礎結構。

<!-- markdownlint-disable MD033 -->

<a
href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fapps%2Fsap-3tier-distributed-ora%2Fazuredeploy.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

<!-- markdownlint-enable MD033 -->

> [!NOTE]
> 在此部署期間不會安裝 SAP 和 Oracle。 您必須分別部署這些元件。

## <a name="related-resources"></a>相關資源

如需在 Azure 中執行 SAP 生產工作負載的其他資訊，請檢閱下列參考架構：

- [適用於 AnyDB 的 SAP NetWeaver](/azure/architecture/reference-architectures/sap/sap-netweaver)
- [SAP S/4HANA](/azure/architecture/reference-architectures/sap/sap-s4hana)
- [SAP HANA 大型執行個體](/azure/architecture/reference-architectures/sap/hana-large-instances)

<!-- links -->
[architecture]: media/architecture-sap-production.png

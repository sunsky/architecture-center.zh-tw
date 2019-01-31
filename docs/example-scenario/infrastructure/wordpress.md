---
title: 高擴充性且安全的 WordPress 網站
titleSuffix: Azure Example Scenarios
description: 針對媒體事件建置高度可調整且安全的 WordPress 網站。
author: david-stanford
ms.date: 09/18/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
social_image_url: /azure/architecture/example-scenario/infrastructure/media/secure-scalable-wordpress.png
ms.openlocfilehash: 6032247dce0d090885bc560d963f1e714d91f69c
ms.sourcegitcommit: 3b15d65e7c35a19506e562c444343f8467b6a073
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2019
ms.locfileid: "54908359"
---
# <a name="highly-scalable-and-secure-wordpress-website"></a>高擴充性且安全的 WordPress 網站

此範例案例適用於需要高擴充性且安全的 WordPress 安裝的公司。 此案例是以下列部署為基礎：使用於大型會議，而且擴充成功以符合工作階段送至網站的尖峰流量。

## <a name="relevant-use-cases"></a>相關使用案例

其他相關的使用案例包括：

- 導致流量激增的媒體事件。
- 使用 WordPress 作為其內容管理系統的部落格。
- 使用 WordPress 的商務或電子商務網站。
- 使用其他內容管理系統建置的網站。

## <a name="architecture"></a>架構

[![可擴充且安全的 WordPress 部署相關 Azure 元件的架構概觀](media/secure-scalable-wordpress.png)](media/secure-scalable-wordpress.png#lightbox)

此案例涵蓋使用 Ubuntu 網頁伺服器和 MariaDB 的 WordPress 安裝，這是可擴充且安全的安裝。 此案例中有兩個不同的資料流，第一個是使用者存取網站：

1. 使用者會透過 CDN 存取前端網站。
2. CDN 會使用 Azure 負載平衡器作為原點，並提取任何不是從這裡快取的資料。
3. Azure 負載平衡器會將要求散發到網頁伺服器的虛擬機器擴展集。
4. WordPress 應用程式會從 Maria DB 叢集中提取任何動態資訊，而所有靜態內容裝載在 Azure 檔案服務中。
5. SSL 金鑰會儲存在 Azure Key Vault 中。

第二個工作流程是作者如何參與新的內容：

1. 作者可安全地連線到公用 VPN 閘道。
2. VPN 驗證資訊會儲存在 Azure Active Directory 中。
3. 然後建立系統管理 Jumpbox 的連線。
4. 從系統管理 Jumpbox，作者就能夠連線到撰寫叢集的 Azure 負載平衡器。
5. Azure 負載平衡器會將流量散發到網頁伺服器的虛擬機器擴展集，而這些擴展集具有 Maria DB 叢集的寫入存取權。
6. 新的靜態內容回上傳至 Azure 檔案，並動態內容會寫入到 Maria DB 叢集中。
7. 然後，這些變更會透過 rsync 或主從式複寫來複寫到替代區域。

### <a name="components"></a>元件

- [Azure 內容傳遞網路 (CDN)](/azure/cdn/cdn-overview) 是可有效率地將 Web 內容傳遞給使用者的分散式伺服器網路。 CDN 會將快取的內容儲存在使用者附近存在點 (POP) 位置的邊緣伺服器上，以將延遲降至最低。
- [虛擬網路](/azure/virtual-network/virtual-networks-overview)可讓 VM 等資源安全地互相通訊，以及與網際網路和內部部署網路通訊。 虛擬網路會提供隔離與分割、篩選與路由流量，並允許位置之間的連線。 這兩個網路是透過 VNet 對等互連進行連線。
- [網路安全性群組 (NSG)](/azure/virtual-network/security-overview) 包含一些安全性規則，可根據來源或目的地 IP 位址、連接埠和通訊協定允許或拒絕輸入或輸出網路流量。 此案例中的虛擬網路會受到網路安全性群組規則保護，這些規則會限制應用程式元件之間的流量。
- [負載平衡器](/azure/load-balancer/load-balancer-overview)會根據規則和健康情況探查來散發輸入流量。 對於所有 TCP 和 UDP 應用程式，負載平衡器可提供低延遲和高輸送量，且最多可相應增加為數百萬個流程。 此案例會使用負載平衡器，將流量從內容傳遞網路散發到前端網頁伺服器。
- [虛擬機器擴展集][docs-vmss]可讓您建立和管理一組負載平衡的相同 VM。 VM 執行個體的數目可以自動增加或減少，以因應需求或已定義的排程。 此案例中使用兩個不同的虛擬機器擴展集 - 一個適用於提供內容的前端網頁伺服器，另一個則適用於用來撰寫新內容的前端網頁伺服器。
- [Azure 檔案服務](/azure/storage/files/storage-files-introduction)會在雲端提供完全受控的檔案共用，以裝載此案例中的所有 WordPress 內容，讓所有 VM 都能存取資料。
- [Azure Key Vault](/azure/key-vault/key-vault-overview) 用來儲存密碼、憑證和金鑰，並嚴密控制其存取權。
- [Azure Active Directory (Azure AD)](/azure/active-directory/fundamentals/active-directory-whatis) 是多租用戶雲端式目錄和身分識別管理服務。 在此案例中，Azure AD 會為網站與 VPN 通道提供驗證服務。

### <a name="alternatives"></a>替代項目

- [適用於 Linux 的 SQL Server](/azure/virtual-machines/linux/sql/sql-server-linux-virtual-machines-overview) 可取代 MariaDB 資料存放區。
- 如果您偏好完全受控的解決方案，[適用於 MySQL 的 Azure 資料庫](/azure/mysql/overview)即可取代 MariaDB 資料存放區。

## <a name="considerations"></a>考量

### <a name="availability"></a>可用性

此案例中的 VM 執行個體會部署於多個區域，其 WordPress 內容的資料會透過 RSYNC 在兩個區域間複寫，而 MariaDB 叢集則採用主從式複寫。

如需其他可用性主題，請參閱 Azure Architecture Center 中的[可用性檢查清單][availability]。

### <a name="scalability"></a>延展性

此案例會在每個區域中使用兩個前端網頁伺服器叢集的虛擬機器擴展集。 使用擴展集時，執行前端應用程式層的 VM 執行個體數目可以視回應客戶需求，或根據定義的排程來自動調整。 如需詳細資訊，請參閱[使用虛擬機器擴展集自動調整概觀][docs-vmss-autoscale]。

後端是可用性設定組中的 MariaDB 叢集。 如需詳細資訊，請參閱 [MariaDB 叢集教學課程][mariadb-tutorial]。

如需其他延展性主題，請參閱 Azure Architecture Center 中的[延展性檢查清單][scalability]。

### <a name="security"></a>安全性

所有流入前端應用程式層的虛擬網路流量都受到網路安全性群組的保護。 規則會限制流量，以便只有前端應用程式層 VM 執行個體可以存取後端資料庫層。 不允許資料庫層的輸出網際網路流量。 若要降低攻擊使用量，請勿開啟任何直接的遠端管理連接埠。 如需詳細資訊，請參閱 [Azure 網路安全性群組][docs-nsg]。

如需設計安全案例的一般指引，請參閱 [Azure 安全性文件][security]。

### <a name="resiliency"></a>災害復原

此案例結合使用多個區域、資料複寫和虛擬機器擴展集，所以會使用 Azure 負載平衡器。 這些網路元件會將流量散發至已連線的 VM 執行個體，並包含健康情況探查，可確保流量只會散發到狀況良好的 VM。 上述所有網路元件都會透過 CDN 成為前端。 這會讓網路資源和應用程式有彈性地處理問題，否則會中斷流量並影響使用者存取。

如需設計彈性案例的一般指引，請參閱[為 Azure 設計有彈性的應用程式][resiliency]。

## <a name="pricing"></a>價格

為了探索執行此案例的成本，所有服務會在成本計算機中預先設定。 若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的流量。

我們根據上面所提供的架構圖，提供了預先設定的[成本設定檔][pricing]。 若要針對您的使用情況設定定價計算機，請考量下列幾個主要事項：

- 您預期有多少流量 (以 GB/月表示)？ 流量會對您的成本造成最大影響，因為它會影響必須在虛擬機器擴展集中呈現資料的 VM 數目。 此外，它會直接與透過 CDN 呈現的資料量相互關聯。
- 您即將在您的網站上撰寫多少新資料？ 寫入至您網站的新資料會與跨區域鏡像處理多少資料相互關聯。
- 您有多少動態內容？ 多少靜態內容？ 動態和靜態內容間的差異會影響必須從資料庫層擷取多少資料，以及要在 CDN 中快取多少資料。

<!-- links -->
[architecture]: ./media/architecture-secure-scalable-wordpress.png
[mariadb-tutorial]: /azure/virtual-machines/linux/classic/mariadb-mysql-cluster
[docs-vmss]: /azure/virtual-machine-scale-sets/overview
[docs-vmss-autoscale]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview
[docs-nsg]: /azure/virtual-network/security-overview
[security]: /azure/security/
[availability]: ../../checklist/availability.md
[resiliency]: /azure/architecture/resiliency/
[scalability]: /azure/architecture/checklist/scalability
[pricing]: https://azure.com/e/a8c4809dab444c1ca4870c489fbb196b

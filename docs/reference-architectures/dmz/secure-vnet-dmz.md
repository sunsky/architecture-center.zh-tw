---
title: 實作 Azure 和網際網路之間的 DMZ
description: 如何在 Azure 中使用網際網路存取實作安全的混合式網路架構。
author: telmosampaio
ms.date: 11/23/2016
pnp.series.title: Network DMZ
pnp.series.next: nva-ha
pnp.series.prev: secure-vnet-hybrid
cardTitle: DMZ between Azure and the Internet
ms.openlocfilehash: c88545b1fcae49b413e7e2b6ac5bd92d3fd3456d
ms.sourcegitcommit: c441fd165e6bebbbbbc19854ec6f3676be9c3b25
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2018
---
# <a name="dmz-between-azure-and-the-internet"></a>Azure 和網際網路之間的 DMZ

此參考架構顯示的安全混合式網路，可將內部部署網路擴充至 Azure 並接受網際網路流量。 

[![0]][0] 

下載這個架構的 [Visio 檔案][visio-download]。

此參考架構由[實作 Azure 和內部部署資料中心之間的 DMZ][implementing-a-secure-hybrid-network-architecture] 中所述的架構擴充而來。 除了用於處理來自內部網路流量的私人 DMZ，還加入了可處理網際網路流量的公用 DMZ 

此架構的典型使用案例包括：

* 部分工作負載在內部部署中執行、部分在 Azure 中執行的混合式應用程式。
* 負責路由傳送內部部署與網際網路的連入流量的 Azure 基礎結構。

## <a name="architecture"></a>架構

此架構由下列元件組成。

* **公用 IP 位址 (PIP)**。 公用端點的 IP 位址。 連線到網際網路的外部使用者可以透過此位址存取此系統。
* **網路虛擬設備 (NVA)**。 此架構有一個個別的 NVA 集區，用於處理來自網際網路的流量。
* **Azure 負載平衡器**。 來自網際網路的所有傳入要求都要通過負載平衡器，並散發到公用 DMZ 中的 NVA。
* **公用 DMZ 輸入子網路**。 此子網路會接受來自 Azure 負載平衡器的要求。 傳入要求會傳遞至公用 DMZ 中的其中一個 NVA。
* **公用 DMZ 輸出子網路**。 NVA 核准的要求會通過此子網路傳遞至 Web 層的內部負載平衡器。

## <a name="recommendations"></a>建議

下列建議適用於大部分的案例。 除非您有特定的需求會覆寫它們，否則請遵循下列建議。 

### <a name="nva-recommendations"></a>NVA 建議

一組 NVA 用於來自網際網路的流量，另一組用於來自內部部署的流量。 兩者使用同一組 NVA 會造成安全性風險，因為在兩組網路流量之間沒有安全性界線。 使用個別 NVA 可降低檢查安全性規則的複雜度，並清楚哪些規則對應到每個傳入網路要求。 一組 NVA 僅實作於網際網路流量的規則，而另一組 NVA 僅實作內部部署流量的規則。

加入第 7 層 NVA 來終止 NVA 層級的應用程式連線，並維持與後端層的相容性。 這可確保對稱的連線，亦即來自後端層的回應流量會透過 NVA 傳回。  

### <a name="public-load-balancer-recommendations"></a>公用負載平衡器建議

為了延展性和可用性，在[可用性設定組][availability-set]中部署公用 DMZ NVA，並使用[網際網路對向負載平衡器][load-balancer]分散可用性設定組中所有 NVA 的網際網路要求。  

負載平衡器設定為只接受網際網路流量所需的通訊埠上的要求。 例如，限制輸入 HTTP 要求在連接埠 80，輸出 HTTPS 要求在連接埠 443。

## <a name="scalability-considerations"></a>延展性考量

即使您的架構一開始只需要公用 DMZ 中有單一 NVA，建議您從一開始就在公用 DMZ 前放置負載平衡器。 未來如果需要，這將更容易擴充至多個 NVA。

## <a name="availability-considerations"></a>可用性考量

網際網路對向負載平衡器需要公用 DMZ 輸入子網路中的每個 NVA 皆實作[健康情況探查][lb-probe]。 此端點無法回應健康情況探查的 NVA 會被視為無法使用，負載平衡器會將要求導向相同可用性設定組中的其他 NVA。 請注意，如果所有 NVA 回應都失敗，您的應用程式就會失敗，所以一定要設定監視，在狀況良好的 NVA 執行個體數目低於定義的臨界值時警示 DevOps。

## <a name="manageability-considerations"></a>管理性考量

對公用 DMZ 中 NVA 的所有監視和管理，應該由管理子網路中的 jumpbox 執行。 如[實作 Azure 與內部部署資料中心之間的 DMZ][implementing-a-secure-hybrid-network-architecture] 中所述，為了限制存取權，定義單一網路透過閘道從內部部署網路路由傳送至 jumpbox。

如果從內部部署網路到 Azure 的閘道連線關閉，藉由部署公用 IP 位址、將它加入 jumpbox、然後從網際網路登入，仍可連線至 jumpbox。

## <a name="security-considerations"></a>安全性考量

此參考架構實作多個安全性層級：

* 網際網路對向負載平衡器會將要求導向輸入公用 DMZ 子網路中的 NVA，而且只在應用程式所需的連接埠。
* 藉著封鎖 NSG 規則範圍之外的要求，輸入和輸出公用 DMZ 子網路的 NSG 規則可防止 NVA 受到危害。
* NVA 的 NAT 路由設定會將連接埠 80 和 443 上的傳入要求導向 Web 層負載平衡器，但會忽略其他所有通訊埠上的要求。

您應該記錄所有連接埠上的所有傳入要求。 定期稽核記錄，留意不在預期參數範圍之內的要求，因為這些可能表示入侵嘗試。

## <a name="solution-deployment"></a>解決方案部署

在 [GitHub][github-folder] 中有實作這些建議的參考架構部署。 可以使用 Windows 或 Linux VM 部署參考架構，請遵循下列指示：

1. 按一下下方的按鈕：<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fdmz%2Fsecure-vnet-dmz%2FvirtualNetwork.azuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
2. 一旦連結已在 Azure 入口網站中開啟，您必須輸入部分設定的值：
   * **資源群組**名稱已在參數檔案中定義，因此請在文字方塊中選取 [新建] 並輸入 `ra-public-dmz-network-rg`。
   * 從 [位置] 下拉式方塊選取區域。
   * 請勿編輯 [範本的根 URI] 或 [參數根 URI] 文字方塊。
   * 從下拉式清單方塊中選取 [作業系統類型]，選取 [Windows] 或 [Linux]。
   * 檢閱條款和條件，然後按一下 [我同意上方所述的條款及條件] 核取方塊。
   * 按一下 [購買] 按鈕。
3. 等待部署完成。
4. 按一下下方的按鈕：<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fdmz%2Fsecure-vnet-dmz%2Fworkload.azuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
5. 一旦連結已在 Azure 入口網站中開啟，您必須輸入部分設定的值：
   * **資源群組**名稱已在參數檔案中定義，因此請在文字方塊中選取 [新建] 並輸入 `ra-public-dmz-wl-rg`。
   * 從 [位置] 下拉式方塊選取區域。
   * 請勿編輯 [範本的根 URI] 或 [參數根 URI] 文字方塊。
   * 檢閱條款和條件，然後按一下 [我同意上方所述的條款及條件] 核取方塊。
   * 按一下 [購買] 按鈕。
6. 等待部署完成。
7. 按一下下方的按鈕：<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fdmz%2Fsecure-vnet-dmz%2Fsecurity.azuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
8. 一旦連結已在 Azure 入口網站中開啟，您必須輸入部分設定的值：
   * [資源群組] 名稱已於參數檔中定義，因此，請選取 [使用現有的] 並在文字方塊中輸入 `ra-public-dmz-network-rg`。
   * 從 [位置] 下拉式方塊選取區域。
   * 請勿編輯 [範本的根 URI] 或 [參數根 URI] 文字方塊。
   * 檢閱條款和條件，然後按一下 [我同意上方所述的條款及條件] 核取方塊。
   * 按一下 [購買] 按鈕。
9. 等待部署完成。
10. 參數檔案中有硬式編碼的所有 VM 的系統管理員使用者名稱和密碼，強烈建議您立即變更這兩項。 在 Azure 入口網站中選取部署中的每個 VM，然後按一下 [支援與疑難排解] 刀鋒視窗中的 [重設密碼]。 選取 [模式] 下拉式清單方塊中的 [重設密碼]，然後選取新的[使用者名稱] 和 [密碼]。 按一下 [更新] 按鈕以儲存。


[availability-set]: /azure/virtual-machines/virtual-machines-windows-manage-availability
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/dmz/secure-vnet-dmz

[implementing-a-secure-hybrid-network-architecture]: ./secure-vnet-hybrid.md
[iptables]: https://help.ubuntu.com/community/IptablesHowTo
[lb-probe]: /azure/load-balancer/load-balancer-custom-probe-overview
[load-balancer]: /azure/load-balancer/load-balancer-Internet-overview
[network-security-group]: /azure/virtual-network/virtual-networks-nsg

[visio-download]: https://archcenter.blob.core.windows.net/cdn/dmz-reference-architectures.vsdx


[0]: ./images/dmz-public.png "安全混合式網路架構"

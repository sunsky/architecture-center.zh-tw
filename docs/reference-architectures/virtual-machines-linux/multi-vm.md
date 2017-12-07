---
title: "在 Azure 上執行負載平衡的 VM 以獲得延展性和可用性"
description: "如何在 Azure 上執行多個 Linux VM 以獲得延展性和可用性。"
author: telmosampaio
ms.date: 11/16/2017
pnp.series.title: Linux VM workloads
pnp.series.next: n-tier
pnp.series.prev: single-vm
ms.openlocfilehash: b1b3c94524d50d05c90b46d26cab54fea8c8061a
ms.sourcegitcommit: 115db7ee008a0b1f2b0be50a26471050742ddb04
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2017
---
# <a name="run-load-balanced-vms-for-scalability-and-availability"></a>執行負載平衡的 VM 以獲得延展性和可用性

此參考架構顯示一組經過證實的作法，可用來在負載平衡器後方的擴展集中執行多個 Linux 虛擬機器 (VM)，以提升可用性和延展性。 此架構可用於任何無狀態的工作負載 (例如網頁伺服器)，而且是用於部署多層式架構應用程式的基礎。 [**部署這個解決方案**。](#deploy-the-solution)

![[0]][0]

下載這個架構的 [Visio 檔案][visio-download]。

## <a name="architecture"></a>架構

此架構是建置在[單一 VM 參考架構][single-vm]上。 那些建議也適用於此架構。

在此架構中，會將工作負載散發到多個 VM 執行個體。 有個單一公用 IP 位址，而且會使用負載平衡器來將網際網路流量散發到 VM。 此架構可用於單層式架構應用程式，例如無狀態的 Web 應用程式。

此架構具有下列元件：

* **資源群組。** [資源群組][resource-manager-overview]可用來將資源組合在一起，讓它們可以依存留期、擁有者或其他準則管理。
* **虛擬網路 (VNet) 和子網路。** 每部 Azure VM 都會部署到可以分割成多個子網路的 VNet。
* **Azure Load Balancer**。 [負載平衡器][load-balancer]會將連入網際網路要求散發到 VM 執行個體。 
* **公用 IP 位址**。 負載平衡器需要公用 IP 位址才能接收網際網路流量。
* **VM 擴展集**。 [VM 擴展集][vm-scaleset]是一組用來裝載工作負載且完全相同的 VM。 擴展集允許以手動方式或根據預先定義的規則，自動相應縮小或放大 VM 數目。
* **可用性設定組**。 [可用性設定組][availability-set]包含 VM，可使 VM 符合較高[服務等級協定 (SLA)][vm-sla] 的資格。 若要套用較高的 SLA，可用性設定組必須至少包含兩個 VM。 可用性設定組會隱含於擴展集中。 如果您在擴展集之外建立 VM，就需要個別建立可用性設定組。
* **受控磁碟**。 Azure 受控磁碟會管理適用於 VM 磁碟的虛擬硬碟 (VHD) 檔案。 
* **儲存體**。 建立 Azure 儲存體帳戶以保存 VM 的診斷記錄。

## <a name="recommendations"></a>建議

此處所述的架構不見得完全與您的需求相符。 請使用以下建議作為起點。 

### <a name="availability-and-scalability-recommendations"></a>可用性和延展性建議

適用於可用性和延展性的選項是使用[虛擬機器擴展集][vmss]。 VM 擴展集可協助您部署及管理一組完全相同的 VM。 擴展集支援根據效能計量自動調整規模。 當 VM 上的負載增加時，就會將其他 VM 自動新增到負載平衡器。 如果您需要快速相應放大 VM 數目，或需要自動調整規模，請考慮擴展集。

根據預設，擴展集會使用「過度佈建」，這表示擴展集一開始會佈建比您所要求更多的 VM，然後刪除額外的 VM。 這可改善佈建 VM 時的整體成功率。 若您未使用[受控磁碟](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-managed-disks)，則建議每個儲存體帳戶不要超過 20 部已啟用過度佈建的 VM，且不要超過 40 部已停用過度佈建的 VM。

有兩種基本方法可用來設定擴展集中部署的 VM：

- 佈建 VM 之後，使用擴充功能來設定它。 透過此方法，新的 VM 執行個體可能需要較長的時間來啟動不具擴充功能的 VM。

- 利用自訂的磁碟映像來部署[受控磁碟](/azure/storage/storage-managed-disks-overview)。 這個選項可能會更快速地部署。 不過，它會要求您讓映像保持最新狀態。

如需其他考量，請參閱[擴展集的設計考量][vmss-design]。

> [!TIP]
> 使用任何自動調整規模解決方案時，事前也要利用生產層級的工作負載來進行測試。

如果您未使用擴展集，請考慮至少使用可用性設定組。 在可用性設定組中至少建立兩部 VM，以支援[適用於 Azure VM 的可用性 SLA][vm-sla]。 Azure 負載平衡器也會要求已負載平衡的 VM 屬於同一個可用性設定組。

每個 Azure 訂用帳戶都已經有預設限制，包括每個區域的 VM 最大數目。 您可以藉由提出支援要求來提高限制。 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束][subscription-limits]。

### <a name="network-recommendations"></a>網路建議

將 VM 部署到同一個子網路中。 不要直接將 VM 公開至網際網路，而是改為每個 VM 提供私人 IP 位址。 用戶端會使用負載平衡器的公用 IP 位址來連線。

若您需要登入負載平衡器後方的 VM，請考慮使用您可以登入的公用 IP 位址，來新增單一 VM 作為 jumpbox (也稱作防禦主機)。 然後從 Jumpbox 登入負載平衡器後方的 VM。 或者，您也可以設定負載平衡器的輸入網路位址轉譯 (NAT) 規則。 不過，當您裝載多層式架構工作負載或多個工作負載時，較好的解決方案是具備 Jumpbox。

### <a name="load-balancer-recommendations"></a>負載平衡器建議

將可用性設定組中的所有 VM 新增至負載平衡器的後端位址集區。

定義負載平衡器規則，以將網路流量導向至 VM。 例如，若要啟用 HTTP 流量，請建立一個規則，將前端設定的連接埠 80 對應至後端位址集區的連接埠 80。 當用戶端將 HTTP 要求傳送到連接埠 80 時，負載平衡器會藉由使用[雜湊演算法][load-balancer-hashing] (其中包含來源 IP 位址) 來選取後端 IP 位址。 如此一來，就會將用戶端要求散發到所有 VM。

若要將流量路由傳送到特定的 VM，請使用 NAT 規則。 例如，若要啟用 RDP 到 VM，可為每個 VM 建立不同的 NAT 規則。 每個規則都應該將不同的連接埠號碼對應至連接埠 3389 (RDP 的預設連接埠)。 例如，針對 "VM1" 使用連接埠 50001、針對 "VM2" 使用連接埠 50002，依此類推。 將 NAT 規則指派給 VM 上的 NIC。

### <a name="storage-account-recommendations"></a>儲存體帳戶建議

我們建議搭配使用[受控磁碟](/azure/storage/storage-managed-disks-overview)與[進階儲存體][premium]。 受控磁碟不需要儲存體帳戶。 您只需指定磁碟的大小和類型，它就會以高度可用的資源方式進行部署。

若您使用的是非受控的磁碟，請針對每部 VM 建立個別的 Azure 儲存體帳戶來保存虛擬硬碟 (VHD)，以避免達到儲存體帳戶的每秒輸入/輸出作業 [(IOPS) 限制][vm-disk-limits]。

建立一個儲存體帳戶以供診斷記錄使用。 所有 VM 都可共用這個儲存體帳戶。 這可以是使用標準磁碟之未受管理的儲存體帳戶。

## <a name="availability-considerations"></a>可用性考量

可用性設定組可讓您的應用程式能以更彈性的方式處理已計劃或尚未計劃的維護事件。

* 「已計劃的維護」會在 Microsoft 更新基礎平台時發生，有時會導致 VM 重新啟動。 Azure 確保可用性設定組中的 VM 不會全部同時重新啟動。 當其他 VM 正在重新啟動時，至少會讓其中一個保留執行狀態。
* 「尚未計劃的維護」會在硬體故障時發生。 Azure 確保可用性設定組中的 VM 會跨多個伺服器機架進行佈建。 這有助於降低對於硬體故障、網路中斷、電源中斷等的影響。

如需詳細資訊，請參閱[管理虛擬機器的可用性][availability-set]。 下列影片也提供適合可用性設定組的概觀：[如何設定可用性設定組來調整 VM][availability-set-ch9]。

> [!WARNING]
> 請確定會在佈建 VM 時設定可用性設定組。 目前沒有任何方法可在佈建 VM 之後，將資源管理員 VM 新增至可用性設定組。

負載平衡器會使用[健康情況探查][health-probes]來監視 VM 執行個體的可用性。 如果探查無法在逾時期間內連線到執行個體，負載平衡器就會停止將流量傳送到該 VM。 不過，負載平衡器將會繼續探查，而且如果 VM 再次變成可用，負載平衡器就會繼續將流量傳送到該 VM。

以下是關於負載平衡器健康情況探查的建議：

* 探查可以測試 HTTP 或 TCP。 如果您的 VM 執行 HTTP 伺服器，請建立 HTTP 探查。 否則，建立 TCP 探查。
* 針對 HTTP 探查，指定 HTTP 端點的路徑。 探查會檢查來自此路徑的 HTTP 200 回應。 這可以是根路徑 ("/")，或是實作一些自訂邏輯來檢查應用程式健康情況的健康情況監視端點。 端點必須允許匿名的 HTTP 要求。
* 探查會從[已知的 IP 位址][health-probe-ip] 168.63.129.16 傳送出來。 請確定您並未在任何防火牆原則或網路安全性群組 (NSG) 規則中封鎖往返此 IP 位址的流量。
* 使用[健康情況探查記錄][health-probe-log]來檢視健康情況探查的狀態。 能夠針對每個負載平衡器登入 Azure 入口網站。 記錄會寫入 Azure Blob 儲存體。 記錄會顯示後端有多少個 VM 因探查回應失敗而不會接收網路流量。

## <a name="manageability-considerations"></a>管理性考量

如果有多個 VM，請務必將程序自動化，如此一來，它們就會更可靠且可重複。 您可以使用 [Azure 自動化][azure-automation]，來將部署、OS 修補及其他工作自動化。

## <a name="security-considerations"></a>安全性考量

虛擬網路是 Azure 中的流量隔離界限。 某一個 VNet 中的 VM 無法直接與不同 VNet 中的 VM 通訊。 除非您建立[網路安全性群組][nsg] (NSG) 來限制流量，否則，相同 VNet 中的 VM 可以彼此通訊。 如需詳細資訊，請參閱 [Microsoft 雲端服務和網路安全性][network-security]。

針對傳入的網際網路流量，負載平衡器規則會定義哪些流量可以連線到後端。 不過，負載平衡器規則不支援 IP 安全清單，因此，如果您想要將特定的公用 IP 位址新增至安全清單，請將 NSG 新增至子網路。

## <a name="deploy-the-solution"></a>部署解決方案

適用於此架構的部署可在 [GitHub][github-folder] 上取得。 它會部署下列各項：

  * 包含 VM 且具有名為 **web** 之單一子網路的虛擬網路。
  * VM 擴展集，其中包含執行最新版 Ubuntu 16.04.3 LTS 的 VM。 已啟用自動調整規模。
  * 位於 VM 擴展集前方的負載平衡器。
  * 含有傳入規則的 NSG，可允許 HTTP 流量通往 VM 擴展集。

### <a name="prerequisites"></a>必要條件

在您可以將參考架構部署到自己的訂用帳戶之前，必須執行下列步驟。

1. 複製、派生或下載適用於 [AzureCAT 參考架構][ref-arch-repo] GitHub 存放庫的 zip 檔案。

2. 確定您已在電腦上安裝 Azure CLI 2.0。 如需 CLI 安裝指示，請參閱[安裝 Azure CLI 2.0][azure-cli-2]。

3. 安裝 [Azure 建置組塊][azbb] npm 封裝。

4. 從命令提示字元、bash 提示字元或 PowerShell 提示字元中，使用下列其中一個命令登入 Azure 帳戶，並遵循提示進行。

  ```bash
  az login
  ```

### <a name="deploy-the-solution-using-azbb"></a>使用 azbb 部署解決方案

若要部署範例單一 VM 工作負載，請依照下列步驟執行：

1. 瀏覽至您在上述必要條件步驟中所下載存放庫的 `virtual-machines\multi-vm\parameters\linux` 資料夾。

2. 開啟 `multi-vm-v2.json` 檔案，然後在引號之間輸入使用者名稱和 SSH 金鑰，如下所示，然後儲存檔案。

  ```bash
  "adminUsername": "",
  "sshPublicKey": "",
  ```

3. 執行 `azbb` 以部署 VM，如下所示。

  ```bash
  azbb -s <subscription_id> -g <resource_group_name> -l <location> -p multi-vm-v2.json --deploy
  ```

如需部署此範例參考架構的詳細資訊，請瀏覽我們的 [GitHub 存放庫][git]。

<!-- links -->

[availability-set]: /azure/virtual-machines/virtual-machines-windows-manage-availability
[availability-set-ch9]: https://channel9.msdn.com/Series/Microsoft-Azure-Fundamentals-Virtual-Machines/08
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[azure-automation]: /azure/automation/automation-intro
[azure-cli]: /azure/virtual-machines-command-line-tools
[azure-cli-2]: /azure/install-azure-cli?view=azure-cli-latest
[git]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/multi-vm
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/multi-vm
[health-probe-log]: /azure/load-balancer/load-balancer-monitor-log
[health-probes]: /azure/load-balancer/load-balancer-overview#load-balancer-features
[health-probe-ip]: /azure/virtual-network/virtual-networks-nsg#special-rules
[load-balancer]: /azure/load-balancer/load-balancer-get-started-internet-arm-cli
[load-balancer-hashing]: /azure/load-balancer/load-balancer-overview#load-balancer-features
[naming-conventions]: ../../best-practices/naming-conventions.md
[network-security]: /azure/best-practices-network-security
[nsg]: /azure/virtual-network/virtual-networks-nsg
[premium]: /azure/storage/common/storage-premium-storage
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview 
[runbook-gallery]: /azure/automation/automation-runbook-gallery#runbooks-in-runbook-gallery
[single-vm]: single-vm.md
[subscription-limits]: /azure/azure-subscription-service-limits
[visio-download]: https://archcenter.azureedge.net/cdn/vm-reference-architectures.vsdx
[vm-disk-limits]: /azure/azure-subscription-service-limits#virtual-machine-disk-limits
[vm-scaleset]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview
[vm-sizes]: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_2/
[vmss]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview
[vmss-design]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview
[vmss-quickstart]: https://azure.microsoft.com/documentation/templates/?term=scale+set
[0]: ./images/multi-vm-diagram.png "Azure 中多個 VM 的解決方案架構是由具有兩個 VM 和一個負載平衡器的可用性設定組所組成"

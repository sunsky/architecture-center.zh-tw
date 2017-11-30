---
title: "在 Azure 上執行 Windows VM"
description: "如何在 Azure 上執行 VM，並注意延展性、恢復能力、管理性和安全性。"
author: telmosampaio
ms.date: 09/06/2017
pnp.series.title: Windows VM workloads
pnp.series.next: multi-vm
pnp.series.prev: ./index
ms.openlocfilehash: adedcd797208e52be58fc0ab0f37fc3da1723484
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="run-a-windows-vm-on-azure"></a>在 Azure 上執行 Windows VM

此參考架構顯示一組經過證實的作法，可用來在 Azure 上執行 Windows 虛擬機器 (VM)。 其中包括適用於佈建 VM，以及網路和存放元件的建議。 此架構可用來執行單一執行個體，而且是適用於更複雜架構 (例如多層式架構應用程式) 的基礎。 [**部署這個解決方案**。](#deploy-the-solution)

![[0]][0]

下載這個架構的 [Visio 檔案][visio-download]。

## <a name="architecture"></a>架構

在 Azure 中佈建 VM 牽涉的移動組建比 VM 本身更多。 您需要考量一些計算、網路及儲存體元素。

* **資源群組。** [*資源群組*][resource-manager-overview]是保存相關資源的容器。 您通常會在解決方案中，根據資源的存留期以及將管理資源的人員，針對不同的資源建立資源群組。 對於單一 VM 工作負載，您可以針對所有資源建立單一資源群組。
* **VM**。 您可以從已發佈的映像清單，或從您上傳至 Azure Blob 儲存體的虛擬硬碟 (VHD) 檔案佈建 VM。
* **作業系統磁碟。** 作業系統磁碟是儲存在 [Azure 儲存體][azure-storage]中的 VHD。 這表示即使主機電腦關閉，它仍持續存在。
* **暫存磁碟。** VM 是使用暫存磁碟 (Windows 上的 `D:` 磁碟機) 來建立。 此磁碟會儲存在主機電腦的實體磁碟機上。 它不會儲存於 Azure 儲存體，而且可能在重新開機期間和其他 VM 生命週期事件中遭到刪除。 僅將此磁碟使用於暫存資料，例如分頁檔或交換檔。
* **資料磁碟。** [資料磁碟][data-disk]是用於應用程式資料的持續性 VHD。 資料磁碟會儲存在 Azure 儲存體中，例如作業系統磁碟。
* **虛擬網路 (VNet) 和子網路。** 在 Azure 中的每個 VM 都會部署到 VNet 中，而虛擬網路會進一步分成數個子網路。
* **公用 IP 位址。** 必須要有公用 IP 位址才能與 VM&mdash; 進行通訊，例如透過遠端桌面 (RDP)。
* **網路介面 (NIC)**。 NIC 可讓 VM 與虛擬網路通訊。
* **網路安全性群組 (NSG)**。 [NSG][nsg] 是用來允許/拒絕對子網路的網路流量。 您可以將 NSG 與獨立的 NIC 或子網路關聯。 如果您將它與子網路關聯，該 NSG 規則會套用至子網路中的所有 VM。
* **診斷。** 診斷記錄對於管理和針對 VM 進行疑難排解十分重要。

## <a name="recommendations"></a>建議

此架構顯示在 Azure 中執行 Windows VM 的基準建議。 但是，我們不建議針對關鍵任務工作負載使用單一 VM，因為它會建立單一失敗點。 若要擁有較高的可用性，您必須在[可用性設定組][availability-set]中部署多個 VM。 如需詳細資訊，請參閱[在 Azure 上執行多個 VM][multi-vm]。 

### <a name="vm-recommendations"></a>VM 建議

Azure 提供許多不同的虛擬機器大小，但是我們建議使用 DS 和 GS 系列，因為這些機器大小支援[進階儲存體][premium-storage]。 選取其中一個機器大小，除非您有高效能運算等特殊工作負載。 如需詳細資訊，請參閱[虛擬機器的大小][virtual-machine-sizes]。 

如果您將現有的工作負載移至 Azure，則從最符合您內部部署伺服器的 VM 大小開始。 然後根據 CPU、記憶體和每秒的磁碟輸入/輸出作業 (IOPS) 測量您的實際工作負載效能，並視需要調整大小。 如果您的 VM 需要多個 NIC，請注意 NIC 的最大數目是 [VM 大小][vm-size-tables]的函式。   

當您佈建 VM 和其他資源時，必須指定一個區域。 一般而言，選擇最接近您的內部使用者或客戶的區域。 不過，並非所有 VM 大小在所有區域都可供使用。 如需詳細資訊，請參閱[依區域提供的服務][services-by-region]。 若要查看特定區域中可用的 VM 大小清單，請執行下列 Azure 命令列介面 (CLI) 命令：

```
az vm list-sizes --location <location>
```

如需選擇已發佈 VM 映像的相關資訊，請參閱[在 Azure 中使用 Powershell 或 CLI 瀏覽並選取 Windows 虛擬機器映像][select-vm-image]。

啟用監視和診斷，包括基本健全狀況度量、診斷基礎結構記錄檔及[開機診斷][boot-diagnostics]。 如果您的 VM 進入無法開機的狀態，開機診斷能協助您診斷開機失敗。 如需詳細資訊，請參閱[啟用監視和診斷][enable-monitoring]。  

### <a name="disk-and-storage-recommendations"></a>磁碟和儲存體建議

為了達到最佳的磁碟 I/O 效能，我們建議使用[進階儲存體][premium-storage]，這會將資料儲存在固態硬碟 (SSD)。 成本是依佈建的磁碟大小而定。 IOPS 和輸送量也取決於磁碟大小，因此當您佈建磁碟時，請考慮以下三個因素 (容量、IOPS 和輸送量)。 

我們也建議使用[受控磁碟](/azure/storage/storage-managed-disks-overview)。 受控磁碟不需要儲存體帳戶。 您只需指定磁碟的大小和類型，並以高度可用的方式來部署它。

如果未使用受控磁碟，請針對每個 VM 建立個別的 Azure 儲存體帳戶來保存虛擬硬碟 (VHD)，以避免達到儲存體帳戶的 IOPS 限制。 

新增一或多個資料磁碟。 當您建立新的 VHD 時，它仍未格式化。 登入 VM 來格式化磁碟。 如果您未使用受控磁碟且具有大量的資料磁碟，請注意儲存體帳戶的總 I/O 限制。 如需詳細資訊，請參閱[虛擬機器磁碟限制][vm-disk-limits]。

若情況允許，請將應用程式安裝在資料磁碟，不要安裝在作業系統磁碟。 不過，某些舊版的應用程式可能需要將元件安裝在 C: 磁碟機。 在此情況下，您可以使用 PowerShell 來[重新調整作業系統磁碟大小][resize-os-disk]。

為了達到最佳效能，請建立個別的儲存體帳戶來保存診斷記錄。 標準本地備援儲存體 (LRS) 帳戶已足以保存診斷記錄。

### <a name="network-recommendations"></a>網路建議

此公用 IP 位址可以是動態或靜態。 預設值為動態。

* 如果您需要一個不會變更的固定 IP 位址 &mdash;(例如，如果您需要在 DNS 中建立一個 A 記錄，或需要將 IP 位址加入安全清單)，請保留一個[靜態 IP 位址][static-ip]。
* 您也可以建立 IP 位址的完整網域名稱 (FQDN)。 然後您可以在 DNS 中註冊指向該 FQDN 的 [CNAME 記錄][cname-record]。 如需詳細資訊，請參閱[在 Azure 入口網站中建立完整網域名稱][fqdn]。

所有 NSG 都包含一組[預設規則][nsg-default-rules]，包括一個封鎖所有網際網路輸入流量的規則。 預設的規則不能刪除，但其他規則可以覆寫它們。 若要啟用網際網路流量，請建立允許輸入流量輸入特定連接埠的規則 &mdash; 例如，允許連接埠 80 用於 HTTP。  

若要啟用 RDP，請新增一個 NSG 規則，以允許將輸入流量輸入至 TCP 連接埠 3389。

## <a name="scalability-considerations"></a>延展性考量

您可以藉由[變更 VM 大小][vm-resize]來相應增加或相應減少 VM。 若要水平相應放大，請將兩個以上的 VM 置於負載平衡器後方。 如需詳細資訊，請參閱[在 Azure 上執行多個 VM 以獲得延展性和可用性][multi-vm]。

## <a name="availability-considerations"></a>可用性考量

若要擁有較高的可用性，請在可用性設定組中部署多個 VM。 這也會提供較高的[服務等級協定][vm-sla] (SLA)。 

您的 VM 可能會受到[計劃性維護][planned-maintenance]或[非計劃性維護][manage-vm-availability]影響。 您可以使用 [VM 重新啟動記錄檔][reboot-logs]來判斷 VM 重新啟動是否是因為計劃性維護所造成。

VHD 是儲存在 [Azure 儲存體][azure-storage]，而系統會複寫 Azure 儲存體來提供持久性和可用性。 

為了防止在正常作業期間意外遺失資料 (例如，因使用者錯誤而造成)，您也應該使用 [Blob 快照][blob-snapshot]或其他工具來實作時間點備份。

## <a name="manageability-considerations"></a>管理性考量

**資源群組。** 將關係密切且共用相同生命週期的資源置於同一個[資源群組][resource-manager-overview]。 資源群組可讓您以群組為單位來部署和監視資源，並根據資源群組列出帳單成本。 您也可以刪除整組資源，這對於測試部署非常有用。 為資源提供有意義的名稱。 這樣能更容易找到特定資源及了解其角色。 請參閱 [Azure 資源的建議命名慣例][naming conventions]。

**停止 VM。** Azure 會區分「已停止」和「已解除配置」狀態。 您需要在 VM 狀態停止時支付費用，而不是在取消配置 VM 時支付。 在 Azure 入口網站中，[停止] 按鈕會取消配置 VM。 如果您已在登入時透過 OS 關閉，則會停止 VM，但不會取消配置，因此您仍需付費。

**刪除 VM。** 如果您刪除 VM，並不會刪除 VHD。 這表示您可以放心地刪除 VM，而不會遺失任何資料。 不過，您仍需支付儲存體費用。 若要刪除 VHD，請將檔案從 [Blob 儲存體][blob-storage]中刪除。

若要防止意外刪除，請使用[資源鎖定][resource-lock]來鎖定整個資源群組或鎖定個別資源 (例如 VM)。 

## <a name="security-considerations"></a>安全性考量

使用 [Azure 資訊安全中心][security-center]來集中檢視 Azure 資源的安全性狀態。 資訊安全中心會監視潛在的安全性問題，並提供全面性的部署安全性健康狀態。 資訊安全中心是依每個 Azure 訂用帳戶設定。 按照 [使用資訊安全中心]所述來啟用安全資料收集。 啟用資料收集時，資訊安全性中心就會自動掃描任何該訂用帳戶建立的 VM。

**修補程式管理。** 如果啟用，資訊安全性中心會檢查是否遺失安全性或重要更新。 使用 VM 上的[群組原則設定][group-policy]來啟用自動系統更新。

**反惡意程式碼。** 如果啟用，資訊安全性中心會檢查是已安裝反惡意程式碼軟體。 您也可以使用資訊安全中心來從 Azure 入口網站內安裝反惡意程式碼軟體。

**作業。** 使用[角色型存取控制][rbac] (RBAC) 來控制對您所部署 Azure 資源的存取。 RBAC 可讓您指派授權角色給您 DevOps 小組的成員。 例如，「讀取者」角色能檢視 Azure 資源但不能建立、管理或刪除它們。 某些角色專門用於特定的 Azure 資源類型。 例如，「虛擬機器參與者」角色能重新啟動或解除配置 VM、重設系統管理員密碼、建立新的 VM 等等。 其他對此架構可能有用的[內建 RBAC 角色][rbac-roles]包括 [DevTest Labs 使用者][rbac-devtest]和[網路參與者][rbac-network]。 使用者可以被指派多個角色，且您可以針對更詳細的權限建立角色。

> [!NOTE]
> RBAC 不會限制使用者登入 VM 可執行的動作。 這些權限是由客體 OS上的帳戶類型來決定。   

使用[稽核記錄檔][audit-logs]來查看佈建動作和其他 VM 事件。

**資料加密。** 如果您要加密作業系統和資料磁碟，請考慮使用 [Azure 磁碟加密][disk-encryption]。 

## <a name="deploy-the-solution"></a>部署解決方案

適用於此架構的部署可在 [GitHub][github-folder] 上取得。 它會部署下列各項：

  * 用來裝載 VM 且具有名為 **web** 之單一子網路的虛擬網路。
  * 含有兩個傳入規則的 NSG，允許 RDP 和 HTTP 流量到 VM。
  * 執行最新版 Windows Server 2016 Datacenter Edition 的 VM。
  * 可將兩個資料磁碟格式化的範例自訂指令碼擴充功能，以及部署 IIS 的 PowerShell DSC 指令碼。

### <a name="prerequisites"></a>必要條件

在您可以將參考架構部署到自己的訂用帳戶之前，必須執行下列步驟。

1. 複製、派生或下載適用於 [AzureCAT 參考架構][ref-arch-repo] GitHub 存放庫的 zip 檔案。

2. 確定您已在電腦上安裝 Azure CLI 2.0。 若要安裝 CLI，請依照[安裝 Azure CLI 2.0][azure-cli-2] 中的指示進行。

3. 安裝 [Azure 建置組塊][azbb] npm 封裝。

4. 從命令提示字元、bash 提示字元或 PowerShell 提示字元中，使用下列其中一個命令登入 Azure 帳戶，並遵循提示進行。

  ```bash
  az login
  ```

### <a name="deploy-the-solution-using-azbb"></a>使用 azbb 部署解決方案

若要部署範例單一 VM 工作負載，請依照下列步驟執行：

1. 瀏覽至您在上述必要條件步驟中所下載存放庫的 `virtual-machines\single-vm\parameters\windows` 資料夾。

2. 開啟 `single-vm-v2.json` 檔案，然後在引號之間輸入使用者名稱和 SSH 金鑰，如下所示，然後儲存檔案。

  ```bash
  "adminUsername": "",
  "adminPassword": "",
  ```

3. 執行 `azbb` 以部署範例 VM，如下所示。

  ```bash
  azbb -s <subscription_id> -g <resource_group_name> -l <location> -p single-vm-v2.json --deploy
  ```

如需部署此範例參考架構的詳細資訊，請瀏覽我們的 [GitHub 存放庫][git]。

## <a name="next-steps"></a>後續步驟

- 了解我們的 [Azure 建置組塊][azbbv2]。
- 在 Azure 中部署[多個 VM][multi-vm]。

<!-- links -->
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[azbbv2]: https://github.com/mspnp/template-building-blocks
[audit-logs]: https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: /azure/virtual-machines/virtual-machines-windows-create-availability-set
[azure-storage]: /azure/storage/storage-introduction
[blob-snapshot]: /azure/storage/storage-blob-snapshots
[blob-storage]: /azure/storage/storage-introduction
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: /azure/virtual-machines/virtual-machines-windows-about-disks-vhds
[disk-encryption]: /azure/security/azure-security-disk-encryption
[enable-monitoring]: /azure/monitoring-and-diagnostics/insights-how-to-use-diagnostics
[fqdn]: /azure/virtual-machines/virtual-machines-windows-portal-create-fqdn
[github-folder]: http://github.com/mspnp/reference-architectures/tree/master/virtual-machines/single-vm
[group-policy]: https://technet.microsoft.com/en-us/library/dn595129.aspx
[log-collector]: https://azure.microsoft.com/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: /azure/virtual-machines/virtual-machines-windows-manage-availability
[multi-vm]: multi-vm.md
[naming conventions]: ../../best-practices/naming-conventions.md
[nsg]: /azure/virtual-network/virtual-networks-nsg
[nsg-default-rules]: /azure/virtual-network/virtual-networks-nsg#default-rules
[planned-maintenance]: /azure/virtual-machines/virtual-machines-windows-planned-maintenance
[premium-storage]: /azure/storage/storage-premium-storage
[rbac]: /azure/active-directory/role-based-access-control-what-is
[rbac-roles]: /azure/active-directory/role-based-access-built-in-roles
[rbac-devtest]: /azure/active-directory/role-based-access-built-in-roles#devtest-labs-user
[rbac-network]: /azure/active-directory/role-based-access-built-in-roles#network-contributor
[reboot-logs]: https://azure.microsoft.com/blog/viewing-vm-reboot-logs/
[resize-os-disk]: /azure/virtual-machines/virtual-machines-windows-expand-os-disk
[resource-lock]: /azure/resource-group-lock-resources
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[security-center]: https://azure.microsoft.com/services/security-center/
[select-vm-image]: /azure/virtual-machines/virtual-machines-windows-cli-ps-findimage
[services-by-region]: https://azure.microsoft.com/regions/#services
[static-ip]: /azure/virtual-network/virtual-networks-reserved-public-ip
[storage-account-limits]: /azure/azure-subscription-service-limits#storage-limits
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[使用資訊安全中心]: /azure/security-center/security-center-get-started#use-security-center
[virtual-machine-sizes]: /azure/virtual-machines/virtual-machines-windows-sizes
[visio-download]: https://archcenter.azureedge.net/cdn/vm-reference-architectures.vsdx
[vm-disk-limits]: /azure/azure-subscription-service-limits#virtual-machine-disk-limits
[vm-resize]: /azure/virtual-machines/virtual-machines-linux-change-vm-size
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[vm-size-tables]: /azure/virtual-machines/virtual-machines-windows-sizes#size-tables
[0]: ./images/single-vm-diagram.png "Azure 中的單一 Windows VM 架構"
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[azure-cli-2]: /cli/azure/install-azure-cli?view=azure-cli-latest
[git]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/single-vm

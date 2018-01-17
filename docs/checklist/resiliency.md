---
title: "復原檢查清單"
description: "提供設計期間復原考量指引的檢查清單。"
author: petertaylor9999
ms.date: 01/10/2018
ms.custom: resiliency, checklist
ms.openlocfilehash: 66ff802c1f7b35db147ffe4279982c827570c3c1
ms.sourcegitcommit: 3d6dba524cc7661740bdbaf43870de7728d60a01
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2018
---
# <a name="resiliency-checklist"></a>復原檢查清單

復原是指系統從失敗中復原並繼續運作的能力，且是[軟體品質要素](../guide/pillars.md)的其中一項。 針對復原能力設計應用程式時，您需要進行規劃並緩和可能發生的各種失敗模式。 請使用此檢查清單，從復原能力的觀點來檢閱您的應用程式架構。 

## <a name="requirements"></a>需求

**定義客戶的可用性需求。** 您的客戶對於您應用程式的元件會有可用性需求，這會影響您的應用程式的設計。 針對應用程式的各項可用性目標，取得客戶的同意，否則您的設計可能不符合客戶的期望。 如需詳細資訊，請參閱[定義您的復原需求](../resiliency/index.md#defining-your-resiliency-requirements)。

## <a name="application-design"></a>應用程式設計

**為您的應用程式執行失敗模式分析 (FMA)。** FMA 是在設計階段早期將復原能力內建到應用程式的程序。 如需詳細資訊，請參閱[失敗模式分析][fma]。 FMA 的目標包括：  

* 識別應用程式可能遭遇哪些失敗類型。
* 擷取可能的影響，以及每種失敗類型對應用程式的影響。
* 找出復原策略。
  

**部署多個服務執行個體。** 如果您的應用程式相依於服務的單一執行個體，它會建立單一失敗點。 佈建多個執行個體，可改善復原能力和延展性。 對於 [Azure App Service](/azure/app-service/app-service-value-prop-what-is/)，選取可提供多個執行個體的 [App Service 方案](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview/)。 對於 Azure 雲端服務，設定您的每個角色以使用[多個執行個體](/azure/cloud-services/cloud-services-choose-me/#scaling-and-management)。 對於 [Azure 虛擬機器 (VM)](/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，確保 VM 架構包含多個 VM，而且每個 VM 都包含在[可用性設定組][availability-sets]中。   

**使用自動調整來因應負載增加。** 如果您的應用程式並未設定為隨著負載增加自動相應放大，而應用程式的服務若充斥著使用者要求，這些服務可能會失敗。 如需詳細資訊，請參閱下列各項：

* 一般：[延展性檢查清單](./scalability.md)
* Azure App Service：[手動或自動調整執行個體計數][app-service-autoscale]
* 雲端服務：[如何自動調整雲端服務][cloud-service-autoscale]
* 虛擬機器：[自動調整與虛擬機器擴展集][vmss-autoscale]

**使用負載平衡功能來分配要求。** 負載平衡功能可藉由從循環中移除狀況不良的執行個體，將應用程式的要求分配至狀況良好的服務執行個體。 如果您的服務使用 Azure App Service 或 Azure 雲端服務，它已經為您平衡負載。 不過，如果您的應用程式使用 Azure VM，您就需要佈建負載平衡器。 如需詳細資訊，請參閱 [Azure Load Balancer](/azure/load-balancer/load-balancer-overview/)概觀。

**將 Azure 應用程式閘道設定為使用多個執行個體。** 根據您應用程式的需求，[Azure 應用程式閘道](/azure/application-gateway/application-gateway-introduction/)可能更適合用於將要求分配至您應用程式的服務。 不過，SLA 不保證應用程式閘道服務的單一執行個體，所以如果應用程式閘道執行個體失敗，您的應用程式也可能會失敗。 佈建一個以上中型或大型應用程式閘道執行個體，可根據 [SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_0/) 條款保證服務的可用性。

**使用每個應用程式層的可用性設定組。** 將執行個體放入[可用性設定組][availability-sets]中，可提供較高的 [SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。 

**請考慮在多個區域中部署您的應用程式。** 如果您的應用程式已部署到單一區域，在少數情況下，整個區域會變得無法使用，您的應用程式也將無法使用。 您應用程式的 SLA 條款可能無法接受這種情況。 若是如此，請考慮在多個區域中部署您的應用程式及其服務。 多區域部署可以使用主動-主動模式 (將要求分散於多個作用中執行個體) 或主動-被動模式 (保留「暖的」執行個體，以防主要執行個體失敗)。 我們建議您跨越區域配對來部署應用程式服務的多個執行個體。 如需詳細資訊，請參閱[商務持續性和災害復原 (BCDR)：Azure 配對區域](/azure/best-practices-availability-paired-regions)。

**使用 Azure 流量管理員將應用程式的流量傳送到不同的區域。**  [Azure 流量管理員][traffic-manager] 會在 DNS 層級執行負載平衡，並根據您指定的[流量傳送][traffic-manager-routing]方法和您的應用程式端點的健康情況，將流量傳送到不同的區域。 若未使用流量管理員，您會受限於您部署的單一區域，這會限制規模，增加某些使用者的延遲時間，並且在整個區域服務中斷的情況下造成應用程式停機。

**為您的負載平衡器和流量管理員設定和測試健康情況探查。** 確定您的健康情況邏輯會檢查系統的重要組件，並適當地回應健康情況探查。

* [Azure 流量管理員][traffic-manager]和 [Azure Load Balancer][load-balancer] 的健康情況探查可提供特定功能。 流量管理員的健康情況探查可決定是否要容錯移轉到另一個區域。 負載平衡器的健康情況探查則可決定是否要從循環中移除 VM。      
* 對於流量管理員探查，您的健康情況端點應該檢查在相同區域中部署的任何重要相依性，而其失敗應會觸發容錯移轉至另一個區域。  
* 對於負載平衡器，健康情況端點應會報告 VM 的健康情況。 不包含其他層或外部服務。 否則，在 VM 外部發生的失敗，可能會導致負載平衡器從循環中移除 VM。
* 如需在應用程式中實作健康情況監視的指引，請參閱[健康情況端點監視模式](https://msdn.microsoft.com/library/dn589789.aspx)。

**監視第三方服務。** 如果您的應用程式具有第三方服務相依性，請找出這些第三方服務可能會失敗的位置和方式，以及這些失敗會對您的應用程式造成何種影響。 第三方服務可能不包含監視與診斷，因此務必記錄您引動它們的過程，並使用唯一識別碼使其與您應用程式的健康情況和診斷記錄相互關聯。 如需監視和診斷的可行作法詳細資訊，請參閱[監視和診斷指引][monitoring-and-diagnostics-guidance]。

**確定您使用的任何第三方服務均提供 SLA。** 如果您的應用程式相依於第三方服務，但第三方並未提供 SLA 形式的可用性保證，則也無法保證應用程式的可用性。 您的 SLA 無異於您的應用程式最不常使用的元件。

**在適當情況下對遠端作業實作復原模式。** 如果您的應用程式相依於遠端服務之間的通訊，請遵循設計模式來處理暫時性失敗，例如[重試模式][retry-pattern]和[斷路器模式][circuit-breaker]。 如需詳細資訊，請參閱[復原策略](../resiliency/index.md#resiliency-strategies)。

**可能的話，請實作非同步作業。** 同步作業會獨佔資源，而且在呼叫端等候程序完成時，封鎖其他作業。 將您應用程式的每個組件設計成儘可能允許非同步作業。 如需有關如何以 C# 實作非同步程式設計的詳細資訊，請參閱[使用 async 和 await 進行非同步程式設計][asynchronous-c-sharp]。

## <a name="data-management"></a>資料管理

**了解您的應用程式資料來源的複寫方法。** 應用程式資料會儲存於不同的資料來源，而且有不同的可用性需求。 評估 Azure 中每種資料儲存體的複寫方法，包括 [Azure 儲存體複寫](/azure/storage/storage-redundancy/)和 [SQL Database 作用中異地複寫](/azure/sql-database/sql-database-geo-replication-overview/)，確保滿足您應用程式的資料需求。

**確保所有單一使用者帳戶均無法同時存取生產和備份資料。** 如果有單一使用者帳戶同時具有寫入生產與備份來源的權限，您的資料備份就會受到危害。 惡意使用者可能會刻意刪除您所有的資料，而一般使用者可能會不小心刪除它。 將您的應用程式設計成限制每個使用者帳戶的權限，只讓需要寫入權限的使用者具有寫入權限，而且只能寫入生產或備份來源 (但非兩者)。

**記載您的資料來源容錯移轉和容錯回復程序並加以測試。** 在您的資料來源發生災難性失敗的情況下，操作人員必須遵循一組書面指示來容錯移轉至新的資料來源。 如果書面步驟有誤，操作人員就無法成功按照步驟來容錯移轉資源。 定期測試指示步驟，確認遵循這些步驟的操作人員能夠成功地容錯移轉及容錯回復資料來源。

**驗證您的資料備份。** 執行指令碼來驗證資料完整性、結構描述和查詢，以定期確認您的備份資料如您所預期。 如果備份不適合用來還原您的資料來源，則具有備份毫無意義。 記錄和報告不一致情形，以便修復備份服務。

**請考慮使用異地備援的儲存體帳戶類型。** 儲存於 Azure 儲存體帳戶的資料一律會在本機複寫。 不過，佈建儲存體帳戶時，有多個複寫策略可供選擇。 選取 [Azure 讀取權限異地備援儲存體 (RA-GRS)](/azure/storage/storage-redundancy/#read-access-geo-redundant-storage)來保護您的應用程式資料，以免遭遇整個區域無法使用的罕見情況。

> [!NOTE]
> 若為 VM，請勿依賴 RA-GRS 複寫來還原 VM 磁碟 (VHD 檔案)。 請改用 [Azure 備份][azure-backup]。   
>
>

## <a name="security"></a>安全性

**實作應用程式層級的防護來抵禦分散式阻斷服務 (DDoS) 攻擊。** Azure 服務會受到保護以免遭受網路層 DDos 攻擊。 不過，Azure 無法抵禦應用程式層攻擊，因為很難區分真正的使用者要求與惡意使用者要求。 如需有關如何抵禦應用程式層 DDoS 攻擊的詳細資訊，請參閱 [Microsoft Azure 網路安全性](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf)的「抵禦 DDoS」一節 (PDF 下載)。

**對應用程式的資源存取實作最低權限原則。** 應用程式資源的預設存取權應儘可能受限制。 在核准時授與較高層級的權限。 依預設，授與過度寬鬆的應用程式資源權限可能會導致其他人刻意或意外地刪除資源。 Azure 提供[角色型存取控制](/azure/active-directory/role-based-access-built-in-roles/)來管理使用者權限，但務必針對擁有自備權限系統的其他資源 (例如 SQL Server ) 確認其最低權限。

## <a name="testing"></a>測試

**對您的應用程式執行容錯移轉和容錯回復測試。** 如果您尚未完整測試容錯移轉和容錯回復，您就無法確定應用程式中的相依服務會在災害復原期間以同步處理方式進行備份。 確保您應用程式的相依服務以正確的順序容錯移轉及容錯回復。

**對您的應用程式執行錯誤插入測試。** 您的應用程式可能會因為許多不同原因而失敗，例如，憑證到期、VM 中的系統資源耗盡，或儲存體失敗。 藉由模擬或觸發真正的失敗，在儘可能接近生產的環境中測試您的應用程式。 例如，刪除憑證、以人為方式取用系統資源，或刪除儲存體來源。 確認您的應用程式能夠從各類型的錯誤 (獨立錯誤和錯誤組合) 中復原。 檢查失敗並未在您的系統中傳播或串聯。

**同時使用綜合與真實的使用者資料在生產環境中執行測試。** 測試與生產環境很少完全相同，因此務必使用藍色/綠色或淡黃色部署，並在生產環境中測試您的應用程式。 這可讓您在實際負載下的生產環境中測試您的應用程式，並確保它在完整部署的情況下如預期般運作。

## <a name="deployment"></a>Deployment

**記載您的應用程式發行程序。** 若未記載詳細的發行程序，操作人員可能會部署不正確的更新，或不正確地設定您的應用程式設定。 明確定義和記載您的發行程序，並確保它可供整個營運小組使用。 

**自動執行應用程式的部署程序。** 如果操作人員必須以手動方式部署您的應用程式，人為錯誤可能會導致部署失敗。 

**設計您的發行程序，以將應用程式的可用性最大化。** 如果您的發行程序要求服務在部署期間離線，您的應用程式將無法使用，直到它們重新上線為止。 使用[藍色/綠色](http://martinfowler.com/bliki/BlueGreenDeployment.html)或[淡黃色](http://martinfowler.com/bliki/CanaryRelease.html)部署技術，將應用程式部署到生產環境。 這兩種技術都牽涉到隨著生產程式碼部署您的發行程式碼，讓發行程式碼的使用者可以在失敗時重新導向至生產程式碼。

**記錄和稽核應用程式的部署。** 如果您使用分段部署技術 (例如藍色/綠色或淡黃色版本)，您的應用程式會有一個以上的版本在生產環境中執行。 如果發生問題，請務必判斷哪一個應用程式版本造成問題。 實作強固的記錄策略，儘可能擷取較多的版本特有資訊。

**具備部署回復計劃。** 您的應用程式部署可能會失敗，並導致您的應用程式無法使用。 設計回復程序，以回到上一個已知良好的版本並將停機時間縮到最短。 

## <a name="operations"></a>作業

**在您的應用程式中實作監視和警示的最佳做法。** 若沒有適當的監視、診斷和警示，便無法在您的應用程式中偵測失敗並警示操作人員進行修正。 如需詳細資訊，請參閱[監視和診斷指引][monitoring-and-diagnostics-guidance]。

**測量遠端呼叫統計資料，並將資訊提供給應用程式小組使用。**  如果您未即時追蹤及報告遠端呼叫統計資料，以及提供簡單的方法來檢閱此資訊，營運小組就無法即時檢視您應用程式的健康情況。 而如果您只測量平均遠端呼叫時間，您就沒有足夠的資訊可揭露服務的問題。 摘要說明遠端呼叫計量，例如延遲、輸送量，以及 99 和 95 百分位數的錯誤。 對計量執行統計分析，以發現在每個百分位數內發生的錯誤。

**追蹤一段適當時間範圍內的暫時性例外狀況和重試數目。** 如果您未追蹤及監視一段時間的暫時性例外狀況和重試數目，則應用程式的重試邏輯可能會隱藏問題或失敗。 也就是說，如果您的監視和記錄作業只顯示作業的成功或失敗，將會隱藏作業因為例外狀況而重試數次的這個事實。 例外狀況隨著時間遞增的趨勢，表示服務有問題而且可能失敗。 如需詳細資訊，請參閱[重試服務的特定指引][retry-service-guidance]。

**實作可警示操作人員的早期警告系統。** 找出應用程式健康情況的關鍵效能指標 (例如暫時性例外狀況和遠端呼叫延遲)，並且為每個指標設定適當的臨界值。 達到臨界值時，傳送警示給營運小組。 在問題變嚴重並需要復原回應之前，在可識別問題的層級設定這些臨界值。

**確保小組中有一個以上的人員經過訓練，可監視應用程式並執行任何手動復原步驟。** 如果小組中只有一個操作人員可以監視應用程式和開始進行復原步驟，則該人員會成為單一失敗點。 訓練多個人進行偵測和復原，並確保隨時都有至少一個值勤的人員。

**確保您的應用程式不會遭遇 [Azure 訂用帳戶限制](/azure/azure-subscription-service-limits/)。** Azure 訂用帳戶對某些資源類型有所限制，例如資源群組數目、核心數目，以及儲存體帳戶數目。  如果應用程式需求超過 Azure 訂用帳戶限制，請建立另一個 Azure 訂用帳戶並在其中佈建足夠的資源。

**確保您的應用程式不會遭遇[各項服務限制](/azure/azure-subscription-service-limits/)。** 個別的 Azure 服務都有取用量限制&mdash;，例如儲存體、輸送量、連線數目、每秒要求數和其他計量的限制。 如果應用程式嘗試使用超出這些限制的資源，應用程式就會失敗。 這會導致受影響的使用者發生服務節流和可能停機。 視特定服務和您的應用程式需求而定，您通常可藉由相應增加 (例如，選擇另一個定價層) 或相應放大 (新增執行個體) 來避開這些限制。  

**設計您應用程式的儲存體需求，使其落在 Azure 儲存體延展性和效能目標內。** Azure 儲存體已設計成要在預先定義的延展性和效能目標內運作，所以將您的應用程式設計成利用這些目標內的儲存體。 如果您超過這些目標，應用程式將會遇到儲存體節流。 若要修正此問題，請佈建額外的儲存體帳戶。 如果您遭遇儲存體帳戶限制，請佈建額外的 Azure 訂用帳戶，然後在其中佈建額外的儲存體帳戶。 如需詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](/azure/storage/storage-scalability-targets/)。

**為您的應用程式選取適當的 VM 大小。** 測量生產環境中的 VM 的實際 CPU、記憶體、磁碟和 I/O，並確認您所選取的 VM 大小夠大。 如果未這麼做，您的應用程式可能會在 VM 接近其限制時遇到容量問題。 [Azure 中的虛擬機器大小](/azure/virtual-machines/virtual-machines-windows-sizes/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)會詳細說明 VM 大小。

**判斷您的應用程式在一段時間內的工作負載是穩定還是波動。** 如果您的工作負載隨著波動，請使用 Azure VM 擴展集來自動調整 VM 執行個體的數目。 否則，您將必須以手動方式增加或減少 VM 數目。 如需詳細資訊，請參閱[虛擬機器擴展集概觀](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview/)。

**選取 Azure SQL Database 適用的服務層。** 如果您的應用程式使用 Azure SQL Database，請確定您已選取適當的服務層。 如果您選取的服務層無法處理您應用程式的資料庫交易單位 (DTU) 需求，您的資料使用將會進行節流。 如需選取正確服務方案的詳細資訊，請參閱 [SQL Database 選項和效能：了解每個服務層中可用的項目](/azure/sql-database/sql-database-service-tiers/)。

**建立可與 Azure 支援服務互動的程序。** 如果在連絡支援服務的需求產生之前，並未設定連絡 [Azure 支援服務](https://azure.microsoft.com/support/plans/)的程序，則停機時間將會因為第一次瀏覽支援程序而延長。 一開始就將連絡支援服務並向上呈報問題的程序納入應用程式的復原能力中。

**確保您的應用程式不會使用超過每一訂用帳戶的儲存體帳戶數目上限。** Azure 允許每一訂用帳戶最多可有 200 個儲存體帳戶。 如果您的應用程式需要的儲存體帳戶超過您的訂用帳戶中目前可用的儲存體帳戶，您必須建立新的訂用帳戶並在其中建立額外的儲存體帳戶。 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](/azure/azure-subscription-service-limits/#storage-limits)。

**確保您的應用程式未超過虛擬機器磁碟的延展性目標。** Azure IaaS VM 支援根據數個因子 (包括 VM 大小和儲存體帳戶類型) 來連結多個資料磁碟。 如果您的應用程式超過虛擬機器磁碟的延展性目標，請佈建額外的儲存體帳戶並在其中建立虛擬機器磁碟。 如需詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](/azure/storage/storage-scalability-targets/#scalability-targets-for-virtual-machine-disks)

## <a name="telemetry"></a>遙測

**當應用程式在生產環境中執行時，請記錄遙測資料。** 當應用程式在生產環境中執行，或它正在服務使用者時，如果您沒有足夠的資訊可診斷問題的原因，請擷取強固的遙測資訊。 如需詳細資訊，請參閱[監視和診斷][monitoring-and-diagnostics-guidance]。

**使用非同步模式實作記錄作業。** 如果記錄作業是同步的，它們可能會封鎖您的應用程式程式碼。 確保您的記錄作業是以非同步作業的形式實作。

**讓不同服務界限的記錄資料相互關聯。** 在典型的多層式架構 (N-Tier) 應用程式中，使用者要求可能會跨越數個服務界限。 例如，使用者要求通常源自 Web 層，而後傳遞至商業層，最後保存在資料層中。 在更複雜的情況下，使用者要求可能會分散於許多不同的服務和資料存放區。 確保您的記錄系統可讓不同服務界限的呼叫相互關聯，以便您在整個應用程式中追蹤要求。

## <a name="azure-resources"></a>Azure 資源

**使用 Azure Resource Manager 範本來佈建資源。** Resource Manager 範本可讓您輕鬆地透過 PowerShell 或 Azure CLI 自動進行部署，因而產生更可靠的部署程序。 如需詳細資訊，請參閱 [Azure Resource Manager概觀][resource-manager]。

**為資源提供有意義的名稱。** 為資源提供有意義的名稱，能更容易找到特定資源及了解其角色。 如需詳細資訊，請參閱 [Azure 資源的命名慣例](../best-practices/naming-conventions.md)

**使用角色型存取控制 (RBAC)。** 使用 RBAC 來控制您所部署之 Azure 資源的存取權。 RBAC 可讓您指派授權角色給您 DevOps 小組的成員，以免不小心刪除或變更已部署的資源。 如需詳細資訊，請參閱[開始使用 Azure 入口網站中的存取管理](/azure/active-directory/role-based-access-control-what-is/)

**使用重要資源 (例如 VM) 的資源鎖定。** 資源鎖定可防止操作人員不小心刪除資源。 如需詳細資訊，請參閱[使用 Azure Resource Manager 來鎖定資源](/azure/azure-resource-manager/resource-group-lock-resources/)

**選擇區域配對。** 部署至兩個區域時，請選擇相同區域配對中的區域。 若發生廣泛中斷事件，會優先復原所有配對中的一個區域。 有些服務 (異地備援儲存體) 會提供自動複寫到配對的區域。 如需詳細資訊，請參閱[商務持續性和災害復原 (BCDR)：Azure 配對的區域](/azure/best-practices-availability-paired-regions)

**依照功能和生命週期來組織資源群組。**  一般而言，資源群組應包含共用相同生命週期的資源。 這可讓您更輕鬆地管理部署、刪除測試部署，以及指派存取權限，進而降低不小心刪除或修改生產部署的可能性。 針對生產、部署和測試環境，各別建立資源群組。 在多區域的部署中，將每個區域的資源放入不同的資源群組中。 這可讓您更輕鬆地重新部署一個區域，而不會影響其他區域。

## <a name="azure-services"></a>Azure 服務
下列檢查清單項目適用於 Azure 中的特定服務。

- [App Service](#app-service)
- [應用程式閘道](#application-gateway)
- [Cosmos DB](#cosmos-db)
- [Redis 快取](#redis-cache)
- [搜尋](#search)
- [儲存體](#storage)
- [SQL Database](#sql-database)
- [在虛擬機器中執行的 SQL Server](#sql-server-running-in-a-vm)
- [流量管理員](#traffic-manager)
- [虛擬機器](#virtual-machines)
- [虛擬網路](#virtual-network)

### <a name="app-service"></a>App Service 方案

**使用標準或進階層。** 這些服務層可支援預備位置和自動化備份。 如需詳細資訊，請參閱 [Azure App Service 方案深入概觀](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview/)

**避免相應增加或相應減少。** 請改為選取在一般負載下符合您效能需求的服務層和執行個體大小，然後[相應放大](/azure/app-service-web/web-sites-scale/)執行個體來處理流量的變更。 相應增加和減少可能會觸發應用程式重新啟動。  

**將組態儲存為應用程式設定。** 使用應用程式設定，將組態設定保留為應用程式設定。 在您的 Resource Manager 範本 中或使用 PowerShell 來定義設定，以便您在更可靠的自動化部署 / 更新程序中套用它們。 如需詳細資訊，請參閱[在 Azure App Service 中設定 Web 應用程式](/azure/app-service-web/web-sites-configure/)。

**為生產和測試環境建立不同的 App Service 方案。** 請勿使用生產部署上的位置進行測試。  相同 App Service 方案中的所有應用程式會共用相同的 VM 執行個體。 如果您將生產和測試部署放在相同的方案中，可能會對生產部署造成負面影響。 例如，負載測試可能會使實際作用的生產網站降級。 透過將測試部署放在不同方案內，您可以使其與生產版本隔離。  

**分隔 Web 應用程式與 Web API。** 如果您的解決方案具有 Web 前端與 Web API，請考慮將它們分解成個別的 App Service 應用程式。 此設計可讓您更輕鬆地依照工作負載來分解解決方案。 您可以在個別的 App Service 方案中執行 Web 應用程式與 API，以便獨立調整其規模。 如果您一開始不需要這種程度的延展性，可以將應用程式部署到相同的方案中，之後有需要時再將它們移到別的方案。

**避免使用 App Service 備份功能來備份 Azure SQL 資料庫。** 請改用 [SQL Database 自動化備份][sql-backup]。 App Service 備份會將資料庫匯出至 SQL .bacpac 檔案，這會使用 DTU。  

**部署到預備位置。** 建立預備的部署位置。 將應用程式更新部署到預備位置，並先確認部署，再將它交換至生產環境。 這可降低生產環境使用不正確更新的可能性。 也可確保所有執行個體在交換至生產環境之前就已準備就緒。 許多應用程式都有重要的暖機和冷啟動時間。 如需詳細資訊，請參閱[針對 Azure App Service 中的 Web 應用程式設定預備環境](/azure/app-service-web/web-sites-staged-publishing/)。

**建立部署位置來保存上一個已知良好 (LKG) 的部署。** 當您將更新部署到生產環境時，請將先前的生產部署移到 LKG 位置。 這可讓您更輕鬆地復原錯誤的部署。 如果您之後發現問題，您可以快速地還原至 LKG 版本。 如需詳細資訊，請參閱[基本 Web 應用程式](../reference-architectures/app-service-web-app/basic-web-app.md)。

**啟用診斷記錄**，包括應用程式記錄和 Web 伺服器記錄。 記錄作業對監視和診斷而言都很重要。 請參閱[在 Azure App Service 中針對 Web 應用程式啟用診斷記錄功能](/azure/app-service-web/web-sites-enable-diagnostic-log/)

**記錄至 Blob 儲存體。** 這可讓您更輕鬆地收集和分析資料。

**為記錄建立個別的儲存體帳戶。** 記錄和應用程式資料不可使用相同儲存體帳戶。 這有助於防止記錄作業降低應用程式效能。

**監視效能。** 使用 [New Relic](http://newrelic.com/) 或 [Application Insights](/azure/application-insights/app-insights-overview/) 等效能監視服務，來監視負載下的應用程式效能和行為。  效能監視可讓您即時深入解析應用程式。 它可讓您診斷問題並執行失敗的根本原因分析。

### <a name="application-gateway"></a>應用程式閘道

**佈建至少兩個執行個體。** 部署具有至少兩個執行個體的應用程式閘道。 單一執行個體為單一失敗點。 使用兩個或多個執行個體，可提供備援和延展性。 為了符合 [SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_0/) 的資格，您必須佈建兩個或更多中型或大型執行個體。

### <a name="cosmos-db"></a>Cosmos DB

**跨區域複寫資料庫。** Cosmos DB 可讓您將任意數目的 Azure 區域與 Cosmos DB 資料庫帳戶產生關聯。 Cosmos DB 資料庫可以有一個寫入區域和多個唯讀區域。 如果寫入區域發生失敗，您可以從另一個複本進行讀取。 用戶端 SDK 會自動處理此作業。 您也可以將寫入區域容錯移轉到另一個區域。 如需詳細資訊，請參閱[如何使用 Azure Cosmos DB 在全域散發資料？](/azure/documentdb/documentdb-distribute-data-globally)

### <a name="redis-cache"></a>Redis 快取

**設定異地複寫**。 異地複寫提供一個機制，可供連結兩個高階層 Azure Redis 快取執行個體。 寫入主要快取中的資料會複寫至次要唯讀快取。 如需詳細資訊，請參閱[如何設定 Azure Redis 快取的異地複寫](/azure/redis-cache/cache-how-to-geo-replication)

**設定資料永續性。** Redis 永續性可讓您保存儲存在 Redis 中的資料。 您也可以擷取快照和備份資料，以在硬體失敗時載入。 如需詳細資訊，請參閱 [如何設定進階 Azure Redis 快取的資料永續性](/azure/redis-cache/cache-how-to-premium-persistence)

若您將 Redis 快取當作暫存資料快取而非持續性存放區，這些建議可能不適用。 

### <a name="search"></a>Search

**佈建一個以上的複本。** 使用至少兩個複本可提供讀取高可用性，或者使用三個複本可提供讀寫高可用性。

**設定多區域部署的索引子。** 如果您有多區域部署，請考慮索引連續性的選項。

  * 如果資料來源已在異地複寫，您通常應將每個區域性 Azure 搜尋服務的每個索引子指向其本機資料來源複本。 不過，該方法不建議用於儲存在 Azure SQL Database 中的大型資料集。 原因是 Azure 搜尋服務無法從次要 SQL Database 複本 (只能從主要複本) 執行增量編製索引。 請改為將所有索引子指向主要複本。 在容錯移轉之後，指著位於新主要複本的 Azure 搜尋服務索引子。  
  * 如果資料來源並未異地複寫，請指著位於相同資料來源的多個索引子，如此一來，多個區域中的 Azure 搜尋服務就能持續且獨立地從資料來源編製索引。 如需詳細資訊，請參閱 [Azure 搜尋服務效能和最佳化考量][search-optimization]。

### <a name="storage"></a>儲存體

**對於應用程式資料，請使用讀取權限異地備援儲存體 (RA-GRS)。** RA-GRS 儲存體會將資料複寫到次要區域，並提供從次要區域的唯讀存取權。 如果主要區域發生儲存體中斷，應用程式可以從次要區域讀取資料。 如需詳細資訊，請參閱 [Azure 儲存體複寫](/azure/storage/storage-redundancy/)。

**若為 VM 磁碟，請使用受控磁碟。** [受控磁碟][managed-disks]可為可用性設定組中的 VM 提供更高的可靠性，因為磁碟彼此充分隔離，以避免單一失敗點。 此外，受控磁碟不受儲存體帳戶中建立之 VHD 的 IOPS 限制所約束。 如需詳細資訊，請參閱[管理 Azure 中 Windows 虛擬機器的可用性][vm-manage-availability]。

**若為佇列儲存體，請在另一個區域中建立備份佇列。** 對於佇列儲存體，唯讀複本的用途有限，因為您無法將項目排入佇列或從佇列中清除。 請改為在其他區域的儲存體帳戶中建立備份佇列。 如果發生儲存體中斷，應用程式可以使用備份佇列，直到主要區域再次變為可使用為止。 這樣一來，應用程式仍然可以處理新的要求。  

### <a name="sql-database"></a>SQL Database

**使用標準或進階層。** 這些服務層提供較長的時間點還原週期 (35 天)。 如需詳細資訊，請參閱 [SQL Database 選項和效能](/azure/sql-database/sql-database-service-tiers/)。

**啟用 SQL Database 稽核。** 稽核可以用來診斷惡意攻擊或人為錯誤。 如需詳細資訊，請參閱 [開始使用 SQL 資料庫稽核](/azure/sql-database/sql-database-auditing-get-started/)。

**使用作用中異地複寫** 使用作用中異地複寫在不同區域中建立可讀取的次要複本。  如果您的主要資料庫失敗，或只需要離線，請執行手動容錯移轉至次要資料庫。  在您容錯移轉前，次要資料庫會維持唯讀狀態。  如需詳細資訊，請參閱 [SQL Database 作用中異地複寫](/azure/sql-database/sql-database-geo-replication-overview/)。

**使用分區化。** 請考慮使用分區化來水平分割資料庫。 分區化可提供容錯隔離。 如需詳細資訊，請參閱[使用 Azure SQL Database 相應放大](/azure/sql-database/sql-database-elastic-scale-introduction/)。

**使用時間點還原從人為錯誤中復原。**  時間點還原會使您的資料庫回到較早的時間點。 如需詳細資訊，請參閱[使用自動化資料庫備份來復原 Azure SQL Database][sql-restore]。

**使用異地還原從服務中斷中復原。** 異地還原可從異地備援備份還原資料庫。  如需詳細資訊，請參閱[使用自動化資料庫備份來復原 Azure SQL Database][sql-restore]。

### <a name="sql-server-running-in-a-vm"></a>在虛擬機器中執行的 SQL Server

**複寫資料庫。** 使用 SQL Server Always On 可用性群組來複寫資料庫。 如果有一個 SQL Server 執行個體失敗，可提供高可用性。 如需詳細資訊，請參閱[執行適用於多層式架構的 Windows VM](../reference-architectures/virtual-machines-windows/n-tier.md)

**備份資料庫**。 如果您已經使用 [Azure 備份](https://azure.microsoft.com/documentation/services/backup/)來備份您的 VM，請考慮使用 [Azure 備份來備份採用 DPM 的 SQL Server 工作負載](/azure/backup/backup-azure-backup-sql/)。 使用此方法，組織有一個備份管理員角色以及 VM 和 SQL Server 的整合復原程序。 否則，使用 [SQL Server 受控備份至 Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx)。

### <a name="traffic-manager"></a>流量管理員

**執行手動容錯回復。** 在流量管理員容錯移轉之後，執行手動容錯回復，而非自動容錯回復。 在容錯回復之前，確認所有應用程式子系統的狀況良好。  或者，您可以建立應用程式會在資料中心之間來回翻轉的情況。 如需詳細資訊，請參閱[在多個區域中執行 VM 以獲得高可用性](../reference-architectures/virtual-machines-windows/multi-region-application.md)。

**建立健康情況探查端點。** 建立可報告應用程式整體健康情況的自訂端點。 如有任何重要的路徑失敗 (不只是前端)，這可讓流量管理員進行容錯移轉。 如有任何重要的相依性處於狀況不良或無法連線，則端點應傳回 HTTP 錯誤碼。 然而，不會回報不重要服務的錯誤。 否則，健康情況探查可能觸發容錯移轉，若不需要，則會建立誤判。 如需詳細資訊，請參閱[流量管理員端點監視和容錯移轉](/azure/traffic-manager/traffic-manager-monitoring/)。

### <a name="virtual-machines"></a>虛擬機器

**避免在單一 VM 上執行生產工作負載。** 單一 VM 部署不適合進行計劃性或非計劃性維護。 請改為將多個 VM 放入一個可用性集合或 [VM 擴展集](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview/)中 (前面是負載平衡器)。

**在佈建 VM 時指定可用性設定組。** 目前沒有任何方法可在佈建 VM 之後，將 VM 新增至可用性設定組。 當您將新的 VM 新增至現有的可用性設定時，務必為此 VM 建立 NIC，然後將此 NIC 新增至負載平衡器上的後端位址集區。 否則，負載平衡器不會將網路流量傳送到該 VM。

**將每個應用程式層放在不同的可用性設定組中。** 在多層式架構應用程式中，請勿將不同層的 VM 放在相同的可用性設定組中。 可用性設定組中的 VM 會置於各個容錯網域 (FD) 與更新網域 (UD) 中。 不過，若要取得 FD 和 UD 的備援優勢，可用性設定組中的每個 VM 必須能夠處理相同的用戶端要求。

**根據效能需求選擇正確的 VM 大小。** 將現有的工作負載移至 Azure 時，從最符合您內部部署伺服器的 VM 大小開始。 然後根據 CPU、記憶體和 IOPS 測量您的實際工作負載效能，並視需要調整大小。 這有助於確保應用程式如預期般在雲端環境中運作。 此外，如果您需要多個 NIC，請留意每個大小的 NIC 限制。

**對 VHD 使用受控磁碟。** [受控磁碟][managed-disks]可為可用性設定組中的 VM 提供更高的可靠性，因為磁碟彼此充分隔離，以避免單一失敗點。 此外，受控磁碟不受儲存體帳戶中建立之 VHD 的 IOPS 限制所約束。 如需詳細資訊，請參閱[管理 Azure 中 Windows 虛擬機器的可用性][vm-manage-availability]。

**將應用程式安裝在資料磁碟上，而非作業系統磁碟上。** 否則，您可能會達到磁碟大小限制。

**使用 Azure 備份來備份 VM。** 備份可防止資料意外遺失。 如需詳細資訊，請參閱[使用復原服務保存庫來保護 Azure VM](/azure/backup/backup-azure-vms-first-look-arm/)。

**啟用診斷記錄**，包括基本健康情況計量、基礎結構記錄及[開機診斷][boot-diagnostics]。 如果您的 VM 進入無法開機的狀態，開機診斷能協助您診斷開機失敗。 如需詳細資訊，請參閱 [Azure Diagnostic Logs 概觀][diagnostics-logs]。

**使用 AzureLogCollector 擴充功能。** (僅限 Windows VM。)這項擴充功能可彙總 Azure 平台記錄，並將其上傳至 Azure 儲存體，而不需操作人員從遠端登入 VM。 如需詳細資訊，請參閱 [AzureLogCollector 擴充功能](/azure/virtual-machines/virtual-machines-windows-log-collector-extension/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

### <a name="virtual-network"></a>虛擬網路

**若要允許或封鎖公用 IP 位址，請將 NSG 新增至子網路。** 封鎖來自惡意使用者的存取，或只允許有權存取應用程式的使用者存取。  

**建立自訂健康情況探查。** Load Balancer 健康情況探查可以測試 HTTP 或 TCP。 如果 VM 執行 HTTP 伺服器，則 HTTP 探查是比 TCP 探查更佳的健康狀態指標。 對於 HTTP 探查，使用可報告應用程式整體健康情況 (包括所有重要相依性) 的自訂端點。 如需詳細資訊，請參閱 [Azure Load Balancer 概觀](/azure/load-balancer/load-balancer-overview/)。

**封鎖健康情況探查。** Load Balancer 健康情況探查會從已知的 IP 位址 168.63.129.16 傳送。 請勿在任何防火牆原則或網路安全性群組 (NSG) 規則中封鎖往返此 IP 的流量。 封鎖健康情況探查，可能會導致負載平衡器從循環中移除 VM。

**啟用 Azure Load Balancer 記錄功能。** 記錄會顯示後端有多少個 VM 因為探查回應失敗而不接收網路流量。 如需詳細資訊，請參閱 [Azure Load Balancer 的記錄分析](/azure/load-balancer/load-balancer-monitor-log/)。


<!-- links -->
[app-service-autoscale]: /azure/monitoring-and-diagnostics/insights-how-to-scale/
[asynchronous-c-sharp]: /dotnet/articles/csharp/async
[availability-sets]:/azure/virtual-machines/virtual-machines-windows-manage-availability/
[azure-backup]: https://azure.microsoft.com/documentation/services/backup/
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[circuit-breaker]: ../patterns/circuit-breaker.md
[cloud-service-autoscale]: /azure/cloud-services/cloud-services-how-to-scale/
[diagnostics-logs]: /azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs/
[fma]: ../resiliency/failure-mode-analysis.md
[resilient-deployment]: ../resiliency/index.md#resilient-deployment
[load-balancer]: /azure/load-balancer/load-balancer-overview/
[managed-disks]: /azure/storage/storage-managed-disks-overview
[monitoring-and-diagnostics-guidance]: ../best-practices/monitoring.md
[resource-manager]: /azure/azure-resource-manager/resource-group-overview/
[retry-pattern]: ../patterns/retry.md
[retry-service-guidance]: ../best-practices/retry-service-specific.md
[search-optimization]: /azure/search/search-performance-optimization/
[sql-backup]: /azure/sql-database/sql-database-automated-backups/
[sql-restore]: /azure/sql-database/sql-database-recovery-using-backups/
[traffic-manager]: /azure/traffic-manager/traffic-manager-overview/
[traffic-manager-routing]: /azure/traffic-manager/traffic-manager-routing-methods/
[vm-manage-availability]: /azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set
[vmss-autoscale]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview/

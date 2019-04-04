---
title: 為 Azure 設計復原應用程式
description: 如何在 Azure 中建置復原應用程式，如高可用性和災害復原。
author: MikeWasson
ms.date: 12/18/2018
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.custom: resiliency
ms.openlocfilehash: 7fd0e1bd42266b5e5718be4519352d99b58c0584
ms.sourcegitcommit: 644c2692a80e89648a80ea249fd17a3b17dc0818
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2019
ms.locfileid: "55987150"
---
# <a name="designing-resilient-applications-for-azure"></a>為 Azure 設計復原應用程式

在分散式系統中，將會發生失敗。 硬體可能會失敗。 網路可能會暫時性失敗。 整個服務或區域很少會發生中斷，但即使如此仍必須加以規劃。

在雲端中建立可靠的應用程式不同於在企業設定中建置可靠的應用程式。 儘管您在過去可能已購買更高階的硬體來相應增加，但在雲端環境中，您必須相應放大而不是相應增加。 透過使用商用硬體，可將雲端環境維持在較低的成本。 此目標不是試著完全避免失敗，而是將失敗在系統中的影響降至最低。

本文提供的概觀說明如何在 Microsoft Azure 中建置復原應用程式。 開頭會說明復原這個字詞的定義和相關的概念。 然後它會描述在應用程式存留期間使用結構化的方法達成復原的流程，從設計和實作到部署和作業。

<!-- markdownlint-disable MD026 -->

## <a name="what-is-resiliency"></a>復原是什麼？

<!-- markdownlint-enable MD026 -->

**復原**是系統從失敗中復原並繼續運作的能力。 它不是避免失敗，而是以避免停機或資料遺失的方式來回應失敗。 復原的目標是讓應用程式在失敗後完全回到正常運作的狀態。

復原的兩個重要層面是高可用性和災害復原。

- **高可用性** (HA)：可讓應用程式繼續在狀況良好狀態下執行，而不需要長期停機。 「狀況良好狀態」是指應用程式有回應，而且使用者可以連線到應用程式並與其互動。  
- **災害復原** (DR) 是從罕見但重大的事件中復原的能力，這些事件是非暫時性且規模廣泛的失敗，例如影響整個區域的服務中斷。 災害復原包括資料備份和封存，而且可能包括手動操作，例如從備份中還原資料庫。

思考 HA 與 DR 的方法之一，是當錯誤的影響超過 HA 設計可處理錯誤的能力時即啟動 DR。  

在設計復原功能時，您必須了解可用性需求。 可接受多少停機時間？ 這是有一部分是成本的功能。 可能的停機時間會造成多少業務成本？ 您應該將多少成本投資於此應用程式的高可用性？ 您也必須定義應用程式可用的意義。 例如，如果客戶可以提交訂單，但系統無法在一般時間範圍內處理，則應用程式是否為「停機」？ 也請考慮特定類型中斷的發生機率，以及因應措施是否符合成本效益。

另一個常見的字詞是**業務續航力** (BC)，這是指在不利的狀況 (例如天然災害或服務暫停) 期間和之後，執行重要商務功能的能力。 BC 涵蓋整個企業的作業，包括實體設備、人員、通訊、運輸和 IT。 本文著重於雲端應用程式，但必須在整體 BC 需求的內容中完成復原規劃。

**資料備份**是 DR 的重要部分。 如果應用程式的無狀態元件失敗，您隨時都可加以重新佈署。 但如果資料遺失，系統就無法回復穩定狀態。 您必須備份資料，而且為了免於全區域災害，最好是備份到不同區域。

備份與*資料複寫*不同。 資料複寫會近乎即時地複製資料，讓系統可以快速容錯移轉至複本。 許多資料庫系統都支援複寫，例如，SQL Server 就支援 SQL Server Always On 可用性群組。 資料複寫可藉由確保資料複本隨時做好準備，來減少從中斷復原所需的時間長度。 不過，資料複寫無法防範人為錯誤。 如果資料因為人為錯誤而損毀，損毀的資料就會複製到複本中。 因此，您仍需要在 DR 策略中包含長期備份措施。

## <a name="process-to-achieve-resiliency"></a>要達到復原的程序

復原並非附加元件。 它必須設計到系統並放入作業做法。 以下是需遵循的一般模型：

1. 以商務需求作為基礎，**定義**程式的可用性需求。
2. 針對復原**設計**應用程式。 從遵循經過證實之做法的架構開始，然後在該架構中找出可能的失敗點。
3. **實作**策略來偵測失敗並從中復原。
4. **測試**實作，方法是模擬錯誤及觸發強制容錯移轉。
5. 使用可靠的可重複執行程序，將應用程式**部署**到生產環境內。
6. **監視**應用程式來偵測失敗。 您可以藉由監視系統來評估應用程式的健康情況，並視需要回應事件。
7. 如果有需要手動介入的失敗就**回應**。

本文的其餘部分中，我們會深入討論每個步驟。

## <a name="define-your-availability-requirements"></a>定義您的可用性需求

復原計劃會從商業需求開始。 以下是在這些字詞中思考復原的一些方法。

### <a name="decompose-by-workload"></a>依工作負載分解

許多雲端解決方案包含多個應用程式工作負載。 「工作負載」一詞在此內容中是指不連續的功能或計算工作，可根據商務邏輯和資料儲存體需求，以邏輯方式與其他工作區隔。 例如，電子商務應用程式可能會包含下列工作負載：

- 瀏覽及搜尋產品類別目錄。
- 建立並追蹤訂單。
- 檢視建議。

這些工作負載在可用性、延展性、資料一致性和災害復原方面，可能有不同的需求。 您需擬定商務決策以在風險與成本之間取得平衡。

也請考慮使用方式模式。 必須使用系統時，是否有某些重要的期間？ 例如，報稅服務不能在歸檔期限前當機，影片串流服務在大型運動賽事期間必須維持運作等等。 在重要的期間，您可能會在數個區域有備援部署，所以如果一個區域失敗，應用程式可能就無法容錯移轉。 不過，多區域部署可能較昂貴，因此在較不重要的時間，您可以在單一區域中執行應用程式。 在某些情況下，您可以利用新式的無伺服器技術 (使用以耗用量為基礎的計費) 來減少額外支出，如此可避免對未充分利用的計算資源支付費用。

### <a name="rto-and-rpo"></a>RTO 和 RPO

所需考慮的兩個重要指標是復原時間目標和復原點目標，因為這兩者與災害復原有關。

- **復原時間目標** (RTO) 是應用程式在事件之後可能無法使用的最大可接受時間。 如果您的 RTO 為 90 分鐘，就必須能夠在災害開始的 90 分鐘內，將應用程式還原為執行狀態。 如果您的 RTO 非常低，就可以讓第二個區域部署持續以待命狀態執行主動/被動組態，從而防止區域性中斷。 在某些情況下，您可以部署主動/主動組態，以達到更低的 RTO。

- **復原點目標** (RPO) 是在災害期間可接受的最大資料遺失時間長度。 例如，如果您在單一資料庫中儲存資料，沒有複寫到其他資料庫，且每小時執行備份，可能會遺失最多一個小時的資料。

RTO 和 RPO 是系統中的非功能性需求，並且應該依照商務需求來設定。 若要衍生這些值，建議方法是進行風險評估，並清楚地了解停機或資料遺失的成本。

### <a name="mttr-and-mtbf"></a>MTTR 和 MTBF

可用性的其他兩個常見量值是平均復原時間 (MTTR) 和平均失敗時間 (MTBF)。 服務提供者通常會在內部使用這些量值，以決定在何處將備援新增至雲端服務，以及為客戶提供哪些 SLA。

**平均復原時間** (MTTR) 是在失敗後還原元件所花費的平均時間。 MTTR 是有關元件的經驗事實。 您可以根據每個元件的 MTTR，來評估整個應用程式的 MTTR。 若從多個具有低 MTTR 值的元件建置應用程式，應用程式的整體 MTTR 值就會很低 &mdash; 如此可快速地從失敗中復原。

**平均失敗時間** (MTBF) 是合理預期元件能夠在各中斷之間持續運作的執行時間。 此計量可協助您計算服務將變成無法使用的頻率。 不可靠的元件會有低 MTBF，並導致該元件有低的 SLA 數目。 不過，低 MTBF 可以藉由部署多個元件執行個體，並在這些執行個體之間實作容錯移轉，來解決此問題。

> [!NOTE]
> 如果高可用性設定中的元件有任一 MTTR 值超出系統的 RTO，則系統中的失敗會導致無法接受的業務中斷。 而且無法在定義的 RTO 內還原系統。

### <a name="slas"></a>SLA

在 Azure 中，[服務等級協定][sla] (SLA) 描述 Microsoft 對執行時間與連線能力的承諾。 如果特定服務的 SLA 是 99.9%，表示您應該預期 99.9% 的時間皆可提供服務。

> [!NOTE]
> 如果不符合 SLA，Azure SLA 也包括取得服務退費的佈建，以及每個服務的明確定義「可用性」。 SLA 的這個部分會作為強制執行原則。

您應該在解決方案中針對每個工作負載定義自己的目標 SLA。 SLA 可讓您評估架構是否符合商務需求。 執行相依性對應練習以識別內部和外部相依性，例如 Active Directory 或第三方服務，例如付款提供者或電子郵件訊息服務。 特別是，要注意可能是單一失敗點或是在事件期間造成瓶頸的任何外部相依性。 例如，如果工作負載需要 99.99% 的執行時間，但需根據具有 99.9 % SLA 的服務而定，該服務就不能是系統中的單一失敗點。 萬一服務失敗時，一個補救是指一個後援的路徑，或採取其他措施從該服務的失敗中復原。

下表顯示各種 SLA 層級的潛在累計停機時間。

| SLA | 每週停機時間 | 每月停機時間 | 每年停機時間 |
| --- | --- | --- | --- |
| 99% |1.68 小時 |7.2 小時 |3.65 天 |
| 99.9% |10.1 分鐘 |43.2 分鐘 |8.76 小時 |
| 99.95% |5 分鐘 |21.6 分鐘 |4.38 小時 |
| 99.99% |1.01 分鐘 |4.32 分鐘 |52.56 分鐘 |
| 99.999% |6 秒 |25.9 秒 |5.26 分鐘 |

當然，可用性越高越好，其他所有項目則相同。 但是，隨著您尋求更多的 9，達到該可用性層級的成本與複雜性也會隨之提高。 99.99% 的執行時間相當於每個月的總停機時間約 5 分鐘。 是否值得額外的複雜性和成本來達到五個 9？ 答案需視商務需求而定。

以下是定義 SLA 時的一些其他考量：

- 若要達到四個 9 (99.99%)，您可能無法依賴手動介入來從失敗中復原。 應用程式必須是自我診斷及自我修復。
- 超過四個 9 時，就不容易以夠快的速度偵測到中斷狀況來符合 SLA。
- 請思考您 SLA 測量根據的時間間隔。 間隔越小，容錯度就更緊密。 根據每小時或每日執行時間來定義 SLA 可能沒有意義。
- 考慮 MTBF 和 MTTR 量值。 您的 SLA 愈低，服務的效能就越不會降低，且服務可更快復原。

### <a name="composite-slas"></a>複合 SLA

請考慮將寫入 Azure SQL Database 中的 App Service web 應用程式。 在撰寫本文時，這些 Azure 服務都有下列 SLA：

- App Service Web Apps = 99.95%
- SQL Database = 99.99%

![複合 SLA](./images/sla1.png)

您預期這個應用程式的停機時間最長是多久？ 如果其中一項服務失敗，整個應用程式就會失敗。 一般情況下，每個服務的失敗機率互相無關，所以這個應用程式的複合 SLA 是 99.95% &times; 99.99% = 99.94%。 這低於個別的 SLA，但並不令人意外，因為仰賴多個服務的應用程式會有多個可能的失敗點。

另一方面，您可以藉由建立獨立的後援路徑來改善複合 SLA。 例如，如果 SQL Database 無法使用，請將交易放入佇列，以供稍後處理。

![複合 SLA](./images/sla2.png)

透過這個設計，即使在無法連線到資料庫的情況下，應用程式還是可以使用。 不過，如果資料庫與佇列同時失敗，它就會失敗。 預期的同時失敗時間百分比是 0.0001 &times; 0.001，因此這個合併路徑的複合 SLA 是：  

- 資料庫或佇列 = 1.0 &minus; (0.0001 &times; 0.001) = 99.99999%

複合 SLA 總計是：

- Web 應用程式和 (資料庫或佇列) = 99.95% &times; 99.99999% = ~99.95%

但這種方法有其優缺點。 應用程式邏輯更複雜、您需支付佇列，並且可能要考量資料一致性的問題。

**多區域部署的 SLA**。 另一項高可用性技術是將應用程式部署在多個區域中，且如果應用程式在一個區域中失敗，就使用 Azure Traffic Manager 進行容錯移轉。 針對多區域部署，複合 SLA 的計算方式如下。

N 代表應用程式 (部署在一個區域中) 的複合 SLA，R 代表其中部署應用程式的區域數目。 應用程式在所有區域同時失敗的預期機率為 ((1 &minus; N) ^ R)。

例如，如果單一區域的 SLA 是 99.95%，

- 兩個區域的合併 SLA = (1 &minus; (0.9995 ^ 2)) = 99.999975%
- 四個區域的合併 SLA = (1 &minus; (0.9995 ^ 4)) = 99.999999%

您也必須納入[適用於流量管理員的 SLA][tm-sla]。 在撰寫本文時，流量管理員 SLA 的 SLA 為 99.99%。

此外，容錯移轉不是在主動-被動組態中瞬間完成，這會在容錯移轉期間導致停機一段時間。 請參閱[流量管理員端點監視和容錯移轉][tm-failover]。

計算的 SLA 數目是實用的基準，但不會顯示有關可用性的整體內容。 通常，當非關鍵路徑失敗時，應用程式可能會依正常程序降級。 請考慮顯示書籍目錄的應用程式。 如果應用程式無法擷取封面的縮圖映像，它可能會顯示預留位置映像。 在此情況下，雖然會影響使用者體驗，但無法取得映像並不會減少應用程式的執行時間。  

## <a name="design-for-resiliency"></a>針對復原而設計

在設計階段中，您應該執行失敗模式分析 (FMA)。 FMA 的目標是要找出失敗的可能點，並定義應用程式回應這些失敗的方式。

- 應用程式會如何偵測此類型的失敗？
- 應用程式會如何回應此類型的失敗？
- 您會如何記錄和監視此類型的失敗？

如需包含適用於 Azure 之特定建議事項的 FMA 程序相關資訊，請參閱 [Azure 復原指引：失敗模式分析][fma]。

### <a name="example-of-identifying-failure-modes-and-detection-strategy"></a>識別失敗模式和偵測策略的範例

**失敗點：** 呼叫外部 Web 服務/API。

| 失敗模式 | 偵測策略 |
| --- | --- |
| 服務停用 |HTTP 5xx |
| 節流 |HTTP 429 (太多要求) |
| Authentication |HTTP 401 (未經授權) |
| 回應變慢 |要求逾時 |

### <a name="redundancy-and-designing-for-failure"></a>針對失敗的備援措施和設計

失敗的影響範圍各不相同。 有些硬體失敗 (例如失敗的磁碟) 可能會影響單一主機電腦。 失敗的網路交換器則可能影響整個伺服器機架。 較不常見的失敗是整個資料中心中斷，例如資料中心斷電。 至於整個區域都變得無法使用的情況則很罕見。

其中一個讓應用程式具有復原能力的主要方法是透過備援。 但您必須在設計應用程式時規劃此備援措施。 此外，您需要的備援層級取決於您的業務需求，並非每個應用程式都需要跨區域備援，以防範區域性中斷。 一般情況下，您需要在更好的備援性和可靠性與更高的成本和複雜性之間做出取捨。  

Azure 有許多功能可讓應用程式具有每個失敗層級的備援能力，從個別 VM 至整個區域都有。

![Azure 復原功能](./images/redundancy.svg)

**單一 VM**。 Azure 可為單一 VM 提供[執行時間 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines)。 (VM 必須針對所有作業系統磁碟和資料磁碟使用進階儲存體)。雖然您可以透過執行兩個以上的 VM 來獲得更高的 SLA，但對於某些工作負載來說，單一 VM 或許就已足夠。 至於生產工作負載，我們建議使用兩個以上的 VM 來作為備援。

**可用性集合**。 若要防範局部硬體失敗 (例如，磁碟或網路交換器失敗)，請在可用性設定組中部署兩個以上的 VM。 可用性設定組包含兩個以上的「容錯網域」，這些網域會共用電力來源和網路交換器。 可用性設定組中的 VM 會分散於這些容錯網域中，因此如果某個硬體失敗影響其中一個容錯網域，網路流量仍可路由傳送至其他容錯網域中的 VM。 如需可用性設定組的詳細資訊，請參閱[管理 Azure 中 Windows 虛擬機器的可用性](/azure/virtual-machines/windows/manage-availability)。

**可用性區域**。  可用性區域實際上是 Azure 地區內的個別區域。 每個可用性區域各有不同的電力來源、網路和冷卻系統。 跨可用性區域部署 VM 可協助應用程式防範全資料中心的失敗。 並非所有區域都支援可用性區域。 如需支援的區域和服務清單，請參閱[什麼是 Azure 中的可用性區域？](/azure/availability-zones/az-overview)。

如果您打算在部署中使用可用性區域，請先確認您的應用程式架構和程式碼基底可支援此設定。 如果您要部署商規品軟體，請洽詢軟體廠商，並在部署到生產環境之前進行適當的測試。 應用程式必須能夠維護狀態，並避免在已設定的區域內發生中斷時造成資料遺失。 應用程式必須能夠在彈性且分散的基礎結構中執行，且程式碼基底中沒有指定硬式編碼的基礎結構元件。

**Azure Site Recovery**。  將 Azure 虛擬機複寫到另一個 Azure 區域，以實現商務持續性和災害復原。 您可以進行定期 DR 鑽研，以確保符合合規性需求。 會以指定的設定將 VM 複寫到選取的區域，以便您可以在來源區域的中斷事件中復原應用程式。 如需詳細資訊，請參閱 [使用 ASR 複寫 Azure VM][site-recovery]。 請考慮您此處解決方案的 RTO 和 RPO 數字，並在測試時確定復原時間和復原點適用於您的需求。

**配對的區域**。 若要協助應用程式防範區域性中斷，您可以將應用程式部署至多個區域，並使用 Azure 流量管理員將網際網路流量分散到不同區域。 每個 Azure 區域都會與另一個區域配對。 這些區域集合在一起就構成了[區域性配對](/azure/best-practices-availability-paired-regions)。 區域性配對會位於相同的地理位置內 (巴西南部除外)，以符合資料常駐地之稅務和執法管轄區的要求。

在設計多區域應用程式時，請將跨區域之網路延遲會大於單一區域的情形納入考量。 例如，如果您要複寫資料庫來啟用容錯移轉，請在某區域中使用同步資料複寫，但在跨區域時使用非同步資料複寫。

選取配對區域時，請確保這兩個區域都具有所需的 Azure 服務。 如需依區域提供的服務清單，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)。 為災害復原選取正確的部署拓撲也很重要，特別是當您的 RPO/RTO 很短的情況下。 若要確保容錯移轉區域有足夠的容量來支援您的工作負載，請選取主動/被動 (完整複本) 拓撲或主動/主動拓撲。 請記住，這些部署拓撲可能會增加複雜度和成本，因為次要區域中的資源是預先佈建的，且可能處於閒置狀態。 如需詳細資訊，請參閱[災害復原的部署拓撲][deployment-topologies]

| &nbsp; | 可用性設定組 | 可用性區域 | Azure Site Recovery/配對的區域 |
|--------|------------------|-------------------|---------------|
| 失敗原因 | 機架 | 資料中心 | 區域 |
| 要求路由 | 負載平衡器 | 跨區域負載平衡器 | 流量管理員 |
| 網路延遲 | 非常低 | 低 | 中到高 |
| 虛擬網路  | VNet | VNet | 跨區域 VNet 對等互連 |

## <a name="implement-resiliency-strategies"></a>實作復原策略

本節提供一些常見復原策略的問卷調查。 這些大多數並不限於特定技術。 本節的描述彙總每一種技巧的一般概念，並提供進一步閱讀的連結。

**重試暫時性失敗**。 可能會因暫時遺失網路連線、卸除資料庫連線或服務忙碌時的逾時而造成暫時性失敗。 通常，只需重試要求就可以解決暫時性失敗。 對於許多 Azure 服務，用戶端 SDK 會透過對呼叫端透明的方式實作自動重試；請參閱[重試服務的特定指引][retry-service-specific guidance]。

每次重試都會新增至總延遲。 此外，失敗的要求過多可能會造成瓶頸，因為擱置中的要求會累積在佇列中。 這些已封鎖的要求可能會佔據重要的系統資源，例如記憶體、執行緒、資料庫連線等等，可能會造成串聯式失敗。 若要避免這個問題，請在每次重試之間增加延遲，並限制失敗的要求總數。

![重試嘗試的圖表](./images/retry.png)

**在執行個體之間進行負載平衡**。 就延展性而言，雲端應用程式應該能夠藉由新增更多執行個體來相應放大。 這種方法也會改善復原，因為狀況不良的執行個體可從旋轉中移除。 例如︰

- 將兩個或多個 VM 放在負載平衡器後。 負載平衡器會散發所有 VM 的流量。 請參閱[執行負載平衡的 VM 以獲得延展性和可用性][ra-multi-vm]。
- 將 Azure App Service 應用程式相應放大至多個執行個體。 App Service 會自動平衡執行個體之間的負載。 請參閱[基本 Web 應用程式][ra-basic-web]。
- 使用 [Azure 流量管理員][tm]來散發一組端點之間的流量。

**複寫資料**。 複寫資料是處理資料存放區中非暫時性失敗的一般策略。 許多儲存體技術都會提供內建的複寫，包括 Azure 儲存體、Azure SQL Database、Cosmos DB 和 Apache Cassandra。 請務必考慮讀取和寫入的路徑。 根據儲存體技術，您可能有多個可寫入複本，或單一可寫入複本及多個唯讀複本。

若要將可用性最大化，可將複本放在多個區域。 不過，這會在複寫資料時增加延遲。 一般而言，跨區域複寫會以非同步方式進行，這表示複本失敗時，會有最終一致性模型和資料遺失。

您可以使用 [Azure Site Recovery][site-recovery]，將 Azure 虛擬機器從一個區域複寫到另一個區域。 Site Recovery 持續將資料複寫至目標區域。 當您的主要站台發生中斷時，您會容錯移轉到次要位置

**正常降級**。 如果服務失敗且沒有容錯移轉路徑，應用程式就可以正常降級，但仍會提供可接受的使用者體驗。 例如︰

- 將工作項目放入佇列，以供稍後處理。
- 傳回預估的值。
- 使用本機快取的資料。
- 向使用者顯示錯誤訊息。 (這個選項優於讓應用程式停止回應要求。)

**節流處理大量的使用者**。 有時候，少數使用者會建立過多的負載。 這可能會對其他使用者造成影響，從而減少您應用程式的整體可用性。

當單一用戶端的要求數目過多時，應用程式可能會將用戶端節流一段時間。 在節流期間，應用程式會拒絕來自該用戶端的部分或所有要求 (取決於實際的節流策略)。 節流的臨界值可能會取決於客戶的服務層。

節流並不表示用戶端一定有惡意行動，僅表示它超過其服務配額。 在某些情況下，取用者可能會持續超過其配額或是行為不當。 在此情況下，您可能會繼續執行並封鎖使用者。 一般而言，這是透過封鎖 API 金鑰或 IP 位址範圍來完成。 如需詳細資訊，請參閱 [Throttling Pattern (分節流模式)][throttling-pattern]。

**使用斷路器**。 [斷路器][circuit-breaker-pattern]模式可防止應用程式重複嘗試很可能會失敗的作業。 斷路器會包裝對服務呼叫，並會追蹤最近的失敗數目。 如果失敗計數超過臨界值，則斷路器會開始傳回錯誤碼而不呼叫服務。 這可讓服務有時間復原。

**使用負載均衡來消除流量尖峰**。
應用程式可能會突然出現流量尖峰，可能會在後端灌爆服務。 如果後端服務回應要求的速度不夠快，可能會造成要求排入佇列 (備份)，或導致服務節流應用程式。 若要避免這個問題，您可以使用佇列作為緩衝區。 沒有新的工作項目時，並非立即呼叫後端服務，而是應用程式會佇列工作項目，以非同步的方式執行。 佇列會作為消除負載尖峰的緩衝區。 如需詳細資訊，請參閱[佇列型負載調節模式][load-leveling-pattern]。

**隔離重要的資源**。 一個子系統中的失敗有時候可能會重疊，導致應用程式其他部分失敗。 如果失敗造成某些資源 (例如執行緒或通訊端) 無法即時釋出，而導致資源耗盡，就會發生這個情況。

若要避免這個問題，您可以將系統分割為隔離群組，讓一個分割區中的失敗不會使整個系統當機。 這項技術通常稱為[隔艙模式][bulkhead-pattern]。

範例：

- 資料資料庫 (例如，依由租用戶)，並針對每個分割區指派 web 伺服器執行個體的個別集區。  
- 使用個別執行緒集區將呼叫隔離至不同的服務。 如果其中一個服務失敗，這有助於防止階層式失敗。 如需範例，請參閱 Netflix [Hystrix 文件庫][hystrix]。
- 使用[容器][containers]來限制特定子系統的可用資源。

![隔艙模式的圖表](./images/bulkhead.png)

**套用補償交易**。 [補償交易][compensating-transaction-pattern]是將另一個已完成交易的影響復原的交易。 在分散式系統中，要達到強式交易一致性非常困難。 補償交易是達到一致性的方式，方法為使用一系列可以在每個步驟中復原的較小型個別交易。

例如，若要預訂旅程，客戶可能要預約汽車、旅館房間和班機。 如果其中任何步驟失敗，整個作業就會失敗。 您並非在整個作業中嘗試使用單一的分散式交易，而是可以定義每個步驟的補償交易。 例如，若要復原汽車預約，您要取消此預約。 若要完成整個作業，協調員會執行每個步驟。 如果任何步驟失敗，協調員會套用補償交易來復原任何已完成的步驟。

## <a name="test-for-resiliency"></a>針對復原的測試

一般而言，您無法使用與您測試應用程式功能 (藉由執行單元測試等等) 的方式相同來測試復原。 相反地，您必須測試端對端工作負載在失敗情況下的執行方式，這只會間歇性發生。

測試是反覆的程序。 測試應用程式、測量結果、分析及解決所發生的任何失敗，並重複此程序。

**錯誤插入式測試**。 在失敗期間測試系統的復原，方法為觸發實際的失敗或加以模擬。 以下是一些要測試的常見失敗案例：

- 關閉 VM 執行個體。
- 毀損程序。
- 憑證到期。
- 變更存取金鑰。
- 關閉網域控制站上的 DNS 服務。
- 限制可用的系統資源，例如 RAM 或執行緒的數目。
- 取消掛接磁碟。
- 重新部署 VM。

測量復原時間，並確認已符合您的業務需求。 此外，也需測試失敗模式組合。 請確定失敗不會重疊，且會以隔離的方式處理。

這是分析設計階段期間可能的失敗點另一個重要的原因。 該分析的結果應該輸入您的測試計劃。

**負載測試**. 負載測試對於識別只有在負載下發生的失敗而言十分重要，例如後端資料庫遭灌爆或服務節流。 使用生產資料或盡可能最接近生產資料的綜合資料，針對尖峰負載進行測試。 目標是查看應用程式在真實世界情況下的運作方式。

**災害復原鑽研**。 如果您有一個好的災害復原方案是不夠的。 您需要定期測試，以確保您的復原方案在需要時可以正常運作。 針對 Azure 虛擬機器，您可以使用 [Azure Site Recovery][site-recovery] 複寫並[執行 DR 鑽研][site-recovery-test-failover]，而不會影響生產應用程式或進行中的複寫。

## <a name="deploy-using-reliable-processes"></a>使用可靠的程序部署

一旦應用程式部署到生產環境後，更新就會成為可能的錯誤來源。 在最糟的情況下，不良的更新可能會導致停機時間。 若要避免這個問題，部署程序必須是可預測且可重複。 部署包括佈建 Azure 資源、部署應用程式的程式碼，以及套用組態集。 更新可能需要這三個全部或一個子集。

重點是手動部署容易出錯。 因此，建議具有自動化的等冪程序，您可以視需要執行，並在失敗時重新執行。

* 若要自動佈建 Azure 資源，您可以使用 [Terraform][terraform]、[Ansible][ansible]、[Chef][chef]、[Puppet][puppet]、[PowerShell][powershell]、[CLI][cli] 或 [Azure Resource Manager 範本][template-deployment]
* 使用 [Azure 自動化期望狀態設定][dsc] (DSC) 來設定 VM。 針對 Linux VM，您可以使用[Cloud-init][cloud-init]。
* 您可以使用 [Azure DevOps Services][azure-devops-services] 或 [Jenkins][jenkins] 自動化應用程式部署。

與復原部署相關的兩個概念是「基礎結構即程式碼」和「不可變的基礎結構」。

- **基礎結構即程式碼**是使用程式碼來佈建及設定基礎結構的做法。 基礎結構即程式碼可以使用宣告式方法或命令式方法 (或兩者的組合)。 Resource Manager 範本便是宣告式方法的其中一個範例。 PowerShell 指令碼是命令式方法的其中一個範例。
- **不可變的基礎結構**是您在將基礎結構部署至生產環境之後不應該修改的準則。 否則，就可以進入已套用臨機操作變更的狀態，因此很難完全了解變更的內容，而且難以理解系統。

另一個問題是如何推出應用程式更新。 建議使用諸如藍綠部署或 Canary 版本等技術，可透過高度控制的方式，將錯誤部署可能造成的影響降至最低。

- [藍綠部署][blue-green]是將更新部署到獨立於即時應用程式之生產環境中的一種技術。 驗證部署之後，將流量路由切換為更新的版本。 例如，Azure App Service Web Apps 可利用[預備位置][staging-slots]來加以啟用。
- [Canary 版本][canary-release]類似於藍綠部署。 您不需切換更新版本的所有流量，而是將更新發行至一小部分的使用者，方法是將部分的流量路由到新的部署。 如果有問題，請退出並還原成舊的部署。 否則，將更多流量路由至新的版本，直到它取得 100% 的流量。

不論您採取的方法為何，萬一新的版本無法正常運作，請確定您可以復原至最後一個已知的良好部署。 並且也採用一種策略來回復資料庫變更和對相依服務的任何變更。 如果發生錯誤，應用程式記錄必須指出是哪一個版本造成錯誤。

## <a name="monitor-to-detect-failures"></a>監視以偵測失敗
監視對於復原至關重要。 如果發生失敗，您必須知道該失敗，且需要深入了解失敗的原因。 

監視大規模分散式系統會是重大挑戰。 試想在數十個 VM 上執行的應用程式 &mdash; 以一次一個的方式登入每個 VM，然後查看記錄檔並嘗試針對問題進行疑難排解，這並不實際。 此外，VM 執行個體的數量可能不是靜態的，VM 會隨著應用程式相應縮小和相應放大而新增及移除，執行個體偶爾可能會失敗，且需要重新佈健。 此外，典型的雲端應用程式可能會使用多個資料存放區 (Azure 儲存體、SQL Database、Cosmos DB、Redis 快取)，且單一使用者動作可能會跨越多個子系統。 

您可以將監視程序視為具有數個不同階段的管線：

![複合 SLA](./images/monitoring.png)

* **檢測**。 用於監視的未經處理資料來自各種來源，包括[應用程式記錄檔](/azure/application-insights/app-insights-overview?toc=/azure/azure-monitor/toc.json)、[作業系統效能度量](/azure/azure-monitor/platform/agents-overview)、[Azure 資源](/azure/monitoring-and-diagnostics/monitoring-supported-metrics?toc=/azure/azure-monitor/toc.json)、[Azure 訂用帳戶](/azure/service-health/service-health-overview)和 [Azure 租用戶](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)。 大部分的 Azure 服務會公開[計量](/azure/azure-monitor/platform/data-collection)，您可以設定這些計量來分析和判斷問題的原因。
* **收集和儲存**。 未經處理的檢測資料可以保留在各種不同的位置以及使用各種格式 (例如，應用程式追蹤記錄、IIS 記錄、效能計數器)。 這些不同的來源會加以收集、彙總，並放入可靠的資料存放區，例如 Application Insights、Azure 監視器計量、服務健康狀態、儲存體帳戶和 Log Analytics。
* **分析及診斷**。 將資料彙總到這些不同的資料存放區之後，可以加以分析來針對問題進行疑難排解，並提供應用程式健康情況的整體檢視。 一般而言，您可以使用 [Kusto 查詢](/azure/log-analytics/log-analytics-queries)在 Application Insights 和 Log Analytics 中搜尋資料。 Azure Advisor 提供的建議著重[復原](/azure/advisor/advisor-high-availability-recommendations)和[最佳化](/azure/advisor/advisor-performance-recommendations)。 
* **視覺效果和警示**。 在這個階段，遙測資料所呈現的方式，能讓操作員快速注意到問題和趨勢。 範例包括儀表板或電子郵件警示。 使用 [Azure 儀表板](/azure/azure-portal/azure-portal-dashboards)，您可以建置一個半透明效果單一窗格的監視圖形，其來自 Application Insights、Log Analytics、Azure 監視器計量和服務健康狀態。 使用 [Azure 監視器警示](/azure/monitoring-and-diagnostics/monitoring-overview-alerts?toc=/azure/azure-monitor/toc.json)，您可以建立警示服務健康狀態和資源健康狀態。

監視與失敗偵測不相同。 例如，您的應用程式可能會偵測到暫時性錯誤然後重試，導致無停機時間。 但是，它應該也記錄重試作業，好讓您可以監視錯誤率，以取得整體的應用程式健康情況。

應用程式記錄是診斷資料的重要來源。 應用程式記錄的最佳做法包括：

- 登入生產環境。 否則，您在最需要的部分就無法深入解析。
- 記錄服務界限上的事件。 包含流過服務界限的相互關聯識別碼。 如果交易流經多個服務且其中一個失敗，相互關聯識別碼可協助您準確地確定交易失敗的原因。
- 使用語意記錄，也稱為結構化記錄。 非結構化記錄會讓您難以將雲端規模所需的記錄資料之耗用量及分析自動化。
- 使用非同步記錄。 否則，記錄系統本身會造成要求備份而導致應用程式失敗，因為它們在等候寫入記錄事件時會予以封鎖。
- 應用程式記錄與稽核不同。 可就合規性或法規原因完成稽核。 因此，稽核資料列必須完成，且不得在處理交易時卸除任何稽核資料列。 如果應用程式需要稽核，這應該與診斷記錄有所區隔。

如需監視和診斷的詳細資訊，請參閱[監視和診斷指引][monitoring-guidance]。

## <a name="respond-to-failures"></a>回應失敗
前幾節著重於自動化復原策略，對於高可用性是很重要的。 不過，有時需要手動介入。

* **警示**。 監視您的應用程式，注意可能需要主動介入的跡象。 例如，如果您看見 SQL Database 或 Cosmos DB 一致地節流您的應用程式，可能就需要增加您的資料庫容量或最佳化查詢。 在此範例中，即使應用程式可能會以透明的方式節流錯誤，您的遙測仍應發出警示，讓您可以追蹤。 建議您根據服務限制和配額閾值，來設定 Azure 資源計量和診斷記錄的警示。 我們建議設定計量的警示，因其與診斷記錄相比有較低的延遲性。 此外，Azure 可以透過[資源健康狀態](https://docs.microsoft.com/en-us/azure/service-health/resource-health-checks-resource-types)來提供一些立即可用的健康狀態，這有助於診斷 Azure 服務的節流。    
* **容錯移轉**。 為應用程式設定災害復原策略。 適合的策略取決於您的 SLA。 在某些情況下，實作主動-被動就足夠了。 如需詳細資訊，請參閱[災害復原的部署拓撲](./disaster-recovery-azure-applications.md#deployment-topologies-for-disaster-recovery)。 大部分的 Azure 服務允許手動或自動容錯移轉。 例如，IaaS 應用程式中，對 Web 和邏輯層使用 [Azure Site Recovery](/azure/site-recovery/azure-to-azure-architecture)，對資料庫層使用 [SQL AlwaysOn 可用性群組](/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr)。 [流量管理員](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-overview)提供跨區域自動容錯移轉。
* **作業整備測試**。 同時對次要區域的容錯移轉和對主要區域的容錯回復執行作業整備測試。 許多 Azure 服務支援手動容錯移轉或測試災害復原演練的容錯移轉。 或者，您可以藉由關閉或移除服務來模擬中斷。
* **資料一致性檢查**。 如果失敗發生在資料存放區中，當存放區再次可用時，可能會有資料不一致的情況，特別是在已複寫資料時。 對於提供跨區域複寫的 Azure 服務，請查看 RTO 和 RPO 以了解失敗導致的預期資料遺失。 檢閱 Azure 服務的 SLA，以了解跨區域容錯移轉是否可以手動起始或由 Microsoft 起始。 對於某些服務，Microsoft 會決定何時執行容錯移轉。 Microsoft 可能會優先考慮復原主要區域中的資料，如果主要區域中的資料視為無法復原，則僅容錯移轉到次要區域。 例如，[異地備援儲存體](/azure/storage/common/storage-redundancy-grs)和 [Key Vault](/azure/key-vault/key-vault-disaster-recovery-guidance) 均遵循此模型。
* **從備份還原**。 在某些情況下，只有在相同區域內才能從備份還原。 [Azure VM 備份](/azure/backup/backup-azure-vms-first-look-arm) 就是屬於這種情況。 其他 Azure 服務提供異地複寫備份，例如[Redis 快取異地複寫](/azure/redis-cache/cache-how-to-geo-replication)。 備份的目的是為了防止意外刪除或損毀資料，以及將應用程式還原到稍早的功能版本。 因此，雖然備份可以在某些情況下做為災害復原解決方案，但反之並非總是如此：災害復原無法防止您意外刪除或損毀資料。  

記錄並測試災害復原計畫。 評估應用程式失敗的商務影響。 盡可能自動化程序並記錄任何手動步驟，例如手動容錯移轉或從備份還原資料。 定期測試您的災害復原程序來驗證和改善計劃。 為應用程式所使用的 Azure 服務設定警示。

## <a name="summary"></a>總結
本文以整體的觀點討論復原，強調一些雲端的唯一挑戰。 這些包括雲端運算的散發本質、使用商用硬體，以及暫時性網路錯誤的存在。

以下是本文強調的幾個重點：

- 復原會帶來較高的可用性，且從失敗中復原的平均時間較少。
- 在雲端中達到復原需要傳統內部部署解決方案中一組不同的技術。
- 復原不會意外發生。 它必須從頭設計並內建。
- 復原會觸及應用程式生命週期的每個部分，從計劃與撰寫程式碼到作業。
- 測試與監視！

<!-- links -->

[blue-green]: https://martinfowler.com/bliki/BlueGreenDeployment.html
[canary-release]: https://martinfowler.com/bliki/CanaryRelease.html
[circuit-breaker-pattern]: ../patterns/circuit-breaker.md
[compensating-transaction-pattern]: ../patterns/compensating-transaction.md
[containers]: https://en.wikipedia.org/wiki/Operating-system-level_virtualization
[dsc]: /azure/automation/automation-dsc-overview
[contingency-planning-guide]: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-34r1.pdf
[fma]: ./failure-mode-analysis.md
[hystrix]: https://medium.com/netflix-techblog/introducing-hystrix-for-resilience-engineering-13531c1ab362
[jmeter]: https://jmeter.apache.org/
[load-leveling-pattern]: ../patterns/queue-based-load-leveling.md
[monitoring-guidance]: ../best-practices/monitoring.md
[ra-basic-web]: ../reference-architectures/app-service-web-app/basic-web-app.md
[ra-multi-vm]: ../reference-architectures/virtual-machines-windows/multi-vm.md
[checklist]: ../checklist/resiliency.md
[retry-pattern]: ../patterns/retry.md
[retry-service-specific guidance]: ../best-practices/retry-service-specific.md
[sla]: https://azure.microsoft.com/support/legal/sla/
[throttling-pattern]: ../patterns/throttling.md
[tm]: https://azure.microsoft.com/services/traffic-manager/
[tm-failover]: /azure/traffic-manager/traffic-manager-monitoring
[tm-sla]: https://azure.microsoft.com/support/legal/sla/traffic-manager
[site-recovery]: /azure/site-recovery/azure-to-azure-quickstart/
[site-recovery-test-failover]: /azure/site-recovery/azure-to-azure-tutorial-dr-drill/
[site-recovery-failover]: /azure/site-recovery/azure-to-azure-tutorial-failover-failback/
[deployment-topologies]: ./disaster-recovery-azure-applications.md#deployment-topologies-for-disaster-recovery
[bulkhead-pattern]: ../patterns/bulkhead.md
[terraform]: /azure/virtual-machines/windows/infrastructure-automation#terraform
[ansible]: /azure/virtual-machines/windows/infrastructure-automation#ansible
[chef]: /azure/virtual-machines/windows/infrastructure-automation#chef
[puppet]: /azure/virtual-machines/windows/infrastructure-automation#puppet
[template-deployment]: /azure/azure-resource-manager/resource-group-overview#template-deployment
[cloud-init]: /azure/virtual-machines/windows/infrastructure-automation#cloud-init
[azure-devops-services]: /azure/virtual-machines/windows/infrastructure-automation#azure-devops-services
[jenkins]: /azure/virtual-machines/windows/infrastructure-automation#jenkins
[staging-slots]: /azure/app-service/deploy-staging-slots
[powershell]: /powershell/azure/overview
[cli]: /cli/azure

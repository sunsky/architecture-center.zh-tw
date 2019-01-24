---
title: 快取指引
titleSuffix: Best practices for cloud applications
description: 用來改善效能和延展性的快取指引。
author: dragon119
ms.date: 05/24/2017
ms.topic: best-practice
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: b93041d87ec1edde91724f6cf6374cb00b8a4941
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54488489"
---
# <a name="caching"></a>快取

快取是改善系統的效能和延展性的常用技術。 這樣做的方法是暫時將經常存取的資料複製到所在位置接近應用程式的快速儲存體中。 如果這個快速資料儲存體比原始來源的位置更接近應用程式，則快取可以透過更快速地提供資料，大幅改善用戶端應用程式的回應時間。

當應用程式執行個體重複讀取相同資料時，快取是最有效率的做法，特別是在原始資料存放區符合下列所有條件時：

- 它會保持相對靜態。
- 相較於快取速度，它的速度較慢。
- 受限於高階爭用。
- 當網路延遲導致存取速度變慢時它位於很遠的地方。

## <a name="caching-in-distributed-applications"></a>分散式應用程式中的快取

當快取資料有下列情況時，分散式應用程式通常會實作下列兩個策略或其中之一：

- 使用私人快取，其中資料會保留於執行應用程式或服務執行個體的本機電腦上。
- 使用共用快取，作為可由多個處理程序和/或電腦存取的常見來源。

在這兩種情況下，快取可以在用戶端和/或伺服器端上執行。 用戶端快取是透過為系統提供使用者介面的處理程序來完成，例如網頁瀏覽器或傳統型應用程式。 伺服器端快取是透過提供從遠端執行的商務服務的處理程序來完成。

### <a name="private-caching"></a>私人快取

最基本的快取類型是記憶體內部存放區。 其會保留於單一處理程序的位址空間中，且可由該處理程序中執行的程式碼直接存取。 此快取類型可進行非常快速的存取。 它也可以提供非常有效的方法來儲存適度的靜態資料量，因為快取大小通常受限於裝載處理程序之電腦上可用的記憶體使用量。

如果您需要快取比記憶體中實際可用更多的資訊，您可以將快取資料寫入本機檔案系統。 這會比存取保留在記憶體內部的資料更慢，但應該還是會比透過網路擷取資料更快且更可靠。

如果您同時執行多個使用此模型的應用程式執行個體，每個應用程式執行個體都會有自己獨立的快取，並保留它自己的資料複本。

將快取視為過去某個時間點的原始資料快照。 如果此資料並非靜態，很可能不同的應用程式執行個體會在其快取中保留不同版本的資料。 因此，這些執行個體所執行的相同查詢就會傳回不同的結果，如圖 1 所示。

![在不同應用程式執行個體中使用記憶體中快取](./images/caching/Figure1.png)

*圖 1：在不同應用程式執行個體中使用記憶體中快取。*

### <a name="shared-caching"></a>共用快取

使用共用快取有助於減少每個快取中可能有不同資料的疑慮，這種情況會在記憶體內部快取中發生。 共用快取可確保不同的應用程式執行個體會看到相同的快取資料檢視。 這樣做的方法是尋找個別位置中的快取，其通常會裝載為個別服務的一部分，如圖 2 所示。

![使用共用快取](./images/caching/Figure2.png)

*圖 2：使用共用快取。*

共用快取方式的重要優點是它提供的延展性。 許多共用快取服務的實作方式，會使用伺服器叢集，並以透明的方式，利用將資料分散到叢集的軟體。 應用程式執行個體只會將要求傳送至快取服務。 基本的基礎結構會負責判斷叢集中快取資料的位置。 您可以輕鬆地透過新增更多伺服器來擴充快取。

共用快取方式有兩個主要缺點︰

- 快取的存取速度變慢，因為它不再本機保留於每個應用程式執行個體中。
- 實作個別快取服務的需求可能會增加方案的複雜性。

## <a name="considerations-for-using-caching"></a>使用快取的考量

下列各節更詳細地說明設計和使用快取的考量。

### <a name="decide-when-to-cache-data"></a>決定快取資料的時機

快取可大幅改善效能、延展性和可用性。 您擁有的資料越多且需要存取此資料的使用者數量越大，快取的優點就會更顯著。 這是因為快取可減少與處理原始資料存放區中大量並行要求相關聯的延遲和爭用。

例如，資料庫可能支援有限的並行連接數目。 但是會從共用快取擷取資料而非基礎資料庫，即使目前可用的連接數目已用盡，還是能讓用戶端應用程式存取此資料。 此外，如果資料庫變成無法使用，用戶端應用程式或許能夠透過使用快取中保留的資料繼續執行。

請考慮快取經常讀取但很少修改的資料 (例如，讀取作業比例高於寫入作業比例的資料)。 不過，不建議您將快取用作授權的重要資訊存放區， 而是確定應用程式無法承受遺失的所有變更一律會儲存至永續性資料存放區 這表示，如果快取無法使用，您的應用程式還是可以使用資料存放區繼續執行作業，而您將不會遺失重要資訊。

### <a name="determine-how-to-cache-data-effectively"></a>判斷如何有效率地快取資料

有效地使用快取的關鍵在於決定最適合快取的資料，以及最適合快取的時間。 資料會在應用程式第一次擷取時隨選新增至快取。 這表示應用程式僅需從資料存放區擷取一次資料，而後續存取可透過使用快取來滿足。

或者，通常當應用程式啟動 (稱為植入) 時，快取會事先填入部分或完整資料。 不過，可能不建議實作植入大型快取，因為這種方法在應用程式開始執行時，會在原始資料存放區上造成突發性高負載。

通常分析使用模式可協助決定是否要預先填入完整或部分快取，以及選擇要快取的資料。 例如，針對定期 (也許是每天) 使用應用程式的客戶，使用靜態使用者設定檔資料植入快取相當實用，但不適用於一週僅使用一次應用程式的客戶。

快取通常適用於固定或很少變更的資料。 範例包含參考資訊，例如，電子商務應用程式中的產品和定價資訊，或高建構成本的共用靜態資源。 此資料的部分或全部可在應用程式啟動時載入到快取，以便將資源需求降到最低並改善效能。 此外，擁有定期更新快取中參考資料的背景處理程序可能也很恰當，以便確定其處於最新狀態，或在參考資料變更時重新整理快取。

儘管此考量有某些例外狀況 (請參閱本文後續的＜快取高動態資料＞一節，以取得詳細資訊)，快取還是較不適合動態資料。 當原始資料定期變更時，快取的資訊會變得很快過時，或為了讓快取與原始資料存放區同步而產生額外負荷，因而導致降低快取的效益。

請注意，快取並未包含實體的完整資料。 舉例來說，如果資料項目代表多重值物件 (例如，具有名稱、地址和帳戶餘額的銀行客戶)，則其中某些項目可能會維持靜態 (例如名稱和地址)，而其他項目 (例如帳戶餘額) 可能更為動態。 在這些情況下，快取資料的靜態部分，以及只有在需要時擷取 (或計算) 剩餘資訊就相當實用。

建議您執行效能測試和使用量分析，以判斷快取的預先填入或隨選載入 (或兩者組合) 是否適當。 此決定應取決於資料的變動性和使用模式。 快取使用情況和效能分析對於遭遇高度負載，且必須是高度可調整的應用程式中特別重要。 例如，在高度可調整的案例中，適合用來植入快取，以在尖峰時間減少資料存放區的負載。

快取也可以用來在應用程式執行時避免重複計算。 如果作業轉換資料，或執行複雜的計算，則它可以在快取中儲存作業的結果。 如果後續需要相同的計算，應用程式就能直接從快取擷取結果。

應用程式可以修改保留在快取中的資料。 不過，建議您將快取視為可能會隨時消失的暫時性資料存放區。 不要將重要資料只儲存於快取中，而是確定您也會維護原始資料存放區內的資訊。 這表示，如果快取變成無法使用，就會降低遺失資料的機會。

### <a name="cache-highly-dynamic-data"></a>快取高動態資料

當您在永續性資料存放區中儲存快速變更的資訊時，可能會造成系統過度負荷。 例如，假設有一個持續報告狀態或其他度量單位的裝置。 在快取資訊幾乎一律會過期的情況下，如果應用程式選擇不要快取此資料，則從資料存放區中儲存和擷取此資訊時相同的考量也適用； 而且在儲存和擷取此資料時，它可能已經變更。

在這類情況下，請考慮在快取 (而非永續性資料存放區) 中直接儲存動態資訊的優點。 如果資料不重要且不需稽核，則遺失非經常性變更不會造成任何影響。

### <a name="manage-data-expiration-in-a-cache"></a>管理快取中的資料到期

在多數情況下，保留於快取中的資料即是保留在原始資料存放區中的資料複本。 原始資料存放區中的資料可能會在快取後變更，並造成快取資料過時。 許多快取系統可讓您設定快取過期資料，並減少資料可能過期的期間。

快取的資料到期時會從快取中移除，且應用程式必須從原始資料存放區中擷取資料 (它可以將新擷取的資訊放回快取中)。 當您設定快取時，您可以設定預設到期原則。 在許多快取服務中，當您以程式設計方式將物件儲存在快取時，您也可以規定個別物件的到期時間。 某些快取可讓您將到期時間指定為絕對值，或是如果未在指定時間內存取，則會從快取移除項目的變化值。 這項設定會覆寫任何快取範圍的到期原則，但僅會針對指定的物件。

> [!NOTE]
> 仔細思考快取的到期時間和其包含的物件。 如果設定的時間太短，則物件會太快過期，而且會減少您使用快取的優點。 如果設定的時間太長，則您需負擔資料過時的風險。

如果允許資料長時間維持駐留狀態，則快取也可能會填滿。 在此情況下，將新項目新增至快取的任何要求可能導致某些項目遭到強制移除，這稱為收回程序。 快取服務通常會根據最近最少使用的 (LRU) 原則來收回資料，但您通常可以覆寫此原則，並防止項目遭到收回。 不過，如果您採用這種方法，則需承擔超過快取中可用記憶體的風險。 應用程式在嘗試將項目新增至快取時將會失敗並發生例外狀況。

某些快取實作可能會提供其他收回原則。 有數個類型的收回原則。 其中包含：

- 最近使用的原則 (不再需要資料的預期狀況)。
- 先進先出原則 (先收回最舊的資料)，
- 以觸發事件 (例如要修改的資料) 為基礎的明確移除原則。

### <a name="invalidate-data-in-a-client-side-cache"></a>使用戶端快取中的資料失效

保留在用戶端快取中的資料，通常會被視為不屬於提供資料給用戶端的服務支援。 服務不能直接強制用戶端新增或移除來自用戶端快取的資訊。

這表示使用設定不良之快取的用戶端，可能會繼續使用過時的資訊。 例如，如果未正確實作快取的到期原則，當原始資料來源中的資訊已變更時，用戶端可能會使用本機快取的過時資訊。

如果您正在建置透過 HTTP 連接提供資料的 Web 應用程式，您可以隱含地強制 Web 用戶端 (例如瀏覽器或 Web Proxy) 來擷取最新資訊。 如果資源是透過變更該資源的 URI 來更新，您就可以執行此動作。 Web 用戶端通常會使用資源的 URI 做為用戶端快取中的索引鍵，因此，如果 URI 變更，就會導致 Web 用戶端忽略任何先前快取的資源版本，並改為擷取新的版本。

## <a name="managing-concurrency-in-a-cache"></a>管理快取中的並行存取

快取的設計目的通常是由應用程式的多個執行個體所共用。 每個應用程式執行個體可以讀取和修改快取中的資料。 因此，對於任何共用資料存放區中所發生相同並行問題也適用於快取。 在應用程式必須修改快取中所保留資料的情況下，您可能需要確保由應用程式的某個執行個體所進行的更新不會覆寫另一個執行個體所做的變更。

根據資料的本質和衝突的可能性，您可以採用兩種方式其中之一來進行並行存取：

- **開放式**。 就在更新資料之前，應用程式會檢查快取中的資料自擷取之後是否已變更。 如果資料仍然相同，則可以進行變更。 否則，應用程式必須決定是否要進行更新  (衍生此決策的商務邏輯是應用程式特定的)。這種方法適合不常更新，或其中不太可能發生衝突的情況。
- **封閉式**。  當應用程式擷取資料時會在快取中鎖定資料，以避免另一個執行個體變更資料。 此處理程序可確保不會發生衝突，但它們也會封鎖其他需要處理相同資料的執行個體。 封閉式並行存取會影響解決方案的延展性，建議僅用於短期作業。 這種方法可能適用於很可能發生衝突的情況，特別是當應用程式更新快取中的多個項目，且必須確保這些變更會一致套用的情況。

### <a name="implement-high-availability-and-scalability-and-improve-performance"></a>實作高可用性和延展性，並改善效能

避免使用快取做為資料的主要儲存機制；這個角色應該是用來填入快取的原始資料存放區。 原始資料存放區會負責確保資料的持續性。

請小心不要將共用快取服務可用性上的重要相依性引入您的解決方案。 如果提供共用快取的服務無法使用，則應用程式應該能夠繼續運作。 應用程式應該不會在等候快取服務繼續時停止回應或失敗。

因此，應用程式必須準備好偵測快取服務的可用性，並在無法存取快取時回復為原始資料存放區。 [Circuit Breaker Pattern (斷路器模式)](../patterns/circuit-breaker.md) 在處理這種案例時相當實用。 提供快取的服務可以復原，而且當服務可供使用時，快取會在從原始資料存放區讀取資料時重新填入，並遵循下列策略，例如 [Cache-Aside Pattern (另行快取模式)](../patterns/cache-aside.md)。

不過，如果應用程式在快取暫時無法使用時回復至原始資料存放區，則可能會影響系統延展性。 復原資料存放區時，原始資料存放區可能忙於處理資料要求，因而導致逾時和連接失敗。

請考慮在應用程式的每個執行個體中實作本機私人快取，以及所有應用程式執行個體存取的共用快取。 當應用程式擷取項目時，可能會先檢查它的本機快取，再來是共用快取，最後則是原始資料存放區。 本機快取可以使用共用快取中的資料填入，或者如果共用快取無法使用，則可使用資料庫中的資料填入。

此方法需要仔細的組態設定，以防止本機快取相對於共用快取變得太過時。 但若無法連接到共用快取時，則會使用本機快取做為緩衝區。 圖 3 會顯示此結構。

![使用本機私人快取搭配共用快取](./images/caching/Caching3.png)

*圖 3：使用本機私人快取搭配共用快取。*

為了支援保留相當長期資料的大型快取，某些快取服務會在快取無法使用時，提供實作自動容錯移轉的高可用性選項。 這種方法通常涉及將儲存於主要快取伺服器上的快取資料複寫到次要快取伺服器，且在主要伺服器故障或遺失連接時切換到次要伺服器。

為了減少與寫入多個目的地相關聯的延遲，當資料寫入主要伺服器上的快取時，複寫到次要伺服器的作業可能會以非同步方式發生。 這種方法會導致某些快取的資訊可能會在發生錯誤時遺失，但此資料的比例應該小於快取的整體大小。

如果共用快取很大，則在節點上分割快取資料可能很有幫助，可減少爭用的機會，並改善延展性。 許多的共用快取支援動態新增 (與移除) 節點，以及重新平衡分割之間資料的功能。 這種方法可能牽涉到叢集，節點的集合會向用戶端應用程式顯示為隨選即用的單一快取。 但內部的資料會分散在節點之間，並遵循某些預先定義的散發策略，以便平均地平衡負載。 若要深入了解可能的資料分割策略，請參閱[資料分割指引](https://msdn.microsoft.com/library/dn589795.aspx)。

叢集也可以進一步提高快取的可用性。 如果節點失敗，則快取的其餘部分仍然可以存取。 使用叢集時經常搭配複寫和容錯移轉。 每個節點皆可複寫，且複本會在節點失敗時快速上線。

許多讀取和寫入作業可能涉及單一資料值或物件。 不過，有時可能必須快速儲存或擷取大量資料。 例如，植入快取可能涉及將數百或數千個項目寫入至快取。 應用程式也可能需要從快取中擷取大量相關項目，以做為相同要求的一部分。

許多大型快取會針對這些用途提供批次作業。 這讓用戶端應用程式可將大量項目封裝成單一要求，並在執行大量的小型要求時減少相關聯的過度負荷。

## <a name="caching-and-eventual-consistency"></a>快取和最終一致性

若要讓另行快取模式能夠運作，填入快取的應用程式執行個體，必須可存取最新且一致的資料版本。 在實作最終一致性 (例如複寫的資料存放區) 的系統中，這可能不會如此。

一個應用程式的執行個體可以修改資料項目，使該項目的快取版本失效。 應用程式的另一個執行個體可能會嘗試從快取中讀取此項目 (這會導致快取遺漏)，因此它會從資料存放區讀取資料，並將它新增至快取。 不過，如果資料存放區沒有完全與其他複本同步，則應用程式執行個體可能會使用舊值來讀取並填入快取。

如需處理資料一致性的詳細資訊，請參閱[資料一致性入門](https://msdn.microsoft.com/library/dn589800.aspx)。

### <a name="protect-cached-data"></a>保護快取的資料

無論您使用的快取服務為何，都要考慮如何保護保留於快取中的資料，以避免未經授權的存取。 其中有兩個主要的考量：

- 快取中資料的隱私權。
- 資料的隱私權可說是在快取和使用快取的應用程式之間流動。

為了保護快取中的資料，快取服務可能會實作一項驗證機制，以要求應用程式指定下列內容︰

- 哪些身分識別可以存取快取中的資料。
- 允許這些身分識別執行哪些作業 (讀取和寫入)。

為了在讀取和寫入資料時減少相關聯的過度負荷，在授與身分識別寫入和/或讀取快取的存取權之後，該身分識別就能使用快取中的任何資料。

如果您需要限制快取資料子集的存取權，可執行下列其中一個動作：

- 將快取分割為分割區 (使用不同的快取伺服器)，並且僅授與身分識別應允許其使用之分割區的存取權。
- 使用不同的金鑰來加密每個子集中的資料，並且僅向應該具有每個子集存取權的身分識別提供加密金鑰。 用戶端應用程式或許仍能擷取快取中的所有資料，但它只能夠解密具有金鑰的資料。

您也可以在資料於快取中流動進出時給予保護。 若要執行此動作，您可以依靠網路基礎結構所提供的安全性功能，用戶端應用程式會使用該基礎結構來連接至快取。 如果在裝載用戶端應用程式的相同組織中使用現場伺服器來實作快取，則網路本身的隔離可能不需要您採取其他步驟。 如果快取位於遠端且需要公用網路 (例如網際網路) 上的 TCP 或 HTTP 連接，請考慮實作 SSL。

## <a name="considerations-for-implementing-caching-in-azure"></a>在 Azure 中實作快取的考量

[Azure Redis 快取](/azure/redis-cache/)是開放原始碼 Redis 快取的實作，可作為 Azure 資料中心的服務來執行。 它提供可從任何 Azure 應用程式存取的快取服務，無論應用程式實作為雲端服務、網站，或在 Azure 虛擬機器中。 擁有適當存取金鑰的用戶端應用程式可以共用快取。

Azure Redis 快取是高效能的快取解決方案，提供可用性、延展性和安全性。 它通常會以分散到一或多部專用電腦上的服務來執行。 它會試著盡可能在記憶體中儲存最多資訊，以確保快速存取。 此架構可透過減少執行緩慢的 I/O 作業的需求，用於提供低度延遲和高輸送量。

Azure Redis 快取可與許多用戶端應用程式所使用的各種 API 相容。 如果您現有的應用程式已使用執行內部部署的 Azure Redis 快取，Azure Redis 快取就能在雲端中提供快取的快速移轉路徑。

### <a name="features-of-redis"></a>Redis 的功能

Redis 不僅是一種簡易快取伺服器。 它提供分散式記憶體內部資料庫搭配廣泛的命令集，可支援許多常見案例。 相關說明請參閱本文件稍後的＜使用 Redis 快取＞一節。 本節將摘要出一些 Redis 所提供的重要功能。

### <a name="redis-as-an-in-memory-database"></a>Redis 作為記憶體中資料庫

Redis 同時支援讀取和寫入作業。 在 Redis 中，寫入可以透過在本機快照集檔案或僅限附加的記錄檔中定期儲存，來提供保護以防止系統失敗。 這與許多快取不同 (應將其視為暫時性資料存放區)。

所有寫入皆為非同步，且不會封鎖用戶端讀取和寫入資料。 當 Redis 開始執行時，它會從快照集或記錄檔讀取資料，並使用它來建構記憶體中快取。 如需詳細資訊，請參閱 Redis 網站上的 [Redis Persistence (Redis 持續性)](https://redis.io/topics/persistence) 。

> [!NOTE]
> Redis 不保證所有寫入在發生災難性失敗時皆能儲存，但最糟的情況是，您可能只會遺失幾秒鐘的資料價值。 請記住，快取並不適合用作授權資料來源，這是應用程式的責任，使用快取以確保重要資料已成功儲存到適當資料存放區。 如需詳細資訊，請參閱 [Cache-Aside Pattern (另行快取模式)](../patterns/cache-aside.md)。

#### <a name="redis-data-types"></a>Redis 資料類型

Redis 是索引鍵值存放區，其中的值可以包含簡易類型或複雜資料結構，例如雜湊、清單，以及集合。 它針對這些資料類型支援一組不可部分完成的作業。 索引鍵可以永久存在或標記為有限的存留時間，屆時索引鍵及其對應值會自動從快取中移除。 如需有關 Redis 索引鍵和值的詳細資訊，請造訪 Redis 網站上的 [An introduction to Redis data types and abstractions (Redis 資料類型與抽象概念簡介)](https://redis.io/topics/data-types-intro) 頁面。

#### <a name="redis-replication-and-clustering"></a>Redis 複寫和叢集

Redis 支援主要/下層複寫，以協助確保可用性並維護輸送量。 Redis 主要節點的寫入作業會複寫至一或多個下層節點。 讀取作業可由主要或任何下層節點來提供。

至於網路磁碟分割，下層項目可以繼續提供資料，然後在重新建立連接時以透明的方式與主要節點重新同步。 如需詳細資訊，請造訪 Redis 網站上的 [複寫 (英文)](https://redis.io/topics/replication) 頁面。

Redis 也提供叢集，其可讓您以透明方式在伺服器之間將資料分割成分區並分散負載。 此功能可改善延展性，因為可新增新的 Redis 伺服器，並增加資料可重新分割的快取大小。

此外，叢集中的每一部伺服器都可以使用主要/下層複寫進行複寫。 這可確保整個叢集中每個節點的可用性 如需有關叢集和分區化的詳細資訊，請造訪 Redis 網站上的 [Redis 叢集教學課程頁面](https://redis.io/topics/cluster-tutorial)。

### <a name="redis-memory-use"></a>Redis 記憶體使用

Redis 快取的大小有限，其取決於主機電腦上可用的資源。 當您設定的 Redis 伺服器時，您可以指定伺服器可以使用的記憶體最大數量。 您也可以設定 Redis 快取中的索引鍵，使其具有到期時間，屆時會自動從快取中移除它。 此功能可協助避免記憶體內部快取中填滿老舊或過時的資料。

當記憶體填滿時，Redis 可以遵循原則數目自動收回索引鍵及其值。 預設值是 LRU (最近最少使用的)，但您也可以選取其他原則，例如，隨機收回索引鍵或完全關閉收回 (在此情況下，當快取已滿時，嘗試將項目新增至快取就會失敗)。 [Using Redis as an LRU cache (使用 Redis 作為 LRU 快取)](https://redis.io/topics/lru-cache) 頁面提供了詳細資訊。

### <a name="redis-transactions-and-batches"></a>Redis 交易與批次

Redis 可讓用戶端應用程式提交一系列的作業，以便在快取中讀取和寫入資料以作為不可部分完成的交易。 交易中的所有命令保證會循序執行，且其他並行用戶端所發出的命令將不會在兩者之間交互編排。

不過，這些不是實際上的交易，因為關聯式資料庫會執行它們。 交易處理包含兩個階段 -- 第一個是在命令排入佇列時，而第二個是在命令執行時。 在命令佇列階段期間，用戶端會提交構成交易的命令。 如果此時發生某種形式的錯誤 (例如，語法錯誤或參數數目不正確)，則 Redis 會拒絕處理整個交易並捨棄它。

在執行階段期間，Redis 會循序執行每個佇列中的命令。 如果命令在這個階段期間失敗，Redis 會繼續執行下一個佇列中的命令，且不會復原任何已經執行之命令的效果。 這個簡化的交易形式可協助維護效能，並避免爭用所造成的效能問題。

Redis 會實作一種開放式鎖定，以便協助維護一致性。 如需使用 Redis 進行交易和鎖定的詳細資訊，請造訪 Redis 網站上的 [交易頁面 (英文)](https://redis.io/topics/transactions) 。

Redis 也支援非交易式批次要求。 用戶端用來將命令傳送到 Redis 伺服器的 Redis 通訊協定，可讓用戶端將以相同要求的一部分來傳送一系列作業。 這有助於減少在網路上的封包分割。 處理批次時，系統會執行每個命令。 如果這些命令的任何格式不正確，將會遭到拒絕 (這不會在交易中發生)，但將會執行剩餘的命令。 此外，也不保證批次中處理命令的相關順序。

### <a name="redis-security"></a>Redis 安全性

Redis 純粹著重於提供資料的快速存取，並設計為在受信任的環境中執行，且僅能由受信任的用戶端存取。 Redis 支援以密碼驗證為基礎的有限安全性模型  (可完全移除驗證，但不建議這樣做)。

所有已驗證的用戶端會共用相同的全域密碼，並存取相同資源。 如果您需要更完整的登入安全性，您必須在 Redis 伺服器之前實作自己的安全性層級，且所有用戶端要求應通過這個額外的層級。 Redis 不應直接向不受信任或未經驗證的用戶端公開。

您可以透過停用命令或重新命名命令 (僅為有權限的用戶端提供新名稱) 來限制命令的存取。

Redis 不直接支援任何形式的資料加密，因此所有編碼必須由用戶端應用程式執行。 此外，Redis 不提供任何形式的傳輸安全性。 如果您基於資料在網路上流通而需要保護資料，建議實作 SSL Proxy。

如需詳細資訊，請造訪 Redis 網站上的 [Redis Security (Redis 安全性)](https://redis.io/topics/security) 頁面。

> [!NOTE]
> Azure Redis 快取透過所連接的用戶端提供自己的安全性層級。 基本的 Redis 伺服器不會向公用網路公開。

### <a name="azure-redis-cache"></a>Azure Redis 快取

Azure Redis 快取提供 Redis 伺服器的存取權，該伺服器裝載於 Azure 資料中心。 它可做為提供存取控制與安全性的表面。 您可以使用 Azure 入口網站來佈建快取。

此入口網站會提供數個預先定義的組態。 範圍可從執行專用服務的 53 GB 快取，用來支援 SSL 通訊 (適用於隱私權) 以及主要/下層複寫搭配 99.9% 可用性的 SLA，到共用硬體上所執行不含複寫 (無可用性保證) 的 250 MB 快取。

您也可以使用 Azure 入口網站來設定快取的收回原則，並透過將使用者新增至所提供角色來控制快取的存取權。 這些角色會定義成員可執行的作業，其中包含擁有者、參與者和讀取者。 例如，擁有者角色成員擁有快取 (包含安全性) 及其內容的完整控制權，參與者角色成員則可以讀取和寫入快取中的資訊，而讀取者角色成員僅能從快取擷取資料。

多數的系統管理工作都是透過 Azure 入口網站來執行。 基於這個理由，許多 Redis 標準版中可用的系統管理命令皆無法使用，包含以程式設計方式修改組態、關閉 Redis 伺服器、設定其他下層項目，或強制將資料儲存到磁碟等功能。

Azure 入口網站包含便利的圖形化顯示，可讓您監視快取的效能。 例如，您可以檢視建立的連接數目、執行的要求數目、讀取與寫入的資料量，以及快取命中與快取遺漏的數目。 您可以使用此資訊來判斷快取的效率，且可視需要切換至不同的組態，或變更收回原則。

此外，如果一個或多個關鍵度量落在預期的範圍之外，您可以建立將電子郵件訊息傳送給系統管理員的警示。 例如，如果快取遺漏數目在最後一小時超過指定的值，則您可能想要警告系統管理員，因為這表示快取可能太小或資料可能太快收回。

您也可以監視 CPU、記憶體和快取的網路使用量。

如需顯示如何建立及設定 Azure Redis 快取的進一步資訊和範例，請造訪 Redis 部落格上的 [瀏覽 Azure Redis 快取 (英文)](https://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) 頁面。

## <a name="caching-session-state-and-html-output"></a>快取工作階段狀態和 HTML 輸出

如果您正在建置使用 Azure Web 角色執行的 ASP.NET Web 應用程式，您可以將工作階段狀態資訊和 HTML 輸出儲存在 Azure Redis 快取中。 Azure Redis 快取的工作階段狀態提供者可讓您在 ASP.NET Web 應用程式的不同執行個體之間共用工作階段資訊，並且在用戶端與伺服器親和性無法使用，且快取工作階段記憶體中資料並不適當的 Web 伺服陣列情況下相當實用。

使用工作階段狀態提供者搭配 Azure Redis 快取可提供幾項優點，包含：

- 與大量的 ASP.NET Web 應用程式執行個體共用工作階段狀態。
- 提供改善的延展性。
- 針對多個讀取者和單一寫入者的相同工作階段狀態資料，支援受控制的並行存取權。
- 使用壓縮來節省記憶體，並改善網路效能。

如需詳細資訊，請參閱 [Azure Redis 快取的 ASP.NET 工作階段狀態提供者](/azure/redis-cache/cache-aspnet-session-state-provider/)。

> [!NOTE]
> 請勿針對在 Azure 環境以外執行的 ASP.NET 應用程式使用 Azure Redis 快取的工作階段狀態供應器。 從 Azure 外部存取快取的延遲會消除快取資料的效能優勢。

同樣地，Azure Redis 快取的輸出快取提供者可讓您儲存透過 ASP.NET Web 應用程式所產生的 HTTP 回應。 使用輸出快取提供者搭配 Azure Redis 快取，可針對呈現複雜 HTML 輸出的應用程式改善回應時間。 產生類似回應的應用程式執行個體可以使用快取中的共用輸出片段，而不會重新產生此 HTML 輸出。 如需詳細資訊，請參閱 [Azure Redis 快取的 ASP.NET 工作階段狀態提供者](/azure/redis-cache/cache-aspnet-output-cache-provider/)。

## <a name="building-a-custom-redis-cache"></a>建置自訂的 Redis 快取

Azure Redis 快取可用來做為基礎 Redis 伺服器的表面。 如果您需要 Azure Redis 快取未涵蓋的進階組態 (例如大於 53 GB 的快取)，您可以使用 Azure 虛擬機器來建置並裝載您自己的 Redis 伺服器。

因為您在想要實作複寫時可能需要建立數個 VM 做為主要和下層節點，所以這可能是個很複雜的程序。 此外，如果您想要建立叢集，則需要多部主要和下層伺服器。 可提供高可用性的最小叢集複寫拓撲，以及包含至少六個 VM 並組織成三組主要/下層伺服器配對 (叢集必須包含至少三個主要節點) 的延展性。

每個主要/下層配對應彼此鄰近，以將延遲降至最低。 但若您想要找出靠近應用程式，且該應用程式最有可能使用的快取資料，每一組配對可以在位於不同區域的不同 Azure 資料中心內執行。 如需建置和設定作為 Azure VM 執行的 Redis 節點範例，請參閱[在 Azure 中的 CentOS Linux VM 上執行 Redis](https://blogs.msdn.microsoft.com/tconte/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure/)。

> [!NOTE]
> 請注意，如果您以這種方式實作自己的 Redis 快取，則必須負責監視、管理和保護服務。

## <a name="partitioning-a-redis-cache"></a>Redis 快取的資料分割

資料分割快取涉及多部電腦之間的分割快取。 此結構具有數個優點，可使用單一快取伺服器，包含：

- 建立比儲存在單一伺服器上更大的快取。
- 將資料分散到伺服器，並改善可用性。 如果某一部伺服器失敗或變成無法存取，其所保存的資料就會無法使用，但剩餘伺服器上的資料仍然可以存取。 至於快取並不重要，因為快取資料只是暫時保留在資料庫中的資料複本。 變成無法存取之伺服器上的快取資料可以改為在不同的伺服器上進行快取。
- 在伺服器之間分配負載，藉此改善效能和延展性。
- 將資料配置在鄰近供使用者存取的地理位置，因而可降低延遲。

至於快取，最常見的資料分割形式是分區化。 在此策略中，每個分割區 (或分區) 都是獨立存在的 Redis 快取。 資料會使用分區化邏輯導向至特定的資料分割，可以使用各種不同的方法來散發資料。 [Sharding Pattern (分區化模式)](../patterns/sharding.md) 提供有關實作分區化的詳細資訊。

若要在 Redis 快取中實作資料分割，您可以採用下列其中一種方法：

- *伺服器端查詢路由。* 在這項技術中，用戶端應用程式將要求傳送到任何包含快取的 Redis 伺服器 (可能是最接近的伺服器)。 每個 Redis 伺服器會儲存描述資料分割的中繼資料，該資料分割會保存並同時包含關於其他伺服器上磁碟分割的資訊。 Redis 伺服器會檢查用戶端要求。 如果它可以在本機解析，就會執行要求的作業， 否則會將要求轉送到適當的伺服器。 此模型會由 Redis 叢集來實作，而更詳細的描述位於 Redis 網站上的 [Redis 叢集教學課程](https://redis.io/topics/cluster-tutorial)頁面。 Redis 叢集對用戶端應用程式而言是透明的，其他 Redis 伺服器可以加入至叢集 (資料會重新分割)，而不需要重新設定用戶端。
- *用戶端資料分割。* 在此模型中，用戶端應用程式包含將要求路由傳送至適當 Redis 伺服器的邏輯 (可能是以程式庫的形式)。 這種方法可以搭配 Azure Redis 快取使用。 建立多個 Azure Redis 快取 (每個資料分割區一個快取)，並實作將要求路由傳送至正確快取的用戶端邏輯。 如果資料分割配置有所變更 (例如，如果建立了其他 Azure Redis 快取)，則用戶端應用程式可能需要重新設定。
- *Proxy 輔助資料分割。* 在此配置中，用戶端應用程式會將要求傳送至一個了解如何分割資料的中繼 Proxy 服務，然後將要求路由傳送至適當的 Redis 伺服器。 這種方法也可以搭配 Azure Redis 快取使用；Proxy 服務可以實作為 Azure 雲端服務。 這種方法需要一層額外的複雜性來實作服務，而且要求的執行時間可能比使用用戶端資料分割的時間更長。

Redis 網站上的 [資料分割：如何在多個 Redis 執行個體上劃分資料](https://redis.io/topics/partitioning) 頁面會提供關於使用 Redis 實作資料分割的進一步資訊。

### <a name="implement-redis-cache-client-applications"></a>實作 Redis 快取用戶端應用程式

Redis 支援以多種程式設計語言撰寫的用戶端應用程式。 如果您要使用.NET Framework 來建置新的應用程式，建議的方法是使用 StackExchange.Redis 用戶端程式庫。 此程式庫會提供.NET Framework 物件模型，用來擷取詳細資料以便連接到 Redis 伺服器連接、傳送命令，以及接收回應。 它是在 Visual Studio 中作為 NuGet 封裝提供使用。 您可以使用這個相同的程式庫，連接至 Azure Redis 快取或 VM 上裝載的自訂 Redis 快取。

若要連接到 Redis 伺服器，您可以使用 `ConnectionMultiplexer` 類別的靜態 `Connect` 方法。 這個方法建立的連接適用於整個用戶端應用程式的存留期，而且相同的連接可供多個並行執行緒使用。 每次您執行 Redis 操作時請勿重新連接並中斷連接，因為這會降低效能。

您可以指定連接參數，例如 Redis 主機的位址和密碼。 如果您使用 Azure Redis 快取，則密碼可能是針對 Azure Redis 快取使用 Azure 管理入口網站所產生的主要或次要索引鍵。

在您已連線到 Redis 伺服器之後，可以在作為快取的 Redis 資料庫上取得控制代碼。 Redis 連線提供 `GetDatabase` 方法來執行這項操作。 您可以使用 `StringGet` 和 `StringSet` 方法，從快取擷取項目並在快取中儲存資料。 這些方法預期索引鍵要做為參數使用，並傳回快取中具有相符值的項目 (`StringGet`)，或利用此索引鍵將項目新增至快取 (`StringSet`)。

根據 Redis 伺服器的位置，許多作業可能會造成一些延遲，而要求會傳輸到伺服器，且回應會傳回給用戶端。 StackExchange 程式庫提供許多方法的非同步版本，它會進行公開以便協助用戶端應用程式保持回應狀態。 這些方法支援 .NET Framework 中的 [以工作為基礎的非同步模式 (TAP)](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap) 。

下列程式碼片段示範一個名為 `RetrieveItem`的方法。 其中說明根據 Redis 和 StackExchange 程式庫實作另行快取模式的範例。 此方法接受字串索引值，並透過呼叫 `StringGetAsync` 方法 (`StringGet` 的非同步版本)，嘗試從 Redis 快取中擷取對應的項目。

如果找不到項目，它會使用 `GetItemFromDataSourceAsync` 方法 (這是本機方法，不屬於 StackExchange 程式庫的一部分)，從基礎資料來源擷取項目。 然後使用 `StringSetAsync` 方法新增至快取，以便下一次可以更快速地擷取項目。

```csharp
// Connect to the Azure Redis cache
ConfigurationOptions config = new ConfigurationOptions();
config.EndPoints.Add("<your DNS name>.redis.cache.windows.net");
config.Password = "<Redis cache key from management portal>";
ConnectionMultiplexer redisHostConnection = ConnectionMultiplexer.Connect(config);
IDatabase cache = redisHostConnection.GetDatabase();
...
private async Task<string> RetrieveItem(string itemKey)
{
    // Attempt to retrieve the item from the Redis cache
    string itemValue = await cache.StringGetAsync(itemKey);

    // If the value returned is null, the item was not found in the cache
    // So retrieve the item from the data source and add it to the cache
    if (itemValue == null)
    {
        itemValue = await GetItemFromDataSourceAsync(itemKey);
        await cache.StringSetAsync(itemKey, itemValue);
    }

    // Return the item
    return itemValue;
}
```

`StringGet` 和 `StringSet` 方法不會限制要擷取或儲存字串值。 它們可以接受任何序列化為位元組陣列的項目。 如果您需要儲存 .NET 物件，可將其序列化為位元組資料流，並使用 `StringSet` 方法，將它寫入快取。

同樣地，您可以使用 `StringGet` 方法，從快取中讀取物件，並將其還原序列化為 .NET 物件。 下列程式碼示範一組 IDatabase 介面的擴充方法 (Redis 連接的 `GetDatabase` 方法會傳回 `IDatabase` 物件)，以及一些使用這些方法來讀取和寫入 `BlogPost` 物件至快取的範例程式碼：

```csharp
public static class RedisCacheExtensions
{
    public static async Task<T> GetAsync<T>(this IDatabase cache, string key)
    {
        return Deserialize<T>(await cache.StringGetAsync(key));
    }

    public static async Task<object> GetAsync(this IDatabase cache, string key)
    {
        return Deserialize<object>(await cache.StringGetAsync(key));
    }

    public static async Task SetAsync(this IDatabase cache, string key, object value)
    {
        await cache.StringSetAsync(key, Serialize(value));
    }

    static byte[] Serialize(object o)
    {
        byte[] objectDataAsStream = null;

        if (o != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream())
            {
                binaryFormatter.Serialize(memoryStream, o);
                objectDataAsStream = memoryStream.ToArray();
            }
        }

        return objectDataAsStream;
    }

    static T Deserialize<T>(byte[] stream)
    {
        T result = default(T);

        if (stream != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream(stream))
            {
                result = (T)binaryFormatter.Deserialize(memoryStream);
            }
        }

        return result;
    }
}
```

下列程式碼將說明一個稱為 `RetrieveBlogPost` 的方法，該方法會使用這些擴充方法，遵循另行快取模式來讀取和寫入序列化 `BlogPost` 物件至快取：

```csharp
// The BlogPost type
[Serializable]
public class BlogPost
{
    private HashSet<string> tags;

    public BlogPost(int id, string title, int score, IEnumerable<string> tags)
    {
        this.Id = id;
        this.Title = title;
        this.Score = score;
        this.tags = new HashSet<string>(tags);
    }

    public int Id { get; set; }
    public string Title { get; set; }
    public int Score { get; set; }
    public ICollection<string> Tags => this.tags;
}
...
private async Task<BlogPost> RetrieveBlogPost(string blogPostKey)
{
    BlogPost blogPost = await cache.GetAsync<BlogPost>(blogPostKey);
    if (blogPost == null)
    {
        blogPost = await GetBlogPostFromDataSourceAsync(blogPostKey);
        await cache.SetAsync(blogPostKey, blogPost);
    }

    return blogPost;
}
```

如果用戶端應用程式傳送多個非同步要求，Redis 支援命令流水線。 Redis 可以使用相同連接來多工處理要求，而非以嚴格的順序接收和回應命令。

此方法有助於透過更有效率地使用網路來減少延遲。 下列程式碼片段顯示的範例會同時擷取兩個客戶的詳細資料。 程式碼會提交兩個要求，然後執行某些其他處理程序 (未顯示)，再等候接收結果。 快取物件的 `Wait` 方法類似於 .NET Framework `Task.Wait` 方法：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
var task1 = cache.StringGetAsync("customer:1");
var task2 = cache.StringGetAsync("customer:2");
...
var customer1 = cache.Wait(task1);
var customer2 = cache.Wait(task2);
```

如需編寫能使用 Azure Redis 快取的用戶端應用程式的詳細資訊，請參閱 [Azure Cache for Redis 文件](/azure/azure-cache-for-redis/)。 在[StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) 中也有更多詳細資訊。

同一個網站上的 [Pipelines and multiplexers (管線和多工器)](https://stackexchange.github.io/StackExchange.Redis/PipelinesMultiplexers) 頁面提供更多關於非同步作業以及使用 Redis 與 StackExchange 程式庫透過管線進行傳送的資訊。

## <a name="using-redis-caching"></a>使用 Redis 快取

基於快取考量而使用 Redis 的最簡單方法是索引鍵/值配對，其中值是未解譯的字串，可包含任意長度的任何二進位資料 (基本上可將它視為字串的位元組陣列)。 此案例即本文稍早的＜實作 Redis 快取用戶端應用程式＞一節中所說明的情況。

請注意，索引鍵也會包含未解譯的資料，因此您可以使用任何二進位資訊做為索引鍵。 但是索引鍵愈長，就需要花費愈多的空間來儲存，而且執行查閱作業的時間也會愈久。 如需可用性和維護的方便性，請仔細設計您的 keyspace 並使用有意義 (但非詳細資訊) 的索引鍵。

例如，使用結構化的索引鍵，像是 "customer:100" 來表示客戶識別碼為 100 的索引鍵，而非僅以 "100" 表示。 此配置可讓您輕鬆區別儲存不同資料類型之間的值。 例如，您也可以使用索引鍵 "orders:100" 來表示順序識別碼為 100 的索引鍵。

除了一維的二進位字串，Redis 索引鍵/值配對中的值也可以保留更結構化資訊，包含清單、集合 (已排序和未排序)，以及雜湊。 Redis 提供一組完整的命令集，可處理這些類型，且這些命令當中有許多可透過例如 StackExchange 的用戶端程式庫，使用於 .NET Framework 應用程式。 Redis 網站上的 [Redis 資料類型和抽象概念簡介 (英文)](https://redis.io/topics/data-types-intro) 頁面可針對這些類型和可用來操作這些類型的命令，提供更詳細的概觀。

本節將針對這些資料類型和命令摘要說明一些常用案例。

### <a name="perform-atomic-and-batch-operations"></a>執行不可部分完成的作業和批次作業

Redis 支援在字串值上進行一系列不可部分完成的取得和設定作業。 這些作業會移除使用個別 `GET` 和 `SET` 命令時可能會發生的競爭危險。 可用的作業包含：

- `INCR`、`INCRBY`、`DECR` 和 `DECRBY`，用來在整數數字資料值上執行不可部分完成的遞增和遞減作業。 StackExchange 程式庫會提供 `IDatabase.StringIncrementAsync` 和 `IDatabase.StringDecrementAsync` 方法的多載版本，來執行這些作業，並傳回儲存在快取中的結果值。 下列程式碼片段說明如何使用這些方法：

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  await cache.StringSetAsync("data:counter", 99);
  ...
  long oldValue = await cache.StringIncrementAsync("data:counter");
  // Increment by 1 (the default)
  // oldValue should be 100
  
  long newValue = await cache.StringDecrementAsync("data:counter", 50);
  // Decrement by 50
  // newValue should be 50
  ```

- `GETSET`用來擷取與索引鍵相關聯的值，並將其變更為新值。 StackExchange 程式庫會透過 `IDatabase.StringGetSetAsync` 方法讓這項作業可供使用。 下方的程式碼片段會顯示這個方法的範例。 此程式碼會從先前範例中傳回目前與索引鍵 "data:counter" 相關聯的值。 然後將此索引鍵的值重設回零，這些全都是相同作業的一部分：

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  string oldValue = await cache.StringGetSetAsync("data:counter", 0);
  ```

- `MGET` 和 `MSET` 可以作為單一作業傳回或變更一組字串值。 `IDatabase.StringGetAsync` 和 `IDatabase.StringSetAsync` 是多載方法，用來支援這項功能，如下列範例所示：

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  // Create a list of key-value pairs
  var keysAndValues =
      new List<KeyValuePair<RedisKey, RedisValue>>()
      {
          new KeyValuePair<RedisKey, RedisValue>("data:key1", "value1"),
          new KeyValuePair<RedisKey, RedisValue>("data:key99", "value2"),
          new KeyValuePair<RedisKey, RedisValue>("data:key322", "value3")
      };
  
  // Store the list of key-value pairs in the cache
  cache.StringSet(keysAndValues.ToArray());
  ...
  // Find all values that match a list of keys
  RedisKey[] keys = { "data:key1", "data:key99", "data:key322"};
  // values should contain { "value1", "value2", "value3" }
  RedisValue[] values = cache.StringGet(keys);

  ```

您也可以將多個作業結合到單一 Redis 交易，如同本文稍早的＜Redis 交易與批次＞一節中所述。 StackExchange 程式庫可透過 `ITransaction` 介面提供交易支援。

您可以使用 `IDatabase.CreateTransaction` 方法來建立 `ITransaction` 物件。 您使用 `ITransaction` 物件提供的方法來叫用交易的命令 。

`ITransaction` 介面提供一組如同 `IDatabase` 介面所存取的類似方法，但不包含所有非同步的方法。 這表示它們只會在叫用 `ITransaction.Execute` 方法時執行。 `ITransaction.Execute` 方法所傳回的值表示是否已成功建立交易 (true) 或建立失敗 (false)。

下列程式碼片段顯示一個範例，即作為相同交易一部分的兩個遞增和遞減計數器：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
ITransaction transaction = cache.CreateTransaction();
var tx1 = transaction.StringIncrementAsync("data:counter1");
var tx2 = transaction.StringDecrementAsync("data:counter2");
bool result = transaction.Execute();
Console.WriteLine("Transaction {0}", result ? "succeeded" : "failed");
Console.WriteLine("Result of increment: {0}", tx1.Result);
Console.WriteLine("Result of decrement: {0}", tx2.Result);
```

請記住，Redis 交易不同於關聯式資料庫中的交易。 `Execute` 方法只會將所有命令排入佇列 (其中包含要執行的交易)，而且如果其中任一個發生格式錯誤，則會中止交易。 如果已成功將所有命令排入佇列，就會以非同步方式執行每個命令。

如果有任何命令失敗，其他命令仍會繼續處理。 如果您需要驗證命令是否已順利完成，您必須使用對應工作的 **Result** 屬性來擷取命令的結果，如上述範例所示。 讀取 **Result** 屬性將會封鎖呼叫執行緒，直到工作已完成。

如需詳細資訊，請參閱 [Redis 中的交易](https://stackexchange.github.io/StackExchange.Redis/Transactions)。

執行批次作業時，您可以使用 StackExchange 程式庫的 `IBatch` 介面。 這個介面提供一組如同 `IDatabase` 介面所存取的類似方法，但不包含所有非同步的方法。

您可以使用 `IDatabase.CreateBatch` 方法來建立 `IBatch` 物件，然後使用 `IBatch.Execute` 方法來執行批次，如下列範例所示。 這段程式碼僅會設定字串值，以及先前範例中相同計數器所使用的遞增和遞減，並會顯示下列結果：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
IBatch batch = cache.CreateBatch();
batch.StringSetAsync("data:key1", 11);
var t1 = batch.StringIncrementAsync("data:counter1");
var t2 = batch.StringDecrementAsync("data:counter2");
batch.Execute();
Console.WriteLine("{0}", t1.Result);
Console.WriteLine("{0}", t2.Result);
```

請務必了解這個方法不同於交易，如果批次中的命令因格式不正確而導致失敗，其他命令仍會執行。 `IBatch.Execute` 方法不會傳回成功或失敗的任何指示。

### <a name="perform-fire-and-forget-cache-operations"></a>執行「射後不理」快取作業

Redis 透過使用命令旗標來支援「射後不理」作業。 在此情況下，用戶端僅初始化作業，但對於結果沒有興趣，且並不會等待命令完成。 下列範例示範如何執行 INCR 命令做為「射後不理」作業：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
await cache.StringSetAsync("data:key1", 99);
...
cache.StringIncrement("data:key1", flags: CommandFlags.FireAndForget);
```

### <a name="specify-automatically-expiring-keys"></a>指定索引鍵自動到期

當您在 Redis 快取中儲存項目時，您可以指定逾時，而在指定時間之後，項目會自動從快取中移除。 您也可以在索引鍵到期之前，使用 `TTL` 命令來查詢剩餘時間。 此命令可透過使用 `IDatabase.KeyTimeToLive` 方法而用於 StackExchange 應用程式。

下列程式碼片段示範如何在索引鍵上設定為 20 秒後到期，並查詢索引鍵的剩餘存留期：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration time of 20 seconds
await cache.StringSetAsync("data:key1", 99, TimeSpan.FromSeconds(20));
...
// Query how much time a key has left to live
// If the key has already expired, the KeyTimeToLive function returns a null
TimeSpan? expiry = cache.KeyTimeToLive("data:key1");
```

您也可以使用 StackExchange 程式庫中用來做為 `KeyExpireAsync` 方法的 EXPIRE 命令，為特定的日期和時間設定到期時間：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration date of midnight on 1st January 2015
await cache.StringSetAsync("data:key1", 99);
await cache.KeyExpireAsync("data:key1",
    new DateTime(2015, 1, 1, 0, 0, 0, DateTimeKind.Utc));
...
```

> [!TIP]
> 您可以使用 DEL 命令手動從快取中移除項目，此命令可透過 StackExchange 程式庫用來作為 `IDatabase.KeyDeleteAsync` 方法。

### <a name="use-tags-to-cross-correlate-cached-items"></a>使用標記針對快取項目建立交叉相互關聯

Redis 集是共用單一索引鍵的多個項目集合。 您可以使用 SADD 命令來建立集合。 您可以使用 SMEMBERS 命令來擷取集合中的項目。 StackExchange 程式庫會透過 `IDatabase.SetAddAsync` 方法實作 SADD 命令，並使用 `IDatabase.SetMembersAsync` 方法來實作 SMEMBERS 命令。

您也可以使用 SDIFF (集合差異)、SINTER (集合交集) 和 SUNION (集合聯集) 命令結合現有集合來建立新的集合。 StackExchange 程式庫會以 `IDatabase.SetCombineAsync` 方法整合這些作業。 這個方法的第一個參數會指定要執行的設定作業。

下列程式碼片段顯示如何設定實用集合，以便用於快速儲存和擷取相關項目的集合。 此程式碼會使用本文稍早的＜實作 Redis 快取用戶端應用程式＞一節中所說明的 `BlogPost` 類型。

`BlogPost` 物件包含四個欄位：識別碼、標題、排名分數和標記集合。 下方的第一個程式碼片段示範用於填入 `BlogPost` 物件之 C# 清單的範例資料：

```csharp
List<string[]> tags = new List<string[]>
{
    new[] { "iot","csharp" },
    new[] { "iot","azure","csharp" },
    new[] { "csharp","git","big data" },
    new[] { "iot","git","database" },
    new[] { "database","git" },
    new[] { "csharp","database" },
    new[] { "iot" },
    new[] { "iot","database","git" },
    new[] { "azure","database","big data","git","csharp" },
    new[] { "azure" }
};

List<BlogPost> posts = new List<BlogPost>();
int blogKey = 1;
int numberOfPosts = 20;
Random random = new Random();
for (int i = 0; i < numberOfPosts; i++)
{
    blogKey++;
    posts.Add(new BlogPost(
        blogKey,                  // Blog post ID
        string.Format(CultureInfo.InvariantCulture, "Blog Post #{0}",
            blogKey),             // Blog post title
        random.Next(100, 10000),  // Ranking score
        tags[i % tags.Count]));   // Tags--assigned from a collection
                                  // in the tags list
}
```

您可以在 Redis 快取中針對每個 `BlogPost` 物件將標記儲存成集合，並將每個集合與 `BlogPost` 識別碼建立關聯。 這可讓應用程式快速尋找屬於特定部落格文章的所有標記。 若要啟用反向搜尋，並尋找所有共用特定標記的部落格文章，您可以建立另一個集合，其中保存參考索引鍵中標記識別碼的部落格文章：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Tags are easily represented as Redis Sets
foreach (BlogPost post in posts)
{
    string redisKey = string.Format(CultureInfo.InvariantCulture,
        "blog:posts:{0}:tags", post.Id);
    // Add tags to the blog post in Redis
    await cache.SetAddAsync(
        redisKey, post.Tags.Select(s => (RedisValue)s).ToArray());

    // Now do the inverse so we can figure how which blog posts have a given tag
    foreach (var tag in post.Tags)
    {
        await cache.SetAddAsync(string.Format(CultureInfo.InvariantCulture,
            "tag:{0}:blog:posts", tag), post.Id);
    }
}
```

這些結構可讓您以非常有效率的方式執行許多常見的查詢。 例如，您可以尋找並顯示部落格文章 1 的所有標記，就像這樣：

```csharp
// Show the tags for blog post #1
foreach (var value in await cache.SetMembersAsync("blog:posts:1:tags"))
{
    Console.WriteLine(value);
}
```

您可以執行集合交集作業，尋找部落格文章 1 和部落格文章 2 常用的所有標記，如下所示：

```csharp
// Show the tags in common for blog posts #1 and #2
foreach (var value in await cache.SetCombineAsync(SetOperation.Intersect, new RedisKey[]
    { "blog:posts:1:tags", "blog:posts:2:tags" }))
{
    Console.WriteLine(value);
}
```

您可以尋找包含特定標記的所有部落格文章：

```csharp
// Show the ids of the blog posts that have the tag "iot".
foreach (var value in await cache.SetMembersAsync("tag:iot:blog:posts"))
{
    Console.WriteLine(value);
}
```

### <a name="find-recently-accessed-items"></a>尋找最近存取過的項目

許多應用程式所需的常見工作是尋找最近存取過的項目。 例如，部落格網站可能會想要顯示最近讀取過部落格文章的相關資訊。

您可以使用 Redis 清單來實作這項功能。 Redis 清單包含多個共用相同索引鍵的項目。 清單會用作雙端的佇列。 您可以使用 LPUSH (推送至左) 和 RPUSH (推送至右) 命令，將項目推送至清單兩端。 您可以使用 LPOP 和 RPOP 命令，從清單的任一端擷取項目。 您也可以使用 LRANGE 和 RRANGE 命令，以傳回一組元素。

下列程式碼片段顯示如何使用 StackExchange 程式庫來執行這些作業。 此程式碼使用先前範例中的 `BlogPost` 類型。 當使用者讀取部落格文章時，`IDatabase.ListLeftPushAsync` 方法會將部落格文章的標題推送至與 Redis 快取中索引鍵 "blog:recent_posts" 相關聯的清單。

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:recent_posts";
BlogPost blogPost = ...; // Reference to the blog post that has just been read
await cache.ListLeftPushAsync(
    redisKey, blogPost.Title); // Push the blog post onto the list
```

當讀取更多部落格文章時，其標題會推播至相同的清單。 清單是根據其新增的順序來排序。 最近讀取過的部落格文章會靠向清單左端  (如果同一篇部落格文章讀取一次以上，則它會在清單中具有多個項目)。

您可以使用 `IDatabase.ListRange` 方法，來顯示最近讀取過的文章標題。 這個方法會採用包含清單、起點和終點的索引鍵。 下列程式碼會從清單中最左端擷取 10 個部落格文章 (項目從 0 到 9) 的標題：

```csharp
// Show latest ten posts
foreach (string postTitle in await cache.ListRangeAsync(redisKey, 0, 9))
{
    Console.WriteLine(postTitle);
}
```

請注意， `ListRangeAsync` 方法不會從清單移除項目。 若要這樣做，您可以使用 `IDatabase.ListLeftPopAsync` 和 `IDatabase.ListRightPopAsync` 方法。

若要防止清單無限成長，您可以透過修剪清單以定期挑選項目。 下方程式碼片段會從清單移除所有項目，但最左邊的五個項目除外：

```csharp
await cache.ListTrimAsync(redisKey, 0, 5);
```

### <a name="implement-a-leader-board"></a>實作領導者面板

依預設，集合中的項目不會以任何特定順序來保留。 您可以使用 ZADD 命令 (StackExchange 程式庫中的 `IDatabase.SortedSetAdd` 方法) 來建立已排序的集合。 系統會使用一個稱為分數的數值來排序項目，該分數是做為命令的參數而提供。

下列程式碼片段會將部落格文章的標題新增至已排序的清單中。 在這個範例中，每篇部落格文章也會有包含部落格文章排名的分數欄位。

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:post_rankings";
BlogPost blogPost = ...; // Reference to a blog post that has just been rated
await cache.SortedSetAddAsync(redisKey, blogPost.Title, blogPost.Score);
```

您可以使用 `IDatabase.SortedSetRangeByRankWithScores` 方法，以遞增分數順序來擷取部落格文章的標題和分數：

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(redisKey))
{
    Console.WriteLine(post);
}
```

> [!NOTE]
> StackExchange 程式庫也會提供 `IDatabase.SortedSetRangeByRankAsync` 方法，用來以分數順序傳回資料，但不會傳回分數。

您也可以使用分數遞減順序來擷取項目，並透過將額外參數提供給 `IDatabase.SortedSetRangeByRankWithScoresAsync` 方法來限制傳回項目的數目。 下一個範例會顯示前 10 項排名部落格文章的標題和分數：

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(
                               redisKey, 0, 9, Order.Descending))
{
    Console.WriteLine(post);
}
```

下一個範例會使用 `IDatabase.SortedSetRangeByScoreWithScoresAsync` 方法，可用來限制傳回給那些落在指定分數範圍的項目：

```csharp
// Blog posts with scores between 5000 and 100000
foreach (var post in await cache.SortedSetRangeByScoreWithScoresAsync(
                               redisKey, 5000, 100000))
{
    Console.WriteLine(post);
}
```

### <a name="message-by-using-channels"></a>使用通道傳訊

Redis 伺服器除了用作資料快取，可透過高效能發行者/訂閱者的機制提供傳訊功能。 用戶端應用程式可以訂閱通道，且其他應用程式或服務可以將訊息發佈至通道。 訂閱應用程式接著將會收到這些訊息，以便能夠進行處理。

Redis 會針對要用來訂閱通道的用戶端應用程式提供 SUBSCRIBE 命令。 此命令需要一個或多個的通道名稱，其中應用程式會接受訊息。 StackExchange 程式庫包含 `ISubscription` 介面，可讓 .NET Framework 應用程式訂閱和發佈至通道。

您可以使用 `GetSubscriber` 方法連接到 Redis 伺服器，來建立 `ISubscription` 物件。 然後您可以使用此物件的 `SubscribeAsync` 方法，來接聽通道上的訊息。 下列程式碼範例顯示如何訂閱稱為 "messages:blogPosts" 的頻道：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
await subscriber.SubscribeAsync("messages:blogPosts", (channel, message) => Console.WriteLine("Title is: {0}", message));
```

`Subscribe` 方法的第一個參數即為通道的名稱。 這個名稱會遵循快取中索引鍵所使用的相同慣例。 名稱可以包含任何二進位資料，但建議您最好使用相對較短且有意義的字串，以協助確保良好的效能和可維護性。

請注意通道所使用的命名空間與索引鍵所使用的不同。 這表示，您可以擁有具有相同名稱的通道和索引鍵，不過這可能會讓應用程式的程式碼更難維護。

第二個參數是動作委派。 每當新的訊息出現在通道上，這個委派就會以非同步方式執行。 這個範例僅顯示主控台上的訊息 (訊息將包含部落格文章的標題)。

若要發佈至通道，應用程式可以使用 Redis PUBLISH 命令。 StackExchange 程式庫提供 `IServer.PublishAsync` 方法來執行此作業。 下列程式碼片段顯示如何將訊息發佈到 "messages:blogPosts" 通道：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
BlogPost blogPost = ...;
subscriber.PublishAsync("messages:blogPosts", blogPost.Title);
```

關於發佈/訂閱機制有幾個您應該了解的重點：

- 多個訂用帳戶可以訂閱相同的通道，而且他們全都會收到發佈至該通道的訊息。
- 訂閱者僅會接收訂閱後發佈的訊息。 通道不會進行緩衝處理，一旦發佈訊息之後，Redis 基礎結構就會將訊息推送至每個訂用帳戶並將它移除。
- 依預設，訂閱者會根據傳送的順序來接收訊息。 在具有大量訊息和許多訂閱者和發行者的高度活躍系統中，保證循序傳遞訊息可能會降低系統效能。 如果每個訊息各自獨立且順序並不重要，您就能透過 Redis 系統啟用並行處理，以協助改善回應性。 您可以針對訂用帳戶使用的連接，將 PreserveAsyncOrder 設定為 false，以便在 StackExchange 用戶端中達到此目的：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
redisHostConnection.PreserveAsyncOrder = false;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
```

### <a name="serialization-considerations"></a>序列化考量

當您選擇序列化格式時，請考慮效能、互通性、版本控制、與現有系統的相容性、資料壓縮、記憶體額外負荷之間的取捨。 當您評估效能時，請記住基準測試與內容高度相關。 它們不一定能反映實際的工作負載，且可能不會考慮較新的程式庫或版本。 沒有任何一個「最快速」序列化程式適用於所有的案例。

能考量的選項包括：

- [Protocol Buffers](https://github.com/google/protobuf) (又稱為 protobuf) 是由 Google 開發的序列化格式，能有效率地將結構化的資料序列化。 它使用強型別定義檔案定義訊息結構。 這些定義檔案接著會被編譯為語言特定的程式碼，用於將訊息序列化和還原序列化。 可透過現有的 RPC 機制使用 protobuf，或是 protobuf 可以產生 RPC 服務。

- [Apache Thrift](https://thrift.apache.org/) 採取類似的做法，利用強型別定義檔案和編譯步驟產生序列化程式碼和 RPC 服務。

- [Apache Avro](https://avro.apache.org/) 提供和 Protocol Buffers、Thrift 類似的功能，但是沒有編譯步驟。 而是序列化過的資料一定會包含描述結構的結構描述。

- [JSON](https://json.org/) 是一項開放標準，使用人類看得懂的文字欄位。 它有廣泛的跨平台支援。 JSON 不使用訊息結構描述。 它是文字型格式，透過線路不是非常有效率。 不過，在某些案例中，您可能會透過 HTTP 直接將已快取的項目傳回用戶端，在此情況下，JSON 可以省下成本將一種格式還原序列化，再序列化為 JSON 的成本。

- [BSON](http://bsonspec.org/) 是二進位序列化格式，使用類似 JSON 的結構。 BSON 設計成輕量、能夠輕鬆瀏覽，而且相對於 JSON 可更快速序列化和還原序列化。 承載大小可與 JSON 比較。 BSON 承載可能小於或大於 JSON 酬載，取決於資料。 BSON 另外有一些 JSON 沒有的資料類型，值得注意的是 BinData (用於位元組陣列) 和 Date。

- [MessagePack](https://msgpack.org/) 是二進位序列化格式，設計得更精巧以利透過網路傳輸。 沒有任何訊息結構描述或訊息類型檢查。

- [Bond](https://microsoft.github.io/bond/) 是使用結構描述化資料的跨平台架構。 支援跨語言序列化和還原序列化。 此處列出與其他系統的差異之中值得注意的：繼承、類型別名和泛型的支援。

- [gRPC](https://www.grpc.io/) 是 Google 開發的開放原始碼 RPC 系統。 根據預設，它會使用 Protocol Buffers 作為其定義語言和基礎訊息交換格式。

## <a name="related-patterns-and-guidance"></a>相關的模式和指導方針

在您的應用程式中實作快取時，下列模式也可能與您的案例相關：

- [另行快取模式](../patterns/cache-aside.md)：這個模式描述如何從資料存放區將資料隨選載入快取中。 此模式也有助於維護快取中所保留的資料與原始資料存放區中的資料之間的一致性。

- [Sharding pattern (分區化模式)](../patterns/sharding.md) 會提供實作水平資料分割的資訊，以在儲存和存取大量資料時協助改善延展性。

## <a name="more-information"></a>詳細資訊

- [Azure Redis 快取文件](/azure/azure-cache-for-redis/)
- [Azure Redis 快取常見問題集](/azure/redis-cache/cache-faq)
- [工作式非同步模式](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)
- [Redis 文件](https://redis.io/documentation)
- [StackExchange.Redis](https://stackexchange.github.io/StackExchange.Redis/)
- [資料分割指南](https://msdn.microsoft.com/library/dn589795.aspx)

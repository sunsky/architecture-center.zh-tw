---
title: 整合的持續性反模式
titleSuffix: Performance antipatterns for cloud apps
description: 將所有應用程式的資料放入單一資料存放區會降低效能。
author: dragon119
ms.date: 06/05/2017
ms.custom: seodec18
ms.openlocfilehash: c54a99dd0754cb2cb6cf4ad85b23a518c14a978b
ms.sourcegitcommit: 680c9cef945dff6fee5e66b38e24f07804510fa9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54010251"
---
# <a name="monolithic-persistence-antipattern"></a>整合的持續性反模式

將所有應用程式的資料放入單一資料存放區會降低效能，因為會造成資源爭用，或資料存放區不適合某些資料。

## <a name="problem-description"></a>問題說明

以前應用程式經常使用單一資料存放區，不論需要儲存之應用程式資料的類型是否不同。 通常這是為了簡化應用程式設計，或是符合開發小組的現有技能集。

現代化雲端式系統通常會有其他的功能性和非功能性需求，而且需要儲存許多異質性類型的資料，例如文件、映像、快取資料、已佇列的訊息、應用程式記錄檔和遙測。 遵循將這項資訊全部放入相同資料存放區的傳統方法會降低效能，有兩個主要原因：

- 在相同資料存放區中儲存和擷取大量不相關的資料，可能會造成爭用，進而導致緩慢回應時間和連線失敗。
- 無論選擇哪個資料存放區，無法適合所有不同類型的資料，或無法針對應用程式執行的作業最佳化。

下列範例會顯示 ASP.NET Web API 控制器，它會將新記錄新增至資料庫，也會將結果記錄到記錄檔。 記錄會保留在與商務資料相同的資料庫。 您可以在[這裡][sample-app]找到完整的範例。

```csharp
public class MonoController : ApiController
{
    private static readonly string ProductionDb = ...;

    public async Task<IHttpActionResult> PostAsync([FromBody]string value)
    {
        await DataAccess.InsertPurchaseOrderHeaderAsync(ProductionDb);
        await DataAccess.LogAsync(ProductionDb, LogTableName);
        return Ok();
    }
}
```

記錄檔記錄產生的速率可能會影響商務作業的效能。 如果另一個元件 (例如應用程式處理序監視器) 定期讀取並處理記錄資料，也會影響商務作業。

## <a name="how-to-fix-the-problem"></a>如何修正問題

根據其使用分隔資料。 針對每個資料集，選取最符合該資料集使用方式的資料存放區。 在上一個範例中，應用程式應該會記錄到與保存商務資料之資料庫不同的個別存放區：

```csharp
public class PolyController : ApiController
{
    private static readonly string ProductionDb = ...;
    private static readonly string LogDb = ...;

    public async Task<IHttpActionResult> PostAsync([FromBody]string value)
    {
        await DataAccess.InsertPurchaseOrderHeaderAsync(ProductionDb);
        // Log to a different data store.
        await DataAccess.LogAsync(LogDb, LogTableName);
        return Ok();
    }
}
```

## <a name="considerations"></a>考量

- 根據資料的使用方式及存取方式來分隔資料。 例如，不要將記錄資訊和商務資料儲存在相同的資料存放區。 這些類型的資料有明顯不同的需求和存取模式。 記錄檔記錄本來就是循序的，而商務資料比較像是需要隨機存取，且通常是關聯式。

- 請考慮每種資料類型的資料存取模式。 例如，在像是 [Cosmos DB][CosmosDB] 的文件資料庫儲存格式化報告和文件，但是使用 [Azure Redis 快取][Azure-cache] 以快取暫存資料。

- 如果您遵循本指南，但仍然達到資料庫的限制，您可能需要相應增加資料庫。 另外請考慮水平調整以及在資料庫伺服器之間分割負載。 不過，資料分割可能需要重新設計應用程式。 如需詳細資訊，請參閱[資料分割指引][DataPartitioningGuidance]。

## <a name="how-to-detect-the-problem"></a>如何偵測問題

系統可能會大幅減慢，最終失敗，因為系統用盡例如資料庫連線的資源。

您可以執行下列步驟來協助識別原因。

1. 檢測系統以記錄關鍵效能統計資料。 擷取每個作業的計時資訊，以及應用程式讀取及寫入資料的位置點。
2. 盡可能監視在生產環境中執行數天的系統，以取得系統使用方式的實際檢視。 如果不可行，以執行典型系列作業的虛擬使用者實際數量執行指令碼式負載測試。
3. 使用遙測資料來識別效能不佳的期間。
4. 識別在這些期間存取了哪些資料存放區。
5. 識別可能經歷爭用的資料儲存體資源。

## <a name="example-diagnosis"></a>範例診斷

下列各節會將這些步驟套用到稍早所述的範例應用程式。

### <a name="instrument-and-monitor-the-system"></a>檢測和監視系統

下圖顯示針對稍早所述之範例應用程式進行負載測試的結果。 測試使用最多 1000 個並行使用者的步驟負載。

![以 SQL 為基礎之控制站的負載測試效能結果][MonolithicScenarioLoadTest]

隨著負載增加至 700 個使用者，輸送量也會跟著增加。 屆時，輸送量會穩定，系統會以其最大容量執行。 平均回應會隨著使用者負載而逐漸增加，顯示系統無法跟上需求。

### <a name="identify-periods-of-poor-performance"></a>識別效能不佳的期間

如果您正在監視生產系統，您可能會發現模式。 例如，回應時間可能會在每天的相同時間大幅下滑。 這可能是由一般工作負載或排程批次作業所造成，或者只是因為系統在特定時間有更多使用者。 您應該專注於這些事件的遙測資料。

尋找增加的回應時間和增加的資料庫活動或 I/O 之間的相互關聯，以共用資源。 如果有相互關聯，則表示資料庫可能是瓶頸。

### <a name="identify-which-data-stores-are-accessed-during-those-periods"></a>識別在這些期間存取了哪些資料存放區

下圖會顯示在負載測試期間資料庫輸送量單位 (DTU) 的使用率。 (DTU 是可用容量的量值，也是 CPU 使用率、記憶體配置、I/O 速率的組合。)DTU 使用率快速達到 100%。 這大約是上圖中的輸送量尖峰點。 測試完成之前，資料庫使用率仍然相當高。 到結束之前會有些微下滑，可能是因為節流、資料庫連線的競爭或其他因素所造成。

![Azure 傳統入口網站中的資料庫監視會顯示資料庫的資源使用率][MonolithicDatabaseUtilization]

### <a name="examine-the-telemetry-for-the-data-stores"></a>檢查資料存放區的遙測

檢測資料存放區以擷取活動的低層級詳細資料。 在範例應用程式中，資料存取統計資料會顯示針對 `PurchaseOrderHeader` 資料表和 `MonoLog` 資料表執行的大量插入作業。

![範例應用程式的資料存取統計資料][MonolithicDataAccessStats]

### <a name="identify-resource-contention"></a>識別資源爭用

此時，您可以檢閱原始程式碼，將焦點放在應用程式存取之爭用資源的位置點。 尋找如下的情況：

- 以邏輯方式分隔的資料被寫入相同的存放區。 例如記錄、報告和已佇列的訊息之資料不應該保存在與商務資訊相同的資料庫。
- 資料存放區和資料類型選擇之間的不相符，例如大型 blob 或關聯式資料庫中的 XML 文件。
- 具有差異極大之使用模式但是共用相同存放區的資料，例如以與低寫入/高讀取資料儲存的高寫入/低讀取資料。

### <a name="implement-the-solution-and-verify-the-result"></a>實作解決方案並確認結果

應用程式已變更為將記錄寫入個別資料存放區。 以下是負載測試結果：

![使用 Polyglot 控制器的負載測試效能結果][PolyglotScenarioLoadTest]

輸送量的模式類似於先前的圖表，但是效能尖峰點大約是每秒要求數 500 個以上。 平均回應時間稍低。 不過，這些統計資料不會訴說完整的資訊。 商務資料庫的遙測顯示 DTU 使用率尖峰於大約在 75%，而不是 100%。

![Azure 傳統入口網站中的資料庫監視會顯示 polyglot 案例中資料庫的資源使用率][PolyglotDatabaseUtilization]

同樣地，記錄資料庫的最大 DTU 使用率只會達到大約 70%。 資料庫不再是系統效能的限制因素。

![Azure 傳統入口網站中的資料庫監視會顯示 polyglot 案例中記錄資料庫的資源使用率][LogDatabaseUtilization]

## <a name="related-resources"></a>相關資源

- [選擇正確的資料存放區][data-store-overview]
- [選擇資料存放區的準則][data-store-comparison]
- [高度可調整解決方案的資料存取：使用 SQL、NoSQL 和 Polyglot 持續性][Data-Access-Guide]
- [資料分割][DataPartitioningGuidance]

[sample-app]: https://github.com/mspnp/performance-optimization/tree/master/MonolithicPersistence
[CosmosDB]: https://azure.microsoft.com/services/cosmos-db/
[Azure-cache]: /azure/redis-cache/
[Data-Access-Guide]: https://msdn.microsoft.com/library/dn271399.aspx
[DataPartitioningGuidance]: ../../best-practices/data-partitioning.md
[data-store-overview]: ../../guide/technology-choices/data-store-overview.md
[data-store-comparison]: ../../guide/technology-choices/data-store-comparison.md

[MonolithicScenarioLoadTest]: _images/MonolithicScenarioLoadTest.jpg
[MonolithicDatabaseUtilization]: _images/MonolithicDatabaseUtilization.jpg
[MonolithicDataAccessStats]: _images/MonolithicDataAccessStats.jpg
[PolyglotScenarioLoadTest]: _images/PolyglotScenarioLoadTest.jpg
[PolyglotDatabaseUtilization]: _images/PolyglotDatabaseUtilization.jpg
[LogDatabaseUtilization]: _images/LogDatabaseUtilization.jpg

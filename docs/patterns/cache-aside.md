---
title: 另行快取
description: 依需要從資料存放區將資料載入快取中
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories:
- data-management
- performance-scalability
ms.openlocfilehash: d4d7c9dcd612c780e3e494509a57b6b4a0144423
ms.sourcegitcommit: f665226cec96ec818ca06ac6c2d83edb23c9f29c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
ms.locfileid: "31012455"
---
# <a name="cache-aside-pattern"></a>另行快取模式

[!INCLUDE [header](../_includes/header.md)]

依需要從資料存放區將資料載入快取中。 這可以改善效能並且有助於維持快取中所保留資料與基礎資料存放區中資料之間的一致性。

## <a name="context-and-problem"></a>內容和問題

應用程式使用快取以減少重複存取資料存放區中存放的資訊。 但是，我們不能期望快取的資料會永遠與資料存放區中的資料完全一致。 應用程式應該實作的策略是，協助確保快取中的資料盡可能保持最新，也可以偵測並處理當快取中的資料變成過時的情況。

## <a name="solution"></a>解決方法

許多商業的快取系統提供貫穿式讀取和貫穿式寫入/事後寫入作業。 在這些系統中，應用程式會參考快取來擷取資料。 如果資料不在快取中，就會從資料存放區擷取，並加入快取。 快取中保留的資料若有任何修改，也會自動寫回資料存放區。

如果快取不提供這項功能，使用快取的應用程式就必須負責維護資料。

應用程式可以實作另行快取策略，模擬貫穿式讀取快取的功能。 此策略會依需要將資料載入快取。 此圖說明使用另行快取模式在快取中儲存資料。

![使用另行快取模式在快取中儲存資料](./_images/cache-aside-diagram.png)


如果應用程式更新資訊，可以對資料存放區進行修改，並且讓快取中對應的項目失效，來遵循貫穿式寫入策略。

而當之後需要此項目時，使用另行快取策略將從資料存放區擷取更新過的資料，再加回快取。

## <a name="issues-and-considerations"></a>問題和考量

當您決定如何實作此模式時，請考慮下列幾點： 

**快取資料的存留期**。 許多快取實作的到期原則是，如果資料在指定的期間內沒有被存取過，就會讓資料無效並從快取移除。 如果要讓另行快取有效，請確定到期原則符合使用該資料的應用程式的存取模式。 不要讓到期期間太短，因為這會造成應用程式要持續從資料存放區擷取資料並加入快取。 同樣地，不要讓到期期間太長，而讓快取的資料有可能變成過時。 請記住，快取對於相對靜態的資料或經常讀取的資料是最有效的。

**收回資料**。 大部分的快取相較於資料來源的資料存放區都有大小限制，因此必要時會收回資料。 大部分的快取會採用最近最少使用過的原則來選取要收回的項目，但這可以自訂。 設定快取的全域到期屬性和其他屬性，以及每個快取項目的到期屬性，可確保快取符合成本效益。 對快取中的每個項目套用全域收回原則不一定永遠適合。 例如，如果從資料存放區擷取快取的項目所耗費的資源很高，在快取中保留此項目可能會比經常存取成本較低的項目有利。

**預備快取**。 許多解決方案會使用應用程式可能需要啟動處理程序的資料預先填入快取。 如果有些資料到期或被收回，另行快取模式就還是很有用。

**一致性**。 實作另行快取模式並不保證資料存放區與快取之間的一致性。 資料存放區中的項目可能會隨時由外部處理程序變更，而這項變更可能要到下次載入項目時才會反映在快取中。 在資料存放區之間會複寫資料的系統中，如果經常發生同步處理，這個問題可能會變得很嚴重。

**本機 (記憶體內) 快取**。 快取對於應用程式執行個體可以是本機的，並儲存在記憶體中。 如果應用程式會重複存取相同的資料，另行快取在這樣的環境中就相當有用。 不過，本機快取屬私人性質，因此不同的應用程式執行個體對於相同的快取資料會分別建立一份複本。 這項資料在快取之間可能很快就會變得不一致，因此可能需要更頻繁地讓私人快取中保留的資料到期，然後重新整理資料。 在這些情況下，請考慮使用共用或分散式快取機制。

## <a name="when-to-use-this-pattern"></a>使用此模式的時機

使用此模式的時機包括：

- 快取並不提供原生的貫穿式讀取和貫穿式寫入作業。
- 資源需求無法預測。 此模式可讓應用程式依需要載入資料。 它不會假設應用程式將會事先需要哪些資料。

此模式可能不適合下列時機︰

- 當快取的資料集是靜態的。 如果資料可放入可用的快取空間，在啟動時使用資料預先填入快取，並套用讓資料不會過期的原則。
- 在裝載於 Web 伺服陣列中的 Web 應用程式中快取工作階段狀態資訊。 在此環境中，您應該避免引入以用戶端-伺服器同質性為基礎的相依性。

## <a name="example"></a>範例

在 Microsoft Azure 中，您可以使用 Azure Redis 快取建立可由多個應用程式執行個體共用的分散式快取。 

若要連接至 Azure Redis 快取執行個體，請呼叫靜態 `Connect` 方法並傳入連接字串。 方法會傳回 `ConnectionMultiplexer`，代表連接。 在您的應用程式中共用 `ConnectionMultiplexer` 執行個體的其中一種方法，就是擁有可傳回已連接執行個體的靜態屬性，類似下列範例。 此方法提供安全執行緒方式，只初始化單一已連接的執行個體。

```csharp
private static ConnectionMultiplexer Connection;

// Redis Connection string info
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
{
    string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
    return ConnectionMultiplexer.Connect(cacheConnection);
});

public static ConnectionMultiplexer Connection => lazyConnection.Value;
```

下列程式碼範例中的 `GetMyEntityAsync` 方法示範如何實作以 Azure Redis 快取為基礎的另行快取模式。 此方法會使用貫穿式讀取方法從快取中擷取物件。

物件的識別方式是使用整數識別碼做為索引鍵。 `GetMyEntityAsync` 方法會嘗試使用此金鑰從快取擷取項目。 如果找到相符的項目，就會傳回。 如果快取中沒有符合的項目，`GetMyEntityAsync` 方法就會從資料存放區擷取物件、將它加入快取，然後將它傳回。 實際從資料存放區中讀取資料的程式碼不會在這裡顯示，因為它取決於資料存放區。 請注意，快取的項目已設定為過期，以避免在其他地方經過更新而變成過時。


```csharp
// Set five minute expiration as a default
private const double DefaultExpirationTimeInMinutes = 5.0;

public async Task<MyEntity> GetMyEntityAsync(int id)
{
  // Define a unique key for this method and its parameters.
  var key = $"MyEntity:{id}";
  var cache = Connection.GetDatabase();
  
  // Try to get the entity from the cache.
  var json = await cache.StringGetAsync(key).ConfigureAwait(false);
  var value = string.IsNullOrWhiteSpace(json) 
                ? default(MyEntity) 
                : JsonConvert.DeserializeObject<MyEntity>(json);
  
  if (value == null) // Cache miss
  {
    // If there's a cache miss, get the entity from the original store and cache it.
    // Code has been omitted because it's data store dependent.  
    value = ...;

    // Avoid caching a null value.
    if (value != null)
    {
      // Put the item in the cache with a custom expiration time that 
      // depends on how critical it is to have stale data.
      await cache.StringSetAsync(key, JsonConvert.SerializeObject(value)).ConfigureAwait(false);
      await cache.KeyExpireAsync(key, TimeSpan.FromMinutes(DefaultExpirationTimeInMinutes)).ConfigureAwait(false);
    }
  }

  return value;
}
```

>  範例使用 Azure Redis 快取 API 來存取存放區，並從快取中擷取資訊。 如需詳細資訊，請參閱 [使用 Microsoft Azure Redis 快取](https://docs.microsoft.com/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache)和[如何使用 Redis 快取建立 Web 應用程式](https://docs.microsoft.com/azure/redis-cache/cache-web-app-howto)

下面顯示的 `UpdateEntityAsync` 方法示範如何讓快取中的物件在值被應用程式變更時變成無效。 程式碼會更新原始資料存放區，然後從快取移除快取的項目。

```csharp
public async Task UpdateEntityAsync(MyEntity entity)
{
    // Update the object in the original data store.
    await this.store.UpdateEntityAsync(entity).ConfigureAwait(false); 

    // Invalidate the current cache object.
    var cache = Connection.GetDatabase();
    var id = entity.Id;
    var key = $"MyEntity:{id}"; // The key for the cached object.
    await cache.KeyDeleteAsync(key).ConfigureAwait(false); // Delete this key from the cache.
}
```

> [!NOTE]
> 步驟的順序很重要。 從快取中移除項目*之前*更新資料存放區。 如果您先移除快取的項目，在資料存放區更新之前，會有一小段時間用戶端可能擷取項目。 這會造成快取遺漏 (因為已從快取中移除項目)，導致要從資料存放區中擷取舊版的項目，並新增回快取。 結果會是過時的快取資料。


## <a name="related-guidance"></a>相關的指引 

以下是實作此模式的相關資訊︰

- [快取指引](https://docs.microsoft.com/azure/architecture/best-practices/caching)。 提供如何在雲端解決方案中快取資料，以及當您實作快取時應該考慮的問題的其他資訊。

- [資料一致性入門](https://msdn.microsoft.com/library/dn589800.aspx)。 雲端應用程式通常使用分散在資料存放區各處的資料。 在此環境中管理和維護資料的一致性，是系統一個相當關鍵的部分，尤其是可能發生並行存取和可用性的問題。 此入門說明有關分散式資料之間一致性的問題，並摘要說明應用程式如何實作最終一致性，以維持資料的可用性。

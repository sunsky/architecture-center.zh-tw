---
title: "另行快取"
description: "依需要從資料存放區將資料載入快取中"
keywords: "設計模式"
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories:
- data-management
- performance-scalability
ms.openlocfilehash: e0a6a91fda6ea43236f6eea552f7b8f8d31160ad
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="cache-aside-pattern"></a><span data-ttu-id="ace12-104">另行快取模式</span><span class="sxs-lookup"><span data-stu-id="ace12-104">Cache-Aside pattern</span></span>

[!INCLUDE [header](../_includes/header.md)]

<span data-ttu-id="ace12-105">依需要從資料存放區將資料載入快取中。</span><span class="sxs-lookup"><span data-stu-id="ace12-105">Load data on demand into a cache from a data store.</span></span> <span data-ttu-id="ace12-106">這可以改善效能並且有助於維持快取中所保留資料與基礎資料存放區中資料之間的一致性。</span><span class="sxs-lookup"><span data-stu-id="ace12-106">This can improve performance and also helps to maintain consistency between data held in the cache and data in the underlying data store.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="ace12-107">內容和問題</span><span class="sxs-lookup"><span data-stu-id="ace12-107">Context and problem</span></span>

<span data-ttu-id="ace12-108">應用程式使用快取以減少重複存取資料存放區中存放的資訊。</span><span class="sxs-lookup"><span data-stu-id="ace12-108">Applications use a cache to improve repeated access to information held in a data store.</span></span> <span data-ttu-id="ace12-109">但是，我們不能期望快取的資料會永遠與資料存放區中的資料完全一致。</span><span class="sxs-lookup"><span data-stu-id="ace12-109">However, it's impractical to expect that cached data will always be completely consistent with the data in the data store.</span></span> <span data-ttu-id="ace12-110">應用程式應該實作的策略是，協助確保快取中的資料盡可能保持最新，也可以偵測並處理當快取中的資料變成過時的情況。</span><span class="sxs-lookup"><span data-stu-id="ace12-110">Applications should implement a strategy that helps to ensure that the data in the cache is as up-to-date as possible, but can also detect and handle situations that arise when the data in the cache has become stale.</span></span>

## <a name="solution"></a><span data-ttu-id="ace12-111">方案</span><span class="sxs-lookup"><span data-stu-id="ace12-111">Solution</span></span>

<span data-ttu-id="ace12-112">許多商業的快取系統提供貫穿式讀取和貫穿式寫入/事後寫入作業。</span><span class="sxs-lookup"><span data-stu-id="ace12-112">Many commercial caching systems provide read-through and write-through/write-behind operations.</span></span> <span data-ttu-id="ace12-113">在這些系統中，應用程式會參考快取來擷取資料。</span><span class="sxs-lookup"><span data-stu-id="ace12-113">In these systems, an application retrieves data by referencing the cache.</span></span> <span data-ttu-id="ace12-114">如果資料不在快取中，就會從資料存放區擷取，並加入快取。</span><span class="sxs-lookup"><span data-stu-id="ace12-114">If the data isn't in the cache, it's retrieved from the data store and added to the cache.</span></span> <span data-ttu-id="ace12-115">快取中保留的資料若有任何修改，也會自動寫回資料存放區。</span><span class="sxs-lookup"><span data-stu-id="ace12-115">Any modifications to data held in the cache are automatically written back to the data store as well.</span></span>

<span data-ttu-id="ace12-116">如果快取不提供這項功能，使用快取的應用程式就必須負責維護資料。</span><span class="sxs-lookup"><span data-stu-id="ace12-116">For caches that don't provide this functionality, it's the responsibility of the applications that use the cache to maintain the data.</span></span>

<span data-ttu-id="ace12-117">應用程式可以實作另行快取策略，模擬貫穿式讀取快取的功能。</span><span class="sxs-lookup"><span data-stu-id="ace12-117">An application can emulate the functionality of read-through caching by implementing the cache-aside strategy.</span></span> <span data-ttu-id="ace12-118">此策略會依需要將資料載入快取。</span><span class="sxs-lookup"><span data-stu-id="ace12-118">This strategy loads data into the cache on demand.</span></span> <span data-ttu-id="ace12-119">此圖說明使用另行快取模式在快取中儲存資料。</span><span class="sxs-lookup"><span data-stu-id="ace12-119">The figure illustrates using the Cache-Aside pattern to store data in the cache.</span></span>

![使用另行快取模式在快取中儲存資料](./_images/cache-aside-diagram.png)


<span data-ttu-id="ace12-121">如果應用程式更新資訊，可以對資料存放區進行修改，並且讓快取中對應的項目失效，來遵循貫穿式寫入策略。</span><span class="sxs-lookup"><span data-stu-id="ace12-121">If an application updates information, it can follow the write-through strategy by making the modification to the data store, and by invalidating the corresponding item in the cache.</span></span>

<span data-ttu-id="ace12-122">而當之後需要此項目時，使用另行快取策略將從資料存放區擷取更新過的資料，再加回快取。</span><span class="sxs-lookup"><span data-stu-id="ace12-122">When the item is next required, using the cache-aside strategy will cause the updated data to be retrieved from the data store and added back into the cache.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="ace12-123">問題和考量</span><span class="sxs-lookup"><span data-stu-id="ace12-123">Issues and considerations</span></span>

<span data-ttu-id="ace12-124">當您決定如何實作此模式時，請考慮下列幾點：</span><span class="sxs-lookup"><span data-stu-id="ace12-124">Consider the following points when deciding how to implement this pattern:</span></span> 

<span data-ttu-id="ace12-125">**快取資料的存留期**。</span><span class="sxs-lookup"><span data-stu-id="ace12-125">**Lifetime of cached data**.</span></span> <span data-ttu-id="ace12-126">許多快取實作的到期原則是，如果資料在指定的期間內沒有被存取過，就會讓資料無效並從快取移除。</span><span class="sxs-lookup"><span data-stu-id="ace12-126">Many caches implement an expiration policy that invalidates data and removes it from the cache if it's not accessed for a specified period.</span></span> <span data-ttu-id="ace12-127">如果要讓另行快取有效，請確定到期原則符合使用該資料的應用程式的存取模式。</span><span class="sxs-lookup"><span data-stu-id="ace12-127">For cache-aside to be effective, ensure that the expiration policy matches the pattern of access for applications that use the data.</span></span> <span data-ttu-id="ace12-128">不要讓到期期間太短，因為這會造成應用程式要持續從資料存放區擷取資料並加入快取。</span><span class="sxs-lookup"><span data-stu-id="ace12-128">Don't make the expiration period too short because this can cause applications to continually retrieve data from the data store and add it to the cache.</span></span> <span data-ttu-id="ace12-129">同樣地，不要讓到期期間太長，而讓快取的資料有可能變成過時。</span><span class="sxs-lookup"><span data-stu-id="ace12-129">Similarly, don't make the expiration period so long that the cached data is likely to become stale.</span></span> <span data-ttu-id="ace12-130">請記住，快取對於相對靜態的資料或經常讀取的資料是最有效的。</span><span class="sxs-lookup"><span data-stu-id="ace12-130">Remember that caching is most effective for relatively static data, or data that is read frequently.</span></span>

<span data-ttu-id="ace12-131">**收回資料**。</span><span class="sxs-lookup"><span data-stu-id="ace12-131">**Evicting data**.</span></span> <span data-ttu-id="ace12-132">大部分的快取相較於資料來源的資料存放區都有大小限制，因此必要時會收回資料。</span><span class="sxs-lookup"><span data-stu-id="ace12-132">Most caches have a limited size compared to the data store where the data originates, and they'll evict data if necessary.</span></span> <span data-ttu-id="ace12-133">大部分的快取會採用最近最少使用過的原則來選取要收回的項目，但這可以自訂。</span><span class="sxs-lookup"><span data-stu-id="ace12-133">Most caches adopt a least-recently-used policy for selecting items to evict, but this might be customizable.</span></span> <span data-ttu-id="ace12-134">設定快取的全域到期屬性和其他屬性，以及每個快取項目的到期屬性，可確保快取符合成本效益。</span><span class="sxs-lookup"><span data-stu-id="ace12-134">Configure the global expiration property and other properties of the cache, and the expiration property of each cached item, to ensure that the cache is cost effective.</span></span> <span data-ttu-id="ace12-135">對快取中的每個項目套用全域收回原則不一定永遠適合。</span><span class="sxs-lookup"><span data-stu-id="ace12-135">It isn't always appropriate to apply a global eviction policy to every item in the cache.</span></span> <span data-ttu-id="ace12-136">例如，如果從資料存放區擷取快取的項目所耗費的資源很高，在快取中保留此項目可能會比經常存取成本較低的項目有利。</span><span class="sxs-lookup"><span data-stu-id="ace12-136">For example, if a cached item is very expensive to retrieve from the data store, it can be beneficial to keep this item in the cache at the expense of more frequently accessed but less costly items.</span></span>

<span data-ttu-id="ace12-137">**預備快取**。</span><span class="sxs-lookup"><span data-stu-id="ace12-137">**Priming the cache**.</span></span> <span data-ttu-id="ace12-138">許多解決方案會使用應用程式可能需要啟動處理程序的資料預先填入快取。</span><span class="sxs-lookup"><span data-stu-id="ace12-138">Many solutions prepopulate the cache with the data that an application is likely to need as part of the startup processing.</span></span> <span data-ttu-id="ace12-139">如果有些資料到期或被收回，另行快取模式就還是很有用。</span><span class="sxs-lookup"><span data-stu-id="ace12-139">The Cache-Aside pattern can still be useful if some of this data expires or is evicted.</span></span>

<span data-ttu-id="ace12-140">**一致性**。</span><span class="sxs-lookup"><span data-stu-id="ace12-140">**Consistency**.</span></span> <span data-ttu-id="ace12-141">實作另行快取模式並不保證資料存放區與快取之間的一致性。</span><span class="sxs-lookup"><span data-stu-id="ace12-141">Implementing the Cache-Aside pattern doesn't guarantee consistency between the data store and the cache.</span></span> <span data-ttu-id="ace12-142">資料存放區中的項目可能會隨時由外部處理程序變更，而這項變更可能要到下次載入項目時才會反映在快取中。</span><span class="sxs-lookup"><span data-stu-id="ace12-142">An item in the data store can be changed at any time by an external process, and this change might not be reflected in the cache until the next time the item is loaded.</span></span> <span data-ttu-id="ace12-143">在資料存放區之間會複寫資料的系統中，如果經常發生同步處理，這個問題可能會變得很嚴重。</span><span class="sxs-lookup"><span data-stu-id="ace12-143">In a system that replicates data across data stores, this problem can become serious if synchronization occurs frequently.</span></span>

<span data-ttu-id="ace12-144">**本機 (記憶體內) 快取**。</span><span class="sxs-lookup"><span data-stu-id="ace12-144">**Local (in-memory) caching**.</span></span> <span data-ttu-id="ace12-145">快取對於應用程式執行個體可以是本機的，並儲存在記憶體中。</span><span class="sxs-lookup"><span data-stu-id="ace12-145">A cache could be local to an application instance and stored in-memory.</span></span> <span data-ttu-id="ace12-146">如果應用程式會重複存取相同的資料，另行快取在這樣的環境中就相當有用。</span><span class="sxs-lookup"><span data-stu-id="ace12-146">Cache-aside can be useful in this environment if an application repeatedly accesses the same data.</span></span> <span data-ttu-id="ace12-147">不過，本機快取屬私人性質，因此不同的應用程式執行個體對於相同的快取資料會分別建立一份複本。</span><span class="sxs-lookup"><span data-stu-id="ace12-147">However, a local cache is private and so different application instances could each have a copy of the same cached data.</span></span> <span data-ttu-id="ace12-148">這項資料在快取之間可能很快就會變得不一致，因此可能需要更頻繁地讓私人快取中保留的資料到期，然後重新整理資料。</span><span class="sxs-lookup"><span data-stu-id="ace12-148">This data could quickly become inconsistent between caches, so it might be necessary to expire data held in a private cache and refresh it more frequently.</span></span> <span data-ttu-id="ace12-149">在這些情況下，請考慮使用共用或分散式快取機制。</span><span class="sxs-lookup"><span data-stu-id="ace12-149">In these scenarios, consider investigating the use of a shared or a distributed caching mechanism.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="ace12-150">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="ace12-150">When to use this pattern</span></span>

<span data-ttu-id="ace12-151">使用此模式的時機包括：</span><span class="sxs-lookup"><span data-stu-id="ace12-151">Use this pattern when:</span></span>

- <span data-ttu-id="ace12-152">快取並不提供原生的貫穿式讀取和貫穿式寫入作業。</span><span class="sxs-lookup"><span data-stu-id="ace12-152">A cache doesn't provide native read-through and write-through operations.</span></span>
- <span data-ttu-id="ace12-153">資源需求無法預測。</span><span class="sxs-lookup"><span data-stu-id="ace12-153">Resource demand is unpredictable.</span></span> <span data-ttu-id="ace12-154">此模式可讓應用程式依需要載入資料。</span><span class="sxs-lookup"><span data-stu-id="ace12-154">This pattern enables applications to load data on demand.</span></span> <span data-ttu-id="ace12-155">它不會假設應用程式將會事先需要哪些資料。</span><span class="sxs-lookup"><span data-stu-id="ace12-155">It makes no assumptions about which data an application will require in advance.</span></span>

<span data-ttu-id="ace12-156">此模式可能不適合下列時機︰</span><span class="sxs-lookup"><span data-stu-id="ace12-156">This pattern might not be suitable:</span></span>

- <span data-ttu-id="ace12-157">當快取的資料集是靜態的。</span><span class="sxs-lookup"><span data-stu-id="ace12-157">When the cached data set is static.</span></span> <span data-ttu-id="ace12-158">如果資料可放入可用的快取空間，在啟動時使用資料預先填入快取，並套用讓資料不會過期的原則。</span><span class="sxs-lookup"><span data-stu-id="ace12-158">If the data will fit into the available cache space, prime the cache with the data on startup and apply a policy that prevents the data from expiring.</span></span>
- <span data-ttu-id="ace12-159">在裝載於 Web 伺服陣列中的 Web 應用程式中快取工作階段狀態資訊。</span><span class="sxs-lookup"><span data-stu-id="ace12-159">For caching session state information in a web application hosted in a web farm.</span></span> <span data-ttu-id="ace12-160">在此環境中，您應該避免引入以用戶端-伺服器同質性為基礎的相依性。</span><span class="sxs-lookup"><span data-stu-id="ace12-160">In this environment, you should avoid introducing dependencies based on client-server affinity.</span></span>

## <a name="example"></a><span data-ttu-id="ace12-161">範例</span><span class="sxs-lookup"><span data-stu-id="ace12-161">Example</span></span>

<span data-ttu-id="ace12-162">在 Microsoft Azure 中，您可以使用 Azure Redis 快取建立可由多個應用程式執行個體共用的分散式快取。</span><span class="sxs-lookup"><span data-stu-id="ace12-162">In Microsoft Azure you can use Azure Redis Cache to create a distributed cache that can be shared by multiple instances of an application.</span></span> 

<span data-ttu-id="ace12-163">若要連接至 Azure Redis 快取執行個體，請呼叫靜態 `Connect` 方法並傳入連接字串。</span><span class="sxs-lookup"><span data-stu-id="ace12-163">To connect to an Azure Redis Cache instance, call the static `Connect` method and pass in the connection string.</span></span> <span data-ttu-id="ace12-164">方法會傳回 `ConnectionMultiplexer`，代表連接。</span><span class="sxs-lookup"><span data-stu-id="ace12-164">The method returns a `ConnectionMultiplexer` that represents the connection.</span></span> <span data-ttu-id="ace12-165">在您的應用程式中共用 `ConnectionMultiplexer` 執行個體的其中一種方法，就是擁有可傳回已連接執行個體的靜態屬性，類似下列範例。</span><span class="sxs-lookup"><span data-stu-id="ace12-165">One approach to sharing a `ConnectionMultiplexer` instance in your application is to have a static property that returns a connected instance, similar to the following example.</span></span> <span data-ttu-id="ace12-166">此方法提供安全執行緒方式，只初始化單一已連接的執行個體。</span><span class="sxs-lookup"><span data-stu-id="ace12-166">This approach provides a thread-safe way to initialize only a single connected instance.</span></span>

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

<span data-ttu-id="ace12-167">下列程式碼範例中的 `GetMyEntityAsync` 方法示範如何實作以 Azure Redis 快取為基礎的另行快取模式。</span><span class="sxs-lookup"><span data-stu-id="ace12-167">The `GetMyEntityAsync` method in the following code example shows an implementation of the Cache-Aside pattern based on Azure Redis Cache.</span></span> <span data-ttu-id="ace12-168">此方法使用貫穿式讀取方法從快取中擷取物件。</span><span class="sxs-lookup"><span data-stu-id="ace12-168">This method retrieves an object from the cache using the read-though approach.</span></span>

<span data-ttu-id="ace12-169">物件的識別方式是使用整數識別碼做為索引鍵。</span><span class="sxs-lookup"><span data-stu-id="ace12-169">An object is identified by using an integer ID as the key.</span></span> <span data-ttu-id="ace12-170">`GetMyEntityAsync` 方法會嘗試使用此金鑰從快取擷取項目。</span><span class="sxs-lookup"><span data-stu-id="ace12-170">The `GetMyEntityAsync` method tries to retrieve an item with this key from the cache.</span></span> <span data-ttu-id="ace12-171">如果找到相符的項目，就會傳回。</span><span class="sxs-lookup"><span data-stu-id="ace12-171">If a matching item is found, it's returned.</span></span> <span data-ttu-id="ace12-172">如果快取中沒有符合的項目，`GetMyEntityAsync` 方法就會從資料存放區擷取物件、將它加入快取，然後將它傳回。</span><span class="sxs-lookup"><span data-stu-id="ace12-172">If there's no match in the cache, the `GetMyEntityAsync` method retrieves the object from a data store, adds it to the cache, and then returns it.</span></span> <span data-ttu-id="ace12-173">實際從資料存放區中讀取資料的程式碼不會在這裡顯示，因為它取決於資料存放區。</span><span class="sxs-lookup"><span data-stu-id="ace12-173">The code that actually reads the data from the data store is not shown here, because it depends on the data store.</span></span> <span data-ttu-id="ace12-174">請注意，快取的項目已設定為過期，以避免在其他地方經過更新而變成過時。</span><span class="sxs-lookup"><span data-stu-id="ace12-174">Note that the cached item is configured to expire to prevent it from becoming stale if it's updated elsewhere.</span></span>


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

>  <span data-ttu-id="ace12-175">範例使用 Azure Redis 快取 API 來存取存放區，並從快取中擷取資訊。</span><span class="sxs-lookup"><span data-stu-id="ace12-175">The examples use the Azure Redis Cache API to access the store and retrieve information from the cache.</span></span> <span data-ttu-id="ace12-176">如需詳細資訊，請參閱 [使用 Microsoft Azure Redis 快取](https://docs.microsoft.com/en-us/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache)和[如何使用 Redis 快取建立 Web 應用程式](https://docs.microsoft.com/en-us/azure/redis-cache/cache-web-app-howto)</span><span class="sxs-lookup"><span data-stu-id="ace12-176">For more information, see [Using Microsoft Azure Redis Cache](https://docs.microsoft.com/en-us/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache) and [How to create a Web App with Redis Cache](https://docs.microsoft.com/en-us/azure/redis-cache/cache-web-app-howto)</span></span>

<span data-ttu-id="ace12-177">下面顯示的 `UpdateEntityAsync` 方法示範如何讓快取中的物件在值被應用程式變更時變成無效。</span><span class="sxs-lookup"><span data-stu-id="ace12-177">The `UpdateEntityAsync` method shown below demonstrates how to invalidate an object in the cache when the value is changed by the application.</span></span> <span data-ttu-id="ace12-178">程式碼會更新原始資料存放區，然後從快取移除快取的項目。</span><span class="sxs-lookup"><span data-stu-id="ace12-178">The code updates the original data store and then removes the cached item from the cache.</span></span>

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
> <span data-ttu-id="ace12-179">步驟的順序很重要。</span><span class="sxs-lookup"><span data-stu-id="ace12-179">The order of the steps is important.</span></span> <span data-ttu-id="ace12-180">從快取中移除項目*之前*更新資料存放區。</span><span class="sxs-lookup"><span data-stu-id="ace12-180">Update the data store *before* removing the item from the cache.</span></span> <span data-ttu-id="ace12-181">如果您先移除快取的項目，在資料存放區更新之前，會有一小段時間用戶端可能擷取項目。</span><span class="sxs-lookup"><span data-stu-id="ace12-181">If you remove the cached item first, there is a small window of time when a client might fetch the item before the data store is updated.</span></span> <span data-ttu-id="ace12-182">這會造成快取遺漏 (因為已從快取中移除項目)，導致要從資料存放區中擷取舊版的項目，並新增回快取。</span><span class="sxs-lookup"><span data-stu-id="ace12-182">That will result in a cache miss (because the item was removed from the cache), causing the earlier version of the item to be fetched from the data store and added back into the cache.</span></span> <span data-ttu-id="ace12-183">結果會是過時的快取資料。</span><span class="sxs-lookup"><span data-stu-id="ace12-183">The result will be stale cache data.</span></span>


## <a name="related-guidance"></a><span data-ttu-id="ace12-184">相關的指引</span><span class="sxs-lookup"><span data-stu-id="ace12-184">Related guidance</span></span> 

<span data-ttu-id="ace12-185">以下是實作此模式的相關資訊︰</span><span class="sxs-lookup"><span data-stu-id="ace12-185">The following information may be relevant when implementing this pattern:</span></span>

- <span data-ttu-id="ace12-186">[快取指引](https://docs.microsoft.com/en-us/azure/architecture/best-practices/caching)。</span><span class="sxs-lookup"><span data-stu-id="ace12-186">[Caching Guidance](https://docs.microsoft.com/en-us/azure/architecture/best-practices/caching).</span></span> <span data-ttu-id="ace12-187">提供如何在雲端解決方案中快取資料，以及當您實作快取時應該考慮的問題的其他資訊。</span><span class="sxs-lookup"><span data-stu-id="ace12-187">Provides additional information on how you can cache data in a cloud solution, and the issues that you should consider when you implement a cache.</span></span>

- <span data-ttu-id="ace12-188">[資料一致性入門](https://msdn.microsoft.com/library/dn589800.aspx)。</span><span class="sxs-lookup"><span data-stu-id="ace12-188">[Data Consistency Primer](https://msdn.microsoft.com/library/dn589800.aspx).</span></span> <span data-ttu-id="ace12-189">雲端應用程式通常使用分散在資料存放區各處的資料。</span><span class="sxs-lookup"><span data-stu-id="ace12-189">Cloud applications typically use data that's spread across data stores.</span></span> <span data-ttu-id="ace12-190">在此環境中管理和維護資料的一致性，是系統一個相當關鍵的部分，尤其是可能發生並行存取和可用性的問題。</span><span class="sxs-lookup"><span data-stu-id="ace12-190">Managing and maintaining data consistency in this environment is a critical aspect of the system, particularly the concurrency and availability issues that can arise.</span></span> <span data-ttu-id="ace12-191">此入門說明有關分散式資料之間一致性的問題，並摘要說明應用程式如何實作最終一致性，以維持資料的可用性。</span><span class="sxs-lookup"><span data-stu-id="ace12-191">This primer describes issues about consistency across distributed data, and summarizes how an application can implement eventual consistency to maintain the availability of data.</span></span>

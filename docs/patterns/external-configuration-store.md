---
title: 外部設定存放區
description: 將設定資訊從應用程式部署套件移至集中位置。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories:
- design-implementation
- management-monitoring
ms.openlocfilehash: 733ca979903d1526d3a1a6b281a8903893e19fda
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
ms.locfileid: "24542276"
---
# <a name="external-configuration-store-pattern"></a>外部設定存放區模式

[!INCLUDE [header](../_includes/header.md)]

將設定資訊從應用程式部署套件移至集中位置。 如此會使得設定資料更易於管理和控制，並能跨應用程式和應用程式執行個體來共用設定資料。

## <a name="context-and-problem"></a>內容和問題

大部分的應用程式執行階段環境都含有設定資訊，其保存在隨應用程式部署的檔案中。 在某些情況下，可以編輯這些檔案以變更部署後的應用程式行為。 不過，變更設定後需要重新部署應用程式，而且通常會導致無法接受的停機時間及其他系統管理額外負荷。

本機設定檔也會將設定限制在單一應用程式，但有時候跨多個應用程式共用設定是很有用的。 範例包括資料庫連接字串、UI 佈景主題資訊，或一組相關應用程式所使用的佇列與儲存體 URL。

跨多個執行中的應用程式執行個體來管理本機設定變更是極具挑戰的，特別是雲端主控的案例。 它可能會導致在部署更新之際，執行個體會使用不同的組態設定。

此外，應用程式和元件的更新可能需要變更設定結構描述。 許多設定系統不支援不同版本的設定資訊。

## <a name="solution"></a>方案

將設定資訊儲存在外部儲存體，並提供可迅速、有效地讀取和更新組態設定的介面。 外部存放區的類型取決於應用程式的裝載和執行階段環境。 在雲端主控的案例中，它通常是以雲端為基礎的儲存體服務，但也可能是主控資料庫或其他系統。

您為設定資訊選擇的備份存放區，應具有存取方式一致且易於使用的介面。 它應該以正確型別和結構化的格式來公開資訊。 實作可能也需要授權使用者的存取權以保護設定資料，並具備足夠的彈性以允許儲存多個版本的設定 (例如開發、預備或生產，包括其中每一種的多個發行版本)。

> 許多內建設定系統會在應用程式啟動時讀取資料，並將資料快取於記憶體中，以提供快速存取並將對應用程式效能的影響降到最低。 根據使用的備份存放區類型和此存放區的延遲而定，在外部設定存放區中實作快取機制可能會有幫助。 如需詳細資訊，請參閱「 [快取指引](https://msdn.microsoft.com/library/dn589802.aspx)」。 此圖概要說明具有選擇性本機快取的外部設定存放區模式。

![具有選擇性本機快取的外部設定存放區模式概觀](./_images/external-configuration-store-overview.png)


## <a name="issues-and-considerations"></a>問題和考量

當您決定如何實作此模式時，請考慮下列幾點：

選擇的備份存放區應提供可接受的效能、高可用性及穩定性，而且可在應用程式維護和管理程序中進行備份。 在雲端主控的應用程式中，使用雲端儲存體機制通常是符合這些需求的不錯選擇。

備份存放區結構描述的設計，應使可保存的資訊類型具有彈性。 請確定它可滿足所有設定需求，例如具有型別的資料、設定的集合、設定的多個版本，以及使用它的應用程式所需的其他任何功能。 結構描述應可隨需求變更輕鬆擴充，以支援額外的設定。

請考慮備份存放區的實體功能、與設定資訊儲存方式的關聯，以及對效能的影響。 例如，儲存包含設定資訊的 XML 文件，將需要設定介面或應用程式來剖析文件，以讀取個別設定。 雖然快取設定有助於抵銷變慢的讀取效能，但會使更新設定更為複雜。

請考慮設定介面如何允許控制組態設定的範圍與繼承。 比方說，組態設定的範圍可能需分為組織、應用程式及電腦層級。 它可能需要支援不同範圍的存取權控制委派，或是拒絕或允許個別應用程式覆寫設定。

請確定設定介面能夠以必要的格式 (例如具有型別的值、集合、索引鍵/值組或屬性包) 來公開設定資料。

請考慮當設定包含錯誤或不存在於備份存放區中時，設定存放區介面將如何作用。 傳回預設設定並記錄錯誤可能是合適的作法。 也請考慮以下層面：例如組態設定索引鍵或名稱的大小寫之別、二進位資料的儲存和處理，以及 Null 或空值的處理方式。

請考慮如何保護設定資料，以便只允許適當的使用者和應用程式來存取。 這可能是設定存放區介面中的一項功能，但也需確保若無適當權限，則無法直接存取備份存放區中的資料。 請嚴格區分讀取和寫入設定資料所需的權限。 也請考慮您需要加密部分或所有的組態設定，以及要如何實作於設定存放區介面中。

在執行階段變更應用程式行為的集中儲存設定極為重要，應使用和部署應用程式程式碼相同的機制來部署、更新和管理。 例如，可能會影響多個應用程式的變更必須使用完整的測試和分段部署方法來實行，以確定變更適用於使用此設定的所有應用程式。 如果系統管理員編輯了一項更新某個應用程式的設定，它可能會對使用相同設定的其他應用程式造成不良影響。

如果應用程式會快取設定資訊，則必須在設定變更時向應用程式發出警示。 在快取的設定資料上實作到期原則是可行的方法，如此一來，這項資訊就會自動定期重新整理，並會接收任何變更 (和採取行動)。

## <a name="when-to-use-this-pattern"></a>使用此模式的時機

這種模式在下列情況中非常有用：

- 由多個應用程式和應用程式執行個體所共用的組態設定，或必須跨多個應用程式和應用程式執行個體來強制執行標準設定的狀況。

- 不支援所有必要組態設定的標準設定系統，像是儲存影像或複雜的資料類型。

- 作為應用程式部分設定的互補存放區，可能允許應用程式覆寫部分或全部的集中儲存設定。

- 作為簡化管理多個應用程式的方式，以及透過記錄部分或所有類型的設定存放區存取，以監視組態設定的使用。

## <a name="example"></a>範例

在 Microsoft Azure 主控的應用程式中，若要在外部儲存設定資訊，通常的選擇就是使用 Azure 儲存體。 這具有復原功能、提供高效能，而且會以自動容錯移轉複寫三次，以提供高可用性。 Azure 資料表儲存體提供索引鍵/值存放區，具有針對值使用彈性結構描述的能力。 Azure Blob 儲存體提供階層式的容器型存放區，可以在個別命名的 Blob 中保存任何資料類型。

下列範例示範如何透過 Blob 儲存體實作設定存放區，以儲存和公開設定資訊。 `BlobSettingsStore` 類別會將 Blob 儲存體抽象化以用於保存設定資訊，並會實作下列程式碼中顯示的 `ISettingsStore` 介面。

> 此程式碼來自 [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/external-configuration-store) \(英文\) 中 _ExternalConfigurationStore_ 解決方案的 _ExternalConfigurationStore.Cloud_ 專案。

```csharp
public interface ISettingsStore
{
    Task<string> GetVersionAsync();

    Task<Dictionary<string, string>> FindAllAsync();
}
```

這個介面會定義用來擷取和更新設定存放區中所保存組態設定的方法，並且包含一個版本號碼，可用來偵測最近是否修改過任何組態設定。 `BlobSettingsStore` 類別使用 Blob 的 `ETag` 屬性來實作版本設定。 每次寫入 Blob 時，就會自動更新 `ETag` 屬性。

> 根據設計，這個簡單的解決方案會將所有組態設定公開為字串值，而非具有型別的值。

`ExternalConfigurationManager` 類別提供 `BlobSettingsStore` 物件的包裝函式。 應用程式可以使用這個類別來儲存和擷取設定資訊。 這個類別會使用 Microsoft [Reactive Extensions](https://msdn.microsoft.com/library/hh242985.aspx) 程式庫來公開透過實作 `IObservable` 介面對設定所做的任何變更。 如果設定是透過呼叫 `SetAppSetting` 方法來修改，就會引發 `Changed` 事件，而且將通知此事件的所有訂閱者。

請注意，所有設定也會快取到 `ExternalConfigurationManager` 類別內的 `Dictionary` 物件，以提供快速存取。 用來擷取組態設定的 `GetSetting` 方法會從快取讀取資料。 如果在快取中找不到設定，它會改從 `BlobSettingsStore` 物件中擷取。

`GetSettings` 方法會叫用 `CheckForConfigurationChanges` 方法來偵測 Blob 儲存體中的設定資訊是否已變更。 它的作法是檢查版本號碼，並將它與 `ExternalConfigurationManager` 物件所持有的目前版本號碼進行比較。 如果發生一或多個變更，就會引發 `Changed` 事件，並重新整理快取到 `Dictionary` 物件的組態設定。 這是[另行快取模式](cache-aside.md)的應用程式。

下列程式碼範例示範如何實作 `Changed` 事件、`GetSettings` 方法和 `CheckForConfigurationChanges` 方法：

```csharp
public class ExternalConfigurationManager : IDisposable
{
  // An abstraction of the configuration store.
  private readonly ISettingsStore settings;
  private readonly ISubject<KeyValuePair<string, string>> changed;
  ...
  private readonly ReaderWriterLockSlim settingsCacheLock = new ReaderWriterLockSlim();
  private readonly SemaphoreSlim syncCacheSemaphore = new SemaphoreSlim(1);  
  ...
  private Dictionary<string, string> settingsCache;
  private string currentVersion;
  ...
  public ExternalConfigurationManager(ISettingsStore settings, ...)
  {
    this.settings = settings;
    ...
  }
  ...
  public IObservable<KeyValuePair<string, string>> Changed => this.changed.AsObservable();
  ...

  public string GetAppSetting(string key)
  {
    ...
    // Try to get the value from the settings cache. 
    // If there's a cache miss, get the setting from the settings store and refresh the settings cache.

    string value;
    try
    {
        this.settingsCacheLock.EnterReadLock();

        this.settingsCache.TryGetValue(key, out value);
    }
    finally
    {
        this.settingsCacheLock.ExitReadLock();
    }

    return value;
  }
  ...
  private void CheckForConfigurationChanges()
  {
    try
    {
        // It is assumed that updates are infrequent.
        // To avoid race conditions in refreshing the cache, synchronize access to the in-memory cache.
        await this.syncCacheSemaphore.WaitAsync();

        var latestVersion = await this.settings.GetVersionAsync();

        // If the versions are the same, nothing has changed in the configuration.
        if (this.currentVersion == latestVersion) return;

        // Get the latest settings from the settings store and publish changes.
        var latestSettings = await this.settings.FindAllAsync();

        // Refresh the settings cache.
        try
        {
            this.settingsCacheLock.EnterWriteLock();

            if (this.settingsCache != null)
            {
                //Notify settings changed
                latestSettings.Except(this.settingsCache).ToList().ForEach(kv => this.changed.OnNext(kv));
            }
            this.settingsCache = latestSettings;
        }
        finally
        {
            this.settingsCacheLock.ExitWriteLock();
        }

        // Update the current version.
        this.currentVersion = latestVersion;
    }
    catch (Exception ex)
    {
        this.changed.OnError(ex);
    }
    finally
    {
        this.syncCacheSemaphore.Release();
    }
  }
}
```

> `ExternalConfigurationManager` 類別也提供一個名為 `Environment` 的屬性。 這個屬性支援在不同環境 (例如預備和生產環境) 中所執行應用程式的各種設定。

`ExternalConfigurationManager` 物件也可定期查詢 `BlobSettingsStore` 物件是否有任何變更。 在下列程式碼中，`StartMonitor` 方法會定期呼叫 `CheckForConfigurationChanges` 以偵測變更，並引發 `Changed` 事件，如先前所述。

```csharp
public class ExternalConfigurationManager : IDisposable
{
  ...
  private readonly ISubject<KeyValuePair<string, string>> changed;
  private Dictionary<string, string> settingsCache;
  private readonly CancellationTokenSource cts = new CancellationTokenSource();
  private Task monitoringTask;
  private readonly TimeSpan interval;

  private readonly SemaphoreSlim timerSemaphore = new SemaphoreSlim(1);
  ...
  public ExternalConfigurationManager(string environment) : this(new BlobSettingsStore(environment), TimeSpan.FromSeconds(15), environment)
  {
  }
  
  public ExternalConfigurationManager(ISettingsStore settings, TimeSpan interval, string environment)
  {
      this.settings = settings;
      this.interval = interval;
      this.CheckForConfigurationChangesAsync().Wait();
      this.changed = new Subject<KeyValuePair<string, string>>();
      this.Environment = environment;
  }
  ...
  /// <summary>
  /// Check to see if the current instance is monitoring for changes
  /// </summary>
  public bool IsMonitoring => this.monitoringTask != null && !this.monitoringTask.IsCompleted;

  /// <summary>
  /// Start the background monitoring for configuration changes in the central store
  /// </summary>
  public void StartMonitor()
  {
      if (this.IsMonitoring)
          return;

      try
      {
          this.timerSemaphore.Wait();

          // Check again to make sure we are not already running.
          if (this.IsMonitoring)
              return;

          // Start running our task loop.
          this.monitoringTask = ConfigChangeMonitor();
      }
      finally
      {
          this.timerSemaphore.Release();
      }
  }

  /// <summary>
  /// Loop that monitors for configuration changes
  /// </summary>
  /// <returns></returns>
  public async Task ConfigChangeMonitor()
  {
      while (!cts.Token.IsCancellationRequested)
      {
          await this.CheckForConfigurationChangesAsync();
          await Task.Delay(this.interval, cts.Token);
      }
  }

  /// <summary>
  /// Stop monitoring for configuration changes
  /// </summary>
  public void StopMonitor()
  {
      try
      {
          this.timerSemaphore.Wait();

          // Signal the task to stop.
          this.cts.Cancel();

          // Wait for the loop to stop.
          this.monitoringTask.Wait();

          this.monitoringTask = null;
      }
      finally
      {
          this.timerSemaphore.Release();
      }
  }

  public void Dispose()
  {
      this.cts.Cancel();
  }
  ...
}
```

`ExternalConfigurationManager` 類別由如下所示的 `ExternalConfiguration` 類別具現化成單一執行個體。

```csharp
public static class ExternalConfiguration
{
    private static readonly Lazy<ExternalConfigurationManager> configuredInstance = new Lazy<ExternalConfigurationManager>(
        () =>
        {
            var environment = CloudConfigurationManager.GetSetting("environment");
            return new ExternalConfigurationManager(environment);
        });

    public static ExternalConfigurationManager Instance => configuredInstance.Value;
}
```

下列程式碼來自 _ExternalConfigurationStore.Cloud_ 專案中的 `WorkerRole` 類別。 它示範應用程式如何使用 `ExternalConfiguration` 類別來讀取設定。

```csharp
public override void Run()
{
  // Start monitoring configuration changes.
  ExternalConfiguration.Instance.StartMonitor();

  // Get a setting.
  var setting = ExternalConfiguration.Instance.GetAppSetting("setting1");
  Trace.TraceInformation("Worker Role: Get setting1, value: " + setting);

  this.completeEvent.WaitOne();
}
```

下列程式碼同樣來自 `WorkerRole` 類別，顯示應用程式如何訂閱設定事件。

```csharp
public override bool OnStart()
{
  ...
  // Subscribe to the event.
  ExternalConfiguration.Instance.Changed.Subscribe(
     m => Trace.TraceInformation("Configuration has changed. Key:{0} Value:{1}",
          m.Key, m.Value),
     ex => Trace.TraceError("Error detected: " + ex.Message));
  ...
}
```

## <a name="related-patterns-and-guidance"></a>相關的模式和指導方針

- [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/external-configuration-store) \(英文\) 上有提供示範此模式的範例。

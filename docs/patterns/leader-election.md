---
title: 選出領導者模式
titleSuffix: Cloud Design Patterns
description: 選取一個執行個體作為領導者，負責管理其他執行個體，協調分散式應用程式中共同作業工作執行個體集合執行的動作。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 1b74228fcceecb350d8df9b7ca2327b5de329a07
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58242969"
---
# <a name="leader-election-pattern"></a>選出領導者模式

[!INCLUDE [header](../_includes/header.md)]

選取一個執行個體作為領導者，負責管理其他執行個體，協調分散式應用程式中共同作業執行個體集合執行的動作。 這有助於確保執行個體彼此不會衝突、造成共用資源爭用，或不小心干擾其他執行個體正在執行的工作。

## <a name="context-and-problem"></a>內容和問題

一般的雲端應用程式有許多以協調方式進行的工作。 這些工作全都是執行相同程式碼和需要存取相同資源的執行個體，或一起並行工作來執行複雜計算的個別部分。

工作執行個體大部分時間都會個別執行，但也可能必須協調每個執行個體的動作，以確保不會衝突、造成共用資源爭用，或不小心干擾其他工作執行個體正在執行的工作。

例如︰

- 在實作水平規模調整的雲端式系統中，相同工作的多個執行個體，只要每個執行個體都為不同使用者提供服務，就可以同時執行。 如果這些執行個體會寫入共用資源，則必須協調其動作，避免每個執行個體覆寫彼此所做的變更。
- 如果工作以平行方式執行複雜計算的個別元素，則在全部完成時需要加以彙總。

工作執行個體全部都是同儕節點，因此沒有自然的領導者可以作為協調器或彙總工具。

## <a name="solution"></a>解決方法

應選擇單一工作執行個體作為領導者，並應由此執行個體協調其他從屬工作執行個體的動作。 如果所有工作執行個體都執行相同的程式碼，每一個都能作為領導者。 因此，必須仔細管理選擇程序，以防止同一時間有兩個以上的執行個體接任領導者角色。

系統必須提供用來選取領導者的健全機制。 這個方法必須處理如網路中斷或處理序失敗等事件。 在許多解決方案中，從屬工作執行個體會透過某種類型的活動訊號方法或透過輪詢來監視領導者。 如果指定的領導者意外終止，或網路失敗造成從屬工作執行個體無法使用領導者，它們就必須選出新的領導者。

在分散式環境中的一組工作之間選擇領導者的數種策略包括：

- 選取其執行個體排名或處理序識別碼最低的工作執行個體。
- 競爭以取得共用、分散式 Mutex。 最先取得 Mutex 的工作執行個體就是領導者。 然而，系統必須確保如果領導者終止或和系統的其餘部分中斷連線，就會釋出 Mutex，以讓另一個工作執行個體成為領導者。
- 實作其中一種常見的領導者選擇演算法，例如[強勢演算法](https://www.cs.colostate.edu/~cs551/CourseNotes/Synchronization/BullyExample.html) \(英文\) 或 [通道演算法](https://www.cs.colostate.edu/~cs551/CourseNotes/Synchronization/RingElectExample.html) \(英文\)。 這些演算法假設選擇中的每個候選項目都有唯一的識別碼，並能與其他候選項目可靠地進行通訊。

## <a name="issues-and-considerations"></a>問題和考量

當您決定如何實作此模式時，請考慮下列幾點：

- 選擇領導者的程序應該要能從暫時性和持續失敗復原。
- 務必要能偵測到領導者失敗或變得無法使用 (例如，由於通訊失敗) 的情況。 多快能偵測到則取決於系統。 在沒有領導者的情況下，有些系統可能還可以運作一小段時間，暫時性錯誤可能會在這段期間內修正。 在其他情況下，可能必須立即偵測到領導者失敗並觸發新的選擇程序。
- 在實作水平自動調整規模的系統中，如果系統調低和關閉一些計算資源，可能會終止領導者。
- 使用共用的分散式 Mutex 會導入與提供 Mutex 之外部服務的相依性。 該服務會構成單一失敗點。 如果因故而無法使用，系統將無法選擇領導者。
- 使用單一的專用處理序作為領導者是最直接的方法。 不過，如果此程序失敗，重新啟動時可能會有明顯的延遲。 產生的延遲情況可能會影響其他正在等候領導者協調作業之處理序的效能和回應時間。
- 實作其中一種選擇領導者的演算法，可為調整和最佳化程式碼手動提供最大的彈性空間。

## <a name="when-to-use-this-pattern"></a>使用此模式的時機

當雲端裝載解決方案等分散式應用程式中的工作需要謹慎協調，而且沒有自然領導者時，可以使用此模式。

> 請避免讓領導者成為系統中的瓶頸。 領導者的目的在協調從屬工作的工作，本身不一定要參與此工作&mdash;雖然未獲選為領導者時應要能這麼做。

此模式可能不適合用於下列時機︰

- 沒有總是可作為領導者的自然領導者或專用的處理序。 例如，可實作協調工作執行個體的單一處理序。 如果此處理序失敗或變得狀況不良，系統就會將它關閉並重新啟動。
- 工作之間的協調可以使用更精簡的方法來達成。 例如，如果多個工作執行個體只需要協調對共用資源的存取，使用開放式或封閉式鎖定來控制存取是比較好的解決方案。
- 協力廠商解決方案更適合。 例如，Microsoft Azure HDInsight 服務 (以 Apache Hadoop 為基礎) 使用 Apache Zookeeper 提供的服務來協調對應，並減少收集和建立資料摘要的工作。

## <a name="example"></a>範例

LeaderElection 方案中的 DistributedMutex 專案 (示範此模式的範例可於 [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/leader-election) 取得) 示範如何使用 Azure 儲存體 Blob 上的租用，提供用於實作共用、分散式 Mutex 的機制。 此 Mutex 可以用來在 Azure 雲端服務中的一組角色執行個體之間選擇領導者。 最先取得租用的角色執行個體會獲選為領導者，而且在其釋出租用或無法更新租用之前，都會是領導者。 其他角色執行個體可以繼續監視 Blob 租用，以防該領導者無法再使用。

> Blob 租用是對 Blob 的獨佔寫入鎖定。 不論何時，單一 Blob 都是唯一租用的主體。 角色執行個體可向指定的 Blob 要求租用，並在沒有其他角色執行個體握有同一個 Blob 的租用時獲得租用。 否則該要求將會擲回例外狀況。
>
> 為了避免發生錯誤的角色執行個體無限期握有租用，須指定租用的存留期。 到期時，租用就會變成可用。 不過，當角色執行個體握有租用，它可以要求更新租用，延長獲得租用的時間。 如果角色執行個體想要一直握有租用，可以不斷重複此程序。
> 如需如何租用 Blob 的詳細資訊，請參閱[租用 Blob (REST API)](https://msdn.microsoft.com/library/azure/ee691972.aspx) \(英文\)。

下面 C# 範例中的 `BlobDistributedMutex` 類別包含的 `RunTaskWhenMutexAquired` 方法可讓角色執行個體嘗試向指定的 Blob 取得租用。 Blob (名稱、容器及儲存體帳戶) 的詳細資料會在建立 `BlobDistributedMutex` 物件 (此物件是範例程式碼中包含的簡單結構) 時，傳遞到 `BlobSettings` 物件中的建構函式。 建構函式也接受參考角色執行個體應執行程式碼的 `Task`，前提是它成功向 Blob 取得租用並獲選為領導者。 請注意，可處理取得租用之低階詳細資料的程式碼，是在名為 `BlobLeaseManager` 個別協助程式類別中實作。

```csharp
public class BlobDistributedMutex
{
  ...
  private readonly BlobSettings blobSettings;
  private readonly Func<CancellationToken, Task> taskToRunWhenLeaseAcquired;
  ...

  public BlobDistributedMutex(BlobSettings blobSettings,
           Func<CancellationToken, Task> taskToRunWhenLeaseAquired)
  {
    this.blobSettings = blobSettings;
    this.taskToRunWhenLeaseAquired = taskToRunWhenLeaseAquired;
  }

  public async Task RunTaskWhenMutexAcquired(CancellationToken token)
  {
    var leaseManager = new BlobLeaseManager(blobSettings);
    await this.RunTaskWhenBlobLeaseAcquired(leaseManager, token);
  }
  ...
```

上述程式碼範例中的 `RunTaskWhenMutexAquired` 方法會叫用下列程式碼範例中所示的 `RunTaskWhenBlobLeaseAcquired` 方法，以實際取得租用。 `RunTaskWhenBlobLeaseAcquired` 方法會以非同步方式執行。 如果成功取得租用，該角色執行個體就會獲選為領導者。 `taskToRunWhenLeaseAcquired` 委派的目的是執行用以協調其他角色執行個體的工作。 如果未取得租用，就會選擇另一個角色執行個體作為領導者，而目前的角色執行個體仍是從屬執行個體。 請注意，`TryAcquireLeaseOrWait` 方法是協助程式方法，它會使用 `BlobLeaseManager` 物件來取得租用。

```csharp
  private async Task RunTaskWhenBlobLeaseAcquired(
    BlobLeaseManager leaseManager, CancellationToken token)
  {
    while (!token.IsCancellationRequested)
    {
      // Try to acquire the blob lease.
      // Otherwise wait for a short time before trying again.
      string leaseId = await this.TryAquireLeaseOrWait(leaseManager, token);

      if (!string.IsNullOrEmpty(leaseId))
      {
        // Create a new linked cancellation token source so that if either the
        // original token is canceled or the lease can't be renewed, the
        // leader task can be canceled.
        using (var leaseCts =
          CancellationTokenSource.CreateLinkedTokenSource(new[] { token }))
        {
          // Run the leader task.
          var leaderTask = this.taskToRunWhenLeaseAquired.Invoke(leaseCts.Token);
          ...
        }
      }
    }
    ...
  }
```

由領導者啟動的工作也會以非同步方式執行。 此工作執行時，下列程式碼範例所示的 `RunTaskWhenBlobLeaseAquired` 方法會定期嘗試更新租用。 這有助於確保該角色執行個體維持領導者的角色。 在範例解決方案中，更新要求之間的延遲會短於租用期間指定的時間，以防止另一個角色執行個體獲選為領導者。 如果更新因任何原因而失敗，則會取消工作。

如果租用更新失敗或工作被取消 (可能是因為角色執行個體關閉所致)，就會釋出租用。 此時，這一個或另一個角色執行個體可能會獲選為領導者。 擷取如下的程式碼示範此程序的一部分。

```csharp
  private async Task RunTaskWhenBlobLeaseAcquired(
    BlobLeaseManager leaseManager, CancellationToken token)
  {
    while (...)
    {
      ...
      if (...)
      {
        ...
        using (var leaseCts = ...)
        {
          ...
          // Keep renewing the lease in regular intervals.
          // If the lease can't be renewed, then the task completes.
          var renewLeaseTask =
            this.KeepRenewingLease(leaseManager, leaseId, leaseCts.Token);

          // When any task completes (either the leader task itself or when it
          // couldn't renew the lease) then cancel the other task.
          await CancelAllWhenAnyCompletes(leaderTask, renewLeaseTask, leaseCts);
        }
      }
    }
  }
  ...
}
```

`KeepRenewingLease` 方法是另一個協助程式方法，它會使用 `BlobLeaseManager` 物件來更新租用。 `CancelAllWhenAnyCompletes` 方法會取消前兩個參數所指定的工作。 下圖說明使用 `BlobDistributedMutex` 類別來選擇領導者，並執行協調作業的工作。

![圖 1 說明 BlobDistributedMutex 類別的函式](./_images/leader-election-diagram.png)

下列程式碼範例示範如何使用背景工作角色中的 `BlobDistributedMutex` 類別。 此程式碼會向開發儲存體中租用容器內名為 `MyLeaderCoordinatorTask` 的 Blob 取得租用，並指出如果該角色執行個體獲選為領導者，則應執行 `MyLeaderCoordinatorTask` 方法中定義的程式碼。

```csharp
var settings = new BlobSettings(CloudStorageAccount.DevelopmentStorageAccount,
  "leases", "MyLeaderCoordinatorTask");
var cts = new CancellationTokenSource();
var mutex = new BlobDistributedMutex(settings, MyLeaderCoordinatorTask);
mutex.RunTaskWhenMutexAcquired(this.cts.Token);
...

// Method that runs if the role instance is elected the leader
private static async Task MyLeaderCoordinatorTask(CancellationToken token)
{
  ...
}
```

請注意下列有關範例解決方案的重點：

- Blob 是潛在的單一失敗點。 如果 Blob 服務變得無法使用或無法存取，領導者將無法更新租用，而且其他角色執行個體都將無法取得租用。 在此情況下，任何角色執行個體都無法作為領導者。 不過，Blob 服務的設計很有彈性，因此 Blob 服務幾乎不可能出現完全失敗的情況。
- 如果領導者執行的工作停止，領導者可能會不停更新租用，避免任何其他角色執行個體取得租用並接任領導者角色來協調工作。 在真實世界中，應頻繁檢查領導者的健康情況。
- 選擇程序並不具決定性。 您不得假設哪個角色執行個體會取得 Blob 租用並成為領導者。
- 當成 Blob 租用目標的 Blob 不應用於任何其他用途。 如果角色執行個體嘗試儲存此 Blob 中的資料，除非該角色執行個體是領導者且握有 Blob 租用，否則便無法存取此資料。

## <a name="related-patterns-and-guidance"></a>相關的模式和指導方針

實作此模式時，下列指導方針可能也相關：

- 此模式有可下載的[範例應用程式](https://github.com/mspnp/cloud-design-patterns/tree/master/leader-election)。
- [自動調整指導方針](https://msdn.microsoft.com/library/dn589774.aspx)。 工作主機的執行個體可以隨著應用程式的負載來啟動和停止。 自動調整規模有助於維護尖峰處理時間的輸送量和效能。
- [計算分割指導方針](https://msdn.microsoft.com/library/dn589773.aspx) \(英文\)。 本指導方針說明如何以有助於將執行成本降至最低，同時維持服務延展性、效能、可用性及安全性的方式，將工作配置到雲端服務中的主機。
- [工作式非同步模式](https://msdn.microsoft.com/library/hh873175.aspx)。
- 說明[強勢演算法](https://www.cs.colostate.edu/~cs551/CourseNotes/Synchronization/BullyExample.html)的範例。
- 說明[通道演算法](https://www.cs.colostate.edu/~cs551/CourseNotes/Synchronization/RingElectExample.html)的範例。
- [Apache Curator](https://curator.apache.org/) 是 Apache ZooKeeper 的用戶端程式庫。
- MSDN 上的[租用 Blob (REST API)](https://msdn.microsoft.com/library/azure/ee691972.aspx) \(英文\) 一文。

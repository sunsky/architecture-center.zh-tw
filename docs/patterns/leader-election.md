---
title: 選出領導者
description: 選取一個執行個體作為領導者，負責管理其他執行個體，協調分散式應用程式中共同作業工作執行個體集合執行的動作。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories:
- design-implementation
- resiliency
ms.openlocfilehash: 3e7d47f70f660f2507f0619e1c41bf9a32a25be4
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="leader-election-pattern"></a><span data-ttu-id="2700e-104">選出領導者模式</span><span class="sxs-lookup"><span data-stu-id="2700e-104">Leader Election pattern</span></span>

[!INCLUDE [header](../_includes/header.md)]

<span data-ttu-id="2700e-105">選取一個執行個體作為領導者，負責管理其他執行個體，協調分散式應用程式中共同作業執行個體集合執行的動作。</span><span class="sxs-lookup"><span data-stu-id="2700e-105">Coordinate the actions performed by a collection of collaborating instances in a distributed application by electing one instance as the leader that assumes responsibility for managing the others.</span></span> <span data-ttu-id="2700e-106">這有助於確保執行個體彼此不會衝突、造成共用資源爭用，或不小心干擾其他執行個體正在執行的工作。</span><span class="sxs-lookup"><span data-stu-id="2700e-106">This can help to ensure that instances don't conflict with each other, cause contention for shared resources, or inadvertently interfere with the work that other instances are performing.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="2700e-107">內容和問題</span><span class="sxs-lookup"><span data-stu-id="2700e-107">Context and problem</span></span>

<span data-ttu-id="2700e-108">一般的雲端應用程式有許多以協調方式進行的工作。</span><span class="sxs-lookup"><span data-stu-id="2700e-108">A typical cloud application has many tasks acting in a coordinated manner.</span></span> <span data-ttu-id="2700e-109">這些工作全都是執行相同程式碼和需要存取相同資源的執行個體，或一起並行工作來執行複雜計算的個別部分。</span><span class="sxs-lookup"><span data-stu-id="2700e-109">These tasks could all be instances running the same code and requiring access to the same resources, or they might be working together in parallel to perform the individual parts of a complex calculation.</span></span>

<span data-ttu-id="2700e-110">工作執行個體大部分時間都會個別執行，但也可能必須協調每個執行個體的動作，以確保不會衝突、造成共用資源爭用，或不小心干擾其他工作執行個體正在執行的工作。</span><span class="sxs-lookup"><span data-stu-id="2700e-110">The task instances might run separately for much of the time, but it might also be necessary to coordinate the actions of each instance to ensure that they don’t conflict, cause contention for shared resources, or accidentally interfere with the work that other task instances are performing.</span></span>

<span data-ttu-id="2700e-111">例如︰</span><span class="sxs-lookup"><span data-stu-id="2700e-111">For example:</span></span>

- <span data-ttu-id="2700e-112">在實作水平規模調整的雲端式系統中，相同工作的多個執行個體，只要每個執行個體都為不同使用者提供服務，就可以同時執行。</span><span class="sxs-lookup"><span data-stu-id="2700e-112">In a cloud-based system that implements horizontal scaling, multiple instances of the same task could be running at the same time with each instance serving a different user.</span></span> <span data-ttu-id="2700e-113">如果這些執行個體會寫入共用資源，則必須協調其動作，避免每個執行個體覆寫彼此所做的變更。</span><span class="sxs-lookup"><span data-stu-id="2700e-113">If these instances write to a shared resource, it's necessary to coordinate their actions to prevent each instance from overwriting the changes made by the others.</span></span>
- <span data-ttu-id="2700e-114">如果工作以平行方式執行複雜計算的個別元素，則在全部完成時需要加以彙總。</span><span class="sxs-lookup"><span data-stu-id="2700e-114">If the tasks are performing individual elements of a complex calculation in parallel, the results need to be aggregated when they all complete.</span></span>

<span data-ttu-id="2700e-115">工作執行個體全部都是同儕節點，因此沒有自然的領導者可以作為協調器或彙總工具。</span><span class="sxs-lookup"><span data-stu-id="2700e-115">The task instances are all peers, so there isn't a natural leader that can act as the coordinator or aggregator.</span></span>

## <a name="solution"></a><span data-ttu-id="2700e-116">解決方法</span><span class="sxs-lookup"><span data-stu-id="2700e-116">Solution</span></span>

<span data-ttu-id="2700e-117">應選擇單一工作執行個體作為領導者，並應由此執行個體協調其他從屬工作執行個體的動作。</span><span class="sxs-lookup"><span data-stu-id="2700e-117">A single task instance should be elected to act as the leader, and this instance should coordinate the actions of the other subordinate task instances.</span></span> <span data-ttu-id="2700e-118">如果所有工作執行個體都執行相同的程式碼，每一個都能作為領導者。</span><span class="sxs-lookup"><span data-stu-id="2700e-118">If all of the task instances are running the same code, they are each capable of acting as the leader.</span></span> <span data-ttu-id="2700e-119">因此，必須仔細管理選擇程序，以防止同一時間有兩個以上的執行個體接任領導者角色。</span><span class="sxs-lookup"><span data-stu-id="2700e-119">Therefore, the election process must be managed carefully to prevent two or more instances taking over the leader role at the same time.</span></span>

<span data-ttu-id="2700e-120">系統必須提供用來選取領導者的健全機制。</span><span class="sxs-lookup"><span data-stu-id="2700e-120">The system must provide a robust mechanism for selecting the leader.</span></span> <span data-ttu-id="2700e-121">這個方法必須處理如網路中斷或處理序失敗等事件。</span><span class="sxs-lookup"><span data-stu-id="2700e-121">This method has to cope with events such as network outages or process failures.</span></span> <span data-ttu-id="2700e-122">在許多解決方案中，從屬工作執行個體會透過某種類型的活動訊號方法或透過輪詢來監視領導者。</span><span class="sxs-lookup"><span data-stu-id="2700e-122">In many solutions, the subordinate task instances monitor the leader through some type of heartbeat method, or by polling.</span></span> <span data-ttu-id="2700e-123">如果指定的領導者意外終止，或網路失敗造成從屬工作執行個體無法使用領導者，它們就必須選出新的領導者。</span><span class="sxs-lookup"><span data-stu-id="2700e-123">If the designated leader terminates unexpectedly, or a network failure makes the leader unavailable to the subordinate task instances, it's necessary for them to elect a new leader.</span></span>

<span data-ttu-id="2700e-124">在分散式環境中的一組工作之間選擇領導者的數種策略包括：</span><span class="sxs-lookup"><span data-stu-id="2700e-124">There are several strategies for electing a leader among a set of tasks in a distributed environment, including:</span></span>
- <span data-ttu-id="2700e-125">選取其執行個體排名或處理序識別碼最低的工作執行個體。</span><span class="sxs-lookup"><span data-stu-id="2700e-125">Selecting the task instance with the lowest-ranked instance or process ID.</span></span>
- <span data-ttu-id="2700e-126">競爭以取得共用、分散式 Mutex。</span><span class="sxs-lookup"><span data-stu-id="2700e-126">Racing to acquire a shared, distributed mutex.</span></span> <span data-ttu-id="2700e-127">最先取得 Mutex 的工作執行個體就是領導者。</span><span class="sxs-lookup"><span data-stu-id="2700e-127">The first task instance that acquires the mutex is the leader.</span></span> <span data-ttu-id="2700e-128">然而，系統必須確保如果領導者終止或和系統的其餘部分中斷連線，就會釋出 Mutex，以讓另一個工作執行個體成為領導者。</span><span class="sxs-lookup"><span data-stu-id="2700e-128">However, the system must ensure that, if the leader terminates or becomes disconnected from the rest of the system, the mutex is released to allow another task instance to become the leader.</span></span>
- <span data-ttu-id="2700e-129">實作其中一種常見的領導者選擇演算法，例如[強勢演算法](http://www.cs.colostate.edu/~cs551/CourseNotes/Synchronization/BullyExample.html) \(英文\) 或 [通道演算法](http://www.cs.colostate.edu/~cs551/CourseNotes/Synchronization/RingElectExample.html) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="2700e-129">Implementing one of the common leader election algorithms such as the [Bully Algorithm](http://www.cs.colostate.edu/~cs551/CourseNotes/Synchronization/BullyExample.html) or the [Ring Algorithm](http://www.cs.colostate.edu/~cs551/CourseNotes/Synchronization/RingElectExample.html).</span></span> <span data-ttu-id="2700e-130">這些演算法假設選擇中的每個候選項目都有唯一的識別碼，並能與其他候選項目可靠地進行通訊。</span><span class="sxs-lookup"><span data-stu-id="2700e-130">These algorithms assume that each candidate in the election has a unique ID, and that it can communicate with the other candidates reliably.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="2700e-131">問題和考量</span><span class="sxs-lookup"><span data-stu-id="2700e-131">Issues and considerations</span></span>

<span data-ttu-id="2700e-132">當您決定如何實作此模式時，請考慮下列幾點：</span><span class="sxs-lookup"><span data-stu-id="2700e-132">Consider the following points when deciding how to implement this pattern:</span></span>
- <span data-ttu-id="2700e-133">選擇領導者的程序應該要能從暫時性和持續失敗復原。</span><span class="sxs-lookup"><span data-stu-id="2700e-133">The process of electing a leader should be resilient to transient and persistent failures.</span></span>
- <span data-ttu-id="2700e-134">務必要能偵測到領導者失敗或變得無法使用 (例如，由於通訊失敗) 的情況。</span><span class="sxs-lookup"><span data-stu-id="2700e-134">It must be possible to detect when the leader has failed or has become otherwise unavailable (such as due to a communications failure).</span></span> <span data-ttu-id="2700e-135">多快能偵測到則取決於系統。</span><span class="sxs-lookup"><span data-stu-id="2700e-135">How quickly detection is needed is system dependent.</span></span> <span data-ttu-id="2700e-136">在沒有領導者的情況下，有些系統可能還可以運作一小段時間，暫時性錯誤可能會在這段期間內修正。</span><span class="sxs-lookup"><span data-stu-id="2700e-136">Some systems might be able to function for a short time without a leader, during which a transient fault might be fixed.</span></span> <span data-ttu-id="2700e-137">在其他情況下，可能必須立即偵測到領導者失敗並觸發新的選擇程序。</span><span class="sxs-lookup"><span data-stu-id="2700e-137">In other cases, it might be necessary to detect leader failure immediately and trigger a new election.</span></span>
- <span data-ttu-id="2700e-138">在實作水平自動調整規模的系統中，如果系統調低和關閉一些計算資源，可能會終止領導者。</span><span class="sxs-lookup"><span data-stu-id="2700e-138">In a system that implements horizontal autoscaling, the leader could be terminated if the system scales back and shuts down some of the computing resources.</span></span>
- <span data-ttu-id="2700e-139">使用共用的分散式 Mutex 會導入與提供 Mutex 之外部服務的相依性。</span><span class="sxs-lookup"><span data-stu-id="2700e-139">Using a shared, distributed mutex introduces a dependency on the external service that provides the mutex.</span></span> <span data-ttu-id="2700e-140">該服務會構成單一失敗點。</span><span class="sxs-lookup"><span data-stu-id="2700e-140">The service constitutes a single point of failure.</span></span> <span data-ttu-id="2700e-141">如果因故而無法使用，系統將無法選擇領導者。</span><span class="sxs-lookup"><span data-stu-id="2700e-141">If it becomes unavailable for any reason, the system won't be able to elect a leader.</span></span>
- <span data-ttu-id="2700e-142">使用單一的專用處理序作為領導者是最直接的方法。</span><span class="sxs-lookup"><span data-stu-id="2700e-142">Using a single dedicated process as the leader is a straightforward approach.</span></span> <span data-ttu-id="2700e-143">不過，如果此程序失敗，重新啟動時可能會有明顯的延遲。</span><span class="sxs-lookup"><span data-stu-id="2700e-143">However, if the process fails there could be a significant delay while it's restarted.</span></span> <span data-ttu-id="2700e-144">產生的延遲情況可能會影響其他正在等候領導者協調作業之處理序的效能和回應時間。</span><span class="sxs-lookup"><span data-stu-id="2700e-144">The resulting latency can affect the performance and response times of other processes if they're waiting for the leader to coordinate an operation.</span></span>
- <span data-ttu-id="2700e-145">實作其中一種選擇領導者的演算法，可為調整和最佳化程式碼手動提供最大的彈性空間。</span><span class="sxs-lookup"><span data-stu-id="2700e-145">Implementing one of the leader election algorithms manually provides the greatest flexibility for tuning and optimizing the code.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="2700e-146">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="2700e-146">When to use this pattern</span></span>

<span data-ttu-id="2700e-147">當雲端裝載解決方案等分散式應用程式中的工作需要謹慎協調，而且沒有自然領導者時，可以使用此模式。</span><span class="sxs-lookup"><span data-stu-id="2700e-147">Use this pattern when the tasks in a distributed application, such as a cloud-hosted solution, need careful coordination and there's no natural leader.</span></span>

>  <span data-ttu-id="2700e-148">請避免讓領導者成為系統中的瓶頸。</span><span class="sxs-lookup"><span data-stu-id="2700e-148">Avoid making the leader a bottleneck in the system.</span></span> <span data-ttu-id="2700e-149">領導者的目的在協調從屬工作的工作，本身不一定要參與此工作&mdash;雖然未獲選為領導者時應要能這麼做。</span><span class="sxs-lookup"><span data-stu-id="2700e-149">The purpose of the leader is to coordinate the work of the subordinate tasks, and it doesn't necessarily have to participate in this work itself&mdash;although it should be able to do so if the task isn't elected as the leader.</span></span>

<span data-ttu-id="2700e-150">此模式可能不適合用於下列時機︰</span><span class="sxs-lookup"><span data-stu-id="2700e-150">This pattern might not be useful if:</span></span>
- <span data-ttu-id="2700e-151">沒有總是可作為領導者的自然領導者或專用的處理序。</span><span class="sxs-lookup"><span data-stu-id="2700e-151">There's a natural leader or dedicated process that can always act as the leader.</span></span> <span data-ttu-id="2700e-152">例如，可實作協調工作執行個體的單一處理序。</span><span class="sxs-lookup"><span data-stu-id="2700e-152">For example, it might be possible to implement a singleton process that coordinates the task instances.</span></span> <span data-ttu-id="2700e-153">如果此處理序失敗或變得狀況不良，系統就會將它關閉並重新啟動。</span><span class="sxs-lookup"><span data-stu-id="2700e-153">If this process fails or becomes unhealthy, the system can shut it down and restart it.</span></span>
- <span data-ttu-id="2700e-154">工作之間的協調可以使用更精簡的方法來達成。</span><span class="sxs-lookup"><span data-stu-id="2700e-154">The coordination between tasks can be achieved using a more lightweight method.</span></span> <span data-ttu-id="2700e-155">例如，如果多個工作執行個體只需要協調對共用資源的存取，使用開放式或封閉式鎖定來控制存取是比較好的解決方案。</span><span class="sxs-lookup"><span data-stu-id="2700e-155">For example, if several task instances simply need coordinated access to a shared resource, a better solution is to use optimistic or pessimistic locking to control access.</span></span>
- <span data-ttu-id="2700e-156">協力廠商解決方案更適合。</span><span class="sxs-lookup"><span data-stu-id="2700e-156">A third-party solution is more appropriate.</span></span> <span data-ttu-id="2700e-157">例如，Microsoft Azure HDInsight 服務 (以 Apache Hadoop 為基礎) 使用 Apache Zookeeper 提供的服務來協調對應，並減少收集和建立資料摘要的工作。</span><span class="sxs-lookup"><span data-stu-id="2700e-157">For example, the Microsoft Azure HDInsight service (based on Apache Hadoop) uses the services provided by Apache Zookeeper to coordinate the map and reduce tasks that collect and summarize data.</span></span>

## <a name="example"></a><span data-ttu-id="2700e-158">範例</span><span class="sxs-lookup"><span data-stu-id="2700e-158">Example</span></span>

<span data-ttu-id="2700e-159">LeaderElection 方案中的 DistributedMutex 專案 (示範此模式的範例可於 [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/leader-election) 取得) 示範如何使用 Azure 儲存體 Blob 上的租用，提供用於實作共用、分散式 Mutex 的機制。</span><span class="sxs-lookup"><span data-stu-id="2700e-159">The DistributedMutex project in the LeaderElection solution (a sample that demonstrates this pattern is available on [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/leader-election)) shows how to use a lease on an Azure Storage blob to provide a mechanism for implementing a shared, distributed mutex.</span></span> <span data-ttu-id="2700e-160">此 Mutex 可以用來在 Azure 雲端服務中的一組角色執行個體之間選擇領導者。</span><span class="sxs-lookup"><span data-stu-id="2700e-160">This mutex can be used to elect a leader among a group of role instances in an Azure cloud service.</span></span> <span data-ttu-id="2700e-161">最先取得租用的角色執行個體會獲選為領導者，而且在其釋出租用或無法更新租用之前，都會是領導者。</span><span class="sxs-lookup"><span data-stu-id="2700e-161">The first role instance to acquire the lease is elected the leader, and remains the leader until it releases the lease or isn't able to renew the lease.</span></span> <span data-ttu-id="2700e-162">其他角色執行個體可以繼續監視 Blob 租用，以防該領導者無法再使用。</span><span class="sxs-lookup"><span data-stu-id="2700e-162">Other role instances can continue to monitor the blob lease in case the leader is no longer available.</span></span>

>  <span data-ttu-id="2700e-163">Blob 租用是對 Blob 的獨佔寫入鎖定。</span><span class="sxs-lookup"><span data-stu-id="2700e-163">A blob lease is an exclusive write lock over a blob.</span></span> <span data-ttu-id="2700e-164">不論何時，單一 Blob 都是唯一租用的主體。</span><span class="sxs-lookup"><span data-stu-id="2700e-164">A single blob can be the subject of only one lease at any point in time.</span></span> <span data-ttu-id="2700e-165">角色執行個體可向指定的 Blob 要求租用，並在沒有其他角色執行個體握有同一個 Blob 的租用時獲得租用。</span><span class="sxs-lookup"><span data-stu-id="2700e-165">A role instance can request a lease over a specified blob, and it'll be granted the lease if no other role instance holds a lease over the same blob.</span></span> <span data-ttu-id="2700e-166">否則該要求將會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="2700e-166">Otherwise the request will throw an exception.</span></span>
> 
> <span data-ttu-id="2700e-167">為了避免發生錯誤的角色執行個體無限期握有租用，須指定租用的存留期。</span><span class="sxs-lookup"><span data-stu-id="2700e-167">To avoid a faulted role instance retaining the lease indefinitely, specify a lifetime for the lease.</span></span> <span data-ttu-id="2700e-168">到期時，租用就會變成可用。</span><span class="sxs-lookup"><span data-stu-id="2700e-168">When this expires, the lease becomes available.</span></span> <span data-ttu-id="2700e-169">不過，當角色執行個體握有租用，它可以要求更新租用，延長獲得租用的時間。</span><span class="sxs-lookup"><span data-stu-id="2700e-169">However, while a role instance holds the lease it can request that the lease is renewed, and it'll be granted the lease for a further period of time.</span></span> <span data-ttu-id="2700e-170">如果角色執行個體想要一直握有租用，可以不斷重複此程序。</span><span class="sxs-lookup"><span data-stu-id="2700e-170">The role instance can continually repeat this process if it wants to retain the lease.</span></span>
> <span data-ttu-id="2700e-171">如需如何租用 Blob 的詳細資訊，請參閱[租用 Blob (REST API)](https://msdn.microsoft.com/library/azure/ee691972.aspx) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="2700e-171">For more information on how to lease a blob, see [Lease Blob (REST API)](https://msdn.microsoft.com/library/azure/ee691972.aspx).</span></span>

<span data-ttu-id="2700e-172">下面 C# 範例中的 `BlobDistributedMutex` 類別包含的 `RunTaskWhenMutexAquired` 方法可讓角色執行個體嘗試向指定的 Blob 取得租用。</span><span class="sxs-lookup"><span data-stu-id="2700e-172">The `BlobDistributedMutex` class in the C# example below contains the `RunTaskWhenMutexAquired` method that enables a role instance to attempt to acquire a lease over a specified blob.</span></span> <span data-ttu-id="2700e-173">Blob (名稱、容器及儲存體帳戶) 的詳細資料會在建立 `BlobDistributedMutex` 物件 (此物件是範例程式碼中包含的簡單結構) 時，傳遞到 `BlobSettings` 物件中的建構函式。</span><span class="sxs-lookup"><span data-stu-id="2700e-173">The details of the blob (the name, container, and storage account) are passed to the constructor in a `BlobSettings` object when the `BlobDistributedMutex` object is created (this object is a simple struct that is included in the sample code).</span></span> <span data-ttu-id="2700e-174">建構函式也接受參考角色執行個體應執行程式碼的 `Task`，前提是它成功向 Blob 取得租用並獲選為領導者。</span><span class="sxs-lookup"><span data-stu-id="2700e-174">The constructor also accepts a `Task` that references the code that the role instance should run if it successfully acquires the lease over the blob and is elected the leader.</span></span> <span data-ttu-id="2700e-175">請注意，可處理取得租用之低階詳細資料的程式碼，是在名為 `BlobLeaseManager` 個別協助程式類別中實作。</span><span class="sxs-lookup"><span data-stu-id="2700e-175">Note that the code that handles the low-level details of acquiring the lease is implemented in a separate helper class named `BlobLeaseManager`.</span></span>

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

<span data-ttu-id="2700e-176">上述程式碼範例中的 `RunTaskWhenMutexAquired` 方法會叫用下列程式碼範例中所示的 `RunTaskWhenBlobLeaseAcquired` 方法，以實際取得租用。</span><span class="sxs-lookup"><span data-stu-id="2700e-176">The `RunTaskWhenMutexAquired` method in the code sample above invokes the `RunTaskWhenBlobLeaseAcquired` method shown in the following code sample to actually acquire the lease.</span></span> <span data-ttu-id="2700e-177">`RunTaskWhenBlobLeaseAcquired` 方法會以非同步方式執行。</span><span class="sxs-lookup"><span data-stu-id="2700e-177">The `RunTaskWhenBlobLeaseAcquired` method runs asynchronously.</span></span> <span data-ttu-id="2700e-178">如果成功取得租用，該角色執行個體就會獲選為領導者。</span><span class="sxs-lookup"><span data-stu-id="2700e-178">If the lease is successfully acquired, the role instance has been elected the leader.</span></span> <span data-ttu-id="2700e-179">`taskToRunWhenLeaseAcquired` 委派的目的是執行用以協調其他角色執行個體的工作。</span><span class="sxs-lookup"><span data-stu-id="2700e-179">The purpose of the `taskToRunWhenLeaseAcquired` delegate is to perform the work that coordinates the other role instances.</span></span> <span data-ttu-id="2700e-180">如果未取得租用，就會選擇另一個角色執行個體作為領導者，而目前的角色執行個體仍是從屬執行個體。</span><span class="sxs-lookup"><span data-stu-id="2700e-180">If the lease isn't acquired, another role instance has been elected as the leader and the current role instance remains a subordinate.</span></span> <span data-ttu-id="2700e-181">請注意，`TryAcquireLeaseOrWait` 方法是協助程式方法，它會使用 `BlobLeaseManager` 物件來取得租用。</span><span class="sxs-lookup"><span data-stu-id="2700e-181">Note that the `TryAcquireLeaseOrWait` method is a helper method that uses the `BlobLeaseManager` object to acquire the lease.</span></span>

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

<span data-ttu-id="2700e-182">由領導者啟動的工作也會以非同步方式執行。</span><span class="sxs-lookup"><span data-stu-id="2700e-182">The task started by the leader also runs asynchronously.</span></span> <span data-ttu-id="2700e-183">此工作執行時，下列程式碼範例所示的 `RunTaskWhenBlobLeaseAquired` 方法會定期嘗試更新租用。</span><span class="sxs-lookup"><span data-stu-id="2700e-183">While this task is running, the `RunTaskWhenBlobLeaseAquired` method shown in the following code sample periodically attempts to renew the lease.</span></span> <span data-ttu-id="2700e-184">這有助於確保該角色執行個體維持領導者的角色。</span><span class="sxs-lookup"><span data-stu-id="2700e-184">This helps to ensure that the role instance remains the leader.</span></span> <span data-ttu-id="2700e-185">在範例解決方案中，更新要求之間的延遲會短於租用期間指定的時間，以防止另一個角色執行個體獲選為領導者。</span><span class="sxs-lookup"><span data-stu-id="2700e-185">In the sample solution, the delay between renewal requests is less than the time specified for the duration of the lease in order to prevent another role instance from being elected the leader.</span></span> <span data-ttu-id="2700e-186">如果更新因任何原因而失敗，則會取消工作。</span><span class="sxs-lookup"><span data-stu-id="2700e-186">If the renewal fails for any reason, the task is canceled.</span></span>

<span data-ttu-id="2700e-187">如果租用更新失敗或工作被取消 (可能是因為角色執行個體關閉所致)，就會釋出租用。</span><span class="sxs-lookup"><span data-stu-id="2700e-187">If the lease fails to be renewed or the task is canceled (possibly as a result of the role instance shutting down), the lease is released.</span></span> <span data-ttu-id="2700e-188">此時，這一個或另一個角色執行個體可能會獲選為領導者。</span><span class="sxs-lookup"><span data-stu-id="2700e-188">At this point, this or another role instance might be elected as the leader.</span></span> <span data-ttu-id="2700e-189">擷取如下的程式碼示範此程序的一部分。</span><span class="sxs-lookup"><span data-stu-id="2700e-189">The code extract below shows this part of the process.</span></span>

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

<span data-ttu-id="2700e-190">`KeepRenewingLease` 方法是另一個協助程式方法，它會使用 `BlobLeaseManager` 物件來更新租用。</span><span class="sxs-lookup"><span data-stu-id="2700e-190">The `KeepRenewingLease` method is another helper method that uses the `BlobLeaseManager` object to renew the lease.</span></span> <span data-ttu-id="2700e-191">`CancelAllWhenAnyCompletes` 方法會取消前兩個參數所指定的工作。</span><span class="sxs-lookup"><span data-stu-id="2700e-191">The `CancelAllWhenAnyCompletes` method cancels the tasks specified as the first two parameters.</span></span> <span data-ttu-id="2700e-192">下圖說明使用 `BlobDistributedMutex` 類別來選擇領導者，並執行協調作業的工作。</span><span class="sxs-lookup"><span data-stu-id="2700e-192">The following diagram illustrates using the `BlobDistributedMutex` class to elect a leader and run a task that coordinates operations.</span></span>

![圖 1 說明 BlobDistributedMutex 類別的函式](./_images/leader-election-diagram.png)


<span data-ttu-id="2700e-194">下列程式碼範例示範如何使用背景工作角色中的 `BlobDistributedMutex` 類別。</span><span class="sxs-lookup"><span data-stu-id="2700e-194">The following code example shows how to use the `BlobDistributedMutex` class in a worker role.</span></span> <span data-ttu-id="2700e-195">此程式碼會向開發儲存體中租用容器內名為 `MyLeaderCoordinatorTask` 的 Blob 取得租用，並指出如果該角色執行個體獲選為領導者，則應執行 `MyLeaderCoordinatorTask` 方法中定義的程式碼。</span><span class="sxs-lookup"><span data-stu-id="2700e-195">This code acquires a lease over a blob named `MyLeaderCoordinatorTask` in the lease's container in development storage, and specifies that the code defined in the `MyLeaderCoordinatorTask` method should run if the role instance is elected the leader.</span></span>

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

<span data-ttu-id="2700e-196">請注意下列有關範例解決方案的重點：</span><span class="sxs-lookup"><span data-stu-id="2700e-196">Note the following points about the sample solution:</span></span>
- <span data-ttu-id="2700e-197">Blob 是潛在的單一失敗點。</span><span class="sxs-lookup"><span data-stu-id="2700e-197">The blob is a potential single point of failure.</span></span> <span data-ttu-id="2700e-198">如果 Blob 服務變得無法使用或無法存取，領導者將無法更新租用，而且其他角色執行個體都將無法取得租用。</span><span class="sxs-lookup"><span data-stu-id="2700e-198">If the blob service becomes unavailable, or is inaccessible, the leader won't be able to renew the lease and no other role instance will be able to acquire the lease.</span></span> <span data-ttu-id="2700e-199">在此情況下，任何角色執行個體都無法作為領導者。</span><span class="sxs-lookup"><span data-stu-id="2700e-199">In this case, no role instance will be able to act as the leader.</span></span> <span data-ttu-id="2700e-200">不過，Blob 服務的設計很有彈性，因此 Blob 服務幾乎不可能出現完全失敗的情況。</span><span class="sxs-lookup"><span data-stu-id="2700e-200">However, the blob service is designed to be resilient, so complete failure of the blob service is considered to be extremely unlikely.</span></span>
- <span data-ttu-id="2700e-201">如果領導者執行的工作停止，領導者可能會不停更新租用，避免任何其他角色執行個體取得租用並接任領導者角色來協調工作。</span><span class="sxs-lookup"><span data-stu-id="2700e-201">If the task being performed by the leader stalls, the leader might continue to renew the lease, preventing any other role instance from acquiring the lease and taking over the leader role in order to coordinate tasks.</span></span> <span data-ttu-id="2700e-202">在真實世界中，應頻繁檢查領導者的健康情況。</span><span class="sxs-lookup"><span data-stu-id="2700e-202">In the real world, the health of the leader should be checked at frequent intervals.</span></span>
- <span data-ttu-id="2700e-203">選擇程序並不具決定性。</span><span class="sxs-lookup"><span data-stu-id="2700e-203">The election process is nondeterministic.</span></span> <span data-ttu-id="2700e-204">您不得假設哪個角色執行個體會取得 Blob 租用並成為領導者。</span><span class="sxs-lookup"><span data-stu-id="2700e-204">You can't make any assumptions about which role instance will acquire the blob lease and become the leader.</span></span>
- <span data-ttu-id="2700e-205">當成 Blob 租用目標的 Blob 不應用於任何其他用途。</span><span class="sxs-lookup"><span data-stu-id="2700e-205">The blob used as the target of the blob lease shouldn't be used for any other purpose.</span></span> <span data-ttu-id="2700e-206">如果角色執行個體嘗試儲存此 Blob 中的資料，除非該角色執行個體是領導者且握有 Blob 租用，否則便無法存取此資料。</span><span class="sxs-lookup"><span data-stu-id="2700e-206">If a role instance attempts to store data in this blob, this data won't be accessible unless the role instance is the leader and holds the blob lease.</span></span>

## <a name="related-patterns-and-guidance"></a><span data-ttu-id="2700e-207">相關的模式和指導方針</span><span class="sxs-lookup"><span data-stu-id="2700e-207">Related patterns and guidance</span></span>

<span data-ttu-id="2700e-208">實作此模式時，下列指導方針可能也相關：</span><span class="sxs-lookup"><span data-stu-id="2700e-208">The following guidance might also be relevant when implementing this pattern:</span></span>
- <span data-ttu-id="2700e-209">此模式有可下載的[範例應用程式](https://github.com/mspnp/cloud-design-patterns/tree/master/leader-election)。</span><span class="sxs-lookup"><span data-stu-id="2700e-209">This pattern has a downloadable [sample application](https://github.com/mspnp/cloud-design-patterns/tree/master/leader-election).</span></span>
- <span data-ttu-id="2700e-210">[自動調整指導方針](https://msdn.microsoft.com/library/dn589774.aspx)。</span><span class="sxs-lookup"><span data-stu-id="2700e-210">[Autoscaling Guidance](https://msdn.microsoft.com/library/dn589774.aspx).</span></span> <span data-ttu-id="2700e-211">工作主機的執行個體可以隨著應用程式的負載來啟動和停止。</span><span class="sxs-lookup"><span data-stu-id="2700e-211">It's possible to start and stop instances of the task hosts as the load on the application varies.</span></span> <span data-ttu-id="2700e-212">自動調整規模有助於維護尖峰處理時間的輸送量和效能。</span><span class="sxs-lookup"><span data-stu-id="2700e-212">Autoscaling can help to maintain throughput and performance during times of peak processing.</span></span>
- <span data-ttu-id="2700e-213">[計算分割指導方針](https://msdn.microsoft.com/library/dn589773.aspx) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="2700e-213">[Compute Partitioning Guidance](https://msdn.microsoft.com/library/dn589773.aspx).</span></span> <span data-ttu-id="2700e-214">本指導方針說明如何以有助於將執行成本降至最低，同時維持服務延展性、效能、可用性及安全性的方式，將工作配置到雲端服務中的主機。</span><span class="sxs-lookup"><span data-stu-id="2700e-214">This guidance describes how to allocate tasks to hosts in a cloud service in a way that helps to minimize running costs while maintaining the scalability, performance, availability, and security of the service.</span></span>
- <span data-ttu-id="2700e-215">[工作式非同步模式](https://msdn.microsoft.com/library/hh873175.aspx)。</span><span class="sxs-lookup"><span data-stu-id="2700e-215">The [Task-based Asynchronous Pattern](https://msdn.microsoft.com/library/hh873175.aspx).</span></span>
- <span data-ttu-id="2700e-216">說明[強勢演算法](http://www.cs.colostate.edu/~cs551/CourseNotes/Synchronization/BullyExample.html)的範例。</span><span class="sxs-lookup"><span data-stu-id="2700e-216">An example illustrating the [Bully Algorithm](http://www.cs.colostate.edu/~cs551/CourseNotes/Synchronization/BullyExample.html).</span></span>
- <span data-ttu-id="2700e-217">說明[通道演算法](http://www.cs.colostate.edu/~cs551/CourseNotes/Synchronization/RingElectExample.html)的範例。</span><span class="sxs-lookup"><span data-stu-id="2700e-217">An example illustrating the [Ring Algorithm](http://www.cs.colostate.edu/~cs551/CourseNotes/Synchronization/RingElectExample.html).</span></span>
- <span data-ttu-id="2700e-218">Microsoft Open Technologies 網站上的 [Apache Zookeeper on Microsoft Azure](https://msopentech.com/opentech-projects/apache-zookeeper-on-windows-azure-2/) 一文。</span><span class="sxs-lookup"><span data-stu-id="2700e-218">The article [Apache Zookeeper on Microsoft Azure](https://msopentech.com/opentech-projects/apache-zookeeper-on-windows-azure-2/) on the Microsoft Open Technologies website.</span></span>
- <span data-ttu-id="2700e-219">[Apache Curator](http://curator.apache.org/) 是 Apache ZooKeeper 的用戶端程式庫。</span><span class="sxs-lookup"><span data-stu-id="2700e-219">[Apache Curator](http://curator.apache.org/) a client library for Apache ZooKeeper.</span></span>
- <span data-ttu-id="2700e-220">MSDN 上的[租用 Blob (REST API)](https://msdn.microsoft.com/library/azure/ee691972.aspx) \(英文\) 一文。</span><span class="sxs-lookup"><span data-stu-id="2700e-220">The article [Lease Blob (REST API)](https://msdn.microsoft.com/library/azure/ee691972.aspx) on MSDN.</span></span>

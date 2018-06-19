---
title: 計算資源彙總
description: 將多個工作或作業合併為單一計算單位
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories:
- design-implementation
ms.openlocfilehash: 6e05a30245fbf5183a4e50a54650505f5a5f2aa8
ms.sourcegitcommit: 85334ab0ccb072dac80de78aa82bcfa0f0044d3f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35252919"
---
# <a name="compute-resource-consolidation-pattern"></a><span data-ttu-id="9abc4-104">計算資源彙總模式</span><span class="sxs-lookup"><span data-stu-id="9abc4-104">Compute Resource Consolidation pattern</span></span>

[!INCLUDE [header](../_includes/header.md)]

<span data-ttu-id="9abc4-105">將多個工作或作業合併為單一計算單位。</span><span class="sxs-lookup"><span data-stu-id="9abc4-105">Consolidate multiple tasks or operations into a single computational unit.</span></span> <span data-ttu-id="9abc4-106">這可以提升計算資源使用率，並降低與雲端內主控的應用程式中執行計算處理相關聯的成本和管理額外負荷。</span><span class="sxs-lookup"><span data-stu-id="9abc4-106">This can increase compute resource utilization, and reduce the costs and management overhead associated with performing compute processing in cloud-hosted applications.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="9abc4-107">內容和問題</span><span class="sxs-lookup"><span data-stu-id="9abc4-107">Context and problem</span></span>

<span data-ttu-id="9abc4-108">雲端應用程式通常會實作各種作業。</span><span class="sxs-lookup"><span data-stu-id="9abc4-108">A cloud application often implements a variety of operations.</span></span> <span data-ttu-id="9abc4-109">在某些解決方案中，可以合理地在一開始依照重要性區隔的設計原則，將這些作業分割成分別主控和部署的個別計算單位 (例如，作為個別 App Service web 應用程式、個別虛擬機器或個別雲端服務角色)。</span><span class="sxs-lookup"><span data-stu-id="9abc4-109">In some solutions it makes sense to follow the design principle of separation of concerns initially, and divide these operations into separate computational units that are hosted and deployed individually (for example, as separate App Service web apps, separate Virtual Machines, or separate Cloud Service roles).</span></span> <span data-ttu-id="9abc4-110">不過，雖然這項策略有助於簡化解決方案的邏輯設計，但在相同應用程式中部署大量的計算單位可能會增加執行階段主機成本，並使得系統管理更為複雜。</span><span class="sxs-lookup"><span data-stu-id="9abc4-110">However, although this strategy can help simplify the logical design of the solution, deploying a large number of computational units as part of the same application can increase runtime hosting costs and make management of the system more complex.</span></span>

<span data-ttu-id="9abc4-111">例如，此圖顯示雲端主控解決方案的簡化結構，是使用一個以上的計算單位進行實作。</span><span class="sxs-lookup"><span data-stu-id="9abc4-111">As an example, the figure shows the simplified structure of a cloud-hosted solution that is implemented using more than one computational unit.</span></span> <span data-ttu-id="9abc4-112">每個計算的單位都會在它自己的虛擬環境中執行。</span><span class="sxs-lookup"><span data-stu-id="9abc4-112">Each computational unit runs in its own virtual environment.</span></span> <span data-ttu-id="9abc4-113">每個函式皆已實作為個別的工作 (標示為工作 A 到工作 E)，在它自己的計算單位中執行。</span><span class="sxs-lookup"><span data-stu-id="9abc4-113">Each function has been implemented as a separate task (labeled Task A through Task E) running in its own computational unit.</span></span>

![使用一組專用的計算單位在雲端環境中執行工作](./_images/compute-resource-consolidation-diagram.png)


<span data-ttu-id="9abc4-115">每個計算單位都會耗用收費的資源，即使在閒置或輕量使用時也一樣。</span><span class="sxs-lookup"><span data-stu-id="9abc4-115">Each computational unit consumes chargeable resources, even when it's idle or lightly used.</span></span> <span data-ttu-id="9abc4-116">因此，這不一定是最具成本效益的解決方案。</span><span class="sxs-lookup"><span data-stu-id="9abc4-116">Therefore, this isn't always the most cost-effective solution.</span></span>

<span data-ttu-id="9abc4-117">在 Azure 中，這個考量也適用於雲端服務、應用程式服務和虛擬機器中的角色。</span><span class="sxs-lookup"><span data-stu-id="9abc4-117">In Azure, this concern applies to roles in a Cloud Service, App Services, and Virtual Machines.</span></span> <span data-ttu-id="9abc4-118">這些項目會在自己的虛擬環境中執行。</span><span class="sxs-lookup"><span data-stu-id="9abc4-118">These items run in their own virtual environment.</span></span> <span data-ttu-id="9abc4-119">執行一系列專為執行一組妥善定義作業所設計的不同角色、網站或虛擬機器，但必須作為單一解決方案進行通訊及共同合作，可以會造成資源的使用效率不佳。</span><span class="sxs-lookup"><span data-stu-id="9abc4-119">Running a collection of separate roles, websites, or virtual machines that are designed to perform a set of well-defined operations, but that need to communicate and cooperate as part of a single solution, can be an inefficient use of resources.</span></span>

## <a name="solution"></a><span data-ttu-id="9abc4-120">解決方法</span><span class="sxs-lookup"><span data-stu-id="9abc4-120">Solution</span></span>

<span data-ttu-id="9abc4-121">為協助降低成本、增加使用率、改善通訊速度並減少管理，可以將多個工作或作業合併為單一計算單位。</span><span class="sxs-lookup"><span data-stu-id="9abc4-121">To help reduce costs, increase utilization, improve communication speed, and reduce management it's possible to consolidate multiple tasks or operations into a single computational unit.</span></span>

<span data-ttu-id="9abc4-122">工作可以根據以環境所提供的功能以及與這些功能相關聯的成本作為基礎的準則進行分組。</span><span class="sxs-lookup"><span data-stu-id="9abc4-122">Tasks can be grouped according to criteria based on the features provided by the environment and the costs associated with these features.</span></span> <span data-ttu-id="9abc4-123">常見方法是尋找具有類似關於其延展性、存留期和處理需求的設定檔工作。</span><span class="sxs-lookup"><span data-stu-id="9abc4-123">A common approach is to look for tasks that have a similar profile concerning their scalability, lifetime, and processing requirements.</span></span> <span data-ttu-id="9abc4-124">將這些群組在一起，可讓它們作為一個單位進行調整。</span><span class="sxs-lookup"><span data-stu-id="9abc4-124">Grouping these together allows them to scale as a unit.</span></span> <span data-ttu-id="9abc4-125">許多雲端環所提供的彈性，可讓其他計算單位的執行個體根據工作負載啟動及停止。</span><span class="sxs-lookup"><span data-stu-id="9abc4-125">The elasticity provided by many cloud environments enables additional instances of a computational unit to be started and stopped according to the workload.</span></span> <span data-ttu-id="9abc4-126">例如，Azure 會提供自動調整，可讓您套用至雲端服務、應用程式服務和虛擬機器中的角色。</span><span class="sxs-lookup"><span data-stu-id="9abc4-126">For example, Azure provides autoscaling that you can apply to roles in a Cloud Service, App Services, and Virtual Machines.</span></span> <span data-ttu-id="9abc4-127">如需詳細資訊，請參閱[自動調整指導方針](https://msdn.microsoft.com/library/dn589774.aspx)。</span><span class="sxs-lookup"><span data-stu-id="9abc4-127">For more information, see [Autoscaling Guidance](https://msdn.microsoft.com/library/dn589774.aspx).</span></span>

<span data-ttu-id="9abc4-128">作為顯示如何使用擴充性來判斷哪些作業不應分組在一起的相反範例，請考慮下列兩項工作：</span><span class="sxs-lookup"><span data-stu-id="9abc4-128">As a counter example to show how scalability can be used to determine which operations shouldn't be grouped together, consider the following two tasks:</span></span>

- <span data-ttu-id="9abc4-129">工作 1 輪詢傳送至佇列的不頻繁、時間不緊迫訊息。</span><span class="sxs-lookup"><span data-stu-id="9abc4-129">Task 1 polls for infrequent, time-insensitive messages sent to a queue.</span></span>
- <span data-ttu-id="9abc4-130">工作 2 處理大量暴增的網路流量。</span><span class="sxs-lookup"><span data-stu-id="9abc4-130">Task 2 handles high-volume bursts of network traffic.</span></span>

<span data-ttu-id="9abc4-131">第二項工作需要的彈性，可包含啟動和停止大量計算單位的執行個體。</span><span class="sxs-lookup"><span data-stu-id="9abc4-131">The second task requires elasticity that can involve starting and stopping a large number of instances of the computational unit.</span></span> <span data-ttu-id="9abc4-132">將相同縮放比例套用至第一項工作只會導致更多工作在相同的佇列上接聽不頻繁訊息，並會浪費資源。</span><span class="sxs-lookup"><span data-stu-id="9abc4-132">Applying the same scaling to the first task would simply result in more tasks listening for infrequent messages on the same queue, and is a waste of resources.</span></span>

<span data-ttu-id="9abc4-133">在許多雲端環境中，可能會就 CPU 核心數目、記憶體、磁碟空間等方面，指定可用於計算單位的資源。</span><span class="sxs-lookup"><span data-stu-id="9abc4-133">In many cloud environments it's possible to specify the resources available to a computational unit in terms of the number of CPU cores, memory, disk space, and so on.</span></span> <span data-ttu-id="9abc4-134">一般而言，指定越多資源，成本就會越高。</span><span class="sxs-lookup"><span data-stu-id="9abc4-134">Generally, the more resources specified, the greater the cost.</span></span> <span data-ttu-id="9abc4-135">為節省成本，務必將昂貴計算單位執行的工作最大化，並且不讓它長時間處於非作用中狀態。</span><span class="sxs-lookup"><span data-stu-id="9abc4-135">To save money, it's important to maximize the work an expensive computational unit performs, and not let it become inactive for an extended period.</span></span>

<span data-ttu-id="9abc4-136">如果有些工作暴增需要大量的 CPU 電源，請考慮將這些項目合併為單一計算單位，以提供必要的電源。</span><span class="sxs-lookup"><span data-stu-id="9abc4-136">If there are tasks that require a great deal of CPU power in short bursts, consider consolidating these into a single computational unit that provides the necessary power.</span></span> <span data-ttu-id="9abc4-137">不過，如果它們過度負荷，請務必平衡這項需求，讓昂貴的資源不停處理可能發生的爭用。</span><span class="sxs-lookup"><span data-stu-id="9abc4-137">However, it's important to balance this need to keep expensive resources busy against the contention that could occur if they are over stressed.</span></span> <span data-ttu-id="9abc4-138">例如，長時間執行、需要大量計算的工作不應該共用相同的計算單位。</span><span class="sxs-lookup"><span data-stu-id="9abc4-138">Long-running, compute-intensive tasks shouldn't share the same computational unit, for example.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="9abc4-139">問題和考量</span><span class="sxs-lookup"><span data-stu-id="9abc4-139">Issues and considerations</span></span>

<span data-ttu-id="9abc4-140">當您實作此模式時，請考慮下列幾點：</span><span class="sxs-lookup"><span data-stu-id="9abc4-140">Consider the following points when implementing this pattern:</span></span>

<span data-ttu-id="9abc4-141">**延展性和彈性**。</span><span class="sxs-lookup"><span data-stu-id="9abc4-141">**Scalability and elasticity**.</span></span> <span data-ttu-id="9abc4-142">許多雲端解決方案會在計算單位層級實作延展性和彈性，方法是啟動和停止單位的執行個體。</span><span class="sxs-lookup"><span data-stu-id="9abc4-142">Many cloud solutions implement scalability and elasticity at the level of the computational unit by starting and stopping instances of units.</span></span> <span data-ttu-id="9abc4-143">避免將相同計算單位中有衝突延展性需求的工作進行群組。</span><span class="sxs-lookup"><span data-stu-id="9abc4-143">Avoid grouping tasks that have conflicting scalability requirements in the same computational unit.</span></span>

<span data-ttu-id="9abc4-144">**存留期**。</span><span class="sxs-lookup"><span data-stu-id="9abc4-144">**Lifetime**.</span></span> <span data-ttu-id="9abc4-145">雲端基礎結構會定期回收主控計算單位的虛擬環境。</span><span class="sxs-lookup"><span data-stu-id="9abc4-145">The cloud infrastructure periodically recycles the virtual environment that hosts a computational unit.</span></span> <span data-ttu-id="9abc4-146">當計算單位內有許多長時間執行的工作時，可能必須設定單位以防止它受到回收，直到完成這些工作為止。</span><span class="sxs-lookup"><span data-stu-id="9abc4-146">When there are many long-running tasks inside a computational unit, it might be necessary to configure the unit to prevent it from being recycled until these tasks have finished.</span></span> <span data-ttu-id="9abc4-147">或者，使用檢查點方法來設計工作，讓他們能夠完全停止，並且當計算單位重新啟動時，從中斷處繼續進行。</span><span class="sxs-lookup"><span data-stu-id="9abc4-147">Alternatively, design the tasks by using a check-pointing approach that enables them to stop cleanly, and continue at the point they were interrupted when the computational unit is restarted.</span></span>

<span data-ttu-id="9abc4-148">**版本日程**。</span><span class="sxs-lookup"><span data-stu-id="9abc4-148">**Release cadence**.</span></span> <span data-ttu-id="9abc4-149">如果工作的實作或組態經常變更，可能必須停止主控更新之程式碼的計算單位、重新設定及重新部署單位，並再重新啟動它。</span><span class="sxs-lookup"><span data-stu-id="9abc4-149">If the implementation or configuration of a task changes frequently, it might be necessary to stop the computational unit hosting the updated code, reconfigure and redeploy the unit, and then restart it.</span></span> <span data-ttu-id="9abc4-150">此程序也需要相同計算單位內的所有其他工作停止、重新部署並重新啟動。</span><span class="sxs-lookup"><span data-stu-id="9abc4-150">This process will also require that all other tasks within the same computational unit are stopped, redeployed, and restarted.</span></span>

<span data-ttu-id="9abc4-151">**安全性**。</span><span class="sxs-lookup"><span data-stu-id="9abc4-151">**Security**.</span></span> <span data-ttu-id="9abc4-152">相同計算單位中的工作可能會共用相同的資訊安全內容，且能夠存取相同的資源。</span><span class="sxs-lookup"><span data-stu-id="9abc4-152">Tasks in the same computational unit might share the same security context and be able to access the same resources.</span></span> <span data-ttu-id="9abc4-153">工作之間必須有較高程度的信任，且有信心一個工作不會損毀或對另一個工作造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="9abc4-153">There must be a high degree of trust between the tasks, and confidence that one task isn't going to corrupt or adversely affect another.</span></span> <span data-ttu-id="9abc4-154">此外，增加計算單位中執行的工作數目會增加單位的攻擊面。</span><span class="sxs-lookup"><span data-stu-id="9abc4-154">Additionally, increasing the number of tasks running in a computational unit increases the attack surface of the unit.</span></span> <span data-ttu-id="9abc4-155">每項工作的安全程度只會相當於具有最多的工作。</span><span class="sxs-lookup"><span data-stu-id="9abc4-155">Each task is only as secure as the one with the most vulnerabilities.</span></span>

<span data-ttu-id="9abc4-156">**容錯**。</span><span class="sxs-lookup"><span data-stu-id="9abc4-156">**Fault tolerance**.</span></span> <span data-ttu-id="9abc4-157">如果計算單位中有一個工作失敗或行為異常，可能會影響相同單位內執行的其他工作。</span><span class="sxs-lookup"><span data-stu-id="9abc4-157">If one task in a computational unit fails or behaves abnormally, it can affect the other tasks running within the same unit.</span></span> <span data-ttu-id="9abc4-158">例如，如果一項工作無法正確啟動，它會造成計算單位的整個啟動邏輯失敗，並且使相同單位中的其他工作無法執行。</span><span class="sxs-lookup"><span data-stu-id="9abc4-158">For example, if one task fails to start correctly it can cause the entire startup logic for the computational unit to fail, and prevent other tasks in the same unit from running.</span></span>

<span data-ttu-id="9abc4-159">**爭用**。</span><span class="sxs-lookup"><span data-stu-id="9abc4-159">**Contention**.</span></span> <span data-ttu-id="9abc4-160">避免造成工作之間的爭用，競爭相同計算單位中的資源。</span><span class="sxs-lookup"><span data-stu-id="9abc4-160">Avoid introducing contention between tasks that compete for resources in the same computational unit.</span></span> <span data-ttu-id="9abc4-161">在理想情況下，共用相同計算單位的工作應該會表現不同的資源使用率特性。</span><span class="sxs-lookup"><span data-stu-id="9abc4-161">Ideally, tasks that share the same computational unit should exhibit different resource utilization characteristics.</span></span> <span data-ttu-id="9abc4-162">例如，兩個需要大量計算的工作可能不應該位於相同的單位計算中，且兩個工作都不應該耗用大量的記憶體。</span><span class="sxs-lookup"><span data-stu-id="9abc4-162">For example, two compute-intensive tasks should probably not reside in the same computational unit, and neither should two tasks that consume large amounts of memory.</span></span> <span data-ttu-id="9abc4-163">不過，將需要大量計算的工作與需要大量記憶體的工作混用，是可行的組合。</span><span class="sxs-lookup"><span data-stu-id="9abc4-163">However, mixing a compute intensive task with a task that requires a large amount of memory is a workable combination.</span></span>

> [!NOTE]
>  <span data-ttu-id="9abc4-164">請考慮僅將已在生產環境中一段時間之系統的計算資源合併，讓操作員和開發人員可以監視系統，並建立_熱度圖_來識別每項工作如何利用不同的資源。</span><span class="sxs-lookup"><span data-stu-id="9abc4-164">Consider consolidating compute resources only for a system that's been in production for a period of time so that operators and developers can monitor the system and create a _heat map_ that identifies how each task utilizes differing resources.</span></span> <span data-ttu-id="9abc4-165">此圖可用來判斷哪些工作適合用於共用計算資源。</span><span class="sxs-lookup"><span data-stu-id="9abc4-165">This map can be used to determine which tasks are good candidates for sharing compute resources.</span></span>

<span data-ttu-id="9abc4-166">**複雜度**。</span><span class="sxs-lookup"><span data-stu-id="9abc4-166">**Complexity**.</span></span> <span data-ttu-id="9abc4-167">將多個工作結合成單一計算單位會使單位中的程式碼複雜度增加，可能會更加難以測試、偵錯及維護。</span><span class="sxs-lookup"><span data-stu-id="9abc4-167">Combining multiple tasks into a single computational unit adds complexity to the code in the unit, possibly making it more difficult to test, debug, and maintain.</span></span>

<span data-ttu-id="9abc4-168">**穩定邏輯架構**。</span><span class="sxs-lookup"><span data-stu-id="9abc4-168">**Stable logical architecture**.</span></span> <span data-ttu-id="9abc4-169">在每個工作中設計和實作程式碼，使它不太需要變更，即使是工作在其中執行的環境有所變更也一樣。</span><span class="sxs-lookup"><span data-stu-id="9abc4-169">Design and implement the code in each task so that it shouldn't need to change, even if the physical environment the task runs in does change.</span></span>

<span data-ttu-id="9abc4-170">**其他策略**。</span><span class="sxs-lookup"><span data-stu-id="9abc4-170">**Other strategies**.</span></span> <span data-ttu-id="9abc4-171">將計算資源合併只是有助於減少同時執行多個工作相關成本的其中一種方式。</span><span class="sxs-lookup"><span data-stu-id="9abc4-171">Consolidating compute resources is only one way to help reduce costs associated with running multiple tasks concurrently.</span></span> <span data-ttu-id="9abc4-172">它需要仔細規劃並監視，以確保它仍然是有效的方法。</span><span class="sxs-lookup"><span data-stu-id="9abc4-172">It requires careful planning and monitoring to ensure that it remains an effective approach.</span></span> <span data-ttu-id="9abc4-173">其他策略可能更合適，視工作的本質和執行這些工作的使用者所在位置而定。</span><span class="sxs-lookup"><span data-stu-id="9abc4-173">Other strategies might be more appropriate, depending on the nature of the work and where the users these tasks are running are located.</span></span> <span data-ttu-id="9abc4-174">例如，工作負載的功能性分解 (依[計算分割區指引](https://msdn.microsoft.com/library/dn589773.aspx)所述) 可能是更好的選項。</span><span class="sxs-lookup"><span data-stu-id="9abc4-174">For example, functional decomposition of the workload (as described by the [Compute Partitioning Guidance](https://msdn.microsoft.com/library/dn589773.aspx)) might be a better option.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="9abc4-175">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="9abc4-175">When to use this pattern</span></span>

<span data-ttu-id="9abc4-176">如果工作在本身的計算單位中執行卻不符合成本效益，請使用此模式。</span><span class="sxs-lookup"><span data-stu-id="9abc4-176">Use this pattern for tasks that are not cost effective if they run in their own computational units.</span></span> <span data-ttu-id="9abc4-177">如果工作花費太多閒置時間，在專用的單位中執行此工作可能會很昂貴。</span><span class="sxs-lookup"><span data-stu-id="9abc4-177">If a task spends much of its time idle, running this task in a dedicated unit can be expensive.</span></span>

<span data-ttu-id="9abc4-178">此模式可能不適用於執行關鍵容錯作業的工作，或是處理高度敏感或私人資料、且需要使用者本身安全性內容的工作。</span><span class="sxs-lookup"><span data-stu-id="9abc4-178">This pattern might not be suitable for tasks that perform critical fault-tolerant operations, or tasks that process highly sensitive or private data and require their own security context.</span></span> <span data-ttu-id="9abc4-179">這些工作應該在其自己的隔離環境中執行，並使用個別計算單位。</span><span class="sxs-lookup"><span data-stu-id="9abc4-179">These tasks should run in their own isolated environment, in a separate computational unit.</span></span>

## <a name="example"></a><span data-ttu-id="9abc4-180">範例</span><span class="sxs-lookup"><span data-stu-id="9abc4-180">Example</span></span>

<span data-ttu-id="9abc4-181">在 Azure 上建置雲端服務時，可以將多個工作執行的處理合併成單一角色。</span><span class="sxs-lookup"><span data-stu-id="9abc4-181">When building a cloud service on Azure, it’s possible to consolidate the processing performed by multiple tasks into a single role.</span></span> <span data-ttu-id="9abc4-182">一般而言，這是背景工作角色，可執行背景或非同步處理工作。</span><span class="sxs-lookup"><span data-stu-id="9abc4-182">Typically this is a worker role that performs background or asynchronous processing tasks.</span></span>

> <span data-ttu-id="9abc4-183">在某些情況下，可能會包含 web 角色中的背景或非同步處理工作。</span><span class="sxs-lookup"><span data-stu-id="9abc4-183">In some cases it's possible to include background or asynchronous processing tasks in the web role.</span></span> <span data-ttu-id="9abc4-184">雖然這項技術可能會影響 web 角色所提供的公眾對應介面之延展性和回應性，但它有助於降低成本並簡化部署。</span><span class="sxs-lookup"><span data-stu-id="9abc4-184">This technique helps to reduce costs and simplify deployment, although it can impact the scalability and responsiveness of the public-facing interface provided by the web role.</span></span> <span data-ttu-id="9abc4-185">[將多個 Azure 背景工作角色結合為 Azure Web 角色](http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2012/02/combining-multiple-azure-worker-roles.html)文章中包含在 web 角色中實作背景或非同步處理工作的詳細描述。</span><span class="sxs-lookup"><span data-stu-id="9abc4-185">The article [Combining Multiple Azure Worker Roles into an Azure Web Role](http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2012/02/combining-multiple-azure-worker-roles.html) contains a detailed description of implementing background or asynchronous processing tasks in a web role.</span></span>

<span data-ttu-id="9abc4-186">角色負責啟動和停止工作。</span><span class="sxs-lookup"><span data-stu-id="9abc4-186">The role is responsible for starting and stopping the tasks.</span></span> <span data-ttu-id="9abc4-187">當 Azure 網狀架構控制器載入角色時，會引發角色的 `Start` 事件。</span><span class="sxs-lookup"><span data-stu-id="9abc4-187">When the Azure fabric controller loads a role, it raises the `Start` event for the role.</span></span> <span data-ttu-id="9abc4-188">您可以覆寫 `WebRole` 或 `WorkerRole` 類別的 `OnStart` 方法來處理此事件，或許還可以初始化這個方法中之工作所仰賴的資料和其他資源。</span><span class="sxs-lookup"><span data-stu-id="9abc4-188">You can override the `OnStart` method of the `WebRole` or `WorkerRole` class to handle this event, perhaps to initialize the data and other resources the tasks in this method depend on.</span></span>

<span data-ttu-id="9abc4-189">當 `OnStart` 方法完成時，角色就可以開始回應要求。</span><span class="sxs-lookup"><span data-stu-id="9abc4-189">When the `OnStart` method completes, the role can start responding to requests.</span></span> <span data-ttu-id="9abc4-190">您可以在[將應用程式移動至雲端](https://msdn.microsoft.com/library/ff728592.aspx)模式和實務指南的[應用程式啟動流程](https://msdn.microsoft.com/library/ff803371.aspx#sec16)一節中，使用角色中的 `OnStart` 和 `Run` 方法，來找到詳細資訊和指引。</span><span class="sxs-lookup"><span data-stu-id="9abc4-190">You can find more information and guidance about using the `OnStart` and `Run` methods in a role in the [Application Startup Processes](https://msdn.microsoft.com/library/ff803371.aspx#sec16) section in the patterns & practices guide [Moving Applications to the Cloud](https://msdn.microsoft.com/library/ff728592.aspx).</span></span>

> <span data-ttu-id="9abc4-191">盡可能以簡明的 `OnStart` 方法來保留程式碼。</span><span class="sxs-lookup"><span data-stu-id="9abc4-191">Keep the code in the `OnStart` method as concise as possible.</span></span> <span data-ttu-id="9abc4-192">Azure 並不限制這個方法完成所花費的時間，但該角色將無法開始回應傳送給它的網路要求，直到這個方法完成為止。</span><span class="sxs-lookup"><span data-stu-id="9abc4-192">Azure doesn't impose any limit on the time taken for this method to complete, but the role won't be able to start responding to network requests sent to it until this method completes.</span></span>

<span data-ttu-id="9abc4-193">當 `OnStart` 方法完成時，角色會執行 `Run` 方法。</span><span class="sxs-lookup"><span data-stu-id="9abc4-193">When the `OnStart` method has finished, the role executes the `Run` method.</span></span> <span data-ttu-id="9abc4-194">此時，網狀架構控制器就可以開始將要求傳送至角色。</span><span class="sxs-lookup"><span data-stu-id="9abc4-194">At this point, the fabric controller can start sending requests to the role.</span></span>

<span data-ttu-id="9abc4-195">放置以 `Run` 方法實際建立工作的程式碼。</span><span class="sxs-lookup"><span data-stu-id="9abc4-195">Place the code that actually creates the tasks in the `Run` method.</span></span> <span data-ttu-id="9abc4-196">請注意，`Run` 方法會定義角色執行個體的存留期。</span><span class="sxs-lookup"><span data-stu-id="9abc4-196">Note that the `Run` method defines the lifetime of the role instance.</span></span> <span data-ttu-id="9abc4-197">這個方法完成時，網狀架構控制器就會排列要關機的角色。</span><span class="sxs-lookup"><span data-stu-id="9abc4-197">When this method completes, the fabric controller will arrange for the role to be shut down.</span></span>

<span data-ttu-id="9abc4-198">當角色關機或回收時，網狀架構控制器會阻止接收來自負載平衡器的任何其他連入要求，並且會引發 `Stop` 事件。</span><span class="sxs-lookup"><span data-stu-id="9abc4-198">When a role shuts down or is recycled, the fabric controller prevents any more incoming requests being received from the load balancer and raises the `Stop` event.</span></span> <span data-ttu-id="9abc4-199">您可以在角色終止之前覆寫角色的 `OnStop` 方法，並執行任何必要的排列，來擷取這個事件。</span><span class="sxs-lookup"><span data-stu-id="9abc4-199">You can capture this event by overriding the `OnStop` method of the role and perform any tidying up required before the role terminates.</span></span>

> <span data-ttu-id="9abc4-200">在 `OnStop` 方法中執行的任何動作，都必須在五分鐘 (或如果您是使用本機電腦上的 Azure 模擬器，就是 30 秒) 內完成。</span><span class="sxs-lookup"><span data-stu-id="9abc4-200">Any actions performed in the `OnStop` method must be completed within five minutes (or 30 seconds if you are using the Azure emulator on a local computer).</span></span> <span data-ttu-id="9abc4-201">否則，Azure 網狀架構控制器就會假設角色延滯，並強制它停止。</span><span class="sxs-lookup"><span data-stu-id="9abc4-201">Otherwise the Azure fabric controller assumes that the role has stalled and will force it to stop.</span></span>

<span data-ttu-id="9abc4-202">工作會由 `Run` 方法啟動，等候工作完成。</span><span class="sxs-lookup"><span data-stu-id="9abc4-202">The tasks are started by the `Run` method that waits for the tasks to complete.</span></span> <span data-ttu-id="9abc4-203">這些工作會實作雲端服務的商務邏輯，並且可回應透過 Azure 負載平衡器向角色公佈的訊息。</span><span class="sxs-lookup"><span data-stu-id="9abc4-203">The tasks implement the business logic of the cloud service, and can respond to messages posted to the role through the Azure load balancer.</span></span> <span data-ttu-id="9abc4-204">此圖會顯示 Azure 雲端服務的角色中，工作和資源的生命週期。</span><span class="sxs-lookup"><span data-stu-id="9abc4-204">The figure shows the lifecycle of tasks and resources in a role in an Azure cloud service.</span></span>

![Azure 雲端服務的角色中，工作和資源的生命週期](./_images/compute-resource-consolidation-lifecycle.png)


<span data-ttu-id="9abc4-206">_ComputeResourceConsolidation.Worker_ 專案中的 _WorkerRole.cs_ 檔案會顯示範例，說明如何在 Azure 雲端服務中實作此模式。</span><span class="sxs-lookup"><span data-stu-id="9abc4-206">The _WorkerRole.cs_ file in the _ComputeResourceConsolidation.Worker_ project shows an example of how you might implement this pattern in an Azure cloud service.</span></span>

> <span data-ttu-id="9abc4-207">_ComputeResourceConsolidation.Worker_ 專案屬於 _ComputeResourceConsolidation_ 解決方案，可從 [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/compute-resource-consolidation) 下載。</span><span class="sxs-lookup"><span data-stu-id="9abc4-207">The _ComputeResourceConsolidation.Worker_ project is part of the _ComputeResourceConsolidation_ solution available for download from [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/compute-resource-consolidation).</span></span>

<span data-ttu-id="9abc4-208">`MyWorkerTask1` 和 `MyWorkerTask2` 方法會說明如何在相同的背景工作角色內執行不同的工作。</span><span class="sxs-lookup"><span data-stu-id="9abc4-208">The `MyWorkerTask1` and the `MyWorkerTask2` methods illustrate how to perform different tasks within the same worker role.</span></span> <span data-ttu-id="9abc4-209">下列程式碼會示範 `MyWorkerTask1`。</span><span class="sxs-lookup"><span data-stu-id="9abc4-209">The following code shows `MyWorkerTask1`.</span></span> <span data-ttu-id="9abc4-210">這是簡單的工作，會進入睡眠狀態 30 秒，然後輸出追蹤訊息。</span><span class="sxs-lookup"><span data-stu-id="9abc4-210">This is a simple task that sleeps for 30 seconds and then outputs a trace message.</span></span> <span data-ttu-id="9abc4-211">它會重複此程序，直到這項工作取消為止。</span><span class="sxs-lookup"><span data-stu-id="9abc4-211">It repeats this process until the task is canceled.</span></span> <span data-ttu-id="9abc4-212">`MyWorkerTask2` 中的程式碼很類似。</span><span class="sxs-lookup"><span data-stu-id="9abc4-212">The code in `MyWorkerTask2` is similar.</span></span>

```csharp
// A sample worker role task.
private static async Task MyWorkerTask1(CancellationToken ct)
{
  // Fixed interval to wake up and check for work and/or do work.
  var interval = TimeSpan.FromSeconds(30);

  try
  {
    while (!ct.IsCancellationRequested)
    {
      // Wake up and do some background processing if not canceled.
      // TASK PROCESSING CODE HERE
      Trace.TraceInformation("Doing Worker Task 1 Work");

      // Go back to sleep for a period of time unless asked to cancel.
      // Task.Delay will throw an OperationCanceledException when canceled.
      await Task.Delay(interval, ct);
    }
  }
  catch (OperationCanceledException)
  {
    // Expect this exception to be thrown in normal circumstances or check
    // the cancellation token. If the role instances are shutting down, a
    // cancellation request will be signaled.
    Trace.TraceInformation("Stopping service, cancellation requested");

    // Rethrow the exception.
    throw;
  }
}
```

> <span data-ttu-id="9abc4-213">範例程式碼會顯示背景程序的一般實作。</span><span class="sxs-lookup"><span data-stu-id="9abc4-213">The sample code shows a common implementation of a background process.</span></span> <span data-ttu-id="9abc4-214">在真實世界的應用程式中，您可以遵循這個相同的結構，不同之處在於，您應該將自己的處理邏輯放置在等待取消要求的迴圈之主體中。</span><span class="sxs-lookup"><span data-stu-id="9abc4-214">In a real world application you can follow this same structure, except that you should place your own processing logic in the body of the loop that waits for the cancellation request.</span></span>

<span data-ttu-id="9abc4-215">在背景工作角色將它所使用的資源初始化之後，`Run` 方法會同時啟動兩個工作，如下所示。</span><span class="sxs-lookup"><span data-stu-id="9abc4-215">After the worker role has initialized the resources it uses, the `Run` method starts the two tasks concurrently, as shown here.</span></span>

```csharp
/// <summary>
/// The cancellation token source use to cooperatively cancel running tasks
/// </summary>
private readonly CancellationTokenSource cts = new CancellationTokenSource();

/// <summary>
/// List of running tasks on the role instance
/// </summary>
private readonly List<Task> tasks = new List<Task>();

// RoleEntry Run() is called after OnStart().
// Returning from Run() will cause a role instance to recycle.
public override void Run()
{
  // Start worker tasks and add to the task list
  tasks.Add(MyWorkerTask1(cts.Token));
  tasks.Add(MyWorkerTask2(cts.Token));

  foreach (var worker in this.workerTasks)
  {
      this.tasks.Add(worker);
  }

  Trace.TraceInformation("Worker host tasks started");
  // The assumption is that all tasks should remain running and not return,
  // similar to role entry Run() behavior.
  try
  {
    Task.WaitAll(tasks.ToArray());
  }
  catch (AggregateException ex)
  {
    Trace.TraceError(ex.Message);

    // If any of the inner exceptions in the aggregate exception
    // are not cancellation exceptions then re-throw the exception.
    ex.Handle(innerEx => (innerEx is OperationCanceledException));
  }

  // If there wasn't a cancellation request, stop all tasks and return from Run()
  // An alternative to canceling and returning when a task exits would be to
  // restart the task.
  if (!cts.IsCancellationRequested)
  {
    Trace.TraceInformation("Task returned without cancellation request");
    Stop(TimeSpan.FromMinutes(5));
  }
}
...
```

<span data-ttu-id="9abc4-216">在此範例中，`Run` 方法會等候工作完成。</span><span class="sxs-lookup"><span data-stu-id="9abc4-216">In this example, the `Run` method waits for tasks to be completed.</span></span> <span data-ttu-id="9abc4-217">如果工作已取消，`Run` 方法就會假設角色正在關機，並在完成前等候其餘的工作取消 (在終止前最多會等候五分鐘)。</span><span class="sxs-lookup"><span data-stu-id="9abc4-217">If a task is canceled, the `Run` method assumes that the role is being shut down and waits for the remaining tasks to be canceled before finishing (it waits for a maximum of five minutes before terminating).</span></span> <span data-ttu-id="9abc4-218">如果工作因為預期的例外狀況而失敗，`Run` 方法就會取消工作。</span><span class="sxs-lookup"><span data-stu-id="9abc4-218">If a task fails due to an expected exception, the `Run` method cancels the task.</span></span>

> <span data-ttu-id="9abc4-219">您可以使用 `Run` 方法來實作更完整的監視和例外狀況處理策略，例如重新啟動已失敗的工作，或包含讓角色停止及啟動個別工作的程式碼。</span><span class="sxs-lookup"><span data-stu-id="9abc4-219">You could implement more comprehensive monitoring and exception handling strategies in the `Run` method such as restarting tasks that have failed, or including code that enables the role to stop and start individual tasks.</span></span>

<span data-ttu-id="9abc4-220">當網狀架構控制器關閉角色執行個體 (會從 `OnStop` 方法叫用) 時，會呼叫下列程式碼中所示的 `Stop` 方法。</span><span class="sxs-lookup"><span data-stu-id="9abc4-220">The `Stop` method shown in the following code is called when the fabric controller shuts down the role instance (it's invoked from the `OnStop` method).</span></span> <span data-ttu-id="9abc4-221">程式碼會取消每項工作，依正常程序加以停止。</span><span class="sxs-lookup"><span data-stu-id="9abc4-221">The code stops each task gracefully by canceling it.</span></span> <span data-ttu-id="9abc4-222">如果任何工作花費超過五分鐘才完成，`Stop` 方法中的取消處理就會停止等候，且會終止該角色。</span><span class="sxs-lookup"><span data-stu-id="9abc4-222">If any task takes more than five minutes to complete, the cancellation processing in the `Stop` method ceases waiting and the role is terminated.</span></span>

```csharp
// Stop running tasks and wait for tasks to complete before returning
// unless the timeout expires.
private void Stop(TimeSpan timeout)
{
  Trace.TraceInformation("Stop called. Canceling tasks.");
  // Cancel running tasks.
  cts.Cancel();

  Trace.TraceInformation("Waiting for canceled tasks to finish and return");

  // Wait for all the tasks to complete before returning. Note that the
  // emulator currently allows 30 seconds and Azure allows five
  // minutes for processing to complete.
  try
  {
    Task.WaitAll(tasks.ToArray(), timeout);
  }
  catch (AggregateException ex)
  {
    Trace.TraceError(ex.Message);

    // If any of the inner exceptions in the aggregate exception
    // are not cancellation exceptions then rethrow the exception.
    ex.Handle(innerEx => (innerEx is OperationCanceledException));
  }
}
```

## <a name="related-patterns-and-guidance"></a><span data-ttu-id="9abc4-223">相關的模式和指導方針</span><span class="sxs-lookup"><span data-stu-id="9abc4-223">Related patterns and guidance</span></span>

<span data-ttu-id="9abc4-224">實作此模式時，下列模式和指導方針可能也相關：</span><span class="sxs-lookup"><span data-stu-id="9abc4-224">The following patterns and guidance might also be relevant when implementing this pattern:</span></span>

- <span data-ttu-id="9abc4-225">[自動調整指導方針](https://msdn.microsoft.com/library/dn589774.aspx)。</span><span class="sxs-lookup"><span data-stu-id="9abc4-225">[Autoscaling Guidance](https://msdn.microsoft.com/library/dn589774.aspx).</span></span> <span data-ttu-id="9abc4-226">自動調整可用來根據預期的處理需求啟動及停止服務主控計算資源的執行個體。</span><span class="sxs-lookup"><span data-stu-id="9abc4-226">Autoscaling can be used to start and stop instances of service hosting computational resources, depending on the anticipated demand for processing.</span></span>

- <span data-ttu-id="9abc4-227">[計算分割指導方針](https://msdn.microsoft.com/library/dn589773.aspx)。</span><span class="sxs-lookup"><span data-stu-id="9abc4-227">[Compute Partitioning Guidance](https://msdn.microsoft.com/library/dn589773.aspx).</span></span> <span data-ttu-id="9abc4-228">說明如何以有助於將執行成本降至最低，同時維持服務延展性、效能、可用性及安全性的方式，從而配置雲端服務中的服務和元件。</span><span class="sxs-lookup"><span data-stu-id="9abc4-228">Describes how to allocate the services and components in a cloud service in a way that helps to minimize running costs while maintaining the scalability, performance, availability, and security of the service.</span></span>

- <span data-ttu-id="9abc4-229">此模式包含可下載的[範例應用程式](https://github.com/mspnp/cloud-design-patterns/tree/master/compute-resource-consolidation)。</span><span class="sxs-lookup"><span data-stu-id="9abc4-229">This pattern includes a downloadable [sample application](https://github.com/mspnp/cloud-design-patterns/tree/master/compute-resource-consolidation).</span></span>

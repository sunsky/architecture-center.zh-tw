---
title: 命令與查詢責任隔離 (CQRS) 模式
titleSuffix: Cloud Design Patterns
description: 如果作業讀取的資料來自使用個別介面更新資料的作業，則隔離該作業。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
ms.custom: seodec18
ms.openlocfilehash: 320f6cd51a44b3a6732d8395f0a5e1db8f9f5774
ms.sourcegitcommit: 680c9cef945dff6fee5e66b38e24f07804510fa9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54010370"
---
# <a name="command-and-query-responsibility-segregation-cqrs-pattern"></a><span data-ttu-id="ae3cd-104">命令與查詢責任隔離 (CQRS) 模式</span><span class="sxs-lookup"><span data-stu-id="ae3cd-104">Command and Query Responsibility Segregation (CQRS) pattern</span></span>

[!INCLUDE [header](../_includes/header.md)]

<span data-ttu-id="ae3cd-105">如果作業讀取的資料來自使用個別介面更新資料的作業，則隔離該作業。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-105">Segregate operations that read data from operations that update data by using separate interfaces.</span></span> <span data-ttu-id="ae3cd-106">這可以最大化效能、延展性及安全性。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-106">This can maximize performance, scalability, and security.</span></span> <span data-ttu-id="ae3cd-107">支援系統隨著時間透過更高的彈性而進化，並且防止更新命令造成網域層級的合併衝突。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-107">Supports the evolution of the system over time through higher flexibility, and prevents update commands from causing merge conflicts at the domain level.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="ae3cd-108">內容和問題</span><span class="sxs-lookup"><span data-stu-id="ae3cd-108">Context and problem</span></span>

<span data-ttu-id="ae3cd-109">在傳統資料管理系統中，命令 (資料的更新) 和查詢 (要求資料) 會針對單一資料存放庫中的相同實體集執行。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-109">In traditional data management systems, both commands (updates to the data) and queries (requests for data) are executed against the same set of entities in a single data repository.</span></span> <span data-ttu-id="ae3cd-110">這些實體可以是關聯式資料庫 (例如 SQL Server) 中一個或多個資料表之資料列的子集。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-110">These entities can be a subset of the rows in one or more tables in a relational database such as SQL Server.</span></span>

<span data-ttu-id="ae3cd-111">通常在這些系統中，所有建立、讀取、更新和刪除 (CRUD) 作業會套用至實體的相同表示法。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-111">Typically in these systems, all create, read, update, and delete (CRUD) operations are applied to the same representation of the entity.</span></span> <span data-ttu-id="ae3cd-112">例如，代表客戶的資料傳輸物件 (DTO) 會由資料存取層 (DAL) 從資料存放區擷取，並且在螢幕上顯示。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-112">For example, a data transfer object (DTO) representing a customer is retrieved from the data store by the data access layer (DAL) and displayed on the screen.</span></span> <span data-ttu-id="ae3cd-113">使用者更新 DTO 中的某些欄位 (可能是透過資料繫結)，然後 DTO 會由 DAL 儲存回資料存放區。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-113">A user updates some fields of the DTO (perhaps through data binding) and the DTO is then saved back in the data store by the DAL.</span></span> <span data-ttu-id="ae3cd-114">相同 DTO 用於讀取和寫入作業。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-114">The same DTO is used for both the read and write operations.</span></span> <span data-ttu-id="ae3cd-115">此圖說明傳統 CRUD 架構。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-115">The figure illustrates a traditional CRUD architecture.</span></span>

![傳統 CRUD 架構](./_images/command-and-query-responsibility-segregation-cqrs-tradition-crud.png)

<span data-ttu-id="ae3cd-117">傳統 CRUD 設計只有在將有限的商務邏輯套用至資料作業時運作良好。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-117">Traditional CRUD designs work well when only limited business logic is applied to the data operations.</span></span> <span data-ttu-id="ae3cd-118">開發工具提供的 Scaffold 機制可以非常快速地建立資料存取碼，然後可以視需要自訂。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-118">Scaffold mechanisms provided by development tools can create data access code very quickly, which can then be customized as required.</span></span>

<span data-ttu-id="ae3cd-119">不過，傳統 CRUD 方法有一些缺點：</span><span class="sxs-lookup"><span data-stu-id="ae3cd-119">However, the traditional CRUD approach has some disadvantages:</span></span>

- <span data-ttu-id="ae3cd-120">這通常代表資料的讀取和寫入表示法之間不相符，例如必須正確更新的額外資料行或屬性，即使它們並非作業的必要部分。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-120">It often means that there's a mismatch between the read and write representations of the data, such as additional columns or properties that must be updated correctly even though they aren't required as part of an operation.</span></span>

- <span data-ttu-id="ae3cd-121">當記錄鎖定在共同作業網域中的資料存放區時，有資料爭用的風險，在該網域中多個執行者會在相同資料集上以平行方式運作。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-121">It risks data contention when records are locked in the data store in a collaborative domain, where multiple actors operate in parallel on the same set of data.</span></span> <span data-ttu-id="ae3cd-122">或者更新衝突，這是由使用開放式鎖定時的並行更新所造成。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-122">Or update conflicts caused by concurrent updates when optimistic locking is used.</span></span> <span data-ttu-id="ae3cd-123">這些風險會隨著系統的複雜度和輸送量增加而提升。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-123">These risks increase as the complexity and throughput of the system grows.</span></span> <span data-ttu-id="ae3cd-124">此外，由於資料存放區和資料存取層上的負載，以及擷取資訊所需之查詢的複雜度，傳統方法對於效能會有負面影響。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-124">In addition, the traditional approach can have a negative effect on performance due to load on the data store and data access layer, and the complexity of queries required to retrieve information.</span></span>

- <span data-ttu-id="ae3cd-125">因為每個實體受限於讀取和寫入作業，這些作業可能會在錯誤內容中公開資料，所以讓管理安全性和權限更加複雜。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-125">It can make managing security and permissions more complex because each entity is subject to both read and write operations, which might expose data in the wrong context.</span></span>

## <a name="solution"></a><span data-ttu-id="ae3cd-126">解決方法</span><span class="sxs-lookup"><span data-stu-id="ae3cd-126">Solution</span></span>

<span data-ttu-id="ae3cd-127">命令與查詢責任隔離 (CQRS) 是一種模式，它會使用個別介面將讀取資料 (查詢) 的作業與更新資料 (命令) 的作業隔離。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-127">Command and Query Responsibility Segregation (CQRS) is a pattern that segregates the operations that read data (queries) from the operations that update data (commands) by using separate interfaces.</span></span> <span data-ttu-id="ae3cd-128">這表示用於查詢和更新的資料模型不相同。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-128">This means that the data models used for querying and updates are different.</span></span> <span data-ttu-id="ae3cd-129">然後模型可以隔離，如下圖所示，雖然這不是絕對需求。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-129">The models can then be isolated, as shown in the following figure, although that's not an absolute requirement.</span></span>

![基本 CQRS 架構](./_images/command-and-query-responsibility-segregation-cqrs-basic.png)

<span data-ttu-id="ae3cd-131">相較於以 CRUD 為基礎的系統中使用之單一資料模型，針對以 CQRS 為基礎的系統中之資料使用個別查詢和更新模型，可以簡化設計和實作。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-131">Compared to the single data model used in CRUD-based systems, the use of separate query and update models for the data in CQRS-based systems simplifies design and implementation.</span></span> <span data-ttu-id="ae3cd-132">不過，其中一個缺點是不同於 CRUD 設計，無法使用 Scaffold 機制自動產生 CQRS 程式碼。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-132">However, one disadvantage is that unlike CRUD designs, CQRS code can't automatically be generated using scaffold mechanisms.</span></span>

<span data-ttu-id="ae3cd-133">讀取資料的查詢模型和寫入資料的更新模型可以存取相同實體存放區，可能是透過使用 SQL 檢視或是立即產生投影。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-133">The query model for reading data and the update model for writing data can access the same physical store, perhaps by using SQL views or by generating projections on the fly.</span></span> <span data-ttu-id="ae3cd-134">不過，將資料分隔成不同實體存放區以最大化效能、延展性及安全性很常見，如下一張圖中所示。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-134">However, it's common to separate the data into different physical stores to maximize performance, scalability, and security, as shown in the next figure.</span></span>

![CQRS 架構，具有個別讀取和寫入存放區](./_images/command-and-query-responsibility-segregation-cqrs-separate-stores.png)

<span data-ttu-id="ae3cd-136">讀取存放區可以是寫入存放區的唯讀複本，或者讀取和寫入存放區可以有完全不同的結構。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-136">The read store can be a read-only replica of the write store, or the read and write stores can have a different structure altogether.</span></span> <span data-ttu-id="ae3cd-137">使用讀取存放區的多個唯讀複本會大幅增加查詢效能和應用程式 UI 回應性，特別是在唯讀複本接近應用程式執行個體的分散式案例中。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-137">Using multiple read-only replicas of the read store can greatly increase query performance and application UI responsiveness, especially in distributed scenarios where read-only replicas are located close to the application instances.</span></span> <span data-ttu-id="ae3cd-138">某些資料庫系統 (SQL Server) 提供額外的功能，例如容錯移轉複本，以最大化可用性。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-138">Some database systems (SQL Server) provide additional features such as failover replicas to maximize availability.</span></span>

<span data-ttu-id="ae3cd-139">分隔讀取和寫入存放區也可以讓每個存放區適當地調整以符合負載。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-139">Separation of the read and write stores also allows each to be scaled appropriately to match the load.</span></span> <span data-ttu-id="ae3cd-140">例如，讀取存放區通常會遇到比寫入存放區更高的負載。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-140">For example, read stores typically encounter a much higher load than write stores.</span></span>

<span data-ttu-id="ae3cd-141">當查詢/讀取模型包含反正規化資料 (請參閱[具體化檢視模式](./materialized-view.md))，當讀取應用程式中每個檢視的資料時，或查詢系統中的資料時，效能會最大化。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-141">When the query/read model contains denormalized data (see [Materialized View pattern](./materialized-view.md)), performance is maximized when reading data for each of the views in an application or when querying the data in the system.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="ae3cd-142">問題和考量</span><span class="sxs-lookup"><span data-stu-id="ae3cd-142">Issues and considerations</span></span>

<span data-ttu-id="ae3cd-143">當您決定如何實作此模式時，請考慮下列幾點：</span><span class="sxs-lookup"><span data-stu-id="ae3cd-143">Consider the following points when deciding how to implement this pattern:</span></span>

- <span data-ttu-id="ae3cd-144">將資料存放區分割成適用於讀取和寫入作業的個別實體存放區，可以增加系統的效能和安全性，但是也會增加復原和最終一致性方面的複雜度。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-144">Dividing the data store into separate physical stores for read and write operations can increase the performance and security of a system, but it can add complexity in terms of resiliency and eventual consistency.</span></span> <span data-ttu-id="ae3cd-145">讀取模型存放區必須更新以反映對寫入模型存放區的變更，而且當使用者根據過時讀取資料發出要求時難以偵測，這表示無法完成作業。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-145">The read model store must be updated to reflect changes to the write model store, and it can be difficult to detect when a user has issued a request based on stale read data, which means that the operation can't be completed.</span></span>

    > <span data-ttu-id="ae3cd-146">如需最終一致性的說明，請參閱[資料一致性入門](https://msdn.microsoft.com/library/dn589800.aspx)。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-146">For a description of eventual consistency see the [Data Consistency Primer](https://msdn.microsoft.com/library/dn589800.aspx).</span></span>

- <span data-ttu-id="ae3cd-147">請考慮將 CQRS 套用至它在其中最有價值的系統有限區段。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-147">Consider applying CQRS to limited sections of your system where it will be most valuable.</span></span>

- <span data-ttu-id="ae3cd-148">部署最終一致性的典型方法是使用事件來源搭配 CQRS，讓寫入事件成為由執行命令所驅動的僅附加串流。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-148">A typical approach to deploying eventual consistency is to use event sourcing in conjunction with CQRS so that the write model is an append-only stream of events driven by execution of commands.</span></span> <span data-ttu-id="ae3cd-149">這些事件是用來更新作為讀取模型的具體化檢視。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-149">These events are used to update materialized views that act as the read model.</span></span> <span data-ttu-id="ae3cd-150">如需詳細資訊，請參閱[事件來源和 CQRS](/azure/architecture/patterns/cqrs#event-sourcing-and-cqrs)。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-150">For more information see [Event Sourcing and CQRS](/azure/architecture/patterns/cqrs#event-sourcing-and-cqrs).</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="ae3cd-151">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="ae3cd-151">When to use this pattern</span></span>

<span data-ttu-id="ae3cd-152">在下列情況下使用此模式：</span><span class="sxs-lookup"><span data-stu-id="ae3cd-152">Use this pattern in the following situations:</span></span>

- <span data-ttu-id="ae3cd-153">共同作業網域，其中多個作業在相同資料上以平行方式執行。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-153">Collaborative domains where multiple operations are performed in parallel on the same data.</span></span> <span data-ttu-id="ae3cd-154">CQRS 可讓您以足夠的細微性定義命令，以最小化網域層級的合併衝突 (發生的任何衝突可以由命令合併)，即使是在更新看似相同類型的資料時。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-154">CQRS allows you to define commands with enough granularity to minimize merge conflicts at the domain level (any conflicts that do arise can be merged by the command), even when updating what appears to be the same type of data.</span></span>

- <span data-ttu-id="ae3cd-155">以工作為基礎的使用者介面，使用者會在其中透過一系列步驟或複雜網域模型引導完成複雜程序。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-155">Task-based user interfaces where users are guided through a complex process as a series of steps or with complex domain models.</span></span> <span data-ttu-id="ae3cd-156">另外，對於已熟悉網域驅動設計 (DDD) 技術的小組也很有用。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-156">Also, useful for teams already familiar with domain-driven design (DDD) techniques.</span></span> <span data-ttu-id="ae3cd-157">寫入模型有完整命令處理堆疊，它具有商務邏輯、輸入驗證與商務驗證，以確保在寫入模型中每個彙總的所有項目永遠一致 (相關聯物件的每個叢集視為資料變更的單位)。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-157">The write model has a full command-processing stack with business logic, input validation, and business validation to ensure that everything is always consistent for each of the aggregates (each cluster of associated objects treated as a unit for data changes) in the write model.</span></span> <span data-ttu-id="ae3cd-158">讀取模型沒有商務邏輯或驗證堆疊，只會傳回 DTO 以在檢視模型中使用。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-158">The read model has no business logic or validation stack and just returns a DTO for use in a view model.</span></span> <span data-ttu-id="ae3cd-159">讀取模型最終與寫入模型一致。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-159">The read model is eventually consistent with the write model.</span></span>

- <span data-ttu-id="ae3cd-160">資料讀取效能必須有別於資料寫入效能單獨微調的案例，特別是當讀取/寫入比率非常高，以及需要水平延展時。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-160">Scenarios where performance of data reads must be fine tuned separately from performance of data writes, especially when the read/write ratio is very high, and when horizontal scaling is required.</span></span> <span data-ttu-id="ae3cd-161">例如，許多系統中讀取作業數目比寫入作業數目大上數倍。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-161">For example, in many systems the number of read operations is many times greater than the number of write operations.</span></span> <span data-ttu-id="ae3cd-162">若要容納，請考慮向外延展讀取模型，但是只在一個或少數執行個體上執行寫入模型。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-162">To accommodate this, consider scaling out the read model, but running the write model on only one or a few instances.</span></span> <span data-ttu-id="ae3cd-163">少量的寫入模型執行個體也有助於降低合併衝突的發生。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-163">A small number of write model instances also helps to minimize the occurrence of merge conflicts.</span></span>

- <span data-ttu-id="ae3cd-164">開發人員的其中一個小組可以專注於複雜網域模型 (屬於寫入模式)，而另一個小組可以專注於讀取模型和使用者介面的案例。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-164">Scenarios where one team of developers can focus on the complex domain model that is part of the write model, and another team can focus on the read model and the user interfaces.</span></span>

- <span data-ttu-id="ae3cd-165">系統預期隨著時間進化，可能包含模型的多個版本，或商務規則會定期變更的案例。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-165">Scenarios where the system is expected to evolve over time and might contain multiple versions of the model, or where business rules change regularly.</span></span>

- <span data-ttu-id="ae3cd-166">與其他系統整合，特別是與事件來源結合，其中一個子系統的時態性失敗不應該影響其他子系統的可用性。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-166">Integration with other systems, especially in combination with event sourcing, where the temporal failure of one subsystem shouldn't affect the availability of the others.</span></span>

<span data-ttu-id="ae3cd-167">不建議在下列情況下使用此模式：</span><span class="sxs-lookup"><span data-stu-id="ae3cd-167">This pattern isn't recommended in the following situations:</span></span>

- <span data-ttu-id="ae3cd-168">網域或商務規則都很簡單。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-168">Where the domain or the business rules are simple.</span></span>

- <span data-ttu-id="ae3cd-169">簡單 CRUD 樣式使用者介面及相關資料存取作業已足夠。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-169">Where a simple CRUD-style user interface and the related data access operations are sufficient.</span></span>

- <span data-ttu-id="ae3cd-170">對於在整個系統實作。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-170">For implementation across the whole system.</span></span> <span data-ttu-id="ae3cd-171">在 CQRS 很有用的整體資料管理案例中有特定元件，但是它會在不需要時增加相當大而且不必要的複雜性。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-171">There are specific components of an overall data management scenario where CQRS can be useful, but it can add considerable and unnecessary complexity when it isn't required.</span></span>

## <a name="event-sourcing-and-cqrs"></a><span data-ttu-id="ae3cd-172">事件來源和 CQRS</span><span class="sxs-lookup"><span data-stu-id="ae3cd-172">Event Sourcing and CQRS</span></span>

<span data-ttu-id="ae3cd-173">CQRS 模式通常與事件來源模式搭配使用。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-173">The CQRS pattern is often used along with the Event Sourcing pattern.</span></span> <span data-ttu-id="ae3cd-174">以 CQRS 為基礎的系統會使用個別讀取和寫入資料模型，每個模型都針對相關工作量身打造，實體上通常位於不同存放區中。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-174">CQRS-based systems use separate read and write data models, each tailored to relevant tasks and often located in physically separate stores.</span></span> <span data-ttu-id="ae3cd-175">當搭配[事件來源](./event-sourcing.md)模式使用時，事件的存放區是寫入模型，而且是資訊的官方來源。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-175">When used with the [Event Sourcing pattern](./event-sourcing.md), the store of events is the write model, and is the official source of information.</span></span> <span data-ttu-id="ae3cd-176">以 CQRS 為基礎的系統之讀取模型提供資料的具體化檢視，通常是高度反正規化檢視。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-176">The read model of a CQRS-based system provides materialized views of the data, typically as highly denormalized views.</span></span> <span data-ttu-id="ae3cd-177">這些檢視針對應用程式的介面和顯示需求而量身打造，可協助最大化顯示和查詢效能。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-177">These views are tailored to the interfaces and display requirements of the application, which helps to maximize both display and query performance.</span></span>

<span data-ttu-id="ae3cd-178">使用事件的串流作為寫入存放區，而不是時間點上的實際資料，避免單一彙總上的更新衝突，以及最大化效能和延展性。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-178">Using the stream of events as the write store, rather than the actual data at a point in time, avoids update conflicts on a single aggregate and maximizes performance and scalability.</span></span> <span data-ttu-id="ae3cd-179">事件可用來以非同步方式產生資料的具體化檢視，該檢視是用來填入讀取存放區。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-179">The events can be used to asynchronously generate materialized views of the data that are used to populate the read store.</span></span>

<span data-ttu-id="ae3cd-180">因為事件存放區是資訊的官方來源，所以可以刪除具體化檢視並重新執行所有過去的事件，以在系統進化或讀取模型必須變更時，建立目前狀態的新表示法。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-180">Because the event store is the official source of information, it is possible to delete the materialized views and replay all past events to create a new representation of the current state when the system evolves, or when the read model must change.</span></span> <span data-ttu-id="ae3cd-181">具體化檢視在資料的耐久唯讀快取期間都有效。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-181">The materialized views are in effect a durable read-only cache of the data.</span></span>

<span data-ttu-id="ae3cd-182">搭配事件來源模式使用 CQRS 時，請考慮下列各項：</span><span class="sxs-lookup"><span data-stu-id="ae3cd-182">When using CQRS combined with the Event Sourcing pattern, consider the following:</span></span>

- <span data-ttu-id="ae3cd-183">如同其寫入和讀取存放區是獨立的任何系統，以此模式為基礎的系統最終只會一致。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-183">As with any system where the write and read stores are separate, systems based on this pattern are only eventually consistent.</span></span> <span data-ttu-id="ae3cd-184">產生之事件和更新之資料存放區之間會有一些延遲。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-184">There will be some delay between the event being generated and the data store being updated.</span></span>

- <span data-ttu-id="ae3cd-185">模式會增加複雜性，因為必須建立程式碼才能起始和處理事件，並且組合或更新查詢或讀取模型所需的適當檢視或物件。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-185">The pattern adds complexity because code must be created to initiate and handle events, and assemble or update the appropriate views or objects required by queries or a read model.</span></span> <span data-ttu-id="ae3cd-186">與事件來源模式搭配使用時的 CQRS 模式複雜性，會讓實作成功更加困難，並且需要不同的方法來設計系統。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-186">The complexity of the CQRS pattern when used with the Event Sourcing pattern can make a successful implementation more difficult, and requires a different approach to designing systems.</span></span> <span data-ttu-id="ae3cd-187">不過，事件來源可以讓建立網域的模型更加容易，重建檢視或建立新的檢視也更加容易，因為會保留資料變更的意圖。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-187">However, event sourcing can make it easier to model the domain, and makes it easier to rebuild views or create new ones because the intent of the changes in the data is preserved.</span></span>

- <span data-ttu-id="ae3cd-188">藉由重新執行及處理特定實體或實體集合的事件，產生具體化檢視以在資料的讀取模型或投影中使用，需要大量處理時間和資源使用量。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-188">Generating materialized views for use in the read model or projections of the data by replaying and handling the events for specific entities or collections of entities can require significant processing time and resource usage.</span></span> <span data-ttu-id="ae3cd-189">如果需要長時間的值總和或分析更是如此，因為可能需要檢查所有相關聯的事件。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-189">This is especially true if it requires summation or analysis of values over long periods, because all the associated events might need to be examined.</span></span> <span data-ttu-id="ae3cd-190">藉由在排程間隔實作資料的快照集來解決這個情形，例如已發生之特定動作的總數，或實體的目前狀態。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-190">Resolve this by implementing snapshots of the data at scheduled intervals, such as a total count of the number of a specific action that have occurred, or the current state of an entity.</span></span>

## <a name="example"></a><span data-ttu-id="ae3cd-191">範例</span><span class="sxs-lookup"><span data-stu-id="ae3cd-191">Example</span></span>

<span data-ttu-id="ae3cd-192">下列程式碼顯示 CQRS 實作範例的一些擷取，該實作針對讀取和寫入模型使用不同的定義。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-192">The following code shows some extracts from an example of a CQRS implementation that uses different definitions for the read and the write models.</span></span> <span data-ttu-id="ae3cd-193">模型介面不會聽寫基礎資料存放區中的任何功能，而且因為這些介面是分隔的，所以它們可以個別進化及微調。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-193">The model interfaces don't dictate any features of the underlying data stores, and they can evolve and be fine-tuned independently because these interfaces are separated.</span></span>

<span data-ttu-id="ae3cd-194">下列程式碼顯示讀取模型定義。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-194">The following code shows the read model definition.</span></span>

```csharp
// Query interface
namespace ReadModel
{
  public interface ProductsDao
  {
    ProductDisplay FindById(int productId);
    ICollection<ProductDisplay> FindByName(string name);
    ICollection<ProductInventory> FindOutOfStockProducts();
    ICollection<ProductDisplay> FindRelatedProducts(int productId);
  }

  public class ProductDisplay
  {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Description { get; set; }
    public decimal UnitPrice { get; set; }
    public bool IsOutOfStock { get; set; }
    public double UserRating { get; set; }
  }

  public class ProductInventory
  {
    public int Id { get; set; }
    public string Name { get; set; }
    public int CurrentStock { get; set; }
  }
}
```

<span data-ttu-id="ae3cd-195">系統允許使用者為產品評分。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-195">The system allows users to rate products.</span></span> <span data-ttu-id="ae3cd-196">應用程式程式碼使用在下列程式碼中顯示的 `RateProduct` 命令來完成這項操作。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-196">The application code does this using the `RateProduct` command shown in the following code.</span></span>

```csharp
public interface ICommand
{
  Guid Id { get; }
}

public class RateProduct : ICommand
{
  public RateProduct()
  {
    this.Id = Guid.NewGuid();
  }
  public Guid Id { get; set; }
  public int ProductId { get; set; }
  public int Rating { get; set; }
  public int UserId {get; set; }
}
```

<span data-ttu-id="ae3cd-197">系統會使用 `ProductsCommandHandler` 類別來處理應用程式所傳送的命令。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-197">The system uses the `ProductsCommandHandler` class to handle commands sent by the application.</span></span> <span data-ttu-id="ae3cd-198">用戶端通常會透過傳訊系統 (例如佇列) 將命令傳送至網域。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-198">Clients typically send commands to the domain through a messaging system such as a queue.</span></span> <span data-ttu-id="ae3cd-199">命令處理常式會接受這些命令，並且叫用網域介面的方法。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-199">The command handler accepts these commands and invokes methods of the domain interface.</span></span> <span data-ttu-id="ae3cd-200">每個命令之細微性的設計目的是減少要求衝突的機會。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-200">The granularity of each command is designed to reduce the chance of conflicting requests.</span></span> <span data-ttu-id="ae3cd-201">下列程式碼示範 `ProductsCommandHandler` 類別的大綱。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-201">The following code shows an outline of the `ProductsCommandHandler` class.</span></span>

```csharp
public class ProductsCommandHandler :
    ICommandHandler<AddNewProduct>,
    ICommandHandler<RateProduct>,
    ICommandHandler<AddToInventory>,
    ICommandHandler<ConfirmItemShipped>,
    ICommandHandler<UpdateStockFromInventoryRecount>
{
  private readonly IRepository<Product> repository;

  public ProductsCommandHandler (IRepository<Product> repository)
  {
    this.repository = repository;
  }

  void Handle (AddNewProduct command)
  {
    ...
  }

  void Handle (RateProduct command)
  {
    var product = repository.Find(command.ProductId);
    if (product != null)
    {
      product.RateProduct(command.UserId, command.Rating);
      repository.Save(product);
    }
  }

  void Handle (AddToInventory command)
  {
    ...
  }

  void Handle (ConfirmItemsShipped command)
  {
    ...
  }

  void Handle (UpdateStockFromInventoryRecount command)
  {
    ...
  }
}
```

<span data-ttu-id="ae3cd-202">下列程式碼示範寫入模型的 `IProductsDomain` 介面。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-202">The following code shows the `IProductsDomain` interface from the write model.</span></span>

```csharp
public interface IProductsDomain
{
  void AddNewProduct(int id, string name, string description, decimal price);
  void RateProduct(int userId, int rating);
  void AddToInventory(int productId, int quantity);
  void ConfirmItemsShipped(int productId, int quantity);
  void UpdateStockFromInventoryRecount(int productId, int updatedQuantity);
}
```

<span data-ttu-id="ae3cd-203">同時也請注意 `IProductsDomain` 介面如何包含在網域中具有意義的方法。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-203">Also notice how the `IProductsDomain` interface contains methods that have a meaning in the domain.</span></span> <span data-ttu-id="ae3cd-204">通常，在 CRUD 環境中這些方法會具有泛型名稱，例如 `Save` 或 `Update`，而且有 DTO 作為唯一的引數。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-204">Typically, in a CRUD environment these methods would have generic names such as `Save` or `Update`, and have a DTO as the only argument.</span></span> <span data-ttu-id="ae3cd-205">CQRS 方法可以設計為符合此組織商務和庫存管理系統的需求。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-205">The CQRS approach can be designed to meet the needs of this organization's business and inventory management systems.</span></span>

## <a name="related-patterns-and-guidance"></a><span data-ttu-id="ae3cd-206">相關的模式和指導方針</span><span class="sxs-lookup"><span data-stu-id="ae3cd-206">Related patterns and guidance</span></span>

<span data-ttu-id="ae3cd-207">下列模式和指導方針在實作此模式時很有用：</span><span class="sxs-lookup"><span data-stu-id="ae3cd-207">The following patterns and guidance are useful when implementing this pattern:</span></span>

- <span data-ttu-id="ae3cd-208">如需 CQRS 與其他架構樣式的比較，請參閱[架構樣式](/azure/architecture/guide/architecture-styles/)和 [CQRS 架構樣式](/azure/architecture/guide/architecture-styles/cqrs)。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-208">For a comparison of CQRS with other architectural styles, see [Architecture styles](/azure/architecture/guide/architecture-styles/) and [CQRS architecture style](/azure/architecture/guide/architecture-styles/cqrs).</span></span>

- <span data-ttu-id="ae3cd-209">[資料一致性入門](https://msdn.microsoft.com/library/dn589800.aspx)。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-209">[Data Consistency Primer](https://msdn.microsoft.com/library/dn589800.aspx).</span></span> <span data-ttu-id="ae3cd-210">說明在使用 CQRS 模式時，由於讀取和寫入資料存放區之間的最終一致性，通常會遇到的問題，以及如何解決這些問題。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-210">Explains the issues that are typically encountered due to eventual consistency between the read and write data stores when using the CQRS pattern, and how these issues can be resolved.</span></span>

- <span data-ttu-id="ae3cd-211">[資料分割指引](https://msdn.microsoft.com/library/dn589795.aspx)。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-211">[Data Partitioning Guidance](https://msdn.microsoft.com/library/dn589795.aspx).</span></span> <span data-ttu-id="ae3cd-212">描述 CQRS 模式中使用的讀取和寫入資料存放區如何分割成資料分割，以便個別管理及存取，來改善延展性、減少爭用以及最佳化效能。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-212">Describes how the read and write data stores used in the CQRS pattern can be divided into partitions that can be managed and accessed separately to improve scalability, reduce contention, and optimize performance.</span></span>

- <span data-ttu-id="ae3cd-213">[事件來源模式](./event-sourcing.md)。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-213">[Event Sourcing pattern](./event-sourcing.md).</span></span> <span data-ttu-id="ae3cd-214">更詳細地描述事件來源如何與 CQRS 模式搭配使用，在改善效能、延展性及回應能力的同時，簡化複雜網域中的工作。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-214">Describes in more detail how Event Sourcing can be used with the CQRS pattern to simplify tasks in complex domains while improving performance, scalability, and responsiveness.</span></span> <span data-ttu-id="ae3cd-215">以及如何在維護完整稽核記錄和歷程記錄 (可以啟用補償動作) 的同時，提供交易資料的一致性。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-215">As well as how to provide consistency for transactional data while maintaining full audit trails and history that can enable compensating actions.</span></span>

- <span data-ttu-id="ae3cd-216">[具體化檢視模式](./materialized-view.md)。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-216">[Materialized View pattern](./materialized-view.md).</span></span> <span data-ttu-id="ae3cd-217">CQRS 實作的讀取模型可以包含寫入模型資料的具體化檢視，或者讀取模型可以用來產生具體化檢視。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-217">The read model of a CQRS implementation can contain materialized views of the write model data, or the read model can be used to generate materialized views.</span></span>

- <span data-ttu-id="ae3cd-218">模式與做法指南 [CQRS 旅程](https://aka.ms/cqrs)。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-218">The patterns & practices guide [CQRS Journey](https://aka.ms/cqrs).</span></span> <span data-ttu-id="ae3cd-219">其中，[簡介命令查詢責任隔離模式](https://msdn.microsoft.com/library/jj591573.aspx)會探索模式及其適用時機，而[結語：學到的課程](https://msdn.microsoft.com/library/jj591568.aspx)可協助您了解使用此模式時出現的一些問題。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-219">In particular, [Introducing the Command Query Responsibility Segregation pattern](https://msdn.microsoft.com/library/jj591573.aspx) explores the pattern and when it's useful, and [Epilogue: Lessons Learned](https://msdn.microsoft.com/library/jj591568.aspx) helps you understand some of the issues that come up when using this pattern.</span></span>

- <span data-ttu-id="ae3cd-220">文章 [CQRS by Martin Fowler](https://martinfowler.com/bliki/CQRS.html)，其中說明模式的基本概念以及其他有用資源的連結。</span><span class="sxs-lookup"><span data-stu-id="ae3cd-220">The post [CQRS by Martin Fowler](https://martinfowler.com/bliki/CQRS.html), which explains the basics of the pattern and links to other useful resources.</span></span>

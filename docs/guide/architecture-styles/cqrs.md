---
title: CQRS 架構樣式
titleSuffix: Azure Application Architecture Guide
description: 描述CQRS 架構的優點、挑戰以及最佳做法。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 35f37afa60f943f410f1fbd46c789c0b2c66e36e
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54481434"
---
# <a name="cqrs-architecture-style"></a><span data-ttu-id="4b83c-103">CQRS 架構樣式</span><span class="sxs-lookup"><span data-stu-id="4b83c-103">CQRS architecture style</span></span>

<span data-ttu-id="4b83c-104">命令和查詢責任隔離 (CQRS) 是可從寫入作業分隔讀取作業的架構樣式。</span><span class="sxs-lookup"><span data-stu-id="4b83c-104">Command and Query Responsibility Segregation (CQRS) is an architecture style that separates read operations from write operations.</span></span>

![CQRS 架構樣式的邏輯圖](./images/cqrs-logical.svg)

<span data-ttu-id="4b83c-106">在傳統架構中，相同資料模型是用來查詢和更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="4b83c-106">In traditional architectures, the same data model is used to query and update a database.</span></span> <span data-ttu-id="4b83c-107">那很簡單，而且對基本 CRUD 作業可運作良好。</span><span class="sxs-lookup"><span data-stu-id="4b83c-107">That's simple and works well for basic CRUD operations.</span></span> <span data-ttu-id="4b83c-108">不過，在更複雜的應用程式中，此方法可能會變得不便。</span><span class="sxs-lookup"><span data-stu-id="4b83c-108">In more complex applications, however, this approach can become unwieldy.</span></span> <span data-ttu-id="4b83c-109">比方說，在讀取端，應用程式可能會執行許多不同的查詢，傳回具有不同圖形的資料傳輸物件 (DTO)。</span><span class="sxs-lookup"><span data-stu-id="4b83c-109">For example, on the read side, the application may perform many different queries, returning data transfer objects (DTOs) with different shapes.</span></span> <span data-ttu-id="4b83c-110">物件對應可能會變得複雜。</span><span class="sxs-lookup"><span data-stu-id="4b83c-110">Object mapping can become complicated.</span></span> <span data-ttu-id="4b83c-111">在寫入端，模型可能會實作複雜的驗證和商務邏輯。</span><span class="sxs-lookup"><span data-stu-id="4b83c-111">On the write side, the model may implement complex validation and business logic.</span></span> <span data-ttu-id="4b83c-112">如此一來，您可能會獲得執行太多作業、過於複雜的模型。</span><span class="sxs-lookup"><span data-stu-id="4b83c-112">As a result, you can end up with an overly complex model that does too much.</span></span>

<span data-ttu-id="4b83c-113">另一個潛在的問題是，讀取和寫入工作負載通常是不對稱的，具有非常不同的效能和調整需求。</span><span class="sxs-lookup"><span data-stu-id="4b83c-113">Another potential problem is that read and write workloads are often asymmetrical, with very different performance and scale requirements.</span></span>

<span data-ttu-id="4b83c-114">CQRS 可解決這些問題，方法是將讀取和寫入分隔至個別的模型，使用 **命令** 來更新資料，以及使用**查詢**來讀取資料。</span><span class="sxs-lookup"><span data-stu-id="4b83c-114">CQRS addresses these problems by separating reads and writes into separate models, using **commands** to update data, and **queries** to read data.</span></span>

- <span data-ttu-id="4b83c-115">命令應該以工作為基礎，而不是以資料為中心。</span><span class="sxs-lookup"><span data-stu-id="4b83c-115">Commands should be task based, rather than data centric.</span></span> <span data-ttu-id="4b83c-116">(「預訂旅館房間」而不是「將保留狀態設為已保留」。) 命令可能會放在佇列上以進行非同步處理，而不是同步處理。</span><span class="sxs-lookup"><span data-stu-id="4b83c-116">("Book hotel room," not "set ReservationStatus to Reserved.") Commands may be placed on a queue for asynchronous processing, rather than being processed synchronously.</span></span>

- <span data-ttu-id="4b83c-117">查詢永遠不會修改資料庫。</span><span class="sxs-lookup"><span data-stu-id="4b83c-117">Queries never modify the database.</span></span> <span data-ttu-id="4b83c-118">查詢會傳回不會封裝任何網域知識的 DTO。</span><span class="sxs-lookup"><span data-stu-id="4b83c-118">A query returns a DTO that does not encapsulate any domain knowledge.</span></span>

<span data-ttu-id="4b83c-119">如需更好的隔離，您可以實際上將讀取資料與寫入資料分隔。</span><span class="sxs-lookup"><span data-stu-id="4b83c-119">For greater isolation, you can physically separate the read data from the write data.</span></span> <span data-ttu-id="4b83c-120">在該情況下，讀取資料庫可以使用自己已對查詢最佳化的資料結構描述。</span><span class="sxs-lookup"><span data-stu-id="4b83c-120">In that case, the read database can use its own data schema that is optimized for queries.</span></span> <span data-ttu-id="4b83c-121">例如，它可以儲存資料的[具體化檢視][materialized-view]，以避免複雜的聯結或複雜的 O/RM 對應。</span><span class="sxs-lookup"><span data-stu-id="4b83c-121">For example, it can store a [materialized view][materialized-view] of the data, in order to avoid complex joins or complex O/RM mappings.</span></span> <span data-ttu-id="4b83c-122">它甚至可能會使用不同類型的資料存放區。</span><span class="sxs-lookup"><span data-stu-id="4b83c-122">It might even use a different type of data store.</span></span> <span data-ttu-id="4b83c-123">例如，寫入資料庫可能為關聯式，而讀取資料庫為文件資料庫。</span><span class="sxs-lookup"><span data-stu-id="4b83c-123">For example, the write database might be relational, while the read database is a document database.</span></span>

<span data-ttu-id="4b83c-124">如果使用個別的讀取和寫入資料庫，它們必須保持同步。通常其實現方式為每當更新資料庫時便由寫入模型發佈事件。</span><span class="sxs-lookup"><span data-stu-id="4b83c-124">If separate read and write databases are used, they must be kept in sync. Typically this is accomplished by having the write model publish an event whenever it updates the database.</span></span> <span data-ttu-id="4b83c-125">更新資料庫和發佈事件必須在單一交易中發生。</span><span class="sxs-lookup"><span data-stu-id="4b83c-125">Updating the database and publishing the event must occur in a single transaction.</span></span>

<span data-ttu-id="4b83c-126">CQRS 的某些實作使用[事件來源模式][event-sourcing]。</span><span class="sxs-lookup"><span data-stu-id="4b83c-126">Some implementations of CQRS use the [Event Sourcing pattern][event-sourcing].</span></span> <span data-ttu-id="4b83c-127">利用此模式，應用程式狀態會儲存為一系列的事件。</span><span class="sxs-lookup"><span data-stu-id="4b83c-127">With this pattern, application state is stored as a sequence of events.</span></span> <span data-ttu-id="4b83c-128">每個事件代表對資料的一組變更。</span><span class="sxs-lookup"><span data-stu-id="4b83c-128">Each event represents a set of changes to the data.</span></span> <span data-ttu-id="4b83c-129">目前狀態則是透過重新執行事件來建構。</span><span class="sxs-lookup"><span data-stu-id="4b83c-129">The current state is constructed by replaying the events.</span></span> <span data-ttu-id="4b83c-130">在 CQRS 內容中，事件來源的其中一個優點是，相同事件可以用來通知其他元件 &mdash; 特別是通知讀取模型。</span><span class="sxs-lookup"><span data-stu-id="4b83c-130">In a CQRS context, one benefit of Event Sourcing is that the same events can be used to notify other components &mdash; in particular, to notify the read model.</span></span> <span data-ttu-id="4b83c-131">讀取模型使用事件來建立目前狀態的快照集，這對查詢更有效率。</span><span class="sxs-lookup"><span data-stu-id="4b83c-131">The read model uses the events to create a snapshot of the current state, which is more efficient for queries.</span></span> <span data-ttu-id="4b83c-132">不過，事件來源會增加設計的複雜性。</span><span class="sxs-lookup"><span data-stu-id="4b83c-132">However, Event Sourcing adds complexity to the design.</span></span>

![CQRS 事件](./images/cqrs-events.svg)

## <a name="when-to-use-this-architecture"></a><span data-ttu-id="4b83c-134">使用此架構的時機</span><span class="sxs-lookup"><span data-stu-id="4b83c-134">When to use this architecture</span></span>

<span data-ttu-id="4b83c-135">考慮將 CQRS 用於許多使用者存取相同資料的共同作業網域，特別是當讀取和寫入的工作負載是不對稱時。</span><span class="sxs-lookup"><span data-stu-id="4b83c-135">Consider CQRS for collaborative domains where many users access the same data, especially when the read and write workloads are asymmetrical.</span></span>

<span data-ttu-id="4b83c-136">CQRS 不是可適用整個系統的最上層結構。</span><span class="sxs-lookup"><span data-stu-id="4b83c-136">CQRS is not a top-level architecture that applies to an entire system.</span></span> <span data-ttu-id="4b83c-137">僅將 CQRS 套用至將讀取和寫入分隔有明顯價值的子系統中。</span><span class="sxs-lookup"><span data-stu-id="4b83c-137">Apply CQRS only to those subsystems where there is clear value in separating reads and writes.</span></span> <span data-ttu-id="4b83c-138">否則，您便是在產生額外的複雜性，但沒有任何好處。</span><span class="sxs-lookup"><span data-stu-id="4b83c-138">Otherwise, you are creating additional complexity for no benefit.</span></span>

## <a name="benefits"></a><span data-ttu-id="4b83c-139">優點</span><span class="sxs-lookup"><span data-stu-id="4b83c-139">Benefits</span></span>

- <span data-ttu-id="4b83c-140">**獨立調整**。</span><span class="sxs-lookup"><span data-stu-id="4b83c-140">**Independently scaling**.</span></span> <span data-ttu-id="4b83c-141">CQRS 允許讀取和寫入工作負載，以獨立調整，並可能導致較少的鎖定爭用。</span><span class="sxs-lookup"><span data-stu-id="4b83c-141">CQRS allows the read and write workloads to scale independently, and may result in fewer lock contentions.</span></span>
- <span data-ttu-id="4b83c-142">**最佳化資料結構描述**。</span><span class="sxs-lookup"><span data-stu-id="4b83c-142">**Optimized data schemas**.</span></span> <span data-ttu-id="4b83c-143">讀取端可以使用已針對查詢最佳化的結構描述，同時寫入端可使用已針對更新最佳化的結構描述。</span><span class="sxs-lookup"><span data-stu-id="4b83c-143">The read side can use a schema that is optimized for queries, while the write side uses a schema that is optimized for updates.</span></span>
- <span data-ttu-id="4b83c-144">**安全性**。</span><span class="sxs-lookup"><span data-stu-id="4b83c-144">**Security**.</span></span> <span data-ttu-id="4b83c-145">要確保只有適當的網域實體會對資料執行寫入更輕鬆。</span><span class="sxs-lookup"><span data-stu-id="4b83c-145">It's easier to ensure that only the right domain entities are performing writes on the data.</span></span>
- <span data-ttu-id="4b83c-146">**考量分隔**。</span><span class="sxs-lookup"><span data-stu-id="4b83c-146">**Separation of concerns**.</span></span> <span data-ttu-id="4b83c-147">分隔讀取和寫入端，會導致更容易維護且更具彈性的模型。</span><span class="sxs-lookup"><span data-stu-id="4b83c-147">Segregating the read and write sides can result in models that are more maintainable and flexible.</span></span> <span data-ttu-id="4b83c-148">大部分的複雜商務邏輯進入寫入模型。</span><span class="sxs-lookup"><span data-stu-id="4b83c-148">Most of the complex business logic goes into the write model.</span></span> <span data-ttu-id="4b83c-149">讀取模型可以是相當簡單。</span><span class="sxs-lookup"><span data-stu-id="4b83c-149">The read model can be relatively simple.</span></span>
- <span data-ttu-id="4b83c-150">**較簡單的查詢**。</span><span class="sxs-lookup"><span data-stu-id="4b83c-150">**Simpler queries**.</span></span> <span data-ttu-id="4b83c-151">藉由將具體化檢視儲存在讀取資料庫，查詢時應用程式可以避免複雜的聯結。</span><span class="sxs-lookup"><span data-stu-id="4b83c-151">By storing a materialized view in the read database, the application can avoid complex joins when querying.</span></span>

## <a name="challenges"></a><span data-ttu-id="4b83c-152">挑戰</span><span class="sxs-lookup"><span data-stu-id="4b83c-152">Challenges</span></span>

- <span data-ttu-id="4b83c-153">**複雜度**。</span><span class="sxs-lookup"><span data-stu-id="4b83c-153">**Complexity**.</span></span> <span data-ttu-id="4b83c-154">CQRS 的基本概念很簡單。</span><span class="sxs-lookup"><span data-stu-id="4b83c-154">The basic idea of CQRS is simple.</span></span> <span data-ttu-id="4b83c-155">但是，它可能會導致更複雜的應用程式設計，特別是如果它們包含事件來源模式。</span><span class="sxs-lookup"><span data-stu-id="4b83c-155">But it can lead to a more complex application design, especially if they include the Event Sourcing pattern.</span></span>

- <span data-ttu-id="4b83c-156">**傳訊**。</span><span class="sxs-lookup"><span data-stu-id="4b83c-156">**Messaging**.</span></span> <span data-ttu-id="4b83c-157">雖然 CQRS 不需要傳訊，通常會使用傳訊來處理命令訊息並發佈更新事件。</span><span class="sxs-lookup"><span data-stu-id="4b83c-157">Although CQRS does not require messaging, it's common to use messaging to process commands and publish update events.</span></span> <span data-ttu-id="4b83c-158">在該情況下，應用程式必須處理訊息失敗或重複的訊息。</span><span class="sxs-lookup"><span data-stu-id="4b83c-158">In that case, the application must handle message failures or duplicate messages.</span></span>

- <span data-ttu-id="4b83c-159">**最終一致性**。</span><span class="sxs-lookup"><span data-stu-id="4b83c-159">**Eventual consistency**.</span></span> <span data-ttu-id="4b83c-160">如果您將讀取和寫入資料庫分隔，讀取資料可能會過時。</span><span class="sxs-lookup"><span data-stu-id="4b83c-160">If you separate the read and write databases, the read data may be stale.</span></span>

## <a name="best-practices"></a><span data-ttu-id="4b83c-161">最佳作法</span><span class="sxs-lookup"><span data-stu-id="4b83c-161">Best practices</span></span>

- <span data-ttu-id="4b83c-162">如需實作 CQRS 的詳細資訊，請參閱 [CQRS 模式][cqrs-pattern]。</span><span class="sxs-lookup"><span data-stu-id="4b83c-162">For more information about implementing CQRS, see the [CQRS pattern][cqrs-pattern].</span></span>

- <span data-ttu-id="4b83c-163">考慮使用[事件來源][event-sourcing]模式以避免發生更新衝突。</span><span class="sxs-lookup"><span data-stu-id="4b83c-163">Consider using the [Event Sourcing][event-sourcing] pattern to avoid update conflicts.</span></span>

- <span data-ttu-id="4b83c-164">考慮對讀取模型使用[具體化檢視模式][materialized-view]，用來將查詢的結構描述最佳化。</span><span class="sxs-lookup"><span data-stu-id="4b83c-164">Consider using the [Materialized View pattern][materialized-view] for the read model, to optimize the schema for queries.</span></span>

## <a name="cqrs-in-microservices"></a><span data-ttu-id="4b83c-165">微服務中的 CQRS</span><span class="sxs-lookup"><span data-stu-id="4b83c-165">CQRS in microservices</span></span>

<span data-ttu-id="4b83c-166">在[微服務架構][microservices]中，CQRS 特別有用。</span><span class="sxs-lookup"><span data-stu-id="4b83c-166">CQRS can be especially useful in a [microservices architecture][microservices].</span></span> <span data-ttu-id="4b83c-167">微服務的其中一個準則是服務無法直接存取另一個服務的資料存放區。</span><span class="sxs-lookup"><span data-stu-id="4b83c-167">One of the principles of microservices is that a service cannot directly access another service's data store.</span></span>

![錯誤的微服務方法圖](./images/cqrs-microservices-wrong.png)

<span data-ttu-id="4b83c-169">在下列表中，服務 A 會寫入資料存放區，而服務 B 會保留資料的具體化檢視。</span><span class="sxs-lookup"><span data-stu-id="4b83c-169">In the following diagram, Service A writes to a data store, and Service B keeps a materialized view of the data.</span></span> <span data-ttu-id="4b83c-170">服務 A 會在寫入資料存放區時發佈事件。</span><span class="sxs-lookup"><span data-stu-id="4b83c-170">Service A publishes an event whenever it writes to the data store.</span></span> <span data-ttu-id="4b83c-171">服務 B 訂閱此事件。</span><span class="sxs-lookup"><span data-stu-id="4b83c-171">Service B subscribes to the event.</span></span>

![正確的微服務方法圖](./images/cqrs-microservices-right.png)

<!-- links -->

[cqrs-pattern]: ../../patterns/cqrs.md
[event-sourcing]: ../../patterns/event-sourcing.md
[materialized-view]: ../../patterns/materialized-view.md
[microservices]: ./microservices.md

---
title: "交易資料"
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: b7fdbb403d2a438ebc59e40ef58ed8067489dddc
ms.sourcegitcommit: 943e671a8d522cef5ddc8c6e04848134b03c2de4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2018
---
# <a name="transactional-data"></a><span data-ttu-id="19c19-102">交易資料</span><span class="sxs-lookup"><span data-stu-id="19c19-102">Transactional data</span></span>

<span data-ttu-id="19c19-103">交易資料是追蹤組織活動的相關互動所產生的資訊。</span><span class="sxs-lookup"><span data-stu-id="19c19-103">Transactional data is information that tracks the interactions related to an organization's activities.</span></span> <span data-ttu-id="19c19-104">這些互動通常是商業交易，例如，客戶的付款、對供應商的付款、庫存的產品、接到的訂單，或是交付的服務。</span><span class="sxs-lookup"><span data-stu-id="19c19-104">These interactions are typically business transactions, such as payments received from customers, payments made to suppliers, products moving through inventory, orders taken, or services delivered.</span></span> <span data-ttu-id="19c19-105">交易事件代表交易本身，通常會包含時間維度、一些數值，以及對其他資料的參考。</span><span class="sxs-lookup"><span data-stu-id="19c19-105">Transactional events, which represent the transactions themselves, typically contain a time dimension, some numerical values, and references to other data.</span></span> 

<span data-ttu-id="19c19-106">交易通常必須是*不可部分完成*且*一致*的。</span><span class="sxs-lookup"><span data-stu-id="19c19-106">Transactions typically need to be *atomic* and *consistent*.</span></span> <span data-ttu-id="19c19-107">不可部分完成性表示整個交易無論成功或失敗都一律是工作單位整體的，且絕不會處於半完成的狀態。</span><span class="sxs-lookup"><span data-stu-id="19c19-107">Atomicity means that an entire transaction always succeeds or fails as one unit of work, and is never left in a half-completed state.</span></span> <span data-ttu-id="19c19-108">如果無法完成交易，則資料庫系統必須復原該交易中已完成的任何步驟。</span><span class="sxs-lookup"><span data-stu-id="19c19-108">If a transaction cannot be completed, the database system must roll back any steps that were already done as part of that transaction.</span></span> <span data-ttu-id="19c19-109">在傳統的 RDBMS 中，只要交易無法完成，就會自動執行此復原。</span><span class="sxs-lookup"><span data-stu-id="19c19-109">In a traditional RDBMS, this rollback happens automatically if a transaction cannot be completed.</span></span> <span data-ttu-id="19c19-110">一致性表示交易一律會讓資料保持在有效狀態。</span><span class="sxs-lookup"><span data-stu-id="19c19-110">Consistency means that transactions always leave the data in a valid state.</span></span> <span data-ttu-id="19c19-111">(以上關於不可部分完成性與一致性的說明只是口語化的。</span><span class="sxs-lookup"><span data-stu-id="19c19-111">(These are very informal descriptions of atomicity and consistency.</span></span> <span data-ttu-id="19c19-112">這些屬性另有更為正式的定義，例如 [ACID](https://en.wikipedia.org/wiki/ACID)。)</span><span class="sxs-lookup"><span data-stu-id="19c19-112">There are more formal definitions of these properties, such as [ACID](https://en.wikipedia.org/wiki/ACID).)</span></span>

<span data-ttu-id="19c19-113">交易資料庫可對使用各種不同鎖定策略 (例如封閉式鎖定) 的交易支援強式一致性，以確保企業內容中的所有資料對於所有使用者和程序而言都絕對是一致的。</span><span class="sxs-lookup"><span data-stu-id="19c19-113">Transactional databases can support strong consistency for transactions using various locking strategies, such as pessimistic locking, to ensure that all data is strongly consistent within the context of the enterprise, for all users and processes.</span></span> 

<span data-ttu-id="19c19-114">使用交易資料最常見的部署架構，是 3 層式架構中的資料存放區層。</span><span class="sxs-lookup"><span data-stu-id="19c19-114">The most common deployment architecture that uses transactional data is the data store tier in a 3-tier architecture.</span></span> <span data-ttu-id="19c19-115">3 層式架構通常由展示層、商務邏輯層和資料存放區層所組成。</span><span class="sxs-lookup"><span data-stu-id="19c19-115">A 3-tier architecture typically consists of a presentation tier, business logic tier, and data store tier.</span></span> <span data-ttu-id="19c19-116">相關部署架構為[多層式](/azure/architecture/guide/architecture-styles/n-tier)架構，此類架構可能會以多個中介層來處理商務邏輯。</span><span class="sxs-lookup"><span data-stu-id="19c19-116">A related deployment architecture is the [N-tier](/azure/architecture/guide/architecture-styles/n-tier) architecture, which may have multiple middle-tiers handling business logic.</span></span>

![3 層式應用程式的範例](./images/three-tier-application.png)

## <a name="typical-traits-of-transactional-data"></a><span data-ttu-id="19c19-118">交易資料的一般特性</span><span class="sxs-lookup"><span data-stu-id="19c19-118">Typical traits of transactional data</span></span>

<span data-ttu-id="19c19-119">交易資料常會有下列特性：</span><span class="sxs-lookup"><span data-stu-id="19c19-119">Transactional data tends to have the following traits:</span></span>

| <span data-ttu-id="19c19-120">需求</span><span class="sxs-lookup"><span data-stu-id="19c19-120">Requirement</span></span> | <span data-ttu-id="19c19-121">說明</span><span class="sxs-lookup"><span data-stu-id="19c19-121">Description</span></span> |
| --- | --- |
| <span data-ttu-id="19c19-122">正規化</span><span class="sxs-lookup"><span data-stu-id="19c19-122">Normalization</span></span> | <span data-ttu-id="19c19-123">高度正規化</span><span class="sxs-lookup"><span data-stu-id="19c19-123">Highly normalized</span></span> |
| <span data-ttu-id="19c19-124">結構描述</span><span class="sxs-lookup"><span data-stu-id="19c19-124">Schema</span></span> | <span data-ttu-id="19c19-125">寫入結構描述，強制執行</span><span class="sxs-lookup"><span data-stu-id="19c19-125">Schema on write, strongly enforced</span></span>|
| <span data-ttu-id="19c19-126">一致性</span><span class="sxs-lookup"><span data-stu-id="19c19-126">Consistency</span></span> | <span data-ttu-id="19c19-127">強式一致性、ACID 保證</span><span class="sxs-lookup"><span data-stu-id="19c19-127">Strong consistency, ACID guarantees</span></span> |
| <span data-ttu-id="19c19-128">完整性</span><span class="sxs-lookup"><span data-stu-id="19c19-128">Integrity</span></span> | <span data-ttu-id="19c19-129">高度完整性</span><span class="sxs-lookup"><span data-stu-id="19c19-129">High integrity</span></span> |
| <span data-ttu-id="19c19-130">使用交易</span><span class="sxs-lookup"><span data-stu-id="19c19-130">Uses transactions</span></span> | <span data-ttu-id="19c19-131">yes</span><span class="sxs-lookup"><span data-stu-id="19c19-131">Yes</span></span> |
| <span data-ttu-id="19c19-132">鎖定策略</span><span class="sxs-lookup"><span data-stu-id="19c19-132">Locking strategy</span></span> | <span data-ttu-id="19c19-133">封閉式或開放式</span><span class="sxs-lookup"><span data-stu-id="19c19-133">Pessimistic or optimistic</span></span>|
| <span data-ttu-id="19c19-134">可更新</span><span class="sxs-lookup"><span data-stu-id="19c19-134">Updateable</span></span> | <span data-ttu-id="19c19-135">yes</span><span class="sxs-lookup"><span data-stu-id="19c19-135">Yes</span></span> |
| <span data-ttu-id="19c19-136">可附加</span><span class="sxs-lookup"><span data-stu-id="19c19-136">Appendable</span></span> | <span data-ttu-id="19c19-137">yes</span><span class="sxs-lookup"><span data-stu-id="19c19-137">Yes</span></span> |
| <span data-ttu-id="19c19-138">工作負載</span><span class="sxs-lookup"><span data-stu-id="19c19-138">Workload</span></span> | <span data-ttu-id="19c19-139">大量寫入，中度讀取</span><span class="sxs-lookup"><span data-stu-id="19c19-139">Heavy writes, moderate reads</span></span> |
| <span data-ttu-id="19c19-140">編製索引</span><span class="sxs-lookup"><span data-stu-id="19c19-140">Indexing</span></span> | <span data-ttu-id="19c19-141">主要和次要索引</span><span class="sxs-lookup"><span data-stu-id="19c19-141">Primary and secondary indexes</span></span> |
| <span data-ttu-id="19c19-142">資料大小</span><span class="sxs-lookup"><span data-stu-id="19c19-142">Datum size</span></span> | <span data-ttu-id="19c19-143">中小型</span><span class="sxs-lookup"><span data-stu-id="19c19-143">Small to medium sized</span></span> |
| <span data-ttu-id="19c19-144">模型</span><span class="sxs-lookup"><span data-stu-id="19c19-144">Model</span></span> | <span data-ttu-id="19c19-145">關聯式</span><span class="sxs-lookup"><span data-stu-id="19c19-145">Relational</span></span> |
| <span data-ttu-id="19c19-146">資料圖形</span><span class="sxs-lookup"><span data-stu-id="19c19-146">Data shape</span></span> | <span data-ttu-id="19c19-147">表格式</span><span class="sxs-lookup"><span data-stu-id="19c19-147">Tabular</span></span> |
| <span data-ttu-id="19c19-148">查詢彈性</span><span class="sxs-lookup"><span data-stu-id="19c19-148">Query flexibility</span></span> | <span data-ttu-id="19c19-149">高彈性</span><span class="sxs-lookup"><span data-stu-id="19c19-149">Highly flexible</span></span> |
| <span data-ttu-id="19c19-150">調整</span><span class="sxs-lookup"><span data-stu-id="19c19-150">Scale</span></span> | <span data-ttu-id="19c19-151">小型 (數 MB) 至大型 (數 TB)</span><span class="sxs-lookup"><span data-stu-id="19c19-151">Small (MBs) to Large (a few TBs)</span></span> | 

## <a name="see-also"></a><span data-ttu-id="19c19-152">另請參閱</span><span class="sxs-lookup"><span data-stu-id="19c19-152">See Also</span></span>

[<span data-ttu-id="19c19-153">線上交易處理</span><span class="sxs-lookup"><span data-stu-id="19c19-153">Online Transaction Processing</span></span>](../scenarios/online-transaction-processing.md)

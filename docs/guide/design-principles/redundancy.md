---
title: 讓各個項目都有備援
titleSuffix: Azure Application Architecture Guide
description: 將備援建置到您的應用程式中，以避免發生單一失敗點。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: c8722250b5b5357c9ffcf65eced95b4f2b487c72
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241719"
---
# <a name="make-all-things-redundant"></a><span data-ttu-id="7dcb8-103">讓各個項目都有備援</span><span class="sxs-lookup"><span data-stu-id="7dcb8-103">Make all things redundant</span></span>

## <a name="build-redundancy-into-your-application-to-avoid-having-single-points-of-failure"></a><span data-ttu-id="7dcb8-104">將備援建置到您的應用程式中，以避免發生單一失敗點</span><span class="sxs-lookup"><span data-stu-id="7dcb8-104">Build redundancy into your application, to avoid having single points of failure</span></span>

<span data-ttu-id="7dcb8-105">復原應用程式會環繞著失敗路由。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-105">A resilient application routes around failure.</span></span> <span data-ttu-id="7dcb8-106">識別您的應用程式中的關鍵路徑。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-106">Identify the critical paths in your application.</span></span> <span data-ttu-id="7dcb8-107">路徑中的每個點上有備援嗎？</span><span class="sxs-lookup"><span data-stu-id="7dcb8-107">Is there redundancy at each point in the path?</span></span> <span data-ttu-id="7dcb8-108">如果子系統失敗，應用程式是否將容錯移轉到其他項目？</span><span class="sxs-lookup"><span data-stu-id="7dcb8-108">If a subsystem fails, will the application fail over to something else?</span></span>

## <a name="recommendations"></a><span data-ttu-id="7dcb8-109">建議</span><span class="sxs-lookup"><span data-stu-id="7dcb8-109">Recommendations</span></span>

<span data-ttu-id="7dcb8-110">**考慮商務需求**。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-110">**Consider business requirements**.</span></span> <span data-ttu-id="7dcb8-111">系統內建的備援數量可能同時影響成本和複雜度。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-111">The amount of redundancy built into a system can affect both cost and complexity.</span></span> <span data-ttu-id="7dcb8-112">您的架構應根據商務需求，例如復原時間目標 (RTO)。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-112">Your architecture should be informed by your business requirements, such as recovery time objective (RTO).</span></span> <span data-ttu-id="7dcb8-113">比方說，多區域部署成本高於單一區域部署，並且管理更為複雜。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-113">For example, a multi-region deployment is more expensive than a single-region deployment, and is more complicated to manage.</span></span> <span data-ttu-id="7dcb8-114">您將需要作業程序來處理容錯移轉和容錯回復。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-114">You will need operational procedures to handle failover and failback.</span></span> <span data-ttu-id="7dcb8-115">額外的成本和複雜度對一些商務情節可能合理，但對其他情節則不適用。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-115">The additional cost and complexity might be justified for some business scenarios and not others.</span></span>

<span data-ttu-id="7dcb8-116">**將 VM 放在負載平衡器後**。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-116">**Place VMs behind a load balancer**.</span></span> <span data-ttu-id="7dcb8-117">請勿對關鍵任務工作負載使用單一 VM。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-117">Don't use a single VM for mission-critical workloads.</span></span> <span data-ttu-id="7dcb8-118">相反地，請將多個 VM 放置在負載平衡器後方。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-118">Instead, place multiple VMs behind a load balancer.</span></span> <span data-ttu-id="7dcb8-119">如果任何 VM 變得無法使用，負載平衡器會將流量散發至其餘狀況良好的 VM。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-119">If any VM becomes unavailable, the load balancer distributes traffic to the remaining healthy VMs.</span></span> <span data-ttu-id="7dcb8-120">若要了解如何部署此設定，請參閱[用於延展性和可用性的多個 VM][multi-vm-blueprint]。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-120">To learn how to deploy this configuration, see [Multiple VMs for scalability and availability][multi-vm-blueprint].</span></span>

![負載平衡的 VM 圖](./images/load-balancing.svg)

<span data-ttu-id="7dcb8-122">**複寫資料庫**。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-122">**Replicate databases**.</span></span> <span data-ttu-id="7dcb8-123">Azure SQL Database 和 Cosmos DB 會自動複寫區域內的資料，而且您可以跨區域啟用異地複寫。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-123">Azure SQL Database and Cosmos DB automatically replicate the data within a region, and you can enable geo-replication across regions.</span></span> <span data-ttu-id="7dcb8-124">如果您使用 IaaS 資料庫解決方案，請選擇一個支援複寫和容錯移轉的解決方案，例如 [SQL Server Alwayson 可用性群組][sql-always-on]。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-124">If you are using an IaaS database solution, choose one that supports replication and failover, such as [SQL Server Always On Availability Groups][sql-always-on].</span></span>

<span data-ttu-id="7dcb8-125">**啟用異地複寫**。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-125">**Enable geo-replication**.</span></span> <span data-ttu-id="7dcb8-126">[Azure SQL Database][sql-geo-replication] 和 [Cosmos DB][cosmosdb-geo-replication] 的異地複寫會在一或多個次要區域中建立資料的次要可讀取複本。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-126">Geo-replication for [Azure SQL Database][sql-geo-replication] and [Cosmos DB][cosmosdb-geo-replication] creates secondary readable replicas of your data in one or more secondary regions.</span></span> <span data-ttu-id="7dcb8-127">如果發生中斷，資料庫可以容錯移轉到次要區域以進行寫入。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-127">In the event of an outage, the database can fail over to the secondary region for writes.</span></span>

<span data-ttu-id="7dcb8-128">**針對可用性的分割**。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-128">**Partition for availability**.</span></span> <span data-ttu-id="7dcb8-129">資料庫分割通常用來改善延展性，但它也可以提升可用性。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-129">Database partitioning is often used to improve scalability, but it can also improve availability.</span></span> <span data-ttu-id="7dcb8-130">如果一個分區關閉，其他分區仍可供存取。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-130">If one shard goes down, the other shards can still be reached.</span></span> <span data-ttu-id="7dcb8-131">一個分區失敗只會中斷交易總計的子集。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-131">A failure in one shard will only disrupt a subset of the total transactions.</span></span>

<span data-ttu-id="7dcb8-132">**部署到多個區域**。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-132">**Deploy to more than one region**.</span></span> <span data-ttu-id="7dcb8-133">如需最高的可用性，請將應用程式部署到多個區域。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-133">For the highest availability, deploy the application to more than one region.</span></span> <span data-ttu-id="7dcb8-134">這樣一來，在極少數的情況下，如果問題會影響整個區域，應用程式可以容錯移轉到另一個區域。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-134">That way, in the rare case when a problem affects an entire region, the application can fail over to another region.</span></span> <span data-ttu-id="7dcb8-135">下圖顯示使用 Azure 流量管理員來處理容錯移轉的多重區域應用程式。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-135">The following diagram shows a multi-region application that uses Azure Traffic Manager to handle failover.</span></span>

![使用 Azure 流量管理員來處理容錯移轉的圖表](./images/failover.svg)

<span data-ttu-id="7dcb8-137">**同步處理前端和後端容錯移轉**。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-137">**Synchronize front and backend failover**.</span></span> <span data-ttu-id="7dcb8-138">使用 Azure 流量管理員來將前端容錯移轉。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-138">Use Azure Traffic Manager to fail over the front end.</span></span> <span data-ttu-id="7dcb8-139">如果一個區域中的後端變成無法連線，流量管理員會將新的要求路由至次要區域。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-139">If the front end becomes unreachable in one region, Traffic Manager will route new requests to the secondary region.</span></span> <span data-ttu-id="7dcb8-140">根據您的資料庫解決方案，您可能需要對資料庫協調容錯移轉。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-140">Depending on your database solution, you may need to coordinate failing over the database.</span></span>

<span data-ttu-id="7dcb8-141">**使用自動容錯移轉但手動容錯回復**。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-141">**Use automatic failover but manual failback**.</span></span> <span data-ttu-id="7dcb8-142">對自動容錯移轉 (而不要對自動容錯回復) 使用流量管理員。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-142">Use Traffic Manager for automatic failover, but not for automatic failback.</span></span> <span data-ttu-id="7dcb8-143">自動容錯回復帶有的風險是，您可能會在區域完全狀況良好之前切換到主要區域。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-143">Automatic failback carries a risk that you might switch to the primary region before the region is completely healthy.</span></span> <span data-ttu-id="7dcb8-144">相反地，請確認所有的應用程式子系統狀況良好，然後再手動進行容錯回復。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-144">Instead, verify that all application subsystems are healthy before manually failing back.</span></span> <span data-ttu-id="7dcb8-145">此外，根據資料庫，在容錯移轉之前，您可能需要檢查資料的一致性。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-145">Also, depending on the database, you might need to check data consistency before failing back.</span></span>

<span data-ttu-id="7dcb8-146">**包含流量管理員的備援**。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-146">**Include redundancy for Traffic Manager**.</span></span> <span data-ttu-id="7dcb8-147">流量管理員是可能的失敗點。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-147">Traffic Manager is a possible failure point.</span></span> <span data-ttu-id="7dcb8-148">檢閱流量管理員 SLA，並判斷單獨使用流量管理員是否符合您獲得高可用性的商務需求。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-148">Review the Traffic Manager SLA, and determine whether using Traffic Manager alone meets your business requirements for high availability.</span></span> <span data-ttu-id="7dcb8-149">如果沒有，請考慮新增另一個流量管理解決方案作為容錯回復。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-149">If not, consider adding another traffic management solution as a failback.</span></span> <span data-ttu-id="7dcb8-150">如果 Azure 流量管理員服務失敗，在 DNS 中變更您的 CNAME 記錄，以指向其他流量管理服務。</span><span class="sxs-lookup"><span data-stu-id="7dcb8-150">If the Azure Traffic Manager service fails, change your CNAME records in DNS to point to the other traffic management service.</span></span>

<!-- links -->

[multi-vm-blueprint]: ../../reference-architectures/virtual-machines-windows/multi-vm.md

[cassandra]: https://cassandra.apache.org/
[cosmosdb-geo-replication]: /azure/cosmos-db/distribute-data-globally
[sql-always-on]: https://msdn.microsoft.com/library/hh510230.aspx
[sql-geo-replication]: /azure/sql-database/sql-database-geo-replication-overview

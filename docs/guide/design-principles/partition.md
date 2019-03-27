---
title: 針對限制進行分割
titleSuffix: Azure Application Architecture Guide
description: 使用分割作業來解決資料庫、網路和計算限制。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 76590d2a0c16df9d599e7d4a856a84b5e3bdcec8
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241779"
---
# <a name="partition-around-limits"></a><span data-ttu-id="43e39-103">針對限制進行分割</span><span class="sxs-lookup"><span data-stu-id="43e39-103">Partition around limits</span></span>

## <a name="use-partitioning-to-work-around-database-network-and-compute-limits"></a><span data-ttu-id="43e39-104">使用分割作業來解決資料庫、網路和計算限制</span><span class="sxs-lookup"><span data-stu-id="43e39-104">Use partitioning to work around database, network, and compute limits</span></span>

<span data-ttu-id="43e39-105">雲端中所有服務的相應增加能力都有其限制。</span><span class="sxs-lookup"><span data-stu-id="43e39-105">In the cloud, all services have limits in their ability to scale up.</span></span> <span data-ttu-id="43e39-106">Azure 服務的限制記載於 [Azure 訂用帳戶和服務的限制、配額及條件約束][azure-limits]。</span><span class="sxs-lookup"><span data-stu-id="43e39-106">Azure service limits are documented in [Azure subscription and service limits, quotas, and constraints][azure-limits].</span></span> <span data-ttu-id="43e39-107">其限制包括核心數目、資料庫大小、查詢輸送量和網路輸送量。</span><span class="sxs-lookup"><span data-stu-id="43e39-107">Limits include number of cores, database size, query throughput, and network throughput.</span></span> <span data-ttu-id="43e39-108">如果您的系統已變得夠大，就可能會達到上述的其中一個或多個限制。</span><span class="sxs-lookup"><span data-stu-id="43e39-108">If your system grows sufficiently large, you may hit one or more of these limits.</span></span> <span data-ttu-id="43e39-109">使用資料分割可解決這些限制。</span><span class="sxs-lookup"><span data-stu-id="43e39-109">Use partitioning to work around these limits.</span></span>

<span data-ttu-id="43e39-110">有許多方式可分割系統，例如：</span><span class="sxs-lookup"><span data-stu-id="43e39-110">There are many ways to partition a system, such as:</span></span>

- <span data-ttu-id="43e39-111">分割資料庫以避開資料庫大小、資料 I/O 或並行工作階段數目的限制。</span><span class="sxs-lookup"><span data-stu-id="43e39-111">Partition a database to avoid limits on database size, data I/O, or number of concurrent sessions.</span></span>

- <span data-ttu-id="43e39-112">分割佇列或訊息匯流排，以避開要求數目或並行連線數目的限制。</span><span class="sxs-lookup"><span data-stu-id="43e39-112">Partition a queue or message bus to avoid limits on the number of requests or the number of concurrent connections.</span></span>

- <span data-ttu-id="43e39-113">分割 App Service Web 應用程式，以避開每個 App Service 方案的執行個體數目限制。</span><span class="sxs-lookup"><span data-stu-id="43e39-113">Partition an App Service web app to avoid limits on the number of instances per App Service plan.</span></span>

<span data-ttu-id="43e39-114">資料庫可以進行「水平」、「垂直」或「功能性」分割。</span><span class="sxs-lookup"><span data-stu-id="43e39-114">A database can be partitioned *horizontally*, *vertically*, or *functionally*.</span></span>

- <span data-ttu-id="43e39-115">在水平分割 (也稱為分區化) 中，每個資料分割都會保有總資料集的一小部分資料。</span><span class="sxs-lookup"><span data-stu-id="43e39-115">In horizontal partitioning, also called sharding, each partition holds data for a subset of the total data set.</span></span> <span data-ttu-id="43e39-116">資料分割會共用相同的資料結構描述。</span><span class="sxs-lookup"><span data-stu-id="43e39-116">The partitions share the same data schema.</span></span> <span data-ttu-id="43e39-117">例如，名稱開頭為 A&ndash;M 的客戶會進入某個資料分割，N&ndash;Z 的客戶則進入另一個資料分割。</span><span class="sxs-lookup"><span data-stu-id="43e39-117">For example, customers whose names start with A&ndash;M go into one partition, N&ndash;Z into another partition.</span></span>

- <span data-ttu-id="43e39-118">在垂直分割中，每個資料分割都會保有資料存放區之項目的一小部分欄位。</span><span class="sxs-lookup"><span data-stu-id="43e39-118">In vertical partitioning, each partition holds a subset of the fields for the items in the data store.</span></span> <span data-ttu-id="43e39-119">例如，將經常存取的欄位放在某個資料分割，較不常存取的欄位放在另一個資料分割。</span><span class="sxs-lookup"><span data-stu-id="43e39-119">For example, put frequently accessed fields in one partition, and less frequently accessed fields in another.</span></span>

- <span data-ttu-id="43e39-120">在功能性分割中，資料會根據系統中每個繫結的內容使用它的方式進行分割。</span><span class="sxs-lookup"><span data-stu-id="43e39-120">In functional partitioning, data is partitioned according to how it is used by each bounded context in the system.</span></span> <span data-ttu-id="43e39-121">例如，將發票資料儲存在某個資料分割，而將產品庫存資料儲存在另一個資料分割。</span><span class="sxs-lookup"><span data-stu-id="43e39-121">For example, store invoice data in one partition and product inventory data in another.</span></span> <span data-ttu-id="43e39-122">其結構描述各自獨立。</span><span class="sxs-lookup"><span data-stu-id="43e39-122">The schemas are independent.</span></span>

<span data-ttu-id="43e39-123">如需詳細的指導方針，請參閱[資料分割][data-partitioning-guidance]。</span><span class="sxs-lookup"><span data-stu-id="43e39-123">For more detailed guidance, see [Data partitioning][data-partitioning-guidance].</span></span>

## <a name="recommendations"></a><span data-ttu-id="43e39-124">建議</span><span class="sxs-lookup"><span data-stu-id="43e39-124">Recommendations</span></span>

<span data-ttu-id="43e39-125">**分割應用程式的不同部分**。</span><span class="sxs-lookup"><span data-stu-id="43e39-125">**Partition different parts of the application**.</span></span> <span data-ttu-id="43e39-126">資料庫顯然是其中一個要分割的候選目標，但也請考慮儲存體、快取、佇列和計算執行個體。</span><span class="sxs-lookup"><span data-stu-id="43e39-126">Databases are one obvious candidate for partitioning, but also consider storage, cache, queues, and compute instances.</span></span>

<span data-ttu-id="43e39-127">**設計資料分割索引鍵以避免作用點**。</span><span class="sxs-lookup"><span data-stu-id="43e39-127">**Design the partition key to avoid hot spots**.</span></span> <span data-ttu-id="43e39-128">如果您分割資料庫，但某個分區仍保有大多數的要求，您還是沒有解決問題。</span><span class="sxs-lookup"><span data-stu-id="43e39-128">If you partition a database, but one shard still gets the majority of the requests, then you haven't solved your problem.</span></span> <span data-ttu-id="43e39-129">理論上，負載會平均分散給所有資料分割。</span><span class="sxs-lookup"><span data-stu-id="43e39-129">Ideally, load gets distributed evenly across all the partitions.</span></span> <span data-ttu-id="43e39-130">例如，依客戶識別碼產生雜湊，而不是依客戶名稱的第一個字母來產生，因為某些字母的出現頻率較高。</span><span class="sxs-lookup"><span data-stu-id="43e39-130">For example, hash by customer ID and not the first letter of the customer name, because some letters are more frequent.</span></span> <span data-ttu-id="43e39-131">在分割訊息佇列時也適用相同原則。</span><span class="sxs-lookup"><span data-stu-id="43e39-131">The same principle applies when partitioning a message queue.</span></span> <span data-ttu-id="43e39-132">您所選擇的資料分割索引鍵，必須能將訊息平均分散給整組佇列。</span><span class="sxs-lookup"><span data-stu-id="43e39-132">Pick a partition key that leads to an even distribution of messages across the set of queues.</span></span> <span data-ttu-id="43e39-133">如需詳細資訊，請參閱[分區化][sharding]。</span><span class="sxs-lookup"><span data-stu-id="43e39-133">For more information, see [Sharding][sharding].</span></span>

<span data-ttu-id="43e39-134">**針對 Azure 訂用帳戶和服務限制來進行分割**。</span><span class="sxs-lookup"><span data-stu-id="43e39-134">**Partition around Azure subscription and service limits**.</span></span> <span data-ttu-id="43e39-135">個別的元件和服務有其限制，但訂用帳戶和資源群組也有限制。</span><span class="sxs-lookup"><span data-stu-id="43e39-135">Individual components and services have limits, but there are also limits for subscriptions and resource groups.</span></span> <span data-ttu-id="43e39-136">對於非常大型的應用程式，您可能需要針對這些限制來進行分割。</span><span class="sxs-lookup"><span data-stu-id="43e39-136">For very large applications, you might need to partition around those limits.</span></span>

<span data-ttu-id="43e39-137">**在不同層級進行分割**。</span><span class="sxs-lookup"><span data-stu-id="43e39-137">**Partition at different levels**.</span></span> <span data-ttu-id="43e39-138">請設想部署在 VM 上的資料庫伺服器。</span><span class="sxs-lookup"><span data-stu-id="43e39-138">Consider a database server deployed on a VM.</span></span> <span data-ttu-id="43e39-139">該 VM 的 VHD 受到 Azure 儲存體所支援。</span><span class="sxs-lookup"><span data-stu-id="43e39-139">The VM has a VHD that is backed by Azure Storage.</span></span> <span data-ttu-id="43e39-140">儲存體帳戶隸屬於 Azure 訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="43e39-140">The storage account belongs to an Azure subscription.</span></span> <span data-ttu-id="43e39-141">請注意到此階層中的每個步驟都有限制。</span><span class="sxs-lookup"><span data-stu-id="43e39-141">Notice that each step in the hierarchy has limits.</span></span> <span data-ttu-id="43e39-142">資料庫伺服器可能有連線集區限制。</span><span class="sxs-lookup"><span data-stu-id="43e39-142">The database server may have a connection pool limit.</span></span> <span data-ttu-id="43e39-143">VM 有 CPU 和網路限制。</span><span class="sxs-lookup"><span data-stu-id="43e39-143">VMs have CPU and network limits.</span></span> <span data-ttu-id="43e39-144">儲存體有 IOPS 限制。</span><span class="sxs-lookup"><span data-stu-id="43e39-144">Storage has IOPS limits.</span></span> <span data-ttu-id="43e39-145">訂用帳戶有 VM 核心數目限制。</span><span class="sxs-lookup"><span data-stu-id="43e39-145">The subscription has limits on the number of VM cores.</span></span> <span data-ttu-id="43e39-146">一般而言，階層中較低的位置比較容易分割。</span><span class="sxs-lookup"><span data-stu-id="43e39-146">Generally, it's easier to partition lower in the hierarchy.</span></span> <span data-ttu-id="43e39-147">只有大型應用程式才必須在訂用帳戶層級進行分割。</span><span class="sxs-lookup"><span data-stu-id="43e39-147">Only large applications should need to partition at the subscription level.</span></span>

<!-- links -->

[azure-limits]: /azure/azure-subscription-service-limits
[data-partitioning-guidance]: ../../best-practices/data-partitioning.md
[sharding]: ../../patterns/sharding.md

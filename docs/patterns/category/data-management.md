---
title: 資料管理模式
titleSuffix: Cloud Design Patterns
description: 資料管理是雲端應用程式的關鍵元素，並且會影響多數的品質屬性。 因為效能、延展性或可用性之類的原因，資料通常裝載在不同位置以及在多個伺服器上，而這可能出現一些挑戰。 例如，必須維持資料的一致性，並且通常需要同步處理跨不同位置的資料。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 25571a431836656856ed3f299455dfdb94ae3477
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243049"
---
# <a name="data-management-patterns"></a><span data-ttu-id="96b93-106">資料管理模式</span><span class="sxs-lookup"><span data-stu-id="96b93-106">Data Management patterns</span></span>

[!INCLUDE [header](../../_includes/header.md)]

<span data-ttu-id="96b93-107">資料管理是雲端應用程式的關鍵元素，並且會影響多數的品質屬性。</span><span class="sxs-lookup"><span data-stu-id="96b93-107">Data management is the key element of cloud applications, and influences most of the quality attributes.</span></span> <span data-ttu-id="96b93-108">因為效能、延展性或可用性之類的原因，資料通常裝載在不同位置以及在多個伺服器上，而這可能出現一些挑戰。</span><span class="sxs-lookup"><span data-stu-id="96b93-108">Data is typically hosted in different locations and across multiple servers for reasons such as performance, scalability or availability, and this can present a range of challenges.</span></span> <span data-ttu-id="96b93-109">例如，必須維持資料的一致性，並且通常需要同步處理跨不同位置的資料。</span><span class="sxs-lookup"><span data-stu-id="96b93-109">For example, data consistency must be maintained, and data will typically need to be synchronized across different locations.</span></span>

|                        <span data-ttu-id="96b93-110">模式</span><span class="sxs-lookup"><span data-stu-id="96b93-110">Pattern</span></span>                         |                                                                  <span data-ttu-id="96b93-111">總結</span><span class="sxs-lookup"><span data-stu-id="96b93-111">Summary</span></span>                                                                  |
|--------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
|            [<span data-ttu-id="96b93-112">另行快取</span><span class="sxs-lookup"><span data-stu-id="96b93-112">Cache-Aside</span></span>](../cache-aside.md)            |                                            <span data-ttu-id="96b93-113">依需要從資料存放區將資料載入快取中</span><span class="sxs-lookup"><span data-stu-id="96b93-113">Load data on demand into a cache from a data store</span></span>                                             |
|                   [<span data-ttu-id="96b93-114">CQRS</span><span class="sxs-lookup"><span data-stu-id="96b93-114">CQRS</span></span>](../cqrs.md)                   |                    <span data-ttu-id="96b93-115">隔離自使用個別介面來更新資料的作業中讀取資料的作業。</span><span class="sxs-lookup"><span data-stu-id="96b93-115">Segregate operations that read data from operations that update data by using separate interfaces.</span></span>                     |
|         [<span data-ttu-id="96b93-116">事件來源</span><span class="sxs-lookup"><span data-stu-id="96b93-116">Event Sourcing</span></span>](../event-sourcing.md)         |               <span data-ttu-id="96b93-117">使用僅附加的存放區記錄完整系列的事件，其描述對網域中的資料採取的動作。</span><span class="sxs-lookup"><span data-stu-id="96b93-117">Use an append-only store to record the full series of events that describe actions taken on data in a domain.</span></span>               |
|            [<span data-ttu-id="96b93-118">索引資料表</span><span class="sxs-lookup"><span data-stu-id="96b93-118">Index Table</span></span>](../index-table.md)            |                         <span data-ttu-id="96b93-119">針對資料存放區中查詢經常參考的欄位建立索引。</span><span class="sxs-lookup"><span data-stu-id="96b93-119">Create indexes over the fields in data stores that are frequently referenced by queries.</span></span>                          |
|      [<span data-ttu-id="96b93-120">具體化檢視</span><span class="sxs-lookup"><span data-stu-id="96b93-120">Materialized View</span></span>](../materialized-view.md)      | <span data-ttu-id="96b93-121">當資料格式對必要的查詢作業而言不理想時，對一或多個資料存放區中的資料產生預先填入的檢視。</span><span class="sxs-lookup"><span data-stu-id="96b93-121">Generate prepopulated views over the data in one or more data stores when the data isn't ideally formatted for required query operations.</span></span> |
|               [<span data-ttu-id="96b93-122">分區化</span><span class="sxs-lookup"><span data-stu-id="96b93-122">Sharding</span></span>](../sharding.md)               |                                    <span data-ttu-id="96b93-123">將資料存放區分割為一組水平分割或分區。</span><span class="sxs-lookup"><span data-stu-id="96b93-123">Divide a data store into a set of horizontal partitions or shards.</span></span>                                     |
| [<span data-ttu-id="96b93-124">靜態內容裝載</span><span class="sxs-lookup"><span data-stu-id="96b93-124">Static Content Hosting</span></span>](../static-content-hosting.md) |                   <span data-ttu-id="96b93-125">將靜態內容部署到可以直接將其交付給用戶端的雲端儲存體服務。</span><span class="sxs-lookup"><span data-stu-id="96b93-125">Deploy static content to a cloud-based storage service that can deliver them directly to the client.</span></span>                    |
|              [<span data-ttu-id="96b93-126">Valet 金鑰</span><span class="sxs-lookup"><span data-stu-id="96b93-126">Valet Key</span></span>](../valet-key.md)              |                 <span data-ttu-id="96b93-127">使用可提供用戶端對特定資源或服務受限制的直接存取的權杖或金鑰。</span><span class="sxs-lookup"><span data-stu-id="96b93-127">Use a token or key that provides clients with restricted direct access to a specific resource or service.</span></span>                 |

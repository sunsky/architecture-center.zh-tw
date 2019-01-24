---
title: Data Lake
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: d433867886ce0afc219fcc9f35eb7f8b3ce6bee1
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54484698"
---
# <a name="data-lakes"></a><span data-ttu-id="6fe8d-102">Data Lake</span><span class="sxs-lookup"><span data-stu-id="6fe8d-102">Data lakes</span></span>

<span data-ttu-id="6fe8d-103">Data Lake 是以資料的原生原始格式保留大量資料的儲存體儲存機制。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-103">A data lake is a storage repository that holds a large amount of data in its native, raw format.</span></span> <span data-ttu-id="6fe8d-104">Data Lake Store 會針對資料 TB 和 PB 調整進行最佳化。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-104">Data lake stores are optimized for scaling to terabytes and petabytes of data.</span></span> <span data-ttu-id="6fe8d-105">資料通常來自多個異質性來源，且可能是結構化、半結構化或非結構化。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-105">The data typically comes from multiple heterogeneous sources, and may be structured, semi-structured, or unstructured.</span></span> <span data-ttu-id="6fe8d-106">Data Lake 的概念是將所有資料儲存在其原始、未轉換的狀態。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-106">The idea with a data lake is to store everything in its original, untransformed state.</span></span> <span data-ttu-id="6fe8d-107">這種方式不同於傳統的[資料倉儲](../relational-data/data-warehousing.md)，後者會在內嵌時轉換以及處理資料。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-107">This approach differs from a traditional [data warehouse](../relational-data/data-warehousing.md), which transforms and processes the data at the time of ingestion.</span></span>

<span data-ttu-id="6fe8d-108">Data Lake 的優點包括：</span><span class="sxs-lookup"><span data-stu-id="6fe8d-108">Advantages of a data lake:</span></span>

- <span data-ttu-id="6fe8d-109">資料永遠不會擲回，因為資料會以其原始格式儲存。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-109">Data is never thrown away, because the data is stored in its raw format.</span></span> <span data-ttu-id="6fe8d-110">這在巨量資料的環境中特別有用，因為您可能無法事先知道可使用資料的哪些深入資訊。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-110">This is especially useful in a big data environment, when you may not know in advance what insights are available from the data.</span></span>
- <span data-ttu-id="6fe8d-111">使用者可以瀏覽資料，並建立自己的查詢。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-111">Users can explore the data and create their own queries.</span></span>
- <span data-ttu-id="6fe8d-112">可能比傳統的 ETL 工具更快。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-112">May be faster than traditional ETL tools.</span></span>
- <span data-ttu-id="6fe8d-113">比資料倉儲更有彈性，因為它可以儲存非結構化和半結構化的資料。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-113">More flexible than a data warehouse, because it can store unstructured and semi-structured data.</span></span>

<span data-ttu-id="6fe8d-114">完整的 Data Lake 解決方案同時包含儲存和處理。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-114">A complete data lake solution consists of both storage and processing.</span></span> <span data-ttu-id="6fe8d-115">Data Lake 儲存體專為容錯、無限延展性，以及高輸送量的資料 (具有不同形狀和大小) 擷取所設計。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-115">Data lake storage is designed for fault-tolerance, infinite scalability, and high-throughput ingestion of data with varying shapes and sizes.</span></span> <span data-ttu-id="6fe8d-116">Data Lake 處理涉及一或多個具有這些建置目標的處理引擎，並可大規模處理 Data Lake 中所儲存的資料。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-116">Data lake processing involves one or more processing engines built with these goals in mind, and can operate on data stored in a data lake at scale.</span></span>

## <a name="when-to-use-a-data-lake"></a><span data-ttu-id="6fe8d-117">使用 Data Lake 的時機</span><span class="sxs-lookup"><span data-stu-id="6fe8d-117">When to use a data lake</span></span>

<span data-ttu-id="6fe8d-118">Data Lake 的標準用法包括[資料瀏覽](./interactive-data-exploration.md)資料分析和機器學習。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-118">Typical uses for a data lake include [data exploration](./interactive-data-exploration.md), data analytics, and machine learning.</span></span>

<span data-ttu-id="6fe8d-119">Data Lake 也可以做為資料倉儲的資料來源。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-119">A data lake can also act as the data source for a data warehouse.</span></span> <span data-ttu-id="6fe8d-120">使用此方法時，未經處理的資料會內嵌到資料湖，然後轉換成結構化的查詢格式。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-120">With this approach, the raw data is ingested into the data lake and then transformed into a structured queryable format.</span></span> <span data-ttu-id="6fe8d-121">此轉換通常使用 [ELT](../relational-data/etl.md#extract-load-and-transform-elt) (擷取-載入-轉換) 管線，其中資料會就地內嵌和轉換。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-121">Typically this transformation uses an [ELT](../relational-data/etl.md#extract-load-and-transform-elt) (extract-load-transform) pipeline, where the data is ingested and transformed in place.</span></span> <span data-ttu-id="6fe8d-122">已具有關聯性的來源資料可能會使用 ETL 程序，直接進入資料倉儲，並略過 Data Lake。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-122">Source data that is already relational may go directly into the data warehouse, using an ETL process, skipping the data lake.</span></span>

<span data-ttu-id="6fe8d-123">Data Lake Store 通常會用於事件串流或 IoT 案例，因為它們可以保存大量的關聯式與非關聯式資料，而不需要轉換或結構描述定義。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-123">Data lake stores are often used in event streaming or IoT scenarios, because they can persist large amounts of relational and nonrelational data without transformation or schema definition.</span></span> <span data-ttu-id="6fe8d-124">其建置目的是要處理大量低延遲的小型寫入，並已經過最佳化而可提供龐大輸送量。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-124">They are built to handle high volumes of small writes at low latency, and are optimized for massive throughput.</span></span>

## <a name="challenges"></a><span data-ttu-id="6fe8d-125">挑戰</span><span class="sxs-lookup"><span data-stu-id="6fe8d-125">Challenges</span></span>

- <span data-ttu-id="6fe8d-126">缺少結構描述或描述性中繼資料可能會讓資料難以使用或查詢。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-126">Lack of a schema or descriptive metadata can make the data hard to consume or query.</span></span>
- <span data-ttu-id="6fe8d-127">缺少資料的一致性語意會讓執行資料分析變得困難，除非使用者對於資料分析非常熟練。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-127">Lack of semantic consistency across the data can make it challenging to perform analysis on the data, unless users are highly skilled at data analytics.</span></span>
- <span data-ttu-id="6fe8d-128">這會使得進入 Data Lake 的資料品質很難保證。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-128">It can be hard to guarantee the quality of the data going into the data lake.</span></span>
- <span data-ttu-id="6fe8d-129">沒有適當的控管，存取控制和隱私權問題將會成為問題。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-129">Without proper governance, access control and privacy issues can be problems.</span></span> <span data-ttu-id="6fe8d-130">什麼資訊會進入 Data Lake，何人可以存取資料，以及為了什麼用途？</span><span class="sxs-lookup"><span data-stu-id="6fe8d-130">What information is going into the data lake, who can access that data, and for what uses?</span></span>
- <span data-ttu-id="6fe8d-131">Data Lake 可能不是整合已具有關聯性資料的最佳方式。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-131">A data lake may not be the best way to integrate data that is already relational.</span></span>
- <span data-ttu-id="6fe8d-132">單獨使用時，Data Lake 不提供整個組織的整合式或整體檢視。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-132">By itself, a data lake does not provide integrated or holistic views across the organization.</span></span>
- <span data-ttu-id="6fe8d-133">Data Lake 可能會成為實際上永遠不會被分析或探索深入剖析之資料的垃圾場。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-133">A data lake may become a dumping ground for data that is never actually analyzed or mined for insights.</span></span>

## <a name="relevant-azure-services"></a><span data-ttu-id="6fe8d-134">相關 Azure 服務</span><span class="sxs-lookup"><span data-stu-id="6fe8d-134">Relevant Azure services</span></span>

- <span data-ttu-id="6fe8d-135">[Data Lake Store](/azure/data-lake-store/) 是超大規模、與 Hadoop 相容的儲存機制。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-135">[Data Lake Store](/azure/data-lake-store/) is a hyper-scale, Hadoop-compatible repository.</span></span>
- <span data-ttu-id="6fe8d-136">[Data Lake Analytics](/azure/data-lake-analytics/) 是隨選分析作業服務，可簡化巨量資料分析。</span><span class="sxs-lookup"><span data-stu-id="6fe8d-136">[Data Lake Analytics](/azure/data-lake-analytics/) is an on-demand analytics job service to simplify big data analytics.</span></span>

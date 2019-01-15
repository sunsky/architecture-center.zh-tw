---
title: 選擇資料存放區的準則
titleSuffix: Azure Application Architecture Guide
description: Azure 計算選項的概觀。
author: MikeWasson
ms.date: 06/01/2018
ms.custom: seojan19
ms.openlocfilehash: 156df11d74d033d40d943c60e8e41d4920a24175
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54114312"
---
# <a name="criteria-for-choosing-a-data-store"></a><span data-ttu-id="91fd8-103">選擇資料存放區的準則</span><span class="sxs-lookup"><span data-stu-id="91fd8-103">Criteria for choosing a data store</span></span>

<span data-ttu-id="91fd8-104">Azure 支援許多類型的資料儲存體解決方案，每種解決方案各有不同的特點與功能。</span><span class="sxs-lookup"><span data-stu-id="91fd8-104">Azure supports many types of data storage solutions, each providing different features and capabilities.</span></span> <span data-ttu-id="91fd8-105">本文說明評估資料存放區時應使用的比較準則。</span><span class="sxs-lookup"><span data-stu-id="91fd8-105">This article describes the comparison criteria you should use when evaluating a data store.</span></span> <span data-ttu-id="91fd8-106">目標是協助您判斷哪些資料儲存體類型可符合您的解決方案需求。</span><span class="sxs-lookup"><span data-stu-id="91fd8-106">The goal is to help you determine which data storage types can meet your solution's requirements.</span></span>

## <a name="general-considerations"></a><span data-ttu-id="91fd8-107">一般考量</span><span class="sxs-lookup"><span data-stu-id="91fd8-107">General Considerations</span></span>

<span data-ttu-id="91fd8-108">為了開始進行比較，請盡量收集下列有關資料需求的資訊。</span><span class="sxs-lookup"><span data-stu-id="91fd8-108">To start your comparison, gather as much of the following information as you can about your data needs.</span></span> <span data-ttu-id="91fd8-109">此資訊將協助您判斷哪些資料儲存體類型符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="91fd8-109">This information will help you to determine which data storage types will meet your needs.</span></span>

### <a name="functional-requirements"></a><span data-ttu-id="91fd8-110">功能需求</span><span class="sxs-lookup"><span data-stu-id="91fd8-110">Functional requirements</span></span>

- <span data-ttu-id="91fd8-111">**資料格式**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-111">**Data format**.</span></span> <span data-ttu-id="91fd8-112">您想要儲存何種資料類型？</span><span class="sxs-lookup"><span data-stu-id="91fd8-112">What type of data are you intending to store?</span></span> <span data-ttu-id="91fd8-113">常見的類型包括交易資料、JSON 物件、遙測、搜尋索引或一般檔案。</span><span class="sxs-lookup"><span data-stu-id="91fd8-113">Common types include transactional data, JSON objects, telemetry, search indexes, or flat files.</span></span>

- <span data-ttu-id="91fd8-114">**資料大小**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-114">**Data size**.</span></span> <span data-ttu-id="91fd8-115">您要儲存的實體有多大？</span><span class="sxs-lookup"><span data-stu-id="91fd8-115">How large are the entities you need to store?</span></span> <span data-ttu-id="91fd8-116">這些實體必須維持單一文件的形式嗎？或是可分割為多個文件、資料表或集合等等？</span><span class="sxs-lookup"><span data-stu-id="91fd8-116">Will these entities need to be maintained as a single document, or can they be split across multiple documents, tables, collections, and so forth?</span></span>

- <span data-ttu-id="91fd8-117">**規模和結構**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-117">**Scale and structure**.</span></span> <span data-ttu-id="91fd8-118">您需要的整體儲存體容量為何？</span><span class="sxs-lookup"><span data-stu-id="91fd8-118">What is the overall amount of storage capacity you need?</span></span> <span data-ttu-id="91fd8-119">您預期會分割資料嗎？</span><span class="sxs-lookup"><span data-stu-id="91fd8-119">Do you anticipate partitioning your data?</span></span>

- <span data-ttu-id="91fd8-120">**資料關聯性**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-120">**Data relationships**.</span></span> <span data-ttu-id="91fd8-121">您的資料需要支援一對多或多對多關聯性嗎？</span><span class="sxs-lookup"><span data-stu-id="91fd8-121">Will your data need to support one-to-many or many-to-many relationships?</span></span> <span data-ttu-id="91fd8-122">關聯性本身是資料很重要的一部分嗎？</span><span class="sxs-lookup"><span data-stu-id="91fd8-122">Are relationships themselves an important part of the data?</span></span> <span data-ttu-id="91fd8-123">您需要加入或結合來自相同資料集或外部資料集的資料嗎？</span><span class="sxs-lookup"><span data-stu-id="91fd8-123">Will you need to join or otherwise combine data from within the same dataset, or from external datasets?</span></span>

- <span data-ttu-id="91fd8-124">**一致性模型**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-124">**Consistency model**.</span></span> <span data-ttu-id="91fd8-125">在可進行進一步的變更之前，使某個節點中的更新出現在其他節點中有多重要？</span><span class="sxs-lookup"><span data-stu-id="91fd8-125">How important is it for updates made in one node to appear in other nodes, before further changes can be made?</span></span> <span data-ttu-id="91fd8-126">您可以接受最終一致性嗎？</span><span class="sxs-lookup"><span data-stu-id="91fd8-126">Can you accept eventual consistency?</span></span> <span data-ttu-id="91fd8-127">您是否需要交易的 ACID 保證？</span><span class="sxs-lookup"><span data-stu-id="91fd8-127">Do you need ACID guarantees for transactions?</span></span>

- <span data-ttu-id="91fd8-128">**結構描述彈性**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-128">**Schema flexibility**.</span></span> <span data-ttu-id="91fd8-129">您要將何種結構描述套用到您的資料？</span><span class="sxs-lookup"><span data-stu-id="91fd8-129">What kind of schemas will you apply to your data?</span></span> <span data-ttu-id="91fd8-130">您將使用採用靜態結構描述 (schema-on-write) 方法的固定結構描述，或是採用動態結構描述 (schema-on-read) 方法？</span><span class="sxs-lookup"><span data-stu-id="91fd8-130">Will you use a fixed schema, a schema-on-write approach, or a schema-on-read approach?</span></span>

- <span data-ttu-id="91fd8-131">**並行存取**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-131">**Concurrency**.</span></span> <span data-ttu-id="91fd8-132">更新和同步處理資料時，要使用何種並行存取機制？</span><span class="sxs-lookup"><span data-stu-id="91fd8-132">What kind of concurrency mechanism do you want to use when updating and synchronizing data?</span></span> <span data-ttu-id="91fd8-133">應用程式是否將執行許多可能發生衝突的更新？</span><span class="sxs-lookup"><span data-stu-id="91fd8-133">Will the application perform many updates that could potentially conflict.</span></span> <span data-ttu-id="91fd8-134">如果是，您可能需要記錄鎖定和封閉式並行存取控制。</span><span class="sxs-lookup"><span data-stu-id="91fd8-134">If so, you may require record locking and pessimistic concurrency control.</span></span> <span data-ttu-id="91fd8-135">或者，您可以支援開放式並行存取控制嗎？</span><span class="sxs-lookup"><span data-stu-id="91fd8-135">Alternatively, can you support optimistic concurrency controls?</span></span> <span data-ttu-id="91fd8-136">如果是這樣，以時間戳記為基礎的簡單並行存取控制是否足夠？或者您需要多版本並行存取控制的新增功能？</span><span class="sxs-lookup"><span data-stu-id="91fd8-136">If so, is simple timestamp-based concurrency control enough, or do you need the added functionality of multi-version concurrency control?</span></span>

- <span data-ttu-id="91fd8-137">**資料移動**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-137">**Data movement**.</span></span> <span data-ttu-id="91fd8-138">您的解決方案需要執行 ETL 工作以將資料移至其他存放區或資料倉儲嗎？</span><span class="sxs-lookup"><span data-stu-id="91fd8-138">Will your solution need to perform ETL tasks to move data to other stores or data warehouses?</span></span>

- <span data-ttu-id="91fd8-139">**資料生命週期**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-139">**Data lifecycle**.</span></span> <span data-ttu-id="91fd8-140">資料是寫入一次但讀取多次嗎？</span><span class="sxs-lookup"><span data-stu-id="91fd8-140">Is the data write-once, read-many?</span></span> <span data-ttu-id="91fd8-141">是否可移到非經常性或冷儲存體？</span><span class="sxs-lookup"><span data-stu-id="91fd8-141">Can it be moved into cool or cold storage?</span></span>

- <span data-ttu-id="91fd8-142">**其他支援的功能**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-142">**Other supported features**.</span></span> <span data-ttu-id="91fd8-143">您是否需要任何其他特定功能，例如結構描述驗證、彙總、索引、全文檢索搜尋、MapReduce 或其他查詢功能？</span><span class="sxs-lookup"><span data-stu-id="91fd8-143">Do you need any other specific features, such as schema validation, aggregation, indexing, full-text search, MapReduce, or other query capabilities?</span></span>

### <a name="non-functional-requirements"></a><span data-ttu-id="91fd8-144">非功能性需求</span><span class="sxs-lookup"><span data-stu-id="91fd8-144">Non-functional requirements</span></span>

- <span data-ttu-id="91fd8-145">**效能和延展性**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-145">**Performance and scalability**.</span></span> <span data-ttu-id="91fd8-146">您的資料效能需求為何？</span><span class="sxs-lookup"><span data-stu-id="91fd8-146">What are your data performance requirements?</span></span> <span data-ttu-id="91fd8-147">您有資料擷取速率和資料處理速率的特殊需求嗎？</span><span class="sxs-lookup"><span data-stu-id="91fd8-147">Do you have specific requirements for data ingestion rates and data processing rates?</span></span> <span data-ttu-id="91fd8-148">針對擷取後的資料查詢和彙總，可接受的回應時間為何？</span><span class="sxs-lookup"><span data-stu-id="91fd8-148">What are the acceptable response times for querying and aggregation of data once ingested?</span></span> <span data-ttu-id="91fd8-149">資料存放區需要相應增加至多大？</span><span class="sxs-lookup"><span data-stu-id="91fd8-149">How large will you need the data store to scale up?</span></span> <span data-ttu-id="91fd8-150">您的工作負載偏向大量讀取或大量寫入？</span><span class="sxs-lookup"><span data-stu-id="91fd8-150">Is your workload more read-heavy or write-heavy?</span></span>

- <span data-ttu-id="91fd8-151">**可靠性**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-151">**Reliability**.</span></span> <span data-ttu-id="91fd8-152">您需要支援的整體 SLA 為何？</span><span class="sxs-lookup"><span data-stu-id="91fd8-152">What overall SLA do you need to support?</span></span> <span data-ttu-id="91fd8-153">您需要提供給資料取用者的容錯層級為何？</span><span class="sxs-lookup"><span data-stu-id="91fd8-153">What level of fault-tolerance do you need to provide for data consumers?</span></span> <span data-ttu-id="91fd8-154">您需要何種備份和還原功能？</span><span class="sxs-lookup"><span data-stu-id="91fd8-154">What kind of backup and restore capabilities do you need?</span></span>

- <span data-ttu-id="91fd8-155">**複寫**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-155">**Replication**.</span></span> <span data-ttu-id="91fd8-156">您的資料是否需要分散至多個複本或區域？</span><span class="sxs-lookup"><span data-stu-id="91fd8-156">Will your data need to be distributed among multiple replicas or regions?</span></span> <span data-ttu-id="91fd8-157">您需要何種資料複寫功能？</span><span class="sxs-lookup"><span data-stu-id="91fd8-157">What kind of data replication capabilities do you require?</span></span>

- <span data-ttu-id="91fd8-158">**限制**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-158">**Limits**.</span></span> <span data-ttu-id="91fd8-159">特定資料存放區的限制是否支援您對規模、連線數目和輸送量的需求？</span><span class="sxs-lookup"><span data-stu-id="91fd8-159">Will the limits of a particular data store support your requirements for scale, number of connections, and throughput?</span></span>

### <a name="management-and-cost"></a><span data-ttu-id="91fd8-160">管理和成本</span><span class="sxs-lookup"><span data-stu-id="91fd8-160">Management and cost</span></span>

- <span data-ttu-id="91fd8-161">**受控服務**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-161">**Managed service**.</span></span> <span data-ttu-id="91fd8-162">除非您需要只能在 IaaS 主控的資料存放區中找到的特定功能，否則請儘可能使用受控資料服務。</span><span class="sxs-lookup"><span data-stu-id="91fd8-162">When possible, use a managed data service, unless you require specific capabilities that can only be found in an IaaS-hosted data store.</span></span>

- <span data-ttu-id="91fd8-163">**區域可用性**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-163">**Region availability**.</span></span> <span data-ttu-id="91fd8-164">針對受控服務，所有 Azure 區域都提供服務嗎嗎？</span><span class="sxs-lookup"><span data-stu-id="91fd8-164">For managed services, is the service available in all Azure regions?</span></span> <span data-ttu-id="91fd8-165">您的解決方案需要裝載在特定的 Azure 區域嗎？</span><span class="sxs-lookup"><span data-stu-id="91fd8-165">Does your solution need to be hosted in certain Azure regions?</span></span>

- <span data-ttu-id="91fd8-166">**可攜性**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-166">**Portability**.</span></span> <span data-ttu-id="91fd8-167">您的資料需要移轉至內部部署、外部資料中心或其他雲端主控環境嗎？</span><span class="sxs-lookup"><span data-stu-id="91fd8-167">Will your data need to migrated to on-premises, external datacenters, or other cloud hosting environments?</span></span>

- <span data-ttu-id="91fd8-168">**授權**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-168">**Licensing**.</span></span> <span data-ttu-id="91fd8-169">您有專屬與 OSS 授權類型的喜好設定嗎？</span><span class="sxs-lookup"><span data-stu-id="91fd8-169">Do you have a preference of a proprietary versus OSS license type?</span></span> <span data-ttu-id="91fd8-170">您可使用的授權類型是否有任何其他外部限制？</span><span class="sxs-lookup"><span data-stu-id="91fd8-170">Are there any other external restrictions on what type of license you can use?</span></span>

- <span data-ttu-id="91fd8-171">**整體成本**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-171">**Overall cost**.</span></span> <span data-ttu-id="91fd8-172">在您的解決方案中使用服務的整體成本為何？</span><span class="sxs-lookup"><span data-stu-id="91fd8-172">What is the overall cost of using the service within your solution?</span></span> <span data-ttu-id="91fd8-173">若要支援您的執行時間和輸送量需求，將需執行多少個執行個體？</span><span class="sxs-lookup"><span data-stu-id="91fd8-173">How many instances will need to run, to support your uptime and throughput requirements?</span></span> <span data-ttu-id="91fd8-174">請考慮此計算中的作業成本。</span><span class="sxs-lookup"><span data-stu-id="91fd8-174">Consider operations costs in this calculation.</span></span> <span data-ttu-id="91fd8-175">建議使用受控服務的其中一個原因，便是可降低作業成本。</span><span class="sxs-lookup"><span data-stu-id="91fd8-175">One reason to prefer managed services is the reduced operational cost.</span></span>

- <span data-ttu-id="91fd8-176">**符合成本效益**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-176">**Cost effectiveness**.</span></span> <span data-ttu-id="91fd8-177">您可以分割資料，以更符合成本效益的方式儲存它嗎？</span><span class="sxs-lookup"><span data-stu-id="91fd8-177">Can you partition your data, to store it more cost effectively?</span></span> <span data-ttu-id="91fd8-178">例如，您可以將大型物件從昂貴的關聯式資料庫移出到物件存放區嗎？</span><span class="sxs-lookup"><span data-stu-id="91fd8-178">For example, can you move large objects out of an expensive relational database into an object store?</span></span>

### <a name="security"></a><span data-ttu-id="91fd8-179">安全性</span><span class="sxs-lookup"><span data-stu-id="91fd8-179">Security</span></span>

- <span data-ttu-id="91fd8-180">**安全性**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-180">**Security**.</span></span> <span data-ttu-id="91fd8-181">您需要何種加密類型？</span><span class="sxs-lookup"><span data-stu-id="91fd8-181">What type of encryption do you require?</span></span> <span data-ttu-id="91fd8-182">您需要待用加密嗎？</span><span class="sxs-lookup"><span data-stu-id="91fd8-182">Do you need encryption at rest?</span></span> <span data-ttu-id="91fd8-183">您要使用何種驗證機制來連線到您的資料？</span><span class="sxs-lookup"><span data-stu-id="91fd8-183">What authentication mechanism do you want to use to connect to your data?</span></span>

- <span data-ttu-id="91fd8-184">**稽核**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-184">**Auditing**.</span></span> <span data-ttu-id="91fd8-185">您需要產生何種稽核記錄？</span><span class="sxs-lookup"><span data-stu-id="91fd8-185">What kind of audit log do you need to generate?</span></span>

- <span data-ttu-id="91fd8-186">**網路需求**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-186">**Networking requirements**.</span></span> <span data-ttu-id="91fd8-187">您需要限制或管理來自其他網路資源對您資料的存取嗎？</span><span class="sxs-lookup"><span data-stu-id="91fd8-187">Do you need to restrict or otherwise manage access to your data from other network resources?</span></span> <span data-ttu-id="91fd8-188">是否有只能從 Azure 環境中存取資料的需求？</span><span class="sxs-lookup"><span data-stu-id="91fd8-188">Does data need to be accessible only from inside the Azure environment?</span></span> <span data-ttu-id="91fd8-189">是否有從特定 IP 位址或子網路存取資料的需求？</span><span class="sxs-lookup"><span data-stu-id="91fd8-189">Does the data need to be accessible from specific IP addresses or subnets?</span></span> <span data-ttu-id="91fd8-190">是否有從裝載於內部部署或其他外部資料中心的應用程式或服務存取資料的需求？</span><span class="sxs-lookup"><span data-stu-id="91fd8-190">Does it need to be accessible from applications or services hosted on-premises or in other external datacenters?</span></span>

### <a name="devops"></a><span data-ttu-id="91fd8-191">DevOps</span><span class="sxs-lookup"><span data-stu-id="91fd8-191">DevOps</span></span>

- <span data-ttu-id="91fd8-192">**技能**。</span><span class="sxs-lookup"><span data-stu-id="91fd8-192">**Skill set**.</span></span> <span data-ttu-id="91fd8-193">您的小組是否有特別擅長使用的特定程式設計語言、作業系統或其他技術？</span><span class="sxs-lookup"><span data-stu-id="91fd8-193">Are there particular programming languages, operating systems, or other technology that your team is particularly adept at using?</span></span> <span data-ttu-id="91fd8-194">您的小組有其他難以使用的技術嗎？</span><span class="sxs-lookup"><span data-stu-id="91fd8-194">Are there others that would be difficult for your team to work with?</span></span>

- <span data-ttu-id="91fd8-195">**用戶端**。您的開發語言有良好的用戶端支援嗎？</span><span class="sxs-lookup"><span data-stu-id="91fd8-195">**Clients** Is there good client support for your development languages?</span></span>

<span data-ttu-id="91fd8-196">下列各節會就工作負載概況、資料類型及使用案例範例，比較各種資料存放區模型。</span><span class="sxs-lookup"><span data-stu-id="91fd8-196">The following sections compare various data store models in terms of workload profile, data types, and example use cases.</span></span>

## <a name="relational-database-management-systems-rdbms"></a><span data-ttu-id="91fd8-197">關聯式資料庫管理系統 (RDBMS)</span><span class="sxs-lookup"><span data-stu-id="91fd8-197">Relational database management systems (RDBMS)</span></span>

<!-- markdownlint-disable MD033 -->

<table>
<tr><td><span data-ttu-id="91fd8-198"><strong>工作負載</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-198"><strong>Workload</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-199">建立新記錄和更新現有的資料會定期發生。</span><span class="sxs-lookup"><span data-stu-id="91fd8-199">Both the creation of new records and updates to existing data happen regularly.</span></span></li>
            <li><span data-ttu-id="91fd8-200">必須在單一交易中完成多項作業。</span><span class="sxs-lookup"><span data-stu-id="91fd8-200">Multiple operations have to be completed in a single transaction.</span></span></li>
            <li><span data-ttu-id="91fd8-201">需要彙總函式來執行交叉資料表。</span><span class="sxs-lookup"><span data-stu-id="91fd8-201">Requires aggregation functions to perform cross-tabulation.</span></span></li>
            <li><span data-ttu-id="91fd8-202">需要與報告工具的強力整合。</span><span class="sxs-lookup"><span data-stu-id="91fd8-202">Strong integration with reporting tools is required.</span></span></li>
            <li><span data-ttu-id="91fd8-203">使用資料庫條件約束來強制執行關聯性。</span><span class="sxs-lookup"><span data-stu-id="91fd8-203">Relationships are enforced using database constraints.</span></span></li>
            <li><span data-ttu-id="91fd8-204">使用索引來最佳化查詢效能。</span><span class="sxs-lookup"><span data-stu-id="91fd8-204">Indexes are used to optimize query performance.</span></span></li>
            <li><span data-ttu-id="91fd8-205">允許存取特定的資料子集。</span><span class="sxs-lookup"><span data-stu-id="91fd8-205">Allows access to specific subsets of data.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-206"><strong>資料類型</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-206"><strong>Data type</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-207">資料為高度正規化。</span><span class="sxs-lookup"><span data-stu-id="91fd8-207">Data is highly normalized.</span></span></li>
            <li><span data-ttu-id="91fd8-208">需要且會強制執行資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="91fd8-208">Database schemas are required and enforced.</span></span></li>
            <li><span data-ttu-id="91fd8-209">資料庫中的資料實體之間有多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="91fd8-209">Many-to-many relationships between data entities in the database.</span></span></li>
            <li><span data-ttu-id="91fd8-210">結構描述中定義了條件約束，而且會加諸於資料庫中的任何資料。</span><span class="sxs-lookup"><span data-stu-id="91fd8-210">Constraints are defined in the schema and imposed on any data in the database.</span></span></li>
            <li><span data-ttu-id="91fd8-211">資料需要高完整性。</span><span class="sxs-lookup"><span data-stu-id="91fd8-211">Data requires high integrity.</span></span> <span data-ttu-id="91fd8-212">需要正確地維護索引和關聯性。</span><span class="sxs-lookup"><span data-stu-id="91fd8-212">Indexes and relationships need to be maintained accurately.</span></span></li>
            <li><span data-ttu-id="91fd8-213">資料需要強式一致性。</span><span class="sxs-lookup"><span data-stu-id="91fd8-213">Data requires strong consistency.</span></span> <span data-ttu-id="91fd8-214">交易的運作方式可確保所有使用者和處理序的所有資料 100% 一致。</span><span class="sxs-lookup"><span data-stu-id="91fd8-214">Transactions operate in a way that ensures all data are 100% consistent for all users and processes.</span></span></li>
            <li><span data-ttu-id="91fd8-215">個別資料項目的大小原本即是針對小至中型大小。</span><span class="sxs-lookup"><span data-stu-id="91fd8-215">Size of individual data entries is intended to be small to medium-sized.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-216"><strong>範例</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-216"><strong>Examples</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-217">企業營運 (人力資本管理、客戶關係管理、企業資源規劃)</span><span class="sxs-lookup"><span data-stu-id="91fd8-217">Line of business  (human capital management, customer relationship management, enterprise resource planning)</span></span></li>
            <li><span data-ttu-id="91fd8-218">庫存管理</span><span class="sxs-lookup"><span data-stu-id="91fd8-218">Inventory management</span></span></li>
            <li><span data-ttu-id="91fd8-219">報表資料庫</span><span class="sxs-lookup"><span data-stu-id="91fd8-219">Reporting database</span></span></li>
            <li><span data-ttu-id="91fd8-220">會計</span><span class="sxs-lookup"><span data-stu-id="91fd8-220">Accounting</span></span></li>
            <li><span data-ttu-id="91fd8-221">資產管理</span><span class="sxs-lookup"><span data-stu-id="91fd8-221">Asset management</span></span></li>
            <li><span data-ttu-id="91fd8-222">資金管理</span><span class="sxs-lookup"><span data-stu-id="91fd8-222">Fund management</span></span></li>
            <li><span data-ttu-id="91fd8-223">訂單管理</span><span class="sxs-lookup"><span data-stu-id="91fd8-223">Order management</span></span></li>
        </ul>
    </td>
</tr>
</table>

## <a name="document-databases"></a><span data-ttu-id="91fd8-224">文件資料庫</span><span class="sxs-lookup"><span data-stu-id="91fd8-224">Document databases</span></span>

<table>
<tr><td><span data-ttu-id="91fd8-225"><strong>工作負載</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-225"><strong>Workload</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-226">一般用途。</span><span class="sxs-lookup"><span data-stu-id="91fd8-226">General purpose.</span></span></li>
            <li><span data-ttu-id="91fd8-227">插入和更新作業很常見。</span><span class="sxs-lookup"><span data-stu-id="91fd8-227">Insert and update operations are common.</span></span> <span data-ttu-id="91fd8-228">建立新記錄和更新現有的資料會定期發生。</span><span class="sxs-lookup"><span data-stu-id="91fd8-228">Both the creation of new records and updates to existing data happen regularly.</span></span></li>
            <li><span data-ttu-id="91fd8-229">沒有物件關聯式本質不相符。</span><span class="sxs-lookup"><span data-stu-id="91fd8-229">No object-relational impedance mismatch.</span></span> <span data-ttu-id="91fd8-230">文件可以更符合應用程式程式碼中使用的物件結構。</span><span class="sxs-lookup"><span data-stu-id="91fd8-230">Documents can better match the object structures used in application code.</span></span></li>
            <li><span data-ttu-id="91fd8-231">較常使用開放式並行存取。</span><span class="sxs-lookup"><span data-stu-id="91fd8-231">Optimistic concurrency is more commonly used.</span></span></li>
            <li><span data-ttu-id="91fd8-232">必須由取用應用程式來修改和處理資料。</span><span class="sxs-lookup"><span data-stu-id="91fd8-232">Data must be modified and processed by consuming application.</span></span></li>
            <li><span data-ttu-id="91fd8-233">資料需在多個欄位中編制索引。</span><span class="sxs-lookup"><span data-stu-id="91fd8-233">Data requires index on multiple fields.</span></span></li>
            <li><span data-ttu-id="91fd8-234">會以單一區塊的形式來擷取與寫入個別文件。</span><span class="sxs-lookup"><span data-stu-id="91fd8-234">Individual documents are retrieved and written as a single block.</span></span></li>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-235"><strong>資料類型</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-235"><strong>Data type</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-236">可以透過反正規化的方式來管理資料。</span><span class="sxs-lookup"><span data-stu-id="91fd8-236">Data can be managed in de-normalized way.</span></span></li>
            <li><span data-ttu-id="91fd8-237">個別文件資料的大小相對較小。</span><span class="sxs-lookup"><span data-stu-id="91fd8-237">Size of individual document data is relatively small.</span></span></li>
            <li><span data-ttu-id="91fd8-238">每個文件類型都可使用自己的結構描述。</span><span class="sxs-lookup"><span data-stu-id="91fd8-238">Each document type can use its own schema.</span></span></li>
            <li><span data-ttu-id="91fd8-239">文件可包含選擇性欄位。</span><span class="sxs-lookup"><span data-stu-id="91fd8-239">Documents can include optional fields.</span></span></li>
            <li><span data-ttu-id="91fd8-240">文件資料是半結構化，表示每個欄位的資料類型未嚴格定義。</span><span class="sxs-lookup"><span data-stu-id="91fd8-240">Document data is semi-structured, meaning that data types of each field are not strictly defined.</span></span></li>
            <li><span data-ttu-id="91fd8-241">支援資料彙總。</span><span class="sxs-lookup"><span data-stu-id="91fd8-241">Data aggregation is supported.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-242"><strong>範例</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-242"><strong>Examples</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-243">產品目錄</span><span class="sxs-lookup"><span data-stu-id="91fd8-243">Product catalog</span></span></li>
            <li><span data-ttu-id="91fd8-244">使用者帳戶</span><span class="sxs-lookup"><span data-stu-id="91fd8-244">User accounts</span></span></li>
            <li><span data-ttu-id="91fd8-245">用料表</span><span class="sxs-lookup"><span data-stu-id="91fd8-245">Bill of materials</span></span></li>
            <li><span data-ttu-id="91fd8-246">個人化</span><span class="sxs-lookup"><span data-stu-id="91fd8-246">Personalization</span></span></li>
            <li><span data-ttu-id="91fd8-247">內容管理</span><span class="sxs-lookup"><span data-stu-id="91fd8-247">Content management</span></span></li>
            <li><span data-ttu-id="91fd8-248">作業資料</span><span class="sxs-lookup"><span data-stu-id="91fd8-248">Operations data</span></span></li>
            <li><span data-ttu-id="91fd8-249">庫存管理</span><span class="sxs-lookup"><span data-stu-id="91fd8-249">Inventory management</span></span></li>
            <li><span data-ttu-id="91fd8-250">交易記錄資料</span><span class="sxs-lookup"><span data-stu-id="91fd8-250">Transaction history data</span></span></li>
            <li><span data-ttu-id="91fd8-251">其他 NoSQL 存放區的具體化檢視。</span><span class="sxs-lookup"><span data-stu-id="91fd8-251">Materialized view of other NoSQL stores.</span></span> <span data-ttu-id="91fd8-252">取代檔案/BLOB 索引。</span><span class="sxs-lookup"><span data-stu-id="91fd8-252">Replaces file/BLOB indexing.</span></span></li>
        </ul>
    </td>
</tr>
</table>

## <a name="keyvalue-stores"></a><span data-ttu-id="91fd8-253">索引鍵/值存放區</span><span class="sxs-lookup"><span data-stu-id="91fd8-253">Key/value stores</span></span>

<table>
<tr><td><span data-ttu-id="91fd8-254"><strong>工作負載</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-254"><strong>Workload</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-255">使用單一識別碼索引鍵 (如字典) 來識別和存取資料。</span><span class="sxs-lookup"><span data-stu-id="91fd8-255">Data is identified and accessed using a single ID key, like a dictionary.</span></span></li>
            <li><span data-ttu-id="91fd8-256">延展性極高。</span><span class="sxs-lookup"><span data-stu-id="91fd8-256">Massively scalable.</span></span></li>
            <li><span data-ttu-id="91fd8-257">不需要聯結、鎖定或聯集。</span><span class="sxs-lookup"><span data-stu-id="91fd8-257">No joins, lock, or unions are required.</span></span></li>
            <li><span data-ttu-id="91fd8-258">不會使用任何彙總機制。</span><span class="sxs-lookup"><span data-stu-id="91fd8-258">No aggregation mechanisms are used.</span></span></li>
            <li><span data-ttu-id="91fd8-259">通常不會使用次要索引。</span><span class="sxs-lookup"><span data-stu-id="91fd8-259">Secondary indexes are generally not used.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-260"><strong>資料類型</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-260"><strong>Data type</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-261">資料大小通常很大。</span><span class="sxs-lookup"><span data-stu-id="91fd8-261">Data size tends to be large.</span></span></li>
            <li><span data-ttu-id="91fd8-262">每個索引鍵都與單一值關聯，也就是非受控資料 BLOB。</span><span class="sxs-lookup"><span data-stu-id="91fd8-262">Each key is associated with a single value, which is an unmanaged data BLOB.</span></span></li>
            <li><span data-ttu-id="91fd8-263">未強制執行任何結構描述。</span><span class="sxs-lookup"><span data-stu-id="91fd8-263">There is no schema enforcement.</span></span></li>
            <li><span data-ttu-id="91fd8-264">實體之間沒有關聯性。</span><span class="sxs-lookup"><span data-stu-id="91fd8-264">No relationships between entities.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-265"><strong>範例</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-265"><strong>Examples</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-266">資料快取</span><span class="sxs-lookup"><span data-stu-id="91fd8-266">Data caching</span></span></li>
            <li><span data-ttu-id="91fd8-267">工作階段管理</span><span class="sxs-lookup"><span data-stu-id="91fd8-267">Session management</span></span></li>
            <li><span data-ttu-id="91fd8-268">使用者的喜好設定和設定檔管理</span><span class="sxs-lookup"><span data-stu-id="91fd8-268">User preference and profile management</span></span></li>
            <li><span data-ttu-id="91fd8-269">產品建議與廣告提供</span><span class="sxs-lookup"><span data-stu-id="91fd8-269">Product recommendation and ad serving</span></span></li>
            <li><span data-ttu-id="91fd8-270">字典</span><span class="sxs-lookup"><span data-stu-id="91fd8-270">Dictionaries</span></span></li>
        </ul>
    </td>
</tr>
</table>

## <a name="graph-databases"></a><span data-ttu-id="91fd8-271">圖表資料庫</span><span class="sxs-lookup"><span data-stu-id="91fd8-271">Graph databases</span></span>

<table>
<tr><td><span data-ttu-id="91fd8-272"><strong>工作負載</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-272"><strong>Workload</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-273">資料項目之間的關聯性非常複雜，相關的資料項目之間包含許多躍點。</span><span class="sxs-lookup"><span data-stu-id="91fd8-273">The relationships between data items are very complex, involving many hops between related data items.</span></span></li>
            <li><span data-ttu-id="91fd8-274">資料項目之間的關聯性為動態，而且會隨時間而變更。</span><span class="sxs-lookup"><span data-stu-id="91fd8-274">The relationship between data items are dynamic and change over time.</span></span></li>
            <li><span data-ttu-id="91fd8-275">物件之間的關聯性具有高優先性，不需要外部索引鍵和聯結即可進行周遊。</span><span class="sxs-lookup"><span data-stu-id="91fd8-275">Relationships between objects are first-class citizens, without requiring foreign-keys and joins to traverse.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-276"><strong>資料類型</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-276"><strong>Data type</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-277">資料由節點和關聯性組成。</span><span class="sxs-lookup"><span data-stu-id="91fd8-277">Data is comprised of nodes and relationships.</span></span></li>
            <li><span data-ttu-id="91fd8-278">節點類似於資料表資料列或 JSON 文件。</span><span class="sxs-lookup"><span data-stu-id="91fd8-278">Nodes are similar to table rows or JSON documents.</span></span></li>
            <li><span data-ttu-id="91fd8-279">關聯性與節點一樣重要，並會直接在查詢語言中公開。</span><span class="sxs-lookup"><span data-stu-id="91fd8-279">Relationships are just as important as nodes, and are exposed directly in the query language.</span></span></li>
            <li><span data-ttu-id="91fd8-280">複合物件 (就像擁有多個電話號碼的人) 通常會分成個別的較小節點，並和可周遊的關聯性結合</span><span class="sxs-lookup"><span data-stu-id="91fd8-280">Composite objects, such as a person with multiple phone numbers, tend to be broken into separate, smaller nodes, combined with traversable relationships</span></span> </li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-281"><strong>範例</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-281"><strong>Examples</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-282">組織圖</span><span class="sxs-lookup"><span data-stu-id="91fd8-282">Organization charts</span></span></li>
            <li><span data-ttu-id="91fd8-283">朋友圈</span><span class="sxs-lookup"><span data-stu-id="91fd8-283">Social graphs</span></span></li>
            <li><span data-ttu-id="91fd8-284">詐騙偵測</span><span class="sxs-lookup"><span data-stu-id="91fd8-284">Fraud detection</span></span></li>
            <li><span data-ttu-id="91fd8-285">分析</span><span class="sxs-lookup"><span data-stu-id="91fd8-285">Analytics</span></span></li>
            <li><span data-ttu-id="91fd8-286">推薦引擎</span><span class="sxs-lookup"><span data-stu-id="91fd8-286">Recommendation engines</span></span></li>
        </ul>
    </td>
</tr>
</table>

## <a name="column-family-databases"></a><span data-ttu-id="91fd8-287">資料行系列資料庫</span><span class="sxs-lookup"><span data-stu-id="91fd8-287">Column-family databases</span></span>

<table>
<tr><td><span data-ttu-id="91fd8-288"><strong>工作負載</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-288"><strong>Workload</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-289">大部分的資料行系列資料庫執行寫入作業的速度非常快。</span><span class="sxs-lookup"><span data-stu-id="91fd8-289">Most column-family databases perform write operations extremely quickly.</span></span></li>
            <li><span data-ttu-id="91fd8-290">更新和刪除作業很少見。</span><span class="sxs-lookup"><span data-stu-id="91fd8-290">Update and delete operations are rare.</span></span></li>
            <li><span data-ttu-id="91fd8-291">專為提供高輸送量和低延遲存取而設計。</span><span class="sxs-lookup"><span data-stu-id="91fd8-291">Designed to provide high throughput and low-latency access.</span></span></li>
            <li><span data-ttu-id="91fd8-292">支援在大量記錄中針對特定一組欄位進行簡單查詢存取。</span><span class="sxs-lookup"><span data-stu-id="91fd8-292">Supports easy query access to a particular set of fields within a much larger record.</span></span></li>
            <li><span data-ttu-id="91fd8-293">延展性極高。</span><span class="sxs-lookup"><span data-stu-id="91fd8-293">Massively scalable.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-294"><strong>資料類型</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-294"><strong>Data type</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-295">資料會儲存在包含索引鍵資料行和一或多個資料行系列的資料表中。</span><span class="sxs-lookup"><span data-stu-id="91fd8-295">Data is stored in tables consisting of a key column and one or more column families.</span></span></li>
            <li><span data-ttu-id="91fd8-296">特定資料行可能會因個別資料列而不同。</span><span class="sxs-lookup"><span data-stu-id="91fd8-296">Specific columns can vary by individual rows.</span></span></li>
            <li><span data-ttu-id="91fd8-297">透過 get 和 put 命令存取個別儲存格</span><span class="sxs-lookup"><span data-stu-id="91fd8-297">Individual cells are accessed via get and put commands</span></span></li>
            <li><span data-ttu-id="91fd8-298">使用 scan 命令傳回多個資料列。</span><span class="sxs-lookup"><span data-stu-id="91fd8-298">Multiple rows are returned using a scan command.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-299"><strong>範例</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-299"><strong>Examples</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-300">建議</span><span class="sxs-lookup"><span data-stu-id="91fd8-300">Recommendations</span></span></li>
            <li><span data-ttu-id="91fd8-301">個人化</span><span class="sxs-lookup"><span data-stu-id="91fd8-301">Personalization</span></span></li>
            <li><span data-ttu-id="91fd8-302">感應器資料</span><span class="sxs-lookup"><span data-stu-id="91fd8-302">Sensor data</span></span></li>
            <li><span data-ttu-id="91fd8-303">遙測</span><span class="sxs-lookup"><span data-stu-id="91fd8-303">Telemetry</span></span></li>
            <li><span data-ttu-id="91fd8-304">訊息</span><span class="sxs-lookup"><span data-stu-id="91fd8-304">Messaging</span></span></li>
            <li><span data-ttu-id="91fd8-305">社交媒體分析</span><span class="sxs-lookup"><span data-stu-id="91fd8-305">Social media analytics</span></span></li>
            <li><span data-ttu-id="91fd8-306">Web Analytics</span><span class="sxs-lookup"><span data-stu-id="91fd8-306">Web analytics</span></span></li>
            <li><span data-ttu-id="91fd8-307">活動監控</span><span class="sxs-lookup"><span data-stu-id="91fd8-307">Activity monitoring</span></span></li>
            <li><span data-ttu-id="91fd8-308">天氣和其他時間序列資料</span><span class="sxs-lookup"><span data-stu-id="91fd8-308">Weather and other time-series data</span></span></li>
        </ul>
    </td>
</tr>
</table>

## <a name="search-engine-databases"></a><span data-ttu-id="91fd8-309">搜尋引擎資料庫</span><span class="sxs-lookup"><span data-stu-id="91fd8-309">Search engine databases</span></span>

<table>
<tr><td><span data-ttu-id="91fd8-310"><strong>工作負載</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-310"><strong>Workload</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-311">索引資料來自多個來源和服務。</span><span class="sxs-lookup"><span data-stu-id="91fd8-311">Indexing data from multiple sources and services.</span></span></li>
            <li><span data-ttu-id="91fd8-312">查詢是臨機操作的，並且可能很複雜。</span><span class="sxs-lookup"><span data-stu-id="91fd8-312">Queries are ad-hoc and can be complex.</span></span></li>
            <li><span data-ttu-id="91fd8-313">需要彙總。</span><span class="sxs-lookup"><span data-stu-id="91fd8-313">Requires aggregation.</span></span></li>
            <li><span data-ttu-id="91fd8-314">需要全文檢索搜尋。</span><span class="sxs-lookup"><span data-stu-id="91fd8-314">Full text search is required.</span></span></li>
            <li><span data-ttu-id="91fd8-315">需要臨機操作自助查詢。</span><span class="sxs-lookup"><span data-stu-id="91fd8-315">Ad hoc self-service query is required.</span></span></li>
            <li><span data-ttu-id="91fd8-316">需要對所有欄位上的索引進行資料分析。</span><span class="sxs-lookup"><span data-stu-id="91fd8-316">Data analysis with index on all fields is required.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-317"><strong>資料類型</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-317"><strong>Data type</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-318">半結構化或非結構化</span><span class="sxs-lookup"><span data-stu-id="91fd8-318">Semi-structured or unstructured</span></span></li>
            <li><span data-ttu-id="91fd8-319">文字</span><span class="sxs-lookup"><span data-stu-id="91fd8-319">Text</span></span></li>
            <li><span data-ttu-id="91fd8-320">具有結構化資料參考的文字</span><span class="sxs-lookup"><span data-stu-id="91fd8-320">Text with reference to structured data</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-321"><strong>範例</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-321"><strong>Examples</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-322">產品類別目錄</span><span class="sxs-lookup"><span data-stu-id="91fd8-322">Product catalogs</span></span></li>
            <li><span data-ttu-id="91fd8-323">網站搜尋</span><span class="sxs-lookup"><span data-stu-id="91fd8-323">Site search</span></span></li>
            <li><span data-ttu-id="91fd8-324">記錄</span><span class="sxs-lookup"><span data-stu-id="91fd8-324">Logging</span></span></li>
            <li><span data-ttu-id="91fd8-325">分析</span><span class="sxs-lookup"><span data-stu-id="91fd8-325">Analytics</span></span></li>
            <li><span data-ttu-id="91fd8-326">購物網站</span><span class="sxs-lookup"><span data-stu-id="91fd8-326">Shopping sites</span></span></li>
        </ul>
    </td>
</tr>
</table>

## <a name="data-warehouse"></a><span data-ttu-id="91fd8-327">資料倉儲</span><span class="sxs-lookup"><span data-stu-id="91fd8-327">Data warehouse</span></span>

<table>
<tr><td><span data-ttu-id="91fd8-328"><strong>工作負載</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-328"><strong>Workload</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-329">資料分析</span><span class="sxs-lookup"><span data-stu-id="91fd8-329">Data analytics</span></span></li>
            <li><span data-ttu-id="91fd8-330">Enterprise BI</span><span class="sxs-lookup"><span data-stu-id="91fd8-330">Enterprise BI</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-331"><strong>資料類型</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-331"><strong>Data type</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-332">來自多個來源的歷程記錄資料。</span><span class="sxs-lookup"><span data-stu-id="91fd8-332">Historical data from multiple sources.</span></span></li>
            <li><span data-ttu-id="91fd8-333">通常在「星型」&quot;&quot;或「雪花式」&quot;&quot;結構描述中進行反正規化，包含事實和維度資料表。</span><span class="sxs-lookup"><span data-stu-id="91fd8-333">Usually denormalized in a &quot;star&quot; or &quot;snowflake&quot; schema, consisting of fact and dimension tables.</span></span></li>
            <li><span data-ttu-id="91fd8-334">通常會根據排程來載入新資料。</span><span class="sxs-lookup"><span data-stu-id="91fd8-334">Usually loaded with new data on a scheduled basis.</span></span></li>
            <li><span data-ttu-id="91fd8-335">維度資料表通常包含實體的多個歷程記錄版本，稱為「緩時變維度」。</span><span class="sxs-lookup"><span data-stu-id="91fd8-335">Dimension tables often include multiple historic versions of an entity, referred to as a <em>slowly changing dimension</em>.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-336"><strong>範例</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-336"><strong>Examples</strong></span></span></td>
    <td><span data-ttu-id="91fd8-337">為分析模型、報表和儀表板提供資料的企業資料倉儲。</span><span class="sxs-lookup"><span data-stu-id="91fd8-337">An enterprise data warehouse that provides data for analytical models, reports, and dashboards.</span></span>
    </td>
</tr>
</table>

## <a name="time-series-databases"></a><span data-ttu-id="91fd8-338">時間序列資料庫</span><span class="sxs-lookup"><span data-stu-id="91fd8-338">Time series databases</span></span>

<table>
<tr><td><span data-ttu-id="91fd8-339"><strong>工作負載</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-339"><strong>Workload</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-340">有壓倒性比例 (95-99%) 的作業為寫入作業。</span><span class="sxs-lookup"><span data-stu-id="91fd8-340">An overwhelming proportion of operations (95-99%) are writes.</span></span></li>
            <li><span data-ttu-id="91fd8-341">記錄通常按照時間順序循序附加。</span><span class="sxs-lookup"><span data-stu-id="91fd8-341">Records are generally appended sequentially in time order.</span></span></li>
            <li><span data-ttu-id="91fd8-342">很少更新。</span><span class="sxs-lookup"><span data-stu-id="91fd8-342">Updates are rare.</span></span></li>
            <li><span data-ttu-id="91fd8-343">刪除會大量發生，並且是針對連續區塊或記錄。</span><span class="sxs-lookup"><span data-stu-id="91fd8-343">Deletes occur in bulk, and are made to contiguous blocks or records.</span></span></li>
            <li><span data-ttu-id="91fd8-344">讀取要求可能大於可用的記憶體。</span><span class="sxs-lookup"><span data-stu-id="91fd8-344">Read requests can be larger than available memory.</span></span></li>
            <li><span data-ttu-id="91fd8-345">同時進行多個讀取很常見。</span><span class="sxs-lookup"><span data-stu-id="91fd8-345">It&#39;s common for multiple reads to occur simultaneously.</span></span></li>
            <li><span data-ttu-id="91fd8-346">以遞增或遞減的時間順序循序讀取資料。</span><span class="sxs-lookup"><span data-stu-id="91fd8-346">Data is read sequentially in either ascending or descending time order.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-347"><strong>資料類型</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-347"><strong>Data type</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-348">作為主索引鍵和排序機制的時間戳記。</span><span class="sxs-lookup"><span data-stu-id="91fd8-348">A time stamp that is used as the primary key and sorting mechanism.</span></span></li>
            <li><span data-ttu-id="91fd8-349">來自項目或項目所代表意義描述的度量。</span><span class="sxs-lookup"><span data-stu-id="91fd8-349">Measurements from the entry or descriptions of what the entry represents.</span></span></li>
            <li><span data-ttu-id="91fd8-350">針對項目的類型、來源和其他資訊，定義額外資訊的標籤。</span><span class="sxs-lookup"><span data-stu-id="91fd8-350">Tags that define additional information about the type, origin, and other information about the entry.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-351"><strong>範例</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-351"><strong>Examples</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-352">監視和事件遙測。</span><span class="sxs-lookup"><span data-stu-id="91fd8-352">Monitoring and event telemetry.</span></span></li>
            <li><span data-ttu-id="91fd8-353">感應器或其他 IoT 資料。</span><span class="sxs-lookup"><span data-stu-id="91fd8-353">Sensor or other IoT data.</span></span></li>
        </ul>
    </td>
</tr>
</table>

## <a name="object-storage"></a><span data-ttu-id="91fd8-354">物件儲存體</span><span class="sxs-lookup"><span data-stu-id="91fd8-354">Object storage</span></span>

<table>
<tr><td><span data-ttu-id="91fd8-355"><strong>工作負載</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-355"><strong>Workload</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-356">由索引鍵識別。</span><span class="sxs-lookup"><span data-stu-id="91fd8-356">Identified by key.</span></span></li>
            <li><span data-ttu-id="91fd8-357">物件可以是可公開存取或非可公開存取的。</span><span class="sxs-lookup"><span data-stu-id="91fd8-357">Objects may be publicly or privately accessible.</span></span></li>
            <li><span data-ttu-id="91fd8-358">內容通常是試算表、影像或視訊檔案等資產。</span><span class="sxs-lookup"><span data-stu-id="91fd8-358">Content is typically an asset such as a spreadsheet, image, or video file.</span></span></li>
            <li><span data-ttu-id="91fd8-359">內容必須為永久性 (持續)，而且在任何應用程式層或虛擬機器外部。</span><span class="sxs-lookup"><span data-stu-id="91fd8-359">Content must be durable (persistent), and external to any application tier or virtual machine.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-360"><strong>資料類型</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-360"><strong>Data type</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-361">資料大小很大。</span><span class="sxs-lookup"><span data-stu-id="91fd8-361">Data size is large.</span></span></li>
            <li><span data-ttu-id="91fd8-362">Blob 資料。</span><span class="sxs-lookup"><span data-stu-id="91fd8-362">Blob data.</span></span></li>
            <li><span data-ttu-id="91fd8-363">值為不透明的。</span><span class="sxs-lookup"><span data-stu-id="91fd8-363">Value is opaque.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-364"><strong>範例</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-364"><strong>Examples</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-365">影像、視訊、Office 文件、PDF</span><span class="sxs-lookup"><span data-stu-id="91fd8-365">Images, videos, office documents, PDFs</span></span></li>
            <li><span data-ttu-id="91fd8-366">CSS、指令碼、CSV</span><span class="sxs-lookup"><span data-stu-id="91fd8-366">CSS, Scripts, CSV</span></span></li>
            <li><span data-ttu-id="91fd8-367">靜態 HTML、JSON</span><span class="sxs-lookup"><span data-stu-id="91fd8-367">Static HTML, JSON</span></span></li>
            <li><span data-ttu-id="91fd8-368">記錄和稽核檔案</span><span class="sxs-lookup"><span data-stu-id="91fd8-368">Log and audit files</span></span></li>
            <li><span data-ttu-id="91fd8-369">資料庫備份</span><span class="sxs-lookup"><span data-stu-id="91fd8-369">Database backups</span></span></li>
        </ul>
    </td>
</tr>
</table>

## <a name="shared-files"></a><span data-ttu-id="91fd8-370">共用檔案</span><span class="sxs-lookup"><span data-stu-id="91fd8-370">Shared files</span></span>

<table>
<tr><td><span data-ttu-id="91fd8-371"><strong>工作負載</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-371"><strong>Workload</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-372">移轉自與檔案系統互動的現有應用程式。</span><span class="sxs-lookup"><span data-stu-id="91fd8-372">Migration from existing apps that interact with the file system.</span></span></li>
            <li><span data-ttu-id="91fd8-373">需要 SMB 介面。</span><span class="sxs-lookup"><span data-stu-id="91fd8-373">Requires SMB interface.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-374"><strong>資料類型</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-374"><strong>Data type</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-375">階層式資料夾集合中的檔案。</span><span class="sxs-lookup"><span data-stu-id="91fd8-375">Files in a hierarchical set of folders.</span></span></li>
            <li><span data-ttu-id="91fd8-376">可使用標準 I/O 程式庫來存取。</span><span class="sxs-lookup"><span data-stu-id="91fd8-376">Accessible with standard I/O libraries.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="91fd8-377"><strong>範例</strong></span><span class="sxs-lookup"><span data-stu-id="91fd8-377"><strong>Examples</strong></span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="91fd8-378">舊版檔案</span><span class="sxs-lookup"><span data-stu-id="91fd8-378">Legacy files</span></span></li>
            <li><span data-ttu-id="91fd8-379">可在多個 VM 或應用程式執行個體之間存取的共用內容</span><span class="sxs-lookup"><span data-stu-id="91fd8-379">Shared content accessible among a number of VMs or app instances</span></span></li>
        </ul>
    </td>
</tr>
</table>

<!-- markdownlint-enable MD033 -->

---
title: 即時處理
description: ''
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: f1054ce5e8c2053aa4f80d8b472604125ba47187
ms.sourcegitcommit: c441fd165e6bebbbbbc19854ec6f3676be9c3b25
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2018
---
# <a name="real-time-processing"></a><span data-ttu-id="0b981-102">即時處理</span><span class="sxs-lookup"><span data-stu-id="0b981-102">Real time processing</span></span>

<span data-ttu-id="0b981-103">即時處理會對資料流進行即時擷取，並在處理資料流時具有最低延遲，進而產生即時 (或接近即時) 報表或自動化回應。</span><span class="sxs-lookup"><span data-stu-id="0b981-103">Real time processing deals with streams of data that are captured in real-time and processed with minimal latency to generate real-time (or near-real-time) reports or automated responses.</span></span> <span data-ttu-id="0b981-104">比方說，即時流量監視解決方案可能會使用感應器資料來偵測高流量。</span><span class="sxs-lookup"><span data-stu-id="0b981-104">For example, a real-time traffic monitoring solution might use sensor data to detect high traffic volumes.</span></span> <span data-ttu-id="0b981-105">這項資料可用於動態更新地圖以顯示擁塞情況，或自動起始高乘載車道或其他流量管理系統。</span><span class="sxs-lookup"><span data-stu-id="0b981-105">This data could be used to dynamically update a map to show congestion, or automatically initiate high-occupancy lanes or other traffic management systems.</span></span>

![](./images/real-time-pipeline.png)

<span data-ttu-id="0b981-106">即時處理是定義成未繫結輸入資料流的處理程序，且處理時需要極短的延遲 (以毫秒或秒為測量單位)。</span><span class="sxs-lookup"><span data-stu-id="0b981-106">Real-time processing is defined as the processing of unbounded stream of input data, with very short latency requirements for processing &mdash; measured in milliseconds or seconds.</span></span> <span data-ttu-id="0b981-107">此內送的資料通常以非結構化或半結構化格式送達 (例如 JSON)，而且具有與[批次處理](./batch-processing.md)相同的處理需求，但具有較短的往返時間來支援即時取用。</span><span class="sxs-lookup"><span data-stu-id="0b981-107">This incoming data typically arrives in an unstructured or semi-structured format, such as JSON, and has the same processing requirements as [batch processing](./batch-processing.md), but with shorter turnaround times to support real-time consumption.</span></span>

<span data-ttu-id="0b981-108">通常會將處理過的資料寫入到分析資料存放區，其最適合用在分析和視覺效果中。</span><span class="sxs-lookup"><span data-stu-id="0b981-108">Processed data is often written to an analytical data store, which is optimized for analytics and visualization.</span></span> <span data-ttu-id="0b981-109">也可以將已處理的資料直接內嵌至分析和報表以用於分析、商業智慧和即時儀表板視覺效果。</span><span class="sxs-lookup"><span data-stu-id="0b981-109">The processed data can also be ingested directly into the analytics and reporting layer for analysis, business intelligence, and real-time dashboard visualization.</span></span>

## <a name="challenges"></a><span data-ttu-id="0b981-110">挑戰</span><span class="sxs-lookup"><span data-stu-id="0b981-110">Challenges</span></span>

<span data-ttu-id="0b981-111">即時內嵌、處理並儲存訊息 (尤其是大量)，是即時處理解決方案中的一個大挑戰。</span><span class="sxs-lookup"><span data-stu-id="0b981-111">One of the big challenges of real-time processing solutions is to ingest, process, and store messages in real time, especially at high volumes.</span></span> <span data-ttu-id="0b981-112">必須以不會封鎖擷取管線的方式處理。</span><span class="sxs-lookup"><span data-stu-id="0b981-112">Processing must be done in such a way that it does not block the ingestion pipeline.</span></span> <span data-ttu-id="0b981-113">資料存放區必須支援大量寫入。</span><span class="sxs-lookup"><span data-stu-id="0b981-113">The data store must support high-volume writes.</span></span> <span data-ttu-id="0b981-114">另一項挑戰是要能快速地依據資料產生動作，例如即時產生警示，或在即時 (或接近即時) 儀表板中呈現資料。</span><span class="sxs-lookup"><span data-stu-id="0b981-114">Another challenge is being able to act on the data quickly, such as generating alerts in real time or presenting the data in a real-time (or near-real-time) dashboard.</span></span>

## <a name="architecture"></a><span data-ttu-id="0b981-115">架構</span><span class="sxs-lookup"><span data-stu-id="0b981-115">Architecture</span></span>

<span data-ttu-id="0b981-116">即時處理架構具有下列邏輯元件。</span><span class="sxs-lookup"><span data-stu-id="0b981-116">A real-time processing architecture has the following logical components.</span></span>

- <span data-ttu-id="0b981-117">**即時訊息擷取。**</span><span class="sxs-lookup"><span data-stu-id="0b981-117">**Real-time message ingestion.**</span></span> <span data-ttu-id="0b981-118">架構必須包含擷取及儲存即時訊息的方式，供串流處理取用者使用。</span><span class="sxs-lookup"><span data-stu-id="0b981-118">The architecture must include a way to capture and store real-time messages to be consumed by a stream processing consumer.</span></span> <span data-ttu-id="0b981-119">在簡單的情況下，此服務可能會實作為簡單的資料存放區，其中的新訊息會存放在資料夾中。</span><span class="sxs-lookup"><span data-stu-id="0b981-119">In simple cases, this service could be implemented as a simple data store in which new messages are deposited in a folder.</span></span> <span data-ttu-id="0b981-120">但是，解決方案通常需要一個作為訊息緩衝區的訊息代理程式，例如 Azure 事件中樞。</span><span class="sxs-lookup"><span data-stu-id="0b981-120">But often the solution requires a message broker, such as Azure Event Hubs, that acts as a buffer for the messages.</span></span> <span data-ttu-id="0b981-121">訊息代理程式應支援向外延展處理和可靠的傳遞。</span><span class="sxs-lookup"><span data-stu-id="0b981-121">The message broker should support scale-out processing and reliable delivery.</span></span>

- <span data-ttu-id="0b981-122">**串流處理**</span><span class="sxs-lookup"><span data-stu-id="0b981-122">**Stream processing.**</span></span> <span data-ttu-id="0b981-123">在擷取即時訊息後，解決方案必須經由篩選、彙總和準備要分析的資料，以便處理這些資料。</span><span class="sxs-lookup"><span data-stu-id="0b981-123">After capturing real-time messages, the solution must process them by filtering, aggregating, and otherwise preparing the data for analysis.</span></span>

- <span data-ttu-id="0b981-124">**分析資料存放區。**</span><span class="sxs-lookup"><span data-stu-id="0b981-124">**Analytical data store.**</span></span> <span data-ttu-id="0b981-125">許多巨量資料解決方案皆設計為準備資料以供分析，然後以可使用分析工具來查詢的結構化格式提供處理過的資料。</span><span class="sxs-lookup"><span data-stu-id="0b981-125">Many big data solutions are designed to prepare data for analysis and then serve the processed data in a structured format that can be queried using analytical tools.</span></span> 

- <span data-ttu-id="0b981-126">**分析和報告。**</span><span class="sxs-lookup"><span data-stu-id="0b981-126">**Analysis and reporting.**</span></span> <span data-ttu-id="0b981-127">大部分巨量資料解決方案的目標，是要透過分析和報告提供資料的深入見解。</span><span class="sxs-lookup"><span data-stu-id="0b981-127">The goal of most big data solutions is to provide insights into the data through analysis and reporting.</span></span> 

## <a name="technology-choices"></a><span data-ttu-id="0b981-128">技術選擇</span><span class="sxs-lookup"><span data-stu-id="0b981-128">Technology choices</span></span>

<span data-ttu-id="0b981-129">下列建議選擇的技術適用於 Azure 中的即時處理解決方案。</span><span class="sxs-lookup"><span data-stu-id="0b981-129">The following technologies are recommended choices for real-time processing solutions in Azure.</span></span>

### <a name="real-time-message-ingestion"></a><span data-ttu-id="0b981-130">即時訊息擷取</span><span class="sxs-lookup"><span data-stu-id="0b981-130">Real-time message ingestion</span></span>

- <span data-ttu-id="0b981-131">**Azure 事件中樞**.</span><span class="sxs-lookup"><span data-stu-id="0b981-131">**Azure Event Hubs**.</span></span> <span data-ttu-id="0b981-132">Azure 事件中樞是訊息佇列解決方案，每秒可內嵌數百萬則事件訊息。</span><span class="sxs-lookup"><span data-stu-id="0b981-132">Azure Event Hubs is a message queuing solution for ingesting millions of event messages per second.</span></span> <span data-ttu-id="0b981-133">擷取的事件資料可由多個取用者平行處理。</span><span class="sxs-lookup"><span data-stu-id="0b981-133">The captured event data can be processed by multiple consumers in parallel.</span></span>
- <span data-ttu-id="0b981-134">**Azure IoT 中樞**。</span><span class="sxs-lookup"><span data-stu-id="0b981-134">**Azure IoT Hub**.</span></span> <span data-ttu-id="0b981-135">Azure IoT 中樞提供網際網路連線裝置與可擴充訊息佇列 (可處理數百萬個同時連線的裝置) 之間的雙向通訊。</span><span class="sxs-lookup"><span data-stu-id="0b981-135">Azure IoT Hub provides bi-directional communication between Internet-connected devices, and a scalable message queue that can handle millions of simultaneously connected devices.</span></span>
- <span data-ttu-id="0b981-136">**Apache Kafka**。</span><span class="sxs-lookup"><span data-stu-id="0b981-136">**Apache Kafka**.</span></span> <span data-ttu-id="0b981-137">Kafka 是開放原始碼訊息佇列和串流處理應用程式，可擴充以處理來自多個訊息產生者的數百萬則訊息 (每秒)，並將它們路由至多位取用者。</span><span class="sxs-lookup"><span data-stu-id="0b981-137">Kafka is an open source message queuing and stream processing application that can scale to handle millions of messages per second from multiple message producers, and route them to multiple consumers.</span></span> <span data-ttu-id="0b981-138">Kafka 可在 Azure 中作為 HDInsight 叢集類型使用。</span><span class="sxs-lookup"><span data-stu-id="0b981-138">Kafka is available in Azure as an HDInsight cluster type.</span></span>

<span data-ttu-id="0b981-139">若需詳細資訊，請參閱[即時訊息擷取](../technology-choices/real-time-ingestion.md)。</span><span class="sxs-lookup"><span data-stu-id="0b981-139">For more information, see [Real-time message ingestion](../technology-choices/real-time-ingestion.md).</span></span>

### <a name="data-storage"></a><span data-ttu-id="0b981-140">資料儲存體</span><span class="sxs-lookup"><span data-stu-id="0b981-140">Data storage</span></span>

- <span data-ttu-id="0b981-141">**Azure 儲存體 Blob 容器**或 **Azure Data Lake Store**。</span><span class="sxs-lookup"><span data-stu-id="0b981-141">**Azure Storage Blob Containers** or **Azure Data Lake Store**.</span></span> <span data-ttu-id="0b981-142">系統通常在訊息代理程式中擷取傳入即時資料 (如上所述)，但在某些情況下，監視資料夾的新檔案，並在檔案建立或更新時處理它們可能較有意義。</span><span class="sxs-lookup"><span data-stu-id="0b981-142">Incoming real-time data is usually captured in a message broker (see above), but in some scenarios, it can make sense to monitor a folder for new files and process them as they are created or updated.</span></span> <span data-ttu-id="0b981-143">此外，許多即時處理解決方案結合串流資料與靜態參考資料 (可儲存在檔案存放區中)。</span><span class="sxs-lookup"><span data-stu-id="0b981-143">Additionally, many real-time processing solutions combine streaming data with static reference data, which can be stored in a file store.</span></span> <span data-ttu-id="0b981-144">最後，檔案儲存體可用來作為所擷取之即時資料的輸出目的地，以便進行封存或在 [lambda 架構](../concepts/big-data.md#lambda-architecture)中進一步批次處理。</span><span class="sxs-lookup"><span data-stu-id="0b981-144">Finally, file storage may be used as an output destination for captured real-time data for archiving, or for further batch processing in a [lambda architecture](../concepts/big-data.md#lambda-architecture).</span></span>

<span data-ttu-id="0b981-145">如需詳細資訊，請參閱[資料儲存體](../technology-choices/data-storage.md)。</span><span class="sxs-lookup"><span data-stu-id="0b981-145">For more information, see [Data storage](../technology-choices/data-storage.md).</span></span>

### <a name="stream-processing"></a><span data-ttu-id="0b981-146">串流處理</span><span class="sxs-lookup"><span data-stu-id="0b981-146">Stream processing</span></span>

- <span data-ttu-id="0b981-147">**Azure 串流分析**。</span><span class="sxs-lookup"><span data-stu-id="0b981-147">**Azure Stream Analytics**.</span></span> <span data-ttu-id="0b981-148">Azure 串流分析可針對未繫結的資料流執行永久查詢。</span><span class="sxs-lookup"><span data-stu-id="0b981-148">Azure Stream Analytics can run perpetual queries against an unbounded stream of data.</span></span> <span data-ttu-id="0b981-149">這些查詢會使用來自儲存體或訊息代理程式的資料流、根據時間範圍篩選和彙總資料，並將結果寫入接收器，例如儲存體、資料庫，或直接寫入 Power BI 中的報表。</span><span class="sxs-lookup"><span data-stu-id="0b981-149">These queries consume streams of data from storage or message brokers, filter and aggregate the data based on temporal windows, and write the results to sinks such as storage, databases, or directly to reports in Power BI.</span></span>
- <span data-ttu-id="0b981-150">**Storm**。</span><span class="sxs-lookup"><span data-stu-id="0b981-150">**Storm**.</span></span> <span data-ttu-id="0b981-151">Apache Storm 是一種使用於串流處理的開放原始碼架構，其使用 Spout 和 Bolt 的拓撲，從即時的串流資料來源中取用、處理及輸出結果。</span><span class="sxs-lookup"><span data-stu-id="0b981-151">Apache Storm is an open source framework for stream processing that uses a topology of spouts and bolts to consume, process, and output the results from real-time streaming data sources.</span></span> <span data-ttu-id="0b981-152">您可以在 Azure HDInsight 叢集中佈建 Storm，並在 Java 或 C# 中實作拓撲。</span><span class="sxs-lookup"><span data-stu-id="0b981-152">You can provision Storm in an Azure HDInsight cluster, and implement a topology in Java or C#.</span></span>
- <span data-ttu-id="0b981-153">**Spark 串流**。</span><span class="sxs-lookup"><span data-stu-id="0b981-153">**Spark Streaming**.</span></span> <span data-ttu-id="0b981-154">Apache Spark 是處理一般資料的開放原始碼分散式平台。</span><span class="sxs-lookup"><span data-stu-id="0b981-154">Apache Spark is an open source distributed platform for general data processing.</span></span> <span data-ttu-id="0b981-155">Spark 提供 Spark 串流 API，您可以在其中以任何支援的 Spark 語言 (包括 Java、Scala 和 Python) 撰寫程式碼。</span><span class="sxs-lookup"><span data-stu-id="0b981-155">Spark provides the Spark Streaming API, in which you can write code in any supported Spark language, including Java, Scala, and Python.</span></span> <span data-ttu-id="0b981-156">Spark 2.0 導入了 Spark 結構化串流 API，可提供更簡單且更一致的程式設計模型。</span><span class="sxs-lookup"><span data-stu-id="0b981-156">Spark 2.0 introduced the Spark Structured Streaming API, which provides a simpler and more consistent programming model.</span></span> <span data-ttu-id="0b981-157">Spark 2.0 可從 Azure HDInsight 叢集取得。</span><span class="sxs-lookup"><span data-stu-id="0b981-157">Spark 2.0 is available in an Azure HDInsight cluster.</span></span>

<span data-ttu-id="0b981-158">如需詳細資訊，請參閱[串流處理](../technology-choices/stream-processing.md)。</span><span class="sxs-lookup"><span data-stu-id="0b981-158">For more information, see [Stream processing](../technology-choices/stream-processing.md).</span></span>

### <a name="analytical-data-store"></a><span data-ttu-id="0b981-159">分析資料存放區</span><span class="sxs-lookup"><span data-stu-id="0b981-159">Analytical data store</span></span>

- <span data-ttu-id="0b981-160">**SQL 資料倉儲**、**HBase**、**Spark** 或 **Hive**。</span><span class="sxs-lookup"><span data-stu-id="0b981-160">**SQL Data Warehouse**, **HBase**, **Spark**, or **Hive**.</span></span> <span data-ttu-id="0b981-161">處理的即時資料可以儲存在關聯式資料庫 (如 Azure SQL 資料倉儲)、NoSQL 存放區 (如 HBase)，或儲存為分散式儲存體上的檔案，Spark 或 Hive 資料表可透過分散式儲存體進行定義和查詢。</span><span class="sxs-lookup"><span data-stu-id="0b981-161">Processed real-time data can be stored in a relational database such Azure SQL Data Warehouse, a NoSQL store such as HBase, or as files in distributed storage over which Spark or Hive tables can be defined and queried.</span></span>

<span data-ttu-id="0b981-162">如需詳細資訊，請參閱[分析資料存放區](../technology-choices/analytical-data-stores.md)。</span><span class="sxs-lookup"><span data-stu-id="0b981-162">For more information, see [Analytical data stores](../technology-choices/analytical-data-stores.md).</span></span>

### <a name="analytics-and-reporting"></a><span data-ttu-id="0b981-163">分析和報告</span><span class="sxs-lookup"><span data-stu-id="0b981-163">Analytics and reporting</span></span>

- <span data-ttu-id="0b981-164">**Azure Analysis Services**、**Power BI** 和 **Microsoft Excel**。</span><span class="sxs-lookup"><span data-stu-id="0b981-164">**Azure Analysis Services**, **Power BI**, and **Microsoft Excel**.</span></span> <span data-ttu-id="0b981-165">經過處理且儲存在分析資料存放區中的即時資料，可用來建立歷程記錄報告和分析 (如同批次處理資料的使用方式)。</span><span class="sxs-lookup"><span data-stu-id="0b981-165">Processed real-time data that is stored in an analytical data store can be used for historical reporting and analysis in the same way as batch processed data.</span></span> <span data-ttu-id="0b981-166">此外，Power BI 可從延遲夠低的分析資料來源，或在某些情況下，直接從串流處理輸出中，發行即時 (或接近即時) 報表和視覺效果。</span><span class="sxs-lookup"><span data-stu-id="0b981-166">Additionally, Power BI can be used to publish real-time (or near-real-time) reports and visualizations from analytical data sources where latency is sufficiently low, or in some cases directly from the stream processing output.</span></span>

<span data-ttu-id="0b981-167">如需詳細資訊，請參閱 [分析和報告](../technology-choices/analysis-visualizations-reporting.md)。</span><span class="sxs-lookup"><span data-stu-id="0b981-167">For more information, see [Analytics and reporting](../technology-choices/analysis-visualizations-reporting.md).</span></span>

<span data-ttu-id="0b981-168">在單純的即時解決方案中，大部分處理協調流程是由訊息擷取和串流處理元件所管理。</span><span class="sxs-lookup"><span data-stu-id="0b981-168">In a purely real-time solution, most of the processing orchestration is managed by the message ingestion and stream processing components.</span></span> <span data-ttu-id="0b981-169">不過，在結合批次處理和即時處理的 lambda 架構中，您可能需要使用協調流程架構 (例如 Azure Data Factory 或 Apache Oozie 和 Sqoop) 來管理所擷取之即時資料的批次工作流程。</span><span class="sxs-lookup"><span data-stu-id="0b981-169">However, in a lambda architecture that combines batch processing and real-time processing, you may need to use an orchestration framework such as Azure Data Factory or Apache Oozie and Sqoop to manage batch workflows for captured real-time data.</span></span>

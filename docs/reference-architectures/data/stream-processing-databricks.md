---
title: 串流處理搭配 Azure Databricks
titleSuffix: Azure Reference Architectures
description: 使用 Azure Databricks 在 Azure 中建立端對端串流處理管線。
author: petertaylor9999
ms.date: 11/30/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18
ms.openlocfilehash: 748b191aeee931d580dd27b1ad54c4f4bd63e369
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243839"
---
# <a name="create-a-stream-processing-pipeline-with-azure-databricks"></a><span data-ttu-id="9c724-103">使用 Azure Databricks 建立串流處理管線</span><span class="sxs-lookup"><span data-stu-id="9c724-103">Create a stream processing pipeline with Azure Databricks</span></span>

<span data-ttu-id="9c724-104">此參考架構顯示端對端[串流處理](/azure/architecture/data-guide/big-data/real-time-processing)管線。</span><span class="sxs-lookup"><span data-stu-id="9c724-104">This reference architecture shows an end-to-end [stream processing](/azure/architecture/data-guide/big-data/real-time-processing) pipeline.</span></span> <span data-ttu-id="9c724-105">此類型的管線有四個階段：擷取、處理、儲存，以及分析和報告。</span><span class="sxs-lookup"><span data-stu-id="9c724-105">This type of pipeline has four stages: ingest, process, store, and analysis and reporting.</span></span> <span data-ttu-id="9c724-106">在此參考架構中，管線會從兩個來源擷取資料、對來自每個資料流的相關記錄執行聯結、擴充結果，並即時計算平均值。</span><span class="sxs-lookup"><span data-stu-id="9c724-106">For this reference architecture, the pipeline ingests data from two sources, performs a join on related records from each stream, enriches the result, and calculates an average in real time.</span></span> <span data-ttu-id="9c724-107">結果會儲存以供進一步分析。</span><span class="sxs-lookup"><span data-stu-id="9c724-107">The results are stored for further analysis.</span></span> <span data-ttu-id="9c724-108">[**部署這個解決方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="9c724-108">[**Deploy this solution**](#deploy-the-solution).</span></span>

![使用 Azure Databricks 的串流處理參考架構](./images/stream-processing-databricks.png)

<span data-ttu-id="9c724-110">**案例**：計程車公司會收集有關每趟計程車車程的資料。</span><span class="sxs-lookup"><span data-stu-id="9c724-110">**Scenario**: A taxi company collects data about each taxi trip.</span></span> <span data-ttu-id="9c724-111">在此案例中，我們假設有兩個不同的裝置會傳送資料。</span><span class="sxs-lookup"><span data-stu-id="9c724-111">For this scenario, we assume there are two separate devices sending data.</span></span> <span data-ttu-id="9c724-112">計程車的其中一個計量會傳送關於每趟車程的資訊，如車程時間、距離和上下車地點等。</span><span class="sxs-lookup"><span data-stu-id="9c724-112">The taxi has a meter that sends information about each ride &mdash; the duration, distance, and pickup and dropoff locations.</span></span> <span data-ttu-id="9c724-113">另一個裝置會接收客戶付款，並傳送費用相關資料。</span><span class="sxs-lookup"><span data-stu-id="9c724-113">A separate device accepts payments from customers and sends data about fares.</span></span> <span data-ttu-id="9c724-114">為了找出乘客數趨勢，計程車公司要即時算出每個鄰近地區、每英里的平均小費。</span><span class="sxs-lookup"><span data-stu-id="9c724-114">To spot ridership trends, the taxi company wants to calculate the average tip per mile driven, in real time, for each neighborhood.</span></span>

## <a name="architecture"></a><span data-ttu-id="9c724-115">架構</span><span class="sxs-lookup"><span data-stu-id="9c724-115">Architecture</span></span>

<span data-ttu-id="9c724-116">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="9c724-116">The architecture consists of the following components.</span></span>

<span data-ttu-id="9c724-117">**資料來源**。</span><span class="sxs-lookup"><span data-stu-id="9c724-117">**Data sources**.</span></span> <span data-ttu-id="9c724-118">在此架構中，有兩個資料來源會即時產生資料流。</span><span class="sxs-lookup"><span data-stu-id="9c724-118">In this architecture, there are two data sources that generate data streams in real time.</span></span> <span data-ttu-id="9c724-119">第一個資料流包含車程資訊，而第二個包含費用資訊。</span><span class="sxs-lookup"><span data-stu-id="9c724-119">The first stream contains ride information, and the second contains fare information.</span></span> <span data-ttu-id="9c724-120">參考架構包含模擬的資料產生器，可讀取一組靜態檔案，並將資料推送到事件中樞。</span><span class="sxs-lookup"><span data-stu-id="9c724-120">The reference architecture includes a simulated data generator that reads from a set of static files and pushes the data to Event Hubs.</span></span> <span data-ttu-id="9c724-121">實際應用程式中的資料來源會是安裝在計程車上的裝置。</span><span class="sxs-lookup"><span data-stu-id="9c724-121">The data sources in a real application would be devices installed in the taxi cabs.</span></span>

<span data-ttu-id="9c724-122">**Azure 事件中樞**.</span><span class="sxs-lookup"><span data-stu-id="9c724-122">**Azure Event Hubs**.</span></span> <span data-ttu-id="9c724-123">[事件中樞](/azure/event-hubs/)是事件擷取服務。</span><span class="sxs-lookup"><span data-stu-id="9c724-123">[Event Hubs](/azure/event-hubs/) is an event ingestion service.</span></span> <span data-ttu-id="9c724-124">此架構使用兩個事件中樞執行個體，分別用於兩個資料來源。</span><span class="sxs-lookup"><span data-stu-id="9c724-124">This architecture uses two event hub instances, one for each data source.</span></span> <span data-ttu-id="9c724-125">每個資料來源會將資料流傳送至相關聯的事件中樞。</span><span class="sxs-lookup"><span data-stu-id="9c724-125">Each data source sends a stream of data to the associated event hub.</span></span>

<span data-ttu-id="9c724-126">**Azure Databricks**。</span><span class="sxs-lookup"><span data-stu-id="9c724-126">**Azure Databricks**.</span></span> <span data-ttu-id="9c724-127">[Databricks](/azure/azure-databricks/) 是一個針對 Microsoft Azure 雲端服務平台進行最佳化的 Apache Spark 分析平台。</span><span class="sxs-lookup"><span data-stu-id="9c724-127">[Databricks](/azure/azure-databricks/) is an Apache Spark-based analytics platform optimized for the Microsoft Azure cloud services platform.</span></span> <span data-ttu-id="9c724-128">Databricks 可用來建立計程車車程與費用資料的相互關聯，並以 Databricks 檔案系統中儲存的鄰近地區資料來擴充相互關聯的資料。</span><span class="sxs-lookup"><span data-stu-id="9c724-128">Databricks is used to correlate of the taxi ride and fare data, and also to enrich the correlated data with neighborhood data stored in the Databricks file system.</span></span>

<span data-ttu-id="9c724-129">**Cosmos DB**。</span><span class="sxs-lookup"><span data-stu-id="9c724-129">**Cosmos DB**.</span></span> <span data-ttu-id="9c724-130">Azure Databricks 作業的輸出是一系列的記錄，會使用 Cassandra API 寫入至 [Cosmos DB](/azure/cosmos-db/)。</span><span class="sxs-lookup"><span data-stu-id="9c724-130">The output from Azure Databricks job is a series of records, which are written to [Cosmos DB](/azure/cosmos-db/) using the Cassandra API.</span></span> <span data-ttu-id="9c724-131">之所以使用 Cassandra API，因為它支援時間序列資料模型化。</span><span class="sxs-lookup"><span data-stu-id="9c724-131">The Cassandra API is used because it supports time series data modeling.</span></span>

<span data-ttu-id="9c724-132">**Azure Log Analytics**。</span><span class="sxs-lookup"><span data-stu-id="9c724-132">**Azure Log Analytics**.</span></span> <span data-ttu-id="9c724-133">[Azure 監視器](/azure/monitoring-and-diagnostics/)所收集的應用程式記錄資料會儲存在 [Log Analytics 工作區](/azure/log-analytics)中。</span><span class="sxs-lookup"><span data-stu-id="9c724-133">Application log data collected by [Azure Monitor](/azure/monitoring-and-diagnostics/) is stored in a [Log Analytics workspace](/azure/log-analytics).</span></span> <span data-ttu-id="9c724-134">Log Analytics 查詢可用來分析和視覺化計量，並可檢查記錄訊息以找出應用程式中的問題。</span><span class="sxs-lookup"><span data-stu-id="9c724-134">Log Analytics queries can be used to analyze and visualize metrics and inspect log messages to identify issues within the application.</span></span>

## <a name="data-ingestion"></a><span data-ttu-id="9c724-135">資料擷取</span><span class="sxs-lookup"><span data-stu-id="9c724-135">Data ingestion</span></span>

<!-- markdownlint-disable MD033 -->

<span data-ttu-id="9c724-136">若要模擬資料來源，此參考架構會使用[紐約市計程車資料](https://uofi.app.box.com/v/NYCtaxidata/folder/2332218797)資料集<sup>[[1]](#note1)</sup>。</span><span class="sxs-lookup"><span data-stu-id="9c724-136">To simulate a data source, this reference architecture uses the [New York City Taxi Data](https://uofi.app.box.com/v/NYCtaxidata/folder/2332218797) dataset<sup>[[1]](#note1)</sup>.</span></span> <span data-ttu-id="9c724-137">此資料集包含紐約市在四年期間的計程車路程資料 (2010 &ndash; 2013)。</span><span class="sxs-lookup"><span data-stu-id="9c724-137">This dataset contains data about taxi trips in New York City over a four-year period (2010 &ndash; 2013).</span></span> <span data-ttu-id="9c724-138">其包含兩種類型的記錄：車程資料和費用資料。</span><span class="sxs-lookup"><span data-stu-id="9c724-138">It contains two types of record: Ride data and fare data.</span></span> <span data-ttu-id="9c724-139">車程資料包括路程持續時間、路程距離和上下車地點。</span><span class="sxs-lookup"><span data-stu-id="9c724-139">Ride data includes trip duration, trip distance, and pickup and dropoff location.</span></span> <span data-ttu-id="9c724-140">費用資料包括費用、稅金和小費金額。</span><span class="sxs-lookup"><span data-stu-id="9c724-140">Fare data includes fare, tax, and tip amounts.</span></span> <span data-ttu-id="9c724-141">在這兩種記錄類型中共同欄位包含計程車牌照號碼、計程車執照和廠商識別碼。</span><span class="sxs-lookup"><span data-stu-id="9c724-141">Common fields in both record types include medallion number, hack license, and vendor ID.</span></span> <span data-ttu-id="9c724-142">這三個欄位可唯一識別一輛計程車加上司機。</span><span class="sxs-lookup"><span data-stu-id="9c724-142">Together these three fields uniquely identify a taxi plus a driver.</span></span> <span data-ttu-id="9c724-143">資料會以 CSV 格式儲存。</span><span class="sxs-lookup"><span data-stu-id="9c724-143">The data is stored in CSV format.</span></span>

> <span data-ttu-id="9c724-144">[1] <span id="note1">Donovan, Brian; Work, Dan (2016)：紐約市計程車車程資料 (2010-2013)。</span><span class="sxs-lookup"><span data-stu-id="9c724-144">[1] <span id="note1">Donovan, Brian; Work, Dan (2016): New York City Taxi Trip Data (2010-2013).</span></span> <span data-ttu-id="9c724-145">伊利諾大學香檳分校。</span><span class="sxs-lookup"><span data-stu-id="9c724-145">University of Illinois at Urbana-Champaign.</span></span> <https://doi.org/10.13012/J8PN93H8>

<!-- markdownlint-enable MD033 -->

<span data-ttu-id="9c724-146">資料產生器為 .NET Core 應用程式，可讀取記錄並將其傳送到 Azure 事件中樞。</span><span class="sxs-lookup"><span data-stu-id="9c724-146">The data generator is a .NET Core application that reads the records and sends them to Azure Event Hubs.</span></span> <span data-ttu-id="9c724-147">產生器會以 JSON 格式傳送車程資料，以 CSV 格式傳送費用資料。</span><span class="sxs-lookup"><span data-stu-id="9c724-147">The generator sends ride data in JSON format and fare data in CSV format.</span></span>

<span data-ttu-id="9c724-148">事件中樞使用[分割區](/azure/event-hubs/event-hubs-features#partitions)來分割資料。</span><span class="sxs-lookup"><span data-stu-id="9c724-148">Event Hubs uses [partitions](/azure/event-hubs/event-hubs-features#partitions) to segment the data.</span></span> <span data-ttu-id="9c724-149">資料分割可讓取用者平行讀取每個分割。</span><span class="sxs-lookup"><span data-stu-id="9c724-149">Partitions allow a consumer to read each partition in parallel.</span></span> <span data-ttu-id="9c724-150">將資料傳送至事件中樞時，您可以明確指定分割區索引鍵。</span><span class="sxs-lookup"><span data-stu-id="9c724-150">When you send data to Event Hubs, you can specify the partition key explicitly.</span></span> <span data-ttu-id="9c724-151">否則，記錄會以循環配置資源的方式指派給分割區。</span><span class="sxs-lookup"><span data-stu-id="9c724-151">Otherwise, records are assigned to partitions in round-robin fashion.</span></span>

<span data-ttu-id="9c724-152">在此案例中，車程資料和費用資料最後應具有指定計程車的同一分割區識別碼。</span><span class="sxs-lookup"><span data-stu-id="9c724-152">In this scenario, ride data and fare data should end up with the same partition ID for a given taxi cab.</span></span> <span data-ttu-id="9c724-153">這可讓 Databricks 在將兩個資料流相互關聯時套用一定程度的平行處理原則。</span><span class="sxs-lookup"><span data-stu-id="9c724-153">This enables Databricks to apply a degree of parallelism when it correlates the two streams.</span></span> <span data-ttu-id="9c724-154">車程資料分割區 *n* 中的記錄會比對到費用資料分割區 *n* 中的記錄。</span><span class="sxs-lookup"><span data-stu-id="9c724-154">A record in partition *n* of the ride data will match a record in partition *n* of the fare data.</span></span>

![使用 Azure Databricks 和事件中樞的串流處理圖表](./images/stream-processing-databricks-eh.png)

<span data-ttu-id="9c724-156">在資料產生器中，這兩種記錄類型的共同資料模型具有 `PartitionKey` 屬性，其為 `Medallion`、`HackLicense` 和 `VendorId` 的串連。</span><span class="sxs-lookup"><span data-stu-id="9c724-156">In the data generator, the common data model for both record types has a `PartitionKey` property that is the concatenation of `Medallion`, `HackLicense`, and `VendorId`.</span></span>

```csharp
public abstract class TaxiData
{
    public TaxiData()
    {
    }

    [JsonProperty]
    public long Medallion { get; set; }

    [JsonProperty]
    public long HackLicense { get; set; }

    [JsonProperty]
    public string VendorId { get; set; }

    [JsonProperty]
    public DateTimeOffset PickupTime { get; set; }

    [JsonIgnore]
    public string PartitionKey
    {
        get => $"{Medallion}_{HackLicense}_{VendorId}";
    }
```

<span data-ttu-id="9c724-157">在傳送至事件中樞時，這個屬性用來提供明確的分割區索引鍵：</span><span class="sxs-lookup"><span data-stu-id="9c724-157">This property is used to provide an explicit partition key when sending to Event Hubs:</span></span>

```csharp
using (var client = pool.GetObject())
{
    return client.Value.SendAsync(new EventData(Encoding.UTF8.GetBytes(
        t.GetData(dataFormat))), t.PartitionKey);
}
```

### <a name="event-hubs"></a><span data-ttu-id="9c724-158">事件中樞</span><span class="sxs-lookup"><span data-stu-id="9c724-158">Event Hubs</span></span>

<span data-ttu-id="9c724-159">事件中樞的輸送量容量會以[輸送量單位](/azure/event-hubs/event-hubs-features#throughput-units)來測量。</span><span class="sxs-lookup"><span data-stu-id="9c724-159">The throughput capacity of Event Hubs is measured in [throughput units](/azure/event-hubs/event-hubs-features#throughput-units).</span></span> <span data-ttu-id="9c724-160">您可以啟用[自動擴充](/azure/event-hubs/event-hubs-auto-inflate)以自動調整事件中樞，這會根據流量 (上限為設定的最大值) 自動調整輸送量單位。</span><span class="sxs-lookup"><span data-stu-id="9c724-160">You can autoscale an event hub by enabling [auto-inflate](/azure/event-hubs/event-hubs-auto-inflate), which automatically scales the throughput units based on traffic, up to a configured maximum.</span></span>

## <a name="stream-processing"></a><span data-ttu-id="9c724-161">串流處理</span><span class="sxs-lookup"><span data-stu-id="9c724-161">Stream processing</span></span>

<span data-ttu-id="9c724-162">在 Azure Databricks 中會以作業執行資料處理。</span><span class="sxs-lookup"><span data-stu-id="9c724-162">In Azure Databricks, data processing is performed by a job.</span></span> <span data-ttu-id="9c724-163">作業會指派給叢集並於其上執行。</span><span class="sxs-lookup"><span data-stu-id="9c724-163">The job is assigned to and runs on a cluster.</span></span> <span data-ttu-id="9c724-164">作業可以是以 Java 撰寫的自訂程式碼或是 Spark [Notebook](https://docs.databricks.com/user-guide/notebooks/index.html)。</span><span class="sxs-lookup"><span data-stu-id="9c724-164">The job can either be custom code written in Java, or a Spark [notebook](https://docs.databricks.com/user-guide/notebooks/index.html).</span></span>

<span data-ttu-id="9c724-165">在此參考架構中，作業是以 Scala 和 Java 撰寫類別的 Java 封存檔。</span><span class="sxs-lookup"><span data-stu-id="9c724-165">In this reference architecture, the job is a Java archive with classes written in both Java and Scala.</span></span> <span data-ttu-id="9c724-166">在指定 Databricks 作業的 Java 封存檔時，Databricks 叢集會指定要執行的類別。</span><span class="sxs-lookup"><span data-stu-id="9c724-166">When specifying the Java archive for a Databricks job, the class is specified for execution by the Databricks cluster.</span></span> <span data-ttu-id="9c724-167">在此，**com.microsoft.pnp.TaxiCabReader** 類別的 **Main** 方法包含資料處理邏輯。</span><span class="sxs-lookup"><span data-stu-id="9c724-167">Here, the **main** method of the **com.microsoft.pnp.TaxiCabReader** class contains the data processing logic.</span></span>

### <a name="reading-the-stream-from-the-two-event-hub-instances"></a><span data-ttu-id="9c724-168">從兩個事件中樞執行個體讀取資料流</span><span class="sxs-lookup"><span data-stu-id="9c724-168">Reading the stream from the two event hub instances</span></span>

<span data-ttu-id="9c724-169">資料處理邏輯會使用 [Spark 結構化串流](https://spark.apache.org/docs/2.1.2/structured-streaming-programming-guide.html)，從兩個 Azure 事件中樞執行個體進行讀取：</span><span class="sxs-lookup"><span data-stu-id="9c724-169">The data processing logic uses [Spark structured streaming](https://spark.apache.org/docs/2.1.2/structured-streaming-programming-guide.html) to read from the two Azure event hub instances:</span></span>

```scala
val rideEventHubOptions = EventHubsConf(rideEventHubConnectionString)
      .setConsumerGroup(conf.taxiRideConsumerGroup())
      .setStartingPosition(EventPosition.fromStartOfStream)
    val rideEvents = spark.readStream
      .format("eventhubs")
      .options(rideEventHubOptions.toMap)
      .load

    val fareEventHubOptions = EventHubsConf(fareEventHubConnectionString)
      .setConsumerGroup(conf.taxiFareConsumerGroup())
      .setStartingPosition(EventPosition.fromStartOfStream)
    val fareEvents = spark.readStream
      .format("eventhubs")
      .options(fareEventHubOptions.toMap)
      .load
```

### <a name="enriching-the-data-with-the-neighborhood-information"></a><span data-ttu-id="9c724-170">以鄰近地區資訊擴充資料</span><span class="sxs-lookup"><span data-stu-id="9c724-170">Enriching the data with the neighborhood information</span></span>

<span data-ttu-id="9c724-171">車程資料包含載客與下車位置的緯度和經度座標。</span><span class="sxs-lookup"><span data-stu-id="9c724-171">The ride data includes the latitude and longitude coordinates of the pick up and drop off locations.</span></span> <span data-ttu-id="9c724-172">雖然這些座標有其效用，但要加以取用並進行分析並不容易。</span><span class="sxs-lookup"><span data-stu-id="9c724-172">While these coordinates are useful, they are not easily consumed for analysis.</span></span> <span data-ttu-id="9c724-173">因此，這項資料會使用讀取自[形狀檔](https://en.wikipedia.org/wiki/Shapefile)的鄰近地區資料進行擴充。</span><span class="sxs-lookup"><span data-stu-id="9c724-173">Therefore, this data is enriched with neighborhood data that is read from a [shapefile](https://en.wikipedia.org/wiki/Shapefile).</span></span>

<span data-ttu-id="9c724-174">形狀檔屬於二進位格式且無法輕易剖析，但 [GeoTools](http://geotools.org/) 程式庫針對地理空間資料而提供的工具即使用形狀檔格式。</span><span class="sxs-lookup"><span data-stu-id="9c724-174">The shapefile format is binary and not easily parsed, but the [GeoTools](http://geotools.org/) library provides tools for geospatial data that use the shapefile format.</span></span> <span data-ttu-id="9c724-175">此程式庫用於 **com.microsoft.pnp.GeoFinder** 類別中，可根據載客和下車座標判斷鄰近地區名稱。</span><span class="sxs-lookup"><span data-stu-id="9c724-175">This library is used in the **com.microsoft.pnp.GeoFinder** class to determine the neighborhood name based on the pick up and drop off coordinates.</span></span>

```scala
val neighborhoodFinder = (lon: Double, lat: Double) => {
      NeighborhoodFinder.getNeighborhood(lon, lat).get()
    }
```

### <a name="joining-the-ride-and-fare-data"></a><span data-ttu-id="9c724-176">聯結車程與費用資料</span><span class="sxs-lookup"><span data-stu-id="9c724-176">Joining the ride and fare data</span></span>

<span data-ttu-id="9c724-177">第一項車程和費用資料會進行轉換：</span><span class="sxs-lookup"><span data-stu-id="9c724-177">First the ride and fare data is transformed:</span></span>

```scala
    val rides = transformedRides
      .filter(r => {
        if (r.isNullAt(r.fieldIndex("errorMessage"))) {
          true
        }
        else {
          malformedRides.add(1)
          false
        }
      })
      .select(
        $"ride.*",
        to_neighborhood($"ride.pickupLon", $"ride.pickupLat")
          .as("pickupNeighborhood"),
        to_neighborhood($"ride.dropoffLon", $"ride.dropoffLat")
          .as("dropoffNeighborhood")
      )
      .withWatermark("pickupTime", conf.taxiRideWatermarkInterval())

    val fares = transformedFares
      .filter(r => {
        if (r.isNullAt(r.fieldIndex("errorMessage"))) {
          true
        }
        else {
          malformedFares.add(1)
          false
        }
      })
      .select(
        $"fare.*",
        $"pickupTime"
      )
      .withWatermark("pickupTime", conf.taxiFareWatermarkInterval())
```

<span data-ttu-id="9c724-178">然後，車程資料會與費用資料相聯結：</span><span class="sxs-lookup"><span data-stu-id="9c724-178">And then the ride data is joined with the fare data:</span></span>

```scala
val mergedTaxiTrip = rides.join(fares, Seq("medallion", "hackLicense", "vendorId", "pickupTime"))
```

### <a name="processing-the-data-and-inserting-into-cosmos-db"></a><span data-ttu-id="9c724-179">處理資料並將其插入 Cosmos DB 中</span><span class="sxs-lookup"><span data-stu-id="9c724-179">Processing the data and inserting into Cosmos DB</span></span>

<span data-ttu-id="9c724-180">每個鄰近地區的平均費用金額均依據指定的時間間隔計算：</span><span class="sxs-lookup"><span data-stu-id="9c724-180">The average fare amount for each neighborhood is calculated for a given time interval:</span></span>

```scala
val maxAvgFarePerNeighborhood = mergedTaxiTrip.selectExpr("medallion", "hackLicense", "vendorId", "pickupTime", "rateCode", "storeAndForwardFlag", "dropoffTime", "passengerCount", "tripTimeInSeconds", "tripDistanceInMiles", "pickupLon", "pickupLat", "dropoffLon", "dropoffLat", "paymentType", "fareAmount", "surcharge", "mtaTax", "tipAmount", "tollsAmount", "totalAmount", "pickupNeighborhood", "dropoffNeighborhood")
      .groupBy(window($"pickupTime", conf.windowInterval()), $"pickupNeighborhood")
      .agg(
        count("*").as("rideCount"),
        sum($"fareAmount").as("totalFareAmount"),
        sum($"tipAmount").as("totalTipAmount")
      )
      .select($"window.start", $"window.end", $"pickupNeighborhood", $"rideCount", $"totalFareAmount", $"totalTipAmount")
```

<span data-ttu-id="9c724-181">得出的結果會插入 Cosmos DB 中：</span><span class="sxs-lookup"><span data-stu-id="9c724-181">Which is then inserted into Cosmos DB:</span></span>

```scala
maxAvgFarePerNeighborhood
      .writeStream
      .queryName("maxAvgFarePerNeighborhood_cassandra_insert")
      .outputMode(OutputMode.Append())
      .foreach(new CassandraSinkForeach(connector))
      .start()
      .awaitTermination()
```

## <a name="security-considerations"></a><span data-ttu-id="9c724-182">安全性考量</span><span class="sxs-lookup"><span data-stu-id="9c724-182">Security considerations</span></span>

<span data-ttu-id="9c724-183">對 Azure 資料庫工作區的存取可使用[管理主控台](https://docs.databricks.com/administration-guide/admin-settings/index.html)來控制。</span><span class="sxs-lookup"><span data-stu-id="9c724-183">Access to the Azure Database workspace is controlled using the [administrator console](https://docs.databricks.com/administration-guide/admin-settings/index.html).</span></span> <span data-ttu-id="9c724-184">管理主控台中包含新增使用者、管理使用者權限，以及設定單一登入的功能。</span><span class="sxs-lookup"><span data-stu-id="9c724-184">The administrator console includes functionality to add users, manage user permissions, and set up single sign-on.</span></span> <span data-ttu-id="9c724-185">工作區、叢集、作業和資料表的存取控制也可以透過管理主控台來設定。</span><span class="sxs-lookup"><span data-stu-id="9c724-185">Access control for workspaces, clusters, jobs, and tables can also be set through the administrator console.</span></span>

### <a name="managing-secrets"></a><span data-ttu-id="9c724-186">管理密碼</span><span class="sxs-lookup"><span data-stu-id="9c724-186">Managing secrets</span></span>

<span data-ttu-id="9c724-187">Azure Databricks 包含[密碼存放區](https://docs.azuredatabricks.net/user-guide/secrets/index.html)，可用來儲存密碼，包括連接字串、存取金鑰、使用者名稱和密碼。</span><span class="sxs-lookup"><span data-stu-id="9c724-187">Azure Databricks includes a [secret store](https://docs.azuredatabricks.net/user-guide/secrets/index.html) that is used to store secrets, including connection strings, access keys, user names, and passwords.</span></span> <span data-ttu-id="9c724-188">Azure Databricks 密碼存放區中的祕密會依**範圍**進行分割：</span><span class="sxs-lookup"><span data-stu-id="9c724-188">Secrets within the Azure Databricks secret store are partitioned by **scopes**:</span></span>

```bash
databricks secrets create-scope --scope "azure-databricks-job"
```

<span data-ttu-id="9c724-189">祕密會在範圍層級新增：</span><span class="sxs-lookup"><span data-stu-id="9c724-189">Secrets are added at the scope level:</span></span>

```bash
databricks secrets put --scope "azure-databricks-job" --key "taxi-ride"
```

> [!NOTE]
> <span data-ttu-id="9c724-190">您可以不要使用原生的 Azure Databricks 範圍，而使用 Azure Key Vault 支援的範圍。</span><span class="sxs-lookup"><span data-stu-id="9c724-190">An Azure Key Vault-backed scope can be used instead of the native Azure Databricks scope.</span></span> <span data-ttu-id="9c724-191">若要深入了解，請參閱 [Azure Key Vault 支援的範圍](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#azure-key-vault-backed-scopes)。</span><span class="sxs-lookup"><span data-stu-id="9c724-191">To learn more, see [Azure Key Vault-backed scopes](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#azure-key-vault-backed-scopes).</span></span>

<span data-ttu-id="9c724-192">在程式碼中，會透過 Azure Databricks [祕密公用程式](https://docs.databricks.com/user-guide/dev-tools/dbutils.html#secrets-utilities)來存取祕密。</span><span class="sxs-lookup"><span data-stu-id="9c724-192">In code, secrets are accessed via the Azure Databricks [secrets utilities](https://docs.databricks.com/user-guide/dev-tools/dbutils.html#secrets-utilities).</span></span>

## <a name="monitoring-considerations"></a><span data-ttu-id="9c724-193">監視功能考量</span><span class="sxs-lookup"><span data-stu-id="9c724-193">Monitoring considerations</span></span>

<span data-ttu-id="9c724-194">Azure Databricks 以 Apache Spark 為基礎，且兩者都以 [log4j](https://logging.apache.org/log4j/2.x/) 作為記錄的標準程式庫。</span><span class="sxs-lookup"><span data-stu-id="9c724-194">Azure Databricks is based on Apache Spark, and both use [log4j](https://logging.apache.org/log4j/2.x/) as the standard library for logging.</span></span> <span data-ttu-id="9c724-195">除了 Apache Spark 所提供的預設記錄外，此參考架構也會將記錄和計量傳送至 [Azure Log Analytics](/azure/log-analytics/)。</span><span class="sxs-lookup"><span data-stu-id="9c724-195">In addition to the default logging provided by Apache Spark, this reference architecture sends logs and metrics to [Azure Log Analytics](/azure/log-analytics/).</span></span>

<span data-ttu-id="9c724-196">**Com.microsoft.pnp.TaxiCabReader** 類別會設定 Apache Spark 記錄系統，以使用 **log4j.properties** 檔案中的值將記錄傳送至 Azure Log Analytics。</span><span class="sxs-lookup"><span data-stu-id="9c724-196">The **com.microsoft.pnp.TaxiCabReader** class configures the Apache Spark logging system to send its logs to Azure Log Analytics using the values in the **log4j.properties** file.</span></span> <span data-ttu-id="9c724-197">雖然 Apache Spark 記錄器訊息是字串，Azure Log Analytics 仍會要求將記錄訊息格式化為 JSON。</span><span class="sxs-lookup"><span data-stu-id="9c724-197">While the Apache Spark logger messages are strings, Azure Log Analytics requires log messages to be formatted as JSON.</span></span> <span data-ttu-id="9c724-198">**com.microsoft.pnp.log4j.LogAnalyticsAppender** 類別會將這些訊息轉換成 JSON：</span><span class="sxs-lookup"><span data-stu-id="9c724-198">The **com.microsoft.pnp.log4j.LogAnalyticsAppender** class transforms these messages to JSON:</span></span>

```scala

    @Override
    protected void append(LoggingEvent loggingEvent) {
        if (this.layout == null) {
            this.setLayout(new JSONLayout());
        }

        String json = this.getLayout().format(loggingEvent);
        try {
            this.client.send(json, this.logType);
        } catch(IOException ioe) {
            LogLog.warn("Error sending LoggingEvent to Log Analytics", ioe);
        }
    }

```

<span data-ttu-id="9c724-199">當 **com.microsoft.pnp.TaxiCabReader** 類別處理車程和費用訊息時，其中任一訊息都有可能因格式不正確而無效。</span><span class="sxs-lookup"><span data-stu-id="9c724-199">As the **com.microsoft.pnp.TaxiCabReader** class processes ride and fare messages, it's possible that either one may be malformed and therefore not valid.</span></span> <span data-ttu-id="9c724-200">在生產環境中，請務必分析這些格式不正確的訊息並找出資料來源的問題，以快速加以修正而避免資料遺失。</span><span class="sxs-lookup"><span data-stu-id="9c724-200">In a production environment, it's important to analyze these malformed messages to identify a problem with the data sources so it can be fixed quickly to prevent data loss.</span></span> <span data-ttu-id="9c724-201">**com.microsoft.pnp.TaxiCabReader** 類別會註冊 Apache Spark Accumulator，用以追蹤格式不正確的費用和車程記錄數目：</span><span class="sxs-lookup"><span data-stu-id="9c724-201">The **com.microsoft.pnp.TaxiCabReader** class registers an Apache Spark Accumulator that keeps track of the number of malformed fare and ride records:</span></span>

```scala
    @transient val appMetrics = new AppMetrics(spark.sparkContext)
    appMetrics.registerGauge("metrics.malformedrides", AppAccumulators.getRideInstance(spark.sparkContext))
    appMetrics.registerGauge("metrics.malformedfares", AppAccumulators.getFareInstance(spark.sparkContext))
    SparkEnv.get.metricsSystem.registerSource(appMetrics)
```

<span data-ttu-id="9c724-202">Apache Spark 會使用 Dropwizard 程式庫來傳送計量，而部分原生 Dropwizard 計量欄位會與 Azure Log Analytics 不相容。</span><span class="sxs-lookup"><span data-stu-id="9c724-202">Apache Spark uses the Dropwizard library to send metrics, and some of the native Dropwizard metrics fields are incompatible with Azure Log Analytics.</span></span> <span data-ttu-id="9c724-203">因此，此參考架構包含自訂的 Dropwizard 接收器和報告程式。</span><span class="sxs-lookup"><span data-stu-id="9c724-203">Therefore, this reference architecture includes a custom Dropwizard sink and reporter.</span></span> <span data-ttu-id="9c724-204">它會以 Azure Log Analytics 所預期的格式將計量格式化。</span><span class="sxs-lookup"><span data-stu-id="9c724-204">It formats the metrics in the format expected by Azure Log Analytics.</span></span> <span data-ttu-id="9c724-205">在 Apache Spark 報告計量時，也會傳送格式不正確的車程和費用資料的自訂計量。</span><span class="sxs-lookup"><span data-stu-id="9c724-205">When Apache Spark reports metrics, the custom metrics for the malformed ride and fare data are also sent.</span></span>

<span data-ttu-id="9c724-206">要記錄到 Azure Log Analytics 工作區的最後一項計量，是 Spark 結構化串流作業進度的累計進度。</span><span class="sxs-lookup"><span data-stu-id="9c724-206">The last metric to be logged to the Azure Log Analytics workspace is the cumulative progress of the Spark Structured Streaming job progress.</span></span> <span data-ttu-id="9c724-207">此動作可使用在 **com.microsoft.pnp.StreamingMetricsListener** 類別中實作的自訂 StreamingQuery 接聽程式來完成。</span><span class="sxs-lookup"><span data-stu-id="9c724-207">This is done using a custom StreamingQuery listener implemented in the **com.microsoft.pnp.StreamingMetricsListener** class.</span></span> <span data-ttu-id="9c724-208">此類別會在作業執行時註冊到 Apache Spark 工作階段：</span><span class="sxs-lookup"><span data-stu-id="9c724-208">This class is registered to the Apache Spark Session when the job runs:</span></span>

```scala
spark.streams.addListener(new StreamingMetricsListener())
```

<span data-ttu-id="9c724-209">每當結構化串流事件發生時，Apache Spark 執行階段就會呼叫 StreamingMetricsListener 中的方法，將記錄訊息和計量傳送至 Azure Log Analytics 工作區。</span><span class="sxs-lookup"><span data-stu-id="9c724-209">The methods in the StreamingMetricsListener are called by the Apache Spark runtime whenever a structured steaming event occurs, sending log messages and metrics to the Azure Log Analytics workspace.</span></span> <span data-ttu-id="9c724-210">您可以在工作區中使用下列查詢來監視應用程式：</span><span class="sxs-lookup"><span data-stu-id="9c724-210">You can use the following queries in your workspace to monitor the application:</span></span>

### <a name="latency-and-throughput-for-streaming-queries"></a><span data-ttu-id="9c724-211">串流查詢的延遲和輸送量</span><span class="sxs-lookup"><span data-stu-id="9c724-211">Latency and throughput for streaming queries</span></span>

```shell
taxijob_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| project  mdc_inputRowsPerSecond_d, mdc_durationms_triggerExecution_d
| render timechart
```

### <a name="exceptions-logged-during-stream-query-execution"></a><span data-ttu-id="9c724-212">在資料流查詢執行期間記錄的例外狀況</span><span class="sxs-lookup"><span data-stu-id="9c724-212">Exceptions logged during stream query execution</span></span>

```shell
taxijob_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| where Level contains "Error"
```

### <a name="accumulation-of-malformed-fare-and-ride-data"></a><span data-ttu-id="9c724-213">格式不正確的費用和車程資料的累計</span><span class="sxs-lookup"><span data-stu-id="9c724-213">Accumulation of malformed fare and ride data</span></span>

```shell
SparkMetric_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| render timechart
| where name_s contains "metrics.malformedrides"

SparkMetric_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| render timechart
| where name_s contains "metrics.malformedfares"
```

### <a name="job-execution-to-trace-resiliency"></a><span data-ttu-id="9c724-214">追蹤復原情形的作業執行</span><span class="sxs-lookup"><span data-stu-id="9c724-214">Job execution to trace resiliency</span></span>

```shell
SparkMetric_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| render timechart
| where name_s contains "driver.DAGScheduler.job.allJobs"
```

## <a name="deploy-the-solution"></a><span data-ttu-id="9c724-215">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="9c724-215">Deploy the solution</span></span>

<span data-ttu-id="9c724-216">若要部署及執行參考實作，請依照 [GitHub 讀我檔案](https://github.com/mspnp/azure-databricks-streaming-analytics)中的步驟。</span><span class="sxs-lookup"><span data-stu-id="9c724-216">To the deploy and run the reference implementation, follow the steps in the [GitHub readme](https://github.com/mspnp/azure-databricks-streaming-analytics).</span></span>

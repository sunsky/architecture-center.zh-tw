---
title: 串流處理搭配 Azure Databricks
titleSuffix: Azure Reference Architectures
description: 使用 Azure Databricks 在 Azure 中建立端對端串流處理管線。
author: petertaylor9999
ms.date: 11/30/2018
ms.custom: seodec18
ms.openlocfilehash: 822a3c448dcc2bdd4ae77ef2a2b7a9ffad633440
ms.sourcegitcommit: 88a68c7e9b6b772172b7faa4b9fd9c061a9f7e9d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53120297"
---
# <a name="create-a-stream-processing-pipeline-with-azure-databricks"></a><span data-ttu-id="dda56-103">使用 Azure Databricks 建立串流處理管線</span><span class="sxs-lookup"><span data-stu-id="dda56-103">Create a stream processing pipeline with Azure Databricks</span></span>

<span data-ttu-id="dda56-104">此參考架構顯示端對端[串流處理](/azure/architecture/data-guide/big-data/real-time-processing)管線。</span><span class="sxs-lookup"><span data-stu-id="dda56-104">This reference architecture shows an end-to-end [stream processing](/azure/architecture/data-guide/big-data/real-time-processing) pipeline.</span></span> <span data-ttu-id="dda56-105">此類型的管線有四個階段：擷取、處理、儲存，以及分析和報告。</span><span class="sxs-lookup"><span data-stu-id="dda56-105">This type of pipeline has four stages: ingest, process, store, and analysis and reporting.</span></span> <span data-ttu-id="dda56-106">在此參考架構中，管線會從兩個來源擷取資料、對來自每個資料流的相關記錄執行聯結、擴充結果，並即時計算平均值。</span><span class="sxs-lookup"><span data-stu-id="dda56-106">For this reference architecture, the pipeline ingests data from two sources, performs a join on related records from each stream, enriches the result, and calculates an average in real time.</span></span> <span data-ttu-id="dda56-107">結果會儲存以供進一步分析。</span><span class="sxs-lookup"><span data-stu-id="dda56-107">The results are stored for further analysis.</span></span> <span data-ttu-id="dda56-108">[**部署這個解決方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="dda56-108">[**Deploy this solution**](#deploy-the-solution).</span></span>

![使用 Azure Databricks 的串流處理參考架構](./images/stream-processing-databricks.png)

<span data-ttu-id="dda56-110">**案例**：計程車公司會收集有關每趟計程車車程的資料。</span><span class="sxs-lookup"><span data-stu-id="dda56-110">**Scenario**: A taxi company collects data about each taxi trip.</span></span> <span data-ttu-id="dda56-111">在此案例中，我們假設有兩個不同的裝置會傳送資料。</span><span class="sxs-lookup"><span data-stu-id="dda56-111">For this scenario, we assume there are two separate devices sending data.</span></span> <span data-ttu-id="dda56-112">計程車的其中一個計量會傳送關於每趟車程的資訊，如車程時間、距離和上下車地點等。</span><span class="sxs-lookup"><span data-stu-id="dda56-112">The taxi has a meter that sends information about each ride &mdash; the duration, distance, and pickup and dropoff locations.</span></span> <span data-ttu-id="dda56-113">另一個裝置會接收客戶付款，並傳送費用相關資料。</span><span class="sxs-lookup"><span data-stu-id="dda56-113">A separate device accepts payments from customers and sends data about fares.</span></span> <span data-ttu-id="dda56-114">為了找出乘客數趨勢，計程車公司想要對每個鄰近地區即時計算出平均每英里的小費。</span><span class="sxs-lookup"><span data-stu-id="dda56-114">To spot ridership trends, the taxi company wants to calculate the average tip per mile driven, in real time, for each neighborhood.</span></span>

## <a name="architecture"></a><span data-ttu-id="dda56-115">架構</span><span class="sxs-lookup"><span data-stu-id="dda56-115">Architecture</span></span>

<span data-ttu-id="dda56-116">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="dda56-116">The architecture consists of the following components.</span></span>

<span data-ttu-id="dda56-117">**資料來源**。</span><span class="sxs-lookup"><span data-stu-id="dda56-117">**Data sources**.</span></span> <span data-ttu-id="dda56-118">在此架構中，有兩個資料來源會即時產生資料流。</span><span class="sxs-lookup"><span data-stu-id="dda56-118">In this architecture, there are two data sources that generate data streams in real time.</span></span> <span data-ttu-id="dda56-119">第一個資料流包含車程資訊，而第二個包含費用資訊。</span><span class="sxs-lookup"><span data-stu-id="dda56-119">The first stream contains ride information, and the second contains fare information.</span></span> <span data-ttu-id="dda56-120">參考架構包含模擬的資料產生器，可讀取一組靜態檔案，並將資料推送到事件中樞。</span><span class="sxs-lookup"><span data-stu-id="dda56-120">The reference architecture includes a simulated data generator that reads from a set of static files and pushes the data to Event Hubs.</span></span> <span data-ttu-id="dda56-121">實際應用程式中的資料來源會是安裝在計程車上的裝置。</span><span class="sxs-lookup"><span data-stu-id="dda56-121">The data sources in a real application would be devices installed in the taxi cabs.</span></span>

<span data-ttu-id="dda56-122">**Azure 事件中樞**.</span><span class="sxs-lookup"><span data-stu-id="dda56-122">**Azure Event Hubs**.</span></span> <span data-ttu-id="dda56-123">[事件中樞](/azure/event-hubs/)是事件擷取服務。</span><span class="sxs-lookup"><span data-stu-id="dda56-123">[Event Hubs](/azure/event-hubs/) is an event ingestion service.</span></span> <span data-ttu-id="dda56-124">此架構使用兩個事件中樞執行個體，分別用於兩個資料來源。</span><span class="sxs-lookup"><span data-stu-id="dda56-124">This architecture uses two event hub instances, one for each data source.</span></span> <span data-ttu-id="dda56-125">每個資料來源會將資料流傳送至相關聯的事件中樞。</span><span class="sxs-lookup"><span data-stu-id="dda56-125">Each data source sends a stream of data to the associated event hub.</span></span>

<span data-ttu-id="dda56-126">**Azure Databricks**。</span><span class="sxs-lookup"><span data-stu-id="dda56-126">**Azure Databricks**.</span></span> <span data-ttu-id="dda56-127">[Databricks](/azure/azure-databricks/) 是一個針對 Microsoft Azure 雲端服務平台進行最佳化的 Apache Spark 分析平台。</span><span class="sxs-lookup"><span data-stu-id="dda56-127">[Databricks](/azure/azure-databricks/) is an Apache Spark-based analytics platform optimized for the Microsoft Azure cloud services platform.</span></span> <span data-ttu-id="dda56-128">Databricks 可用來建立計程車車程與費用資料的相互關聯，並以 Databricks 檔案系統中儲存的鄰近地區資料來擴充相互關聯的資料。</span><span class="sxs-lookup"><span data-stu-id="dda56-128">Databricks is used to correlate of the taxi ride and fare data, and also to enrich the correlated data with neighborhood data stored in the Databricks file system.</span></span>

<span data-ttu-id="dda56-129">**Cosmos DB**。</span><span class="sxs-lookup"><span data-stu-id="dda56-129">**Cosmos DB**.</span></span> <span data-ttu-id="dda56-130">Azure Databricks 作業的輸出是一系列的記錄，會使用 Cassandra API 寫入至 [Cosmos DB](/azure/cosmos-db/)。</span><span class="sxs-lookup"><span data-stu-id="dda56-130">The output from Azure Databricks job is a series of records, which are written to [Cosmos DB](/azure/cosmos-db/) using the Cassandra API.</span></span> <span data-ttu-id="dda56-131">之所以使用 Cassandra API，因為它支援時間序列資料模型化。</span><span class="sxs-lookup"><span data-stu-id="dda56-131">The Cassandra API is used because it supports time series data modeling.</span></span>

<span data-ttu-id="dda56-132">**Azure Log Analytics**。</span><span class="sxs-lookup"><span data-stu-id="dda56-132">**Azure Log Analytics**.</span></span> <span data-ttu-id="dda56-133">[Azure 監視器](/azure/monitoring-and-diagnostics/)所收集的應用程式記錄資料會儲存在 [Log Analytics 工作區](/azure/log-analytics)中。</span><span class="sxs-lookup"><span data-stu-id="dda56-133">Application log data collected by [Azure Monitor](/azure/monitoring-and-diagnostics/) is stored in a [Log Analytics workspace](/azure/log-analytics).</span></span> <span data-ttu-id="dda56-134">Log Analytics 查詢可用來分析和視覺化計量，並可檢查記錄訊息以找出應用程式中的問題。</span><span class="sxs-lookup"><span data-stu-id="dda56-134">Log Analytics queries can be used to analyze and visualize metrics and inspect log messages to identify issues within the application.</span></span>

## <a name="data-ingestion"></a><span data-ttu-id="dda56-135">資料擷取</span><span class="sxs-lookup"><span data-stu-id="dda56-135">Data ingestion</span></span>

<span data-ttu-id="dda56-136">若要模擬資料來源，此參考架構會使用[紐約市計程車資料](https://uofi.app.box.com/v/NYCtaxidata/folder/2332218797)資料集<sup>[[1]](#note1)</sup>。</span><span class="sxs-lookup"><span data-stu-id="dda56-136">To simulate a data source, this reference architecture uses the [New York City Taxi Data](https://uofi.app.box.com/v/NYCtaxidata/folder/2332218797) dataset<sup>[[1]](#note1)</sup>.</span></span> <span data-ttu-id="dda56-137">此資料集包含紐約市在四年期間的計程車路程資料 (2010 &ndash; 2013)。</span><span class="sxs-lookup"><span data-stu-id="dda56-137">This dataset contains data about taxi trips in New York City over a four-year period (2010 &ndash; 2013).</span></span> <span data-ttu-id="dda56-138">其包含兩種類型的記錄：車程資料和費用資料。</span><span class="sxs-lookup"><span data-stu-id="dda56-138">It contains two types of record: Ride data and fare data.</span></span> <span data-ttu-id="dda56-139">車程資料包括路程持續時間、路程距離和上下車地點。</span><span class="sxs-lookup"><span data-stu-id="dda56-139">Ride data includes trip duration, trip distance, and pickup and dropoff location.</span></span> <span data-ttu-id="dda56-140">費用資料包括費用、稅金和小費金額。</span><span class="sxs-lookup"><span data-stu-id="dda56-140">Fare data includes fare, tax, and tip amounts.</span></span> <span data-ttu-id="dda56-141">在這兩種記錄類型中共同欄位包含計程車牌照號碼、計程車執照和廠商識別碼。</span><span class="sxs-lookup"><span data-stu-id="dda56-141">Common fields in both record types include medallion number, hack license, and vendor ID.</span></span> <span data-ttu-id="dda56-142">這三個欄位可唯一識別一輛計程車加上司機。</span><span class="sxs-lookup"><span data-stu-id="dda56-142">Together these three fields uniquely identify a taxi plus a driver.</span></span> <span data-ttu-id="dda56-143">資料會以 CSV 格式儲存。</span><span class="sxs-lookup"><span data-stu-id="dda56-143">The data is stored in CSV format.</span></span>

<span data-ttu-id="dda56-144">資料產生器為 .NET Core 應用程式，可讀取記錄並將其傳送到 Azure 事件中樞。</span><span class="sxs-lookup"><span data-stu-id="dda56-144">The data generator is a .NET Core application that reads the records and sends them to Azure Event Hubs.</span></span> <span data-ttu-id="dda56-145">產生器會以 JSON 格式傳送車程資料，以 CSV 格式傳送費用資料。</span><span class="sxs-lookup"><span data-stu-id="dda56-145">The generator sends ride data in JSON format and fare data in CSV format.</span></span>

<span data-ttu-id="dda56-146">事件中樞使用[分割區](/azure/event-hubs/event-hubs-features#partitions)來分割資料。</span><span class="sxs-lookup"><span data-stu-id="dda56-146">Event Hubs uses [partitions](/azure/event-hubs/event-hubs-features#partitions) to segment the data.</span></span> <span data-ttu-id="dda56-147">資料分割可讓取用者平行讀取每個分割。</span><span class="sxs-lookup"><span data-stu-id="dda56-147">Partitions allow a consumer to read each partition in parallel.</span></span> <span data-ttu-id="dda56-148">將資料傳送至事件中樞時，您可以明確指定分割區索引鍵。</span><span class="sxs-lookup"><span data-stu-id="dda56-148">When you send data to Event Hubs, you can specify the partition key explicitly.</span></span> <span data-ttu-id="dda56-149">否則，記錄會以循環配置資源的方式指派給分割區。</span><span class="sxs-lookup"><span data-stu-id="dda56-149">Otherwise, records are assigned to partitions in round-robin fashion.</span></span>

<span data-ttu-id="dda56-150">在此案例中，車程資料和費用資料最後應具有指定計程車的同一分割區識別碼。</span><span class="sxs-lookup"><span data-stu-id="dda56-150">In this scenario, ride data and fare data should end up with the same partition ID for a given taxi cab.</span></span> <span data-ttu-id="dda56-151">這可讓 Databricks 在將兩個資料流相互關聯時套用一定程度的平行處理原則。</span><span class="sxs-lookup"><span data-stu-id="dda56-151">This enables Databricks to apply a degree of parallelism when it correlates the two streams.</span></span> <span data-ttu-id="dda56-152">車程資料分割區 *n* 中的記錄會比對到費用資料分割區 *n* 中的記錄。</span><span class="sxs-lookup"><span data-stu-id="dda56-152">A record in partition *n* of the ride data will match a record in partition *n* of the fare data.</span></span>

![使用 Azure Databricks 和事件中樞的串流處理圖表](./images/stream-processing-databricks-eh.png)

<span data-ttu-id="dda56-154">在資料產生器中，這兩種記錄類型的共同資料模型具有 `PartitionKey` 屬性，其為 `Medallion`、`HackLicense` 和 `VendorId` 的串連。</span><span class="sxs-lookup"><span data-stu-id="dda56-154">In the data generator, the common data model for both record types has a `PartitionKey` property that is the concatenation of `Medallion`, `HackLicense`, and `VendorId`.</span></span>

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

<span data-ttu-id="dda56-155">在傳送至事件中樞時，這個屬性用來提供明確的分割區索引鍵：</span><span class="sxs-lookup"><span data-stu-id="dda56-155">This property is used to provide an explicit partition key when sending to Event Hubs:</span></span>

```csharp
using (var client = pool.GetObject())
{
    return client.Value.SendAsync(new EventData(Encoding.UTF8.GetBytes(
        t.GetData(dataFormat))), t.PartitionKey);
}
```

### <a name="event-hubs"></a><span data-ttu-id="dda56-156">事件中樞</span><span class="sxs-lookup"><span data-stu-id="dda56-156">Event Hubs</span></span>

<span data-ttu-id="dda56-157">事件中樞的輸送量容量會以[輸送量單位](/azure/event-hubs/event-hubs-features#throughput-units)來測量。</span><span class="sxs-lookup"><span data-stu-id="dda56-157">The throughput capacity of Event Hubs is measured in [throughput units](/azure/event-hubs/event-hubs-features#throughput-units).</span></span> <span data-ttu-id="dda56-158">您可以啟用[自動擴充](/azure/event-hubs/event-hubs-auto-inflate)以自動調整事件中樞，這會根據流量 (上限為設定的最大值) 自動調整輸送量單位。</span><span class="sxs-lookup"><span data-stu-id="dda56-158">You can autoscale an event hub by enabling [auto-inflate](/azure/event-hubs/event-hubs-auto-inflate), which automatically scales the throughput units based on traffic, up to a configured maximum.</span></span>

## <a name="stream-processing"></a><span data-ttu-id="dda56-159">串流處理</span><span class="sxs-lookup"><span data-stu-id="dda56-159">Stream processing</span></span>

<span data-ttu-id="dda56-160">在 Azure Databricks 中會以作業執行資料處理。</span><span class="sxs-lookup"><span data-stu-id="dda56-160">In Azure Databricks, data processing is performed by a job.</span></span> <span data-ttu-id="dda56-161">作業會指派給叢集並於其上執行。</span><span class="sxs-lookup"><span data-stu-id="dda56-161">The job is assigned to and runs on a cluster.</span></span> <span data-ttu-id="dda56-162">作業可以是以 Java 撰寫的自訂程式碼或是 Spark [Notebook](https://docs.databricks.com/user-guide/notebooks/index.html)。</span><span class="sxs-lookup"><span data-stu-id="dda56-162">The job can either be custom code written in Java, or a Spark [notebook](https://docs.databricks.com/user-guide/notebooks/index.html).</span></span>

<span data-ttu-id="dda56-163">在此參考架構中，作業是以 Scala 和 Java 撰寫類別的 Java 封存檔。</span><span class="sxs-lookup"><span data-stu-id="dda56-163">In this reference architecture, the job is a Java archive with classes written in both Java and Scala.</span></span> <span data-ttu-id="dda56-164">在指定 Databricks 作業的 Java 封存檔時，Databricks 叢集會指定要執行的類別。</span><span class="sxs-lookup"><span data-stu-id="dda56-164">When specifying the Java archive for a Databricks job, the class is specified for execution by the Databricks cluster.</span></span> <span data-ttu-id="dda56-165">在此，**com.microsoft.pnp.TaxiCabReader** 類別的 **Main** 方法包含資料處理邏輯。</span><span class="sxs-lookup"><span data-stu-id="dda56-165">Here, the **main** method of the **com.microsoft.pnp.TaxiCabReader** class contains the data processing logic.</span></span>

### <a name="reading-the-stream-from-the-two-event-hub-instances"></a><span data-ttu-id="dda56-166">從兩個事件中樞執行個體讀取資料流</span><span class="sxs-lookup"><span data-stu-id="dda56-166">Reading the stream from the two event hub instances</span></span>

<span data-ttu-id="dda56-167">資料處理邏輯會使用 [Spark 結構化串流](https://spark.apache.org/docs/2.1.2/structured-streaming-programming-guide.html)，從兩個 Azure 事件中樞執行個體進行讀取：</span><span class="sxs-lookup"><span data-stu-id="dda56-167">The data processing logic uses [Spark structured streaming](https://spark.apache.org/docs/2.1.2/structured-streaming-programming-guide.html) to read from the two Azure event hub instances:</span></span>

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

### <a name="enriching-the-data-with-the-neighborhood-information"></a><span data-ttu-id="dda56-168">以鄰近地區資訊擴充資料</span><span class="sxs-lookup"><span data-stu-id="dda56-168">Enriching the data with the neighborhood information</span></span>

<span data-ttu-id="dda56-169">車程資料包含載客與下車位置的緯度和經度座標。</span><span class="sxs-lookup"><span data-stu-id="dda56-169">The ride data includes the latitude and longitude coordinates of the pick up and drop off locations.</span></span> <span data-ttu-id="dda56-170">雖然這些座標有其效用，但要加以取用並進行分析並不容易。</span><span class="sxs-lookup"><span data-stu-id="dda56-170">While these coordinates are useful, they are not easily consumed for analysis.</span></span> <span data-ttu-id="dda56-171">因此，這項資料會使用讀取自[形狀檔](https://en.wikipedia.org/wiki/Shapefile)的鄰近地區資料進行擴充。</span><span class="sxs-lookup"><span data-stu-id="dda56-171">Therefore, this data is enriched with neighborhood data that is read from a [shapefile](https://en.wikipedia.org/wiki/Shapefile).</span></span>

<span data-ttu-id="dda56-172">形狀檔屬於二進位格式且無法輕易剖析，但 [GeoTools](http://geotools.org/) 程式庫針對地理空間資料而提供的工具即使用形狀檔格式。</span><span class="sxs-lookup"><span data-stu-id="dda56-172">The shapefile format is binary and not easily parsed, but the [GeoTools](http://geotools.org/) library provides tools for geospatial data that use the shapefile format.</span></span> <span data-ttu-id="dda56-173">此程式庫用於 **com.microsoft.pnp.GeoFinder** 類別中，可根據載客和下車座標判斷鄰近地區名稱。</span><span class="sxs-lookup"><span data-stu-id="dda56-173">This library is used in the **com.microsoft.pnp.GeoFinder** class to determine the neighborhood name based on the pick up and drop off coordinates.</span></span>

```scala
val neighborhoodFinder = (lon: Double, lat: Double) => {
      NeighborhoodFinder.getNeighborhood(lon, lat).get()
    }
```

### <a name="joining-the-ride-and-fare-data"></a><span data-ttu-id="dda56-174">聯結車程與費用資料</span><span class="sxs-lookup"><span data-stu-id="dda56-174">Joining the ride and fare data</span></span>

<span data-ttu-id="dda56-175">第一項車程和費用資料會進行轉換：</span><span class="sxs-lookup"><span data-stu-id="dda56-175">First the ride and fare data is transformed:</span></span>

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

<span data-ttu-id="dda56-176">然後，車程資料會與費用資料相聯結：</span><span class="sxs-lookup"><span data-stu-id="dda56-176">And then the ride data is joined with the fare data:</span></span>

```scala
val mergedTaxiTrip = rides.join(fares, Seq("medallion", "hackLicense", "vendorId", "pickupTime"))
```

### <a name="processing-the-data-and-inserting-into-cosmos-db"></a><span data-ttu-id="dda56-177">處理資料並將其插入 Cosmos DB 中</span><span class="sxs-lookup"><span data-stu-id="dda56-177">Processing the data and inserting into Cosmos DB</span></span>

<span data-ttu-id="dda56-178">每個鄰近地區的平均費用金額均依據指定的時間間隔計算：</span><span class="sxs-lookup"><span data-stu-id="dda56-178">The average fare amount for each neighborhood is calculated for a given time interval:</span></span>

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

<span data-ttu-id="dda56-179">得出的結果會插入 Cosmos DB 中：</span><span class="sxs-lookup"><span data-stu-id="dda56-179">Which is then inserted into Cosmos DB:</span></span>

```scala
maxAvgFarePerNeighborhood
      .writeStream
      .queryName("maxAvgFarePerNeighborhood_cassandra_insert")
      .outputMode(OutputMode.Append())
      .foreach(new CassandraSinkForeach(connector))
      .start()
      .awaitTermination()
```

## <a name="security-considerations"></a><span data-ttu-id="dda56-180">安全性考量</span><span class="sxs-lookup"><span data-stu-id="dda56-180">Security considerations</span></span>

<span data-ttu-id="dda56-181">對 Azure 資料庫工作區的存取可使用[管理主控台](https://docs.databricks.com/administration-guide/admin-settings/index.html)來控制。</span><span class="sxs-lookup"><span data-stu-id="dda56-181">Access to the Azure Database workspace is controlled using the [administrator console](https://docs.databricks.com/administration-guide/admin-settings/index.html).</span></span> <span data-ttu-id="dda56-182">管理主控台中包含新增使用者、管理使用者權限，以及設定單一登入的功能。</span><span class="sxs-lookup"><span data-stu-id="dda56-182">The administrator console includes functionality to add users, manage user permissions, and set up single sign-on.</span></span> <span data-ttu-id="dda56-183">工作區、叢集、作業和資料表的存取控制也可以透過管理主控台來設定。</span><span class="sxs-lookup"><span data-stu-id="dda56-183">Access control for workspaces, clusters, jobs, and tables can also be set through the administrator console.</span></span>

### <a name="managing-secrets"></a><span data-ttu-id="dda56-184">管理密碼</span><span class="sxs-lookup"><span data-stu-id="dda56-184">Managing secrets</span></span>

<span data-ttu-id="dda56-185">Azure Databricks 包含[密碼存放區](https://docs.azuredatabricks.net/user-guide/secrets/index.html)，可用來儲存密碼，包括連接字串、存取金鑰、使用者名稱和密碼。</span><span class="sxs-lookup"><span data-stu-id="dda56-185">Azure Databricks includes a [secret store](https://docs.azuredatabricks.net/user-guide/secrets/index.html) that is used to store secrets, including connection strings, access keys, user names, and passwords.</span></span> <span data-ttu-id="dda56-186">Azure Databricks 密碼存放區中的祕密會依**範圍**進行分割：</span><span class="sxs-lookup"><span data-stu-id="dda56-186">Secrets within the Azure Databricks secret store are partitioned by **scopes**:</span></span>

```bash
databricks secrets create-scope --scope "azure-databricks-job"
```

<span data-ttu-id="dda56-187">祕密會在範圍層級新增：</span><span class="sxs-lookup"><span data-stu-id="dda56-187">Secrets are added at the scope level:</span></span>

```bash
databricks secrets put --scope "azure-databricks-job" --key "taxi-ride"
```

> [!NOTE]
> <span data-ttu-id="dda56-188">您可以不要使用原生的 Azure Databricks 範圍，而使用 Azure Key Vault 支援的範圍。</span><span class="sxs-lookup"><span data-stu-id="dda56-188">An Azure Key Vault-backed scope can be used instead of the native Azure Databricks scope.</span></span> <span data-ttu-id="dda56-189">若要深入了解，請參閱 [Azure Key Vault 支援的範圍](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#azure-key-vault-backed-scopes)。</span><span class="sxs-lookup"><span data-stu-id="dda56-189">To learn more, see [Azure Key Vault-backed scopes](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#azure-key-vault-backed-scopes).</span></span>

<span data-ttu-id="dda56-190">在程式碼中，會透過 Azure Databricks [祕密公用程式](https://docs.databricks.com/user-guide/dev-tools/dbutils.html#secrets-utilities)來存取祕密。</span><span class="sxs-lookup"><span data-stu-id="dda56-190">In code, secrets are accessed via the Azure Databricks [secrets utilities](https://docs.databricks.com/user-guide/dev-tools/dbutils.html#secrets-utilities).</span></span>

## <a name="monitoring-considerations"></a><span data-ttu-id="dda56-191">監視功能考量</span><span class="sxs-lookup"><span data-stu-id="dda56-191">Monitoring considerations</span></span>

<span data-ttu-id="dda56-192">Azure Databricks 以 Apache Spark 為基礎，且兩者都以 [log4j](https://logging.apache.org/log4j/2.x/) 作為記錄的標準程式庫。</span><span class="sxs-lookup"><span data-stu-id="dda56-192">Azure Databricks is based on Apache Spark, and both use [log4j](https://logging.apache.org/log4j/2.x/) as the standard library for logging.</span></span> <span data-ttu-id="dda56-193">除了 Apache Spark 所提供的預設記錄外，此參考架構也會將記錄和計量傳送至 [Azure Log Analytics](/azure/log-analytics/)。</span><span class="sxs-lookup"><span data-stu-id="dda56-193">In addition to the default logging provided by Apache Spark, this reference architecture sends logs and metrics to [Azure Log Analytics](/azure/log-analytics/).</span></span>

<span data-ttu-id="dda56-194">**Com.microsoft.pnp.TaxiCabReader** 類別會設定 Apache Spark 記錄系統，以使用 **log4j.properties** 檔案中的值將記錄傳送至 Azure Log Analytics。</span><span class="sxs-lookup"><span data-stu-id="dda56-194">The **com.microsoft.pnp.TaxiCabReader** class configures the Apache Spark logging system to send its logs to Azure Log Analytics using the values in the **log4j.properties** file.</span></span> <span data-ttu-id="dda56-195">雖然 Apache Spark 記錄器訊息是字串，Azure Log Analytics 仍會要求將記錄訊息格式化為 JSON。</span><span class="sxs-lookup"><span data-stu-id="dda56-195">While the Apache Spark logger messages are strings, Azure Log Analytics requires log messages to be formatted as JSON.</span></span> <span data-ttu-id="dda56-196">**com.microsoft.pnp.log4j.LogAnalyticsAppender** 類別會將這些訊息轉換成 JSON：</span><span class="sxs-lookup"><span data-stu-id="dda56-196">The **com.microsoft.pnp.log4j.LogAnalyticsAppender** class transforms these messages to JSON:</span></span>

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

<span data-ttu-id="dda56-197">當 **com.microsoft.pnp.TaxiCabReader** 類別處理車程和費用訊息時，其中任一訊息都有可能因格式不正確而無效。</span><span class="sxs-lookup"><span data-stu-id="dda56-197">As the **com.microsoft.pnp.TaxiCabReader** class processes ride and fare messages, it's possible that either one may be malformed and therefore not valid.</span></span> <span data-ttu-id="dda56-198">在生產環境中，請務必分析這些格式不正確的訊息並找出資料來源的問題，以快速加以修正而避免資料遺失。</span><span class="sxs-lookup"><span data-stu-id="dda56-198">In a production environment, it's important to analyze these malformed messages to identify a problem with the data sources so it can be fixed quickly to prevent data loss.</span></span> <span data-ttu-id="dda56-199">**com.microsoft.pnp.TaxiCabReader** 類別會註冊 Apache Spark Accumulator，用以追蹤格式不正確的費用和車程記錄數目：</span><span class="sxs-lookup"><span data-stu-id="dda56-199">The **com.microsoft.pnp.TaxiCabReader** class registers an Apache Spark Accumulator that keeps track of the number of malformed fare and ride records:</span></span>

```scala
    @transient val appMetrics = new AppMetrics(spark.sparkContext)
    appMetrics.registerGauge("metrics.malformedrides", AppAccumulators.getRideInstance(spark.sparkContext))
    appMetrics.registerGauge("metrics.malformedfares", AppAccumulators.getFareInstance(spark.sparkContext))
    SparkEnv.get.metricsSystem.registerSource(appMetrics)
```

<span data-ttu-id="dda56-200">Apache Spark 會使用 Dropwizard 程式庫來傳送計量，而部分原生 Dropwizard 計量欄位會與 Azure Log Analytics 不相容。</span><span class="sxs-lookup"><span data-stu-id="dda56-200">Apache Spark uses the Dropwizard library to send metrics, and some of the native Dropwizard metrics fields are incompatible with Azure Log Analytics.</span></span> <span data-ttu-id="dda56-201">因此，此參考架構包含自訂的 Dropwizard 接收器和報告程式。</span><span class="sxs-lookup"><span data-stu-id="dda56-201">Therefore, this reference architecture includes a custom Dropwizard sink and reporter.</span></span> <span data-ttu-id="dda56-202">它會以 Azure Log Analytics 所預期的格式將計量格式化。</span><span class="sxs-lookup"><span data-stu-id="dda56-202">It formats the metrics in the format expected by Azure Log Analytics.</span></span> <span data-ttu-id="dda56-203">在 Apache Spark 報告計量時，也會傳送格式不正確的車程和費用資料的自訂計量。</span><span class="sxs-lookup"><span data-stu-id="dda56-203">When Apache Spark reports metrics, the custom metrics for the malformed ride and fare data are also sent.</span></span>

<span data-ttu-id="dda56-204">要記錄到 Azure Log Analytics 工作區的最後一項計量，是 Spark 結構化串流作業進度的累計進度。</span><span class="sxs-lookup"><span data-stu-id="dda56-204">The last metric to be logged to the Azure Log Analytics workspace is the cumulative progress of the Spark Structured Streaming job progress.</span></span> <span data-ttu-id="dda56-205">此動作可使用在 **com.microsoft.pnp.StreamingMetricsListener** 類別中實作的自訂 StreamingQuery 接聽程式來完成。</span><span class="sxs-lookup"><span data-stu-id="dda56-205">This is done using a custom StreamingQuery listener implemented in the **com.microsoft.pnp.StreamingMetricsListener** class.</span></span> <span data-ttu-id="dda56-206">此類別會在作業執行時註冊到 Apache Spark 工作階段：</span><span class="sxs-lookup"><span data-stu-id="dda56-206">This class is registered to the Apache Spark Session when the job runs:</span></span>

```scala
spark.streams.addListener(new StreamingMetricsListener())
```

<span data-ttu-id="dda56-207">每當結構化串流事件發生時，Apache Spark 執行階段就會呼叫 StreamingMetricsListener 中的方法，將記錄訊息和計量傳送至 Azure Log Analytics 工作區。</span><span class="sxs-lookup"><span data-stu-id="dda56-207">The methods in the StreamingMetricsListener are called by the Apache Spark runtime whenever a structured steaming event occurs, sending log messages and metrics to the Azure Log Analytics workspace.</span></span> <span data-ttu-id="dda56-208">您可以在工作區中使用下列查詢來監視應用程式：</span><span class="sxs-lookup"><span data-stu-id="dda56-208">You can use the following queries in your workspace to monitor the application:</span></span>

### <a name="latency-and-throughput-for-streaming-queries"></a><span data-ttu-id="dda56-209">串流查詢的延遲和輸送量</span><span class="sxs-lookup"><span data-stu-id="dda56-209">Latency and throughput for streaming queries</span></span>

```shell
taxijob_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| project  mdc_inputRowsPerSecond_d, mdc_durationms_triggerExecution_d
| render timechart
```

### <a name="exceptions-logged-during-stream-query-execution"></a><span data-ttu-id="dda56-210">在資料流查詢執行期間記錄的例外狀況</span><span class="sxs-lookup"><span data-stu-id="dda56-210">Exceptions logged during stream query execution</span></span>

```shell
taxijob_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| where Level contains "Error"
```

### <a name="accumulation-of-malformed-fare-and-ride-data"></a><span data-ttu-id="dda56-211">格式不正確的費用和車程資料的累計</span><span class="sxs-lookup"><span data-stu-id="dda56-211">Accumulation of malformed fare and ride data</span></span>

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

### <a name="job-execution-to-trace-resiliency"></a><span data-ttu-id="dda56-212">追蹤復原情形的作業執行</span><span class="sxs-lookup"><span data-stu-id="dda56-212">Job execution to trace resiliency</span></span>

```shell
SparkMetric_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| render timechart
| where name_s contains "driver.DAGScheduler.job.allJobs"
```

## <a name="deploy-the-solution"></a><span data-ttu-id="dda56-213">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="dda56-213">Deploy the solution</span></span>

<span data-ttu-id="dda56-214">此參考架構的部署可在 [GitHub](https://github.com/mspnp/azure-databricks-streaming-analytics) 上取得。</span><span class="sxs-lookup"><span data-stu-id="dda56-214">A deployment for this reference architecture is available on [GitHub](https://github.com/mspnp/azure-databricks-streaming-analytics).</span></span>

### <a name="prerequisites"></a><span data-ttu-id="dda56-215">必要條件</span><span class="sxs-lookup"><span data-stu-id="dda56-215">Prerequisites</span></span>

1. <span data-ttu-id="dda56-216">複製、派生或下載[串流處理，使用 Azure Databricks](https://github.com/mspnp/azure-databricks-streaming-analytics) GitHub 存放庫。</span><span class="sxs-lookup"><span data-stu-id="dda56-216">Clone, fork, or download the [stream processing with Azure Databricks](https://github.com/mspnp/azure-databricks-streaming-analytics) GitHub repository.</span></span>

2. <span data-ttu-id="dda56-217">安裝 [Docker](https://www.docker.com/) 以執行資料產生器。</span><span class="sxs-lookup"><span data-stu-id="dda56-217">Install [Docker](https://www.docker.com/) to run the data generator.</span></span>

3. <span data-ttu-id="dda56-218">安裝 [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest)。</span><span class="sxs-lookup"><span data-stu-id="dda56-218">Install [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest).</span></span>

4. <span data-ttu-id="dda56-219">安裝 [Databricks CLI](https://docs.databricks.com/user-guide/dev-tools/databricks-cli.html)。</span><span class="sxs-lookup"><span data-stu-id="dda56-219">Install [Databricks CLI](https://docs.databricks.com/user-guide/dev-tools/databricks-cli.html).</span></span>

5. <span data-ttu-id="dda56-220">從命令提示字元、Bash 提示字元或 PowerShell 提示字元中登入 Azure 帳戶，如下所示：</span><span class="sxs-lookup"><span data-stu-id="dda56-220">From a command prompt, bash prompt, or PowerShell prompt, sign into your Azure account as follows:</span></span>
    ```shell
    az login
    ```
6. <span data-ttu-id="dda56-221">安裝 Java IDE 與下列資源：</span><span class="sxs-lookup"><span data-stu-id="dda56-221">Install a Java IDE, with the following resources:</span></span>
    - <span data-ttu-id="dda56-222">JDK 1.8</span><span class="sxs-lookup"><span data-stu-id="dda56-222">JDK 1.8</span></span>
    - <span data-ttu-id="dda56-223">Scala SDK 2.11</span><span class="sxs-lookup"><span data-stu-id="dda56-223">Scala SDK 2.11</span></span>
    - <span data-ttu-id="dda56-224">Maven 3.5.4</span><span class="sxs-lookup"><span data-stu-id="dda56-224">Maven 3.5.4</span></span>

### <a name="download-the-new-york-city-taxi-and-neighborhood-data-files"></a><span data-ttu-id="dda56-225">下載紐約市計程車和鄰近地區資料檔案</span><span class="sxs-lookup"><span data-stu-id="dda56-225">Download the New York City taxi and neighborhood data files</span></span>

1. <span data-ttu-id="dda56-226">在本機檔案系統中複製的 Github 存放庫根目錄中建立名為 `DataFile` 的目錄。</span><span class="sxs-lookup"><span data-stu-id="dda56-226">Create a directory named `DataFile` in the root of the cloned Github repository in your local file system.</span></span>

2. <span data-ttu-id="dda56-227">開啟 web 瀏覽器並巡覽至 https://uofi.app.box.com/v/NYCtaxidata/folder/2332219935。</span><span class="sxs-lookup"><span data-stu-id="dda56-227">Open a web browser and navigate to https://uofi.app.box.com/v/NYCtaxidata/folder/2332219935.</span></span>

3. <span data-ttu-id="dda56-228">按一下此頁面上的 [下載] 按鈕，下載該年度所有計程車資料的 ZIP 檔案。</span><span class="sxs-lookup"><span data-stu-id="dda56-228">Click the **Download** button on this page to download a zip file of all the taxi data for that year.</span></span>

4. <span data-ttu-id="dda56-229">將 ZIP 檔案解壓縮至 `DataFile` 目錄。</span><span class="sxs-lookup"><span data-stu-id="dda56-229">Extract the zip file to the `DataFile` directory.</span></span>

    > [!NOTE]
    > <span data-ttu-id="dda56-230">此 ZIP 檔案包含其他 ZIP 檔案。</span><span class="sxs-lookup"><span data-stu-id="dda56-230">This zip file contains other zip files.</span></span> <span data-ttu-id="dda56-231">請勿解壓縮子系 ZIP 檔案。</span><span class="sxs-lookup"><span data-stu-id="dda56-231">Don't extract the child zip files.</span></span>

    <span data-ttu-id="dda56-232">目錄結構看起來必須如下所示：</span><span class="sxs-lookup"><span data-stu-id="dda56-232">The directory structure must look like the following:</span></span>

    ```shell
    /DataFile
        /FOIL2013
            trip_data_1.zip
            trip_data_2.zip
            trip_data_3.zip
            ...
    ```

5. <span data-ttu-id="dda56-233">開啟 web 瀏覽器並巡覽至 https://www.zillow.com/howto/api/neighborhood-boundaries.htm。</span><span class="sxs-lookup"><span data-stu-id="dda56-233">Open a web browser and navigate to https://www.zillow.com/howto/api/neighborhood-boundaries.htm.</span></span>

6. <span data-ttu-id="dda56-234">按一下 [紐約鄰近地區界限] 以下載檔案。</span><span class="sxs-lookup"><span data-stu-id="dda56-234">Click on **New York Neighborhood Boundaries** to download the file.</span></span>

7. <span data-ttu-id="dda56-235">從瀏覽器的 **downloads** 目錄將 **ZillowNeighborhoods-NY.zip** 檔案複製到 `DataFile` 目錄。</span><span class="sxs-lookup"><span data-stu-id="dda56-235">Copy the **ZillowNeighborhoods-NY.zip** file from your browser's **downloads** directory to the `DataFile` directory.</span></span>

### <a name="deploy-the-azure-resources"></a><span data-ttu-id="dda56-236">部署 Azure 資源</span><span class="sxs-lookup"><span data-stu-id="dda56-236">Deploy the Azure resources</span></span>

1. <span data-ttu-id="dda56-237">從殼層或 Windows 命令提示字元執行下列命令，並遵循登入提示：</span><span class="sxs-lookup"><span data-stu-id="dda56-237">From a shell or Windows Command Prompt, run the following command and follow the sign-in prompt:</span></span>

    ```bash
    az login
    ```

2. <span data-ttu-id="dda56-238">巡覽至 GitHub 存放庫中名為 `azure` 的資料夾：</span><span class="sxs-lookup"><span data-stu-id="dda56-238">Navigate to the folder named `azure` in the GitHub repository:</span></span>

    ```bash
    cd azure
    ```

3. <span data-ttu-id="dda56-239">執行下列命令以部署 Azure 資源：</span><span class="sxs-lookup"><span data-stu-id="dda56-239">Run the following commands to deploy the Azure resources:</span></span>

    ```bash
    export resourceGroup='[Resource group name]'
    export resourceLocation='[Region]'
    export eventHubNamespace='[Event Hubs namespace name]'
    export databricksWorkspaceName='[Azure Databricks workspace name]'
    export cosmosDatabaseAccount='[Cosmos DB database name]'
    export logAnalyticsWorkspaceName='[Log Analytics workspace name]'
    export logAnalyticsWorkspaceRegion='[Log Analytics region]'

    # Create a resource group
    az group create --name $resourceGroup --location $resourceLocation

    # Deploy resources
    az group deployment create --resource-group $resourceGroup \
        --template-file deployresources.json --parameters \
        eventHubNamespace=$eventHubNamespace \
        databricksWorkspaceName=$databricksWorkspaceName \
        cosmosDatabaseAccount=$cosmosDatabaseAccount \
        logAnalyticsWorkspaceName=$logAnalyticsWorkspaceName \
        logAnalyticsWorkspaceRegion=$logAnalyticsWorkspaceRegion
    ```

4. <span data-ttu-id="dda56-240">部署的輸出會在完成後隨即寫入至主控台。</span><span class="sxs-lookup"><span data-stu-id="dda56-240">The output of the deployment is written to the console once complete.</span></span> <span data-ttu-id="dda56-241">請搜尋下列 JSON 的輸出：</span><span class="sxs-lookup"><span data-stu-id="dda56-241">Search the output for the following JSON:</span></span>

```json
"outputs": {
        "cosmosDb": {
          "type": "Object",
          "value": {
            "hostName": <value>,
            "secret": <value>,
            "username": <value>
          }
        },
        "eventHubs": {
          "type": "Object",
          "value": {
            "taxi-fare-eh": <value>,
            "taxi-ride-eh": <value>
          }
        },
        "logAnalytics": {
          "type": "Object",
          "value": {
            "secret": <value>,
            "workspaceId": <value>
          }
        }
},
```

<span data-ttu-id="dda56-242">這些值是將在後續小節中新增至 Databricks 祕密的祕密。</span><span class="sxs-lookup"><span data-stu-id="dda56-242">These values are the secrets that will be added to Databricks secrets in upcoming sections.</span></span> <span data-ttu-id="dda56-243">在您將其新增至這些小節前，請妥善加以保存。</span><span class="sxs-lookup"><span data-stu-id="dda56-243">Keep them secure until you add them in those sections.</span></span>

### <a name="add-a-cassandra-table-to-the-cosmos-db-account"></a><span data-ttu-id="dda56-244">將 Cassandra 資料表新增至 Cosmos DB 帳戶</span><span class="sxs-lookup"><span data-stu-id="dda56-244">Add a Cassandra table to the Cosmos DB Account</span></span>

1. <span data-ttu-id="dda56-245">在 Azure 入口網站中，瀏覽至在上述**部署 Azure 資源**一節中建立的資源群組。</span><span class="sxs-lookup"><span data-stu-id="dda56-245">In the Azure portal, navigate to the resource group created in the **deploy the Azure resources** section above.</span></span> <span data-ttu-id="dda56-246">按一下 [Azure Cosmos DB 帳戶]。</span><span class="sxs-lookup"><span data-stu-id="dda56-246">Click on **Azure Cosmos DB Account**.</span></span> <span data-ttu-id="dda56-247">使用 Cassandra API 建立資料表。</span><span class="sxs-lookup"><span data-stu-id="dda56-247">Create a table with the Cassandra API.</span></span>

2. <span data-ttu-id="dda56-248">在 [概觀] 刀鋒視窗中，按一下 [新增資料表]。</span><span class="sxs-lookup"><span data-stu-id="dda56-248">In the **overview** blade, click **add table**.</span></span>

3. <span data-ttu-id="dda56-249">當 [新增資料表] 刀鋒視窗開啟時，在 [Keyspace 名稱] 文字方塊輸入 `newyorktaxi`。</span><span class="sxs-lookup"><span data-stu-id="dda56-249">When the **add table** blade opens, enter `newyorktaxi` in the **Keyspace name** text box.</span></span>

4. <span data-ttu-id="dda56-250">在 [輸入建立資料表的 CQL 命令] 區段中，於 `newyorktaxi` 旁邊的文字方塊中輸入 `neighborhoodstats`。</span><span class="sxs-lookup"><span data-stu-id="dda56-250">In the **enter CQL command to create the table** section, enter `neighborhoodstats` in the text box beside `newyorktaxi`.</span></span>

5. <span data-ttu-id="dda56-251">在下方的文字方塊中，輸入下列項目：</span><span class="sxs-lookup"><span data-stu-id="dda56-251">In the text box below, enter the following:</span></span>
    ```shell
    (neighborhood text, window_end timestamp, number_of_rides bigint,total_fare_amount double, primary key(neighborhood, window_end))
    ```
6. <span data-ttu-id="dda56-252">在 [輸送量 (1,000 - 1,000,000 RU/s)] 文字方塊中，輸入 `4000` 值。</span><span class="sxs-lookup"><span data-stu-id="dda56-252">In the **Throughput (1,000 - 1,000,000 RU/s)** text box enter the value `4000`.</span></span>

7. <span data-ttu-id="dda56-253">按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="dda56-253">Click **OK**.</span></span>

### <a name="add-the-databricks-secrets-using-the-databricks-cli"></a><span data-ttu-id="dda56-254">使用 Databricks CLI 新增 Databricks 祕密</span><span class="sxs-lookup"><span data-stu-id="dda56-254">Add the Databricks secrets using the Databricks CLI</span></span>

<span data-ttu-id="dda56-255">首先，請輸入 EventHub 的祕密：</span><span class="sxs-lookup"><span data-stu-id="dda56-255">First, enter the secrets for EventHub:</span></span>

1. <span data-ttu-id="dda56-256">使用在必要條件的步驟 2 中安裝的 **Azure Databricks CLI**，建立 Azure Databricks 的祕密範圍：</span><span class="sxs-lookup"><span data-stu-id="dda56-256">Using the **Azure Databricks CLI** installed in step 2 of the prerequisites, create the Azure Databricks secret scope:</span></span>
    ```shell
    databricks secrets create-scope --scope "azure-databricks-job"
    ```
2. <span data-ttu-id="dda56-257">新增計程車車程 EventHub 的祕密：</span><span class="sxs-lookup"><span data-stu-id="dda56-257">Add the secret for the taxi ride EventHub:</span></span>
    ```shell
    databricks secrets put --scope "azure-databricks-job" --key "taxi-ride"
    ```
    <span data-ttu-id="dda56-258">執行後，此命令會開啟 vi 編輯器。</span><span class="sxs-lookup"><span data-stu-id="dda56-258">Once executed, this command opens the vi editor.</span></span> <span data-ttu-id="dda56-259">從*部署 Azure 資源*一節的步驟 4 輸入 **eventHubs** 輸出區段中的 **taxi-ride-eh** 值。</span><span class="sxs-lookup"><span data-stu-id="dda56-259">Enter the **taxi-ride-eh** value from the **eventHubs** output section in step 4 of the *deploy the Azure resources* section.</span></span> <span data-ttu-id="dda56-260">儲存並結束 vi。</span><span class="sxs-lookup"><span data-stu-id="dda56-260">Save and exit vi.</span></span>

3. <span data-ttu-id="dda56-261">新增計程車費用 EventHub 的祕密：</span><span class="sxs-lookup"><span data-stu-id="dda56-261">Add the secret for the taxi fare EventHub:</span></span>
    ```shell
    databricks secrets put --scope "azure-databricks-job" --key "taxi-fare"
    ```
    <span data-ttu-id="dda56-262">執行後，此命令會開啟 vi 編輯器。</span><span class="sxs-lookup"><span data-stu-id="dda56-262">Once executed, this command opens the vi editor.</span></span> <span data-ttu-id="dda56-263">從*部署 Azure 資源*一節的步驟 4 輸入 **eventHubs** 輸出區段中的 **taxi-fare-eh** 值。</span><span class="sxs-lookup"><span data-stu-id="dda56-263">Enter the **taxi-fare-eh** value from the **eventHubs** output section in step 4 of the *deploy the Azure resources* section.</span></span> <span data-ttu-id="dda56-264">儲存並結束 vi。</span><span class="sxs-lookup"><span data-stu-id="dda56-264">Save and exit vi.</span></span>

<span data-ttu-id="dda56-265">接著，輸入 Cosmos DB 的祕密：</span><span class="sxs-lookup"><span data-stu-id="dda56-265">Next, enter the secrets for Cosmos DB:</span></span>

1. <span data-ttu-id="dda56-266">開啟 Azure 入口網站，並瀏覽至在**部署 Azure 資源**一節的步驟 3 中指定的資源群組。</span><span class="sxs-lookup"><span data-stu-id="dda56-266">Open the Azure portal, and navigate to the resource group specified in step 3 of the **deploy the Azure resources** section.</span></span> <span data-ttu-id="dda56-267">按一下 [Azure Cosmos DB 帳戶]。</span><span class="sxs-lookup"><span data-stu-id="dda56-267">Click on the Azure Cosmos DB Account.</span></span>

2. <span data-ttu-id="dda56-268">使用 **Azure Databricks CLI**，新增 Cosmos DB 使用者名稱的秘密：</span><span class="sxs-lookup"><span data-stu-id="dda56-268">Using the **Azure Databricks CLI**, add the secret for the Cosmos DB user name:</span></span>
    ```shell
    databricks secrets put --scope azure-databricks-job --key "cassandra-username"
    ```
<span data-ttu-id="dda56-269">執行後，此命令會開啟 vi 編輯器。</span><span class="sxs-lookup"><span data-stu-id="dda56-269">Once executed, this command opens the vi editor.</span></span> <span data-ttu-id="dda56-270">從*部署 Azure 資源*一節的步驟 4 輸入 **CosmosDb** 輸出區段中的 **username** 值。</span><span class="sxs-lookup"><span data-stu-id="dda56-270">Enter the **username** value from the **CosmosDb** output section in step 4 of the *deploy the Azure resources* section.</span></span> <span data-ttu-id="dda56-271">儲存並結束 vi。</span><span class="sxs-lookup"><span data-stu-id="dda56-271">Save and exit vi.</span></span>

3. <span data-ttu-id="dda56-272">接著，新增 Cosmos DB 密碼的祕密：</span><span class="sxs-lookup"><span data-stu-id="dda56-272">Next, add the secret for the Cosmos DB password:</span></span>
    ```shell
    databricks secrets put --scope azure-databricks-job --key "cassandra-password"
    ```

<span data-ttu-id="dda56-273">執行後，此命令會開啟 vi 編輯器。</span><span class="sxs-lookup"><span data-stu-id="dda56-273">Once executed, this command opens the vi editor.</span></span> <span data-ttu-id="dda56-274">從*部署 Azure 資源*一節的步驟 4 輸入 **CosmosDb** 輸出區段中的 **secret** 值。</span><span class="sxs-lookup"><span data-stu-id="dda56-274">Enter the **secret** value from the **CosmosDb** output section in step 4 of the *deploy the Azure resources* section.</span></span> <span data-ttu-id="dda56-275">儲存並結束 vi。</span><span class="sxs-lookup"><span data-stu-id="dda56-275">Save and exit vi.</span></span>

> [!NOTE]
> <span data-ttu-id="dda56-276">如果使用 [Azure Key Vault 支援的祕密範圍](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#azure-key-vault-backed-scopes)，則必須範圍命名為 **azure-databricks-job**，且祕密的名稱必須與前述名稱完全相同。</span><span class="sxs-lookup"><span data-stu-id="dda56-276">If using an [Azure Key Vault-backed secret scope](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#azure-key-vault-backed-scopes), the scope must be named **azure-databricks-job** and the secrets must have the exact same names as those above.</span></span>

### <a name="add-the-zillow-neighborhoods-data-file-to-the-databricks-file-system"></a><span data-ttu-id="dda56-277">將 Zillow 鄰近地區資料檔案新增至 Databricks 檔案系統</span><span class="sxs-lookup"><span data-stu-id="dda56-277">Add the Zillow Neighborhoods data file to the Databricks file system</span></span>

1. <span data-ttu-id="dda56-278">在 Databricks 檔案系統中建立目錄：</span><span class="sxs-lookup"><span data-stu-id="dda56-278">Create a directory in the Databricks file system:</span></span>
    ```bash
    dbfs mkdirs dbfs:/azure-databricks-jobs
    ```

2. <span data-ttu-id="dda56-279">瀏覽至 `DataFile` 目錄並輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="dda56-279">Navigate to the `DataFile` directory and enter the following:</span></span>
    ```bash
    dbfs cp ZillowNeighborhoods-NY.zip dbfs:/azure-databricks-jobs
    ```

### <a name="add-the-azure-log-analytics-workspace-id-and-primary-key-to-configuration-files"></a><span data-ttu-id="dda56-280">將 Log Analytics 工作區識別碼和主要金鑰新增至組態檔</span><span class="sxs-lookup"><span data-stu-id="dda56-280">Add the Azure Log Analytics workspace ID and primary key to configuration files</span></span>

<span data-ttu-id="dda56-281">在本節中，您將需要 Log Analytics 工作區識別碼和主要金鑰。</span><span class="sxs-lookup"><span data-stu-id="dda56-281">For this section, you require the Log Analytics workspace ID and primary key.</span></span> <span data-ttu-id="dda56-282">工作區識別碼是*部署 Azure 資源*一節步驟 4 中的 **logAnalytics** 輸出區段所包含的 **workspaceId** 值。</span><span class="sxs-lookup"><span data-stu-id="dda56-282">The workspace ID is the **workspaceId** value from the **logAnalytics** output section in step 4 of the *deploy the Azure resources* section.</span></span> <span data-ttu-id="dda56-283">主要金鑰是輸出區段中的 **secret**。</span><span class="sxs-lookup"><span data-stu-id="dda56-283">The primary key is the **secret** from the output section.</span></span>

1. <span data-ttu-id="dda56-284">若要設定 log4j 記錄，請開啟 `\azure\AzureDataBricksJob\src\main\resources\com\microsoft\pnp\azuredatabricksjob\log4j.properties`。</span><span class="sxs-lookup"><span data-stu-id="dda56-284">To configure log4j logging, open `\azure\AzureDataBricksJob\src\main\resources\com\microsoft\pnp\azuredatabricksjob\log4j.properties`.</span></span> <span data-ttu-id="dda56-285">請編輯以下兩個值：</span><span class="sxs-lookup"><span data-stu-id="dda56-285">Edit the following two values:</span></span>
    ```shell
    log4j.appender.A1.workspaceId=<Log Analytics workspace ID>
    log4j.appender.A1.secret=<Log Analytics primary key>
    ```

2. <span data-ttu-id="dda56-286">若要設定自訂記錄，請開啟 `\azure\azure-databricks-monitoring\scripts\metrics.properties`。</span><span class="sxs-lookup"><span data-stu-id="dda56-286">To configure custom logging, open `\azure\azure-databricks-monitoring\scripts\metrics.properties`.</span></span> <span data-ttu-id="dda56-287">請編輯以下兩個值：</span><span class="sxs-lookup"><span data-stu-id="dda56-287">Edit the following two values:</span></span>
    ```shell
    *.sink.loganalytics.workspaceId=<Log Analytics workspace ID>
    *.sink.loganalytics.secret=<Log Analytics primary key>
    ```

### <a name="build-the-jar-files-for-the-databricks-job-and-databricks-monitoring"></a><span data-ttu-id="dda56-288">建置 Databricks 作業和 Databricks 監視的 .jar 檔案</span><span class="sxs-lookup"><span data-stu-id="dda56-288">Build the .jar files for the Databricks job and Databricks monitoring</span></span>

1. <span data-ttu-id="dda56-289">使用您的 Java IDE，匯入根目錄中名為 **pom.xml** 的 Maven 專案檔。</span><span class="sxs-lookup"><span data-stu-id="dda56-289">Use your Java IDE to import the Maven project file named **pom.xml** located in the root directory.</span></span>

2. <span data-ttu-id="dda56-290">執行清除組建。</span><span class="sxs-lookup"><span data-stu-id="dda56-290">Perform a clean build.</span></span> <span data-ttu-id="dda56-291">此組建的輸出是名為 **azure-databricks-job-1.0-SNAPSHOT.jar** 和 **azure-databricks-monitoring-0.9.jar** 的檔案。</span><span class="sxs-lookup"><span data-stu-id="dda56-291">The output of this build is files named **azure-databricks-job-1.0-SNAPSHOT.jar** and **azure-databricks-monitoring-0.9.jar**.</span></span>

### <a name="configure-custom-logging-for-the-databricks-job"></a><span data-ttu-id="dda56-292">設定 Databricks 作業的自訂記錄</span><span class="sxs-lookup"><span data-stu-id="dda56-292">Configure custom logging for the Databricks job</span></span>

1. <span data-ttu-id="dda56-293">在 **Databricks CLI** 中輸入下列命令，以將 **azure-databricks-monitoring-0.9.jar** 檔案複製到 Databricks 檔案系統：</span><span class="sxs-lookup"><span data-stu-id="dda56-293">Copy the **azure-databricks-monitoring-0.9.jar** file to the Databricks file system by entering the following command in the **Databricks CLI**:</span></span>
    ```shell
    databricks fs cp --overwrite azure-databricks-monitoring-0.9.jar dbfs:/azure-databricks-job/azure-databricks-monitoring-0.9.jar
    ```

2. <span data-ttu-id="dda56-294">輸入下列命令，以從 `\azure\azure-databricks-monitoring\scripts\metrics.properties` 將自訂的記錄屬性複製到 Databricks 檔案系統：</span><span class="sxs-lookup"><span data-stu-id="dda56-294">Copy the custom logging properties from `\azure\azure-databricks-monitoring\scripts\metrics.properties` to the Databricks file system by entering the following command:</span></span>
    ```shell
    databricks fs cp --overwrite metrics.properties dbfs:/azure-databricks-job/metrics.properties
    ```

3. <span data-ttu-id="dda56-295">您尚未決定 Databricks 叢集的名稱，請先選取此名稱。</span><span class="sxs-lookup"><span data-stu-id="dda56-295">While you haven't yet decided on a name for your Databricks cluster, select one now.</span></span> <span data-ttu-id="dda56-296">後續您將在叢集的 Databricks 檔案系統路徑中輸入此名稱。</span><span class="sxs-lookup"><span data-stu-id="dda56-296">You'll enter the name below in the Databricks file system path for your cluster.</span></span> <span data-ttu-id="dda56-297">輸入下列命令，以從 `\azure\azure-databricks-monitoring\scripts\spark.metrics` 將初始化指令碼複製到 Databricks 檔案系統：</span><span class="sxs-lookup"><span data-stu-id="dda56-297">Copy the initialization script from `\azure\azure-databricks-monitoring\scripts\spark.metrics` to the Databricks file system by entering the following command:</span></span>
    ```shell
    databricks fs cp --overwrite spark-metrics.sh dbfs:/databricks/init/<cluster-name>/spark-metrics.sh
    ```

### <a name="create-a-databricks-cluster"></a><span data-ttu-id="dda56-298">建立 Databricks 叢集</span><span class="sxs-lookup"><span data-stu-id="dda56-298">Create a Databricks cluster</span></span>

1. <span data-ttu-id="dda56-299">在 Databricks 工作區中按一下 [叢集]，然後按一下 [建立叢集]。</span><span class="sxs-lookup"><span data-stu-id="dda56-299">In the Databricks workspace, click "Clusters", then click "create cluster".</span></span> <span data-ttu-id="dda56-300">輸入您在上述**設定 Databricks 作業的自訂記錄**一節的步驟 3 中建立的叢集名稱。</span><span class="sxs-lookup"><span data-stu-id="dda56-300">Enter the cluster name you created in step 3 of the **configure custom logging for the Databricks job** section above.</span></span>

2. <span data-ttu-id="dda56-301">選取 [標準] 叢集模式。</span><span class="sxs-lookup"><span data-stu-id="dda56-301">Select a **standard** cluster mode.</span></span>

3. <span data-ttu-id="dda56-302">將 [Databricks 執行階段版本] 設為 **4.3 (包括 Apache Spark 2.3.1、Scala 2.11)**</span><span class="sxs-lookup"><span data-stu-id="dda56-302">Set **Databricks runtime version** to **4.3 (includes Apache Spark 2.3.1, Scala 2.11)**</span></span>

4. <span data-ttu-id="dda56-303">將 [Python 版本] 設為 **2**。</span><span class="sxs-lookup"><span data-stu-id="dda56-303">Set **Python version** to **2**.</span></span>

5. <span data-ttu-id="dda56-304">將 [驅動程式類型] 設為 [與背景工作角色相同]</span><span class="sxs-lookup"><span data-stu-id="dda56-304">Set **Driver Type** to **Same as worker**</span></span>

6. <span data-ttu-id="dda56-305">將 [背景工作角色類型] 設為 **Standard_DS3_v2**。</span><span class="sxs-lookup"><span data-stu-id="dda56-305">Set **Worker Type** to **Standard_DS3_v2**.</span></span>

7. <span data-ttu-id="dda56-306">將 [背景工作數下限] 設為 **2**。</span><span class="sxs-lookup"><span data-stu-id="dda56-306">Set **Min Workers** to **2**.</span></span>

8. <span data-ttu-id="dda56-307">取消選取 [啟用自動調整]。</span><span class="sxs-lookup"><span data-stu-id="dda56-307">Deselect **Enable autoscaling**.</span></span>

9. <span data-ttu-id="dda56-308">在 [自動終止] 對話方塊下方，按一下 [Init 指令碼]。</span><span class="sxs-lookup"><span data-stu-id="dda56-308">Below the **Auto Termination** dialog box, click on **Init Scripts**.</span></span>

10. <span data-ttu-id="dda56-309">輸入 **dbfs:/databricks/init/<cluster-name>/spark-metrics.sh**，替換在 <cluster-name> 的步驟 1 中所建立的叢集名稱。</span><span class="sxs-lookup"><span data-stu-id="dda56-309">Enter **dbfs:/databricks/init/<cluster-name>/spark-metrics.sh**, substituting the cluster name created in step 1 for <cluster-name>.</span></span>

11. <span data-ttu-id="dda56-310">按一下 [新增]  按鈕。</span><span class="sxs-lookup"><span data-stu-id="dda56-310">Click the **Add** button.</span></span>

12. <span data-ttu-id="dda56-311">按一下 [建立叢集] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="dda56-311">Click the **Create Cluster** button.</span></span>

### <a name="create-a-databricks-job"></a><span data-ttu-id="dda56-312">建立 Databricks 作業</span><span class="sxs-lookup"><span data-stu-id="dda56-312">Create a Databricks job</span></span>

1. <span data-ttu-id="dda56-313">在 Databricks 工作區中，依序按一下 [作業]、[建立作業]。</span><span class="sxs-lookup"><span data-stu-id="dda56-313">In the Databricks workspace, click "Jobs", "create job".</span></span>

2. <span data-ttu-id="dda56-314">輸入作業名稱。</span><span class="sxs-lookup"><span data-stu-id="dda56-314">Enter a job name.</span></span>

3. <span data-ttu-id="dda56-315">按一下 [設定 jar]，這會開啟 [上傳要執行的 JAR] 對話方塊。</span><span class="sxs-lookup"><span data-stu-id="dda56-315">Click "set jar", this opens the "Upload JAR to Run" dialog box.</span></span>

4. <span data-ttu-id="dda56-316">將在**建置 Databricks 作業的 .jar** 一節中建立的 **azure-databricks-job-1.0-SNAPSHOT.jar** 檔案拖曳到 [在這裡放置 JAR 以上傳] 方塊。</span><span class="sxs-lookup"><span data-stu-id="dda56-316">Drag the **azure-databricks-job-1.0-SNAPSHOT.jar** file you created in the **build the .jar for the Databricks job** section to the **Drop JAR here to upload** box.</span></span>

5. <span data-ttu-id="dda56-317">在 [主要類別] 欄位中輸入 **com.microsoft.pnp.TaxiCabReader**。</span><span class="sxs-lookup"><span data-stu-id="dda56-317">Enter **com.microsoft.pnp.TaxiCabReader** in the **Main Class** field.</span></span>

6. <span data-ttu-id="dda56-318">在引數欄位中，輸入下列項目：</span><span class="sxs-lookup"><span data-stu-id="dda56-318">In the arguments field, enter the following:</span></span>
    ```shell
    -n jar:file:/dbfs/azure-databricks-jobs/ZillowNeighborhoods-NY.zip!/ZillowNeighborhoods-NY.shp --taxi-ride-consumer-group taxi-ride-eh-cg --taxi-fare-consumer-group taxi-fare-eh-cg --window-interval "1 minute" --cassandra-host <Cosmos DB Cassandra host name from above>
    ```

7. <span data-ttu-id="dda56-319">依照下列步驟安裝相依程式庫：</span><span class="sxs-lookup"><span data-stu-id="dda56-319">Install the dependent libraries by following these steps:</span></span>

    1. <span data-ttu-id="dda56-320">在 Databricks 使用者介面中，按一下 [首頁] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="dda56-320">In the Databricks user interface, click on the **home** button.</span></span>

    2. <span data-ttu-id="dda56-321">在 [使用者] 下拉式清單中按一下您的使用者帳戶名稱，以開啟您的帳戶工作區設定。</span><span class="sxs-lookup"><span data-stu-id="dda56-321">In the **Users** drop-down, click on your user account name to open your account workspace settings.</span></span>

    3. <span data-ttu-id="dda56-322">按一下您帳戶名稱旁邊的下拉式箭號，再按一下 [建立]，然後按一下 [程式庫] 以開啟 [新增文件庫] 對話方塊。</span><span class="sxs-lookup"><span data-stu-id="dda56-322">Click on the drop-down arrow beside your account name, click on **create**, and click on **Library** to open the **New Library** dialog.</span></span>

    4. <span data-ttu-id="dda56-323">在 [來源] 下拉式控制項中，選取 [Maven 座標]。</span><span class="sxs-lookup"><span data-stu-id="dda56-323">In the **Source** drop-down control, select **Maven Coordinate**.</span></span>

    5. <span data-ttu-id="dda56-324">在 [安裝 Maven 成品] 標題下方，於 [座標] 文字方塊中輸入 `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.5`。</span><span class="sxs-lookup"><span data-stu-id="dda56-324">Under the **Install Maven Artifacts** heading, enter `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.5` in the **Coordinate** text box.</span></span>

    6. <span data-ttu-id="dda56-325">按一下 [建立程式庫] 以開啟 [成品] 視窗。</span><span class="sxs-lookup"><span data-stu-id="dda56-325">Click on **Create Library** to open the **Artifacts** window.</span></span>

    7. <span data-ttu-id="dda56-326">在 [執行叢集的狀態] 下方，勾選 [自動連結至所有叢集] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="dda56-326">Under **Status on running clusters** check the **Attach automatically to all clusters** checkbox.</span></span>

    8. <span data-ttu-id="dda56-327">針對 `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0` Maven 座標重複執行步驟 1 - 7。</span><span class="sxs-lookup"><span data-stu-id="dda56-327">Repeat steps 1 - 7 for the `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0` Maven coordinate.</span></span>

    9. <span data-ttu-id="dda56-328">針對 `org.geotools:gt-shapefile:19.2` Maven 座標重複執行步驟 1 - 6。</span><span class="sxs-lookup"><span data-stu-id="dda56-328">Repeat steps 1 - 6 for the `org.geotools:gt-shapefile:19.2` Maven coordinate.</span></span>

    10. <span data-ttu-id="dda56-329">按一下 [進階選項]。</span><span class="sxs-lookup"><span data-stu-id="dda56-329">Click on **Advanced Options**.</span></span>

    11. <span data-ttu-id="dda56-330">在 [存放庫] 文字方塊中輸入 `http://download.osgeo.org/webdav/geotools/`。</span><span class="sxs-lookup"><span data-stu-id="dda56-330">Enter `http://download.osgeo.org/webdav/geotools/` in the **Repository** text box.</span></span>

    12. <span data-ttu-id="dda56-331">按一下 [建立程式庫] 以開啟 [成品] 視窗。</span><span class="sxs-lookup"><span data-stu-id="dda56-331">Click **Create Library** to open the **Artifacts** window.</span></span> 

    13. <span data-ttu-id="dda56-332">在 [執行叢集的狀態] 下方，勾選 [自動連結至所有叢集] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="dda56-332">Under **Status on running clusters** check the **Attach automatically to all clusters** checkbox.</span></span>

8. <span data-ttu-id="dda56-333">將在步驟 7 中新增的相依程式庫新增至在步驟 6 的結尾建立的作業：</span><span class="sxs-lookup"><span data-stu-id="dda56-333">Add the dependent libraries added in step 7 to the job created at the end of step 6:</span></span>

    1. <span data-ttu-id="dda56-334">在 Azure Databricks 工作區中，按一下 [作業]。</span><span class="sxs-lookup"><span data-stu-id="dda56-334">In the Azure Databricks workspace, click on **Jobs**.</span></span>

    2. <span data-ttu-id="dda56-335">按一下在**建立 Databricks 作業**一節的步驟 2 中建立的作業名稱。</span><span class="sxs-lookup"><span data-stu-id="dda56-335">Click on the job name created in step 2 of the **create a Databricks job** section.</span></span>

    3. <span data-ttu-id="dda56-336">在 [相依程式庫] 區段旁邊按一下 [新增]，以開啟 [新增相依程式庫] 對話方塊。</span><span class="sxs-lookup"><span data-stu-id="dda56-336">Beside the **Dependent Libraries** section, click on **Add** to open the **Add Dependent Library** dialog.</span></span>

    4. <span data-ttu-id="dda56-337">在 [程式庫來源] 下方，選取 [工作區]。</span><span class="sxs-lookup"><span data-stu-id="dda56-337">Under **Library From** select **Workspace**.</span></span>

    5. <span data-ttu-id="dda56-338">依序按一下 [使用者]、您的使用者名稱，然後按一下 `azure-eventhubs-spark_2.11:2.3.5`。</span><span class="sxs-lookup"><span data-stu-id="dda56-338">Click on **users**, then your username, then click on `azure-eventhubs-spark_2.11:2.3.5`.</span></span>

    6. <span data-ttu-id="dda56-339">按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="dda56-339">Click **OK**.</span></span>

    7. <span data-ttu-id="dda56-340">針對 `spark-cassandra-connector_2.11:2.3.1` 和 `gt-shapefile:19.2` 重複執行步驟 1 - 6。</span><span class="sxs-lookup"><span data-stu-id="dda56-340">Repeat steps 1 - 6 for `spark-cassandra-connector_2.11:2.3.1` and `gt-shapefile:19.2`.</span></span>

9. <span data-ttu-id="dda56-341">在 [叢集：] 旁邊，按一下 [編輯]。</span><span class="sxs-lookup"><span data-stu-id="dda56-341">Beside **Cluster:**, click on **Edit**.</span></span> <span data-ttu-id="dda56-342">這會開啟 [設定叢集] 對話方塊。</span><span class="sxs-lookup"><span data-stu-id="dda56-342">This opens the **Configure Cluster** dialog.</span></span> <span data-ttu-id="dda56-343">在 [叢集類型] 下拉式清單中，選取 [現有的叢集]。</span><span class="sxs-lookup"><span data-stu-id="dda56-343">In the **Cluster Type** drop-down, select **Existing Cluster**.</span></span> <span data-ttu-id="dda56-344">在 [選取叢集] 下拉式清單中，選取在**建立 Databricks 叢集**一節中建立的叢集。</span><span class="sxs-lookup"><span data-stu-id="dda56-344">In the **Select Cluster** drop-down, select the cluster created the **create a Databricks cluster** section.</span></span> <span data-ttu-id="dda56-345">按一下 [確認]。</span><span class="sxs-lookup"><span data-stu-id="dda56-345">Click **confirm**.</span></span>

10. <span data-ttu-id="dda56-346">按一下 [立即執行]。</span><span class="sxs-lookup"><span data-stu-id="dda56-346">Click **run now**.</span></span>

### <a name="run-the-data-generator"></a><span data-ttu-id="dda56-347">執行資料產生器</span><span class="sxs-lookup"><span data-stu-id="dda56-347">Run the data generator</span></span>

1. <span data-ttu-id="dda56-348">瀏覽至 GitHub 存放庫中名為 `onprem` 的目錄。</span><span class="sxs-lookup"><span data-stu-id="dda56-348">Navigate to the directory named `onprem` in the GitHub repository.</span></span>

2. <span data-ttu-id="dda56-349">更新 **main.env** 檔案中的值，如下所示：</span><span class="sxs-lookup"><span data-stu-id="dda56-349">Update the values in the file **main.env** as follows:</span></span>

    ```shell
    RIDE_EVENT_HUB=[Connection string for the taxi-ride event hub]
    FARE_EVENT_HUB=[Connection string for the taxi-fare event hub]
    RIDE_DATA_FILE_PATH=/DataFile/FOIL2013
    MINUTES_TO_LEAD=0
    PUSH_RIDE_DATA_FIRST=false
    ```
    <span data-ttu-id="dda56-350">計程車車程事件中樞的連接字串是*部署 Azure 資源*一節步驟 4 的 **eventHubs** 輸出區段所包含的 **taxi-ride-eh** 值。</span><span class="sxs-lookup"><span data-stu-id="dda56-350">The connection string for the taxi-ride event hub is the **taxi-ride-eh** value from the **eventHubs** output section in step 4 of the *deploy the Azure resources* section.</span></span> <span data-ttu-id="dda56-351">計程車費用事件中樞的連接字串是*部署 Azure 資源*一節步驟 4 的 **eventHubs** 輸出區段所包含的 **taxi-fare-eh** 值。</span><span class="sxs-lookup"><span data-stu-id="dda56-351">The connection string for the taxi-fare event hub the **taxi-fare-eh** value from the **eventHubs** output section in step 4 of the *deploy the Azure resources* section.</span></span>

3. <span data-ttu-id="dda56-352">執行下列命令以建置 Docker 映像。</span><span class="sxs-lookup"><span data-stu-id="dda56-352">Run the following command to build the Docker image.</span></span>

    ```bash
    docker build --no-cache -t dataloader .
    ```

4. <span data-ttu-id="dda56-353">瀏覽回父代目錄。</span><span class="sxs-lookup"><span data-stu-id="dda56-353">Navigate back to the parent directory.</span></span>

    ```bash
    cd ..
    ```

5. <span data-ttu-id="dda56-354">執行下列命令以執行 Docker 映像。</span><span class="sxs-lookup"><span data-stu-id="dda56-354">Run the following command to run the Docker image.</span></span>

    ```bash
    docker run -v `pwd`/DataFile:/DataFile --env-file=onprem/main.env dataloader:latest
    ```

<span data-ttu-id="dda56-355">輸出應該看起來如下所示：</span><span class="sxs-lookup"><span data-stu-id="dda56-355">The output should look like the following:</span></span>

```
Created 10000 records for TaxiFare
Created 10000 records for TaxiRide
Created 20000 records for TaxiFare
Created 20000 records for TaxiRide
Created 30000 records for TaxiFare
...
```

<span data-ttu-id="dda56-356">若要確認 Databricks 作業正確執行，請開啟 Azure 入口網站並瀏覽至 Cosmos DB 資料庫。</span><span class="sxs-lookup"><span data-stu-id="dda56-356">To verify the Databricks job is running correctly, open the Azure portal and navigate to the Cosmos DB database.</span></span> <span data-ttu-id="dda56-357">開啟 [資料總管] 刀鋒視窗，並檢查**計程車記錄**資料表中的資料。</span><span class="sxs-lookup"><span data-stu-id="dda56-357">Open the **Data Explorer** blade and examine the data in the **taxi records** table.</span></span> 

<span data-ttu-id="dda56-358">[1] <span id="note1">Donovan, Brian; Work, Dan (2016)：紐約市計程車車程資料 (2010-2013)。</span><span class="sxs-lookup"><span data-stu-id="dda56-358">[1] <span id="note1">Donovan, Brian; Work, Dan (2016): New York City Taxi Trip Data (2010-2013).</span></span> <span data-ttu-id="dda56-359">伊利諾大學香檳分校。</span><span class="sxs-lookup"><span data-stu-id="dda56-359">University of Illinois at Urbana-Champaign.</span></span> <span data-ttu-id="dda56-360">https://doi.org/10.13012/J8PN93H8</span><span class="sxs-lookup"><span data-stu-id="dda56-360">https://doi.org/10.13012/J8PN93H8</span></span>

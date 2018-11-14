---
title: 串流處理搭配 Azure 串流分析
description: 在 Azure 中建立端對端串流處理管線
author: MikeWasson
ms.date: 11/06/2018
ms.openlocfilehash: e16547ccdcb81007e154e341f09be555ac82d1a1
ms.sourcegitcommit: 02ecd259a6e780d529c853bc1db320f4fcf919da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263757"
---
# <a name="stream-processing-with-azure-stream-analytics"></a><span data-ttu-id="23051-103">串流處理搭配 Azure 串流分析</span><span class="sxs-lookup"><span data-stu-id="23051-103">Stream processing with Azure Stream Analytics</span></span>

<span data-ttu-id="23051-104">此參考架構會示範端對端串流處理管線。</span><span class="sxs-lookup"><span data-stu-id="23051-104">This reference architecture shows an end-to-end stream processing pipeline.</span></span> <span data-ttu-id="23051-105">管線會從兩個來源擷取資料、在兩個串流中將記錄相互關聯，並計算時間範圍內的移動平均。</span><span class="sxs-lookup"><span data-stu-id="23051-105">The pipeline ingests data from two sources, correlates records in the two streams, and calculates a rolling average across a time window.</span></span> <span data-ttu-id="23051-106">結果會儲存以供進一步分析。</span><span class="sxs-lookup"><span data-stu-id="23051-106">The results are stored for further analysis.</span></span> 

<span data-ttu-id="23051-107">此架構的參考實作可在 [GitHub][github] 上取得。</span><span class="sxs-lookup"><span data-stu-id="23051-107">A reference implementation for this architecture is available on [GitHub][github].</span></span> 

![](./images/stream-processing-asa/stream-processing-asa.png)

<span data-ttu-id="23051-108">**案例**：計程車公司會收集有關每趟計程車車程的資料。</span><span class="sxs-lookup"><span data-stu-id="23051-108">**Scenario**: A taxi company collects data about each taxi trip.</span></span> <span data-ttu-id="23051-109">在此案例中，我們假設有兩個不同的裝置會傳送資料。</span><span class="sxs-lookup"><span data-stu-id="23051-109">For this scenario, we assume there are two separate devices sending data.</span></span> <span data-ttu-id="23051-110">計程車的其中一個計量會傳送關於每趟車程的資訊，如車程時間、距離和上下車地點等。</span><span class="sxs-lookup"><span data-stu-id="23051-110">The taxi has a meter that sends information about each ride &mdash; the duration, distance, and pickup and dropoff locations.</span></span> <span data-ttu-id="23051-111">另一個裝置會接收客戶付款，並傳送費用相關資料。</span><span class="sxs-lookup"><span data-stu-id="23051-111">A separate device accepts payments from customers and sends data about fares.</span></span> <span data-ttu-id="23051-112">計程車公司想要即時計算出平均每英里的小費，以便找出趨勢。</span><span class="sxs-lookup"><span data-stu-id="23051-112">The taxi company wants to calculate the average tip per mile driven, in real time, in order to spot trends.</span></span>

## <a name="architecture"></a><span data-ttu-id="23051-113">架構</span><span class="sxs-lookup"><span data-stu-id="23051-113">Architecture</span></span>

<span data-ttu-id="23051-114">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="23051-114">The architecture consists of the following components.</span></span>

<span data-ttu-id="23051-115">**資料來源**。</span><span class="sxs-lookup"><span data-stu-id="23051-115">**Data sources**.</span></span> <span data-ttu-id="23051-116">在此架構中，有兩個資料來源會即時產生資料流。</span><span class="sxs-lookup"><span data-stu-id="23051-116">In this architecture, there are two data sources that generate data streams in real time.</span></span> <span data-ttu-id="23051-117">第一個資料流包含車程資訊，而第二個包含費用資訊。</span><span class="sxs-lookup"><span data-stu-id="23051-117">The first stream contains ride information, and the second contains fare information.</span></span> <span data-ttu-id="23051-118">參考架構包含模擬的資料產生器，可讀取一組靜態檔案，並將資料推送到事件中樞。</span><span class="sxs-lookup"><span data-stu-id="23051-118">The reference architecture includes a simulated data generator that reads from a set of static files and pushes the data to Event Hubs.</span></span> <span data-ttu-id="23051-119">在實際的應用程式中，資料來源會是安裝在計程車上的裝置。</span><span class="sxs-lookup"><span data-stu-id="23051-119">In a real application, the data sources would be devices installed in the taxi cabs.</span></span>

<span data-ttu-id="23051-120">**Azure 事件中樞**.</span><span class="sxs-lookup"><span data-stu-id="23051-120">**Azure Event Hubs**.</span></span> <span data-ttu-id="23051-121">[事件中樞](/azure/event-hubs/)是事件擷取服務。</span><span class="sxs-lookup"><span data-stu-id="23051-121">[Event Hubs](/azure/event-hubs/) is an event ingestion service.</span></span> <span data-ttu-id="23051-122">此架構使用兩個事件中樞執行個體，分別用於兩個資料來源。</span><span class="sxs-lookup"><span data-stu-id="23051-122">This architecture uses two event hub instances, one for each data source.</span></span> <span data-ttu-id="23051-123">每個資料來源會將資料流傳送至相關聯的事件中樞。</span><span class="sxs-lookup"><span data-stu-id="23051-123">Each data source sends a stream of data to the associated event hub.</span></span>

<span data-ttu-id="23051-124">**Azure 串流分析**。</span><span class="sxs-lookup"><span data-stu-id="23051-124">**Azure Stream Analytics**.</span></span> <span data-ttu-id="23051-125">[串流分析](/azure/stream-analytics/)是事件處理引擎。</span><span class="sxs-lookup"><span data-stu-id="23051-125">[Stream Analytics](/azure/stream-analytics/) is an event-processing engine.</span></span> <span data-ttu-id="23051-126">串流分析作業會從兩個事件中樞讀取資料流，並執行串流處理。</span><span class="sxs-lookup"><span data-stu-id="23051-126">A Stream Analytics job reads the data streams from the two event hubs and performs stream processing.</span></span>

<span data-ttu-id="23051-127">**Cosmos DB**。</span><span class="sxs-lookup"><span data-stu-id="23051-127">**Cosmos DB**.</span></span> <span data-ttu-id="23051-128">串流分析作業的輸出是一系列記錄，其會以 JSON 文件寫入 Cosmos DB 文件資料庫。</span><span class="sxs-lookup"><span data-stu-id="23051-128">The output from the Stream Analytics job is a series of records, which are written as JSON documents to a Cosmos DB document database.</span></span>

<span data-ttu-id="23051-129">**Microsoft Power BI**。</span><span class="sxs-lookup"><span data-stu-id="23051-129">**Microsoft Power BI**.</span></span> <span data-ttu-id="23051-130">Power BI 是一套用來分析資料以產生商業見解的商務分析工具。</span><span class="sxs-lookup"><span data-stu-id="23051-130">Power BI is a suite of business analytics tools to analyze data for business insights.</span></span> <span data-ttu-id="23051-131">在此架構中，它會從 Cosmos DB 載入資料。</span><span class="sxs-lookup"><span data-stu-id="23051-131">In this architecture, it loads the data from Cosmos DB.</span></span> <span data-ttu-id="23051-132">這可讓使用者分析收集到的一組完整歷程記錄資料。</span><span class="sxs-lookup"><span data-stu-id="23051-132">This allows users to analyze the complete set of historical data that's been collected.</span></span> <span data-ttu-id="23051-133">您也可以直接從串流分析將結果串流到 Power BI，以取得資料的即時檢視。</span><span class="sxs-lookup"><span data-stu-id="23051-133">You could also stream the results directly from Stream Analytics to Power BI for a real-time view of the data.</span></span> <span data-ttu-id="23051-134">如需詳細資訊，請參閱 [Power BI 中的即時串流](/power-bi/service-real-time-streaming)。</span><span class="sxs-lookup"><span data-stu-id="23051-134">For more information, see [Real-time streaming in Power BI](/power-bi/service-real-time-streaming).</span></span>

<span data-ttu-id="23051-135">**Azure 監視器**。</span><span class="sxs-lookup"><span data-stu-id="23051-135">**Azure Monitor**.</span></span> <span data-ttu-id="23051-136">[Azure 監視器](/azure/monitoring-and-diagnostics/)會收集解決方案中部署的 Azure 服務相關效能計量。</span><span class="sxs-lookup"><span data-stu-id="23051-136">[Azure Monitor](/azure/monitoring-and-diagnostics/) collects performance metrics about the Azure services deployed in the solution.</span></span> <span data-ttu-id="23051-137">透過儀表板中的資料視覺化，您可以取得解決方案健康情況的深入解析。</span><span class="sxs-lookup"><span data-stu-id="23051-137">By visualizing these in a dashboard, you can get insights into the health of the solution.</span></span> 

## <a name="data-ingestion"></a><span data-ttu-id="23051-138">資料擷取</span><span class="sxs-lookup"><span data-stu-id="23051-138">Data ingestion</span></span>

<span data-ttu-id="23051-139">若要模擬資料來源，此參考架構會使用[紐約市計程車資料](https://uofi.app.box.com/v/NYCtaxidata/folder/2332218797)資料集<sup>[[1]](#note1)</sup>。</span><span class="sxs-lookup"><span data-stu-id="23051-139">To simulate a data source, this reference architecture uses the [New York City Taxi Data](https://uofi.app.box.com/v/NYCtaxidata/folder/2332218797) dataset<sup>[[1]](#note1)</sup>.</span></span> <span data-ttu-id="23051-140">此資料集包含紐約市在 4 年期間的計程車路程資料 (2010 &ndash; 2013)。</span><span class="sxs-lookup"><span data-stu-id="23051-140">This dataset contains data about taxi trips in New York City over a 4-year period (2010 &ndash; 2013).</span></span> <span data-ttu-id="23051-141">它包含兩種類型的記錄：車程資料和費用資料。</span><span class="sxs-lookup"><span data-stu-id="23051-141">It contains two types of record: Ride data and fare data.</span></span> <span data-ttu-id="23051-142">車程資料包括路程持續時間、路程距離和上下車地點。</span><span class="sxs-lookup"><span data-stu-id="23051-142">Ride data includes trip duration, trip distance, and pickup and dropoff location.</span></span> <span data-ttu-id="23051-143">費用資料包括費用、稅金和小費金額。</span><span class="sxs-lookup"><span data-stu-id="23051-143">Fare data includes fare, tax, and tip amounts.</span></span> <span data-ttu-id="23051-144">在這兩種記錄類型中共同欄位包含計程車牌照號碼、計程車執照和廠商識別碼。</span><span class="sxs-lookup"><span data-stu-id="23051-144">Common fields in both record types include medallion number, hack license, and vendor ID.</span></span> <span data-ttu-id="23051-145">這三個欄位可唯一識別一輛計程車加上司機。</span><span class="sxs-lookup"><span data-stu-id="23051-145">Together these three fields uniquely identify a taxi plus a driver.</span></span> <span data-ttu-id="23051-146">資料會以 CSV 格式儲存。</span><span class="sxs-lookup"><span data-stu-id="23051-146">The data is stored in CSV format.</span></span> 

<span data-ttu-id="23051-147">[1] <span id="note1">Donovan, Brian; Work, Dan (2016)：紐約市計程車路程資料 (2010-2013)。</span><span class="sxs-lookup"><span data-stu-id="23051-147">[1] <span id="note1">Donovan, Brian; Work, Dan (2016): New York City Taxi Trip Data (2010-2013).</span></span> <span data-ttu-id="23051-148">伊利諾大學香檳分校。</span><span class="sxs-lookup"><span data-stu-id="23051-148">University of Illinois at Urbana-Champaign.</span></span> <span data-ttu-id="23051-149">https://doi.org/10.13012/J8PN93H8</span><span class="sxs-lookup"><span data-stu-id="23051-149">https://doi.org/10.13012/J8PN93H8</span></span>

<span data-ttu-id="23051-150">資料產生器為 .NET Core 應用程式，可讀取記錄並將其傳送到 Azure 事件中樞。</span><span class="sxs-lookup"><span data-stu-id="23051-150">The data generator is a .NET Core application that reads the records and sends them to Azure Event Hubs.</span></span> <span data-ttu-id="23051-151">產生器會以 JSON 格式傳送車程資料，以 CSV 格式傳送費用資料。</span><span class="sxs-lookup"><span data-stu-id="23051-151">The generator sends ride data in JSON format and fare data in CSV format.</span></span> 

<span data-ttu-id="23051-152">事件中樞使用[分割區](/azure/event-hubs/event-hubs-features#partitions)來分割資料。</span><span class="sxs-lookup"><span data-stu-id="23051-152">Event Hubs uses [partitions](/azure/event-hubs/event-hubs-features#partitions) to segment the data.</span></span> <span data-ttu-id="23051-153">資料分割可讓取用者平行讀取每個分割。</span><span class="sxs-lookup"><span data-stu-id="23051-153">Partitions allow a consumer to read each partition in parallel.</span></span> <span data-ttu-id="23051-154">將資料傳送至事件中樞時，您可以明確指定分割區索引鍵。</span><span class="sxs-lookup"><span data-stu-id="23051-154">When you send data to Event Hubs, you can specify the partition key explicitly.</span></span> <span data-ttu-id="23051-155">否則，記錄會以循環配置資源的方式指派給分割區。</span><span class="sxs-lookup"><span data-stu-id="23051-155">Otherwise, records are assigned to partitions in round-robin fashion.</span></span> 

<span data-ttu-id="23051-156">在此特定案例中，車程資料和費用資料最後應具有指定計程車的同一分割區識別碼。</span><span class="sxs-lookup"><span data-stu-id="23051-156">In this particular scenario, ride data and fare data should end up with the same partition ID for a given taxi cab.</span></span> <span data-ttu-id="23051-157">這可讓串流分析在將兩個資料流相互關聯時套用平行處理原則。</span><span class="sxs-lookup"><span data-stu-id="23051-157">This enables Stream Analytics to apply a degree of parallelism when it correlates the two streams.</span></span> <span data-ttu-id="23051-158">車程資料分割區 *n* 中的記錄會比對到費用資料分割區 *n* 中的記錄。</span><span class="sxs-lookup"><span data-stu-id="23051-158">A record in partition *n* of the ride data will match a record in partition *n* of the fare data.</span></span>

![](./images/stream-processing-asa/stream-processing-eh.png)

<span data-ttu-id="23051-159">在資料產生器中，這兩種記錄類型的共同資料模型具有 `PartitionKey` 屬性，其為 `Medallion`、`HackLicense` 和 `VendorId` 的串連。</span><span class="sxs-lookup"><span data-stu-id="23051-159">In the data generator, the common data model for both record types has a `PartitionKey` property which is the concatenation of `Medallion`, `HackLicense`, and `VendorId`.</span></span>

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

<span data-ttu-id="23051-160">在傳送至事件中樞時，這個屬性用來提供明確的分割區索引鍵：</span><span class="sxs-lookup"><span data-stu-id="23051-160">This property is used to provide an explicit partition key when sending to Event Hubs:</span></span>

```csharp
using (var client = pool.GetObject())
{
    return client.Value.SendAsync(new EventData(Encoding.UTF8.GetBytes(
        t.GetData(dataFormat))), t.PartitionKey);
}
```

## <a name="stream-processing"></a><span data-ttu-id="23051-161">串流處理</span><span class="sxs-lookup"><span data-stu-id="23051-161">Stream processing</span></span>

<span data-ttu-id="23051-162">串流處理作業使用 SQL 查詢搭配數個不同步驟來定義。</span><span class="sxs-lookup"><span data-stu-id="23051-162">The stream processing job is defined using a SQL query with several distinct steps.</span></span> <span data-ttu-id="23051-163">前兩個步驟僅從兩個輸入資料流中選取記錄。</span><span class="sxs-lookup"><span data-stu-id="23051-163">The first two steps simply select records from the two input streams.</span></span>

```sql
WITH
Step1 AS (
    SELECT PartitionId,
           TRY_CAST(Medallion AS nvarchar(max)) AS Medallion,
           TRY_CAST(HackLicense AS nvarchar(max)) AS HackLicense,
           VendorId,
           TRY_CAST(PickupTime AS datetime) AS PickupTime,
           TripDistanceInMiles
    FROM [TaxiRide] PARTITION BY PartitionId
),
Step2 AS (
    SELECT PartitionId,
           medallion AS Medallion,
           hack_license AS HackLicense,
           vendor_id AS VendorId,
           TRY_CAST(pickup_datetime AS datetime) AS PickupTime,
           tip_amount AS TipAmount
    FROM [TaxiFare] PARTITION BY PartitionId
),
```

<span data-ttu-id="23051-164">下一個步驟會將兩個輸入資料流聯結，從每個資料流中選取相符的記錄。</span><span class="sxs-lookup"><span data-stu-id="23051-164">The next step joins the two input streams to select matching records from each stream.</span></span>

```sql
Step3 AS (
  SELECT
         tr.Medallion,
         tr.HackLicense,
         tr.VendorId,
         tr.PickupTime,
         tr.TripDistanceInMiles,
         tf.TipAmount
    FROM [Step1] tr
    PARTITION BY PartitionId
    JOIN [Step2] tf PARTITION BY PartitionId
      ON tr.Medallion = tf.Medallion
     AND tr.HackLicense = tf.HackLicense
     AND tr.VendorId = tf.VendorId
     AND tr.PickupTime = tf.PickupTime
     AND tr.PartitionId = tf.PartitionId
     AND DATEDIFF(minute, tr, tf) BETWEEN 0 AND 15
)
```

<span data-ttu-id="23051-165">此查詢會聯結一組欄位上的記錄，可唯一識別相符記錄 (Medallion、HackLicense、VendorId 和 PickupTime)。</span><span class="sxs-lookup"><span data-stu-id="23051-165">This query joins records on a set of fields that uniquely identify matching records (Medallion, HackLicense, VendorId, and PickupTime).</span></span> <span data-ttu-id="23051-166">`JOIN` 陳述式也包含分割區識別碼。</span><span class="sxs-lookup"><span data-stu-id="23051-166">The `JOIN` statement also includes the partition ID.</span></span> <span data-ttu-id="23051-167">如上所述，此案例中這是利用了相符的記錄具有相同的分割區識別碼。</span><span class="sxs-lookup"><span data-stu-id="23051-167">As mentioned, this takes advantage of the fact that matching records always have the same partition ID in this scenario.</span></span>

<span data-ttu-id="23051-168">在串流分析中，聯結是*時態性*，表示記錄已加入特定的時間範圍內。</span><span class="sxs-lookup"><span data-stu-id="23051-168">In Stream Analytics, joins are *temporal*, meaning records are joined within a particular window of time.</span></span> <span data-ttu-id="23051-169">否則，作業可能需要無限期等待相符項目。</span><span class="sxs-lookup"><span data-stu-id="23051-169">Otherwise, the job might need to wait indefinitely for a match.</span></span> <span data-ttu-id="23051-170">[DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/join-azure-stream-analytics) 函式可指定針對符合項目，兩筆相符記錄的時間可以間隔多久。</span><span class="sxs-lookup"><span data-stu-id="23051-170">The [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/join-azure-stream-analytics) function specifies how far two matching records can be separated in time for a match.</span></span> 

<span data-ttu-id="23051-171">作業的最後一個步驟會計算每英里的平均小費，依 5 分鐘跳動視窗分組。</span><span class="sxs-lookup"><span data-stu-id="23051-171">The last step in the job computes the average tip per mile, grouped by a hopping window of 5 minutes.</span></span>

```sql
SELECT System.Timestamp AS WindowTime,
       SUM(tr.TipAmount) / SUM(tr.TripDistanceInMiles) AS AverageTipPerMile
  INTO [TaxiDrain]
  FROM [Step3] tr
  GROUP BY HoppingWindow(Duration(minute, 5), Hop(minute, 1))
```

<span data-ttu-id="23051-172">串流分析提供多種[時間範圍函式](/azure/stream-analytics/stream-analytics-window-functions)。</span><span class="sxs-lookup"><span data-stu-id="23051-172">Stream Analytics provides several [windowing functions](/azure/stream-analytics/stream-analytics-window-functions).</span></span> <span data-ttu-id="23051-173">跳動視窗會以一段固定時間向前移動，在此案例中為每個躍點 1 分鐘。</span><span class="sxs-lookup"><span data-stu-id="23051-173">A hopping window moves forward in time by a fixed period, in this case 1 minute per hop.</span></span> <span data-ttu-id="23051-174">目的是要計算過去 5 分鐘的移動平均。</span><span class="sxs-lookup"><span data-stu-id="23051-174">The result is to calculate a moving average over the past 5 minutes.</span></span>

<span data-ttu-id="23051-175">在此處所示的架構中，只有串流分析作業的結果會儲存到 Cosmos DB。</span><span class="sxs-lookup"><span data-stu-id="23051-175">In the architecture shown here, only the results of the Stream Analytics job are saved to Cosmos DB.</span></span> <span data-ttu-id="23051-176">在巨量資料案例中，也請考慮使用[事件中樞擷取](/azure/event-hubs/event-hubs-capture-overview)，將原始事件資料儲存至 Azure Blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="23051-176">For a big data scenario, consider also using [Event Hubs Capture](/azure/event-hubs/event-hubs-capture-overview) to save the raw event data into Azure Blob storage.</span></span> <span data-ttu-id="23051-177">保留未經處理資料可讓您在稍後對歷程記錄資料執行批次查詢，以便從資料中找出新的深入解析。</span><span class="sxs-lookup"><span data-stu-id="23051-177">Keeping the raw data will allow you to run batch queries over your historical data at later time, in order to derive new insights from the data.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="23051-178">延展性考量</span><span class="sxs-lookup"><span data-stu-id="23051-178">Scalability considerations</span></span>

### <a name="event-hubs"></a><span data-ttu-id="23051-179">事件中樞</span><span class="sxs-lookup"><span data-stu-id="23051-179">Event Hubs</span></span>

<span data-ttu-id="23051-180">事件中樞的輸送量容量會以[輸送量單位](/azure/event-hubs/event-hubs-features#throughput-units)來測量。</span><span class="sxs-lookup"><span data-stu-id="23051-180">The throughput capacity of Event Hubs is measured in [throughput units](/azure/event-hubs/event-hubs-features#throughput-units).</span></span> <span data-ttu-id="23051-181">您可以啟用[自動擴充](/azure/event-hubs/event-hubs-auto-inflate)以自動調整事件中樞，這會根據流量 (上限為設定的最大值) 自動調整輸送量單位。</span><span class="sxs-lookup"><span data-stu-id="23051-181">You can autoscale an event hub by enabling [auto-inflate](/azure/event-hubs/event-hubs-auto-inflate), which automatically scales the throughput units based on traffic, up to a configured maximum.</span></span> 

### <a name="stream-analytics"></a><span data-ttu-id="23051-182">串流分析</span><span class="sxs-lookup"><span data-stu-id="23051-182">Stream Analytics</span></span>

<span data-ttu-id="23051-183">對於串流分析，配置給作業的計算資源會以串流單位測量。</span><span class="sxs-lookup"><span data-stu-id="23051-183">For Stream Analytics, the computing resources allocated to a job are measured in Streaming Units.</span></span> <span data-ttu-id="23051-184">如果作業可以平行處理，則串流分析作業可進行最佳調整。</span><span class="sxs-lookup"><span data-stu-id="23051-184">Stream Analytics jobs scale best if the job can be parallelized.</span></span> <span data-ttu-id="23051-185">如此一來，串流分析可以將作業分散至多個計算節點。</span><span class="sxs-lookup"><span data-stu-id="23051-185">That way, Stream Analytics can distribute the job across multiple compute nodes.</span></span>

<span data-ttu-id="23051-186">對於事件中樞輸入，使用 `PARTITION BY` 關鍵字來分割串流分析作業。</span><span class="sxs-lookup"><span data-stu-id="23051-186">For Event Hubs input, use the `PARTITION BY` keyword to partition the Stream Analytics job.</span></span> <span data-ttu-id="23051-187">資料會根據事件中樞分割區分割為子集。</span><span class="sxs-lookup"><span data-stu-id="23051-187">The data will be divided into subsets based on the Event Hubs partitions.</span></span> 

<span data-ttu-id="23051-188">時間範圍函式和時態性聯結需要額外的 SU。</span><span class="sxs-lookup"><span data-stu-id="23051-188">Windowing functions and temporal joins require additional SU.</span></span> <span data-ttu-id="23051-189">可能的話，請使用 `PARTITION BY` 以便每個分割區可個別處理。</span><span class="sxs-lookup"><span data-stu-id="23051-189">When possible, use `PARTITION BY` so that each partition is processed separately.</span></span> <span data-ttu-id="23051-190">如需詳細資訊，請參閱[了解和調整串流單位](/azure/stream-analytics/stream-analytics-streaming-unit-consumption#windowed-aggregates)。</span><span class="sxs-lookup"><span data-stu-id="23051-190">For more information, see [Understand and adjust Streaming Units](/azure/stream-analytics/stream-analytics-streaming-unit-consumption#windowed-aggregates).</span></span>

<span data-ttu-id="23051-191">如果無法平行處理整個串流分析作業，請嘗試將作業分成多個步驟，從一或多個平行步驟開始。</span><span class="sxs-lookup"><span data-stu-id="23051-191">If it's not possible to parallelize the entire Stream Analytics job, try to break the job into multiple steps, starting with one or more parallel steps.</span></span> <span data-ttu-id="23051-192">如此一來，第一個步驟即可平行執行。</span><span class="sxs-lookup"><span data-stu-id="23051-192">That way, the first steps can run in parallel.</span></span> <span data-ttu-id="23051-193">例如，在此參考架構中：</span><span class="sxs-lookup"><span data-stu-id="23051-193">For example, in this reference architecture:</span></span>

- <span data-ttu-id="23051-194">步驟 1 和 2 是簡單的 `SELECT` 陳述式，可選取單一分割區內的記錄。</span><span class="sxs-lookup"><span data-stu-id="23051-194">Steps 1 and 2 are simple `SELECT` statements that select records within a single partition.</span></span> 
- <span data-ttu-id="23051-195">步驟 3 會跨兩個輸入資料流執行資料分割的聯結。</span><span class="sxs-lookup"><span data-stu-id="23051-195">Step 3 performs a partitioned join across two input streams.</span></span> <span data-ttu-id="23051-196">此步驟是由於相符記錄共用相同的分割區索引鍵，因此每個輸入資料流一定會有相同的分割區識別碼。</span><span class="sxs-lookup"><span data-stu-id="23051-196">This step takes advantage of the fact that matching records share the same partition key, and so are guaranteed to have the same partition ID in each input stream.</span></span>
- <span data-ttu-id="23051-197">步驟 4 會彙總所有資料分割。</span><span class="sxs-lookup"><span data-stu-id="23051-197">Step 4 aggregates across all of the partitions.</span></span> <span data-ttu-id="23051-198">此步驟無法平行處理。</span><span class="sxs-lookup"><span data-stu-id="23051-198">This step cannot be parallelized.</span></span>

<span data-ttu-id="23051-199">使用串流分析[作業圖表](/azure/stream-analytics/stream-analytics-job-diagram-with-metrics)，查看有多少分割區指派給作業中的每個步驟。</span><span class="sxs-lookup"><span data-stu-id="23051-199">Use the Stream Analytics [job diagram](/azure/stream-analytics/stream-analytics-job-diagram-with-metrics) to see how many partitions are assigned to each step in the job.</span></span> <span data-ttu-id="23051-200">下圖顯示此參考架構的作業圖表：</span><span class="sxs-lookup"><span data-stu-id="23051-200">The following diagram shows the job diagram for this reference architecture:</span></span>

![](./images/stream-processing-asa/job-diagram.png)

### <a name="cosmos-db"></a><span data-ttu-id="23051-201">Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="23051-201">Cosmos DB</span></span>

<span data-ttu-id="23051-202">Cosmos DB 的輸送量容量會以[要求單位](/azure/cosmos-db/request-units) (RU) 來測量。</span><span class="sxs-lookup"><span data-stu-id="23051-202">Throughput capacity for Cosmos DB is measured in [Request Units](/azure/cosmos-db/request-units) (RU).</span></span> <span data-ttu-id="23051-203">若要將 Cosmos DB 容器調整超過 10,000 RU，建立容器時您必須指定[分割區索引鍵](/azure/cosmos-db/partition-data)，並在每份文件中包含分割區索引鍵。</span><span class="sxs-lookup"><span data-stu-id="23051-203">In order to scale a Cosmos DB container past 10,000 RU, you must specify a [partition key](/azure/cosmos-db/partition-data) when you create the container, and include the partition key in every document.</span></span> 

<span data-ttu-id="23051-204">在此參考架構中，新文件每分鐘只會建立一次 (跳動視窗間隔)，因此輸送量需求相當低。</span><span class="sxs-lookup"><span data-stu-id="23051-204">In this reference architecture, new documents are created only once per minute (the hopping window interval), so the throughput requirements are quite low.</span></span> <span data-ttu-id="23051-205">基於這個理由，此案例不需要指派分割區索引鍵。</span><span class="sxs-lookup"><span data-stu-id="23051-205">For that reason, there's no need to assign a partition key in this scenario.</span></span>

## <a name="monitoring-considerations"></a><span data-ttu-id="23051-206">監視功能考量</span><span class="sxs-lookup"><span data-stu-id="23051-206">Monitoring considerations</span></span>

<span data-ttu-id="23051-207">使用任何串流處理解決方案，請務必監視系統效能與健康情況。</span><span class="sxs-lookup"><span data-stu-id="23051-207">With any stream processing solution, it's important to monitor the performance and health of the system.</span></span> <span data-ttu-id="23051-208">[Azure 監視器](/azure/monitoring-and-diagnostics/)會為架構中使用的 Azure 服務收集計量和診斷記錄。</span><span class="sxs-lookup"><span data-stu-id="23051-208">[Azure Monitor](/azure/monitoring-and-diagnostics/) collects metrics and diagnostics logs for the Azure services used in the architecture.</span></span> <span data-ttu-id="23051-209">Azure 監視器內建於 Azure 平台，且不需要您應用程式中任何額外的程式碼。</span><span class="sxs-lookup"><span data-stu-id="23051-209">Azure Monitor is built into the Azure platform and does not require any additional code in your application.</span></span>

<span data-ttu-id="23051-210">任何下列警告信號表示您應相應放大相關的 Azure 資源：</span><span class="sxs-lookup"><span data-stu-id="23051-210">Any of the following warning signals indicate that you should scale out the relevant Azure resource:</span></span>

- <span data-ttu-id="23051-211">事件中樞會對要求進行節流，否則會接近每日的訊息配額。</span><span class="sxs-lookup"><span data-stu-id="23051-211">Event Hubs throttles requests or is close to the daily message quota.</span></span>
- <span data-ttu-id="23051-212">串流分析作業持續使用超過 80% 的配置串流單位 (SU)。</span><span class="sxs-lookup"><span data-stu-id="23051-212">The Stream Analytics job consistently uses more than 80% of allocated Streaming Units (SU).</span></span>
- <span data-ttu-id="23051-213">Cosmos DB 會開始對要求進行節流。</span><span class="sxs-lookup"><span data-stu-id="23051-213">Cosmos DB begins to throttle requests.</span></span>

<span data-ttu-id="23051-214">參考架構包含部署至 Azure 入口網站的自訂儀表板。</span><span class="sxs-lookup"><span data-stu-id="23051-214">The reference architecture includes a custom dashboard, which is deployed to the Azure portal.</span></span> <span data-ttu-id="23051-215">部署架構之後，您可以開啟 [Azure 入口網站](https://portal.azure.com)並從儀表板清單選取 `TaxiRidesDashboard` 來檢視儀表板。</span><span class="sxs-lookup"><span data-stu-id="23051-215">After you deploy the architecture, you can view the dashboard by opening the [Azure Portal](https://portal.azure.com) and selecting `TaxiRidesDashboard` from list of dashboards.</span></span> <span data-ttu-id="23051-216">如需在 Azure 入口網站建立及部署自訂儀表板的詳細資訊，請參閱[以程式設計方式建立 Azure 儀表板](/azure/azure-portal/azure-portal-dashboards-create-programmatically)。</span><span class="sxs-lookup"><span data-stu-id="23051-216">For more information about creating and deploying custom dashboards in the Azure portal, see [Programmatically create Azure Dashboards](/azure/azure-portal/azure-portal-dashboards-create-programmatically).</span></span>

<span data-ttu-id="23051-217">串流分析作業執行大約一小時後，下圖會顯示儀表板。</span><span class="sxs-lookup"><span data-stu-id="23051-217">The following image shows the dashboard after the Stream Analytics job ran for about an hour.</span></span>

![](./images/stream-processing-asa/asa-dashboard.png)

<span data-ttu-id="23051-218">左下方的面板顯示出串流分析作業的 SU 耗用量在前 15 分鐘攀升，然後再下降。</span><span class="sxs-lookup"><span data-stu-id="23051-218">The panel on the lower left shows that the SU consumption for the Stream Analytics job climbs during the first 15 minutes and then levels off.</span></span> <span data-ttu-id="23051-219">作業達到穩定狀態時，這是典型的模式。</span><span class="sxs-lookup"><span data-stu-id="23051-219">This is a typical pattern as the job reaches a steady state.</span></span> 

<span data-ttu-id="23051-220">請注意，事件中樞正在對要求進行節流，如右上角面板所示。</span><span class="sxs-lookup"><span data-stu-id="23051-220">Notice that Event Hubs is throttling requests, shown in the upper right panel.</span></span> <span data-ttu-id="23051-221">偶爾節流的要求不是問題，因為收到節流錯誤時，事件中樞用戶端 SDK 會自動重試。</span><span class="sxs-lookup"><span data-stu-id="23051-221">An occasional throttled request is not a problem, because the Event Hubs client SDK automatically retries when it receives a throttling error.</span></span> <span data-ttu-id="23051-222">不過，如果您看到一致的節流錯誤，表示事件中樞需要更多輸送量單位。</span><span class="sxs-lookup"><span data-stu-id="23051-222">However, if you see consistent throttling errors, it means the event hub needs more throughput units.</span></span> <span data-ttu-id="23051-223">以下圖表顯示使用事件中樞自動擴充功能的測試回合，其可自動相應放大所需的輸送量單位。</span><span class="sxs-lookup"><span data-stu-id="23051-223">The following graph shows a test run using the Event Hubs auto-inflate feature, which automatically scales out the throughput units as needed.</span></span> 

![](./images/stream-processing-asa/stream-processing-eh-autoscale.png)

<span data-ttu-id="23051-224">自動擴充已於 06:35 標記啟用。</span><span class="sxs-lookup"><span data-stu-id="23051-224">Auto-inflate was enabled at about the 06:35 mark.</span></span> <span data-ttu-id="23051-225">事件中樞自動向上調整至 3 個輸送量單位時，您會在節流的要求中看到 p 下降。</span><span class="sxs-lookup"><span data-stu-id="23051-225">You can see the p drop in throttled requests, as Event Hubs automatically scaled up to 3 throughput units.</span></span>

<span data-ttu-id="23051-226">有趣的是，這樣做的副作用是在串流分析作業中增加 SU 使用率。</span><span class="sxs-lookup"><span data-stu-id="23051-226">Interestingly, this had the side effect of increasing the SU utilization in the Stream Analytics job.</span></span> <span data-ttu-id="23051-227">藉由節流，事件中樞會以人為方式減少串流分析作業的擷取率。</span><span class="sxs-lookup"><span data-stu-id="23051-227">By throttling, Event Hubs was artificially reducing the ingestion rate for the Stream Analytics job.</span></span> <span data-ttu-id="23051-228">實際上常見到，解決一個效能瓶頸會帶來另一個瓶頸。</span><span class="sxs-lookup"><span data-stu-id="23051-228">It's actually common that resolving one performance bottleneck reveals another.</span></span> <span data-ttu-id="23051-229">在此案例中，為串流分析作業配置額外的 SU 解決了問題。</span><span class="sxs-lookup"><span data-stu-id="23051-229">In this case, allocating additional SU for the Stream Analytics job resolved the issue.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="23051-230">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="23051-230">Deploy the solution</span></span>

<span data-ttu-id="23051-231">若要部署及執行參考實作，請依照 [GitHub 讀我檔案][github]中的步驟。</span><span class="sxs-lookup"><span data-stu-id="23051-231">To the deploy and run the reference implementation, follow the steps in the [GitHub readme][github].</span></span> 


[github]: https://github.com/mspnp/reference-architectures/tree/master/data/streaming_asa
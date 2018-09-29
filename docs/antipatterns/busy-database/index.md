---
title: 忙碌資料庫反模式
description: 對資料庫伺服器進行卸載處理，可能會導致效能和延展性問題。
author: dragon119
ms.date: 06/05/2017
ms.openlocfilehash: a14a350aefc1801ae08cb4a8d0eb3d5b248c92bf
ms.sourcegitcommit: 94d50043db63416c4d00cebe927a0c88f78c3219
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2018
ms.locfileid: "47428902"
---
# <a name="busy-database-antipattern"></a><span data-ttu-id="51f1b-103">忙碌資料庫反模式</span><span class="sxs-lookup"><span data-stu-id="51f1b-103">Busy Database antipattern</span></span>

<span data-ttu-id="51f1b-104">對資料庫伺服器進行卸載處理，可能會導致它花費大部分時間執行程式碼，而不是回應儲存和擷取資料的要求。</span><span class="sxs-lookup"><span data-stu-id="51f1b-104">Offloading processing to a database server can cause it to spend a significant proportion of time running code, rather than responding to requests to store and retrieve data.</span></span> 

## <a name="problem-description"></a><span data-ttu-id="51f1b-105">問題說明</span><span class="sxs-lookup"><span data-stu-id="51f1b-105">Problem description</span></span>

<span data-ttu-id="51f1b-106">許多資料庫系統可以執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="51f1b-106">Many database systems can run code.</span></span> <span data-ttu-id="51f1b-107">範例包括預存程序和觸發程序。</span><span class="sxs-lookup"><span data-stu-id="51f1b-107">Examples include stored procedures and triggers.</span></span> <span data-ttu-id="51f1b-108">通常，鄰近著資料來執行這個處理更有效率，而不是將資料傳送到用戶端應用程式進行處理。</span><span class="sxs-lookup"><span data-stu-id="51f1b-108">Often, it's more efficient to perform this processing close to the data, rather than transmitting the data to a client application for processing.</span></span> <span data-ttu-id="51f1b-109">不過，過度使用這些功能會降低效能，原因如下：</span><span class="sxs-lookup"><span data-stu-id="51f1b-109">However, overusing these features can hurt performance, for several reasons:</span></span>

- <span data-ttu-id="51f1b-110">資料庫伺服器可能會花費太多時間進行處理，而不接受新的用戶端要求及擷取資料。</span><span class="sxs-lookup"><span data-stu-id="51f1b-110">The database server may spend too much time processing, rather than accepting new client requests and fetching data.</span></span>
- <span data-ttu-id="51f1b-111">資料庫通常是共用的資源，所以會在高使用的期間成為瓶頸。</span><span class="sxs-lookup"><span data-stu-id="51f1b-111">A database is usually a shared resource, so it can become a bottleneck during periods of high use.</span></span>
- <span data-ttu-id="51f1b-112">如果資料存放區計量付費，執行階段成本可能過高。</span><span class="sxs-lookup"><span data-stu-id="51f1b-112">Runtime costs may be excessive if the data store is metered.</span></span> <span data-ttu-id="51f1b-113">受控資料庫服務更是如此。</span><span class="sxs-lookup"><span data-stu-id="51f1b-113">That's particularly true of managed database services.</span></span> <span data-ttu-id="51f1b-114">例如，[資料庫交易單位][ dtu] (DTU) 的 Azure SQL Database 費用。</span><span class="sxs-lookup"><span data-stu-id="51f1b-114">For example, Azure SQL Database charges for [Database Transaction Units][dtu] (DTUs).</span></span>
- <span data-ttu-id="51f1b-115">資料庫具有有限的相應增加容量，所以水平調整資料庫並不簡單。</span><span class="sxs-lookup"><span data-stu-id="51f1b-115">Databases have finite capacity to scale up, and it's not trivial to scale a database horizontally.</span></span> <span data-ttu-id="51f1b-116">因此，最好是將處理移到可以輕易相應放大的運算資源，例如，VM 或 App Service 應用程式。</span><span class="sxs-lookup"><span data-stu-id="51f1b-116">Therefore, it may be better to move processing into a compute resource, such as a VM or App Service app, that can easily scale out.</span></span>

<span data-ttu-id="51f1b-117">此反模式會發生通常是因為：</span><span class="sxs-lookup"><span data-stu-id="51f1b-117">This antipattern typically occurs because:</span></span>

- <span data-ttu-id="51f1b-118">資料庫檢視為服務，而不是存放庫。</span><span class="sxs-lookup"><span data-stu-id="51f1b-118">The database is viewed as a service rather than a repository.</span></span> <span data-ttu-id="51f1b-119">應用程式可能會使用資料庫伺服器來格式化資料 (例如，轉換成 XML)、操作字串資料，或執行複雜的計算。</span><span class="sxs-lookup"><span data-stu-id="51f1b-119">An application might use the database server to format data (for example, converting to XML), manipulate string data, or perform complex calculations.</span></span>
- <span data-ttu-id="51f1b-120">開發人員嘗試寫入查詢，其結果可以直接向使用者顯示。</span><span class="sxs-lookup"><span data-stu-id="51f1b-120">Developers try to write queries whose results can be displayed directly to users.</span></span> <span data-ttu-id="51f1b-121">例如，查詢可能會根據地區設定合併欄位，或格式化日期、時間和貨幣。</span><span class="sxs-lookup"><span data-stu-id="51f1b-121">For example a query might combine fields, or format dates, times, and currency according to locale.</span></span>
- <span data-ttu-id="51f1b-122">開發人員藉由將計算推送至資料庫，嘗試更正[沒有直接關聯的擷取][ExtraneousFetching]。</span><span class="sxs-lookup"><span data-stu-id="51f1b-122">Developers are trying to correct the [Extraneous Fetching][ExtraneousFetching] antipattern by pushing computations to the database.</span></span>
- <span data-ttu-id="51f1b-123">預存程序用來封裝商務邏輯，可能是因為被視為更容易維護和更新。</span><span class="sxs-lookup"><span data-stu-id="51f1b-123">Stored procedures are used to encapsulate business logic, perhaps because they are considered easier to maintain and update.</span></span>

<span data-ttu-id="51f1b-124">下列範例會擷取指定銷售領域的 20 個最有價值的訂單，並將結果格式化為 XML。</span><span class="sxs-lookup"><span data-stu-id="51f1b-124">The following example retrieves the 20 most valuable orders for a specified sales territory and formats the results as XML.</span></span>
<span data-ttu-id="51f1b-125">它會使用 Transact-SQL 函式來剖析資料，並將結果轉換成 XML。</span><span class="sxs-lookup"><span data-stu-id="51f1b-125">It uses Transact-SQL functions to parse the data and convert the results to XML.</span></span> <span data-ttu-id="51f1b-126">您可以在[這裡][sample-app]找到完整的範例。</span><span class="sxs-lookup"><span data-stu-id="51f1b-126">You can find the complete sample [here][sample-app].</span></span>

```SQL
SELECT TOP 20
  soh.[SalesOrderNumber]  AS '@OrderNumber',
  soh.[Status]            AS '@Status',
  soh.[ShipDate]          AS '@ShipDate',
  YEAR(soh.[OrderDate])   AS '@OrderDateYear',
  MONTH(soh.[OrderDate])  AS '@OrderDateMonth',
  soh.[DueDate]           AS '@DueDate',
  FORMAT(ROUND(soh.[SubTotal],2),'C')
                          AS '@SubTotal',
  FORMAT(ROUND(soh.[TaxAmt],2),'C')
                          AS '@TaxAmt',
  FORMAT(ROUND(soh.[TotalDue],2),'C')
                          AS '@TotalDue',
  CASE WHEN soh.[TotalDue] > 5000 THEN 'Y' ELSE 'N' END
                          AS '@ReviewRequired',
  (
  SELECT
    c.[AccountNumber]     AS '@AccountNumber',
    UPPER(LTRIM(RTRIM(REPLACE(
    CONCAT( p.[Title], ' ', p.[FirstName], ' ', p.[MiddleName], ' ', p.[LastName], ' ', p.[Suffix]),
    '  ', ' '))))         AS '@FullName'
  FROM [Sales].[Customer] c
    INNER JOIN [Person].[Person] p
  ON c.[PersonID] = p.[BusinessEntityID]
  WHERE c.[CustomerID] = soh.[CustomerID]
  FOR XML PATH ('Customer'), TYPE
  ),

  (
  SELECT
    sod.[OrderQty]      AS '@Quantity',
    FORMAT(sod.[UnitPrice],'C')
                        AS '@UnitPrice',
    FORMAT(ROUND(sod.[LineTotal],2),'C')
                        AS '@LineTotal',
    sod.[ProductID]     AS '@ProductId',
    CASE WHEN (sod.[ProductID] >= 710) AND (sod.[ProductID] <= 720) AND (sod.[OrderQty] >= 5) THEN 'Y' ELSE 'N' END
                        AS '@InventoryCheckRequired'

  FROM [Sales].[SalesOrderDetail] sod
  WHERE sod.[SalesOrderID] = soh.[SalesOrderID]
  ORDER BY sod.[SalesOrderDetailID]
  FOR XML PATH ('LineItem'), TYPE, ROOT('OrderLineItems')
  )

FROM [Sales].[SalesOrderHeader] soh
WHERE soh.[TerritoryId] = @TerritoryId
ORDER BY soh.[TotalDue] DESC
FOR XML PATH ('Order'), ROOT('Orders')
```

<span data-ttu-id="51f1b-127">很明顯地，這是複雜的查詢。</span><span class="sxs-lookup"><span data-stu-id="51f1b-127">Clearly, this is complex query.</span></span> <span data-ttu-id="51f1b-128">我們稍後將會看到，它轉為在資料庫伺服器上使用大量處理資源。</span><span class="sxs-lookup"><span data-stu-id="51f1b-128">As we'll see later, it turns out to use significant processing resources on the database server.</span></span>

## <a name="how-to-fix-the-problem"></a><span data-ttu-id="51f1b-129">如何修正問題</span><span class="sxs-lookup"><span data-stu-id="51f1b-129">How to fix the problem</span></span>

<span data-ttu-id="51f1b-130">將處理從資料庫伺服器移到其他應用程式層。</span><span class="sxs-lookup"><span data-stu-id="51f1b-130">Move processing from the database server into other application tiers.</span></span> <span data-ttu-id="51f1b-131">在理想情況下，您應該將資料庫限制為執行資料存取作業，僅使用資料庫已最佳化的功能，例如 RDBMS 中的彙總。</span><span class="sxs-lookup"><span data-stu-id="51f1b-131">Ideally, you should limit the database to performing data access operations, using only the capabilities that the database is optimized for, such as aggregation in an RDBMS.</span></span>

<span data-ttu-id="51f1b-132">例如，先前的 Transact-SQL 程式碼可以使用只擷取要處理的資料之陳述式來取代。</span><span class="sxs-lookup"><span data-stu-id="51f1b-132">For example, the previous Transact-SQL code can be replaced with a statement that simply retrieves the data to be processed.</span></span>

```SQL
SELECT
soh.[SalesOrderNumber]  AS [OrderNumber],
soh.[Status]            AS [Status],
soh.[OrderDate]         AS [OrderDate],
soh.[DueDate]           AS [DueDate],
soh.[ShipDate]          AS [ShipDate],
soh.[SubTotal]          AS [SubTotal],
soh.[TaxAmt]            AS [TaxAmt],
soh.[TotalDue]          AS [TotalDue],
c.[AccountNumber]       AS [AccountNumber],
p.[Title]               AS [CustomerTitle],
p.[FirstName]           AS [CustomerFirstName],
p.[MiddleName]          AS [CustomerMiddleName],
p.[LastName]            AS [CustomerLastName],
p.[Suffix]              AS [CustomerSuffix],
sod.[OrderQty]          AS [Quantity],
sod.[UnitPrice]         AS [UnitPrice],
sod.[LineTotal]         AS [LineTotal],
sod.[ProductID]         AS [ProductId]
FROM [Sales].[SalesOrderHeader] soh
INNER JOIN [Sales].[Customer] c ON soh.[CustomerID] = c.[CustomerID]
INNER JOIN [Person].[Person] p ON c.[PersonID] = p.[BusinessEntityID]
INNER JOIN [Sales].[SalesOrderDetail] sod ON soh.[SalesOrderID] = sod.[SalesOrderID]
WHERE soh.[TerritoryId] = @TerritoryId
AND soh.[SalesOrderId] IN (
    SELECT TOP 20 SalesOrderId
    FROM [Sales].[SalesOrderHeader] soh
    WHERE soh.[TerritoryId] = @TerritoryId
    ORDER BY soh.[TotalDue] DESC)
ORDER BY soh.[TotalDue] DESC, sod.[SalesOrderDetailID]
```

<span data-ttu-id="51f1b-133">接著，應用程式會使用 .NET Framework `System.Xml.Linq` API，將結果格式化為 XML。</span><span class="sxs-lookup"><span data-stu-id="51f1b-133">The application then uses the .NET Framework `System.Xml.Linq` APIs to format the results as XML.</span></span>

```csharp
// Create a new SqlCommand to run the Transact-SQL query
using (var command = new SqlCommand(...))
{
    command.Parameters.AddWithValue("@TerritoryId", id);

    // Run the query and create the initial XML document
    using (var reader = await command.ExecuteReaderAsync())
    {
        var lastOrderNumber = string.Empty;
        var doc = new XDocument();
        var orders = new XElement("Orders");
        doc.Add(orders);

        XElement lineItems = null;
        // Fetch each row in turn, format the results as XML, and add them to the XML document
        while (await reader.ReadAsync())
        {
            var orderNumber = reader["OrderNumber"].ToString();
            if (orderNumber != lastOrderNumber)
            {
                lastOrderNumber = orderNumber;

                var order = new XElement("Order");
                orders.Add(order);
                var customer = new XElement("Customer");
                lineItems = new XElement("OrderLineItems");
                order.Add(customer, lineItems);

                var orderDate = (DateTime)reader["OrderDate"];
                var totalDue = (Decimal)reader["TotalDue"];
                var reviewRequired = totalDue > 5000 ? 'Y' : 'N';

                order.Add(
                    new XAttribute("OrderNumber", orderNumber),
                    new XAttribute("Status", reader["Status"]),
                    new XAttribute("ShipDate", reader["ShipDate"]),
                    ... // More attributes, not shown.

                    var fullName = string.Join(" ",
                        reader["CustomerTitle"],
                        reader["CustomerFirstName"],
                        reader["CustomerMiddleName"],
                        reader["CustomerLastName"],
                        reader["CustomerSuffix"]
                    )
                   .Replace("  ", " ") //remove double spaces
                   .Trim()
                   .ToUpper();

               customer.Add(
                    new XAttribute("AccountNumber", reader["AccountNumber"]),
                    new XAttribute("FullName", fullName));
            }

            var productId = (int)reader["ProductID"];
            var quantity = (short)reader["Quantity"];
            var inventoryCheckRequired = (productId >= 710 && productId <= 720 && quantity >= 5) ? 'Y' : 'N';

            lineItems.Add(
                new XElement("LineItem",
                    new XAttribute("Quantity", quantity),
                    new XAttribute("UnitPrice", ((Decimal)reader["UnitPrice"]).ToString("C")),
                    new XAttribute("LineTotal", RoundAndFormat(reader["LineTotal"])),
                    new XAttribute("ProductId", productId),
                    new XAttribute("InventoryCheckRequired", inventoryCheckRequired)
                ));
        }
        // Match the exact formatting of the XML returned from SQL
        var xml = doc
            .ToString(SaveOptions.DisableFormatting)
            .Replace(" />", "/>");
    }
}
```

> [!NOTE]
> <span data-ttu-id="51f1b-134">此程式碼較為複雜。</span><span class="sxs-lookup"><span data-stu-id="51f1b-134">This code is somewhat complex.</span></span> <span data-ttu-id="51f1b-135">對於新的應用程式，您可能會想要使用序列化程式庫。</span><span class="sxs-lookup"><span data-stu-id="51f1b-135">For a new application, you might prefer to use a serialization library.</span></span> <span data-ttu-id="51f1b-136">不過，這裡的假設是開發小組重構現有的應用程式，讓方法必須傳回與原始程式碼完全相同的格式。</span><span class="sxs-lookup"><span data-stu-id="51f1b-136">However, the assumption here is that the development team is refactoring an existing application, so the method needs to return the exact same format as the original code.</span></span>

## <a name="considerations"></a><span data-ttu-id="51f1b-137">考量</span><span class="sxs-lookup"><span data-stu-id="51f1b-137">Considerations</span></span>

- <span data-ttu-id="51f1b-138">許多資料庫系統高度最佳化，以執行特定類型的資料處理，例如計算大型資料集的彙總值。</span><span class="sxs-lookup"><span data-stu-id="51f1b-138">Many database systems are highly optimized to perform certain types of data processing, such as calculating aggregate values over large datasets.</span></span> <span data-ttu-id="51f1b-139">不要將這些類型的處理移出資料庫。</span><span class="sxs-lookup"><span data-stu-id="51f1b-139">Don't move those types of processing out of the database.</span></span>

- <span data-ttu-id="51f1b-140">如果重新定位處理會導致資料庫透過網路傳送更多資料，請不要這樣做。</span><span class="sxs-lookup"><span data-stu-id="51f1b-140">Do not relocate processing if doing so causes the database to transfer far more data over the network.</span></span> <span data-ttu-id="51f1b-141">請參閱[沒有直接關聯的擷取反模式][ExtraneousFetching]。</span><span class="sxs-lookup"><span data-stu-id="51f1b-141">See the [Extraneous Fetching antipattern][ExtraneousFetching].</span></span>

- <span data-ttu-id="51f1b-142">如果您將處理移到應用程式層，該階層可能需要相應放大以處理其他工作。</span><span class="sxs-lookup"><span data-stu-id="51f1b-142">If you move processing to an application tier, that tier may need to scale out to handle the additional work.</span></span>

## <a name="how-to-detect-the-problem"></a><span data-ttu-id="51f1b-143">如何偵測問題</span><span class="sxs-lookup"><span data-stu-id="51f1b-143">How to detect the problem</span></span>

<span data-ttu-id="51f1b-144">忙碌資料庫的徵兆包含在存取資料庫的作業中不成比例的拒絕輸送量和回應時間。</span><span class="sxs-lookup"><span data-stu-id="51f1b-144">Symptoms of a busy database include a disproportionate decline in throughput and response times in operations that access the database.</span></span> 

<span data-ttu-id="51f1b-145">您可以執行下列步驟來協助識別此問題：</span><span class="sxs-lookup"><span data-stu-id="51f1b-145">You can perform the following steps to help identify this problem:</span></span> 

1. <span data-ttu-id="51f1b-146">使用效能監視，以識別生產系統執行資料庫活動所花費的時間。</span><span class="sxs-lookup"><span data-stu-id="51f1b-146">Use performance monitoring to identify how much time the production system spends performing database activity.</span></span>

2. <span data-ttu-id="51f1b-147">檢查這些期間由資料庫執行的工作。</span><span class="sxs-lookup"><span data-stu-id="51f1b-147">Examine the work performed by the database during these periods.</span></span>

3. <span data-ttu-id="51f1b-148">如果您懷疑特定作業可能會導致資料庫活動過多，在受控制的環境中執行負載測試。</span><span class="sxs-lookup"><span data-stu-id="51f1b-148">If you suspect that particular operations might cause too much database activity, perform load testing in a controlled environment.</span></span> <span data-ttu-id="51f1b-149">每個測試應該使用變數使用者負載來執行混合懷疑作業。</span><span class="sxs-lookup"><span data-stu-id="51f1b-149">Each test should run a mixture of the suspect operations with a variable user load.</span></span> <span data-ttu-id="51f1b-150">檢查負載測試的遙測，來觀察資料庫的使用方式。</span><span class="sxs-lookup"><span data-stu-id="51f1b-150">Examine the telemetry from the load tests to observe how the database is used.</span></span>

4. <span data-ttu-id="51f1b-151">如果資料庫活動顯示大量處理，但是資料流量很少，檢閱原始程式碼，以判斷處理是否可以在其他位置獲得更好的執行。</span><span class="sxs-lookup"><span data-stu-id="51f1b-151">If the database activity reveals significant processing but little data traffic, review the source code to determine whether the processing can better be performed elsewhere.</span></span>

<span data-ttu-id="51f1b-152">如果資料庫活動量低或回應時間相對快速，則忙碌資料庫不太可能是效能問題。</span><span class="sxs-lookup"><span data-stu-id="51f1b-152">If the volume of database activity is low or response times are relatively fast, then a busy database is unlikely to be a performance problem.</span></span>

## <a name="example-diagnosis"></a><span data-ttu-id="51f1b-153">範例診斷</span><span class="sxs-lookup"><span data-stu-id="51f1b-153">Example diagnosis</span></span>

<span data-ttu-id="51f1b-154">下列各節會將這些步驟套用到稍早所述的範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="51f1b-154">The following sections apply these steps to the sample application described earlier.</span></span>

### <a name="monitor-the-volume-of-database-activity"></a><span data-ttu-id="51f1b-155">監視資料庫活動量</span><span class="sxs-lookup"><span data-stu-id="51f1b-155">Monitor the volume of database activity</span></span>

<span data-ttu-id="51f1b-156">下圖顯示針對相同應用程式，使用最多 50 個並行使用者的步驟負載，執行負載測試的結果。</span><span class="sxs-lookup"><span data-stu-id="51f1b-156">The following graph shows the results of running a load test against the sample application, using a step load of up to 50 concurrent users.</span></span> <span data-ttu-id="51f1b-157">要求數量會快速達到限制並且維持在該層級，而平均回應時間則穩定增加。</span><span class="sxs-lookup"><span data-stu-id="51f1b-157">The volume of requests quickly reaches a limit and stays at that level, while the average response time steadily increases.</span></span> <span data-ttu-id="51f1b-158">請注意，針對這兩個計量使用對數範圍。</span><span class="sxs-lookup"><span data-stu-id="51f1b-158">Note that a logarithmic scale is used for those two metrics.</span></span>

![在資料庫中執行處理的負載測試結果][ProcessingInDatabaseLoadTest]

<span data-ttu-id="51f1b-160">下一個圖表會將 CPU 使用率和 DTU 顯示為服務配額百分比。</span><span class="sxs-lookup"><span data-stu-id="51f1b-160">The next graph shows CPU utilization and DTUs as a percentage of service quota.</span></span> <span data-ttu-id="51f1b-161">DTU 會提供資料庫執行多少處理的量值。</span><span class="sxs-lookup"><span data-stu-id="51f1b-161">DTUs provides a measure of how much processing the database performs.</span></span> <span data-ttu-id="51f1b-162">圖表會顯示即將達到 100% 的 CPU 和 DTU 使用率。</span><span class="sxs-lookup"><span data-stu-id="51f1b-162">The graph shows that CPU and DTU utilization both quickly reached 100%.</span></span>

![Azure SQL Database 監視會顯示執行處理時的資料庫效能][ProcessingInDatabaseMonitor]

### <a name="examine-the-work-performed-by-the-database"></a><span data-ttu-id="51f1b-164">檢查由資料庫執行的工作</span><span class="sxs-lookup"><span data-stu-id="51f1b-164">Examine the work performed by the database</span></span>

<span data-ttu-id="51f1b-165">資料庫執行的工作可能是正版資料存取作業，而不是處理，因此請務必了解當資料庫忙碌時執行的 SQL 陳述式。</span><span class="sxs-lookup"><span data-stu-id="51f1b-165">It could be that the tasks performed by the database are genuine data access operations, rather than processing, so it is important to understand the SQL statements being run while the database is busy.</span></span> <span data-ttu-id="51f1b-166">監控系統以擷取 SQL 流量，並且讓 SQL 作業與應用程式要求相互關聯。</span><span class="sxs-lookup"><span data-stu-id="51f1b-166">Monitor the system to capture the SQL traffic and correlate the SQL operations with application requests.</span></span>

<span data-ttu-id="51f1b-167">如果資料庫作業只是單純的資料存取作業，而沒有太多處理，則問題可能是[沒有直接關聯的擷取][ExtraneousFetching]。</span><span class="sxs-lookup"><span data-stu-id="51f1b-167">If the database operations are purely data access operations, without a lot of processing, then the problem might be [Extraneous Fetching][ExtraneousFetching].</span></span>

### <a name="implement-the-solution-and-verify-the-result"></a><span data-ttu-id="51f1b-168">實作解決方案並確認結果</span><span class="sxs-lookup"><span data-stu-id="51f1b-168">Implement the solution and verify the result</span></span>

<span data-ttu-id="51f1b-169">下圖顯示使用更新程式碼的負載測試。</span><span class="sxs-lookup"><span data-stu-id="51f1b-169">The following graph shows a load test using the updated code.</span></span> <span data-ttu-id="51f1b-170">輸送量相當高，相較於先前的 12 個，每秒要求數超過 400 個。</span><span class="sxs-lookup"><span data-stu-id="51f1b-170">Throughput is significantly higher, over 400 requests per second versus 12 earlier.</span></span> <span data-ttu-id="51f1b-171">平均回應時間也低非常多，相較於超過 4 秒，只要 0.1 秒。</span><span class="sxs-lookup"><span data-stu-id="51f1b-171">The average response time is also much lower, just above 0.1 seconds compared to over 4 seconds.</span></span>

![在資料庫中執行處理的負載測試結果][ProcessingInClientApplicationLoadTest]

<span data-ttu-id="51f1b-173">CPU 和 DTU 使用率會顯示儘管增加了輸送量，系統花費較長的時間才達到飽和。</span><span class="sxs-lookup"><span data-stu-id="51f1b-173">CPU and DTU utilization shows that the system took longer to reach saturation, despite the increased throughput.</span></span>

![Azure SQL Database 監視會顯示在用戶端應用程式中執行處理時的資料庫效能][ProcessingInClientApplicationMonitor]

## <a name="related-resources"></a><span data-ttu-id="51f1b-175">相關資源</span><span class="sxs-lookup"><span data-stu-id="51f1b-175">Related resources</span></span> 

- <span data-ttu-id="51f1b-176">[沒有直接關聯的擷取反模式][ExtraneousFetching]</span><span class="sxs-lookup"><span data-stu-id="51f1b-176">[Extraneous Fetching antipattern][ExtraneousFetching]</span></span>


[dtu]: /azure/sql-database/sql-database-service-tiers-dtu
[ExtraneousFetching]: ../extraneous-fetching/index.md
[sample-app]: https://github.com/mspnp/performance-optimization/tree/master/BusyDatabase

[ProcessingInDatabaseLoadTest]: ./_images/ProcessingInDatabaseLoadTest.jpg
[ProcessingInClientApplicationLoadTest]: ./_images/ProcessingInClientApplicationLoadTest.jpg
[ProcessingInDatabaseMonitor]: ./_images/ProcessingInDatabaseMonitor.jpg
[ProcessingInClientApplicationMonitor]: ./_images/ProcessingInClientApplicationMonitor.jpg

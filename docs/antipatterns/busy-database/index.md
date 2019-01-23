---
title: 忙碌資料庫反模式
titleSuffix: Performance antipatterns for cloud apps
description: 對資料庫伺服器進行卸載處理，可能會導致效能和延展性問題。
author: dragon119
ms.date: 06/05/2017
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 7d3fe47407eff7168dfd227a1dd1bd5917c7d431
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54487027"
---
# <a name="busy-database-antipattern"></a>忙碌資料庫反模式

對資料庫伺服器進行卸載處理，可能會導致它花費大部分時間執行程式碼，而不是回應儲存和擷取資料的要求。

## <a name="problem-description"></a>問題說明

許多資料庫系統可以執行程式碼。 範例包括預存程序和觸發程序。 通常，鄰近著資料來執行這個處理更有效率，而不是將資料傳送到用戶端應用程式進行處理。 不過，過度使用這些功能會降低效能，原因如下：

- 資料庫伺服器可能會花費太多時間進行處理，而不接受新的用戶端要求及擷取資料。
- 資料庫通常是共用的資源，所以會在高使用的期間成為瓶頸。
- 如果資料存放區計量付費，執行階段成本可能過高。 受控資料庫服務更是如此。 例如，[資料庫交易單位][ dtu] (DTU) 的 Azure SQL Database 費用。
- 資料庫具有有限的相應增加容量，所以水平調整資料庫並不簡單。 因此，最好是將處理移到可以輕易相應放大的運算資源，例如，VM 或 App Service 應用程式。

此反模式會發生通常是因為：

- 資料庫檢視為服務，而不是存放庫。 應用程式可能會使用資料庫伺服器來格式化資料 (例如，轉換成 XML)、操作字串資料，或執行複雜的計算。
- 開發人員嘗試寫入查詢，其結果可以直接向使用者顯示。 例如，查詢可能會根據地區設定合併欄位，或格式化日期、時間和貨幣。
- 開發人員藉由將計算推送至資料庫，嘗試更正[沒有直接關聯的擷取][ExtraneousFetching]。
- 預存程序用來封裝商務邏輯，可能是因為被視為更容易維護和更新。

下列範例會擷取指定銷售領域的 20 個最有價值的訂單，並將結果格式化為 XML。
它會使用 Transact-SQL 函式來剖析資料，並將結果轉換成 XML。 您可以在[這裡][sample-app]找到完整的範例。

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

很明顯地，這是複雜的查詢。 我們稍後將會看到，它轉為在資料庫伺服器上使用大量處理資源。

## <a name="how-to-fix-the-problem"></a>如何修正問題

將處理從資料庫伺服器移到其他應用程式層。 在理想情況下，您應該將資料庫限制為執行資料存取作業，僅使用資料庫已最佳化的功能，例如 RDBMS 中的彙總。

例如，先前的 Transact-SQL 程式碼可以使用只擷取要處理的資料之陳述式來取代。

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

接著，應用程式會使用 .NET Framework `System.Xml.Linq` API，將結果格式化為 XML。

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
> 此程式碼較為複雜。 對於新的應用程式，您可能會想要使用序列化程式庫。 不過，這裡的假設是開發小組重構現有的應用程式，讓方法必須傳回與原始程式碼完全相同的格式。

## <a name="considerations"></a>考量

- 許多資料庫系統高度最佳化，以執行特定類型的資料處理，例如計算大型資料集的彙總值。 不要將這些類型的處理移出資料庫。

- 如果重新定位處理會導致資料庫透過網路傳送更多資料，請不要這樣做。 請參閱[沒有直接關聯的擷取反模式][ExtraneousFetching]。

- 如果您將處理移到應用程式層，該階層可能需要相應放大以處理其他工作。

## <a name="how-to-detect-the-problem"></a>如何偵測問題

忙碌資料庫的徵兆包含在存取資料庫的作業中不成比例的拒絕輸送量和回應時間。

您可以執行下列步驟來協助識別此問題：

1. 使用效能監視，以識別生產系統執行資料庫活動所花費的時間。

2. 檢查這些期間由資料庫執行的工作。

3. 如果您懷疑特定作業可能會導致資料庫活動過多，在受控制的環境中執行負載測試。 每個測試應該使用變數使用者負載來執行混合懷疑作業。 檢查負載測試的遙測，來觀察資料庫的使用方式。

4. 如果資料庫活動顯示大量處理，但是資料流量很少，檢閱原始程式碼，以判斷處理是否可以在其他位置獲得更好的執行。

如果資料庫活動量低或回應時間相對快速，則忙碌資料庫不太可能是效能問題。

## <a name="example-diagnosis"></a>範例診斷

下列各節會將這些步驟套用到稍早所述的範例應用程式。

### <a name="monitor-the-volume-of-database-activity"></a>監視資料庫活動量

下圖顯示針對相同應用程式，使用最多 50 個並行使用者的步驟負載，執行負載測試的結果。 要求數量會快速達到限制並且維持在該層級，而平均回應時間則穩定增加。 請注意，針對這兩個計量使用對數範圍。

![在資料庫中執行處理的負載測試結果][ProcessingInDatabaseLoadTest]

下一個圖表會將 CPU 使用率和 DTU 顯示為服務配額百分比。 DTU 會提供資料庫執行多少處理的量值。 圖表會顯示即將達到 100% 的 CPU 和 DTU 使用率。

![Azure SQL Database 監視會顯示執行處理時的資料庫效能][ProcessingInDatabaseMonitor]

### <a name="examine-the-work-performed-by-the-database"></a>檢查由資料庫執行的工作

資料庫執行的工作可能是正版資料存取作業，而不是處理，因此請務必了解當資料庫忙碌時執行的 SQL 陳述式。 監控系統以擷取 SQL 流量，並且讓 SQL 作業與應用程式要求相互關聯。

如果資料庫作業只是單純的資料存取作業，而沒有太多處理，則問題可能是[沒有直接關聯的擷取][ExtraneousFetching]。

### <a name="implement-the-solution-and-verify-the-result"></a>實作解決方案並確認結果

下圖顯示使用更新程式碼的負載測試。 輸送量相當高，相較於先前的 12 個，每秒要求數超過 400 個。 平均回應時間也低非常多，相較於超過 4 秒，只要 0.1 秒。

![在資料庫中執行處理的負載測試結果][ProcessingInClientApplicationLoadTest]

CPU 和 DTU 使用率會顯示儘管增加了輸送量，系統花費較長的時間才達到飽和。

![Azure SQL Database 監視會顯示在用戶端應用程式中執行處理時的資料庫效能][ProcessingInClientApplicationMonitor]

## <a name="related-resources"></a>相關資源

- [沒有直接關聯的擷取反模式][ExtraneousFetching]

[dtu]: /azure/sql-database/sql-database-service-tiers-dtu
[ExtraneousFetching]: ../extraneous-fetching/index.md
[sample-app]: https://github.com/mspnp/performance-optimization/tree/master/BusyDatabase

[ProcessingInDatabaseLoadTest]: ./_images/ProcessingInDatabaseLoadTest.jpg
[ProcessingInClientApplicationLoadTest]: ./_images/ProcessingInClientApplicationLoadTest.jpg
[ProcessingInDatabaseMonitor]: ./_images/ProcessingInDatabaseMonitor.jpg
[ProcessingInClientApplicationMonitor]: ./_images/ProcessingInClientApplicationMonitor.jpg

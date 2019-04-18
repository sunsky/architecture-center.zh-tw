---
title: 重試服務的特定指引
titleSuffix: Best practices for cloud applications
description: 用於設定重試機制的服務特定指引。
author: dragon119
ms.date: 08/13/2018
ms.topic: best-practice
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 170a38f6b8a6c107670561e63f236e43af948d7d
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59641037"
---
# <a name="retry-guidance-for-specific-services"></a>特定服務的重試指引

大多數的 Azure 服務與用戶端 SDK 皆包含重試機制， 但各有不同，因為每個服務有不同的特性與需求，也因此系統會根據特定的服務調整每個重試機制。 本指南摘要說明大多數 Azure 服務的重試機制功能，並提供一些資訊幫助您使用、調整，或擴充該服務的重試機制。

如需處理暫時性錯誤，及對服務與資源重試連接與作業的一般指引，請參閱 [重試指引](./transient-faults.md)。

下表摘要說明此指引中所述 Azure 服務的重試功能。

| **服务** | **重試功能** | **原則組態** | **範圍** | **遙測功能** |
| --- | --- | --- | --- | --- |
| **[Azure Active Directory](#azure-active-directory)** |ADAL 程式庫原生 |內嵌至 ADAL 程式庫 |內部 |None |
| **[Cosmos DB](#cosmos-db)** |服務原生 |不可設定 |全局 |TraceSource |
| **Data Lake Store** |用戶端原生 |不可設定 |個別作業 |None |
| **[事件中樞](#event-hubs)** |用戶端原生 |程式設計 |用戶端 |None |
| **[IoT 中樞](#iot-hub)** |用戶端 SDK 原生 |程式設計 |用戶端 |None |
| **[Redis 快取](#azure-redis-cache)** |用戶端原生 |程式設計 |用戶端 |TextWriter |
| **[搜尋](#azure-search)** |用戶端原生 |程式設計 |用戶端 |ETW 或自訂 |
| **[服務匯流排](#service-bus)** |用戶端原生 |程式設計 |命名空間管理員、傳訊處理站及用戶端 |ETW |
| **[Service Fabric](#service-fabric)** |用戶端原生 |程式設計 |用戶端 |None |
| **[使用 ADO.NET 的 SQL Database](#sql-database-using-adonet)** |[Polly](#transient-fault-handling-with-polly) |宣告與程式設計 |單一陳述式或程式碼區塊 |自訂 |
| **[使用 Entity Framework 的 SQL Database](#sql-database-using-entity-framework-6)** |用戶端原生 |程式設計 |每個 AppDomain 全域 |None |
| **[使用 Entity Framework Core 的 SQL Database](#sql-database-using-entity-framework-core)** |用戶端原生 |程式設計 |每個 AppDomain 全域 |None |
| **[儲存體](#azure-storage)** |用戶端原生 |程式設計 |用戶端與個別作業 |TraceSource |

> [!NOTE]
> 對於大多數的 Azure 內建重試機制而言，目前還無法為不同類型的錯誤或例外狀況套用不同的重試原則。 您應該設定一個原則，提供最佳的平均效能和可用性。 微調原則的一種方法，就是分析記錄檔來判斷正在發生的暫時性錯誤類型。

<!-- markdownlint-disable MD024 MD033 -->

## <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) 是完整的身分識別與存取管理雲端解決方案，結合了核心目錄服務、進階身分識別控管、安全性，以及應用程式存取管理。 Azure AD 也提供給開發人員一個身分識別管理平台，以根據集中化的原則和規則將存取控制傳遞給其應用程式。

> [!NOTE]
> 如需受控服務識別端點的重試指引，請參閱[如何使用 Azure 虛擬機器受控服務識別 (MSI) 來取得權杖](/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling)。

### <a name="retry-mechanism"></a>重試機制

Active Directory 驗證程式庫 (ADAL) 中有內建的 Azure Active Directory 重試機制。 為了避免非預期的鎖定，建議第三方廠商程式庫和應用程式程式碼「不要」重試失敗的連線，但允許 ADAL 處理重試。

### <a name="retry-usage-guidance"></a>重試使用指引

使用 Azure Active Directory 時請考慮下列指引：

- 可能的話，使用 ADAL 程式庫和內建支援進行重試。
- 如果您正在使用 Azure Active Directory 的 REST API，請在結果碼為 429 (要求太多) 或 5xx 範圍內出現錯誤時，重試該作業。 若為任何其他錯誤，請勿重試。
- 建議將指數退避原則搭配 Azure Active Directory 用於批次案例中。

請考慮讓重試作業從下列設定開始。 這些是一般用途設定，您應該監視作業並微調其值以符合您自己的需求。

| **內容** | **範例目標 E2E<br />最大延遲** | **重試策略** | **設定** | **值** | **運作方式** |
| --- | --- | --- | --- | --- | --- |
| 互動式、UI <br />或前台 |2 秒 |FixedInterval |重試計數<br />重試間隔<br />第一個快速重試 |3<br />500 毫秒<br />true |嘗試 1 - 延遲 0 秒<br />嘗試 2 - 延遲 500 毫秒<br />嘗試 3 - 延遲 500 毫秒 |
| 背景或<br />或批处理 |60 秒 |ExponentialBackoff |重試計數<br />最小輪詢<br />最大輪詢<br />差異輪詢<br />第一個快速重試 |5<br />0 秒<br />60 秒<br />2 秒<br />false |嘗試 1 - 延遲 0 秒<br />嘗試 2 - 延遲 ~2 秒<br />嘗試 3 - 延遲 ~6 秒<br />嘗試 4 - 延遲 ~14 秒<br />嘗試 5 - 延遲 ~30 秒 |

### <a name="more-information"></a>詳細資訊

- [Azure Active Directory 驗證程式庫][adal]

## <a name="cosmos-db"></a>Cosmos DB

Cosmos DB 是完全受控的多模型資料庫，可支援無結構描述的 JSON 資料。 它提供可設定且可靠的效能、原生的 JavaScript 交易處理，而且是針對可彈性延展的雲端建置而成。

### <a name="retry-mechanism"></a>重試機制

`DocumentClient` 類別會自動重試失敗的嘗試。 若要設定重試次數和等待時間上限，請設定 [ConnectionPolicy.RetryOptions]。 用戶端引發的例外狀況可能超出重試原則，或不是暫時性的錯誤。

如果 Cosmos DB 將用戶端節流，它會傳回 HTTP 429 錯誤。 檢查 `DocumentClientException` 中的狀態碼。

### <a name="policy-configuration"></a>原則組態

下表顯示 `RetryOptions` 類別的預設設定。

| 設定 | 預設值 | 描述 |
| --- | --- | --- |
| MaxRetryAttemptsOnThrottledRequests |9 |如果要求因為 Cosmos DB 在用戶端上套用速率限制而失敗，重試次數的上限。 |
| MaxRetryWaitTimeInSeconds |30 |最大重試時間 (秒)。 |

### <a name="example"></a>範例

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); ;
var options = client.ConnectionPolicy.RetryOptions;
options.MaxRetryAttemptsOnThrottledRequests = 5;
options.MaxRetryWaitTimeInSeconds = 15;
```

### <a name="telemetry"></a>遙測

系統會透過 .NET **TraceSource**將重試嘗試記錄成非結構化的追蹤訊息。 必须配置 **TraceListener**，才能捕获事件并将这些事件写入合适的目标日志。

例如，如果您將下列新增至您的 App.config 檔案，則會在與可執行檔相同位置的文字檔案中產生追蹤：

```xml
<configuration>
  <system.diagnostics>
    <switches>
      <add name="SourceSwitch" value="Verbose"/>
    </switches>
    <sources>
      <source name="DocDBTrace" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" >
        <listeners>
          <add name="MyTextListener" type="System.Diagnostics.TextWriterTraceListener" traceOutputOptions="DateTime,ProcessId,ThreadId" initializeData="CosmosDBTrace.txt"></add>
        </listeners>
      </source>
    </sources>
  </system.diagnostics>
</configuration>
```

## <a name="event-hubs"></a>事件中樞

Azure 事件中樞是大規模的遙測擷取服務，能夠收集、轉換及儲存數百萬個事件。

### <a name="retry-mechanism"></a>重試機制

Azure 事件中樞用戶端程式庫中的重試行為是由 `EventHubClient` 類別上的 `RetryPolicy` 屬性控制。 當 Azure 事件中樞傳回暫時的 `EventHubsException` 或 `OperationCanceledException` 時，預設原則會以指數輪詢重試。

### <a name="example"></a>範例

```csharp
EventHubClient client = EventHubClient.CreateFromConnectionString("[event_hub_connection_string]");
client.RetryPolicy = RetryPolicy.Default;
```

### <a name="more-information"></a>詳細資訊

[Azure 事件中樞的 .NET 標準用戶端程式庫](https://github.com/Azure/azure-event-hubs-dotnet)

## <a name="iot-hub"></a>IoT 中樞

Azure IoT 中樞是一種用於連接、監視和管理裝置以開發物聯網 (IoT) 應用程式的服務。

### <a name="retry-mechanism"></a>重試機制

Azure IoT 裝置 SDK 可以偵測網路、通訊協定或應用程式中的錯誤。 根據錯誤類型，SDK 會檢查是否需要執行重試。 如果該錯誤可以*復原*，則 SDK 開始使用設定的重試原則進行重試。

預設的重試原則是*具隨機抖動的指數後退*，但可以加以設定。

### <a name="policy-configuration"></a>原則組態

原則設定因語言而異。 如需詳細資訊，請參閱 [IoT 中樞重試原則設定](/azure/iot-hub/iot-hub-reliability-features-in-sdks#retry-policy-apis)。

### <a name="more-information"></a>詳細資訊

- [IoT 中樞重試原則](/azure/iot-hub/iot-hub-reliability-features-in-sdks)
- [IoT 中樞裝置中斷連線的疑難排解](/azure/iot-hub/iot-hub-troubleshoot-connectivity)

## <a name="azure-redis-cache"></a>Azure Redis 快取

Azure Redis 快取是快速資料存取與低延遲性的快取服務，以普遍的開放原始碼 Redis 快取為基礎。 它很安全，且由 Microsoft 管理，可使用 Azure 的任何應用程式存取。

本節中的指引以使用 StackExchange.Redis 用戶端來存取快取為基礎。 有關其他適合的用戶端清單，請參閱 [Redis 網站](https://redis.io/clients)，這些用戶端可能有不同的重試機制。

請注意 StackExchange.Redis 用戶端透過單一連接使用多工。 建議用法是在應用程式啟動時建立用戶端的執行個體，並對快取的所有作業使用此執行個體。 基於這個理由，快取的連線只進行一次，而且因此所有的這一節中的指導方針與此初始連線的重試原則&mdash;，而不是用於存取快取每個作業。

### <a name="retry-mechanism"></a>重試機制

StackExchange.Redis 用戶端使用透過一組選項設定的連線管理員類別，包括：

- **ConnectRetry**。 重試快取失敗連接的次數。
- **ReconnectRetryPolicy**。 要使用的重試策略。
- **ConnectTimeout**。 最長等待時間，以毫秒為單位。

### <a name="policy-configuration"></a>原則組態

重試原則是以程式設計的方式設定的，做法是先設定用戶端的選項，再連接至快取。 做法是建立 **ConfigurationOptions** 類別的執行個體、填入其屬性，並將它傳遞給 **Connect** 方法。

內建類別支援線性 (固定) 延遲重試間隔以及隨機指數輪詢重試間隔。 您也可以藉由實作 **IReconnectRetryPolicy** 介面建立自訂重試原則。

下列範例使用指數輪詢設定重試策略。

```csharp
var deltaBackOffInMilliseconds = TimeSpan.FromSeconds(5).Milliseconds;
var maxDeltaBackOffInMilliseconds = TimeSpan.FromSeconds(20).Milliseconds;
var options = new ConfigurationOptions
{
    EndPoints = {"localhost"},
    ConnectRetry = 3,
    ReconnectRetryPolicy = new ExponentialRetry(deltaBackOffInMilliseconds, maxDeltaBackOffInMilliseconds),
    ConnectTimeout = 2000
};
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

或者，您可以將選項指定為字串，並將此字串傳遞到 **Connect** 方法。 請注意，**ReconnectRetryPolicy** 屬性不能這樣設定，只能透過程式碼。

```csharp
var options = "localhost,connectRetry=3,connectTimeout=2000";
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

連線至快取時，您也可以直接指定選項。

```csharp
var conn = ConnectionMultiplexer.Connect("redis0:6380,redis1:6380,connectRetry=3");
```

如需詳細資訊，請參閱 StackExchange.Redis 文件的 [Stack Exchange.Redis 設定](https://stackexchange.github.io/StackExchange.Redis/Configuration) (英文)。

下表顯示內建重試原則的預設設定。

| **內容** | **設定** | **預設值**<br />(v 1.2.2) | **意義** |
| --- | --- | --- | --- |
| ConfigurationOptions |ConnectRetry<br /><br />ConnectTimeout<br /><br />SyncTimeout<br /><br />ReconnectRetryPolicy |3<br /><br />最多 5000 毫秒加上 SyncTimeout<br />1000<br /><br />LinearRetry 5000 毫秒 |初始連線作業的重複連線嘗試次數。<br />連線作業的逾時 (毫秒)。 非重試嘗試之間的延遲。<br />允許同步作業的時間 (毫秒)。<br /><br />每 5000 毫秒重試一次。|

> [!NOTE]
> 針對同步作業，可以將 `SyncTimeout` 加入端對端延遲，但設定值過低可能會導致過多逾時。 請參閱[如何針對 Azure Redis 快取進行疑難排解][redis-cache-troubleshoot]。 一般來說，請避免使用同步作業，改為使用非同步作業。 如需詳細資訊，請參閱 [管線與多工器](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/PipelinesMultiplexers.md)。

### <a name="retry-usage-guidance"></a>重试使用指南

使用 Azure Redis 快取時請考慮下列指引：

- StackExchange Redis 用戶端會管理自己的重試，但只有在應用程式第一次啟動時對快取建立連接時才會。 您可以設定連線逾時、重試嘗試次數、重試之間的間隔時間來建立這個連線，但重試原則不適用於對快取的作業。
- 請不要使用大量的重試嘗試，而是考慮改為存取原始的資料來源來回復。

### <a name="telemetry"></a>遙測

您可以使用 **TextWriter**收集連接 (而非其他作業) 的資訊。

```csharp
var writer = new StringWriter();
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

這產生的輸出範例如下所示。

```text
localhost:6379,connectTimeout=2000,connectRetry=3
1 unique nodes specified
Requesting tie-break from localhost:6379 > __Booksleeve_TieBreak...
Allowing endpoints 00:00:02 to respond...
localhost:6379 faulted: SocketFailure on PING
localhost:6379 failed to nominate (Faulted)
> UnableToResolvePhysicalConnection on GET
No masters detected
localhost:6379: Standalone v2.0.0, master; keep-alive: 00:01:00; int: Connecting; sub: Connecting; not in use: DidNotRespond
localhost:6379: int ops=0, qu=0, qs=0, qc=1, wr=0, sync=1, socks=2; sub ops=0, qu=0, qs=0, qc=0, wr=0, socks=2
Circular op-count snapshot; int: 0 (0.00 ops/s; spans 10s); sub: 0 (0.00 ops/s; spans 10s)
Sync timeouts: 0; fire and forget: 0; last heartbeat: -1s ago
resetting failing connections to retry...
retrying; attempts left: 2...
...
```

### <a name="examples"></a>範例

下列程式碼範例會設定初始化 StackExchange.Redis 用戶端時的重試之間的固定 (線性) 延遲。 此範例示範如何使用 **ConfigurationOptions** 執行個體來設定組態。

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
    class CacheRedisCodeSamples
    {
        public async static Task Samples()
        {
            var writer = new StringWriter();
            {
                try
                {
                    var retryTimeInMilliseconds = TimeSpan.FromSeconds(4).Milliseconds; // delay between retries

                    // Using object-based configuration.
                    var options = new ConfigurationOptions
                                        {
                                            EndPoints = { "localhost" },
                                            ConnectRetry = 3,
                                            ReconnectRetryPolicy = new LinearRetry(retryTimeInMilliseconds)
                                        };
                    ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

                    // Store a reference to the multiplexer for use in the application.
                }
                catch
                {
                    Console.WriteLine(writer.ToString());
                    throw;
                }
            }
        }
    }
}
```

此範例透過將選項指定為字串來設定組態。 連線逾時是等待連線到快取的時間上限，不是重試嘗試之間的延遲。 請注意，**ReconnectRetryPolicy** 屬性只能透過程式碼設定。

```csharp
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
    class CacheRedisCodeSamples
    {
        public async static Task Samples()
        {
            var writer = new StringWriter();
            {
                try
                {
                    // Using string-based configuration.
                    var options = "localhost,connectRetry=3,connectTimeout=2000";
                    ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

                    // Store a reference to the multiplexer for use in the application.
                }
                catch
                {
                    Console.WriteLine(writer.ToString());
                    throw;
                }
            }
        }
    }
}
```

如需其他範例，請參閱專案網站上的 [組態](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Configuration.md) 。

### <a name="more-information"></a>詳細資訊

- [Redis 網站](https://redis.io/)

## <a name="azure-search"></a>Azure 搜尋服務

Azure 搜尋服務可用來在網站或應用程式中加入強大且複雜的搜尋功能、快速輕鬆地微調搜尋結果，並建構豐富且微調的排名模型。

### <a name="retry-mechanism"></a>重試機制

Azure 搜尋服務 SDK 中的重試行為是由 [SearchServiceClient] 和 [SearchIndexClient] 類別上的 `SetRetryPolicy` 方法控制。 當 Azure 搜尋服務傳回 5xx 或 408 (要求逾時) 回應時，預設原則會以指數輪詢重試。

### <a name="telemetry"></a>遙測

使用 ETW 追蹤或註冊自訂的追蹤提供者。 如需詳細資訊，請參閱 [AutoRest 文件][autorest]。

## <a name="service-bus"></a>服務匯流排

服務匯流排是雲端傳訊平台，能夠提供鬆散結合的訊息交換，以及為在雲端託管或內部部署的應用程式元件改善擴充性和恢復能力。

### <a name="retry-mechanism"></a>重試機制

服務匯流排使用 [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) 基底類別的實作來實作重試。 所有的服務匯流排用戶端皆會公開 **RetryPolicy** 屬性，此屬性可設為 **RetryPolicy** 基底類別的其中一個實作。 內建實作如下：

- [RetryExponential 類別](/dotnet/api/microsoft.servicebus.retryexponential)。 這會公開控制退避間隔的屬性、重試計數，以及用來限制作業完成總時間的 **TerminationTimeBuffer** 屬性。

- [NoRetry 類別](/dotnet/api/microsoft.servicebus.noretry)。 這用於當不需要在服務匯流排 API 層級重試時，例如當在批次或多步驟作業期間由另一個處理序管理重試時。

服務匯流排動作會傳回某個範圍的例外狀況，如[服務匯流排傳訊例外狀況](/azure/service-bus-messaging/service-bus-messaging-exceptions)中所列的清單。 此清單會提供這些重試作業的表示是否適合的資訊。 例如， **ServerBusyException** 表示用戶端應等待一段時間後，再重試作業。 **ServerBusyException** 的發生也會導致服務匯流排切換成不同的模式，在該模式中會在計算出的重試延遲中額外加上 10 秒的延遲。 此模式會在短時間後重設。

從服務匯流排傳回的例外狀況會公開 **IsTransient** 屬性，以表示用戶端是否應該重試作業。 內建的 **RetryExponential** 原則依賴 **MessagingException** 類別中的 **IsTransient** 屬性，也就是所有服務匯流排例外狀況的基底類別。 如果您建立 **RetryPolicy** 基底類別的自訂實作，您可以使用例外狀況類型和 **IsTransient** 屬性的組合，藉此更細微地控制重試動作。 例如，您可能會偵測到 **QuotaExceededException** ，並先採取動作清空佇列，再重試將訊息傳送到佇列。

### <a name="policy-configuration"></a>原則組態

重試原則是以程式設計的方式設定的，並可設為 **NamespaceManager** 與 **MessagingFactory** 兩者的預設原則，或為每個傳訊用戶端個別設為預設原則。 若要設定傳訊工作階段的預設重試原則，請設定 **NamespaceManager** 的 **RetryPolicy**。

```csharp
namespaceManager.Settings.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                                                maxBackoff: TimeSpan.FromSeconds(30),
                                                                maxRetryCount: 3);
```

若要為所有從傳訊處理站建立的用戶端設定預設重試原則，請設定 **MessagingFactory** 的 **RetryPolicy**。

```csharp
messagingFactory.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                                    maxBackoff: TimeSpan.FromSeconds(30),
                                                    maxRetryCount: 3);
```

若要為傳訊用戶端設定重試原則，或覆寫其預設原則，請使用所需原則類別的執行個體設定其 **RetryPolicy** 屬性：

```csharp
client.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                            maxBackoff: TimeSpan.FromSeconds(30),
                                            maxRetryCount: 3);
```

無法在個別的作業層級設定重試原則。 它適用於傳訊用戶端的所有作業。
下表顯示內建重試原則的預設設定。

| 設定 | 預設值 | 意義 |
|---------|---------------|---------|
| 原則 | 指數 | 指數輪詢。 |
| MinimalBackoff | 0 | 輪詢間隔最小值。 系統會將此值新增至透過 deltaBackoff 所計算出的重試間隔。 |
| MaximumBackoff | 30 秒 | 輪詢間隔最大值。 如果計算的重試間隔大於 MaxBackoff，則會使用 MaximumBackoff。 |
| DeltaBackoff | 3 秒 | 重試之間的輪詢間隔。 此時間範圍的倍數將用於後續的重試次數。 |
| TimeBuffer | 5 秒 | 與重試相關聯的終止時間緩衝區。 如果剩餘時間小於 TimeBuffer，則會放棄重試次數。 |
| MaxRetryCount | 10 | 重試次數上限。 |
| ServerBusyBaseSleepTime | 10 秒 | 如果上次發生的例外狀況為 **ServerBusyException**，系統會將這個值新增至計算的重試間隔。 無法變更此值。 |

### <a name="retry-usage-guidance"></a>重試使用指引

使用服務匯流排時請考慮下列指引：

- 當使用內建 **RetryExponential** 實作時，若原則回應伺服器忙碌例外狀況並自動切換到適當的重試模式時，請不要實作後援作業。
- 服務匯流排支援名為「配對命名空間」的功能，如果主要命名空間中的佇列失敗，此功能會實作自動容錯移轉到備份的佇列。 主要佇列復原時，次要佇列中的訊息會傳回主要佇列。 此功能有助於處理暫時性錯誤。 如需詳細資訊，請參閱 [非同步傳訊模式和高可用性](/azure/service-bus-messaging/service-bus-async-messaging)。

請考慮從重試作業的下列設定開始。 這些是一般用途設定，您應該監視作業並微調其值以符合您自己的需求。

| Context | 延遲最大值範例 | 重試原則 | 設定 | 運作方式 |
|---------|---------|---------|---------|---------|
| 互動式、UI 或前景 | 2 秒*  | 指數 | MinimumBackoff = 0 <br/> MaximumBackoff = 30 秒。 <br/> DeltaBackoff = 300 毫秒。 <br/> TimeBuffer = 300 毫秒。 <br/> MaxRetryCount = 2 | 嘗試 1：延遲 0 秒。 <br/> 嘗試 2：延遲 ~300 毫秒。 <br/> 嘗試 3：延遲 ~900 毫秒。 |
| 背景或批次 | 30 秒 | 指數 | MinimumBackoff = 1 <br/> MaximumBackoff = 30 秒。 <br/> DeltaBackoff = 1.75 秒。 <br/> TimeBuffer = 5 秒。 <br/> MaxRetryCount = 3 | 嘗試 1：延遲 ~1 秒。 <br/> 嘗試 2：延遲 ~3 秒。 <br/> 嘗試 3：延遲 ~6 毫秒。 <br/> 嘗試 4：延遲 ~13 毫秒。 |

\* 不包括系統收到伺服器忙碌回應時所新增的額外延遲。

### <a name="telemetry"></a>遙測

服務匯流排使用 **EventSource**，將重試記錄成 ETW 事件。 您必須將 **EventListener** 附加到事件來源，以擷取事件並在效能檢視器中檢視事件。 重試事件的格式如下：

```text
Microsoft-ServiceBus-Client/RetryPolicyIteration
ThreadID="14,500"
FormattedMessage="[TrackingId:] RetryExponential: Operation Get:https://retry-tests.servicebus.windows.net/TestQueue/?api-version=2014-05 at iteration 0 is retrying after 00:00:00.1000000 sleep because of Microsoft.ServiceBus.Messaging.MessagingCommunicationException: The remote name could not be resolved: 'retry-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3, TimeStamp:9/5/2014 10:00:13 PM."
trackingId=""
policyType="RetryExponential"
operation="Get:https://retry-tests.servicebus.windows.net/TestQueue/?api-version=2014-05"
iteration="0"
iterationSleep="00:00:00.1000000"
lastExceptionType="Microsoft.ServiceBus.Messaging.MessagingCommunicationException"
exceptionMessage="The remote name could not be resolved: 'retry-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3,TimeStamp:9/5/2014 10:00:13 PM"
```

### <a name="examples"></a>範例

下列範例程式碼顯示如何為下列項目設定重試原則：

- 命名空間管理員。 此原則適用於該管理員上的所有作業，而且無法針對個別作業覆寫。
- 傳訊處理站。 此原則適用於從該處理站建立的所有用戶端，當建立個別的用戶端時無法覆寫此原則。
- 個別的傳訊用戶端。 建立用戶端之後，您可以為該用戶端設定重試原則。 此原則適用於該用戶端上的所有作業。

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Messaging;

namespace RetryCodeSamples
{
    class ServiceBusCodeSamples
    {
        private const string connectionString =
            @"Endpoint=sb://[my-namespace].servicebus.windows.net/;
                SharedAccessKeyName=RootManageSharedAccessKey;
                SharedAccessKey=C99..........Mk=";

        public async static Task Samples()
        {
            const string QueueName = "TestQueue";

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.Http;

            var namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);

            // The namespace manager will have a default exponential policy with 10 retry attempts
            // and a 3 second delay delta.
            // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
            // with an extra 10 sec added when receiving a ServiceBusyException.

            {
                // Set different values for the retry policy, used for all operations on the namespace manager.
                namespaceManager.Settings.RetryPolicy =
                    new RetryExponential(
                        minBackoff: TimeSpan.FromSeconds(0),
                        maxBackoff: TimeSpan.FromSeconds(30),
                        maxRetryCount: 3);

                // Policies cannot be specified on a per-operation basis.
                if (!await namespaceManager.QueueExistsAsync(QueueName))
                {
                    await namespaceManager.CreateQueueAsync(QueueName);
                }
            }

            var messagingFactory = MessagingFactory.Create(
                namespaceManager.Address, namespaceManager.Settings.TokenProvider);
            // The messaging factory will have a default exponential policy with 10 retry attempts
            // and a 3 second delay delta.
            // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
            // with an extra 10 sec added when receiving a ServiceBusyException.

            {
                // Set different values for the retry policy, used for clients created from it.
                messagingFactory.RetryPolicy =
                    new RetryExponential(
                        minBackoff: TimeSpan.FromSeconds(1),
                        maxBackoff: TimeSpan.FromSeconds(30),
                        maxRetryCount: 3);


                // Policies cannot be specified on a per-operation basis.
                var session = await messagingFactory.AcceptMessageSessionAsync();
            }

            {
                var client = messagingFactory.CreateQueueClient(QueueName);
                // The client inherits the policy from the factory that created it.


                // Set different values for the retry policy on the client.
                client.RetryPolicy =
                    new RetryExponential(
                        minBackoff: TimeSpan.FromSeconds(0.1),
                        maxBackoff: TimeSpan.FromSeconds(30),
                        maxRetryCount: 3);

                // Policies cannot be specified on a per-operation basis.
                var session = await client.AcceptMessageSessionAsync();
            }
        }
    }
}
```

### <a name="more-information"></a>詳細資訊

- [非同步傳訊模式和高可用性](/azure/service-bus-messaging/service-bus-async-messaging)

## <a name="service-fabric"></a>Service Fabric

分散Service Fabric 叢集中可靠的服務，可防範大部分本文所討論的潛在暫時性錯誤。 不過，某些暫時性錯誤仍有可能發生。 例如，命名服務收到要求時可能正在進行路由變更，造成它擲回例外狀況。 如果相同的要求在 100 毫秒之後才收到，就可能會成功。

就內部而言，Service Fabric 會管理這種暫時性錯誤。 設定服務時，您可以使用 `OperationRetrySettings` 類別進行某些設定。 下列程式碼為範例。 大部分情況下，這並非必要，用預設設定就可以了。

```csharp
FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
{
    OperationTimeout = TimeSpan.FromSeconds(30)
};

var retrySettings = new OperationRetrySettings(TimeSpan.FromSeconds(15), TimeSpan.FromSeconds(1), 5);

var clientFactory = new FabricTransportServiceRemotingClientFactory(transportSettings);

var serviceProxyFactory = new ServiceProxyFactory((c) => clientFactory, retrySettings);

var client = serviceProxyFactory.CreateServiceProxy<ISomeService>(
    new Uri("fabric:/SomeApp/SomeStatefulReliableService"),
    new ServicePartitionKey(0));
```

### <a name="more-information"></a>詳細資訊

- [遠端例外狀況處理](/azure/service-fabric/service-fabric-reliable-services-communication-remoting#remoting-exception-handling)

## <a name="sql-database-using-adonet"></a>使用 ADO.NET 的 SQL Database

SQL Database 是託管的 SQL 資料庫，有各種大小，並以標準 (共用) 與高階 (非共用) 服務提供。

### <a name="retry-mechanism"></a>重試機制

SQL Database 在使用 ADO.NET 存取時，沒有內建的重試支援。 不過，要求的傳回碼可用來判斷要求失敗的原因。 如需有關 SQL Database 節流的詳細資訊，請參閱 [Azure SQL Database 資源限制](/azure/sql-database/sql-database-resource-limits)。 如需相關錯誤碼的清單，請參閱 [SQL Database 用戶端應用程式的 SQL 錯誤碼](/azure/sql-database/sql-database-develop-error-messages)。

您可以使用 Polly 程式庫實作 SQL Database 重試。 請參閱 [Polly 的暫時性錯誤處理](#transient-fault-handling-with-polly)。

### <a name="retry-usage-guidance"></a>重試使用指引

使用 ADO.NET 存取 SQL Database 時，請考慮下列指引：

- 選擇適當的服務選項 (共用或高階)。 共用的執行個體可能會有延遲時間比一般連接長，及因為共用伺服器有其他租用戶使用而節流的問題。 如果需要更可預測的效能和可靠的低延遲作業，請考慮選擇高階選項。
- 請確定您是在適當的層級或範圍執行重試，以避免非等冪作業導致資料不一致。 理想的狀況是，所有的作業應等冪，而因此能夠重複執行，而不會造成不一致。 若不是此狀況，則應在如果一個作業失敗，則允許復原所有相關變更的層級或範圍中執行重試；例如，從交易範圍內。 如需詳細資訊，請參閱 [雲端服務基礎資料存取層 – 暫時性錯誤處理](https://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee)。
- 互動式案例除外，不建議將固定間隔策略搭配 Azure SQL Database 使用；互動式案例中只有一些間隔非常短的重試。 相反地，請考慮為大部分的案例使用指數退避策略。
- 定義連接時，為連接與命令逾時選擇合適的值。 逾時值過短，會造成資料庫忙碌時連接永遠失敗。 逾時值過長，可能會因為在偵測失敗連接之前等待過久，而造成重試邏輯無法正確運作。 逾時值是端對端延遲的元件；它會有效地加到在重試原則中為每個重試嘗試指定的重試延遲。
- 在重試特定次數後即關閉連接，即使使用的是指數退避策略，然後在新的連接上重試作業。 在同一個連接上多次重試同一個作業，也是造成連接問題的因素之一。 有關此技術的範例，請參閱 [雲端服務基礎資料存取層 – 暫時性錯誤處理](https://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx)。
- 當使用連接共用時 (預設值)，有可能會從共用中選擇同一個連接，即使是在關閉並重新開啟連接後。 如果發生這種情況，則解決的技術是呼叫 **SqlConnection** 類別的 **ClearPool** 方法將連接標示為不可重複使用。 但只應在數次連接嘗試皆失敗後，及碰到與錯誤連接有關的特定類別暫時性錯誤時，如 SQL 逾時 (錯誤碼 -2)，才這麼做。
- 如果資料存取程式碼使用交易做為起始的 **TransactionScope** 執行個體，則重試邏輯應重新開啟連接並啟動新的交易範圍。 基於這個原因，可以重試的程式碼區塊應包含交易的整個範圍。

請考慮從重試作業的下列設定開始。 这些都是通用设置，要监视操作，并对值进行微调以适应自己的方案。

| **內容** | **範例目標 E2E<br />最大延遲** | **重試策略** | **設定** | **值** | **運作方式** |
| --- | --- | --- | --- | --- | --- |
| 互動式、UI <br />或前台 |2 秒 |FixedInterval |重試計數<br />重試間隔<br />第一個快速重試 |3<br />500 毫秒<br />true |嘗試 1 - 延遲 0 秒<br />嘗試 2 - 延遲 500 毫秒<br />嘗試 3 - 延遲 500 毫秒 |
| 后台<br />或批次 |30 秒 |ExponentialBackoff |重試計數<br />最小輪詢<br />最大輪詢<br />差異輪詢<br />第一個快速重試 |5<br />0 秒<br />60 秒<br />2 秒<br />false |嘗試 1 - 延遲 0 秒<br />嘗試 2 - 延遲 ~2 秒<br />嘗試 3 - 延遲 ~6 秒<br />嘗試 4 - 延遲 ~14 秒<br />嘗試 5 - 延遲 ~30 秒 |

> [!NOTE]
> 端對端延遲目標會假設服務連接的預設逾時值。 如果您指定較長的連接逾時值，則系統會為每個重試嘗試增加這段時間，來延長端對端延遲。

### <a name="examples"></a>範例

本節說明如何搭配在 `Policy` 類別中設定的一組重試原則，使用 Polly 存取 Azure SQL Database。

下列程式碼顯示 `SqlCommand` 類別上的擴充方法，會使用指數輪詢呼叫 `ExecuteAsync`。

```csharp
public async static Task<SqlDataReader> ExecuteReaderWithRetryAsync(this SqlCommand command)
{
    GuardConnectionIsNotNull(command);

    var policy = Policy.Handle<Exception>().WaitAndRetryAsync(
        retryCount: 3, // Retry 3 times
        sleepDurationProvider: attempt => TimeSpan.FromMilliseconds(200 * Math.Pow(2, attempt - 1)), // Exponential backoff based on an initial 200ms delay.
        onRetry: (exception, attempt) =>
        {
            // Capture some info for logging/telemetry.
            logger.LogWarn($"ExecuteReaderWithRetryAsync: Retry {attempt} due to {exception}.");
        });

    // Retry the following call according to the policy.
    await policy.ExecuteAsync<SqlDataReader>(async token =>
    {
        // This code is executed within the Policy

        if (conn.State != System.Data.ConnectionState.Open) await conn.OpenAsync(token);
        return await command.ExecuteReaderAsync(System.Data.CommandBehavior.Default, token);

    }, cancellationToken);
}
```

這個非同步擴充方法的用法如下。

```csharp
var sqlCommand = sqlConnection.CreateCommand();
sqlCommand.CommandText = "[some query]";

using (var reader = await sqlCommand.ExecuteReaderWithRetryAsync())
{
    // Do something with the values
}
```

### <a name="more-information"></a>詳細資訊

- [雲端服務基礎資料存取層 – 暫時性錯誤處理。](https://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx)

如需充分使用 SQL Database 的一般指引，請參閱 [Azure SQL 資料庫效能和彈性指南](https://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)。

## <a name="sql-database-using-entity-framework-6"></a>使用 Entity Framework 6 的 SQL Database

SQL Database 是託管的 SQL 資料庫，有各種大小，並以標準 (共用) 與高階 (非共用) 服務提供。 Entity Framework 是物件關聯式對應程式，可讓 .NET 開發人員使用網域特有的物件來處理關聯式資料。 有了 Entity Framework，開發人員便不再需要撰寫大多數必須撰寫的資料存取程式碼。

### <a name="retry-mechanism"></a>重試機制

透過名為[連線恢復/重試邏輯](/ef/ef6/fundamentals/connection-resiliency/retry-logic)的機制使用 Entity Framework 6.0 或更新版本存取 SQL Database 時，會提供重試支援。 重試機制的主要功能有：

- 主要抽象層是 **IDbExecutionStrategy** 介面。 此介面會：
  - 定義同步和非同步 **Execute*** 方法。
  - 定義類別以直接使用或在資料庫內容上設定以做為預設策略、對應至提供者名稱，或對應至提供者名稱和伺服器名稱。 在內容上設定時，重試是在個別資料庫作業層級發生的，其中可能有數個重試是針對指定的內容作業。
  - 定義何時要重試失敗的連接，以及如何重試。
- 它包含 **IDbExecutionStrategy** 介面的數個內建實作：
  - 預設值 - 無重試。
  - SQL Database 的預設值 (自動) - 沒有重試，但會檢查例外狀況，並以使用 SQL Database 策略的建議來包裝例外狀況。
  - SQL Database 的預設值 - 指數 (繼承自基底類別) 再加上 SQL Database 偵測邏輯。
- 它會實作包含隨機的指數退避策略。
- 內建的重試類別是可設定狀態，且非安全執行緒。 不過，在目前的作業完成之後可重複執行這些類別。
- 如果超出指定的重試計數，則會以新的例外狀況包裝結果。 它不会加剧当前异常。

### <a name="policy-configuration"></a>原則組態

使用 Entity Framework 6.0 或更新版本存取 SQL Database 時，會提供重試支援。 以程式設計方式設定重試原則。 無法根據預先作業變更組態。

在內容上將策略設定為預設值時，您要指定一個會視需要建立新策略的功能。 下列程式碼示範如何建立重試組態類別來延伸 **DbConfiguration** 基底類別。

```csharp
public class BloggingContextConfiguration : DbConfiguration
{
  public BlogConfiguration()
  {
    // Set up the execution strategy for SQL Database (exponential) with 5 retries and 4 sec delay
    this.SetExecutionStrategy(
         "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4)));
  }
}
```

當應用程式開啟時，您可以使用 **DbConfiguration** 執行個體的 **SetConfiguration** 方法將此指定為所有作業的預設重試策略。 依預設，EF 將自動探索和使用組態類別。

```csharp
DbConfiguration.SetConfiguration(new BloggingContextConfiguration());
```

您可以使用 **DbConfigurationType** 屬性來標註內容類別，以指定內容的重試組態類別。 不過，如果您只有一個組態類別，EF 會使用它，而不需要標註內容。

```csharp
[DbConfigurationType(typeof(BloggingContextConfiguration))]
public class BloggingContext : DbContext
```

如果您需要針對特定作業使用不同的重試策略，或針對特定作業停用重試，您可以建立組態類別來讓您透過在 **CallContext**中設定旗標，來暫停或切換策略。 組態類別可使用此旗標來切換策略，或停用您提供的策略並使用預設策略。 如需詳細資訊，請參閱[暫停執行策略](/ef/ef6/fundamentals/connection-resiliency/retry-logic#workaround-suspend-execution-strategy) (EF6 之後的版本)。

針對個別作業使用特定重試策略的另一個方法，就是建立必要策略類別的執行個體，並透過參數提供所需的設定。 然後叫用其 **ExecuteAsync** 方法。

```csharp
var executionStrategy = new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4));
var blogs = await executionStrategy.ExecuteAsync(
    async () =>
    {
        using (var db = new BloggingContext("Blogs"))
        {
            // Acquire some values asynchronously and return them
        }
    },
    new CancellationToken()
);
```

使用 **DbConfiguration** 類別最簡單的方式，就是在與 **DbContext** 類別相同的組件中找到該類別。 不過，這不適用在不同案例中需要相同的內容時，例如不同的互動式和背景重試策略。 如果不同的內容在不同的 AppDomain 中執行，您可以使用內建支援在組態檔案中指定組態類別，或使用程式碼明確地設定組態類別。 如果不同的內容必須在相同的 AppDomain 中執行，則需要自訂解決方案。

如需詳細資訊，請參閱[程式碼式組態 (EF6 之後的版本)](/ef/ef6/fundamentals/configuring/code-based)。

下表顯示使用 EF6 時內建重試原則的預設設定。

| 設定 | 預設值 | 意義 |
|---------|---------------|---------|
| 原則 | 指數 | 指數輪詢。 |
| MaxRetryCount | 5 | 重試次數上限。 |
| MaxDelay | 30 秒 | 重試之間的延遲上限。 此值不會影響延遲序列的計算方式。 它只會定義上限。 |
| DefaultCoefficient | 1 秒 | 指數輪詢計算係數。 無法變更此值。 |
| DefaultRandomFactor | 1.1 | 乘數，用來對每個項目新增隨機延遲。 無法變更此值。 |
| DefaultExponentialBase | 2 | 乘數，用來計算下一個延遲。 無法變更此值。 |

### <a name="retry-usage-guidance"></a>重試使用指引

使用 EF6 存取 SQL Database 時，請考慮下列指引：

- 選擇適當的服務選項 (共用或高階)。 共用的執行個體可能會有延遲時間比一般連接長，及因為共用伺服器有其他租用戶使用而節流的問題。 如果需要可預測的效能和可靠的低延遲作業，請考慮選擇高階選項。

- 不建議將固定間隔策略搭配 Azure SQL Database 使用。 相反地，應使用指數退避策略，因為服務可能會過載，且延遲時間越長，復原所需的時間也越長。

- 定义连接时，为连接和命令超时选择合适的值。 讓逾時以您的商務邏輯設計與直通測試為基礎。 您可能需要隨時間修改此值，因為資料量或商務程序會改變。 逾時值過短，會造成資料庫忙碌時連接永遠失敗。 逾時值過長，可能會因為在偵測失敗連接之前等待過久，而造成重試邏輯無法正確運作。 逾時值是端對端延遲的元件，雖然您無法輕易判斷儲存內容時會執行幾個命令。 您可以透過設定 **DbContext** 執行個體的 **CommandTimeout** 屬性，來變更預設逾時。

- Entity Framework 支援在組態檔中定義的重試組態。 不過，為了讓 Azure 有最大的彈性，您應該考慮在應用程式內以程式設計方式建立組態。 重試原則的特定參數 (例如重試次數與重試間隔) 可以儲存在服務組態檔中，並在執行階段用於建立適當的原則。 這會讓設定變更，而1不需要應用程式重新啟動。

請考慮讓重試作業從下列設定開始。 您無法指定重試嘗試之間的延遲 (它是固定的，並產生成為指數序列)。 您可以只指定最大值，如此處所示，除非您建立自訂重試策略。 這些是一般用途設定，您應該監視作業並微調其值以符合您自己的需求。

| **內容** | **範例目標 E2E<br />最大延遲** | **重試原則** | **设置** | **值** | **運作方式** |
| --- | --- | --- | --- | --- | --- |
| 互動式、UI <br />或前景 |2 秒 |指數 |MaxRetryCount<br />MaxDelay |3<br />750 毫秒 |嘗試 1 - 延遲 0 秒<br />嘗試 2 - 延遲 750 毫秒<br />嘗試 3 - 延遲 750 毫秒 |
| 背景<br /> 或批处理 |30 秒 |指數 |MaxRetryCount<br />MaxDelay |5<br />12 秒 |嘗試 1 - 延遲 0 秒<br />嘗試 2 - 延遲 ~1 秒<br />嘗試 3 - 延遲 ~3 秒<br />嘗試 4 - 延遲 ~7 秒<br />嘗試 5 - 延遲 12 秒 |

> [!NOTE]
> 端對端延遲目標會假設服務連接的預設逾時值。 如果您指定較長的連接逾時值，則系統會為每個重試嘗試增加這段時間，來延長端對端延遲。

### <a name="examples"></a>範例

下列程式碼範例會定義使用 Entity Framework 的簡單資料存取解決方案。 它會設定特定的重試策略，做法是為會延伸 **DbConfiguration** 的 **BlogConfiguration** 類別定義執行個體。

```csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.Entity.SqlServer;
using System.Threading.Tasks;

namespace RetryCodeSamples
{
    public class BlogConfiguration : DbConfiguration
    {
        public BlogConfiguration()
        {
            // Set up the execution strategy for SQL Database (exponential) with 5 retries and 12 sec delay.
            // These values could be loaded from configuration rather than being hard-coded.
            this.SetExecutionStrategy(
                    "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(12)));
        }
    }

    // Specify the configuration type if more than one has been defined.
    // [DbConfigurationType(typeof(BlogConfiguration))]
    public class BloggingContext : DbContext
    {
        // Definition of content goes here.
    }

    class EF6CodeSamples
    {
        public async static Task Samples()
        {
            // Execution strategy configured by DbConfiguration subclass, discovered automatically or
            // or explicitly indicated through configuration or with an attribute. Default is no retries.
            using (var db = new BloggingContext("Blogs"))
            {
                // Add, edit, delete blog items here, then:
                await db.SaveChangesAsync();
            }
        }
    }
}
```

使用 Entity Framework 重試機制的更多範例位於 [連接恢復/重試邏輯](/ef/ef6/fundamentals/connection-resiliency/retry-logic)中。

### <a name="more-information"></a>詳細資訊

- [Azure SQL Database 效能和彈性指南 (英文)](https://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)

## <a name="sql-database-using-entity-framework-core"></a>使用 Entity Framework Core 的 SQL Database

[Entity Framework Core](/ef/core/) 是物件關聯式對應程式，可讓 .NET Core 開發人員使用網域特有的物件來處理資料。 有了 Entity Framework，開發人員便不再需要撰寫大多數必須撰寫的資料存取程式碼。 這個版本的 Entity Framework 為從頭撰寫，不會自動繼承 EF6.x 的所有功能。

### <a name="retry-mechanism"></a>重試機制

透過名為[連線恢復](/ef/core/miscellaneous/connection-resiliency)的機制使用 Entity Framework Core 版本存取 SQL Database 時，會提供重試支援。 恢復連線功能是在 EF Core 1.1.0 引進。

主要抽象層是 `IExecutionStrategy` 介面。 SQL Server 包括 SQL Azure 的執行策略，知道可以重試的例外狀況類型，有合理的重試次數上限、重試之間的延遲等預設值。

### <a name="examples"></a>範例

設定 DbContext 物件時 (該物件表示資料庫的工作階段)，下列程式碼會啟用自動重試。

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(
            @"Server=(localdb)\mssqllocaldb;Database=EFMiscellanous.ConnectionResiliency;Trusted_Connection=True;",
            options => options.EnableRetryOnFailure());
}
```

下列程式碼示範如何使用執行策略來執行有自動重試的交易。 交易在委派中定義。 如果發生暫時性失敗，執行策略會再次叫用委派。

```csharp
using (var db = new BloggingContext())
{
    var strategy = db.Database.CreateExecutionStrategy();

    strategy.Execute(() =>
    {
        using (var transaction = db.Database.BeginTransaction())
        {
            db.Blogs.Add(new Blog { Url = "https://blogs.msdn.com/dotnet" });
            db.SaveChanges();

            db.Blogs.Add(new Blog { Url = "https://blogs.msdn.com/visualstudio" });
            db.SaveChanges();

            transaction.Commit();
        }
    });
}
```

## <a name="azure-storage"></a>Azure 儲存體

Azure 儲存體服務包括資料表、Blob 儲存體、檔案，以及儲存體佇列。

### <a name="retry-mechanism"></a>重試機制

重試發生在個別的 REST 作業層級，是用戶端 API 實作不可或缺的一部分。 用戶端儲存體 SDK 使用實作 [IExtendedRetryPolicy 介面](/dotnet/api/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy)的類別。

有不同的實作的介面。 儲存體用戶端會選擇特別針對存取資料表、Blob 與佇列設計的原則。 每個實作會使用不同的重試策略，這些策略基本上定義重試間隔與其他詳細資料。

內建類別支援線性 (固定延遲) 重試間隔和隨機指數重試間隔。 當另一個處理序在更高的層級處理重試時，沒有重試原則可使用。 但如果內建類別未提供您的特定需求，您可以實作您自己的重試類別。

如果您正在使用讀取權限異地備援儲存體服務位置 (RA-GRS)，替代重試會在主要與次要儲存體服務位置之間切換，要求的結果是可重試的錯誤。 如需詳細資訊，請參閱 [Azure 儲存體備援選項](/azure/storage/common/storage-redundancy) 。

### <a name="policy-configuration"></a>原則組態

以程式設計方式設定重試原則。 一般程序是建立和填入 **TableRequestOptions**、**BlobRequestOptions**、**FileRequestOptions** 或 **QueueRequestOptions** 執行個體。

```csharp
TableRequestOptions interactiveRequestOption = new TableRequestOptions()
{
  RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
  // For Read-access geo-redundant storage, use PrimaryThenSecondary.
  // Otherwise set this to PrimaryOnly.
  LocationMode = LocationMode.PrimaryThenSecondary,
  // Maximum execution time based on the business use case.
  MaximumExecutionTime = TimeSpan.FromSeconds(2)
};
```

接著可以在用戶端上設定要求選項執行個體，涉及用戶端的所有作業都將使用指定的要求選項。

```csharp
client.DefaultRequestOptions = interactiveRequestOption;
var stats = await client.GetServiceStatsAsync();
```

您可以將填入的要求選項類別執行個體做為參數傳遞至作業方法，以覆寫用戶端要求選項。

```csharp
var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
```

使用 **OperationContext** 執行個體指定當發生重試時與作業完成時，要執行的程式碼。 此程式碼可以收集用於記錄與遙測中的作業相關資訊。

```csharp
// Set up notifications for an operation
var context = new OperationContext();
context.ClientRequestID = "some request id";
context.Retrying += (sender, args) =>
{
    /* Collect retry information */
};
context.RequestCompleted += (sender, args) =>
{
    /* Collect operation completion information */
};
var stats = await client.GetServiceStatsAsync(null, context);
```

除了指出錯誤是否適合重試外，擴充的重試原則會傳回 **RetryContext** 以表示重試次數、最後一個要求的結果，以及下一個重試會在主要或次要位置發生 (請參閱下表以取得詳細資料)。 **RetryContext** 物年的屬性可用於判定是否與何時嘗試重試。 如需詳細資料，請參閱 [IExtendedRetryPolicy.Evaluate Method](/dotnet/api/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate)。

下表顯示內建重試原則的預設設定。

**要求選項：**

| **設定** | **預設值** | **意義** |
| --- | --- | --- |
| MaximumExecutionTime | None | 要求的最大執行時間，包含所有可能的重試嘗試。 如果未指定，則允許的要求採用時間量無限制。 換句話說，要求可能會停止回應。 |
| ServerTimeout | None | 要求的伺服器逾時間隔 (值四捨五入至秒)。 如果未指定，則會對伺服器的所有要求使用預設值。 通常，最好的選擇是略過此設定，以便使用伺服器預設值。 |
| LocationMode | None | 如果使用讀取權限異地備援儲存體 (RA-GRS) 複寫選項建立儲存體帳戶，您可以使用位置模式來指出哪個位置應接收要求。 例如，如果指定 **PrimaryThenSecondary**，則一律先將要求傳送至主要位置。 如果要求失敗，就會傳送到次要位置。 |
| RetryPolicy | ExponentialPolicy | 如需每個選項的詳細資訊，請參閱下列內容。 |

**指數原則：**

| **設定** | **預設值** | **意義** |
| --- | --- | --- |
| maxAttempt | 3 | 重試嘗試的次數。 |
| deltaBackoff | 4 秒 | 重試之間的輪詢間隔。 此時間範圍的倍數 (包含隨機元素)，將用於後續的重試次數。 |
| MinBackoff | 3 秒 | 新增從 deltaBackoff 計算的所有重試間隔。 無法變更此值。
| MaxBackoff | 120 秒 | 如果計算的重試間隔大於 MaxBackoff，則會使用 MaxBackoff。 無法變更此值。 |

**線性原則：**

| **設定** | **預設值** | **意義** |
| --- | --- | --- |
| maxAttempt | 3 | 重試嘗試的次數。 |
| deltaBackoff | 30 秒 | 重試之間的輪詢間隔。 |

### <a name="retry-usage-guidance"></a>重試使用指引

當使用儲存體用戶端 API 存取 Azure 儲存體服務時，請考量下列指引：

- 使用 Microsoft.WindowsAzure.Storage.RetryPolicies 命名空間的內建重試原則，此命名空間中的原則適合您的需求。 在大多數的狀況中，這些原則已經足夠。

- 在批次作業、 背景工作或非互動式案例中使用 **ExponentialRetry** 原則。 在這些情況下，您通常可以允許更多時間來復原服務&mdash;藉此增加作業最後成功的機會。

- 請考慮指定 **RequestOptions** 參數的 **MaximumExecutionTime** 屬性，以限制總執行時間，但在選擇逾時值時要考量到作業的類型與大小。

- 如果您需要實作自訂重試，請避免建立儲存體用戶端類別周圍的包裝函式。 相反地，使用一些功能透過 **IExtendedRetryPolicy** 介面來擴充現有的原則。

- 如果您使用的是讀取權限異地備援儲存體 (RA-GRS)，您可以使用 **LocationMode** 指定如果主要存取失敗，重試嘗試將會存取存放區的次要唯讀複本。 不過使用這個選項時，若尚未完成從主要存放區複寫，則必須確定應用程式可以成功使用過時的資料。

请考虑从下列重试操作设置入手。 这些都是通用设置，要监视操作，并对值进行微调以适应自己的方案。

| **內容** | **範例目標 E2E<br />最大延遲** | **重試原則** | **设置** | **值** | **運作方式** |
| --- | --- | --- | --- | --- | --- |
| 互動式、UI <br />或前景 |2 秒 |线性 |maxAttempt<br />deltaBackoff |3<br />500 毫秒 |嘗試 1 - 延遲 500 毫秒<br />嘗試 2 - 延遲 500 毫秒<br />嘗試 3 - 延遲 500 毫秒 |
| 后台<br />或批处理 |30 秒 |指數 |maxAttempt<br />deltaBackoff |5<br />4 秒 |嘗試 1 - 延遲 ~3 毫秒<br />嘗試 2 - 延遲 ~7 秒<br />嘗試 3 - 延遲 ~15 毫秒 |

### <a name="telemetry"></a>遙測

重試嘗試會記錄到 **TraceSource**。 您必須設定 **TraceListener** 來擷取事件，並將事件寫入適當的目的地記錄中。 您可以使用 **TextWriterTraceListener** 或 **XmlWriterTraceListener** 將資料寫入記錄檔，使用 **EventLogTraceListener** 寫入 Windows 事件記錄檔，或使用 **EventProviderTraceListener** 將追蹤資料寫入 ETW 子系統。 您也可以設定自動排清緩衝區，並設定所記錄事件的詳細資訊 (例如錯誤、警告、資訊和詳細資訊)。 如需詳細資訊，請參閱 [使用 .NET 儲存體用戶端程式庫在用戶端記錄](/rest/api/storageservices/Client-side-Logging-with-the-.NET-Storage-Client-Library)。

作業會接收 **OperationContext** 執行個體，以公開用於附加自訂遙測邏輯的 **Retrying** 事件。 如需詳細資訊，請參閱 [OperationContext.Retrying Event](/dotnet/api/microsoft.windowsazure.storage.operationcontext.retrying)。

### <a name="examples"></a>範例

下列程式碼範例示範如何建立兩個具有不同重試設定的 **TableRequestOptions** 執行個體，一個用於互動式要求，一個用於背景要求。 然後此範例會在用戶端上設定這兩個重試原則，讓這些原則適用於所有要求，此外也在特定要求上設定互動式策略，讓該策略覆寫套用到用戶端的預設設定。

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.RetryPolicies;
using Microsoft.WindowsAzure.Storage.Table;

namespace RetryCodeSamples
{
    class AzureStorageCodeSamples
    {
        private const string connectionString = "UseDevelopmentStorage=true";

        public async static Task Samples()
        {
            var storageAccount = CloudStorageAccount.Parse(connectionString);

            TableRequestOptions interactiveRequestOption = new TableRequestOptions()
            {
                RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
                // For Read-access geo-redundant storage, use PrimaryThenSecondary.
                // Otherwise set this to PrimaryOnly.
                LocationMode = LocationMode.PrimaryThenSecondary,
                // Maximum execution time based on the business use case.
                MaximumExecutionTime = TimeSpan.FromSeconds(2)
            };

            TableRequestOptions backgroundRequestOption = new TableRequestOptions()
            {
                // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
                // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
                MaximumExecutionTime = TimeSpan.FromSeconds(30),
                // PrimaryThenSecondary in case of Read-access geo-redundant storage, else set this to PrimaryOnly
                LocationMode = LocationMode.PrimaryThenSecondary
            };

            var client = storageAccount.CreateCloudTableClient();
            // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
            // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
            // ServerTimeout and MaximumExecutionTime are not set

            {
                // Set properties for the client (used on all requests unless overridden)
                // Different exponential policy parameters for background scenarios
                client.DefaultRequestOptions = backgroundRequestOption;
                // Linear policy for interactive scenarios
                client.DefaultRequestOptions = interactiveRequestOption;
            }

            {
                // set properties for a specific request
                var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
            }

            {
                // Set up notifications for an operation
                var context = new OperationContext();
                context.ClientRequestID = "some request id";
                context.Retrying += (sender, args) =>
                {
                    /* Collect retry information */
                };
                context.RequestCompleted += (sender, args) =>
                {
                    /* Collect operation completion information */
                };
                var stats = await client.GetServiceStatsAsync(null, context);
            }
        }
    }
}
```

### <a name="more-information"></a>詳細資訊

- [Azure 儲存體用戶端程式庫重試原則建議](https://azure.microsoft.com/blog/2014/05/22/azure-storage-client-library-retry-policy-recommendations/)

- [儲存體用戶端程式庫 2.0 – 實作重試原則](https://gauravmantri.com/2012/12/30/storage-client-library-2-0-implementing-retry-policies/)

## <a name="general-rest-and-retry-guidelines"></a>一般 REST 與重試指引

存取 Azure 或協力廠商服務時請考量下列事項：

- 使用系統化的方法管理重試，或許是做為可重複使用的程式碼，讓您可以在所有的用戶端與所有的解決方案之間套用一致的方法。

- 如果目標服務或用戶端有沒有內建的重試機制，請考慮使用重試架構 (例如 [Polly][polly]) 來管理重試。 這可以幫助您實作一致的重試行為，且可能會為目標服務提供適合的預設重試策略。 不過，您可能需要為具有非標準行為的服務建立自訂重試程式碼，非標準行為不依例外狀況來表示暫時性錯誤，或如果您想要使用 **Retry-Response** 來管理重試行為，也必須建立自訂重試程式碼。

- 暫時性偵測邏輯將取決於您用來叫用 REST 呼叫的實際用戶端 API。 有些用戶端 (例如較新的 **HttpClient** 類別)，不會為已完成的要求擲回狀態碼為 HTTP 不成功的例外狀況。

- 從服務傳回的 HTTP 狀態碼可協助指出錯誤是否為暫時性。 您可能需要檢查用戶端產生的例外狀況，或檢查重試架構，以存取狀態碼或決定等同的例外狀況類型。 下列 HTTP 代碼通常表示重試是適當的：
  
  - 408 要求逾時
  - 429 要求太多
  - 500 內部伺服器錯誤
  - 502 错误的网关
  - 503 服務無法使用
  - 504 閘道逾時

- 如果您讓重試邏輯以例外狀況為基礎，則下列項目通常表示無法建立連接的暫時性錯誤：

  - WebExceptionStatus.ConnectionClosed
  - WebExceptionStatus.ConnectFailure
  - WebExceptionStatus.Timeout
  - WebExceptionStatus.RequestCanceled

- 在服務無法使用狀態下，服務可能會先指出適當的延遲，然後在 **Retry-After** 回應標頭或不同的自訂標頭中重試。 服務也可能傳送額外的資訊做為自訂標頭，或內嵌在回應的內容中。

- 408 要求逾時除外，不要重試代表用戶端錯誤 (4xx 範圍中的錯誤) 的狀態碼。

- 在各種條件下 (例如不同的網路狀態及各種的系統負載) 徹底測試您的重試策略和機制。

### <a name="retry-strategies"></a>重試策略

以下為一般類型的重試策略間隔：

- **指數**。 此重試原則會執行指定的重試次數，並使用隨機的指數退避方法來決定重試之間的間隔。 例如︰

    ```csharp
    var random = new Random();

    var delta = (int)((Math.Pow(2.0, currentRetryCount) - 1.0) *
                random.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8),
                (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
    var interval = (int)Math.Min(checked(this.minBackoff.TotalMilliseconds + delta),
                    this.maxBackoff.TotalMilliseconds);
    retryInterval = TimeSpan.FromMilliseconds(interval);
    ```

- **累加**。 此重試策略具有指定的重試嘗試次數，並在重試之間使用累加時間間隔。 例如︰

    ```csharp
    retryInterval = TimeSpan.FromMilliseconds(this.initialInterval.TotalMilliseconds +
                    (this.increment.TotalMilliseconds * currentRetryCount));
    ```

- **LinearRetry**。 此重試原則會執行指定的重試次數，並在重試之間使用指定的固定時間間隔。 例如︰

    ```csharp
    retryInterval = this.deltaBackoff;
    ```

### <a name="transient-fault-handling-with-polly"></a>Polly 的暫時性錯誤處理

[Polly] [ polly]是程式庫來以程式設計方式處理重試並[斷路器](../patterns/circuit-breaker.md)策略。 Polly 專案是 [.NET Foundation][dotnet-foundation] 的成員。 當服務所在的用戶端本身不支援重試，Polly 是有效的替代方法，可以不必撰寫自訂重試程式碼，但有可能難以正確實作。 Polly 也可用來在錯誤發生時追蹤錯誤，讓您可以記錄重試。

### <a name="more-information"></a>詳細資訊

- [連接恢復功能](/ef/core/miscellaneous/connection-resiliency)
- [Data Points - EF Core 1.1](https://msdn.microsoft.com/magazine/mt745093.aspx)

<!-- links -->

[adal]: /azure/active-directory/develop/active-directory-authentication-libraries
[autorest]: https://github.com/Azure/autorest/tree/master/docs
[ConnectionPolicy.RetryOptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.retryoptions.aspx
[dotnet-foundation]: https://dotnetfoundation.org/
[polly]: http://www.thepollyproject.org
[redis-cache-troubleshoot]: /azure/redis-cache/cache-how-to-troubleshoot
[SearchIndexClient]: https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchindexclient.aspx
[SearchServiceClient]: https://msdn.microsoft.com/library/microsoft.azure.search.searchserviceclient.aspx

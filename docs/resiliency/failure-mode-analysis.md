---
title: "失敗模式分析"
description: "針對以 Azure 為基礎的雲端解決方案執行失敗模式分析的指導方針。"
author: MikeWasson
ms.date: 03/24/2017
ms.custom: resiliency
pnp.series.title: Design for Resiliency
ms.openlocfilehash: 09d09468eebe5c6fe1c9cdab14e142ff46cf0b25
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="failure-mode-analysis"></a>失敗模式分析
[!INCLUDE [header](../_includes/header.md)]

失敗模式分析 (FMA) 是一項程序，可藉由識別系統中可能的失敗點，在系統中建置復原功能。 FMA 應該加入成為架構和設計階段的一部分，這樣您才能在系統中從頭建置失敗復原。

以下是用來進行 FMA 的一般程序：

1. 識別系統中的所有元件。 包含外部相依性，例如，作為身分識別提供者、第三方服務等等。   
2. 識別每個元件可能發生的潛在失敗。 單一元件可能會有多個失敗模式。 例如，您應該分開考慮讀取失敗和寫入失敗，因為其影響和可能的補救措施會不同。
3. 根據每個失敗模式的整體風險來為這些模式訂定評等。 請考慮下列因素：  

   * 失敗的可能性有多高。 相對常見嗎？ 極為罕見嗎？ 您不需要確切的數字；我們的目的是為了協助排列優先順序。
   * 對應用程式的影響為何 (就可用性、資料遺失、貨幣成本和業務中斷等方面來說)？
4. 為每個失敗模式決定應用程式的回應和復原方式。 考慮成本和應用程式複雜性的取捨。   

作為 FMA 程序的起點，本文包含可能之失敗模式及其補救措施的目錄。 此目錄會依技術或 Azure 服務，再加上應用程式層級設計的一般類別來分類。 此目錄並不完整，但會涵蓋許多核心的 Azure 服務。

## <a name="app-service"></a>App Service
### <a name="app-service-app-shuts-down"></a>App Service 應用程式關閉。
**偵測**。 可能的原因：

* 預期性關閉

  * 操作員關閉應用程式；例如，使用 Azure 入口網站。
  * 應用程式因為閒置而卸載。 (當 `Always On` 設定停用時才會發生)。
* 非預期性關閉

  * 應用程式當機。
  * App Service VM 執行個體變得無法使用。

Application_End 記錄會捕捉到應用程式網域關閉 (軟程序當機)，而且也只有此記錄會捕捉到應用程式網域關閉。

**復原**

* 如果是預期性關閉，請使用應用程式的關閉事件來正常關閉。 例如，在 ASP.NET 中使用 `Application_End` 方法。
* 如果應用程式已於閒置時卸載，它會在下一個要求到來時自動重新啟動。 不過，您會產生「冷啟動」成本。
* 若要防止應用程式在閒置時卸載，請在 Web 應用程式中啟用 `Always On` 設定。 請參閱[在 Azure App Service 中設定 Web 應用程式][app-service-configure]。
* 若要防止操作員關閉應用程式，請以 `ReadOnly` 層級設定資源鎖定。 請參閱[使用 Azure Resource Manager 鎖定資源][rm-locks]。
* 如果應用程式當機或 App Service VM 變得無法使用，App Service 會自動重新啟動應用程式。

**診斷**。 應用程式記錄和 Web 伺服器記錄。 請參閱[在 Azure App Service 中針對 Web 應用程式啟用診斷記錄功能][app-service-logging]。

### <a name="a-particular-user-repeatedly-makes-bad-requests-or-overloads-the-system"></a>特定使用者重複提出不正確的要求，或讓多載系統。
**偵測**。 驗證使用者和在應用程式記錄中納入使用者識別碼。

**復原**

* 使用 [Azure API 管理][api-management] 對該使用者的要求進行節流處理。 請參閱[以 Azure API 管理進行進階要求節流][api-management-throttling]
* 封鎖該使用者。

**診斷**。 記錄所有驗證要求。

### <a name="a-bad-update-was-deployed"></a>所部署的更新不正確。
**偵測**。 透過 Azure 入口網站監視應用程式健康情況 (請參閱[監視 Azure Web 應用程式效能][app-insights-web-apps]) 或實作[健康情況端點監視模式][health-endpoint-monitoring-pattern]。

**復原**。 使用多個[部署位置][app-service-slots]並復原為已知正確的最後一個部署。 如需詳細資訊，請參閱[基本 Web 應用程式][ra-web-apps-basic]。

## <a name="azure-active-directory"></a>Azure Active Directory
### <a name="openid-connect-oidc-authentication-fails"></a>OpenID Connect (OIDC) 驗證失敗。
**偵測**。 可能的失敗模式包括：

1. Azure AD 無法使用，或由於網路問題而無法連線。 重新導向至驗證端點時失敗，而且 OIDC 中介軟體擲回例外狀況。
2. Azure AD 租用戶不存在。 重新導向至驗證端點時傳回 HTTP 錯誤碼，而且 OIDC 中介軟體擲回例外狀況。
3. 使用者無法通過驗證。 不需要偵測策略；Azure AD 會處理登入失敗。

**復原**

1. 從中介軟體捕捉未處理的例外狀況。
2. 處理 `AuthenticationFailed` 事件。
3. 將使用者重新導向到錯誤頁面。
4. 使用者進行重試。

## <a name="azure-search"></a>Azure 搜尋服務
### <a name="writing-data-to-azure-search-fails"></a>將資料寫入 Azure 搜尋服務時失敗。
**偵測**。 捕捉 `Microsoft.Rest.Azure.CloudException` 錯誤。

**復原**

[搜尋服務 .NET SDK][search-sdk] 會在暫時性失敗後自動重試。 您應該將用戶端 SDK 擲回的任何例外狀況都視為非暫時性錯誤。

預設的重試原則會使用指數輪詢。 若要使用不同的重試原則，請在 `SearchIndexClient` 或 `SearchServiceClient` 類別上呼叫 `SetRetryPolicy`。 如需詳細資訊，請參閱[自動重試][auto-rest-client-retry]。

**診斷**。 使用[搜尋服務流量分析][search-analytics]。

### <a name="reading-data-from-azure-search-fails"></a>從 Azure 搜尋服務讀取資料時失敗。
**偵測**。 捕捉 `Microsoft.Rest.Azure.CloudException` 錯誤。

**復原**

[搜尋服務 .NET SDK][search-sdk] 會在暫時性失敗後自動重試。 您應該將用戶端 SDK 擲回的任何例外狀況都視為非暫時性錯誤。

預設的重試原則會使用指數輪詢。 若要使用不同的重試原則，請在 `SearchIndexClient` 或 `SearchServiceClient` 類別上呼叫 `SetRetryPolicy`。 如需詳細資訊，請參閱[自動重試][auto-rest-client-retry]。

**診斷**。 使用[搜尋服務流量分析][search-analytics]。

## <a name="cassandra"></a>Cassandra
### <a name="reading-or-writing-to-a-node-fails"></a>讀取或寫入至節點時失敗。
**偵測**。 捕捉例外狀況。 若為 .NET 用戶端，這通常會是 `System.Web.HttpException`。 其他用戶端可能有其他的例外狀況類型。  如需詳細資訊，請參閱[正確處理 Cassandra 錯誤](http://www.datastax.com/dev/blog/cassandra-error-handling-done-right)。

**復原**

* 每個 [Cassandra 用戶端](https://wiki.apache.org/cassandra/ClientOptions)都有自己的重試原則和功能。 如需詳細資訊，請參閱[正確處理 Cassandra 錯誤][cassandra-error-handling]。
* 使用機架感知部署，並將資料節點分散於所有容錯網域。
* 利用本機仲裁一致性部署至多個區域。 如果發生非暫時性失敗，則容錯移轉至其他區域。

**診斷**。 應用程式記錄檔

## <a name="cloud-service"></a>服務雲端
### <a name="web-or-worker-roles-are-unexpectedlybeing-shut-down"></a>Web 角色或背景工作角色遭到意外關閉。
**偵測**。 系統引發了 [RoleEnvironment.Stopping][RoleEnvironment.Stopping] 事件。

**復原**。 覆寫 [RoleEntryPoint.OnStop][RoleEntryPoint.OnStop] 方法以便正常地清除。 如需詳細資訊，請參閱 [Azure OnStop 事件的正確處理方式][onstop-events] (部落格)。

## <a name="cosmos-db"></a>Cosmos DB 
### <a name="reading-data-fails"></a>讀取資料時失敗。
**偵測**。 捕捉 `System.Net.Http.HttpRequestException` 或 `Microsoft.Azure.Documents.DocumentClientException`。

**復原**

* SDK 會自動重試失敗的嘗試。 若要設定重試次數和等待時間上限，請設定 `ConnectionPolicy.RetryOptions`。 用戶端引發的例外狀況可能超出重試原則，或不是暫時性的錯誤。
* 如果 Cosmos DB 將用戶端節流，它會傳回 HTTP 429 錯誤。 檢查 `DocumentClientException` 中的狀態碼。 如果您一直收到 429 錯誤，請考慮增加集合的輸送量值。
    * 如果您使用 MongoDB API，節流時服務會傳回錯誤碼 16500。
* 跨兩個以上的區域複寫 Cosmos DB 資料庫。 所有複本都要可供讀取。 使用用戶端 SDK 指定 `PreferredLocations` 參數。 這是 Azure 區域的排序清單。 所有讀取都會傳送到清單中的第一個可用區域。 如果要求失敗，用戶端會依序嘗試清單中的其他區域。 如需詳細資訊，請參閱[如何使用 DocumentDB API 來設定 Azure Cosmos DB 全域散發][docdb-multi-region]。

**診斷**。 記錄用戶端上的所有錯誤。

### <a name="writing-data-fails"></a>寫入資料時失敗。
**偵測**。 捕捉 `System.Net.Http.HttpRequestException` 或 `Microsoft.Azure.Documents.DocumentClientException`。

**復原**

* SDK 會自動重試失敗的嘗試。 若要設定重試次數和等待時間上限，請設定 `ConnectionPolicy.RetryOptions`。 用戶端引發的例外狀況可能超出重試原則，或不是暫時性的錯誤。
* 如果 Cosmos DB 將用戶端節流，它會傳回 HTTP 429 錯誤。 檢查 `DocumentClientException` 中的狀態碼。 如果您一直收到 429 錯誤，請考慮增加集合的輸送量值。
* 跨兩個以上的區域複寫 Cosmos DB 資料庫。 如果主要區域失敗，系統會將另一個區域升階以便寫入。 您也可以手動觸發容錯移轉。 SDK 會進行自動探索及路由，讓應用程式程式碼可以在容錯移轉後繼續運作。 在容錯移轉期間 (通常是幾分鐘)，SDK 會尋找新的寫入區域，因此寫入作業的延遲會比較高。
  如需詳細資訊，請參閱[如何使用 DocumentDB API 來設定 Azure Cosmos DB 全域散發][docdb-multi-region]。
* 將文件保存至備份佇列，並於稍後處理佇列，以作為後援手段。

**診斷**。 記錄用戶端上的所有錯誤。

## <a name="elasticsearch"></a>Elasticsearch
### <a name="reading-data-from-elasticsearch-fails"></a>從 Elasticsearch 讀取資料時失敗。
**偵測**。 針對正在使用的特定 [Elasticsearch 用戶端][elasticsearch-client] 捕捉到適當的例外狀況。

**復原**

* 使用重試機制。 每個用戶端都有自己的重試原則。
* 請部署多個 Elasticsearch 節點，並使用複寫功能以獲得高可用性。

如需詳細資訊，請參閱[在 Azure 上執行 Elasticsearch][elasticsearch-azure]。

**診斷**。 您可以使用 Elasticsearch 的監視工具，或在用戶端上使用承載記錄所有錯誤。 請參閱[在 Azure 上執行 Elasticsearch][elasticsearch-azure] 中的＜監視＞一節。

### <a name="writing-data-to-elasticsearch-fails"></a>將資料寫入 Elasticsearch 時失敗。
**偵測**。 針對正在使用的特定 [Elasticsearch 用戶端][elasticsearch-client] 捕捉到適當的例外狀況。  

**復原**

* 使用重試機制。 每個用戶端都有自己的重試原則。
* 如果應用程式可以容忍一致性層級降低，請考慮使用 `quorum` 的 `write_consistency` 設定來寫入。

如需詳細資訊，請參閱[在 Azure 上執行 Elasticsearch][elasticsearch-azure]。

**診斷**。 您可以使用 Elasticsearch 的監視工具，或在用戶端上使用承載記錄所有錯誤。 請參閱[在 Azure 上執行 Elasticsearch][elasticsearch-azure] 中的＜監視＞一節。

## <a name="queue-storage"></a>佇列儲存體
### <a name="writing-a-message-to-azure-queue-storage-fails-consistently"></a>將訊息寫入 Azure 佇列儲存體時一直失敗。
**偵測**。 嘗試 N 次重試之後，寫入作業仍舊失敗。

**復原**

* 將資料儲存在本機快取，並於之後服務恢復可用時將寫入轉送至儲存體。
* 建立次要佇列，並在主要佇列無法使用時寫入至該佇列。

**診斷**。 使用[儲存體度量][storage-metrics]。

### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>應用程式無法處理來自該佇列的特定訊息。
**偵測**。 應用程式所特有。 例如，訊息包含無效資料，或是商務邏輯因故失敗。

**復原**

將訊息移至其他佇列。 執行其他處理序以檢查該佇列中的訊息。

請考慮使用 Azure 服務匯流排傳訊佇列，其可提供[無效信件佇列][sb-dead-letter-queue]以便用於此目的。

> [!NOTE]
> 如果您搭配使用儲存體佇列和 WebJobs，WebJobs SDK 會提供內建的有害訊息處理功能。 請參閱[如何透過 WebJob SDK 使用 Azure 佇列儲存體 (英文)][sb-poison-message]。

**診斷**。 使用應用程式記錄。

## <a name="redis-cache"></a>Redis 快取
### <a name="reading-from-the-cache-fails"></a>讀取快取時失敗。
**偵測**。 捕捉 `StackExchange.Redis.RedisConnectionException`。

**復原**

1. 重試暫時性失敗。 Azure Redis 快取支援內建重試功能。請參閱 [Redis 快取重試指引][redis-retry]。
2. 將非暫時性失敗視為快取遺漏，並切換回原始資料來源。

**診斷**。 使用 [Redis 快取診斷][redis-monitor]。

### <a name="writing-to-the-cache-fails"></a>寫入至快取時失敗。
**偵測**。 捕捉 `StackExchange.Redis.RedisConnectionException`。

**復原**

1. 重試暫時性失敗。 Azure Redis 快取支援內建重試功能。請參閱 [Redis 快取重試指引][redis-retry]。
2. 如果是非暫時性錯誤，請予以忽略，並於稍後讓其他交易寫入至快取。

**診斷**。 使用 [Redis 快取診斷][redis-monitor]。

## <a name="sql-database"></a>SQL Database
### <a name="cannot-connect-to-the-database-in-the-primary-region"></a>無法連線到主要區域中的資料庫。
**偵測**。 連線失敗。

**復原**

必要條件：資料庫必須設定作用中異地複寫。 請參閱 [SQL Database 作用中異地複寫][sql-db-replication]。

* 若為查詢，讀取次要複本。
* 若為插入和更新，手動容錯移轉至次要複本。 請參閱[為 Azure SQL Database 起始計劃性或非計劃性容錯移轉][sql-db-failover]。

複本會使用不同的連接字串，因此您必須更新應用程式中的連接字串。

### <a name="client-runs-out-of-connections-in-the-connection-pool"></a>用戶端用盡連線集區中的連線。
**偵測**。 捕捉 `System.InvalidOperationException` 錯誤。

**復原**

* 重試作業。
* 作為風險降低計畫，請隔離每個使用案例的連線集區，不要讓一個使用案例就能支配所有連線。
* 增加連線集區上限。

**診斷**。 應用程式記錄。

### <a name="database-connection-limit-is-reached"></a>達到資料庫連線限制。
**偵測**。 Azure SQL Database 會限制並行之工作者、登入和工作階段的數目。 這些限制取決於服務層。 如需詳細資訊，請參閱 [Azure SQL Database 資源限制][sql-db-limits]。

若要偵測這些錯誤，請捕捉 `System.Data.SqlClient.SqlException` 並檢查 SQL 錯誤碼的 `SqlException.Number` 值。 如需相關錯誤碼的清單，請參閱 [SQL Database 用戶端應用程式的 SQL 錯誤碼：資料庫連線錯誤和其他問題][sql-db-errors]。

**復原**。 這些錯誤應該是暫時性的，因此重試後或許就能解決問題。 如果您一直遇到這些錯誤，請考慮調整資料庫。

**診斷**。 - [sys.event_log][sys.event_log] 查詢會傳回成功的資料庫連線、連線失敗和死結。

* 為失敗的連線建立[警示規則][azure-alerts]。
* 啟用 [SQL Database 稽核][sql-db-audit]，並檢查失敗的登入。

## <a name="service-bus-messaging"></a>服務匯流排傳訊
### <a name="reading-a-message-from-a-service-bus-queue-fails"></a>從服務匯流排佇列讀取訊息時失敗。
**偵測**。 從用戶端 SDK 捕捉例外狀況。 服務匯流排例外狀況的基底類別是 [MessagingException][sb-messagingexception-class]。 如果是暫時性錯誤，`IsTransient` 屬性會是 true。

如需詳細資訊，請參閱[服務匯流排傳訊例外狀況][sb-messaging-exceptions]。

**復原**

1. 重試暫時性失敗。 請參閱[服務匯流排重試指引][sb-retry]。
2. 無法傳遞給任何接收者的訊息會放在「無效信件佇列」。 您可以使用此佇列來查看有哪些訊息無法接收。 無效信件佇列不會自動清除。 訊息會一直保留在此，直到您明確地擷取走。 請參閱[服務匯流排寄不出的信件佇列的概觀][sb-dead-letter-queue]。

### <a name="writing-a-message-to-a-service-bus-queue-fails"></a>將訊息寫入服務匯流排佇列時失敗。
**偵測**。 從用戶端 SDK 捕捉例外狀況。 服務匯流排例外狀況的基底類別是 [MessagingException][sb-messagingexception-class]。 如果是暫時性錯誤，`IsTransient` 屬性會是 true。

如需詳細資訊，請參閱[服務匯流排傳訊例外狀況][sb-messaging-exceptions]。

**復原**

1. 服務匯流排用戶端會在發生暫時性錯誤後自動重試。 根據預設，它會使用指數輪詢。 在達到重試次數上限或最大逾時期限後，用戶端會擲回例外狀況。 如需詳細資訊，請參閱[服務匯流排重試指引][sb-retry]。
2. 如果超過佇列配額，用戶端就會擲回 [QuotaExceededException][QuotaExceededException]。 例外狀況訊息會提供更多詳細資料。 重試前請先清空佇列中的某些訊息，並考慮使用斷路器模式，以免在超出配額時不斷重試。 此外，請確定 [BrokeredMessage.TimeToLive] 屬性未設得太高。
3. 區域的復原能力可使用[分割的佇列或主題][sb-partition]來加以改善。 非分割的佇列或主題會指派給一個訊息存放區。 如果此訊息存放區無法使用，該佇列或主題上的所有作業都會失敗。 分割的佇列或主題會分割到多個訊息存放區。
4. 若要提升復原能力，請在不同區域建立兩個服務匯流排命名空間，並複寫訊息。 使用作用中複寫或被動複寫。

   * 作用中複寫：用戶端會將每個訊息傳送給這兩個佇列。 收件者會接聽這兩個佇列。 使用唯一識別碼標記訊息，讓用戶端可以捨棄重複的訊息。
   * 被動複寫：用戶端會將訊息傳送給一個佇列。 如果發生錯誤，用戶端會退為傳送給另一個佇列。 收件者會接聽這兩個佇列。 這種方式可減少所傳送的重複訊息數目。 不過，接收者仍必須處理重複的訊息。

     如需詳細資訊，請參閱 [GeoReplication sample][sb-georeplication-sample] 和[將應用程式與服務匯流排中斷和災難隔絕的最佳做法 (英文)](/azure/service-bus-messaging/service-bus-outages-disasters/)。

### <a name="duplicate-message"></a>重複的訊息。
**偵測**。 檢查訊息的 `MessageId` 和 `DeliveryCount` 屬性。

**復原**

* 可能的話，請將訊息處理作業設計為等冪形式。 否則，請儲存已經處理之訊息的訊息識別碼，然後於檢查識別碼後再處理訊息。
* 藉由建立佇列並將 `RequiresDuplicateDetection` 設為 true，以啟用重複偵測。 使用此設定時，只要訊息在傳送時所使用的 `MessageId` 和先前的訊息相同，服務匯流排就會自動刪除該訊息。  請注意：

  * 此設定可防止佇列中放入重複的訊息。 它不會防止接收者多次處理相同的訊息。
  * 重複偵測有時間範圍。 如果重複項目不是在此時間範圍內傳送，系統就不會偵測到。

**診斷**。 記錄重複的訊息。

### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>應用程式無法處理來自該佇列的特定訊息。
**偵測**。 應用程式所特有。 例如，訊息包含無效資料，或是商務邏輯因故失敗。

**復原**

可考慮使用的失敗模式有兩個。

* 接收者偵測失敗。 在此案例中，將訊息移至無效信件佇列。 之後，執行其他處理序以檢查無效信件佇列中的訊息。
* 接收者在訊息處理過程中失敗，例如，由於發生未處理的例外狀況。 若要處理這種情況，請使用 `PeekLock` 模式。 在此模式中，如果鎖定到期，訊息會恢復可供其他接收者接收。 如果訊息超出最大傳遞計數或存留時間，訊息會自動移至無效信件佇列。

如需詳細資訊，請參閱[服務匯流排寄不出的信件佇列的概觀][sb-dead-letter-queue]。

**診斷**。 每當應用程式將訊息移至無效信件佇列，就會在應用程式記錄寫入事件。

## <a name="service-fabric"></a>Service Fabric
### <a name="a-request-to-a-service-fails"></a>要求使用某項服務時失敗。
**偵測**。 服務會傳回錯誤。

**復原**

* 再次尋找 Proxy (`ServiceProxy` 或 `ActorProxy`)，然後再次呼叫服務/動作項目方法。
* **具狀態服務**。 將可靠集合上的作業包裝在交易中。 如果發生錯誤，便會復原交易。 從佇列提取出的要求會再次進行處理。
* **無狀態服務**。 如果服務將資料保存到外部存放區，所有作業都必須具有等冪性。

**診斷**。 應用程式記錄

### <a name="service-fabric-node-is-shut-down"></a>Service Fabric 節點關閉。
**偵測**。 系統將取消權杖傳遞給服務的 `RunAsync` 方法。 Service Fabric 在關閉節點前取消工作。

**復原**。 您可以使用取消權杖來偵測關閉情形。 當 Service Fabric 要求取消時，請盡快完成任何工作並結束  `RunAsync`。

**診斷**。 應用程式記錄檔

## <a name="storage"></a>儲存體
### <a name="writing-data-to-azure-storage-fails"></a>將資料寫入 Azure 儲存體時失敗
**偵測**。 用戶端會在寫入時收到錯誤。

**復原**

1. 重試作業以從暫時性失敗中復原。 用戶端 SDK 中的[重試原則][Storage.RetryPolicies]會自動處理此作業。
2. 實作斷路器模式，以避免過量儲存。
3. 如果嘗試 N 次重試都失敗，請執行正常遞補。 例如：

   * 將資料儲存在本機快取，並於之後服務恢復可用時將寫入轉送至儲存體。
   * 如果寫入動作位於交易式範圍中，則補償交易。

**診斷**。 使用[儲存體度量][storage-metrics]。

### <a name="reading-data-from-azure-storage-fails"></a>從 Azure 儲存體讀取資料時失敗。
**偵測**。 用戶端會在讀取時收到錯誤。

**復原**

1. 重試作業以從暫時性失敗中復原。 用戶端 SDK 中的[重試原則][Storage.RetryPolicies]會自動處理此作業。
2. 對於 RA-GRS 儲存體，如果讀取主要端點時失敗，請試著讀取次要端點。 用戶端 SDK 會自動處理此作業。 請參閱 [Azure 儲存體複寫][storage-replication]。
3. 如果嘗試 N 次重試都失敗，請採取遞補動作以正常地降級。 例如，如果無法從儲存體擷取產品映像，則顯示一般的預留位置影像。

**診斷**。 使用[儲存體度量][storage-metrics]。

## <a name="virtual-machine"></a>虛擬機器
### <a name="connection-to-a-backend-vm-fails"></a>連線至後端 VM 時失敗。
**偵測**。 網路連線錯誤。

**復原**

* 於負載平衡器後方，在可用性設定組內部署至少兩個後端 VM。
* 如果是暫時性的連線錯誤，有時 TCP 會成功地重試傳送訊息。
* 在應用程式中實作重試原則。
* 若為持續性錯誤或非暫時性的錯誤，請實作[斷路器][circuit-breaker]模式。
* 如果呼叫端 VM 超出其網路輸出限制，則輸出佇列會滿溢。 如果輸出佇列一直滿溢，請考慮相應放大。

**診斷**。 記錄服務界限上的事件。

### <a name="vm-instance-becomes-unavailable-or-unhealthy"></a>VM 執行個體變得無法使用或狀況不良。
**偵測**。 設定負載平衡器[健康情況探查][lb-probe]，以指出 VM 執行個體是否狀況良好。 探查應該檢查重大功能是否會正確回應。

**復原**。 對每個應用程式層，在相同的可用性設定組內放入多個 VM 執行個體，並在 VM 前放置負載平衡器。 如果健康情況探查失敗，負載平衡器會停止傳送新的連線至狀況不良的執行個體。

**診斷**。 - 使用負載平衡器[記錄分析][lb-monitor]。

* 設定您的監視系統來監視所有的健康情況監視端點。

### <a name="operator-accidentally-shuts-down-a-vm"></a>操作員不小心關閉 VM。
**偵測**。 N/A

**復原**。 使用 `ReadOnly` 層級設定資源鎖定。 請參閱[使用 Azure Resource Manager 鎖定資源][rm-locks]。

**診斷**。 使用 [Azure 活動記錄][azure-activity-logs]。

## <a name="webjobs"></a>WebJobs
### <a name="continuous-job-stops-running-when-the-scm-host-is-idle"></a>SCM 主機閒置時，連續作業停止執行。
**偵測**。 將取消權杖傳遞給 WebJob 函式。 如需詳細資訊，請參閱[正常關機][web-jobs-shutdown]。

**復原**。 在 Web 應用程式中啟用 `Always On` 設定。 如需詳細資訊，請參閱[使用 WebJobs 執行背景工作][web-jobs]。

## <a name="application-design"></a>應用程式設計
### <a name="application-cant-handle-a-spike-in-incoming-requests"></a>應用程式無法處理內送要求突然增加。
**偵測**。 視應用程式而定。 一般徵兆：

* 網站開始傳回 HTTP 5xx 錯誤碼。
* 相依服務 (例如資料庫或儲存體) 開始對要求進行節流。 根據服務尋找 HTTP 錯誤，例如 HTTP 429 (太多要求)。
* HTTP 佇列長度增加。

**復原**

* 相應放大以處理增加的負載。
* 降低失敗風險，以免連鎖性失敗中斷整個應用程式。 風險降低策略包括：

  * 實作[節流模式][throttling-pattern]以免壓垮後端系統。
  * 使用[佇列型負載調節][queue-based-load-leveling]為要求提供緩衝，並以適當步調處理這些要求。
  * 設定特定用戶端的優先順序。 例如，如果應用程式有免費層與付費層，對免費層的客戶節流，但不要對付費客戶節流。 請參閱[優先順序佇列模式][priority-queue-pattern]。

**診斷**。 使用 [App Service 診斷記錄][app-service-logging]。 使用 [Azure Log Analytics][azure-log-analytics]、[Application Insights][app-insights] 或 [New Relic][new-relic] 等服務，以協助了解診斷記錄。

### <a name="one-of-the-operations-in-a-workflow-or-distributed-transaction-fails"></a>工作流程或分散式交易中的其中一個作業失敗。
**偵測**。 嘗試 N 次重試之後，作業仍失敗。

**復原**

* 作為風險降低計畫，請實作[排程器代理程式監督員][scheduler-agent-supervisor]模式以管理整個工作流程。
* 請不要在逾時的時候重試。 此錯誤的成功率很低。
* 將工作排入佇列，以便稍後重試。

**診斷**。 記錄所有作業 (成功與失敗)，包括補償動作。 使用相互關聯識別碼，以便追蹤相同交易內的所有作業。

### <a name="a-call-to-a-remote-service-fails"></a>遠端服務的呼叫失敗。
**偵測**。 HTTP 錯誤碼。

**復原**

1. 重試暫時性失敗。
2. 如果呼叫在嘗試 N 次之後失敗，請採取遞補動作。 (應用程式所特有)。
3. 實作[斷路器模式][circuit-breaker]，以免發生連鎖性失敗。

**診斷**。 記錄所有遠端呼叫的失敗。

## <a name="next-steps"></a>後續步驟
如需 FMA 程序的詳細資訊，請參閱[雲端服務原本就有的復原功能設計][resilience-by-design-pdf] (PDF 下載)。

<!-- links -->

[api-management]: https://azure.microsoft.com/documentation/services/api-management/
[api-management-throttling]: /azure/api-management/api-management-sample-flexible-throttling/
[app-insights]: /azure/application-insights/app-insights-overview/
[app-insights-web-apps]: /azure/application-insights/app-insights-azure-web-apps/
[app-service-configure]: /azure/app-service-web/web-sites-configure/
[app-service-logging]: /azure/app-service-web/web-sites-enable-diagnostic-log/
[app-service-slots]: /azure/app-service-web/web-sites-staged-publishing/
[auto-rest-client-retry]: https://github.com/Azure/autorest/tree/master/docs
[azure-activity-logs]: /azure/monitoring-and-diagnostics/monitoring-overview-activity-logs/
[azure-alerts]: /azure/monitoring-and-diagnostics/insights-alerts-portal/
[azure-log-analytics]: /azure/log-analytics/log-analytics-overview/
[BrokeredMessage.TimeToLive]: https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
[cassandra-error-handling]: http://www.datastax.com/dev/blog/cassandra-error-handling-done-right
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[docdb-multi-region]: /azure/documentdb/documentdb-developing-with-multiple-regions/
[elasticsearch-azure]: ../elasticsearch/index.md
[elasticsearch-client]: https://www.elastic.co/guide/en/elasticsearch/client/index.html
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[onstop-events]: https://azure.microsoft.com/blog/the-right-way-to-handle-azure-onstop-events/
[lb-monitor]: /azure/load-balancer/load-balancer-monitor-log/
[lb-probe]: /azure/load-balancer/load-balancer-custom-probe-overview/#learn-about-the-types-of-probes
[new-relic]: https://newrelic.com/
[priority-queue-pattern]: https://msdn.microsoft.com/library/dn589794.aspx
[queue-based-load-leveling]: https://msdn.microsoft.com/library/dn589783.aspx
[QuotaExceededException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx
[ra-web-apps-basic]: ../reference-architectures/app-service-web-app/basic-web-app.md
[redis-monitor]: /azure/redis-cache/cache-how-to-monitor/
[redis-retry]: ../best-practices/retry-service-specific.md#azure-redis-cache-retry-guidelines
[resilience-by-design-pdf]: http://download.microsoft.com/download/D/8/C/D8C599A4-4E8A-49BF-80EE-FE35F49B914D/Resilience_by_Design_for_Cloud_Services_White_Paper.pdf
[RoleEntryPoint.OnStop]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[RoleEnvironment.Stopping]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx
[rm-locks]: /azure/azure-resource-manager/resource-group-lock-resources/
[sb-dead-letter-queue]: /azure/service-bus-messaging/service-bus-dead-letter-queues/
[sb-georeplication-sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/GeoReplication
[sb-messagingexception-class]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
[sb-messaging-exceptions]: /azure/service-bus-messaging/service-bus-messaging-exceptions/
[sb-outages]: /azure/service-bus-messaging/service-bus-outages-disasters/#protecting-queues-and-topics-against-datacenter-outages-or-disasters
[sb-partition]: /azure/service-bus-messaging/service-bus-partitioning/
[sb-poison-message]: /azure/app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to/#poison
[sb-retry]: ../best-practices/retry-service-specific.md#service-bus-retry-guidelines
[search-sdk]: https://msdn.microsoft.com/library/dn951165.aspx
[scheduler-agent-supervisor]: https://msdn.microsoft.com/library/dn589780.aspx
[search-analytics]: /azure/search/search-traffic-analytics/
[sql-db-audit]: /azure/sql-database/sql-database-auditing-get-started/
[sql-db-errors]: /azure/sql-database/sql-database-develop-error-messages/#resource-governance-errors
[sql-db-failover]: /azure/sql-database/sql-database-geo-replication-failover-portal/
[sql-db-limits]: /azure/sql-database/sql-database-resource-limits/
[sql-db-replication]: /azure/sql-database/sql-database-geo-replication-overview/
[storage-metrics]: https://msdn.microsoft.com/library/dn782843.aspx
[storage-replication]: /azure/storage/storage-redundancy/
[Storage.RetryPolicies]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.aspx
[sys.event_log]: https://msdn.microsoft.com/library/dn270018.aspx
[throttling-pattern]: https://msdn.microsoft.com/library/dn589798.aspx
[web-jobs]: /azure/app-service-web/web-sites-create-web-jobs/
[web-jobs-shutdown]: /azure/app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to/#graceful

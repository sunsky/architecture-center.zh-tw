---
title: "重試服務的特定指引"
description: "用於設定重試機制的服務特定指引。"
author: dragon119
ms.date: 07/13/2016
pnp.series.title: Best Practices
ms.openlocfilehash: 6aba60dc3a60e96e59e2034d4a1e380e0f1c996a
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="retry-guidance-for-specific-services"></a><span data-ttu-id="00d33-103">特定服務的重試指引</span><span class="sxs-lookup"><span data-stu-id="00d33-103">Retry guidance for specific services</span></span>

<span data-ttu-id="00d33-104">大多數的 Azure 服務與用戶端 SDK 皆包含重試機制，</span><span class="sxs-lookup"><span data-stu-id="00d33-104">Most Azure services and client SDKs include a retry mechanism.</span></span> <span data-ttu-id="00d33-105">但各有不同，因為每個服務有不同的特性與需求，也因此系統會根據特定的服務調整每個重試機制。</span><span class="sxs-lookup"><span data-stu-id="00d33-105">However, these differ because each service has different characteristics and requirements, and so each retry mechanism is tuned to a specific service.</span></span> <span data-ttu-id="00d33-106">本指南摘要說明大多數 Azure 服務的重試機制功能，並提供一些資訊幫助您使用、調整，或擴充該服務的重試機制。</span><span class="sxs-lookup"><span data-stu-id="00d33-106">This guide summarizes the retry mechanism features for the majority of Azure services, and includes information to help you use, adapt, or extend the retry mechanism for that service.</span></span>

<span data-ttu-id="00d33-107">如需處理暫時性錯誤，及對服務與資源重試連接與作業的一般指引，請參閱 [重試指引](./transient-faults.md)。</span><span class="sxs-lookup"><span data-stu-id="00d33-107">For general guidance on handling transient faults, and retrying connections and operations against services and resources, see [Retry guidance](./transient-faults.md).</span></span>

<span data-ttu-id="00d33-108">下表摘要說明此指引中所述 Azure 服務的重試功能。</span><span class="sxs-lookup"><span data-stu-id="00d33-108">The following table summarizes the retry features for the Azure services described in this guidance.</span></span>

| <span data-ttu-id="00d33-109">**服務**</span><span class="sxs-lookup"><span data-stu-id="00d33-109">**Service**</span></span> | <span data-ttu-id="00d33-110">**重試功能**</span><span class="sxs-lookup"><span data-stu-id="00d33-110">**Retry capabilities**</span></span> | <span data-ttu-id="00d33-111">**原則組態**</span><span class="sxs-lookup"><span data-stu-id="00d33-111">**Policy configuration**</span></span> | <span data-ttu-id="00d33-112">**範圍**</span><span class="sxs-lookup"><span data-stu-id="00d33-112">**Scope**</span></span> | <span data-ttu-id="00d33-113">**遙測功能**</span><span class="sxs-lookup"><span data-stu-id="00d33-113">**Telemetry features**</span></span> |
| --- | --- | --- | --- | --- |
| <span data-ttu-id="00d33-114">**[Azure 儲存體](#azure-storage-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="00d33-114">**[Azure Storage](#azure-storage-retry-guidelines)**</span></span> |<span data-ttu-id="00d33-115">用戶端原生</span><span class="sxs-lookup"><span data-stu-id="00d33-115">Native in client</span></span> |<span data-ttu-id="00d33-116">程式設計</span><span class="sxs-lookup"><span data-stu-id="00d33-116">Programmatic</span></span> |<span data-ttu-id="00d33-117">用戶端與個別作業</span><span class="sxs-lookup"><span data-stu-id="00d33-117">Client and individual operations</span></span> |<span data-ttu-id="00d33-118">TraceSource</span><span class="sxs-lookup"><span data-stu-id="00d33-118">TraceSource</span></span> |
| <span data-ttu-id="00d33-119">**[使用 Entity Framework 的 SQL Database](#sql-database-using-entity-framework-6-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="00d33-119">**[SQL Database with Entity Framework](#sql-database-using-entity-framework-6-retry-guidelines)**</span></span> |<span data-ttu-id="00d33-120">用戶端原生</span><span class="sxs-lookup"><span data-stu-id="00d33-120">Native in client</span></span> |<span data-ttu-id="00d33-121">程式設計</span><span class="sxs-lookup"><span data-stu-id="00d33-121">Programmatic</span></span> |<span data-ttu-id="00d33-122">每個 AppDomain 全域</span><span class="sxs-lookup"><span data-stu-id="00d33-122">Global per AppDomain</span></span> |<span data-ttu-id="00d33-123">None</span><span class="sxs-lookup"><span data-stu-id="00d33-123">None</span></span> |
| <span data-ttu-id="00d33-124">**[使用 Entity Framework Core 的 SQL Database](#sql-database-using-entity-framework-core-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="00d33-124">**[SQL Database with Entity Framework Core](#sql-database-using-entity-framework-core-retry-guidelines)**</span></span> |<span data-ttu-id="00d33-125">用戶端原生</span><span class="sxs-lookup"><span data-stu-id="00d33-125">Native in client</span></span> |<span data-ttu-id="00d33-126">程式設計</span><span class="sxs-lookup"><span data-stu-id="00d33-126">Programmatic</span></span> |<span data-ttu-id="00d33-127">每個 AppDomain 全域</span><span class="sxs-lookup"><span data-stu-id="00d33-127">Global per AppDomain</span></span> |<span data-ttu-id="00d33-128">None</span><span class="sxs-lookup"><span data-stu-id="00d33-128">None</span></span> |
| <span data-ttu-id="00d33-129">**[使用 ADO.NET 的 SQL Database](#sql-database-using-adonet-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="00d33-129">**[SQL Database with ADO.NET](#sql-database-using-adonet-retry-guidelines)**</span></span> |[<span data-ttu-id="00d33-130">Polly</span><span class="sxs-lookup"><span data-stu-id="00d33-130">Polly</span></span>](#transient-fault-handling-with-polly) |<span data-ttu-id="00d33-131">宣告與程式設計</span><span class="sxs-lookup"><span data-stu-id="00d33-131">Declarative and programmatic</span></span> |<span data-ttu-id="00d33-132">單一陳述式或程式碼區塊</span><span class="sxs-lookup"><span data-stu-id="00d33-132">Single statements or blocks of code</span></span> |<span data-ttu-id="00d33-133">自訂</span><span class="sxs-lookup"><span data-stu-id="00d33-133">Custom</span></span> |
| <span data-ttu-id="00d33-134">**[服務匯流排](#service-bus-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="00d33-134">**[Service Bus](#service-bus-retry-guidelines)**</span></span> |<span data-ttu-id="00d33-135">用戶端原生</span><span class="sxs-lookup"><span data-stu-id="00d33-135">Native in client</span></span> |<span data-ttu-id="00d33-136">程式設計</span><span class="sxs-lookup"><span data-stu-id="00d33-136">Programmatic</span></span> |<span data-ttu-id="00d33-137">命名空間管理員、傳訊處理站及用戶端</span><span class="sxs-lookup"><span data-stu-id="00d33-137">Namespace Manager, Messaging Factory, and Client</span></span> |<span data-ttu-id="00d33-138">ETW</span><span class="sxs-lookup"><span data-stu-id="00d33-138">ETW</span></span> |
| <span data-ttu-id="00d33-139">**[Azure Redis 快取](#azure-redis-cache-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="00d33-139">**[Azure Redis Cache](#azure-redis-cache-retry-guidelines)**</span></span> |<span data-ttu-id="00d33-140">用戶端原生</span><span class="sxs-lookup"><span data-stu-id="00d33-140">Native in client</span></span> |<span data-ttu-id="00d33-141">程式設計</span><span class="sxs-lookup"><span data-stu-id="00d33-141">Programmatic</span></span> |<span data-ttu-id="00d33-142">用戶端</span><span class="sxs-lookup"><span data-stu-id="00d33-142">Client</span></span> |<span data-ttu-id="00d33-143">TextWriter</span><span class="sxs-lookup"><span data-stu-id="00d33-143">TextWriter</span></span> |
| <span data-ttu-id="00d33-144">**[DocumentDB API](#documentdb-api-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="00d33-144">**[DocumentDB API](#documentdb-api-retry-guidelines)**</span></span> |<span data-ttu-id="00d33-145">服務原生</span><span class="sxs-lookup"><span data-stu-id="00d33-145">Native in service</span></span> |<span data-ttu-id="00d33-146">不可設定</span><span class="sxs-lookup"><span data-stu-id="00d33-146">Non-configurable</span></span> |<span data-ttu-id="00d33-147">全域</span><span class="sxs-lookup"><span data-stu-id="00d33-147">Global</span></span> |<span data-ttu-id="00d33-148">TraceSource</span><span class="sxs-lookup"><span data-stu-id="00d33-148">TraceSource</span></span> |
| <span data-ttu-id="00d33-149">**[Azure 搜尋服務](#azure-storage-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="00d33-149">**[Azure Search](#azure-storage-retry-guidelines)**</span></span> |<span data-ttu-id="00d33-150">用戶端原生</span><span class="sxs-lookup"><span data-stu-id="00d33-150">Native in client</span></span> |<span data-ttu-id="00d33-151">程式設計</span><span class="sxs-lookup"><span data-stu-id="00d33-151">Programmatic</span></span> |<span data-ttu-id="00d33-152">用戶端</span><span class="sxs-lookup"><span data-stu-id="00d33-152">Client</span></span> |<span data-ttu-id="00d33-153">ETW 或自訂</span><span class="sxs-lookup"><span data-stu-id="00d33-153">ETW or Custom</span></span> |
| <span data-ttu-id="00d33-154">**[Azure Active Directory](#azure-active-directory-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="00d33-154">**[Azure Active Directory](#azure-active-directory-retry-guidelines)**</span></span> |<span data-ttu-id="00d33-155">ADAL 程式庫原生</span><span class="sxs-lookup"><span data-stu-id="00d33-155">Native in ADAL library</span></span> |<span data-ttu-id="00d33-156">內嵌至 ADAL 程式庫</span><span class="sxs-lookup"><span data-stu-id="00d33-156">Embeded into ADAL library</span></span> |<span data-ttu-id="00d33-157">內部</span><span class="sxs-lookup"><span data-stu-id="00d33-157">Internal</span></span> |<span data-ttu-id="00d33-158">None</span><span class="sxs-lookup"><span data-stu-id="00d33-158">None</span></span> |
| <span data-ttu-id="00d33-159">**[Service Fabric](#service-fabric-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="00d33-159">**[Service Fabric](#service-fabric-retry-guidelines)**</span></span> |<span data-ttu-id="00d33-160">用戶端原生</span><span class="sxs-lookup"><span data-stu-id="00d33-160">Native in client</span></span> |<span data-ttu-id="00d33-161">程式設計</span><span class="sxs-lookup"><span data-stu-id="00d33-161">Programmatic</span></span> |<span data-ttu-id="00d33-162">用戶端</span><span class="sxs-lookup"><span data-stu-id="00d33-162">Client</span></span> |<span data-ttu-id="00d33-163">None</span><span class="sxs-lookup"><span data-stu-id="00d33-163">None</span></span> | 
| <span data-ttu-id="00d33-164">**[Azure 事件中樞](#azure-event-hubs-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="00d33-164">**[Azure Event Hubs](#azure-event-hubs-retry-guidelines)**</span></span> |<span data-ttu-id="00d33-165">用戶端原生</span><span class="sxs-lookup"><span data-stu-id="00d33-165">Native in client</span></span> |<span data-ttu-id="00d33-166">程式設計</span><span class="sxs-lookup"><span data-stu-id="00d33-166">Programmatic</span></span> |<span data-ttu-id="00d33-167">用戶端</span><span class="sxs-lookup"><span data-stu-id="00d33-167">Client</span></span> |<span data-ttu-id="00d33-168">None</span><span class="sxs-lookup"><span data-stu-id="00d33-168">None</span></span> |

> [!NOTE]
> <span data-ttu-id="00d33-169">對於大多數的 Azure 內建重試機制而言，目前還無法為重試原則中所包含功能之外的不同類型錯誤或例外狀況套用不同的重試原則。</span><span class="sxs-lookup"><span data-stu-id="00d33-169">For most of the Azure built-in retry mechanisms, there is currently no way apply a different retry policy for different types of error or exception beyond the functionality include in the retry policy.</span></span> <span data-ttu-id="00d33-170">因此在撰寫本文時的最佳指引是，設定一個可提供最佳平均效能和可用性的原則。</span><span class="sxs-lookup"><span data-stu-id="00d33-170">Therefore, the best guidance available at the time of writing is to configure a policy that provides the optimum average performance and availability.</span></span> <span data-ttu-id="00d33-171">微調原則的一種方法，就是分析記錄檔來判斷正在發生的暫時性錯誤類型。</span><span class="sxs-lookup"><span data-stu-id="00d33-171">One way to fine-tune the policy is to analyze log files to determine the type of transient faults that are occurring.</span></span> <span data-ttu-id="00d33-172">例如，如果大部分的錯誤與網路連線問題有關，您可能會嘗試立即重試，而非等待一段時間後才第一次重試。</span><span class="sxs-lookup"><span data-stu-id="00d33-172">For example, if the majority of errors are related to network connectivity issues, you might attempt an immediate retry rather than wait a long time for the first retry.</span></span>
>
>

## <a name="azure-storage-retry-guidelines"></a><span data-ttu-id="00d33-173">Azure 儲存體重試指引</span><span class="sxs-lookup"><span data-stu-id="00d33-173">Azure Storage retry guidelines</span></span>
<span data-ttu-id="00d33-174">Azure 儲存體服務包括資料表、Blob 儲存體、檔案，以及儲存體佇列。</span><span class="sxs-lookup"><span data-stu-id="00d33-174">Azure storage services include table and blob storage, files, and storage queues.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="00d33-175">重試機制</span><span class="sxs-lookup"><span data-stu-id="00d33-175">Retry mechanism</span></span>
<span data-ttu-id="00d33-176">重試發生在個別的 REST 作業層級，是用戶端 API 實作不可或缺的一部分。</span><span class="sxs-lookup"><span data-stu-id="00d33-176">Retries occur at the individual REST operation level and are an integral part of the client API implementation.</span></span> <span data-ttu-id="00d33-177">用戶端儲存體 SDK 使用實作 [IExtendedRetryPolicy 介面](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.aspx)的類別。</span><span class="sxs-lookup"><span data-stu-id="00d33-177">The client storage SDK uses classes that implement the [IExtendedRetryPolicy Interface](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.aspx).</span></span>

<span data-ttu-id="00d33-178">有不同的實作的介面。</span><span class="sxs-lookup"><span data-stu-id="00d33-178">There are different implementations of the interface.</span></span> <span data-ttu-id="00d33-179">儲存體用戶端會選擇特別針對存取資料表、Blob 與佇列設計的原則。</span><span class="sxs-lookup"><span data-stu-id="00d33-179">Storage clients can choose from policies specifically designed for accessing tables, blobs, and queues.</span></span> <span data-ttu-id="00d33-180">每個實作會使用不同的重試策略，這些策略基本上定義重試間隔與其他詳細資料。</span><span class="sxs-lookup"><span data-stu-id="00d33-180">Each implementation uses a different retry strategy that essentially defines the retry interval and other details.</span></span>

<span data-ttu-id="00d33-181">內建類別支援線性 (固定延遲) 重試間隔和隨機指數重試間隔。</span><span class="sxs-lookup"><span data-stu-id="00d33-181">The built-in classes provide support for linear (constant delay) and exponential with randomization retry intervals.</span></span> <span data-ttu-id="00d33-182">當另一個處理序在更高的層級處理重試時，沒有重試原則可使用。</span><span class="sxs-lookup"><span data-stu-id="00d33-182">There is also a no retry policy for use when another process is handling retries at a higher level.</span></span> <span data-ttu-id="00d33-183">但如果內建類別未提供您的特定需求，您可以實作您自己的重試類別。</span><span class="sxs-lookup"><span data-stu-id="00d33-183">However, you can implement your own retry classes if you have specific requirements not provided by the built-in classes.</span></span>

<span data-ttu-id="00d33-184">如果您正在使用讀取權限異地備援儲存體服務位置 (RA-GRS)，替代重試會在主要與次要儲存體服務位置之間切換，要求的結果是可重試的錯誤。</span><span class="sxs-lookup"><span data-stu-id="00d33-184">Alternate retries switch between primary and secondary storage service location if you are using read access geo-redundant storage (RA-GRS) and the result of the request is a retryable error.</span></span> <span data-ttu-id="00d33-185">如需詳細資訊，請參閱 [Azure 儲存體備援選項](http://msdn.microsoft.com/library/azure/dn727290.aspx) 。</span><span class="sxs-lookup"><span data-stu-id="00d33-185">See [Azure Storage Redundancy Options](http://msdn.microsoft.com/library/azure/dn727290.aspx) for more information.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="00d33-186">原則組態</span><span class="sxs-lookup"><span data-stu-id="00d33-186">Policy configuration</span></span>
<span data-ttu-id="00d33-187">以程式設計方式設定重試原則。</span><span class="sxs-lookup"><span data-stu-id="00d33-187">Retry policies are configured programmatically.</span></span> <span data-ttu-id="00d33-188">一般程序是建立和填入 **TableRequestOptions**、**BlobRequestOptions**、**FileRequestOptions** 或 **QueueRequestOptions** 執行個體。</span><span class="sxs-lookup"><span data-stu-id="00d33-188">A typical procedure is to create and populate a **TableRequestOptions**, **BlobRequestOptions**, **FileRequestOptions**, or **QueueRequestOptions** instance.</span></span>

```csharp
TableRequestOptions interactiveRequestOption = new TableRequestOptions()
{
  RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
  // For Read-access geo-redundant storage, use PrimaryThenSecondary.
  // Otherwise set this to PrimaryOnly.
  LocationMode = LocationMode.PrimaryThenSecondary,
  // Maximum execution time based on the business use case. Maximum value up to 10 seconds.
  MaximumExecutionTime = TimeSpan.FromSeconds(2)
};
```

<span data-ttu-id="00d33-189">接著可以在用戶端上設定要求選項執行個體，涉及用戶端的所有作業都將使用指定的要求選項。</span><span class="sxs-lookup"><span data-stu-id="00d33-189">The request options instance can then be set on the client, and all operations with the client will use the specified request options.</span></span>

```csharp
client.DefaultRequestOptions = interactiveRequestOption;
var stats = await client.GetServiceStatsAsync();
```

<span data-ttu-id="00d33-190">您可以將填入的要求選項類別執行個體做為參數傳遞至作業方法，以覆寫用戶端要求選項。</span><span class="sxs-lookup"><span data-stu-id="00d33-190">You can override the client request options by passing a populated instance of the request options class as a parameter to operation methods.</span></span>

```csharp
var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
```

<span data-ttu-id="00d33-191">使用 **OperationContext** 執行個體指定當發生重試時與作業完成時，要執行的程式碼。</span><span class="sxs-lookup"><span data-stu-id="00d33-191">You use an **OperationContext** instance to specify the code to execute when a retry occurs and when an operation has completed.</span></span> <span data-ttu-id="00d33-192">此程式碼可以收集用於記錄與遙測中的作業相關資訊。</span><span class="sxs-lookup"><span data-stu-id="00d33-192">This code can collect information about the operation for use in logs and telemetry.</span></span>

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

<span data-ttu-id="00d33-193">除了指出錯誤是否適合重試外，擴充的重試原則會傳回 **RetryContext** 以表示重試次數、最後一個要求的結果，以及下一個重試會在主要或次要位置發生 (請參閱下表以取得詳細資料)。</span><span class="sxs-lookup"><span data-stu-id="00d33-193">In addition to indicating whether a failure is suitable for retry, the extended retry policies return a **RetryContext** object that indicates the number of retries, the results of the last request, whether the next retry will happen in the primary or secondary location (see table below for details).</span></span> <span data-ttu-id="00d33-194">**RetryContext** 物年的屬性可用於判定是否與何時嘗試重試。</span><span class="sxs-lookup"><span data-stu-id="00d33-194">The properties of the **RetryContext** object can be used to decide if and when to attempt a retry.</span></span> <span data-ttu-id="00d33-195">如需詳細資料，請參閱 [IExtendedRetryPolicy.Evaluate Method](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx)。</span><span class="sxs-lookup"><span data-stu-id="00d33-195">For more details, see [IExtendedRetryPolicy.Evaluate Method](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx).</span></span>

<span data-ttu-id="00d33-196">下表顯示內建重試原則的的預設設定。</span><span class="sxs-lookup"><span data-stu-id="00d33-196">The following table shows the default settings for the built-in retry policies.</span></span>

| <span data-ttu-id="00d33-197">**內容**</span><span class="sxs-lookup"><span data-stu-id="00d33-197">**Context**</span></span> | <span data-ttu-id="00d33-198">**設定**</span><span class="sxs-lookup"><span data-stu-id="00d33-198">**Setting**</span></span> | <span data-ttu-id="00d33-199">**預設值**</span><span class="sxs-lookup"><span data-stu-id="00d33-199">**Default value**</span></span> | <span data-ttu-id="00d33-200">**意義**</span><span class="sxs-lookup"><span data-stu-id="00d33-200">**Meaning**</span></span> |
| --- | --- | --- | --- |
| <span data-ttu-id="00d33-201">資料表 / Blob / 檔案</span><span class="sxs-lookup"><span data-stu-id="00d33-201">Table / Blob / File</span></span><br /><span data-ttu-id="00d33-202">QueueRequestOptions</span><span class="sxs-lookup"><span data-stu-id="00d33-202">QueueRequestOptions</span></span> |<span data-ttu-id="00d33-203">MaximumExecutionTime</span><span class="sxs-lookup"><span data-stu-id="00d33-203">MaximumExecutionTime</span></span><br /><br /><span data-ttu-id="00d33-204">ServerTimeout</span><span class="sxs-lookup"><span data-stu-id="00d33-204">ServerTimeout</span></span><br /><br /><br /><br /><br /><span data-ttu-id="00d33-205">LocationMode</span><span class="sxs-lookup"><span data-stu-id="00d33-205">LocationMode</span></span><br /><br /><br /><br /><br /><br /><br /><span data-ttu-id="00d33-206">RetryPolicy</span><span class="sxs-lookup"><span data-stu-id="00d33-206">RetryPolicy</span></span> |<span data-ttu-id="00d33-207">120 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-207">120 seconds</span></span><br /><br /><span data-ttu-id="00d33-208">無</span><span class="sxs-lookup"><span data-stu-id="00d33-208">None</span></span><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><span data-ttu-id="00d33-209">ExponentialPolicy</span><span class="sxs-lookup"><span data-stu-id="00d33-209">ExponentialPolicy</span></span> |<span data-ttu-id="00d33-210">要求的最大執行時間，包含所有可能的重試嘗試。</span><span class="sxs-lookup"><span data-stu-id="00d33-210">Maximum execution time for the request, including all potential retry attempts.</span></span><br /><span data-ttu-id="00d33-211">要求的伺服器逾時間隔 (值四捨五入至秒)。</span><span class="sxs-lookup"><span data-stu-id="00d33-211">Server timeout interval for the request (value is rounded to seconds).</span></span> <span data-ttu-id="00d33-212">如果未指定，則會對伺服器的所有要求使用預設值。</span><span class="sxs-lookup"><span data-stu-id="00d33-212">If not specified, it will use the default value for all requests to the server.</span></span> <span data-ttu-id="00d33-213">通常，最好的選擇是略過此設定，以便使用伺服器預設值。</span><span class="sxs-lookup"><span data-stu-id="00d33-213">Usually, the best option is to omit this setting so that the server default is used.</span></span><br /><span data-ttu-id="00d33-214">如果使用讀取權限異地備援儲存體 (RA-GRS) 複寫選項建立儲存體帳戶，您可以使用位置模式來指出哪個位置應接收要求。</span><span class="sxs-lookup"><span data-stu-id="00d33-214">If the storage account is created with the Read access geo-redundant storage (RA-GRS) replication option, you can use the location mode to indicate which location should receive the request.</span></span> <span data-ttu-id="00d33-215">例如，如果指定 **PrimaryThenSecondary**，則一律先將要求傳送至主要位置。</span><span class="sxs-lookup"><span data-stu-id="00d33-215">For example, if **PrimaryThenSecondary** is specified, requests are always sent to the primary location first.</span></span> <span data-ttu-id="00d33-216">如果要求失敗，就會傳送到次要位置。</span><span class="sxs-lookup"><span data-stu-id="00d33-216">If a request fails, it is sent to the secondary location.</span></span><br /><span data-ttu-id="00d33-217">如需每個選項的詳細資訊，請參閱下列內容。</span><span class="sxs-lookup"><span data-stu-id="00d33-217">See below for details of each option.</span></span> |
| <span data-ttu-id="00d33-218">指數原則</span><span class="sxs-lookup"><span data-stu-id="00d33-218">Exponential policy</span></span> |<span data-ttu-id="00d33-219">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="00d33-219">maxAttempt</span></span><br /><span data-ttu-id="00d33-220">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="00d33-220">deltaBackoff</span></span><br /><br /><br /><span data-ttu-id="00d33-221">MinBackoff</span><span class="sxs-lookup"><span data-stu-id="00d33-221">MinBackoff</span></span><br /><br /><span data-ttu-id="00d33-222">MaxBackoff</span><span class="sxs-lookup"><span data-stu-id="00d33-222">MaxBackoff</span></span> |<span data-ttu-id="00d33-223">3</span><span class="sxs-lookup"><span data-stu-id="00d33-223">3</span></span><br /><span data-ttu-id="00d33-224">4 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-224">4 seconds</span></span><br /><br /><br /><span data-ttu-id="00d33-225">3 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-225">3 seconds</span></span><br /><br /><span data-ttu-id="00d33-226">120 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-226">120 seconds</span></span> |<span data-ttu-id="00d33-227">重試嘗試的次數。</span><span class="sxs-lookup"><span data-stu-id="00d33-227">Number of retry attempts.</span></span><br /><span data-ttu-id="00d33-228">重試之間的輪詢間隔。</span><span class="sxs-lookup"><span data-stu-id="00d33-228">Back-off interval between retries.</span></span> <span data-ttu-id="00d33-229">此時間範圍的倍數 (包含隨機元素)，將用於後續的重試次數。</span><span class="sxs-lookup"><span data-stu-id="00d33-229">Multiples of this timespan, including a random element, will be used for subsequent retry attempts.</span></span><br /><span data-ttu-id="00d33-230">新增從 deltaBackoff 計算的所有重試間隔。</span><span class="sxs-lookup"><span data-stu-id="00d33-230">Added to all retry intervals computed from deltaBackoff.</span></span> <span data-ttu-id="00d33-231">無法變更此值。</span><span class="sxs-lookup"><span data-stu-id="00d33-231">This value cannot be changed.</span></span><br /><span data-ttu-id="00d33-232">如果計算的重試間隔大於 MaxBackoff，則會使用 MaxBackoff。</span><span class="sxs-lookup"><span data-stu-id="00d33-232">MaxBackoff is used if the computed retry interval is greater than MaxBackoff.</span></span> <span data-ttu-id="00d33-233">無法變更此值。</span><span class="sxs-lookup"><span data-stu-id="00d33-233">This value cannot be changed.</span></span> |
| <span data-ttu-id="00d33-234">線性原則</span><span class="sxs-lookup"><span data-stu-id="00d33-234">Linear policy</span></span> |<span data-ttu-id="00d33-235">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="00d33-235">maxAttempt</span></span><br /><span data-ttu-id="00d33-236">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="00d33-236">deltaBackoff</span></span> |<span data-ttu-id="00d33-237">3</span><span class="sxs-lookup"><span data-stu-id="00d33-237">3</span></span><br /><span data-ttu-id="00d33-238">30 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-238">30 seconds</span></span> |<span data-ttu-id="00d33-239">重試嘗試的次數。</span><span class="sxs-lookup"><span data-stu-id="00d33-239">Number of retry attempts.</span></span><br /><span data-ttu-id="00d33-240">重試之間的輪詢間隔。</span><span class="sxs-lookup"><span data-stu-id="00d33-240">Back-off interval between retries.</span></span> |

### <a name="retry-usage-guidance"></a><span data-ttu-id="00d33-241">重試使用指引</span><span class="sxs-lookup"><span data-stu-id="00d33-241">Retry usage guidance</span></span>
<span data-ttu-id="00d33-242">當使用儲存體用戶端 API 存取 Azure 儲存體服務時，請考量下列指引：</span><span class="sxs-lookup"><span data-stu-id="00d33-242">Consider the following guidelines when accessing Azure storage services using the storage client API:</span></span>

* <span data-ttu-id="00d33-243">使用 Microsoft.WindowsAzure.Storage.RetryPolicies 命名空間的內建重試原則，此命名空間中的原則適合您的需求。</span><span class="sxs-lookup"><span data-stu-id="00d33-243">Use the built-in retry policies from the Microsoft.WindowsAzure.Storage.RetryPolicies namespace where they are appropriate for your requirements.</span></span> <span data-ttu-id="00d33-244">在大多數的狀況中，這些原則已經足夠。</span><span class="sxs-lookup"><span data-stu-id="00d33-244">In most cases, these policies will be sufficient.</span></span>
* <span data-ttu-id="00d33-245">在批次作業、 背景工作或非互動式案例中使用 **ExponentialRetry** 原則。</span><span class="sxs-lookup"><span data-stu-id="00d33-245">Use the **ExponentialRetry** policy in batch operations, background tasks, or non-interactive scenarios.</span></span> <span data-ttu-id="00d33-246">在這些案例中，您通常會允許服務有更多的時間復原，藉此增加作業最後成功的機會。</span><span class="sxs-lookup"><span data-stu-id="00d33-246">In these scenarios, you can typically allow more time for the service to recover—with a consequently increased chance of the operation eventually succeeding.</span></span>
* <span data-ttu-id="00d33-247">請考慮指定 **RequestOptions** 參數的 **MaximumExecutionTime** 屬性，以限制總執行時間，但在選擇逾時值時要考量到作業的類型與大小。</span><span class="sxs-lookup"><span data-stu-id="00d33-247">Consider specifying the **MaximumExecutionTime** property of the **RequestOptions** parameter to limit the total execution time, but take into account the type and size of the operation when choosing a timeout value.</span></span>
* <span data-ttu-id="00d33-248">如果您需要實作自訂重試，請避免建立儲存體用戶端類別周圍的包裝函式。</span><span class="sxs-lookup"><span data-stu-id="00d33-248">If you need to implement a custom retry, avoid creating wrappers around the storage client classes.</span></span> <span data-ttu-id="00d33-249">相反地，使用一些功能透過 **IExtendedRetryPolicy** 介面來擴充現有的原則。</span><span class="sxs-lookup"><span data-stu-id="00d33-249">Instead, use the capabilities to extend the existing policies through the **IExtendedRetryPolicy** interface.</span></span>
* <span data-ttu-id="00d33-250">如果您使用的是讀取權限異地備援儲存體 (RA-GRS)，您可以使用 **LocationMode** 指定如果主要存取失敗，重試嘗試將會存取存放區的次要唯讀複本。</span><span class="sxs-lookup"><span data-stu-id="00d33-250">If you are using read access geo-redundant storage (RA-GRS) you can use the **LocationMode** to specify that retry attempts will access the secondary read-only copy of the store should the primary access fail.</span></span> <span data-ttu-id="00d33-251">不過使用這個選項時，若尚未完成從主要存放區複寫，則必須確定應用程式可以成功使用過時的資料。</span><span class="sxs-lookup"><span data-stu-id="00d33-251">However, when using this option you must ensure that your application can work successfully with data that may be stale if the replication from the primary store has not yet completed.</span></span>

<span data-ttu-id="00d33-252">請考慮從重試作業的下列設定開始。</span><span class="sxs-lookup"><span data-stu-id="00d33-252">Consider starting with following settings for retrying operations.</span></span> <span data-ttu-id="00d33-253">這些是一般用途設定，您應該監視作業並微調其值以符合您自己的需求。</span><span class="sxs-lookup"><span data-stu-id="00d33-253">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>  

| <span data-ttu-id="00d33-254">**內容**</span><span class="sxs-lookup"><span data-stu-id="00d33-254">**Context**</span></span> | <span data-ttu-id="00d33-255">**範例目標 E2E<br />最大延遲**</span><span class="sxs-lookup"><span data-stu-id="00d33-255">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="00d33-256">**重試原則**</span><span class="sxs-lookup"><span data-stu-id="00d33-256">**Retry policy**</span></span> | <span data-ttu-id="00d33-257">**設定**</span><span class="sxs-lookup"><span data-stu-id="00d33-257">**Settings**</span></span> | <span data-ttu-id="00d33-258">**值**</span><span class="sxs-lookup"><span data-stu-id="00d33-258">**Values**</span></span> | <span data-ttu-id="00d33-259">**運作方式**</span><span class="sxs-lookup"><span data-stu-id="00d33-259">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="00d33-260">互動式、UI </span><span class="sxs-lookup"><span data-stu-id="00d33-260">Interactive, UI,</span></span><br /><span data-ttu-id="00d33-261">或前景</span><span class="sxs-lookup"><span data-stu-id="00d33-261">or foreground</span></span> |<span data-ttu-id="00d33-262">2 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-262">2 seconds</span></span> |<span data-ttu-id="00d33-263">線性</span><span class="sxs-lookup"><span data-stu-id="00d33-263">Linear</span></span> |<span data-ttu-id="00d33-264">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="00d33-264">maxAttempt</span></span><br /><span data-ttu-id="00d33-265">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="00d33-265">deltaBackoff</span></span> |<span data-ttu-id="00d33-266">3</span><span class="sxs-lookup"><span data-stu-id="00d33-266">3</span></span><br /><span data-ttu-id="00d33-267">500 毫秒</span><span class="sxs-lookup"><span data-stu-id="00d33-267">500 ms</span></span> |<span data-ttu-id="00d33-268">嘗試 1 - 延遲 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="00d33-268">Attempt 1 - delay 500 ms</span></span><br /><span data-ttu-id="00d33-269">嘗試 2 - 延遲 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="00d33-269">Attempt 2 - delay 500 ms</span></span><br /><span data-ttu-id="00d33-270">嘗試 3 - 延遲 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="00d33-270">Attempt 3 - delay 500 ms</span></span> |
| <span data-ttu-id="00d33-271">背景</span><span class="sxs-lookup"><span data-stu-id="00d33-271">Background</span></span><br /><span data-ttu-id="00d33-272">或批次</span><span class="sxs-lookup"><span data-stu-id="00d33-272">or batch</span></span> |<span data-ttu-id="00d33-273">30 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-273">30 seconds</span></span> |<span data-ttu-id="00d33-274">指數</span><span class="sxs-lookup"><span data-stu-id="00d33-274">Exponential</span></span> |<span data-ttu-id="00d33-275">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="00d33-275">maxAttempt</span></span><br /><span data-ttu-id="00d33-276">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="00d33-276">deltaBackoff</span></span> |<span data-ttu-id="00d33-277">5</span><span class="sxs-lookup"><span data-stu-id="00d33-277">5</span></span><br /><span data-ttu-id="00d33-278">4 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-278">4 seconds</span></span> |<span data-ttu-id="00d33-279">嘗試 1 - 延遲 ~3 毫秒</span><span class="sxs-lookup"><span data-stu-id="00d33-279">Attempt 1 - delay ~3 sec</span></span><br /><span data-ttu-id="00d33-280">嘗試 2 - 延遲 ~7 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-280">Attempt 2 - delay ~7 sec</span></span><br /><span data-ttu-id="00d33-281">嘗試 3 - 延遲 ~15 毫秒</span><span class="sxs-lookup"><span data-stu-id="00d33-281">Attempt 3 - delay ~15 sec</span></span> |

### <a name="telemetry"></a><span data-ttu-id="00d33-282">遙測</span><span class="sxs-lookup"><span data-stu-id="00d33-282">Telemetry</span></span>
<span data-ttu-id="00d33-283">重試嘗試會記錄到 **TraceSource**。</span><span class="sxs-lookup"><span data-stu-id="00d33-283">Retry attempts are logged to a **TraceSource**.</span></span> <span data-ttu-id="00d33-284">您必須設定 **TraceListener** 來擷取事件，並將事件寫入適當的目的地記錄中。</span><span class="sxs-lookup"><span data-stu-id="00d33-284">You must configure a **TraceListener** to capture the events and write them to a suitable destination log.</span></span> <span data-ttu-id="00d33-285">您可以使用 **TextWriterTraceListener** 或 **XmlWriterTraceListener** 將資料寫入記錄檔，使用 **EventLogTraceListener** 寫入 Windows 事件記錄檔，或使用 **EventProviderTraceListener** 將追蹤資料寫入 ETW 子系統。</span><span class="sxs-lookup"><span data-stu-id="00d33-285">You can use the **TextWriterTraceListener** or **XmlWriterTraceListener** to write the data to a log file, the **EventLogTraceListener** to write to the Windows Event Log, or the **EventProviderTraceListener** to write trace data to the ETW subsystem.</span></span> <span data-ttu-id="00d33-286">您也可以設定自動排清緩衝區，並設定所記錄事件的詳細資訊 (例如錯誤、警告、資訊和詳細資訊)。</span><span class="sxs-lookup"><span data-stu-id="00d33-286">You can also configure auto-flushing of the buffer, and the verbosity of events that will be logged (for example, Error, Warning, Informational, and Verbose).</span></span> <span data-ttu-id="00d33-287">如需詳細資訊，請參閱 [使用 .NET 儲存體用戶端程式庫在用戶端記錄](http://msdn.microsoft.com/library/azure/dn782839.aspx)。</span><span class="sxs-lookup"><span data-stu-id="00d33-287">For more information, see [Client-side Logging with the .NET Storage Client Library](http://msdn.microsoft.com/library/azure/dn782839.aspx).</span></span>

<span data-ttu-id="00d33-288">作業會接收 **OperationContext** 執行個體，以公開用於附加自訂遙測邏輯的 **Retrying** 事件。</span><span class="sxs-lookup"><span data-stu-id="00d33-288">Operations can receive an **OperationContext** instance, which exposes a **Retrying** event that can be used to attach custom telemetry logic.</span></span> <span data-ttu-id="00d33-289">如需詳細資訊，請參閱 [OperationContext.Retrying Event](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx)。</span><span class="sxs-lookup"><span data-stu-id="00d33-289">For more information, see [OperationContext.Retrying Event](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx).</span></span>

### <a name="examples"></a><span data-ttu-id="00d33-290">範例</span><span class="sxs-lookup"><span data-stu-id="00d33-290">Examples</span></span>
<span data-ttu-id="00d33-291">下列程式碼範例示範如何建立兩個具有不同重試設定的 **TableRequestOptions** 執行個體，一個用於互動式要求，一個用於背景要求。</span><span class="sxs-lookup"><span data-stu-id="00d33-291">The following code example shows how to create two **TableRequestOptions** instances with different retry settings; one for interactive requests and one for background requests.</span></span> <span data-ttu-id="00d33-292">然後此範例會在用戶端上設定這兩個重試原則，讓這些原則適用於所有要求，此外也在特定要求上設定互動式策略，讓該策略覆寫套用到用戶端的預設設定。</span><span class="sxs-lookup"><span data-stu-id="00d33-292">The example then sets these two retry policies on the client so that they apply for all requests, and also sets the interactive strategy on a specific request so that it overrides the default settings applied to the client.</span></span>

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
                // Maximum execution time based on the business use case. Maximum value up to 10 seconds.
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

### <a name="more-information"></a><span data-ttu-id="00d33-293">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="00d33-293">More information</span></span>
* [<span data-ttu-id="00d33-294">Azure 儲存體用戶端程式庫重試原則建議</span><span class="sxs-lookup"><span data-stu-id="00d33-294">Azure Storage Client Library Retry Policy Recommendations</span></span>](https://azure.microsoft.com/blog/2014/05/22/azure-storage-client-library-retry-policy-recommendations/)
* [<span data-ttu-id="00d33-295">儲存體用戶端程式庫 2.0 – 實作重試原則</span><span class="sxs-lookup"><span data-stu-id="00d33-295">Storage Client Library 2.0 – Implementing Retry Policies</span></span>](http://gauravmantri.com/2012/12/30/storage-client-library-2-0-implementing-retry-policies/)

## <a name="sql-database-using-entity-framework-6-retry-guidelines"></a><span data-ttu-id="00d33-296">使用 Entity Framework 6 的 SQL Database 重試指引</span><span class="sxs-lookup"><span data-stu-id="00d33-296">SQL Database using Entity Framework 6 retry guidelines</span></span>
<span data-ttu-id="00d33-297">SQL Database 是託管的 SQL 資料庫，有各種大小，並以標準 (共用) 與高階 (非共用) 服務提供。</span><span class="sxs-lookup"><span data-stu-id="00d33-297">SQL Database is a hosted SQL database available in a range of sizes and as both a standard (shared) and premium (non-shared) service.</span></span> <span data-ttu-id="00d33-298">Entity Framework 是物件關聯式對應程式，可讓 .NET 開發人員使用網域特有的物件來處理關聯式資料。</span><span class="sxs-lookup"><span data-stu-id="00d33-298">Entity Framework is an object-relational mapper that enables .NET developers to work with relational data using domain-specific objects.</span></span> <span data-ttu-id="00d33-299">有了 Entity Framework，開發人員便不再需要撰寫大多數必須撰寫的資料存取程式碼。</span><span class="sxs-lookup"><span data-stu-id="00d33-299">It eliminates the need for most of the data-access code that developers usually need to write.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="00d33-300">重試機制</span><span class="sxs-lookup"><span data-stu-id="00d33-300">Retry mechanism</span></span>
<span data-ttu-id="00d33-301">透過名為 [連接恢復/重試邏輯](http://msdn.microsoft.com/data/dn456835.aspx)的機制使用 Entity Framework 6.0 或更新版本存取 SQL Database 時，會提供重試支援。</span><span class="sxs-lookup"><span data-stu-id="00d33-301">Retry support is provided when accessing SQL Database using Entity Framework 6.0 and higher through a mechanism called [Connection Resiliency / Retry Logic](http://msdn.microsoft.com/data/dn456835.aspx).</span></span> <span data-ttu-id="00d33-302">重試機制的主要功能有：</span><span class="sxs-lookup"><span data-stu-id="00d33-302">The main features of the retry mechanism are:</span></span>

* <span data-ttu-id="00d33-303">主要抽象層是 **IDbExecutionStrategy** 介面。</span><span class="sxs-lookup"><span data-stu-id="00d33-303">The primary abstraction is the **IDbExecutionStrategy** interface.</span></span> <span data-ttu-id="00d33-304">此介面會：</span><span class="sxs-lookup"><span data-stu-id="00d33-304">This interface:</span></span>
  * <span data-ttu-id="00d33-305">定義同步和非同步 **Execute*** 方法。</span><span class="sxs-lookup"><span data-stu-id="00d33-305">Defines synchronous and asynchronous **Execute*** methods.</span></span>
  * <span data-ttu-id="00d33-306">定義類別以直接使用或在資料庫內容上設定以做為預設策略、對應至提供者名稱，或對應至提供者名稱和伺服器名稱。</span><span class="sxs-lookup"><span data-stu-id="00d33-306">Defines classes that can be used directly or can be configured on a database context as a default strategy, mapped to provider name, or mapped to a provider name and server name.</span></span> <span data-ttu-id="00d33-307">在內容上設定時，重試是在個別資料庫作業層級發生的，其中可能有數個重試是針對指定的內容作業。</span><span class="sxs-lookup"><span data-stu-id="00d33-307">When configured on a context, retries occur at the level of individual database operations, of which there might be several for a given context operation.</span></span>
  * <span data-ttu-id="00d33-308">定義何時要重試失敗的連接，以及如何重試。</span><span class="sxs-lookup"><span data-stu-id="00d33-308">Defines when to retry a failed connection, and how.</span></span>
* <span data-ttu-id="00d33-309">它包含 **IDbExecutionStrategy** 介面的數個內建實作：</span><span class="sxs-lookup"><span data-stu-id="00d33-309">It includes several built-in implementations of the **IDbExecutionStrategy** interface:</span></span>
  * <span data-ttu-id="00d33-310">預設值 - 無重試。</span><span class="sxs-lookup"><span data-stu-id="00d33-310">Default - no retrying.</span></span>
  * <span data-ttu-id="00d33-311">SQL Database 的預設值 (自動) - 沒有重試，但會檢查例外狀況，並以使用 SQL Database 策略的建議來包裝例外狀況。</span><span class="sxs-lookup"><span data-stu-id="00d33-311">Default for SQL Database (automatic) - no retrying, but inspects exceptions and wraps them with suggestion to use the SQL Database strategy.</span></span>
  * <span data-ttu-id="00d33-312">SQL Database 的預設值 - 指數 (繼承自基底類別) 再加上 SQL Database 偵測邏輯。</span><span class="sxs-lookup"><span data-stu-id="00d33-312">Default for SQL Database - exponential (inherited from base class) plus SQL Database detection logic.</span></span>
* <span data-ttu-id="00d33-313">它會實作包含隨機的指數退避策略。</span><span class="sxs-lookup"><span data-stu-id="00d33-313">It implements an exponential back-off strategy that includes randomization.</span></span>
* <span data-ttu-id="00d33-314">內建的重試類別是可設定狀態，且非安全執行緒。</span><span class="sxs-lookup"><span data-stu-id="00d33-314">The built-in retry classes are stateful and are not thread safe.</span></span> <span data-ttu-id="00d33-315">不過，在目前的作業完成之後可重複執行這些類別。</span><span class="sxs-lookup"><span data-stu-id="00d33-315">However, they can be reused after the current operation is completed.</span></span>
* <span data-ttu-id="00d33-316">如果超出指定的重試計數，則會以新的例外狀況包裝結果。</span><span class="sxs-lookup"><span data-stu-id="00d33-316">If the specified retry count is exceeded, the results are wrapped in a new exception.</span></span> <span data-ttu-id="00d33-317">它不會冒出目前的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="00d33-317">It does not bubble up the current exception.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="00d33-318">原則組態</span><span class="sxs-lookup"><span data-stu-id="00d33-318">Policy configuration</span></span>
<span data-ttu-id="00d33-319">使用 Entity Framework 6.0 或更新版本存取 SQL Database 時，會提供重試支援。</span><span class="sxs-lookup"><span data-stu-id="00d33-319">Retry support is provided when accessing SQL Database using Entity Framework 6.0 and higher.</span></span> <span data-ttu-id="00d33-320">以程式設計方式設定重試原則。</span><span class="sxs-lookup"><span data-stu-id="00d33-320">Retry policies are configured programmatically.</span></span> <span data-ttu-id="00d33-321">無法根據預先作業變更組態。</span><span class="sxs-lookup"><span data-stu-id="00d33-321">The configuration cannot be changed on a per-operation basis.</span></span>

<span data-ttu-id="00d33-322">在內容上將策略設定為預設值時，您要指定一個會視需要建立新策略的功能。</span><span class="sxs-lookup"><span data-stu-id="00d33-322">When configuring a strategy on the context as the default, you specify a function that creates a new strategy on demand.</span></span> <span data-ttu-id="00d33-323">下列程式碼示範如何建立重試組態類別來延伸 **DbConfiguration** 基底類別。</span><span class="sxs-lookup"><span data-stu-id="00d33-323">The following code shows how you can create a retry configuration class that extends the **DbConfiguration** base class.</span></span>

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

<span data-ttu-id="00d33-324">當應用程式開啟時，您可以使用 **DbConfiguration** 執行個體的 **SetConfiguration** 方法將此指定為所有作業的預設重試策略。</span><span class="sxs-lookup"><span data-stu-id="00d33-324">You can then specify this as the default retry strategy for all operations using the **SetConfiguration** method of the **DbConfiguration** instance when the application starts.</span></span> <span data-ttu-id="00d33-325">依預設，EF 將自動探索和使用組態類別。</span><span class="sxs-lookup"><span data-stu-id="00d33-325">By default, EF will automatically discover and use the configuration class.</span></span>

    DbConfiguration.SetConfiguration(new BloggingContextConfiguration());

<span data-ttu-id="00d33-326">您可以使用 **DbConfigurationType** 屬性來標註內容類別，以指定內容的重試組態類別。</span><span class="sxs-lookup"><span data-stu-id="00d33-326">You can specify the retry configuration class for a context by annotating the context class with a **DbConfigurationType** attribute.</span></span> <span data-ttu-id="00d33-327">不過，如果您只有一個組態類別，EF 會使用它，而不需要標註內容。</span><span class="sxs-lookup"><span data-stu-id="00d33-327">However, if you have only one configuration class, EF will use it without the need to annotate the context.</span></span>

    [DbConfigurationType(typeof(BloggingContextConfiguration))]
    public class BloggingContext : DbContext
    { ...

<span data-ttu-id="00d33-328">如果您需要針對特定作業使用不同的重試策略，或針對特定作業停用重試，您可以建立組態類別來讓您透過在 **CallContext**中設定旗標，來暫停或切換策略。</span><span class="sxs-lookup"><span data-stu-id="00d33-328">If you need to use different retry strategies for specific operations, or disable retries for specific operations, you can create a configuration class that allows you to suspend or swap strategies by setting a flag in the **CallContext**.</span></span> <span data-ttu-id="00d33-329">組態類別可使用此旗標來切換策略，或停用您提供的策略並使用預設策略。</span><span class="sxs-lookup"><span data-stu-id="00d33-329">The configuration class can use this flag to switch strategies, or disable the strategy you provide and use a default strategy.</span></span> <span data-ttu-id="00d33-330">如需詳細資訊，請參閱「使用重試執行策略限制 (EF6 之後的版本)」一頁上的 [暫停執行策略](http://msdn.microsoft.com/dn307226#transactions_workarounds) 。</span><span class="sxs-lookup"><span data-stu-id="00d33-330">For more information, see [Suspend Execution Strategy](http://msdn.microsoft.com/dn307226#transactions_workarounds) in the page Limitations with Retrying Execution Strategies (EF6 onwards).</span></span>

<span data-ttu-id="00d33-331">針對個別作業使用特定重試策略的另一個方法，就是建立必要策略類別的執行個體，並透過參數提供所需的設定。</span><span class="sxs-lookup"><span data-stu-id="00d33-331">Another technique for using specific retry strategies for individual operations is to create an instance of the required strategy class and supply the desired settings through parameters.</span></span> <span data-ttu-id="00d33-332">然後叫用其 **ExecuteAsync** 方法。</span><span class="sxs-lookup"><span data-stu-id="00d33-332">You then invoke its **ExecuteAsync** method.</span></span>

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

<span data-ttu-id="00d33-333">使用 **DbConfiguration** 類別最簡單的方式，就是在與 **DbContext** 類別相同的組件中找到該類別。</span><span class="sxs-lookup"><span data-stu-id="00d33-333">The simplest way to use a **DbConfiguration** class is to locate it in the same assembly as the **DbContext** class.</span></span> <span data-ttu-id="00d33-334">不過，這不適用在不同案例中需要相同的內容時，例如不同的互動式和背景重試策略。</span><span class="sxs-lookup"><span data-stu-id="00d33-334">However, this is not appropriate when the same context is required in different scenarios, such as different interactive and background retry strategies.</span></span> <span data-ttu-id="00d33-335">如果不同的內容在不同的 AppDomain 中執行，您可以使用內建支援在組態檔案中指定組態類別，或使用程式碼明確地設定組態類別。</span><span class="sxs-lookup"><span data-stu-id="00d33-335">If the different contexts execute in separate AppDomains, you can use the built-in support for specifying configuration classes in the configuration file or set it explicitly using code.</span></span> <span data-ttu-id="00d33-336">如果不同的內容必須在相同的 AppDomain 中執行，則需要自訂解決方案。</span><span class="sxs-lookup"><span data-stu-id="00d33-336">If the different contexts must execute in the same AppDomain, a custom solution will be required.</span></span>

<span data-ttu-id="00d33-337">如需詳細資訊，請參閱 [程式碼式組態 (EF6 之後版本)](http://msdn.microsoft.com/data/jj680699.aspx)。</span><span class="sxs-lookup"><span data-stu-id="00d33-337">For more information, see [Code-Based Configuration (EF6 onwards)](http://msdn.microsoft.com/data/jj680699.aspx).</span></span>

<span data-ttu-id="00d33-338">下表顯示使用 EF6 時內建重試原則的預設設定。</span><span class="sxs-lookup"><span data-stu-id="00d33-338">The following table shows the default settings for the built-in retry policy when using EF6.</span></span>

![重試指引表格](./images/retry-service-specific/RetryServiceSpecificGuidanceTable4.png)

### <a name="retry-usage-guidance"></a><span data-ttu-id="00d33-340">重試使用指引</span><span class="sxs-lookup"><span data-stu-id="00d33-340">Retry usage guidance</span></span>
<span data-ttu-id="00d33-341">使用 EF6 存取 SQL Database 時，請考慮下列指引：</span><span class="sxs-lookup"><span data-stu-id="00d33-341">Consider the following guidelines when accessing SQL Database using EF6:</span></span>

* <span data-ttu-id="00d33-342">選擇適當的服務選項 (共用或高階)。</span><span class="sxs-lookup"><span data-stu-id="00d33-342">Choose the appropriate service option (shared or premium).</span></span> <span data-ttu-id="00d33-343">共用的執行個體可能會有延遲時間比一般連接長，及因為共用伺服器有其他租用戶使用而節流的問題。</span><span class="sxs-lookup"><span data-stu-id="00d33-343">A shared instance may suffer longer than usual connection delays and throttling due to the usage by other tenants of the shared server.</span></span> <span data-ttu-id="00d33-344">如果需要可預測的效能和可靠的低延遲作業，請考慮選擇高階選項。</span><span class="sxs-lookup"><span data-stu-id="00d33-344">If predictable performance and reliable low latency operations are required, consider choosing the premium option.</span></span>
* <span data-ttu-id="00d33-345">不建議將固定間隔策略搭配 Azure SQL Database 使用。</span><span class="sxs-lookup"><span data-stu-id="00d33-345">A fixed interval strategy is not recommended for use with Azure SQL Database.</span></span> <span data-ttu-id="00d33-346">相反地，應使用指數退避策略，因為服務可能會過載，且延遲時間越長，復原所需的時間也越長。</span><span class="sxs-lookup"><span data-stu-id="00d33-346">Instead, use an exponential back-off strategy because the service may be overloaded, and longer delays allow more time for it to recover.</span></span>
* <span data-ttu-id="00d33-347">定義連接時，為連接與命令逾時選擇合適的值。</span><span class="sxs-lookup"><span data-stu-id="00d33-347">Choose a suitable value for the connection and command timeouts when defining connections.</span></span> <span data-ttu-id="00d33-348">讓逾時以您的商務邏輯設計與直通測試為基礎。</span><span class="sxs-lookup"><span data-stu-id="00d33-348">Base the timeout on both your business logic design and through testing.</span></span> <span data-ttu-id="00d33-349">您可能需要隨時間修改此值，因為資料量或商務程序會改變。</span><span class="sxs-lookup"><span data-stu-id="00d33-349">You may need to modify this value over time as the volumes of data or the business processes change.</span></span> <span data-ttu-id="00d33-350">逾時值過短，會造成資料庫忙碌時連接永遠失敗。</span><span class="sxs-lookup"><span data-stu-id="00d33-350">Too short a timeout may result in premature failures of connections when the database is busy.</span></span> <span data-ttu-id="00d33-351">逾時值過長，可能會因為在偵測失敗連接之前等待過久，而造成重試邏輯無法正確運作。</span><span class="sxs-lookup"><span data-stu-id="00d33-351">Too long a timeout may prevent the retry logic working correctly by waiting too long before detecting a failed connection.</span></span> <span data-ttu-id="00d33-352">逾時值是端對端延遲的元件，雖然您無法輕易判斷儲存內容時會執行幾個命令。</span><span class="sxs-lookup"><span data-stu-id="00d33-352">The value of the timeout is a component of the end-to-end latency, although you cannot easily determine how many commands will execute when saving the context.</span></span> <span data-ttu-id="00d33-353">您可以透過設定 **DbContext** 執行個體的 **CommandTimeout** 屬性，來變更預設逾時。</span><span class="sxs-lookup"><span data-stu-id="00d33-353">You can change the default timeout by setting the **CommandTimeout** property of the **DbContext** instance.</span></span>
* <span data-ttu-id="00d33-354">Entity Framework 支援在組態檔中定義的重試組態。</span><span class="sxs-lookup"><span data-stu-id="00d33-354">Entity Framework supports retry configurations defined in configuration files.</span></span> <span data-ttu-id="00d33-355">不過，為了讓 Azure 有最大的彈性，您應該考慮在應用程式內以程式設計方式建立組態。</span><span class="sxs-lookup"><span data-stu-id="00d33-355">However, for maximum flexibility on Azure you should consider creating the configuration programmatically within the application.</span></span> <span data-ttu-id="00d33-356">重試原則的特定參數 (例如重試次數與重試間隔) 可以儲存在服務組態檔中，並在執行階段用於建立適當的原則。</span><span class="sxs-lookup"><span data-stu-id="00d33-356">The specific parameters for the retry policies, such as the number of retries and the retry intervals, can be stored in the service configuration file and used at runtime to create the appropriate policies.</span></span> <span data-ttu-id="00d33-357">這會讓設定變更，而1不需要應用程式重新啟動。</span><span class="sxs-lookup"><span data-stu-id="00d33-357">This allows the settings to be changed without requiring the application to be restarted.</span></span>

<span data-ttu-id="00d33-358">請考慮讓重試作業從下列設定開始。</span><span class="sxs-lookup"><span data-stu-id="00d33-358">Consider starting with the following settings for retrying operations.</span></span> <span data-ttu-id="00d33-359">您無法指定重試嘗試之間的延遲 (它是固定的，並產生成為指數序列)。</span><span class="sxs-lookup"><span data-stu-id="00d33-359">You cannot specify the delay between retry attempts (it is fixed and generated as an exponential sequence).</span></span> <span data-ttu-id="00d33-360">您可以只指定最大值，如此處所示，除非您建立自訂重試策略。</span><span class="sxs-lookup"><span data-stu-id="00d33-360">You can specify only the maximum values, as shown here; unless you create a custom retry strategy.</span></span> <span data-ttu-id="00d33-361">這些是一般用途設定，您應該監視作業並微調其值以符合您自己的需求。</span><span class="sxs-lookup"><span data-stu-id="00d33-361">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="00d33-362">**內容**</span><span class="sxs-lookup"><span data-stu-id="00d33-362">**Context**</span></span> | <span data-ttu-id="00d33-363">**範例目標 E2E<br />最大延遲**</span><span class="sxs-lookup"><span data-stu-id="00d33-363">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="00d33-364">**重試原則**</span><span class="sxs-lookup"><span data-stu-id="00d33-364">**Retry policy**</span></span> | <span data-ttu-id="00d33-365">**設定**</span><span class="sxs-lookup"><span data-stu-id="00d33-365">**Settings**</span></span> | <span data-ttu-id="00d33-366">**值**</span><span class="sxs-lookup"><span data-stu-id="00d33-366">**Values**</span></span> | <span data-ttu-id="00d33-367">**運作方式**</span><span class="sxs-lookup"><span data-stu-id="00d33-367">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="00d33-368">互動式、UI </span><span class="sxs-lookup"><span data-stu-id="00d33-368">Interactive, UI,</span></span><br /><span data-ttu-id="00d33-369">或前景</span><span class="sxs-lookup"><span data-stu-id="00d33-369">or foreground</span></span> |<span data-ttu-id="00d33-370">2 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-370">2 seconds</span></span> |<span data-ttu-id="00d33-371">指數</span><span class="sxs-lookup"><span data-stu-id="00d33-371">Exponential</span></span> |<span data-ttu-id="00d33-372">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="00d33-372">MaxRetryCount</span></span><br /><span data-ttu-id="00d33-373">MaxDelay</span><span class="sxs-lookup"><span data-stu-id="00d33-373">MaxDelay</span></span> |<span data-ttu-id="00d33-374">3</span><span class="sxs-lookup"><span data-stu-id="00d33-374">3</span></span><br /><span data-ttu-id="00d33-375">750 毫秒</span><span class="sxs-lookup"><span data-stu-id="00d33-375">750 ms</span></span> |<span data-ttu-id="00d33-376">嘗試 1 - 延遲 0 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-376">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="00d33-377">嘗試 2 - 延遲 750 毫秒</span><span class="sxs-lookup"><span data-stu-id="00d33-377">Attempt 2 - delay 750 ms</span></span><br /><span data-ttu-id="00d33-378">嘗試 3 - 延遲 750 毫秒</span><span class="sxs-lookup"><span data-stu-id="00d33-378">Attempt 3 – delay 750 ms</span></span> |
| <span data-ttu-id="00d33-379">背景</span><span class="sxs-lookup"><span data-stu-id="00d33-379">Background</span></span><br /> <span data-ttu-id="00d33-380">或批次</span><span class="sxs-lookup"><span data-stu-id="00d33-380">or batch</span></span> |<span data-ttu-id="00d33-381">30 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-381">30 seconds</span></span> |<span data-ttu-id="00d33-382">指數</span><span class="sxs-lookup"><span data-stu-id="00d33-382">Exponential</span></span> |<span data-ttu-id="00d33-383">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="00d33-383">MaxRetryCount</span></span><br /><span data-ttu-id="00d33-384">MaxDelay</span><span class="sxs-lookup"><span data-stu-id="00d33-384">MaxDelay</span></span> |<span data-ttu-id="00d33-385">5</span><span class="sxs-lookup"><span data-stu-id="00d33-385">5</span></span><br /><span data-ttu-id="00d33-386">12 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-386">12 seconds</span></span> |<span data-ttu-id="00d33-387">嘗試 1 - 延遲 0 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-387">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="00d33-388">嘗試 2 - 延遲 ~1 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-388">Attempt 2 - delay ~1 sec</span></span><br /><span data-ttu-id="00d33-389">嘗試 3 - 延遲 ~3 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-389">Attempt 3 - delay ~3 sec</span></span><br /><span data-ttu-id="00d33-390">嘗試 4 - 延遲 ~7 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-390">Attempt 4 - delay ~7 sec</span></span><br /><span data-ttu-id="00d33-391">嘗試 5 - 延遲 12 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-391">Attempt 5 - delay 12 sec</span></span> |

> [!NOTE]
> <span data-ttu-id="00d33-392">端對端延遲目標會假設服務連接的預設逾時值。</span><span class="sxs-lookup"><span data-stu-id="00d33-392">The end-to-end latency targets assume the default timeout for connections to the service.</span></span> <span data-ttu-id="00d33-393">如果您指定較長的連接逾時值，則系統會為每個重試嘗試增加這段時間，來延長端對端延遲。</span><span class="sxs-lookup"><span data-stu-id="00d33-393">If you specify longer connection timeouts, the end-to-end latency will be extended by this additional time for every retry attempt.</span></span>
>
>

### <a name="examples"></a><span data-ttu-id="00d33-394">範例</span><span class="sxs-lookup"><span data-stu-id="00d33-394">Examples</span></span>
<span data-ttu-id="00d33-395">下列程式碼範例會定義使用 Entity Framework 的簡單資料存取解決方案。</span><span class="sxs-lookup"><span data-stu-id="00d33-395">The following code example defines a simple data access solution that uses Entity Framework.</span></span> <span data-ttu-id="00d33-396">它會設定特定的重試策略，做法是為會延伸 **DbConfiguration** 的 **BlogConfiguration** 類別定義執行個體。</span><span class="sxs-lookup"><span data-stu-id="00d33-396">It sets a specific retry strategy by defining an instance of a class named **BlogConfiguration** that extends **DbConfiguration**.</span></span>

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

<span data-ttu-id="00d33-397">使用 Entity Framework 重試機制的更多範例位於 [連接恢復/重試邏輯](http://msdn.microsoft.com/data/dn456835.aspx)中。</span><span class="sxs-lookup"><span data-stu-id="00d33-397">More examples of using the Entity Framework retry mechanism can be found in [Connection Resiliency / Retry Logic](http://msdn.microsoft.com/data/dn456835.aspx).</span></span>

### <a name="more-information"></a><span data-ttu-id="00d33-398">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="00d33-398">More information</span></span>
* [<span data-ttu-id="00d33-399">Azure SQL Database 效能和彈性指南 (英文)</span><span class="sxs-lookup"><span data-stu-id="00d33-399">Azure SQL Database Performance and Elasticity Guide</span></span>](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)

## <a name="sql-database-using-entity-framework-core-retry-guidelines"></a><span data-ttu-id="00d33-400">使用 Entity Framework Core 的 SQL Database 重試指引</span><span class="sxs-lookup"><span data-stu-id="00d33-400">SQL Database using Entity Framework Core retry guidelines</span></span>
<span data-ttu-id="00d33-401">[Entity Framework Core](/ef/core/) 是物件關聯式對應程式，可讓 .NET Core 開發人員使用網域特有的物件來處理資料。</span><span class="sxs-lookup"><span data-stu-id="00d33-401">[Entity Framework Core](/ef/core/) is an object-relational mapper that enables .NET Core developers to work with data using domain-specific objects.</span></span> <span data-ttu-id="00d33-402">有了 Entity Framework，開發人員便不再需要撰寫大多數必須撰寫的資料存取程式碼。</span><span class="sxs-lookup"><span data-stu-id="00d33-402">It eliminates the need for most of the data-access code that developers usually need to write.</span></span> <span data-ttu-id="00d33-403">這個版本的 Entity Framework 為從頭撰寫，不會自動繼承 EF6.x 的所有功能。</span><span class="sxs-lookup"><span data-stu-id="00d33-403">This version of Entity Framework was written from the ground up, and doesn't automatically inherit all the features from EF6.x.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="00d33-404">重試機制</span><span class="sxs-lookup"><span data-stu-id="00d33-404">Retry mechanism</span></span>
<span data-ttu-id="00d33-405">透過名為[恢復連線](/ef/core/miscellaneous/connection-resiliency)的機制使用 Entity Framework Core 版本存取 SQL Database 時，會提供重試支援。</span><span class="sxs-lookup"><span data-stu-id="00d33-405">Retry support is provided when accessing SQL Database using Entity Framework Core through a mechanism called [Connection Resiliency](/ef/core/miscellaneous/connection-resiliency).</span></span> <span data-ttu-id="00d33-406">恢復連線功能是在 EF Core 1.1.0 引進。</span><span class="sxs-lookup"><span data-stu-id="00d33-406">Connection resiliency was introduced in EF Core 1.1.0.</span></span>

<span data-ttu-id="00d33-407">主要抽象層是 `IExecutionStrategy` 介面。</span><span class="sxs-lookup"><span data-stu-id="00d33-407">The primary abstraction is the `IExecutionStrategy` interface.</span></span> <span data-ttu-id="00d33-408">SQL Server 包括 SQL Azure 的執行策略，知道可以重試的例外狀況類型，有合理的重試次數上限、重試之間的延遲等預設值。</span><span class="sxs-lookup"><span data-stu-id="00d33-408">The execution strategy for SQL Server, including SQL Azure, is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, and so on.</span></span>

### <a name="examples"></a><span data-ttu-id="00d33-409">範例</span><span class="sxs-lookup"><span data-stu-id="00d33-409">Examples</span></span>

<span data-ttu-id="00d33-410">設定 DbContext 物件時 (該物件表示資料庫的工作階段)，下列程式碼會啟用自動重試。</span><span class="sxs-lookup"><span data-stu-id="00d33-410">The following code enables automatic retries when configuring the DbContext object, which represents a session with the database.</span></span> 

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(
            @"Server=(localdb)\mssqllocaldb;Database=EFMiscellanous.ConnectionResiliency;Trusted_Connection=True;",
            options => options.EnableRetryOnFailure());
}
```

<span data-ttu-id="00d33-411">下列程式碼示範如何使用執行策略來執行有自動重試的交易。</span><span class="sxs-lookup"><span data-stu-id="00d33-411">The following code shows how to execute a transaction with automatic retries, by using an execution strategy.</span></span> <span data-ttu-id="00d33-412">交易在委派中定義。</span><span class="sxs-lookup"><span data-stu-id="00d33-412">The transaction is defined in a delegate.</span></span> <span data-ttu-id="00d33-413">如果發生暫時性失敗，執行策略會再次叫用委派。</span><span class="sxs-lookup"><span data-stu-id="00d33-413">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

```csharp
using (var db = new BloggingContext())
{
    var strategy = db.Database.CreateExecutionStrategy();

    strategy.Execute(() =>
    {
        using (var transaction = db.Database.BeginTransaction())
        {
            db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/dotnet" });
            db.SaveChanges();

            db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/visualstudio" });
            db.SaveChanges();

            transaction.Commit();
        }
    });
}
```

### <a name="more-information"></a><span data-ttu-id="00d33-414">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="00d33-414">More information</span></span>
* [<span data-ttu-id="00d33-415">恢復連線</span><span class="sxs-lookup"><span data-stu-id="00d33-415">Connection Resiliency</span></span>](/ef/core/miscellaneous/connection-resiliency)
* [<span data-ttu-id="00d33-416">Data Points - EF Core 1.1</span><span class="sxs-lookup"><span data-stu-id="00d33-416">Data Points - EF Core 1.1</span></span>](https://msdn.microsoft.com/en-us/magazine/mt745093.aspx)

## <a name="sql-database-using-adonet-retry-guidelines"></a><span data-ttu-id="00d33-417">使用 ADO.NET 的 SQL Database 重試指引</span><span class="sxs-lookup"><span data-stu-id="00d33-417">SQL Database using ADO.NET retry guidelines</span></span>
<span data-ttu-id="00d33-418">SQL Database 是託管的 SQL 資料庫，有各種大小，並以標準 (共用) 與高階 (非共用) 服務提供。</span><span class="sxs-lookup"><span data-stu-id="00d33-418">SQL Database is a hosted SQL database available in a range of sizes and as both a standard (shared) and premium (non-shared) service.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="00d33-419">重試機制</span><span class="sxs-lookup"><span data-stu-id="00d33-419">Retry mechanism</span></span>
<span data-ttu-id="00d33-420">SQL Database 在使用 ADO.NET 存取時，沒有內建的重試支援。</span><span class="sxs-lookup"><span data-stu-id="00d33-420">SQL Database has no built-in support for retries when accessed using ADO.NET.</span></span> <span data-ttu-id="00d33-421">不過，要求的傳回碼可用來判斷要求失敗的原因。</span><span class="sxs-lookup"><span data-stu-id="00d33-421">However, the return codes from requests can be used to determine why a request failed.</span></span> <span data-ttu-id="00d33-422">如需有關 SQL Database 節流的詳細資訊，請參閱 [Azure SQL Database 資源限制](/azure/sql-database/sql-database-resource-limits)。</span><span class="sxs-lookup"><span data-stu-id="00d33-422">For more information about SQL Database throttling, see [Azure SQL Database resource limits](/azure/sql-database/sql-database-resource-limits).</span></span> <span data-ttu-id="00d33-423">如需相關錯誤碼的清單，請參閱 [SQL Database 用戶端應用程式的 SQL 錯誤碼](/azure/sql-database/sql-database-develop-error-messages)。</span><span class="sxs-lookup"><span data-stu-id="00d33-423">For a list of relevant error codes, see [SQL error codes for SQL Database client applications](/azure/sql-database/sql-database-develop-error-messages).</span></span>

<span data-ttu-id="00d33-424">您可以使用 Polly 程式庫實作 SQL Database 重試。</span><span class="sxs-lookup"><span data-stu-id="00d33-424">You can use the Polly library to implement retries for SQL Database.</span></span> <span data-ttu-id="00d33-425">請參閱 [Polly 的暫時性錯誤處理](#transient-fault-handling-with-polly)。</span><span class="sxs-lookup"><span data-stu-id="00d33-425">See [Transient fault handling with Polly](#transient-fault-handling-with-polly).</span></span>

### <a name="retry-usage-guidance"></a><span data-ttu-id="00d33-426">重試使用指引</span><span class="sxs-lookup"><span data-stu-id="00d33-426">Retry usage guidance</span></span>
<span data-ttu-id="00d33-427">使用 ADO.NET 存取 SQL Database 時，請考慮下列指引：</span><span class="sxs-lookup"><span data-stu-id="00d33-427">Consider the following guidelines when accessing SQL Database using ADO.NET:</span></span>

* <span data-ttu-id="00d33-428">選擇適當的服務選項 (共用或高階)。</span><span class="sxs-lookup"><span data-stu-id="00d33-428">Choose the appropriate service option (shared or premium).</span></span> <span data-ttu-id="00d33-429">共用的執行個體可能會有延遲時間比一般連接長，及因為共用伺服器有其他租用戶使用而節流的問題。</span><span class="sxs-lookup"><span data-stu-id="00d33-429">A shared instance may suffer longer than usual connection delays and throttling due to the usage by other tenants of the shared server.</span></span> <span data-ttu-id="00d33-430">如果需要更可預測的效能和可靠的低延遲作業，請考慮選擇高階選項。</span><span class="sxs-lookup"><span data-stu-id="00d33-430">If more predictable performance and reliable low latency operations are required, consider choosing the premium option.</span></span>
* <span data-ttu-id="00d33-431">請確定您是在適當的層級或範圍執行重試，以避免非等冪作業導致資料不一致。</span><span class="sxs-lookup"><span data-stu-id="00d33-431">Ensure that you perform retries at the appropriate level or scope to avoid non-idempotent operations causing inconsistency in the data.</span></span> <span data-ttu-id="00d33-432">理想的狀況是，所有的作業應等冪，而因此能夠重複執行，而不會造成不一致。</span><span class="sxs-lookup"><span data-stu-id="00d33-432">Ideally, all operations should be idempotent so that they can be repeated without causing inconsistency.</span></span> <span data-ttu-id="00d33-433">若不是此狀況，則應在如果一個作業失敗，則允許復原所有相關變更的層級或範圍中執行重試；例如，從交易範圍內。</span><span class="sxs-lookup"><span data-stu-id="00d33-433">Where this is not the case, the retry should be performed at a level or scope that allows all related changes to be undone if one operation fails; for example, from within a transactional scope.</span></span> <span data-ttu-id="00d33-434">如需詳細資訊，請參閱 [雲端服務基礎資料存取層 – 暫時性錯誤處理](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee)。</span><span class="sxs-lookup"><span data-stu-id="00d33-434">For more information, see [Cloud Service Fundamentals Data Access Layer – Transient Fault Handling](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee).</span></span>
* <span data-ttu-id="00d33-435">互動式案例除外，不建議將固定間隔策略搭配 Azure SQL Database 使用；互動式案例中只有一些間隔非常短的重試。</span><span class="sxs-lookup"><span data-stu-id="00d33-435">A fixed interval strategy is not recommended for use with Azure SQL Database except for interactive scenarios where there are only a few retries at very short intervals.</span></span> <span data-ttu-id="00d33-436">相反地，請考慮為大部分的案例使用指數退避策略。</span><span class="sxs-lookup"><span data-stu-id="00d33-436">Instead, consider using an exponential back-off strategy for the majority of scenarios.</span></span>
* <span data-ttu-id="00d33-437">定義連接時，為連接與命令逾時選擇合適的值。</span><span class="sxs-lookup"><span data-stu-id="00d33-437">Choose a suitable value for the connection and command timeouts when defining connections.</span></span> <span data-ttu-id="00d33-438">逾時值過短，會造成資料庫忙碌時連接永遠失敗。</span><span class="sxs-lookup"><span data-stu-id="00d33-438">Too short a timeout may result in premature failures of connections when the database is busy.</span></span> <span data-ttu-id="00d33-439">逾時值過長，可能會因為在偵測失敗連接之前等待過久，而造成重試邏輯無法正確運作。</span><span class="sxs-lookup"><span data-stu-id="00d33-439">Too long a timeout may prevent the retry logic working correctly by waiting too long before detecting a failed connection.</span></span> <span data-ttu-id="00d33-440">逾時值是端對端延遲的元件；它會有效地加到在重試原則中為每個重試嘗試指定的重試延遲。</span><span class="sxs-lookup"><span data-stu-id="00d33-440">The value of the timeout is a component of the end-to-end latency; it is effectively added to the retry delay specified in the retry policy for every retry attempt.</span></span>
* <span data-ttu-id="00d33-441">在重試特定次數後即關閉連接，即使使用的是指數退避策略，然後在新的連接上重試作業。</span><span class="sxs-lookup"><span data-stu-id="00d33-441">Close the connection after a certain number of retries, even when using an exponential back off retry logic, and retry the operation on a new connection.</span></span> <span data-ttu-id="00d33-442">在同一個連接上多次重試同一個作業，也是造成連接問題的因素之一。</span><span class="sxs-lookup"><span data-stu-id="00d33-442">Retrying the same operation multiple times on the same connection can be a factor that contributes to connection problems.</span></span> <span data-ttu-id="00d33-443">有關此技術的範例，請參閱 [雲端服務基礎資料存取層 – 暫時性錯誤處理](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx)。</span><span class="sxs-lookup"><span data-stu-id="00d33-443">For an example of this technique, see [Cloud Service Fundamentals Data Access Layer – Transient Fault Handling](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx).</span></span>
* <span data-ttu-id="00d33-444">當使用連接共用時 (預設值)，有可能會從共用中選擇同一個連接，即使是在關閉並重新開啟連接後。</span><span class="sxs-lookup"><span data-stu-id="00d33-444">When connection pooling is in use (the default) there is a chance that the same connection will be chosen from the pool, even after closing and reopening a connection.</span></span> <span data-ttu-id="00d33-445">如果發生這種情況，則解決的技術是呼叫 **SqlConnection** 類別的 **ClearPool** 方法將連接標示為不可重複使用。</span><span class="sxs-lookup"><span data-stu-id="00d33-445">If this is the case, a technique to resolve it is to call the **ClearPool** method of the **SqlConnection** class to mark the connection as not reusable.</span></span> <span data-ttu-id="00d33-446">但只應在數次連接嘗試皆失敗後，及碰到與錯誤連接有關的特定類別暫時性錯誤時，如 SQL 逾時 (錯誤碼 -2)，才這麼做。</span><span class="sxs-lookup"><span data-stu-id="00d33-446">However, you should do this only after several connection attempts have failed, and only when encountering the specific class of transient failures such as SQL timeouts (error code -2) related to faulty connections.</span></span>
* <span data-ttu-id="00d33-447">如果資料存取程式碼使用交易做為起始的 **TransactionScope** 執行個體，則重試邏輯應重新開啟連接並啟動新的交易範圍。</span><span class="sxs-lookup"><span data-stu-id="00d33-447">If the data access code uses transactions initiated as **TransactionScope** instances, the retry logic should reopen the connection and initiate a new transaction scope.</span></span> <span data-ttu-id="00d33-448">基於這個原因，可以重試的程式碼區塊應包含交易的整個範圍。</span><span class="sxs-lookup"><span data-stu-id="00d33-448">For this reason, the retryable code block should encompass the entire scope of the transaction.</span></span>

<span data-ttu-id="00d33-449">請考慮從重試作業的下列設定開始。</span><span class="sxs-lookup"><span data-stu-id="00d33-449">Consider starting with following settings for retrying operations.</span></span> <span data-ttu-id="00d33-450">這些是一般用途設定，您應該監視作業並微調其值以符合您自己的需求。</span><span class="sxs-lookup"><span data-stu-id="00d33-450">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="00d33-451">**內容**</span><span class="sxs-lookup"><span data-stu-id="00d33-451">**Context**</span></span> | <span data-ttu-id="00d33-452">**範例目標 E2E<br />最大延遲**</span><span class="sxs-lookup"><span data-stu-id="00d33-452">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="00d33-453">**重試策略**</span><span class="sxs-lookup"><span data-stu-id="00d33-453">**Retry strategy**</span></span> | <span data-ttu-id="00d33-454">**設定**</span><span class="sxs-lookup"><span data-stu-id="00d33-454">**Settings**</span></span> | <span data-ttu-id="00d33-455">**值**</span><span class="sxs-lookup"><span data-stu-id="00d33-455">**Values**</span></span> | <span data-ttu-id="00d33-456">**運作方式**</span><span class="sxs-lookup"><span data-stu-id="00d33-456">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="00d33-457">互動式、UI </span><span class="sxs-lookup"><span data-stu-id="00d33-457">Interactive, UI,</span></span><br /><span data-ttu-id="00d33-458">或前景</span><span class="sxs-lookup"><span data-stu-id="00d33-458">or foreground</span></span> |<span data-ttu-id="00d33-459">2 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-459">2 sec</span></span> |<span data-ttu-id="00d33-460">FixedInterval</span><span class="sxs-lookup"><span data-stu-id="00d33-460">FixedInterval</span></span> |<span data-ttu-id="00d33-461">重試計數</span><span class="sxs-lookup"><span data-stu-id="00d33-461">Retry count</span></span><br /><span data-ttu-id="00d33-462">重試間隔</span><span class="sxs-lookup"><span data-stu-id="00d33-462">Retry interval</span></span><br /><span data-ttu-id="00d33-463">第一個快速重試</span><span class="sxs-lookup"><span data-stu-id="00d33-463">First fast retry</span></span> |<span data-ttu-id="00d33-464">3</span><span class="sxs-lookup"><span data-stu-id="00d33-464">3</span></span><br /><span data-ttu-id="00d33-465">500 毫秒</span><span class="sxs-lookup"><span data-stu-id="00d33-465">500 ms</span></span><br /><span data-ttu-id="00d33-466">true</span><span class="sxs-lookup"><span data-stu-id="00d33-466">true</span></span> |<span data-ttu-id="00d33-467">嘗試 1 - 延遲 0 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-467">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="00d33-468">嘗試 2 - 延遲 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="00d33-468">Attempt 2 - delay 500 ms</span></span><br /><span data-ttu-id="00d33-469">嘗試 3 - 延遲 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="00d33-469">Attempt 3 - delay 500 ms</span></span> |
| <span data-ttu-id="00d33-470">背景</span><span class="sxs-lookup"><span data-stu-id="00d33-470">Background</span></span><br /><span data-ttu-id="00d33-471">或批次</span><span class="sxs-lookup"><span data-stu-id="00d33-471">or batch</span></span> |<span data-ttu-id="00d33-472">30 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-472">30 sec</span></span> |<span data-ttu-id="00d33-473">ExponentialBackoff</span><span class="sxs-lookup"><span data-stu-id="00d33-473">ExponentialBackoff</span></span> |<span data-ttu-id="00d33-474">重試計數</span><span class="sxs-lookup"><span data-stu-id="00d33-474">Retry count</span></span><br /><span data-ttu-id="00d33-475">最小輪詢</span><span class="sxs-lookup"><span data-stu-id="00d33-475">Min back-off</span></span><br /><span data-ttu-id="00d33-476">最大輪詢</span><span class="sxs-lookup"><span data-stu-id="00d33-476">Max back-off</span></span><br /><span data-ttu-id="00d33-477">差異輪詢</span><span class="sxs-lookup"><span data-stu-id="00d33-477">Delta back-off</span></span><br /><span data-ttu-id="00d33-478">第一個快速重試</span><span class="sxs-lookup"><span data-stu-id="00d33-478">First fast retry</span></span> |<span data-ttu-id="00d33-479">5</span><span class="sxs-lookup"><span data-stu-id="00d33-479">5</span></span><br /><span data-ttu-id="00d33-480">0 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-480">0 sec</span></span><br /><span data-ttu-id="00d33-481">60 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-481">60 sec</span></span><br /><span data-ttu-id="00d33-482">2 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-482">2 sec</span></span><br /><span data-ttu-id="00d33-483">false</span><span class="sxs-lookup"><span data-stu-id="00d33-483">false</span></span> |<span data-ttu-id="00d33-484">嘗試 1 - 延遲 0 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-484">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="00d33-485">嘗試 2 - 延遲 ~2 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-485">Attempt 2 - delay ~2 sec</span></span><br /><span data-ttu-id="00d33-486">嘗試 3 - 延遲 ~6 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-486">Attempt 3 - delay ~6 sec</span></span><br /><span data-ttu-id="00d33-487">嘗試 4 - 延遲 ~14 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-487">Attempt 4 - delay ~14 sec</span></span><br /><span data-ttu-id="00d33-488">嘗試 5 - 延遲 ~30 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-488">Attempt 5 - delay ~30 sec</span></span> |

> [!NOTE]
> <span data-ttu-id="00d33-489">端對端延遲目標會假設服務連接的預設逾時值。</span><span class="sxs-lookup"><span data-stu-id="00d33-489">The end-to-end latency targets assume the default timeout for connections to the service.</span></span> <span data-ttu-id="00d33-490">如果您指定較長的連接逾時值，則系統會為每個重試嘗試增加這段時間，來延長端對端延遲。</span><span class="sxs-lookup"><span data-stu-id="00d33-490">If you specify longer connection timeouts, the end-to-end latency will be extended by this additional time for every retry attempt.</span></span>
>
>

### <a name="examples"></a><span data-ttu-id="00d33-491">範例</span><span class="sxs-lookup"><span data-stu-id="00d33-491">Examples</span></span>
<span data-ttu-id="00d33-492">本節說明如何搭配在 `Policy` 類別中設定的一組重試原則，使用 Polly 存取 Azure SQL Database。</span><span class="sxs-lookup"><span data-stu-id="00d33-492">This section shows how you can use Polly to access Azure SQL Database using a set of retry policies configured in the `Policy` class.</span></span>

<span data-ttu-id="00d33-493">下列程式碼顯示 `SqlCommand` 類別上的擴充方法，會使用指數輪詢呼叫 `ExecuteAsync`。</span><span class="sxs-lookup"><span data-stu-id="00d33-493">The following code shows an extension method on the `SqlCommand` class that calls `ExecuteAsync` with exponential backoff.</span></span>

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

<span data-ttu-id="00d33-494">這個非同步擴充方法的用法如下。</span><span class="sxs-lookup"><span data-stu-id="00d33-494">This asynchronous extension method can be used as follows.</span></span>

```csharp
var sqlCommand = sqlConnection.CreateCommand();
sqlCommand.CommandText = "[some query]";

using (var reader = await sqlCommand.ExecuteReaderWithRetryAsync())
{
    // Do something with the values
}
```

### <a name="more-information"></a><span data-ttu-id="00d33-495">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="00d33-495">More information</span></span>
* [<span data-ttu-id="00d33-496">雲端服務基礎資料存取層 – 暫時性錯誤處理。</span><span class="sxs-lookup"><span data-stu-id="00d33-496">Cloud Service Fundamentals Data Access Layer – Transient Fault Handling</span></span>](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx)

<span data-ttu-id="00d33-497">如需充分使用 SQL Database 的一般指引，請參閱 [Azure SQL 資料庫效能和彈性指南](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)。</span><span class="sxs-lookup"><span data-stu-id="00d33-497">For general guidance on getting the most from SQL Database, see [Azure SQL Database Performance and Elasticity Guide](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx).</span></span>


## <a name="service-bus-retry-guidelines"></a><span data-ttu-id="00d33-498">服務匯流排重試指引</span><span class="sxs-lookup"><span data-stu-id="00d33-498">Service Bus retry guidelines</span></span>
<span data-ttu-id="00d33-499">服務匯流排是雲端傳訊平台，能夠提供鬆散結合的訊息交換，以及為在雲端託管或內部部署的應用程式元件改善擴充性和恢復能力。</span><span class="sxs-lookup"><span data-stu-id="00d33-499">Service Bus is a cloud messaging platform that provides loosely coupled message exchange with improved scale and resiliency for components of an application, whether hosted in the cloud or on-premises.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="00d33-500">重試機制</span><span class="sxs-lookup"><span data-stu-id="00d33-500">Retry mechanism</span></span>
<span data-ttu-id="00d33-501">服務匯流排使用 [RetryPolicy](http://msdn.microsoft.com/library/microsoft.servicebus.retrypolicy.aspx) 基底類別的實作來實作重試。</span><span class="sxs-lookup"><span data-stu-id="00d33-501">Service Bus implements retries using implementations of the [RetryPolicy](http://msdn.microsoft.com/library/microsoft.servicebus.retrypolicy.aspx) base class.</span></span> <span data-ttu-id="00d33-502">所有的服務匯流排用戶端皆會公開 **RetryPolicy** 屬性，此屬性可設為 **RetryPolicy** 基底類別的其中一個實作。</span><span class="sxs-lookup"><span data-stu-id="00d33-502">All of the Service Bus clients expose a **RetryPolicy** property that can be set to one of the implementations of the **RetryPolicy** base class.</span></span> <span data-ttu-id="00d33-503">內建實作如下：</span><span class="sxs-lookup"><span data-stu-id="00d33-503">The built-in implementations are:</span></span>

* <span data-ttu-id="00d33-504">[RetryExponential 類別](http://msdn.microsoft.com/library/microsoft.servicebus.retryexponential.aspx)。</span><span class="sxs-lookup"><span data-stu-id="00d33-504">The [RetryExponential Class](http://msdn.microsoft.com/library/microsoft.servicebus.retryexponential.aspx).</span></span> <span data-ttu-id="00d33-505">這會公開控制退避間隔的屬性、重試計數，以及用來限制作業完成總時間的 **TerminationTimeBuffer** 屬性。</span><span class="sxs-lookup"><span data-stu-id="00d33-505">This exposes properties that control the back-off interval, the retry count, and the **TerminationTimeBuffer** property that is used to limit the total time for the operation to complete.</span></span>
* <span data-ttu-id="00d33-506">[NoRetry 類別](http://msdn.microsoft.com/library/microsoft.servicebus.noretry.aspx)。</span><span class="sxs-lookup"><span data-stu-id="00d33-506">The [NoRetry Class](http://msdn.microsoft.com/library/microsoft.servicebus.noretry.aspx).</span></span> <span data-ttu-id="00d33-507">這用於當不需要在服務匯流排 API 層級重試時，例如當在批次或多步驟作業期間由另一個處理序管理重試時。</span><span class="sxs-lookup"><span data-stu-id="00d33-507">This is used when retries at the Service Bus API level are not required, such as when retries are managed by another process as part of a batch or multiple step operation.</span></span>

<span data-ttu-id="00d33-508">服務匯流排動作會傳回某個範圍的例外狀況，如 [附錄：傳訊例外狀況](http://msdn.microsoft.com/library/hh418082.aspx)中所列的清單。</span><span class="sxs-lookup"><span data-stu-id="00d33-508">Service Bus actions can return a range of exceptions, as listed in [Appendix: Messaging Exceptions](http://msdn.microsoft.com/library/hh418082.aspx).</span></span> <span data-ttu-id="00d33-509">此清單會提供這些重試作業的表示是否適合的資訊。</span><span class="sxs-lookup"><span data-stu-id="00d33-509">The list provides information about which if these indicate that retrying the operation is appropriate.</span></span> <span data-ttu-id="00d33-510">例如， [ServerBusyException](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.serverbusyexception.aspx) 表示用戶端應等待一段時間後，再重試作業。</span><span class="sxs-lookup"><span data-stu-id="00d33-510">For example, a [ServerBusyException](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.serverbusyexception.aspx) indicates that the client should wait for a period of time, then retry the operation.</span></span> <span data-ttu-id="00d33-511">**ServerBusyException** 的發生也會導致服務匯流排切換成不同的模式，在該模式中會在計算出的重試延遲中額外加上 10 秒的延遲。</span><span class="sxs-lookup"><span data-stu-id="00d33-511">The occurrence of a **ServerBusyException** also causes Service Bus to switch to a different mode, in which an extra 10-second delay is added to the computed retry delays.</span></span> <span data-ttu-id="00d33-512">此模式會在短時間後重設。</span><span class="sxs-lookup"><span data-stu-id="00d33-512">This mode is reset after a short period.</span></span>

<span data-ttu-id="00d33-513">從服務匯流排傳回的例外狀況會公開 **IsTransient** 屬性，以表示用戶端是否應該重試作業。</span><span class="sxs-lookup"><span data-stu-id="00d33-513">The exceptions returned from Service Bus expose the **IsTransient** property that indicates if the client should retry the operation.</span></span> <span data-ttu-id="00d33-514">內建的 **RetryExponential** 原則依賴 **MessagingException** 類別中的 **IsTransient** 屬性，也就是所有服務匯流排例外狀況的基底類別。</span><span class="sxs-lookup"><span data-stu-id="00d33-514">The built-in **RetryExponential** policy relies on the **IsTransient** property in the **MessagingException** class, which is the base class for all Service Bus exceptions.</span></span> <span data-ttu-id="00d33-515">如果您建立 **RetryPolicy** 基底類別的自訂實作，您可以使用例外狀況類型和 **IsTransient** 屬性的組合，藉此更細微地控制重試動作。</span><span class="sxs-lookup"><span data-stu-id="00d33-515">If you create custom implementations of the **RetryPolicy** base class you could use a combination of the exception type and the **IsTransient** property to provide more fine-grained control over retry actions.</span></span> <span data-ttu-id="00d33-516">例如，您可能會偵測到 **QuotaExceededException** ，並先採取動作清空佇列，再重試將訊息傳送到佇列。</span><span class="sxs-lookup"><span data-stu-id="00d33-516">For example, you could detect a **QuotaExceededException** and take action to drain the queue before retrying sending a message to it.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="00d33-517">原則組態</span><span class="sxs-lookup"><span data-stu-id="00d33-517">Policy configuration</span></span>
<span data-ttu-id="00d33-518">重試原則是以程式設計的方式設定的，並可設為 **NamespaceManager** 與 **MessagingFactory** 兩者的預設原則，或為每個傳訊用戶端個別設為預設原則。</span><span class="sxs-lookup"><span data-stu-id="00d33-518">Retry policies are set programmatically, and can be set as a default policy for a **NamespaceManager** and for a **MessagingFactory**, or individually for each messaging client.</span></span> <span data-ttu-id="00d33-519">若要設定傳訊工作階段的預設重試原則，請設定 **NamespaceManager** 的 **RetryPolicy**。</span><span class="sxs-lookup"><span data-stu-id="00d33-519">To set the default retry policy for a messaging session you set the **RetryPolicy** of the **NamespaceManager**.</span></span>

    namespaceManager.Settings.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                                                 maxBackoff: TimeSpan.FromSeconds(30),
                                                                 maxRetryCount: 3);

<span data-ttu-id="00d33-520">若要為所有從傳訊處理站建立的用戶端設定預設重試原則，請設定 **MessagingFactory** 的 **RetryPolicy**。</span><span class="sxs-lookup"><span data-stu-id="00d33-520">To set the default retry policy for all clients created from a messaging factory, you set the **RetryPolicy** of the **MessagingFactory**.</span></span>

    messagingFactory.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                                        maxBackoff: TimeSpan.FromSeconds(30),
                                                        maxRetryCount: 3);

<span data-ttu-id="00d33-521">若要為傳訊用戶端設定重試原則，或覆寫其預設原則，請使用所需原則類別的執行個體設定其 **RetryPolicy** 屬性：</span><span class="sxs-lookup"><span data-stu-id="00d33-521">To set the retry policy for a messaging client, or to override its default policy, you set its **RetryPolicy** property using an instance of the required policy class:</span></span>

```csharp
client.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                            maxBackoff: TimeSpan.FromSeconds(30),
                                            maxRetryCount: 3);
```

<span data-ttu-id="00d33-522">無法在個別的作業層級設定重試原則。</span><span class="sxs-lookup"><span data-stu-id="00d33-522">The retry policy cannot be set at the individual operation level.</span></span> <span data-ttu-id="00d33-523">它適用於傳訊用戶端的所有作業。</span><span class="sxs-lookup"><span data-stu-id="00d33-523">It applies to all operations for the messaging client.</span></span>
<span data-ttu-id="00d33-524">下表顯示內建重試原則的預設設定。</span><span class="sxs-lookup"><span data-stu-id="00d33-524">The following table shows the default settings for the built-in retry policy.</span></span>

![重試指引表格](./images/retry-service-specific/RetryServiceSpecificGuidanceTable7.png)

### <a name="retry-usage-guidance"></a><span data-ttu-id="00d33-526">重試使用指引</span><span class="sxs-lookup"><span data-stu-id="00d33-526">Retry usage guidance</span></span>
<span data-ttu-id="00d33-527">使用服務匯流排時請考慮下列指引：</span><span class="sxs-lookup"><span data-stu-id="00d33-527">Consider the following guidelines when using Service Bus:</span></span>

* <span data-ttu-id="00d33-528">當使用內建 **RetryExponential** 實作時，若原則回應伺服器忙碌例外狀況並自動切換到適當的重試模式時，請不要實作後援作業。</span><span class="sxs-lookup"><span data-stu-id="00d33-528">When using the built-in **RetryExponential** implementation, do not implement a fallback operation as the policy reacts to Server Busy exceptions and automatically switches to an appropriate retry mode.</span></span>
* <span data-ttu-id="00d33-529">服務匯流排支援名為「配對命名空間」的功能，如果主要命名空間中的佇列失敗，此功能會實作自動容錯移轉到備份的佇列。</span><span class="sxs-lookup"><span data-stu-id="00d33-529">Service Bus supports a feature called Paired Namespaces, which implements automatic failover to a backup queue in a separate namespace if the queue in the primary namespace fails.</span></span> <span data-ttu-id="00d33-530">主要佇列復原時，次要佇列中的訊息會傳回主要佇列。</span><span class="sxs-lookup"><span data-stu-id="00d33-530">Messages from the secondary queue can be sent back to the primary queue when it recovers.</span></span> <span data-ttu-id="00d33-531">此功能有助於處理暫時性錯誤。</span><span class="sxs-lookup"><span data-stu-id="00d33-531">This feature helps to address transient failures.</span></span> <span data-ttu-id="00d33-532">如需詳細資訊，請參閱 [非同步傳訊模式和高可用性](http://msdn.microsoft.com/library/azure/dn292562.aspx)。</span><span class="sxs-lookup"><span data-stu-id="00d33-532">For more information, see [Asynchronous Messaging Patterns and High Availability](http://msdn.microsoft.com/library/azure/dn292562.aspx).</span></span>

<span data-ttu-id="00d33-533">請考慮從重試作業的下列設定開始。</span><span class="sxs-lookup"><span data-stu-id="00d33-533">Consider starting with following settings for retrying operations.</span></span> <span data-ttu-id="00d33-534">這些是一般用途設定，您應該監視作業並微調其值以符合您自己的需求。</span><span class="sxs-lookup"><span data-stu-id="00d33-534">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

![重試指引表格](./images/retry-service-specific/RetryServiceSpecificGuidanceTable8.png)

### <a name="telemetry"></a><span data-ttu-id="00d33-536">遙測</span><span class="sxs-lookup"><span data-stu-id="00d33-536">Telemetry</span></span>
<span data-ttu-id="00d33-537">服務匯流排使用 **EventSource**，將重試記錄成 ETW 事件。</span><span class="sxs-lookup"><span data-stu-id="00d33-537">Service Bus logs retries as ETW events using an **EventSource**.</span></span> <span data-ttu-id="00d33-538">您必須將 **EventListener** 附加到事件來源，以擷取事件並在效能檢視器中檢視事件。</span><span class="sxs-lookup"><span data-stu-id="00d33-538">You must attach an **EventListener** to the event source to capture the events and view them in Performance Viewer, or write them to a suitable destination log.</span></span> <span data-ttu-id="00d33-539">您可以使用 [語意記錄應用程式區塊](http://msdn.microsoft.com/library/dn775006.aspx) 來達成。</span><span class="sxs-lookup"><span data-stu-id="00d33-539">You could use the [Semantic Logging Application Block](http://msdn.microsoft.com/library/dn775006.aspx) to do this.</span></span> <span data-ttu-id="00d33-540">重試事件的格式如下：</span><span class="sxs-lookup"><span data-stu-id="00d33-540">The retry events are of the following form:</span></span>

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

### <a name="examples"></a><span data-ttu-id="00d33-541">範例</span><span class="sxs-lookup"><span data-stu-id="00d33-541">Examples</span></span>
<span data-ttu-id="00d33-542">下列範例程式碼顯示如何為下列項目設定重試原則：</span><span class="sxs-lookup"><span data-stu-id="00d33-542">The following code example shows how to set the retry policy for:</span></span>

* <span data-ttu-id="00d33-543">命名空間管理員。</span><span class="sxs-lookup"><span data-stu-id="00d33-543">A namespace manager.</span></span> <span data-ttu-id="00d33-544">此原則適用於該管理員上的所有作業，而且無法針對個別作業覆寫。</span><span class="sxs-lookup"><span data-stu-id="00d33-544">The policy applies to all operations on that manager, and cannot be overridden for individual operations.</span></span>
* <span data-ttu-id="00d33-545">傳訊處理站。</span><span class="sxs-lookup"><span data-stu-id="00d33-545">A messaging factory.</span></span> <span data-ttu-id="00d33-546">此原則適用於從該處理站建立的所有用戶端，當建立個別的用戶端時無法覆寫此原則。</span><span class="sxs-lookup"><span data-stu-id="00d33-546">The policy applies to all clients created from that factory, and cannot be overridden when creating individual clients.</span></span>
* <span data-ttu-id="00d33-547">個別的傳訊用戶端。</span><span class="sxs-lookup"><span data-stu-id="00d33-547">An individual messaging client.</span></span> <span data-ttu-id="00d33-548">建立用戶端之後，您可以為該用戶端設定重試原則。</span><span class="sxs-lookup"><span data-stu-id="00d33-548">After a client has been created, you can set the retry policy for that client.</span></span> <span data-ttu-id="00d33-549">此原則適用於該用戶端上的所有作業。</span><span class="sxs-lookup"><span data-stu-id="00d33-549">The policy applies to all operations on that client.</span></span>

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

### <a name="more-information"></a><span data-ttu-id="00d33-550">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="00d33-550">More information</span></span>
* [<span data-ttu-id="00d33-551">非同步傳訊模式和高可用性</span><span class="sxs-lookup"><span data-stu-id="00d33-551">Asynchronous Messaging Patterns and High Availability</span></span>](http://msdn.microsoft.com/library/azure/dn292562.aspx)

## <a name="azure-redis-cache-retry-guidelines"></a><span data-ttu-id="00d33-552">Azure Redis 快取重試指引</span><span class="sxs-lookup"><span data-stu-id="00d33-552">Azure Redis Cache retry guidelines</span></span>
<span data-ttu-id="00d33-553">Azure Redis 快取是快速資料存取與低延遲性的快取服務，以普遍的開放原始碼 Redis 快取為基礎。</span><span class="sxs-lookup"><span data-stu-id="00d33-553">Azure Redis Cache is a fast data access and low latency cache service based on the popular open source Redis Cache.</span></span> <span data-ttu-id="00d33-554">它很安全，且由 Microsoft 管理，可使用 Azure 的任何應用程式存取。</span><span class="sxs-lookup"><span data-stu-id="00d33-554">It is secure, managed by Microsoft, and is accessible from any application in Azure.</span></span>

<span data-ttu-id="00d33-555">本節中的指引以使用 StackExchange.Redis 用戶端來存取快取為基礎。</span><span class="sxs-lookup"><span data-stu-id="00d33-555">The guidance in this section is based on using the StackExchange.Redis client to access the cache.</span></span> <span data-ttu-id="00d33-556">有關其他適合的用戶端清單，請參閱 [Redis 網站](http://redis.io/clients)，這些用戶端可能有不同的重試機制。</span><span class="sxs-lookup"><span data-stu-id="00d33-556">A list of other suitable clients can be found on the [Redis website](http://redis.io/clients), and these may have different retry mechanisms.</span></span>

<span data-ttu-id="00d33-557">請注意 StackExchange.Redis 用戶端透過單一連接使用多工。</span><span class="sxs-lookup"><span data-stu-id="00d33-557">Note that the StackExchange.Redis client uses multiplexing through a single connection.</span></span> <span data-ttu-id="00d33-558">建議用法是在應用程式啟動時建立用戶端的執行個體，並對快取的所有作業使用此執行個體。</span><span class="sxs-lookup"><span data-stu-id="00d33-558">The recommended usage is to create an instance of the client at application startup and use this instance for all operations against the cache.</span></span> <span data-ttu-id="00d33-559">基於這個理由，對快取只連接一次，且因此本節中指引的所有一切都與此初始連接的重試原則有關，不適用於存取快取的每個作業。</span><span class="sxs-lookup"><span data-stu-id="00d33-559">For this reason, the connection to the cache is made only once, and so all of the guidance in this section is related to the retry policy for this initial connection—and not for each operation that accesses the cache.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="00d33-560">重試機制</span><span class="sxs-lookup"><span data-stu-id="00d33-560">Retry mechanism</span></span>
<span data-ttu-id="00d33-561">StackExchange.Redis 用戶端會使用透過一組選項設定的連線管理員類別，包括：</span><span class="sxs-lookup"><span data-stu-id="00d33-561">The StackExchange.Redis client uses a connection manager class that is configured through a set of options, incuding:</span></span>

- <span data-ttu-id="00d33-562">**ConnectRetry**。</span><span class="sxs-lookup"><span data-stu-id="00d33-562">**ConnectRetry**.</span></span> <span data-ttu-id="00d33-563">重試快取失敗連接的次數。</span><span class="sxs-lookup"><span data-stu-id="00d33-563">The number of times a failed connection to the cache will be retried.</span></span>
- <span data-ttu-id="00d33-564">**ReconnectRetryPolicy**。</span><span class="sxs-lookup"><span data-stu-id="00d33-564">**ReconnectRetryPolicy**.</span></span> <span data-ttu-id="00d33-565">要使用的重試策略。</span><span class="sxs-lookup"><span data-stu-id="00d33-565">The retry strategy to use.</span></span>
- <span data-ttu-id="00d33-566">**ConnectTimeout**。</span><span class="sxs-lookup"><span data-stu-id="00d33-566">**ConnectTimeout**.</span></span> <span data-ttu-id="00d33-567">最長等待時間，以毫秒為單位。</span><span class="sxs-lookup"><span data-stu-id="00d33-567">The maximum waiting time in milliseconds.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="00d33-568">原則組態</span><span class="sxs-lookup"><span data-stu-id="00d33-568">Policy configuration</span></span>
<span data-ttu-id="00d33-569">重試原則是以程式設計的方式設定的，做法是先設定用戶端的選項，再連接至快取。</span><span class="sxs-lookup"><span data-stu-id="00d33-569">Retry policies are configured programmatically by setting the options for the client before connecting to the cache.</span></span> <span data-ttu-id="00d33-570">做法是建立 **ConfigurationOptions** 類別的執行個體、填入其屬性，並將它傳遞給 **Connect** 方法。</span><span class="sxs-lookup"><span data-stu-id="00d33-570">This can be done by creating an instance of the **ConfigurationOptions** class, populating its properties, and passing it to the **Connect** method.</span></span>

<span data-ttu-id="00d33-571">內建類別支援線性 (固定) 延遲重試間隔以及隨機指數輪詢重試間隔。</span><span class="sxs-lookup"><span data-stu-id="00d33-571">The built-in classes support linear (constant) delay and exponential backoff with randomized retry intervals.</span></span> <span data-ttu-id="00d33-572">您也可以藉由實作 **IReconnectRetryPolicy** 介面建立自訂重試原則。</span><span class="sxs-lookup"><span data-stu-id="00d33-572">You can also create a custom retry policy by implementing the **IReconnectRetryPolicy** interface.</span></span>

<span data-ttu-id="00d33-573">下列範例使用指數輪詢設定重試策略。</span><span class="sxs-lookup"><span data-stu-id="00d33-573">The following example configures a retry strategy using exponential backoff.</span></span>

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

<span data-ttu-id="00d33-574">或者，您可以將選項指定為字串，並將此字串傳遞到 **Connect** 方法。</span><span class="sxs-lookup"><span data-stu-id="00d33-574">Alternatively, you can specify the options as a string, and pass this to the **Connect** method.</span></span> <span data-ttu-id="00d33-575">請注意，**ReconnectRetryPolicy** 屬性不能這樣設定，只能透過程式碼。</span><span class="sxs-lookup"><span data-stu-id="00d33-575">Note that the **ReconnectRetryPolicy** property cannot be set this way, only through code.</span></span>

```csharp
    var options = "localhost,connectRetry=3,connectTimeout=2000";
    ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

<span data-ttu-id="00d33-576">連線至快取時，您也可以直接指定選項。</span><span class="sxs-lookup"><span data-stu-id="00d33-576">You can also specify options directly when you connect to the cache.</span></span>

```csharp
var conn = ConnectionMultiplexer.Connect("redis0:6380,redis1:6380,connectRetry=3");
```

<span data-ttu-id="00d33-577">如需詳細資訊，請參閱 StackExchange.Redis 文件的 [Stack Exchange.Redis 設定](https://stackexchange.github.io/StackExchange.Redis/Configuration) (英文)。</span><span class="sxs-lookup"><span data-stu-id="00d33-577">For more information, see [Stack Exchange Redis Configuration](https://stackexchange.github.io/StackExchange.Redis/Configuration) in the StackExchange.Redis documentation.</span></span>

<span data-ttu-id="00d33-578">下表顯示內建重試原則的預設設定。</span><span class="sxs-lookup"><span data-stu-id="00d33-578">The following table shows the default settings for the built-in retry policy.</span></span>

| <span data-ttu-id="00d33-579">**內容**</span><span class="sxs-lookup"><span data-stu-id="00d33-579">**Context**</span></span> | <span data-ttu-id="00d33-580">**設定**</span><span class="sxs-lookup"><span data-stu-id="00d33-580">**Setting**</span></span> | <span data-ttu-id="00d33-581">**預設值**</span><span class="sxs-lookup"><span data-stu-id="00d33-581">**Default value**</span></span><br /><span data-ttu-id="00d33-582">(v 1.2.2)</span><span class="sxs-lookup"><span data-stu-id="00d33-582">(v 1.2.2)</span></span> | <span data-ttu-id="00d33-583">**意義**</span><span class="sxs-lookup"><span data-stu-id="00d33-583">**Meaning**</span></span> |
| --- | --- | --- | --- |
| <span data-ttu-id="00d33-584">ConfigurationOptions</span><span class="sxs-lookup"><span data-stu-id="00d33-584">ConfigurationOptions</span></span> |<span data-ttu-id="00d33-585">ConnectRetry</span><span class="sxs-lookup"><span data-stu-id="00d33-585">ConnectRetry</span></span><br /><br /><span data-ttu-id="00d33-586">ConnectTimeout</span><span class="sxs-lookup"><span data-stu-id="00d33-586">ConnectTimeout</span></span><br /><br /><span data-ttu-id="00d33-587">SyncTimeout</span><span class="sxs-lookup"><span data-stu-id="00d33-587">SyncTimeout</span></span><br /><br /><span data-ttu-id="00d33-588">ReconnectRetryPolicy</span><span class="sxs-lookup"><span data-stu-id="00d33-588">ReconnectRetryPolicy</span></span> |<span data-ttu-id="00d33-589">3</span><span class="sxs-lookup"><span data-stu-id="00d33-589">3</span></span><br /><br /><span data-ttu-id="00d33-590">最多 5000 毫秒加上 SyncTimeout</span><span class="sxs-lookup"><span data-stu-id="00d33-590">Maximum 5000 ms plus SyncTimeout</span></span><br /><span data-ttu-id="00d33-591">1000</span><span class="sxs-lookup"><span data-stu-id="00d33-591">1000</span></span><br /><br /><span data-ttu-id="00d33-592">LinearRetry 5000 毫秒</span><span class="sxs-lookup"><span data-stu-id="00d33-592">LinearRetry 5000 ms</span></span> |<span data-ttu-id="00d33-593">初始連線作業的重複連線嘗試次數。</span><span class="sxs-lookup"><span data-stu-id="00d33-593">The number of times to repeat connect attempts during the initial connection operation.</span></span><br /><span data-ttu-id="00d33-594">連線作業的逾時 (毫秒)。</span><span class="sxs-lookup"><span data-stu-id="00d33-594">Timeout (ms) for connect operations.</span></span> <span data-ttu-id="00d33-595">非重試嘗試之間的延遲。</span><span class="sxs-lookup"><span data-stu-id="00d33-595">Not a delay between retry attempts.</span></span><br /><span data-ttu-id="00d33-596">允許同步作業的時間 (毫秒)。</span><span class="sxs-lookup"><span data-stu-id="00d33-596">Time (ms) to allow for synchronous operations.</span></span><br /><br /><span data-ttu-id="00d33-597">每 5000 毫秒重試一次。</span><span class="sxs-lookup"><span data-stu-id="00d33-597">Retry every 5000 ms.</span></span>|

> [!NOTE]
> <span data-ttu-id="00d33-598">針對同步作業，可以將 `SyncTimeout` 加入端對端延遲，但設定值過低可能會導致過多逾時。</span><span class="sxs-lookup"><span data-stu-id="00d33-598">For synchronous operations, `SyncTimeout` can add to the end-to-end latency, but setting the value too low can cause excessive timeouts.</span></span> <span data-ttu-id="00d33-599">請參閱[如何針對 Azure Redis 快取進行疑難排解][redis-cache-troubleshoot]。</span><span class="sxs-lookup"><span data-stu-id="00d33-599">See [How to troubleshoot Azure Redis Cache][redis-cache-troubleshoot].</span></span> <span data-ttu-id="00d33-600">一般來說，請避免使用同步作業，改為使用非同步作業。</span><span class="sxs-lookup"><span data-stu-id="00d33-600">In general, avoid using synchronous operations, and use asynchronous operations instead.</span></span> <span data-ttu-id="00d33-601">如需詳細資訊，請參閱 [管線與多工器](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md)。</span><span class="sxs-lookup"><span data-stu-id="00d33-601">For more information see [Pipelines and Multiplexers](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md).</span></span>
>
>

### <a name="retry-usage-guidance"></a><span data-ttu-id="00d33-602">重試使用指引</span><span class="sxs-lookup"><span data-stu-id="00d33-602">Retry usage guidance</span></span>
<span data-ttu-id="00d33-603">使用 Azure Redis 快取時請考慮下列指引：</span><span class="sxs-lookup"><span data-stu-id="00d33-603">Consider the following guidelines when using Azure Redis Cache:</span></span>

* <span data-ttu-id="00d33-604">StackExchange Redis 用戶端會管理自己的重試，但只有在應用程式第一次啟動時對快取建立連接時才會。</span><span class="sxs-lookup"><span data-stu-id="00d33-604">The StackExchange Redis client manages its own retries, but only when establishing a connection to the cache when the application first starts.</span></span> <span data-ttu-id="00d33-605">您可以設定連線逾時、重試嘗試次數、重試之間的間隔時間來建立這個連線，但重試原則不適用於對快取的作業。</span><span class="sxs-lookup"><span data-stu-id="00d33-605">You can configure the connection timeout, the number of retry attempts, and the time between retries to establish this connection, but the retry policy does not apply to operations against the cache.</span></span>
* <span data-ttu-id="00d33-606">請不要使用大量的重試嘗試，而是考慮改為存取原始的資料來源來回復。</span><span class="sxs-lookup"><span data-stu-id="00d33-606">Instead of using a large number of retry attempts, consider falling back by accessing the original data source instead.</span></span>

### <a name="telemetry"></a><span data-ttu-id="00d33-607">遙測</span><span class="sxs-lookup"><span data-stu-id="00d33-607">Telemetry</span></span>
<span data-ttu-id="00d33-608">您可以使用 **TextWriter**收集連接 (而非其他作業) 的資訊。</span><span class="sxs-lookup"><span data-stu-id="00d33-608">You can collect information about connections (but not other operations) using a **TextWriter**.</span></span>

```csharp
var writer = new StringWriter();
...
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

<span data-ttu-id="00d33-609">這產生的輸出範例如下所示。</span><span class="sxs-lookup"><span data-stu-id="00d33-609">An example of the output this generates is shown below.</span></span>

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

### <a name="examples"></a><span data-ttu-id="00d33-610">範例</span><span class="sxs-lookup"><span data-stu-id="00d33-610">Examples</span></span>
<span data-ttu-id="00d33-611">下列程式碼範例會設定初始化 StackExchange.Redis 用戶端時的重試之間的固定 (線性) 延遲。</span><span class="sxs-lookup"><span data-stu-id="00d33-611">The following code example configures a constant (linear) delay between retries when initializing the StackExchange.Redis client.</span></span> <span data-ttu-id="00d33-612">此範例示範如何使用 **ConfigurationOptions** 執行個體來設定組態。</span><span class="sxs-lookup"><span data-stu-id="00d33-612">This example shows how to set the configuration using a **ConfigurationOptions** instance.</span></span>

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

<span data-ttu-id="00d33-613">此範例透過將選項指定為字串來設定組態。</span><span class="sxs-lookup"><span data-stu-id="00d33-613">The next example sets the configuration by specifying the options as a string.</span></span> <span data-ttu-id="00d33-614">連線逾時是等待連線到快取的時間上限，不是重試嘗試之間的延遲。</span><span class="sxs-lookup"><span data-stu-id="00d33-614">The connection timeout is the maximum period of time to wait for a connection to the cache, not the delay between retry attempts.</span></span> <span data-ttu-id="00d33-615">請注意，**ReconnectRetryPolicy** 屬性只能透過程式碼設定。</span><span class="sxs-lookup"><span data-stu-id="00d33-615">Note that the **ReconnectRetryPolicy** property can only be set by code.</span></span>

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

<span data-ttu-id="00d33-616">如需其他範例，請參閱專案網站上的 [組態](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md#configuration) 。</span><span class="sxs-lookup"><span data-stu-id="00d33-616">For more examples, see [Configuration](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md#configuration) on the project website.</span></span>

### <a name="more-information"></a><span data-ttu-id="00d33-617">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="00d33-617">More information</span></span>
* [<span data-ttu-id="00d33-618">Redis 網站</span><span class="sxs-lookup"><span data-stu-id="00d33-618">Redis website</span></span>](http://redis.io/)

## <a name="documentdb-api-retry-guidelines"></a><span data-ttu-id="00d33-619">DocumentDB API 重試指引</span><span class="sxs-lookup"><span data-stu-id="00d33-619">DocumentDB API retry guidelines</span></span>

<span data-ttu-id="00d33-620">Cosmos DB 是完全受管理的多模型資料庫，由於使用 [DocumentDB API][documentdb-api]，可支援無結構描述的 JSON 資料。</span><span class="sxs-lookup"><span data-stu-id="00d33-620">Cosmos DB is a fully-managed multi-model database that supports schema-less JSON data by using the [DocumentDB API][documentdb-api].</span></span> <span data-ttu-id="00d33-621">它提供可設定且可靠的效能、原生的 JavaScript 交易處理，而且是針對可彈性延展的雲端建置而成。</span><span class="sxs-lookup"><span data-stu-id="00d33-621">It offers configurable and reliable performance, native JavaScript transactional processing, and is built for the cloud with elastic scale.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="00d33-622">重試機制</span><span class="sxs-lookup"><span data-stu-id="00d33-622">Retry mechanism</span></span>
<span data-ttu-id="00d33-623">`DocumentClient` 類別會自動重試失敗的嘗試。</span><span class="sxs-lookup"><span data-stu-id="00d33-623">The `DocumentClient` class automatically retries failed attempts.</span></span> <span data-ttu-id="00d33-624">若要設定重試次數和等待時間上限，請設定 [ConnectionPolicy.RetryOptions]。</span><span class="sxs-lookup"><span data-stu-id="00d33-624">To set the number of retries and the maximum wait time, configure [ConnectionPolicy.RetryOptions].</span></span> <span data-ttu-id="00d33-625">用戶端引發的例外狀況可能超出重試原則，或不是暫時性的錯誤。</span><span class="sxs-lookup"><span data-stu-id="00d33-625">Exceptions that the client raises are either beyond the retry policy or are not transient errors.</span></span>

<span data-ttu-id="00d33-626">如果 Cosmos DB 將用戶端節流，它會傳回 HTTP 429 錯誤。</span><span class="sxs-lookup"><span data-stu-id="00d33-626">If Cosmos DB throttles the client, it returns an HTTP 429 error.</span></span> <span data-ttu-id="00d33-627">檢查 `DocumentClientException` 中的狀態碼。</span><span class="sxs-lookup"><span data-stu-id="00d33-627">Check the status code in the `DocumentClientException`.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="00d33-628">原則組態</span><span class="sxs-lookup"><span data-stu-id="00d33-628">Policy configuration</span></span>
<span data-ttu-id="00d33-629">下表顯示 `RetryOptions` 類別的預設設定。</span><span class="sxs-lookup"><span data-stu-id="00d33-629">The following table shows the default settings for the `RetryOptions` class.</span></span>

| <span data-ttu-id="00d33-630">設定</span><span class="sxs-lookup"><span data-stu-id="00d33-630">Setting</span></span> | <span data-ttu-id="00d33-631">預設值</span><span class="sxs-lookup"><span data-stu-id="00d33-631">Default value</span></span> | <span data-ttu-id="00d33-632">說明</span><span class="sxs-lookup"><span data-stu-id="00d33-632">Description</span></span> |
| --- | --- | --- |
| <span data-ttu-id="00d33-633">MaxRetryAttemptsOnThrottledRequests</span><span class="sxs-lookup"><span data-stu-id="00d33-633">MaxRetryAttemptsOnThrottledRequests</span></span> |<span data-ttu-id="00d33-634">9</span><span class="sxs-lookup"><span data-stu-id="00d33-634">9</span></span> |<span data-ttu-id="00d33-635">如果要求因為 Cosmos DB 在用戶端上套用速率限制而失敗，重試次數的上限。</span><span class="sxs-lookup"><span data-stu-id="00d33-635">The maximum number of retries if the request fails because Cosmos DB applied rate limiting on the client.</span></span> |
| <span data-ttu-id="00d33-636">MaxRetryWaitTimeInSeconds</span><span class="sxs-lookup"><span data-stu-id="00d33-636">MaxRetryWaitTimeInSeconds</span></span> |<span data-ttu-id="00d33-637">30</span><span class="sxs-lookup"><span data-stu-id="00d33-637">30</span></span> |<span data-ttu-id="00d33-638">最大重試時間 (秒)。</span><span class="sxs-lookup"><span data-stu-id="00d33-638">The maximum retry time in seconds.</span></span> |

### <a name="example"></a><span data-ttu-id="00d33-639">範例</span><span class="sxs-lookup"><span data-stu-id="00d33-639">Example</span></span>
```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); ;
var options = client.ConnectionPolicy.RetryOptions;
options.MaxRetryAttemptsOnThrottledRequests = 5;
options.MaxRetryWaitTimeInSeconds = 15;
```

### <a name="telemetry"></a><span data-ttu-id="00d33-640">遙測</span><span class="sxs-lookup"><span data-stu-id="00d33-640">Telemetry</span></span>
<span data-ttu-id="00d33-641">系統會透過 .NET **TraceSource**將重試嘗試記錄成非結構化的追蹤訊息。</span><span class="sxs-lookup"><span data-stu-id="00d33-641">Retry attempts are logged as unstructured trace messages through a .NET **TraceSource**.</span></span> <span data-ttu-id="00d33-642">您必須設定 **TraceListener** 來擷取事件，並將事件寫入適當的目的地記錄中。</span><span class="sxs-lookup"><span data-stu-id="00d33-642">You must configure a **TraceListener** to capture the events and write them to a suitable destination log.</span></span>

<span data-ttu-id="00d33-643">例如，如果您將下列新增至您的 App.config 檔案，則會在與可執行檔相同位置的文字檔案中產生追蹤：</span><span class="sxs-lookup"><span data-stu-id="00d33-643">For example, if you add the following to your App.config file, traces will be generated in a text file in the same location as the executable:</span></span>

```
<configuration>
  <system.diagnostics>
    <switches>
      <add name="SourceSwitch" value="Verbose"/>
    </switches>
    <sources>
      <source name="DocDBTrace" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" >
        <listeners>
          <add name="MyTextListener" type="System.Diagnostics.TextWriterTraceListener" traceOutputOptions="DateTime,ProcessId,ThreadId" initializeData="DocumentDBTrace.txt"></add>
        </listeners>
      </source>
    </sources>
  </system.diagnostics>
</configuration>
```


## <a name="azure-search-retry-guidelines"></a><span data-ttu-id="00d33-644">Azure 搜尋服務重試指引</span><span class="sxs-lookup"><span data-stu-id="00d33-644">Azure Search retry guidelines</span></span>
<span data-ttu-id="00d33-645">Azure 搜尋服務可用來在網站或應用程式中加入強大且複雜的搜尋功能、快速輕鬆地微調搜尋結果，並建構豐富且微調的排名模型。</span><span class="sxs-lookup"><span data-stu-id="00d33-645">Azure Search can be used to add powerful and sophisticated search capabilities to a website or application, quickly and easily tune search results, and construct rich and fine-tuned ranking models.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="00d33-646">重試機制</span><span class="sxs-lookup"><span data-stu-id="00d33-646">Retry mechanism</span></span>
<span data-ttu-id="00d33-647">Azure 搜尋服務 SDK 中的重試行為是由 [SearchServiceClient] 和 [SearchIndexClient] 類別上的 `SetRetryPolicy` 方法控制。</span><span class="sxs-lookup"><span data-stu-id="00d33-647">Retry behavior in the Azure Search SDK is controlled by the `SetRetryPolicy` method on the [SearchServiceClient] and [SearchIndexClient] classes.</span></span> <span data-ttu-id="00d33-648">當 Azure 搜尋服務傳回 5xx 或 408 (要求逾時) 回應時，預設原則會以指數輪詢重試。</span><span class="sxs-lookup"><span data-stu-id="00d33-648">The default policy retries with exponential backoff when Azure Search returns a 5xx or 408 (Request Timeout) response.</span></span>

### <a name="telemetry"></a><span data-ttu-id="00d33-649">遙測</span><span class="sxs-lookup"><span data-stu-id="00d33-649">Telemetry</span></span>
<span data-ttu-id="00d33-650">使用 ETW 追蹤或註冊自訂的追蹤提供者。</span><span class="sxs-lookup"><span data-stu-id="00d33-650">Trace with ETW or by registering a custom trace provider.</span></span> <span data-ttu-id="00d33-651">如需詳細資訊，請參閱 [AutoRest 文件][autorest]。</span><span class="sxs-lookup"><span data-stu-id="00d33-651">For more information, see the [AutoRest documentation][autorest].</span></span>

## <a name="azure-active-directory-retry-guidelines"></a><span data-ttu-id="00d33-652">Azure Active Directory 重試指引</span><span class="sxs-lookup"><span data-stu-id="00d33-652">Azure Active Directory retry guidelines</span></span>
<span data-ttu-id="00d33-653">Azure Active Directory (Azure AD) 是完整的身分識別與存取管理雲端解決方案，結合了核心目錄服務、進階身分識別控管、安全性，以及應用程式存取管理。</span><span class="sxs-lookup"><span data-stu-id="00d33-653">Azure Active Directory (Azure AD) is a comprehensive identity and access management cloud solution that combines core directory services, advanced identity governance, security, and application access management.</span></span> <span data-ttu-id="00d33-654">Azure AD 也提供給開發人員一個身分識別管理平台，以根據集中化的原則和規則將存取控制傳遞給其應用程式。</span><span class="sxs-lookup"><span data-stu-id="00d33-654">Azure AD also offers developers an identity management platform to deliver access control to their applications, based on centralized policy and rules.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="00d33-655">重試機制</span><span class="sxs-lookup"><span data-stu-id="00d33-655">Retry mechanism</span></span>
<span data-ttu-id="00d33-656">Active Directory 驗證程式庫 (ADAL) 中有內建的 Azure Active Directory 重試機制。</span><span class="sxs-lookup"><span data-stu-id="00d33-656">There is a built-in retry mechanism for Azure Active Directory in the Active Directory Authentication Library (ADAL).</span></span> <span data-ttu-id="00d33-657">為了避免非預期的鎖定，建議第三方廠商程式庫和應用程式程式碼「不要」重試失敗的連線，但允許 ADAL 處理重試。</span><span class="sxs-lookup"><span data-stu-id="00d33-657">To avoid unexpected lockouts, we recommend that third party libraries and application code do *not* retry failed connections, but allow ADAL to handle retries.</span></span> 

### <a name="retry-usage-guidance"></a><span data-ttu-id="00d33-658">重試使用指引</span><span class="sxs-lookup"><span data-stu-id="00d33-658">Retry usage guidance</span></span>
<span data-ttu-id="00d33-659">使用 Azure Active Directory 時請考慮下列指引：</span><span class="sxs-lookup"><span data-stu-id="00d33-659">Consider the following guidelines when using Azure Active Directory:</span></span>

* <span data-ttu-id="00d33-660">可能的話，使用 ADAL 程式庫和內建支援進行重試。</span><span class="sxs-lookup"><span data-stu-id="00d33-660">When possible, use the ADAL library and the built-in support for retries.</span></span>
* <span data-ttu-id="00d33-661">如果您為 Azure Active Directory 使用 REST API，則只有在下列狀況才應重試作業：結果是 5xx 範圍中的錯誤 (例如 500 內部伺服器錯誤、502 錯誤的閘道、503 服務無法使用，以及 504 閘道器逾時)。</span><span class="sxs-lookup"><span data-stu-id="00d33-661">If you are using the REST API for Azure Active Directory, you should retry the operation only if the result is an error in the 5xx range (such as 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable, and 504 Gateway Timeout).</span></span> <span data-ttu-id="00d33-662">若為任何其他錯誤，請勿重試。</span><span class="sxs-lookup"><span data-stu-id="00d33-662">Do not retry for any other errors.</span></span>
* <span data-ttu-id="00d33-663">建議將指數退避原則搭配 Azure Active Directory 用於批次案例中。</span><span class="sxs-lookup"><span data-stu-id="00d33-663">An exponential back-off policy is recommended for use in batch scenarios with Azure Active Directory.</span></span>

<span data-ttu-id="00d33-664">請考慮讓重試作業從下列設定開始。</span><span class="sxs-lookup"><span data-stu-id="00d33-664">Consider starting with the following settings for retrying operations.</span></span> <span data-ttu-id="00d33-665">這些是一般用途設定，您應該監視作業並微調其值以符合您自己的需求。</span><span class="sxs-lookup"><span data-stu-id="00d33-665">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="00d33-666">**內容**</span><span class="sxs-lookup"><span data-stu-id="00d33-666">**Context**</span></span> | <span data-ttu-id="00d33-667">**範例目標 E2E<br />最大延遲**</span><span class="sxs-lookup"><span data-stu-id="00d33-667">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="00d33-668">**重試策略**</span><span class="sxs-lookup"><span data-stu-id="00d33-668">**Retry strategy**</span></span> | <span data-ttu-id="00d33-669">**設定**</span><span class="sxs-lookup"><span data-stu-id="00d33-669">**Settings**</span></span> | <span data-ttu-id="00d33-670">**值**</span><span class="sxs-lookup"><span data-stu-id="00d33-670">**Values**</span></span> | <span data-ttu-id="00d33-671">**運作方式**</span><span class="sxs-lookup"><span data-stu-id="00d33-671">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="00d33-672">互動式、UI </span><span class="sxs-lookup"><span data-stu-id="00d33-672">Interactive, UI,</span></span><br /><span data-ttu-id="00d33-673">或前景</span><span class="sxs-lookup"><span data-stu-id="00d33-673">or foreground</span></span> |<span data-ttu-id="00d33-674">2 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-674">2 sec</span></span> |<span data-ttu-id="00d33-675">FixedInterval</span><span class="sxs-lookup"><span data-stu-id="00d33-675">FixedInterval</span></span> |<span data-ttu-id="00d33-676">重試計數</span><span class="sxs-lookup"><span data-stu-id="00d33-676">Retry count</span></span><br /><span data-ttu-id="00d33-677">重試間隔</span><span class="sxs-lookup"><span data-stu-id="00d33-677">Retry interval</span></span><br /><span data-ttu-id="00d33-678">第一個快速重試</span><span class="sxs-lookup"><span data-stu-id="00d33-678">First fast retry</span></span> |<span data-ttu-id="00d33-679">3</span><span class="sxs-lookup"><span data-stu-id="00d33-679">3</span></span><br /><span data-ttu-id="00d33-680">500 毫秒</span><span class="sxs-lookup"><span data-stu-id="00d33-680">500 ms</span></span><br /><span data-ttu-id="00d33-681">true</span><span class="sxs-lookup"><span data-stu-id="00d33-681">true</span></span> |<span data-ttu-id="00d33-682">嘗試 1 - 延遲 0 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-682">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="00d33-683">嘗試 2 - 延遲 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="00d33-683">Attempt 2 - delay 500 ms</span></span><br /><span data-ttu-id="00d33-684">嘗試 3 - 延遲 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="00d33-684">Attempt 3 - delay 500 ms</span></span> |
| <span data-ttu-id="00d33-685">背景或</span><span class="sxs-lookup"><span data-stu-id="00d33-685">Background or</span></span><br /><span data-ttu-id="00d33-686">批次</span><span class="sxs-lookup"><span data-stu-id="00d33-686">batch</span></span> |<span data-ttu-id="00d33-687">60 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-687">60 sec</span></span> |<span data-ttu-id="00d33-688">ExponentialBackoff</span><span class="sxs-lookup"><span data-stu-id="00d33-688">ExponentialBackoff</span></span> |<span data-ttu-id="00d33-689">重試計數</span><span class="sxs-lookup"><span data-stu-id="00d33-689">Retry count</span></span><br /><span data-ttu-id="00d33-690">最小輪詢</span><span class="sxs-lookup"><span data-stu-id="00d33-690">Min back-off</span></span><br /><span data-ttu-id="00d33-691">最大輪詢</span><span class="sxs-lookup"><span data-stu-id="00d33-691">Max back-off</span></span><br /><span data-ttu-id="00d33-692">差異輪詢</span><span class="sxs-lookup"><span data-stu-id="00d33-692">Delta back-off</span></span><br /><span data-ttu-id="00d33-693">第一個快速重試</span><span class="sxs-lookup"><span data-stu-id="00d33-693">First fast retry</span></span> |<span data-ttu-id="00d33-694">5</span><span class="sxs-lookup"><span data-stu-id="00d33-694">5</span></span><br /><span data-ttu-id="00d33-695">0 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-695">0 sec</span></span><br /><span data-ttu-id="00d33-696">60 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-696">60 sec</span></span><br /><span data-ttu-id="00d33-697">2 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-697">2 sec</span></span><br /><span data-ttu-id="00d33-698">false</span><span class="sxs-lookup"><span data-stu-id="00d33-698">false</span></span> |<span data-ttu-id="00d33-699">嘗試 1 - 延遲 0 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-699">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="00d33-700">嘗試 2 - 延遲 ~2 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-700">Attempt 2 - delay ~2 sec</span></span><br /><span data-ttu-id="00d33-701">嘗試 3 - 延遲 ~6 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-701">Attempt 3 - delay ~6 sec</span></span><br /><span data-ttu-id="00d33-702">嘗試 4 - 延遲 ~14 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-702">Attempt 4 - delay ~14 sec</span></span><br /><span data-ttu-id="00d33-703">嘗試 5 - 延遲 ~30 秒</span><span class="sxs-lookup"><span data-stu-id="00d33-703">Attempt 5 - delay ~30 sec</span></span> |

### <a name="more-information"></a><span data-ttu-id="00d33-704">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="00d33-704">More information</span></span>
* <span data-ttu-id="00d33-705">[Azure Active Directory 驗證程式庫][adal]</span><span class="sxs-lookup"><span data-stu-id="00d33-705">[Azure Active Directory Authentication Libraries][adal]</span></span>

## <a name="service-fabric-retry-guidelines"></a><span data-ttu-id="00d33-706">Service Fabric 重試指引</span><span class="sxs-lookup"><span data-stu-id="00d33-706">Service Fabric retry guidelines</span></span>

<span data-ttu-id="00d33-707">分散Service Fabric 叢集中可靠的服務，可防範大部分本文所討論的潛在暫時性錯誤。</span><span class="sxs-lookup"><span data-stu-id="00d33-707">Distributing reliable services in a Service Fabric cluster guards against most of the potential transient faults discussed in this article.</span></span> <span data-ttu-id="00d33-708">不過，某些暫時性錯誤仍有可能發生。</span><span class="sxs-lookup"><span data-stu-id="00d33-708">Some transient faults are still possible, however.</span></span> <span data-ttu-id="00d33-709">例如，命名服務收到要求時可能正在進行路由變更，造成它擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="00d33-709">For example, the naming service might be in the middle of a routing change when it gets a request, causing it to throw an exception.</span></span> <span data-ttu-id="00d33-710">如果相同的要求在 100 毫秒之後才收到，就可能會成功。</span><span class="sxs-lookup"><span data-stu-id="00d33-710">If the same request comes 100 milliseconds later, it will probably succeed.</span></span>

<span data-ttu-id="00d33-711">就內部而言，Service Fabric 會管理這種暫時性錯誤。</span><span class="sxs-lookup"><span data-stu-id="00d33-711">Internally, Service Fabric manages this kind of transient fault.</span></span> <span data-ttu-id="00d33-712">設定服務時，您可以使用 `OperationRetrySettings` 類別進行某些設定。</span><span class="sxs-lookup"><span data-stu-id="00d33-712">You can configure some settings by using the `OperationRetrySettings` class while setting up your services.</span></span>  <span data-ttu-id="00d33-713">下列程式碼為範例。</span><span class="sxs-lookup"><span data-stu-id="00d33-713">The following code shows an example.</span></span> <span data-ttu-id="00d33-714">大部分情況下，這並非必要，用預設設定就可以了。</span><span class="sxs-lookup"><span data-stu-id="00d33-714">In most cases, this should not be necessary, and the default settings will be fine.</span></span>

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

## <a name="more-information"></a><span data-ttu-id="00d33-715">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="00d33-715">More information</span></span>

* [<span data-ttu-id="00d33-716">遠端例外狀況處理</span><span class="sxs-lookup"><span data-stu-id="00d33-716">Remote Exception Handling</span></span>](https://github.com/Microsoft/azure-docs/blob/master/articles/service-fabric/service-fabric-reliable-services-communication-remoting.md#remoting-exception-handling)


## <a name="azure-event-hubs-retry-guidelines"></a><span data-ttu-id="00d33-717">Azure 事件中樞重試指引</span><span class="sxs-lookup"><span data-stu-id="00d33-717">Azure Event Hubs retry guidelines</span></span>

<span data-ttu-id="00d33-718">Azure 事件中樞是大規模的遙測擷取服務，能夠收集、轉換及儲存數百萬個事件。</span><span class="sxs-lookup"><span data-stu-id="00d33-718">Azure Event Hubs is a hyper-scale telemetry ingestion service that collects, transforms, and stores millions of events.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="00d33-719">重試機制</span><span class="sxs-lookup"><span data-stu-id="00d33-719">Retry mechanism</span></span>
<span data-ttu-id="00d33-720">Azure 事件中樞用戶端程式庫中的重試行為是由 `EventHubClient` 類別上的 `RetryPolicy` 屬性控制。</span><span class="sxs-lookup"><span data-stu-id="00d33-720">Retry behavior in the Azure Event Hubs Client Library is controlled by the `RetryPolicy` property on the `EventHubClient` class.</span></span> <span data-ttu-id="00d33-721">當 Azure 事件中樞傳回暫時的 `EventHubsException` 或 `OperationCanceledException` 時，預設原則會以指數輪詢重試。</span><span class="sxs-lookup"><span data-stu-id="00d33-721">The default policy retries with exponential backoff when Azure Event Hub returns a transient `EventHubsException` or an `OperationCanceledException`.</span></span>

### <a name="example"></a><span data-ttu-id="00d33-722">範例</span><span class="sxs-lookup"><span data-stu-id="00d33-722">Example</span></span>
```csharp
EventHubClient client = EventHubClient.CreateFromConnectionString("[event_hub_connection_string]");
client.RetryPolicy = RetryPolicy.Default;
```

### <a name="more-information"></a><span data-ttu-id="00d33-723">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="00d33-723">More information</span></span>
[<span data-ttu-id="00d33-724">Azure 事件中樞的 .NET 標準用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="00d33-724"> .NET Standard client library for Azure Event Hubs</span></span>](https://github.com/Azure/azure-event-hubs-dotnet)

## <a name="general-rest-and-retry-guidelines"></a><span data-ttu-id="00d33-725">一般 REST 與重試指引</span><span class="sxs-lookup"><span data-stu-id="00d33-725">General REST and retry guidelines</span></span>
<span data-ttu-id="00d33-726">存取 Azure 或協力廠商服務時請考量下列事項：</span><span class="sxs-lookup"><span data-stu-id="00d33-726">Consider the following when accessing Azure or third party services:</span></span>

* <span data-ttu-id="00d33-727">使用系統化的方法管理重試，或許是做為可重複使用的程式碼，讓您可以在所有的用戶端與所有的解決方案之間套用一致的方法。</span><span class="sxs-lookup"><span data-stu-id="00d33-727">Use a systematic approach to managing retries, perhaps as reusable code, so that you can apply a consistent methodology across all clients and all solutions.</span></span>
* <span data-ttu-id="00d33-728">如果目標服務或用戶端有沒有內建的重試機制，請考慮使用重試架構 (例如暫時性錯誤處理應用程式區塊) 來管理重試。</span><span class="sxs-lookup"><span data-stu-id="00d33-728">Consider using a retry framework such as the Transient Fault Handling Application Block to manage retries if the target service or client has no built-in retry mechanism.</span></span> <span data-ttu-id="00d33-729">這可以幫助您實作一致的重試行為，且可能會為目標服務提供適合的預設重試策略。</span><span class="sxs-lookup"><span data-stu-id="00d33-729">This will help you implement a consistent retry behavior, and it may provide a suitable default retry strategy for the target service.</span></span> <span data-ttu-id="00d33-730">不過，您可能需要為具有非標準行為的服務建立自訂重試程式碼，非標準行為不依例外狀況來表示暫時性錯誤，或如果您想要使用 **Retry-Response** 來管理重試行為，也必須建立自訂重試程式碼。</span><span class="sxs-lookup"><span data-stu-id="00d33-730">However, you may need to create custom retry code for services that have non-standard behavior, that do not rely on exceptions to indicate transient failures, or if you want to use a **Retry-Response** reply to manage retry behavior.</span></span>
* <span data-ttu-id="00d33-731">暫時性偵測邏輯將取決於您用來叫用 REST 呼叫的實際用戶端 API。</span><span class="sxs-lookup"><span data-stu-id="00d33-731">The transient detection logic will depend on the actual client API you use to invoke the REST calls.</span></span> <span data-ttu-id="00d33-732">有些用戶端 (例如較新的 **HttpClient** 類別)，不會為已完成的要求擲回狀態碼為 HTTP 不成功的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="00d33-732">Some clients, such as the newer **HttpClient** class, will not throw exceptions for completed requests with a non-success HTTP status code.</span></span> <span data-ttu-id="00d33-733">這會改善效能，但會防止使用暫時性錯誤處理應用程式區塊。</span><span class="sxs-lookup"><span data-stu-id="00d33-733">This improves performance but prevents the use of the Transient Fault Handling Application Block.</span></span> <span data-ttu-id="00d33-734">在此情況下，您無法使用會為 HTTP 不成功的狀態碼產生例外狀況的程式碼，來包裝對 REST API 的呼叫，接著會由區塊來處理該狀態碼。</span><span class="sxs-lookup"><span data-stu-id="00d33-734">In this case you could wrap the call to the REST API with code that produces exceptions for non-success HTTP status codes, which can then be processed by the block.</span></span> <span data-ttu-id="00d33-735">或者，您可以使用不同的機制來驅動重試。</span><span class="sxs-lookup"><span data-stu-id="00d33-735">Alternatively, you can use a different mechanism to drive the retries.</span></span>
* <span data-ttu-id="00d33-736">從服務傳回的 HTTP 狀態碼可協助指出錯誤是否為暫時性。</span><span class="sxs-lookup"><span data-stu-id="00d33-736">The HTTP status code returned from the service can help to indicate whether the failure is transient.</span></span> <span data-ttu-id="00d33-737">您可能需要檢查用戶端產生的例外狀況，或檢查重試架構，以存取狀態碼或決定等同的例外狀況類型。</span><span class="sxs-lookup"><span data-stu-id="00d33-737">You may need to examine the exceptions generated by a client or the retry framework to access the status code or to determine the equivalent exception type.</span></span> <span data-ttu-id="00d33-738">下列 HTTP 代碼通常表示重試是適當的：</span><span class="sxs-lookup"><span data-stu-id="00d33-738">The following HTTP codes typically indicate that a retry is appropriate:</span></span>
  * <span data-ttu-id="00d33-739">408 要求逾時</span><span class="sxs-lookup"><span data-stu-id="00d33-739">408 Request Timeout</span></span>
  * <span data-ttu-id="00d33-740">500 內部伺服器錯誤</span><span class="sxs-lookup"><span data-stu-id="00d33-740">500 Internal Server Error</span></span>
  * <span data-ttu-id="00d33-741">502 錯誤的閘道</span><span class="sxs-lookup"><span data-stu-id="00d33-741">502 Bad Gateway</span></span>
  * <span data-ttu-id="00d33-742">503 服務無法使用</span><span class="sxs-lookup"><span data-stu-id="00d33-742">503 Service Unavailable</span></span>
  * <span data-ttu-id="00d33-743">504 閘道逾時</span><span class="sxs-lookup"><span data-stu-id="00d33-743">504 Gateway Timeout</span></span>
* <span data-ttu-id="00d33-744">如果您讓重試邏輯以例外狀況為基礎，則下列項目通常表示無法建立連接的暫時性錯誤：</span><span class="sxs-lookup"><span data-stu-id="00d33-744">If you base your retry logic on exceptions, the following typically indicate a transient failure where no connection could be established:</span></span>
  * <span data-ttu-id="00d33-745">WebExceptionStatus.ConnectionClosed</span><span class="sxs-lookup"><span data-stu-id="00d33-745">WebExceptionStatus.ConnectionClosed</span></span>
  * <span data-ttu-id="00d33-746">WebExceptionStatus.ConnectFailure</span><span class="sxs-lookup"><span data-stu-id="00d33-746">WebExceptionStatus.ConnectFailure</span></span>
  * <span data-ttu-id="00d33-747">WebExceptionStatus.Timeout</span><span class="sxs-lookup"><span data-stu-id="00d33-747">WebExceptionStatus.Timeout</span></span>
  * <span data-ttu-id="00d33-748">WebExceptionStatus.RequestCanceled</span><span class="sxs-lookup"><span data-stu-id="00d33-748">WebExceptionStatus.RequestCanceled</span></span>
* <span data-ttu-id="00d33-749">在服務無法使用狀態下，服務可能會先指出適當的延遲，然後在 **Retry-After** 回應標頭或不同的自訂標頭中重試。</span><span class="sxs-lookup"><span data-stu-id="00d33-749">In the case of a service unavailable status, the service might indicate the appropriate delay before retrying in the **Retry-After** response header or a different custom header.</span></span> <span data-ttu-id="00d33-750">服務也可能傳送額外的資訊做為自訂標頭，或內嵌在回應的內容中。</span><span class="sxs-lookup"><span data-stu-id="00d33-750">Services might also send additional information as custom headers, or embedded in the content of the response.</span></span> <span data-ttu-id="00d33-751">暫時性錯誤處理應用程式區塊無法使用標準或任何自訂的「retry-after」標頭。</span><span class="sxs-lookup"><span data-stu-id="00d33-751">The Transient Fault Handling Application Block cannot use the standard or any custom “retry-after” headers.</span></span>
* <span data-ttu-id="00d33-752">408 要求逾時除外，不要重試代表用戶端錯誤 (4xx 範圍中的錯誤) 的狀態碼。</span><span class="sxs-lookup"><span data-stu-id="00d33-752">Do not retry for status codes representing client errors (errors in the 4xx range) except for a 408 Request Timeout.</span></span>
* <span data-ttu-id="00d33-753">在各種條件下 (例如不同的網路狀態及各種的系統負載) 徹底測試您的重試策略和機制。</span><span class="sxs-lookup"><span data-stu-id="00d33-753">Thoroughly test your retry strategies and mechanisms under a range of conditions, such as different network states and varying system loadings.</span></span>

### <a name="retry-strategies"></a><span data-ttu-id="00d33-754">重試策略</span><span class="sxs-lookup"><span data-stu-id="00d33-754">Retry strategies</span></span>
<span data-ttu-id="00d33-755">以下為一般類型的重試策略間隔：</span><span class="sxs-lookup"><span data-stu-id="00d33-755">The following are the typical types of retry strategy intervals:</span></span>

* <span data-ttu-id="00d33-756">**指數**：此重試原則會執行指定的重試次數，並使用隨機的指數退避方法來決定重試之間的間隔。</span><span class="sxs-lookup"><span data-stu-id="00d33-756">**Exponential**: A retry policy that performs a specified number of retries, using a randomized exponential back off approach to determine the interval between retries.</span></span> <span data-ttu-id="00d33-757">例如：</span><span class="sxs-lookup"><span data-stu-id="00d33-757">For example:</span></span>

        var random = new Random();

        var delta = (int)((Math.Pow(2.0, currentRetryCount) - 1.0) *
                    random.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8),
                    (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
        var interval = (int)Math.Min(checked(this.minBackoff.TotalMilliseconds + delta),
                       this.maxBackoff.TotalMilliseconds);
        retryInterval = TimeSpan.FromMilliseconds(interval);
* <span data-ttu-id="00d33-758">**累加**：此重試策略具有指定的重試嘗試次數，並在重試之間使用累加時間間隔。</span><span class="sxs-lookup"><span data-stu-id="00d33-758">**Incremental**: A retry strategy with a specified number of retry attempts and an incremental time interval between retries.</span></span> <span data-ttu-id="00d33-759">例如：</span><span class="sxs-lookup"><span data-stu-id="00d33-759">For example:</span></span>

        retryInterval = TimeSpan.FromMilliseconds(this.initialInterval.TotalMilliseconds +
                       (this.increment.TotalMilliseconds * currentRetryCount));
* <span data-ttu-id="00d33-760">**線性重試**：此重試原則會執行指定的重試次數，並在重試之間使用指定的固定時間間隔。</span><span class="sxs-lookup"><span data-stu-id="00d33-760">**LinearRetry**: A retry policy that performs a specified number of retries, using a specified fixed time interval between retries.</span></span> <span data-ttu-id="00d33-761">例如：</span><span class="sxs-lookup"><span data-stu-id="00d33-761">For example:</span></span>

        retryInterval = this.deltaBackoff;

### <a name="more-information"></a><span data-ttu-id="00d33-762">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="00d33-762">More information</span></span>
* [<span data-ttu-id="00d33-763">斷路器策略</span><span class="sxs-lookup"><span data-stu-id="00d33-763">Circuit breaker strategies</span></span>](http://msdn.microsoft.com/library/dn589784.aspx)

## <a name="transient-fault-handling-with-polly"></a><span data-ttu-id="00d33-764">Polly 的暫時性錯誤處理</span><span class="sxs-lookup"><span data-stu-id="00d33-764">Transient fault handling with Polly</span></span>
<span data-ttu-id="00d33-765">[Polly](http://www.thepollyproject.org) 是程式庫，以程式設計方式處理重試和[斷路器][circuit-breaker]策略。</span><span class="sxs-lookup"><span data-stu-id="00d33-765">[Polly](http://www.thepollyproject.org) is a library to programatically handle retries and [circuit breaker][circuit-breaker] strategies.</span></span> <span data-ttu-id="00d33-766">Polly 專案是 [.NET Foundation][dotnet-foundation] 的成員。</span><span class="sxs-lookup"><span data-stu-id="00d33-766">The Polly project is a member of the [.NET Foundation][dotnet-foundation].</span></span> <span data-ttu-id="00d33-767">當服務所在的用戶端本身不支援重試，Polly 是有效的替代方法，可以不必撰寫自訂重試程式碼，但有可能難以正確實作。</span><span class="sxs-lookup"><span data-stu-id="00d33-767">For services where the client does not natively support retries, Polly is a valid alternative and avoids the need to write custom retry code, which can be hard to implement correctly.</span></span> <span data-ttu-id="00d33-768">Polly 也可用來在錯誤發生時追蹤錯誤，讓您可以記錄重試。</span><span class="sxs-lookup"><span data-stu-id="00d33-768">Polly also provides a way to trace errors when they occur, so that you can log retries.</span></span>

<!-- links -->

[adal]: /azure/active-directory/develop/active-directory-authentication-libraries
[autorest]: https://github.com/Azure/autorest/tree/master/docs
[circuit-breaker]: ../patterns/circuit-breaker.md
[ConnectionPolicy.RetryOptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.retryoptions.aspx
[documentdb-api]: /azure/documentdb/documentdb-introduction
[dotnet-foundation]: https://dotnetfoundation.org/
[polly]: http://www.thepollyproject.org
[redis-cache-troubleshoot]: /azure/redis-cache/cache-how-to-troubleshoot
[SearchIndexClient]: https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchindexclient.aspx
[SearchServiceClient]: https://msdn.microsoft.com/library/microsoft.azure.search.searchserviceclient.aspx

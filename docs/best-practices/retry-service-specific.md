---
title: 重試服務的特定指引
description: 用於設定重試機制的服務特定指引。
author: dragon119
ms.date: 07/13/2016
pnp.series.title: Best Practices
ms.openlocfilehash: c5a9bc99c4693f35c38dabcf07b3465add6a8cb1
ms.sourcegitcommit: 94d50043db63416c4d00cebe927a0c88f78c3219
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2018
ms.locfileid: "47429535"
---
# <a name="retry-guidance-for-specific-services"></a><span data-ttu-id="3fa35-103">特定服務的重試指引</span><span class="sxs-lookup"><span data-stu-id="3fa35-103">Retry guidance for specific services</span></span>

<span data-ttu-id="3fa35-104">大多數的 Azure 服務與用戶端 SDK 皆包含重試機制，</span><span class="sxs-lookup"><span data-stu-id="3fa35-104">Most Azure services and client SDKs include a retry mechanism.</span></span> <span data-ttu-id="3fa35-105">但各有不同，因為每個服務有不同的特性與需求，也因此系統會根據特定的服務調整每個重試機制。</span><span class="sxs-lookup"><span data-stu-id="3fa35-105">However, these differ because each service has different characteristics and requirements, and so each retry mechanism is tuned to a specific service.</span></span> <span data-ttu-id="3fa35-106">本指南摘要說明大多數 Azure 服務的重試機制功能，並提供一些資訊幫助您使用、調整，或擴充該服務的重試機制。</span><span class="sxs-lookup"><span data-stu-id="3fa35-106">This guide summarizes the retry mechanism features for the majority of Azure services, and includes information to help you use, adapt, or extend the retry mechanism for that service.</span></span>

<span data-ttu-id="3fa35-107">如需處理暫時性錯誤，及對服務與資源重試連接與作業的一般指引，請參閱 [重試指引](./transient-faults.md)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-107">For general guidance on handling transient faults, and retrying connections and operations against services and resources, see [Retry guidance](./transient-faults.md).</span></span>

<span data-ttu-id="3fa35-108">下表摘要說明此指引中所述 Azure 服務的重試功能。</span><span class="sxs-lookup"><span data-stu-id="3fa35-108">The following table summarizes the retry features for the Azure services described in this guidance.</span></span>

| <span data-ttu-id="3fa35-109">**服務**</span><span class="sxs-lookup"><span data-stu-id="3fa35-109">**Service**</span></span> | <span data-ttu-id="3fa35-110">**重試功能**</span><span class="sxs-lookup"><span data-stu-id="3fa35-110">**Retry capabilities**</span></span> | <span data-ttu-id="3fa35-111">**原則組態**</span><span class="sxs-lookup"><span data-stu-id="3fa35-111">**Policy configuration**</span></span> | <span data-ttu-id="3fa35-112">**範圍**</span><span class="sxs-lookup"><span data-stu-id="3fa35-112">**Scope**</span></span> | <span data-ttu-id="3fa35-113">**遙測功能**</span><span class="sxs-lookup"><span data-stu-id="3fa35-113">**Telemetry features**</span></span> |
| --- | --- | --- | --- | --- |
| <span data-ttu-id="3fa35-114">**[Azure Active Directory](#azure-active-directory)**</span><span class="sxs-lookup"><span data-stu-id="3fa35-114">**[Azure Active Directory](#azure-active-directory)**</span></span> |<span data-ttu-id="3fa35-115">ADAL 程式庫原生</span><span class="sxs-lookup"><span data-stu-id="3fa35-115">Native in ADAL library</span></span> |<span data-ttu-id="3fa35-116">內嵌至 ADAL 程式庫</span><span class="sxs-lookup"><span data-stu-id="3fa35-116">Embeded into ADAL library</span></span> |<span data-ttu-id="3fa35-117">內部</span><span class="sxs-lookup"><span data-stu-id="3fa35-117">Internal</span></span> |<span data-ttu-id="3fa35-118">None</span><span class="sxs-lookup"><span data-stu-id="3fa35-118">None</span></span> |
| <span data-ttu-id="3fa35-119">**[Cosmos DB](#cosmos-db)**</span><span class="sxs-lookup"><span data-stu-id="3fa35-119">**[Cosmos DB](#cosmos-db)**</span></span> |<span data-ttu-id="3fa35-120">服務原生</span><span class="sxs-lookup"><span data-stu-id="3fa35-120">Native in service</span></span> |<span data-ttu-id="3fa35-121">不可設定</span><span class="sxs-lookup"><span data-stu-id="3fa35-121">Non-configurable</span></span> |<span data-ttu-id="3fa35-122">全域</span><span class="sxs-lookup"><span data-stu-id="3fa35-122">Global</span></span> |<span data-ttu-id="3fa35-123">TraceSource</span><span class="sxs-lookup"><span data-stu-id="3fa35-123">TraceSource</span></span> |
| <span data-ttu-id="3fa35-124">**Data Lake Store**</span><span class="sxs-lookup"><span data-stu-id="3fa35-124">**Data Lake Store**</span></span> |<span data-ttu-id="3fa35-125">用戶端原生</span><span class="sxs-lookup"><span data-stu-id="3fa35-125">Native in client</span></span> |<span data-ttu-id="3fa35-126">不可設定</span><span class="sxs-lookup"><span data-stu-id="3fa35-126">Non-configurable</span></span> |<span data-ttu-id="3fa35-127">個別作業</span><span class="sxs-lookup"><span data-stu-id="3fa35-127">Individual operations</span></span> |<span data-ttu-id="3fa35-128">None</span><span class="sxs-lookup"><span data-stu-id="3fa35-128">None</span></span> |
| <span data-ttu-id="3fa35-129">**[事件中樞](#event-hubs)**</span><span class="sxs-lookup"><span data-stu-id="3fa35-129">**[Event Hubs](#event-hubs)**</span></span> |<span data-ttu-id="3fa35-130">用戶端原生</span><span class="sxs-lookup"><span data-stu-id="3fa35-130">Native in client</span></span> |<span data-ttu-id="3fa35-131">程式設計</span><span class="sxs-lookup"><span data-stu-id="3fa35-131">Programmatic</span></span> |<span data-ttu-id="3fa35-132">用戶端</span><span class="sxs-lookup"><span data-stu-id="3fa35-132">Client</span></span> |<span data-ttu-id="3fa35-133">None</span><span class="sxs-lookup"><span data-stu-id="3fa35-133">None</span></span> |
| <span data-ttu-id="3fa35-134">**[IoT 中樞](#iot-hub)**</span><span class="sxs-lookup"><span data-stu-id="3fa35-134">**[IoT Hub](#iot-hub)**</span></span> |<span data-ttu-id="3fa35-135">用戶端 SDK 原生</span><span class="sxs-lookup"><span data-stu-id="3fa35-135">Native in client SDK</span></span> |<span data-ttu-id="3fa35-136">程式設計</span><span class="sxs-lookup"><span data-stu-id="3fa35-136">Programmatic</span></span> |<span data-ttu-id="3fa35-137">用戶端</span><span class="sxs-lookup"><span data-stu-id="3fa35-137">Client</span></span> |<span data-ttu-id="3fa35-138">None</span><span class="sxs-lookup"><span data-stu-id="3fa35-138">None</span></span> |
| <span data-ttu-id="3fa35-139">**[Redis 快取](#azure-redis-cache)**</span><span class="sxs-lookup"><span data-stu-id="3fa35-139">**[Redis Cache](#azure-redis-cache)**</span></span> |<span data-ttu-id="3fa35-140">用戶端原生</span><span class="sxs-lookup"><span data-stu-id="3fa35-140">Native in client</span></span> |<span data-ttu-id="3fa35-141">程式設計</span><span class="sxs-lookup"><span data-stu-id="3fa35-141">Programmatic</span></span> |<span data-ttu-id="3fa35-142">用戶端</span><span class="sxs-lookup"><span data-stu-id="3fa35-142">Client</span></span> |<span data-ttu-id="3fa35-143">TextWriter</span><span class="sxs-lookup"><span data-stu-id="3fa35-143">TextWriter</span></span> |
| <span data-ttu-id="3fa35-144">**[搜尋](#azure-search)**</span><span class="sxs-lookup"><span data-stu-id="3fa35-144">**[Search](#azure-search)**</span></span> |<span data-ttu-id="3fa35-145">用戶端原生</span><span class="sxs-lookup"><span data-stu-id="3fa35-145">Native in client</span></span> |<span data-ttu-id="3fa35-146">程式設計</span><span class="sxs-lookup"><span data-stu-id="3fa35-146">Programmatic</span></span> |<span data-ttu-id="3fa35-147">用戶端</span><span class="sxs-lookup"><span data-stu-id="3fa35-147">Client</span></span> |<span data-ttu-id="3fa35-148">ETW 或自訂</span><span class="sxs-lookup"><span data-stu-id="3fa35-148">ETW or Custom</span></span> |
| <span data-ttu-id="3fa35-149">**[服務匯流排](#service-bus)**</span><span class="sxs-lookup"><span data-stu-id="3fa35-149">**[Service Bus](#service-bus)**</span></span> |<span data-ttu-id="3fa35-150">用戶端原生</span><span class="sxs-lookup"><span data-stu-id="3fa35-150">Native in client</span></span> |<span data-ttu-id="3fa35-151">程式設計</span><span class="sxs-lookup"><span data-stu-id="3fa35-151">Programmatic</span></span> |<span data-ttu-id="3fa35-152">命名空間管理員、傳訊處理站及用戶端</span><span class="sxs-lookup"><span data-stu-id="3fa35-152">Namespace Manager, Messaging Factory, and Client</span></span> |<span data-ttu-id="3fa35-153">ETW</span><span class="sxs-lookup"><span data-stu-id="3fa35-153">ETW</span></span> |
| <span data-ttu-id="3fa35-154">**[Service Fabric](#service-fabric)**</span><span class="sxs-lookup"><span data-stu-id="3fa35-154">**[Service Fabric](#service-fabric)**</span></span> |<span data-ttu-id="3fa35-155">用戶端原生</span><span class="sxs-lookup"><span data-stu-id="3fa35-155">Native in client</span></span> |<span data-ttu-id="3fa35-156">程式設計</span><span class="sxs-lookup"><span data-stu-id="3fa35-156">Programmatic</span></span> |<span data-ttu-id="3fa35-157">用戶端</span><span class="sxs-lookup"><span data-stu-id="3fa35-157">Client</span></span> |<span data-ttu-id="3fa35-158">None</span><span class="sxs-lookup"><span data-stu-id="3fa35-158">None</span></span> | 
| <span data-ttu-id="3fa35-159">**[使用 ADO.NET 的 SQL Database](#sql-database-using-adonet)**</span><span class="sxs-lookup"><span data-stu-id="3fa35-159">**[SQL Database with ADO.NET](#sql-database-using-adonet)**</span></span> |[<span data-ttu-id="3fa35-160">Polly</span><span class="sxs-lookup"><span data-stu-id="3fa35-160">Polly</span></span>](#transient-fault-handling-with-polly) |<span data-ttu-id="3fa35-161">宣告與程式設計</span><span class="sxs-lookup"><span data-stu-id="3fa35-161">Declarative and programmatic</span></span> |<span data-ttu-id="3fa35-162">單一陳述式或程式碼區塊</span><span class="sxs-lookup"><span data-stu-id="3fa35-162">Single statements or blocks of code</span></span> |<span data-ttu-id="3fa35-163">自訂</span><span class="sxs-lookup"><span data-stu-id="3fa35-163">Custom</span></span> |
| <span data-ttu-id="3fa35-164">**[使用 Entity Framework 的 SQL Database](#sql-database-using-entity-framework-6)**</span><span class="sxs-lookup"><span data-stu-id="3fa35-164">**[SQL Database with Entity Framework](#sql-database-using-entity-framework-6)**</span></span> |<span data-ttu-id="3fa35-165">用戶端原生</span><span class="sxs-lookup"><span data-stu-id="3fa35-165">Native in client</span></span> |<span data-ttu-id="3fa35-166">程式設計</span><span class="sxs-lookup"><span data-stu-id="3fa35-166">Programmatic</span></span> |<span data-ttu-id="3fa35-167">每個 AppDomain 全域</span><span class="sxs-lookup"><span data-stu-id="3fa35-167">Global per AppDomain</span></span> |<span data-ttu-id="3fa35-168">None</span><span class="sxs-lookup"><span data-stu-id="3fa35-168">None</span></span> |
| <span data-ttu-id="3fa35-169">**[使用 Entity Framework Core 的 SQL Database](#sql-database-using-entity-framework-core)**</span><span class="sxs-lookup"><span data-stu-id="3fa35-169">**[SQL Database with Entity Framework Core](#sql-database-using-entity-framework-core)**</span></span> |<span data-ttu-id="3fa35-170">用戶端原生</span><span class="sxs-lookup"><span data-stu-id="3fa35-170">Native in client</span></span> |<span data-ttu-id="3fa35-171">程式設計</span><span class="sxs-lookup"><span data-stu-id="3fa35-171">Programmatic</span></span> |<span data-ttu-id="3fa35-172">每個 AppDomain 全域</span><span class="sxs-lookup"><span data-stu-id="3fa35-172">Global per AppDomain</span></span> |<span data-ttu-id="3fa35-173">None</span><span class="sxs-lookup"><span data-stu-id="3fa35-173">None</span></span> |
| <span data-ttu-id="3fa35-174">**[儲存體](#azure-storage)**</span><span class="sxs-lookup"><span data-stu-id="3fa35-174">**[Storage](#azure-storage)**</span></span> |<span data-ttu-id="3fa35-175">用戶端原生</span><span class="sxs-lookup"><span data-stu-id="3fa35-175">Native in client</span></span> |<span data-ttu-id="3fa35-176">程式設計</span><span class="sxs-lookup"><span data-stu-id="3fa35-176">Programmatic</span></span> |<span data-ttu-id="3fa35-177">用戶端與個別作業</span><span class="sxs-lookup"><span data-stu-id="3fa35-177">Client and individual operations</span></span> |<span data-ttu-id="3fa35-178">TraceSource</span><span class="sxs-lookup"><span data-stu-id="3fa35-178">TraceSource</span></span> |

> [!NOTE]
> <span data-ttu-id="3fa35-179">對於大多數的 Azure 內建重試機制而言，目前還無法為不同類型的錯誤或例外狀況套用不同的重試原則。</span><span class="sxs-lookup"><span data-stu-id="3fa35-179">For most of the Azure built-in retry mechanisms, there is currently no way apply a different retry policy for different types of error or exception.</span></span> <span data-ttu-id="3fa35-180">您應該設定一個原則，提供最佳的平均效能和可用性。</span><span class="sxs-lookup"><span data-stu-id="3fa35-180">You should configure a policy that provides the optimum average performance and availability.</span></span> <span data-ttu-id="3fa35-181">微調原則的一種方法，就是分析記錄檔來判斷正在發生的暫時性錯誤類型。</span><span class="sxs-lookup"><span data-stu-id="3fa35-181">One way to fine-tune the policy is to analyze log files to determine the type of transient faults that are occurring.</span></span> 

## <a name="azure-active-directory"></a><span data-ttu-id="3fa35-182">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="3fa35-182">Azure Active Directory</span></span>
<span data-ttu-id="3fa35-183">Azure Active Directory (Azure AD) 是完整的身分識別與存取管理雲端解決方案，結合了核心目錄服務、進階身分識別控管、安全性，以及應用程式存取管理。</span><span class="sxs-lookup"><span data-stu-id="3fa35-183">Azure Active Directory (Azure AD) is a comprehensive identity and access management cloud solution that combines core directory services, advanced identity governance, security, and application access management.</span></span> <span data-ttu-id="3fa35-184">Azure AD 也提供給開發人員一個身分識別管理平台，以根據集中化的原則和規則將存取控制傳遞給其應用程式。</span><span class="sxs-lookup"><span data-stu-id="3fa35-184">Azure AD also offers developers an identity management platform to deliver access control to their applications, based on centralized policy and rules.</span></span>

> [!NOTE]
> <span data-ttu-id="3fa35-185">如需受控服務識別端點的重試指引，請參閱[如何使用 Azure 虛擬機器受控服務識別 (MSI) 來取得權杖](/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-185">For retry guidance on Managed Service Identity endpoints, see [How to use an Azure VM Managed Service Identity (MSI) for token acquisition](/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="3fa35-186">重試機制</span><span class="sxs-lookup"><span data-stu-id="3fa35-186">Retry mechanism</span></span>
<span data-ttu-id="3fa35-187">Active Directory 驗證程式庫 (ADAL) 中有內建的 Azure Active Directory 重試機制。</span><span class="sxs-lookup"><span data-stu-id="3fa35-187">There is a built-in retry mechanism for Azure Active Directory in the Active Directory Authentication Library (ADAL).</span></span> <span data-ttu-id="3fa35-188">為了避免非預期的鎖定，建議第三方廠商程式庫和應用程式程式碼「不要」重試失敗的連線，但允許 ADAL 處理重試。</span><span class="sxs-lookup"><span data-stu-id="3fa35-188">To avoid unexpected lockouts, we recommend that third party libraries and application code do **not** retry failed connections, but allow ADAL to handle retries.</span></span> 

### <a name="retry-usage-guidance"></a><span data-ttu-id="3fa35-189">重試使用指引</span><span class="sxs-lookup"><span data-stu-id="3fa35-189">Retry usage guidance</span></span>
<span data-ttu-id="3fa35-190">使用 Azure Active Directory 時請考慮下列指引：</span><span class="sxs-lookup"><span data-stu-id="3fa35-190">Consider the following guidelines when using Azure Active Directory:</span></span>

* <span data-ttu-id="3fa35-191">可能的話，使用 ADAL 程式庫和內建支援進行重試。</span><span class="sxs-lookup"><span data-stu-id="3fa35-191">When possible, use the ADAL library and the built-in support for retries.</span></span>
* <span data-ttu-id="3fa35-192">如果您正在使用 Azure Active Directory 的 REST API，請在結果碼為 429 (要求太多) 或 5xx 範圍內出現錯誤時，重試該作業。</span><span class="sxs-lookup"><span data-stu-id="3fa35-192">If you are using the REST API for Azure Active Directory, retry the operation if the result code is 429 (Too Many Requests) or an error in the 5xx range.</span></span> <span data-ttu-id="3fa35-193">若為任何其他錯誤，請勿重試。</span><span class="sxs-lookup"><span data-stu-id="3fa35-193">Do not retry for any other errors.</span></span>
* <span data-ttu-id="3fa35-194">建議將指數退避原則搭配 Azure Active Directory 用於批次案例中。</span><span class="sxs-lookup"><span data-stu-id="3fa35-194">An exponential back-off policy is recommended for use in batch scenarios with Azure Active Directory.</span></span>

<span data-ttu-id="3fa35-195">請考慮讓重試作業從下列設定開始。</span><span class="sxs-lookup"><span data-stu-id="3fa35-195">Consider starting with the following settings for retrying operations.</span></span> <span data-ttu-id="3fa35-196">這些是一般用途設定，您應該監視作業並微調其值以符合您自己的需求。</span><span class="sxs-lookup"><span data-stu-id="3fa35-196">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="3fa35-197">**內容**</span><span class="sxs-lookup"><span data-stu-id="3fa35-197">**Context**</span></span> | <span data-ttu-id="3fa35-198">**範例目標 E2E<br />最大延遲**</span><span class="sxs-lookup"><span data-stu-id="3fa35-198">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="3fa35-199">**重試策略**</span><span class="sxs-lookup"><span data-stu-id="3fa35-199">**Retry strategy**</span></span> | <span data-ttu-id="3fa35-200">**設定**</span><span class="sxs-lookup"><span data-stu-id="3fa35-200">**Settings**</span></span> | <span data-ttu-id="3fa35-201">**值**</span><span class="sxs-lookup"><span data-stu-id="3fa35-201">**Values**</span></span> | <span data-ttu-id="3fa35-202">**運作方式**</span><span class="sxs-lookup"><span data-stu-id="3fa35-202">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="3fa35-203">互動式、UI </span><span class="sxs-lookup"><span data-stu-id="3fa35-203">Interactive, UI,</span></span><br /><span data-ttu-id="3fa35-204">或前景</span><span class="sxs-lookup"><span data-stu-id="3fa35-204">or foreground</span></span> |<span data-ttu-id="3fa35-205">2 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-205">2 sec</span></span> |<span data-ttu-id="3fa35-206">FixedInterval</span><span class="sxs-lookup"><span data-stu-id="3fa35-206">FixedInterval</span></span> |<span data-ttu-id="3fa35-207">重試計數</span><span class="sxs-lookup"><span data-stu-id="3fa35-207">Retry count</span></span><br /><span data-ttu-id="3fa35-208">重試間隔</span><span class="sxs-lookup"><span data-stu-id="3fa35-208">Retry interval</span></span><br /><span data-ttu-id="3fa35-209">第一個快速重試</span><span class="sxs-lookup"><span data-stu-id="3fa35-209">First fast retry</span></span> |<span data-ttu-id="3fa35-210">3</span><span class="sxs-lookup"><span data-stu-id="3fa35-210">3</span></span><br /><span data-ttu-id="3fa35-211">500 毫秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-211">500 ms</span></span><br /><span data-ttu-id="3fa35-212">true</span><span class="sxs-lookup"><span data-stu-id="3fa35-212">true</span></span> |<span data-ttu-id="3fa35-213">嘗試 1 - 延遲 0 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-213">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="3fa35-214">嘗試 2 - 延遲 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-214">Attempt 2 - delay 500 ms</span></span><br /><span data-ttu-id="3fa35-215">嘗試 3 - 延遲 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-215">Attempt 3 - delay 500 ms</span></span> |
| <span data-ttu-id="3fa35-216">背景或</span><span class="sxs-lookup"><span data-stu-id="3fa35-216">Background or</span></span><br /><span data-ttu-id="3fa35-217">批次</span><span class="sxs-lookup"><span data-stu-id="3fa35-217">batch</span></span> |<span data-ttu-id="3fa35-218">60 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-218">60 sec</span></span> |<span data-ttu-id="3fa35-219">ExponentialBackoff</span><span class="sxs-lookup"><span data-stu-id="3fa35-219">ExponentialBackoff</span></span> |<span data-ttu-id="3fa35-220">重試計數</span><span class="sxs-lookup"><span data-stu-id="3fa35-220">Retry count</span></span><br /><span data-ttu-id="3fa35-221">最小輪詢</span><span class="sxs-lookup"><span data-stu-id="3fa35-221">Min back-off</span></span><br /><span data-ttu-id="3fa35-222">最大輪詢</span><span class="sxs-lookup"><span data-stu-id="3fa35-222">Max back-off</span></span><br /><span data-ttu-id="3fa35-223">差異輪詢</span><span class="sxs-lookup"><span data-stu-id="3fa35-223">Delta back-off</span></span><br /><span data-ttu-id="3fa35-224">第一個快速重試</span><span class="sxs-lookup"><span data-stu-id="3fa35-224">First fast retry</span></span> |<span data-ttu-id="3fa35-225">5</span><span class="sxs-lookup"><span data-stu-id="3fa35-225">5</span></span><br /><span data-ttu-id="3fa35-226">0 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-226">0 sec</span></span><br /><span data-ttu-id="3fa35-227">60 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-227">60 sec</span></span><br /><span data-ttu-id="3fa35-228">2 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-228">2 sec</span></span><br /><span data-ttu-id="3fa35-229">false</span><span class="sxs-lookup"><span data-stu-id="3fa35-229">false</span></span> |<span data-ttu-id="3fa35-230">嘗試 1 - 延遲 0 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-230">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="3fa35-231">嘗試 2 - 延遲 ~2 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-231">Attempt 2 - delay ~2 sec</span></span><br /><span data-ttu-id="3fa35-232">嘗試 3 - 延遲 ~6 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-232">Attempt 3 - delay ~6 sec</span></span><br /><span data-ttu-id="3fa35-233">嘗試 4 - 延遲 ~14 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-233">Attempt 4 - delay ~14 sec</span></span><br /><span data-ttu-id="3fa35-234">嘗試 5 - 延遲 ~30 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-234">Attempt 5 - delay ~30 sec</span></span> |

### <a name="more-information"></a><span data-ttu-id="3fa35-235">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="3fa35-235">More information</span></span>
* <span data-ttu-id="3fa35-236">[Azure Active Directory 驗證程式庫][adal]</span><span class="sxs-lookup"><span data-stu-id="3fa35-236">[Azure Active Directory Authentication Libraries][adal]</span></span>

## <a name="cosmos-db"></a><span data-ttu-id="3fa35-237">Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="3fa35-237">Cosmos DB</span></span>

<span data-ttu-id="3fa35-238">Cosmos DB 是完全受控的多模型資料庫，可支援無結構描述的 JSON 資料。</span><span class="sxs-lookup"><span data-stu-id="3fa35-238">Cosmos DB is a fully-managed multi-model database that supports schema-less JSON data.</span></span> <span data-ttu-id="3fa35-239">它提供可設定且可靠的效能、原生的 JavaScript 交易處理，而且是針對可彈性延展的雲端建置而成。</span><span class="sxs-lookup"><span data-stu-id="3fa35-239">It offers configurable and reliable performance, native JavaScript transactional processing, and is built for the cloud with elastic scale.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="3fa35-240">重試機制</span><span class="sxs-lookup"><span data-stu-id="3fa35-240">Retry mechanism</span></span>
<span data-ttu-id="3fa35-241">`DocumentClient` 類別會自動重試失敗的嘗試。</span><span class="sxs-lookup"><span data-stu-id="3fa35-241">The `DocumentClient` class automatically retries failed attempts.</span></span> <span data-ttu-id="3fa35-242">若要設定重試次數和等待時間上限，請設定 [ConnectionPolicy.RetryOptions]。</span><span class="sxs-lookup"><span data-stu-id="3fa35-242">To set the number of retries and the maximum wait time, configure [ConnectionPolicy.RetryOptions].</span></span> <span data-ttu-id="3fa35-243">用戶端引發的例外狀況可能超出重試原則，或不是暫時性的錯誤。</span><span class="sxs-lookup"><span data-stu-id="3fa35-243">Exceptions that the client raises are either beyond the retry policy or are not transient errors.</span></span>

<span data-ttu-id="3fa35-244">如果 Cosmos DB 將用戶端節流，它會傳回 HTTP 429 錯誤。</span><span class="sxs-lookup"><span data-stu-id="3fa35-244">If Cosmos DB throttles the client, it returns an HTTP 429 error.</span></span> <span data-ttu-id="3fa35-245">檢查 `DocumentClientException` 中的狀態碼。</span><span class="sxs-lookup"><span data-stu-id="3fa35-245">Check the status code in the `DocumentClientException`.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="3fa35-246">原則組態</span><span class="sxs-lookup"><span data-stu-id="3fa35-246">Policy configuration</span></span>
<span data-ttu-id="3fa35-247">下表顯示 `RetryOptions` 類別的預設設定。</span><span class="sxs-lookup"><span data-stu-id="3fa35-247">The following table shows the default settings for the `RetryOptions` class.</span></span>

| <span data-ttu-id="3fa35-248">設定</span><span class="sxs-lookup"><span data-stu-id="3fa35-248">Setting</span></span> | <span data-ttu-id="3fa35-249">預設值</span><span class="sxs-lookup"><span data-stu-id="3fa35-249">Default value</span></span> | <span data-ttu-id="3fa35-250">說明</span><span class="sxs-lookup"><span data-stu-id="3fa35-250">Description</span></span> |
| --- | --- | --- |
| <span data-ttu-id="3fa35-251">MaxRetryAttemptsOnThrottledRequests</span><span class="sxs-lookup"><span data-stu-id="3fa35-251">MaxRetryAttemptsOnThrottledRequests</span></span> |<span data-ttu-id="3fa35-252">9</span><span class="sxs-lookup"><span data-stu-id="3fa35-252">9</span></span> |<span data-ttu-id="3fa35-253">如果要求因為 Cosmos DB 在用戶端上套用速率限制而失敗，重試次數的上限。</span><span class="sxs-lookup"><span data-stu-id="3fa35-253">The maximum number of retries if the request fails because Cosmos DB applied rate limiting on the client.</span></span> |
| <span data-ttu-id="3fa35-254">MaxRetryWaitTimeInSeconds</span><span class="sxs-lookup"><span data-stu-id="3fa35-254">MaxRetryWaitTimeInSeconds</span></span> |<span data-ttu-id="3fa35-255">30</span><span class="sxs-lookup"><span data-stu-id="3fa35-255">30</span></span> |<span data-ttu-id="3fa35-256">最大重試時間 (秒)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-256">The maximum retry time in seconds.</span></span> |

### <a name="example"></a><span data-ttu-id="3fa35-257">範例</span><span class="sxs-lookup"><span data-stu-id="3fa35-257">Example</span></span>
```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); ;
var options = client.ConnectionPolicy.RetryOptions;
options.MaxRetryAttemptsOnThrottledRequests = 5;
options.MaxRetryWaitTimeInSeconds = 15;
```

### <a name="telemetry"></a><span data-ttu-id="3fa35-258">遙測</span><span class="sxs-lookup"><span data-stu-id="3fa35-258">Telemetry</span></span>
<span data-ttu-id="3fa35-259">系統會透過 .NET **TraceSource**將重試嘗試記錄成非結構化的追蹤訊息。</span><span class="sxs-lookup"><span data-stu-id="3fa35-259">Retry attempts are logged as unstructured trace messages through a .NET **TraceSource**.</span></span> <span data-ttu-id="3fa35-260">您必須設定 **TraceListener** 來擷取事件，並將事件寫入適當的目的地記錄中。</span><span class="sxs-lookup"><span data-stu-id="3fa35-260">You must configure a **TraceListener** to capture the events and write them to a suitable destination log.</span></span>

<span data-ttu-id="3fa35-261">例如，如果您將下列新增至您的 App.config 檔案，則會在與可執行檔相同位置的文字檔案中產生追蹤：</span><span class="sxs-lookup"><span data-stu-id="3fa35-261">For example, if you add the following to your App.config file, traces will be generated in a text file in the same location as the executable:</span></span>

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

## <a name="event-hubs"></a><span data-ttu-id="3fa35-262">事件中樞</span><span class="sxs-lookup"><span data-stu-id="3fa35-262">Event Hubs</span></span>

<span data-ttu-id="3fa35-263">Azure 事件中樞是大規模的遙測擷取服務，能夠收集、轉換及儲存數百萬個事件。</span><span class="sxs-lookup"><span data-stu-id="3fa35-263">Azure Event Hubs is a hyper-scale telemetry ingestion service that collects, transforms, and stores millions of events.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="3fa35-264">重試機制</span><span class="sxs-lookup"><span data-stu-id="3fa35-264">Retry mechanism</span></span>
<span data-ttu-id="3fa35-265">Azure 事件中樞用戶端程式庫中的重試行為是由 `EventHubClient` 類別上的 `RetryPolicy` 屬性控制。</span><span class="sxs-lookup"><span data-stu-id="3fa35-265">Retry behavior in the Azure Event Hubs Client Library is controlled by the `RetryPolicy` property on the `EventHubClient` class.</span></span> <span data-ttu-id="3fa35-266">當 Azure 事件中樞傳回暫時的 `EventHubsException` 或 `OperationCanceledException` 時，預設原則會以指數輪詢重試。</span><span class="sxs-lookup"><span data-stu-id="3fa35-266">The default policy retries with exponential backoff when Azure Event Hub returns a transient `EventHubsException` or an `OperationCanceledException`.</span></span>

### <a name="example"></a><span data-ttu-id="3fa35-267">範例</span><span class="sxs-lookup"><span data-stu-id="3fa35-267">Example</span></span>
```csharp
EventHubClient client = EventHubClient.CreateFromConnectionString("[event_hub_connection_string]");
client.RetryPolicy = RetryPolicy.Default;
```

### <a name="more-information"></a><span data-ttu-id="3fa35-268">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="3fa35-268">More information</span></span>
[<span data-ttu-id="3fa35-269">Azure 事件中樞的 .NET 標準用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="3fa35-269"> .NET Standard client library for Azure Event Hubs</span></span>](https://github.com/Azure/azure-event-hubs-dotnet)

## <a name="iot-hub"></a><span data-ttu-id="3fa35-270">IoT 中樞</span><span class="sxs-lookup"><span data-stu-id="3fa35-270">IoT Hub</span></span>

<span data-ttu-id="3fa35-271">Azure IoT 中樞是一種用於連接、監視和管理裝置以開發物聯網 (IoT) 應用程式的服務。</span><span class="sxs-lookup"><span data-stu-id="3fa35-271">Azure IoT Hub is a service for connecting, monitoring, and managing devices to develop Internet of Things (IoT) applications.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="3fa35-272">重試機制</span><span class="sxs-lookup"><span data-stu-id="3fa35-272">Retry mechanism</span></span>

<span data-ttu-id="3fa35-273">Azure IoT 裝置 SDK 可以偵測網路、通訊協定或應用程式中的錯誤。</span><span class="sxs-lookup"><span data-stu-id="3fa35-273">The Azure IoT device SDK can detect errors in the network, protocol, or application.</span></span> <span data-ttu-id="3fa35-274">根據錯誤類型，SDK 會檢查是否需要執行重試。</span><span class="sxs-lookup"><span data-stu-id="3fa35-274">Based on the error type, the SDK checks whether a retry needs to be performed.</span></span> <span data-ttu-id="3fa35-275">如果該錯誤可以*復原*，則 SDK 開始使用設定的重試原則進行重試。</span><span class="sxs-lookup"><span data-stu-id="3fa35-275">If the error is *recoverable*, the SDK begins to retry using the configured retry policy.</span></span>

<span data-ttu-id="3fa35-276">預設的重試原則是*具隨機抖動的指數後退*，但可以加以設定。</span><span class="sxs-lookup"><span data-stu-id="3fa35-276">The default retry policy is *exponential back-off with random jitter*, but it can be configured.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="3fa35-277">原則組態</span><span class="sxs-lookup"><span data-stu-id="3fa35-277">Policy configuration</span></span>

<span data-ttu-id="3fa35-278">原則設定因語言而異。</span><span class="sxs-lookup"><span data-stu-id="3fa35-278">Policy configuration differs by language.</span></span> <span data-ttu-id="3fa35-279">如需詳細資訊，請參閱 [IoT 中樞重試原則設定](/azure/iot-hub/iot-hub-reliability-features-in-sdks#retry-policy-apis)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-279">For more details, see [IoT Hub retry policy configuration](/azure/iot-hub/iot-hub-reliability-features-in-sdks#retry-policy-apis).</span></span>

### <a name="more-information"></a><span data-ttu-id="3fa35-280">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="3fa35-280">More information</span></span>

* [<span data-ttu-id="3fa35-281">IoT 中樞重試原則</span><span class="sxs-lookup"><span data-stu-id="3fa35-281">IoT Hub retry policy</span></span>](/azure/iot-hub/iot-hub-reliability-features-in-sdks)
* [<span data-ttu-id="3fa35-282">IoT 中樞裝置中斷連線的疑難排解</span><span class="sxs-lookup"><span data-stu-id="3fa35-282">Troubleshoot IoT Hub device disconnection</span></span>](/azure/iot-hub/iot-hub-troubleshoot-connectivity)

## <a name="azure-redis-cache"></a><span data-ttu-id="3fa35-283">Azure Redis 快取</span><span class="sxs-lookup"><span data-stu-id="3fa35-283">Azure Redis Cache</span></span>
<span data-ttu-id="3fa35-284">Azure Redis 快取是快速資料存取與低延遲性的快取服務，以普遍的開放原始碼 Redis 快取為基礎。</span><span class="sxs-lookup"><span data-stu-id="3fa35-284">Azure Redis Cache is a fast data access and low latency cache service based on the popular open source Redis Cache.</span></span> <span data-ttu-id="3fa35-285">它很安全，且由 Microsoft 管理，可使用 Azure 的任何應用程式存取。</span><span class="sxs-lookup"><span data-stu-id="3fa35-285">It is secure, managed by Microsoft, and is accessible from any application in Azure.</span></span>

<span data-ttu-id="3fa35-286">本節中的指引以使用 StackExchange.Redis 用戶端來存取快取為基礎。</span><span class="sxs-lookup"><span data-stu-id="3fa35-286">The guidance in this section is based on using the StackExchange.Redis client to access the cache.</span></span> <span data-ttu-id="3fa35-287">有關其他適合的用戶端清單，請參閱 [Redis 網站](https://redis.io/clients)，這些用戶端可能有不同的重試機制。</span><span class="sxs-lookup"><span data-stu-id="3fa35-287">A list of other suitable clients can be found on the [Redis website](https://redis.io/clients), and these may have different retry mechanisms.</span></span>

<span data-ttu-id="3fa35-288">請注意 StackExchange.Redis 用戶端透過單一連接使用多工。</span><span class="sxs-lookup"><span data-stu-id="3fa35-288">Note that the StackExchange.Redis client uses multiplexing through a single connection.</span></span> <span data-ttu-id="3fa35-289">建議用法是在應用程式啟動時建立用戶端的執行個體，並對快取的所有作業使用此執行個體。</span><span class="sxs-lookup"><span data-stu-id="3fa35-289">The recommended usage is to create an instance of the client at application startup and use this instance for all operations against the cache.</span></span> <span data-ttu-id="3fa35-290">基於這個理由，對快取只連接一次，且因此本節中指引的所有一切都與此初始連接的重試原則有關，不適用於存取快取的每個作業。</span><span class="sxs-lookup"><span data-stu-id="3fa35-290">For this reason, the connection to the cache is made only once, and so all of the guidance in this section is related to the retry policy for this initial connection—and not for each operation that accesses the cache.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="3fa35-291">重試機制</span><span class="sxs-lookup"><span data-stu-id="3fa35-291">Retry mechanism</span></span>
<span data-ttu-id="3fa35-292">StackExchange.Redis 用戶端使用透過一組選項設定的連線管理員類別，包括：</span><span class="sxs-lookup"><span data-stu-id="3fa35-292">The StackExchange.Redis client uses a connection manager class that is configured through a set of options, including:</span></span>

- <span data-ttu-id="3fa35-293">**ConnectRetry**。</span><span class="sxs-lookup"><span data-stu-id="3fa35-293">**ConnectRetry**.</span></span> <span data-ttu-id="3fa35-294">重試快取失敗連接的次數。</span><span class="sxs-lookup"><span data-stu-id="3fa35-294">The number of times a failed connection to the cache will be retried.</span></span>
- <span data-ttu-id="3fa35-295">**ReconnectRetryPolicy**。</span><span class="sxs-lookup"><span data-stu-id="3fa35-295">**ReconnectRetryPolicy**.</span></span> <span data-ttu-id="3fa35-296">要使用的重試策略。</span><span class="sxs-lookup"><span data-stu-id="3fa35-296">The retry strategy to use.</span></span>
- <span data-ttu-id="3fa35-297">**ConnectTimeout**。</span><span class="sxs-lookup"><span data-stu-id="3fa35-297">**ConnectTimeout**.</span></span> <span data-ttu-id="3fa35-298">最長等待時間，以毫秒為單位。</span><span class="sxs-lookup"><span data-stu-id="3fa35-298">The maximum waiting time in milliseconds.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="3fa35-299">原則組態</span><span class="sxs-lookup"><span data-stu-id="3fa35-299">Policy configuration</span></span>
<span data-ttu-id="3fa35-300">重試原則是以程式設計的方式設定的，做法是先設定用戶端的選項，再連接至快取。</span><span class="sxs-lookup"><span data-stu-id="3fa35-300">Retry policies are configured programmatically by setting the options for the client before connecting to the cache.</span></span> <span data-ttu-id="3fa35-301">做法是建立 **ConfigurationOptions** 類別的執行個體、填入其屬性，並將它傳遞給 **Connect** 方法。</span><span class="sxs-lookup"><span data-stu-id="3fa35-301">This can be done by creating an instance of the **ConfigurationOptions** class, populating its properties, and passing it to the **Connect** method.</span></span>

<span data-ttu-id="3fa35-302">內建類別支援線性 (固定) 延遲重試間隔以及隨機指數輪詢重試間隔。</span><span class="sxs-lookup"><span data-stu-id="3fa35-302">The built-in classes support linear (constant) delay and exponential backoff with randomized retry intervals.</span></span> <span data-ttu-id="3fa35-303">您也可以藉由實作 **IReconnectRetryPolicy** 介面建立自訂重試原則。</span><span class="sxs-lookup"><span data-stu-id="3fa35-303">You can also create a custom retry policy by implementing the **IReconnectRetryPolicy** interface.</span></span>

<span data-ttu-id="3fa35-304">下列範例使用指數輪詢設定重試策略。</span><span class="sxs-lookup"><span data-stu-id="3fa35-304">The following example configures a retry strategy using exponential backoff.</span></span>

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

<span data-ttu-id="3fa35-305">或者，您可以將選項指定為字串，並將此字串傳遞到 **Connect** 方法。</span><span class="sxs-lookup"><span data-stu-id="3fa35-305">Alternatively, you can specify the options as a string, and pass this to the **Connect** method.</span></span> <span data-ttu-id="3fa35-306">請注意，**ReconnectRetryPolicy** 屬性不能這樣設定，只能透過程式碼。</span><span class="sxs-lookup"><span data-stu-id="3fa35-306">Note that the **ReconnectRetryPolicy** property cannot be set this way, only through code.</span></span>

```csharp
var options = "localhost,connectRetry=3,connectTimeout=2000";
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

<span data-ttu-id="3fa35-307">連線至快取時，您也可以直接指定選項。</span><span class="sxs-lookup"><span data-stu-id="3fa35-307">You can also specify options directly when you connect to the cache.</span></span>

```csharp
var conn = ConnectionMultiplexer.Connect("redis0:6380,redis1:6380,connectRetry=3");
```

<span data-ttu-id="3fa35-308">如需詳細資訊，請參閱 StackExchange.Redis 文件的 [Stack Exchange.Redis 設定](https://stackexchange.github.io/StackExchange.Redis/Configuration) (英文)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-308">For more information, see [Stack Exchange Redis Configuration](https://stackexchange.github.io/StackExchange.Redis/Configuration) in the StackExchange.Redis documentation.</span></span>

<span data-ttu-id="3fa35-309">下表顯示內建重試原則的預設設定。</span><span class="sxs-lookup"><span data-stu-id="3fa35-309">The following table shows the default settings for the built-in retry policy.</span></span>

| <span data-ttu-id="3fa35-310">**內容**</span><span class="sxs-lookup"><span data-stu-id="3fa35-310">**Context**</span></span> | <span data-ttu-id="3fa35-311">**設定**</span><span class="sxs-lookup"><span data-stu-id="3fa35-311">**Setting**</span></span> | <span data-ttu-id="3fa35-312">**預設值**</span><span class="sxs-lookup"><span data-stu-id="3fa35-312">**Default value**</span></span><br /><span data-ttu-id="3fa35-313">(v 1.2.2)</span><span class="sxs-lookup"><span data-stu-id="3fa35-313">(v 1.2.2)</span></span> | <span data-ttu-id="3fa35-314">**意義**</span><span class="sxs-lookup"><span data-stu-id="3fa35-314">**Meaning**</span></span> |
| --- | --- | --- | --- |
| <span data-ttu-id="3fa35-315">ConfigurationOptions</span><span class="sxs-lookup"><span data-stu-id="3fa35-315">ConfigurationOptions</span></span> |<span data-ttu-id="3fa35-316">ConnectRetry</span><span class="sxs-lookup"><span data-stu-id="3fa35-316">ConnectRetry</span></span><br /><br /><span data-ttu-id="3fa35-317">ConnectTimeout</span><span class="sxs-lookup"><span data-stu-id="3fa35-317">ConnectTimeout</span></span><br /><br /><span data-ttu-id="3fa35-318">SyncTimeout</span><span class="sxs-lookup"><span data-stu-id="3fa35-318">SyncTimeout</span></span><br /><br /><span data-ttu-id="3fa35-319">ReconnectRetryPolicy</span><span class="sxs-lookup"><span data-stu-id="3fa35-319">ReconnectRetryPolicy</span></span> |<span data-ttu-id="3fa35-320">3</span><span class="sxs-lookup"><span data-stu-id="3fa35-320">3</span></span><br /><br /><span data-ttu-id="3fa35-321">最多 5000 毫秒加上 SyncTimeout</span><span class="sxs-lookup"><span data-stu-id="3fa35-321">Maximum 5000 ms plus SyncTimeout</span></span><br /><span data-ttu-id="3fa35-322">1000</span><span class="sxs-lookup"><span data-stu-id="3fa35-322">1000</span></span><br /><br /><span data-ttu-id="3fa35-323">LinearRetry 5000 毫秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-323">LinearRetry 5000 ms</span></span> |<span data-ttu-id="3fa35-324">初始連線作業的重複連線嘗試次數。</span><span class="sxs-lookup"><span data-stu-id="3fa35-324">The number of times to repeat connect attempts during the initial connection operation.</span></span><br /><span data-ttu-id="3fa35-325">連線作業的逾時 (毫秒)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-325">Timeout (ms) for connect operations.</span></span> <span data-ttu-id="3fa35-326">非重試嘗試之間的延遲。</span><span class="sxs-lookup"><span data-stu-id="3fa35-326">Not a delay between retry attempts.</span></span><br /><span data-ttu-id="3fa35-327">允許同步作業的時間 (毫秒)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-327">Time (ms) to allow for synchronous operations.</span></span><br /><br /><span data-ttu-id="3fa35-328">每 5000 毫秒重試一次。</span><span class="sxs-lookup"><span data-stu-id="3fa35-328">Retry every 5000 ms.</span></span>|

> [!NOTE]
> <span data-ttu-id="3fa35-329">針對同步作業，可以將 `SyncTimeout` 加入端對端延遲，但設定值過低可能會導致過多逾時。</span><span class="sxs-lookup"><span data-stu-id="3fa35-329">For synchronous operations, `SyncTimeout` can add to the end-to-end latency, but setting the value too low can cause excessive timeouts.</span></span> <span data-ttu-id="3fa35-330">請參閱[如何針對 Azure Redis 快取進行疑難排解][redis-cache-troubleshoot]。</span><span class="sxs-lookup"><span data-stu-id="3fa35-330">See [How to troubleshoot Azure Redis Cache][redis-cache-troubleshoot].</span></span> <span data-ttu-id="3fa35-331">一般來說，請避免使用同步作業，改為使用非同步作業。</span><span class="sxs-lookup"><span data-stu-id="3fa35-331">In general, avoid using synchronous operations, and use asynchronous operations instead.</span></span> <span data-ttu-id="3fa35-332">如需詳細資訊，請參閱 [管線與多工器](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/PipelinesMultiplexers.md)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-332">For more information see [Pipelines and Multiplexers](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/PipelinesMultiplexers.md).</span></span>
>
>

### <a name="retry-usage-guidance"></a><span data-ttu-id="3fa35-333">重試使用指引</span><span class="sxs-lookup"><span data-stu-id="3fa35-333">Retry usage guidance</span></span>
<span data-ttu-id="3fa35-334">使用 Azure Redis 快取時請考慮下列指引：</span><span class="sxs-lookup"><span data-stu-id="3fa35-334">Consider the following guidelines when using Azure Redis Cache:</span></span>

* <span data-ttu-id="3fa35-335">StackExchange Redis 用戶端會管理自己的重試，但只有在應用程式第一次啟動時對快取建立連接時才會。</span><span class="sxs-lookup"><span data-stu-id="3fa35-335">The StackExchange Redis client manages its own retries, but only when establishing a connection to the cache when the application first starts.</span></span> <span data-ttu-id="3fa35-336">您可以設定連線逾時、重試嘗試次數、重試之間的間隔時間來建立這個連線，但重試原則不適用於對快取的作業。</span><span class="sxs-lookup"><span data-stu-id="3fa35-336">You can configure the connection timeout, the number of retry attempts, and the time between retries to establish this connection, but the retry policy does not apply to operations against the cache.</span></span>
* <span data-ttu-id="3fa35-337">請不要使用大量的重試嘗試，而是考慮改為存取原始的資料來源來回復。</span><span class="sxs-lookup"><span data-stu-id="3fa35-337">Instead of using a large number of retry attempts, consider falling back by accessing the original data source instead.</span></span>

### <a name="telemetry"></a><span data-ttu-id="3fa35-338">遙測</span><span class="sxs-lookup"><span data-stu-id="3fa35-338">Telemetry</span></span>
<span data-ttu-id="3fa35-339">您可以使用 **TextWriter**收集連接 (而非其他作業) 的資訊。</span><span class="sxs-lookup"><span data-stu-id="3fa35-339">You can collect information about connections (but not other operations) using a **TextWriter**.</span></span>

```csharp
var writer = new StringWriter();
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

<span data-ttu-id="3fa35-340">這產生的輸出範例如下所示。</span><span class="sxs-lookup"><span data-stu-id="3fa35-340">An example of the output this generates is shown below.</span></span>

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

### <a name="examples"></a><span data-ttu-id="3fa35-341">範例</span><span class="sxs-lookup"><span data-stu-id="3fa35-341">Examples</span></span>
<span data-ttu-id="3fa35-342">下列程式碼範例會設定初始化 StackExchange.Redis 用戶端時的重試之間的固定 (線性) 延遲。</span><span class="sxs-lookup"><span data-stu-id="3fa35-342">The following code example configures a constant (linear) delay between retries when initializing the StackExchange.Redis client.</span></span> <span data-ttu-id="3fa35-343">此範例示範如何使用 **ConfigurationOptions** 執行個體來設定組態。</span><span class="sxs-lookup"><span data-stu-id="3fa35-343">This example shows how to set the configuration using a **ConfigurationOptions** instance.</span></span>

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

<span data-ttu-id="3fa35-344">此範例透過將選項指定為字串來設定組態。</span><span class="sxs-lookup"><span data-stu-id="3fa35-344">The next example sets the configuration by specifying the options as a string.</span></span> <span data-ttu-id="3fa35-345">連線逾時是等待連線到快取的時間上限，不是重試嘗試之間的延遲。</span><span class="sxs-lookup"><span data-stu-id="3fa35-345">The connection timeout is the maximum period of time to wait for a connection to the cache, not the delay between retry attempts.</span></span> <span data-ttu-id="3fa35-346">請注意，**ReconnectRetryPolicy** 屬性只能透過程式碼設定。</span><span class="sxs-lookup"><span data-stu-id="3fa35-346">Note that the **ReconnectRetryPolicy** property can only be set by code.</span></span>

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

<span data-ttu-id="3fa35-347">如需其他範例，請參閱專案網站上的 [組態](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Configuration.md) 。</span><span class="sxs-lookup"><span data-stu-id="3fa35-347">For more examples, see [Configuration](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Configuration.md) on the project website.</span></span>

### <a name="more-information"></a><span data-ttu-id="3fa35-348">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="3fa35-348">More information</span></span>
* [<span data-ttu-id="3fa35-349">Redis 網站</span><span class="sxs-lookup"><span data-stu-id="3fa35-349">Redis website</span></span>](https://redis.io/)

## <a name="azure-search"></a><span data-ttu-id="3fa35-350">Azure 搜尋服務</span><span class="sxs-lookup"><span data-stu-id="3fa35-350">Azure Search</span></span>
<span data-ttu-id="3fa35-351">Azure 搜尋服務可用來在網站或應用程式中加入強大且複雜的搜尋功能、快速輕鬆地微調搜尋結果，並建構豐富且微調的排名模型。</span><span class="sxs-lookup"><span data-stu-id="3fa35-351">Azure Search can be used to add powerful and sophisticated search capabilities to a website or application, quickly and easily tune search results, and construct rich and fine-tuned ranking models.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="3fa35-352">重試機制</span><span class="sxs-lookup"><span data-stu-id="3fa35-352">Retry mechanism</span></span>
<span data-ttu-id="3fa35-353">Azure 搜尋服務 SDK 中的重試行為是由 [SearchServiceClient] 和 [SearchIndexClient] 類別上的 `SetRetryPolicy` 方法控制。</span><span class="sxs-lookup"><span data-stu-id="3fa35-353">Retry behavior in the Azure Search SDK is controlled by the `SetRetryPolicy` method on the [SearchServiceClient] and [SearchIndexClient] classes.</span></span> <span data-ttu-id="3fa35-354">當 Azure 搜尋服務傳回 5xx 或 408 (要求逾時) 回應時，預設原則會以指數輪詢重試。</span><span class="sxs-lookup"><span data-stu-id="3fa35-354">The default policy retries with exponential backoff when Azure Search returns a 5xx or 408 (Request Timeout) response.</span></span>

### <a name="telemetry"></a><span data-ttu-id="3fa35-355">遙測</span><span class="sxs-lookup"><span data-stu-id="3fa35-355">Telemetry</span></span>
<span data-ttu-id="3fa35-356">使用 ETW 追蹤或註冊自訂的追蹤提供者。</span><span class="sxs-lookup"><span data-stu-id="3fa35-356">Trace with ETW or by registering a custom trace provider.</span></span> <span data-ttu-id="3fa35-357">如需詳細資訊，請參閱 [AutoRest 文件][autorest]。</span><span class="sxs-lookup"><span data-stu-id="3fa35-357">For more information, see the [AutoRest documentation][autorest].</span></span>

## <a name="service-bus"></a><span data-ttu-id="3fa35-358">服務匯流排</span><span class="sxs-lookup"><span data-stu-id="3fa35-358">Service Bus</span></span>
<span data-ttu-id="3fa35-359">服務匯流排是雲端傳訊平台，能夠提供鬆散結合的訊息交換，以及為在雲端託管或內部部署的應用程式元件改善擴充性和恢復能力。</span><span class="sxs-lookup"><span data-stu-id="3fa35-359">Service Bus is a cloud messaging platform that provides loosely coupled message exchange with improved scale and resiliency for components of an application, whether hosted in the cloud or on-premises.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="3fa35-360">重試機制</span><span class="sxs-lookup"><span data-stu-id="3fa35-360">Retry mechanism</span></span>
<span data-ttu-id="3fa35-361">服務匯流排使用 [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) 基底類別的實作來實作重試。</span><span class="sxs-lookup"><span data-stu-id="3fa35-361">Service Bus implements retries using implementations of the [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) base class.</span></span> <span data-ttu-id="3fa35-362">所有的服務匯流排用戶端皆會公開 **RetryPolicy** 屬性，此屬性可設為 **RetryPolicy** 基底類別的其中一個實作。</span><span class="sxs-lookup"><span data-stu-id="3fa35-362">All of the Service Bus clients expose a **RetryPolicy** property that can be set to one of the implementations of the **RetryPolicy** base class.</span></span> <span data-ttu-id="3fa35-363">內建實作如下：</span><span class="sxs-lookup"><span data-stu-id="3fa35-363">The built-in implementations are:</span></span>

* <span data-ttu-id="3fa35-364">[RetryExponential 類別](/dotnet/api/microsoft.servicebus.retryexponential)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-364">The [RetryExponential Class](/dotnet/api/microsoft.servicebus.retryexponential).</span></span> <span data-ttu-id="3fa35-365">這會公開控制退避間隔的屬性、重試計數，以及用來限制作業完成總時間的 **TerminationTimeBuffer** 屬性。</span><span class="sxs-lookup"><span data-stu-id="3fa35-365">This exposes properties that control the back-off interval, the retry count, and the **TerminationTimeBuffer** property that is used to limit the total time for the operation to complete.</span></span>
* <span data-ttu-id="3fa35-366">[NoRetry 類別](/dotnet/api/microsoft.servicebus.noretry)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-366">The [NoRetry Class](/dotnet/api/microsoft.servicebus.noretry).</span></span> <span data-ttu-id="3fa35-367">這用於當不需要在服務匯流排 API 層級重試時，例如當在批次或多步驟作業期間由另一個處理序管理重試時。</span><span class="sxs-lookup"><span data-stu-id="3fa35-367">This is used when retries at the Service Bus API level are not required, such as when retries are managed by another process as part of a batch or multiple step operation.</span></span>

<span data-ttu-id="3fa35-368">服務匯流排動作會傳回某個範圍的例外狀況，如[服務匯流排傳訊例外狀況](/azure/service-bus-messaging/service-bus-messaging-exceptions)中所列的清單。</span><span class="sxs-lookup"><span data-stu-id="3fa35-368">Service Bus actions can return a range of exceptions, as listed in [Service Bus messaging exceptions](/azure/service-bus-messaging/service-bus-messaging-exceptions).</span></span> <span data-ttu-id="3fa35-369">此清單會提供這些重試作業的表示是否適合的資訊。</span><span class="sxs-lookup"><span data-stu-id="3fa35-369">The list provides information about which if these indicate that retrying the operation is appropriate.</span></span> <span data-ttu-id="3fa35-370">例如， **ServerBusyException** 表示用戶端應等待一段時間後，再重試作業。</span><span class="sxs-lookup"><span data-stu-id="3fa35-370">For example, a **ServerBusyException** indicates that the client should wait for a period of time, then retry the operation.</span></span> <span data-ttu-id="3fa35-371">**ServerBusyException** 的發生也會導致服務匯流排切換成不同的模式，在該模式中會在計算出的重試延遲中額外加上 10 秒的延遲。</span><span class="sxs-lookup"><span data-stu-id="3fa35-371">The occurrence of a **ServerBusyException** also causes Service Bus to switch to a different mode, in which an extra 10-second delay is added to the computed retry delays.</span></span> <span data-ttu-id="3fa35-372">此模式會在短時間後重設。</span><span class="sxs-lookup"><span data-stu-id="3fa35-372">This mode is reset after a short period.</span></span>

<span data-ttu-id="3fa35-373">從服務匯流排傳回的例外狀況會公開 **IsTransient** 屬性，以表示用戶端是否應該重試作業。</span><span class="sxs-lookup"><span data-stu-id="3fa35-373">The exceptions returned from Service Bus expose the **IsTransient** property that indicates if the client should retry the operation.</span></span> <span data-ttu-id="3fa35-374">內建的 **RetryExponential** 原則依賴 **MessagingException** 類別中的 **IsTransient** 屬性，也就是所有服務匯流排例外狀況的基底類別。</span><span class="sxs-lookup"><span data-stu-id="3fa35-374">The built-in **RetryExponential** policy relies on the **IsTransient** property in the **MessagingException** class, which is the base class for all Service Bus exceptions.</span></span> <span data-ttu-id="3fa35-375">如果您建立 **RetryPolicy** 基底類別的自訂實作，您可以使用例外狀況類型和 **IsTransient** 屬性的組合，藉此更細微地控制重試動作。</span><span class="sxs-lookup"><span data-stu-id="3fa35-375">If you create custom implementations of the **RetryPolicy** base class you could use a combination of the exception type and the **IsTransient** property to provide more fine-grained control over retry actions.</span></span> <span data-ttu-id="3fa35-376">例如，您可能會偵測到 **QuotaExceededException** ，並先採取動作清空佇列，再重試將訊息傳送到佇列。</span><span class="sxs-lookup"><span data-stu-id="3fa35-376">For example, you could detect a **QuotaExceededException** and take action to drain the queue before retrying sending a message to it.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="3fa35-377">原則組態</span><span class="sxs-lookup"><span data-stu-id="3fa35-377">Policy configuration</span></span>
<span data-ttu-id="3fa35-378">重試原則是以程式設計的方式設定的，並可設為 **NamespaceManager** 與 **MessagingFactory** 兩者的預設原則，或為每個傳訊用戶端個別設為預設原則。</span><span class="sxs-lookup"><span data-stu-id="3fa35-378">Retry policies are set programmatically, and can be set as a default policy for a **NamespaceManager** and for a **MessagingFactory**, or individually for each messaging client.</span></span> <span data-ttu-id="3fa35-379">若要設定傳訊工作階段的預設重試原則，請設定 **NamespaceManager** 的 **RetryPolicy**。</span><span class="sxs-lookup"><span data-stu-id="3fa35-379">To set the default retry policy for a messaging session you set the **RetryPolicy** of the **NamespaceManager**.</span></span>

```csharp
namespaceManager.Settings.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                                                maxBackoff: TimeSpan.FromSeconds(30),
                                                                maxRetryCount: 3);
```

<span data-ttu-id="3fa35-380">若要為所有從傳訊處理站建立的用戶端設定預設重試原則，請設定 **MessagingFactory** 的 **RetryPolicy**。</span><span class="sxs-lookup"><span data-stu-id="3fa35-380">To set the default retry policy for all clients created from a messaging factory, you set the **RetryPolicy** of the **MessagingFactory**.</span></span>

```csharp
messagingFactory.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                                    maxBackoff: TimeSpan.FromSeconds(30),
                                                    maxRetryCount: 3);
```

<span data-ttu-id="3fa35-381">若要為傳訊用戶端設定重試原則，或覆寫其預設原則，請使用所需原則類別的執行個體設定其 **RetryPolicy** 屬性：</span><span class="sxs-lookup"><span data-stu-id="3fa35-381">To set the retry policy for a messaging client, or to override its default policy, you set its **RetryPolicy** property using an instance of the required policy class:</span></span>

```csharp
client.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                            maxBackoff: TimeSpan.FromSeconds(30),
                                            maxRetryCount: 3);
```

<span data-ttu-id="3fa35-382">無法在個別的作業層級設定重試原則。</span><span class="sxs-lookup"><span data-stu-id="3fa35-382">The retry policy cannot be set at the individual operation level.</span></span> <span data-ttu-id="3fa35-383">它適用於傳訊用戶端的所有作業。</span><span class="sxs-lookup"><span data-stu-id="3fa35-383">It applies to all operations for the messaging client.</span></span>
<span data-ttu-id="3fa35-384">下表顯示內建重試原則的預設設定。</span><span class="sxs-lookup"><span data-stu-id="3fa35-384">The following table shows the default settings for the built-in retry policy.</span></span>

| <span data-ttu-id="3fa35-385">設定</span><span class="sxs-lookup"><span data-stu-id="3fa35-385">Setting</span></span> | <span data-ttu-id="3fa35-386">預設值</span><span class="sxs-lookup"><span data-stu-id="3fa35-386">Default value</span></span> | <span data-ttu-id="3fa35-387">意義</span><span class="sxs-lookup"><span data-stu-id="3fa35-387">Meaning</span></span> |
|---------|---------------|---------|
| <span data-ttu-id="3fa35-388">原則</span><span class="sxs-lookup"><span data-stu-id="3fa35-388">Policy</span></span> | <span data-ttu-id="3fa35-389">指數</span><span class="sxs-lookup"><span data-stu-id="3fa35-389">Exponential</span></span> | <span data-ttu-id="3fa35-390">指數輪詢。</span><span class="sxs-lookup"><span data-stu-id="3fa35-390">Exponential back-off.</span></span> |
| <span data-ttu-id="3fa35-391">MinimalBackoff</span><span class="sxs-lookup"><span data-stu-id="3fa35-391">MinimalBackoff</span></span> | <span data-ttu-id="3fa35-392">0</span><span class="sxs-lookup"><span data-stu-id="3fa35-392">0</span></span> | <span data-ttu-id="3fa35-393">輪詢間隔最小值。</span><span class="sxs-lookup"><span data-stu-id="3fa35-393">Minimum back-off interval.</span></span> <span data-ttu-id="3fa35-394">系統會將此值新增至透過 deltaBackoff 所計算出的重試間隔。</span><span class="sxs-lookup"><span data-stu-id="3fa35-394">This is added to the retry interval computed from deltaBackoff.</span></span> |
| <span data-ttu-id="3fa35-395">MaximumBackoff</span><span class="sxs-lookup"><span data-stu-id="3fa35-395">MaximumBackoff</span></span> | <span data-ttu-id="3fa35-396">30 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-396">30 seconds</span></span> | <span data-ttu-id="3fa35-397">輪詢間隔最大值。</span><span class="sxs-lookup"><span data-stu-id="3fa35-397">Maximum back-off interval.</span></span> <span data-ttu-id="3fa35-398">如果計算的重試間隔大於 MaxBackoff，則會使用 MaximumBackoff。</span><span class="sxs-lookup"><span data-stu-id="3fa35-398">MaximumBackoff is used if the computed retry interval is greater than MaxBackoff.</span></span> |
| <span data-ttu-id="3fa35-399">DeltaBackoff</span><span class="sxs-lookup"><span data-stu-id="3fa35-399">DeltaBackoff</span></span> | <span data-ttu-id="3fa35-400">3 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-400">3 seconds</span></span> | <span data-ttu-id="3fa35-401">重試之間的輪詢間隔。</span><span class="sxs-lookup"><span data-stu-id="3fa35-401">Back-off interval between retries.</span></span> <span data-ttu-id="3fa35-402">此時間範圍的倍數將用於後續的重試次數。</span><span class="sxs-lookup"><span data-stu-id="3fa35-402">Multiples of this timespan will be used for subsequent retry attempts.</span></span> |
| <span data-ttu-id="3fa35-403">TimeBuffer</span><span class="sxs-lookup"><span data-stu-id="3fa35-403">TimeBuffer</span></span> | <span data-ttu-id="3fa35-404">5 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-404">5 seconds</span></span> | <span data-ttu-id="3fa35-405">與重試相關聯的終止時間緩衝區。</span><span class="sxs-lookup"><span data-stu-id="3fa35-405">The termination time buffer associated with the retry.</span></span> <span data-ttu-id="3fa35-406">如果剩餘時間小於 TimeBuffer，則會放棄重試次數。</span><span class="sxs-lookup"><span data-stu-id="3fa35-406">Retry attempts will be abandoned if the remaining time is less than TimeBuffer.</span></span> |
| <span data-ttu-id="3fa35-407">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="3fa35-407">MaxRetryCount</span></span> | <span data-ttu-id="3fa35-408">10</span><span class="sxs-lookup"><span data-stu-id="3fa35-408">10</span></span> | <span data-ttu-id="3fa35-409">重試次數上限。</span><span class="sxs-lookup"><span data-stu-id="3fa35-409">The maximum number of retries.</span></span> |
| <span data-ttu-id="3fa35-410">ServerBusyBaseSleepTime</span><span class="sxs-lookup"><span data-stu-id="3fa35-410">ServerBusyBaseSleepTime</span></span> | <span data-ttu-id="3fa35-411">10 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-411">10 seconds</span></span> | <span data-ttu-id="3fa35-412">如果上次發生的例外狀況為 **ServerBusyException**，系統會將這個值新增至計算的重試間隔。</span><span class="sxs-lookup"><span data-stu-id="3fa35-412">If the last exception encountered was **ServerBusyException**, this value will be added to the computed retry interval.</span></span> <span data-ttu-id="3fa35-413">無法變更此值。</span><span class="sxs-lookup"><span data-stu-id="3fa35-413">This value cannot be changed.</span></span> |

### <a name="retry-usage-guidance"></a><span data-ttu-id="3fa35-414">重試使用指引</span><span class="sxs-lookup"><span data-stu-id="3fa35-414">Retry usage guidance</span></span>
<span data-ttu-id="3fa35-415">使用服務匯流排時請考慮下列指引：</span><span class="sxs-lookup"><span data-stu-id="3fa35-415">Consider the following guidelines when using Service Bus:</span></span>

* <span data-ttu-id="3fa35-416">當使用內建 **RetryExponential** 實作時，若原則回應伺服器忙碌例外狀況並自動切換到適當的重試模式時，請不要實作後援作業。</span><span class="sxs-lookup"><span data-stu-id="3fa35-416">When using the built-in **RetryExponential** implementation, do not implement a fallback operation as the policy reacts to Server Busy exceptions and automatically switches to an appropriate retry mode.</span></span>
* <span data-ttu-id="3fa35-417">服務匯流排支援名為「配對命名空間」的功能，如果主要命名空間中的佇列失敗，此功能會實作自動容錯移轉到備份的佇列。</span><span class="sxs-lookup"><span data-stu-id="3fa35-417">Service Bus supports a feature called Paired Namespaces, which implements automatic failover to a backup queue in a separate namespace if the queue in the primary namespace fails.</span></span> <span data-ttu-id="3fa35-418">主要佇列復原時，次要佇列中的訊息會傳回主要佇列。</span><span class="sxs-lookup"><span data-stu-id="3fa35-418">Messages from the secondary queue can be sent back to the primary queue when it recovers.</span></span> <span data-ttu-id="3fa35-419">此功能有助於處理暫時性錯誤。</span><span class="sxs-lookup"><span data-stu-id="3fa35-419">This feature helps to address transient failures.</span></span> <span data-ttu-id="3fa35-420">如需詳細資訊，請參閱 [非同步傳訊模式和高可用性](/azure/service-bus-messaging/service-bus-async-messaging)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-420">For more information, see [Asynchronous Messaging Patterns and High Availability](/azure/service-bus-messaging/service-bus-async-messaging).</span></span>

<span data-ttu-id="3fa35-421">請考慮從重試作業的下列設定開始。</span><span class="sxs-lookup"><span data-stu-id="3fa35-421">Consider starting with following settings for retrying operations.</span></span> <span data-ttu-id="3fa35-422">這些是一般用途設定，您應該監視作業並微調其值以符合您自己的需求。</span><span class="sxs-lookup"><span data-stu-id="3fa35-422">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="3fa35-423">Context</span><span class="sxs-lookup"><span data-stu-id="3fa35-423">Context</span></span> | <span data-ttu-id="3fa35-424">延遲最大值範例</span><span class="sxs-lookup"><span data-stu-id="3fa35-424">Example maximum latency</span></span> | <span data-ttu-id="3fa35-425">重試原則</span><span class="sxs-lookup"><span data-stu-id="3fa35-425">Retry policy</span></span> | <span data-ttu-id="3fa35-426">設定</span><span class="sxs-lookup"><span data-stu-id="3fa35-426">Settings</span></span> | <span data-ttu-id="3fa35-427">運作方式</span><span class="sxs-lookup"><span data-stu-id="3fa35-427">How it works</span></span> |
|---------|---------|---------|---------|---------|
| <span data-ttu-id="3fa35-428">互動式、UI 或前景</span><span class="sxs-lookup"><span data-stu-id="3fa35-428">Interactive, UI, or foreground</span></span> | <span data-ttu-id="3fa35-429">2 秒\*</span><span class="sxs-lookup"><span data-stu-id="3fa35-429">2 seconds\*</span></span>  | <span data-ttu-id="3fa35-430">指數</span><span class="sxs-lookup"><span data-stu-id="3fa35-430">Exponential</span></span> | <span data-ttu-id="3fa35-431">MinimumBackoff = 0</span><span class="sxs-lookup"><span data-stu-id="3fa35-431">MinimumBackoff = 0</span></span> <br/> <span data-ttu-id="3fa35-432">MaximumBackoff = 30 秒。</span><span class="sxs-lookup"><span data-stu-id="3fa35-432">MaximumBackoff = 30 sec.</span></span> <br/> <span data-ttu-id="3fa35-433">DeltaBackoff = 300 毫秒。</span><span class="sxs-lookup"><span data-stu-id="3fa35-433">DeltaBackoff = 300 msec.</span></span> <br/> <span data-ttu-id="3fa35-434">TimeBuffer = 300 毫秒。</span><span class="sxs-lookup"><span data-stu-id="3fa35-434">TimeBuffer = 300 msec.</span></span> <br/> <span data-ttu-id="3fa35-435">MaxRetryCount = 2</span><span class="sxs-lookup"><span data-stu-id="3fa35-435">MaxRetryCount = 2</span></span> | <span data-ttu-id="3fa35-436">嘗試 1：延遲 0 秒。</span><span class="sxs-lookup"><span data-stu-id="3fa35-436">Attempt 1: Delay 0 sec.</span></span> <br/> <span data-ttu-id="3fa35-437">嘗試 2：延遲 ~300 毫秒。</span><span class="sxs-lookup"><span data-stu-id="3fa35-437">Attempt 2: Delay ~300 msec.</span></span> <br/> <span data-ttu-id="3fa35-438">嘗試 3：延遲 ~900 毫秒。</span><span class="sxs-lookup"><span data-stu-id="3fa35-438">Attempt 3: Delay ~900 msec.</span></span> |
| <span data-ttu-id="3fa35-439">背景或批次</span><span class="sxs-lookup"><span data-stu-id="3fa35-439">Background or batch</span></span> | <span data-ttu-id="3fa35-440">30 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-440">30 seconds</span></span> | <span data-ttu-id="3fa35-441">指數</span><span class="sxs-lookup"><span data-stu-id="3fa35-441">Exponential</span></span> | <span data-ttu-id="3fa35-442">MinimumBackoff = 1</span><span class="sxs-lookup"><span data-stu-id="3fa35-442">MinimumBackoff = 1</span></span> <br/> <span data-ttu-id="3fa35-443">MaximumBackoff = 30 秒。</span><span class="sxs-lookup"><span data-stu-id="3fa35-443">MaximumBackoff = 30 sec.</span></span> <br/> <span data-ttu-id="3fa35-444">DeltaBackoff = 1.75 秒。</span><span class="sxs-lookup"><span data-stu-id="3fa35-444">DeltaBackoff = 1.75 sec.</span></span> <br/> <span data-ttu-id="3fa35-445">TimeBuffer = 5 秒。</span><span class="sxs-lookup"><span data-stu-id="3fa35-445">TimeBuffer = 5 sec.</span></span> <br/> <span data-ttu-id="3fa35-446">MaxRetryCount = 3</span><span class="sxs-lookup"><span data-stu-id="3fa35-446">MaxRetryCount = 3</span></span> | <span data-ttu-id="3fa35-447">嘗試 1：延遲 ~1 秒。</span><span class="sxs-lookup"><span data-stu-id="3fa35-447">Attempt 1: Delay ~1 sec.</span></span> <br/> <span data-ttu-id="3fa35-448">嘗試 2：延遲 ~3 秒。</span><span class="sxs-lookup"><span data-stu-id="3fa35-448">Attempt 2: Delay ~3 sec.</span></span> <br/> <span data-ttu-id="3fa35-449">嘗試 3：延遲 ~6 毫秒。</span><span class="sxs-lookup"><span data-stu-id="3fa35-449">Attempt 3: Delay ~6 msec.</span></span> <br/> <span data-ttu-id="3fa35-450">嘗試 4：延遲 ~13 毫秒。</span><span class="sxs-lookup"><span data-stu-id="3fa35-450">Attempt 4: Delay ~13 msec.</span></span> |

<span data-ttu-id="3fa35-451">\* 不包括系統收到伺服器忙碌回應時所新增的額外延遲。</span><span class="sxs-lookup"><span data-stu-id="3fa35-451">\* Not including additional delay that is added if a Server Busy response is received.</span></span>

### <a name="telemetry"></a><span data-ttu-id="3fa35-452">遙測</span><span class="sxs-lookup"><span data-stu-id="3fa35-452">Telemetry</span></span>
<span data-ttu-id="3fa35-453">服務匯流排使用 **EventSource**，將重試記錄成 ETW 事件。</span><span class="sxs-lookup"><span data-stu-id="3fa35-453">Service Bus logs retries as ETW events using an **EventSource**.</span></span> <span data-ttu-id="3fa35-454">您必須將 **EventListener** 附加到事件來源，以擷取事件並在效能檢視器中檢視事件。</span><span class="sxs-lookup"><span data-stu-id="3fa35-454">You must attach an **EventListener** to the event source to capture the events and view them in Performance Viewer, or write them to a suitable destination log.</span></span> <span data-ttu-id="3fa35-455">重試事件的格式如下：</span><span class="sxs-lookup"><span data-stu-id="3fa35-455">The retry events are of the following form:</span></span>

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

### <a name="examples"></a><span data-ttu-id="3fa35-456">範例</span><span class="sxs-lookup"><span data-stu-id="3fa35-456">Examples</span></span>
<span data-ttu-id="3fa35-457">下列範例程式碼顯示如何為下列項目設定重試原則：</span><span class="sxs-lookup"><span data-stu-id="3fa35-457">The following code example shows how to set the retry policy for:</span></span>

* <span data-ttu-id="3fa35-458">命名空間管理員。</span><span class="sxs-lookup"><span data-stu-id="3fa35-458">A namespace manager.</span></span> <span data-ttu-id="3fa35-459">此原則適用於該管理員上的所有作業，而且無法針對個別作業覆寫。</span><span class="sxs-lookup"><span data-stu-id="3fa35-459">The policy applies to all operations on that manager, and cannot be overridden for individual operations.</span></span>
* <span data-ttu-id="3fa35-460">傳訊處理站。</span><span class="sxs-lookup"><span data-stu-id="3fa35-460">A messaging factory.</span></span> <span data-ttu-id="3fa35-461">此原則適用於從該處理站建立的所有用戶端，當建立個別的用戶端時無法覆寫此原則。</span><span class="sxs-lookup"><span data-stu-id="3fa35-461">The policy applies to all clients created from that factory, and cannot be overridden when creating individual clients.</span></span>
* <span data-ttu-id="3fa35-462">個別的傳訊用戶端。</span><span class="sxs-lookup"><span data-stu-id="3fa35-462">An individual messaging client.</span></span> <span data-ttu-id="3fa35-463">建立用戶端之後，您可以為該用戶端設定重試原則。</span><span class="sxs-lookup"><span data-stu-id="3fa35-463">After a client has been created, you can set the retry policy for that client.</span></span> <span data-ttu-id="3fa35-464">此原則適用於該用戶端上的所有作業。</span><span class="sxs-lookup"><span data-stu-id="3fa35-464">The policy applies to all operations on that client.</span></span>

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

### <a name="more-information"></a><span data-ttu-id="3fa35-465">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="3fa35-465">More information</span></span>
* [<span data-ttu-id="3fa35-466">非同步傳訊模式和高可用性</span><span class="sxs-lookup"><span data-stu-id="3fa35-466">Asynchronous Messaging Patterns and High Availability</span></span>](/azure/service-bus-messaging/service-bus-async-messaging)

## <a name="service-fabric"></a><span data-ttu-id="3fa35-467">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="3fa35-467">Service Fabric</span></span>

<span data-ttu-id="3fa35-468">分散Service Fabric 叢集中可靠的服務，可防範大部分本文所討論的潛在暫時性錯誤。</span><span class="sxs-lookup"><span data-stu-id="3fa35-468">Distributing reliable services in a Service Fabric cluster guards against most of the potential transient faults discussed in this article.</span></span> <span data-ttu-id="3fa35-469">不過，某些暫時性錯誤仍有可能發生。</span><span class="sxs-lookup"><span data-stu-id="3fa35-469">Some transient faults are still possible, however.</span></span> <span data-ttu-id="3fa35-470">例如，命名服務收到要求時可能正在進行路由變更，造成它擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="3fa35-470">For example, the naming service might be in the middle of a routing change when it gets a request, causing it to throw an exception.</span></span> <span data-ttu-id="3fa35-471">如果相同的要求在 100 毫秒之後才收到，就可能會成功。</span><span class="sxs-lookup"><span data-stu-id="3fa35-471">If the same request comes 100 milliseconds later, it will probably succeed.</span></span>

<span data-ttu-id="3fa35-472">就內部而言，Service Fabric 會管理這種暫時性錯誤。</span><span class="sxs-lookup"><span data-stu-id="3fa35-472">Internally, Service Fabric manages this kind of transient fault.</span></span> <span data-ttu-id="3fa35-473">設定服務時，您可以使用 `OperationRetrySettings` 類別進行某些設定。</span><span class="sxs-lookup"><span data-stu-id="3fa35-473">You can configure some settings by using the `OperationRetrySettings` class while setting up your services.</span></span>  <span data-ttu-id="3fa35-474">下列程式碼為範例。</span><span class="sxs-lookup"><span data-stu-id="3fa35-474">The following code shows an example.</span></span> <span data-ttu-id="3fa35-475">大部分情況下，這並非必要，用預設設定就可以了。</span><span class="sxs-lookup"><span data-stu-id="3fa35-475">In most cases, this should not be necessary, and the default settings will be fine.</span></span>

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

### <a name="more-information"></a><span data-ttu-id="3fa35-476">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="3fa35-476">More information</span></span>

* [<span data-ttu-id="3fa35-477">遠端例外狀況處理</span><span class="sxs-lookup"><span data-stu-id="3fa35-477">Remote Exception Handling</span></span>](https://github.com/Microsoft/azure-docs/blob/master/articles/service-fabric/service-fabric-reliable-services-communication-remoting.md#remoting-exception-handling)

## <a name="sql-database-using-adonet"></a><span data-ttu-id="3fa35-478">使用 ADO.NET 的 SQL Database</span><span class="sxs-lookup"><span data-stu-id="3fa35-478">SQL Database using ADO.NET</span></span>
<span data-ttu-id="3fa35-479">SQL Database 是託管的 SQL 資料庫，有各種大小，並以標準 (共用) 與高階 (非共用) 服務提供。</span><span class="sxs-lookup"><span data-stu-id="3fa35-479">SQL Database is a hosted SQL database available in a range of sizes and as both a standard (shared) and premium (non-shared) service.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="3fa35-480">重試機制</span><span class="sxs-lookup"><span data-stu-id="3fa35-480">Retry mechanism</span></span>
<span data-ttu-id="3fa35-481">SQL Database 在使用 ADO.NET 存取時，沒有內建的重試支援。</span><span class="sxs-lookup"><span data-stu-id="3fa35-481">SQL Database has no built-in support for retries when accessed using ADO.NET.</span></span> <span data-ttu-id="3fa35-482">不過，要求的傳回碼可用來判斷要求失敗的原因。</span><span class="sxs-lookup"><span data-stu-id="3fa35-482">However, the return codes from requests can be used to determine why a request failed.</span></span> <span data-ttu-id="3fa35-483">如需有關 SQL Database 節流的詳細資訊，請參閱 [Azure SQL Database 資源限制](/azure/sql-database/sql-database-resource-limits)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-483">For more information about SQL Database throttling, see [Azure SQL Database resource limits](/azure/sql-database/sql-database-resource-limits).</span></span> <span data-ttu-id="3fa35-484">如需相關錯誤碼的清單，請參閱 [SQL Database 用戶端應用程式的 SQL 錯誤碼](/azure/sql-database/sql-database-develop-error-messages)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-484">For a list of relevant error codes, see [SQL error codes for SQL Database client applications](/azure/sql-database/sql-database-develop-error-messages).</span></span>

<span data-ttu-id="3fa35-485">您可以使用 Polly 程式庫實作 SQL Database 重試。</span><span class="sxs-lookup"><span data-stu-id="3fa35-485">You can use the Polly library to implement retries for SQL Database.</span></span> <span data-ttu-id="3fa35-486">請參閱 [Polly 的暫時性錯誤處理](#transient-fault-handling-with-polly)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-486">See [Transient fault handling with Polly](#transient-fault-handling-with-polly).</span></span>

### <a name="retry-usage-guidance"></a><span data-ttu-id="3fa35-487">重試使用指引</span><span class="sxs-lookup"><span data-stu-id="3fa35-487">Retry usage guidance</span></span>
<span data-ttu-id="3fa35-488">使用 ADO.NET 存取 SQL Database 時，請考慮下列指引：</span><span class="sxs-lookup"><span data-stu-id="3fa35-488">Consider the following guidelines when accessing SQL Database using ADO.NET:</span></span>

* <span data-ttu-id="3fa35-489">選擇適當的服務選項 (共用或高階)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-489">Choose the appropriate service option (shared or premium).</span></span> <span data-ttu-id="3fa35-490">共用的執行個體可能會有延遲時間比一般連接長，及因為共用伺服器有其他租用戶使用而節流的問題。</span><span class="sxs-lookup"><span data-stu-id="3fa35-490">A shared instance may suffer longer than usual connection delays and throttling due to the usage by other tenants of the shared server.</span></span> <span data-ttu-id="3fa35-491">如果需要更可預測的效能和可靠的低延遲作業，請考慮選擇高階選項。</span><span class="sxs-lookup"><span data-stu-id="3fa35-491">If more predictable performance and reliable low latency operations are required, consider choosing the premium option.</span></span>
* <span data-ttu-id="3fa35-492">請確定您是在適當的層級或範圍執行重試，以避免非等冪作業導致資料不一致。</span><span class="sxs-lookup"><span data-stu-id="3fa35-492">Ensure that you perform retries at the appropriate level or scope to avoid non-idempotent operations causing inconsistency in the data.</span></span> <span data-ttu-id="3fa35-493">理想的狀況是，所有的作業應等冪，而因此能夠重複執行，而不會造成不一致。</span><span class="sxs-lookup"><span data-stu-id="3fa35-493">Ideally, all operations should be idempotent so that they can be repeated without causing inconsistency.</span></span> <span data-ttu-id="3fa35-494">若不是此狀況，則應在如果一個作業失敗，則允許復原所有相關變更的層級或範圍中執行重試；例如，從交易範圍內。</span><span class="sxs-lookup"><span data-stu-id="3fa35-494">Where this is not the case, the retry should be performed at a level or scope that allows all related changes to be undone if one operation fails; for example, from within a transactional scope.</span></span> <span data-ttu-id="3fa35-495">如需詳細資訊，請參閱 [雲端服務基礎資料存取層 – 暫時性錯誤處理](https://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-495">For more information, see [Cloud Service Fundamentals Data Access Layer – Transient Fault Handling](https://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee).</span></span>
* <span data-ttu-id="3fa35-496">互動式案例除外，不建議將固定間隔策略搭配 Azure SQL Database 使用；互動式案例中只有一些間隔非常短的重試。</span><span class="sxs-lookup"><span data-stu-id="3fa35-496">A fixed interval strategy is not recommended for use with Azure SQL Database except for interactive scenarios where there are only a few retries at very short intervals.</span></span> <span data-ttu-id="3fa35-497">相反地，請考慮為大部分的案例使用指數退避策略。</span><span class="sxs-lookup"><span data-stu-id="3fa35-497">Instead, consider using an exponential back-off strategy for the majority of scenarios.</span></span>
* <span data-ttu-id="3fa35-498">定義連接時，為連接與命令逾時選擇合適的值。</span><span class="sxs-lookup"><span data-stu-id="3fa35-498">Choose a suitable value for the connection and command timeouts when defining connections.</span></span> <span data-ttu-id="3fa35-499">逾時值過短，會造成資料庫忙碌時連接永遠失敗。</span><span class="sxs-lookup"><span data-stu-id="3fa35-499">Too short a timeout may result in premature failures of connections when the database is busy.</span></span> <span data-ttu-id="3fa35-500">逾時值過長，可能會因為在偵測失敗連接之前等待過久，而造成重試邏輯無法正確運作。</span><span class="sxs-lookup"><span data-stu-id="3fa35-500">Too long a timeout may prevent the retry logic working correctly by waiting too long before detecting a failed connection.</span></span> <span data-ttu-id="3fa35-501">逾時值是端對端延遲的元件；它會有效地加到在重試原則中為每個重試嘗試指定的重試延遲。</span><span class="sxs-lookup"><span data-stu-id="3fa35-501">The value of the timeout is a component of the end-to-end latency; it is effectively added to the retry delay specified in the retry policy for every retry attempt.</span></span>
* <span data-ttu-id="3fa35-502">在重試特定次數後即關閉連接，即使使用的是指數退避策略，然後在新的連接上重試作業。</span><span class="sxs-lookup"><span data-stu-id="3fa35-502">Close the connection after a certain number of retries, even when using an exponential back off retry logic, and retry the operation on a new connection.</span></span> <span data-ttu-id="3fa35-503">在同一個連接上多次重試同一個作業，也是造成連接問題的因素之一。</span><span class="sxs-lookup"><span data-stu-id="3fa35-503">Retrying the same operation multiple times on the same connection can be a factor that contributes to connection problems.</span></span> <span data-ttu-id="3fa35-504">有關此技術的範例，請參閱 [雲端服務基礎資料存取層 – 暫時性錯誤處理](https://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-504">For an example of this technique, see [Cloud Service Fundamentals Data Access Layer – Transient Fault Handling](https://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx).</span></span>
* <span data-ttu-id="3fa35-505">當使用連接共用時 (預設值)，有可能會從共用中選擇同一個連接，即使是在關閉並重新開啟連接後。</span><span class="sxs-lookup"><span data-stu-id="3fa35-505">When connection pooling is in use (the default) there is a chance that the same connection will be chosen from the pool, even after closing and reopening a connection.</span></span> <span data-ttu-id="3fa35-506">如果發生這種情況，則解決的技術是呼叫 **SqlConnection** 類別的 **ClearPool** 方法將連接標示為不可重複使用。</span><span class="sxs-lookup"><span data-stu-id="3fa35-506">If this is the case, a technique to resolve it is to call the **ClearPool** method of the **SqlConnection** class to mark the connection as not reusable.</span></span> <span data-ttu-id="3fa35-507">但只應在數次連接嘗試皆失敗後，及碰到與錯誤連接有關的特定類別暫時性錯誤時，如 SQL 逾時 (錯誤碼 -2)，才這麼做。</span><span class="sxs-lookup"><span data-stu-id="3fa35-507">However, you should do this only after several connection attempts have failed, and only when encountering the specific class of transient failures such as SQL timeouts (error code -2) related to faulty connections.</span></span>
* <span data-ttu-id="3fa35-508">如果資料存取程式碼使用交易做為起始的 **TransactionScope** 執行個體，則重試邏輯應重新開啟連接並啟動新的交易範圍。</span><span class="sxs-lookup"><span data-stu-id="3fa35-508">If the data access code uses transactions initiated as **TransactionScope** instances, the retry logic should reopen the connection and initiate a new transaction scope.</span></span> <span data-ttu-id="3fa35-509">基於這個原因，可以重試的程式碼區塊應包含交易的整個範圍。</span><span class="sxs-lookup"><span data-stu-id="3fa35-509">For this reason, the retryable code block should encompass the entire scope of the transaction.</span></span>

<span data-ttu-id="3fa35-510">請考慮從重試作業的下列設定開始。</span><span class="sxs-lookup"><span data-stu-id="3fa35-510">Consider starting with following settings for retrying operations.</span></span> <span data-ttu-id="3fa35-511">這些是一般用途設定，您應該監視作業並微調其值以符合您自己的需求。</span><span class="sxs-lookup"><span data-stu-id="3fa35-511">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="3fa35-512">**內容**</span><span class="sxs-lookup"><span data-stu-id="3fa35-512">**Context**</span></span> | <span data-ttu-id="3fa35-513">**範例目標 E2E<br />最大延遲**</span><span class="sxs-lookup"><span data-stu-id="3fa35-513">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="3fa35-514">**重試策略**</span><span class="sxs-lookup"><span data-stu-id="3fa35-514">**Retry strategy**</span></span> | <span data-ttu-id="3fa35-515">**設定**</span><span class="sxs-lookup"><span data-stu-id="3fa35-515">**Settings**</span></span> | <span data-ttu-id="3fa35-516">**值**</span><span class="sxs-lookup"><span data-stu-id="3fa35-516">**Values**</span></span> | <span data-ttu-id="3fa35-517">**運作方式**</span><span class="sxs-lookup"><span data-stu-id="3fa35-517">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="3fa35-518">互動式、UI </span><span class="sxs-lookup"><span data-stu-id="3fa35-518">Interactive, UI,</span></span><br /><span data-ttu-id="3fa35-519">或前景</span><span class="sxs-lookup"><span data-stu-id="3fa35-519">or foreground</span></span> |<span data-ttu-id="3fa35-520">2 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-520">2 sec</span></span> |<span data-ttu-id="3fa35-521">FixedInterval</span><span class="sxs-lookup"><span data-stu-id="3fa35-521">FixedInterval</span></span> |<span data-ttu-id="3fa35-522">重試計數</span><span class="sxs-lookup"><span data-stu-id="3fa35-522">Retry count</span></span><br /><span data-ttu-id="3fa35-523">重試間隔</span><span class="sxs-lookup"><span data-stu-id="3fa35-523">Retry interval</span></span><br /><span data-ttu-id="3fa35-524">第一個快速重試</span><span class="sxs-lookup"><span data-stu-id="3fa35-524">First fast retry</span></span> |<span data-ttu-id="3fa35-525">3</span><span class="sxs-lookup"><span data-stu-id="3fa35-525">3</span></span><br /><span data-ttu-id="3fa35-526">500 毫秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-526">500 ms</span></span><br /><span data-ttu-id="3fa35-527">true</span><span class="sxs-lookup"><span data-stu-id="3fa35-527">true</span></span> |<span data-ttu-id="3fa35-528">嘗試 1 - 延遲 0 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-528">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="3fa35-529">嘗試 2 - 延遲 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-529">Attempt 2 - delay 500 ms</span></span><br /><span data-ttu-id="3fa35-530">嘗試 3 - 延遲 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-530">Attempt 3 - delay 500 ms</span></span> |
| <span data-ttu-id="3fa35-531">背景</span><span class="sxs-lookup"><span data-stu-id="3fa35-531">Background</span></span><br /><span data-ttu-id="3fa35-532">或批次</span><span class="sxs-lookup"><span data-stu-id="3fa35-532">or batch</span></span> |<span data-ttu-id="3fa35-533">30 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-533">30 sec</span></span> |<span data-ttu-id="3fa35-534">ExponentialBackoff</span><span class="sxs-lookup"><span data-stu-id="3fa35-534">ExponentialBackoff</span></span> |<span data-ttu-id="3fa35-535">重試計數</span><span class="sxs-lookup"><span data-stu-id="3fa35-535">Retry count</span></span><br /><span data-ttu-id="3fa35-536">最小輪詢</span><span class="sxs-lookup"><span data-stu-id="3fa35-536">Min back-off</span></span><br /><span data-ttu-id="3fa35-537">最大輪詢</span><span class="sxs-lookup"><span data-stu-id="3fa35-537">Max back-off</span></span><br /><span data-ttu-id="3fa35-538">差異輪詢</span><span class="sxs-lookup"><span data-stu-id="3fa35-538">Delta back-off</span></span><br /><span data-ttu-id="3fa35-539">第一個快速重試</span><span class="sxs-lookup"><span data-stu-id="3fa35-539">First fast retry</span></span> |<span data-ttu-id="3fa35-540">5</span><span class="sxs-lookup"><span data-stu-id="3fa35-540">5</span></span><br /><span data-ttu-id="3fa35-541">0 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-541">0 sec</span></span><br /><span data-ttu-id="3fa35-542">60 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-542">60 sec</span></span><br /><span data-ttu-id="3fa35-543">2 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-543">2 sec</span></span><br /><span data-ttu-id="3fa35-544">false</span><span class="sxs-lookup"><span data-stu-id="3fa35-544">false</span></span> |<span data-ttu-id="3fa35-545">嘗試 1 - 延遲 0 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-545">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="3fa35-546">嘗試 2 - 延遲 ~2 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-546">Attempt 2 - delay ~2 sec</span></span><br /><span data-ttu-id="3fa35-547">嘗試 3 - 延遲 ~6 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-547">Attempt 3 - delay ~6 sec</span></span><br /><span data-ttu-id="3fa35-548">嘗試 4 - 延遲 ~14 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-548">Attempt 4 - delay ~14 sec</span></span><br /><span data-ttu-id="3fa35-549">嘗試 5 - 延遲 ~30 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-549">Attempt 5 - delay ~30 sec</span></span> |

> [!NOTE]
> <span data-ttu-id="3fa35-550">端對端延遲目標會假設服務連接的預設逾時值。</span><span class="sxs-lookup"><span data-stu-id="3fa35-550">The end-to-end latency targets assume the default timeout for connections to the service.</span></span> <span data-ttu-id="3fa35-551">如果您指定較長的連接逾時值，則系統會為每個重試嘗試增加這段時間，來延長端對端延遲。</span><span class="sxs-lookup"><span data-stu-id="3fa35-551">If you specify longer connection timeouts, the end-to-end latency will be extended by this additional time for every retry attempt.</span></span>
>
>

### <a name="examples"></a><span data-ttu-id="3fa35-552">範例</span><span class="sxs-lookup"><span data-stu-id="3fa35-552">Examples</span></span>
<span data-ttu-id="3fa35-553">本節說明如何搭配在 `Policy` 類別中設定的一組重試原則，使用 Polly 存取 Azure SQL Database。</span><span class="sxs-lookup"><span data-stu-id="3fa35-553">This section shows how you can use Polly to access Azure SQL Database using a set of retry policies configured in the `Policy` class.</span></span>

<span data-ttu-id="3fa35-554">下列程式碼顯示 `SqlCommand` 類別上的擴充方法，會使用指數輪詢呼叫 `ExecuteAsync`。</span><span class="sxs-lookup"><span data-stu-id="3fa35-554">The following code shows an extension method on the `SqlCommand` class that calls `ExecuteAsync` with exponential backoff.</span></span>

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

<span data-ttu-id="3fa35-555">這個非同步擴充方法的用法如下。</span><span class="sxs-lookup"><span data-stu-id="3fa35-555">This asynchronous extension method can be used as follows.</span></span>

```csharp
var sqlCommand = sqlConnection.CreateCommand();
sqlCommand.CommandText = "[some query]";

using (var reader = await sqlCommand.ExecuteReaderWithRetryAsync())
{
    // Do something with the values
}
```

### <a name="more-information"></a><span data-ttu-id="3fa35-556">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="3fa35-556">More information</span></span>
* [<span data-ttu-id="3fa35-557">雲端服務基礎資料存取層 – 暫時性錯誤處理。</span><span class="sxs-lookup"><span data-stu-id="3fa35-557">Cloud Service Fundamentals Data Access Layer – Transient Fault Handling</span></span>](https://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx)

<span data-ttu-id="3fa35-558">如需充分使用 SQL Database 的一般指引，請參閱 [Azure SQL 資料庫效能和彈性指南](https://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-558">For general guidance on getting the most from SQL Database, see [Azure SQL Database Performance and Elasticity Guide](https://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx).</span></span>

## <a name="sql-database-using-entity-framework-6"></a><span data-ttu-id="3fa35-559">使用 Entity Framework 6 的 SQL Database</span><span class="sxs-lookup"><span data-stu-id="3fa35-559">SQL Database using Entity Framework 6</span></span>
<span data-ttu-id="3fa35-560">SQL Database 是託管的 SQL 資料庫，有各種大小，並以標準 (共用) 與高階 (非共用) 服務提供。</span><span class="sxs-lookup"><span data-stu-id="3fa35-560">SQL Database is a hosted SQL database available in a range of sizes and as both a standard (shared) and premium (non-shared) service.</span></span> <span data-ttu-id="3fa35-561">Entity Framework 是物件關聯式對應程式，可讓 .NET 開發人員使用網域特有的物件來處理關聯式資料。</span><span class="sxs-lookup"><span data-stu-id="3fa35-561">Entity Framework is an object-relational mapper that enables .NET developers to work with relational data using domain-specific objects.</span></span> <span data-ttu-id="3fa35-562">有了 Entity Framework，開發人員便不再需要撰寫大多數必須撰寫的資料存取程式碼。</span><span class="sxs-lookup"><span data-stu-id="3fa35-562">It eliminates the need for most of the data-access code that developers usually need to write.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="3fa35-563">重試機制</span><span class="sxs-lookup"><span data-stu-id="3fa35-563">Retry mechanism</span></span>
<span data-ttu-id="3fa35-564">透過名為 [連接恢復/重試邏輯](/ef/ef6/fundamentals/connection-resiliency/retry-logic)的機制使用 Entity Framework 6.0 或更新版本存取 SQL Database 時，會提供重試支援。</span><span class="sxs-lookup"><span data-stu-id="3fa35-564">Retry support is provided when accessing SQL Database using Entity Framework 6.0 and higher through a mechanism called [Connection Resiliency / Retry Logic](/ef/ef6/fundamentals/connection-resiliency/retry-logic).</span></span> <span data-ttu-id="3fa35-565">重試機制的主要功能有：</span><span class="sxs-lookup"><span data-stu-id="3fa35-565">The main features of the retry mechanism are:</span></span>

* <span data-ttu-id="3fa35-566">主要抽象層是 **IDbExecutionStrategy** 介面。</span><span class="sxs-lookup"><span data-stu-id="3fa35-566">The primary abstraction is the **IDbExecutionStrategy** interface.</span></span> <span data-ttu-id="3fa35-567">此介面會：</span><span class="sxs-lookup"><span data-stu-id="3fa35-567">This interface:</span></span>
  * <span data-ttu-id="3fa35-568">定義同步和非同步 **Execute**\* 方法。</span><span class="sxs-lookup"><span data-stu-id="3fa35-568">Defines synchronous and asynchronous **Execute**\* methods.</span></span>
  * <span data-ttu-id="3fa35-569">定義類別以直接使用或在資料庫內容上設定以做為預設策略、對應至提供者名稱，或對應至提供者名稱和伺服器名稱。</span><span class="sxs-lookup"><span data-stu-id="3fa35-569">Defines classes that can be used directly or can be configured on a database context as a default strategy, mapped to provider name, or mapped to a provider name and server name.</span></span> <span data-ttu-id="3fa35-570">在內容上設定時，重試是在個別資料庫作業層級發生的，其中可能有數個重試是針對指定的內容作業。</span><span class="sxs-lookup"><span data-stu-id="3fa35-570">When configured on a context, retries occur at the level of individual database operations, of which there might be several for a given context operation.</span></span>
  * <span data-ttu-id="3fa35-571">定義何時要重試失敗的連接，以及如何重試。</span><span class="sxs-lookup"><span data-stu-id="3fa35-571">Defines when to retry a failed connection, and how.</span></span>
* <span data-ttu-id="3fa35-572">它包含 **IDbExecutionStrategy** 介面的數個內建實作：</span><span class="sxs-lookup"><span data-stu-id="3fa35-572">It includes several built-in implementations of the **IDbExecutionStrategy** interface:</span></span>
  * <span data-ttu-id="3fa35-573">預設值 - 無重試。</span><span class="sxs-lookup"><span data-stu-id="3fa35-573">Default - no retrying.</span></span>
  * <span data-ttu-id="3fa35-574">SQL Database 的預設值 (自動) - 沒有重試，但會檢查例外狀況，並以使用 SQL Database 策略的建議來包裝例外狀況。</span><span class="sxs-lookup"><span data-stu-id="3fa35-574">Default for SQL Database (automatic) - no retrying, but inspects exceptions and wraps them with suggestion to use the SQL Database strategy.</span></span>
  * <span data-ttu-id="3fa35-575">SQL Database 的預設值 - 指數 (繼承自基底類別) 再加上 SQL Database 偵測邏輯。</span><span class="sxs-lookup"><span data-stu-id="3fa35-575">Default for SQL Database - exponential (inherited from base class) plus SQL Database detection logic.</span></span>
* <span data-ttu-id="3fa35-576">它會實作包含隨機的指數退避策略。</span><span class="sxs-lookup"><span data-stu-id="3fa35-576">It implements an exponential back-off strategy that includes randomization.</span></span>
* <span data-ttu-id="3fa35-577">內建的重試類別是可設定狀態，且非安全執行緒。</span><span class="sxs-lookup"><span data-stu-id="3fa35-577">The built-in retry classes are stateful and are not thread safe.</span></span> <span data-ttu-id="3fa35-578">不過，在目前的作業完成之後可重複執行這些類別。</span><span class="sxs-lookup"><span data-stu-id="3fa35-578">However, they can be reused after the current operation is completed.</span></span>
* <span data-ttu-id="3fa35-579">如果超出指定的重試計數，則會以新的例外狀況包裝結果。</span><span class="sxs-lookup"><span data-stu-id="3fa35-579">If the specified retry count is exceeded, the results are wrapped in a new exception.</span></span> <span data-ttu-id="3fa35-580">它不會冒出目前的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="3fa35-580">It does not bubble up the current exception.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="3fa35-581">原則組態</span><span class="sxs-lookup"><span data-stu-id="3fa35-581">Policy configuration</span></span>
<span data-ttu-id="3fa35-582">使用 Entity Framework 6.0 或更新版本存取 SQL Database 時，會提供重試支援。</span><span class="sxs-lookup"><span data-stu-id="3fa35-582">Retry support is provided when accessing SQL Database using Entity Framework 6.0 and higher.</span></span> <span data-ttu-id="3fa35-583">以程式設計方式設定重試原則。</span><span class="sxs-lookup"><span data-stu-id="3fa35-583">Retry policies are configured programmatically.</span></span> <span data-ttu-id="3fa35-584">無法根據預先作業變更組態。</span><span class="sxs-lookup"><span data-stu-id="3fa35-584">The configuration cannot be changed on a per-operation basis.</span></span>

<span data-ttu-id="3fa35-585">在內容上將策略設定為預設值時，您要指定一個會視需要建立新策略的功能。</span><span class="sxs-lookup"><span data-stu-id="3fa35-585">When configuring a strategy on the context as the default, you specify a function that creates a new strategy on demand.</span></span> <span data-ttu-id="3fa35-586">下列程式碼示範如何建立重試組態類別來延伸 **DbConfiguration** 基底類別。</span><span class="sxs-lookup"><span data-stu-id="3fa35-586">The following code shows how you can create a retry configuration class that extends the **DbConfiguration** base class.</span></span>

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

<span data-ttu-id="3fa35-587">當應用程式開啟時，您可以使用 **DbConfiguration** 執行個體的 **SetConfiguration** 方法將此指定為所有作業的預設重試策略。</span><span class="sxs-lookup"><span data-stu-id="3fa35-587">You can then specify this as the default retry strategy for all operations using the **SetConfiguration** method of the **DbConfiguration** instance when the application starts.</span></span> <span data-ttu-id="3fa35-588">依預設，EF 將自動探索和使用組態類別。</span><span class="sxs-lookup"><span data-stu-id="3fa35-588">By default, EF will automatically discover and use the configuration class.</span></span>

```csharp
DbConfiguration.SetConfiguration(new BloggingContextConfiguration());
```

<span data-ttu-id="3fa35-589">您可以使用 **DbConfigurationType** 屬性來標註內容類別，以指定內容的重試組態類別。</span><span class="sxs-lookup"><span data-stu-id="3fa35-589">You can specify the retry configuration class for a context by annotating the context class with a **DbConfigurationType** attribute.</span></span> <span data-ttu-id="3fa35-590">不過，如果您只有一個組態類別，EF 會使用它，而不需要標註內容。</span><span class="sxs-lookup"><span data-stu-id="3fa35-590">However, if you have only one configuration class, EF will use it without the need to annotate the context.</span></span>

```csharp
[DbConfigurationType(typeof(BloggingContextConfiguration))]
public class BloggingContext : DbContext
```

<span data-ttu-id="3fa35-591">如果您需要針對特定作業使用不同的重試策略，或針對特定作業停用重試，您可以建立組態類別來讓您透過在 **CallContext**中設定旗標，來暫停或切換策略。</span><span class="sxs-lookup"><span data-stu-id="3fa35-591">If you need to use different retry strategies for specific operations, or disable retries for specific operations, you can create a configuration class that allows you to suspend or swap strategies by setting a flag in the **CallContext**.</span></span> <span data-ttu-id="3fa35-592">組態類別可使用此旗標來切換策略，或停用您提供的策略並使用預設策略。</span><span class="sxs-lookup"><span data-stu-id="3fa35-592">The configuration class can use this flag to switch strategies, or disable the strategy you provide and use a default strategy.</span></span> <span data-ttu-id="3fa35-593">如需詳細資訊，請參閱[暫停執行策略](/ef/ef6/fundamentals/connection-resiliency/retry-logic#workaround-suspend-execution-strategy) (EF6 之後的版本)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-593">For more information, see [Suspend Execution Strategy](/ef/ef6/fundamentals/connection-resiliency/retry-logic#workaround-suspend-execution-strategy) (EF6 onwards).</span></span>

<span data-ttu-id="3fa35-594">針對個別作業使用特定重試策略的另一個方法，就是建立必要策略類別的執行個體，並透過參數提供所需的設定。</span><span class="sxs-lookup"><span data-stu-id="3fa35-594">Another technique for using specific retry strategies for individual operations is to create an instance of the required strategy class and supply the desired settings through parameters.</span></span> <span data-ttu-id="3fa35-595">然後叫用其 **ExecuteAsync** 方法。</span><span class="sxs-lookup"><span data-stu-id="3fa35-595">You then invoke its **ExecuteAsync** method.</span></span>

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

<span data-ttu-id="3fa35-596">使用 **DbConfiguration** 類別最簡單的方式，就是在與 **DbContext** 類別相同的組件中找到該類別。</span><span class="sxs-lookup"><span data-stu-id="3fa35-596">The simplest way to use a **DbConfiguration** class is to locate it in the same assembly as the **DbContext** class.</span></span> <span data-ttu-id="3fa35-597">不過，這不適用在不同案例中需要相同的內容時，例如不同的互動式和背景重試策略。</span><span class="sxs-lookup"><span data-stu-id="3fa35-597">However, this is not appropriate when the same context is required in different scenarios, such as different interactive and background retry strategies.</span></span> <span data-ttu-id="3fa35-598">如果不同的內容在不同的 AppDomain 中執行，您可以使用內建支援在組態檔案中指定組態類別，或使用程式碼明確地設定組態類別。</span><span class="sxs-lookup"><span data-stu-id="3fa35-598">If the different contexts execute in separate AppDomains, you can use the built-in support for specifying configuration classes in the configuration file or set it explicitly using code.</span></span> <span data-ttu-id="3fa35-599">如果不同的內容必須在相同的 AppDomain 中執行，則需要自訂解決方案。</span><span class="sxs-lookup"><span data-stu-id="3fa35-599">If the different contexts must execute in the same AppDomain, a custom solution will be required.</span></span>

<span data-ttu-id="3fa35-600">如需詳細資訊，請參閱[程式碼式組態 (EF6 之後的版本)](/ef/ef6/fundamentals/configuring/code-based)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-600">For more information, see [Code-Based Configuration](/ef/ef6/fundamentals/configuring/code-based) (EF6 onwards).</span></span>

<span data-ttu-id="3fa35-601">下表顯示使用 EF6 時內建重試原則的預設設定。</span><span class="sxs-lookup"><span data-stu-id="3fa35-601">The following table shows the default settings for the built-in retry policy when using EF6.</span></span>

| <span data-ttu-id="3fa35-602">設定</span><span class="sxs-lookup"><span data-stu-id="3fa35-602">Setting</span></span> | <span data-ttu-id="3fa35-603">預設值</span><span class="sxs-lookup"><span data-stu-id="3fa35-603">Default value</span></span> | <span data-ttu-id="3fa35-604">意義</span><span class="sxs-lookup"><span data-stu-id="3fa35-604">Meaning</span></span> |
|---------|---------------|---------|
| <span data-ttu-id="3fa35-605">原則</span><span class="sxs-lookup"><span data-stu-id="3fa35-605">Policy</span></span> | <span data-ttu-id="3fa35-606">指數</span><span class="sxs-lookup"><span data-stu-id="3fa35-606">Exponential</span></span> | <span data-ttu-id="3fa35-607">指數輪詢。</span><span class="sxs-lookup"><span data-stu-id="3fa35-607">Exponential back-off.</span></span> |
| <span data-ttu-id="3fa35-608">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="3fa35-608">MaxRetryCount</span></span> | <span data-ttu-id="3fa35-609">5</span><span class="sxs-lookup"><span data-stu-id="3fa35-609">5</span></span> | <span data-ttu-id="3fa35-610">重試次數上限。</span><span class="sxs-lookup"><span data-stu-id="3fa35-610">The maximum number of retries.</span></span> |
| <span data-ttu-id="3fa35-611">MaxDelay</span><span class="sxs-lookup"><span data-stu-id="3fa35-611">MaxDelay</span></span> | <span data-ttu-id="3fa35-612">30 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-612">30 seconds</span></span> | <span data-ttu-id="3fa35-613">重試之間的延遲上限。</span><span class="sxs-lookup"><span data-stu-id="3fa35-613">The maximum delay between retries.</span></span> <span data-ttu-id="3fa35-614">此值不會影響延遲序列的計算方式。</span><span class="sxs-lookup"><span data-stu-id="3fa35-614">This value does not affect how the series of delays are computed.</span></span> <span data-ttu-id="3fa35-615">它只會定義上限。</span><span class="sxs-lookup"><span data-stu-id="3fa35-615">It only defines an upper bound.</span></span> |
| <span data-ttu-id="3fa35-616">DefaultCoefficient</span><span class="sxs-lookup"><span data-stu-id="3fa35-616">DefaultCoefficient</span></span> | <span data-ttu-id="3fa35-617">1 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-617">1 second</span></span> | <span data-ttu-id="3fa35-618">指數輪詢計算係數。</span><span class="sxs-lookup"><span data-stu-id="3fa35-618">The coefficient for the exponential back-off computation.</span></span> <span data-ttu-id="3fa35-619">無法變更此值。</span><span class="sxs-lookup"><span data-stu-id="3fa35-619">This value cannot be changed.</span></span> |
| <span data-ttu-id="3fa35-620">DefaultRandomFactor</span><span class="sxs-lookup"><span data-stu-id="3fa35-620">DefaultRandomFactor</span></span> | <span data-ttu-id="3fa35-621">1.1</span><span class="sxs-lookup"><span data-stu-id="3fa35-621">1.1</span></span> | <span data-ttu-id="3fa35-622">乘數，用來對每個項目新增隨機延遲。</span><span class="sxs-lookup"><span data-stu-id="3fa35-622">The multiplier used to add a random delay for each entry.</span></span> <span data-ttu-id="3fa35-623">無法變更此值。</span><span class="sxs-lookup"><span data-stu-id="3fa35-623">This value cannot be changed.</span></span> |
| <span data-ttu-id="3fa35-624">DefaultExponentialBase</span><span class="sxs-lookup"><span data-stu-id="3fa35-624">DefaultExponentialBase</span></span> | <span data-ttu-id="3fa35-625">2</span><span class="sxs-lookup"><span data-stu-id="3fa35-625">2</span></span> | <span data-ttu-id="3fa35-626">乘數，用來計算下一個延遲。</span><span class="sxs-lookup"><span data-stu-id="3fa35-626">The multiplier used to calculate the next delay.</span></span> <span data-ttu-id="3fa35-627">無法變更此值。</span><span class="sxs-lookup"><span data-stu-id="3fa35-627">This value cannot be changed.</span></span> |

### <a name="retry-usage-guidance"></a><span data-ttu-id="3fa35-628">重試使用指引</span><span class="sxs-lookup"><span data-stu-id="3fa35-628">Retry usage guidance</span></span>
<span data-ttu-id="3fa35-629">使用 EF6 存取 SQL Database 時，請考慮下列指引：</span><span class="sxs-lookup"><span data-stu-id="3fa35-629">Consider the following guidelines when accessing SQL Database using EF6:</span></span>

* <span data-ttu-id="3fa35-630">選擇適當的服務選項 (共用或高階)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-630">Choose the appropriate service option (shared or premium).</span></span> <span data-ttu-id="3fa35-631">共用的執行個體可能會有延遲時間比一般連接長，及因為共用伺服器有其他租用戶使用而節流的問題。</span><span class="sxs-lookup"><span data-stu-id="3fa35-631">A shared instance may suffer longer than usual connection delays and throttling due to the usage by other tenants of the shared server.</span></span> <span data-ttu-id="3fa35-632">如果需要可預測的效能和可靠的低延遲作業，請考慮選擇高階選項。</span><span class="sxs-lookup"><span data-stu-id="3fa35-632">If predictable performance and reliable low latency operations are required, consider choosing the premium option.</span></span>
* <span data-ttu-id="3fa35-633">不建議將固定間隔策略搭配 Azure SQL Database 使用。</span><span class="sxs-lookup"><span data-stu-id="3fa35-633">A fixed interval strategy is not recommended for use with Azure SQL Database.</span></span> <span data-ttu-id="3fa35-634">相反地，應使用指數退避策略，因為服務可能會過載，且延遲時間越長，復原所需的時間也越長。</span><span class="sxs-lookup"><span data-stu-id="3fa35-634">Instead, use an exponential back-off strategy because the service may be overloaded, and longer delays allow more time for it to recover.</span></span>
* <span data-ttu-id="3fa35-635">定義連接時，為連接與命令逾時選擇合適的值。</span><span class="sxs-lookup"><span data-stu-id="3fa35-635">Choose a suitable value for the connection and command timeouts when defining connections.</span></span> <span data-ttu-id="3fa35-636">讓逾時以您的商務邏輯設計與直通測試為基礎。</span><span class="sxs-lookup"><span data-stu-id="3fa35-636">Base the timeout on both your business logic design and through testing.</span></span> <span data-ttu-id="3fa35-637">您可能需要隨時間修改此值，因為資料量或商務程序會改變。</span><span class="sxs-lookup"><span data-stu-id="3fa35-637">You may need to modify this value over time as the volumes of data or the business processes change.</span></span> <span data-ttu-id="3fa35-638">逾時值過短，會造成資料庫忙碌時連接永遠失敗。</span><span class="sxs-lookup"><span data-stu-id="3fa35-638">Too short a timeout may result in premature failures of connections when the database is busy.</span></span> <span data-ttu-id="3fa35-639">逾時值過長，可能會因為在偵測失敗連接之前等待過久，而造成重試邏輯無法正確運作。</span><span class="sxs-lookup"><span data-stu-id="3fa35-639">Too long a timeout may prevent the retry logic working correctly by waiting too long before detecting a failed connection.</span></span> <span data-ttu-id="3fa35-640">逾時值是端對端延遲的元件，雖然您無法輕易判斷儲存內容時會執行幾個命令。</span><span class="sxs-lookup"><span data-stu-id="3fa35-640">The value of the timeout is a component of the end-to-end latency, although you cannot easily determine how many commands will execute when saving the context.</span></span> <span data-ttu-id="3fa35-641">您可以透過設定 **DbContext** 執行個體的 **CommandTimeout** 屬性，來變更預設逾時。</span><span class="sxs-lookup"><span data-stu-id="3fa35-641">You can change the default timeout by setting the **CommandTimeout** property of the **DbContext** instance.</span></span>
* <span data-ttu-id="3fa35-642">Entity Framework 支援在組態檔中定義的重試組態。</span><span class="sxs-lookup"><span data-stu-id="3fa35-642">Entity Framework supports retry configurations defined in configuration files.</span></span> <span data-ttu-id="3fa35-643">不過，為了讓 Azure 有最大的彈性，您應該考慮在應用程式內以程式設計方式建立組態。</span><span class="sxs-lookup"><span data-stu-id="3fa35-643">However, for maximum flexibility on Azure you should consider creating the configuration programmatically within the application.</span></span> <span data-ttu-id="3fa35-644">重試原則的特定參數 (例如重試次數與重試間隔) 可以儲存在服務組態檔中，並在執行階段用於建立適當的原則。</span><span class="sxs-lookup"><span data-stu-id="3fa35-644">The specific parameters for the retry policies, such as the number of retries and the retry intervals, can be stored in the service configuration file and used at runtime to create the appropriate policies.</span></span> <span data-ttu-id="3fa35-645">這會讓設定變更，而1不需要應用程式重新啟動。</span><span class="sxs-lookup"><span data-stu-id="3fa35-645">This allows the settings to be changed without requiring the application to be restarted.</span></span>

<span data-ttu-id="3fa35-646">請考慮讓重試作業從下列設定開始。</span><span class="sxs-lookup"><span data-stu-id="3fa35-646">Consider starting with the following settings for retrying operations.</span></span> <span data-ttu-id="3fa35-647">您無法指定重試嘗試之間的延遲 (它是固定的，並產生成為指數序列)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-647">You cannot specify the delay between retry attempts (it is fixed and generated as an exponential sequence).</span></span> <span data-ttu-id="3fa35-648">您可以只指定最大值，如此處所示，除非您建立自訂重試策略。</span><span class="sxs-lookup"><span data-stu-id="3fa35-648">You can specify only the maximum values, as shown here; unless you create a custom retry strategy.</span></span> <span data-ttu-id="3fa35-649">這些是一般用途設定，您應該監視作業並微調其值以符合您自己的需求。</span><span class="sxs-lookup"><span data-stu-id="3fa35-649">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="3fa35-650">**內容**</span><span class="sxs-lookup"><span data-stu-id="3fa35-650">**Context**</span></span> | <span data-ttu-id="3fa35-651">**範例目標 E2E<br />最大延遲**</span><span class="sxs-lookup"><span data-stu-id="3fa35-651">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="3fa35-652">**重試原則**</span><span class="sxs-lookup"><span data-stu-id="3fa35-652">**Retry policy**</span></span> | <span data-ttu-id="3fa35-653">**設定**</span><span class="sxs-lookup"><span data-stu-id="3fa35-653">**Settings**</span></span> | <span data-ttu-id="3fa35-654">**值**</span><span class="sxs-lookup"><span data-stu-id="3fa35-654">**Values**</span></span> | <span data-ttu-id="3fa35-655">**運作方式**</span><span class="sxs-lookup"><span data-stu-id="3fa35-655">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="3fa35-656">互動式、UI </span><span class="sxs-lookup"><span data-stu-id="3fa35-656">Interactive, UI,</span></span><br /><span data-ttu-id="3fa35-657">或前景</span><span class="sxs-lookup"><span data-stu-id="3fa35-657">or foreground</span></span> |<span data-ttu-id="3fa35-658">2 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-658">2 seconds</span></span> |<span data-ttu-id="3fa35-659">指數</span><span class="sxs-lookup"><span data-stu-id="3fa35-659">Exponential</span></span> |<span data-ttu-id="3fa35-660">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="3fa35-660">MaxRetryCount</span></span><br /><span data-ttu-id="3fa35-661">MaxDelay</span><span class="sxs-lookup"><span data-stu-id="3fa35-661">MaxDelay</span></span> |<span data-ttu-id="3fa35-662">3</span><span class="sxs-lookup"><span data-stu-id="3fa35-662">3</span></span><br /><span data-ttu-id="3fa35-663">750 毫秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-663">750 ms</span></span> |<span data-ttu-id="3fa35-664">嘗試 1 - 延遲 0 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-664">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="3fa35-665">嘗試 2 - 延遲 750 毫秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-665">Attempt 2 - delay 750 ms</span></span><br /><span data-ttu-id="3fa35-666">嘗試 3 - 延遲 750 毫秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-666">Attempt 3 – delay 750 ms</span></span> |
| <span data-ttu-id="3fa35-667">背景</span><span class="sxs-lookup"><span data-stu-id="3fa35-667">Background</span></span><br /> <span data-ttu-id="3fa35-668">或批次</span><span class="sxs-lookup"><span data-stu-id="3fa35-668">or batch</span></span> |<span data-ttu-id="3fa35-669">30 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-669">30 seconds</span></span> |<span data-ttu-id="3fa35-670">指數</span><span class="sxs-lookup"><span data-stu-id="3fa35-670">Exponential</span></span> |<span data-ttu-id="3fa35-671">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="3fa35-671">MaxRetryCount</span></span><br /><span data-ttu-id="3fa35-672">MaxDelay</span><span class="sxs-lookup"><span data-stu-id="3fa35-672">MaxDelay</span></span> |<span data-ttu-id="3fa35-673">5</span><span class="sxs-lookup"><span data-stu-id="3fa35-673">5</span></span><br /><span data-ttu-id="3fa35-674">12 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-674">12 seconds</span></span> |<span data-ttu-id="3fa35-675">嘗試 1 - 延遲 0 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-675">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="3fa35-676">嘗試 2 - 延遲 ~1 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-676">Attempt 2 - delay ~1 sec</span></span><br /><span data-ttu-id="3fa35-677">嘗試 3 - 延遲 ~3 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-677">Attempt 3 - delay ~3 sec</span></span><br /><span data-ttu-id="3fa35-678">嘗試 4 - 延遲 ~7 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-678">Attempt 4 - delay ~7 sec</span></span><br /><span data-ttu-id="3fa35-679">嘗試 5 - 延遲 12 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-679">Attempt 5 - delay 12 sec</span></span> |

> [!NOTE]
> <span data-ttu-id="3fa35-680">端對端延遲目標會假設服務連接的預設逾時值。</span><span class="sxs-lookup"><span data-stu-id="3fa35-680">The end-to-end latency targets assume the default timeout for connections to the service.</span></span> <span data-ttu-id="3fa35-681">如果您指定較長的連接逾時值，則系統會為每個重試嘗試增加這段時間，來延長端對端延遲。</span><span class="sxs-lookup"><span data-stu-id="3fa35-681">If you specify longer connection timeouts, the end-to-end latency will be extended by this additional time for every retry attempt.</span></span>
>
>

### <a name="examples"></a><span data-ttu-id="3fa35-682">範例</span><span class="sxs-lookup"><span data-stu-id="3fa35-682">Examples</span></span>
<span data-ttu-id="3fa35-683">下列程式碼範例會定義使用 Entity Framework 的簡單資料存取解決方案。</span><span class="sxs-lookup"><span data-stu-id="3fa35-683">The following code example defines a simple data access solution that uses Entity Framework.</span></span> <span data-ttu-id="3fa35-684">它會設定特定的重試策略，做法是為會延伸 **DbConfiguration** 的 **BlogConfiguration** 類別定義執行個體。</span><span class="sxs-lookup"><span data-stu-id="3fa35-684">It sets a specific retry strategy by defining an instance of a class named **BlogConfiguration** that extends **DbConfiguration**.</span></span>

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

<span data-ttu-id="3fa35-685">使用 Entity Framework 重試機制的更多範例位於 [連接恢復/重試邏輯](/ef/ef6/fundamentals/connection-resiliency/retry-logic)中。</span><span class="sxs-lookup"><span data-stu-id="3fa35-685">More examples of using the Entity Framework retry mechanism can be found in [Connection Resiliency / Retry Logic](/ef/ef6/fundamentals/connection-resiliency/retry-logic).</span></span>

### <a name="more-information"></a><span data-ttu-id="3fa35-686">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="3fa35-686">More information</span></span>
* [<span data-ttu-id="3fa35-687">Azure SQL Database 效能和彈性指南 (英文)</span><span class="sxs-lookup"><span data-stu-id="3fa35-687">Azure SQL Database Performance and Elasticity Guide</span></span>](https://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)

## <a name="sql-database-using-entity-framework-core"></a><span data-ttu-id="3fa35-688">使用 Entity Framework Core 的 SQL Database</span><span class="sxs-lookup"><span data-stu-id="3fa35-688">SQL Database using Entity Framework Core</span></span>
<span data-ttu-id="3fa35-689">[Entity Framework Core](/ef/core/) 是物件關聯式對應程式，可讓 .NET Core 開發人員使用網域特有的物件來處理資料。</span><span class="sxs-lookup"><span data-stu-id="3fa35-689">[Entity Framework Core](/ef/core/) is an object-relational mapper that enables .NET Core developers to work with data using domain-specific objects.</span></span> <span data-ttu-id="3fa35-690">有了 Entity Framework，開發人員便不再需要撰寫大多數必須撰寫的資料存取程式碼。</span><span class="sxs-lookup"><span data-stu-id="3fa35-690">It eliminates the need for most of the data-access code that developers usually need to write.</span></span> <span data-ttu-id="3fa35-691">這個版本的 Entity Framework 為從頭撰寫，不會自動繼承 EF6.x 的所有功能。</span><span class="sxs-lookup"><span data-stu-id="3fa35-691">This version of Entity Framework was written from the ground up, and doesn't automatically inherit all the features from EF6.x.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="3fa35-692">重試機制</span><span class="sxs-lookup"><span data-stu-id="3fa35-692">Retry mechanism</span></span>
<span data-ttu-id="3fa35-693">透過名為[恢復連線](/ef/core/miscellaneous/connection-resiliency)的機制使用 Entity Framework Core 版本存取 SQL Database 時，會提供重試支援。</span><span class="sxs-lookup"><span data-stu-id="3fa35-693">Retry support is provided when accessing SQL Database using Entity Framework Core through a mechanism called [Connection Resiliency](/ef/core/miscellaneous/connection-resiliency).</span></span> <span data-ttu-id="3fa35-694">恢復連線功能是在 EF Core 1.1.0 引進。</span><span class="sxs-lookup"><span data-stu-id="3fa35-694">Connection resiliency was introduced in EF Core 1.1.0.</span></span>

<span data-ttu-id="3fa35-695">主要抽象層是 `IExecutionStrategy` 介面。</span><span class="sxs-lookup"><span data-stu-id="3fa35-695">The primary abstraction is the `IExecutionStrategy` interface.</span></span> <span data-ttu-id="3fa35-696">SQL Server 包括 SQL Azure 的執行策略，知道可以重試的例外狀況類型，有合理的重試次數上限、重試之間的延遲等預設值。</span><span class="sxs-lookup"><span data-stu-id="3fa35-696">The execution strategy for SQL Server, including SQL Azure, is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, and so on.</span></span>

### <a name="examples"></a><span data-ttu-id="3fa35-697">範例</span><span class="sxs-lookup"><span data-stu-id="3fa35-697">Examples</span></span>

<span data-ttu-id="3fa35-698">設定 DbContext 物件時 (該物件表示資料庫的工作階段)，下列程式碼會啟用自動重試。</span><span class="sxs-lookup"><span data-stu-id="3fa35-698">The following code enables automatic retries when configuring the DbContext object, which represents a session with the database.</span></span> 

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(
            @"Server=(localdb)\mssqllocaldb;Database=EFMiscellanous.ConnectionResiliency;Trusted_Connection=True;",
            options => options.EnableRetryOnFailure());
}
```

<span data-ttu-id="3fa35-699">下列程式碼示範如何使用執行策略來執行有自動重試的交易。</span><span class="sxs-lookup"><span data-stu-id="3fa35-699">The following code shows how to execute a transaction with automatic retries, by using an execution strategy.</span></span> <span data-ttu-id="3fa35-700">交易在委派中定義。</span><span class="sxs-lookup"><span data-stu-id="3fa35-700">The transaction is defined in a delegate.</span></span> <span data-ttu-id="3fa35-701">如果發生暫時性失敗，執行策略會再次叫用委派。</span><span class="sxs-lookup"><span data-stu-id="3fa35-701">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

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

## <a name="azure-storage"></a><span data-ttu-id="3fa35-702">Azure 儲存體</span><span class="sxs-lookup"><span data-stu-id="3fa35-702">Azure Storage</span></span>
<span data-ttu-id="3fa35-703">Azure 儲存體服務包括資料表、Blob 儲存體、檔案，以及儲存體佇列。</span><span class="sxs-lookup"><span data-stu-id="3fa35-703">Azure storage services include table and blob storage, files, and storage queues.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="3fa35-704">重試機制</span><span class="sxs-lookup"><span data-stu-id="3fa35-704">Retry mechanism</span></span>
<span data-ttu-id="3fa35-705">重試發生在個別的 REST 作業層級，是用戶端 API 實作不可或缺的一部分。</span><span class="sxs-lookup"><span data-stu-id="3fa35-705">Retries occur at the individual REST operation level and are an integral part of the client API implementation.</span></span> <span data-ttu-id="3fa35-706">用戶端儲存體 SDK 使用實作 [IExtendedRetryPolicy 介面](/dotnet/api/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy)的類別。</span><span class="sxs-lookup"><span data-stu-id="3fa35-706">The client storage SDK uses classes that implement the [IExtendedRetryPolicy Interface](/dotnet/api/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy).</span></span>

<span data-ttu-id="3fa35-707">有不同的實作的介面。</span><span class="sxs-lookup"><span data-stu-id="3fa35-707">There are different implementations of the interface.</span></span> <span data-ttu-id="3fa35-708">儲存體用戶端會選擇特別針對存取資料表、Blob 與佇列設計的原則。</span><span class="sxs-lookup"><span data-stu-id="3fa35-708">Storage clients can choose from policies specifically designed for accessing tables, blobs, and queues.</span></span> <span data-ttu-id="3fa35-709">每個實作會使用不同的重試策略，這些策略基本上定義重試間隔與其他詳細資料。</span><span class="sxs-lookup"><span data-stu-id="3fa35-709">Each implementation uses a different retry strategy that essentially defines the retry interval and other details.</span></span>

<span data-ttu-id="3fa35-710">內建類別支援線性 (固定延遲) 重試間隔和隨機指數重試間隔。</span><span class="sxs-lookup"><span data-stu-id="3fa35-710">The built-in classes provide support for linear (constant delay) and exponential with randomization retry intervals.</span></span> <span data-ttu-id="3fa35-711">當另一個處理序在更高的層級處理重試時，沒有重試原則可使用。</span><span class="sxs-lookup"><span data-stu-id="3fa35-711">There is also a no retry policy for use when another process is handling retries at a higher level.</span></span> <span data-ttu-id="3fa35-712">但如果內建類別未提供您的特定需求，您可以實作您自己的重試類別。</span><span class="sxs-lookup"><span data-stu-id="3fa35-712">However, you can implement your own retry classes if you have specific requirements not provided by the built-in classes.</span></span>

<span data-ttu-id="3fa35-713">如果您正在使用讀取權限異地備援儲存體服務位置 (RA-GRS)，替代重試會在主要與次要儲存體服務位置之間切換，要求的結果是可重試的錯誤。</span><span class="sxs-lookup"><span data-stu-id="3fa35-713">Alternate retries switch between primary and secondary storage service location if you are using read access geo-redundant storage (RA-GRS) and the result of the request is a retryable error.</span></span> <span data-ttu-id="3fa35-714">如需詳細資訊，請參閱 [Azure 儲存體備援選項](/azure/storage/common/storage-redundancy) 。</span><span class="sxs-lookup"><span data-stu-id="3fa35-714">See [Azure Storage Redundancy Options](/azure/storage/common/storage-redundancy) for more information.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="3fa35-715">原則組態</span><span class="sxs-lookup"><span data-stu-id="3fa35-715">Policy configuration</span></span>
<span data-ttu-id="3fa35-716">以程式設計方式設定重試原則。</span><span class="sxs-lookup"><span data-stu-id="3fa35-716">Retry policies are configured programmatically.</span></span> <span data-ttu-id="3fa35-717">一般程序是建立和填入 **TableRequestOptions**、**BlobRequestOptions**、**FileRequestOptions** 或 **QueueRequestOptions** 執行個體。</span><span class="sxs-lookup"><span data-stu-id="3fa35-717">A typical procedure is to create and populate a **TableRequestOptions**, **BlobRequestOptions**, **FileRequestOptions**, or **QueueRequestOptions** instance.</span></span>

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

<span data-ttu-id="3fa35-718">接著可以在用戶端上設定要求選項執行個體，涉及用戶端的所有作業都將使用指定的要求選項。</span><span class="sxs-lookup"><span data-stu-id="3fa35-718">The request options instance can then be set on the client, and all operations with the client will use the specified request options.</span></span>

```csharp
client.DefaultRequestOptions = interactiveRequestOption;
var stats = await client.GetServiceStatsAsync();
```

<span data-ttu-id="3fa35-719">您可以將填入的要求選項類別執行個體做為參數傳遞至作業方法，以覆寫用戶端要求選項。</span><span class="sxs-lookup"><span data-stu-id="3fa35-719">You can override the client request options by passing a populated instance of the request options class as a parameter to operation methods.</span></span>

```csharp
var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
```

<span data-ttu-id="3fa35-720">使用 **OperationContext** 執行個體指定當發生重試時與作業完成時，要執行的程式碼。</span><span class="sxs-lookup"><span data-stu-id="3fa35-720">You use an **OperationContext** instance to specify the code to execute when a retry occurs and when an operation has completed.</span></span> <span data-ttu-id="3fa35-721">此程式碼可以收集用於記錄與遙測中的作業相關資訊。</span><span class="sxs-lookup"><span data-stu-id="3fa35-721">This code can collect information about the operation for use in logs and telemetry.</span></span>

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

<span data-ttu-id="3fa35-722">除了指出錯誤是否適合重試外，擴充的重試原則會傳回 **RetryContext** 以表示重試次數、最後一個要求的結果，以及下一個重試會在主要或次要位置發生 (請參閱下表以取得詳細資料)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-722">In addition to indicating whether a failure is suitable for retry, the extended retry policies return a **RetryContext** object that indicates the number of retries, the results of the last request, whether the next retry will happen in the primary or secondary location (see table below for details).</span></span> <span data-ttu-id="3fa35-723">**RetryContext** 物年的屬性可用於判定是否與何時嘗試重試。</span><span class="sxs-lookup"><span data-stu-id="3fa35-723">The properties of the **RetryContext** object can be used to decide if and when to attempt a retry.</span></span> <span data-ttu-id="3fa35-724">如需詳細資料，請參閱 [IExtendedRetryPolicy.Evaluate Method](/dotnet/api/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-724">For more details, see [IExtendedRetryPolicy.Evaluate Method](/dotnet/api/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate).</span></span>

<span data-ttu-id="3fa35-725">下表顯示內建重試原則的預設設定。</span><span class="sxs-lookup"><span data-stu-id="3fa35-725">The following tables show the default settings for the built-in retry policies.</span></span>

<span data-ttu-id="3fa35-726">**要求選項**</span><span class="sxs-lookup"><span data-stu-id="3fa35-726">**Request options**</span></span>

| <span data-ttu-id="3fa35-727">**設定**</span><span class="sxs-lookup"><span data-stu-id="3fa35-727">**Setting**</span></span> | <span data-ttu-id="3fa35-728">**預設值**</span><span class="sxs-lookup"><span data-stu-id="3fa35-728">**Default value**</span></span> | <span data-ttu-id="3fa35-729">**意義**</span><span class="sxs-lookup"><span data-stu-id="3fa35-729">**Meaning**</span></span> |
| --- | --- | --- |
| <span data-ttu-id="3fa35-730">MaximumExecutionTime</span><span class="sxs-lookup"><span data-stu-id="3fa35-730">MaximumExecutionTime</span></span> | <span data-ttu-id="3fa35-731">None</span><span class="sxs-lookup"><span data-stu-id="3fa35-731">None</span></span> | <span data-ttu-id="3fa35-732">要求的最大執行時間，包含所有可能的重試嘗試。</span><span class="sxs-lookup"><span data-stu-id="3fa35-732">Maximum execution time for the request, including all potential retry attempts.</span></span> <span data-ttu-id="3fa35-733">如果未指定，則允許的要求採用時間量無限制。</span><span class="sxs-lookup"><span data-stu-id="3fa35-733">If it is not specified, then the amount of time that a request is permitted to take is unlimited.</span></span> <span data-ttu-id="3fa35-734">換句話說，要求可能會停止回應。</span><span class="sxs-lookup"><span data-stu-id="3fa35-734">In other words, the request might hang.</span></span> |
| <span data-ttu-id="3fa35-735">ServerTimeout</span><span class="sxs-lookup"><span data-stu-id="3fa35-735">ServerTimeout</span></span> | <span data-ttu-id="3fa35-736">None</span><span class="sxs-lookup"><span data-stu-id="3fa35-736">None</span></span> | <span data-ttu-id="3fa35-737">要求的伺服器逾時間隔 (值四捨五入至秒)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-737">Server timeout interval for the request (value is rounded to seconds).</span></span> <span data-ttu-id="3fa35-738">如果未指定，則會對伺服器的所有要求使用預設值。</span><span class="sxs-lookup"><span data-stu-id="3fa35-738">If not specified, it will use the default value for all requests to the server.</span></span> <span data-ttu-id="3fa35-739">通常，最好的選擇是略過此設定，以便使用伺服器預設值。</span><span class="sxs-lookup"><span data-stu-id="3fa35-739">Usually, the best option is to omit this setting so that the server default is used.</span></span> | 
| <span data-ttu-id="3fa35-740">LocationMode</span><span class="sxs-lookup"><span data-stu-id="3fa35-740">LocationMode</span></span> | <span data-ttu-id="3fa35-741">None</span><span class="sxs-lookup"><span data-stu-id="3fa35-741">None</span></span> | <span data-ttu-id="3fa35-742">如果使用讀取權限異地備援儲存體 (RA-GRS) 複寫選項建立儲存體帳戶，您可以使用位置模式來指出哪個位置應接收要求。</span><span class="sxs-lookup"><span data-stu-id="3fa35-742">If the storage account is created with the Read access geo-redundant storage (RA-GRS) replication option, you can use the location mode to indicate which location should receive the request.</span></span> <span data-ttu-id="3fa35-743">例如，如果指定 **PrimaryThenSecondary**，則一律先將要求傳送至主要位置。</span><span class="sxs-lookup"><span data-stu-id="3fa35-743">For example, if **PrimaryThenSecondary** is specified, requests are always sent to the primary location first.</span></span> <span data-ttu-id="3fa35-744">如果要求失敗，就會傳送到次要位置。</span><span class="sxs-lookup"><span data-stu-id="3fa35-744">If a request fails, it is sent to the secondary location.</span></span> |
| <span data-ttu-id="3fa35-745">RetryPolicy</span><span class="sxs-lookup"><span data-stu-id="3fa35-745">RetryPolicy</span></span> | <span data-ttu-id="3fa35-746">ExponentialPolicy</span><span class="sxs-lookup"><span data-stu-id="3fa35-746">ExponentialPolicy</span></span> | <span data-ttu-id="3fa35-747">如需每個選項的詳細資訊，請參閱下列內容。</span><span class="sxs-lookup"><span data-stu-id="3fa35-747">See below for details of each option.</span></span> |

<span data-ttu-id="3fa35-748">**指數原則**</span><span class="sxs-lookup"><span data-stu-id="3fa35-748">**Exponential policy**</span></span> 

| <span data-ttu-id="3fa35-749">**設定**</span><span class="sxs-lookup"><span data-stu-id="3fa35-749">**Setting**</span></span> | <span data-ttu-id="3fa35-750">**預設值**</span><span class="sxs-lookup"><span data-stu-id="3fa35-750">**Default value**</span></span> | <span data-ttu-id="3fa35-751">**意義**</span><span class="sxs-lookup"><span data-stu-id="3fa35-751">**Meaning**</span></span> |
| --- | --- | --- |
| <span data-ttu-id="3fa35-752">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="3fa35-752">maxAttempt</span></span> | <span data-ttu-id="3fa35-753">3</span><span class="sxs-lookup"><span data-stu-id="3fa35-753">3</span></span> | <span data-ttu-id="3fa35-754">重試嘗試的次數。</span><span class="sxs-lookup"><span data-stu-id="3fa35-754">Number of retry attempts.</span></span> |
| <span data-ttu-id="3fa35-755">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="3fa35-755">deltaBackoff</span></span> | <span data-ttu-id="3fa35-756">4 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-756">4 seconds</span></span> | <span data-ttu-id="3fa35-757">重試之間的輪詢間隔。</span><span class="sxs-lookup"><span data-stu-id="3fa35-757">Back-off interval between retries.</span></span> <span data-ttu-id="3fa35-758">此時間範圍的倍數 (包含隨機元素)，將用於後續的重試次數。</span><span class="sxs-lookup"><span data-stu-id="3fa35-758">Multiples of this timespan, including a random element, will be used for subsequent retry attempts.</span></span> |
| <span data-ttu-id="3fa35-759">MinBackoff</span><span class="sxs-lookup"><span data-stu-id="3fa35-759">MinBackoff</span></span> | <span data-ttu-id="3fa35-760">3 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-760">3 seconds</span></span> | <span data-ttu-id="3fa35-761">新增從 deltaBackoff 計算的所有重試間隔。</span><span class="sxs-lookup"><span data-stu-id="3fa35-761">Added to all retry intervals computed from deltaBackoff.</span></span> <span data-ttu-id="3fa35-762">無法變更此值。</span><span class="sxs-lookup"><span data-stu-id="3fa35-762">This value cannot be changed.</span></span>
| <span data-ttu-id="3fa35-763">MaxBackoff</span><span class="sxs-lookup"><span data-stu-id="3fa35-763">MaxBackoff</span></span> | <span data-ttu-id="3fa35-764">120 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-764">120 seconds</span></span> | <span data-ttu-id="3fa35-765">如果計算的重試間隔大於 MaxBackoff，則會使用 MaxBackoff。</span><span class="sxs-lookup"><span data-stu-id="3fa35-765">MaxBackoff is used if the computed retry interval is greater than MaxBackoff.</span></span> <span data-ttu-id="3fa35-766">無法變更此值。</span><span class="sxs-lookup"><span data-stu-id="3fa35-766">This value cannot be changed.</span></span> |

<span data-ttu-id="3fa35-767">**線性原則**</span><span class="sxs-lookup"><span data-stu-id="3fa35-767">**Linear policy**</span></span>

| <span data-ttu-id="3fa35-768">**設定**</span><span class="sxs-lookup"><span data-stu-id="3fa35-768">**Setting**</span></span> | <span data-ttu-id="3fa35-769">**預設值**</span><span class="sxs-lookup"><span data-stu-id="3fa35-769">**Default value**</span></span> | <span data-ttu-id="3fa35-770">**意義**</span><span class="sxs-lookup"><span data-stu-id="3fa35-770">**Meaning**</span></span> |
| --- | --- | --- |
| <span data-ttu-id="3fa35-771">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="3fa35-771">maxAttempt</span></span> | <span data-ttu-id="3fa35-772">3</span><span class="sxs-lookup"><span data-stu-id="3fa35-772">3</span></span> | <span data-ttu-id="3fa35-773">重試嘗試的次數。</span><span class="sxs-lookup"><span data-stu-id="3fa35-773">Number of retry attempts.</span></span> |
| <span data-ttu-id="3fa35-774">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="3fa35-774">deltaBackoff</span></span> | <span data-ttu-id="3fa35-775">30 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-775">30 seconds</span></span> | <span data-ttu-id="3fa35-776">重試之間的輪詢間隔。</span><span class="sxs-lookup"><span data-stu-id="3fa35-776">Back-off interval between retries.</span></span> |

### <a name="retry-usage-guidance"></a><span data-ttu-id="3fa35-777">重試使用指引</span><span class="sxs-lookup"><span data-stu-id="3fa35-777">Retry usage guidance</span></span>
<span data-ttu-id="3fa35-778">當使用儲存體用戶端 API 存取 Azure 儲存體服務時，請考量下列指引：</span><span class="sxs-lookup"><span data-stu-id="3fa35-778">Consider the following guidelines when accessing Azure storage services using the storage client API:</span></span>

* <span data-ttu-id="3fa35-779">使用 Microsoft.WindowsAzure.Storage.RetryPolicies 命名空間的內建重試原則，此命名空間中的原則適合您的需求。</span><span class="sxs-lookup"><span data-stu-id="3fa35-779">Use the built-in retry policies from the Microsoft.WindowsAzure.Storage.RetryPolicies namespace where they are appropriate for your requirements.</span></span> <span data-ttu-id="3fa35-780">在大多數的狀況中，這些原則已經足夠。</span><span class="sxs-lookup"><span data-stu-id="3fa35-780">In most cases, these policies will be sufficient.</span></span>
* <span data-ttu-id="3fa35-781">在批次作業、 背景工作或非互動式案例中使用 **ExponentialRetry** 原則。</span><span class="sxs-lookup"><span data-stu-id="3fa35-781">Use the **ExponentialRetry** policy in batch operations, background tasks, or non-interactive scenarios.</span></span> <span data-ttu-id="3fa35-782">在這些案例中，您通常會允許服務有更多的時間復原，藉此增加作業最後成功的機會。</span><span class="sxs-lookup"><span data-stu-id="3fa35-782">In these scenarios, you can typically allow more time for the service to recover—with a consequently increased chance of the operation eventually succeeding.</span></span>
* <span data-ttu-id="3fa35-783">請考慮指定 **RequestOptions** 參數的 **MaximumExecutionTime** 屬性，以限制總執行時間，但在選擇逾時值時要考量到作業的類型與大小。</span><span class="sxs-lookup"><span data-stu-id="3fa35-783">Consider specifying the **MaximumExecutionTime** property of the **RequestOptions** parameter to limit the total execution time, but take into account the type and size of the operation when choosing a timeout value.</span></span>
* <span data-ttu-id="3fa35-784">如果您需要實作自訂重試，請避免建立儲存體用戶端類別周圍的包裝函式。</span><span class="sxs-lookup"><span data-stu-id="3fa35-784">If you need to implement a custom retry, avoid creating wrappers around the storage client classes.</span></span> <span data-ttu-id="3fa35-785">相反地，使用一些功能透過 **IExtendedRetryPolicy** 介面來擴充現有的原則。</span><span class="sxs-lookup"><span data-stu-id="3fa35-785">Instead, use the capabilities to extend the existing policies through the **IExtendedRetryPolicy** interface.</span></span>
* <span data-ttu-id="3fa35-786">如果您使用的是讀取權限異地備援儲存體 (RA-GRS)，您可以使用 **LocationMode** 指定如果主要存取失敗，重試嘗試將會存取存放區的次要唯讀複本。</span><span class="sxs-lookup"><span data-stu-id="3fa35-786">If you are using read access geo-redundant storage (RA-GRS) you can use the **LocationMode** to specify that retry attempts will access the secondary read-only copy of the store should the primary access fail.</span></span> <span data-ttu-id="3fa35-787">不過使用這個選項時，若尚未完成從主要存放區複寫，則必須確定應用程式可以成功使用過時的資料。</span><span class="sxs-lookup"><span data-stu-id="3fa35-787">However, when using this option you must ensure that your application can work successfully with data that may be stale if the replication from the primary store has not yet completed.</span></span>

<span data-ttu-id="3fa35-788">請考慮從重試作業的下列設定開始。</span><span class="sxs-lookup"><span data-stu-id="3fa35-788">Consider starting with following settings for retrying operations.</span></span> <span data-ttu-id="3fa35-789">這些是一般用途設定，您應該監視作業並微調其值以符合您自己的需求。</span><span class="sxs-lookup"><span data-stu-id="3fa35-789">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>  

| <span data-ttu-id="3fa35-790">**內容**</span><span class="sxs-lookup"><span data-stu-id="3fa35-790">**Context**</span></span> | <span data-ttu-id="3fa35-791">**範例目標 E2E<br />最大延遲**</span><span class="sxs-lookup"><span data-stu-id="3fa35-791">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="3fa35-792">**重試原則**</span><span class="sxs-lookup"><span data-stu-id="3fa35-792">**Retry policy**</span></span> | <span data-ttu-id="3fa35-793">**設定**</span><span class="sxs-lookup"><span data-stu-id="3fa35-793">**Settings**</span></span> | <span data-ttu-id="3fa35-794">**值**</span><span class="sxs-lookup"><span data-stu-id="3fa35-794">**Values**</span></span> | <span data-ttu-id="3fa35-795">**運作方式**</span><span class="sxs-lookup"><span data-stu-id="3fa35-795">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="3fa35-796">互動式、UI </span><span class="sxs-lookup"><span data-stu-id="3fa35-796">Interactive, UI,</span></span><br /><span data-ttu-id="3fa35-797">或前景</span><span class="sxs-lookup"><span data-stu-id="3fa35-797">or foreground</span></span> |<span data-ttu-id="3fa35-798">2 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-798">2 seconds</span></span> |<span data-ttu-id="3fa35-799">線性</span><span class="sxs-lookup"><span data-stu-id="3fa35-799">Linear</span></span> |<span data-ttu-id="3fa35-800">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="3fa35-800">maxAttempt</span></span><br /><span data-ttu-id="3fa35-801">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="3fa35-801">deltaBackoff</span></span> |<span data-ttu-id="3fa35-802">3</span><span class="sxs-lookup"><span data-stu-id="3fa35-802">3</span></span><br /><span data-ttu-id="3fa35-803">500 毫秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-803">500 ms</span></span> |<span data-ttu-id="3fa35-804">嘗試 1 - 延遲 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-804">Attempt 1 - delay 500 ms</span></span><br /><span data-ttu-id="3fa35-805">嘗試 2 - 延遲 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-805">Attempt 2 - delay 500 ms</span></span><br /><span data-ttu-id="3fa35-806">嘗試 3 - 延遲 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-806">Attempt 3 - delay 500 ms</span></span> |
| <span data-ttu-id="3fa35-807">背景</span><span class="sxs-lookup"><span data-stu-id="3fa35-807">Background</span></span><br /><span data-ttu-id="3fa35-808">或批次</span><span class="sxs-lookup"><span data-stu-id="3fa35-808">or batch</span></span> |<span data-ttu-id="3fa35-809">30 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-809">30 seconds</span></span> |<span data-ttu-id="3fa35-810">指數</span><span class="sxs-lookup"><span data-stu-id="3fa35-810">Exponential</span></span> |<span data-ttu-id="3fa35-811">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="3fa35-811">maxAttempt</span></span><br /><span data-ttu-id="3fa35-812">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="3fa35-812">deltaBackoff</span></span> |<span data-ttu-id="3fa35-813">5</span><span class="sxs-lookup"><span data-stu-id="3fa35-813">5</span></span><br /><span data-ttu-id="3fa35-814">4 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-814">4 seconds</span></span> |<span data-ttu-id="3fa35-815">嘗試 1 - 延遲 ~3 毫秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-815">Attempt 1 - delay ~3 sec</span></span><br /><span data-ttu-id="3fa35-816">嘗試 2 - 延遲 ~7 秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-816">Attempt 2 - delay ~7 sec</span></span><br /><span data-ttu-id="3fa35-817">嘗試 3 - 延遲 ~15 毫秒</span><span class="sxs-lookup"><span data-stu-id="3fa35-817">Attempt 3 - delay ~15 sec</span></span> |

### <a name="telemetry"></a><span data-ttu-id="3fa35-818">遙測</span><span class="sxs-lookup"><span data-stu-id="3fa35-818">Telemetry</span></span>
<span data-ttu-id="3fa35-819">重試嘗試會記錄到 **TraceSource**。</span><span class="sxs-lookup"><span data-stu-id="3fa35-819">Retry attempts are logged to a **TraceSource**.</span></span> <span data-ttu-id="3fa35-820">您必須設定 **TraceListener** 來擷取事件，並將事件寫入適當的目的地記錄中。</span><span class="sxs-lookup"><span data-stu-id="3fa35-820">You must configure a **TraceListener** to capture the events and write them to a suitable destination log.</span></span> <span data-ttu-id="3fa35-821">您可以使用 **TextWriterTraceListener** 或 **XmlWriterTraceListener** 將資料寫入記錄檔，使用 **EventLogTraceListener** 寫入 Windows 事件記錄檔，或使用 **EventProviderTraceListener** 將追蹤資料寫入 ETW 子系統。</span><span class="sxs-lookup"><span data-stu-id="3fa35-821">You can use the **TextWriterTraceListener** or **XmlWriterTraceListener** to write the data to a log file, the **EventLogTraceListener** to write to the Windows Event Log, or the **EventProviderTraceListener** to write trace data to the ETW subsystem.</span></span> <span data-ttu-id="3fa35-822">您也可以設定自動排清緩衝區，並設定所記錄事件的詳細資訊 (例如錯誤、警告、資訊和詳細資訊)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-822">You can also configure auto-flushing of the buffer, and the verbosity of events that will be logged (for example, Error, Warning, Informational, and Verbose).</span></span> <span data-ttu-id="3fa35-823">如需詳細資訊，請參閱 [使用 .NET 儲存體用戶端程式庫在用戶端記錄](/rest/api/storageservices/Client-side-Logging-with-the-.NET-Storage-Client-Library)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-823">For more information, see [Client-side Logging with the .NET Storage Client Library](/rest/api/storageservices/Client-side-Logging-with-the-.NET-Storage-Client-Library).</span></span>

<span data-ttu-id="3fa35-824">作業會接收 **OperationContext** 執行個體，以公開用於附加自訂遙測邏輯的 **Retrying** 事件。</span><span class="sxs-lookup"><span data-stu-id="3fa35-824">Operations can receive an **OperationContext** instance, which exposes a **Retrying** event that can be used to attach custom telemetry logic.</span></span> <span data-ttu-id="3fa35-825">如需詳細資訊，請參閱 [OperationContext.Retrying Event](/dotnet/api/microsoft.windowsazure.storage.operationcontext.retrying)。</span><span class="sxs-lookup"><span data-stu-id="3fa35-825">For more information, see [OperationContext.Retrying Event](/dotnet/api/microsoft.windowsazure.storage.operationcontext.retrying).</span></span>

### <a name="examples"></a><span data-ttu-id="3fa35-826">範例</span><span class="sxs-lookup"><span data-stu-id="3fa35-826">Examples</span></span>
<span data-ttu-id="3fa35-827">下列程式碼範例示範如何建立兩個具有不同重試設定的 **TableRequestOptions** 執行個體，一個用於互動式要求，一個用於背景要求。</span><span class="sxs-lookup"><span data-stu-id="3fa35-827">The following code example shows how to create two **TableRequestOptions** instances with different retry settings; one for interactive requests and one for background requests.</span></span> <span data-ttu-id="3fa35-828">然後此範例會在用戶端上設定這兩個重試原則，讓這些原則適用於所有要求，此外也在特定要求上設定互動式策略，讓該策略覆寫套用到用戶端的預設設定。</span><span class="sxs-lookup"><span data-stu-id="3fa35-828">The example then sets these two retry policies on the client so that they apply for all requests, and also sets the interactive strategy on a specific request so that it overrides the default settings applied to the client.</span></span>

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

### <a name="more-information"></a><span data-ttu-id="3fa35-829">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="3fa35-829">More information</span></span>
* [<span data-ttu-id="3fa35-830">Azure 儲存體用戶端程式庫重試原則建議</span><span class="sxs-lookup"><span data-stu-id="3fa35-830">Azure Storage Client Library Retry Policy Recommendations</span></span>](https://azure.microsoft.com/blog/2014/05/22/azure-storage-client-library-retry-policy-recommendations/)
* [<span data-ttu-id="3fa35-831">儲存體用戶端程式庫 2.0 – 實作重試原則</span><span class="sxs-lookup"><span data-stu-id="3fa35-831">Storage Client Library 2.0 – Implementing Retry Policies</span></span>](https://gauravmantri.com/2012/12/30/storage-client-library-2-0-implementing-retry-policies/)

## <a name="general-rest-and-retry-guidelines"></a><span data-ttu-id="3fa35-832">一般 REST 與重試指引</span><span class="sxs-lookup"><span data-stu-id="3fa35-832">General REST and retry guidelines</span></span>
<span data-ttu-id="3fa35-833">存取 Azure 或協力廠商服務時請考量下列事項：</span><span class="sxs-lookup"><span data-stu-id="3fa35-833">Consider the following when accessing Azure or third party services:</span></span>

* <span data-ttu-id="3fa35-834">使用系統化的方法管理重試，或許是做為可重複使用的程式碼，讓您可以在所有的用戶端與所有的解決方案之間套用一致的方法。</span><span class="sxs-lookup"><span data-stu-id="3fa35-834">Use a systematic approach to managing retries, perhaps as reusable code, so that you can apply a consistent methodology across all clients and all solutions.</span></span>
* <span data-ttu-id="3fa35-835">如果目標服務或用戶端有沒有內建的重試機制，請考慮使用重試架構 (例如 [Polly][polly]) 來管理重試。</span><span class="sxs-lookup"><span data-stu-id="3fa35-835">Consider using a retry framework such as [Polly][polly] to manage retries if the target service or client has no built-in retry mechanism.</span></span> <span data-ttu-id="3fa35-836">這可以幫助您實作一致的重試行為，且可能會為目標服務提供適合的預設重試策略。</span><span class="sxs-lookup"><span data-stu-id="3fa35-836">This will help you implement a consistent retry behavior, and it may provide a suitable default retry strategy for the target service.</span></span> <span data-ttu-id="3fa35-837">不過，您可能需要為具有非標準行為的服務建立自訂重試程式碼，非標準行為不依例外狀況來表示暫時性錯誤，或如果您想要使用 **Retry-Response** 來管理重試行為，也必須建立自訂重試程式碼。</span><span class="sxs-lookup"><span data-stu-id="3fa35-837">However, you may need to create custom retry code for services that have non-standard behavior, that do not rely on exceptions to indicate transient failures, or if you want to use a **Retry-Response** reply to manage retry behavior.</span></span>
* <span data-ttu-id="3fa35-838">暫時性偵測邏輯將取決於您用來叫用 REST 呼叫的實際用戶端 API。</span><span class="sxs-lookup"><span data-stu-id="3fa35-838">The transient detection logic will depend on the actual client API you use to invoke the REST calls.</span></span> <span data-ttu-id="3fa35-839">有些用戶端 (例如較新的 **HttpClient** 類別)，不會為已完成的要求擲回狀態碼為 HTTP 不成功的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="3fa35-839">Some clients, such as the newer **HttpClient** class, will not throw exceptions for completed requests with a non-success HTTP status code.</span></span> 
* <span data-ttu-id="3fa35-840">從服務傳回的 HTTP 狀態碼可協助指出錯誤是否為暫時性。</span><span class="sxs-lookup"><span data-stu-id="3fa35-840">The HTTP status code returned from the service can help to indicate whether the failure is transient.</span></span> <span data-ttu-id="3fa35-841">您可能需要檢查用戶端產生的例外狀況，或檢查重試架構，以存取狀態碼或決定等同的例外狀況類型。</span><span class="sxs-lookup"><span data-stu-id="3fa35-841">You may need to examine the exceptions generated by a client or the retry framework to access the status code or to determine the equivalent exception type.</span></span> <span data-ttu-id="3fa35-842">下列 HTTP 代碼通常表示重試是適當的：</span><span class="sxs-lookup"><span data-stu-id="3fa35-842">The following HTTP codes typically indicate that a retry is appropriate:</span></span>
  * <span data-ttu-id="3fa35-843">408 要求逾時</span><span class="sxs-lookup"><span data-stu-id="3fa35-843">408 Request Timeout</span></span>
  * <span data-ttu-id="3fa35-844">429 要求太多</span><span class="sxs-lookup"><span data-stu-id="3fa35-844">429 Too Many Requests</span></span>
  * <span data-ttu-id="3fa35-845">500 內部伺服器錯誤</span><span class="sxs-lookup"><span data-stu-id="3fa35-845">500 Internal Server Error</span></span>
  * <span data-ttu-id="3fa35-846">502 錯誤的閘道</span><span class="sxs-lookup"><span data-stu-id="3fa35-846">502 Bad Gateway</span></span>
  * <span data-ttu-id="3fa35-847">503 服務無法使用</span><span class="sxs-lookup"><span data-stu-id="3fa35-847">503 Service Unavailable</span></span>
  * <span data-ttu-id="3fa35-848">504 閘道逾時</span><span class="sxs-lookup"><span data-stu-id="3fa35-848">504 Gateway Timeout</span></span>
* <span data-ttu-id="3fa35-849">如果您讓重試邏輯以例外狀況為基礎，則下列項目通常表示無法建立連接的暫時性錯誤：</span><span class="sxs-lookup"><span data-stu-id="3fa35-849">If you base your retry logic on exceptions, the following typically indicate a transient failure where no connection could be established:</span></span>
  * <span data-ttu-id="3fa35-850">WebExceptionStatus.ConnectionClosed</span><span class="sxs-lookup"><span data-stu-id="3fa35-850">WebExceptionStatus.ConnectionClosed</span></span>
  * <span data-ttu-id="3fa35-851">WebExceptionStatus.ConnectFailure</span><span class="sxs-lookup"><span data-stu-id="3fa35-851">WebExceptionStatus.ConnectFailure</span></span>
  * <span data-ttu-id="3fa35-852">WebExceptionStatus.Timeout</span><span class="sxs-lookup"><span data-stu-id="3fa35-852">WebExceptionStatus.Timeout</span></span>
  * <span data-ttu-id="3fa35-853">WebExceptionStatus.RequestCanceled</span><span class="sxs-lookup"><span data-stu-id="3fa35-853">WebExceptionStatus.RequestCanceled</span></span>
* <span data-ttu-id="3fa35-854">在服務無法使用狀態下，服務可能會先指出適當的延遲，然後在 **Retry-After** 回應標頭或不同的自訂標頭中重試。</span><span class="sxs-lookup"><span data-stu-id="3fa35-854">In the case of a service unavailable status, the service might indicate the appropriate delay before retrying in the **Retry-After** response header or a different custom header.</span></span> <span data-ttu-id="3fa35-855">服務也可能傳送額外的資訊做為自訂標頭，或內嵌在回應的內容中。</span><span class="sxs-lookup"><span data-stu-id="3fa35-855">Services might also send additional information as custom headers, or embedded in the content of the response.</span></span> 
* <span data-ttu-id="3fa35-856">408 要求逾時除外，不要重試代表用戶端錯誤 (4xx 範圍中的錯誤) 的狀態碼。</span><span class="sxs-lookup"><span data-stu-id="3fa35-856">Do not retry for status codes representing client errors (errors in the 4xx range) except for a 408 Request Timeout.</span></span>
* <span data-ttu-id="3fa35-857">在各種條件下 (例如不同的網路狀態及各種的系統負載) 徹底測試您的重試策略和機制。</span><span class="sxs-lookup"><span data-stu-id="3fa35-857">Thoroughly test your retry strategies and mechanisms under a range of conditions, such as different network states and varying system loadings.</span></span>

### <a name="retry-strategies"></a><span data-ttu-id="3fa35-858">重試策略</span><span class="sxs-lookup"><span data-stu-id="3fa35-858">Retry strategies</span></span>
<span data-ttu-id="3fa35-859">以下為一般類型的重試策略間隔：</span><span class="sxs-lookup"><span data-stu-id="3fa35-859">The following are the typical types of retry strategy intervals:</span></span>

* <span data-ttu-id="3fa35-860">**指數**。</span><span class="sxs-lookup"><span data-stu-id="3fa35-860">**Exponential**.</span></span> <span data-ttu-id="3fa35-861">此重試原則會執行指定的重試次數，並使用隨機的指數退避方法來決定重試之間的間隔。</span><span class="sxs-lookup"><span data-stu-id="3fa35-861">A retry policy that performs a specified number of retries, using a randomized exponential back off approach to determine the interval between retries.</span></span> <span data-ttu-id="3fa35-862">例如︰</span><span class="sxs-lookup"><span data-stu-id="3fa35-862">For example:</span></span>

    ```csharp
    var random = new Random();

    var delta = (int)((Math.Pow(2.0, currentRetryCount) - 1.0) *
                random.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8),
                (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
    var interval = (int)Math.Min(checked(this.minBackoff.TotalMilliseconds + delta),
                    this.maxBackoff.TotalMilliseconds);
    retryInterval = TimeSpan.FromMilliseconds(interval);
    ```

* <span data-ttu-id="3fa35-863">**累加**。</span><span class="sxs-lookup"><span data-stu-id="3fa35-863">**Incremental**.</span></span> <span data-ttu-id="3fa35-864">此重試策略具有指定的重試嘗試次數，並在重試之間使用累加時間間隔。</span><span class="sxs-lookup"><span data-stu-id="3fa35-864">A retry strategy with a specified number of retry attempts and an incremental time interval between retries.</span></span> <span data-ttu-id="3fa35-865">例如︰</span><span class="sxs-lookup"><span data-stu-id="3fa35-865">For example:</span></span>

    ```csharp
    retryInterval = TimeSpan.FromMilliseconds(this.initialInterval.TotalMilliseconds +
                    (this.increment.TotalMilliseconds * currentRetryCount));
    ```

* <span data-ttu-id="3fa35-866">**LinearRetry**。</span><span class="sxs-lookup"><span data-stu-id="3fa35-866">**LinearRetry**.</span></span> <span data-ttu-id="3fa35-867">此重試原則會執行指定的重試次數，並在重試之間使用指定的固定時間間隔。</span><span class="sxs-lookup"><span data-stu-id="3fa35-867">A retry policy that performs a specified number of retries, using a specified fixed time interval between retries.</span></span> <span data-ttu-id="3fa35-868">例如︰</span><span class="sxs-lookup"><span data-stu-id="3fa35-868">For example:</span></span>

    ```csharp
    retryInterval = this.deltaBackoff;
    ```

### <a name="transient-fault-handling-with-polly"></a><span data-ttu-id="3fa35-869">Polly 的暫時性錯誤處理</span><span class="sxs-lookup"><span data-stu-id="3fa35-869">Transient fault handling with Polly</span></span>
<span data-ttu-id="3fa35-870">[Polly][polly] 是程式庫，以程式設計方式處理重試和[斷路器][circuit-breaker]策略。</span><span class="sxs-lookup"><span data-stu-id="3fa35-870">[Polly][polly] is a library to programatically handle retries and [circuit breaker][circuit-breaker] strategies.</span></span> <span data-ttu-id="3fa35-871">Polly 專案是 [.NET Foundation][dotnet-foundation] 的成員。</span><span class="sxs-lookup"><span data-stu-id="3fa35-871">The Polly project is a member of the [.NET Foundation][dotnet-foundation].</span></span> <span data-ttu-id="3fa35-872">當服務所在的用戶端本身不支援重試，Polly 是有效的替代方法，可以不必撰寫自訂重試程式碼，但有可能難以正確實作。</span><span class="sxs-lookup"><span data-stu-id="3fa35-872">For services where the client does not natively support retries, Polly is a valid alternative and avoids the need to write custom retry code, which can be hard to implement correctly.</span></span> <span data-ttu-id="3fa35-873">Polly 也可用來在錯誤發生時追蹤錯誤，讓您可以記錄重試。</span><span class="sxs-lookup"><span data-stu-id="3fa35-873">Polly also provides a way to trace errors when they occur, so that you can log retries.</span></span>


<!-- links -->

[adal]: /azure/active-directory/develop/active-directory-authentication-libraries
[autorest]: https://github.com/Azure/autorest/tree/master/docs
[circuit-breaker]: ../patterns/circuit-breaker.md
[ConnectionPolicy.RetryOptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.retryoptions.aspx
[dotnet-foundation]: https://dotnetfoundation.org/
[polly]: http://www.thepollyproject.org
[redis-cache-troubleshoot]: /azure/redis-cache/cache-how-to-troubleshoot
[SearchIndexClient]: https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchindexclient.aspx
[SearchServiceClient]: https://msdn.microsoft.com/library/microsoft.azure.search.searchserviceclient.aspx


### <a name="more-information"></a><span data-ttu-id="3fa35-877">詳細資訊</span><span class="sxs-lookup"><span data-stu-id="3fa35-877">More information</span></span>
* [<span data-ttu-id="3fa35-878">恢復連線</span><span class="sxs-lookup"><span data-stu-id="3fa35-878">Connection Resiliency</span></span>](/ef/core/miscellaneous/connection-resiliency)
* [<span data-ttu-id="3fa35-879">Data Points - EF Core 1.1</span><span class="sxs-lookup"><span data-stu-id="3fa35-879">Data Points - EF Core 1.1</span></span>](https://msdn.microsoft.com/magazine/mt745093.aspx)



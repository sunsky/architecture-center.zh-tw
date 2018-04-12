---
title: 健康情況端點監視
description: 實作應用程式中的功能檢查，而外部工具可透過公開的端點定期存取此應用程式。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories:
- availability
- management-monitoring
- resiliency
ms.openlocfilehash: 3b3bce46b460148af17bfe6064cd052a5f9a6458
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="health-endpoint-monitoring-pattern"></a><span data-ttu-id="1c34d-104">健康情況端點監視模式</span><span class="sxs-lookup"><span data-stu-id="1c34d-104">Health Endpoint Monitoring pattern</span></span>

[!INCLUDE [header](../_includes/header.md)]

<span data-ttu-id="1c34d-105">實作應用程式中的功能檢查，而外部工具可透過公開的端點定期存取此應用程式。</span><span class="sxs-lookup"><span data-stu-id="1c34d-105">Implement functional checks in an application that external tools can access through exposed endpoints at regular intervals.</span></span> <span data-ttu-id="1c34d-106">這可協助確認應用程式和服務正確執行。</span><span class="sxs-lookup"><span data-stu-id="1c34d-106">This can help to verify that applications and services are performing correctly.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="1c34d-107">內容和問題</span><span class="sxs-lookup"><span data-stu-id="1c34d-107">Context and problem</span></span>

<span data-ttu-id="1c34d-108">監視 Web 應用程式和後端服務以確保它們可用且正確執行，是良好的做法，而且通常是商務需求。</span><span class="sxs-lookup"><span data-stu-id="1c34d-108">It's a good practice, and often a business requirement, to monitor web applications and back-end services, to ensure they're available and performing correctly.</span></span> <span data-ttu-id="1c34d-109">不過，監視在雲端中執行的服務比監視內部部署服務還要困難。</span><span class="sxs-lookup"><span data-stu-id="1c34d-109">However, it's more difficult to monitor services running in the cloud than it is to monitor on-premises services.</span></span> <span data-ttu-id="1c34d-110">例如，您沒有裝載環境的完整控制權，服務通常相依於平台廠商和其他人所提供的其他服務。</span><span class="sxs-lookup"><span data-stu-id="1c34d-110">For example, you don't have full control of the hosting environment, and the services typically depend on other services provided by platform vendors and others.</span></span>

<span data-ttu-id="1c34d-111">有許多因素會影響雲端裝載應用程式，例如網路延遲、基礎運算和儲存體裝置的效能和可用性，以及它們之間的網路頻寬。</span><span class="sxs-lookup"><span data-stu-id="1c34d-111">There are many factors that affect cloud-hosted applications such as network latency, the performance and availability of the underlying compute and storage systems, and the network bandwidth between them.</span></span> <span data-ttu-id="1c34d-112">服務會因為這些因素任何一項而部分或完全失敗。</span><span class="sxs-lookup"><span data-stu-id="1c34d-112">The service can fail entirely or partially due to any of these factors.</span></span> <span data-ttu-id="1c34d-113">因此，您必須定期確認服務正確執行，以確保可用性的必要層級，這可能是您的服務等級協定 (SLA) 的一部分。</span><span class="sxs-lookup"><span data-stu-id="1c34d-113">Therefore, you must verify at regular intervals that the service is performing correctly to ensure the required level of availability, which might be part of your service level agreement (SLA).</span></span>

## <a name="solution"></a><span data-ttu-id="1c34d-114">解決方法</span><span class="sxs-lookup"><span data-stu-id="1c34d-114">Solution</span></span>

<span data-ttu-id="1c34d-115">藉由將要求傳送到應用程式上的端點，實作健康情況監視。</span><span class="sxs-lookup"><span data-stu-id="1c34d-115">Implement health monitoring by sending requests to an endpoint on the application.</span></span> <span data-ttu-id="1c34d-116">應用程式應執行所需的檢查，並傳回其狀態的表示。</span><span class="sxs-lookup"><span data-stu-id="1c34d-116">The application should perform the necessary checks, and return an indication of its status.</span></span>

<span data-ttu-id="1c34d-117">健康情況監視檢查通常結合兩個因素：</span><span class="sxs-lookup"><span data-stu-id="1c34d-117">A health monitoring check typically combines two factors:</span></span>

- <span data-ttu-id="1c34d-118">應用程式或服務執行的檢查 (如果有的話)，以回應健康情況驗證端點的要求。</span><span class="sxs-lookup"><span data-stu-id="1c34d-118">The checks (if any) performed by the application or service in response to the request to the health verification endpoint.</span></span>
- <span data-ttu-id="1c34d-119">依據執行健康情況驗證檢查的工作或架構，分析結果。</span><span class="sxs-lookup"><span data-stu-id="1c34d-119">Analysis of the results by the tool or framework that performs the health verification check.</span></span>

<span data-ttu-id="1c34d-120">回應碼表示應用程式的狀態，以及選擇性表示任何元件或其使用之服務的狀態。</span><span class="sxs-lookup"><span data-stu-id="1c34d-120">The response code indicates the status of the application and, optionally, any components or services it uses.</span></span> <span data-ttu-id="1c34d-121">延遲或回應時間檢查是由監視工具或架構執行。</span><span class="sxs-lookup"><span data-stu-id="1c34d-121">The latency or response time check is performed by the monitoring tool or framework.</span></span> <span data-ttu-id="1c34d-122">此圖提供模式的概觀。</span><span class="sxs-lookup"><span data-stu-id="1c34d-122">The figure provides an overview of the pattern.</span></span>

![模式概觀](./_images/health-endpoint-monitoring-pattern.png)

<span data-ttu-id="1c34d-124">應用程式中健康情況監視程式碼可能會執行的其他檢查包括：</span><span class="sxs-lookup"><span data-stu-id="1c34d-124">Other checks that might be carried out by the health monitoring code in the application include:</span></span>
- <span data-ttu-id="1c34d-125">檢查雲端儲存體或資料庫的可用性和回應時間。</span><span class="sxs-lookup"><span data-stu-id="1c34d-125">Checking cloud storage or a database for availability and response time.</span></span>
- <span data-ttu-id="1c34d-126">檢查其他資源或服務位於應用程式，或位於其他位置但是由應用程式使用。</span><span class="sxs-lookup"><span data-stu-id="1c34d-126">Checking other resources or services located in the application, or located elsewhere but used by the application.</span></span>

<span data-ttu-id="1c34d-127">服務和工具可以用來監視 Web 應用程式，方法是將要求提交至一組可設定的端點，並且根據一組可設定的規則來評估結果。</span><span class="sxs-lookup"><span data-stu-id="1c34d-127">Services and tools are available that monitor web applications by submitting a request to a configurable set of endpoints, and evaluating the results against a set of configurable rules.</span></span> <span data-ttu-id="1c34d-128">建立其唯一目的是要在系統上執行某些功能測試的服務端點相對容易。</span><span class="sxs-lookup"><span data-stu-id="1c34d-128">It's relatively easy to create a service endpoint whose sole purpose is to perform some functional tests on the system.</span></span>

<span data-ttu-id="1c34d-129">可以由監視工具執行的典型檢查包括：</span><span class="sxs-lookup"><span data-stu-id="1c34d-129">Typical checks that can be performed by the monitoring tools include:</span></span>

- <span data-ttu-id="1c34d-130">驗證回應碼。</span><span class="sxs-lookup"><span data-stu-id="1c34d-130">Validating the response code.</span></span> <span data-ttu-id="1c34d-131">例如，200 (良好) 的 HTTP 回應表示應用程式回應且沒有錯誤。</span><span class="sxs-lookup"><span data-stu-id="1c34d-131">For example, an HTTP response of 200 (OK) indicates that the application responded without error.</span></span> <span data-ttu-id="1c34d-132">監視系統也可能會檢查其他回應碼，以提供更完整的結果。</span><span class="sxs-lookup"><span data-stu-id="1c34d-132">The monitoring system might also check for other response codes to give more comprehensive results.</span></span>
- <span data-ttu-id="1c34d-133">檢查回應內容來偵測錯誤，即使已傳回 200 (良好) 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="1c34d-133">Checking the content of the response to detect errors, even when a 200 (OK) status code is returned.</span></span> <span data-ttu-id="1c34d-134">這樣可以偵測只會影響已傳回網頁或服務回應區段的錯誤。</span><span class="sxs-lookup"><span data-stu-id="1c34d-134">This can detect errors that affect only a section of the returned web page or service response.</span></span> <span data-ttu-id="1c34d-135">例如，檢查分頁標題或尋找特定片語，這些項目會指出已傳回正確的分頁。</span><span class="sxs-lookup"><span data-stu-id="1c34d-135">For example, checking the title of a page or looking for a specific phrase that indicates the correct page was returned.</span></span>
- <span data-ttu-id="1c34d-136">測量回應時間，這表示網路延遲與應用程式執行要求所花費之時間的組合。</span><span class="sxs-lookup"><span data-stu-id="1c34d-136">Measuring the response time, which indicates a combination of the network latency and the time that the application took to execute the request.</span></span> <span data-ttu-id="1c34d-137">遞增值表示應用程式或網路出現問題。</span><span class="sxs-lookup"><span data-stu-id="1c34d-137">An increasing value can indicate an emerging problem with the application or network.</span></span>
- <span data-ttu-id="1c34d-138">檢查位於應用程式外部的資源或服務，例如應用程式用來從全域快取傳遞內容的內容傳遞網路。</span><span class="sxs-lookup"><span data-stu-id="1c34d-138">Checking resources or services located outside the application, such as a content delivery network used by the application to deliver content from global caches.</span></span>
- <span data-ttu-id="1c34d-139">檢查 SSL 憑證是否到期。</span><span class="sxs-lookup"><span data-stu-id="1c34d-139">Checking for expiration of SSL certificates.</span></span>
- <span data-ttu-id="1c34d-140">測量 DNS 查閱應用程式 URL 的回應時間，以測量 DNS 延遲和 DNS 失敗。</span><span class="sxs-lookup"><span data-stu-id="1c34d-140">Measuring the response time of a DNS lookup for the URL of the application to measure DNS latency and DNS failures.</span></span>
- <span data-ttu-id="1c34d-141">驗證 DNS 查閱傳回的 URL，以確保正確的項目。</span><span class="sxs-lookup"><span data-stu-id="1c34d-141">Validating the URL returned by the DNS lookup to ensure correct entries.</span></span> <span data-ttu-id="1c34d-142">這樣可協助避免透過 DNS 伺服器上成功攻擊導致的惡意要求重新導向。</span><span class="sxs-lookup"><span data-stu-id="1c34d-142">This can help to avoid malicious request redirection through a successful attack on the DNS server.</span></span>

<span data-ttu-id="1c34d-143">如果從不同的內部部署或裝載位置執行這些檢查，以測量和比較回應時間，也很有用。</span><span class="sxs-lookup"><span data-stu-id="1c34d-143">It's also useful, where possible, to run these checks from different on-premises or hosted locations to measure and compare response times.</span></span> <span data-ttu-id="1c34d-144">在理想情況下，您應該從靠近客戶的位置監視應用程式，以從每個位置取得準確的效能檢視。</span><span class="sxs-lookup"><span data-stu-id="1c34d-144">Ideally you should monitor applications from locations that are close to customers to get an accurate view of the performance from each location.</span></span> <span data-ttu-id="1c34d-145">除了提供更強固的檢查機制，結果可以協助您決定應用程式的部署位置 &mdash; 以及是否將它部署在多個資料中心。</span><span class="sxs-lookup"><span data-stu-id="1c34d-145">In addition to providing a more robust checking mechanism, the results can help you decide on the deployment location for the application&mdash;and whether to deploy it in more than one datacenter.</span></span>

<span data-ttu-id="1c34d-146">測試也應該針對客戶使用的所有服務執行個體執行，以確保應用程式為所有客戶都正確運作。</span><span class="sxs-lookup"><span data-stu-id="1c34d-146">Tests should also be run against all the service instances that customers use to ensure the application is working correctly for all customers.</span></span> <span data-ttu-id="1c34d-147">例如，如果客戶儲存體分散到多個儲存體帳戶，監視處理程序應該全部檢查。</span><span class="sxs-lookup"><span data-stu-id="1c34d-147">For example, if customer storage is spread across more than one storage account, the monitoring process should check all of these.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="1c34d-148">問題和考量</span><span class="sxs-lookup"><span data-stu-id="1c34d-148">Issues and considerations</span></span>

<span data-ttu-id="1c34d-149">當您決定如何實作此模式時，請考慮下列幾點：</span><span class="sxs-lookup"><span data-stu-id="1c34d-149">Consider the following points when deciding how to implement this pattern:</span></span>

<span data-ttu-id="1c34d-150">如何驗證回應。</span><span class="sxs-lookup"><span data-stu-id="1c34d-150">How to validate the response.</span></span> <span data-ttu-id="1c34d-151">例如，只要單一 200 (良好) 狀態碼就足以確認應用程式正常運作嗎？</span><span class="sxs-lookup"><span data-stu-id="1c34d-151">For example, is just a single 200 (OK) status code sufficient to verify the application is working correctly?</span></span> <span data-ttu-id="1c34d-152">提供最基本應用程式可用性的量值，也是此模式最小實作的同時，提供應用程式中作業、趨勢和可能即將發生問題的少數資訊。</span><span class="sxs-lookup"><span data-stu-id="1c34d-152">While this provides the most basic measure of application availability, and is the minimum implementation of this pattern, it provides little information about the operations, trends, and possible upcoming issues in the application.</span></span>

   >  <span data-ttu-id="1c34d-153">請確定只有當找到目標資源並且處理時，應用程式才會正確傳回 200 (良好)。</span><span class="sxs-lookup"><span data-stu-id="1c34d-153">Make sure that the application correctly returns a 200 (OK) only when the target resource is found and processed.</span></span> <span data-ttu-id="1c34d-154">在某些情況下，例如當使用主版頁面裝載目標網頁時，伺服器會傳回 200 (良好) 狀態碼，而不是 404 (找不到) 狀態碼，即使找不到目標內容頁。</span><span class="sxs-lookup"><span data-stu-id="1c34d-154">In some scenarios, such as when using a master page to host the target web page, the server sends back a 200 (OK) status code instead of a 404 (Not Found) code, even when the target content page was not found.</span></span>

<span data-ttu-id="1c34d-155">針對應用程式公開的端點數目。</span><span class="sxs-lookup"><span data-stu-id="1c34d-155">The number of endpoints to expose for an application.</span></span> <span data-ttu-id="1c34d-156">其中一個方法是為應用程式使用的核心服務公開至少一個端點，而為優先順序較低的服務公開另一個端點，對每個監視結果指派不同層級重要性。</span><span class="sxs-lookup"><span data-stu-id="1c34d-156">One approach is to expose at least one endpoint for the core services that the application uses and another for lower priority services, allowing different levels of importance to be assigned to each monitoring result.</span></span> <span data-ttu-id="1c34d-157">另請考慮公開多個端點，例如為每個核心服務公開一個端點，以取得額外監視細微性。</span><span class="sxs-lookup"><span data-stu-id="1c34d-157">Also consider exposing more endpoints, such as one for each core service, for additional monitoring granularity.</span></span> <span data-ttu-id="1c34d-158">例如，健康情況驗證檢查可能會檢查應用程式使用之資料庫、儲存體，以及外部地理編碼服務，各個需要不同層級的執行時間和回應時間。</span><span class="sxs-lookup"><span data-stu-id="1c34d-158">For example, a health verification check might check the database, storage, and an external geocoding service that an application uses, with each requiring a different level of uptime and response time.</span></span> <span data-ttu-id="1c34d-159">如果地理編碼服務或某些其他背景工作在幾分鐘內無法使用，應用程式可能依然狀況良好。</span><span class="sxs-lookup"><span data-stu-id="1c34d-159">The application could still be healthy if the geocoding service, or some other background task, is unavailable for a few minutes.</span></span>

<span data-ttu-id="1c34d-160">是否使用相同端點以進行監視，如同用於一般存取，但是使用為健康情況驗證檢查設計的特定路徑，例如，一般存取端點上的 /HealthCheck/{GUID}/。</span><span class="sxs-lookup"><span data-stu-id="1c34d-160">Whether to use the same endpoint for monitoring as is used for general access, but to a specific path designed for health verification checks, for example, /HealthCheck/{GUID}/ on the general access endpoint.</span></span> <span data-ttu-id="1c34d-161">這可以讓應用程式中的某些功能測試由監視工具執行，例如新增新的使用者註冊、登入以及下測試訂單，同時確認一般存取端點可供使用。</span><span class="sxs-lookup"><span data-stu-id="1c34d-161">This allows some functional tests in the application to be run by the monitoring tools, such as adding a new user registration, signing in, and placing a test order, while also verifying that the general access endpoint is available.</span></span>

<span data-ttu-id="1c34d-162">要在服務中收集以回應監視要求的資訊類型，以及如何傳回這項資訊。</span><span class="sxs-lookup"><span data-stu-id="1c34d-162">The type of information to collect in the service in response to monitoring requests, and how to return this information.</span></span> <span data-ttu-id="1c34d-163">大部分現有工具和架構只著眼於端點傳回的 HTTP 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="1c34d-163">Most existing tools and frameworks look only at the HTTP status code that the endpoint returns.</span></span> <span data-ttu-id="1c34d-164">若要傳回並驗證其他資訊，您可能必須建立自訂監視公用程式或服務。</span><span class="sxs-lookup"><span data-stu-id="1c34d-164">To return and validate additional information, you might have to create a custom monitoring utility or service.</span></span>

<span data-ttu-id="1c34d-165">要收集多少資訊。</span><span class="sxs-lookup"><span data-stu-id="1c34d-165">How much information to collect.</span></span> <span data-ttu-id="1c34d-166">在檢查期間執行過多處理會讓應用程式多載，並且會影響其他使用者。</span><span class="sxs-lookup"><span data-stu-id="1c34d-166">Performing excessive processing during the check can overload the application and impact other users.</span></span> <span data-ttu-id="1c34d-167">花費的時間可能會超過監視系統的逾時，因此它會將應用程式標示為無法使用。</span><span class="sxs-lookup"><span data-stu-id="1c34d-167">The time it takes might exceed the timeout of the monitoring system so it marks the application as unavailable.</span></span> <span data-ttu-id="1c34d-168">大部分應用程式包含檢測，例如會記錄效能和詳細錯誤資訊的錯誤處理常式和效能計數器，這樣可能足夠，而不用從健康情況驗證檢查傳回其他資訊。</span><span class="sxs-lookup"><span data-stu-id="1c34d-168">Most applications include instrumentation such as error handlers and performance counters that log performance and detailed error information, this might be sufficient instead of returning additional information from a health verification check.</span></span>

<span data-ttu-id="1c34d-169">快取端點狀態。</span><span class="sxs-lookup"><span data-stu-id="1c34d-169">Caching the endpoint status.</span></span> <span data-ttu-id="1c34d-170">如果太頻繁地執行健康情況檢查，成本可能相當昂貴。</span><span class="sxs-lookup"><span data-stu-id="1c34d-170">It could be expensive to run the health check too frequently.</span></span> <span data-ttu-id="1c34d-171">例如，如果健康情況狀態是透過儀表板報告，您不想要讓來自儀表板的每個要求都觸發健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="1c34d-171">If the health status is reported through a dashboard, for example, you don't want every request from the dashboard to trigger a health check.</span></span> <span data-ttu-id="1c34d-172">相反地，定期檢查系統健康情況並快取狀態。</span><span class="sxs-lookup"><span data-stu-id="1c34d-172">Instead, periodically check the system health and cache the status.</span></span> <span data-ttu-id="1c34d-173">公開會傳回快取狀態的端點。</span><span class="sxs-lookup"><span data-stu-id="1c34d-173">Expose an endpoint that returns the cached status.</span></span>

<span data-ttu-id="1c34d-174">如何設定監視端點的安全性，保護它們免於公用存取，公用存取可能會讓應用程式公開於惡意攻擊、暴露機密資訊的風險，或吸引阻絕服務 (DoS) 攻擊。</span><span class="sxs-lookup"><span data-stu-id="1c34d-174">How to configure security for the monitoring endpoints to protect them from public access, which might expose the application to malicious attacks, risk the exposure of sensitive information, or attract denial of service (DoS) attacks.</span></span> <span data-ttu-id="1c34d-175">通常這應該在應用程式組態中完成，以便不需要重新啟動應用程式就可以輕鬆地更新。</span><span class="sxs-lookup"><span data-stu-id="1c34d-175">Typically this should be done in the application configuration so that it can be updated easily without restarting the application.</span></span> <span data-ttu-id="1c34d-176">請考慮使用下列一或多個技術：</span><span class="sxs-lookup"><span data-stu-id="1c34d-176">Consider using one or more of the following techniques:</span></span>

- <span data-ttu-id="1c34d-177">透過要求驗證來保護端點。</span><span class="sxs-lookup"><span data-stu-id="1c34d-177">Secure the endpoint by requiring authentication.</span></span> <span data-ttu-id="1c34d-178">您可以藉由以下方法來完成這項操作：在要求標頭中使用驗證安全性金鑰，或是隨著要求傳遞認證，前提是監視服務或工具支援驗證。</span><span class="sxs-lookup"><span data-stu-id="1c34d-178">You can do this by using an authentication security key in the request header or by passing credentials with the request, provided that the monitoring service or tool supports authentication.</span></span>

  - <span data-ttu-id="1c34d-179">使用隱晦或隱藏的端點。</span><span class="sxs-lookup"><span data-stu-id="1c34d-179">Use an obscure or hidden endpoint.</span></span> <span data-ttu-id="1c34d-180">例如，將不同 IP 位址上的端點公開至預設應用程式 URL 使用的 IP 位址，在非標準 HTTP 連接埠上設定端點，和/或使用測試分頁的複雜路徑。</span><span class="sxs-lookup"><span data-stu-id="1c34d-180">For example, expose the endpoint on a different IP address to that used by the default application URL, configure the endpoint on a nonstandard HTTP port, and/or use a complex path to the test page.</span></span> <span data-ttu-id="1c34d-181">您通常可以在應用程式組態中指定其他端點位址和連接埠，視需要將這些端點的項目新增至 DNS 伺服器以避免直接指定 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="1c34d-181">You can usually specify additional endpoint addresses and ports in the application configuration, and add entries for these endpoints to the DNS server if required to avoid having to specify the IP address directly.</span></span>

  - <span data-ttu-id="1c34d-182">在端點上公開方法，可接受例如索引鍵值或作業模式值的參數。</span><span class="sxs-lookup"><span data-stu-id="1c34d-182">Expose a method on an endpoint that accepts a parameter such as a key value or an operation mode value.</span></span> <span data-ttu-id="1c34d-183">根據為此參數提供的值，當收到要求時程式碼可以執行特定測試或一組測試，或者在無法辨識參數值時傳回 404 (找不到) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="1c34d-183">Depending on the value supplied for this parameter, when a request is received the code can perform a specific test or set of tests, or return a 404 (Not Found) error if the parameter value isn't recognized.</span></span> <span data-ttu-id="1c34d-184">辨識的參數值可以在應用程式組態中設定。</span><span class="sxs-lookup"><span data-stu-id="1c34d-184">The recognized parameter values could be set in the application configuration.</span></span>

     >  <span data-ttu-id="1c34d-185">DoS 攻擊對於執行基本功能測試而不會危害應用程式作業之個別端點的影響較低。</span><span class="sxs-lookup"><span data-stu-id="1c34d-185">DoS attacks are likely to have less impact on a separate endpoint that performs basic functional tests without compromising the operation of the application.</span></span> <span data-ttu-id="1c34d-186">在理想情況下，請避免使用可能會公開機密資訊的測試。</span><span class="sxs-lookup"><span data-stu-id="1c34d-186">Ideally, avoid using a test that might expose sensitive information.</span></span> <span data-ttu-id="1c34d-187">如果您必須傳回可能對攻擊者很有用的資訊，請考慮如何保護端點和資料免於未經授權的存取。</span><span class="sxs-lookup"><span data-stu-id="1c34d-187">If you must return information that might be useful to an attacker, consider how you'll protect the endpoint and the data from unauthorized access.</span></span> <span data-ttu-id="1c34d-188">在此情況下，只依賴隱晦程度是不夠的。</span><span class="sxs-lookup"><span data-stu-id="1c34d-188">In this case just relying on obscurity isn't enough.</span></span> <span data-ttu-id="1c34d-189">您也應該考慮使用 HTTPS 連線並加密所有敏感性資料，雖然這樣會增加伺服器的負載。</span><span class="sxs-lookup"><span data-stu-id="1c34d-189">You should also consider using an HTTPS connection and encrypting any sensitive data, although this will increase the load on the server.</span></span>

- <span data-ttu-id="1c34d-190">如何存取使用驗證保護的端點。</span><span class="sxs-lookup"><span data-stu-id="1c34d-190">How to access an endpoint that's secured using authentication.</span></span> <span data-ttu-id="1c34d-191">並非所有工具和架構都可以設定為包含具有健康情況驗證要求的認證。</span><span class="sxs-lookup"><span data-stu-id="1c34d-191">Not all tools and frameworks can be configured to include credentials with the health verification request.</span></span> <span data-ttu-id="1c34d-192">例如，Microsoft Azure 內建健康情況驗證功能無法提供驗證認證。</span><span class="sxs-lookup"><span data-stu-id="1c34d-192">For example, Microsoft Azure built-in health verification features can't provide authentication credentials.</span></span> <span data-ttu-id="1c34d-193">某些協力廠商替代項目為 [Pingdom](https://www.pingdom.com/)、[Panopta](http://www.panopta.com/)、[NewRelic](https://newrelic.com/) 和 [Statuscake](https://www.statuscake.com/)。</span><span class="sxs-lookup"><span data-stu-id="1c34d-193">Some third-party alternatives are [Pingdom](https://www.pingdom.com/), [Panopta](http://www.panopta.com/), [NewRelic](https://newrelic.com/), and [Statuscake](https://www.statuscake.com/).</span></span>

- <span data-ttu-id="1c34d-194">如何確定監視代理程式正確執行。</span><span class="sxs-lookup"><span data-stu-id="1c34d-194">How to ensure that the monitoring agent is performing correctly.</span></span> <span data-ttu-id="1c34d-195">其中一個方法是公開端點，該端點只會傳回應用程式組態的值，或者可以用來測試代理程式的隨機值。</span><span class="sxs-lookup"><span data-stu-id="1c34d-195">One approach is to expose an endpoint that simply returns a value from the application configuration or a random value that can be used to test the agent.</span></span>

   >  <span data-ttu-id="1c34d-196">同時，確定監視系統會對本身執行檢查，例如自我測試與內建測試，以避免它發出誤判結果。</span><span class="sxs-lookup"><span data-stu-id="1c34d-196">Also ensure that the monitoring system performs checks on itself, such as a self-test and built-in test, to avoid it issuing false positive results.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="1c34d-197">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="1c34d-197">When to use this pattern</span></span>

<span data-ttu-id="1c34d-198">這種模式在下列情況中非常有用：</span><span class="sxs-lookup"><span data-stu-id="1c34d-198">This pattern is useful for:</span></span>
- <span data-ttu-id="1c34d-199">監視網站和 Web 應用程式來驗證可用性。</span><span class="sxs-lookup"><span data-stu-id="1c34d-199">Monitoring websites and web applications to verify availability.</span></span>
- <span data-ttu-id="1c34d-200">監視網站和 Web 應用程式來檢查是否正常運作。</span><span class="sxs-lookup"><span data-stu-id="1c34d-200">Monitoring websites and web applications to check for correct operation.</span></span>
- <span data-ttu-id="1c34d-201">監視中介層或共用服務以偵測可能會中斷其他應用程式的失敗，並加以隔離。</span><span class="sxs-lookup"><span data-stu-id="1c34d-201">Monitoring middle-tier or shared services to detect and isolate a failure that could disrupt other applications.</span></span>
- <span data-ttu-id="1c34d-202">在應用程式中補充現有檢測，例如效能計數器和錯誤處理常式。</span><span class="sxs-lookup"><span data-stu-id="1c34d-202">Complementing existing instrumentation in the application, such as performance counters and error handlers.</span></span> <span data-ttu-id="1c34d-203">健康情況驗證檢查不會取代在應用程式中記錄和稽核的需求。</span><span class="sxs-lookup"><span data-stu-id="1c34d-203">Health verification checking doesn't replace the requirement for logging and auditing in the application.</span></span> <span data-ttu-id="1c34d-204">檢測可以提供現有架構的重要資訊，它會監視計數器和錯誤記錄以偵測失敗或其他問題。</span><span class="sxs-lookup"><span data-stu-id="1c34d-204">Instrumentation can provide valuable information for an existing framework that monitors counters and error logs to detect failures or other issues.</span></span> <span data-ttu-id="1c34d-205">不過，如果應用程式無法使用，它無法提供資訊。</span><span class="sxs-lookup"><span data-stu-id="1c34d-205">However, it can't provide information if the application is unavailable.</span></span>

## <a name="example"></a><span data-ttu-id="1c34d-206">範例</span><span class="sxs-lookup"><span data-stu-id="1c34d-206">Example</span></span>

<span data-ttu-id="1c34d-207">下列程式碼範例擷取自 `HealthCheckController` 類別 (示範這個模式可用於 [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/health-endpoint-monitoring) 的範例)，示範公開端點以執行範圍的健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="1c34d-207">The following code examples, taken from the `HealthCheckController` class (a sample that demonstrates this pattern is available on [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/health-endpoint-monitoring)), demonstrates exposing an endpoint for performing a range of health checks.</span></span>

<span data-ttu-id="1c34d-208">`CoreServices` 方法在下方以 C# 顯示，會對應用程式中使用之服務執行一系列的檢查。</span><span class="sxs-lookup"><span data-stu-id="1c34d-208">The `CoreServices` method, shown below in C#, performs a series of checks on services used in the application.</span></span> <span data-ttu-id="1c34d-209">如果所有測試執行都沒有錯誤，則方法會傳回 200 (良好) 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="1c34d-209">If all of the tests run without error, the method returns a 200 (OK) status code.</span></span> <span data-ttu-id="1c34d-210">如果任何測試引發例外狀況，則方法會傳回 500 (內部錯誤) 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="1c34d-210">If any of the tests raises an exception, the method returns a 500 (Internal Error) status code.</span></span> <span data-ttu-id="1c34d-211">如果監視工具或架構可加以利用，方法會在發生錯誤時選擇性地傳回其他資訊。</span><span class="sxs-lookup"><span data-stu-id="1c34d-211">The method could optionally return additional information when an error occurs, if the monitoring tool or framework is able to make use of it.</span></span>

```csharp
public ActionResult CoreServices()
{
  try
  {
    // Run a simple check to ensure the database is available.
    DataStore.Instance.CoreHealthCheck();

    // Run a simple check on our external service.
    MyExternalService.Instance.CoreHealthCheck();
  }
  catch (Exception ex)
  {
    Trace.TraceError("Exception in basic health check: {0}", ex.Message);

    // This can optionally return different status codes based on the exception.
    // Optionally it could return more details about the exception.
    // The additional information could be used by administrators who access the
    // endpoint with a browser, or using a ping utility that can display the
    // additional information.
    return new HttpStatusCodeResult((int)HttpStatusCode.InternalServerError);
  }
  return new HttpStatusCodeResult((int)HttpStatusCode.OK);
}
```
<span data-ttu-id="1c34d-212">`ObscurePath` 方法顯示如何從應用程式組態讀取路徑，並且使用它作為測試端點。</span><span class="sxs-lookup"><span data-stu-id="1c34d-212">The `ObscurePath` method shows how you can read a path from the application configuration and use it as the endpoint for tests.</span></span> <span data-ttu-id="1c34d-213">以 C# 表示的這個範例，也會顯示如何接受識別碼作為參數，並且使用它來檢查有效的要求。</span><span class="sxs-lookup"><span data-stu-id="1c34d-213">This example, in C#, also shows how you can accept an ID as a parameter and use it to check for valid requests.</span></span>

```csharp
public ActionResult ObscurePath(string id)
{
  // The id could be used as a simple way to obscure or hide the endpoint.
  // The id to match could be retrieved from configuration and, if matched,
  // perform a specific set of tests and return the result. If not matched it
  // could return a 404 (Not Found) status.

  // The obscure path can be set through configuration to hide the endpoint.
  var hiddenPathKey = CloudConfigurationManager.GetSetting("Test.ObscurePath");

  // If the value passed does not match that in configuration, return 404 (Not Found).
  if (!string.Equals(id, hiddenPathKey))
  {
    return new HttpStatusCodeResult((int)HttpStatusCode.NotFound);
  }

  // Else continue and run the tests...
  // Return results from the core services test.
  return this.CoreServices();
}
```

<span data-ttu-id="1c34d-214">`TestResponseFromConfig` 方法顯示如何公開端點，執行指定組態集值的檢查。</span><span class="sxs-lookup"><span data-stu-id="1c34d-214">The `TestResponseFromConfig` method shows how you can expose an endpoint that performs a check for a specified configuration setting value.</span></span>

```csharp
public ActionResult TestResponseFromConfig()
{
  // Health check that returns a response code set in configuration for testing.
  var returnStatusCodeSetting = CloudConfigurationManager.GetSetting(
                                                          "Test.ReturnStatusCode");

  int returnStatusCode;

  if (!int.TryParse(returnStatusCodeSetting, out returnStatusCode))
  {
    returnStatusCode = (int)HttpStatusCode.OK;
  }

  return new HttpStatusCodeResult(returnStatusCode);
}
```
## <a name="monitoring-endpoints-in-azure-hosted-applications"></a><span data-ttu-id="1c34d-215">監視 Azure 裝載應用程式中的端點</span><span class="sxs-lookup"><span data-stu-id="1c34d-215">Monitoring endpoints in Azure hosted applications</span></span>

<span data-ttu-id="1c34d-216">監視 Azure 應用程式中端點的一些選項如下：</span><span class="sxs-lookup"><span data-stu-id="1c34d-216">Some options for monitoring endpoints in Azure applications are:</span></span>

- <span data-ttu-id="1c34d-217">使用 Azure 的內建監視功能。</span><span class="sxs-lookup"><span data-stu-id="1c34d-217">Use the built-in monitoring features of Azure.</span></span>

- <span data-ttu-id="1c34d-218">使用協力廠商服務或架構，例如 Microsoft System Center Operations Manager。</span><span class="sxs-lookup"><span data-stu-id="1c34d-218">Use a third-party service or a framework such as Microsoft System Center Operations Manager.</span></span>

- <span data-ttu-id="1c34d-219">建立自訂公用程式或服務，在您自己的伺服器或裝載的伺服器上執行。</span><span class="sxs-lookup"><span data-stu-id="1c34d-219">Create a custom utility or a service that runs on your own or on a hosted server.</span></span>

   >  <span data-ttu-id="1c34d-220">雖然 Azure 提供一組相當完整的監視選項，您還是可以使用其他服務和工具，以提供額外的資訊。</span><span class="sxs-lookup"><span data-stu-id="1c34d-220">Even though Azure provides a reasonably comprehensive set of monitoring options, you can use additional services and tools to provide extra information.</span></span> <span data-ttu-id="1c34d-221">Azure 管理服務提供適用於警示規則的內建監視機制。</span><span class="sxs-lookup"><span data-stu-id="1c34d-221">Azure Management Services provides a built-in monitoring mechanism for alert rules.</span></span> <span data-ttu-id="1c34d-222">Azure 入口網站中管理服務分頁的警示區段，可讓您為服務的每個訂用帳戶設定最多十個警示規則。</span><span class="sxs-lookup"><span data-stu-id="1c34d-222">The alerts section of the management services page in the Azure portal allows you to configure up to ten alert rules per subscription for your services.</span></span> <span data-ttu-id="1c34d-223">這些規則指定服務的條件和臨界值，例如 CPU 負載或者每秒要求或錯誤數目，服務可以自動將電子郵件通知傳送到您在每個規則中定義的地址。</span><span class="sxs-lookup"><span data-stu-id="1c34d-223">These rules specify a condition and a threshold value for a service such as CPU load, or the number of requests or errors per second, and the service can automatically send email notifications to addresses you define in each rule.</span></span>

<span data-ttu-id="1c34d-224">您可以監視的條件取決於您為應用程式選擇的裝載機制 (例如網站、雲端服務、虛擬機器或行動服務)，但是這些項目都包括建立警示規則的能力，使用您在服務設定中指定的 Web 端點。</span><span class="sxs-lookup"><span data-stu-id="1c34d-224">The conditions you can monitor vary depending on the hosting mechanism you choose for your application (such as Web Sites, Cloud Services, Virtual Machines, or Mobile Services), but all of these include the ability to create an alert rule that uses a web endpoint you specify in the settings for your service.</span></span> <span data-ttu-id="1c34d-225">此端點應該會以即時方式回應，讓警示系統可以偵測應用程式是否運作正常。</span><span class="sxs-lookup"><span data-stu-id="1c34d-225">This endpoint should respond in a timely way so that the alert system can detect that the application is operating correctly.</span></span>

>  <span data-ttu-id="1c34d-226">深入了解[建立警示通知][portal-alerts]。</span><span class="sxs-lookup"><span data-stu-id="1c34d-226">Read more information about [creating alert notifications][portal-alerts].</span></span>

<span data-ttu-id="1c34d-227">如果您將應用程式裝載在 Azure 雲端服務 Web 和背景工作角色或虛擬機器，您可以利用 Azure 其中一項稱為「流量管理員」的內建服務。</span><span class="sxs-lookup"><span data-stu-id="1c34d-227">If you host your application in Azure Cloud Services web and worker roles or Virtual Machines, you can take advantage of one of the built-in services in Azure called Traffic Manager.</span></span> <span data-ttu-id="1c34d-228">「流量管理員」是路由和負載平衡服務，可以根據規則和設定的範圍，將要求散發到雲端服務裝載應用程式的特定執行個體。</span><span class="sxs-lookup"><span data-stu-id="1c34d-228">Traffic Manager is a routing and load-balancing service that can distribute requests to specific instances of your Cloud Services hosted application based on a range of rules and settings.</span></span>

<span data-ttu-id="1c34d-229">除了路由要求，「流量管理員」會偵測您定期指定的 URL、連接埠和相對路徑，以判斷在其規則中定義之應用程式的哪些執行個體正在使用中並且回應要求。</span><span class="sxs-lookup"><span data-stu-id="1c34d-229">In addition to routing requests, Traffic Manager pings a URL, port, and relative path that you specify on a regular basis to determine which instances of the application defined in its rules are active and are responding to requests.</span></span> <span data-ttu-id="1c34d-230">如果它偵測到狀態碼 200 (良好)，它會將應用程式標示為可用。</span><span class="sxs-lookup"><span data-stu-id="1c34d-230">If it detects a status code 200 (OK), it marks the application as available.</span></span> <span data-ttu-id="1c34d-231">任何其他狀態碼會讓「流量管理員」將應用程式標示為離線。</span><span class="sxs-lookup"><span data-stu-id="1c34d-231">Any other status code causes Traffic Manager to mark the application as offline.</span></span> <span data-ttu-id="1c34d-232">您可以在「流量管理員」主控台中檢視狀態，並設定規則以將要求重新路由到會回應的應用程式其他執行個體。</span><span class="sxs-lookup"><span data-stu-id="1c34d-232">You can view the status in the Traffic Manager console, and configure the rule to reroute requests to other instances of the application that are responding.</span></span>

<span data-ttu-id="1c34d-233">不過，「流量管理員」在接收監視 URL 的回應時只會等候 10 秒。</span><span class="sxs-lookup"><span data-stu-id="1c34d-233">However, Traffic Manager will only wait ten seconds to receive a response from the monitoring URL.</span></span> <span data-ttu-id="1c34d-234">因此，您應該確定您的健康情況驗證程式碼在這段時間內執行，允許從「流量管理員」到您的應用程式再返回之來回行程的網路延遲。</span><span class="sxs-lookup"><span data-stu-id="1c34d-234">Therefore, you should ensure that your health verification code executes in this time, allowing for network latency for the round trip from Traffic Manager to your application and back again.</span></span>

>  <span data-ttu-id="1c34d-235">深入了解使用[流量管理員監視應用程式](https://azure.microsoft.com/documentation/services/traffic-manager/)。</span><span class="sxs-lookup"><span data-stu-id="1c34d-235">Read more information about using [Traffic Manager to monitor your applications](https://azure.microsoft.com/documentation/services/traffic-manager/).</span></span> <span data-ttu-id="1c34d-236">流量管理員也會在[多個資料中心部署指導方針](https://msdn.microsoft.com/library/dn589779.aspx)中討論。</span><span class="sxs-lookup"><span data-stu-id="1c34d-236">Traffic Manager is also discussed in [Multiple Datacenter Deployment Guidance](https://msdn.microsoft.com/library/dn589779.aspx).</span></span>

## <a name="related-guidance"></a><span data-ttu-id="1c34d-237">相關的指引</span><span class="sxs-lookup"><span data-stu-id="1c34d-237">Related guidance</span></span>

<span data-ttu-id="1c34d-238">下列指導方針在實作此模式時很有用：</span><span class="sxs-lookup"><span data-stu-id="1c34d-238">The following guidance can be useful when implementing this pattern:</span></span>
- <span data-ttu-id="1c34d-239">[檢測和遙測指引](https://msdn.microsoft.com/library/dn589775.aspx)。</span><span class="sxs-lookup"><span data-stu-id="1c34d-239">[Instrumentation and Telemetry Guidance](https://msdn.microsoft.com/library/dn589775.aspx).</span></span> <span data-ttu-id="1c34d-240">檢查服務和元件的健康情況通常是藉由探查來完成，但是讓資訊就地監視應用程式效能及偵測執行階段發生的事件也很有用。</span><span class="sxs-lookup"><span data-stu-id="1c34d-240">Checking the health of services and components is typically done by probing, but it's also useful to have information in place to monitor application performance and detect events that occur at runtime.</span></span> <span data-ttu-id="1c34d-241">此資料可以傳輸回到監視工具，作為健康情況監視的額外資訊。</span><span class="sxs-lookup"><span data-stu-id="1c34d-241">This data can be transmitted back to monitoring tools as additional information for health monitoring.</span></span> <span data-ttu-id="1c34d-242">檢測和遙測指引會探索蒐集應用程式中檢測所收集的遠端診斷資訊。</span><span class="sxs-lookup"><span data-stu-id="1c34d-242">Instrumentation and Telemetry Guidance explores gathering remote diagnostics information that's collected by instrumentation in applications.</span></span>
- <span data-ttu-id="1c34d-243">[接收警示通知][portal-alerts]。</span><span class="sxs-lookup"><span data-stu-id="1c34d-243">[Receiving alert notifications][portal-alerts].</span></span>
- <span data-ttu-id="1c34d-244">此模式包含可下載的[範例應用程式](https://github.com/mspnp/cloud-design-patterns/tree/master/health-endpoint-monitoring)。</span><span class="sxs-lookup"><span data-stu-id="1c34d-244">This pattern includes a downloadable [sample application](https://github.com/mspnp/cloud-design-patterns/tree/master/health-endpoint-monitoring).</span></span>

[portal-alerts]: https://azure.microsoft.com/documentation/articles/insights-receive-alert-notifications/

---
title: 即時詐欺偵測
titleSuffix: Azure Example Scenarios
description: 使用 Azure 事件中樞和串流分析，即時偵測詐騙活動。
author: alexbuckgit
ms.date: 07/05/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
social_image_url: /azure/architecture/example-scenario/data/media/architecture-fraud-detection.png
ms.openlocfilehash: b10838635cb592eb93d35ce745832c55a6daae8b
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58245789"
---
# <a name="real-time-fraud-detection-on-azure"></a><span data-ttu-id="1ae2b-103">Azure 上的即時詐騙偵測</span><span class="sxs-lookup"><span data-stu-id="1ae2b-103">Real-time fraud detection on Azure</span></span>

<span data-ttu-id="1ae2b-104">這個範例案例與需要即時分析資料以偵測詐騙交易或其他異常活動的組織相關。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-104">This example scenario is relevant to organizations that need to analyze data in real time to detect fraudulent transactions or other anomalous activity.</span></span>

<span data-ttu-id="1ae2b-105">潛在適用情形包括識別詐騙信用卡活動或行動電話通話。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-105">Potential applications include identifying fraudulent credit card activity or mobile phone calls.</span></span> <span data-ttu-id="1ae2b-106">傳統的線上分析系統可能需要數小時來轉換及分析資料，以識別異常活動。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-106">Traditional online analytical systems might take hours to transform and analyze the data to identify anomalous activity.</span></span>

<span data-ttu-id="1ae2b-107">藉由使用完全受控的 Azure 服務，例如事件中樞和串流分析，公司可以消除管理個別伺服器的需求，同時減少成本及利用 Microsoft 在雲端規模資料擷取和即時分析方面的專業知識。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-107">By using fully managed Azure services such as Event Hubs and Stream Analytics, companies can eliminate the need to manage individual servers, while reducing costs and leveraging Microsoft's expertise in cloud-scale data ingestion and real-time analytics.</span></span> <span data-ttu-id="1ae2b-108">這個案例特別說明詐騙活動的偵測。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-108">This scenario specifically addresses the detection of fraudulent activity.</span></span> <span data-ttu-id="1ae2b-109">如果您有其他資料分析的需求，應該檢閱可用 [Azure Analytics 服務][product-category]的清單。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-109">If you have other needs for data analytics, you should review the list of available [Azure Analytics services][product-category].</span></span>

<span data-ttu-id="1ae2b-110">這個範例代表更廣泛資料處理架構和策略的一部分。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-110">This sample represents one part of a broader data processing architecture and strategy.</span></span> <span data-ttu-id="1ae2b-111">這方面的其他選項和整體架構會在本文稍後討論。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-111">Other options for this aspect of an overall architecture are discussed later in this article.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="1ae2b-112">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="1ae2b-112">Relevant use cases</span></span>

<span data-ttu-id="1ae2b-113">其他相關的使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="1ae2b-113">Other relevant use cases include:</span></span>

- <span data-ttu-id="1ae2b-114">偵測電子通訊案例中的詐騙行動電話通話。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-114">Detecting fraudulent mobile-phone calls in telecommunications scenarios.</span></span>
- <span data-ttu-id="1ae2b-115">識別銀行機構的詐騙信用卡交易。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-115">Identifying fraudulent credit card transactions for banking institutions.</span></span>
- <span data-ttu-id="1ae2b-116">識別零售或電子商務案例中的詐騙購買。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-116">Identifying fraudulent purchases in retail or e-commerce scenarios.</span></span>

## <a name="architecture"></a><span data-ttu-id="1ae2b-117">架構</span><span class="sxs-lookup"><span data-stu-id="1ae2b-117">Architecture</span></span>

![即時詐騙偵測案例的 Azure 元件架構概觀][architecture]

<span data-ttu-id="1ae2b-119">此案例涵蓋了即時分析管線的後端元件。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-119">This scenario covers the back-end components of a real-time analytics pipeline.</span></span> <span data-ttu-id="1ae2b-120">整個案例的資料流程如下所示：</span><span class="sxs-lookup"><span data-stu-id="1ae2b-120">Data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="1ae2b-121">行動電話通話中繼資料從來源系統傳送到 Azure 事件中樞執行個體。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-121">Mobile phone call metadata is sent from the source system to an Azure Event Hubs instance.</span></span>
2. <span data-ttu-id="1ae2b-122">串流分析作業隨即啟動，它會透過事件中樞來源接收資料。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-122">A Stream Analytics job is started, which receives data via the event hub source.</span></span>
3. <span data-ttu-id="1ae2b-123">串流分析作業會執行預先定義的查詢以轉換輸入串流，並且根據詐騙交易演算法來分析它。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-123">The Stream Analytics job runs a predefined query to transform the input stream and analyze it based on a fraudulent-transaction algorithm.</span></span> <span data-ttu-id="1ae2b-124">此查詢會使用輪轉視窗將串流分割成不同的時態性單位。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-124">This query uses a tumbling window to segment the stream into distinct temporal units.</span></span>
4. <span data-ttu-id="1ae2b-125">串流分析作業會寫入轉換後的串流，該串流代表對於 Azure Blob 儲存體中輸出接收的已偵測詐騙通話。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-125">The Stream Analytics job writes the transformed stream representing detected fraudulent calls to an output sink in Azure Blob storage.</span></span>

### <a name="components"></a><span data-ttu-id="1ae2b-126">元件</span><span class="sxs-lookup"><span data-stu-id="1ae2b-126">Components</span></span>

- <span data-ttu-id="1ae2b-127">[Azure 事件中樞][docs-event-hubs]是即時串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-127">[Azure Event Hubs][docs-event-hubs] is a real-time streaming platform and event ingestion service, capable of receiving and processing millions of events per second.</span></span> <span data-ttu-id="1ae2b-128">事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-128">Event Hubs can process and store events, data, or telemetry produced by distributed software and devices.</span></span> <span data-ttu-id="1ae2b-129">在此案例中，事件中樞會接收要分析是否有詐騙活動的所有通話中繼資料。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-129">In this scenario, Event Hubs receives all phone call metadata to be analyzed for fraudulent activity.</span></span>
- <span data-ttu-id="1ae2b-130">[Azure 串流分析][docs-stream-analytics]是事件處理引擎，可以分析來自裝置和其他資料來源的大量資料流。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-130">[Azure Stream Analytics][docs-stream-analytics] is an event-processing engine that can analyze high volumes of data streaming from devices and other data sources.</span></span> <span data-ttu-id="1ae2b-131">它也支援從資料流擷取資訊，以識別模式和關聯性。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-131">It also supports extracting information from data streams to identify patterns and relationships.</span></span> <span data-ttu-id="1ae2b-132">這些模式可以觸發其他下游動作。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-132">These patterns can trigger other downstream actions.</span></span> <span data-ttu-id="1ae2b-133">在此案例中，串流分析會轉換來自事件中樞的輸入串流，以識別詐騙通話。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-133">In this scenario, Stream Analytics transforms the input stream from Event Hubs to identify fraudulent calls.</span></span>
- <span data-ttu-id="1ae2b-134">在此案例中使用 [Blob 儲存體](/azure/storage/blobs/storage-blobs-introduction)，以儲存串流分析作業的結果。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-134">[Blob storage](/azure/storage/blobs/storage-blobs-introduction) is used in this scenario to store the results of the Stream Analytics job.</span></span>

## <a name="considerations"></a><span data-ttu-id="1ae2b-135">考量</span><span class="sxs-lookup"><span data-stu-id="1ae2b-135">Considerations</span></span>

### <a name="alternatives"></a><span data-ttu-id="1ae2b-136">替代項目</span><span class="sxs-lookup"><span data-stu-id="1ae2b-136">Alternatives</span></span>

<span data-ttu-id="1ae2b-137">許多技術選項都適用於即時訊息擷取、資料儲存、串流處理、分析資料儲存，以及分析和報告。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-137">Many technology choices are available for real-time message ingestion, data storage, stream processing, storage of analytical data, and analytics and reporting.</span></span> <span data-ttu-id="1ae2b-138">如需這些選項、其功能及重要選取準則的概觀，請參閱[巨量資料架構：：即時處理](/azure/architecture/data-guide/technology-choices/real-time-ingestion)，出處：《Azure 資料架構指南》。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-138">For an overview of these options, their capabilities, and key selection criteria, see [Big data architectures: Real-time processing](/azure/architecture/data-guide/technology-choices/real-time-ingestion) in the Azure Data Architecture Guide.</span></span>

<span data-ttu-id="1ae2b-139">此外，Azure 中各種機器學習服務可以產生適用於詐騙偵測的更複雜演算法。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-139">Additionally, more complex algorithms for fraud detection can be produced by various machine learning services in Azure.</span></span> <span data-ttu-id="1ae2b-140">如需這些選項的概觀，請參閱《[Azure 資料架構指南](../../data-guide/index.md)》中的[適用於機器學習的技術選項](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-140">For an overview of these options, see [Technology choices for machine learning](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning) in the [Azure Data Architecture Guide](../../data-guide/index.md).</span></span>

### <a name="availability"></a><span data-ttu-id="1ae2b-141">可用性</span><span class="sxs-lookup"><span data-stu-id="1ae2b-141">Availability</span></span>

<span data-ttu-id="1ae2b-142">「Azure 監視器」提供統一的使用者介面，可供您監視各個不同的 Azure 服務。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-142">Azure Monitor provides unified user interfaces for monitoring across various Azure services.</span></span> <span data-ttu-id="1ae2b-143">如需詳細資訊，請參閱[在 Microsoft Azure 中監視](/azure/monitoring-and-diagnostics/monitoring-overview)。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-143">For more information, see [Monitoring in Microsoft Azure](/azure/monitoring-and-diagnostics/monitoring-overview).</span></span> <span data-ttu-id="1ae2b-144">事件中樞和串流分析都與 Azure 監視器整合在一起。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-144">Event Hubs and Stream Analytics are both integrated with Azure Monitor.</span></span>

<span data-ttu-id="1ae2b-145">如需其他可用性考量，請參閱 Azure Architecture Center 中的[可用性檢查清單][availability]。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-145">For other availability considerations, see the [availability checklist][availability] in the Azure Architecture Center.</span></span>

### <a name="scalability"></a><span data-ttu-id="1ae2b-146">延展性</span><span class="sxs-lookup"><span data-stu-id="1ae2b-146">Scalability</span></span>

<span data-ttu-id="1ae2b-147">此案例的元件是專為超大規模擷取與大量平行即時分析而設計。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-147">The components of this scenario are designed for hyper-scale ingestion and massively parallel real-time analytics.</span></span> <span data-ttu-id="1ae2b-148">Azure 事件中樞可高度調整，每秒可接收和處理數百萬個事件且低延遲。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-148">Azure Event Hubs is highly scalable, capable of receiving and processing millions of events per second with low latency.</span></span> <span data-ttu-id="1ae2b-149">事件中樞可以[自動相應增加](/azure/event-hubs/event-hubs-auto-inflate)輸送量單位數，來符合使用量需求。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-149">Event Hubs can [automatically scale up](/azure/event-hubs/event-hubs-auto-inflate) the number of throughput units to meet usage needs.</span></span> <span data-ttu-id="1ae2b-150">Azure 串流分析可以分析來自許多來源的大量串流資料。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-150">Azure Stream Analytics is capable of analyzing high volumes of streaming data from many sources.</span></span> <span data-ttu-id="1ae2b-151">您可以藉由增加配置來執行串流作業的[串流單位](/azure/stream-analytics/stream-analytics-streaming-unit-consumption)數，以相應增加串流分析。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-151">You can scale up Stream Analytics by increasing the number of [streaming units](/azure/stream-analytics/stream-analytics-streaming-unit-consumption) allocated to execute your streaming job.</span></span>

<span data-ttu-id="1ae2b-152">如需設計可調整解決方案的一般指引，請參閱 Azure Architecture Center 中的[延展性檢查清單][scalability]。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-152">For general guidance on designing scalable solutions, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="1ae2b-153">安全性</span><span class="sxs-lookup"><span data-stu-id="1ae2b-153">Security</span></span>

<span data-ttu-id="1ae2b-154">Azure 事件中樞是以根據共用存取簽章 (SAS) 權杖和事件發行者組合的[驗證和安全性模型][docs-event-hubs-security-model]，來保護資料。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-154">Azure Event Hubs secures data through an [authentication and security model][docs-event-hubs-security-model] based on a combination of Shared Access Signature (SAS) tokens and event publishers.</span></span> <span data-ttu-id="1ae2b-155">事件發行者會定義事件中樞的虛擬端點。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-155">An event publisher defines a virtual endpoint for an event hub.</span></span> <span data-ttu-id="1ae2b-156">發行者只能用來將訊息傳送到事件中樞。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-156">The publisher can only be used to send messages to an event hub.</span></span> <span data-ttu-id="1ae2b-157">您無法接收來自發佈者的訊息。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-157">It is not possible to receive messages from a publisher.</span></span>

<span data-ttu-id="1ae2b-158">如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-158">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="1ae2b-159">災害復原</span><span class="sxs-lookup"><span data-stu-id="1ae2b-159">Resiliency</span></span>

<span data-ttu-id="1ae2b-160">如需設計彈性解決方案的一般指引，請參閱[為 Azure 設計有彈性的應用程式][resiliency]。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-160">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="1ae2b-161">部署案例</span><span class="sxs-lookup"><span data-stu-id="1ae2b-161">Deploy the scenario</span></span>

<span data-ttu-id="1ae2b-162">若要部署此案例，您可以遵循示範如何以手動方式部署案例每個元件的這個[逐步教學課程][tutorial]。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-162">To deploy this scenario, you can follow this [step-by-step tutorial][tutorial] demonstrating how to manually deploy each component of the scenario.</span></span> <span data-ttu-id="1ae2b-163">本教學課程也會提供 .NET 用戶端應用程式，以產生範例通話中繼資料，並將該資料傳送至事件中樞執行個體。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-163">This tutorial also provides a .NET client application to generate sample phone call metadata and send that data to an event hub instance.</span></span>

## <a name="pricing"></a><span data-ttu-id="1ae2b-164">價格</span><span class="sxs-lookup"><span data-stu-id="1ae2b-164">Pricing</span></span>

<span data-ttu-id="1ae2b-165">為了探索執行此案例的成本，所有服務會在成本計算機中預先設定。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-165">To explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="1ae2b-166">若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的資料量。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-166">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected data volume.</span></span>

<span data-ttu-id="1ae2b-167">我們根據您預期取得的流量，提供了三個範例成本設定檔：</span><span class="sxs-lookup"><span data-stu-id="1ae2b-167">We have provided three sample cost profiles based on amount of traffic you expect to get:</span></span>

- <span data-ttu-id="1ae2b-168">[小型][small-pricing]：每月透過 1 個標準串流單位處理 100 萬個事件。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-168">[Small][small-pricing]: process one million events through one standard streaming unit per month.</span></span>
- <span data-ttu-id="1ae2b-169">[中型][medium-pricing]：每月透過 5 個標準串流單位處理 1 億個事件。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-169">[Medium][medium-pricing]: process 100M events through five standard streaming units per month.</span></span>
- <span data-ttu-id="1ae2b-170">[大型][large-pricing]：每月透過 20 個標準串流單位處理 9 億 9900 萬個事件。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-170">[Large][large-pricing]: process 999 million events through 20 standard streaming units per month.</span></span>

## <a name="related-resources"></a><span data-ttu-id="1ae2b-171">相關資源</span><span class="sxs-lookup"><span data-stu-id="1ae2b-171">Related resources</span></span>

<span data-ttu-id="1ae2b-172">更複雜的詐騙偵測案例受益於機器學習模型。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-172">More complex fraud detection scenarios can benefit from a machine learning model.</span></span> <span data-ttu-id="1ae2b-173">如需使用 Machine Learning Server 建置的案例，請參閱[使用 Machine Learning Server 的詐騙偵測][r-server-fraud-detection]。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-173">For scenarios built using Machine Learning Server, see [Fraud detection using Machine Learning Server][r-server-fraud-detection].</span></span> <span data-ttu-id="1ae2b-174">如需使用 Machine Learning Server 的其他解決方案範本，請參閱[資料科學案例和解決方案範本][docs-r-server-sample-solutions]。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-174">For other solution templates using Machine Learning Server, see [Data science scenarios and solution templates][docs-r-server-sample-solutions].</span></span> <span data-ttu-id="1ae2b-175">如需使用 Azure Data Lake Analytics 的範例解決方案，請參閱[使用 Azure Data Lake 和 R 進行詐騙偵測][technet-fraud-detection]。</span><span class="sxs-lookup"><span data-stu-id="1ae2b-175">For an example solution using Azure Data Lake Analytics, see [Using Azure Data Lake and R for Fraud Detection][technet-fraud-detection].</span></span>

<!-- links -->
[product-category]: https://azure.microsoft.com/product-categories/analytics/
[tutorial]: /azure/stream-analytics/stream-analytics-real-time-fraud-detection
[small-pricing]: https://azure.com/e/74149ec312c049ccba79bfb3cfa67606
[medium-pricing]: https://azure.com/e/4fc94f7376de484d8ae67a6958cae60a
[large-pricing]: https://azure.com/e/7da8804396f9428a984578700003ba42
[architecture]: ./media/architecture-fraud-detection.png
[docs-event-hubs]: /azure/event-hubs/event-hubs-what-is-event-hubs
[docs-event-hubs-security-model]: /azure/event-hubs/event-hubs-authentication-and-security-model-overview
[docs-stream-analytics]: /azure/stream-analytics/stream-analytics-introduction
[docs-r-server-sample-solutions]: /machine-learning-server/r/sample-solutions
[r-server-fraud-detection]: https://microsoft.github.io/r-server-fraud-detection/
[technet-fraud-detection]: https://blogs.technet.microsoft.com/machinelearning/2017/06/28/using-azure-data-lake-and-r-for-fraud-detection/
[availability]: /azure/architecture/checklist/availability
[scalability]: /azure/architecture/checklist/scalability
[resiliency]: ../../resiliency/index.md
[security]: /azure/security/

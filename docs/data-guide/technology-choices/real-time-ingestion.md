---
title: 選擇即時訊息擷取技術
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: 9f787a0de5db97f5c0a5651b510e49762fbc44b9
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54482983"
---
# <a name="choosing-a-real-time-message-ingestion-technology-in-azure"></a><span data-ttu-id="23324-102">在 Azure 中選擇即時訊息擷取技術</span><span class="sxs-lookup"><span data-stu-id="23324-102">Choosing a real-time message ingestion technology in Azure</span></span>

<span data-ttu-id="23324-103">即時處理可處理即時擷取的資料流，並以延遲性最低的方式加以處理。</span><span class="sxs-lookup"><span data-stu-id="23324-103">Real time processing deals with streams of data that are captured in real-time and processed with minimal latency.</span></span> <span data-ttu-id="23324-104">許多即時處理解決方案需要有訊息擷取存放區，以作為訊息的緩衝區，以及支援相應放大處理、可靠的傳遞和其他訊息佇列語意。</span><span class="sxs-lookup"><span data-stu-id="23324-104">Many real-time processing solutions need a message ingestion store to act as a buffer for messages, and to support scale-out processing, reliable delivery, and other message queuing semantics.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="what-are-your-options-for-real-time-message-ingestion"></a><span data-ttu-id="23324-105">即時訊息擷取有哪些選項？</span><span class="sxs-lookup"><span data-stu-id="23324-105">What are your options for real-time message ingestion?</span></span>

<!-- markdownlint-enable MD026 -->

- [<span data-ttu-id="23324-106">Azure 事件中樞</span><span class="sxs-lookup"><span data-stu-id="23324-106">Azure Event Hubs</span></span>](/azure/event-hubs/)
- [<span data-ttu-id="23324-107">Azure IoT 中心</span><span class="sxs-lookup"><span data-stu-id="23324-107">Azure IoT Hub</span></span>](/azure/iot-hub/)
- [<span data-ttu-id="23324-108">HDInsight 上的 Kafka</span><span class="sxs-lookup"><span data-stu-id="23324-108">Kafka on HDInsight</span></span>](/azure/hdinsight/kafka/apache-kafka-get-started)

## <a name="azure-event-hubs"></a><span data-ttu-id="23324-109">Azure 事件中心</span><span class="sxs-lookup"><span data-stu-id="23324-109">Azure Event Hubs</span></span>

<span data-ttu-id="23324-110">[Azure 事件中樞](/azure/event-hubs/)是可高度調整的資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。</span><span class="sxs-lookup"><span data-stu-id="23324-110">[Azure Event Hubs](/azure/event-hubs/) is a highly scalable data streaming platform and event ingestion service, capable of receiving and processing millions of events per second.</span></span> <span data-ttu-id="23324-111">事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。</span><span class="sxs-lookup"><span data-stu-id="23324-111">Event Hubs can process and store events, data, or telemetry produced by distributed software and devices.</span></span> <span data-ttu-id="23324-112">傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。</span><span class="sxs-lookup"><span data-stu-id="23324-112">Data sent to an event hub can be transformed and stored using any real-time analytics provider or batching/storage adapters.</span></span> <span data-ttu-id="23324-113">事件中樞可提供低延遲性、大規模的發佈-訂閱功能，因而適用於巨量資料案例。</span><span class="sxs-lookup"><span data-stu-id="23324-113">Event Hubs provides publish-subscribe capabilities with low latency at massive scale, which makes it appropriate for big data scenarios.</span></span>

## <a name="azure-iot-hub"></a><span data-ttu-id="23324-114">Azure IoT 中樞</span><span class="sxs-lookup"><span data-stu-id="23324-114">Azure IoT Hub</span></span>

<span data-ttu-id="23324-115">[Azure IoT 中樞](/azure/iot-hub/)是一項受控服務，可在數百萬個 IoT 裝置和一個雲端式後端之間支援可靠且安全的雙向通訊。</span><span class="sxs-lookup"><span data-stu-id="23324-115">[Azure IoT Hub](/azure/iot-hub/) is a managed service that enables reliable and secure bidirectional communications between millions of IoT devices and a cloud-based back end.</span></span>

<span data-ttu-id="23324-116">IoT 中樞的功能包括：</span><span class="sxs-lookup"><span data-stu-id="23324-116">Feature of IoT Hub include:</span></span>

- <span data-ttu-id="23324-117">多個裝置到雲端和雲端到裝置的通訊選項。</span><span class="sxs-lookup"><span data-stu-id="23324-117">Multiple options for device-to-cloud and cloud-to-device communication.</span></span> <span data-ttu-id="23324-118">這些選項包括單向通訊、檔案傳輸和要求回覆方法。</span><span class="sxs-lookup"><span data-stu-id="23324-118">These options include one-way messaging, file transfer, and request-reply methods.</span></span>
- <span data-ttu-id="23324-119">對其他 Azure 服務的訊息路由。</span><span class="sxs-lookup"><span data-stu-id="23324-119">Message routing to other Azure services.</span></span>
- <span data-ttu-id="23324-120">裝置中繼資料與同步化狀態資訊的可查詢存放區。</span><span class="sxs-lookup"><span data-stu-id="23324-120">Queryable store for device metadata and synchronized state information.</span></span>
- <span data-ttu-id="23324-121">使用個別裝置安全性金鑰或 X.509 憑證的安全通訊與存取控制。</span><span class="sxs-lookup"><span data-stu-id="23324-121">Secure communications and access control using per-device security keys or X.509 certificates.</span></span>
- <span data-ttu-id="23324-122">可監視裝置的連線情況和裝置的身分識別管理事件。</span><span class="sxs-lookup"><span data-stu-id="23324-122">Monitoring of device connectivity and device identity management events.</span></span>

<span data-ttu-id="23324-123">就訊息擷取的角度而言，IoT 中樞與事件中樞相類似。</span><span class="sxs-lookup"><span data-stu-id="23324-123">In terms of message ingestion, IoT Hub is similar to Event Hubs.</span></span> <span data-ttu-id="23324-124">不過，前者是專為管理 IoT 裝置連線 (而不只是訊息擷取) 而設計的。</span><span class="sxs-lookup"><span data-stu-id="23324-124">However, it was specifically designed for managing IoT device connectivity, not just message ingestion.</span></span> <span data-ttu-id="23324-125">如需詳細資訊，請參閱 [Azure IoT 中樞和 Azure 事件中樞的比較](/azure/iot-hub/iot-hub-compare-event-hubs)。</span><span class="sxs-lookup"><span data-stu-id="23324-125">For more information, see [Comparison of Azure IoT Hub and Azure Event Hubs](/azure/iot-hub/iot-hub-compare-event-hubs).</span></span>

## <a name="kafka-on-hdinsight"></a><span data-ttu-id="23324-126">HDInsight 上的 Kafka</span><span class="sxs-lookup"><span data-stu-id="23324-126">Kafka on HDInsight</span></span>

<span data-ttu-id="23324-127">[Apache Kafka](https://kafka.apache.org/) 是開放原始碼分散式串流平台，可用來建置即時資料管線和串流應用程式。</span><span class="sxs-lookup"><span data-stu-id="23324-127">[Apache Kafka](https://kafka.apache.org/) is an open-source distributed streaming platform that can be used to build real-time data pipelines and streaming applications.</span></span> <span data-ttu-id="23324-128">Kafka 也提供類似於訊息佇列的訊息代理程式功能，可讓您發佈和訂閱具名資料流。</span><span class="sxs-lookup"><span data-stu-id="23324-128">Kafka also provides message broker functionality similar to a message queue, where you can publish and subscribe to named data streams.</span></span> <span data-ttu-id="23324-129">它可進行水平擴充、具備容錯能力，且速度極快。</span><span class="sxs-lookup"><span data-stu-id="23324-129">It is horizontally scalable, fault-tolerant, and extremely fast.</span></span> <span data-ttu-id="23324-130">[HDInsight 上的 Kafka](/azure/hdinsight/kafka/apache-kafka-get-started) 可為您提供 Kafka，作為 Azure 中的受控、具備高度調整性和高可用性的服務。</span><span class="sxs-lookup"><span data-stu-id="23324-130">[Kafka on HDInsight](/azure/hdinsight/kafka/apache-kafka-get-started) provides a Kafka as a managed, highly scalable, and highly available service in Azure.</span></span>

<span data-ttu-id="23324-131">Kafka 的常見使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="23324-131">Some common use cases for Kafka are:</span></span>

- <span data-ttu-id="23324-132">**傳訊**。</span><span class="sxs-lookup"><span data-stu-id="23324-132">**Messaging**.</span></span> <span data-ttu-id="23324-133">Kafka 支援發佈-訂閱傳訊模式，因此常會作為訊息代理程式。</span><span class="sxs-lookup"><span data-stu-id="23324-133">Because it supports the publish-subscribe message pattern, Kafka is often used as a message broker.</span></span>
- <span data-ttu-id="23324-134">**活動追蹤**。</span><span class="sxs-lookup"><span data-stu-id="23324-134">**Activity tracking**.</span></span> <span data-ttu-id="23324-135">Kafka 能夠依序登載記錄，因此可用來追蹤和重新建立活動，例如網站上的使用者動作。</span><span class="sxs-lookup"><span data-stu-id="23324-135">Because Kafka provides in-order logging of records, it can be used to track and re-create activities, such as user actions on a web site.</span></span>
- <span data-ttu-id="23324-136">**彙總**。</span><span class="sxs-lookup"><span data-stu-id="23324-136">**Aggregation**.</span></span> <span data-ttu-id="23324-137">您可以利用串流處理來彙總不同串流的資訊，將資訊結合並集中而成為可操作的資料。</span><span class="sxs-lookup"><span data-stu-id="23324-137">Using stream processing, you can aggregate information from different streams to combine and centralize the information into operational data.</span></span>
- <span data-ttu-id="23324-138">**轉換**。</span><span class="sxs-lookup"><span data-stu-id="23324-138">**Transformation**.</span></span> <span data-ttu-id="23324-139">您可以利用串流處理來結合並充實多個輸入主題的資料，而成為一或多個輸出主題。</span><span class="sxs-lookup"><span data-stu-id="23324-139">Using stream processing, you can combine and enrich data from multiple input topics into one or more output topics.</span></span>

## <a name="key-selection-criteria"></a><span data-ttu-id="23324-140">關鍵選取準則</span><span class="sxs-lookup"><span data-stu-id="23324-140">Key selection criteria</span></span>

<span data-ttu-id="23324-141">為了縮小選擇範圍，請先回答下列問題：</span><span class="sxs-lookup"><span data-stu-id="23324-141">To narrow the choices, start by answering these questions:</span></span>

- <span data-ttu-id="23324-142">您的 IoT 裝置與 Azure 之間是否需要雙向通訊？</span><span class="sxs-lookup"><span data-stu-id="23324-142">Do you need two-way communication between your IoT devices and Azure?</span></span> <span data-ttu-id="23324-143">如果是，請選擇 IoT 中樞。</span><span class="sxs-lookup"><span data-stu-id="23324-143">If so, choose IoT Hub.</span></span>

- <span data-ttu-id="23324-144">您需要管理對個別裝置的存取，並且能夠撤銷對特定裝置的存取嗎？</span><span class="sxs-lookup"><span data-stu-id="23324-144">Do you need to manage access for individual devices and be able to revoke access to a specific device?</span></span> <span data-ttu-id="23324-145">如果是，請選擇 IoT 中樞。</span><span class="sxs-lookup"><span data-stu-id="23324-145">If yes, choose IoT Hub.</span></span>

## <a name="capability-matrix"></a><span data-ttu-id="23324-146">功能對照表</span><span class="sxs-lookup"><span data-stu-id="23324-146">Capability matrix</span></span>

<span data-ttu-id="23324-147">下表摘要列出各項功能的主要差異。</span><span class="sxs-lookup"><span data-stu-id="23324-147">The following tables summarize the key differences in capabilities.</span></span>

<!-- markdownlint-disable MD033 -->

| | <span data-ttu-id="23324-148">IoT 中樞</span><span class="sxs-lookup"><span data-stu-id="23324-148">IoT Hub</span></span> | <span data-ttu-id="23324-149">事件中樞</span><span class="sxs-lookup"><span data-stu-id="23324-149">Event Hubs</span></span> | <span data-ttu-id="23324-150">HDInsight 上的 Kafka</span><span class="sxs-lookup"><span data-stu-id="23324-150">Kafka on HDInsight</span></span> |
| --- | --- | --- | --- |
| <span data-ttu-id="23324-151">雲端到裝置通訊</span><span class="sxs-lookup"><span data-stu-id="23324-151">Cloud-to-device communications</span></span> | <span data-ttu-id="23324-152">是</span><span class="sxs-lookup"><span data-stu-id="23324-152">Yes</span></span> | <span data-ttu-id="23324-153">否</span><span class="sxs-lookup"><span data-stu-id="23324-153">No</span></span> | <span data-ttu-id="23324-154">否</span><span class="sxs-lookup"><span data-stu-id="23324-154">No</span></span> |
| <span data-ttu-id="23324-155">裝置起始的檔案上傳</span><span class="sxs-lookup"><span data-stu-id="23324-155">Device-initiated file upload</span></span> | <span data-ttu-id="23324-156">是</span><span class="sxs-lookup"><span data-stu-id="23324-156">Yes</span></span> | <span data-ttu-id="23324-157">否</span><span class="sxs-lookup"><span data-stu-id="23324-157">No</span></span> | <span data-ttu-id="23324-158">否</span><span class="sxs-lookup"><span data-stu-id="23324-158">No</span></span> |
| <span data-ttu-id="23324-159">裝置狀態資訊</span><span class="sxs-lookup"><span data-stu-id="23324-159">Device state information</span></span> | [<span data-ttu-id="23324-160">裝置對應項</span><span class="sxs-lookup"><span data-stu-id="23324-160">Device twins</span></span>](/azure/iot-hub/iot-hub-devguide-device-twins) | <span data-ttu-id="23324-161">否</span><span class="sxs-lookup"><span data-stu-id="23324-161">No</span></span> | <span data-ttu-id="23324-162">否</span><span class="sxs-lookup"><span data-stu-id="23324-162">No</span></span> |
| <span data-ttu-id="23324-163">通訊協定支援</span><span class="sxs-lookup"><span data-stu-id="23324-163">Protocol support</span></span> | <span data-ttu-id="23324-164">MQTT、AMQP、HTTPS <sup>1</sup></span><span class="sxs-lookup"><span data-stu-id="23324-164">MQTT, AMQP, HTTPS <sup>1</sup></span></span> | <span data-ttu-id="23324-165">AMQP、HTTPS</span><span class="sxs-lookup"><span data-stu-id="23324-165">AMQP, HTTPS</span></span> | [<span data-ttu-id="23324-166">Kafka 通訊協定</span><span class="sxs-lookup"><span data-stu-id="23324-166">Kafka Protocol</span></span>](https://cwiki.apache.org/confluence/display/KAFKA/A+Guide+To+The+Kafka+Protocol) |
| <span data-ttu-id="23324-167">安全性</span><span class="sxs-lookup"><span data-stu-id="23324-167">Security</span></span> | <span data-ttu-id="23324-168">提供個別裝置身分識別，可撤銷的存取控制。</span><span class="sxs-lookup"><span data-stu-id="23324-168">Per-device identity; revocable access control.</span></span> | <span data-ttu-id="23324-169">共用存取原則，透過發行者原則的有限撤銷。</span><span class="sxs-lookup"><span data-stu-id="23324-169">Shared access policies; limited revocation through publisher policies.</span></span> | <span data-ttu-id="23324-170">使用 SASL 的驗證，插入式授權，與支援的外部驗證服務整合。</span><span class="sxs-lookup"><span data-stu-id="23324-170">Authentication using SASL; pluggable authorization; integration with external authentication services supported.</span></span> |

<!-- markdownlint-enable MD026 -->

<span data-ttu-id="23324-171">[1] 您也可以使用 [Azure IoT 通訊協定閘道](/azure/iot-hub/iot-hub-protocol-gateway)作為自訂閘道，為 IoT 中樞啟用通訊協定調適。</span><span class="sxs-lookup"><span data-stu-id="23324-171">[1] You can also use [Azure IoT protocol gateway](/azure/iot-hub/iot-hub-protocol-gateway) as a custom gateway to enable protocol adaptation for IoT Hub.</span></span>

<span data-ttu-id="23324-172">如需詳細資訊，請參閱 [Azure IoT 中樞和 Azure 事件中樞的比較](/azure/iot-hub/iot-hub-compare-event-hubs)。</span><span class="sxs-lookup"><span data-stu-id="23324-172">For more information, see [Comparison of Azure IoT Hub and Azure Event Hubs](/azure/iot-hub/iot-hub-compare-event-hubs).</span></span>

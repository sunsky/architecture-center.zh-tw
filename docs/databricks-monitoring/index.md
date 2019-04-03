---
title: 使用 Azure 監視器監視 Azure Databricks
description: 在 Azure Log Analytics 中啟用 Azure Databricks 監視的 scala 程式庫
author: petertaylor9999
ms.date: 03/26/2019
ms.openlocfilehash: 6d3d17b2e49919aea7bb08f59e19032c1c8bdafd
ms.sourcegitcommit: 9854bd27fb5cf92041bbfb743d43045cd3552a69
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58503260"
---
# <a name="monitoring-azure-databricks-with-azure-monitor"></a><span data-ttu-id="0f561-103">使用 Azure 監視器監視 Azure Databricks</span><span class="sxs-lookup"><span data-stu-id="0f561-103">Monitoring Azure Databricks with Azure Monitor</span></span>

<span data-ttu-id="0f561-104">[Azure Databricks](/azure/azure-databricks/) 是一種快速、功能強大且以 [Apache Spark](https://spark.apache.org/) 為基礎的分析服務，可讓您輕鬆快速開發並部署巨量資料分析和人工智慧 (AI) 解決方案。</span><span class="sxs-lookup"><span data-stu-id="0f561-104">[Azure Databricks](/azure/azure-databricks/) is a fast, powerful [Apache Spark](https://spark.apache.org/)–based analytics service that makes it easy to rapidly develop and deploy big data analytics and artificial intelligence (AI) solutions.</span></span> <span data-ttu-id="0f561-105">許多使用者在其 Azure Databricks 解決方案中利用筆記本的簡潔性。</span><span class="sxs-lookup"><span data-stu-id="0f561-105">Many users take advantage of the simplicity of notebooks in their Azure Databricks solutions.</span></span> <span data-ttu-id="0f561-106">對於需要更強大運算選項的使用者，Azure Databricks 會支援自訂應用程式程式碼的分散式執行。</span><span class="sxs-lookup"><span data-stu-id="0f561-106">For users that require more robust computing options, Azure Databricks supports the distributed execution of custom application code.</span></span>

<span data-ttu-id="0f561-107">監視是任何生產層級解決方案不可或缺的一部分，而 Azure Databricks 在監視自訂應用程式計量、串流查詢事件以及應用程式記錄檔訊息方面提供強大的功能。</span><span class="sxs-lookup"><span data-stu-id="0f561-107">Monitoring is a critical part of any production-level solution, and Azure Databricks offers robust functionality for monitoring custom application metrics, streaming query events, and application log messages.</span></span> <span data-ttu-id="0f561-108">Azure Databricks 可將此監視記錄傳送到不同的記錄服務。</span><span class="sxs-lookup"><span data-stu-id="0f561-108">Azure Databricks can send this monitoring data to different logging services.</span></span>

<span data-ttu-id="0f561-109">下列文章會顯示將監視資料從 Azure Databricks 傳送到 [Azure 監視器](/azure/azure-monitor/overview)(Azure 的監視資料平台) 的方法。</span><span class="sxs-lookup"><span data-stu-id="0f561-109">The following articles show how to send monitoring data from Azure Databricks to [Azure Monitor](/azure/azure-monitor/overview), the monitoring data platform for Azure.</span></span> <span data-ttu-id="0f561-110">您應該依序遵循這些文章。</span><span class="sxs-lookup"><span data-stu-id="0f561-110">You should follow these articles in order.</span></span>

1. [<span data-ttu-id="0f561-111">設定 Azure Databricks 以傳送計量至 Azure 監視器</span><span class="sxs-lookup"><span data-stu-id="0f561-111">Configure Azure Databricks to send metrics to Azure Monitor</span></span>](./configure-cluster.md)
1. [<span data-ttu-id="0f561-112">傳送 Azure Databricks 應用程式記錄檔至 Azure 監視器</span><span class="sxs-lookup"><span data-stu-id="0f561-112">Send Azure Databricks application logs to Azure Monitor</span></span>](./application-logs.md)
1. [<span data-ttu-id="0f561-113">使用儀表板將 Azure Databricks 計量視覺化</span><span class="sxs-lookup"><span data-stu-id="0f561-113">Use dashboards to visualize Azure Databricks metrics</span></span>](./dashboards.md)

<span data-ttu-id="0f561-114">隨附於這些文章的程式碼程式庫，擴充了 Azure Databricks 的核心監視功能，以將 Spark 計量、事件和記錄資訊傳送至 Azure 監視器。</span><span class="sxs-lookup"><span data-stu-id="0f561-114">The code library that accompanies these articles extends the core monitoring functionality of Azure Databricks to send Spark metrics, events, and logging information to Azure Monitor.</span></span>

<span data-ttu-id="0f561-115">這些文章與其隨附程式碼程式庫的對象是 Apache Spark 與 Azure Databricks 解決方案開發人員。</span><span class="sxs-lookup"><span data-stu-id="0f561-115">The audience for these articles and the accompanying code library are Apache Spark and Azure Databricks solution developers.</span></span> <span data-ttu-id="0f561-116">程式碼必須內建於 Java 封存 (JAR) 檔案中，並接著部署至 Azure Databricks 叢集。</span><span class="sxs-lookup"><span data-stu-id="0f561-116">The code must be built into Java Archive (JAR) files and then deployed to an Azure Databricks cluster.</span></span> <span data-ttu-id="0f561-117">該程式碼是 [Scala](https://www.scala-lang.org/) 和 Java 的組合，使用 [Maven](https://maven.apache.org) 專案物件模型 (POM) 的對應集以建置輸出 JAR 檔。</span><span class="sxs-lookup"><span data-stu-id="0f561-117">The code is a combination of [Scala](https://www.scala-lang.org/) and Java, with a corresponding set of [Maven](https://maven.apache.org) project object model (POM) files to build the output JAR files.</span></span> <span data-ttu-id="0f561-118">建議您先深入了解 Java、Scala 和 Maven。</span><span class="sxs-lookup"><span data-stu-id="0f561-118">Understanding of Java, Scala, and Maven are recommended as prerequisistes.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0f561-119">後續步驟</span><span class="sxs-lookup"><span data-stu-id="0f561-119">Next steps</span></span>

<span data-ttu-id="0f561-120">開始建置程式碼程式庫，並將其部署到您的 Azure Databricks 叢集。</span><span class="sxs-lookup"><span data-stu-id="0f561-120">Start by building the code library and deploying it to your Azure Databricks cluster.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="0f561-121">設定 Azure Databricks 以傳送計量至 Azure 監視器</span><span class="sxs-lookup"><span data-stu-id="0f561-121">Configure Azure Databricks to send metrics to Azure Monitor</span></span>](./configure-cluster.md)
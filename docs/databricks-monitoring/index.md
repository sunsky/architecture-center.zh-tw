---
title: 使用 Azure 監視器監視 Azure Databricks
description: 在 Azure Log Analytics 中啟用 Azure Databricks 監視的 scala 程式庫
author: petertaylor9999
ms.date: 03/26/2019
ms.openlocfilehash: 93798ccf74735a880eab2999008b1495e6a63e10
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640527"
---
# <a name="monitoring-azure-databricks"></a>監視 Azure Databricks

[Azure Databricks](/azure/azure-databricks/) 是一種快速、功能強大且以 [Apache Spark](https://spark.apache.org/) 為基礎的分析服務，可讓您輕鬆快速開發並部署巨量資料分析和人工智慧 (AI) 解決方案。 許多使用者在其 Azure Databricks 解決方案中利用筆記本的簡潔性。 對於需要更強大運算選項的使用者，Azure Databricks 會支援自訂應用程式程式碼的分散式執行。

監視是任何生產層級解決方案不可或缺的一部分，而 Azure Databricks 在監視自訂應用程式計量、串流查詢事件以及應用程式記錄檔訊息方面提供強大的功能。 Azure Databricks 可將此監視記錄傳送到不同的記錄服務。

下列文章會顯示將監視資料從 Azure Databricks 傳送到 [Azure 監視器](/azure/azure-monitor/overview)(Azure 的監視資料平台) 的方法。 您應該依序遵循這些文章。

1. [設定 Azure Databricks 以傳送計量至 Azure 監視器](./configure-cluster.md)
1. [傳送 Azure Databricks 應用程式記錄檔至 Azure 監視器](./application-logs.md)
1. [使用儀表板將 Azure Databricks 計量視覺化](./dashboards.md)

隨附於這些文章的程式碼程式庫，擴充了 Azure Databricks 的核心監視功能，以將 Spark 計量、事件和記錄資訊傳送至 Azure 監視器。

這些文章與其隨附程式碼程式庫的對象是 Apache Spark 與 Azure Databricks 解決方案開發人員。 程式碼必須內建於 Java 封存 (JAR) 檔案中，並接著部署至 Azure Databricks 叢集。 該程式碼是 [Scala](https://www.scala-lang.org/) 和 Java 的組合，使用 [Maven](https://maven.apache.org) 專案物件模型 (POM) 的對應集以建置輸出 JAR 檔。 建議您先深入了解 Java、Scala 和 Maven。

## <a name="next-steps"></a>後續步驟

開始建置程式碼程式庫，並將其部署到您的 Azure Databricks 叢集。

> [!div class="nextstepaction"]
> [設定 Azure Databricks 以傳送計量至 Azure 監視器](./configure-cluster.md)
---
title: 選擇資料管線協調流程技術
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: 31f052cc22f039540c89759c55028c368be1d238
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640782"
---
# <a name="choosing-a-data-pipeline-orchestration-technology-in-azure"></a>在 Azure 中選擇資料管線協調流程技術

大部分的巨量資料解決方案都會包含封裝在工作流程中的重複性資料處理作業。 管線協調器是有助於將這些工作流程自動化的工具。 協調器可以排程工作、執行工作流程，以及協調工作之間的相依性。

## <a name="what-are-your-options-for-data-pipeline-orchestration"></a>資料管線協調流程有哪些選項？

在 Azure 中，下列服務和工具將符合管線協調流程、控制流程和資料移動的核心需求：

- [Azure Data Factory](/azure/data-factory/)
- [HDInsight 上的 Oozie](/azure/hdinsight/hdinsight-use-oozie-linux-mac)
- [SQL Server Integration Services (SSIS)](/sql/integration-services/sql-server-integration-services)

這些服務和工具可以單獨使用或搭配使用，以建立混合式解決方案。 例如，Azure Data Factory V2 中的整合執行階段 (IR) 原本即可在受控 Azure 計算環境中執行 SSIS 套件。 這些服務的功能雖然有部分重疊，但仍有幾項主要差異。

## <a name="key-selection-criteria"></a>關鍵選取準則

為了縮小選擇範圍，請先回答下列問題：

- 您是否需要以巨量資料功能來移動和轉換資料？ 這通常表示數 GB 到 TB 的資料。 如果是，請將您的選擇範圍縮小到最適用於巨量資料的選項。

- 您是否需要可大規模運作的受控服務？ 如果是，請選取不受限於您本機處理能力的其中一項雲端式服務。

- 您是否有部分資料來源位於內部部署？ 如果是，請尋找雲端和內部部署資料來源或目的地皆適用的選項。

- 您的資料來源是否儲存在 HDFS 檔案系統上的 Blob 儲存體中？ 如果是，請選擇支援 Hive 查詢的選項。

## <a name="capability-matrix"></a>功能對照表

下表摘要列出各項功能的主要差異。

### <a name="general-capabilities"></a>一般功能

| | Azure Data Factory | SQL Server Integration Services (SSIS) | HDInsight 上的 Oozie
| --- | --- | --- | --- |
| 受控 | 是 | 否 | 是 |
| 雲端式 | 是 | 否 (本機) | 是 |
| 必要條件 | Azure 訂閱 | SQL Server  | Azure 訂用帳戶、HDInsight 叢集 |
| 管理工具 | Azure 入口網站、PowerShell、CLI、.NET SDK | SSMS、PowerShell | Bash 殼層、Oozie REST API、Oozie Web UI |
| 價格 | 依使用量付費 | 功能的授權/付費 | 除了執行 HDInsight 叢集以外不另收費用 |

### <a name="pipeline-capabilities"></a>管線功能

| | Azure Data Factory | SQL Server Integration Services (SSIS) | HDInsight 上的 Oozie
| --- | --- | --- | --- |
| 複製資料 | 是 | 是 | 是 |
| 自訂轉換 | 是 | 是 | 是 (MapReduce、Pig 和 Hive 作業) |
| Azure Machine Learning 評分 | 是 | 是 (使用指令碼) | 否 |
| HDInsight (隨選) | 是 | 否 | 否 |
| Azure Batch | 是 | 否 | 否 |
| Pig、Hive、MapReduce | 是 | 否 | 是 |
| Spark | 是 | 否 | 否 |
| 執行 SSIS 套件 | 是 | 是 | 否 |
| 控制流程 | 是 | 是 | 是 |
| 存取內部部署資料 | 是 | 是 | 否 |

### <a name="scalability-capabilities"></a>延展性功能

| | Azure Data Factory | SQL Server Integration Services (SSIS) | HDInsight 上的 Oozie
| --- | --- | --- | --- |
| 相應增加 | 是 | 否 | 否 |
| 相應放大 | 是 | 否 | 是 (藉由將背景工作節點新增至叢集) |
| 已針對巨量資料最佳化 | 是 | 否 | 是 |

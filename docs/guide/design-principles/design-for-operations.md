---
title: 作業的設計
titleSuffix: Azure Application Architecture Guide
description: 設計應用程式，讓作業小組擁有所需的工具。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 75eaa7f8e322c66a83d2b43d180780a2fdcd745b
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58245629"
---
# <a name="design-for-operations"></a>作業的設計

## <a name="design-an-application-so-that-the-operations-team-has-the-tools-they-need"></a>設計應用程式，讓作業小組擁有所需的工具

雲端已大幅變更作業小組的角色。 他們不再負責管理主控此應用程式的硬體及基礎結構。  話雖如此，作業仍在執行成功的雲端應用程式中佔很重要的一部分。 作業小組的一些重要功能包括：

- 部署
- 監視
- 升級
- 事件回應
- 安全性稽核

健全的記錄與追蹤在雲端應用程式中特別重要。 讓作業小組參與設計與規劃，以確保應用程式可提供讓他們成功所需的資料與見解。  <!-- to do: Link to DevOps checklist -->

## <a name="recommendations"></a>建議

**讓各個項目可觀察**。 一旦解決方案部署且執行中，記錄檔和追蹤是您了解系統的主要解析方式。 *追蹤*會記錄經過系統的路徑，並可用於找出瓶頸、效能問題和失敗點。 *記錄*會擷取個別事件，例如應用程式狀態變更、錯誤和例外狀況。 在生產環境記錄，否則當您最需要它時，可能會錯過解析。

**檢測監視**。 監視提供對於應用程式執行情況的解析 (良好或狀況不佳)，就可用性、效能和系統健康情況而言。 例如，監視可讓您知道您是否符合 SLA。 監視會在系統正常運作期間進行。 它應該盡可能接近即時，使得作業人員可以快速反應問題。 在理想情況下，監視可以協助在問題導致嚴重失敗之前避開問題。 如需詳細資訊，請參閱[監視和診斷][monitoring]。

**檢測根本原因分析**。 根本原因分析是尋找失敗的根本原因的程序。 分析發生在失敗已發生之後。

**使用分散式追蹤**。 使用針對並行、非同步和雲端規模設計的分散式追蹤系統。 追蹤應包含流過服務界限的相互關聯識別碼。 單一作業可能牽涉到對多個應用程式服務的呼叫。 如果作業失敗，相互關聯識別碼可協助找出失敗的原因。

**將記錄檔和計量標準化**。 作業小組必須從您的解決方案中的各種服務之間彙總記錄檔。 如果每個服務使用自己的記錄格式，要從中取得有用的資訊會變得困難或不可行。 定義通用的結構描述，其中包含欄位如相互關聯識別碼、事件名稱、寄件者的 IP 位址等。 個別服務可以衍生自訂結構描述，其會繼承基底結構描述，並包含額外的欄位。

**自動化管理工作**，包括佈建、部署和監視。 將工作自動化使得工作可重複，且較不容易出現人為錯誤。

**將設定視為程式碼**。 將設定檔簽入版本控制系統，以便您可以追蹤和設定變更的版本，並在必要時復原。

<!-- links -->

[monitoring]: ../../best-practices/monitoring.md

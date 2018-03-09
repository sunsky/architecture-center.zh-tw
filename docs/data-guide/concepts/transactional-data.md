---
title: "交易資料"
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: b7fdbb403d2a438ebc59e40ef58ed8067489dddc
ms.sourcegitcommit: 943e671a8d522cef5ddc8c6e04848134b03c2de4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2018
---
# <a name="transactional-data"></a>交易資料

交易資料是追蹤組織活動的相關互動所產生的資訊。 這些互動通常是商業交易，例如，客戶的付款、對供應商的付款、庫存的產品、接到的訂單，或是交付的服務。 交易事件代表交易本身，通常會包含時間維度、一些數值，以及對其他資料的參考。 

交易通常必須是*不可部分完成*且*一致*的。 不可部分完成性表示整個交易無論成功或失敗都一律是工作單位整體的，且絕不會處於半完成的狀態。 如果無法完成交易，則資料庫系統必須復原該交易中已完成的任何步驟。 在傳統的 RDBMS 中，只要交易無法完成，就會自動執行此復原。 一致性表示交易一律會讓資料保持在有效狀態。 (以上關於不可部分完成性與一致性的說明只是口語化的。 這些屬性另有更為正式的定義，例如 [ACID](https://en.wikipedia.org/wiki/ACID)。)

交易資料庫可對使用各種不同鎖定策略 (例如封閉式鎖定) 的交易支援強式一致性，以確保企業內容中的所有資料對於所有使用者和程序而言都絕對是一致的。 

使用交易資料最常見的部署架構，是 3 層式架構中的資料存放區層。 3 層式架構通常由展示層、商務邏輯層和資料存放區層所組成。 相關部署架構為[多層式](/azure/architecture/guide/architecture-styles/n-tier)架構，此類架構可能會以多個中介層來處理商務邏輯。

![3 層式應用程式的範例](./images/three-tier-application.png)

## <a name="typical-traits-of-transactional-data"></a>交易資料的一般特性

交易資料常會有下列特性：

| 需求 | 說明 |
| --- | --- |
| 正規化 | 高度正規化 |
| 結構描述 | 寫入結構描述，強制執行|
| 一致性 | 強式一致性、ACID 保證 |
| 完整性 | 高度完整性 |
| 使用交易 | yes |
| 鎖定策略 | 封閉式或開放式|
| 可更新 | yes |
| 可附加 | yes |
| 工作負載 | 大量寫入，中度讀取 |
| 編製索引 | 主要和次要索引 |
| 資料大小 | 中小型 |
| 模型 | 關聯式 |
| 資料圖形 | 表格式 |
| 查詢彈性 | 高彈性 |
| 調整 | 小型 (數 MB) 至大型 (數 TB) | 

## <a name="see-also"></a>另請參閱

[線上交易處理](../scenarios/online-transaction-processing.md)

---
title: CAF：中小型企業 - 治理策略背後的初始公司原則
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 2/11/2019
description: 中小型企業 - 治理策略背後的初始公司原則
author: BrianBlanchard
ms.openlocfilehash: 4f49d0aae2c6ab5a5c8feba669cbbb904af2773b
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900783"
---
# <a name="small-to-medium-enterprise-initial-corporate-policy-behind-the-governance-strategy"></a>中小型企業：治理策略背後的初始公司原則

下列公司原則會定義初始治理立場，也就是這趟旅程的起點。 本文定義初期風險、初始原則聲明，以及強制執行原則聲明的初期流程。

> [!NOTE]
>公司原則不是技術文件，但是會推動許多技術決策。 [概觀](./overview.md)中所述的治理 MVP 最終會衍生自這個原則。 實作治理 MVP 之前，貴組織應該根據自己的目標和業務風險來開發公司原則。

## <a name="cloud-governance-team"></a>雲端治理小組

在此敘述中，雲端治理小組是由兩個系統管理員 (辨識治理的需求) 所組成。 接下來幾個月，他們會繼承清除公司雲端目前狀態治理的作業，為他們贏得雲端監管人的稱謂。 在後續演進中，這個稱謂很有可能會變更。

[!INCLUDE [business-risk](../../../../../includes/cloud-adoption/governance/business-risks.md)]

## <a name="tolerance-indicators"></a>承受度指標

目前的風險承受度很高，且投資雲端治理的意願偏低。 因此，承受度指標可當作早期警告系統來觸發更多時間和精力的投資。 如果發現下列指標，您應該發展治理策略。

- 成本管理：部署到雲端的規模超過 100 項資產，或每月支出超過每個月 $1,000 美元。
- 安全性基準：在定義的雲端採用計劃中納入受保護的資料。
- 資源一致性：在定義的雲端採用計劃中納入任何任務關鍵性應用程式。

[!INCLUDE [policy-statements](../../../../../includes/cloud-adoption/governance/policy-statements.md)]

## <a name="next-steps"></a>後續步驟

此公司原則會籌備雲端治理小組來實作治理 MVP，這會是採用基礎。 下一步是實作此 MVP。

> [!div class="nextstepaction"]
> [最佳做法說明](./best-practice-explained.md)
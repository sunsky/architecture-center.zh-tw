---
title: CAF：大型企業 - 治理策略背後的初始公司原則
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 2/11/2019
description: 大型企業 - 治理策略背後的初始公司原則。
author: BrianBlanchard
ms.openlocfilehash: d3bc31f95229a82cbc2f6ac6e684a87107783c07
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901088"
---
# <a name="large-enterprise-initial-corporate-policy-behind-the-governance-strategy"></a>大型企業：治理策略背後的初始公司原則

下列公司原則會定義初始治理立場，也就是這趟旅程的起點。 本文定義初期風險、初始原則聲明，以及強制執行原則聲明的初期流程。

> [!NOTE]
>公司原則不是技術文件，但是會推動許多技術決策。 [概觀](./overview.md)中所述的治理 MVP 最終會衍生自這個原則。 實作治理 MVP 之前，貴組織應該根據自己的目標和業務風險來開發公司原則。

## <a name="cloud-governance-team"></a>雲端治理小組

CIO 最近與 IT 治理小組開會，以了解 PII 和任務關鍵性原則的歷程記錄，及檢閱變更這些原則的影響。 她也討論了雲端對於 IT 部門和公司的整體潛力。

會議之後，有兩個 IT 治理小組成員要求了研究並支援雲端規劃工作的權限。 IT 治理主管認可治理需求以及限制影子 IT 的機會，因此支援這個想法。 因此，雲端治理小組於是誕生。 在接下來的幾個月，他們會從治理的觀點，接手清除在探索雲端期間所造成的許多錯誤。 這讓他們贏得雲端守護者的美名。 在後續的發展中，這趟旅程會顯示如何隨著時間變更其角色。

[!INCLUDE [business-risk](../../../../../includes/cloud-adoption/governance/business-risks.md)]

## <a name="tolerance-indicators"></a>承受度指標

目前的風險承受度很高，且投資雲端治理的意願偏低。 因此，承受度指標可當作早期警告系統來觸發時間和精力的投資。 如果發現下列指標，最好能夠發展治理策略。

- 成本管理：部署到雲端的規模超過 1,000 項資產，或每月支出超過每個月 $10,000 美元。
- 身分識別基準：納入具有舊版或第三方多因素驗證 (MFA) 需求的應用程式。
- 安全性基準：在定義的雲端採用計劃中納入受保護的資料。
- 資源一致性：在定義的雲端採用計劃中納入任何任務關鍵性應用程式。

[!INCLUDE [policy-statements](../../../../../includes/cloud-adoption/governance/policy-statements.md)]

## <a name="next-steps"></a>後續步驟

此公司原則會籌備雲端治理小組來實作治理 MVP，這會是採用基礎。 下一步是實作此 MVP。

> [!div class="nextstepaction"]
> [最佳做法說明](./best-practice-explained.md)
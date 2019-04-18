---
title: CAF：建立雲端轉換的財務模型
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
description: 如何建立雲端轉換的財務模型。
author: BrianBlanchard
ms.date: 12/10/2018
ms.topic: guide
ms.openlocfilehash: 1f3ed8a84b84ba577ad5e5db8b1becd318dc04a3
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068866"
---
# <a name="create-a-financial-model-for-cloud-transformation"></a>建立雲端轉換的財務模型

要建立財務模型以準確呈現任何雲端轉換的完整商業價值，可能是很複雜的作業。 不同的組織會有不同的財務模型和商業論證。 本文將建立某些公式，並指出在建立財務模型時常會疏漏的若干要點。

## <a name="return-on-investment-roi"></a>投資報酬率 (ROI)

投資報酬率 (ROI) 常是高層主管或董事的重要考量依據。 投資報酬率可用來比較投入有限資本資源的不同方式。 投資報酬率的公式相當簡單。 但建立各項公式輸入所需的詳細資料可能不易取得。 基本上，ROI 會是初始投資所產生的回收金額。 此值通常以百分比表示：

![投資報酬率 (ROI) 等於 (投資所得 – 投資成本)/投資成本](../_images/formula-roi.png)

<!-- markdownlint-disable MD036 -->
<!--*ROI = (Gain from Investment &minus; Initial Investment) / Initial Investment*-->
<!-- markdownlint-enable MD036 -->

在下一節中，我們將逐步說明計算初始投資和投資所得 (盈餘) 所需的資料。

## <a name="calculating-initial-investment"></a>計算初始投資

初始投資是指完成轉換所需的資本支出 (CapEx) 和營運費用 (OpEx)。 成本的分類可能會隨著會計模型和 CFO 的偏好而不同。 不過，此類別會包含如下的項目：要轉換的專業服務、僅適用於轉換期間的軟體授權、轉換期間的雲端服務成本，以及轉換期間可能產生的支薪員工成本。

將這些成本加總，即會產生初始投資的估計值。

## <a name="calculating-the-gain-from-investment"></a>計算投資所得

投資所得常需要以第二個公式計算，而此公式大多會隨著商務成果和相關的技術變更而不同。 盈餘並不是扣除掉成本就能計算出來的。

若要計算盈餘，必須使用兩個變數：

![投資所得等於收益差異 + 成本差異](../_images/formula-gain-from-investment.png)

<!-- markdownlint-disable MD036 -->
<!--*Gain from Investment = Revenue Deltas + Cost Deltas*-->
<!-- markdownlint-enable MD036 -->

各項目分述如下。

## <a name="revenue-delta"></a>收益差異

收益差異的預測應與企業合作共同進行。 在企業的利害關係人達成收益影響的共識後，即可據以改善盈利狀況。

## <a name="cost-deltas"></a>成本差異

成本差異是指因轉換而將增加或減少的金額。 有數個獨立變數可能會影響成本的差異。 盈餘主要取決於資本支出降低、成本規避、營運成本降低和折舊降低等硬性成本。 以下幾節將以範例說明應考量的成本差異。

### <a name="depreciation-reductions-or-acceleration"></a>折舊縮減或加速

如需折舊的相關指引，請諮詢 CFO 或財務小組。 以下內容是折舊相關主題的的通用性參考。

投入資本以取得資產時，該項投資有可能是基於財務或稅務用途，目的是要在資產的預期使用期限內產生持續性的效益。 有些公司會將折舊列為稅務利益。 有些公司則將其認列為持續性費用，類似於其他歸屬於年度 IT 預算的週期性費用。

請向財務人員諮詢是否有可能消除折舊，以及此做法是否可對成本差異產生正面貢獻。

### <a name="physical-asset-recovery"></a>實體資產回收

在某些情況下，汰用的資產可轉售而成為收益來源。 為求簡便，此收益通常會一併計入成本降低金額中。 不過，其實質上的意義是收益增加，可能需要按金額課稅。 請諮詢財務人員以了解此選項的可行性，並了解相關收益的會計處理方式。

### <a name="operational-cost-reductions"></a>營運成本降低

企業營運所需的週期性費用，通常稱為營運費用 (OpEx)。 OpEx 類別的涵蓋範圍相當廣泛。 在大多數的會計模型中，此類別會包含軟體授權、裝載費用、電費、不動產租金、冷卻費用、營運所需的臨時員工、設備租金、更換零件、維護合約、維修服務、商務持續性/災害復原 (BC/DR) 服務，和其他多項不需經過資本支出核准的費用。

此類別時，下列其中一個最大的盈餘區域考慮操作的轉換。 只要花時間詳盡列出此份清單，通常都可獲得效益。 請洽詢 CIO 和財務小組，確定所有營運成本都已納入會計帳目。

### <a name="cost-avoidance"></a>成本規避

營運費用 (OpEx) 預期會產生、但尚未納入核准的預算中時，可能無法列為成本降低類別。 例如，如果 VMWare 和 Microsoft 授權必須重新議價並於下一個年度付費，則尚不屬於既定成本。 這類預期成本的降低，將視同因成本差異計算而產生的營運成本。 但非正式的做法是，在議價和預算核准完成之前，應稱之為「成本規避」。

### <a name="soft-cost-reductions"></a>軟性成本降低

某些公司也有可能納入軟性成本，例如降低操作複雜度，或縮減操作資料中心的全職人員。 不過，納入軟性成本可能不是理想的做法。 一旦納入軟性成本，等於是沒由來地假設成本降低金額將等同於有形成本節省金額。 科技專案鮮少會產生實際的軟性成本回收。

### <a name="headcount-reductions"></a>員工人數縮減

節省的工時通常會計入降低的軟性成本中。 這些節省的時間在對應至實際降低的 IT 薪資或人員配置時，可以個別計為員工人數縮減值。

即便如此，內部部署環境所需的技能，通常會對應於雲端中所需的類似 (或更高層級) 的技能集。 這表示在一般情況下，員工並不會因為雲端移轉而遭到解雇。

但由第三方或受控服務提供者 (MSP) 提供運作容量時，則屬例外。 如果 IT 系統由第三方管理，則運作成本可能會取代為雲端原生解決方案或雲端原生 MSP。 雲端原生 MSP 的運作可能更有效率，且成本可能更低。 若是如此，營運成本降低就會歸屬於硬性成本的計算。

### <a name="capital-expense-reductions-or-avoidance"></a>資本支出降低或規避

資本支出 (CapEx) 與營運費用略有不同。 此類別通常由更新週期或資料中心擴充所驅動。 以新的高效能叢集裝載巨量資料解決方案或資料倉儲時，若要將其統合納入 CapEx 類別中，即屬於資料中心擴充的範例。 更常見的是基本更新週期。 有些公司具有嚴謹的硬體，重新整理循環，意義資產已停用，並取代一般的循環 （通常每隔三、 五年或八年）。 這些週期通常會與資產租用週期或預測的設備使用期限一致。 在達到更新週期時，IT 即會提取 CapEx 以購買新設備。

在更新週期通過核准並編入預算後，雲端轉換將有助於消除該成本。 若已規劃更新週期但尚未核准，雲端轉換將可產生 CapEx 成本規避的效果。 這兩個案例都會計入成本差異中。

## <a name="next-steps"></a>後續步驟

請閱讀雲端轉換所造就的範例財會結果。

> [!div class="nextstepaction"]
> [財會結果範例](./business-outcomes/fiscal-outcomes.md)
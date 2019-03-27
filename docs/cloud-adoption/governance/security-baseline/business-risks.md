---
title: CAF：安全性基準動機和業務風險
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 安全性基準動機和業務風險
author: BrianBlanchard
ms.openlocfilehash: 8407ed358e5862e466176096ee6a82ad792027cb
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58247309"
---
# <a name="security-baseline-motivations-and-business-risks"></a>安全性基準動機和業務風險

本文討論客戶通常會在雲端治理策略中採用安全性基準專業領域的原因。 它也提供幾個能驅使原則聲明之潛在商務風險的範例。

<!-- markdownlint-disable MD026 -->

## <a name="is-a-security-baseline-relevant"></a>安全性基準是否相關？

安全性是任何 IT 組織的一大考量。 雲端部署面臨許多與裝載於傳統內部部署資料中心的工作負載相同的安全性風險。 不過，公用雲端平台的性質，缺乏對儲存和執行工作負載之實體硬體的直接擁有權，意味著雲端安全性需要其本身的原則和程序。

區分雲端安全性治理與傳統安全性原則的主要原因之一是可以輕鬆地建立資源，如果在部署之前未考慮安全性，則可能會增加漏洞。 [軟體定義網路 (SDN)](../../decision-guides/software-defined-network/overview.md) 等技術為快速變更雲端架網路拓樸提供的靈活性，也可以輕鬆地以出人意料的方式修改整體網路攻擊面。 雲端平台也提供工具和功能，可以不一定可行的方式在內部部署環境中改善您的安全性功能。

您投入安全性原則和程序的金額，將大量取決於您雲端部署的本質。 初始測試部署可能只需要最基本的安全性原則，而任務關鍵性工作負載則需要解決複雜而廣泛的安全性需求。 在某種程度上所有部署都需要參與該專業領域。

安全性基準專業領域涵蓋了您可以採取的公司原則和手動程序，以保護您的雲端部署免受安全性風險的影響。

> [!NOTE]
>雖然在安全性基準內容下理解[身分識別基準](../identity-baseline/overview.md)以及它與存取控制的關係非常重要，但[五個雲端治理專業領域](../overview.md)會呼叫[身分識別基準](../identity-baseline/overview.md)作為自己的專業領域，與安全性基準區隔。

## <a name="business-risk"></a>業務風險

安全性基準專業領域會嘗試解決與核心安全性相關的商業風險。 在您規劃和實作雲端部署時，與您的企業一起識別這些風險，並監視它們的關聯性。

組織之間的風險不同，但是下列項目可作為通用安全性相關風險，供您在雲端治理小組內討論時作為起點：

- **資料外洩**。 意外暴露或遺失裝載於雲端的敏感性資料，可能會導致失去客戶、合約問題或法律責任。
- **服務中斷**。 由於不安全的基礎結構所導致的中斷和其他效能問題會中斷正常作業，並可能導致產能損失或業務損失。

## <a name="next-steps"></a>後續步驟

使用[雲端管理範本](./template.md)，記錄目前雲端採用方案可能會引入的商務風險。

一旦建立對於實際商務風險的了解，下一步是記錄風險的業務承受度與用來監視承受度的指標和關鍵計量。

> [!div class="nextstepaction"]
> [了解指標、計量及風險承受度](./metrics-tolerance.md)

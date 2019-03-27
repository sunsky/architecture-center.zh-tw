---
title: CAF：成本管理動機和業務風險
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 成本管理動機和業務風險
author: BrianBlanchard
ms.openlocfilehash: b9bf31a796ea1a7530c6a668a231d74b9b765509
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58247469"
---
# <a name="cost-management-motivations-and-business-risks"></a>成本管理動機和業務風險

本文將討論客戶通常會在雲端治理策略中採用成本管理專業領域的原因。 它也提供數個衍生原則聲明的業務風險範例。

<!-- markdownlint-disable MD026 -->

## <a name="is-cost-management-relevant"></a>是否與成本管理相關？

在成本治理方面，採用雲端會建立範式轉移。 傳統內部部署世界中的成本管理會以下列各項為基礎：重新整理週期、資料中心收購、主機更新，以及週期性維護問題。 您可以預測、規劃和精簡這其中每一個成本，以便與每年的資本支出預算保持一致。

對於雲端解決方案，許多企業都傾向於對成本管理採用更具反應性的方法。 在許多情況下，企業將會預購 (或承諾使用) 一定數量的雲端服務。 此模型假設要根據企業規劃來針對特定雲端廠商花費的費用來取得最大折扣，即會建立對已規劃之主動式成本週期的認知。 不過，該認知只有在企業也會實作成熟的成本管理專業領域時成真。

雲端提供在傳統內部部署資料中心前所未聞的自助功能。 這些新功能讓企業能夠更靈活、更少限制且更開放地採用新技術。 不過，自助的缺點是終端使用者會在不知情的情況下超過已配置的預算。 相反地，相同的使用者可以體驗方案中的變化，並且意外地不使用所預測的雲端服務數量。 在治理小組內，任一個方向的轉移可能性，會證明成本管理專業領域中投資的正當性。

## <a name="business-risk"></a>業務風險

成本管理專業領域會嘗試解決與在裝載雲端式工作負載時所產生費用相關的核心業務風險。 在您規劃和實作雲端部署時，與您的企業一起識別這些風險，並監視它們的關聯性。

組織之間的風險各不相同，但是下列項目可成為與成本相關的常見風險，可讓您在雲端治理小組內用來作為討論的起點：

- **預算控制**。 未控制預算，可能導致對於雲端廠商的過度支出。
- **損失使用量**。 未使用預購或預先承諾，可能導致投資浪費。
- **支出異常狀況**：在任一個方向中以未預期的方式突然增加，即為不當使用的指標。
- **過度佈建的資產**。 在超過應用程式或虛擬機器 (VM) 需求的設定中部署資產時，它們會增加成本並形成浪費。

## <a name="next-steps"></a>後續步驟

使用[雲端管理範本](./template.md)，記錄目前雲端採用方案可能會引入的商務風險。

一旦建立對於實際商務風險的了解，下一步是記錄風險的業務承受度與用來監視承受度的指標和關鍵計量。

> [!div class="nextstepaction"]
> [了解指標、計量及風險承受度](./metrics-tolerance.md)

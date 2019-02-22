---
title: CAF：中小型企業治理旅程
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 中小型企業治理旅程
author: BrianBlanchard
ms.openlocfilehash: a3e078845038a12977e7be5affbf22708411069f
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900939"
---
# <a name="small-to-medium-enterprise-governance-journey"></a>中小型企業治理旅程

## <a name="best-practice-overview"></a>最佳做法概觀

這個治理旅程會遵循虛構公司在治理成熟度各個階段的經驗。 以實際的客戶旅程為基礎。 建議的最佳做法則以虛構公司的條件約束和需求為主。

此概觀根據最佳做法定義治理的最小可行性產品 (MVP)，作為快速起點。 它還提供了一些治理演變的連結，這些演變會隨著新業務或技術風險的出現而進一步增加最佳做法。

> [!WARNING]
> 這個 MVP 是基於一組假設的基準起點。 即便是這一系列最佳做法，也是以獨特的業務風險和風險承受度推動的公司原則。 若要查看這些假設是否適用於您，請閱讀本文後面[較長的敘述](./narrative.md)。

## <a name="governance-best-practice"></a>治理最佳做法

這個最佳做法可作為組織用於快速一致地在多個 Azure 訂用帳戶中增加治理護欄的基礎。

### <a name="resource-organization"></a>資源組織

下圖顯示組織資源的治理 MVP 階層。

![資源組織圖](../../../_images/governance/resource-organization.png)

每個應用程式都應該在管理群組、訂用帳戶，以及資源群組階層的適當區域中部署。 在部署規劃期間，雲端治理小組將會在階層中建立必要的節點，讓雲端採用小組更強大。  

1. 每種類型環境 (例如生產、開發和測試) 的管理群組。
2. 每個「應用程式分類」的訂用帳戶。
3. 每個應用程式單獨的資源群組。
4. 應該在此群組階層的每個層級應用一致的命名法。

以下是使用中的這種模式的一個範例：

![中型市場公司的資源組織範例](../../../_images/governance/mid-market-resource-organization.png)

這些模式會提供成長的空間，而不會不必要地使階層複雜化。

[!INCLUDE [governance-of-resources](../../../../../includes/cloud-adoption/governance/governance-of-resources.md)]

## <a name="governance-evolutions"></a>治理演變

一旦部署此 MVP 之後，其他治理層就可以快速地合併到環境中。 以下是一些 MVP 的演變方式，可符合特定的業務需求：

- [受保護資料的安全性基準](./security-baseline-evolution.md)
- [任務關鍵性應用程式的資源設定](./resource-consistency-evolution.md)
- [成本管理控制](./cost-management-evolution.md)
- [多重雲端演進的控制](./multi-cloud-evolution.md)

<!-- markdownlint-disable MD026 -->

## <a name="what-does-this-best-practice-do"></a>這個最佳做法有哪些用途？

在 MVP 中，從[部署加速](../../deployment-acceleration/overview.md)專業領域建立做法和工具是為了快速套用公司原則。 特別是，MVP 會使用 Azure 藍圖、Azure 原則以及 Azure 管理群組套用幾個基本的公司原則，如這個虛構公司的敘述中所定義。 這些公司原則是使用 Resource Manager 範本與 Azure 原則套用的，可建立非常小的身分識別和安全性基準。

![漸進式治理 MVP 的範例](../../../_images/governance/governance-mvp.png)

## <a name="evolving-the-best-practice"></a>發展最佳做法

經過一段時間之後，這個治理 MVP 將用於發展治理的做法。 隨著採用率提高，業務風險也會增加。 CAF 治理模型中的各種專業領域將不斷發展以減輕這些風險。 本系列的後續文章將討論影響虛構公司的公司原則演變。 這些演變會跨三個專業領域進行：

- 成本管理 (隨著採用的規模)。
- 安全性基準 (隨著受保護資料的部署)。
- 資源一致性 (隨著 IT 營運開始支援任務關鍵性工作負載)。

![漸進式治理 MVP 的範例](../../../_images/governance/governance-evolution.png)

## <a name="next-steps"></a>後續步驟

既然您已經熟悉治理 MVP，而且了解後續的治理演變，請閱讀其他背景的支持性敘述。

> [!div class="nextstepaction"]
> [閱讀支持性敘述](./narrative.md)

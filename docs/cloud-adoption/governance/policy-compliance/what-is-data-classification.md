---
title: CAF：什麼是資料分類
description: 什麼是資料分類？
author: BrianBlanchard
ms.date: 2/11/2019
ms.openlocfilehash: 07268e7242d92ac2581bf28b378a3c43d166620c
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900900"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-is-data-classification"></a>什麼是資料分類？

這是資料分類一般主題的簡介文章。 所有治理一般從資料分類開始。

## <a name="business-risks-and-governance"></a>商務風險和治理

在大多數組織中，投資治理的主要原因可以歸納為三種商務風險：

* 與資料安全性缺口建立關聯的責任
* 中斷導致業務停擺
* 非計劃性或非預期的費用

這三種商務風險有很多種變化。 不過，這個趨勢最常見。

## <a name="understand-then-mitigate"></a>了解然後降低

降低任何風險之前，必須先了解風險。 對於資料安全性缺口責任，必須先了解資料分類。 資料分類是將中繼資料特性與數位資產中的每個資產建立關聯的過程，這能夠識別與該資產建立關聯的資料類型。

Microsoft 建議已識別將移轉或部署到雲端的任何潛在候選資產都應該記錄中繼資料，以便記錄資料分類、業務重要性和帳單責任。 這三個分類點對於了解和降低風險很有幫助。

## <a name="microsofts-data-classification"></a>Microsoft 的資料分類

下列是 Microsoft 使用的分類清單。 根據您的產業或現有安全性需求，組織中可能已存在資料分類標準。 如果沒有標準，歡迎您使用此範例分類，以便您更充分了解您的數位資產和風險狀況。  

* **非商務：** 您的個人生活中不屬於 Microsoft 的資料
* **公用︰** 已公開提供且經核准可供公眾使用的商務資料。
* **一般：** 不對外公開的業務資料
* **機密：** 過度共用可能對 Microsoft 造成危害的業務資料
* **高度機密：** 過度共用可能對 Microsoft 造成嚴重危害的業務資料

## <a name="tagging-data-classification-in-azure"></a>在 Azure 中標記資料分類

每個雲端服務提供者都應提供用於記錄任何資產中繼資料的機制。 中繼資料對於管理雲端中的資產極為重要。 對於 Azure，資源標記是中繼資料儲存體的建議方法。 如需 Azure 資源標記的詳細資訊，請參閱[使用標記來組織您的 Azure 資源](/azure/azure-resource-manager/resource-group-using-tags)一文。

## <a name="next-steps"></a>後續步驟

在其中一個可採取動作的治理程序中運用資料分類。

> [!div class="nextstepaction"]
> [開始可採取動作的治理程序](../journeys/overview.md)

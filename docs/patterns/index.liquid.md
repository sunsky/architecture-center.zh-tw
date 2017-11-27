---
title: "雲端設計模式"
description: "Microsoft Azure 的雲端設計模式"
keywords: Azure
ms.openlocfilehash: 264b8296a428f9c1b87314b782efcabc89cf010f
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="cloud-design-patterns"></a>雲端設計模式

[!INCLUDE [header](../../_includes/header.md)]

這些設計模式有助於在雲端中建置可靠、可擴充且安全的應用程式。

每個模式都會說明模式處理的問題、套用模式的考量，以及以 Microsoft Azure 為基礎的範例。 大部分的模式會包括程式碼範例或程式碼片段，示範如何在 Azure 上實作模式。 無論如何，大部分的模式都適用於任一分散式系統 (無論是裝載於 Azure 或其他雲端平台上)。

## <a name="problem-areas-in-the-cloud"></a>雲端中的問題區域

<ul id="categories" class="panel">
{%- for category in categories %}
    <li>
    {% include 'pattern-category-card' %}
    </li>
{%- endfor %}
</ul>

## <a name="catalog-of-patterns"></a>模式的目錄

| 模式 | 摘要 |
| ------- | ------- |
{%- for pattern in patterns %} | [{{ pattern.title }}](./{{ pattern.file }}) | {{ pattern.description }} | {%- endfor %}
---
title: 雲端設計模式
titleSuffix: Azure Architecture Center
description: Microsoft Azure 的雲端設計模式
keywords: Azure
author: dragon119
ms.date: 12/10/2018
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 4229531366f1b0c3257384694cf4358da9e63177
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241479"
---
# <a name="cloud-design-patterns"></a>雲端設計模式

[!INCLUDE [header](../../_includes/header.md)]

這些設計模式有助於在雲端中建置可靠、可擴充且安全的應用程式。

每個模式都會說明模式處理的問題、套用模式的考量，以及以 Microsoft Azure 為基礎的範例。 大部分的模式會包括程式碼範例或程式碼片段，示範如何在 Azure 上實作模式。 無論如何，大部分的模式都適用於任一分散式系統 (無論是裝載於 Azure 或其他雲端平台上)。

## <a name="problem-areas-in-the-cloud"></a>雲端中的問題區域

<!-- markdownlint-disable MD033 -->

<ul id="categories" class="panel">
{%- for category in categories %}
    <li>
    {% include 'pattern-category-card' %}
    </li>
{%- endfor %}
</ul>

<!-- markdownlint-enable MD033 -->

## <a name="catalog-of-patterns"></a>模式的目錄

| 模式 | 總結 |
|---------|---------|
|         |         |

{%- for pattern in patterns %} | [{{ pattern.title }}](./{{ pattern.file }}) | {{ pattern.description }} | {%- endfor %}

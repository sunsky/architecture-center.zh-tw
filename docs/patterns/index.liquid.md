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
# <a name="cloud-design-patterns"></a><span data-ttu-id="7bcfc-104">雲端設計模式</span><span class="sxs-lookup"><span data-stu-id="7bcfc-104">Cloud Design Patterns</span></span>

[!INCLUDE [header](../../_includes/header.md)]

<span data-ttu-id="7bcfc-105">這些設計模式有助於在雲端中建置可靠、可擴充且安全的應用程式。</span><span class="sxs-lookup"><span data-stu-id="7bcfc-105">These design patterns are useful for building reliable, scalable, secure applications in the cloud.</span></span>

<span data-ttu-id="7bcfc-106">每個模式都會說明模式處理的問題、套用模式的考量，以及以 Microsoft Azure 為基礎的範例。</span><span class="sxs-lookup"><span data-stu-id="7bcfc-106">Each pattern describes the problem that the pattern addresses, considerations for applying the pattern, and an example based on Microsoft Azure.</span></span> <span data-ttu-id="7bcfc-107">大部分的模式會包括程式碼範例或程式碼片段，示範如何在 Azure 上實作模式。</span><span class="sxs-lookup"><span data-stu-id="7bcfc-107">Most of the patterns include code samples or snippets that show how to implement the pattern on Azure.</span></span> <span data-ttu-id="7bcfc-108">無論如何，大部分的模式都適用於任一分散式系統 (無論是裝載於 Azure 或其他雲端平台上)。</span><span class="sxs-lookup"><span data-stu-id="7bcfc-108">However, most of the patterns are relevant to any distributed system, whether hosted on Azure or on other cloud platforms.</span></span>

## <a name="problem-areas-in-the-cloud"></a><span data-ttu-id="7bcfc-109">雲端中的問題區域</span><span class="sxs-lookup"><span data-stu-id="7bcfc-109">Problem areas in the cloud</span></span>

<!-- markdownlint-disable MD033 -->

<ul id="categories" class="panel">
<span data-ttu-id="7bcfc-110">{%- for category in categories %}</span><span class="sxs-lookup"><span data-stu-id="7bcfc-110">{%- for category in categories %}</span></span>
    <li>
    <span data-ttu-id="7bcfc-111">{% include 'pattern-category-card' %}</span><span class="sxs-lookup"><span data-stu-id="7bcfc-111">{% include 'pattern-category-card' %}</span></span>
    </li>
<span data-ttu-id="7bcfc-112">{%- endfor %}</span><span class="sxs-lookup"><span data-stu-id="7bcfc-112">{%- endfor %}</span></span>
</ul>

<!-- markdownlint-enable MD033 -->

## <a name="catalog-of-patterns"></a><span data-ttu-id="7bcfc-113">模式的目錄</span><span class="sxs-lookup"><span data-stu-id="7bcfc-113">Catalog of patterns</span></span>

| <span data-ttu-id="7bcfc-114">模式</span><span class="sxs-lookup"><span data-stu-id="7bcfc-114">Pattern</span></span> | <span data-ttu-id="7bcfc-115">總結</span><span class="sxs-lookup"><span data-stu-id="7bcfc-115">Summary</span></span> |
|---------|---------|
|         |         |

<span data-ttu-id="7bcfc-116">{%- for pattern in patterns %} | [{{ pattern.title }}](./{{ pattern.file }}) | {{ pattern.description }} | {%- endfor %}</span><span class="sxs-lookup"><span data-stu-id="7bcfc-116">{%- for pattern in patterns %} | [{{ pattern.title }}](./{{ pattern.file }}) | {{ pattern.description }} | {%- endfor %}</span></span>

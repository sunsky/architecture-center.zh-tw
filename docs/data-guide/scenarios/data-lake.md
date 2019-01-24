---
title: Data Lake
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: d433867886ce0afc219fcc9f35eb7f8b3ce6bee1
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54484698"
---
# <a name="data-lakes"></a>Data Lake

Data Lake 是以資料的原生原始格式保留大量資料的儲存體儲存機制。 Data Lake Store 會針對資料 TB 和 PB 調整進行最佳化。 資料通常來自多個異質性來源，且可能是結構化、半結構化或非結構化。 Data Lake 的概念是將所有資料儲存在其原始、未轉換的狀態。 這種方式不同於傳統的[資料倉儲](../relational-data/data-warehousing.md)，後者會在內嵌時轉換以及處理資料。

Data Lake 的優點包括：

- 資料永遠不會擲回，因為資料會以其原始格式儲存。 這在巨量資料的環境中特別有用，因為您可能無法事先知道可使用資料的哪些深入資訊。
- 使用者可以瀏覽資料，並建立自己的查詢。
- 可能比傳統的 ETL 工具更快。
- 比資料倉儲更有彈性，因為它可以儲存非結構化和半結構化的資料。

完整的 Data Lake 解決方案同時包含儲存和處理。 Data Lake 儲存體專為容錯、無限延展性，以及高輸送量的資料 (具有不同形狀和大小) 擷取所設計。 Data Lake 處理涉及一或多個具有這些建置目標的處理引擎，並可大規模處理 Data Lake 中所儲存的資料。

## <a name="when-to-use-a-data-lake"></a>使用 Data Lake 的時機

Data Lake 的標準用法包括[資料瀏覽](./interactive-data-exploration.md)資料分析和機器學習。

Data Lake 也可以做為資料倉儲的資料來源。 使用此方法時，未經處理的資料會內嵌到資料湖，然後轉換成結構化的查詢格式。 此轉換通常使用 [ELT](../relational-data/etl.md#extract-load-and-transform-elt) (擷取-載入-轉換) 管線，其中資料會就地內嵌和轉換。 已具有關聯性的來源資料可能會使用 ETL 程序，直接進入資料倉儲，並略過 Data Lake。

Data Lake Store 通常會用於事件串流或 IoT 案例，因為它們可以保存大量的關聯式與非關聯式資料，而不需要轉換或結構描述定義。 其建置目的是要處理大量低延遲的小型寫入，並已經過最佳化而可提供龐大輸送量。

## <a name="challenges"></a>挑戰

- 缺少結構描述或描述性中繼資料可能會讓資料難以使用或查詢。
- 缺少資料的一致性語意會讓執行資料分析變得困難，除非使用者對於資料分析非常熟練。
- 這會使得進入 Data Lake 的資料品質很難保證。
- 沒有適當的控管，存取控制和隱私權問題將會成為問題。 什麼資訊會進入 Data Lake，何人可以存取資料，以及為了什麼用途？
- Data Lake 可能不是整合已具有關聯性資料的最佳方式。
- 單獨使用時，Data Lake 不提供整個組織的整合式或整體檢視。
- Data Lake 可能會成為實際上永遠不會被分析或探索深入剖析之資料的垃圾場。

## <a name="relevant-azure-services"></a>相關 Azure 服務

- [Data Lake Store](/azure/data-lake-store/) 是超大規模、與 Hadoop 相容的儲存機制。
- [Data Lake Analytics](/azure/data-lake-analytics/) 是隨選分析作業服務，可簡化巨量資料分析。

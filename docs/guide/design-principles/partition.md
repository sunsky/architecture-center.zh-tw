---
title: 針對限制進行分割
titleSuffix: Azure Application Architecture Guide
description: 使用分割作業來解決資料庫、網路和計算限制。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 76590d2a0c16df9d599e7d4a856a84b5e3bdcec8
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241779"
---
# <a name="partition-around-limits"></a>針對限制進行分割

## <a name="use-partitioning-to-work-around-database-network-and-compute-limits"></a>使用分割作業來解決資料庫、網路和計算限制

雲端中所有服務的相應增加能力都有其限制。 Azure 服務的限制記載於 [Azure 訂用帳戶和服務的限制、配額及條件約束][azure-limits]。 其限制包括核心數目、資料庫大小、查詢輸送量和網路輸送量。 如果您的系統已變得夠大，就可能會達到上述的其中一個或多個限制。 使用資料分割可解決這些限制。

有許多方式可分割系統，例如：

- 分割資料庫以避開資料庫大小、資料 I/O 或並行工作階段數目的限制。

- 分割佇列或訊息匯流排，以避開要求數目或並行連線數目的限制。

- 分割 App Service Web 應用程式，以避開每個 App Service 方案的執行個體數目限制。

資料庫可以進行「水平」、「垂直」或「功能性」分割。

- 在水平分割 (也稱為分區化) 中，每個資料分割都會保有總資料集的一小部分資料。 資料分割會共用相同的資料結構描述。 例如，名稱開頭為 A&ndash;M 的客戶會進入某個資料分割，N&ndash;Z 的客戶則進入另一個資料分割。

- 在垂直分割中，每個資料分割都會保有資料存放區之項目的一小部分欄位。 例如，將經常存取的欄位放在某個資料分割，較不常存取的欄位放在另一個資料分割。

- 在功能性分割中，資料會根據系統中每個繫結的內容使用它的方式進行分割。 例如，將發票資料儲存在某個資料分割，而將產品庫存資料儲存在另一個資料分割。 其結構描述各自獨立。

如需詳細的指導方針，請參閱[資料分割][data-partitioning-guidance]。

## <a name="recommendations"></a>建議

**分割應用程式的不同部分**。 資料庫顯然是其中一個要分割的候選目標，但也請考慮儲存體、快取、佇列和計算執行個體。

**設計資料分割索引鍵以避免作用點**。 如果您分割資料庫，但某個分區仍保有大多數的要求，您還是沒有解決問題。 理論上，負載會平均分散給所有資料分割。 例如，依客戶識別碼產生雜湊，而不是依客戶名稱的第一個字母來產生，因為某些字母的出現頻率較高。 在分割訊息佇列時也適用相同原則。 您所選擇的資料分割索引鍵，必須能將訊息平均分散給整組佇列。 如需詳細資訊，請參閱[分區化][sharding]。

**針對 Azure 訂用帳戶和服務限制來進行分割**。 個別的元件和服務有其限制，但訂用帳戶和資源群組也有限制。 對於非常大型的應用程式，您可能需要針對這些限制來進行分割。

**在不同層級進行分割**。 請設想部署在 VM 上的資料庫伺服器。 該 VM 的 VHD 受到 Azure 儲存體所支援。 儲存體帳戶隸屬於 Azure 訂用帳戶。 請注意到此階層中的每個步驟都有限制。 資料庫伺服器可能有連線集區限制。 VM 有 CPU 和網路限制。 儲存體有 IOPS 限制。 訂用帳戶有 VM 核心數目限制。 一般而言，階層中較低的位置比較容易分割。 只有大型應用程式才必須在訂用帳戶層級進行分割。

<!-- links -->

[azure-limits]: /azure/azure-subscription-service-limits
[data-partitioning-guidance]: ../../best-practices/data-partitioning.md
[sharding]: ../../patterns/sharding.md

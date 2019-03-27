---
title: CAF：軟體定義網路 - 雲端原生
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 討論雲端原生的虛擬網路服務
author: rotycenh
ms.openlocfilehash: e0ad6803f2ddc982ea0c42c59fdf2486e1710433
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241130"
---
# <a name="software-defined-networks-hub-and-spoke"></a>軟體定義網路：中樞與輪輻

中樞與輪輻網路模型會將以 Azure 為基礎的雲端網路基礎結構組織為多個已連線的虛擬網路。 此模型可讓您更有效率地管理一般通訊或安全性需求，以及處理潛在的訂用帳戶限制。

在中樞與輪輻模型中，「中樞」是一個虛擬網路，可做為中心位置來管理外部連線能力，以及裝載多個工作負載所使用的服務。 「輪輻」是可裝載工作負載並透過[虛擬網路對等互連](/virtual-network/virtual-network-peering-overview)來連線到中央中樞的虛擬網路。

傳入或傳出工作負載輪輻網路的所有流量，都會透過中樞網路進行路由傳送，可透過集中管理的 IT 規則或流程，對其進行路由傳送、檢查，或以其他方式來管理。

此模型旨在處理下列問題：

- 節省成本和管理效率。 將可由多個工作負載共用的服務 (例如網路虛擬設備 (NVA) 和 DNS 伺服器) 集中在單一位置，讓 IT 能夠跨多個工作負載，將多餘的資源和管理投入量降至最低。
- 克服訂用帳戶限制。 大型雲端式工作負載需要使用的資源，可能比單一 Azure 訂用帳戶內所允許的資源還要多 (請參閱[訂用帳戶限制](/azure/azure-subscription-service-limits))。 將工作負載虛擬網路從不同的訂用帳戶對等互連到中央中樞，即可克服這些限制。
- 分開考量。 能夠在中央 IT 小組和工作負載小組之間部署個別工作負載。

下圖顯示一個範例中樞與輪輻架構，其中包括集中管理的混合式連線。

![中樞輪輻網路架構](../../../reference-architectures/hybrid-networking/images/hub-spoke.png)

中樞與輪輻架構通常會與混合式網路架構搭配使用，為您在多個工作負載之間共用的內部部署環境提供集中管理的連線。 在此案例中，往返工作負載與內部部署之間的所有流量，都會通過可管理並保護它的中樞。

## <a name="hub-and-spoke-assumptions"></a>中樞與輪輻的假設

實作中樞與輪輻虛擬網路架構的假設如下：

- 您的雲端部署將涵蓋裝載於個別工作環境 (例如開發、測試和生產) 中的工作負載，這些工作環境全都依賴一組常見的服務 (例如 DNS 或目錄服務)。
- 您的工作負載不需要彼此通訊，但會有常見的外部通訊和共用服務需求。
- 您的工作負載所需的資源，比單一 Azure 訂用帳戶內可用的資源還要多。
- 您需要為工作負載小組提供其本身資源的委派管理權限，同時保有對外部連線的集中安全性管理。

## <a name="global-hub-and-spoke"></a>全域中樞與輪輻

中樞與輪輻架構通常會使用部署至同一個 Azure 區域的虛擬網路來實作，以便將網路之間的延遲降至最低。 不過，觸角擴及全球的大型組織可能需要跨多個區域部署工作負載，以滿足可用性、災害復原或法規需求。 透過使用 Azure [全域虛擬網路對等互連](/azure/virtual-network/virtual-network-peering-overview)，中樞與輪輻模型可以跨區域擴充集中式管理和共用服務，以支援分散在世界各地的工作負載。

## <a name="learn-more"></a>深入了解

如需如何在 Azure 上實作中樞與輪輻網路的範例，請參閱 Azure 參考架構網站上的下列範例：

- [在 Azure 中實作中樞輪輻網路拓撲](../../../reference-architectures/hybrid-networking/hub-spoke.md)
- [在 Azure 中實作中樞輪輻網路拓撲與共用服務](../../../reference-architectures/hybrid-networking/shared-services.md)

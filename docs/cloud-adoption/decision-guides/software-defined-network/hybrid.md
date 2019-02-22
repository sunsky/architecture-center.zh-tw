---
title: CAF：軟體定義網路 - 混合式網路
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 討論混合式網路如何讓您的雲端虛擬網路連線到內部部署資源
author: rotycenh
ms.openlocfilehash: 02d181db0ae9baef3b453b8623d212b624f6b16a
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900732"
---
# <a name="software-defined-networks-hybrid-network"></a>軟體定義網路：混合式網路

混合式雲端網路架構可讓虛擬網路使用專用 WAN 連線 (例如 ExpressRoute 或其他直接連線網路的連線方法) 存取您的內部部署資源和服務，反之亦然。

![混合式網路](../../../reference-architectures/hybrid-networking/images/expressroute.png)

混合式虛擬網路是建置在雲端原生虛擬網路架構上，最初建立時是獨立的。 將連線新增至內部部署環境會授與內部部署網路的存取權，雖然必須明確允許虛擬網路中的其他所有輸入流量目標資源。 您可以使用虛擬防火牆裝置和路由規則來保護連線以限制存取，或者您可以準確指定可使用雲端原生路由功能存取兩個網路之間的哪些服務，或部署網路虛擬設備 (NVA) 以管理流量。

雖然混合式網路架構支援 VPN 連線，但是例如 ExpressRoute 的專用 WAN 連線因為有較高的效能和更高的安全性所以經常是偏好選項。

## <a name="hybrid-assumptions"></a>混合式假設

部署混合式虛擬網路假設以下事項：

- 您的 IT 安全性小組具備一致的內部部署和雲端型網路安全性原則，以確保可以信任雲端型虛擬網路，直接與內部部署系統通訊。
- 您的雲端型工作負載需要存取儲存體、應用程式和您的內部部署或第三方網路上裝載的服務，或您的使用者或您內部部署中的應用程式需要存取雲端裝載資源。
- 您需要遷移現有的應用程式和服務 (相依於內部部署資源)，但不想要耗費資源重新開發來移除這些相依性。
- 在您的內部部署網路和雲端提供者之間實作 VPN 或專用 WAN 連線，不會被公司原則、法規需求或技術功能問題阻止。
- 您的工作負載不需要多個訂用帳戶以略過訂用帳戶資源限制，或您的工作負載涉及多個訂用帳戶，但不需要集中管理多個訂用帳戶之間分散之資源使用的連線或共用資源。

您的雲端採用小組在查看實作混合式虛擬網路架構時，應該考量下列問題：

- 連線內部部署網路與雲端網路，增加您的安全性需求的複雜度。 這兩個網路都必須受到保護，免於混合式環境兩端的外部弱點和未經授權存取的侵襲。
- 調整混合式雲端環境內工作負載的數量和大小，可以對路由和流量管理增加大幅複雜性。
- 您必須開發相容的管理和存取控制原則，以維護整個組織一致的治理。

## <a name="learn-more"></a>深入了解

如需 Azure 平台中混合式網路的詳細資訊，請參閱下列各項。

- [混合式網路參考架構](../../../reference-architectures/hybrid-networking/expressroute.md)。 Azure 混合式虛擬網路使用 ExpressRoute 線路或 Azure VPN，連接您的虛擬網路與貴組織現有非 Azure 裝載 IT 資產。 本文討論在 Azure 中建立混合式網路的選項。

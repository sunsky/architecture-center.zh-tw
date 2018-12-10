---
title: 將內部部署網路連線到 Azure
titleSuffix: Azure Reference Architectures
description: 比較將內部部署網路連線到 Azure 的參考架構。
author: telmosampaio
ms.date: 07/02/2018
ms.openlocfilehash: de509b6d95805f4fc871f6dbd76a87d2c0bec6f1
ms.sourcegitcommit: 88a68c7e9b6b772172b7faa4b9fd9c061a9f7e9d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53119909"
---
# <a name="choose-a-solution-for-connecting-an-on-premises-network-to-azure"></a>選擇將內部部署網路連線到 Azure 的解決方案

本文會比較將內部部署網路連線到 Azure 虛擬網路 (VNet) 的選項。 對於每個選項都可以使用更詳細的參考架構。

## <a name="vpn-connection"></a>VPN 連線

[VPN 閘道](/azure/vpn-gateway/vpn-gateway-about-vpngateways)是一種虛擬網路閘道，可在 Azure 虛擬網路和內部部署位置之間傳送加密流量。 加密流量會通過公用網際網路。

此架構適合內部部署硬體和雲端之間的流量可能不大的混合式應用程式，或是您願意用稍微延長的延遲交換雲端彈性和處理能力的混合式應用程式。

**優點**

- 設定容易。

**挑戰**

- 需要有內部部署 VPN 裝置。
- 雖然 Microsoft 保證每個 VPN 閘道可達 99.9% 的可用性，但是此 [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) 只涵蓋 VPN 閘道，而不涵蓋閘道的網路連線。
- 透過 Azure VPN 閘道的 VPN 連線目前最多可支援 1.25 Gbps 的頻寬。 如果您預期會超過此輸送量，可能需要跨多個 VPN 連線分割 Azure 虛擬網路。

**參考架構**

- [使用 VPN 閘道的混合式網路](./vpn.md)

## <a name="azure-expressroute-connection"></a>Azure ExpressRoute 連線

[ExpressRoute](/azure/expressroute/) 連線會透過第三方連線提供者，使用私人的專用連線。 私人連線會將內部部署網路延伸到 Azure。 

此架構適合執行需要高度延展性之大型、任務關鍵性工作負載的混合式應用程式。 

**優點**

- 可用的頻寬更高；根據連線提供者而定，最多可達 10 Gbps。
- 支援動態調整頻寬，有助於在要求較低的期間降低成本。 不過，並非所有連線提供者都有這個選項。
- 根據連線提供者而定，或許可讓您的組織直接存取國家雲端。
- 整個連線的可用性 SLA 為 99.9%。

**挑戰**

- 設定可能相當複雜。 建立 ExpressRoute 連線需要使用第三方連線提供者。 此提供者負責佈建網路連線。
- 需要高頻寬的路由器內部部署。

**參考架構**

- [使用 ExpressRoute 的混合式網路](./expressroute.md)

## <a name="expressroute-with-vpn-failover"></a>含 VPN 容錯移轉的 ExpressRoute

此選項結合上述兩者，在正常情況下使用 ExpressRoute，但在 ExpressRoute 線路的連線中斷時，容錯移轉到 VPN 連線。

此架構適合需要較高頻寬的 ExpressRoute，而且也需要高可用性網路連線的混合式應用程式。 

**優點**

- 如果 ExpressRoute 線路失敗，雖然後援連線位於較低頻寬的網路，但仍然可達高可用性。

**挑戰**

- 設定複雜。 您需要同時設定 VPN 連線與 ExpressRoute 線路。
- 需要備援硬體 (VPN 設備)，以及您必須支付費用的備援 Azure VPN 閘道連線。

**參考架構**

- [使用 ExpressRoute 和 VPN 容錯移轉的混合式網路](./expressroute-vpn-failover.md)

## <a name="hub-spoke-network-topology"></a>中樞輪輻網路拓撲

中樞輪輻網路拓撲是一種在共用服務 (例如身分識別和安全性) 時隔離工作負載的方式。 「中樞」是 Azure 中的虛擬網路 (VNet)，可當作內部部署網路的連線中心點。 「輪輻」是與中樞對等的 VNet。 共用的服務會部署在中樞中，而個別的工作負載會部署為輪輻。

**參考架構**

- [中樞輪輻拓撲](./hub-spoke.md)
- [具有共用服務的中樞輪輻](./shared-services.md)

---
title: 選擇將內部部署網路連線到 Azure 的解決方案
description: 比較將內部部署網路連線到 Azure 的參考架構。
author: telmosampaio
ms.date: 04/06/2017
ms.openlocfilehash: 274b9df1817632a7f3eaafa8bf02e965fdc3feea
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
ms.locfileid: "24541044"
---
# <a name="choose-a-solution-for-connecting-an-on-premises-network-to-azure"></a>選擇將內部部署網路連線到 Azure 的解決方案

本文會比較將內部部署網路連線到 Azure 虛擬網路 (VNet) 的選項。 我們會為每個選項提供一個參考架構和可部署的解決方案。

## <a name="vpn-connection"></a>VPN 連線

使用虛擬私人網路 (VPN)，透過 IPSec VPN 通道將您的內部部署網路與 Azure VNet 連線。

此架構適合內部部署硬體和雲端之間的流量可能不大的混合式應用程式，或是您願意用稍微延長的延遲交換雲端彈性和處理能力的混合式應用程式。

**優點**

- 設定容易。

**挑戰**

- 需要有內部部署 VPN 裝置。
- 雖然 Microsoft 保證每個 VPN 閘道可達 99.9% 的可用性，但是此 SLA 只涵蓋 VPN 閘道，而不涵蓋閘道的網路連線。
- 透過 Azure VPN 閘道的 VPN 連線目前最多可支援 200 Mbps 的頻寬。 如果您預期會超過此輸送量，可能需要跨多個 VPN 連線分割 Azure 虛擬網路。

**[閱讀更多...][vpn]**

## <a name="azure-expressroute-connection"></a>Azure ExpressRoute 連線

ExpressRoute 連線會透過第三方連線提供者，使用私人的專用連線。 私人連線會將內部部署網路延伸到 Azure。 

此架構適合執行需要高度延展性之大型、任務關鍵性工作負載的混合式應用程式。 

**優點**

- 可用的頻寬更高；根據連線提供者而定，最多可達 10 Gbps。
- 支援動態調整頻寬，有助於在要求較低的期間降低成本。 不過，並非所有連線提供者都有這個選項。
- 根據連線提供者而定，或許可讓您的組織直接存取國家雲端。
- 整個連線的可用性 SLA 為 99.9%。

**挑戰**

- 設定可能相當複雜。 建立 ExpressRoute 連線需要使用第三方連線提供者。 此提供者負責佈建網路連線。
- 需要高頻寬的路由器內部部署。

**[閱讀更多...][expressroute]**

## <a name="expressroute-with-vpn-failover"></a>含 VPN 容錯移轉的 ExpressRoute

此選項結合上述兩者，在正常情況下使用 ExpressRoute，但在 ExpressRoute 線路的連線中斷時，容錯移轉到 VPN 連線。

此架構適合需要較高頻寬的 ExpressRoute，而且也需要高可用性網路連線的混合式應用程式。 

**優點**

- 如果 ExpressRoute 線路失敗，雖然後援連線位於較低頻寬的網路，但仍然可達高可用性。

**挑戰**

- 設定複雜。 您需要同時設定 VPN 連線與 ExpressRoute 線路。
- 需要備援硬體 (VPN 設備)，以及您必須支付費用的備援 Azure VPN 閘道連線。

**[閱讀更多...][expressroute-vpn-failover]**

<!-- links -->
[expressroute]: ./expressroute.md
[expressroute-vpn-failover]: ./expressroute-vpn-failover.md
[vpn]: ./vpn.md
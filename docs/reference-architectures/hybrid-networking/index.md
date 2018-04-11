---
title: 將內部部署網路連線到 Azure
description: 在內部部署網路與 Azure 之間的安全、強固網路連線之建議架構。
layout: LandingPage
ms.openlocfilehash: 372efb8ecf69245a5895c51e3da156a348bd665e
ms.sourcegitcommit: 2123c25b1a0b5501ff1887f98030787191cf6994
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
<!-- This file is generated! -->
<!-- See the templates in ./build/reference-architectures  -->
<!-- See data in index.json -->

# <a name="connect-an-on-premises-network-to-azure"></a>將內部部署網路連線到 Azure

這些參考架構顯示經過證實做法，可建立內部部署網路與 Azure 之間的強固網路連線。 [應選取哪個？](./considerations.md)

<section class="series">
    <ul class="panelContent">
    <!-- VPN -->
<li style="display: flex; flex-direction: column;">
    <a href="./vpn.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./images/vpn.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>VPN</h3>
                        <p>使用網站對網站虛擬私人網路 (VPN) 將內部部署網路擴充至 Azure。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
    <!-- ExpressRoute -->
<li style="display: flex; flex-direction: column;">
    <a href="./expressroute.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./images/expressroute.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>ExpressRoute</h3>
                        <p>使用 Azure ExpressRoute 將內部部署網路擴充至 Azure。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
    <!-- ExpressRoute with VPN failover -->
<li style="display: flex; flex-direction: column;">
    <a href="./expressroute-vpn-failover.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./images/expressroute-vpn-failover.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>含 VPN 容錯移轉的 ExpressRoute</h3>
                        <p>使用 Azure ExpressRoute 搭配 VPN 作為容錯移轉連線，將內部部署網路擴充至 Azure。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
    <!-- Hub-spoke topology -->
<li style="display: flex; flex-direction: column;">
    <a href="./hub-spoke.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./images/hub-spoke.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>中樞輪輻拓撲</h3>
                        <p>中樞是您內部部署網路連線的集中點。 輪輻是與中樞對等的 VNet，可用於隔離工作負載。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
    <!-- Hub-spoke topology with shared services -->
<li style="display: flex; flex-direction: column;">
    <a href="./shared-services.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./images/shared-services.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>具有共用服務的中樞輪輻拓撲</h3>
                        <p>部署中樞輪輻拓撲，其中包含共用服務，包括 Active Directory 服務和網路虛擬設備 (NVA)。 共用服務可供所有輪輻取用。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
    </ul>
</section>

<ul class="panelContent cardsI">
</ul>
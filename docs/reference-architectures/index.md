---
title: Azure 參考架構
description: Azure 上一般工作負載的參考架構、藍圖和精準實作指導方針。
layout: LandingPage
ms.topic: landing-page
ms.openlocfilehash: 6c9be20e2b831f2e6c1ffd33aa89a56375a0511c
ms.sourcegitcommit: bb348bd3a8a4e27ef61e8eee74b54b07b65dbf98
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2018
---
<!-- This file is generated! -->
<!-- See the templates in ./build/reference-architectures  -->
<!-- See data in index.json -->

# <a name="azure-reference-architectures"></a>Azure 參考架構

我們參考架構是依情節排列，將相關架構群組在一起。 每個架構都包含建議的做法，以及延展性、可用性、管理性和安全性的考量。 大部分還包含可部署的解決方案。

<section class="series">
    <ul class="panelContent">

<!-- N-tier -->
<li style="display: flex; flex-direction: column;">
    <a href="./n-tier/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./n-tier/images/n-tier-sql-server.svg" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>多層式架構 (N-tier) 的應用程式</h3>
                        <p>部署適用於 Windows 或 Linux 的多層式架構 (N-tier) 應用程式至 Azure。</p>
                        <p>顯示適用於 SQL Server 和 Apache Cassandra 的組態。 要獲得高可用性，請在兩個區域中部署主動-被動組態。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>

<!-- Hybrid network -->
<li style="display: flex; flex-direction: column;">
    <a href="./hybrid-networking/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./hybrid-networking/images/vpn.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>混合式網路</h3>
                        <p>此系列會顯示在內部部署網路與 Azure 之間建立網路連線的選項。</p>
                        <p>設定包括適用於私人和專用連線的站對站 VPN 或Azure ExpressRoute。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
    <!-- Network DMZ -->
<li style="display: flex; flex-direction: column;">
    <a href="./dmz/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./dmz/images/secure-vnet-dmz.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>網路 DMZ</h3>
                        <p>此系列會顯示如何建立網路 DMZ 來保護 Azure 虛擬網路與內部部署網路或網際網路之間的界限。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
    <!-- Identity management -->
<li style="display: flex; flex-direction: column;">
    <a href="./identity/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./identity/images/adds-extend-domain.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>身分識別管理</h3>
                        <p>此系列會顯示整合內部部署 Active Directory (AD) 環境與 Azure 網路的選項。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
    <!-- App Service web application -->
<li style="display: flex; flex-direction: column;">
    <a href="./app-service-web-app/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./app-service-web-app/images/scalable-web-app.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>App Service Web 應用程式</h3>
                        <p>此系列會顯示使用 Azure App Service 的 web 應用程式之最佳做法。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
    </ul>
</section>

<ul class="panelContent cardsI">
    <!-- Jenkins build server -->
<li style="display: flex; flex-direction: column;">
    <a href="./jenkins/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./jenkins/images/logo.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Jenkins 組建伺服器</h3>
                        <p>在 Azure 上部署和操作可調整的企業級 Jenkins 伺服器。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
    <!-- SharePoint Server 2016 farm -->
<li style="display: flex; flex-direction: column;">
    <a href="./sharepoint/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./sharepoint/images/sharepoint.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>SharePoint Server 2016 伺服器陣列</h3>
                        <p>使用 SQL Server AlwaysOn 可用性群組，來部署及執行高可用性 SharePoint Server 2016 伺服器陣列。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
    <!-- SAP NetWeaver and SAP HANA -->
<li style="display: flex; flex-direction: column;">
    <a href="./sap/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./sap/images/sap.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>執行 Azure 上的 SAP</h3>
                        <p>在 Azure 的高可用性環境中部署及執行 SAP。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>
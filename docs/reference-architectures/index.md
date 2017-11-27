---
title: "Azure 參考架構"
description: "Azure 上一般工作負載的參考架構、藍圖和精準實作指導方針。"
layout: LandingPage
ms.openlocfilehash: eaff531c28faeb0aac774acf70d4334fb4e1f319
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="azure-reference-architectures"></a>Azure 參考架構

我們參考架構是依情節排列，將相關架構群組在一起。 每個架構都包含建議的做法，以及延展性、可用性、管理性和安全性的考量。 大部分還包含可部署的解決方案。

<section class="series">
    <ul class="panelContent">
    <!--Windows VM -->
    <li>
        <a href="./virtual-machines-windows/index.md">
            <div class="cardSize">
                <div class="cardPadding">
                    <div class="card">
                        <div class="cardImageOuter">
                            <div class="cardImage">
                                <img src="./virtual-machines-windows/images/n-tier.svg" height="140px" />
                            </div>
                        </div>
                        <div class="cardText">
                            <h3>Windows VM 工作負載</h3>
                            <p>此系列是以執行單一 Windows VM 的最佳做法作為開頭，接著是多個負載平衡的 VM，最後是多區域的多層式架構應用程式。</p>
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <!-- Linux VM -->
    <li>
        <a href="./virtual-machines-linux/index.md">
            <div class="cardSize">
                <div class="cardPadding">
                    <div class="card">
                        <div class="cardImageOuter">
                            <div class="cardImage">
                                <img src="./virtual-machines-linux/images/n-tier.svg" height="140px" />
                            </div>
                        </div>
                        <div class="cardText">
                            <h3>Linux VM 工作負載</h3>
                            <p>此系列是以執行單一 Linux VM 的最佳做法作為開頭，接著是多個負載平衡的 VM，最後是多區域的多層式架構應用程式。</p>
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <!-- Hybrid network -->
    <li>
        <a href="./hybrid-networking/index.md">
            <div class="cardSize">
                <div class="cardPadding">
                    <div class="card">
                        <div class="cardImageOuter">
                            <div class="cardImage">
                                <img src="./hybrid-networking/images/vpn.svg" height="140px" />
                            </div>
                        </div>
                        <div class="cardText">
                            <h3>混合式網路</h3>
                            <p>此系列會顯示在內部部署網路與 Azure 之間建立網路連線的選項。</p>
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <!-- DMZ -->
    <li>
        <a href="./dmz/index.md">
            <div class="cardSize">
                <div class="cardPadding">
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
    <!-- Identity -->
    <li>
        <a href="./identity/index.md">
            <div class="cardSize">
                <div class="cardPadding">
                    <div class="card">
                        <div class="cardImageOuter">
                            <div class="cardImage">
                                <img src="./identity/images/adds-extend-domain.svg" height="140px" >
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
    <!-- Managed web app -->
    <li>
        <a href="./app-service-web-app/index.md">
            <div class="cardSize">
                <div class="cardPadding">
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

<ul class="panelContent cardsI">
<li>
    <a href="./sharepoint/index.md">
    <div class="cardSize">
        <div class="cardPadding">
            <div class="card">
                <div class="cardImageOuter">
                    <div class="cardImage">
                        <img src="./sharepoint/images/sharepoint.svg" alt="SharePoint Server 2016" height="100%" />
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

<li>
    <a href="./sap/index.md">
    <div class="cardSize">
        <div class="cardPadding">
            <div class="card">
                <div class="cardImageOuter">
                    <div class="cardImage">
                        <img src="./sap/images/sap.svg" alt="SAP NetWeaver and SAP HANA" width="100%" />
                    </div>
                </div>
                <div class="cardText">
                    <h3>SAP NetWeaver 和 SAP HANA</h3>
                    <p>在 Azure 的高可用性環境中部署及執行 SAP NetWeaver SAP HANA。</p>
                </div>
            </div>
        </div>
    </div>
    </a>
</li>
</ul>


</section>


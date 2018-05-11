---
title: 多層式架構 (N-Tier) 參考架構
description: 說明某些部署在 Azure 中裝載企業規模應用程式之 VM 的常見架構。
layout: LandingPage
ms.openlocfilehash: 288acc36e7c310e70240caa3ed9f2095bbb8bc58
ms.sourcegitcommit: d08f6ee27e1e8a623aeee32d298e616bc9bb87ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
<!-- This file is generated! -->
<!-- See the templates in ./build/reference-architectures  -->
<!-- See data in index.json -->

# <a name="n-tier-application-reference-architectures"></a>多層式架構 (N-Tier) 參考架構

<section class="series">
    <ul class="panelContent">

<!-- N-tier Windows -->
<li style="display: flex; flex-direction: column;">
    <a href="./n-tier-sql-server.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./images/n-tier-sql-server.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>具有 SQL Server 的多層式架構 (N-tier) 應用程式</h3>
                        <p>在 Windows 上使用 SQL Server 部署為多層式架構 (N-Tier) 應用程式設定的 VM 和虛擬網路。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>

<!-- Multi-region Windows -->
<li style="display: flex; flex-direction: column;">
    <a href="./multi-region-sql-server.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./images/multi-region-application.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>具有 SQL Server 的多區域多層式架構 (N-tier) 應用程式</h3>
                        <p>使用 SQL Server Alwayson 可用性群組，將多層式架構 (N-Tier) 部署至兩個區域以獲得高可用性。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>

<!-- N-tier Linux -->
<li style="display: flex; flex-direction: column;">
    <a href="./n-tier-cassandra.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./images/n-tier-cassandra.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>具有 Cassandra 的多層式架構 (N-tier) 應用程式</h3>
                        <p>使用 Apache Cassandra 部署為多層式架構 (N-Tier) 應用程式設定的 Linux VM 和虛擬網路。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>

<ul class="panelContent cardsI">
<li style="display: flex; flex-direction: column;">
    <a href="./windows-vm.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./images/Windows.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Windows VM</h3>
                        <p>在 Azure 中執行 Windows VM 的基準建議。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>

<li style="display: flex; flex-direction: column;">
    <a href="./linux-vm.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./images/LinuxPenguin.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Linux VM</h3>
                        <p>在 Azure 中執行 Linux VM 的基準建議。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>

</ul>
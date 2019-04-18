---
title: CAF：雲端治理的五個專業領域
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 在 CAF 中治理內容的概觀
author: BrianBlanchard
layout: LandingPage
ms.topic: landing-page
ms.openlocfilehash: ddd7f5e4b7e79ebe2ddf87be7421b6c4691aced1
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59639864"
---
# <a name="the-five-disciplines-of-cloud-governance"></a>雲端治理的五個專業領域

<!-- markdownlint-disable MD033 -->

<ul class="panelContent cardsI">
<li style="display: flex; flex-direction: column;">
    <div class="cardSize">
        <div class="cardPadding" style="padding-bottom:10px;">
            <div class="card" style="padding-bottom:10px;">
                <div class="cardText" style="padding-left:0px;">
對業務程序或技術平台進行的任何變更都會引進風險。 雲端治理小組 (其成員又稱雲端監管人) 負責降低這些風險，且對採用或創新工作造成最少的中斷。<br/><br/>咖控管模型引導這些決策 （不論所選擇的雲端平台） 上開發的公司原則並<a href="#disciplines-of-cloud-governance">專業領域的雲端治理</a>。 <a href="./journeys/overview.md">可採取動作的旅程</a>示範如何使用 Azure 服務此模型。 本文是作為 CAF 治理模型五個專業領域的登陸頁面。
                </div>
            </div>
        </div>
    </div>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="../_images/operational-transformation-govern-highres.png" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize">
            <div class="cardPadding" style="padding-bottom:10px;">
                <div class="card" style="padding-bottom:10px;">
                    <div class="cardText" style="padding-left:0px;">
<img src="../_images/operational-transformation-govern-highres.png" alt="Diagram of the CAF governance model: Corporate policy and governance disciplines">
<br>
<i>圖 1.公司原則和雲端管理五個專業領域的圖表</i>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

<!-- markdownlint-enable MD033 -->

## <a name="disciplines-of-cloud-governance"></a>雲端治理的專業領域

在每個雲端提供者中，有可做為指引，協助通知原則並對齊工具鏈的通用控管專業領域。 這些專業領域會引導有關自動化之適當層級、跨雲端服務提供者之公司原則執行的決策。

<!-- markdownlint-disable MD033 -->

<ul class="panelContent cardsA">
<li style="display: flex; flex-direction: column;">
    <a href="./cost-management/overview.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/governance/cost-management.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>成本管理</h3>
                        <p>成本是雲端使用者的主要考量。 開發適用於所有雲端平台的成本控制原則。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./security-baseline/overview.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/governance/security-baseline.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>安全性基準</h3>
                        <p>安全性是複雜又私人的主題，對每家公司都是唯一的。 建立安全性需求之後，雲端治理原則和強制執行會將那些需求套用至整個網路、資料和資產設定。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./identity-baseline/overview.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/governance/identity-baseline.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>身分識別基準</h3>
                        <p>身分識別需求套用中的不一致可能會增加遭入侵的風險。 身分識別基準專業領域著重於如何確保身分識別一致地套用至整個雲端採用工作。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./resource-consistency/overview.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/governance/resource-consistency.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>資源一致性</h3>
                        <p>雲端作業有賴於資源設定中的一致性。 透過治理工具，就能一致地設定資源，以降低上架、漂移、發現性和復原相關的風險。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./deployment-acceleration/overview.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/governance/deployment-acceleration.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>部署加速</h3>
                        <p>部署和設定方法中的集中化、標準化和一致性可改善治理實務。 可以透過雲端式治理工具取得之後，它們會建立能加速部署活動的雲端因素。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

<!-- markdownlint-enable MD033 -->

---
title: CAF：身分識別基準專業領域概觀
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 說明與雲端治理相關的身分識別基準
author: BrianBlanchard
layout: LandingPage
ms.topic: landing-page
ms.openlocfilehash: 14569b8db68434ee30fa7bff7fba2da5fa537049
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58242029"
---
# <a name="caf-identity-baseline-discipline-overview"></a>CAF：身分識別基準專業領域概觀

身分識別基準是 [CAF 治理模型](../overview.md)中的[五個雲端治理專業領域](../governance-disciplines.md)之一。 身分識別逐漸被視為雲端的主要安全邊界，從傳統著重於網路安全性轉變而來。 身分識別服務提供核心機制，以支援 IT 環境中的存取控制和組織，而身分識別基準專業領域可透過雲端採用工作一致地套用驗證和授權需求，以補充[安全性基準專業領域](../security-baseline/overview.md)。

> [!NOTE]
> 身分識別基準治理不會取代可讓貴組織管理及保護身分識別服務的現有 IT 小組、流程和程序。 此專業領域的主要目的是要找出潛在的身分識別相關業務風險，並將風險降低指引提供給負責實作、維護及操作身分識別管理基礎結構的 IT 人員。 當您開發治理原則和流程時，請務必在您的規劃與檢閱流程中包含相關的 IT 小組。

這一節的 CAF 指引將概述開發身分識別基準專業領域作為雲端治理策略一部分的方法。 本指引的主要對象是貴組織的雲端架構設計師和雲端治理小組的其他成員。 不過，從此專業領域衍生的決策、原則和流程，應涉及與負責實作和管理貴組織身分識別管理解決方案的 IT 小組相關成員進行互動與討論。

如果貴組織沒有身分識別基準和安全性方面的內部專業知識，請考慮聘請外部顧問成為此專業領域的一部分。 此外，考慮聘請 [Microsoft Consulting Services](https://www.microsoft.com/enterprise/services)、[Microsoft FastTrack](https://azure.microsoft.com/programs/azure-fasttrack) 雲端採用服務，或其他外部雲端採用專家來討論此專業領域的相關適宜。

## <a name="policy-statements"></a>Policy statements

可操作的原則聲明和所產生的架構需求，均可作為身分識別基準專業領域的基礎。 若要查看原則聲明範例，請參閱關於[身分識別基準原則聲明](./policy-statements.md)的文章。 這些範例可作為貴組織治理原則的起點。

> [!CAUTION]
> 範例原則來自於常見的客戶體驗。 若要進一步使這些原則與特定雲端治理需求保持一致，請執行下列步驟，以建立符合您獨特商務需求的原則聲明。

## <a name="developing-identity-baseline-governance-policy-statements"></a>開發身分識別基準治理原則聲明

下列六個步驟提供在開發身分識別基準治理時所要考量的範例和可能選項。 使用每個步驟作為起點，以便在雲端治理小組中討論以及與組織內受影響的業務和 IT 小組討論，進而建立降低身分識別相關風險所需的原則和流程。

<!-- markdownlint-disable MD033 -->

<ul class="panelContent cardsE">
<li style="display: flex; flex-direction: column;">
    <a href="./template.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/governance/process-template.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>身分識別基準範本</h3>
                        <p class="x-hidden-focus">下載此範本以便記載身分識別基準專業領域</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li><li style="display: flex; flex-direction: column;">
    <a href="./business-risks.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/governance/process-risks.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>業務風險</h3>
                        <p class="x-hidden-focus">了解通常與身分識別基準專業領域相關聯的動機與風險。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./metrics-tolerance.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/governance/process-metrics.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>指標和計量</h3>
                        <p class="x-hidden-focus">了解其是否為投資身分識別基準專業領域之正確時機的指標。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./compliance-processes.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/governance/process-enforce.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>原則遵循流程</h3>
                        <p class="x-hidden-focus">用以在身分識別基準專業領域中支援原則合規性的建議流程。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./discipline-improvement.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/governance/process-maturity.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>成熟度</h3>
                        <p class="x-hidden-focus">使雲端管理成熟度和雲端採用階段保持一致。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./toolchain.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/governance/process-toolchain.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>工具鏈</h3>
                        <p class="x-hidden-focus">可實作來支援身分識別基準專業領域的 Azure 服務。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

<!-- markdownlint-enable MD033 -->

## <a name="next-steps"></a>後續步驟

開始評估特定環境中的[商務風險](./business-risks.md)。

> [!div class="nextstepaction"]
> [了解業務風險](./business-risks.md)

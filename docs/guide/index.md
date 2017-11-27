---
layout: LandingPage
ms.openlocfilehash: 00abbfdeac89a9006517195bd4bbc514d587fe74
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="azure-application-architecture-guide"></a>Azure 應用程式架構指南

本指南會告訴您在 Azure 上設定可擴充、可復原和高可用性應用程式的結構化方法。 者是以我們與客戶合作後了解到的實證做法作為基礎。

<img src="./images/guide-steps.svg" style="max-width:800px;"/>

## <a name="introduction"></a>簡介

雲端正在改變應用程式的設計方式。 不同於龐大且單一的體系，應用程式會分解成較小型的非集中式服務。 這些服務會透過 API 或使用非同步傳訊進行通訊。 應用程式可水平調整，以依據需求要求新增執行個體。 

這些趨勢將帶來新的挑戰。 應用程式狀態是分散的。 作業會平行且非同步地完成。 發生失敗時，整體系統必須具有復原功能。 部署必須自動化且要可預測。 監視和遙測是取得系統深入解析的關鍵。 Azure 應用程式架構指南旨在協助您瀏覽這些變更。 

<table>
<thead>
    <tr><th>傳統內部部署</th><th>現代化雲端</th></tr>
</thead>
<tbody>
<tr><td>龐大且單一的體系、集中式<br/>
為可預測的延展性而設計<br/>
關聯式資料庫<br/>
強式一致性<br/>
序列和同步處理<br/>
為避免失敗而設計 (MTBF)<br/>
偶爾有大型更新<br/>
手動管理<br/>
雪花式伺服器</td>
<td>
分解、不集中<br/>
為彈性延展而設計<br/>
Polyglot 持續性 (混合儲存體技術)<br/>
最終一致性<br/>
並行和非同步處理<br/>
為失敗而設計 (MTTR)<br/>
頻繁的小型更新<br/>
自動化自我管理<br/>
不可變的基礎結構<br/>
</td>
</tbody>
</table>

本指南適用於應用程式架構設計人員、開發人員和營運團隊。 這不是個別 Azure 服務的使用說明指南。 閱讀本指南之後，您將了解在 Azure 雲端平台上進行建置時，該套用何種架構模式和最佳作法。

## <a name="how-this-guide-is-structured"></a>指南結構

Azure 應用程式架構指南會整理成一系列的步驟，從架構與設計到實作。 每個步驟都有支援的指引，可協助您設計您的應用程式架構。

**[架構樣式][arch-styles]**。 第一個決策點是最基本的。 您正在建置何種架構？ 架構可能是微服務架構、更傳統的多層式架構 (N-tier) 應用程式或巨量資料解決方案。 我們已識別七個不同的架構樣式。 每個都有優點和挑戰。

> &#10148; [Azure 參考架構][ref-archs]會說明 Azure 中的建議部署，以及延展性、可用性、管理性和安全性的考量。 大部分也會包含可部署的 Resource Manager 範本。

**[技術選擇][technology-choices]**。 應該盡早決定兩種技術的選擇，因為它們會影響整個架構。 這兩個選擇就是計算和儲存體技術。 「計算」一詞指的是運算資源的裝載模型，您的應用程式會在運算資源上執行。 儲存體包含資料庫，但也包含其他儲存體，這些儲存體會用於訊息佇列、快取、IoT 資料、非結構化記錄資料，以及其他應用程式可能會保存到儲存體的項目。 

> &#10148; [計算選項][compute-options]和[儲存體選項][storage-options]會提供選取計算和儲存體服務的詳細比較準則。

**[設計原則][design-principles]**。 在整個設計程序中，請記住這些十個高階的設計原則。 

> &#10148; [最佳做法][best-practices]一文會提供自動調整、快取、資料分割和 API 設計等層面的專屬指引。   

**[核心][pillars]**。 成功的雲端應用程式會將焦點放在軟體品質的五個核心：延展性、可用性、復原力、管理性和安全性。 

> &#10148; 使用我們的[設計檢閱清單][checklists]，以根據這些品質核心來檢閱您的設計。 

**[雲端設計模式][patterns]**。 這些設計模式有助於在 Azure 中建置可靠、可擴充且安全的應用程式。 每個模式都會說明一個問題、可處理此問題的模式，以及以 Azure 為基礎的範例。

> &#10148; 檢閱完整的[雲端設計模式目錄](../patterns/index.md)。


[arch-styles]: ./architecture-styles/index.md
[best-practices]: ../best-practices/index.md
[checklists]: ../checklist/index.md
[compute-options]: ./technology-choices/compute-comparison.md
[design-principles]: ./design-principles/index.md
[patterns]: ../patterns/index.md?toc=/azure/architecture/guide/toc.json
[pillars]: ./pillars.md
[ref-archs]: ../reference-architectures/index.md
[storage-options]: ./technology-choices/data-store-comparison.md
[technology-choices]: ./technology-choices/index.md


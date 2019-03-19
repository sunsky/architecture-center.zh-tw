---
layout: LandingPage
ms.topic: landing-page
ms.service: architecture-center
ms.subservice: reference-architecture
ms.date: 08/30/2018
ms.openlocfilehash: 651f59344e7785a8a23e7b56dd67b4c4a3044741
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54484375"
---
# <a name="azure-application-architecture-guide"></a>Azure 應用程式架構指南

指南會告訴您在 Azure 上設定可擴充、可復原和高可用性應用程式的結構化方法。這是以我們與客戶合作後了解到的實證做法作為基礎

## <a name="introduction"></a>簡介

雲端正在改變應用程式的設計方式。 不同於龐大且單一的體系，應用程式會分解成較小型的非集中式服務。 這些服務會透過 API 或使用非同步傳訊進行通訊。 應用程式可水平調整，以依據需求要求新增執行個體。

這些趨勢將帶來新的挑戰。 應用程式狀態是分散的。 作業會平行且非同步地完成。 發生失敗時，整體系統必須具有復原功能。 部署必須自動化且要可預測。 監視和遙測是取得系統深入解析的關鍵。 Azure 應用程式架構指南旨在協助您瀏覽這些變更。

<!-- markdownlint-disable MD033 -->

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

<!-- markdownlint-enable MD033 -->

本指南適用於應用程式架構設計人員、開發人員和營運團隊。 這不是個別 Azure 服務的使用說明指南。 閱讀本指南之後，您將了解在 Azure 雲端平台上進行建置時，該套用何種架構模式和最佳作法。 您也可以下載[本指南的電子書版本][ebook]。

## <a name="how-this-guide-is-structured"></a>指南結構

Azure 應用程式架構指南會整理成一系列的步驟，從架構與設計到實作。 每個步驟都有支援的指引，可協助您設計您的應用程式架構。

### <a name="architecture-styles"></a>架構樣式

第一個決策點是最基本的。 您正在建置何種架構？ 架構可能是微服務架構、更傳統的多層式架構 (N-tier) 應用程式或巨量資料解決方案。 我們已識別數個不同的架構樣式。 每個都有優點和挑戰。

深入了解：

- [架構樣式](./architecture-styles/index.md)

### <a name="technology-choices"></a>技術選擇

應該盡早決定兩種技術的選擇，因為它們會影響整個架構。 這兩個選擇就是計算服務和資料存放區。 *計算*指的是運算資源的裝載模型，您的應用程式會在運算資源上執行。 *資料存放區*包含資料庫，但也包含其他儲存體，這些儲存體會用於訊息佇列、快取、記錄，以及其他應用程式可能會保存到儲存體的項目。

深入了解：

- [選擇計算服務](./technology-choices/compute-overview.md)
- [選擇資料存放區](./technology-choices/data-store-overview.md)

### <a name="design-principles"></a>設計原則

我們已識別十個高階的設計原則，讓您的應用程式更有擴充空間、可容易復原且更方便管理。 這些設計原則可套用至任何架構樣式。 在整個設計程序中，請記住這些十個高階的設計原則。 接著請考慮該架構特定方面的一組最佳做法，例如自動調整大小、快取、資料分割、API 設計等。

深入了解：

- [設計原則](./design-principles/index.md)

### <a name="quality-pillars"></a>品質要素

成功的雲端應用程式將著重於軟體品質的五大支柱：延展性、可用性、復原、管理和安全性。 使用我們的設計檢閱清單，以根據這些品質要素來檢閱您的架構。

- [品質要素](./pillars.md)

[ebook]: https://azure.microsoft.com/campaigns/cloud-application-architecture-guide/

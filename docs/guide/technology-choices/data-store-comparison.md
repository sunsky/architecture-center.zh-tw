---
title: 選擇資料存放區的準則
titleSuffix: Azure Application Architecture Guide
description: Azure 計算選項的概觀。
author: MikeWasson
ms.date: 06/01/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 3fd3badd66edbe561bea88576bb80d9fc3e0bb79
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068917"
---
# <a name="criteria-for-choosing-a-data-store"></a>選擇資料存放區的準則

Azure 支援許多類型的資料儲存體解決方案，每種解決方案各有不同的特點與功能。 本文說明評估資料存放區時應使用的比較準則。 目標是協助您判斷哪些資料儲存體類型可符合您的解決方案需求。

## <a name="general-considerations"></a>一般考量

為了開始進行比較，請盡量收集下列有關資料需求的資訊。 此資訊將協助您判斷哪些資料儲存體類型符合您的需求。

### <a name="functional-requirements"></a>功能需求

- **資料格式**。 您想要儲存何種資料類型？ 常見的類型包括交易資料、JSON 物件、遙測、搜尋索引或一般檔案。

- **資料大小**。 您要儲存的實體有多大？ 這些實體必須維持單一文件的形式嗎？或是可分割為多個文件、資料表或集合等等？

- **規模和結構**。 您需要的整體儲存體容量為何？ 您預期會分割資料嗎？

- **資料關聯性**。 您的資料需要支援一對多或多對多關聯性嗎？ 關聯性本身是資料很重要的一部分嗎？ 您需要加入或結合來自相同資料集或外部資料集的資料嗎？

- **一致性模型**。 在可進行進一步的變更之前，使某個節點中的更新出現在其他節點中有多重要？ 您可以接受最終一致性嗎？ 您是否需要交易的 ACID 保證？

- **結構描述彈性**。 您要將何種結構描述套用到您的資料？ 您將使用採用靜態結構描述 (schema-on-write) 方法的固定結構描述，或是採用動態結構描述 (schema-on-read) 方法？

- **並行存取**。 更新和同步處理資料時，要使用何種並行存取機制？ 應用程式是否將執行許多可能發生衝突的更新？ 如果是，您可能需要記錄鎖定和封閉式並行存取控制。 或者，您可以支援開放式並行存取控制嗎？ 如果是這樣，以時間戳記為基礎的簡單並行存取控制是否足夠？或者您需要多版本並行存取控制的新增功能？

- **資料移動**。 您的解決方案需要執行 ETL 工作以將資料移至其他存放區或資料倉儲嗎？

- **資料生命週期**。 資料是寫入一次但讀取多次嗎？ 是否可移到非經常性或冷儲存體？

- **其他支援的功能**。 您是否需要任何其他特定功能，例如結構描述驗證、彙總、索引、全文檢索搜尋、MapReduce 或其他查詢功能？

### <a name="non-functional-requirements"></a>非功能性需求

- **效能和延展性**。 您的資料效能需求為何？ 您有資料擷取速率和資料處理速率的特殊需求嗎？ 針對擷取後的資料查詢和彙總，可接受的回應時間為何？ 資料存放區需要相應增加至多大？ 您的工作負載偏向大量讀取或大量寫入？

- **可靠性**。 您需要支援的整體 SLA 為何？ 您需要提供給資料取用者的容錯層級為何？ 您需要何種備份和還原功能？

- **複寫**。 您的資料是否需要分散至多個複本或區域？ 您需要何種資料複寫功能？

- **限制**。 特定資料存放區的限制是否支援您對規模、連線數目和輸送量的需求？

### <a name="management-and-cost"></a>管理和成本

- **受控服務**。 除非您需要只能在 IaaS 主控的資料存放區中找到的特定功能，否則請儘可能使用受控資料服務。

- **區域可用性**。 針對受控服務，所有 Azure 區域都提供服務嗎嗎？ 您的解決方案需要裝載在特定的 Azure 區域嗎？

- **可攜性**。 您的資料需要移轉到內部、 外部資料中心或其他雲端主控環境嗎？

- **授權**。 您有專屬與 OSS 授權類型的喜好設定嗎？ 您可使用的授權類型是否有任何其他外部限制？

- **整體成本**。 在您的解決方案中使用服務的整體成本為何？ 若要支援您的執行時間和輸送量需求，將需執行多少個執行個體？ 請考慮此計算中的作業成本。 建議使用受控服務的其中一個原因，便是可降低作業成本。

- **符合成本效益**。 您可以分割資料，以更符合成本效益的方式儲存它嗎？ 例如，您可以將大型物件從昂貴的關聯式資料庫移出到物件存放區嗎？

### <a name="security"></a>安全性

- **安全性**。 您需要何種加密類型？ 您需要待用加密嗎？ 您要使用何種驗證機制來連線到您的資料？

- **稽核**。 您需要產生何種稽核記錄？

- **網路需求**。 您需要限制或管理來自其他網路資源對您資料的存取嗎？ 是否有只能從 Azure 環境中存取資料的需求？ 是否有從特定 IP 位址或子網路存取資料的需求？ 是否有從裝載於內部部署或其他外部資料中心的應用程式或服務存取資料的需求？

### <a name="devops"></a>DevOps

- **技能**。 您的小組是否有特別擅長使用的特定程式設計語言、作業系統或其他技術？ 您的小組有其他難以使用的技術嗎？

- **用戶端**。您的開發語言有良好的用戶端支援嗎？

下列各節會就工作負載概況、資料類型及使用案例範例，比較各種資料存放區模型。

## <a name="relational-database-management-systems-rdbms"></a>關聯式資料庫管理系統 (RDBMS)

<!-- markdownlint-disable MD033 -->

<table>
<tr><td><strong>工作負載</strong></td>
    <td>
        <ul>
            <li>建立新記錄和更新現有的資料會定期發生。</li>
            <li>必須在單一交易中完成多項作業。</li>
            <li>需要彙總函式來執行交叉資料表。</li>
            <li>需要與報告工具的強力整合。</li>
            <li>使用資料庫條件約束來強制執行關聯性。</li>
            <li>使用索引來最佳化查詢效能。</li>
            <li>允許存取特定的資料子集。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>資料類型</strong></td>
    <td>
        <ul>
            <li>資料為高度正規化。</li>
            <li>需要且會強制執行資料庫結構描述。</li>
            <li>資料庫中的資料實體之間有多對多關聯性。</li>
            <li>結構描述中定義了條件約束，而且會加諸於資料庫中的任何資料。</li>
            <li>資料需要高完整性。 需要正確地維護索引和關聯性。</li>
            <li>資料需要強式一致性。 交易的運作方式可確保所有使用者和處理序的所有資料 100% 一致。</li>
            <li>個別資料項目的大小原本即是針對小至中型大小。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>範例</strong></td>
    <td>
        <ul>
            <li>企業營運 (人力資本管理、客戶關係管理、企業資源規劃)</li>
            <li>庫存管理</li>
            <li>報表資料庫</li>
            <li>會計</li>
            <li>資產管理</li>
            <li>資金管理</li>
            <li>訂單管理</li>
        </ul>
    </td>
</tr>
</table>

## <a name="document-databases"></a>文件資料庫

<table>
<tr><td><strong>工作負載</strong></td>
    <td>
        <ul>
            <li>一般用途。</li>
            <li>插入和更新作業很常見。 建立新記錄和更新現有的資料會定期發生。</li>
            <li>沒有物件關聯式本質不相符。 文件可以更符合應用程式程式碼中使用的物件結構。</li>
            <li>較常使用開放式並行存取。</li>
            <li>必須由取用應用程式來修改和處理資料。</li>
            <li>資料需在多個欄位中編制索引。</li>
            <li>會以單一區塊的形式來擷取與寫入個別文件。</li>
    </td>
</tr>
<tr><td><strong>資料類型</strong></td>
    <td>
        <ul>
            <li>可以透過反正規化的方式來管理資料。</li>
            <li>個別文件資料的大小相對較小。</li>
            <li>每個文件類型都可使用自己的結構描述。</li>
            <li>文件可包含選擇性欄位。</li>
            <li>文件資料是半結構化，表示每個欄位的資料類型未嚴格定義。</li>
            <li>支援資料彙總。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>範例</strong></td>
    <td>
        <ul>
            <li>產品目錄</li>
            <li>使用者帳戶</li>
            <li>用料表</li>
            <li>個人化</li>
            <li>內容管理</li>
            <li>作業資料</li>
            <li>庫存管理</li>
            <li>交易記錄資料</li>
            <li>其他 NoSQL 存放區的具體化檢視。 取代檔案/BLOB 索引。</li>
        </ul>
    </td>
</tr>
</table>

## <a name="keyvalue-stores"></a>索引鍵/值存放區

<table>
<tr><td><strong>工作負載</strong></td>
    <td>
        <ul>
            <li>使用單一識別碼索引鍵 (如字典) 來識別和存取資料。</li>
            <li>延展性極高。</li>
            <li>不需要聯結、鎖定或聯集。</li>
            <li>不會使用任何彙總機制。</li>
            <li>通常不會使用次要索引。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>資料類型</strong></td>
    <td>
        <ul>
            <li>資料大小通常很大。</li>
            <li>每個索引鍵都與單一值關聯，也就是非受控資料 BLOB。</li>
            <li>未強制執行任何結構描述。</li>
            <li>實體之間沒有關聯性。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>範例</strong></td>
    <td>
        <ul>
            <li>資料快取</li>
            <li>工作階段管理</li>
            <li>使用者的喜好設定和設定檔管理</li>
            <li>產品建議與廣告提供</li>
            <li>字典</li>
        </ul>
    </td>
</tr>
</table>

## <a name="graph-databases"></a>圖表資料庫

<table>
<tr><td><strong>工作負載</strong></td>
    <td>
        <ul>
            <li>資料項目之間的關聯性非常複雜，相關的資料項目之間包含許多躍點。</li>
            <li>資料項目之間的關聯性為動態，而且會隨時間而變更。</li>
            <li>物件之間的關聯性具有高優先性，不需要外部索引鍵和聯結即可進行周遊。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>資料類型</strong></td>
    <td>
        <ul>
            <li>資料由節點和關聯性組成。</li>
            <li>節點類似於資料表資料列或 JSON 文件。</li>
            <li>關聯性與節點一樣重要，並會直接在查詢語言中公開。</li>
            <li>複合物件 (就像擁有多個電話號碼的人) 通常會分成個別的較小節點，並和可周遊的關聯性結合 </li>
        </ul>
    </td>
</tr>
<tr><td><strong>範例</strong></td>
    <td>
        <ul>
            <li>組織圖</li>
            <li>朋友圈</li>
            <li>詐騙偵測</li>
            <li>分析</li>
            <li>推薦引擎</li>
        </ul>
    </td>
</tr>
</table>

## <a name="column-family-databases"></a>資料行系列資料庫

<table>
<tr><td><strong>工作負載</strong></td>
    <td>
        <ul>
            <li>大部分的資料行系列資料庫執行寫入作業的速度非常快。</li>
            <li>更新和刪除作業很少見。</li>
            <li>專為提供高輸送量和低延遲存取而設計。</li>
            <li>支援在大量記錄中針對特定一組欄位進行簡單查詢存取。</li>
            <li>延展性極高。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>資料類型</strong></td>
    <td>
        <ul>
            <li>資料會儲存在包含索引鍵資料行和一或多個資料行系列的資料表中。</li>
            <li>特定資料行可能會因個別資料列而不同。</li>
            <li>透過 get 和 put 命令存取個別儲存格</li>
            <li>使用 scan 命令傳回多個資料列。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>範例</strong></td>
    <td>
        <ul>
            <li>建議</li>
            <li>個人化</li>
            <li>感應器資料</li>
            <li>遙測</li>
            <li>訊息</li>
            <li>社交媒體分析</li>
            <li>Web Analytics</li>
            <li>活動監控</li>
            <li>天氣和其他時間序列資料</li>
        </ul>
    </td>
</tr>
</table>

## <a name="search-engine-databases"></a>搜尋引擎資料庫

<table>
<tr><td><strong>工作負載</strong></td>
    <td>
        <ul>
            <li>索引資料來自多個來源和服務。</li>
            <li>查詢是臨機操作的，並且可能很複雜。</li>
            <li>需要彙總。</li>
            <li>需要全文檢索搜尋。</li>
            <li>需要臨機操作自助查詢。</li>
            <li>需要對所有欄位上的索引進行資料分析。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>資料類型</strong></td>
    <td>
        <ul>
            <li>半結構化或非結構化</li>
            <li>文字</li>
            <li>具有結構化資料參考的文字</li>
        </ul>
    </td>
</tr>
<tr><td><strong>範例</strong></td>
    <td>
        <ul>
            <li>產品類別目錄</li>
            <li>網站搜尋</li>
            <li>記錄</li>
            <li>分析</li>
            <li>購物網站</li>
        </ul>
    </td>
</tr>
</table>

## <a name="data-warehouse"></a>資料倉儲

<table>
<tr><td><strong>工作負載</strong></td>
    <td>
        <ul>
            <li>資料分析</li>
            <li>Enterprise BI</li>
        </ul>
    </td>
</tr>
<tr><td><strong>資料類型</strong></td>
    <td>
        <ul>
            <li>來自多個來源的歷程記錄資料。</li>
            <li>通常在「星型」&quot;&quot;或「雪花式」&quot;&quot;結構描述中進行反正規化，包含事實和維度資料表。</li>
            <li>通常會根據排程來載入新資料。</li>
            <li>維度資料表通常包含實體的多個歷程記錄版本，稱為「緩時變維度」。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>範例</strong></td>
    <td>為分析模型、報表和儀表板提供資料的企業資料倉儲。
    </td>
</tr>
</table>

## <a name="time-series-databases"></a>時間序列資料庫

<table>
<tr><td><strong>工作負載</strong></td>
    <td>
        <ul>
            <li>有壓倒性比例 (95-99%) 的作業為寫入作業。</li>
            <li>記錄通常按照時間順序循序附加。</li>
            <li>很少更新。</li>
            <li>刪除會大量發生，並且是針對連續區塊或記錄。</li>
            <li>讀取要求可能大於可用的記憶體。</li>
            <li>同時進行多個讀取很常見。</li>
            <li>以遞增或遞減的時間順序循序讀取資料。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>資料類型</strong></td>
    <td>
        <ul>
            <li>作為主索引鍵和排序機制的時間戳記。</li>
            <li>來自項目或項目所代表意義描述的度量。</li>
            <li>針對項目的類型、來源和其他資訊，定義額外資訊的標籤。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>範例</strong></td>
    <td>
        <ul>
            <li>監視和事件遙測。</li>
            <li>感應器或其他 IoT 資料。</li>
        </ul>
    </td>
</tr>
</table>

## <a name="object-storage"></a>物件儲存體

<table>
<tr><td><strong>工作負載</strong></td>
    <td>
        <ul>
            <li>由索引鍵識別。</li>
            <li>物件可以是可公開存取或非可公開存取的。</li>
            <li>內容通常是試算表、影像或視訊檔案等資產。</li>
            <li>內容必須為永久性 (持續)，而且在任何應用程式層或虛擬機器外部。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>資料類型</strong></td>
    <td>
        <ul>
            <li>資料大小很大。</li>
            <li>Blob 資料。</li>
            <li>值為不透明的。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>範例</strong></td>
    <td>
        <ul>
            <li>影像、視訊、Office 文件、PDF</li>
            <li>CSS、指令碼、CSV</li>
            <li>靜態 HTML、JSON</li>
            <li>記錄和稽核檔案</li>
            <li>資料庫備份</li>
        </ul>
    </td>
</tr>
</table>

## <a name="shared-files"></a>共用檔案

<table>
<tr><td><strong>工作負載</strong></td>
    <td>
        <ul>
            <li>移轉自與檔案系統互動的現有應用程式。</li>
            <li>需要 SMB 介面。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>資料類型</strong></td>
    <td>
        <ul>
            <li>階層式資料夾集合中的檔案。</li>
            <li>可使用標準 I/O 程式庫來存取。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>範例</strong></td>
    <td>
        <ul>
            <li>舊版檔案</li>
            <li>可在多個 VM 或應用程式執行個體之間存取的共用內容</li>
        </ul>
    </td>
</tr>
</table>

<!-- markdownlint-enable MD033 -->

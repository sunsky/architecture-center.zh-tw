---
title: Azure 資源的命名慣例
description: Azure 資源的命名慣例。 如何為虛擬機器、儲存體帳戶、網路、虛擬網路、子網路和其他 Azure 實體命名
author: telmosampaio
ms.date: 10/19/2018
pnp.series.title: Best Practices
ms.openlocfilehash: 891fa774442ab7ec8f65eb7d8c405fa533db4760
ms.sourcegitcommit: dbbf914757b03cdee7a274204f9579fa63d7eed2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2018
ms.locfileid: "50916527"
---
# <a name="naming-conventions"></a>命名慣例

[!INCLUDE [header](../_includes/header.md)]

本文摘要說明 Azure 資源的命名規則和限制，以及一套基本的命名慣例建議。  您可以使用這些建議來開始建立符合個人需求的特有慣例。

請務必慎選 Microsoft Azure 資源的名稱，原因如下：

* 難以在之後變更名稱。
* 名稱必須符合其特定資源類型的需求。

一致的命名慣例可讓您更容易找到資源。 也能指出資源在解決方案中的角色。

命名慣例的成功關鍵在於必須跨應用程式和組織加以建立及遵循。

## <a name="naming-subscriptions"></a>命名訂用帳戶
在命名 Azure 訂用帳戶時，詳細的名稱可讓您清楚了解每個訂用帳戶的內容和目的。  在包含許多訂用帳戶的環境中工作時，遵循共同的命名慣例可以改善明確性。

訂用帳戶的建議命名模式如下：

`<Company> <Department (optional)> <Product Line (optional)> <Environment>`

* 每個訂用帳戶的公司 (Company) 通常都相同。 不過，有些公司的組織結構內可能有子公司。 這些公司可能由一個中央 IT 群組管理。 在這些情況下，它們可以同時具有父公司名稱 (*Contoso*) 和子公司名稱 (*Northwind*)，並藉此區別。
* Department (部門) 是組織內包含一群人所在單位的名稱。 此為命名空間內的選用項目。
* Product Line (產品線) 是產品或部門所行使之職責的特定名稱。 對於內部服務和應用程式來說，此項目通常是選用的。 不過，如果是需要能夠輕易區別和辨識 (例如為了清楚區別帳單記錄) 的公開服務，則強烈建議使用此項目。
* Environment (環境) 是描述應用程式或服務之部署生命週期的名稱，例如開發、品管或生產。

| 公司 | 部門 | 產品線或服務 | 環境 | 完整名稱 |
| --- | --- | --- | --- | --- |
| Contoso |SocialGaming |AwesomeService |Production |Contoso SocialGaming AwesomeService Production |
| Contoso |SocialGaming |AwesomeService |Dev |Contoso SocialGaming AwesomeService Dev |
| Contoso |IT |InternalApps |Production |Contoso IT InternalApps Production |
| Contoso |IT |InternalApps |Dev |Contoso IT InternalApps Dev |

如需有關如何組織大型企業訂用帳戶的詳細資訊，請參閱 [Azure 企業版 Scaffold - 規範性訂用帳戶治理][scaffold]。

## <a name="use-affixes-to-avoid-ambiguity"></a>使用詞綴以避免名稱不夠明確

在 Azure 中命名資源時，建議使用通用的首碼或尾碼來識別資源的類型和內容。  雖然關於類型、中繼資料和內容的資訊皆能透過程式設計方式取得，但套用通用詞綴可讓您更容易地進行識別。  在將詞綴併入命名慣例時，請務必明確指定詞綴的所在位置是名稱的開頭 (首碼) 或結尾 (尾碼)。

例如，以下是兩個適用於裝載計算引擎之服務的可能名稱：

* SvcCalculationEngine (首碼)
* CalculationEngineSvc (尾碼)

詞綴指的是可說明特定資源的各個層面。 下列表格顯示一些常用範例。

| 層面 | 範例 | 注意 |
| --- | --- | --- |
| 環境 |dev, prod, QA |識別資源的環境 |
| 位置 |uw (美國西部)、ue (美國東部) |識別資源所要部署到的區域 |
| 執行個體 |01、02 |適用於有多個具名執行個體 (Web 伺服器等) 的資源。 |
| 產品或服務 |service |識別資源可支援的產品、應用程式或服務 |
| 角色 |sql、Web、傳訊 |識別相關聯資源的角色 |

在為公司或專案開發特定命名慣例時，請務必選擇一組通用的詞綴及其位置 (尾碼或首碼)。

## <a name="naming-rules-and-restrictions"></a>命名規則和限制

在 Azure 中每個資源或服務類型會強制使用一套命名限制及範圍；任何命名慣例或模式必須遵守必要的命名規則和範圍。  例如，當 VM 的名稱對應至 DNS 名稱 (而且因此需要是整個 Azure 中是唯一的)，VNET 名稱的範圍限制在建立它的資源群組內。

一般來說，要避免任何特殊字元 (`-` 或 `_`) 作為任何名稱的第一個或最後一個字元。 這些字元會導致大部分驗證規則失敗。

### <a name="general"></a>一般

| 實體 | 影響範圍 | 長度 | 大小寫 | 有效字元 | 建議模式 | 範例 |
| --- | --- | --- | --- | --- | --- | --- |
|資源群組 |訂用帳戶 |1-90 |不區分大小寫 |英數字元、底線、括號、連字號、句號 (結尾除外) 及符合規則運算式的 Unicode 字元皆記錄在[這裡](/rest/api/resources/resourcegroups/createorupdate)。  |`<service short name>-<environment>-rg` |`profx-prod-rg` |
|可用性設定組 |資源群組 |1-80 |不區分大小寫 |英數字元、底線和連字號 |`<service-short-name>-<context>-as` |`profx-sql-as` |
|Tag |相關聯的實體 |512 (名稱)、256 (值) |不區分大小寫 |英數字元 |`"key" : "value"` |`"department" : "Central IT"` |

### <a name="compute"></a>計算

| 實體 | 影響範圍 | 長度 | 大小寫 | 有效字元 | 建議模式 | 範例 |
| --- | --- | --- | --- | --- | --- | --- |
|虛擬機器 |資源群組 |1-15 (Windows)、1-64 (Linux) |不區分大小寫 |英數字元和連字號 |`<name>-<role>-vm<number>` |`profx-sql-vm1` |
|函式應用程式 | 全域 |1-60 |不區分大小寫 |英數字元和連字號 |`<name>-func` |`calcprofit-func` |

> [!NOTE]
> Azure 中的虛擬機器有兩個不同的名稱：虛擬機器名稱和主機名稱。 當您在入口網站中建立 VM，主機名稱和虛擬機器的資源名稱會使用相同的名稱。 上述限制適用於主機名稱。 實際資源名稱最多可以有 64 個字元。

### <a name="storage"></a>儲存體

| 實體 | 影響範圍 | 長度 | 大小寫 | 有效字元 | 建議模式 | 範例 |
| --- | --- | --- | --- | --- | --- | --- |
|儲存體帳戶名稱 (資料) |全域 |3-24 |小寫 |英數字元 |`<globally unique name><number>` (使用函式來計算命名的儲存體帳戶的唯一 GUID) |`profxdata001` |
|儲存體帳戶名稱 (磁碟) |全域 |3-24 |小寫 |英數字元 |`<vm name without hyphens>st<number>` |`profxsql001st0` |
| 容器名稱 |儲存體帳戶 |3-63 |小寫 |英數字元和連字號 |`<context>` |`logs` |
|Blob 名稱 | 容器 |1-1024 |區分大小寫 |任何 URL 字元 |`<variable based on blob usage>` |`<variable based on blob usage>` |
|Queue name |儲存體帳戶 |3-63 |小寫 |英數字元和連字號 |`<service short name>-<context>-<num>` |`awesomeservice-messages-001` |
|資料表名稱 | 儲存體帳戶 |3-63 |不區分大小寫 |英數字元 |`<service short name><context>` |`awesomeservicelogs` |
|檔案名稱 | 儲存體帳戶 |3-63 |小寫 | 英數字元 |`<variable based on blob usage>` |`<variable based on blob usage>` |
|Data Lake Store | 全域 |3-24 |小寫 | 英數字元 |`<name>dls` |`telemetrydls` |

### <a name="networking"></a>網路功能

| 實體 | 影響範圍 | 長度 | 大小寫 | 有效字元 | 建議模式 | 範例 |
| --- | --- | --- | --- | --- | --- | --- |
|虛擬網路 (VNet) |資源群組 |2-64 |不區分大小寫 |英數字元、連字號、底線和句點 |`<service short name>-vnet` |`profx-vnet` |
|子網路 |父 VNet |2-80 |不區分大小寫 |英數字元、連字號、底線和句點 |`<descriptive context>` |`web` |
|網路介面 |資源群組 |1-80 |不區分大小寫 |英數字元、連字號、底線和句點 |`<vmname>-nic<num>` |`profx-sql1-vm1-nic1` |
|網路安全性群組 |資源群組 |1-80 |不區分大小寫 |英數字元、連字號、底線和句點 |`<service short name>-<context>-nsg` |`profx-app-nsg` |
|網路安全性群組規則 |資源群組 |1-80 |不區分大小寫 |英數字元、連字號、底線和句點 |`<descriptive context>` |`sql-allow` |
|公用 IP 位址 |資源群組 |1-80 |不區分大小寫 |英數字元、連字號、底線和句點 |`<vm or service name>-pip` |`profx-sql1-vm1-pip` |
|負載平衡器 |資源群組 |1-80 |不區分大小寫 |英數字元、連字號、底線和句點 |`<service or role>-lb` |`profx-lb` |
|負載平衡的規則組態 |負載平衡器 |1-80 |不區分大小寫 |英數字元、連字號、底線和句點 |`<descriptive context>` |`http` |
|Azure 應用程式閘道 |資源群組 |1-80 |不區分大小寫 |英數字元、連字號、底線和句點 |`<service or role>-agw` |`profx-agw` |
|流量管理員設定檔 |資源群組 |1-63 |不區分大小寫 |英數字元、連字號和句點 |`<descriptive context>` |`app1` |

### <a name="containers"></a>容器

| 實體 | 影響範圍 | 長度 | 大小寫 | 有效字元 | 建議模式 | 範例 |
| --- | --- | --- | --- | --- | --- | --- |
|Container Registry | 全域 |5-50 |不區分大小寫 | 英數字元 |`<service short name>registry` |`app1registry` |


## <a name="organize-resources-with-tags"></a>具有標籤的組織資源

Azure Resource Manager 支援使用任意文字字串來標記實體，以識別內容並簡化自動化作業。  例如，標記 `"sqlVersion"="sql2014ee"` 可以識別執行 SQL Server 2014 企業版的虛擬機器。 標記應該用來與所選擇的命名慣例一起擴充及增強內容。

> [!TIP]
> 標記的另一項優點是標記可跨越資源群組，因此可讓您連結不同部署間的實體並予以相互關聯。

每個資源或資源群組最多可以有 **15** 個標記。 標記名稱上限為 512 個字元，且標記值上限為 256 字元。

如需資源標記的詳細資訊，請參閱[使用標記來組織您的 Azure 資源](/azure/azure-resource-manager/resource-group-using-tags/)。

部分常見的標記使用案例包括：

* **計費**：將資源分組，並讓其與計費或退費代碼建立關聯。
* **服務內容識別**：識別所有資源群組的各組資源，以進行常見作業和分組
* **存取控制和安全性內容**：根據產品組合、系統、服務、應用程式、執行個體等項目識別系統管理角色。

> [!TIP]
> 請盡早且經常地標記。  最好是備有基準標記計畫並隨時調整，而不要事後才來修改。

部分常見的標記方法範例如下：

| 標記名稱 | Key | 範例 | 註解 |
| --- | --- | --- | --- |
| 帳單收件者/內部退款識別碼 |billTo |`IT-Chargeback-1234` |內部的 I/O 或計費代碼 |
| 操作員或直接負責人 (DRI) |managedBy |`joe@contoso.com` |別名或電子郵件地址 |
| 專案名稱 |projectName |`myproject` |專案或產品線的名稱 |
| 專案版本 |projectVersion |`3.4` |專案或產品線的版本 |
| 環境 |Environment |`<Production, Staging, QA >` |環境識別碼 |
| 層 |層 |`Front End, Back End, Data` |層或角色/內容識別 |
| 資料設定檔 |dataProfile |`Public, Confidential, Restricted, Internal` |資源中所儲存資料的敏感性 |

## <a name="tips-and-tricks"></a>秘訣與技巧

某些類型的資源的命名和慣例可能需要特別注意。

### <a name="virtual-machines"></a>虛擬機器

特別是在較大型的拓撲中，小心命名虛擬機器可簡化每個機器之角色和用途的識別，以及製作出更容易預測的指令碼。

### <a name="storage-accounts-and-storage-entities"></a>儲存體帳戶和儲存體實體

儲存體帳戶有兩個主要的使用案例，分別是備份 VM 的磁碟以及將資料儲存在 Blob、佇列和資料表中。  用於 VM 磁碟的儲存體帳戶應遵循將其與父 VM 名稱建立關聯的命名慣例 (如果可能需要多個儲存體帳戶來用於高階 VM SKU，則還需要套用數字尾碼)。

> [!TIP]
> 無論是用於資料還是磁碟的儲存體帳戶，都應該遵循能夠利用多個儲存體帳戶的命名慣例 (也就是一律使用數字尾碼)。

您可以設定自訂網域名稱，以供存取 Azure 儲存體帳戶中的 blob 資料。 Blob 服務的預設端點是 https://\<name\>.blob.core.windows.net。

但如果您將自訂網域 (如 www.contoso.com) 對應至儲存體帳戶的 Blob 端點，也能使用該網域來存取儲存體帳戶中的 Blob 資料。 例如，使用自訂網域名稱，可以用 `https://www.contoso.com/mycontainer/myblob` 存取 `https://mystorage.blob.core.windows.net/mycontainer/myblob`。

如需有關設定此功能的詳細資訊，請參閱 [針對 blob 儲存體端點設定自訂網域名稱](/azure/storage/storage-custom-domain-name/)。

如需有關命名 Blob、容器和資料表的詳細資訊，請參閱以下清單：

* [命名和參考容器、Blob 及中繼資料](https://msdn.microsoft.com/library/dd135715.aspx)
* [命名佇列和中繼資料](https://msdn.microsoft.com/library/dd179349.aspx)
* [命名資料表](https://msdn.microsoft.com/library/azure/dd179338.aspx)

Blob 名稱可以包含任何字元組合，但是必須正確逸出保留的 URL 字元。 避免使用結尾是句號 (.)、正斜線 (/) 或一連串的兩者或兩者之組合的 Blob 名稱。 依照慣例，正斜線是 **虛擬** 目錄分隔符號。 請勿在 blob 名稱中使用反斜線 (\\)。 雖然用戶端 API 可能允許，但接下來卻會無法正常雜湊且簽章不會相符。

儲存體帳戶或容器的名稱在建立後就無法修改。 如果想要使用新名稱，必須先刪除舊名稱再建立新名稱。

> [!TIP]
> 建議您先對所有儲存體帳戶和類型建立命名慣例，再開始開發新的服務或應用程式。

<!-- links -->

[scaffold]: /azure/architecture/cloud-adoption/appendix/azure-scaffold

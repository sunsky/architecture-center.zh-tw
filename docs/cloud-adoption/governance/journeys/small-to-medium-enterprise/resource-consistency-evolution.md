---
title: 'CAF：中小型企業 - 資源一致性演進 '
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 說明：中小型企業 - 資源一致性演進
author: BrianBlanchard
ms.openlocfilehash: 402bb3ff4182123cdc8825522f965f7cf8637980
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900951"
---
# <a name="small-to-medium-enterprise-resource-consistency-evolution"></a>中小型企業：資源一致性演進

本文將從新增資源一致性控制項來支援任務關鍵性應用程式這一點上發展敘述。

## <a name="evolution-of-the-narrative"></a>敘述的演進

新的客戶體驗、新的預測工具和已移轉的基礎結構會繼續進行。 企業現在已準備開始在產能上使用這些資產。

### <a name="evolution-of-the-current-state"></a>目前狀態的演進

在此敘述的上一個階段中，應用程式開發和 BI 小組幾乎已準備好將客戶和財務資料整合到生產工作負載。 IT 小組已經開始淘汰 DR 資料中心。

從那時起，某些將會影響治理的事項已經改變：

- IT 已 100% 淘汰 DR 資料中心，且進度超前。 在過程中，生產資料中心的大量資產會識別為雲端移轉候選項目。
- 應用程式開發小組現在已準備好使用生產環境流量。
- BI 小組已準備好將預測和深入解析回饋至生產資料中心內的作業系統。

### <a name="evolution-of-the-future-state"></a>未來狀態的演進

在生產業務程序中使用 Azure 部署之前，必須有成熟的雲端作業。 必須搭配其他治理演進，才能確保資產可正確運作。

目前和未來狀態的變更會產生新風險，需要新的原則聲明。

## <a name="evolution-of-tangible-risks"></a>實質風險的演進

**業務中斷**：任何新平台都有使任務關鍵性業務程序中斷的固有風險。 IT 營運小組和執行各種雲端採用的小組都相對缺乏雲端作業的相關經驗。 這會增加業務中斷的風險，而且必須降低風險及進行治理。

此業務風險會延伸成大量技術風險：

- 外部入侵或拒絕服務攻擊可能會導致業務中斷
- 無法正確探索到任務關鍵性的資產，因此可能無法正確運作
- 現有的作業管理程序可能不支援未探索到或標記錯誤的資產。
- 已部署的資產設定可能不符合預期的效能
- 記錄可能無法正確記載並集中，因此無法補救效能問題。
- 復原原則可能會失敗，或是執行時間超出預期。
- 不一致的部署程序可能導致安全性漏洞，並造成資料外洩或中斷。
- 設定漂移或遺失修補程式可能會導致非預期的安全性漏洞，並造成資料外洩或中斷。
- 設定可能不會強制執行所定義的 SLA 需求或已認可的復原要求。
- 已部署的作業系統或應用程式可能無法符合強化需求
- 有很多小組在雲端中工作時，就會有不一致的風險。

## <a name="evolution-of-the-policy-statements"></a>原則聲明的演進

下列原則變更有助於減緩新風險和指導實作。 清單看起來很長，但採用這些原則可能比看起來的簡單。

1. 所有已部署的資產必須依據重要性和資料類別來分類。 分類會由雲端治理小組和應用程式擁有者進行檢閱，然後再部署到雲端
2. 包含任務關鍵性應用程式的子網路必須受到防火牆解決方案的保護，以偵測入侵及回應攻擊。
3. 治理工具必須稽核並強制執行安全性管理小組所定義的網路設定需求
4. 治理工具必須確認所有與任務關鍵性應用程式或受保護資料相關的資產都會受到監視，以了解資源損耗與最佳化的情形。
5. 治理工具必須確認會針對所有任務關鍵性應用程式或受保護的資料，收集適當層級的記錄資料。
6. 治理程序必須確認任務關鍵性應用程式和受保護資料的備份、復原和 SLA 遵循皆正確實作。
7. 治理工具必須限制只對已核准的映像進行虛擬機器部署。
8. 治理工具必須強制避免在支援任務關鍵性應用程式的所有已部署資產上進行自動更新。 必須與作業管理小組一起檢閱違規事件，並根據作業原則來進行修復。 不會自動更新的資產必須包含在 IT 部門所負責的處理程序中。
9. 治理工具必須確認與成本、重要性、SLA、應用程式及資料類別相關的標記。 所有值必須符合由治理小組管理的預先定義值。
10. 治理程序必須包含部署期間和一般週期的稽核，以確保所有資產之間的一致性。
11. 安全性小組應定期檢閱可能影響雲端部署的趨勢與攻擊，以更新雲端中使用的安全性管理工具。
12. 在發行到生產環境之前，必須將所有任務關鍵性應用程式和受保護的資料新增至指定的作業監視解決方案。 如果所選取的 IT 作業工具找不到某些資產，則這些資產就無法發行為生產用的資產。 為了讓資產變得可探索所做的變更，也必須對相關部署程序執行，如此才能確保在未來的部署中能探索到該資產。
13. 發現資產時，作業管理小組會調整資產的大小，以確保資產符合效能需求
14. 部署工具必須由雲端治理小組核准，以確保能持續治理已部署的資產。
15. 部署指令碼必須在雲端治理小組可存取的中央存放庫中進行維護，以便定進行定期檢閱和稽核。
16. 治理檢閱程序必須確認部署的資產已根據 SLA 及復原需求進行正確的設定。

## <a name="evolution-of-the-best-practices"></a>最佳做法的演進

本文的這一節將推演治理 MVP 設計，以包含新的 Azure 原則和實作 Azure 成本管理。 這兩個設計變更將共同實現新的公司原則聲明。

1. 雲端作業小組會定義作業監視工具及自動補救工具。 雲端治理小組將會支援這些探索程序。 在此使用案例中，雲端作業小組會選擇 Azure 監視器作為監視任務關鍵性應用程式的主要工具。
2. 在 Azure DevOps 中建立存放庫來存放所有相關的 Resource Manager 範本和指令碼式的組態，並為這些項目設定版本。
3. Azure Vault 實作：
    1. 定義及部署用於備份和復原程序的 Azure Vault。
    2. 建立 Resource Manager 範本以在每個訂用帳戶中建立保存庫。
4. 更新所有訂用帳戶的 Azure 原則：
    1. 在所有訂用帳戶上稽核並強制執行重要性和資料分類，以識別任何具有任務關鍵性資產的訂用帳戶。
    2. 稽核並限制使用已核准的映像。
5. Azure 監視器實作：
    1. 找到任務關鍵性的訂用帳戶後，使用 PowerShell 建立 Azure 監視器工作區。 這是一個預先部署程序。
    2. 在部署測試期間，雲端作業小組會部署所需的代理程式並測試探索。
6. 針對包含任務關鍵性應用程式的所有訂用帳戶更新 Azure 原則。
    1. 稽核並強制執行使用 NSG 連到所有 NIC 和子網路的應用程式。 網路和 IT 安全性會定義 NSG。
    2. 稽核並強制對每個網路介面使用已核准的網路子網路和 VNet。
    3. 稽核並強制執行使用者定義路由表的限制。
    4. 稽核並強制對所有虛擬機器部署 Azure 監視器代理程式。
    5. 稽核並強制訂用帳戶中必須有 Azure Vault。
7. 防火牆組態：
    1. 識別符合安全性需求的 Azure 防火牆設定。 或者，識別與 Azure 相容的第三方應用裝置。
    2. 建立 Resource Manager 範本來部署具有必要設定的防火牆。
8. Azure 藍圖：
    1. 建立名為 `protected-data` 的新 Azure 藍圖。
    2. 對藍圖新增防火牆和 Azure Vault 範本。
    3. 為受保護資料的訂用帳戶新增原則。
    4. 將藍圖發佈到任何要裝載任務關鍵性應用程式的管理群組。
    5. 對每個受影響的訂用帳戶及現有藍圖套用新的藍圖。

## <a name="conclusion"></a>結論

對治理 MVP 新增的這些流程和變更，有助於降低與資源治理相關聯的許多風險。 同時還會新增可加強雲端感知作業的復原、大小調整及監視控制項。

## <a name="next-steps"></a>後續步驟

隨著雲端採用持續演進，並提供額外的商業價值，風險和雲端治理需求也需要與時俱進。 對此旅程中的虛構公司而言，下一個觸發點是將超過 100 個資產部署到雲端的時候，或每月支出超過 $1,000 美元的時候。 此時，雲端治理小組會新增成本管理控制項。

> [!div class="nextstepaction"]
> [成本管理演進](./cost-management-evolution.md)
---
title: 軟體品質的要素
description: 說明軟體品質的五大要素，也就是延展性、可用性、災害復原、管理性和安全性。
author: MikeWasson
ms.openlocfilehash: 1d5e30602cafa0d39f92de3101974e77ae258595
ms.sourcegitcommit: a7aae13569e165d4e768ce0aaaac154ba612934f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2018
ms.locfileid: "28896349"
---
# <a name="pillars-of-software-quality"></a>軟體品質的要素 

成功的雲端應用程式會將焦點放在軟體品質的五大要素：延展性、可用性、災害復原、管理性和安全性。

| 要素 | 說明 |
|--------|-------------|
| 延展性 | 系統處理負載增加的能力。 |
| 可用性 | 系統運作並執行作業的時間比例。 |
| 災害復原 | 系統從失敗中復原並繼續運作的能力。 |
| 管理性 | 讓系統在生產環境中順利運作的作業流程。 |
| 安全性 | 保護應用程式和資料，使其免於威脅。 |

## <a name="scalability"></a>延展性

延展性是系統處理負載增加的能力。 應用程式有兩種主要的調整方式。 垂直調整 (「相應增加」) 代表增加資源的容量，例如使用較大的 VM 大小。 水平調整 (「相應放大」) 則會為資源 (例如 VM 或資料庫複本) 新增執行個體。 

水平調整的優勢明顯大過垂直調整：

- 真正的雲端規模。 可以將應用程式設計成在數百乃至數千個節點上執行，達到單一節點所不可能提供的規模。
- 水平調整具有彈性。 您可以在負載增加時新增更多執行個體，也可以在負載較少時予以移除。
- 相應放大程序可自動觸發，不論是根據排程或因應負載的變化。 
- 相應放大的成本可能低於相應增加。 執行數個小型 VM 的成本會少於執行單一大型 VM。 
- 藉由增加備援執行個體，水平調整也可以提升災害復原。 如果有某執行個體停止運作，應用程式還是會繼續執行。

垂直調整的優點是不必變更應用程式就可進行。 但是到達一定規模後就會出現限制，而無法再進行相應增加。 屆時，如果要再擴大規模，就必須進行水平調整。 

水平調整必須在系統設計時就要考量到。 例如，您可以將 VM 放在負載平衡器後方，以進行相應放大。 但是在集區中的每個 VM 都必須能夠處理任何用戶端要求，因此，應用程式必須是無狀態模式或是將狀態儲存在外部 (例如，儲存在分散式快取)。 受控 PaaS 服務通常都會內建水平調整和自動調整。 能夠輕鬆調整這些服務是使用 PaaS 服務的主要優勢。

能夠輕鬆調整這些服務是使用 PaaS 服務的主要優勢之一。 這種做法可能只是將效能瓶頸推向其他地方。 例如，如果您調整 Web 前端來處理更多用戶端要求，這可能會讓資料庫觸發鎖定爭用。 然後，您就必須考慮採取其他措施 (例如，開放式並行存取或資料分割)，才能提高進入資料庫的交易量。

請一定要進行效能和負載測試，以找出這些潛在的瓶頸。 系統的具狀態部分 (例如資料庫) 是最常造成瓶頸的因素，必須要謹慎的設計才能順利進行水平調整。 解決一個瓶頸可能會讓其他地方又出現瓶頸。

請使用[延展性檢查清單][scalability-checklist]，從延展性的角度檢視您的設計。

### <a name="scalability-guidance"></a>延展性指導方針

- [延展性和效能的設計模式][scalability-patterns]
- 最佳實踐 ：[自動調整][autoscale]、[背景作業][background-jobs]、[快取][caching]、[CDN][cdn]、[資料分割][data-partitioning]

## <a name="availability"></a>可用性

可用性是系統正常運作並執行作業的時間比例。 通常以運作時間百分比加以測量。 應用程式錯誤、基礎結構問題和系統負載全都有可能會降低可用性。 

雲端應用程式應該要有服務等級目標 (SLO)，用以清楚定義預期的可用性以及可用性的測量方式。 在定義可用性時，應關注關鍵路徑。 Web 前端或許可以服務用戶端要求，但如果每一筆交易都因無法連線到資料庫而失敗，使用者就無法使用應用程式。 

可用性通常是以「幾個 9」來描述，例如，「四個 9」表示 99.99% 的運作時間。 下表顯示各種可用性等級的潛在累計停機時間。

| 運作時間 % | 每週停機時間 | 每月停機時間 | 每年停機時間 |
|----------|-------------------|--------------------|-------------------|
| 99% | 1.68 小時 | 7.2 小時 | 3.65 天 |
| 99.9% | 10 分鐘 | 43.2 分鐘 | 8.76 小時 |
| 99.95% | 5 分鐘 | 21.6 分鐘 | 4.38 小時 |
| 99.99% | 1 分鐘 | 4.32 分鐘 | 52.56 分鐘 |
| 99.999% | 6 秒 | 26 秒 | 5.26 分鐘 |

請注意，99% 的運作時間相當於每週會有將近 2 小時的服務中斷時間。 對於許多應用程式來說，尤其是消費者導向的應用程式，這樣的 SLO 令人無法接受。 另一方面，五個 9 (99.999%) 代表「一年」當中的停機時間不會超過 5 分鐘。 光是可以快速的偵測到服務中斷情形就已經很困難了，更別說要解決問題。 若要有相當高的可用性 (99.99% 或以上)，您不能想要光靠人為的方式進行從失敗中復原。 應用程式必須要能夠自我診斷和自我修復，因此災害復原就顯得非常重要。

在 Azure 中，服務等級協定 (SLA) 描述 Microsoft 對運作時間與連線能力的承諾。 如果特定服務的 SLA 是 99.95%，表示您可以預期 99.95% 的時間皆可提供服務。

應用程式經常依賴多個服務。 一般情況下，服務發生停機的機率是各自獨立的。 例如，假設您的應用程式依賴兩個服務，這兩個服務各有 99.9% 的 SLA。 這兩個服務的複合 SLA 是 99.9% &times; 99.9% &asymp; 99.8%，也就是略低於每個服務本身的 SLA。 

請使用[可用性檢查清單][availability-checklist]，從可用性的角度檢視您的系統設計。

### <a name="availability-guidance"></a>可用性指導方針

- [可用性的設計模式][availability-patterns]
- 最佳實踐 ：[自動調整][autoscale]、[背景作業][background-jobs]

## <a name="resiliency"></a>災害復原

災害復原是指系統從失敗中復原並繼續運作的能力。 災害復原的目標是讓應用程式在發生失敗後，能夠恢復到完全正常運作的狀態。 災害復原與可用性緊密相關。

傳統上在開發應用程式時，會將焦點放在減少平均失敗時間 (MTBF)。 將許多心力花在避免系統發生失敗之情形。 在雲端運算中，則需要不同的思維，原因如下：

- 分散式系統實為複雜，某處發生失敗很可能會讓整個系統發生連鎖反應。
- 為了維持低成本，雲端環境會使用標準規格之硬體，因此一定會偶有硬體發生故障情形。 
- 應用程式經常依賴外部服務，但這些外部服務可能會暫時無法使用，或是針對大量使用者進行限流處理。 
- 現今的使用者都期望應用程式應全天候都隨時可用，永遠不會離線。

綜合上述之因素，雲端應用程式必須設計成預期會偶爾發生失敗並可以從中復原。 Azure 平台已內建許多災害復原功能。 例如， 

- Azure 儲存體、SQL Database 和 Cosmos DB 全都提供內建的資料複寫功能，不論是單一區域內或是跨區域皆有提供。
- Azure 受控磁碟會自動放在不同的儲存體縮放單位，以減少受到硬體故障之影響程度。
- 可用性群組中的 VM 會分散到數個容錯網域。 容錯網域是一組 VM，這些 VM 會共用電力來源和網路交換器。 將 VM 分散到不同的容錯網域可以減少當實體硬體故障、網路中斷或電源中斷所產生的影響。

話雖如此，您仍需要在您的應用程式中建立災害復原能力。 復原策略可以套用到系統架構中的所有層級。 某些緩和措施在本質上較有戰術意義，例如，在短暫的網路失敗後重試遠端呼叫。 其他緩和措施則較有策略意義，例如將整個應用程式容錯移轉到次要區域。 戰術性緩和措施可以產生很大的不同結果。 雖然很少遇到整個區域都發生中斷的情況，但網路壅塞之類的暫時性問題並不罕見，因此請先以此做為目標措施。 擁有適當的監視和診斷能力也很重要，這樣才能偵測到失敗的發生，並找出根本原因。

在為應用程式設計災害復原能力時，您必須了解您的可用性需求。 可接受多少停機時間？ 這是功能成本的部分考量。 可能的停機時間會造成多少業務成本？ 您應該將多少成本投資於此應用程式的高可用性？

請使用[災害復原檢查清單][resiliency-checklist]，從災害復原的角度檢視您的設計。

### <a name="resiliency-guidance"></a>災害復原指導方針

- [為 Azure 設計有彈性的應用程式][resiliency]
- [災害復原的設計模式][resiliency-patterns]
- 最佳實踐 ：[暫時性錯誤的處理][transient-fault-handling]、[特定服務的重試指引][retry-service-specific]

## <a name="management-and-devops"></a>管理性和 DevOps

此要素涵蓋讓應用程式在生產環境中執行的作業流程。

部署必須可靠且可預測。 應將部署程序自動化，以避免發生人為錯誤。 部署程序應迅速且成為例行工作，以免拖累新功能或錯誤修正的發行速度。 同樣重要的是，您必須能夠在更新發生問題時快速復原或向前復原。

監視和診斷能力極為重要。 雲端應用程式會在遠端資料中心內執行，而您並沒有其基礎架構或作業系統 (有時候) 的完整控制權。 在大型應用程式中，登入 VM 來疑難排解問題或詳查記錄檔是不切實際的行為。 在使用 PaaS 服務時，甚至可能沒有可供登入的專用 VM。 監視和診斷能力可讓您深入了解系統，以在失敗發生時得知消息並知道其發生位置。 所有系統必須可被觀察。 請使用通用且一致的記錄結構描述，以便讓系統中的所有事件相互關聯。

監視和診斷程序有數個不同的階段：

- 檢測。 從應用程式記錄、Web 伺服器記錄、Azure 平台內建的診斷功能以及其他來源，產生未經處理的資料。
- 收集和儲存。 將資料合併到一處。
- 分析及診斷。 疑難排解問題，並觀察系統整體健康程度。
- 視覺化和警示。 使用遙測資料來找出趨勢或對作業小組發出警示。

請使用 [DevOps 檢查清單][ devops-checklist]，從管理和 DevOps 的角度檢視您的設計。

### <a name="management-and-devops-guidance"></a>管理和 DevOps 指導方針

- [管理與監視的設計模式][management-patterns]
- 最佳實踐：[監視和診斷][monitoring]

## <a name="security"></a>安全性

您必須思考應用程式從設計和實作到部署與運維之整個生命週期的安全性。 Azure 平台提供防範各種不同威脅，例如網路入侵和 DDoS 攻擊。 但您仍需要在應用程式和 DevOps 程序中建立安全性。

以下是部分需要考慮的安全性領域。 

### <a name="identity-management"></a>身分識別管理

請考慮使用 Azure Active Directory (Azure AD) 來驗證並授權使用者。 Azure AD 是完全受控的身分識別和存取管理服務。 您可以用它來建立僅存在於 Azure 中的網域，或用它來整合內部部署之 Active Directory 身分識別。 Azure AD 也整合了 Office365、Dynamics CRM Online 和眾多第三方 SaaS 應用程式。 對於消費者導向的應用程式，Azure Active Directory B2C 可讓使用者使用其現有的社交帳戶 (例如 Facebook、Google 或 LinkedIn) 進行驗證，或建立由 Azure AD 管理的新使用者帳戶。

如果您想要整合內部部署 Active Directory 環境與 Azure 網路，可行的方法有好幾個，端視您的需求而定。 如需詳細資訊，請參閱[身分識別管理][identity-ref-arch]參考架構。

### <a name="protecting-your-infrastructure"></a>保護您的基礎架構 

請控管您所部署之 Azure 資源的存取權。 每個 Azure 訂用帳戶都會與 Azure AD 租用戶有[信任關係][ad-subscriptions]。 請使用[角色存取控制][ rbac] (RBAC) 來為您組織內的使用者授與正確的 Azure 資源權限。 若要授與存取權，請將 RBAC 角色指派給某個範圍內的使用者或群組。 此範圍可以是訂用帳戶、資源群組或是單一資源。 對基礎架構的所有變更進行[稽核][resource-manager-auditing]。 

### <a name="application-security"></a>應用程式安全性

一般情況下，開發應用程式時的安全性最佳實踐仍適用於雲端。 這些做法包括在所有地方使用 SSL、防範 CSRF 和 XSS 攻擊、防止 SQL 資料隱碼攻擊等等。 

雲端應用程式通常會使用具有存取金鑰的受控服務。 請永遠不要將這些存取金鑰簽入到原始檔控制。 您可以考慮將應用程式密碼儲存在 Azure Key Vault。

### <a name="data-sovereignty-and-encryption"></a>資料主權和加密

在使用 Azure 的高可用性功能時，請確保您的資料會留在正確的地緣政治區域內。 Azure 的異地備援儲存體會在相同的地緣政治區域中使用[配對區域][paired-region]的概念。 

請使用 Key Vault 來保護加密金鑰和密碼。 藉由使用 Key Vault，您便可以使用硬體安全性模組 (HSM) 所保護的金鑰來加密金鑰和密碼。 許多 Azure 儲存體和 DB 服務都支援資料靜態加密，包括 [Azure 儲存體][storage-encryption]、[Azure SQL Database][sql-db-encryption]、[Azure SQL 資料倉儲][data-warehouse-encryption]和 [Cosmos DB][cosmosdb-encryption]。

### <a name="security-resources"></a>安全性資源

- [Azure 資訊安全中心][security-center]可針對您所有的 Azure 訂用帳戶提供整合式安全性監視和原則管理。 
- [Azure 安全性文件][security-documentation]
- [Microsoft 信任中心][trust-center]



<!-- links -->

[dr-guidance]: ../resiliency/disaster-recovery-azure-applications.md
[identity-ref-arch]: ../reference-architectures/identity/index.md
[resiliency]: ../resiliency/index.md

[ad-subscriptions]: /azure/active-directory/active-directory-how-subscriptions-associated-directory
[data-warehouse-encryption]: /azure/data-lake-store/data-lake-store-security-overview#data-protection
[cosmosdb-encryption]: /azure/cosmos-db/database-security
[rbac]: /azure/active-directory/role-based-access-control-what-is
[paired-region]: /azure/best-practices-availability-paired-regions
[resource-manager-auditing]: /azure/azure-resource-manager/resource-group-audit
[security-blog]: https://azure.microsoft.com/blog/tag/security/
[security-center]: https://azure.microsoft.com/services/security-center/
[security-documentation]: /azure/security/
[sql-db-encryption]: /azure/sql-database/sql-database-always-encrypted-azure-key-vault
[storage-encryption]: /azure/storage/storage-service-encryption
[trust-center]: https://azure.microsoft.com/support/trust-center/


<!-- patterns -->
[availability-patterns]: ../patterns/category/availability.md
[management-patterns]: ../patterns/category/management-monitoring.md
[resiliency-patterns]: ../patterns/category/resiliency.md
[scalability-patterns]: ../patterns/category/performance-scalability.md


<!-- practices -->
[autoscale]: ../best-practices/auto-scaling.md
[background-jobs]: ../best-practices/background-jobs.md
[caching]: ../best-practices/caching.md
[cdn]: ../best-practices/cdn.md
[data-partitioning]: ../best-practices/data-partitioning.md
[monitoring]: ../best-practices/monitoring.md
[retry-service-specific]: ../best-practices/retry-service-specific.md
[transient-fault-handling]: ../best-practices/transient-faults.md


<!-- checklist -->
[availability-checklist]: ../checklist/availability.md
[devops-checklist]: ../checklist/dev-ops.md
[resiliency-checklist]: ../checklist/resiliency.md
[scalability-checklist]: ../checklist/scalability.md

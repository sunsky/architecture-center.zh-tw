---
title: 保護資料解決方案
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.openlocfilehash: 453897d1dde205ec8eb094df06ec66da43f7de7b
ms.sourcegitcommit: e7e0e0282fa93f0063da3b57128ade395a9c1ef9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52901638"
---
# <a name="securing-data-solutions"></a>保護資料解決方案

在許多情況下，若要讓資料能夠在雲端中存取 (特別是移轉只在內部部署資料存放區使用的資料時)，在增加該資料的協助工具及用來保護其安全的新方法上，可能會產生一些顧慮。

## <a name="challenges"></a>挑戰

* 集中監視和分析儲存在多個記錄中的安全性事件。
* 跨應用程式和服務實作加密和授權管理。
* 確保集中式身分識別管理可跨越所有解決方案元件運作，不論在內部部署或雲端。

## <a name="data-protection"></a>資料保護

保護資訊的第一步是識別要保護的目標。 開發清楚、簡單及易懂的指導方針來識別、保護及監視最重要的資料資產 (無論位於任何地方)。 針對會對組織任務或獲利能力產生過度影響的資產，提供最嚴密的保護。 這些資產稱為高價值資產或 HVA。 執行嚴格的 HVA 生命週期和安全相依性分析，並建立適當的安全性控制及條件。 同樣地，識別並分類機密資產，並定義技術和程序來自動套用安全性控制。

一旦已識別需要保護的資料，請考量資料在「待用」和「傳輸中」的保護方式。

* **待用資料**：在實體媒體 (磁碟或光碟)、內部部署或雲端中靜態儲存的資料。
* **傳輸中資料**︰正在元件、位置或程式之間傳送的資料，可能會透過網路、透過服務匯流排 (從內部部署至雲端，反之亦然)，或透過輸入/輸出的程序。

若要了解保護待用或傳輸中資料的詳細資訊，請參閱 [Azure 資料安全性和加密最佳做法](/azure/security/azure-security-data-encryption-best-practices)。

## <a name="access-control"></a>存取控制

保護雲端中資料的重點是身分識別管理和存取控制的組合。 由於有各種雲端服務類型以及逐漸普及的[混合式雲端](../scenarios/hybrid-on-premises-and-cloud.md)，在身分識別和存取控制上應遵循幾個重要的做法：

* 集中管理您的身分識別。
* 啟用單一登入 (SSO)。
* 部署密碼管理。
* 對使用者強制執行多重要素驗證 (MFA)。
* 使用角色型存取控制 (RBAC)。
* 應設定條件式存取原則，其透過使用者位置、裝置類型、修補程式等級等其他相關屬性來增強傳統使用者識別的概念。
* 使用資源管理員來控制資源的建立位置。
* 主動監視可疑的活動

如需詳細資訊，請參閱 [Azure 身分識別管理和存取控制安全性最佳作法](/azure/security/azure-security-identity-management-best-practices)。

## <a name="auditing"></a>稽核

除了先前所述的身份識別與存取監視外，您在雲端中使用的服務和應用程式應產生您可以監視的安全性相關事件。 要監視這些事件的主要挑戰是處理大量記錄，這是為了避免潛在問題或對過去的問題進行疑難排解。 雲端式應用程式通常含有許多可移動的組件，其中大部分會產生某種程度的記錄與遙測。 使用集中監視及分析可協助您管理大量資訊，並讓其具有意義。

如需詳細資訊，請參閱 [Azure 記錄與稽核](/azure/security/azure-log-audit)。



## <a name="securing-data-solutions-in-azure"></a>在 Azure 中保護資料解決方案

### <a name="encryption"></a>加密

**虛擬機器**。 使用 [Azure 磁碟加密](/azure/security/azure-security-disk-encryption)來加密 Windows 或 Linux VM 上連結的磁碟。 此解決方案與 [Azure Key Vault](/azure/key-vault/) 整合，可控制和管理磁碟加密金鑰和祕密。 

**Azure 儲存體**。 使用[Azure 儲存體服務加密](/azure/storage/common/storage-service-encryption)來自動加密 Azure 儲存體中的待用資料。 以完全無感的方式處理所有加密、解密和金鑰管理。 也可以使用用戶端加密與 Azure Key Vault 來保護傳輸中的資料。 如需詳細資訊，請參閱 [Microsoft Azure 儲存體的用戶端加密和 Azure Key Vault](/azure/storage/common/storage-client-side-encryption)。

**SQL Database** 和 **Microsoft Azure SQL 資料倉儲**。 使用[透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) (TDE) 可在不需變更應用程式的情況下，對資料庫、相關聯的備份和交易記錄檔執行即時加密和解密。 SQL Database 亦可使用 [一律加密](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)，不論是當機密資料在伺服器上待用時、在用戶端與伺服器之間移動時，還是使用中時，都可協助保護資料。 您可以使用 Azure Key Vault 來儲存「一律加密」加密金鑰。 

### <a name="rights-management"></a>版權管理

[Azure Rights Management](/information-protection/understand-explore/what-is-azure-rms) 是使用加密、身分識別和授權原則來保護檔案和電子郵件的雲端式服務。 可以跨多個裝置 (手機、平板電腦及電腦) 運作。 可以在組織內部和外部保護資料，因為該保護會保存資料，即使資料已離開組織的範圍也一樣。

### <a name="access-control"></a>存取控制

使用[角色型存取控制](/azure/active-directory/role-based-access-control-what-is) (RBAC)，根據使用者角色來限制 Azure 資源的存取。 如果您使用 Active Directory 內部部署，您可以[ 與 Azure AD 同步](/azure/active-directory/active-directory-hybrid-identity-design-considerations-directory-sync-requirements)，根據使用者的內部部署身份識別為其提供雲端識別身分。

您可以使用 [Azure Active Directory 中的條件式存取](/azure/active-directory/active-directory-conditional-access-azure-portal)，根據特定條件來強制控制您環境中的應用程式存取。 例如，您的原則聲明可能需要採取下列形式：_當約聘員工嘗試從不受信任的網路存取我們的雲端應用程式時，則封鎖存取_。 

[Azure AD Privileged Identity Management](/azure/active-directory/active-directory-privileged-identity-management-configure) 可協助您管理、控制及監視您的使用者，以及他們正在以其系統管理員權限執行哪些工作。 這是重要的步驟，可限制您組織中的哪些人可以在 Azure AD、Azure、Office 365 或 SaaS 應用程式中執行特權作業，以及監視他們活動。

### <a name="network"></a>網路

若要保護傳輸中的資料，請一律使用 SSL/TLS 來交換不同位置之間的資料。 您有時需要使用虛擬私人網路 (VPN) 或 [ExpressRoute](/azure/expressroute/)，隔離您的內部部署與雲端基礎結構之間的整個通訊通道。 如需詳細資訊，請參閱[將內部部署資料解決方案擴充至雲端](../scenarios/hybrid-on-premises-and-cloud.md)。

使用[網路安全性群組](/azure/virtual-network/virtual-networks-nsg) (NSG) 以減少潛在的攻擊媒介數目。 網路安全性群組包含一些安全性規則，可根據來源或目的地 IP 位址、連接埠和通訊協定允許或拒絕輸入或輸出網路流量。 

使用[虛擬網路服務端點](/azure/virtual-network/virtual-network-service-endpoints-overview) 來保護 Azure SQL 或 Azure 儲存體資源，只允許虛擬網路的流量存取這些資源。

Azure 虛擬網路 (VNet) 內的 VM 可使用[虛擬網路對等互連互連](/azure/virtual-network/virtual-network-peering-overview)，安全地與其他 VNet 通訊。 對等互連虛擬網路之間的網路流量為私用。 虛擬網路之間的流量會保留在 Microsoft 骨幹網路上。

如需詳細資訊，請參閱 [Azure 網路安全性](/azure/security/azure-network-security)

### <a name="monitoring"></a>監視

[Azure 資訊安全中心](/azure/security-center/security-center-intro)會自動收集、分析及整合您 Azure 資源、網路和已連線的合作夥伴解決方案 (例如防火牆解決方案) 的記錄資料，來偵測真正的威脅並減少誤判情形。 

[Log Analytics](/azure/log-analytics/log-analytics-overview) 提供記錄的集中式存取，並幫助您分析資料與建立自訂警示。

[Azure SQL Database 威脅偵測](/azure/sql-database/sql-database-threat-detection)會偵測意圖存取或攻擊資料庫，並可能會造成損害的異常活動。 資訊安全人員或其他指定的系統管理員可以在發生可疑的資料庫活動時立即接收通知。 每個通知都會提供可疑活動的詳細資料，以及建議如何進一步調查並減輕威脅。



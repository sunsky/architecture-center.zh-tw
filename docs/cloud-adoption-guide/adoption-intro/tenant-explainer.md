---
title: 說明：什麼是 Azure Active Directory 租用戶？
description: 說明 Azure Active Directory 在 Azure 中提供身分識別即服務 (IDaaS) 的內部運作方式
author: petertay
ms.openlocfilehash: ce5a33b92047e1f360eee8fcbc7a726bcf8cd19f
ms.sourcegitcommit: 2e8b06e9c07875d65b91d5431bfd4bc465a7a242
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="explainer-what-is-an-azure-active-directory-tenant"></a>說明：什麼是 Azure Active Directory 租用戶？

在 [Azure 如何運作？](azure-explainer.md)說明文章中，您已了解 Azure 是代替使用者執行虛擬化硬體和軟體的伺服器和網路硬體的集合。 您也了解到其中有部分伺服器會執行分散式協調流程應用程式，來管理 Azure 資源的建立、讀取、更新和刪除。

不過，如您所預期的，Azure 並非允許所有人對資源執行這些作業。 Azure 會使用名為 **Azure Active Directory** (Azure AD) 的信任數位身分識別服務來限制對這些作業的存取。 Azure AD 會儲存使用者名稱、密碼、設定檔資料和其他資訊。 Azure AD 使用者會劃分到**租用戶**中。 租用戶是一種邏輯建構，代表通常與組織相關聯的 Azure AD 專用的安全執行個體。

若要建立租用戶，Azure 會要求**特殊權限帳戶**。 此特殊權限帳戶會與 Azure 帳戶或 Enterprise 合約相關聯。 這兩者都是計費建構，而不會儲存在 Azure AD 中 &mdash; 這些帳戶會儲存在高安全性的計費資料庫中。 

租用戶建立後，系統會為租用戶產生**租用戶識別碼**，並儲存在高安全性的內部 Azure AD 資料庫中。 其後，特殊權限帳戶擁有者即可登入 Azure 入口網站，並將使用者新增至新建立的 Azure AD 租用戶。 

大多數的企業至少都已有一項身分識別管理服務，通常是 Active Directory Domain Services (AD DS)。 Azure AD 能夠從 AD DS 同步處理使用者身分識別或建立其同盟，因此企業不需要分別在兩個環境中管理身分識別。 中階和進階採用階段文章將針對數位身分識別詳細說明這項功能。

## <a name="next-steps"></a>後續步驟

* 在您了解何為 Azure AD 租用戶後，基本採用階段中的第一個步驟，是要了解[如何取得 Azure Active Directory 租用戶][how-to-get-aad-tenant]。 然後，請檢閱 [Azure AD 租用戶的設計指引](tenant.md)。

<!-- Links -->
[how-to-get-aad-tenant]: /azure/active-directory/develop/active-directory-howto-tenant?toc=/azure/architecture/cloud-adoption-guide/toc.json
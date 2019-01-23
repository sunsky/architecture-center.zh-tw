---
title: 多組織用戶共享應用程式的身分識別管理
description: 在多租用戶應用程式中進行驗證、授權和身分識別管理的最佳作法。
author: MikeWasson
ms.date: 07/21/2017
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.openlocfilehash: f8875612ad6b1a71fdb6f7a768078ae599eb70b5
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54480535"
---
# <a name="manage-identity-in-multitenant-applications"></a>管理多租用戶應用程式中的身分識別

此系列文章會說明多租用戶使用 Azure AD 進行驗證和身分識別管理時的最佳做法。

[![GitHub](../_images/github.png) 程式碼範例][sample-application]

建置多租用戶應用程式時，最初會遇到管理使用者身分識別的難題，因為現在每位使用者各屬於一名租用戶。 例如︰

- 使用者透過其組織認證登入。
- 使用者應該可以存取其組織的資料，但不能存取屬於其他租用戶的資料。
- 組織可以註冊應用程式，並再將該應用程式角色指派給其成員。

Azure Active Directory (Azure AD) 有一些絕佳功能，能夠支援這些所有案例。

伴隨著這一系列的文章，我們建立了多租用戶應用程式的完整[端對端實作][ sample-application]。 文章反映出我們在建置應用程式過程中學到的知識。 若要開始使用應用程式，請參閱[執行調查應用程式][running-the-app]。

## <a name="introduction"></a>簡介

假設您正在撰寫要裝載在雲端的企業 SaaS 應用程式。 當然，應用程式將會有使用者：

![使用者](./images/users.png)

但那些使用者隸屬於組織：

![組織使用者](./images/org-users.png)

範例：Tailspin 銷售其 SaaS 應用程式的訂用帳戶。 Contoso 與 Fabrikam 註冊該應用程式。 當 Alice (`alice@contoso`) 登入時，應用程式應該會知道 Alice 隸屬於 Contoso。

- Alice *應該有* Contoso 資料的存取權。
- Alice 應該沒有 Fabrikam 資料的存取權。

此指南將向您說明如何使用 [Azure Active Directory (Azure AD)](/azure/active-directory) 處理登入與驗證，於多組織用戶共享應用程式中管理使用者身分識別。

<!-- markdownlint-disable MD026 -->

## <a name="what-is-multitenancy"></a>何謂多組織用戶管理？

<!-- markdownlint-enable MD026 -->

*租用戶* 是一群使用者。 在 SaaS 應用程式中，租用戶是應用程式的訂閱者或客戶。 *多組織用戶管理* 是多個租用戶共用同一應用程式實際執行個體的架構。 雖然租用戶共用實際資源 (例如 VM 或儲存體)，但每個租用戶都會獲得自己的應用程式邏輯執行個體。

通常，應用程式資料會在租用戶的使用者之間共用，但是不會與其他租用戶共用。

![多組織用戶共享](./images/multitenant.png)

將此架構與每個租用戶都有專用實體執行個體的單一租用戶架構比較。 在單一租用戶架構中，您可以透過執行應用程式的新執行個體來新增租用戶。

![單一租用戶](./images/single-tenant.png)

### <a name="multitenancy-and-horizontal-scaling"></a>多組織用戶管理與水平放大

若要在雲端達到某種規模，通常是新增更多的實體執行個體。 這稱為水平調整或相應放大。請考慮 Web 應用程式。 若要處理更多流量，您可以新增更多伺服器 VM 並將它們放到負載平衡器後方。 每個 VM 都會執行 Web 應用程式的個別實體執行個體。

![執行網站負載平衡](./images/load-balancing.png)

任何要求都可以路由到任何執行個體。 同時，系統也會以單一邏輯執行個體的方式運作。 您可以在不會影響使用者的情況下終止 VM 或執行新的 VM。 在這個架構中，每個實體執行個體都是多組織用戶共享，而且您可以透過新增更多執行個體來放大。 如果某個執行個體故障，應不會影響任何租用戶。

## <a name="identity-in-a-multitenant-app"></a>多組織用戶共享應用程式中的身分識別

在多組織用戶共享應用程式中，您必須考量使用者是多組織用戶共享環境中的使用者。

### <a name="authentication"></a>驗證

- 使用者透過其組織認證登入應用程式。 它們不需要針對應用程式建立新的使用者設定檔。
- 同一組織內的使用者隸屬於同一租用戶。
- 當使用者登入時，應用程式會知道使用者隸屬於哪一個租用戶。

### <a name="authorization"></a>Authorization

- 授權使用者的動作時 (例如檢視資源)，應用程式必須考量使用者隸屬的租用戶。
- 使用者可能會被指派在應用程式中的角色，例如「系統管理員」或「標準使用者」。 角色指派應由客戶管理，而不是由 SaaS 提供者管理。

**範例。** Alice 為 Contoso 的員工，使用其瀏覽器瀏覽至應用程式並按一下 [登入] 按鈕。 她被重新導向到可在其中輸入企業認證 (使用者名稱與密碼) 登入畫面。 此時，她以 `alice@contoso.com`的身分登入應用程式。 應用程式也會知道 Alice 是此應用程式的系統管理員使用者。 因為她是系統管理員，所以可以看到屬於 Contoso 之所有資源的清單。 但是，她無法檢視 Fabrikam 的資源，因為她只是其所屬租用戶中的系統管理員。

在本指南中，我們將特別探討使用 Azure AD 來管理身分識別。

- 我們假設客戶將他們的使用者設定檔儲存在 Azure AD (包括 Office365 和 Dynamics CRM 租用戶)
- 使用內部部署 Active Directory (AD) 的客戶可以使用 [Azure AD Connect](/azure/active-directory/hybrid/whatis-hybrid-identity) 來同步其內部部署 AD 與 Azure AD。

如果擁有內部部署 AD 的客戶無法使用 Azure AD Connect (因為公司 IT 原則或其他原因)，SaaS 提供者可以透過 Active Directory Federation Services (AD FS) 與客戶的 AD 聯合。 此選項已在 [與客戶的 AD FS 聯合](adfs.md)中說明。

本指南不考量多組織用戶管理的其他層面，例如參與、個別租用戶設定等等。

[**下一步**](./tailspin.md)

<!-- links -->

[sample-application]: https://github.com/mspnp/multitenant-saas-guidance
[running-the-app]: ./run-the-app.md

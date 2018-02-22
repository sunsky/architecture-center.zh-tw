---
title: "指引：Azure AD 租用戶設計"
description: "依據基本雲端採用策略設計 Azure 租用戶的指引"
author: telmosampaio
ms.openlocfilehash: 5bf710f74bde04e041f2896b4a638c3513e4aa44
ms.sourcegitcommit: 2e8b06e9c07875d65b91d5431bfd4bc465a7a242
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="guidance-azure-ad-tenant-design"></a>指引：Azure AD 租用戶設計

Azure AD 租用戶提供可用於一或多個 [Azure 訂用帳戶](subscription-explainer.md)的數位身分識別服務和命名空間。 如果您了解基本採用大綱，您即已了解[如何取得 Azure AD 租用戶][how-to-get-aad-tenant]。 

## <a name="design-considerations"></a>設計考量

- 在基本採用階段中，您可以從單一 Azure AD 租用戶著手。 如果您的組織已有 Office 365 訂用帳戶或 Azure 訂用帳戶，您就已有 Azure AD 租用戶可供使用。 如果都沒有這兩種訂用帳戶，您可以深入了解[如何取得 Azure AD 租用戶][how-to-get-aad-tenant]。 
- 在中階和進階採用階段中，您將了解如何同步處理內部部署目錄與 Azure AD 或建立其同盟。 這可讓您在 Azure AD 中使用內部部署的數位身分識別。 但在基本階段中，您將會新增僅具有單一 Azure AD 租用戶之身分識別的新使用者。 您須負責管理這些身分識別。 例如，您將必須登入新的 Azure AD 使用者、登出您不想再為其提供 Azure 資源存取的 Azure AD 使用者，以及對使用者權限進行其他變更。

## <a name="next-steps"></a>後續步驟

* 現在您已有 Azure AD 租用戶，接下來請了解[如何新增使用者][azure-ad-add-user]。 在您將一或多個新使用者新增至 Azure AD 租用戶之後，接下來您應了解 [Azure 訂用帳戶](subscription-explainer.md)。

<!-- Links -->

[azure-ad-add-user]: /azure/active-directory/add-users-azure-active-directory?toc=/azure/architecture/cloud-adoption-guide/toc.json
[docs-manage-azure-ad]: /azure/active-directory/active-directory-administer?toc=/azure/architecture/cloud-adoption-guide/toc.json
[docs-tenant]: /azure/active-directory/develop/active-directory-howto-tenant?toc=/azure/architecture/cloud-adoption-guide/toc.json
[docs-associate-subscription]: /azure/active-directory/active-directory-how-subscriptions-associated-directory?toc=/azure/architecture/cloud-adoption-guide/toc.json
[how-to-get-aad-tenant]: /azure/active-directory/develop/active-directory-howto-tenant?toc=/azure/architecture/cloud-adoption-guide/toc.json
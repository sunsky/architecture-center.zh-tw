---
title: 說明：什麼是 Azure 訂用帳戶？
description: 說明 Azure 訂用帳戶、帳戶和供應項目
author: alexbuckgit
ms.openlocfilehash: 1650d90d6f78b46b7fe4128d2dab6a80bd6cca78
ms.sourcegitcommit: 3d9ee03e2dda23753661a80c7106d1789f5223bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2018
ms.locfileid: "29478301"
---
# <a name="explainer-what-is-an-azure-subscription"></a>說明：什麼是 Azure 訂用帳戶？

在[什麼是 Azure Active Directory 租用戶？](tenant-explainer.md)說明文章中，您已了解組織的數位身分識別會儲存在 Azure Active Directory 租用戶中。 您也了解到 Azure 會仰賴 Azure Active Directory 來驗證使用者建立、讀取、更新或刪除資源的要求。 

我們基本上已了解為何需要限制對這些資源作業的存取 - 為了防止未經驗證和未經授權的使用者存取我們的資源。 不過，這些資源作業還有其他內容是組織會想要控制的，例如，使用者或使用者群組可建立的資源數目，以及執行這些資源的成本。 

Azure 會實作這項控制，其名稱為**訂用帳戶**。 訂用帳戶會將使用者以及這些使用者所建立的資源歸類為同一個群組。 其中的每一項資源都會計入該項資源的[整體限制][subscription-service-limits]。

組織可以使用訂用帳戶來管理成本以及使用者、小組、專案的資源建立，或使用多種其他策略。 這些策略將在中階和進階採用階段文章中討論。 

## <a name="next-steps"></a>後續步驟

* 現在您已了解何為 Azure 訂用帳戶，在您建立第一項 Azure 資源之前，請先深入了解如何[建立訂用帳戶](subscription.md)。

<!-- Links -->
[azure-get-started]: https://azure.microsoft.com/get-started/
[azure-offers]: https://azure.microsoft.com/support/legal/offer-details/
[azure-free-trial]: https://azure.microsoft.com/offers/ms-azr-0044p/
[azure-change-subscription-offer]: /azure/billing/billing-how-to-switch-azure-offer
[microsoft-account]: https://account.microsoft.com/account
[subscription-service-limits]: /azure/azure-subscription-service-limits
[docs-organizational-account]: https://docs.microsoft.com/azure/active-directory/sign-up-organization

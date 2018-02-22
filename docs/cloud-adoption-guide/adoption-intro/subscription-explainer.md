---
title: "說明：什麼是 Azure 訂用帳戶？"
description: "說明 Azure 訂用帳戶、帳戶和供應項目"
author: abuck
ms.openlocfilehash: 7b88e9489b40b100eecb76602b45901566b3f37f
ms.sourcegitcommit: 2e8b06e9c07875d65b91d5431bfd4bc465a7a242
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="explainer-what-is-an-azure-subscription"></a><span data-ttu-id="f3722-103">說明：什麼是 Azure 訂用帳戶？</span><span class="sxs-lookup"><span data-stu-id="f3722-103">Explainer: What is an Azure subscription?</span></span>

<span data-ttu-id="f3722-104">在[什麼是 Azure Active Directory 租用戶？](tenant-explainer.md)說明文章中，您已了解組織的數位身分識別會儲存在 Azure Active Directory 租用戶中。</span><span class="sxs-lookup"><span data-stu-id="f3722-104">In the [what is an Azure Active Directory tenant?](tenant-explainer.md) explainer article, you learned that digital identity for your organization is stored in an Azure Active Directory tenant.</span></span> <span data-ttu-id="f3722-105">您也了解到 Azure 會仰賴 Azure Active Directory 來驗證使用者建立、讀取、更新或刪除資源的要求。</span><span class="sxs-lookup"><span data-stu-id="f3722-105">You also learned that Azure trusts Azure Active Directory to authenticate user requests to create, read, update, or delete a resource.</span></span> 

<span data-ttu-id="f3722-106">我們基本上已了解為何需要限制對這些資源作業的存取 - 為了防止未經驗證和未經授權的使用者存取我們的資源。</span><span class="sxs-lookup"><span data-stu-id="f3722-106">We fundamentally understand why it's necessary to restrict access to these operations on a resource - to prevent unauthenticated and unauthorized users from accessing our resources.</span></span> <span data-ttu-id="f3722-107">不過，這些資源作業還有其他內容是組織會想要控制的，例如，使用者或使用者群組可建立的資源數目，以及執行這些資源的成本。</span><span class="sxs-lookup"><span data-stu-id="f3722-107">However, these resource operations have other properties that an organization would like to control, such as the number of resources a user or group of users is allowed to create, and, the cost to run those resources.</span></span> 

<span data-ttu-id="f3722-108">Azure 會實作這項控制，其名稱為**訂用帳戶**。</span><span class="sxs-lookup"><span data-stu-id="f3722-108">Azure implements this control, and it is named a **subscription**.</span></span> <span data-ttu-id="f3722-109">訂用帳戶會將使用者以及這些使用者所建立的資源歸類為同一個群組。</span><span class="sxs-lookup"><span data-stu-id="f3722-109">A subscription groups together users and the resources that have been created by those users.</span></span> <span data-ttu-id="f3722-110">其中的每一項資源都會計入該項資源的[整體限制][subscription-service-limits]。</span><span class="sxs-lookup"><span data-stu-id="f3722-110">Each of those resources contributes to an [overall limit][subscription-service-limits] on that particular resource.</span></span>

<span data-ttu-id="f3722-111">組織可以使用訂用帳戶來管理成本以及使用者、小組、專案的資源建立，或使用多種其他策略。</span><span class="sxs-lookup"><span data-stu-id="f3722-111">Organizations can use subscriptions to manage costs and creation of resource by users, teams, projects, or using many other strategies.</span></span> <span data-ttu-id="f3722-112">這些策略將在中階和進階採用階段文章中討論。</span><span class="sxs-lookup"><span data-stu-id="f3722-112">These strategies will be discussed in the intermediate and advanced adoption stage articles.</span></span> 

## <a name="next-steps"></a><span data-ttu-id="f3722-113">後續步驟</span><span class="sxs-lookup"><span data-stu-id="f3722-113">Next steps</span></span>

* <span data-ttu-id="f3722-114">現在您已了解何為 Azure 訂用帳戶，在您建立第一項 Azure 資源之前，請先深入了解如何[建立訂用帳戶](subscription.md)。</span><span class="sxs-lookup"><span data-stu-id="f3722-114">Now that you have learned about Azure subscriptions, learn more about [creating a subscription](subscription.md) before you create your first Azure resources..</span></span>

<!-- Links -->
[azure-get-started]: https://azure.microsoft.com/en-us/get-started/
[azure-offers]: https://azure.microsoft.com/en-us/support/legal/offer-details/
[azure-free-trial]: https://azure.microsoft.com/en-us/offers/ms-azr-0044p/
[azure-change-subscription-offer]: /azure/billing/billing-how-to-switch-azure-offer
[microsoft-account]: https://account.microsoft.com/account
[subscription-service-limits]: /azure/azure-subscription-service-limits
[docs-organizational-account]: https://docs.microsoft.com/en-us/azure/active-directory/sign-up-organization

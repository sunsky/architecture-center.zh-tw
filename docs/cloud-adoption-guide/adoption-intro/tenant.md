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
# <a name="guidance-azure-ad-tenant-design"></a><span data-ttu-id="d1dce-103">指引：Azure AD 租用戶設計</span><span class="sxs-lookup"><span data-stu-id="d1dce-103">Guidance: Azure AD tenant design</span></span>

<span data-ttu-id="d1dce-104">Azure AD 租用戶提供可用於一或多個 [Azure 訂用帳戶](subscription-explainer.md)的數位身分識別服務和命名空間。</span><span class="sxs-lookup"><span data-stu-id="d1dce-104">An Azure AD tenant provides digital identity services and namespaces used for one or more [Azure subscriptions](subscription-explainer.md).</span></span> <span data-ttu-id="d1dce-105">如果您了解基本採用大綱，您即已了解[如何取得 Azure AD 租用戶][how-to-get-aad-tenant]。</span><span class="sxs-lookup"><span data-stu-id="d1dce-105">If you are following the foundational adoption outline, you have already learned [how to get an Azure AD tenant][how-to-get-aad-tenant].</span></span> 

## <a name="design-considerations"></a><span data-ttu-id="d1dce-106">設計考量</span><span class="sxs-lookup"><span data-stu-id="d1dce-106">Design considerations</span></span>

- <span data-ttu-id="d1dce-107">在基本採用階段中，您可以從單一 Azure AD 租用戶著手。</span><span class="sxs-lookup"><span data-stu-id="d1dce-107">At the foundational adoption stage, you can begin with a single Azure AD tenant.</span></span> <span data-ttu-id="d1dce-108">如果您的組織已有 Office 365 訂用帳戶或 Azure 訂用帳戶，您就已有 Azure AD 租用戶可供使用。</span><span class="sxs-lookup"><span data-stu-id="d1dce-108">If your organization has an existing Office 365 subscription or an Azure subscription, you already have an Azure AD tenant that you can use.</span></span> <span data-ttu-id="d1dce-109">如果都沒有這兩種訂用帳戶，您可以深入了解[如何取得 Azure AD 租用戶][how-to-get-aad-tenant]。</span><span class="sxs-lookup"><span data-stu-id="d1dce-109">If you do not have either or of these, you can learn more about [how to get an Azure AD tenant][how-to-get-aad-tenant].</span></span> 
- <span data-ttu-id="d1dce-110">在中階和進階採用階段中，您將了解如何同步處理內部部署目錄與 Azure AD 或建立其同盟。</span><span class="sxs-lookup"><span data-stu-id="d1dce-110">In the intermediate and advanced adoption stages, you will learn how to synchronize or federate on-premises directories with Azure AD.</span></span> <span data-ttu-id="d1dce-111">這可讓您在 Azure AD 中使用內部部署的數位身分識別。</span><span class="sxs-lookup"><span data-stu-id="d1dce-111">This will allow you to use on-premises digital identity in Azure AD.</span></span> <span data-ttu-id="d1dce-112">但在基本階段中，您將會新增僅具有單一 Azure AD 租用戶之身分識別的新使用者。</span><span class="sxs-lookup"><span data-stu-id="d1dce-112">However, at the foundational stage, you will be adding new users that only have identity your single Azure AD tenant.</span></span> <span data-ttu-id="d1dce-113">您須負責管理這些身分識別。</span><span class="sxs-lookup"><span data-stu-id="d1dce-113">You are be responsible for managing those identities.</span></span> <span data-ttu-id="d1dce-114">例如，您將必須登入新的 Azure AD 使用者、登出您不想再為其提供 Azure 資源存取的 Azure AD 使用者，以及對使用者權限進行其他變更。</span><span class="sxs-lookup"><span data-stu-id="d1dce-114">For example, you will have to on-board new Azure AD users, off-board Azure AD users that you no longer wish to have access to Azure resources, and other changes to user permissions.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d1dce-115">後續步驟</span><span class="sxs-lookup"><span data-stu-id="d1dce-115">Next Steps</span></span>

* <span data-ttu-id="d1dce-116">現在您已有 Azure AD 租用戶，接下來請了解[如何新增使用者][azure-ad-add-user]。</span><span class="sxs-lookup"><span data-stu-id="d1dce-116">Now that you have an Azure AD tenant, learn [how to add a user][azure-ad-add-user].</span></span> <span data-ttu-id="d1dce-117">在您將一或多個新使用者新增至 Azure AD 租用戶之後，接下來您應了解 [Azure 訂用帳戶](subscription-explainer.md)。</span><span class="sxs-lookup"><span data-stu-id="d1dce-117">After you have added one or more new users to your Azure AD tenant, your next step is learning about [Azure subscriptions](subscription-explainer.md).</span></span>

<!-- Links -->

[azure-ad-add-user]: /azure/active-directory/add-users-azure-active-directory?toc=/azure/architecture/cloud-adoption-guide/toc.json
[docs-manage-azure-ad]: /azure/active-directory/active-directory-administer?toc=/azure/architecture/cloud-adoption-guide/toc.json
[docs-tenant]: /azure/active-directory/develop/active-directory-howto-tenant?toc=/azure/architecture/cloud-adoption-guide/toc.json
[docs-associate-subscription]: /azure/active-directory/active-directory-how-subscriptions-associated-directory?toc=/azure/architecture/cloud-adoption-guide/toc.json
[how-to-get-aad-tenant]: /azure/active-directory/develop/active-directory-howto-tenant?toc=/azure/architecture/cloud-adoption-guide/toc.json
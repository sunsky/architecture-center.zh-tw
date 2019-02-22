---
title: CAF：Azure 中的資源存取管理
description: Azure 中資源存取權管理建構的說明：Azure Resource Manager、訂用帳戶、資源群組和資源。
author: petertaylor9999
ms.date: 2/11/2019
ms.openlocfilehash: f23540a03c82fbc46872645ac0fd82d574db353a
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55898114"
---
# <a name="resource-access-management-in-azure"></a><span data-ttu-id="959e9-103">Azure 中的資源存取管理</span><span class="sxs-lookup"><span data-stu-id="959e9-103">Resource access management in Azure</span></span>

<span data-ttu-id="959e9-104">在[資源治理是什麼？](what-is-governance.md)中，您已了解治理指的是進行中的程序，用以管理、監視及稽核 Azure 資源的使用符合組織目標和需求。</span><span class="sxs-lookup"><span data-stu-id="959e9-104">In [what is resource governance?](what-is-governance.md), you learned that governance refers to the ongoing process of managing, monitoring, and auditing the use of Azure resources to meet the goals and requirements of your organization.</span></span> <span data-ttu-id="959e9-105">在您繼續了解如何設計治理模型之前，務必了解 Azure 中的資源存取權管理控制項。</span><span class="sxs-lookup"><span data-stu-id="959e9-105">Before you move on to learn how to design a governance model, it's important to understand the resource access management controls in Azure.</span></span> <span data-ttu-id="959e9-106">這些資源存取權管理控制項的組態會形成治理模型的基礎。</span><span class="sxs-lookup"><span data-stu-id="959e9-106">The configuration of these resource access management controls forms the basis of your governance model.</span></span>

<span data-ttu-id="959e9-107">一開始請先仔細看看資源在 Azure 中的部署方式。</span><span class="sxs-lookup"><span data-stu-id="959e9-107">Let's begin by taking a closer look at how resources are deployed in Azure.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="what-is-an-azure-resource"></a><span data-ttu-id="959e9-108">什麼是 Azure 資源？</span><span class="sxs-lookup"><span data-stu-id="959e9-108">What is an Azure resource?</span></span>

<span data-ttu-id="959e9-109">在 Azure 中，**資源**一詞是指由 Azure 管理的實體。</span><span class="sxs-lookup"><span data-stu-id="959e9-109">In Azure, the term **resource** refers to an entity managed by Azure.</span></span> <span data-ttu-id="959e9-110">例如，虛擬機器、虛擬網路以及儲存體帳戶都是 Azure 資源。</span><span class="sxs-lookup"><span data-stu-id="959e9-110">For example, virtual machines, virtual networks, and storage accounts are all referred to as Azure resources.</span></span>

<span data-ttu-id="959e9-111">![](../_images/governance-1-9.png)
*圖 1.資源。*</span><span class="sxs-lookup"><span data-stu-id="959e9-111">![](../_images/governance-1-9.png)
*Figure 1. A resource.*</span></span>

## <a name="what-is-an-azure-resource-group"></a><span data-ttu-id="959e9-112">什麼是 Azure 資源群組？</span><span class="sxs-lookup"><span data-stu-id="959e9-112">What is an Azure resource group?</span></span>

<span data-ttu-id="959e9-113">Azure 中的每個資源都必須屬於一個[資源群組](/azure/azure-resource-manager/resource-group-overview#resource-groups)。</span><span class="sxs-lookup"><span data-stu-id="959e9-113">Each resource in Azure must belong to a [resource group](/azure/azure-resource-manager/resource-group-overview#resource-groups).</span></span> <span data-ttu-id="959e9-114">資源群組是只是一個邏輯建構，將多個資源群組在一起，系統就能以單一實體的形式來管理這些資源。</span><span class="sxs-lookup"><span data-stu-id="959e9-114">A resource group is simply a logical construct that groups multiple resources together so they can be managed as a single entity.</span></span> <span data-ttu-id="959e9-115">例如，共用類似生命週期的資源如[多層式架構應用程式](/azure/architecture/guide/architecture-styles/n-tier)的資源，能以群組形式建立或刪除。</span><span class="sxs-lookup"><span data-stu-id="959e9-115">For example, resources that share a similar lifecycle, such as the resources for an [n-tier application](/azure/architecture/guide/architecture-styles/n-tier) may be created or deleted as a group.</span></span>

<span data-ttu-id="959e9-116">![](../_images/governance-1-10.png)
*圖 2.資源群組包含資源。*</span><span class="sxs-lookup"><span data-stu-id="959e9-116">![](../_images/governance-1-10.png)
*Figure 2. A resource group contains a resource.*</span></span>

<span data-ttu-id="959e9-117">資源群組及其所包含的資源會與 Azure **訂用帳戶**相關聯。</span><span class="sxs-lookup"><span data-stu-id="959e9-117">Resource groups and the resources they contain are associated with an Azure **subscription**.</span></span>

## <a name="what-is-an-azure-subscription"></a><span data-ttu-id="959e9-118">什麼是 Azure 訂用帳戶？</span><span class="sxs-lookup"><span data-stu-id="959e9-118">What is an Azure subscription?</span></span>

<span data-ttu-id="959e9-119">Azure 訂用帳戶類似於資源群組，它是一個邏輯建構，將資源群組及其資源群組在一起。</span><span class="sxs-lookup"><span data-stu-id="959e9-119">An Azure subscription is similar to a resource group in that it's a logical construct that groups together resource groups and their resources.</span></span> <span data-ttu-id="959e9-120">不過，Azure 訂用帳戶也與 Azure Resource Manager 所使用的控制項相關聯。</span><span class="sxs-lookup"><span data-stu-id="959e9-120">However, an Azure subscription is also associated with the controls used by Azure Resource Manager.</span></span> <span data-ttu-id="959e9-121">這代表什麼？</span><span class="sxs-lookup"><span data-stu-id="959e9-121">What does this mean?</span></span> <span data-ttu-id="959e9-122">讓我們看看 Azure Resource Manager，以了解它與 Azure 訂用帳戶之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="959e9-122">Let's take a closer look at Resource Manager to learn about the relationship between it and an Azure subscription.</span></span>

<span data-ttu-id="959e9-123">![](../_images/governance-1-11.png)
*圖 3.Azure 訂用帳戶。*</span><span class="sxs-lookup"><span data-stu-id="959e9-123">![](../_images/governance-1-11.png)
*Figure 3. An Azure subscription.*</span></span>

## <a name="what-is-azure-resource-manager"></a><span data-ttu-id="959e9-124">什麼是 Azure Resource Manager？</span><span class="sxs-lookup"><span data-stu-id="959e9-124">What is Azure Resource Manager?</span></span>

<span data-ttu-id="959e9-125">在 [Azure 如何運作？](what-is-azure.md)中，您已了解 Azure 包含「前端」與會協調 Azure 所有函式的多個服務。</span><span class="sxs-lookup"><span data-stu-id="959e9-125">In [how does Azure work?](what-is-azure.md) you learned that Azure includes a "front end" with many services that orchestrate all the functions of Azure.</span></span> <span data-ttu-id="959e9-126">這些服務的其中之一就是 [Azure Resource Manager](/azure/azure-resource-manager/)，這個服務會裝載用戶端用來管理資源的 RESTful API。</span><span class="sxs-lookup"><span data-stu-id="959e9-126">One of these services is [Resource Manager](/azure/azure-resource-manager/), and this service hosts the RESTful API used by clients to manage resources.</span></span>

<span data-ttu-id="959e9-127">![](../_images/governance-1-12.png)
*圖 4.Azure Resource Manager。*</span><span class="sxs-lookup"><span data-stu-id="959e9-127">![](../_images/governance-1-12.png)
*Figure 4. Azure resource manager.*</span></span>

<span data-ttu-id="959e9-128">下圖顯示三個用戶端：[PowerShell](/powershell/azure/overview)、[Azure 入口網站](https://portal.azure.com)和 [Azure 命令列介面 (CLI)](/cli/azure)：</span><span class="sxs-lookup"><span data-stu-id="959e9-128">The following figure shows three clients: [PowerShell](/powershell/azure/overview), the [Azure portal](https://portal.azure.com), and the [Azure command line interface (CLI)](/cli/azure):</span></span>

<span data-ttu-id="959e9-129">![](../_images/governance-1-13.png)
*圖 5.Azure 用戶端會連線到 Resource Manager RESTful API。*</span><span class="sxs-lookup"><span data-stu-id="959e9-129">![](../_images/governance-1-13.png)
*Figure 5. Azure clients connect to the Resource Manager RESTful API.*</span></span>

<span data-ttu-id="959e9-130">雖然這些用戶端會使用 RESTful API 連線到 Resource Manager，但 Resource Manager 不包含直接管理資源的功能。</span><span class="sxs-lookup"><span data-stu-id="959e9-130">While these clients connect to Resource Manager using the RESTful API, Resource Manager does not include functionality to manage resources directly.</span></span> <span data-ttu-id="959e9-131">相反地，Azure 中的大多數資源類型都有自己的[**資源提供者**](/azure/azure-resource-manager/resource-group-overview#terminology)。</span><span class="sxs-lookup"><span data-stu-id="959e9-131">Rather, most resource types in Azure have their own [**resource provider**](/azure/azure-resource-manager/resource-group-overview#terminology).</span></span>

<span data-ttu-id="959e9-132">\*
*圖 6.Azure 資源提供者。*</span><span class="sxs-lookup"><span data-stu-id="959e9-132">![](../_images/governance-1-14.png)
*Figure 6. Azure resource providers.*</span></span>

<span data-ttu-id="959e9-133">當用戶端要求管理特定資源時，Azure Resource Manager 會連線到該資源類型的資源提供者，來完成要求。</span><span class="sxs-lookup"><span data-stu-id="959e9-133">When a client makes a request to manage a specific resource, Resource Manager connects to the resource provider for that resource type to complete the request.</span></span> <span data-ttu-id="959e9-134">例如，如果用戶端要求管理虛擬機器資源，Resource Manager 會連線到 **Microsoft.compute** 資源提供者。</span><span class="sxs-lookup"><span data-stu-id="959e9-134">For example, if a client makes a request to manage a virtual machine resource, Resource Manager connects to the **Microsoft.Compute** resource provider.</span></span>

<span data-ttu-id="959e9-135">![](../_images/governance-1-15.png)
*圖 7.Resource Manager 會連線到 **Microsoft.compute** 資源提供者，來管理用戶端要求中指定的資源。*</span><span class="sxs-lookup"><span data-stu-id="959e9-135">![](../_images/governance-1-15.png)
*Figure 7. Resource Manager connects to the **Microsoft.Compute** resource provider to manage the resource specified in the client request.*</span></span>

<span data-ttu-id="959e9-136">Resource Manager 需要用戶端同時指定訂用帳戶和資源群組的識別碼，才能管理虛擬機器資源。</span><span class="sxs-lookup"><span data-stu-id="959e9-136">Resource Manager requires the client to specify an identifier for both the subscription and the resource group in order to manage the virtual machine resource.</span></span>

<span data-ttu-id="959e9-137">既然您已了解 Resource Manager 如何運作，讓我們回來討論 Azure 訂用帳戶如何與 Azure 資源管理員所使用的控制項相關聯。</span><span class="sxs-lookup"><span data-stu-id="959e9-137">Now that you have an understanding of how Resource Manager works, let's return to our discussion of how an Azure subscription is associated with the controls used by Azure resource manager.</span></span> <span data-ttu-id="959e9-138">在 Resource Manager 可以執行任何資源管理要求之前，會先檢查一組控制項。</span><span class="sxs-lookup"><span data-stu-id="959e9-138">Before any resource management request can be executed by Resource Manager, a set of controls are checked.</span></span>

<span data-ttu-id="959e9-139">第一個控制項是要求必須由已驗證使用者提出，而 Resource Manager 具有與 [Azure Active Directory (Azure AD)](/azure/active-directory/) 的信任關係，可以提供使用者身分識別功能。</span><span class="sxs-lookup"><span data-stu-id="959e9-139">The first control is that a request must be made by a validated user, and Resource Manager has a trusted relationship with [Azure Active Directory](/azure/active-directory/) (Azure AD) to provide user identity functionality.</span></span>

<span data-ttu-id="959e9-140">![](../_images/governance-1-16.png)
*圖 8.Azure Active Directory。*</span><span class="sxs-lookup"><span data-stu-id="959e9-140">![](../_images/governance-1-16.png)
*Figure 8. Azure Active Directory.*</span></span>

<span data-ttu-id="959e9-141">在 Azure AD 中，使用者會劃分到不同**租用戶**中。</span><span class="sxs-lookup"><span data-stu-id="959e9-141">In Azure AD, users are segmented into **tenants**.</span></span> <span data-ttu-id="959e9-142">租用戶是一種邏輯建構，代表通常與組織相關聯的 Azure AD 專用的安全執行個體。</span><span class="sxs-lookup"><span data-stu-id="959e9-142">A tenant is a logical construct that represents a secure, dedicated instance of Azure AD typically associated with an organization.</span></span> <span data-ttu-id="959e9-143">每個訂用帳戶都會與 Azure AD 租用戶相關聯。</span><span class="sxs-lookup"><span data-stu-id="959e9-143">Each subscription is associated with an Azure AD tenant.</span></span>

<span data-ttu-id="959e9-144">![](../_images/governance-1-17.png)
*圖 9.與訂用帳戶相關聯的 Azure AD 租用戶。*</span><span class="sxs-lookup"><span data-stu-id="959e9-144">![](../_images/governance-1-17.png)
*Figure 9. An Azure AD tenant associated with a subscription.*</span></span>

<span data-ttu-id="959e9-145">對於要在特定訂用帳戶中管理資源的每個用戶端要求，都需要使用者在相關聯的 Azure AD 租用戶中具有帳戶。</span><span class="sxs-lookup"><span data-stu-id="959e9-145">Each client request to manage a resource in a particular subscription requires that the user has an account in the associated Azure AD tenant.</span></span>

<span data-ttu-id="959e9-146">下一個控制項會檢查使用者具有足夠權限可以提出要求。</span><span class="sxs-lookup"><span data-stu-id="959e9-146">The next control is a check that the user has sufficient permission to make the request.</span></span> <span data-ttu-id="959e9-147">您要使用[角色型存取控制 (RBAC)](/azure/role-based-access-control/) 將權限指派給使用者。</span><span class="sxs-lookup"><span data-stu-id="959e9-147">Permissions are assigned to users using [role-based access control (RBAC)](/azure/role-based-access-control/).</span></span>

<span data-ttu-id="959e9-148">![](../_images/governance-1-18.png)
*圖 10.租用戶中的每個使用者都會獲得指派一或多個 RBAC 角色。*</span><span class="sxs-lookup"><span data-stu-id="959e9-148">![](../_images/governance-1-18.png)
*Figure 10. Each user in the tenant is assigned one or more RBAC roles.*</span></span>

<span data-ttu-id="959e9-149">RBAC 角色指定了一組權限，使用者可能會在特定資源上採用那些權限。</span><span class="sxs-lookup"><span data-stu-id="959e9-149">An RBAC role specifies a set of permissions a user may take on a specific resource.</span></span> <span data-ttu-id="959e9-150">當角色指派給使用者時，會套用這些權限。</span><span class="sxs-lookup"><span data-stu-id="959e9-150">When the role is assigned to the user, those permissions are applied.</span></span> <span data-ttu-id="959e9-151">例如，[內建**擁有者**角色](/azure/role-based-access-control/built-in-roles#owner)可讓使用者對資源執行任何動作。</span><span class="sxs-lookup"><span data-stu-id="959e9-151">For example, the [built-in **owner** role](/azure/role-based-access-control/built-in-roles#owner) allows a user to perform any action on a resource.</span></span>

<span data-ttu-id="959e9-152">下一個控制項會檢查在針對 [Azure 資源原則](/azure/governance/policy/)指定的設定下，是否允許要求。</span><span class="sxs-lookup"><span data-stu-id="959e9-152">The next control is a check that the request is allowed under the settings specified for [Azure resource policy](/azure/governance/policy/).</span></span> <span data-ttu-id="959e9-153">Azure 資源原則會指定對特定資源允許的作業。</span><span class="sxs-lookup"><span data-stu-id="959e9-153">Azure resource policies specify the operations allowed for a specific resource.</span></span> <span data-ttu-id="959e9-154">例如，Azure 資源原則可以指定只允許使用者部署特定類型的虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="959e9-154">For example, an Azure resource policy can specify that users are only allowed to deploy a specific type of virtual machine.</span></span>

<span data-ttu-id="959e9-155">![](../_images/governance-1-19.png)
*圖 11.Azure 資源原則。*</span><span class="sxs-lookup"><span data-stu-id="959e9-155">![](../_images/governance-1-19.png)
*Figure 11. Azure resource policy.*</span></span>

<span data-ttu-id="959e9-156">下一個控制項會檢查要求不超過 [Azure 訂用帳戶限制](/azure/azure-subscription-service-limits)。</span><span class="sxs-lookup"><span data-stu-id="959e9-156">The next control is a check that the request does not exceed an [Azure subscription limit](/azure/azure-subscription-service-limits).</span></span> <span data-ttu-id="959e9-157">例如，每個訂用帳戶的資源群組上限為 980 個。</span><span class="sxs-lookup"><span data-stu-id="959e9-157">For example, each subscription has a limit of 980 resource groups per subscription.</span></span> <span data-ttu-id="959e9-158">如果收到的部署額外資源群組要求達到上限，就會遭到拒絕。</span><span class="sxs-lookup"><span data-stu-id="959e9-158">If a request is received to deploy an additional resource group once the limit has been reached, it is denied.</span></span>

<span data-ttu-id="959e9-159">![](../_images/governance-1-20.png)
*圖 12.Azure 資源限制。*</span><span class="sxs-lookup"><span data-stu-id="959e9-159">![](../_images/governance-1-20.png)
*Figure 12. Azure resource limits.*</span></span>

<span data-ttu-id="959e9-160">最後一個控制項會檢查要求仍在與訂用帳戶相關聯的財務承諾之內。</span><span class="sxs-lookup"><span data-stu-id="959e9-160">The final control is a check that the request is within the financial commitment associated with the subscription.</span></span> <span data-ttu-id="959e9-161">例如，如果要求是要部署虛擬機器，Resource Manager 會確認訂用帳戶具有足夠的付款資訊。</span><span class="sxs-lookup"><span data-stu-id="959e9-161">For example, if the request is to deploy a virtual machine, Resource Manager verifies that the subscription has sufficient payment information.</span></span>

<span data-ttu-id="959e9-162">![](../_images/governance-1-21.png)
*圖 13.財務承諾與訂用帳戶相關聯。*</span><span class="sxs-lookup"><span data-stu-id="959e9-162">![](../_images/governance-1-21.png)
*Figure 13. A financial commitment is associated with a subscription.*</span></span>

## <a name="summary"></a><span data-ttu-id="959e9-163">總結</span><span class="sxs-lookup"><span data-stu-id="959e9-163">Summary</span></span>

<span data-ttu-id="959e9-164">在本文中，您已了解如何使用 Azure Resource Manager 在 Azure 中管理資源存取權。</span><span class="sxs-lookup"><span data-stu-id="959e9-164">In this article, you learned about how resource access is managed in Azure using Azure Resource Manager.</span></span>

## <a name="next-steps"></a><span data-ttu-id="959e9-165">後續步驟</span><span class="sxs-lookup"><span data-stu-id="959e9-165">Next steps</span></span>

<span data-ttu-id="959e9-166">既然您已了解如何在 Azure 中管理資源存取權，請繼續了解如何設計治理模型。</span><span class="sxs-lookup"><span data-stu-id="959e9-166">Now that you understand how resource access is managed in Azure, move on to learn how to design a governance model.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="959e9-167">雲端治理</span><span class="sxs-lookup"><span data-stu-id="959e9-167">Cloud governance</span></span>](../governance/overview.md)

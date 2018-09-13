---
title: 企業雲端採用：簡單工作負載的治理設計
description: 設定 Azure 治理控制，讓使用者部署簡單工作負載的指導方針
author: petertaylor9999
ms.date: 09/10/2018
ms.openlocfilehash: 13c7c4b41df14151d28b9c685f01019af3ec63f2
ms.sourcegitcommit: c49aeef818d7dfe271bc4128b230cfc676f05230
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2018
ms.locfileid: "44389242"
---
# <a name="enterprise-cloud-adoption-governance-design-for-a-simple-workload"></a><span data-ttu-id="e577a-103">企業雲端採用：簡單工作負載的治理設計</span><span class="sxs-lookup"><span data-stu-id="e577a-103">Enterprise Cloud Adoption: Governance design for a simple workload</span></span>

<span data-ttu-id="e577a-104">本指南的目標是協助您了解在 Azure 中設計資源治理模型的程序，以支援單一小組和簡單工作負載。</span><span class="sxs-lookup"><span data-stu-id="e577a-104">The goal of this guidance is to help you learn the process for designing a resource governance model in Azure to support a single team and a simple workload.</span></span>  <span data-ttu-id="e577a-105">我們會查看一組假設性治理需求，然後瀏覽滿足這些需求的數個範例實作。</span><span class="sxs-lookup"><span data-stu-id="e577a-105">We'll look at a set of hypothetical governance requirements, then go through several example implementations that satisfy those requirements.</span></span> 

<span data-ttu-id="e577a-106">在基礎採用階段中，我們的目標是將簡單工作負載部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="e577a-106">In the foundational adoption stage, our goal is to deploy a simple workload to Azure.</span></span> <span data-ttu-id="e577a-107">這會導致下列需求：</span><span class="sxs-lookup"><span data-stu-id="e577a-107">This results in the following requirements:</span></span>
* <span data-ttu-id="e577a-108">單一**工作負載擁有者**的身分識別管理，該擁有者負責部署和維護簡單工作負載。</span><span class="sxs-lookup"><span data-stu-id="e577a-108">Identity management for a single **workload owner** who is responsible for deploying and maintaining the simple workload.</span></span> <span data-ttu-id="e577a-109">工作負載擁有者需要建立、讀取、更新和刪除資源的權限，以及將這些權限委派給身分識別管理系統中其他使用者的權限。</span><span class="sxs-lookup"><span data-stu-id="e577a-109">The workload owner requires permission to create, read, update, and delete resources as well as permission to delegate these rights to other users in the identity management system.</span></span>
* <span data-ttu-id="e577a-110">以單一管理單位的形式管理簡單工作負載的所有資源。</span><span class="sxs-lookup"><span data-stu-id="e577a-110">Manage all resources for the simple workload as a single management unit.</span></span>

## <a name="licensing-azure"></a><span data-ttu-id="e577a-111">授權 Azure</span><span class="sxs-lookup"><span data-stu-id="e577a-111">Licensing Azure</span></span>

<span data-ttu-id="e577a-112">在我們開始設計治理模型之前，務必先了解 Azure 的授權方式。</span><span class="sxs-lookup"><span data-stu-id="e577a-112">Before we begin designing our governance model, it's important to understand how Azure is licensed.</span></span> <span data-ttu-id="e577a-113">這是因為與您的 Azure 授權相關聯的系統管理帳戶，具有所有 Azure 資源的最高等級存取權。</span><span class="sxs-lookup"><span data-stu-id="e577a-113">This is because the administrative accounts associated with your Azure license have the highest level of access to all of your Azure resources.</span></span> <span data-ttu-id="e577a-114">這些系統管理帳戶形成治理模型的基礎。</span><span class="sxs-lookup"><span data-stu-id="e577a-114">These administrative accounts form the basis of your governance model.</span></span>  

> [!NOTE]
> <span data-ttu-id="e577a-115">如果貴組織擁有現有 [Microsoft Enterprise 合約](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx)，但是其中不包含 Azure，可以藉由預先付款承諾來新增 Azure。</span><span class="sxs-lookup"><span data-stu-id="e577a-115">If your organization has an existing [Microsoft Enterprise Agreement](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) that does not include Azure, Azure can be added by making an upfront monetary commitment.</span></span> <span data-ttu-id="e577a-116">如需詳細資訊，請參閱 [Azure 企業授權](https://azure.microsoft.com/pricing/enterprise-agreement/)。</span><span class="sxs-lookup"><span data-stu-id="e577a-116">See [licensing Azure for the enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) for more information.</span></span> 

<span data-ttu-id="e577a-117">當 Azure 新增至貴組織的 Enterprise 合約時，系統會提示貴組織建立 **Azure 帳戶**。</span><span class="sxs-lookup"><span data-stu-id="e577a-117">When Azure was added to your organization's Enterprise Agreement, your organization was prompted to create an **Azure account**.</span></span> <span data-ttu-id="e577a-118">在帳戶建立程序中，會建立 **Azure 帳戶擁有者**，以及具有**全域管理員**帳戶的 Azure Active Directory (Azure AD) 租用戶。</span><span class="sxs-lookup"><span data-stu-id="e577a-118">During the account creation process, an **Azure account owner** was created, as well as an Azure Active Directory (Azure AD) tenant with a **global administrator** account.</span></span> <span data-ttu-id="e577a-119">Azure AD 租用戶是一種邏輯建構，代表 Azure AD 專用的安全執行個體。</span><span class="sxs-lookup"><span data-stu-id="e577a-119">An Azure AD tenant is a logical construct that represents a secure, dedicated instance of Azure AD.</span></span>

<span data-ttu-id="e577a-120">![具有 Azure 帳戶管理員和 Azure AD 全域管理員的 Azure 帳戶](../_images/governance-3-0.png)
*圖 1。具有帳戶管理員和 Azure AD 全域管理員的 Azure 帳戶。*</span><span class="sxs-lookup"><span data-stu-id="e577a-120">![Azure account with Azure Account Manager and Azure AD global administrator](../_images/governance-3-0.png)
*Figure 1. An Azure account with an Account Manager and Azure AD Global Administrator.*</span></span>

## <a name="identity-management"></a><span data-ttu-id="e577a-121">身分識別管理</span><span class="sxs-lookup"><span data-stu-id="e577a-121">Identity management</span></span>

<span data-ttu-id="e577a-122">Azure 只信任由 [Azure AD](/azure/active-directory) 來驗證使用者以及將資源存取權授權給使用者，因此 Azure AD 是我們的身分識別管理系統。</span><span class="sxs-lookup"><span data-stu-id="e577a-122">Azure only trusts [Azure AD](/azure/active-directory) to authenticate users and authorize user access to resources, so Azure AD is our identity management system.</span></span> <span data-ttu-id="e577a-123">Azure AD 全域管理員具有最高等級權限，可以執行與身分識別相關的所有動作，包括建立使用者與指派權限。</span><span class="sxs-lookup"><span data-stu-id="e577a-123">The Azure AD global administrator has the highest level of permissions and can perform all actions related to identity, including creating users and assigning permissions.</span></span> 

<span data-ttu-id="e577a-124">我們的需求是單一**工作負載擁有者**的身分識別管理，該擁有者負責部署和維護簡單工作負載。</span><span class="sxs-lookup"><span data-stu-id="e577a-124">Our requirement is identity management for a single **workload owner** who is responsible for deploying and maintaining the simple workload.</span></span> <span data-ttu-id="e577a-125">工作負載擁有者需要建立、讀取、更新和刪除資源的權限，以及將這些權限委派給身分識別管理系統中其他使用者的權限。</span><span class="sxs-lookup"><span data-stu-id="e577a-125">The workload owner requires permission to create, read, update, and delete resources as well as permission to delegate these rights to other users in the identity management system.</span></span>

<span data-ttu-id="e577a-126">我們的 Azure AD 全域管理員會為**工作負載擁有者**建立**工作負載擁有者**帳戶：</span><span class="sxs-lookup"><span data-stu-id="e577a-126">Our Azure AD global administrator will create the **workload owner** account for the **workload owner**:</span></span>

<span data-ttu-id="e577a-127">![Azure AD 全域管理員建立工作負載擁有者帳戶](../_images/governance-1-2.png)
*圖 2。Azure AD 全域管理員建立工作負載擁有者使用者帳戶。*</span><span class="sxs-lookup"><span data-stu-id="e577a-127">![The Azure AD global administrator creates the workload owner account](../_images/governance-1-2.png)
*Figure 2. The Azure AD global administrator creates the workload owner user account.*</span></span>

<span data-ttu-id="e577a-128">在這個使用者新增至**訂用帳戶**之前，我們無法指派資源存取權限，因此我們會在後續兩個章節進行這項操作。</span><span class="sxs-lookup"><span data-stu-id="e577a-128">We aren't able to assign resource access permission until this user is added to a **subscription**, so we'll do that in the next two sections.</span></span> 

## <a name="resource-management-scope"></a><span data-ttu-id="e577a-129">資源管理範圍</span><span class="sxs-lookup"><span data-stu-id="e577a-129">Resource management scope</span></span>

<span data-ttu-id="e577a-130">隨著貴組織部署的資源數成長，治理這些資源的複雜度也隨之增加。</span><span class="sxs-lookup"><span data-stu-id="e577a-130">As the number of resources deployed by your organization grows, the complexity of governing those resources grows as well.</span></span> <span data-ttu-id="e577a-131">Azure 會實作邏輯容器階層，讓貴組織以各種層級的細微性 (也稱為**範圍**) 管理群組中的資源。</span><span class="sxs-lookup"><span data-stu-id="e577a-131">Azure implements a logical container hierarchy to enable your organization to manage your resources in groups at various levels of granularity, also known as **scope**.</span></span> 

<span data-ttu-id="e577a-132">最上層的資源管理範圍是**訂用帳戶**層級。</span><span class="sxs-lookup"><span data-stu-id="e577a-132">The top level of resource management scope is the **subscription** level.</span></span> <span data-ttu-id="e577a-133">訂用帳戶是由 Azure **帳戶擁有者**建立，該擁有者會建立財務承諾，並且負責支付與訂用帳戶相關聯的所有 Azure 資源費用：</span><span class="sxs-lookup"><span data-stu-id="e577a-133">A subscription is created by the Azure **account owner**, who establishes the financial commitment and is responsible for paying for all Azure resources associated with the subscription:</span></span>

<span data-ttu-id="e577a-134">![Azure 帳戶擁有者建立訂用帳戶](../_images/governance-1-3.png)
*圖 3。Azure 帳戶擁有者建立訂用帳戶。*</span><span class="sxs-lookup"><span data-stu-id="e577a-134">![The Azure account owner creates a subscription](../_images/governance-1-3.png)
*Figure 3. The Azure account owner creates a subscription.*</span></span>

<span data-ttu-id="e577a-135">訂用帳戶建立時，Azure **帳戶擁有者**會讓 Azure AD 租用戶與訂用帳戶產生關聯，並且使用這個 Azure AD 租用戶來驗證和授權使用者：</span><span class="sxs-lookup"><span data-stu-id="e577a-135">When the subscription is created, the Azure **account owner** associates an Azure AD tenant with the subscription, and this Azure AD tenant is used for authenticating and authorizing users:</span></span>

<span data-ttu-id="e577a-136">![Azure 帳戶擁有者讓 Azure AD 租用戶與訂用帳戶產生關聯](../_images/governance-1-4.png)
*圖 4。Azure 帳戶擁有者讓 Azure AD 租用戶與訂用帳戶產生關聯。*</span><span class="sxs-lookup"><span data-stu-id="e577a-136">![The Azure account owner associates the Azure AD tenant with the subscription](../_images/governance-1-4.png)
*Figure 4. The Azure account owner associates the Azure AD tenant with the subscription.*</span></span>

<span data-ttu-id="e577a-137">您可能已經注意到，目前沒有任何使用者與訂用帳戶相關聯，這表示沒有任何使用者具有管理資源的權限。</span><span class="sxs-lookup"><span data-stu-id="e577a-137">You may have noticed that there is currently no user associated with the subscription, which means that no one has permission to manage resources.</span></span> <span data-ttu-id="e577a-138">事實上，**帳戶擁有者**是訂用帳戶的擁有者，而且具有對訂用帳戶中資源採取任何動作的權限。</span><span class="sxs-lookup"><span data-stu-id="e577a-138">In reality, the **account owner** is the owner of the subscription and has permission to take any action on a resource in the subscription.</span></span> <span data-ttu-id="e577a-139">不過，實際上**帳戶擁有者**更像是貴組織中的財務人員，不負責建立、讀取、更新和刪除資源，這些工作會由**工作負載擁有者**執行。</span><span class="sxs-lookup"><span data-stu-id="e577a-139">However, in practical terms the **account owner** is more than likely a finance person in your organization and is not responsible for creating, reading, updating, and deleting resources - those tasks will be performed by the **workload owner**.</span></span> <span data-ttu-id="e577a-140">因此，我們必須將**工作負載擁有者**新增至訂用帳戶並指派權限。</span><span class="sxs-lookup"><span data-stu-id="e577a-140">Therefore, we need to add the **workload owner** to the subscription and assign permissions.</span></span>

<span data-ttu-id="e577a-141">由於**帳戶擁有者**是目前唯一有權將**工作負載擁有者**新增至訂用帳戶的使用者，因此他們要將**工作負載擁有者**新增至訂用帳戶：</span><span class="sxs-lookup"><span data-stu-id="e577a-141">Since the **account owner** is currently the only user with permission to add the **workload owner** to the subscription, they add the **workload owner** to the subscription:</span></span>

<span data-ttu-id="e577a-142">![Azure 帳戶擁有者將 **工作負載擁有者** 新增至訂用帳戶](../_images/governance-1-5.png)
*圖 5。Azure 帳戶擁有者將工作負載擁有者新增至訂用帳戶。*</span><span class="sxs-lookup"><span data-stu-id="e577a-142">![The Azure account owner adds the **workload owner** to the subscription](../_images/governance-1-5.png)
*Figure 5. The Azure account owner adds the workload owner to the subscription.*</span></span>

<span data-ttu-id="e577a-143">Azure **帳戶擁有者**會藉由指派[角色型存取控制 (RBAC)](/azure/role-based-access-control/) 角色，將權限授與**工作負載擁有者**。</span><span class="sxs-lookup"><span data-stu-id="e577a-143">The Azure **account owner** grants permissions to the **workload owner** by assigning a [role-based access control (RBAC)](/azure/role-based-access-control/) role.</span></span> <span data-ttu-id="e577a-144">RBAC 角色會指定一組權限，讓**工作負載擁有者**針對個別資源類型或一組資源類型使用。</span><span class="sxs-lookup"><span data-stu-id="e577a-144">The RBAC role specifies a set of permissions that the **workload owner** has for an individual resource type or a set of resource types.</span></span>

<span data-ttu-id="e577a-145">請注意，在此範例中，**帳戶擁有者**已獲得指派[內建**擁有者**角色](/azure/role-based-access-control/built-in-roles#owner)：</span><span class="sxs-lookup"><span data-stu-id="e577a-145">Notice that in this example, the **account owner** has assigned the [built-in **owner** role](/azure/role-based-access-control/built-in-roles#owner):</span></span> 

<span data-ttu-id="e577a-146">![**工作負載擁有者** 獲得指派內建擁有者角色](../_images/governance-1-6.png)
*圖 6。工作負載擁有者獲得指派內建擁有者角色。*</span><span class="sxs-lookup"><span data-stu-id="e577a-146">![The **workload owner** was assigned the built-in owner role](../_images/governance-1-6.png)
*Figure 6. The workload owner was assigned the built-in owner role.*</span></span>

<span data-ttu-id="e577a-147">內建**擁有者**角色會在訂用帳戶範圍內，將所有權限授與**工作負載擁有者**。</span><span class="sxs-lookup"><span data-stu-id="e577a-147">The built-in **owner** role grants all permissions to the **workload owner** at the subscription scope.</span></span> 

> [!IMPORTANT]
> <span data-ttu-id="e577a-148">Azure **帳戶擁有者**負責與訂用帳戶相關聯的財務承諾，但是**工作負載擁有者**具有相同的權限。</span><span class="sxs-lookup"><span data-stu-id="e577a-148">The Azure **acount owner** is responsible for the financial committment associated with the subscription, but the **workload owner** has the same permissions.</span></span> <span data-ttu-id="e577a-149">**帳戶擁有者**必須信任**工作負載擁有者**會在訂用帳戶預算內部署資源。</span><span class="sxs-lookup"><span data-stu-id="e577a-149">The **account owner** must trust the **workload owner** to deploy resources that are within the subscription budget.</span></span>

<span data-ttu-id="e577a-150">管理範圍的下一個層級是**資源群組**層級。</span><span class="sxs-lookup"><span data-stu-id="e577a-150">The next level of management scope is the **resource group** level.</span></span> <span data-ttu-id="e577a-151">資源群組是資源的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="e577a-151">A resource group is a logical container for resources.</span></span> <span data-ttu-id="e577a-152">在資源群組層級套用的作業會套用至群組中的所有資源。</span><span class="sxs-lookup"><span data-stu-id="e577a-152">Operations applied at the resource group level apply to all resources in a group.</span></span> <span data-ttu-id="e577a-153">此外，請務必注意，每個使用者的權限繼承自下一個層級以上，除非在該範圍明確變更。</span><span class="sxs-lookup"><span data-stu-id="e577a-153">Also, it's important to note that permissions for each user are inherited from the next level up unless they are explicitly changed at that scope.</span></span> 

<span data-ttu-id="e577a-154">為了說明這點，讓我們看看當**工作負載擁有者**建立資源群組時會發生什麼事：</span><span class="sxs-lookup"><span data-stu-id="e577a-154">To illustrate this, let's look at what happens when the **workload owner** creates a resource group:</span></span>

<span data-ttu-id="e577a-155">![**工作負載擁有者** 建立資源群組](../_images/governance-1-7.png)
*圖 7。工作負載擁有者建立資源群組，並且在資源群組範圍繼承內建擁有者角色。*</span><span class="sxs-lookup"><span data-stu-id="e577a-155">![The **workload owner** creates a resource group](../_images/governance-1-7.png)
*Figure 7. The workload owner creates a resource group and inherits the built-in owner role at the resource group scope.*</span></span>

<span data-ttu-id="e577a-156">同樣地，內建**擁有者**角色會在資源群組範圍內，將所有權限授與**工作負載擁有者**。</span><span class="sxs-lookup"><span data-stu-id="e577a-156">Again, the built-in **owner** role grants all permissions to the **workload owner** at the resource group scope.</span></span> <span data-ttu-id="e577a-157">如同我們稍早所述，此角色是繼承自訂用帳戶層級。</span><span class="sxs-lookup"><span data-stu-id="e577a-157">As we discussed earlier, this role is inherited from the subscription level.</span></span> <span data-ttu-id="e577a-158">如果有不同的角色在此範圍中指派給這位使用者，則它僅適用於此範圍。</span><span class="sxs-lookup"><span data-stu-id="e577a-158">If a different role is assigned to this user at this scope, it applies to this scope only.</span></span>

<span data-ttu-id="e577a-159">管理範圍的最低層級是**資源**層級。</span><span class="sxs-lookup"><span data-stu-id="e577a-159">The lowest level of management scope is at the **resource** level.</span></span> <span data-ttu-id="e577a-160">在資源層級套用的作業僅會套用到資源本身。</span><span class="sxs-lookup"><span data-stu-id="e577a-160">Operations applied at the resource level apply only to the resource itself.</span></span> <span data-ttu-id="e577a-161">同樣地，資源層級的權限繼承自資源群組範圍。</span><span class="sxs-lookup"><span data-stu-id="e577a-161">And once again, permissions at the resource level are inherited from resource group scope.</span></span> <span data-ttu-id="e577a-162">例如，讓我們看看如果**工作負載擁有者**將[虛擬網路](/azure/virtual-network/virtual-networks-overview)部署到資源群組，會發生什麼情況：</span><span class="sxs-lookup"><span data-stu-id="e577a-162">For example, let's look at what happens if the **workload owner** deploys a [virtual network](/azure/virtual-network/virtual-networks-overview) into the resource group:</span></span>

<span data-ttu-id="e577a-163">![**工作負載擁有者** 建立資源](../_images/governance-1-8.png)
*圖 8。工作負載擁有者建立資源，並且在資源範圍繼承內建擁有者角色。*</span><span class="sxs-lookup"><span data-stu-id="e577a-163">![The **workload owner** creates a resource](../_images/governance-1-8.png)
*Figure 8. The workload owner creates a resource and inherits the built-in owner role at the resource scope.*</span></span>

<span data-ttu-id="e577a-164">**工作負載擁有者**會在資源範圍繼承擁有者角色，這表示工作負載擁有者具有虛擬網路的所有權限。</span><span class="sxs-lookup"><span data-stu-id="e577a-164">The **workload owner** inherits the owner role at the resource scope, which means the workload owner has all permissions for the virtual network.</span></span>

## <a name="implementing-the-basic-resource-access-management-model"></a><span data-ttu-id="e577a-165">實作基本資源存取權管理模型</span><span class="sxs-lookup"><span data-stu-id="e577a-165">Implementing the basic resource access management model</span></span>

<span data-ttu-id="e577a-166">讓我們繼續了解如何實作先前所設計的治理模型。</span><span class="sxs-lookup"><span data-stu-id="e577a-166">Let's move on to learn how to implement the governance model designed earlier.</span></span> 

<span data-ttu-id="e577a-167">若要開始，貴組織需要 Azure 帳戶。</span><span class="sxs-lookup"><span data-stu-id="e577a-167">To begin, your organization requires an Azure account.</span></span> <span data-ttu-id="e577a-168">如果貴組織擁有現有 [Microsoft Enterprise 合約](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx)，但是其中不包含 Azure，可以藉由預先付款承諾來新增 Azure。</span><span class="sxs-lookup"><span data-stu-id="e577a-168">If your organization has an existing [Microsoft Enterprise Agreement](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx) that does not include Azure, Azure can be added by making an upfront monetary commitment.</span></span> <span data-ttu-id="e577a-169">如需詳細資訊，請參閱 [Azure 企業授權](https://azure.microsoft.com/pricing/enterprise-agreement/)。</span><span class="sxs-lookup"><span data-stu-id="e577a-169">See [licensing Azure for the enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) for more information.</span></span> 

<span data-ttu-id="e577a-170">在建立您的 Azure 帳戶時，會指定貴組織中的某位人員成為 Azure **帳戶擁有者**。</span><span class="sxs-lookup"><span data-stu-id="e577a-170">When your Azure account is created, you specify a person in your organization to be the Azure **account owner**.</span></span> <span data-ttu-id="e577a-171">預設會接著建立 Azure Active Directory (Azure AD) 租用戶。</span><span class="sxs-lookup"><span data-stu-id="e577a-171">An Azure Active Directory (Azure AD) tenant is then created by default.</span></span> <span data-ttu-id="e577a-172">您的 Azure **帳戶擁有者**，必須為組織中身為**工作負載擁有者**的人員[建立使用者帳戶](/azure/active-directory/add-users-azure-active-directory)。</span><span class="sxs-lookup"><span data-stu-id="e577a-172">Your Azure **account owner** must [create the user account](/azure/active-directory/add-users-azure-active-directory) for the person in your organization who is the **workload owner**.</span></span> 

<span data-ttu-id="e577a-173">接下來，您的 Azure **帳戶擁有者**必須[建立訂用帳戶](https://docs.microsoft.com/partner-center/create-a-new-subscription)，並且將其[關聯到 Azure AD 租用戶](/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory)。</span><span class="sxs-lookup"><span data-stu-id="e577a-173">Next, your Azure **account owner** must [create a subscription](https://docs.microsoft.com/partner-center/create-a-new-subscription) and [associate the Azure AD tenant](/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory) with it.</span></span>

<span data-ttu-id="e577a-174">最後，既然已建立訂用帳戶，且您的 Azure AD 租用戶已與其相關聯，就可以將**工作負載擁有者**[，新增至具備內建**擁有者**角色](/azure/billing/billing-add-change-azure-subscription-administrator#add-an-rbac-owner-for-a-subscription-in-azure-portal)的訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="e577a-174">Finally, now that the subscription is created and your Azure AD tenant is associated with it, you can [add the **workload owner** to the subscription with the built-in **owner** role](/azure/billing/billing-add-change-azure-subscription-administrator#add-an-rbac-owner-for-a-subscription-in-azure-portal).</span></span>

## <a name="next-steps"></a><span data-ttu-id="e577a-175">後續步驟</span><span class="sxs-lookup"><span data-stu-id="e577a-175">Next steps</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="e577a-176">將基本工作負載部署至 Azure</span><span class="sxs-lookup"><span data-stu-id="e577a-176">Deploy a basic workload to Azure</span></span>](../infrastructure/basic-workload.md)
> [!div class="nextstepaction"]
> [<span data-ttu-id="e577a-177">深入了解多個小組的資源存取</span><span class="sxs-lookup"><span data-stu-id="e577a-177">Learn about resource access for multiple teams</span></span>](governance-multiple-teams.md)

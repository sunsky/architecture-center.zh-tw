---
title: 多組織用戶共享應用程式的身分識別管理
description: 在多租用戶應用程式中進行驗證、授權和身分識別管理的最佳作法。
author: MikeWasson
ms.date: 07/21/2017
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
---

# <a name="manage-identity-in-multitenant-applications"></a><span data-ttu-id="80c9a-103">管理多租用戶應用程式中的身分識別</span><span class="sxs-lookup"><span data-stu-id="80c9a-103">Manage identity in multitenant applications</span></span>

<span data-ttu-id="80c9a-104">此系列文章會說明多租用戶使用 Azure AD 進行驗證和身分識別管理時的最佳做法。</span><span class="sxs-lookup"><span data-stu-id="80c9a-104">This series of articles describes best practices for multitenancy, when using Azure AD for authentication and identity management.</span></span>

<span data-ttu-id="80c9a-105">[![GitHub](../_images/github.png) 程式碼範例][sample-application]</span><span class="sxs-lookup"><span data-stu-id="80c9a-105">[![GitHub](../_images/github.png) Sample code][sample-application]</span></span>

<span data-ttu-id="80c9a-106">建置多租用戶應用程式時，最初會遇到管理使用者身分識別的難題，因為現在每位使用者各屬於一名租用戶。</span><span class="sxs-lookup"><span data-stu-id="80c9a-106">When you're building a multitenant application, one of the first challenges is managing user identities, because now every user belongs to a tenant.</span></span> <span data-ttu-id="80c9a-107">例如︰</span><span class="sxs-lookup"><span data-stu-id="80c9a-107">For example:</span></span>

- <span data-ttu-id="80c9a-108">使用者透過其組織認證登入。</span><span class="sxs-lookup"><span data-stu-id="80c9a-108">Users sign in with their organizational credentials.</span></span>
- <span data-ttu-id="80c9a-109">使用者應該可以存取其組織的資料，但不能存取屬於其他租用戶的資料。</span><span class="sxs-lookup"><span data-stu-id="80c9a-109">Users should have access to their organization's data, but not data that belongs to other tenants.</span></span>
- <span data-ttu-id="80c9a-110">組織可以註冊應用程式，並再將該應用程式角色指派給其成員。</span><span class="sxs-lookup"><span data-stu-id="80c9a-110">An organization can sign up for the application, and then assign application roles to its members.</span></span>

<span data-ttu-id="80c9a-111">Azure Active Directory (Azure AD) 有一些絕佳功能，能夠支援這些所有案例。</span><span class="sxs-lookup"><span data-stu-id="80c9a-111">Azure Active Directory (Azure AD) has some great features that support all of these scenarios.</span></span>

<span data-ttu-id="80c9a-112">伴隨著這一系列的文章，我們建立了多租用戶應用程式的完整[端對端實作][ sample-application]。</span><span class="sxs-lookup"><span data-stu-id="80c9a-112">To accompany this series of articles, we created a complete [end-to-end implementation][sample-application] of a multitenant application.</span></span> <span data-ttu-id="80c9a-113">文章反映出我們在建置應用程式過程中學到的知識。</span><span class="sxs-lookup"><span data-stu-id="80c9a-113">The articles reflect what we learned in the process of building the application.</span></span> <span data-ttu-id="80c9a-114">若要開始使用應用程式，請參閱[執行調查應用程式][running-the-app]。</span><span class="sxs-lookup"><span data-stu-id="80c9a-114">To get started with the application, see [Run the Surveys application][running-the-app].</span></span>

## <a name="introduction"></a><span data-ttu-id="80c9a-115">簡介</span><span class="sxs-lookup"><span data-stu-id="80c9a-115">Introduction</span></span>

<span data-ttu-id="80c9a-116">假設您正在撰寫要裝載在雲端的企業 SaaS 應用程式。</span><span class="sxs-lookup"><span data-stu-id="80c9a-116">Let's say you're writing an enterprise SaaS application to be hosted in the cloud.</span></span> <span data-ttu-id="80c9a-117">當然，應用程式將會有使用者：</span><span class="sxs-lookup"><span data-stu-id="80c9a-117">Of course, the application will have users:</span></span>

![使用者](./images/users.png)

<span data-ttu-id="80c9a-119">但那些使用者隸屬於組織：</span><span class="sxs-lookup"><span data-stu-id="80c9a-119">But those users belong to organizations:</span></span>

![組織使用者](./images/org-users.png)

<span data-ttu-id="80c9a-121">範例：Tailspin 銷售其 SaaS 應用程式的訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="80c9a-121">Example: Tailspin sells subscriptions to its SaaS application.</span></span> <span data-ttu-id="80c9a-122">Contoso 與 Fabrikam 註冊該應用程式。</span><span class="sxs-lookup"><span data-stu-id="80c9a-122">Contoso and Fabrikam sign up for the app.</span></span> <span data-ttu-id="80c9a-123">當 Alice (`alice@contoso`) 登入時，應用程式應該會知道 Alice 隸屬於 Contoso。</span><span class="sxs-lookup"><span data-stu-id="80c9a-123">When Alice (`alice@contoso`) signs in, the application should know that Alice is part of Contoso.</span></span>

- <span data-ttu-id="80c9a-124">Alice *應該有* Contoso 資料的存取權。</span><span class="sxs-lookup"><span data-stu-id="80c9a-124">Alice *should* have access to Contoso data.</span></span>
- <span data-ttu-id="80c9a-125">Alice 應該沒有 Fabrikam 資料的存取權。</span><span class="sxs-lookup"><span data-stu-id="80c9a-125">Alice *should not* have access to Fabrikam data.</span></span>

<span data-ttu-id="80c9a-126">此指南將向您說明如何使用 [Azure Active Directory (Azure AD)](/azure/active-directory) 處理登入與驗證，於多組織用戶共享應用程式中管理使用者身分識別。</span><span class="sxs-lookup"><span data-stu-id="80c9a-126">This guidance will show you how to manage user identities in a multitenant application, using [Azure Active Directory (Azure AD)](/azure/active-directory) to handle sign-in and authentication.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="what-is-multitenancy"></a><span data-ttu-id="80c9a-127">何謂多組織用戶管理？</span><span class="sxs-lookup"><span data-stu-id="80c9a-127">What is multitenancy?</span></span>

<!-- markdownlint-enable MD026 -->

<span data-ttu-id="80c9a-128">*租用戶* 是一群使用者。</span><span class="sxs-lookup"><span data-stu-id="80c9a-128">A *tenant* is a group of users.</span></span> <span data-ttu-id="80c9a-129">在 SaaS 應用程式中，租用戶是應用程式的訂閱者或客戶。</span><span class="sxs-lookup"><span data-stu-id="80c9a-129">In a SaaS application, the tenant is a subscriber or customer of the application.</span></span> <span data-ttu-id="80c9a-130">*多組織用戶管理* 是多個租用戶共用同一應用程式實際執行個體的架構。</span><span class="sxs-lookup"><span data-stu-id="80c9a-130">*Multitenancy* is an architecture where multiple tenants share the same physical instance of the app.</span></span> <span data-ttu-id="80c9a-131">雖然租用戶共用實際資源 (例如 VM 或儲存體)，但每個租用戶都會獲得自己的應用程式邏輯執行個體。</span><span class="sxs-lookup"><span data-stu-id="80c9a-131">Although tenants share physical resources (such as VMs or storage), each tenant gets its own logical instance of the app.</span></span>

<span data-ttu-id="80c9a-132">通常，應用程式資料會在租用戶的使用者之間共用，但是不會與其他租用戶共用。</span><span class="sxs-lookup"><span data-stu-id="80c9a-132">Typically, application data is shared among the users within a tenant, but not with other tenants.</span></span>

![多組織用戶共享](./images/multitenant.png)

<span data-ttu-id="80c9a-134">將此架構與每個租用戶都有專用實體執行個體的單一租用戶架構比較。</span><span class="sxs-lookup"><span data-stu-id="80c9a-134">Compare this architecture with a single-tenant architecture, where each tenant has a dedicated physical instance.</span></span> <span data-ttu-id="80c9a-135">在單一租用戶架構中，您可以透過執行應用程式的新執行個體來新增租用戶。</span><span class="sxs-lookup"><span data-stu-id="80c9a-135">In a single-tenant architecture, you add tenants by spinning up new instances of the app.</span></span>

![單一租用戶](./images/single-tenant.png)

### <a name="multitenancy-and-horizontal-scaling"></a><span data-ttu-id="80c9a-137">多組織用戶管理與水平放大</span><span class="sxs-lookup"><span data-stu-id="80c9a-137">Multitenancy and horizontal scaling</span></span>

<span data-ttu-id="80c9a-138">若要在雲端達到某種規模，通常是新增更多的實體執行個體。</span><span class="sxs-lookup"><span data-stu-id="80c9a-138">To achieve scale in the cloud, it’s common to add more physical instances.</span></span> <span data-ttu-id="80c9a-139">這稱為水平調整或相應放大。請考慮 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="80c9a-139">This is known as *horizontal scaling* or *scaling out*. Consider a web app.</span></span> <span data-ttu-id="80c9a-140">若要處理更多流量，您可以新增更多伺服器 VM 並將它們放到負載平衡器後方。</span><span class="sxs-lookup"><span data-stu-id="80c9a-140">To handle more traffic, you can add more server VMs and put them behind a load balancer.</span></span> <span data-ttu-id="80c9a-141">每個 VM 都會執行 Web 應用程式的個別實體執行個體。</span><span class="sxs-lookup"><span data-stu-id="80c9a-141">Each VM runs a separate physical instance of the web app.</span></span>

![執行網站負載平衡](./images/load-balancing.png)

<span data-ttu-id="80c9a-143">任何要求都可以路由到任何執行個體。</span><span class="sxs-lookup"><span data-stu-id="80c9a-143">Any request can be routed to any instance.</span></span> <span data-ttu-id="80c9a-144">同時，系統也會以單一邏輯執行個體的方式運作。</span><span class="sxs-lookup"><span data-stu-id="80c9a-144">Together, the system functions as a single logical instance.</span></span> <span data-ttu-id="80c9a-145">您可以在不會影響使用者的情況下終止 VM 或執行新的 VM。</span><span class="sxs-lookup"><span data-stu-id="80c9a-145">You can tear down a VM or spin up a new VM, without affecting users.</span></span> <span data-ttu-id="80c9a-146">在這個架構中，每個實體執行個體都是多組織用戶共享，而且您可以透過新增更多執行個體來放大。</span><span class="sxs-lookup"><span data-stu-id="80c9a-146">In this architecture, each physical instance is multi-tenant, and you scale by adding more instances.</span></span> <span data-ttu-id="80c9a-147">如果某個執行個體故障，應不會影響任何租用戶。</span><span class="sxs-lookup"><span data-stu-id="80c9a-147">If one instance goes down, it should not affect any tenant.</span></span>

## <a name="identity-in-a-multitenant-app"></a><span data-ttu-id="80c9a-148">多組織用戶共享應用程式中的身分識別</span><span class="sxs-lookup"><span data-stu-id="80c9a-148">Identity in a multitenant app</span></span>

<span data-ttu-id="80c9a-149">在多組織用戶共享應用程式中，您必須考量使用者是多組織用戶共享環境中的使用者。</span><span class="sxs-lookup"><span data-stu-id="80c9a-149">In a multitenant app, you must consider users in the context of tenants.</span></span>

### <a name="authentication"></a><span data-ttu-id="80c9a-150">Authentication</span><span class="sxs-lookup"><span data-stu-id="80c9a-150">Authentication</span></span>

- <span data-ttu-id="80c9a-151">使用者透過其組織認證登入應用程式。</span><span class="sxs-lookup"><span data-stu-id="80c9a-151">Users sign into the app with their organization credentials.</span></span> <span data-ttu-id="80c9a-152">它們不需要針對應用程式建立新的使用者設定檔。</span><span class="sxs-lookup"><span data-stu-id="80c9a-152">They don't have to create new user profiles for the app.</span></span>
- <span data-ttu-id="80c9a-153">同一組織內的使用者隸屬於同一租用戶。</span><span class="sxs-lookup"><span data-stu-id="80c9a-153">Users within the same organization are part of the same tenant.</span></span>
- <span data-ttu-id="80c9a-154">當使用者登入時，應用程式會知道使用者隸屬於哪一個租用戶。</span><span class="sxs-lookup"><span data-stu-id="80c9a-154">When a user signs in, the application knows which tenant the user belongs to.</span></span>

### <a name="authorization"></a><span data-ttu-id="80c9a-155">Authorization</span><span class="sxs-lookup"><span data-stu-id="80c9a-155">Authorization</span></span>

- <span data-ttu-id="80c9a-156">授權使用者的動作時 (例如檢視資源)，應用程式必須考量使用者隸屬的租用戶。</span><span class="sxs-lookup"><span data-stu-id="80c9a-156">When authorizing a user's actions (say, viewing a resource), the app must take into account the user's tenant.</span></span>
- <span data-ttu-id="80c9a-157">使用者可能會被指派在應用程式中的角色，例如「系統管理員」或「標準使用者」。</span><span class="sxs-lookup"><span data-stu-id="80c9a-157">Users might be assigned roles within the application, such as "Admin" or "Standard User".</span></span> <span data-ttu-id="80c9a-158">角色指派應由客戶管理，而不是由 SaaS 提供者管理。</span><span class="sxs-lookup"><span data-stu-id="80c9a-158">Role assignments should be managed by the customer, not by the SaaS provider.</span></span>

<span data-ttu-id="80c9a-159">**範例。**</span><span class="sxs-lookup"><span data-stu-id="80c9a-159">**Example.**</span></span> <span data-ttu-id="80c9a-160">Alice 為 Contoso 的員工，使用其瀏覽器瀏覽至應用程式並按一下 [登入] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="80c9a-160">Alice, an employee at Contoso, navigates to the application in her browser and clicks the “Log in” button.</span></span> <span data-ttu-id="80c9a-161">她被重新導向到可在其中輸入企業認證 (使用者名稱與密碼) 登入畫面。</span><span class="sxs-lookup"><span data-stu-id="80c9a-161">She is redirected to a login screen where she enters her corporate credentials (username and password).</span></span> <span data-ttu-id="80c9a-162">此時，她以 `alice@contoso.com`的身分登入應用程式。</span><span class="sxs-lookup"><span data-stu-id="80c9a-162">At this point, she is logged into the app as `alice@contoso.com`.</span></span> <span data-ttu-id="80c9a-163">應用程式也會知道 Alice 是此應用程式的系統管理員使用者。</span><span class="sxs-lookup"><span data-stu-id="80c9a-163">The application also knows that Alice is an admin user for this application.</span></span> <span data-ttu-id="80c9a-164">因為她是系統管理員，所以可以看到屬於 Contoso 之所有資源的清單。</span><span class="sxs-lookup"><span data-stu-id="80c9a-164">Because she is an admin, she can see a list of all the resources that belong to Contoso.</span></span> <span data-ttu-id="80c9a-165">但是，她無法檢視 Fabrikam 的資源，因為她只是其所屬租用戶中的系統管理員。</span><span class="sxs-lookup"><span data-stu-id="80c9a-165">However, she cannot view Fabrikam's resources, because she is an admin only within her tenant.</span></span>

<span data-ttu-id="80c9a-166">在本指南中，我們將特別探討使用 Azure AD 來管理身分識別。</span><span class="sxs-lookup"><span data-stu-id="80c9a-166">In this guidance, we'll look specifically at using Azure AD for identity management.</span></span>

- <span data-ttu-id="80c9a-167">我們假設客戶將他們的使用者設定檔儲存在 Azure AD (包括 Office365 和 Dynamics CRM 租用戶)</span><span class="sxs-lookup"><span data-stu-id="80c9a-167">We assume the customer stores their user profiles in Azure AD (including Office365 and Dynamics CRM tenants)</span></span>
- <span data-ttu-id="80c9a-168">使用內部部署 Active Directory 的客戶可以使用 [Azure AD Connect](/azure/active-directory/hybrid/whatis-hybrid-identity) 來同步其內部部署 Active Directory 與 Azure AD。</span><span class="sxs-lookup"><span data-stu-id="80c9a-168">Customers with on-premises Active Directory can use [Azure AD Connect](/azure/active-directory/hybrid/whatis-hybrid-identity) to sync their on-premises Active Directory with Azure AD.</span></span> <span data-ttu-id="80c9a-169">如果擁有內部部署 Active Directory 的客戶無法使用 Azure AD Connect (因為公司 IT 原則或其他原因)，SaaS 提供者可以透過 Active Directory Federation Services (AD FS) 與客戶的目錄聯合。</span><span class="sxs-lookup"><span data-stu-id="80c9a-169">If a customer with on-premises Active Directory cannot use Azure AD Connect (due to corporate IT policy or other reasons), the SaaS provider can federate with the customer's directory through Active Directory Federation Services (AD FS).</span></span> <span data-ttu-id="80c9a-170">此選項已在 [與客戶的 AD FS 聯合](adfs.md)中說明。</span><span class="sxs-lookup"><span data-stu-id="80c9a-170">This option is described in [Federating with a customer's AD FS](adfs.md).</span></span>

<span data-ttu-id="80c9a-171">本指南不考量多組織用戶管理的其他層面，例如參與、個別租用戶設定等等。</span><span class="sxs-lookup"><span data-stu-id="80c9a-171">This guidance does not consider other aspects of multitenancy such as data partitioning, per-tenant configuration, and so forth.</span></span>

[<span data-ttu-id="80c9a-172">**下一步**</span><span class="sxs-lookup"><span data-stu-id="80c9a-172">**Next**</span></span>](./tailspin.md)

<!-- links -->

[sample-application]: https://github.com/mspnp/multitenant-saas-guidance
[running-the-app]: ./run-the-app.md

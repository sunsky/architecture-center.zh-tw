---
title: 將內部部署 AD FS 擴充至 Azure
titleSuffix: Azure Reference Architectures
description: 在 Azure 中使用 Active Directory 同盟服務授權來實作安全的混合式網路架構。
author: telmosampaio
ms.date: 12/18.2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18, identity
ms.openlocfilehash: 22a2a2042c85e70d0d5a523c9ecf72395a9e774c
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54488268"
---
# <a name="extend-active-directory-federation-services-ad-fs-to-azure"></a><span data-ttu-id="7b7bd-103">將 Active Directory 同盟服務 (AD FS) 擴充至 Azure</span><span class="sxs-lookup"><span data-stu-id="7b7bd-103">Extend Active Directory Federation Services (AD FS) to Azure</span></span>

<span data-ttu-id="7b7bd-104">此參考架構會實作安全的混合式網路，可將您的內部部署網路擴充至 Azure，並使用 [Active Directory 同盟服務 (AD FS)][active-directory-federation-services] 來執行同盟驗證，以及授權 Azure 中執行的元件。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-104">This reference architecture implements a secure hybrid network that extends your on-premises network to Azure and uses [Active Directory Federation Services (AD FS)][active-directory-federation-services] to perform federated authentication and authorization for components running in Azure.</span></span> <span data-ttu-id="7b7bd-105">[**部署這個解決方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-105">[**Deploy this solution**](#deploy-the-solution).</span></span>

![使用 Active Directory 保護混合式網路架構的安全](./images/adfs.png)

<span data-ttu-id="7b7bd-107">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-107">*Download a [Visio file][visio-download] of this architecture.*</span></span>

<span data-ttu-id="7b7bd-108">AD FS 可在內部部署主控，但如果是當中有些組件實作在 Azure 中的混合式應用程式，在雲端中複寫 AD FS 可能會更有效率。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-108">AD FS can be hosted on-premises, but if your application is a hybrid in which some parts are implemented in Azure, it may be more efficient to replicate AD FS in the cloud.</span></span>

<span data-ttu-id="7b7bd-109">下圖顯示下列情節：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-109">The diagram shows the following scenarios:</span></span>

- <span data-ttu-id="7b7bd-110">夥伴組織中的應用程式程式碼會存取 Azure VNet 內主控的 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-110">Application code from a partner organization accesses a web application hosted inside your Azure VNet.</span></span>
- <span data-ttu-id="7b7bd-111">已外部註冊、且認證儲存在 Active Directory Domain Services (DS) 的使用者，可存取 Azure VNet 內主控的 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-111">An external, registered user with credentials stored inside Active Directory Domain Services (DS) accesses a web application hosted inside your Azure VNet.</span></span>
- <span data-ttu-id="7b7bd-112">使用授權裝置連線至 VNet 的使用者會執行 Azure VNet 內主控的 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-112">A user connected to your VNet using an authorized device executes a web application hosted inside your Azure VNet.</span></span>

<span data-ttu-id="7b7bd-113">此架構的典型使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-113">Typical uses for this architecture include:</span></span>

- <span data-ttu-id="7b7bd-114">部分工作負載在內部部署中執行、部分在 Azure 中執行的混合式應用程式。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-114">Hybrid applications where workloads run partly on-premises and partly in Azure.</span></span>
- <span data-ttu-id="7b7bd-115">使用同盟授權向夥伴組織公開 web 應用程式的解決方案。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-115">Solutions that use federated authorization to expose web applications to partner organizations.</span></span>
- <span data-ttu-id="7b7bd-116">支援從組織防火牆外部執行網頁瀏覽器之存取的系統。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-116">Systems that support access from web browsers running outside of the organizational firewall.</span></span>
- <span data-ttu-id="7b7bd-117">讓使用者能夠從授權的外部裝置 (例如遠端電腦、筆記型電腦和其他行動裝置) 連線來存取 web 應用程式的系統。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-117">Systems that enable users to access to web applications by connecting from authorized external devices such as remote computers, notebooks, and other mobile devices.</span></span>

<span data-ttu-id="7b7bd-118">此參考架構著重於被動同盟，同盟伺服器會在當中決定如何及何時要驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-118">This reference architecture focuses on *passive federation*, in which the federation servers decide how and when to authenticate a user.</span></span> <span data-ttu-id="7b7bd-119">使用者在應用程式啟動時，要提供登入資訊。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-119">The user provides sign in information when the application is started.</span></span> <span data-ttu-id="7b7bd-120">這項機制最常受網頁瀏覽器使用，且涉及將瀏覽器重新導向至使用者驗證之站台的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-120">This mechanism is most commonly used by web browsers and involves a protocol that redirects the browser to a site where the user authenticates.</span></span> <span data-ttu-id="7b7bd-121">AD FS 也支援主動同盟，其中應用程式會負責提供認證，而不需要使用者互動，但這種情節是在此架構範圍之外。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-121">AD FS also supports *active federation*, where an application takes on responsibility for supplying credentials without further user interaction, but that scenario is outside the scope of this architecture.</span></span>

<span data-ttu-id="7b7bd-122">如需了解其他考量，請參閱[選擇解決方案以整合內部部署 Active Directory 與 Azure][considerations]。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-122">For additional considerations, see [Choose a solution for integrating on-premises Active Directory with Azure][considerations].</span></span>

## <a name="architecture"></a><span data-ttu-id="7b7bd-123">架構</span><span class="sxs-lookup"><span data-stu-id="7b7bd-123">Architecture</span></span>

<span data-ttu-id="7b7bd-124">此架構會擴充[將 AD DS 延伸至 Azure][extending-ad-to-azure]中所述的實作。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-124">This architecture extends the implementation described in [Extending AD DS to Azure][extending-ad-to-azure].</span></span> <span data-ttu-id="7b7bd-125">它包含下列元件。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-125">It contains the followign components.</span></span>

- <span data-ttu-id="7b7bd-126">**AD DS 子網路**。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-126">**AD DS subnet**.</span></span> <span data-ttu-id="7b7bd-127">AD DS 伺服器會包含在自己的子網路中，且會將網路安全性群組 (NSG) 規則作為防火牆。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-127">The AD DS servers are contained in their own subnet with network security group (NSG) rules acting as a firewall.</span></span>

- <span data-ttu-id="7b7bd-128">**AD DS 伺服器**。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-128">**AD DS servers**.</span></span> <span data-ttu-id="7b7bd-129">在 Azure 中作為 VM 執行的網域控制站。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-129">Domain controllers running as VMs in Azure.</span></span> <span data-ttu-id="7b7bd-130">這些伺服器會提供網域內本機識別身分的驗證。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-130">These servers provide authentication of local identities within the domain.</span></span>

- <span data-ttu-id="7b7bd-131">**AD FS 子網路**。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-131">**AD FS subnet**.</span></span> <span data-ttu-id="7b7bd-132">AD FS 伺服器都位於自己的子網路內，且將 NSG 規則作為防火牆。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-132">The AD FS servers are located within their own subnet with NSG rules acting as a firewall.</span></span>

- <span data-ttu-id="7b7bd-133">**AD FS 伺服器**。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-133">**AD FS servers**.</span></span> <span data-ttu-id="7b7bd-134">AD FS 伺服器會提供同盟授權和驗證。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-134">The AD FS servers provide federated authorization and authentication.</span></span> <span data-ttu-id="7b7bd-135">在此架構中，他們會執行下列工作：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-135">In this architecture, they perform the following tasks:</span></span>

  - <span data-ttu-id="7b7bd-136">接收安全性權杖，當中包含由代表夥伴使用者之夥伴同盟伺服器進行的宣告。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-136">Receiving security tokens containing claims made by a partner federation server on behalf of a partner user.</span></span> <span data-ttu-id="7b7bd-137">AD FS 會先驗證權杖為有效後，才能將宣告傳遞至 Azure 中執行的 web 應用程式來授權要求。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-137">AD FS verifies that the tokens are valid before passing the claims to the web application running in Azure to authorize requests.</span></span>

    <span data-ttu-id="7b7bd-138">在 Azure 中執行的應用程式是「信賴憑證者」。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-138">The application running in Azure is the *relying party*.</span></span> <span data-ttu-id="7b7bd-139">夥伴同盟伺服器必須發出 web 應用程式可解讀的宣告。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-139">The partner federation server must issue claims that are understood by the web application.</span></span> <span data-ttu-id="7b7bd-140">夥伴同盟伺服器稱為帳戶夥伴，因為它們會代表夥伴組織中的驗證帳戶提交存取要求。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-140">The partner federation servers are referred to as *account partners*, because they submit access requests on behalf of authenticated accounts in the partner organization.</span></span> <span data-ttu-id="7b7bd-141">AD FS 伺服器稱為資源夥伴，因為它們提供資源 (web 應用程式) 的存取權。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-141">The AD FS servers are called *resource partners* because they provide access to resources (the web application).</span></span>

  - <span data-ttu-id="7b7bd-142">驗證並授權來自執行網頁瀏覽器或裝置 (需要 web 應用程式的存取權) 之外部使用者的內送要求，方法是使用 AD DS 和 [Active Directory 裝置註冊服務][ADDRS]。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-142">Authenticating and authorizing incoming requests from external users running a web browser or device that needs access to web applications, by using AD DS and the [Active Directory Device Registration Service][ADDRS].</span></span>

  <span data-ttu-id="7b7bd-143">AD FS 伺服器會設定為透過 Azure 負載平衡器存取的伺服器陣列。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-143">The AD FS servers are configured as a farm accessed through an Azure load balancer.</span></span> <span data-ttu-id="7b7bd-144">這個實作能改善可用性和延展性。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-144">This implementation improves availability and scalability.</span></span> <span data-ttu-id="7b7bd-145">AD FS 伺服器不會直接向網際網路公開。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-145">The AD FS servers are not exposed directly to the Internet.</span></span> <span data-ttu-id="7b7bd-146">所有網際網路流量都會透過 AD FS web 應用程式 proxy 伺服器和 DMZ (也稱為周邊網路) 進行篩選。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-146">All Internet traffic is filtered through AD FS web application proxy servers and a DMZ (also referred to as a perimeter network).</span></span>

  <span data-ttu-id="7b7bd-147">如需有關 AD FS 運作方式的詳細資訊，請參閱 [Active Directory 同盟服務概觀][active-directory-federation-services-overview]。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-147">For more information about how AD FS works, see [Active Directory Federation Services Overview][active-directory-federation-services-overview].</span></span> <span data-ttu-id="7b7bd-148">此外，[Azure 中的 AD FS 部署][adfs-intro]文章中包含了詳細的逐步實作。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-148">Also, the article [AD FS deployment in Azure][adfs-intro] contains a detailed step-by-step introduction to implementation.</span></span>

- <span data-ttu-id="7b7bd-149">**AD FS proxy 子網路**。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-149">**AD FS proxy subnet**.</span></span> <span data-ttu-id="7b7bd-150">AD FS proxy 伺服器可以包含在自己的子網路內，且 NSG 規則會提供保護。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-150">The AD FS proxy servers can be contained within their own subnet, with NSG rules providing protection.</span></span> <span data-ttu-id="7b7bd-151">此子網路中的伺服器會透過一組可在 Azure 虛擬網路與網際網路之間提供防火牆的網路虛擬設備，向網路虛擬公開。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-151">The servers in this subnet are exposed to the Internet through a set of network virtual appliances that provide a firewall between your Azure virtual network and the Internet.</span></span>

- <span data-ttu-id="7b7bd-152">**AD FS Web 應用程式 proxy (WAP) 伺服器**。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-152">**AD FS web application proxy (WAP) servers**.</span></span> <span data-ttu-id="7b7bd-153">這些 VM 會作為 AD FS 伺服器，接收來自夥伴組織和外部裝置的內送要求。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-153">These VMs act as AD FS servers for incoming requests from partner organizations and external devices.</span></span> <span data-ttu-id="7b7bd-154">WAP 伺服器會作為篩選條件，保護來自網際網路的直接存取 AD FS 伺服器。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-154">The WAP servers act as a filter, shielding the AD FS servers from direct access from the Internet.</span></span> <span data-ttu-id="7b7bd-155">如同 AD FS 伺服器，在伺服器陣列中使用負載平衡部署 WAP 伺服器，可為您提供比部署獨立伺服器的集合更高的可用性和延展性。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-155">As with the AD FS servers, deploying the WAP servers in a farm with load balancing gives you greater availability and scalability than deploying a collection of stand-alone servers.</span></span>

  > [!NOTE]
  > <span data-ttu-id="7b7bd-156">如需安裝 WAP 伺服器的詳細資訊，請參閱[安裝及設定 Web 應用程式 Proxy 伺服器][install_and_configure_the_web_application_proxy_server]</span><span class="sxs-lookup"><span data-stu-id="7b7bd-156">For detailed information about installing WAP servers, see [Install and Configure the Web Application Proxy Server][install_and_configure_the_web_application_proxy_server]</span></span>
  >

- <span data-ttu-id="7b7bd-157">**夥伴組織**。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-157">**Partner organization**.</span></span> <span data-ttu-id="7b7bd-158">執行 web 應用程式的夥伴組織要求存取 Azure 中執行的 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-158">A partner organization running a web application that requests access to a web application running in Azure.</span></span> <span data-ttu-id="7b7bd-159">夥伴組織中的同盟伺服器會在本機驗證要求，並提交安全性權杖，當中包含對 Azure 中執行的 AD FS 之宣告。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-159">The federation server at the partner organization authenticates requests locally, and submits security tokens containing claims to AD FS running in Azure.</span></span> <span data-ttu-id="7b7bd-160">在 Azure 中的 AD FS 會驗證安全性權杖，且如果有效，就可將宣告傳遞至 Azure 中執行的 web 應用程式來授權它們。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-160">AD FS in Azure validates the security tokens, and if valid can pass the claims to the web application running in Azure to authorize them.</span></span>

  > [!NOTE]
  > <span data-ttu-id="7b7bd-161">您也可以使用 Azure 閘道來設定 VPN 通道，為受信任的夥伴提供 AD FS 的直接存取。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-161">You can also configure a VPN tunnel using Azure gateway to provide direct access to AD FS for trusted partners.</span></span> <span data-ttu-id="7b7bd-162">從這些夥伴收到的要求不會通過 WAP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-162">Requests received from these partners do not pass through the WAP servers.</span></span>
  >

## <a name="recommendations"></a><span data-ttu-id="7b7bd-163">建議</span><span class="sxs-lookup"><span data-stu-id="7b7bd-163">Recommendations</span></span>

<span data-ttu-id="7b7bd-164">下列建議適用於大部分的案例。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-164">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="7b7bd-165">除非您有特定的需求會覆寫它們，否則請遵循下列建議。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-165">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="networking-recommendations"></a><span data-ttu-id="7b7bd-166">網路功能的建議</span><span class="sxs-lookup"><span data-stu-id="7b7bd-166">Networking recommendations</span></span>

<span data-ttu-id="7b7bd-167">針對每個主控具有靜態私人 IP 位址之 AD FS 和 WAP 伺服器的 VM 設定網路介面。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-167">Configure the network interface for each of the VMs hosting AD FS and WAP servers with static private IP addresses.</span></span>

<span data-ttu-id="7b7bd-168">請勿提供 AD FS VM 公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-168">Do not give the AD FS VMs public IP addresses.</span></span> <span data-ttu-id="7b7bd-169">如需詳細資訊，請參閱[安全性調整](#security-considerations)一節。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-169">For more information, see the [Security considerations](#security-considerations) section.</span></span>

<span data-ttu-id="7b7bd-170">針對每個 AD FS 和 WAP VM 的網路介面，設定偏好及次要網域名稱服務 (DNS) 伺服器，來參考 Active Directory DS VM。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-170">Set the IP address of the preferred and secondary domain name service (DNS) servers for the network interfaces for each AD FS and WAP VM to reference the Active Directory DS VMs.</span></span> <span data-ttu-id="7b7bd-171">Active Directory DS VM 應執行 DNS。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-171">The Active Directory DS VMs should be running DNS.</span></span> <span data-ttu-id="7b7bd-172">這是讓每個 VM 加入網域的必要步驟。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-172">This step is necessary to enable each VM to join the domain.</span></span>

### <a name="ad-fs-installation"></a><span data-ttu-id="7b7bd-173">AD FS 安裝</span><span class="sxs-lookup"><span data-stu-id="7b7bd-173">AD FS installation</span></span>

<span data-ttu-id="7b7bd-174">[部署同盟伺服器陣列][Deploying_a_federation_server_farm]文章會提供安裝和設定 AD FS 的詳細指示。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-174">The article [Deploying a Federation Server Farm][Deploying_a_federation_server_farm] provides detailed instructions for installing and configuring AD FS.</span></span> <span data-ttu-id="7b7bd-175">在伺服器陣列中設定第一部 AD FS 伺服器之前，請執行下列工作：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-175">Perform the following tasks before configuring the first AD FS server in the farm:</span></span>

1. <span data-ttu-id="7b7bd-176">取得公開受信任的憑證以執行伺服器驗證。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-176">Obtain a publicly trusted certificate for performing server authentication.</span></span> <span data-ttu-id="7b7bd-177">主體名稱必須包含用戶端用來存取同盟服務的名稱。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-177">The *subject name* must contain the name clients use to access the federation service.</span></span> <span data-ttu-id="7b7bd-178">這可以是負載平衡器的已註冊 DNS 名稱，例如 adfs.contoso.com (基於安全性理由，請避免使用萬用字元名稱，例如 \*.contoso.com)。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-178">This can be the DNS name registered for the load balancer, for example, *adfs.contoso.com* (avoid using wildcard names such as \**.contoso.com*, for security reasons).</span></span> <span data-ttu-id="7b7bd-179">在所有 AD FS 伺服器 VM 上使用相同的憑證。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-179">Use the same certificate on all AD FS server VMs.</span></span> <span data-ttu-id="7b7bd-180">您可以向受信任的憑證授權單位購買憑證，但是如果貴組織是使用 Active Directory 憑證服務，您就可以建立自己的憑證。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-180">You can purchase a certificate from a trusted certification authority, but if your organization uses Active Directory Certificate Services you can create your own.</span></span>

    <span data-ttu-id="7b7bd-181">主體別名會由裝置註冊服務 (DRS) 用來啟用來自外部裝置的存取。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-181">The *subject alternative name* is used by the device registration service (DRS) to enable access from external devices.</span></span> <span data-ttu-id="7b7bd-182">此形式應該是 enterpriseregistration.contoso.com。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-182">This should be of the form *enterpriseregistration.contoso.com*.</span></span>

    <span data-ttu-id="7b7bd-183">如需詳細資訊，請參閱[取得和設定 AD FS 的安全通訊端層 (SSL) 憑證][adfs_certificates]。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-183">For more information, see [Obtain and Configure a Secure Sockets Layer (SSL) Certificate for AD FS][adfs_certificates].</span></span>

2. <span data-ttu-id="7b7bd-184">在網域控制站上，產生金鑰發佈服務的新根金鑰。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-184">On the domain controller, generate a new root key for the Key Distribution Service.</span></span> <span data-ttu-id="7b7bd-185">將有效時間設為目前時間減去 10 小時 (這個設定可減少跨網域發佈及同步處理金鑰時可能會發生的延遲)。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-185">Set the effective time to the current time minus 10 hours (this configuration reduces the delay that can occur in distributing and synchronizing keys across the domain).</span></span> <span data-ttu-id="7b7bd-186">若要支援建立用來執行 AD FS 服務的群組服務帳戶，此為必要步驟。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-186">This step is necessary to support creating the group service account that is used to run the AD FS service.</span></span> <span data-ttu-id="7b7bd-187">下列 PowerShell 命令會顯示如何執行這項操作的範例：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-187">The following PowerShell command shows an example of how to do this:</span></span>

    ```powershell
    Add-KdsRootKey -EffectiveTime (Get-Date).AddHours(-10)
    ```

3. <span data-ttu-id="7b7bd-188">將每個 AD FS 伺服器 VM 新增至網域。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-188">Add each AD FS server VM to the domain.</span></span>

> [!NOTE]
> <span data-ttu-id="7b7bd-189">若要安裝 AD FS，針對網域執行主要網域控制站 (PDC) 模擬器彈性單一主機作業 (FSMO) 角色的網域控制站必須執行且從 AD FS VM 存取。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-189">To install AD FS, the domain controller running the primary domain controller (PDC) emulator flexible single master operation (FSMO) role for the domain must be running and accessible from the AD FS VMs.</span></span> <span data-ttu-id="7b7bd-190"><<RBC：是否有方法可讓此較少重複？>></span><span class="sxs-lookup"><span data-stu-id="7b7bd-190"><<RBC: Is there a way to make this less repetitive?>></span></span>
>

### <a name="ad-fs-trust"></a><span data-ttu-id="7b7bd-191">AD FS 信任</span><span class="sxs-lookup"><span data-stu-id="7b7bd-191">AD FS trust</span></span>

<span data-ttu-id="7b7bd-192">在 AD FS 安裝與任何夥伴組織的同盟伺服器之間建立同盟信任。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-192">Establish federation trust between your AD FS installation, and the federation servers of any partner organizations.</span></span> <span data-ttu-id="7b7bd-193">設定篩選和對應所需的任何宣告。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-193">Configure any claims filtering and mapping required.</span></span>

- <span data-ttu-id="7b7bd-194">每個夥伴組織的 DevOps 人員必須新增信賴憑證者信任，才能透過 AD FS 伺服器存取 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-194">DevOps staff at each partner organization must add a relying party trust for the web applications accessible through your AD FS servers.</span></span>
- <span data-ttu-id="7b7bd-195">貴組織中的 DevOps 人員必須設定宣告提供者信任，才能讓 AD FS 伺服器信任夥伴組織所提供的宣告。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-195">DevOps staff in your organization must configure claims-provider trust to enable your AD FS servers to trust the claims that partner organizations provide.</span></span>
- <span data-ttu-id="7b7bd-196">貴組織中的 DevOps 人員也必須設定 AD FS，才能將宣告傳遞至貴組織的 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-196">DevOps staff in your organization must also configure AD FS to pass claims on to your organization's web applications.</span></span>

<span data-ttu-id="7b7bd-197">如需詳細資訊，請參閱[建立同盟信任][establishing-federation-trust]。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-197">For more information, see [Establishing Federation Trust][establishing-federation-trust].</span></span>

<span data-ttu-id="7b7bd-198">透過 WAP 伺服器使用預先驗證來發佈貴組織的 web 應用程式，並讓外部夥伴可使用它們。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-198">Publish your organization's web applications and make them available to external partners by using preauthentication through the WAP servers.</span></span> <span data-ttu-id="7b7bd-199">如需詳細資訊，請參閱[使用 AD FS 預先驗證來發佈應用程式][publish_applications_using_AD_FS_preauthentication]</span><span class="sxs-lookup"><span data-stu-id="7b7bd-199">For more information, see [Publish Applications using AD FS Preauthentication][publish_applications_using_AD_FS_preauthentication]</span></span>

<span data-ttu-id="7b7bd-200">AD FS 支援權杖轉換和增強指定。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-200">AD FS supports token transformation and augmentation.</span></span> <span data-ttu-id="7b7bd-201">Azure Active Directory 不提供這項功能。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-201">Azure Active Directory does not provide this feature.</span></span> <span data-ttu-id="7b7bd-202">利用 AD FS，當您設定信任關係時，可以：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-202">With AD FS, when you set up the trust relationships, you can:</span></span>

- <span data-ttu-id="7b7bd-203">設定授權規則的宣告轉換。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-203">Configure claim transformations for authorization rules.</span></span> <span data-ttu-id="7b7bd-204">例如，您可以將來自非 Microsoft 夥伴組織使用之表示法的群組安全性對應到 Active Directory DS 可在貴組織中授權的項目。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-204">For example, you can map group security from a representation used by a non-Microsoft partner organization to something that that Active Directory DS can authorize in your organization.</span></span>
- <span data-ttu-id="7b7bd-205">將宣告從一種格式轉換成另一種格式。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-205">Transform claims from one format to another.</span></span> <span data-ttu-id="7b7bd-206">例如，如果您的應用程式只支援 SAML 1.1 宣告，您可以從 SAML 2.0 對應到 SAML 1.1。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-206">For example, you can map from SAML 2.0 to SAML 1.1 if your application only supports SAML 1.1 claims.</span></span>

### <a name="ad-fs-monitoring"></a><span data-ttu-id="7b7bd-207">AD FS 監視</span><span class="sxs-lookup"><span data-stu-id="7b7bd-207">AD FS monitoring</span></span>

<span data-ttu-id="7b7bd-208">[適用於 Active Directory 同盟服務 2012 R2 的 Microsoft System Center Management 套件][oms-adfs-pack]會提供適用於同盟伺服器的 AD FS 部署之主動式及回應式監視。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-208">The [Microsoft System Center Management Pack for Active Directory Federation Services 2012 R2][oms-adfs-pack] provides both proactive and reactive monitoring of your AD FS deployment for the federation server.</span></span> <span data-ttu-id="7b7bd-209">此管理組件會監視：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-209">This management pack monitors:</span></span>

- <span data-ttu-id="7b7bd-210">AD FS 服務在其事件記錄中記錄的事件。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-210">Events that the AD FS service records in its event logs.</span></span>
- <span data-ttu-id="7b7bd-211">AD FS 效能計數器收集的效能資料。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-211">The performance data that the AD FS performance counters collect.</span></span>
- <span data-ttu-id="7b7bd-212">AD FS 系統和 web 應用程式 (信賴憑證者) 的整體健康情況，並提供重大問題和警告的警示。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-212">The overall health of the AD FS system and web applications (relying parties), and provides alerts for critical issues and warnings.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="7b7bd-213">延展性考量</span><span class="sxs-lookup"><span data-stu-id="7b7bd-213">Scalability considerations</span></span>

<span data-ttu-id="7b7bd-214">下列考量摘要自[規劃 AD FS 部署][plan-your-adfs-deployment]文章，提供一個調整 AD FS 伺服器陣列大小的起始點：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-214">The following considerations, summarized from the article [Plan your AD FS deployment][plan-your-adfs-deployment], give a starting point for sizing AD FS farms:</span></span>

- <span data-ttu-id="7b7bd-215">如果您的使用者低於 1000 個，請勿建立專用的伺服器，而是改為在雲端中的每個 Active Directory DS 伺服器上安裝 AD FS。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-215">If you have fewer than 1000 users, do not create dedicated servers, but instead install AD FS on each of the Active Directory DS servers in the cloud.</span></span> <span data-ttu-id="7b7bd-216">請確定您有至少兩部 Active Directory DS 伺服器以維持可用性。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-216">Make sure that you have at least two Active Directory DS servers to maintain availability.</span></span> <span data-ttu-id="7b7bd-217">建立單一 WAP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-217">Create a single WAP server.</span></span>
- <span data-ttu-id="7b7bd-218">如果您的使用者介於 1000 和 15000 個之間，請建立兩部專用的 AD FS 伺服器和兩部專用的 WAP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-218">If you have between 1000 and 15000 users, create two dedicated AD FS servers and two dedicated WAP servers.</span></span>
- <span data-ttu-id="7b7bd-219">如果您的使用者介於 15000 與 60000 個之間，請建立三到五部專用的 AD FS 伺服器，和至少兩部專用的 WAP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-219">If you have between 15000 and 60000 users, create between three and five dedicated AD FS servers and at least two dedicated WAP servers.</span></span>

<span data-ttu-id="7b7bd-220">這些考量是假設您在 Azure 中使用雙四核心 VM (標準 D4_v2 或更新版本) 的大小。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-220">These considerations assume that you are using dual quad-core VM (Standard D4_v2, or better) sizes in Azure.</span></span>

<span data-ttu-id="7b7bd-221">如果您是使用 Windows 內部資料庫來儲存 AD FS 組態資料，在伺服器陣列中就會限制為八個 AD FS 伺服器。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-221">If you are using the Windows Internal Database to store AD FS configuration data, you are limited to eight AD FS servers in the farm.</span></span> <span data-ttu-id="7b7bd-222">如果您預期未來會需要更多，請使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-222">If you anticipate that you will need more in the future, use SQL Server.</span></span> <span data-ttu-id="7b7bd-223">如需詳細資訊，請參閱 [AD FS 設定資料庫的角色][adfs-configuration-database]。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-223">For more information, see [The Role of the AD FS Configuration Database][adfs-configuration-database].</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="7b7bd-224">可用性考量</span><span class="sxs-lookup"><span data-stu-id="7b7bd-224">Availability considerations</span></span>

<span data-ttu-id="7b7bd-225">使用至少兩部伺服器建立 AD FS 伺服器陣列，以提升服務的可用性。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-225">Create an AD FS farm with at least two servers to increase availability of the service.</span></span> <span data-ttu-id="7b7bd-226">在伺服器陣列中針對每個 AD FS VM 使用不同的儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-226">Use different storage accounts for each AD FS VM in the farm.</span></span> <span data-ttu-id="7b7bd-227">這種方式有助於確保單一儲存體帳戶中的失敗不會使整個伺服器陣列無法存取。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-227">This approach helps to ensure that a failure in a single storage account does not make the entire farm inaccessible.</span></span>

<span data-ttu-id="7b7bd-228">建立AD FS 和 WAP VM 的個別 Azure 可用性設定組。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-228">Create separate Azure availability sets for the AD FS and WAP VMs.</span></span> <span data-ttu-id="7b7bd-229">請確保每個集合中至少有兩個 VM。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-229">Ensure that there are at least two VMs in each set.</span></span> <span data-ttu-id="7b7bd-230">每個可用性設定組必須至少有兩個更新網域和兩個容錯網域。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-230">Each availability set must have at least two update domains and two fault domains.</span></span>

<span data-ttu-id="7b7bd-231">設定 AD FS VM 和 WAP VM 的負載平衡器，如下所示：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-231">Configure the load balancers for the AD FS VMs and WAP VMs as follows:</span></span>

- <span data-ttu-id="7b7bd-232">使用 Azure 負載平衡器來提供 WAP VM 的外部存取權，以及內部負載平衡器可在伺服器陣列中的 AD FS 伺服器之間發佈負載。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-232">Use an Azure load balancer to provide external access to the WAP VMs, and an internal load balancer to distribute the load across the AD FS servers in the farm.</span></span>
- <span data-ttu-id="7b7bd-233">只會將出現在連接埠 443 (HTTPS) 上的流量傳遞至 AD FS/WAP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-233">Only pass traffic appearing on port 443 (HTTPS) to the AD FS/WAP servers.</span></span>
- <span data-ttu-id="7b7bd-234">為負載平衡器提供靜態 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-234">Give the load balancer a static IP address.</span></span>
- <span data-ttu-id="7b7bd-235">使用 HTTP 針對 `/adfs/probe` 建立健康情況探查。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-235">Create a health probe using HTTP against `/adfs/probe`.</span></span> <span data-ttu-id="7b7bd-236">如需詳細資訊，請參閱[硬體負載平衡器健康情況檢查和 Web 應用程式 Proxy / AD FS 2012 R2](https://blogs.technet.microsoft.com/applicationproxyblog/2014/10/17/hardware-load-balancer-health-checks-and-web-application-proxy-ad-fs-2012-r2/)。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-236">For more information, see [Hardware Load Balancer Health Checks and Web Application Proxy / AD FS 2012 R2](https://blogs.technet.microsoft.com/applicationproxyblog/2014/10/17/hardware-load-balancer-health-checks-and-web-application-proxy-ad-fs-2012-r2/).</span></span>

  > [!NOTE]
  > <span data-ttu-id="7b7bd-237">AD FS 伺服器會使用伺服器名稱指示 (SNI) 通訊協定，因此使用 HTTPS 端點從負載平衡器嘗試探查就會失敗。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-237">AD FS servers use the Server Name Indication (SNI) protocol, so attempting to probe using an HTTPS endpoint from the load balancer fails.</span></span>
  >

- <span data-ttu-id="7b7bd-238">將 DNS A 記錄新增到 AD FS 負載平衡器的網域。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-238">Add a DNS *A* record to the domain for the AD FS load balancer.</span></span> <span data-ttu-id="7b7bd-239">指定負載平衡器的 IP 位址，並在網域 (例如 adfs.contoso.com) 中為其提供名稱。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-239">Specify the IP address of the load balancer, and give it a name in the domain (such as adfs.contoso.com).</span></span> <span data-ttu-id="7b7bd-240">這是用戶端和 WAP 伺服器用來存取 AD FS 伺服器陣列的名稱。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-240">This is the name clients and the WAP servers use to access the AD FS server farm.</span></span>

<span data-ttu-id="7b7bd-241">您可以使用 SQL Server 或 Windows 內部資料庫來保存 AD FS 組態資訊。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-241">You can use either SQL Server or the Windows Internal Database to hold AD FS configuration information.</span></span> <span data-ttu-id="7b7bd-242">Windows 內部資料庫會提供基本的備援性。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-242">The Windows Internal Database provides basic redundancy.</span></span> <span data-ttu-id="7b7bd-243">變更只會直接寫入 AD FS 叢集中的其中一個 AD FS 資料庫，而其他伺服器會使用提取複寫，讓其資料庫保持最新狀態。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-243">Changes are written directly to only one of the AD FS databases in the AD FS cluster, while the other servers use pull replication to keep their databases up to date.</span></span> <span data-ttu-id="7b7bd-244">使用 SQL Server，可以在使用容錯移轉叢集或鏡像時提供完整的資料庫備援性和高可用性。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-244">Using SQL Server can provide full database redundancy and high availability using failover clustering or mirroring.</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="7b7bd-245">管理性考量</span><span class="sxs-lookup"><span data-stu-id="7b7bd-245">Manageability considerations</span></span>

<span data-ttu-id="7b7bd-246">DevOps 人員應該準備好執行下列工作：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-246">DevOps staff should be prepared to perform the following tasks:</span></span>

- <span data-ttu-id="7b7bd-247">管理同盟伺服器，包括管理 AD FS 伺服器陣列、管理同盟伺服器上的信任原則，和管理同盟服務所使用的憑證。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-247">Managing the federation servers, including managing the AD FS farm, managing trust policy on the federation servers, and managing the certificates used by the federation services.</span></span>
- <span data-ttu-id="7b7bd-248">管理 WAP 伺服器包括管理 WAP 伺服器陣列和憑證。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-248">Managing the WAP servers including managing the WAP farm and certificates.</span></span>
- <span data-ttu-id="7b7bd-249">管理 web 應用程式，包括設定信賴憑證者、驗證方法及宣告對應。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-249">Managing web applications including configuring relying parties, authentication methods, and claims mappings.</span></span>
- <span data-ttu-id="7b7bd-250">備份 AD FS 元件。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-250">Backing up AD FS components.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="7b7bd-251">安全性考量</span><span class="sxs-lookup"><span data-stu-id="7b7bd-251">Security considerations</span></span>

<span data-ttu-id="7b7bd-252">AD FS 會使用 HTTPS，因此子網路若包含 web 層 VM，請確定其 NSG 規則允許 HTTPS 要求。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-252">AD FS uses HTTPS, so make sure that the NSG rules for the subnet containing the web tier VMs permit HTTPS requests.</span></span> <span data-ttu-id="7b7bd-253">可從內部部署網路、包含 web 層的子網路、商業層、資料層、私人 DMZ、公用 DMZ，以及包含 AD FS 伺服器的子網路等起始這些要求。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-253">These requests can originate from the on-premises network, the subnets containing the web tier, business tier, data tier, private DMZ, public DMZ, and the subnet containing the AD FS servers.</span></span>

<span data-ttu-id="7b7bd-254">防止 AD FS 伺服器直接暴露在網際網路。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-254">Prevent direct exposure of the AD FS servers to the Internet.</span></span> <span data-ttu-id="7b7bd-255">AD FS 伺服器是已加入網域的電腦，具有完整權限可授與安全性權杖。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-255">AD FS servers are domain-joined computers that have full authorization to grant security tokens.</span></span> <span data-ttu-id="7b7bd-256">如果伺服器遭到入侵，惡意使用者可以向所有 web 應用程式以及所有受到 AD FS 保護的同盟伺服器發出完整存取權杖。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-256">If a server is compromised, a malicious user can issue full access tokens to all web applications and to all federation servers that are protected by AD FS.</span></span> <span data-ttu-id="7b7bd-257">如果您系統必須處理的要求不是從受信任夥伴站台連線的外部使用者，請使用 WAP 伺服器來處理這些要求。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-257">If your system must handle requests from external users not connecting from trusted partner sites, use WAP servers to handle these requests.</span></span> <span data-ttu-id="7b7bd-258">如需詳細資訊，請參閱[要將同盟伺服器 Proxy 放置在何處][where-to-place-an-fs-proxy]。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-258">For more information, see [Where to Place a Federation Server Proxy][where-to-place-an-fs-proxy].</span></span>

<span data-ttu-id="7b7bd-259">將 AD FS 伺服器和 WAP 伺服器與其本身的防火牆放在不同的子網路中。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-259">Place AD FS servers and WAP servers in separate subnets with their own firewalls.</span></span> <span data-ttu-id="7b7bd-260">您可以使用 NSG 規則來定義防火牆規則。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-260">You can use NSG rules to define firewall rules.</span></span> <span data-ttu-id="7b7bd-261">所有防火牆都應該允許連接埠 443 (HTTPS) 流量。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-261">All firewalls should allow traffic on port 443 (HTTPS).</span></span>

<span data-ttu-id="7b7bd-262">限制直接登入存取 AD FS 和 WAP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-262">Restrict direct sign in access to the AD FS and WAP servers.</span></span> <span data-ttu-id="7b7bd-263">應該只有 DevOps 工作人員能夠連線。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-263">Only DevOps staff should be able to connect.</span></span> <span data-ttu-id="7b7bd-264">請勿將 WAP 伺服器加入網域。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-264">Do not join the WAP servers to the domain.</span></span>

<span data-ttu-id="7b7bd-265">請考慮使用一組網路虛擬設備，可就稽核目的，記錄關於流量周遊虛擬網路邊緣的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-265">Consider using a set of network virtual appliances that logs detailed information on traffic traversing the edge of your virtual network for auditing purposes.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="7b7bd-266">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="7b7bd-266">Deploy the solution</span></span>

<span data-ttu-id="7b7bd-267">適用於此架構的部署可在 [GitHub][github] 上取得。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-267">A deployment for this architecture is available on [GitHub][github].</span></span> <span data-ttu-id="7b7bd-268">請注意，整個部署最多可能需要兩個小時，其中包括建立 VPN 閘道和執行設定 Active Directory 和 AD FS 的指令碼。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-268">Note that the entire deployment can take up to two hours, which includes creating the VPN gateway and running the scripts that configure Active Directory and AD FS.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="7b7bd-269">必要條件</span><span class="sxs-lookup"><span data-stu-id="7b7bd-269">Prerequisites</span></span>

1. <span data-ttu-id="7b7bd-270">複製、派生或下載適用於 [GitHub 存放庫](https://github.com/mspnp/identity-reference-architectures)的 zip 檔案。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-270">Clone, fork, or download the zip file for the [GitHub repository](https://github.com/mspnp/identity-reference-architectures).</span></span>

1. <span data-ttu-id="7b7bd-271">安裝 [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest)。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-271">Install [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest).</span></span>

1. <span data-ttu-id="7b7bd-272">安裝 [Azure 建置組塊](https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks) npm 封裝。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-272">Install the [Azure building blocks](https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks) npm package.</span></span>

   ```bash
   npm install -g @mspnp/azure-building-blocks
   ```

1. <span data-ttu-id="7b7bd-273">從命令提示字元、Bash 提示字元或 PowerShell 提示字元中登入 Azure 帳戶，如下所示：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-273">From a command prompt, bash prompt, or PowerShell prompt, sign into your Azure account as follows:</span></span>

   ```bash
   az login
   ```

### <a name="deploy-the-simulated-on-premises-datacenter"></a><span data-ttu-id="7b7bd-274">部署模擬的內部部署資料中心</span><span class="sxs-lookup"><span data-stu-id="7b7bd-274">Deploy the simulated on-premises datacenter</span></span>

1. <span data-ttu-id="7b7bd-275">巡覽至 GitHub 存放庫的 `adfs` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-275">Navigate to the `adfs` folder of the GitHub repository.</span></span>

1. <span data-ttu-id="7b7bd-276">開啟 `onprem.json` 檔案。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-276">Open the `onprem.json` file.</span></span> <span data-ttu-id="7b7bd-277">搜尋 `adminPassword`、`Password` 和 `SafeModeAdminPassword` 的執行個體，並更新密碼。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-277">Search for instances of `adminPassword`, `Password`, and `SafeModeAdminPassword` and update the passwords.</span></span>

1. <span data-ttu-id="7b7bd-278">執行下列命令，並等待部署完成：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-278">Run the following command and wait for the deployment to finish:</span></span>

    ```bash
    azbb -s <subscription_id> -g <resource group> -l <location> -p onprem.json --deploy
    ```

### <a name="deploy-the-azure-infrastructure"></a><span data-ttu-id="7b7bd-279">部署 Azure 基礎結構</span><span class="sxs-lookup"><span data-stu-id="7b7bd-279">Deploy the Azure infrastructure</span></span>

1. <span data-ttu-id="7b7bd-280">開啟 `azure.json` 檔案。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-280">Open the `azure.json` file.</span></span>  <span data-ttu-id="7b7bd-281">搜尋 `adminPassword` 和 `Password` 的執行個體，並新增密碼的值。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-281">Search for instances of `adminPassword` and `Password` and add values for the passwords.</span></span>

1. <span data-ttu-id="7b7bd-282">執行下列命令，並等待部署完成：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-282">Run the following command and wait for the deployment to finish:</span></span>

    ```bash
    azbb -s <subscription_id> -g <resource group> -l <location> -p azure.json --deploy
    ```

### <a name="set-up-the-ad-fs-farm"></a><span data-ttu-id="7b7bd-283">設定 AD FS 伺服器陣列</span><span class="sxs-lookup"><span data-stu-id="7b7bd-283">Set up the AD FS farm</span></span>

1. <span data-ttu-id="7b7bd-284">開啟 `adfs-farm-first.json` 檔案。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-284">Open the `adfs-farm-first.json` file.</span></span>  <span data-ttu-id="7b7bd-285">搜尋 `AdminPassword` 並取代預設的密碼。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-285">Search for `AdminPassword` and replace the default password.</span></span>

1. <span data-ttu-id="7b7bd-286">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-286">Run the following command:</span></span>

    ```bash
    azbb -s <subscription_id> -g <resource group> -l <location> -p adfs-farm-first.json --deploy
    ```

1. <span data-ttu-id="7b7bd-287">開啟 `adfs-farm-rest.json` 檔案。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-287">Open the `adfs-farm-rest.json` file.</span></span>  <span data-ttu-id="7b7bd-288">搜尋 `AdminPassword` 並取代預設的密碼。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-288">Search for `AdminPassword` and replace the default password.</span></span>

1. <span data-ttu-id="7b7bd-289">執行下列命令，並等待部署完成：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-289">Run the following command and wait for the deployment to finish:</span></span>

    ```bash
    azbb -s <subscription_id> -g <resource group> -l <location> -p adfs-farm-rest.json --deploy
    ```

### <a name="configure-ad-fs-part-1"></a><span data-ttu-id="7b7bd-290">設定 AD FS (第一部分)</span><span class="sxs-lookup"><span data-stu-id="7b7bd-290">Configure AD FS (part 1)</span></span>

1. <span data-ttu-id="7b7bd-291">開啟遠端桌面工作階段以連線至名為 `ra-adfs-jb-vm1` 的 VM，也就是跳板 (jumpbox) VM。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-291">Open a remote desktop session to the VM named `ra-adfs-jb-vm1`, which is the jumpbox VM.</span></span> <span data-ttu-id="7b7bd-292">使用者名稱為 `testuser`。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-292">The user name is `testuser`.</span></span>

1. <span data-ttu-id="7b7bd-293">從跳板中，開啟遠端桌面工作階段以連線至名為 `ra-adfs-proxy-vm1` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-293">From the jumpbox, open a remote desktop session to the VM named `ra-adfs-proxy-vm1`.</span></span> <span data-ttu-id="7b7bd-294">私人 IP 位址為 10.0.6.4。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-294">The private IP address is 10.0.6.4.</span></span>

1. <span data-ttu-id="7b7bd-295">從此遠端桌面工作階段，執行 [PowerShell ISE](/powershell/scripting/components/ise/windows-powershell-integrated-scripting-environment--ise-)。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-295">From this remote desktop session, run the [PowerShell ISE](/powershell/scripting/components/ise/windows-powershell-integrated-scripting-environment--ise-).</span></span>

1. <span data-ttu-id="7b7bd-296">在 PowerShell 中，瀏覽至下列目錄：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-296">In PowerShell, navigate to the following directory:</span></span>

    ```powershell
    C:\Packages\Plugins\Microsoft.Powershell.DSC\2.77.0.0\DSCWork\adfs-v2.0
    ```

1. <span data-ttu-id="7b7bd-297">將以下程式碼貼到指令碼窗格中並執行：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-297">Paste the following code into a script pane and run it:</span></span>

    ```powershell
    . .\adfs-webproxy.ps1
    $cd = @{
        AllNodes = @(
            @{
                NodeName = 'localhost'
                PSDscAllowPlainTextPassword = $true
                PSDscAllowDomainUser = $true
            }
        )
    }

    $c1 = Get-Credential -UserName testuser -Message "Enter password"
    InstallWebProxyApp -DomainName contoso.com -FederationName adfs.contoso.com -WebApplicationProxyName "Contoso App" -AdminCreds $c1 -ConfigurationData $cd
    Start-DscConfiguration .\InstallWebProxyApp
    ```

    <span data-ttu-id="7b7bd-298">在 `Get-Credential` 提示中，輸入部署參數檔案中指定的密碼。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-298">At the `Get-Credential` prompt, enter the password that you specified in the deployment parameter file.</span></span>

1. <span data-ttu-id="7b7bd-299">執行下列命令來監視 [DSC](/powershell/dsc/overview/overview) 設定的進度：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-299">Run the following command to monitor the progress of the [DSC](/powershell/dsc/overview/overview) configuration:</span></span>

    ```powershell
    Get-DscConfigurationStatus
    ```

    <span data-ttu-id="7b7bd-300">若要達到一致，可能需要幾分鐘的時間。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-300">It can take several minutes to reach consistency.</span></span> <span data-ttu-id="7b7bd-301">在此期間，您可能會看到來自命令的錯誤。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-301">During this time, you may see errors from the command.</span></span> <span data-ttu-id="7b7bd-302">設定成功時，輸出會如下列所示：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-302">When the configuration succeeds, the output should look similar to the following:</span></span>

    ```powershell
    PS C:\Packages\Plugins\Microsoft.Powershell.DSC\2.77.0.0\DSCWork\adfs-v2.0> Get-DscConfigurationStatus

    Status     StartDate                 Type            Mode  RebootRequested      NumberOfResources
    ------     ---------                 ----            ----  ---------------      -----------------
    Success    12/17/2018 8:21:09 PM     Consistency     PUSH  True                 4
    ```

### <a name="configure-ad-fs-part-2"></a><span data-ttu-id="7b7bd-303">設定 AD FS (第二部分)</span><span class="sxs-lookup"><span data-stu-id="7b7bd-303">Configure AD FS (part 2)</span></span>

1. <span data-ttu-id="7b7bd-304">從跳板中，開啟遠端桌面工作階段以連線至名為 `ra-adfs-proxy-vm2` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-304">From the jumpbox, open a remote desktop session to the VM named `ra-adfs-proxy-vm2`.</span></span> <span data-ttu-id="7b7bd-305">私人 IP 位址為 10.0.6.5。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-305">The private IP address is 10.0.6.5.</span></span>

1. <span data-ttu-id="7b7bd-306">從此遠端桌面工作階段，執行 [PowerShell ISE](/powershell/scripting/components/ise/windows-powershell-integrated-scripting-environment--ise-)。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-306">From this remote desktop session, run the [PowerShell ISE](/powershell/scripting/components/ise/windows-powershell-integrated-scripting-environment--ise-).</span></span>

1. <span data-ttu-id="7b7bd-307">瀏覽到下列目錄：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-307">Navigate to the following directory:</span></span>

    ```powershell
    C:\Packages\Plugins\Microsoft.Powershell.DSC\2.77.0.0\DSCWork\adfs-v2.0
    ```

1. <span data-ttu-id="7b7bd-308">將下列內容貼到指令碼窗格並執行指令碼：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-308">Past the following in a script pane and run the script:</span></span>

    ```powershell
    . .\adfs-webproxy-rest.ps1
    $cd = @{
        AllNodes = @(
            @{
                NodeName = 'localhost'
                PSDscAllowPlainTextPassword = $true
                PSDscAllowDomainUser = $true
            }
        )
    }

    $c1 = Get-Credential -UserName testuser -Message "Enter password"
    InstallWebProxy -DomainName contoso.com -FederationName adfs.contoso.com -WebApplicationProxyName "Contoso App" -AdminCreds $c1 -ConfigurationData $cd
    Start-DscConfiguration .\InstallWebProxy
    ```

    <span data-ttu-id="7b7bd-309">在 `Get-Credential` 提示中，輸入部署參數檔案中指定的密碼。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-309">At the `Get-Credential` prompt, enter the password that you specified in the deployment parameter file.</span></span>

1. <span data-ttu-id="7b7bd-310">執行下列命令來監視 DSC 設定的進度：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-310">Run the following command to monitor the progress of the DSC configuration:</span></span>

    ```powershell
    Get-DscConfigurationStatus
    ```

    <span data-ttu-id="7b7bd-311">若要達到一致，可能需要幾分鐘的時間。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-311">It can take several minutes to reach consistency.</span></span> <span data-ttu-id="7b7bd-312">在此期間，您可能會看到來自命令的錯誤。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-312">During this time, you may see errors from the command.</span></span> <span data-ttu-id="7b7bd-313">設定成功時，輸出會如下列所示：</span><span class="sxs-lookup"><span data-stu-id="7b7bd-313">When the configuration succeeds, the output should look similar to the following:</span></span>

    ```powershell
    PS C:\Packages\Plugins\Microsoft.Powershell.DSC\2.77.0.0\DSCWork\adfs-v2.0> Get-DscConfigurationStatus

    Status     StartDate                 Type            Mode  RebootRequested      NumberOfResources
    ------     ---------                 ----            ----  ---------------      -----------------
    Success    12/17/2018 8:21:09 PM     Consistency     PUSH  True                 4
    ```

    <span data-ttu-id="7b7bd-314">有時候此 DSC 會失敗。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-314">Sometimes this DSC fails.</span></span> <span data-ttu-id="7b7bd-315">如果狀態檢查顯示 `Status=Failure` 和 `Type=Consistency`，請嘗試重新執行步驟 4。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-315">If the status check shows `Status=Failure` and `Type=Consistency`, try re-running step 4.</span></span>

### <a name="sign-into-ad-fs"></a><span data-ttu-id="7b7bd-316">登入 AD FS</span><span class="sxs-lookup"><span data-stu-id="7b7bd-316">Sign into AD FS</span></span>

1. <span data-ttu-id="7b7bd-317">從跳板中，開啟遠端桌面工作階段以連線至名為 `ra-adfs-adfs-vm1` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-317">From the jumpbox, open a remote desktop session to the VM named `ra-adfs-adfs-vm1`.</span></span> <span data-ttu-id="7b7bd-318">私人 IP 位址為 10.0.5.4。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-318">The private IP address is 10.0.5.4.</span></span>

1. <span data-ttu-id="7b7bd-319">請依照[啟用 Idp 起始登入頁面](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-initiatedsignon#enable-the-idp-intiated-sign-on-page)中的步驟來啟用登入頁面。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-319">Follow the steps in [Enable the Idp-Intiated Sign on page](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-initiatedsignon#enable-the-idp-intiated-sign-on-page) to enable the sign-on page.</span></span>

1. <span data-ttu-id="7b7bd-320">從跳板瀏覽至 `https://adfs.contoso.com/adfs/ls/idpinitiatedsignon.htm`。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-320">From the jump box, browse to `https://adfs.contoso.com/adfs/ls/idpinitiatedsignon.htm`.</span></span> <span data-ttu-id="7b7bd-321">您可能會收到憑證警告，您可以在此測試中忽略該警告。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-321">You may receive a certificate warning that you can ignore for this test.</span></span>

1. <span data-ttu-id="7b7bd-322">確認出現 Contoso Corporation 登入頁面。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-322">Verify that the Contoso Corporation sign-in page appears.</span></span> <span data-ttu-id="7b7bd-323">以 **contoso\testuser** 身分登入。</span><span class="sxs-lookup"><span data-stu-id="7b7bd-323">Sign in as **contoso\testuser**.</span></span>

<!-- links -->
[extending-ad-to-azure]: adds-extend-domain.md

[vm-recommendations]: ../virtual-machines-windows/single-vm.md
[implementing-a-secure-hybrid-network-architecture]: ../dmz/secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ../dmz/secure-vnet-dmz.md
[hybrid-azure-on-prem-vpn]: ../hybrid-networking/vpn.md

[azure-cli]: /azure/azure-resource-manager/xplat-cli-azure-resource-manager
[DRS]: https://technet.microsoft.com/library/dn280945.aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048.aspx
[ADDRS]: https://technet.microsoft.com/library/dn486831.aspx
[plan-your-adfs-deployment]: https://msdn.microsoft.com/library/azure/dn151324.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[adfs_certificates]: https://technet.microsoft.com/library/dn781428(v=ws.11).aspx
[create_service_account_for_adfs_farm]: https://technet.microsoft.com/library/dd807078.aspx
[adfs-configuration-database]: https://technet.microsoft.com/library/ee913581(v=ws.11).aspx
[active-directory-federation-services]: /windows-server/identity/active-directory-federation-services
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[active-directory-federation-services-overview]: https://technet.microsoft.com/library/hh831502(v=ws.11).aspx
[establishing-federation-trust]: https://blogs.msdn.microsoft.com/alextch/2011/06/27/establishing-federation-trust/
[Deploying_a_federation_server_farm]:  /windows-server/identity/ad-fs/deployment/deploying-a-federation-server-farm
[install_and_configure_the_web_application_proxy_server]: https://technet.microsoft.com/library/dn383662.aspx
[publish_applications_using_AD_FS_preauthentication]: https://technet.microsoft.com/library/dn383640.aspx
[managing-adfs-components]: https://technet.microsoft.com/library/cc759026.aspx
[oms-adfs-pack]: https://www.microsoft.com/download/details.aspx?id=41184
[azure-powershell-download]: /powershell/azure/overview
[aad]: /azure/active-directory/
[aadb2c]: /azure/active-directory-b2c/
[adfs-intro]: /azure/active-directory/hybrid/whatis-hybrid-identity
[github]: https://github.com/mspnp/identity-reference-architectures/tree/master/adfs
[adfs_certificates]: https://technet.microsoft.com/library/dn781428(v=ws.11).aspx
[considerations]: ./considerations.md
[visio-download]: https://archcenter.blob.core.windows.net/cdn/identity-architectures.vsdx

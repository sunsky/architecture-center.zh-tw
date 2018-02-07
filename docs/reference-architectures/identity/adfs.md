---
title: "在 Azure 中實作 Active Directory 同盟服務 (AD FS)"
description: "如何在 Azure 中使用 Active Directory 同盟服務授權來實作安全的混合式網路架構。\n指引,vpn 閘道,expressroute,負載平衡器,虛擬網路,active directory"
author: telmosampaio
ms.date: 11/28/2016
pnp.series.title: Identity management
pnp.series.prev: adds-forest
cardTitle: Extend AD FS to Azure
ms.openlocfilehash: b8c9ae0621c087c68d449dd13e60046104c01513
ms.sourcegitcommit: 8ab30776e0c4cdc16ca0dcc881960e3108ad3e94
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2017
---
# <a name="extend-active-directory-federation-services-ad-fs-to-azure"></a><span data-ttu-id="874af-104">將 Active Directory 同盟服務 (AD FS) 擴充至 Azure</span><span class="sxs-lookup"><span data-stu-id="874af-104">Extend Active Directory Federation Services (AD FS) to Azure</span></span>

<span data-ttu-id="874af-105">此參考架構會實作安全的混合式網路，可將您的內部部署網路擴充至 Azure，並使用 [Active Directory 同盟服務 (AD FS)][active-directory-federation-services] 來執行同盟驗證，以及授權 Azure 中執行的元件。</span><span class="sxs-lookup"><span data-stu-id="874af-105">This reference architecture implements a secure hybrid network that extends your on-premises network to Azure and uses [Active Directory Federation Services (AD FS)][active-directory-federation-services] to perform federated authentication and authorization for components running in Azure.</span></span> [<span data-ttu-id="874af-106">**部署這個解決方案**。</span><span class="sxs-lookup"><span data-stu-id="874af-106">**Deploy this solution**.</span></span>](#deploy-the-solution)

<span data-ttu-id="874af-107">[![0]][0]</span><span class="sxs-lookup"><span data-stu-id="874af-107">[![0]][0]</span></span>

<span data-ttu-id="874af-108">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="874af-108">*Download a [Visio file][visio-download] of this architecture.*</span></span>

<span data-ttu-id="874af-109">AD FS 可在內部部署主控，但如果是當中有些組件實作在 Azure 中的混合式應用程式，在雲端中複寫 AD FS 可能會更有效率。</span><span class="sxs-lookup"><span data-stu-id="874af-109">AD FS can be hosted on-premises, but if your application is a hybrid in which some parts are implemented in Azure, it may be more efficient to replicate AD FS in the cloud.</span></span> 

<span data-ttu-id="874af-110">下圖顯示下列情節：</span><span class="sxs-lookup"><span data-stu-id="874af-110">The diagram shows the following scenarios:</span></span>

* <span data-ttu-id="874af-111">夥伴組織中的應用程式程式碼會存取 Azure VNet 內主控的 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="874af-111">Application code from a partner organization accesses a web application hosted inside your Azure VNet.</span></span>
* <span data-ttu-id="874af-112">已外部註冊、且認證儲存在 Active Directory Domain Services (DS) 的使用者，可存取 Azure VNet 內主控的 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="874af-112">An external, registered user with credentials stored inside Active Directory Domain Services (DS) accesses a web application hosted inside your Azure VNet.</span></span>
* <span data-ttu-id="874af-113">使用授權裝置連線至 VNet 的使用者會執行 Azure VNet 內主控的 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="874af-113">A user connected to your VNet using an authorized device executes a web application hosted inside your Azure VNet.</span></span>

<span data-ttu-id="874af-114">此架構的典型使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="874af-114">Typical uses for this architecture include:</span></span>

* <span data-ttu-id="874af-115">部分工作負載在內部部署中執行、部分在 Azure 中執行的混合式應用程式。</span><span class="sxs-lookup"><span data-stu-id="874af-115">Hybrid applications where workloads run partly on-premises and partly in Azure.</span></span>
* <span data-ttu-id="874af-116">使用同盟授權向夥伴組織公開 web 應用程式的解決方案。</span><span class="sxs-lookup"><span data-stu-id="874af-116">Solutions that use federated authorization to expose web applications to partner organizations.</span></span>
* <span data-ttu-id="874af-117">支援從組織防火牆外部執行網頁瀏覽器之存取的系統。</span><span class="sxs-lookup"><span data-stu-id="874af-117">Systems that support access from web browsers running outside of the organizational firewall.</span></span>
* <span data-ttu-id="874af-118">讓使用者能夠從授權的外部裝置 (例如遠端電腦、筆記型電腦和其他行動裝置) 連線來存取 web 應用程式的系統。</span><span class="sxs-lookup"><span data-stu-id="874af-118">Systems that enable users to access to web applications by connecting from authorized external devices such as remote computers, notebooks, and other mobile devices.</span></span> 

<span data-ttu-id="874af-119">此參考架構著重於被動同盟，同盟伺服器會在當中決定如何及何時要驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="874af-119">This reference architecture focuses on *passive federation*, in which the federation servers decide how and when to authenticate a user.</span></span> <span data-ttu-id="874af-120">使用者在應用程式啟動時，要提供登入資訊。</span><span class="sxs-lookup"><span data-stu-id="874af-120">The user provides sign in information when the application is started.</span></span> <span data-ttu-id="874af-121">這項機制最常受網頁瀏覽器使用，且涉及將瀏覽器重新導向至使用者驗證之站台的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="874af-121">This mechanism is most commonly used by web browsers and involves a protocol that redirects the browser to a site where the user authenticates.</span></span> <span data-ttu-id="874af-122">AD FS 也支援主動同盟，其中應用程式會負責提供認證，而不需要使用者互動，但這種情節是在此架構範圍之外。</span><span class="sxs-lookup"><span data-stu-id="874af-122">AD FS also supports *active federation*, where an application takes on responsibility for supplying credentials without further user interaction, but that scenario is outside the scope of this architecture.</span></span>

<span data-ttu-id="874af-123">如需了解其他考量，請參閱[選擇解決方案以整合內部部署 Active Directory 與 Azure][considerations]。</span><span class="sxs-lookup"><span data-stu-id="874af-123">For additional considerations, see [Choose a solution for integrating on-premises Active Directory with Azure][considerations].</span></span> 

## <a name="architecture"></a><span data-ttu-id="874af-124">架構</span><span class="sxs-lookup"><span data-stu-id="874af-124">Architecture</span></span>

<span data-ttu-id="874af-125">此架構會擴充[將 AD DS 延伸至 Azure][extending-ad-to-azure]中所述的實作。</span><span class="sxs-lookup"><span data-stu-id="874af-125">This architecture extends the implementation described in [Extending AD DS to Azure][extending-ad-to-azure].</span></span> <span data-ttu-id="874af-126">它包含下列元件。</span><span class="sxs-lookup"><span data-stu-id="874af-126">It contains the followign components.</span></span>

* <span data-ttu-id="874af-127">**AD DS 子網路**。</span><span class="sxs-lookup"><span data-stu-id="874af-127">**AD DS subnet**.</span></span> <span data-ttu-id="874af-128">AD DS 伺服器會包含在自己的子網路中，且會將網路安全性群組 (NSG) 規則作為防火牆。</span><span class="sxs-lookup"><span data-stu-id="874af-128">The AD DS servers are contained in their own subnet with network security group (NSG) rules acting as a firewall.</span></span>

* <span data-ttu-id="874af-129">**AD DS 伺服器**。</span><span class="sxs-lookup"><span data-stu-id="874af-129">**AD DS servers**.</span></span> <span data-ttu-id="874af-130">在 Azure 中作為 VM 執行的網域控制站。</span><span class="sxs-lookup"><span data-stu-id="874af-130">Domain controllers running as VMs in Azure.</span></span> <span data-ttu-id="874af-131">這些伺服器會提供網域內本機識別身分的驗證。</span><span class="sxs-lookup"><span data-stu-id="874af-131">These servers provide authentication of local identities within the domain.</span></span>

* <span data-ttu-id="874af-132">**AD FS 子網路**。</span><span class="sxs-lookup"><span data-stu-id="874af-132">**AD FS subnet**.</span></span> <span data-ttu-id="874af-133">AD FS 伺服器都位於自己的子網路內，且將 NSG 規則作為防火牆。</span><span class="sxs-lookup"><span data-stu-id="874af-133">The AD FS servers are located within their own subnet with NSG rules acting as a firewall.</span></span>

* <span data-ttu-id="874af-134">**AD FS 伺服器**。</span><span class="sxs-lookup"><span data-stu-id="874af-134">**AD FS servers**.</span></span> <span data-ttu-id="874af-135">AD FS 伺服器會提供同盟授權和驗證。</span><span class="sxs-lookup"><span data-stu-id="874af-135">The AD FS servers provide federated authorization and authentication.</span></span> <span data-ttu-id="874af-136">在此架構中，他們會執行下列工作：</span><span class="sxs-lookup"><span data-stu-id="874af-136">In this architecture, they perform the following tasks:</span></span>
  
  * <span data-ttu-id="874af-137">接收安全性權杖，當中包含由代表夥伴使用者之夥伴同盟伺服器進行的宣告。</span><span class="sxs-lookup"><span data-stu-id="874af-137">Receiving security tokens containing claims made by a partner federation server on behalf of a partner user.</span></span> <span data-ttu-id="874af-138">AD FS 會先驗證權杖為有效後，才能將宣告傳遞至 Azure 中執行的 web 應用程式來授權要求。</span><span class="sxs-lookup"><span data-stu-id="874af-138">AD FS verifies that the tokens are valid before passing the claims to the web application running in Azure to authorize requests.</span></span> 
  
    <span data-ttu-id="874af-139">在 Azure 中執行的 web 應用程式是信賴憑證者。</span><span class="sxs-lookup"><span data-stu-id="874af-139">The web application running in Azure is the *relying party*.</span></span> <span data-ttu-id="874af-140">夥伴同盟伺服器必須發出 web 應用程式可解讀的宣告。</span><span class="sxs-lookup"><span data-stu-id="874af-140">The partner federation server must issue claims that are understood by the web application.</span></span> <span data-ttu-id="874af-141">夥伴同盟伺服器稱為帳戶夥伴，因為它們會代表夥伴組織中的驗證帳戶提交存取要求。</span><span class="sxs-lookup"><span data-stu-id="874af-141">The partner federation servers are referred to as *account partners*, because they submit access requests on behalf of authenticated accounts in the partner organization.</span></span> <span data-ttu-id="874af-142">AD FS 伺服器稱為資源夥伴，因為它們提供資源 (web 應用程式) 的存取權。</span><span class="sxs-lookup"><span data-stu-id="874af-142">The AD FS servers are called *resource partners* because they provide access to resources (the web application).</span></span>

  * <span data-ttu-id="874af-143">驗證並授權來自執行網頁瀏覽器或裝置 (需要 web 應用程式的存取權) 之外部使用者的內送要求，方法是使用 AD DS 和 [Active Directory 裝置註冊服務][ADDRS]。</span><span class="sxs-lookup"><span data-stu-id="874af-143">Authenticating and authorizing incoming requests from external users running a web browser or device that needs access to web applications, by using AD DS and the [Active Directory Device Registration Service][ADDRS].</span></span>
    
  <span data-ttu-id="874af-144">AD FS 伺服器會設定為透過 Azure 負載平衡器存取的伺服器陣列。</span><span class="sxs-lookup"><span data-stu-id="874af-144">The AD FS servers are configured as a farm accessed through an Azure load balancer.</span></span> <span data-ttu-id="874af-145">這個實作能改善可用性和延展性。</span><span class="sxs-lookup"><span data-stu-id="874af-145">This implementation improves availability and scalability.</span></span> <span data-ttu-id="874af-146">AD FS 伺服器不會直接向網際網路公開。</span><span class="sxs-lookup"><span data-stu-id="874af-146">The AD FS servers are not exposed directly to the Internet.</span></span> <span data-ttu-id="874af-147">所有網際網路流量都會透過 AD FS web 應用程式 proxy 伺服器和 DMZ (也稱為周邊網路) 進行篩選。</span><span class="sxs-lookup"><span data-stu-id="874af-147">All Internet traffic is filtered through AD FS web application proxy servers and a DMZ (also referred to as a perimeter network).</span></span>

  <span data-ttu-id="874af-148">如需有關 AD FS 運作方式的詳細資訊，請參閱 [Active Directory 同盟服務概觀][active-directory-federation-services-overview]。</span><span class="sxs-lookup"><span data-stu-id="874af-148">For more information about how AD FS works, see [Active Directory Federation Services Overview][active-directory-federation-services-overview].</span></span> <span data-ttu-id="874af-149">此外，[Azure 中的 AD FS 部署][adfs-intro]文章中包含了詳細的逐步實作。</span><span class="sxs-lookup"><span data-stu-id="874af-149">Also, the article [AD FS deployment in Azure][adfs-intro] contains a detailed step-by-step introduction to implementation.</span></span>

* <span data-ttu-id="874af-150">**AD FS proxy 子網路**。</span><span class="sxs-lookup"><span data-stu-id="874af-150">**AD FS proxy subnet**.</span></span> <span data-ttu-id="874af-151">AD FS proxy 伺服器可以包含在自己的子網路內，且 NSG 規則會提供保護。</span><span class="sxs-lookup"><span data-stu-id="874af-151">The AD FS proxy servers can be contained within their own subnet, with NSG rules providing protection.</span></span> <span data-ttu-id="874af-152">此子網路中的伺服器會透過一組可在 Azure 虛擬網路與網際網路之間提供防火牆的網路虛擬設備，向網路虛擬公開。</span><span class="sxs-lookup"><span data-stu-id="874af-152">The servers in this subnet are exposed to the Internet through a set of network virtual appliances that provide a firewall between your Azure virtual network and the Internet.</span></span>

* <span data-ttu-id="874af-153">**AD FS Web 應用程式 proxy (WAP) 伺服器**。</span><span class="sxs-lookup"><span data-stu-id="874af-153">**AD FS web application proxy (WAP) servers**.</span></span> <span data-ttu-id="874af-154">這些 VM 會作為 AD FS 伺服器，接收來自夥伴組織和外部裝置的內送要求。</span><span class="sxs-lookup"><span data-stu-id="874af-154">These VMs act as AD FS servers for incoming requests from partner organizations and external devices.</span></span> <span data-ttu-id="874af-155">WAP 伺服器會作為篩選條件，保護來自網際網路的直接存取 AD FS 伺服器。</span><span class="sxs-lookup"><span data-stu-id="874af-155">The WAP servers act as a filter, shielding the AD FS servers from direct access from the Internet.</span></span> <span data-ttu-id="874af-156">如同 AD FS 伺服器，在伺服器陣列中使用負載平衡部署 WAP 伺服器，可為您提供比部署獨立伺服器的集合更高的可用性和延展性。</span><span class="sxs-lookup"><span data-stu-id="874af-156">As with the AD FS servers, deploying the WAP servers in a farm with load balancing gives you greater availability and scalability than deploying a collection of stand-alone servers.</span></span>
  
  > [!NOTE]
  > <span data-ttu-id="874af-157">如需安裝 WAP 伺服器的詳細資訊，請參閱[安裝及設定 Web 應用程式 Proxy 伺服器][install_and_configure_the_web_application_proxy_server]</span><span class="sxs-lookup"><span data-stu-id="874af-157">For detailed information about installing WAP servers, see [Install and Configure the Web Application Proxy Server][install_and_configure_the_web_application_proxy_server]</span></span>
  > 
  > 

* <span data-ttu-id="874af-158">**夥伴組織**。</span><span class="sxs-lookup"><span data-stu-id="874af-158">**Partner organization**.</span></span> <span data-ttu-id="874af-159">執行 web 應用程式的夥伴組織要求存取 Azure 中執行的 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="874af-159">A partner organization running a web application that requests access to a web application running in Azure.</span></span> <span data-ttu-id="874af-160">夥伴組織中的同盟伺服器會在本機驗證要求，並提交安全性權杖，當中包含對 Azure 中執行的 AD FS 之宣告。</span><span class="sxs-lookup"><span data-stu-id="874af-160">The federation server at the partner organization authenticates requests locally, and submits security tokens containing claims to AD FS running in Azure.</span></span> <span data-ttu-id="874af-161">在 Azure 中的 AD FS 會驗證安全性權杖，且如果有效，就可將宣告傳遞至 Azure 中執行的 web 應用程式來授權它們。</span><span class="sxs-lookup"><span data-stu-id="874af-161">AD FS in Azure validates the security tokens, and if valid can pass the claims to the web application running in Azure to authorize them.</span></span>
  
  > [!NOTE]
  > <span data-ttu-id="874af-162">您也可以使用 Azure 閘道來設定 VPN 通道，為受信任的夥伴提供 AD FS 的直接存取。</span><span class="sxs-lookup"><span data-stu-id="874af-162">You can also configure a VPN tunnel using Azure gateway to provide direct access to AD FS for trusted partners.</span></span> <span data-ttu-id="874af-163">從這些夥伴收到的要求不會通過 WAP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="874af-163">Requests received from these partners do not pass through the WAP servers.</span></span>
  > 
  > 

<span data-ttu-id="874af-164">如需與 AD FS 不相關之架構組件的詳細資訊，請參閱下列各項：</span><span class="sxs-lookup"><span data-stu-id="874af-164">For more information about the parts of the architecture that are not related to AD FS, see the following:</span></span>
- <span data-ttu-id="874af-165">[在 Azure 中實作安全的混合式網路架構][implementing-a-secure-hybrid-network-architecture]</span><span class="sxs-lookup"><span data-stu-id="874af-165">[Implementing a secure hybrid network architecture in Azure][implementing-a-secure-hybrid-network-architecture]</span></span>
- <span data-ttu-id="874af-166">[在 Azure 中使用網際網路存取實作安全的混合式網路架構][implementing-a-secure-hybrid-network-architecture-with-internet-access]</span><span class="sxs-lookup"><span data-stu-id="874af-166">[Implementing a secure hybrid network architecture with Internet access in Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]</span></span>
- <span data-ttu-id="874af-167">[在 Azure 中使用 Active Directory 身分識別來實作安全的混合式網路架構][extending-ad-to-azure]。</span><span class="sxs-lookup"><span data-stu-id="874af-167">[Implementing a secure hybrid network architecture with Active Directory identities in Azure][extending-ad-to-azure].</span></span>


## <a name="recommendations"></a><span data-ttu-id="874af-168">建議</span><span class="sxs-lookup"><span data-stu-id="874af-168">Recommendations</span></span>

<span data-ttu-id="874af-169">下列建議適用於大部分的案例。</span><span class="sxs-lookup"><span data-stu-id="874af-169">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="874af-170">除非您有特定的需求會覆寫它們，否則請遵循下列建議。</span><span class="sxs-lookup"><span data-stu-id="874af-170">Follow these recommendations unless you have a specific requirement that overrides them.</span></span> 

### <a name="vm-recommendations"></a><span data-ttu-id="874af-171">VM 建議</span><span class="sxs-lookup"><span data-stu-id="874af-171">VM recommendations</span></span>

<span data-ttu-id="874af-172">使用足夠的資源來建立 VM，以處理預期的流量。</span><span class="sxs-lookup"><span data-stu-id="874af-172">Create VMs with sufficient resources to handle the expected volume of traffic.</span></span> <span data-ttu-id="874af-173">使用內部部署主控 AD FS 的現有電腦大小作為起點。</span><span class="sxs-lookup"><span data-stu-id="874af-173">Use the size of the existing machines hosting AD FS on premises as a starting point.</span></span> <span data-ttu-id="874af-174">監視資源使用率。</span><span class="sxs-lookup"><span data-stu-id="874af-174">Monitor the resource utilization.</span></span> <span data-ttu-id="874af-175">您可以調整 VM 大小，並在太大時相應減少。</span><span class="sxs-lookup"><span data-stu-id="874af-175">You can resize the VMs and scale down if they are too large.</span></span>

<span data-ttu-id="874af-176">請遵循[在 Azure 上執行 Windows VM][vm-recommendations]中所列的建議。</span><span class="sxs-lookup"><span data-stu-id="874af-176">Follow the recommendations listed in [Running a Windows VM on Azure][vm-recommendations].</span></span>

### <a name="networking-recommendations"></a><span data-ttu-id="874af-177">網路功能的建議</span><span class="sxs-lookup"><span data-stu-id="874af-177">Networking recommendations</span></span>

<span data-ttu-id="874af-178">針對每個主控具有靜態私人 IP 位址之 AD FS 和 WAP 伺服器的 VM 設定網路介面。</span><span class="sxs-lookup"><span data-stu-id="874af-178">Configure the network interface for each of the VMs hosting AD FS and WAP servers with static private IP addresses.</span></span>

<span data-ttu-id="874af-179">請勿提供 AD FS VM 公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="874af-179">Do not give the AD FS VMs public IP addresses.</span></span> <span data-ttu-id="874af-180">如需詳細資訊，請參閱安全性調整一節。</span><span class="sxs-lookup"><span data-stu-id="874af-180">For more information, see the Security considerations section.</span></span>

<span data-ttu-id="874af-181">針對每個 AD FS 和 WAP VM 的網路介面，設定偏好及次要網域名稱服務 (DNS) 伺服器，來參考 Active Directory DS VM。</span><span class="sxs-lookup"><span data-stu-id="874af-181">Set the IP address of the preferred and secondary domain name service (DNS) servers for the network interfaces for each AD FS and WAP VM to reference the Active Directory DS VMs.</span></span> <span data-ttu-id="874af-182">Active Directory DS VM 應執行 DNS。</span><span class="sxs-lookup"><span data-stu-id="874af-182">The Active Directory DS VMS should be running DNS.</span></span> <span data-ttu-id="874af-183">這是讓每個 VM 加入網域的必要步驟。</span><span class="sxs-lookup"><span data-stu-id="874af-183">This step is necessary to enable each VM to join the domain.</span></span>

### <a name="ad-fs-availability"></a><span data-ttu-id="874af-184">AD FS 可用性</span><span class="sxs-lookup"><span data-stu-id="874af-184">AD FS availability</span></span> 

<span data-ttu-id="874af-185">使用至少兩部伺服器建立 AD FS 伺服器陣列，以提升服務的可用性。</span><span class="sxs-lookup"><span data-stu-id="874af-185">Create an AD FS farm with at least two servers to increase availability of the service.</span></span> <span data-ttu-id="874af-186">在伺服器陣列中針對每個 AD FS VM 使用不同的儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="874af-186">Use different storage accounts for each AD FS VM in the farm.</span></span> <span data-ttu-id="874af-187">這種方式有助於確保單一儲存體帳戶中的失敗不會使整個伺服器陣列無法存取。</span><span class="sxs-lookup"><span data-stu-id="874af-187">This approach helps to ensure that a failure in a single storage account does not make the entire farm inaccessible.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="874af-188">建議使用[受控磁碟](/azure/storage/storage-managed-disks-overview)。</span><span class="sxs-lookup"><span data-stu-id="874af-188">We recommend the use of [managed disks](/azure/storage/storage-managed-disks-overview).</span></span> <span data-ttu-id="874af-189">受控磁碟不需要儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="874af-189">Managed disks do not require a storage account.</span></span> <span data-ttu-id="874af-190">您只需指定磁碟的大小和類型，並以高度可用的方式來部署它。</span><span class="sxs-lookup"><span data-stu-id="874af-190">You simply specify the size and type of disk and it is deployed in a highly available way.</span></span> <span data-ttu-id="874af-191">我們的[參考架構](/azure/architecture/reference-architectures/)目前不會部署受控磁碟，但[範本建置組塊](https://github.com/mspnp/template-building-blocks/wiki)將在第 2 版中更新，以部署受控磁碟。</span><span class="sxs-lookup"><span data-stu-id="874af-191">Our [reference architectures](/azure/architecture/reference-architectures/) do not currently deploy managed disks but the [template building blocks](https://github.com/mspnp/template-building-blocks/wiki) will be updated to deploy managed disks in version 2.</span></span>

<span data-ttu-id="874af-192">建立AD FS 和 WAP VM 的個別 Azure 可用性設定組。</span><span class="sxs-lookup"><span data-stu-id="874af-192">Create separate Azure availability sets for the AD FS and WAP VMs.</span></span> <span data-ttu-id="874af-193">請確保每個集合中至少有兩個 VM。</span><span class="sxs-lookup"><span data-stu-id="874af-193">Ensure that there are at least two VMs in each set.</span></span> <span data-ttu-id="874af-194">每個可用性設定組必須至少有兩個更新網域和兩個容錯網域。</span><span class="sxs-lookup"><span data-stu-id="874af-194">Each availability set must have at least two update domains and two fault domains.</span></span>

<span data-ttu-id="874af-195">設定 AD FS VM 和 WAP VM 的負載平衡器，如下所示：</span><span class="sxs-lookup"><span data-stu-id="874af-195">Configure the load balancers for the AD FS VMs and WAP VMs as follows:</span></span>

* <span data-ttu-id="874af-196">使用 Azure 負載平衡器來提供 WAP VM 的外部存取權，以及內部負載平衡器可在伺服器陣列中的 AD FS 伺服器之間發佈負載。</span><span class="sxs-lookup"><span data-stu-id="874af-196">Use an Azure load balancer to provide external access to the WAP VMs, and an internal load balancer to distribute the load across the AD FS servers in the farm.</span></span>
* <span data-ttu-id="874af-197">只會將出現在連接埠 443 (HTTPS) 上的流量傳遞至 AD FS/WAP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="874af-197">Only pass traffic appearing on port 443 (HTTPS) to the AD FS/WAP servers.</span></span>
* <span data-ttu-id="874af-198">為負載平衡器提供靜態 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="874af-198">Give the load balancer a static IP address.</span></span>
* <span data-ttu-id="874af-199">使用 TCP 通訊協定而不是 HTTPS 來建立健康狀態探查。</span><span class="sxs-lookup"><span data-stu-id="874af-199">Create a health probe using the TCP protocol rather than HTTPS.</span></span> <span data-ttu-id="874af-200">您可以連接埠 443 以確認 AD FS 伺服器正常運作。</span><span class="sxs-lookup"><span data-stu-id="874af-200">You can ping port 443 to verify that an AD FS server is functioning.</span></span>
  
  > [!NOTE]
  > <span data-ttu-id="874af-201">AD FS 伺服器會使用伺服器名稱指示 (SNI) 通訊協定，因此使用 HTTPS 端點從負載平衡器嘗試探查就會失敗。</span><span class="sxs-lookup"><span data-stu-id="874af-201">AD FS servers use the Server Name Indication (SNI) protocol, so attempting to probe using an HTTPS endpoint from the load balancer fails.</span></span>
  > 
  > 
* <span data-ttu-id="874af-202">將 DNS A 記錄新增到 AD FS 負載平衡器的網域。</span><span class="sxs-lookup"><span data-stu-id="874af-202">Add a DNS *A* record to the domain for the AD FS load balancer.</span></span> <span data-ttu-id="874af-203">指定負載平衡器的 IP 位址，並在網域 (例如 adfs.contoso.com) 中為其提供名稱。</span><span class="sxs-lookup"><span data-stu-id="874af-203">Specify the IP address of the load balancer, and give it a name in the domain (such as adfs.contoso.com).</span></span> <span data-ttu-id="874af-204">這是用戶端和 WAP 伺服器用來存取 AD FS 伺服器陣列的名稱。</span><span class="sxs-lookup"><span data-stu-id="874af-204">This is the name clients and the WAP servers use to access the AD FS server farm.</span></span>

### <a name="ad-fs-security"></a><span data-ttu-id="874af-205">AD FS 安全性</span><span class="sxs-lookup"><span data-stu-id="874af-205">AD FS security</span></span> 

<span data-ttu-id="874af-206">防止 AD FS 伺服器直接暴露在網際網路。</span><span class="sxs-lookup"><span data-stu-id="874af-206">Prevent direct exposure of the AD FS servers to the Internet.</span></span> <span data-ttu-id="874af-207">AD FS 伺服器是已加入網域的電腦，具有完整權限可授與安全性權杖。</span><span class="sxs-lookup"><span data-stu-id="874af-207">AD FS servers are domain-joined computers that have full authorization to grant security tokens.</span></span> <span data-ttu-id="874af-208">如果伺服器遭到入侵，惡意使用者可以向所有 web 應用程式以及所有受到 AD FS 保護的同盟伺服器發出完整存取權杖。</span><span class="sxs-lookup"><span data-stu-id="874af-208">If a server is compromised, a malicious user can issue full access tokens to all web applications and to all federation servers that are protected by AD FS.</span></span> <span data-ttu-id="874af-209">如果您系統必須處理的要求不是從受信任夥伴站台連線的外部使用者，請使用 WAP 伺服器來處理這些要求。</span><span class="sxs-lookup"><span data-stu-id="874af-209">If your system must handle requests from external users not connecting from trusted partner sites, use WAP servers to handle these requests.</span></span> <span data-ttu-id="874af-210">如需詳細資訊，請參閱[要將同盟伺服器 Proxy 放置在何處][where-to-place-an-fs-proxy]。</span><span class="sxs-lookup"><span data-stu-id="874af-210">For more information, see [Where to Place a Federation Server Proxy][where-to-place-an-fs-proxy].</span></span>

<span data-ttu-id="874af-211">將 AD FS 伺服器和 WAP 伺服器與其本身的防火牆放在不同的子網路中。</span><span class="sxs-lookup"><span data-stu-id="874af-211">Place AD FS servers and WAP servers in separate subnets with their own firewalls.</span></span> <span data-ttu-id="874af-212">您可以使用 NSG 規則來定義防火牆規則。</span><span class="sxs-lookup"><span data-stu-id="874af-212">You can use NSG rules to define firewall rules.</span></span> <span data-ttu-id="874af-213">如果您需要更完整的保護，可以使用一組子網路和網路虛擬設備 (NVA) 來實作其他的安全性周邊，如[在 Azure 中使用網際網路存取實作安全的混合式網路架構][implementing-a-secure-hybrid-network-architecture-with-internet-access]文件中所述。</span><span class="sxs-lookup"><span data-stu-id="874af-213">If you require more comprehensive protection you can implement an additional security perimeter around servers by using a pair of subnets and network virtual appliances (NVAs), as described in the document [Implementing a secure hybrid network architecture with Internet access in Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].</span></span> <span data-ttu-id="874af-214">所有防火牆都應該允許連接埠 443 (HTTPS) 流量。</span><span class="sxs-lookup"><span data-stu-id="874af-214">All firewalls should allow traffic on port 443 (HTTPS).</span></span>

<span data-ttu-id="874af-215">限制直接登入存取 AD FS 和 WAP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="874af-215">Restrict direct sign in access to the AD FS and WAP servers.</span></span> <span data-ttu-id="874af-216">應該只有 DevOps 工作人員能夠連線。</span><span class="sxs-lookup"><span data-stu-id="874af-216">Only DevOps staff should be able to connect.</span></span>

<span data-ttu-id="874af-217">請勿將 WAP 伺服器加入網域。</span><span class="sxs-lookup"><span data-stu-id="874af-217">Do not join the WAP servers to the domain.</span></span>

### <a name="ad-fs-installation"></a><span data-ttu-id="874af-218">AD FS 安裝</span><span class="sxs-lookup"><span data-stu-id="874af-218">AD FS installation</span></span> 

<span data-ttu-id="874af-219">[部署同盟伺服器陣列][Deploying_a_federation_server_farm]文章會提供安裝和設定 AD FS 的詳細指示。</span><span class="sxs-lookup"><span data-stu-id="874af-219">The article [Deploying a Federation Server Farm][Deploying_a_federation_server_farm] provides detailed instructions for installing and configuring AD FS.</span></span> <span data-ttu-id="874af-220">在伺服器陣列中設定第一部 AD FS 伺服器之前，請執行下列工作：</span><span class="sxs-lookup"><span data-stu-id="874af-220">Perform the following tasks before configuring the first AD FS server in the farm:</span></span>

1. <span data-ttu-id="874af-221">取得公開受信任的憑證以執行伺服器驗證。</span><span class="sxs-lookup"><span data-stu-id="874af-221">Obtain a publicly trusted certificate for performing server authentication.</span></span> <span data-ttu-id="874af-222">主體名稱必須包含用戶端用來存取同盟服務的名稱。</span><span class="sxs-lookup"><span data-stu-id="874af-222">The *subject name* must contain the name clients use to access the federation service.</span></span> <span data-ttu-id="874af-223">這可以是負載平衡器的已註冊 DNS 名稱，例如 *adfs.contoso.com* (基於安全性理由，請避免使用萬用字元名稱，例如 \**.contoso.com*)。</span><span class="sxs-lookup"><span data-stu-id="874af-223">This can be the DNS name registered for the load balancer, for example, *adfs.contoso.com* (avoid using wildcard names such as \**.contoso.com*, for security reasons).</span></span> <span data-ttu-id="874af-224">在所有 AD FS 伺服器 VM 上使用相同的憑證。</span><span class="sxs-lookup"><span data-stu-id="874af-224">Use the same certificate on all AD FS server VMs.</span></span> <span data-ttu-id="874af-225">您可以向受信任的憑證授權單位購買憑證，但是如果貴組織是使用 Active Directory 憑證服務，您就可以建立自己的憑證。</span><span class="sxs-lookup"><span data-stu-id="874af-225">You can purchase a certificate from a trusted certification authority, but if your organization uses Active Directory Certificate Services you can create your own.</span></span> 
   
    <span data-ttu-id="874af-226">主體別名會由裝置註冊服務 (DRS) 用來啟用來自外部裝置的存取。</span><span class="sxs-lookup"><span data-stu-id="874af-226">The *subject alternative name* is used by the device registration service (DRS) to enable access from external devices.</span></span> <span data-ttu-id="874af-227">此形式應該是 enterpriseregistration.contoso.com。</span><span class="sxs-lookup"><span data-stu-id="874af-227">This should be of the form *enterpriseregistration.contoso.com*.</span></span>
   
    <span data-ttu-id="874af-228">如需詳細資訊，請參閱[取得和設定 AD FS 的安全通訊端層 (SSL) 憑證][adfs_certificates]。</span><span class="sxs-lookup"><span data-stu-id="874af-228">For more information, see [Obtain and Configure a Secure Sockets Layer (SSL) Certificate for AD FS][adfs_certificates].</span></span>

2. <span data-ttu-id="874af-229">在網域控制站上，產生金鑰發佈服務的新根金鑰。</span><span class="sxs-lookup"><span data-stu-id="874af-229">On the domain controller, generate a new root key for the Key Distribution Service.</span></span> <span data-ttu-id="874af-230">將有效時間設為目前時間減去 10 小時 (這個設定可減少跨網域發佈及同步處理金鑰時可能會發生的延遲)。</span><span class="sxs-lookup"><span data-stu-id="874af-230">Set the effective time to the current time minus 10 hours (this configuration reduces the delay that can occur in distributing and synchronizing keys across the domain).</span></span> <span data-ttu-id="874af-231">若要支援建立用來執行 AD FS 服務的群組服務帳戶，此為必要步驟。</span><span class="sxs-lookup"><span data-stu-id="874af-231">This step is necessary to support creating the group service account that is used to run the AD FS service.</span></span> <span data-ttu-id="874af-232">下列 PowerShell 命令會顯示如何執行這項操作的範例：</span><span class="sxs-lookup"><span data-stu-id="874af-232">The following PowerShell command shows an example of how to do this:</span></span>
   
    ```powershell
    Add-KdsRootKey -EffectiveTime (Get-Date).AddHours(-10)
    ```

3. <span data-ttu-id="874af-233">將每個 AD FS 伺服器 VM 新增至網域。</span><span class="sxs-lookup"><span data-stu-id="874af-233">Add each AD FS server VM to the domain.</span></span>

> [!NOTE]
> <span data-ttu-id="874af-234">若要安裝 AD FS，針對網域執行主要網域控制站 (PDC) 模擬器彈性單一主機作業 (FSMO) 角色的網域控制站必須執行且從 AD FS VM 存取。</span><span class="sxs-lookup"><span data-stu-id="874af-234">To install AD FS, the domain controller running the primary domain controller (PDC) emulator flexible single master operation (FSMO) role for the domain must be running and accessible from the AD FS VMs.</span></span> <span data-ttu-id="874af-235"><<RBC：是否有方法可讓此較少重複？>></span><span class="sxs-lookup"><span data-stu-id="874af-235"><<RBC: Is there a way to make this less repetitive?>></span></span>
> 
> 

### <a name="ad-fs-trust"></a><span data-ttu-id="874af-236">AD FS 信任</span><span class="sxs-lookup"><span data-stu-id="874af-236">AD FS trust</span></span> 

<span data-ttu-id="874af-237">在 AD FS 安裝與任何夥伴組織的同盟伺服器之間建立同盟信任。</span><span class="sxs-lookup"><span data-stu-id="874af-237">Establish federation trust between your AD FS installation, and the federation servers of any partner organizations.</span></span> <span data-ttu-id="874af-238">設定篩選和對應所需的任何宣告。</span><span class="sxs-lookup"><span data-stu-id="874af-238">Configure any claims filtering and mapping required.</span></span> 

* <span data-ttu-id="874af-239">每個夥伴組織的 DevOps 人員必須新增信賴憑證者信任，才能透過 AD FS 伺服器存取 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="874af-239">DevOps staff at each partner organization must add a relying party trust for the web applications accessible through your AD FS servers.</span></span>
* <span data-ttu-id="874af-240">貴組織中的 DevOps 人員必須設定宣告提供者信任，才能讓 AD FS 伺服器信任夥伴組織所提供的宣告。</span><span class="sxs-lookup"><span data-stu-id="874af-240">DevOps staff in your organization must configure claims-provider trust to enable your AD FS servers to trust the claims that partner organizations provide.</span></span>
* <span data-ttu-id="874af-241">貴組織中的 DevOps 人員也必須設定 AD FS，才能將宣告傳遞至貴組織的 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="874af-241">DevOps staff in your organization must also configure AD FS to pass claims on to your organization's web applications.</span></span>
  
<span data-ttu-id="874af-242">如需詳細資訊，請參閱[建立同盟信任][establishing-federation-trust]。</span><span class="sxs-lookup"><span data-stu-id="874af-242">For more information, see [Establishing Federation Trust][establishing-federation-trust].</span></span>

<span data-ttu-id="874af-243">透過 WAP 伺服器使用預先驗證來發佈貴組織的 web 應用程式，並讓外部夥伴可使用它們。</span><span class="sxs-lookup"><span data-stu-id="874af-243">Publish your organization's web applications and make them available to external partners by using preauthentication through the WAP servers.</span></span> <span data-ttu-id="874af-244">如需詳細資訊，請參閱[使用 AD FS 預先驗證來發佈應用程式][publish_applications_using_AD_FS_preauthentication]</span><span class="sxs-lookup"><span data-stu-id="874af-244">For more information, see [Publish Applications using AD FS Preauthentication][publish_applications_using_AD_FS_preauthentication]</span></span>

<span data-ttu-id="874af-245">AD FS 支援權杖轉換和增強指定。</span><span class="sxs-lookup"><span data-stu-id="874af-245">AD FS supports token transformation and augmentation.</span></span> <span data-ttu-id="874af-246">Azure Active Directory 不提供這項功能。</span><span class="sxs-lookup"><span data-stu-id="874af-246">Azure Active Directory does not provide this feature.</span></span> <span data-ttu-id="874af-247">利用 AD FS，當您設定信任關係時，可以：</span><span class="sxs-lookup"><span data-stu-id="874af-247">With AD FS, when you set up the trust relationships, you can:</span></span>

* <span data-ttu-id="874af-248">設定授權規則的宣告轉換。</span><span class="sxs-lookup"><span data-stu-id="874af-248">Configure claim transformations for authorization rules.</span></span> <span data-ttu-id="874af-249">例如，您可以將來自非 Microsoft 夥伴組織使用之表示法的群組安全性對應到 Active Directory DS 可在貴組織中授權的項目。</span><span class="sxs-lookup"><span data-stu-id="874af-249">For example, you can map group security from a representation used by a non-Microsoft partner organization to something that that Active Directory DS can authorize in your organization.</span></span>
* <span data-ttu-id="874af-250">將宣告從一種格式轉換成另一種格式。</span><span class="sxs-lookup"><span data-stu-id="874af-250">Transform claims from one format to another.</span></span> <span data-ttu-id="874af-251">例如，如果您的應用程式只支援 SAML 1.1 宣告，您可以從 SAML 2.0 對應到 SAML 1.1。</span><span class="sxs-lookup"><span data-stu-id="874af-251">For example, you can map from SAML 2.0 to SAML 1.1 if your application only supports SAML 1.1 claims.</span></span>

### <a name="ad-fs-monitoring"></a><span data-ttu-id="874af-252">AD FS 監視</span><span class="sxs-lookup"><span data-stu-id="874af-252">AD FS monitoring</span></span> 

<span data-ttu-id="874af-253">[適用於 Active Directory 同盟服務 2012 R2 的 Microsoft System Center Management 套件][oms-adfs-pack]會提供適用於同盟伺服器的 AD FS 部署之主動式及回應式監視。</span><span class="sxs-lookup"><span data-stu-id="874af-253">The [Microsoft System Center Management Pack for Active Directory Federation Services 2012 R2][oms-adfs-pack] provides both proactive and reactive monitoring of your AD FS deployment for the federation server.</span></span> <span data-ttu-id="874af-254">此管理組件會監視：</span><span class="sxs-lookup"><span data-stu-id="874af-254">This management pack monitors:</span></span>

* <span data-ttu-id="874af-255">AD FS 服務在其事件記錄中記錄的事件。</span><span class="sxs-lookup"><span data-stu-id="874af-255">Events that the AD FS service records in its event logs.</span></span>
* <span data-ttu-id="874af-256">AD FS 效能計數器收集的效能資料。</span><span class="sxs-lookup"><span data-stu-id="874af-256">The performance data that the AD FS performance counters collect.</span></span> 
* <span data-ttu-id="874af-257">AD FS 系統和 web 應用程式 (信賴憑證者) 的整體健康情況，並提供重大問題和警告的警示。</span><span class="sxs-lookup"><span data-stu-id="874af-257">The overall health of the AD FS system and web applications (relying parties), and provides alerts for critical issues and warnings.</span></span> 

## <a name="scalability-considerations"></a><span data-ttu-id="874af-258">延展性考量</span><span class="sxs-lookup"><span data-stu-id="874af-258">Scalability considerations</span></span>

<span data-ttu-id="874af-259">下列考量摘要自[規劃 AD FS 部署][plan-your-adfs-deployment]文章，提供一個調整 AD FS 伺服器陣列大小的起始點：</span><span class="sxs-lookup"><span data-stu-id="874af-259">The following considerations, summarized from the article [Plan your AD FS deployment][plan-your-adfs-deployment], give a starting point for sizing AD FS farms:</span></span>

* <span data-ttu-id="874af-260">如果您的使用者低於 1000 個，請勿建立專用的伺服器，而是改為在雲端中的每個 Active Directory DS 伺服器上安裝 AD FS。</span><span class="sxs-lookup"><span data-stu-id="874af-260">If you have fewer than 1000 users, do not create dedicated servers, but instead install AD FS on each of the Active Directory DS servers in the cloud.</span></span> <span data-ttu-id="874af-261">請確定您有至少兩部 Active Directory DS 伺服器以維持可用性。</span><span class="sxs-lookup"><span data-stu-id="874af-261">Make sure that you have at least two Active Directory DS servers to maintain availability.</span></span> <span data-ttu-id="874af-262">建立單一 WAP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="874af-262">Create a single WAP server.</span></span>
* <span data-ttu-id="874af-263">如果您的使用者介於 1000 和 15000 個之間，請建立兩部專用的 AD FS 伺服器和兩部專用的 WAP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="874af-263">If you have between 1000 and 15000 users, create two dedicated AD FS servers and two dedicated WAP servers.</span></span>
* <span data-ttu-id="874af-264">如果您的使用者介於 15000 與 60000 個之間，請建立三到五部專用的 AD FS 伺服器，和至少兩部專用的 WAP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="874af-264">If you have between 15000 and 60000 users, create between three and five dedicated AD FS servers and at least two dedicated WAP servers.</span></span>

<span data-ttu-id="874af-265">這些考量是假設您在 Azure 中使用雙四核心 VM (標準 D4_v2 或更新版本) 的大小。</span><span class="sxs-lookup"><span data-stu-id="874af-265">These considerations assume that you are using dual quad-core VM (Standard D4_v2, or better) sizes in Azure.</span></span>

<span data-ttu-id="874af-266">如果您是使用 Windows 內部資料庫來儲存 AD FS 組態資料，在伺服器陣列中就會限制為八個 AD FS 伺服器。</span><span class="sxs-lookup"><span data-stu-id="874af-266">If you are using the Windows Internal Database to store AD FS configuration data, you are limited to eight AD FS servers in the farm.</span></span> <span data-ttu-id="874af-267">如果您預期未來會需要更多，請使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="874af-267">If you anticipate that you will need more in the future, use SQL Server.</span></span> <span data-ttu-id="874af-268">如需詳細資訊，請參閱 [AD FS 設定資料庫的角色][adfs-configuration-database]。</span><span class="sxs-lookup"><span data-stu-id="874af-268">For more information, see [The Role of the AD FS Configuration Database][adfs-configuration-database].</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="874af-269">可用性考量</span><span class="sxs-lookup"><span data-stu-id="874af-269">Availability considerations</span></span>

<span data-ttu-id="874af-270">您可以使用 SQL Server 或 Windows 內部資料庫來保存 AD FS 組態資訊。</span><span class="sxs-lookup"><span data-stu-id="874af-270">You can use either SQL Server or the Windows Internal Database to hold AD FS configuration information.</span></span> <span data-ttu-id="874af-271">Windows 內部資料庫會提供基本的備援性。</span><span class="sxs-lookup"><span data-stu-id="874af-271">The Windows Internal Database provides basic redundancy.</span></span> <span data-ttu-id="874af-272">變更只會直接寫入 AD FS 叢集中的其中一個 AD FS 資料庫，而其他伺服器會使用提取複寫，讓其資料庫保持最新狀態。</span><span class="sxs-lookup"><span data-stu-id="874af-272">Changes are written directly to only one of the AD FS databases in the AD FS cluster, while the other servers use pull replication to keep their databases up to date.</span></span> <span data-ttu-id="874af-273">使用 SQL Server，可以在使用容錯移轉叢集或鏡像時提供完整的資料庫備援性和高可用性。</span><span class="sxs-lookup"><span data-stu-id="874af-273">Using SQL Server can provide full database redundancy and high availability using failover clustering or mirroring.</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="874af-274">管理性考量</span><span class="sxs-lookup"><span data-stu-id="874af-274">Manageability considerations</span></span>

<span data-ttu-id="874af-275">DevOps 人員應該準備好執行下列工作：</span><span class="sxs-lookup"><span data-stu-id="874af-275">DevOps staff should be prepared to perform the following tasks:</span></span>

* <span data-ttu-id="874af-276">管理同盟伺服器，包括管理 AD FS 伺服器陣列、管理同盟伺服器上的信任原則，和管理同盟服務所使用的憑證。</span><span class="sxs-lookup"><span data-stu-id="874af-276">Managing the federation servers, including managing the AD FS farm, managing trust policy on the federation servers, and managing the certificates used by the federation services.</span></span>
* <span data-ttu-id="874af-277">管理 WAP 伺服器包括管理 WAP 伺服器陣列和憑證。</span><span class="sxs-lookup"><span data-stu-id="874af-277">Managing the WAP servers including managing the WAP farm and certificates.</span></span>
* <span data-ttu-id="874af-278">管理 web 應用程式，包括設定信賴憑證者、驗證方法及宣告對應。</span><span class="sxs-lookup"><span data-stu-id="874af-278">Managing web applications including configuring relying parties, authentication methods, and claims mappings.</span></span>
* <span data-ttu-id="874af-279">備份 AD FS 元件。</span><span class="sxs-lookup"><span data-stu-id="874af-279">Backing up AD FS components.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="874af-280">安全性考量</span><span class="sxs-lookup"><span data-stu-id="874af-280">Security considerations</span></span>

<span data-ttu-id="874af-281">AD FS 會使用 HTTPS 通訊協定，因此請確定包含 web 層 VM 的子網路之 NSG 規則允許 HTTPS 要求。</span><span class="sxs-lookup"><span data-stu-id="874af-281">AD FS utilizes the HTTPS protocol, so make sure that the NSG rules for the subnet containing the web tier VMs permit HTTPS requests.</span></span> <span data-ttu-id="874af-282">可從內部部署網路、包含 web 層的子網路、商業層、資料層、私人 DMZ、公用 DMZ，以及包含 AD FS 伺服器的子網路等起始這些要求。</span><span class="sxs-lookup"><span data-stu-id="874af-282">These requests can originate from the on-premises network, the subnets containing the web tier, business tier, data tier, private DMZ, public DMZ, and the subnet containing the AD FS servers.</span></span>

<span data-ttu-id="874af-283">請考慮使用一組網路虛擬設備，可就稽核目的，記錄關於流量周遊虛擬網路邊緣的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="874af-283">Consider using a set of network virtual appliances that logs detailed information on traffic traversing the edge of your virtual network for auditing purposes.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="874af-284">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="874af-284">Deploy the solution</span></span>

<span data-ttu-id="874af-285">部署此參考架構的解決方案可在 [GitHub][github] 上取得。</span><span class="sxs-lookup"><span data-stu-id="874af-285">A solution is available on [GitHub][github] to deploy this reference architecture.</span></span> <span data-ttu-id="874af-286">您需要最新版的 [Azure CLI][azure-cli]，才能執行可部署解決方案的 Powershell 指令碼。</span><span class="sxs-lookup"><span data-stu-id="874af-286">You will need the latest version of the [Azure CLI][azure-cli] to run the Powershell script that deploys the solution.</span></span> <span data-ttu-id="874af-287">若要部署參考架構，請依照下列步驟執行：</span><span class="sxs-lookup"><span data-stu-id="874af-287">To deploy the reference architecture, follow these steps:</span></span>

1. <span data-ttu-id="874af-288">將解決方案資料夾從 [GitHub][github] 下載或複製到本機電腦。</span><span class="sxs-lookup"><span data-stu-id="874af-288">Download or clone the solution folder from [GitHub][github] to your local machine.</span></span>

2. <span data-ttu-id="874af-289">開啟 Azure CLI，並瀏覽至本機解決方案資料夾。</span><span class="sxs-lookup"><span data-stu-id="874af-289">Open the Azure CLI and navigate to the local solution folder.</span></span>

3. <span data-ttu-id="874af-290">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="874af-290">Run the following command:</span></span>
   
    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```
   
    <span data-ttu-id="874af-291">使用您的 Azure 訂用帳戶識別碼來取代 `<subscription id>` 。</span><span class="sxs-lookup"><span data-stu-id="874af-291">Replace `<subscription id>` with your Azure subscription ID.</span></span>
   
    <span data-ttu-id="874af-292">針對 `<location>`，請指定 Azure 區域，例如 `eastus` 或 `westus`。</span><span class="sxs-lookup"><span data-stu-id="874af-292">For `<location>`, specify an Azure region, such as `eastus` or `westus`.</span></span>
   
    <span data-ttu-id="874af-293">`<mode>` 參數可精密控制部署，而且可以是下列其中一個值：</span><span class="sxs-lookup"><span data-stu-id="874af-293">The `<mode>` parameter controls the granularity of the deployment, and can be one of the following values:</span></span>
   
   * <span data-ttu-id="874af-294">`Onpremise`：部署模擬的內部部署環境。</span><span class="sxs-lookup"><span data-stu-id="874af-294">`Onpremise`: Deploys a simulated on-premises environment.</span></span> <span data-ttu-id="874af-295">如果您沒有任何現有內部部署網路，或如果您需要在不變更現有內部部署網路設定的情況下測試此參考架構，可以使用此部署進行測試和實驗。</span><span class="sxs-lookup"><span data-stu-id="874af-295">You can use this deployment to test and experiment if you do not have an existing on-premises network, or if you want to test this reference architecture without changing the configuration of your existing on-premises network.</span></span>
   * <span data-ttu-id="874af-296">`Infrastructure`：部署 VNet 基礎結構和跳躍方塊。</span><span class="sxs-lookup"><span data-stu-id="874af-296">`Infrastructure`: deploys the VNet infrastructure and jump box.</span></span>
   * <span data-ttu-id="874af-297">`CreateVpn`：部署 Azure 虛擬網路閘道，並將其連線到模擬的內部部署網路。</span><span class="sxs-lookup"><span data-stu-id="874af-297">`CreateVpn`: deploys an Azure virtual network gateway and connects it to the simulated on-premises network.</span></span>
   * <span data-ttu-id="874af-298">`AzureADDS`：部署當作 Active Directory DS 伺服器的 VM、將 Active Directory 部署到這些 VM，然後在 Azure 中建立網域。</span><span class="sxs-lookup"><span data-stu-id="874af-298">`AzureADDS`: deploys the VMs acting as Active Directory DS servers, deploys Active Directory to these VMs, and creates the domain in Azure.</span></span>
   * <span data-ttu-id="874af-299">`AdfsVm`：部署 AD FS VM，並將它們加入 Azure 中的網域。</span><span class="sxs-lookup"><span data-stu-id="874af-299">`AdfsVm`: deploys the AD FS VMs and joins them to the domain in Azure.</span></span>
   * <span data-ttu-id="874af-300">`PublicDMZ`：在 Azure 中部署公用 DMZ。</span><span class="sxs-lookup"><span data-stu-id="874af-300">`PublicDMZ`: deploys the public DMZ in Azure.</span></span>
   * <span data-ttu-id="874af-301">`ProxyVm`：部署 AD FS proxy VM，並將它們加入 Azure 中的網域。</span><span class="sxs-lookup"><span data-stu-id="874af-301">`ProxyVm`: deploys the AD FS proxy VMs and joins them to the domain in Azure.</span></span>
   * <span data-ttu-id="874af-302">`Prepare`：部署所有先前的部署。</span><span class="sxs-lookup"><span data-stu-id="874af-302">`Prepare`: deploys all of the preceding deployments.</span></span> <span data-ttu-id="874af-303">**如果您要建置全新的部署，而且沒有任何現有內部部署基礎結構，這就是建議的選項。**</span><span class="sxs-lookup"><span data-stu-id="874af-303">**This is the recommended option if you are building an entirely new deployment and you don't have an existing on-premises infrastructure.**</span></span> 
   * <span data-ttu-id="874af-304">`Workload`：選擇性地部署 web、商務和資料層 VM 和支援的網路。</span><span class="sxs-lookup"><span data-stu-id="874af-304">`Workload`: optionally deploys web, business, and data tier VMs and supporting network.</span></span> <span data-ttu-id="874af-305">不包含在 `Prepare` 部署模式中。</span><span class="sxs-lookup"><span data-stu-id="874af-305">Not included in the `Prepare` deployment mode.</span></span>
   * <span data-ttu-id="874af-306">`PrivateDMZ`：選擇性地將 Azure 中的私人 DMZ 部署在以上所部署的 `Workload`VM 前面。</span><span class="sxs-lookup"><span data-stu-id="874af-306">`PrivateDMZ`: optionally deploys the private DMZ in Azure in front of the `Workload` VMs deployed above.</span></span> <span data-ttu-id="874af-307">不包含在 `Prepare` 部署模式中。</span><span class="sxs-lookup"><span data-stu-id="874af-307">Not included in the `Prepare` deployment mode.</span></span>

4. <span data-ttu-id="874af-308">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="874af-308">Wait for the deployment to complete.</span></span> <span data-ttu-id="874af-309">如果您使用 `Prepare` 選項，部署就需要數小時才能完成，且完成時會出現 `Preparation is completed. Please install certificate to all AD FS and proxy VMs.` 訊息</span><span class="sxs-lookup"><span data-stu-id="874af-309">If you used the `Prepare` option, the deployment takes several hours to complete, and finishes with the message `Preparation is completed. Please install certificate to all AD FS and proxy VMs.`</span></span>

5. <span data-ttu-id="874af-310">重新啟動跳躍方塊 (ra-adfs-security-rg 群組中的 ra-adfs-mgmt-vm1)，以允許其 DNS 設定生效。</span><span class="sxs-lookup"><span data-stu-id="874af-310">Restart the jump box (*ra-adfs-mgmt-vm1* in the *ra-adfs-security-rg* group) to allow its DNS settings to take effect.</span></span>

6. <span data-ttu-id="874af-311">[取得 AD FS 的 SSL 憑證][adfs_certificates] 並在 AD FS VM 上安裝這個憑證。</span><span class="sxs-lookup"><span data-stu-id="874af-311">[Obtain an SSL Certificate for AD FS][adfs_certificates] and install this certificate on the AD FS VMs.</span></span> <span data-ttu-id="874af-312">請注意，您可透過跳躍方塊加以連線。</span><span class="sxs-lookup"><span data-stu-id="874af-312">Note that you can connect to them through the jump box.</span></span> <span data-ttu-id="874af-313">IP 位址為 10.0.5.4 和 10.0.5.5。</span><span class="sxs-lookup"><span data-stu-id="874af-313">The IP addresses are *10.0.5.4* and *10.0.5.5*.</span></span> <span data-ttu-id="874af-314">預設使用者名稱為 contoso\testuser，密碼為 AweSome@PW。</span><span class="sxs-lookup"><span data-stu-id="874af-314">The default username is *contoso\testuser* with password *AweSome@PW*.</span></span>
   
   > [!NOTE]
   > <span data-ttu-id="874af-315">此時 Deploy-ReferenceArchitecture.ps1 指令碼中的註解會提供詳細指示，說明如何使用 `makecert` 命令來建立自我簽署的測試憑證和授權單位。</span><span class="sxs-lookup"><span data-stu-id="874af-315">The comments in the Deploy-ReferenceArchitecture.ps1 script at this point provides detailed instructions for creating a self-signed test certificate and authority using the `makecert` command.</span></span> <span data-ttu-id="874af-316">不過，僅執行這些步驟作為**測試**，並且請勿在生產環境中使用 makecert 所產生的憑證。</span><span class="sxs-lookup"><span data-stu-id="874af-316">However, perform these steps as a **test** only and do not use the certificates generated by makecert in a production environment.</span></span>
   > 
   > 

7. <span data-ttu-id="874af-317">執行下列 PowerShell 命令來部署 AD FS 伺服器陣列：</span><span class="sxs-lookup"><span data-stu-id="874af-317">Run the following PowerShell command to deploy the AD FS server farm:</span></span>
   
    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Adfs
    ``` 

8. <span data-ttu-id="874af-318">在跳躍方塊中，瀏覽至 `https://adfs.contoso.com/adfs/ls/idpinitiatedsignon.htm` 來測試 AD FS 安裝 (可能會收到憑證，警告您可以忽略這項測試)。</span><span class="sxs-lookup"><span data-stu-id="874af-318">On the jump box, browse to `https://adfs.contoso.com/adfs/ls/idpinitiatedsignon.htm` to test the AD FS installation (you may receive a certificate warning that you can ignore for this test).</span></span> <span data-ttu-id="874af-319">確認出現 Contoso Corporation 登入頁面。</span><span class="sxs-lookup"><span data-stu-id="874af-319">Verify that the Contoso Corporation sign-in page appears.</span></span> <span data-ttu-id="874af-320">以 contoso\testuser 身分登入，密碼為 AweS0me@PW。</span><span class="sxs-lookup"><span data-stu-id="874af-320">Sign in as *contoso\testuser* with password *AweS0me@PW*.</span></span>

9. <span data-ttu-id="874af-321">在 AD FS proxy VM 上安裝 SSL 憑證。</span><span class="sxs-lookup"><span data-stu-id="874af-321">Install the SSL certificate on the AD FS proxy VMs.</span></span> <span data-ttu-id="874af-322">IP 位址為 10.0.6.4 和 10.0.6.5。</span><span class="sxs-lookup"><span data-stu-id="874af-322">The IP addresses are *10.0.6.4* and *10.0.6.5*.</span></span>

10. <span data-ttu-id="874af-323">執行下列 PowerShell 命令來部署第一個 AD FS Proxy 伺服器：</span><span class="sxs-lookup"><span data-stu-id="874af-323">Run the following PowerShell command to deploy the first AD FS proxy server:</span></span>
   
    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy1
    ```

11. <span data-ttu-id="874af-324">依照指令碼所顯示的指示來測試安裝第一個 Proxy 伺服器。</span><span class="sxs-lookup"><span data-stu-id="874af-324">Follow the instructions displayed by the script to test the installation of the first proxy server.</span></span>

12. <span data-ttu-id="874af-325">執行下列 PowerShell 命令來部署第二個 Proxy 伺服器：</span><span class="sxs-lookup"><span data-stu-id="874af-325">Run the following PowerShell command to deploy the second proxy server:</span></span>
    
    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy2
    ```

13. <span data-ttu-id="874af-326">依照指令碼所顯示的指示來測試完整 Proxy 設定。</span><span class="sxs-lookup"><span data-stu-id="874af-326">Follow the instructions displayed by the script to test the complete proxy configuration.</span></span>

## <a name="next-steps"></a><span data-ttu-id="874af-327">後續步驟</span><span class="sxs-lookup"><span data-stu-id="874af-327">Next steps</span></span>

* <span data-ttu-id="874af-328">了解 [Azure Active Directory][aad]。</span><span class="sxs-lookup"><span data-stu-id="874af-328">Learn about [Azure Active Directory][aad].</span></span>
* <span data-ttu-id="874af-329">了解 [Azure Active Directory B2C][aadb2c]。</span><span class="sxs-lookup"><span data-stu-id="874af-329">Learn about [Azure Active Directory B2C][aadb2c].</span></span>

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
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[active-directory-federation-services-overview]: https://technet.microsoft.com/library/hh831502(v=ws.11).aspx
[establishing-federation-trust]: https://blogs.msdn.microsoft.com/alextch/2011/06/27/establishing-federation-trust/
[Deploying_a_federation_server_farm]:  https://azure.microsoft.com/documentation/articles/active-directory-aadconnect-azure-adfs/
[install_and_configure_the_web_application_proxy_server]: https://technet.microsoft.com/library/dn383662.aspx
[publish_applications_using_AD_FS_preauthentication]: https://technet.microsoft.com/library/dn383640.aspx
[managing-adfs-components]: https://technet.microsoft.com/library/cc759026.aspx
[oms-adfs-pack]: https://www.microsoft.com/download/details.aspx?id=41184
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[aad]: https://azure.microsoft.com/documentation/services/active-directory/
[aadb2c]: https://azure.microsoft.com/documentation/services/active-directory-b2c/
[adfs-intro]: /azure/active-directory/active-directory-aadconnect-azure-adfs
[github]: https://github.com/mspnp/reference-architectures/tree/master/identity/adfs
[adfs_certificates]: https://technet.microsoft.com/library/dn781428(v=ws.11).aspx
[considerations]: ./considerations.md
[visio-download]: https://archcenter.azureedge.net/cdn/identity-architectures.vsdx
[0]: ./images/adfs.png "使用 Active Directory 保護混合式網路架構的安全"

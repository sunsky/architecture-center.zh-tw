---
title: "將 Active Directory Domain Services (AD DS) 擴充至 Azure"
description: "如何在 Azure 中使用 Active Directory 授權來實作安全的混合式網路架構。\n指引,vpn 閘道,expressroute,負載平衡器,虛擬網路,active directory"
author: telmosampaio
ms.date: 11/28/2016
pnp.series.title: Identity management
pnp.series.prev: azure-ad
pnp.series.next: adds-forest
ms.openlocfilehash: 7f771f77c7fa7f266dcce9f5b45e5be658213b8d
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="extend-active-directory-domain-services-ad-ds-to-azure"></a><span data-ttu-id="89973-104">將 Active Directory Domain Services (AD DS) 擴充至 Azure</span><span class="sxs-lookup"><span data-stu-id="89973-104">Extend Active Directory Domain Services (AD DS) to Azure</span></span>

<span data-ttu-id="89973-105">此參考架構示範如何將 Active Directory 環境擴充到 Azure，才能使用 [Active Directory Domain Services (AD DS)][active-directory-domain-services] 提供分散式驗證服務。</span><span class="sxs-lookup"><span data-stu-id="89973-105">This reference architecture shows how to extend your Active Directory environment to Azure to provide distributed authentication services using [Active Directory Domain Services (AD DS)][active-directory-domain-services].</span></span>  [<span data-ttu-id="89973-106">**部署這個解決方案**。</span><span class="sxs-lookup"><span data-stu-id="89973-106">**Deploy this solution**.</span></span>](#deploy-the-solution)

<span data-ttu-id="89973-107">[![0]][0]</span><span class="sxs-lookup"><span data-stu-id="89973-107">[![0]][0]</span></span> 

<span data-ttu-id="89973-108">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="89973-108">*Download a [Visio file][visio-download] of this architecture.*</span></span>

<span data-ttu-id="89973-109">AD DS 可用來驗證包含在安全性網域中的使用者、電腦、應用程式或其他身分識別。</span><span class="sxs-lookup"><span data-stu-id="89973-109">AD DS is used to authenticate user, computer, application, or other identities that are included in a security domain.</span></span> <span data-ttu-id="89973-110">它可以裝載在內部部署環境，但如果您的應用程式部分裝載在內部部署環境且部分裝載在 Azure 中，則在 Azure 中複寫此功能可能更有效率。</span><span class="sxs-lookup"><span data-stu-id="89973-110">It can be hosted on-premises, but if your application is hosted partly on-premises and partly in Azure, it may be more efficient to replicate this functionality in Azure.</span></span> <span data-ttu-id="89973-111">如此可以減少將驗證和本機授權要求從雲端傳回執行內部部署之 AD DS 所造成的延遲。</span><span class="sxs-lookup"><span data-stu-id="89973-111">This can reduce the latency caused by sending authentication and local authorization requests from the cloud back to AD DS running on-premises.</span></span> 

<span data-ttu-id="89973-112">當內部部署網路與 Azure 虛擬網路透過 VPN 或 ExpressRoute 連線連接時，通常會使用這個架構。</span><span class="sxs-lookup"><span data-stu-id="89973-112">This architecture is commonly used when the on-premises network and the Azure virtual network are connected by a VPN or ExpressRoute connection.</span></span> <span data-ttu-id="89973-113">此架構也支援雙向複寫，也就是說，您可以在內部部署或雲端上進行變更，而且這兩個來源會保持一致。</span><span class="sxs-lookup"><span data-stu-id="89973-113">This architecture also supports bidirectional replication, meaning changes can be made either on-premises or in the cloud, and both sources will be kept consistent.</span></span> <span data-ttu-id="89973-114">此架構的典型用途包括在內部部署與 Azure 之間散佈功能的混合式應用程式，以及使用 Active Directory 執行驗證的應用程式和服務。</span><span class="sxs-lookup"><span data-stu-id="89973-114">Typical uses for this architecture include hybrid applications in which functionality is distributed between on-premises and Azure, and applications and services that perform authentication using Active Directory.</span></span>

<span data-ttu-id="89973-115">如需了解其他考量，請參閱[選擇解決方案以整合內部部署 Active Directory 與 Azure][considerations]。</span><span class="sxs-lookup"><span data-stu-id="89973-115">For additional considerations, see [Choose a solution for integrating on-premises Active Directory with Azure][considerations].</span></span> 

## <a name="architecture"></a><span data-ttu-id="89973-116">架構</span><span class="sxs-lookup"><span data-stu-id="89973-116">Architecture</span></span> 

<span data-ttu-id="89973-117">此架構可擴充 [Azure 與網際網路之間的 DMZ][implementing-a-secure-hybrid-network-architecture-with-internet-access] 所示的架構。</span><span class="sxs-lookup"><span data-stu-id="89973-117">This architecture extends the architecture shown in [DMZ between Azure and the Internet][implementing-a-secure-hybrid-network-architecture-with-internet-access].</span></span> <span data-ttu-id="89973-118">其元件如下。</span><span class="sxs-lookup"><span data-stu-id="89973-118">It has the following components.</span></span>

* <span data-ttu-id="89973-119">**內部部署網路**。</span><span class="sxs-lookup"><span data-stu-id="89973-119">**On-premises network**.</span></span> <span data-ttu-id="89973-120">內部部署網路包括可以針對內部部署元件執行驗證和授權的本機 Active Directory 伺服器。</span><span class="sxs-lookup"><span data-stu-id="89973-120">The on-premises network includes local Active Directory servers that can perform authentication and authorization for components located on-premises.</span></span>
* <span data-ttu-id="89973-121">**Active Directory 伺服器**。</span><span class="sxs-lookup"><span data-stu-id="89973-121">**Active Directory servers**.</span></span> <span data-ttu-id="89973-122">這些網域控制站是雲端中執行的 VM，負責實作目錄服務 (AD DS)。</span><span class="sxs-lookup"><span data-stu-id="89973-122">These are domain controllers implementing directory services (AD DS) running as VMs in the cloud.</span></span> <span data-ttu-id="89973-123">這些伺服器可以驗證在 Azure 虛擬網路中執行的元件。</span><span class="sxs-lookup"><span data-stu-id="89973-123">These servers can provide authentication of components running in your Azure virtual network.</span></span>
* <span data-ttu-id="89973-124">**Active Directory 子網路**。</span><span class="sxs-lookup"><span data-stu-id="89973-124">**Active Directory subnet**.</span></span> <span data-ttu-id="89973-125">AD DS 伺服器會裝載在不同的子網路中。</span><span class="sxs-lookup"><span data-stu-id="89973-125">The AD DS servers are hosted in a separate subnet.</span></span> <span data-ttu-id="89973-126">網路安全性群組 (NSG) 規則可保護 AD DS 伺服器，並提供防火牆以防禦來自非預期來源的流量。</span><span class="sxs-lookup"><span data-stu-id="89973-126">Network security group (NSG) rules protect the AD DS servers and provide a firewall against traffic from unexpected sources.</span></span>
* <span data-ttu-id="89973-127">**Azure 閘道和 Active Directory 同步處理**。</span><span class="sxs-lookup"><span data-stu-id="89973-127">**Azure Gateway and Active Directory synchronization**.</span></span> <span data-ttu-id="89973-128">Azure 閘道會提供內部部署網路與 Azure VNet 之間的連線。</span><span class="sxs-lookup"><span data-stu-id="89973-128">The Azure gateway provides a connection between the on-premises network and the Azure VNet.</span></span> <span data-ttu-id="89973-129">這可能是 [VPN 連線][azure-vpn-gateway]或 [Azure ExpressRoute][azure-expressroute]。</span><span class="sxs-lookup"><span data-stu-id="89973-129">This can be a [VPN connection][azure-vpn-gateway] or [Azure ExpressRoute][azure-expressroute].</span></span> <span data-ttu-id="89973-130">雲端與與內部部署環境中 Active Directory 伺服器之間的所有同步處理要求都會通過閘道。</span><span class="sxs-lookup"><span data-stu-id="89973-130">All synchronization requests between the Active Directory servers in the cloud and on-premises pass through the gateway.</span></span> <span data-ttu-id="89973-131">使用者定義的路由 (UDR) 會針對傳送至 Azure 的內部部署流量處理路由傳送。</span><span class="sxs-lookup"><span data-stu-id="89973-131">User-defined routes (UDRs) handle routing for on-premises traffic that passes to Azure.</span></span> <span data-ttu-id="89973-132">往返 Active Directory 伺服器的流量不會通過此案例中使用的網路虛擬設備 (NVA)。</span><span class="sxs-lookup"><span data-stu-id="89973-132">Traffic to and from the Active Directory servers does not pass through the network virtual appliances (NVAs) used in this scenario.</span></span>

<span data-ttu-id="89973-133">如需有關設定 UDR 與 NVA 的詳細資訊，請參閱[在 Azure 中實作安全的混合式網路架構][implementing-a-secure-hybrid-network-architecture]。</span><span class="sxs-lookup"><span data-stu-id="89973-133">For more information about configuring UDRs and the NVAs, see [Implementing a secure hybrid network architecture in Azure][implementing-a-secure-hybrid-network-architecture].</span></span> 

## <a name="recommendations"></a><span data-ttu-id="89973-134">建議</span><span class="sxs-lookup"><span data-stu-id="89973-134">Recommendations</span></span>

<span data-ttu-id="89973-135">下列建議適用於大部分的案例。</span><span class="sxs-lookup"><span data-stu-id="89973-135">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="89973-136">除非您有特定的需求會覆寫它們，否則請遵循下列建議。</span><span class="sxs-lookup"><span data-stu-id="89973-136">Follow these recommendations unless you have a specific requirement that overrides them.</span></span> 

### <a name="vm-recommendations"></a><span data-ttu-id="89973-137">VM 建議</span><span class="sxs-lookup"><span data-stu-id="89973-137">VM recommendations</span></span>

<span data-ttu-id="89973-138">根據預期的驗證要求量，決定您的 [VM 大小][vm-windows-sizes]需求。</span><span class="sxs-lookup"><span data-stu-id="89973-138">Determine your [VM size][vm-windows-sizes] requirements based on the expected volume of authentication requests.</span></span> <span data-ttu-id="89973-139">針對在內部部署環境中裝載 AD DS 的電腦，使用其規格作為起點，以比對 Azure VM 的大小。</span><span class="sxs-lookup"><span data-stu-id="89973-139">Use the specifications of the machines hosting AD DS on premises as a starting point, and match them with the Azure VM sizes.</span></span> <span data-ttu-id="89973-140">一旦部署之後，請根據 VM 上的實際負載，監視使用量並相應增加或減少。</span><span class="sxs-lookup"><span data-stu-id="89973-140">Once deployed, monitor utilization and scale up or down based on the actual load on the VMs.</span></span> <span data-ttu-id="89973-141">如需有關調整 AD DS 網域控制站大小的詳細資訊，請參閱 [Active Directory Domain Services 的容量規劃][capacity-planning-for-adds]。</span><span class="sxs-lookup"><span data-stu-id="89973-141">For more information about sizing AD DS domain controllers, see [Capacity Planning for Active Directory Domain Services][capacity-planning-for-adds].</span></span>

<span data-ttu-id="89973-142">建立個別的虛擬資料磁碟，以儲存 Active Directory 的資料庫、記錄檔和 SYSVOL。</span><span class="sxs-lookup"><span data-stu-id="89973-142">Create a separate virtual data disk for storing the database, logs, and SYSVOL for Active Directory.</span></span> <span data-ttu-id="89973-143">請不要將這些項目儲存在與作業系統相同的磁碟上。</span><span class="sxs-lookup"><span data-stu-id="89973-143">Do not store these items on the same disk as the operating system.</span></span> <span data-ttu-id="89973-144">請注意，連線到 VM 的資料磁碟預設會使用即刻寫入快取。</span><span class="sxs-lookup"><span data-stu-id="89973-144">Note that by default, data disks that are attached to a VM use write-through caching.</span></span> <span data-ttu-id="89973-145">不過，這種形式的快取可能會與 AD DS 的需求發生衝突。</span><span class="sxs-lookup"><span data-stu-id="89973-145">However, this form of caching can conflict with the requirements of AD DS.</span></span> <span data-ttu-id="89973-146">因此，請將資料磁碟上的 [主機快取偏好設定] 設定設為 [無]。</span><span class="sxs-lookup"><span data-stu-id="89973-146">For this reason, set the *Host Cache Preference* setting on the data disk to *None*.</span></span> <span data-ttu-id="89973-147">如需詳細資訊，請參閱 [Windows Server AD DS 資料庫和 SYSVOL 的位置][adds-data-disks]。</span><span class="sxs-lookup"><span data-stu-id="89973-147">For more information, see [Placement of the Windows Server AD DS database and SYSVOL][adds-data-disks].</span></span>

<span data-ttu-id="89973-148">至少將兩個執行 AD DS 的 VM 部署為網域控制站，並將其新增至[可用性設定組][availability-set]。</span><span class="sxs-lookup"><span data-stu-id="89973-148">Deploy at least two VMs running AD DS as domain controllers and add them to an [availability set][availability-set].</span></span>

### <a name="networking-recommendations"></a><span data-ttu-id="89973-149">網路功能的建議</span><span class="sxs-lookup"><span data-stu-id="89973-149">Networking recommendations</span></span>

<span data-ttu-id="89973-150">使用靜態私人 IP 位址，設定每個 AD DS 伺服器的 VM 網路介面 (NIC)，以獲得完整的網域名稱服務 (DNS) 支援。</span><span class="sxs-lookup"><span data-stu-id="89973-150">Configure the VM network interface (NIC) for each AD DS server with a static private IP address for full domain name service (DNS) support.</span></span> <span data-ttu-id="89973-151">如需詳細資訊，請參閱[如何在 Azure 入口網站中設定靜態私人 IP 位址][set-a-static-ip-address]。</span><span class="sxs-lookup"><span data-stu-id="89973-151">For more information, see [How to set a static private IP address in the Azure portal][set-a-static-ip-address].</span></span>

> [!NOTE]
> <span data-ttu-id="89973-152">請不要使用公用 IP 位址設定任何 AD DS 的 VM NIC。</span><span class="sxs-lookup"><span data-stu-id="89973-152">Do not configure the VM NIC for any AD DS with a public IP address.</span></span> <span data-ttu-id="89973-153">如需詳細資料，請參閱[安全性考量][security-considerations]。</span><span class="sxs-lookup"><span data-stu-id="89973-153">See [Security considerations][security-considerations] for more details.</span></span>
> 
> 

<span data-ttu-id="89973-154">Active Directory 子網路 NSG 需要有允許來自內部部署環境傳入流量的規則。</span><span class="sxs-lookup"><span data-stu-id="89973-154">The Active Directory subnet NSG requires rules to permit incoming traffic from on-premises.</span></span> <span data-ttu-id="89973-155">如需有關 AD DS 使用之連接埠的詳細資訊，請參閱 [Active Directory 和 Active Directory Domain Services 連接埠需求][ad-ds-ports]。</span><span class="sxs-lookup"><span data-stu-id="89973-155">For detailed information on the ports used by AD DS, see [Active Directory and Active Directory Domain Services Port Requirements][ad-ds-ports].</span></span> <span data-ttu-id="89973-156">此外，也請確定 UDR 表格沒有透過此架構中使用的 NVA 來路由傳送 AD DS 流量。</span><span class="sxs-lookup"><span data-stu-id="89973-156">Also, ensure the UDR tables do not route AD DS traffic through the NVAs used in this architecture.</span></span> 

### <a name="active-directory-site"></a><span data-ttu-id="89973-157">Active Directory 站台</span><span class="sxs-lookup"><span data-stu-id="89973-157">Active Directory site</span></span>

<span data-ttu-id="89973-158">在 AD DS 中，站台代表裝置的實體位置、網路或集合。</span><span class="sxs-lookup"><span data-stu-id="89973-158">In AD DS, a site represents a physical location, network, or collection of devices.</span></span> <span data-ttu-id="89973-159">AD DS 站台可將彼此位置接近且透過高速網路連線的 AD DS 物件分組在一起，以便用來管理 AD DS 資料庫複寫。</span><span class="sxs-lookup"><span data-stu-id="89973-159">AD DS sites are used to manage AD DS database replication by grouping together AD DS objects that are located close to one another and are connected by a high speed network.</span></span> <span data-ttu-id="89973-160">AD DS 包含選取最佳策略的邏輯，以便在站台之間複寫 AD DS 資料庫。</span><span class="sxs-lookup"><span data-stu-id="89973-160">AD DS includes logic to select the best strategy for replacating the AD DS database between sites.</span></span>

<span data-ttu-id="89973-161">建議您建立一個 AD DS 站台，其中包括針對 Azure 中應用程式所定義的子網路。</span><span class="sxs-lookup"><span data-stu-id="89973-161">We recommend that you create an AD DS site including the subnets defined for your application in Azure.</span></span> <span data-ttu-id="89973-162">接著，設定內部部署 AD DS 站台之間的站台連結，AD DS 就會自動執行最有效率的資料庫複寫。</span><span class="sxs-lookup"><span data-stu-id="89973-162">Then, configure a site link between your on-premises AD DS sites, and AD DS will automatically perform the most efficient database replication possible.</span></span> <span data-ttu-id="89973-163">請注意，此資料庫複寫不需要超出初始設定。</span><span class="sxs-lookup"><span data-stu-id="89973-163">Note that this database replication requires little beyond the initial configuration.</span></span>

### <a name="active-directory-operations-masters"></a><span data-ttu-id="89973-164">Active Directory 操作主機</span><span class="sxs-lookup"><span data-stu-id="89973-164">Active Directory operations masters</span></span>

<span data-ttu-id="89973-165">操作主機角色可以指派給 AD DS 網域控制站，以支援複寫 AD DS 資料庫執行個體之間的一致性檢查。</span><span class="sxs-lookup"><span data-stu-id="89973-165">The operations masters role can be assigned to AD DS domain controllers to support consistency checking between instances of replicated AD DS databases.</span></span> <span data-ttu-id="89973-166">操作主機角色有五個：架構主機、網域命名主機、相關識別元主機、主要網域控制站主機模擬器和基礎結構主機。</span><span class="sxs-lookup"><span data-stu-id="89973-166">There are five operations master roles: schema master, domain naming master, relative identifier master, primary domain controller master emulator, and infrastructure master.</span></span> <span data-ttu-id="89973-167">如需有關這些角色的詳細資訊，請參閱[什麼是操作主機？][ad-ds-operations-masters]。</span><span class="sxs-lookup"><span data-stu-id="89973-167">For more information about these roles, see [What are Operations Masters?][ad-ds-operations-masters].</span></span>

<span data-ttu-id="89973-168">建議您不要將操作主機角色指派給在 Azure 中部署的網域控制站。</span><span class="sxs-lookup"><span data-stu-id="89973-168">We recommend you do not assign operations masters roles to the domain controllers deployed in Azure.</span></span>

### <a name="monitoring"></a><span data-ttu-id="89973-169">監視</span><span class="sxs-lookup"><span data-stu-id="89973-169">Monitoring</span></span>

<span data-ttu-id="89973-170">監視網域控制站 VM 的資源以及 AD DS 服務，並建立計劃以快速修正任何問題。</span><span class="sxs-lookup"><span data-stu-id="89973-170">Monitor the resources of the domain controller VMs as well as the AD DS Services and create a plan to quickly correct any problems.</span></span> <span data-ttu-id="89973-171">如需詳細資訊，請參閱[監視 Active Directory][monitoring_ad]。</span><span class="sxs-lookup"><span data-stu-id="89973-171">For more information, see [Monitoring Active Directory][monitoring_ad].</span></span> <span data-ttu-id="89973-172">您也可以在監視伺服器上安裝 [Microsoft Systems Center][microsoft_systems_center] 之類的工具 (請參見架構圖表)，以協助執行這些工作。</span><span class="sxs-lookup"><span data-stu-id="89973-172">You can also install tools such as [Microsoft Systems Center][microsoft_systems_center] on the monitoring server (see the architecture diagram) to help perform these tasks.</span></span>  

## <a name="scalability-considerations"></a><span data-ttu-id="89973-173">延展性考量</span><span class="sxs-lookup"><span data-stu-id="89973-173">Scalability considerations</span></span>

<span data-ttu-id="89973-174">AD DS 是針對延展性而設計。</span><span class="sxs-lookup"><span data-stu-id="89973-174">AD DS is designed for scalability.</span></span> <span data-ttu-id="89973-175">您不需要設定負載平衡器或流量控制器，就可以將要求導向到 AD DS 網域控制站。</span><span class="sxs-lookup"><span data-stu-id="89973-175">You don't need to configure a load balancer or traffic controller to direct requests to AD DS domain controllers.</span></span> <span data-ttu-id="89973-176">唯一的延展性考量是要以適合您網路負載需求的正確大小來設定執行 AD DS 的 VM、監視 VM 的負載，以及視需要相應增加或減少。</span><span class="sxs-lookup"><span data-stu-id="89973-176">The only scalability consideration is to configure the VMs running AD DS with the correct size for your network load requirements, monitor the load on the VMs, and scale up or down as necessary.</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="89973-177">可用性考量</span><span class="sxs-lookup"><span data-stu-id="89973-177">Availability considerations</span></span>

<span data-ttu-id="89973-178">將執行 AD DS 的 VM 部署至[可用性設定組][availability-set]。</span><span class="sxs-lookup"><span data-stu-id="89973-178">Deploy the VMs running AD DS into an [availability set][availability-set].</span></span> <span data-ttu-id="89973-179">此外，請考慮將[待命操作主機][standby-operations-masters]的角色指派到至少一部伺服器，且數量取決於您的需求。</span><span class="sxs-lookup"><span data-stu-id="89973-179">Also, consider assigning the role of [standby operations master][standby-operations-masters] to at least one server, and possibly more depending on your requirements.</span></span> <span data-ttu-id="89973-180">待命操作主機是操作主機的使用中複本，可在容錯移轉期間用來代替主要操作主機伺服器。</span><span class="sxs-lookup"><span data-stu-id="89973-180">A standby operations master is an active copy of the operations master that can be used in place of the primary operations masters server during fail over.</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="89973-181">管理性考量</span><span class="sxs-lookup"><span data-stu-id="89973-181">Manageability considerations</span></span>

<span data-ttu-id="89973-182">執行 AD DS 定期備份。</span><span class="sxs-lookup"><span data-stu-id="89973-182">Perform regular AD DS backups.</span></span> <span data-ttu-id="89973-183">不要只複製網域控制站的 VHD 檔案，而是要執行定期備份，因為 VHD 上的 AD DS 資料庫檔案經過複製之後可能會處於不一致的狀態，進而無法重新啟動資料庫。</span><span class="sxs-lookup"><span data-stu-id="89973-183">Don't simply copy the VHD files of domain controllers instead of performing regular backups, because the AD DS database file on the VHD may not be in a consistent state when it's copied, making it impossible to restart the database.</span></span>

<span data-ttu-id="89973-184">請不要使用 Azure 入口網站來關閉網域控制站 VM，</span><span class="sxs-lookup"><span data-stu-id="89973-184">Do not shut down a domain controller VM using Azure portal.</span></span> <span data-ttu-id="89973-185">而是從客體作業系統來關機並重新啟動。</span><span class="sxs-lookup"><span data-stu-id="89973-185">Instead, shut down and restart from the guest operating system.</span></span> <span data-ttu-id="89973-186">透過入口網站關機會導致將 VM 解除配置，進而同時重設 Active Directory 存放庫的 `VM-GenerationID` 和 `invocationID`。</span><span class="sxs-lookup"><span data-stu-id="89973-186">Shuting down through the portal causes the VM to be deallocated, which resets both the `VM-GenerationID` and the `invocationID` of the Active Directory repository.</span></span> <span data-ttu-id="89973-187">如此會捨棄 AD DS 相關的識別元 (RID) 集區，並將 SYSVOL 標示為非權威，因而可能需要重新設定網域控制站。</span><span class="sxs-lookup"><span data-stu-id="89973-187">This discards the AD DS relative identifier (RID) pool and marks SYSVOL as nonauthoritative, and may require reconfiguration of the domain controller.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="89973-188">安全性考量</span><span class="sxs-lookup"><span data-stu-id="89973-188">Security considerations</span></span>

<span data-ttu-id="89973-189">AD DS 伺服器負責提供驗證服務，容易引來攻擊。</span><span class="sxs-lookup"><span data-stu-id="89973-189">AD DS servers provide authentication services and are an attractive target for attacks.</span></span> <span data-ttu-id="89973-190">為保護其安全，請將 AD DS 伺服器放在不同的子網路，並使用 NSG 當作防火牆，以防止直接網際網路連線。</span><span class="sxs-lookup"><span data-stu-id="89973-190">To secure them, prevent direct Internet connectivity by placing the AD DS servers in a separate subnet with an NSG acting as a firewall.</span></span> <span data-ttu-id="89973-191">關閉 AD DS 伺服器上的所有連接埠，但驗證、授權及伺服器同步處理所需的連接埠除外。</span><span class="sxs-lookup"><span data-stu-id="89973-191">Close all ports on the AD DS servers except those necessary for authentication, authorization, and server synchronization.</span></span> <span data-ttu-id="89973-192">如需詳細資訊，請參閱 [Active Directory 和 Active Directory Domain Services 連接埠需求][ad-ds-ports]。</span><span class="sxs-lookup"><span data-stu-id="89973-192">For more information, see [Active Directory and Active Directory Domain Services Port Requirements][ad-ds-ports].</span></span>

<span data-ttu-id="89973-193">請考慮使用一對子網路和 NVA，在伺服器周圍實作額外的安全性周邊，如[在 Azure 中實作具有網際網路存取權的安全混合式網路架構][implementing-a-secure-hybrid-network-architecture-with-internet-access]中所述。</span><span class="sxs-lookup"><span data-stu-id="89973-193">Consider implementing an additional security perimeter around servers with a pair of subnets and NVAs, as described in [Implementing a secure hybrid network architecture with Internet access in Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].</span></span>

<span data-ttu-id="89973-194">使用 BitLocker 或 Azure 磁碟加密，將裝載 AD DS 資料庫的磁碟加密。</span><span class="sxs-lookup"><span data-stu-id="89973-194">Use either BitLocker or Azure disk encryption to encrypt the disk hosting the AD DS database.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="89973-195">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="89973-195">Deploy the solution</span></span>

<span data-ttu-id="89973-196">部署此參考架構的解決方案可在 [GitHub][github] 上取得。</span><span class="sxs-lookup"><span data-stu-id="89973-196">A solution is available on [Github][github] to deploy this reference architecture.</span></span> <span data-ttu-id="89973-197">您需要最新版的 [Azure CLI][azure-powershell]，才能執行可部署解決方案的 Powershell 指令碼。</span><span class="sxs-lookup"><span data-stu-id="89973-197">You will need the latest version of the [Azure CLI][azure-powershell] to run the Powershell script that deploys the solution.</span></span> <span data-ttu-id="89973-198">若要部署參考架構，請依照下列步驟執行：</span><span class="sxs-lookup"><span data-stu-id="89973-198">To deploy the reference architecture, follow these steps:</span></span>

1. <span data-ttu-id="89973-199">將解決方案資料夾從 [Github][github] 下載或複製到本機電腦。</span><span class="sxs-lookup"><span data-stu-id="89973-199">Download or clone the solution folder from [Github][github] to your local machine.</span></span>

2. <span data-ttu-id="89973-200">開啟 Azure CLI，並瀏覽至本機解決方案資料夾。</span><span class="sxs-lookup"><span data-stu-id="89973-200">Open the Azure CLI and navigate to the local solution folder.</span></span>

3. <span data-ttu-id="89973-201">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="89973-201">Run the following command:</span></span>
    ```Powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```
    <span data-ttu-id="89973-202">使用您的 Azure 訂用帳戶識別碼來取代 `<subscription id>` 。</span><span class="sxs-lookup"><span data-stu-id="89973-202">Replace `<subscription id>` with your Azure subscription ID.</span></span>
    <span data-ttu-id="89973-203">針對 `<location>`，請指定 Azure 區域，例如 `eastus` 或 `westus`。</span><span class="sxs-lookup"><span data-stu-id="89973-203">For `<location>`, specify an Azure region, such as `eastus` or `westus`.</span></span>
    <span data-ttu-id="89973-204">`<mode>` 參數可精密控制部署，而且可以是下列其中一個值：</span><span class="sxs-lookup"><span data-stu-id="89973-204">The `<mode>` parameter controls the granularity of the deployment, and can be one of the following values:</span></span>
    * <span data-ttu-id="89973-205">`Onpremise`：部署模擬的內部部署環境。</span><span class="sxs-lookup"><span data-stu-id="89973-205">`Onpremise`: deploys the simulated on-premises environment.</span></span>
    * <span data-ttu-id="89973-206">`Infrastructure`：在 Azure 中部署 VNet 基礎結構和跳躍箱。</span><span class="sxs-lookup"><span data-stu-id="89973-206">`Infrastructure`: deploys the VNet infrastructure and jump box in Azure.</span></span>
    * <span data-ttu-id="89973-207">`CreateVpn`：部署 Azure 虛擬網路閘道，並將其連線到模擬的內部部署網路。</span><span class="sxs-lookup"><span data-stu-id="89973-207">`CreateVpn`: deploys the Azure virtual network gateway and connects it to the simulated on-premises network.</span></span>
    * <span data-ttu-id="89973-208">`AzureADDS`：部署當作 AD DS 伺服器的 VM、將 Active Directory 部署到這些 VM，然後在 Azure 中部署網域。</span><span class="sxs-lookup"><span data-stu-id="89973-208">`AzureADDS`: deploys the VMs acting as AD DS servers, deploys Active Directory to these VMs, and deploys the domain in Azure.</span></span>
    * <span data-ttu-id="89973-209">`Workload`：部署公用和私用 DMZ，以及工作負載層。</span><span class="sxs-lookup"><span data-stu-id="89973-209">`Workload`: deploys the public and private DMZs and the workload tier.</span></span>
    * <span data-ttu-id="89973-210">`All`：部署所有先前的部署。</span><span class="sxs-lookup"><span data-stu-id="89973-210">`All`: deploys all of the preceding deployments.</span></span> <span data-ttu-id="89973-211">**如果您沒有現有的內部部署網路，但想要部署上述完整參考架構以供測試或評估之用，這是建議的選項。**</span><span class="sxs-lookup"><span data-stu-id="89973-211">**This is the recommended option if If you do not have an existing on-premises network but you want to deploy the complete reference architecture described above for testing or evaluation.**</span></span>

4. <span data-ttu-id="89973-212">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="89973-212">Wait for the deployment to complete.</span></span> <span data-ttu-id="89973-213">如果您要部署 `All` 部署，將需要數小時的時間。</span><span class="sxs-lookup"><span data-stu-id="89973-213">If you are deploying the `All` deployment, it will take several hours.</span></span>

## <a name="next-steps"></a><span data-ttu-id="89973-214">後續步驟</span><span class="sxs-lookup"><span data-stu-id="89973-214">Next steps</span></span>

* <span data-ttu-id="89973-215">了解[在 Azure 中建立 AD DS 資源樹系][adds-resource-forest]的最佳作法。</span><span class="sxs-lookup"><span data-stu-id="89973-215">Learn the best practices for [creating an AD DS resource forest][adds-resource-forest] in Azure.</span></span>
* <span data-ttu-id="89973-216">了解[在 Azure 中建立 Active Directory Federation Services (AD FS) 基礎結構][adfs]的最佳作法。</span><span class="sxs-lookup"><span data-stu-id="89973-216">Learn the best practices for [creating an Active Directory Federation Services (AD FS) infrastructure][adfs] in Azure.</span></span>

<!-- links -->
[adds-resource-forest]: adds-forest.md
[adfs]: adfs.md

[implementing-a-secure-hybrid-network-architecture]: ../dmz/secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ../dmz/secure-vnet-dmz.md

[active-directory-domain-services]: https://technet.microsoft.com/library/dd448614.aspx
[adds-data-disks]: https://msdn.microsoft.com/library/azure/jj156090.aspx#BKMK_PlaceDB
[ad-ds-operations-masters]: https://technet.microsoft.com/library/cc779716(v=ws.10).aspx
[ad-ds-ports]: https://technet.microsoft.com/library/dd772723(v=ws.11).aspx
[availability-set]: /azure/virtual-machines/virtual-machines-windows-create-availability-set
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[azure-powershell]: /powershell/azureps-cmdlets-docs
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[capacity-planning-for-adds]: http://social.technet.microsoft.com/wiki/contents/articles/14355.capacity-planning-for-active-directory-domain-services.aspx
[considerations]: ./considerations.md
[GitHub]: https://github.com/mspnp/reference-architectures/tree/master/identity/adds-extend-domain
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[security-considerations]: #security-considerations
[set-a-static-ip-address]: https://azure.microsoft.com/documentation/articles/virtual-networks-static-private-ip-arm-pportal/
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[visio-download]: https://archcenter.azureedge.net/cdn/identity-architectures.vsdx
[vm-windows-sizes]: /azure/virtual-machines/virtual-machines-windows-sizes

[0]: ./images/adds-extend-domain.png "使用 Active Directory 保護混合式網路架構的安全"

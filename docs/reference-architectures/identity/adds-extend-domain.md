---
title: 將您的內部部署 Active Directory 網域擴充至 Azure
titleSuffix: Azure Reference Architectures
description: 在 Azure 虛擬網路中部署 Active Directory 網域服務 (AD DS)。
author: telmosampaio
ms.date: 05/02/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18, identity
ms.openlocfilehash: c617a0ceba900fc9cd78eff21aadf5c94f6b143b
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640340"
---
# <a name="extend-your-on-premises-active-directory-domain-to-azure"></a><span data-ttu-id="fb904-103">將您的內部部署 Active Directory 網域擴充至 Azure</span><span class="sxs-lookup"><span data-stu-id="fb904-103">Extend your on-premises Active Directory domain to Azure</span></span>

<span data-ttu-id="fb904-104">此架構會示範如何將內部部署 Active Directory 網域擴充至 Azure，以提供分散式的驗證服務。</span><span class="sxs-lookup"><span data-stu-id="fb904-104">This architecture shows how to extend an on-premises Active Directory domain to Azure to provide distributed authentication services.</span></span> <span data-ttu-id="fb904-105">[**部署這個解決方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="fb904-105">[**Deploy this solution**](#deploy-the-solution).</span></span>

![使用 Active Directory 保護混合式網路架構的安全](./images/adds-extend-domain.png)

<span data-ttu-id="fb904-107">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="fb904-107">*Download a [Visio file][visio-download] of this architecture.*</span></span>

<span data-ttu-id="fb904-108">如果您的應用程式部分裝載在內部，而且部分在 Azure 中，它可能更有效率的方式複寫在 Azure 中的 Active Directory 網域服務 (AD DS)。</span><span class="sxs-lookup"><span data-stu-id="fb904-108">If your application is hosted partly on-premises and partly in Azure, it may be more efficient to replicate Active Directory Domain Services (AD DS) in Azure.</span></span> <span data-ttu-id="fb904-109">這可以減少將驗證要求從雲端傳回執行內部部署 AD DS 所造成的延遲。</span><span class="sxs-lookup"><span data-stu-id="fb904-109">This can reduce the latency caused by sending authentication requests from the cloud back to AD DS running on-premises.</span></span>

<span data-ttu-id="fb904-110">當內部部署網路與 Azure 虛擬網路透過 VPN 或 ExpressRoute 連線連接時，通常會使用這個架構。</span><span class="sxs-lookup"><span data-stu-id="fb904-110">This architecture is commonly used when the on-premises network and the Azure virtual network are connected by a VPN or ExpressRoute connection.</span></span> <span data-ttu-id="fb904-111">此架構也支援雙向複寫，也就是說，您可以在內部部署或雲端上進行變更，而且這兩個來源會保持一致。</span><span class="sxs-lookup"><span data-stu-id="fb904-111">This architecture also supports bidirectional replication, meaning changes can be made either on-premises or in the cloud, and both sources will be kept consistent.</span></span> <span data-ttu-id="fb904-112">此架構的典型用途包括在內部部署與 Azure 之間散佈功能的混合式應用程式，以及使用 Active Directory 執行驗證的應用程式和服務。</span><span class="sxs-lookup"><span data-stu-id="fb904-112">Typical uses for this architecture include hybrid applications in which functionality is distributed between on-premises and Azure, and applications and services that perform authentication using Active Directory.</span></span>

<span data-ttu-id="fb904-113">如需了解其他考量，請參閱[選擇解決方案以整合內部部署 Active Directory 與 Azure][considerations]。</span><span class="sxs-lookup"><span data-stu-id="fb904-113">For additional considerations, see [Choose a solution for integrating on-premises Active Directory with Azure][considerations].</span></span>

## <a name="architecture"></a><span data-ttu-id="fb904-114">架構</span><span class="sxs-lookup"><span data-stu-id="fb904-114">Architecture</span></span>

<span data-ttu-id="fb904-115">此架構可擴充 [Azure 與網際網路之間的 DMZ][implementing-a-secure-hybrid-network-architecture-with-internet-access] 所示的架構。</span><span class="sxs-lookup"><span data-stu-id="fb904-115">This architecture extends the architecture shown in [DMZ between Azure and the Internet][implementing-a-secure-hybrid-network-architecture-with-internet-access].</span></span> <span data-ttu-id="fb904-116">其元件如下。</span><span class="sxs-lookup"><span data-stu-id="fb904-116">It has the following components.</span></span>

- <span data-ttu-id="fb904-117">**內部部署網路**。</span><span class="sxs-lookup"><span data-stu-id="fb904-117">**On-premises network**.</span></span> <span data-ttu-id="fb904-118">內部部署網路包括可以針對內部部署元件執行驗證和授權的本機 Active Directory 伺服器。</span><span class="sxs-lookup"><span data-stu-id="fb904-118">The on-premises network includes local Active Directory servers that can perform authentication and authorization for components located on-premises.</span></span>
- <span data-ttu-id="fb904-119">**Active Directory 伺服器**。</span><span class="sxs-lookup"><span data-stu-id="fb904-119">**Active Directory servers**.</span></span> <span data-ttu-id="fb904-120">這些網域控制站是雲端中執行的 VM，負責實作目錄服務 (AD DS)。</span><span class="sxs-lookup"><span data-stu-id="fb904-120">These are domain controllers implementing directory services (AD DS) running as VMs in the cloud.</span></span> <span data-ttu-id="fb904-121">這些伺服器可以驗證在 Azure 虛擬網路中執行的元件。</span><span class="sxs-lookup"><span data-stu-id="fb904-121">These servers can provide authentication of components running in your Azure virtual network.</span></span>
- <span data-ttu-id="fb904-122">**Active Directory 子網路**。</span><span class="sxs-lookup"><span data-stu-id="fb904-122">**Active Directory subnet**.</span></span> <span data-ttu-id="fb904-123">AD DS 伺服器會裝載在不同的子網路中。</span><span class="sxs-lookup"><span data-stu-id="fb904-123">The AD DS servers are hosted in a separate subnet.</span></span> <span data-ttu-id="fb904-124">網路安全性群組 (NSG) 規則可保護 AD DS 伺服器，並提供防火牆以防禦來自非預期來源的流量。</span><span class="sxs-lookup"><span data-stu-id="fb904-124">Network security group (NSG) rules protect the AD DS servers and provide a firewall against traffic from unexpected sources.</span></span>
- <span data-ttu-id="fb904-125">**Azure 閘道和 Active Directory 同步處理**。</span><span class="sxs-lookup"><span data-stu-id="fb904-125">**Azure Gateway and Active Directory synchronization**.</span></span> <span data-ttu-id="fb904-126">Azure 閘道會提供內部部署網路與 Azure VNet 之間的連線。</span><span class="sxs-lookup"><span data-stu-id="fb904-126">The Azure gateway provides a connection between the on-premises network and the Azure VNet.</span></span> <span data-ttu-id="fb904-127">這可能是 [VPN 連線][azure-vpn-gateway]或 [Azure ExpressRoute][azure-expressroute]。</span><span class="sxs-lookup"><span data-stu-id="fb904-127">This can be a [VPN connection][azure-vpn-gateway] or [Azure ExpressRoute][azure-expressroute].</span></span> <span data-ttu-id="fb904-128">雲端與與內部部署環境中 Active Directory 伺服器之間的所有同步處理要求都會通過閘道。</span><span class="sxs-lookup"><span data-stu-id="fb904-128">All synchronization requests between the Active Directory servers in the cloud and on-premises pass through the gateway.</span></span> <span data-ttu-id="fb904-129">使用者定義的路由 (UDR) 會針對傳送至 Azure 的內部部署流量處理路由傳送。</span><span class="sxs-lookup"><span data-stu-id="fb904-129">User-defined routes (UDRs) handle routing for on-premises traffic that passes to Azure.</span></span> <span data-ttu-id="fb904-130">往返 Active Directory 伺服器的流量不會通過此案例中使用的網路虛擬設備 (NVA)。</span><span class="sxs-lookup"><span data-stu-id="fb904-130">Traffic to and from the Active Directory servers does not pass through the network virtual appliances (NVAs) used in this scenario.</span></span>

<span data-ttu-id="fb904-131">如需有關設定 UDR 與 NVA 的詳細資訊，請參閱[在 Azure 中實作安全的混合式網路架構][implementing-a-secure-hybrid-network-architecture]。</span><span class="sxs-lookup"><span data-stu-id="fb904-131">For more information about configuring UDRs and the NVAs, see [Implementing a secure hybrid network architecture in Azure][implementing-a-secure-hybrid-network-architecture].</span></span>

## <a name="recommendations"></a><span data-ttu-id="fb904-132">建議</span><span class="sxs-lookup"><span data-stu-id="fb904-132">Recommendations</span></span>

<span data-ttu-id="fb904-133">下列建議適用於大部分的案例。</span><span class="sxs-lookup"><span data-stu-id="fb904-133">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="fb904-134">除非您有特定的需求會覆寫它們，否則請遵循下列建議。</span><span class="sxs-lookup"><span data-stu-id="fb904-134">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="vm-recommendations"></a><span data-ttu-id="fb904-135">VM 建議</span><span class="sxs-lookup"><span data-stu-id="fb904-135">VM recommendations</span></span>

<span data-ttu-id="fb904-136">根據預期的驗證要求量，決定您的 [VM 大小][vm-windows-sizes]需求。</span><span class="sxs-lookup"><span data-stu-id="fb904-136">Determine your [VM size][vm-windows-sizes] requirements based on the expected volume of authentication requests.</span></span> <span data-ttu-id="fb904-137">針對在內部部署環境中裝載 AD DS 的電腦，使用其規格作為起點，以比對 Azure VM 的大小。</span><span class="sxs-lookup"><span data-stu-id="fb904-137">Use the specifications of the machines hosting AD DS on premises as a starting point, and match them with the Azure VM sizes.</span></span> <span data-ttu-id="fb904-138">一旦部署之後，請根據 VM 上的實際負載，監視使用量並相應增加或減少。</span><span class="sxs-lookup"><span data-stu-id="fb904-138">Once deployed, monitor utilization and scale up or down based on the actual load on the VMs.</span></span> <span data-ttu-id="fb904-139">如需有關調整 AD DS 網域控制站大小的詳細資訊，請參閱 [Active Directory Domain Services 的容量規劃][capacity-planning-for-adds]。</span><span class="sxs-lookup"><span data-stu-id="fb904-139">For more information about sizing AD DS domain controllers, see [Capacity Planning for Active Directory Domain Services][capacity-planning-for-adds].</span></span>

<span data-ttu-id="fb904-140">建立個別的虛擬資料磁碟，以儲存 Active Directory 的資料庫、記錄和 SYSVOL。</span><span class="sxs-lookup"><span data-stu-id="fb904-140">Create a separate virtual data disk for storing the database, logs, and SYSVOL for Active Directory.</span></span> <span data-ttu-id="fb904-141">請不要將這些項目儲存在與作業系統相同的磁碟上。</span><span class="sxs-lookup"><span data-stu-id="fb904-141">Do not store these items on the same disk as the operating system.</span></span> <span data-ttu-id="fb904-142">請注意，連線到 VM 的資料磁碟預設會使用即刻寫入快取。</span><span class="sxs-lookup"><span data-stu-id="fb904-142">Note that by default, data disks that are attached to a VM use write-through caching.</span></span> <span data-ttu-id="fb904-143">不過，這種形式的快取可能會與 AD DS 的需求發生衝突。</span><span class="sxs-lookup"><span data-stu-id="fb904-143">However, this form of caching can conflict with the requirements of AD DS.</span></span> <span data-ttu-id="fb904-144">因此，請將資料磁碟上的 [主機快取偏好設定] 設定設為 [無]。</span><span class="sxs-lookup"><span data-stu-id="fb904-144">For this reason, set the *Host Cache Preference* setting on the data disk to *None*.</span></span>

<span data-ttu-id="fb904-145">至少將兩個執行 AD DS 的 VM 部署為網域控制站，並將其新增至[可用性設定組][availability-set]。</span><span class="sxs-lookup"><span data-stu-id="fb904-145">Deploy at least two VMs running AD DS as domain controllers and add them to an [availability set][availability-set].</span></span>

### <a name="networking-recommendations"></a><span data-ttu-id="fb904-146">網路功能的建議</span><span class="sxs-lookup"><span data-stu-id="fb904-146">Networking recommendations</span></span>

<span data-ttu-id="fb904-147">使用靜態私人 IP 位址，設定每個 AD DS 伺服器的 VM 網路介面 (NIC)，以獲得完整的網域名稱服務 (DNS) 支援。</span><span class="sxs-lookup"><span data-stu-id="fb904-147">Configure the VM network interface (NIC) for each AD DS server with a static private IP address for full domain name service (DNS) support.</span></span> <span data-ttu-id="fb904-148">如需詳細資訊，請參閱[如何在 Azure 入口網站中設定靜態私人 IP 位址][set-a-static-ip-address]。</span><span class="sxs-lookup"><span data-stu-id="fb904-148">For more information, see [How to set a static private IP address in the Azure portal][set-a-static-ip-address].</span></span>

> [!NOTE]
> <span data-ttu-id="fb904-149">請不要使用公用 IP 位址設定任何 AD DS 的 VM NIC。</span><span class="sxs-lookup"><span data-stu-id="fb904-149">Do not configure the VM NIC for any AD DS with a public IP address.</span></span> <span data-ttu-id="fb904-150">如需詳細資料，請參閱[安全性考量][security-considerations]。</span><span class="sxs-lookup"><span data-stu-id="fb904-150">See [Security considerations][security-considerations] for more details.</span></span>
>

<span data-ttu-id="fb904-151">Active Directory 子網路 NSG 需要有允許來自內部部署環境傳入流量的規則。</span><span class="sxs-lookup"><span data-stu-id="fb904-151">The Active Directory subnet NSG requires rules to permit incoming traffic from on-premises.</span></span> <span data-ttu-id="fb904-152">如需有關 AD DS 使用之連接埠的詳細資訊，請參閱 [Active Directory 和 Active Directory Domain Services 連接埠需求][ad-ds-ports]。</span><span class="sxs-lookup"><span data-stu-id="fb904-152">For detailed information on the ports used by AD DS, see [Active Directory and Active Directory Domain Services Port Requirements][ad-ds-ports].</span></span> <span data-ttu-id="fb904-153">此外，也請確定 UDR 表格沒有透過此架構中使用的 NVA 來路由傳送 AD DS 流量。</span><span class="sxs-lookup"><span data-stu-id="fb904-153">Also, ensure the UDR tables do not route AD DS traffic through the NVAs used in this architecture.</span></span>

### <a name="active-directory-site"></a><span data-ttu-id="fb904-154">Active Directory 站台</span><span class="sxs-lookup"><span data-stu-id="fb904-154">Active Directory site</span></span>

<span data-ttu-id="fb904-155">在 AD DS 中，站台代表裝置的實體位置、網路或集合。</span><span class="sxs-lookup"><span data-stu-id="fb904-155">In AD DS, a site represents a physical location, network, or collection of devices.</span></span> <span data-ttu-id="fb904-156">AD DS 站台可將彼此位置接近且透過高速網路連線的 AD DS 物件分組在一起，以便用來管理 AD DS 資料庫複寫。</span><span class="sxs-lookup"><span data-stu-id="fb904-156">AD DS sites are used to manage AD DS database replication by grouping together AD DS objects that are located close to one another and are connected by a high speed network.</span></span> <span data-ttu-id="fb904-157">AD DS 包含選取最佳策略的邏輯，以便在站台之間複寫 AD DS 資料庫。</span><span class="sxs-lookup"><span data-stu-id="fb904-157">AD DS includes logic to select the best strategy for replacating the AD DS database between sites.</span></span>

<span data-ttu-id="fb904-158">建議您建立一個 AD DS 站台，其中包括針對 Azure 中應用程式所定義的子網路。</span><span class="sxs-lookup"><span data-stu-id="fb904-158">We recommend that you create an AD DS site including the subnets defined for your application in Azure.</span></span> <span data-ttu-id="fb904-159">接著，設定內部部署 AD DS 站台之間的站台連結，AD DS 就會自動執行最有效率的資料庫複寫。</span><span class="sxs-lookup"><span data-stu-id="fb904-159">Then, configure a site link between your on-premises AD DS sites, and AD DS will automatically perform the most efficient database replication possible.</span></span> <span data-ttu-id="fb904-160">請注意，此資料庫複寫不需要超出初始設定。</span><span class="sxs-lookup"><span data-stu-id="fb904-160">Note that this database replication requires little beyond the initial configuration.</span></span>

### <a name="active-directory-operations-masters"></a><span data-ttu-id="fb904-161">Active Directory 操作主機</span><span class="sxs-lookup"><span data-stu-id="fb904-161">Active Directory operations masters</span></span>

<span data-ttu-id="fb904-162">操作主機角色可以指派給 AD DS 網域控制站，以支援複寫 AD DS 資料庫執行個體之間的一致性檢查。</span><span class="sxs-lookup"><span data-stu-id="fb904-162">The operations masters role can be assigned to AD DS domain controllers to support consistency checking between instances of replicated AD DS databases.</span></span> <span data-ttu-id="fb904-163">操作主機角色有五個：架構主機、網域命名主機、相關識別元主機、主要網域控制站主機模擬器和基礎結構主機。</span><span class="sxs-lookup"><span data-stu-id="fb904-163">There are five operations master roles: schema master, domain naming master, relative identifier master, primary domain controller master emulator, and infrastructure master.</span></span> <span data-ttu-id="fb904-164">如需有關這些角色的詳細資訊，請參閱[什麼是操作主機？][ad-ds-operations-masters]。</span><span class="sxs-lookup"><span data-stu-id="fb904-164">For more information about these roles, see [What are Operations Masters?][ad-ds-operations-masters].</span></span>

<span data-ttu-id="fb904-165">建議您不要將操作主機角色指派給在 Azure 中部署的網域控制站。</span><span class="sxs-lookup"><span data-stu-id="fb904-165">We recommend you do not assign operations masters roles to the domain controllers deployed in Azure.</span></span>

### <a name="monitoring"></a><span data-ttu-id="fb904-166">監視</span><span class="sxs-lookup"><span data-stu-id="fb904-166">Monitoring</span></span>

<span data-ttu-id="fb904-167">監視網域控制站 VM 的資源以及 AD DS 服務，並建立計劃以快速修正任何問題。</span><span class="sxs-lookup"><span data-stu-id="fb904-167">Monitor the resources of the domain controller VMs as well as the AD DS Services and create a plan to quickly correct any problems.</span></span> <span data-ttu-id="fb904-168">如需詳細資訊，請參閱[監視 Active Directory][monitoring_ad]。</span><span class="sxs-lookup"><span data-stu-id="fb904-168">For more information, see [Monitoring Active Directory][monitoring_ad].</span></span> <span data-ttu-id="fb904-169">您也可以在監視伺服器上安裝 [Microsoft Systems Center][microsoft_systems_center] 之類的工具 (請參見架構圖表)，以協助執行這些工作。</span><span class="sxs-lookup"><span data-stu-id="fb904-169">You can also install tools such as [Microsoft Systems Center][microsoft_systems_center] on the monitoring server (see the architecture diagram) to help perform these tasks.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="fb904-170">延展性考量</span><span class="sxs-lookup"><span data-stu-id="fb904-170">Scalability considerations</span></span>

<span data-ttu-id="fb904-171">AD DS 是針對延展性而設計。</span><span class="sxs-lookup"><span data-stu-id="fb904-171">AD DS is designed for scalability.</span></span> <span data-ttu-id="fb904-172">您不需要設定負載平衡器或流量控制器，就可以將要求導向到 AD DS 網域控制站。</span><span class="sxs-lookup"><span data-stu-id="fb904-172">You don't need to configure a load balancer or traffic controller to direct requests to AD DS domain controllers.</span></span> <span data-ttu-id="fb904-173">唯一的延展性考量是要以適合您網路負載需求的正確大小來設定執行 AD DS 的 VM、監視 VM 的負載，以及視需要相應增加或減少。</span><span class="sxs-lookup"><span data-stu-id="fb904-173">The only scalability consideration is to configure the VMs running AD DS with the correct size for your network load requirements, monitor the load on the VMs, and scale up or down as necessary.</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="fb904-174">可用性考量</span><span class="sxs-lookup"><span data-stu-id="fb904-174">Availability considerations</span></span>

<span data-ttu-id="fb904-175">將執行 AD DS 的 VM 部署至[可用性設定組][availability-set]。</span><span class="sxs-lookup"><span data-stu-id="fb904-175">Deploy the VMs running AD DS into an [availability set][availability-set].</span></span> <span data-ttu-id="fb904-176">此外，請考慮將[待命操作主機][standby-operations-masters]的角色指派到至少一部伺服器，且數量取決於您的需求。</span><span class="sxs-lookup"><span data-stu-id="fb904-176">Also, consider assigning the role of [standby operations master][standby-operations-masters] to at least one server, and possibly more depending on your requirements.</span></span> <span data-ttu-id="fb904-177">待命操作主機是操作主機的使用中複本，可在容錯移轉期間用來代替主要操作主機伺服器。</span><span class="sxs-lookup"><span data-stu-id="fb904-177">A standby operations master is an active copy of the operations master that can be used in place of the primary operations masters server during fail over.</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="fb904-178">管理性考量</span><span class="sxs-lookup"><span data-stu-id="fb904-178">Manageability considerations</span></span>

<span data-ttu-id="fb904-179">執行 AD DS 定期備份。</span><span class="sxs-lookup"><span data-stu-id="fb904-179">Perform regular AD DS backups.</span></span> <span data-ttu-id="fb904-180">不要只複製網域控制站的 VHD 檔案，而是要執行定期備份，因為 VHD 上的 AD DS 資料庫檔案經過複製之後可能會處於不一致的狀態，進而無法重新啟動資料庫。</span><span class="sxs-lookup"><span data-stu-id="fb904-180">Don't simply copy the VHD files of domain controllers instead of performing regular backups, because the AD DS database file on the VHD may not be in a consistent state when it's copied, making it impossible to restart the database.</span></span>

<span data-ttu-id="fb904-181">請不要使用 Azure 入口網站來關閉網域控制站 VM，</span><span class="sxs-lookup"><span data-stu-id="fb904-181">Do not shut down a domain controller VM using Azure portal.</span></span> <span data-ttu-id="fb904-182">而是從客體作業系統來關機並重新啟動。</span><span class="sxs-lookup"><span data-stu-id="fb904-182">Instead, shut down and restart from the guest operating system.</span></span> <span data-ttu-id="fb904-183">透過入口網站關機會導致將 VM 解除配置，進而同時重設 Active Directory 存放庫的 `VM-GenerationID` 和 `invocationID`。</span><span class="sxs-lookup"><span data-stu-id="fb904-183">Shutting down through the portal causes the VM to be deallocated, which resets both the `VM-GenerationID` and the `invocationID` of the Active Directory repository.</span></span> <span data-ttu-id="fb904-184">如此會捨棄 AD DS 相關的識別元 (RID) 集區，並將 SYSVOL 標示為非權威，因而可能需要重新設定網域控制站。</span><span class="sxs-lookup"><span data-stu-id="fb904-184">This discards the AD DS relative identifier (RID) pool and marks SYSVOL as nonauthoritative, and may require reconfiguration of the domain controller.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="fb904-185">安全性考量</span><span class="sxs-lookup"><span data-stu-id="fb904-185">Security considerations</span></span>

<span data-ttu-id="fb904-186">AD DS 伺服器負責提供驗證服務，容易引來攻擊。</span><span class="sxs-lookup"><span data-stu-id="fb904-186">AD DS servers provide authentication services and are an attractive target for attacks.</span></span> <span data-ttu-id="fb904-187">為保護其安全，請將 AD DS 伺服器放在不同的子網路，並使用 NSG 當作防火牆，以防止直接網際網路連線。</span><span class="sxs-lookup"><span data-stu-id="fb904-187">To secure them, prevent direct Internet connectivity by placing the AD DS servers in a separate subnet with an NSG acting as a firewall.</span></span> <span data-ttu-id="fb904-188">關閉 AD DS 伺服器上的所有連接埠，但驗證、授權及伺服器同步處理所需的連接埠除外。</span><span class="sxs-lookup"><span data-stu-id="fb904-188">Close all ports on the AD DS servers except those necessary for authentication, authorization, and server synchronization.</span></span> <span data-ttu-id="fb904-189">如需詳細資訊，請參閱 [Active Directory 和 Active Directory Domain Services 連接埠需求][ad-ds-ports]。</span><span class="sxs-lookup"><span data-stu-id="fb904-189">For more information, see [Active Directory and Active Directory Domain Services Port Requirements][ad-ds-ports].</span></span>

<span data-ttu-id="fb904-190">請考慮使用一對子網路和 NVA，在伺服器周圍實作額外的安全性周邊，如[在 Azure 中實作具有網際網路存取權的安全混合式網路架構][implementing-a-secure-hybrid-network-architecture-with-internet-access]中所述。</span><span class="sxs-lookup"><span data-stu-id="fb904-190">Consider implementing an additional security perimeter around servers with a pair of subnets and NVAs, as described in [Implementing a secure hybrid network architecture with Internet access in Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].</span></span>

<span data-ttu-id="fb904-191">使用 BitLocker 或 Azure 磁碟加密，將裝載 AD DS 資料庫的磁碟加密。</span><span class="sxs-lookup"><span data-stu-id="fb904-191">Use either BitLocker or Azure disk encryption to encrypt the disk hosting the AD DS database.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="fb904-192">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="fb904-192">Deploy the solution</span></span>

<span data-ttu-id="fb904-193">適用於此架構的部署可在 [GitHub][github] 上取得。</span><span class="sxs-lookup"><span data-stu-id="fb904-193">A deployment for this architecture is available on [GitHub][github].</span></span> <span data-ttu-id="fb904-194">請注意，整個部署最多可能需要兩個小時，其中包括建立 VPN 閘道和執行設定 AD DS 的指令碼。</span><span class="sxs-lookup"><span data-stu-id="fb904-194">Note that the entire deployment can take up to two hours, which includes creating the VPN gateway and running the scripts that configure AD DS.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="fb904-195">必要條件</span><span class="sxs-lookup"><span data-stu-id="fb904-195">Prerequisites</span></span>

1. <span data-ttu-id="fb904-196">複製、派生或下載適用於 [GitHub 存放庫](https://github.com/mspnp/identity-reference-architectures)的 zip 檔案。</span><span class="sxs-lookup"><span data-stu-id="fb904-196">Clone, fork, or download the zip file for the [GitHub repository](https://github.com/mspnp/identity-reference-architectures).</span></span>

2. <span data-ttu-id="fb904-197">安裝 [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest)。</span><span class="sxs-lookup"><span data-stu-id="fb904-197">Install [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest).</span></span>

3. <span data-ttu-id="fb904-198">安裝 [Azure 建置組塊](https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks) npm 封裝。</span><span class="sxs-lookup"><span data-stu-id="fb904-198">Install the [Azure building blocks](https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks) npm package.</span></span>

   ```bash
   npm install -g @mspnp/azure-building-blocks
   ```

4. <span data-ttu-id="fb904-199">從命令提示字元、Bash 提示字元或 PowerShell 提示字元中登入 Azure 帳戶，如下所示：</span><span class="sxs-lookup"><span data-stu-id="fb904-199">From a command prompt, bash prompt, or PowerShell prompt, sign into your Azure account as follows:</span></span>

   ```bash
   az login
   ```

### <a name="deploy-the-simulated-on-premises-datacenter"></a><span data-ttu-id="fb904-200">部署模擬的內部部署資料中心</span><span class="sxs-lookup"><span data-stu-id="fb904-200">Deploy the simulated on-premises datacenter</span></span>

1. <span data-ttu-id="fb904-201">巡覽至 GitHub 存放庫的 `identity/adds-extend-domain` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fb904-201">Navigate to the `identity/adds-extend-domain` folder of the GitHub repository.</span></span>

2. <span data-ttu-id="fb904-202">開啟 `onprem.json` 檔案。</span><span class="sxs-lookup"><span data-stu-id="fb904-202">Open the `onprem.json` file.</span></span> <span data-ttu-id="fb904-203">搜尋 `adminPassword` 和 `Password` 的執行個體，並新增密碼的值。</span><span class="sxs-lookup"><span data-stu-id="fb904-203">Search for instances of `adminPassword` and `Password` and add values for the passwords.</span></span>

3. <span data-ttu-id="fb904-204">執行下列命令，並等待部署完成：</span><span class="sxs-lookup"><span data-stu-id="fb904-204">Run the following command and wait for the deployment to finish:</span></span>

    ```bash
    azbb -s <subscription_id> -g <resource group> -l <location> -p onprem.json --deploy
    ```

### <a name="deploy-the-azure-vnet"></a><span data-ttu-id="fb904-205">部署 Azure VNet</span><span class="sxs-lookup"><span data-stu-id="fb904-205">Deploy the Azure VNet</span></span>

1. <span data-ttu-id="fb904-206">開啟 `azure.json` 檔案。</span><span class="sxs-lookup"><span data-stu-id="fb904-206">Open the `azure.json` file.</span></span>  <span data-ttu-id="fb904-207">搜尋 `adminPassword` 和 `Password` 的執行個體，並新增密碼的值。</span><span class="sxs-lookup"><span data-stu-id="fb904-207">Search for instances of `adminPassword` and `Password` and add values for the passwords.</span></span>

2. <span data-ttu-id="fb904-208">在同一個檔案中，搜尋 `sharedKey` 的執行個體，並輸入 VPN 連線的共用金鑰。</span><span class="sxs-lookup"><span data-stu-id="fb904-208">In the same file, search for instances of `sharedKey` and enter shared keys for the VPN connection.</span></span>

    ```json
    "sharedKey": "",
    ```

3. <span data-ttu-id="fb904-209">執行下列命令，並等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="fb904-209">Run the following command and wait for the deployment to finish.</span></span>

    ```bash
    azbb -s <subscription_id> -g <resource group> -l <location> -p azure.json --deploy
    ```

   <span data-ttu-id="fb904-210">部署至與內部 VNet 相同的資源群組。</span><span class="sxs-lookup"><span data-stu-id="fb904-210">Deploy to the same resource group as the on-premises VNet.</span></span>

### <a name="test-connectivity-with-the-azure-vnet"></a><span data-ttu-id="fb904-211">測試 Azure VNet 的連線</span><span class="sxs-lookup"><span data-stu-id="fb904-211">Test connectivity with the Azure VNet</span></span>

<span data-ttu-id="fb904-212">部署完成後，您可以測試從模擬的內部部署環境到 Azure VNet 的連線。</span><span class="sxs-lookup"><span data-stu-id="fb904-212">After deployment completes, you can test conectivity from the simulated on-premises environment to the Azure VNet.</span></span>

1. <span data-ttu-id="fb904-213">使用 Azure 入口網站，巡覽至您所建立的資源群組。</span><span class="sxs-lookup"><span data-stu-id="fb904-213">Use the Azure portal, navigate to the resource group that you created.</span></span>

2. <span data-ttu-id="fb904-214">尋找名為 `ra-onpremise-mgmt-vm1` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="fb904-214">Find the VM named `ra-onpremise-mgmt-vm1`.</span></span>

3. <span data-ttu-id="fb904-215">按一下 `Connect` 以開啟 VM 的遠端桌面工作階段。</span><span class="sxs-lookup"><span data-stu-id="fb904-215">Click `Connect` to open a remote desktop session to the VM.</span></span> <span data-ttu-id="fb904-216">使用者名稱是 `contoso\testuser`，而密碼是您在 `onprem.json` 參數檔案中指定的密碼。</span><span class="sxs-lookup"><span data-stu-id="fb904-216">The username is `contoso\testuser`, and the password is the one that you specified in the `onprem.json` parameter file.</span></span>

4. <span data-ttu-id="fb904-217">從遠端桌面工作階段中開啟連至 10.0.4.4 (此為 VM `adds-vm1` 的 IP 位址) 的另一個遠端桌面工作階段。</span><span class="sxs-lookup"><span data-stu-id="fb904-217">From inside your remote desktop session, open another remote desktop session to 10.0.4.4, which is the IP address of the VM named `adds-vm1`.</span></span> <span data-ttu-id="fb904-218">使用者名稱是 `contoso\testuser`，而密碼是您在 `azure.json` 參數檔案中指定的密碼。</span><span class="sxs-lookup"><span data-stu-id="fb904-218">The username is `contoso\testuser`, and the password is the one that you specified in the `azure.json` parameter file.</span></span>

5. <span data-ttu-id="fb904-219">從 `adds-vm1` 的遠端桌面工作階段中移至**伺服器管理員**，然後按一下 [新增其他要管理的伺服器]。</span><span class="sxs-lookup"><span data-stu-id="fb904-219">From inside the remote desktop session for `adds-vm1`, go to **Server Manager** and click **Add other servers to manage**.</span></span>

6. <span data-ttu-id="fb904-220">在 [Active Directory] 索引標籤中，按一下 [立即尋找]。</span><span class="sxs-lookup"><span data-stu-id="fb904-220">In the **Active Directory** tab, click **Find now**.</span></span> <span data-ttu-id="fb904-221">您應該會看到 AD、AD DS 和 Web VM 的清單。</span><span class="sxs-lookup"><span data-stu-id="fb904-221">You should see a list of the AD, AD DS, and Web VMs.</span></span>

   ![新增伺服器對話方塊的螢幕擷取畫面](./images/add-servers-dialog.png)

## <a name="next-steps"></a><span data-ttu-id="fb904-223">後續步驟</span><span class="sxs-lookup"><span data-stu-id="fb904-223">Next steps</span></span>

- <span data-ttu-id="fb904-224">了解[在 Azure 中建立 AD DS 資源樹系][adds-resource-forest]的最佳作法。</span><span class="sxs-lookup"><span data-stu-id="fb904-224">Learn the best practices for [creating an AD DS resource forest][adds-resource-forest] in Azure.</span></span>
- <span data-ttu-id="fb904-225">了解[在 Azure 中建立 Active Directory Federation Services (AD FS) 基礎結構][adfs]的最佳作法。</span><span class="sxs-lookup"><span data-stu-id="fb904-225">Learn the best practices for [creating an Active Directory Federation Services (AD FS) infrastructure][adfs] in Azure.</span></span>

<!-- links -->

[adds-resource-forest]: adds-forest.md
[adfs]: adfs.md
[azure-cli-2]: /azure/install-azure-cli
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[implementing-a-secure-hybrid-network-architecture]: ../dmz/secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ../dmz/secure-vnet-dmz.md

[adds-data-disks]: https://msdn.microsoft.com/library/mt674703.aspx
[ad-ds-operations-masters]: https://technet.microsoft.com/library/cc779716(v=ws.10).aspx
[ad-ds-ports]: https://technet.microsoft.com/library/dd772723(v=ws.11).aspx
[availability-set]: /azure/virtual-machines/virtual-machines-windows-create-availability-set
[azure-expressroute]: /azure/expressroute/expressroute-introduction
[azure-vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[capacity-planning-for-adds]: https://social.technet.microsoft.com/wiki/contents/articles/14355.capacity-planning-for-active-directory-domain-services.aspx
[considerations]: ./considerations.md
[GitHub]: https://github.com/mspnp/identity-reference-architectures/tree/master/adds-extend-domain
[microsoft_systems_center]: https://www.microsoft.com/download/details.aspx?id=50013
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[security-considerations]: #security-considerations
[set-a-static-ip-address]: /azure/virtual-network/virtual-networks-static-private-ip-arm-pportal
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[visio-download]: https://archcenter.blob.core.windows.net/cdn/identity-architectures.vsdx
[vm-windows-sizes]: /azure/virtual-machines/virtual-machines-windows-sizes

[0]: ./images/adds-extend-domain.png "使用 Active Directory 保護混合式網路架構的安全"

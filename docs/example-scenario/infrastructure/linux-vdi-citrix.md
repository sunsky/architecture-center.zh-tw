---
title: 使用 Citrix 的 Linux 虛擬桌面
description: 在 Azure 上使用 Citrix 建置適用於 Linux 桌面的 VDI 環境。
author: miguelangelopereira
ms.date: 09/12/2018
ms.openlocfilehash: 374d59f7a528bd89870baa601a49a30ea00a08f1
ms.sourcegitcommit: b2a4eb132857afa70201e28d662f18458865a48e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48819137"
---
# <a name="linux-virtual-desktops-with-citrix"></a><span data-ttu-id="8845a-103">使用 Citrix 的 Linux 虛擬桌面</span><span class="sxs-lookup"><span data-stu-id="8845a-103">Linux Virtual Desktops with Citrix</span></span>

<span data-ttu-id="8845a-104">此範例案例也適用於任何需要 Linux 桌面適用虛擬桌面基礎結構 (VDI) 的產業。</span><span class="sxs-lookup"><span data-stu-id="8845a-104">This example scenario is applicable to any industry that needs a Virtual Desktop Infrastructure (VDI) for Linux Desktops.</span></span> <span data-ttu-id="8845a-105">VDI 是指在虛擬機器內部執行使用者桌面的程序，而該虛擬機器需存留在資料中心內的伺服器上。</span><span class="sxs-lookup"><span data-stu-id="8845a-105">VDI refers to the process of running a user desktop inside a virtual machine that lives on a server in the datacenter.</span></span> <span data-ttu-id="8845a-106">此案例中的客戶選擇使用以 Citrix 為基礎的解決方案來滿足其 VDI 需求。</span><span class="sxs-lookup"><span data-stu-id="8845a-106">The customer in this scenario chose to use a Citrix-based solution for their VDI needs.</span></span>

<span data-ttu-id="8845a-107">組織通常有一些異質環境，其員工會在其中使用多個裝置和作業系統。</span><span class="sxs-lookup"><span data-stu-id="8845a-107">Organizations often have heterogeneous environments with multiple devices and operating systems being used by employees.</span></span> <span data-ttu-id="8845a-108">對應用程式提供一致的存取，同時維護安全的環境，很有挑戰。</span><span class="sxs-lookup"><span data-stu-id="8845a-108">It can be challenging to provide consistent access to applications while maintaining a secure environment.</span></span> <span data-ttu-id="8845a-109">不管使用者使用哪個裝置或作業系統，Linux 桌面適用的 VDI 解決方案都可讓貴組織提供存取權。</span><span class="sxs-lookup"><span data-stu-id="8845a-109">A VDI solution for Linux desktops will allow your organization to provide access irrespective of the device or OS used by the end user.</span></span>

<span data-ttu-id="8845a-110">此範例方案的一些優點包括：</span><span class="sxs-lookup"><span data-stu-id="8845a-110">Some benefits for this sample solution include the following:</span></span>
* <span data-ttu-id="8845a-111">讓更多使用者存取相同的基礎結構，共用 Linux 虛擬桌面的投資報酬率就比較高。</span><span class="sxs-lookup"><span data-stu-id="8845a-111">Return on investment will be higher with shared Linux virtual desktops by giving more users access to the same infrastructure.</span></span> <span data-ttu-id="8845a-112">在集中式 VDI 環境中合併資源，終端使用者裝置就不需要非常強大。</span><span class="sxs-lookup"><span data-stu-id="8845a-112">By consolidating resources on a centralized VDI environment, the end user devices don't need to be as powerful.</span></span>
* <span data-ttu-id="8845a-113">不論使用者裝置為何，效能都會一致。</span><span class="sxs-lookup"><span data-stu-id="8845a-113">Performance will be consistent regardless of the end user device.</span></span>
* <span data-ttu-id="8845a-114">使用者可以從任何裝置 (包括非 Linux 裝置) 存取 Linux 應用程式。</span><span class="sxs-lookup"><span data-stu-id="8845a-114">Users can access Linux applications from any device (including non-Linux devices).</span></span>
* <span data-ttu-id="8845a-115">所有分散各地的員工都可以在 Azure 資料中心保護敏感性資料。</span><span class="sxs-lookup"><span data-stu-id="8845a-115">Sensitive data can be secured in the Azure data center for all distributed employees.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="8845a-116">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="8845a-116">Relevant use cases</span></span>

<span data-ttu-id="8845a-117">請針對下列使用案例考慮此案例：</span><span class="sxs-lookup"><span data-stu-id="8845a-117">Consider this scenario for the following use case:</span></span>

* <span data-ttu-id="8845a-118">提供從 Linux 或非 Linux 裝置對任務關鍵性、特殊化 Linux VDI 桌面的安全存取</span><span class="sxs-lookup"><span data-stu-id="8845a-118">Providing secure access to mission-critical, specialized Linux VDI desktops from Linux or non-Linux devices</span></span>

## <a name="architecture"></a><span data-ttu-id="8845a-119">架構</span><span class="sxs-lookup"><span data-stu-id="8845a-119">Architecture</span></span>

<span data-ttu-id="8845a-120">[![](./media/azure-citrix-sample-diagram.png "架構圖")](./media/azure-citrix-sample-diagram.png#lightbox)</span><span class="sxs-lookup"><span data-stu-id="8845a-120">[![](./media/azure-citrix-sample-diagram.png "Architecture Diagram")](./media/azure-citrix-sample-diagram.png#lightbox)</span></span>

<span data-ttu-id="8845a-121">此範例案例示範如何允許公司網路存取 Linux 虛擬桌面：</span><span class="sxs-lookup"><span data-stu-id="8845a-121">This example scenario demonstrates allowing the corporate network to access the Linux Virtual Desktops:</span></span>

* <span data-ttu-id="8845a-122">ExpressRoute 建立於內部部署環境與 Azure 之間，可供快速且可靠連線至雲端。</span><span class="sxs-lookup"><span data-stu-id="8845a-122">An ExpressRoute is established between the on-premises environment and Azure, for fast and reliable connectivity to the cloud.</span></span>
* <span data-ttu-id="8845a-123">針對 VDI 部署的 Citrix XenDeskop 解決方案。</span><span class="sxs-lookup"><span data-stu-id="8845a-123">Citrix XenDeskop solution deployed for VDI.</span></span>
* <span data-ttu-id="8845a-124">在 Ubuntu (或另一個支援的散發版本) 上執行的 CitrixVDA。</span><span class="sxs-lookup"><span data-stu-id="8845a-124">The CitrixVDA run on Ubuntu (or another supported distro).</span></span>
* <span data-ttu-id="8845a-125">Azure 網路安全性群組會套用正確的網路 ACL。</span><span class="sxs-lookup"><span data-stu-id="8845a-125">Azure Network Security Groups will apply the correct network ACLs.</span></span>
* <span data-ttu-id="8845a-126">Citrix ADC (NetScaler) 會發行所有的 Citrix 服務並使其達到負載平衡。</span><span class="sxs-lookup"><span data-stu-id="8845a-126">Citrix ADC (NetScaler) will publish and load balance all the Citrix services.</span></span>
* <span data-ttu-id="8845a-127">Active Directory Domain Services 將用於使 Citrix 伺服器網域加入。</span><span class="sxs-lookup"><span data-stu-id="8845a-127">Active Directory Domain Services will be used to domain join the Citrix servers.</span></span> <span data-ttu-id="8845a-128">VDA 伺服器不會加入網域。</span><span class="sxs-lookup"><span data-stu-id="8845a-128">VDA servers will not be domain joined.</span></span>
* <span data-ttu-id="8845a-129">Azure 混合式檔案同步會在解決方案內啟用共用儲存體。</span><span class="sxs-lookup"><span data-stu-id="8845a-129">Azure Hybrid File Sync will enable shared storage across the solution.</span></span> <span data-ttu-id="8845a-130">例如，它可以使用於遠端/首頁解決方案。</span><span class="sxs-lookup"><span data-stu-id="8845a-130">For example, it can be used in remote/home solutions.</span></span>

<span data-ttu-id="8845a-131">在此案例中，使用下列 SKU：</span><span class="sxs-lookup"><span data-stu-id="8845a-131">For this scenario, the following SKUs are used:</span></span>

- <span data-ttu-id="8845a-132">Citrix ADC (NetScaler)：2 x D4sv3 與 [NetScaler 12.0 VPX Standard Edition 200 MBPS PAYG 映像](https://azuremarketplace.microsoft.com/pt-br/marketplace/apps/citrix.netscalervpx-120?tab=PlansAndPrice)</span><span class="sxs-lookup"><span data-stu-id="8845a-132">Citrix ADC (NetScaler): 2 x D4sv3 with [NetScaler 12.0 VPX Standard Edition 200 MBPS PAYG image](https://azuremarketplace.microsoft.com/pt-br/marketplace/apps/citrix.netscalervpx-120?tab=PlansAndPrice)</span></span>
- <span data-ttu-id="8845a-133">Citrix License Server：1 x D2s v3</span><span class="sxs-lookup"><span data-stu-id="8845a-133">Citrix License Server: 1 x D2s v3</span></span>
- <span data-ttu-id="8845a-134">Citrix VDA：4 x D8s v3</span><span class="sxs-lookup"><span data-stu-id="8845a-134">Citrix VDA: 4 x D8s v3</span></span>
- <span data-ttu-id="8845a-135">Citrix Storefront：2 x D2s v3</span><span class="sxs-lookup"><span data-stu-id="8845a-135">Citrix Storefront: 2 x D2s v3</span></span>
- <span data-ttu-id="8845a-136">Citrix Delivery Controller：2 x D2s v3</span><span class="sxs-lookup"><span data-stu-id="8845a-136">Citrix Delivery Controller: 2 x D2s v3</span></span>
- <span data-ttu-id="8845a-137">網域控制站：2 x D2sv3</span><span class="sxs-lookup"><span data-stu-id="8845a-137">Domain Controllers: 2 x D2sv3</span></span>
- <span data-ttu-id="8845a-138">Azure 檔案伺服器：2 x D2sv3</span><span class="sxs-lookup"><span data-stu-id="8845a-138">Azure File Servers: 2 x D2sv3</span></span>

> [!NOTE]
> <span data-ttu-id="8845a-139">所有授權 (NetScaler 除外) 都自備授權 (BYOL)</span><span class="sxs-lookup"><span data-stu-id="8845a-139">All the licenses (other than NetScaler) are bring-your-own-license (BYOL)</span></span>

### <a name="components"></a><span data-ttu-id="8845a-140">元件</span><span class="sxs-lookup"><span data-stu-id="8845a-140">Components</span></span>

- <span data-ttu-id="8845a-141">[Azure 虛擬網路](/azure/virtual-network/virtual-networks-overview)可讓 VM 等資源安全地互相通訊，以及與網際網路和內部部署網路通訊。</span><span class="sxs-lookup"><span data-stu-id="8845a-141">[Azure Virtual Network](/azure/virtual-network/virtual-networks-overview) allows resources such as VMs to securely communicate with each other, the internet, and on-premises networks.</span></span> <span data-ttu-id="8845a-142">虛擬網路會提供隔離與分割、篩選與路由流量，並允許位置之間的連線。</span><span class="sxs-lookup"><span data-stu-id="8845a-142">Virtual networks provide isolation and segmentation, filter and route traffic, and allow connection between locations.</span></span> <span data-ttu-id="8845a-143">一個虛擬網路將用於此案例中的所有資源。</span><span class="sxs-lookup"><span data-stu-id="8845a-143">One virtual network will be used for all resources in this scenario.</span></span>
- <span data-ttu-id="8845a-144">[Azure 網路安全性群組 (NSG)](/azure/virtual-network/security-overview) 包含一些安全性規則，可根據來源或目的地 IP 位址、連接埠和通訊協定允許或拒絕輸入或輸出網路流量。</span><span class="sxs-lookup"><span data-stu-id="8845a-144">[Azure network security groups](/azure/virtual-network/security-overview) contain a list of security rules that allow or deny inbound or outbound network traffic based on source or destination IP address, port, and protocol.</span></span> <span data-ttu-id="8845a-145">此案例中的虛擬網路會受到網路安全性群組規則保護，這些規則會限制應用程式元件之間的流量。</span><span class="sxs-lookup"><span data-stu-id="8845a-145">The virtual networks in this scenario are secured with network security group rules that restrict the flow of traffic between the application components.</span></span>
- <span data-ttu-id="8845a-146">[Azure 負載平衡器](/azure/application-gateway/overview)會根據規則和健康情況探查來散發輸入流量。</span><span class="sxs-lookup"><span data-stu-id="8845a-146">[Azure load balancer](/azure/application-gateway/overview) distributes inbound traffic according to rules and health probes.</span></span> <span data-ttu-id="8845a-147">對於所有 TCP 和 UDP 應用程式，負載平衡器可提供低延遲和高輸送量，且最多可相應增加為數百萬個流程。</span><span class="sxs-lookup"><span data-stu-id="8845a-147">A load balancer provides low latency and high throughput, and scales up to millions of flows for all TCP and UDP applications.</span></span> <span data-ttu-id="8845a-148">此案例會使用內部負載平衡器，將流量散發於 Citrix NetScaler。</span><span class="sxs-lookup"><span data-stu-id="8845a-148">An internal load balancer is used in this scenario to distribute traffic on the Citrix NetScaler.</span></span>
- <span data-ttu-id="8845a-149">[Azure 混合式檔案同步](https://github.com/MicrosoftDocs/azure-docs/edit/master/articles/storage/files/storage-sync-files-planning.md)會使用於所有共用儲存體。</span><span class="sxs-lookup"><span data-stu-id="8845a-149">[Azure Hybrid File Sync](https://github.com/MicrosoftDocs/azure-docs/edit/master/articles/storage/files/storage-sync-files-planning.md) will be used for all shared storage.</span></span> <span data-ttu-id="8845a-150">使用混合式檔案同步，此儲存體會複寫到兩部檔案伺服器。</span><span class="sxs-lookup"><span data-stu-id="8845a-150">The storage will replicate to two file servers using Hybrid File Sync.</span></span>
- <span data-ttu-id="8845a-151">[Azure SQL Database](/azure/sql-database/sql-database-technical-overview) 是關聯式資料庫即服務 (DBaaS)，它是根據最新穩定版本的 Microsoft SQL Server 資料庫引擎而建置。</span><span class="sxs-lookup"><span data-stu-id="8845a-151">[Azure SQL Database](/azure/sql-database/sql-database-technical-overview) is a relational database-as-a-service (DBaaS) based on the latest stable version of Microsoft SQL Server Database Engine.</span></span> <span data-ttu-id="8845a-152">它將用於裝載 Citrix 資料庫。</span><span class="sxs-lookup"><span data-stu-id="8845a-152">It will be used for hosting Citrix databases.</span></span>
- <span data-ttu-id="8845a-153">[ExpressRoute](/azure/expressroute/expressroute-introduction) 可讓您透過連線提供者所提供的私人連線，將內部部署網路延伸至 Microsoft 雲端。</span><span class="sxs-lookup"><span data-stu-id="8845a-153">[ExpressRoute](/azure/expressroute/expressroute-introduction) lets you extend your on-premises networks into the Microsoft cloud over a private connection facilitated by a connectivity provider.</span></span> 
- <span data-ttu-id="8845a-154">[Active Directory Domain Services 使用於目錄服務和使用者驗證</span><span class="sxs-lookup"><span data-stu-id="8845a-154">[Active Directory Domain Services is used for Directory Services and user authentication</span></span>
- <span data-ttu-id="8845a-155">[Azure 可用性設定組](/azure/virtual-machines/windows/tutorial-availability-sets)可確保您在 Azure 上部署的 VM 會分散到叢集中多個各自獨立的硬體節點。</span><span class="sxs-lookup"><span data-stu-id="8845a-155">[Azure Availabilty Sets](/azure/virtual-machines/windows/tutorial-availability-sets) will ensure that the VMs you deploy on Azure are distributed across multiple isolated hardware nodes in a cluster.</span></span> <span data-ttu-id="8845a-156">這麼做可以確保當 Azure 發生硬體或軟體故障時，受到影響的只會是一部分的 VM 子集，您整體的解決方案則會維持可用且正常運作。</span><span class="sxs-lookup"><span data-stu-id="8845a-156">Doing this ensures that if a hardware or software failure within Azure happens, only a subset of your VMs are impacted and that your overall solution remains available and operational.</span></span> 
- <span data-ttu-id="8845a-157">[Citrix ADC (NetScaler)](https://www.citrix.com/products/citrix-adc) 是一個應用程式傳遞控制站，可執行特定應用程式流量分析，進而以智慧方式散發、最佳化及保護 Web 應用程式的第 4 層 7 (L4–L7) 網路流量。</span><span class="sxs-lookup"><span data-stu-id="8845a-157">[Citrix ADC (NetScaler)](https://www.citrix.com/products/citrix-adc) is an application delivery controller that performs application-specific traffic analysis to intelligently distribute, optimize, and secure Layer 4-Layer 7 (L4–L7) network traffic for web applications.</span></span> 
- <span data-ttu-id="8845a-158">[Citrix Storefront](https://www.citrix.com/products/citrix-virtual-apps-and-desktops/citrix-storefront.html) 是一個企業應用程式存放區，可改善安全性並簡化部署，以及在任何平台上透過 Citrix Receiver 提供現代化、極為近乎原生的使用者體驗。</span><span class="sxs-lookup"><span data-stu-id="8845a-158">[Citrix Storefront](https://www.citrix.com/products/citrix-virtual-apps-and-desktops/citrix-storefront.html) is an enterprise app store that improves security and simplifies deployments, delivering a modern, unmatched near-native user experience across Citrix Receiver on any platform.</span></span> <span data-ttu-id="8845a-159">StoreFront 可讓您輕鬆地管理多網站和多版本的 Citrix 虛擬應用程式和桌面環境。</span><span class="sxs-lookup"><span data-stu-id="8845a-159">StoreFront makes it easy to manage multi-site and multi-version Citrix Virtual Apps and Desktops environments.</span></span> 
- <span data-ttu-id="8845a-160">[Citrix License Server](https://www.citrix.com/buy/licensing/overview.html) 會管理 Citrix 產品的授權。</span><span class="sxs-lookup"><span data-stu-id="8845a-160">[Citrix License Server](https://www.citrix.com/buy/licensing/overview.html) will manage the licenses for Citrix products.</span></span>
- <span data-ttu-id="8845a-161">[Citrix XenDesktops VDA](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service) 能夠連線應用程式和桌面。</span><span class="sxs-lookup"><span data-stu-id="8845a-161">[Citrix XenDesktops VDA](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service) enables connections to applications and desktops.</span></span> <span data-ttu-id="8845a-162">VDA 會針對使用者安裝在執行應用程式或虛擬桌面的電腦上。</span><span class="sxs-lookup"><span data-stu-id="8845a-162">The VDA is installed on the machine that runs the applications or virtual desktops for the user.</span></span> <span data-ttu-id="8845a-163">它可讓電腦向 Delivery Controller 註冊，以及管理使用者裝置的高畫質體驗 (HDX) 連線。</span><span class="sxs-lookup"><span data-stu-id="8845a-163">It enables the machines to register with Delivery Controllers and manage the High Definition eXperience (HDX) connection to a user device.</span></span>
- <span data-ttu-id="8845a-164">[Citrix Delivery Controller](https://docs.citrix.com/en-us/xenapp-and-xendesktop/7-15-ltsr/manage-deployment/delivery-controllers) 是一個伺服器端元件，負責管理使用者存取權，以及代理和最佳化連線。</span><span class="sxs-lookup"><span data-stu-id="8845a-164">[Citrix Delivery Controller](https://docs.citrix.com/en-us/xenapp-and-xendesktop/7-15-ltsr/manage-deployment/delivery-controllers) is the server-side component responsible for managing user access, plus brokering and optimizing connections.</span></span> <span data-ttu-id="8845a-165">Controller 也會提供可建立桌面和伺服器映像的 Machine Creation Services。</span><span class="sxs-lookup"><span data-stu-id="8845a-165">Controllers also provide the Machine Creation Services that create desktop and server images.</span></span>

### <a name="alternatives"></a><span data-ttu-id="8845a-166">替代項目</span><span class="sxs-lookup"><span data-stu-id="8845a-166">Alternatives</span></span>

- <span data-ttu-id="8845a-167">有多個夥伴在 Azure 中支援 VDI 解決方案，例如 VMware、Workspot 等等。</span><span class="sxs-lookup"><span data-stu-id="8845a-167">There are multiple partners with VDI solutions that supported in Azure such as VMware, Workspot, and others.</span></span> <span data-ttu-id="8845a-168">此特定範例架構是以使用 Citrix 的已部署專案為基礎。</span><span class="sxs-lookup"><span data-stu-id="8845a-168">This specific sample architecture is based on a deployed project that used Citrix.</span></span>
- <span data-ttu-id="8845a-169">Citrix 提供可擷取此架構一部分的雲端服務。</span><span class="sxs-lookup"><span data-stu-id="8845a-169">Citrix provides a cloud service that abstracts part of this architecture.</span></span> <span data-ttu-id="8845a-170">這可能是此解決方案的替代項目。</span><span class="sxs-lookup"><span data-stu-id="8845a-170">It could be an alternative for this solution.</span></span> <span data-ttu-id="8845a-171">如需詳細資訊，請參閱 [Citrix Cloud](https://www.citrix.com/products/citrix-cloud)。</span><span class="sxs-lookup"><span data-stu-id="8845a-171">For more information, see [Citrix Cloud](https://www.citrix.com/products/citrix-cloud).</span></span>

## <a name="considerations"></a><span data-ttu-id="8845a-172">考量</span><span class="sxs-lookup"><span data-stu-id="8845a-172">Considerations</span></span>

- <span data-ttu-id="8845a-173">檢查 [Citrix Linux 要求](https://docs.citrix.com/en-us/linux-virtual-delivery-agent/current-release/system-requirements)。</span><span class="sxs-lookup"><span data-stu-id="8845a-173">Check the [Citrix Linux Requirements](https://docs.citrix.com/en-us/linux-virtual-delivery-agent/current-release/system-requirements).</span></span>
- <span data-ttu-id="8845a-174">延遲會對整體解決方案造成影響。</span><span class="sxs-lookup"><span data-stu-id="8845a-174">Latency can have impact on the overall solution.</span></span> <span data-ttu-id="8845a-175">在生產環境中，據此進行測試。</span><span class="sxs-lookup"><span data-stu-id="8845a-175">For a production environment, test accordingly.</span></span>
- <span data-ttu-id="8845a-176">視案例而定，解決方案可能需要具有 VDA 適用 GPU 的 VM。</span><span class="sxs-lookup"><span data-stu-id="8845a-176">Depending on the scenario, the solution may need VMs with GPUs for VDA.</span></span> <span data-ttu-id="8845a-177">在此解決方案中，假設 GPU 不是必要項目。</span><span class="sxs-lookup"><span data-stu-id="8845a-177">For this solution, it is assumed that GPU is not a requirement.</span></span>

### <a name="availability-scalability-and-security"></a><span data-ttu-id="8845a-178">可用性、延展性與安全性</span><span class="sxs-lookup"><span data-stu-id="8845a-178">Availability, Scalability, and Security</span></span>

- <span data-ttu-id="8845a-179">此範例解決方案專為所有角色 (授權伺服器除外) 的高可用性而設計。</span><span class="sxs-lookup"><span data-stu-id="8845a-179">This sample solution is designed for high availability for all roles other than the licensing server.</span></span> <span data-ttu-id="8845a-180">如果授權伺服器離線，此環境在 30 天的寬限期內會持續運作，因此該伺服器上不需要任何額外的備援。</span><span class="sxs-lookup"><span data-stu-id="8845a-180">Because the environment continues to function during a 30-day grace period if the license server is offline, no additional redundancy is required on that server.</span></span>
- <span data-ttu-id="8845a-181">所有提供類似角色的伺服器都應該部署在[可用性設定組](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)中。</span><span class="sxs-lookup"><span data-stu-id="8845a-181">All servers providing similar roles should be deployed in [Availability Sets](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).</span></span>
- <span data-ttu-id="8845a-182">此範例解決方案不包含災害復原功能。</span><span class="sxs-lookup"><span data-stu-id="8845a-182">This sample solution does not include Disaster Recovery capabilities.</span></span> <span data-ttu-id="8845a-183">[Azure Site Recovery](/azure/site-recovery/site-recovery-overview) 可能是這項設計的良好附加元件。</span><span class="sxs-lookup"><span data-stu-id="8845a-183">[Azure Site Recovery](/azure/site-recovery/site-recovery-overview) could be a good add-on to this design.</span></span>
- <span data-ttu-id="8845a-184">在生產部署中，應該實作[備份](/azure/backup/backup-introduction-to-azure-backup)、[監視](/azure/monitoring-and-diagnostics/monitoring-overview)和[更新管理](/azure/automation/automation-update-management)等管理解決方案。</span><span class="sxs-lookup"><span data-stu-id="8845a-184">For a production deployment management solution should be implemented such as [backup](/azure/backup/backup-introduction-to-azure-backup), [monitoring](/azure/monitoring-and-diagnostics/monitoring-overview) and [update management](/azure/automation/automation-update-management).</span></span>
- <span data-ttu-id="8845a-185">此範例解決方案應適用於大約 250 個並行 (每部 VDA 伺服器大約 50-60 個) 使用者 (混合使用方式)。</span><span class="sxs-lookup"><span data-stu-id="8845a-185">This sample solution should work for about 250 concurrent (about 50-60 per VDA server) users with a mixed usage.</span></span> <span data-ttu-id="8845a-186">不過，這主要取決於所使用的應用程式類型。</span><span class="sxs-lookup"><span data-stu-id="8845a-186">But that will greatly depended on the type of applications being used.</span></span> <span data-ttu-id="8845a-187">針對生產用途，應該執行嚴格的負載測試。</span><span class="sxs-lookup"><span data-stu-id="8845a-187">For production use, rigorous load testing should be performed.</span></span>

## <a name="deploy-this-scenario"></a><span data-ttu-id="8845a-188">部署此案例</span><span class="sxs-lookup"><span data-stu-id="8845a-188">Deploy this scenario</span></span>

<span data-ttu-id="8845a-189">如需部署資訊，請參閱官方 [ 文件](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/install-configure.html) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="8845a-189">For deployment information see the official [Citrix documentation](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/install-configure.html).</span></span>

## <a name="pricing"></a><span data-ttu-id="8845a-190">價格</span><span class="sxs-lookup"><span data-stu-id="8845a-190">Pricing</span></span>

- <span data-ttu-id="8845a-191">Citrix XenDestop 授權不會包含在 Azure 服務費用中。</span><span class="sxs-lookup"><span data-stu-id="8845a-191">The Citrix XenDestop licenses are not included in Azure service charges.</span></span>
- <span data-ttu-id="8845a-192">Citrix NetScaler 授權包含在預付型方案中。</span><span class="sxs-lookup"><span data-stu-id="8845a-192">The Citrix NetScaler license is included in a pay-as-you-go model.</span></span>
- <span data-ttu-id="8845a-193">使用保留的執行個體，可大幅降低解決方案的計算成本。</span><span class="sxs-lookup"><span data-stu-id="8845a-193">Using reserved instances will greatly reduce the compute cost for the solution.</span></span>
- <span data-ttu-id="8845a-194">不包含 ExpressRoute 費用。</span><span class="sxs-lookup"><span data-stu-id="8845a-194">The ExpressRoute cost is not included.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8845a-195">後續步驟</span><span class="sxs-lookup"><span data-stu-id="8845a-195">Next Steps</span></span>

- <span data-ttu-id="8845a-196">請參閱[這裡](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/install-configure)的 Citrix 文件進行規劃和部署。</span><span class="sxs-lookup"><span data-stu-id="8845a-196">Check Citrix documentation for planning and deployment [here](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/install-configure).</span></span>
- <span data-ttu-id="8845a-197">若要在 Azure 中部署 Citrix ADC (NetScaler)，請檢閱 Citrix 在[這裡](https://github.com/citrix/netscaler-azure-templates)提供的 Resource Manager 範本。</span><span class="sxs-lookup"><span data-stu-id="8845a-197">To deploy Citrix ADC (NetScaler) in Azure, review the Resource Manager templates provided by Citrix [here](https://github.com/citrix/netscaler-azure-templates).</span></span>
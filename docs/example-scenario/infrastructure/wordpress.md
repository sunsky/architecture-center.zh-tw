---
title: 高擴充性且安全的 WordPress 網站
titleSuffix: Azure Example Scenarios
description: 針對媒體事件建置高度可調整且安全的 WordPress 網站。
author: david-stanford
ms.date: 09/18/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
social_image_url: /azure/architecture/example-scenario/infrastructure/media/secure-scalable-wordpress.png
ms.openlocfilehash: 4f347f91d5958fb83404856ec5d36d70a7ed0d19
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640085"
---
# <a name="highly-scalable-and-secure-wordpress-website"></a><span data-ttu-id="2f79d-103">高擴充性且安全的 WordPress 網站</span><span class="sxs-lookup"><span data-stu-id="2f79d-103">Highly scalable and secure WordPress website</span></span>

<span data-ttu-id="2f79d-104">此範例案例適用於需要高擴充性且安全的 WordPress 安裝的公司。</span><span class="sxs-lookup"><span data-stu-id="2f79d-104">This example scenario is applicable to companies that need a highly scalable and secure installation of WordPress.</span></span> <span data-ttu-id="2f79d-105">此案例是以下列部署為基礎：使用於大型會議，而且擴充成功以符合工作階段送至網站的尖峰流量。</span><span class="sxs-lookup"><span data-stu-id="2f79d-105">This scenario is based on a deployment that was used for a large convention and was successfully able to scale to meet the spike traffic that sessions drove to the site.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="2f79d-106">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="2f79d-106">Relevant use cases</span></span>

<span data-ttu-id="2f79d-107">其他相關的使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="2f79d-107">Other relevant use cases include:</span></span>

- <span data-ttu-id="2f79d-108">導致流量激增的媒體事件。</span><span class="sxs-lookup"><span data-stu-id="2f79d-108">Media events that cause traffic surges.</span></span>
- <span data-ttu-id="2f79d-109">使用 WordPress 作為其內容管理系統的部落格。</span><span class="sxs-lookup"><span data-stu-id="2f79d-109">Blogs that use WordPress as their content management system.</span></span>
- <span data-ttu-id="2f79d-110">使用 WordPress 的商務或電子商務網站。</span><span class="sxs-lookup"><span data-stu-id="2f79d-110">Business or e-commerce websites that use WordPress.</span></span>
- <span data-ttu-id="2f79d-111">使用其他內容管理系統建置的網站。</span><span class="sxs-lookup"><span data-stu-id="2f79d-111">Web sites built using other content management systems.</span></span>

## <a name="architecture"></a><span data-ttu-id="2f79d-112">架構</span><span class="sxs-lookup"><span data-stu-id="2f79d-112">Architecture</span></span>

<span data-ttu-id="2f79d-113">[![可擴充且安全的 WordPress 部署相關 Azure 元件的架構概觀](media/secure-scalable-wordpress.png)](media/secure-scalable-wordpress.png#lightbox)</span><span class="sxs-lookup"><span data-stu-id="2f79d-113">[![Architecture overview of the Azure components involved in a scalable and secure WordPress deployment](media/secure-scalable-wordpress.png)](media/secure-scalable-wordpress.png#lightbox)</span></span>

<span data-ttu-id="2f79d-114">此案例涵蓋使用 Ubuntu 網頁伺服器和 MariaDB 的 WordPress 安裝，這是可擴充且安全的安裝。</span><span class="sxs-lookup"><span data-stu-id="2f79d-114">This scenario covers a scalable and secure installation of WordPress that uses Ubuntu web servers and MariaDB.</span></span> <span data-ttu-id="2f79d-115">此案例中有兩個不同的資料流，第一個是使用者存取網站：</span><span class="sxs-lookup"><span data-stu-id="2f79d-115">There are two distinct data flows in this scenario the first is users access the website:</span></span>

1. <span data-ttu-id="2f79d-116">使用者會透過 CDN 存取前端網站。</span><span class="sxs-lookup"><span data-stu-id="2f79d-116">Users access the front-end website through a CDN.</span></span>
2. <span data-ttu-id="2f79d-117">CDN 會使用 Azure 負載平衡器作為原點，並提取任何不是從這裡快取的資料。</span><span class="sxs-lookup"><span data-stu-id="2f79d-117">The CDN uses an Azure load balancer as the origin, and pulls any data that isn't cached from there.</span></span>
3. <span data-ttu-id="2f79d-118">Azure 負載平衡器會將要求散發到網頁伺服器的虛擬機器擴展集。</span><span class="sxs-lookup"><span data-stu-id="2f79d-118">The Azure load balancer distributes requests to the virtual machine scale sets of web servers.</span></span>
4. <span data-ttu-id="2f79d-119">WordPress 應用程式會從 Maria DB 叢集中提取任何動態資訊，而所有靜態內容裝載在 Azure 檔案服務中。</span><span class="sxs-lookup"><span data-stu-id="2f79d-119">The WordPress application pulls any dynamic information out of the Maria DB clusters, all static content is hosted in Azure Files.</span></span>
5. <span data-ttu-id="2f79d-120">SSL 金鑰會儲存在 Azure Key Vault 中。</span><span class="sxs-lookup"><span data-stu-id="2f79d-120">SSL keys are stored Azure Key Vault.</span></span>

<span data-ttu-id="2f79d-121">第二個工作流程是作者如何參與新的內容：</span><span class="sxs-lookup"><span data-stu-id="2f79d-121">The second workflow is how authors contribute new content:</span></span>

1. <span data-ttu-id="2f79d-122">作者可安全地連線到公用 VPN 閘道。</span><span class="sxs-lookup"><span data-stu-id="2f79d-122">Authors connect securely to the public VPN gateway.</span></span>
2. <span data-ttu-id="2f79d-123">VPN 驗證資訊會儲存在 Azure Active Directory 中。</span><span class="sxs-lookup"><span data-stu-id="2f79d-123">VPN authentication information is stored in Azure Active Directory.</span></span>
3. <span data-ttu-id="2f79d-124">然後建立系統管理 Jumpbox 的連線。</span><span class="sxs-lookup"><span data-stu-id="2f79d-124">A connection is then established to the Admin jump boxes.</span></span>
4. <span data-ttu-id="2f79d-125">從系統管理 Jumpbox，作者就能夠連線到撰寫叢集的 Azure 負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="2f79d-125">From the admin jump box, the author is then able to connect to the Azure load balancer for the authoring cluster.</span></span>
5. <span data-ttu-id="2f79d-126">Azure 負載平衡器會將流量散發到網頁伺服器的虛擬機器擴展集，而這些擴展集具有 Maria DB 叢集的寫入存取權。</span><span class="sxs-lookup"><span data-stu-id="2f79d-126">The Azure load balancer distributes traffic to the virtual machine scale sets of web servers that have write access to the Maria DB cluster.</span></span>
6. <span data-ttu-id="2f79d-127">新的靜態內容回上傳至 Azure 檔案，並動態內容會寫入到 Maria DB 叢集中。</span><span class="sxs-lookup"><span data-stu-id="2f79d-127">New static content is uploaded to Azure files and dynamic content is written into the Maria DB cluster.</span></span>
7. <span data-ttu-id="2f79d-128">然後，這些變更會透過 rsync 或主從式複寫來複寫到替代區域。</span><span class="sxs-lookup"><span data-stu-id="2f79d-128">These changes are then replicated to the alternate region via rsync or master/slave replication.</span></span>

### <a name="components"></a><span data-ttu-id="2f79d-129">元件</span><span class="sxs-lookup"><span data-stu-id="2f79d-129">Components</span></span>

- <span data-ttu-id="2f79d-130">[Azure 內容傳遞網路 (CDN)](/azure/cdn/cdn-overview) 是可有效率地將 Web 內容傳遞給使用者的分散式伺服器網路。</span><span class="sxs-lookup"><span data-stu-id="2f79d-130">[Azure Content Delivery Network (CDN)](/azure/cdn/cdn-overview) is a distributed network of servers that efficiently delivers web content to users.</span></span> <span data-ttu-id="2f79d-131">CDN 會將快取的內容儲存在使用者附近存在點 (POP) 位置的邊緣伺服器上，以將延遲降至最低。</span><span class="sxs-lookup"><span data-stu-id="2f79d-131">CDNs minimize latency by storing cached content on edge servers in point-of-presence locations near to end users.</span></span>
- <span data-ttu-id="2f79d-132">[虛擬網路](/azure/virtual-network/virtual-networks-overview)可讓 VM 等資源安全地互相通訊，以及與網際網路和內部部署網路通訊。</span><span class="sxs-lookup"><span data-stu-id="2f79d-132">[Virtual networks](/azure/virtual-network/virtual-networks-overview) allow resources such as VMs to securely communicate with each other, the Internet, and on-premises networks.</span></span> <span data-ttu-id="2f79d-133">虛擬網路會提供隔離與分割、篩選與路由流量，並允許位置之間的連線。</span><span class="sxs-lookup"><span data-stu-id="2f79d-133">Virtual networks provide isolation and segmentation, filter and route traffic, and allow connection between locations.</span></span> <span data-ttu-id="2f79d-134">這兩個網路是透過 VNet 對等互連進行連線。</span><span class="sxs-lookup"><span data-stu-id="2f79d-134">The two networks are connected via Vnet peering.</span></span>
- <span data-ttu-id="2f79d-135">[網路安全性群組 (NSG)](/azure/virtual-network/security-overview) 包含一些安全性規則，可根據來源或目的地 IP 位址、連接埠和通訊協定允許或拒絕輸入或輸出網路流量。</span><span class="sxs-lookup"><span data-stu-id="2f79d-135">[Network security groups](/azure/virtual-network/security-overview) contain a list of security rules that allow or deny inbound or outbound network traffic based on source or destination IP address, port, and protocol.</span></span> <span data-ttu-id="2f79d-136">此案例中的虛擬網路會受到網路安全性群組規則保護，這些規則會限制應用程式元件之間的流量。</span><span class="sxs-lookup"><span data-stu-id="2f79d-136">The virtual networks in this scenario are secured with network security group rules that restrict the flow of traffic between the application components.</span></span>
- <span data-ttu-id="2f79d-137">[負載平衡器](/azure/load-balancer/load-balancer-overview)會根據規則和健康情況探查來散發輸入流量。</span><span class="sxs-lookup"><span data-stu-id="2f79d-137">[Load balancers](/azure/load-balancer/load-balancer-overview) distribute inbound traffic according to rules and health probes.</span></span> <span data-ttu-id="2f79d-138">對於所有 TCP 和 UDP 應用程式，負載平衡器可提供低延遲和高輸送量，且最多可相應增加為數百萬個流程。</span><span class="sxs-lookup"><span data-stu-id="2f79d-138">A load balancer provides low latency and high throughput, and scales up to millions of flows for all TCP and UDP applications.</span></span> <span data-ttu-id="2f79d-139">此案例會使用負載平衡器，將流量從內容傳遞網路散發到前端網頁伺服器。</span><span class="sxs-lookup"><span data-stu-id="2f79d-139">A load balancer is used in this scenario to distribute traffic from the content deliver network to the front-end web servers.</span></span>
- <span data-ttu-id="2f79d-140">[虛擬機器擴展集][docs-vmss]可讓您建立和管理一組負載平衡的相同 VM。</span><span class="sxs-lookup"><span data-stu-id="2f79d-140">[Virtual machine scale sets][docs-vmss] let you create and manage a group of identical load-balanced VMs.</span></span> <span data-ttu-id="2f79d-141">VM 執行個體的數目可以自動增加或減少，以因應需求或已定義的排程。</span><span class="sxs-lookup"><span data-stu-id="2f79d-141">The number of VM instances can automatically increase or decrease in response to demand or a defined schedule.</span></span> <span data-ttu-id="2f79d-142">此案例中使用兩個不同的虛擬機器擴展集 - 一個適用於提供內容的前端網頁伺服器，另一個則適用於用來撰寫新內容的前端網頁伺服器。</span><span class="sxs-lookup"><span data-stu-id="2f79d-142">Two separate virtual machine scale sets are used in this scenario - one for the front-end web-servers serving content, and one for the front-end webservers used to author new content.</span></span>
- <span data-ttu-id="2f79d-143">[Azure 檔案服務](/azure/storage/files/storage-files-introduction)會在雲端提供完全受控的檔案共用，以裝載此案例中的所有 WordPress 內容，讓所有 VM 都能存取資料。</span><span class="sxs-lookup"><span data-stu-id="2f79d-143">[Azure Files](/azure/storage/files/storage-files-introduction) provides a fully-managed file share in the cloud that hosts all of the WordPress content in this scenario, so that all of the VMs have access to the data.</span></span>
- <span data-ttu-id="2f79d-144">[Azure Key Vault](/azure/key-vault/key-vault-overview) 用來儲存密碼、憑證和金鑰，並嚴密控制其存取權。</span><span class="sxs-lookup"><span data-stu-id="2f79d-144">[Azure Key Vault](/azure/key-vault/key-vault-overview) is used to store and tightly control access to passwords, certificates, and keys.</span></span>
- <span data-ttu-id="2f79d-145">[Azure Active Directory (Azure AD)](/azure/active-directory/fundamentals/active-directory-whatis) 是多租用戶雲端式目錄和身分識別管理服務。</span><span class="sxs-lookup"><span data-stu-id="2f79d-145">[Azure Active Directory (Azure AD)](/azure/active-directory/fundamentals/active-directory-whatis) is a multi-tenant, cloud-based directory and identity management service.</span></span> <span data-ttu-id="2f79d-146">在此案例中，Azure AD 會為網站與 VPN 通道提供驗證服務。</span><span class="sxs-lookup"><span data-stu-id="2f79d-146">In this scenario, Azure AD provides authentication services for the website and the VPN tunnels.</span></span>

### <a name="alternatives"></a><span data-ttu-id="2f79d-147">替代項目</span><span class="sxs-lookup"><span data-stu-id="2f79d-147">Alternatives</span></span>

- <span data-ttu-id="2f79d-148">[適用於 Linux 的 SQL Server](/azure/virtual-machines/linux/sql/sql-server-linux-virtual-machines-overview) 可取代 MariaDB 資料存放區。</span><span class="sxs-lookup"><span data-stu-id="2f79d-148">[SQL Server for Linux](/azure/virtual-machines/linux/sql/sql-server-linux-virtual-machines-overview) can replace the MariaDB data store.</span></span>
- <span data-ttu-id="2f79d-149">如果您偏好完全受控的解決方案，[適用於 MySQL 的 Azure 資料庫](/azure/mysql/overview)即可取代 MariaDB 資料存放區。</span><span class="sxs-lookup"><span data-stu-id="2f79d-149">[Azure database for MySQL](/azure/mysql/overview) can replace the MariaDB data store if you prefer a fully managed solution.</span></span>

## <a name="considerations"></a><span data-ttu-id="2f79d-150">考量</span><span class="sxs-lookup"><span data-stu-id="2f79d-150">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="2f79d-151">可用性</span><span class="sxs-lookup"><span data-stu-id="2f79d-151">Availability</span></span>

<span data-ttu-id="2f79d-152">此案例中的 VM 執行個體會部署於多個區域，其 WordPress 內容的資料會透過 RSYNC 在兩個區域間複寫，而 MariaDB 叢集則採用主從式複寫。</span><span class="sxs-lookup"><span data-stu-id="2f79d-152">The VM instances in this scenario are deployed across multiple regions, with the data replicated between the two via RSYNC for the WordPress content and master slave replication for the MariaDB clusters.</span></span>

### <a name="scalability"></a><span data-ttu-id="2f79d-153">延展性</span><span class="sxs-lookup"><span data-stu-id="2f79d-153">Scalability</span></span>

<span data-ttu-id="2f79d-154">此案例會在每個區域中使用兩個前端網頁伺服器叢集的虛擬機器擴展集。</span><span class="sxs-lookup"><span data-stu-id="2f79d-154">This scenario uses virtual machine scale sets for the two front-end web server clusters in each region.</span></span> <span data-ttu-id="2f79d-155">使用擴展集時，執行前端應用程式層的 VM 執行個體數目可以視回應客戶需求，或根據定義的排程來自動調整。</span><span class="sxs-lookup"><span data-stu-id="2f79d-155">With scale sets, the number of VM instances that run the front-end application tier can automatically scale in response to customer demand, or based on a defined schedule.</span></span> <span data-ttu-id="2f79d-156">如需詳細資訊，請參閱[使用虛擬機器擴展集自動調整概觀][docs-vmss-autoscale]。</span><span class="sxs-lookup"><span data-stu-id="2f79d-156">For more information, see [Overview of autoscale with virtual machine scale sets][docs-vmss-autoscale].</span></span>

<span data-ttu-id="2f79d-157">後端是可用性設定組中的 MariaDB 叢集。</span><span class="sxs-lookup"><span data-stu-id="2f79d-157">The back end is a MariaDB cluster in an availability set.</span></span> <span data-ttu-id="2f79d-158">如需詳細資訊，請參閱 [MariaDB 叢集教學課程][mariadb-tutorial]。</span><span class="sxs-lookup"><span data-stu-id="2f79d-158">For more information, see the [MariaDB cluster tutorial][mariadb-tutorial].</span></span>

<span data-ttu-id="2f79d-159">如需其他擴充性主題，請參閱 [延展性檢查清單] [延展性] 在 Azure Architecture Center。</span><span class="sxs-lookup"><span data-stu-id="2f79d-159">For other scalability topics, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="2f79d-160">安全性</span><span class="sxs-lookup"><span data-stu-id="2f79d-160">Security</span></span>

<span data-ttu-id="2f79d-161">所有流入前端應用程式層的虛擬網路流量都受到網路安全性群組的保護。</span><span class="sxs-lookup"><span data-stu-id="2f79d-161">All the virtual network traffic into the front-end application tier and protected by network security groups.</span></span> <span data-ttu-id="2f79d-162">規則會限制流量，以便只有前端應用程式層 VM 執行個體可以存取後端資料庫層。</span><span class="sxs-lookup"><span data-stu-id="2f79d-162">Rules limit the flow of traffic so that only the front-end application tier VM instances can access the back-end database tier.</span></span> <span data-ttu-id="2f79d-163">不允許資料庫層的輸出網際網路流量。</span><span class="sxs-lookup"><span data-stu-id="2f79d-163">No outbound Internet traffic is allowed from the database tier.</span></span> <span data-ttu-id="2f79d-164">若要降低攻擊使用量，請勿開啟任何直接的遠端管理連接埠。</span><span class="sxs-lookup"><span data-stu-id="2f79d-164">To reduce the attack footprint, no direct remote management ports are open.</span></span> <span data-ttu-id="2f79d-165">如需詳細資訊，請參閱 [Azure 網路安全性群組][docs-nsg]。</span><span class="sxs-lookup"><span data-stu-id="2f79d-165">For more information, see [Azure network security groups][docs-nsg].</span></span>

<span data-ttu-id="2f79d-166">如需設計安全案例的一般指引，請參閱 [Azure 安全性文件][security]。</span><span class="sxs-lookup"><span data-stu-id="2f79d-166">For general guidance on designing secure scenarios, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="2f79d-167">災害復原</span><span class="sxs-lookup"><span data-stu-id="2f79d-167">Resiliency</span></span>

<span data-ttu-id="2f79d-168">此案例結合使用多個區域、資料複寫和虛擬機器擴展集，所以會使用 Azure 負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="2f79d-168">In combination with the use of multiple regions, data replication and virtual machine scale sets, this scenario uses Azure load balancers.</span></span> <span data-ttu-id="2f79d-169">這些網路元件會將流量散發至已連線的 VM 執行個體，並包含健康情況探查，可確保流量只會散發到狀況良好的 VM。</span><span class="sxs-lookup"><span data-stu-id="2f79d-169">These networking components distribute traffic to the connected VM instances, and include health probes that ensure traffic is only distributed to healthy VMs.</span></span> <span data-ttu-id="2f79d-170">上述所有網路元件都會透過 CDN 成為前端。</span><span class="sxs-lookup"><span data-stu-id="2f79d-170">All of these networking components are fronted via a CDN.</span></span> <span data-ttu-id="2f79d-171">這會讓網路資源和應用程式有彈性地處理問題，否則會中斷流量並影響使用者存取。</span><span class="sxs-lookup"><span data-stu-id="2f79d-171">This makes the networking resources and application resilient to issues that would otherwise disrupt traffic and impact end-user access.</span></span>

<span data-ttu-id="2f79d-172">如需設計有彈性的案例的一般指引，請參閱[設計可靠的 Azure 應用程式](../../reliability/index.md)。</span><span class="sxs-lookup"><span data-stu-id="2f79d-172">For general guidance on designing resilient scenarios, see [Designing reliable Azure applications](../../reliability/index.md).</span></span>

## <a name="pricing"></a><span data-ttu-id="2f79d-173">價格</span><span class="sxs-lookup"><span data-stu-id="2f79d-173">Pricing</span></span>

<span data-ttu-id="2f79d-174">為了探索執行此案例的成本，所有服務會在成本計算機中預先設定。</span><span class="sxs-lookup"><span data-stu-id="2f79d-174">To explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="2f79d-175">若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的流量。</span><span class="sxs-lookup"><span data-stu-id="2f79d-175">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="2f79d-176">我們根據上面所提供的架構圖，提供了預先設定的[成本設定檔][pricing]。</span><span class="sxs-lookup"><span data-stu-id="2f79d-176">We have provided a pre-configured [cost profile][pricing] based on the architecture diagram provided above.</span></span> <span data-ttu-id="2f79d-177">若要針對您的使用情況設定定價計算機，請考量下列幾個主要事項：</span><span class="sxs-lookup"><span data-stu-id="2f79d-177">To configure the pricing calculator for your use case, there are a couple main things to consider:</span></span>

- <span data-ttu-id="2f79d-178">您預期有多少流量 (以 GB/月表示)？</span><span class="sxs-lookup"><span data-stu-id="2f79d-178">How much traffic are you expecting in terms of GB/month?</span></span> <span data-ttu-id="2f79d-179">流量會對您的成本造成最大影響，因為它會影響必須在虛擬機器擴展集中呈現資料的 VM 數目。</span><span class="sxs-lookup"><span data-stu-id="2f79d-179">The amount of traffic will have the biggest impact on your cost, as it will impact the number of VMs that are required to surface the data in the virtual machine scale set.</span></span> <span data-ttu-id="2f79d-180">此外，它會直接與透過 CDN 呈現的資料量相互關聯。</span><span class="sxs-lookup"><span data-stu-id="2f79d-180">Additionally, it will directly correlate with the amount of data that is surfaced via the CDN.</span></span>
- <span data-ttu-id="2f79d-181">您即將在您的網站上撰寫多少新資料？</span><span class="sxs-lookup"><span data-stu-id="2f79d-181">How much new data are you going to be writing to your website?</span></span> <span data-ttu-id="2f79d-182">寫入至您網站的新資料會與跨區域鏡像處理多少資料相互關聯。</span><span class="sxs-lookup"><span data-stu-id="2f79d-182">New data written to your website correlates with how much data is mirrored across the regions.</span></span>
- <span data-ttu-id="2f79d-183">您有多少動態內容？</span><span class="sxs-lookup"><span data-stu-id="2f79d-183">How much of your content is dynamic?</span></span> <span data-ttu-id="2f79d-184">多少靜態內容？</span><span class="sxs-lookup"><span data-stu-id="2f79d-184">How much is static?</span></span> <span data-ttu-id="2f79d-185">動態和靜態內容間的差異會影響必須從資料庫層擷取多少資料，以及要在 CDN 中快取多少資料。</span><span class="sxs-lookup"><span data-stu-id="2f79d-185">The variance around dynamic and static content influences how much data has to be retrieved from the database tier versus how much will be cached in the CDN.</span></span>

<!-- links -->
[architecture]: ./media/architecture-secure-scalable-wordpress.png
[mariadb-tutorial]: /azure/virtual-machines/linux/classic/mariadb-mysql-cluster
[docs-vmss]: /azure/virtual-machine-scale-sets/overview
[docs-vmss-autoscale]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview
[docs-nsg]: /azure/virtual-network/security-overview
[security]: /azure/security/
[availability]: ../../checklist/availability.md
[pricing]: https://azure.com/e/a8c4809dab444c1ca4870c489fbb196b

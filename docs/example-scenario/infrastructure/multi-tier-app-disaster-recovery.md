---
title: 針對 HA/DR 所建置的多層式 Web 應用程式
titleSuffix: Azure Example Scenarios
description: 使用 Azure 虛擬機器、可用性集合、可用性區域、Azure Site Recovery 和 Azure 流量管理員，在 Azure 上建立為高可用性和災害復原而建置的多層式 Web 應用程式。
author: sujayt
ms.date: 11/16/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: product-team
social_image_url: /azure/architecture/example-scenario/infrastructure/media/arhitecture-disaster-recovery-multi-tier-app.png
ms.openlocfilehash: 1f82f0bf5421bb251bda2eb60349cc74014fd454
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58249563"
---
# <a name="multitier-web-application-built-for-high-availability-and-disaster-recovery-on-azure"></a><span data-ttu-id="57344-103">在 Azure 上為高可用性和災害復原而建置的多層式 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="57344-103">Multitier web application built for high availability and disaster recovery on Azure</span></span>

<span data-ttu-id="57344-104">任何產業只要需要部署為高可用性和災害復原而建置的彈性多層式應用程式，皆適用此範例案例。</span><span class="sxs-lookup"><span data-stu-id="57344-104">This example scenario is applicable to any industry that needs to deploy resilient multitier applications built for high availability and disaster recovery.</span></span> <span data-ttu-id="57344-105">在此案例中，應用程式包含三個層級。</span><span class="sxs-lookup"><span data-stu-id="57344-105">In this scenario, the application consists of three layers.</span></span>

- <span data-ttu-id="57344-106">Web 層：包含使用者介面的最上層。</span><span class="sxs-lookup"><span data-stu-id="57344-106">Web tier: The top layer including the user interface.</span></span> <span data-ttu-id="57344-107">這一層會剖析使用者互動，並將動作傳至下一層進行處理。</span><span class="sxs-lookup"><span data-stu-id="57344-107">This layer parses user interactions and passes the actions to next layer for processing.</span></span>
- <span data-ttu-id="57344-108">商務層：處理使用者互動，並擬定關於後續步驟的邏輯決策。</span><span class="sxs-lookup"><span data-stu-id="57344-108">Business tier: Processes the user interactions and makes logical decisions about the next steps.</span></span> <span data-ttu-id="57344-109">這一層會連接 Web 層與資料層。</span><span class="sxs-lookup"><span data-stu-id="57344-109">This layer connects the web tier and the data tier.</span></span>
- <span data-ttu-id="57344-110">資料層：儲存應用程式資料。</span><span class="sxs-lookup"><span data-stu-id="57344-110">Data tier: Stores the application data.</span></span> <span data-ttu-id="57344-111">通常會使用資料庫、物件儲存體或檔案儲存體。</span><span class="sxs-lookup"><span data-stu-id="57344-111">Either a database, object storage, or file storage is typically used.</span></span>

<span data-ttu-id="57344-112">常見的應用程式案例包括任何在 Windows 或 Linux 上執行的任務關鍵性應用程式。</span><span class="sxs-lookup"><span data-stu-id="57344-112">Common application scenarios include any mission-critical application running on Windows or Linux.</span></span> <span data-ttu-id="57344-113">這可以是現成可用的應用程式 (例如 SAP 和 SharePoint) 或自訂的企業營運應用程式。</span><span class="sxs-lookup"><span data-stu-id="57344-113">This can be an off-the-shelf application such as SAP and SharePoint or a custom line-of-business application.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="57344-114">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="57344-114">Relevant use cases</span></span>

<span data-ttu-id="57344-115">其他相關的使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="57344-115">Other relevant use cases include:</span></span>

- <span data-ttu-id="57344-116">部署高復原能力的應用程式，例如 SAP 和 SharePoint</span><span class="sxs-lookup"><span data-stu-id="57344-116">Deploying highly resilient applications such as SAP and SharePoint</span></span>
- <span data-ttu-id="57344-117">設計企業營運應用程式的商務持續性和災害復原計劃</span><span class="sxs-lookup"><span data-stu-id="57344-117">Designing a business continuity and disaster recovery plan for line-of-business applications</span></span>
- <span data-ttu-id="57344-118">設定災害復原和執行合規性的相關演練</span><span class="sxs-lookup"><span data-stu-id="57344-118">Configure disaster recovery and perform related drills for compliance purposes</span></span>

## <a name="architecture"></a><span data-ttu-id="57344-119">架構</span><span class="sxs-lookup"><span data-stu-id="57344-119">Architecture</span></span>

<span data-ttu-id="57344-120">此案例將示範使用 ASP.NET 和 Microsoft SQL Server 的多層式應用程式。</span><span class="sxs-lookup"><span data-stu-id="57344-120">This scenario demonstrates a multitier application that uses ASP.NET and Microsoft SQL Server.</span></span> <span data-ttu-id="57344-121">在[支援可用性區域的 Azure 區域](/azure/availability-zones/az-overview#regions-that-support-availability-zones)中，您可以在來源區域中跨可用性區域部署虛擬機器 (VM)，並將 VM 複寫至用於災害復原的目標區域。</span><span class="sxs-lookup"><span data-stu-id="57344-121">In [Azure regions that support availability zones](/azure/availability-zones/az-overview#regions-that-support-availability-zones), you can deploy your virtual machines (VMs) in a source region across availability zones and replicate the VMs to the target region used for disaster recovery.</span></span> <span data-ttu-id="57344-122">在不支援可用性區域的 Azure 區域中，您可以在可用性設定組內部署 VM，並將 VM 複寫至目標區域。</span><span class="sxs-lookup"><span data-stu-id="57344-122">In Azure regions that don't support availability zones, you can deploy your VMs within an availability set and replicate the VMs to the target region.</span></span>

![高復原能力的多層式 Web 應用程式的架構概觀][architecture]

- <span data-ttu-id="57344-124">在支援區域的區域中，將 VM 分散到兩個可用性區域的每一層中。</span><span class="sxs-lookup"><span data-stu-id="57344-124">Distribute the VMs in each tier across two availability zones in regions that support zones.</span></span> <span data-ttu-id="57344-125">在其他區域中，則將 VM 部署在一個可用性設定組中的每一層。</span><span class="sxs-lookup"><span data-stu-id="57344-125">In other regions, deploy the VMs in each tier within one availability set.</span></span>
- <span data-ttu-id="57344-126">您可以將資料庫層設定為使用 Always On 可用性群組。</span><span class="sxs-lookup"><span data-stu-id="57344-126">The database tier can be configured to use Always On availability groups.</span></span> <span data-ttu-id="57344-127">使用此 SQL Server 設定時，會使用最多八個次要資料庫來設定叢集內的一個主要資料庫。</span><span class="sxs-lookup"><span data-stu-id="57344-127">With this SQL Server configuration, one primary database within a cluster is configured with up to eight secondary databases.</span></span> <span data-ttu-id="57344-128">如果主要資料庫發生問題，叢集就會容錯移轉至其中一個次要資料庫，讓應用程式能夠繼續使用。</span><span class="sxs-lookup"><span data-stu-id="57344-128">If an issue occurs with the primary database, the cluster fails over to one of the secondary databases, allowing the application to remain available.</span></span> <span data-ttu-id="57344-129">如需詳細資訊，請參閱[適用於 SQL Server 的 Always On 可用性群組概觀][docs-sql-always-on]。</span><span class="sxs-lookup"><span data-stu-id="57344-129">For more information, see [Overview of Always On availability groups for SQL Server][docs-sql-always-on].</span></span>
- <span data-ttu-id="57344-130">針對災害復原案例，您可以進行相關設定，以對用於災害復原的目標區域執行 SQL Always On 非同步原生複寫。</span><span class="sxs-lookup"><span data-stu-id="57344-130">For disaster recovery scenarios, you can configure SQL Always On asynchronous native replication to the target region used for disaster recovery.</span></span> <span data-ttu-id="57344-131">如果資料變更率在 Azure Site Recovery 支援的限制內，您也可以設定對目標區域的 Azure Site Recovery 複寫。</span><span class="sxs-lookup"><span data-stu-id="57344-131">You can also configure Azure Site Recovery replication to the target region if the data change rate is within supported limits of Azure Site Recovery.</span></span>
- <span data-ttu-id="57344-132">使用者會透過流量管理員端點來存取前端 ASP.NET Web 層。</span><span class="sxs-lookup"><span data-stu-id="57344-132">Users access the front-end ASP.NET web tier via the traffic manager endpoint.</span></span>
- <span data-ttu-id="57344-133">流量管理員會將流量重新導向至主要來源區域中的主要公用 IP 端點。</span><span class="sxs-lookup"><span data-stu-id="57344-133">The traffic manager redirects traffic to the primary public IP endpoint in the primary source region.</span></span>
- <span data-ttu-id="57344-134">此公用 IP 會透過公用負載平衡器將呼叫重新導向至其中一個 Web 層 VM 執行個體。</span><span class="sxs-lookup"><span data-stu-id="57344-134">The public IP redirects the call to one of the web tier VM instances through a public load balancer.</span></span> <span data-ttu-id="57344-135">所有的 Web 層 VM 執行個體會位於一個子網路中。</span><span class="sxs-lookup"><span data-stu-id="57344-135">All web tier VM instances are in one subnet.</span></span>
- <span data-ttu-id="57344-136">來自 Web 層 VM 的每個呼叫，都會透過內部負載平衡器路由至商務層中的其中一個 VM 執行個體，以進行處理。</span><span class="sxs-lookup"><span data-stu-id="57344-136">From the web tier VM, each call is routed to one of the VM instances in the business tier through an internal load balancer for processing.</span></span> <span data-ttu-id="57344-137">所有的商務層 VM 會位於一個不同的子網路中。</span><span class="sxs-lookup"><span data-stu-id="57344-137">All business tier VMs are in a separate subnet.</span></span>
- <span data-ttu-id="57344-138">作業會在商務層中進行處理，而 ASP.NET 應用程式會透過 Azure 內部負載平衡器連線到後端層中的 Microsoft SQL Server 叢集。</span><span class="sxs-lookup"><span data-stu-id="57344-138">The operation is processed in the business tier and the ASP.NET application connects to Microsoft SQL Server cluster in a back-end tier via an Azure internal load balancer.</span></span> <span data-ttu-id="57344-139">這些後端 SQL Server 執行個體會位於一個不同的子網路中。</span><span class="sxs-lookup"><span data-stu-id="57344-139">These back-end SQL Server instances are in a separate subnet.</span></span>
- <span data-ttu-id="57344-140">流量管理員的次要端點會在用於災害復原的目標區域中設定為公用 IP。</span><span class="sxs-lookup"><span data-stu-id="57344-140">The traffic manager's secondary endpoint is configured as the public IP in the target region used for disaster recovery.</span></span>
- <span data-ttu-id="57344-141">如果主要區域的運作中斷，您只要叫用 Azure Site Recovery 容錯移轉，應用程式在目標區域中即會成為使用中狀態。</span><span class="sxs-lookup"><span data-stu-id="57344-141">In the event of a primary region disruption, you invoke Azure Site Recovery failover and the application becomes active in the target region.</span></span>
- <span data-ttu-id="57344-142">流量管理員端點會將用戶端流量自動重新導向至目標區域中的公用 IP。</span><span class="sxs-lookup"><span data-stu-id="57344-142">The traffic manager endpoint automatically redirects the client traffic to the public IP in the target region.</span></span>

### <a name="components"></a><span data-ttu-id="57344-143">元件</span><span class="sxs-lookup"><span data-stu-id="57344-143">Components</span></span>

- <span data-ttu-id="57344-144">[可用性設定組][docs-availability-sets]可確保您在 Azure 上部署的 VM 會分散到叢集中多個各自獨立的硬體節點。</span><span class="sxs-lookup"><span data-stu-id="57344-144">[Availability sets][docs-availability-sets] ensure that the VMs you deploy on Azure are distributed across multiple isolated hardware nodes in a cluster.</span></span> <span data-ttu-id="57344-145">當 Azure 內發生硬體或軟體故障時，您的 VM 將只有一部分會受到影響，且您整體的解決方案仍可供使用且正常運作。</span><span class="sxs-lookup"><span data-stu-id="57344-145">If a hardware or software failure occurs within Azure, only a subset of your VMs are affected and your entire solution remains available and operational.</span></span>
- <span data-ttu-id="57344-146">[可用性區域][docs-availability-zones]可讓您的應用程式和資料不因資料中心失敗而受影響。</span><span class="sxs-lookup"><span data-stu-id="57344-146">[Availability zones][docs-availability-zones] protect your applications and data from datacenter failures.</span></span> <span data-ttu-id="57344-147">可用性區域是 Azure 區域內個別的實體位置。</span><span class="sxs-lookup"><span data-stu-id="57344-147">Availability zones are separate physical locations within an Azure region.</span></span> <span data-ttu-id="57344-148">每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。</span><span class="sxs-lookup"><span data-stu-id="57344-148">Each zone consists of one or more datacenters equipped with independent power, cooling, and networking.</span></span>
- <span data-ttu-id="57344-149">[Azure Site Recovery (ASR)][docs-azure-site-recovery] 可讓您將 VM 複寫到另一個 Azure 區域，以符合商務持續性和災害復原的需求。</span><span class="sxs-lookup"><span data-stu-id="57344-149">[Azure Site Recovery (ASR)][docs-azure-site-recovery] allows you to replicate VMs to another Azure region for business continuity and disaster recovery needs.</span></span> <span data-ttu-id="57344-150">您可以進行定期的災害復原演練，以確實符合合規需求。</span><span class="sxs-lookup"><span data-stu-id="57344-150">You can conduct periodic disaster recovery drills to ensure you meet the compliance needs.</span></span> <span data-ttu-id="57344-151">會以指定的設定將 VM 複寫到選取的區域，以便您可以在來源區域的中斷事件中復原應用程式。</span><span class="sxs-lookup"><span data-stu-id="57344-151">The VM will be replicated with the specified settings to the selected region so that you can recover your applications in the event of outages in the source region.</span></span>
- <span data-ttu-id="57344-152">[Azure 流量管理員][docs-traffic-manager]是 DNS 型流量負載平衡器，可跨全球的 Azure 區域將流量最適當地分散至服務，同時提供高可用性和回應性。</span><span class="sxs-lookup"><span data-stu-id="57344-152">[Azure Traffic Manager][docs-traffic-manager] is a DNS-based traffic load balancer that distributes traffic optimally to services across global Azure regions while providing high availability and responsiveness.</span></span>
- <span data-ttu-id="57344-153">[Azure Load Balancer][docs-load-balancer] 會根據規則和健康情況探查來散發輸入流量。</span><span class="sxs-lookup"><span data-stu-id="57344-153">[Azure Load Balancer][docs-load-balancer] distributes inbound traffic according to defined rules and health probes.</span></span> <span data-ttu-id="57344-154">對於所有 TCP 和 UDP 應用程式，負載平衡器可提供低延遲和高輸送量，且最多可相應增加為數百萬個流程。</span><span class="sxs-lookup"><span data-stu-id="57344-154">A load balancer provides low latency and high throughput, scaling up to millions of flows for all TCP and UDP applications.</span></span> <span data-ttu-id="57344-155">此案例會使用公用負載平衡器，將傳入的用戶端流量散發至 Web 層。</span><span class="sxs-lookup"><span data-stu-id="57344-155">A public load balancer is used in this scenario to distribute incoming client traffic to the web tier.</span></span> <span data-ttu-id="57344-156">此案例會使用內部負載平衡器，從商務層將流量散發到後端 SQL Server 叢集。</span><span class="sxs-lookup"><span data-stu-id="57344-156">An internal load balancer is used in this scenario to distribute traffic from the business tier to the back-end SQL Server cluster.</span></span>

### <a name="alternatives"></a><span data-ttu-id="57344-157">替代項目</span><span class="sxs-lookup"><span data-stu-id="57344-157">Alternatives</span></span>

- <span data-ttu-id="57344-158">您可以將 Windows 取代為其他作業系統，因為基礎結構中沒有任何項目依存於作業系統。</span><span class="sxs-lookup"><span data-stu-id="57344-158">Windows can be replaced by other operating systems because nothing in the infrastructure is dependent on the operating system.</span></span>
- <span data-ttu-id="57344-159">[適用於 Linux 的 SQL Server][docs-sql-server-linux] 可取代後端資料存放區。</span><span class="sxs-lookup"><span data-stu-id="57344-159">[SQL Server for Linux][docs-sql-server-linux] can replace the back-end data store.</span></span>
- <span data-ttu-id="57344-160">資料庫可以取代為任何可用的標準資料庫應用程式。</span><span class="sxs-lookup"><span data-stu-id="57344-160">The database can be replaced by any standard database application available.</span></span>

## <a name="other-considerations"></a><span data-ttu-id="57344-161">其他考量</span><span class="sxs-lookup"><span data-stu-id="57344-161">Other considerations</span></span>

### <a name="scalability"></a><span data-ttu-id="57344-162">延展性</span><span class="sxs-lookup"><span data-stu-id="57344-162">Scalability</span></span>

<span data-ttu-id="57344-163">您可以根據自己的調整需求，在各層中新增或移除 VM。</span><span class="sxs-lookup"><span data-stu-id="57344-163">You can add or remove VMs in each tier based on your scaling requirements.</span></span> <span data-ttu-id="57344-164">由於此案例使用負載平衡器，因此您可以在一層中新增更多 VM，而不會影響應用程式的運作時間。</span><span class="sxs-lookup"><span data-stu-id="57344-164">Because this scenario uses load balancers, you can add more VMs to a tier without affecting application uptime.</span></span>

<span data-ttu-id="57344-165">如需其他延展性主題，請參閱 Azure Architecture Center 中的[延展性檢查清單][scalability]。</span><span class="sxs-lookup"><span data-stu-id="57344-165">For other scalability topics, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="57344-166">安全性</span><span class="sxs-lookup"><span data-stu-id="57344-166">Security</span></span>

<span data-ttu-id="57344-167">所有流入前端應用程式層的虛擬網路流量都受到網路安全性群組的保護。</span><span class="sxs-lookup"><span data-stu-id="57344-167">All the virtual network traffic into the front-end application tier is protected by network security groups.</span></span> <span data-ttu-id="57344-168">規則會限制流量，以便只有前端應用程式層 VM 執行個體可以存取後端資料庫層。</span><span class="sxs-lookup"><span data-stu-id="57344-168">Rules limit the flow of traffic so that only the front-end application tier VM instances can access the back-end database tier.</span></span> <span data-ttu-id="57344-169">不允許從商務層或資料庫層輸出網際網路流量。</span><span class="sxs-lookup"><span data-stu-id="57344-169">No outbound internet traffic is allowed from the business tier or database tier.</span></span> <span data-ttu-id="57344-170">若要降低攻擊使用量，請勿開啟任何直接的遠端管理連接埠。</span><span class="sxs-lookup"><span data-stu-id="57344-170">To reduce the attack footprint, no direct remote management ports are open.</span></span> <span data-ttu-id="57344-171">如需詳細資訊，請參閱 [Azure 網路安全性群組][docs-nsg]。</span><span class="sxs-lookup"><span data-stu-id="57344-171">For more information, see [Azure network security groups][docs-nsg].</span></span>

<span data-ttu-id="57344-172">如需設計安全案例的一般指引，請參閱 [Azure 安全性文件][security]。</span><span class="sxs-lookup"><span data-stu-id="57344-172">For general guidance on designing secure scenarios, see the [Azure Security Documentation][security].</span></span>

## <a name="pricing"></a><span data-ttu-id="57344-173">價格</span><span class="sxs-lookup"><span data-stu-id="57344-173">Pricing</span></span>

<span data-ttu-id="57344-174">使用 Azure Site Recovery 設定 Azure VM 的災害復原，將會持續產生下列費用。</span><span class="sxs-lookup"><span data-stu-id="57344-174">Configuring disaster recovery for Azure VMs using Azure Site Recovery will incur the following charges on an ongoing basis.</span></span>

- <span data-ttu-id="57344-175">每一 VM 的 Azure Site Recovery 授權成本。</span><span class="sxs-lookup"><span data-stu-id="57344-175">Azure Site Recovery licensing cost per VM.</span></span>
- <span data-ttu-id="57344-176">從來源 VM 磁碟將資料變更複寫到另一個 Azure 區域的網路輸出費用。</span><span class="sxs-lookup"><span data-stu-id="57344-176">Network egress costs to replicate data changes from the source VM disks to another Azure region.</span></span> <span data-ttu-id="57344-177">Azure Site Recovery 會使用內建的壓縮功能，資料傳輸需求約可減少 50%。</span><span class="sxs-lookup"><span data-stu-id="57344-177">Azure Site Recovery uses built-in compression to reduce the data transfer requirements by approximately 50%.</span></span>
- <span data-ttu-id="57344-178">復原網站的儲存體成本。</span><span class="sxs-lookup"><span data-stu-id="57344-178">Storage costs on the recovery site.</span></span> <span data-ttu-id="57344-179">此成本通常等同於來源區域儲存體再加上維護復原的快照集作為復原點所需的任何額外的儲存體。</span><span class="sxs-lookup"><span data-stu-id="57344-179">This is typically the same as the source region storage plus any additional storage needed to maintain the recovery points as snapshots for recovery.</span></span>

<span data-ttu-id="57344-180">我們提供了[範例成本計算機][calculator]，可用來對使用六個虛擬機器的三層式應用程式設定災害復原。</span><span class="sxs-lookup"><span data-stu-id="57344-180">We have provided a [sample cost calculator][calculator] for configuring disaster recovery for a three-tier application using six virtual machines.</span></span> <span data-ttu-id="57344-181">所有服務都會在成本計算機中預先設定。</span><span class="sxs-lookup"><span data-stu-id="57344-181">All of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="57344-182">若要查看價格如何隨著您的特定使用案例而變更，請變更適當的變數，以估計成本。</span><span class="sxs-lookup"><span data-stu-id="57344-182">To see how the pricing would change for your particular use case, change the appropriate variables to estimate the cost.</span></span>

<!-- links -->
[architecture]: ./media/arhitecture-disaster-recovery-multi-tier-app.png
[autoscaling]: /azure/architecture/best-practices/auto-scaling
[availability]: ../../checklist/availability.md
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[docs-availability-zones]: /azure/availability-zones/az-overview
[docs-load-balancer]: /azure/load-balancer/load-balancer-overview
[docs-nsg]: /azure/virtual-network/security-overview
[docs-vmss]: /azure/virtual-machine-scale-sets/overview
[docs-sql-always-on]: /sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server
[docs-vmss-autoscale]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview
[docs-vnet]: /azure/virtual-network/virtual-networks-overview
[docs-sql-server-linux]: /sql/linux/sql-server-linux-overview?view=sql-server-linux-2017
[docs-traffic-manager]: /azure/traffic-manager/
[docs-azure-site-recovery]: /azure/site-recovery/azure-to-azure-quickstart/
[docs-availability-sets]: /azure/virtual-machines/windows/manage-availability/
[calculator]: https://azure.com/e/6835332265044d6d931d68c917979e6d/

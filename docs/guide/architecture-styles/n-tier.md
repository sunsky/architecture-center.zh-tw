---
title: 多層式架構樣式
titleSuffix: Azure Application Architecture Guide
description: 說明 Azure 上多層式架構的優點、挑戰和最佳做法。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: d7b94d56831a6b9172a9091f0e4f7fa63a8881f1
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58244609"
---
# <a name="n-tier-architecture-style"></a><span data-ttu-id="152b3-103">多層式架構樣式</span><span class="sxs-lookup"><span data-stu-id="152b3-103">N-tier architecture style</span></span>

<span data-ttu-id="152b3-104">多層式架構會將應用程式分成**邏輯層**和**實體層**。</span><span class="sxs-lookup"><span data-stu-id="152b3-104">An N-tier architecture divides an application into **logical layers** and **physical tiers**.</span></span>

![多層式架構 (N-Tier) 樣式的邏輯圖](./images/n-tier-logical.svg)

<span data-ttu-id="152b3-106">層是用來分隔責任與管理相依性的一個方式。</span><span class="sxs-lookup"><span data-stu-id="152b3-106">Layers are a way to separate responsibilities and manage dependencies.</span></span> <span data-ttu-id="152b3-107">每個層有特定的責任。</span><span class="sxs-lookup"><span data-stu-id="152b3-107">Each layer has a specific responsibility.</span></span> <span data-ttu-id="152b3-108">較高層可以使用較低層中的服務，反向則不行。</span><span class="sxs-lookup"><span data-stu-id="152b3-108">A higher layer can use services in a lower layer, but not the other way around.</span></span>

<span data-ttu-id="152b3-109">層在實際上是分隔的，在個別電腦上執行。</span><span class="sxs-lookup"><span data-stu-id="152b3-109">Tiers are physically separated, running on separate machines.</span></span> <span data-ttu-id="152b3-110">層可以直接呼叫另一層，或使用非同步傳訊 (訊息佇列)。</span><span class="sxs-lookup"><span data-stu-id="152b3-110">A tier can call to another tier directly, or use asynchronous messaging (message queue).</span></span> <span data-ttu-id="152b3-111">雖然每個層可能會裝載在自己的層中，但並非必要。</span><span class="sxs-lookup"><span data-stu-id="152b3-111">Although each layer might be hosted in its own tier, that's not required.</span></span> <span data-ttu-id="152b3-112">數個層可能會裝載於相同層上。</span><span class="sxs-lookup"><span data-stu-id="152b3-112">Several layers might be hosted on the same tier.</span></span> <span data-ttu-id="152b3-113">實際分隔各層可改善延展性和復原，但也會從其他網路通訊增加延遲。</span><span class="sxs-lookup"><span data-stu-id="152b3-113">Physically separating the tiers improves scalability and resiliency, but also adds latency from the additional network communication.</span></span>

<span data-ttu-id="152b3-114">傳統的三層式應用程式有展示層、中介層和資料庫層。</span><span class="sxs-lookup"><span data-stu-id="152b3-114">A traditional three-tier application has a presentation tier, a middle tier, and a database tier.</span></span> <span data-ttu-id="152b3-115">中介層是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="152b3-115">The middle tier is optional.</span></span> <span data-ttu-id="152b3-116">更複雜的應用程式可以有三層以上。</span><span class="sxs-lookup"><span data-stu-id="152b3-116">More complex applications can have more than three tiers.</span></span> <span data-ttu-id="152b3-117">上圖顯示具有兩個中間層的應用程式，封裝不同區域的功能。</span><span class="sxs-lookup"><span data-stu-id="152b3-117">The diagram above shows an application with two middle tiers, encapsulating different areas of functionality.</span></span>

<span data-ttu-id="152b3-118">多層式架構應用程式可以有**關閉層架構**或**開放層架構**：</span><span class="sxs-lookup"><span data-stu-id="152b3-118">An N-tier application can have a **closed layer architecture** or an **open layer architecture**:</span></span>

- <span data-ttu-id="152b3-119">在關閉層架構中，層只能呼叫緊接著的下一層。</span><span class="sxs-lookup"><span data-stu-id="152b3-119">In a closed layer architecture, a layer can only call the next layer immediately down.</span></span>
- <span data-ttu-id="152b3-120">在開放層架構中，層也可以呼叫其下方的任何層。</span><span class="sxs-lookup"><span data-stu-id="152b3-120">In an open layer architecture, a layer can call any of the layers below it.</span></span>

<span data-ttu-id="152b3-121">關閉層架構會限制層之間的相依性。</span><span class="sxs-lookup"><span data-stu-id="152b3-121">A closed layer architecture limits the dependencies between layers.</span></span> <span data-ttu-id="152b3-122">不過，如果某層只會將要求傳遞至下一層，它可能會產生不必要的網路流量。</span><span class="sxs-lookup"><span data-stu-id="152b3-122">However, it might create unnecessary network traffic, if one layer simply passes requests along to the next layer.</span></span>

## <a name="when-to-use-this-architecture"></a><span data-ttu-id="152b3-123">使用此架構的時機</span><span class="sxs-lookup"><span data-stu-id="152b3-123">When to use this architecture</span></span>

<span data-ttu-id="152b3-124">多層式架構通常實作為基礎結構即服務 (IaaS) 應用程式，每個層均執行在一組個別的 VM 上。</span><span class="sxs-lookup"><span data-stu-id="152b3-124">N-tier architectures are typically implemented as infrastructure-as-service (IaaS) applications, with each tier running on a separate set of VMs.</span></span> <span data-ttu-id="152b3-125">不過，多層式架構應用程式不需要是純 IaaS。</span><span class="sxs-lookup"><span data-stu-id="152b3-125">However, an N-tier application doesn't need to be pure IaaS.</span></span> <span data-ttu-id="152b3-126">通常，對架構的某些部分 (特別是快取、傳訊和資料存放區) 使用受控服務會有幫助。</span><span class="sxs-lookup"><span data-stu-id="152b3-126">Often, it's advantageous to use managed services for some parts of the architecture, particularly caching, messaging, and data storage.</span></span>

<span data-ttu-id="152b3-127">考慮下列項目的多層式架構：</span><span class="sxs-lookup"><span data-stu-id="152b3-127">Consider an N-tier architecture for:</span></span>

- <span data-ttu-id="152b3-128">簡單的 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="152b3-128">Simple web applications.</span></span>
- <span data-ttu-id="152b3-129">使用最小重構將內部部署應用程式移轉至 Azure。</span><span class="sxs-lookup"><span data-stu-id="152b3-129">Migrating an on-premises application to Azure with minimal refactoring.</span></span>
- <span data-ttu-id="152b3-130">內部部署和雲端應用程式的整合開發。</span><span class="sxs-lookup"><span data-stu-id="152b3-130">Unified development of on-premises and cloud applications.</span></span>

<span data-ttu-id="152b3-131">多層式架構架構在傳統內部部署應用程式中很常見，因此，它自然很適合用來將現有的工作負載移轉至 Azure。</span><span class="sxs-lookup"><span data-stu-id="152b3-131">N-tier architectures are very common in traditional on-premises applications, so it's a natural fit for migrating existing workloads to Azure.</span></span>

## <a name="benefits"></a><span data-ttu-id="152b3-132">優點</span><span class="sxs-lookup"><span data-stu-id="152b3-132">Benefits</span></span>

- <span data-ttu-id="152b3-133">雲端與內部部署之間，以及雲端平台之間的可攜性。</span><span class="sxs-lookup"><span data-stu-id="152b3-133">Portability between cloud and on-premises, and between cloud platforms.</span></span>
- <span data-ttu-id="152b3-134">對大部分開發人員來說，學習曲線較少。</span><span class="sxs-lookup"><span data-stu-id="152b3-134">Less learning curve for most developers.</span></span>
- <span data-ttu-id="152b3-135">從傳統應用程式模型的自然演進。</span><span class="sxs-lookup"><span data-stu-id="152b3-135">Natural evolution from the traditional application model.</span></span>
- <span data-ttu-id="152b3-136">開放給異質性環境 (Windows/Linux)</span><span class="sxs-lookup"><span data-stu-id="152b3-136">Open to heterogeneous environment (Windows/Linux)</span></span>

## <a name="challenges"></a><span data-ttu-id="152b3-137">挑戰</span><span class="sxs-lookup"><span data-stu-id="152b3-137">Challenges</span></span>

- <span data-ttu-id="152b3-138">產生只會在資料庫上執行 CRUD 作業、新增額外的延遲而不會執行任何實用工作的中介層很容易。</span><span class="sxs-lookup"><span data-stu-id="152b3-138">It's easy to end up with a middle tier that just does CRUD operations on the database, adding extra latency without doing any useful work.</span></span>
- <span data-ttu-id="152b3-139">整合型設計可避免獨立部署功能。</span><span class="sxs-lookup"><span data-stu-id="152b3-139">Monolithic design prevents independent deployment of features.</span></span>
- <span data-ttu-id="152b3-140">管理 IaaS 應用程式的工作比只使用受控服務的應用程式更多。</span><span class="sxs-lookup"><span data-stu-id="152b3-140">Managing an IaaS application is more work than an application that uses only managed services.</span></span>
- <span data-ttu-id="152b3-141">在大型系統中可能難以管理網路安全性。</span><span class="sxs-lookup"><span data-stu-id="152b3-141">It can be difficult to manage network security in a large system.</span></span>

## <a name="best-practices"></a><span data-ttu-id="152b3-142">最佳作法</span><span class="sxs-lookup"><span data-stu-id="152b3-142">Best practices</span></span>

- <span data-ttu-id="152b3-143">您可以使用自動調整來處理負載中的變更。</span><span class="sxs-lookup"><span data-stu-id="152b3-143">Use autoscaling to handle changes in load.</span></span> <span data-ttu-id="152b3-144">請參閱[自動調整最佳做法][autoscaling]。</span><span class="sxs-lookup"><span data-stu-id="152b3-144">See [Autoscaling best practices][autoscaling].</span></span>
- <span data-ttu-id="152b3-145">使用非同步傳訊來分離各層。</span><span class="sxs-lookup"><span data-stu-id="152b3-145">Use asynchronous messaging to decouple tiers.</span></span>
- <span data-ttu-id="152b3-146">快取半靜態資料。</span><span class="sxs-lookup"><span data-stu-id="152b3-146">Cache semi-static data.</span></span> <span data-ttu-id="152b3-147">請參閱[快取最佳做法][caching]。</span><span class="sxs-lookup"><span data-stu-id="152b3-147">See [Caching best practices][caching].</span></span>
- <span data-ttu-id="152b3-148">使用 [SQL Server Always On 可用性群組][sql-always-on]之類解決方案來設定資料庫層以獲得高可用性。</span><span class="sxs-lookup"><span data-stu-id="152b3-148">Configure database tier for high availability, using a solution such as [SQL Server Always On Availability Groups][sql-always-on].</span></span>
- <span data-ttu-id="152b3-149">將 Web 應用程式防火牆 (WAF) 放置在前端與網際網路之間。</span><span class="sxs-lookup"><span data-stu-id="152b3-149">Place a web application firewall (WAF) between the front end and the Internet.</span></span>
- <span data-ttu-id="152b3-150">將每一層放在自己的子網路中，並使用子網路作為安全性界限。</span><span class="sxs-lookup"><span data-stu-id="152b3-150">Place each tier in its own subnet, and use subnets as a security boundary.</span></span>
- <span data-ttu-id="152b3-151">透過僅允許來自中間層的要求，限制存取資料層。</span><span class="sxs-lookup"><span data-stu-id="152b3-151">Restrict access to the data tier, by allowing requests only from the middle tier(s).</span></span>

## <a name="n-tier-architecture-on-virtual-machines"></a><span data-ttu-id="152b3-152">虛擬機器上的多層式架構</span><span class="sxs-lookup"><span data-stu-id="152b3-152">N-tier architecture on virtual machines</span></span>

<span data-ttu-id="152b3-153">本節說明 VM 上執行的建議多層式架構。</span><span class="sxs-lookup"><span data-stu-id="152b3-153">This section describes a recommended N-tier architecture running on VMs.</span></span>

![多層式架構 (N-Tier) 的實體圖](./images/n-tier-physical.png)

<span data-ttu-id="152b3-155">每一層是由放在可用性設定組或 VM 擴展集的兩或多個 VM 所組成。</span><span class="sxs-lookup"><span data-stu-id="152b3-155">Each tier consists of two or more VMs, placed in an availability set or VM scale set.</span></span> <span data-ttu-id="152b3-156">一個 VM 失敗時，多個 VM 會提供復原。</span><span class="sxs-lookup"><span data-stu-id="152b3-156">Multiple VMs provide resiliency in case one VM fails.</span></span> <span data-ttu-id="152b3-157">負載平衡器可用來在層中的 VM 間散發要求。</span><span class="sxs-lookup"><span data-stu-id="152b3-157">Load balancers are used to distribute requests across the VMs in a tier.</span></span> <span data-ttu-id="152b3-158">將更多 VM 新增至集區，即可以水平調整層。</span><span class="sxs-lookup"><span data-stu-id="152b3-158">A tier can be scaled horizontally by adding more VMs to the pool.</span></span>

<span data-ttu-id="152b3-159">每一層也會放在自內己的子網路，這表示其內部 IP 位址落在相同的位址範圍內。</span><span class="sxs-lookup"><span data-stu-id="152b3-159">Each tier is also placed inside its own subnet, meaning their internal IP addresses fall within the same address range.</span></span> <span data-ttu-id="152b3-160">此方式能讓您輕鬆地套用網路安全性群組 (NSG) 規則，並以將表格路由至個別層。</span><span class="sxs-lookup"><span data-stu-id="152b3-160">That makes it easy to apply network security group (NSG) rules and route tables to individual tiers.</span></span>

<span data-ttu-id="152b3-161">Web 和商務層為無狀態。</span><span class="sxs-lookup"><span data-stu-id="152b3-161">The web and business tiers are stateless.</span></span> <span data-ttu-id="152b3-162">任何 VM 可以處理該層的任何要求。</span><span class="sxs-lookup"><span data-stu-id="152b3-162">Any VM can handle any request for that tier.</span></span> <span data-ttu-id="152b3-163">資料層應該包含複寫的資料庫。</span><span class="sxs-lookup"><span data-stu-id="152b3-163">The data tier should consist of a replicated database.</span></span> <span data-ttu-id="152b3-164">對於 Windows，我們建議使用 SQL Server，利用 Always On 可用性群組來獲得高可用性。</span><span class="sxs-lookup"><span data-stu-id="152b3-164">For Windows, we recommend SQL Server, using Always On Availability Groups for high availability.</span></span> <span data-ttu-id="152b3-165">對於 Linux，請選擇支援複寫的資料庫，例如 Apache Cassandra。</span><span class="sxs-lookup"><span data-stu-id="152b3-165">For Linux, choose a database that supports replication, such as Apache Cassandra.</span></span>

<span data-ttu-id="152b3-166">網路安全性群組 (NSG) 會限制存取每一層。</span><span class="sxs-lookup"><span data-stu-id="152b3-166">Network Security Groups (NSGs) restrict access to each tier.</span></span> <span data-ttu-id="152b3-167">例如，資料庫層只會允許來自商務層的存取。</span><span class="sxs-lookup"><span data-stu-id="152b3-167">For example, the database tier only allows access from the business tier.</span></span>

<span data-ttu-id="152b3-168">如需有關在 Azure 上執行多層式架構 (N-tier) 應用程式的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="152b3-168">For more information about running N-tier applications on Azure:</span></span>

- <span data-ttu-id="152b3-169">[執行適用於多層式應用程式的 Windows VM][n-tier-windows]</span><span class="sxs-lookup"><span data-stu-id="152b3-169">[Run Windows VMs for an N-tier application][n-tier-windows]</span></span>
- <span data-ttu-id="152b3-170">[Azure 上具有 SQL Server 的 Windows 多層式架構 (N-tier) 應用程式][n-tier-linux]</span><span class="sxs-lookup"><span data-stu-id="152b3-170">[Windows N-tier application on Azure with SQL Server][n-tier-linux]</span></span>
- [<span data-ttu-id="152b3-171">Microsoft Learn 模組：導覽多層式架構樣式</span><span class="sxs-lookup"><span data-stu-id="152b3-171">Microsoft Learn module: Tour the N-tier architecture style</span></span>](/learn/modules/n-tier-architecture/)

### <a name="additional-considerations"></a><span data-ttu-id="152b3-172">其他考量</span><span class="sxs-lookup"><span data-stu-id="152b3-172">Additional considerations</span></span>

- <span data-ttu-id="152b3-173">多層式架構架構並不限於三層。</span><span class="sxs-lookup"><span data-stu-id="152b3-173">N-tier architectures are not restricted to three tiers.</span></span> <span data-ttu-id="152b3-174">對於更複雜的應用程式，通常會有更多層。</span><span class="sxs-lookup"><span data-stu-id="152b3-174">For more complex applications, it is common to have more tiers.</span></span> <span data-ttu-id="152b3-175">在此情況下，請考慮使用 7 層路由，以將要求路由至特定層。</span><span class="sxs-lookup"><span data-stu-id="152b3-175">In that case, consider using layer-7 routing to route requests to a particular tier.</span></span>

- <span data-ttu-id="152b3-176">層是延展性、可靠性和安全性的界限。</span><span class="sxs-lookup"><span data-stu-id="152b3-176">Tiers are the boundary of scalability, reliability, and security.</span></span> <span data-ttu-id="152b3-177">考慮對這些區域中具有個別需求的服務有個別的層。</span><span class="sxs-lookup"><span data-stu-id="152b3-177">Consider having separate tiers for services with different requirements in those areas.</span></span>

- <span data-ttu-id="152b3-178">使用 VM 擴展集來自動調整。</span><span class="sxs-lookup"><span data-stu-id="152b3-178">Use VM Scale Sets for autoscaling.</span></span>

- <span data-ttu-id="152b3-179">在架構中尋找您可以使用受控服務，而不需進行重大重構的位置。</span><span class="sxs-lookup"><span data-stu-id="152b3-179">Look for places in the architecture where you can use a managed service without significant refactoring.</span></span> <span data-ttu-id="152b3-180">特別是，查看快取、傳訊、儲存體和資料庫。</span><span class="sxs-lookup"><span data-stu-id="152b3-180">In particular, look at caching, messaging, storage, and databases.</span></span>

- <span data-ttu-id="152b3-181">為獲得較高安全性，請在應用程式前方放置網路 DMZ。</span><span class="sxs-lookup"><span data-stu-id="152b3-181">For higher security, place a network DMZ in front of the application.</span></span> <span data-ttu-id="152b3-182">DMZ 包含實作安全性功能 (例如防火牆和封包檢查) 的網路虛擬裝置 (NVA)。</span><span class="sxs-lookup"><span data-stu-id="152b3-182">The DMZ includes network virtual appliances (NVAs) that implement security functionality such as firewalls and packet inspection.</span></span> <span data-ttu-id="152b3-183">如需詳細資訊，請參閱[網路 DMZ 參考架構][dmz]。</span><span class="sxs-lookup"><span data-stu-id="152b3-183">For more information, see [Network DMZ reference architecture][dmz].</span></span>

- <span data-ttu-id="152b3-184">為獲得高可用性，請將兩個或多個 NVA 放置在可用性設定組，加上一個外部負載平衡器在執行個體間散發網際網路要求。</span><span class="sxs-lookup"><span data-stu-id="152b3-184">For high availability, place two or more NVAs in an availability set, with an external load balancer to distribute Internet requests across the instances.</span></span> <span data-ttu-id="152b3-185">如需詳細資訊，請參閱[部署高可用性的網路虛擬裝置][ha-nva]。</span><span class="sxs-lookup"><span data-stu-id="152b3-185">For more information, see [Deploy highly available network virtual appliances][ha-nva].</span></span>

- <span data-ttu-id="152b3-186">請勿允許直接使用 RDP 或 SSH 存取執行應用程式碼的 VM。</span><span class="sxs-lookup"><span data-stu-id="152b3-186">Do not allow direct RDP or SSH access to VMs that are running application code.</span></span> <span data-ttu-id="152b3-187">相反地，操作員必須登入 Jumpbox，也稱為防禦主機。</span><span class="sxs-lookup"><span data-stu-id="152b3-187">Instead, operators should log into a jumpbox, also called a bastion host.</span></span> <span data-ttu-id="152b3-188">這是網路上系統管理員用來連線到其他 VM 的安全 VM。</span><span class="sxs-lookup"><span data-stu-id="152b3-188">This is a VM on the network that administrators use to connect to the other VMs.</span></span> <span data-ttu-id="152b3-189">Jumpbox 具有的 NSG 可允許僅來自核准的公用 IP 位址的 RDP 或 SSH。</span><span class="sxs-lookup"><span data-stu-id="152b3-189">The jumpbox has an NSG that allows RDP or SSH only from approved public IP addresses.</span></span>

- <span data-ttu-id="152b3-190">您可以使用站對站虛擬私人網路 (VPN) 或 Azure ExpressRoute，將 Azure 虛擬網路延伸至您的內部部署網路。</span><span class="sxs-lookup"><span data-stu-id="152b3-190">You can extend the Azure virtual network to your on-premises network using a site-to-site virtual private network (VPN) or Azure ExpressRoute.</span></span> <span data-ttu-id="152b3-191">如需詳細資訊，請參閱[混合式網路參考架構][hybrid-network]。</span><span class="sxs-lookup"><span data-stu-id="152b3-191">For more information, see [Hybrid network reference architecture][hybrid-network].</span></span>

- <span data-ttu-id="152b3-192">如果您的組織使用 Active Directory 來管理身分識別，您可以將 Active Directory 環境延伸至 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="152b3-192">If your organization uses Active Directory to manage identity, you may want to extend your Active Directory environment to the Azure VNet.</span></span> <span data-ttu-id="152b3-193">如需詳細資訊，請參閱[身分識別管理參考架構][identity]。</span><span class="sxs-lookup"><span data-stu-id="152b3-193">For more information, see [Identity management reference architecture][identity].</span></span>

- <span data-ttu-id="152b3-194">如果您需要比適用於 VM 的 Azure SLA 所提供更高的可用性，請跨兩個區域複寫應用程式並使用 Azure 流量管理員進行容錯移轉。</span><span class="sxs-lookup"><span data-stu-id="152b3-194">If you need higher availability than the Azure SLA for VMs provides, replicate the application across two regions and use Azure Traffic Manager for failover.</span></span> <span data-ttu-id="152b3-195">如需詳細資訊，請參閱[在多個區域中執行 Windows VM][multiregion-windows] 或[在多個區域中執行 Linux VM][multiregion-linux]。</span><span class="sxs-lookup"><span data-stu-id="152b3-195">For more information, see [Run Windows VMs in multiple regions][multiregion-windows] or [Run Linux VMs in multiple regions][multiregion-linux].</span></span>

[autoscaling]: ../../best-practices/auto-scaling.md
[caching]: ../../best-practices/caching.md
[dmz]: ../../reference-architectures/dmz/index.md
[ha-nva]: ../../reference-architectures/dmz/nva-ha.md
[hybrid-network]: ../../reference-architectures/hybrid-networking/index.md
[identity]: ../../reference-architectures/identity/index.md
[multiregion-linux]: ../../reference-architectures/virtual-machines-linux/multi-region-application.md
[multiregion-windows]: ../../reference-architectures/virtual-machines-windows/multi-region-application.md
[n-tier-linux]: ../../reference-architectures/virtual-machines-linux/n-tier.md
[n-tier-windows]: ../../reference-architectures/virtual-machines-windows/n-tier.md
[sql-always-on]: /sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server
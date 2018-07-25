---
title: 在 Azure 中實作中樞輪輻網路拓撲與共用服務
description: 如何在 Azure 中實作中樞輪輻網路拓撲與共用服務。
author: telmosampaio
ms.date: 06/19/2018
pnp.series.title: Implement a hub-spoke network topology with shared services in Azure
pnp.series.prev: hub-spoke
ms.openlocfilehash: 283251d5b11f76985405410c5c237e5a64ee98fe
ms.sourcegitcommit: 71cbef121c40ef36e2d6e3a088cb85c4260599b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060790"
---
# <a name="implement-a-hub-spoke-network-topology-with-shared-services-in-azure"></a><span data-ttu-id="f3ad8-103">在 Azure 中實作中樞輪輻網路拓撲與共用服務</span><span class="sxs-lookup"><span data-stu-id="f3ad8-103">Implement a hub-spoke network topology with shared services in Azure</span></span>

<span data-ttu-id="f3ad8-104">此參考架構是建置在[中樞輪輻][guidance-hub-spoke]參考架構基礎上，以包含可以被所有輪輻取用之中樞中的共用服務。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-104">This reference architecture builds on the [hub-spoke][guidance-hub-spoke] reference architecture to include shared services in the hub that can be consumed by all spokes.</span></span> <span data-ttu-id="f3ad8-105">作為將資料中心移轉至雲端的第一步，並且建置[虛擬資料中心]，您需要共用的首要服務是身分識別和安全性。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-105">As a first step toward migrating a datacenter to the cloud, and building a [virtual datacenter], the first services you need to share are identity and security.</span></span> <span data-ttu-id="f3ad8-106">此參考架構說明如何從您的內部部署資料中心將 Active Directory 服務擴充至 Azure，以及如何在中樞輪輻拓撲中新增可作為防火牆的網路虛擬設備 (NVA)。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-106">This reference architecture shows you how to extend your Active Directory services from your on-premises datacenter to Azure, and how to add a network virtual appliance (NVA) that can act as a firewall, in a hub-spoke topology.</span></span>  <span data-ttu-id="f3ad8-107">[**部署這個解決方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-107">[**Deploy this solution**](#deploy-the-solution).</span></span>

<span data-ttu-id="f3ad8-108">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="f3ad8-108">![[0]][0]</span></span>

<span data-ttu-id="f3ad8-109">*下載這個架構的 [Visio 檔案][visio-download]*</span><span class="sxs-lookup"><span data-stu-id="f3ad8-109">*Download a [Visio file][visio-download] of this architecture*</span></span>

<span data-ttu-id="f3ad8-110">此拓撲的優點包括：</span><span class="sxs-lookup"><span data-stu-id="f3ad8-110">The benefits of this topology include:</span></span>

* <span data-ttu-id="f3ad8-111">**節省成本**：將可由多個工作負載共用的服務 (例如網路虛擬裝置 (NVA) 和 DNS 伺服器) 集中在單一位置。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-111">**Cost savings** by centralizing services that can be shared by multiple workloads, such as network virtual appliances (NVAs) and DNS servers, in a single location.</span></span>
* <span data-ttu-id="f3ad8-112">**克服訂用帳戶限制**：將不同訂用帳戶中的 VNet 對等互連到中心中樞。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-112">**Overcome subscriptions limits** by peering VNets from different subscriptions to the central hub.</span></span>
* <span data-ttu-id="f3ad8-113">**關注點分離**：中央 IT (SecOps、InfraOps) 和工作負載 (DevOps)。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-113">**Separation of concerns** between central IT (SecOps, InfraOps) and workloads (DevOps).</span></span>

<span data-ttu-id="f3ad8-114">此架構的一般使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="f3ad8-114">Typical uses for this architecture include:</span></span>

* <span data-ttu-id="f3ad8-115">在不同環境 (例如開發、測試和生產環境) 下部署，且需要共用服務 (例如 DNS、IDS、NTP 或 AD DS) 的工作負載。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-115">Workloads deployed in different environments, such as development, testing, and production, that require shared services such as DNS, IDS, NTP, or AD DS.</span></span> <span data-ttu-id="f3ad8-116">系統會將共用服務置於中樞 VNet，同時將每個環境部署至輪輻以維持隔離。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-116">Shared services are placed in the hub VNet, while each environment is deployed to a spoke to maintain isolation.</span></span>
* <span data-ttu-id="f3ad8-117">不需要彼此連線，但需要存取共用服務的工作負載。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-117">Workloads that do not require connectivity to each other, but require access to shared services.</span></span>
* <span data-ttu-id="f3ad8-118">需要集中控制安全性層面 (例如，在中樞當作 DMZ 的防火牆)，並對每個輪輻中的工作負載進行分離管理的企業。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-118">Enterprises that require central control over security aspects, such as a firewall in the hub as a DMZ, and segregated management for the workloads in each spoke.</span></span>

## <a name="architecture"></a><span data-ttu-id="f3ad8-119">架構</span><span class="sxs-lookup"><span data-stu-id="f3ad8-119">Architecture</span></span>

<span data-ttu-id="f3ad8-120">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-120">The architecture consists of the following components.</span></span>

* <span data-ttu-id="f3ad8-121">**內部部署網路**。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-121">**On-premises network**.</span></span> <span data-ttu-id="f3ad8-122">在組織內執行的私用區域網路。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-122">A private local-area network running within an organization.</span></span>

* <span data-ttu-id="f3ad8-123">**VPN 裝置**。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-123">**VPN device**.</span></span> <span data-ttu-id="f3ad8-124">可對內部部署網路提供外部連線的裝置或服務。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-124">A device or service that provides external connectivity to the on-premises network.</span></span> <span data-ttu-id="f3ad8-125">VPN 裝置可能是硬體裝置或軟體解決方案，例如，Windows Server 2012 中的路由及遠端存取服務 (RRAS)。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-125">The VPN device may be a hardware device, or a software solution such as the Routing and Remote Access Service (RRAS) in Windows Server 2012.</span></span> <span data-ttu-id="f3ad8-126">如需支援的 VPN 設備清單，以及設定選取的 VPN 設備以連線至 Azure 的相關資訊，請參閱[關於站對站 VPN 閘道連線的 VPN 裝置][vpn-appliance]。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-126">For a list of supported VPN appliances and information on configuring selected VPN appliances for connecting to Azure, see [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance].</span></span>

* <span data-ttu-id="f3ad8-127">**VPN 虛擬網路閘道或 ExpressRoute 閘道**。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-127">**VPN virtual network gateway or ExpressRoute gateway**.</span></span> <span data-ttu-id="f3ad8-128">虛擬網路閘道可讓 VNet 連線到與內部部署網路連線所使用的 VPN 裝置或 ExpressRoute 線路。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-128">The virtual network gateway enables the VNet to connect to the VPN device, or ExpressRoute circuit, used for connectivity with your on-premises network.</span></span> <span data-ttu-id="f3ad8-129">如需詳細資訊，請參閱[將內部部署網路連線至 Microsoft Azure 虛擬網路][connect-to-an-Azure-vnet]。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-129">For more information, see [Connect an on-premises network to a Microsoft Azure virtual network][connect-to-an-Azure-vnet].</span></span>

> [!NOTE]
> <span data-ttu-id="f3ad8-130">此參考架構的部署指令碼會使用 VPN 閘道進行連線，並使用 Azure 中的 VNet 模擬內部部署網路。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-130">The deployment scripts for this reference architecture use a VPN gateway for connectivity, and a VNet in Azure to simulate your on-premises network.</span></span>

* <span data-ttu-id="f3ad8-131">**中樞 VNet**。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-131">**Hub VNet**.</span></span> <span data-ttu-id="f3ad8-132">在中樞輪輻拓撲當作中樞使用的 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-132">Azure VNet used as the hub in the hub-spoke topology.</span></span> <span data-ttu-id="f3ad8-133">中樞是內部部署網路的連線中心點，也是裝載可由輪輻 VNet 中裝載的不同工作負載使用之服務的位置。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-133">The hub is the central point of connectivity to your on-premises network, and a place to host services that can be consumed by the different workloads hosted in the spoke VNets.</span></span>

* <span data-ttu-id="f3ad8-134">**閘道子網路**。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-134">**Gateway subnet**.</span></span> <span data-ttu-id="f3ad8-135">虛擬網路閘道會保留在相同的子網路中。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-135">The virtual network gateways are held in the same subnet.</span></span>

* <span data-ttu-id="f3ad8-136">**共用服務子網路**。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-136">**Shared services subnet**.</span></span> <span data-ttu-id="f3ad8-137">中樞 VNet 中用來裝載可在所有輪輻間共用之服務 (例如 DNS 或 AD DS) 的子網路。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-137">A subnet in the hub VNet used to host services that can be shared among all spokes, such as DNS or AD DS.</span></span>

* <span data-ttu-id="f3ad8-138">**DMZ 子網路**。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-138">**DMZ subnet**.</span></span> <span data-ttu-id="f3ad8-139">中樞 VNet 中的子網路，用來裝載可以作為安全性設備 (例如防火牆) 的 NVA。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-139">A subnet in the hub VNet used to host NVAs that can act as security appliances, such as firewalls.</span></span>

* <span data-ttu-id="f3ad8-140">**輪輻 VNet**。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-140">**Spoke VNets**.</span></span> <span data-ttu-id="f3ad8-141">在中樞輪輻拓撲中用來當作輪輻的一個或多個 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-141">One or more Azure VNets that are used as spokes in the hub-spoke topology.</span></span> <span data-ttu-id="f3ad8-142">輪輻可在自己的 VNet 中，用來隔離從其他輪輻分開管理的工作負載。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-142">Spokes can be used to isolate workloads in their own VNets, managed separately from other spokes.</span></span> <span data-ttu-id="f3ad8-143">每個工作負載在透過 Azure 負載平衡器連線多個子網路的情況下，都可能包括多層。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-143">Each workload might include multiple tiers, with multiple subnets connected through Azure load balancers.</span></span> <span data-ttu-id="f3ad8-144">如需有關應用程式基礎結構的詳細資訊，請參閱[執行 Windows VM 工作負載][windows-vm-ra]和[執行 Linux VM 工作負載][linux-vm-ra]。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-144">For more information about the application infrastructure, see [Running Windows VM workloads][windows-vm-ra] and [Running Linux VM workloads][linux-vm-ra].</span></span>

* <span data-ttu-id="f3ad8-145">**VNet 對等互連**。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-145">**VNet peering**.</span></span> <span data-ttu-id="f3ad8-146">在相同的 Azure 區域中，可以使用[對等連線][vnet-peering]來連線兩個 VNet。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-146">Two VNets in the same Azure region can be connected using a [peering connection][vnet-peering].</span></span> <span data-ttu-id="f3ad8-147">對等連線是 VNet 之間不可轉移的低延遲連線。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-147">Peering connections are non-transitive, low latency connections between VNets.</span></span> <span data-ttu-id="f3ad8-148">因此，一旦對等互連之後，VNet 就會使用 Azure 骨幹交換流量，而不需要使用路由器。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-148">Once peered, the VNets exchange traffic by using the Azure backbone, without the need for a router.</span></span> <span data-ttu-id="f3ad8-149">在中樞輪輻網路拓撲中，您可以使用 VNet 對等互連，將中樞連線至每個輪輻。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-149">In a hub-spoke network topology, you use VNet peering to connect the hub to each spoke.</span></span>

> [!NOTE]
> <span data-ttu-id="f3ad8-150">本文僅涵蓋 [Resource Manager](/azure/azure-resource-manager/resource-group-overview) 部署，但是您也可以在相同的訂用帳戶中，將傳統 VNet 連線至 Resource Manager VNet。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-150">This article only covers [Resource Manager](/azure/azure-resource-manager/resource-group-overview) deployments, but you can also connect a classic VNet to a Resource Manager VNet in the same subscription.</span></span> <span data-ttu-id="f3ad8-151">如此一來，您的輪輻就可以裝載傳統部署，而且仍然受益於中樞中共用的服務。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-151">That way, your spokes can host classic deployments and still benefit from services shared in the hub.</span></span>

## <a name="recommendations"></a><span data-ttu-id="f3ad8-152">建議</span><span class="sxs-lookup"><span data-stu-id="f3ad8-152">Recommendations</span></span>

<span data-ttu-id="f3ad8-153">適用於[中樞輪輻][guidance-hub-spoke]參考架構的所有建議也會套用至共用服務參考架構。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-153">All the recommendations for the [hub-spoke][guidance-hub-spoke] reference architecture also apply to the shared services reference architecture.</span></span> 

<span data-ttu-id="f3ad8-154">此外，下列建議適用於共用服務底下大部分的案例。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-154">Also, the following recommendations apply for most scenarios under shared services.</span></span> <span data-ttu-id="f3ad8-155">除非您有特定的需求會覆寫它們，否則請遵循下列建議。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-155">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="identity"></a><span data-ttu-id="f3ad8-156">身分識別</span><span class="sxs-lookup"><span data-stu-id="f3ad8-156">Identity</span></span>

<span data-ttu-id="f3ad8-157">大部分企業組織在其內部部署資料中心有 Active Directory 目錄服務 (ADDS) 環境。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-157">Most enterprise organizations have an Active Directory Directory Services (ADDS) environment in their on-premises datacenter.</span></span> <span data-ttu-id="f3ad8-158">若要將從依據 ADDS 之內部部署網路移到 Azure 的資產管理簡化，建議在 Azure 中裝載 ADDS 網域控制站。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-158">To facilitate management of assets moved to Azure from your on-premises network that depend on ADDS, it is recommended to host ADDS domain controllers in Azure.</span></span>

<span data-ttu-id="f3ad8-159">如果您使用群組原則物件，想要分別控制 Azure 和內部部署環境，請為每個 Azure 區域使用不同的 AD 站台。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-159">If you make use of Group Policy Objects, that you want to control separately for Azure and your on-premises environment, use a different AD site for each Azure region.</span></span> <span data-ttu-id="f3ad8-160">將您的網域控制站放在相依工作負載可存取的中央 VNet (中樞)。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-160">Place your domain controllers in a central VNet (hub) that dependent workloads can access.</span></span>

### <a name="security"></a><span data-ttu-id="f3ad8-161">安全性</span><span class="sxs-lookup"><span data-stu-id="f3ad8-161">Security</span></span>

<span data-ttu-id="f3ad8-162">當您從內部部署環境將工作負載移至 Azure 時，部分工作負載必須裝載在 VM 中。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-162">As you move workloads from your on-premises environment to Azure, some of these workloads will require to be hosted in VMs.</span></span> <span data-ttu-id="f3ad8-163">基於相容性因素，您可能需要對周遊這些工作負載的流量強制執行限制。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-163">For compliance reasons, you may need to enforce restrictions on traffic traversing those workloads.</span></span> 

<span data-ttu-id="f3ad8-164">您可以在 Azure 中使用網路虛擬設備 (NVA) 以裝載不同類型的安全性和效能服務。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-164">You can use network virtual appliances (NVAs) in Azure to host different types of security and performance services.</span></span> <span data-ttu-id="f3ad8-165">如果您很熟悉現今一組特定的設備，建議在適用時於 Azure 中使用相同的虛擬設備。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-165">If you are familiar with a given set of appliances on-premises today, it is recommended to use the same virtualized appliances in Azure, where applicable.</span></span>

> [!NOTE]
> <span data-ttu-id="f3ad8-166">此參考架構的部署指令碼會使用已啟用 IP 轉送的 Ubuntu VM 來模擬網路虛擬設備。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-166">The deployment scripts for this reference architecture use an Ubuntu VM with IP forwarding enabled to mimic a network virtual appliance.</span></span>

## <a name="considerations"></a><span data-ttu-id="f3ad8-167">考量</span><span class="sxs-lookup"><span data-stu-id="f3ad8-167">Considerations</span></span>

### <a name="overcoming-vnet-peering-limits"></a><span data-ttu-id="f3ad8-168">克服 VNet 對等互連的限制</span><span class="sxs-lookup"><span data-stu-id="f3ad8-168">Overcoming VNet peering limits</span></span>

<span data-ttu-id="f3ad8-169">請確定您有在 Azure 中考慮[每個 VNet 的 VNet 對等互連數目限制][vnet-peering-limit]。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-169">Make sure you consider the [limitation on number of VNets peerings per VNet][vnet-peering-limit] in Azure.</span></span> <span data-ttu-id="f3ad8-170">如果您決定您需要的輪輻比允許的限制還多，請考慮建立中樞輪輻中樞輪輻拓撲，其中輪輻的第一層也可作為中樞。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-170">If you decide you need more spokes than the limit will allow, consider creating a hub-spoke-hub-spoke topology, where the first level of spokes also act as hubs.</span></span> <span data-ttu-id="f3ad8-171">下圖顯示這個方法。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-171">The following diagram shows this approach.</span></span>

<span data-ttu-id="f3ad8-172">![[3]][3]</span><span class="sxs-lookup"><span data-stu-id="f3ad8-172">![[3]][3]</span></span>

<span data-ttu-id="f3ad8-173">此外，請考慮要在中樞共用哪些服務，以確保中樞可針對大量輪輻進行調整。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-173">Also consider what services are shared in the hub, to ensure the hub scales for a larger number of spokes.</span></span> <span data-ttu-id="f3ad8-174">例如，如果您的中樞提供防火牆服務，請在新增多個輪輻時，考慮防火牆解決方案的頻寬限制。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-174">For instance, if your hub provides firewall services, consider the bandwidth limits of your firewall solution when adding multiple spokes.</span></span> <span data-ttu-id="f3ad8-175">您可以將其中部分共用服務移到中樞的第二層。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-175">You might want to move some of these shared services to a second level of hubs.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="f3ad8-176">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="f3ad8-176">Deploy the solution</span></span>

<span data-ttu-id="f3ad8-177">適用於此架構的部署可在 [GitHub][ref-arch-repo] 上取得。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-177">A deployment for this architecture is available on [GitHub][ref-arch-repo].</span></span> <span data-ttu-id="f3ad8-178">此部署會在您的訂用帳戶中建立下列資源群組︰</span><span class="sxs-lookup"><span data-stu-id="f3ad8-178">The deployment creates the following resource groups in your subscription:</span></span>

- <span data-ttu-id="f3ad8-179">hub-adds-rg</span><span class="sxs-lookup"><span data-stu-id="f3ad8-179">hub-adds-rg</span></span>
- <span data-ttu-id="f3ad8-180">hub-nva-rg</span><span class="sxs-lookup"><span data-stu-id="f3ad8-180">hub-nva-rg</span></span>
- <span data-ttu-id="f3ad8-181">hub-vnet-rg</span><span class="sxs-lookup"><span data-stu-id="f3ad8-181">hub-vnet-rg</span></span>
- <span data-ttu-id="f3ad8-182">onprem-vnet-rg</span><span class="sxs-lookup"><span data-stu-id="f3ad8-182">onprem-vnet-rg</span></span>
- <span data-ttu-id="f3ad8-183">spoke1-vnet-rg</span><span class="sxs-lookup"><span data-stu-id="f3ad8-183">spoke1-vnet-rg</span></span>
- <span data-ttu-id="f3ad8-184">spoke2-vent-rg</span><span class="sxs-lookup"><span data-stu-id="f3ad8-184">spoke2-vent-rg</span></span>

<span data-ttu-id="f3ad8-185">範本參數檔案會參照這些名稱，因此，如果您變更這些名稱，請據以更新參數檔案。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-185">The template parameter files refer to these names, so if you change them, update the parameter files to match.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="f3ad8-186">必要條件</span><span class="sxs-lookup"><span data-stu-id="f3ad8-186">Prerequisites</span></span>

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-the-simulated-on-premises-datacenter-using-azbb"></a><span data-ttu-id="f3ad8-187">使用 azbb 部署模擬的內部部署資料中心</span><span class="sxs-lookup"><span data-stu-id="f3ad8-187">Deploy the simulated on-premises datacenter using azbb</span></span>

<span data-ttu-id="f3ad8-188">此步驟將模擬的內部部署資料中心部署為 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-188">This step deploys the simulated on-premises datacenter as an Azure VNet.</span></span>

1. <span data-ttu-id="f3ad8-189">巡覽至 GitHub 存放庫的 `hybrid-networking\shared-services-stack\` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-189">Navigate to the `hybrid-networking\shared-services-stack\` folder of the GitHub repository.</span></span>

2. <span data-ttu-id="f3ad8-190">開啟 `onprem.json` 檔案。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-190">Open the `onprem.json` file.</span></span> 

3. <span data-ttu-id="f3ad8-191">搜尋 `Password` 和 `adminPassword` 的所有執行個體。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-191">Search for all instances of `Password` and `adminPassword`.</span></span> <span data-ttu-id="f3ad8-192">在參數中輸入使用者名稱和密碼的值，並儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-192">Enter values for the user name and password in the parameters and save the file.</span></span> 

4. <span data-ttu-id="f3ad8-193">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f3ad8-193">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g onprem-vnet-rg -l <location> -p onprem.json --deploy
   ```
5. <span data-ttu-id="f3ad8-194">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-194">Wait for the deployment to finish.</span></span> <span data-ttu-id="f3ad8-195">此部署會建立一個虛擬網路、一個執行 Windows 的虛擬機器，以及一個 VPN 閘道。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-195">This deployment creates a virtual network, a virtual machine running Windows, and a VPN gateway.</span></span> <span data-ttu-id="f3ad8-196">建立 VPN 閘道可能需要超過 40 分鐘才能完成。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-196">The VPN gateway creation can take more than 40 minutes to complete.</span></span>

### <a name="deploy-the-hub-vnet"></a><span data-ttu-id="f3ad8-197">部署中樞 VNet</span><span class="sxs-lookup"><span data-stu-id="f3ad8-197">Deploy the hub VNet</span></span>

<span data-ttu-id="f3ad8-198">此步驟部署中樞 VNet 並將其連接至模擬的內部部署 VNet。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-198">This step deploys the hub VNet and connects it to the simulated on-premises VNet.</span></span>

1. <span data-ttu-id="f3ad8-199">開啟 `hub-vnet.json` 檔案。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-199">Open the `hub-vnet.json` file.</span></span> 

2. <span data-ttu-id="f3ad8-200">搜尋 `adminPassword` 並輸入參數中的使用者名稱和密碼。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-200">Search for `adminPassword` and enter a user name and password in the parameters.</span></span> 

3. <span data-ttu-id="f3ad8-201">搜尋 `sharedKey` 的所有執行個體，並輸入共用金鑰的值。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-201">Search for all instances of `sharedKey` and enter a value for a shared key.</span></span> <span data-ttu-id="f3ad8-202">儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-202">Save the file.</span></span>

   ```bash
   "sharedKey": "abc123",
   ```

4. <span data-ttu-id="f3ad8-203">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f3ad8-203">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-vnet-rg -l <location> -p hub-vnet.json --deploy
   ```

5. <span data-ttu-id="f3ad8-204">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-204">Wait for the deployment to finish.</span></span> <span data-ttu-id="f3ad8-205">此部署會建立一個虛擬網路、一個虛擬機器、一個 VPN 閘道，以及一個與上節中建立之閘道的連線。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-205">This deployment creates a virtual network, a virtual machine, a VPN gateway, and a connection to the gateway created in the previous section.</span></span> <span data-ttu-id="f3ad8-206">VPN 閘道可能需要超過 40 分鐘才能完成。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-206">The VPN gateway can take more than 40 minutes to complete.</span></span>

### <a name="deploy-ad-ds-in-azure"></a><span data-ttu-id="f3ad8-207">在 Azure 中部署 AD DS</span><span class="sxs-lookup"><span data-stu-id="f3ad8-207">Deploy AD DS in Azure</span></span>

<span data-ttu-id="f3ad8-208">此步驟將在 Azure 中部署 AD DS 網域控制站。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-208">This step deploys AD DS domain controllers in Azure.</span></span>

1. <span data-ttu-id="f3ad8-209">開啟 `hub-adds.json` 檔案。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-209">Open the `hub-adds.json` file.</span></span>

2. <span data-ttu-id="f3ad8-210">搜尋 `Password` 和 `adminPassword` 的所有執行個體。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-210">Search for all instances of `Password` and `adminPassword`.</span></span> <span data-ttu-id="f3ad8-211">在參數中輸入使用者名稱和密碼的值，並儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-211">Enter values for the user name and password in the parameters and save the file.</span></span> 

3. <span data-ttu-id="f3ad8-212">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f3ad8-212">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-adds-rg -l <location> -p hub-adds.json --deploy
   ```
  
<span data-ttu-id="f3ad8-213">此部署步驟可能需要幾分鐘的時間，因為它將兩個 VM 加入模擬內部部署資料中心託管的網域，並在其上安裝 AD DS。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-213">This deployment step may take several minutes, because it joins the two VMs to the domain hosted in the simulated on-premises datacenter, and installs AD DS on them.</span></span>

### <a name="deploy-the-spoke-vnets"></a><span data-ttu-id="f3ad8-214">部署輪輻 VNet</span><span class="sxs-lookup"><span data-stu-id="f3ad8-214">Deploy the spoke VNets</span></span>

<span data-ttu-id="f3ad8-215">此步驟部署支點 Vnet。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-215">This step deploys the spoke VNets.</span></span>

1. <span data-ttu-id="f3ad8-216">開啟 `spoke1.json` 檔案。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-216">Open the `spoke1.json` file.</span></span>

2. <span data-ttu-id="f3ad8-217">搜尋 `adminPassword` 並輸入參數中的使用者名稱和密碼。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-217">Search for `adminPassword` and enter a user name and password in the parameters.</span></span> 

3. <span data-ttu-id="f3ad8-218">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f3ad8-218">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g spoke1-vnet-rg -l <location> -p spoke1.json --deploy
   ```
  
4. <span data-ttu-id="f3ad8-219">對 `spoke2.json` 檔案重複步驟 1 和 2。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-219">Repeat steps 1 and 2 for the file `spoke2.json`.</span></span>

5. <span data-ttu-id="f3ad8-220">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f3ad8-220">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g spoke2-vnet-rg -l <location> -p spoke2.json --deploy
   ```

### <a name="peer-the-hub-vnet-to-the-spoke-vnets"></a><span data-ttu-id="f3ad8-221">將中樞 VNet 對等到支點 Vnet</span><span class="sxs-lookup"><span data-stu-id="f3ad8-221">Peer the hub VNet to the spoke VNets</span></span>

<span data-ttu-id="f3ad8-222">若要建立從中樞 VNet 到支點 VNet 的對等互連連線，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="f3ad8-222">To create a peering connection from the hub VNet to the spoke VNets, run the following command:</span></span>

```bash
azbb -s <subscription_id> -g hub-vnet-rg -l <location> -p hub-vnet-peering.json --deploy
```

### <a name="deploy-the-nva"></a><span data-ttu-id="f3ad8-223">部署 NVA</span><span class="sxs-lookup"><span data-stu-id="f3ad8-223">Deploy the NVA</span></span>

<span data-ttu-id="f3ad8-224">此步驟會在 `dmz` 子網路中部署 NVA。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-224">This step deploys an NVA in the `dmz` subnet.</span></span>

1. <span data-ttu-id="f3ad8-225">開啟 `hub-nva.json` 檔案。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-225">Open the `hub-nva.json` file.</span></span>

2. <span data-ttu-id="f3ad8-226">搜尋 `adminPassword` 並輸入參數中的使用者名稱和密碼。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-226">Search for `adminPassword` and enter a user name and password in the parameters.</span></span> 

3. <span data-ttu-id="f3ad8-227">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f3ad8-227">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-nva-rg -l <location> -p hub-nva.json --deploy
   ```

### <a name="test-connectivity"></a><span data-ttu-id="f3ad8-228">測試連線能力</span><span class="sxs-lookup"><span data-stu-id="f3ad8-228">Test connectivity</span></span> 

<span data-ttu-id="f3ad8-229">測試從模擬的內部部署環境到中樞 VNet 的連線。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-229">Test conectivity from the simulated on-premises environment to the hub VNet.</span></span>

1. <span data-ttu-id="f3ad8-230">使用 Azure 入口網站尋找 `onprem-jb-rg` 資源群組中名為 `jb-vm1` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-230">Use the Azure portal to find the VM named `jb-vm1` in the `onprem-jb-rg` resource group.</span></span>

2. <span data-ttu-id="f3ad8-231">按一下 `Connect` 以開啟 VM 的遠端桌面工作階段。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-231">Click `Connect` to open a remote desktop session to the VM.</span></span> <span data-ttu-id="f3ad8-232">請使用您在 `onprem.json` 參數檔案中指定的密碼。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-232">Use the password that you specified in the `onprem.json` parameter file.</span></span>

3. <span data-ttu-id="f3ad8-233">在 VM 中開啟 PowerShell 主控台，並使用 `Test-NetConnection` Cmdlet 確認您可以連線至中樞 VNet 中的 jumpbox VM。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-233">Open a PowerShell console in the VM, and use the `Test-NetConnection` cmdlet to verify that you can connect to the jumpbox VM in the hub VNet.</span></span>

   ```powershell
   Test-NetConnection 10.0.0.68 -CommonTCPPort RDP
   ```
<span data-ttu-id="f3ad8-234">輸出應如下所示：</span><span class="sxs-lookup"><span data-stu-id="f3ad8-234">The output should look similar to the following:</span></span>

```powershell
ComputerName     : 10.0.0.68
RemoteAddress    : 10.0.0.68
RemotePort       : 3389
InterfaceAlias   : Ethernet 2
SourceAddress    : 192.168.1.000
TcpTestSucceeded : True
```

> [!NOTE]
> <span data-ttu-id="f3ad8-235">根據預設，Windows Server VM 在 Azure 中不允許 ICMP 回應。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-235">By default, Windows Server VMs do not allow ICMP responses in Azure.</span></span> <span data-ttu-id="f3ad8-236">如果您想要使用 `ping` 來測試連線，您需要在 Windows 進階防火牆中為每個 VM 啟用 ICMP 流量。</span><span class="sxs-lookup"><span data-stu-id="f3ad8-236">If you want to use `ping` to test connectivity, you need to enable ICMP traffic in the Windows Advanced Firewall for each VM.</span></span>

<span data-ttu-id="f3ad8-237">重複執行相同的步驟來測試與支點 VNet 的連接性：</span><span class="sxs-lookup"><span data-stu-id="f3ad8-237">Repeat the sames steps to test connectivity to the spoke VNets:</span></span>

```powershell
Test-NetConnection 10.1.0.68 -CommonTCPPort RDP
Test-NetConnection 10.2.0.68 -CommonTCPPort RDP
```


<!-- links -->

[azure-cli-2]: /azure/install-azure-cli
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[guidance-hub-spoke]: ./hub-spoke.md
[azure-vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[best-practices-security]: /azure/best-practices-network-securit
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[guidance-expressroute]: ./expressroute.md
[guidance-vpn]: ./vpn.md
[linux-vm-ra]: ../virtual-machines-linux/index.md
[hybrid-ha]: ./expressroute-vpn-failover.md
[naming conventions]: /azure/guidance/guidance-naming-conventions
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[虛擬資料中心]: https://aka.ms/vdc
[virtual datacenter]: https://aka.ms/vdc
[vnet-peering]: /azure/virtual-network/virtual-network-peering-overview
[vnet-peering-limit]: /azure/azure-subscription-service-limits#networking-limits
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[windows-vm-ra]: ../virtual-machines-windows/index.md

[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-hub-spoke.vsdx
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[0]: ./images/shared-services.png "Azure 中的共用服務拓撲"
[3]: ./images/hub-spokehub-spoke.svg "Azure 中的中樞輪輻中樞輪輻拓撲"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/

---
title: 在 Azure 中實作中樞輪輻網路拓撲與共用服務
description: 如何在 Azure 中實作中樞輪輻網路拓撲與共用服務。
author: telmosampaio
ms.date: 02/25/2018
pnp.series.title: Implement a hub-spoke network topology with shared services in Azure
pnp.series.prev: hub-spoke
ms.openlocfilehash: 83367a3be2f7a1e33c2ef7018d42f70aae99104d
ms.sourcegitcommit: f665226cec96ec818ca06ac6c2d83edb23c9f29c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="implement-a-hub-spoke-network-topology-with-shared-services-in-azure"></a><span data-ttu-id="20d2a-103">在 Azure 中實作中樞輪輻網路拓撲與共用服務</span><span class="sxs-lookup"><span data-stu-id="20d2a-103">Implement a hub-spoke network topology with shared services in Azure</span></span>

<span data-ttu-id="20d2a-104">此參考架構是建置在[中樞輪輻][guidance-hub-spoke]參考架構基礎上，以包含可以被所有輪輻取用之中樞中的共用服務。</span><span class="sxs-lookup"><span data-stu-id="20d2a-104">This reference architecture builds on the [hub-spoke][guidance-hub-spoke] reference architecture to include shared services in the hub that can be consumed by all spokes.</span></span> <span data-ttu-id="20d2a-105">作為將資料中心移轉至雲端的第一步，並且建置[虛擬資料中心]，您需要共用的首要服務是身分識別和安全性。</span><span class="sxs-lookup"><span data-stu-id="20d2a-105">As a first step toward migrating a datacenter to the cloud, and building a [virtual datacenter], the first services you need to share are identity and security.</span></span> <span data-ttu-id="20d2a-106">此參考架構說明如何從您的內部部署資料中心將 Active Directory 服務擴充至 Azure，以及如何在中樞輪輻拓撲中新增可作為防火牆的網路虛擬設備 (NVA)。</span><span class="sxs-lookup"><span data-stu-id="20d2a-106">This reference architecture shows you how to extend your Active Directory services from your on-premises datacenter to Azure, and how to add a network virtual appliance (NVA) that can act as a firewall, in a hub-spoke topology.</span></span>  <span data-ttu-id="20d2a-107">[**部署這個解決方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="20d2a-107">[**Deploy this solution**](#deploy-the-solution).</span></span>

<span data-ttu-id="20d2a-108">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="20d2a-108">![[0]][0]</span></span>

<span data-ttu-id="20d2a-109">*下載這個架構的 [Visio 檔案][visio-download]*</span><span class="sxs-lookup"><span data-stu-id="20d2a-109">*Download a [Visio file][visio-download] of this architecture*</span></span>

<span data-ttu-id="20d2a-110">此拓撲的優點包括：</span><span class="sxs-lookup"><span data-stu-id="20d2a-110">The benefits of this topology include:</span></span>

* <span data-ttu-id="20d2a-111">**節省成本**：將可由多個工作負載共用的服務 (例如網路虛擬裝置 (NVA) 和 DNS 伺服器) 集中在單一位置。</span><span class="sxs-lookup"><span data-stu-id="20d2a-111">**Cost savings** by centralizing services that can be shared by multiple workloads, such as network virtual appliances (NVAs) and DNS servers, in a single location.</span></span>
* <span data-ttu-id="20d2a-112">**克服訂用帳戶限制**：將不同訂用帳戶中的 VNet 對等互連到中心中樞。</span><span class="sxs-lookup"><span data-stu-id="20d2a-112">**Overcome subscriptions limits** by peering VNets from different subscriptions to the central hub.</span></span>
* <span data-ttu-id="20d2a-113">**關注點分離**：中央 IT (SecOps、InfraOps) 和工作負載 (DevOps)。</span><span class="sxs-lookup"><span data-stu-id="20d2a-113">**Separation of concerns** between central IT (SecOps, InfraOps) and workloads (DevOps).</span></span>

<span data-ttu-id="20d2a-114">此架構的一般使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="20d2a-114">Typical uses for this architecture include:</span></span>

* <span data-ttu-id="20d2a-115">在不同環境 (例如開發、測試和生產環境) 下部署，且需要共用服務 (例如 DNS、IDS、NTP 或 AD DS) 的工作負載。</span><span class="sxs-lookup"><span data-stu-id="20d2a-115">Workloads deployed in different environments, such as development, testing, and production, that require shared services such as DNS, IDS, NTP, or AD DS.</span></span> <span data-ttu-id="20d2a-116">系統會將共用服務置於中樞 VNet，同時將每個環境部署至輪輻以維持隔離。</span><span class="sxs-lookup"><span data-stu-id="20d2a-116">Shared services are placed in the hub VNet, while each environment is deployed to a spoke to maintain isolation.</span></span>
* <span data-ttu-id="20d2a-117">不需要彼此連線，但需要存取共用服務的工作負載。</span><span class="sxs-lookup"><span data-stu-id="20d2a-117">Workloads that do not require connectivity to each other, but require access to shared services.</span></span>
* <span data-ttu-id="20d2a-118">需要集中控制安全性層面 (例如，在中樞當作 DMZ 的防火牆)，並對每個輪輻中的工作負載進行分離管理的企業。</span><span class="sxs-lookup"><span data-stu-id="20d2a-118">Enterprises that require central control over security aspects, such as a firewall in the hub as a DMZ, and segregated management for the workloads in each spoke.</span></span>

## <a name="architecture"></a><span data-ttu-id="20d2a-119">架構</span><span class="sxs-lookup"><span data-stu-id="20d2a-119">Architecture</span></span>

<span data-ttu-id="20d2a-120">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="20d2a-120">The architecture consists of the following components.</span></span>

* <span data-ttu-id="20d2a-121">**內部部署網路**。</span><span class="sxs-lookup"><span data-stu-id="20d2a-121">**On-premises network**.</span></span> <span data-ttu-id="20d2a-122">在組織內執行的私用區域網路。</span><span class="sxs-lookup"><span data-stu-id="20d2a-122">A private local-area network running within an organization.</span></span>

* <span data-ttu-id="20d2a-123">**VPN 裝置**。</span><span class="sxs-lookup"><span data-stu-id="20d2a-123">**VPN device**.</span></span> <span data-ttu-id="20d2a-124">可對內部部署網路提供外部連線的裝置或服務。</span><span class="sxs-lookup"><span data-stu-id="20d2a-124">A device or service that provides external connectivity to the on-premises network.</span></span> <span data-ttu-id="20d2a-125">VPN 裝置可能是硬體裝置或軟體解決方案，例如，Windows Server 2012 中的路由及遠端存取服務 (RRAS)。</span><span class="sxs-lookup"><span data-stu-id="20d2a-125">The VPN device may be a hardware device, or a software solution such as the Routing and Remote Access Service (RRAS) in Windows Server 2012.</span></span> <span data-ttu-id="20d2a-126">如需支援的 VPN 設備清單，以及設定選取的 VPN 設備以連線至 Azure 的相關資訊，請參閱[關於站對站 VPN 閘道連線的 VPN 裝置][vpn-appliance]。</span><span class="sxs-lookup"><span data-stu-id="20d2a-126">For a list of supported VPN appliances and information on configuring selected VPN appliances for connecting to Azure, see [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance].</span></span>

* <span data-ttu-id="20d2a-127">**VPN 虛擬網路閘道或 ExpressRoute 閘道**。</span><span class="sxs-lookup"><span data-stu-id="20d2a-127">**VPN virtual network gateway or ExpressRoute gateway**.</span></span> <span data-ttu-id="20d2a-128">虛擬網路閘道可讓 VNet 連線到與內部部署網路連線所使用的 VPN 裝置或 ExpressRoute 線路。</span><span class="sxs-lookup"><span data-stu-id="20d2a-128">The virtual network gateway enables the VNet to connect to the VPN device, or ExpressRoute circuit, used for connectivity with your on-premises network.</span></span> <span data-ttu-id="20d2a-129">如需詳細資訊，請參閱[將內部部署網路連線至 Microsoft Azure 虛擬網路][connect-to-an-Azure-vnet]。</span><span class="sxs-lookup"><span data-stu-id="20d2a-129">For more information, see [Connect an on-premises network to a Microsoft Azure virtual network][connect-to-an-Azure-vnet].</span></span>

> [!NOTE]
> <span data-ttu-id="20d2a-130">此參考架構的部署指令碼會使用 VPN 閘道進行連線，並使用 Azure 中的 VNet 模擬內部部署網路。</span><span class="sxs-lookup"><span data-stu-id="20d2a-130">The deployment scripts for this reference architecture use a VPN gateway for connectivity, and a VNet in Azure to simulate your on-premises network.</span></span>

* <span data-ttu-id="20d2a-131">**中樞 VNet**。</span><span class="sxs-lookup"><span data-stu-id="20d2a-131">**Hub VNet**.</span></span> <span data-ttu-id="20d2a-132">在中樞輪輻拓撲當作中樞使用的 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="20d2a-132">Azure VNet used as the hub in the hub-spoke topology.</span></span> <span data-ttu-id="20d2a-133">中樞是內部部署網路的連線中心點，也是裝載可由輪輻 VNet 中裝載的不同工作負載使用之服務的位置。</span><span class="sxs-lookup"><span data-stu-id="20d2a-133">The hub is the central point of connectivity to your on-premises network, and a place to host services that can be consumed by the different workloads hosted in the spoke VNets.</span></span>

* <span data-ttu-id="20d2a-134">**閘道子網路**。</span><span class="sxs-lookup"><span data-stu-id="20d2a-134">**Gateway subnet**.</span></span> <span data-ttu-id="20d2a-135">虛擬網路閘道會保留在相同的子網路中。</span><span class="sxs-lookup"><span data-stu-id="20d2a-135">The virtual network gateways are held in the same subnet.</span></span>

* <span data-ttu-id="20d2a-136">**共用服務子網路**。</span><span class="sxs-lookup"><span data-stu-id="20d2a-136">**Shared services subnet**.</span></span> <span data-ttu-id="20d2a-137">中樞 VNet 中用來裝載可在所有輪輻間共用之服務 (例如 DNS 或 AD DS) 的子網路。</span><span class="sxs-lookup"><span data-stu-id="20d2a-137">A subnet in the hub VNet used to host services that can be shared among all spokes, such as DNS or AD DS.</span></span>

* <span data-ttu-id="20d2a-138">**DMZ 子網路**。</span><span class="sxs-lookup"><span data-stu-id="20d2a-138">**DMZ subnet**.</span></span> <span data-ttu-id="20d2a-139">中樞 VNet 中的子網路，用來裝載可以作為安全性設備 (例如防火牆) 的 NVA。</span><span class="sxs-lookup"><span data-stu-id="20d2a-139">A subnet in the hub VNet used to host NVAs that can act as security appliances, such as firewalls.</span></span>

* <span data-ttu-id="20d2a-140">**輪輻 VNet**。</span><span class="sxs-lookup"><span data-stu-id="20d2a-140">**Spoke VNets**.</span></span> <span data-ttu-id="20d2a-141">在中樞輪輻拓撲中用來當作輪輻的一個或多個 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="20d2a-141">One or more Azure VNets that are used as spokes in the hub-spoke topology.</span></span> <span data-ttu-id="20d2a-142">輪輻可在自己的 VNet 中，用來隔離從其他輪輻分開管理的工作負載。</span><span class="sxs-lookup"><span data-stu-id="20d2a-142">Spokes can be used to isolate workloads in their own VNets, managed separately from other spokes.</span></span> <span data-ttu-id="20d2a-143">每個工作負載在透過 Azure 負載平衡器連線多個子網路的情況下，都可能包括多層。</span><span class="sxs-lookup"><span data-stu-id="20d2a-143">Each workload might include multiple tiers, with multiple subnets connected through Azure load balancers.</span></span> <span data-ttu-id="20d2a-144">如需有關應用程式基礎結構的詳細資訊，請參閱[執行 Windows VM 工作負載][windows-vm-ra]和[執行 Linux VM 工作負載][linux-vm-ra]。</span><span class="sxs-lookup"><span data-stu-id="20d2a-144">For more information about the application infrastructure, see [Running Windows VM workloads][windows-vm-ra] and [Running Linux VM workloads][linux-vm-ra].</span></span>

* <span data-ttu-id="20d2a-145">**VNet 對等互連**。</span><span class="sxs-lookup"><span data-stu-id="20d2a-145">**VNet peering**.</span></span> <span data-ttu-id="20d2a-146">在相同的 Azure 區域中，可以使用[對等連線][vnet-peering]來連線兩個 VNet。</span><span class="sxs-lookup"><span data-stu-id="20d2a-146">Two VNets in the same Azure region can be connected using a [peering connection][vnet-peering].</span></span> <span data-ttu-id="20d2a-147">對等連線是 VNet 之間不可轉移的低延遲連線。</span><span class="sxs-lookup"><span data-stu-id="20d2a-147">Peering connections are non-transitive, low latency connections between VNets.</span></span> <span data-ttu-id="20d2a-148">因此，一旦對等互連之後，VNet 就會使用 Azure 骨幹交換流量，而不需要使用路由器。</span><span class="sxs-lookup"><span data-stu-id="20d2a-148">Once peered, the VNets exchange traffic by using the Azure backbone, without the need for a router.</span></span> <span data-ttu-id="20d2a-149">在中樞輪輻網路拓撲中，您可以使用 VNet 對等互連，將中樞連線至每個輪輻。</span><span class="sxs-lookup"><span data-stu-id="20d2a-149">In a hub-spoke network topology, you use VNet peering to connect the hub to each spoke.</span></span>

> [!NOTE]
> <span data-ttu-id="20d2a-150">本文僅涵蓋 [Resource Manager](/azure/azure-resource-manager/resource-group-overview) 部署，但是您也可以在相同的訂用帳戶中，將傳統 VNet 連線至 Resource Manager VNet。</span><span class="sxs-lookup"><span data-stu-id="20d2a-150">This article only covers [Resource Manager](/azure/azure-resource-manager/resource-group-overview) deployments, but you can also connect a classic VNet to a Resource Manager VNet in the same subscription.</span></span> <span data-ttu-id="20d2a-151">如此一來，您的輪輻就可以裝載傳統部署，而且仍然受益於中樞中共用的服務。</span><span class="sxs-lookup"><span data-stu-id="20d2a-151">That way, your spokes can host classic deployments and still benefit from services shared in the hub.</span></span>

## <a name="recommendations"></a><span data-ttu-id="20d2a-152">建議</span><span class="sxs-lookup"><span data-stu-id="20d2a-152">Recommendations</span></span>

<span data-ttu-id="20d2a-153">適用於[中樞輪輻][guidance-hub-spoke]參考架構的所有建議也會套用至共用服務參考架構。</span><span class="sxs-lookup"><span data-stu-id="20d2a-153">All the recommendations for the [hub-spoke][guidance-hub-spoke] reference architecture also apply to the shared services reference architecture.</span></span> 

<span data-ttu-id="20d2a-154">此外，下列建議適用於共用服務底下大部分的案例。</span><span class="sxs-lookup"><span data-stu-id="20d2a-154">ALso, the following recommendations apply for most scenarios under shared services.</span></span> <span data-ttu-id="20d2a-155">除非您有特定的需求會覆寫它們，否則請遵循下列建議。</span><span class="sxs-lookup"><span data-stu-id="20d2a-155">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="identity"></a><span data-ttu-id="20d2a-156">身分識別</span><span class="sxs-lookup"><span data-stu-id="20d2a-156">Identity</span></span>

<span data-ttu-id="20d2a-157">大部分企業組織在其內部部署資料中心有 Active Directory 目錄服務 (ADDS) 環境。</span><span class="sxs-lookup"><span data-stu-id="20d2a-157">Most enterprise organizations have an Active Directory Directory Services (ADDS) environment in their on-premises datacenter.</span></span> <span data-ttu-id="20d2a-158">若要將從依據 ADDS 之內部部署網路移到 Azure 的資產管理簡化，建議在 Azure 中裝載 ADDS 網域控制站。</span><span class="sxs-lookup"><span data-stu-id="20d2a-158">To facilitate management of assets moved to Azure from your on-premises network that depend on ADDS, it is recommended to host ADDS domain controllers in Azure.</span></span>

<span data-ttu-id="20d2a-159">如果您使用群組原則物件，想要分別控制 Azure 和內部部署環境，請為每個 Azure 區域使用不同的 AD 站台。</span><span class="sxs-lookup"><span data-stu-id="20d2a-159">If you make use of Group Policy Objects, that you want to control separately for Azure and your on-premises environment, use a different AD site for each Azure region.</span></span> <span data-ttu-id="20d2a-160">將您的網域控制站放在相依工作負載可存取的中央 VNet (中樞)。</span><span class="sxs-lookup"><span data-stu-id="20d2a-160">Place your domain controllers in a central VNet (hub) that dependent workloads can access.</span></span>

### <a name="security"></a><span data-ttu-id="20d2a-161">安全性</span><span class="sxs-lookup"><span data-stu-id="20d2a-161">Security</span></span>

<span data-ttu-id="20d2a-162">當您從內部部署環境將工作負載移至 Azure 時，部分工作負載必須裝載在 VM 中。</span><span class="sxs-lookup"><span data-stu-id="20d2a-162">As you move workloads from your on-premises environment to Azure, some of these workloads will require to be hosted in VMs.</span></span> <span data-ttu-id="20d2a-163">基於相容性因素，您可能需要對周遊這些工作負載的流量強制執行限制。</span><span class="sxs-lookup"><span data-stu-id="20d2a-163">For compliance reasons, you may need to enforce restrictions on traffic traversing those workloads.</span></span> 

<span data-ttu-id="20d2a-164">您可以在 Azure 中使用網路虛擬設備 (NVA) 以裝載不同類型的安全性和效能服務。</span><span class="sxs-lookup"><span data-stu-id="20d2a-164">You can use network virtual appliances (NVAs) in Azure to host different types of security and performance services.</span></span> <span data-ttu-id="20d2a-165">如果您很熟悉現今一組特定的設備，建議在適用時於 Azure 中使用相同的虛擬設備。</span><span class="sxs-lookup"><span data-stu-id="20d2a-165">If you are familiar with a given set of appliances on-premises today, it is recommended to use the same virtualized appliances in Azure, where applicable.</span></span>

> [!NOTE]
> <span data-ttu-id="20d2a-166">此參考架構的部署指令碼會使用已啟用 IP 轉送的 Ubuntu VM 來模擬網路虛擬設備。</span><span class="sxs-lookup"><span data-stu-id="20d2a-166">The deployment scripts for this reference architecture use an Ubuntu VM with IP forwarding enabled to mimic a network virtual appliance.</span></span>

## <a name="considerations"></a><span data-ttu-id="20d2a-167">考量</span><span class="sxs-lookup"><span data-stu-id="20d2a-167">Considerations</span></span>

### <a name="overcoming-vnet-peering-limits"></a><span data-ttu-id="20d2a-168">克服 VNet 對等互連的限制</span><span class="sxs-lookup"><span data-stu-id="20d2a-168">Overcoming VNet peering limits</span></span>

<span data-ttu-id="20d2a-169">請確定您有在 Azure 中考慮[每個 VNet 的 VNet 對等互連數目限制][vnet-peering-limit]。</span><span class="sxs-lookup"><span data-stu-id="20d2a-169">Make sure you consider the [limitation on number of VNets peerings per VNet][vnet-peering-limit] in Azure.</span></span> <span data-ttu-id="20d2a-170">如果您決定您需要的輪輻比允許的限制還多，請考慮建立中樞輪輻中樞輪輻拓撲，其中輪輻的第一層也可作為中樞。</span><span class="sxs-lookup"><span data-stu-id="20d2a-170">If you decide you need more spokes than the limit will allow, consider creating a hub-spoke-hub-spoke topology, where the first level of spokes also act as hubs.</span></span> <span data-ttu-id="20d2a-171">下圖顯示這個方法。</span><span class="sxs-lookup"><span data-stu-id="20d2a-171">The following diagram shows this approach.</span></span>

<span data-ttu-id="20d2a-172">![[3]][3]</span><span class="sxs-lookup"><span data-stu-id="20d2a-172">![[3]][3]</span></span>

<span data-ttu-id="20d2a-173">此外，請考慮要在中樞共用哪些服務，以確保中樞可針對大量輪輻進行調整。</span><span class="sxs-lookup"><span data-stu-id="20d2a-173">Also consider what services are shared in the hub, to ensure the hub scales for a larger number of spokes.</span></span> <span data-ttu-id="20d2a-174">例如，如果您的中樞提供防火牆服務，請在新增多個輪輻時，考慮防火牆解決方案的頻寬限制。</span><span class="sxs-lookup"><span data-stu-id="20d2a-174">For instance, if your hub provides firewall services, consider the bandwidth limits of your firewall solution when adding multiple spokes.</span></span> <span data-ttu-id="20d2a-175">您可以將其中部分共用服務移到中樞的第二層。</span><span class="sxs-lookup"><span data-stu-id="20d2a-175">You might want to move some of these shared services to a second level of hubs.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="20d2a-176">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="20d2a-176">Deploy the solution</span></span>

<span data-ttu-id="20d2a-177">適用於此架構的部署可在 [GitHub][ref-arch-repo] 上取得。</span><span class="sxs-lookup"><span data-stu-id="20d2a-177">A deployment for this architecture is available on [GitHub][ref-arch-repo].</span></span> <span data-ttu-id="20d2a-178">它會使用每個 VNet 中的 Ubuntu VM 測試連線。</span><span class="sxs-lookup"><span data-stu-id="20d2a-178">It uses Ubuntu VMs in each VNet to test connectivity.</span></span> <span data-ttu-id="20d2a-179">**中樞 VNet** 的**共用服務**子網路中沒有裝載任何實際的服務。</span><span class="sxs-lookup"><span data-stu-id="20d2a-179">There are no actual services hosted in the **shared-services** subnet in the **hub VNet**.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="20d2a-180">先決條件</span><span class="sxs-lookup"><span data-stu-id="20d2a-180">Prerequisites</span></span>

<span data-ttu-id="20d2a-181">在您可以將參考架構部署到自己的訂用帳戶之前，必須執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="20d2a-181">Before you can deploy the reference architecture to your own subscription, you must perform the following steps.</span></span>

1. <span data-ttu-id="20d2a-182">複製、派生或下載適用於[參考架構][ref-arch-repo] GitHub 存放庫的 zip 檔案。</span><span class="sxs-lookup"><span data-stu-id="20d2a-182">Clone, fork, or download the zip file for the [reference architectures][ref-arch-repo] GitHub repository.</span></span>

2. <span data-ttu-id="20d2a-183">確定您已在電腦上安裝 Azure CLI 2.0。</span><span class="sxs-lookup"><span data-stu-id="20d2a-183">Make sure you have the Azure CLI 2.0 installed on your computer.</span></span> <span data-ttu-id="20d2a-184">如需 CLI 安裝指示，請參閱[安裝 Azure CLI 2.0][azure-cli-2]。</span><span class="sxs-lookup"><span data-stu-id="20d2a-184">For CLI installation instructions, see [Install Azure CLI 2.0][azure-cli-2].</span></span>

3. <span data-ttu-id="20d2a-185">安裝 [Azure 建置組塊][azbb] npm 封裝。</span><span class="sxs-lookup"><span data-stu-id="20d2a-185">Install the [Azure building blocks][azbb] npm package.</span></span>

4. <span data-ttu-id="20d2a-186">從命令提示字元、bash 提示字元或 PowerShell 提示字元中，使用下列命令登入 Azure 帳戶，並遵循提示進行。</span><span class="sxs-lookup"><span data-stu-id="20d2a-186">From a command prompt, bash prompt, or PowerShell prompt, login to your Azure account by using the command below, and follow the prompts.</span></span>

   ```bash
   az login
   ```

### <a name="deploy-the-simulated-on-premises-datacenter-using-azbb"></a><span data-ttu-id="20d2a-187">使用 azbb 部署模擬的內部部署資料中心</span><span class="sxs-lookup"><span data-stu-id="20d2a-187">Deploy the simulated on-premises datacenter using azbb</span></span>

<span data-ttu-id="20d2a-188">若要將模擬的內部部署資料中心部署為 Azure VNet，請遵循下列步驟：</span><span class="sxs-lookup"><span data-stu-id="20d2a-188">To deploy the simulated on-premises datacenter as an Azure VNet, follow these steps:</span></span>

1. <span data-ttu-id="20d2a-189">瀏覽至您在上述必要條件步驟中所下載存放庫的 `hybrid-networking\shared-services-stack\` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="20d2a-189">Navigate to the `hybrid-networking\shared-services-stack\` folder for the repository you downloaded in the pre-requisites step above.</span></span>

2. <span data-ttu-id="20d2a-190">開啟 `onprem.json` 檔案，然後在第 45 和 46 行的引號之間輸入使用者名稱和密碼，如下所示，然後儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="20d2a-190">Open the `onprem.json` file and enter a username and password between the quotes in line 45 and 46, as shown below, then save the file.</span></span>

   ```bash
   "adminUsername": "XXX",
   "adminPassword": "YYY",
   ```

3. <span data-ttu-id="20d2a-191">執行 `azbb` 以部署模擬的內部部署環境，如下所示。</span><span class="sxs-lookup"><span data-stu-id="20d2a-191">Run `azbb` to deploy the simulated onprem environment as shown below.</span></span>

   ```bash
   azbb -s <subscription_id> -g onprem-vnet-rg - l <location> -p onoprem.json --deploy
   ```
   > [!NOTE]
   > <span data-ttu-id="20d2a-192">如果您決定使用不同的資源群組名稱 (非 `onprem-vnet-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。</span><span class="sxs-lookup"><span data-stu-id="20d2a-192">If you decide to use a different resource group name (other than `onprem-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

4. <span data-ttu-id="20d2a-193">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="20d2a-193">Wait for the deployment to finish.</span></span> <span data-ttu-id="20d2a-194">此部署會建立一個虛擬網路、一個執行 Windows 的虛擬機器，以及一個 VPN 閘道。</span><span class="sxs-lookup"><span data-stu-id="20d2a-194">This deployment creates a virtual network, a virtual machine running Windows, and a VPN gateway.</span></span> <span data-ttu-id="20d2a-195">建立 VPN 閘道可能需要超過 40 分鐘才能完成。</span><span class="sxs-lookup"><span data-stu-id="20d2a-195">The VPN gateway creation can take more than 40 minutes to complete.</span></span>

### <a name="azure-hub-vnet"></a><span data-ttu-id="20d2a-196">Azure 中樞 VNet</span><span class="sxs-lookup"><span data-stu-id="20d2a-196">Azure hub VNet</span></span>

<span data-ttu-id="20d2a-197">若要部署中樞 VNet 並連線到以上所建立的模擬內部部署 VNet，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="20d2a-197">To deploy the hub VNet, and connect to the simulated on-premises VNet created above, perform the following steps.</span></span>

1. <span data-ttu-id="20d2a-198">開啟 `hub-vnet.json` 檔案，然後在第 50 和 51 行的引號之間輸入使用者名稱和密碼，如下所示。</span><span class="sxs-lookup"><span data-stu-id="20d2a-198">Open the `hub-vnet.json` file and enter a username and password between the quotes in line 50 and 51, as shown below.</span></span>

   ```bash
   "adminUsername": "XXX",
   "adminPassword": "YYY",
   ```

2. <span data-ttu-id="20d2a-199">在第 52 行上，針對 `osType` 輸入 `Windows` 或 `Linux` 以安裝 Windows Server 2016 Datacenter 或 Ubuntu 16.04 作為 jumpbox 的作業系統。</span><span class="sxs-lookup"><span data-stu-id="20d2a-199">On line 52, for `osType`, type `Windows` or `Linux` to install either Windows Server 2016 Datacenter, or Ubuntu 16.04 as the operating system for the jumpbox.</span></span>

3. <span data-ttu-id="20d2a-200">在第 83 行的引號之間輸入共用金鑰，如下所示，然後儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="20d2a-200">Enter a shared key between the quotes in line 83, as shown below, then save the file.</span></span>

   ```bash
   "sharedKey": "",
   ```

4. <span data-ttu-id="20d2a-201">執行 `azbb` 以部署模擬的內部部署環境，如下所示。</span><span class="sxs-lookup"><span data-stu-id="20d2a-201">Run `azbb` to deploy the simulated onprem environment as shown below.</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-vnet-rg - l <location> -p hub-vnet.json --deploy
   ```
   > [!NOTE]
   > <span data-ttu-id="20d2a-202">如果您決定使用不同的資源群組名稱 (非 `hub-vnet-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。</span><span class="sxs-lookup"><span data-stu-id="20d2a-202">If you decide to use a different resource group name (other than `hub-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

5. <span data-ttu-id="20d2a-203">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="20d2a-203">Wait for the deployment to finish.</span></span> <span data-ttu-id="20d2a-204">此部署會建立一個虛擬網路、一個虛擬機器、一個 VPN 閘道，以及一個與上節中建立之閘道的連線。</span><span class="sxs-lookup"><span data-stu-id="20d2a-204">This deployment creates a virtual network, a virtual machine, a VPN gateway, and a connection to the gateway created in the previous section.</span></span> <span data-ttu-id="20d2a-205">建立 VPN 閘道可能需要超過 40 分鐘才能完成。</span><span class="sxs-lookup"><span data-stu-id="20d2a-205">The VPN gateway creation can take more than 40 minutes to complete.</span></span>

### <a name="adds-in-azure"></a><span data-ttu-id="20d2a-206">在 Azure 中的 ADDS</span><span class="sxs-lookup"><span data-stu-id="20d2a-206">ADDS in Azure</span></span>

<span data-ttu-id="20d2a-207">若要在 Azure 中部署 ADDS 網域控制站，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="20d2a-207">To deploy the ADDS domain controllers in Azure, perform the following steps.</span></span>

1. <span data-ttu-id="20d2a-208">開啟 `hub-adds.json` 檔案，然後在第 14 和 15 行的引號之間輸入使用者名稱和密碼，如下所示，然後儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="20d2a-208">Open the `hub-adds.json` file and enter a username and password between the quotes in lines 14 and 15, as shown below, then save the file.</span></span>

   ```bash
   "adminUsername": "XXX",
   "adminPassword": "YYY",
   ```

2. <span data-ttu-id="20d2a-209">執行 `azbb` 以部署 ADDS 網域控制站，如下所示。</span><span class="sxs-lookup"><span data-stu-id="20d2a-209">Run `azbb` to deploy the ADDS domain controllers as shown below.</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-adds-rg - l <location> -p hub-adds.json --deploy
   ```
  
   > [!NOTE]
   > <span data-ttu-id="20d2a-210">如果您決定使用不同的資源群組名稱 (非 `hub-adds-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。</span><span class="sxs-lookup"><span data-stu-id="20d2a-210">If you decide to use a different resource group name (other than `hub-adds-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

   > [!NOTE]
   > <span data-ttu-id="20d2a-211">這個部分的部署可能需要幾分鐘的時間，因為它需要將兩個 VM 加入裝載於模擬的內部部署資料中心之網域，然後在其上安裝 AD DS。</span><span class="sxs-lookup"><span data-stu-id="20d2a-211">This part of the deployment may take several minutes, since it requires joining the two VMs to the domain hosted int he simulated on-premises datacenter, then installing AD DS on them.</span></span>

### <a name="nva"></a><span data-ttu-id="20d2a-212">NVA</span><span class="sxs-lookup"><span data-stu-id="20d2a-212">NVA</span></span>

<span data-ttu-id="20d2a-213">若要在 `dmz` 子網路中部署 NVA，請執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="20d2a-213">To deploy an NVA in the `dmz` subnet, perform the following steps:</span></span>

1. <span data-ttu-id="20d2a-214">開啟 `hub-nva.json` 檔案，然後在第 13 和 14 行的引號之間輸入使用者名稱和密碼，如下所示，然後儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="20d2a-214">Open the `hub-nva.json` file and enter a username and password between the quotes in lines 13 and 14, as shown below, then save the file.</span></span>

   ```bash
   "adminUsername": "XXX",
   "adminPassword": "YYY",
   ```
2. <span data-ttu-id="20d2a-215">執行 `azbb` 以部署 NVA VM 和使用者定義的路由。</span><span class="sxs-lookup"><span data-stu-id="20d2a-215">Run `azbb` to deploy the NVA VM and user defined routes.</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-nva-rg - l <location> -p hub-nva.json --deploy
   ```
   > [!NOTE]
   > <span data-ttu-id="20d2a-216">如果您決定使用不同的資源群組名稱 (非 `hub-nva-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。</span><span class="sxs-lookup"><span data-stu-id="20d2a-216">If you decide to use a different resource group name (other than `hub-nva-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

### <a name="azure-spoke-vnets"></a><span data-ttu-id="20d2a-217">Azure 輪輻 VNet</span><span class="sxs-lookup"><span data-stu-id="20d2a-217">Azure spoke VNets</span></span>

<span data-ttu-id="20d2a-218">若要部署輪輻 VNet，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="20d2a-218">To deploy the spoke VNets, perform the following steps.</span></span>

1. <span data-ttu-id="20d2a-219">開啟 `spoke1.json` 檔案，然後在第 52 和 53 行的引號之間輸入使用者名稱和密碼，如下所示，然後儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="20d2a-219">Open the `spoke1.json` file and enter a username and password between the quotes in lines 52 and 53, as shown below, then save the file.</span></span>

   ```bash
   "adminUsername": "XXX",
   "adminPassword": "YYY",
   ```

2. <span data-ttu-id="20d2a-220">在第 54 行上，針對 `osType` 輸入 `Windows` 或 `Linux` 以安裝 Windows Server 2016 Datacenter 或 Ubuntu 16.04 作為 jumpbox 的作業系統。</span><span class="sxs-lookup"><span data-stu-id="20d2a-220">On line 54, for `osType`, type `Windows` or `Linux` to install either Windows Server 2016 Datacenter, or Ubuntu 16.04 as the operating system for the jumpbox.</span></span>

3. <span data-ttu-id="20d2a-221">執行 `azbb` 以部署第一個輪輻 VNet 環境，如下所示。</span><span class="sxs-lookup"><span data-stu-id="20d2a-221">Run `azbb` to deploy the first spoke VNet environment as shown below.</span></span>

   ```bash
   azbb -s <subscription_id> -g spoke1-vnet-rg - l <location> -p spoke1.json --deploy
   ```
  
   > [!NOTE]
   > <span data-ttu-id="20d2a-222">如果您決定使用不同的資源群組名稱 (非 `spoke1-vnet-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。</span><span class="sxs-lookup"><span data-stu-id="20d2a-222">If you decide to use a different resource group name (other than `spoke1-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

4. <span data-ttu-id="20d2a-223">對 `spoke2.json` 檔案重複上述的步驟 1。</span><span class="sxs-lookup"><span data-stu-id="20d2a-223">Repeat step 1 above for file `spoke2.json`.</span></span>

5. <span data-ttu-id="20d2a-224">執行 `azbb` 以部署第二個輪輻 VNet 環境，如下所示。</span><span class="sxs-lookup"><span data-stu-id="20d2a-224">Run `azbb` to deploy the second spoke VNet environment as shown below.</span></span>

   ```bash
   azbb -s <subscription_id> -g spoke2-vnet-rg - l <location> -p spoke2.json --deploy
   ```
   > [!NOTE]
   > <span data-ttu-id="20d2a-225">如果您決定使用不同的資源群組名稱 (非 `spoke2-vnet-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。</span><span class="sxs-lookup"><span data-stu-id="20d2a-225">If you decide to use a different resource group name (other than `spoke2-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

### <a name="azure-hub-vnet-peering-to-spoke-vnets"></a><span data-ttu-id="20d2a-226">Azure 中樞 VNet 對等互連至輪輻 VNet</span><span class="sxs-lookup"><span data-stu-id="20d2a-226">Azure hub VNet peering to spoke VNets</span></span>

<span data-ttu-id="20d2a-227">若要建立從中樞 VNet 到輪輻 VNet 的對等互連連線，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="20d2a-227">To create a peering connection from the hub VNet to the spoke VNets, perform the following steps.</span></span>

1. <span data-ttu-id="20d2a-228">開啟 `hub-vnet-peering.json` 檔案並確認從第 29 行開始之每個虛擬網路對等互連的資源群組名稱和虛擬網路名稱正確無誤。</span><span class="sxs-lookup"><span data-stu-id="20d2a-228">Open the `hub-vnet-peering.json` file and verify that the resource group name, and virtual network name for each of the virtual network peerings starting in line 29 are correct.</span></span>

2. <span data-ttu-id="20d2a-229">執行 `azbb` 以部署第一個輪輻 VNet 環境，如下所示。</span><span class="sxs-lookup"><span data-stu-id="20d2a-229">Run `azbb` to deploy the first spoke VNet environment as shown below.</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-vnet-rg - l <location> -p hub-vnet-peering.json --deploy
   ```

   > [!NOTE]
   > <span data-ttu-id="20d2a-230">如果您決定使用不同的資源群組名稱 (非 `hub-vnet-rg`)，請務必搜尋使用該名稱的所有參數檔案，然後加以編輯，以便使用您自己的資源群組名稱。</span><span class="sxs-lookup"><span data-stu-id="20d2a-230">If you decide to use a different resource group name (other than `hub-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

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

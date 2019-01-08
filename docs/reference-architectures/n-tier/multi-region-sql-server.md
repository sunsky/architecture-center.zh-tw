---
title: 適用於高可用性的多重區域多層式架構 (N-tier) 應用程式
titleSuffix: Azure Reference Architectures
description: 在多個區域中的 Azure 虛擬機器上部署應用程式，以具備高可用性和復原能力。
author: MikeWasson
ms.date: 07/19/2018
ms.custom: seodec18
ms.openlocfilehash: 84da8aaef7e552beff1f06befbaa2e50a3ac3d8b
ms.sourcegitcommit: bb7fcffbb41e2c26a26f8781df32825eb60df70c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2018
ms.locfileid: "53643694"
---
# <a name="run-an-n-tier-application-in-multiple-azure-regions-for-high-availability"></a><span data-ttu-id="9d5e2-103">在多個 Azure 區域中執行多層式架構 (N-Tier) 應用程式以獲得高可用性</span><span class="sxs-lookup"><span data-stu-id="9d5e2-103">Run an N-tier application in multiple Azure regions for high availability</span></span>

<span data-ttu-id="9d5e2-104">此參考架構顯示一組經過證實的作法，其可在多個 Azure 區域中執行多層式架構應用程式，以實現可用性和強固的災害復原基礎結構。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-104">This reference architecture shows a set of proven practices for running an N-tier application in multiple Azure regions, in order to achieve availability and a robust disaster recovery infrastructure.</span></span>

![適用於 Azure 多層式架構 (N-Tier) 應用程式的高可用性網路架構"](./images/multi-region-sql-server.png)

<span data-ttu-id="9d5e2-106">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-106">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="9d5e2-107">架構</span><span class="sxs-lookup"><span data-stu-id="9d5e2-107">Architecture</span></span>

<span data-ttu-id="9d5e2-108">此架構係根據[具有 SQL Server 的多層式架構 (N-tier) 應用程式](n-tier-sql-server.md)中顯示的架構而建置的。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-108">This architecture builds on the one shown in [N-tier application with SQL Server](n-tier-sql-server.md).</span></span>

- <span data-ttu-id="9d5e2-109">**主要和次要區域**。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-109">**Primary and secondary regions**.</span></span> <span data-ttu-id="9d5e2-110">使用兩個區域以實現更高的可用性。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-110">Use two regions to achieve higher availability.</span></span> <span data-ttu-id="9d5e2-111">其中一個是主要區域。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-111">One is the primary region.</span></span> <span data-ttu-id="9d5e2-112">另一個則用於容錯移轉。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-112">The other region is for failover.</span></span>

- <span data-ttu-id="9d5e2-113">**Azure 流量管理員**。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-113">**Azure Traffic Manager**.</span></span> <span data-ttu-id="9d5e2-114">[流量管理員][traffic-manager]會將連入要求路由傳送到其中一個區域。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-114">[Traffic Manager][traffic-manager] routes incoming requests to one of the regions.</span></span> <span data-ttu-id="9d5e2-115">正常作業期間，它會將要求路由傳送到主要區域。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-115">During normal operations, it routes requests to the primary region.</span></span> <span data-ttu-id="9d5e2-116">如果該區域變得無法使用，流量管理員就會容錯移轉到次要區域。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-116">If that region becomes unavailable, Traffic Manager fails over to the secondary region.</span></span> <span data-ttu-id="9d5e2-117">如需詳細資訊，請參閱[流量管理員設定](#traffic-manager-configuration)。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-117">For more information, see the section [Traffic Manager configuration](#traffic-manager-configuration).</span></span>

- <span data-ttu-id="9d5e2-118">**資源群組**。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-118">**Resource groups**.</span></span> <span data-ttu-id="9d5e2-119">分別針對主要區域、次要區域及流量管理員建立[資源群組][resource groups]。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-119">Create separate [resource groups][resource groups] for the primary region, the secondary region, and for Traffic Manager.</span></span> <span data-ttu-id="9d5e2-120">這可讓您彈性地將每個區域當作單一資源集合來管理。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-120">This gives you the flexibility to manage each region as a single collection of resources.</span></span> <span data-ttu-id="9d5e2-121">例如，您可以重新部署某一個區域，而不需拿掉另一個區域。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-121">For example, you could redeploy one region, without taking down the other one.</span></span> <span data-ttu-id="9d5e2-122">[連結資源群組][resource-group-links]，如此一來，您就能執行查詢，以列出應用程式的所有資源。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-122">[Link the resource groups][resource-group-links], so that you can run a query to list all the resources for the application.</span></span>

- <span data-ttu-id="9d5e2-123">**VNet**。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-123">**VNets**.</span></span> <span data-ttu-id="9d5e2-124">針對每個區域建立不同的 VNet。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-124">Create a separate VNet for each region.</span></span> <span data-ttu-id="9d5e2-125">確定位址空間並未重疊。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-125">Make sure the address spaces do not overlap.</span></span>

- <span data-ttu-id="9d5e2-126">**SQL Server Always On 可用性群組**。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-126">**SQL Server Always On Availability Group**.</span></span> <span data-ttu-id="9d5e2-127">如果您使用 SQL Server，我們建議使用 [SQL Always On 可用性群組][sql-always-on]來獲得高可用性。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-127">If you are using SQL Server, we recommend [SQL Always On Availability Groups][sql-always-on] for high availability.</span></span> <span data-ttu-id="9d5e2-128">建立單一可用性群組，以包含這兩個區域中的 SQL Server 執行個體。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-128">Create a single availability group that includes the SQL Server instances in both regions.</span></span>

    > [!NOTE]
    > <span data-ttu-id="9d5e2-129">另請考慮 [Azure SQL Database][azure-sql-db]，其可提供關聯式資料庫作為雲端服務。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-129">Also consider [Azure SQL Database][azure-sql-db], which provides a relational database as a cloud service.</span></span> <span data-ttu-id="9d5e2-130">使用 SQL Database 時，您不需要設定可用性群組或管理容錯移轉。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-130">With SQL Database, you don't need to configure an availability group or manage failover.</span></span>
    >

- <span data-ttu-id="9d5e2-131">**VPN 閘道**。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-131">**VPN Gateways**.</span></span> <span data-ttu-id="9d5e2-132">在每個 VNet 中建立 [VPN 閘道][vpn-gateway]，並設定 [VNet 對 VNet 連線][vnet-to-vnet]，以啟用這兩個 VNet 之間的網路流量。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-132">Create a [VPN gateway][vpn-gateway] in each VNet, and configure a [VNet-to-VNet connection][vnet-to-vnet], to enable network traffic between the two VNets.</span></span> <span data-ttu-id="9d5e2-133">這對於 SQL Always On 可用性群組是必要的。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-133">This is required for the SQL Always On Availability Group.</span></span>

## <a name="recommendations"></a><span data-ttu-id="9d5e2-134">建議</span><span class="sxs-lookup"><span data-stu-id="9d5e2-134">Recommendations</span></span>

<span data-ttu-id="9d5e2-135">多區域架構可以提供比部署到單一區域更高的可用性。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-135">A multi-region architecture can provide higher availability than deploying to a single region.</span></span> <span data-ttu-id="9d5e2-136">如果區域中斷會影響主要區域，您可以使用[流量管理員][traffic-manager]容錯移轉到次要區域。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-136">If a regional outage affects the primary region, you can use [Traffic Manager][traffic-manager] to fail over to the secondary region.</span></span> <span data-ttu-id="9d5e2-137">如果應用程式的個別子系統失敗，此架構也可以提供協助。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-137">This architecture can also help if an individual subsystem of the application fails.</span></span>

<span data-ttu-id="9d5e2-138">有數種一般方法可用來跨區域實現高可用性：</span><span class="sxs-lookup"><span data-stu-id="9d5e2-138">There are several general approaches to achieving high availability across regions:</span></span>

- <span data-ttu-id="9d5e2-139">搭配熱待命的主動/被動。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-139">Active/passive with hot standby.</span></span> <span data-ttu-id="9d5e2-140">流量會傳送到其中一個區域，而另一個區域會以熱待命模式等候。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-140">Traffic goes to one region, while the other waits on hot standby.</span></span> <span data-ttu-id="9d5e2-141">熱待命表示次要區域中隨時都已配置 VM 且正在執行中。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-141">Hot standby means the VMs in the secondary region are allocated and running at all times.</span></span>
- <span data-ttu-id="9d5e2-142">搭配冷待命的主動/被動。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-142">Active/passive with cold standby.</span></span> <span data-ttu-id="9d5e2-143">流量會傳送到其中一個區域，而另一個區域會以冷待命模式等候。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-143">Traffic goes to one region, while the other waits on cold standby.</span></span> <span data-ttu-id="9d5e2-144">冷待命表示在需要容錯移轉之前，不會在次要區域中配置 VM。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-144">Cold standby means the VMs in the secondary region are not allocated until needed for failover.</span></span> <span data-ttu-id="9d5e2-145">此方式的執行成本較小，但在失敗發生時通常需要較久的時間才能上線。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-145">This approach costs less to run, but will generally take longer to come online during a failure.</span></span>
- <span data-ttu-id="9d5e2-146">主動/主動。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-146">Active/active.</span></span> <span data-ttu-id="9d5e2-147">兩個區域均為主動，而且要求會在它們之間負載平衡。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-147">Both regions are active, and requests are load balanced between them.</span></span> <span data-ttu-id="9d5e2-148">如果其中一個區域變成無法使用，就會將它從輪替中剔除。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-148">If one region becomes unavailable, it is taken out of rotation.</span></span>

<span data-ttu-id="9d5e2-149">此參考架構著重於搭配熱待命的主動/被動 (使用流量管理員進行容錯移轉)。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-149">This reference architecture focuses on active/passive with hot standby, using Traffic Manager for failover.</span></span> <span data-ttu-id="9d5e2-150">請注意，您可以部署少量的 VM 來進行熱待命，然後視需要相應放大。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-150">Note that you could deploy a small number of VMs for hot standby and then scale out as needed.</span></span>

### <a name="regional-pairing"></a><span data-ttu-id="9d5e2-151">區域配對</span><span class="sxs-lookup"><span data-stu-id="9d5e2-151">Regional pairing</span></span>

<span data-ttu-id="9d5e2-152">每個 Azure 區域都會與相同地理位置內的另一個區域配對。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-152">Each Azure region is paired with another region within the same geography.</span></span> <span data-ttu-id="9d5e2-153">通常會從相同區域配對選擇區域 (例如，美國東部 2 和美國中部)。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-153">In general, choose regions from the same regional pair (for example, East US 2 and US Central).</span></span> <span data-ttu-id="9d5e2-154">這樣做的優點包括：</span><span class="sxs-lookup"><span data-stu-id="9d5e2-154">Benefits of doing so include:</span></span>

- <span data-ttu-id="9d5e2-155">如果發生廣泛的中斷，至少會優先復原每個配對中的一個區域。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-155">If there is a broad outage, recovery of at least one region out of every pair is prioritized.</span></span>
- <span data-ttu-id="9d5e2-156">已計劃的 Azure 系統更新會循序在配對區域中推出，以將可能的停機時間降到最低。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-156">Planned Azure system updates are rolled out to paired regions sequentially, to minimize possible downtime.</span></span>
- <span data-ttu-id="9d5e2-157">配對會位於相同的地理位置，以符合資料落地需求。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-157">Pairs reside within the same geography, to meet data residency requirements.</span></span>

<span data-ttu-id="9d5e2-158">不過，請確定這兩個區域均支援您應用程式所需的所有 Azure 服務 (請參閱[依區域提供的服務][services-by-region])。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-158">However, make sure that both regions support all of the Azure services needed for your application (see [Services by region][services-by-region]).</span></span> <span data-ttu-id="9d5e2-159">如需區域配對的詳細資訊，請參閱[商務持續性和災害復原 (BCDR)：Azure 配對的區域][regional-pairs]。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-159">For more information about regional pairs, see [Business continuity and disaster recovery (BCDR): Azure Paired Regions][regional-pairs].</span></span>

### <a name="traffic-manager-configuration"></a><span data-ttu-id="9d5e2-160">流量管理員設定</span><span class="sxs-lookup"><span data-stu-id="9d5e2-160">Traffic Manager configuration</span></span>

<span data-ttu-id="9d5e2-161">設定流量管理員時，請考慮下列各點：</span><span class="sxs-lookup"><span data-stu-id="9d5e2-161">Consider the following points when configuring Traffic Manager:</span></span>

- <span data-ttu-id="9d5e2-162">**路由**。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-162">**Routing**.</span></span> <span data-ttu-id="9d5e2-163">流量管理員支援數個[路由演算法][tm-routing]。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-163">Traffic Manager supports several [routing algorithms][tm-routing].</span></span> <span data-ttu-id="9d5e2-164">針對本文所述的案例，請使用「優先順序」路由 (先前稱為「容錯移轉」路由)。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-164">For the scenario described in this article, use *priority* routing (formerly called *failover* routing).</span></span> <span data-ttu-id="9d5e2-165">使用此設定，流量管理員會將所有要求傳送到主要區域，除非主要地區變成無法連線。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-165">With this setting, Traffic Manager sends all requests to the primary region, unless the primary region becomes unreachable.</span></span> <span data-ttu-id="9d5e2-166">那時，就會自動容錯移轉到次要區域。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-166">At that point, it automatically fails over to the secondary region.</span></span> <span data-ttu-id="9d5e2-167">請參閱[設定容錯移轉路由方法][tm-configure-failover]。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-167">See [Configure Failover routing method][tm-configure-failover].</span></span>
- <span data-ttu-id="9d5e2-168">**健康情況探查**。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-168">**Health probe**.</span></span> <span data-ttu-id="9d5e2-169">流量管理員會使用 HTTP (或 HTTPS) [探查][tm-monitoring]來監視每個區域的可用性。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-169">Traffic Manager uses an HTTP (or HTTPS) [probe][tm-monitoring] to monitor the availability of each region.</span></span> <span data-ttu-id="9d5e2-170">探查會針對指定的 URL 路徑檢查 HTTP 200 回應。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-170">The probe checks for an HTTP 200 response for a specified URL path.</span></span> <span data-ttu-id="9d5e2-171">最佳作法是，建立端點來報告應用程式的整體健康情況，並使用此端點進行健康情況探查。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-171">As a best practice, create an endpoint that reports the overall health of the application, and use this endpoint for the health probe.</span></span> <span data-ttu-id="9d5e2-172">否則，探查可能會在應用程式的關鍵組件真的失敗時報告端點狀況良好。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-172">Otherwise, the probe might report a healthy endpoint when critical parts of the application are actually failing.</span></span> <span data-ttu-id="9d5e2-173">如需詳細資訊，請參閱[健康情況端點監視模式][health-endpoint-monitoring-pattern]。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-173">For more information, see [Health Endpoint Monitoring Pattern][health-endpoint-monitoring-pattern].</span></span>

<span data-ttu-id="9d5e2-174">當流量管理員容錯移轉時，用戶端會有一段時間無法連線到應用程式。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-174">When Traffic Manager fails over there is a period of time when clients cannot reach the application.</span></span> <span data-ttu-id="9d5e2-175">持續時間會受到下列因素影響：</span><span class="sxs-lookup"><span data-stu-id="9d5e2-175">The duration is affected by the following factors:</span></span>

- <span data-ttu-id="9d5e2-176">健康情況探查必須偵測到主要區域已變成無法連線。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-176">The health probe must detect that the primary region has become unreachable.</span></span>
- <span data-ttu-id="9d5e2-177">DNS 伺服器必須針對 IP 位址更新快取的 DNS 記錄，這取決於 DNS 存留時間 (TTL)。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-177">DNS servers must update the cached DNS records for the IP address, which depends on the DNS time-to-live (TTL).</span></span> <span data-ttu-id="9d5e2-178">預設的 TTL 是 300 秒 (5 分鐘)，但是當您建立流量管理員設定檔時，您可以設定此值。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-178">The default TTL is 300 seconds (5 minutes), but you can configure this value when you create the Traffic Manager profile.</span></span>

<span data-ttu-id="9d5e2-179">如需詳細資料，請參閱[關於流量管理員監視][tm-monitoring]。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-179">For details, see [About Traffic Manager Monitoring][tm-monitoring].</span></span>

<span data-ttu-id="9d5e2-180">如果流量管理員容錯移轉，我們建議執行手動容錯回復，而不是實作自動容錯回復。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-180">If Traffic Manager fails over, we recommend performing a manual failback rather than implementing an automatic failback.</span></span> <span data-ttu-id="9d5e2-181">或者，您可以建立應用程式會在區域之間來回翻轉的情況。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-181">Otherwise, you can create a situation where the application flips back and forth between regions.</span></span> <span data-ttu-id="9d5e2-182">請確認所有的應用程式子系統狀況良好，然後再進行容錯回復。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-182">Verify that all application subsystems are healthy before failing back.</span></span>

<span data-ttu-id="9d5e2-183">請注意，流量管理員預設會自動容錯回復。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-183">Note that Traffic Manager automatically fails back by default.</span></span> <span data-ttu-id="9d5e2-184">若要避免這個問題，請在容錯移轉事件之後，手動降低主要區域的優先順序。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-184">To prevent this, manually lower the priority of the primary region after a failover event.</span></span> <span data-ttu-id="9d5e2-185">例如，假設主要區域是優先順序 1，而次要為優先順序 2。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-185">For example, suppose the primary region is priority 1 and the secondary is priority 2.</span></span> <span data-ttu-id="9d5e2-186">在容錯移轉之後，將主要區域設定為優先順序 3，以防止自動容錯回復。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-186">After a failover, set the primary region to priority 3, to prevent automatic failback.</span></span> <span data-ttu-id="9d5e2-187">當您準備好切換回來時，將優先順序更新為 1。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-187">When you are ready to switch back, update the priority to 1.</span></span>

<span data-ttu-id="9d5e2-188">下列 [Azure CLI][azure-cli] 命令會更新優先順序：</span><span class="sxs-lookup"><span data-stu-id="9d5e2-188">The following [Azure CLI][azure-cli] command updates the priority:</span></span>

```azurecli
az network traffic-manager endpoint update --resource-group <resource-group> --profile-name <profile>
    --name <endpoint-name> --type azureEndpoints --priority 3
```

<span data-ttu-id="9d5e2-189">另一種方法是暫時停用端點，直到您準備好進行容錯回復為止：</span><span class="sxs-lookup"><span data-stu-id="9d5e2-189">Another approach is to temporarily disable the endpoint until you are ready to fail back:</span></span>

```azurecli
az network traffic-manager endpoint update --resource-group <resource-group> --profile-name <profile>
    --name <endpoint-name> --type azureEndpoints --endpoint-status Disabled
```

<span data-ttu-id="9d5e2-190">依據容錯移轉的原因而定，您可能需要重新部署區域內的資源。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-190">Depending on the cause of a failover, you might need to redeploy the resources within a region.</span></span> <span data-ttu-id="9d5e2-191">容錯回復之前，請執行操作性整備測試。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-191">Before failing back, perform an operational readiness test.</span></span> <span data-ttu-id="9d5e2-192">測試應該確認如下的事項：</span><span class="sxs-lookup"><span data-stu-id="9d5e2-192">The test should verify things like:</span></span>

- <span data-ttu-id="9d5e2-193">VM 已正確設定</span><span class="sxs-lookup"><span data-stu-id="9d5e2-193">VMs are configured correctly.</span></span> <span data-ttu-id="9d5e2-194">(已安裝所有必要的軟體，IIS 正在執行等設定)。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-194">(All required software is installed, IIS is running, and so on.)</span></span>
- <span data-ttu-id="9d5e2-195">應用程式子系統均狀況良好。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-195">Application subsystems are healthy.</span></span>
- <span data-ttu-id="9d5e2-196">功能測試</span><span class="sxs-lookup"><span data-stu-id="9d5e2-196">Functional testing.</span></span> <span data-ttu-id="9d5e2-197">(例如，資料庫層可從 Web 層連線)。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-197">(For example, the database tier is reachable from the web tier.)</span></span>

### <a name="configure-sql-server-always-on-availability-groups"></a><span data-ttu-id="9d5e2-198">設定 SQL Server Always On 可用性群組</span><span class="sxs-lookup"><span data-stu-id="9d5e2-198">Configure SQL Server Always On Availability Groups</span></span>

<span data-ttu-id="9d5e2-199">在 Windows Server 2016 之前，SQL Server Always On 可用性群組需要一個網域控制站，而且可用性群組中的所有節點都必須位於相同的 Active Directory (AD) 網域。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-199">Prior to Windows Server 2016, SQL Server Always On Availability Groups require a domain controller, and all nodes in the availability group must be in the same Active Directory (AD) domain.</span></span>

<span data-ttu-id="9d5e2-200">設定可用性群組：</span><span class="sxs-lookup"><span data-stu-id="9d5e2-200">To configure the availability group:</span></span>

- <span data-ttu-id="9d5e2-201">在每個區域中至少放置兩個網域控制站。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-201">At a minimum, place two domain controllers in each region.</span></span>
- <span data-ttu-id="9d5e2-202">為每個網域控制站提供一個靜態 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-202">Give each domain controller a static IP address.</span></span>
- <span data-ttu-id="9d5e2-203">建立 VNet 對 VNet 連線以啟用 VNet 之間的通訊。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-203">Create a VNet-to-VNet connection to enable communication between the VNets.</span></span>
- <span data-ttu-id="9d5e2-204">針對每個 VNet，將網域控制站的 IP 位址 (從這兩個區域) 新增到 DNS 伺服器清單。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-204">For each VNet, add the IP addresses of the domain controllers (from both regions) to the DNS server list.</span></span> <span data-ttu-id="9d5e2-205">您可以使用下列 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-205">You can use the following CLI command.</span></span> <span data-ttu-id="9d5e2-206">如需詳細資訊，請參閱[變更 DNS 伺服器][vnet-dns]。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-206">For more information, see [Change DNS servers][vnet-dns].</span></span>

    ```azurecli
    az network vnet update --resource-group <resource-group> --name <vnet-name> --dns-servers "10.0.0.4,10.0.0.6,172.16.0.4,172.16.0.6"
    ```

- <span data-ttu-id="9d5e2-207">建立 [Windows Server 容錯移轉叢集][wsfc] (WSFC) 叢集，其中包含這兩個區域中的 SQL Server 執行個體。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-207">Create a [Windows Server Failover Clustering][wsfc] (WSFC) cluster that includes the SQL Server instances in both regions.</span></span>
- <span data-ttu-id="9d5e2-208">建立 SQL Server Always On 可用性群組，其中包含主要和次要區域中的 SQL Server 執行個體。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-208">Create a SQL Server Always On Availability Group that includes the SQL Server instances in both the primary and secondary regions.</span></span> <span data-ttu-id="9d5e2-209">如需相關步驟，請參閱[將 Always On 可用性群組擴充到遠端 Azure 資料中心 (PowerShell)](https://blogs.msdn.microsoft.com/sqlcat/2014/09/22/extending-alwayson-availability-group-to-remote-azure-datacenter-powershell/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-209">See [Extending Always On Availability Group to Remote Azure Datacenter (PowerShell)](https://blogs.msdn.microsoft.com/sqlcat/2014/09/22/extending-alwayson-availability-group-to-remote-azure-datacenter-powershell/) for the steps.</span></span>

  - <span data-ttu-id="9d5e2-210">將主要複本放置於主要區域。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-210">Put the primary replica in the primary region.</span></span>
  - <span data-ttu-id="9d5e2-211">將一或多個次要複本放置於主要區域。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-211">Put one or more secondary replicas in the primary region.</span></span> <span data-ttu-id="9d5e2-212">設定這些來使用具有自動容錯移轉的同步認可。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-212">Configure these to use synchronous commit with automatic failover.</span></span>
  - <span data-ttu-id="9d5e2-213">將一或多個次要複本放置於次要區域。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-213">Put one or more secondary replicas in the secondary region.</span></span> <span data-ttu-id="9d5e2-214">基於效能考量，設定這些來使用「非同步」認可</span><span class="sxs-lookup"><span data-stu-id="9d5e2-214">Configure these to use *asynchronous* commit, for performance reasons.</span></span> <span data-ttu-id="9d5e2-215">(否則，所有的 T-SQL 交易都必須等候透過網路到次要區域的來回行程)。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-215">(Otherwise, all T-SQL transactions have to wait on a round trip over the network to the secondary region.)</span></span>

    > [!NOTE]
    > <span data-ttu-id="9d5e2-216">非同步認可複本不支援自動容錯移轉。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-216">Asynchronous commit replicas do not support automatic failover.</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="9d5e2-217">可用性考量</span><span class="sxs-lookup"><span data-stu-id="9d5e2-217">Availability considerations</span></span>

<span data-ttu-id="9d5e2-218">使用複雜的多層式架構應用程式，您可能不需要在次要區域中複寫整個應用程式，</span><span class="sxs-lookup"><span data-stu-id="9d5e2-218">With a complex N-tier app, you may not need to replicate the entire application in the secondary region.</span></span> <span data-ttu-id="9d5e2-219">而是可能只需複寫支援商務持續性所需的關鍵子系統。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-219">Instead, you might just replicate a critical subsystem that is needed to support business continuity.</span></span>

<span data-ttu-id="9d5e2-220">流量管理員可能是此系統中的失敗點。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-220">Traffic Manager is a possible failure point in the system.</span></span> <span data-ttu-id="9d5e2-221">如果流量管理員服務失敗，用戶端就無法在當機期間存取您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-221">If the Traffic Manager service fails, clients cannot access your application during the downtime.</span></span> <span data-ttu-id="9d5e2-222">檢閱[流量管理員 SLA][tm-sla]，並判斷單獨使用流量管理員是否符合您獲得高可用性的業務需求。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-222">Review the [Traffic Manager SLA][tm-sla], and determine whether using Traffic Manager alone meets your business requirements for high availability.</span></span> <span data-ttu-id="9d5e2-223">如果沒有，請考慮新增另一個流量管理解決方案作為容錯回復。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-223">If not, consider adding another traffic management solution as a failback.</span></span> <span data-ttu-id="9d5e2-224">如果 Azure 流量管理員服務失敗，請變更您在 DNS 中的 CNAME 記錄，以指向其他流量管理服務</span><span class="sxs-lookup"><span data-stu-id="9d5e2-224">If the Azure Traffic Manager service fails, change your CNAME records in DNS to point to the other traffic management service.</span></span> <span data-ttu-id="9d5e2-225">(此步驟必須手動執行，而且在傳播 DNS 變更之前，將無法使用您的應用程式)。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-225">(This step must be performed manually, and your application will be unavailable until the DNS changes are propagated.)</span></span>

<span data-ttu-id="9d5e2-226">對於 SQL Server 叢集，有兩個要考慮的容錯移轉案例：</span><span class="sxs-lookup"><span data-stu-id="9d5e2-226">For the SQL Server cluster, there are two failover scenarios to consider:</span></span>

- <span data-ttu-id="9d5e2-227">主要區域中的所有 SQL Server 資料庫複本都失敗。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-227">All of the SQL Server database replicas in the primary region fail.</span></span> <span data-ttu-id="9d5e2-228">例如，這可能會在區域中斷期間發生。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-228">For example, this could happen during a regional outage.</span></span> <span data-ttu-id="9d5e2-229">在該情況下，您必須手動容錯移轉可用性群組，即使流量管理員會在前端自動容錯移轉。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-229">In that case, you must manually fail over the availability group, even though Traffic Manager automatically fails over on the front end.</span></span> <span data-ttu-id="9d5e2-230">請依照[執行 SQL Server 可用性群組的強制手動容錯移轉](https://msdn.microsoft.com/library/ff877957.aspx)，其中描述如何使用 SQL Server 2016 中的 SQL Server Management Studio、Transact-SQL 或 PowerShell 執行強制容錯移轉。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-230">Follow the steps in [Perform a Forced Manual Failover of a SQL Server Availability Group](https://msdn.microsoft.com/library/ff877957.aspx), which describes how to perform a forced failover by using SQL Server Management Studio, Transact-SQL, or PowerShell in SQL Server 2016.</span></span>

   > [!WARNING]
   > <span data-ttu-id="9d5e2-231">使用強制容錯移轉，會有資料遺失的風險。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-231">With forced failover, there is a risk of data loss.</span></span> <span data-ttu-id="9d5e2-232">當主要區域再次上線之後，請取得資料庫的快照集，然後使用 [tablediff] 來找出差異。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-232">Once the primary region is back online, take a snapshot of the database and use [tablediff] to find the differences.</span></span>

- <span data-ttu-id="9d5e2-233">流量管理員會容錯移轉到次要區域，但仍然可以使用主要的 SQL Server 資料庫複本。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-233">Traffic Manager fails over to the secondary region, but the primary SQL Server database replica is still available.</span></span> <span data-ttu-id="9d5e2-234">例如，前端層可能失敗，但不會影響 SQL Server VM。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-234">For example, the front-end tier might fail, without affecting the SQL Server VMs.</span></span> <span data-ttu-id="9d5e2-235">在該情況下，會將網際網路流量路由傳送到次要區域，而且該區域仍然可以連線到主要複本。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-235">In that case, Internet traffic is routed to the secondary region, and that region can still connect to the primary replica.</span></span> <span data-ttu-id="9d5e2-236">不過，將會增加延遲，因為 SQL Server 連線會跨區域進行。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-236">However, there will be increased latency, because the SQL Server connections are going across regions.</span></span> <span data-ttu-id="9d5e2-237">在此情況下，您應該執行手動容錯移轉，如下所示：</span><span class="sxs-lookup"><span data-stu-id="9d5e2-237">In this situation, you should perform a manual failover as follows:</span></span>

   1. <span data-ttu-id="9d5e2-238">將次要區域中的 SQL Server 資料庫複本暫時切換到「同步」認可。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-238">Temporarily switch a SQL Server database replica in the secondary region to *synchronous* commit.</span></span> <span data-ttu-id="9d5e2-239">這確保在容錯移轉期間將不會遺失資料。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-239">This ensures there won't be data loss during the failover.</span></span>
   2. <span data-ttu-id="9d5e2-240">容錯移轉到該複本。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-240">Fail over to that replica.</span></span>
   3. <span data-ttu-id="9d5e2-241">當您容錯回復到主要區域時，還原非同步認可設定。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-241">When you fail back to the primary region, restore the asynchronous commit setting.</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="9d5e2-242">管理性考量</span><span class="sxs-lookup"><span data-stu-id="9d5e2-242">Manageability considerations</span></span>

<span data-ttu-id="9d5e2-243">當您更新部署時，請一次更新一個區域，以減少因為應用程式中的不正確設定或錯誤而導致全域失敗的機會。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-243">When you update your deployment, update one region at a time to reduce the chance of a global failure from an incorrect configuration or an error in the application.</span></span>

<span data-ttu-id="9d5e2-244">測試系統對於失敗的復原能力。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-244">Test the resiliency of the system to failures.</span></span> <span data-ttu-id="9d5e2-245">以下是一些要測試的常見失敗案例：</span><span class="sxs-lookup"><span data-stu-id="9d5e2-245">Here are some common failure scenarios to test:</span></span>

- <span data-ttu-id="9d5e2-246">關閉 VM 執行個體。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-246">Shut down VM instances.</span></span>
- <span data-ttu-id="9d5e2-247">壓力資源，例如 CPU 和記憶體。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-247">Pressure resources such as CPU and memory.</span></span>
- <span data-ttu-id="9d5e2-248">中斷連線/延遲網路。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-248">Disconnect/delay network.</span></span>
- <span data-ttu-id="9d5e2-249">毀損程序。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-249">Crash processes.</span></span>
- <span data-ttu-id="9d5e2-250">憑證到期。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-250">Expire certificates.</span></span>
- <span data-ttu-id="9d5e2-251">模擬硬體故障。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-251">Simulate hardware faults.</span></span>
- <span data-ttu-id="9d5e2-252">關閉網域控制站上的 DNS 服務。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-252">Shut down the DNS service on the domain controllers.</span></span>

<span data-ttu-id="9d5e2-253">測量復原時間，並確認它們符合您的業務需求。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-253">Measure the recovery times and verify they meet your business requirements.</span></span> <span data-ttu-id="9d5e2-254">此外，也需測試失敗模式組合。</span><span class="sxs-lookup"><span data-stu-id="9d5e2-254">Test combinations of failure modes, as well.</span></span>

## <a name="related-resources"></a><span data-ttu-id="9d5e2-255">相關資源</span><span class="sxs-lookup"><span data-stu-id="9d5e2-255">Related resources</span></span>

<span data-ttu-id="9d5e2-256">您可以檢閱下列 [Azure 範例案例](/azure/architecture/example-scenario)，其中示範使用相同技術的一些特定解決方案：</span><span class="sxs-lookup"><span data-stu-id="9d5e2-256">You may wish to review the following [Azure example scenarios](/azure/architecture/example-scenario) that demonstrate specific solutions using some of the same technologies:</span></span>

- [<span data-ttu-id="9d5e2-257">在 Azure 上為高可用性和災害復原而建置的多層式 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="9d5e2-257">Multitier web application built for high availability and disaster recovery on Azure</span></span>](/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)
- [<span data-ttu-id="9d5e2-258">使用 Azure 上的 Windows 虛擬機器建置安全的 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="9d5e2-258">Building secure web applications with Windows virtual machines on Azure</span></span>](/azure/architecture/example-scenario/infrastructure/regulated-multitier-app)

<!-- links -->

[hybrid-vpn]: ../hybrid-networking/vpn.md
[azure-dns]: /azure/dns/dns-overview
[azure-sla]: https://azure.microsoft.com/support/legal/sla/
[azure-sql-db]: https://azure.microsoft.com/documentation/services/sql-database/
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[azure-cli]: /cli/azure/
[regional-pairs]: /azure/best-practices-availability-paired-regions
[resource groups]: /azure/azure-resource-manager/resource-group-overview
[resource-group-links]: /azure/resource-group-link-resources
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[services-by-region]: https://azure.microsoft.com/regions/#services
[sql-always-on]: https://msdn.microsoft.com/library/hh510230.aspx
[tablediff]: https://msdn.microsoft.com/library/ms162843.aspx
[tm-configure-failover]: /azure/traffic-manager/traffic-manager-configure-failover-routing-method
[tm-monitoring]: /azure/traffic-manager/traffic-manager-monitoring
[tm-routing]: /azure/traffic-manager/traffic-manager-routing-methods
[tm-sla]: https://azure.microsoft.com/support/legal/sla/traffic-manager
[traffic-manager]: https://azure.microsoft.com/services/traffic-manager
[visio-download]: https://archcenter.blob.core.windows.net/cdn/vm-reference-architectures.vsdx
[vnet-dns]: /azure/virtual-network/manage-virtual-network#change-dns-servers
[vnet-to-vnet]: /azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps
[vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[wsfc]: https://msdn.microsoft.com/library/hh270278.aspx

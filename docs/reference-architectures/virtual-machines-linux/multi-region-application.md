---
title: "在多個 Azure 區域中執行 Linux VM 以獲得高可用性"
description: "如何在 Azure 上的多個區域中部署 VM 以獲得高可用性和復原能力。"
author: MikeWasson
ms.date: 11/22/2016
pnp.series.title: Linux VM workloads
pnp.series.prev: n-tier
ms.openlocfilehash: 3b68f6fc79ba4b29e41ba2b04537b834bb8859b0
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="run-linux-vms-in-multiple-regions-for-high-availability"></a><span data-ttu-id="1a961-103">在多個區域中執行 Linux VM 以獲得高可用性</span><span class="sxs-lookup"><span data-stu-id="1a961-103">Run Linux VMs in multiple regions for high availability</span></span>

<span data-ttu-id="1a961-104">此參考架構顯示一組經過證實的作法，其可在多個 Azure 區域中執行多層式架構應用程式，以實現可用性和強固的災害復原基礎結構。</span><span class="sxs-lookup"><span data-stu-id="1a961-104">This reference architecture shows a set of proven practices for running an N-tier application in multiple Azure regions, in order to achieve availability and a robust disaster recovery infrastructure.</span></span> 

<span data-ttu-id="1a961-105">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="1a961-105">![[0]][0]</span></span>

<span data-ttu-id="1a961-106">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="1a961-106">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="1a961-107">架構</span><span class="sxs-lookup"><span data-stu-id="1a961-107">Architecture</span></span> 

<span data-ttu-id="1a961-108">此架構是根據[執行適用於多層式架構應用程式的 Linux VM](n-tier.md) 建置的。</span><span class="sxs-lookup"><span data-stu-id="1a961-108">This architecture builds on the one shown in [Run Linux VMs for an N-tier application](n-tier.md).</span></span> 

* <span data-ttu-id="1a961-109">**主要和次要區域**。</span><span class="sxs-lookup"><span data-stu-id="1a961-109">**Primary and secondary regions**.</span></span> <span data-ttu-id="1a961-110">使用兩個區域以實現更高的可用性。</span><span class="sxs-lookup"><span data-stu-id="1a961-110">Use two regions to achieve higher availability.</span></span> <span data-ttu-id="1a961-111">其中一個是主要區域。另一個區域則用於容錯移轉。</span><span class="sxs-lookup"><span data-stu-id="1a961-111">One is the primary region.The other region is for failover.</span></span>
* <span data-ttu-id="1a961-112">**Azure 流量管理員**。</span><span class="sxs-lookup"><span data-stu-id="1a961-112">**Azure Traffic Manager**.</span></span> <span data-ttu-id="1a961-113">[流量管理員][traffic-manager]會將連入要求路由傳送到其中一個區域。</span><span class="sxs-lookup"><span data-stu-id="1a961-113">[Traffic Manager][traffic-manager] routes incoming requests to one of the regions.</span></span> <span data-ttu-id="1a961-114">正常作業期間，它會將要求路由傳送到主要區域。</span><span class="sxs-lookup"><span data-stu-id="1a961-114">During normal operations, it routes requests to the primary region.</span></span> <span data-ttu-id="1a961-115">如果該區域變得無法使用，流量管理員就會容錯移轉到次要區域。</span><span class="sxs-lookup"><span data-stu-id="1a961-115">If that region becomes unavailable, Traffic Manager fails over to the secondary region.</span></span> <span data-ttu-id="1a961-116">如需詳細資訊，請參閱[流量管理員設定](#traffic-manager-configuration)。</span><span class="sxs-lookup"><span data-stu-id="1a961-116">For more information, see the section [Traffic Manager configuration](#traffic-manager-configuration).</span></span>
* <span data-ttu-id="1a961-117">**資源群組**。</span><span class="sxs-lookup"><span data-stu-id="1a961-117">**Resource groups**.</span></span> <span data-ttu-id="1a961-118">分別針對主要區域、次要區域及流量管理員建立[資源群組][resource groups]。</span><span class="sxs-lookup"><span data-stu-id="1a961-118">Create separate [resource groups][resource groups] for the primary region, the secondary region, and for Traffic Manager.</span></span> <span data-ttu-id="1a961-119">這可讓您彈性地將每個區域當作單一資源集合來管理。</span><span class="sxs-lookup"><span data-stu-id="1a961-119">This gives you the flexibility to manage each region as a single collection of resources.</span></span> <span data-ttu-id="1a961-120">例如，您可以重新部署某一個區域，而不需拿掉另一個區域。</span><span class="sxs-lookup"><span data-stu-id="1a961-120">For example, you could redeploy one region, without taking down the other one.</span></span> <span data-ttu-id="1a961-121">[連結資源群組][resource-group-links]，如此一來，您就能執行查詢，以列出應用程式的所有資源。</span><span class="sxs-lookup"><span data-stu-id="1a961-121">[Link the resource groups][resource-group-links], so that you can run a query to list all the resources for the application.</span></span>
* <span data-ttu-id="1a961-122">**VNet**。</span><span class="sxs-lookup"><span data-stu-id="1a961-122">**VNets**.</span></span> <span data-ttu-id="1a961-123">針對每個區域建立不同的 VNet。</span><span class="sxs-lookup"><span data-stu-id="1a961-123">Create a separate VNet for each region.</span></span> <span data-ttu-id="1a961-124">確定位址空間並未重疊。</span><span class="sxs-lookup"><span data-stu-id="1a961-124">Make sure the address spaces do not overlap.</span></span>
* <span data-ttu-id="1a961-125">**Apache Cassandra**。</span><span class="sxs-lookup"><span data-stu-id="1a961-125">**Apache Cassandra**.</span></span> <span data-ttu-id="1a961-126">跨 Azure 區域在資料中心部署 Cassandra 以獲得高可用性。</span><span class="sxs-lookup"><span data-stu-id="1a961-126">Deploy Cassandra in data centers across Azure regions for high availability.</span></span> <span data-ttu-id="1a961-127">在每個區域中，會在機架感知的模式中使用錯誤和升級網域來設定節點，以便在區域內部提供復原能力。</span><span class="sxs-lookup"><span data-stu-id="1a961-127">Within each region, nodes are configured in rack-aware mode with fault and upgrade domains, for resiliency inside the region.</span></span>

## <a name="recommendations"></a><span data-ttu-id="1a961-128">建議</span><span class="sxs-lookup"><span data-stu-id="1a961-128">Recommendations</span></span>

<span data-ttu-id="1a961-129">多區域架構可以提供比部署到單一區域更高的可用性。</span><span class="sxs-lookup"><span data-stu-id="1a961-129">A multi-region architecture can provide higher availability than deploying to a single region.</span></span> <span data-ttu-id="1a961-130">如果區域中斷會影響主要區域，您可以使用[流量管理員][traffic-manager]容錯移轉到次要區域。</span><span class="sxs-lookup"><span data-stu-id="1a961-130">If a regional outage affects the primary region, you can use [Traffic Manager][traffic-manager] to fail over to the secondary region.</span></span> <span data-ttu-id="1a961-131">如果應用程式的個別子系統失敗，此架構也可以提供協助。</span><span class="sxs-lookup"><span data-stu-id="1a961-131">This architecture can also help if an individual subsystem of the application fails.</span></span>

<span data-ttu-id="1a961-132">有數種一般方法可用來跨區域實現高可用性：</span><span class="sxs-lookup"><span data-stu-id="1a961-132">There are several general approaches to achieving high availability across regions:</span></span>   

* <span data-ttu-id="1a961-133">搭配熱待命的主動/被動。</span><span class="sxs-lookup"><span data-stu-id="1a961-133">Active/passive with hot standby.</span></span> <span data-ttu-id="1a961-134">流量會傳送到其中一個區域，而另一個區域會以熱待命模式等候。</span><span class="sxs-lookup"><span data-stu-id="1a961-134">Traffic goes to one region, while the other waits on hot standby.</span></span> <span data-ttu-id="1a961-135">熱待命表示次要區域中隨時都已配置 VM 且正在執行中。</span><span class="sxs-lookup"><span data-stu-id="1a961-135">Hot standby means the VMs in the secondary region are allocated and running at all times.</span></span>
* <span data-ttu-id="1a961-136">搭配冷待命的主動/被動。</span><span class="sxs-lookup"><span data-stu-id="1a961-136">Active/passive with cold standby.</span></span> <span data-ttu-id="1a961-137">流量會傳送到其中一個區域，而另一個區域會以冷待命模式等候。</span><span class="sxs-lookup"><span data-stu-id="1a961-137">Traffic goes to one region, while the other waits on cold standby.</span></span> <span data-ttu-id="1a961-138">冷待命表示在需要容錯移轉之前，不會在次要區域中配置 VM。</span><span class="sxs-lookup"><span data-stu-id="1a961-138">Cold standby means the VMs in the secondary region are not allocated until needed for failover.</span></span> <span data-ttu-id="1a961-139">此方式的成本小於執行，但在失敗期間通常需要較久的時間才能上線。</span><span class="sxs-lookup"><span data-stu-id="1a961-139">This approach costs less to run, but will generally take longer to come online during a failure.</span></span>
* <span data-ttu-id="1a961-140">主動/主動。</span><span class="sxs-lookup"><span data-stu-id="1a961-140">Active/active.</span></span> <span data-ttu-id="1a961-141">這兩個區域均為主動，而且要求會在它們之間進行負載平衡。</span><span class="sxs-lookup"><span data-stu-id="1a961-141">Both regions are active, and requests are load balanced between them.</span></span> <span data-ttu-id="1a961-142">如果其中一個區域變成無法使用，就會將它從輪替中剔除。</span><span class="sxs-lookup"><span data-stu-id="1a961-142">If one region becomes unavailable, it is taken out of rotation.</span></span> 

<span data-ttu-id="1a961-143">此架構著重於搭配熱待命的主動/被動 (使用流量管理員進行容錯移轉)。</span><span class="sxs-lookup"><span data-stu-id="1a961-143">This architecture focuses on active/passive with hot standby, using Traffic Manager for failover.</span></span> <span data-ttu-id="1a961-144">請注意，您可以部署少量的 VM 來進行熱待命，然後視需要相應放大。</span><span class="sxs-lookup"><span data-stu-id="1a961-144">Note that you could deploy a small number of VMs for hot standby and then scale out as needed.</span></span>


### <a name="regional-pairing"></a><span data-ttu-id="1a961-145">區域配對</span><span class="sxs-lookup"><span data-stu-id="1a961-145">Regional pairing</span></span>

<span data-ttu-id="1a961-146">每個 Azure 區域都會與相同地理位置內的另一個區域配對。</span><span class="sxs-lookup"><span data-stu-id="1a961-146">Each Azure region is paired with another region within the same geography.</span></span> <span data-ttu-id="1a961-147">通常會從相同區域配對選擇區域 (例如，美國東部 2 和美國中部)。</span><span class="sxs-lookup"><span data-stu-id="1a961-147">In general, choose regions from the same regional pair (for example, East US 2 and US Central).</span></span> <span data-ttu-id="1a961-148">這樣做的優點包括：</span><span class="sxs-lookup"><span data-stu-id="1a961-148">Benefits of doing so include:</span></span>

* <span data-ttu-id="1a961-149">如果發生廣泛的中斷，至少會優先復原每個配對中的一個區域。</span><span class="sxs-lookup"><span data-stu-id="1a961-149">If there is a broad outage, recovery of at least one region out of every pair is prioritized.</span></span>
* <span data-ttu-id="1a961-150">已計劃的 Azure 系統更新會循序在配對區域中推出，以將可能的停機時間降到最低。</span><span class="sxs-lookup"><span data-stu-id="1a961-150">Planned Azure system updates are rolled out to paired regions sequentially, to minimize possible downtime.</span></span>
* <span data-ttu-id="1a961-151">配對會位於相同的地理位置，以符合資料落地需求。</span><span class="sxs-lookup"><span data-stu-id="1a961-151">Pairs reside within the same geography, to meet data residency requirements.</span></span>

<span data-ttu-id="1a961-152">不過，請確定這兩個區域均支援您應用程式所需的所有 Azure 服務 (請參閱[依區域提供的服務][services-by-region])。</span><span class="sxs-lookup"><span data-stu-id="1a961-152">However, make sure that both regions support all of the Azure services needed for your application (see [Services by region][services-by-region]).</span></span> <span data-ttu-id="1a961-153">如需區域配對的詳細資訊，請參閱[業務持續性和災害復原 (BCDR)：Azure 配對的區域][regional-pairs]。</span><span class="sxs-lookup"><span data-stu-id="1a961-153">For more information about regional pairs, see [Business continuity and disaster recovery (BCDR): Azure Paired Regions][regional-pairs].</span></span>

### <a name="traffic-manager-configuration"></a><span data-ttu-id="1a961-154">流量管理員設定</span><span class="sxs-lookup"><span data-stu-id="1a961-154">Traffic Manager configuration</span></span>

<span data-ttu-id="1a961-155">設定流量管理員時，請考慮下列各點：</span><span class="sxs-lookup"><span data-stu-id="1a961-155">Consider the following points when configuring Traffic Manager:</span></span>

* <span data-ttu-id="1a961-156">**路由**。</span><span class="sxs-lookup"><span data-stu-id="1a961-156">**Routing**.</span></span> <span data-ttu-id="1a961-157">流量管理員支援數個[路由演算法][tm-routing]。</span><span class="sxs-lookup"><span data-stu-id="1a961-157">Traffic Manager supports several [routing algorithms][tm-routing].</span></span> <span data-ttu-id="1a961-158">針對本文所述的案例，請使用「優先順序」路由 (先前稱為「容錯移轉」路由)。</span><span class="sxs-lookup"><span data-stu-id="1a961-158">For the scenario described in this article, use *priority* routing (formerly called *failover* routing).</span></span> <span data-ttu-id="1a961-159">使用此設定，流量管理員會將所有要求傳送到主要區域，除非主要地區變成無法連線。</span><span class="sxs-lookup"><span data-stu-id="1a961-159">With this setting, Traffic Manager sends all requests to the primary region, unless the primary region becomes unreachable.</span></span> <span data-ttu-id="1a961-160">那時，就會自動容錯移轉到次要區域。</span><span class="sxs-lookup"><span data-stu-id="1a961-160">At that point, it automatically fails over to the secondary region.</span></span> <span data-ttu-id="1a961-161">請參閱[設定容錯移轉路由方法][tm-configure-failover]。</span><span class="sxs-lookup"><span data-stu-id="1a961-161">See [Configure Failover routing method][tm-configure-failover].</span></span>
* <span data-ttu-id="1a961-162">**健康情況探查**。</span><span class="sxs-lookup"><span data-stu-id="1a961-162">**Health probe**.</span></span> <span data-ttu-id="1a961-163">流量管理員會使用 HTTP (或 HTTPS) [探查][tm-monitoring]來監視每個區域的可用性。</span><span class="sxs-lookup"><span data-stu-id="1a961-163">Traffic Manager uses an HTTP (or HTTPS) [probe][tm-monitoring] to monitor the availability of each region.</span></span> <span data-ttu-id="1a961-164">探查會針對指定的 URL 路徑檢查 HTTP 200 回應。</span><span class="sxs-lookup"><span data-stu-id="1a961-164">The probe checks for an HTTP 200 response for a specified URL path.</span></span> <span data-ttu-id="1a961-165">最佳作法是，建立端點來報告應用程式的整體健康情況，並使用此端點進行健康情況探查。</span><span class="sxs-lookup"><span data-stu-id="1a961-165">As a best practice, create an endpoint that reports the overall health of the application, and use this endpoint for the health probe.</span></span> <span data-ttu-id="1a961-166">否則，探查可能會在應用程式的關鍵組件真的失敗時報告端點狀況良好。</span><span class="sxs-lookup"><span data-stu-id="1a961-166">Otherwise, the probe might report a healthy endpoint when critical parts of the application are actually failing.</span></span> <span data-ttu-id="1a961-167">如需詳細資訊，請參閱[健康情況端點監視模式][health-endpoint-monitoring-pattern]。</span><span class="sxs-lookup"><span data-stu-id="1a961-167">For more information, see [Health Endpoint Monitoring Pattern][health-endpoint-monitoring-pattern].</span></span>

<span data-ttu-id="1a961-168">當流量管理員容錯移轉時，用戶端會有一段時間無法連線到應用程式。</span><span class="sxs-lookup"><span data-stu-id="1a961-168">When Traffic Manager fails over there is a period of time when clients cannot reach the application.</span></span> <span data-ttu-id="1a961-169">持續時間會受到下列因素影響：</span><span class="sxs-lookup"><span data-stu-id="1a961-169">The duration is affected by the following factors:</span></span>

* <span data-ttu-id="1a961-170">健康情況探查必須偵測到主要區域已變成無法連線。</span><span class="sxs-lookup"><span data-stu-id="1a961-170">The health probe must detect that the primary region has become unreachable.</span></span>
* <span data-ttu-id="1a961-171">DNS 伺服器必須針對 IP 位址更新快取的 DNS 記錄，這取決於 DNS 存留時間 (TTL)。</span><span class="sxs-lookup"><span data-stu-id="1a961-171">DNS servers must update the cached DNS records for the IP address, which depends on the DNS time-to-live (TTL).</span></span> <span data-ttu-id="1a961-172">預設的 TTL 是 300 秒 (5 分鐘)，但是當您建立流量管理員設定檔時，您可以設定此值。</span><span class="sxs-lookup"><span data-stu-id="1a961-172">The default TTL is 300 seconds (5 minutes), but you can configure this value when you create the Traffic Manager profile.</span></span>

<span data-ttu-id="1a961-173">如需詳細資料，請參閱[關於流量管理員監視][tm-monitoring]。</span><span class="sxs-lookup"><span data-stu-id="1a961-173">For details, see [About Traffic Manager Monitoring][tm-monitoring].</span></span>

<span data-ttu-id="1a961-174">如果流量管理員容錯移轉，我們建議執行手動容錯回復，而不是實作自動容錯回復。</span><span class="sxs-lookup"><span data-stu-id="1a961-174">If Traffic Manager fails over, we recommend performing a manual failback rather than implementing an automatic failback.</span></span> <span data-ttu-id="1a961-175">或者，您可以建立應用程式會在區域之間來回翻轉的情況。</span><span class="sxs-lookup"><span data-stu-id="1a961-175">Otherwise, you can create a situation where the application flips back and forth between regions.</span></span> <span data-ttu-id="1a961-176">請確認所有的應用程式子系統狀況良好，然後再進行容錯回復。</span><span class="sxs-lookup"><span data-stu-id="1a961-176">Verify that all application subsystems are healthy before failing back.</span></span>

<span data-ttu-id="1a961-177">請注意，流量管理員預設會自動容錯回復。</span><span class="sxs-lookup"><span data-stu-id="1a961-177">Note that Traffic Manager automatically fails back by default.</span></span> <span data-ttu-id="1a961-178">若要避免這個問題，請在容錯移轉事件之後，手動降低主要區域的優先順序。</span><span class="sxs-lookup"><span data-stu-id="1a961-178">To prevent this, manually lower the priority of the primary region after a failover event.</span></span> <span data-ttu-id="1a961-179">例如，假設主要區域是優先順序 1，而次要為優先順序 2。</span><span class="sxs-lookup"><span data-stu-id="1a961-179">For example, suppose the primary region is priority 1 and the secondary is priority 2.</span></span> <span data-ttu-id="1a961-180">在容錯移轉之後，將主要區域設定為優先順序 3，以防止自動容錯回復。</span><span class="sxs-lookup"><span data-stu-id="1a961-180">After a failover, set the primary region to priority 3, to prevent automatic failback.</span></span> <span data-ttu-id="1a961-181">當您準備好切換回來時，將優先順序更新為 1。</span><span class="sxs-lookup"><span data-stu-id="1a961-181">When you are ready to switch back, update the priority to 1.</span></span>

<span data-ttu-id="1a961-182">下列 [Azure CLI][install-azure-cli] 命令會更新優先順序：</span><span class="sxs-lookup"><span data-stu-id="1a961-182">The following [Azure CLI][install-azure-cli] command updates the priority:</span></span>

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --priority 3
```    

<span data-ttu-id="1a961-183">另一種方法是暫時停用端點，直到您準備好進行容錯回復為止：</span><span class="sxs-lookup"><span data-stu-id="1a961-183">Another approach is to temporarily disable the endpoint until you are ready to fail back:</span></span>

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --status Disabled
```    

<span data-ttu-id="1a961-184">依據容錯移轉的原因而定，您可能需要重新部署區域內的資源。</span><span class="sxs-lookup"><span data-stu-id="1a961-184">Depending on the cause of a failover, you might need to redeploy the resources within a region.</span></span> <span data-ttu-id="1a961-185">容錯回復之前，請執行操作性整備測試。</span><span class="sxs-lookup"><span data-stu-id="1a961-185">Before failing back, perform an operational readiness test.</span></span> <span data-ttu-id="1a961-186">測試應該確認如下的事項：</span><span class="sxs-lookup"><span data-stu-id="1a961-186">The test should verify things like:</span></span>

* <span data-ttu-id="1a961-187">VM 已正確設定</span><span class="sxs-lookup"><span data-stu-id="1a961-187">VMs are configured correctly.</span></span> <span data-ttu-id="1a961-188">(已安裝所有必要的軟體，IIS 正在執行等設定)。</span><span class="sxs-lookup"><span data-stu-id="1a961-188">(All required software is installed, IIS is running, and so on.)</span></span>
* <span data-ttu-id="1a961-189">應用程式子系統均狀況良好。</span><span class="sxs-lookup"><span data-stu-id="1a961-189">Application subsystems are healthy.</span></span>
* <span data-ttu-id="1a961-190">功能測試</span><span class="sxs-lookup"><span data-stu-id="1a961-190">Functional testing.</span></span> <span data-ttu-id="1a961-191">(例如，資料庫層可從 Web 層連線)。</span><span class="sxs-lookup"><span data-stu-id="1a961-191">(For example, the database tier is reachable from the web tier.)</span></span>

### <a name="cassandra-deployment-across-multiple-regions"></a><span data-ttu-id="1a961-192">跨多個區域的 Cassandra 部署</span><span class="sxs-lookup"><span data-stu-id="1a961-192">Cassandra deployment across multiple regions</span></span>

<span data-ttu-id="1a961-193">Cassandra 資料中心是一群相關的資料節點，其會一起設定於叢集內，以進行複寫和工作負載隔離。</span><span class="sxs-lookup"><span data-stu-id="1a961-193">Cassandra data centers are a group of related data nodes that are configured together within a cluster for replication and workload segregation.</span></span>

<span data-ttu-id="1a961-194">我們建議針對生產環境使用 [DataStax Enterprise][datastax]。</span><span class="sxs-lookup"><span data-stu-id="1a961-194">We recommend [DataStax Enterprise][datastax] for production use.</span></span> <span data-ttu-id="1a961-195">如需在 Azure 中執行 DataStax 的詳細資訊，請參閱[適用於 Azure 的DataStax Enterprise 部署指南][cassandra-in-azure]。</span><span class="sxs-lookup"><span data-stu-id="1a961-195">For more information on running DataStax in Azure, see [DataStax Enterprise Deployment Guide for Azure][cassandra-in-azure].</span></span> <span data-ttu-id="1a961-196">下列一般建議適用於所有 Cassandra 版本：</span><span class="sxs-lookup"><span data-stu-id="1a961-196">The following general recommendations apply to any Cassandra edition:</span></span> 

* <span data-ttu-id="1a961-197">將公用 IP 位址指派給每個節點。</span><span class="sxs-lookup"><span data-stu-id="1a961-197">Assign a public IP address to each node.</span></span> <span data-ttu-id="1a961-198">這讓叢集能夠使用 Azure 骨幹基礎結構跨區域通訊，以低成本提供高輸送量。</span><span class="sxs-lookup"><span data-stu-id="1a961-198">This enables the clusters to communicate across regions using the Azure backbone infrastructure, providing high throughput at low cost.</span></span>
* <span data-ttu-id="1a961-199">使用適當的防火牆和網路安全性群組 (NSG) 設定來保護節點，只允許來回已知主機 (包括用戶端和其他叢集節點) 的流量。</span><span class="sxs-lookup"><span data-stu-id="1a961-199">Secure nodes using the appropriate firewall and network security group (NSG) configurations, allowing traffic only to and from known hosts, including clients and other cluster nodes.</span></span> <span data-ttu-id="1a961-200">請注意，Cassandra 會針對通訊、OpsCenter、Spark 等使用不同的連接埠。</span><span class="sxs-lookup"><span data-stu-id="1a961-200">Note that Cassandra uses different ports for communication, OpsCenter, Spark, and so forth.</span></span> <span data-ttu-id="1a961-201">如需 Cassandra 中的連接埠使用方式，請參閱[設定防火牆連接埠存取][cassandra-ports]。</span><span class="sxs-lookup"><span data-stu-id="1a961-201">For port usage in Cassandra, see [Configuring firewall port access][cassandra-ports].</span></span>
* <span data-ttu-id="1a961-202">針對所有[用戶端對節點][ssl-client-node]和[節點對節點][ssl-node-node]通訊使用 SSL 加密。</span><span class="sxs-lookup"><span data-stu-id="1a961-202">Use SSL encryption for all [client-to-node][ssl-client-node] and [node-to-node][ssl-node-node] communications.</span></span>
* <span data-ttu-id="1a961-203">在區域中，請遵循 [Cassandra 建議](n-tier.md#cassandra)中的方針進行。</span><span class="sxs-lookup"><span data-stu-id="1a961-203">Within a region, follow the guidelines in [Cassandra recommendations](n-tier.md#cassandra).</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="1a961-204">可用性考量</span><span class="sxs-lookup"><span data-stu-id="1a961-204">Availability considerations</span></span>

<span data-ttu-id="1a961-205">使用複雜的多層式架構應用程式，您可能不需要在次要區域中複寫整個應用程式，</span><span class="sxs-lookup"><span data-stu-id="1a961-205">With a complex N-tier app, you may not need to replicate the entire application in the secondary region.</span></span> <span data-ttu-id="1a961-206">而是可能只需複寫支援商務持續性所需的關鍵子系統。</span><span class="sxs-lookup"><span data-stu-id="1a961-206">Instead, you might just replicate a critical subsystem that is needed to support business continuity.</span></span>

<span data-ttu-id="1a961-207">流量管理員可能是此系統中的失敗點。</span><span class="sxs-lookup"><span data-stu-id="1a961-207">Traffic Manager is a possible failure point in the system.</span></span> <span data-ttu-id="1a961-208">如果流量管理員服務失敗，用戶端就無法在當機期間存取您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="1a961-208">If the Traffic Manager service fails, clients cannot access your application during the downtime.</span></span> <span data-ttu-id="1a961-209">檢閱[流量管理員 SLA][tm-sla]，並判斷單獨使用流量管理員是否符合您獲得高可用性的業務需求。</span><span class="sxs-lookup"><span data-stu-id="1a961-209">Review the [Traffic Manager SLA][tm-sla], and determine whether using Traffic Manager alone meets your business requirements for high availability.</span></span> <span data-ttu-id="1a961-210">如果沒有，請考慮新增另一個流量管理解決方案作為容錯回復。</span><span class="sxs-lookup"><span data-stu-id="1a961-210">If not, consider adding another traffic management solution as a failback.</span></span> <span data-ttu-id="1a961-211">如果 Azure 流量管理員服務失敗，請變更您在 DNS 中的 CNAME 記錄，以指向其他流量管理服務</span><span class="sxs-lookup"><span data-stu-id="1a961-211">If the Azure Traffic Manager service fails, change your CNAME records in DNS to point to the other traffic management service.</span></span> <span data-ttu-id="1a961-212">(此步驟必須手動執行，而且在傳播 DNS 變更之前，將無法使用您的應用程式)。</span><span class="sxs-lookup"><span data-stu-id="1a961-212">(This step must be performed manually, and your application will be unavailable until the DNS changes are propagated.)</span></span>

<span data-ttu-id="1a961-213">針對 Cassandra 叢集，要考慮的容錯移轉案例取決於應用程式所使用的一致性層級，以及使用的複本數目。</span><span class="sxs-lookup"><span data-stu-id="1a961-213">For the Cassandra cluster, the failover scenarios to consider depend on the consistency levels used by the application, as well as the number of replicas used.</span></span> <span data-ttu-id="1a961-214">如需 Cassandra 中的一致性層級和使用方式，請參閱[設定資料一致性][cassandra-consistency]和 [Cassandra：有多少節點會與仲裁交談？][cassandra-consistency-usage]</span><span class="sxs-lookup"><span data-stu-id="1a961-214">For consistency levels and usage in Cassandra, see [Configuring data consistency][cassandra-consistency] and [Cassandra: How many nodes are talked to with Quorum?][cassandra-consistency-usage]</span></span> <span data-ttu-id="1a961-215">Cassandra 中的資料可用性取決於應用程式所使用的一致性層級和複寫機制。</span><span class="sxs-lookup"><span data-stu-id="1a961-215">Data availability in Cassandra is determined by the consistency level used by the application and the replication mechanism.</span></span> <span data-ttu-id="1a961-216">如需 Cassandra 中的複寫，請參閱 [NoSQL 資料庫的資料複寫說明][cassandra-replication]。</span><span class="sxs-lookup"><span data-stu-id="1a961-216">For replication in Cassandra, see [Data Replication in NoSQL Databases Explained][cassandra-replication].</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="1a961-217">管理性考量</span><span class="sxs-lookup"><span data-stu-id="1a961-217">Manageability considerations</span></span>

<span data-ttu-id="1a961-218">當您更新部署時，請一次更新一個區域，以減少因為應用程式中的不正確設定或錯誤而導致全域失敗的機會。</span><span class="sxs-lookup"><span data-stu-id="1a961-218">When you update your deployment, update one region at a time to reduce the chance of a global failure from an incorrect configuration or an error in the application.</span></span>

<span data-ttu-id="1a961-219">測試系統對於失敗的復原能力。</span><span class="sxs-lookup"><span data-stu-id="1a961-219">Test the resiliency of the system to failures.</span></span> <span data-ttu-id="1a961-220">以下是一些要測試的常見失敗案例：</span><span class="sxs-lookup"><span data-stu-id="1a961-220">Here are some common failure scenarios to test:</span></span>

* <span data-ttu-id="1a961-221">關閉 VM 執行個體。</span><span class="sxs-lookup"><span data-stu-id="1a961-221">Shut down VM instances.</span></span>
* <span data-ttu-id="1a961-222">壓力資源，例如 CPU 和記憶體。</span><span class="sxs-lookup"><span data-stu-id="1a961-222">Pressure resources such as CPU and memory.</span></span>
* <span data-ttu-id="1a961-223">中斷連線/延遲網路。</span><span class="sxs-lookup"><span data-stu-id="1a961-223">Disconnect/delay network.</span></span>
* <span data-ttu-id="1a961-224">毀損程序。</span><span class="sxs-lookup"><span data-stu-id="1a961-224">Crash processes.</span></span>
* <span data-ttu-id="1a961-225">憑證到期。</span><span class="sxs-lookup"><span data-stu-id="1a961-225">Expire certificates.</span></span>
* <span data-ttu-id="1a961-226">模擬硬體故障。</span><span class="sxs-lookup"><span data-stu-id="1a961-226">Simulate hardware faults.</span></span>
* <span data-ttu-id="1a961-227">關閉網域控制站上的 DNS 服務。</span><span class="sxs-lookup"><span data-stu-id="1a961-227">Shut down the DNS service on the domain controllers.</span></span>

<span data-ttu-id="1a961-228">測量復原時間，並確認它們符合您的業務需求。</span><span class="sxs-lookup"><span data-stu-id="1a961-228">Measure the recovery times and verify they meet your business requirements.</span></span> <span data-ttu-id="1a961-229">此外，也需測試失敗模式組合。</span><span class="sxs-lookup"><span data-stu-id="1a961-229">Test combinations of failure modes, as well.</span></span>


<!-- Links -->
[hybrid-vpn]: ../hybrid-networking/vpn.md

[cassandra-in-azure]: https://academy.datastax.com/resources/deployment-guide-azure
[cassandra-consistency]: http://docs.datastax.com/en/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
[cassandra-replication]: http://www.planetcassandra.org/data-replication-in-nosql-databases-explained/
[cassandra-consistency-usage]: https://medium.com/@foundev/cassandra-how-many-nodes-are-talked-to-with-quorum-also-should-i-use-it-98074e75d7d5#.b4pb4alb2
[cassandra-ports]: https://docs.datastax.com/en/datastax_enterprise/5.0/datastax_enterprise/sec/configFirewallPorts.html
[datastax]: https://www.datastax.com/products/datastax-enterprise
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[install-azure-cli]: /azure/xplat-cli-install
[regional-pairs]: /azure/best-practices-availability-paired-regions
[resource groups]: /azure/azure-resource-manager/resource-group-overview
[resource-group-links]: /azure/resource-group-link-resources
[services-by-region]: https://azure.microsoft.com/regions/#services
[ssl-client-node]: http://docs.datastax.com/en/cassandra/2.0/cassandra/security/secureSSLClientToNode_t.html
[ssl-node-node]: http://docs.datastax.com/en/cassandra/2.0/cassandra/security/secureSSLNodeToNode_t.html
[tablediff]: https://msdn.microsoft.com/library/ms162843.aspx
[tm-configure-failover]: /azure/traffic-manager/traffic-manager-configure-failover-routing-method
[tm-monitoring]: /azure/traffic-manager/traffic-manager-monitoring
[tm-routing]: /azure/traffic-manager/traffic-manager-routing-methods
[tm-sla]: https://azure.microsoft.com/support/legal/sla/traffic-manager/v1_0/
[traffic-manager]: https://azure.microsoft.com/services/traffic-manager/
[visio-download]: https://archcenter.azureedge.net/cdn/vm-reference-architectures.vsdx
[wsfc]: https://msdn.microsoft.com/library/hh270278.aspx
[0]: ./images/multi-region-application-diagram.png "適用於 Azure 多層式架構應用程式的高可用性網路架構"

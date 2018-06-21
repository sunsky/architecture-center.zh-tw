---
title: 實作 Azure 和網際網路之間的 DMZ
description: 如何在 Azure 中使用網際網路存取實作安全的混合式網路架構。
author: telmosampaio
ms.date: 11/23/2016
pnp.series.title: Network DMZ
pnp.series.next: nva-ha
pnp.series.prev: secure-vnet-hybrid
cardTitle: DMZ between Azure and the Internet
ms.openlocfilehash: c88545b1fcae49b413e7e2b6ac5bd92d3fd3456d
ms.sourcegitcommit: c441fd165e6bebbbbbc19854ec6f3676be9c3b25
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2018
ms.locfileid: "30270393"
---
# <a name="dmz-between-azure-and-the-internet"></a><span data-ttu-id="d71cf-103">Azure 和網際網路之間的 DMZ</span><span class="sxs-lookup"><span data-stu-id="d71cf-103">DMZ between Azure and the Internet</span></span>

<span data-ttu-id="d71cf-104">此參考架構顯示的安全混合式網路，可將內部部署網路擴充至 Azure 並接受網際網路流量。</span><span class="sxs-lookup"><span data-stu-id="d71cf-104">This reference architecture shows a secure hybrid network that extends an on-premises network to Azure and also accepts Internet traffic.</span></span> 

<span data-ttu-id="d71cf-105">[![0]][0]</span><span class="sxs-lookup"><span data-stu-id="d71cf-105">[![0]][0]</span></span> 

<span data-ttu-id="d71cf-106">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="d71cf-106">*Download a [Visio file][visio-download] of this architecture.*</span></span>

<span data-ttu-id="d71cf-107">此參考架構由[實作 Azure 和內部部署資料中心之間的 DMZ][implementing-a-secure-hybrid-network-architecture] 中所述的架構擴充而來。</span><span class="sxs-lookup"><span data-stu-id="d71cf-107">This reference architecture extends the architecture described in [Implementing a DMZ between Azure and your on-premises datacenter][implementing-a-secure-hybrid-network-architecture].</span></span> <span data-ttu-id="d71cf-108">除了用於處理來自內部網路流量的私人 DMZ，還加入了可處理網際網路流量的公用 DMZ</span><span class="sxs-lookup"><span data-stu-id="d71cf-108">It adds a public DMZ that handles Internet traffic, in addition to the private DMZ that handles traffic from the on-premises network</span></span> 

<span data-ttu-id="d71cf-109">此架構的典型使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="d71cf-109">Typical uses for this architecture include:</span></span>

* <span data-ttu-id="d71cf-110">部分工作負載在內部部署中執行、部分在 Azure 中執行的混合式應用程式。</span><span class="sxs-lookup"><span data-stu-id="d71cf-110">Hybrid applications where workloads run partly on-premises and partly in Azure.</span></span>
* <span data-ttu-id="d71cf-111">負責路由傳送內部部署與網際網路的連入流量的 Azure 基礎結構。</span><span class="sxs-lookup"><span data-stu-id="d71cf-111">Azure infrastructure that routes incoming traffic from on-premises and the Internet.</span></span>

## <a name="architecture"></a><span data-ttu-id="d71cf-112">架構</span><span class="sxs-lookup"><span data-stu-id="d71cf-112">Architecture</span></span>

<span data-ttu-id="d71cf-113">此架構由下列元件組成。</span><span class="sxs-lookup"><span data-stu-id="d71cf-113">The architecture consists of the following components.</span></span>

* <span data-ttu-id="d71cf-114">**公用 IP 位址 (PIP)**。</span><span class="sxs-lookup"><span data-stu-id="d71cf-114">**Public IP address (PIP)**.</span></span> <span data-ttu-id="d71cf-115">公用端點的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="d71cf-115">The IP address of the public endpoint.</span></span> <span data-ttu-id="d71cf-116">連線到網際網路的外部使用者可以透過此位址存取此系統。</span><span class="sxs-lookup"><span data-stu-id="d71cf-116">External users connected to the Internet can access the system through this address.</span></span>
* <span data-ttu-id="d71cf-117">**網路虛擬設備 (NVA)**。</span><span class="sxs-lookup"><span data-stu-id="d71cf-117">**Network virtual appliance (NVA)**.</span></span> <span data-ttu-id="d71cf-118">此架構有一個個別的 NVA 集區，用於處理來自網際網路的流量。</span><span class="sxs-lookup"><span data-stu-id="d71cf-118">This architecture includes a separate pool of NVAs for traffic originating on the Internet.</span></span>
* <span data-ttu-id="d71cf-119">**Azure 負載平衡器**。</span><span class="sxs-lookup"><span data-stu-id="d71cf-119">**Azure load balancer**.</span></span> <span data-ttu-id="d71cf-120">來自網際網路的所有傳入要求都要通過負載平衡器，並散發到公用 DMZ 中的 NVA。</span><span class="sxs-lookup"><span data-stu-id="d71cf-120">All incoming requests from the Internet pass through the load balancer and are distributed to the NVAs in the public DMZ.</span></span>
* <span data-ttu-id="d71cf-121">**公用 DMZ 輸入子網路**。</span><span class="sxs-lookup"><span data-stu-id="d71cf-121">**Public DMZ inbound subnet**.</span></span> <span data-ttu-id="d71cf-122">此子網路會接受來自 Azure 負載平衡器的要求。</span><span class="sxs-lookup"><span data-stu-id="d71cf-122">This subnet accepts requests from the Azure load balancer.</span></span> <span data-ttu-id="d71cf-123">傳入要求會傳遞至公用 DMZ 中的其中一個 NVA。</span><span class="sxs-lookup"><span data-stu-id="d71cf-123">Incoming requests are passed to one of the NVAs in the public DMZ.</span></span>
* <span data-ttu-id="d71cf-124">**公用 DMZ 輸出子網路**。</span><span class="sxs-lookup"><span data-stu-id="d71cf-124">**Public DMZ outbound subnet**.</span></span> <span data-ttu-id="d71cf-125">NVA 核准的要求會通過此子網路傳遞至 Web 層的內部負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="d71cf-125">Requests that are approved by the NVA pass through this subnet to the internal load balancer for the web tier.</span></span>

## <a name="recommendations"></a><span data-ttu-id="d71cf-126">建議</span><span class="sxs-lookup"><span data-stu-id="d71cf-126">Recommendations</span></span>

<span data-ttu-id="d71cf-127">下列建議適用於大部分的案例。</span><span class="sxs-lookup"><span data-stu-id="d71cf-127">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="d71cf-128">除非您有特定的需求會覆寫它們，否則請遵循下列建議。</span><span class="sxs-lookup"><span data-stu-id="d71cf-128">Follow these recommendations unless you have a specific requirement that overrides them.</span></span> 

### <a name="nva-recommendations"></a><span data-ttu-id="d71cf-129">NVA 建議</span><span class="sxs-lookup"><span data-stu-id="d71cf-129">NVA recommendations</span></span>

<span data-ttu-id="d71cf-130">一組 NVA 用於來自網際網路的流量，另一組用於來自內部部署的流量。</span><span class="sxs-lookup"><span data-stu-id="d71cf-130">Use one set of NVAs for traffic originating on the Internet, and another for traffic originating on-premises.</span></span> <span data-ttu-id="d71cf-131">兩者使用同一組 NVA 會造成安全性風險，因為在兩組網路流量之間沒有安全性界線。</span><span class="sxs-lookup"><span data-stu-id="d71cf-131">Using only one set of NVAs for both is a security risk, because it provides no security perimeter between the two sets of network traffic.</span></span> <span data-ttu-id="d71cf-132">使用個別 NVA 可降低檢查安全性規則的複雜度，並清楚哪些規則對應到每個傳入網路要求。</span><span class="sxs-lookup"><span data-stu-id="d71cf-132">Using separate NVAs reduces the complexity of checking security rules, and makes it clear which rules correspond to each incoming network request.</span></span> <span data-ttu-id="d71cf-133">一組 NVA 僅實作於網際網路流量的規則，而另一組 NVA 僅實作內部部署流量的規則。</span><span class="sxs-lookup"><span data-stu-id="d71cf-133">One set of NVAs implements rules for Internet traffic only, while another set of NVAs implement rules for on-premises traffic only.</span></span>

<span data-ttu-id="d71cf-134">加入第 7 層 NVA 來終止 NVA 層級的應用程式連線，並維持與後端層的相容性。</span><span class="sxs-lookup"><span data-stu-id="d71cf-134">Include a layer-7 NVA to terminate application connections at the NVA level and maintain compatibility with the backend tiers.</span></span> <span data-ttu-id="d71cf-135">這可確保對稱的連線，亦即來自後端層的回應流量會透過 NVA 傳回。</span><span class="sxs-lookup"><span data-stu-id="d71cf-135">This guarantees symmetric connectivity where response traffic from the backend tiers returns through the NVA.</span></span>  

### <a name="public-load-balancer-recommendations"></a><span data-ttu-id="d71cf-136">公用負載平衡器建議</span><span class="sxs-lookup"><span data-stu-id="d71cf-136">Public load balancer recommendations</span></span>

<span data-ttu-id="d71cf-137">為了延展性和可用性，在[可用性設定組][availability-set]中部署公用 DMZ NVA，並使用[網際網路對向負載平衡器][load-balancer]分散可用性設定組中所有 NVA 的網際網路要求。</span><span class="sxs-lookup"><span data-stu-id="d71cf-137">For scalability and availability, deploy the public DMZ NVAs in an [availability set][availability-set] and use an [Internet facing load balancer][load-balancer] to distribute Internet requests across the NVAs in the availability set.</span></span>  

<span data-ttu-id="d71cf-138">負載平衡器設定為只接受網際網路流量所需的通訊埠上的要求。</span><span class="sxs-lookup"><span data-stu-id="d71cf-138">Configure the load balancer to accept requests only on the ports necessary for Internet traffic.</span></span> <span data-ttu-id="d71cf-139">例如，限制輸入 HTTP 要求在連接埠 80，輸出 HTTPS 要求在連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="d71cf-139">For example, restrict inbound HTTP requests to port 80 and inbound HTTPS requests to port 443.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="d71cf-140">延展性考量</span><span class="sxs-lookup"><span data-stu-id="d71cf-140">Scalability considerations</span></span>

<span data-ttu-id="d71cf-141">即使您的架構一開始只需要公用 DMZ 中有單一 NVA，建議您從一開始就在公用 DMZ 前放置負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="d71cf-141">Even if your architecture initially requires a single NVA in the public DMZ, we recommend putting a load balancer in front of the public DMZ from the beginning.</span></span> <span data-ttu-id="d71cf-142">未來如果需要，這將更容易擴充至多個 NVA。</span><span class="sxs-lookup"><span data-stu-id="d71cf-142">That will make it easier to scale to multiple NVAs in the future, if needed.</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="d71cf-143">可用性考量</span><span class="sxs-lookup"><span data-stu-id="d71cf-143">Availability considerations</span></span>

<span data-ttu-id="d71cf-144">網際網路對向負載平衡器需要公用 DMZ 輸入子網路中的每個 NVA 皆實作[健康情況探查][lb-probe]。</span><span class="sxs-lookup"><span data-stu-id="d71cf-144">The Internet facing load balancer requires each NVA in the public DMZ inbound subnet to implement a [health probe][lb-probe].</span></span> <span data-ttu-id="d71cf-145">此端點無法回應健康情況探查的 NVA 會被視為無法使用，負載平衡器會將要求導向相同可用性設定組中的其他 NVA。</span><span class="sxs-lookup"><span data-stu-id="d71cf-145">A health probe that fails to respond on this endpoint is considered to be unavailable, and the load balancer will direct requests to other NVAs in the same availability set.</span></span> <span data-ttu-id="d71cf-146">請注意，如果所有 NVA 回應都失敗，您的應用程式就會失敗，所以一定要設定監視，在狀況良好的 NVA 執行個體數目低於定義的臨界值時警示 DevOps。</span><span class="sxs-lookup"><span data-stu-id="d71cf-146">Note that if all NVAs fail to respond, your application will fail, so it's important to have monitoring configured to alert DevOps when the number of healthy NVA instances falls below a defined threshold.</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="d71cf-147">管理性考量</span><span class="sxs-lookup"><span data-stu-id="d71cf-147">Manageability considerations</span></span>

<span data-ttu-id="d71cf-148">對公用 DMZ 中 NVA 的所有監視和管理，應該由管理子網路中的 jumpbox 執行。</span><span class="sxs-lookup"><span data-stu-id="d71cf-148">All monitoring and management for the NVAs in the public DMZ should be performed by the jumpbox in the management subnet.</span></span> <span data-ttu-id="d71cf-149">如[實作 Azure 與內部部署資料中心之間的 DMZ][implementing-a-secure-hybrid-network-architecture] 中所述，為了限制存取權，定義單一網路透過閘道從內部部署網路路由傳送至 jumpbox。</span><span class="sxs-lookup"><span data-stu-id="d71cf-149">As discussed in [Implementing a DMZ between Azure and your on-premises datacenter][implementing-a-secure-hybrid-network-architecture], define a single network route from the on-premises network through the gateway to the jumpbox, in order to restrict access.</span></span>

<span data-ttu-id="d71cf-150">如果從內部部署網路到 Azure 的閘道連線關閉，藉由部署公用 IP 位址、將它加入 jumpbox、然後從網際網路登入，仍可連線至 jumpbox。</span><span class="sxs-lookup"><span data-stu-id="d71cf-150">If gateway connectivity from your on-premises network to Azure is down, you can still reach the jumpbox by deploying a public IP address, adding it to the jumpbox, and logging in from the Internet.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="d71cf-151">安全性考量</span><span class="sxs-lookup"><span data-stu-id="d71cf-151">Security considerations</span></span>

<span data-ttu-id="d71cf-152">此參考架構實作多個安全性層級：</span><span class="sxs-lookup"><span data-stu-id="d71cf-152">This reference architecture implements multiple levels of security:</span></span>

* <span data-ttu-id="d71cf-153">網際網路對向負載平衡器會將要求導向輸入公用 DMZ 子網路中的 NVA，而且只在應用程式所需的連接埠。</span><span class="sxs-lookup"><span data-stu-id="d71cf-153">The Internet facing load balancer directs requests to the NVAs in the inbound public DMZ subnet, and only on the ports necessary for the application.</span></span>
* <span data-ttu-id="d71cf-154">藉著封鎖 NSG 規則範圍之外的要求，輸入和輸出公用 DMZ 子網路的 NSG 規則可防止 NVA 受到危害。</span><span class="sxs-lookup"><span data-stu-id="d71cf-154">The NSG rules for the inbound and outbound public DMZ subnets prevent the NVAs from being compromised, by blocking requests that fall outside of the NSG rules.</span></span>
* <span data-ttu-id="d71cf-155">NVA 的 NAT 路由設定會將連接埠 80 和 443 上的傳入要求導向 Web 層負載平衡器，但會忽略其他所有通訊埠上的要求。</span><span class="sxs-lookup"><span data-stu-id="d71cf-155">The NAT routing configuration for the NVAs directs incoming requests on port 80 and port 443 to the web tier load balancer, but ignores requests on all other ports.</span></span>

<span data-ttu-id="d71cf-156">您應該記錄所有連接埠上的所有傳入要求。</span><span class="sxs-lookup"><span data-stu-id="d71cf-156">You should log all incoming requests on all ports.</span></span> <span data-ttu-id="d71cf-157">定期稽核記錄，留意不在預期參數範圍之內的要求，因為這些可能表示入侵嘗試。</span><span class="sxs-lookup"><span data-stu-id="d71cf-157">Regularly audit the logs, paying attention to requests that fall outside of expected parameters, as these may indicate intrusion attempts.</span></span>

## <a name="solution-deployment"></a><span data-ttu-id="d71cf-158">解決方案部署</span><span class="sxs-lookup"><span data-stu-id="d71cf-158">Solution deployment</span></span>

<span data-ttu-id="d71cf-159">在 [GitHub][github-folder] 中有實作這些建議的參考架構部署。</span><span class="sxs-lookup"><span data-stu-id="d71cf-159">A deployment for a reference architecture that implements these recommendations is available on [GitHub][github-folder].</span></span> <span data-ttu-id="d71cf-160">可以使用 Windows 或 Linux VM 部署參考架構，請遵循下列指示：</span><span class="sxs-lookup"><span data-stu-id="d71cf-160">The reference architecture can be deployed either with Windows or Linux VMs by following the directions below:</span></span>

1. <span data-ttu-id="d71cf-161">按一下下方的按鈕：</span><span class="sxs-lookup"><span data-stu-id="d71cf-161">Click the button below:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fdmz%2Fsecure-vnet-dmz%2FvirtualNetwork.azuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
2. <span data-ttu-id="d71cf-162">一旦連結已在 Azure 入口網站中開啟，您必須輸入部分設定的值：</span><span class="sxs-lookup"><span data-stu-id="d71cf-162">Once the link has opened in the Azure portal, you must enter values for some of the settings:</span></span>
   * <span data-ttu-id="d71cf-163">**資源群組**名稱已在參數檔案中定義，因此請在文字方塊中選取 [新建] 並輸入 `ra-public-dmz-network-rg`。</span><span class="sxs-lookup"><span data-stu-id="d71cf-163">The **Resource group** name is already defined in the parameter file, so select **Create New** and enter `ra-public-dmz-network-rg` in the text box.</span></span>
   * <span data-ttu-id="d71cf-164">從 [位置] 下拉式方塊選取區域。</span><span class="sxs-lookup"><span data-stu-id="d71cf-164">Select the region from the **Location** drop down box.</span></span>
   * <span data-ttu-id="d71cf-165">請勿編輯 [範本的根 URI] 或 [參數根 URI] 文字方塊。</span><span class="sxs-lookup"><span data-stu-id="d71cf-165">Do not edit the **Template Root Uri** or the **Parameter Root Uri** text boxes.</span></span>
   * <span data-ttu-id="d71cf-166">從下拉式清單方塊中選取 [作業系統類型]，選取 [Windows] 或 [Linux]。</span><span class="sxs-lookup"><span data-stu-id="d71cf-166">Select the **Os Type** from the drop down box, **windows** or **linux**.</span></span>
   * <span data-ttu-id="d71cf-167">檢閱條款和條件，然後按一下 [我同意上方所述的條款及條件] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="d71cf-167">Review the terms and conditions, then click the **I agree to the terms and conditions stated above** checkbox.</span></span>
   * <span data-ttu-id="d71cf-168">按一下 [購買] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="d71cf-168">Click the **Purchase** button.</span></span>
3. <span data-ttu-id="d71cf-169">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="d71cf-169">Wait for the deployment to complete.</span></span>
4. <span data-ttu-id="d71cf-170">按一下下方的按鈕：</span><span class="sxs-lookup"><span data-stu-id="d71cf-170">Click the button below:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fdmz%2Fsecure-vnet-dmz%2Fworkload.azuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
5. <span data-ttu-id="d71cf-171">一旦連結已在 Azure 入口網站中開啟，您必須輸入部分設定的值：</span><span class="sxs-lookup"><span data-stu-id="d71cf-171">Once the link has opened in the Azure portal, you must enter values for some of the settings:</span></span>
   * <span data-ttu-id="d71cf-172">**資源群組**名稱已在參數檔案中定義，因此請在文字方塊中選取 [新建] 並輸入 `ra-public-dmz-wl-rg`。</span><span class="sxs-lookup"><span data-stu-id="d71cf-172">The **Resource group** name is already defined in the parameter file, so select **Create New** and enter `ra-public-dmz-wl-rg` in the text box.</span></span>
   * <span data-ttu-id="d71cf-173">從 [位置] 下拉式方塊選取區域。</span><span class="sxs-lookup"><span data-stu-id="d71cf-173">Select the region from the **Location** drop down box.</span></span>
   * <span data-ttu-id="d71cf-174">請勿編輯 [範本的根 URI] 或 [參數根 URI] 文字方塊。</span><span class="sxs-lookup"><span data-stu-id="d71cf-174">Do not edit the **Template Root Uri** or the **Parameter Root Uri** text boxes.</span></span>
   * <span data-ttu-id="d71cf-175">檢閱條款和條件，然後按一下 [我同意上方所述的條款及條件] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="d71cf-175">Review the terms and conditions, then click the **I agree to the terms and conditions stated above** checkbox.</span></span>
   * <span data-ttu-id="d71cf-176">按一下 [購買] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="d71cf-176">Click the **Purchase** button.</span></span>
6. <span data-ttu-id="d71cf-177">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="d71cf-177">Wait for the deployment to complete.</span></span>
7. <span data-ttu-id="d71cf-178">按一下下方的按鈕：</span><span class="sxs-lookup"><span data-stu-id="d71cf-178">Click the button below:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fdmz%2Fsecure-vnet-dmz%2Fsecurity.azuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
8. <span data-ttu-id="d71cf-179">一旦連結已在 Azure 入口網站中開啟，您必須輸入部分設定的值：</span><span class="sxs-lookup"><span data-stu-id="d71cf-179">Once the link has opened in the Azure portal, you must enter values for some of the settings:</span></span>
   * <span data-ttu-id="d71cf-180">[資源群組] 名稱已於參數檔中定義，因此，請選取 [使用現有的] 並在文字方塊中輸入 `ra-public-dmz-network-rg`。</span><span class="sxs-lookup"><span data-stu-id="d71cf-180">The **Resource group** name is already defined in the parameter file, so select **Use Existing** and enter `ra-public-dmz-network-rg` in the text box.</span></span>
   * <span data-ttu-id="d71cf-181">從 [位置] 下拉式方塊選取區域。</span><span class="sxs-lookup"><span data-stu-id="d71cf-181">Select the region from the **Location** drop down box.</span></span>
   * <span data-ttu-id="d71cf-182">請勿編輯 [範本的根 URI] 或 [參數根 URI] 文字方塊。</span><span class="sxs-lookup"><span data-stu-id="d71cf-182">Do not edit the **Template Root Uri** or the **Parameter Root Uri** text boxes.</span></span>
   * <span data-ttu-id="d71cf-183">檢閱條款和條件，然後按一下 [我同意上方所述的條款及條件] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="d71cf-183">Review the terms and conditions, then click the **I agree to the terms and conditions stated above** checkbox.</span></span>
   * <span data-ttu-id="d71cf-184">按一下 [購買] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="d71cf-184">Click the **Purchase** button.</span></span>
9. <span data-ttu-id="d71cf-185">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="d71cf-185">Wait for the deployment to complete.</span></span>
10. <span data-ttu-id="d71cf-186">參數檔案中有硬式編碼的所有 VM 的系統管理員使用者名稱和密碼，強烈建議您立即變更這兩項。</span><span class="sxs-lookup"><span data-stu-id="d71cf-186">The parameter files include hard-coded administrator user name and password for all VMs, and it is strongly recommended that you immediately change both.</span></span> <span data-ttu-id="d71cf-187">在 Azure 入口網站中選取部署中的每個 VM，然後按一下 [支援與疑難排解] 刀鋒視窗中的 [重設密碼]。</span><span class="sxs-lookup"><span data-stu-id="d71cf-187">For each VM in the deployment, select it in the Azure portal and then click  **Reset password** in the **Support + troubleshooting** blade.</span></span> <span data-ttu-id="d71cf-188">選取 [模式] 下拉式清單方塊中的 [重設密碼]，然後選取新的[使用者名稱] 和 [密碼]。</span><span class="sxs-lookup"><span data-stu-id="d71cf-188">Select **Reset password** in the **Mode** drop down box, then select a new **User name** and **Password**.</span></span> <span data-ttu-id="d71cf-189">按一下 [更新] 按鈕以儲存。</span><span class="sxs-lookup"><span data-stu-id="d71cf-189">Click the **Update** button to save.</span></span>


[availability-set]: /azure/virtual-machines/virtual-machines-windows-manage-availability
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/dmz/secure-vnet-dmz

[implementing-a-secure-hybrid-network-architecture]: ./secure-vnet-hybrid.md
[iptables]: https://help.ubuntu.com/community/IptablesHowTo
[lb-probe]: /azure/load-balancer/load-balancer-custom-probe-overview
[load-balancer]: /azure/load-balancer/load-balancer-Internet-overview
[network-security-group]: /azure/virtual-network/virtual-networks-nsg

[visio-download]: https://archcenter.blob.core.windows.net/cdn/dmz-reference-architectures.vsdx


[0]: ./images/dmz-public.png "安全混合式網路架構"

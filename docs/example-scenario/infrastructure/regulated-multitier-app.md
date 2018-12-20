---
title: 使用 Azure 上的 Windows 虛擬機器建置安全的 Web 應用程式
description: 透過 Azure 上使用擴展集、應用程式閘道和負載平衡器的 Windows Server，建置安全、多層式 Web 應用程式。
author: iainfoulds
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 4e4d2117fbc46eda46f7ef276a71739e3a79270e
ms.sourcegitcommit: 4ba3304eebaa8c493c3e5307bdd9d723cd90b655
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53307056"
---
# <a name="building-secure-web-applications-with-windows-virtual-machines-on-azure"></a><span data-ttu-id="5e99a-103">使用 Azure 上的 Windows 虛擬機器建置安全的 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="5e99a-103">Building secure web applications with Windows virtual machines on Azure</span></span>

<span data-ttu-id="5e99a-104">此案例提供在 Microsoft Azure 上執行安全、多層式 Web 應用程式的架構和設計指引。</span><span class="sxs-lookup"><span data-stu-id="5e99a-104">This scenario provides architecture and design guidance for running secure, multi-tier web applications on Microsoft Azure.</span></span> <span data-ttu-id="5e99a-105">在此範例中，ASP.NET 應用程式會使用虛擬機器安全地連線到受保護的後端 Microsoft SQL Server 叢集。</span><span class="sxs-lookup"><span data-stu-id="5e99a-105">In this example, an ASP.NET application securely connects to a protected back-end Microsoft SQL Server cluster using virtual machines.</span></span>

<span data-ttu-id="5e99a-106">傳統上，組織必須維護舊版內部部署應用程式和服務，才能提供安全的基礎結構。</span><span class="sxs-lookup"><span data-stu-id="5e99a-106">Traditionally, organizations had to maintain legacy on-premises applications and services to provide a secure infrastructure.</span></span> <span data-ttu-id="5e99a-107">藉由在 Azure 中安全地部署這些 Windows Server 應用程式，組織可以將其部署現代化，從而降低內部部署成本和管理額外負荷。</span><span class="sxs-lookup"><span data-stu-id="5e99a-107">By deploying these Windows Server applications securely in Azure, organizations can modernize their deployments and reduce their on-premises operating costs and management overhead.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="5e99a-108">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="5e99a-108">Relevant use cases</span></span>

<span data-ttu-id="5e99a-109">可能適用此案例的一些範例：</span><span class="sxs-lookup"><span data-stu-id="5e99a-109">A few examples of where this scenario may apply:</span></span>

* <span data-ttu-id="5e99a-110">在安全的雲端環境中將應用程式部署現代化。</span><span class="sxs-lookup"><span data-stu-id="5e99a-110">Modernizing application deployments in a secure cloud environment.</span></span>
* <span data-ttu-id="5e99a-111">減少舊版內部部署應用程式和服務的管理額外負荷。</span><span class="sxs-lookup"><span data-stu-id="5e99a-111">Reducing the management overhead of legacy on-premises applications and services.</span></span>
* <span data-ttu-id="5e99a-112">利用新的應用程式平台來改善病患的醫療保健與體驗。</span><span class="sxs-lookup"><span data-stu-id="5e99a-112">Improving patient healthcare and experience with new application platforms.</span></span>

## <a name="architecture"></a><span data-ttu-id="5e99a-113">架構</span><span class="sxs-lookup"><span data-stu-id="5e99a-113">Architecture</span></span>

![受規範產業的多層式 Windows Server 應用程式相關 Azure 元件的架構概觀][architecture]

<span data-ttu-id="5e99a-115">此案例顯示前端 Web 應用程式如何連線至後端資料庫 (這兩者皆在 Windows Server 2016 上執行)。</span><span class="sxs-lookup"><span data-stu-id="5e99a-115">This scenario shows a front-end web application connecting to a back-end database, both running on Windows Server 2016.</span></span> <span data-ttu-id="5e99a-116">整個案例的資料流程如下所示：</span><span class="sxs-lookup"><span data-stu-id="5e99a-116">The data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="5e99a-117">使用者會透過 Azure 應用程式閘道存取前端 ASP.NET 應用程式。</span><span class="sxs-lookup"><span data-stu-id="5e99a-117">Users access the front-end ASP.NET application through an Azure Application Gateway.</span></span>
2. <span data-ttu-id="5e99a-118">應用程式閘道會將流量散發至 Azure 虛擬機器擴展集內的 VM 執行個體。</span><span class="sxs-lookup"><span data-stu-id="5e99a-118">The Application Gateway distributes traffic to VM instances within an Azure virtual machine scale set.</span></span>
3. <span data-ttu-id="5e99a-119">應用程式會透過 Azure 負載平衡器連線到後端層中的 Microsoft SQL Server 叢集。</span><span class="sxs-lookup"><span data-stu-id="5e99a-119">The application connects to Microsoft SQL Server cluster in a back-end tier via an Azure load balancer.</span></span> <span data-ttu-id="5e99a-120">這些後端 SQL Server 執行個體位於不同的 Azure 虛擬網路，受到限制流量的網路安全性群組規則保護。</span><span class="sxs-lookup"><span data-stu-id="5e99a-120">These back-end SQL Server instances are in a separate Azure virtual network, secured by network security group rules that limit traffic flow.</span></span>
4. <span data-ttu-id="5e99a-121">負載平衡器會將 SQL Server 流量散發到另一個虛擬機器擴展集中的 VM 執行個體。</span><span class="sxs-lookup"><span data-stu-id="5e99a-121">The load balancer distributes SQL Server traffic to VM instances in another virtual machine scale set.</span></span>
5. <span data-ttu-id="5e99a-122">Azure Blob 儲存體會在後端層中作為 SQL Server 叢集的[雲端見證][cloud-witness]。</span><span class="sxs-lookup"><span data-stu-id="5e99a-122">Azure Blob Storage acts as a [cloud witness][cloud-witness] for the SQL Server cluster in the back-end tier.</span></span> <span data-ttu-id="5e99a-123">已利用 Azure 儲存體的 VNet 服務端點啟用 VNet 內的連線。</span><span class="sxs-lookup"><span data-stu-id="5e99a-123">The connection from within the VNet is enabled with a VNet Service Endpoint for Azure Storage.</span></span>

### <a name="components"></a><span data-ttu-id="5e99a-124">元件</span><span class="sxs-lookup"><span data-stu-id="5e99a-124">Components</span></span>

* <span data-ttu-id="5e99a-125">[Azure 應用程式閘道][appgateway-docs]是第 7 層 web 流量負載平衡器，會感知應用程式且可以根據特定的路由規則來散發流量。</span><span class="sxs-lookup"><span data-stu-id="5e99a-125">[Azure Application Gateway][appgateway-docs] is a layer 7 web traffic load balancer that is application-aware and can distribute traffic based on specific routing rules.</span></span> <span data-ttu-id="5e99a-126">應用程式閘道也可以處理 SSL 卸載，以獲得改善的 Web 伺服器效能。</span><span class="sxs-lookup"><span data-stu-id="5e99a-126">App Gateway can also handle SSL offloading for improved web server performance.</span></span>
* <span data-ttu-id="5e99a-127">[Azure 虛擬網路][vnet-docs]可讓 VM 等資源安全地互相通訊，以及與網際網路和內部部署網路通訊。</span><span class="sxs-lookup"><span data-stu-id="5e99a-127">[Azure Virtual Network][vnet-docs] allows resources such as VMs to securely communicate with each other, the Internet, and on-premises networks.</span></span> <span data-ttu-id="5e99a-128">虛擬網路會提供隔離與分割、篩選與路由流量，並允許位置之間的連線。</span><span class="sxs-lookup"><span data-stu-id="5e99a-128">Virtual networks provide isolation and segmentation, filter and route traffic, and allow connection between locations.</span></span> <span data-ttu-id="5e99a-129">此案例中會使用與適當 NSG 結合的兩個虛擬網路，來提供[周邊網路][ dmz] (DMZ) 和應用程式元件隔離。</span><span class="sxs-lookup"><span data-stu-id="5e99a-129">Two virtual networks combined with the appropriate NSGs are used in this scenario to provide a [demilitarized zone][dmz] (DMZ) and isolation of the application components.</span></span> <span data-ttu-id="5e99a-130">虛擬網路對等互連會將兩個網路連線在一起。</span><span class="sxs-lookup"><span data-stu-id="5e99a-130">Virtual network peering connects the two networks together.</span></span>
* <span data-ttu-id="5e99a-131">[Azure 虛擬機器擴展集][scaleset-docs]可讓您建立和管理一組負載平衡的相同 VM。</span><span class="sxs-lookup"><span data-stu-id="5e99a-131">[Azure virtual machine scale set][scaleset-docs] lets you create and manager a group of identical, load balanced, VMs.</span></span> <span data-ttu-id="5e99a-132">VM 執行個體的數目可以自動增加或減少，以因應需求或已定義的排程。</span><span class="sxs-lookup"><span data-stu-id="5e99a-132">The number of VM instances can automatically increase or decrease in response to demand or a defined schedule.</span></span> <span data-ttu-id="5e99a-133">此案例中會使用兩個不同的虛擬機器擴展集 - 一個用於前端 ASP.NET 應用程式執行個體，另一個則用於後端 SQL Server 叢集 VM 執行個體。</span><span class="sxs-lookup"><span data-stu-id="5e99a-133">Two separate virtual machine scale sets are used in this scenario - one for the front-end ASP.NET application instances, and one for the back-end SQL Server cluster VM instances.</span></span> <span data-ttu-id="5e99a-134">PowerShell 預期的狀態設定 (DSC) 或 Azure 自訂指令碼擴充功能可用來佈建使用所需軟體和組態設定的 VM 執行個體。</span><span class="sxs-lookup"><span data-stu-id="5e99a-134">PowerShell desired state configuration (DSC) or the Azure custom script extension can be used to provision the VM instances with the required software and configuration settings.</span></span>
* <span data-ttu-id="5e99a-135">[Azure 網路安全性群組 (NSG)][nsg-docs] 包含一些安全性規則，可根據來源或目的地 IP 位址、連接埠和通訊協定允許或拒絕輸入或輸出網路流量。</span><span class="sxs-lookup"><span data-stu-id="5e99a-135">[Azure network security groups][nsg-docs] contain a list of security rules that allow or deny inbound or outbound network traffic based on source or destination IP address, port, and protocol.</span></span> <span data-ttu-id="5e99a-136">此案例中的虛擬網路會受到網路安全性群組規則保護，這些規則會限制應用程式元件之間的流量。</span><span class="sxs-lookup"><span data-stu-id="5e99a-136">The virtual networks in this scenario are secured with network security group rules that restrict the flow of traffic between the application components.</span></span>
* <span data-ttu-id="5e99a-137">[Azure 負載平衡器][loadbalancer-docs]會根據規則和健康情況探查來散發輸入流量。</span><span class="sxs-lookup"><span data-stu-id="5e99a-137">[Azure load balancer][loadbalancer-docs] distributes inbound traffic according to rules and health probes.</span></span> <span data-ttu-id="5e99a-138">對於所有 TCP 和 UDP 應用程式，負載平衡器可提供低延遲和高輸送量，且最多可相應增加為數百萬個流程。</span><span class="sxs-lookup"><span data-stu-id="5e99a-138">A load balancer provides low latency and high throughput, and scales up to millions of flows for all TCP and UDP applications.</span></span> <span data-ttu-id="5e99a-139">此案例中會使用內部負載平衡器，從前端應用程式層將流量散發到後端 SQL Server 叢集。</span><span class="sxs-lookup"><span data-stu-id="5e99a-139">An internal load balancer is used in this scenario to distribute traffic from the front-end application tier to the back-end SQL Server cluster.</span></span>
* <span data-ttu-id="5e99a-140">[Azure Blob 儲存體][cloudwitness-docs]會作為 SQL Server 叢集的雲端見證。</span><span class="sxs-lookup"><span data-stu-id="5e99a-140">[Azure Blob Storage][cloudwitness-docs] acts a Cloud Witness location for the SQL Server cluster.</span></span> <span data-ttu-id="5e99a-141">此見證用於需要額外投票來決定仲裁的叢集作業及決策。</span><span class="sxs-lookup"><span data-stu-id="5e99a-141">This witness is used for cluster operations and decisions that require an additional vote to decide quorum.</span></span> <span data-ttu-id="5e99a-142">使用雲端見證不需要額外的 VM 來作為傳統的檔案共用見證。</span><span class="sxs-lookup"><span data-stu-id="5e99a-142">Using Cloud Witness removes the need for an additional VM to act as a traditional File Share Witness.</span></span>

### <a name="alternatives"></a><span data-ttu-id="5e99a-143">替代項目</span><span class="sxs-lookup"><span data-stu-id="5e99a-143">Alternatives</span></span>

* <span data-ttu-id="5e99a-144">您可以交換使用 Linux 和 Windows，因為基礎結構並不相依於作業系統。</span><span class="sxs-lookup"><span data-stu-id="5e99a-144">Linux and Windows can be used interchangeably since the infrastructure isn't dependent on the operating system.</span></span>

* <span data-ttu-id="5e99a-145">[適用於 Linux 的 SQL Server][sql-linux] 可取代後端資料存放區。</span><span class="sxs-lookup"><span data-stu-id="5e99a-145">[SQL Server for Linux][sql-linux] can replace the back-end data store.</span></span>

* <span data-ttu-id="5e99a-146">[Cosmos DB](/azure/cosmos-db/introduction) 是資料存放區的替代項目。</span><span class="sxs-lookup"><span data-stu-id="5e99a-146">[Cosmos DB](/azure/cosmos-db/introduction) is another alternative for the data store.</span></span>

## <a name="considerations"></a><span data-ttu-id="5e99a-147">考量</span><span class="sxs-lookup"><span data-stu-id="5e99a-147">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="5e99a-148">可用性</span><span class="sxs-lookup"><span data-stu-id="5e99a-148">Availability</span></span>

<span data-ttu-id="5e99a-149">此案例中的 VM 執行個體會部署在可用性區域之間。</span><span class="sxs-lookup"><span data-stu-id="5e99a-149">The VM instances in this scenario are deployed across Availability Zones.</span></span> <span data-ttu-id="5e99a-150">每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。</span><span class="sxs-lookup"><span data-stu-id="5e99a-150">Each zone is made up of one or more datacenters equipped with independent power, cooling, and networking.</span></span> <span data-ttu-id="5e99a-151">所有已啟用的區域中至少有三個可用的區域。</span><span class="sxs-lookup"><span data-stu-id="5e99a-151">A minimum of three zones are available in all enabled regions.</span></span> <span data-ttu-id="5e99a-152">這個跨區域 VM 執行個體的分佈能為應用程式層提供高可用性。</span><span class="sxs-lookup"><span data-stu-id="5e99a-152">This distribution of VM instances across zones provides high availability to the application tiers.</span></span> <span data-ttu-id="5e99a-153">如需詳細資訊，請參閱[什麼是 Azure 中的可用性區域？][azureaz-docs]</span><span class="sxs-lookup"><span data-stu-id="5e99a-153">For more information, see [what are Availability Zones in Azure?][azureaz-docs]</span></span>

<span data-ttu-id="5e99a-154">您可以將資料庫層設定為使用 Always On 可用性群組。</span><span class="sxs-lookup"><span data-stu-id="5e99a-154">The database tier can be configured to use Always On availability groups.</span></span> <span data-ttu-id="5e99a-155">使用此 SQL Server 設定時，會使用最多八個次要資料庫來設定叢集內的一個主要資料庫。</span><span class="sxs-lookup"><span data-stu-id="5e99a-155">With this SQL Server configuration, one primary database within a cluster is configured with up to eight secondary databases.</span></span> <span data-ttu-id="5e99a-156">如果主要資料庫發生問題，叢集就會容錯移轉至其中一個次要資料庫，讓應用程式能夠繼續使用。</span><span class="sxs-lookup"><span data-stu-id="5e99a-156">If an issue occurs with the primary database, the cluster fails over to one of the secondary databases, which allows the application to continue to be available.</span></span> <span data-ttu-id="5e99a-157">如需詳細資訊，請參閱[適用於 SQL Server 的 Always On 可用性群組概觀][sqlalwayson-docs]。</span><span class="sxs-lookup"><span data-stu-id="5e99a-157">For more information, see [Overview of Always On availability groups for SQL Server][sqlalwayson-docs].</span></span>

<span data-ttu-id="5e99a-158">如需詳細的可用性指引，請參閱 Azure 架構中心內的[可用性檢查清單][availability]。</span><span class="sxs-lookup"><span data-stu-id="5e99a-158">For more availability guidance, see the [availability checklist][availability] in the Azure Architecture Center.</span></span>

### <a name="scalability"></a><span data-ttu-id="5e99a-159">延展性</span><span class="sxs-lookup"><span data-stu-id="5e99a-159">Scalability</span></span>

<span data-ttu-id="5e99a-160">此案例會使用前端和後端元件的虛擬機器擴展集。</span><span class="sxs-lookup"><span data-stu-id="5e99a-160">This scenario uses virtual machine scale sets for the front-end and back-end components.</span></span> <span data-ttu-id="5e99a-161">使用擴展集時，執行前端應用程式層的 VM 執行個體數目可以視回應客戶需求，或根據定義的排程來自動調整。</span><span class="sxs-lookup"><span data-stu-id="5e99a-161">With scale sets, the number of VM instances that run the front-end application tier can automatically scale in response to customer demand, or based on a defined schedule.</span></span> <span data-ttu-id="5e99a-162">如需詳細資訊，請參閱[使用虛擬機器擴展集自動調整概觀][vmssautoscale-docs]。</span><span class="sxs-lookup"><span data-stu-id="5e99a-162">For more information, see [Overview of autoscale with virtual machine scale sets][vmssautoscale-docs].</span></span>

<span data-ttu-id="5e99a-163">如需其他延展性主題，請參閱 Azure Architecture Center 中的[延展性檢查清單][scalability]。</span><span class="sxs-lookup"><span data-stu-id="5e99a-163">For other scalability topics, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="5e99a-164">安全性</span><span class="sxs-lookup"><span data-stu-id="5e99a-164">Security</span></span>

<span data-ttu-id="5e99a-165">所有流入前端應用程式層的虛擬網路流量都受到網路安全性群組的保護。</span><span class="sxs-lookup"><span data-stu-id="5e99a-165">All the virtual network traffic into the front-end application tier and protected by network security groups.</span></span> <span data-ttu-id="5e99a-166">規則會限制流量，以便只有前端應用程式層 VM 執行個體可以存取後端資料庫層。</span><span class="sxs-lookup"><span data-stu-id="5e99a-166">Rules limit the flow of traffic so that only the front-end application tier VM instances can access the back-end database tier.</span></span> <span data-ttu-id="5e99a-167">不允許資料庫層的輸出網際網路流量。</span><span class="sxs-lookup"><span data-stu-id="5e99a-167">No outbound Internet traffic is allowed from the database tier.</span></span> <span data-ttu-id="5e99a-168">若要降低攻擊使用量，請勿開啟任何直接的遠端管理連接埠。</span><span class="sxs-lookup"><span data-stu-id="5e99a-168">To reduce the attack footprint, no direct remote management ports are open.</span></span> <span data-ttu-id="5e99a-169">如需詳細資訊，請參閱 [Azure 網路安全性群組][nsg-docs]。</span><span class="sxs-lookup"><span data-stu-id="5e99a-169">For more information, see [Azure network security groups][nsg-docs].</span></span>

<span data-ttu-id="5e99a-170">若要檢視部署支付卡產業資料安全性標準 (PCI DSS 3.2) 的指導方針[符合規範的基礎結構][pci-dss]。</span><span class="sxs-lookup"><span data-stu-id="5e99a-170">To view guidance on deploying Payment Card Industry Data Security Standards (PCI DSS 3.2) [compliant infrastructure][pci-dss].</span></span> <span data-ttu-id="5e99a-171">如需設計安全案例的一般指引，請參閱 [Azure 安全性文件][security]。</span><span class="sxs-lookup"><span data-stu-id="5e99a-171">For general guidance on designing secure scenarios, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="5e99a-172">災害復原</span><span class="sxs-lookup"><span data-stu-id="5e99a-172">Resiliency</span></span>

<span data-ttu-id="5e99a-173">此案例結合使用「可用性區域」和「虛擬機器擴展集」，會使用 Azure 應用程式閘道和負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="5e99a-173">In combination with the use of Availability Zones and virtual machine scale sets, this scenario uses Azure Application Gateway and load balancer.</span></span> <span data-ttu-id="5e99a-174">這兩個網路元件會將流量散發至已連線的 VM 執行個體，並包含健康情況探查，可確保流量只會散發到狀況良好的 VM。</span><span class="sxs-lookup"><span data-stu-id="5e99a-174">These two networking components distribute traffic to the connected VM instances, and include health probes that ensure traffic is only distributed to healthy VMs.</span></span> <span data-ttu-id="5e99a-175">在主動被動組態中，會設定兩個應用程式閘道執行個體，並使用區域備援負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="5e99a-175">Two Application Gateway instances are configured in an active-passive configuration, and a zone-redundant load balancer is used.</span></span> <span data-ttu-id="5e99a-176">此設定會讓網路資源和應用程式有彈性地處理問題，否則會中斷流量並影響使用者存取。</span><span class="sxs-lookup"><span data-stu-id="5e99a-176">This configuration makes the networking resources and application resilient to issues that would otherwise disrupt traffic and impact end-user access.</span></span>

<span data-ttu-id="5e99a-177">如需設計彈性案例的一般指引，請參閱[為 Azure 設計有彈性的應用程式][resiliency]。</span><span class="sxs-lookup"><span data-stu-id="5e99a-177">For general guidance on designing resilient scenarios, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="5e99a-178">部署案例</span><span class="sxs-lookup"><span data-stu-id="5e99a-178">Deploy the scenario</span></span>

<span data-ttu-id="5e99a-179">**必要條件。**</span><span class="sxs-lookup"><span data-stu-id="5e99a-179">**Prerequisites.**</span></span>

* <span data-ttu-id="5e99a-180">您必須具有現有的 Azure 帳戶。</span><span class="sxs-lookup"><span data-stu-id="5e99a-180">You must have an existing Azure account.</span></span> <span data-ttu-id="5e99a-181">如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。</span><span class="sxs-lookup"><span data-stu-id="5e99a-181">If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.</span></span>
* <span data-ttu-id="5e99a-182">若要將 SQL Server 叢集部署到後端擴展集，您需要 Azure Active Directory (AD) Domain Services 中的網域。</span><span class="sxs-lookup"><span data-stu-id="5e99a-182">To deploy a SQL Server cluster into the back-end scale set, you would need a domain in Azure Active Directory (AD) Domain Services.</span></span>

<span data-ttu-id="5e99a-183">若要使用 Azure Resource Manager 範本部署此案例的核心基礎結構，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="5e99a-183">To deploy the core infrastructure for this scenario with an Azure Resource Manager template, perform the following steps.</span></span>

1. <span data-ttu-id="5e99a-184">選取 [部署至 Azure] 按鈕：</span><span class="sxs-lookup"><span data-stu-id="5e99a-184">Select the **Deploy to Azure** button:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Finfrastructure%2Fregulated-multitier-app%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. <span data-ttu-id="5e99a-185">等待 Azure 入口網站中的範本部署開啟，然後完成下列步驟：</span><span class="sxs-lookup"><span data-stu-id="5e99a-185">Wait for the template deployment to open in the Azure portal, then complete the following steps:</span></span>
   * <span data-ttu-id="5e99a-186">選擇 [新建] 資源群組，然後在文字方塊中提供名稱，例如 myWindowsscenario。</span><span class="sxs-lookup"><span data-stu-id="5e99a-186">Choose to **Create new** resource group, then provide a name such as *myWindowsscenario* in the text box.</span></span>
   * <span data-ttu-id="5e99a-187">從 [位置] 下拉式方塊選取區域。</span><span class="sxs-lookup"><span data-stu-id="5e99a-187">Select a region from the **Location** drop-down box.</span></span>
   * <span data-ttu-id="5e99a-188">提供虛擬機器擴展集執行個體的使用者名稱和安全的密碼。</span><span class="sxs-lookup"><span data-stu-id="5e99a-188">Provide a username and secure password for the virtual machine scale set instances.</span></span>
   * <span data-ttu-id="5e99a-189">檢閱條款及條件，然後按一下 [我同意上方所述的條款及條件]。</span><span class="sxs-lookup"><span data-stu-id="5e99a-189">Review the terms and conditions, then check **I agree to the terms and conditions stated above**.</span></span>
   * <span data-ttu-id="5e99a-190">選取 [購買] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="5e99a-190">Select the **Purchase** button.</span></span>

<span data-ttu-id="5e99a-191">需要 15-20 分鐘的時間才能完成部署。</span><span class="sxs-lookup"><span data-stu-id="5e99a-191">It can take 15-20 minutes for the deployment to complete.</span></span>

## <a name="pricing"></a><span data-ttu-id="5e99a-192">價格</span><span class="sxs-lookup"><span data-stu-id="5e99a-192">Pricing</span></span>

<span data-ttu-id="5e99a-193">為了探索執行此案例的成本，所有服務會在成本計算機中預先設定。</span><span class="sxs-lookup"><span data-stu-id="5e99a-193">To explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="5e99a-194">若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的流量。</span><span class="sxs-lookup"><span data-stu-id="5e99a-194">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="5e99a-195">我們根據執行應用程式的擴展集 VM 執行個體數目，提供了 3 個範例成本設定檔。</span><span class="sxs-lookup"><span data-stu-id="5e99a-195">We have provided three sample cost profiles based on the number of scale set VM instances that run your applications.</span></span>

* <span data-ttu-id="5e99a-196">[小型][small-pricing]：這個定價範例與兩個前端和兩個後端 VM 執行個體相互關聯。</span><span class="sxs-lookup"><span data-stu-id="5e99a-196">[Small][small-pricing]: this pricing example correlates to two front-end and two back-end VM instances.</span></span>
* <span data-ttu-id="5e99a-197">[中型][medium-pricing]：這個定價範例與 20 個前端和 5 個後端 VM 執行個體相互關聯。</span><span class="sxs-lookup"><span data-stu-id="5e99a-197">[Medium][medium-pricing]: this pricing example correlates to 20 front-end and 5 back-end VM instances.</span></span>
* <span data-ttu-id="5e99a-198">[大型][large-pricing]：這個定價範例與 100 個前端和 10 個後端 VM 執行個體相互關聯。</span><span class="sxs-lookup"><span data-stu-id="5e99a-198">[Large][large-pricing]: this pricing example correlates to 100 front-end and 10 back-end VM instances.</span></span>

## <a name="related-resources"></a><span data-ttu-id="5e99a-199">相關資源</span><span class="sxs-lookup"><span data-stu-id="5e99a-199">Related resources</span></span>

<span data-ttu-id="5e99a-200">此案例中使用執行 Microsoft SQL Server 叢集的後端虛擬機器擴展集。</span><span class="sxs-lookup"><span data-stu-id="5e99a-200">This scenario used a back-end virtual machine scale set that runs a Microsoft SQL Server cluster.</span></span> <span data-ttu-id="5e99a-201">Cosmos DB 也可用來作為應用程式資料的可調整及安全資料庫層。</span><span class="sxs-lookup"><span data-stu-id="5e99a-201">Cosmos DB could also be used as a scalable and secure database tier for the application data.</span></span> <span data-ttu-id="5e99a-202">[Azure 虛擬網路服務][vnetendpoint-docs]端點可讓您將重要的 Azure 服務資源只放到您的虛擬網路保護。</span><span class="sxs-lookup"><span data-stu-id="5e99a-202">An [Azure virtual network service endpoint][vnetendpoint-docs] allows you to secure your critical Azure service resources to only your virtual networks.</span></span> <span data-ttu-id="5e99a-203">在此案例中，VNet 端點可讓您保護前端應用程式層與 Cosmos DB 之間的流量。</span><span class="sxs-lookup"><span data-stu-id="5e99a-203">In this scenario, VNet endpoints allow you to secure traffic between the front-end application tier and Cosmos DB.</span></span> <span data-ttu-id="5e99a-204">如需詳細資訊，請參閱 [Azure Cosmos DB 概觀](/azure/cosmos-db/introduction)。</span><span class="sxs-lookup"><span data-stu-id="5e99a-204">For more information, see the [Azure Cosmos DB overview](/azure/cosmos-db/introduction).</span></span>

<span data-ttu-id="5e99a-205">如需詳細的實作指南，請檢閱[使用 SQL Server 的多層式架構 (N-Tier) 應用程式參考架構][ntiersql-ra]。</span><span class="sxs-lookup"><span data-stu-id="5e99a-205">For more detailed implementation guides, review the [reference architecture for N-tier applications using SQL Server][ntiersql-ra].</span></span>

<!-- links -->
[appgateway-docs]: /azure/application-gateway/overview
[architecture]: ./media/architecture-regulated-multitier-app.png
[autoscaling]: /azure/architecture/best-practices/auto-scaling
[availability]: ../../checklist/availability.md
[azureaz-docs]: /azure/availability-zones/az-overview
[cloudwitness-docs]: /windows-server/failover-clustering/deploy-cloud-witness
[loadbalancer-docs]: /azure/load-balancer/load-balancer-overview
[nsg-docs]: /azure/virtual-network/security-overview
[ntiersql-ra]: /azure/architecture/reference-architectures/n-tier/n-tier-sql-server
[resiliency]: /azure/architecture/resiliency/ 
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability 
[scaleset-docs]: /azure/virtual-machine-scale-sets/overview
[sqlalwayson-docs]: /sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server
[vmssautoscale-docs]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview
[vnet-docs]: /azure/virtual-network/virtual-networks-overview
[vnetendpoint-docs]: /azure/virtual-network/virtual-network-service-endpoints-overview
[pci-dss]: /azure/security/blueprints/pcidss-iaaswa-overview
[dmz]: /azure/virtual-network/virtual-networks-dmz-nsg
[sql-linux]: /sql/linux/sql-server-linux-overview?view=sql-server-linux-2017
[cloud-witness]: /windows-server/failover-clustering/deploy-cloud-witness
[small-pricing]: https://azure.com/e/711bbfcbbc884ef8aa91cdf0f2caff72
[medium-pricing]: https://azure.com/e/b622d82d79b34b8398c4bce35477856f
[large-pricing]: https://azure.com/e/1d99d8b92f90496787abecffa1473a93

---
title: 適用於開發/測試工作負載的 SAP
description: 開發/測試環境的 SAP 案例
author: AndrewDibbins
ms.date: 7/11/18
ms.openlocfilehash: d0f266e40969cf4782e69041889a686387499722
ms.sourcegitcommit: c49aeef818d7dfe271bc4128b230cfc676f05230
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2018
ms.locfileid: "44389174"
---
# <a name="sap-for-devtest-workloads"></a><span data-ttu-id="1d581-103">適用於開發/測試工作負載的 SAP</span><span class="sxs-lookup"><span data-stu-id="1d581-103">SAP for dev/test workloads</span></span>

<span data-ttu-id="1d581-104">此範例提供如何在 Azure 上執行 Windows 或 Linux 環境中的 SAP NetWeaver 開發/測試實作指引。</span><span class="sxs-lookup"><span data-stu-id="1d581-104">This example provides guidance for how to run a dev/test implementation of SAP NetWeaver in a Windows or Linux environment on Azure.</span></span> <span data-ttu-id="1d581-105">所使用的資料庫是 AnyDB，這是任何支援 DBMS 的 SAP 字詞 (並非 SAP HANA)。</span><span class="sxs-lookup"><span data-stu-id="1d581-105">The database used is AnyDB, the SAP term for any supported DBMS (that isn't SAP HANA).</span></span> <span data-ttu-id="1d581-106">因為此架構是設計用於非生產環境，它僅使用單一虛擬機器 (VM) 部署，且可以變更其大小，以容納貴組織的需求。</span><span class="sxs-lookup"><span data-stu-id="1d581-106">Because this architecture is designed for non-production environments, it's deployed with just a single virtual machine (VM) and it's size can be changed to accommodate your organization's needs.</span></span>

<span data-ttu-id="1d581-107">如需生產環境使用案例，請檢閱以下提供的 SAP 參考架構：</span><span class="sxs-lookup"><span data-stu-id="1d581-107">For production use cases review the SAP reference architectures available below:</span></span>

* <span data-ttu-id="1d581-108">[適用於 AnyDB 的 SAP NetWeaver][sap-netweaver]</span><span class="sxs-lookup"><span data-stu-id="1d581-108">[SAP netweaver for AnyDB][sap-netweaver]</span></span>
* <span data-ttu-id="1d581-109">[SAP S/4Hana][sap-hana]</span><span class="sxs-lookup"><span data-stu-id="1d581-109">[SAP S/4Hana][sap-hana]</span></span>
* <span data-ttu-id="1d581-110">[Azure 上的 SAP 大型執行個體][sap-large]</span><span class="sxs-lookup"><span data-stu-id="1d581-110">[SAP on Azure large instances][sap-large]</span></span>

## <a name="related-use-cases"></a><span data-ttu-id="1d581-111">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="1d581-111">Related use cases</span></span>

<span data-ttu-id="1d581-112">請針對下列使用案例考慮此案例：</span><span class="sxs-lookup"><span data-stu-id="1d581-112">Consider this scenario for the following use cases:</span></span>

* <span data-ttu-id="1d581-113">非關鍵性的 SAP 非產能工作負載 (沙箱、開發、測試、品質保證)</span><span class="sxs-lookup"><span data-stu-id="1d581-113">Non-critical SAP non-productive workloads (sandbox, development, test, quality assurance)</span></span>
* <span data-ttu-id="1d581-114">非關鍵性的 SAP Business One 工作負載</span><span class="sxs-lookup"><span data-stu-id="1d581-114">Non-critical SAP business one workloads</span></span>

## <a name="architecture"></a><span data-ttu-id="1d581-115">架構</span><span class="sxs-lookup"><span data-stu-id="1d581-115">Architecture</span></span>

![圖表](media/sap-2tier/SAP-Infra-2Tier_finalversion.png)

<span data-ttu-id="1d581-117">此案例中會討論佈建單一 SAP 系統資料庫和單一虛擬機器上的 SAP 應用程式伺服器，整個案例的資料流程如下所示：</span><span class="sxs-lookup"><span data-stu-id="1d581-117">This scenario covers the provision of a single SAP system database and SAP application Server on a single virtual machine, the data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="1d581-118">展示層中的客戶會使用他們的 SAP GUI，或是其他內部部署的使用者介面 (Internet Explorer、Excel 或其他 Web 應用程式)，來存取以 Azure 為基礎的 SAP 系統。</span><span class="sxs-lookup"><span data-stu-id="1d581-118">Customers from the Presentation Tier use their SAP GUI, or other user interfaces (Internet Explorer, Excel, or other web application) on premise to access the Azure-based SAP system.</span></span>
2. <span data-ttu-id="1d581-119">會透過使用已建立的 Express Route 來提供連線能力。</span><span class="sxs-lookup"><span data-stu-id="1d581-119">Connectivity is provided through the use of an established Express Route.</span></span> <span data-ttu-id="1d581-120">Azure 中的 Express Route 連線會在 Express Route 閘道終止。</span><span class="sxs-lookup"><span data-stu-id="1d581-120">The Express Route connection is terminated in Azure at the Express Route Gateway.</span></span> <span data-ttu-id="1d581-121">網路流量會透過 Express Route 閘道路由至閘道子網路，以及從閘道子網路路由至應用程式層輪輻子網路 (請參閱 [hub-spoke][hub-spoke] 模式)，然後透過網路安全性群組路由至 SAP 應用程式虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="1d581-121">Network traffic routes through the Express Route gateway to the Gateway Subnet and from the gateway subnet to the Application Tier Spoke subnet (see the [hub-spoke][hub-spoke] pattern) and via a Network Security Gateway to the SAP application virtual machine.</span></span>
3. <span data-ttu-id="1d581-122">身分識別管理伺服器會提供驗證服務。</span><span class="sxs-lookup"><span data-stu-id="1d581-122">The identity management servers provide authentication services.</span></span>
4. <span data-ttu-id="1d581-123">「跳躍箱」會提供本機管理功能。</span><span class="sxs-lookup"><span data-stu-id="1d581-123">The jump box provides local management capabilities.</span></span>

### <a name="components"></a><span data-ttu-id="1d581-124">元件</span><span class="sxs-lookup"><span data-stu-id="1d581-124">Components</span></span>

* <span data-ttu-id="1d581-125">[資源群組](/azure/azure-resource-manager/resource-group-overview#resource-groups)是 Azure 資源的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="1d581-125">[Resource Groups](/azure/azure-resource-manager/resource-group-overview#resource-groups) is a logical container for Azure resources.</span></span>
* <span data-ttu-id="1d581-126">[虛擬網路](/azure/virtual-network/virtual-networks-overview)是 Azure 內的網路通訊基礎</span><span class="sxs-lookup"><span data-stu-id="1d581-126">[Virtual Networks](/azure/virtual-network/virtual-networks-overview) is the basis of network communications within Azure</span></span>
* <span data-ttu-id="1d581-127">[虛擬機器](/azure/virtual-machines/windows/overview) Azure 虛擬機器使用 Windows 或 Linux Server 提供隨選、高度可調整、安全且虛擬化的基礎結構</span><span class="sxs-lookup"><span data-stu-id="1d581-127">[Virtual Machine](/azure/virtual-machines/windows/overview) Azure Virtual Machines provides on-demand, high-scale, secure, virtualized infrastructure using Windows or Linux Server</span></span>
* <span data-ttu-id="1d581-128">[Express Route](/azure/expressroute/expressroute-introduction) 可讓您透過連線提供者所提供的私人連線，將內部部署網路延伸至 Microsoft 雲端。</span><span class="sxs-lookup"><span data-stu-id="1d581-128">[Express Route](/azure/expressroute/expressroute-introduction) lets you extend your on-premises networks into the Microsoft cloud over a private connection facilitated by a connectivity provider.</span></span>
* <span data-ttu-id="1d581-129">[網路安全性群組](/azure/virtual-network/security-overview)可讓您將網路流量限制為虛擬網路中的資源。</span><span class="sxs-lookup"><span data-stu-id="1d581-129">[Network Security Group](/azure/virtual-network/security-overview) lets you limit network traffic to resources in a virtual network.</span></span> <span data-ttu-id="1d581-130">網路安全性群組包含一些安全性規則，可根據來源或目的地 IP 位址、連接埠和通訊協定允許或拒絕輸入或輸出網路流量。</span><span class="sxs-lookup"><span data-stu-id="1d581-130">A network security group contains a list of security rules that allow or deny inbound or outbound network traffic based on source or destination IP address, port, and protocol.</span></span> 

## <a name="considerations"></a><span data-ttu-id="1d581-131">考量</span><span class="sxs-lookup"><span data-stu-id="1d581-131">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="1d581-132">可用性</span><span class="sxs-lookup"><span data-stu-id="1d581-132">Availability</span></span>

 <span data-ttu-id="1d581-133">Microsoft 會提供單一 VM 執行個體的服務等級協定 (SLA)。</span><span class="sxs-lookup"><span data-stu-id="1d581-133">Microsoft offers a service level agreement (SLA) for single VM instances.</span></span> <span data-ttu-id="1d581-134">如需關於適用於虛擬機器的 Microsoft Azure 服務等級協定相關資訊，請參閱[虛擬機器 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines)</span><span class="sxs-lookup"><span data-stu-id="1d581-134">For more information on Microsoft Azure Service Level Agreement for Virtual Machines [SLA For Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines)</span></span>

### <a name="scalability"></a><span data-ttu-id="1d581-135">延展性</span><span class="sxs-lookup"><span data-stu-id="1d581-135">Scalability</span></span>

<span data-ttu-id="1d581-136">如需設計可調整解決方案的一般指引，請參閱 Azure Architecture Center 中的[延展性檢查清單][scalability]。</span><span class="sxs-lookup"><span data-stu-id="1d581-136">For general guidance on designing scalable solutions, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="1d581-137">安全性</span><span class="sxs-lookup"><span data-stu-id="1d581-137">Security</span></span>

<span data-ttu-id="1d581-138">如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。</span><span class="sxs-lookup"><span data-stu-id="1d581-138">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="1d581-139">災害復原</span><span class="sxs-lookup"><span data-stu-id="1d581-139">Resiliency</span></span>

<span data-ttu-id="1d581-140">如需設計彈性解決方案的一般指引，請參閱[為 Azure 設計有彈性的應用程式][resiliency]。</span><span class="sxs-lookup"><span data-stu-id="1d581-140">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="pricing"></a><span data-ttu-id="1d581-141">價格</span><span class="sxs-lookup"><span data-stu-id="1d581-141">Pricing</span></span>

<span data-ttu-id="1d581-142">探索執行此案例的成本，所有服務會在成本計算機中預先設定。</span><span class="sxs-lookup"><span data-stu-id="1d581-142">Explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span>  <span data-ttu-id="1d581-143">若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的流量。</span><span class="sxs-lookup"><span data-stu-id="1d581-143">To see how the pricing would change for your particular use case change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="1d581-144">我們根據您預期取得的流量，提供了四個範例成本設定檔：</span><span class="sxs-lookup"><span data-stu-id="1d581-144">We have provided four sample cost profiles based on amount of traffic you expect to get:</span></span>

|<span data-ttu-id="1d581-145">大小</span><span class="sxs-lookup"><span data-stu-id="1d581-145">Size</span></span>|<span data-ttu-id="1d581-146">SAP</span><span class="sxs-lookup"><span data-stu-id="1d581-146">SAPs</span></span>|<span data-ttu-id="1d581-147">VM 類型</span><span class="sxs-lookup"><span data-stu-id="1d581-147">VM Type</span></span>|<span data-ttu-id="1d581-148">儲存體</span><span class="sxs-lookup"><span data-stu-id="1d581-148">Storage</span></span>|<span data-ttu-id="1d581-149">Azure 價格計算機</span><span class="sxs-lookup"><span data-stu-id="1d581-149">Azure Pricing Calculator</span></span>|
|----|----|-------|-------|---------------|
|<span data-ttu-id="1d581-150">小型</span><span class="sxs-lookup"><span data-stu-id="1d581-150">Small</span></span>|<span data-ttu-id="1d581-151">8000</span><span class="sxs-lookup"><span data-stu-id="1d581-151">8000</span></span>|<span data-ttu-id="1d581-152">D8s_v3</span><span class="sxs-lookup"><span data-stu-id="1d581-152">D8s_v3</span></span>|<span data-ttu-id="1d581-153">2xP20、1xP10</span><span class="sxs-lookup"><span data-stu-id="1d581-153">2xP20, 1xP10</span></span>|[<span data-ttu-id="1d581-154">小型</span><span class="sxs-lookup"><span data-stu-id="1d581-154">Small</span></span>](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1)|
|<span data-ttu-id="1d581-155">中</span><span class="sxs-lookup"><span data-stu-id="1d581-155">Medium</span></span>|<span data-ttu-id="1d581-156">16000</span><span class="sxs-lookup"><span data-stu-id="1d581-156">16000</span></span>|<span data-ttu-id="1d581-157">D16s_v3</span><span class="sxs-lookup"><span data-stu-id="1d581-157">D16s_v3</span></span>|<span data-ttu-id="1d581-158">3xP20、1xP10</span><span class="sxs-lookup"><span data-stu-id="1d581-158">3xP20, 1xP10</span></span>|[<span data-ttu-id="1d581-159">中型</span><span class="sxs-lookup"><span data-stu-id="1d581-159">Medium</span></span>](https://azure.com/e/465bd07047d148baab032b2f461550cd)|
<span data-ttu-id="1d581-160">大型</span><span class="sxs-lookup"><span data-stu-id="1d581-160">Large</span></span>|<span data-ttu-id="1d581-161">32000</span><span class="sxs-lookup"><span data-stu-id="1d581-161">32000</span></span>|<span data-ttu-id="1d581-162">E32s_v3</span><span class="sxs-lookup"><span data-stu-id="1d581-162">E32s_v3</span></span>|<span data-ttu-id="1d581-163">3xP20、1xP10</span><span class="sxs-lookup"><span data-stu-id="1d581-163">3xP20, 1xP10</span></span>|[<span data-ttu-id="1d581-164">大型</span><span class="sxs-lookup"><span data-stu-id="1d581-164">Large</span></span>](https://azure.com/e/ada2e849d68b41c3839cc976000c6931)|
<span data-ttu-id="1d581-165">超大型</span><span class="sxs-lookup"><span data-stu-id="1d581-165">Extra Large</span></span>|<span data-ttu-id="1d581-166">64000</span><span class="sxs-lookup"><span data-stu-id="1d581-166">64000</span></span>|<span data-ttu-id="1d581-167">M64s</span><span class="sxs-lookup"><span data-stu-id="1d581-167">M64s</span></span>|<span data-ttu-id="1d581-168">4xP20、1xP10</span><span class="sxs-lookup"><span data-stu-id="1d581-168">4xP20, 1xP10</span></span>|[<span data-ttu-id="1d581-169">超大型</span><span class="sxs-lookup"><span data-stu-id="1d581-169">Extra Large</span></span>](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef)|

<span data-ttu-id="1d581-170">請注意：定價是一個指南，只會表示 VM 和儲存體成本 (不包括網路、備份儲存體和資料輸入/輸出費用)。</span><span class="sxs-lookup"><span data-stu-id="1d581-170">Note: pricing is a guide and only indicates the VMs and storage costs (excludes, networking, backup storage, and data ingress/egress charges).</span></span>

* <span data-ttu-id="1d581-171">[小型](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1)：小型系統包含具有 8 個 vCPU 的 VM 類型 D8s_v3，32 GB RAM 和 200 GB 暫存儲存體，另外還有兩個 512 GB 和一個 128 GB 的進階儲存體磁碟。</span><span class="sxs-lookup"><span data-stu-id="1d581-171">[Small](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1): A small system consists of VM type D8s_v3 with 8x vCPUs, 32 GB RAM and 200 GB temp storage, additionally two 512 GB and one 128 GB premium storage disks.</span></span>
* <span data-ttu-id="1d581-172">[中型](https://azure.com/e/465bd07047d148baab032b2f461550cd)：中型系統包含具有 16 個 vCPU 的 VM 類型 D16s_v3，64 GB RAM 和 400 GB 暫存儲存體，另外還有三個 512 GB 和一個 128 GB 的進階儲存體磁碟。</span><span class="sxs-lookup"><span data-stu-id="1d581-172">[Medium](https://azure.com/e/465bd07047d148baab032b2f461550cd): A medium system consists of VM type D16s_v3 with 16x vCPUs, 64 GB RAM and 400 GB temp storage, additionally three 512 GB and one 128 GB premium storage disks.</span></span>
* <span data-ttu-id="1d581-173">[大型](https://azure.com/e/ada2e849d68b41c3839cc976000c6931)：大型系統包含具有 32 個 vCPU 的 VM 類型 E32s_v3，256 GB RAM 和 512 GB 暫存儲存體，另外還有三個 512 GB 和一個 128 GB 的進階儲存體磁碟。</span><span class="sxs-lookup"><span data-stu-id="1d581-173">[Large](https://azure.com/e/ada2e849d68b41c3839cc976000c6931): A large system consists of VM type E32s_v3 with 32x vCPUs, 256 GB RAM and 512 GB temp storage, additionally three 512GB and one 128GB premium storage disks.</span></span>
* <span data-ttu-id="1d581-174">[超大型](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef)：超大型系統包含具有 64 個 vCPU 的 VM 類型 M64s，1024 GB RAM 和 2000 GB 暫存儲存體，另外還有四個 512 GB 和一個 128 GB 的進階儲存體磁碟。</span><span class="sxs-lookup"><span data-stu-id="1d581-174">[Extra Large](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef): An extra large system consists of a VM type M64s with 64x vCPUs, 1024 GB RAM and 2000 GB temp storage, additionally four 512 GB and one 128 GB premium storage disks.</span></span>

## <a name="deployment"></a><span data-ttu-id="1d581-175">部署</span><span class="sxs-lookup"><span data-stu-id="1d581-175">Deployment</span></span>

<span data-ttu-id="1d581-176">若要部署類似於上述案例的基礎結構，請使用 [部署] 按鈕</span><span class="sxs-lookup"><span data-stu-id="1d581-176">To deploy the underlying infrastructure similar to the scenario above, use the deploy button</span></span>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fapps%2Fsap-2tier%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

<span data-ttu-id="1d581-177">\* SAP 不會自動安裝，請在建置基礎結構之後，手動安裝它。</span><span class="sxs-lookup"><span data-stu-id="1d581-177">\* SAP won't be automatically installed, manually install it after the infrastructure has been built.</span></span>

<!-- links -->
[reference architecture]:  /azure/architecture/reference-architectures/sap
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[sap-netweaver]: /azure/architecture/reference-architectures/sap/sap-netweaver
[sap-hana]: /azure/architecture/reference-architectures/sap/sap-s4hana
[sap-large]: /azure/architecture/reference-architectures/sap/hana-large-instances
[hub-spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
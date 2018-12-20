---
title: Azure 上 SAP 工作負載的開發/測試環境
description: 為 SAP 工作負載建置開發/測試環境。
author: AndrewDibbins
ms.date: 7/11/18
ms.custom: fasttrack
ms.openlocfilehash: 84665bfeb6ada568c631e1db72b97269d79f2e60
ms.sourcegitcommit: a0e8d11543751d681953717f6e78173e597ae207
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2018
ms.locfileid: "53004671"
---
# <a name="devtest-environments-for-sap-workloads-on-azure"></a><span data-ttu-id="2b70d-103">Azure 上 SAP 工作負載的開發/測試環境</span><span class="sxs-lookup"><span data-stu-id="2b70d-103">Dev/test environments for SAP workloads on Azure</span></span>

<span data-ttu-id="2b70d-104">此範例示範如何在 Windows 或 Linux 環境的 Azure 上建立 SAP NetWeaver 的開發/測試環境。</span><span class="sxs-lookup"><span data-stu-id="2b70d-104">This example shows how to establish a dev/test environment for SAP NetWeaver in a Windows or Linux environment on Azure.</span></span> <span data-ttu-id="2b70d-105">所使用的資料庫是 AnyDB，這是任何支援 DBMS 的 SAP 字詞 (並非 SAP HANA)。</span><span class="sxs-lookup"><span data-stu-id="2b70d-105">The database used is AnyDB, the SAP term for any supported DBMS (that isn't SAP HANA).</span></span> <span data-ttu-id="2b70d-106">因為此架構是設計用於非生產環境，它僅使用單一虛擬機器 (VM) 部署，且可以變更其大小，以容納貴組織的需求。</span><span class="sxs-lookup"><span data-stu-id="2b70d-106">Because this architecture is designed for non-production environments, it's deployed with just a single virtual machine (VM) and it's size can be changed to accommodate your organization's needs.</span></span>

<span data-ttu-id="2b70d-107">如需生產環境使用案例，請檢閱以下提供的 SAP 參考架構：</span><span class="sxs-lookup"><span data-stu-id="2b70d-107">For production use cases review the SAP reference architectures available below:</span></span>

* <span data-ttu-id="2b70d-108">[適用於 AnyDB 的 SAP NetWeaver][sap-netweaver]</span><span class="sxs-lookup"><span data-stu-id="2b70d-108">[SAP NetWeaver for AnyDB][sap-netweaver]</span></span>
* <span data-ttu-id="2b70d-109">[SAP S/4HANA][sap-hana]</span><span class="sxs-lookup"><span data-stu-id="2b70d-109">[SAP S/4HANA][sap-hana]</span></span>
* <span data-ttu-id="2b70d-110">[Azure 上的 SAP 大型執行個體][sap-large]</span><span class="sxs-lookup"><span data-stu-id="2b70d-110">[SAP on Azure large instances][sap-large]</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="2b70d-111">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="2b70d-111">Relevant use cases</span></span>

<span data-ttu-id="2b70d-112">其他相關的使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="2b70d-112">Other relevant use cases include:</span></span>

* <span data-ttu-id="2b70d-113">非關鍵性的 SAP 非產能工作負載 (沙箱、開發、測試、品質保證)</span><span class="sxs-lookup"><span data-stu-id="2b70d-113">Non-critical SAP non-productive workloads (sandbox, development, test, quality assurance)</span></span>
* <span data-ttu-id="2b70d-114">非關鍵性的 SAP 商務工作負載</span><span class="sxs-lookup"><span data-stu-id="2b70d-114">Non-critical SAP business workloads</span></span>

## <a name="architecture"></a><span data-ttu-id="2b70d-115">架構</span><span class="sxs-lookup"><span data-stu-id="2b70d-115">Architecture</span></span>

![SAP 工作負載的開發/測試環境架構圖](media/architecture-sap-dev-test.png)

<span data-ttu-id="2b70d-117">此案例示範如何在單一虛擬機器上佈建單一 SAP 系統資料庫和 SAP 應用程式伺服器。</span><span class="sxs-lookup"><span data-stu-id="2b70d-117">This scenario demonstrates provisioning a single SAP system database and SAP application server on a single virtual machine.</span></span> <span data-ttu-id="2b70d-118">整個案例的資料流程如下所示：</span><span class="sxs-lookup"><span data-stu-id="2b70d-118">The data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="2b70d-119">客戶可使用 SAP 使用者介面或其他用戶端工具 (Excel、網頁瀏覽器或其他 Web 應用程式) 來存取 Azure 型 SAP 系統。</span><span class="sxs-lookup"><span data-stu-id="2b70d-119">Customers use the SAP user interface or other client tools (Excel, a web browser, or other web application) to access the Azure-based SAP system.</span></span>
2. <span data-ttu-id="2b70d-120">透過使用已建立的 ExpressRoute 來提供連線能力。</span><span class="sxs-lookup"><span data-stu-id="2b70d-120">Connectivity is provided through the use of an established ExpressRoute.</span></span> <span data-ttu-id="2b70d-121">Azure 中的 ExpressRoute 連線會在 ExpressRoute 閘道終止。</span><span class="sxs-lookup"><span data-stu-id="2b70d-121">The ExpressRoute connection is terminated in Azure at the ExpressRoute gateway.</span></span> <span data-ttu-id="2b70d-122">網路流量會透過 ExpressRoute 閘道路由至閘道子網路，以及從閘道子網路路由至應用程式層輪輻子網路 (請參閱[中樞輪輻][hub-spoke]模式)，然後透過網路安全性群組路由至 SAP 應用程式虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="2b70d-122">Network traffic routes through the ExpressRoute gateway to the gateway subnet, and from the gateway subnet to the application-tier spoke subnet (see the [hub-spoke][hub-spoke] pattern) and via a Network Security Gateway to the SAP application virtual machine.</span></span>
3. <span data-ttu-id="2b70d-123">身分識別管理伺服器會提供驗證服務。</span><span class="sxs-lookup"><span data-stu-id="2b70d-123">The identity management servers provide authentication services.</span></span>
4. <span data-ttu-id="2b70d-124">「跳躍箱」會提供本機管理功能。</span><span class="sxs-lookup"><span data-stu-id="2b70d-124">The jump box provides local management capabilities.</span></span>

### <a name="components"></a><span data-ttu-id="2b70d-125">元件</span><span class="sxs-lookup"><span data-stu-id="2b70d-125">Components</span></span>

* <span data-ttu-id="2b70d-126">[虛擬網路](/azure/virtual-network/virtual-networks-overview)是 Azure 內的網路通訊基礎。</span><span class="sxs-lookup"><span data-stu-id="2b70d-126">[Virtual Networks](/azure/virtual-network/virtual-networks-overview) are the basis of network communication within Azure.</span></span>
* <span data-ttu-id="2b70d-127">[虛擬機器](/azure/virtual-machines/windows/overview) Azure 虛擬機器使用 Windows 或 Linux Server 提供隨選、高度可調整、安全且虛擬化的基礎結構。</span><span class="sxs-lookup"><span data-stu-id="2b70d-127">[Virtual Machine](/azure/virtual-machines/windows/overview) Azure Virtual Machines provides on-demand, high-scale, secure, virtualized infrastructure using Windows or Linux Server.</span></span>
* <span data-ttu-id="2b70d-128">[ExpressRoute](/azure/expressroute/expressroute-introduction) 可讓您透過連線提供者所提供的私人連線，將內部部署網路延伸至 Microsoft 雲端。</span><span class="sxs-lookup"><span data-stu-id="2b70d-128">[ExpressRoute](/azure/expressroute/expressroute-introduction) lets you extend your on-premises networks into the Microsoft cloud over a private connection facilitated by a connectivity provider.</span></span>
* <span data-ttu-id="2b70d-129">[網路安全性群組](/azure/virtual-network/security-overview)可讓您將網路流量限制為虛擬網路中的資源。</span><span class="sxs-lookup"><span data-stu-id="2b70d-129">[Network Security Group](/azure/virtual-network/security-overview) lets you limit network traffic to resources in a virtual network.</span></span> <span data-ttu-id="2b70d-130">網路安全性群組包含一些安全性規則，可根據來源或目的地 IP 位址、連接埠和通訊協定允許或拒絕輸入或輸出網路流量。</span><span class="sxs-lookup"><span data-stu-id="2b70d-130">A network security group contains a list of security rules that allow or deny inbound or outbound network traffic based on source or destination IP address, port, and protocol.</span></span> 
* <span data-ttu-id="2b70d-131">[資源群組](/azure/azure-resource-manager/resource-group-overview#resource-groups)可作為 Azure 資源的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="2b70d-131">[Resource Groups](/azure/azure-resource-manager/resource-group-overview#resource-groups) act as logical containers for Azure resources.</span></span>

## <a name="considerations"></a><span data-ttu-id="2b70d-132">考量</span><span class="sxs-lookup"><span data-stu-id="2b70d-132">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="2b70d-133">可用性</span><span class="sxs-lookup"><span data-stu-id="2b70d-133">Availability</span></span>

 <span data-ttu-id="2b70d-134">Microsoft 會提供單一 VM 執行個體的服務等級協定 (SLA)。</span><span class="sxs-lookup"><span data-stu-id="2b70d-134">Microsoft offers a service level agreement (SLA) for single VM instances.</span></span> <span data-ttu-id="2b70d-135">如需關於適用於虛擬機器的 Microsoft Azure 服務等級協定相關資訊，請參閱[虛擬機器 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines)</span><span class="sxs-lookup"><span data-stu-id="2b70d-135">For more information on Microsoft Azure Service Level Agreement for Virtual Machines [SLA For Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines)</span></span>

### <a name="scalability"></a><span data-ttu-id="2b70d-136">延展性</span><span class="sxs-lookup"><span data-stu-id="2b70d-136">Scalability</span></span>

<span data-ttu-id="2b70d-137">如需設計可調整解決方案的一般指引，請參閱 Azure Architecture Center 中的[延展性檢查清單][scalability]。</span><span class="sxs-lookup"><span data-stu-id="2b70d-137">For general guidance on designing scalable solutions, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="2b70d-138">安全性</span><span class="sxs-lookup"><span data-stu-id="2b70d-138">Security</span></span>

<span data-ttu-id="2b70d-139">如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。</span><span class="sxs-lookup"><span data-stu-id="2b70d-139">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="2b70d-140">災害復原</span><span class="sxs-lookup"><span data-stu-id="2b70d-140">Resiliency</span></span>

<span data-ttu-id="2b70d-141">如需設計彈性解決方案的一般指引，請參閱[為 Azure 設計有彈性的應用程式][resiliency]。</span><span class="sxs-lookup"><span data-stu-id="2b70d-141">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="pricing"></a><span data-ttu-id="2b70d-142">價格</span><span class="sxs-lookup"><span data-stu-id="2b70d-142">Pricing</span></span>

<span data-ttu-id="2b70d-143">為了協助您探索執行此案例的成本，所有服務都會在以下成本計算機範例中預先設定。</span><span class="sxs-lookup"><span data-stu-id="2b70d-143">To help you explore the cost of running this scenario, all of the services are pre-configured in the cost calculator examples below.</span></span> <span data-ttu-id="2b70d-144">若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的流量。</span><span class="sxs-lookup"><span data-stu-id="2b70d-144">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="2b70d-145">我們根據您預期接收的流量，提供了四個範例成本設定檔：</span><span class="sxs-lookup"><span data-stu-id="2b70d-145">We have provided four sample cost profiles based on amount of traffic you expect to receive:</span></span>

|<span data-ttu-id="2b70d-146">大小</span><span class="sxs-lookup"><span data-stu-id="2b70d-146">Size</span></span>|<span data-ttu-id="2b70d-147">SAP</span><span class="sxs-lookup"><span data-stu-id="2b70d-147">SAPs</span></span>|<span data-ttu-id="2b70d-148">VM 類型</span><span class="sxs-lookup"><span data-stu-id="2b70d-148">VM Type</span></span>|<span data-ttu-id="2b70d-149">儲存體</span><span class="sxs-lookup"><span data-stu-id="2b70d-149">Storage</span></span>|<span data-ttu-id="2b70d-150">Azure 價格計算機</span><span class="sxs-lookup"><span data-stu-id="2b70d-150">Azure Pricing Calculator</span></span>|
|----|----|-------|-------|---------------|
|<span data-ttu-id="2b70d-151">小型</span><span class="sxs-lookup"><span data-stu-id="2b70d-151">Small</span></span>|<span data-ttu-id="2b70d-152">8000</span><span class="sxs-lookup"><span data-stu-id="2b70d-152">8000</span></span>|<span data-ttu-id="2b70d-153">D8s_v3</span><span class="sxs-lookup"><span data-stu-id="2b70d-153">D8s_v3</span></span>|<span data-ttu-id="2b70d-154">2xP20、1xP10</span><span class="sxs-lookup"><span data-stu-id="2b70d-154">2xP20, 1xP10</span></span>|[<span data-ttu-id="2b70d-155">小型</span><span class="sxs-lookup"><span data-stu-id="2b70d-155">Small</span></span>](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1)|
|<span data-ttu-id="2b70d-156">中</span><span class="sxs-lookup"><span data-stu-id="2b70d-156">Medium</span></span>|<span data-ttu-id="2b70d-157">16000</span><span class="sxs-lookup"><span data-stu-id="2b70d-157">16000</span></span>|<span data-ttu-id="2b70d-158">D16s_v3</span><span class="sxs-lookup"><span data-stu-id="2b70d-158">D16s_v3</span></span>|<span data-ttu-id="2b70d-159">3xP20、1xP10</span><span class="sxs-lookup"><span data-stu-id="2b70d-159">3xP20, 1xP10</span></span>|[<span data-ttu-id="2b70d-160">中型</span><span class="sxs-lookup"><span data-stu-id="2b70d-160">Medium</span></span>](https://azure.com/e/465bd07047d148baab032b2f461550cd)|
<span data-ttu-id="2b70d-161">大型</span><span class="sxs-lookup"><span data-stu-id="2b70d-161">Large</span></span>|<span data-ttu-id="2b70d-162">32000</span><span class="sxs-lookup"><span data-stu-id="2b70d-162">32000</span></span>|<span data-ttu-id="2b70d-163">E32s_v3</span><span class="sxs-lookup"><span data-stu-id="2b70d-163">E32s_v3</span></span>|<span data-ttu-id="2b70d-164">3xP20、1xP10</span><span class="sxs-lookup"><span data-stu-id="2b70d-164">3xP20, 1xP10</span></span>|[<span data-ttu-id="2b70d-165">大型</span><span class="sxs-lookup"><span data-stu-id="2b70d-165">Large</span></span>](https://azure.com/e/ada2e849d68b41c3839cc976000c6931)|
<span data-ttu-id="2b70d-166">超大型</span><span class="sxs-lookup"><span data-stu-id="2b70d-166">Extra Large</span></span>|<span data-ttu-id="2b70d-167">64000</span><span class="sxs-lookup"><span data-stu-id="2b70d-167">64000</span></span>|<span data-ttu-id="2b70d-168">M64s</span><span class="sxs-lookup"><span data-stu-id="2b70d-168">M64s</span></span>|<span data-ttu-id="2b70d-169">4xP20、1xP10</span><span class="sxs-lookup"><span data-stu-id="2b70d-169">4xP20, 1xP10</span></span>|[<span data-ttu-id="2b70d-170">超大型</span><span class="sxs-lookup"><span data-stu-id="2b70d-170">Extra Large</span></span>](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef)|

> [!NOTE]
> <span data-ttu-id="2b70d-171">此定價是一份指南，只會指出 VM 和儲存體成本。</span><span class="sxs-lookup"><span data-stu-id="2b70d-171">This pricing is a guide that only indicates the VMs and storage costs.</span></span> <span data-ttu-id="2b70d-172">它會排除網路、備份儲存體和資料輸入/輸出費用。</span><span class="sxs-lookup"><span data-stu-id="2b70d-172">It excludes networking, backup storage, and data ingress/egress charges.</span></span>

* <span data-ttu-id="2b70d-173">[小型](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1)：小型系統包含具有 8 個 vCPU 的 VM 類型 D8s_v3，32 GB RAM 和 200 GB 暫存儲存體，另外還有兩個 512 GB 和一個 128 GB 的進階儲存體磁碟。</span><span class="sxs-lookup"><span data-stu-id="2b70d-173">[Small](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1): A small system consists of VM type D8s_v3 with 8x vCPUs, 32 GB RAM and 200 GB temp storage, additionally two 512 GB and one 128 GB premium storage disks.</span></span>
* <span data-ttu-id="2b70d-174">[中型](https://azure.com/e/465bd07047d148baab032b2f461550cd)：中型系統包含具有 16 個 vCPU 的 VM 類型 D16s_v3，64 GB RAM 和 400 GB 暫存儲存體，另外還有三個 512 GB 和一個 128 GB 的進階儲存體磁碟。</span><span class="sxs-lookup"><span data-stu-id="2b70d-174">[Medium](https://azure.com/e/465bd07047d148baab032b2f461550cd): A medium system consists of VM type D16s_v3 with 16x vCPUs, 64 GB RAM and 400 GB temp storage, additionally three 512 GB and one 128 GB premium storage disks.</span></span>
* <span data-ttu-id="2b70d-175">[大型](https://azure.com/e/ada2e849d68b41c3839cc976000c6931)：大型系統包含具有 32 個 vCPU 的 VM 類型 E32s_v3，256 GB RAM 和 512 GB 暫存儲存體，另外還有三個 512 GB 和一個 128 GB 的進階儲存體磁碟。</span><span class="sxs-lookup"><span data-stu-id="2b70d-175">[Large](https://azure.com/e/ada2e849d68b41c3839cc976000c6931): A large system consists of VM type E32s_v3 with 32x vCPUs, 256 GB RAM and 512 GB temp storage, additionally three 512GB and one 128GB premium storage disks.</span></span>
* <span data-ttu-id="2b70d-176">[超大型](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef)：超大型系統包含具有 64 個 vCPU 的 VM 類型 M64s，1024 GB RAM 和 2000 GB 暫存儲存體，另外還有四個 512 GB 和一個 128 GB 的進階儲存體磁碟。</span><span class="sxs-lookup"><span data-stu-id="2b70d-176">[Extra Large](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef): An extra large system consists of a VM type M64s with 64x vCPUs, 1024 GB RAM and 2000 GB temp storage, additionally four 512 GB and one 128 GB premium storage disks.</span></span>

## <a name="deployment"></a><span data-ttu-id="2b70d-177">部署</span><span class="sxs-lookup"><span data-stu-id="2b70d-177">Deployment</span></span>

<span data-ttu-id="2b70d-178">按一下這裡可部署此案例的基礎結構。</span><span class="sxs-lookup"><span data-stu-id="2b70d-178">Click here to deploy the underlying infrastructure for this scenario.</span></span>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fapps%2Fsap-2tier%2Fazuredeploy.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

> [!NOTE]
> <span data-ttu-id="2b70d-179">在此部署期間不會安裝 SAP 和 Oracle。</span><span class="sxs-lookup"><span data-stu-id="2b70d-179">SAP and Oracle are not installed during this deployment.</span></span> <span data-ttu-id="2b70d-180">您必須分別部署這些元件。</span><span class="sxs-lookup"><span data-stu-id="2b70d-180">You will need to deploy these components separately.</span></span>

<!-- links -->
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[sap-netweaver]: /azure/architecture/reference-architectures/sap/sap-netweaver
[sap-hana]: /azure/architecture/reference-architectures/sap/sap-s4hana
[sap-large]: /azure/architecture/reference-architectures/sap/hana-large-instances
[hub-spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

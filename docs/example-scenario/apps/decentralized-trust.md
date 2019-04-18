---
title: 銀行間的分散式信任
titleSuffix: Azure Example Scenarios
description: 建立適用於通訊和資訊共用的受信任環境，不需要重新排序至集中式資料庫。
author: vitoc
ms.date: 09/09/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: csa-team
social_image_url: /azure/architecture/example-scenario/apps/media/architecture-decentralized-trust.png
ms.openlocfilehash: 3bc75e59a4d391c74a0e606f9670c88509a3375b
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640441"
---
# <a name="decentralized-trust-between-banks-on-azure"></a><span data-ttu-id="a997d-103">Azure 上銀行間的分散式信任</span><span class="sxs-lookup"><span data-stu-id="a997d-103">Decentralized trust between banks on Azure</span></span>

<span data-ttu-id="a997d-104">如果銀行或任何其他機構想要建立適用於資訊共用的受信任環境，而不需藉助於集中式資料庫，很適合使用此範例案例。</span><span class="sxs-lookup"><span data-stu-id="a997d-104">This example scenario is useful for banks or any other institutions that want to establish a trusted environment for information sharing without resorting to a centralized database.</span></span> <span data-ttu-id="a997d-105">基於此範例的目的，我們會在維護銀行間信用分數資訊的內容中描述此範例，但是架構可套用至以下案例：組織聯盟想要與彼此共用經過驗證的資訊，而不必藉助於使用單一方所執行的中央系統。</span><span class="sxs-lookup"><span data-stu-id="a997d-105">For the purpose of this example, we will describe the scenario in the context of maintaining credit score information between banks, but the architecture can be applied to any scenario where a consortium of organizations want to share validated information with one another without resorting to the use of a central system ran by one single party.</span></span>

<span data-ttu-id="a997d-106">傳統上，金融體系內的銀行會依賴集中式來源 (例如徵信機構) 提供有關個人的信用分數和歷程資訊。</span><span class="sxs-lookup"><span data-stu-id="a997d-106">Traditionally, banks within a financial system rely on centralized sources such as credit bureaus for information on an individual's credit score and history.</span></span> <span data-ttu-id="a997d-107">集中式方法會導致營運風險集中，有時還會出現不必要的第三方。</span><span class="sxs-lookup"><span data-stu-id="a997d-107">A centralized approach presents a concentration of operational risk and sometimes an unnecessary third party.</span></span>

<span data-ttu-id="a997d-108">銀行聯盟可以利用 DLT (分散式帳本技術) 建立非集中式系統，該系統更有效率、較不容易受到攻擊，並作為可實作創新結構的新平台，以解決傳統的隱私權、速度和成本挑戰。</span><span class="sxs-lookup"><span data-stu-id="a997d-108">With DLTs (distributed ledger technology), a consortium of banks can establish a decentralized system that can be more efficient, less susceptible to attack, and serve as a new platform where innovative structures can be implemented to solve traditional challenges with privacy, speed, and cost.</span></span>

<span data-ttu-id="a997d-109">此範例顯示如何快速佈建 Azure 服務 (例如虛擬機器擴展集、虛擬網路、Key Vault、儲存體、Load Balancer 和監視器)，以供部署有效率的私用以太坊 PoA 區塊鏈，讓成員銀行在其中建立自己的節點。</span><span class="sxs-lookup"><span data-stu-id="a997d-109">This example will show you how Azure services such as virtual machine scale sets, Virtual Network, Key Vault, Storage, Load Balancer, and Monitor can be quickly provisioned for the deployment of an efficient private Ethereum PoA blockchain where member banks can establish their own nodes.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="a997d-110">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="a997d-110">Relevant use cases</span></span>

<span data-ttu-id="a997d-111">其他相關的使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="a997d-111">Other relevant use cases include:</span></span>

- <span data-ttu-id="a997d-112">在多語系公司的不同業務單位之間移動已配置的預算</span><span class="sxs-lookup"><span data-stu-id="a997d-112">Movement of allocated budgets between different business units of a multinational corporation</span></span>
- <span data-ttu-id="a997d-113">跨境支付</span><span class="sxs-lookup"><span data-stu-id="a997d-113">Cross-border payments</span></span>
- <span data-ttu-id="a997d-114">貿易融資案例</span><span class="sxs-lookup"><span data-stu-id="a997d-114">Trade finance scenarios</span></span>
- <span data-ttu-id="a997d-115">涉及不同公司的忠誠度系統</span><span class="sxs-lookup"><span data-stu-id="a997d-115">Loyalty systems involving different companies</span></span>
- <span data-ttu-id="a997d-116">供應鏈生態系統</span><span class="sxs-lookup"><span data-stu-id="a997d-116">Supply chain ecosystems</span></span>

## <a name="architecture"></a><span data-ttu-id="a997d-117">架構</span><span class="sxs-lookup"><span data-stu-id="a997d-117">Architecture</span></span>

![非集中式銀行信任架構圖](./media/architecture-decentralized-trust.png)

<span data-ttu-id="a997d-119">此案例涵蓋在包含兩個或更多成員的聯盟中，建立可擴充、安全且受監視的私人、企業區塊鏈網路所需的後端元件。</span><span class="sxs-lookup"><span data-stu-id="a997d-119">This scenario covers the back-end components that are necessary to create a scalable, secure, and monitored private, enterprise blockchain network within a consortium of two or more members.</span></span> <span data-ttu-id="a997d-120">其中保留了如何佈建這些元件的詳細資訊 (亦即，在不同訂用帳戶和資源群組內)，以及連線需求 (亦即 VPN 或 ExpressRoute)，以便您根據貴組織的原則需求仔細考慮。</span><span class="sxs-lookup"><span data-stu-id="a997d-120">Details of how these components are provisioned (that is, within different subscriptions and resource groups) as well as the connectivity requirements (that is, VPN or ExpressRoute) are left for your consideration based on your organization's policy requirements.</span></span> <span data-ttu-id="a997d-121">以下是資料的流動方式：</span><span class="sxs-lookup"><span data-stu-id="a997d-121">Here's how data flows:</span></span>

1. <span data-ttu-id="a997d-122">銀行 A 透過 JSON-RPC 將交易傳送到區塊鏈網路，以建立/更新個人的信用記錄。</span><span class="sxs-lookup"><span data-stu-id="a997d-122">Bank A creates/updates an individual's credit record by sending a transaction to the blockchain network via JSON-RPC.</span></span>
2. <span data-ttu-id="a997d-123">資料會從銀行 A 的私用應用程式伺服器流動至 Azure 負載平衡器，接著流動至虛擬機器擴展集上的驗證節點 VM。</span><span class="sxs-lookup"><span data-stu-id="a997d-123">Data flows from Bank A's private application server to the Azure load balancer and subsequently to a validating node VM on the virtual machine scale set.</span></span>
3. <span data-ttu-id="a997d-124">以太坊 PoA 網路會在預先設定的時間建立一個區塊 (在此案例中為 2 秒)。</span><span class="sxs-lookup"><span data-stu-id="a997d-124">The Ethereum PoA network creates a block at a preset time (2 seconds for this scenario).</span></span>
4. <span data-ttu-id="a997d-125">此交易會放入所建立的區塊中，並且在區塊鏈網路中驗證。</span><span class="sxs-lookup"><span data-stu-id="a997d-125">The transaction is bundled into the created block and validated across the blockchain network.</span></span>
5. <span data-ttu-id="a997d-126">銀行 B 同樣可透過 JSON-RPC 與自己的節點通訊，以讀取銀行 A 所建立的信用記錄。</span><span class="sxs-lookup"><span data-stu-id="a997d-126">Bank B can read the credit record created by bank A by communicating with its own node similarly via JSON-RPC.</span></span>

### <a name="components"></a><span data-ttu-id="a997d-127">元件</span><span class="sxs-lookup"><span data-stu-id="a997d-127">Components</span></span>

- <span data-ttu-id="a997d-128">虛擬機器擴展集內的虛擬機器會提供隨選計算設施，來裝載區塊鏈的驗證程式程序</span><span class="sxs-lookup"><span data-stu-id="a997d-128">Virtual machines within virtual machine scale sets provides the on-demand compute facility to host the validator processes for the blockchain</span></span>
- <span data-ttu-id="a997d-129">Key Vault 可作為每個驗證程式私密金鑰的安全儲存設備</span><span class="sxs-lookup"><span data-stu-id="a997d-129">Key Vault is used as the secure storage facility for the private keys of each validator</span></span>
- <span data-ttu-id="a997d-130">Load Balancer 會分散 RPC、對等互連和 Governance DApp 要求</span><span class="sxs-lookup"><span data-stu-id="a997d-130">Load Balancer spreads the RPC, peering, and Governance DApp requests</span></span>
- <span data-ttu-id="a997d-131">裝載持續性網路資訊和協調租用的儲存體</span><span class="sxs-lookup"><span data-stu-id="a997d-131">Storage hosting persistent network information and coordinating leasing</span></span>
- <span data-ttu-id="a997d-132">Operations Management Suite (一組 Azure 服務) 提供可用節點、每分鐘交易數和聯盟成員的見解</span><span class="sxs-lookup"><span data-stu-id="a997d-132">Operations Management Suite (a bundling of a few Azure services) provides insight into available nodes, transactions per minute and consortium members</span></span>

### <a name="alternatives"></a><span data-ttu-id="a997d-133">替代項目</span><span class="sxs-lookup"><span data-stu-id="a997d-133">Alternatives</span></span>

<span data-ttu-id="a997d-134">此範例選擇使用以太坊 PoA 方法，因為如果組織聯盟想要建立可以信任、非集中式且易於理解的方式輕鬆互相交換及分享資訊的環境，這是很好的進入點。</span><span class="sxs-lookup"><span data-stu-id="a997d-134">The Ethereum PoA approach is chosen for this example because it is a good entry point for a consortium of organizations that want to create an environment where information can be exchanged and shared with one another easily in a trusted, decentralized, and easy to understand way.</span></span> <span data-ttu-id="a997d-135">此外，可用的 Azure 解決方案範本會提供快速且方便的方式，不只可供聯盟領導者開始以太坊 PoA 區塊鏈，也可供聯盟中的成員組織在自己的資源群組和訂用帳戶內啟動自己的 Azure 資源，以加入現有的網路。</span><span class="sxs-lookup"><span data-stu-id="a997d-135">The available Azure solution templates also provide a fast and convenient way not just for a consortium leader to start an Ethereum PoA blockchain, but also for member organizations in the consortium to spin up their own Azure resources within their own resource group and subscription to join an existing network.</span></span>

<span data-ttu-id="a997d-136">在其他擴充或不同的情況下，可能會引發交易隱私權等疑慮。</span><span class="sxs-lookup"><span data-stu-id="a997d-136">For other extended or different scenarios, concerns such as transaction privacy may arise.</span></span> <span data-ttu-id="a997d-137">比方說，在證券轉讓案例中，聯盟中的成員可能不希望其他成員看到其交易。</span><span class="sxs-lookup"><span data-stu-id="a997d-137">For example, in a securities transfer scenario, members in a consortium may not want their transactions to be visible even to other members.</span></span> <span data-ttu-id="a997d-138">以太坊 PoA 有其他替代項目會以其各自的方式處理這些疑慮：</span><span class="sxs-lookup"><span data-stu-id="a997d-138">Other alternatives to Ethereum PoA exist that addresses these concerns in their own way:</span></span>

- <span data-ttu-id="a997d-139">Corda</span><span class="sxs-lookup"><span data-stu-id="a997d-139">Corda</span></span>
- <span data-ttu-id="a997d-140">Quorum</span><span class="sxs-lookup"><span data-stu-id="a997d-140">Quorum</span></span>
- <span data-ttu-id="a997d-141">Hyperledger</span><span class="sxs-lookup"><span data-stu-id="a997d-141">Hyperledger</span></span>

## <a name="considerations"></a><span data-ttu-id="a997d-142">考量</span><span class="sxs-lookup"><span data-stu-id="a997d-142">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="a997d-143">可用性</span><span class="sxs-lookup"><span data-stu-id="a997d-143">Availability</span></span>

<span data-ttu-id="a997d-144">[Azure 監視器][ monitor]用來持續監視區塊鏈網路的問題，以確保可用性。</span><span class="sxs-lookup"><span data-stu-id="a997d-144">[Azure Monitor][monitor] is used to continuously monitor the blockchain network for issues to ensure availability.</span></span> <span data-ttu-id="a997d-145">此案例中使用的區塊鏈解決方案範本部署成功時，系統就會傳送以 Azure 監視器為基礎的自訂監視儀表板連結給您。</span><span class="sxs-lookup"><span data-stu-id="a997d-145">A link to a custom monitoring dashboard based on Azure Monitor will be sent to you upon successful deployment of the blockchain solution template used in this scenario.</span></span> <span data-ttu-id="a997d-146">此儀表板顯示的節點會報告過去 30 分鐘內的活動訊號，以及其他有用的統計資料。</span><span class="sxs-lookup"><span data-stu-id="a997d-146">The dashboard shows nodes that are reporting heartbeats in the past 30 minutes as well as other useful statistics.</span></span>

### <a name="scalability"></a><span data-ttu-id="a997d-147">延展性</span><span class="sxs-lookup"><span data-stu-id="a997d-147">Scalability</span></span>

<span data-ttu-id="a997d-148">區塊鏈的常見疑慮是區塊鏈在預先設定的時間量內可以包含的交易數目。</span><span class="sxs-lookup"><span data-stu-id="a997d-148">A popular concern for blockchain is the number of transactions that a blockchain can include within a preset amount of time.</span></span> <span data-ttu-id="a997d-149">此案例使用權威證明，其可比工作量證明更妥善地管理這類延展性。</span><span class="sxs-lookup"><span data-stu-id="a997d-149">This scenario uses Proof-of-Authority where such scalability can be better managed than Proof-of-Work.</span></span> <span data-ttu-id="a997d-150">在權威證明&ndash;型網路中，共識參與者為已知且受到管理，使其更適合於彼此相識之組織聯盟的私密區塊鏈。</span><span class="sxs-lookup"><span data-stu-id="a997d-150">In Proof-of-Authority&ndash;based networks, consensus participants are known and managed, making it more suitable for private blockchain for a consortium of organization that knows one another.</span></span> <span data-ttu-id="a997d-151">透過自訂儀表板，可以輕鬆地監視平均封鎖時間、每分鐘交易數和計算資源取用量等參數。</span><span class="sxs-lookup"><span data-stu-id="a997d-151">Parameters such as average block time, transactions per minute and compute resource consumption can be easily monitored via the custom dashboard.</span></span> <span data-ttu-id="a997d-152">然後可根據規模需求來調整資源。</span><span class="sxs-lookup"><span data-stu-id="a997d-152">Resources can then be adjusted accordingly based on scale requirements.</span></span>

<span data-ttu-id="a997d-153">如需設計可調整解決方案的一般指引，請參閱 Azure Architecture Center 中的[延展性檢查清單][scalability]。</span><span class="sxs-lookup"><span data-stu-id="a997d-153">For general guidance on designing scalable solutions, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="a997d-154">安全性</span><span class="sxs-lookup"><span data-stu-id="a997d-154">Security</span></span>

<span data-ttu-id="a997d-155">[Azure Key vault][vault] 用於輕鬆儲存及管理驗證程式的私密金鑰。</span><span class="sxs-lookup"><span data-stu-id="a997d-155">[Azure Key Vault][vault] is used to easily store and manage the private keys of validators.</span></span> <span data-ttu-id="a997d-156">此範例中的預設部署會建立可透過網際網路存取的區塊鏈網路。</span><span class="sxs-lookup"><span data-stu-id="a997d-156">The default deployment in this example creates a blockchain network that is accessible via the internet.</span></span> <span data-ttu-id="a997d-157">在想擁有私人網路的生產案例中，成員可以透過 VNet 對 VNet VPN 閘道連線彼此連線。</span><span class="sxs-lookup"><span data-stu-id="a997d-157">For production scenario where a private network is desired, members can be connected to each other via VNet-to-VNet VPN gateway connections.</span></span> <span data-ttu-id="a997d-158">設定 VPN 的步驟包含底下相關資源一節中。</span><span class="sxs-lookup"><span data-stu-id="a997d-158">The steps for configuring a VPN are included in the related resources section below.</span></span>

<span data-ttu-id="a997d-159">如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。</span><span class="sxs-lookup"><span data-stu-id="a997d-159">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="a997d-160">災害復原</span><span class="sxs-lookup"><span data-stu-id="a997d-160">Resiliency</span></span>

<span data-ttu-id="a997d-161">以太坊 PoA 區塊鏈本身可提供某種程度的復原能力，因為驗證程式節點可以部署在不同的區域中。</span><span class="sxs-lookup"><span data-stu-id="a997d-161">The Ethereum PoA blockchain can itself provide some degree of resilience as the validator nodes can be deployed in different regions.</span></span> <span data-ttu-id="a997d-162">Azure 具有全球超過 54 個區域的部署選項。</span><span class="sxs-lookup"><span data-stu-id="a997d-162">Azure has options for deployments in over 54 regions worldwide.</span></span> <span data-ttu-id="a997d-163">例如此案例中的區塊鏈可提供唯一和全新的合作可能性，以增加復原能力。</span><span class="sxs-lookup"><span data-stu-id="a997d-163">A blockchain such as the one in this scenario provides unique and refreshing possibilities of cooperation to increase resilience.</span></span> <span data-ttu-id="a997d-164">網路的復原能力不只是針對單一集中式對象，而是針對聯盟的所有成員提供。</span><span class="sxs-lookup"><span data-stu-id="a997d-164">The resilience of the network is not just provided for by a single centralized party but all members of the consortium.</span></span> <span data-ttu-id="a997d-165">以權威證明&ndash;為基礎的區塊鏈可讓網路復原變得更有規劃和審慎。</span><span class="sxs-lookup"><span data-stu-id="a997d-165">A proof-of-authority&ndash;based blockchain allows network resilience to be even more planned and deliberate.</span></span>

<span data-ttu-id="a997d-166">如需設計彈性的解決方案的一般指引，請參閱[設計可靠的 Azure 應用程式](../../reliability/index.md)。</span><span class="sxs-lookup"><span data-stu-id="a997d-166">For general guidance on designing resilient solutions, see [Designing reliable Azure applications](../../reliability/index.md).</span></span>

## <a name="pricing"></a><span data-ttu-id="a997d-167">價格</span><span class="sxs-lookup"><span data-stu-id="a997d-167">Pricing</span></span>

<span data-ttu-id="a997d-168">為了探索執行此案例的成本，所有服務會在成本計算機中預先設定。</span><span class="sxs-lookup"><span data-stu-id="a997d-168">To explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="a997d-169">若要查看定價如何針對特定使用案例而變更，請變更適當的變數，以符合您預期的效能和可用性需求。</span><span class="sxs-lookup"><span data-stu-id="a997d-169">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected performance and availability requirements.</span></span>

<span data-ttu-id="a997d-170">我們根據執行應用程式的擴展集 VM 執行個體數目 (這些執行個體可位於不同的區域)，提供了 3 個範例成本設定檔。</span><span class="sxs-lookup"><span data-stu-id="a997d-170">We have provided three sample cost profiles based on the number of scale set VM instances that run your applications (the instances can reside in different regions).</span></span>

- <span data-ttu-id="a997d-171">[小型][small-pricing]：此定價範例與已關閉監視功能的每個月 2 部 VM 相互關聯</span><span class="sxs-lookup"><span data-stu-id="a997d-171">[Small][small-pricing]: this pricing example correlates to 2 VMs per month with monitoring turned off</span></span>
- <span data-ttu-id="a997d-172">[中型][medium-pricing]：此定價範例與已開啟監視功能的每個月 7 部 VM 相互關聯</span><span class="sxs-lookup"><span data-stu-id="a997d-172">[Medium][medium-pricing]: this pricing example correlates to 7 VMs per month with monitoring turned on</span></span>
- <span data-ttu-id="a997d-173">[大型][large-pricing]：此定價範例與已開啟監視功能的每個月 15 部 VM 相互關聯</span><span class="sxs-lookup"><span data-stu-id="a997d-173">[Large][large-pricing]: this pricing example correlates to 15 VMs per month with monitoring turned on</span></span>

<span data-ttu-id="a997d-174">上述定價適用於要開始或加入區塊鏈網路的一個聯盟成員。</span><span class="sxs-lookup"><span data-stu-id="a997d-174">The above pricing is for one consortium member to start or join a blockchain network.</span></span> <span data-ttu-id="a997d-175">通常在涉及多家公司或組織的聯盟中，每個成員都會取得自己的 Azure 訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="a997d-175">Typically in a consortium where there are multiple companies or organizations involved, each member will get their own Azure subscription.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a997d-176">後續步驟</span><span class="sxs-lookup"><span data-stu-id="a997d-176">Next Steps</span></span>

<span data-ttu-id="a997d-177">若要查看此案例的範例，請在 Azure 上部署[以太坊 PoA 區塊鏈示範應用程式][deploy]。</span><span class="sxs-lookup"><span data-stu-id="a997d-177">To see an example of this scenario, deploy the [Ethereum PoA blockchain demo application][deploy] on Azure.</span></span> <span data-ttu-id="a997d-178">然後檢閱[案例原始程式碼的讀我檔案][source]。</span><span class="sxs-lookup"><span data-stu-id="a997d-178">Then review the [README of the scenario source code][source].</span></span>

## <a name="related-resources"></a><span data-ttu-id="a997d-179">相關資源</span><span class="sxs-lookup"><span data-stu-id="a997d-179">Related resources</span></span>

<span data-ttu-id="a997d-180">如需有關如何使用適用於 Azure 的以太坊權威證明解決方案範本的詳細資訊，請檢閱這份[使用量指南][guide]。</span><span class="sxs-lookup"><span data-stu-id="a997d-180">For more information on using the Ethereum Proof-of-Authority solution template for Azure, review this [usage guide][guide].</span></span>

<!-- links -->
[small-pricing]: https://azure.com/e/4e429d721eb54adc9a1558fae3e67990
[medium-pricing]: https://azure.com/e/bb42cd77437744be8ed7064403bfe2ef
[large-pricing]: https://azure.com/e/e205b443de3e4adfadf4e09ffee30c56
[guide]: /azure/blockchain-workbench/ethereum-poa-deployment
[deploy]: https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium
[source]: https://github.com/vitoc/creditscoreblockchain
[monitor]: /azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor
[scalability]: /azure/architecture/checklist/scalability
[security]: /azure/security/
[vault]: https://azure.microsoft.com/services/key-vault/

---
title: "在 Azure 中建立 AD DS 資源樹系"
description: "如何在 Azure 中建立受信任的 Active Directory 網域。\n指引,vpn 閘道,expressroute,負載平衡器,虛擬網路,active directory"
author: telmosampaio
ms.date: 11/28/2016
pnp.series.title: Identity management
pnp.series.prev: adds-extend-domain
pnp.series.next: adfs
cardTitle: Create an AD DS forest in Azure
ms.openlocfilehash: b946afa91e8bd303c51f97e18be170c4105cc8c5
ms.sourcegitcommit: 8ab30776e0c4cdc16ca0dcc881960e3108ad3e94
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2017
---
# <a name="create-an-active-directory-domain-services-ad-ds-resource-forest-in-azure"></a><span data-ttu-id="3119e-104">在 Azure 中建立 Active Directory Domain Services (AD DS) 資源樹系</span><span class="sxs-lookup"><span data-stu-id="3119e-104">Create an Active Directory Domain Services (AD DS) resource forest in Azure</span></span>

<span data-ttu-id="3119e-105">此參考架構會顯示如何在 Azure 中建立受到內部部署 AD 樹系內網域信任的另一個 Active Directory 網域。</span><span class="sxs-lookup"><span data-stu-id="3119e-105">This reference architecture shows how to create a separate Active Directory domain in Azure that is trusted by domains in your on-premises AD forest.</span></span> [<span data-ttu-id="3119e-106">**部署這個解決方案**。</span><span class="sxs-lookup"><span data-stu-id="3119e-106">**Deploy this solution**.</span></span>](#deploy-the-solution)

<span data-ttu-id="3119e-107">[![0]][0]</span><span class="sxs-lookup"><span data-stu-id="3119e-107">[![0]][0]</span></span> 

<span data-ttu-id="3119e-108">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="3119e-108">*Download a [Visio file][visio-download] of this architecture.*</span></span>

<span data-ttu-id="3119e-109">Active Directory Domain Services (AD DS) 會以階層式結構儲存身分識別資訊。</span><span class="sxs-lookup"><span data-stu-id="3119e-109">Active Directory Domain Services (AD DS) stores identity information in a hierarchical structure.</span></span> <span data-ttu-id="3119e-110">階層式結構中的最上層節點稱為樹系。</span><span class="sxs-lookup"><span data-stu-id="3119e-110">The top node in the hierarchical structure is known as a forest.</span></span> <span data-ttu-id="3119e-111">樹系包含網域，而網域則包含其他類型的物件。</span><span class="sxs-lookup"><span data-stu-id="3119e-111">A forest contains domains, and domains contain other types of objects.</span></span> <span data-ttu-id="3119e-112">此參考架構會在 Azure 中建立與內部部署網域具有單向連出信任關係的 AD DS 樹系。</span><span class="sxs-lookup"><span data-stu-id="3119e-112">This reference architecture creates an AD DS forest in Azure with a one-way outgoing trust relationship with an on-premises domain.</span></span> <span data-ttu-id="3119e-113">Azure 中的樹系包含不存在於內部部署的網域。</span><span class="sxs-lookup"><span data-stu-id="3119e-113">The forest in Azure contains a domain that does not exist on-premises.</span></span> <span data-ttu-id="3119e-114">因為信任關係，才能夠信任針對內部部署網域的登入，以存取個別 Azure 網域中的資源。</span><span class="sxs-lookup"><span data-stu-id="3119e-114">Because of the trust relationship, logons made against on-premises domains can be trusted for access to resources in the separate Azure domain.</span></span> 

<span data-ttu-id="3119e-115">這個架構的典型用途包括維持雲端中保留之物件和身分識別的安全性隔離，以及將個別網域從內部部署移轉到雲端。</span><span class="sxs-lookup"><span data-stu-id="3119e-115">Typical uses for this architecture include maintaining security separation for objects and identities held in the cloud, and migrating individual domains from on-premises to the cloud.</span></span> 

<span data-ttu-id="3119e-116">如需了解其他考量，請參閱[選擇解決方案以整合內部部署 Active Directory 與 Azure][considerations]。</span><span class="sxs-lookup"><span data-stu-id="3119e-116">For additional considerations, see [Choose a solution for integrating on-premises Active Directory with Azure][considerations].</span></span> 

## <a name="architecture"></a><span data-ttu-id="3119e-117">架構</span><span class="sxs-lookup"><span data-stu-id="3119e-117">Architecture</span></span>

<span data-ttu-id="3119e-118">此架構具有下列元件。</span><span class="sxs-lookup"><span data-stu-id="3119e-118">The architecture has the following components.</span></span>

* <span data-ttu-id="3119e-119">**內部部署網路**。</span><span class="sxs-lookup"><span data-stu-id="3119e-119">**On-premises network**.</span></span> <span data-ttu-id="3119e-120">內部部署網路包含自己的 Active Directory 樹系和網域。</span><span class="sxs-lookup"><span data-stu-id="3119e-120">The on-premises network contains its own Active Directory forest and domains.</span></span>
* <span data-ttu-id="3119e-121">**Active Directory 伺服器**。</span><span class="sxs-lookup"><span data-stu-id="3119e-121">**Active Directory servers**.</span></span> <span data-ttu-id="3119e-122">這些是雲端中實作當作 VM 執行之網域服務的網域控制站。</span><span class="sxs-lookup"><span data-stu-id="3119e-122">These are domain controllers implementing domain services running as VMs in the cloud.</span></span> <span data-ttu-id="3119e-123">這些伺服器會裝載包含一個或多個網域的樹系，而且與內部部署的網域不同。</span><span class="sxs-lookup"><span data-stu-id="3119e-123">These servers host a forest containing one or more domains, separate from those located on-premises.</span></span>
* <span data-ttu-id="3119e-124">**單向信任關係**。</span><span class="sxs-lookup"><span data-stu-id="3119e-124">**One-way trust relationship**.</span></span> <span data-ttu-id="3119e-125">圖表中的範例顯示 Azure 中網域到內部部署網域的單向信任。</span><span class="sxs-lookup"><span data-stu-id="3119e-125">The example in the diagram shows a one-way trust from the domain in Azure to the on-premises domain.</span></span> <span data-ttu-id="3119e-126">這種關係可讓內部部署使用者在 Azure 中存取網域內的資源，但不適用於反向。</span><span class="sxs-lookup"><span data-stu-id="3119e-126">This relationship enables on-premises users to access resources in the domain in Azure, but not the other way around.</span></span> <span data-ttu-id="3119e-127">如果雲端使用者也需要存取內部部署資源，您可以建立雙向信任。</span><span class="sxs-lookup"><span data-stu-id="3119e-127">It is possible to create a two-way trust if cloud users also require access to on-premises resources.</span></span>
* <span data-ttu-id="3119e-128">**Active Directory 子網路**。</span><span class="sxs-lookup"><span data-stu-id="3119e-128">**Active Directory subnet**.</span></span> <span data-ttu-id="3119e-129">AD DS 伺服器會裝載在不同的子網路中。</span><span class="sxs-lookup"><span data-stu-id="3119e-129">The AD DS servers are hosted in a separate subnet.</span></span> <span data-ttu-id="3119e-130">網路安全性群組 (NSG) 規則可保護 AD DS 伺服器，並提供防火牆以防禦來自非預期來源的流量。</span><span class="sxs-lookup"><span data-stu-id="3119e-130">Network security group (NSG) rules protect the AD DS servers and provide a firewall against traffic from unexpected sources.</span></span>
* <span data-ttu-id="3119e-131">**Azure 閘道**。</span><span class="sxs-lookup"><span data-stu-id="3119e-131">**Azure gateway**.</span></span> <span data-ttu-id="3119e-132">Azure 閘道會提供內部部署網路與 Azure VNet 之間的連線。</span><span class="sxs-lookup"><span data-stu-id="3119e-132">The Azure gateway provides a connection between the on-premises network and the Azure VNet.</span></span> <span data-ttu-id="3119e-133">這可能是 [VPN 連線][azure-vpn-gateway]或 [Azure ExpressRoute][azure-expressroute]。</span><span class="sxs-lookup"><span data-stu-id="3119e-133">This can be a [VPN connection][azure-vpn-gateway] or [Azure ExpressRoute][azure-expressroute].</span></span> <span data-ttu-id="3119e-134">如需詳細資訊，請參閱[在 Azure 中實作安全的混合式網路架構][implementing-a-secure-hybrid-network-architecture]。</span><span class="sxs-lookup"><span data-stu-id="3119e-134">For more information, see [Implementing a secure hybrid network architecture in Azure][implementing-a-secure-hybrid-network-architecture].</span></span>

## <a name="recommendations"></a><span data-ttu-id="3119e-135">建議</span><span class="sxs-lookup"><span data-stu-id="3119e-135">Recommendations</span></span>

<span data-ttu-id="3119e-136">如需在 Azure 中實作 Active Directory 的特定建議，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="3119e-136">For specific recommendations on implementing Active Directory in Azure, see the following articles:</span></span>

- <span data-ttu-id="3119e-137">[將 Active Directory Domain Services (AD DS) 擴充至 Azure][adds-extend-domain]。</span><span class="sxs-lookup"><span data-stu-id="3119e-137">[Extending Active Directory Domain Services (AD DS) to Azure][adds-extend-domain].</span></span> 
- <span data-ttu-id="3119e-138">[在 Azure 虛擬機器上部署 Windows Server Active Directory 的指導方針][ad-azure-guidelines]。</span><span class="sxs-lookup"><span data-stu-id="3119e-138">[Guidelines for Deploying Windows Server Active Directory on Azure Virtual Machines][ad-azure-guidelines].</span></span>

### <a name="trust"></a><span data-ttu-id="3119e-139">信任</span><span class="sxs-lookup"><span data-stu-id="3119e-139">Trust</span></span>

<span data-ttu-id="3119e-140">內部部署網域包含在雲端網域中的不同樹系內。</span><span class="sxs-lookup"><span data-stu-id="3119e-140">The on-premises domains are contained within a different forest from the domains in the cloud.</span></span> <span data-ttu-id="3119e-141">若要在雲端驗證內部部署使用者，Azure 中的網域必須信任內部部署樹系中的登入網域。</span><span class="sxs-lookup"><span data-stu-id="3119e-141">To enable authentication of on-premises users in the cloud, the domains in Azure must trust the logon domain in the on-premises forest.</span></span> <span data-ttu-id="3119e-142">同樣地，如果雲端為外部使用者提供登入網域，內部部署樹系也需要信任雲端網域。</span><span class="sxs-lookup"><span data-stu-id="3119e-142">Similarly, if the cloud provides a logon domain for external users, it may be necessary for the on-premises forest to trust the cloud domain.</span></span>

<span data-ttu-id="3119e-143">您可以在樹系層級[建立樹系信任][creating-forest-trusts]，也可以在網域層級[建立外部信任][creating-external-trusts]來建立信任。</span><span class="sxs-lookup"><span data-stu-id="3119e-143">You can establish trusts at the forest level by [creating forest trusts][creating-forest-trusts], or at the domain level by [creating external trusts][creating-external-trusts].</span></span> <span data-ttu-id="3119e-144">樹系層級信任會建立兩個樹系中所有網域之間的關係。</span><span class="sxs-lookup"><span data-stu-id="3119e-144">A forest level trust creates a relationship between all domains in two forests.</span></span> <span data-ttu-id="3119e-145">外部網域層級信任只會建立兩個指定網域之間的關係。</span><span class="sxs-lookup"><span data-stu-id="3119e-145">An external domain level trust only creates a relationship between two specified domains.</span></span> <span data-ttu-id="3119e-146">您僅應在不同樹系的網域之間建立外部網域層級信任。</span><span class="sxs-lookup"><span data-stu-id="3119e-146">You should only create external domain level trusts between domains in different forests.</span></span>

<span data-ttu-id="3119e-147">信任可以是單向或雙向：</span><span class="sxs-lookup"><span data-stu-id="3119e-147">Trusts can be unidirectional (one-way) or bidirectional (two-way):</span></span>

* <span data-ttu-id="3119e-148">單向信任可讓一個網域或樹系 (稱為「連入」網域或樹系) 中的使用者存取另一個網域或樹系 (「連出」網域或樹系) 中保留的資源。</span><span class="sxs-lookup"><span data-stu-id="3119e-148">A one-way trust enables users in one domain or forest (known as the *incoming* domain or forest) to access the resources held in another (the *outgoing* domain or forest).</span></span>
* <span data-ttu-id="3119e-149">雙向信任可讓網域或樹系中的使用者存取其他網域或樹系中保留的資源。</span><span class="sxs-lookup"><span data-stu-id="3119e-149">A two-way trust enables users in either domain or forest to access resources held in the other.</span></span>

<span data-ttu-id="3119e-150">下表摘要說明一些簡單案例的信任設定：</span><span class="sxs-lookup"><span data-stu-id="3119e-150">The following table summarizes trust configurations for some simple scenarios:</span></span>

| <span data-ttu-id="3119e-151">案例</span><span class="sxs-lookup"><span data-stu-id="3119e-151">Scenario</span></span> | <span data-ttu-id="3119e-152">內部部署信任</span><span class="sxs-lookup"><span data-stu-id="3119e-152">On-premises trust</span></span> | <span data-ttu-id="3119e-153">雲端信任</span><span class="sxs-lookup"><span data-stu-id="3119e-153">Cloud trust</span></span> |
| --- | --- | --- |
| <span data-ttu-id="3119e-154">內部部署使用者需要存取雲端的資源，但反之則否</span><span class="sxs-lookup"><span data-stu-id="3119e-154">On-premises users require access to resources in the cloud, but not vice versa</span></span> |<span data-ttu-id="3119e-155">單向，連入</span><span class="sxs-lookup"><span data-stu-id="3119e-155">One-way, incoming</span></span> |<span data-ttu-id="3119e-156">單向，連出</span><span class="sxs-lookup"><span data-stu-id="3119e-156">One-way, outgoing</span></span> |
| <span data-ttu-id="3119e-157">雲端使用者需要存取內部部署的資源，但反之則否</span><span class="sxs-lookup"><span data-stu-id="3119e-157">Users in the cloud require access to resources located on-premises, but not vice versa</span></span> |<span data-ttu-id="3119e-158">單向，連出</span><span class="sxs-lookup"><span data-stu-id="3119e-158">One-way, outgoing</span></span> |<span data-ttu-id="3119e-159">單向，連入</span><span class="sxs-lookup"><span data-stu-id="3119e-159">One-way, incoming</span></span> |
| <span data-ttu-id="3119e-160">雲端和內部部署的使用者需要同時存取雲端和內部部署保留的資源</span><span class="sxs-lookup"><span data-stu-id="3119e-160">Users in the cloud and on-premises both requires access to resources held in the cloud and on-premises</span></span> |<span data-ttu-id="3119e-161">雙向，連入和連出</span><span class="sxs-lookup"><span data-stu-id="3119e-161">Two-way, incoming and outgoing</span></span> |<span data-ttu-id="3119e-162">雙向，連入和連出</span><span class="sxs-lookup"><span data-stu-id="3119e-162">Two-way, incoming and outgoing</span></span> |

## <a name="scalability-considerations"></a><span data-ttu-id="3119e-163">延展性考量</span><span class="sxs-lookup"><span data-stu-id="3119e-163">Scalability considerations</span></span>

<span data-ttu-id="3119e-164">Active Directory 會針對屬於相同網域的網域控制站自動進行調整。</span><span class="sxs-lookup"><span data-stu-id="3119e-164">Active Directory is automatically scalable for domain controllers that are part of the same domain.</span></span> <span data-ttu-id="3119e-165">要求會分散到網域內的所有控制站。</span><span class="sxs-lookup"><span data-stu-id="3119e-165">Requests are distributed across all controllers within a domain.</span></span> <span data-ttu-id="3119e-166">您可以新增另一個網域控制站，而且該網域控制站會自動與網域同步。</span><span class="sxs-lookup"><span data-stu-id="3119e-166">You can add another domain controller, and it synchronizes automatically with the domain.</span></span> <span data-ttu-id="3119e-167">請勿將個別的負載平衡器設定為將流量導向至網域內的控制站。</span><span class="sxs-lookup"><span data-stu-id="3119e-167">Do not configure a separate load balancer to direct traffic to controllers within the domain.</span></span> <span data-ttu-id="3119e-168">請確定所有網域控制站都有足夠的記憶體和儲存體資源，可處理網域資料庫。</span><span class="sxs-lookup"><span data-stu-id="3119e-168">Ensure that all domain controllers have sufficient memory and storage resources to handle the domain database.</span></span> <span data-ttu-id="3119e-169">將所有網域控制站 VM 的大小設為相同。</span><span class="sxs-lookup"><span data-stu-id="3119e-169">Make all domain controller VMs the same size.</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="3119e-170">可用性考量</span><span class="sxs-lookup"><span data-stu-id="3119e-170">Availability considerations</span></span>

<span data-ttu-id="3119e-171">針對每個網域至少佈建兩個網域控制站。</span><span class="sxs-lookup"><span data-stu-id="3119e-171">Provision at least two domain controllers for each domain.</span></span> <span data-ttu-id="3119e-172">如此可在伺服器之間啟用自動複寫。</span><span class="sxs-lookup"><span data-stu-id="3119e-172">This enables automatic replication between servers.</span></span> <span data-ttu-id="3119e-173">針對當作處理每個網域之 Active Directory 伺服器的 VM，建立可用性設定組。</span><span class="sxs-lookup"><span data-stu-id="3119e-173">Create an availability set for the VMs acting as Active Directory servers handling each domain.</span></span> <span data-ttu-id="3119e-174">在此可用性設定組中至少放置兩部伺服器。</span><span class="sxs-lookup"><span data-stu-id="3119e-174">Put at least two servers in this availability set.</span></span>

<span data-ttu-id="3119e-175">此外，如果作為彈性單一主機操作 (FSMO) 角色之伺服器的連線失敗，請考慮將每個網域中的一部或多部伺服器指定為[待命操作主機][standby-operations-masters]。</span><span class="sxs-lookup"><span data-stu-id="3119e-175">Also, consider designating one or more servers in each domain as [standby operations masters][standby-operations-masters] in case connectivity to a server acting as a flexible single master operation (FSMO) role fails.</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="3119e-176">管理性考量</span><span class="sxs-lookup"><span data-stu-id="3119e-176">Manageability considerations</span></span>

<span data-ttu-id="3119e-177">如需管理與監視考量的相關資訊，請參閱[將 Active Directory 擴充至 Azure][adds-extend-domain]。</span><span class="sxs-lookup"><span data-stu-id="3119e-177">For information about management and monitoring considerations, see [Extending Active Directory to Azure][adds-extend-domain].</span></span> 
 
<span data-ttu-id="3119e-178">如需其他資訊，請參閱[監視 Active Directory][monitoring_ad]。</span><span class="sxs-lookup"><span data-stu-id="3119e-178">For additional information, see [Monitoring Active Directory][monitoring_ad].</span></span> <span data-ttu-id="3119e-179">您可以在管理子網路中的監視伺服器上安裝工具 (例如 [Microsoft Systems Center][microsoft_systems_center]) 以協助執行這些工作。</span><span class="sxs-lookup"><span data-stu-id="3119e-179">You can install tools such as [Microsoft Systems Center][microsoft_systems_center] on a monitoring server in the management subnet to help perform these tasks.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="3119e-180">安全性考量</span><span class="sxs-lookup"><span data-stu-id="3119e-180">Security considerations</span></span>

<span data-ttu-id="3119e-181">樹系層級信任是可轉移的。</span><span class="sxs-lookup"><span data-stu-id="3119e-181">Forest level trusts are transitive.</span></span> <span data-ttu-id="3119e-182">如果您在內部部署樹系與雲端樹系之間建立樹系層級信任，此信任會擴充至任一樹系中建立的其他新網域。</span><span class="sxs-lookup"><span data-stu-id="3119e-182">If you establish a forest level trust between an on-premises forest and a forest in the cloud, this trust is extended to other new domains created in either forest.</span></span> <span data-ttu-id="3119e-183">如果您基於安全性目的而使用網域提供區隔，請考慮僅在網域層級建立信任。</span><span class="sxs-lookup"><span data-stu-id="3119e-183">If you use domains to provide separation for security purposes, consider creating trusts at the domain level only.</span></span> <span data-ttu-id="3119e-184">網域層級信任是不可轉移的。</span><span class="sxs-lookup"><span data-stu-id="3119e-184">Domain level trusts are non-transitive.</span></span>

<span data-ttu-id="3119e-185">如需了解 Active Directory 專屬的安全性考量，請參閱[將 Active Directory 擴充至 Azure][adds-extend-domain] 中的＜安全性考量＞一節。</span><span class="sxs-lookup"><span data-stu-id="3119e-185">For Active Directory-specific security considerations, see the security considerations section in [Extending Active Directory to Azure][adds-extend-domain].</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="3119e-186">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="3119e-186">Deploy the solution</span></span>

<span data-ttu-id="3119e-187">部署此參考架構的解決方案可在 [GitHub][github] 上取得。</span><span class="sxs-lookup"><span data-stu-id="3119e-187">A solution is available on [GitHub][github] to deploy this reference architecture.</span></span> <span data-ttu-id="3119e-188">您需要最新版的 Azure CLI，才能執行可部署解決方案的 PowerShell 指令碼。</span><span class="sxs-lookup"><span data-stu-id="3119e-188">You will need the latest version of the Azure CLI to run the Powershell script that deploys the solution.</span></span> <span data-ttu-id="3119e-189">若要部署參考架構，請依照下列步驟執行：</span><span class="sxs-lookup"><span data-stu-id="3119e-189">To deploy the reference architecture, follow these steps:</span></span>

1. <span data-ttu-id="3119e-190">將解決方案資料夾從 [GitHub][github] 下載或複製到本機電腦。</span><span class="sxs-lookup"><span data-stu-id="3119e-190">Download or clone the solution folder from [GitHub][github] to your local machine.</span></span>

2. <span data-ttu-id="3119e-191">開啟 Azure CLI，並瀏覽至本機解決方案資料夾。</span><span class="sxs-lookup"><span data-stu-id="3119e-191">Open the Azure CLI and navigate to the local solution folder.</span></span>

3. <span data-ttu-id="3119e-192">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="3119e-192">Run the following command:</span></span>
   
    ```Powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```
   
    <span data-ttu-id="3119e-193">使用您的 Azure 訂用帳戶識別碼來取代 `<subscription id>` 。</span><span class="sxs-lookup"><span data-stu-id="3119e-193">Replace `<subscription id>` with your Azure subscription ID.</span></span>
   
    <span data-ttu-id="3119e-194">針對 `<location>`，請指定 Azure 區域，例如 `eastus` 或 `westus`。</span><span class="sxs-lookup"><span data-stu-id="3119e-194">For `<location>`, specify an Azure region, such as `eastus` or `westus`.</span></span>
   
    <span data-ttu-id="3119e-195">`<mode>` 參數可精密控制部署，而且可以是下列其中一個值：</span><span class="sxs-lookup"><span data-stu-id="3119e-195">The `<mode>` parameter controls the granularity of the deployment, and can be one of the following values:</span></span>
   
   * <span data-ttu-id="3119e-196">`Onpremise`：部署模擬的內部部署環境。</span><span class="sxs-lookup"><span data-stu-id="3119e-196">`Onpremise`: deploys the simulated on-premises environment.</span></span>
   * <span data-ttu-id="3119e-197">`Infrastructure`：在 Azure 中部署 VNet 基礎結構和跳躍箱。</span><span class="sxs-lookup"><span data-stu-id="3119e-197">`Infrastructure`: deploys the VNet infrastructure and jump box in Azure.</span></span>
   * <span data-ttu-id="3119e-198">`CreateVpn`：部署 Azure 虛擬網路閘道，並將其連線到模擬的內部部署網路。</span><span class="sxs-lookup"><span data-stu-id="3119e-198">`CreateVpn`: deploys the Azure virtual network gateway and connects it to the simulated on-premises network.</span></span>
   * <span data-ttu-id="3119e-199">`AzureADDS`：部署當作 Active Directory DS 伺服器的 VM、將 Active Directory 部署到這些 VM，然後在 Azure 中部署網域。</span><span class="sxs-lookup"><span data-stu-id="3119e-199">`AzureADDS`: deploys the VMs acting as Active Directory DS servers, deploys Active Directory to these VMs, and deploys the domain in Azure.</span></span>
   * <span data-ttu-id="3119e-200">`WebTier`：部署 Web 層 VM 和負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="3119e-200">`WebTier`: deploys the web tier VMs and load balancer.</span></span>
   * <span data-ttu-id="3119e-201">`Prepare`：部署所有先前的部署。</span><span class="sxs-lookup"><span data-stu-id="3119e-201">`Prepare`: deploys all of the preceding deployments.</span></span> <span data-ttu-id="3119e-202">**如果您沒有現有的內部部署網路，但您想要部署上述的完整參考架構以供測試或評估之用，這是建議的選項。**</span><span class="sxs-lookup"><span data-stu-id="3119e-202">**This is the recommended option if If you do not have an existing on-premises network but you want to deploy the complete reference architecture described above for testing or evaluation.**</span></span> 
   * <span data-ttu-id="3119e-203">`Workload`：部署商務和資料層 VM 以及負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="3119e-203">`Workload`: deploys the business and data tier VMs and load balancers.</span></span> <span data-ttu-id="3119e-204">請注意，這些 VM 不包含在 `Prepare` 部署中。</span><span class="sxs-lookup"><span data-stu-id="3119e-204">Note that these VMs are not included in the `Prepare` deployment.</span></span>

4. <span data-ttu-id="3119e-205">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="3119e-205">Wait for the deployment to complete.</span></span> <span data-ttu-id="3119e-206">如果您要部署 `Prepare` 部署，將需要數小時的時間。</span><span class="sxs-lookup"><span data-stu-id="3119e-206">If you are deploying the `Prepare` deployment, it will take several hours.</span></span>
     
5. <span data-ttu-id="3119e-207">如果您要使用模擬的內部部署設定，請設定連入信任關係：</span><span class="sxs-lookup"><span data-stu-id="3119e-207">If you are using the simulated on-premises configuration, configure the incoming trust relationship:</span></span>
   
   1. <span data-ttu-id="3119e-208">連線到跳躍箱 (*ra-adtrust-security-rg* 資源群組中的 *ra-adtrust-mgmt-vm1*)。</span><span class="sxs-lookup"><span data-stu-id="3119e-208">Connect to the jump box (*ra-adtrust-mgmt-vm1* in the *ra-adtrust-security-rg* resource group).</span></span> <span data-ttu-id="3119e-209">以 *testuser* 的身分和密碼 *AweS0me@PW* 登入。</span><span class="sxs-lookup"><span data-stu-id="3119e-209">Log in as *testuser* with password *AweS0me@PW*.</span></span>
   2. <span data-ttu-id="3119e-210">在跳躍箱上，開啟 *contoso.com* 網域 (內部部署網域) 第一個 VM 中的 RDP 工作階段。</span><span class="sxs-lookup"><span data-stu-id="3119e-210">On the jump box open an RDP session on the first VM in the *contoso.com* domain (the on-premises domain).</span></span> <span data-ttu-id="3119e-211">此 VM 的 IP 位址為 192.168.0.4。</span><span class="sxs-lookup"><span data-stu-id="3119e-211">This VM has the IP address 192.168.0.4.</span></span> <span data-ttu-id="3119e-212">使用者名稱為 *contoso\testuser*，且密碼為 *AweS0me@PW*。</span><span class="sxs-lookup"><span data-stu-id="3119e-212">The username is *contoso\testuser* with password *AweS0me@PW*.</span></span>
   3. <span data-ttu-id="3119e-213">下載 [incoming-trust.ps1][incoming-trust] 指令碼並執行，以便從 *treyresearch.com* 網域建立連入信任。</span><span class="sxs-lookup"><span data-stu-id="3119e-213">Download the [incoming-trust.ps1][incoming-trust] script and run it to create the incoming trust from the *treyresearch.com* domain.</span></span>

6. <span data-ttu-id="3119e-214">如果您要使用自己的內部部署基礎結構：</span><span class="sxs-lookup"><span data-stu-id="3119e-214">If you are using your own on-premises infrastructure:</span></span>
   
   1. <span data-ttu-id="3119e-215">下載 [incoming-trust.ps1][incoming-trust] 指令碼。</span><span class="sxs-lookup"><span data-stu-id="3119e-215">Download the [incoming-trust.ps1][incoming-trust] script.</span></span>
   2. <span data-ttu-id="3119e-216">編輯指令碼，並將 `$TrustedDomainName` 變數的值取代為您自己網域的名稱。</span><span class="sxs-lookup"><span data-stu-id="3119e-216">Edit the script and replace the value of the `$TrustedDomainName` variable with the name of your own domain.</span></span>
   3. <span data-ttu-id="3119e-217">執行指令碼。</span><span class="sxs-lookup"><span data-stu-id="3119e-217">Run the script.</span></span>

7. <span data-ttu-id="3119e-218">從跳躍箱連線至 *treyresearch.com* 網域 (雲端中的網域) 中的第一個 VM。</span><span class="sxs-lookup"><span data-stu-id="3119e-218">From the jump-box, connect to the first VM in the *treyresearch.com* domain (the domain in the cloud).</span></span> <span data-ttu-id="3119e-219">此 VM 的 IP 位址為 10.0.4.4。</span><span class="sxs-lookup"><span data-stu-id="3119e-219">This VM has the IP address 10.0.4.4.</span></span> <span data-ttu-id="3119e-220">使用者名稱為 *treyresearch\testuser*，且密碼為 *AweS0me@PW*。</span><span class="sxs-lookup"><span data-stu-id="3119e-220">The username is *treyresearch\testuser* with password *AweS0me@PW*.</span></span>

8. <span data-ttu-id="3119e-221">下載 [outgoing-trust.ps1][outgoing-trust] 指令碼並加以執行，以便從 *treyresearch.com* 網域建立連入信任。</span><span class="sxs-lookup"><span data-stu-id="3119e-221">Download the [outgoing-trust.ps1][outgoing-trust] script and run it to create the incoming trust from the *treyresearch.com* domain.</span></span> <span data-ttu-id="3119e-222">如果您要使用自己的內部部署電腦，請先編輯指令碼。</span><span class="sxs-lookup"><span data-stu-id="3119e-222">If you are using your own on-premises machines, then edit the script first.</span></span> <span data-ttu-id="3119e-223">將 `$TrustedDomainName` 變數設為您內部部署網域的名稱，並使用 `$TrustedDomainDnsIpAddresses` 變數，針對此網域指定 Active Directory DS 伺服器的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="3119e-223">Set the `$TrustedDomainName` variable to the name of your on-premises domain, and specify the IP addresses of the Active Directory DS servers for this domain in the `$TrustedDomainDnsIpAddresses` variable.</span></span>

9. <span data-ttu-id="3119e-224">等待幾分鐘，讓上述步驟完成，然後連線至內部部署 VM，並執行[驗證信任][verify-a-trust]一文中所述的步驟，來判斷 *contoso.com* 和 *treyresearch.com* 網域之間的信任關係是否已正確設定。</span><span class="sxs-lookup"><span data-stu-id="3119e-224">Wait a few minutes for the previous steps to complete, then connect to an on-premises VM and perform the steps outlined in the article [Verify a Trust][verify-a-trust] to determine whether the trust relationship between the *contoso.com* and *treyresearch.com* domains is correctly configured.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3119e-225">後續步驟</span><span class="sxs-lookup"><span data-stu-id="3119e-225">Next steps</span></span>

* <span data-ttu-id="3119e-226">了解[將內部部署 AD DS 網域擴充至 Azure][adds-extend-domain] 的最佳做法</span><span class="sxs-lookup"><span data-stu-id="3119e-226">Learn the best practices for [extending your on-premises AD DS domain to Azure][adds-extend-domain]</span></span>
* <span data-ttu-id="3119e-227">了解[在 Azure 中建立 AD FS 基礎結構][adfs]的最佳做法。</span><span class="sxs-lookup"><span data-stu-id="3119e-227">Learn the best practices for [creating an AD FS infrastructure][adfs] in Azure.</span></span>

<!-- links -->
[adds-extend-domain]: adds-extend-domain.md
[adfs]: adfs.md

[implementing-a-secure-hybrid-network-architecture]: ../dmz/secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ../dmz/secure-vnet-dmz.md

[running-VMs-for-an-N-tier-architecture-on-Azure]: ../virtual-machines-windows/n-tier.md

[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[considerations]: ./considerations.md
[creating-external-trusts]: https://technet.microsoft.com/library/cc816837(v=ws.10).aspx
[creating-forest-trusts]: https://technet.microsoft.com/library/cc816810(v=ws.10).aspx
[github]: https://github.com/mspnp/reference-architectures/tree/master/identity/adds-forest
[incoming-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/identity/adds-forest/extensions/incoming-trust.ps1
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/identity/adds-forest/Deploy-ReferenceArchitecture.ps1
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[outgoing-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/identity/adds-forest/extensions/outgoing-trust.ps1
[verify-a-trust]: https://technet.microsoft.com/library/cc753821.aspx
[visio-download]: https://archcenter.azureedge.net/cdn/identity-architectures.vsdx
[0]: ./images/adds-forest.png "使用不同的 Active Directory 網域保護混合式網路架構的安全"
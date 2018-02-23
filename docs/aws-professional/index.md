---
title: "Azure for AWS 專業人員"
description: "了解 Microsoft Azure 帳戶、平台和服務的基本概念。 並了解 AWS 和 Azure 平台之間的金鑰相似度和差異。 利用您在 Azure 中的 AWS 體驗。"
keywords: "AWS 專家, Azure 比較, AWS 比較, azure 與 aws 之間的差異, azure 與 aws"
author: lbrader
ms.date: 03/24/2017
pnp.series.title: Azure for AWS Professionals
ms.openlocfilehash: b8698675efa42bb3fae73cefe7b078942549b412
ms.sourcegitcommit: 3d9ee03e2dda23753661a80c7106d1789f5223bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2018
---
# <a name="azure-for-aws-professionals"></a><span data-ttu-id="69b13-106">適用於 AWS 專業人員的 Azure</span><span class="sxs-lookup"><span data-stu-id="69b13-106">Azure for AWS Professionals</span></span>

<span data-ttu-id="69b13-107">本文可協助 Amazon Web Services (AWS) 專家了解 Microsoft Azure 帳戶、平台和服務的基本概念。</span><span class="sxs-lookup"><span data-stu-id="69b13-107">This article helps Amazon Web Services (AWS) experts understand the basics of Microsoft Azure accounts, platform, and services.</span></span> <span data-ttu-id="69b13-108">其中也涵蓋 AWS 和 Azure 平台之間的主要相似度和差異。</span><span class="sxs-lookup"><span data-stu-id="69b13-108">It also covers key similarities and differences between the AWS and Azure platforms.</span></span>

<span data-ttu-id="69b13-109">您將了解：</span><span class="sxs-lookup"><span data-stu-id="69b13-109">You'll learn:</span></span>

* <span data-ttu-id="69b13-110">帳戶和資源在 Azure 中組織的方式。</span><span class="sxs-lookup"><span data-stu-id="69b13-110">How accounts and resources are organized in Azure.</span></span>
* <span data-ttu-id="69b13-111">Azure 中可用解決方案的結構方式。</span><span class="sxs-lookup"><span data-stu-id="69b13-111">How available solutions are structured in Azure.</span></span>
* <span data-ttu-id="69b13-112">主要 Azure 服務與 AWS 服務有何差異。</span><span class="sxs-lookup"><span data-stu-id="69b13-112">How the major Azure services differ from AWS services.</span></span>

<span data-ttu-id="69b13-113">Azure 和 AWS 會隨時間獨立建置其功能，讓每個都有重要實作和設計的差異。</span><span class="sxs-lookup"><span data-stu-id="69b13-113">Azure and AWS built their capabilities independently over time so that each has important implementation and design differences.</span></span>

## <a name="overview"></a><span data-ttu-id="69b13-114">概觀</span><span class="sxs-lookup"><span data-stu-id="69b13-114">Overview</span></span>

<span data-ttu-id="69b13-115">如同 AWS，Microsoft Azure 架構是以一組核心計算、儲存體、資料庫和網路服務所建置。</span><span class="sxs-lookup"><span data-stu-id="69b13-115">Like AWS, Microsoft Azure is built around a core set of compute, storage, database, and networking services.</span></span> <span data-ttu-id="69b13-116">在許多情況下，這兩個平台會在其提供的產品和服務之間提供基本等價。</span><span class="sxs-lookup"><span data-stu-id="69b13-116">In many cases, both platforms offer a basic equivalence between the products and services they offer.</span></span> <span data-ttu-id="69b13-117">AWS 和 Azure 都可讓您以 Windows 或 Linux 主機作為基礎，建置高可用性的解決方案。</span><span class="sxs-lookup"><span data-stu-id="69b13-117">Both AWS and Azure allow you to build highly available solutions based on Windows or Linux hosts.</span></span> <span data-ttu-id="69b13-118">因此，如果您習慣使用 Linux 及 OSS 技術的開發，這兩個平台皆可執行作業。</span><span class="sxs-lookup"><span data-stu-id="69b13-118">So, if you're used to development using Linux and OSS technology, both platforms can do the job.</span></span>

<span data-ttu-id="69b13-119">雖然這兩個平台的功能類似，提供這些功能的資源通常會以不同的方式組織。</span><span class="sxs-lookup"><span data-stu-id="69b13-119">While the capabilities of both platforms are similar, the resources that provide those capabilities are often organized differently.</span></span> <span data-ttu-id="69b13-120">建置解決方案所需服務之間的確切一對一關聯性並不一定顯而易見。</span><span class="sxs-lookup"><span data-stu-id="69b13-120">Exact one-to-one relationships between the services required to build a solution are not always clear.</span></span> <span data-ttu-id="69b13-121">也有一些情況下，可能會在一個平台上提供對特定服務，但其他平台則不提供。</span><span class="sxs-lookup"><span data-stu-id="69b13-121">There are also cases where a particular service might be offered on one platform, but not the other.</span></span> <span data-ttu-id="69b13-122">請參閱[可比較的 Azure 與 AWS 服務圖表](services.md)。</span><span class="sxs-lookup"><span data-stu-id="69b13-122">See [charts of comparable Azure and AWS services](services.md).</span></span>

## <a name="accounts-and-subscriptions"></a><span data-ttu-id="69b13-123">帳戶及訂用帳戶</span><span class="sxs-lookup"><span data-stu-id="69b13-123">Accounts and subscriptions</span></span>

<span data-ttu-id="69b13-124">可以使用數個定價選項來購買 Azure 服務，取決於貴組織的大小和需求。</span><span class="sxs-lookup"><span data-stu-id="69b13-124">Azure services can be purchased using several pricing options, depending on your organization's size and needs.</span></span> <span data-ttu-id="69b13-125">請參閱[定價概觀](https://azure.microsoft.com/pricing/)頁面以取得詳細資料。</span><span class="sxs-lookup"><span data-stu-id="69b13-125">See the [pricing overview](https://azure.microsoft.com/pricing/) page for details.</span></span>

<span data-ttu-id="69b13-126">[Azure 訂用帳戶](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-infrastructure-subscription-accounts-guidelines/)是具有指派擁有者的資源群組，負責帳單和權限管理。</span><span class="sxs-lookup"><span data-stu-id="69b13-126">[Azure subscriptions](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-infrastructure-subscription-accounts-guidelines/) are a grouping of resources with an assigned owner responsible for billing and permissions management.</span></span> <span data-ttu-id="69b13-127">不同於 AWS，其中在 AWS 帳戶下建立的任何資源都會繫結至該帳戶，訂用帳戶會與其擁有者帳戶分開存在，且可視需要重新指派給新的擁有者。</span><span class="sxs-lookup"><span data-stu-id="69b13-127">Unlike AWS, where any resources created under the AWS account are tied to that account, subscriptions exist independently of their owner accounts, and can be reassigned to new owners as needed.</span></span>

<span data-ttu-id="69b13-128">![比較 AWS 帳戶與 Azure 訂用帳戶的結構和擁有權](./images/azure-aws-account-compare.png "比較 AWS 帳戶與 Azure 訂用帳戶的結構和擁有權")</span><span class="sxs-lookup"><span data-stu-id="69b13-128">![Comparison of structure and ownership of AWS accounts and Azure subscriptions](./images/azure-aws-account-compare.png "Comparison of structure and ownership of AWS accounts and Azure subscriptions")</span></span>
<br/><span data-ttu-id="69b13-129">*比較 AWS 帳戶與 Azure 訂用帳戶的結構和 擁有權*</span><span class="sxs-lookup"><span data-stu-id="69b13-129">*Comparison of structure and ownership of AWS accounts and Azure subscriptions*</span></span>
<br/><br/>

<span data-ttu-id="69b13-130">有三種類型的系統管理員帳戶會指派給訂用帳戶：</span><span class="sxs-lookup"><span data-stu-id="69b13-130">Subscriptions are assigned three types of administrator accounts:</span></span>

-   <span data-ttu-id="69b13-131">**帳戶管理員** - 訂用帳戶擁有者和針對訂用帳戶中使用的資源計費的帳戶。</span><span class="sxs-lookup"><span data-stu-id="69b13-131">**Account Administrator** - The subscription owner and the account billed for the resources used in the subscription.</span></span> <span data-ttu-id="69b13-132">只能藉由轉移訂用帳戶的擁有權來變更帳戶管理員。</span><span class="sxs-lookup"><span data-stu-id="69b13-132">The account administrator can only be changed by transferring ownership of the subscription.</span></span>

-   <span data-ttu-id="69b13-133">**服務管理員** - 此帳戶有權限可建立及管理訂用帳戶中的資源，但不負責計費。</span><span class="sxs-lookup"><span data-stu-id="69b13-133">**Service Administrator** - This account has rights to create and manage resources in the subscription, but is not responsible for billing.</span></span> <span data-ttu-id="69b13-134">根據預設，帳戶管理員和服務管理員會指派給相同的帳戶。</span><span class="sxs-lookup"><span data-stu-id="69b13-134">By default, the account administrator and service administrator are assigned to the same account.</span></span> <span data-ttu-id="69b13-135">帳戶管理員可以將個別使用者指派給服務管理員帳戶，來管理訂用帳戶的技術和運作層面。</span><span class="sxs-lookup"><span data-stu-id="69b13-135">The account administrator can assign a separate user to the service administrator account for managing the technical and operational aspects of a subscription.</span></span> <span data-ttu-id="69b13-136">每個訂用帳戶只有一個服務管理員。</span><span class="sxs-lookup"><span data-stu-id="69b13-136">There is only one service administrator per subscription.</span></span>

-   <span data-ttu-id="69b13-137">**共同管理員** - 可指派多個共同管理員帳戶給訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="69b13-137">**Co-administrator** - There can be multiple co-administrator accounts assigned to a subscription.</span></span> <span data-ttu-id="69b13-138">共同管理員無法變更服務管理員，但擁有訂用帳戶資源和使用者的完整控制權。</span><span class="sxs-lookup"><span data-stu-id="69b13-138">Co-administrators cannot change the service administrator, but otherwise have full control over subscription resources and users.</span></span>

<span data-ttu-id="69b13-139">以下訂用帳戶層級使用者角色和個別權限也可以指派給特定的資源，類似於在 AWS 中將權限授與 IAM 使用者和群組的方式。</span><span class="sxs-lookup"><span data-stu-id="69b13-139">Below the subscription level user roles and individual permissions can also be assigned to specific resources, similarly to how permissions are granted to IAM users and groups in AWS.</span></span> <span data-ttu-id="69b13-140">在 Azure 中的所有使用者帳戶都會與 Microsoft 帳戶或組織帳戶 (透過 Azure Active Directory 管理的帳戶) 相關聯。</span><span class="sxs-lookup"><span data-stu-id="69b13-140">In Azure all user accounts are associated with either a Microsoft Account or Organizational Account (an account managed through an Azure Active Directory).</span></span>

<span data-ttu-id="69b13-141">如同 AWS 帳戶，訂用帳戶具有預設的服務配額和限制。</span><span class="sxs-lookup"><span data-stu-id="69b13-141">Like AWS accounts, subscriptions have default service quotas and limits.</span></span> <span data-ttu-id="69b13-142">如需這些限制的完整清單，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/)。</span><span class="sxs-lookup"><span data-stu-id="69b13-142">For a full list of these limits, see [Azure subscription and service limits, quotas, and constraints](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/).</span></span>
<span data-ttu-id="69b13-143">可以藉由[在管理入口網站中提出支援要求](https://blogs.msdn.microsoft.com/girishp/2015/09/20/increasing-core-quota-limits-in-azure/)，將這些限制增加到最大值。</span><span class="sxs-lookup"><span data-stu-id="69b13-143">These limits can be increased up to the maximum by [filing a support request in the management portal](https://blogs.msdn.microsoft.com/girishp/2015/09/20/increasing-core-quota-limits-in-azure/).</span></span>

### <a name="see-also"></a><span data-ttu-id="69b13-144">另請參閱</span><span class="sxs-lookup"><span data-stu-id="69b13-144">See also</span></span>

-   [<span data-ttu-id="69b13-145">如何新增或變更 Azure 管理員角色</span><span class="sxs-lookup"><span data-stu-id="69b13-145">How to add or change Azure administrator roles</span></span>](https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/)

-   [<span data-ttu-id="69b13-146">如何下載您的 Azure 帳單發票和每日使用量資料</span><span class="sxs-lookup"><span data-stu-id="69b13-146">How to download your Azure billing invoice and daily usage data</span></span>](https://azure.microsoft.com/documentation/articles/billing-download-azure-invoice-daily-usage-date/)

## <a name="resource-management"></a><span data-ttu-id="69b13-147">資源管理</span><span class="sxs-lookup"><span data-stu-id="69b13-147">Resource management</span></span>

<span data-ttu-id="69b13-148">Azure 中的「資源」一詞與 AWS 中的使用方式相同，這表示您可以在平台內建立或設定的任何計算執行個體、儲存物件、網路裝置或其他實體。</span><span class="sxs-lookup"><span data-stu-id="69b13-148">The term "resource" in Azure is used in the same way as in AWS, meaning any compute instance, storage object, networking device, or other entity you can create or configure within the platform.</span></span>

<span data-ttu-id="69b13-149">會使用下列兩個模型其中之一來部署及管理 Azure 資源：[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) 或較舊的 Azure [傳統部署模型](/azure/azure-resource-manager/resource-manager-deployment-model)。</span><span class="sxs-lookup"><span data-stu-id="69b13-149">Azure resources are deployed and managed using one of two models: [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview), or the older Azure [classic deployment model](/azure/azure-resource-manager/resource-manager-deployment-model).</span></span>
<span data-ttu-id="69b13-150">使用 Resource Manager 模型來建立任何新的資源。</span><span class="sxs-lookup"><span data-stu-id="69b13-150">Any new resources are created using the Resource Manager model.</span></span>

### <a name="resource-groups"></a><span data-ttu-id="69b13-151">資源群組</span><span class="sxs-lookup"><span data-stu-id="69b13-151">Resource groups</span></span>

<span data-ttu-id="69b13-152">Azure 和 AWS 都有稱為「資源群組」的實體，能組織諸如 VM、儲存體和虛擬網路裝置等資源。</span><span class="sxs-lookup"><span data-stu-id="69b13-152">Both Azure and AWS have entities called "resource groups" that organize resources such as VMs, storage, and virtual networking devices.</span></span> <span data-ttu-id="69b13-153">不過，[Azure 資源群組](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/)無法直接與 AWS 資源群組比較。</span><span class="sxs-lookup"><span data-stu-id="69b13-153">However, [Azure resource groups](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/) are not directly comparable to AWS resource groups.</span></span>

<span data-ttu-id="69b13-154">儘管 AWS 允許將資源標記成多個資源群組，但 Azure 資源一律與一個資源群組相關聯。</span><span class="sxs-lookup"><span data-stu-id="69b13-154">While AWS allows a resource to be tagged into multiple resource groups, an Azure resource is always associated with one resource group.</span></span> <span data-ttu-id="69b13-155">一個資源群組中建立的資源可以移至另一個群組，但一次只能在一個資源群組中。</span><span class="sxs-lookup"><span data-stu-id="69b13-155">A resource created in one resource group can be moved to another group, but can only be in one resource group at a time.</span></span> <span data-ttu-id="69b13-156">資源群組是 Azure Resource Manager 所使用的基本群組。</span><span class="sxs-lookup"><span data-stu-id="69b13-156">Resource groups are the fundamental grouping used by Azure Resource Manager.</span></span>

<span data-ttu-id="69b13-157">也可以使用[標記](https://azure.microsoft.com/documentation/articles/resource-group-using-tags/)來組織資源。</span><span class="sxs-lookup"><span data-stu-id="69b13-157">Resources can also be organized using [tags](https://azure.microsoft.com/documentation/articles/resource-group-using-tags/).</span></span>
<span data-ttu-id="69b13-158">標記是機碼值組，可讓您跨訂用帳戶群組資源，不論資源群組成員資格。</span><span class="sxs-lookup"><span data-stu-id="69b13-158">Tags are key-value pairs that allow you to group resources across your subscription irrespective of resource group membership.</span></span>

### <a name="management-interfaces"></a><span data-ttu-id="69b13-159">管理介面</span><span class="sxs-lookup"><span data-stu-id="69b13-159">Management interfaces</span></span>

<span data-ttu-id="69b13-160">Azure 提供數種方式來管理您的資源：</span><span class="sxs-lookup"><span data-stu-id="69b13-160">Azure offers several ways to manage your resources:</span></span>

-   <span data-ttu-id="69b13-161">[Web 介面](https://azure.microsoft.com/documentation/articles/resource-group-portal/)。</span><span class="sxs-lookup"><span data-stu-id="69b13-161">[Web interface](https://azure.microsoft.com/documentation/articles/resource-group-portal/).</span></span>
    <span data-ttu-id="69b13-162">如同 AWS 儀表板，Azure 入口網站會提供 Azure 資源的完整 web 型管理介面。</span><span class="sxs-lookup"><span data-stu-id="69b13-162">Like the AWS Dashboard, the Azure portal provides a full web-based management interface for Azure resources.</span></span>

-   <span data-ttu-id="69b13-163">[REST API](https://azure.microsoft.com/documentation/articles/resource-manager-rest-api/)。</span><span class="sxs-lookup"><span data-stu-id="69b13-163">[REST API](https://azure.microsoft.com/documentation/articles/resource-manager-rest-api/).</span></span>
    <span data-ttu-id="69b13-164">Azure Resource Manager REST API 會提供以程式設計方式存取 Azure 入口網站中大部分可用的功能。</span><span class="sxs-lookup"><span data-stu-id="69b13-164">The Azure Resource Manager REST API provides programmatic access to most of the features available in the Azure portal.</span></span>

-   <span data-ttu-id="69b13-165">[命令列](https://azure.microsoft.com/documentation/articles/xplat-cli-azure-resource-manager/)。</span><span class="sxs-lookup"><span data-stu-id="69b13-165">[Command Line](https://azure.microsoft.com/documentation/articles/xplat-cli-azure-resource-manager/).</span></span>
    <span data-ttu-id="69b13-166">Azure CLI 2.0 工具提供的命令列介面能夠建立及管理 Azure 資源。</span><span class="sxs-lookup"><span data-stu-id="69b13-166">The Azure CLI 2.0 tool provides a command-line interface capable of creating and managing Azure resources.</span></span> <span data-ttu-id="69b13-167">Azure CLI 適用於 [Windows、Linux 和 Mac OS](https://aka.ms/azurecli2)。</span><span class="sxs-lookup"><span data-stu-id="69b13-167">Azure CLI is available for [Windows, Linux, and Mac OS](https://aka.ms/azurecli2).</span></span>

-   <span data-ttu-id="69b13-168">[PowerShell](https://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/)。</span><span class="sxs-lookup"><span data-stu-id="69b13-168">[PowerShell](https://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/).</span></span>
    <span data-ttu-id="69b13-169">適用於 PowerShell 的 Azure 模組可讓您使用指令碼執行自動化的管理工作。</span><span class="sxs-lookup"><span data-stu-id="69b13-169">The Azure modules for PowerShell allow you to execute automated management tasks using a script.</span></span> <span data-ttu-id="69b13-170">PowerShell 適用於 [Windows、Linux 和 Mac OS](https://github.com/PowerShell/PowerShell)。</span><span class="sxs-lookup"><span data-stu-id="69b13-170">PowerShell is available for [Windows, Linux, and Mac OS](https://github.com/PowerShell/PowerShell).</span></span>

-   <span data-ttu-id="69b13-171">[範本](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/)。</span><span class="sxs-lookup"><span data-stu-id="69b13-171">[Templates](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/).</span></span>
    <span data-ttu-id="69b13-172">Azure Resource Manager 範本會提供類似的以 JSON 範本為基礎之資源管理功能給 AWS CloudFormation 服務。</span><span class="sxs-lookup"><span data-stu-id="69b13-172">Azure Resource Manager templates provide similar JSON template-based resource management capabilities to the AWS CloudFormation service.</span></span>

<span data-ttu-id="69b13-173">在這些介面中，資源群組是 Azure 資源建立、部署或修改方式的核心。</span><span class="sxs-lookup"><span data-stu-id="69b13-173">In each of these interfaces, the resource group is central to how Azure resources get created, deployed, or modified.</span></span> <span data-ttu-id="69b13-174">這類似於「堆疊」在 CloudFormation 部署期間群組 AWS 資源所扮演的角色。</span><span class="sxs-lookup"><span data-stu-id="69b13-174">This is similar to the role a "stack" plays in grouping AWS resources during CloudFormation deployments.</span></span>

<span data-ttu-id="69b13-175">這些介面的語法和結構與其 AWS 對等項目不同，但可提供相當程度的功能。</span><span class="sxs-lookup"><span data-stu-id="69b13-175">The syntax and structure of these interfaces are different from their AWS equivalents, but they provide comparable capabilities.</span></span> <span data-ttu-id="69b13-176">此外，許多 AWS 上使用的第三方管理工具 (例如 [Hashicorp 的 Terraform](https://www.terraform.io/docs/providers/azurerm/) 和 [Netflix Spinnaker](http://www.spinnaker.io/) 也會在 Azure 上提供。</span><span class="sxs-lookup"><span data-stu-id="69b13-176">In addition, many third party management tools used on AWS, like [Hashicorp's Terraform](https://www.terraform.io/docs/providers/azurerm/) and [Netflix Spinnaker](http://www.spinnaker.io/), are also available on Azure.</span></span>

### <a name="see-also"></a><span data-ttu-id="69b13-177">另請參閱</span><span class="sxs-lookup"><span data-stu-id="69b13-177">See also</span></span>

-   [<span data-ttu-id="69b13-178">Azure 資源群組指導方針</span><span class="sxs-lookup"><span data-stu-id="69b13-178">Azure resource group guidelines</span></span>](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/)

## <a name="regions-and-zones-high-availability"></a><span data-ttu-id="69b13-179">地區和區域 (高可用性)</span><span class="sxs-lookup"><span data-stu-id="69b13-179">Regions and zones (high availability)</span></span>

<span data-ttu-id="69b13-180">失敗的影響範圍各不相同。</span><span class="sxs-lookup"><span data-stu-id="69b13-180">Failures can vary in the scope of their impact.</span></span> <span data-ttu-id="69b13-181">有些硬體失敗 (例如失敗的磁碟) 可能會影響單一主機電腦。</span><span class="sxs-lookup"><span data-stu-id="69b13-181">Some hardware failures, such as a failed disk, may affect a single host machine.</span></span> <span data-ttu-id="69b13-182">失敗的網路交換器則可能影響整個伺服器機架。</span><span class="sxs-lookup"><span data-stu-id="69b13-182">A failed network switch could affect a whole server rack.</span></span> <span data-ttu-id="69b13-183">較不常見的失敗是整個資料中心中斷，例如資料中心斷電。</span><span class="sxs-lookup"><span data-stu-id="69b13-183">Less common are failures that disrupt a whole data center, such as loss of power in a data center.</span></span> <span data-ttu-id="69b13-184">至於整個區域都變得無法使用的情況則很罕見。</span><span class="sxs-lookup"><span data-stu-id="69b13-184">Rarely, an entire region could become unavailable.</span></span>

<span data-ttu-id="69b13-185">其中一個讓應用程式具有復原能力的主要方法是透過備援。</span><span class="sxs-lookup"><span data-stu-id="69b13-185">One of the main ways to make an application resilient is through redundancy.</span></span> <span data-ttu-id="69b13-186">但您必須在設計應用程式時規劃此備援措施。</span><span class="sxs-lookup"><span data-stu-id="69b13-186">But you need to plan for this redundancy when you design the application.</span></span> <span data-ttu-id="69b13-187">此外，您需要的備援層級取決於您的業務需求，並非每個應用程式都需要跨區域備援，以防範區域性中斷。</span><span class="sxs-lookup"><span data-stu-id="69b13-187">Also, the level of redundancy that you need depends on your business requirements &mdash; not every application needs redundancy across regions to guard against a regional outage.</span></span> <span data-ttu-id="69b13-188">一般情況下，您需要在更好的備援性和可靠性與更高的成本和複雜性之間做出取捨。</span><span class="sxs-lookup"><span data-stu-id="69b13-188">In general, there is a tradeoff between greater redundancy and reliability versus higher cost and complexity.</span></span>  

<span data-ttu-id="69b13-189">在 AWS 中，地區會分割成兩個或多個可用性區域。</span><span class="sxs-lookup"><span data-stu-id="69b13-189">In AWS, a region is divided into two or more Availability Zones.</span></span> <span data-ttu-id="69b13-190">可用性區域會與地理區域中實際隔離的資料中心相對應。</span><span class="sxs-lookup"><span data-stu-id="69b13-190">An Availability Zone corresponds with a physically isolated datacenter in the geographic region.</span></span> <span data-ttu-id="69b13-191">Azure 提供許多功能，可讓應用程式在每個失敗階段都進行備援，包括**可用性設定組**、**可用性區域**和**配對區域**。</span><span class="sxs-lookup"><span data-stu-id="69b13-191">Azure has a number of features to make an application redundant at every level of failure, including **availability sets**, **availability zones**, and **paired regions**.</span></span> 

![](../resiliency/images/redundancy.svg)

<span data-ttu-id="69b13-192">下表摘要說明每個選項。</span><span class="sxs-lookup"><span data-stu-id="69b13-192">The following table summarizes each option.</span></span>

| &nbsp; | <span data-ttu-id="69b13-193">可用性設定組</span><span class="sxs-lookup"><span data-stu-id="69b13-193">Availability Set</span></span> | <span data-ttu-id="69b13-194">可用性區域</span><span class="sxs-lookup"><span data-stu-id="69b13-194">Availability Zone</span></span> | <span data-ttu-id="69b13-195">配對的區域</span><span class="sxs-lookup"><span data-stu-id="69b13-195">Paired region</span></span> |
|--------|------------------|-------------------|---------------|
| <span data-ttu-id="69b13-196">失敗原因</span><span class="sxs-lookup"><span data-stu-id="69b13-196">Scope of failure</span></span> | <span data-ttu-id="69b13-197">機架</span><span class="sxs-lookup"><span data-stu-id="69b13-197">Rack</span></span> | <span data-ttu-id="69b13-198">資料中心</span><span class="sxs-lookup"><span data-stu-id="69b13-198">Datacenter</span></span> | <span data-ttu-id="69b13-199">區域</span><span class="sxs-lookup"><span data-stu-id="69b13-199">Region</span></span> |
| <span data-ttu-id="69b13-200">要求路由</span><span class="sxs-lookup"><span data-stu-id="69b13-200">Request routing</span></span> | <span data-ttu-id="69b13-201">負載平衡器</span><span class="sxs-lookup"><span data-stu-id="69b13-201">Load Balancer</span></span> | <span data-ttu-id="69b13-202">跨區域負載平衡器</span><span class="sxs-lookup"><span data-stu-id="69b13-202">Cross-zone Load Balancer</span></span> | <span data-ttu-id="69b13-203">流量管理員</span><span class="sxs-lookup"><span data-stu-id="69b13-203">Traffic Manager</span></span> |
| <span data-ttu-id="69b13-204">網路延遲</span><span class="sxs-lookup"><span data-stu-id="69b13-204">Network latency</span></span> | <span data-ttu-id="69b13-205">非常低</span><span class="sxs-lookup"><span data-stu-id="69b13-205">Very low</span></span> | <span data-ttu-id="69b13-206">低</span><span class="sxs-lookup"><span data-stu-id="69b13-206">Low</span></span> | <span data-ttu-id="69b13-207">中到高</span><span class="sxs-lookup"><span data-stu-id="69b13-207">Mid to high</span></span> |
| <span data-ttu-id="69b13-208">虛擬網路</span><span class="sxs-lookup"><span data-stu-id="69b13-208">Virtual networking</span></span>  | <span data-ttu-id="69b13-209">VNet</span><span class="sxs-lookup"><span data-stu-id="69b13-209">VNet</span></span> | <span data-ttu-id="69b13-210">VNet</span><span class="sxs-lookup"><span data-stu-id="69b13-210">VNet</span></span> | <span data-ttu-id="69b13-211">跨區域 VNet 對等互連 (預覽)</span><span class="sxs-lookup"><span data-stu-id="69b13-211">Cross-region VNet peering (preview)</span></span> |

### <a name="availability-sets"></a><span data-ttu-id="69b13-212">可用性設定組</span><span class="sxs-lookup"><span data-stu-id="69b13-212">Availability sets</span></span> 

<span data-ttu-id="69b13-213">若要防範局部硬體失敗 (例如，磁碟或網路交換器失敗)，請在可用性設定組中部署兩個以上的 VM。</span><span class="sxs-lookup"><span data-stu-id="69b13-213">To protect against localized hardware failures, such as a disk or network switch failing, deploy two or more VMs in an availability set.</span></span> <span data-ttu-id="69b13-214">可用性設定組包含兩個以上的「容錯網域」，這些網域會共用電力來源和網路交換器。</span><span class="sxs-lookup"><span data-stu-id="69b13-214">An availability set consists of two or more *fault domains* that share a common power source and network switch.</span></span> <span data-ttu-id="69b13-215">可用性設定組中的 VM 會分散於這些容錯網域中，因此如果某個硬體失敗影響其中一個容錯網域，網路流量仍可路由傳送至其他容錯網域中的 VM。</span><span class="sxs-lookup"><span data-stu-id="69b13-215">VMs in an availability set are distributed across the fault domains, so if a hardware failure affects one fault domain, network traffic can still be routed the VMs in the other fault domains.</span></span> <span data-ttu-id="69b13-216">如需可用性設定組的詳細資訊，請參閱[管理 Azure 中 Windows 虛擬機器的可用性](/azure/virtual-machines/windows/manage-availability)。</span><span class="sxs-lookup"><span data-stu-id="69b13-216">For more information about Availability Sets, see [Manage the availability of Windows virtual machines in Azure](/azure/virtual-machines/windows/manage-availability).</span></span>

<span data-ttu-id="69b13-217">將 VM 執行個體新增至可用性設定組時，也會指派[更新網域](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-manage-availability/)給它們。</span><span class="sxs-lookup"><span data-stu-id="69b13-217">When VM instances are added to availability sets, they are also assigned an [update domain](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-manage-availability/).</span></span> <span data-ttu-id="69b13-218">更新網域是 VM 群組，會同時針對預定進行的維修作業事件而設定。</span><span class="sxs-lookup"><span data-stu-id="69b13-218">An update domain is a group of VMs that are set for planned maintenance events at the same time.</span></span> <span data-ttu-id="69b13-219">將 VM 分散到多個更新網域，以確保計劃的更新和修補事件在任何指定時間只會影響這些 VM 的子集。</span><span class="sxs-lookup"><span data-stu-id="69b13-219">Distributing VMs across multiple update domains ensures that planned update and patching events affect only a subset of these VMs at any given time.</span></span>

<span data-ttu-id="69b13-220">應該依您應用程式中執行個體的角色來組織可用性設定組，以確保每個角色的一個執行個體皆可運作。</span><span class="sxs-lookup"><span data-stu-id="69b13-220">Availability sets should be organized by the instance's role in your application to ensure one instance in each role is operational.</span></span> <span data-ttu-id="69b13-221">例如，在三層 Web 應用程式中，為前端、應用程式和資料層分別建立可用性設定組。</span><span class="sxs-lookup"><span data-stu-id="69b13-221">For example, in a three-tier web application, create separate availability sets for the front-end, application, and data tiers.</span></span>

<span data-ttu-id="69b13-222">![每個應用程式角色的 Azure 可用性設定組](./images/three-tier-example.png "每個應用程式角色的可用性設定組")</span><span class="sxs-lookup"><span data-stu-id="69b13-222">![Azure availability sets for each application role](./images/three-tier-example.png "Availability sets for each application role")</span></span>

### <a name="availability-zones-preview"></a><span data-ttu-id="69b13-223">可用性區域 (預覽)</span><span class="sxs-lookup"><span data-stu-id="69b13-223">Availability zones (preview)</span></span>

<span data-ttu-id="69b13-224">[可用性區域](/azure/availability-zones/az-overview)實際上是 Azure 地區內的個別區域。</span><span class="sxs-lookup"><span data-stu-id="69b13-224">An [Availability Zone](/azure/availability-zones/az-overview) is a physically separate zone within an Azure region.</span></span> <span data-ttu-id="69b13-225">每個可用性區域各有不同的電力來源、網路和冷卻系統。</span><span class="sxs-lookup"><span data-stu-id="69b13-225">Each Availability Zone has a distinct power source, network, and cooling.</span></span> <span data-ttu-id="69b13-226">跨可用性區域部署 VM 可協助應用程式防範全資料中心的失敗。</span><span class="sxs-lookup"><span data-stu-id="69b13-226">Deploying VMs across availability zones helps to protect an application against datacenter-wide failures.</span></span> 

### <a name="paired-regions"></a><span data-ttu-id="69b13-227">配對的區域</span><span class="sxs-lookup"><span data-stu-id="69b13-227">Paired regions</span></span>

<span data-ttu-id="69b13-228">若要協助應用程式防範區域性中斷，您可以將應用程式部署至多個區域，並使用 [Azure 流量管理員][traffic-manager]將網際網路流量分散到不同區域。</span><span class="sxs-lookup"><span data-stu-id="69b13-228">To protect an application against a regional outage, you can deploy the application across multiple regions, using [Azure Traffic Manager][traffic-manager] to distribute internet traffic to the different regions.</span></span> <span data-ttu-id="69b13-229">每個 Azure 區域都會與另一個區域配對。</span><span class="sxs-lookup"><span data-stu-id="69b13-229">Each Azure region is paired with another region.</span></span> <span data-ttu-id="69b13-230">這些區域集合在一起就構成了[區域性配對][paired-regions]。</span><span class="sxs-lookup"><span data-stu-id="69b13-230">Together, these form a [regional pair][paired-regions].</span></span> <span data-ttu-id="69b13-231">區域性配對會位於相同的地理位置內 (巴西南部除外)，以符合資料常駐地之稅務和執法管轄區的要求。</span><span class="sxs-lookup"><span data-stu-id="69b13-231">With the exception of Brazil South, regional pairs are located within the same geography in order to meet data residency requirements for tax and law enforcement jurisdiction purposes.</span></span>

<span data-ttu-id="69b13-232">不同於實體上分隔資料中心但可能會相對在附近地理區域的可用性區域，通常會以最少 300 英哩來分隔配對地區。</span><span class="sxs-lookup"><span data-stu-id="69b13-232">Unlike Availability Zones, which are physically separate datacenters but may be in relatively nearby geographic areas, paired regions are usually separated by at least 300 miles.</span></span> <span data-ttu-id="69b13-233">這是為了確保較大規模的災難只會影響配對中的其中一個地區。</span><span class="sxs-lookup"><span data-stu-id="69b13-233">This is intended to ensure larger scale disasters only impact one of the regions in the pair.</span></span> <span data-ttu-id="69b13-234">相鄰的配對可以設定為同步處理資料庫和儲存體服務資料，並加以設定以便平台更新一次只會發行至配對中的一個地區。</span><span class="sxs-lookup"><span data-stu-id="69b13-234">Neighboring pairs can be set to sync database and storage service data, and are configured so that platform updates are rolled out to only one region in the pair at a time.</span></span>

<span data-ttu-id="69b13-235">Azure [異地備援儲存體](https://azure.microsoft.com/documentation/articles/storage-redundancy/#geo-redundant-storage)會自動備份至適當的配對區域。</span><span class="sxs-lookup"><span data-stu-id="69b13-235">Azure [geo-redundant storage](https://azure.microsoft.com/documentation/articles/storage-redundancy/#geo-redundant-storage) is automatically backed up to the appropriate paired region.</span></span> <span data-ttu-id="69b13-236">如需其他所有資源，請使用配對區域建立完整備援的解決方案，即表示在兩個區域中建立您解決方案的完整副本。</span><span class="sxs-lookup"><span data-stu-id="69b13-236">For all other resources, creating a fully redundant solution using paired regions means creating a full copy of your solution in both regions.</span></span>


### <a name="see-also"></a><span data-ttu-id="69b13-237">另請參閱</span><span class="sxs-lookup"><span data-stu-id="69b13-237">See also</span></span>

-   [<span data-ttu-id="69b13-238">Azure 中虛擬機器的區域和可用性</span><span class="sxs-lookup"><span data-stu-id="69b13-238">Regions and availability for virtual machines in Azure</span></span>](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-regions-and-availability/)

-   [<span data-ttu-id="69b13-239">Azure 應用程式的高可用性</span><span class="sxs-lookup"><span data-stu-id="69b13-239">High availability for Azure applications</span></span>](../resiliency/high-availability-azure-applications.md)

-   [<span data-ttu-id="69b13-240">Azure 應用程式的災害復原</span><span class="sxs-lookup"><span data-stu-id="69b13-240">Disaster recovery for Azure applications</span></span>](../resiliency/disaster-recovery-azure-applications.md)

-   [<span data-ttu-id="69b13-241">Azure 中 Linux 虛擬機器預定進行的維修</span><span class="sxs-lookup"><span data-stu-id="69b13-241">Planned maintenance for Linux virtual machines in Azure</span></span>](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-planned-maintenance/)

## <a name="services"></a><span data-ttu-id="69b13-242">服務</span><span class="sxs-lookup"><span data-stu-id="69b13-242">Services</span></span>

<span data-ttu-id="69b13-243">如需所有服務在平台之間對應方式的完整清單，請參閱[完成 AWS 和 Azure 服務比較矩陣](https://aka.ms/azure4aws-services)。</span><span class="sxs-lookup"><span data-stu-id="69b13-243">Consult the [complete AWS and Azure service comparison matrix](https://aka.ms/azure4aws-services) for a full listing of how all services map between platforms.</span></span>

<span data-ttu-id="69b13-244">並非所有區域中都可使用所有的 Azure 產品和服務。</span><span class="sxs-lookup"><span data-stu-id="69b13-244">Not all Azure products and services are available in all regions.</span></span> <span data-ttu-id="69b13-245">如需詳細資料，請參閱[依區域的產品](https://azure.microsoft.com/regions/services/)頁面。</span><span class="sxs-lookup"><span data-stu-id="69b13-245">Consult the [Products by Region](https://azure.microsoft.com/regions/services/) page for details.</span></span> <span data-ttu-id="69b13-246">您可以在[服務等級協定](https://azure.microsoft.com/support/legal/sla/)頁面上找到每個 Azure 產品或服務的執行時間保證和停機時間信用原則。</span><span class="sxs-lookup"><span data-stu-id="69b13-246">You can find the uptime guarantees and downtime credit policies for each Azure product or service on the [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/) page.</span></span>

<span data-ttu-id="69b13-247">下列章節會提供 AWS 和 Azure 平台之間的常用功能和服務之差異的簡短說明。</span><span class="sxs-lookup"><span data-stu-id="69b13-247">The following sections provide a brief explanation of how commonly used features and services differ between the AWS and Azure platforms.</span></span>

### <a name="compute-services"></a><span data-ttu-id="69b13-248">計算服務</span><span class="sxs-lookup"><span data-stu-id="69b13-248">Compute services</span></span>

#### <a name="ec2-instances-and-azure-virtual-machines"></a><span data-ttu-id="69b13-249">EC2 執行個體和 Azure 虛擬機器</span><span class="sxs-lookup"><span data-stu-id="69b13-249">EC2 Instances and Azure virtual machines</span></span>

<span data-ttu-id="69b13-250">雖然 AWS 執行個體類型和 Azure 虛擬機器大小是以類似的方式分解，但 RAM、CPU 和儲存體功能中會有所差異。</span><span class="sxs-lookup"><span data-stu-id="69b13-250">Although AWS instance types and Azure virtual machine sizes breakdown in a similar way, there are differences in the RAM, CPU, and storage capabilities.</span></span>

-   [<span data-ttu-id="69b13-251">Amazon EC2 執行個體類型</span><span class="sxs-lookup"><span data-stu-id="69b13-251">Amazon EC2 Instance Types</span></span>](https://aws.amazon.com/ec2/instance-types/)

-   [<span data-ttu-id="69b13-252">Azure 中的虛擬機器大小 (Windows)</span><span class="sxs-lookup"><span data-stu-id="69b13-252">Sizes for virtual machines in Azure (Windows)</span></span>](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/)

-   [<span data-ttu-id="69b13-253">Azure 中的虛擬機器大小 (Linux)</span><span class="sxs-lookup"><span data-stu-id="69b13-253">Sizes for virtual machines in Azure (Linux)</span></span>](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/)

<span data-ttu-id="69b13-254">不同於 AWS 的每個第二個計費，Azure 隨選 VM 會依分鐘計費。</span><span class="sxs-lookup"><span data-stu-id="69b13-254">Unlike AWS' per second billing, Azure on-demand VMs are billed by the minute.</span></span>

<span data-ttu-id="69b13-255">Azure 與 EC2 Spot 執行個體或專用主機並沒有對等項目。</span><span class="sxs-lookup"><span data-stu-id="69b13-255">Azure has no equivalent to EC2 Spot Instances or Dedicated Hosts.</span></span>

#### <a name="ebs-and-azure-storage-for-vm-disks"></a><span data-ttu-id="69b13-256">適用於 VM 磁碟的 EBS 和 Azure 儲存體</span><span class="sxs-lookup"><span data-stu-id="69b13-256">EBS and Azure Storage for VM disks</span></span>

<span data-ttu-id="69b13-257">Azure VM 的長期資料儲存區是由位在 blob 儲存體中的[資料磁碟](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-about-disks-vhds/)所提供。</span><span class="sxs-lookup"><span data-stu-id="69b13-257">Durable data storage for Azure VMs is provided by [data disks](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-about-disks-vhds/) residing in blob storage.</span></span> <span data-ttu-id="69b13-258">這類似於 EC2 執行個體在 Elastic Block Store (EBS) 上儲存磁碟區的方式。</span><span class="sxs-lookup"><span data-stu-id="69b13-258">This is similar to how EC2 instances store disk volumes on Elastic Block Store (EBS).</span></span> <span data-ttu-id="69b13-259">[Azure 暫存儲存體](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)也會提供 VM 與 EC2 執行個體儲存體 (也稱為暫時儲存體) 相同的低延遲暫存讀寫儲存體。</span><span class="sxs-lookup"><span data-stu-id="69b13-259">[Azure temporary storage](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/) also provides VMs the same low-latency temporary read-write storage as EC2 Instance Storage (also called ephemeral storage).</span></span>

<span data-ttu-id="69b13-260">會使用 [Azure 高階儲存體](https://docs.microsoft.com/azure/storage/storage-premium-storage)支援較高的效能磁碟 IO。</span><span class="sxs-lookup"><span data-stu-id="69b13-260">Higher performance disk IO is supported using [Azure premium storage](https://docs.microsoft.com/azure/storage/storage-premium-storage).</span></span>
<span data-ttu-id="69b13-261">這是類似於 AWS 所提供之佈建的 IOPS 儲存體選項。</span><span class="sxs-lookup"><span data-stu-id="69b13-261">This is similar to the Provisioned IOPS storage options provided by AWS.</span></span>

#### <a name="lambda-azure-functions-azure-web-jobs-and-azure-logic-apps"></a><span data-ttu-id="69b13-262">Lambda、Azure Functions、Azure Web 作業以及 Azure Logic Apps</span><span class="sxs-lookup"><span data-stu-id="69b13-262">Lambda, Azure Functions, Azure Web-Jobs, and Azure Logic Apps</span></span>

<span data-ttu-id="69b13-263">[Azure Functions](https://azure.microsoft.com/services/functions/) 在提供無伺服器、隨選程式碼方面是 AWS Lambda 的主要對等項目。</span><span class="sxs-lookup"><span data-stu-id="69b13-263">[Azure Functions](https://azure.microsoft.com/services/functions/) is the primary equivalent of AWS Lambda in providing serverless, on-demand code.</span></span>
<span data-ttu-id="69b13-264">不過，Lambda 功能也會與其他 Azure 服務重疊：</span><span class="sxs-lookup"><span data-stu-id="69b13-264">However, Lambda functionality also overlaps with other Azure services:</span></span>

-   <span data-ttu-id="69b13-265">[WebJobs](https://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/) - 可讓您建立排程或連續執行的背景工作。</span><span class="sxs-lookup"><span data-stu-id="69b13-265">[WebJobs](https://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/) - allow you to create scheduled or continuously running background tasks.</span></span>

-   <span data-ttu-id="69b13-266">[Logic Apps](https://azure.microsoft.com/services/logic-apps/) - 提供通訊、整合及商務規則管理服務。</span><span class="sxs-lookup"><span data-stu-id="69b13-266">[Logic Apps](https://azure.microsoft.com/services/logic-apps/) - provides communications, integration, and business rule management services.</span></span>

#### <a name="autoscaling-azure-vm-scaling-and-azure-app-service-autoscale"></a><span data-ttu-id="69b13-267">自動調整、Azure VM 縮放比例和 Azure App Service 自動調整</span><span class="sxs-lookup"><span data-stu-id="69b13-267">Autoscaling, Azure VM scaling, and Azure App Service Autoscale</span></span>

<span data-ttu-id="69b13-268">Azure 中的自動調整是由兩個服務處理：</span><span class="sxs-lookup"><span data-stu-id="69b13-268">Autoscaling in Azure is handled by two services:</span></span>

-   <span data-ttu-id="69b13-269">[VM 擴展集](https://azure.microsoft.com/documentation/articles/virtual-machine-scale-sets-overview/) - 可讓您部署及管理一組完全相同的 VM。</span><span class="sxs-lookup"><span data-stu-id="69b13-269">[VM scale sets](https://azure.microsoft.com/documentation/articles/virtual-machine-scale-sets-overview/) - allow you to deploy and manage an identical set of VMs.</span></span> <span data-ttu-id="69b13-270">執行個體數目可以效能需求作為基礎來自動調整。</span><span class="sxs-lookup"><span data-stu-id="69b13-270">The number of instances can autoscale based on performance needs.</span></span>

-   <span data-ttu-id="69b13-271">[App Service 自動調整](https://azure.microsoft.com/documentation/articles/web-sites-scale/) - 提供自動調整 Azure App Service 解決方案的功能。</span><span class="sxs-lookup"><span data-stu-id="69b13-271">[App Service Autoscale](https://azure.microsoft.com/documentation/articles/web-sites-scale/) - provides the capability to autoscale Azure App Service solutions.</span></span>


#### <a name="container-service"></a><span data-ttu-id="69b13-272">容器服務</span><span class="sxs-lookup"><span data-stu-id="69b13-272">Container Service</span></span>
<span data-ttu-id="69b13-273">[Azure Container Service](https://docs.microsoft.com/azure/container-service/container-service-intro) 支援透過 Docker Swarm、Kubernetes 或 DC/OS 管理的 Docker 容器。</span><span class="sxs-lookup"><span data-stu-id="69b13-273">The [Azure Container Service](https://docs.microsoft.com/azure/container-service/container-service-intro) supports Docker containers managed through Docker Swarm, Kubernetes, or DC/OS.</span></span>

#### <a name="other-compute-services"></a><span data-ttu-id="69b13-274">其他計算服務</span><span class="sxs-lookup"><span data-stu-id="69b13-274">Other compute services</span></span> 


<span data-ttu-id="69b13-275">Azure 會提供數個計算服務，在 AWS 中沒有直接的對等項目：</span><span class="sxs-lookup"><span data-stu-id="69b13-275">Azure offers several compute services that do not have direct equivalents in AWS:</span></span>

-   <span data-ttu-id="69b13-276">[Azure Batch](https://azure.microsoft.com/documentation/articles/batch-technical-overview/) - 可讓您在虛擬機器的擴充集合之間管理大量的計算工作。</span><span class="sxs-lookup"><span data-stu-id="69b13-276">[Azure Batch](https://azure.microsoft.com/documentation/articles/batch-technical-overview/) - allows you to manage compute-intensive work across a scalable collection of virtual machines.</span></span>

-   <span data-ttu-id="69b13-277">[Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-overview/) - 開發及主控可擴充的[微服務](https://azure.microsoft.com/documentation/articles/service-fabric-overview-microservices/)解決方案平台。</span><span class="sxs-lookup"><span data-stu-id="69b13-277">[Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-overview/) - platform for developing and hosting scalable [microservice](https://azure.microsoft.com/documentation/articles/service-fabric-overview-microservices/) solutions.</span></span>

#### <a name="see-also"></a><span data-ttu-id="69b13-278">另請參閱</span><span class="sxs-lookup"><span data-stu-id="69b13-278">See also</span></span>

-   [<span data-ttu-id="69b13-279">使用入口網站在 Azure 上建立 Linux VM</span><span class="sxs-lookup"><span data-stu-id="69b13-279">Create a Linux VM on Azure using the Portal</span></span>](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-quick-create-portal/)

-   [<span data-ttu-id="69b13-280">Azure 參考架構：在 Azure 上執行 Linux VM</span><span class="sxs-lookup"><span data-stu-id="69b13-280">Azure Reference Architecture: Running a Linux VM on Azure</span></span>](https://azure.microsoft.com/documentation/articles/guidance-compute-single-vm-linux/)

-   [<span data-ttu-id="69b13-281">在 Azure App Service 中開始使用 Node.js Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="69b13-281">Get started with Node.js web apps in Azure App Service</span></span>](https://azure.microsoft.com/documentation/articles/app-service-web-nodejs-get-started/)

-   [<span data-ttu-id="69b13-282">Azure 參考架構︰基本 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="69b13-282">Azure Reference Architecture: Basic web application</span></span>](https://azure.microsoft.com/documentation/articles/guidance-web-apps-basic/)

-   [<span data-ttu-id="69b13-283">建立您的第一個 Azure 函式</span><span class="sxs-lookup"><span data-stu-id="69b13-283">Create your first Azure Function</span></span>](https://azure.microsoft.com/documentation/articles/functions-create-first-azure-function/)

### <a name="storage"></a><span data-ttu-id="69b13-284">儲存體</span><span class="sxs-lookup"><span data-stu-id="69b13-284">Storage</span></span>

#### <a name="s3ebsefs-and-azure-storage"></a><span data-ttu-id="69b13-285">S3/EBS/EFS 與 Azure 儲存體</span><span class="sxs-lookup"><span data-stu-id="69b13-285">S3/EBS/EFS and Azure Storage</span></span>

<span data-ttu-id="69b13-286">在 AWS 平台中，雲端儲存體主要分為三種服務：</span><span class="sxs-lookup"><span data-stu-id="69b13-286">In the AWS platform, cloud storage is primarily broken down into three services:</span></span>

-   <span data-ttu-id="69b13-287">**Simple Storage Service (S3)** - 基本物件儲存體。</span><span class="sxs-lookup"><span data-stu-id="69b13-287">**Simple Storage Service (S3)** - basic object storage.</span></span> <span data-ttu-id="69b13-288">讓資料可透過網際網路存取的 API 使用。</span><span class="sxs-lookup"><span data-stu-id="69b13-288">Makes data available through an Internet accessible API.</span></span>

-   <span data-ttu-id="69b13-289">**Elastic Block Storage (EBS)** - 區塊層級儲存體，可供單一 VM 存取。</span><span class="sxs-lookup"><span data-stu-id="69b13-289">**Elastic Block Storage (EBS)** - block level storage, intended for access by a single VM.</span></span>

-   <span data-ttu-id="69b13-290">**Elastic File System (EFS)** - 適用於最多上千個 EC2 執行個體作為共用存放裝置使用的檔案儲存體。</span><span class="sxs-lookup"><span data-stu-id="69b13-290">**Elastic File System (EFS)** - file storage meant for use as shared storage for up to thousands of EC2 instances.</span></span>

<span data-ttu-id="69b13-291">在 Azure 儲存體中，訂用帳戶繫結的[儲存體帳戶](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/)可讓您建立及管理下列儲存體服務：</span><span class="sxs-lookup"><span data-stu-id="69b13-291">In Azure Storage, subscription-bound [storage accounts](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/) allow you to create and manage the following storage services:</span></span>

-   <span data-ttu-id="69b13-292">[Blob 儲存體](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/) - 儲存任何類型的文字或二進位資料，例如文件、媒體檔案或應用程式安裝程式。</span><span class="sxs-lookup"><span data-stu-id="69b13-292">[Blob storage](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/) - stores any type of text or binary data, such as a document, media file, or application installer.</span></span> <span data-ttu-id="69b13-293">您可以設定私人存取的 Blob 儲存體或公開共用內容到網際網路。</span><span class="sxs-lookup"><span data-stu-id="69b13-293">You can set Blob storage for private access or share contents publicly to the Internet.</span></span> <span data-ttu-id="69b13-294">Blob 儲存體與 AWS S3 和 EBS 有相同的用途。</span><span class="sxs-lookup"><span data-stu-id="69b13-294">Blob storage serves the same purpose as both AWS S3 and EBS.</span></span>
-   <span data-ttu-id="69b13-295">[表格儲存體](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-table-storage/) - 可儲存結構化資料集。</span><span class="sxs-lookup"><span data-stu-id="69b13-295">[Table storage](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-table-storage/) - stores structured datasets.</span></span> <span data-ttu-id="69b13-296">表格儲存體屬於 NoSQL 索引鍵屬性資料儲存，可允許快速開發和迅速存取大量資料。</span><span class="sxs-lookup"><span data-stu-id="69b13-296">Table storage is a NoSQL key-attribute data store that allows for rapid development and fast access to large quantities of data.</span></span> <span data-ttu-id="69b13-297">類似於 AWS 的 SimpleDB 和 DynamoDB 服務。</span><span class="sxs-lookup"><span data-stu-id="69b13-297">Similar to AWS' SimpleDB and DynamoDB services.</span></span>

-   <span data-ttu-id="69b13-298">[佇列儲存體](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-queues/) - 可為工作流程處理及雲端服務元件間的通訊，提供訊息服務。</span><span class="sxs-lookup"><span data-stu-id="69b13-298">[Queue storage](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-queues/) - provides messaging for workflow processing and for communication between components of cloud services.</span></span>

-   <span data-ttu-id="69b13-299">[檔案儲存體](https://azure.microsoft.com/documentation/articles/storage-java-how-to-use-file-storage/) - 標準使用伺服器訊息區 (SMB) 通訊協定為繼承應用程式提供共用儲存體。</span><span class="sxs-lookup"><span data-stu-id="69b13-299">[File storage](https://azure.microsoft.com/documentation/articles/storage-java-how-to-use-file-storage/) - offers shared storage for legacy applications using the standard server message block (SMB) protocol.</span></span> <span data-ttu-id="69b13-300">檔案儲存體會以類似的方式在 AWS 平台用於 EFS。</span><span class="sxs-lookup"><span data-stu-id="69b13-300">File storage is used in a similar manner to EFS in the AWS platform.</span></span>
 
#### <a name="glacier-and-azure-storage"></a><span data-ttu-id="69b13-301">Glacier 與 Azure 儲存體</span><span class="sxs-lookup"><span data-stu-id="69b13-301">Glacier and Azure Storage</span></span> 

<span data-ttu-id="69b13-302">[Azure 封存 Blob 儲存體](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier)相當於 AWS Glacier 儲存體服務。</span><span class="sxs-lookup"><span data-stu-id="69b13-302">[Azure Archive Blob Storage](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier) is comparable to AWS Glacier storage service.</span></span> <span data-ttu-id="69b13-303">它適用於不太會存取的資料，且該資料已儲存至少 180 天且可以容忍數小時的擷取延遲。</span><span class="sxs-lookup"><span data-stu-id="69b13-303">It is intended for rarely accessed data that is stored for at least 180 days and can tolerate several hours of retrieval latency.</span></span> 

<span data-ttu-id="69b13-304">針對不常存取、卻又必須可供立即存取的資料，[Azure 非經常性 Blob 儲存層](/azure/storage/blobs/storage-blob-storage-tiers#cool-access-tier)可提供比標準 Blob 儲存體還要便宜的儲存體。</span><span class="sxs-lookup"><span data-stu-id="69b13-304">For data that is infrequently accessed but must be available immediately when accessed, [Azure Cool Blob Storage tier](/azure/storage/blobs/storage-blob-storage-tiers#cool-access-tier) provides cheaper storage than standard blob storage.</span></span> <span data-ttu-id="69b13-305">此儲存層相當於 AWS S3 - 不常存取儲存體服務。</span><span class="sxs-lookup"><span data-stu-id="69b13-305">This storage tier is comparable to AWS S3 - Infrequent Access storage service.</span></span>

#### <a name="see-also"></a><span data-ttu-id="69b13-306">另請參閱</span><span class="sxs-lookup"><span data-stu-id="69b13-306">See also</span></span>

-   [<span data-ttu-id="69b13-307">Microsoft Azure 儲存體效能與延展性檢查清單</span><span class="sxs-lookup"><span data-stu-id="69b13-307">Microsoft Azure Storage Performance and Scalability Checklist</span></span>](https://azure.microsoft.com/documentation/articles/storage-performance-checklist/)

-   [<span data-ttu-id="69b13-308">Azure 儲存體安全性指南</span><span class="sxs-lookup"><span data-stu-id="69b13-308">Azure Storage security guide</span></span>](https://azure.microsoft.com/documentation/articles/storage-security-guide/)

-   [<span data-ttu-id="69b13-309">模式與做法：內容傳遞網路 (CDN) 指引</span><span class="sxs-lookup"><span data-stu-id="69b13-309">Patterns & Practices: Content Delivery Network (CDN) guidance</span></span>](https://azure.microsoft.com/documentation/articles/best-practices-cdn/)

### <a name="networking"></a><span data-ttu-id="69b13-310">網路</span><span class="sxs-lookup"><span data-stu-id="69b13-310">Networking</span></span>

#### <a name="elastic-load-balancing-azure-load-balancer-and-azure-application-gateway"></a><span data-ttu-id="69b13-311">彈性負載平衡、Azure Load Balancer 以及 Azure 應用程式閘道</span><span class="sxs-lookup"><span data-stu-id="69b13-311">Elastic Load Balancing, Azure Load Balancer, and Azure Application Gateway</span></span>

<span data-ttu-id="69b13-312">這兩項彈性負載平衡服務的 Azure 對等項目為：</span><span class="sxs-lookup"><span data-stu-id="69b13-312">The Azure equivalents of the two Elastic Load Balancing services are:</span></span>

-   <span data-ttu-id="69b13-313">[負載平衡器](https://azure.microsoft.com/documentation/articles/load-balancer-overview/) - 提供與 AWS 傳統 Load Balancer 相同的功能，可讓您在網路層級散發多個 VM 的流量。</span><span class="sxs-lookup"><span data-stu-id="69b13-313">[Load Balancer](https://azure.microsoft.com/documentation/articles/load-balancer-overview/) - provides the same capabilities as the AWS Classic Load Balancer, allowing you to distribute traffic for multiple VMs at the network level.</span></span> <span data-ttu-id="69b13-314">它也提供容錯移轉功能。</span><span class="sxs-lookup"><span data-stu-id="69b13-314">It also provides failover capability.</span></span>

-   <span data-ttu-id="69b13-315">[應用程式閘道](https://azure.microsoft.com/documentation/articles/application-gateway-introduction/) - 提供可比較 AWS 應用程式 Load Balancer 的應用程式層級規則型路由。</span><span class="sxs-lookup"><span data-stu-id="69b13-315">[Application Gateway](https://azure.microsoft.com/documentation/articles/application-gateway-introduction/) - offers application-level rule-based routing comparable to the AWS Application Load Balancer.</span></span>

#### <a name="route-53-azure-dns-and-azure-traffic-manager"></a><span data-ttu-id="69b13-316">Route 53、Azure DNS 和 Azure 流量管理員</span><span class="sxs-lookup"><span data-stu-id="69b13-316">Route 53, Azure DNS, and Azure Traffic Manager</span></span>

<span data-ttu-id="69b13-317">在 AWS 中，Route 53 會提供 DNS 名稱管理和 DNS 層級流量路由與容錯移轉服務。</span><span class="sxs-lookup"><span data-stu-id="69b13-317">In AWS, Route 53 provides both DNS name management and DNS-level traffic routing and failover services.</span></span> <span data-ttu-id="69b13-318">在 Azure 中，這會透過兩個服務進行處理：</span><span class="sxs-lookup"><span data-stu-id="69b13-318">In Azure this is handled through two services:</span></span>

-   <span data-ttu-id="69b13-319">[Azure DNS](https://azure.microsoft.com/documentation/services/dns/) 提供網域和 DNS 管理。</span><span class="sxs-lookup"><span data-stu-id="69b13-319">[Azure DNS](https://azure.microsoft.com/documentation/services/dns/) provides domain and DNS management.</span></span>

-   <span data-ttu-id="69b13-320">[流量管理員][traffic-manager]提供 DNS 層級流量路由、負載平衡和容錯移轉功能。</span><span class="sxs-lookup"><span data-stu-id="69b13-320">[Traffic Manager][traffic-manager] provides DNS level traffic routing, load balancing, and failover capabilities.</span></span>

#### <a name="direct-connect-and-azure-expressroute"></a><span data-ttu-id="69b13-321">Direct Connect 與 Azure ExpressRoute</span><span class="sxs-lookup"><span data-stu-id="69b13-321">Direct Connect and Azure ExpressRoute</span></span>

<span data-ttu-id="69b13-322">Azure 會透過其 [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) 服務提供類似的站台對站台專用連線。</span><span class="sxs-lookup"><span data-stu-id="69b13-322">Azure provides similar site-to-site dedicated connections through its [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) service.</span></span> <span data-ttu-id="69b13-323">ExpressRoute 可讓您使用專用私人網路連線，將本機網路直接連線到 Azure 資源。</span><span class="sxs-lookup"><span data-stu-id="69b13-323">ExpressRoute allows you to connect your local network directly to Azure resources using a dedicated private network connection.</span></span> <span data-ttu-id="69b13-324">Azure 還以較低的成本提供更多傳統[站對站 VPN 連線](https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-site-to-site-resource-manager-portal/)。</span><span class="sxs-lookup"><span data-stu-id="69b13-324">Azure also offers more conventional [site-to-site VPN connections](https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-site-to-site-resource-manager-portal/) at a lower cost.</span></span>

#### <a name="see-also"></a><span data-ttu-id="69b13-325">另請參閱</span><span class="sxs-lookup"><span data-stu-id="69b13-325">See also</span></span>

-   [<span data-ttu-id="69b13-326">使用 Azure 入口網站建立虛擬網路</span><span class="sxs-lookup"><span data-stu-id="69b13-326">Create a virtual network using the Azure portal</span></span>](https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/)

-   [<span data-ttu-id="69b13-327">規劃和設計 Azure 虛擬網路</span><span class="sxs-lookup"><span data-stu-id="69b13-327">Plan and design Azure Virtual Networks</span></span>](https://azure.microsoft.com/documentation/articles/virtual-network-vnet-plan-design-arm/)

-   [<span data-ttu-id="69b13-328">Azure 網路安全性最佳做法</span><span class="sxs-lookup"><span data-stu-id="69b13-328">Azure Network Security Best Practices</span></span>](https://azure.microsoft.com/documentation/articles/azure-security-network-security-best-practices/)

### <a name="database-services"></a><span data-ttu-id="69b13-329">資料庫服務</span><span class="sxs-lookup"><span data-stu-id="69b13-329">Database services</span></span>

#### <a name="rds-and-azure-relational-database-services"></a><span data-ttu-id="69b13-330">RDS 和 Azure 關聯式資料庫服務</span><span class="sxs-lookup"><span data-stu-id="69b13-330">RDS and Azure relational database services</span></span>

<span data-ttu-id="69b13-331">Azure 會提供與 AWS 關聯式資料庫服務 (RDS) 對等的幾種不同關聯式資料庫服務。</span><span class="sxs-lookup"><span data-stu-id="69b13-331">Azure provides several different relational database services that are the equivalent of AWS' Relational Database Service (RDS).</span></span>

-   [<span data-ttu-id="69b13-332">SQL Database</span><span class="sxs-lookup"><span data-stu-id="69b13-332">SQL Database</span></span>](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)
-   [<span data-ttu-id="69b13-333">適用於 MySQL 的 Azure 資料庫</span><span class="sxs-lookup"><span data-stu-id="69b13-333">Azure Database for MySQL</span></span>](https://docs.microsoft.com/azure/mysql/overview)
-   [<span data-ttu-id="69b13-334">適用於 PostgreSQL 的 Azure 資料庫</span><span class="sxs-lookup"><span data-stu-id="69b13-334">Azure Database for PostgreSQL</span></span>](https://docs.microsoft.com/azure/postgresql/overview)

<span data-ttu-id="69b13-335">可以使用 Azure VM 執行個體部署諸如 [SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)、[Oracle](https://azure.microsoft.com/campaigns/oracle/) 和 [MySQL](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-classic-mysql-2008r2/) 等其他資料庫引擎。</span><span class="sxs-lookup"><span data-stu-id="69b13-335">Other database engines such as [SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/), [Oracle](https://azure.microsoft.com/campaigns/oracle/), and [MySQL](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-classic-mysql-2008r2/) can be deployed using Azure VM Instances.</span></span>

<span data-ttu-id="69b13-336">AWS RDS 的成本取決於您執行個體所使用的硬體資源，例如 CPU、RAM、儲存體和網路頻寬。</span><span class="sxs-lookup"><span data-stu-id="69b13-336">Costs for AWS RDS are determined by the amount of hardware resources that your instance uses, like CPU, RAM, storage, and network bandwidth.</span></span> <span data-ttu-id="69b13-337">在 Azure 資料庫服務中，成本取決於您的資料庫大小、並行連線及輸送量層級。</span><span class="sxs-lookup"><span data-stu-id="69b13-337">In the Azure database services, cost depends on your database size, concurrent connections, and throughput levels.</span></span>

#### <a name="see-also"></a><span data-ttu-id="69b13-338">另請參閱</span><span class="sxs-lookup"><span data-stu-id="69b13-338">See also</span></span>

-   [<span data-ttu-id="69b13-339">Azure SQL Database 教學課程</span><span class="sxs-lookup"><span data-stu-id="69b13-339">Azure SQL Database Tutorials</span></span>](https://azure.microsoft.com/documentation/articles/sql-database-explore-tutorials/)

-   [<span data-ttu-id="69b13-340">使用 Azure 入口網站為 Azure SQL Database 設定異地複寫</span><span class="sxs-lookup"><span data-stu-id="69b13-340">Configure geo-replication for Azure SQL Database with the Azure portal</span></span>](https://azure.microsoft.com/documentation/articles/sql-database-geo-replication-portal/)

-   [<span data-ttu-id="69b13-341">Cosmos DB 簡介：NoSQL JSON 資料庫</span><span class="sxs-lookup"><span data-stu-id="69b13-341">Introduction to Cosmos DB: A NoSQL JSON Database</span></span>](/azure/cosmos-db/sql-api-introduction)

-   [<span data-ttu-id="69b13-342">如何從 Node.js 使用 Azure 資料表儲存體</span><span class="sxs-lookup"><span data-stu-id="69b13-342">How to use Azure Table storage from Node.js</span></span>](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-table-storage/)

### <a name="security-and-identity"></a><span data-ttu-id="69b13-343">安全性與身分識別</span><span class="sxs-lookup"><span data-stu-id="69b13-343">Security and identity</span></span>

#### <a name="directory-service-and-azure-active-directory"></a><span data-ttu-id="69b13-344">目錄服務和 Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="69b13-344">Directory service and Azure Active Directory</span></span>

<span data-ttu-id="69b13-345">Azure 會將目錄服務分割成下列供應項目：</span><span class="sxs-lookup"><span data-stu-id="69b13-345">Azure splits up directory services into the following offerings:</span></span>

-   <span data-ttu-id="69b13-346">[Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-whatis/) - 以雲端作為基礎的目錄和身分識別管理服務。</span><span class="sxs-lookup"><span data-stu-id="69b13-346">[Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-whatis/) - cloud based directory and identity management service.</span></span>

-   <span data-ttu-id="69b13-347">[Azure Active Directory B2B](https://azure.microsoft.com/documentation/articles/active-directory-b2b-collaboration-overview/) - 允許以合作夥伴管理的身分識別來存取您的公司應用程式。</span><span class="sxs-lookup"><span data-stu-id="69b13-347">[Azure Active Directory B2B](https://azure.microsoft.com/documentation/articles/active-directory-b2b-collaboration-overview/) - enables access to your corporate applications from partner-managed identities.</span></span>

-   <span data-ttu-id="69b13-348">[Azure Active Directory B2C](https://azure.microsoft.com/documentation/articles/active-directory-b2c-overview/) - 支援取用者面相應用程式的單一登入和使用者管理的服務供應項目。</span><span class="sxs-lookup"><span data-stu-id="69b13-348">[Azure Active Directory B2C](https://azure.microsoft.com/documentation/articles/active-directory-b2c-overview/) - service offering support for single sign-on and user management for consumer facing applications.</span></span>

-   <span data-ttu-id="69b13-349">[Azure Active Directory Domain Services](https://azure.microsoft.com/documentation/articles/active-directory-ds-overview/) - 主控網域控制站服務，可允許 Active Directory 相容網域加入和使用者管理功能。</span><span class="sxs-lookup"><span data-stu-id="69b13-349">[Azure Active Directory Domain Services](https://azure.microsoft.com/documentation/articles/active-directory-ds-overview/) - hosted domain controller service, allowing Active Directory compatible domain join and user management functionality.</span></span>

#### <a name="web-application-firewall"></a><span data-ttu-id="69b13-350">Web 應用程式防火牆</span><span class="sxs-lookup"><span data-stu-id="69b13-350">Web application firewall</span></span>

<span data-ttu-id="69b13-351">除了[應用程式閘道 Web 應用程式防火牆](https://azure.microsoft.com/documentation/articles/application-gateway-webapplicationfirewall-overview/)之外，您也可以[使用第三方廠商提供的 web 應用程式防火牆](https://azure.microsoft.com/documentation/articles/application-gateway-webapplicationfirewall-overview/)，像是 [Barracuda Networks](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf/)。</span><span class="sxs-lookup"><span data-stu-id="69b13-351">In addition to the [Application Gateway Web Application Firewall](https://azure.microsoft.com/documentation/articles/application-gateway-webapplicationfirewall-overview/), you can also [use web application firewalls](https://azure.microsoft.com/documentation/articles/application-gateway-webapplicationfirewall-overview/) from third-party vendors like [Barracuda Networks](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf/).</span></span>

#### <a name="see-also"></a><span data-ttu-id="69b13-352">另請參閱</span><span class="sxs-lookup"><span data-stu-id="69b13-352">See also</span></span>

-   [<span data-ttu-id="69b13-353">開始使用 Microsoft Azure 安全性</span><span class="sxs-lookup"><span data-stu-id="69b13-353">Getting started with Microsoft Azure security</span></span>](https://azure.microsoft.com/documentation/articles/azure-security-getting-started/)

-   [<span data-ttu-id="69b13-354">Azure 身分識別管理和存取控制安全性最佳作法</span><span class="sxs-lookup"><span data-stu-id="69b13-354">Azure Identity Management and access control security best practices</span></span>](https://azure.microsoft.com/documentation/articles/azure-security-identity-management-best-practices/)

### <a name="application-and-messaging-services"></a><span data-ttu-id="69b13-355">應用程式及傳訊服務</span><span class="sxs-lookup"><span data-stu-id="69b13-355">Application and messaging services</span></span>

#### <a name="simple-email-service"></a><span data-ttu-id="69b13-356">Simple Email Service</span><span class="sxs-lookup"><span data-stu-id="69b13-356">Simple Email Service</span></span>

<span data-ttu-id="69b13-357">AWS 提供 Simple Email Service (SES) 來傳送通知、交易式或行銷電子郵件。</span><span class="sxs-lookup"><span data-stu-id="69b13-357">AWS provides the Simple Email Service (SES) for sending notification, transactional, or marketing emails.</span></span> <span data-ttu-id="69b13-358">在 Azure 中，第三方解決方案 (例如 [Sendgrid](https://sendgrid.com/partners/azure/)) 會提供電子郵件服務。</span><span class="sxs-lookup"><span data-stu-id="69b13-358">In Azure, third-party solutions like [Sendgrid](https://sendgrid.com/partners/azure/) provide email services.</span></span>

#### <a name="simple-queueing-service"></a><span data-ttu-id="69b13-359">Simple Queueing Service</span><span class="sxs-lookup"><span data-stu-id="69b13-359">Simple Queueing Service</span></span>

<span data-ttu-id="69b13-360">AWS Simple Queueing Service (SQS) 會提供傳訊系統，可連線 AWS 平台內的應用程式、服務和裝置。</span><span class="sxs-lookup"><span data-stu-id="69b13-360">AWS Simple Queueing Service (SQS) provides a messaging system for connecting applications, services, and devices within the AWS platform.</span></span> <span data-ttu-id="69b13-361">Azure 有兩項服務，可提供類似的功能：</span><span class="sxs-lookup"><span data-stu-id="69b13-361">Azure has two services that provide similar functionality:</span></span>

-   <span data-ttu-id="69b13-362">[佇列儲存體](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-queues/) - 雲端傳訊服務，可在 Azure 平台內的應用程式元件之間進行通訊。</span><span class="sxs-lookup"><span data-stu-id="69b13-362">[Queue storage](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-queues/) - a cloud messaging service that allows communication between application components within the Azure platform.</span></span>

-   <span data-ttu-id="69b13-363">[服務匯流排](https://azure.microsoft.com/services/service-bus/) - 更強固的傳訊系統，可連線應用程式、服務和裝置。</span><span class="sxs-lookup"><span data-stu-id="69b13-363">[Service Bus](https://azure.microsoft.com/services/service-bus/) - a more robust messaging system for connecting applications, services, and devices.</span></span> <span data-ttu-id="69b13-364">服務匯流排會使用相關的[服務匯流排轉送](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it)，也可以連線到遠端主控應用程式和服務。</span><span class="sxs-lookup"><span data-stu-id="69b13-364">Using the related [Service Bus relay](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it), Service Bus can also connect to remotely hosted applications and services.</span></span>

#### <a name="device-farm"></a><span data-ttu-id="69b13-365">裝置伺服器陣列</span><span class="sxs-lookup"><span data-stu-id="69b13-365">Device Farm</span></span>

<span data-ttu-id="69b13-366">AWS 裝置伺服陣列會提供跨裝置測試服務。</span><span class="sxs-lookup"><span data-stu-id="69b13-366">The AWS Device Farm provides cross-device testing services.</span></span> <span data-ttu-id="69b13-367">在 Azure 中，[Xamarin Test Cloud](https://www.xamarin.com/test-cloud) 會提供行動裝置的類似跨裝置前端測試。</span><span class="sxs-lookup"><span data-stu-id="69b13-367">In Azure, [Xamarin Test Cloud](https://www.xamarin.com/test-cloud) provides similar cross-device front-end testing for mobile devices.</span></span>

<span data-ttu-id="69b13-368">除了前端測試之外，[Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) 還提供 Linux 和 Windows 環境的後端測試資源。</span><span class="sxs-lookup"><span data-stu-id="69b13-368">In addition to front-end testing, the [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) provides back end testing resources for Linux and Windows environments.</span></span>

#### <a name="see-also"></a><span data-ttu-id="69b13-369">另請參閱</span><span class="sxs-lookup"><span data-stu-id="69b13-369">See also</span></span>

-   [<span data-ttu-id="69b13-370">如何使用 Node.js 的佇列儲存體</span><span class="sxs-lookup"><span data-stu-id="69b13-370">How to use Queue storage from Node.js</span></span>](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-queues/)

-   [<span data-ttu-id="69b13-371">如何使用服務匯流排佇列</span><span class="sxs-lookup"><span data-stu-id="69b13-371">How to use Service Bus queues</span></span>](https://azure.microsoft.com/documentation/articles/service-bus-nodejs-how-to-use-queues/)

### <a name="analytics-and-big-data"></a><span data-ttu-id="69b13-372">分析與巨量資料</span><span class="sxs-lookup"><span data-stu-id="69b13-372">Analytics and big data</span></span>

<span data-ttu-id="69b13-373">[Cortana Intelligence Suite](https://azure.microsoft.com/suites/cortana-intelligence-suite/) 是 Azure 的產品和服務套件，專為擷取、組織、分析和視覺化大量資料而設計。</span><span class="sxs-lookup"><span data-stu-id="69b13-373">[The Cortana Intelligence Suite](https://azure.microsoft.com/suites/cortana-intelligence-suite/) is Azure's package of products and services designed to capture, organize, analyze, and visualize large amounts of data.</span></span> <span data-ttu-id="69b13-374">Cortana 套件包含下列服務：</span><span class="sxs-lookup"><span data-stu-id="69b13-374">The Cortana suite consists of the following services:</span></span>

-   <span data-ttu-id="69b13-375">
            [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) - 受控 Apache 分佈，其中包括 Hadoop、Spark、Storm 或 HBase。</span><span class="sxs-lookup"><span data-stu-id="69b13-375">[HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) - managed Apache distribution that includes Hadoop, Spark, Storm, or HBase.</span></span>

-   <span data-ttu-id="69b13-376">[Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) - 提供資料協調流程和 Data Pipeline 功能。</span><span class="sxs-lookup"><span data-stu-id="69b13-376">[Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) - provides data orchestration and data pipeline functionality.</span></span>

-   <span data-ttu-id="69b13-377">[SQL 資料倉儲](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) - 大規模關聯式資料存放區。</span><span class="sxs-lookup"><span data-stu-id="69b13-377">[SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) - large-scale relational data storage.</span></span>

-   <span data-ttu-id="69b13-378">[Data Lake Store](https://azure.microsoft.com/documentation/services/data-lake-store/) - 針對巨量資料分析工作負載最佳化的大規模儲存體。</span><span class="sxs-lookup"><span data-stu-id="69b13-378">[Data Lake Store](https://azure.microsoft.com/documentation/services/data-lake-store/) - large-scale storage optimized for big data analytics workloads.</span></span>

-   <span data-ttu-id="69b13-379">[Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) - 用來建置及套用關於資料的預測性分析。</span><span class="sxs-lookup"><span data-stu-id="69b13-379">[Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) - used to build and apply predictive analytics on data.</span></span>

-   <span data-ttu-id="69b13-380">[串流分析](https://azure.microsoft.com/documentation/services/stream-analytics/) - 即時資料分析。</span><span class="sxs-lookup"><span data-stu-id="69b13-380">[Stream Analytics](https://azure.microsoft.com/documentation/services/stream-analytics/) - real-time data analysis.</span></span>

-   <span data-ttu-id="69b13-381">[Data Lake Analytics](https://azure.microsoft.com/documentation/articles/data-lake-analytics-overview/) - 針對使用 Data Lake Store 進行最佳化的大規模分析服務</span><span class="sxs-lookup"><span data-stu-id="69b13-381">[Data Lake Analytics](https://azure.microsoft.com/documentation/articles/data-lake-analytics-overview/) - large-scale analytics service optimized to work with Data Lake Store</span></span>

-   <span data-ttu-id="69b13-382">[PowerBI](https://powerbi.microsoft.com/) - 用來提供資料視覺效果。</span><span class="sxs-lookup"><span data-stu-id="69b13-382">[PowerBI](https://powerbi.microsoft.com/) - used to power data visualization.</span></span>

#### <a name="see-also"></a><span data-ttu-id="69b13-383">另請參閱</span><span class="sxs-lookup"><span data-stu-id="69b13-383">See also</span></span>

-   [<span data-ttu-id="69b13-384">Cortana Intelligence Gallery</span><span class="sxs-lookup"><span data-stu-id="69b13-384">Cortana Intelligence Gallery</span></span>](https://gallery.cortanaintelligence.com/)

-   [<span data-ttu-id="69b13-385">了解 Microsoft 巨量資料解決方案</span><span class="sxs-lookup"><span data-stu-id="69b13-385">Understanding Microsoft big data solutions</span></span>](https://msdn.microsoft.com/library/dn749804.aspx)

-   [<span data-ttu-id="69b13-386">Azure Data Lake 與 Azure HDInsight 部落格</span><span class="sxs-lookup"><span data-stu-id="69b13-386">Azure Data Lake & Azure HDInsight Blog</span></span>](https://blogs.msdn.microsoft.com/azuredatalake/)

### <a name="internet-of-things"></a><span data-ttu-id="69b13-387">物聯網</span><span class="sxs-lookup"><span data-stu-id="69b13-387">Internet of Things</span></span>

#### <a name="see-also"></a><span data-ttu-id="69b13-388">另請參閱</span><span class="sxs-lookup"><span data-stu-id="69b13-388">See also</span></span>

-   [<span data-ttu-id="69b13-389">開始使用 Azure IoT 中樞</span><span class="sxs-lookup"><span data-stu-id="69b13-389">Get started with Azure IoT Hub</span></span>](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/)

-   [<span data-ttu-id="69b13-390">IoT 中樞和事件中樞的比較</span><span class="sxs-lookup"><span data-stu-id="69b13-390">Comparison of IoT Hub and Event Hubs</span></span>](https://azure.microsoft.com/documentation/articles/iot-hub-compare-event-hubs/)

### <a name="mobile-services"></a><span data-ttu-id="69b13-391">行動服務</span><span class="sxs-lookup"><span data-stu-id="69b13-391">Mobile services</span></span>

#### <a name="notifications"></a><span data-ttu-id="69b13-392">通知</span><span class="sxs-lookup"><span data-stu-id="69b13-392">Notifications</span></span>

<span data-ttu-id="69b13-393">通知中樞不支援傳送簡訊或電子郵件訊息，因此需要這些傳遞類型的第三方服務。</span><span class="sxs-lookup"><span data-stu-id="69b13-393">Notification Hubs do not support sending SMS or email messages, so third-party services are needed for those delivery types.</span></span>

#### <a name="see-also"></a><span data-ttu-id="69b13-394">另請參閱</span><span class="sxs-lookup"><span data-stu-id="69b13-394">See also</span></span>

-   [<span data-ttu-id="69b13-395">建立 Android 應用程式</span><span class="sxs-lookup"><span data-stu-id="69b13-395">Create an Android app</span></span>](https://azure.microsoft.com/documentation/articles/app-service-mobile-android-get-started/)

-   [<span data-ttu-id="69b13-396">Azure Mobile Apps 中的驗證與授權</span><span class="sxs-lookup"><span data-stu-id="69b13-396">Authentication and Authorization in Azure Mobile Apps</span></span>](https://azure.microsoft.com/documentation/articles/app-service-mobile-auth/)

-   [<span data-ttu-id="69b13-397">使用 Azure 通知中樞傳送推播通知</span><span class="sxs-lookup"><span data-stu-id="69b13-397">Sending push notifications with Azure Notification Hubs</span></span>](https://azure.microsoft.com/documentation/articles/notification-hubs-android-push-notification-google-fcm-get-started/)

### <a name="management-and-monitoring"></a><span data-ttu-id="69b13-398">管理與監視</span><span class="sxs-lookup"><span data-stu-id="69b13-398">Management and monitoring</span></span>

#### <a name="see-also"></a><span data-ttu-id="69b13-399">另請參閱</span><span class="sxs-lookup"><span data-stu-id="69b13-399">See also</span></span>
-   [<span data-ttu-id="69b13-400">監視和診斷指引</span><span class="sxs-lookup"><span data-stu-id="69b13-400">Monitoring and diagnostics guidance</span></span>](https://azure.microsoft.com/documentation/articles/best-practices-monitoring/)

-   [<span data-ttu-id="69b13-401">建立 Azure Resource Manager 範本的最佳做法</span><span class="sxs-lookup"><span data-stu-id="69b13-401">Best practices for creating Azure Resource Manager templates</span></span>](https://azure.microsoft.com/documentation/articles/resource-manager-template-best-practices/)

-   [<span data-ttu-id="69b13-402">Azure Resource Manager 快速入門範本</span><span class="sxs-lookup"><span data-stu-id="69b13-402">Azure Resource Manager Quickstart templates</span></span>](https://azure.microsoft.com/documentation/templates/)


## <a name="next-steps"></a><span data-ttu-id="69b13-403">後續步驟</span><span class="sxs-lookup"><span data-stu-id="69b13-403">Next steps</span></span>

-   [<span data-ttu-id="69b13-404">完成 AWS 和 Azure 服務比較矩陣</span><span class="sxs-lookup"><span data-stu-id="69b13-404">Complete AWS and Azure service comparison matrix</span></span>](https://aka.ms/azure4aws-services)

-   [<span data-ttu-id="69b13-405">互動式 Azure 平台概略圖</span><span class="sxs-lookup"><span data-stu-id="69b13-405">Interactive Azure Platform Big Picture</span></span>](http://azureplatform.azurewebsites.net/)

-   [<span data-ttu-id="69b13-406">開始使用 Azure</span><span class="sxs-lookup"><span data-stu-id="69b13-406">Get started with Azure</span></span>](https://azure.microsoft.com/get-started/)

-   [<span data-ttu-id="69b13-407">Azure 解決方案架構</span><span class="sxs-lookup"><span data-stu-id="69b13-407">Azure solution architectures</span></span>](https://azure.microsoft.com/solutions/architecture/)

-   [<span data-ttu-id="69b13-408">Azure 參考架構</span><span class="sxs-lookup"><span data-stu-id="69b13-408">Azure Reference Architectures</span></span>](https://azure.microsoft.com/documentation/articles/guidance-architecture/)

-   [<span data-ttu-id="69b13-409">模式與做法：Azure 指導方針</span><span class="sxs-lookup"><span data-stu-id="69b13-409">Patterns & Practices: Azure Guidance</span></span>](https://azure.microsoft.com/documentation/articles/guidance/)

-   [<span data-ttu-id="69b13-410">免費線上課程：AWS 專家適用的 Microsoft Azure</span><span class="sxs-lookup"><span data-stu-id="69b13-410">Free Online Course: Microsoft Azure for AWS Experts</span></span>](http://aka.ms/azureforaws)


<!-- links -->

[paired-regions]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[traffic-manager]: /azure/traffic-manager/
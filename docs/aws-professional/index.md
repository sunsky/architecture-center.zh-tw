---
title: "Azure for AWS 專業人員"
description: "了解 Microsoft Azure 帳戶、平台和服務的基本概念。 並了解 AWS 和 Azure 平台之間的金鑰相似度和差異。 利用您在 Azure 中的 AWS 體驗。"
keywords: "AWS 專家, Azure 比較, AWS 比較, azure 與 aws 之間的差異, azure 與 aws"
author: lbrader
ms.date: 03/24/2017
pnp.series.title: Azure for AWS Professionals
ms.openlocfilehash: b576b11bc152ef721f56e79609cb7a03f2d31dd3
ms.sourcegitcommit: 1c0465cea4ceb9ba9bb5e8f1a8a04d3ba2fa5acd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2018
---
# <a name="azure-for-aws-professionals"></a><span data-ttu-id="ecda6-106">適用於 AWS 專業人員的 Azure</span><span class="sxs-lookup"><span data-stu-id="ecda6-106">Azure for AWS Professionals</span></span>

<span data-ttu-id="ecda6-107">本文可協助 Amazon Web Services (AWS) 專家了解 Microsoft Azure 帳戶、平台和服務的基本概念。</span><span class="sxs-lookup"><span data-stu-id="ecda6-107">This article helps Amazon Web Services (AWS) experts understand the basics of Microsoft Azure accounts, platform, and services.</span></span> <span data-ttu-id="ecda6-108">其中也涵蓋 AWS 和 Azure 平台之間的主要相似度和差異。</span><span class="sxs-lookup"><span data-stu-id="ecda6-108">It also covers key similarities and differences between the AWS and Azure platforms.</span></span>

<span data-ttu-id="ecda6-109">您將了解：</span><span class="sxs-lookup"><span data-stu-id="ecda6-109">You'll learn:</span></span>

* <span data-ttu-id="ecda6-110">帳戶和資源在 Azure 中組織的方式。</span><span class="sxs-lookup"><span data-stu-id="ecda6-110">How accounts and resources are organized in Azure.</span></span>
* <span data-ttu-id="ecda6-111">Azure 中可用解決方案的結構方式。</span><span class="sxs-lookup"><span data-stu-id="ecda6-111">How available solutions are structured in Azure.</span></span>
* <span data-ttu-id="ecda6-112">主要 Azure 服務與 AWS 服務有何差異。</span><span class="sxs-lookup"><span data-stu-id="ecda6-112">How the major Azure services differ from AWS services.</span></span>

<span data-ttu-id="ecda6-113">Azure 和 AWS 會隨時間獨立建置其功能，讓每個都有重要實作和設計的差異。</span><span class="sxs-lookup"><span data-stu-id="ecda6-113">Azure and AWS built their capabilities independently over time so that each has important implementation and design differences.</span></span>

## <a name="overview"></a><span data-ttu-id="ecda6-114">概觀</span><span class="sxs-lookup"><span data-stu-id="ecda6-114">Overview</span></span>

<span data-ttu-id="ecda6-115">如同 AWS，Microsoft Azure 架構是以一組核心計算、儲存體、資料庫和網路服務所建置。</span><span class="sxs-lookup"><span data-stu-id="ecda6-115">Like AWS, Microsoft Azure is built around a core set of compute, storage, database, and networking services.</span></span> <span data-ttu-id="ecda6-116">在許多情況下，這兩個平台會在其提供的產品和服務之間提供基本等價。</span><span class="sxs-lookup"><span data-stu-id="ecda6-116">In many cases, both platforms offer a basic equivalence between the products and services they offer.</span></span> <span data-ttu-id="ecda6-117">AWS 和 Azure 都可讓您以 Windows 或 Linux 主機作為基礎，建置高可用性的解決方案。</span><span class="sxs-lookup"><span data-stu-id="ecda6-117">Both AWS and Azure allow you to build highly available solutions based on Windows or Linux hosts.</span></span> <span data-ttu-id="ecda6-118">因此，如果您習慣使用 Linux 及 OSS 技術的開發，這兩個平台皆可執行作業。</span><span class="sxs-lookup"><span data-stu-id="ecda6-118">So, if you're used to development using Linux and OSS technology, both platforms can do the job.</span></span>

<span data-ttu-id="ecda6-119">雖然這兩個平台的功能類似，提供這些功能的資源通常會以不同的方式組織。</span><span class="sxs-lookup"><span data-stu-id="ecda6-119">While the capabilities of both platforms are similar, the resources that provide those capabilities are often organized differently.</span></span> <span data-ttu-id="ecda6-120">建置解決方案所需服務之間的確切一對一關聯性並不一定顯而易見。</span><span class="sxs-lookup"><span data-stu-id="ecda6-120">Exact one-to-one relationships between the services required to build a solution are not always clear.</span></span> <span data-ttu-id="ecda6-121">也有一些情況下，可能會在一個平台上提供對特定服務，但其他平台則不提供。</span><span class="sxs-lookup"><span data-stu-id="ecda6-121">There are also cases where a particular service might be offered on one platform, but not the other.</span></span> <span data-ttu-id="ecda6-122">請參閱[可比較的 Azure 與 AWS 服務圖表](services.md)。</span><span class="sxs-lookup"><span data-stu-id="ecda6-122">See [charts of comparable Azure and AWS services](services.md).</span></span>

## <a name="accounts-and-subscriptions"></a><span data-ttu-id="ecda6-123">帳戶及訂用帳戶</span><span class="sxs-lookup"><span data-stu-id="ecda6-123">Accounts and subscriptions</span></span>

<span data-ttu-id="ecda6-124">可以使用數個定價選項來購買 Azure 服務，取決於貴組織的大小和需求。</span><span class="sxs-lookup"><span data-stu-id="ecda6-124">Azure services can be purchased using several pricing options, depending on your organization's size and needs.</span></span> <span data-ttu-id="ecda6-125">請參閱[定價概觀](https://azure.microsoft.com/pricing/)頁面以取得詳細資料。</span><span class="sxs-lookup"><span data-stu-id="ecda6-125">See the [pricing overview](https://azure.microsoft.com/pricing/) page for details.</span></span>

<span data-ttu-id="ecda6-126">[Azure 訂用帳戶](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-infrastructure-subscription-accounts-guidelines/)是具有指派擁有者的資源群組，負責帳單和權限管理。</span><span class="sxs-lookup"><span data-stu-id="ecda6-126">[Azure subscriptions](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-infrastructure-subscription-accounts-guidelines/) are a grouping of resources with an assigned owner responsible for billing and permissions management.</span></span> <span data-ttu-id="ecda6-127">不同於 AWS，其中在 AWS 帳戶下建立的任何資源都會繫結至該帳戶，訂用帳戶會與其擁有者帳戶分開存在，且可視需要重新指派給新的擁有者。</span><span class="sxs-lookup"><span data-stu-id="ecda6-127">Unlike AWS, where any resources created under the AWS account are tied to that account, subscriptions exist independently of their owner accounts, and can be reassigned to new owners as needed.</span></span>

<span data-ttu-id="ecda6-128">![比較 AWS 帳戶與 Azure 訂用帳戶的結構和擁有權](./images/azure-aws-account-compare.png "比較 AWS 帳戶與 Azure 訂用帳戶的結構和擁有權")</span><span class="sxs-lookup"><span data-stu-id="ecda6-128">![Comparison of structure and ownership of AWS accounts and Azure subscriptions](./images/azure-aws-account-compare.png "Comparison of structure and ownership of AWS accounts and Azure subscriptions")</span></span>
<br/><span data-ttu-id="ecda6-129">*比較 AWS 帳戶與 Azure 訂用帳戶的結構和 擁有權*</span><span class="sxs-lookup"><span data-stu-id="ecda6-129">*Comparison of structure and ownership of AWS accounts and Azure subscriptions*</span></span>
<br/><br/>

<span data-ttu-id="ecda6-130">有三種類型的系統管理員帳戶會指派給訂用帳戶：</span><span class="sxs-lookup"><span data-stu-id="ecda6-130">Subscriptions are assigned three types of administrator accounts:</span></span>

-   <span data-ttu-id="ecda6-131">**帳戶管理員** - 訂用帳戶擁有者和針對訂用帳戶中使用的資源計費的帳戶。</span><span class="sxs-lookup"><span data-stu-id="ecda6-131">**Account Administrator** - The subscription owner and the account billed for the resources used in the subscription.</span></span> <span data-ttu-id="ecda6-132">只能藉由轉移訂用帳戶的擁有權來變更帳戶管理員。</span><span class="sxs-lookup"><span data-stu-id="ecda6-132">The account administrator can only be changed by transferring ownership of the subscription.</span></span>

-   <span data-ttu-id="ecda6-133">**服務管理員** - 此帳戶有權限可建立及管理訂用帳戶中的資源，但不負責計費。</span><span class="sxs-lookup"><span data-stu-id="ecda6-133">**Service Administrator** - This account has rights to create and manage resources in the subscription, but is not responsible for billing.</span></span> <span data-ttu-id="ecda6-134">根據預設，帳戶管理員和服務管理員會指派給相同的帳戶。</span><span class="sxs-lookup"><span data-stu-id="ecda6-134">By default, the account administrator and service administrator are assigned to the same account.</span></span> <span data-ttu-id="ecda6-135">帳戶管理員可以將個別使用者指派給服務管理員帳戶，來管理訂用帳戶的技術和運作層面。</span><span class="sxs-lookup"><span data-stu-id="ecda6-135">The account administrator can assign a separate user to the service administrator account for managing the technical and operational aspects of a subscription.</span></span> <span data-ttu-id="ecda6-136">每個訂用帳戶只有一個服務管理員。</span><span class="sxs-lookup"><span data-stu-id="ecda6-136">There is only one service administrator per subscription.</span></span>

-   <span data-ttu-id="ecda6-137">**共同管理員** - 可指派多個共同管理員帳戶給訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="ecda6-137">**Co-administrator** - There can be multiple co-administrator accounts assigned to a subscription.</span></span> <span data-ttu-id="ecda6-138">共同管理員無法變更服務管理員，但擁有訂用帳戶資源和使用者的完整控制權。</span><span class="sxs-lookup"><span data-stu-id="ecda6-138">Co-administrators cannot change the service administrator, but otherwise have full control over subscription resources and users.</span></span>

<span data-ttu-id="ecda6-139">以下訂用帳戶層級使用者角色和個別權限也可以指派給特定的資源，類似於在 AWS 中將權限授與 IAM 使用者和群組的方式。</span><span class="sxs-lookup"><span data-stu-id="ecda6-139">Below the subscription level user roles and individual permissions can also be assigned to specific resources, similarly to how permissions are granted to IAM users and groups in AWS.</span></span> <span data-ttu-id="ecda6-140">在 Azure 中的所有使用者帳戶都會與 Microsoft 帳戶或組織帳戶 (透過 Azure Active Directory 管理的帳戶) 相關聯。</span><span class="sxs-lookup"><span data-stu-id="ecda6-140">In Azure all user accounts are associated with either a Microsoft Account or Organizational Account (an account managed through an Azure Active Directory).</span></span>

<span data-ttu-id="ecda6-141">如同 AWS 帳戶，訂用帳戶具有預設的服務配額和限制。</span><span class="sxs-lookup"><span data-stu-id="ecda6-141">Like AWS accounts, subscriptions have default service quotas and limits.</span></span> <span data-ttu-id="ecda6-142">如需這些限制的完整清單，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/)。</span><span class="sxs-lookup"><span data-stu-id="ecda6-142">For a full list of these limits, see [Azure subscription and service limits, quotas, and constraints](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/).</span></span>
<span data-ttu-id="ecda6-143">可以藉由[在管理入口網站中提出支援要求](https://blogs.msdn.microsoft.com/girishp/2015/09/20/increasing-core-quota-limits-in-azure/)，將這些限制增加到最大值。</span><span class="sxs-lookup"><span data-stu-id="ecda6-143">These limits can be increased up to the maximum by [filing a support request in the management portal](https://blogs.msdn.microsoft.com/girishp/2015/09/20/increasing-core-quota-limits-in-azure/).</span></span>

### <a name="see-also"></a><span data-ttu-id="ecda6-144">另請參閱</span><span class="sxs-lookup"><span data-stu-id="ecda6-144">See also</span></span>

-   [<span data-ttu-id="ecda6-145">如何新增或變更 Azure 管理員角色</span><span class="sxs-lookup"><span data-stu-id="ecda6-145">How to add or change Azure administrator roles</span></span>](https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/)

-   [<span data-ttu-id="ecda6-146">如何下載您的 Azure 帳單發票和每日使用量資料</span><span class="sxs-lookup"><span data-stu-id="ecda6-146">How to download your Azure billing invoice and daily usage data</span></span>](https://azure.microsoft.com/documentation/articles/billing-download-azure-invoice-daily-usage-date/)

## <a name="resource-management"></a><span data-ttu-id="ecda6-147">資源管理</span><span class="sxs-lookup"><span data-stu-id="ecda6-147">Resource management</span></span>

<span data-ttu-id="ecda6-148">Azure 中的「資源」一詞與 AWS 中的使用方式相同，這表示您可以在平台內建立或設定的任何計算執行個體、儲存物件、網路裝置或其他實體。</span><span class="sxs-lookup"><span data-stu-id="ecda6-148">The term "resource" in Azure is used in the same way as in AWS, meaning any compute instance, storage object, networking device, or other entity you can create or configure within the platform.</span></span>

<span data-ttu-id="ecda6-149">會使用下列兩個模型其中之一來部署及管理 Azure 資源：[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) 或較舊的 Azure [傳統部署模型](/azure/azure-resource-manager/resource-manager-deployment-model)。</span><span class="sxs-lookup"><span data-stu-id="ecda6-149">Azure resources are deployed and managed using one of two models: [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview), or the older Azure [classic deployment model](/azure/azure-resource-manager/resource-manager-deployment-model).</span></span>
<span data-ttu-id="ecda6-150">使用 Resource Manager 模型來建立任何新的資源。</span><span class="sxs-lookup"><span data-stu-id="ecda6-150">Any new resources are created using the Resource Manager model.</span></span>

### <a name="resource-groups"></a><span data-ttu-id="ecda6-151">資源群組</span><span class="sxs-lookup"><span data-stu-id="ecda6-151">Resource groups</span></span>

<span data-ttu-id="ecda6-152">Azure 和 AWS 都有稱為「資源群組」的實體，能組織諸如 VM、儲存體和虛擬網路裝置等資源。</span><span class="sxs-lookup"><span data-stu-id="ecda6-152">Both Azure and AWS have entities called "resource groups" that organize resources such as VMs, storage, and virtual networking devices.</span></span> <span data-ttu-id="ecda6-153">不過，[Azure 資源群組](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/)無法直接與 AWS 資源群組比較。</span><span class="sxs-lookup"><span data-stu-id="ecda6-153">However, [Azure resource groups](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/) are not directly comparable to AWS resource groups.</span></span>

<span data-ttu-id="ecda6-154">儘管 AWS 允許將資源標記成多個資源群組，但 Azure 資源一律與一個資源群組相關聯。</span><span class="sxs-lookup"><span data-stu-id="ecda6-154">While AWS allows a resource to be tagged into multiple resource groups, an Azure resource is always associated with one resource group.</span></span> <span data-ttu-id="ecda6-155">一個資源群組中建立的資源可以移至另一個群組，但一次只能在一個資源群組中。</span><span class="sxs-lookup"><span data-stu-id="ecda6-155">A resource created in one resource group can be moved to another group, but can only be in one resource group at a time.</span></span> <span data-ttu-id="ecda6-156">資源群組是 Azure Resource Manager 所使用的基本群組。</span><span class="sxs-lookup"><span data-stu-id="ecda6-156">Resource groups are the fundamental grouping used by Azure Resource Manager.</span></span>

<span data-ttu-id="ecda6-157">也可以使用[標記](https://azure.microsoft.com/documentation/articles/resource-group-using-tags/)來組織資源。</span><span class="sxs-lookup"><span data-stu-id="ecda6-157">Resources can also be organized using [tags](https://azure.microsoft.com/documentation/articles/resource-group-using-tags/).</span></span>
<span data-ttu-id="ecda6-158">標記是機碼值組，可讓您跨訂用帳戶群組資源，不論資源群組成員資格。</span><span class="sxs-lookup"><span data-stu-id="ecda6-158">Tags are key-value pairs that allow you to group resources across your subscription irrespective of resource group membership.</span></span>

### <a name="management-interfaces"></a><span data-ttu-id="ecda6-159">管理介面</span><span class="sxs-lookup"><span data-stu-id="ecda6-159">Management interfaces</span></span>

<span data-ttu-id="ecda6-160">Azure 提供數種方式來管理您的資源：</span><span class="sxs-lookup"><span data-stu-id="ecda6-160">Azure offers several ways to manage your resources:</span></span>

-   <span data-ttu-id="ecda6-161">[Web 介面](https://azure.microsoft.com/documentation/articles/resource-group-portal/)。</span><span class="sxs-lookup"><span data-stu-id="ecda6-161">[Web interface](https://azure.microsoft.com/documentation/articles/resource-group-portal/).</span></span>
    <span data-ttu-id="ecda6-162">如同 AWS 儀表板，Azure 入口網站會提供 Azure 資源的完整 web 型管理介面。</span><span class="sxs-lookup"><span data-stu-id="ecda6-162">Like the AWS Dashboard, the Azure portal provides a full web-based management interface for Azure resources.</span></span>

-   <span data-ttu-id="ecda6-163">[REST API](https://azure.microsoft.com/documentation/articles/resource-manager-rest-api/)。</span><span class="sxs-lookup"><span data-stu-id="ecda6-163">[REST API](https://azure.microsoft.com/documentation/articles/resource-manager-rest-api/).</span></span>
    <span data-ttu-id="ecda6-164">Azure Resource Manager REST API 會提供以程式設計方式存取 Azure 入口網站中大部分可用的功能。</span><span class="sxs-lookup"><span data-stu-id="ecda6-164">The Azure Resource Manager REST API provides programmatic access to most of the features available in the Azure portal.</span></span>

-   <span data-ttu-id="ecda6-165">[命令列](https://azure.microsoft.com/documentation/articles/xplat-cli-azure-resource-manager/)。</span><span class="sxs-lookup"><span data-stu-id="ecda6-165">[Command Line](https://azure.microsoft.com/documentation/articles/xplat-cli-azure-resource-manager/).</span></span>
    <span data-ttu-id="ecda6-166">Azure CLI 2.0 工具提供的命令列介面能夠建立及管理 Azure 資源。</span><span class="sxs-lookup"><span data-stu-id="ecda6-166">The Azure CLI 2.0 tool provides a command-line interface capable of creating and managing Azure resources.</span></span> <span data-ttu-id="ecda6-167">Azure CLI 適用於 [Windows、Linux 和 Mac OS](https://aka.ms/azurecli2)。</span><span class="sxs-lookup"><span data-stu-id="ecda6-167">Azure CLI is available for [Windows, Linux, and Mac OS](https://aka.ms/azurecli2).</span></span>

-   <span data-ttu-id="ecda6-168">[PowerShell](https://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/)。</span><span class="sxs-lookup"><span data-stu-id="ecda6-168">[PowerShell](https://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/).</span></span>
    <span data-ttu-id="ecda6-169">適用於 PowerShell 的 Azure 模組可讓您使用指令碼執行自動化的管理工作。</span><span class="sxs-lookup"><span data-stu-id="ecda6-169">The Azure modules for PowerShell allow you to execute automated management tasks using a script.</span></span> <span data-ttu-id="ecda6-170">PowerShell 適用於 [Windows、Linux 和 Mac OS](https://github.com/PowerShell/PowerShell)。</span><span class="sxs-lookup"><span data-stu-id="ecda6-170">PowerShell is available for [Windows, Linux, and Mac OS](https://github.com/PowerShell/PowerShell).</span></span>

-   <span data-ttu-id="ecda6-171">[範本](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/)。</span><span class="sxs-lookup"><span data-stu-id="ecda6-171">[Templates](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/).</span></span>
    <span data-ttu-id="ecda6-172">Azure Resource Manager 範本會提供類似的以 JSON 範本為基礎之資源管理功能給 AWS CloudFormation 服務。</span><span class="sxs-lookup"><span data-stu-id="ecda6-172">Azure Resource Manager templates provide similar JSON template-based resource management capabilities to the AWS CloudFormation service.</span></span>

<span data-ttu-id="ecda6-173">在這些介面中，資源群組是 Azure 資源建立、部署或修改方式的核心。</span><span class="sxs-lookup"><span data-stu-id="ecda6-173">In each of these interfaces, the resource group is central to how Azure resources get created, deployed, or modified.</span></span> <span data-ttu-id="ecda6-174">這類似於「堆疊」在 CloudFormation 部署期間群組 AWS 資源所扮演的角色。</span><span class="sxs-lookup"><span data-stu-id="ecda6-174">This is similar to the role a "stack" plays in grouping AWS resources during CloudFormation deployments.</span></span>

<span data-ttu-id="ecda6-175">這些介面的語法和結構與其 AWS 對等項目不同，但可提供相當程度的功能。</span><span class="sxs-lookup"><span data-stu-id="ecda6-175">The syntax and structure of these interfaces are different from their AWS equivalents, but they provide comparable capabilities.</span></span> <span data-ttu-id="ecda6-176">此外，許多 AWS 上使用的第三方管理工具 (例如 [Hashicorp 的 Terraform](https://www.terraform.io/docs/providers/azurerm/) 和 [Netflix Spinnaker](http://www.spinnaker.io/) 也會在 Azure 上提供。</span><span class="sxs-lookup"><span data-stu-id="ecda6-176">In addition, many third party management tools used on AWS, like [Hashicorp's Terraform](https://www.terraform.io/docs/providers/azurerm/) and [Netflix Spinnaker](http://www.spinnaker.io/), are also available on Azure.</span></span>

### <a name="see-also"></a><span data-ttu-id="ecda6-177">另請參閱</span><span class="sxs-lookup"><span data-stu-id="ecda6-177">See also</span></span>

-   [<span data-ttu-id="ecda6-178">Azure 資源群組指導方針</span><span class="sxs-lookup"><span data-stu-id="ecda6-178">Azure resource group guidelines</span></span>](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/)

## <a name="regions-and-zones-high-availability"></a><span data-ttu-id="ecda6-179">地區和區域 (高可用性)</span><span class="sxs-lookup"><span data-stu-id="ecda6-179">Regions and zones (high availability)</span></span>

<span data-ttu-id="ecda6-180">在 AWS 中，可用性集中於「可用性區域」的概念。</span><span class="sxs-lookup"><span data-stu-id="ecda6-180">In AWS, availability centers around the concept of Availability Zones.</span></span> <span data-ttu-id="ecda6-181">在 Azure 中，建置高可用性解決方案會牽涉容錯網域和可用性設定組。</span><span class="sxs-lookup"><span data-stu-id="ecda6-181">In Azure, fault domains and availability sets are all involved in building highly available solutions.</span></span> <span data-ttu-id="ecda6-182">配對的地區會提供額外的災害復原功能。</span><span class="sxs-lookup"><span data-stu-id="ecda6-182">Paired regions provide additional disaster recovery capabilities.</span></span>

### <a name="availability-zones-azure-fault-domains-and-availability-sets"></a><span data-ttu-id="ecda6-183">可用性區域、容錯網域和可用性設定組</span><span class="sxs-lookup"><span data-stu-id="ecda6-183">Availability Zones, Azure fault domains, and availability sets</span></span>

<span data-ttu-id="ecda6-184">在 AWS 中，地區會分割成兩個或多個可用性區域。</span><span class="sxs-lookup"><span data-stu-id="ecda6-184">In AWS, a region is divided into two or more Availability Zones.</span></span> <span data-ttu-id="ecda6-185">可用性區域會與地理區域中實際隔離的資料中心相對應。</span><span class="sxs-lookup"><span data-stu-id="ecda6-185">An Availability Zone corresponds with a physically isolated datacenter in the geographic region.</span></span>
<span data-ttu-id="ecda6-186">如果您將部署應用程式伺服器來分隔可用性區域，影響一個區域的硬體或連線中斷並不會影響其他區域中裝載的任何伺服器。</span><span class="sxs-lookup"><span data-stu-id="ecda6-186">If you deploy your application servers to separate Availability Zones, a hardware or connectivity outage affecting one zone does not impact any servers hosted in other zones.</span></span>

<span data-ttu-id="ecda6-187">在 Azure 中，[容錯網域](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-manage-availability/)會定義共用實體電源來源和網路交換器的 VM 群組。</span><span class="sxs-lookup"><span data-stu-id="ecda6-187">In Azure, a [fault domain](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-manage-availability/) defines a group of VMs that shares a physical power source and network switch.</span></span>
<span data-ttu-id="ecda6-188">您要使用[可用性設定組](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-manage-availability/)將多個容錯網域分散到 VM。</span><span class="sxs-lookup"><span data-stu-id="ecda6-188">You use [availability sets](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-manage-availability/) to spread VMs across multiple fault domains.</span></span> <span data-ttu-id="ecda6-189">當執行個體指派給相同的可用性設定組時，Azure 會平均地跨數個容錯網域加以散發。</span><span class="sxs-lookup"><span data-stu-id="ecda6-189">When instances are assigned to the same availability set, Azure distributes them evenly across several fault domains.</span></span> <span data-ttu-id="ecda6-190">如果一個容錯網域中發生電源故障或網路中斷，至少有一些集合的 VM 會在容錯網域中，且不受中斷影響。</span><span class="sxs-lookup"><span data-stu-id="ecda6-190">If a power failure or network outage occurs in one fault domain, at least some of the set's VMs are in another fault domain and unaffected by the outage.</span></span>

<span data-ttu-id="ecda6-191">![AWS 可用性區域相較於 Azure 容錯網域和可用性設定組](./images/zone-fault-domains.png "AWS 可用性區域相較於 Azure 容錯網域和可用性設定組")</span><span class="sxs-lookup"><span data-stu-id="ecda6-191">![AWS Availability Zones comparison to Azure fault domains and availability sets](./images/zone-fault-domains.png "AWS Availability Zones compared with Azure fault domains and availability sets")</span></span>
<br/><span data-ttu-id="ecda6-192">*AWS 可用性區域相較於 Azure 容錯網域和可用性設定組*</span><span class="sxs-lookup"><span data-stu-id="ecda6-192">*AWS Availability Zones compared with Azure fault domains and availability sets*</span></span>
<br/><br/>

<span data-ttu-id="ecda6-193">應該依您應用程式中執行個體的角色來組織可用性設定組，以確保每個角色的一個執行個體皆可運作。</span><span class="sxs-lookup"><span data-stu-id="ecda6-193">Availability sets should be organized by the instance's role in your application to ensure one instance in each role is operational.</span></span> <span data-ttu-id="ecda6-194">例如，在標準三層式 web 應用程式中，您需要針對前端、應用程式及資料執行個體建立個別的可用性設定組。</span><span class="sxs-lookup"><span data-stu-id="ecda6-194">For example, in a standard three-tier web application, you would want to create a separate availability set for front-end, application, and data instances.</span></span>

<span data-ttu-id="ecda6-195">![每個應用程式角色的 Azure 可用性設定組](./images/three-tier-example.png "每個應用程式角色的可用性設定組")</span><span class="sxs-lookup"><span data-stu-id="ecda6-195">![Azure availability sets for each application role](./images/three-tier-example.png "Availability sets for each application role")</span></span>
<br/><span data-ttu-id="ecda6-196">*每個應用程式角色的 Azure 可用性設定組*</span><span class="sxs-lookup"><span data-stu-id="ecda6-196">*Azure availability sets for each application role*</span></span>
<br/><br/>

<span data-ttu-id="ecda6-197">將 VM 執行個體新增至可用性設定組時，也會指派[更新網域](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-manage-availability/)給它們。</span><span class="sxs-lookup"><span data-stu-id="ecda6-197">When VM instances are added to availability sets, they are also assigned an [update domain](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-manage-availability/).</span></span>
<span data-ttu-id="ecda6-198">更新網域是 VM 群組，會同時針對預定進行的維修作業事件而設定。</span><span class="sxs-lookup"><span data-stu-id="ecda6-198">An update domain is a group of VMs that are set for planned maintenance events at the same time.</span></span> <span data-ttu-id="ecda6-199">將 VM 分散到多個更新網域，以確保計劃的更新和修補事件在任何指定時間只會影響這些 VM 的子集。</span><span class="sxs-lookup"><span data-stu-id="ecda6-199">Distributing VMs across multiple update domains ensures that planned update and patching events affect only a subset of these VMs at any given time.</span></span>

### <a name="paired-regions"></a><span data-ttu-id="ecda6-200">配對的區域</span><span class="sxs-lookup"><span data-stu-id="ecda6-200">Paired regions</span></span>

<span data-ttu-id="ecda6-201">在 Azure 中，您要使用[配對地區](https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/)跨兩個預先定義的地理區域支援備援性，以確保即使中斷影響到整個 Azure 地區，您的解決方案仍然可以使用。</span><span class="sxs-lookup"><span data-stu-id="ecda6-201">In Azure, you use [paired regions](https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/) to support redundancy across two predefined geographic regions, ensuring that even if an outage affects an entire Azure region, your solution is still available.</span></span>

<span data-ttu-id="ecda6-202">不同於實體上分隔資料中心但可能會相對在附近地理區域的 AWS 可用性區域，通常會以最少 300 英哩來分隔配對地區。</span><span class="sxs-lookup"><span data-stu-id="ecda6-202">Unlike AWS Availability Zones, which are physically separate datacenters but may be in relatively nearby geographic areas, paired regions are usually separated by at least 300 miles.</span></span> <span data-ttu-id="ecda6-203">這是為了確保較大規模的災難只會影響配對中的其中一個地區。</span><span class="sxs-lookup"><span data-stu-id="ecda6-203">This is intended to ensure larger scale disasters only impact one of the regions in the pair.</span></span> <span data-ttu-id="ecda6-204">相鄰的配對可以設定為同步處理資料庫和儲存體服務資料，並加以設定以便平台更新一次只會發行至配對中的一個地區。</span><span class="sxs-lookup"><span data-stu-id="ecda6-204">Neighboring pairs can be set to sync database and storage service data, and are configured so that platform updates are rolled out to only one region in the pair at a time.</span></span>

<span data-ttu-id="ecda6-205">Azure [異地備援儲存體](https://azure.microsoft.com/documentation/articles/storage-redundancy/#geo-redundant-storage)會自動備份至適當的配對區域。</span><span class="sxs-lookup"><span data-stu-id="ecda6-205">Azure [geo-redundant storage](https://azure.microsoft.com/documentation/articles/storage-redundancy/#geo-redundant-storage) is automatically backed up to the appropriate paired region.</span></span> <span data-ttu-id="ecda6-206">如需其他所有資源，請使用配對區域建立完整備援的解決方案，即表示在兩個區域中建立您解決方案的完整副本。</span><span class="sxs-lookup"><span data-stu-id="ecda6-206">For all other resources, creating a fully redundant solution using paired regions means creating a full copy of your solution in both regions.</span></span>

### <a name="see-also"></a><span data-ttu-id="ecda6-207">另請參閱</span><span class="sxs-lookup"><span data-stu-id="ecda6-207">See also</span></span>

-   [<span data-ttu-id="ecda6-208">Azure 中虛擬機器的區域和可用性</span><span class="sxs-lookup"><span data-stu-id="ecda6-208">Regions and availability for virtual machines in Azure</span></span>](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-regions-and-availability/)

-   [<span data-ttu-id="ecda6-209">Azure 應用程式的高可用性</span><span class="sxs-lookup"><span data-stu-id="ecda6-209">High availability for Azure applications</span></span>](../resiliency/high-availability-azure-applications.md)

-   [<span data-ttu-id="ecda6-210">Azure 應用程式的災害復原</span><span class="sxs-lookup"><span data-stu-id="ecda6-210">Disaster recovery for Azure applications</span></span>](../resiliency/disaster-recovery-azure-applications.md)

-   [<span data-ttu-id="ecda6-211">Azure 中 Linux 虛擬機器預定進行的維修</span><span class="sxs-lookup"><span data-stu-id="ecda6-211">Planned maintenance for Linux virtual machines in Azure</span></span>](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-planned-maintenance/)

## <a name="services"></a><span data-ttu-id="ecda6-212">服務</span><span class="sxs-lookup"><span data-stu-id="ecda6-212">Services</span></span>

<span data-ttu-id="ecda6-213">如需所有服務在平台之間對應方式的完整清單，請參閱[完成 AWS 和 Azure 服務比較矩陣](https://aka.ms/azure4aws-services)。</span><span class="sxs-lookup"><span data-stu-id="ecda6-213">Consult the [complete AWS and Azure service comparison matrix](https://aka.ms/azure4aws-services) for a full listing of how all services map between platforms.</span></span>

<span data-ttu-id="ecda6-214">並非所有區域中都可使用所有的 Azure 產品和服務。</span><span class="sxs-lookup"><span data-stu-id="ecda6-214">Not all Azure products and services are available in all regions.</span></span> <span data-ttu-id="ecda6-215">如需詳細資料，請參閱[依區域的產品](https://azure.microsoft.com/regions/services/)頁面。</span><span class="sxs-lookup"><span data-stu-id="ecda6-215">Consult the [Products by Region](https://azure.microsoft.com/regions/services/) page for details.</span></span> <span data-ttu-id="ecda6-216">您可以在[服務等級協定](https://azure.microsoft.com/support/legal/sla/)頁面上找到每個 Azure 產品或服務的執行時間保證和停機時間信用原則。</span><span class="sxs-lookup"><span data-stu-id="ecda6-216">You can find the uptime guarantees and downtime credit policies for each Azure product or service on the [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/) page.</span></span>

<span data-ttu-id="ecda6-217">下列章節會提供 AWS 和 Azure 平台之間的常用功能和服務之差異的簡短說明。</span><span class="sxs-lookup"><span data-stu-id="ecda6-217">The following sections provide a brief explanation of how commonly used features and services differ between the AWS and Azure platforms.</span></span>

### <a name="compute-services"></a><span data-ttu-id="ecda6-218">計算服務</span><span class="sxs-lookup"><span data-stu-id="ecda6-218">Compute services</span></span>

#### <a name="ec2-instances-and-azure-virtual-machines"></a><span data-ttu-id="ecda6-219">EC2 執行個體和 Azure 虛擬機器</span><span class="sxs-lookup"><span data-stu-id="ecda6-219">EC2 Instances and Azure virtual machines</span></span>

<span data-ttu-id="ecda6-220">雖然 AWS 執行個體類型和 Azure 虛擬機器大小是以類似的方式分解，但 RAM、CPU 和儲存體功能中會有所差異。</span><span class="sxs-lookup"><span data-stu-id="ecda6-220">Although AWS instance types and Azure virtual machine sizes breakdown in a similar way, there are differences in the RAM, CPU, and storage capabilities.</span></span>

-   [<span data-ttu-id="ecda6-221">Amazon EC2 執行個體類型</span><span class="sxs-lookup"><span data-stu-id="ecda6-221">Amazon EC2 Instance Types</span></span>](https://aws.amazon.com/ec2/instance-types/)

-   [<span data-ttu-id="ecda6-222">Azure 中的虛擬機器大小 (Windows)</span><span class="sxs-lookup"><span data-stu-id="ecda6-222">Sizes for virtual machines in Azure (Windows)</span></span>](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/)

-   [<span data-ttu-id="ecda6-223">Azure 中的虛擬機器大小 (Linux)</span><span class="sxs-lookup"><span data-stu-id="ecda6-223">Sizes for virtual machines in Azure (Linux)</span></span>](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/)

<span data-ttu-id="ecda6-224">不同於 AWS 的每個第二個計費，Azure 隨選 VM 會依分鐘計費。</span><span class="sxs-lookup"><span data-stu-id="ecda6-224">Unlike AWS' per second billing, Azure on-demand VMs are billed by the minute.</span></span>

<span data-ttu-id="ecda6-225">Azure 與 EC2 Spot 執行個體或專用主機並沒有對等項目。</span><span class="sxs-lookup"><span data-stu-id="ecda6-225">Azure has no equivalent to EC2 Spot Instances or Dedicated Hosts.</span></span>

#### <a name="ebs-and-azure-storage-for-vm-disks"></a><span data-ttu-id="ecda6-226">適用於 VM 磁碟的 EBS 和 Azure 儲存體</span><span class="sxs-lookup"><span data-stu-id="ecda6-226">EBS and Azure Storage for VM disks</span></span>

<span data-ttu-id="ecda6-227">Azure VM 的長期資料儲存區是由位在 blob 儲存體中的[資料磁碟](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-about-disks-vhds/)所提供。</span><span class="sxs-lookup"><span data-stu-id="ecda6-227">Durable data storage for Azure VMs is provided by [data disks](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-about-disks-vhds/) residing in blob storage.</span></span> <span data-ttu-id="ecda6-228">這類似於 EC2 執行個體在 Elastic Block Store (EBS) 上儲存磁碟區的方式。</span><span class="sxs-lookup"><span data-stu-id="ecda6-228">This is similar to how EC2 instances store disk volumes on Elastic Block Store (EBS).</span></span> <span data-ttu-id="ecda6-229">[Azure 暫存儲存體](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)也會提供 VM 與 EC2 執行個體儲存體 (也稱為暫時儲存體) 相同的低延遲暫存讀寫儲存體。</span><span class="sxs-lookup"><span data-stu-id="ecda6-229">[Azure temporary storage](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/) also provides VMs the same low-latency temporary read-write storage as EC2 Instance Storage (also called ephemeral storage).</span></span>

<span data-ttu-id="ecda6-230">會使用 [Azure 高階儲存體](https://docs.microsoft.com/azure/storage/storage-premium-storage)支援較高的效能磁碟 IO。</span><span class="sxs-lookup"><span data-stu-id="ecda6-230">Higher performance disk IO is supported using [Azure premium storage](https://docs.microsoft.com/azure/storage/storage-premium-storage).</span></span>
<span data-ttu-id="ecda6-231">這是類似於 AWS 所提供之佈建的 IOPS 儲存體選項。</span><span class="sxs-lookup"><span data-stu-id="ecda6-231">This is similar to the Provisioned IOPS storage options provided by AWS.</span></span>

#### <a name="lambda-azure-functions-azure-web-jobs-and-azure-logic-apps"></a><span data-ttu-id="ecda6-232">Lambda、Azure Functions、Azure Web 作業以及 Azure Logic Apps</span><span class="sxs-lookup"><span data-stu-id="ecda6-232">Lambda, Azure Functions, Azure Web-Jobs, and Azure Logic Apps</span></span>

<span data-ttu-id="ecda6-233">[Azure Functions](https://azure.microsoft.com/services/functions/) 在提供無伺服器、隨選程式碼方面是 AWS Lambda 的主要對等項目。</span><span class="sxs-lookup"><span data-stu-id="ecda6-233">[Azure Functions](https://azure.microsoft.com/services/functions/) is the primary equivalent of AWS Lambda in providing serverless, on-demand code.</span></span>
<span data-ttu-id="ecda6-234">不過，Lambda 功能也會與其他 Azure 服務重疊：</span><span class="sxs-lookup"><span data-stu-id="ecda6-234">However, Lambda functionality also overlaps with other Azure services:</span></span>

-   <span data-ttu-id="ecda6-235">[WebJobs](https://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/) - 可讓您建立排程或連續執行的背景工作。</span><span class="sxs-lookup"><span data-stu-id="ecda6-235">[WebJobs](https://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/) - allow you to create scheduled or continuously running background tasks.</span></span>

-   <span data-ttu-id="ecda6-236">[Logic Apps](https://azure.microsoft.com/services/logic-apps/) - 提供通訊、整合及商務規則管理服務。</span><span class="sxs-lookup"><span data-stu-id="ecda6-236">[Logic Apps](https://azure.microsoft.com/services/logic-apps/) - provides communications, integration, and business rule management services.</span></span>

#### <a name="autoscaling-azure-vm-scaling-and-azure-app-service-autoscale"></a><span data-ttu-id="ecda6-237">自動調整、Azure VM 縮放比例和 Azure App Service 自動調整</span><span class="sxs-lookup"><span data-stu-id="ecda6-237">Autoscaling, Azure VM scaling, and Azure App Service Autoscale</span></span>

<span data-ttu-id="ecda6-238">Azure 中的自動調整是由兩個服務處理：</span><span class="sxs-lookup"><span data-stu-id="ecda6-238">Autoscaling in Azure is handled by two services:</span></span>

-   <span data-ttu-id="ecda6-239">[VM 擴展集](https://azure.microsoft.com/documentation/articles/virtual-machine-scale-sets-overview/) - 可讓您部署及管理一組完全相同的 VM。</span><span class="sxs-lookup"><span data-stu-id="ecda6-239">[VM scale sets](https://azure.microsoft.com/documentation/articles/virtual-machine-scale-sets-overview/) - allow you to deploy and manage an identical set of VMs.</span></span> <span data-ttu-id="ecda6-240">執行個體數目可以效能需求作為基礎來自動調整。</span><span class="sxs-lookup"><span data-stu-id="ecda6-240">The number of instances can autoscale based on performance needs.</span></span>

-   <span data-ttu-id="ecda6-241">[App Service 自動調整](https://azure.microsoft.com/documentation/articles/web-sites-scale/) - 提供自動調整 Azure App Service 解決方案的功能。</span><span class="sxs-lookup"><span data-stu-id="ecda6-241">[App Service Autoscale](https://azure.microsoft.com/documentation/articles/web-sites-scale/) - provides the capability to autoscale Azure App Service solutions.</span></span>


#### <a name="container-service"></a><span data-ttu-id="ecda6-242">容器服務</span><span class="sxs-lookup"><span data-stu-id="ecda6-242">Container Service</span></span>
<span data-ttu-id="ecda6-243">[Azure Container Service](https://docs.microsoft.com/azure/container-service/container-service-intro) 支援透過 Docker Swarm、Kubernetes 或 DC/OS 管理的 Docker 容器。</span><span class="sxs-lookup"><span data-stu-id="ecda6-243">The [Azure Container Service](https://docs.microsoft.com/azure/container-service/container-service-intro) supports Docker containers managed through Docker Swarm, Kubernetes, or DC/OS.</span></span>

#### <a name="other-compute-services"></a><span data-ttu-id="ecda6-244">其他計算服務</span><span class="sxs-lookup"><span data-stu-id="ecda6-244">Other compute services</span></span> 


<span data-ttu-id="ecda6-245">Azure 會提供數個計算服務，在 AWS 中沒有直接的對等項目：</span><span class="sxs-lookup"><span data-stu-id="ecda6-245">Azure offers several compute services that do not have direct equivalents in AWS:</span></span>

-   <span data-ttu-id="ecda6-246">[Azure Batch](https://azure.microsoft.com/documentation/articles/batch-technical-overview/) - 可讓您在虛擬機器的擴充集合之間管理大量的計算工作。</span><span class="sxs-lookup"><span data-stu-id="ecda6-246">[Azure Batch](https://azure.microsoft.com/documentation/articles/batch-technical-overview/) - allows you to manage compute-intensive work across a scalable collection of virtual machines.</span></span>

-   <span data-ttu-id="ecda6-247">[Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-overview/) - 開發及主控可擴充的[微服務](https://azure.microsoft.com/documentation/articles/service-fabric-overview-microservices/)解決方案平台。</span><span class="sxs-lookup"><span data-stu-id="ecda6-247">[Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-overview/) - platform for developing and hosting scalable [microservice](https://azure.microsoft.com/documentation/articles/service-fabric-overview-microservices/) solutions.</span></span>

#### <a name="see-also"></a><span data-ttu-id="ecda6-248">另請參閱</span><span class="sxs-lookup"><span data-stu-id="ecda6-248">See also</span></span>

-   [<span data-ttu-id="ecda6-249">使用入口網站在 Azure 上建立 Linux VM</span><span class="sxs-lookup"><span data-stu-id="ecda6-249">Create a Linux VM on Azure using the Portal</span></span>](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-quick-create-portal/)

-   [<span data-ttu-id="ecda6-250">Azure 參考架構：在 Azure 上執行 Linux VM</span><span class="sxs-lookup"><span data-stu-id="ecda6-250">Azure Reference Architecture: Running a Linux VM on Azure</span></span>](https://azure.microsoft.com/documentation/articles/guidance-compute-single-vm-linux/)

-   [<span data-ttu-id="ecda6-251">在 Azure App Service 中開始使用 Node.js Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="ecda6-251">Get started with Node.js web apps in Azure App Service</span></span>](https://azure.microsoft.com/documentation/articles/app-service-web-nodejs-get-started/)

-   [<span data-ttu-id="ecda6-252">Azure 參考架構︰基本 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="ecda6-252">Azure Reference Architecture: Basic web application</span></span>](https://azure.microsoft.com/documentation/articles/guidance-web-apps-basic/)

-   [<span data-ttu-id="ecda6-253">建立您的第一個 Azure 函式</span><span class="sxs-lookup"><span data-stu-id="ecda6-253">Create your first Azure Function</span></span>](https://azure.microsoft.com/documentation/articles/functions-create-first-azure-function/)

### <a name="storage"></a><span data-ttu-id="ecda6-254">儲存體</span><span class="sxs-lookup"><span data-stu-id="ecda6-254">Storage</span></span>

#### <a name="s3ebsefs-and-azure-storage"></a><span data-ttu-id="ecda6-255">S3/EBS/EFS 與 Azure 儲存體</span><span class="sxs-lookup"><span data-stu-id="ecda6-255">S3/EBS/EFS and Azure Storage</span></span>

<span data-ttu-id="ecda6-256">在 AWS 平台中，雲端儲存體主要分為三種服務：</span><span class="sxs-lookup"><span data-stu-id="ecda6-256">In the AWS platform, cloud storage is primarily broken down into three services:</span></span>

-   <span data-ttu-id="ecda6-257">**Simple Storage Service (S3)** - 基本物件儲存體。</span><span class="sxs-lookup"><span data-stu-id="ecda6-257">**Simple Storage Service (S3)** - basic object storage.</span></span> <span data-ttu-id="ecda6-258">讓資料可透過網際網路存取的 API 使用。</span><span class="sxs-lookup"><span data-stu-id="ecda6-258">Makes data available through an Internet accessible API.</span></span>

-   <span data-ttu-id="ecda6-259">**Elastic Block Storage (EBS)** - 區塊層級儲存體，可供單一 VM 存取。</span><span class="sxs-lookup"><span data-stu-id="ecda6-259">**Elastic Block Storage (EBS)** - block level storage, intended for access by a single VM.</span></span>

-   <span data-ttu-id="ecda6-260">**Elastic File System (EFS)** - 適用於最多上千個 EC2 執行個體作為共用存放裝置使用的檔案儲存體。</span><span class="sxs-lookup"><span data-stu-id="ecda6-260">**Elastic File System (EFS)** - file storage meant for use as shared storage for up to thousands of EC2 instances.</span></span>

<span data-ttu-id="ecda6-261">在 Azure 儲存體中，訂用帳戶繫結的[儲存體帳戶](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/)可讓您建立及管理下列儲存體服務：</span><span class="sxs-lookup"><span data-stu-id="ecda6-261">In Azure Storage, subscription-bound [storage accounts](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/) allow you to create and manage the following storage services:</span></span>

-   <span data-ttu-id="ecda6-262">[Blob 儲存體](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/) - 儲存任何類型的文字或二進位資料，例如文件、媒體檔案或應用程式安裝程式。</span><span class="sxs-lookup"><span data-stu-id="ecda6-262">[Blob storage](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/) - stores any type of text or binary data, such as a document, media file, or application installer.</span></span> <span data-ttu-id="ecda6-263">您可以設定私人存取的 Blob 儲存體或公開共用內容到網際網路。</span><span class="sxs-lookup"><span data-stu-id="ecda6-263">You can set Blob storage for private access or share contents publicly to the Internet.</span></span> <span data-ttu-id="ecda6-264">Blob 儲存體與 AWS S3 和 EBS 有相同的用途。</span><span class="sxs-lookup"><span data-stu-id="ecda6-264">Blob storage serves the same purpose as both AWS S3 and EBS.</span></span>
-   <span data-ttu-id="ecda6-265">[表格儲存體](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-table-storage/) - 可儲存結構化資料集。</span><span class="sxs-lookup"><span data-stu-id="ecda6-265">[Table storage](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-table-storage/) - stores structured datasets.</span></span> <span data-ttu-id="ecda6-266">表格儲存體屬於 NoSQL 索引鍵屬性資料儲存，可允許快速開發和迅速存取大量資料。</span><span class="sxs-lookup"><span data-stu-id="ecda6-266">Table storage is a NoSQL key-attribute data store that allows for rapid development and fast access to large quantities of data.</span></span> <span data-ttu-id="ecda6-267">類似於 AWS 的 SimpleDB 和 DynamoDB 服務。</span><span class="sxs-lookup"><span data-stu-id="ecda6-267">Similar to AWS' SimpleDB and DynamoDB services.</span></span>

-   <span data-ttu-id="ecda6-268">[佇列儲存體](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-queues/) - 可為工作流程處理及雲端服務元件間的通訊，提供訊息服務。</span><span class="sxs-lookup"><span data-stu-id="ecda6-268">[Queue storage](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-queues/) - provides messaging for workflow processing and for communication between components of cloud services.</span></span>

-   <span data-ttu-id="ecda6-269">[檔案儲存體](https://azure.microsoft.com/documentation/articles/storage-java-how-to-use-file-storage/) - 標準使用伺服器訊息區 (SMB) 通訊協定為繼承應用程式提供共用儲存體。</span><span class="sxs-lookup"><span data-stu-id="ecda6-269">[File storage](https://azure.microsoft.com/documentation/articles/storage-java-how-to-use-file-storage/) - offers shared storage for legacy applications using the standard server message block (SMB) protocol.</span></span> <span data-ttu-id="ecda6-270">檔案儲存體會以類似的方式在 AWS 平台用於 EFS。</span><span class="sxs-lookup"><span data-stu-id="ecda6-270">File storage is used in a similar manner to EFS in the AWS platform.</span></span>
 
#### <a name="glacier-and-azure-storage"></a><span data-ttu-id="ecda6-271">Glacier 與 Azure 儲存體</span><span class="sxs-lookup"><span data-stu-id="ecda6-271">Glacier and Azure Storage</span></span> 

<span data-ttu-id="ecda6-272">[Azure 封存 Blob 儲存體](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier)相當於 AWS Glacier 儲存體服務。</span><span class="sxs-lookup"><span data-stu-id="ecda6-272">[Azure Archive Blob Storage](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier) is comparable to AWS Glacier storage service.</span></span> <span data-ttu-id="ecda6-273">它適用於不太會存取的資料，且該資料已儲存至少 180 天且可以容忍數小時的擷取延遲。</span><span class="sxs-lookup"><span data-stu-id="ecda6-273">It is intended for rarely accessed data that is stored for at least 180 days and can tolerate several hours of retrieval latency.</span></span> 

<span data-ttu-id="ecda6-274">針對不常存取、卻又必須可供立即存取的資料，[Azure 非經常性 Blob 儲存層](/azure/storage/blobs/storage-blob-storage-tiers#cool-access-tier)可提供比標準 Blob 儲存體還要便宜的儲存體。</span><span class="sxs-lookup"><span data-stu-id="ecda6-274">For data that is infrequently accessed but must be available immediately when accessed, [Azure Cool Blob Storage tier](/azure/storage/blobs/storage-blob-storage-tiers#cool-access-tier) provides cheaper storage than standard blob storage.</span></span> <span data-ttu-id="ecda6-275">此儲存層相當於 AWS S3 - 不常存取儲存體服務。</span><span class="sxs-lookup"><span data-stu-id="ecda6-275">This storage tier is comparable to AWS S3 - Infrequent Access storage service.</span></span>

#### <a name="see-also"></a><span data-ttu-id="ecda6-276">另請參閱</span><span class="sxs-lookup"><span data-stu-id="ecda6-276">See also</span></span>

-   [<span data-ttu-id="ecda6-277">Microsoft Azure 儲存體效能與延展性檢查清單</span><span class="sxs-lookup"><span data-stu-id="ecda6-277">Microsoft Azure Storage Performance and Scalability Checklist</span></span>](https://azure.microsoft.com/documentation/articles/storage-performance-checklist/)

-   [<span data-ttu-id="ecda6-278">Azure 儲存體安全性指南</span><span class="sxs-lookup"><span data-stu-id="ecda6-278">Azure Storage security guide</span></span>](https://azure.microsoft.com/documentation/articles/storage-security-guide/)

-   [<span data-ttu-id="ecda6-279">模式與做法：內容傳遞網路 (CDN) 指引</span><span class="sxs-lookup"><span data-stu-id="ecda6-279">Patterns & Practices: Content Delivery Network (CDN) guidance</span></span>](https://azure.microsoft.com/documentation/articles/best-practices-cdn/)

### <a name="networking"></a><span data-ttu-id="ecda6-280">網路</span><span class="sxs-lookup"><span data-stu-id="ecda6-280">Networking</span></span>

#### <a name="elastic-load-balancing-azure-load-balancer-and-azure-application-gateway"></a><span data-ttu-id="ecda6-281">彈性負載平衡、Azure Load Balancer 以及 Azure 應用程式閘道</span><span class="sxs-lookup"><span data-stu-id="ecda6-281">Elastic Load Balancing, Azure Load Balancer, and Azure Application Gateway</span></span>

<span data-ttu-id="ecda6-282">這兩項彈性負載平衡服務的 Azure 對等項目為：</span><span class="sxs-lookup"><span data-stu-id="ecda6-282">The Azure equivalents of the two Elastic Load Balancing services are:</span></span>

-   <span data-ttu-id="ecda6-283">[負載平衡器](https://azure.microsoft.com/documentation/articles/load-balancer-overview/) - 提供與 AWS 傳統 Load Balancer 相同的功能，可讓您在網路層級散發多個 VM 的流量。</span><span class="sxs-lookup"><span data-stu-id="ecda6-283">[Load Balancer](https://azure.microsoft.com/documentation/articles/load-balancer-overview/) - provides the same capabilities as the AWS Classic Load Balancer, allowing you to distribute traffic for multiple VMs at the network level.</span></span> <span data-ttu-id="ecda6-284">它也提供容錯移轉功能。</span><span class="sxs-lookup"><span data-stu-id="ecda6-284">It also provides failover capability.</span></span>

-   <span data-ttu-id="ecda6-285">[應用程式閘道](https://azure.microsoft.com/documentation/articles/application-gateway-introduction/) - 提供可比較 AWS 應用程式 Load Balancer 的應用程式層級規則型路由。</span><span class="sxs-lookup"><span data-stu-id="ecda6-285">[Application Gateway](https://azure.microsoft.com/documentation/articles/application-gateway-introduction/) - offers application-level rule-based routing comparable to the AWS Application Load Balancer.</span></span>

#### <a name="route-53-azure-dns-and-azure-traffic-manager"></a><span data-ttu-id="ecda6-286">Route 53、Azure DNS 和 Azure 流量管理員</span><span class="sxs-lookup"><span data-stu-id="ecda6-286">Route 53, Azure DNS, and Azure Traffic Manager</span></span>

<span data-ttu-id="ecda6-287">在 AWS 中，Route 53 會提供 DNS 名稱管理和 DNS 層級流量路由與容錯移轉服務。</span><span class="sxs-lookup"><span data-stu-id="ecda6-287">In AWS, Route 53 provides both DNS name management and DNS-level traffic routing and failover services.</span></span> <span data-ttu-id="ecda6-288">在 Azure 中，這會透過兩個服務進行處理：</span><span class="sxs-lookup"><span data-stu-id="ecda6-288">In Azure this is handled through two services:</span></span>

-   <span data-ttu-id="ecda6-289">[Azure DNS](https://azure.microsoft.com/documentation/services/dns/) - 提供網域和 DNS 管理。</span><span class="sxs-lookup"><span data-stu-id="ecda6-289">[Azure DNS](https://azure.microsoft.com/documentation/services/dns/) - provides domain and DNS management.</span></span>

-   <span data-ttu-id="ecda6-290">[流量管理員](https://azure.microsoft.com/documentation/articles/traffic-manager-overview/) - 提供 DNS 層級流量路由、負載平衡和容錯移轉功能。</span><span class="sxs-lookup"><span data-stu-id="ecda6-290">[Traffic Manager](https://azure.microsoft.com/documentation/articles/traffic-manager-overview/) - provides DNS level traffic routing, load balancing, and failover capabilities.</span></span>

#### <a name="direct-connect-and-azure-expressroute"></a><span data-ttu-id="ecda6-291">Direct Connect 與 Azure ExpressRoute</span><span class="sxs-lookup"><span data-stu-id="ecda6-291">Direct Connect and Azure ExpressRoute</span></span>

<span data-ttu-id="ecda6-292">Azure 會透過其 [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) 服務提供類似的站台對站台專用連線。</span><span class="sxs-lookup"><span data-stu-id="ecda6-292">Azure provides similar site-to-site dedicated connections through its [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) service.</span></span> <span data-ttu-id="ecda6-293">ExpressRoute 可讓您使用專用私人網路連線，將本機網路直接連線到 Azure 資源。</span><span class="sxs-lookup"><span data-stu-id="ecda6-293">ExpressRoute allows you to connect your local network directly to Azure resources using a dedicated private network connection.</span></span> <span data-ttu-id="ecda6-294">Azure 還以較低的成本提供更多傳統[站對站 VPN 連線](https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-site-to-site-resource-manager-portal/)。</span><span class="sxs-lookup"><span data-stu-id="ecda6-294">Azure also offers more conventional [site-to-site VPN connections](https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-site-to-site-resource-manager-portal/) at a lower cost.</span></span>

#### <a name="see-also"></a><span data-ttu-id="ecda6-295">另請參閱</span><span class="sxs-lookup"><span data-stu-id="ecda6-295">See also</span></span>

-   [<span data-ttu-id="ecda6-296">使用 Azure 入口網站建立虛擬網路</span><span class="sxs-lookup"><span data-stu-id="ecda6-296">Create a virtual network using the Azure portal</span></span>](https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/)

-   [<span data-ttu-id="ecda6-297">規劃和設計 Azure 虛擬網路</span><span class="sxs-lookup"><span data-stu-id="ecda6-297">Plan and design Azure Virtual Networks</span></span>](https://azure.microsoft.com/documentation/articles/virtual-network-vnet-plan-design-arm/)

-   [<span data-ttu-id="ecda6-298">Azure 網路安全性最佳做法</span><span class="sxs-lookup"><span data-stu-id="ecda6-298">Azure Network Security Best Practices</span></span>](https://azure.microsoft.com/documentation/articles/azure-security-network-security-best-practices/)

### <a name="database-services"></a><span data-ttu-id="ecda6-299">資料庫服務</span><span class="sxs-lookup"><span data-stu-id="ecda6-299">Database services</span></span>

#### <a name="rds-and-azure-relational-database-services"></a><span data-ttu-id="ecda6-300">RDS 和 Azure 關聯式資料庫服務</span><span class="sxs-lookup"><span data-stu-id="ecda6-300">RDS and Azure relational database services</span></span>

<span data-ttu-id="ecda6-301">Azure 會提供與 AWS 關聯式資料庫服務 (RDS) 對等的幾種不同關聯式資料庫服務。</span><span class="sxs-lookup"><span data-stu-id="ecda6-301">Azure provides several different relational database services that are the equivalent of AWS' Relational Database Service (RDS).</span></span>

-   [<span data-ttu-id="ecda6-302">SQL Database</span><span class="sxs-lookup"><span data-stu-id="ecda6-302">SQL Database</span></span>](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)
-   [<span data-ttu-id="ecda6-303">適用於 MySQL 的 Azure 資料庫</span><span class="sxs-lookup"><span data-stu-id="ecda6-303">Azure Database for MySQL</span></span>](https://docs.microsoft.com/azure/mysql/overview)
-   [<span data-ttu-id="ecda6-304">適用於 PostgreSQL 的 Azure 資料庫</span><span class="sxs-lookup"><span data-stu-id="ecda6-304">Azure Database for PostgreSQL</span></span>](https://docs.microsoft.com/azure/postgresql/overview)

<span data-ttu-id="ecda6-305">可以使用 Azure VM 執行個體部署諸如 [SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)、[Oracle](https://azure.microsoft.com/campaigns/oracle/) 和 [MySQL](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-classic-mysql-2008r2/) 等其他資料庫引擎。</span><span class="sxs-lookup"><span data-stu-id="ecda6-305">Other database engines such as [SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/), [Oracle](https://azure.microsoft.com/campaigns/oracle/), and [MySQL](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-classic-mysql-2008r2/) can be deployed using Azure VM Instances.</span></span>

<span data-ttu-id="ecda6-306">AWS RDS 的成本取決於您執行個體所使用的硬體資源，例如 CPU、RAM、儲存體和網路頻寬。</span><span class="sxs-lookup"><span data-stu-id="ecda6-306">Costs for AWS RDS are determined by the amount of hardware resources that your instance uses, like CPU, RAM, storage, and network bandwidth.</span></span> <span data-ttu-id="ecda6-307">在 Azure 資料庫服務中，成本取決於您的資料庫大小、並行連線及輸送量層級。</span><span class="sxs-lookup"><span data-stu-id="ecda6-307">In the Azure database services, cost depends on your database size, concurrent connections, and throughput levels.</span></span>

#### <a name="see-also"></a><span data-ttu-id="ecda6-308">另請參閱</span><span class="sxs-lookup"><span data-stu-id="ecda6-308">See also</span></span>

-   [<span data-ttu-id="ecda6-309">Azure SQL Database 教學課程</span><span class="sxs-lookup"><span data-stu-id="ecda6-309">Azure SQL Database Tutorials</span></span>](https://azure.microsoft.com/documentation/articles/sql-database-explore-tutorials/)

-   [<span data-ttu-id="ecda6-310">使用 Azure 入口網站為 Azure SQL Database 設定異地複寫</span><span class="sxs-lookup"><span data-stu-id="ecda6-310">Configure geo-replication for Azure SQL Database with the Azure portal</span></span>](https://azure.microsoft.com/documentation/articles/sql-database-geo-replication-portal/)

-   [<span data-ttu-id="ecda6-311">Cosmos DB 簡介：NoSQL JSON 資料庫</span><span class="sxs-lookup"><span data-stu-id="ecda6-311">Introduction to Cosmos DB: A NoSQL JSON Database</span></span>](https://azure.microsoft.com/documentation/articles/documentdb-introduction/)

-   [<span data-ttu-id="ecda6-312">如何從 Node.js 使用 Azure 資料表儲存體</span><span class="sxs-lookup"><span data-stu-id="ecda6-312">How to use Azure Table storage from Node.js</span></span>](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-table-storage/)

### <a name="security-and-identity"></a><span data-ttu-id="ecda6-313">安全性與身分識別</span><span class="sxs-lookup"><span data-stu-id="ecda6-313">Security and identity</span></span>

#### <a name="directory-service-and-azure-active-directory"></a><span data-ttu-id="ecda6-314">目錄服務和 Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="ecda6-314">Directory service and Azure Active Directory</span></span>

<span data-ttu-id="ecda6-315">Azure 會將目錄服務分割成下列供應項目：</span><span class="sxs-lookup"><span data-stu-id="ecda6-315">Azure splits up directory services into the following offerings:</span></span>

-   <span data-ttu-id="ecda6-316">[Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-whatis/) - 以雲端作為基礎的目錄和身分識別管理服務。</span><span class="sxs-lookup"><span data-stu-id="ecda6-316">[Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-whatis/) - cloud based directory and identity management service.</span></span>

-   <span data-ttu-id="ecda6-317">[Azure Active Directory B2B](https://azure.microsoft.com/documentation/articles/active-directory-b2b-collaboration-overview/) - 允許以合作夥伴管理的身分識別來存取您的公司應用程式。</span><span class="sxs-lookup"><span data-stu-id="ecda6-317">[Azure Active Directory B2B](https://azure.microsoft.com/documentation/articles/active-directory-b2b-collaboration-overview/) - enables access to your corporate applications from partner-managed identities.</span></span>

-   <span data-ttu-id="ecda6-318">[Azure Active Directory B2C](https://azure.microsoft.com/documentation/articles/active-directory-b2c-overview/) - 支援取用者面相應用程式的單一登入和使用者管理的服務供應項目。</span><span class="sxs-lookup"><span data-stu-id="ecda6-318">[Azure Active Directory B2C](https://azure.microsoft.com/documentation/articles/active-directory-b2c-overview/) - service offering support for single sign-on and user management for consumer facing applications.</span></span>

-   <span data-ttu-id="ecda6-319">[Azure Active Directory Domain Services](https://azure.microsoft.com/documentation/articles/active-directory-ds-overview/) - 主控網域控制站服務，可允許 Active Directory 相容網域加入和使用者管理功能。</span><span class="sxs-lookup"><span data-stu-id="ecda6-319">[Azure Active Directory Domain Services](https://azure.microsoft.com/documentation/articles/active-directory-ds-overview/) - hosted domain controller service, allowing Active Directory compatible domain join and user management functionality.</span></span>

#### <a name="web-application-firewall"></a><span data-ttu-id="ecda6-320">Web 應用程式防火牆</span><span class="sxs-lookup"><span data-stu-id="ecda6-320">Web application firewall</span></span>

<span data-ttu-id="ecda6-321">除了[應用程式閘道 Web 應用程式防火牆](https://azure.microsoft.com/documentation/articles/application-gateway-webapplicationfirewall-overview/)之外，您也可以[使用第三方廠商提供的 web 應用程式防火牆](https://azure.microsoft.com/documentation/articles/application-gateway-webapplicationfirewall-overview/)，像是 [Barracuda Networks](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf/)。</span><span class="sxs-lookup"><span data-stu-id="ecda6-321">In addition to the [Application Gateway Web Application Firewall](https://azure.microsoft.com/documentation/articles/application-gateway-webapplicationfirewall-overview/), you can also [use web application firewalls](https://azure.microsoft.com/documentation/articles/application-gateway-webapplicationfirewall-overview/) from third-party vendors like [Barracuda Networks](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf/).</span></span>

#### <a name="see-also"></a><span data-ttu-id="ecda6-322">另請參閱</span><span class="sxs-lookup"><span data-stu-id="ecda6-322">See also</span></span>

-   [<span data-ttu-id="ecda6-323">開始使用 Microsoft Azure 安全性</span><span class="sxs-lookup"><span data-stu-id="ecda6-323">Getting started with Microsoft Azure security</span></span>](https://azure.microsoft.com/documentation/articles/azure-security-getting-started/)

-   [<span data-ttu-id="ecda6-324">Azure 身分識別管理和存取控制安全性最佳作法</span><span class="sxs-lookup"><span data-stu-id="ecda6-324">Azure Identity Management and access control security best practices</span></span>](https://azure.microsoft.com/documentation/articles/azure-security-identity-management-best-practices/)

### <a name="application-and-messaging-services"></a><span data-ttu-id="ecda6-325">應用程式及傳訊服務</span><span class="sxs-lookup"><span data-stu-id="ecda6-325">Application and messaging services</span></span>

#### <a name="simple-email-service"></a><span data-ttu-id="ecda6-326">Simple Email Service</span><span class="sxs-lookup"><span data-stu-id="ecda6-326">Simple Email Service</span></span>

<span data-ttu-id="ecda6-327">AWS 提供 Simple Email Service (SES) 來傳送通知、交易式或行銷電子郵件。</span><span class="sxs-lookup"><span data-stu-id="ecda6-327">AWS provides the Simple Email Service (SES) for sending notification, transactional, or marketing emails.</span></span> <span data-ttu-id="ecda6-328">在 Azure 中，第三方解決方案 (例如 [Sendgrid](https://sendgrid.com/partners/azure/)) 會提供電子郵件服務。</span><span class="sxs-lookup"><span data-stu-id="ecda6-328">In Azure, third-party solutions like [Sendgrid](https://sendgrid.com/partners/azure/) provide email services.</span></span>

#### <a name="simple-queueing-service"></a><span data-ttu-id="ecda6-329">Simple Queueing Service</span><span class="sxs-lookup"><span data-stu-id="ecda6-329">Simple Queueing Service</span></span>

<span data-ttu-id="ecda6-330">AWS Simple Queueing Service (SQS) 會提供傳訊系統，可連線 AWS 平台內的應用程式、服務和裝置。</span><span class="sxs-lookup"><span data-stu-id="ecda6-330">AWS Simple Queueing Service (SQS) provides a messaging system for connecting applications, services, and devices within the AWS platform.</span></span> <span data-ttu-id="ecda6-331">Azure 有兩項服務，可提供類似的功能：</span><span class="sxs-lookup"><span data-stu-id="ecda6-331">Azure has two services that provide similar functionality:</span></span>

-   <span data-ttu-id="ecda6-332">[佇列儲存體](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-queues/) - 雲端傳訊服務，可在 Azure 平台內的應用程式元件之間進行通訊。</span><span class="sxs-lookup"><span data-stu-id="ecda6-332">[Queue storage](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-queues/) - a cloud messaging service that allows communication between application components within the Azure platform.</span></span>

-   <span data-ttu-id="ecda6-333">[服務匯流排](https://azure.microsoft.com/en-us/services/service-bus/) - 更強固的傳訊系統，可連線應用程式、服務和裝置。</span><span class="sxs-lookup"><span data-stu-id="ecda6-333">[Service Bus](https://azure.microsoft.com/en-us/services/service-bus/) - a more robust messaging system for connecting applications, services, and devices.</span></span> <span data-ttu-id="ecda6-334">服務匯流排會使用相關的[服務匯流排轉送](https://docs.microsoft.com/en-us/azure/service-bus-relay/relay-what-is-it)，也可以連線到遠端主控應用程式和服務。</span><span class="sxs-lookup"><span data-stu-id="ecda6-334">Using the related [Service Bus relay](https://docs.microsoft.com/en-us/azure/service-bus-relay/relay-what-is-it), Service Bus can also connect to remotely hosted applications and services.</span></span>

#### <a name="device-farm"></a><span data-ttu-id="ecda6-335">裝置伺服器陣列</span><span class="sxs-lookup"><span data-stu-id="ecda6-335">Device Farm</span></span>

<span data-ttu-id="ecda6-336">AWS 裝置伺服陣列會提供跨裝置測試服務。</span><span class="sxs-lookup"><span data-stu-id="ecda6-336">The AWS Device Farm provides cross-device testing services.</span></span> <span data-ttu-id="ecda6-337">在 Azure 中，[Xamarin Test Cloud](https://www.xamarin.com/test-cloud) 會提供行動裝置的類似跨裝置前端測試。</span><span class="sxs-lookup"><span data-stu-id="ecda6-337">In Azure, [Xamarin Test Cloud](https://www.xamarin.com/test-cloud) provides similar cross-device front-end testing for mobile devices.</span></span>

<span data-ttu-id="ecda6-338">除了前端測試之外，[Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) 還提供 Linux 和 Windows 環境的後端測試資源。</span><span class="sxs-lookup"><span data-stu-id="ecda6-338">In addition to front-end testing, the [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) provides back end testing resources for Linux and Windows environments.</span></span>

#### <a name="see-also"></a><span data-ttu-id="ecda6-339">另請參閱</span><span class="sxs-lookup"><span data-stu-id="ecda6-339">See also</span></span>

-   [<span data-ttu-id="ecda6-340">如何使用 Node.js 的佇列儲存體</span><span class="sxs-lookup"><span data-stu-id="ecda6-340">How to use Queue storage from Node.js</span></span>](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-queues/)

-   [<span data-ttu-id="ecda6-341">如何使用服務匯流排佇列</span><span class="sxs-lookup"><span data-stu-id="ecda6-341">How to use Service Bus queues</span></span>](https://azure.microsoft.com/documentation/articles/service-bus-nodejs-how-to-use-queues/)

### <a name="analytics-and-big-data"></a><span data-ttu-id="ecda6-342">分析與巨量資料</span><span class="sxs-lookup"><span data-stu-id="ecda6-342">Analytics and big data</span></span>

<span data-ttu-id="ecda6-343">[Cortana Intelligence Suite](https://azure.microsoft.com/suites/cortana-intelligence-suite/) 是 Azure 的產品和服務套件，專為擷取、組織、分析和視覺化大量資料而設計。</span><span class="sxs-lookup"><span data-stu-id="ecda6-343">[The Cortana Intelligence Suite](https://azure.microsoft.com/suites/cortana-intelligence-suite/) is Azure's package of products and services designed to capture, organize, analyze, and visualize large amounts of data.</span></span> <span data-ttu-id="ecda6-344">Cortana 套件包含下列服務：</span><span class="sxs-lookup"><span data-stu-id="ecda6-344">The Cortana suite consists of the following services:</span></span>

-   <span data-ttu-id="ecda6-345">
            [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) - 受控 Apache 分佈，其中包括 Hadoop、Spark、Storm 或 HBase。</span><span class="sxs-lookup"><span data-stu-id="ecda6-345">[HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) - managed Apache distribution that includes Hadoop, Spark, Storm, or HBase.</span></span>

-   <span data-ttu-id="ecda6-346">[Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) - 提供資料協調流程和 Data Pipeline 功能。</span><span class="sxs-lookup"><span data-stu-id="ecda6-346">[Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) - provides data orchestration and data pipeline functionality.</span></span>

-   <span data-ttu-id="ecda6-347">[SQL 資料倉儲](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) - 大規模關聯式資料存放區。</span><span class="sxs-lookup"><span data-stu-id="ecda6-347">[SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) - large-scale relational data storage.</span></span>

-   <span data-ttu-id="ecda6-348">[Data Lake Store](https://azure.microsoft.com/documentation/services/data-lake-store/) - 針對巨量資料分析工作負載最佳化的大規模儲存體。</span><span class="sxs-lookup"><span data-stu-id="ecda6-348">[Data Lake Store](https://azure.microsoft.com/documentation/services/data-lake-store/) - large-scale storage optimized for big data analytics workloads.</span></span>

-   <span data-ttu-id="ecda6-349">[Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) - 用來建置及套用關於資料的預測性分析。</span><span class="sxs-lookup"><span data-stu-id="ecda6-349">[Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) - used to build and apply predictive analytics on data.</span></span>

-   <span data-ttu-id="ecda6-350">[串流分析](https://azure.microsoft.com/documentation/services/stream-analytics/) - 即時資料分析。</span><span class="sxs-lookup"><span data-stu-id="ecda6-350">[Stream Analytics](https://azure.microsoft.com/documentation/services/stream-analytics/) - real-time data analysis.</span></span>

-   <span data-ttu-id="ecda6-351">[Data Lake Analytics](https://azure.microsoft.com/documentation/articles/data-lake-analytics-overview/) - 針對使用 Data Lake Store 進行最佳化的大規模分析服務</span><span class="sxs-lookup"><span data-stu-id="ecda6-351">[Data Lake Analytics](https://azure.microsoft.com/documentation/articles/data-lake-analytics-overview/) - large-scale analytics service optimized to work with Data Lake Store</span></span>

-   <span data-ttu-id="ecda6-352">[PowerBI](https://powerbi.microsoft.com/) - 用來提供資料視覺效果。</span><span class="sxs-lookup"><span data-stu-id="ecda6-352">[PowerBI](https://powerbi.microsoft.com/) - used to power data visualization.</span></span>

#### <a name="see-also"></a><span data-ttu-id="ecda6-353">另請參閱</span><span class="sxs-lookup"><span data-stu-id="ecda6-353">See also</span></span>

-   [<span data-ttu-id="ecda6-354">Cortana Intelligence Gallery</span><span class="sxs-lookup"><span data-stu-id="ecda6-354">Cortana Intelligence Gallery</span></span>](https://gallery.cortanaintelligence.com/)

-   [<span data-ttu-id="ecda6-355">了解 Microsoft 巨量資料解決方案</span><span class="sxs-lookup"><span data-stu-id="ecda6-355">Understanding Microsoft big data solutions</span></span>](https://msdn.microsoft.com/library/dn749804.aspx)

-   [<span data-ttu-id="ecda6-356">Azure Data Lake 與 Azure HDInsight 部落格</span><span class="sxs-lookup"><span data-stu-id="ecda6-356">Azure Data Lake & Azure HDInsight Blog</span></span>](https://blogs.msdn.microsoft.com/azuredatalake/)

### <a name="internet-of-things"></a><span data-ttu-id="ecda6-357">物聯網</span><span class="sxs-lookup"><span data-stu-id="ecda6-357">Internet of Things</span></span>

#### <a name="see-also"></a><span data-ttu-id="ecda6-358">另請參閱</span><span class="sxs-lookup"><span data-stu-id="ecda6-358">See also</span></span>

-   [<span data-ttu-id="ecda6-359">開始使用 Azure IoT 中樞</span><span class="sxs-lookup"><span data-stu-id="ecda6-359">Get started with Azure IoT Hub</span></span>](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/)

-   [<span data-ttu-id="ecda6-360">IoT 中樞和事件中樞的比較</span><span class="sxs-lookup"><span data-stu-id="ecda6-360">Comparison of IoT Hub and Event Hubs</span></span>](https://azure.microsoft.com/documentation/articles/iot-hub-compare-event-hubs/)

### <a name="mobile-services"></a><span data-ttu-id="ecda6-361">行動服務</span><span class="sxs-lookup"><span data-stu-id="ecda6-361">Mobile services</span></span>

#### <a name="notifications"></a><span data-ttu-id="ecda6-362">通知</span><span class="sxs-lookup"><span data-stu-id="ecda6-362">Notifications</span></span>

<span data-ttu-id="ecda6-363">通知中樞不支援傳送簡訊或電子郵件訊息，因此需要這些傳遞類型的第三方服務。</span><span class="sxs-lookup"><span data-stu-id="ecda6-363">Notification Hubs do not support sending SMS or email messages, so third-party services are needed for those delivery types.</span></span>

#### <a name="see-also"></a><span data-ttu-id="ecda6-364">另請參閱</span><span class="sxs-lookup"><span data-stu-id="ecda6-364">See also</span></span>

-   [<span data-ttu-id="ecda6-365">建立 Android 應用程式</span><span class="sxs-lookup"><span data-stu-id="ecda6-365">Create an Android app</span></span>](https://azure.microsoft.com/documentation/articles/app-service-mobile-android-get-started/)

-   [<span data-ttu-id="ecda6-366">Azure Mobile Apps 中的驗證與授權</span><span class="sxs-lookup"><span data-stu-id="ecda6-366">Authentication and Authorization in Azure Mobile Apps</span></span>](https://azure.microsoft.com/documentation/articles/app-service-mobile-auth/)

-   [<span data-ttu-id="ecda6-367">使用 Azure 通知中樞傳送推播通知</span><span class="sxs-lookup"><span data-stu-id="ecda6-367">Sending push notifications with Azure Notification Hubs</span></span>](https://azure.microsoft.com/documentation/articles/notification-hubs-android-push-notification-google-fcm-get-started/)

### <a name="management-and-monitoring"></a><span data-ttu-id="ecda6-368">管理與監視</span><span class="sxs-lookup"><span data-stu-id="ecda6-368">Management and monitoring</span></span>

#### <a name="see-also"></a><span data-ttu-id="ecda6-369">另請參閱</span><span class="sxs-lookup"><span data-stu-id="ecda6-369">See also</span></span>
-   [<span data-ttu-id="ecda6-370">監視和診斷指引</span><span class="sxs-lookup"><span data-stu-id="ecda6-370">Monitoring and diagnostics guidance</span></span>](https://azure.microsoft.com/documentation/articles/best-practices-monitoring/)

-   [<span data-ttu-id="ecda6-371">建立 Azure Resource Manager 範本的最佳做法</span><span class="sxs-lookup"><span data-stu-id="ecda6-371">Best practices for creating Azure Resource Manager templates</span></span>](https://azure.microsoft.com/documentation/articles/resource-manager-template-best-practices/)

-   [<span data-ttu-id="ecda6-372">Azure Resource Manager 快速入門範本</span><span class="sxs-lookup"><span data-stu-id="ecda6-372">Azure Resource Manager Quickstart templates</span></span>](https://azure.microsoft.com/documentation/templates/)


## <a name="next-steps"></a><span data-ttu-id="ecda6-373">後續步驟</span><span class="sxs-lookup"><span data-stu-id="ecda6-373">Next steps</span></span>

-   [<span data-ttu-id="ecda6-374">完成 AWS 和 Azure 服務比較矩陣</span><span class="sxs-lookup"><span data-stu-id="ecda6-374">Complete AWS and Azure service comparison matrix</span></span>](https://aka.ms/azure4aws-services)

-   [<span data-ttu-id="ecda6-375">互動式 Azure 平台概略圖</span><span class="sxs-lookup"><span data-stu-id="ecda6-375">Interactive Azure Platform Big Picture</span></span>](http://azureplatform.azurewebsites.net/)

-   [<span data-ttu-id="ecda6-376">開始使用 Azure</span><span class="sxs-lookup"><span data-stu-id="ecda6-376">Get started with Azure</span></span>](https://azure.microsoft.com/get-started/)

-   [<span data-ttu-id="ecda6-377">Azure 解決方案架構</span><span class="sxs-lookup"><span data-stu-id="ecda6-377">Azure solution architectures</span></span>](https://azure.microsoft.com/solutions/architecture/)

-   [<span data-ttu-id="ecda6-378">Azure 參考架構</span><span class="sxs-lookup"><span data-stu-id="ecda6-378">Azure Reference Architectures</span></span>](https://azure.microsoft.com/documentation/articles/guidance-architecture/)

-   [<span data-ttu-id="ecda6-379">模式與做法：Azure 指導方針</span><span class="sxs-lookup"><span data-stu-id="ecda6-379">Patterns & Practices: Azure Guidance</span></span>](https://azure.microsoft.com/documentation/articles/guidance/)

-   [<span data-ttu-id="ecda6-380">免費線上課程：AWS 專家適用的 Microsoft Azure</span><span class="sxs-lookup"><span data-stu-id="ecda6-380">Free Online Course: Microsoft Azure for AWS Experts</span></span>](http://aka.ms/azureforaws)

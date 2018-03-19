---
title: "擴充 Azure Resource Manager 範本功能"
description: "說明如何擴充 Azure Resource Manager 範本功能的秘訣與技巧"
author: petertay
ms.date: 06/09/2017
ms.openlocfilehash: 33ae6850ffa5b28108f30475804be5347859f0c3
ms.sourcegitcommit: ea7108f71dab09175ff69322874d1bcba800a37a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="extend-azure-resource-manager-template-functionality"></a><span data-ttu-id="23c27-103">擴充 Azure Resource Manager 範本功能</span><span class="sxs-lookup"><span data-stu-id="23c27-103">Extend Azure Resource Manager template functionality</span></span>

<span data-ttu-id="23c27-104">在 2016 年，Microsoft 模式與練習小組建立了一組 Azure Resource Manager [範本建置組塊](https://github.com/mspnp/template-building-blocks/wiki)，其目標是簡化資源部署。</span><span class="sxs-lookup"><span data-stu-id="23c27-104">In 2016, the Microsoft patterns & practices team created a set of Azure Resource Manager [template building blocks](https://github.com/mspnp/template-building-blocks/wiki) with the goal of simplifying resource deployment.</span></span> <span data-ttu-id="23c27-105">每個建置組塊包含一組預先建置的範本，這個範本會部署由個別參數檔案指定的資源集。</span><span class="sxs-lookup"><span data-stu-id="23c27-105">Each building block contains a set of pre-built templates that deploy sets of resources specified by separate parameter files.</span></span>

<span data-ttu-id="23c27-106">建置組塊範本的設計目的是結合在一起以建立更大且更複雜的部署。</span><span class="sxs-lookup"><span data-stu-id="23c27-106">The building block templates are designed to be combined together to create larger and more complex deployments.</span></span> <span data-ttu-id="23c27-107">例如，在 Azure 中部署虛擬機器需要虛擬網路、儲存體帳戶和其他資源。</span><span class="sxs-lookup"><span data-stu-id="23c27-107">For example, deploying a virtual machine in Azure requires a virtual network, storage accounts, and other resources.</span></span> <span data-ttu-id="23c27-108">[虛擬網路建置組塊範本](https://github.com/mspnp/template-building-blocks/wiki/VNet-(v1))會部署虛擬網路和子網路。</span><span class="sxs-lookup"><span data-stu-id="23c27-108">The [virtual network building block template](https://github.com/mspnp/template-building-blocks/wiki/VNet-(v1)) deploys a virtual network and subnets.</span></span> <span data-ttu-id="23c27-109">[虛擬機器建置組塊範本](https://github.com/mspnp/template-building-blocks/wiki/Windows-and-Linux-VMs-(v1))會部署儲存體帳戶、網路介面和實際的 VM。</span><span class="sxs-lookup"><span data-stu-id="23c27-109">The [virtual machine building block template](https://github.com/mspnp/template-building-blocks/wiki/Windows-and-Linux-VMs-(v1)) deploys storage accounts, network interfaces, and the actual VMs.</span></span> <span data-ttu-id="23c27-110">然後您就可以建立指令碼或範本以使用其對應參數檔案呼叫兩個建置組塊範本，使用一個作業來部署完整的架構。</span><span class="sxs-lookup"><span data-stu-id="23c27-110">You can then create a script or template to call both building block templates with their corresponding parameter files to deploy a complete architecture with one operation.</span></span>

<span data-ttu-id="23c27-111">在開發建置組塊範本時，p&p 設計了數個概念以擴充 Azure Resource Manager 範本功能。</span><span class="sxs-lookup"><span data-stu-id="23c27-111">While developing the building block templates, p&p designed several concepts to extend Azure Resource Manager template functionality.</span></span> <span data-ttu-id="23c27-112">在此系列中，我們將說明其中幾個概念，讓您可以在自己的範本中使用它們。</span><span class="sxs-lookup"><span data-stu-id="23c27-112">In this series, we will describe several of these concepts so you can use them in your own templates.</span></span>

> [!NOTE]
> <span data-ttu-id="23c27-113">這些文章假設您對於 Azure Resource Manager 範本已有進階了解。</span><span class="sxs-lookup"><span data-stu-id="23c27-113">These articles assume you have an advanced understanding of Azure Resource Manager templates.</span></span>
---
title: 更新 Azure Resource Manager 範本中的資源
description: 說明如何擴充 Azure Resource Manager 範本的功能，以更新資源。
author: petertay
ms.date: 10/31/2018
ms.topic: article
ms.service: architecture-center
ms.subservice: reference-architecture
ms.openlocfilehash: de76e69e94917bbbe94c0f87fda2cdbe415181dc
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241709"
---
# <a name="update-a-resource-in-an-azure-resource-manager-template"></a><span data-ttu-id="0e260-103">更新 Azure Resource Manager 範本中的資源</span><span class="sxs-lookup"><span data-stu-id="0e260-103">Update a resource in an Azure Resource Manager template</span></span>

<span data-ttu-id="0e260-104">在某些情況下，您必須在部署期間更新資源。</span><span class="sxs-lookup"><span data-stu-id="0e260-104">There are some scenarios in which you need to update a resource during a deployment.</span></span> <span data-ttu-id="0e260-105">當您必須等到其他相依資源建立後才能指定某項資源的所有屬性時，可能會遇到這種情況。</span><span class="sxs-lookup"><span data-stu-id="0e260-105">You might encounter this scenario when you cannot specify all the properties for a resource until other, dependent resources are created.</span></span> <span data-ttu-id="0e260-106">例如，如果您建立負載平衡器的後端集區，您可能會更新虛擬機器 (VM) 上的網路介面 (NIC)，以將這些介面納入後端集區。</span><span class="sxs-lookup"><span data-stu-id="0e260-106">For example, if you create a backend pool for a load balancer, you might update the network interfaces (NICs) on your virtual machines (VMs) to include them in the backend pool.</span></span> <span data-ttu-id="0e260-107">當 Resource Manager 支援在部署期間更新資源，您必須正確設計範本以免發生錯誤，並確保會以更新的方式來處理部署。</span><span class="sxs-lookup"><span data-stu-id="0e260-107">And while Resource Manager supports updating resources during deployment, you must design your template correctly to avoid errors and to ensure the deployment is handled as an update.</span></span>

<span data-ttu-id="0e260-108">首先，您必須在範本中參考資源一次以建立資源，之後必須以相同的名稱參考資源，才能在稍後對資源進行更新。</span><span class="sxs-lookup"><span data-stu-id="0e260-108">First, you must reference the resource once in the template to create it and then reference the resource by the same name to update it later.</span></span> <span data-ttu-id="0e260-109">不過，如果這兩個資源在範本中擁有相同名稱，Resource Manager 就會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="0e260-109">However, if two resources have the same name in a template, Resource Manager throws an exception.</span></span> <span data-ttu-id="0e260-110">為了避免這個錯誤，請在第二個範本中指定更新後的資源，並使用 `Microsoft.Resources/deployments` 資源類型，將更新後的資源連結或納入為子範本。</span><span class="sxs-lookup"><span data-stu-id="0e260-110">To avoid this error, specify the updated resource in a second template that's either linked or included as a subtemplate using the `Microsoft.Resources/deployments` resource type.</span></span>

<span data-ttu-id="0e260-111">其次，您必須在巢狀的範本中指定要變更的現有屬性名稱，或為要新增的屬性指定新名稱。</span><span class="sxs-lookup"><span data-stu-id="0e260-111">Second, you must either specify the name of the existing property to change or a new name for a property to add in the nested template.</span></span> <span data-ttu-id="0e260-112">您也必須指定原始屬性和其原始值。</span><span class="sxs-lookup"><span data-stu-id="0e260-112">You must also specify the original properties and their original values.</span></span> <span data-ttu-id="0e260-113">如果您無法提供原始的屬性和值，Resource Manager 會假設您想要建立新的資源，並會刪除原始資源。</span><span class="sxs-lookup"><span data-stu-id="0e260-113">If you fail to provide the original properties and values, Resource Manager assumes you want to create a new resource and deletes the original resource.</span></span>

## <a name="example-template"></a><span data-ttu-id="0e260-114">範本範例</span><span class="sxs-lookup"><span data-stu-id="0e260-114">Example template</span></span>

<span data-ttu-id="0e260-115">讓我們看看示範這項操作的範例範本。</span><span class="sxs-lookup"><span data-stu-id="0e260-115">Let's look at an example template that demonstrates this.</span></span> <span data-ttu-id="0e260-116">我們的範本會部署名為 `firstVNet` 的虛擬網路，此網路具有一個名為 `firstSubnet` 的子網路。</span><span class="sxs-lookup"><span data-stu-id="0e260-116">Our template deploys a virtual network named `firstVNet` that has one subnet named `firstSubnet`.</span></span> <span data-ttu-id="0e260-117">接著，它會部署名為 `nic1` 的虛擬網路介面 (NIC)，並讓它與我們的子網路相關聯。</span><span class="sxs-lookup"><span data-stu-id="0e260-117">It then deploys a virtual network interface (NIC) named `nic1` and associates it with our subnet.</span></span> <span data-ttu-id="0e260-118">然後，名為 `updateVNet` 的部署資源會納入巢狀範本，此範本會更新 `firstVNet` 資源，新增名為 `secondSubnet` 的第二個子網路 。</span><span class="sxs-lookup"><span data-stu-id="0e260-118">Then, a deployment resource named `updateVNet` includes a nested template that updates our `firstVNet` resource by adding a second subnet named `secondSubnet`.</span></span>

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
      {
      "apiVersion": "2016-03-30",
      "name": "firstVNet",
      "location":"[resourceGroup().location]",
      "type": "Microsoft.Network/virtualNetworks",
      "properties": {
          "addressSpace":{"addressPrefixes": [
              "10.0.0.0/22"
          ]},
          "subnets":[
              {
                  "name":"firstSubnet",
                  "properties":{
                    "addressPrefix":"10.0.0.0/24"
                  }
              }
            ]
      }
    },
    {
        "apiVersion": "2015-06-15",
        "type":"Microsoft.Network/networkInterfaces",
        "name":"nic1",
        "location":"[resourceGroup().location]",
        "dependsOn": [
            "firstVNet"
        ],
        "properties": {
            "ipConfigurations":[
                {
                    "name":"ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod":"Dynamic",
                        "subnet": {
                            "id": "[concat(resourceId('Microsoft.Network/virtualNetworks','firstVNet'),'/subnets/firstSubnet')]"
                        }
                    }
                }
            ]
        }
    },
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "updateVNet",
      "dependsOn": [
          "nic1"
      ],
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
              {
                  "apiVersion": "2016-03-30",
                  "name": "firstVNet",
                  "location":"[resourceGroup().location]",
                  "type": "Microsoft.Network/virtualNetworks",
                  "properties": {
                      "addressSpace": "[reference('firstVNet').addressSpace]",
                      "subnets":[
                          {
                              "name":"[reference('firstVNet').subnets[0].name]",
                              "properties":{
                                  "addressPrefix":"[reference('firstVNet').subnets[0].properties.addressPrefix]"
                                  }
                          },
                          {
                              "name":"secondSubnet",
                              "properties":{
                                  "addressPrefix":"10.0.1.0/24"
                                  }
                          }
                     ]
                  }
              }
          ],
          "outputs": {}
          }
        }
    }
  ],
  "outputs": {}
}
```

<span data-ttu-id="0e260-119">讓我們先來看看 `firstVNet` 資源的資源物件。</span><span class="sxs-lookup"><span data-stu-id="0e260-119">Let's take a look at the resource object for our `firstVNet` resource first.</span></span> <span data-ttu-id="0e260-120">請注意，我們重新指定巢狀範本中 `firstVNet` 的設定 &mdash; 這是因為 Resource Manager 不允許在相同範本內，有相同部署名稱，而巢狀範本被視為不同的範本。</span><span class="sxs-lookup"><span data-stu-id="0e260-120">Notice that we respecify the settings for our `firstVNet` in a nested template&mdash;this is because Resource Manager doesn't allow the same deployment name within the same template and nested templates are considered to be a different template.</span></span> <span data-ttu-id="0e260-121">我們藉由重新指定 `firstSubnet` 資源的值，告訴 Resource Manager 要更新現有的資源，而不是刪除它並重新部署它。</span><span class="sxs-lookup"><span data-stu-id="0e260-121">By respecifying our values for our `firstSubnet` resource, we are telling Resource Manager to update the existing resource instead of deleting it and redeploying it.</span></span> <span data-ttu-id="0e260-122">最後，在此更新期間會收取 `secondSubnet` 的新設定。</span><span class="sxs-lookup"><span data-stu-id="0e260-122">Finally, our new settings for `secondSubnet` are picked up during this update.</span></span>

## <a name="try-the-template"></a><span data-ttu-id="0e260-123">試用範本</span><span class="sxs-lookup"><span data-stu-id="0e260-123">Try the template</span></span>

<span data-ttu-id="0e260-124">您可以在 [GitHub][github] 上取得範本範例。</span><span class="sxs-lookup"><span data-stu-id="0e260-124">An example template is available on [GitHub][github].</span></span> <span data-ttu-id="0e260-125">若要部署範本，請執行下列 [Azure CLI][cli] 命令：</span><span class="sxs-lookup"><span data-stu-id="0e260-125">To deploy the template, run the following [Azure CLI][cli] commands:</span></span>

```bash
az group create --location <location> --name <resource-group-name>
az group deployment create -g <resource-group-name> \
    --template-uri https://raw.githubusercontent.com/mspnp/template-examples/master/example1-update/deploy.json
```

<span data-ttu-id="0e260-126">當部署完成之後，開啟您在入口網站中指定的資源群組。</span><span class="sxs-lookup"><span data-stu-id="0e260-126">Once deployment has finished, open the resource group you specified in the portal.</span></span> <span data-ttu-id="0e260-127">您會看到名為 `firstVNet` 的虛擬網路和名為 `nic1` 的 NIC。</span><span class="sxs-lookup"><span data-stu-id="0e260-127">You see a virtual network named `firstVNet` and a NIC named `nic1`.</span></span> <span data-ttu-id="0e260-128">按一下 `firstVNet`，然後再按一下 `subnets`。</span><span class="sxs-lookup"><span data-stu-id="0e260-128">Click `firstVNet`, then click `subnets`.</span></span> <span data-ttu-id="0e260-129">您會看到原先建立的 `firstSubnet`，並看到 `updateVNet` 資源中新增的 `secondSubnet`。</span><span class="sxs-lookup"><span data-stu-id="0e260-129">You see the `firstSubnet` that was originally created, and you see the `secondSubnet` that was added in the `updateVNet` resource.</span></span>

![原始子網路和更新後的子網路](../_images/firstVNet-subnets.png)

<span data-ttu-id="0e260-131">然後，返回資源群組，並依序按一下 `nic1` 和 `IP configurations`。</span><span class="sxs-lookup"><span data-stu-id="0e260-131">Then, go back to the resource group and click `nic1` then click `IP configurations`.</span></span> <span data-ttu-id="0e260-132">在 `IP configurations` 區段中，`subnet` 會設為 `firstSubnet (10.0.0.0/24)`。</span><span class="sxs-lookup"><span data-stu-id="0e260-132">In the `IP configurations` section, the `subnet` is set to `firstSubnet (10.0.0.0/24)`.</span></span>

![nic1 IP 組態設定](../_images/nic1-ipconfigurations.png)

<span data-ttu-id="0e260-134">原始的 `firstVNet` 已更新而非重新建立。</span><span class="sxs-lookup"><span data-stu-id="0e260-134">The original `firstVNet` has been updated instead of recreated.</span></span> <span data-ttu-id="0e260-135">如果 `firstVNet` 已重新建立，`nic1` 不會與 `firstVNet` 相關聯。</span><span class="sxs-lookup"><span data-stu-id="0e260-135">If `firstVNet` had been recreated, `nic1` would not be associated with `firstVNet`.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0e260-136">後續步驟</span><span class="sxs-lookup"><span data-stu-id="0e260-136">Next steps</span></span>

* <span data-ttu-id="0e260-137">了解如何根據條件部署資源，例如參數值是否存在。</span><span class="sxs-lookup"><span data-stu-id="0e260-137">Learn how deploy a resource based on a condition, such as whether a parameter value is present.</span></span> <span data-ttu-id="0e260-138">請參閱[在 Azure Resource Manager 範本中依條件部署資源](./conditional-deploy.md)。</span><span class="sxs-lookup"><span data-stu-id="0e260-138">See [Conditionally deploy a resource in an Azure Resource Manager template](./conditional-deploy.md).</span></span>

[cli]: /cli/azure/?view=azure-cli-latest
[github]: https://github.com/mspnp/template-examples

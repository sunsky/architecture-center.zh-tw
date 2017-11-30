---
title: "更新 Azure Resource Manager 範本中的資源"
description: "說明如何擴充 Azure Resource Manager 範本的功能，以更新資源"
author: petertay
ms.date: 06/09/2017
ms.openlocfilehash: fc2565819c66ee7695224ef5793e7276e6e552e0
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="update-a-resource-in-an-azure-resource-manager-template"></a><span data-ttu-id="2db9b-103">更新 Azure Resource Manager 範本中的資源</span><span class="sxs-lookup"><span data-stu-id="2db9b-103">Update a resource in an Azure Resource Manager template</span></span>

<span data-ttu-id="2db9b-104">在某些情況下，您必須在部署期間更新資源。</span><span class="sxs-lookup"><span data-stu-id="2db9b-104">There are some scenarios in which you need to update a resource during a deployment.</span></span> <span data-ttu-id="2db9b-105">當您必須等到其他相依資源建立後才能指定某項資源的所有屬性時，可能會遇到這種情況。</span><span class="sxs-lookup"><span data-stu-id="2db9b-105">You might encounter this scenario when you cannot specify all the properties for a resource until other, dependent resources are created.</span></span> <span data-ttu-id="2db9b-106">例如，如果您建立負載平衡器的後端集區，您可能會更新虛擬機器 (VM) 上的網路介面 (NIC)，以將這些介面納入後端集區。</span><span class="sxs-lookup"><span data-stu-id="2db9b-106">For example, if you create a backend pool for a load balancer, you might update the network interfaces (NICs) on your virtual machines (VMs) to include them in the backend pool.</span></span> <span data-ttu-id="2db9b-107">當 Resource Manager 支援在部署期間更新資源，您必須正確設計範本以免發生錯誤，並確保會以更新的方式來處理部署。</span><span class="sxs-lookup"><span data-stu-id="2db9b-107">And while Resource Manager supports updating resources during deployment, you must design your template correctly to avoid errors and to ensure the deployment is handled as an update.</span></span>

<span data-ttu-id="2db9b-108">首先，您必須在範本中參考資源一次以建立資源，之後必須以相同的名稱參考資源，才能在稍後對資源進行更新。</span><span class="sxs-lookup"><span data-stu-id="2db9b-108">First, you must reference the resource once in the template to create it and then reference the resource by the same name to update it later.</span></span> <span data-ttu-id="2db9b-109">不過，如果這兩個資源在範本中擁有相同名稱，Resource Manager 就會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="2db9b-109">However, if two resources have the same name in a template, Resource Manager throws an exception.</span></span> <span data-ttu-id="2db9b-110">為了避免這個錯誤，請在第二個範本中指定更新後的資源，並使用 `Microsoft.Resources/deployments` 資源類型，將更新後的資源連結或納入為子範本。</span><span class="sxs-lookup"><span data-stu-id="2db9b-110">To avoid this error, specify the updated resource in a second template that's either linked or included as a subtemplate using the `Microsoft.Resources/deployments` resource type.</span></span>

<span data-ttu-id="2db9b-111">其次，您必須在巢狀的範本中指定要變更的現有屬性名稱，或為要新增的屬性指定新名稱。</span><span class="sxs-lookup"><span data-stu-id="2db9b-111">Second, you must either specify the name of the existing property to change or a new name for a property to add in the nested template.</span></span> <span data-ttu-id="2db9b-112">您也必須指定原始屬性和其原始值。</span><span class="sxs-lookup"><span data-stu-id="2db9b-112">You must also specify the original properties and their original values.</span></span> <span data-ttu-id="2db9b-113">如果您無法提供原始的屬性和值，Resource Manager 會假設您想要建立新的資源，並會刪除原始資源。</span><span class="sxs-lookup"><span data-stu-id="2db9b-113">If you fail to provide the original properties and values, Resource Manager assumes you want to create a new resource and deletes the original resource.</span></span>

## <a name="example-template"></a><span data-ttu-id="2db9b-114">範本範例</span><span class="sxs-lookup"><span data-stu-id="2db9b-114">Example template</span></span>

<span data-ttu-id="2db9b-115">讓我們看看示範這項操作的範例範本。</span><span class="sxs-lookup"><span data-stu-id="2db9b-115">Let's look at an example template that demonstrates this.</span></span> <span data-ttu-id="2db9b-116">我們的範本會部署名為 `firstVNet` 的虛擬網路，此網路具有一個名為 `firstSubnet` 的子網路。</span><span class="sxs-lookup"><span data-stu-id="2db9b-116">Our template deploys a virtual network  named `firstVNet` that has one subnet named `firstSubnet`.</span></span> <span data-ttu-id="2db9b-117">接著，它會部署名為 `nic1` 的虛擬網路介面 (NIC)，並讓它與我們的子網路相關聯。</span><span class="sxs-lookup"><span data-stu-id="2db9b-117">It then deploys a virtual network interface (NIC) named `nic1` and associates it with our subnet.</span></span> <span data-ttu-id="2db9b-118">然後，名為 `updateVNet` 的部署資源會納入巢狀範本，此範本會更新 `firstVNet` 資源，新增名為 `secondSubnet` 的第二個子網路 。</span><span class="sxs-lookup"><span data-stu-id="2db9b-118">Then, a deployment resource named `updateVNet` includes a nested template that updates our `firstVNet` resource by adding a second subnet named `secondSubnet`.</span></span> 

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
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

<span data-ttu-id="2db9b-119">讓我們先來看看 `firstVNet` 資源的資源物件。</span><span class="sxs-lookup"><span data-stu-id="2db9b-119">Let's take a look at the resource object for our `firstVNet` resource first.</span></span> <span data-ttu-id="2db9b-120">請注意，我們重新指定巢狀範本中 `firstVNet` 的設定 &mdash; 這是因為 Resource Manager 不允許在相同範本內，有相同部署名稱，而巢狀範本被視為不同的範本。</span><span class="sxs-lookup"><span data-stu-id="2db9b-120">Notice that we respecify the settings for our `firstVNet` in a nested template&mdash;this is because Resource Manager doesn't allow the same deployment name within the same template and nested templates are considered to be a different template.</span></span> <span data-ttu-id="2db9b-121">我們藉由重新指定 `firstSubnet` 資源的值，告訴 Resource Manager 要更新現有的資源，而不是刪除它並重新部署它。</span><span class="sxs-lookup"><span data-stu-id="2db9b-121">By respecifying our values for our `firstSubnet` resource, we are telling Resource Manager to update the existing resource instead of deleting it and redeploying it.</span></span> <span data-ttu-id="2db9b-122">最後，在此更新期間會收取 `secondSubnet` 的新設定。</span><span class="sxs-lookup"><span data-stu-id="2db9b-122">Finally, our new settings for `secondSubnet` are picked up during this update.</span></span>

## <a name="try-the-template"></a><span data-ttu-id="2db9b-123">試用範本</span><span class="sxs-lookup"><span data-stu-id="2db9b-123">Try the template</span></span>

<span data-ttu-id="2db9b-124">如果您想要實驗此範本，請遵循下列步驟︰</span><span class="sxs-lookup"><span data-stu-id="2db9b-124">If you would like to experiment with this template, follow these steps:</span></span>

1.  <span data-ttu-id="2db9b-125">請移至 Azure 入口網站，選取 **+** 圖示，並搜尋 [範本部署] 資源類型，並選取它。</span><span class="sxs-lookup"><span data-stu-id="2db9b-125">Go to the Azure portal, select the **+** icon, and search for the **template deployment** resource type, and select it.</span></span>
2.  <span data-ttu-id="2db9b-126">導覽至 [範本部署] 頁面，選取 [建立] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="2db9b-126">Navigate to the **template deployment** page, select the **create** button.</span></span> <span data-ttu-id="2db9b-127">這個按鈕會開啟 [自訂部署] 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="2db9b-127">This button opens the **custom deployment** blade.</span></span>
3.  <span data-ttu-id="2db9b-128">選取 [編輯] 圖示。</span><span class="sxs-lookup"><span data-stu-id="2db9b-128">Select the **edit** icon.</span></span>
4.  <span data-ttu-id="2db9b-129">刪除空白範本。</span><span class="sxs-lookup"><span data-stu-id="2db9b-129">Delete the empty template.</span></span>
5.  <span data-ttu-id="2db9b-130">複製範例範本並貼到右窗格。</span><span class="sxs-lookup"><span data-stu-id="2db9b-130">Copy and paste the sample template into the right pane.</span></span>
6.  <span data-ttu-id="2db9b-131">選取 [儲存] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="2db9b-131">Select the **save** button.</span></span>
7.  <span data-ttu-id="2db9b-132">您會返回 [自訂部署] 窗格，但這次窗格中會出現一些下拉式清單。</span><span class="sxs-lookup"><span data-stu-id="2db9b-132">You return to the **custom deployment** pane, but this time there are some drop-down list boxes.</span></span> <span data-ttu-id="2db9b-133">請選取您的訂閱，您可以建立新的或使用現有的資源群組，並選取一個位置。</span><span class="sxs-lookup"><span data-stu-id="2db9b-133">Select your subscription, either create new or use existing resource group, and select a location.</span></span> <span data-ttu-id="2db9b-134">檢閱條款及條件，然後選取 [我同意] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="2db9b-134">Review the terms and conditions, then select the **I agree** button.</span></span>
8.  <span data-ttu-id="2db9b-135">選取 [購買] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="2db9b-135">Select the **purchase** button.</span></span>

<span data-ttu-id="2db9b-136">當部署完成之後，開啟您在入口網站中指定的資源群組。</span><span class="sxs-lookup"><span data-stu-id="2db9b-136">Once deployment has finished, open the resource group you specified in the portal.</span></span> <span data-ttu-id="2db9b-137">您會看到名為 `firstVNet` 的虛擬網路和名為 `nic1` 的 NIC。</span><span class="sxs-lookup"><span data-stu-id="2db9b-137">You see a virtual network named `firstVNet` and a NIC named `nic1`.</span></span> <span data-ttu-id="2db9b-138">按一下 `firstVNet`，然後再按一下 `subnets`。</span><span class="sxs-lookup"><span data-stu-id="2db9b-138">Click `firstVNet`, then click `subnets`.</span></span> <span data-ttu-id="2db9b-139">您會看到原先建立的 `firstSubnet`，並看到 `updateVNet` 資源中新增的 `secondSubnet`。</span><span class="sxs-lookup"><span data-stu-id="2db9b-139">You see the `firstSubnet` that was originally created, and you see the `secondSubnet` that was added in the `updateVNet` resource.</span></span> 

![原始子網路和更新後的子網路](../_images/firstVNet-subnets.png)

<span data-ttu-id="2db9b-141">然後，返回資源群組，並依序按一下 `nic1` 和 `IP configurations`。</span><span class="sxs-lookup"><span data-stu-id="2db9b-141">Then, go back to the resource group and click `nic1` then click `IP configurations`.</span></span> <span data-ttu-id="2db9b-142">在 `IP configurations` 區段中，`subnet` 會設為 `firstSubnet (10.0.0.0/24)`。</span><span class="sxs-lookup"><span data-stu-id="2db9b-142">In the `IP configurations` section, the `subnet` is set to `firstSubnet (10.0.0.0/24)`.</span></span> 

![nic1 IP 組態設定](../_images/nic1-ipconfigurations.png)

<span data-ttu-id="2db9b-144">原始的 `firstVNet` 已更新而非重新建立。</span><span class="sxs-lookup"><span data-stu-id="2db9b-144">The original `firstVNet` has been updated instead of recreated.</span></span> <span data-ttu-id="2db9b-145">如果 `firstVNet` 已重新建立，`nic1` 不會與 `firstVNet` 相關聯。</span><span class="sxs-lookup"><span data-stu-id="2db9b-145">If `firstVNet` had been recreated, `nic1` would not be associated with `firstVNet`.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2db9b-146">後續步驟</span><span class="sxs-lookup"><span data-stu-id="2db9b-146">Next steps</span></span>

* <span data-ttu-id="2db9b-147">此技術可以在[範本建置區塊專案](https://github.com/mspnp/template-building-blocks)與 [Azure 參考架構](/azure/architecture/reference-architectures/)中實作。</span><span class="sxs-lookup"><span data-stu-id="2db9b-147">This technique is implemented in the [template building blocks project](https://github.com/mspnp/template-building-blocks) and the [Azure reference architectures](/azure/architecture/reference-architectures/).</span></span> <span data-ttu-id="2db9b-148">您可以使用它們來建立您自己的架構，或部署我們的其中一個參考架構。</span><span class="sxs-lookup"><span data-stu-id="2db9b-148">You can use these to create your own architecture or deploy one of our reference architectures.</span></span>

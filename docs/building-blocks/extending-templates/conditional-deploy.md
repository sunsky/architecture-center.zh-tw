---
title: "在 Azure Resource Manager 範本中依條件部署資源"
description: "說明如何擴充 Azure Resource Manager 範本的功能，根據參數值條件部署資源"
author: petertay
ms.date: 06/09/2017
ms.openlocfilehash: e911e7dc41b4f71ebfaf13a00f8cdbb5b4e2578b
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="conditionally-deploy-a-resource-in-an-azure-resource-manager-template"></a><span data-ttu-id="c6b0f-103">在 Azure Resource Manager 範本中依條件部署資源</span><span class="sxs-lookup"><span data-stu-id="c6b0f-103">Conditionally deploy a resource in an Azure Resource Manager template</span></span>

<span data-ttu-id="c6b0f-104">在某些情況下，您需要將您的範本設計為根據條件部署資源，像是某個參數值是否存在。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-104">There are some scenarios in which you need to design your template to deploy a resource based on a condition, such as whether or not a parameter value is present.</span></span> <span data-ttu-id="c6b0f-105">例如，範本會部署虛擬網路，並包含參數來指定進行對等互連的其他虛擬網路。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-105">For example, your template may deploy a virtual network and include parameters to specify other virtual networks for peering.</span></span> <span data-ttu-id="c6b0f-106">如果您未指定任何對等互連的參數值，即表示您不想要 Resource Manager 部署對等互連資源。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-106">If you've not specified any parameter values for peering, you don't want Resource Manager to deploy the peering resource.</span></span>

<span data-ttu-id="c6b0f-107">若要完成此操作，在資源中使用[`condition` 元素][azure-resource-manager-condition]測試參數陣列的長度。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-107">To accomplish this, use the [`condition` element][azure-resource-manager-condition] in the resource to test the length of your parameter array.</span></span> <span data-ttu-id="c6b0f-108">如果長度為零，傳回 `false` 以阻止部署，但所有大於零的值都會傳回 `true` 以允許部署。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-108">If the length is zero, return `false` to prevent deployment, but for all values greater than zero return `true` to allow deployment.</span></span>

## <a name="example-template"></a><span data-ttu-id="c6b0f-109">範本範例</span><span class="sxs-lookup"><span data-stu-id="c6b0f-109">Example template</span></span>

<span data-ttu-id="c6b0f-110">讓我們看看示範這項操作的範例範本。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-110">Let's look at an example template that demonstrates this.</span></span> <span data-ttu-id="c6b0f-111">我們的範本使用[`condition` 元素][azure-resource-manager-condition]來控制 `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` 資源的部署。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-111">Our template uses the [`condition` element][azure-resource-manager-condition] to control deployment of the `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` resource.</span></span> <span data-ttu-id="c6b0f-112">這項資源會在相同區域中的兩個 Azure 虛擬網路之間建立對等互連。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-112">This resource creates a peering between two Azure Virtual Networks in the same region.</span></span>

<span data-ttu-id="c6b0f-113">讓我們看看範本的每個區段。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-113">Let's take a look at each section of the template.</span></span>

<span data-ttu-id="c6b0f-114">`parameters` 元素會定義一個名為 `virtualNetworkPeerings` 的參數：</span><span class="sxs-lookup"><span data-stu-id="c6b0f-114">The `parameters` element defines a single parameter named `virtualNetworkPeerings`:</span></span> 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "virtualNetworkPeerings": {
      "type": "array",
      "defaultValue": []
    }
  },
```
<span data-ttu-id="c6b0f-115">`virtualNetworkPeerings` 參數是 `array`，具有下列結構描述：</span><span class="sxs-lookup"><span data-stu-id="c6b0f-115">Our `virtualNetworkPeerings` parameter is an `array` and has the following schema:</span></span>

```json
"virtualNetworkPeerings": [
    {
        "remoteVirtualNetwork": {
            "name": "my-other-virtual-network"
        },
        "allowForwardedTraffic": true,
        "allowGatewayTransit": true,
        "useRemoteGateways": false
    }
]
```

<span data-ttu-id="c6b0f-116">參數中的屬性指定[與對等互連虛擬網路相關的設定][vnet-peering-resource-schema]。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-116">The properties in our parameter specify the [settings related to peering virtual networks][vnet-peering-resource-schema].</span></span> <span data-ttu-id="c6b0f-117">當我們在 `resources` 區段中指定 `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` 資源，會提供這些屬性的值：</span><span class="sxs-lookup"><span data-stu-id="c6b0f-117">We'll provide the values for these properties when we specify the `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` resource in the `resources` section:</span></span>

```json
"resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "[concat('vnp-', copyIndex())]",
      "condition": "[greater(length(parameters('virtualNetworkPeerings')), 0)]",
      "dependsOn": [
        "virtualNetworks"
      ],
      "copy": {
          "name": "iterator",
          "count": "[length(variables('peerings'))]",
          "mode": "serial"
      },
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
            {
              "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
              "apiVersion": "2016-06-01",
              "location": "[resourceGroup().location]",
              "name": "[variables('peerings')[copyIndex()].name]",
              "properties": "[variables('peerings')[copyIndex()].properties]"
            }
          ],
          "outputs": {
          }
        }
      }
    }
]
```
<span data-ttu-id="c6b0f-118">在範本的這個部分做了幾件事情。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-118">There are a couple of things going on in this part of our template.</span></span> <span data-ttu-id="c6b0f-119">首先，實際被部署的資源是 `Microsoft.Resources/deployments` 類型的內嵌範本，它有自己的範本可實際部署 `Microsoft.Network/virtualNetworks/virtualNetworkPeerings`。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-119">First, the actual resource being deployed is an inline template of type `Microsoft.Resources/deployments` that includes its own template that actually deploys the `Microsoft.Network/virtualNetworks/virtualNetworkPeerings`.</span></span>

<span data-ttu-id="c6b0f-120">內嵌範本的 `name` 是將 `copyIndex()`的目前反覆運算與前置詞 `vnp-` 串連，得到的唯一名稱。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-120">Our `name` for the inline template is made unique by concatenating the current iteration of the `copyIndex()` with the prefix `vnp-`.</span></span> 

<span data-ttu-id="c6b0f-121">`condition` 元素指定當 `greater()` 函式評估為 `true` 時，應該處理的資源。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-121">The `condition` element specifies that our resource should be processed when the `greater()` function evaluates to `true`.</span></span> <span data-ttu-id="c6b0f-122">在這裡，我們測試 `virtualNetworkPeerings` 參數陣列是否 `greater()` 零。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-122">Here, we're testing if the `virtualNetworkPeerings` parameter array is `greater()` than zero.</span></span> <span data-ttu-id="c6b0f-123">如果是，則評估為 `true`，滿足 `condition`。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-123">If it is, it evaluates to `true` and the `condition` is satisfied.</span></span> <span data-ttu-id="c6b0f-124">否則，便為 `false`。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-124">Otherwise, it's `false`.</span></span>

<span data-ttu-id="c6b0f-125">接下來，我們指定 `copy` 迴圈。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-125">Next, we specify our `copy` loop.</span></span> <span data-ttu-id="c6b0f-126">它是 `serial` 迴圈，表示迴圈會依序完成，每個資源都會等候直到最後一個資源部署完成。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-126">It's a `serial` loop that means the loop is done in sequence, with each resource waiting until the last resource has been deployed.</span></span> <span data-ttu-id="c6b0f-127">`count` 屬性指定迴圈反覆執行的次數。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-127">The `count` property specifies the number of times the loop iterates.</span></span> <span data-ttu-id="c6b0f-128">在這裡，通常我們會將它設定為 `virtualNetworkPeerings` 陣列的長度，因為它包含的參數物件指定了我們要部署的資源。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-128">Here, normally we'd set it to the length of the `virtualNetworkPeerings` array because it contains the parameter objects specifying the resource we want to deploy.</span></span> <span data-ttu-id="c6b0f-129">不過，如果這樣做，若陣列是空的，驗證會失敗，因為 Resource Manager 注意到我們嘗試存取不存在的屬性。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-129">However, if we do that, validation will fail if the array is empty because Resource Manager notices that we are attempting to access properties that do not exist.</span></span> <span data-ttu-id="c6b0f-130">不過，這個問題可以解決。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-130">We can work around this, however.</span></span> <span data-ttu-id="c6b0f-131">看一下我們需要的變數：</span><span class="sxs-lookup"><span data-stu-id="c6b0f-131">Let's take a look at the variables we'll need:</span></span>

```json
  "variables": {
    "workaround": {
       "true": "[parameters('virtualNetworkPeerings')]",
       "false": [{
           "name": "workaround",
           "properties": {}
       }]
     },
     "peerings": "[variables('workaround')[string(greater(length(parameters('virtualNetworkPeerings')), 0))]]"
  },
```

<span data-ttu-id="c6b0f-132">`workaround` 變數有兩個屬性，一個名為 `true`、一個名為 `false`。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-132">Our `workaround` variable includes two properties, one named `true` and one named `false`.</span></span> <span data-ttu-id="c6b0f-133">`true` 屬性評估為 `virtualNetworkPeerings` 參數陣列的值。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-133">The `true` property evaluates to the value of the `virtualNetworkPeerings` parameter array.</span></span> <span data-ttu-id="c6b0f-134">`false` 屬性評估為空物件，包括Resource Manager 預期會看到的具名屬性 &mdash; 請注意，`false` 其實是一個陣列，和 `virtualNetworkPeerings`參數一樣，能滿足驗證。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-134">The `false` property evaluates to an empty object including the named properties that Resource Manager expects to see&mdash;note that `false` is actually an array, just as our `virtualNetworkPeerings` parameter is, which will satisfy validation.</span></span> 

<span data-ttu-id="c6b0f-135">`peerings` 變數會再使用 `workaround` 變數一次，測試 `virtualNetworkPeerings`參數陣列的長度是否大於零。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-135">Our `peerings` variable uses our `workaround` variable by once again testing if the length of the `virtualNetworkPeerings` parameter array is greater than zero.</span></span> <span data-ttu-id="c6b0f-136">如果是，`string` 評估為 `true`，且`workaround` 變數評估為 `virtualNetworkPeerings` 參數陣列。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-136">If it is, the `string` evaluates to `true` and the `workaround` variable evalutes to the `virtualNetworkPeerings` parameter array.</span></span> <span data-ttu-id="c6b0f-137">否則，評估為 `false`，且 `workaround` 變數評估為空物件，是陣列的第一個元素。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-137">Otherwise, it evaluates to `false` and the `workaround` variable evaluates to our empty object in the first element of the array.</span></span>

<span data-ttu-id="c6b0f-138">既然我們已解決驗證問題，我們可以在巢狀範本中直接指定 `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` 資源的部署，傳遞 `virtualNetworkPeerings` 參數陣列中的 `name` 和 `properties`。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-138">Now that we've worked around the validation issue, we can simply specify the deployment of the `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` resource in the nested template, passing the `name` and `properties` from our `virtualNetworkPeerings` parameter array.</span></span> <span data-ttu-id="c6b0f-139">在巢狀置於資源的 `properties` 元素中的 `template` 元素中可以看到。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-139">You can see this in the `template` element nested in the `properties` element of our resource.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c6b0f-140">後續步驟</span><span class="sxs-lookup"><span data-stu-id="c6b0f-140">Next steps</span></span>

* <span data-ttu-id="c6b0f-141">此技術可以在[範本建置區塊專案](https://github.com/mspnp/template-building-blocks)與 [Azure 參考架構](/azure/architecture/reference-architectures/)中實作。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-141">This technique is implemented in the [template building blocks project](https://github.com/mspnp/template-building-blocks) and the [Azure reference architectures](/azure/architecture/reference-architectures/).</span></span> <span data-ttu-id="c6b0f-142">您可以使用它們來建立您自己的架構，或部署我們的其中一個參考架構。</span><span class="sxs-lookup"><span data-stu-id="c6b0f-142">You can use these to create your own architecture or deploy one of our reference architectures.</span></span>

<!-- links -->
[azure-resource-manager-condition]: /azure/azure-resource-manager/resource-group-authoring-templates#resources
[azure-resource-manager-variable]: /azure/azure-resource-manager/resource-group-authoring-templates#variables
[vnet-peering-resource-schema]: /azure/templates/microsoft.network/virtualnetworks/virtualnetworkpeerings
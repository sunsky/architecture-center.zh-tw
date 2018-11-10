---
title: 在 Azure Resource Manager 範本中依條件部署資源
description: 說明如何擴充 Azure Resource Manager 範本的功能，根據參數值條件部署資源
author: petertay
ms.date: 10/30/2018
ms.openlocfilehash: 2c74e17a5f38f9225b696640a23b55b1285276bb
ms.sourcegitcommit: e9eb2b895037da0633ef3ccebdea2fcce047620f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2018
ms.locfileid: "50251833"
---
# <a name="conditionally-deploy-a-resource-in-an-azure-resource-manager-template"></a><span data-ttu-id="72c2a-103">在 Azure Resource Manager 範本中依條件部署資源</span><span class="sxs-lookup"><span data-stu-id="72c2a-103">Conditionally deploy a resource in an Azure Resource Manager template</span></span>

<span data-ttu-id="72c2a-104">在某些情況下，您需要將您的範本設計為根據條件部署資源，像是某個參數值是否存在。</span><span class="sxs-lookup"><span data-stu-id="72c2a-104">There are some scenarios in which you need to design your template to deploy a resource based on a condition, such as whether or not a parameter value is present.</span></span> <span data-ttu-id="72c2a-105">例如，範本會部署虛擬網路，並包含參數來指定進行對等互連的其他虛擬網路。</span><span class="sxs-lookup"><span data-stu-id="72c2a-105">For example, your template may deploy a virtual network and include parameters to specify other virtual networks for peering.</span></span> <span data-ttu-id="72c2a-106">如果您未指定任何對等互連的參數值，即表示您不想要 Resource Manager 部署對等互連資源。</span><span class="sxs-lookup"><span data-stu-id="72c2a-106">If you've not specified any parameter values for peering, you don't want Resource Manager to deploy the peering resource.</span></span>

<span data-ttu-id="72c2a-107">若要完成此操作，在資源中使用[條件元素][azure-resource-manager-condition]以測試參數陣列的長度。</span><span class="sxs-lookup"><span data-stu-id="72c2a-107">To accomplish this, use the [condition element][azure-resource-manager-condition] in the resource to test the length of your parameter array.</span></span> <span data-ttu-id="72c2a-108">如果長度為零，傳回 `false` 以阻止部署，但所有大於零的值都會傳回 `true` 以允許部署。</span><span class="sxs-lookup"><span data-stu-id="72c2a-108">If the length is zero, return `false` to prevent deployment, but for all values greater than zero return `true` to allow deployment.</span></span>

## <a name="example-template"></a><span data-ttu-id="72c2a-109">範本範例</span><span class="sxs-lookup"><span data-stu-id="72c2a-109">Example template</span></span>

<span data-ttu-id="72c2a-110">讓我們看看示範這項操作的範例範本。</span><span class="sxs-lookup"><span data-stu-id="72c2a-110">Let's look at an example template that demonstrates this.</span></span> <span data-ttu-id="72c2a-111">我們的範本使用[條件元素][azure-resource-manager-condition]來控制 `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` 資源的部署。</span><span class="sxs-lookup"><span data-stu-id="72c2a-111">Our template uses the [condition element][azure-resource-manager-condition] to control deployment of the `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` resource.</span></span> <span data-ttu-id="72c2a-112">這項資源會在相同區域中的兩個 Azure 虛擬網路之間建立對等互連。</span><span class="sxs-lookup"><span data-stu-id="72c2a-112">This resource creates a peering between two Azure Virtual Networks in the same region.</span></span>

<span data-ttu-id="72c2a-113">讓我們看看範本的每個區段。</span><span class="sxs-lookup"><span data-stu-id="72c2a-113">Let's take a look at each section of the template.</span></span>

<span data-ttu-id="72c2a-114">`parameters` 元素會定義一個名為 `virtualNetworkPeerings` 的參數：</span><span class="sxs-lookup"><span data-stu-id="72c2a-114">The `parameters` element defines a single parameter named `virtualNetworkPeerings`:</span></span> 

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
<span data-ttu-id="72c2a-115">`virtualNetworkPeerings` 參數是 `array`，具有下列結構描述：</span><span class="sxs-lookup"><span data-stu-id="72c2a-115">Our `virtualNetworkPeerings` parameter is an `array` and has the following schema:</span></span>

```json
"virtualNetworkPeerings": [
    {
      "name": "firstVNet/peering1",
      "properties": {
          "remoteVirtualNetwork": {
              "id": "[resourceId('Microsoft.Network/virtualNetworks','secondVNet')]"
          },
          "allowForwardedTraffic": true,
          "allowGatewayTransit": true,
          "useRemoteGateways": false
      }
    }
]
```

<span data-ttu-id="72c2a-116">參數中的屬性指定[與對等互連虛擬網路相關的設定][vnet-peering-resource-schema]。</span><span class="sxs-lookup"><span data-stu-id="72c2a-116">The properties in our parameter specify the [settings related to peering virtual networks][vnet-peering-resource-schema].</span></span> <span data-ttu-id="72c2a-117">當我們在 `resources` 區段中指定 `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` 資源，會提供這些屬性的值：</span><span class="sxs-lookup"><span data-stu-id="72c2a-117">We'll provide the values for these properties when we specify the `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` resource in the `resources` section:</span></span>

```json
"resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "[concat('vnp-', copyIndex())]",
      "condition": "[greater(length(parameters('virtualNetworkPeerings')), 0)]",
      "dependsOn": [
        "firstVNet", "secondVNet"
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
<span data-ttu-id="72c2a-118">在範本的這個部分做了幾件事情。</span><span class="sxs-lookup"><span data-stu-id="72c2a-118">There are a couple of things going on in this part of our template.</span></span> <span data-ttu-id="72c2a-119">首先，實際被部署的資源是 `Microsoft.Resources/deployments` 類型的內嵌範本，它有自己的範本可實際部署 `Microsoft.Network/virtualNetworks/virtualNetworkPeerings`。</span><span class="sxs-lookup"><span data-stu-id="72c2a-119">First, the actual resource being deployed is an inline template of type `Microsoft.Resources/deployments` that includes its own template that actually deploys the `Microsoft.Network/virtualNetworks/virtualNetworkPeerings`.</span></span>

<span data-ttu-id="72c2a-120">內嵌範本的 `name` 是將 `copyIndex()`的目前反覆運算與前置詞 `vnp-` 串連，得到的唯一名稱。</span><span class="sxs-lookup"><span data-stu-id="72c2a-120">Our `name` for the inline template is made unique by concatenating the current iteration of the `copyIndex()` with the prefix `vnp-`.</span></span> 

<span data-ttu-id="72c2a-121">`condition` 元素指定當 `greater()` 函式評估為 `true` 時，應該處理的資源。</span><span class="sxs-lookup"><span data-stu-id="72c2a-121">The `condition` element specifies that our resource should be processed when the `greater()` function evaluates to `true`.</span></span> <span data-ttu-id="72c2a-122">在這裡，我們測試 `virtualNetworkPeerings` 參數陣列是否 `greater()` 零。</span><span class="sxs-lookup"><span data-stu-id="72c2a-122">Here, we're testing if the `virtualNetworkPeerings` parameter array is `greater()` than zero.</span></span> <span data-ttu-id="72c2a-123">如果是，則評估為 `true`，滿足 `condition`。</span><span class="sxs-lookup"><span data-stu-id="72c2a-123">If it is, it evaluates to `true` and the `condition` is satisfied.</span></span> <span data-ttu-id="72c2a-124">否則，便為 `false`。</span><span class="sxs-lookup"><span data-stu-id="72c2a-124">Otherwise, it's `false`.</span></span>

<span data-ttu-id="72c2a-125">接下來，我們指定 `copy` 迴圈。</span><span class="sxs-lookup"><span data-stu-id="72c2a-125">Next, we specify our `copy` loop.</span></span> <span data-ttu-id="72c2a-126">它是 `serial` 迴圈，表示迴圈會依序完成，每個資源都會等候直到最後一個資源部署完成。</span><span class="sxs-lookup"><span data-stu-id="72c2a-126">It's a `serial` loop that means the loop is done in sequence, with each resource waiting until the last resource has been deployed.</span></span> <span data-ttu-id="72c2a-127">`count` 屬性指定迴圈反覆執行的次數。</span><span class="sxs-lookup"><span data-stu-id="72c2a-127">The `count` property specifies the number of times the loop iterates.</span></span> <span data-ttu-id="72c2a-128">在這裡，通常我們會將它設定為 `virtualNetworkPeerings` 陣列的長度，因為它包含的參數物件指定了我們要部署的資源。</span><span class="sxs-lookup"><span data-stu-id="72c2a-128">Here, normally we'd set it to the length of the `virtualNetworkPeerings` array because it contains the parameter objects specifying the resource we want to deploy.</span></span> <span data-ttu-id="72c2a-129">不過，如果這樣做，若陣列是空的，驗證會失敗，因為 Resource Manager 注意到我們嘗試存取不存在的屬性。</span><span class="sxs-lookup"><span data-stu-id="72c2a-129">However, if we do that, validation will fail if the array is empty because Resource Manager notices that we are attempting to access properties that do not exist.</span></span> <span data-ttu-id="72c2a-130">不過，這個問題可以解決。</span><span class="sxs-lookup"><span data-stu-id="72c2a-130">We can work around this, however.</span></span> <span data-ttu-id="72c2a-131">看一下我們需要的變數：</span><span class="sxs-lookup"><span data-stu-id="72c2a-131">Let's take a look at the variables we'll need:</span></span>

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

<span data-ttu-id="72c2a-132">`workaround` 變數有兩個屬性，一個名為 `true`、一個名為 `false`。</span><span class="sxs-lookup"><span data-stu-id="72c2a-132">Our `workaround` variable includes two properties, one named `true` and one named `false`.</span></span> <span data-ttu-id="72c2a-133">`true` 屬性評估為 `virtualNetworkPeerings` 參數陣列的值。</span><span class="sxs-lookup"><span data-stu-id="72c2a-133">The `true` property evaluates to the value of the `virtualNetworkPeerings` parameter array.</span></span> <span data-ttu-id="72c2a-134">`false` 屬性評估為空物件，包括 Resource Manager 預期會看到的具名屬性 &mdash; 請注意，`false` 其實是一個陣列，和 `virtualNetworkPeerings` 參數一樣，能滿足驗證。</span><span class="sxs-lookup"><span data-stu-id="72c2a-134">The `false` property evaluates to an empty object including the named properties that Resource Manager expects to see &mdash; note that `false` is actually an array, just as our `virtualNetworkPeerings` parameter is, which will satisfy validation.</span></span> 

<span data-ttu-id="72c2a-135">`peerings` 變數會再使用 `workaround` 變數一次，測試 `virtualNetworkPeerings`參數陣列的長度是否大於零。</span><span class="sxs-lookup"><span data-stu-id="72c2a-135">Our `peerings` variable uses our `workaround` variable by once again testing if the length of the `virtualNetworkPeerings` parameter array is greater than zero.</span></span> <span data-ttu-id="72c2a-136">如果是，`string` 評估為 `true`，且 `workaround` 變數評估為 `virtualNetworkPeerings` 參數陣列。</span><span class="sxs-lookup"><span data-stu-id="72c2a-136">If it is, the `string` evaluates to `true` and the `workaround` variable evaluates to the `virtualNetworkPeerings` parameter array.</span></span> <span data-ttu-id="72c2a-137">否則，評估為 `false`，且 `workaround` 變數評估為空物件，是陣列的第一個元素。</span><span class="sxs-lookup"><span data-stu-id="72c2a-137">Otherwise, it evaluates to `false` and the `workaround` variable evaluates to our empty object in the first element of the array.</span></span>

<span data-ttu-id="72c2a-138">既然我們已解決驗證問題，我們可以在巢狀範本中直接指定 `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` 資源的部署，傳遞 `virtualNetworkPeerings` 參數陣列中的 `name` 和 `properties`。</span><span class="sxs-lookup"><span data-stu-id="72c2a-138">Now that we've worked around the validation issue, we can simply specify the deployment of the `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` resource in the nested template, passing the `name` and `properties` from our `virtualNetworkPeerings` parameter array.</span></span> <span data-ttu-id="72c2a-139">在巢狀置於資源的 `properties` 元素中的 `template` 元素中可以看到。</span><span class="sxs-lookup"><span data-stu-id="72c2a-139">You can see this in the `template` element nested in the `properties` element of our resource.</span></span>

## <a name="try-the-template"></a><span data-ttu-id="72c2a-140">試用範本</span><span class="sxs-lookup"><span data-stu-id="72c2a-140">Try the template</span></span>

<span data-ttu-id="72c2a-141">您可以在 [GitHub][github] 上取得範本範例。</span><span class="sxs-lookup"><span data-stu-id="72c2a-141">An example template is available on [GitHub][github].</span></span> <span data-ttu-id="72c2a-142">若要部署範本，請執行下列 [Azure CLI][cli] 命令：</span><span class="sxs-lookup"><span data-stu-id="72c2a-142">To deploy the template, run the following [Azure CLI][cli] commands:</span></span>

```bash
az group create --location <location> --name <resource-group-name>
az group deployment create -g <resource-group-name> \
    --template-uri https://raw.githubusercontent.com/mspnp/template-examples/master/example2-conditional/deploy.json
```

## <a name="next-steps"></a><span data-ttu-id="72c2a-143">後續步驟</span><span class="sxs-lookup"><span data-stu-id="72c2a-143">Next steps</span></span>

* <span data-ttu-id="72c2a-144">使用物件做為範本參數，而不是使用純量值。</span><span class="sxs-lookup"><span data-stu-id="72c2a-144">Use objects instead of scalar values as template parameters.</span></span> <span data-ttu-id="72c2a-145">請參閱[在 Azure Resource Manager 範本中使用物件作為參數](./objects-as-parameters.md)</span><span class="sxs-lookup"><span data-stu-id="72c2a-145">See [Use an object as a parameter in an Azure Resource Manager template](./objects-as-parameters.md)</span></span>

<!-- links -->
[azure-resource-manager-condition]: /azure/azure-resource-manager/resource-group-authoring-templates#resources
[azure-resource-manager-variable]: /azure/azure-resource-manager/resource-group-authoring-templates#variables
[vnet-peering-resource-schema]: /azure/templates/microsoft.network/virtualnetworks/virtualnetworkpeerings
[cli]: /cli/azure/?view=azure-cli-latest
[github]: https://github.com/mspnp/template-examples
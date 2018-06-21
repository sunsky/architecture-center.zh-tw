---
title: 在 Azure Resource Manager 範本中使用物件作為參數
description: 說明如何擴充 Azure Resource Manager 範本的功能，以使用物件作為參數
author: petertay
ms.date: 06/09/2017
ms.openlocfilehash: 76f8b9d459f4ab3147b52762b7c26552ec92c7a3
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
ms.locfileid: "30847170"
---
# <a name="use-an-object-as-a-parameter-in-an-azure-resource-manager-template"></a><span data-ttu-id="628e1-103">在 Azure Resource Manager 範本中使用物件作為參數</span><span class="sxs-lookup"><span data-stu-id="628e1-103">Use an object as a parameter in an Azure Resource Manager template</span></span>

<span data-ttu-id="628e1-104">[編寫 Azure Resource Manager 範本][azure-resource-manager-create-template]時，您可以直接在範本中指定資源屬性值，或定義參數並在部署期間提供值。</span><span class="sxs-lookup"><span data-stu-id="628e1-104">When you [author Azure Resource Manager templates][azure-resource-manager-create-template], you can either specify resource property values directly in the template or define a parameter and provide values during deployment.</span></span> <span data-ttu-id="628e1-105">可以對小型部署的每個屬性值使用參數，但每個部署的限制為 255 個參數。</span><span class="sxs-lookup"><span data-stu-id="628e1-105">It's fine to use a parameter for each property value for small deployments, but there is a limit of 255 parameters per deployment.</span></span> <span data-ttu-id="628e1-106">一旦您的部署變得更大且更複雜，可能用盡參數。</span><span class="sxs-lookup"><span data-stu-id="628e1-106">Once you get to larger and more complex deployments you may run out of parameters.</span></span>

<span data-ttu-id="628e1-107">若要解決此問題，有一個方法是使用物件來作為參數而非值。</span><span class="sxs-lookup"><span data-stu-id="628e1-107">One way to solve this problem is to use an object as a parameter instead of a value.</span></span> <span data-ttu-id="628e1-108">若要這樣做，於部署期間，請在範本中定義參數並指定 JSON 物件，而非單一值。</span><span class="sxs-lookup"><span data-stu-id="628e1-108">To do this, define the parameter in your template and specify a JSON object instead of a single value during deployment.</span></span> <span data-ttu-id="628e1-109">然後，在您的範本中使用 [`parameter()` 函式][azure-resource-manager-functions]和點運算子參考參數的子屬性。</span><span class="sxs-lookup"><span data-stu-id="628e1-109">Then, reference the subproperties of the parameter using the [`parameter()` function][azure-resource-manager-functions] and dot operator in your template.</span></span>

<span data-ttu-id="628e1-110">讓我們看看可部署虛擬網路資源的範例。</span><span class="sxs-lookup"><span data-stu-id="628e1-110">Let's take a look at an example that deploys a virtual network resource.</span></span> <span data-ttu-id="628e1-111">首先，讓我們在範本中指定 `VNetSettings` 參數，並將 `type` 設定為 `object`：</span><span class="sxs-lookup"><span data-stu-id="628e1-111">First, let's specify a `VNetSettings` parameter in our template and set the `type` to `object`:</span></span>

```json
...
"parameters": {
    "VNetSettings":{"type":"object"}
},
```
<span data-ttu-id="628e1-112">接下來，讓我們提供 `VNetSettings` 物件的值：</span><span class="sxs-lookup"><span data-stu-id="628e1-112">Next, let's provide values for the `VNetSettings` object:</span></span>

> [!NOTE]
> <span data-ttu-id="628e1-113">若要了解如何在部署期間提供參數值，請參閱[了解 Azure Resource Manager 範本的結構和語法][azure-resource-manager-authoring-templates]的**參數**一節。</span><span class="sxs-lookup"><span data-stu-id="628e1-113">To learn how to provide parameter values during deploment, see the **parameters** section of [understand the structure and syntax of Azure Resource Manager templates][azure-resource-manager-authoring-templates].</span></span> 

```json
"parameters":{
    "VNetSettings":{
        "value":{
            "name":"VNet1",
            "addressPrefixes": [
                {
                    "name": "firstPrefix",
                    "addressPrefix": "10.0.0.0/22"
                }
            ],
            "subnets":[
                {
                    "name": "firstSubnet",
                    "addressPrefix": "10.0.0.0/24"
                },
                {
                    "name":"secondSubnet",
                    "addressPrefix":"10.0.1.0/24"
                }
            ]
        }
    }
}
```

<span data-ttu-id="628e1-114">如您所見，我們的單一參數實際上會指定三個子屬性：`name`、`addressPrefixes` 和 `subnets`。</span><span class="sxs-lookup"><span data-stu-id="628e1-114">As you can see, our single parameter actually specifies three subproperties: `name`, `addressPrefixes`, and `subnets`.</span></span> <span data-ttu-id="628e1-115">每個子屬性會指定值或其他子屬性。</span><span class="sxs-lookup"><span data-stu-id="628e1-115">Each of these subproperties either specifies a value or other subproperties.</span></span> <span data-ttu-id="628e1-116">結果是我們的單一參數會指定部署虛擬網路所需的所有值。</span><span class="sxs-lookup"><span data-stu-id="628e1-116">The result is that our single parameter specifies all the values necessary to deploy our virtual network.</span></span>

<span data-ttu-id="628e1-117">現在讓我們看看範本的其餘部分，以了解使用 `VNetSettings` 物件的方式：</span><span class="sxs-lookup"><span data-stu-id="628e1-117">Now let's have a look at the rest of our template to see how the `VNetSettings` object is used:</span></span>

```json
...
"resources": [
    {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/virtualNetworks",
        "name": "[parameters('VNetSettings').name]",
        "location":"[resourceGroup().location]",
        "properties": {
          "addressSpace":{
              "addressPrefixes": [
                "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
              ]
          },
          "subnets":[
              {
                  "name":"[parameters('VNetSettings').subnets[0].name]",
                  "properties": {
                      "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
                  }
              },
              {
                  "name":"[parameters('VNetSettings').subnets[1].name]",
                  "properties": {
                      "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
                  }
              }
          ]
        }
    }
  ]
```
<span data-ttu-id="628e1-118">`VNetSettings` 物件的值會使用 `parameters()` 函式搭配 `[]` 陣列索引子和點運算子，套用至我們的虛擬網路資源所需的屬性。</span><span class="sxs-lookup"><span data-stu-id="628e1-118">The values of our `VNetSettings` object are applied to the properties required by our virtual network resource using the `parameters()` function with both the `[]` array indexer and the dot operator.</span></span> <span data-ttu-id="628e1-119">如果您只想要以靜態方式將參數物件的值套用至資源，則此方法可行。</span><span class="sxs-lookup"><span data-stu-id="628e1-119">This approach works if you just want to statically apply the values of the parameter object to the resource.</span></span> <span data-ttu-id="628e1-120">不過，如果您想要在部署期間動態指派屬性值的陣列，則可以使用[複製迴圈][azure-resource-manager-create-multiple-instances]。</span><span class="sxs-lookup"><span data-stu-id="628e1-120">However, if you want to dynamically assign an array of property values during deployment you can use a [copy loop][azure-resource-manager-create-multiple-instances].</span></span> <span data-ttu-id="628e1-121">若要使用複製迴圈，您可以提供資源屬性值的 JSON 陣列，複製迴圈即會動態將值套用至資源的屬性。</span><span class="sxs-lookup"><span data-stu-id="628e1-121">To use a copy loop, you provide a JSON array of resource property values and the copy loop dynamically applies the values to the resource's properties.</span></span> 

<span data-ttu-id="628e1-122">如果您使用動態方法，需注意一個問題。</span><span class="sxs-lookup"><span data-stu-id="628e1-122">There is one issue to be aware of if you use the dynamic approach.</span></span> <span data-ttu-id="628e1-123">為了示範此問題，讓我們看一下屬性值的一般陣列。</span><span class="sxs-lookup"><span data-stu-id="628e1-123">To demonstrate the issue, let's take a look at a typical array of property values.</span></span> <span data-ttu-id="628e1-124">在此範例中，我們屬性的值會儲存在變數中。</span><span class="sxs-lookup"><span data-stu-id="628e1-124">In this example the values for our properties are stored in a variable.</span></span> <span data-ttu-id="628e1-125">請注意我們這裡有兩個陣列&mdash;一個名為 `firstProperty`，另一個名為 `secondProperty`。</span><span class="sxs-lookup"><span data-stu-id="628e1-125">Notice we have two arrays here&mdash;one named `firstProperty` and one named `secondProperty`.</span></span> 

```json
"variables": {
    "firstProperty": [
        {
            "name": "A",
            "type": "typeA"
        },
        {
            "name": "B",
            "type": "typeB"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ],
    "secondProperty": [
        "one","two", "three"
    ]
}
```

<span data-ttu-id="628e1-126">現在讓我們看看使用複製迴圈存取變數中屬性的方式。</span><span class="sxs-lookup"><span data-stu-id="628e1-126">Now let's take a look at the way we access the properties in the variable using a copy loop.</span></span>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    ...
    "copy": {
        "name": "copyLoop1",
        "count": "[length(variables('firstProperty'))]"
    },
    ...
    "properties": {
        "name": { "value": "[variables('firstProperty')[copyIndex()].name]" },
        "type": { "value": "[variables('firstProperty')[copyIndex()].type]" },
        "number": { "value": "[variables('secondProperty')[copyIndex()]]" }
    }
}
```

<span data-ttu-id="628e1-127">`copyIndex()` 函式會傳回複製迴圈目前的反覆運算，而我們同時用它來做為這兩個陣列的索引。</span><span class="sxs-lookup"><span data-stu-id="628e1-127">The `copyIndex()` function returns the current iteration of the copy loop, and we use that as an index into each of the two arrays simultaneously.</span></span>

<span data-ttu-id="628e1-128">兩個陣列有相同的長度時，這可正常運作。</span><span class="sxs-lookup"><span data-stu-id="628e1-128">This works fine when the two arrays are the same length.</span></span> <span data-ttu-id="628e1-129">如果您操作錯誤且兩個陣列的長度不同，就會發生此問題&mdash;在此情況下，您的範本在部署期間將無法通過驗證。</span><span class="sxs-lookup"><span data-stu-id="628e1-129">The issue arises if you've made a mistake and the two arrays are different lengths&mdash;in this case your template will fail validation during deployment.</span></span> <span data-ttu-id="628e1-130">在單一物件中包括您的所有屬性即可以避免此問題，因為在遺漏值時要查看更為容易。</span><span class="sxs-lookup"><span data-stu-id="628e1-130">You can avoid this issue by including all your properties in a single object, because it is much easier to see when a value is missing.</span></span> <span data-ttu-id="628e1-131">比方說，讓我們看看另一個參數物件中，`propertyObject` 陣列的每個元素是稍早的 `firstProperty` 和 `secondProperty` 陣列的聯集。</span><span class="sxs-lookup"><span data-stu-id="628e1-131">For example, let's take a look another parameter object in which each element of the `propertyObject` array is the union of the `firstProperty` and `secondProperty` arrays from earlier.</span></span>

```json
"variables": {
    "propertyObject": [
        {
            "name": "A",
            "type": "typeA",
            "number": "one"
        },
        {
            "name": "B",
            "type": "typeB",
            "number": "two"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ]
}
```

<span data-ttu-id="628e1-132">注意到陣列中的第三個元素嗎？</span><span class="sxs-lookup"><span data-stu-id="628e1-132">Notice the third element in the array?</span></span> <span data-ttu-id="628e1-133">它遺漏了 `number`屬性，但在您編寫參數值時，這個方式可讓您更容易注意到遺漏了它。</span><span class="sxs-lookup"><span data-stu-id="628e1-133">It's missing the `number` property, but it's much easier to notice that you've missed it when you're authoring the parameter values this way.</span></span>

## <a name="using-a-property-object-in-a-copy-loop"></a><span data-ttu-id="628e1-134">在複製迴圈中使用屬性物件</span><span class="sxs-lookup"><span data-stu-id="628e1-134">Using a property object in a copy loop</span></span>

<span data-ttu-id="628e1-135">此方法在與[serial copy loop][azure-resource-manager-create-multiple]結合使用時會變得更加實用，特別是在部署子資源時。</span><span class="sxs-lookup"><span data-stu-id="628e1-135">This approach becomes even more useful when combined with the [serial copy loop][azure-resource-manager-create-multiple], particularly for deploying child resources.</span></span> 

<span data-ttu-id="628e1-136">為了示範這點，讓我們看看使用兩個安全性規則部署[網路安全性群組 (NSG)][nsg] 的範本。</span><span class="sxs-lookup"><span data-stu-id="628e1-136">To demonstrate this, let's look at a template that deploys a [network security group (NSG)][nsg] with two security rules.</span></span> 

<span data-ttu-id="628e1-137">首先，讓我們看看我們的參數。</span><span class="sxs-lookup"><span data-stu-id="628e1-137">First, let's take a look at our parameters.</span></span> <span data-ttu-id="628e1-138">查看範本時，我們會看到已定義一個名為 `networkSecurityGroupsSettings` 的參數，其包含名為 `securityRules` 的陣列。</span><span class="sxs-lookup"><span data-stu-id="628e1-138">When we look at our template we'll see that we've defined one parameter named `networkSecurityGroupsSettings` that includes an array named `securityRules`.</span></span> <span data-ttu-id="628e1-139">此陣列包含指定安全性規則一些設定的兩個 JSON 物件。</span><span class="sxs-lookup"><span data-stu-id="628e1-139">This array contains two JSON objects that specify a number of settings for a security rule.</span></span>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{ 
      "networkSecurityGroupsSettings": {
      "value": {
          "securityRules": [
            {
              "name": "RDPAllow",
              "description": "allow RDP connections",
              "direction": "Inbound",
              "priority": 100,
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "10.0.0.0/24",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "access": "Allow",
              "protocol": "Tcp"
            },
            {
              "name": "HTTPAllow",
              "description": "allow HTTP connections",
              "direction": "Inbound",
              "priority": 200,
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "10.0.1.0/24",
              "sourcePortRange": "*",
              "destinationPortRange": "80",
              "access": "Allow",
              "protocol": "Tcp"
            }
          ]
        }
      }
    }
  }
```

<span data-ttu-id="628e1-140">現在，讓我們看看我們的範本。</span><span class="sxs-lookup"><span data-stu-id="628e1-140">Now let's take a look at our template.</span></span> <span data-ttu-id="628e1-141">我們名為 `NSG1` 的第一個資源會部署 NSG。</span><span class="sxs-lookup"><span data-stu-id="628e1-141">Our first resource named `NSG1` deploys the NSG.</span></span> <span data-ttu-id="628e1-142">我們名為 `loop-0` 的第二個資源會執行兩個函式︰第一，它會 `dependsOn` NSG，因此要等到 `NSG1` 完成後才會開始執行部署，而且它是循序迴圈的第一個反覆項目。</span><span class="sxs-lookup"><span data-stu-id="628e1-142">Our second resource named `loop-0` performs two functions: first, it `dependsOn` the NSG so its deployment doesn't begin until `NSG1` is completed, and it is the first iteration of the sequential loop.</span></span> <span data-ttu-id="628e1-143">我們的第三個資源是巢狀範本，和最後一個範例一樣，它會使用物件來作為其參數值以部署安全性規則。</span><span class="sxs-lookup"><span data-stu-id="628e1-143">Our third resource is a nested template that deploys our security rules using an object for its parameter values as in the last example.</span></span>

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "networkSecurityGroupsSettings": {"type":"object"}
  },
  "variables": {},
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "NSG1",
      "location":"[resourceGroup().location]",
      "properties": {
          "securityRules":[]
      }
    },
    {
        "apiVersion": "2015-01-01",
        "type": "Microsoft.Resources/deployments",
        "name": "loop-0",
        "dependsOn": [
            "NSG1"
        ],
        "properties": {
            "mode":"Incremental",
            "parameters":{},
            "template": {
                "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {},
                "resources": [],
                "outputs": {}
            }
        }       
    },
    {
        "apiVersion": "2015-01-01",
        "type": "Microsoft.Resources/deployments",
        "name": "[concat('loop-', copyIndex(1))]",
        "dependsOn": [
          "[concat('loop-', copyIndex())]"
        ],
        "copy": {
          "name": "iterator",
          "count": "[length(parameters('networkSecurityGroupsSettings').securityRules)]"
        },
        "properties": {
          "mode": "Incremental",
          "template": {
            "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
           "parameters": {},
            "variables": {},
            "resources": [
                {
                    "name": "[concat('NSG1/' , parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].name)]",
                    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
                    "apiVersion": "2016-09-01",
                    "location":"[resourceGroup().location]",
                    "properties":{
                        "description": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].description]",
                        "priority":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].priority]",
                        "protocol":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].protocol]",
                        "sourcePortRange": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].sourcePortRange]",
                        "destinationPortRange": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].destinationPortRange]",
                        "sourceAddressPrefix": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].sourceAddressPrefix]",
                        "destinationAddressPrefix": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].destinationAddressPrefix]",
                        "access":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].access]",
                        "direction":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].direction]"
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

<span data-ttu-id="628e1-144">讓我們來看看我們如何在 `securityRules` 子資源中指定我們的屬性值。</span><span class="sxs-lookup"><span data-stu-id="628e1-144">Let's take a closer look at how we specify our property values in the `securityRules` child resource.</span></span> <span data-ttu-id="628e1-145">我們的所有屬性是使用 `parameter()` 函式參考，然後我們會使用點運算子來參考我們的 `securityRules` 陣列，依反覆項目的目前值編製索引。</span><span class="sxs-lookup"><span data-stu-id="628e1-145">All of our properties are referenced using the `parameter()` function, and then we use the dot operator to reference our `securityRules` array, indexed by the current value of the iteration.</span></span> <span data-ttu-id="628e1-146">最後，我們會使用另一個點運算子來參考物件的名稱。</span><span class="sxs-lookup"><span data-stu-id="628e1-146">Finally, we use another dot operator to reference the name of the object.</span></span> 

## <a name="try-the-template"></a><span data-ttu-id="628e1-147">試用範本</span><span class="sxs-lookup"><span data-stu-id="628e1-147">Try the template</span></span>

<span data-ttu-id="628e1-148">如果您想要實驗此範本，請遵循下列步驟︰</span><span class="sxs-lookup"><span data-stu-id="628e1-148">If you would like to experiment with this template, follow these steps:</span></span> 

1.  <span data-ttu-id="628e1-149">請移至 Azure 入口網站，選取 **+** 圖示，並搜尋 [範本部署] 資源類型，並選取它。</span><span class="sxs-lookup"><span data-stu-id="628e1-149">Go to the Azure portal, select the **+** icon, and search for the **template deployment** resource type, and select it.</span></span>
2.  <span data-ttu-id="628e1-150">導覽至 [範本部署] 頁面，選取 [建立] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="628e1-150">Navigate to the **template deployment** page, select the **create** button.</span></span> <span data-ttu-id="628e1-151">這個按鈕會開啟 [自訂部署] 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="628e1-151">This button opens the **custom deployment** blade.</span></span>
3.  <span data-ttu-id="628e1-152">選取 [編輯範本] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="628e1-152">Select the **edit template** button.</span></span>
4.  <span data-ttu-id="628e1-153">刪除空白範本。</span><span class="sxs-lookup"><span data-stu-id="628e1-153">Delete the empty template.</span></span> 
5.  <span data-ttu-id="628e1-154">複製範例範本並貼到右窗格。</span><span class="sxs-lookup"><span data-stu-id="628e1-154">Copy and paste the sample template into the right pane.</span></span>
6.  <span data-ttu-id="628e1-155">選取 [儲存] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="628e1-155">Select the **save** button.</span></span>
7.  <span data-ttu-id="628e1-156">當您返回 [自訂部署] 窗格時，選取 [編輯參數] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="628e1-156">When you are returned to the **custom deployment** pane, select the **edit parameters** button.</span></span>
8.  <span data-ttu-id="628e1-157">在 [編輯參數] 刀鋒視窗中，刪除現有範本。</span><span class="sxs-lookup"><span data-stu-id="628e1-157">On the **edit parameters** blade, delete the existing template.</span></span>
9.  <span data-ttu-id="628e1-158">複製並貼上前面的參數範本樣本。</span><span class="sxs-lookup"><span data-stu-id="628e1-158">Copy and paste the sample parameter template from above.</span></span>
10. <span data-ttu-id="628e1-159">選取 [儲存] 按鈕，此動作會讓您返回 [自訂部署] 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="628e1-159">Select the **save** button, which returns you to the **custom deployment** blade.</span></span>
11. <span data-ttu-id="628e1-160">在 [自訂部署] 刀鋒視窗上選取您的訂用帳戶，接著新建資源群組或使用現有資源群組，然後選取位置。</span><span class="sxs-lookup"><span data-stu-id="628e1-160">On the **custom deployment** blade, select your subscription, either create new or use existing resource group, and select a location.</span></span> <span data-ttu-id="628e1-161">檢閱條款及條件，然後選取 [我同意] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="628e1-161">Review the terms and conditions, and select the **I agree** checkbox.</span></span>
12. <span data-ttu-id="628e1-162">選取 [購買] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="628e1-162">Select the **purchase** button.</span></span>

## <a name="next-steps"></a><span data-ttu-id="628e1-163">後續步驟</span><span class="sxs-lookup"><span data-stu-id="628e1-163">Next steps</span></span>

* <span data-ttu-id="628e1-164">您可以展開這些技術來實作[屬性物件轉換程式與收集器](./collector.md)。</span><span class="sxs-lookup"><span data-stu-id="628e1-164">You can expand upon these techniques to implement a [property object transformer and collector](./collector.md).</span></span> <span data-ttu-id="628e1-165">轉換程式與收集器技術更為一般，而且可以從您的範本連結。</span><span class="sxs-lookup"><span data-stu-id="628e1-165">The transformer and collector techniques are more general and can be linked from your templates.</span></span>
* <span data-ttu-id="628e1-166">此技術也可以在[範本建置區塊專案](https://github.com/mspnp/template-building-blocks)與 [Azure 參考架構](/azure/architecture/reference-architectures/)中實作。</span><span class="sxs-lookup"><span data-stu-id="628e1-166">This technique is also implemented in the [template building blocks project](https://github.com/mspnp/template-building-blocks) and the [Azure reference architectures](/azure/architecture/reference-architectures/).</span></span> <span data-ttu-id="628e1-167">您可以檢閱我們的範本，來查看我們如何實作這項技術。</span><span class="sxs-lookup"><span data-stu-id="628e1-167">You can review our templates to see how we've implemented this technique.</span></span>

<!-- links -->
[azure-resource-manager-authoring-templates]: /azure/azure-resource-manager/resource-group-authoring-templates
[azure-resource-manager-create-template]: /azure/azure-resource-manager/resource-manager-create-first-template
[azure-resource-manager-create-multiple-instances]: /azure/azure-resource-manager/resource-group-create-multiple
[azure-resource-manager-functions]: /azure/azure-resource-manager/resource-group-template-functions-resource
[nsg]: /azure/virtual-network/virtual-networks-nsg
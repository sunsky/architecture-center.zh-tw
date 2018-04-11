---
title: 在 Azure Resource Manager 範本中實作屬性轉換器與收集器
description: 說明如何在 Azure Resource Manager 範本中實作屬性轉換器與收集器
author: petertay
ms.date: 06/09/2017
ms.openlocfilehash: 893779e652b845b3d936d11936dc767ef632fa43
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="implement-a-property-transformer-and-collector-in-an-azure-resource-manager-template"></a><span data-ttu-id="bce56-103">在 Azure Resource Manager 範本中實作屬性轉換器與收集器</span><span class="sxs-lookup"><span data-stu-id="bce56-103">Implement a property transformer and collector in an Azure Resource Manager template</span></span>

<span data-ttu-id="bce56-104">在[使用物件作為 Azure Resource Manager 範本中的參數][objects-as-parameters]中，您學會了如何將資源屬性值儲存在物件中，並在部署期間將它們套用至資源。</span><span class="sxs-lookup"><span data-stu-id="bce56-104">In [use an object as a parameter in an Azure Resource Manager template][objects-as-parameters], you learned how to store resource property values in an object and apply them to a resource during deployment.</span></span> <span data-ttu-id="bce56-105">雖然這是非常實用的參數管理方式，但每次您在範本中使用它時，您仍需要將物件的屬性對應到資源屬性。</span><span class="sxs-lookup"><span data-stu-id="bce56-105">While this is a very useful way to manage your parameters, it still requires you to map the object's properties to resource properties each time you use it in your template.</span></span>

<span data-ttu-id="bce56-106">為了因應這種情況，您可以實作屬性轉換和收集器範本，逐一查看物件陣列，並將其轉換為資源所預期的 JSON 結構描述。</span><span class="sxs-lookup"><span data-stu-id="bce56-106">To work around this, you can implement a property transform and collector template that iterates your object array and transforms it into the JSON schema expected by the resource.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="bce56-107">採取這種做法，您需要十分熟悉 Resource Manager 範本和函式。</span><span class="sxs-lookup"><span data-stu-id="bce56-107">This approach requires that you have a deep understanding of Resource Manager templates and functions.</span></span>

<span data-ttu-id="bce56-108">讓我們透過部署[網路安全性群組 (NSG)][nsg]的範例，看看如何實作屬性收集器和轉換器。</span><span class="sxs-lookup"><span data-stu-id="bce56-108">Let's take a look at how we can implement a property collector and transformer with an example that deploys a [network security group (NSG)][nsg].</span></span> <span data-ttu-id="bce56-109">下圖顯示我們的範本和我們在這些範本內的資源之間的關聯性：</span><span class="sxs-lookup"><span data-stu-id="bce56-109">The diagram below shows the relationship between our templates and our resources within those templates:</span></span>

![屬性收集器和轉換器架構](../_images/collector-transformer.png)

<span data-ttu-id="bce56-111">我們的**呼叫範本**有兩個資源：</span><span class="sxs-lookup"><span data-stu-id="bce56-111">Our **calling template** includes two resources:</span></span>
* <span data-ttu-id="bce56-112">範本超連結，可呼叫的我們的**收集器範本**。</span><span class="sxs-lookup"><span data-stu-id="bce56-112">a template link that invokes our **collector template**.</span></span>
* <span data-ttu-id="bce56-113">要部署的 NSG 資源。</span><span class="sxs-lookup"><span data-stu-id="bce56-113">the NSG resource to deploy.</span></span>

<span data-ttu-id="bce56-114">我們的**收集器範本**有兩個資源：</span><span class="sxs-lookup"><span data-stu-id="bce56-114">Our **collector template** includes two resources:</span></span>
* <span data-ttu-id="bce56-115">**錨點**資源。</span><span class="sxs-lookup"><span data-stu-id="bce56-115">an **anchor** resource.</span></span>
* <span data-ttu-id="bce56-116">範本超連結，可在複製迴圈中呼叫轉換範本。</span><span class="sxs-lookup"><span data-stu-id="bce56-116">a template link that invokes the transform template in a copy loop.</span></span>

<span data-ttu-id="bce56-117">我們的**轉換範本**有一個資源：包含單一變數的空白範本，會將我們的 `source` JSON 結構描述轉換為我們的**主要範本**中 NSG 資源所預期的 JSON 結構描述。</span><span class="sxs-lookup"><span data-stu-id="bce56-117">Our **transform template** includes a single resource: an empty template with a variable that transforms our `source` JSON to the JSON schema expected by our NSG resource in the **main template**.</span></span>

## <a name="parameter-object"></a><span data-ttu-id="bce56-118">參數物件</span><span class="sxs-lookup"><span data-stu-id="bce56-118">Parameter object</span></span>

<span data-ttu-id="bce56-119">我們將使用 [ 物件中的 `securityRules` 參數作為參數][objects-as-parameters]。</span><span class="sxs-lookup"><span data-stu-id="bce56-119">We'll be using our `securityRules` parameter object from [objects as parameters][objects-as-parameters].</span></span> <span data-ttu-id="bce56-120">**轉換範本**會將 `securityRules` 陣列中的每個物件轉換為**呼叫範本**中 NSG 資源所預期的 JSON 結構描述。</span><span class="sxs-lookup"><span data-stu-id="bce56-120">Our **transform template** will transform each object in the `securityRules` array into the JSON schema expected by the NSG resource in our **calling template**.</span></span>

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

<span data-ttu-id="bce56-121">先來看看**轉換範本**。</span><span class="sxs-lookup"><span data-stu-id="bce56-121">Let's look at our **transform template** first.</span></span>

## <a name="transform-template"></a><span data-ttu-id="bce56-122">轉換範本</span><span class="sxs-lookup"><span data-stu-id="bce56-122">Transform template</span></span>

<span data-ttu-id="bce56-123">我們的**轉換範本**包含從**收集器範本**傳來的兩個參數：</span><span class="sxs-lookup"><span data-stu-id="bce56-123">Our **transform template** includes two parameters that are passed from the **collector template**:</span></span> 
* <span data-ttu-id="bce56-124">`source` 是物件，會接收屬性陣列的其中一個屬性值物件。</span><span class="sxs-lookup"><span data-stu-id="bce56-124">`source` is an object that receives one of the property value objects from the property array.</span></span> <span data-ttu-id="bce56-125">我們的範例會傳遞 `"securityRules"` 陣列中的每個物件，一次傳遞一個。</span><span class="sxs-lookup"><span data-stu-id="bce56-125">In our example, each object from the `"securityRules"` array will be passed in one at a time.</span></span>
* <span data-ttu-id="bce56-126">`state` 是陣列，會接收所有先前轉換後的結果串連。</span><span class="sxs-lookup"><span data-stu-id="bce56-126">`state` is an array that receives the concatenated results of all the previous transforms.</span></span> <span data-ttu-id="bce56-127">這是轉換後 JSON 的集合。</span><span class="sxs-lookup"><span data-stu-id="bce56-127">This is the collection of transformed JSON.</span></span>

<span data-ttu-id="bce56-128">我們的參數看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="bce56-128">Our parameters look like this:</span></span>

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "source": { "type": "object" },
    "state": {
      "type": "array",
      "defaultValue": [ ]
    }
  },
```

<span data-ttu-id="bce56-129">我們的範本也會定義名為 `instance` 的變數。</span><span class="sxs-lookup"><span data-stu-id="bce56-129">Our template also defines a variable named `instance`.</span></span> <span data-ttu-id="bce56-130">它會執行實際的轉換，將 `source` 物件轉換成所需的 JSON 結構描述：</span><span class="sxs-lookup"><span data-stu-id="bce56-130">It performs the actual tranform of our `source` object into the required JSON schema:</span></span>

```json
  "variables": {
    "instance": [
      {
        "name": "[parameters('source').name]",
        "properties":{
            "description": "[parameters('source').description]",
            "protocol": "[parameters('source').protocol]",
            "sourcePortRange": "[parameters('source').sourcePortRange]",
            "destinationPortRange": "[parameters('source').destinationPortRange]",
            "sourceAddressPrefix": "[parameters('source').sourceAddressPrefix]",
            "destinationAddressPrefix": "[parameters('source').destinationAddressPrefix]",
            "access": "[parameters('source').access]",
            "priority": "[parameters('source').priority]",
            "direction": "[parameters('source').direction]"            
        }
      }
    ]

  },
```

<span data-ttu-id="bce56-131">最後，範本中的 `output` 會將收集到的 `state`參數的轉換，以及 `instance` 變數目前執行的轉換串連起來：</span><span class="sxs-lookup"><span data-stu-id="bce56-131">Finally, the `output` of our template concatenates the collected transforms of our `state` parameter with the current transform performed by our `instance` variable:</span></span>

```json
  "outputs": {
    "collection": {
      "type": "array",
      "value": "[concat(parameters('state'), variables('instance'))]"
    }
```

<span data-ttu-id="bce56-132">接下來，看看**收集器**如何傳遞參數值。</span><span class="sxs-lookup"><span data-stu-id="bce56-132">Next, let's take a look at our **collector template** to see how it passes in our parameter values.</span></span>

## <a name="collector-template"></a><span data-ttu-id="bce56-133">收集器範本</span><span class="sxs-lookup"><span data-stu-id="bce56-133">Collector template</span></span>

<span data-ttu-id="bce56-134">我們的**收集器範本**包含三個參數：</span><span class="sxs-lookup"><span data-stu-id="bce56-134">Our **collector template** includes three parameters:</span></span>
* <span data-ttu-id="bce56-135">`source` 是完整的參數物件陣列，</span><span class="sxs-lookup"><span data-stu-id="bce56-135">`source` is our complete parameter object array.</span></span> <span data-ttu-id="bce56-136">由**呼叫範本**傳入。</span><span class="sxs-lookup"><span data-stu-id="bce56-136">It's passed in by the **calling template**.</span></span> <span data-ttu-id="bce56-137">它的名稱和**轉換範本**中的 `source`參數相同，但您可能已經注意到一個主要的差異：這是完整的陣列，但是我們一次只傳遞這個陣列的一個元素給**轉換範本**。</span><span class="sxs-lookup"><span data-stu-id="bce56-137">This has the same name as the `source` parameter in our **transform template** but there is one key difference that you may have already noticed: this is the complete array, but we only pass one element of this array to the **transform template** at a time.</span></span>
* <span data-ttu-id="bce56-138">`transformTemplateUri` 是**轉換範本**的 URI。</span><span class="sxs-lookup"><span data-stu-id="bce56-138">`transformTemplateUri` is the URI of our **transform template**.</span></span> <span data-ttu-id="bce56-139">我們在此將它定義為範本，以利重複使用範本。</span><span class="sxs-lookup"><span data-stu-id="bce56-139">We're defining it as a parameter here for template reusability.</span></span>
* <span data-ttu-id="bce56-140">`state` 一開始是空陣列，由我們傳遞給**轉換範本**。</span><span class="sxs-lookup"><span data-stu-id="bce56-140">`state` is an initially empty array that we pass to our **transform template**.</span></span> <span data-ttu-id="bce56-141">在複製迴圈完成時，它會儲存轉換後之參數物件的集合。</span><span class="sxs-lookup"><span data-stu-id="bce56-141">It stores the collection of transformed parameter objects when the copy loop is complete.</span></span>

<span data-ttu-id="bce56-142">我們的參數看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="bce56-142">Our parameters look like this:</span></span>

```json
  "parameters": {
    "source": { "type": "array" },
    "transformTemplateUri": { "type": "string" },
    "state": {
      "type": "array",
      "defaultValue": [ ]
    }
``` 

<span data-ttu-id="bce56-143">接下來，我們要定義名為 `count` 的變數。</span><span class="sxs-lookup"><span data-stu-id="bce56-143">Next, we define a variable named `count`.</span></span> <span data-ttu-id="bce56-144">其值是 `source` 參數物件陣列的長度：</span><span class="sxs-lookup"><span data-stu-id="bce56-144">Its value is the length of the `source` parameter object array:</span></span>

```json
  "variables": {
    "count": "[length(parameters('source'))]"
  },
```

<span data-ttu-id="bce56-145">您大概猜到了，我們用它來作為複製迴圈中的反覆項目數。</span><span class="sxs-lookup"><span data-stu-id="bce56-145">As you might suspect, we use it for the number of iterations in our copy loop.</span></span>

<span data-ttu-id="bce56-146">現在，來看看我們的資源。</span><span class="sxs-lookup"><span data-stu-id="bce56-146">Now let's take a look at our resources.</span></span> <span data-ttu-id="bce56-147">我們定義兩個資源︰</span><span class="sxs-lookup"><span data-stu-id="bce56-147">We define two resources:</span></span>
* <span data-ttu-id="bce56-148">`loop-0` 是複製迴圈的從零開始編號的資源。</span><span class="sxs-lookup"><span data-stu-id="bce56-148">`loop-0` is the zero-based resource for our copy loop.</span></span>
* <span data-ttu-id="bce56-149">`loop-` 會與 `copyIndex(1)` 函式的結果串連，產生以反覆項目編號的唯一資源名稱，從 `1` 開始。</span><span class="sxs-lookup"><span data-stu-id="bce56-149">`loop-` is concatenated with the result of the `copyIndex(1)` function to generate a unique iteration-based name for our resource, starting with `1`.</span></span>

<span data-ttu-id="bce56-150">我們的資源看起來會像這樣：</span><span class="sxs-lookup"><span data-stu-id="bce56-150">Our resources look like this:</span></span>

```json
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2015-01-01",
      "name": "loop-0",
      "properties": {
        "mode": "Incremental",
        "parameters": { },
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": { },
          "variables": { },
          "resources": [ ],
          "outputs": {
            "collection": {
              "type": "array",
              "value": "[parameters('state')]"
            }
          }
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2015-01-01",
      "name": "[concat('loop-', copyindex(1))]",
      "copy": {
        "name": "iterator",
        "count": "[variables('count')]",
        "mode": "serial"
      },
      "dependsOn": [
        "loop-0"
      ],
      "properties": {
        "mode": "Incremental",
        "templateLink": { "uri": "[parameters('transformTemplateUri')]" },
        "parameters": {
          "source": { "value": "[parameters('source')[copyindex()]]" },
          "state": { "value": "[reference(concat('loop-', copyindex())).outputs.collection.value]" }
        }
      }
    }
  ],
```

<span data-ttu-id="bce56-151">讓我們來看看我們傳遞給巢狀範本中**轉換範本**的參數。</span><span class="sxs-lookup"><span data-stu-id="bce56-151">Let's take a closer look at the parameters we're passing to our **transform template** in the nested template.</span></span> <span data-ttu-id="bce56-152">回想前面，`source` 參數將目前的物件傳遞給 `source` 參數物件陣列。</span><span class="sxs-lookup"><span data-stu-id="bce56-152">Recall from earlier that our `source` parameter passes the current object in the `source` parameter object array.</span></span> <span data-ttu-id="bce56-153">`state` 參數就是集合發生之處，因為它會取得先前複製迴圈的反覆運算輸出 &mdash; 請注意，`reference()` 函式使用 `copyIndex()` 函式時沒有使用參數來參考先前連結的範本物件的 `name` &mdash; 然後將其傳遞給目前的反覆運算。</span><span class="sxs-lookup"><span data-stu-id="bce56-153">The `state` parameter is where the collection happens, because it takes the output of the previous iteration of our copy loop&mdash;notice that the `reference()` function uses the `copyIndex()` function with no parameter to reference the `name` of our previous linked template object&mdash;and passes it to the current iteration.</span></span>

<span data-ttu-id="bce56-154">最後，範本的 `output` 傳回**轉換範本**最後一個反覆運算的 `output`：</span><span class="sxs-lookup"><span data-stu-id="bce56-154">Finally, the `output` of our template returns the `output` of the last iteration of our **transform template**:</span></span>

```json
  "outputs": {
    "result": {
      "type": "array",
      "value": "[reference(concat('loop-', variables('count'))).outputs.collection.value]"
    }
  }
```
<span data-ttu-id="bce56-155">將**轉換範本**最後一個反覆運算的 `output` 傳回至**呼叫範本**看似矛盾，因為看來我們要將它儲存在 `source` 參數。</span><span class="sxs-lookup"><span data-stu-id="bce56-155">It may seem counterintuitive to return the `output` of the last iteration of our **transform template** to our **calling template** because it appeared we were storing it in our `source` parameter.</span></span> <span data-ttu-id="bce56-156">不過，請記住，**轉換範本**最後一個反覆運算才有完整的轉換後屬性物件陣列，而這就是我們要傳回的東西。</span><span class="sxs-lookup"><span data-stu-id="bce56-156">However, remember that it's the last iteration of our **transform template** that holds the complete array of transformed property objects, and that's what we want to return.</span></span>

<span data-ttu-id="bce56-157">最後，讓我們看看如何從**呼叫範本**呼叫**收集器範本**。</span><span class="sxs-lookup"><span data-stu-id="bce56-157">Finally, let's take a look at how to call the **collector template** from our **calling template**.</span></span>

## <a name="calling-template"></a><span data-ttu-id="bce56-158">呼叫範本</span><span class="sxs-lookup"><span data-stu-id="bce56-158">Calling template</span></span>

<span data-ttu-id="bce56-159">我們的**呼叫範本**會定義一個名為 `networkSecurityGroupsSettings` 的參數：</span><span class="sxs-lookup"><span data-stu-id="bce56-159">Our **calling template** defines a single parameter named `networkSecurityGroupsSettings`:</span></span>

```json
...
"parameters": {
    "networkSecurityGroupsSettings": {
        "type": "object"
    }
```

<span data-ttu-id="bce56-160">我們的範本會定義一個名為 `collectorTemplateUri` 的參數：</span><span class="sxs-lookup"><span data-stu-id="bce56-160">Next, our template defines a single variable named `collectorTemplateUri`:</span></span>

```json
"variables": {
    "collectorTemplateUri": "[uri(deployment().properties.templateLink.uri, 'collector.template.json')]"
  }
```

<span data-ttu-id="bce56-161">如您所預期，這是**收集器範本**的 URI，將由我們連結的範本資源使用：</span><span class="sxs-lookup"><span data-stu-id="bce56-161">As you would expect, this is the URI for the **collector template** that will be used by our linked template resource:</span></span>

```json
{
    "apiVersion": "2015-01-01",
    "name": "collector",
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "[variables('linkedTemplateUri')]",
            "contentVersion": "1.0.0.0"
        },
        "parameters": {
            "source" : {"value": "[parameters('networkSecurityGroupsSettings').securityRules]"},
            "transformTemplateUri": { "value": "[uri(deployment().properties.templateLink.uri, 'transform.json')]"}
        }
    }
}
```

<span data-ttu-id="bce56-162">我們傳遞兩個參數給**收集器範本**：</span><span class="sxs-lookup"><span data-stu-id="bce56-162">We pass two parameters to the **collector template**:</span></span>
* <span data-ttu-id="bce56-163">`source` 是屬性物件陣列。</span><span class="sxs-lookup"><span data-stu-id="bce56-163">`source` is our property object array.</span></span> <span data-ttu-id="bce56-164">在我們的範例中是 `networkSecurityGroupsSettings` 參數。</span><span class="sxs-lookup"><span data-stu-id="bce56-164">In our example, it's our `networkSecurityGroupsSettings` parameter.</span></span>
* <span data-ttu-id="bce56-165">`transformTemplateUri` 是我們剛才用**收集器範本** URI 定義的變數。</span><span class="sxs-lookup"><span data-stu-id="bce56-165">`transformTemplateUri` is the variable we just defined with the URI of our **collector template**.</span></span>

<span data-ttu-id="bce56-166">最後，`Microsoft.Network/networkSecurityGroups` 資源將 `collector` 連結範本資源的 `output` 直接指派給其 `securityRules` 屬性：</span><span class="sxs-lookup"><span data-stu-id="bce56-166">Finally, our `Microsoft.Network/networkSecurityGroups` resource directly assigns the `output` of the `collector` linked template resource to its `securityRules` property:</span></span>

```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "networkSecurityGroup1",
      "location": "[resourceGroup().location]",
      "properties": {
        "securityRules": "[reference('firstResource').outputs.result.value]"
      }
    }
  ],
  "outputs": {
      "instance":{
          "type": "array",
          "value": "[reference('firstResource').outputs.result.value]"
      }

  }
```

## <a name="next-steps"></a><span data-ttu-id="bce56-167">後續步驟</span><span class="sxs-lookup"><span data-stu-id="bce56-167">Next steps</span></span>

* <span data-ttu-id="bce56-168">此技術可以在[範本建置區塊專案](https://github.com/mspnp/template-building-blocks)與 [Azure 參考架構](/azure/architecture/reference-architectures/)中實作。</span><span class="sxs-lookup"><span data-stu-id="bce56-168">This technique is implemented in the [template building blocks project](https://github.com/mspnp/template-building-blocks) and the [Azure reference architectures](/azure/architecture/reference-architectures/).</span></span> <span data-ttu-id="bce56-169">您可以使用它們來建立您自己的架構，或部署我們的其中一個參考架構。</span><span class="sxs-lookup"><span data-stu-id="bce56-169">You can use these to create your own architecture or deploy one of our reference architectures.</span></span>

<!-- links -->
[objects-as-parameters]: ./objects-as-parameters.md
[resource-manager-linked-template]: /azure/azure-resource-manager/resource-group-linked-templates
[resource-manager-variables]: /azure/azure-resource-manager/resource-group-template-functions-deployment
[nsg]: /azure/virtual-network/virtual-networks-nsg

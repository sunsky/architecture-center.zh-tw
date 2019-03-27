---
title: 在 Azure Resource Manager 範本中使用物件作為參數
description: 說明如何擴充 Azure Resource Manager 範本的功能，以使用物件作為參數。
author: petertay
ms.date: 10/30/2018
ms.topic: article
ms.service: architecture-center
ms.subservice: reference-architecture
ms.openlocfilehash: d7ef704670aa78738098f08d7a81fb74fa5ad59a
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58244189"
---
# <a name="use-an-object-as-a-parameter-in-an-azure-resource-manager-template"></a>在 Azure Resource Manager 範本中使用物件作為參數

[編寫 Azure Resource Manager 範本][azure-resource-manager-create-template]時，您可以直接在範本中指定資源屬性值，或定義參數並在部署期間提供值。 可以對小型部署的每個屬性值使用參數，但每個部署的限制為 255 個參數。 一旦您的部署變得更大且更複雜，可能用盡參數。

若要解決此問題，有一個方法是使用物件來作為參數而非值。 若要這樣做，於部署期間，請在範本中定義參數並指定 JSON 物件，而非單一值。 然後，在您的範本中使用 [`parameter()` 函式][azure-resource-manager-functions]和點運算子參考參數的子屬性。

讓我們看看可部署虛擬網路資源的範例。 首先，讓我們在範本中指定 `VNetSettings` 參數，並將 `type` 設定為 `object`：

```json
...
"parameters": {
    "VNetSettings":{"type":"object"}
},
```

接下來，讓我們提供 `VNetSettings` 物件的值：

> [!NOTE]
> 若要了解如何在部署期間提供參數值，請參閱[了解 Azure Resource Manager 範本的結構和語法][azure-resource-manager-authoring-templates]的**參數**一節。

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

如您所見，我們的單一參數實際上會指定三個子屬性：`name`、`addressPrefixes` 和 `subnets`。 每個子屬性會指定值或其他子屬性。 結果是我們的單一參數會指定部署虛擬網路所需的所有值。

現在讓我們看看範本的其餘部分，以了解使用 `VNetSettings` 物件的方式：

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

`VNetSettings` 物件的值會使用 `parameters()` 函式搭配 `[]` 陣列索引子和點運算子，套用至我們的虛擬網路資源所需的屬性。 如果您只想要以靜態方式將參數物件的值套用至資源，則此方法可行。 不過，如果您想要在部署期間動態指派屬性值的陣列，則可以使用[複製迴圈][azure-resource-manager-create-multiple-instances]。 若要使用複製迴圈，您可以提供資源屬性值的 JSON 陣列，複製迴圈即會動態將值套用至資源的屬性。

如果您使用動態方法，需注意一個問題。 為了示範此問題，讓我們看一下屬性值的一般陣列。 在此範例中，我們屬性的值會儲存在變數中。 請注意我們這裡有兩個陣列&mdash;一個名為 `firstProperty`，另一個名為 `secondProperty`。

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

現在讓我們看看使用複製迴圈存取變數中屬性的方式。

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

`copyIndex()` 函式會傳回複製迴圈目前的反覆運算，而我們同時用它來做為這兩個陣列的索引。

兩個陣列有相同的長度時，這可正常運作。 如果您操作錯誤且兩個陣列的長度不同，就會發生此問題&mdash;在此情況下，您的範本在部署期間將無法通過驗證。 在單一物件中包括您的所有屬性即可以避免此問題，因為在遺漏值時要查看更為容易。 比方說，讓我們看看另一個參數物件中，`propertyObject` 陣列的每個元素是稍早的 `firstProperty` 和 `secondProperty` 陣列的聯集。

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

注意到陣列中的第三個元素嗎？ 它遺漏了 `number`屬性，但在您編寫參數值時，這個方式可讓您更容易注意到遺漏了它。

## <a name="using-a-property-object-in-a-copy-loop"></a>在複製迴圈中使用屬性物件

此方法在與[serial copy loop][azure-resource-manager-create-multiple]結合使用時會變得更加實用，特別是在部署子資源時。

為了示範這點，讓我們看看使用兩個安全性規則部署[網路安全性群組 (NSG)][nsg] 的範本。

首先，讓我們看看我們的參數。 查看範本時，我們會看到已定義一個名為 `networkSecurityGroupsSettings` 的參數，其包含名為 `securityRules` 的陣列。 此陣列包含指定安全性規則一些設定的兩個 JSON 物件。

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

現在，讓我們看看我們的範本。 我們名為 `NSG1` 的第一個資源會部署 NSG。 我們名為 `loop-0` 的第二個資源會執行兩個函式︰第一，它會 `dependsOn` NSG，因此要等到 `NSG1` 完成後才會開始執行部署，而且它是循序迴圈的第一個反覆項目。 我們的第三個資源是巢狀範本，和最後一個範例一樣，它會使用物件來作為其參數值以部署安全性規則。

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
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

讓我們來看看我們如何在 `securityRules` 子資源中指定我們的屬性值。 我們的所有屬性是使用 `parameter()` 函式參考，然後我們會使用點運算子來參考我們的 `securityRules` 陣列，依反覆項目的目前值編製索引。 最後，我們會使用另一個點運算子來參考物件的名稱。

## <a name="try-the-template"></a>試用範本

您可以在 [GitHub][github] 上取得範本範例。 若要部署範本，請複製報告並執行下列 [Azure CLI][cli] 命令：

```bash
git clone https://github.com/mspnp/template-examples.git
cd template-examples/example3-object-param
az group create --location <location> --name <resource-group-name>
az group deployment create -g <resource-group-name> \
    --template-uri https://raw.githubusercontent.com/mspnp/template-examples/master/example3-object-param/deploy.json \
    --parameters deploy.parameters.json
```

## <a name="next-steps"></a>後續步驟

- 了解如何建立範本，逐一查看物件陣列，並將其轉換為 JSON 結構描述。 請參閱[在 Azure Resource Manager 範本中實作屬性轉換器與收集器](./collector.md)

<!-- links -->

[azure-resource-manager-authoring-templates]: /azure/azure-resource-manager/resource-group-authoring-templates
[azure-resource-manager-create-template]: /azure/azure-resource-manager/resource-manager-create-first-template
[azure-resource-manager-create-multiple-instances]: /azure/azure-resource-manager/resource-group-create-multiple
[azure-resource-manager-functions]: /azure/azure-resource-manager/resource-group-template-functions-resource
[nsg]: /azure/virtual-network/virtual-networks-nsg
[cli]: /cli/azure/?view=azure-cli-latest
[github]: https://github.com/mspnp/template-examples

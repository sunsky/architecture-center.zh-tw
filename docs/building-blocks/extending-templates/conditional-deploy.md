---
title: 在 Azure Resource Manager 範本中依條件部署資源
description: 說明如何擴充 Azure Resource Manager 範本的功能，根據參數值條件部署資源
author: petertay
ms.date: 06/09/2017
ms.openlocfilehash: e911e7dc41b4f71ebfaf13a00f8cdbb5b4e2578b
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="conditionally-deploy-a-resource-in-an-azure-resource-manager-template"></a>在 Azure Resource Manager 範本中依條件部署資源

在某些情況下，您需要將您的範本設計為根據條件部署資源，像是某個參數值是否存在。 例如，範本會部署虛擬網路，並包含參數來指定進行對等互連的其他虛擬網路。 如果您未指定任何對等互連的參數值，即表示您不想要 Resource Manager 部署對等互連資源。

若要完成此操作，在資源中使用[`condition` 元素][azure-resource-manager-condition]測試參數陣列的長度。 如果長度為零，傳回 `false` 以阻止部署，但所有大於零的值都會傳回 `true` 以允許部署。

## <a name="example-template"></a>範本範例

讓我們看看示範這項操作的範例範本。 我們的範本使用[`condition` 元素][azure-resource-manager-condition]來控制 `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` 資源的部署。 這項資源會在相同區域中的兩個 Azure 虛擬網路之間建立對等互連。

讓我們看看範本的每個區段。

`parameters` 元素會定義一個名為 `virtualNetworkPeerings` 的參數： 

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
`virtualNetworkPeerings` 參數是 `array`，具有下列結構描述：

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

參數中的屬性指定[與對等互連虛擬網路相關的設定][vnet-peering-resource-schema]。 當我們在 `resources` 區段中指定 `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` 資源，會提供這些屬性的值：

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
在範本的這個部分做了幾件事情。 首先，實際被部署的資源是 `Microsoft.Resources/deployments` 類型的內嵌範本，它有自己的範本可實際部署 `Microsoft.Network/virtualNetworks/virtualNetworkPeerings`。

內嵌範本的 `name` 是將 `copyIndex()`的目前反覆運算與前置詞 `vnp-` 串連，得到的唯一名稱。 

`condition` 元素指定當 `greater()` 函式評估為 `true` 時，應該處理的資源。 在這裡，我們測試 `virtualNetworkPeerings` 參數陣列是否 `greater()` 零。 如果是，則評估為 `true`，滿足 `condition`。 否則，便為 `false`。

接下來，我們指定 `copy` 迴圈。 它是 `serial` 迴圈，表示迴圈會依序完成，每個資源都會等候直到最後一個資源部署完成。 `count` 屬性指定迴圈反覆執行的次數。 在這裡，通常我們會將它設定為 `virtualNetworkPeerings` 陣列的長度，因為它包含的參數物件指定了我們要部署的資源。 不過，如果這樣做，若陣列是空的，驗證會失敗，因為 Resource Manager 注意到我們嘗試存取不存在的屬性。 不過，這個問題可以解決。 看一下我們需要的變數：

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

`workaround` 變數有兩個屬性，一個名為 `true`、一個名為 `false`。 `true` 屬性評估為 `virtualNetworkPeerings` 參數陣列的值。 `false` 屬性評估為空物件，包括Resource Manager 預期會看到的具名屬性 &mdash; 請注意，`false` 其實是一個陣列，和 `virtualNetworkPeerings`參數一樣，能滿足驗證。 

`peerings` 變數會再使用 `workaround` 變數一次，測試 `virtualNetworkPeerings`參數陣列的長度是否大於零。 如果是，`string` 評估為 `true`，且`workaround` 變數評估為 `virtualNetworkPeerings` 參數陣列。 否則，評估為 `false`，且 `workaround` 變數評估為空物件，是陣列的第一個元素。

既然我們已解決驗證問題，我們可以在巢狀範本中直接指定 `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` 資源的部署，傳遞 `virtualNetworkPeerings` 參數陣列中的 `name` 和 `properties`。 在巢狀置於資源的 `properties` 元素中的 `template` 元素中可以看到。

## <a name="next-steps"></a>後續步驟

* 此技術可以在[範本建置區塊專案](https://github.com/mspnp/template-building-blocks)與 [Azure 參考架構](/azure/architecture/reference-architectures/)中實作。 您可以使用它們來建立您自己的架構，或部署我們的其中一個參考架構。

<!-- links -->
[azure-resource-manager-condition]: /azure/azure-resource-manager/resource-group-authoring-templates#resources
[azure-resource-manager-variable]: /azure/azure-resource-manager/resource-group-authoring-templates#variables
[vnet-peering-resource-schema]: /azure/templates/microsoft.network/virtualnetworks/virtualnetworkpeerings
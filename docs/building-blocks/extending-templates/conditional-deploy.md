---
title: 在 Azure Resource Manager 範本中依條件部署資源
description: 說明如何擴充 Azure Resource Manager 範本的功能，根據參數值條件部署資源。
author: petertay
ms.date: 10/30/2018
ms.openlocfilehash: 0e02fbbd130bd6be2fc10173c8466b028d5d61da
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54113462"
---
# <a name="conditionally-deploy-a-resource-in-an-azure-resource-manager-template"></a>在 Azure Resource Manager 範本中依條件部署資源

在某些情況下，您需要將您的範本設計為根據條件部署資源，像是某個參數值是否存在。 例如，範本會部署虛擬網路，並包含參數來指定進行對等互連的其他虛擬網路。 如果您未指定任何對等互連的參數值，即表示您不想要 Resource Manager 部署對等互連資源。

若要完成此操作，在資源中使用[條件元素][azure-resource-manager-condition]以測試參數陣列的長度。 如果長度為零，傳回 `false` 以阻止部署，但所有大於零的值都會傳回 `true` 以允許部署。

## <a name="example-template"></a>範本範例

讓我們看看示範這項操作的範例範本。 我們的範本使用[條件元素][azure-resource-manager-condition]來控制 `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` 資源的部署。 這項資源會在相同區域中的兩個 Azure 虛擬網路之間建立對等互連。

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

參數中的屬性指定[與對等互連虛擬網路相關的設定][vnet-peering-resource-schema]。 當我們在 `resources` 區段中指定 `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` 資源，會提供這些屬性的值：

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

`workaround` 變數有兩個屬性，一個名為 `true`、一個名為 `false`。 `true` 屬性評估為 `virtualNetworkPeerings` 參數陣列的值。 `false` 屬性評估為空物件，包括 Resource Manager 預期會看到的具名屬性 &mdash; 請注意，`false` 其實是一個陣列，和 `virtualNetworkPeerings` 參數一樣，能滿足驗證。

`peerings` 變數會再使用 `workaround` 變數一次，測試 `virtualNetworkPeerings`參數陣列的長度是否大於零。 如果是，`string` 評估為 `true`，且 `workaround` 變數評估為 `virtualNetworkPeerings` 參數陣列。 否則，評估為 `false`，且 `workaround` 變數評估為空物件，是陣列的第一個元素。

既然我們已解決驗證問題，我們可以在巢狀範本中直接指定 `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` 資源的部署，傳遞 `virtualNetworkPeerings` 參數陣列中的 `name` 和 `properties`。 在巢狀置於資源的 `properties` 元素中的 `template` 元素中可以看到。

## <a name="try-the-template"></a>試用範本

您可以在 [GitHub][github] 上取得範本範例。 若要部署範本，請執行下列 [Azure CLI][cli] 命令：

```bash
az group create --location <location> --name <resource-group-name>
az group deployment create -g <resource-group-name> \
    --template-uri https://raw.githubusercontent.com/mspnp/template-examples/master/example2-conditional/deploy.json
```

## <a name="next-steps"></a>後續步驟

* 使用物件做為範本參數，而不是使用純量值。 請參閱[在 Azure Resource Manager 範本中使用物件作為參數](./objects-as-parameters.md)

<!-- links -->
[azure-resource-manager-condition]: /azure/azure-resource-manager/resource-manager-templates-resources#condition
[azure-resource-manager-variable]: /azure/azure-resource-manager/resource-group-authoring-templates#variables
[vnet-peering-resource-schema]: /azure/templates/microsoft.network/virtualnetworks/virtualnetworkpeerings
[cli]: /cli/azure/?view=azure-cli-latest
[github]: https://github.com/mspnp/template-examples

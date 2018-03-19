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
# <a name="extend-azure-resource-manager-template-functionality"></a>擴充 Azure Resource Manager 範本功能

在 2016 年，Microsoft 模式與練習小組建立了一組 Azure Resource Manager [範本建置組塊](https://github.com/mspnp/template-building-blocks/wiki)，其目標是簡化資源部署。 每個建置組塊包含一組預先建置的範本，這個範本會部署由個別參數檔案指定的資源集。

建置組塊範本的設計目的是結合在一起以建立更大且更複雜的部署。 例如，在 Azure 中部署虛擬機器需要虛擬網路、儲存體帳戶和其他資源。 [虛擬網路建置組塊範本](https://github.com/mspnp/template-building-blocks/wiki/VNet-(v1))會部署虛擬網路和子網路。 [虛擬機器建置組塊範本](https://github.com/mspnp/template-building-blocks/wiki/Windows-and-Linux-VMs-(v1))會部署儲存體帳戶、網路介面和實際的 VM。 然後您就可以建立指令碼或範本以使用其對應參數檔案呼叫兩個建置組塊範本，使用一個作業來部署完整的架構。

在開發建置組塊範本時，p&p 設計了數個概念以擴充 Azure Resource Manager 範本功能。 在此系列中，我們將說明其中幾個概念，讓您可以在自己的範本中使用它們。

> [!NOTE]
> 這些文章假設您對於 Azure Resource Manager 範本已有進階了解。
---
title: CAF：軟體定義網路 - 僅限 PaaS
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 討論雲端式網路功能的僅限 PaaS 模型
author: rotycenh
ms.openlocfilehash: 2f3f82d781ddb6544721e82e7b7d795222a2f8ff
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241199"
---
# <a name="software-defined-networks-paas-only"></a>軟體定義網路：僅限 PaaS

當您實作平台即服務 (PaaS) 資源時，部署程序會利用對於該網路的有限控制項，自動建立假設的基礎網路，包括負載平衡、連接埠封鎖，以及連線至其他 PaaS 服務。

在 Azure 中，可以將數個 PaaS 資源類型[部署到](/azure/virtual-network/virtual-network-for-azure-services)或[連線到](/azure/virtual-network/virtual-network-service-endpoints-overview)虛擬網路，讓這些資源能與您現有的虛擬網路基礎結構整合。 不過，在許多情況下，僅限 PaaS 的網路架構只依賴 PaaS 資源原生提供的這些預設網路功能，就足以符合工作負載需求。

如果您考慮使用僅限 PaaS 的網路架構，請務必驗證符合您需求的必要假設。

## <a name="paas-only-assumptions"></a>僅限 PaaS 的假設

部署僅限 PaaS 網路架構的假設如下：

- 正在部署的應用程式是獨立應用程式，或只是相依於其他 PaaS 資源。
- 您的 IT 作業小組可以更新其工具、訓練和流程，以支援獨立 PaaS 應用程式的管理、設定和部署。
- PaaS 應用程式不屬於將要包含 IaaS 資源的更廣泛雲端移轉工作。

這些假設是對應於部署僅限 PaaS 網路的最小限定詞。 雖然這個方法可能與單一應用程式部署的需求相符，但您的雲端採用小組應該檢查下列長期問題：

- 此部署會擴大規模而要求存取其他非 PaaS 資源嗎？
- 所規劃的其他 PaaS 部署超過目前的解決方案嗎？
- 組織是否有其他未來雲端移轉的計劃？

這些問題的答案並不會阻止小組選擇 PaaS 僅限選項，但是應在做出最後決定前加以考量。

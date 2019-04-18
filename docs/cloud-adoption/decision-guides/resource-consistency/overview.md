---
title: CAF：資源一致性決策指南
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 了解規劃 Azure 移轉時的資源一致性。
author: rotycenh
ms.openlocfilehash: 3159e4b7aeddfdd99261c0f68591998d741f3359
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640036"
---
# <a name="caf-resource-consistency-decision-guide"></a>CAF：資源一致性決策指南

Azure[訂用帳戶設計](../subscriptions/overview.md)定義您組織您的雲端資產，相對於您的組織結構、 會計做法及工作負載需求的方式。 除了此結構的層級解決您組織的控管的原則需求，您的雲端資產之間必須能夠以一致的方式組織、 部署及管理訂用帳戶內的資源。

![規劃符合下列快速連結的資源一致性選項 (從最簡單到最複雜)](../../_images/discovery-guides/discovery-guide-resource-consistency.png)

跳至：[基本群組](#basic-grouping) | [部署一致性](#deployment-consistency) | [原則一致性](#policy-consistency) | [階層式一致性](#hierarchical-consistency)  | [自動化的一致性](#automated-consistency)

決定您的雲端資產資源一致性需求的層級主要取決於下列因素： 移轉後的數位資產的大小、 商務或不符合您現有的訂用帳戶內的整齊的環境需求設計方式或需要強制執行控管，經過一段時間後已部署資源。 

這些因素會增加的重要性，確保一致的部署、 分組和管理雲端資源的優點變得更重要。 達到資源一致性，以符合日益增加的需求的更多進階程度需要更多自動化、 工具和一致性強制實行，所花費的努力，這會導致更多時間花在變更管理和追蹤。

## <a name="basic-grouping"></a>基本分組

在 Azure 中，[資源群組](/azure/azure-resource-manager/resource-group-overview#resource-groups)是以邏輯方式將訂用帳戶內的資源分組的核心資源組織機制。

資源群組會做為具有一般生命週期或共用管理條件約束 (例如原則或以角色為基礎的存取控制 (RBAC) 要求) 的資源容器。 資源群組不能建立巢狀結構，且資源只能屬於一個資源群組。 某些動作可套用於資源群組中的所有資源。 例如，刪除資源群組即可移除該群組內的所有資源。 有幾種常見模式可建立資源群組，通常區分為兩種類別：

- 傳統 IT 工作負載：最常見的分組方式為依照相同生命週期內的項目分組，例如應用程式。 依照應用程式分組，即可進行個別應用程式管理。
- 敏捷式 IT 工作負載：著重於外部客戶面向的雲端應用程式。 資源群組通常會反映出部署 (例如 Web 層或應用程式層) 和管理的功能層次。

## <a name="deployment-consistency"></a>部署一致性

Azure 平台建置在基底的資源群組機制之上，為使用範本來將您的資源部署到雲端環境提供系統。 您可以在部署工作負載時使用範本建立一致的組織和命名慣例，強制執行您資源部署和管理設計的各個層面。

[Azure Resource Manager 範本](/azure/azure-resource-manager/resource-group-overview#template-deployment)可讓您使用預先決定的組態和資源群組結構，以一致的狀態重複部署資源。 Resource Manager 範本可協助您定義一組標準作為部署基礎。

例如，您可以部署包含兩部虛擬機器做為 web 伺服器與發佈伺服器之間的流量負載平衡器結合的 web 伺服器工作負載的標準範本。 您可以重複使用此範本來建立組結構完全相同的虛擬機器和負載平衡器，每當需要這類工作負載，部署名稱和 IP 位址設定的變更只牽涉到。

請注意，您也可以用程式設計方式部署這些範本，並將它們與您的 CI/CD 系統整合。

## <a name="policy-consistency"></a>原則一致性

為確保在建立資源時套用治理原則，資源群組設計的一部份會涉及使用一般組態部署資源。

您可以透過結合資源群組和標準化 Resource Manager 範本，針對部署時需要哪些設定，以及要對每個資源群組或資源套用哪些 [Azure 原則](/azure/governance/policy/overview)規則等需求，強制執行適用標準。

例如，您可能需要讓訂用帳戶內部署的所有虛擬機器皆連線到由您的中央 IT 小組所管理的一般子網路。 您可以建立標準範本，以用於部署會針對工作負載建立個別資源群組，並在其中部署必要 VM 的工作負載 VM。 此資源群組會有一項原則規則，限制只允許將資源群組內的網路介面加入至共用子網路。

如需有關在雲端部署內強制執行原則決策的更深入討論，請參閱[原則強制執行](../policy-enforcement/overview.md)。

## <a name="hierarchical-consistency"></a>階層式一致性

資源群組可讓您支援額外的層級的階層內的訂用帳戶中，您的組織中套用 Azure 原則規則，並存取資源群組層級的控制項。 不過，當您的雲端資產的大小增加時，您可能需要支援跨訂用帳戶治理需求上低於使用 Azure Enterprise 合約的企業/部門/帳戶/訂用帳戶階層可支援更複雜。 

[Azure 的管理群組](../subscriptions/overview.md#management-groups)可讓您組織的訂用帳戶到更複雜的組織結構的群組訂用帳戶的替代的階層，以建立您的 enterprise 合約的結構。 這個替代的階層架構，可讓您跨多個訂用帳戶和其所包含的資源套用存取控制和原則強制執行機制。 管理群組階層可用來符合您的雲端資產訂用帳戶與 operations 或商務控管需求。 

## <a name="automated-consistency"></a>自動化的一致性

對於大型雲端部署而言，全域治理變得更加重要，而且更複雜。 務必要自動套用和部署資源時，強制執行控管需求，以及針對現有部署更新的需求。

[Azure 藍圖](/azure/governance/blueprints/overview)可讓組織支援 Azure 中大型雲端資產的全域治理。 藍圖超越了標準 Azure Resource Manager 範本所提供的功能，可建立完整且能夠部署資源及套用原則規則的部署協調流程。 藍圖支援下列功能：版本控制、將更新套用至使用藍圖的所有訂用帳戶，以及鎖定已部署的訂用帳戶，藉此防止未經授權建立及修改資源。

這些部署套件可讓 IT 和開發團隊，快速部署符合變更組織性原則需求的新工作負載和網路資產。 藍圖也可以整合至 CI/CD 管線中，以在更新部署時套用修改過的治理標準。

## <a name="next-steps"></a>後續步驟

了解如何使用資源命名與標記進一步組織及管理雲端資源。

> [!div class="nextstepaction"]
> [資源命名與標記](../resource-tagging/overview.md)

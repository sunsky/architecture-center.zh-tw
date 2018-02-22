---
title: "指引：Azure 資源群組設計"
description: "依據基本雲端採用策略設計 Azure 資源群組的指引"
author: petertay
ms.openlocfilehash: ac6cbb03be8cdba020641d3b9034ad9d20101acf
ms.sourcegitcommit: 2e8b06e9c07875d65b91d5431bfd4bc465a7a242
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="guidance-azure-resource-group-design"></a>指引：Azure 資源群組設計

在 Azure 中，[資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)是用來為資源分組的邏輯容器。 在 Azure 中部署的每項資源，都必須部署到單一資源群組中。

## <a name="design-considerations"></a>設計考量

- 資源群組中的所有資源應具有相同的生命週期。 也就是說，您的作法應該是以群組的形式部署、更新和刪除具有相同生命週期的資源。 例如，單一 Web 應用程式的計算資源通常會部署為單一單位。 不過，與其他 Web 應用程式共用的資料庫很可能會在不同的生命週期中受到管理，因此應屬於其本身的資源群組。
- 資源群組可以包含位於不同區域中的資源。
- 單一資源群組中的所有資源，都必須與單一訂用帳戶相關聯。 
- 一項資源可在不同的資源群組間移動，但不可移入有資源部署到不同訂用帳戶的資源群組中。
- 資源的群組指派不會影響到與其他資源群組中各項資源的連線或互動。 例如，指派給一個資源群組的虛擬機器，可以連線至指派給另一個資源群組的資料庫，只要兩者之間有網路連線即可。
- 資源群組可以用來設定系統管理動作的存取控制範圍。 您可以在訂用帳戶層級或資源群組層級套用角色型存取控制 (RBAC) 權限。 資源群組層級會繼承任何在訂用帳戶層級指派的權限。 您將進一步了解中階和進階採用階段中的 RBAC 和資源權限。

## <a name="proven-practices"></a>經過證實的作法

- 在基本階段中，您可能只會管理幾個概念證明 (POC) 專案，且每個專案只有少量資源。 POC 資源通常會使用相同的生命週期，因此您可以為這類專案分別建立單一資源群組。
- 在中階採用階段中，您將管理多個專案。 不同類型的專案可受益於其他資源群組設計的優點。 如果您想要將任何初始 POC 專案升階到生產環境，您可以將資源移至屬於相同訂用帳戶的另一個資源群組。 因此，在這個階段中，您應將這些資源部署到相同的訂用帳戶，以便日後可重新組織資源。

## <a name="next-steps"></a>後續步驟

* 現在，您已了解基本採用階段有哪些經過證實的作法，因此能夠建立資源群組，並為其新增資源。 雖然您在此階段僅管理少量資源，但隨著資源的新增，這些資源的管理工作也將趨於複雜。 請了解 [Azure 的命名慣例和標記](/azure/architecture/best-practices/naming-conventions?toc=/azure/architecture/cloud-adoption-guide/toc.json)以命名和標記您的資源，做好中階採用階段的準備工作。

---
title: CAF：什麼是雲端資源治理？
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.date: 02/11/2019
description: 說明 Azure 上的雲端資源治理
author: petertaylor9999
ms.openlocfilehash: 0989a5aad8a6290cce07fd71771c690bbd615e0d
ms.sourcegitcommit: 0a8a60d782facc294f7f78ec0e9033e3ee16bf4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068849"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-is-cloud-resource-governance"></a>什麼是雲端資源治理？

在  [Azure 如何運作？](what-is-azure.md)、 您已了解 Azure 是伺服器的集合，以及網路代表使用者執行虛擬化的硬體和軟體的硬體。 Azure 可讓您組織的應用程式開發和 IT 部門，讓您輕鬆地建立、 讀取、 更新和刪除資源，視需要變得敏捷。

不過，雖然無限制地的存取資源，可以讓開發人員非常敏捷式軟體開發，它可能也會導致非預期的成本。 例如，開發小組可能會核准要部署一組測試資源，但是在完成測試之後忘記刪除。 這些資源會繼續累算費用，即使它們不再是已核准或不必要。

解決方法是資源的存取管理。 **控管**是進行中的程序的管理、 監視及稽核以符合組織需求的 Azure 資源的使用。

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ii94]

<!-- markdownlint-enable MD034 -->

這些需求是每個組織，唯一的因此萬用的方法，來控管不是很有幫助。 相反地，它是由設計使用 Azure 的兩個主要的控管工具及其控管模型的每個組織：**角色型存取控制 (RBAC)** 並**資源原則**。

RBAC 定義的角色和角色定義指派給該角色每一位使用者的功能。 例如，**擁有者**角色可讓所有的功能 （建立、 讀取、 更新和刪除） 對於資源，而**讀取器**角色可讓讀取的功能。 可以使用廣泛的範圍會套用到多個資源類型或適用於少數的狹窄範圍中定義角色。

資源原則會定義資源建立的規則。 例如，資源原則可以限制為特定的預先核准大小的虛擬機器的 SKU。 若要建立的資源提出要求時，另一個資源的原則可能會強制使用指派的成本中心標記的應用程式。

在設定這些工具時，務必控管與組織的敏捷度之間取得平衡。 更嚴格您控管的原則，敏捷式較不會是您的開發人員和 IT 工作者。 嚴格控管的原則可能需要更多的手動步驟，例如要求開發人員在填寫表單，或將電子郵件傳送至管理小組的成員，才能以手動方式建立資源。 管理小組具有有限的容量，並且可能成為瓶頸，導致 unproductively 等候其資源來建立或不必要的資源產生成本，在刪除前的開發團隊。

## <a name="next-steps"></a>後續步驟

既然您了解雲端資源管理的概念，深入了解如何在 Azure 中管理資源的存取。

> [!div class="nextstepaction"]
> [深入了解在 Azure 中的資源存取](azure-resource-access.md)

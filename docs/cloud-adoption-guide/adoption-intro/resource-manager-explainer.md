---
title: 說明：什麼是 Azure Resource Manager？
description: 說明 Azure Resource Manager 的內部運作方式
author: petertay
ms.openlocfilehash: 60f09901bdc4b292abd73335b78c7d56a76f27a6
ms.sourcegitcommit: 2e8b06e9c07875d65b91d5431bfd4bc465a7a242
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
ms.locfileid: "29062026"
---
# <a name="explainer-what-is-azure-resource-manager"></a>說明：什麼是 Azure Resource Manager？

在 [Azure 如何運作？](azure-explainer.md)說明文章中，您已了解 Azure 的內部架構。 此架構包含一個前端，負責主控用來管理內部 Azure 服務的分散式應用程式。

Azure 的前端包含名為 Azure Resource Manager 的服務。 Azure Resource Manager 負責管理 Azure 所主控的資源從建立到刪除這段期間的生命週期。 有許多方法可與 Azure Resource Manager 互動 &mdash; 使用 Powershell、Azure 命令列介面、SDK &mdash; 但這些工具都只是透過 Azure Resource Manager 所主控的 RESTful API 而提供的包裝函式。

Azure Resource Manager 所提供的 RESTful API 是一組**資源提供者**通用的介面。 資源提供者就是在 Azure 中建立、讀取、更新和刪除資源的 Azure 服務。 事實上，RESTful API 包含前述各項功能適用的方法。 

要使用 RESTful API，必須要有使用者的存取權杖、**訂用帳戶識別碼**，以及**資源群組識別碼**這個新元素。 我們將在[資源群組說明](resource-group-explainer.md)文章中討論資源群組。 Azure Resource Manager 也需要**租用戶識別碼**，此識別碼會編碼為存取權杖的一部分。 

在收到建立資源的有效 API 呼叫時，Azure Resource Manager 會尋找指定區域中的容量，並將任何必要的檔案複製到暫存位置。 接著，要求會傳送至機架中的網狀架構控制器，而網狀架構控制器會配置資源。 網狀架構控制器會以成功或失敗通知回應要求，並提供新建資源的**資源識別碼**。 這些四個識別碼會儲存在 Azure 內部，並統合為已部署資源的唯一識別碼。

## <a name="next-steps"></a>後續步驟

* 現在您已了解 Azure Resource Manager 的內部運作方式，接下來請了解[資源群組](resource-group-explainer.md)，以建立您的第一個資源群組。

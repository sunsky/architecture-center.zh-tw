---
title: "說明 - 什麼是 Azure 資源群組？"
description: "說明資源群組的內部 Azure 函式"
author: petertay
ms.openlocfilehash: e7c7334bd88c28f57498486bd2bed3c349565222
ms.sourcegitcommit: 2e8b06e9c07875d65b91d5431bfd4bc465a7a242
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="what-is-an-azure-resource-group"></a>什麼是 Azure 資源群組？

在[什麼是 Azure Resource Manager？](resource-manager-explainer.md)說明文章中，您已了解在執行建立、讀取、更新或刪除資源的呼叫時，Azure Resource Manager 會要求提供**資源群組識別碼**。 此資源群組識別碼會參照**資源群組**。 資源群組就是一個 Azure Resource Manager 對資源套用以將其歸類為同一群組的識別碼。 此資源群組識別碼可讓 Azure Resource Manager 對共用此識別碼的資源群組執行作業。

例如，使用者可藉由指定資源群組識別碼對 Azure Resource Manager RESTful API 發出**刪除**呼叫，而無須提供任何特定的資源識別碼。 Azure Resource Manager 會查詢內部 Azure 資料庫中所有具有指定資源群組識別碼的資源，並呼叫 RESTful API 以刪除每項資源。

一個資源群組不可包含來自不同訂用帳戶的資源。 這是因為租用戶識別碼與訂用帳戶識別碼之間具有一對多的關聯性 &mdash; 多個訂用帳戶可以仰賴相同的租用戶來提供驗證和授權，但每個訂用帳戶只能信任一個租用戶。 訂用帳戶識別碼與資源群組識別碼之間也具有一對多的關聯性 &mdash; 多個資源群組可以屬於相同的訂用帳戶，但每個資源群組只能屬於一個訂用帳戶。 最後，資源群組識別碼與資源識別碼之間也具有一對多的關聯性 &mdash; 一個資源群組可以有多項資源，但每項資源只能屬於一個資源群組。

## <a name="next-steps"></a>後續步驟

* 現在您已了解何為 Azure 資源群組，接下來請取得[關於限制資源存取](/azure/active-directory/active-directory-understanding-resource-access?toc=/azure/architecture/cloud-adoption-guide/toc.json)的基本知識。 雖然這不是基本採用階段的一部分，但在中階採用階段卻有其重要性。 接著，您可以[建立您的第一個資源群組](/azure/azure-resource-manager/resource-group-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json)，並檢閱 [Azure 資源群組的設計指引](resource-group.md)。

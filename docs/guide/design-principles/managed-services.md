---
title: 使用受控服務
titleSuffix: Azure Application Architecture Guide
description: 可能的話，請使用平台即服務 (PaaS) 而非基礎結構即服務 (IaaS)。
author: MikeWasson
ms.date: 08/30/2018
ms.custom: seojan19
ms.openlocfilehash: 6f1ea3f3bf2442b331583a59973e3d32908aadeb
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54111660"
---
# <a name="use-managed-services"></a>使用受控服務

## <a name="when-possible-use-platform-as-a-service-paas-rather-than-infrastructure-as-a-service-iaas"></a>可能的話，請使用平台即服務 (PaaS) 而非基礎結構即服務 (IaaS)

IaaS 就像是有一箱零件。 您可以建立任何項目，但您必須自行組合。 受控服務則更容易設定及管理。 您不需要佈建 VM、設定 VNet、管理修補程式和更新，以及與在 VM 上執行中軟體相關聯的所有其他額外負荷。

例如，假設您的應用程式需要訊息佇列。 您可以在 VM 上設定您自己的訊息服務，使用 RabbitMQ 之類的項目。 Azure 服務匯流排已提供可靠的傳訊即服務，並且較容易設定。 只要建立服務匯流排命名空間 (這可以隨著部署指令碼的一部分完成)，然後使用用戶端 SDK 呼叫服務匯流排。

當然，您的應用程式可能有使 IaaS 方法更合適的特定需求。 不過，即使您的應用程式是以 IaaS 為基礎，請尋找能自然併入受控服務的位置。 這些包括快取、佇列和資料儲存體。

| 而不是執行... | 考慮使用... |
|-----------------------|-------------|
| Active Directory | Azure Active Directory Domain Services |
| Elasticsearch | Azure 搜尋服務 |
| Hadoop | HDInsight |
| IIS | App Service 方案 |
| MongoDB | Cosmos DB |
| Redis | Azure Redis 快取 |
| SQL Server | 連接字串 |

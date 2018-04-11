---
title: Azure 應用程式的設計原則
description: Azure 應用程式的設計原則
author: MikeWasson
layout: LandingPage
ms.openlocfilehash: 57b04839e14804ad97fc9c86e1f9c4fe6e0da472
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="design-principles-for-azure-applications"></a>Azure 應用程式的設計原則

請遵循這些設計原則，讓您的應用程式更有擴充空間、可容易復原且更方便管理。 

**[自我修復設計](self-healing.md)**。 在分散式系統中，發生失敗在所難免。 將您的應用程式設計為可在發生失敗時自我修復。

**[讓各個項目都有備援](redundancy.md)**。 將備援建置到您的應用程式中，以避免發生單一失敗點。
 
**[最小化協調](minimize-coordination.md)**。 將來應用程式服務之間的協調最小化，以達成延展性。
 
**[相應放大的設計](scale-out.md)**。設計您的應用程式，讓它可水平調整，以依需求新增或移除新的執行個體。

**[在限制中使用分割](partition.md)**。 使用分割作業來解決資料庫、網路和計算限制。

**[作業的設計](design-for-operations.md)**。 設計應用程式，讓作業小組擁有所需的工具。

**[使用受管理的服務](managed-services.md)**。 可能的話，請使用平台即服務 (PaaS) 而非基礎結構即服務 (IaaS)。

**[使用作業的最佳資料存放區](use-the-best-data-store.md)**。 挑選最適合您資料的儲存體技術，以及了解使用方式。 
 
**[進化的設計](design-for-evolution.md)**。 所有成功的應用程式，在經過一段時間後都會有所變更。 進化的設計是連續創新的關鍵。

**[針對企業的需求而建置](build-for-business.md)**。 每個設計決策必須對應某個業務需求。


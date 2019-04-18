---
title: Azure Kubernetes Service 的微服務參考實作
description: 此參考實作會說明微服務架構的最佳做法
author: MikeWasson
ms.date: 02/26/2019
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: microservices
ms.openlocfilehash: 17e275e5b5f45233f7467192402cb28fce35c57b
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068900"
---
# <a name="designing-a-microservices-architecture"></a>設計微服務架構

微服務已成為熱門的架構樣式，用於建置可復原、高延展性、可獨立部署，以及能夠快速發展的雲端應用程式。 不過，微服務需要不同的方法來設計和建置應用程式，而不僅僅是現今流行的用語。

在這一系列文章中，我們會探討如何在 Azure 上建置和執行微服務架構。 主題包括：

- [服務間通訊](./interservice-communication.md)
- [API 設計](./api-design.md)
- [API 閘道](./gateway.md)
- [資料考量](./data-considerations.md)
- [設計模式](./patterns.md)

## <a name="prerequisites"></a>必要條件

閱讀這些文章之前，您可以從下列文章著手：

- [微服務架構簡介](../introduction.md)。 了解微服務的優勢和挑戰，以及何時使用這種架構樣式。
- [使用領域分析進行微服務建模](../model/domain-analysis.md)。 了解領域導向方法來進行微服務建模。

## <a name="reference-implementation"></a>參考實作

為了說明微服務架構的最佳做法，我們建立了名為「無人機遞送應用程式」的參考實作。 此參考實作會使用 Azure Kubernetes Service (AKS) 在 Kubernetes 上執行。 您可以在 [GitHub][drone-ri] 上找到此參考實作。

![無人機遞送應用程式的架構](../images/drone-delivery.png)

## <a name="scenario"></a>案例

Fabrikam, Inc. 正在推動無人機遞送服務。 該公司經營一個無人機隊。 企業會註冊此服務，而使用者可要求無人機收取貨物進行遞送。 當客戶排程取件後，後端系統就會指派一台無人機並將預估的遞送時間通知使用者。 在遞送過程中，客戶可以使用持續更新的 ETA 來追蹤無人機的位置。

此案例牽涉到相當複雜的領域。 其中一些企業的疑慮包括無人機排程、追蹤包裹、管理使用者帳戶，以及儲存和分析歷史資料。 此外，Fabrikam 希望能快速上市，而後快速反覆運作，並新增各項功能。 此應用程式必須以最高服務等級目標 (SLO) 在雲端規模運作。 Fabrikam 也預期不同的系統部分會有非常不同的資料儲存和查詢需求。 上述所有考量導致 Fabrikam 針對無人機遞送應用程式選擇微服務架構。

> [!NOTE]
> 如需選擇微服務架構或其他架構樣式的說明，請參閱 [Azure 應用程式架構指南](../../guide/index.md)。

我們的參考實作會使用 Kubernetes 搭配 [Azure Kubernetes Service](/azure/aks/) (AKS)。 不過，任何容器協調工具 (包括 [Azure Service Fabric](/azure/service-fabric/)) 都會面臨許多高層級的架構決策和挑戰。

<!-- links -->

[drone-ri]: https://github.com/mspnp/microservices-reference-implementation/tree/v0.1.0-orig

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [選擇計算選項](./compute-options.md)
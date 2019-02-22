---
title: CAF：架構決策指南
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 深入了解雲端採用架構中的架構決策指南。
author: rotycenh
ms.openlocfilehash: b43047b5fc5b636bc84b9f28ec24846730e63672
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901063"
---
# <a name="architectural-decision-guides"></a>架構決策指南

雲端採用架構中的架構決策指南說明在建立雲端治理設計指引時有幫助的模式和模型。 每個決策指南著重於雲端部署的一個核心基礎結構元件，並列出旨在支援特定雲端部署案例的潛在模式或模型。

當您開始為貴組織建立雲端治理時，可採取動作的治理旅程提供基準藍圖。 不過，這些旅程假設不一定能反映貴組織的需求和優先順序。
這些決策指南藉由提供替代模式和模型來補充範例治理旅程，這些模式和模型可以協助您讓在範例設計指引中所做的架構設計選擇與您自己的需求保持一致。

## <a name="design-guidance-categories"></a>設計指引類別

下列類別分別代表所有雲端部署的基礎技術。 針對範例設計旅程中不符合您的需求的每個選擇，檢查以下相關區段中的選項以選擇更適合您的需求的模式或模型。

[訂用帳戶](./subscriptions/overview.md)：規劃您的雲端部署訂用帳戶設計和帳戶結構，以符合您的組織擁有權、計費及管理功能。

[身分識別](./identity/overview.md)：整合雲端型身分識別服務與您現有的身分識別資源，以支援 IT 環境內的存取控制。

[原則強制執行](./policy-enforcement/overview.md)：定義和強制執行您部署到雲端之資源和工作負載的組織原則規則。

[資源一致性](./resource-consistency/overview.md)：請確定您的雲端型資源部署和組織保持一致，以強制執行資源管理和原則需求。

[資源標記](./resource-tagging/overview.md)：組織您的雲端型資源，以支援計費模型、雲端帳戶管理方法、管理、存取控制及操作效率。 資源標記需要一致且井然有序的命名和中繼資料配置。

[軟體定義網路](./software-defined-network/overview.md)：使用快速部署和修改虛擬網路功能，將安全工作負載部署到雲端。 軟體定義網路 (SDN) 可以支援敏捷工作流程、隔離資源，並且整合雲端型系統與您現有的 IT 基礎結構。

[加密](./encryption/overview.md)：使用加密保護敏感性資料，這是雲端部署內安全性的重要層面。

[記錄和報告](./log-and-report/overview.md)：監視雲端型資源產生的記錄資料。 分析資料會提供工作負載的作業、維護及原則強制執行狀態的健康情況相關深入解析。

## <a name="next-steps"></a>後續步驟

深入了解訂用帳戶和帳戶如何作為雲端部署的基礎。

> [!div class="nextstepaction"]
> [訂用帳戶設計](subscriptions/overview.md)

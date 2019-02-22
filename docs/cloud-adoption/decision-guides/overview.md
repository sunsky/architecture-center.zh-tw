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
# <a name="architectural-decision-guides"></a><span data-ttu-id="ccfe6-103">架構決策指南</span><span class="sxs-lookup"><span data-stu-id="ccfe6-103">Architectural decision guides</span></span>

<span data-ttu-id="ccfe6-104">雲端採用架構中的架構決策指南說明在建立雲端治理設計指引時有幫助的模式和模型。</span><span class="sxs-lookup"><span data-stu-id="ccfe6-104">The architectural decision guides in the Cloud Adoption Framework describe patterns and models that help when creating cloud governance design guidance.</span></span> <span data-ttu-id="ccfe6-105">每個決策指南著重於雲端部署的一個核心基礎結構元件，並列出旨在支援特定雲端部署案例的潛在模式或模型。</span><span class="sxs-lookup"><span data-stu-id="ccfe6-105">Each decision guide focuses on one core infrastructure component of cloud deployments and lists potential patterns or models intended to support specific cloud deployment scenarios.</span></span>

<span data-ttu-id="ccfe6-106">當您開始為貴組織建立雲端治理時，可採取動作的治理旅程提供基準藍圖。</span><span class="sxs-lookup"><span data-stu-id="ccfe6-106">When you begin to establish cloud governance for your organization,  actionable governance journeys provide a baseline roadmap.</span></span> <span data-ttu-id="ccfe6-107">不過，這些旅程假設不一定能反映貴組織的需求和優先順序。</span><span class="sxs-lookup"><span data-stu-id="ccfe6-107">However, these journeys make assumptions about requirements and priorities that may not reflect those of your organization.</span></span>
<span data-ttu-id="ccfe6-108">這些決策指南藉由提供替代模式和模型來補充範例治理旅程，這些模式和模型可以協助您讓在範例設計指引中所做的架構設計選擇與您自己的需求保持一致。</span><span class="sxs-lookup"><span data-stu-id="ccfe6-108">These decision guides supplement the sample governance journeys by providing alternative patterns and models that help you align the architectural design choices made in the example design guidance with your own requirements.</span></span>

## <a name="design-guidance-categories"></a><span data-ttu-id="ccfe6-109">設計指引類別</span><span class="sxs-lookup"><span data-stu-id="ccfe6-109">Design guidance categories</span></span>

<span data-ttu-id="ccfe6-110">下列類別分別代表所有雲端部署的基礎技術。</span><span class="sxs-lookup"><span data-stu-id="ccfe6-110">Each of the following categories represents a foundational technology of all cloud deployments.</span></span> <span data-ttu-id="ccfe6-111">針對範例設計旅程中不符合您的需求的每個選擇，檢查以下相關區段中的選項以選擇更適合您的需求的模式或模型。</span><span class="sxs-lookup"><span data-stu-id="ccfe6-111">For each choice in the example design journeys that doesn’t match your requirements, examine the options in the relevant section below to choose a pattern or model better suited to your needs.</span></span>

<span data-ttu-id="ccfe6-112">[訂用帳戶](./subscriptions/overview.md)：規劃您的雲端部署訂用帳戶設計和帳戶結構，以符合您的組織擁有權、計費及管理功能。</span><span class="sxs-lookup"><span data-stu-id="ccfe6-112">[Subscriptions](./subscriptions/overview.md): Plan your cloud deployment's subscription design and account structure to match your organization's ownership, billing, and management capabilities.</span></span>

<span data-ttu-id="ccfe6-113">[身分識別](./identity/overview.md)：整合雲端型身分識別服務與您現有的身分識別資源，以支援 IT 環境內的存取控制。</span><span class="sxs-lookup"><span data-stu-id="ccfe6-113">[Identity](./identity/overview.md): Integrate cloud-based identity services with your existing identity resources to support access control within your IT environment.</span></span>

<span data-ttu-id="ccfe6-114">[原則強制執行](./policy-enforcement/overview.md)：定義和強制執行您部署到雲端之資源和工作負載的組織原則規則。</span><span class="sxs-lookup"><span data-stu-id="ccfe6-114">[Policy Enforcement](./policy-enforcement/overview.md): Define and enforce organizational policy rules for resources and workloads that you deploy to the cloud.</span></span>

<span data-ttu-id="ccfe6-115">[資源一致性](./resource-consistency/overview.md)：請確定您的雲端型資源部署和組織保持一致，以強制執行資源管理和原則需求。</span><span class="sxs-lookup"><span data-stu-id="ccfe6-115">[Resource Consistency](./resource-consistency/overview.md): Ensure that deployment and organization of your cloud-based resources align to enforce resource management and policy requirements.</span></span>

<span data-ttu-id="ccfe6-116">[資源標記](./resource-tagging/overview.md)：組織您的雲端型資源，以支援計費模型、雲端帳戶管理方法、管理、存取控制及操作效率。</span><span class="sxs-lookup"><span data-stu-id="ccfe6-116">[Resource Tagging](./resource-tagging/overview.md): Organize your cloud-based resources to support billing models, cloud accounting approaches, management, access control, and operational efficiency.</span></span> <span data-ttu-id="ccfe6-117">資源標記需要一致且井然有序的命名和中繼資料配置。</span><span class="sxs-lookup"><span data-stu-id="ccfe6-117">Resource tagging requires a consistent and well-organized naming and metadata scheme.</span></span>

<span data-ttu-id="ccfe6-118">[軟體定義網路](./software-defined-network/overview.md)：使用快速部署和修改虛擬網路功能，將安全工作負載部署到雲端。</span><span class="sxs-lookup"><span data-stu-id="ccfe6-118">[Software Defined Networks](./software-defined-network/overview.md): Deploy secure workloads to the cloud using rapid deployment and modification of virtualized networking capabilities.</span></span> <span data-ttu-id="ccfe6-119">軟體定義網路 (SDN) 可以支援敏捷工作流程、隔離資源，並且整合雲端型系統與您現有的 IT 基礎結構。</span><span class="sxs-lookup"><span data-stu-id="ccfe6-119">Software-defined networks (SDNs) can support agile workflows, isolate resources, and integrate cloud-based systems with your existing IT infrastructure.</span></span>

<span data-ttu-id="ccfe6-120">[加密](./encryption/overview.md)：使用加密保護敏感性資料，這是雲端部署內安全性的重要層面。</span><span class="sxs-lookup"><span data-stu-id="ccfe6-120">[Encryption](./encryption/overview.md): Secure your sensitive data using encryption, an important aspect of security within a cloud deployment.</span></span>

<span data-ttu-id="ccfe6-121">[記錄和報告](./log-and-report/overview.md)：監視雲端型資源產生的記錄資料。</span><span class="sxs-lookup"><span data-stu-id="ccfe6-121">[Logs and Reporting](./log-and-report/overview.md): Monitor log data generated by cloud-based resources.</span></span> <span data-ttu-id="ccfe6-122">分析資料會提供工作負載的作業、維護及原則強制執行狀態的健康情況相關深入解析。</span><span class="sxs-lookup"><span data-stu-id="ccfe6-122">Analyzing data provides health-related insights into the operations, maintenance, and policy enforcement status of workloads.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ccfe6-123">後續步驟</span><span class="sxs-lookup"><span data-stu-id="ccfe6-123">Next steps</span></span>

<span data-ttu-id="ccfe6-124">深入了解訂用帳戶和帳戶如何作為雲端部署的基礎。</span><span class="sxs-lookup"><span data-stu-id="ccfe6-124">Learn how subscriptions and accounts serve as the base of a cloud deployment.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="ccfe6-125">訂用帳戶設計</span><span class="sxs-lookup"><span data-stu-id="ccfe6-125">Subscriptions design</span></span>](subscriptions/overview.md)

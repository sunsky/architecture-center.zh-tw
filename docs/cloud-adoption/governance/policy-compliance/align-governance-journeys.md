---
title: CAF：讓設計指南與原則保持一致。
description: 如何讓設計指南與原則保持一致？
author: BrianBlanchard
ms.date: 01/04/2019
ms.openlocfilehash: 77a35597585e5f58967ea79d3c7b0fa17b6ab80e
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241589"
---
<!---
I've established policies. How to help developers adopt these policies?
Draft an architecture design guide.

[Aspirational statement] If you're using Azure, you can use one of ours as a starting point. The choose one of the following 6 as a starting point and mold it to fit your policies.
--->

<!-- markdownlint-disable MD026 -->

# <a name="how-do-you-align-design-guides-with-policy"></a><span data-ttu-id="c4a8d-103">如何讓設計指南與原則保持一致？</span><span class="sxs-lookup"><span data-stu-id="c4a8d-103">How do you align design guides with policy?</span></span>

<span data-ttu-id="c4a8d-104">在根據您的[已識別風險](understanding-business-risk.md)來[定義雲端原則](define-policy.md)之後，您必須產生可採取行動的指引，與 IT 人員和開發人員參考的這些原則保持一致。</span><span class="sxs-lookup"><span data-stu-id="c4a8d-104">After you've [defined cloud policies](define-policy.md) based on your [identified risks](understanding-business-risk.md), you'll need to generate actionable guidance that aligns with these policies for your IT staff and developers to refer to.</span></span> <span data-ttu-id="c4a8d-105">草擬雲端治理設計指南可讓您根據為五個治理專業領域產生的原則聲明，指定特定結構化、技術和流程選擇。</span><span class="sxs-lookup"><span data-stu-id="c4a8d-105">Drafting a cloud governance design guide allows you to specify specific structural, technological, and process choices based on the policy statements you generated for each of the five governance disciplines.</span></span>

<span data-ttu-id="c4a8d-106">雲端治理設計指南應該為最符合您原則需求之雲端部署的每個核心基礎結構元件，建立架構選擇和設計模式。</span><span class="sxs-lookup"><span data-stu-id="c4a8d-106">A cloud governance design guide should establish the architecture choices and design patterns for each of the core infrastructure components of cloud deployments that best meet your policy requirements.</span></span> <span data-ttu-id="c4a8d-107">除此之外，您應該提供將會支援每個設計決策之技術、工具和流程的高階說明。</span><span class="sxs-lookup"><span data-stu-id="c4a8d-107">Alongside these you should provide a high-level explanation of the technology, tools, and processes that will support each of these design decisions.</span></span>

<span data-ttu-id="c4a8d-108">雖然您的風險分析和原則聲明某種程度上是雲端平台無從驗證，您的設計指南應該提供平台特定實作詳細資料。</span><span class="sxs-lookup"><span data-stu-id="c4a8d-108">Although your risk analysis and policy statements may, to some degree, be cloud platform agnostic, your design guide should provide platform-specific implementation details that your IT and dev.</span></span> <span data-ttu-id="c4a8d-109">進行設計決策和提供指引時，專注於您所選擇平台的架構、工具和功能。</span><span class="sxs-lookup"><span data-stu-id="c4a8d-109">Focus on the architecture, tools, and features of your chosen platform when making design decision and providing guidance.</span></span>

<span data-ttu-id="c4a8d-110">雲端設計指南應該納入與每個基礎結構元件相關聯的一些技術詳細資料，它們不一定是廣泛的技術文件或規格。</span><span class="sxs-lookup"><span data-stu-id="c4a8d-110">While cloud design guides should take into account some of the technical details associated with each infrastructure component, they are not meant to be extensive technical documents or specifications.</span></span> <span data-ttu-id="c4a8d-111">請確定您的指南會解決所有原則聲明，並且以人員容易了解和參考的格式清楚地陳述設計決策。</span><span class="sxs-lookup"><span data-stu-id="c4a8d-111">Make sure your guides address all of your policy statements and clearly state design decisions in a format easy for staff to understand and reference.</span></span>

<!-- markdownlint-enable MD033 -->

## <a name="using-the-actionable-governance-journeys"></a><span data-ttu-id="c4a8d-112">使用可採取動作的治理旅程</span><span class="sxs-lookup"><span data-stu-id="c4a8d-112">Using the actionable governance journeys</span></span>

<span data-ttu-id="c4a8d-113">如果您計劃針對雲端採用使用 Azure 平台，CAF 提供[治理旅程](../journeys/overview.md)，說明 CAF 治理模型的累加方法。</span><span class="sxs-lookup"><span data-stu-id="c4a8d-113">If you're planning to use the Azure platform for your cloud adoption, the CAF provides [governance journeys](../journeys/overview.md) illustrating the incremental approach of the CAF governance model.</span></span> <span data-ttu-id="c4a8d-114">這個敘述旅程涵蓋通用採用案例的範圍，包括業務風險、承受度需求和原則聲明，用以建立治理最低可行性產品 (MVP)。</span><span class="sxs-lookup"><span data-stu-id="c4a8d-114">These narrative journeys cover a range of common adoption scenarios, including the business risks, tolerance requirements, and policy statements that went into creating a governance minimum viable product (MVP).</span></span> <span data-ttu-id="c4a8d-115">這些旅程代表真實世界客戶在 Azure 中雲端採用體驗的合成。</span><span class="sxs-lookup"><span data-stu-id="c4a8d-115">These journeys represent a synthesis of real-world customer experience of the cloud adoption process in Azure.</span></span>

<span data-ttu-id="c4a8d-116">雖然每個雲端採用都有唯一的目標、優先順序及挑戰，這些範例應該提供良好的範本，將您的原則轉換成指引。</span><span class="sxs-lookup"><span data-stu-id="c4a8d-116">While every cloud adoption has unique goals, priorities, and challenges, these samples should provide a good template for converting your policy into guidance.</span></span> <span data-ttu-id="c4a8d-117">挑選最接近您的情況的案例作為起點，並加以塑造以符合您的特定原則需求。</span><span class="sxs-lookup"><span data-stu-id="c4a8d-117">Pick the closest scenario to your situation as a starting point, and mold it to fit your specific policy needs.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c4a8d-118">後續步驟</span><span class="sxs-lookup"><span data-stu-id="c4a8d-118">Next steps</span></span>

<span data-ttu-id="c4a8d-119">設計指引就位時，建立原則遵循流程以確保原則合規性。</span><span class="sxs-lookup"><span data-stu-id="c4a8d-119">With design guidance in place, establish policy adherence processes to ensure policy compliance.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="c4a8d-120">原則遵循流程</span><span class="sxs-lookup"><span data-stu-id="c4a8d-120">Policy adherence processes</span></span>](processes.md)
---
title: CAF：成本管理原則合規性流程
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 成本管理原則合規性流程
author: BrianBlanchard
ms.openlocfilehash: 5fd007546589020f376107382c54c391cc5d05ad
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900759"
---
# <a name="cost-management-policy-compliance-processes"></a><span data-ttu-id="612cb-103">成本管理原則合規性流程</span><span class="sxs-lookup"><span data-stu-id="612cb-103">Cost Management policy compliance processes</span></span>

<span data-ttu-id="612cb-104">本文將討論如何建立支援[成本管理](./overview.md)治理專業領域的流程。</span><span class="sxs-lookup"><span data-stu-id="612cb-104">This article discusses an approach to creating processes that support a [Cost Management](./overview.md) governance discipline.</span></span> <span data-ttu-id="612cb-105">有效的雲端成本治理都起始於可支援原則合規性的重複手動程序。</span><span class="sxs-lookup"><span data-stu-id="612cb-105">Effective governance of cloud costs starts with recurring manual processes designed to support policy compliance.</span></span> <span data-ttu-id="612cb-106">這需要雲端治理小組和感興趣的業務專案關係人，定期檢閱及更新原則並確保原則合規性。</span><span class="sxs-lookup"><span data-stu-id="612cb-106">This requires regular involvement of the Cloud Governance team and interested business stakeholders to review and update policy and ensure policy compliance.</span></span> <span data-ttu-id="612cb-107">此外，許多進行中的監視和強制執行流程可以透過工具來自動化或補強，減少治理的額外負荷並且得以更快速回應原則偏差。</span><span class="sxs-lookup"><span data-stu-id="612cb-107">In addition, many ongoing monitoring and enforcement processes can be automated or supplemented with tooling to reduce the overhead of governance and allow for faster response to policy deviation.</span></span>

## <a name="planning-review-and-reporting-processes"></a><span data-ttu-id="612cb-108">規劃、檢閱及報告流程</span><span class="sxs-lookup"><span data-stu-id="612cb-108">Planning, review, and reporting processes</span></span>

<span data-ttu-id="612cb-109">即使是雲端中最佳的成本管理工具，其效果也就和其所支援的流程與原則相同而已。</span><span class="sxs-lookup"><span data-stu-id="612cb-109">The best Cost Management tools in the cloud are only as good as the processes and policies that they support.</span></span> <span data-ttu-id="612cb-110">以下是一組成本管理專業領域經常會牽涉到的範例流程。</span><span class="sxs-lookup"><span data-stu-id="612cb-110">The following is a set of example processes commonly involved in the Cost Management discipline.</span></span> <span data-ttu-id="612cb-111">當您規劃可讓您根據業務變更和業務小組遵循成本治理指引後的意見反應，來繼續更新成本原則的流程時，使用這些範例作為起點。</span><span class="sxs-lookup"><span data-stu-id="612cb-111">Use these examples as a starting point when planning the processes that will allow you to continue to update cost policy based on business change and feedback from the business teams subject to cost governance guidance.</span></span>

<span data-ttu-id="612cb-112">**初始風險評估及規劃**：在您初始採用成本管理專業領域時，識別您的核心業務風險和與雲端成本相關的承受度。</span><span class="sxs-lookup"><span data-stu-id="612cb-112">**Initial risk assessment and planning**: As part of your initial adoption of the Cost Management discipline, identify your core business risks and tolerances related to cloud costs.</span></span> <span data-ttu-id="612cb-113">使用此資訊與您的業務小組成員討論預算和成本相關風險，以及開發一組基本的原則來降低這些風險，以建立初始治理策略。</span><span class="sxs-lookup"><span data-stu-id="612cb-113">Use this information to discuss budget and cost-related risks with members of your business teams and develop a baseline set of policies for mitigating these risks to establish your initial governance strategy.</span></span>

<span data-ttu-id="612cb-114">**部署規劃：** 在部署任何資產之前，請根據預期的雲端配置建立預算的預測。</span><span class="sxs-lookup"><span data-stu-id="612cb-114">**Deployment planning:** Prior to deployment of any asset, establish a forecasted budget based on expected cloud allocation.</span></span> <span data-ttu-id="612cb-115">請務必記錄部署的擁有權和會計資訊。</span><span class="sxs-lookup"><span data-stu-id="612cb-115">Ensure that ownership and accounting information for the deployment is documented.</span></span>  

<span data-ttu-id="612cb-116">**年度規劃**：每年對所有已部署和要部署的資產執行彙總分析。</span><span class="sxs-lookup"><span data-stu-id="612cb-116">**Annual planning:** On an annual basis, perform a roll-up analysis on all deployed and to-be-deployed assets.</span></span> <span data-ttu-id="612cb-117">透過業務單位、部門、小組及其他適當的分配來推動自助採用，以遵循預算限制。</span><span class="sxs-lookup"><span data-stu-id="612cb-117">Align budgets by business units, departments, teams, and other appropriate divisions to empower self-service adoption.</span></span> <span data-ttu-id="612cb-118">確保每一個計費單位領導人都知道預算，以及知道如何追蹤費用。</span><span class="sxs-lookup"><span data-stu-id="612cb-118">Ensure that the leader of each billing unit is aware of the budget and how to track spending.</span></span>

<span data-ttu-id="612cb-119">這正是做出預先承諾和預先採購來最大化折扣的好時機。</span><span class="sxs-lookup"><span data-stu-id="612cb-119">This is the time to make a precommitment or prepurchase to maximize discounting.</span></span> <span data-ttu-id="612cb-120">最好能配合雲端廠商的會計年度來編訂年度預算，以進一步利用年底折扣的選擇。</span><span class="sxs-lookup"><span data-stu-id="612cb-120">It is wise to align annual budgeting with the cloud vendor's fiscal year to further capitalize on year-end discount options.</span></span>

<span data-ttu-id="612cb-121">**每季規劃：** 每季與每個計費單位的領導人一起檢閱預算，以校準預測和實際費用。</span><span class="sxs-lookup"><span data-stu-id="612cb-121">**Quarterly planning:** On a quarterly basis, review budgets with each billing unit leader to align forecast and actual spending.</span></span> <span data-ttu-id="612cb-122">如果計劃發生變更或有非預期的支出模式，應校正和重新配置預算。</span><span class="sxs-lookup"><span data-stu-id="612cb-122">If there are changes to the plan or unexpected spending patterns, align and reallocate the budget.</span></span>

<span data-ttu-id="612cb-123">這個每季規劃的流程也是評估您雲端治理小組目前成員資格，以了解目前和未來業務計畫差距的好時機。</span><span class="sxs-lookup"><span data-stu-id="612cb-123">This quarterly planning process is also a good time to evaluate the current membership of your Cloud Governance team for knowledge gaps related to current or future business plans.</span></span> <span data-ttu-id="612cb-124">邀請相關人員和工作負載擁有者來參與檢閱及規劃，無論是作為小組的暫時顧問或永久成員。</span><span class="sxs-lookup"><span data-stu-id="612cb-124">Invite relevant staff and workload owners to participate in reviews and planning as either temporary advisors or permanent members of your team.</span></span>

<span data-ttu-id="612cb-125">**教育與訓練**：每兩個月提供訓練課程，以確定業務和 IT 人員能跟上最新的成本原則需求。</span><span class="sxs-lookup"><span data-stu-id="612cb-125">**Education and Training**: On a bi-monthly basis, offer training sessions to make sure business and IT staff are up-to-date on the latest Cost Management policy requirements.</span></span> <span data-ttu-id="612cb-126">在此流程中檢閱及更新任何文件、指導方針或其他訓練資產，以確保這些項目與最新的公司原則聲明同步。</span><span class="sxs-lookup"><span data-stu-id="612cb-126">As part of this process review and update any documentation, guidance, or other training assets to ensure they are in sync with the latest corporate policy statements.</span></span>

<span data-ttu-id="612cb-127">**每月報告：** 每月報告實際費用，並與預測最比對。</span><span class="sxs-lookup"><span data-stu-id="612cb-127">**Monthly reporting:** On a monthly basis, report actual spending against forecast.</span></span> <span data-ttu-id="612cb-128">將任何非預期的差異通知計費領導者。</span><span class="sxs-lookup"><span data-stu-id="612cb-128">Notify billing leaders of any unexpected deviations.</span></span>

<span data-ttu-id="612cb-129">這些基本程序將有助於校正花費，並建立成本管理專業領域的基礎。</span><span class="sxs-lookup"><span data-stu-id="612cb-129">These basic processes will help align spending and establish a foundation for the Cost Management discipline.</span></span>

## <a name="ongoing-monitoring-processes"></a><span data-ttu-id="612cb-130">持續監視流程</span><span class="sxs-lookup"><span data-stu-id="612cb-130">Ongoing monitoring processes</span></span>

<span data-ttu-id="612cb-131">成功的成本管理治理策略取決於對過去、現在及未來雲端相關費用的了解程度。</span><span class="sxs-lookup"><span data-stu-id="612cb-131">A successful Cost Management governance strategy depends on visibility into the past, current, and planned future cloud-related spending.</span></span> <span data-ttu-id="612cb-132">如果無法分析現有成本相關計量和資料，您就無法識別風險的變更或者偵測是否違反風險承受度。</span><span class="sxs-lookup"><span data-stu-id="612cb-132">Without the ability to analyze the relevant metrics and data of your existing costs, you cannot identify changes in your risks or detect violations of your risk tolerances.</span></span> <span data-ttu-id="612cb-133">上面所討論的持續治理流程需要品質資料，以確保可以針對不斷變化的業務需求和雲端使用方式，修改原則以便更佳地保護您的基礎結構。</span><span class="sxs-lookup"><span data-stu-id="612cb-133">The ongoing governance processes discussed above require quality data to ensure policy can be modified to better protect your infrastructure against changing business requirements and cloud usage.</span></span>

<span data-ttu-id="612cb-134">請確定您的 IT 小組已實作自動化系統來監視您的雲端費用和使用方式，以找出與預期成本相比之下的非預期差異。</span><span class="sxs-lookup"><span data-stu-id="612cb-134">Ensure that your IT teams have implemented automated systems for monitoring your cloud spending and usage for unplanned deviations from expected costs.</span></span> <span data-ttu-id="612cb-135">建立報告和警示系統，以確保能快速偵測和降低潛在的原則違規風險。</span><span class="sxs-lookup"><span data-stu-id="612cb-135">Establish reporting and alerting systems to ensure prompt detection and mitigation of potential policy violations.</span></span>

## <a name="compliance-violation-triggers-and-enforcement-actions"></a><span data-ttu-id="612cb-136">違反合規性的觸發程序及強制執行動作</span><span class="sxs-lookup"><span data-stu-id="612cb-136">Compliance violation triggers and enforcement actions</span></span>

<span data-ttu-id="612cb-137">偵測到違規時，您應該採取強制動作來校正原則。</span><span class="sxs-lookup"><span data-stu-id="612cb-137">When violations are detected, you should take enforcement actions to realign with policy.</span></span> <span data-ttu-id="612cb-138">您的可以使用[適用於 Azure 的成本管理工具鏈](toolchain.md)中概述的工具，將大部分違規觸發程序自動化。</span><span class="sxs-lookup"><span data-stu-id="612cb-138">You can automate most violation triggers using the tools outlined in the [Cost Management toolchain for Azure](toolchain.md).</span></span>

<span data-ttu-id="612cb-139">以下為觸發條件的範例：</span><span class="sxs-lookup"><span data-stu-id="612cb-139">The following are examples of triggers:</span></span>

* <span data-ttu-id="612cb-140">每月預算偏差：與計費單位領導者討論預測與實際比率超過 20% 的每用費用。</span><span class="sxs-lookup"><span data-stu-id="612cb-140">Monthly budget deviations: Discuss any deviations in monthly spending that exceed 20% forecast-versus-actual ratio with the billing unit leader.</span></span> <span data-ttu-id="612cb-141">記錄解決方法與預測中的變更。</span><span class="sxs-lookup"><span data-stu-id="612cb-141">Record resolutions and changes in forecast.</span></span>
* <span data-ttu-id="612cb-142">採用步調：訂用帳戶層級上任何超過 20% 的偏差都會觸發計費單位領導人進行檢閱。</span><span class="sxs-lookup"><span data-stu-id="612cb-142">Pace of adoption: Any deviation at a subscription level exceeding 20% will trigger a review with billing unit leader.</span></span> <span data-ttu-id="612cb-143">記錄解決方法與預測中的變更。</span><span class="sxs-lookup"><span data-stu-id="612cb-143">Record resolutions and changes in forecast.</span></span>

## <a name="next-steps"></a><span data-ttu-id="612cb-144">後續步驟</span><span class="sxs-lookup"><span data-stu-id="612cb-144">Next steps</span></span>

<span data-ttu-id="612cb-145">使用[雲端管理範本](./template.md)，記錄與目前雲端採用方案一致的流程和觸發程序。</span><span class="sxs-lookup"><span data-stu-id="612cb-145">Using the [Cloud Management template](./template.md), document the processes and triggers that align to the current cloud adoption plan.</span></span>

<span data-ttu-id="612cb-146">如需執行與採用方案一致的雲端管理原則指引，請參閱有關成本管理專業領域改進的文章。</span><span class="sxs-lookup"><span data-stu-id="612cb-146">For guidance on executing cloud management policies in alignment with adoption plans, see the article on Cost Management discipline improvement.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="612cb-147">成本管理專業領域改進</span><span class="sxs-lookup"><span data-stu-id="612cb-147">Cost Management discipline improvement</span></span>](./discipline-improvement.md)

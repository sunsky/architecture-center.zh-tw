---
title: 'CAF：中小型企業 – 成本管理演進  '
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 2/11/2019
description: 說明中小型企業 – 成本管理演進
author: BrianBlanchard
ms.openlocfilehash: ca070bfca3efbf9548faa8cf28a1adffefd4a33a
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900459"
---
# <a name="small-to-medium-enterprise-cost-management-evolution"></a><span data-ttu-id="3525e-103">中小型企業：成本管理演進</span><span class="sxs-lookup"><span data-stu-id="3525e-103">Small-to-medium enterprise: Cost Management evolution</span></span>

<span data-ttu-id="3525e-104">本文藉由將成本控制新增至治理 MVP 來推演敘述。</span><span class="sxs-lookup"><span data-stu-id="3525e-104">This article evolves the narrative by adding cost controls to the governance MVP.</span></span>

## <a name="evolution-of-the-narrative"></a><span data-ttu-id="3525e-105">敘述的推演</span><span class="sxs-lookup"><span data-stu-id="3525e-105">Evolution of the narrative</span></span>

<span data-ttu-id="3525e-106">採用已成長超越治理 MVP 中定義的成本承受度指標。</span><span class="sxs-lookup"><span data-stu-id="3525e-106">Adoption has grown beyond the cost tolerance indicator defined in the governance MVP.</span></span> <span data-ttu-id="3525e-107">這樣很好，因為它與 "DR" 資料中心的移轉相對應。</span><span class="sxs-lookup"><span data-stu-id="3525e-107">This is a good thing, as it corresponds with migrations from the "DR" datacenter.</span></span> <span data-ttu-id="3525e-108">花費的增加現在證明了雲端治理小組對於時間的投資。</span><span class="sxs-lookup"><span data-stu-id="3525e-108">The increase in spending now justifies an investment of time from the Cloud Governance team.</span></span>

### <a name="evolution-of-the-current-state"></a><span data-ttu-id="3525e-109">目前狀態的演進</span><span class="sxs-lookup"><span data-stu-id="3525e-109">Evolution of the current state</span></span>

<span data-ttu-id="3525e-110">在此敘述的先前階段中，IT 已淘汰 100% 的 DR 資料中心。</span><span class="sxs-lookup"><span data-stu-id="3525e-110">In the previous phase of this narrative, IT had retired 100% of the DR datacenter.</span></span> <span data-ttu-id="3525e-111">應用程式開發和 BI 小組已準備好使用生產環境流量。</span><span class="sxs-lookup"><span data-stu-id="3525e-111">The application development and BI teams were ready for production traffic.</span></span>

<span data-ttu-id="3525e-112">從那時起，某些事項已經改變，將會影響治理：</span><span class="sxs-lookup"><span data-stu-id="3525e-112">Since then, some things have changed that will affect governance:</span></span>

- <span data-ttu-id="3525e-113">移轉小組已開始將 VM 移轉出生產環境資料中心外。</span><span class="sxs-lookup"><span data-stu-id="3525e-113">The migration team has begun migrating VMs out of the production datacenter.</span></span>
- <span data-ttu-id="3525e-114">應用程式開發小組會積極地透過 CI/CD 管線將生產環境應用程式推送至雲端。</span><span class="sxs-lookup"><span data-stu-id="3525e-114">The application development teams is actively pushing production applications to the cloud through CI/CD pipelines.</span></span> <span data-ttu-id="3525e-115">這些應用程式可以因應使用者需求被動地進行調整。</span><span class="sxs-lookup"><span data-stu-id="3525e-115">Those applications can reactively scale with user demands.</span></span>
- <span data-ttu-id="3525e-116">IT 內的商業智慧小組已在雲端中傳遞一些預測性分析工具。</span><span class="sxs-lookup"><span data-stu-id="3525e-116">The business intelligence team within IT has delivered a number of predictive analytics tools in the cloud.</span></span> <span data-ttu-id="3525e-117">在雲端中彙總的資料量持續成長。</span><span class="sxs-lookup"><span data-stu-id="3525e-117">the volumes of data aggregated in the cloud continues to grow.</span></span>
- <span data-ttu-id="3525e-118">此成長支援認可業務成果。</span><span class="sxs-lookup"><span data-stu-id="3525e-118">All of this growth supports committed business outcomes.</span></span> <span data-ttu-id="3525e-119">不過，成本已開始迅速成長。</span><span class="sxs-lookup"><span data-stu-id="3525e-119">However, costs have begun to mushroom.</span></span> <span data-ttu-id="3525e-120">預計預算的成長速度比預期更快。</span><span class="sxs-lookup"><span data-stu-id="3525e-120">Projected budgets are growing faster than expected.</span></span> <span data-ttu-id="3525e-121">CFO 需要改進管理成本的方法。</span><span class="sxs-lookup"><span data-stu-id="3525e-121">The CFO needs improved approaches to managing costs.</span></span>

### <a name="evolution-of-the-future-state"></a><span data-ttu-id="3525e-122">未來狀態的演進</span><span class="sxs-lookup"><span data-stu-id="3525e-122">Evolution of the future state</span></span>

<span data-ttu-id="3525e-123">成本監視和報告要新增至雲端解決方案。</span><span class="sxs-lookup"><span data-stu-id="3525e-123">Cost monitoring and reporting is to be added to the cloud solution.</span></span> <span data-ttu-id="3525e-124">IT 仍然作為成本結算所。</span><span class="sxs-lookup"><span data-stu-id="3525e-124">IT is still serving as a cost clearing house.</span></span> <span data-ttu-id="3525e-125">這表示 IT 採購會持續有雲端服務的付款。</span><span class="sxs-lookup"><span data-stu-id="3525e-125">This means that payment for cloud services continues to come from IT procurement.</span></span> <span data-ttu-id="3525e-126">不過，報告應該將直接操作費用繫結至耗用雲端成本的功能。</span><span class="sxs-lookup"><span data-stu-id="3525e-126">However, reporting should tie direct operational expenses to the functions that are consuming the cloud costs.</span></span> <span data-ttu-id="3525e-127">此模型稱為雲端帳戶處理的「上一步」模型。</span><span class="sxs-lookup"><span data-stu-id="3525e-127">This model is referred to as "Show Back" model to cloud accounting.</span></span>

<span data-ttu-id="3525e-128">目前和未來狀態的變更會公開新的風險，需要新的原則聲明。</span><span class="sxs-lookup"><span data-stu-id="3525e-128">The changes to current and future state expose new risks that will require new policy statements.</span></span>

## <a name="evolution-of-tangible-risks"></a><span data-ttu-id="3525e-129">有形風險的演進</span><span class="sxs-lookup"><span data-stu-id="3525e-129">Evolution of tangible risks</span></span>

<span data-ttu-id="3525e-130">**預算控制**：有一個固有風險就是自助功能會導致新平台的超量和非預期成本。</span><span class="sxs-lookup"><span data-stu-id="3525e-130">**Budget control**: There is an inherent risk that self-service capabilities will result in excessive and unexpected costs on the new platform.</span></span> <span data-ttu-id="3525e-131">監視成本及降低持續成本風險的治理流程必須就位，以確保與規劃預算持續保持一致。</span><span class="sxs-lookup"><span data-stu-id="3525e-131">Governance processes for monitoring costs and mitigating ongoing cost risks must be in place to ensure continued alignment with the planned budget.</span></span>

<span data-ttu-id="3525e-132">此業務風險會延伸成少數技術風險：</span><span class="sxs-lookup"><span data-stu-id="3525e-132">This business risk can be expanded into a few technical risks:</span></span>

- <span data-ttu-id="3525e-133">實際成本可能會超過計劃。</span><span class="sxs-lookup"><span data-stu-id="3525e-133">Actual costs might exceed the plan.</span></span>
- <span data-ttu-id="3525e-134">業務狀況變更。</span><span class="sxs-lookup"><span data-stu-id="3525e-134">Business conditions change.</span></span> <span data-ttu-id="3525e-135">發生變更時，業務功能有可能必須耗用比預期還多的雲端服務，造成異常支出。</span><span class="sxs-lookup"><span data-stu-id="3525e-135">When they do, there will be cases when a business function needs to consume more cloud services than expected, leading to spending anomalies.</span></span> <span data-ttu-id="3525e-136">會有此額外支出被視為超額 (相對於計劃的必要調整) 的風險。</span><span class="sxs-lookup"><span data-stu-id="3525e-136">There is a risk that this extra spending will be considered overages, as opposed to a necessary adjustment to the plan.</span></span>
- <span data-ttu-id="3525e-137">系統可能會過度佈建，導致過多支出。</span><span class="sxs-lookup"><span data-stu-id="3525e-137">Systems could be overprovisioned, resulting in excess spending.</span></span>

## <a name="evolution-of-the-policy-statements"></a><span data-ttu-id="3525e-138">原則聲明的演進</span><span class="sxs-lookup"><span data-stu-id="3525e-138">Evolution of the policy statements</span></span>

<span data-ttu-id="3525e-139">下列原則變更有助於降低新風險和指導實作。</span><span class="sxs-lookup"><span data-stu-id="3525e-139">The following changes to policy will help mitigate the new risks and guide implementation.</span></span>

1. <span data-ttu-id="3525e-140">治理小組應該針對計劃每週監視所有雲端成本。</span><span class="sxs-lookup"><span data-stu-id="3525e-140">All cloud costs should be monitored against plan on a weekly basis by the governance team.</span></span> <span data-ttu-id="3525e-141">雲端成本與計劃之間偏差的報告要每月與 IT 主管和財務部門分享。</span><span class="sxs-lookup"><span data-stu-id="3525e-141">Reporting on deviations between cloud costs and plan is to be shared with IT leadership and finance monthly.</span></span> <span data-ttu-id="3525e-142">所有雲端成本和計劃更新應該要每月與 IT 主管和財務部門一起檢閱。</span><span class="sxs-lookup"><span data-stu-id="3525e-142">All cloud costs and plan updates should be reviewed with IT leadership and finance monthly.</span></span>
2. <span data-ttu-id="3525e-143">所有成本必須針對權責目的配置給業務功能。</span><span class="sxs-lookup"><span data-stu-id="3525e-143">All costs must be allocated to a business function for accountability purposes.</span></span>
3. <span data-ttu-id="3525e-144">應該針對最佳化商機持續監視雲端資產。</span><span class="sxs-lookup"><span data-stu-id="3525e-144">Cloud assets should be continually monitored for optimization opportunities.</span></span>
4. <span data-ttu-id="3525e-145">雲端治理工具必須將資產調整大小選項限制為設定的核准清單。</span><span class="sxs-lookup"><span data-stu-id="3525e-145">Cloud Governance tooling must limit Asset sizing options to an approved list of configurations.</span></span> <span data-ttu-id="3525e-146">工具必須確保所有資產都可探索且可由成本監視解決方案追蹤。</span><span class="sxs-lookup"><span data-stu-id="3525e-146">The tooling must ensure that all assets are discoverable and tracked by the cost monitoring solution.</span></span>
5. <span data-ttu-id="3525e-147">在部署規劃期間，應該記錄與生產環境工作負載裝載相關聯的任何必要雲端資源。</span><span class="sxs-lookup"><span data-stu-id="3525e-147">During deployment planning, any required cloud resources associated with the hosting of production workloads should be documented.</span></span> <span data-ttu-id="3525e-148">本文件可協助精簡預算及準備其他自動化，以避免使用較昂貴的選項。</span><span class="sxs-lookup"><span data-stu-id="3525e-148">This documentation will help refine budgets and prepare additional automation to prevent the use of more expensive options.</span></span> <span data-ttu-id="3525e-149">在此流程期間，應該考量雲端提供者提供的不同折扣工具，例如保留執行個體或授權成本降低。</span><span class="sxs-lookup"><span data-stu-id="3525e-149">During this process consideration should be given to different discounting tools offered by the cloud provider, such as reserved instances or license cost reductions.</span></span>
6. <span data-ttu-id="3525e-150">所有應用程式擁有者必須參加最佳化工作負載的實務訓練，以便更佳地控制雲端成本。</span><span class="sxs-lookup"><span data-stu-id="3525e-150">All application owners are required to attend trained on practices for optimizing workloads to better control cloud costs.</span></span>

## <a name="evolution-of-the-best-practices"></a><span data-ttu-id="3525e-151">最佳做法的演進</span><span class="sxs-lookup"><span data-stu-id="3525e-151">Evolution of the best practices</span></span>

<span data-ttu-id="3525e-152">文章的這一節會演進治理 MVP 設計，以包含新的 Azure 原則和實作 Azure 成本管理。</span><span class="sxs-lookup"><span data-stu-id="3525e-152">This section of the article will evolve the governance MVP design to include new Azure policies and an implementation of Azure Cost Management.</span></span> <span data-ttu-id="3525e-153">這兩個設計變更會履行新的公司原則聲明。</span><span class="sxs-lookup"><span data-stu-id="3525e-153">Together, these two design changes will fulfill the new corporate policy statements.</span></span>

1. <span data-ttu-id="3525e-154">實作 Azure 成本管理</span><span class="sxs-lookup"><span data-stu-id="3525e-154">Implement Azure Cost Management</span></span>
    1. <span data-ttu-id="3525e-155">建立存取的正確範圍，與訂用帳戶模式和資源一致性專業領域保持一致。</span><span class="sxs-lookup"><span data-stu-id="3525e-155">Establish the right scope of access to align with the subscription pattern and the Resource Consistency discipline.</span></span> <span data-ttu-id="3525e-156">假設與先前文章中定義的治理 MVP 保持一致，需要執行高階報告之雲端治理小組的**註冊帳戶範圍**存取權。</span><span class="sxs-lookup"><span data-stu-id="3525e-156">Assuming alignment with the governance MVP defined in prior articles, this requires **Enrollment Account Scope** access for the Cloud Governance team executing on high-level reporting.</span></span> <span data-ttu-id="3525e-157">治理之外的其他小組可能需要**資源群組範圍**存取權。</span><span class="sxs-lookup"><span data-stu-id="3525e-157">Additional teams outside of governance may require **Resource Group Scope** access.</span></span>
    2. <span data-ttu-id="3525e-158">在 Azure 成本管理中建立預算。</span><span class="sxs-lookup"><span data-stu-id="3525e-158">Establish a budget in Azure Cost Management.</span></span>
    3. <span data-ttu-id="3525e-159">檢閱初始建議並且採取動作。</span><span class="sxs-lookup"><span data-stu-id="3525e-159">Review and act on initial recommendations.</span></span> <span data-ttu-id="3525e-160">進行週期性流程以支援報告。</span><span class="sxs-lookup"><span data-stu-id="3525e-160">Have a recurring process to support reporting.</span></span>
    4. <span data-ttu-id="3525e-161">設定及執行初始和週期性 Azure 成本管理報告。</span><span class="sxs-lookup"><span data-stu-id="3525e-161">Configure and execute Azure Cost Management Reporting, both initial and recurring.</span></span>
2. <span data-ttu-id="3525e-162">更新 Azure 原則</span><span class="sxs-lookup"><span data-stu-id="3525e-162">Update Azure Policy</span></span>
    1. <span data-ttu-id="3525e-163">稽核標記、管理群組、訂用帳戶及資源群組值，以識別任何偏差。</span><span class="sxs-lookup"><span data-stu-id="3525e-163">Audit the tagging, management group, subscription, and resource group values to identify any deviation.</span></span>
    2. <span data-ttu-id="3525e-164">建立 SKU 大小選項以限制對於部署規劃文件中列出之 SKU 的部署。</span><span class="sxs-lookup"><span data-stu-id="3525e-164">Establish SKU size options to limit deployments to SKUs listed in deployment planning documentation.</span></span>

## <a name="conclusion"></a><span data-ttu-id="3525e-165">結論</span><span class="sxs-lookup"><span data-stu-id="3525e-165">Conclusion</span></span>

<span data-ttu-id="3525e-166">對治理 MVP 新增這些流程和變更，有助於降低與成本治理相關聯的許多風險。</span><span class="sxs-lookup"><span data-stu-id="3525e-166">Adding these processes and changes to the governance MVP helps mitigate many of the risks associated with cost governance.</span></span> <span data-ttu-id="3525e-167">它們會建立控制成本所需的可見度、權責和最佳化。</span><span class="sxs-lookup"><span data-stu-id="3525e-167">Together, they create the visibility, accountability, and optimization needed to control costs.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3525e-168">後續步驟</span><span class="sxs-lookup"><span data-stu-id="3525e-168">Next steps</span></span>

<span data-ttu-id="3525e-169">隨著雲端採用持續演進，並且提供額外的商業價值，風險和雲端治理需求也需要與時俱進。</span><span class="sxs-lookup"><span data-stu-id="3525e-169">As cloud adoption continues to evolve and deliver additional business value, risks and cloud governance needs will also evolve.</span></span> <span data-ttu-id="3525e-170">針對這趟旅程中的虛構公司，下一個步驟是使用此治理投資來管理多個雲端。</span><span class="sxs-lookup"><span data-stu-id="3525e-170">For the fictional company in this journey, the next step is using this governance investment to manage multiple clouds.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="3525e-171">多重雲端演進</span><span class="sxs-lookup"><span data-stu-id="3525e-171">Multi-cloud evolution</span></span>](./multi-cloud-evolution.md)

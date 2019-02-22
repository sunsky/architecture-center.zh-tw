---
title: CAF：大型企業 – 成本管理演進
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 大型企業 – 成本管理演進
author: BrianBlanchard
ms.openlocfilehash: 6bf63e36f6fb19dd0e5f9a16a7f66eb6e0ed54ff
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900927"
---
# <a name="large-enterprise-cost-management-evolution"></a><span data-ttu-id="77e80-103">大型企業：成本管理演進</span><span class="sxs-lookup"><span data-stu-id="77e80-103">Large enterprise: Cost Management evolution</span></span>

<span data-ttu-id="77e80-104">本文藉由將成本控制新增至最簡可行產品 (MVP) 治理來推演敘述。</span><span class="sxs-lookup"><span data-stu-id="77e80-104">This article evolves the narrative by adding cost controls to the minimum viable product (MVP) governance.</span></span>

## <a name="evolution-of-the-narrative"></a><span data-ttu-id="77e80-105">敘述的演進</span><span class="sxs-lookup"><span data-stu-id="77e80-105">Evolution of the narrative</span></span>

<span data-ttu-id="77e80-106">採用規模已超越治理 MVP 中所定義的承受度指標。</span><span class="sxs-lookup"><span data-stu-id="77e80-106">Adoption has grown beyond the tolerance indicator defined in the governance MVP.</span></span> <span data-ttu-id="77e80-107">支出增加現在證明了雲端治理小組投入時間來監視與控制支出模式是合理的。</span><span class="sxs-lookup"><span data-stu-id="77e80-107">The increases in spending now justifies an investment of time from the Cloud Governance team to monitor and control spending patterns.</span></span>

<span data-ttu-id="77e80-108">做為全新創新技術的先驅，不再只是將 IT 視為成本中心。</span><span class="sxs-lookup"><span data-stu-id="77e80-108">As a clear driver of innovation, IT is no longer seen primarily as a cost center.</span></span> <span data-ttu-id="77e80-109">隨著 IT 組織提供更多價值，CIO 和 CFO 均同意現在正是時候來演進 IT 在公司中所扮演的角色。</span><span class="sxs-lookup"><span data-stu-id="77e80-109">As the IT organization delivers more value, the CIO and CFO agree that the time is right to evolve the role IT plays in the company.</span></span> <span data-ttu-id="77e80-110">在其他變更之間，CFO 想要針對某個營業單位的加拿大分公司，測試用於雲端帳戶處理的直接付款方法。</span><span class="sxs-lookup"><span data-stu-id="77e80-110">Amongst other changes, the CFO wants to test a direct pay approach to cloud accounting for the Canadian branch of one of the business units.</span></span> <span data-ttu-id="77e80-111">這兩個已淘汰的資料中心之一，專門裝載可供該營業單位的加拿大營運使用的資產。</span><span class="sxs-lookup"><span data-stu-id="77e80-111">One of the two retired datacenters was exclusively hosted assets for that business unit’s Canadian operations.</span></span> <span data-ttu-id="77e80-112">在此模型中，該營業單位的加拿大分公司將直接支付與所裝載資產相關的操作費用。</span><span class="sxs-lookup"><span data-stu-id="77e80-112">In this model, the business unit’s Canadian subsidiary will be billed directly for the operational expenses related to the hosted assets.</span></span> <span data-ttu-id="77e80-113">此模型可讓 IT 減少投注於管理其他人支出的心力，而能更專注於創造價值。</span><span class="sxs-lookup"><span data-stu-id="77e80-113">This model allows IT to focus less on managing someone else’s spending and more on creating value.</span></span> <span data-ttu-id="77e80-114">不過，成本管理工具必須先就緒，才能開始這個轉換。</span><span class="sxs-lookup"><span data-stu-id="77e80-114">However, before this transition can begin Cost Management tooling needs to be in place.</span></span>

### <a name="evolution-of-current-state"></a><span data-ttu-id="77e80-115">目前狀態的演進</span><span class="sxs-lookup"><span data-stu-id="77e80-115">Evolution of current state</span></span>

<span data-ttu-id="77e80-116">在此敘述的上一個階段，IT 小組已主動將含有受保護資料的生產工作負載移到 Azure。</span><span class="sxs-lookup"><span data-stu-id="77e80-116">In the previous phase of this narrative, the IT team was actively moving production workloads with protected data into Azure.</span></span>

<span data-ttu-id="77e80-117">從那時起，某些將會影響治理的事項已經改變：</span><span class="sxs-lookup"><span data-stu-id="77e80-117">Since then, some things have changed that will affect governance:</span></span>

- <span data-ttu-id="77e80-118">已從這兩個標記為淘汰的資料中心移除了 5,000 個資產。</span><span class="sxs-lookup"><span data-stu-id="77e80-118">5,000 assets have been removed from the two datacenters flagged for retirement.</span></span> <span data-ttu-id="77e80-119">採購和 IT 安全性會立即取消佈建剩餘的實體資產。</span><span class="sxs-lookup"><span data-stu-id="77e80-119">Procurement and IT security are now deprovisioning the remaining physical assets.</span></span>
- <span data-ttu-id="77e80-120">應用程式開發小組已實作 CI/CD 管線，來部署一些會大幅影響客戶體驗的雲端原生應用程式。</span><span class="sxs-lookup"><span data-stu-id="77e80-120">The application development teams have implemented CI/CD pipelines to deploy a number of cloud native applications, significantly affecting customer experiences.</span></span>
- <span data-ttu-id="77e80-121">BI 小組已建立彙總、鑑藏、深入解析及預測流程，以便為企業營運帶來實質利益。</span><span class="sxs-lookup"><span data-stu-id="77e80-121">The BI team has created aggregation, curation, insight, and prediction processes driving tangible benefits for business operations.</span></span> <span data-ttu-id="77e80-122">那些預測現在賦予了極富創意的新產品和服務。</span><span class="sxs-lookup"><span data-stu-id="77e80-122">Those predictions are now empowering creative new products and services.</span></span>

### <a name="evolution-of-future-state"></a><span data-ttu-id="77e80-123">未來狀態的演進</span><span class="sxs-lookup"><span data-stu-id="77e80-123">Evolution of future state</span></span>

- <span data-ttu-id="77e80-124">成本監視和報告要新增至雲端解決方案。</span><span class="sxs-lookup"><span data-stu-id="77e80-124">Cost monitoring and reporting is to be added to the cloud solution.</span></span> <span data-ttu-id="77e80-125">報告應該將直接操作費用繫結至耗用雲端成本的功能。</span><span class="sxs-lookup"><span data-stu-id="77e80-125">Reporting should tie direct operational expenses to the functions that are consuming the cloud costs.</span></span> <span data-ttu-id="77e80-126">其他報告應該可讓 IT 監視支出，並提供關於成本管理的技術指引。</span><span class="sxs-lookup"><span data-stu-id="77e80-126">Additional reporting should allow IT to monitor spending and provide technical guidance on cost management.</span></span> <span data-ttu-id="77e80-127">針對加拿大分公司，該部門將直接支付費用。</span><span class="sxs-lookup"><span data-stu-id="77e80-127">For the Canadian branch, the department will be billed directly.</span></span>

## <a name="evolution-of-tangible-risks"></a><span data-ttu-id="77e80-128">實質風險的演進</span><span class="sxs-lookup"><span data-stu-id="77e80-128">Evolution of tangible risks</span></span>

<span data-ttu-id="77e80-129">**預算控制**：有一個固有風險是，自助功能將在新平台上導致超量且非預期的成本。</span><span class="sxs-lookup"><span data-stu-id="77e80-129">**Budget control**: There is an inherent risk that self-service capabilities will result in excessive and unexpected costs on the new platform.</span></span> <span data-ttu-id="77e80-130">監視成本及降低持續成本風險的治理流程必須就緒，才能確保會持續與規劃的預算保持一致。</span><span class="sxs-lookup"><span data-stu-id="77e80-130">Governance processes for monitoring costs and mitigating ongoing cost risks must be in place to ensure continued alignment with the planned budget.</span></span>

<span data-ttu-id="77e80-131">此業務風險可能會延伸出少數技術風險：</span><span class="sxs-lookup"><span data-stu-id="77e80-131">This business risk can be expanded into a few technical risks:</span></span>

- <span data-ttu-id="77e80-132">有一個關於實際成本的風險是會超出方案。</span><span class="sxs-lookup"><span data-stu-id="77e80-132">There is a risk of actual costs exceeding the plan.</span></span>
- <span data-ttu-id="77e80-133">業務狀況變更。</span><span class="sxs-lookup"><span data-stu-id="77e80-133">Business conditions change.</span></span> <span data-ttu-id="77e80-134">發生變更時，將出現業務功能必須耗用比預期還多之雲端服務的情況，因而導致支出異常狀況。</span><span class="sxs-lookup"><span data-stu-id="77e80-134">When they do, there will be cases when a business function needs to consume more cloud services than expected, leading to spending anomalies.</span></span> <span data-ttu-id="77e80-135">若將這些額外的成本視為超額部分，而不是必要的方案調整，則會有風險存在。</span><span class="sxs-lookup"><span data-stu-id="77e80-135">There is a risk that these additional costs would be seen as overages as opposed to a required adjustment to the plan.</span></span> <span data-ttu-id="77e80-136">如果成功，加拿大的實驗應該就能協助降低此風險。</span><span class="sxs-lookup"><span data-stu-id="77e80-136">If successful, the Canadian experiment should help mitigate this risk.</span></span>
- <span data-ttu-id="77e80-137">過度佈建系統，因而導致超額支出也是一個風險。</span><span class="sxs-lookup"><span data-stu-id="77e80-137">There is a risk of systems being overprovisioned, resulting in excess spending.</span></span>

## <a name="evolution-of-the-policy-statements"></a><span data-ttu-id="77e80-138">原則聲明的演進</span><span class="sxs-lookup"><span data-stu-id="77e80-138">Evolution of the policy statements</span></span>

<span data-ttu-id="77e80-139">下列原則變更有助於降低新風險和指導實作。</span><span class="sxs-lookup"><span data-stu-id="77e80-139">The following changes to policy will help mitigate the new risks and guide implementation.</span></span>

1. <span data-ttu-id="77e80-140">成本治理小組每週都應針對方案監視所有雲端成本。</span><span class="sxs-lookup"><span data-stu-id="77e80-140">All cloud costs should be monitored against plan on a weekly basis by the Cloud Governance team.</span></span> <span data-ttu-id="77e80-141">雲端成本與方案之間的偏差報告，每月都要與 IT 主管和財務部門分享。</span><span class="sxs-lookup"><span data-stu-id="77e80-141">Reporting on deviations between cloud costs and plan is to be shared with IT leadership and finance monthly.</span></span> <span data-ttu-id="77e80-142">所有雲端成本和方案更新，每月都應該與 IT 主管和財務部門一起檢閱。</span><span class="sxs-lookup"><span data-stu-id="77e80-142">All cloud costs and plan updates should be reviewed with IT leadership and finance monthly.</span></span>
2. <span data-ttu-id="77e80-143">所有成本都必須基於責任歸屬目的配置給企業功能。</span><span class="sxs-lookup"><span data-stu-id="77e80-143">All costs must be allocated to a business function for accountability purposes</span></span>
3. <span data-ttu-id="77e80-144">您應該持續監視雲端資產，以獲得最佳化的機會。</span><span class="sxs-lookup"><span data-stu-id="77e80-144">Cloud assets should be continually monitored for optimization opportunities</span></span>
4. <span data-ttu-id="77e80-145">雲端治理工具必須將資產調整大小選項限制為已核准的設定清單。</span><span class="sxs-lookup"><span data-stu-id="77e80-145">Cloud Governance tooling must limit Asset sizing options to an approved list of configurations.</span></span> <span data-ttu-id="77e80-146">此工具必須確保所有資產都可探索且可透過成本監視解決方案來追蹤。</span><span class="sxs-lookup"><span data-stu-id="77e80-146">The tooling must ensure that all assets are discoverable and tracked by the cost monitoring solution.</span></span>
5. <span data-ttu-id="77e80-147">在部署規劃期間，應該記載與裝載生產工作負載相關聯的任何必要雲端資源。</span><span class="sxs-lookup"><span data-stu-id="77e80-147">During deployment planning, any required cloud resources associated with the hosting of production workloads should be documented.</span></span> <span data-ttu-id="77e80-148">本文件將協助精簡預算並準備其他自動化，以避免使用較昂貴的選項。</span><span class="sxs-lookup"><span data-stu-id="77e80-148">This documentation will help refine budgets and prepare additional automations to prevent the use of more expensive options.</span></span> <span data-ttu-id="77e80-149">在此流程期間，應該考量雲端提供者所提供的不同折扣工具，例如，保留執行個體或授權成本降低。</span><span class="sxs-lookup"><span data-stu-id="77e80-149">During this process consideration should be given to different discounting tools offered by the cloud provider, such as Reserved Instances or License cost reductions.</span></span>
6. <span data-ttu-id="77e80-150">所有應用程式擁有者都必須參加將工作負載最佳化的實務訓練，以更好的方式來控制雲端成本。</span><span class="sxs-lookup"><span data-stu-id="77e80-150">All application owners are required to attend trained on practices for optimizing workloads to better control cloud costs.</span></span>

## <a name="evolution-of-the-best-practices"></a><span data-ttu-id="77e80-151">最佳做法的演進</span><span class="sxs-lookup"><span data-stu-id="77e80-151">Evolution of the best practices</span></span>

<span data-ttu-id="77e80-152">本文的這一節將推演治理 MVP 設計，以包含新的 Azure 原則和實作 Azure 成本管理。</span><span class="sxs-lookup"><span data-stu-id="77e80-152">This section of the article will evolve the governance MVP design to include new Azure policies and an implementation of Azure Cost Management.</span></span> <span data-ttu-id="77e80-153">這兩個設計變更將共同實現新的公司原則聲明。</span><span class="sxs-lookup"><span data-stu-id="77e80-153">Together, these two design changes will fulfill the new corporate policy statements.</span></span>

1. <span data-ttu-id="77e80-154">Azure 企業版入口網站中的變更，要向加拿大部署的部門管理員收取費用。</span><span class="sxs-lookup"><span data-stu-id="77e80-154">Changes in the Azure Enterprise Portal to bill the Department administrator for the Canadian deployment.</span></span>
2. <span data-ttu-id="77e80-155">實作 Azure 成本管理。</span><span class="sxs-lookup"><span data-stu-id="77e80-155">Implement Azure Cost Management.</span></span>
    1. <span data-ttu-id="77e80-156">建立正確層級的存取範圍，以便與訂用帳戶模式和資源群組模式保持一致。</span><span class="sxs-lookup"><span data-stu-id="77e80-156">Establish the right level of access scope to align with the subscription pattern and resource grouping pattern.</span></span> <span data-ttu-id="77e80-157">假設要與先前文章中定義的治理 MVP 保持一致，這樣需要針對在高階報告中執行的雲端治理小組存取**註冊帳戶範圍**。</span><span class="sxs-lookup"><span data-stu-id="77e80-157">Assuming alignment with the governance MVP defined in prior articles, this would require **Enrollment Account Scope** access for the Cloud Governance team executing on high level reporting.</span></span> <span data-ttu-id="77e80-158">治理以外的其他小組 (例如加拿大採購小組) 將需要存取**資源群組範圍**。</span><span class="sxs-lookup"><span data-stu-id="77e80-158">Additional teams outside of governance, like the Canadian procurement team, will require **Resource Group Scope** access.</span></span>
    2. <span data-ttu-id="77e80-159">在 Azure 成本管理中建立預算。</span><span class="sxs-lookup"><span data-stu-id="77e80-159">Establish a budget in Azure Cost Management.</span></span>
    3. <span data-ttu-id="77e80-160">檢閱初始建議並採取動作。</span><span class="sxs-lookup"><span data-stu-id="77e80-160">Review and act on initial recommendations.</span></span> <span data-ttu-id="77e80-161">建議使用週期性流程來支援報告流程。</span><span class="sxs-lookup"><span data-stu-id="77e80-161">It's recommended to have a recurring process to support the reporting process.</span></span>
    4. <span data-ttu-id="77e80-162">設定及執行初始和週期性 Azure 成本管理報告。</span><span class="sxs-lookup"><span data-stu-id="77e80-162">Configure and execute Azure Cost Management Reporting, both initial and recurring.</span></span>
3. <span data-ttu-id="77e80-163">更新 Azure 原則。</span><span class="sxs-lookup"><span data-stu-id="77e80-163">Update Azure Policy.</span></span>
    1. <span data-ttu-id="77e80-164">稽核標記、管理群組、訂用帳戶及資源群組值，以識別任何偏差。</span><span class="sxs-lookup"><span data-stu-id="77e80-164">Audit tagging, management group, subscription, and resource group values to identify any deviation.</span></span>
    2. <span data-ttu-id="77e80-165">建立 SKU 大小選項，以限制對於部署規劃文件中列出之 SKU 的部署。</span><span class="sxs-lookup"><span data-stu-id="77e80-165">Establish SKU size options to limit deployments to SKUs listed in deployment planning documentation.</span></span>

## <a name="conclusion"></a><span data-ttu-id="77e80-166">結論</span><span class="sxs-lookup"><span data-stu-id="77e80-166">Conclusion</span></span>

<span data-ttu-id="77e80-167">對治理 MVP 新增上述流程和變更，有助於降低與成本治理相關聯的許多風險。</span><span class="sxs-lookup"><span data-stu-id="77e80-167">Adding the above processes and changes to the governance MVP helps mitigate many of the risks associated with cost governance.</span></span> <span data-ttu-id="77e80-168">它們會建立控制成本所需的可見度、責任歸屬和最佳化。</span><span class="sxs-lookup"><span data-stu-id="77e80-168">Together, they create the visibility, accountability, and optimization needed to control costs.</span></span>

## <a name="next-steps"></a><span data-ttu-id="77e80-169">後續步驟</span><span class="sxs-lookup"><span data-stu-id="77e80-169">Next steps</span></span>

<span data-ttu-id="77e80-170">隨著雲端採用持續演進，並提供額外的商業價值，風險和雲端治理需求也需要與時俱進。</span><span class="sxs-lookup"><span data-stu-id="77e80-170">As cloud adoption continues to evolve and deliver additional business value, risks and cloud governance needs will also evolve.</span></span> <span data-ttu-id="77e80-171">針對這個旅程圖中的虛構公司，下一個步驟是使用此治理投資來管理多個雲端。</span><span class="sxs-lookup"><span data-stu-id="77e80-171">For the fictional company in this journey, the next step is using this governance investment to manage multiple clouds.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="77e80-172">多重雲端演進</span><span class="sxs-lookup"><span data-stu-id="77e80-172">Multi-cloud evolution</span></span>](./multi-cloud-evolution.md)
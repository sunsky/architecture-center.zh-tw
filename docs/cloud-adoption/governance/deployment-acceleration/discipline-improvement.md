---
title: CAF：部署加速專業領域改進
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 部署加速專業領域改進
author: alexbuckgit
ms.openlocfilehash: 98192c777d8866efb01544737e8cabea6354c4d7
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901180"
---
# <a name="deployment-acceleration-discipline-improvement"></a><span data-ttu-id="410a7-103">部署加速專業領域改進</span><span class="sxs-lookup"><span data-stu-id="410a7-103">Deployment Acceleration discipline improvement</span></span>

<span data-ttu-id="410a7-104">部署加速專業領域著重於建立原則，以確保資源會部署並以一致且可重複的方式進行設定，並且在其整個生命週期持續保有合規性。</span><span class="sxs-lookup"><span data-stu-id="410a7-104">The Deployment Acceleration discipline focuses on establishing policies that ensure that resources are deployed and configured consistently and repeatably, and remain in compliance throughout their lifecycle.</span></span> <span data-ttu-id="410a7-105">在這五個雲端治理專業領域中，部署加速包括與下列各項有關的決策：將部署自動化、控制原始檔的部署成品、監視已部署的資源以保有預期狀態，以及稽核任何合規性問題。</span><span class="sxs-lookup"><span data-stu-id="410a7-105">Within the Five Disciplines of Cloud Governance, Deployment Acceleration includes decisions regarding automating deployments, source-controlling deployment artifacts, monitoring deployed resources to maintain desired state, and auditing any compliance issues.</span></span>

<span data-ttu-id="410a7-106">本文將概述一些貴公司可參與的潛在工作，以更好的方式來開發部署加速專業領域並使其臻至成熟。</span><span class="sxs-lookup"><span data-stu-id="410a7-106">This article outlines some potential tasks your company can engage in to better develop and mature the Deployment Acceleration discipline.</span></span> <span data-ttu-id="410a7-107">這些工作可以細分為實作雲端解決方案的規劃、建置、採用及操作階段，接著反覆執行以允許開發[雲端治理的累加方法](../journeys/overview.md#an-incremental-approach-to-cloud-governance)。</span><span class="sxs-lookup"><span data-stu-id="410a7-107">These tasks can be broken down into planning, building, adopting, and operating phases of implementing a cloud solution, which are then iterated on allowing the development of an [incremental approach to cloud governance](../journeys/overview.md#an-incremental-approach-to-cloud-governance).</span></span>

![四個採用階段](../../_images/adoption-phases.png)

<span data-ttu-id="410a7-109">*圖 1.雲端治理累加方法的採用階段。*</span><span class="sxs-lookup"><span data-stu-id="410a7-109">*Figure 1. Adoption phases of the incremental approach to cloud governance.*</span></span>

<span data-ttu-id="410a7-110">沒有任何一份文件能夠滿足所有企業需求。</span><span class="sxs-lookup"><span data-stu-id="410a7-110">It's impossible for any one document to account for the requirements of all businesses.</span></span> <span data-ttu-id="410a7-111">因此，本文將針對治理成熟流程的每個階段，概述建議的最小和潛在範例活動。</span><span class="sxs-lookup"><span data-stu-id="410a7-111">As such, this article outlines suggested minimum and potential example activities for each phase of the governance maturation process.</span></span> <span data-ttu-id="410a7-112">這些活動的初始目的是協助您建置[原則 MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance)，並建立累加式原則演進的架構。</span><span class="sxs-lookup"><span data-stu-id="410a7-112">The initial objective of these activities is to help you build a [Policy MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance) and establish a framework for incremental policy evolution.</span></span> <span data-ttu-id="410a7-113">您的雲端治理小組將需決定要對這些活動進行多少投資，以改進您的身分識別基準治理功能。</span><span class="sxs-lookup"><span data-stu-id="410a7-113">Your Cloud Governance team will need to decide how much to invest in these activities to improve your Identity Baseline governance capabilities.</span></span>

> [!CAUTION]
> <span data-ttu-id="410a7-114">本文中概述的最小或潛在活動都不會與特定的公司原則或協力廠商合規性需求保持一致。</span><span class="sxs-lookup"><span data-stu-id="410a7-114">Neither the minimum or potential activities outlined in this article are aligned to specific corporate policies or third party compliance requirements.</span></span> <span data-ttu-id="410a7-115">本指引旨在協助促成交談，從而使這兩個需求與雲端治理模型保持一致。</span><span class="sxs-lookup"><span data-stu-id="410a7-115">This guidance is designed to help facilitate the conversations that will lead to alignment of both requirements with a cloud governance model.</span></span>

## <a name="planning-and-readiness"></a><span data-ttu-id="410a7-116">規劃和整備</span><span class="sxs-lookup"><span data-stu-id="410a7-116">Planning and readiness</span></span>

<span data-ttu-id="410a7-117">這個階段的治理成熟度可消彌業務成果與可操作之策略間的鴻溝。</span><span class="sxs-lookup"><span data-stu-id="410a7-117">This phase of governance maturity bridges the divide between business outcomes and actionable strategies.</span></span> <span data-ttu-id="410a7-118">在此流程中，領導小組會定義特定的計量、將這些計量對應至數位資產，並開始規劃整體移轉工作。</span><span class="sxs-lookup"><span data-stu-id="410a7-118">During this process, the leadership team defines specific metrics, maps those metrics to the digital estate, and begins planning the overall migration effort.</span></span>

<span data-ttu-id="410a7-119">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="410a7-119">**Minimum suggested activities:**</span></span>

- <span data-ttu-id="410a7-120">評估您的[部署加速工具鏈](toolchain.md)選項，並實作適用於貴組織的混合式策略。</span><span class="sxs-lookup"><span data-stu-id="410a7-120">Evaluate your [Deployment Acceleration toolchain](toolchain.md) options and implement a hybrid strategy that is appropriate to your organization.</span></span>
- <span data-ttu-id="410a7-121">開發架構方針文件的草稿，並散發給重要的專案關係人。</span><span class="sxs-lookup"><span data-stu-id="410a7-121">Develop a draft Architecture Guidelines document and distribute to key stakeholders.</span></span>
- <span data-ttu-id="410a7-122">教育並涵蓋受到開發架構方針影響的人員和小組。</span><span class="sxs-lookup"><span data-stu-id="410a7-122">Educate and involve the people and teams affected by the development of Architecture Guidelines.</span></span>
- <span data-ttu-id="410a7-123">訓練開發小組和 IT 人員，使其了解 DevSecOps 準則和策略，以及在部署加速專業領域中將部署完全自動化的重要性。</span><span class="sxs-lookup"><span data-stu-id="410a7-123">Train development teams and IT staff to understand DevSecOps principles and strategies and the importance of fully automated deployments in the Deployment Acceleration Discipline.</span></span>

<span data-ttu-id="410a7-124">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="410a7-124">**Potential activities:**</span></span>

- <span data-ttu-id="410a7-125">定義將在雲端中治理部署加速的角色和指派。</span><span class="sxs-lookup"><span data-stu-id="410a7-125">Define roles and assignments that will govern Deployment Acceleration in the cloud.</span></span>

## <a name="build-and-pre-deployment"></a><span data-ttu-id="410a7-126">建置和部署前</span><span class="sxs-lookup"><span data-stu-id="410a7-126">Build and pre-deployment</span></span>

<span data-ttu-id="410a7-127">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="410a7-127">**Minimum suggested activities:**</span></span>

- <span data-ttu-id="410a7-128">對於新的雲端式應用程式，在開發流程初期引進完全自動化的部署。</span><span class="sxs-lookup"><span data-stu-id="410a7-128">For new cloud-based applications, introduce fully automated deployments early in the development process.</span></span> <span data-ttu-id="410a7-129">此投資將改進測試流程的可靠性，並確保開發、QA 和生產環境之間的一致性。</span><span class="sxs-lookup"><span data-stu-id="410a7-129">This investment will improve the reliability of your testing processes and ensure consistency across your development, QA, and production environments.</span></span>
- <span data-ttu-id="410a7-130">使用原始檔控制平台 (例如 GitHub 或 Azure DevOps) 來儲存所有部署成品 (例如部署範本或設定指令碼)。</span><span class="sxs-lookup"><span data-stu-id="410a7-130">Store all deployment artifacts such as deployment templates or configuration scripts using a source-control platform such as GitHub or Azure DevOps.</span></span>
- <span data-ttu-id="410a7-131">實作您的[部署加速工具鏈](toolchain.md)之前，請考慮先執行試驗測試，以確定它會盡可能地簡化您的部署。</span><span class="sxs-lookup"><span data-stu-id="410a7-131">Consider a pilot test before implementing your [Deployment Acceleration toolchain](toolchain.md), making sure it streamlines your deployments as much as possible.</span></span> <span data-ttu-id="410a7-132">在部署前階段期間套用來自試驗測試的意見反應，必要時可重複執行。</span><span class="sxs-lookup"><span data-stu-id="410a7-132">Apply feedback from pilot tests during the pre-deployment phase, repeating as needed.</span></span>
- <span data-ttu-id="410a7-133">評估應用程式的邏輯與實體架構，並找機會來將應用程式資源部署自動化，或使用其他雲端式資源改進架構的某些部分。</span><span class="sxs-lookup"><span data-stu-id="410a7-133">Evaluate the logical and physical architecture of your applications, and identify opportunities to automate the deployment of application resources or improve portions of the architecture using other cloud-based resources.</span></span>
- <span data-ttu-id="410a7-134">更新架構方針文件，以包含部署與使用者採用方案，並散發給重要的專案關係人。</span><span class="sxs-lookup"><span data-stu-id="410a7-134">Update the Architecture Guidelines document to include deployment and user adoption plans, and distribute to key stakeholders.</span></span>
- <span data-ttu-id="410a7-135">繼續教育受架構方針影響最大的人員與小組。</span><span class="sxs-lookup"><span data-stu-id="410a7-135">Continue to educate the people and teams most affected by the architecture guidelines.</span></span>

<span data-ttu-id="410a7-136">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="410a7-136">**Potential activities:**</span></span>

- <span data-ttu-id="410a7-137">定義持續整合和持續部署 (CI/CD) 管線，透過開發、QA 及生產環境完全管理發行應用程式更新的程序。</span><span class="sxs-lookup"><span data-stu-id="410a7-137">Define a continuous integration and continuous deployment (CI/CD) pipeline to fully manage releasing updates to your application through your development, QA, and production environments.</span></span>

## <a name="adopt-and-migrate"></a><span data-ttu-id="410a7-138">採用和移轉</span><span class="sxs-lookup"><span data-stu-id="410a7-138">Adopt and migrate</span></span>

<span data-ttu-id="410a7-139">移轉是一個累加式流程，著重於在現有的數位資產中，移動、測試和採用應用程式或工作負載。</span><span class="sxs-lookup"><span data-stu-id="410a7-139">Migration is an incremental process that focuses on the movement, testing, and adoption of applications or workloads in an existing digital estate.</span></span>

<span data-ttu-id="410a7-140">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="410a7-140">**Minimum suggested activities:**</span></span>

- <span data-ttu-id="410a7-141">將您的[部署加速工具鏈](toolchain.md)從開發環境移轉至生產環境。</span><span class="sxs-lookup"><span data-stu-id="410a7-141">Migrate your [Deployment Acceleration toolchain](toolchain.md) from development to production.</span></span>
- <span data-ttu-id="410a7-142">更新架構方針文件，並散發給重要的專案關係人。</span><span class="sxs-lookup"><span data-stu-id="410a7-142">Update the Architecture Guidelines document and distribute to key stakeholders.</span></span>
- <span data-ttu-id="410a7-143">開發教育性資料和文件、認知溝通、獎勵和其他計畫，以協助試用產品的開發人員和 IT 採用。</span><span class="sxs-lookup"><span data-stu-id="410a7-143">Develop educational materials and documentation, awareness communications, incentives, and other programs to help drive developer and IT adoption.</span></span>

<span data-ttu-id="410a7-144">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="410a7-144">**Potential activities:**</span></span>

- <span data-ttu-id="410a7-145">驗證在建置和預先部署階段期間定義的最佳做法均會正確執行。</span><span class="sxs-lookup"><span data-stu-id="410a7-145">Validate that the best practices defined during the build and pre-deployment phases are properly executed.</span></span>
- <span data-ttu-id="410a7-146">確定每個應用程式或工作負載在發行之前，都會與部署加速策略保持一致。</span><span class="sxs-lookup"><span data-stu-id="410a7-146">Ensure that each application or workload aligns with the Deployment Acceleration strategy before release.</span></span>

## <a name="operate-and-post-implementation"></a><span data-ttu-id="410a7-147">操作和實作後</span><span class="sxs-lookup"><span data-stu-id="410a7-147">Operate and post-implementation</span></span>

<span data-ttu-id="410a7-148">轉換完成之後，治理和操作必須依存於應用程式或工作負載的自然生命週期。</span><span class="sxs-lookup"><span data-stu-id="410a7-148">Once the transformation is complete, governance and operations must live on for the natural lifecycle of an application or workload.</span></span> <span data-ttu-id="410a7-149">這個階段的治理成熟度著重於通常會在實作解決方案且轉換週期開始穩定後隨之而來的活動。</span><span class="sxs-lookup"><span data-stu-id="410a7-149">This phase of governance maturity focuses on the activities that commonly come after the solution is implemented and the transformation cycle begins to stabilize.</span></span>

<span data-ttu-id="410a7-150">**最小的建議活動：**</span><span class="sxs-lookup"><span data-stu-id="410a7-150">**Minimum suggested activities:**</span></span>

- <span data-ttu-id="410a7-151">根據對於貴組織變更身分識別需求的變更，自訂您的[部署加速工具鏈](toolchain.md)。</span><span class="sxs-lookup"><span data-stu-id="410a7-151">Customize your [Deployment Acceleration toolchain](toolchain.md) based on changes to your organization’s changing identity needs.</span></span>
- <span data-ttu-id="410a7-152">將通知和報告自動化，以警示您潛在的設定問題或惡意威脅。</span><span class="sxs-lookup"><span data-stu-id="410a7-152">Automate notifications and reports to alert you of potential configuration issues or malicious threats.</span></span>
- <span data-ttu-id="410a7-153">監視和報告應用程式與資源使用量。</span><span class="sxs-lookup"><span data-stu-id="410a7-153">Monitor and report on application and resource usage.</span></span>
- <span data-ttu-id="410a7-154">報告部署後計量，並散發給專案關係人。</span><span class="sxs-lookup"><span data-stu-id="410a7-154">Report on post-deployment metrics and distribute to stakeholders.</span></span>
- <span data-ttu-id="410a7-155">修訂架構方針，以引導未來的採用程序。</span><span class="sxs-lookup"><span data-stu-id="410a7-155">Revise the Architecture Guidelines to guide future adoption processes.</span></span>
- <span data-ttu-id="410a7-156">繼續溝通，並定期訓練受影響的人員和小組，以確保會持續遵循架構方針。</span><span class="sxs-lookup"><span data-stu-id="410a7-156">Continue to communicate with and train the affected people and teams on a regular basis to ensure ongoing adherence to Architecture Guidelines.</span></span>

<span data-ttu-id="410a7-157">**潛在的活動：**</span><span class="sxs-lookup"><span data-stu-id="410a7-157">**Potential activities:**</span></span>

- <span data-ttu-id="410a7-158">設定預期狀態設定監視和報告工具。</span><span class="sxs-lookup"><span data-stu-id="410a7-158">Configure a desired state configuration monitoring and reporting tool.</span></span>
- <span data-ttu-id="410a7-159">定期檢閱設定工具和指令碼，以改進流程並找出常見的問題。</span><span class="sxs-lookup"><span data-stu-id="410a7-159">Regularly review configuration tools and scripts to improve processes and identify common issues.</span></span>
- <span data-ttu-id="410a7-160">與開發、作業及安全性小組合作，以使 DevSecOps 做法臻至成熟，並細分導致低效率的組織定址接收器。</span><span class="sxs-lookup"><span data-stu-id="410a7-160">Work with development, operations, and security teams to help mature DevSecOps practices and break down organizational silos that lead to inefficiencies.</span></span>

## <a name="next-steps"></a><span data-ttu-id="410a7-161">後續步驟</span><span class="sxs-lookup"><span data-stu-id="410a7-161">Next steps</span></span>

<span data-ttu-id="410a7-162">既然您已了解雲端身分識別治理的概念，請檢查[身分識別基準工具鏈](toolchain.md)，來識別您在 Azure 平台上開發身分識別基準治理專業領域時所需的 Azure 工具和功能。</span><span class="sxs-lookup"><span data-stu-id="410a7-162">Now that you understand the concept of cloud identity governance, examine the [Identity Baseline toolchain](toolchain.md) to identify Azure tools and features that you'll need when developing the Identity Baseline governance discipline on the Azure platform.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="410a7-163">適用於 Azure 的身分識別基準工具鏈</span><span class="sxs-lookup"><span data-stu-id="410a7-163">Identity Baseline toolchain for Azure</span></span>](toolchain.md)

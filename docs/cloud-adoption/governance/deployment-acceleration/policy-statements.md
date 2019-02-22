---
title: CAF：部署加速範例原則聲明
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 部署加速範例原則聲明
author: alexbuckgit
ms.openlocfilehash: 4f7d59e6653c29db03f966d1c7105524b72586fc
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900996"
---
# <a name="deployment-acceleration-sample-policy-statements"></a><span data-ttu-id="3fde7-103">部署加速範例原則聲明</span><span class="sxs-lookup"><span data-stu-id="3fde7-103">Deployment Acceleration sample policy statements</span></span>

<span data-ttu-id="3fde7-104">個別的雲端原則聲明是用來解決在風險評估流程中識別出之特定風險的方針。</span><span class="sxs-lookup"><span data-stu-id="3fde7-104">Individual cloud policy statements are guidelines for addressing specific risks identified during your risk assessment process.</span></span> <span data-ttu-id="3fde7-105">這些聲明應該會提供風險的簡明摘要，以及如何處理它們的方案。</span><span class="sxs-lookup"><span data-stu-id="3fde7-105">These statements should provide a concise summary of risks and plans to deal with them.</span></span> <span data-ttu-id="3fde7-106">每個聲明定義都應該包含下列資訊：</span><span class="sxs-lookup"><span data-stu-id="3fde7-106">Each statement definition should include these pieces of information:</span></span>

- <span data-ttu-id="3fde7-107">**技術風險。**</span><span class="sxs-lookup"><span data-stu-id="3fde7-107">**Technical risk.**</span></span> <span data-ttu-id="3fde7-108">此原則將解決的風險摘要。</span><span class="sxs-lookup"><span data-stu-id="3fde7-108">A summary of the risk this policy will address.</span></span>
- <span data-ttu-id="3fde7-109">**原則聲明。**</span><span class="sxs-lookup"><span data-stu-id="3fde7-109">**Policy statement.**</span></span> <span data-ttu-id="3fde7-110">明確地摘要說明原則需求。</span><span class="sxs-lookup"><span data-stu-id="3fde7-110">A clear summary explanation of the policy requirements.</span></span>
- <span data-ttu-id="3fde7-111">**設計選項。**</span><span class="sxs-lookup"><span data-stu-id="3fde7-111">**Design options.**</span></span> <span data-ttu-id="3fde7-112">IT 小組與開發人員可以在實作原則時使用之可操作的建議、規格或其他指引。</span><span class="sxs-lookup"><span data-stu-id="3fde7-112">Actionable recommendations, specifications, or other guidance that IT teams and developers can use when implementing the policy.</span></span>

<span data-ttu-id="3fde7-113">下列範例原則聲明會解決一些與設定相關的常見業務風險，並提供來做為您撰寫原則聲明的草稿以滿足自己的組織需求時所參考的範例。</span><span class="sxs-lookup"><span data-stu-id="3fde7-113">The following sample policy statements address a number of common configuration-related business risks, and are provided as examples for you to reference when drafting policy statements to address your own organization's needs.</span></span> <span data-ttu-id="3fde7-114">請注意，這些範例並非禁止使用，而且可能提供數個原則選項來處理任何特定的風險。</span><span class="sxs-lookup"><span data-stu-id="3fde7-114">Note that these examples are not meant to be proscriptive, and there are potentially several policy options for dealing with any particular risk.</span></span> <span data-ttu-id="3fde7-115">與業務和 IT 小組密切合作，來識別適用於您獨特風險集合的最佳原則解決方案。</span><span class="sxs-lookup"><span data-stu-id="3fde7-115">Work closely with business and IT teams to identify the best policy solutions for your unique set of risks.</span></span>

## <a name="reliance-on-manual-deployment-or-configuration-of-systems"></a><span data-ttu-id="3fde7-116">依賴手動部署或設定系統</span><span class="sxs-lookup"><span data-stu-id="3fde7-116">Reliance on manual deployment or configuration of systems</span></span>

<span data-ttu-id="3fde7-117">**技術風險**：依賴部署或設定期間的人為介入，會提高人為錯誤的可能性，並降低系統部署和設定的重複性和可預測性。</span><span class="sxs-lookup"><span data-stu-id="3fde7-117">**Technical risk**: Relying on human intervention during deployment or configuration increases the likelihood of human error and reduces the repeatability and predictability of system deployments and configuration.</span></span> <span data-ttu-id="3fde7-118">它通常也會導致系統資源的部署變慢。</span><span class="sxs-lookup"><span data-stu-id="3fde7-118">It also typically leads to slower deployment of system resources.</span></span>

<span data-ttu-id="3fde7-119">**原則聲明**：部署到雲端的所有資產都應盡可能地使用範本或自動化指令碼來部署。</span><span class="sxs-lookup"><span data-stu-id="3fde7-119">**Policy statement**: All assets deployed to the cloud should be deployed using templates or automation scripts whenever possible.</span></span>

<span data-ttu-id="3fde7-120">**潛在的設計選項**：[Azure Resource Manager 範本](/azure/azure-resource-manager/resource-group-overview#template-deployment)提供基礎結構即程式碼的方法來將資源部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="3fde7-120">**Potential design options**: [Azure Resource Manager templates](/azure/azure-resource-manager/resource-group-overview#template-deployment) provides an infrastructure-as-code approach to deploying your resources to Azure.</span></span> <span data-ttu-id="3fde7-121">[Azure 建置組塊](https://github.com/mspnp/template-building-blocks/wiki) \(英文\) 提供命令列工具，以及一組設計來簡化 Azure 資源部署的 Resource Manager 範本。</span><span class="sxs-lookup"><span data-stu-id="3fde7-121">The [Azure Building Blocks](https://github.com/mspnp/template-building-blocks/wiki) provide a command-line tool and set of Resource Manager templates designed to simplify deployment of Azure resources.</span></span>

## <a name="lack-of-visibility-into-system-issues"></a><span data-ttu-id="3fde7-122">缺少系統問題的可見性</span><span class="sxs-lookup"><span data-stu-id="3fde7-122">Lack of visibility into system issues</span></span>

<span data-ttu-id="3fde7-123">**技術風險**：對於商務系統監視與診斷的不足，會妨礙操作人員在發生系統中斷之前找出並修復問題，而且可能大幅增加正確解析中斷情況所需的時間。</span><span class="sxs-lookup"><span data-stu-id="3fde7-123">**Technical risk**: Insufficient monitoring and diagnostics for business systems prevent operations personnel from identifying and remediating issues before a system outage occurs, and can significantly increase the time needed to properly resolve an outage.</span></span>

<span data-ttu-id="3fde7-124">**原則聲明**：您將實作下列原則：</span><span class="sxs-lookup"><span data-stu-id="3fde7-124">**Policy statement**: The following policies will be implemented:</span></span>

- <span data-ttu-id="3fde7-125">系統將針對所有生產系統和元件找出關鍵計量和診斷量值，而且會將監視與診斷工具套用至這些系統，並由操作人員定期監控。</span><span class="sxs-lookup"><span data-stu-id="3fde7-125">Key metrics and diagnostics measures will be identified for all production systems and components, and monitoring and diagnostic tools will be applied to these systems and monitored regularly by operations personnel.</span></span>
- <span data-ttu-id="3fde7-126">操作人員將考慮在預備和 QA 之類的非生產環境中使用監視與診斷工具，並於生產環境中發生系統問題之前先找到它們。</span><span class="sxs-lookup"><span data-stu-id="3fde7-126">Operations will consider using monitoring and diagnostic tools in non-production environments such as Staging and QA to identify system issues before they occur in the production environment.</span></span>

<span data-ttu-id="3fde7-127">**潛在的設計選項**：[Azure 監視器](/azure/azure-monitor/) (其中也包括 Log Analytics 和 Application Insights) 提供工具來收集和分析遙測，以協助您了解應用程式的執行方式，並主動找出影響它們的問題及其相依的資源。</span><span class="sxs-lookup"><span data-stu-id="3fde7-127">**Potential design options**: [Azure Monitor](/azure/azure-monitor/), which also includes Log Analytics and Application Insights, provides tools for collecting and analyzing telemetry to help you understand how your applications are performing and proactively identify issues affecting them and the resources they depend on.</span></span>

## <a name="configuration-security-reviews"></a><span data-ttu-id="3fde7-128">設定安全性檢閱</span><span class="sxs-lookup"><span data-stu-id="3fde7-128">Configuration security reviews</span></span>

<span data-ttu-id="3fde7-129">**技術風險**：經過一段時間之後，新的安全性威脅或考量可能會提高未經授權存取安全資源的風險。</span><span class="sxs-lookup"><span data-stu-id="3fde7-129">**Technical risk**: Over time, new security threats or concerns can increase the risks of unauthorized access to secure resources.</span></span>

<span data-ttu-id="3fde7-130">**原則聲明**：雲端治理流程必須包括每季與設定管理小組一起檢閱，以找出雲端資產設定應該避免的惡意執行者或使用模式。</span><span class="sxs-lookup"><span data-stu-id="3fde7-130">**Policy statement**: Cloud Governance processes must include quarterly review with configuration management teams to identify malicious actors or usage patterns that should be prevented by cloud asset configuration.</span></span>

<span data-ttu-id="3fde7-131">**潛在的設計選項**：召開每季安全性檢閱會議，其中包括治理小組成員，以及負責設定雲端應用程式和資源的 IT 人員。</span><span class="sxs-lookup"><span data-stu-id="3fde7-131">**Potential design options**: Establish a quarterly security review meeting that includes both governance team members and IT staff responsible for configuration cloud applications and resources.</span></span> <span data-ttu-id="3fde7-132">檢閱現有的安全性資料和計量，以便在目前的部署加速原則和工具中建立間距，並更新原則來降低任何新風險。</span><span class="sxs-lookup"><span data-stu-id="3fde7-132">Review existing security data and metrics to establish gaps in current Deployment Acceleration policy and tooling, and update policy to mitigate any new risks.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3fde7-133">後續步驟</span><span class="sxs-lookup"><span data-stu-id="3fde7-133">Next steps</span></span>

<span data-ttu-id="3fde7-134">使用本文提及的範例作為起點，以開發與您雲端採用方案保持一致的原則來解決特定的業務風險。</span><span class="sxs-lookup"><span data-stu-id="3fde7-134">Use the samples mentioned in this article as a starting point to develop policies that address specific business risks that align with your cloud adoption plans.</span></span>

<span data-ttu-id="3fde7-135">若要開始自行開發與身分識別管理相關的自訂原則聲明，請下載[身分識別基準範本](template.md)。</span><span class="sxs-lookup"><span data-stu-id="3fde7-135">To begin developing your own custom policy statements related to identity management, download the [Identity Baseline template](template.md).</span></span>

<span data-ttu-id="3fde7-136">若要加速採用這個專業領域，請選擇最密切配合您的環境且[可操作的治理旅程圖](../journeys/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="3fde7-136">To accelerate adoption of this discipline, choose the [Actionable Governance Journey](../journeys/overview.md) that most closely aligns with your environment.</span></span> <span data-ttu-id="3fde7-137">然後修改設計，以納入您特定的公司原則決策。</span><span class="sxs-lookup"><span data-stu-id="3fde7-137">Then modify the design to incorporate your specific corporate policy decisions.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="3fde7-138">可操作的治理旅程圖</span><span class="sxs-lookup"><span data-stu-id="3fde7-138">Actionable Governance Journeys</span></span>](../journeys/overview.md)
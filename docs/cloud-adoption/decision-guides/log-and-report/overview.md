---
title: CAF：記錄與報告決策指南
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 了解 Azure 移轉中的新服務：記錄、報告和監視。
author: rotycenh
ms.openlocfilehash: 36552488872622ec59e2fcf4816da4184c3d4fbf
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900943"
---
# <a name="logging-and-reporting-decision-guide"></a><span data-ttu-id="c9683-103">記錄與報告決策指南</span><span class="sxs-lookup"><span data-stu-id="c9683-103">Logging and reporting decision guide</span></span>

<span data-ttu-id="c9683-104">所有組織都需要通知機制，以在效能、運作時間與安全性產生疑慮時通知 IT 小組，避免疑慮變成嚴重的問題。</span><span class="sxs-lookup"><span data-stu-id="c9683-104">All organizations need mechanisms for notifying IT teams of performance, uptime, and security issues before they become serious problems.</span></span> <span data-ttu-id="c9683-105">成功的監視策略可讓您了解工作負載和網路基礎結構個別構成元件的執行狀況。</span><span class="sxs-lookup"><span data-stu-id="c9683-105">A successful monitoring strategy allows you to understand how the individual components that make up your workloads and networking infrastructure are performing.</span></span> <span data-ttu-id="c9683-106">在公用雲端移轉的情況下，將記錄和報告與任何現有的監視系統整合，同時向正確的 IT 人員呈現適當的重要事件和度量至關重要，因為這可以確保您的組織符合運作時間、安全性和原則合規性目標。</span><span class="sxs-lookup"><span data-stu-id="c9683-106">Within the context of a public cloud migration, integrating logging and reporting with any of your existing monitoring systems, while surfacing important events and metrics to the appropriate IT staff, is critical in ensuring your organization is meeting uptime, security, and policy compliance goals.</span></span>

![規劃符合下列快速連結的記錄、報告和監視選項 (從最簡單到最複雜)](../../_images/discovery-guides/discovery-guide-logs-and-reporting.png)

<span data-ttu-id="c9683-108">跳至：[規劃監視基礎結構](#planning-your-monitoring-infrastructure) | [雲端原生](#cloud-native) | [內部部署擴充](#on-premises-extension) | [閘道彙總](#gateway-aggregation)  | [混合式監視 (內部部署)](#hybrid-monitoring-on-premises) | [混合式監視 (雲端式)](#hybrid-monitoring-cloud-based) | [多重雲端](#multi-cloud) | [進一步瞭解](#learn-more)</span><span class="sxs-lookup"><span data-stu-id="c9683-108">Jump to: [Planning your monitoring infrastructure](#planning-your-monitoring-infrastructure) | [Cloud native](#cloud-native) | [On-premises extension](#on-premises-extension) | [Gateway aggregation](#gateway-aggregation) | [Hybrid monitoring (on-premises)](#hybrid-monitoring-on-premises) | [Hybrid monitoring (cloud-based)](#hybrid-monitoring-cloud-based) | [Multi-cloud](#multi-cloud) | [Learn more](#learn-more)</span></span>

<span data-ttu-id="c9683-109">判斷雲端身分識別策略時，關鍵點主要是依據您的組織在作業流程上所做的現有投資，以及對多重雲端策略在某種程度上的支援需求。</span><span class="sxs-lookup"><span data-stu-id="c9683-109">The inflection point when determining a cloud identity strategy is based primarily on existing investments your organization has made in operational processes, and to some degree any requirements you have to support a multi-cloud strategy.</span></span>

<span data-ttu-id="c9683-110">有多種方式可用於記錄和報告雲端活動。</span><span class="sxs-lookup"><span data-stu-id="c9683-110">There are multiple ways to log and report on activities in the cloud.</span></span> <span data-ttu-id="c9683-111">雲端原生和集中式記錄是運用訂用帳戶設計和訂用帳戶數目的兩個常見軟體即服務 (SaaS) 選項。</span><span class="sxs-lookup"><span data-stu-id="c9683-111">Cloud native and centralized logging are two common software as a service (SaaS) options that are driven by the subscription design and the number of subscriptions.</span></span>

## <a name="planning-your-monitoring-infrastructure"></a><span data-ttu-id="c9683-112">規劃監視基礎結構</span><span class="sxs-lookup"><span data-stu-id="c9683-112">Planning your monitoring infrastructure</span></span>

<span data-ttu-id="c9683-113">規劃部署時，需考量記錄資料儲存位置，以及如何將雲端式報告和監視服務與現有的處理程序和工具整合。</span><span class="sxs-lookup"><span data-stu-id="c9683-113">When planning your deployment, you need to consider where logging data is stored and how you will integrate cloud-based reporting and monitoring services with your existing processes and tools.</span></span>

| <span data-ttu-id="c9683-114">問題</span><span class="sxs-lookup"><span data-stu-id="c9683-114">Question</span></span> | <span data-ttu-id="c9683-115">雲端原生</span><span class="sxs-lookup"><span data-stu-id="c9683-115">Cloud native</span></span> | <span data-ttu-id="c9683-116">內部部署擴充功能</span><span class="sxs-lookup"><span data-stu-id="c9683-116">On-premises extension</span></span> | <span data-ttu-id="c9683-117">混合式監視</span><span class="sxs-lookup"><span data-stu-id="c9683-117">Hybrid monitoring</span></span> | <span data-ttu-id="c9683-118">閘道彙總</span><span class="sxs-lookup"><span data-stu-id="c9683-118">Gateway aggregation</span></span> |
|-----|-----|-----|-----|-----|
| <span data-ttu-id="c9683-119">您已經擁有內部部署監視基礎結構嗎？</span><span class="sxs-lookup"><span data-stu-id="c9683-119">Do you have an existing on-premises monitoring infrastructure?</span></span> | <span data-ttu-id="c9683-120">否</span><span class="sxs-lookup"><span data-stu-id="c9683-120">No</span></span> | <span data-ttu-id="c9683-121">yes</span><span class="sxs-lookup"><span data-stu-id="c9683-121">Yes</span></span> | <span data-ttu-id="c9683-122">是</span><span class="sxs-lookup"><span data-stu-id="c9683-122">Yes</span></span> |  <span data-ttu-id="c9683-123">否</span><span class="sxs-lookup"><span data-stu-id="c9683-123">No</span></span> |
| <span data-ttu-id="c9683-124">您有避免將記錄資料儲存在外部儲存位置的需求嗎？</span><span class="sxs-lookup"><span data-stu-id="c9683-124">Do you have requirements preventing storage of log data on external storage locations?</span></span> | <span data-ttu-id="c9683-125">否</span><span class="sxs-lookup"><span data-stu-id="c9683-125">No</span></span> | <span data-ttu-id="c9683-126">yes</span><span class="sxs-lookup"><span data-stu-id="c9683-126">Yes</span></span> | <span data-ttu-id="c9683-127">否</span><span class="sxs-lookup"><span data-stu-id="c9683-127">No</span></span> | <span data-ttu-id="c9683-128">否</span><span class="sxs-lookup"><span data-stu-id="c9683-128">No</span></span> |
| <span data-ttu-id="c9683-129">您需要整合雲端監視與內部部署系統嗎？</span><span class="sxs-lookup"><span data-stu-id="c9683-129">Do you need to integrate cloud monitoring with on-premises systems?</span></span> | <span data-ttu-id="c9683-130">否</span><span class="sxs-lookup"><span data-stu-id="c9683-130">No</span></span> | <span data-ttu-id="c9683-131">否</span><span class="sxs-lookup"><span data-stu-id="c9683-131">No</span></span> | <span data-ttu-id="c9683-132">yes</span><span class="sxs-lookup"><span data-stu-id="c9683-132">Yes</span></span> | <span data-ttu-id="c9683-133">否</span><span class="sxs-lookup"><span data-stu-id="c9683-133">No</span></span> |
<span data-ttu-id="c9683-134">您需要先處理或篩選遙測資料再將它們提交至監視系統嗎？</span><span class="sxs-lookup"><span data-stu-id="c9683-134">Do you need to process or filter telemetry data before submitting it to your monitoring systems?</span></span> | <span data-ttu-id="c9683-135">否</span><span class="sxs-lookup"><span data-stu-id="c9683-135">No</span></span> | <span data-ttu-id="c9683-136">否</span><span class="sxs-lookup"><span data-stu-id="c9683-136">No</span></span> | <span data-ttu-id="c9683-137">否</span><span class="sxs-lookup"><span data-stu-id="c9683-137">No</span></span> | <span data-ttu-id="c9683-138">yes</span><span class="sxs-lookup"><span data-stu-id="c9683-138">Yes</span></span> |

### <a name="cloud-native"></a><span data-ttu-id="c9683-139">雲端原生</span><span class="sxs-lookup"><span data-stu-id="c9683-139">Cloud native</span></span>

<span data-ttu-id="c9683-140">如果您的組織目前尚未建置記錄和報告系統，或您規劃的雲端部署不需要與現有內部部署或其他外部監視系統整合，雲端原生 SaaS 解決方案就是您最簡單的選擇。</span><span class="sxs-lookup"><span data-stu-id="c9683-140">If your organization currently lacks established logging and reporting systems, or if your planned cloud deployment does not need to be integrated with existing on-premises or other external monitoring systems, a cloud native SaaS solution is the simplest choice.</span></span>

<span data-ttu-id="c9683-141">在此案例中，記錄資料的記錄和儲存作業都是在與您工作負載相同的雲端環境中執行，處理資訊並向 IT 人員呈現資訊的記錄和報告工具則是由雲端平台提供。</span><span class="sxs-lookup"><span data-stu-id="c9683-141">In this scenario, log data is recorded and stored in the same cloud environment as your workload, while the logging and reporting tools that process and surface information to IT staff are offered as part of the cloud platform.</span></span>

<span data-ttu-id="c9683-142">雲端原生記錄解決方案可以依據較小型或實驗性部署的訂用帳戶或工作負載進行實作，並以集中方式安排組織，以監控整個雲環境的記錄資料。</span><span class="sxs-lookup"><span data-stu-id="c9683-142">Cloud native logging solutions can be implemented ad hoc per subscription or workload for smaller or experimental deployments and are organized in a centralized manner to monitor log data across your entire cloud estate.</span></span>

<span data-ttu-id="c9683-143">**雲端原生假設事項**。</span><span class="sxs-lookup"><span data-stu-id="c9683-143">**Cloud native assumptions**.</span></span> <span data-ttu-id="c9683-144">使用雲端原生記錄和報告系統時會假設以下事項：</span><span class="sxs-lookup"><span data-stu-id="c9683-144">Using a cloud native logging and reporting system assumes the following:</span></span>

- <span data-ttu-id="c9683-145">您不需要將記錄資料從雲端工作負載整合到現有的內部部署系統中。</span><span class="sxs-lookup"><span data-stu-id="c9683-145">You do not need to integrate the log data from you cloud workloads into existing on-premises systems.</span></span>
- <span data-ttu-id="c9683-146">您將不會使用雲端式報告系統監視內部部署系統。</span><span class="sxs-lookup"><span data-stu-id="c9683-146">You will not be using your cloud-based reporting systems to monitor on-premises systems.</span></span>

### <a name="on-premises-extension"></a><span data-ttu-id="c9683-147">內部部署擴充功能</span><span class="sxs-lookup"><span data-stu-id="c9683-147">On-premises extension</span></span>

<span data-ttu-id="c9683-148">在需要將雲端遙測資料與內部部署系統整合的情況下，無論是內部部署系統不支援混合式記錄和報告，或支援在進行最少開發作業情況下移轉應用程式和服務的，都必須在 VM 中部署會將記錄資料直接傳送至您的內部部署系統，而不是將資料儲存在雲端環境中的代理程式。</span><span class="sxs-lookup"><span data-stu-id="c9683-148">In scenarios where you need to integrate cloud telemetry with on-premises systems that do not support hybrid logging and reporting, or support the migration of applications and services with a minimum amount of redevelopment, you will need to deploy monitoring agents to VMs that will send log data directly to your on-premises systems, rather than storing it in the cloud environment.</span></span>

<span data-ttu-id="c9683-149">為了支援這種方法，您的雲端資源必須能夠透過結合[混合式網路功能](../software-defined-network/hybrid.md)和[雲端裝載網域服務](../identity/overview.md#cloud-hosted-domain-services)，直接與您的內部部署系統通訊。</span><span class="sxs-lookup"><span data-stu-id="c9683-149">In order to support this approach, your cloud resources will need to be able to communicate directly with your on-premises systems through a combination of [hybrid networking](../software-defined-network/hybrid.md) and [cloud hosted domain services](../identity/overview.md#cloud-hosted-domain-services).</span></span> <span data-ttu-id="c9683-150">然後雲端虛擬網路才能以內部部署環境網路擴充功能的形式運作。</span><span class="sxs-lookup"><span data-stu-id="c9683-150">With this in place, the cloud virtual network functions as a network extension of the on-premises environment.</span></span> <span data-ttu-id="c9683-151">如此一來，雲端裝載工作負載才能直接與您的內部部署記錄和報告系統通訊。</span><span class="sxs-lookup"><span data-stu-id="c9683-151">Therefore, cloud hosted workloads can communicate directly with your on-premises logging and reporting system.</span></span>

<span data-ttu-id="c9683-152">這個方法會運用您在監控工具上的現有投資，對任何雲端部署的應用程序或服務進行有限的修改。</span><span class="sxs-lookup"><span data-stu-id="c9683-152">This approach capitalizes on your existing investment in monitoring tooling with limited modification to any cloud-deployed applications or services.</span></span> <span data-ttu-id="c9683-153">這通常進行移植移轉時支援監視的最快方法。</span><span class="sxs-lookup"><span data-stu-id="c9683-153">This is often the fastest approach to support monitoring during a lift-and-shift migration.</span></span> <span data-ttu-id="c9683-154">但是這不會擷取雲端 PaaS 和 SaaS 資源製造的記錄資料，而且將會省略雲端平台產生的任何 VM 相關記錄，例如 VM 狀態。</span><span class="sxs-lookup"><span data-stu-id="c9683-154">However, it won’t capture log data produced by cloud-based PaaS and SaaS resources, and it will omit any VM-related logs generated by the cloud platform itself such as VM status.</span></span> <span data-ttu-id="c9683-155">因此應將此模式視為實作更完善混合式監視解決方案之前的暫時性解決方案。</span><span class="sxs-lookup"><span data-stu-id="c9683-155">As a result, this pattern should be a temporary solution until a more comprehensive hybrid monitoring solution is implemented.</span></span>

<span data-ttu-id="c9683-156">僅限內部部署的假設事項：</span><span class="sxs-lookup"><span data-stu-id="c9683-156">On-premises only assumptions:</span></span>

- <span data-ttu-id="c9683-157">您只需要在內部部署環境中維護記錄資料 (基於支援技術需求或法規或原則需求)。</span><span class="sxs-lookup"><span data-stu-id="c9683-157">You need to maintain log data only in your on-premises environment only, either in support of technical requirements or due to regulatory or policy requirements.</span></span>
- <span data-ttu-id="c9683-158">您的內務部署系統不支援混合式記錄和報告或閘道彙總解決方案。</span><span class="sxs-lookup"><span data-stu-id="c9683-158">Your on-premises systems do not support hybrid logging and reporting or gateway aggregation solutions.</span></span>
- <span data-ttu-id="c9683-159">您的雲端式應用程式可以直接向內部記錄系統提交遙測資料，或向內部部署系統提交遙測資料的監視代理程式可以部署至工作負載 VM。</span><span class="sxs-lookup"><span data-stu-id="c9683-159">Your cloud-based applications can submit telemetry directly to your on-premises logging systems or monitoring agents that submit to on-premises can be deployed to workload VMs.</span></span>
- <span data-ttu-id="c9683-160">您的工作負載無須依賴需要雲端式記錄和報告的 PaaS 或 SaaS 服務。</span><span class="sxs-lookup"><span data-stu-id="c9683-160">Your workloads are not dependent on PaaS or SaaS services that require cloud-based logging and reporting.</span></span>

### <a name="gateway-aggregation"></a><span data-ttu-id="c9683-161">閘道彙總</span><span class="sxs-lookup"><span data-stu-id="c9683-161">Gateway aggregation</span></span>

<span data-ttu-id="c9683-162">對於雲端遙測資料量很大，或現有內部部署監視系統需要先修改記錄資料才能加以處理的案例，可能就需要記錄資料[閘道彙總](../../../patterns/gateway-aggregation.md)服務。</span><span class="sxs-lookup"><span data-stu-id="c9683-162">For scenarios where the amount of cloud-based telemetry data is very large or existing on-premises monitoring systems need log data modified before it can be processed, a log data [gateway aggregation](../../../patterns/gateway-aggregation.md) service may be required.</span></span>

<span data-ttu-id="c9683-163">閘道服務會部署至您的雲端提供者。</span><span class="sxs-lookup"><span data-stu-id="c9683-163">A gateway service is deployed to your cloud provider.</span></span> <span data-ttu-id="c9683-164">然後將相關應用程式和服務設定為提交遙測資料給閘道，而不是提交給預設的記錄系統。</span><span class="sxs-lookup"><span data-stu-id="c9683-164">Then, relevant applications and services are configured to submit telemetry data to the gateway instead of a default logging system.</span></span> <span data-ttu-id="c9683-165">接著閘道可以處理資料 (彙總、結合或設定資料格式)，然後再將資料提交給您的監視服務以進行擷取及分析。</span><span class="sxs-lookup"><span data-stu-id="c9683-165">The gateway can then process the data: aggregating, combining, or otherwise formatting it before then submitting it to your monitoring service for ingestion and analysis.</span></span>

<span data-ttu-id="c9683-166">此外，閘道也可以用來彙總及預先處理要提交給雲端或混合式系統的遙測資料。</span><span class="sxs-lookup"><span data-stu-id="c9683-166">Also, a gateway can be used to aggregate and preprocess telemetry data bound for cloud-native or hybrid systems.</span></span>

<span data-ttu-id="c9683-167">閘道彙總假設事項：</span><span class="sxs-lookup"><span data-stu-id="c9683-167">Gateway aggregation assumptions:</span></span>

- <span data-ttu-id="c9683-168">您預期您的雲端式應用程式或服務將會產生大量遙測資料。</span><span class="sxs-lookup"><span data-stu-id="c9683-168">You expect very high levels of telemetry data from your cloud-based applications or services.</span></span>
- <span data-ttu-id="c9683-169">您需要先設定遙測資料格式或將資料最佳化，才能將它們提交給監視系統。</span><span class="sxs-lookup"><span data-stu-id="c9683-169">You need to format or otherwise optimize telemetry data before submitting it to your monitoring systems.</span></span>
- <span data-ttu-id="c9683-170">您的監視系統具備可用來擷取閘道處理後的記錄資料的 API 或其他機制。</span><span class="sxs-lookup"><span data-stu-id="c9683-170">Your monitoring systems have APIs or other mechanisms available to ingest log data after processing by the gateway.</span></span>

### <a name="hybrid-monitoring-on-premises"></a><span data-ttu-id="c9683-171">混合式監視 (內部部署)</span><span class="sxs-lookup"><span data-stu-id="c9683-171">Hybrid monitoring (on-premises)</span></span>

<span data-ttu-id="c9683-172">混合式監視解決方案可結合來自內部部署和雲端資源的記錄資料，以提供 IT 資產運作狀態的整合式檢視。</span><span class="sxs-lookup"><span data-stu-id="c9683-172">A hybrid monitoring solution combines log data from both your on-premises and cloud resources to provide an integrated view into your IT estate's operational status.</span></span>

<span data-ttu-id="c9683-173">如果您已經投資並擁有取代難度高或取代代價高昂的內部部署監視系統，就可能需要將來自雲端工作負載的遙測資料整合到現有的內部部署監視解決方案。</span><span class="sxs-lookup"><span data-stu-id="c9683-173">If you have an existing investment in on-premises monitoring systems that would be difficult or costly to replace, you may need to integrate the telemetry from your cloud workloads into preexisting on-premises monitoring solutions.</span></span> <span data-ttu-id="c9683-174">在混合式內部部署監視系統中，內部部署遙測資料會繼續使用現有的內部部署監視系統。</span><span class="sxs-lookup"><span data-stu-id="c9683-174">In a hybrid on-premises monitoring system, on-premises telemetry data continues to use the existing on-premises monitoring system.</span></span> <span data-ttu-id="c9683-175">雲端遙測資料則會直接傳送至雲端監視系統，或者與您的工作負載一起儲存在雲端，然後定期經過編譯並擷取到內部部署系統中。</span><span class="sxs-lookup"><span data-stu-id="c9683-175">Cloud-based telemetry data is either sent to the cloud monitoring system directly, or the data is stored on the cloud alongside your workloads and then compiled and ingested into the on-premises system at regular intervals.</span></span>

<span data-ttu-id="c9683-176">**內部部署混合式監視假設事項**。</span><span class="sxs-lookup"><span data-stu-id="c9683-176">**On-premises hybrid monitoring assumptions**.</span></span> <span data-ttu-id="c9683-177">使用內部部署記錄和報告系統以進行混合式監視時會假設以下事項：</span><span class="sxs-lookup"><span data-stu-id="c9683-177">Using an on-premises logging and reporting system for hybrid monitoring assumes the following:</span></span>

- <span data-ttu-id="c9683-178">您需要使用現有的內部部署報告系統監視雲端工作負載。</span><span class="sxs-lookup"><span data-stu-id="c9683-178">You need to use existing on-premises reporting systems to monitor cloud workloads.</span></span>
- <span data-ttu-id="c9683-179">您需要維護記錄資料內部部署的擁有權。</span><span class="sxs-lookup"><span data-stu-id="c9683-179">You need to maintain ownership of log data on-premises.</span></span>
- <span data-ttu-id="c9683-180">您的內部部署管理系統具備可用來從雲端系統擷取記錄資料的 API 或其他機制。</span><span class="sxs-lookup"><span data-stu-id="c9683-180">Your on-premises management systems have APIs or other mechanisms available to ingest log data from cloud-based systems.</span></span>

> [!TIP]
> <span data-ttu-id="c9683-181">由於雲端移轉具有反覆性，因此可能會從不同的雲端原生和內部部署監視轉換成部分混合式方法。</span><span class="sxs-lookup"><span data-stu-id="c9683-181">As part of the iterative nature of cloud migration, transitioning from distinct cloud-native and on-premises monitoring to a partial hybrid approach is likely.</span></span> <span data-ttu-id="c9683-182">請務必保留變更到您的監視結構與整體 IT 和作業程序。請務必根據整體 IT 和作業流程對監視基礎結構持續進行更改。</span><span class="sxs-lookup"><span data-stu-id="c9683-182">Make sure to keep changes to your monitoring architecture in line with your overall IT and operational processes.</span></span>

### <a name="hybrid-monitoring-cloud-based"></a><span data-ttu-id="c9683-183">混合式監視 (雲端式)</span><span class="sxs-lookup"><span data-stu-id="c9683-183">Hybrid monitoring (cloud-based)</span></span>

<span data-ttu-id="c9683-184">如果您沒有需維護內部部署監視系統的迫切需求，或想要以 SaaS 解決方案取代內部部署監視系統，您也可以選擇整合內部部署記錄資料與集中式雲端監視系統。</span><span class="sxs-lookup"><span data-stu-id="c9683-184">If you do not have a compelling need to maintain an on-premises monitoring system, or you want to replace on-premises monitoring systems with a SaaS solution, you can also choose to integrate on-premises log data with a centralized cloud-based monitoring system.</span></span>

<span data-ttu-id="c9683-185">鏡像內部部署集中式方式 (在此案例中，雲端工作負載會使用其預設的雲端記錄機制)，與內部部署應用程式和服務會將遙測資料目錄傳送至雲端式記錄系統，或定期彙總該資料以擷取至雲端系統中。</span><span class="sxs-lookup"><span data-stu-id="c9683-185">Mirroring the on-premises centered approach, in this scenario cloud workloads would use their default cloud logging mechanism, and on-premises applications and services would either send telemetry directory to the cloud-based logging system, or aggregate that data for ingestion into the cloud system at regular intervals.</span></span> <span data-ttu-id="c9683-186">雲端式監視系統就能作為您整個 IT 資產的主要監視與報告系統。</span><span class="sxs-lookup"><span data-stu-id="c9683-186">The cloud-based monitoring system would then serve as your primary monitoring and reporting system for your entire IT estate.</span></span>

<span data-ttu-id="c9683-187">雲端混合式監視假設事項：使用雲端式記錄和報告系統以進行混合式監視時會假設以下事項：</span><span class="sxs-lookup"><span data-stu-id="c9683-187">Cloud-based hybrid monitoring assumptions: Using cloud-based logging and reporting systems for hybrid monitoring assumes the following:</span></span>

- <span data-ttu-id="c9683-188">您不依賴現有的內部部署監視系統。</span><span class="sxs-lookup"><span data-stu-id="c9683-188">You are not dependent upon existing on-premises monitoring systems.</span></span>
- <span data-ttu-id="c9683-189">您的工作負載沒有需將記錄資料儲存在內部部署系統的法規或原則需求。</span><span class="sxs-lookup"><span data-stu-id="c9683-189">Your workloads do not have regulatory or policy requirements to store log data on-premises.</span></span>
- <span data-ttu-id="c9683-190">您的雲端式管理系統具備可用來從內部部署應用程式和服務擷取記錄資料的 API 或其他機制。</span><span class="sxs-lookup"><span data-stu-id="c9683-190">Your cloud-based monitoring systems have APIs or other mechanisms available to ingest log data from on-premises applications and services.</span></span>

### <a name="multi-cloud"></a><span data-ttu-id="c9683-191">多重雲端</span><span class="sxs-lookup"><span data-stu-id="c9683-191">Multi-cloud</span></span>

<span data-ttu-id="c9683-192">跨多個雲端平台整合記錄和報告功能可能會是非常複雜的工作。</span><span class="sxs-lookup"><span data-stu-id="c9683-192">Integrating logging and reporting capabilities across a multiple-cloud platform can be complicated.</span></span> <span data-ttu-id="c9683-193">平台之間提供的服務通常無法直接比較，而且這些服務提供的記錄與遙測功能也不盡相同。</span><span class="sxs-lookup"><span data-stu-id="c9683-193">Services offered between platforms are often not directly comparable, and logging and telemetry capabilities provided by these services differ as well.</span></span>
<span data-ttu-id="c9683-194">多重雲端記錄支援通常需要先使用閘道服務將記錄資料處理成通用格式，然後再將資料提交給混合式記錄解決方案。</span><span class="sxs-lookup"><span data-stu-id="c9683-194">Multi-cloud logging support often requires the use of gateway services to process log data into a common format before submitting data to a hybrid logging solution.</span></span>

## <a name="learn-more"></a><span data-ttu-id="c9683-195">深入了解</span><span class="sxs-lookup"><span data-stu-id="c9683-195">Learn more</span></span>

<span data-ttu-id="c9683-196">[Azure 監視器](/azure/azure-monitor/overview)是 Azure 預設的報告和監視服務。</span><span class="sxs-lookup"><span data-stu-id="c9683-196">[Azure Monitor](/azure/azure-monitor/overview) is the default reporting and monitoring service for Azure.</span></span> <span data-ttu-id="c9683-197">它提供：</span><span class="sxs-lookup"><span data-stu-id="c9683-197">It provides:</span></span>

- <span data-ttu-id="c9683-198">一個統一平台，用來收集應用程式遙測資料、主機遙測資料 (例如 VM)、容器計量、Azure 平台計量，以及事件記錄檔。</span><span class="sxs-lookup"><span data-stu-id="c9683-198">A unified platform for collecting app telemetry, host telemetry (such as VMs), container metrics, Azure platform metrics, and event logs.</span></span>
- <span data-ttu-id="c9683-199">視覺效果、查詢、警示和分析工具。</span><span class="sxs-lookup"><span data-stu-id="c9683-199">Visualization, queries, alerts, and analytical tools.</span></span> <span data-ttu-id="c9683-200">它可提供虛擬機器、客體作業系統、虛擬網路，以及工作負載應用程式事件的深入解析。</span><span class="sxs-lookup"><span data-stu-id="c9683-200">It can provide insights into virtual machines, guest operating systems, virtual networks, and workload application events.</span></span>
- <span data-ttu-id="c9683-201">[REST API](/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough)，用來與外部服務整合，以及將監視與警示服務自動化</span><span class="sxs-lookup"><span data-stu-id="c9683-201">[REST APIs](/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough) for integration with external services and automation of monitoring and alerting services</span></span>
- <span data-ttu-id="c9683-202">與許多熱門的第三方廠商[整合](/azure/monitoring-and-diagnostics/monitoring-partners)。</span><span class="sxs-lookup"><span data-stu-id="c9683-202">[Integration](/azure/monitoring-and-diagnostics/monitoring-partners) with many popular third-party vendors.</span></span>
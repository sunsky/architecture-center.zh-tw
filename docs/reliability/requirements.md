---
title: 定義復原 Azure 應用程式的需求
description: 收集 Azure 應用程式的可用性和恢復功能需求的概觀
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: 2af2ce4200c285abfda1395862d21efc3c17e577
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59646511"
---
# <a name="developing-requirements-for-resilient-azure-applications"></a><span data-ttu-id="46b4c-103">開發復原 Azure 應用程式的需求</span><span class="sxs-lookup"><span data-stu-id="46b4c-103">Developing requirements for resilient Azure applications</span></span>

<span data-ttu-id="46b4c-104">建置*恢復*（從失敗復原） 及*可用性*（執行中而不需顯著停機的狀況良好狀態） 到您的應用程式開始收集需求。</span><span class="sxs-lookup"><span data-stu-id="46b4c-104">Building *resiliency* (recovering from failures) and *availability* (running in a healthy state without significant downtime) into your apps begins with gathering requirements.</span></span> <span data-ttu-id="46b4c-105">比方說，多少停機時間是可接受？</span><span class="sxs-lookup"><span data-stu-id="46b4c-105">For example, how much downtime is acceptable?</span></span> <span data-ttu-id="46b4c-106">可能的停機時間費用業務是多少？</span><span class="sxs-lookup"><span data-stu-id="46b4c-106">How much does potential downtime cost your business?</span></span> <span data-ttu-id="46b4c-107">客戶的可用性需求為何？</span><span class="sxs-lookup"><span data-stu-id="46b4c-107">What are your customer's availability requirements?</span></span> <span data-ttu-id="46b4c-108">您投資，讓您的應用程式高可用性多少？</span><span class="sxs-lookup"><span data-stu-id="46b4c-108">How much do you invest in making your application highly available?</span></span> <span data-ttu-id="46b4c-109">什麼是成本與風險？</span><span class="sxs-lookup"><span data-stu-id="46b4c-109">What is the risk versus the cost?</span></span>

## <a name="identify-distinct-workloads"></a><span data-ttu-id="46b4c-110">識別不同的工作負載</span><span class="sxs-lookup"><span data-stu-id="46b4c-110">Identify distinct workloads</span></span>

<span data-ttu-id="46b4c-111">雲端解決方案通常包含多個應用程式*工作負載*。</span><span class="sxs-lookup"><span data-stu-id="46b4c-111">Cloud solutions typically consist of multiple application *workloads*.</span></span> <span data-ttu-id="46b4c-112">工作負載是不同的功能或以邏輯方式分開根據商務邏輯和資料儲存體需求的其他工作的工作。</span><span class="sxs-lookup"><span data-stu-id="46b4c-112">A workload is a distinct capability or task that is logically separated from other tasks in terms of business logic and data storage requirements.</span></span> <span data-ttu-id="46b4c-113">例如，電子商務應用程式可能會有下列的工作負載：</span><span class="sxs-lookup"><span data-stu-id="46b4c-113">For example, an e-commerce app might have the following workloads:</span></span>

- <span data-ttu-id="46b4c-114">瀏覽及搜尋產品類別目錄。</span><span class="sxs-lookup"><span data-stu-id="46b4c-114">Browse and search a product catalog.</span></span>
- <span data-ttu-id="46b4c-115">建立並追蹤訂單。</span><span class="sxs-lookup"><span data-stu-id="46b4c-115">Create and track orders.</span></span>
- <span data-ttu-id="46b4c-116">檢視建議。</span><span class="sxs-lookup"><span data-stu-id="46b4c-116">View recommendations.</span></span>

<span data-ttu-id="46b4c-117">每個工作負載都有不同的可用性、 延展性、 資料一致性和災害復原的需求。</span><span class="sxs-lookup"><span data-stu-id="46b4c-117">Each workload has different requirements for availability, scalability, data consistency, and disaster recovery.</span></span> <span data-ttu-id="46b4c-118">每個工作負載的風險與成本之間的平衡，讓您的商務決策。</span><span class="sxs-lookup"><span data-stu-id="46b4c-118">Make your business decisions by balancing cost versus risk for each workload.</span></span>

<span data-ttu-id="46b4c-119">服務等級目標，也分解工作負載。</span><span class="sxs-lookup"><span data-stu-id="46b4c-119">Also decompose workloads by service-level objective.</span></span> <span data-ttu-id="46b4c-120">如果服務包含的重要和比較不重要的工作負載，就會以不同的方式加以管理，並指定以符合其可用性需求所需的執行個體數目的服務功能。</span><span class="sxs-lookup"><span data-stu-id="46b4c-120">If a service is composed of critical and less-critical workloads, manage them differently and specify the service features and number of instances needed to meet their availability requirements.</span></span>

## <a name="plan-for-usage-patterns"></a><span data-ttu-id="46b4c-121">使用模式的計劃</span><span class="sxs-lookup"><span data-stu-id="46b4c-121">Plan for usage patterns</span></span>

<span data-ttu-id="46b4c-122">在重大和非重大的期間，找出需求的差異。</span><span class="sxs-lookup"><span data-stu-id="46b4c-122">Identify differences in requirements during critical and non-critical periods.</span></span> <span data-ttu-id="46b4c-123">必須使用系統時，是否有某些重要的期間？</span><span class="sxs-lookup"><span data-stu-id="46b4c-123">Are there certain critical periods when the system must be available?</span></span> <span data-ttu-id="46b4c-124">例如，報稅應用程式不能在歸檔期限期間失敗，以及影片串流服務不應該延遲期間的即時事件。</span><span class="sxs-lookup"><span data-stu-id="46b4c-124">For example, a tax-filing application can't fail during a filing deadline and a video streaming service shouldn't lag during a live event.</span></span> <span data-ttu-id="46b4c-125">在這些情況下，權衡成本與風險。</span><span class="sxs-lookup"><span data-stu-id="46b4c-125">In these situations, weigh the cost against the risk.</span></span>

- <span data-ttu-id="46b4c-126">若要確保穩定性，並符合服務等級協定 (Sla) 中重要的期間，規劃備援跨數個區域一個失敗時，即使其成本較高。</span><span class="sxs-lookup"><span data-stu-id="46b4c-126">To ensure uptime and meet service-level agreements (SLAs) in critical periods, plan redundancy across several regions in case one fails, even if it costs more.</span></span>
- <span data-ttu-id="46b4c-127">相反地，如果非關鍵期間，您可以執行應用程式將成本降至最低的單一區域中。</span><span class="sxs-lookup"><span data-stu-id="46b4c-127">Conversely, during non-critical periods, run your application in a single region to minimize costs.</span></span>
- <span data-ttu-id="46b4c-128">在某些情況下，您可以使用新式無伺服器技術，具有以使用量為基礎的計費，以避免額外的費用。</span><span class="sxs-lookup"><span data-stu-id="46b4c-128">In some cases, you can mitigate additional expenses by using modern serverless techniques that have consumption-based billing.</span></span>

## <a name="establish-availability-and-recovery-metrics"></a><span data-ttu-id="46b4c-129">建立可用性和復原的計量</span><span class="sxs-lookup"><span data-stu-id="46b4c-129">Establish availability and recovery metrics</span></span>

<span data-ttu-id="46b4c-130">建立兩個集合的需求程序的一部分的度量的基準線數目。</span><span class="sxs-lookup"><span data-stu-id="46b4c-130">Create baseline numbers for two sets of metrics as part of the requirements process.</span></span> <span data-ttu-id="46b4c-131">第一組可協助您決定加入雲端服務中的冗餘的 where 和哪些要為客戶提供 Sla。</span><span class="sxs-lookup"><span data-stu-id="46b4c-131">The first set helps you determine where to add redundancy to cloud services and which SLAs to provide to customers.</span></span> <span data-ttu-id="46b4c-132">第二個設定可協助您規劃災害復原。</span><span class="sxs-lookup"><span data-stu-id="46b4c-132">The second set helps you plan your disaster recovery.</span></span>

### <a name="availability-metrics"></a><span data-ttu-id="46b4c-133">可用性度量</span><span class="sxs-lookup"><span data-stu-id="46b4c-133">Availability metrics</span></span>

<span data-ttu-id="46b4c-134">使用這些量值來規劃備援並判斷客戶的 Sla。</span><span class="sxs-lookup"><span data-stu-id="46b4c-134">Use these measures to plan for redundancy and determine customer SLAs.</span></span>

- <span data-ttu-id="46b4c-135">**若要復原 (MTTR) 的平均時間**是平均所需的時間在失敗後還原元件。</span><span class="sxs-lookup"><span data-stu-id="46b4c-135">**Mean time to recover (MTTR)** is the average time it takes to restore a component after a failure.</span></span>
- <span data-ttu-id="46b4c-136">**失敗 (MTBF) 之間的平均時間**是長時間元件可以合理預期能夠關閉間的最後一個。</span><span class="sxs-lookup"><span data-stu-id="46b4c-136">**Mean time between failures (MTBF)** is the how long a component can reasonably expect to last between outages.</span></span>

### <a name="recovery-metrics"></a><span data-ttu-id="46b4c-137">復原計量</span><span class="sxs-lookup"><span data-stu-id="46b4c-137">Recovery metrics</span></span>

<span data-ttu-id="46b4c-138">藉由執行風險評定，來衍生這些值，並確定您了解成本和停機時間和資料遺失的風險。</span><span class="sxs-lookup"><span data-stu-id="46b4c-138">Derive these values by conducting a risk assessment, and make sure you understand the cost and risk of downtime and data loss.</span></span> <span data-ttu-id="46b4c-139">這些是系統的非功能性需求，而且應該依照商務需求。</span><span class="sxs-lookup"><span data-stu-id="46b4c-139">These are nonfunctional requirements of a system and should be dictated by business requirements.</span></span>

- <span data-ttu-id="46b4c-140">**復原時間目標 (RTO)** 是事件之後無法使用應用程式的最大可接受時間。</span><span class="sxs-lookup"><span data-stu-id="46b4c-140">**Recovery time objective (RTO)** is the maximum acceptable time an application is unavailable after an incident.</span></span>
- <span data-ttu-id="46b4c-141">**復原點目標 (RPO)** 是在災害期間可接受資料遺失的最大時間長度。</span><span class="sxs-lookup"><span data-stu-id="46b4c-141">**Recovery point objective (RPO)** is the maximum duration of data loss that's acceptable during a disaster.</span></span>

<span data-ttu-id="46b4c-142">如果 MTTR 值*任何*高度可用的安裝程式中的重要元件超出系統 RTO，系統中的失敗可能會導致無法接受的業務中斷。</span><span class="sxs-lookup"><span data-stu-id="46b4c-142">If the MTTR value of *any* critical component in a highly available setup exceeds the system RTO, a failure in the system might cause an unacceptable business disruption.</span></span> <span data-ttu-id="46b4c-143">也就是說，您無法還原的系統定義的 RTO 內。</span><span class="sxs-lookup"><span data-stu-id="46b4c-143">That is, you can't restore the system within the defined RTO.</span></span>

## <a name="determine-workload-availability-targets"></a><span data-ttu-id="46b4c-144">判斷工作負載的可用性目標</span><span class="sxs-lookup"><span data-stu-id="46b4c-144">Determine workload availability targets</span></span>

<span data-ttu-id="46b4c-145">因此您可以判斷架構是否符合商務需求，請在 您的解決方案中定義您自己的每個工作負載的目標 Sla。</span><span class="sxs-lookup"><span data-stu-id="46b4c-145">Define your own target SLAs for each workload in your solution so you can determine whether the architecture meets the business requirements.</span></span>

### <a name="consider-cost-and-complexity"></a><span data-ttu-id="46b4c-146">請考慮成本和複雜度</span><span class="sxs-lookup"><span data-stu-id="46b4c-146">Consider cost and complexity</span></span>

<span data-ttu-id="46b4c-147">其他的一切，更高的可用性最好。</span><span class="sxs-lookup"><span data-stu-id="46b4c-147">Everything else being equal, higher availability is better.</span></span> <span data-ttu-id="46b4c-148">但您尋求更多的九，成本和複雜度增加。</span><span class="sxs-lookup"><span data-stu-id="46b4c-148">But as you strive for more nines, the cost and complexity grow.</span></span> <span data-ttu-id="46b4c-149">99.99%的執行時間會轉譯為大約五分鐘內的每個月的總停機時間。</span><span class="sxs-lookup"><span data-stu-id="46b4c-149">An uptime of 99.99% translates to about five minutes of total downtime per month.</span></span> <span data-ttu-id="46b4c-150">是否值得額外的複雜性和成本來達到五個九？</span><span class="sxs-lookup"><span data-stu-id="46b4c-150">Is it worth the additional complexity and cost to reach five nines?</span></span> <span data-ttu-id="46b4c-151">答案需視商務需求而定。</span><span class="sxs-lookup"><span data-stu-id="46b4c-151">The answer depends on the business requirements.</span></span>

<span data-ttu-id="46b4c-152">以下是定義 SLA 時的一些其他考量：</span><span class="sxs-lookup"><span data-stu-id="46b4c-152">Here are some other considerations when defining an SLA:</span></span>

- <span data-ttu-id="46b4c-153">若要達到四個九 （99.99%)，您不能依賴手動介入來從失敗中復原。</span><span class="sxs-lookup"><span data-stu-id="46b4c-153">To achieve four nines (99.99%), you can't rely on manual intervention to recover from failures.</span></span> <span data-ttu-id="46b4c-154">應用程式必須是自我診斷及自我修復。</span><span class="sxs-lookup"><span data-stu-id="46b4c-154">The application must be self-diagnosing and self-healing.</span></span>
- <span data-ttu-id="46b4c-155">超過四個九，其難以速度不夠快偵測到中斷狀況，以符合 SLA。</span><span class="sxs-lookup"><span data-stu-id="46b4c-155">Beyond four nines, it's challenging to detect outages quickly enough to meet the SLA.</span></span>
- <span data-ttu-id="46b4c-156">請思考您 SLA 測量根據的時間間隔。</span><span class="sxs-lookup"><span data-stu-id="46b4c-156">Think about the time window that your SLA is measured against.</span></span> <span data-ttu-id="46b4c-157">間隔越小，容錯度就更緊密。</span><span class="sxs-lookup"><span data-stu-id="46b4c-157">The smaller the window, the tighter the tolerances.</span></span> <span data-ttu-id="46b4c-158">它沒有任何意義，來定義以每小時或每日執行時間 SLA。</span><span class="sxs-lookup"><span data-stu-id="46b4c-158">It doesn't make sense to define your SLA in terms of hourly or daily uptime.</span></span>
- <span data-ttu-id="46b4c-159">考慮 MTBF 和 MTTR 量值。</span><span class="sxs-lookup"><span data-stu-id="46b4c-159">Consider the MTBF and MTTR measurements.</span></span> <span data-ttu-id="46b4c-160">愈高您的 SLA，越不常服務可以移往下，並必須在復原服務更快。</span><span class="sxs-lookup"><span data-stu-id="46b4c-160">The higher your SLA, the less frequently the service can go down and the quicker the service must recover.</span></span>
- <span data-ttu-id="46b4c-161">取得合約從您的客戶每一份您的應用程式的可用性目標並加以記錄。</span><span class="sxs-lookup"><span data-stu-id="46b4c-161">Get agreement from your customers for the availability targets of each piece of your application, and document it.</span></span> <span data-ttu-id="46b4c-162">否則，您的設計可能不符合客戶的期望。</span><span class="sxs-lookup"><span data-stu-id="46b4c-162">Otherwise, your design may not meet the customers' expectations.</span></span>

### <a name="identify-dependencies"></a><span data-ttu-id="46b4c-163">識別相依性</span><span class="sxs-lookup"><span data-stu-id="46b4c-163">Identify dependencies</span></span>

<span data-ttu-id="46b4c-164">執行相依性對應來識別內部和外部相依性的練習。</span><span class="sxs-lookup"><span data-stu-id="46b4c-164">Perform dependency-mapping exercises to identify internal and external dependencies.</span></span> <span data-ttu-id="46b4c-165">範例包括安全性或身分識別，例如 Active Directory 中或第三方服務，例如付款提供者相關的相依性，或電子郵件訊息的服務。</span><span class="sxs-lookup"><span data-stu-id="46b4c-165">Examples include dependencies relating to security or identity, such as Active Directory, or third-party services such as a payment provider or e-mail messaging service.</span></span>

<span data-ttu-id="46b4c-166">請特別注意外部相依性，可能會是單一失敗點，或是造成瓶頸。</span><span class="sxs-lookup"><span data-stu-id="46b4c-166">Pay particular attention to external dependencies that might be a single point of failure or cause bottlenecks.</span></span> <span data-ttu-id="46b4c-167">如果工作負載需要 99.99%執行時間，但具有 99.9 %sla 的服務而定，該服務無法在系統中是單一失敗點。</span><span class="sxs-lookup"><span data-stu-id="46b4c-167">If a workload requires 99.99% uptime but depends on a service with a 99.9% SLA, that service can't be a single point of failure in the system.</span></span> <span data-ttu-id="46b4c-168">一個補救是指萬一服務失敗，有一個後援的路徑。</span><span class="sxs-lookup"><span data-stu-id="46b4c-168">One remedy is to have a fallback path in case the service fails.</span></span> <span data-ttu-id="46b4c-169">或者，採取其他措施從該服務中的失敗中復原。</span><span class="sxs-lookup"><span data-stu-id="46b4c-169">Alternatively, take other measures to recover from a failure in that service.</span></span>

<span data-ttu-id="46b4c-170">下表顯示各種 SLA 層級的潛在累計停機時間。</span><span class="sxs-lookup"><span data-stu-id="46b4c-170">The following table shows the potential cumulative downtime for various SLA levels.</span></span>

| <span data-ttu-id="46b4c-171">**SLA**</span><span class="sxs-lookup"><span data-stu-id="46b4c-171">**SLA**</span></span> | <span data-ttu-id="46b4c-172">**每週停機時間**</span><span class="sxs-lookup"><span data-stu-id="46b4c-172">**Downtime per week**</span></span> | <span data-ttu-id="46b4c-173">**每月停機時間**</span><span class="sxs-lookup"><span data-stu-id="46b4c-173">**Downtime per month**</span></span> | <span data-ttu-id="46b4c-174">**每年停機時間**</span><span class="sxs-lookup"><span data-stu-id="46b4c-174">**Downtime per year**</span></span> |
|---------|-----------------------|------------------------|-----------------------|
| <span data-ttu-id="46b4c-175">99%</span><span class="sxs-lookup"><span data-stu-id="46b4c-175">99%</span></span>     | <span data-ttu-id="46b4c-176">1.68 小時</span><span class="sxs-lookup"><span data-stu-id="46b4c-176">1.68 hours</span></span>            | <span data-ttu-id="46b4c-177">7.2 小時</span><span class="sxs-lookup"><span data-stu-id="46b4c-177">7.2 hours</span></span>              | <span data-ttu-id="46b4c-178">3.65 天</span><span class="sxs-lookup"><span data-stu-id="46b4c-178">3.65 days</span></span>             |
| <span data-ttu-id="46b4c-179">99.9%</span><span class="sxs-lookup"><span data-stu-id="46b4c-179">99.9%</span></span>   | <span data-ttu-id="46b4c-180">10.1 分鐘</span><span class="sxs-lookup"><span data-stu-id="46b4c-180">10.1 minutes</span></span>          | <span data-ttu-id="46b4c-181">43.2 分鐘</span><span class="sxs-lookup"><span data-stu-id="46b4c-181">43.2 minutes</span></span>           | <span data-ttu-id="46b4c-182">8.76 小時</span><span class="sxs-lookup"><span data-stu-id="46b4c-182">8.76 hours</span></span>            |
| <span data-ttu-id="46b4c-183">99.95%</span><span class="sxs-lookup"><span data-stu-id="46b4c-183">99.95%</span></span>  | <span data-ttu-id="46b4c-184">5 分鐘</span><span class="sxs-lookup"><span data-stu-id="46b4c-184">5 minutes</span></span>             | <span data-ttu-id="46b4c-185">21.6 分鐘</span><span class="sxs-lookup"><span data-stu-id="46b4c-185">21.6 minutes</span></span>           | <span data-ttu-id="46b4c-186">4.38 小時</span><span class="sxs-lookup"><span data-stu-id="46b4c-186">4.38 hours</span></span>            |
| <span data-ttu-id="46b4c-187">99.99%</span><span class="sxs-lookup"><span data-stu-id="46b4c-187">99.99%</span></span>  | <span data-ttu-id="46b4c-188">1.01 分鐘</span><span class="sxs-lookup"><span data-stu-id="46b4c-188">1.01 minutes</span></span>          | <span data-ttu-id="46b4c-189">4.32 分鐘</span><span class="sxs-lookup"><span data-stu-id="46b4c-189">4.32 minutes</span></span>           | <span data-ttu-id="46b4c-190">52.56 分鐘</span><span class="sxs-lookup"><span data-stu-id="46b4c-190">52.56 minutes</span></span>         |
| <span data-ttu-id="46b4c-191">99.999%</span><span class="sxs-lookup"><span data-stu-id="46b4c-191">99.999%</span></span> | <span data-ttu-id="46b4c-192">6 秒</span><span class="sxs-lookup"><span data-stu-id="46b4c-192">6 seconds</span></span>             | <span data-ttu-id="46b4c-193">25.9 秒</span><span class="sxs-lookup"><span data-stu-id="46b4c-193">25.9 seconds</span></span>           | <span data-ttu-id="46b4c-194">5.26 分鐘</span><span class="sxs-lookup"><span data-stu-id="46b4c-194">5.26 minutes</span></span>          |

## <a name="understand-service-level-agreements"></a><span data-ttu-id="46b4c-195">了解服務等級協定</span><span class="sxs-lookup"><span data-stu-id="46b4c-195">Understand service-level agreements</span></span>

<span data-ttu-id="46b4c-196">在 Azure 中，[服務等級協定](https://azure.microsoft.com/en-us/support/legal/sla/)說明 Microsoft 的承諾，對執行時間與連線能力。</span><span class="sxs-lookup"><span data-stu-id="46b4c-196">In Azure, the [Service Level Agreement](https://azure.microsoft.com/en-us/support/legal/sla/) describes Microsoft's commitments for uptime and connectivity.</span></span> <span data-ttu-id="46b4c-197">如果特定服務的 SLA 是 99.9%，您應該預期 99.9%的時間是可用的服務。</span><span class="sxs-lookup"><span data-stu-id="46b4c-197">If the SLA for a particular service is 99.9%, you should expect the service to be available 99.9% of the time.</span></span> <span data-ttu-id="46b4c-198">不同的服務有不同的 Sla。</span><span class="sxs-lookup"><span data-stu-id="46b4c-198">Different services have different SLAs.</span></span>

<span data-ttu-id="46b4c-199">Azure SLA 也包括如果不符合 SLA，以及特定的定義，取得服務退費的佈建*可用性*針對每個服務。</span><span class="sxs-lookup"><span data-stu-id="46b4c-199">The Azure SLA also includes provisions for obtaining a service credit if the SLA is not met, along with specific definitions of *availability* for each service.</span></span> <span data-ttu-id="46b4c-200">SLA 的這個部分會作為強制執行原則。</span><span class="sxs-lookup"><span data-stu-id="46b4c-200">That aspect of the SLA acts as an enforcement policy.</span></span>

### <a name="composite-slas"></a><span data-ttu-id="46b4c-201">複合 SLA</span><span class="sxs-lookup"><span data-stu-id="46b4c-201">Composite SLAs</span></span>

<span data-ttu-id="46b4c-202">*複合 Sla*牽涉到多個服務支援的應用程式，各有不同層級的可用性。</span><span class="sxs-lookup"><span data-stu-id="46b4c-202">*Composite SLAs* involve multiple services supporting an application, each with differing levels of availability.</span></span> <span data-ttu-id="46b4c-203">例如，請考慮將寫入至 Azure SQL Database 的 App Service web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="46b4c-203">For example, consider an App Service web app that writes to Azure SQL Database.</span></span> <span data-ttu-id="46b4c-204">在撰寫本文時，這些 Azure 服務都有下列 SLA：</span><span class="sxs-lookup"><span data-stu-id="46b4c-204">At the time of this writing, these Azure services have the following SLAs:</span></span>

- <span data-ttu-id="46b4c-205">App Service web 應用程式 = 99.95%</span><span class="sxs-lookup"><span data-stu-id="46b4c-205">App Service web apps = 99.95%</span></span>
- <span data-ttu-id="46b4c-206">SQL Database = 99.99%</span><span class="sxs-lookup"><span data-stu-id="46b4c-206">SQL Database = 99.99%</span></span>

<span data-ttu-id="46b4c-207">您預期這個應用程式的停機時間最長是多久？</span><span class="sxs-lookup"><span data-stu-id="46b4c-207">What is the maximum downtime you would expect for this application?</span></span> <span data-ttu-id="46b4c-208">如果其中一項服務失敗，整個應用程式就會失敗。</span><span class="sxs-lookup"><span data-stu-id="46b4c-208">If either service fails, the whole application fails.</span></span> <span data-ttu-id="46b4c-209">每個服務失敗的機率是各自獨立，因此這個應用程式的複合 SLA 是 99.95%× 99.99%= 99.94%。</span><span class="sxs-lookup"><span data-stu-id="46b4c-209">The probability of each service failing is independent, so the composite SLA for this application is 99.95% × 99.99% = 99.94%.</span></span> <span data-ttu-id="46b4c-210">這低於個別的 Sla，這不令人驚訝，因為仰賴多個服務的應用程式有更多潛在的失敗點。</span><span class="sxs-lookup"><span data-stu-id="46b4c-210">That's lower than the individual SLAs, which isn't surprising because an application that relies on multiple services has more potential failure points.</span></span>

<span data-ttu-id="46b4c-211">您可以建立獨立的後援路徑來改善複合 SLA。</span><span class="sxs-lookup"><span data-stu-id="46b4c-211">You can improve the composite SLA by creating independent fallback paths.</span></span> <span data-ttu-id="46b4c-212">例如，如果 SQL 資料庫無法使用，將交易放入佇列以供稍後處理。</span><span class="sxs-lookup"><span data-stu-id="46b4c-212">For example, if SQL Database is unavailable, put transactions into a queue to be processed later.</span></span>

![複合 SLA](_images/composite-sla.png)

<span data-ttu-id="46b4c-214">透過這個設計，即使在無法連線到資料庫的情況下，應用程式還是可以使用。</span><span class="sxs-lookup"><span data-stu-id="46b4c-214">With this design, the application is still available even if it can't connect to the database.</span></span> <span data-ttu-id="46b4c-215">不過，如果資料庫與佇列同時失敗，它就會失敗。</span><span class="sxs-lookup"><span data-stu-id="46b4c-215">However, it fails if the database and the queue both fail at the same time.</span></span> <span data-ttu-id="46b4c-216">預期的同時失敗百分比是時間的 0.0001 × 0.001，，因此這個合併路徑的複合 SLA 是時間的：</span><span class="sxs-lookup"><span data-stu-id="46b4c-216">The expected percentage of time for a simultaneous failure is 0.0001 × 0.001, so the composite SLA for this combined path is:</span></span>

- <span data-ttu-id="46b4c-217">資料庫*或*佇列 = 1.0 − (0.0001 × 0.001) = 99.99999%</span><span class="sxs-lookup"><span data-stu-id="46b4c-217">Database *or* queue = 1.0 − (0.0001 × 0.001) = 99.99999%</span></span>

<span data-ttu-id="46b4c-218">複合 SLA 總計是：</span><span class="sxs-lookup"><span data-stu-id="46b4c-218">The total composite SLA is:</span></span>

- <span data-ttu-id="46b4c-219">Web 應用程式*並*(資料庫*或是*佇列) = 99.95%× 99.99999%= \~99.95%</span><span class="sxs-lookup"><span data-stu-id="46b4c-219">Web app *and* (database *or* queue) = 99.95% × 99.99999% = \~99.95%</span></span>

<span data-ttu-id="46b4c-220">都會有這種方法的優缺點。</span><span class="sxs-lookup"><span data-stu-id="46b4c-220">There are tradeoffs to this approach.</span></span> <span data-ttu-id="46b4c-221">應用程式邏輯是更複雜，您需支付佇列，，您必須考量資料一致性問題。</span><span class="sxs-lookup"><span data-stu-id="46b4c-221">The application logic is more complex, you are paying for the queue, and you need to consider data consistency issues.</span></span>

### <a name="slas-for-multiregion-deployments"></a><span data-ttu-id="46b4c-222">多區域部署的 Sla</span><span class="sxs-lookup"><span data-stu-id="46b4c-222">SLAs for multiregion deployments</span></span>

<span data-ttu-id="46b4c-223">*多區域部署的 Sla*涉及部署多個區域中的應用程式，並使用 Azure 流量管理員容錯移轉，如果應用程式無法在一個區域中的高可用性技術。</span><span class="sxs-lookup"><span data-stu-id="46b4c-223">*SLAs for multiregion deployments* involve a high-availability technique to deploy the application in more than one region and use Azure Traffic Manager to fail over if the application fails in one region.</span></span>

<span data-ttu-id="46b4c-224">多區域部署的複合 SLA 的計算方式如下：</span><span class="sxs-lookup"><span data-stu-id="46b4c-224">The composite SLA for a multiregion deployment is calculated as follows:</span></span>

- <span data-ttu-id="46b4c-225">*N*是一個區域中部署的應用程式的複合 SLA。</span><span class="sxs-lookup"><span data-stu-id="46b4c-225">*N* is the composite SLA for the application deployed in one region.</span></span>
- <span data-ttu-id="46b4c-226">*R*是應用程式部署所在的區域數目。</span><span class="sxs-lookup"><span data-stu-id="46b4c-226">*R* is the number of regions where the application is deployed.</span></span>

<span data-ttu-id="46b4c-227">應用程式無法同時在所有區域中的預期的機率為 ((1 − N) \^ R)。</span><span class="sxs-lookup"><span data-stu-id="46b4c-227">The expected chance that the application fails in all regions at the same time is ((1 − N) \^ R).</span></span> <span data-ttu-id="46b4c-228">比方說，如果單一區域的 SLA 是 99.95%:</span><span class="sxs-lookup"><span data-stu-id="46b4c-228">For example, if the single-region SLA is 99.95%:</span></span>

- <span data-ttu-id="46b4c-229">兩個區域的合併的 SLA = (1 − （1 − 0.9995） \^ 2) = 99.999975%</span><span class="sxs-lookup"><span data-stu-id="46b4c-229">The combined SLA for two regions = (1 − (1 − 0.9995) \^ 2) = 99.999975%</span></span>
- <span data-ttu-id="46b4c-230">四個區域的合併的 SLA = (1 − （1 − 0.9995） \^ 4) = 99.999999%</span><span class="sxs-lookup"><span data-stu-id="46b4c-230">The combined SLA for four regions =  (1 − (1 − 0.9995) \^ 4)  = 99.999999%</span></span>

<span data-ttu-id="46b4c-231">[Traffic Manager SLA](https://azure.microsoft.com/en-us/support/legal/sla/traffic-manager/v1_0/)也是一項因素。</span><span class="sxs-lookup"><span data-stu-id="46b4c-231">The [SLA for Traffic Manager](https://azure.microsoft.com/en-us/support/legal/sla/traffic-manager/v1_0/) is also a factor.</span></span> <span data-ttu-id="46b4c-232">容錯移轉並非瞬間完成，在主動-被動組態中，這可能會導致容錯移轉期間的停機時間。</span><span class="sxs-lookup"><span data-stu-id="46b4c-232">Failing over is not instantaneous in active-passive configurations, which can result in downtime during a failover.</span></span> <span data-ttu-id="46b4c-233">請參閱[流量管理員端點監視和容錯移轉](/azure/traffic-manager/traffic-manager-monitoring)。</span><span class="sxs-lookup"><span data-stu-id="46b4c-233">See [Traffic Manager endpoint monitoring and failover](/azure/traffic-manager/traffic-manager-monitoring).</span></span>

## <a name="next-steps"></a><span data-ttu-id="46b4c-234">後續步驟</span><span class="sxs-lookup"><span data-stu-id="46b4c-234">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="46b4c-235">架構設計人員的恢復功能和可用性</span><span class="sxs-lookup"><span data-stu-id="46b4c-235">Architect for resiliency and availability</span></span>](./architect.md)

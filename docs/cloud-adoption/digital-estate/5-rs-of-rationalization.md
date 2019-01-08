---
title: 合理化的 5R 策略
titleSuffix: Enterprise Cloud Adoption
description: 說明合理化數位資產時可用的選項
author: BrianBlanchard
ms.date: 12/10/2018
ms.openlocfilehash: 66eee782b63210bcba3022e3be5b8fdee11716ec
ms.sourcegitcommit: bb7fcffbb41e2c26a26f8781df32825eb60df70c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2018
ms.locfileid: "53643674"
---
# <a name="enterprise-cloud-adoption-the-5-rs-of-rationalization"></a><span data-ttu-id="a1e7b-103">企業雲端採用：合理化的 5R 策略</span><span class="sxs-lookup"><span data-stu-id="a1e7b-103">Enterprise Cloud Adoption: The 5 Rs of rationalization</span></span>

<span data-ttu-id="a1e7b-104">雲端合理化是評估資產以判斷何種方法最適合用來移轉或現代化雲端中各個資產的程序。</span><span class="sxs-lookup"><span data-stu-id="a1e7b-104">Cloud Rationalization is the process of evaluating assets to determine the best approach to migrating or modernizing each asset in the cloud.</span></span> <span data-ttu-id="a1e7b-105">確定關於合理化程序的詳細資訊，請參閱[什麼是數位資產？](overview.md)</span><span class="sxs-lookup"><span data-stu-id="a1e7b-105">For more information about the process of rationalization, see [What is a digital estate?](overview.md)</span></span>

<span data-ttu-id="a1e7b-106">此處所列的「合理化的 5R 策略」說明最常用的合理化選項。</span><span class="sxs-lookup"><span data-stu-id="a1e7b-106">The "5 Rs of rationalization" listed here describe the most common options for rationalization.</span></span>

## <a name="rehost"></a><span data-ttu-id="a1e7b-107">重新裝載</span><span class="sxs-lookup"><span data-stu-id="a1e7b-107">Rehost</span></span>

<span data-ttu-id="a1e7b-108">重新裝載工作也稱為「隨即移轉」，可在盡可能不變更整體架構的情況下，將目前的資產移至選擇的雲端提供者。</span><span class="sxs-lookup"><span data-stu-id="a1e7b-108">Also known as "lift and shift," a rehost effort moves the current state asset to the chosen cloud provider, with minimal change to overall architecture.</span></span>

<span data-ttu-id="a1e7b-109">常見的動因可能包括：</span><span class="sxs-lookup"><span data-stu-id="a1e7b-109">Common drivers could include:</span></span>

* <span data-ttu-id="a1e7b-110">降低 CapEx</span><span class="sxs-lookup"><span data-stu-id="a1e7b-110">Reduce CapEx</span></span>
* <span data-ttu-id="a1e7b-111">釋放資料中心空間</span><span class="sxs-lookup"><span data-stu-id="a1e7b-111">Free up datacenter space</span></span>
* <span data-ttu-id="a1e7b-112">快速實現雲端 ROI</span><span class="sxs-lookup"><span data-stu-id="a1e7b-112">Quick cloud ROI</span></span>

<span data-ttu-id="a1e7b-113">量化分析因素：</span><span class="sxs-lookup"><span data-stu-id="a1e7b-113">Quantitative analysis factors:</span></span>

* <span data-ttu-id="a1e7b-114">VM 大小 (CPU、記憶體、儲存體)</span><span class="sxs-lookup"><span data-stu-id="a1e7b-114">VM size (CPU, memory, storage)</span></span>
* <span data-ttu-id="a1e7b-115">相依性 (篩選網路)</span><span class="sxs-lookup"><span data-stu-id="a1e7b-115">Dependencies (network traffic)</span></span>
* <span data-ttu-id="a1e7b-116">資產相容性</span><span class="sxs-lookup"><span data-stu-id="a1e7b-116">Asset compatibility</span></span>

<span data-ttu-id="a1e7b-117">定性分析因素：</span><span class="sxs-lookup"><span data-stu-id="a1e7b-117">Qualitative analysis factors:</span></span>

* <span data-ttu-id="a1e7b-118">對變更的容忍度</span><span class="sxs-lookup"><span data-stu-id="a1e7b-118">Tolerance for change</span></span>
* <span data-ttu-id="a1e7b-119">商業優先順序</span><span class="sxs-lookup"><span data-stu-id="a1e7b-119">Business priorities</span></span>
* <span data-ttu-id="a1e7b-120">重大商業事件</span><span class="sxs-lookup"><span data-stu-id="a1e7b-120">Critical business events</span></span>
* <span data-ttu-id="a1e7b-121">程序相依性</span><span class="sxs-lookup"><span data-stu-id="a1e7b-121">Process dependencies</span></span>

## <a name="refactor"></a><span data-ttu-id="a1e7b-122">重構</span><span class="sxs-lookup"><span data-stu-id="a1e7b-122">Refactor</span></span>

<span data-ttu-id="a1e7b-123">平台即服務 (PaaS) 選項可減少許多應用程式的相關作業成本。</span><span class="sxs-lookup"><span data-stu-id="a1e7b-123">Platform as a Service (PaaS) options can reduce operational costs associated with many applications.</span></span> <span data-ttu-id="a1e7b-124">稍微重構應用程式以符合 PaaS 模型，是審慎的做法。</span><span class="sxs-lookup"><span data-stu-id="a1e7b-124">It can be prudent to slightly refactor an application to fit a PaaS based model.</span></span>

<span data-ttu-id="a1e7b-125">重構也是指重構程式碼，讓應用程式能夠因應新商機的應用程式開發程序。</span><span class="sxs-lookup"><span data-stu-id="a1e7b-125">Refactor also refers to the application development process of refactoring code to allow an application to deliver on new business opportunities.</span></span>

<span data-ttu-id="a1e7b-126">常見的動因可能包括：</span><span class="sxs-lookup"><span data-stu-id="a1e7b-126">Common drivers could include:</span></span>

* <span data-ttu-id="a1e7b-127">更快速簡易的更新</span><span class="sxs-lookup"><span data-stu-id="a1e7b-127">Faster, shorter, updates</span></span>
* <span data-ttu-id="a1e7b-128">程式碼可攜性</span><span class="sxs-lookup"><span data-stu-id="a1e7b-128">Code portability</span></span>
* <span data-ttu-id="a1e7b-129">更高的雲端效率 (資源、速度、成本)</span><span class="sxs-lookup"><span data-stu-id="a1e7b-129">Greater cloud efficiency (resources, speed, cost)</span></span>

<span data-ttu-id="a1e7b-130">量化分析因素：</span><span class="sxs-lookup"><span data-stu-id="a1e7b-130">Quantitative analysis factors:</span></span>

* <span data-ttu-id="a1e7b-131">應用程式資產大小 (CPU、記憶體、儲存體)</span><span class="sxs-lookup"><span data-stu-id="a1e7b-131">Application asset size (CPU, memory, storage)</span></span>
* <span data-ttu-id="a1e7b-132">相依性 (篩選網路)</span><span class="sxs-lookup"><span data-stu-id="a1e7b-132">Dependencies (network traffic)</span></span>
* <span data-ttu-id="a1e7b-133">使用者流量 (頁面檢視、頁面瀏覽時間、載入時間)</span><span class="sxs-lookup"><span data-stu-id="a1e7b-133">User traffic (page views, time on page, load time)</span></span>
* <span data-ttu-id="a1e7b-134">開發平台 (語言、資料平台、中介層服務)</span><span class="sxs-lookup"><span data-stu-id="a1e7b-134">Development platform (languages, data platform, middle tier services)</span></span>

<span data-ttu-id="a1e7b-135">定性分析因素：</span><span class="sxs-lookup"><span data-stu-id="a1e7b-135">Qualitative analysis factors:</span></span>

* <span data-ttu-id="a1e7b-136">持續性的商業投資</span><span class="sxs-lookup"><span data-stu-id="a1e7b-136">Continued business investments</span></span>
* <span data-ttu-id="a1e7b-137">負載平衡選項/時間軸</span><span class="sxs-lookup"><span data-stu-id="a1e7b-137">Bursting options/timelines</span></span>
* <span data-ttu-id="a1e7b-138">商務程序相依性</span><span class="sxs-lookup"><span data-stu-id="a1e7b-138">Business process dependencies</span></span>

## <a name="rearchitect"></a><span data-ttu-id="a1e7b-139">重新架構</span><span class="sxs-lookup"><span data-stu-id="a1e7b-139">Rearchitect</span></span>

<span data-ttu-id="a1e7b-140">某些過時應用程式與雲端提供者無法相容，因為其架構決策是在應用程式建置時所制定的。</span><span class="sxs-lookup"><span data-stu-id="a1e7b-140">Some aging applications aren't compatible with cloud providers because of the architectural decisions made when the application was built.</span></span> <span data-ttu-id="a1e7b-141">在這種情況下，應用程式可能需要先重新架構，再進行轉換。</span><span class="sxs-lookup"><span data-stu-id="a1e7b-141">In these cases, the application may need to be rearchitected prior to transformation.</span></span>

<span data-ttu-id="a1e7b-142">在其他情況下，與雲端相容、但不屬於雲端原生效益的應用程式，可藉由將解決方案重新架構為雲端原生應用程式，而產生成本效益和操作效率。</span><span class="sxs-lookup"><span data-stu-id="a1e7b-142">In other cases, applications that are cloud compatible, but not cloud native benefits, may produce cost efficiencies and operational efficiencies by rearchitecting the solution to be a cloud native application.</span></span>

<span data-ttu-id="a1e7b-143">常見的動因可能包括：</span><span class="sxs-lookup"><span data-stu-id="a1e7b-143">Common drivers could include:</span></span>

* <span data-ttu-id="a1e7b-144">應用程式的調整能力與靈活性</span><span class="sxs-lookup"><span data-stu-id="a1e7b-144">Application scale and agility</span></span>
* <span data-ttu-id="a1e7b-145">更容易採用新的雲端功能</span><span class="sxs-lookup"><span data-stu-id="a1e7b-145">Easier adoption of new cloud capabilities</span></span>
* <span data-ttu-id="a1e7b-146">混用技術堆疊</span><span class="sxs-lookup"><span data-stu-id="a1e7b-146">Mix of technology stacks</span></span>

<span data-ttu-id="a1e7b-147">量化分析因素：</span><span class="sxs-lookup"><span data-stu-id="a1e7b-147">Quantitative analysis factors:</span></span>

* <span data-ttu-id="a1e7b-148">應用程式資產大小 (CPU、記憶體、儲存體)</span><span class="sxs-lookup"><span data-stu-id="a1e7b-148">Application asset size (CPU, memory, storage)</span></span>
* <span data-ttu-id="a1e7b-149">相依性 (篩選網路)</span><span class="sxs-lookup"><span data-stu-id="a1e7b-149">Dependencies (network traffic)</span></span>
* <span data-ttu-id="a1e7b-150">使用者流量 (頁面檢視、頁面瀏覽時間、載入時間)</span><span class="sxs-lookup"><span data-stu-id="a1e7b-150">User traffic (page views, time on page, load time)</span></span>
* <span data-ttu-id="a1e7b-151">開發平台 (語言、資料平台、中介層服務)</span><span class="sxs-lookup"><span data-stu-id="a1e7b-151">Development platform (languages, data platform, middle tier services)</span></span>

<span data-ttu-id="a1e7b-152">定性分析因素：</span><span class="sxs-lookup"><span data-stu-id="a1e7b-152">Qualitative analysis factors:</span></span>

* <span data-ttu-id="a1e7b-153">不斷成長的商業投資</span><span class="sxs-lookup"><span data-stu-id="a1e7b-153">Growing business investments</span></span>
* <span data-ttu-id="a1e7b-154">營運成本</span><span class="sxs-lookup"><span data-stu-id="a1e7b-154">Operational costs</span></span>
* <span data-ttu-id="a1e7b-155">潛在的意見反應迴圈與 DevOps 投資</span><span class="sxs-lookup"><span data-stu-id="a1e7b-155">Potential feedback loops and DevOps investments</span></span>

## <a name="rebuild"></a><span data-ttu-id="a1e7b-156">重建</span><span class="sxs-lookup"><span data-stu-id="a1e7b-156">Rebuild</span></span>

<span data-ttu-id="a1e7b-157">在某些情況下，可能會因為推動應用程式所需克服的差異過大大，而無法進一步投資。</span><span class="sxs-lookup"><span data-stu-id="a1e7b-157">In some scenarios, the delta that must be overcome to carry forward an application can be too large to justify further investment.</span></span> <span data-ttu-id="a1e7b-158">尤其是在用來符合商務需求的應用程式目前不受支援，或是與目前執行商務程序的方式不一致時，更是如此。</span><span class="sxs-lookup"><span data-stu-id="a1e7b-158">This is especially true for applications that used to meet the needs of the business, but are now unsupported or misaligned with how the business processes are executed today.</span></span> <span data-ttu-id="a1e7b-159">在此情況下，將會依據雲端原生方法建立新的程式碼基底。</span><span class="sxs-lookup"><span data-stu-id="a1e7b-159">In this case, a new code base is created to align with a cloud native approach.</span></span>

<span data-ttu-id="a1e7b-160">常見的動因可能包括：</span><span class="sxs-lookup"><span data-stu-id="a1e7b-160">Common drivers could include:</span></span>

* <span data-ttu-id="a1e7b-161">加速推動創新</span><span class="sxs-lookup"><span data-stu-id="a1e7b-161">Accelerate innovation</span></span>
* <span data-ttu-id="a1e7b-162">快速建置應用程式</span><span class="sxs-lookup"><span data-stu-id="a1e7b-162">Build apps faster</span></span>
* <span data-ttu-id="a1e7b-163">降低營運成本</span><span class="sxs-lookup"><span data-stu-id="a1e7b-163">Reduce operational cost</span></span>

<span data-ttu-id="a1e7b-164">量化分析因素：</span><span class="sxs-lookup"><span data-stu-id="a1e7b-164">Quantitative analysis factors:</span></span>

* <span data-ttu-id="a1e7b-165">應用程式資產大小 (CPU、記憶體、儲存體)</span><span class="sxs-lookup"><span data-stu-id="a1e7b-165">Application asset size (CPU, memory, storage)</span></span>
* <span data-ttu-id="a1e7b-166">相依性 (篩選網路)</span><span class="sxs-lookup"><span data-stu-id="a1e7b-166">Dependencies (network traffic)</span></span>
* <span data-ttu-id="a1e7b-167">使用者流量 (頁面檢視、頁面瀏覽時間、載入時間)</span><span class="sxs-lookup"><span data-stu-id="a1e7b-167">User traffic (page views, time on page, load time)</span></span>
* <span data-ttu-id="a1e7b-168">開發平台 (語言、資料平台、中介層服務)</span><span class="sxs-lookup"><span data-stu-id="a1e7b-168">Development platform (languages, data platform, middle tier services)</span></span>

<span data-ttu-id="a1e7b-169">定性分析因素：</span><span class="sxs-lookup"><span data-stu-id="a1e7b-169">Qualitative analysis Factors:</span></span>

* <span data-ttu-id="a1e7b-170">下降的使用者滿意度</span><span class="sxs-lookup"><span data-stu-id="a1e7b-170">Declining end user satisfaction</span></span>
* <span data-ttu-id="a1e7b-171">商務程序受限於功能</span><span class="sxs-lookup"><span data-stu-id="a1e7b-171">Business processes limited by functionality</span></span>
* <span data-ttu-id="a1e7b-172">潛在的成本、體驗或營收提升</span><span class="sxs-lookup"><span data-stu-id="a1e7b-172">Potential cost, experience, or revenue gains</span></span>

## <a name="replace"></a><span data-ttu-id="a1e7b-173">Replace</span><span class="sxs-lookup"><span data-stu-id="a1e7b-173">Replace</span></span>

<span data-ttu-id="a1e7b-174">解決方案通常會使用時當前最好的技術和方法來實作。</span><span class="sxs-lookup"><span data-stu-id="a1e7b-174">Solutions are generally implemented using the best technology and approach available at the time.</span></span> <span data-ttu-id="a1e7b-175">在某些情況下，軟體即服務 (SaaS) 應用程式足以提供裝載的應用程式所需的所有功能。</span><span class="sxs-lookup"><span data-stu-id="a1e7b-175">In some cases, Software as a Service (SaaS) applications can meet all of the functionality required of the hosted application.</span></span> <span data-ttu-id="a1e7b-176">在這類情況下，可將工作負載排入未來替換的時程中，以將其從轉換工作中有效移除。</span><span class="sxs-lookup"><span data-stu-id="a1e7b-176">In these scenarios, a workload could be slated for future replacement, effectively removing it from the transformation effort.</span></span>

<span data-ttu-id="a1e7b-177">常見的動因可能包括：</span><span class="sxs-lookup"><span data-stu-id="a1e7b-177">Common drivers could include:</span></span>

* <span data-ttu-id="a1e7b-178">標準化業界最佳做法</span><span class="sxs-lookup"><span data-stu-id="a1e7b-178">Standardize around industry-best practices</span></span>
* <span data-ttu-id="a1e7b-179">加速採用商務程序導向方法</span><span class="sxs-lookup"><span data-stu-id="a1e7b-179">Accelerate adoption of business process driven approaches</span></span>
* <span data-ttu-id="a1e7b-180">將開發投資重新配置於可創造競爭差異或優勢的應用程式上</span><span class="sxs-lookup"><span data-stu-id="a1e7b-180">Reallocate development investments into applications that create competitive differentiation or advantages</span></span>

<span data-ttu-id="a1e7b-181">量化分析因素：</span><span class="sxs-lookup"><span data-stu-id="a1e7b-181">Quantitative analysis factors:</span></span>

* <span data-ttu-id="a1e7b-182">降低整體營運成本</span><span class="sxs-lookup"><span data-stu-id="a1e7b-182">General operating cost reductions</span></span>
* <span data-ttu-id="a1e7b-183">VM 大小 (CPU、記憶體、儲存體)</span><span class="sxs-lookup"><span data-stu-id="a1e7b-183">VM size (CPU, memory, storage)</span></span>
* <span data-ttu-id="a1e7b-184">相依性 (篩選網路)</span><span class="sxs-lookup"><span data-stu-id="a1e7b-184">Dependencies (network traffic)</span></span>
* <span data-ttu-id="a1e7b-185">即將淘汰的資產</span><span class="sxs-lookup"><span data-stu-id="a1e7b-185">Assets to be retired</span></span>

<span data-ttu-id="a1e7b-186">定性分析因素：</span><span class="sxs-lookup"><span data-stu-id="a1e7b-186">Qualitative analysis factors:</span></span>

* <span data-ttu-id="a1e7b-187">目前的架構與 SaaS 解決方案的成本效益分析</span><span class="sxs-lookup"><span data-stu-id="a1e7b-187">Cost benefit analysis of current architecture vs SaaS solution</span></span>
* <span data-ttu-id="a1e7b-188">商務程序對應</span><span class="sxs-lookup"><span data-stu-id="a1e7b-188">Business process maps</span></span>
* <span data-ttu-id="a1e7b-189">資料結構描述</span><span class="sxs-lookup"><span data-stu-id="a1e7b-189">Data schemas</span></span>
* <span data-ttu-id="a1e7b-190">自訂或自動化程序</span><span class="sxs-lookup"><span data-stu-id="a1e7b-190">Custom or automated processes</span></span>

## <a name="next-steps"></a><span data-ttu-id="a1e7b-191">後續步驟</span><span class="sxs-lookup"><span data-stu-id="a1e7b-191">Next steps</span></span>

<span data-ttu-id="a1e7b-192">整體而言，這些「合理化的 5R 策略」可套用至數位資產，以制定關於未來各種應用程式型態的合理化決策。</span><span class="sxs-lookup"><span data-stu-id="a1e7b-192">Collectively, these 5 Rs of Rationalization can be applied to a digital estate to make rationalization decisions regarding the future state of each application.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="a1e7b-193">什麼是數位資產？</span><span class="sxs-lookup"><span data-stu-id="a1e7b-193">What is a digital estate?</span></span>](overview.md)

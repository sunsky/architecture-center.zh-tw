---
title: CAF：業務風險在雲端中有何變更？
description: 了解移轉期間的業務風險
author: BrianBlanchard
ms.date: 10/10/2018
ms.openlocfilehash: 458474f3c94c5df4f7ffef439095adf138f33d78
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58246489"
---
<!-- markdownlint-disable MD026 -->

# <a name="how-does-business-risk-change-in-the-cloud"></a><span data-ttu-id="eeab4-103">業務風險在雲端中有何變更？</span><span class="sxs-lookup"><span data-stu-id="eeab4-103">How does business risk change in the cloud?</span></span>

<span data-ttu-id="eeab4-104">了解業務風險是任何雲端轉換最重要的元素之一。</span><span class="sxs-lookup"><span data-stu-id="eeab4-104">An understanding of business risk is one of the most important elements of any cloud transformation.</span></span> <span data-ttu-id="eeab4-105">風險驅動原則，它會影響監視和強制執行的需求。</span><span class="sxs-lookup"><span data-stu-id="eeab4-105">Risk drives policy, it influences monitoring and enforcement requirements.</span></span> <span data-ttu-id="eeab4-106">風險深刻影響我們如何管理在內部部署或在雲端中的數位資產。</span><span class="sxs-lookup"><span data-stu-id="eeab4-106">Risk heavily influences how we manage the digital estate, on-premises or in the cloud.</span></span>

<!-- markdownlint-enable MD026 -->

## <a name="relativity-of-risk"></a><span data-ttu-id="eeab4-107">風險的相對性</span><span class="sxs-lookup"><span data-stu-id="eeab4-107">Relativity of risk</span></span>

<span data-ttu-id="eeab4-108">風險是相對的。</span><span class="sxs-lookup"><span data-stu-id="eeab4-108">Risk is relative.</span></span> <span data-ttu-id="eeab4-109">在封閉建築中且擁有少數 IT 資產的小型公司，其風險是小的。</span><span class="sxs-lookup"><span data-stu-id="eeab4-109">A small company with a few IT assets, in a closed building has little risk.</span></span> <span data-ttu-id="eeab4-110">新增使用者和可存取那些資產的網際網路連線，風險便會增加。</span><span class="sxs-lookup"><span data-stu-id="eeab4-110">Add users and an internet connection with access to those assets, the risk is intensified.</span></span> <span data-ttu-id="eeab4-111">當小型公司成長為財星前 500 名的狀態時，風險會指數倍增加。</span><span class="sxs-lookup"><span data-stu-id="eeab4-111">When that small company grows to Fortune 500 status, the risks are exponentially greater.</span></span> <span data-ttu-id="eeab4-112">隨著營收、業務程序、員工數量和 IT 資產累積，風險會增加並聯合。</span><span class="sxs-lookup"><span data-stu-id="eeab4-112">As revenue, business process, employee counts, and IT assets accumulate, risks increase and coalesce.</span></span> <span data-ttu-id="eeab4-113">輔助產生營收之 IT 資產的具體風險是當它中斷時，也會使營收來源中斷。</span><span class="sxs-lookup"><span data-stu-id="eeab4-113">IT assets that aid in generating revenue are at tangible risk of stopping that revenue stream in the event of an outage.</span></span> <span data-ttu-id="eeab4-114">停機的每個時刻都等於損失。</span><span class="sxs-lookup"><span data-stu-id="eeab4-114">Every moment of downtime equates to losses.</span></span> <span data-ttu-id="eeab4-115">同樣地，隨著資料累積，損害客戶的風險也會增長。</span><span class="sxs-lookup"><span data-stu-id="eeab4-115">Likewise, as data accumulates, the risk of harming customers grows.</span></span>

<span data-ttu-id="eeab4-116">在傳統內部部署世界中，IT 治理小組專注於評估這些風險。</span><span class="sxs-lookup"><span data-stu-id="eeab4-116">In the traditional on-premises world, IT governance teams focus on assessing those risks.</span></span> <span data-ttu-id="eeab4-117">建立程序，以降低風險。</span><span class="sxs-lookup"><span data-stu-id="eeab4-117">Creating processes to mitigate the risk.</span></span> <span data-ttu-id="eeab4-118">部署系統以確保風險降低措施成功並實作。</span><span class="sxs-lookup"><span data-stu-id="eeab4-118">Deploying systems to ensure mitigation measures are successful and implemented.</span></span> <span data-ttu-id="eeab4-119">這會平衡在連線、新式的企業環境中營運所需的風險。</span><span class="sxs-lookup"><span data-stu-id="eeab4-119">This balances the risks required to operate in a connected, modern business environment.</span></span>

## <a name="understanding-business-risks-in-the-cloud"></a><span data-ttu-id="eeab4-120">了解雲端中的業務風險</span><span class="sxs-lookup"><span data-stu-id="eeab4-120">Understanding business risks in the cloud</span></span>

<span data-ttu-id="eeab4-121">在轉換期間，可以看到相同的相對風險。</span><span class="sxs-lookup"><span data-stu-id="eeab4-121">During a transformation, the same relative risks can be seen.</span></span>

* <span data-ttu-id="eeab4-122">在測試早期，會部署幾乎沒有相關資料的少數資產。</span><span class="sxs-lookup"><span data-stu-id="eeab4-122">During early experimentation, a few assets are deployed with little to no relevant data.</span></span> <span data-ttu-id="eeab4-123">風險很小。</span><span class="sxs-lookup"><span data-stu-id="eeab4-123">The risk is small.</span></span>
* <span data-ttu-id="eeab4-124">部署第一個工作負載之後，風險會上升一點。</span><span class="sxs-lookup"><span data-stu-id="eeab4-124">When the first workload is deployed, risk goes up a little.</span></span> <span data-ttu-id="eeab4-125">選擇使用者群小且原本就是低風險的應用程式，可以輕鬆降低此風險。</span><span class="sxs-lookup"><span data-stu-id="eeab4-125">This risk is easily mitigated by choosing an inherently low risk application with a small user base.</span></span>
* <span data-ttu-id="eeab4-126">隨著更多工作負載上線，風險會在每次發行時變更。</span><span class="sxs-lookup"><span data-stu-id="eeab4-126">As more workloads come online, risks change at each release.</span></span> <span data-ttu-id="eeab4-127">新應用程式上線，風險會變更。</span><span class="sxs-lookup"><span data-stu-id="eeab4-127">New apps go live, risks change.</span></span>
* <span data-ttu-id="eeab4-128">公司將前 10-20 個應用程式遷移到線上時的風險勢態，與第 1000 個應用程式在雲端進入生產環境時的截然不同。</span><span class="sxs-lookup"><span data-stu-id="eeab4-128">When a company brings the first 10-20 applications online, the risk profile is much different that it is when the 1000th applications go into production in the cloud.</span></span>

<span data-ttu-id="eeab4-129">在傳統內部部署資產中累積的資產很可能隨時間累積。</span><span class="sxs-lookup"><span data-stu-id="eeab4-129">The assets that accumulated in the traditional, on-premises estate likely accumulated overtime.</span></span> <span data-ttu-id="eeab4-130">企業和 IT 小組的成熟度也以類似的方式增長。</span><span class="sxs-lookup"><span data-stu-id="eeab4-130">The maturity of the business and IT teams was likely growing in a similar fashion.</span></span> <span data-ttu-id="eeab4-131">這樣平行成長可能傾向於建立一些不必要的原則負擔。</span><span class="sxs-lookup"><span data-stu-id="eeab4-131">That parallel growth can tend to create some unnecessary policy baggage.</span></span>

<span data-ttu-id="eeab4-132">在雲端轉換期間，企業和 IT 小組都有機會重設那些原則，並以成熟心態建立新原則。</span><span class="sxs-lookup"><span data-stu-id="eeab4-132">During a cloud transformation, both the business and IT teams have an opportunity to reset those policies and build new with a matured mindset.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="what-is-a-business-risk-mvp"></a><span data-ttu-id="eeab4-133">什麼是業務風險 MVP？</span><span class="sxs-lookup"><span data-stu-id="eeab4-133">What is a business risk MVP?</span></span>

<span data-ttu-id="eeab4-134">**最簡可行產品**是業界標準詞彙，其定義是某事物可產生具體價值的最小單位。</span><span class="sxs-lookup"><span data-stu-id="eeab4-134">**Minimum viable product** is an industry-standard term for defining the smallest unit of something that can produce tangible value.</span></span> <span data-ttu-id="eeab4-135">在業務風險 MVP 中，小組從假設某些資產將部署到雲端環境開始。</span><span class="sxs-lookup"><span data-stu-id="eeab4-135">In a business risk MVP, the team starts with an assumption that some assets will be deployed to a cloud environment.</span></span> <span data-ttu-id="eeab4-136">當下並不知道是哪些資產。</span><span class="sxs-lookup"><span data-stu-id="eeab4-136">It's unknown at the time what those assets are.</span></span> <span data-ttu-id="eeab4-137">也不知道那些資產會處理的資料類型為何。</span><span class="sxs-lookup"><span data-stu-id="eeab4-137">It's also unknown what types of data will be processed by those assets.</span></span>

<span data-ttu-id="eeab4-138">雲端治理小組可針對最糟的案例來建置，並將每個可能的原則對應到雲端。</span><span class="sxs-lookup"><span data-stu-id="eeab4-138">The Cloud Governance team could build for the worst-case scenario and map every possible policy to the cloud.</span></span> <span data-ttu-id="eeab4-139">不建議這麼做，但這是一個選項。</span><span class="sxs-lookup"><span data-stu-id="eeab4-139">This is not advised, but is an option.</span></span>

<span data-ttu-id="eeab4-140">相反地，小組可以採取 MVP 方法，並定義起始點和適用於大部分/全部資產的一組假設。</span><span class="sxs-lookup"><span data-stu-id="eeab4-140">Conversely, the team could take an MVP approach and define a starting point and set of assumptions that would be true for most/all assets.</span></span>
<span data-ttu-id="eeab4-141">下列是幾個非常基本的範例：</span><span class="sxs-lookup"><span data-stu-id="eeab4-141">The following are a few extremely basic examples:</span></span>

* <span data-ttu-id="eeab4-142">所有資產都有被終止的風險 (因為錯誤、失誤或維護)</span><span class="sxs-lookup"><span data-stu-id="eeab4-142">All assets are at risk of being terminated (through error, mistake or maintenance)</span></span>
* <span data-ttu-id="eeab4-143">所有資產都有產生太多費用的風險</span><span class="sxs-lookup"><span data-stu-id="eeab4-143">All assets are at risk of generating too much spending</span></span>
* <span data-ttu-id="eeab4-144">所有資產都可能因弱式密碼而遭入侵</span><span class="sxs-lookup"><span data-stu-id="eeab4-144">All assets could be compromised by weak passwords</span></span>
* <span data-ttu-id="eeab4-145">任何資產的所有開啟的連接埠都對網際網路公開，而有遭入侵的風險</span><span class="sxs-lookup"><span data-stu-id="eeab4-145">Any asset with all open ports exposed to the internet are at risk of compromise</span></span>

<span data-ttu-id="eeab4-146">上述範例旨在將 MVP 業務風險建立為理論。</span><span class="sxs-lookup"><span data-stu-id="eeab4-146">The above examples are meant to establish MVP business risks as a theory.</span></span> <span data-ttu-id="eeab4-147">每個環境的實際清單會是唯一的。</span><span class="sxs-lookup"><span data-stu-id="eeab4-147">The actual list will be unique to every environment.</span></span>
<span data-ttu-id="eeab4-148">建立業務風險 MVP 之後，可以將它們轉換為[原則](overview.md)以降低每個風險。</span><span class="sxs-lookup"><span data-stu-id="eeab4-148">Once the Business Risk MVP is established, they can be converted to [Policies](overview.md) to mitigate each risk.</span></span>

<!-- markdownlint-enable MD026 -->

## <a name="incremental-risk-mitigation"></a><span data-ttu-id="eeab4-149">漸進風險降低</span><span class="sxs-lookup"><span data-stu-id="eeab4-149">Incremental risk mitigation</span></span>

<span data-ttu-id="eeab4-150">假設業務風險 MVP 是起始點，治理可以與計劃的部署平行成熟 (相對於與業務成長平行增長)。</span><span class="sxs-lookup"><span data-stu-id="eeab4-150">Assuming a business risk MVP is the starting point, governance can mature in parallel to planned deployment (as opposed to growing in parallel to business growth).</span></span> <span data-ttu-id="eeab4-151">這是更穩定的治理成熟度模型。</span><span class="sxs-lookup"><span data-stu-id="eeab4-151">This is a much more stable model for governance maturity.</span></span> <span data-ttu-id="eeab4-152">在每次反覆項目中，新資產都會複寫並預備。</span><span class="sxs-lookup"><span data-stu-id="eeab4-152">At each iteration, new assets are replicated and staged.</span></span> <span data-ttu-id="eeab4-153">在每次發行，工作負載都已經準備好進行生產環境升階。</span><span class="sxs-lookup"><span data-stu-id="eeab4-153">At each release, workloads are readied for production promotion.</span></span> <span data-ttu-id="eeab4-154">當然，每次循環相對風險都可能增長。</span><span class="sxs-lookup"><span data-stu-id="eeab4-154">Of course, the relative risk could grow with each cycle.</span></span>

<span data-ttu-id="eeab4-155">當雲端治理小組與雲端採用小組平行運作時，業務風險的成長同樣可以被解決。</span><span class="sxs-lookup"><span data-stu-id="eeab4-155">When the Cloud Governance team operates in parallel to the cloud adoption teams, the growth of business risks can likewise be addressed.</span></span> <span data-ttu-id="eeab4-156">預備的每個資產都能根據風險輕鬆地分類。</span><span class="sxs-lookup"><span data-stu-id="eeab4-156">Each asset staged can easily be classified according to risk.</span></span> <span data-ttu-id="eeab4-157">資料分類文件的建置或建立可以和預備工作平行進行。</span><span class="sxs-lookup"><span data-stu-id="eeab4-157">Data classification documents can be built or created in parallel to staging.</span></span> <span data-ttu-id="eeab4-158">同樣可以記載風險勢態和公開點。</span><span class="sxs-lookup"><span data-stu-id="eeab4-158">Risk profile and exposure points can likewise be documented.</span></span> <span data-ttu-id="eeab4-159">隨時間經過，在整個組織就能看出非常清楚的業務風險。</span><span class="sxs-lookup"><span data-stu-id="eeab4-159">Overtime an extremely clear view of business risk wil come into focus across the organization.</span></span>

<span data-ttu-id="eeab4-160">在每次的反覆項目中，雲端治理小組會與雲端策略小組合作，以快速交流新風險、風險降低策略、權衡取捨和潛在成本。</span><span class="sxs-lookup"><span data-stu-id="eeab4-160">With each iteration, the Cloud Governance team can work with Cloud Strategy team to quickly communicate new risks, mitigation strategies, tradeoffs, and potential costs.</span></span> <span data-ttu-id="eeab4-161">這樣可讓業務參與者 IT 領導者共同做出成熟且資訊充足的決策。</span><span class="sxs-lookup"><span data-stu-id="eeab4-161">This empowers business participants and IT leaders to partner in mature, well-informed decisions.</span></span> <span data-ttu-id="eeab4-162">那些決策接著會通知原則成熟度。</span><span class="sxs-lookup"><span data-stu-id="eeab4-162">Those decisions then inform policy maturity.</span></span> <span data-ttu-id="eeab4-163">在必要時，原則變更會針對核心基礎結構系統成熟度產生新的工作項目。</span><span class="sxs-lookup"><span data-stu-id="eeab4-163">When required, the policy changes produce new work items for the maturity of core infrastructure systems.</span></span> <span data-ttu-id="eeab4-164">當需要變更為預備系統時，雲端採用小組有充足的時間進行變更，企業也同時測試預備系統並開發使用者採用計劃。</span><span class="sxs-lookup"><span data-stu-id="eeab4-164">When changes to staged systems are required, the cloud adoption teams have ample time to make changes, while the business tests the staged systems and develops a user adoption plan.</span></span>

<span data-ttu-id="eeab4-165">此方法可降低風險，同時讓小組快速推動程序。</span><span class="sxs-lookup"><span data-stu-id="eeab4-165">This approach minimizes risks, while empowering the team to move quickly.</span></span> <span data-ttu-id="eeab4-166">它也能確保風險會即時被識別出，並在部署之前解決。</span><span class="sxs-lookup"><span data-stu-id="eeab4-166">It also ensures that risks are promptly identified and resolved before deployment.</span></span>

## <a name="next-steps"></a><span data-ttu-id="eeab4-167">後續步驟</span><span class="sxs-lookup"><span data-stu-id="eeab4-167">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="eeab4-168">評估風險承受度</span><span class="sxs-lookup"><span data-stu-id="eeab4-168">Evaluate risk tolerance</span></span>](./risk-tolerance.md)

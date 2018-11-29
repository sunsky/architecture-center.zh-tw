---
title: 微服務架構樣式
description: 說明 Azure 上微服務架構的優點、挑戰和最佳做法
author: MikeWasson
ms.date: 11/13/2018
ms.openlocfilehash: 4e5d50f829323829c953977257e690354566ebf6
ms.sourcegitcommit: 19a517a2fb70768b3edb9a7c3c37197baa61d9b5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2018
ms.locfileid: "52295526"
---
# <a name="microservices-architecture-style"></a><span data-ttu-id="57515-103">微服務架構樣式</span><span class="sxs-lookup"><span data-stu-id="57515-103">Microservices architecture style</span></span>

<span data-ttu-id="57515-104">微服務架構是由一組小型的自發服務所組成。</span><span class="sxs-lookup"><span data-stu-id="57515-104">A microservices architecture consists of a collection of small, autonomous services.</span></span> <span data-ttu-id="57515-105">每個服務各自獨立，並且應該實作單一的商務功能。</span><span class="sxs-lookup"><span data-stu-id="57515-105">Each service is self-contained and should implement a single business capability.</span></span> 

![](./images/microservices-logical.svg)
 
<span data-ttu-id="57515-106">在某些方面，微服務算是服務導向架構 (SOA) 的自然演化，但微服務和 SOA 之間存有差異。</span><span class="sxs-lookup"><span data-stu-id="57515-106">In some ways, microservices are the natural evolution of service oriented architectures (SOA), but there are differences between microservices and SOA.</span></span> <span data-ttu-id="57515-107">以下是微服務的一些定義特性：</span><span class="sxs-lookup"><span data-stu-id="57515-107">Here are some defining characteristics of a microservice:</span></span>

- <span data-ttu-id="57515-108">微服務架構中的服務屬於小型、獨立且鬆散結合的服務。</span><span class="sxs-lookup"><span data-stu-id="57515-108">In a microservices architecture, services are small, independent, and loosely coupled.</span></span>

- <span data-ttu-id="57515-109">每個服務都是個別的程式碼基底，可由小型的開發小組負責管理。</span><span class="sxs-lookup"><span data-stu-id="57515-109">Each service is a separate codebase, which can be managed by a small development team.</span></span>

- <span data-ttu-id="57515-110">服務可以獨立部署。</span><span class="sxs-lookup"><span data-stu-id="57515-110">Services can be deployed independently.</span></span> <span data-ttu-id="57515-111">小組可以直接更新現有服務，而不需要重建和重新部署整個應用程式。</span><span class="sxs-lookup"><span data-stu-id="57515-111">A team can update an existing service without rebuilding and redeploying the entire application.</span></span>

- <span data-ttu-id="57515-112">服務需負責保存自己的資料或外部狀態。</span><span class="sxs-lookup"><span data-stu-id="57515-112">Services are responsible for persisting their own data or external state.</span></span> <span data-ttu-id="57515-113">這一點不同於傳統模型，傳統模型是由個別資料層處理資料持續性。</span><span class="sxs-lookup"><span data-stu-id="57515-113">This differs from the traditional model, where a separate data layer handles data persistence.</span></span>

- <span data-ttu-id="57515-114">服務會使用妥善定義的 API 來彼此通訊。</span><span class="sxs-lookup"><span data-stu-id="57515-114">Services communicate with each other by using well-defined APIs.</span></span> <span data-ttu-id="57515-115">每個服務的內部實作詳細資料都會對其他服務隱藏。</span><span class="sxs-lookup"><span data-stu-id="57515-115">Internal implementation details of each service are hidden from other services.</span></span>

- <span data-ttu-id="57515-116">服務不需要共用相同的技術堆疊、程式庫或架構。</span><span class="sxs-lookup"><span data-stu-id="57515-116">Services don't need to share the same technology stack, libraries, or frameworks.</span></span>

<span data-ttu-id="57515-117">除了服務本身外，典型的微服務架構中還會出現一些其他元件：</span><span class="sxs-lookup"><span data-stu-id="57515-117">Besides for the services themselves, some other components appear in a typical microservices architecture:</span></span>

<span data-ttu-id="57515-118">**管理**。</span><span class="sxs-lookup"><span data-stu-id="57515-118">**Management**.</span></span> <span data-ttu-id="57515-119">管理元件負責將服務放在節點上、識別失敗、跨節點重新平衡服務等等。</span><span class="sxs-lookup"><span data-stu-id="57515-119">The management component is responsible for placing services on nodes, identifying failures, rebalancing services across nodes, and so forth.</span></span>  

<span data-ttu-id="57515-120">**服務探索**。</span><span class="sxs-lookup"><span data-stu-id="57515-120">**Service Discovery**.</span></span>  <span data-ttu-id="57515-121">保有服務及其所在節點的清單。</span><span class="sxs-lookup"><span data-stu-id="57515-121">Maintains a list of services and which nodes they are located on.</span></span> <span data-ttu-id="57515-122">可進行服務查閱來尋找服務端點。</span><span class="sxs-lookup"><span data-stu-id="57515-122">Enables service lookup to find the endpoint for a service.</span></span> 

<span data-ttu-id="57515-123">**API 閘道**。</span><span class="sxs-lookup"><span data-stu-id="57515-123">**API Gateway**.</span></span> <span data-ttu-id="57515-124">API 閘道是用戶端的進入點。</span><span class="sxs-lookup"><span data-stu-id="57515-124">The API gateway is the entry point for clients.</span></span> <span data-ttu-id="57515-125">用戶端不會直接呼叫服務。</span><span class="sxs-lookup"><span data-stu-id="57515-125">Clients don't call services directly.</span></span> <span data-ttu-id="57515-126">相反地，它們會呼叫 API 閘道，API 閘道再將呼叫轉送至後端的適當服務。</span><span class="sxs-lookup"><span data-stu-id="57515-126">Instead, they call the API gateway, which forwards the call to the appropriate services on the back end.</span></span> <span data-ttu-id="57515-127">API 閘道可能會彙總數個服務的回應，並傳回彙總的回應。</span><span class="sxs-lookup"><span data-stu-id="57515-127">The API gateway might aggregate the responses from several services and return the aggregated response.</span></span> 

<span data-ttu-id="57515-128">使用 API 閘道的優點包括：</span><span class="sxs-lookup"><span data-stu-id="57515-128">The advantages of using an API gateway include:</span></span>

- <span data-ttu-id="57515-129">它可讓用戶端與服務分離。</span><span class="sxs-lookup"><span data-stu-id="57515-129">It decouples clients from services.</span></span> <span data-ttu-id="57515-130">服務可以控制版本或重構，而不需要更新所有的用戶端。</span><span class="sxs-lookup"><span data-stu-id="57515-130">Services can be versioned or refactored without needing to update all of the clients.</span></span>

-  <span data-ttu-id="57515-131">服務可使用非專供 Web 使用的傳訊通訊協定，例如 AMQP。</span><span class="sxs-lookup"><span data-stu-id="57515-131">Services can use messaging protocols that are not web friendly, such as AMQP.</span></span>

- <span data-ttu-id="57515-132">API 閘道可以執行其他跨領域功能，例如驗證、記錄、SSL 終止和負載平衡。</span><span class="sxs-lookup"><span data-stu-id="57515-132">The API Gateway can perform other cross-cutting functions such as authentication, logging, SSL termination, and load balancing.</span></span>

## <a name="when-to-use-this-architecture"></a><span data-ttu-id="57515-133">使用此架構的時機</span><span class="sxs-lookup"><span data-stu-id="57515-133">When to use this architecture</span></span>

<span data-ttu-id="57515-134">請考慮將此架構樣式用於：</span><span class="sxs-lookup"><span data-stu-id="57515-134">Consider this architecture style for:</span></span>

- <span data-ttu-id="57515-135">需要高發行速度的大型應用程式。</span><span class="sxs-lookup"><span data-stu-id="57515-135">Large applications that require a high release velocity.</span></span>

- <span data-ttu-id="57515-136">需要具有高擴充性的複雜應用程式。</span><span class="sxs-lookup"><span data-stu-id="57515-136">Complex applications that need to be highly scalable.</span></span>

- <span data-ttu-id="57515-137">具有豐富網域或許多子網域的應用程式。</span><span class="sxs-lookup"><span data-stu-id="57515-137">Applications with rich domains or many subdomains.</span></span>

- <span data-ttu-id="57515-138">由小型開發小組所組成的組織。</span><span class="sxs-lookup"><span data-stu-id="57515-138">An organization that consists of small development teams.</span></span>


## <a name="benefits"></a><span data-ttu-id="57515-139">優點</span><span class="sxs-lookup"><span data-stu-id="57515-139">Benefits</span></span> 

- <span data-ttu-id="57515-140">**獨立部署**。</span><span class="sxs-lookup"><span data-stu-id="57515-140">**Independent deployments**.</span></span> <span data-ttu-id="57515-141">您可以逕自更新服務而不必重新部署整個應用程式，並於發生錯誤時復原或向前復原。</span><span class="sxs-lookup"><span data-stu-id="57515-141">You can update a service without redeploying the entire application, and roll back or roll forward an update if something goes wrong.</span></span> <span data-ttu-id="57515-142">錯誤修正和功能版本會更容易管理且風險較低。</span><span class="sxs-lookup"><span data-stu-id="57515-142">Bug fixes and feature releases are more manageable and less risky.</span></span>

- <span data-ttu-id="57515-143">**獨立開發**。</span><span class="sxs-lookup"><span data-stu-id="57515-143">**Independent development**.</span></span> <span data-ttu-id="57515-144">單一開發小組可以建置、測試及部署服務。</span><span class="sxs-lookup"><span data-stu-id="57515-144">A single development team can build, test, and deploy a service.</span></span> <span data-ttu-id="57515-145">結果便是不斷創新和更快速的發行頻率。</span><span class="sxs-lookup"><span data-stu-id="57515-145">The result is continuous innovation and a faster release cadence.</span></span> 

- <span data-ttu-id="57515-146">**小型焦點小組**。</span><span class="sxs-lookup"><span data-stu-id="57515-146">**Small, focused teams**.</span></span> <span data-ttu-id="57515-147">小組可以專注於一項服務。</span><span class="sxs-lookup"><span data-stu-id="57515-147">Teams can focus on one service.</span></span> <span data-ttu-id="57515-148">每個服務的範圍越小，就能更容易了解程式碼基底，並讓新的小組成員容易跟上進展。</span><span class="sxs-lookup"><span data-stu-id="57515-148">The smaller scope of each service makes the code base easier to understand, and it's easier for new team members to ramp up.</span></span>

- <span data-ttu-id="57515-149">**錯誤隔離**。</span><span class="sxs-lookup"><span data-stu-id="57515-149">**Fault isolation**.</span></span> <span data-ttu-id="57515-150">如果服務停止運作，不必因此取出整個應用程式。</span><span class="sxs-lookup"><span data-stu-id="57515-150">If a service goes down, it won't take out the entire application.</span></span> <span data-ttu-id="57515-151">不過，這並非表示您可以不必付出代價就完成復原。</span><span class="sxs-lookup"><span data-stu-id="57515-151">However, that doesn't mean you get resiliency for free.</span></span> <span data-ttu-id="57515-152">您仍需遵循復原功能的最佳做法和設計模式。</span><span class="sxs-lookup"><span data-stu-id="57515-152">You still need to follow resiliency best practices and design patterns.</span></span> <span data-ttu-id="57515-153">請參閱[為 Azure 設計復原應用程式][resiliency-overview]。</span><span class="sxs-lookup"><span data-stu-id="57515-153">See [Designing resilient applications for Azure][resiliency-overview].</span></span>

- <span data-ttu-id="57515-154">**混合技術堆疊**。</span><span class="sxs-lookup"><span data-stu-id="57515-154">**Mixed technology stacks**.</span></span> <span data-ttu-id="57515-155">小組可以挑選最適合其服務使用的技術。</span><span class="sxs-lookup"><span data-stu-id="57515-155">Teams can pick the technology that best fits their service.</span></span> 

- <span data-ttu-id="57515-156">**細微調整**。</span><span class="sxs-lookup"><span data-stu-id="57515-156">**Granular scaling**.</span></span> <span data-ttu-id="57515-157">服務可以獨立調整。</span><span class="sxs-lookup"><span data-stu-id="57515-157">Services can be scaled independently.</span></span> <span data-ttu-id="57515-158">同時，每個 VM 的服務密度越高，代表 VM 資源已充分運用。</span><span class="sxs-lookup"><span data-stu-id="57515-158">At the same time, the higher density of services per VM means that VM resources are fully utilized.</span></span> <span data-ttu-id="57515-159">使用放置條件約束，服務即可對應至 VM 設定檔 (高 CPU、高記憶體等等)。</span><span class="sxs-lookup"><span data-stu-id="57515-159">Using placement constraints, a services can be matched to a VM profile (high CPU, high memory, and so on).</span></span>

## <a name="challenges"></a><span data-ttu-id="57515-160">挑戰</span><span class="sxs-lookup"><span data-stu-id="57515-160">Challenges</span></span>

- <span data-ttu-id="57515-161">**複雜度**。</span><span class="sxs-lookup"><span data-stu-id="57515-161">**Complexity**.</span></span> <span data-ttu-id="57515-162">微服務應用程式的變動組件數量比同等單體式應用程式的還多。</span><span class="sxs-lookup"><span data-stu-id="57515-162">A microservices application has more moving parts than the equivalent monolithic application.</span></span> <span data-ttu-id="57515-163">每個服務會更為簡單，但是系統整個會變得更複雜。</span><span class="sxs-lookup"><span data-stu-id="57515-163">Each service is simpler, but the entire system as a whole is more complex.</span></span>

- <span data-ttu-id="57515-164">**開發和測試**。</span><span class="sxs-lookup"><span data-stu-id="57515-164">**Development and test**.</span></span> <span data-ttu-id="57515-165">根據服務相依性來進行開發需要不同的方法。</span><span class="sxs-lookup"><span data-stu-id="57515-165">Developing against service dependencies requires a different approach.</span></span> <span data-ttu-id="57515-166">現有工具的設計不一定可處理服務相依性。</span><span class="sxs-lookup"><span data-stu-id="57515-166">Existing tools are not necessarily designed to work with service dependencies.</span></span> <span data-ttu-id="57515-167">跨服務界限進行重構會很困難。</span><span class="sxs-lookup"><span data-stu-id="57515-167">Refactoring across service boundaries can be difficult.</span></span> <span data-ttu-id="57515-168">測試服務相依性也會很困難，尤其是當應用程式的演變速度很快時。</span><span class="sxs-lookup"><span data-stu-id="57515-168">It is also challenging to test service dependencies, especially when the application is evolving quickly.</span></span>

- <span data-ttu-id="57515-169">**缺乏控管**。</span><span class="sxs-lookup"><span data-stu-id="57515-169">**Lack of governance**.</span></span> <span data-ttu-id="57515-170">微服務的非集中式建置方法有其優點，卻也可能導致問題發生。</span><span class="sxs-lookup"><span data-stu-id="57515-170">The decentralized approach to building microservices has advantages, but it can also lead to problems.</span></span> <span data-ttu-id="57515-171">您最終可能會有很多不同的語言和架構，而讓應用程式變得難以維護。</span><span class="sxs-lookup"><span data-stu-id="57515-171">You may end up with so many different languages and frameworks that the application becomes hard to maintain.</span></span> <span data-ttu-id="57515-172">備有某些適用於全部專案的標準，而不要過度限制小組的彈性，這樣的做法可能會有幫助。</span><span class="sxs-lookup"><span data-stu-id="57515-172">It may be useful to put some project-wide standards in place, without overly restricting teams' flexibility.</span></span> <span data-ttu-id="57515-173">這特別適用於跨領域功能，例如記錄。</span><span class="sxs-lookup"><span data-stu-id="57515-173">This especially applies to cross-cutting functionality such as logging.</span></span>

- <span data-ttu-id="57515-174">**網路壅塞與延遲**。</span><span class="sxs-lookup"><span data-stu-id="57515-174">**Network congestion and latency**.</span></span> <span data-ttu-id="57515-175">使用許多小型且細微的服務可能會導致服務之間需要更多通訊。</span><span class="sxs-lookup"><span data-stu-id="57515-175">The use of many small, granular services can result in more interservice communication.</span></span> <span data-ttu-id="57515-176">此外，如果服務相依性鏈結太長 (服務 A 呼叫 B、B 再呼叫 C...)，額外的延遲可能成為問題。</span><span class="sxs-lookup"><span data-stu-id="57515-176">Also, if the chain of service dependencies gets too long (service A calls B, which calls C...), the additional latency can become a problem.</span></span> <span data-ttu-id="57515-177">您將必須謹慎地設計 API。</span><span class="sxs-lookup"><span data-stu-id="57515-177">You will need to design APIs carefully.</span></span> <span data-ttu-id="57515-178">請避免對話過度的 API、考慮使用序列化格式，並找找看有沒有地方可以使用非同步的通訊模式。</span><span class="sxs-lookup"><span data-stu-id="57515-178">Avoid overly chatty APIs, think about serialization formats, and look for places to use asynchronous communication patterns.</span></span>

- <span data-ttu-id="57515-179">**資料完整性**。</span><span class="sxs-lookup"><span data-stu-id="57515-179">**Data integrity**.</span></span> <span data-ttu-id="57515-180">每個微服務負責維持自己的資料持續性。</span><span class="sxs-lookup"><span data-stu-id="57515-180">With each microservice responsible for its own data persistence.</span></span> <span data-ttu-id="57515-181">因此，維持資料一致性會成為挑戰。</span><span class="sxs-lookup"><span data-stu-id="57515-181">As a result, data consistency can be a challenge.</span></span> <span data-ttu-id="57515-182">可能的話，請採用最終一致性。</span><span class="sxs-lookup"><span data-stu-id="57515-182">Embrace eventual consistency where possible.</span></span>

- <span data-ttu-id="57515-183">**管理**。</span><span class="sxs-lookup"><span data-stu-id="57515-183">**Management**.</span></span> <span data-ttu-id="57515-184">想要成功運用微服務，就必須有成熟的 DevOps 文化。</span><span class="sxs-lookup"><span data-stu-id="57515-184">To be successful with microservices requires a mature DevOps culture.</span></span> <span data-ttu-id="57515-185">在服務之間建立關聯式記錄會非常困難。</span><span class="sxs-lookup"><span data-stu-id="57515-185">Correlated logging across services can be challenging.</span></span> <span data-ttu-id="57515-186">一般而言，記錄必須將多個服務呼叫相互關聯到單一使用者作業。</span><span class="sxs-lookup"><span data-stu-id="57515-186">Typically, logging must correlate multiple service calls for a single user operation.</span></span>

- <span data-ttu-id="57515-187">**版本控制**。</span><span class="sxs-lookup"><span data-stu-id="57515-187">**Versioning**.</span></span> <span data-ttu-id="57515-188">服務的更新不得打斷與其相依的服務。</span><span class="sxs-lookup"><span data-stu-id="57515-188">Updates to a service must not break services that depend on it.</span></span> <span data-ttu-id="57515-189">多個服務有可能在同一時間一起更新，因此若未謹慎設計，便可能發生回溯相容性或往後相容性問題。</span><span class="sxs-lookup"><span data-stu-id="57515-189">Multiple services could be updated at any given time, so without careful design, you might have problems with backward or forward compatibility.</span></span>

- <span data-ttu-id="57515-190">**技能集**。</span><span class="sxs-lookup"><span data-stu-id="57515-190">**Skillset**.</span></span> <span data-ttu-id="57515-191">微服務是高度分散的系統。</span><span class="sxs-lookup"><span data-stu-id="57515-191">Microservices are highly distributed systems.</span></span> <span data-ttu-id="57515-192">請謹慎評估小組是否有技能和經驗能夠成功。</span><span class="sxs-lookup"><span data-stu-id="57515-192">Carefully evaluate whether the team has the skills and experience to be successful.</span></span>

## <a name="best-practices"></a><span data-ttu-id="57515-193">最佳作法</span><span class="sxs-lookup"><span data-stu-id="57515-193">Best practices</span></span>

- <span data-ttu-id="57515-194">根據商業領域建立服務的模型。</span><span class="sxs-lookup"><span data-stu-id="57515-194">Model services around the business domain.</span></span> 

- <span data-ttu-id="57515-195">將所有項目拆開。</span><span class="sxs-lookup"><span data-stu-id="57515-195">Decentralize everything.</span></span> <span data-ttu-id="57515-196">由個別小組負責設計和建置服務。</span><span class="sxs-lookup"><span data-stu-id="57515-196">Individual teams are responsible for designing and building services.</span></span> <span data-ttu-id="57515-197">避免共用程式碼或資料結構描述。</span><span class="sxs-lookup"><span data-stu-id="57515-197">Avoid sharing code or data schemas.</span></span> 

- <span data-ttu-id="57515-198">資料儲存體應該為擁有資料的服務私用。</span><span class="sxs-lookup"><span data-stu-id="57515-198">Data storage should be private to the service that owns the data.</span></span> <span data-ttu-id="57515-199">針對每個服務和資料類型使用最佳的儲存體。</span><span class="sxs-lookup"><span data-stu-id="57515-199">Use the best storage for each service and data type.</span></span> 

- <span data-ttu-id="57515-200">服務應透過設計良好的 API 進行通訊。</span><span class="sxs-lookup"><span data-stu-id="57515-200">Services communicate through well-designed APIs.</span></span> <span data-ttu-id="57515-201">避免洩漏實作詳細資料。</span><span class="sxs-lookup"><span data-stu-id="57515-201">Avoid leaking implementation details.</span></span> <span data-ttu-id="57515-202">API 應該建立網域的模型，而不是建立服務內部實作的模型。</span><span class="sxs-lookup"><span data-stu-id="57515-202">APIs should model the domain, not the internal implementation of the service.</span></span>

- <span data-ttu-id="57515-203">避免服務彼此結合。</span><span class="sxs-lookup"><span data-stu-id="57515-203">Avoid coupling between services.</span></span> <span data-ttu-id="57515-204">結合的原因包括共用資料庫結構描述和固定的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="57515-204">Causes of coupling include shared database schemas and rigid communication protocols.</span></span>

- <span data-ttu-id="57515-205">將跨領域考量 (例如驗證和 SSL 終止) 卸載給閘道。</span><span class="sxs-lookup"><span data-stu-id="57515-205">Offload cross-cutting concerns, such as authentication and SSL termination, to the gateway.</span></span>

- <span data-ttu-id="57515-206">不要在閘道中暴露網域資訊。</span><span class="sxs-lookup"><span data-stu-id="57515-206">Keep domain knowledge out of the gateway.</span></span> <span data-ttu-id="57515-207">閘道應該在不知道商務規則或網域邏輯的情況下處理和路由傳送用戶端要求。</span><span class="sxs-lookup"><span data-stu-id="57515-207">The gateway should handle and route client requests without any knowledge of the business rules or domain logic.</span></span> <span data-ttu-id="57515-208">否則，閘道會成為相依項目，而讓服務彼此結合。</span><span class="sxs-lookup"><span data-stu-id="57515-208">Otherwise, the gateway becomes a dependency and can cause coupling between services.</span></span>

- <span data-ttu-id="57515-209">服務應該具有鬆散結合和高度功能一致性的特性。</span><span class="sxs-lookup"><span data-stu-id="57515-209">Services should have loose coupling and high functional cohesion.</span></span> <span data-ttu-id="57515-210">可能會一起變更的功能應該一起封裝和部署。</span><span class="sxs-lookup"><span data-stu-id="57515-210">Functions that are likely to change together should be packaged and deployed together.</span></span> <span data-ttu-id="57515-211">如果這些功能分居不同服務，它們最後會緊密結合，因為對某項服務進行變更就會需要更新其他服務。</span><span class="sxs-lookup"><span data-stu-id="57515-211">If they reside in separate services, those services end up being tightly coupled, because a change in one service will require updating the other service.</span></span> <span data-ttu-id="57515-212">兩個服務之間對話過度可能是緊密結合和低一致性的徵兆。</span><span class="sxs-lookup"><span data-stu-id="57515-212">Overly chatty communication between two services may be a symptom of tight coupling and low cohesion.</span></span> 

- <span data-ttu-id="57515-213">隔離失敗。</span><span class="sxs-lookup"><span data-stu-id="57515-213">Isolate failures.</span></span> <span data-ttu-id="57515-214">請使用復原策略來避免某個服務的失敗產生連鎖反應。</span><span class="sxs-lookup"><span data-stu-id="57515-214">Use resiliency strategies to prevent failures within a service from cascading.</span></span> <span data-ttu-id="57515-215">請參閱[恢復模式][resiliency-patterns]和[設計復原應用程式][resiliency-overview]。</span><span class="sxs-lookup"><span data-stu-id="57515-215">See [Resiliency patterns][resiliency-patterns] and [Designing resilient applications][resiliency-overview].</span></span>

## <a name="next-steps"></a><span data-ttu-id="57515-216">後續步驟</span><span class="sxs-lookup"><span data-stu-id="57515-216">Next steps</span></span>

<span data-ttu-id="57515-217">如需在 Azure 上建置微服務架構的詳細指引，請參閱[在 Azure 上設計、建置及操作微服務](../../microservices/index.md)。</span><span class="sxs-lookup"><span data-stu-id="57515-217">For detailed guidance about building a microservices architecture on Azure, see [Designing, building, and operating microservices on Azure](../../microservices/index.md).</span></span>


<!-- links -->

[resiliency-overview]: ../../resiliency/index.md
[resiliency-patterns]: ../../patterns/category/resiliency.md




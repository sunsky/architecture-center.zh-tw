---
title: CAF：建立混合式雲端一致性
description: 定義建立混合式雲端一致性的方法
author: BrianBlanchard
ms.date: 12/27/2018
ms.openlocfilehash: 726ea56f52d68f6c9b0d1478d19a91c2b7f12fdd
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901167"
---
# <a name="create-hybrid-cloud-consistency"></a><span data-ttu-id="77ca7-103">建立混合式雲端一致性</span><span class="sxs-lookup"><span data-stu-id="77ca7-103">Create hybrid cloud consistency</span></span>

<span data-ttu-id="77ca7-104">本文將引導您完成建立混合式雲端一致性的概略方法。</span><span class="sxs-lookup"><span data-stu-id="77ca7-104">This article guides you through the high level approaches for creating hybrid cloud consistency.</span></span>

<span data-ttu-id="77ca7-105">混合式部署模型在移轉期間可以降低風險，並提供順暢的基礎結構轉換。</span><span class="sxs-lookup"><span data-stu-id="77ca7-105">Hybrid deployment models during migration can reduce risk and contribute to a smooth infrastructure transition.</span></span> <span data-ttu-id="77ca7-106">在處理商務程序時，雲端平台可提供最高等級的彈性。</span><span class="sxs-lookup"><span data-stu-id="77ca7-106">Cloud platforms offer the greatest level of flexibility when it comes to business processes.</span></span> <span data-ttu-id="77ca7-107">許多組織對於移至雲端抱持著遲疑，而寧願保留最機密資料的完整控制權。</span><span class="sxs-lookup"><span data-stu-id="77ca7-107">Many organizations are hesitant to make the move to the cloud, preferring instead to keep full control over the most sensitive data.</span></span> <span data-ttu-id="77ca7-108">遺憾的是，內部部署伺服器不允許與雲端相同的創新速率。</span><span class="sxs-lookup"><span data-stu-id="77ca7-108">Unfortunately, on-premises servers don’t allow for the same rate of innovation as the cloud.</span></span> <span data-ttu-id="77ca7-109">混合式雲端解決方案可讓您兼得魚與熊掌：雲端創新的速度和安心的內部部署管理。</span><span class="sxs-lookup"><span data-stu-id="77ca7-109">A hybrid cloud solution allows you the best of both worlds: The speed of cloud innovation AND the comfort of on-premises management.</span></span>

## <a name="integrate-hybrid-cloud-consistency"></a><span data-ttu-id="77ca7-110">整合混合式雲端一致性</span><span class="sxs-lookup"><span data-stu-id="77ca7-110">Integrate hybrid cloud consistency</span></span>

<span data-ttu-id="77ca7-111">使用混合式雲端解決方案可讓組織調整運算資源。</span><span class="sxs-lookup"><span data-stu-id="77ca7-111">Using a hybrid cloud solution allows organizations to scale computing resources.</span></span> <span data-ttu-id="77ca7-112">它也消除為了處理短期需求高峰而支出大規模資本的需求。</span><span class="sxs-lookup"><span data-stu-id="77ca7-112">It also eliminates the need to make massive capital expenditures to handle short-term spikes in demand.</span></span> <span data-ttu-id="77ca7-113">當業務變更而需要針對更敏感的資料或應用程式釋出本機資源時，取消佈建雲端資源更輕鬆、更快速且成本較低。</span><span class="sxs-lookup"><span data-stu-id="77ca7-113">When changes to your business drive the need to free up local resources for more sensitive data or applications, it is easier, faster, and less expensive to deprovision cloud resources.</span></span> <span data-ttu-id="77ca7-114">您只需要為組織暫時使用的那些資源付費，而不需要購買及維護額外資源。</span><span class="sxs-lookup"><span data-stu-id="77ca7-114">You pay only for those resources your organization temporarily uses, instead of having to purchase and maintain additional resources.</span></span> <span data-ttu-id="77ca7-115">這樣可減少可能長時間維持在閒置狀態的設備數量。</span><span class="sxs-lookup"><span data-stu-id="77ca7-115">This reduces the amount of equipment that might remain idle over long periods of time.</span></span> <span data-ttu-id="77ca7-116">混合式雲端運算是「萬中選一」的平台，提供雲端運算的所有優勢：彈性、延展性及成本效益，並且把資料外洩的風險降到最低。</span><span class="sxs-lookup"><span data-stu-id="77ca7-116">Hybrid cloud computing is a “best of all possible worlds” platform, delivering all the benefits of cloud computing flexibility, scalability, and cost efficiencies; all with the lowest possible risk of data exposure.</span></span>

<span data-ttu-id="77ca7-117">![建立跨身分識別、管理、安全性、資料、開發和 DevOps 的混合式雲端一致性](../../_images/hybrid-consistency.png)
*圖 1.建立跨身分識別、管理、安全性、資料、開發和 DevOps 的混合式雲端一致性*</span><span class="sxs-lookup"><span data-stu-id="77ca7-117">![Creating hybrid cloud consistency across identity, management, security, data, development, and DevOps](../../_images/hybrid-consistency.png)
*Figure 1. Creating hybrid cloud consistency across identity, management, security, data, development, and DevOps*</span></span>

<span data-ttu-id="77ca7-118">真正的混合式雲端解決方案必須提供四個元件，其中每個都可帶來顯著的優勢，包括：</span><span class="sxs-lookup"><span data-stu-id="77ca7-118">A true hybrid cloud solution must provide four components, each of which brings significant benefits, including:</span></span>

- <span data-ttu-id="77ca7-119">適用於內部部署和雲端應用程式的通用身分識別：藉由提供使用者對其所有應用程式的單一登入 (SSO)，提升他們的生產力。</span><span class="sxs-lookup"><span data-stu-id="77ca7-119">Common identity for on-premises and cloud applications: This improves user productivity by giving users single sign-on (SSO) to all their applications.</span></span> <span data-ttu-id="77ca7-120">它也可確保應用程式和使用者跨網路/雲端界限的一致性。</span><span class="sxs-lookup"><span data-stu-id="77ca7-120">It also ensures consistency as applications and users cross network/cloud boundaries.</span></span>
- <span data-ttu-id="77ca7-121">跨混合式雲端的整合式管理和安全性：提供您一致的方式來監視、管理和保護環境，增加可見度和控制能力。</span><span class="sxs-lookup"><span data-stu-id="77ca7-121">Integrated management and security across your hybrid cloud: This provides you with a cohesive way to monitor, manage, and secure the environment, enabling increased visibility and control.</span></span>
- <span data-ttu-id="77ca7-122">資料中心和雲端一致的資料平台：這會建立資料可攜性，結合內部部署與雲端資料服務的順暢存取，提供所有資料來源的深入見解。</span><span class="sxs-lookup"><span data-stu-id="77ca7-122">A consistent data platform for the datacenter and the cloud: This creates data portability, combined with seamless access to on-premises and cloud data services for deep insight into all data sources.</span></span>
- <span data-ttu-id="77ca7-123">跨雲端與內部部署資料中心的統一部署和 DevOps：這可讓您視需要在兩個環境之間移動應用程式，提升開發人員的生產力，因為這兩個位置現在會有相同的開發環境。</span><span class="sxs-lookup"><span data-stu-id="77ca7-123">Unified development and DevOps across the cloud and on-premises datacenters: This allows you to move applications between the two environments as needed, improving developer productivity, as both places now have the same development environment.</span></span>
  
<span data-ttu-id="77ca7-124">從 Azure 的觀點來看，這些元件的範例包括：</span><span class="sxs-lookup"><span data-stu-id="77ca7-124">Examples of these components from an Azure perspective include:</span></span>

- <span data-ttu-id="77ca7-125">Azure Active Directory (Azure AD)，能夠搭配內部部署 Azure AD 運作，提供所有使用者通用的身分識別。</span><span class="sxs-lookup"><span data-stu-id="77ca7-125">Azure Active Directory (Azure AD), which works with on-premises Azure AD to provide common identity for all users.</span></span> <span data-ttu-id="77ca7-126">跨內部部署和透過雲端的 SSO 可讓使用者輕鬆安全地存取需要的應用程式與資產。</span><span class="sxs-lookup"><span data-stu-id="77ca7-126">SSO across on-premises and via the cloud makes it simple for users to safely access the applications and assets they need.</span></span> <span data-ttu-id="77ca7-127">系統管理員可以管理安全性和治理控制權，如此一來既有調整權限的彈性，讓使用者可存取所需項目，又不會影響使用者體驗。</span><span class="sxs-lookup"><span data-stu-id="77ca7-127">Adminis can manage security and governance controls so that users can access what they need, with flexibility to adjust those permissions without affecting the user experience.</span></span>
- <span data-ttu-id="77ca7-128">Azure 為雲端和內部部署基礎結構提供整合式管理和安全性服務，包括一組可用來監視、設定和保護混合式雲端的整合式工具。</span><span class="sxs-lookup"><span data-stu-id="77ca7-128">Azure provides integrated management and security services for both cloud and on-premises infrastructure that include an integrated set of tools for monitoring, configuring, and protecting hybrid clouds.</span></span> <span data-ttu-id="77ca7-129">這套全面性的管理方法，專門用來解決組織在考慮混合式雲端解決方案時所面臨的實際挑戰。</span><span class="sxs-lookup"><span data-stu-id="77ca7-129">This end-to-end approach to management specifically addresses real-world challenges facing organizations considering a hybrid cloud solution.</span></span>
- <span data-ttu-id="77ca7-130">Azure 混合式雲端提供通用工具，確保對所有資料的存取都是安全、順暢且有效率的。</span><span class="sxs-lookup"><span data-stu-id="77ca7-130">Azure hybrid cloud provides common tools that ensure secure access to all data, seamlessly and efficiently.</span></span> <span data-ttu-id="77ca7-131">Azure 資料服務結合 Microsoft SQL Server，以建立一致的資料平台。</span><span class="sxs-lookup"><span data-stu-id="77ca7-131">Azure data services combine with Microsoft SQL Server to create a consistent data platform.</span></span> <span data-ttu-id="77ca7-132">一致的混合式雲端模型可讓使用者處理作業和分析資料，針對資料倉儲、資料分析和資料視覺化，在內部部署和雲端中提供相同服務。</span><span class="sxs-lookup"><span data-stu-id="77ca7-132">A consistent hybrid cloud model allows users to work with both operational and analytical data, providing the same services on-premises and in the cloud for data warehousing, data analysis, and data visualization.</span></span>
- <span data-ttu-id="77ca7-133">Microsoft Azure 雲端服務結合 Microsoft Azure Stack 內部部署，提供統一的開發和 DevOps。</span><span class="sxs-lookup"><span data-stu-id="77ca7-133">Microsoft Azure cloud services, combined with Microsoft Azure Stack on-premises, provide unified development and DevOps.</span></span> <span data-ttu-id="77ca7-134">跨雲端和內部部署的一致性表示您的 DevOps 小組可以建置在任一環境中執行的應用程式，且可輕鬆地部署到正確位置。</span><span class="sxs-lookup"><span data-stu-id="77ca7-134">Consistency across the cloud and on-premises means that your DevOps team can build applications that run in either environment, and can easily deploy to the right location.</span></span> <span data-ttu-id="77ca7-135">您也可以跨混合式方案重複使用範本，進一步簡化 DevOps 程序。</span><span class="sxs-lookup"><span data-stu-id="77ca7-135">You can reuse templates across the hybrid solution as well, which can further simplify DevOps processes.</span></span>

## <a name="azure-stack-in-a-hybrid-cloud-environment"></a><span data-ttu-id="77ca7-136">混合式雲端環境中的 Azure Stack</span><span class="sxs-lookup"><span data-stu-id="77ca7-136">Azure Stack in a hybrid cloud environment</span></span>

<span data-ttu-id="77ca7-137">Microsoft Azure Stack 是混合式雲端解決方案，可讓組織在其資料中心執行一致的 Azure 服務，並提供與 Azure 公用雲端服務一致的簡化開發、管理和安全性體驗。</span><span class="sxs-lookup"><span data-stu-id="77ca7-137">Microsoft Azure Stack is a hybrid cloud solution that allows organizations to run Azure-consistent services in their datacenter, providing a simplified development, management, and security experience that is consistent with Azure public cloud services.</span></span> <span data-ttu-id="77ca7-138">Azure Stack 是 Azure 的延伸，讓您能夠從內部部署環境執行 Azure 服務，然後在需要的時候移至 Azure 雲端。</span><span class="sxs-lookup"><span data-stu-id="77ca7-138">Azure Stack is an extension of Azure, enabling you to run Azure services from your on-premises environments and then move to the Azure cloud if and when required.</span></span>

<span data-ttu-id="77ca7-139">Azure Stack 可讓您使用相同的工具部署和操作 IaaS 與 PaaS，並提供與 Azure 公用雲端相同的體驗。</span><span class="sxs-lookup"><span data-stu-id="77ca7-139">Azure Stack allows you to deploy and operate both IaaS and PaaS using the same tools and offering the same experience as the Azure public cloud.</span></span> <span data-ttu-id="77ca7-140">不論是透過 Web UI 入口網站或 PowerShell，Azure Stack 的管理對於使用 Azure 的 IT 系統管理員和終端使用者都有一致的外觀與操作。</span><span class="sxs-lookup"><span data-stu-id="77ca7-140">Management of Azure Stack, whether through the web UI portal or through PowerShell, has a consistent look and feel for IT administrators and end users with Azure.</span></span>

<span data-ttu-id="77ca7-141">Azure 和 Azure Stack 針對面向客戶與內部的企業營運應用程式，開拓全新的混合式使用案例，包括：</span><span class="sxs-lookup"><span data-stu-id="77ca7-141">Azure and Azure Stack unlock new hybrid use cases for both customer-facing and internal line-of-business applications, including:</span></span>

- <span data-ttu-id="77ca7-142">**邊緣和離線解決方案**。</span><span class="sxs-lookup"><span data-stu-id="77ca7-142">**Edge and disconnected solutions**.</span></span> <span data-ttu-id="77ca7-143">客戶可以在 Azure Stack 本機處理資料，然後在 Azure 中彙總以進一步分析，並在兩者之間使用通用應用程式邏輯，來解決延遲和連線需求。</span><span class="sxs-lookup"><span data-stu-id="77ca7-143">Customers can address latency and connectivity requirements by processing data locally in Azure Stack and then aggregating in Azure for further analytics, with common application logic across both.</span></span> <span data-ttu-id="77ca7-144">許多客戶對於此跨不同內容的邊緣案例很有興趣，包括工廠樓層管理、遊輪和礦井。</span><span class="sxs-lookup"><span data-stu-id="77ca7-144">Many customers are interested in this edge scenario across different contexts, including factory floors, cruise ships, and mine shafts.</span></span>
- <span data-ttu-id="77ca7-145">**符合各種法規的雲端應用程式**。</span><span class="sxs-lookup"><span data-stu-id="77ca7-145">**Cloud applications that meet various regulations**.</span></span> <span data-ttu-id="77ca7-146">客戶可在 Azure 中開發及部署應用程式，並有充分的彈性可在 Azure Stack 上的內部部署進行部署以符合法規或原則需求，完全不需要變更程式碼。</span><span class="sxs-lookup"><span data-stu-id="77ca7-146">Customers can develop and deploy applications in Azure, with full flexibility to deploy on-premises on Azure Stack to meet regulatory or policy requirements, with no code changes needed.</span></span> <span data-ttu-id="77ca7-147">說明應用程式範例包括全域稽核、財務報告、外匯交易、線上遊戲和費用報告。</span><span class="sxs-lookup"><span data-stu-id="77ca7-147">Illustrative application examples include global audit, financial reporting, foreign exchange trading, online gaming, and expense reporting.</span></span> <span data-ttu-id="77ca7-148">根據企業和技術需求，客戶有時候想要部署相同應用程式的不同執行個體到 Azure 或 Azure Stack。</span><span class="sxs-lookup"><span data-stu-id="77ca7-148">Customers are sometimes looking to deploy different instances of the same application to Azure or Azure Stack, based on business and technical requirements.</span></span> <span data-ttu-id="77ca7-149">Azure 可滿足大部分的需求，而 Azure Stack 可視需要補充部署方法。</span><span class="sxs-lookup"><span data-stu-id="77ca7-149">While Azure meets most requirements, Azure Stack complements the deployment approach where needed.</span></span>
- <span data-ttu-id="77ca7-150">**內部部署雲端應用程式模型**。</span><span class="sxs-lookup"><span data-stu-id="77ca7-150">**Cloud application model on-premises**.</span></span> <span data-ttu-id="77ca7-151">客戶可以使用 Azure Web 服務、容器、無伺服器和微服務架構來更新及延伸現有應用程式，或建置新的應用程式。</span><span class="sxs-lookup"><span data-stu-id="77ca7-151">Customers can use Azure web services, containers, serverless, and microservice architectures to update and extend existing applications or build new ones.</span></span> <span data-ttu-id="77ca7-152">您可以跨雲端中的 Azure 和內部部署的 Azure Stack，使用一致的 DevOps 程序。</span><span class="sxs-lookup"><span data-stu-id="77ca7-152">You can use consistent DevOps processes across Azure in the cloud and Azure Stack on-premises.</span></span> <span data-ttu-id="77ca7-153">大眾對於應用程式現代化 (包括核心任務關鍵性應用程式) 的興趣日漸增長。</span><span class="sxs-lookup"><span data-stu-id="77ca7-153">There is a growing interest in application modernization, including for core mission-critical applications.</span></span>

<span data-ttu-id="77ca7-154">Azure Stack 透過兩種部署選項來提供現代化：</span><span class="sxs-lookup"><span data-stu-id="77ca7-154">Azure Stack is offered via two deployment options:</span></span>

- <span data-ttu-id="77ca7-155">**Azure Stack 整合系統**。</span><span class="sxs-lookup"><span data-stu-id="77ca7-155">**Azure Stack integrated systems**.</span></span> <span data-ttu-id="77ca7-156">Azure Stack 整合系統是透過 Microsoft 與硬體合作夥伴的合作來提供的，提供既具有雲端步調的創新又兼顧管理簡易性的解決方案。</span><span class="sxs-lookup"><span data-stu-id="77ca7-156">Azure Stack integrated systems are offered through a partnership of Microsoft and hardware partners, creating a solution that provides cloud-paced innovation balanced with simplicity in management.</span></span> <span data-ttu-id="77ca7-157">由於是以硬體與軟體的整合系統形式來提供 Azure Stack，因此除了仍然會採用來自雲端的創新之外，您還有適度的彈性和控制。</span><span class="sxs-lookup"><span data-stu-id="77ca7-157">Because Azure Stack is offered as an integrated system of hardware and software, you get the right amount of flexibility and control, while still adopting innovation from the cloud.</span></span> <span data-ttu-id="77ca7-158">Azure Stack 整合系統的大小範圍為 4 到 12 個節點，並且由硬體合作夥伴與 Microsoft 共同支援。</span><span class="sxs-lookup"><span data-stu-id="77ca7-158">Azure Stack integrated systems range in size from 4–12 nodes and are jointly supported by the hardware partner and Microsoft.</span></span> <span data-ttu-id="77ca7-159">您可以使用 Azure Stack 整合系統，來為生產環境工作負載啟用新案例。</span><span class="sxs-lookup"><span data-stu-id="77ca7-159">Use Azure Stack integrated systems to enable new scenarios for your production workloads.</span></span>
- <span data-ttu-id="77ca7-160">**Azure Stack 開發套件**。</span><span class="sxs-lookup"><span data-stu-id="77ca7-160">**Azure Stack Development Kit**.</span></span> <span data-ttu-id="77ca7-161">「Microsoft Azure Stack 開發套件」是單一節點的 Azure Stack 部署，您可以用來評估和瞭解 Azure Stack。</span><span class="sxs-lookup"><span data-stu-id="77ca7-161">Microsoft Azure Stack Development Kit is a single-node deployment of Azure Stack, which you can use to evaluate and learn about Azure Stack.</span></span> <span data-ttu-id="77ca7-162">您也可以使用套件作為開發人員環境，可以在其中使用與 Azure 一致的 API 和工具進行開發。</span><span class="sxs-lookup"><span data-stu-id="77ca7-162">You can also use the kit as a developer environment, where you can develop using APIs and tooling that are consistent with Azure.</span></span> <span data-ttu-id="77ca7-163">「Azure Stack 開發套件」不應該用來作為生產環境。</span><span class="sxs-lookup"><span data-stu-id="77ca7-163">Azure Stack Development Kit is not intended to be used as a production environment.</span></span>

## <a name="azure-stack-one-cloud-ecosystem"></a><span data-ttu-id="77ca7-164">Azure Stack 單一雲端生態系統</span><span class="sxs-lookup"><span data-stu-id="77ca7-164">Azure Stack One Cloud Ecosystem</span></span>

<span data-ttu-id="77ca7-165">您可以利用完整的 Azure 生態系統來加速 Azure Stack 計畫：</span><span class="sxs-lookup"><span data-stu-id="77ca7-165">You can speed up Azure Stack initiatives by leveraging the complete Azure ecosystem:</span></span>

- <span data-ttu-id="77ca7-166">Azure 可確保大部分經過 Azure 認證的應用程式和服務將可在 Azure Stack 上運作。</span><span class="sxs-lookup"><span data-stu-id="77ca7-166">Azure ensures that most applications and services certified for Azure will work on Azure Stack.</span></span> <span data-ttu-id="77ca7-167">數個 ISV (包括 Bitnami、Docker、Kmep Technologies、Pivotal Cloud Foundry、Red Hat Enterprise Linux 和 SUSE Linux) 都將它們的解決方案延伸到 Azure Stack。</span><span class="sxs-lookup"><span data-stu-id="77ca7-167">Several ISVs &mdash; including Bitnami, Docker, Kemp Technologies, Pivotal Cloud Foundry, Red Hat Enterprise Linux, and SUSE Linux &mdash; are extending their solutions to Azure Stack.</span></span>
- <span data-ttu-id="77ca7-168">您可以選擇讓 Azure Stack 提供這些解決方案，並以完全受控服務的方式運作。</span><span class="sxs-lookup"><span data-stu-id="77ca7-168">You can opt to have Azure Stack delivered and operated as a fully managed service.</span></span> <span data-ttu-id="77ca7-169">數個合作夥伴(包括 Tieto、Yourhosting、Revera、Pulsant 和 NTT) 在近期將會有跨 Azure 與 Azure Stack 的受控服務供應項目。</span><span class="sxs-lookup"><span data-stu-id="77ca7-169">Several partners &mdash; including Tieto, Yourhosting, Revera, Pulsant, and NTT &mdash; will have managed service offerings across Azure and Azure Stack shortly.</span></span> <span data-ttu-id="77ca7-170">這些合作夥伴已透過雲端解決方案提供者 (雲端提供者) 方案獲得 Azure 提供的受控服務，而現在可擴充它們的供應項目以納入混合式解決方案。</span><span class="sxs-lookup"><span data-stu-id="77ca7-170">These partners have been delivering managed services for Azure via the Cloud Solution Provider (Cloud Providers) program and are now extending their offerings to include hybrid solutions.</span></span>
- <span data-ttu-id="77ca7-171">作為完整且完全受控的混合式雲端解決方案範例，Avanade 提供全方位的供應項目，其包括雲端轉換服務、軟體、基礎結構、設定和組態，以及持續的受控服務，讓現在的客戶可以像透過 Azure 作業一樣使用 Azure Stack。</span><span class="sxs-lookup"><span data-stu-id="77ca7-171">As an example of a complete, fully managed hybrid cloud solution, Avanade is delivering an all-in-one offer that includes cloud transformation services, software, infrastructure, setup and configuration, and ongoing managed services so customers can consume Azure Stack just as they do with Azure today.</span></span>
- <span data-ttu-id="77ca7-172">系統整合者 (SI) 可為客戶建置全面性的 Azure 解決方案，協助加速應用程式現代化計畫。</span><span class="sxs-lookup"><span data-stu-id="77ca7-172">Systems Integrators (SI) can help accelerate application modernization initiatives by building end-to-end Azure solutions for customers.</span></span> <span data-ttu-id="77ca7-173">他們能夠提供深度的 Azure 技能集、領域和產業知識，以及程序專業技術 (例如 DevOps)。</span><span class="sxs-lookup"><span data-stu-id="77ca7-173">They bring in-depth Azure skill sets, domain and industry knowledge, and process expertise (e.g., DevOps).</span></span> <span data-ttu-id="77ca7-174">每個 Azure Stack 雲端對於 SI 而言都是設計解決方案的機會，能夠引導並影響系統部署、自訂內含的功能，並傳遞操作活動。</span><span class="sxs-lookup"><span data-stu-id="77ca7-174">Every Azure Stack cloud is an opportunity for an SI to design the solution, lead and influence system deployment, customize the included capabilities, and deliver operational activities.</span></span> <span data-ttu-id="77ca7-175">這包括的 SI 例如 Avanade、DXC、Dell EMC Services、InFront Consulting Group、HPE Pointnext 和 Pricewaterhouse Coopers (PwC)。</span><span class="sxs-lookup"><span data-stu-id="77ca7-175">This includes SIs like Avanade, DXC, Dell EMC Services, InFront Consulting Group, HPE Pointnext, and Pricewaterhouse Coopers (PwC).</span></span>
---
title: 適用於預約旅館的交談聊天機器人
titleSuffix: Azure Example Scenarios
description: 使用 Azure Bot 服務建置適用於商務應用程式的對話聊天機器人。
author: iainfoulds
ms.date: 07/05/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
social_image_url: /azure/architecture/example-scenario/ai/media/architecture-commerce-chatbot.png
ms.openlocfilehash: 48f85e7443bcd6149c8024d20fb50816c1a4df38
ms.sourcegitcommit: 3b15d65e7c35a19506e562c444343f8467b6a073
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2019
ms.locfileid: "54908285"
---
# <a name="conversational-chatbot-for-hotel-reservations-on-azure"></a><span data-ttu-id="d7995-103">Azure 上適用於預約旅館的交談聊天機器人</span><span class="sxs-lookup"><span data-stu-id="d7995-103">Conversational chatbot for hotel reservations on Azure</span></span>

<span data-ttu-id="d7995-104">此範例案例適用於需要將交談聊天機器人整合到應用程式的企業。</span><span class="sxs-lookup"><span data-stu-id="d7995-104">This example scenario is applicable to businesses that need to integrate a conversational chatbot into applications.</span></span> <span data-ttu-id="d7995-105">在此案例中，C# 聊天機器人用於連鎖旅館，讓客戶可以透過 Web 或行動裝置應用程式檢查空房狀況及預訂住宿。</span><span class="sxs-lookup"><span data-stu-id="d7995-105">In this scenario, a C# chatbot is used for a hotel chain that allows customers to check availability and book accommodation through a web or mobile application.</span></span>

<span data-ttu-id="d7995-106">潛在的使用者包括為客戶提供檢視旅館空房及預訂房間、檢閱餐廳外帶菜單及訂購餐點，或者搜尋及訂購相片沖印的方式。</span><span class="sxs-lookup"><span data-stu-id="d7995-106">Potential uses include providing a way for customers to view hotel availability and book rooms, review a restaurant take-out menu and place a food order, or search for and order prints of photographs.</span></span> <span data-ttu-id="d7995-107">傳統上，企業可能需要雇用及訓練客戶服務代理商，以回應這些客戶要求，而且客戶在業務代表可以提供協助之前必須等待。</span><span class="sxs-lookup"><span data-stu-id="d7995-107">Traditionally, businesses would need to hire and train customer service agents to respond to these customer requests, and customers would have to wait until a representative is available to provide assistance.</span></span>

<span data-ttu-id="d7995-108">藉由使用 Azure 服務，例如 Bot 服務和 Language Understanding 或 Speech API 服務，公司可以透過自動化、可調整的聊天機器人，協助客戶及處理訂單或預約。</span><span class="sxs-lookup"><span data-stu-id="d7995-108">By using Azure services such as the Bot Service and Language Understanding or Speech API services, companies can assist customers and process orders or reservations with automated, scalable bots.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="d7995-109">相關使用案例</span><span class="sxs-lookup"><span data-stu-id="d7995-109">Relevant use cases</span></span>

<span data-ttu-id="d7995-110">其他相關的使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="d7995-110">Other relevant use cases include:</span></span>

- <span data-ttu-id="d7995-111">檢視餐廳外帶菜單及訂購餐點</span><span class="sxs-lookup"><span data-stu-id="d7995-111">Viewing a restaurant take-out menu and ordering food</span></span>
- <span data-ttu-id="d7995-112">檢查旅館空房狀況及預約房間</span><span class="sxs-lookup"><span data-stu-id="d7995-112">Checking hotel availability and reserving a room</span></span>
- <span data-ttu-id="d7995-113">搜尋可用的相片及訂購沖印</span><span class="sxs-lookup"><span data-stu-id="d7995-113">Searching available photos and ordering prints</span></span>

## <a name="architecture"></a><span data-ttu-id="d7995-114">架構</span><span class="sxs-lookup"><span data-stu-id="d7995-114">Architecture</span></span>

![交談聊天機器人所牽涉到 Azure 元件的架構概觀][architecture]

<span data-ttu-id="d7995-116">此案例涵蓋可以作為旅館禮賓接待的交談聊天機器人。</span><span class="sxs-lookup"><span data-stu-id="d7995-116">This scenario covers a conversational bot that functions as a concierge for a hotel.</span></span> <span data-ttu-id="d7995-117">整個案例的資料流程如下所示：</span><span class="sxs-lookup"><span data-stu-id="d7995-117">The data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="d7995-118">客戶使用行動裝置或 Web 應用程式存取聊天機器人。</span><span class="sxs-lookup"><span data-stu-id="d7995-118">The customer accesses the chatbot with a mobile or web app.</span></span>
2. <span data-ttu-id="d7995-119">使用 Azure Active Directory B2C (企業對客戶)，使用者經過驗證。</span><span class="sxs-lookup"><span data-stu-id="d7995-119">Using Azure Active Directory B2C (Business 2 Customer), the user is authenticated.</span></span>
3. <span data-ttu-id="d7995-120">與 Bot 服務互動，使用者要求旅館空房的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="d7995-120">Interacting with the Bot Service, the user requests information about hotel availability.</span></span>
4. <span data-ttu-id="d7995-121">認知服務會處理自然語言要求，以了解客戶通訊。</span><span class="sxs-lookup"><span data-stu-id="d7995-121">Cognitive Services processes the natural language request to understand the customer communication.</span></span>
5. <span data-ttu-id="d7995-122">若使用者滿意該結果，聊天機器人就會在 SQL Database 中新增或更新客戶的預約。</span><span class="sxs-lookup"><span data-stu-id="d7995-122">After the user is happy with the results, the bot adds or updates the customer’s reservation in a SQL Database.</span></span>
6. <span data-ttu-id="d7995-123">Application Insights 會收集整個程序的執行階段遙測，利用聊天機器人效能和使用量來協助 DevOps 小組。</span><span class="sxs-lookup"><span data-stu-id="d7995-123">Application Insights gathers runtime telemetry throughout the process to help the DevOps team with bot performance and usage.</span></span>

### <a name="components"></a><span data-ttu-id="d7995-124">元件</span><span class="sxs-lookup"><span data-stu-id="d7995-124">Components</span></span>

- <span data-ttu-id="d7995-125">[Azure Active Directory][aad-docs] 是 Microsoft 的多租用戶雲端式目錄和身分識別管理服務。</span><span class="sxs-lookup"><span data-stu-id="d7995-125">[Azure Active Directory][aad-docs] is Microsoft’s multi-tenant cloud-based directory and identity management service.</span></span> <span data-ttu-id="d7995-126">Azure AD 支援 B2C 連接器，可讓您使用外部識別碼 (例如 Google、Facebook 或 Microsoft 帳戶) 來識別個人。</span><span class="sxs-lookup"><span data-stu-id="d7995-126">Azure AD supports a B2C connector allowing you to identify individuals using external IDs such as Google, Facebook, or a Microsoft Account.</span></span>
- <span data-ttu-id="d7995-127">[App Service][appservice-docs] 可讓您以選定的程式設計語言來建置並裝載 Web 應用程式，無需管理基礎結構。</span><span class="sxs-lookup"><span data-stu-id="d7995-127">[App Service][appservice-docs] enables you to build and host web applications in the programming language of your choice without managing infrastructure.</span></span>
- <span data-ttu-id="d7995-128">[Bot 服務][botservice-docs] 提供工具，可以建置、測試、部署及管理智慧型聊天機器人。</span><span class="sxs-lookup"><span data-stu-id="d7995-128">[Bot Service][botservice-docs] provides tools to build, test, deploy, and manage intelligent bots.</span></span>
- <span data-ttu-id="d7995-129">[認知服務][cognitive-docs]可讓您使用智慧型演算法，透過自然的溝通方式，來查看、聆聽、述說、了解及詮釋您的使用者需求。</span><span class="sxs-lookup"><span data-stu-id="d7995-129">[Cognitive Services][cognitive-docs] lets you use intelligent algorithms to see, hear, speak, understand, and interpret your user needs through natural methods of communication.</span></span>
- <span data-ttu-id="d7995-130">[SQL Database][sqldatabase-docs] 是完全受控的關聯式雲端資料庫服務，它提供 SQL Server 引擎相容性。</span><span class="sxs-lookup"><span data-stu-id="d7995-130">[SQL Database][sqldatabase-docs] is a fully managed relational cloud database service that provides SQL Server engine compatibility.</span></span>
- <span data-ttu-id="d7995-131">[Application Insights][appinsights-docs] 是可擴充應用程式效能管理 (APM) 服務，讓您監視應用程式 (例如聊天機器人) 的效能。</span><span class="sxs-lookup"><span data-stu-id="d7995-131">[Application Insights][appinsights-docs] is an extensible Application Performance Management (APM) service that lets you monitor the performance of applications, such as your chatbot.</span></span>

### <a name="alternatives"></a><span data-ttu-id="d7995-132">替代項目</span><span class="sxs-lookup"><span data-stu-id="d7995-132">Alternatives</span></span>

- <span data-ttu-id="d7995-133">[Microsoft Speech API][speech-api] 可以用來變更客戶與聊天機器人的接觸方式。</span><span class="sxs-lookup"><span data-stu-id="d7995-133">[Microsoft Speech API][speech-api] can be used to change how customers interface with your bot.</span></span>
- <span data-ttu-id="d7995-134">[QnA Maker][qna-maker] 可以作為快速新增知識，從半結構化內容 (例如常見問題集) 新增至您的聊天機器人。</span><span class="sxs-lookup"><span data-stu-id="d7995-134">[QnA Maker][qna-maker] can be used as to quickly add knowledge to your bot from semi-structured content like an FAQ.</span></span>
- <span data-ttu-id="d7995-135">[翻譯工具文字][translator]是一種服務，可讓您輕易地將多語言支援新增至您的聊天機器人。</span><span class="sxs-lookup"><span data-stu-id="d7995-135">[Translator Text][translator] is a service that you might consider to easily add multi-lingual support to your bot.</span></span>

## <a name="considerations"></a><span data-ttu-id="d7995-136">考量</span><span class="sxs-lookup"><span data-stu-id="d7995-136">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="d7995-137">可用性</span><span class="sxs-lookup"><span data-stu-id="d7995-137">Availability</span></span>

<span data-ttu-id="d7995-138">此案例會使用 Azure SQL Database 來儲存客戶預約。</span><span class="sxs-lookup"><span data-stu-id="d7995-138">This scenario uses Azure SQL Database for storing customer reservations.</span></span> <span data-ttu-id="d7995-139">SQL Database 包括區域備援資料庫、容錯移轉群組和異地複寫。</span><span class="sxs-lookup"><span data-stu-id="d7995-139">SQL Database includes zone redundant databases, failover groups, and geo-replication.</span></span> <span data-ttu-id="d7995-140">如需詳細資訊，請參閱 [Azure SQL Database 可用性功能][sqlavailability-docs]。</span><span class="sxs-lookup"><span data-stu-id="d7995-140">For more information, see [Azure SQL Database availability capabilities][sqlavailability-docs].</span></span>

<span data-ttu-id="d7995-141">如需其他可用性主題，請參閱 Azure Architecture Center 中的[可用性檢查清單][availability]。</span><span class="sxs-lookup"><span data-stu-id="d7995-141">For other availability topics, see the [availability checklist][availability] in the Azure Architecture Center.</span></span>

### <a name="scalability"></a><span data-ttu-id="d7995-142">延展性</span><span class="sxs-lookup"><span data-stu-id="d7995-142">Scalability</span></span>

<span data-ttu-id="d7995-143">此案例會使用 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="d7995-143">This scenario uses Azure App Service.</span></span> <span data-ttu-id="d7995-144">您可以使用 App Service，自動調整執行聊天機器人的執行個體數目。</span><span class="sxs-lookup"><span data-stu-id="d7995-144">With App Service, you can automatically scale the number of instances that run your bot.</span></span> <span data-ttu-id="d7995-145">這項功能可讓您的 Web 應用程式和聊天機器人掌握客戶需求。</span><span class="sxs-lookup"><span data-stu-id="d7995-145">This functionality lets you keep up with customer demand for your web application and chatbot.</span></span> <span data-ttu-id="d7995-146">如需自動調整規模的詳細資訊，請參閱 Azure 架構中心的[自動調整規模最佳做法][autoscaling]。</span><span class="sxs-lookup"><span data-stu-id="d7995-146">For more information on autoscale, see [Autoscaling best practices][autoscaling] in the Azure Architecture Center.</span></span>

<span data-ttu-id="d7995-147">如需其他延展性主題，請參閱 Azure Architecture Center 中的[延展性檢查清單][scalability]。</span><span class="sxs-lookup"><span data-stu-id="d7995-147">For other scalability topics, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="d7995-148">安全性</span><span class="sxs-lookup"><span data-stu-id="d7995-148">Security</span></span>

<span data-ttu-id="d7995-149">此案例會使用 Azure Active Directory B2C (企業對客戶) 來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="d7995-149">This scenario uses Azure Active Directory B2C (Business 2 Consumer) to authenticate users.</span></span> <span data-ttu-id="d7995-150">使用 AAD B2C，您的聊天機器人不會儲存任何機密客戶帳戶資訊或認證。</span><span class="sxs-lookup"><span data-stu-id="d7995-150">With AAD B2C, your chatbot doesn't store any sensitive customer account information or credentials.</span></span> <span data-ttu-id="d7995-151">如需詳細資訊，請參閱 [Azure Active Directory B2C 概觀][aadb2c-docs]。</span><span class="sxs-lookup"><span data-stu-id="d7995-151">For more information, see [Azure Active Directory B2C overview][aadb2c-docs].</span></span>

<span data-ttu-id="d7995-152">儲存在 Azure SQL Database 中的資訊，會以透明資料加密 (TDE) 進行加密並待用。</span><span class="sxs-lookup"><span data-stu-id="d7995-152">Information stored in Azure SQL Database is encrypted at rest with transparent data encryption (TDE).</span></span> <span data-ttu-id="d7995-153">SQL Database 也提供 Always Encrypted，這項功能會在查詢和處理期間將資料加密。</span><span class="sxs-lookup"><span data-stu-id="d7995-153">SQL Database also offers Always Encrypted which encrypts data during querying and processing.</span></span> <span data-ttu-id="d7995-154">如需 SQL Database 安全性的詳細資訊，請參閱 [Azure SQL Database 安全性與合規性][sqlsecurity-docs]。</span><span class="sxs-lookup"><span data-stu-id="d7995-154">For more information on SQL Database security, see [Azure SQL Database security and compliance][sqlsecurity-docs].</span></span>

<span data-ttu-id="d7995-155">如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。</span><span class="sxs-lookup"><span data-stu-id="d7995-155">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="d7995-156">災害復原</span><span class="sxs-lookup"><span data-stu-id="d7995-156">Resiliency</span></span>

<span data-ttu-id="d7995-157">此案例會使用 Azure SQL Database 來儲存客戶預約。</span><span class="sxs-lookup"><span data-stu-id="d7995-157">This scenario uses Azure SQL Database for storing customer reservations.</span></span> <span data-ttu-id="d7995-158">SQL Database 包括區域備援資料庫、容錯移轉群組、異地複寫和自動備份。</span><span class="sxs-lookup"><span data-stu-id="d7995-158">SQL Database includes zone redundant databases, failover groups, geo-replication, and automatic backups.</span></span> <span data-ttu-id="d7995-159">這些功能可讓您的應用程式在發生維護事件或中斷時繼續執行。</span><span class="sxs-lookup"><span data-stu-id="d7995-159">These features allow your application to continue running if there is a maintenance event or outage.</span></span> <span data-ttu-id="d7995-160">如需詳細資訊，請參閱 [Azure SQL Database 可用性功能][sqlavailability-docs]。</span><span class="sxs-lookup"><span data-stu-id="d7995-160">For more information, see [Azure SQL Database availability capabilities][sqlavailability-docs].</span></span>

<span data-ttu-id="d7995-161">為了監視應用程式的健康情況，此案例使用 Application Insights。</span><span class="sxs-lookup"><span data-stu-id="d7995-161">To monitor the health of your application, this scenario uses Application Insights.</span></span> <span data-ttu-id="d7995-162">使用 Application Insights，您可以產生警示，並且針對會影響客戶體驗和聊天機器人可用性的效能問題做回應。</span><span class="sxs-lookup"><span data-stu-id="d7995-162">With Application Insights, you can generate alerts and respond to performance issues that would impact the customer experience and availability of the chatbot.</span></span> <span data-ttu-id="d7995-163">如需詳細資訊，請參閱 [Application Insights 是什麼？][appinsights-docs]</span><span class="sxs-lookup"><span data-stu-id="d7995-163">For more information, see [What is Application Insights?][appinsights-docs]</span></span>

<span data-ttu-id="d7995-164">如需設計彈性解決方案的一般指引，請參閱[為 Azure 設計有彈性的應用程式][resiliency]。</span><span class="sxs-lookup"><span data-stu-id="d7995-164">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="d7995-165">部署案例</span><span class="sxs-lookup"><span data-stu-id="d7995-165">Deploy the scenario</span></span>

<span data-ttu-id="d7995-166">此案例可分為三個元件，讓您可以探索最著重的部分：</span><span class="sxs-lookup"><span data-stu-id="d7995-166">This scenario is divided into three components for you to explore areas that you are most focused on:</span></span>

- <span data-ttu-id="d7995-167">[基礎結構元件](#deploy-infrastructure-components)。</span><span class="sxs-lookup"><span data-stu-id="d7995-167">[Infrastructure components](#deploy-infrastructure-components).</span></span> <span data-ttu-id="d7995-168">使用 Azure Resource Manger 範本來部署 App Service、Web 應用程式、Application Insights、儲存體帳戶及 SQL Server 和資料庫的核心基礎結構元件。</span><span class="sxs-lookup"><span data-stu-id="d7995-168">Use an Azure Resource Manger template to deploy the core infrastructure components of an App Service, Web App, Application Insights, Storage account, and SQL Server and database.</span></span>
- <span data-ttu-id="d7995-169">[Web 應用程式聊天機器人](#deploy-web-app-chatbot)。</span><span class="sxs-lookup"><span data-stu-id="d7995-169">[Web App Chatbot](#deploy-web-app-chatbot).</span></span> <span data-ttu-id="d7995-170">使用 Azure CLI 來部署具有 Bot 服務和 Language Understanding and Intelligent Services (LUIS) 應用程式的聊天機器人。</span><span class="sxs-lookup"><span data-stu-id="d7995-170">Use the Azure CLI to deploy a bot with the Bot Service and Language Understanding and Intelligent Services (LUIS) app.</span></span>
- <span data-ttu-id="d7995-171">[範例 C# 聊天機器人應用程式](#deploy-chatbot-c-application-code)。</span><span class="sxs-lookup"><span data-stu-id="d7995-171">[Sample C# chatbot application](#deploy-chatbot-c-application-code).</span></span> <span data-ttu-id="d7995-172">使用 Visual Studio 來檢閱範例旅館預訂 C# 應用程式程式碼，並且部署到 Azure 中的聊天機器人。</span><span class="sxs-lookup"><span data-stu-id="d7995-172">Use Visual Studio to review the sample hotel reservation C# application code and deploy to a bot in Azure.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="d7995-173">必要條件</span><span class="sxs-lookup"><span data-stu-id="d7995-173">Prerequisites</span></span>

<span data-ttu-id="d7995-174">您必須具有現有的 Azure 帳戶。</span><span class="sxs-lookup"><span data-stu-id="d7995-174">You must have an existing Azure account.</span></span> <span data-ttu-id="d7995-175">如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。</span><span class="sxs-lookup"><span data-stu-id="d7995-175">If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.</span></span>

### <a name="walk-through"></a><span data-ttu-id="d7995-176">逐步解說</span><span class="sxs-lookup"><span data-stu-id="d7995-176">Walk-through</span></span>

<span data-ttu-id="d7995-177">若要使用 Resource Manager 範本部署基礎結構元件，請執行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="d7995-177">To deploy the infrastructure components with a Resource Manager template, perform the following steps.</span></span>

<!-- markdownlint-disable MD033 -->

1. <span data-ttu-id="d7995-178">按一下 [部署至 Azure] 按鈕：</span><span class="sxs-lookup"><span data-stu-id="d7995-178">Click the **Deploy to Azure** button:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fapps%2Fcommerce-chatbot.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. <span data-ttu-id="d7995-179">等待 Azure 入口網站中的範本部署開啟，然後完成下列步驟：</span><span class="sxs-lookup"><span data-stu-id="d7995-179">Wait for the template deployment to open in the Azure portal, then complete the following steps:</span></span>
   - <span data-ttu-id="d7995-180">選擇 [新建] 資源群組，然後在文字方塊中提供名稱，例如 myCommerceChatBotInfrastructure。</span><span class="sxs-lookup"><span data-stu-id="d7995-180">Choose to **Create new** resource group, then provide a name such as *myCommerceChatBotInfrastructure* in the text box.</span></span>
   - <span data-ttu-id="d7995-181">從 [位置] 下拉式方塊選取區域。</span><span class="sxs-lookup"><span data-stu-id="d7995-181">Select a region from the **Location** drop-down box.</span></span>
   - <span data-ttu-id="d7995-182">為 SQL Server 系統管理員帳戶提供使用者名稱和安全的密碼。</span><span class="sxs-lookup"><span data-stu-id="d7995-182">Provide a username and secure password for the SQL Server administrator account.</span></span>
   - <span data-ttu-id="d7995-183">檢閱條款及條件，然後按一下 [我同意上方所述的條款及條件]。</span><span class="sxs-lookup"><span data-stu-id="d7995-183">Review the terms and conditions, then check **I agree to the terms and conditions stated above**.</span></span>
   - <span data-ttu-id="d7995-184">選取 [購買] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="d7995-184">Select the **Purchase** button.</span></span>

<!-- markdownlint-enable MD033 -->

<span data-ttu-id="d7995-185">需要幾分鐘的時間才能完成部署。</span><span class="sxs-lookup"><span data-stu-id="d7995-185">It takes a few minutes for the deployment to complete.</span></span>

### <a name="deploy-web-app-chatbot"></a><span data-ttu-id="d7995-186">部署 Web 應用程式聊天機器人</span><span class="sxs-lookup"><span data-stu-id="d7995-186">Deploy Web App chatbot</span></span>

<span data-ttu-id="d7995-187">若要建立聊天機器人，請使用 Azure CLI。</span><span class="sxs-lookup"><span data-stu-id="d7995-187">To create the chatbot, use the Azure CLI.</span></span> <span data-ttu-id="d7995-188">下列範例會安裝 Bot 服務的 CLI 擴充、建立資源群組，然後部署會使用 Application Insights 的聊天機器人。</span><span class="sxs-lookup"><span data-stu-id="d7995-188">The following example installs the CLI extension for Bot Service, creates a resource group, then deploys a bot that uses Application Insights.</span></span> <span data-ttu-id="d7995-189">出現提示時，驗證您的 Microsoft 帳戶，並且允許聊天機器人能夠自行向 Bot 服務和 Language Understanding and Intelligent Services (LUIS) 應用程式註冊。</span><span class="sxs-lookup"><span data-stu-id="d7995-189">When prompted, authenticate your Microsoft account and allow the bot to register itself with the Bot Service and Language Understanding and Intelligent Services (LUIS) app.</span></span>

```azurecli-interactive
# Install the Azure CLI extension for the Bot Service
az extension add --name botservice --yes

# Create a resource group
az group create --name myCommerceChatbot --location eastus

# Create a Web App Chatbot that uses Application Insights
az bot create \
    --resource-group myCommerceChatbot \
    --name commerceChatbot \
    --location eastus \
    --kind webapp \
    --sku S1 \
    --insights eastus
```

### <a name="deploy-chatbot-c-application-code"></a><span data-ttu-id="d7995-190">部署聊天機器人 C# 應用程式程式碼</span><span class="sxs-lookup"><span data-stu-id="d7995-190">Deploy chatbot C# application code</span></span>

<span data-ttu-id="d7995-191">範例 C# 應用程式可以在 GitHub 上取得：</span><span class="sxs-lookup"><span data-stu-id="d7995-191">A sample C# application is available on GitHub:</span></span>

- [<span data-ttu-id="d7995-192">商務聊天機器人 C# 範例</span><span class="sxs-lookup"><span data-stu-id="d7995-192">Commerce Bot C# sample</span></span>](https://github.com/Microsoft/AzureBotServices-scenarios/tree/master/CSharp/Commerce/src)

<span data-ttu-id="d7995-193">範例應用程式包含 Azure Active Directory 驗證元件，並且與認知服務的 Language Understanding and Intelligent Services (LUIS) 元件整合。</span><span class="sxs-lookup"><span data-stu-id="d7995-193">The sample application includes the Azure Active Directory authentication components and integration with the Language Understanding and Intelligent Services (LUIS) component of Cognitive Services.</span></span> <span data-ttu-id="d7995-194">應用程式需要 Visual Studio 以便建置及部署案例。</span><span class="sxs-lookup"><span data-stu-id="d7995-194">The application requires Visual Studio to build and deploy the scenario.</span></span> <span data-ttu-id="d7995-195">關於設定 AAD B2C 和 LUIS 應用程式的額外資訊，可以在 GitHub 存放庫文件中找到。</span><span class="sxs-lookup"><span data-stu-id="d7995-195">Additional information on configuring AAD B2C and the LUIS app can be found in the GitHub repo documentation.</span></span>

## <a name="pricing"></a><span data-ttu-id="d7995-196">價格</span><span class="sxs-lookup"><span data-stu-id="d7995-196">Pricing</span></span>

<span data-ttu-id="d7995-197">為了探索執行此案例的成本，所有服務會在成本計算機中預先設定。</span><span class="sxs-lookup"><span data-stu-id="d7995-197">To explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="d7995-198">若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的流量。</span><span class="sxs-lookup"><span data-stu-id="d7995-198">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="d7995-199">我們根據您期望聊天機器人處理的訊息數，提供了 3 個範例成本設定檔：</span><span class="sxs-lookup"><span data-stu-id="d7995-199">We have provided three sample cost profiles based on the number of messages you expect your chatbot to process:</span></span>

- <span data-ttu-id="d7995-200">[小型][small-pricing]：這個定價範例適用於每月處理低於 10,000 個映像。</span><span class="sxs-lookup"><span data-stu-id="d7995-200">[Small][small-pricing]: this pricing example correlates to processing < 10,000 messages per month.</span></span>
- <span data-ttu-id="d7995-201">[中型][medium-pricing]：這個定價範例適用於每月處理低於 500,000 個映像。</span><span class="sxs-lookup"><span data-stu-id="d7995-201">[Medium][medium-pricing]: this pricing example correlates to processing < 500,000 messages per month.</span></span>
- <span data-ttu-id="d7995-202">[大型][large-pricing]：這個定價範例適用於每月處理低於 1,000 萬個映像。</span><span class="sxs-lookup"><span data-stu-id="d7995-202">[Large][large-pricing]: this pricing example correlates to processing < 10 million messages per month.</span></span>

## <a name="related-resources"></a><span data-ttu-id="d7995-203">相關資源</span><span class="sxs-lookup"><span data-stu-id="d7995-203">Related resources</span></span>

<span data-ttu-id="d7995-204">如需 Azure Bot 服務的一套引導式教學課程，請參閱文件的[教學課程區段][botservice-docs]。</span><span class="sxs-lookup"><span data-stu-id="d7995-204">For a set of guided tutorials for the Azure Bot Service, see the [tutorial section][botservice-docs] of the documentation.</span></span>

<!-- links -->

[aadb2c-docs]: /azure/active-directory-b2c/active-directory-b2c-overview
[aad-docs]: /azure/active-directory/
[appinsights-docs]: /azure/application-insights/app-insights-overview
[appservice-docs]: /azure/app-service/
[architecture]: ./media/architecture-commerce-chatbot.png
[autoscaling]: ../../best-practices/auto-scaling.md
[availability]: ../../checklist/availability.md
[botservice-docs]: /azure/bot-service/
[cognitive-docs]: /azure/cognitive-services/
[resiliency]: ../../resiliency/index.md
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[security]: /azure/security/
[scalability]: ../../checklist/scalability.md
[sqlavailability-docs]: /azure/sql-database/sql-database-technical-overview#availability-capabilities
[sqldatabase-docs]: /azure/sql-database/
[sqlsecurity-docs]: /azure/sql-database/sql-database-technical-overview#advanced-security-and-compliance
[qna-maker]: /azure/cognitive-services/QnAMaker/Overview/overview
[speech-api]: /azure/cognitive-services/speech/home
[translator]: /azure/cognitive-services/translator/translator-info-overview

[small-pricing]: https://azure.com/e/dce05b6184904c50b38e1a8654f726b6
[medium-pricing]: https://azure.com/e/304d17106afc480dbc414f9726078a03
[large-pricing]: https://azure.com/e/8319dd5e5e3d4f118f9029e32a80e887
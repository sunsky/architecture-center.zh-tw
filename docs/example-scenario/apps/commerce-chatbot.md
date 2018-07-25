---
title: Azure 上適用於預約旅館的交談聊天機器人
description: 經過證明的案例，可以使用 Azure Bot 服務、認知服務和 LUIS、Azure SQL Database 及 Application Insights，為商務應用程式建置交談聊天機器人。
author: iainfoulds
ms.date: 07/05/2018
ms.openlocfilehash: b664faf20d806824c2581346aaa592b0d74207da
ms.sourcegitcommit: 71cbef121c40ef36e2d6e3a088cb85c4260599b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060858"
---
# <a name="conversational-chatbot-for-hotel-reservations-on-azure"></a>Azure 上適用於預約旅館的交談聊天機器人

此範例案例適用於需要將交談聊天機器人整合到應用程式的企業。 在此案例中，C# 聊天機器人用於連鎖旅館，讓客戶可以透過 Web 或行動裝置應用程式檢查空房狀況及預訂住宿。

案例範例包括為客戶提供檢視旅館空房及預訂房間、檢閱餐廳外帶菜單及訂購餐點，或者搜尋及訂購相片沖印的方式。 傳統上，企業可能需要雇用及訓練客戶服務代理商，以回應這些客戶要求，而且客戶在業務代表可以提供協助之前必須等待。

藉由使用 Azure 服務，例如 Bot 服務和 Language Understanding 或 Speech API 服務，公司可以透過自動化、可調整的聊天機器人，協助客戶及處理訂單或預約。

## <a name="related-use-cases"></a>相關使用案例

請針對下列使用案例考慮此案例：

* 檢視餐廳外帶菜單及訂購餐點
* 檢查旅館空房狀況及預約房間
* 搜尋可用的相片及訂購沖印

## <a name="architecture"></a>架構

![交談聊天機器人所牽涉到 Azure 元件的架構概觀][architecture]

此案例涵蓋可以作為旅館禮賓接待的交談聊天機器人。 整個案例的資料流程如下所示：

1. 客戶使用行動裝置或 Web 應用程式存取聊天機器人。
2. 使用 Azure Active Directory B2C (企業對客戶)，使用者經過驗證。
3. 與 Bot 服務互動，使用者要求旅館空房的相關資訊。
4. 認知服務會處理自然語言要求，以了解客戶通訊。
5. 若使用者滿意該結果，聊天機器人就會在 SQL Database 中新增或更新客戶的預約。
6. Application Insights 會收集整個程序的執行階段遙測，利用聊天機器人效能和使用量來協助 DevOps 小組。

### <a name="components"></a>元件

* [Azure Active Directory][aad-docs] 是 Microsoft 的多租用戶雲端式目錄和身分識別管理服務。 Azure AD 支援 B2C 連接器，可讓您使用外部識別碼 (例如 Google、Facebook 或 Microsoft 帳戶) 來識別個人。
* [App Service][appservice-docs] 可讓您以選定的程式設計語言來建置並裝載 Web 應用程式，無需管理基礎結構。
* [Bot 服務][botservice-docs] 提供工具，可以建置、測試、部署及管理智慧型聊天機器人。
* [認知服務][cognitive-docs]可讓您使用智慧型演算法，透過自然的溝通方式，來查看、聆聽、述說、了解及詮釋您的使用者需求。
* [SQL Database][sqldatabase-docs] 是完全受控的關聯式雲端資料庫服務，它提供 SQL Server 引擎相容性。
* [Application Insights][appinsights-docs] 是可擴充應用程式效能管理 (APM) 服務，讓您監視應用程式 (例如聊天機器人) 的效能。

### <a name="alternatives"></a>替代項目

* [Microsoft Speech API][speech-api] 可以用來變更客戶與聊天機器人的接觸方式。
* [QnA Maker][qna-maker] 可以作為快速新增知識，從半結構化內容 (例如常見問題集) 新增至您的聊天機器人。
* [翻譯工具文字][translator]是一種服務，可讓您輕易地將多語言支援新增至您的聊天機器人。

## <a name="considerations"></a>考量

### <a name="availability"></a>可用性

此案例會使用 Azure SQL Database 來儲存客戶預約。 SQL Database 包括區域備援資料庫、容錯移轉群組和異地複寫。 如需詳細資訊，請參閱 [Azure SQL Database 可用性功能][sqlavailability-docs]。

如需其他可用性主題，請參閱 Azure Architecture Center 中的[可用性檢查清單][availability]。

### <a name="scalability"></a>延展性

此案例會使用 Azure App Service。 您可以使用 App Service，自動調整執行聊天機器人的執行個體數目。 這項功能可讓您的 Web 應用程式和聊天機器人掌握客戶需求。 如需自動調整規模的詳細資訊，請參閱架構中心的[自動調整規模最佳做法][autoscaling]。

如需其他延展性主題，請參閱 Azure Architecture Center 中的[延展性檢查清單][scalability]。

### <a name="security"></a>安全性

此案例會使用 Azure Active Directory B2C (企業對客戶) 來驗證使用者。 使用 AAD B2C，您的聊天機器人不會儲存任何機密客戶帳戶資訊或認證。 如需詳細資訊，請參閱 [Azure Active Directory B2C 概觀][aadb2c-docs]。

儲存在 Azure SQL Database 中的資訊，會以透明資料加密 (TDE) 進行加密並待用。 SQL Database 也提供 Always Encrypted，這項功能會在查詢和處理期間將資料加密。 如需 SQL Database 安全性的詳細資訊，請參閱 [Azure SQL Database 安全性與合規性][sqlsecurity-docs]。

如需設計安全解決方案的一般指引，請參閱 [Azure 安全性文件][security]。

### <a name="resiliency"></a>災害復原

此案例會使用 Azure SQL Database 來儲存客戶預約。 SQL Database 包括區域備援資料庫、容錯移轉群組、異地複寫和自動備份。 這些功能可讓您的應用程式在發生維護事件或中斷時繼續執行。 如需詳細資訊，請參閱 [Azure SQL Database 可用性功能][sqlavailability-docs]。

為了監視應用程式的健康情況，此案例使用 Application Insights。 使用 Application Insights，您可以產生警示，並且針對會影響客戶體驗和聊天機器人可用性的效能問題做回應。 如需詳細資訊，請參閱 [Application Insights 是什麼？][appinsights-docs]

如需設計彈性解決方案的一般指引，請參閱[為 Azure 設計有彈性的應用程式][resiliency]。

## <a name="deploy-the-scenario"></a>部署案例

此案例可分為三個元件，讓您可以探索最著重的部分：

* [基礎結構元件](#deploy-infrastructure-components)。 使用 Azure Resource Manger 範本來部署 App Service、Web 應用程式、Application Insights、儲存體帳戶及 SQL Server 和資料庫的核心基礎結構元件。
* [Web 應用程式聊天機器人](#deploy-web-app-chatbot)。 使用 Azure CLI 來部署具有 Bot 服務和 Language Understanding and Intelligent Services (LUIS) 應用程式的聊天機器人。
* [範例 C# 聊天機器人應用程式](#deploy-chatbot-c-application-code)。 使用 Visual Studio 來檢閱範例旅館預訂 C# 應用程式程式碼，並且部署到 Azure 中的聊天機器人。

**必要條件。** 您必須具有現有的 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

### <a name="deploy-infrastructure-components"></a>部署基礎結構元件

若要使用 Azure Resource Manager 範本部署基礎結構元件，請執行下列步驟。

1. 按一下 [部署至 Azure] 按鈕：<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fapps%2Fcommerce-chatbot.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. 等待 Azure 入口網站中的範本部署開啟，然後完成下列步驟：
   * 選擇 [新建] 資源群組，然後在文字方塊中提供名稱，例如 myCommerceChatBotInfrastructure。
   * 從 [位置] 下拉式方塊選取區域。
   * 為 SQL Server 系統管理員帳戶提供使用者名稱和安全的密碼。
   * 檢閱條款及條件，然後按一下 [我同意上方所述的條款及條件]。
   * 選取 [購買] 按鈕。

需要幾分鐘的時間才能完成部署。

### <a name="deploy-web-app-chatbot"></a>部署 Web 應用程式聊天機器人

若要建立聊天機器人，請使用 Azure CLI。 下列範例會安裝 Bot 服務的 CLI 擴充、建立資源群組，然後部署會使用 Application Insights 的聊天機器人。 出現提示時，驗證您的 Microsoft 帳戶，並且允許聊天機器人能夠自行向 Bot 服務和 Language Understanding and Intelligent Services (LUIS) 應用程式註冊。

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

### <a name="deploy-chatbot-c-application-code"></a>部署聊天機器人 C# 應用程式程式碼

範例 C# 應用程式可以在 GitHub 上取得： 

* [商務聊天機器人 C# 範例](https://github.com/Microsoft/AzureBotServices-scenarios/tree/master/CSharp/Commerce/src)

範例應用程式包含 Azure Active Directory 驗證元件，並且與認知服務的 Language Understanding and Intelligent Services (LUIS) 元件整合。 應用程式需要 Visual Studio 以便建置及部署案例。 關於設定 AAD B2C 和 LUIS 應用程式的額外資訊，可以在 GitHub 存放庫文件中找到。

## <a name="pricing"></a>價格

為了探索執行此案例的成本，所有服務會在成本計算機中預先設定。 若要查看價格如何針對您的特定使用案例而變更，請變更適當的變數，以符合您預期的流量。

我們根據您期望聊天機器人處理的訊息量，提供了 3 個範例成本設定檔：

* [小型][small-pricing]：這個設定檔適用於每月處理 < 10000 則訊息。
* [中型][medium-pricing]：這個設定檔適用於每月處理 < 500000 則訊息。
* [大型][large-pricing]：這個設定檔適用於每月處理 < 1000 萬則訊息。

## <a name="related-resources"></a>相關資源

如需關於利用 Azure Bot 服務的一套引導式教學課程，請參閱文件的[教學課程節點][botservice-docs]。

<!-- links -->
[aadb2c-docs]: /azure/active-directory-b2c/active-directory-b2c-overview
[aad-docs]: /azure/active-directory/
[appinsights-docs]: /azure/application-insights/app-insights-overview
[appservice-docs]: /azure/app-service/
[architecture]: ./media/commerce-chatbot/architecture-commerce-chatbot.png
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
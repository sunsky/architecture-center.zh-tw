---
title: CAF：身分識別基準動機和業務風險
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 身分識別基準動機和業務風險
author: BrianBlanchard
ms.openlocfilehash: ef831d1b3b01251b27f1f7b18e551c49996a2468
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58246519"
---
# <a name="identity-baseline-motivations-and-business-risks"></a><span data-ttu-id="72562-103">身分識別基準動機和業務風險</span><span class="sxs-lookup"><span data-stu-id="72562-103">Identity Baseline motivations and business risks</span></span>

<span data-ttu-id="72562-104">本文討論客戶通常會在雲端治理策略中採用身分識別基準專業領域的原因。</span><span class="sxs-lookup"><span data-stu-id="72562-104">This article discusses the reasons that customers typically adopt an Identity Baseline discipline within a cloud governance strategy.</span></span> <span data-ttu-id="72562-105">它也提供幾個驅使原則聲明之業務風險的範例。</span><span class="sxs-lookup"><span data-stu-id="72562-105">It also provides a few examples of business risks that drive policy statements.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="is-identity-baseline-relevant"></a><span data-ttu-id="72562-106">身分識別基準是否相關？</span><span class="sxs-lookup"><span data-stu-id="72562-106">Is Identity Baseline relevant?</span></span>

<span data-ttu-id="72562-107">傳統內部部署目錄的設計目的是讓企業能夠嚴格控制其內部網路和資料中心內使用者、群組及角色的權限和原則。</span><span class="sxs-lookup"><span data-stu-id="72562-107">Traditional on-premises directories are designed to allow businesses to strictly control permissions and policies for users, groups, and roles within their internal networks and datacenters.</span></span> <span data-ttu-id="72562-108">這通常是為了支援單一租用戶實作，讓服務僅能在內部部署環境內使用。</span><span class="sxs-lookup"><span data-stu-id="72562-108">This is usually intended to support single tenant implementations, with services applicable only within the on-premises environment.</span></span>

<span data-ttu-id="72562-109">雲端身分識別服務旨在將組織的驗證和存取控制功能展開至網際網路。</span><span class="sxs-lookup"><span data-stu-id="72562-109">Cloud identity services are intended to expand an organization's authentication and access control capabilities to the internet.</span></span> <span data-ttu-id="72562-110">它們支援多租用戶，且可用來管理雲端應用程式和部署之間的使用者和存取原則。</span><span class="sxs-lookup"><span data-stu-id="72562-110">They support multi-tenancy and can be used to manage users and access policy across cloud applications and deployments.</span></span> <span data-ttu-id="72562-111">公用雲端平台有某種形式的雲端原生身分識別服務，支援管理和部署工作，並且能夠使用您現有的內部部署身分識別解決方案來[變化整合的層級](../../decision-guides/identity/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="72562-111">Public cloud platforms have some form of cloud-native identity services supporting management and deployment tasks and are capable of [varying levels of integration](../../decision-guides/identity/overview.md) with your existing on-premises identity solutions.</span></span> <span data-ttu-id="72562-112">這些功能都可能會導致雲端身分識別原則比傳統內部部署解決方案所需的更加複雜。</span><span class="sxs-lookup"><span data-stu-id="72562-112">All of these features can result in cloud identity policy being more complicated than your traditional on-premises solutions require.</span></span>

<span data-ttu-id="72562-113">身分識別基準專業領域對於您的雲端部署的重要性取決於您的小組大小，並且需要整合您的雲端型身分識別解決方案與現有內部部署身分識別服務。</span><span class="sxs-lookup"><span data-stu-id="72562-113">The importance of the Identity Baseline discipline to your cloud deployment will depend on the size of your team and need to integrate your cloud-based identity solution with an existing on-premises identity service.</span></span> <span data-ttu-id="72562-114">初始測試部署可能不需要太多的使用者組織或管理，但隨著您的雲端資產成熟，您可能必須支援更複雜的組織整合和集中化管理。</span><span class="sxs-lookup"><span data-stu-id="72562-114">Initial test deployments may not require much in the way of user organization or management, but as your cloud estate matures, you will likely need to support more complicated organizational integration and centralized management.</span></span>

## <a name="business-risk"></a><span data-ttu-id="72562-115">業務風險</span><span class="sxs-lookup"><span data-stu-id="72562-115">Business risk</span></span>

<span data-ttu-id="72562-116">身分識別基準專業領域會嘗試解決與身分識別服務和存取控制相關的核心業務風險。</span><span class="sxs-lookup"><span data-stu-id="72562-116">The Identity Baseline discipline attempts to address core business risks related to identity services and access control.</span></span> <span data-ttu-id="72562-117">在您規劃和實作您的雲端部署時，與您的企業一起識別這些風險，並監視它們的關聯性。</span><span class="sxs-lookup"><span data-stu-id="72562-117">Work with your business to identify these risks and monitor each of them for relevance as you plan for and implement your cloud deployments.</span></span>

<span data-ttu-id="72562-118">組織之間的風險不同，但是下列項目可作為通用身分識別相關風險，供您在雲端治理小組內討論時作為起點：</span><span class="sxs-lookup"><span data-stu-id="72562-118">Risks will differ between organization, but the following serve as common identity-related risks that you can use as a starting point for discussions within your Cloud Governance team:</span></span>

- <span data-ttu-id="72562-119">**未經授權的存取**。</span><span class="sxs-lookup"><span data-stu-id="72562-119">**Unauthorized access**.</span></span> <span data-ttu-id="72562-120">可由未經授權使用者存取的敏感性資料和資源，會導致資料外洩或服務中斷，違反貴組織的安全性周邊，並且讓業務或法律責任處於風險中。</span><span class="sxs-lookup"><span data-stu-id="72562-120">Sensitive data and resources that can be accessed by unauthorized users can lead to data leaks or service disruptions, violating your organization's security perimeter and risking business or legal liabilities.</span></span>
- <span data-ttu-id="72562-121">**由於多個身分識別解決方案造成效率低落**。</span><span class="sxs-lookup"><span data-stu-id="72562-121">**Inefficiency due to multiple identity solutions**.</span></span> <span data-ttu-id="72562-122">具有多個身分識別服務租用戶的組織需要多個使用者的帳戶。</span><span class="sxs-lookup"><span data-stu-id="72562-122">Organizations with multiple identity services tenants can require multiple accounts for users.</span></span> <span data-ttu-id="72562-123">這可能會導致使用者的效率低落，因為使用者必須記住多組認證，IT 也必須跨多個系統管理帳戶。</span><span class="sxs-lookup"><span data-stu-id="72562-123">This can lead to inefficiency for users who need to remember multiple sets of credentials and for IT in managing accounts across multiple systems.</span></span> <span data-ttu-id="72562-124">如果使用者存取指派在身分識別解決方案之間未隨著人員、小組和業務目標變更進行更新，您的雲端資源可能會遭到未獲授權存取的攻擊，或者使用者無法存取必要的資源。</span><span class="sxs-lookup"><span data-stu-id="72562-124">If user access assignments are not updated across identity solutions as staff, teams, and business goals change, your cloud resources may be vulnerable to unauthorized access or users unable to access required resources.</span></span>
- <span data-ttu-id="72562-125">**無法與外部合作夥伴共用資源**。</span><span class="sxs-lookup"><span data-stu-id="72562-125">**Inability to share resources with external partners**.</span></span> <span data-ttu-id="72562-126">無法將外部業務合作夥伴新增至您現有的身分識別解決方案，阻止有效率的資源共用和業務通訊。</span><span class="sxs-lookup"><span data-stu-id="72562-126">Difficulty adding external business partners to your existing identity solutions can prevent efficient resource sharing and business communication.</span></span>
- <span data-ttu-id="72562-127">**內部部署身分識別相依性**。</span><span class="sxs-lookup"><span data-stu-id="72562-127">**On-premises identity dependencies**.</span></span> <span data-ttu-id="72562-128">舊的驗證機制或第三方多重要素驗證可能無法在雲端使用，需要移轉工作負載以便改良，或者需要額外身分識別服務部署到雲端。</span><span class="sxs-lookup"><span data-stu-id="72562-128">Legacy authentication mechanisms or third-party multi-factor authentication might not be available in the cloud, requiring either migrating workloads to be retooled, or additional identity services to be deployed to the cloud.</span></span> <span data-ttu-id="72562-129">任一要求都可能會延遲或防止移轉並增加成本。</span><span class="sxs-lookup"><span data-stu-id="72562-129">Either requirement could delay or prevent migration, and increase costs.</span></span>

## <a name="next-steps"></a><span data-ttu-id="72562-130">後續步驟</span><span class="sxs-lookup"><span data-stu-id="72562-130">Next steps</span></span>

<span data-ttu-id="72562-131">使用[雲端管理範本](./template.md)，記錄目前雲端採用方案可能會引入的商務風險。</span><span class="sxs-lookup"><span data-stu-id="72562-131">Using the [Cloud Management Template](./template.md), document business risks that are likely to be introduced by the current cloud adoption plan.</span></span>

<span data-ttu-id="72562-132">一旦建立對於實際商務風險的了解，下一步是記錄風險的業務承受度與用來監視承受度的指標和關鍵計量。</span><span class="sxs-lookup"><span data-stu-id="72562-132">Once an understanding of realistic business risks is established, the next step is to document the business's tolerance for risk and the indicators and key metrics to monitor that tolerance.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="72562-133">了解指標、計量及風險承受度</span><span class="sxs-lookup"><span data-stu-id="72562-133">Understand indicators, metrics, and risk tolerance</span></span>](./metrics-tolerance.md)
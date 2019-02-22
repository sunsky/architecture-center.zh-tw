---
title: CAF：身分識別基準範例原則聲明
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 身分識別基準範例原則聲明
author: BrianBlanchard
ms.openlocfilehash: 5fad9265b9c048ee502c7e084ddd03faa0ad3e23
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901100"
---
# <a name="identity-baseline-sample-policy-statements"></a><span data-ttu-id="42ba1-103">身分識別基準範例原則聲明</span><span class="sxs-lookup"><span data-stu-id="42ba1-103">Identity Baseline sample policy statements</span></span>

<span data-ttu-id="42ba1-104">個別雲端原則聲明是解決在風險評估流程中識別出的特定風險方針。</span><span class="sxs-lookup"><span data-stu-id="42ba1-104">Individual cloud policy statements are guidelines for addressing specific risks identified during your risk assessment process.</span></span> <span data-ttu-id="42ba1-105">這些聲明應該會提供風險的簡明摘要，以及如何處理這些風險的方案。</span><span class="sxs-lookup"><span data-stu-id="42ba1-105">These statements should provide a concise summary of risks and plans to deal with them.</span></span> <span data-ttu-id="42ba1-106">每個聲明定義都應該包含下列資訊：</span><span class="sxs-lookup"><span data-stu-id="42ba1-106">Each statement definition should include these pieces of information:</span></span>

- <span data-ttu-id="42ba1-107">技術風險 - 此原則將解決的風險摘要。</span><span class="sxs-lookup"><span data-stu-id="42ba1-107">Technical risk - A summary of the risk this policy will address.</span></span>
- <span data-ttu-id="42ba1-108">原則聲明 - 明確地摘要說明原則需求。</span><span class="sxs-lookup"><span data-stu-id="42ba1-108">Policy statement - A clear summary explanation of the policy requirements.</span></span>
- <span data-ttu-id="42ba1-109">設計選項：IT 小組與開發人員可以在實作原則時使用的可操作建議、規格或其他指引。</span><span class="sxs-lookup"><span data-stu-id="42ba1-109">Design options - Actionable recommendations, specifications, or other guidance that IT teams and developers can use when implementing the policy.</span></span>

<span data-ttu-id="42ba1-110">下列範例原則聲明會解決一些與身分識別相關的常見業務風險，並可作為您為自己組織需求撰寫原則聲明草稿時的參考範例。</span><span class="sxs-lookup"><span data-stu-id="42ba1-110">The following sample policy statements address a number of common identity-related business risks, and are provided as examples for you to reference when drafting policy statements to address your own organization's needs.</span></span> <span data-ttu-id="42ba1-111">這些範例並非禁止使用，而且可能提供數個原則選項來處理任何特定的風險。</span><span class="sxs-lookup"><span data-stu-id="42ba1-111">These examples are not meant to be proscriptive, and there are potentially several policy options for dealing with any particular risk.</span></span> <span data-ttu-id="42ba1-112">請與業務和 IT 小組密切合作，以識別適用於您獨特風險集合的最佳原則解決方案。</span><span class="sxs-lookup"><span data-stu-id="42ba1-112">Work closely with business and IT teams to identify the best policy solutions for your unique set of risks.</span></span>

## <a name="lack-of-access-controls"></a><span data-ttu-id="42ba1-113">缺少存取控制</span><span class="sxs-lookup"><span data-stu-id="42ba1-113">Lack of access controls</span></span>

<span data-ttu-id="42ba1-114">**技術風險**：不足或特定的存取控制設定可能導致敏感或任務關鍵性資源遭到未經授權的存取。</span><span class="sxs-lookup"><span data-stu-id="42ba1-114">**Technical risk**: Insufficient or ad-hoc access control settings can introduce risk of unauthorized access to sensitive or mission-critical resources.</span></span>

<span data-ttu-id="42ba1-115">**原則聲明**：部署至雲端的所有資產，均應使用經目前的治理原則核准的身分識別與角色來控管。</span><span class="sxs-lookup"><span data-stu-id="42ba1-115">**Policy statement**: All assets deployed to the cloud should be controlled using identities and roles approved by current governance policies.</span></span>

<span data-ttu-id="42ba1-116">**潛在的設計選項**：[Azure Active Directory 條件式存取](/azure/active-directory/conditional-access/overview)是 Azure 中的預設存取控制機制。</span><span class="sxs-lookup"><span data-stu-id="42ba1-116">**Potential design options**: [Azure Active Directory conditional access](/azure/active-directory/conditional-access/overview) is the default access control mechanism in Azure.</span></span>

## <a name="overprovisioned-access"></a><span data-ttu-id="42ba1-117">過度佈建的存取</span><span class="sxs-lookup"><span data-stu-id="42ba1-117">Overprovisioned access</span></span>

<span data-ttu-id="42ba1-118">**技術風險**：使用者和群組若對資源有超過其責任範圍的控制權，則可能會發生未經授權的修改情形，進而造成業務中斷或安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="42ba1-118">**Technical risk**: Users and groups with control over resources beyond their area of responsibility can result in unauthorized modifications leading to outages or security vulnerabilities.</span></span>

<span data-ttu-id="42ba1-119">**原則聲明**：您將實作下列原則：</span><span class="sxs-lookup"><span data-stu-id="42ba1-119">**Policy statement**: The following policies will be implemented:</span></span>

- <span data-ttu-id="42ba1-120">對於任何與任務關鍵性應用程式或受保護資料有關的資源，將會套用存取權限最低的模型。</span><span class="sxs-lookup"><span data-stu-id="42ba1-120">A least privilege access model will be applied to any resources involved in mission-critical applications or protected data.</span></span>
- <span data-ttu-id="42ba1-121">提高的權限應屬於例外狀況，而且必須由雲端治理小組記錄這類例外狀況。</span><span class="sxs-lookup"><span data-stu-id="42ba1-121">Elevated permissions should be an exception, and any such exceptions must be recorded with the Cloud Governance team.</span></span> <span data-ttu-id="42ba1-122">應定期稽核例外狀況。</span><span class="sxs-lookup"><span data-stu-id="42ba1-122">Exceptions will be audited regularly.</span></span>

<span data-ttu-id="42ba1-123">**潛在的設計選項**：請參閱 [Azure 身分識別管理最佳做法](/azure/security/azure-security-identity-management-best-practices)，並根據[須知事項](https://wikipedia.org/wiki/Need_to_know)和[最低權限安全性](https://wikipedia.org/wiki/Principle_of_least_privilege)原則，實作限制存取權的角色型存取控制 (RBAC) 策略。</span><span class="sxs-lookup"><span data-stu-id="42ba1-123">**Potential design options**: Consult the [Azure Identity Management best practices](/azure/security/azure-security-identity-management-best-practices) to implement a role-based access control (RBAC) strategy that restricts access based on the [need to know](https://wikipedia.org/wiki/Need_to_know) and [least privilege security](https://wikipedia.org/wiki/Principle_of_least_privilege) principles.</span></span>

## <a name="lack-of-shared-management-accounts-between-on-premises-and-the-cloud"></a><span data-ttu-id="42ba1-124">缺少內部部署與雲端之間的共用管理帳戶</span><span class="sxs-lookup"><span data-stu-id="42ba1-124">Lack of shared management accounts between on-premises and the cloud</span></span>

<span data-ttu-id="42ba1-125">**技術風險**：您內部部署 Active Directory 上具有帳戶的 IT 管理或系統管理人員若無足夠的存取權來存取雲端資源，則可能無法有效率地解決操作問題或安全性問題。</span><span class="sxs-lookup"><span data-stu-id="42ba1-125">**Technical risk**: IT management or administrative staff with accounts on your on-premises Active Directory may not have sufficient access to cloud resources may not be able to efficiently resolve operational or security issues.</span></span>

<span data-ttu-id="42ba1-126">**原則聲明**：內部部署 Active Directory 基礎結構中已提高權限的所有群組，均應對應至經核准的 RBAC 角色。</span><span class="sxs-lookup"><span data-stu-id="42ba1-126">**Policy statement**: All groups in the on-premises Active Directory infrastructure that have elevated privileges should be mapped to an approved RBAC role.</span></span>

<span data-ttu-id="42ba1-127">**潛在的設計選項**：在雲端式 Azure Active Directory 與內部部署 Active Directory 之間部署混合式身分識別解決方案，並將必要的內部部署群組新增至執行其工作所需的 RBAC 角色。</span><span class="sxs-lookup"><span data-stu-id="42ba1-127">**Potential design options**: Implement a hybrid identity solution between your cloud-based Azure Active Directory and your on-premise Active Directory, and add the required on-premises groups to the RBAC roles necessary to do their work.</span></span>

## <a name="weak-authentication-mechanisms"></a><span data-ttu-id="42ba1-128">弱式驗證機制</span><span class="sxs-lookup"><span data-stu-id="42ba1-128">Weak authentication mechanisms</span></span>

<span data-ttu-id="42ba1-129">**技術風險**：身分識別管理系統所用的使用者驗證方法 (例如基本的使用者/密碼組合) 若不夠安全，可能會導致密碼外洩或遭駭客入侵，而最大風險是安全的雲端系統會遭到未經授權的存取。</span><span class="sxs-lookup"><span data-stu-id="42ba1-129">**Technical risk**: Identity management systems with insufficiently secure user authentication methods, such as basic user/password combinations, can lead to compromised or hacked passwords, providing a major risk of unauthorized access to secure cloud systems.</span></span>

<span data-ttu-id="42ba1-130">**原則聲明**：所有帳戶都必須使用多重要素驗證 (MFA) 方法來登入受保護的資源。</span><span class="sxs-lookup"><span data-stu-id="42ba1-130">**Policy statement**: All accounts are required to login to secured resources using a multi-factor authentication (MFA) method.</span></span>

<span data-ttu-id="42ba1-131">**潛在的設計選項**：針對 Azure Active Directory，請將 [Azure Multi-factor Authentication](/azure/active-directory/authentication/concept-mfa-howitworks) 實作為使用者授權程序的一部分。</span><span class="sxs-lookup"><span data-stu-id="42ba1-131">**Potential design options**: For Azure Active Directory, implement [Azure Multi-Factor Authentication](/azure/active-directory/authentication/concept-mfa-howitworks) as part of your user authorization process.</span></span>

## <a name="isolated-identity-providers"></a><span data-ttu-id="42ba1-132">獨立的身分識別提供者</span><span class="sxs-lookup"><span data-stu-id="42ba1-132">Isolated identity providers</span></span>

<span data-ttu-id="42ba1-133">**技術風險**：不相容的識別提供者可能會導致無法與客戶或其他業務夥伴共用資源或服務。</span><span class="sxs-lookup"><span data-stu-id="42ba1-133">**Technical risk**: Incompatible identity providers can result in the inability to share resources or services with customers or other business partners.</span></span>

<span data-ttu-id="42ba1-134">**原則聲明**：部署需要客戶驗證的任何應用程式時，必須使用適用於內部使用者的主要身分識別提供者核准的身分識別提供者。</span><span class="sxs-lookup"><span data-stu-id="42ba1-134">**Policy statement**: Deployment of any applications that require customer authentication must use an approved identity provider that is compatible with the primary identity provider for internal users.</span></span>

<span data-ttu-id="42ba1-135">**潛在的設計選項**：在內部與客戶識別提供者之間實作[與 Azure Active Directory 同盟](/azure/active-directory/hybrid/whatis-fed)。</span><span class="sxs-lookup"><span data-stu-id="42ba1-135">**Potential design options**: Implement [Federation with Azure Active Directory](/azure/active-directory/hybrid/whatis-fed) between your internal and customer identity providers.</span></span>

## <a name="identity-reviews"></a><span data-ttu-id="42ba1-136">身分識別檢閱</span><span class="sxs-lookup"><span data-stu-id="42ba1-136">Identity reviews</span></span>

<span data-ttu-id="42ba1-137">**技術風險**：當業務隨著時間發生變化時，您就需要增加新的雲端部署或其他安全性考量，而這可能會增加安全資源遭受未經授權存取的風險。</span><span class="sxs-lookup"><span data-stu-id="42ba1-137">**Technical risk**: Over time business change, the addition of new cloud deployments or other security concerns can increase the risks of unauthorized access to secure resources.</span></span>

<span data-ttu-id="42ba1-138">**原則聲明**：雲端治理流程必須包括與身分識別管理小組一起進行每季檢閱，以找出雲端資產設定應該避免的惡意執行者或使用模式。</span><span class="sxs-lookup"><span data-stu-id="42ba1-138">**Policy statement**: Cloud Governance processes must include quarterly review with identity management teams to identify malicious actors or usage patterns that should be prevented by cloud asset configuration.</span></span>

<span data-ttu-id="42ba1-139">**潛在的設計選項**：召開每季安全性檢閱會議，其中包括治理小組成員，以及負責管理識別服務的 IT 人員。</span><span class="sxs-lookup"><span data-stu-id="42ba1-139">**Potential design options**: Establish a quarterly security review meeting that includes both governance team members and IT staff responsible for managing identity services.</span></span> <span data-ttu-id="42ba1-140">檢閱現有的安全性資料和計量，以證實目前身分識別管理原則和工具間的差距，並更新原則來降低任何新風險。</span><span class="sxs-lookup"><span data-stu-id="42ba1-140">Review existing security data and metrics to establish gaps in current identity management policy and tooling, and update policy to mitigate any new risks.</span></span>

## <a name="next-steps"></a><span data-ttu-id="42ba1-141">後續步驟</span><span class="sxs-lookup"><span data-stu-id="42ba1-141">Next steps</span></span>

<span data-ttu-id="42ba1-142">使用本文提及的範例作為起點，以開發與您雲端採用方案保持一致的原則來解決特定業務風險。</span><span class="sxs-lookup"><span data-stu-id="42ba1-142">Use the samples mentioned in this article as a starting point to develop policies that address specific business risks that align with your cloud adoption plans.</span></span>

<span data-ttu-id="42ba1-143">若要開始自行開發與身分識別基準相關的自訂原則聲明，請下載[身分識別基準範本](template.md)。</span><span class="sxs-lookup"><span data-stu-id="42ba1-143">To begin developing your own custom policy statements related to Identity Baseline, download the [Identity Baseline template](template.md).</span></span>

<span data-ttu-id="42ba1-144">若要加速採用這個專業領域，請選擇最密切配合您環境的[可採取動作的治理旅程](../journeys/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="42ba1-144">To accelerate adoption of this discipline, choose the [Actionable Governance Journey](../journeys/overview.md) that most closely aligns with your environment.</span></span> <span data-ttu-id="42ba1-145">然後修改設計，以納入您特定的公司原則決策。</span><span class="sxs-lookup"><span data-stu-id="42ba1-145">Then modify the design to incorporate your specific corporate policy decisions.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="42ba1-146">可採取動作的治理旅程</span><span class="sxs-lookup"><span data-stu-id="42ba1-146">Actionable Governance Journeys</span></span>](../journeys/overview.md)
---
title: CAF：安全性基準範例原則聲明
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 安全性基準範例原則聲明
author: BrianBlanchard
ms.openlocfilehash: ac40e022f8dff0c3021c04cd9d6ae42d28afaf1f
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900798"
---
# <a name="security-baseline-sample-policy-statements"></a><span data-ttu-id="5f761-103">安全性基準範例原則聲明</span><span class="sxs-lookup"><span data-stu-id="5f761-103">Security Baseline sample policy statements</span></span>

<span data-ttu-id="5f761-104">個別雲端原則聲明是解決在風險評估流程中識別出的特定風險方針。</span><span class="sxs-lookup"><span data-stu-id="5f761-104">Individual cloud policy statements are guidelines for addressing specific risks identified during your risk assessment process.</span></span> <span data-ttu-id="5f761-105">這些聲明應該會提供風險的簡明摘要，以及如何處理這些風險的方案。</span><span class="sxs-lookup"><span data-stu-id="5f761-105">These statements should provide a concise summary of risks and plans to deal with them.</span></span> <span data-ttu-id="5f761-106">每個聲明定義都應該包含下列資訊：</span><span class="sxs-lookup"><span data-stu-id="5f761-106">Each statement definition should include these pieces of information:</span></span>

- <span data-ttu-id="5f761-107">技術風險 - 此原則將解決的風險摘要。</span><span class="sxs-lookup"><span data-stu-id="5f761-107">Technical risk - A summary of the risk this policy will address.</span></span>
- <span data-ttu-id="5f761-108">原則聲明 - 明確地摘要說明原則需求。</span><span class="sxs-lookup"><span data-stu-id="5f761-108">Policy statement - A clear summary explanation of the policy requirements.</span></span>
- <span data-ttu-id="5f761-109">設計選項 - IT 小組與開發人員可以在實作原則時使用之可操作的建議、規格或其他指引。</span><span class="sxs-lookup"><span data-stu-id="5f761-109">Technical options - Actionable recommendations, specifications, or other guidance that IT teams and developers can use when implementing the policy.</span></span>

<span data-ttu-id="5f761-110">下列範例原則聲明會解決一些與安全性相關的常見業務風險，並提供來做為您撰寫實際原則聲明的草稿以滿足自己的組織需求時所參考的範例。</span><span class="sxs-lookup"><span data-stu-id="5f761-110">The following sample policy statements address a number of common security-related business risks, and are provided as examples for you to reference when drafting actual policy statements addressing your own organization's needs.</span></span> <span data-ttu-id="5f761-111">這些範例並非禁止使用，而且可能提供數個原則選項來處理任何已識別出的單一風險。</span><span class="sxs-lookup"><span data-stu-id="5f761-111">These examples are not meant to be proscriptive, and there are potentially several policy options for dealing with any single identified risk.</span></span> <span data-ttu-id="5f761-112">與業務、安全性和 IT 小組密切合作，來識別適用於特定安全性風險的最佳原則解決方案。</span><span class="sxs-lookup"><span data-stu-id="5f761-112">Work closely with business, security, and IT teams to identify the best policy solutions for your particular security risks.</span></span>  

## <a name="asset-classification"></a><span data-ttu-id="5f761-113">資產分類</span><span class="sxs-lookup"><span data-stu-id="5f761-113">Asset classification</span></span>

<span data-ttu-id="5f761-114">**技術風險**：不正確地識別為任務關鍵性或是涉及敏感性資料的資產，可能不會收到足夠的保護，導致潛在的資料外洩或業務中斷。</span><span class="sxs-lookup"><span data-stu-id="5f761-114">**Technical risk**: Assets that are not correctly identified as mission-critical or involving sensitive data may not receive sufficient protections, leading to potential data leaks or business disruptions.</span></span>

<span data-ttu-id="5f761-115">**原則聲明**：所有已部署的資產必須依據重要性和資料類別來分類。</span><span class="sxs-lookup"><span data-stu-id="5f761-115">**Policy statement**: All deployed assets must be categorized by criticality and data classification.</span></span> <span data-ttu-id="5f761-116">分類必須由雲端治理小組和應用程式擁有者進行檢閱，然後再部署到雲端。</span><span class="sxs-lookup"><span data-stu-id="5f761-116">Classifications must be reviewed by the Cloud Governance team and the application owner before deployment to the cloud.</span></span>

<span data-ttu-id="5f761-117">**潛在的設計選項**：建立[資源標記標準](../../decision-guides/resource-tagging/overview.md)，並確保 IT 人員使用 [Azure 資源標記](/azure/azure-resource-manager/resource-group-using-tags)，一致地將其套用至任何部署的資源。</span><span class="sxs-lookup"><span data-stu-id="5f761-117">**Potential design option**: Establish [resource tagging standards](../../decision-guides/resource-tagging/overview.md) and ensure IT staff apply them consistently to any deployed resources using [Azure resource tags](/azure/azure-resource-manager/resource-group-using-tags).</span></span>

## <a name="data-encryption"></a><span data-ttu-id="5f761-118">資料加密</span><span class="sxs-lookup"><span data-stu-id="5f761-118">Data encryption</span></span>

<span data-ttu-id="5f761-119">**技術風險**：儲存期間公開的受保護資料有風險。</span><span class="sxs-lookup"><span data-stu-id="5f761-119">**Technical risk**: There is a risk of protected data being exposed during storage.</span></span>

<span data-ttu-id="5f761-120">**原則聲明**：所有受保護的資料必須在待用時加密。</span><span class="sxs-lookup"><span data-stu-id="5f761-120">**Policy statement**: All protected data must be encrypted when at rest.</span></span>

<span data-ttu-id="5f761-121">**潛在的設計選項**：請參閱 [Azure 加密概觀](/azure/security/security-azure-encryption-overview)一文，以取得如何在 Azure 平台上執行待用資料加密的討論。</span><span class="sxs-lookup"><span data-stu-id="5f761-121">**Potential design option**: See the [Azure encryption overview](/azure/security/security-azure-encryption-overview) article for a discussion of how data at rest encryption is performed on the Azure platform.</span></span>  

## <a name="network-isolation"></a><span data-ttu-id="5f761-122">網路隔離</span><span class="sxs-lookup"><span data-stu-id="5f761-122">Network isolation</span></span>

<span data-ttu-id="5f761-123">**技術風險**：網路內網路與子網路之間的連線會引入潛在的弱點，導致資料外洩或任務關鍵性服務中斷。</span><span class="sxs-lookup"><span data-stu-id="5f761-123">**Technical risk**: Connectivity between networks and subnets within networks introduces potential vulnerabilities that can result in data leaks or disruption of mission-critical services.</span></span>

<span data-ttu-id="5f761-124">**原則聲明**：包含受保護資料的網路子網路必須與其他任何子網路隔離。</span><span class="sxs-lookup"><span data-stu-id="5f761-124">**Policy statement**: Network subnets containing protected data must be isolated from any other subnets.</span></span> <span data-ttu-id="5f761-125">受保護資料子網路之間的網路流量必須定期稽核。</span><span class="sxs-lookup"><span data-stu-id="5f761-125">Network traffic between protected data subnets is to be audited regularly.</span></span>

<span data-ttu-id="5f761-126">**潛在的設計選項**：在 Azure 中，網路和子網路隔離是透過 [Azure 虛擬網路](/azure/virtual-network/virtual-networks-overview)進行管理。</span><span class="sxs-lookup"><span data-stu-id="5f761-126">**Potential design option**: In Azure, network and subnet isolation is managed through [Azure Virtual Networks](/azure/virtual-network/virtual-networks-overview).</span></span>

## <a name="secure-external-access"></a><span data-ttu-id="5f761-127">保護外部存取</span><span class="sxs-lookup"><span data-stu-id="5f761-127">Secure external access</span></span>

<span data-ttu-id="5f761-128">**技術風險**：允許從公用網際網路存取工作負載，會引入入侵風險，導致未獲授權資料曝光或業務中斷。</span><span class="sxs-lookup"><span data-stu-id="5f761-128">**Technical risk**: Allowing access to workloads from the public internet introduces a risk of intrusion resulting in unauthorized data exposure or business disruption.</span></span>

<span data-ttu-id="5f761-129">**原則聲明**：沒有包含受保護資料的任何子網路可以透過公用網際網路或跨資料中心直接存取。</span><span class="sxs-lookup"><span data-stu-id="5f761-129">**Policy statement**: No subnet containing protected data can be directly accessed over public internet or across datacenters.</span></span> <span data-ttu-id="5f761-130">這些子網路的存取必須透過中繼子網路進行路由。</span><span class="sxs-lookup"><span data-stu-id="5f761-130">Access to those subnets must be routed through intermediate subnet works.</span></span> <span data-ttu-id="5f761-131">這些子網路的所有存取都必須經過防火牆解決方案，該解決方案可以執行封包掃描和封鎖功能。</span><span class="sxs-lookup"><span data-stu-id="5f761-131">All access into those subnets must come through a firewall solution capable of performing packet scanning and blocking functions.</span></span>

<span data-ttu-id="5f761-132">**潛在的設計選項**：在 Azure 中，請藉由部署[公用網際網路與雲端型網路之間的 DMZ](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)，來保護公用端點。</span><span class="sxs-lookup"><span data-stu-id="5f761-132">**Potential design option**: In Azure, secure public endpoints by deploying a [DMZ between the public internet and your cloud-based network](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz).</span></span>

## <a name="ddos-protection"></a><span data-ttu-id="5f761-133">DDoS 保護</span><span class="sxs-lookup"><span data-stu-id="5f761-133">DDoS protection</span></span>

<span data-ttu-id="5f761-134">**技術風險**：分散式阻斷服務 (DDoS) 攻擊會導致業務中斷。</span><span class="sxs-lookup"><span data-stu-id="5f761-134">**Technical risk**: Distributed denial of service (DDoS) attacks can result in a business interruption.</span></span>

<span data-ttu-id="5f761-135">**原則聲明**：將自動化 DDoS 風險降低機制部署至所有可公開存取的網路端點。</span><span class="sxs-lookup"><span data-stu-id="5f761-135">**Policy statement**: Deploy automated DDoS mitigation mechanisms to all publicly accessible network endpoints.</span></span>

<span data-ttu-id="5f761-136">**潛在的設計選項**：使用 [Azure DDoS 保護](/azure/virtual-network/ddos-protection-overview)將 DDoS 攻擊所造成的中斷情況降到最低。</span><span class="sxs-lookup"><span data-stu-id="5f761-136">**Potential design option**: Use [Azure DDoS Protection](/azure/virtual-network/ddos-protection-overview) to minimize disruptions caused by DDoS attacks.</span></span>

## <a name="secure-on-premises-connectivity"></a><span data-ttu-id="5f761-137">保護內部部署連線</span><span class="sxs-lookup"><span data-stu-id="5f761-137">Secure on-premises connectivity</span></span>

<span data-ttu-id="5f761-138">**技術風險**：您的雲端網路與內部部署之間的未加密流量 (透過網際網路) 容易遭到攔截，引入資料曝光風險。</span><span class="sxs-lookup"><span data-stu-id="5f761-138">**Technical risk**: Unencrypted traffic between your cloud network and on-premises over the public internet is vulnerable to interception, introducing the risk of data exposure.</span></span>

<span data-ttu-id="5f761-139">**原則聲明**：內部部署與雲端網路之間的所有連線必須透過安全加密 VPN 連線或專用私人 WAN 連結進行。</span><span class="sxs-lookup"><span data-stu-id="5f761-139">**Policy statement**: All connections between the on-premises and cloud networks must take place either through a secure encrypted VPN connection or a dedicated private WAN link.</span></span>

<span data-ttu-id="5f761-140">**潛在的設計選項**：在 Azure 中，使用 ExpressRoute 或 Azure VPN 來建立您的內部部署與雲端網路之間的私人連線。</span><span class="sxs-lookup"><span data-stu-id="5f761-140">**Potential design option**: In Azure, use ExpressRoute or Azure VPN to establish private connections between your on-premises and cloud networks.</span></span>

## <a name="network-monitoring-and-enforcement"></a><span data-ttu-id="5f761-141">網路監視和強制執行</span><span class="sxs-lookup"><span data-stu-id="5f761-141">Network monitoring and enforcement</span></span>

<span data-ttu-id="5f761-142">**技術風險**：變更網路設定會導致新的弱點和資料曝光風險。</span><span class="sxs-lookup"><span data-stu-id="5f761-142">**Technical risk**: Changes to network configuration can lead to new vulnerabilities and data exposure risks.</span></span>

<span data-ttu-id="5f761-143">**原則聲明**：治理工具必須稽核並強制執行安全性基準小組所定義的網路設定需求。</span><span class="sxs-lookup"><span data-stu-id="5f761-143">**Policy statement**: Governance tooling must audit and enforce network configuration requirements defined by the Security Baseline team.</span></span>

<span data-ttu-id="5f761-144">**潛在的設計選項**：在 Azure 中，網路活動可以使用 [Azure 網路監看員](/azure/network-watcher/network-watcher-monitoring-overview)來監視，而 [Azure 資訊安全中心](/azure/security-center/security-center-network-recommendations)可協助識別安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="5f761-144">**Potential design option**: In Azure, network activity can be monitored using [Azure Network Watcher](/azure/network-watcher/network-watcher-monitoring-overview), and [Azure Security Center](/azure/security-center/security-center-network-recommendations) can help identify security vulnerabilities.</span></span> <span data-ttu-id="5f761-145">Azure 原則可讓您根據安全性小組定義的限制，限制網路資源和資源設定原則。</span><span class="sxs-lookup"><span data-stu-id="5f761-145">Azure Policy allows you to restrict network resources and resource configuration policy according to limits defined by the security team.</span></span>

## <a name="security-review"></a><span data-ttu-id="5f761-146">安全性檢閱</span><span class="sxs-lookup"><span data-stu-id="5f761-146">Security review</span></span>

<span data-ttu-id="5f761-147">**技術風險**：經過一段時間，會出現新的安全性威脅和攻擊類型，增加您的雲端資源曝光或中斷的風險。</span><span class="sxs-lookup"><span data-stu-id="5f761-147">**Technical risk**: Over time, new security threats and attack types emerge, increasing the risk of exposure or disruption of your cloud resources.</span></span>

<span data-ttu-id="5f761-148">**原則聲明**：安全性小組應定期檢閱可能影響雲端部署的趨勢與潛在攻擊，以更新雲端中使用的安全性基準工具。</span><span class="sxs-lookup"><span data-stu-id="5f761-148">**Policy statement**: Trends and potential exploits that could affect cloud deployments should be reviewed regularly by the security team to provide updates to Security Baseline tooling used in the cloud.</span></span>

<span data-ttu-id="5f761-149">**潛在的設計選項**：建立定期安全性檢閱會議，包括相關 IT 和治理小組成員。</span><span class="sxs-lookup"><span data-stu-id="5f761-149">**Potential design option**: Establish a regular security review meeting that includes relevant IT and governance team members.</span></span> <span data-ttu-id="5f761-150">檢閱現有的安全性資料和計量，以便在目前的原則和安全性基準工具中建立間距，並更新原則來降低任何新風險。</span><span class="sxs-lookup"><span data-stu-id="5f761-150">Review existing security data and metrics to establish gaps in current policy and Security Baseline tooling, and update policy to mitigate any new risks.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5f761-151">後續步驟</span><span class="sxs-lookup"><span data-stu-id="5f761-151">Next steps</span></span>

<span data-ttu-id="5f761-152">使用本文提及的範例作為起點，以開發與您雲端採用方案保持一致的原則來解決特定的安全性風險。</span><span class="sxs-lookup"><span data-stu-id="5f761-152">Use the samples mentioned in this article as a starting point to develop policies that address specific security risks that align with your cloud adoption plans.</span></span>

<span data-ttu-id="5f761-153">若要開始自行開發與安全性基準相關的自訂原則聲明，請下載[安全性基準範本](template.md)。</span><span class="sxs-lookup"><span data-stu-id="5f761-153">To begin developing your own custom policy statements related to Security Baseline, download the [Security Baseline template](template.md).</span></span>

<span data-ttu-id="5f761-154">若要加速採用這個專業領域，請選擇最密切配合您環境的[可採取動作的治理旅程](../journeys/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="5f761-154">To accelerate adoption of this discipline, choose the [Actionable Governance Journey](../journeys/overview.md) that most closely aligns with your environment.</span></span> <span data-ttu-id="5f761-155">然後修改設計，以納入您特定的公司原則決策。</span><span class="sxs-lookup"><span data-stu-id="5f761-155">Then modify the design to incorporate your specific corporate policy decisions.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="5f761-156">可採取動作的治理旅程</span><span class="sxs-lookup"><span data-stu-id="5f761-156">Actionable Governance Journeys</span></span>](../journeys/overview.md)

---
title: CAF：軟體定義網路 - 雲端 DMZ
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 此網路架構允許內部部署和雲端式網路之間的有限存取
author: rotycenh
ms.openlocfilehash: a192541dcfb0f3d713f4139a2ab0541d0c7202db
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900919"
---
# <a name="software-defined-networks-cloud-dmz"></a><span data-ttu-id="66d1f-103">軟體定義網路：雲端 DMZ</span><span class="sxs-lookup"><span data-stu-id="66d1f-103">Software Defined Networks: Cloud DMZ</span></span>

<span data-ttu-id="66d1f-104">透過虛擬私人網路 (VPN) 連線網路，雲端 DMZ 網路架構允許內部部署和雲端式網路之間的有限存取。</span><span class="sxs-lookup"><span data-stu-id="66d1f-104">The Cloud DMZ network architecture allows limited access between your on-premises and cloud-based networks, using a virtual private network (VPN) to connect the networks.</span></span> <span data-ttu-id="66d1f-105">DMZ 部署在雲端，從雲端式資源保護對內部部署網路的存取。</span><span class="sxs-lookup"><span data-stu-id="66d1f-105">A DMZ is deployed in the cloud to secure access to the on-premises network from cloud-based resources.</span></span>

![安全混合式網路架構](../../../reference-architectures/dmz/images/dmz-private.png)

<span data-ttu-id="66d1f-107">此架構支援的案例為，您的組織想開始整合雲端式工作負載與內部部署工作負載，但雲端安全性原則可能尚未完全成熟，或尚未取得兩個環境之間安全的專用 WAN 連線。</span><span class="sxs-lookup"><span data-stu-id="66d1f-107">This architecture is designed to support scenarios where your organization wants to start integrating cloud-based workloads with on-premises workloads but may not have fully matured cloud security policies or acquired a secure dedicated WAN connection between the two environments.</span></span> <span data-ttu-id="66d1f-108">因此，雲端網路應被視為周邊網路，以確保內部部署服務的安全。</span><span class="sxs-lookup"><span data-stu-id="66d1f-108">As a result, cloud networks should be treated like a demilitarized zone to ensure on-premises services are secure.</span></span>

<span data-ttu-id="66d1f-109">DMZ 會部署網路虛擬裝置 (NVA) 以實作防火牆和封包檢查等安全性功能。</span><span class="sxs-lookup"><span data-stu-id="66d1f-109">The DMZ deploys network virtual appliances (NVAs) to implement security functionality such as firewalls and packet inspection.</span></span> <span data-ttu-id="66d1f-110">在內部部署與雲端式應用程式或服務之間傳遞的流量必須通過 DMZ 進行稽核。</span><span class="sxs-lookup"><span data-stu-id="66d1f-110">Traffic passing between on-premises and cloud-based applications or services must pass through the DMZ where it can be audited.</span></span> <span data-ttu-id="66d1f-111">VPN 連線以及判斷哪些流量可通過 DMZ 網路的規則由 IT 安全性小組嚴格控管。</span><span class="sxs-lookup"><span data-stu-id="66d1f-111">VPN connections and the rules determining what traffic is allowed through the DMZ network are strictly controlled by IT security teams.</span></span>

## <a name="cloud-dmz-assumptions"></a><span data-ttu-id="66d1f-112">雲端 DMZ 假設</span><span class="sxs-lookup"><span data-stu-id="66d1f-112">Cloud DMZ assumptions</span></span>

<span data-ttu-id="66d1f-113">部署雲端 DMZ 會有下列假設：</span><span class="sxs-lookup"><span data-stu-id="66d1f-113">Deploying a Cloud DMZ assumes the following:</span></span>

- <span data-ttu-id="66d1f-114">您的安全性小組尚未完全符合內部部署和雲端式安全需求與原則。</span><span class="sxs-lookup"><span data-stu-id="66d1f-114">Your security teams have not fully aligned on-premises and cloud-based security requirements and policies.</span></span>
- <span data-ttu-id="66d1f-115">您的雲端式工作負載能有限地存取內部部署或第三方網路上所裝載的服務，或者您的使用者或內部部署環境中的應用程式能有限地存取雲端所裝載的資源。</span><span class="sxs-lookup"><span data-stu-id="66d1f-115">Your cloud-based workloads require limited access to services hosted on your on-premises or third-party networks, or your users or applications in your on-premises environment need limited access to cloud-hosted resources.</span></span>
- <span data-ttu-id="66d1f-116">公司原則、法規需求或技術相容性問題無法阻止您在內部部署網路和雲端提供者之間實作 VPN。</span><span class="sxs-lookup"><span data-stu-id="66d1f-116">Implementing a VPN connection between your on-premises networks and cloud provider is not prevented by corporate policy, regulatory requirements, or technical compatibility issues.</span></span>
- <span data-ttu-id="66d1f-117">您的工作負載不需透過多個訂用帳戶來避開資源限制，或者您的工作負載涉及多個訂用帳戶，但不需要集中管理分佈在多個訂用帳戶中的資源所使用的連線或共用服務。</span><span class="sxs-lookup"><span data-stu-id="66d1f-117">Your workloads either do not require multiple subscriptions to bypass subscription resource limits, or they involve multiple subscriptions but don't require central management of connectivity or shared services used by resources spread across multiple subscriptions.</span></span>

<span data-ttu-id="66d1f-118">您的雲端採用小組在查看實作雲端 DMZ 虛擬網路架構時，應該考量下列問題：</span><span class="sxs-lookup"><span data-stu-id="66d1f-118">Your Cloud Adoption team should consider the following issues when looking at implementing a Cloud DMZ virtual networking architecture:</span></span>

- <span data-ttu-id="66d1f-119">將內部部署網路與雲端網路連線，會使安全性需求變得更複雜。</span><span class="sxs-lookup"><span data-stu-id="66d1f-119">Connecting on-premises networks with cloud networks increases the complexity of your security requirements.</span></span> <span data-ttu-id="66d1f-120">即使雲端網路與內部部署環境之間的連線受到保護，您仍需要確保雲端資源的安全。</span><span class="sxs-lookup"><span data-stu-id="66d1f-120">Even though the connection between cloud networks and the on-premises environment are secured, you still need to ensure cloud resources are secured.</span></span>
- <span data-ttu-id="66d1f-121">雲端 DMZ 架構常作為跳板，當內部部署與雲端網路之間的連線進一步受到保護且安全性原則一致時，讓您能更廣泛採用完整的混合式網路功能架構。</span><span class="sxs-lookup"><span data-stu-id="66d1f-121">The Cloud DMZ architecture is commonly used as a stepping stone while connectivity is further secured and security policy aligned between on-premises and cloud networks, allowing a broader adoption of a full-scale hybrid networking architecture.</span></span>

## <a name="learn-more"></a><span data-ttu-id="66d1f-122">深入了解</span><span class="sxs-lookup"><span data-stu-id="66d1f-122">Learn more</span></span>

<span data-ttu-id="66d1f-123">如需 Azure 平台中實作雲端 DMZ 的詳細資訊，請參閱下列各項。</span><span class="sxs-lookup"><span data-stu-id="66d1f-123">See the following for more information about the implementing a Cloud DMZ in the Azure platform.</span></span>

- <span data-ttu-id="66d1f-124">[實作 Azure 與內部部署資料中心之間的 DMZ](../../../reference-architectures/dmz/secure-vnet-hybrid.md)。</span><span class="sxs-lookup"><span data-stu-id="66d1f-124">[Implement a DMZ between Azure and your on-premises datacenter](../../../reference-architectures/dmz/secure-vnet-hybrid.md).</span></span> <span data-ttu-id="66d1f-125">本文討論如何在 Azure 中實作安全的混合式網路架構。</span><span class="sxs-lookup"><span data-stu-id="66d1f-125">This article discusses how to implement a secure hybrid network architecture in Azure.</span></span>

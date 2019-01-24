---
title: 閘道管理員模式
titleSuffix: Cloud Design Patterns
description: 保護應用程式和服務，方法是使用專用的主機執行個體，其會作為用戶端和應用程式或服務之間的代理程式、會驗證和處理要求，並在兩者之間傳遞要求和資料。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 99d3f1f3e4ea1f189fbdfc4cb9d9b3d18e8551a2
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54487945"
---
# <a name="gatekeeper-pattern"></a><span data-ttu-id="f3f93-104">閘道管理員模式</span><span class="sxs-lookup"><span data-stu-id="f3f93-104">Gatekeeper pattern</span></span>

[!INCLUDE [header](../_includes/header.md)]

<span data-ttu-id="f3f93-105">保護應用程式和服務，方法是使用專用的主機執行個體，其會作為用戶端和應用程式或服務之間的代理程式、會驗證和處理要求，並在兩者之間傳遞要求和資料。</span><span class="sxs-lookup"><span data-stu-id="f3f93-105">Protect applications and services by using a dedicated host instance that acts as a broker between clients and the application or service, validates and sanitizes requests, and passes requests and data between them.</span></span> <span data-ttu-id="f3f93-106">這可以提供一層額外的安全性，並可限制系統的受攻擊面。</span><span class="sxs-lookup"><span data-stu-id="f3f93-106">This can provide an additional layer of security, and limit the attack surface of the system.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="f3f93-107">內容和問題</span><span class="sxs-lookup"><span data-stu-id="f3f93-107">Context and problem</span></span>

<span data-ttu-id="f3f93-108">應用程式透過接受和處理要求來向用戶端公開其功能。</span><span class="sxs-lookup"><span data-stu-id="f3f93-108">Applications expose their functionality to clients by accepting and processing requests.</span></span> <span data-ttu-id="f3f93-109">在雲端裝載案例中，應用程式公開用戶端連線的端點，並通常包括可處理來自用戶端之要求的程式碼。</span><span class="sxs-lookup"><span data-stu-id="f3f93-109">In cloud-hosted scenarios, applications expose endpoints clients connect to, and typically include the code to handle the requests from clients.</span></span> <span data-ttu-id="f3f93-110">此程式碼會執行驗證、處理部分或全部要求，並可能代表用戶端存取儲存體和其他服務。</span><span class="sxs-lookup"><span data-stu-id="f3f93-110">This code performs authentication and validation, some or all request processing, and is likely to accesses storage and other services on behalf of the client.</span></span>

<span data-ttu-id="f3f93-111">如果惡意使用者能夠入侵系統並存取應用程式的裝載環境，它使用的安全性機制 (例如，認證與儲存體金鑰) 和所存取的服務與資料都會遭到公開。</span><span class="sxs-lookup"><span data-stu-id="f3f93-111">If a malicious user is able to compromise the system and gain access to the application’s hosting environment, the security mechanisms it uses such as credentials and storage keys, and the services and data it accesses, are exposed.</span></span> <span data-ttu-id="f3f93-112">因此，惡意使用者將能任意存取機密資訊與其他服務。</span><span class="sxs-lookup"><span data-stu-id="f3f93-112">As a result, the malicious user can gain unrestrained access to sensitive information and other services.</span></span>

## <a name="solution"></a><span data-ttu-id="f3f93-113">解決方法</span><span class="sxs-lookup"><span data-stu-id="f3f93-113">Solution</span></span>

<span data-ttu-id="f3f93-114">若要讓用戶端取得機密資訊與服務存取權的風險降至最低，可從處理要求和存取儲存體的程式碼分離會公開公用端點的主機或工作。</span><span class="sxs-lookup"><span data-stu-id="f3f93-114">To minimize the risk of clients gaining access to sensitive information and services, decouple hosts or tasks that expose public endpoints from the code that processes requests and accesses storage.</span></span> <span data-ttu-id="f3f93-115">若要達到此目的，您可以使用外觀或專用工作來與用戶端互動，然後將要求&mdash;或許透過低耦合介面&mdash;遞交給主機或工作，以處理要求。</span><span class="sxs-lookup"><span data-stu-id="f3f93-115">You can achieve this by using a façade or a dedicated task that interacts with clients and then hands off the request&mdash;perhaps through a decoupled interface&mdash;to the hosts or tasks that'll handle the request.</span></span> <span data-ttu-id="f3f93-116">下圖提供此模式的高階概述。</span><span class="sxs-lookup"><span data-stu-id="f3f93-116">The figure provides a high-level overview of this pattern.</span></span>

![此模式的高階概述](./_images/gatekeeper-diagram.png)

<span data-ttu-id="f3f93-118">閘道管理員模式可用來只保護儲存體，也可作為更完整的外觀來保護應用程式的所有功能。</span><span class="sxs-lookup"><span data-stu-id="f3f93-118">The gatekeeper pattern can be used to simply protect storage, or it can be used as a more comprehensive façade to protect all of the functions of the application.</span></span> <span data-ttu-id="f3f93-119">重要因素如下：</span><span class="sxs-lookup"><span data-stu-id="f3f93-119">The important factors are:</span></span>

- <span data-ttu-id="f3f93-120">**受控制的驗證。**</span><span class="sxs-lookup"><span data-stu-id="f3f93-120">**Controlled validation**.</span></span> <span data-ttu-id="f3f93-121">閘道管理員會驗證所有要求，並拒絕不符合驗證需求的所有要求。</span><span class="sxs-lookup"><span data-stu-id="f3f93-121">The gatekeeper validates all requests, and rejects those that don't meet validation requirements.</span></span>
- <span data-ttu-id="f3f93-122">**風險受限且公開程度有限。**</span><span class="sxs-lookup"><span data-stu-id="f3f93-122">**Limited risk and exposure**.</span></span> <span data-ttu-id="f3f93-123">閘道管理員無法存取受信任主機用來存取儲存體與服務的認證或金鑰。</span><span class="sxs-lookup"><span data-stu-id="f3f93-123">The gatekeeper doesn't have access to the credentials or keys used by the trusted host to access storage and services.</span></span> <span data-ttu-id="f3f93-124">如果閘道管理員遭到入侵，攻擊者不會取得這些認證或金鑰的存取權。</span><span class="sxs-lookup"><span data-stu-id="f3f93-124">If the gatekeeper is compromised, the attacker doesn't get access to these credentials or keys.</span></span>
- <span data-ttu-id="f3f93-125">**安全性恰到好處。**</span><span class="sxs-lookup"><span data-stu-id="f3f93-125">**Appropriate security**.</span></span> <span data-ttu-id="f3f93-126">閘道管理員要在有限權限模式中執行，而應用程式的其餘部分要在完全信任模式中執行，才能存取儲存體和服務。</span><span class="sxs-lookup"><span data-stu-id="f3f93-126">The gatekeeper runs in a limited privilege mode, while the rest of the application runs in the full trust mode required to access storage and services.</span></span> <span data-ttu-id="f3f93-127">如果閘道管理員遭到入侵，它無法直接存取應用程式服務或資料。</span><span class="sxs-lookup"><span data-stu-id="f3f93-127">If the gatekeeper is compromised, it can't directly access the application services or data.</span></span>

<span data-ttu-id="f3f93-128">此模式就像是一般網路拓撲中的防火牆。</span><span class="sxs-lookup"><span data-stu-id="f3f93-128">This pattern acts like a firewall in a typical network topography.</span></span> <span data-ttu-id="f3f93-129">它允許閘道管理員檢查要求並決定是否將該要求傳遞至執行所需工作的信任主機 (有時稱為金鑰主機)。</span><span class="sxs-lookup"><span data-stu-id="f3f93-129">It allows the gatekeeper to examine requests and make a decision about whether to pass the request on to the trusted host (sometimes called the keymaster) that performs the required tasks.</span></span> <span data-ttu-id="f3f93-130">這通常會要求閘道管理員驗證並處理要求內容之後，再決定是否傳遞給信任的主機。</span><span class="sxs-lookup"><span data-stu-id="f3f93-130">This decision typically requires the gatekeeper to validate and sanitize the request content before passing it on to the trusted host.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="f3f93-131">問題和考量</span><span class="sxs-lookup"><span data-stu-id="f3f93-131">Issues and considerations</span></span>

<span data-ttu-id="f3f93-132">當您決定如何實作此模式時，請考慮下列幾點：</span><span class="sxs-lookup"><span data-stu-id="f3f93-132">Consider the following points when deciding how to implement this pattern:</span></span>

- <span data-ttu-id="f3f93-133">確定閘道管理員傳遞要求的目標信任主機僅公開內部或受保護的端點，並且只和閘道管理員連線。</span><span class="sxs-lookup"><span data-stu-id="f3f93-133">Ensure that the trusted hosts the gatekeeper passes requests to expose only internal or protected endpoints, and connect only to the gatekeeper.</span></span> <span data-ttu-id="f3f93-134">信任的主機不應公開任何外部端點或介面。</span><span class="sxs-lookup"><span data-stu-id="f3f93-134">The trusted hosts shouldn't expose any external endpoints or interfaces.</span></span>
- <span data-ttu-id="f3f93-135">閘道管理員必須在有限權限模式中執行。</span><span class="sxs-lookup"><span data-stu-id="f3f93-135">The gatekeeper must run in a limited privilege mode.</span></span> <span data-ttu-id="f3f93-136">這通常表示要在個別的託管服務或虛擬機器中執行閘道管理員與信任主機。</span><span class="sxs-lookup"><span data-stu-id="f3f93-136">Typically this means running the gatekeeper and the trusted host in separate hosted services or virtual machines.</span></span>
- <span data-ttu-id="f3f93-137">閘道管理員不應執行和應用程式或服務相關的任何處理，或存取任何資料。</span><span class="sxs-lookup"><span data-stu-id="f3f93-137">The gatekeeper shouldn't perform any processing related to the application or services, or access any data.</span></span> <span data-ttu-id="f3f93-138">它單純用來驗證和處理要求。</span><span class="sxs-lookup"><span data-stu-id="f3f93-138">Its function is purely to validate and sanitize requests.</span></span> <span data-ttu-id="f3f93-139">信任主機可能需要額外執行要求驗證，但核心驗證應由閘道管理員執行。</span><span class="sxs-lookup"><span data-stu-id="f3f93-139">The trusted hosts might need to perform additional validation of requests, but the core validation should be performed by the gatekeeper.</span></span>
- <span data-ttu-id="f3f93-140">儘可能在閘道管理員與信任主機或工作之間使用安全通訊通道 (HTTPS、SSL 或 TLS)。</span><span class="sxs-lookup"><span data-stu-id="f3f93-140">Use a secure communication channel (HTTPS, SSL, or TLS) between the gatekeeper and the trusted hosts or tasks where this is possible.</span></span> <span data-ttu-id="f3f93-141">然而，有些裝載環境在內部端點上並不支援 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="f3f93-141">However, some hosting environments don't support HTTPS on internal endpoints.</span></span>
- <span data-ttu-id="f3f93-142">對應用程式另外增加實作閘道管理員模式的一層，會需要額外的處理與網路通訊而可能對效能產生一些影響。</span><span class="sxs-lookup"><span data-stu-id="f3f93-142">Adding the extra layer to the application to implement the gatekeeper pattern is likely to have some impact on performance due to the additional processing and network communication it requires.</span></span>
- <span data-ttu-id="f3f93-143">閘道管理員執行個體可能是單一失敗點。</span><span class="sxs-lookup"><span data-stu-id="f3f93-143">The gatekeeper instance could be a single point of failure.</span></span> <span data-ttu-id="f3f93-144">若要將失敗的影響降到最低，請考慮部署額外的執行個體，並使用自動調整規模機制，以確保容量能夠維持可用性。</span><span class="sxs-lookup"><span data-stu-id="f3f93-144">To minimize the impact of a failure, consider deploying additional instances and using an autoscaling mechanism to ensure capacity to maintain availability.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="f3f93-145">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="f3f93-145">When to use this pattern</span></span>

<span data-ttu-id="f3f93-146">這種模式在下列情況中非常有用：</span><span class="sxs-lookup"><span data-stu-id="f3f93-146">This pattern is useful for:</span></span>

- <span data-ttu-id="f3f93-147">處理機密資訊、公開必須提供高度保護以抵禦惡意攻擊的服務，或執行不應中斷之關鍵任務作業的的應用程式。</span><span class="sxs-lookup"><span data-stu-id="f3f93-147">Applications that handle sensitive information, expose services that must have a high degree of protection from malicious attacks, or perform mission-critical operations that shouldn't be disrupted.</span></span>
- <span data-ttu-id="f3f93-148">有必要和主要工作分開執行要求驗證，或集中管理此驗證以簡化維護和系統管理的分散式應用程式。</span><span class="sxs-lookup"><span data-stu-id="f3f93-148">Distributed applications where it's necessary to perform request validation separately from the main tasks, or to centralize this validation to simplify maintenance and administration.</span></span>

## <a name="example"></a><span data-ttu-id="f3f93-149">範例</span><span class="sxs-lookup"><span data-stu-id="f3f93-149">Example</span></span>

<span data-ttu-id="f3f93-150">在雲端裝載案例中，可以透過將閘道管理員角色或虛擬機器從信任角色與應用程式中的服務分離，來實作此模式。</span><span class="sxs-lookup"><span data-stu-id="f3f93-150">In a cloud-hosted scenario, this pattern can be implemented by decoupling the gatekeeper role or virtual machine from the trusted roles and services in an application.</span></span> <span data-ttu-id="f3f93-151">使用內部端點、佇列或儲存體作為中繼通訊機制，即可達成此目的。</span><span class="sxs-lookup"><span data-stu-id="f3f93-151">Do this by using an internal endpoint, a queue, or storage as an intermediate communication mechanism.</span></span> <span data-ttu-id="f3f93-152">下圖示範使用內部端點。</span><span class="sxs-lookup"><span data-stu-id="f3f93-152">The figure illustrates using an internal endpoint.</span></span>

![使用雲端服務 Web 和背景工作角色的模式範例](./_images/gatekeeper-endpoint.png)

## <a name="related-patterns"></a><span data-ttu-id="f3f93-154">相關的模式</span><span class="sxs-lookup"><span data-stu-id="f3f93-154">Related patterns</span></span>

<span data-ttu-id="f3f93-155">實作閘道管理員模式時，也可能與[有限權限金鑰模式](./valet-key.md)相關。</span><span class="sxs-lookup"><span data-stu-id="f3f93-155">The [Valet Key pattern](./valet-key.md) might also be relevant when implementing the Gatekeeper pattern.</span></span> <span data-ttu-id="f3f93-156">在閘道管理員與信任的角色之間通訊時，最好使用可限制存取資源權限的金鑰或權杖，來增強安全性。</span><span class="sxs-lookup"><span data-stu-id="f3f93-156">When communicating between the Gatekeeper and trusted roles it's good practice to enhance security by using keys or tokens that limit permissions for accessing resources.</span></span> <span data-ttu-id="f3f93-157">描述如何使用權杖或金鑰為用戶端提供特定資源或服務的受限制直接存取。</span><span class="sxs-lookup"><span data-stu-id="f3f93-157">Describes how to use a token or key that provides clients with restricted direct access to a specific resource or service.</span></span>

---
title: 安全性模式
titleSuffix: Cloud Design Patterns
description: 安全性是指系統有能力防止正確用法以外的惡意或意外動作，並且防止洩漏或遺失資訊。 雲端應用程式皆暴露在受信任內部部署範圍外的網際網路上，且開放給大眾使用，也可能服務不受信任的使用者。 應用程式的設計和部署方式應要能保護應用程式不受到惡意攻擊、僅限核准的使用者可存取應用程式，以及保護敏感性資料。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: c18255dccdbd8659705884a4141f5ecd099d9ece
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58248339"
---
# <a name="security-patterns"></a><span data-ttu-id="73aaa-106">安全性模式</span><span class="sxs-lookup"><span data-stu-id="73aaa-106">Security patterns</span></span>

[!INCLUDE [header](../../_includes/header.md)]

<span data-ttu-id="73aaa-107">安全性是指系統有能力防止正確用法以外的惡意或意外動作，並且防止洩漏或遺失資訊。</span><span class="sxs-lookup"><span data-stu-id="73aaa-107">Security is the capability of a system to prevent malicious or accidental actions outside of the designed usage, and to prevent disclosure or loss of information.</span></span> <span data-ttu-id="73aaa-108">雲端應用程式皆暴露在受信任內部部署範圍外的網際網路上，且開放給大眾使用，也可能服務不受信任的使用者。</span><span class="sxs-lookup"><span data-stu-id="73aaa-108">Cloud applications are exposed on the Internet outside trusted on-premises boundaries, are often open to the public, and may serve untrusted users.</span></span> <span data-ttu-id="73aaa-109">應用程式的設計和部署方式應要能保護應用程式不受到惡意攻擊、僅限核准的使用者可存取應用程式，以及保護敏感性資料。</span><span class="sxs-lookup"><span data-stu-id="73aaa-109">Applications must be designed and deployed in a way that protects them from malicious attacks, restricts access to only approved users, and protects sensitive data.</span></span>

|                    <span data-ttu-id="73aaa-110">模式</span><span class="sxs-lookup"><span data-stu-id="73aaa-110">Pattern</span></span>                     |                                                                                                         <span data-ttu-id="73aaa-111">總結</span><span class="sxs-lookup"><span data-stu-id="73aaa-111">Summary</span></span>                                                                                                         |
|------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [<span data-ttu-id="73aaa-112">同盟身分識別</span><span class="sxs-lookup"><span data-stu-id="73aaa-112">Federated Identity</span></span>](../federated-identity.md) |                                                                                <span data-ttu-id="73aaa-113">將驗證委派給外部身分識別提供者。</span><span class="sxs-lookup"><span data-stu-id="73aaa-113">Delegate authentication to an external identity provider.</span></span>                                                                                |
|         [<span data-ttu-id="73aaa-114">閘道管理員</span><span class="sxs-lookup"><span data-stu-id="73aaa-114">Gatekeeper</span></span>](../gatekeeper.md)         | <span data-ttu-id="73aaa-115">保護應用程式和服務，方法是使用專用的主機執行個體，其會作為用戶端和應用程式或服務之間的代理程式、會驗證和處理要求，並在兩者之間傳遞要求和資料。</span><span class="sxs-lookup"><span data-stu-id="73aaa-115">Protect applications and services by using a dedicated host instance that acts as a broker between clients and the application or service, validates and sanitizes requests, and passes requests and data between them.</span></span> |
|          [<span data-ttu-id="73aaa-116">Valet 金鑰</span><span class="sxs-lookup"><span data-stu-id="73aaa-116">Valet Key</span></span>](../valet-key.md)          |                                                        <span data-ttu-id="73aaa-117">使用可提供用戶端對特定資源或服務受限制的直接存取的權杖或金鑰。</span><span class="sxs-lookup"><span data-stu-id="73aaa-117">Use a token or key that provides clients with restricted direct access to a specific resource or service.</span></span>                                                        |

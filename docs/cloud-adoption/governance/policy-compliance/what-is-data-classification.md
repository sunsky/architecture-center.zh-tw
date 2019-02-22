---
title: CAF：什麼是資料分類
description: 什麼是資料分類？
author: BrianBlanchard
ms.date: 2/11/2019
ms.openlocfilehash: 07268e7242d92ac2581bf28b378a3c43d166620c
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900900"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-is-data-classification"></a><span data-ttu-id="bb234-103">什麼是資料分類？</span><span class="sxs-lookup"><span data-stu-id="bb234-103">What is data classification?</span></span>

<span data-ttu-id="bb234-104">這是資料分類一般主題的簡介文章。</span><span class="sxs-lookup"><span data-stu-id="bb234-104">This is an introductory article on the general topic of Data Classification.</span></span> <span data-ttu-id="bb234-105">所有治理一般從資料分類開始。</span><span class="sxs-lookup"><span data-stu-id="bb234-105">Data classification is a very common starting point for all governance.</span></span>

## <a name="business-risks-and-governance"></a><span data-ttu-id="bb234-106">商務風險和治理</span><span class="sxs-lookup"><span data-stu-id="bb234-106">Business risks and governance</span></span>

<span data-ttu-id="bb234-107">在大多數組織中，投資治理的主要原因可以歸納為三種商務風險：</span><span class="sxs-lookup"><span data-stu-id="bb234-107">In most organizations, the primary reasons for investing in governance can be reduced to three business risks:</span></span>

* <span data-ttu-id="bb234-108">與資料安全性缺口建立關聯的責任</span><span class="sxs-lookup"><span data-stu-id="bb234-108">Liability associated with data breaches</span></span>
* <span data-ttu-id="bb234-109">中斷導致業務停擺</span><span class="sxs-lookup"><span data-stu-id="bb234-109">Interruption to the business from outages</span></span>
* <span data-ttu-id="bb234-110">非計劃性或非預期的費用</span><span class="sxs-lookup"><span data-stu-id="bb234-110">Unplanned or unexpected spending</span></span>

<span data-ttu-id="bb234-111">這三種商務風險有很多種變化。</span><span class="sxs-lookup"><span data-stu-id="bb234-111">There are many variants of these three business risks.</span></span> <span data-ttu-id="bb234-112">不過，這個趨勢最常見。</span><span class="sxs-lookup"><span data-stu-id="bb234-112">However, the tend to be the most common.</span></span>

## <a name="understand-then-mitigate"></a><span data-ttu-id="bb234-113">了解然後降低</span><span class="sxs-lookup"><span data-stu-id="bb234-113">Understand then mitigate</span></span>

<span data-ttu-id="bb234-114">降低任何風險之前，必須先了解風險。</span><span class="sxs-lookup"><span data-stu-id="bb234-114">Before any risk can be mitigated, it must be understood.</span></span> <span data-ttu-id="bb234-115">對於資料安全性缺口責任，必須先了解資料分類。</span><span class="sxs-lookup"><span data-stu-id="bb234-115">In the case of data breach liability, that understanding starts with data classification.</span></span> <span data-ttu-id="bb234-116">資料分類是將中繼資料特性與數位資產中的每個資產建立關聯的過程，這能夠識別與該資產建立關聯的資料類型。</span><span class="sxs-lookup"><span data-stu-id="bb234-116">Data classification is the process of associating a meta data characteristic to every asset in a digital estate, which identifies the type of data associated with that asset.</span></span>

<span data-ttu-id="bb234-117">Microsoft 建議已識別將移轉或部署到雲端的任何潛在候選資產都應該記錄中繼資料，以便記錄資料分類、業務重要性和帳單責任。</span><span class="sxs-lookup"><span data-stu-id="bb234-117">Microsoft suggests that any asset which has been identified as a potential candidate for migration or deployment to the cloud should have documented meta data to record the data classification, business criticality, and billing responsibility.</span></span> <span data-ttu-id="bb234-118">這三個分類點對於了解和降低風險很有幫助。</span><span class="sxs-lookup"><span data-stu-id="bb234-118">These three points of classification can go a long way to understanding and mitigating risks.</span></span>

## <a name="microsofts-data-classification"></a><span data-ttu-id="bb234-119">Microsoft 的資料分類</span><span class="sxs-lookup"><span data-stu-id="bb234-119">Microsoft's data classification</span></span>

<span data-ttu-id="bb234-120">下列是 Microsoft 使用的分類清單。</span><span class="sxs-lookup"><span data-stu-id="bb234-120">The following is a list of classifications Microsoft uses.</span></span> <span data-ttu-id="bb234-121">根據您的產業或現有安全性需求，組織中可能已存在資料分類標準。</span><span class="sxs-lookup"><span data-stu-id="bb234-121">Depending on your industry or existing security requirements, data classifications standards may already exist within your organization.</span></span> <span data-ttu-id="bb234-122">如果沒有標準，歡迎您使用此範例分類，以便您更充分了解您的數位資產和風險狀況。</span><span class="sxs-lookup"><span data-stu-id="bb234-122">If no standard exists, we welcome you to use this sample classification, to help you better understand your digital estate and risk profile.</span></span>  

* <span data-ttu-id="bb234-123">**非商務：** 您的個人生活中不屬於 Microsoft 的資料</span><span class="sxs-lookup"><span data-stu-id="bb234-123">**Non-Business:** Data from your personal life that does not belong to Microsoft</span></span>
* <span data-ttu-id="bb234-124">**公用︰** 已公開提供且經核准可供公眾使用的商務資料。</span><span class="sxs-lookup"><span data-stu-id="bb234-124">**Public:** Business data that is freely available and approved for public consumption</span></span>
* <span data-ttu-id="bb234-125">**一般：** 不對外公開的業務資料</span><span class="sxs-lookup"><span data-stu-id="bb234-125">**General:** Business data that is not meant for a public audience</span></span>
* <span data-ttu-id="bb234-126">**機密：** 過度共用可能對 Microsoft 造成危害的業務資料</span><span class="sxs-lookup"><span data-stu-id="bb234-126">**Confidential:** Business data that could cause harm to Microsoft if over-shared</span></span>
* <span data-ttu-id="bb234-127">**高度機密：** 過度共用可能對 Microsoft 造成嚴重危害的業務資料</span><span class="sxs-lookup"><span data-stu-id="bb234-127">**Highly Confidential:** Business data that would cause extensive harm to Microsoft if over-shared</span></span>

## <a name="tagging-data-classification-in-azure"></a><span data-ttu-id="bb234-128">在 Azure 中標記資料分類</span><span class="sxs-lookup"><span data-stu-id="bb234-128">Tagging data classification in Azure</span></span>

<span data-ttu-id="bb234-129">每個雲端服務提供者都應提供用於記錄任何資產中繼資料的機制。</span><span class="sxs-lookup"><span data-stu-id="bb234-129">Every cloud provider should offer a mechanism for recording metadata about any asset.</span></span> <span data-ttu-id="bb234-130">中繼資料對於管理雲端中的資產極為重要。</span><span class="sxs-lookup"><span data-stu-id="bb234-130">Metadata is vital to managing assets in the cloud.</span></span> <span data-ttu-id="bb234-131">對於 Azure，資源標記是中繼資料儲存體的建議方法。</span><span class="sxs-lookup"><span data-stu-id="bb234-131">In the case of Azure, resource tags are the suggested approach for metadata storage.</span></span> <span data-ttu-id="bb234-132">如需 Azure 資源標記的詳細資訊，請參閱[使用標記來組織您的 Azure 資源](/azure/azure-resource-manager/resource-group-using-tags)一文。</span><span class="sxs-lookup"><span data-stu-id="bb234-132">For additional information on resource tagging in Azure, see the article on [Using tags to organize your Azure resources](/azure/azure-resource-manager/resource-group-using-tags).</span></span>

## <a name="next-steps"></a><span data-ttu-id="bb234-133">後續步驟</span><span class="sxs-lookup"><span data-stu-id="bb234-133">Next steps</span></span>

<span data-ttu-id="bb234-134">在其中一個可採取動作的治理程序中運用資料分類。</span><span class="sxs-lookup"><span data-stu-id="bb234-134">Apply data classifications during one of the actionable governance journeys.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="bb234-135">開始可採取動作的治理程序</span><span class="sxs-lookup"><span data-stu-id="bb234-135">Begin an Actionable Governance Journeys</span></span>](../journeys/overview.md)

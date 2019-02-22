---
title: CAF：收集數位資產的清查資料
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: 收集數位資產清查的方式。
author: BrianBlanchard
ms.date: 12/10/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.openlocfilehash: 0c68ff1e5ff51395698d37fb9b59c7a76c9479b7
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55897247"
---
# <a name="gather-inventory-data-for-a-digital-estate"></a><span data-ttu-id="63ed9-103">收集數位資產的清查資料</span><span class="sxs-lookup"><span data-stu-id="63ed9-103">Gather inventory data for a digital estate</span></span>

<span data-ttu-id="63ed9-104">開發清查是[數位資產規劃](overview.md)的第一個步驟。</span><span class="sxs-lookup"><span data-stu-id="63ed9-104">Developing an inventory is the first step in [Digital Estate Planning](overview.md).</span></span> <span data-ttu-id="63ed9-105">在此程序中，將會收集支援特定企業功能的 IT 資產清單，以供日後分析與合理化。</span><span class="sxs-lookup"><span data-stu-id="63ed9-105">In this process, a list of IT assets that support specific business functions would be collected for later analysis and rationalization.</span></span> <span data-ttu-id="63ed9-106">本文假設以由下而上的方式進行分析最符合規劃需求。</span><span class="sxs-lookup"><span data-stu-id="63ed9-106">This article assumes that a bottom-up approach to analysis is most appropriate for planning needs.</span></span> <span data-ttu-id="63ed9-107">如需詳細資訊，請參閱[數位資產規劃方法](./approach.md)。</span><span class="sxs-lookup"><span data-stu-id="63ed9-107">For more information, see [Approaches to digital estate planning](./approach.md).</span></span>

## <a name="take-inventory-of-a-digital-estate"></a><span data-ttu-id="63ed9-108">清查數位資產</span><span class="sxs-lookup"><span data-stu-id="63ed9-108">Take inventory of a digital estate</span></span>

<span data-ttu-id="63ed9-109">支援數位資產的清查會隨著所需數位轉型和對應的轉型程序而不同。</span><span class="sxs-lookup"><span data-stu-id="63ed9-109">The inventory supporting a digital estate changes depending on the desired digital transformation and corresponding transformation journey.</span></span>

- <span data-ttu-id="63ed9-110">**營運轉型**。</span><span class="sxs-lookup"><span data-stu-id="63ed9-110">**Operational transformation**.</span></span> <span data-ttu-id="63ed9-111">在營運轉型的過程中，一般通常會建議從掃描工具收集清查資料，因為這些工具可統合建立所有 VM 和伺服器的清單。</span><span class="sxs-lookup"><span data-stu-id="63ed9-111">During an operational transformation, it is often advised that the inventory be collected from scanning tools which can create a centralized list of all VMs and servers.</span></span> <span data-ttu-id="63ed9-112">有些工具也可建立網路對應和相依性，這將有助於定義工作負載的調整。</span><span class="sxs-lookup"><span data-stu-id="63ed9-112">Some tools can also create network mappings and dependencies, which will help define workload alignment.</span></span>

- <span data-ttu-id="63ed9-113">**漸進式轉型。**</span><span class="sxs-lookup"><span data-stu-id="63ed9-113">**Incremental transformation.**</span></span> <span data-ttu-id="63ed9-114">漸進式轉型的清查會從客戶開始。</span><span class="sxs-lookup"><span data-stu-id="63ed9-114">Inventory for an incremental transformation begins with the customer.</span></span> <span data-ttu-id="63ed9-115">對應出客戶從頭到尾的體驗，是不錯的著手之處。</span><span class="sxs-lookup"><span data-stu-id="63ed9-115">Mapping the customer experience from start to finish is a good place to begin.</span></span> <span data-ttu-id="63ed9-116">對應至應用程式、API、資料和其他資產的調整，將可建立詳細的清查資料以供分析之用。</span><span class="sxs-lookup"><span data-stu-id="63ed9-116">Aligning that map to applications, APIs, data, and other assets will create a detailed inventory for analysis.</span></span>

- <span data-ttu-id="63ed9-117">**革新式轉型。**</span><span class="sxs-lookup"><span data-stu-id="63ed9-117">**Disruptive transformation.**</span></span> <span data-ttu-id="63ed9-118">革新式轉型著重於產品或服務。</span><span class="sxs-lookup"><span data-stu-id="63ed9-118">Disruptive transformation focuses on the product or service.</span></span> <span data-ttu-id="63ed9-119">據此，清查將會對應出足以衝擊市場的機會以及所需的能力。</span><span class="sxs-lookup"><span data-stu-id="63ed9-119">From there an inventory would include a mapping of the opportunities to disrupt the market and the capabilities needed.</span></span>

## <a name="accuracy-and-completeness-of-an-inventory"></a><span data-ttu-id="63ed9-120">清查的精確度和完整性</span><span class="sxs-lookup"><span data-stu-id="63ed9-120">Accuracy and completeness of an inventory</span></span>

<span data-ttu-id="63ed9-121">清查鮮少能靠一次反覆執行就完成。</span><span class="sxs-lookup"><span data-stu-id="63ed9-121">An inventory is seldom fully complete in its first iteration.</span></span> <span data-ttu-id="63ed9-122">我們強烈建議，雲端策略小組的各種成員應與專案關係人和進階使用者合作，以驗證清查。</span><span class="sxs-lookup"><span data-stu-id="63ed9-122">It is highly advised that various members of the Cloud Strategy team align stakeholders and power users to validate the inventory.</span></span> <span data-ttu-id="63ed9-123">如果可能的話，也可以使用網路和相依性分析等其他工具來識別正在傳送流量、但不在清查中的資產。</span><span class="sxs-lookup"><span data-stu-id="63ed9-123">When possible, additional tools like network and dependency analysis can be used to identify assets that are being sent traffic, but are not in the inventory.</span></span>

## <a name="next-steps"></a><span data-ttu-id="63ed9-124">後續步驟</span><span class="sxs-lookup"><span data-stu-id="63ed9-124">Next steps</span></span>

<span data-ttu-id="63ed9-125">清查資料在經過編譯和驗證後，即可進行合理化。</span><span class="sxs-lookup"><span data-stu-id="63ed9-125">Once an inventory is compiled and validated, it can rationalized.</span></span> <span data-ttu-id="63ed9-126">清查合理化是數位資產規劃的下一個步驟。</span><span class="sxs-lookup"><span data-stu-id="63ed9-126">Inventory Rationalization is the next step to digital estate planning.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="63ed9-127">合理化數位資產</span><span class="sxs-lookup"><span data-stu-id="63ed9-127">Rationalize the digital estate</span></span>](rationalize.md)
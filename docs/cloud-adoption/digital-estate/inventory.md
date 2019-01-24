---
title: 收集數位資產的清查資料
titleSuffix: Enterprise Cloud Adoption
description: 如何建立數位資產的清查
author: BrianBlanchard
ms.date: 12/10/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.openlocfilehash: 5c2f270cf8de81c8a94d1f924f51611e657ed0ed
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54481791"
---
# <a name="enterprise-cloud-adoption-gather-inventory-data-for-a-digital-estate"></a><span data-ttu-id="47d3c-103">企業雲端採用：收集數位資產的清查資料</span><span class="sxs-lookup"><span data-stu-id="47d3c-103">Enterprise Cloud Adoption: Gather inventory data for a digital estate</span></span>

<span data-ttu-id="47d3c-104">開發清查是[數位資產規劃](overview.md)的第一個步驟。</span><span class="sxs-lookup"><span data-stu-id="47d3c-104">Developing an inventory is the first step in [Digital Estate Planning](overview.md).</span></span> <span data-ttu-id="47d3c-105">在此程序中，將會收集支援特定企業功能的 IT 資產清單，以供日後分析與合理化。</span><span class="sxs-lookup"><span data-stu-id="47d3c-105">In this process, a list of IT assets that support specific business functions would be collected for later analysis and rationalization.</span></span> <span data-ttu-id="47d3c-106">本文假設以由下而上的方式進行分析最符合規劃需求。</span><span class="sxs-lookup"><span data-stu-id="47d3c-106">This article assumes that a bottom-up approach to analysis is most appropriate for planning needs.</span></span> <span data-ttu-id="47d3c-107">如需詳細資訊，請參閱[數位資產規劃方法](./approach.md)。</span><span class="sxs-lookup"><span data-stu-id="47d3c-107">For more information, see [Approaches to digital estate planning](./approach.md).</span></span>

## <a name="how-can-a-digital-estate-be-inventoried"></a><span data-ttu-id="47d3c-108">如何清查數位資產？</span><span class="sxs-lookup"><span data-stu-id="47d3c-108">How can a digital estate be inventoried?</span></span>

<span data-ttu-id="47d3c-109">支援數位資產的清查會隨著所需的數位轉型和對應的轉型過程而不同。</span><span class="sxs-lookup"><span data-stu-id="47d3c-109">The inventory supporting a digital estate changes depending on the desired Digital Transformation and corresponding Transformation Journey.</span></span>

- <span data-ttu-id="47d3c-110">營運轉型：在營運轉型的過程中，一般通常會建議從掃描工具收集清查資料，因為這些工具可統合建立所有 VM 和伺服器的清單。</span><span class="sxs-lookup"><span data-stu-id="47d3c-110">Operational Transformation: During an operational transformation, it is often advised that the inventory be collected from scanning tools which can create a centralized list of all VMs and servers.</span></span> <span data-ttu-id="47d3c-111">有些工具也可建立網路對應和相依性，這將有助於定義工作負載的調整。</span><span class="sxs-lookup"><span data-stu-id="47d3c-111">Some tools can also create network mappings and dependencies, which will help define workload alignment.</span></span>

- <span data-ttu-id="47d3c-112">漸進式轉型：漸進式轉型的清查會從客戶開始。</span><span class="sxs-lookup"><span data-stu-id="47d3c-112">Incremental Transformation: Inventory for an incremental transformation begins with the customer.</span></span> <span data-ttu-id="47d3c-113">對應出客戶從頭到尾的體驗，是不錯的著手之處。</span><span class="sxs-lookup"><span data-stu-id="47d3c-113">Mapping the customer experience from start to finish is a good place to begin.</span></span> <span data-ttu-id="47d3c-114">對應至應用程式、API、資料和其他資產的調整，將可建立詳細的清查資料以供分析之用。</span><span class="sxs-lookup"><span data-stu-id="47d3c-114">Aligning that map to applications, APIs, data, and other assets will create a detailed inventory for analysis.</span></span>

- <span data-ttu-id="47d3c-115">革新式轉型：革新式轉型著重於產品或服務。</span><span class="sxs-lookup"><span data-stu-id="47d3c-115">Disruptive Transformation: Disruptive transformation focuses on the product or service.</span></span> <span data-ttu-id="47d3c-116">據此，清查將會對應出足以衝擊市場的機會以及所需的能力。</span><span class="sxs-lookup"><span data-stu-id="47d3c-116">From there an inventory would include a mapping of the opportunities to disrupt the market and the capabilities needed.</span></span>

## <a name="accuracy-and-completeness-of-an-inventory"></a><span data-ttu-id="47d3c-117">清查的精確度和完整性</span><span class="sxs-lookup"><span data-stu-id="47d3c-117">Accuracy and completeness of an inventory</span></span>

<span data-ttu-id="47d3c-118">清查鮮少能靠一次反覆執行就完成。</span><span class="sxs-lookup"><span data-stu-id="47d3c-118">An inventory is seldom fully complete in its first iteration.</span></span> <span data-ttu-id="47d3c-119">我們強烈建議，雲端策略小組的各種成員應與專案關係人和進階使用者合作，以驗證清查。</span><span class="sxs-lookup"><span data-stu-id="47d3c-119">It is highly advised that various members of the Cloud Strategy team align stakeholders and power users to validate the inventory.</span></span> <span data-ttu-id="47d3c-120">如果可能的話，也可以使用網路和相依性分析等其他工具來識別正在傳送流量、但不在清查中的資產。</span><span class="sxs-lookup"><span data-stu-id="47d3c-120">When possible, additional tools like network and dependency analysis can be used to identify assets that are being sent traffic, but are not in the inventory.</span></span>

## <a name="next-steps"></a><span data-ttu-id="47d3c-121">後續步驟</span><span class="sxs-lookup"><span data-stu-id="47d3c-121">Next steps</span></span>

<span data-ttu-id="47d3c-122">清查資料在經過編譯和驗證後，即可進行合理化。</span><span class="sxs-lookup"><span data-stu-id="47d3c-122">Once an inventory is compiled and validated, it can rationalized.</span></span> <span data-ttu-id="47d3c-123">清查合理化是數位資產規劃的下一個步驟。</span><span class="sxs-lookup"><span data-stu-id="47d3c-123">Inventory Rationalization is the next step to digital estate planning.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="47d3c-124">合理化數位資產</span><span class="sxs-lookup"><span data-stu-id="47d3c-124">Rationalize the digital estate</span></span>](rationalize.md)
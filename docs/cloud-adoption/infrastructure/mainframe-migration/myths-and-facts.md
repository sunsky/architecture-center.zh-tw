---
title: 大型主機移轉：迷思與事實
description: 將應用程式從大型主機環境遷移至 Azure，這是經過實證、高度可用且可調整的基礎結構，適用於目前在大型主機上執行的系統。
author: njray
ms.date: 12/27/2018
ms.openlocfilehash: bcad01ec044d2d802b055e328a9496aae7b33311
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241539"
---
# <a name="mainframe-myths-and-facts"></a><span data-ttu-id="eb349-103">大型主機迷思與事實</span><span class="sxs-lookup"><span data-stu-id="eb349-103">Mainframe myths and facts</span></span>

<span data-ttu-id="eb349-104">大型主機在計算歷史中特別突出且重要，而且對非常特定的工作負載持續保持可行。</span><span class="sxs-lookup"><span data-stu-id="eb349-104">Mainframes figure prominently in the history of computing and remain viable for highly specific workloads.</span></span> <span data-ttu-id="eb349-105">多數人同意大型主機是經過實證的平台，具有悠久的作業程序，造就其可靠且穩固的環境。</span><span class="sxs-lookup"><span data-stu-id="eb349-105">Most agree that mainframes are a proven platform with long-established operating procedures that make them reliable, robust environments.</span></span> <span data-ttu-id="eb349-106">軟體根據使用情況執行 (以每秒百萬個指令 (MIPS) 測量)，且針對背後計費提供大量使用情況報告。</span><span class="sxs-lookup"><span data-stu-id="eb349-106">Software runs based on usage, measured in million instructions per second (MIPS), and extensive usage reports are available for charge backs.</span></span>

<span data-ttu-id="eb349-107">大型主機的可靠性、可用性和處理能力佔據迷思大部分的比例。</span><span class="sxs-lookup"><span data-stu-id="eb349-107">The reliability, availability, and processing power of mainframes have taken on almost mythical proportions.</span></span> <span data-ttu-id="eb349-108">為了評估最適合 Azure 的大型主機工作負載，您會想要先區別迷思與現實。</span><span class="sxs-lookup"><span data-stu-id="eb349-108">To evaluate the mainframe workloads that are most suitable for Azure, you first want to distinguish the myths from the reality.</span></span>

## <a name="myth-mainframes-never-go-down-and-have-a-minimum-of-five-9s-of-availability"></a><span data-ttu-id="eb349-109">迷思：大型主機永遠不會停機，且至少有五個 9 的可用性</span><span class="sxs-lookup"><span data-stu-id="eb349-109">Myth: Mainframes never go down and have a minimum of five 9s of availability</span></span>

<span data-ttu-id="eb349-110">大型主機硬體和作業系統被視為可靠又穩定。</span><span class="sxs-lookup"><span data-stu-id="eb349-110">Mainframe hardware and operating systems are viewed as reliable and stable.</span></span> <span data-ttu-id="eb349-111">但事實上為了維護及重新開機 (稱為初始程式載入或 IPL)，還是必須排程停機時間。</span><span class="sxs-lookup"><span data-stu-id="eb349-111">But the reality is that downtime must be scheduled for maintenance and reboots (referred to as initial program loads or IPLs).</span></span> <span data-ttu-id="eb349-112">當考慮到這些工作時，大型主機解決方案的可用性通常更貼近兩個或三個 9，這等同於高階的 Intel 型伺服器。</span><span class="sxs-lookup"><span data-stu-id="eb349-112">When these tasks are considered, a mainframe solution often has closer to two or three 9s of availability, which is equivalent to that of high-end, Intel-based servers.</span></span>

<span data-ttu-id="eb349-113">大型主機也與任何其他伺服器一樣易受災害損害，因此需要不斷電供應系統 (UPS) 來處理這些類故障。</span><span class="sxs-lookup"><span data-stu-id="eb349-113">Mainframes also remain as vulnerable to disasters as any other servers do, and require uninterruptible power supply (UPS) systems to handle these types of failures.</span></span>

## <a name="myth-mainframes-have-limitless-scalability"></a><span data-ttu-id="eb349-114">迷思：大型主機有無限的延展性</span><span class="sxs-lookup"><span data-stu-id="eb349-114">Myth: Mainframes have limitless scalability</span></span>

<span data-ttu-id="eb349-115">大型主機的延展性取決於其系統軟體，例如客戶資訊控制系統 (CICS)，以及大型主機引擎和儲存體之新執行個體的容量。</span><span class="sxs-lookup"><span data-stu-id="eb349-115">A mainframe’s scalability depends on the capacity of its system software, such as the customer information control system (CICS), and the capacity of new instances of mainframe engines and storage.</span></span> <span data-ttu-id="eb349-116">某些使用大型主機的大型公司已針對效能自訂其 CICS，並在其他方面已超越最大型可取得主機的功能。</span><span class="sxs-lookup"><span data-stu-id="eb349-116">Some large companies that use mainframes have customized their CICS for performance, and have otherwise outgrown the capability of the largest available mainframes.</span></span>

## <a name="myth-intel-based-servers-are-not-as-powerful-as-mainframes"></a><span data-ttu-id="eb349-117">迷思：Intel 型伺服器不如大型主機強大</span><span class="sxs-lookup"><span data-stu-id="eb349-117">Myth: Intel-based servers are not as powerful as mainframes</span></span>

<span data-ttu-id="eb349-118">新的核心密集 Intel 系統的計算容量與大型主機一樣。</span><span class="sxs-lookup"><span data-stu-id="eb349-118">The new core-dense, Intel-based systems have as much compute capacity as mainframes.</span></span>

## <a name="myth-the-cloud-cannot-accommodate-mission-critical-applications-for-large-companies-such-as-financial-institutions"></a><span data-ttu-id="eb349-119">迷思：雲端無法容納金融機構等大型公司的任務關鍵應用程式</span><span class="sxs-lookup"><span data-stu-id="eb349-119">Myth: The cloud cannot accommodate mission-critical applications for large companies, such as financial institutions</span></span>

<span data-ttu-id="eb349-120">雖然有些雲端解決方案不適用的個案，但通常是因為該應用程式演算法無法分散。</span><span class="sxs-lookup"><span data-stu-id="eb349-120">Although there may be some isolated instances where cloud solutions fall short, it is usually becuase the application algorithms cannot be distributed.</span></span> <span data-ttu-id="eb349-121">這些範例是例外狀況，而不是規則。</span><span class="sxs-lookup"><span data-stu-id="eb349-121">These few examples are the exceptions, not the rule.</span></span>

## <a name="summary"></a><span data-ttu-id="eb349-122">總結</span><span class="sxs-lookup"><span data-stu-id="eb349-122">Summary</span></span>

<span data-ttu-id="eb349-123">相較之下，Azure 提供相當於大型主機性能與功能的替代平台，且其成本更低。</span><span class="sxs-lookup"><span data-stu-id="eb349-123">By comparison, Azure offers  an alternative platform that is capable of delivering equivalent mainframe functionality and features, and at a much lower cost.</span></span> <span data-ttu-id="eb349-124">此外，雲端的訂閱型、使用量導向的成本模型之擁有權成本 (TCO) 的總成本遠低於大型主機電腦。</span><span class="sxs-lookup"><span data-stu-id="eb349-124">In addition, the total cost of ownership (TCO) of the cloud’s subscription-based, usage-driven cost model is far less expensive than mainframe computers.</span></span>

## <a name="next-steps"></a><span data-ttu-id="eb349-125">後續步驟</span><span class="sxs-lookup"><span data-stu-id="eb349-125">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="eb349-126">從大型主機切換至 Azure</span><span class="sxs-lookup"><span data-stu-id="eb349-126">Make the Switch from Mainframes to Azure</span></span>](migration-strategies.md)

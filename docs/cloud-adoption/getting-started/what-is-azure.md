---
title: CAF：Azure 如何運作？
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
description: Azure 內部運作方式的說明
author: petertaylor9999
ms.date: 02/11/2019
ms.openlocfilehash: 724d16a810865dd947a7ade34766818c8ea525a1
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58245269"
---
<!-- markdownlint-disable MD026 -->

# <a name="how-does-azure-work"></a><span data-ttu-id="a1913-103">Azure 如何運作？</span><span class="sxs-lookup"><span data-stu-id="a1913-103">How does Azure work?</span></span>

<span data-ttu-id="a1913-104">Azure 是 Microsoft 的公用雲端平台。</span><span class="sxs-lookup"><span data-stu-id="a1913-104">Azure is Microsoft's public cloud platform.</span></span> <span data-ttu-id="a1913-105">Azure 提供多種不同的服務，包括平台即服務 (PaaS)、基礎結構即服務 (IaaS)、資料庫即服務 (DBaaS) 等等。</span><span class="sxs-lookup"><span data-stu-id="a1913-105">Azure offers a large collection of services including platform as a service (PaaS), infrastructure as a service (IaaS), database as a service (DBaaS), and many others.</span></span> <span data-ttu-id="a1913-106">但 Azure 到底是什麼？它如何運作？</span><span class="sxs-lookup"><span data-stu-id="a1913-106">But what exactly is Azure, and how does it work?</span></span>

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ixGo]

<!-- markdownlint-enable MD034 -->

<span data-ttu-id="a1913-107">Azure 就像其他雲端平台一樣，需仰賴名為**虛擬化**的技術。</span><span class="sxs-lookup"><span data-stu-id="a1913-107">Azure, like other cloud platforms, relies on a technology known as **virtualization**.</span></span> <span data-ttu-id="a1913-108">大部分的電腦硬體都可用軟體來模擬，因為大部分的電腦硬體都只是一組永久或半永久編碼在矽晶片材料中的指令。</span><span class="sxs-lookup"><span data-stu-id="a1913-108">Most computer hardware can be emulated in software, because most computer hardware is simply a set of instructions permanently or semi-permanently encoded in silicon.</span></span> <span data-ttu-id="a1913-109">使用將軟體指令對應至硬體指令的模擬層，虛擬化的硬體即可用軟體執行，如同在實際的硬體中執行一般。</span><span class="sxs-lookup"><span data-stu-id="a1913-109">Using an emulation layer that maps software instructions to hardware instructions, virtualized hardware can execute in software as if it were the actual hardware itself.</span></span>

<span data-ttu-id="a1913-110">基本上，雲端是一或多個資料中心內部代替客戶執行虛擬化硬體的一組實體伺服器。</span><span class="sxs-lookup"><span data-stu-id="a1913-110">Essentially, the cloud is a set of physical servers in one or more datacenters that execute virtualized hardware on behalf of customers.</span></span> <span data-ttu-id="a1913-111">那麼，雲端要如何同時為數百萬個客戶建立、啟動、停止及刪除數百萬個虛擬化硬體執行個體呢？</span><span class="sxs-lookup"><span data-stu-id="a1913-111">So how does the cloud create, start, stop, and delete millions of instances of virtualized hardware for millions of customers simultaneously?</span></span>

<span data-ttu-id="a1913-112">要了解這一點，就必須看看資料中心裡的硬體架構。</span><span class="sxs-lookup"><span data-stu-id="a1913-112">To understand this, let's look at the architecture of the hardware in the datacenter.</span></span>  <span data-ttu-id="a1913-113">每個資料中心都有伺服器集合設置在伺服器機架中。</span><span class="sxs-lookup"><span data-stu-id="a1913-113">Within each datacenter is a collection of servers sitting in server racks.</span></span> <span data-ttu-id="a1913-114">每個伺服器機架都包含許多伺服器**刀鋒**，以及提供網路連線的網路交換器和提供電力的配電裝置 (PDU)。</span><span class="sxs-lookup"><span data-stu-id="a1913-114">Each server rack contains many server **blades** as well as a network switch providing network connectivity and a power distribution unit (PDU) providing power.</span></span> <span data-ttu-id="a1913-115">機架有時會一起分組到較大的單位中，名為**叢集**。</span><span class="sxs-lookup"><span data-stu-id="a1913-115">Racks are sometimes grouped together in larger units known as **clusters**.</span></span>

<span data-ttu-id="a1913-116">在每個機架或叢集內，大部分的伺服器都會被指定用來代替使用者執行這些虛擬化硬體執行個體。</span><span class="sxs-lookup"><span data-stu-id="a1913-116">Within each rack or cluster, most of the servers are designated to run these virtualized hardware instances on behalf of the user.</span></span> <span data-ttu-id="a1913-117">不過，有一些伺服器則會執行名為網狀架構控制器的雲端管理軟體。</span><span class="sxs-lookup"><span data-stu-id="a1913-117">However, a number of the servers run cloud management software known as a fabric controller.</span></span> <span data-ttu-id="a1913-118">**網狀架構控制器**是一種分散式應用程式，負責執行多項工作。</span><span class="sxs-lookup"><span data-stu-id="a1913-118">The **fabric controller** is a distributed application with many responsibilities.</span></span> <span data-ttu-id="a1913-119">它會配置服務、監視伺服器及其執行之服務的健全狀況，並在伺服器故障時加以修復。</span><span class="sxs-lookup"><span data-stu-id="a1913-119">It allocates services, monitors the health of the server and the services running on it, and heals servers when they fail.</span></span>

<span data-ttu-id="a1913-120">每個網狀架構控制器執行個體都會連線至執行雲端協調流程軟體的另一組伺服器，一般稱之為**前端**。</span><span class="sxs-lookup"><span data-stu-id="a1913-120">Each instance of the fabric controller is connected to another set of servers running cloud orchestration software, typically known as a **front end**.</span></span> <span data-ttu-id="a1913-121">前端會主控 Web 服務、RESTful API，以及雲端執行的所有功能所使用的內部 Azure 資料庫。</span><span class="sxs-lookup"><span data-stu-id="a1913-121">The front end hosts the web services, RESTful APIs, and internal Azure databases used for all functions the cloud performs.</span></span>

<span data-ttu-id="a1913-122">例如，前端會主控負責處理客戶配置 Azure 資源 (例如[虛擬網路][vnet]、[虛擬機器][vms]和 [Cosmos DB][cosmosdb] 之類的服務) 之要求的服務。</span><span class="sxs-lookup"><span data-stu-id="a1913-122">For example, the front end hosts the services that handle customer requests to allocate Azure resources such as [virtual networks][vnet], [virtual machines][vms], and services like [Cosmos DB][cosmosdb].</span></span> <span data-ttu-id="a1913-123">首先，前端會驗證使用者，並確認使用者是否有權配置要求的資源。</span><span class="sxs-lookup"><span data-stu-id="a1913-123">First, the front end validates the user and verifies the user is authorized to allocate the requested resources.</span></span> <span data-ttu-id="a1913-124">如果是，前端會參照資料庫以找出具有足夠容量的伺服器機架，然後指示機架上的網狀架構控制器配置資源。</span><span class="sxs-lookup"><span data-stu-id="a1913-124">If so, the front end consults a database to locate a server rack with sufficient capacity, and then instructs the fabric controller on the rack to allocate the resource.</span></span>

<span data-ttu-id="a1913-125">因此，簡言之，Azure 就是大量伺服器和網路硬體的集合，外加一組複雜的分散式應用程式，負責協調這些伺服器上的虛擬化硬體和軟體的組態和作業。</span><span class="sxs-lookup"><span data-stu-id="a1913-125">So, very simply, Azure is a huge collection of servers and networking hardware, along with a complex set of distributed applications that orchestrate the configuration and operation of the virtualized hardware and software on those servers.</span></span> <span data-ttu-id="a1913-126">Azure 之所以功能強大，憑藉的就是此協調流程 - 使用者無須再負責硬體的維護和升級，一切都可由 Azure 在幕後代勞。</span><span class="sxs-lookup"><span data-stu-id="a1913-126">And it is this orchestration that makes Azure so powerful - users are no longer responsible for maintaining and upgrading hardware, Azure does all this behind the scenes.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a1913-127">後續步驟</span><span class="sxs-lookup"><span data-stu-id="a1913-127">Next steps</span></span>

<span data-ttu-id="a1913-128">既然您已經了解 Azure 的內部運作，請深入了解雲端資源治理。</span><span class="sxs-lookup"><span data-stu-id="a1913-128">Now that you understand the internal functioning of Azure, learn about cloud resource governance.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="a1913-129">深入了解資源管理</span><span class="sxs-lookup"><span data-stu-id="a1913-129">Learn about resource governance</span></span>](what-is-governance.md)

<!-- Links -->

[cosmosdb]: /azure/cosmos-db/introduction
[docs-add-users-to-aad]: /azure/active-directory/add-users-azure-active-directory?toc=/azure/architecture/cloud-adoption-guide/toc.json
[vms]: /azure/virtual-machines/
[vnet]: /azure/virtual-network/virtual-networks-overview

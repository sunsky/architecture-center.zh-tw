<!-- TEMPLATE FILE - DO NOT ADD METADATA -->

### <a name="governance-of-resources"></a><span data-ttu-id="fe639-101">資源治理</span><span class="sxs-lookup"><span data-stu-id="fe639-101">Governance of resources</span></span>

<span data-ttu-id="fe639-102">要在訂用帳戶間施行治理，需仰賴 Azure 藍圖和藍圖內的相關資產。</span><span class="sxs-lookup"><span data-stu-id="fe639-102">Enforcing governance across subscriptions will come from Azure Blueprints and the associated assets within the blueprint.</span></span>

1. <span data-ttu-id="fe639-103">建立名為「治理 MVP」的 Azure 藍圖。</span><span class="sxs-lookup"><span data-stu-id="fe639-103">Create an Azure Blueprint named "Governance MVP".</span></span>
    1. <span data-ttu-id="fe639-104">強制使用標準 Azure 角色。</span><span class="sxs-lookup"><span data-stu-id="fe639-104">Enforce the use of standard Azure roles.</span></span>
    2. <span data-ttu-id="fe639-105">強制使用者只能依據現有的 RBAC 實作進行驗證。</span><span class="sxs-lookup"><span data-stu-id="fe639-105">Enforce that users can only authenticate against existing an RBAC implementation.</span></span>
    3. <span data-ttu-id="fe639-106">此藍圖套用至管理群組中的所有訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="fe639-106">Apply this blueprint to all subscriptions within the management group.</span></span>
2. <span data-ttu-id="fe639-107">建立 Azure 原則以套用或強制執行下列事項：</span><span class="sxs-lookup"><span data-stu-id="fe639-107">Create an Azure Policy to apply or enforce the following:</span></span>
    1. <span data-ttu-id="fe639-108">資源標記應該要有業務功能、資料分類、嚴重性、SLA、環境和應用程式的值。</span><span class="sxs-lookup"><span data-stu-id="fe639-108">Resource tagging should require values for Business Function, Data Classification, Criticality, SLA, Environment, and  Application.</span></span>
    2. <span data-ttu-id="fe639-109">應用程式標記的值應該符合資源群組的名稱。</span><span class="sxs-lookup"><span data-stu-id="fe639-109">The value of the Application tag should match the name of the resource group.</span></span>
    3. <span data-ttu-id="fe639-110">驗證每個資源群組和資源的角色指派。</span><span class="sxs-lookup"><span data-stu-id="fe639-110">Validate role assignments for each resource group and resource.</span></span>
3. <span data-ttu-id="fe639-111">將「治理 MVP」Azure 藍圖發佈並套用至每個管理群組。</span><span class="sxs-lookup"><span data-stu-id="fe639-111">Publish and apply the "Governance MVP" Azure Blueprint to each management group.</span></span>

<span data-ttu-id="fe639-112">這些模式可讓您探索和追蹤資源，並強制執行基本角色管理。</span><span class="sxs-lookup"><span data-stu-id="fe639-112">These patterns enable resources to be discovered and tracked, and enforce basic role management.</span></span>

### <a name="demilitarized-zone-dmz"></a><span data-ttu-id="fe639-113">非管制區域 (DMZ)</span><span class="sxs-lookup"><span data-stu-id="fe639-113">Demilitarized Zone (DMZ)</span></span>

<span data-ttu-id="fe639-114">特定訂用帳戶對於內部部署資源需要某種程度的存取權，是常有的情況。</span><span class="sxs-lookup"><span data-stu-id="fe639-114">It’s common for specific subscriptions to require some level of access to on-premises resources.</span></span> <span data-ttu-id="fe639-115">在進行移轉或開發時，如果有某些相依的資源仍位於內部部署資料中心，就會有此需要。</span><span class="sxs-lookup"><span data-stu-id="fe639-115">This may be the case for migration scenarios or development scenarios, when some dependent resources are still in the on-premises datacenter.</span></span> <span data-ttu-id="fe639-116">在此情況下，治理 MVP 可新增下列最佳做法：</span><span class="sxs-lookup"><span data-stu-id="fe639-116">In this case, the governance MVP adds the following best practices:</span></span>

1. <span data-ttu-id="fe639-117">建立雲端 DMZ。</span><span class="sxs-lookup"><span data-stu-id="fe639-117">Establish a Cloud DMZ.</span></span>
    1. <span data-ttu-id="fe639-118">[雲端 DMZ 參考架構](/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)會建立在 Azure 中建立 VPN 閘道的模式和部署模型。</span><span class="sxs-lookup"><span data-stu-id="fe639-118">The [Cloud DMZ reference architecture](/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) establishes a pattern and deployment model for creating a VPN Gateway in Azure.</span></span>
    2. <span data-ttu-id="fe639-119">驗證內部部署資料中心內的本機 Edge 裝置已有適當的 DMZ 連線和安全性需求。</span><span class="sxs-lookup"><span data-stu-id="fe639-119">Validate that proper DMZ connectivity and security requirements are in place for a local edge device in the on-premises datacenter.</span></span>
    3. <span data-ttu-id="fe639-120">驗證本機 Edge 裝置相符 Azure VPN 閘道需求。</span><span class="sxs-lookup"><span data-stu-id="fe639-120">Validate that the local edge device is compatible with Azure VPN Gateway requirements.</span></span>
    4. <span data-ttu-id="fe639-121">內部部署 VPN 的連線經過驗證後，請擷取該參考架構所建立的 Resource Manager 範本。</span><span class="sxs-lookup"><span data-stu-id="fe639-121">Once connection to the on-premise VPN has been verified, capture the Resource Manager template created by that reference architecture.</span></span>
2. <span data-ttu-id="fe639-122">建立名為 "DMZ" 的第二個藍圖。</span><span class="sxs-lookup"><span data-stu-id="fe639-122">Create a second blueprint named "DMZ".</span></span>
    1. <span data-ttu-id="fe639-123">將 VPN 閘道的 Resource Manager 範本新增至藍圖。</span><span class="sxs-lookup"><span data-stu-id="fe639-123">Add the Resource Manager template for the VPN Gateway to the blueprint.</span></span>
3. <span data-ttu-id="fe639-124">將 DMZ 藍圖套用至任何需要內部部署連線的訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="fe639-124">Apply the DMZ blueprint to any subscriptions requiring on-premises connectivity.</span></span> <span data-ttu-id="fe639-125">除了套用治理 MVP 藍圖外，也應套用此藍圖。</span><span class="sxs-lookup"><span data-stu-id="fe639-125">This blueprint should be applied in addition to the governance MVP blueprint.</span></span>

<span data-ttu-id="fe639-126">IT 安全性與傳統治理小組所引起的最大問題之一，是早期階段的雲端採用有可能使現有資產受損。</span><span class="sxs-lookup"><span data-stu-id="fe639-126">One of the biggest concerns raised by IT security and traditional governance teams, is the risk of early stage cloud adoption compromising existing assets.</span></span> <span data-ttu-id="fe639-127">上述方法可讓雲端採用小組建置和移轉混合式解決方案，而降低內部部署資產承受的風險。</span><span class="sxs-lookup"><span data-stu-id="fe639-127">The above approach allows cloud adoption teams to build and migrate hybrid solutions, with reduced risk to on-premises assets.</span></span> <span data-ttu-id="fe639-128">在後續的發展中，將會移除這個暫時性的解決方案。</span><span class="sxs-lookup"><span data-stu-id="fe639-128">In later evolution, this temporary solution would be removed.</span></span>

> [!NOTE]
> <span data-ttu-id="fe639-129">以上是快速建立基準治理 MVP 的起始點。</span><span class="sxs-lookup"><span data-stu-id="fe639-129">The above is a starting point to quickly create a baseline governance MVP.</span></span> <span data-ttu-id="fe639-130">這只是治理旅程的開端。</span><span class="sxs-lookup"><span data-stu-id="fe639-130">This is only the beginning of the governance journey.</span></span> <span data-ttu-id="fe639-131">隨著公司持續採用雲端，且承受更多來自下列領域的風險，治理將需要進一步演進修正：</span><span class="sxs-lookup"><span data-stu-id="fe639-131">Further evolution will be needed as the company continues to adopt the cloud and takes on more risk in the following areas:</span></span>
>
> - <span data-ttu-id="fe639-132">任務關鍵性工作負載</span><span class="sxs-lookup"><span data-stu-id="fe639-132">Mission-critical workloads</span></span>
> - <span data-ttu-id="fe639-133">受保護的資料</span><span class="sxs-lookup"><span data-stu-id="fe639-133">Protected data</span></span>
> - <span data-ttu-id="fe639-134">成本管理</span><span class="sxs-lookup"><span data-stu-id="fe639-134">Cost management</span></span>
> - <span data-ttu-id="fe639-135">多重雲端案例</span><span class="sxs-lookup"><span data-stu-id="fe639-135">Multi-cloud scenarios</span></span>
>
><span data-ttu-id="fe639-136">此外，此 MVP 的特定詳細資料皆以虛構公司的範例旅程為基礎，相關說明請見後續文章。</span><span class="sxs-lookup"><span data-stu-id="fe639-136">Moreover, the specific details of this MVP are based on the example journey of a fictitious company, described in the articles that follow.</span></span> <span data-ttu-id="fe639-137">強烈建議您在依照此最佳做法實作之前，應先閱讀這一系列的其他文章。</span><span class="sxs-lookup"><span data-stu-id="fe639-137">We highly recommend becoming familiar with the other articles in this series before implementing this best practice.</span></span>

<!-- TEMPLATE FILE - DO NOT ADD METADATA -->

## <a name="policy-statements"></a><span data-ttu-id="d222e-101">Policy statements</span><span class="sxs-lookup"><span data-stu-id="d222e-101">Policy statements</span></span>

<span data-ttu-id="d222e-102">下列原則聲明將建立要降低確知風險所需符合的要求。</span><span class="sxs-lookup"><span data-stu-id="d222e-102">The following policy statements establish the requirements needed to mitigate the defined risks.</span></span> <span data-ttu-id="d222e-103">這些原則將定義治理 MVP 的功能需求。</span><span class="sxs-lookup"><span data-stu-id="d222e-103">These policies define the functional requirements for the governance MVP.</span></span> <span data-ttu-id="d222e-104">每項原則都會反映在治理 MVP 的實作中。</span><span class="sxs-lookup"><span data-stu-id="d222e-104">Each will be represented in the implementation of the governance MVP.</span></span>

<span data-ttu-id="d222e-105">部署加速：</span><span class="sxs-lookup"><span data-stu-id="d222e-105">Deployment Acceleration:</span></span>

- <span data-ttu-id="d222e-106">所有資產都必須根據已定義的分組和標記策略進行分組和標記。</span><span class="sxs-lookup"><span data-stu-id="d222e-106">All assets must be grouped and tagged according to defined grouping and tagging strategies.</span></span>
- <span data-ttu-id="d222e-107">所有資產都必須使用經核准的部署模型。</span><span class="sxs-lookup"><span data-stu-id="d222e-107">All assets must use an approved deployment model.</span></span>
- <span data-ttu-id="d222e-108">在建立了雲端提供者的治理基礎之後，任何部署工具必須與治理小組所定義的工具相容。</span><span class="sxs-lookup"><span data-stu-id="d222e-108">Once a governance foundation has been established for a cloud provider, any deployment tooling must be compatible with the tools defined by the governance team.</span></span>

<span data-ttu-id="d222e-109">身分識別基準：</span><span class="sxs-lookup"><span data-stu-id="d222e-109">Identity Baseline:</span></span>

- <span data-ttu-id="d222e-110">部署至雲端的所有資產，均應使用經目前的治理原則核准的身分識別與角色來控管。</span><span class="sxs-lookup"><span data-stu-id="d222e-110">All assets deployed to the cloud should be controlled using identities and roles approved by current governance policies.</span></span>
- <span data-ttu-id="d222e-111">內部部署 Active Directory 基礎結構中已提高權限的所有群組，均應對應至經核准的 RBAC 角色。</span><span class="sxs-lookup"><span data-stu-id="d222e-111">All groups in the on-premises Active Directory infrastructure that have elevated privileges should be mapped to an approved RBAC role.</span></span>

<span data-ttu-id="d222e-112">安全性基準：</span><span class="sxs-lookup"><span data-stu-id="d222e-112">Security Baseline:</span></span>

- <span data-ttu-id="d222e-113">部署至雲端的任何資產，都必須有已核准的資料分類。</span><span class="sxs-lookup"><span data-stu-id="d222e-113">Any asset deployed to the cloud must have an approved data classification.</span></span>
- <span data-ttu-id="d222e-114">在可核准並實作足夠的安全性和治理需求之前，任何資產一經識別有受保護的資料層級，即不可部署至雲端。</span><span class="sxs-lookup"><span data-stu-id="d222e-114">No assets identified with a protected level of data may be deployed to the cloud, until sufficient requirements for security and governance can be approved and implemented.</span></span>
- <span data-ttu-id="d222e-115">在可驗證並控管最低網路安全需求之前，雲端環境將被視為非管制區域，且應符合與其他資料中心或內部網路類似的連線需求。</span><span class="sxs-lookup"><span data-stu-id="d222e-115">Until minimum network security requirements can be validated and governed, cloud environments are seen as a demilitarized zone and should meet similar connection requirements to other data centers or internal networks.</span></span>

<span data-ttu-id="d222e-116">成本管理：</span><span class="sxs-lookup"><span data-stu-id="d222e-116">Cost Management:</span></span>

- <span data-ttu-id="d222e-117">為了進行追蹤，所有資產皆必須指派給其中一項核心業務功能內的應用程式擁有者。</span><span class="sxs-lookup"><span data-stu-id="d222e-117">For tracking purposes, all assets must be assigned to an application owner within one of the core business functions.</span></span>
- <span data-ttu-id="d222e-118">出現成本考量時，將與財務小組一起建立額外的治理需求。</span><span class="sxs-lookup"><span data-stu-id="d222e-118">When cost concerns arise, additional governance requirements will be established with the Finance team.</span></span>

<span data-ttu-id="d222e-119">資源一致性：</span><span class="sxs-lookup"><span data-stu-id="d222e-119">Resource Consistency:</span></span>

- <span data-ttu-id="d222e-120">由於在此階段並未部署沒有任務關鍵性工作負載，因此無須控管 SLA、效能或 BCDR 需求。</span><span class="sxs-lookup"><span data-stu-id="d222e-120">Because no mission-critical workloads are deployed at this stage, there are no SLA, performance, or BCDR requirements to be governed.</span></span>
- <span data-ttu-id="d222e-121">在部署任務關鍵性工作負載時，將會隨 IT 營運一起建立額外的治理需求。</span><span class="sxs-lookup"><span data-stu-id="d222e-121">When mission-critical workloads are deployed, additional governance requirements will be established with IT operations.</span></span>

## <a name="processes"></a><span data-ttu-id="d222e-122">處理序</span><span class="sxs-lookup"><span data-stu-id="d222e-122">Processes</span></span>

<span data-ttu-id="d222e-123">目前對於上述治理原則的監視和施行，並未分配任何預算。</span><span class="sxs-lookup"><span data-stu-id="d222e-123">No budget has been allocated for ongoing monitoring and enforcement of these governance policies.</span></span> <span data-ttu-id="d222e-124">因此，雲端治理小組會以一些暫行措施來監視原則聲明是否受遵行。</span><span class="sxs-lookup"><span data-stu-id="d222e-124">Because of that, the Cloud Governance team has some ad hoc ways to monitor adherence to policy statements.</span></span>

- <span data-ttu-id="d222e-125">**教育訓練**：雲端治理小組會投注時間，針對支援這些原則的治理旅程為雲端採用小組提供相關教育訓練。</span><span class="sxs-lookup"><span data-stu-id="d222e-125">**Education**: The Cloud Governance team is investing time to educate the cloud adoption teams on the governance journeys that support these policies.</span></span>
- <span data-ttu-id="d222e-126">**部署**檢閱：在部署任何資產之前，雲端治理小組會檢閱雲端採用團隊的治理旅程。</span><span class="sxs-lookup"><span data-stu-id="d222e-126">**Deployment** reviews: Before deploying any asset, the Cloud Governance team will review the governance journey with the cloud adoption teams.</span></span>

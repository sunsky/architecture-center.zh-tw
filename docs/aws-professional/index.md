---
title: 適用於 AWS 專業人員的 Azure
titleSuffix: Azure Architecture Center
description: 了解 Microsoft Azure 帳戶、平台和服務的基本概念。 並了解 AWS 和 Azure 平台之間的金鑰相似度和差異。 利用您在 Azure 中的 AWS 體驗。
keywords: AWS 專家, Azure 比較, AWS 比較, azure 與 aws 之間的差異, azure 與 aws
author: lbrader
ms.date: 09/19/2018
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 6e44953cccf39cc40be775f4043bcf8b1b890dae
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59641156"
---
# <a name="azure-for-aws-professionals"></a>適用於 AWS 專業人員的 Azure

本文可協助 Amazon Web Services (AWS) 專家了解 Microsoft Azure 帳戶、平台和服務的基本概念。 其中也涵蓋 AWS 和 Azure 平台之間的主要相似度和差異。

您將了解：

- 帳戶和資源在 Azure 中組織的方式。
- Azure 中可用解決方案的結構方式。
- 主要 Azure 服務與 AWS 服務有何差異。

Azure 和 AWS 會隨時間獨立建置其功能，讓每個都有重要實作和設計的差異。

## <a name="overview"></a>概觀

如同 AWS，Microsoft Azure 架構是以一組核心計算、儲存體、資料庫和網路服務所建置。 在許多情況下，這兩個平台會在其提供的產品和服務之間提供基本等價。 AWS 和 Azure 都可讓您以 Windows 或 Linux 主機作為基礎，建置高可用性的解決方案。 因此，如果您習慣使用 Linux 及 OSS 技術的開發，這兩個平台皆可執行作業。

雖然這兩個平台的功能類似，提供這些功能的資源通常會以不同的方式組織。 建置解決方案所需服務之間的確切一對一關聯性並不一定顯而易見。 也有一些情況下，可能會在一個平台上提供對特定服務，但其他平台則不提供。 請參閱[可比較的 Azure 與 AWS 服務圖表](./services.md)。

## <a name="accounts-and-subscriptions"></a>帳戶及訂用帳戶

可以使用數個定價選項來購買 Azure 服務，取決於貴組織的大小和需求。 請參閱[定價概觀](https://azure.microsoft.com/pricing/)頁面以取得詳細資料。

[Azure 訂用帳戶](/azure/virtual-machines/linux/infrastructure-example)是具有指派擁有者的資源群組，負責帳單和權限管理。 不同於 AWS，其中在 AWS 帳戶下建立的任何資源都會繫結至該帳戶，訂用帳戶會與其擁有者帳戶分開存在，且可視需要重新指派給新的擁有者。

<!-- markdownlint-disable MD033 -->

![比較 AWS 帳戶與 Azure 訂用帳戶的結構和擁有權](./images/azure-aws-account-compare.png "比較 AWS 帳戶與 Azure 訂用帳戶的結構和擁有權")
<br/>*比較 AWS 帳戶與 Azure 訂用帳戶的結構和 擁有權*
<br/><br/>

<!-- markdownlint-enable MD033 -->

有三種類型的系統管理員帳戶會指派給訂用帳戶：

- **帳戶管理員**。 訂用帳戶擁有者和針對訂用帳戶中使用的資源計費的帳戶。 只能藉由轉移訂用帳戶的擁有權來變更帳戶管理員。

- **服務管理員**。 此帳戶有權限可建立及管理訂用帳戶中的資源，但不負責計費。 根據預設，帳戶管理員和服務管理員會指派給相同的帳戶。 帳戶管理員可以將個別使用者指派給服務管理員帳戶，來管理訂用帳戶的技術和運作層面。 每個訂用帳戶只有一個服務管理員。

- **共同管理員**。 可指派多個共同管理員帳戶給訂用帳戶。 共同管理員無法變更服務管理員，但擁有訂用帳戶資源和使用者的完整控制權。

以下訂用帳戶層級使用者角色和個別權限也可以指派給特定的資源，類似於在 AWS 中將權限授與 IAM 使用者和群組的方式。 在 Azure 中的所有使用者帳戶都會與 Microsoft 帳戶或組織帳戶 (透過 Azure Active Directory 管理的帳戶) 相關聯。

如同 AWS 帳戶，訂用帳戶具有預設的服務配額和限制。 如需這些限制的完整清單，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](/azure/azure-subscription-service-limits)。
可以藉由[在管理入口網站中提出支援要求](https://blogs.msdn.microsoft.com/girishp/2015/09/20/increasing-core-quota-limits-in-azure/)，將這些限制增加到最大值。

### <a name="see-also"></a>另請參閱

- [如何新增或變更 Azure 管理員角色](/azure/billing/billing-add-change-azure-subscription-administrator)

- [如何下載您的 Azure 帳單發票和每日使用量資料](/azure/billing/billing-download-azure-invoice-daily-usage-date)

## <a name="resource-management"></a>資源管理

Azure 中的「資源」一詞與 AWS 中的使用方式相同，這表示您可以在平台內建立或設定的任何計算執行個體、儲存物件、網路裝置或其他實體。

使用下列其中一種模型來部署和管理 Azure 資源：[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)，或較舊的 Azure [傳統部署模型](/azure/azure-resource-manager/resource-manager-deployment-model)。 使用 Resource Manager 模型來建立任何新的資源。

### <a name="resource-groups"></a>資源群組

Azure 和 AWS 都有稱為「資源群組」的實體，能組織諸如 VM、儲存體和虛擬網路裝置等資源。 不過，[Azure 資源群組](/azure/virtual-machines/windows/infrastructure-example)無法直接與 AWS 資源群組比較。

儘管 AWS 允許將資源標記成多個資源群組，但 Azure 資源一律與一個資源群組相關聯。 一個資源群組中建立的資源可以移至另一個群組，但一次只能在一個資源群組中。 資源群組是 Azure Resource Manager 所使用的基本群組。

也可以使用[標記](/azure/azure-resource-manager/resource-group-using-tags)來組織資源。 標記是機碼值組，可讓您跨訂用帳戶群組資源，不論資源群組成員資格。

### <a name="management-interfaces"></a>管理介面

Azure 提供數種方式來管理您的資源：

- [Web 介面](/azure/azure-resource-manager/resource-group-portal)。
    如同 AWS 儀表板，Azure 入口網站會提供 Azure 資源的完整 web 型管理介面。

- [REST API](/rest/api/)。
    Azure Resource Manager REST API 會提供以程式設計方式存取 Azure 入口網站中大部分可用的功能。

- [命令列](/azure/azure-resource-manager/cli-azure-resource-manager)。
    Azure CLI 2.0 工具提供的命令列介面能夠建立及管理 Azure 資源。 Azure CLI 適用於 [Windows、Linux 和 Mac OS](https://aka.ms/azurecli2)。

- [PowerShell](/azure/azure-resource-manager/powershell-azure-resource-manager)。
    適用於 PowerShell 的 Azure 模組可讓您使用指令碼執行自動化的管理工作。 PowerShell 適用於 [Windows、Linux 和 Mac OS](https://github.com/PowerShell/PowerShell)。

- [範本](/azure/azure-resource-manager/resource-group-authoring-templates)。
    Azure Resource Manager 範本會提供類似的以 JSON 範本為基礎之資源管理功能給 AWS CloudFormation 服務。

在這些介面中，資源群組是 Azure 資源建立、部署或修改方式的核心。 這類似於「堆疊」在 CloudFormation 部署期間群組 AWS 資源所扮演的角色。

這些介面的語法和結構與其 AWS 對等項目不同，但可提供相當程度的功能。 此外，許多 AWS 上使用的第三方管理工具 (例如 [Hashicorp 的 Terraform](https://www.terraform.io/docs/providers/azurerm/) 和 [Netflix Spinnaker](https://www.spinnaker.io/)) 也會在 Azure 上提供。

<!-- markdownlint-disable MD024 -->

### <a name="see-also"></a>另請參閱

- [Azure 資源群組指導方針](/azure/azure-resource-manager/resource-group-overview#resource-groups)

## <a name="regions-and-zones-high-availability"></a>地區和區域 (高可用性)

失敗的影響範圍各不相同。 有些硬體失敗 (例如失敗的磁碟) 可能會影響單一主機電腦。 失敗的網路交換器則可能影響整個伺服器機架。 較不常見的失敗是整個資料中心中斷，例如資料中心斷電。 至於整個區域都變得無法使用的情況則很罕見。

其中一個讓應用程式具有復原能力的主要方法是透過備援。 但您必須在設計應用程式時規劃此備援措施。 此外，您需要的備援層級取決於您的業務需求，並非每個應用程式都需要跨區域備援，以防範區域性中斷。 一般情況下，您需要在更好的備援性和可靠性與更高的成本和複雜性之間做出取捨。

在 AWS 中，地區會分割成兩個或多個可用性區域。 可用性區域會與地理區域中實際隔離的資料中心相對應。 Azure 提供許多功能，可讓應用程式在每個失敗階段都進行備援，包括**可用性設定組**、**可用性區域**和**配對區域**。

![備援性](../resiliency/images/redundancy.svg)

下表摘要說明每個選項。

| &nbsp; | 可用性設定組 | 可用性區域 | 配對的區域 |
|--------|------------------|-------------------|---------------|
| 失敗原因 | 機架 | 資料中心 | 區域 |
| 要求路由 | 負載平衡器 | 跨區域負載平衡器 | 流量管理員 |
| 網路延遲 | 非常低 | 低 | 中到高 |
| 虛擬網路  | VNet | VNet | 跨區域 VNet 對等互連 |

### <a name="availability-sets"></a>可用性設定組

若要防範局部硬體失敗 (例如，磁碟或網路交換器失敗)，請在可用性設定組中部署兩個以上的 VM。 可用性設定組包含兩個以上的「容錯網域」，這些網域會共用電力來源和網路交換器。 可用性設定組中的 VM 會分散於這些容錯網域中，因此如果某個硬體失敗影響其中一個容錯網域，網路流量仍可路由傳送至其他容錯網域中的 VM。 如需可用性設定組的詳細資訊，請參閱[管理 Azure 中 Windows 虛擬機器的可用性](/azure/virtual-machines/windows/manage-availability)。

將 VM 執行個體新增至可用性設定組時，也會指派[更新網域](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-manage-availability/)給它們。 更新網域是 VM 群組，會同時針對預定進行的維修作業事件而設定。 將 VM 分散到多個更新網域，以確保計劃的更新和修補事件在任何指定時間只會影響這些 VM 的子集。

應該依您應用程式中執行個體的角色來組織可用性設定組，以確保每個角色的一個執行個體皆可運作。 例如，在三層 Web 應用程式中，為前端、應用程式和資料層分別建立可用性設定組。

![每個應用程式角色的 Azure 可用性設定組](./images/three-tier-example.png "每個應用程式角色的可用性設定組")

### <a name="availability-zones"></a>可用性區域

[可用性區域](/azure/availability-zones/az-overview)實際上是 Azure 地區內的個別區域。 每個可用性區域各有不同的電力來源、網路和冷卻系統。 跨可用性區域部署 VM 可協助應用程式防範全資料中心的失敗。

### <a name="paired-regions"></a>配對的區域

若要協助應用程式防範區域性中斷，您可以將應用程式部署至多個區域，並使用 [Azure 流量管理員][traffic-manager]將網際網路流量分散到不同區域。 每個 Azure 區域都會與另一個區域配對。 這些區域集合在一起就構成了[區域性配對][paired-regions]。 區域性配對會位於相同的地理位置內 (巴西南部除外)，以符合資料常駐地之稅務和執法管轄區的要求。

不同於實體上分隔資料中心但可能會相對在附近地理區域的可用性區域，通常會以最少 300 英哩來分隔配對地區。 這是為了確保較大規模的災難只會影響配對中的其中一個地區。 相鄰的配對可以設定為同步處理資料庫和儲存體服務資料，並加以設定以便平台更新一次只會發行至配對中的一個地區。

Azure [異地備援儲存體](/azure/storage/common/storage-redundancy-grs)會自動備份至適當的配對區域。 如需其他所有資源，請使用配對區域建立完整備援的解決方案，即表示在兩個區域中建立您解決方案的完整副本。

### <a name="see-also"></a>另請參閱

- [Azure 中虛擬機器的區域和可用性](/azure/virtual-machines/linux/regions-and-availability)

- [Azure 應用程式的高可用性](../resiliency/high-availability-azure-applications.md)

- [Azure 應用程式的失敗及災害復原](../reliability/disaster-recovery.md)

- [Azure 中 Linux 虛擬機器預定進行的維修](/azure/virtual-machines/linux/maintenance-and-updates)

## <a name="services"></a>服務

如需服務在平台之間對應方式的清單，請參閱 [AWS 與 Azure 服務比較](./services.md)。

並非所有區域中都可使用所有的 Azure 產品和服務。 如需詳細資料，請參閱[依區域的產品](https://azure.microsoft.com/global-infrastructure/services/)頁面。 您可以在[服務等級協定](https://azure.microsoft.com/support/legal/sla/)頁面上找到每個 Azure 產品或服務的執行時間保證和停機時間信用原則。

下列章節會提供 AWS 和 Azure 平台之間的常用功能和服務之差異的簡短說明。

### <a name="compute-services"></a>計算服務

#### <a name="ec2-instances-and-azure-virtual-machines"></a>EC2 執行個體和 Azure 虛擬機器

雖然 AWS 執行個體類型和 Azure 虛擬機器大小是以類似的方式分解，但 RAM、CPU 和儲存體功能中會有所差異。

- [Amazon EC2 執行個體類型](https://aws.amazon.com/ec2/instance-types/)

- [Azure 中的虛擬機器大小 (Windows)](/azure/virtual-machines/windows/sizes)

- [Azure 中的虛擬機器大小 (Linux)](/azure/virtual-machines/linux/sizes)

與 AWS 的每個第二個計費相似，Azure 隨選 VM 會依秒計費。

#### <a name="ebs-and-azure-storage-for-vm-disks"></a>適用於 VM 磁碟的 EBS 和 Azure 儲存體

Azure VM 的長期資料儲存區是由位在 blob 儲存體中的[資料磁碟](/azure/virtual-machines/linux/about-disks-and-vhds)所提供。 這類似於 EC2 執行個體在 Elastic Block Store (EBS) 上儲存磁碟區的方式。 [Azure 暫存儲存體](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)也會提供 VM 與 EC2 執行個體儲存體 (也稱為暫時儲存體) 相同的低延遲暫存讀寫儲存體。

會使用 [Azure 高階儲存體](/azure/virtual-machines/windows/premium-storage)支援較高的效能磁碟 IO。 這是類似於 AWS 所提供之佈建的 IOPS 儲存體選項。

#### <a name="lambda-azure-functions-azure-web-jobs-and-azure-logic-apps"></a>Lambda、Azure Functions、Azure Web 作業以及 Azure Logic Apps

[Azure Functions](https://azure.microsoft.com/services/functions/) 在提供無伺服器、隨選程式碼方面是 AWS Lambda 的主要對等項目。 不過，Lambda 功能也會與其他 Azure 服務重疊：

- [WebJobs](/azure/app-service/web-sites-create-web-jobs) 可讓您建立排程或連續執行背景工作。

- [Logic Apps](https://azure.microsoft.com/services/logic-apps/) 提供通訊、整合及商務規則管理服務。

#### <a name="autoscaling-azure-vm-scaling-and-azure-app-service-autoscale"></a>自動調整、Azure VM 縮放比例和 Azure App Service 自動調整

Azure 中的自動調整是由兩個服務處理：

- [VM 擴展集](/azure/virtual-machine-scale-sets/overview) 可讓您部署及管理一組完全相同的 VM。 執行個體數目可以效能需求作為基礎來自動調整。

- [App Service 自動調整](/azure/app-service/web-sites-scale) 提供自動調整 Azure App Service 解決方案的功能。

#### <a name="container-service"></a>容器服務

[Azure Kubernetes Service](/azure/aks/intro-kubernetes) 支援透過 Kubernetes 管理的 Docker 容器。

#### <a name="other-compute-services"></a>其他計算服務

Azure 會提供數個計算服務，在 AWS 中沒有直接的對等項目：

- [Azure Batch](/azure/batch/batch-technical-overview) 可讓您在虛擬機器的擴充集合之間管理大量的計算工作。

- [Service Fabric](/azure/service-fabric/service-fabric-overview) 開發及主控可擴充的[微服務](/azure/service-fabric/service-fabric-overview-microservices)解決方案平台。

#### <a name="see-also"></a>另請參閱

- [使用入口網站在 Azure 上建立 Linux VM](/azure/virtual-machines/linux/quick-create-portal)

- [Azure 參考架構：在 Azure 上執行 Linux VM](/azure/architecture/reference-architectures/n-tier/linux-vm)

- [在 Azure App Service 中開始使用 Node.js Web 應用程式](/azure/app-service/app-service-web-get-started-nodejs)

- [Azure 參考架構：基本 Web 應用程式](/azure/architecture/reference-architectures/app-service-web-app/basic-web-app)

- [建立您的第一個Azure Functions](/azure/azure-functions/functions-create-first-azure-function)

### <a name="storage"></a>儲存體

#### <a name="s3ebsefs-and-azure-storage"></a>S3/EBS/EFS 與 Azure 儲存體

在 AWS 平台中，雲端儲存體主要分為三種服務：

- **Simple Storage Services (S3)**。 基本物件儲存體可透過網際網路可存取 API 來使用資料。

- **Elastic Block Store (EBS)**。 區塊層級儲存體可供單一 VM 存取。

- **Elastic File System (EFS)**。 檔案儲存體適用於最多上千個 EC2 執行個體作為共用存放裝置使用。

在 Azure 儲存體中，訂用帳戶繫結的[儲存體帳戶](/azure/storage/common/storage-quickstart-create-account)可讓您建立及管理下列儲存體服務：

- [Blob 儲存體](/azure/storage/common/storage-quickstart-create-account)儲存任何類型的文字或二進位資料，例如文件、媒體檔案或應用程式安裝程式。 您可以設定私人存取的 Blob 儲存體或公開共用內容到網際網路。 Blob 儲存體與 AWS S3 和 EBS 有相同的用途。
- [表格儲存體](/azure/cosmos-db/table-storage-how-to-use-nodejs)可儲存結構化資料集。 表格儲存體屬於 NoSQL 索引鍵屬性資料儲存，可允許快速開發和迅速存取大量資料。 類似於 AWS 的 SimpleDB 和 DynamoDB 服務。

- [佇列儲存體](/azure/storage/queues/storage-nodejs-how-to-use-queues)可為工作流程處理及雲端服務元件間的通訊，提供訊息服務。

- [檔案儲存體](/azure/storage/files/storage-java-how-to-use-file-storage)使用標準伺服器訊息區 (SMB) 通訊協定為繼承應用程式提供共用儲存體。 檔案儲存體會以類似的方式在 AWS 平台用於 EFS。

#### <a name="glacier-and-azure-storage"></a>Glacier 與 Azure 儲存體

[Azure 封存 Blob 儲存體](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier)相當於 AWS Glacier 儲存體服務。 它適用於不太會存取的資料，且該資料已儲存至少 180 天且可以容忍數小時的擷取延遲。

針對不常存取、卻又必須可供立即存取的資料，[Azure 非經常性 Blob 儲存層](/azure/storage/blobs/storage-blob-storage-tiers#cool-access-tier)可提供比標準 Blob 儲存體還要便宜的儲存體。 此儲存層相當於 AWS S3 - 不常存取儲存體服務。

#### <a name="see-also"></a>另請參閱

- [Microsoft Azure 儲存體效能與延展性檢查清單](/azure/storage/common/storage-performance-checklist)

- [Azure 儲存體安全性指南](/azure/storage/common/storage-security-guide)

- [使用內容傳遞網路 (CDN) 的最佳作法](/azure/architecture/best-practices/cdn)

### <a name="networking"></a>網路功能

#### <a name="elastic-load-balancing-azure-load-balancer-and-azure-application-gateway"></a>彈性負載平衡、Azure Load Balancer 以及 Azure 應用程式閘道

這兩項彈性負載平衡服務的 Azure 對等項目為：

- [負載平衡器](https://azure.microsoft.com/documentation/articles/load-balancer-overview/) - 提供與 AWS 傳統 Load Balancer 相同的功能，可讓您在網路層級散發多個 VM 的流量。 它也提供容錯移轉功能。

- [應用程式閘道](https://azure.microsoft.com/documentation/articles/application-gateway-introduction/) - 提供可比較 AWS 應用程式 Load Balancer 的應用程式層級規則型路由。

#### <a name="route-53-azure-dns-and-azure-traffic-manager"></a>Route 53、Azure DNS 和 Azure 流量管理員

在 AWS 中，Route 53 會提供 DNS 名稱管理和 DNS 層級流量路由與容錯移轉服務。 在 Azure 中，這會透過兩個服務進行處理：

- [Azure DNS](https://azure.microsoft.com/documentation/services/dns/) 提供網域和 DNS 管理。

- [流量管理員][traffic-manager]提供 DNS 層級流量路由、負載平衡和容錯移轉功能。

#### <a name="direct-connect-and-azure-expressroute"></a>Direct Connect 與 Azure ExpressRoute

Azure 會透過其 [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) 服務提供類似的站台對站台專用連線。 ExpressRoute 可讓您使用專用私人網路連線，將本機網路直接連線到 Azure 資源。 Azure 還以較低的成本提供更多傳統[站對站 VPN 連線](https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-site-to-site-resource-manager-portal/)。

#### <a name="see-also"></a>另請參閱

- [使用 Azure 入口網站建立虛擬網路](https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/)

- [規劃和設計 Azure 虛擬網路](https://azure.microsoft.com/documentation/articles/virtual-network-vnet-plan-design-arm/)

- [Azure 網路安全性最佳做法](https://azure.microsoft.com/documentation/articles/azure-security-network-security-best-practices/)

### <a name="database-services"></a>資料庫服務

#### <a name="rds-and-azure-relational-database-services"></a>RDS 和 Azure 關聯式資料庫服務

Azure 會提供與 AWS 關聯式資料庫服務 (RDS) 對等的幾種不同關聯式資料庫服務。

- [SQL Database](/azure/sql-database/sql-database-technical-overview)
- [適用於 MySQL 的 Azure 資料庫](/azure/mysql/overview)
- [適用於 PostgreSQL 的 Azure 資料庫](/azure/postgresql/overview)

可以使用 Azure VM 執行個體部署諸如 [SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)、[Oracle](https://azure.microsoft.com/campaigns/oracle/) 和 [MySQL](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-classic-mysql-2008r2/) 等其他資料庫引擎。

AWS RDS 的成本取決於您執行個體所使用的硬體資源，例如 CPU、RAM、儲存體和網路頻寬。 在 Azure 資料庫服務中，成本取決於您的資料庫大小、並行連線及輸送量層級。

#### <a name="see-also"></a>另請參閱

- [Azure SQL Database 教學課程](https://azure.microsoft.com/documentation/articles/sql-database-explore-tutorials/)

- [使用 Azure 入口網站為 Azure SQL Database 設定異地複寫](https://azure.microsoft.com/documentation/articles/sql-database-geo-replication-portal/)

- [Cosmos DB 簡介：NoSQL JSON 資料庫](/azure/cosmos-db/sql-api-introduction)

- [如何從 Node.js 使用 Azure 資料表儲存體](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-table-storage/)

### <a name="security-and-identity"></a>安全性與身分識別

#### <a name="directory-service-and-azure-active-directory"></a>目錄服務和 Azure Active Directory

Azure 會將目錄服務分割成下列供應項目：

- [Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-whatis/) - 以雲端為基礎的目錄和身分識別管理服務。

- [Azure Active Directory B2B](https://azure.microsoft.com/documentation/articles/active-directory-b2b-collaboration-overview/) - 允許以合作夥伴管理的身分識別來存取您的公司應用程式。

- [Azure Active Directory B2C](https://azure.microsoft.com/documentation/articles/active-directory-b2c-overview/) - 支援取用者面相應用程式的單一登入和使用者管理的服務供應項目。

- [Azure Active Directory Domain Services](https://azure.microsoft.com/documentation/articles/active-directory-ds-overview/) - 主控網域控制站服務，可允許 Active Directory 相容網域加入和使用者管理功能。

#### <a name="web-application-firewall"></a>Web 應用程式防火牆

除了[應用程式閘道 Web 應用程式防火牆](/azure/application-gateway/waf-overview)之外，您也可以使用第三方廠商提供的 Web 應用程式防火牆，像是 [Barracuda Networks](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf/)。

#### <a name="see-also"></a>另請參閱

- [開始使用 Microsoft Azure 安全性](/azure/security)

- [Azure 身分識別管理和存取控制安全性最佳作法](/azure/security/azure-security-identity-management-best-practices)

### <a name="application-and-messaging-services"></a>應用程式及傳訊服務

#### <a name="simple-email-service"></a>Simple Email Service

AWS 提供 Simple Email Service (SES) 來傳送通知、交易式或行銷電子郵件。 在 Azure 中，第三方解決方案 (例如 [SendGrid](https://sendgrid.com/partners/azure/)) 會提供電子郵件服務。

#### <a name="simple-queueing-service"></a>Simple Queueing Service

AWS Simple Queueing Service (SQS) 會提供傳訊系統，可連線 AWS 平台內的應用程式、服務和裝置。 Azure 有兩項服務，可提供類似的功能：

- [佇列儲存體](/azure/storage/queues/storage-nodejs-how-to-use-queues)：雲端傳訊服務可在 Azure 平台內的應用程式元件之間進行通訊。

- [服務匯流排](https://azure.microsoft.com/services/service-bus/)：更強固的傳訊系統，可連線應用程式、服務和裝置。 服務匯流排會使用相關的[服務匯流排轉送](/azure/service-bus-relay/relay-what-is-it)，也可以連線到遠端主控應用程式和服務。

#### <a name="device-farm"></a>裝置伺服器陣列

AWS 裝置伺服陣列會提供跨裝置測試服務。 在 Azure 中，[Xamarin Test Cloud](https://www.xamarin.com/test-cloud) 會提供行動裝置的類似跨裝置前端測試。

除了前端測試之外，[Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) 還提供 Linux 和 Windows 環境的後端測試資源。

#### <a name="see-also"></a>另請參閱

- [如何使用 Node.js 的佇列儲存體](/azure/storage/queues/storage-nodejs-how-to-use-queues)

- [如何使用服務匯流排佇列](/azure/service-bus-messaging/service-bus-nodejs-how-to-use-queues)

### <a name="analytics-and-big-data"></a>分析與巨量資料

[Cortana Intelligence Suite](https://azure.microsoft.com/suites/cortana-intelligence-suite/) 是 Azure 的產品和服務套件，專為擷取、組織、分析和視覺化大量資料而設計。 Cortana 套件包含下列服務：

- [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) - 受控 Apache 分佈，其中包括 Hadoop、Spark、Storm 或 HBase。

- [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) - 提供資料協調流程和 Data Pipeline 功能。

- [SQL 資料倉儲](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) - 大規模關聯式資料存放區。

- [Data Lake Store](https://azure.microsoft.com/documentation/services/data-lake-store/) - 針對巨量資料分析工作負載最佳化的大規模儲存體。

- [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) - 用來建置及套用關於資料的預測性分析。

- [串流分析](https://azure.microsoft.com/documentation/services/stream-analytics/) - 即時資料分析。

- [Data Lake Analytics](https://azure.microsoft.com/documentation/articles/data-lake-analytics-overview/) - 針對使用 Data Lake Store 進行最佳化的大規模分析服務

- [PowerBI](https://powerbi.microsoft.com/) - 用來提供資料視覺效果。

#### <a name="see-also"></a>另請參閱

- [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com/)

- [了解 Microsoft 巨量資料解決方案](https://msdn.microsoft.com/library/dn749804.aspx)

- [Azure Data Lake 與 Azure HDInsight 部落格](https://blogs.msdn.microsoft.com/azuredatalake/)

### <a name="internet-of-things"></a>物聯網

#### <a name="see-also"></a>另請參閱

- [開始使用 Azure IoT 中樞](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/)

- [IoT 中樞和事件中樞的比較](https://azure.microsoft.com/documentation/articles/iot-hub-compare-event-hubs/)

### <a name="mobile-services"></a>行動服務

#### <a name="notifications"></a>通知

通知中樞不支援傳送簡訊或電子郵件訊息，因此需要這些傳遞類型的第三方服務。

#### <a name="see-also"></a>另請參閱

- [建立 Android 應用程式](https://azure.microsoft.com/documentation/articles/app-service-mobile-android-get-started/)

- [Azure Mobile Apps 中的驗證與授權](https://azure.microsoft.com/documentation/articles/app-service-mobile-auth/)

- [使用 Azure 通知中樞傳送推播通知](https://azure.microsoft.com/documentation/articles/notification-hubs-android-push-notification-google-fcm-get-started/)

### <a name="management-and-monitoring"></a>管理與監視

#### <a name="see-also"></a>另請參閱

- [監視和診斷指引](https://azure.microsoft.com/documentation/articles/best-practices-monitoring/)

- [建立 Azure Resource Manager 範本的最佳做法](https://azure.microsoft.com/documentation/articles/resource-manager-template-best-practices/)

- [Azure Resource Manager 快速入門範本](https://azure.microsoft.com/documentation/templates/)

## <a name="next-steps"></a>後續步驟

- [開始使用 Azure](https://azure.microsoft.com/get-started/)

- [Azure 解決方案架構](https://azure.microsoft.com/solutions/architecture/)

- [Azure 參考架構](https://azure.microsoft.com/documentation/articles/guidance-architecture/)

<!-- links -->

[paired-regions]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[traffic-manager]: /azure/traffic-manager/

<!-- markdownlint-enable MD024 -->

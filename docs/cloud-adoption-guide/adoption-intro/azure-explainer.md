---
title: 說明：Azure 如何運作？
description: 說明 Azure 的內部運作方式
author: petertay
ms.openlocfilehash: b4830fec69ac6d256d934d91ea2c295219925a9a
ms.sourcegitcommit: ea7108f71dab09175ff69322874d1bcba800a37a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="explainer-how-does-azure-work"></a>說明：Azure 如何運作？

Azure 是 Microsoft 的公用雲端平台。 Azure 提供多種不同的服務，包括平台即服務 (PaaS)、基礎結構即服務 (IaaS)、資料庫即服務 (DBaaS) 等等。 但 Azure 到底是什麼？它如何運作？

Azure 就像其他雲端平台一樣，需仰賴名為**虛擬化**的技術。 大部分的電腦硬體都可用軟體來模擬，因為大部分的電腦硬體都只是一組永久或半永久編碼在矽晶片材料中的指令。 使用將軟體指令對應至硬體指令的模擬層，虛擬化的硬體即可用軟體執行，如同在實際的硬體中執行一般。

基本上，雲端是一或多個資料中心內部代替客戶執行虛擬化硬體的一組實體伺服器。 那麼，雲端要如何同時為數百萬個客戶建立、啟動、停止及刪除數百萬個虛擬化硬體執行個體呢？

要了解這一點，就必須看看資料中心裡的硬體架構。  每個資料中心都有伺服器集合設置在伺服器機架中。 每個伺服器機架都包含許多伺服器**刀鋒**，以及提供網路連線的網路交換器和提供電力的配電裝置 (PDU)。 機架有時會一起分組到較大的單位中，名為**叢集**。 

在每個機架或叢集內，大部分的伺服器都會被指定用來代替使用者執行這些虛擬化硬體執行個體。 不過，有一些伺服器則會執行名為網狀架構控制器的雲端管理軟體。 **網狀架構控制器**是一種分散式應用程式，負責執行多項工作。 它會配置服務、監視伺服器及其執行之服務的健全狀況，並在伺服器故障時加以修復。

每個網狀架構控制器執行個體都會連線至執行雲端協調流程軟體的另一組伺服器，一般稱之為**前端**。 前端會主控 Web 服務、RESTful API，以及雲端執行的所有功能所使用的內部 Azure 資料庫。 

例如，前端會主控負責處理客戶配置 Azure 資源 (例如[虛擬網路][vnet]、[虛擬機器][vms]和 [Cosmos DB][cosmosdb] 之類的服務) 之要求的服務。 首先，前端會驗證使用者，並確認使用者是否有權配置要求的資源。 如果是，前端會參照資料庫以找出具有足夠容量的伺服器機架，然後指示機架上的網狀架構控制器配置資源。

因此，簡言之，Azure 就是大量伺服器和網路硬體的集合，外加一組複雜的分散式應用程式，負責協調這些伺服器上的虛擬化硬體和軟體的組態和作業。 Azure 之所以功能強大，憑藉的就是此協調流程 - 使用者無須再負責硬體的維護和升級，一切都可由 Azure 在幕後代勞。 

## <a name="next-steps"></a>後續步驟

* 在您了解 Azure 的內部運作方式後，若要採用 Azure，您必須先[了解 Azure 中的數位身分識別](tenant-explainer.md)。 其後，您即可[在 Azure AD 中建立您的第一個使用者][docs-add-users-to-aad]。

<!-- Links -->

[cosmosdb]: /azure/cosmos-db/introduction
[docs-add-users-to-aad]: /azure/active-directory/add-users-azure-active-directory?toc=/azure/architecture/cloud-adoption-guide/toc.json
[vms]: /azure/virtual-machines/
[vnet]: /azure/virtual-network/virtual-networks-overview

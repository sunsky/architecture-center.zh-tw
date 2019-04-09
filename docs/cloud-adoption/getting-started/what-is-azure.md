---
title: CAF：Azure 如何運作？
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
description: Azure 內部運作方式的說明
author: petertaylor9999
ms.date: 02/11/2019
ms.openlocfilehash: 215e4e4954a2f3e722e3ac865fea19508f6edadd
ms.sourcegitcommit: 0a8a60d782facc294f7f78ec0e9033e3ee16bf4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068815"
---
<!-- markdownlint-disable MD026 -->

# <a name="how-does-azure-work"></a>Azure 如何運作？

Azure 是 Microsoft 的公用雲端平台。 Azure 提供服務，包括平台即服務 (PaaS) 基礎結構即服務 (IaaS)、 資料庫即服務 (DBaaS) 功能的大型的集合。 但 Azure 到底是什麼？它如何運作？

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ixGo]

<!-- markdownlint-enable MD034 -->

Azure 就像其他雲端平台一樣，需仰賴名為**虛擬化**的技術。 大部分的電腦硬體都可用軟體來模擬，因為大部分的電腦硬體都只是一組永久或半永久編碼在矽晶片材料中的指令。 使用將軟體指令對應至硬體指令的模擬層，虛擬化的硬體即可用軟體執行，如同在實際的硬體中執行一般。

基本上，雲端是一或多個資料中心內部代替客戶執行虛擬化硬體的一組實體伺服器。 那麼，雲端要如何同時為數百萬個客戶建立、啟動、停止及刪除數百萬個虛擬化硬體執行個體呢？

要了解這一點，就必須看看資料中心裡的硬體架構。  在每個資料中心內是伺服器集合設置在伺服器機架中。 每個伺服器機架都包含許多伺服器**刀鋒**，以及提供網路連線的網路交換器和提供電力的配電裝置 (PDU)。 機架有時會一起分組到較大的單位中，名為**叢集**。

在每個機架或叢集內，大部分的伺服器都會被指定用來代替使用者執行這些虛擬化硬體執行個體。 不過，部分伺服器執行稱為網狀架構控制器的雲端管理軟體。 **網狀架構控制器**是一種分散式應用程式，負責執行多項工作。 它會配置服務、監視伺服器及其執行之服務的健全狀況，並在伺服器故障時加以修復。

每個網狀架構控制器執行個體都會連線至執行雲端協調流程軟體的另一組伺服器，一般稱之為**前端**。 前端會主控 Web 服務、RESTful API，以及雲端執行的所有功能所使用的內部 Azure 資料庫。

例如，前端會主控處理客戶要求，例如配置 Azure 資源的服務[虛擬網路](/azure/virtual-network/virtual-networks-overview)，[虛擬機器](/azure/virtual-machines)，及服務，例如[Cosmos DB](/azure/cosmos-db/introduction). 首先，前端會驗證使用者，並確認使用者是否有權配置要求的資源。 如果是這樣，前端會檢查資料庫，以找出具有足夠容量的伺服器機架，並接著指示 網狀架構控制器配置資源的機架上。

因此基本上，Azure 會是大量的伺服器和網路硬體，在這些伺服器上執行一組複雜的分散式應用程式，以協調將組態和虛擬化的硬體和軟體的作業。 它是此協調流程使 Azure 如此強大&mdash;使用者不再負責維護和升級硬體，因為 Azure 會這一切背後運作。

## <a name="next-steps"></a>後續步驟

既然您了解 Azure 內部項目，深入了解雲端資源管理。

> [!div class="nextstepaction"]
> [深入了解資源管理](what-is-governance.md)

<!-- Links -->

[docs-add-users-to-aad]: /azure/active-directory/add-users-azure-active-directory?toc=/azure/architecture/cloud-adoption-guide/toc.json

---
title: CAF：軟體定義網路 - 雲端原生
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 討論雲端原生的虛擬網路服務
author: rotycenh
ms.openlocfilehash: c6200491bc9ba35a9f00e0003e51716b58628980
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900438"
---
# <a name="software-defined-networks-cloud-native"></a>軟體定義網路：雲端原生

將諸如虛擬機器之類的 IaaS 資源部署到雲端平台時，需要雲端原生虛擬網路。 從外部來源 (類似於 Web) 對虛擬網路的存取需要明確佈建。 這些類型的虛擬網路支援建立子網路、路由規則和虛擬防火牆，以及流量管理裝置。

雲端原生虛擬網路並不依賴您組織的內部部署或其他非雲端資源來支援雲端裝載的工作負載。 所有必要的資源都會佈建在虛擬網路本身，或使用受控 PaaS 供應項目進行佈建。

## <a name="cloud-native-assumptions"></a>雲端原生假設事項

部署雲端原生虛擬網路時會假設以下事項：

- 您對虛擬網路部署的工作負載，與只能從您內部部署網路內部存取的應用程式和密碼，兩者之間並沒有相依性。 除非它們提供可透過公用網際網路存取的端點，否則雲端平台裝載的資源無法使用內部部署內裝載的應用程式和服務。
- 您工作負載的身分識別管理和存取控制取決於雲端平台的身分識別服務或雲端環境中裝載的 IssS 伺服器。 您將不需要直接連線到內部部署或其他外部位置裝載的身分識別服務。
- 您的身分識別服務不需要支援單一登入 (SSO) 與內部部署目錄。

雲端原生虛擬網路沒有任何外部相依性。 這讓它們能夠很容易地部署和設定，因此此架構通常是適用於實驗用途或其他較小型獨立或快速重複部署的最佳選擇。

您的雲端採用小組在討論雲端原生虛擬網路架構時，應該考量以下其他問題：

- 設計成在內部部署資料中心執行的現有工作負載可能需要大規模修改，才能善用雲端的功能，例如儲存體或驗證服務。
- 雲端原生網路僅透過雲端平台管理工具進行管理，因此隨著時間過去，可能會導致管理和原則與您現有的 IT 標準產生出入。

## <a name="learn-more"></a>深入了解

如需 Azure 平台中雲端原生虛擬網路的詳細資訊，請參閱下列各項。

- [Azure 虛擬網路：使用說明指南](/azure/virtual-network/virtual-network-vnet-plan-design-arm)。 新建立的 Azure 虛擬網路預設為雲端原生網路。 您可以使用這些指南，協助規劃虛擬網路的設計與部署。
- [訂用帳戶限制：網路](/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)。 任何單一虛擬網路和連線的資源只能存在於單一訂用帳戶內，並受訂用帳戶限制約束。

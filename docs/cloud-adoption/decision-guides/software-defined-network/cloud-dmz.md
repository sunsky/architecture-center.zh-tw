---
title: CAF：軟體定義網路 - 雲端 DMZ
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 此網路架構允許內部部署和雲端式網路之間的有限存取
author: rotycenh
ms.openlocfilehash: a192541dcfb0f3d713f4139a2ab0541d0c7202db
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900919"
---
# <a name="software-defined-networks-cloud-dmz"></a>軟體定義網路：雲端 DMZ

透過虛擬私人網路 (VPN) 連線網路，雲端 DMZ 網路架構允許內部部署和雲端式網路之間的有限存取。 DMZ 部署在雲端，從雲端式資源保護對內部部署網路的存取。

![安全混合式網路架構](../../../reference-architectures/dmz/images/dmz-private.png)

此架構支援的案例為，您的組織想開始整合雲端式工作負載與內部部署工作負載，但雲端安全性原則可能尚未完全成熟，或尚未取得兩個環境之間安全的專用 WAN 連線。 因此，雲端網路應被視為周邊網路，以確保內部部署服務的安全。

DMZ 會部署網路虛擬裝置 (NVA) 以實作防火牆和封包檢查等安全性功能。 在內部部署與雲端式應用程式或服務之間傳遞的流量必須通過 DMZ 進行稽核。 VPN 連線以及判斷哪些流量可通過 DMZ 網路的規則由 IT 安全性小組嚴格控管。

## <a name="cloud-dmz-assumptions"></a>雲端 DMZ 假設

部署雲端 DMZ 會有下列假設：

- 您的安全性小組尚未完全符合內部部署和雲端式安全需求與原則。
- 您的雲端式工作負載能有限地存取內部部署或第三方網路上所裝載的服務，或者您的使用者或內部部署環境中的應用程式能有限地存取雲端所裝載的資源。
- 公司原則、法規需求或技術相容性問題無法阻止您在內部部署網路和雲端提供者之間實作 VPN。
- 您的工作負載不需透過多個訂用帳戶來避開資源限制，或者您的工作負載涉及多個訂用帳戶，但不需要集中管理分佈在多個訂用帳戶中的資源所使用的連線或共用服務。

您的雲端採用小組在查看實作雲端 DMZ 虛擬網路架構時，應該考量下列問題：

- 將內部部署網路與雲端網路連線，會使安全性需求變得更複雜。 即使雲端網路與內部部署環境之間的連線受到保護，您仍需要確保雲端資源的安全。
- 雲端 DMZ 架構常作為跳板，當內部部署與雲端網路之間的連線進一步受到保護且安全性原則一致時，讓您能更廣泛採用完整的混合式網路功能架構。

## <a name="learn-more"></a>深入了解

如需 Azure 平台中實作雲端 DMZ 的詳細資訊，請參閱下列各項。

- [實作 Azure 與內部部署資料中心之間的 DMZ](../../../reference-architectures/dmz/secure-vnet-hybrid.md)。 本文討論如何在 Azure 中實作安全的混合式網路架構。

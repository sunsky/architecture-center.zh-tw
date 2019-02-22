---
title: CAF：軟體定義網路
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 討論在 Azure 移轉中作為核心服務的軟體定義網路
author: rotycenh
ms.openlocfilehash: d164ba488552715dc97719329ae9de3fcf5d83ed
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901043"
---
# <a name="caf-software-defined-network-decision-guide"></a>CAF：軟體定義網路決策指南

軟體定義網路 (SDN) 是一種網路架構，其設計目的是允許已虛擬化的網路功能可透過軟體進行集中管理、設定及修改。 SDN 會透過實體網路基礎結構提供一個抽象層，並將相當於實體路由器、防火牆，以及您會在內部部署網路中找到的其他網路硬體虛擬化。

SDN 讓 IT 人員能夠設定和部署網路結構與功能，使用已虛擬化的資源來支援工作負載需求。 以軟體為基礎的部署管理彈性可讓您快速修改網路資源，並且能夠同時支援敏捷式和傳統部署模型。 使用 SDN 技術建立的已虛擬化網路，對於在公用雲端平台上建立安全網路而言非常重要。

## <a name="networking-decision-guide"></a>網路決策指南

![繪製符合下列快速連結的網路選項 (從最簡單到最複雜)](../../_images/discovery-guides/discovery-guide-sdn.png)

跳至：[僅限 PaaS](paas-only.md) | [雲端原生](cloud-native.md) | | [雲端 DMZ](cloud-dmz.md) [混合式](hybrid.md) | [中樞/輪輻模型](hub-spoke.md) | [深入了解](#learn-more)

SDN 提供數個選項，搭配各種不同程度的價格和複雜度。 上述探索指南提供可將這些選項快速個人化，以便最符合特定商務和技術策略的參考。

本指南中的轉折點取決於您的雲端策略小組在進行關於網路架構的決策之前所做的數個關鍵決策。 其中最重要的決策涉及您的[數位資產定義](../../digital-estate/overview.md)和[訂用帳戶設計](../subscriptions/overview.md) (這可能也需要您針對雲端帳戶處理和全球市場策略所做的相關決策提供輸入)。

少於 1,000 個 VM 的小型單一區域部署不太可能會顯著地受到此轉折點所影響。 相反地，如果大量採用超過 1,000 個 VM、多個營業單位或多個地理策略市場，則可能會大幅受到您的 SDN 決策和此關鍵轉折點所影響。

## <a name="choosing-the-right-virtual-networking-architectures"></a>選擇正確的虛擬網路架構

本節將細述決策指南，以協助您選擇正確的虛擬網路架構。

有許多方式可實作 SDN 技術來建立雲端式虛擬網路。 您如何建立要在移轉中使用的虛擬網路結構，以及那些網路如何與您現有 IT 基礎結構互動的方式，將取決於工作負載需求和您治理需求的組合。

規劃要在規劃雲端移轉時考量哪個虛擬網路架構或架構組合時，請考慮下列問題，以協助判斷何者適合您的組織：

| 問題 | 僅限 PaaS | 雲端原生 | 雲端 DMZ | 混合式 | 中樞與輪輻 |
|-----|-----|-----|-----|-----|-----|
| 您的工作負載將只會使用 PaaS 服務，而且除了服務本身所提供的網路功能，不需要任何網路功能嗎？ | yes | 否 | 否 | 否 | 否 |
| 您的工作負載需要與內部部署應用程式整合嗎？ | 否 | 否 | yes | 是 | yes |
| 您是否已在內部部署與雲端網路之間，建立了成熟的安全性原則和安全的連線？ | 否 | 否 | 否 | yes | yes |
| 您的工作負載是否需要不會透過雲端識別服務支援的驗證服務，或者您是否需要直接存取內部部署網域控制站？ | 否 | 否 | 否 | yes | yes |
| 您是否將需要部署和管理大量 VM 和工作負載？ | 否 | 否 | 否 | 否 | yes |
| 將對資源的控制委派給個別工作負載小組時，您是否將需要提供集中式管理與內部部署連線能力？ | 否 | 否 | 否 | 否 | yes |

## <a name="virtual-networking-architectures"></a>虛擬網路架構

深入了解主要的軟體定義網路架構：

- [**僅限 PaaS**](paas-only.md)：平台即服務 (PaaS) 產品支援一組有限的內建網路功能，而且可能不需要明確定義的軟體定義網路來支援工作負載需求。
- [**雲端原生**](cloud-native.md)：將資源部署到雲端平台時，雲端原生虛擬網路是預設的軟體定義網路架構。
- [**雲端 DMZ**](cloud-dmz.md)：在您的內部部署與雲端網路之間提供有限的連線能力，其會透過在雲端環境上實作非軍事區域而受到保護。
- [**混合式**](hybrid.md)：混合式雲端網路架構可讓虛擬網路存取您的內部部署資源，反之亦然。
- [**中樞與輪輻**](hub-spoke.md)：中樞與輪輻架構可讓您集中管理外部連線能力與共用服務、隔離個別工作負載，以及克服潛在的訂用帳戶限制。

## <a name="learn-more"></a>深入了解

如需 Azure 平台中軟體定義網路的詳細資訊，請參閱下列各項。

- [Azure 虛擬網路](/azure/virtual-network/virtual-networks-overview)。 在 Azure 上，核心 SDN 功能會由 Azure 虛擬網路所提供，以做為實體內部部署網路的雲端類比。 虛擬網路也可做為平台上資源之間的預設隔離界限。
- [Azure 網路安全性最佳做法](/azure/security/azure-security-network-security-best-practices)。 由 Azure 安全性小組所提供，對於如何設定您的虛擬網路以將安全性弱點最小化的建議。

## <a name="next-steps"></a>後續步驟

了解作業小組如何使用記錄、監視和報告，來管理雲端工作負載的健康情況和原則合規性。

> [!div class="nextstepaction"]
> [記錄和報告](../log-and-report/overview.md)

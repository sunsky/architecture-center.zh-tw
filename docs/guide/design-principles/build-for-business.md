---
title: 針對企業的需求而建置
titleSuffix: Azure Application Architecture Guide
description: 每個設計決策必須對應某個業務需求。
author: MikeWasson
ms.date: 08/30/2018
ms.custom: seojan19
ms.openlocfilehash: 872c4c5a6e0ecbecaa6736c63a7049a1ce514bf6
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54113377"
---
# <a name="build-for-the-needs-of-the-business"></a>針對企業的需求而建置

## <a name="every-design-decision-must-be-justified-by-a-business-requirement"></a>每個設計決策必須對應合理的商務需求

此設計原則似乎是顯而易見的，但在設計解決方案時請謹記。 您預期會有數百萬名或幾千名使用者？ 一小時的應用程式中斷可接受嗎？ 您預期流量會有大量缺口，或工作負載非常可預測？ 最後，每個設計決策必須對應合理的商務需求。

## <a name="recommendations"></a>建議

**定義商務目標**，包括復原時間目標 (RTO)、復原點目標 (RPO)，以及最大可容忍中斷 (MTO)。 決策人員應該了解架構相關的這些數字。 例如，為了達成低 RTO，您可能會將自動容錯移轉實作到次要區域。 但是，如果您的解決方案可以容忍較高的 RTO，可能就不需要該程度的備援。

**文件服務等級協定 (SLA) 和服務等級目標 (SLO)**，包括可用性和效能計量。 您可能會建立可提供 99.95%可用性的解決方案。 這足夠嗎？ 答案是業務決策。

**針對商務網域建立應用程式的模型**。 從分析商務需求開始。 使用這些需求來建立應用程式的模型。 考慮使用網域導向設計 (DDD) 方法來建立可反映商務程序和使用情節的[網域模型][domain-model]。

**擷取功能及非功能性需求**。 功能需求可讓您判斷應用程式的行為是否正確。 非功能性需求可讓您判斷應用程式的行為是否執行*良好*。 特別是，請確定您了解您的擴充性、可用性和延遲需求。 這些需求會影響設計決策和技術的選擇。

**依工作負載分解**。 「工作負載」一詞在此內容中是指不連續的功能或計算工作，可利用邏輯方式與其他工作分隔。 不同工作負載在可用性、延展性、資料一致性和災害復原方面可能有不同的需求。

**規劃成長**。 解決方案可能符合您目前的需求，就使用者數目、交易數量、資料存放區等而言。 但是，健全的應用程式在沒有重大架構變更下即可以處理成長。 請參閱[相應放大的設計](scale-out.md)和[分割處理限制](partition.md)。 也請考慮您的商務模型和商務需求將可能隨著時間變更。 如果應用程式的服務模型和資料模型太僵化，要針對新使用情節和情節演化應用程式會變得太困難。 請參閱[進化的設計](design-for-evolution.md)。

**管理成本**。 在傳統的內部部署應用程式中，您需預先支付硬體 (CAPEX)。 在雲端應用程式中，您需支付所使用的資源。 確定您了解所使用之服務的價格模型。 總成本將包含網路頻寬使用量、儲存體、IP 位址、服務使用，以及其他因素。 如需詳細資訊，請參閱 [Azure 價格][pricing]。 也請考慮您的作業成本。 在雲端中，您不必管理硬體或其他基礎結構，但仍然需要管理您的應用程式，包括 DevOps、事件回應、災害復原等。

[domain-model]: https://martinfowler.com/eaaCatalog/domainModel.html
[pricing]: https://azure.microsoft.com/pricing/

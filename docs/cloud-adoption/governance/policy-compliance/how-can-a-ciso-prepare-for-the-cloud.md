---
title: CAF：CISO 整備
description: CISO 如何為雲端做準備
author: BrianBlanchard
ms.date: 10/03/2018
ms.openlocfilehash: cedb86488304e2fc84897e1da373730768adce66
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901168"
---
# <a name="ciso-cloud-readiness-guide"></a>CISO 雲端整備指南

Azure 雲端採用架構 (CAF) 等 Microsoft 指引並無法判斷或引導由本文件所支援之數以千計企業的獨特安全性限制。 移動到雲端時，資訊安全長或資訊安全長辦公室 (CISO) 的角色並不會被雲端技術所取而代之。 相反地，CISO 和 CISO 的辦公室都將擔任起更加根深蒂固且整合的角色。 本指南假設讀者已熟悉 CISO 程序，並想要將那些程序現代化以進行雲端轉換。

雲端採用能提供通常不會在傳統 IT 環境中考慮使用的服務。 自助或自動化部署通常是由傳統上不會與生產部署直接關聯的應用程式開發或其他 IT 團隊來執行。 在某些組織中，其商務組成也同樣具有類似的自助功能。 這可能會產生先前不存在於內部部署環境中的新安全性需求。 集中式安全性更具挑戰性，因為安全性經常會成為商務與 IT 文化所共有的責任。 本文可以協助 CISO 針對該作法進行準備，並開始著手於漸進式的治理。

## <a name="how-can-the-ciso-prepare-for-the-cloud"></a>CISO 如何為雲端做準備

和大多數的原則相同，組織內的安全性與治理原則通常會自然地成長。 發生安全性事件時，這些事件會塑造出原則來通知使用者，並降低相同事件重複發生的機會。 此方式雖然自然，卻會產生原則膨脹與技術相依性。 雲端轉換旅程可提供獨特的機會，以針對原則進行現代化與重設。 在準備任何轉換旅程時，CISO 可透過擔任[原則檢閱](./what-is-a-cloud-policy-review.md)的主要關係人，來建立立即且可測量的值。

在這種檢閱中，CISO 必須負責在現有原則/合規性的限制，以及雲端提供者較為優異的安全性狀態之間，取得安全的平衡性。 此程序的測量可以透過許多形式達成，並通常會以可安全地卸載到雲端提供者的安全性原則數目來測量。

**傳輸安全性風險**：隨著服務被移到基礎結構即服務 (IaaS) 裝載模型，企業將能在硬體的佈建上肩負起較少的直接風險。 但此風險並沒有就此消失，而是被轉換到雲端廠商身上。 如果雲端廠商對硬體佈建所採取的作法能提供相同程度的風險降低，便代表可透過如此安全的可重複程序，將硬體佈建執行的風險從企業原則中移除。 它現在可能已被用於驗證那些程序的新原則索取但，但執行所帶來的風險已重新配置，進一步降低了整體的安全性風險。

隨著解決方案持續「向上堆疊」，以納入平台即服務 (PaaS) 或軟體即服務 (SaaS) 模型，這將能減輕、轉移並取代額外的風險。 當風險被安全地移到雲端提供者時，執行、監視及強制執行安全性原則或其他合規性原則的成本也都能安全地降低。

**成長心態**：變更對於企業 (以及技術實作者) 來說，可能是件令人憂心的事。 當組織中的 CISO 能夠率先轉換為成長心態時，我們發現那些自然而來的憂慮，都會轉換成對安全性及原則合規性與日俱增的興趣。 以抱持成長心態的方式來進行[原則檢閱](./what-is-a-cloud-policy-review.md)、轉換旅程，或是單純的實作檢閱，都能使團隊能迅速完成目標，同時維持公正且可管理的風險設定檔。

## <a name="resources-for-the-chief-information-security-officer"></a>資訊安全長的資源

對於雲端的認識，是抱持成長心態進行[原則檢閱](./what-is-a-cloud-policy-review.md)最重要的關鍵。 下列資源可協助 CISO 更加了解 Microsoft Azure 平台的安全性狀態。

安全性平台資源：

* [安全性開發週期、內部稽核](https://www.microsoft.com/sdl/) \(英文\)
* [必要的安全性訓練、背景檢查](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) \(英文\)
* [滲透測試、入侵偵測、DDoS、稽核和記錄](https://www.microsoft.com/trustcenter/Security/AuditingAndLogging) \(英文\)
* [尖端的資料中心](https://www.microsoft.com/cloud-platform/global-datacenters)、實體安全性、[安全網路](/azure/security/security-network-overview)
* [雲端的 Microsoft Azure 安全性回應](http://aka.ms/SecurityResponsePaper) \(英文\)

隱私權與控制：

* [隨時管理您的資料 (英文)](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data)
* [資料位置控制 (英文)](https://www.microsoft.com/trustcenter/Privacy/Where-your-data-is-located)
* [根據您的條款提供資料存取 (英文)](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)
* [回應執法機關 (英文)](https://www.microsoft.com/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data)
* [嚴格的隱私權標準 (英文)](https://www.microsoft.com/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards)

合規性：

* [信任中心](https://www.microsoft.com/trustcenter/default.aspx)
* [Common Controls Hub (英文)](https://www.microsoft.com/trustcenter/Common-Controls-Hub)
* [雲端服務審查評鑑檢查表 (英文)](https://www.microsoft.com/trustcenter/Compliance/Due-Diligence-Checklist)
* [依服務、位置和產業的合規性](https://www.microsoft.com/trustcenter/Compliance/default.aspx) \(英文\)

透明度：

* [Microsoft 如何保護 Azure 服務中的客戶資料 (英文)](https://www.microsoft.com/trustcenter/Transparency/default.aspx)
* [Microsoft 如何管理 Azure 服務中的資料位置](http://azuredatacentermap.azurewebsites.net/) \(英文\)
* [Microsoft 內部的哪些人員可根據哪些條款存取您的資料 (英文)](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)
* [Microsoft 如何保護 Azure 服務中的客戶資料 (英文)](https://www.microsoft.com/trustcenter/Transparency/default.aspx)
* [檢閱 Azure 服務的憑證、透明度中樞](https://www.microsoft.com/trustcenter/Compliance/default.aspx) \(英文\)

## <a name="next-steps"></a>後續步驟

針對任何治理策略採取動作的第一步，便是[原則檢閱](./what-is-a-cloud-policy-review.md)。 在您進行原則檢閱的期間，[原則與合規性](./overview.md)可能是個很有用的指南。

> [!div class="nextstepaction"]
> [針對原則檢閱進行準備](./what-is-a-cloud-policy-review.md)
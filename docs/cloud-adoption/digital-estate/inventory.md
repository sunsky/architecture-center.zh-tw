---
title: CAF：收集數位資產的清查資料
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: 收集數位資產清查的方式。
author: BrianBlanchard
ms.date: 12/10/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.openlocfilehash: 0c68ff1e5ff51395698d37fb9b59c7a76c9479b7
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55897247"
---
# <a name="gather-inventory-data-for-a-digital-estate"></a>收集數位資產的清查資料

開發清查是[數位資產規劃](overview.md)的第一個步驟。 在此程序中，將會收集支援特定企業功能的 IT 資產清單，以供日後分析與合理化。 本文假設以由下而上的方式進行分析最符合規劃需求。 如需詳細資訊，請參閱[數位資產規劃方法](./approach.md)。

## <a name="take-inventory-of-a-digital-estate"></a>清查數位資產

支援數位資產的清查會隨著所需數位轉型和對應的轉型程序而不同。

- **營運轉型**。 在營運轉型的過程中，一般通常會建議從掃描工具收集清查資料，因為這些工具可統合建立所有 VM 和伺服器的清單。 有些工具也可建立網路對應和相依性，這將有助於定義工作負載的調整。

- **漸進式轉型。** 漸進式轉型的清查會從客戶開始。 對應出客戶從頭到尾的體驗，是不錯的著手之處。 對應至應用程式、API、資料和其他資產的調整，將可建立詳細的清查資料以供分析之用。

- **革新式轉型。** 革新式轉型著重於產品或服務。 據此，清查將會對應出足以衝擊市場的機會以及所需的能力。

## <a name="accuracy-and-completeness-of-an-inventory"></a>清查的精確度和完整性

清查鮮少能靠一次反覆執行就完成。 我們強烈建議，雲端策略小組的各種成員應與專案關係人和進階使用者合作，以驗證清查。 如果可能的話，也可以使用網路和相依性分析等其他工具來識別正在傳送流量、但不在清查中的資產。

## <a name="next-steps"></a>後續步驟

清查資料在經過編譯和驗證後，即可進行合理化。 清查合理化是數位資產規劃的下一個步驟。

> [!div class="nextstepaction"]
> [合理化數位資產](rationalize.md)
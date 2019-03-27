---
title: 閘道卸載模式
titleSuffix: Cloud Design Patterns
description: 將共用或特殊服務功能卸載至閘道 Proxy。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 8be05c30ac974b3e58fb0decc52ab623fc5478c8
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58248329"
---
# <a name="gateway-offloading-pattern"></a>閘道卸載模式

將共用或特殊服務功能卸載至閘道 Proxy。 透過將共用服務功能 (例如 SSL 憑證的使用) 從應用程式的其他部分移動到閘道，此模式可以簡化應用程式開發。

## <a name="context-and-problem"></a>內容和問題

某些功能常會在多個服務間使用，而這些功能都需要設定、管理和維護。 隨著每個應用程式部署散發的共用或特殊服務會增加系統管理額外負荷，並增加部署錯誤的可能性。 必須在共用該功能的所有服務上部署對共用功能的任何更新。

正確處理安全性問題 (權杖驗證、加密、SSL 憑證管理)，以及其他複雜工作可能需要小組成員具備高度特殊的技能。 例如，必須在所有應用程式執行個體上設定和部署應用程式所需的憑證。 對於每個新部署，必須管理憑證，以確保它不會過期。 必須在每個應用程式部署上更新、測試及驗證即將到期的任何一般憑證。

其他常見的服務，例如驗證、授權、記錄、監視或[節流](./throttling.md)，在大量部署間可能難以實作和管理。 較好的方式是合併這種類型的功能，以減少額外負荷及錯誤的機會。

## <a name="solution"></a>解決方法

將某些功能卸載至 API 閘道，特別是跨領域考量，例如憑證管理、驗證、SSL 終止、監視、通訊協定轉譯，或是節流。

下圖顯示會終止輸入 SSL 連線的 API 閘道。 它會代表來自 API 閘道任何 HTTP 伺服器上游的原始要求者要求資料。

 ![閘道卸載模式圖](./_images/gateway-offload.png)

此模式的好處包括：

- 讓您不再需要散發及維護支援資源，例如 Web 伺服器憑證和安全網站的設定，簡化服務的開發。 較簡單的設定會導致更易於管理和延展性，並可使服務升級更簡單。

- 允許專用小組實作需要特殊專業技術的功能，例如安全性。 這可讓您的核心小組專注於應用程式功能，將特殊化但跨領域考量留給相關的專家。

- 為要求和回應記錄和監視提供一些一致性。 即使未正確檢測服務，也可以設定閘道以確保最低層級的監視和記錄。

## <a name="issues-and-considerations"></a>問題和考量

- 確定 API 閘道高度可用且能夠從失敗復原。 執行 API 閘道的多個執行個體，以避免單一失敗點。
- 請確定閘道已針對應用程式和端點的容量和調整需求設計。 確定閘道不會成為應用程式的瓶頸，而且可以完全擴充。
- 只卸載整個應用程式使用的功能，例如安全性或資料傳輸。
- 永遠不應將商務邏輯卸載到 API 閘道。
- 如果您需要追蹤交易，請考慮針對記錄目的產生相互關聯識別碼。

## <a name="when-to-use-this-pattern"></a>使用此模式的時機

使用此模式的時機包括：

- 應用程式部署具有共用問題，例如 SSL 憑證或加密。
- 可能有不同資源需求 (例如：記憶體資源、儲存容量或網路連線) 的應用程式部署之間共通的功能。
- 您想要將網路安全性、節流設定或其他網路界限考量之類問題的責任轉移給更特殊的小組。

如果跨服務引入了結合，則此模式可能不適合。

## <a name="example"></a>範例

使用 Nginx 做為 SSL 卸載應用裝置，下列設定會終止連入的 SSL 連線，並將連線散發至三個上游 HTTP 伺服器的其中一個。

```console
upstream iis {
        server  10.3.0.10    max_fails=3    fail_timeout=15s;
        server  10.3.0.20    max_fails=3    fail_timeout=15s;
        server  10.3.0.30    max_fails=3    fail_timeout=15s;
}

server {
        listen 443;
        ssl on;
        ssl_certificate /etc/nginx/ssl/domain.cer;
        ssl_certificate_key /etc/nginx/ssl/domain.key;

        location / {
                set $targ iis;
                proxy_pass http://$targ;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto https;
proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header Host $host;
        }
}
```

在 Azure 上，可以藉由[在應用程式閘道上設定 SSL 終止](/azure/application-gateway/tutorial-ssl-cli)來完成此作業。

## <a name="related-guidance"></a>相關的指引

- [針對前端的後端模式](./backends-for-frontends.md)
- [閘道彙總模式](./gateway-aggregation.md)
- [閘道路由模式](./gateway-routing.md)

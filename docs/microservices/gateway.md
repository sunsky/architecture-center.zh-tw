---
title: API 閘道
description: 微服務中的 API 閘道。
author: MikeWasson
ms.date: 10/23/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: microservices
ms.openlocfilehash: 84add394ee9ae6fa3c0df19b5a263dab66a6b140
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54485872"
---
# <a name="designing-microservices-api-gateways"></a>設計微服務：API 閘道

在微服務架構中，用戶端可能會與多個前端服務互動。 因此，用戶端要如何知道應該呼叫哪些端點？ 引進新的服務或重構現有服務時，會發生什麼事？ 服務如何處理 SSL 終止、驗證和其他考量？ 「API 閘道」可協助您解決這些挑戰。

![API 閘道圖](./images/gateway.png)

<!-- markdownlint-disable MD026 -->

## <a name="what-is-an-api-gateway"></a>什麼是 API 閘道？

<!-- markdownlint-enable MD026 -->

API 閘道位於用戶端和服務之間。 它會作為反向 Proxy，將要求從用戶端路由傳送到服務。 它也會執行各種跨領域工作，例如驗證、SSL 終止和速率限制。 如果您未部署閘道，用戶端就必須將要求直接傳送給前端服務。 不過，直接向用戶端公開服務會有一些潛在問題：

- 它會導致複雜的用戶端程式碼。 用戶端必須追蹤多個端點，並以彈性的方式處理失敗。
- 它會讓用戶端與後端發生結合。 用戶端需要知道個別服務的分解方式。 這會讓您難以維護用戶端，也較不容易重構服務。
- 單一作業可能需要呼叫多個服務。 此動作會讓用戶端與伺服器之間有多次網路往返，而大幅增加延遲時間。
- 每個公眾對應的服務都必須處理各種考量，例如驗證、SSL 及用戶端速率限制。
- 服務必須公開用戶端熟悉的通訊協定，例如 HTTP 或 WebSocket。 這會限制[通訊協定](./interservice-communication.md)的選擇。
- 具有公用端點的服務是潛在的受攻擊面，因此必須予以強化。

閘道可透過讓用戶端與服務分離來協助解決這些問題。 閘道可以執行幾種不同的功能，您不一定需要全部使用。 功能可分為下列設計模式：

[閘道路由](../patterns/gateway-routing.md)。 將閘道作為反向 Proxy，以使用第 7 層路由將要求路由傳送至一或多個後端服務。 閘道會為用戶端提供單一端點，並有助於讓用戶端與服務分離。

[閘道彙總](../patterns/gateway-aggregation.md)。 您可以使用閘道將多個個別要求彙總為單一要求。 當單一作業需要呼叫多個後端服務時，便適用此模式。 用戶端會將一個要求傳送至閘道。 閘道會將要求分派至各種後端系統，然後彙總結果並傳回給用戶端。 這有助於減少用戶端與後端之間的通訊頻率。

[閘道卸載](../patterns/gateway-offloading.md)。 您可以使用閘道將功能從個別服務卸載到閘道 (特別是在有跨領域考量時)。 它可用來將這些功能合併到一個地方，而不是讓每項服務負責實作這些功能。 對於需要專門技術才能正確實作的功能 (例如驗證和授權)，尤其應該這麼做。

以下是可以卸載到閘道的一些功能範例：

- SSL 終止
- 驗證
- IP 允許清單
- 用戶端速率限制 (節流)
- 記錄和監視
- 回應快取
- Web 應用程式防火牆
- GZip 壓縮
- 提供靜態內容

## <a name="choosing-a-gateway-technology"></a>選擇閘道技術

以下是在應用程式中實作 API 閘道的一些選項。

- **反向 Proxy 伺服器**。 Nginx 和 HAProxy 等熱門的反向 Proxy 伺服器可支援負載平衡、SSL 和第 7 層路由等功能。 兩者都是免費的開放原始碼產品，而其付費版則會提供額外的功能和支援選項。 Nginx 及 HAProxy 都是成熟的產品，並具有豐富的功能集和高效能。 您可以透過第三方模組或在 Lua 中撰寫自訂指令碼來對兩者進行擴充。 Nginx 也支援稱為 NginScript 的 JavaScript 型指令碼模組。

- **服務網格輸入控制器**。 如果您要使用 linkerd 或 Istio 等服務網格，請考慮使用輸入控制器針對該服務網格所提供的功能。 例如，Istio 輸入控制器可支援第 7 層路由、HTTP 重新導向、重試和其他功能。

- [Azure 應用程式閘道](/azure/application-gateway/)。 應用程式閘道是受控的負載平衡服務，可執行第 7 層路由和 SSL 終止。 此外，也會提供 Web 應用程式防火牆 (WAF)。

- [Azure API 管理](/azure/api-management/)。 Azure API 管理是周全的解決方案，可將 API 發佈給外部及內部客戶。 它提供的功能可用於管理公眾對應 API，包括速率限制、IP 允許清單建立，以及使用 Azure Active Directory 或其他識別提供者來進行的驗證。 API 管理不會執行任何負載平衡作業，因此應該與負載平衡器搭配使用，例如應用程式閘道或反向 Proxy。 如需搭配使用 API 管理與應用程式閘道的相關資訊，請參閱[整合內部 VNET 中的 API 管理與應用程式閘道](/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)。

在選擇閘道技術時，請考慮下列因素：

**功能**。 以上列出的選項皆支援第 7 層路由，但對於其他功能的支援則各不相同。 根據您所需要的功能，您可能會部署多個閘道。

**部署**。 Azure 應用程式閘道與 API 管理是受控服務。 Nginx 及 HAProxy 一般會在叢集內的容器中執行，但也可部署到叢集外的專用 VM。 這麼做可將閘道與其他工作負載隔離開來，但會產生較高的管理負荷。

**管理**。 當服務經過更新或有新增的服務時，您可能需要更新閘道路由規則。 請考慮該如何管理此程序。 管理 SSL 憑證、IP 允許清單和其他組態部分也會有類似考量。

## <a name="deploying-nginx-or-haproxy-to-kubernetes"></a>將 Nginx 或 HAProxy 部署到 Kubernetes

您可以將 Nginx 或 HAProxy 部署至 Kubernetes 來作為 [ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) 或 [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)，以指定 Nginx 或 HAProxy 容器映像。 使用 ConfigMap 來儲存 Proxy 的組態檔，並將 ConfigMap 掛接為磁碟區。 建立 LoadBalancer 類型的服務，以透過 Azure Load Balancer 公開閘道。

另一種方法是建立輸入控制器。 輸入控制器是一種 Kubernetes 資源，可部署負載平衡器或反向 Proxy 伺服器。 存在的實作有好幾種，包括 Nginx 及 HAProxy。 另有稱為輸入的資源會定義輸入控制器的設定，例如路由規則和 TLS 憑證。 這樣一來，您就不需要管理專屬於特定 Proxy 伺服器技術的複雜組態檔。

閘道是系統中的潛在瓶頸或單一失敗點，因此請一律部署至少兩個複本以獲得高可用性。 您可以視負載而定將複本進一步相應放大。

也請考慮在叢集中的一組專用節點上執行閘道。 此方法的好處包括：

- 隔離。 所有輸入流量會流向一組固定節點，因而可以與後端服務隔開。

- 穩定的設定。 如果閘道的設定不正確，整個應用程式可能會變得無法使用。

- 效能。 基於效能的考量，建議您對閘道使用特定的 VM 設定。

> [!div class="nextstepaction"]
> [記錄和監視](./logging-monitoring.md)

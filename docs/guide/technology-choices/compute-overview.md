---
title: "Azure 計算選項的概觀"
description: "Azure 計算選項的概觀"
author: MikeWasson
ms.openlocfilehash: a23dd49f24bc52db6f357540e3ebccb19e0497ee
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="overview-of-azure-compute-options"></a>Azure 計算選項的概觀

*計算*一詞是指您的應用程式執行所在運算資源的裝載模型。 

頻譜的一端是 **基礎結構即服務** (IaaS)。 有了 IaaS，您可以佈建您需要的 VM，以及相關聯的網路和儲存體元件。 然後您會部署您想放在這些 VM 上的任何軟體和應用程式。 此模型最接近傳統內部部署環境，不同之處在於 Microsoft 會管理基礎結構。 您還是可以管理個別的 VM。  

**平台即服務** (PaaS) 提供受管理裝載環境，您可以在其中部署您的應用程式而不需要管理 VM 或網路資源。 例如，不要建立個別的 VM，您可以指定執行個體計數，而服務將會佈建、設定和管理所需的資源。 Azure App Service 是 PaaS 服務的範例。

從 IaaS 到純 PaaS 有一個頻譜。 例如，Azure VM 可以使用 VM 擴展集來自動調整。 此自動調整功能不完全是 PaaS，但它是可能在 PaaS 服務中找到的管理功能類型。

**功能即服務** (FaaS) 則更進一步，讓您不需擔心裝載環境。 不要建立計算執行個體並將程式碼部署至那些執行個體，您只需部署您的程式碼，服務即會自動執行程式碼。 您不需要管理計算資源。 這些服務會利用無伺服器架構，且順暢地相應增加或相應減少處理流量所需的任何層級。 Azure Functions 是 FaaS 服務。

IaaS 提供最大控制、彈性和可攜性。 FaaS 提供簡潔、彈性的調整，與潛在的成本節省，因為您只針對您的程式碼執行的時間付費。 PaaS 介於兩者之間。 一般情況下，服務提供的彈性愈大，對於設定和管理資源，您要負責的愈多。 FaaS 服務會自動管理近乎執行中應用程式的所有層面，而 IaaS 解決方案需要您佈建、設定及管理 VM 和您所建立的網路元件。

以下是 Azure 中目前可用的主要計算選項：

- [虛擬機器](/azure/virtual-machines/)是 IaaS 服務，可讓您部署及管理虛擬網路 (VNet) 內的 VM。
- [App Service](/azure/app-service/app-service-value-prop-what-is) 是用於裝載 Web 應用程式、行動裝置應用程式後端、RESTful API 或自動化商務程序的受管理服務。
- [Service Fabric](/azure/service-fabric/service-fabric-overview) 是可以在許多環境中 (包括 Azure 或內部部署) 執行的分散式系統平台。 Service Fabric 是跨電腦叢集的微服務協調者。 
- [Azure Container Service](/azure/container-service/container-service-intro) 可讓您建立、設定和管理虛擬機器的叢集，這些虛擬機器預先設定為執行容器化應用程式。
- [Azure Functions](/azure/azure-functions/functions-overview) 是受管理的 FaaS 服務。
- [Azure Batch](/azure/batch/batch-technical-overview) 是受管理服務，可用於執行大規模的平行和高效能運算 (HPC) 應用程式。
- [雲端服務](/azure/cloud-services/cloud-services-choose-me)是用於執行雲端應用程式的受管理服務。 它使用 PaaS 裝載模型。 

選取計算選項時，以下是要考慮的因素：

- 裝載模型。 服務的裝載方式？ 此裝載環境會施加哪些需求和限制？ 
- DevOps。 對於應用程式升級有內建支援嗎？ 什麼是部署模型？
- 延展性。 服務如何處理新增或移除執行個體？ 它可以根據負載和其他計量自動調整嗎？ 
- 可用性。 什麼是服務 SLA？ 
- 成本。 除了服務本身的成本，請考慮用於管理該服務上所建置解決方案的作業成本。 例如，IaaS 解決方案可能會有較高的作業成本。
- 每個服務的整體限制為何？ 
- 此服務適用什麼種類的應用程式架構？ 

如需 Azure 中的計算選項的詳細比較，請參閱[選擇 Azure 計算選項的準則](./compute-comparison.md)。
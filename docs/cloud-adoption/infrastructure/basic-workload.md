---
title: 企業雲端採用：部署基本工作負載
description: 描述如何將基本工作負載部署到 Azure
author: petertaylor9999
ms.date: 09/10/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: Windows, Linux
ms.openlocfilehash: 031a8f2e1dc0b137fc830733d025997a2657ef3c
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54481400"
---
# <a name="enterprise-cloud-adoption-deploy-a-basic-workload"></a>企業雲端採用：部署基本工作負載

**工作負載**這個詞彙通常被理解為定義任意單位的功能，例如應用程式或服務。 我們會以部署到伺服器的程式碼成品角度，以及任何必要的其他服務來思考工作負載。 這對於內部部署應用程式或服務是有用的定義，但是在雲端中我們就需要加以延伸。

在雲端中，工作負載不僅是包括所有成品，同時也包含雲端資源。 我們包含雲端資源作為定義的一部分，是因為所謂基礎結構即程式碼的概念。 如同您在 [Azure 的運作方式？](../getting-started/what-is-azure.md)說明中所了解的，Azure 中的資源是由協調器服務部署。 協調器服務會透過 Web API 公開這個功能，此 Web API 可以使用數個工具來呼叫，例如 Powershell、Azure 命令列介面 (CLI) 以及 Azure 入口網站。 這表示我們可以在電腦可讀取檔案中指定我們的資源，該檔案可以與程式碼成品 (與我們的應用程式相關聯) 一起儲存。

可以讓我們以程式碼成品和必要雲端資源的角度定義工作負載，進一步讓我們隔離我們的工作負載。 工作負載可能會依據資源的組織方式、網路拓撲或其他屬性，而遭到隔離。 工作負載隔離的目標是要將工作負載的特定資源關聯至小組，讓小組可以獨立管理這些資源的所有層面。 這可讓多個小組共用 Azure 中的資源管理服務，同時防止不小心刪除或修改彼此的資源。

這個隔離也會啟用稱為 DevOps 的另一個概念。 DevOps 包含軟體開發實務，其中包含上述的軟體開發和 IT 作業，但是盡可能新增自動化的使用。 DevOps 的其中一個原則稱為持續整合與持續傳遞 (CI/CD)。 持續整合是指開發人員認可程式碼變更時每次都會執行的自動化建置程序，而持續傳遞是指將這個程式碼部署到各種環境 (例如進行測試的開發環境或進行最終部署的生產環境) 的自動化程序。

## <a name="basic-workload"></a>基本工作負載

**基本工作負載**通常會定義為單一 Web 應用程式，或包含虛擬機器 (VM) 的虛擬網路 (VNet)。 

> [!NOTE]
> 本指南並未涵蓋應用程式開發。 如需有關在 Azure 上開發應用程式的詳細資訊，請參閱 [Azure 應用程式架構指南](/azure/architecture/guide/)。

不論工作負載是 Web 應用程式或 VM，每個部署都需要**資源群組**。 具有權限建立資源群組的使用者必須先建立資源群組，然後再遵循下列步驟。

## <a name="basic-web-application-paas"></a>基本 Web 應用程式 (PaaS)

針對基本 Web 應用程式，請從 [Web 應用程式文件](/azure/app-service?toc=/azure/architecture/cloud-adoption-guide/toc.json)中選取其中一個 5 分鐘快速入門，並遵循其中的步驟。 

> [!NOTE]
> 部分快速入門預設會部署資源群組。 在此情況下，不需要明確建立的資源群組。 否則，請將 Web 應用程式部署到先前建立的資源群組。

一旦部署了簡單工作負載，您就可以深入了解將[基本 Web 應用程式](/azure/architecture/reference-architectures/app-service-web-app/basic-web-app?toc=/azure/architecture/cloud-adoption-guide/toc.json)部署至 Azure 的經實證做法。

## <a name="single-windows-or-linux-vm-iaas"></a>單一 Windows 或 Linux VM (IaaS)

針對在虛擬機器上執行的簡單工作負載，第一個步驟是部署虛擬網路。 Azure 中的所有 IaaS 資源 (例如虛擬機器、負載平衡器和閘道) 都需要虛擬網路。 請了解 [Azure 虛擬網路](/azure/virtual-network/virtual-networks-overview?toc=/azure/architecture/cloud-adoption-guide/toc.json)，然後遵循步驟以[使用入口網站將虛擬網路部署至 Azure](/azure/virtual-network/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json)。 當您在 Azure 入口網站中指定虛擬網路的設定時，請為先前建立的資源群組指定名稱。

下一個步驟是決定要部署單一 Windows 或 Linux VM。 針對 Windows VM，請遵循步驟以[使用入口網站將 Windows VM 部署至 Azure](/azure/virtual-machines/windows/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json)。 同樣地，當您在 Azure 入口網站中指定虛擬機器的設定時，請為先前建立的資源群組指定名稱。

一旦您遵循步驟並部署了 VM，您就可以了解[在 Azure 上執行 Windows VM 的經實證做法](/azure/architecture/reference-architectures/virtual-machines-windows/single-vm?toc=/azure/architecture/cloud-adoption-guide/toc.json)。 針對 Linux VM，請遵循步驟以[使用入口網站將 Linux VM 部署至 Azure](/azure/virtual-machines/linux/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json)。 您也可以深入了解[在 Azure 上執行 Linux VM 的經實證做法](/azure/architecture/reference-architectures/virtual-machines-linux/single-vm?toc=/azure/architecture/cloud-adoption-guide/toc.json)。

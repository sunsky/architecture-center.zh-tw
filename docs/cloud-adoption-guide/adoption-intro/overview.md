---
title: 採用 Azure：基本
description: 說明企業要採用 Azure 所需具備的基本知識
author: petertay
ms.openlocfilehash: b5a0a4a2c4ed1d06c97774b0eca643a89a5a2110
ms.sourcegitcommit: c704d5d51c8f9bbab26465941ddcf267040a8459
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2018
ms.locfileid: "39229162"
---
# <a name="adopting-microsoft-azure-foundational"></a>採用 Microsoft Azure：基礎

對於雲端技術新手的組織而言，決定要從哪裡開始採用會有困難。 基礎採用階段的目標是提供一個起點。 一旦組織中的個人完成此階段，就會具備將簡單工作負載的計算資源部署至 Azure 所需的所有知識和技能。 

> [!NOTE]
> 本指南並未涵蓋應用程式開發。 如需有關在 Azure 上開發應用程式的詳細資訊，請參閱 [Azure 應用程式架構指南](/azure/architecture/guide/)。

本指南這個階段的對象是組織內的下列角色：

- 財務：對 Azure 進行財務認可的擁有者，負責開發原則和追蹤資源耗用量成本的程序，包括計費和退款。
- 中央 IT：負責治理貴組織的雲端資源，包括資源管理和存取，以及工作負載健康情況和監視。
- 工作負載擁有者：將工作負載部署至 Azure 所牽涉到的所有開發角色，包括開發人員、測試人員和建置工程師。

## <a name="section-1-azure-basics"></a>第 1 節：Azure 基本概念

本簡介章節適用於「財務」和「中央 IT」角色。 本節焦點在於取得 [Azure 運作方式](azure-explainer.md)的基本了解，以準備深入了解[雲端治理概念](governance-explainer.md)。 對於貴組織中的「工作負載擁有者」，檢閱此內容也很有用，可以協助他們了解資源存取權的管理方式。

## <a name="section-2-governance-design-guide"></a>第 2 節：治理設計指南

既然您已了解 Azure 運作方式和雲端治理的基本概念，採用 Azure 的第一個步驟是學習在 Azure 中的[資源存取權管理](azure-resource-access.md)。 本文說明用來進行資源存取權要求的 Azure 服務，和用來驗證這些要求的控制項。

下一步是學習如何針對單一小組[設計治理模型](governance-how-to.md)。 本文說明如何設定您稍早所學的資源存取權管理服務和控制項。

## <a name="section-3-implementing-a-basic-resource-access-management-model"></a>第 3 節：實作基本資源存取權管理模型

採用旅程中的最後一個步驟是了解如何實作先前所設計的治理模型。 

若要開始，貴組織需要 Azure 帳戶。 如果貴組織擁有現有 [Microsoft Enterprise 合約](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx)，但是其中不包含 Azure，可以藉由預先付款承諾來新增 Azure。 如需詳細資訊，請參閱 [Azure 企業授權](https://azure.microsoft.com/pricing/enterprise-agreement/)。 

在建立您的 Azure 帳戶時，會指定貴組織中的某位人員成為 Azure **帳戶擁有者**。 預設會接著建立 Azure Active Directory (Azure AD) 租用戶。 您的 Azure **帳戶擁有者**，必須為組織中身為**工作負載擁有者**的人員[建立使用者帳戶](/azure/active-directory/add-users-azure-active-directory)。 

接下來，您的 Azure **帳戶擁有者**必須[建立訂用帳戶](https://docs.microsoft.com/partner-center/create-a-new-subscription)，並且將其[關聯到 Azure AD 租用戶](/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory)。

最後，既然已建立訂用帳戶，且您的 Azure AD 租用戶已與其相關聯，就可以將**工作負載擁有者**[，新增至具備內建**擁有者**角色](/azure/billing/billing-add-change-azure-subscription-administrator#add-an-rbac-owner-for-a-subscription-in-azure-portal)的訂用帳戶。

## <a name="section-4-deploy-a-basic-workload-architecture-to-azure"></a>第 4 節：將基本工作負載架構部署至 Azure

本節的對象是「工作負載擁有者」角色。 「工作負載擁有者」會為其工作負載定義計算和網路需求、選取正確的資源以符合這些需求，並將其部署至 Azure。 

針對基礎採用階段，工作負載擁有者可以選取基本 Web 應用程式或虛擬網路 (VNet) 和虛擬機器 (VM)。 如需有關 Azure 中這些不同類型計算選項的詳細資訊，請檢閱 [Azure 計算選項概觀](/azure/architecture/guide/technology-choices/compute-overview?toc=/azure/architecture/cloud-adoption-guide/toc.json)。

不論選取哪一個計算選項，這些部署都需要**資源群組**。 您的**工作負載擁有者**必須[建立資源群組](/azure/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy)。 部署的其中一部分是**工作負載擁有者**指定資源群組的名稱。 這個名稱會在下列各節中使用。

### <a name="basic-web-application-paas"></a>基本 Web 應用程式 (PaaS)

針對基本 Web 應用程式，請從 [Web 應用程式文件](/azure/app-service?toc=/azure/architecture/cloud-adoption-guide/toc.json)中選取其中一個 5 分鐘快速入門，並遵循其中的步驟。 

> [!NOTE]
> 部分快速入門預設會部署資源群組。 在此情況下，**工作負載擁有者**不需要明確建立資源群組。 否則，請將 Web 應用程式部署到先前建立的資源群組。

一旦部署了簡單工作負載，您就可以深入了解將[基本 Web 應用程式](/azure/architecture/reference-architectures/app-service-web-app/basic-web-app?toc=/azure/architecture/cloud-adoption-guide/toc.json)部署至 Azure 的經實證做法。

### <a name="single-windows-or-linux-vm-iaas"></a>單一 Windows 或 Linux VM (IaaS)

針對在虛擬機器上執行的簡單工作負載，第一個步驟是部署虛擬網路。 Azure 中的所有 IaaS 資源 (例如虛擬機器、負載平衡器和閘道) 都需要虛擬網路。 請了解 [Azure 虛擬網路](/azure/virtual-network/virtual-networks-overview?toc=/azure/architecture/cloud-adoption-guide/toc.json)，然後遵循步驟以[使用入口網站將虛擬網路部署至 Azure](/azure/virtual-network/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json)。 當您在 Azure 入口網站中指定虛擬網路的設定時，請為先前建立的資源群組指定名稱。

下一個步驟是決定要部署單一 Windows 或 Linux VM。 針對 Windows VM，請遵循步驟以[使用入口網站將 Windows VM 部署至 Azure](/azure/virtual-machines/windows/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json)。 同樣地，當您在 Azure 入口網站中指定虛擬機器的設定時，請為先前建立的資源群組指定名稱。

一旦您遵循步驟並部署了 VM，您就可以了解[在 Azure 上執行 Windows VM 的經實證做法](/azure/architecture/reference-architectures/virtual-machines-windows/single-vm?toc=/azure/architecture/cloud-adoption-guide/toc.json)。 針對 Linux VM，請遵循步驟以[使用入口網站將 Linux VM 部署至 Azure](/azure/virtual-machines/linux/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json)。 您也可以深入了解[在 Azure 上執行 Linux VM 的經實證做法](/azure/architecture/reference-architectures/virtual-machines-linux/single-vm?toc=/azure/architecture/cloud-adoption-guide/toc.json)。

## <a name="next-steps"></a>後續步驟

雲端整備的下一個階段是[**中繼階段**](../intermediate-stage/overview.md)。 在中繼階段中，您將了解擴充內部部署網路，該網路針對多個小組執行多個工作負載和治理模型。
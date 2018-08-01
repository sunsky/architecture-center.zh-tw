---
title: 採用 Azure：中繼
description: 說明企業要採用 Azure 所需具備的中繼層級知識
author: petertay
ms.openlocfilehash: a1f93616f5f1ecf4f395ce39bbb037ef6ab5991b
ms.sourcegitcommit: c704d5d51c8f9bbab26465941ddcf267040a8459
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2018
ms.locfileid: "39229230"
---
# <a name="azure-cloud-adoption-guide-intermediate-overview"></a>Azure 雲端採用指南：中繼概觀

在基礎採用階段中，我們會介紹 Azure 資源治理的基本概念。 基礎階段的設計目的是協助您開始您的 Azure 採用過程，它會逐步引導您部署單一小型小組的簡單工作負載。 實際上，大部分大型組織有許多小組同時間處理許多不同的工作負載。 如同您的預期，簡單的治理模型不足以管理更加複雜的組織和開發案例。

Azure 採用中繼階段的焦點是設計您的治理模型，讓多個小組在多個新的 Azure 開發工作負載上運作。  

本指南這個階段的對象是組織內的下列角色：
- 財務：對 Azure 進行財務認可的擁有者，負責開發原則和追蹤資源耗用量成本的程序，包括計費和退款。
- 中央 IT：負責治理貴組織的雲端資源，包括資源管理和存取，以及工作負載健康情況和監視。
- 共用基礎結構擁有者：技術角色，負責從內部部署到雲端的網路連線。
- 安全性作業：負責實作將內部部署安全性界限擴充為包含 Azure 所需的安全性原則。 可能也擁有 Azure 中的安全性基礎結構以儲存祕密。
- 工作負載擁有者：負責將工作負載發行至 Azure。 根據貴組織開發小組的結構，這可能是開發負責人、程式管理負責人或建置工程負責人。 發行程序可能會包含將資源部署至 Azure。
  - 工作負載參與者：負責將工作負載發行至 Azure 的參與。 可能需要 Azure 資源的讀取權限，以進行效能監視或調整。 不需要建立、更新或刪除資源的權限。

## <a name="section-1-azure-concepts-for-multiple-workloads-and-multiple-teams"></a>第 1 節：多個工作負載和多個小組的 Azure 概念

在基礎採用階段中，您已了解有關 Azure 內部與如何建立、讀取、更新和刪除資源的一些基本概念。 您也了解身分識別，且 Azure 只信任 Azure Active Directory (AD) 驗證及授權需要這些資源存取權的使用者。

您也會開始了解如何設定 Azure 的治理工具，來管理貴組織對於 Azure 資源的使用。 我們討論了如何治理單一小組部署簡單工作負載所需的資源存取權。 事實上，貴組織會組成多個小組，同時處理多個工作負載。 

開始之前，讓我們看看**工作負載**這個詞彙真正的意義。 這個詞彙通常被理解為定義任意單位的功能，例如應用程式或服務。 我們會以部署到伺服器以及所需任何其他服務 (例如資料庫) 的程式碼成品角度來思考工作負載。 這對於內部部署應用程式或服務是有用的定義，但是在雲端中我們就需要加以延伸。 

在雲端中，工作負載不僅是包括所有成品，同時也包含雲端資源。 我們包含雲端資源作為定義的一部分，是因為所謂**基礎結構即程式碼**的概念。 如同您在「Azure 的運作方式」說明中所了解的，Azure 中的資源是由協調器服務部署。 協調器服務會透過 Web API 公開這個功能，此 Web API 可以使用數個工具來呼叫，例如 Powershell、Azure 命令列介面 (CLI) 以及 Azure 入口網站。 這表示我們可以在電腦可讀取檔案中指定我們的資源，該檔案可以與程式碼成品 (與我們的應用程式相關聯) 一起儲存。

可以讓我們以程式碼成品和必要雲端資源的角度定義工作負載，進一步讓我們**隔離**我們的工作負載。 工作負載可能會依據資源的組織方式、網路拓撲或其他屬性，而遭到隔離。 工作負載隔離的目標是要將工作負載的特定資源關聯至小組，讓小組可以獨立管理這些資源的所有層面。 這可讓多個小組共用 Azure 中的資源管理服務，同時防止不小心刪除或修改彼此的資源。

這個隔離也會啟用稱為 [DevOps](https://azure.microsoft.com/solutions/devops/) 的另一個概念。 DevOps 包含軟體開發實務，其中包含上述的軟體開發和 IT 作業，但是盡可能新增自動化的使用。 DevOps 的其中一個原則稱為持續整合與持續傳遞 (CI/CD)。 持續整合是指開發人員認可程式碼變更時每次都會執行的自動化建置程序，而持續傳遞是指將這個程式碼部署到各種**環境** (例如進行測試的**開發環境**或進行最終部署的**生產環境**) 的自動化程序。

## <a name="section-2-governance-design-for-multiple-teams-and-multiple-workloads"></a>第 2 節：多個小組和多個工作負載的治理設計

在 Azure 雲端採用指南的[基礎階段](/azure/architecture/cloud-adoption-guide/adoption-intro/overview)中，我們會為您介紹雲端治理的概念。 您已了解如何為在單一工作負載上運作的單一小組設計簡單治理模型。 

在中繼階段中，[治理設計指南](governance-design-guide.md)會將基礎概念延伸為新增多個小組、多個工作負載和多個環境。 一旦您完成文件中的範例，您可以將設計原則套用至貴組織治理模型的設計和實作。

## <a name="section-3-implementing-a-resource-management-model"></a>第 3 節：實作資源管理模型

貴組織的雲端治理模型代表 Azure 的資源存取管理工具、您的人員與您已定義的管理存取規則之間的交集。 在治理設計指南中，您已了解用來管理 Azure 資源存取權的數個不同模型。 現在我們會逐步進行所需的步驟，使用一個訂用帳戶為設計指南的每個**共用基礎結構**、**生產**和**開發**環境，實作資源管理模型。 我們針對全部三個環境有一個**訂用帳戶擁有者**。 每個工作負載會在**資源群組**中隔離，具有新增了**參與者**角色的**工作負載擁有者**。

> [!NOTE]
> 請參閱[了解 Azure 中的資源存取][understand-resource-access-in-azure]以深入了解 Azure 帳戶和訂用帳戶之間的關聯性。 

請遵循下列步驟：

1. 如果貴組織還沒有帳戶的話，請建立 [Azure 帳戶](/azure/active-directory/sign-up-organization)。 註冊 Azure 帳戶的人員會成為 Azure 帳戶管理員，而且貴組織的領導必須選取個人來承擔這個角色。 這個個人將負責：
    * 建立訂用帳戶，並且
    * 建立和管理 [Azure Active Directory (AD)](/azure/active-directory/active-directory-whatis) 租用戶，在其中儲存這些訂用帳戶的使用者身分識別。    
2. 貴組織的領導小組決定哪些人負責：
    * 使用者身分識別管理，[Azure AD 租用戶](/azure/active-directory/develop/active-directory-howto-tenant)預設會在貴組織的 Azure 帳戶建立時建立，而且帳戶管理員預設會新增為 [Azure AD 全域管理員](/azure/active-directory/active-directory-assign-admin-roles-azure-portal#details-about-the-global-administrator-role)。 貴組織可以選擇另一個使用者來管理使用者身分識別，方法是[將 Azure AD 全域管理員角色指派給該使用者](/azure/active-directory/active-directory-users-assign-role-azure-portal)。 
    * 訂用帳戶，這表示這些使用者：
        * 管理與該訂用帳戶中資源使用量相關聯的成本，
        * 實作和維護資源存取權的最小權限模型，以及
        * 持續追蹤服務限制。
    * 共用基礎結構服務 (如果貴組織決定要使用此模型)，這表示此使用者負責：
        * 內部部署至 Azure 網路連線，以及 
        * 透過虛擬網路對等互連，在 Azure 內網路連線的擁有權。
    * 工作負載擁有者。 
3. Azure AD 全域管理員會為以下人員[建立新的使用者帳戶](/azure/active-directory/add-users-azure-active-directory)：
    * 將成為與每個環境相關聯訂用帳戶**訂用帳戶擁有者**的人員。 請注意，只有當訂用帳戶**服務管理員**不負責管理每個訂用帳戶/環境的資源存取權時，才需要這麼做。
    * 將成為**網路作業使用者**的人員，以及
    * 身為**工作負載擁有者**的人員。
4. Azure 帳戶管理員會使用 [Azure 帳戶入口網站](https://account.azure.com)，建立下列三個訂用帳戶：
    * 適用於**共用基礎結構**環境的訂用帳戶，
    * 適用於**生產**環境的訂用帳戶，以及 
    * 適用於**開發**環境的訂用帳戶。 
5. Azure 帳戶管理員會[將訂用帳戶服務擁有者新增至每個訂用帳戶](/azure/billing/billing-add-change-azure-subscription-administrator#add-an-rbac-owner-admin-for-a-subscription-in-azure-portal)。
6. 建立**工作負載擁有者**的核准程序，以要求建立資源群組。 核准程序的實作方式有許多種，例如透過電子郵件，或者您可以使用如 [Sharepoint 工作流程](https://support.office.com/article/introduction-to-sharepoint-workflow-07982276-54e8-4e17-8699-5056eff4d9e3)的處理程序管理工具。 核准程序可以依照下列步驟：  
    * **工作負載擁有者**為**開發**環境、**生產**環境或兩者中的必要 Azure 資源準備用料表，並且將它提交給**訂用帳戶擁有者**。
    * **訂用帳戶擁有者**檢閱用料表並驗證要求的資源，以確保要求的資源適合其規劃使用，例如，檢查要求的[虛擬機器大小](/azure/virtual-machines/windows/sizes)正確無誤。
    * 如果要求未獲得核准，**工作負載擁有者**會收到通知。 如果要求通過核准，**訂用帳戶擁有者**會遵循貴組織的[命名慣例](/azure/architecture/best-practices/naming-conventions)來[建立要求的資源群組](/azure/azure-resource-manager/resource-group-portal#manage-resource-groups)，[新增**工作負載擁有者**](/azure/role-based-access-control/role-assignments-portal#add-access)，其具有[**參與者**角色](/azure/role-based-access-control/built-in-roles#contributor)，並且將通知傳送給已建立資源群組的**工作負載擁有者**。
7. 為工作負載擁有者建立核准程序，以要求來自共用基礎結構擁有者的虛擬網路對等互連連線。 如同上一個步驟，這個核准程序可以使用電子郵件或處理程序管理工具來實作。

既然您已實作治理模型，您可以部署共用基礎結構服務。

## <a name="section-4-deploy-shared-infrastructure-services"></a>第 4 節：部署共用基礎結構服務

貴組織可以使用數種[混合式網路參考架構](/azure/architecture/reference-architectures/hybrid-networking/)，將內部部署網路連線到 Azure。 這些參考架構皆包含需要訂用帳戶識別碼的部署。 在部署期間，針對與您的**共用基礎結構**環境相關聯的訂用帳戶，指定訂用帳戶識別碼。 您也必須編輯範本檔案，以指定由您的**網路作業**使用者管理的資源群組，或者，您可以在部署中使用預設資源群組，並新增具有**參與者**角色的**網路作業**使用者。

<!-- links -->
[understand-resource-access-in-azure]: /azure/role-based-access-control/rbac-and-directory-admin-roles
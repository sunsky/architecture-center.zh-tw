---
title: 選擇解決方案以整合內部部署 Active Directory 與 Azure。
description: 比較用於整合內部部署 Active Directory 與 Azure 的參考架構。
ms.date: 07/02/2018
ms.openlocfilehash: 7e89998c59bccf4d37cebca5ddd4ea7ecba58cd5
ms.sourcegitcommit: 776b8c1efc662d42273a33de3b82ec69e3cd80c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38987524"
---
# <a name="choose-a-solution-for-integrating-on-premises-active-directory-with-azure"></a>選擇解決方案以整合內部部署 Active Directory 與 Azure

本文會比較整合內部部署 Active Directory (AD) 環境與 Azure 網路的選項。 對於每個選項都可以使用更詳細的參考架構。

許多組織使用 Active Directory Domain Services (AD DS) 來驗證與使用者、電腦、應用程式或包含在安全性邊界內其他資源相關聯的身分識別。 目錄和身分識別服務通常裝載在內部部署，但如果您的應用程式部分裝載在內部部署且部分裝載在 Azure 中，將驗證要求從 Azure 傳回內部部署時，可能會有延遲。 在 Azure 中實作目錄和身分識別服務可以減少這種延遲。

Azure 會針對在 Azure 中實作目錄和身分識別服務，提供兩種解決方案： 

* 使用 [Azure AD][azure-active-directory] 在雲端建立 Active Directory 網域，然後將其連線至您的內部部署 Active Directory 網域。 [Azure AD Connect][azure-ad-connect] 會整合您的內部部署目錄與 Azure AD。

* 在 Azure 中部署執行 AD DS 作為網域控制站的 VM，以便將現有的內部部署 Active Directory 基礎結構擴充至 Azure 中。 當內部部署網路與 Azure 虛擬網路 (VNet) 透過 VPN 或 ExpressRoute 連線連接時，這個架構更為常見。 此架構可能會有數種變化： 

    - 在 Azure 中建立網域，並將其加入至您的內部部署 AD 樹系。
    - 在 Azure 中建立您內部部署樹系之網域所信任的另一個樹系。
    - 將 Active Directory Federation Services (AD FS) 部署複寫至 Azure。 

以下章節詳細說明其中各個選項。

## <a name="integrate-your-on-premises-domains-with-azure-ad"></a>整合內部部署網域與 Azure AD

使用 Azure Active Directory (Azure AD) 在 Azure 中建立網域，並將其連結到內部部署 AD 網域。 

Azure AD 目錄不是內部部署目錄的擴充功能。 而是包含相同物件和身分識別的複本。 在內部部署對這些項目所做的變更會複製到 Azure AD，但在 Azure AD 中所做的變更不會複寫回內部部署網域。

您也可以使用 Azure AD，而不使用內部部署目錄。 在此情況下，Azure AD 會當作所有身分識別資訊的主要來源，而不包含從內部部署目錄複寫的資料。

**優點**

* 您不需要維護雲端的 AD 基礎結構。 Azure AD 會完全由 Microsoft 所管理和維護。
* Azure AD 會提供與內部部署相同的身分識別資訊。
* 驗證可能會在 Azure 中進行，進而減少外部應用程式和使用者與內部部署網域連絡的需求。

**挑戰**

* 識別服務受到使用者和群組的限制。 無法驗證服務與電腦帳戶。
* 您必須設定與內部部署網域的連線，讓 Azure AD 目錄維持同步。 
* 應用程式可能需要重寫，才能透過 Azure AD 啟用驗證。

**參考架構**

- [整合內部部署 Active Directory 網域與 Azure Active Directory][aad]

## <a name="ad-ds-in-azure-joined-to-an-on-premises-forest"></a>Azure 中的 AD DS 會加入內部部署樹系

將 AD 網域服務 (AD DS) 伺服器部署到 Azure。 在 Azure 中建立網域，並將其加入至您的內部部署 AD 樹系。 

如果您需要使用 Azure AD 目前尚未實作的 AD DS 功能，請考慮此選項。 

**優點**

* 可存取與內部部署相同的身分識別資訊。
* 您可以在內部部署和 Azure 中驗證使用者、服務和電腦帳戶。
* 您不需要管理另一個 AD 樹系。 Azure 中的網域可以屬於內部部署樹系。
* 您可以將內部部署群組原則物件所定義的群組原則套用到 Azure 中的網域。

**挑戰**

* 您必須在雲端部署並管理您自己的 AD DS 伺服器和網域。
* 在雲端的網域伺服器與在內部部署執行的伺服器之間可能會有一些同步處理延遲。

**參考架構**

- [將 Active Directory Domain Services (AD DS) 擴充至 Azure][ad-ds]

## <a name="ad-ds-in-azure-with-a-separate-forest"></a>在 Azure 中具有不同樹系的 AD DS

將 AD Domain Services (AD DS) 伺服器部署到 Azure，但建立與內部部署樹系不同的另一個 Active Directory [樹系][ad-forest-defn]。 此樹系受到內部部署樹系中的網域信任。

這個架構的典型用途包括維持雲端中保留之物件和身分識別的安全性隔離，以及將個別網域從內部部署移轉到雲端。

**優點**

* 您可以實作內部部署身分識別，並區隔僅 Azure 的身分識別。
* 您不需要從內部部署 AD 樹系複寫至 Azure。

**挑戰**

* 在 Azure 中為內部部署身分識別進行驗證時，需要到內部部署 AD 伺服器的額外網路躍點。
* 您必須在雲端部署自己的 AD DS 伺服器和樹系，並在樹系之間建立適當的信任關係。

**參考架構**

- [在 Azure 中建立 Active Directory Domain Services (AD DS) 資源樹系][ad-ds-forest]

## <a name="extend-ad-fs-to-azure"></a>將 AD FS 擴充至 Azure

將 Active Directory Federation Services (AD FS) 部署複寫至 Azure，以便針對在 Azure 中執行的元件，執行同盟驗證和授權。 

此架構的一般用途包括：

* 驗證和授權夥伴組織的使用者。
* 可讓使用者從組織防火牆外部執行的網頁瀏覽器進行驗證。
* 可讓使用者從授權的外部裝置 (例如行動裝置) 連線。 

**優點**

* 您可以運用宣告感知應用程式。
* 能夠信任要進行驗證的外部夥伴。
* 與多種驗證通訊協定相容。

**挑戰**

* 您必須在 Azure 中部署您自己的 AD DS、AD FS 與 AD FS Web 應用程式 Proxy 伺服器。
* 此架構的設定可能相當複雜。

**參考架構**

- [將 Active Directory 同盟服務 (AD FS) 擴充至 Azure][adfs]

<!-- links -->

[aad]: ./azure-ad.md
[ad-ds]: ./adds-extend-domain.md
[ad-ds-forest]: ./adds-forest.md
[ad-forest-defn]: /windows/desktop/AD/forests
[adfs]: ./adfs.md

[azure-active-directory]: /azure/active-directory-domain-services/active-directory-ds-overview
[azure-ad-connect]: /azure/active-directory/active-directory-aadconnect

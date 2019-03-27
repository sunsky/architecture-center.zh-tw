---
title: 有關 Tailspin Surveys 應用程式
description: Tailspin Surveys 應用程式的概觀。
author: MikeWasson
ms.date: 07/21/2017
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.openlocfilehash: de2830b5c492e027c189a79e45ccc6634cab436b
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58247249"
---
# <a name="the-tailspin-scenario"></a>Tailspin 情節

[![GitHub](../_images/github.png) 程式碼範例][sample application]

Tailspin 是虛構的公司，他們正在開發名為 Surveys 的 SaaS 應用程式。 此應用程式可讓組織建立並發佈線上問卷。

* 組織可以註冊該應用程式。
* 組織註冊之後，使用者可以使用他們組織的認證登入應用程式。
* 使用者可以建立、編輯和發佈問卷。

> [!NOTE]
> 若要開始使用應用程式，請參閱[執行 Surveys 應用程式]。

## <a name="users-can-create-edit-and-view-surveys"></a>使用者可以建立、編輯和檢視問卷

驗證的使用者可以檢視他或她所建立或具有參與者權限的所有調查，以及建立新的調查。 請注意，該使用者是使用他的組織身分識別 `bob@contoso.com`登入。

![Surveys 應用程式](./images/surveys-screenshot.png)

此螢幕擷取畫面顯示 [Edit Survey] (編輯問卷) 頁面：

![編輯問卷](./images/edit-survey.png)

使用者也可以檢視相同租用戶中的其他使用者所建立的問卷。

![租用戶問卷](./images/tenant-surveys.png)

## <a name="survey-owners-can-invite-contributors"></a>調查擁有者可以邀請參與者

當使用者建立問卷時，他或她可以邀請其他人參與該問卷。 參與者可以編輯問卷，但不能刪除或發佈問卷。

![加入參與者](./images/add-contributor.png)

使用者可以從其他租用戶加入參與者，因此能夠跨租用戶共用資源。 在此螢幕擷取畫面中，Bob (`bob@contoso.com`) 正在將 Alice (`alice@fabrikam.com`) 新增為 Bob 所建立的問卷調查的參與者。

當 Alice 登入時，她會看見該問卷列在 [Surveys I can contribute to] (我可以參與的問卷) 之下。

![問卷參與者](./images/contributor.png)

請留意，Alice 是用自己的租用戶登入，而不是以 Contoso 租用戶之來賓的身分登入。 Alice 只針對該調查擁有參與者權限 &mdash; 她無法檢視來自 Contoso 租用戶的其他問卷。

## <a name="architecture"></a>架構

Surveys 應用程式由 Web 前端和 Web API 後端組成。 兩者都使用 [ASP.NET Core] 實作。

該 Web 應用程式使用 Azure Active Directory (Azure AD) 來驗證使用者。 該 Web 應用程式也會呼叫 Azure AD 來取得 Web API 的 OAuth 2 存取權杖。 取存取權杖快取在 Azure Redis Cache 中。 快取可讓多個執行個體共用相同的權杖快取 (例如，在伺服器陣列中)。

![架構](./images/architecture.png)

[**下一主題**][authentication]

<!-- links -->

[authentication]: authenticate.md

[執行 Surveys 應用程式]: ./run-the-app.md
[ASP.NET Core]: /aspnet/core
[sample application]: https://github.com/mspnp/multitenant-saas-guidance

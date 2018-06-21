---
title: 使用用戶端判斷提示從 Azure AD 取得存取權杖
description: 使用用戶端判斷提示從 Azure AD 取得存取權杖的方式。
author: MikeWasson
ms:date: 07/21/2017
pnp.series.title: Manage Identity in Multitenant Applications
pnp.series.prev: adfs
pnp.series.next: key-vault
ms.openlocfilehash: 9fe1ee2ec5a540edc41c3a310476507f8d862f0c
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
ms.locfileid: "24540276"
---
# <a name="use-client-assertion-to-get-access-tokens-from-azure-ad"></a><span data-ttu-id="b3a17-103">使用用戶端判斷提示從 Azure AD 取得存取權杖</span><span class="sxs-lookup"><span data-stu-id="b3a17-103">Use client assertion to get access tokens from Azure AD</span></span>

<span data-ttu-id="b3a17-104">[![GitHub](../_images/github.png) 範例程式碼][sample application]</span><span class="sxs-lookup"><span data-stu-id="b3a17-104">[![GitHub](../_images/github.png) Sample code][sample application]</span></span>

## <a name="background"></a><span data-ttu-id="b3a17-105">背景</span><span class="sxs-lookup"><span data-stu-id="b3a17-105">Background</span></span>
<span data-ttu-id="b3a17-106">在 OpenID Connect 中使用授權碼流程或混合式流程時，用戶端會交換授權碼以取得存取權杖。</span><span class="sxs-lookup"><span data-stu-id="b3a17-106">When using authorization code flow or hybrid flow in OpenID Connect, the client exchanges an authorization code for an access token.</span></span> <span data-ttu-id="b3a17-107">在此步驟中，用戶端必須向伺服器驗證自身。</span><span class="sxs-lookup"><span data-stu-id="b3a17-107">During this step, the client has to authenticate itself to the server.</span></span>

![用戶端密碼](./images/client-secret.png)

<span data-ttu-id="b3a17-109">驗證用戶端的一種方法是使用用戶端密碼。</span><span class="sxs-lookup"><span data-stu-id="b3a17-109">One way to authenticate the client is by using a client secret.</span></span> <span data-ttu-id="b3a17-110">這是 [Tailspin Surveys][Surveys] 應用程式的預設設定。</span><span class="sxs-lookup"><span data-stu-id="b3a17-110">That's how the [Tailspin Surveys][Surveys] application is configured by default.</span></span>

<span data-ttu-id="b3a17-111">以下是從用戶端向 IDP 提出的要求範例，要求存取權杖。</span><span class="sxs-lookup"><span data-stu-id="b3a17-111">Here is an example request from the client to the IDP, requesting an access token.</span></span> <span data-ttu-id="b3a17-112">請注意 `client_secret` 參數。</span><span class="sxs-lookup"><span data-stu-id="b3a17-112">Note the `client_secret` parameter.</span></span>

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_secret=i3Bf12Dn...
  &grant_type=authorization_code
  &code=PG8wJG6Y...
```

<span data-ttu-id="b3a17-113">密碼只是一個字串，因此您必須確保不會洩漏值。</span><span class="sxs-lookup"><span data-stu-id="b3a17-113">The secret is just a string, so you have to make sure not to leak the value.</span></span> <span data-ttu-id="b3a17-114">最佳做法是防止用戶端密碼進入原始檔控制中。</span><span class="sxs-lookup"><span data-stu-id="b3a17-114">The best practice is to keep the client secret out of source control.</span></span> <span data-ttu-id="b3a17-115">部署至 Azure 時，將密碼存放在[應用程式設定][configure-web-app]中。</span><span class="sxs-lookup"><span data-stu-id="b3a17-115">When you deploy to Azure, store the secret in an [app setting][configure-web-app].</span></span>

<span data-ttu-id="b3a17-116">不過，任何可存取 Azure 訂用帳戶的使用者，都可以檢視應用程式設定。</span><span class="sxs-lookup"><span data-stu-id="b3a17-116">However, anyone with access to the Azure subscription can view the app settings.</span></span> <span data-ttu-id="b3a17-117">此外，一定會有嘗試將密碼簽入原始檔控制 (例如簽入部署指令碼中)，透過電子郵件共用它們等等。</span><span class="sxs-lookup"><span data-stu-id="b3a17-117">Further, there is always a temptation to check secrets into source control (e.g., in deployment scripts), share them by email, and so on.</span></span>

<span data-ttu-id="b3a17-118">為了增加安全性，您可以使用[用戶端判斷提示]來取代用戶端密碼。</span><span class="sxs-lookup"><span data-stu-id="b3a17-118">For additional security, you can use [client assertion] instead of a client secret.</span></span> <span data-ttu-id="b3a17-119">透過用戶端判斷提示，用戶端使用 X.509 憑證提供來自用戶端的權杖要求。</span><span class="sxs-lookup"><span data-stu-id="b3a17-119">With client assertion, the client uses an X.509 certificate to prove the token request came from the client.</span></span> <span data-ttu-id="b3a17-120">用戶端憑證會安裝在 Web 伺服器上。</span><span class="sxs-lookup"><span data-stu-id="b3a17-120">The client certificate is installed on the web server.</span></span> <span data-ttu-id="b3a17-121">一般而言，限制憑證的存取會比確保不讓任何人不慎揭露用戶端密碼更為容易。</span><span class="sxs-lookup"><span data-stu-id="b3a17-121">Generally, it will be easier to restrict access to the certificate, than to ensure that nobody inadvertently reveals a client secret.</span></span> <span data-ttu-id="b3a17-122">如需在 Web 應用程式中設定憑證的相關資訊，請參閱 [在 Azure 網站應用程式中使用憑證][using-certs-in-websites]。</span><span class="sxs-lookup"><span data-stu-id="b3a17-122">For more information about configuring certificates in a web app, see [Using Certificates in Azure Websites Applications][using-certs-in-websites]</span></span>

<span data-ttu-id="b3a17-123">以下是使用用戶端判斷提示的權杖要求：</span><span class="sxs-lookup"><span data-stu-id="b3a17-123">Here is a token request using client assertion:</span></span>

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
  &client_assertion=eyJhbGci...
  &grant_type=authorization_code
  &code= PG8wJG6Y...
```

<span data-ttu-id="b3a17-124">請注意，不會再使用 `client_secret` 參數。</span><span class="sxs-lookup"><span data-stu-id="b3a17-124">Notice that the `client_secret` parameter is no longer used.</span></span> <span data-ttu-id="b3a17-125">相反地， `client_assertion` 參數包含使用用戶端憑證簽署的 JWT 權杖。</span><span class="sxs-lookup"><span data-stu-id="b3a17-125">Instead, the `client_assertion` parameter contains a JWT token that was signed using the client certificate.</span></span> <span data-ttu-id="b3a17-126">`client_assertion_type` 參數會指定判斷提示的類型 &mdash; 在此案例中是 JWT 權杖。</span><span class="sxs-lookup"><span data-stu-id="b3a17-126">The `client_assertion_type` parameter specifies the type of assertion &mdash; in this case, JWT token.</span></span> <span data-ttu-id="b3a17-127">伺服器會驗證 JWT 權杖。</span><span class="sxs-lookup"><span data-stu-id="b3a17-127">The server validates the JWT token.</span></span> <span data-ttu-id="b3a17-128">若 JWT 權杖無效，權杖要求會傳回錯誤。</span><span class="sxs-lookup"><span data-stu-id="b3a17-128">If the JWT token is invalid, the token request returns an error.</span></span>

> [!NOTE]
> <span data-ttu-id="b3a17-129">X.509 憑證並非唯一的用戶端判斷提示格式；我們在此將焦點放在其上，是因為它受 Azure AD 支援。</span><span class="sxs-lookup"><span data-stu-id="b3a17-129">X.509 certificates are not the only form of client assertion; we focus on it here because it is supported by Azure AD.</span></span>
> 
> 

<span data-ttu-id="b3a17-130">在執行時，Web 應用程式會從憑證存放區讀取憑證。</span><span class="sxs-lookup"><span data-stu-id="b3a17-130">At run time, the web application reads the certificate from the certificate store.</span></span> <span data-ttu-id="b3a17-131">憑證必須與 Web 應用程式安裝在相同的電腦上。</span><span class="sxs-lookup"><span data-stu-id="b3a17-131">The certificate must be installed on the same machine as the web app.</span></span>

<span data-ttu-id="b3a17-132">Surveys 應用程式包含協助程式類別，會建立您可以傳遞給 [AuthenticationContext.AcquireTokenSilentAsync](/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokensilentasync) 方法的 [ClientAssertionCertificate](/dotnet/api/microsoft.identitymodel.clients.activedirectory.clientassertioncertificate)，以從 Azure AD 取得權杖。</span><span class="sxs-lookup"><span data-stu-id="b3a17-132">The Surveys application includes a helper class that creates a [ClientAssertionCertificate](/dotnet/api/microsoft.identitymodel.clients.activedirectory.clientassertioncertificate) that you can pass to the [AuthenticationContext.AcquireTokenSilentAsync](/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokensilentasync) method to acquire a token from Azure AD.</span></span>

```csharp
public class CertificateCredentialService : ICredentialService
{
    private Lazy<Task<AdalCredential>> _credential;

    public CertificateCredentialService(IOptions<ConfigurationOptions> options)
    {
        var aadOptions = options.Value?.AzureAd;
        _credential = new Lazy<Task<AdalCredential>>(() =>
        {
            X509Certificate2 cert = CertificateUtility.FindCertificateByThumbprint(
                aadOptions.Asymmetric.StoreName,
                aadOptions.Asymmetric.StoreLocation,
                aadOptions.Asymmetric.CertificateThumbprint,
                aadOptions.Asymmetric.ValidationRequired);
            string password = null;
            var certBytes = CertificateUtility.ExportCertificateWithPrivateKey(cert, out password);
            return Task.FromResult(new AdalCredential(new ClientAssertionCertificate(aadOptions.ClientId, new X509Certificate2(certBytes, password))));
        });
    }

    public async Task<AdalCredential> GetCredentialsAsync()
    {
        return await _credential.Value;
    }
}
```

<span data-ttu-id="b3a17-133">如需在 Surveys 應用程式中設定用戶端判斷提示的詳細資訊，請參閱[使用 Azure Key Vault 保護應用程式密碼][key vault]。</span><span class="sxs-lookup"><span data-stu-id="b3a17-133">For information about setting up client assertion in the Surveys application, see [Use Azure Key Vault to protect application secrets ][key vault].</span></span>

<span data-ttu-id="b3a17-134">[**下一步**][key vault]</span><span class="sxs-lookup"><span data-stu-id="b3a17-134">[**Next**][key vault]</span></span>

<!-- Links -->
[configure-web-app]: /azure/app-service-web/web-sites-configure/
[azure-management-portal]: https://portal.azure.com
[用戶端判斷提示]: https://tools.ietf.org/html/rfc7521
[client assertion]: https://tools.ietf.org/html/rfc7521
[key vault]: key-vault.md
[Setup-KeyVault]: https://github.com/mspnp/multitenant-saas-guidance/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: tailspin.md
[using-certs-in-websites]: https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/

[sample application]: https://github.com/mspnp/multitenant-saas-guidance

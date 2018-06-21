---
title: 使用 Key Vault 來保護應用程式的機密資訊
description: 如何使用 Key Vault 服務來儲存應用程式的機密資訊
author: MikeWasson
ms:date: 07/21/2017
pnp.series.title: Manage Identity in Multitenant Applications
pnp.series.prev: client-assertion
ms.openlocfilehash: d49129a38d0413f6006095f03b817885e1ce6c92
ms.sourcegitcommit: f665226cec96ec818ca06ac6c2d83edb23c9f29c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
ms.locfileid: "31012513"
---
# <a name="use-azure-key-vault-to-protect-application-secrets"></a><span data-ttu-id="fba38-103">使用 Azure Key Vault 來保護應用程式的機密資訊</span><span class="sxs-lookup"><span data-stu-id="fba38-103">Use Azure Key Vault to protect application secrets</span></span>

<span data-ttu-id="fba38-104">[![GitHub](../_images/github.png) 程式碼範例][sample application]</span><span class="sxs-lookup"><span data-stu-id="fba38-104">[![GitHub](../_images/github.png) Sample code][sample application]</span></span>

<span data-ttu-id="fba38-105">應用程式設定經常含有機密內容因此必須受到保護，例如：</span><span class="sxs-lookup"><span data-stu-id="fba38-105">It's common to have application settings that are sensitive and must be protected, such as:</span></span>

* <span data-ttu-id="fba38-106">資料庫連接字串</span><span class="sxs-lookup"><span data-stu-id="fba38-106">Database connection strings</span></span>
* <span data-ttu-id="fba38-107">密碼</span><span class="sxs-lookup"><span data-stu-id="fba38-107">Passwords</span></span>
* <span data-ttu-id="fba38-108">密碼編譯金鑰</span><span class="sxs-lookup"><span data-stu-id="fba38-108">Cryptographic keys</span></span>

<span data-ttu-id="fba38-109">安全性的最佳做法是永遠不要將這些機密資訊儲存在原始檔控制中。</span><span class="sxs-lookup"><span data-stu-id="fba38-109">As a security best practice, you should never store these secrets in source control.</span></span> <span data-ttu-id="fba38-110">即使您的原始程式碼存放庫是私用的，這些機密資訊也很容易外洩。</span><span class="sxs-lookup"><span data-stu-id="fba38-110">It's too easy for them to leak &mdash; even if your source code repository is private.</span></span> <span data-ttu-id="fba38-111">這不只是讓這些機密資訊無法公開取得的問題。</span><span class="sxs-lookup"><span data-stu-id="fba38-111">And it's not just about keeping secrets from the general public.</span></span> <span data-ttu-id="fba38-112">在較大型的專案中，您還可以限制有哪些開發人員和操作員可以存取生產機密資訊。</span><span class="sxs-lookup"><span data-stu-id="fba38-112">On larger projects, you might want to restrict which developers and operators can access the production secrets.</span></span> <span data-ttu-id="fba38-113">(測試或開發環境的設定不同)。</span><span class="sxs-lookup"><span data-stu-id="fba38-113">(Settings for test or development environments are different.)</span></span>

<span data-ttu-id="fba38-114">較安全的選項是將這些機密資訊儲存在 [Azure Key Vault][KeyVault]。</span><span class="sxs-lookup"><span data-stu-id="fba38-114">A more secure option is to store these secrets in [Azure Key Vault][KeyVault].</span></span> <span data-ttu-id="fba38-115">金鑰保存庫是用於管理密碼編譯金鑰和其他機密資訊的雲端託管服務。</span><span class="sxs-lookup"><span data-stu-id="fba38-115">Key Vault is a cloud-hosted service for managing cryptographic keys and other secrets.</span></span> <span data-ttu-id="fba38-116">本文說明如何使用金鑰保存庫來儲存應用程式的組態設定。</span><span class="sxs-lookup"><span data-stu-id="fba38-116">This article shows how to use Key Vault to store configuration settings for your app.</span></span>

<span data-ttu-id="fba38-117">在 [Tailspin Surveys][Surveys] 應用程式中，以下設定是機密資訊：</span><span class="sxs-lookup"><span data-stu-id="fba38-117">In the [Tailspin Surveys][Surveys] application, the following settings are secret:</span></span>

* <span data-ttu-id="fba38-118">資料庫連接字串。</span><span class="sxs-lookup"><span data-stu-id="fba38-118">The database connection string.</span></span>
* <span data-ttu-id="fba38-119">Redis 連接字串。</span><span class="sxs-lookup"><span data-stu-id="fba38-119">The Redis connection string.</span></span>
* <span data-ttu-id="fba38-120">Web 應用程式的用戶端密碼。</span><span class="sxs-lookup"><span data-stu-id="fba38-120">The client secret for the web application.</span></span>

<span data-ttu-id="fba38-121">Surveys 應用程式會從下列位置載入組態設定：</span><span class="sxs-lookup"><span data-stu-id="fba38-121">The Surveys application loads configuration settings from the following places:</span></span>

* <span data-ttu-id="fba38-122">appsettings.json 檔案</span><span class="sxs-lookup"><span data-stu-id="fba38-122">The appsettings.json file</span></span>
* <span data-ttu-id="fba38-123">[使用者機密資訊存放區][user-secrets] (僅限開發環境；測試用)</span><span class="sxs-lookup"><span data-stu-id="fba38-123">The [user secrets store][user-secrets] (development environment only; for testing)</span></span>
* <span data-ttu-id="fba38-124">裝載環境 (Azure Web Apps 中的應用程式設定)</span><span class="sxs-lookup"><span data-stu-id="fba38-124">The hosting environment (app settings in Azure web apps)</span></span>
* <span data-ttu-id="fba38-125">Key Vault (啟用時)</span><span class="sxs-lookup"><span data-stu-id="fba38-125">Key Vault (when enabled)</span></span>

<span data-ttu-id="fba38-126">上述每一個位置都會覆寫前一個位置，以便儲存在金鑰保存庫中的設定會優先採用。</span><span class="sxs-lookup"><span data-stu-id="fba38-126">Each of these overrides the previous one, so any settings stored in Key Vault take precedence.</span></span>

> [!NOTE]
> <span data-ttu-id="fba38-127">依預設會停用金鑰保存庫組態提供者。</span><span class="sxs-lookup"><span data-stu-id="fba38-127">By default, the Key Vault configuration provider is disabled.</span></span> <span data-ttu-id="fba38-128">在本機執行應用程式時並不需要此項目。</span><span class="sxs-lookup"><span data-stu-id="fba38-128">It's not needed for running the application locally.</span></span> <span data-ttu-id="fba38-129">在生產部署中則會予以啟用。</span><span class="sxs-lookup"><span data-stu-id="fba38-129">You would enable it in a production deployment.</span></span>

<span data-ttu-id="fba38-130">在啟動時，應用程式會從每個已註冊的組態提供者讀取設定，並使用它們來填入強型別的選項物件。</span><span class="sxs-lookup"><span data-stu-id="fba38-130">At startup, the application reads settings from every registered configuration provider, and uses them to populate a strongly typed options object.</span></span> <span data-ttu-id="fba38-131">如需詳細資訊，請參閱[使用選項和組態物件][options]。</span><span class="sxs-lookup"><span data-stu-id="fba38-131">For more information, see [Using Options and configuration objects][options].</span></span>

## <a name="setting-up-key-vault-in-the-surveys-app"></a><span data-ttu-id="fba38-132">在 Surveys 應用程式中設定金鑰保存庫</span><span class="sxs-lookup"><span data-stu-id="fba38-132">Setting up Key Vault in the Surveys app</span></span>
<span data-ttu-id="fba38-133">必要條件：</span><span class="sxs-lookup"><span data-stu-id="fba38-133">Prerequisites:</span></span>

* <span data-ttu-id="fba38-134">安裝 [Azure Resource Manager Cmdlet][azure-rm-cmdlets]。</span><span class="sxs-lookup"><span data-stu-id="fba38-134">Install the [Azure Resource Manager Cmdlets][azure-rm-cmdlets].</span></span>
* <span data-ttu-id="fba38-135">設定 Surveys 應用程式，如[執行 Surveys 應用程式][readme]所述。</span><span class="sxs-lookup"><span data-stu-id="fba38-135">Configure the Surveys application as described in [Run the Surveys application][readme].</span></span>

<span data-ttu-id="fba38-136">高階步驟：</span><span class="sxs-lookup"><span data-stu-id="fba38-136">High-level steps:</span></span>

1. <span data-ttu-id="fba38-137">設定租用戶中的系統管理員使用者。</span><span class="sxs-lookup"><span data-stu-id="fba38-137">Set up an admin user in the tenant.</span></span>
2. <span data-ttu-id="fba38-138">設定用戶端憑證。</span><span class="sxs-lookup"><span data-stu-id="fba38-138">Set up a client certificate.</span></span>
3. <span data-ttu-id="fba38-139">建立金鑰保存庫。</span><span class="sxs-lookup"><span data-stu-id="fba38-139">Create a key vault.</span></span>
4. <span data-ttu-id="fba38-140">將組態設定新增至金鑰保存庫。</span><span class="sxs-lookup"><span data-stu-id="fba38-140">Add configuration settings to your key vault.</span></span>
5. <span data-ttu-id="fba38-141">取消註解可啟用金鑰保存庫的程式碼。</span><span class="sxs-lookup"><span data-stu-id="fba38-141">Uncomment the code that enables key vault.</span></span>
6. <span data-ttu-id="fba38-142">更新應用程式的使用者機密資訊。</span><span class="sxs-lookup"><span data-stu-id="fba38-142">Update the application's user secrets.</span></span>

### <a name="set-up-an-admin-user"></a><span data-ttu-id="fba38-143">設定系統管理員使用者</span><span class="sxs-lookup"><span data-stu-id="fba38-143">Set up an admin user</span></span>
> [!NOTE]
> <span data-ttu-id="fba38-144">若要建立金鑰保存庫，您必須使用可管理 Azure 訂用帳戶的帳戶。</span><span class="sxs-lookup"><span data-stu-id="fba38-144">To create a key vault, you must use an account which can manage your Azure subscription.</span></span> <span data-ttu-id="fba38-145">此外，任何您授權讀取金鑰保存庫的應用程式必須在和該帳戶相同的租用戶中註冊。</span><span class="sxs-lookup"><span data-stu-id="fba38-145">Also, any application that you authorize to read from the key vault must be registered in the same tenant as that account.</span></span>
> 
> 

<span data-ttu-id="fba38-146">在此步驟中，您將確保在登入為 Surveys 應用程式註冊所在之租用戶的使用者時，您可以建立金鑰保存庫。</span><span class="sxs-lookup"><span data-stu-id="fba38-146">In this step, you will make sure that you can create a key vault while signed in as a user from the tenant where the Surveys app is registered.</span></span>

<span data-ttu-id="fba38-147">在 Surveys 應用程式註冊所在的 Azure AD 租用戶中建立系統管理員使用者。</span><span class="sxs-lookup"><span data-stu-id="fba38-147">Create an administrator user within the Azure AD tenant where the Surveys application is registered.</span></span>

1. <span data-ttu-id="fba38-148">登入 [Azure 入口網站][azure-portal]。</span><span class="sxs-lookup"><span data-stu-id="fba38-148">Log into the [Azure portal][azure-portal].</span></span>
2. <span data-ttu-id="fba38-149">選取應用程式註冊所在的 Azure AD 租用戶。</span><span class="sxs-lookup"><span data-stu-id="fba38-149">Select the Azure AD tenant where your application is registered.</span></span>
3. <span data-ttu-id="fba38-150">按一下 [更多服務] > [安全性 + 身分識別] > [Azure Active Directory] > [使用者和群組] > [所有使用者]。</span><span class="sxs-lookup"><span data-stu-id="fba38-150">Click **More service** > **SECURITY + IDENTITY** > **Azure Active Directory** > **User and groups** > **All users**.</span></span>
4. <span data-ttu-id="fba38-151">按一下入口網站頂端的 [新增使用者]。</span><span class="sxs-lookup"><span data-stu-id="fba38-151">At the top of the portal, click **New user**.</span></span>
5. <span data-ttu-id="fba38-152">填寫欄位，然後將使用者指派至 [全域管理員] 目錄角色。</span><span class="sxs-lookup"><span data-stu-id="fba38-152">Fill in the fields and assign the user to the **Global administrator** directory role.</span></span>
6. <span data-ttu-id="fba38-153">按一下頁面底部的 [新增] 。</span><span class="sxs-lookup"><span data-stu-id="fba38-153">Click **Create**.</span></span>

![全域管理員使用者](./images/running-the-app/global-admin-user.png)

<span data-ttu-id="fba38-155">現在將此使用者指派為訂用帳戶擁有者。</span><span class="sxs-lookup"><span data-stu-id="fba38-155">Now assign this user as the subscription owner.</span></span>

1. <span data-ttu-id="fba38-156">在 [中樞] 功能表中，選取 [訂用帳戶]。</span><span class="sxs-lookup"><span data-stu-id="fba38-156">On the Hub menu, select **Subscriptions**.</span></span>

    ![](./images/running-the-app/subscriptions.png)

2. <span data-ttu-id="fba38-157">選取您想讓系統管理員存取的訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="fba38-157">Select the subscription that you want the administrator to access.</span></span>
3. <span data-ttu-id="fba38-158">在 [訂用帳戶] 刀鋒視窗中，選取 [存取控制 (IAM)]。</span><span class="sxs-lookup"><span data-stu-id="fba38-158">In the subscription blade, select **Access control (IAM)**.</span></span>
4. <span data-ttu-id="fba38-159">按一下 [新增] 。</span><span class="sxs-lookup"><span data-stu-id="fba38-159">Click **Add**.</span></span>
4. <span data-ttu-id="fba38-160">選取 [角色] 底下的 [擁有者]。</span><span class="sxs-lookup"><span data-stu-id="fba38-160">Under **Role**, select **Owner**.</span></span>
5. <span data-ttu-id="fba38-161">輸入要新增為擁有者之使用者的電子郵件地址。</span><span class="sxs-lookup"><span data-stu-id="fba38-161">Type the email address of the user you want to add as owner.</span></span>
6. <span data-ttu-id="fba38-162">選取使用者，然後按一下 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="fba38-162">Select the user and click **Save**.</span></span>

### <a name="set-up-a-client-certificate"></a><span data-ttu-id="fba38-163">設定用戶端憑證</span><span class="sxs-lookup"><span data-stu-id="fba38-163">Set up a client certificate</span></span>
1. <span data-ttu-id="fba38-164">執行 PowerShell 指令碼 [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault]，如下所示：</span><span class="sxs-lookup"><span data-stu-id="fba38-164">Run the PowerShell script [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] as follows:</span></span>
   
    ```
    .\Setup-KeyVault.ps1 -Subject <<subject>>
    ```
    <span data-ttu-id="fba38-165">針對 `Subject` 參數，輸入任何名稱，如「surveysapp」。</span><span class="sxs-lookup"><span data-stu-id="fba38-165">For the `Subject` parameter, enter any name, such as "surveysapp".</span></span> <span data-ttu-id="fba38-166">指令碼會產生自我簽署憑證，並將它儲存在「目前使用者/個人」憑證存放區。</span><span class="sxs-lookup"><span data-stu-id="fba38-166">The script generates a self-signed certificate and stores it in the "Current User/Personal" certificate store.</span></span> <span data-ttu-id="fba38-167">指令碼的輸出是 JSON 片段。</span><span class="sxs-lookup"><span data-stu-id="fba38-167">The output from the script is a JSON fragment.</span></span> <span data-ttu-id="fba38-168">複製這個值。</span><span class="sxs-lookup"><span data-stu-id="fba38-168">Copy this value.</span></span>

2. <span data-ttu-id="fba38-169">在 [Azure 入口網站][azure-portal]中，於入口網站右上角選取您的帳戶，以切換至 Surveys 應用程式註冊所在的目錄。</span><span class="sxs-lookup"><span data-stu-id="fba38-169">In the [Azure portal][azure-portal], switch to the directory where the Surveys application is registered, by selecting your account in the top right corner of the portal.</span></span>

3. <span data-ttu-id="fba38-170">選取 [Azure Active Directory] > [應用程式註冊] > Surveys</span><span class="sxs-lookup"><span data-stu-id="fba38-170">Select **Azure Active Directory** > **App Registrations** > Surveys</span></span>

4.  <span data-ttu-id="fba38-171">依序按一下 [資訊清單] 和 [編輯]。</span><span class="sxs-lookup"><span data-stu-id="fba38-171">Click **Manifest** and then **Edit**.</span></span>

5.  <span data-ttu-id="fba38-172">將指令碼的輸出貼入至 `keyCredentials` 屬性。</span><span class="sxs-lookup"><span data-stu-id="fba38-172">Paste the output from the script into the `keyCredentials` property.</span></span> <span data-ttu-id="fba38-173">看起來應該會像下面這樣：</span><span class="sxs-lookup"><span data-stu-id="fba38-173">It should look similar to the following:</span></span>
        
    ```json
    "keyCredentials": [
        {
        "type": "AsymmetricX509Cert",
        "usage": "Verify",
        "keyId": "29d4f7db-0539-455e-b708-....",
        "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
        "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
        }
    ],
    ```          

6. <span data-ttu-id="fba38-174">按一下 [檔案] 。</span><span class="sxs-lookup"><span data-stu-id="fba38-174">Click **Save**.</span></span>  

7. <span data-ttu-id="fba38-175">重複步驟 3-6，將相同的 JSON 片段新增至 Web API (Surveys.WebAPI) 的應用程式資訊清單。</span><span class="sxs-lookup"><span data-stu-id="fba38-175">Repeat steps 3-6 to add the same JSON fragment to the application manifest of the web API (Surveys.WebAPI).</span></span>

8. <span data-ttu-id="fba38-176">從 PowerShell 視窗中執行下列命令，以取得憑證的指紋。</span><span class="sxs-lookup"><span data-stu-id="fba38-176">From the PowerShell window, run the following command to get the thumbprint of the certificate.</span></span>
   
    ```
    certutil -store -user my [subject]
    ```
    
    <span data-ttu-id="fba38-177">針對 `[subject]`，請使用您在 PowerShell 指令碼中為 Subject 所指定的值。</span><span class="sxs-lookup"><span data-stu-id="fba38-177">For `[subject]`, use the value that you specified for Subject in the PowerShell script.</span></span> <span data-ttu-id="fba38-178">指紋會列在「Cert Hash(sha1)」之下。</span><span class="sxs-lookup"><span data-stu-id="fba38-178">The thumbprint is listed under "Cert Hash(sha1)".</span></span> <span data-ttu-id="fba38-179">複製這個值。</span><span class="sxs-lookup"><span data-stu-id="fba38-179">Copy this value.</span></span> <span data-ttu-id="fba38-180">您稍後會使用指紋。</span><span class="sxs-lookup"><span data-stu-id="fba38-180">You will use the thumbprint later.</span></span>

### <a name="create-a-key-vault"></a><span data-ttu-id="fba38-181">建立金鑰保存庫</span><span class="sxs-lookup"><span data-stu-id="fba38-181">Create a key vault</span></span>
1. <span data-ttu-id="fba38-182">執行 PowerShell 指令碼 [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault]，如下所示：</span><span class="sxs-lookup"><span data-stu-id="fba38-182">Run the PowerShell script [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] as follows:</span></span>
   
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ResourceGroupName <<resource group name>> -Location <<location>>
    ```
   
    <span data-ttu-id="fba38-183">當系統提示您輸入認證時，以您稍早建立的 Azure AD 使用者身分登入。</span><span class="sxs-lookup"><span data-stu-id="fba38-183">When prompted for credentials, sign in as the Azure AD user that you created earlier.</span></span> <span data-ttu-id="fba38-184">指令碼會建立新的資源群組，而該資源群組內會有新的金鑰保存庫。</span><span class="sxs-lookup"><span data-stu-id="fba38-184">The script creates a new resource group, and a new key vault within that resource group.</span></span> 
   
2. <span data-ttu-id="fba38-185">再次執行 SetupKeyVault.ps，如下所示：</span><span class="sxs-lookup"><span data-stu-id="fba38-185">Run SetupKeyVault.ps again as follows:</span></span>
   
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ApplicationIds @("<<Surveys app id>>", "<<Surveys.WebAPI app ID>>")
    ```
   
    <span data-ttu-id="fba38-186">設定下列參數值：</span><span class="sxs-lookup"><span data-stu-id="fba38-186">Set the following parameter values:</span></span>
   
       * <span data-ttu-id="fba38-187">金鑰保存庫名稱 = 您在上一個步驟中提供給金鑰保存庫的名稱。</span><span class="sxs-lookup"><span data-stu-id="fba38-187">key vault name = The name that you gave the key vault in the previous step.</span></span>
       * <span data-ttu-id="fba38-188">Surveys 應用程式識別碼 = Surveys Web 應用程式的應用程式識別碼。</span><span class="sxs-lookup"><span data-stu-id="fba38-188">Surveys app ID = The application ID for the Surveys web application.</span></span>
       * <span data-ttu-id="fba38-189">Surveys.WebApi 應用程式識別碼 = Surveys.WebAPI 應用程式的應用程式識別碼。</span><span class="sxs-lookup"><span data-stu-id="fba38-189">Surveys.WebApi app ID = The application ID for the Surveys.WebAPI application.</span></span>
         
    <span data-ttu-id="fba38-190">範例：</span><span class="sxs-lookup"><span data-stu-id="fba38-190">Example:</span></span>
     
    ```
     .\Setup-KeyVault.ps1 -KeyVaultName tailspinkv -ApplicationIds @("f84df9d1-91cc-4603-b662-302db51f1031", "8871a4c2-2a23-4650-8b46-0625ff3928a6")
    ```
    
    <span data-ttu-id="fba38-191">此指令碼會授權 Web 應用程式和 Web API 擷取金鑰保存庫中的機密資訊。</span><span class="sxs-lookup"><span data-stu-id="fba38-191">This script authorizes the web app and web API to retrieve secrets from your key vault.</span></span> <span data-ttu-id="fba38-192">如需詳細資訊，請參閱[開始使用 Azure Key Vault](/azure/key-vault/key-vault-get-started/)。</span><span class="sxs-lookup"><span data-stu-id="fba38-192">See [Get started with Azure Key Vault](/azure/key-vault/key-vault-get-started/) for more information.</span></span>

### <a name="add-configuration-settings-to-your-key-vault"></a><span data-ttu-id="fba38-193">將組態設定新增至金鑰保存庫</span><span class="sxs-lookup"><span data-stu-id="fba38-193">Add configuration settings to your key vault</span></span>
1. <span data-ttu-id="fba38-194">執行 SetupKeyVault.ps，如下所示：</span><span class="sxs-lookup"><span data-stu-id="fba38-194">Run SetupKeyVault.ps as follows::</span></span>
   
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName Redis--Configuration -KeyValue "<<Redis DNS name>>.redis.cache.windows.net,password=<<Redis access key>>,ssl=true" 
    ```
    <span data-ttu-id="fba38-195">其中</span><span class="sxs-lookup"><span data-stu-id="fba38-195">where</span></span>
   
   * <span data-ttu-id="fba38-196">金鑰保存庫名稱 = 您在上一個步驟中提供給金鑰保存庫的名稱。</span><span class="sxs-lookup"><span data-stu-id="fba38-196">key vault name = The name that you gave the key vault in the previous step.</span></span>
   * <span data-ttu-id="fba38-197">Redis DNS 名稱 = Redis 快取執行個體的 DNS 名稱。</span><span class="sxs-lookup"><span data-stu-id="fba38-197">Redis DNS name = The DNS name of your Redis cache instance.</span></span>
   * <span data-ttu-id="fba38-198">Redis 存取金鑰 = Redis 快取執行個體的存取金鑰。</span><span class="sxs-lookup"><span data-stu-id="fba38-198">Redis access key = The access key for your Redis cache instance.</span></span>
     
2. <span data-ttu-id="fba38-199">這個階段很適合測試是否已成功將密碼儲存到金鑰保存庫。</span><span class="sxs-lookup"><span data-stu-id="fba38-199">At this point, it's a good idea to test whether you successfully stored the secrets to key vault.</span></span> <span data-ttu-id="fba38-200">執行下列 PowerShell 命令：</span><span class="sxs-lookup"><span data-stu-id="fba38-200">Run the following PowerShell command:</span></span>
   
    ```
    Get-AzureKeyVaultSecret <<key vault name>> Redis--Configuration | Select-Object *
    ```

3. <span data-ttu-id="fba38-201">再次執行 SetupKeyVault.ps 以新增資料庫連接字串：</span><span class="sxs-lookup"><span data-stu-id="fba38-201">Run SetupKeyVault.ps again to add the database connection string:</span></span>
   
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName Data--SurveysConnectionString -KeyValue <<DB connection string>> -ConfigName "Data:SurveysConnectionString"
    ```
   
    <span data-ttu-id="fba38-202">其中 `<<DB connection string>>` 是資料庫連接字串的值。</span><span class="sxs-lookup"><span data-stu-id="fba38-202">where `<<DB connection string>>` is the value of the database connection string.</span></span>
   
    <span data-ttu-id="fba38-203">若要使用本機資料庫進行測試，請複製 Tailspin.Surveys.Web/appsettings.json 檔案中的連接字串。</span><span class="sxs-lookup"><span data-stu-id="fba38-203">For testing with the local database, copy the connection string from the Tailspin.Surveys.Web/appsettings.json file.</span></span> <span data-ttu-id="fba38-204">如果您這樣做，請務必將雙反斜線 (\\\\) 變更為單一反斜線。</span><span class="sxs-lookup"><span data-stu-id="fba38-204">If you do that, make sure to change the double backslash ('\\\\') into a single backslash.</span></span> <span data-ttu-id="fba38-205">雙反斜線是 JSON 檔案中的逸出字元。</span><span class="sxs-lookup"><span data-stu-id="fba38-205">The double backslash is an escape character in the JSON file.</span></span>
   
    <span data-ttu-id="fba38-206">範例：</span><span class="sxs-lookup"><span data-stu-id="fba38-206">Example:</span></span>
   
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName mykeyvault -KeyName Data--SurveysConnectionString -KeyValue "Server=(localdb)\MSSQLLocalDB;Database=Tailspin.SurveysDB;Trusted_Connection=True;MultipleActiveResultSets=true" 
    ```

### <a name="uncomment-the-code-that-enables-key-vault"></a><span data-ttu-id="fba38-207">取消註解可啟用金鑰保存庫的程式碼</span><span class="sxs-lookup"><span data-stu-id="fba38-207">Uncomment the code that enables Key Vault</span></span>
1. <span data-ttu-id="fba38-208">開啟 Tailspin.Surveys 方案。</span><span class="sxs-lookup"><span data-stu-id="fba38-208">Open the Tailspin.Surveys solution.</span></span>
2. <span data-ttu-id="fba38-209">在 Tailspin.Surveys.Web/Startup.cs 中，找出以下程式碼區塊並將它取消註解。</span><span class="sxs-lookup"><span data-stu-id="fba38-209">In Tailspin.Surveys.Web/Startup.cs, locate the following code block and uncomment it.</span></span>
   
    ```csharp
    //var config = builder.Build();
    //builder.AddAzureKeyVault(
    //    $"https://{config["KeyVault:Name"]}.vault.azure.net/",
    //    config["AzureAd:ClientId"],
    //    config["AzureAd:ClientSecret"]);
    ```
3. <span data-ttu-id="fba38-210">在 Tailspin.Surveys.Web/Startup.cs 中，找出註冊 `ICredentialService` 的程式碼。</span><span class="sxs-lookup"><span data-stu-id="fba38-210">In Tailspin.Surveys.Web/Startup.cs, locate the code that registers the `ICredentialService`.</span></span> <span data-ttu-id="fba38-211">將使用 `CertificateCredentialService` 的程式行取消註解，然後將使用 `ClientCredentialService` 的程式行註解化：</span><span class="sxs-lookup"><span data-stu-id="fba38-211">Uncomment the line that uses `CertificateCredentialService`, and comment out the line that uses `ClientCredentialService`:</span></span>
   
    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```
   
    <span data-ttu-id="fba38-212">這項變更可讓 Web 應用程式使用[用戶端判斷提示][client-assertion]來取得 OAuth 存取權杖。</span><span class="sxs-lookup"><span data-stu-id="fba38-212">This change enables the web app to use [Client assertion][client-assertion] to get OAuth access tokens.</span></span> <span data-ttu-id="fba38-213">使用用戶端判斷提示就不需要 OAuth 用戶端密碼。</span><span class="sxs-lookup"><span data-stu-id="fba38-213">With client assertion, you don't need an OAuth client secret.</span></span> <span data-ttu-id="fba38-214">或者，您可以將用戶端密碼儲存在金鑰保存庫中。</span><span class="sxs-lookup"><span data-stu-id="fba38-214">Alternatively, you could store the client secret in key vault.</span></span> <span data-ttu-id="fba38-215">不過，金鑰保存庫和用戶端判斷提示都使用用戶端憑證，因此如果您啟用金鑰保存庫，則最好也啟用用戶端判斷提示。</span><span class="sxs-lookup"><span data-stu-id="fba38-215">However, key vault and client assertion both use a client certificate, so if you enable key vault, it's a good practice to enable client assertion as well.</span></span>

### <a name="update-the-user-secrets"></a><span data-ttu-id="fba38-216">更新使用者密碼</span><span class="sxs-lookup"><span data-stu-id="fba38-216">Update the user secrets</span></span>
<span data-ttu-id="fba38-217">在 [方案總管] 中，以右鍵按一下 Tailspin.Surveys.Web 專案，然後選取 [管理使用者密碼] 。</span><span class="sxs-lookup"><span data-stu-id="fba38-217">In Solution Explorer, right-click the Tailspin.Surveys.Web project and select **Manage User Secrets**.</span></span> <span data-ttu-id="fba38-218">在 secrets.json 檔案中，刪除現有 JSON 並貼上下列內容：</span><span class="sxs-lookup"><span data-stu-id="fba38-218">In the secrets.json file, delete the existing JSON and paste in the following:</span></span>

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys web app client ID]",
        "ClientSecret": "[Surveys web app client secret]",
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Surveys.WebAPI application]",
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint. Example: 105b2ff3bc842c53582661716db1b7cdc6b43ec9]",
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "KeyVault": {
        "Name": "[key vault name]"
      }
    }
    ```

<span data-ttu-id="fba38-219">請將 [方括號] 中的項目更換為正確值。</span><span class="sxs-lookup"><span data-stu-id="fba38-219">Replace the entries in [square brackets] with the correct values.</span></span>

* <span data-ttu-id="fba38-220">`AzureAd:ClientId`：Surveys 應用程式的用戶端識別碼。</span><span class="sxs-lookup"><span data-stu-id="fba38-220">`AzureAd:ClientId`: The client ID of the Surveys app.</span></span>
* <span data-ttu-id="fba38-221">`AzureAd:ClientSecret`：在 Azure AD 中註冊問卷應用程式時產生的金鑰。</span><span class="sxs-lookup"><span data-stu-id="fba38-221">`AzureAd:ClientSecret`: The key that you generated when you registered the Surveys application in Azure AD.</span></span>
* <span data-ttu-id="fba38-222">`AzureAd:WebApiResourceId`：當您在 Azure AD 建立 Surveys.WebAPI 應用程式時所指定的應用程式識別碼 URI。</span><span class="sxs-lookup"><span data-stu-id="fba38-222">`AzureAd:WebApiResourceId`: The App ID URI that you specified when you created the Surveys.WebAPI application in Azure AD.</span></span>
* <span data-ttu-id="fba38-223">`Asymmetric:CertificateThumbprint`：您先前在建立用戶端憑證時所取得的憑證指紋。</span><span class="sxs-lookup"><span data-stu-id="fba38-223">`Asymmetric:CertificateThumbprint`: The certificate thumbprint that you got previously, when you created the client certificate.</span></span>
* <span data-ttu-id="fba38-224">`KeyVault:Name`：金鑰保存庫的名稱。</span><span class="sxs-lookup"><span data-stu-id="fba38-224">`KeyVault:Name`: The name of your key vault.</span></span>

> [!NOTE]
> <span data-ttu-id="fba38-225">`Asymmetric:ValidationRequired` 為 false，因為您先前建立的憑證並未由根憑證授權單位 (CA) 簽署。</span><span class="sxs-lookup"><span data-stu-id="fba38-225">`Asymmetric:ValidationRequired` is false because the certificate that you created previously was not signed by a root certificate authority (CA).</span></span> <span data-ttu-id="fba38-226">在生產環境中，使用由根 CA 簽署的憑證，並將 `ValidationRequired` 設為 true。</span><span class="sxs-lookup"><span data-stu-id="fba38-226">In production, use a certificate that is signed by a root CA and set `ValidationRequired` to true.</span></span>
> 
> 

<span data-ttu-id="fba38-227">儲存更新後的 secrets.json 檔案。</span><span class="sxs-lookup"><span data-stu-id="fba38-227">Save the updated secrets.json file.</span></span>

<span data-ttu-id="fba38-228">接下來，在 [方案總管] 中以滑鼠右鍵按一下 Tailspin.Surveys.WebApi 專案，然後選取 [管理使用者密碼] 。</span><span class="sxs-lookup"><span data-stu-id="fba38-228">Next, in Solution Explorer, right-click the Tailspin.Surveys.WebApi project and select **Manage User Secrets**.</span></span> <span data-ttu-id="fba38-229">刪除現有 JSON 並貼上下列內容：</span><span class="sxs-lookup"><span data-stu-id="fba38-229">Delete the existing JSON and paste in the following:</span></span>

```
{
  "AzureAd": {
    "ClientId": "[Surveys.WebAPI client ID]",
    "WebApiResourceId": "https://tailspin5.onmicrosoft.com/surveys.webapi",
    "Asymmetric": {
      "CertificateThumbprint": "[certificate thumbprint]",
      "StoreName": "My",
      "StoreLocation": "CurrentUser",
      "ValidationRequired": "false"
    }
  },
  "KeyVault": {
    "Name": "[key vault name]"
  }
}
```

<span data-ttu-id="fba38-230">更換 [方括號] 中的項目，並儲存 secrets.json 檔案。</span><span class="sxs-lookup"><span data-stu-id="fba38-230">Replace the entries in [square brackets] and save the secrets.json file.</span></span>

> [!NOTE]
> <span data-ttu-id="fba38-231">若為 Web API，請務必使用 Surveys.WebAPI 應用程式 (而非 Surveys 應用程式) 的用戶端識別碼。</span><span class="sxs-lookup"><span data-stu-id="fba38-231">For the web API, make sure to use the client ID for the Surveys.WebAPI application, not the Surveys application.</span></span>
> 
> 

<span data-ttu-id="fba38-232">[**下一主題**][adfs]</span><span class="sxs-lookup"><span data-stu-id="fba38-232">[**Next**][adfs]</span></span>

<!-- Links -->
[adfs]: ./adfs.md
[authorize-app]: /azure/key-vault/key-vault-get-started//#authorize
[azure-portal]: https://portal.azure.com
[azure-rm-cmdlets]: https://msdn.microsoft.com/library/mt125356.aspx
[client-assertion]: client-assertion.md
[configuration]: /aspnet/core/fundamentals/configuration
[KeyVault]: https://azure.microsoft.com/services/key-vault/
[key-tags]: https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_Keytags
[Microsoft.Azure.KeyVault]: https://www.nuget.org/packages/Microsoft.Azure.KeyVault/
[options]: /aspnet/core/fundamentals/configuration#using-options-and-configuration-objects
[readme]: ./run-the-app.md
[Setup-KeyVault]: https://github.com/mspnp/multitenant-saas-guidance/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: tailspin.md
[user-secrets]: http://go.microsoft.com/fwlink/?LinkID=532709
[sample application]: https://github.com/mspnp/multitenant-saas-guidance

---
title: 靜態內容裝載模式
titleSuffix: Cloud Design Patterns
description: 將靜態內容部署到可以直接將其交付給用戶端的雲端儲存體服務。
keywords: 設計模式
author: dragon119
ms.date: 01/04/2019
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 719f0221ecc8d52267cba3136eec20dadef30b99
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58249303"
---
# <a name="static-content-hosting-pattern"></a><span data-ttu-id="539a1-104">靜態內容裝載模式</span><span class="sxs-lookup"><span data-stu-id="539a1-104">Static Content Hosting pattern</span></span>

<span data-ttu-id="539a1-105">將靜態內容部署到可以直接將其交付給用戶端的雲端儲存體服務。</span><span class="sxs-lookup"><span data-stu-id="539a1-105">Deploy static content to a cloud-based storage service that can deliver them directly to the client.</span></span> <span data-ttu-id="539a1-106">這可以降低對潛在昂貴之計算執行個體的需求。</span><span class="sxs-lookup"><span data-stu-id="539a1-106">This can reduce the need for potentially expensive compute instances.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="539a1-107">內容和問題</span><span class="sxs-lookup"><span data-stu-id="539a1-107">Context and problem</span></span>

<span data-ttu-id="539a1-108">Web 應用程式通常包含靜態內容的某些元素。</span><span class="sxs-lookup"><span data-stu-id="539a1-108">Web applications typically include some elements of static content.</span></span> <span data-ttu-id="539a1-109">這個靜態內容可能包括 HTML 頁面及其他資源，例如用戶端可用的影像和文件，無論是作為 HTML 網頁的一部分 (如內嵌影像、樣式表和用戶端 JavaScript 檔案)，或是作為個別的下載項目 (例如 PDF 文件)。</span><span class="sxs-lookup"><span data-stu-id="539a1-109">This static content might include HTML pages and other resources such as images and documents that are available to the client, either as part of an HTML page (such as inline images, style sheets, and client-side JavaScript files) or as separate downloads (such as PDF documents).</span></span>

<span data-ttu-id="539a1-110">雖然已針對動態轉譯和輸出快取而對 Web 伺服器進行最佳化，但 Web 伺服器仍然需要處理下載靜態內容的要求。</span><span class="sxs-lookup"><span data-stu-id="539a1-110">Although web servers are optimized for dynamic rendering and output caching, they still have to handle requests to download static content.</span></span> <span data-ttu-id="539a1-111">這會取用通常可以更加適當使用的處理週期。</span><span class="sxs-lookup"><span data-stu-id="539a1-111">This consumes processing cycles that could often be put to better use.</span></span>

## <a name="solution"></a><span data-ttu-id="539a1-112">解決方法</span><span class="sxs-lookup"><span data-stu-id="539a1-112">Solution</span></span>

<span data-ttu-id="539a1-113">在大部分的雲端裝載環境中，您可以將一些應用程式的資源和靜態網頁放在儲存體服務中。</span><span class="sxs-lookup"><span data-stu-id="539a1-113">In most cloud hosting environments, you can put some of an application's resources and static pages in a storage service.</span></span> <span data-ttu-id="539a1-114">儲存體服務可處理這些資源的要求，從而讓負責處理其他 Web 要求的計算資源減少負載。</span><span class="sxs-lookup"><span data-stu-id="539a1-114">The storage service can serve requests for these resources, reducing load on the compute resources that handle other web requests.</span></span> <span data-ttu-id="539a1-115">雲端裝載儲存體的成本通常遠低於計算執行個體的成本。</span><span class="sxs-lookup"><span data-stu-id="539a1-115">The cost for cloud-hosted storage is typically much less than for compute instances.</span></span>

<span data-ttu-id="539a1-116">在儲存體服務中裝載應用程式的某些部分時，主要考量因素與應用程式部署以及保護不打算提供給匿名使用者使用的資源有關。</span><span class="sxs-lookup"><span data-stu-id="539a1-116">When hosting some parts of an application in a storage service, the main considerations are related to deployment of the application and to securing resources that aren't intended to be available to anonymous users.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="539a1-117">問題和考量</span><span class="sxs-lookup"><span data-stu-id="539a1-117">Issues and considerations</span></span>

<span data-ttu-id="539a1-118">當您決定如何實作此模式時，請考慮下列幾點：</span><span class="sxs-lookup"><span data-stu-id="539a1-118">Consider the following points when deciding how to implement this pattern:</span></span>

- <span data-ttu-id="539a1-119">裝載的儲存體服務必須公開使用者可以存取，以便下載靜態資源的 HTTP 端點。</span><span class="sxs-lookup"><span data-stu-id="539a1-119">The hosted storage service must expose an HTTP endpoint that users can access to download the static resources.</span></span> <span data-ttu-id="539a1-120">某些儲存體服務也支援 HTTPS，因此可以將資源裝載在需要 SSL 的儲存體服務中。</span><span class="sxs-lookup"><span data-stu-id="539a1-120">Some storage services also support HTTPS, so it's possible to host resources in storage services that require SSL.</span></span>

- <span data-ttu-id="539a1-121">為了獲得最大效能和可用性，請考慮使用內容傳遞網路 (CDN) 快取全球各地多個資料中心中之儲存體容器的內容。</span><span class="sxs-lookup"><span data-stu-id="539a1-121">For maximum performance and availability, consider using a content delivery network (CDN) to cache the contents of the storage container in multiple datacenters around the world.</span></span> <span data-ttu-id="539a1-122">但是，您可能需要支付使用 CDN 的費用。</span><span class="sxs-lookup"><span data-stu-id="539a1-122">However, you'll likely have to pay for using the CDN.</span></span>

- <span data-ttu-id="539a1-123">儲存體帳戶依預設通常會進行異地複寫，針對可能會影響資料中心的事件提供復原功能。</span><span class="sxs-lookup"><span data-stu-id="539a1-123">Storage accounts are often geo-replicated by default to provide resiliency against events that might affect a datacenter.</span></span> <span data-ttu-id="539a1-124">這表示 IP 位址可能會變更，但 URL 將會維持不變。</span><span class="sxs-lookup"><span data-stu-id="539a1-124">This means that the IP address might change, but the URL will remain the same.</span></span>

- <span data-ttu-id="539a1-125">當某些內容位於儲存體帳戶中，而其他內容位於裝載的計算執行個體中時，部署和更新應用程式會變得更麻煩。</span><span class="sxs-lookup"><span data-stu-id="539a1-125">When some content is located in a storage account and other content is in a hosted compute instance, it becomes more challenging to deploy and update the application.</span></span> <span data-ttu-id="539a1-126">您可能必須執行個別的部署，並對應用程式和內容進行版本化，以便更輕鬆地管理&mdash;特別是當靜態內容包含指令碼檔案或 UI 元件時。</span><span class="sxs-lookup"><span data-stu-id="539a1-126">You might have to perform separate deployments, and version the application and content to manage it more easily&mdash;especially when the static content includes script files or UI components.</span></span> <span data-ttu-id="539a1-127">但是，如果只需要更新靜態資源，則只需將其上傳至儲存體帳戶，而不需要重新部署應用程式套件。</span><span class="sxs-lookup"><span data-stu-id="539a1-127">However, if only static resources have to be updated, they can simply be uploaded to the storage account without needing to redeploy the application package.</span></span>

- <span data-ttu-id="539a1-128">儲存體服務可能不支援使用自訂網域名稱。</span><span class="sxs-lookup"><span data-stu-id="539a1-128">Storage services might not support the use of custom domain names.</span></span> <span data-ttu-id="539a1-129">在此情況下，就必須在連結中指定資源的完整 URL，因為它們將位於與含有連結之動態產生內容不同的網域中。</span><span class="sxs-lookup"><span data-stu-id="539a1-129">In this case it's necessary to specify the full URL of the resources in links because they'll be in a different domain from the dynamically-generated content containing the links.</span></span>

- <span data-ttu-id="539a1-130">儲存體容器必須設定公用讀取權限，但一定要確保它們未設定公用寫入權限，以避免使用者上傳內容。</span><span class="sxs-lookup"><span data-stu-id="539a1-130">The storage containers must be configured for public read access, but it's vital to ensure that they aren't configured for public write access to prevent users being able to upload content.</span></span>

- <span data-ttu-id="539a1-131">請考慮使用限時金鑰或權杖，來針對不應匿名使用的資源控制其存取權。</span><span class="sxs-lookup"><span data-stu-id="539a1-131">Consider using a valet key or token to control access to resources that shouldn't be available anonymously.</span></span> <span data-ttu-id="539a1-132">如需詳細資訊，請參閱[限時金鑰模式](./valet-key.md)。</span><span class="sxs-lookup"><span data-stu-id="539a1-132">See the [Valet Key pattern](./valet-key.md) for more information.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="539a1-133">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="539a1-133">When to use this pattern</span></span>

<span data-ttu-id="539a1-134">這種模式在下列情況中非常有用：</span><span class="sxs-lookup"><span data-stu-id="539a1-134">This pattern is useful for:</span></span>

- <span data-ttu-id="539a1-135">將包含一些靜態資源之網站和應用程式的託管成本降至最低。</span><span class="sxs-lookup"><span data-stu-id="539a1-135">Minimizing the hosting cost for websites and applications that contain some static resources.</span></span>

- <span data-ttu-id="539a1-136">將僅由靜態內容和資源組成之網站的託管成本降至最低。</span><span class="sxs-lookup"><span data-stu-id="539a1-136">Minimizing the hosting cost for websites that consist of only static content and resources.</span></span> <span data-ttu-id="539a1-137">根據主機服務提供者儲存體系統的功能，在儲存體帳戶中完全代管完整的靜態網站是有可能的。</span><span class="sxs-lookup"><span data-stu-id="539a1-137">Depending on the capabilities of the hosting provider's storage system, it might be possible to entirely host a fully static website in a storage account.</span></span>

- <span data-ttu-id="539a1-138">公開在其他代管環境或內部部署伺服器中執行之應用程式的靜態資源和內容。</span><span class="sxs-lookup"><span data-stu-id="539a1-138">Exposing static resources and content for applications running in other hosting environments or on-premises servers.</span></span>

- <span data-ttu-id="539a1-139">使用內容傳遞網路快取全球各地多個資料中心中之儲存體帳戶的內容，查找多個地理區域中的內容。</span><span class="sxs-lookup"><span data-stu-id="539a1-139">Locating content in more than one geographical area using a content delivery network that caches the contents of the storage account in multiple datacenters around the world.</span></span>

- <span data-ttu-id="539a1-140">監視成本和頻寬使用量。</span><span class="sxs-lookup"><span data-stu-id="539a1-140">Monitoring costs and bandwidth usage.</span></span> <span data-ttu-id="539a1-141">針對部分或全部靜態內容使用不同的儲存體帳戶，可使成本更容易與代管和執行階段成本分開。</span><span class="sxs-lookup"><span data-stu-id="539a1-141">Using a separate storage account for some or all of the static content allows the costs to be more easily separated from hosting and runtime costs.</span></span>

<span data-ttu-id="539a1-142">此模式在下列情況下可能不是很有用：</span><span class="sxs-lookup"><span data-stu-id="539a1-142">This pattern might not be useful in the following situations:</span></span>

- <span data-ttu-id="539a1-143">應用程式在將靜態內容傳遞給用戶端之前，需要對其進行一些處理。</span><span class="sxs-lookup"><span data-stu-id="539a1-143">The application needs to perform some processing on the static content before delivering it to the client.</span></span> <span data-ttu-id="539a1-144">例如，可能需要在文件加上時間戳記。</span><span class="sxs-lookup"><span data-stu-id="539a1-144">For example, it might be necessary to add a timestamp to a document.</span></span>

- <span data-ttu-id="539a1-145">靜態內容的量非常小。</span><span class="sxs-lookup"><span data-stu-id="539a1-145">The volume of static content is very small.</span></span> <span data-ttu-id="539a1-146">從不同的儲存體擷取此內容的負擔，可能超過將它從計算資源中區隔出來的成本優勢。</span><span class="sxs-lookup"><span data-stu-id="539a1-146">The overhead of retrieving this content from separate storage can outweigh the cost benefit of separating it out from the compute resource.</span></span>

## <a name="example"></a><span data-ttu-id="539a1-147">範例</span><span class="sxs-lookup"><span data-stu-id="539a1-147">Example</span></span>

<span data-ttu-id="539a1-148">Azure 儲存體支援直接從儲存體容器提供靜態內容。</span><span class="sxs-lookup"><span data-stu-id="539a1-148">Azure Storage supports serving static content directly from a storage container.</span></span> <span data-ttu-id="539a1-149">檔案會透過匿名存取要求來提供。</span><span class="sxs-lookup"><span data-stu-id="539a1-149">Files are served through anonymous access requests.</span></span> <span data-ttu-id="539a1-150">根據預設，檔案的 URL 中會有 `core.windows.net` 子網域，例如 `https://contoso.z4.web.core.windows.net/image.png`。</span><span class="sxs-lookup"><span data-stu-id="539a1-150">By default, files have a URL in a subdomain of `core.windows.net`, such as `https://contoso.z4.web.core.windows.net/image.png`.</span></span> <span data-ttu-id="539a1-151">您可以設定自訂網域名稱，並使用 Azure CDN 來透過 HTTPS 存取檔案。</span><span class="sxs-lookup"><span data-stu-id="539a1-151">You can configure a custom domain name, and use Azure CDN to access the files over HTTPS.</span></span> <span data-ttu-id="539a1-152">如需詳細資訊，請參閱 [Azure 儲存體中的靜態網站裝載](/azure/storage/blobs/storage-blob-static-website)。</span><span class="sxs-lookup"><span data-stu-id="539a1-152">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

![直接從儲存體服務傳遞應用程式的靜態部分](./_images/static-content-hosting-pattern.png)

<span data-ttu-id="539a1-154">靜態網站裝載可讓您透過匿名方式來存取檔案。</span><span class="sxs-lookup"><span data-stu-id="539a1-154">Static website hosting makes the files available for anonymous access.</span></span> <span data-ttu-id="539a1-155">如果您需要控制哪些人可以存取檔案，您可以將檔案儲存在 Azure Blob 儲存體中，然後產生[共用存取簽章](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)來限制存取。</span><span class="sxs-lookup"><span data-stu-id="539a1-155">If you need to control who can access the files, you can store files in Azure blob storage and then generate [shared access signatures](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) to limit access.</span></span>

<span data-ttu-id="539a1-156">傳遞至用戶端的頁面中的連結，必須指定資源的完整 URL。</span><span class="sxs-lookup"><span data-stu-id="539a1-156">The links in the pages delivered to the client must specify the full URL of the resource.</span></span> <span data-ttu-id="539a1-157">如果使用限時金鑰 (例如共用存取簽章) 保護資源，此簽章必須包含在 URL 中。</span><span class="sxs-lookup"><span data-stu-id="539a1-157">If the resource is protected with a valet key, such as a shared access signature, this signature must be included in the URL.</span></span>

<span data-ttu-id="539a1-158">在 [GitHub][sample-app] 上可以找到一個應用程式範例，該應用程式範例示範如何針對靜態資源使用外部儲存體。</span><span class="sxs-lookup"><span data-stu-id="539a1-158">A sample application that demonstrates using external storage for static resources is available on [GitHub][sample-app].</span></span> <span data-ttu-id="539a1-159">此範例使用設定檔來指定儲存體帳戶，以及包含靜態內容的容器。</span><span class="sxs-lookup"><span data-stu-id="539a1-159">This sample uses configuration files to specify the storage account and container that holds the static content.</span></span>

```xml
<Setting name="StaticContent.StorageConnectionString"
         value="UseDevelopmentStorage=true" />
<Setting name="StaticContent.Container" value="static-content" />
```

<span data-ttu-id="539a1-160">StaticContentHosting.Web 專案之 Settings.cs 檔案中的 `Settings` 類別包含數種方法，可擷取這些值並建立包含雲端儲存體帳戶容器 URL 的字串值。</span><span class="sxs-lookup"><span data-stu-id="539a1-160">The `Settings` class in the file Settings.cs of the StaticContentHosting.Web project contains methods to extract these values and build a string value containing the cloud storage account container URL.</span></span>

```csharp
public class Settings
{
  public static string StaticContentStorageConnectionString {
    get
    {
      return RoleEnvironment.GetConfigurationSettingValue(
                              "StaticContent.StorageConnectionString");
    }
  }

  public static string StaticContentContainer
  {
    get
    {
      return RoleEnvironment.GetConfigurationSettingValue("StaticContent.Container");
    }
  }

  public static string StaticContentBaseUrl
  {
    get
    {
      var account = CloudStorageAccount.Parse(StaticContentStorageConnectionString);

      return string.Format("{0}/{1}", account.BlobEndpoint.ToString().TrimEnd('/'),
                                      StaticContentContainer.TrimStart('/'));
    }
  }
}
```

<span data-ttu-id="539a1-161">StaticContentUrlHtmlHelper.cs 檔案中的 `StaticContentUrlHtmlHelper` 類別會公開一個名為 `StaticContentUrl` 的方法，該方法會產生包含雲端儲存體帳戶路徑的 URL (如果傳遞給它的 URL 以 ASP.NET 根路徑字元 (~) 為開頭)。</span><span class="sxs-lookup"><span data-stu-id="539a1-161">The `StaticContentUrlHtmlHelper` class in the file StaticContentUrlHtmlHelper.cs exposes a method named `StaticContentUrl` that generates a URL containing the path to the cloud storage account if the URL passed to it starts with the ASP.NET root path character (~).</span></span>

```csharp
public static class StaticContentUrlHtmlHelper
{
  public static string StaticContentUrl(this HtmlHelper helper, string contentPath)
  {
    if (contentPath.StartsWith("~"))
    {
      contentPath = contentPath.Substring(1);
    }

    contentPath = string.Format("{0}/{1}", Settings.StaticContentBaseUrl.TrimEnd('/'),
                                contentPath.TrimStart('/'));

    var url = new UrlHelper(helper.ViewContext.RequestContext);

    return url.Content(contentPath);
  }
}
```

<span data-ttu-id="539a1-162">Views\Home 資料夾中的 Index.cshtml 檔案包含使用 `StaticContentUrl` 方法為其 `src` 屬性建立 URL 的影像元素。</span><span class="sxs-lookup"><span data-stu-id="539a1-162">The file Index.cshtml in the Views\Home folder contains an image element that uses the `StaticContentUrl` method to create the URL for its `src` attribute.</span></span>

```html
<img src="@Html.StaticContentUrl("~/media/orderedList1.png")" alt="Test Image" />
```

## <a name="related-patterns-and-guidance"></a><span data-ttu-id="539a1-163">相關的模式和指導方針</span><span class="sxs-lookup"><span data-stu-id="539a1-163">Related patterns and guidance</span></span>

- <span data-ttu-id="539a1-164">[靜態內容裝載範例][sample-app]。</span><span class="sxs-lookup"><span data-stu-id="539a1-164">[Static Content Hosting sample][sample-app].</span></span> <span data-ttu-id="539a1-165">示範此模式的應用程式範例。</span><span class="sxs-lookup"><span data-stu-id="539a1-165">A sample application that demonstrates this pattern.</span></span>
- <span data-ttu-id="539a1-166">[有限權限金鑰模式](./valet-key.md)。</span><span class="sxs-lookup"><span data-stu-id="539a1-166">[Valet Key pattern](./valet-key.md).</span></span> <span data-ttu-id="539a1-167">如果目標資源不應提供給匿名使用者使用，請使用此模式來限制直接存取。</span><span class="sxs-lookup"><span data-stu-id="539a1-167">If the target resources aren't supposed to be available to anonymous users, use this pattern to restrict direct access.</span></span>
- <span data-ttu-id="539a1-168">[Azure 上的無伺服器 Web 應用程式](../reference-architectures/serverless/web-app.md)。</span><span class="sxs-lookup"><span data-stu-id="539a1-168">[Serverless web application on Azure](../reference-architectures/serverless/web-app.md).</span></span> <span data-ttu-id="539a1-169">這是參考架構，會搭配使用靜態網站裝載和 Azure Functions 來實作無伺服器的 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="539a1-169">A reference architecture that uses static website hosting with Azure Functions to implement a serverless web app.</span></span>

[sample-app]: https://github.com/mspnp/cloud-design-patterns/tree/master/static-content-hosting

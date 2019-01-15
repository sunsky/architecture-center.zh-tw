---
title: 靜態內容裝載模式
titleSuffix: Cloud Design Patterns
description: 將靜態內容部署到可以直接將其交付給用戶端的雲端儲存體服務。
keywords: 設計模式
author: dragon119
ms.date: 01/04/2019
ms.custom: seodec18
ms.openlocfilehash: cf4f65e935a01e4d84b3cc82b5779edb729bd80e
ms.sourcegitcommit: 036cd03c39f941567e0de4bae87f4e2aa8c84cf8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2019
ms.locfileid: "54058177"
---
# <a name="static-content-hosting-pattern"></a>靜態內容裝載模式

將靜態內容部署到可以直接將其交付給用戶端的雲端儲存體服務。 這可以降低對潛在昂貴之計算執行個體的需求。

## <a name="context-and-problem"></a>內容和問題

Web 應用程式通常包含靜態內容的某些元素。 這個靜態內容可能包括 HTML 頁面及其他資源，例如用戶端可用的影像和文件，無論是作為 HTML 網頁的一部分 (如內嵌影像、樣式表和用戶端 JavaScript 檔案)，或是作為個別的下載項目 (例如 PDF 文件)。

雖然已針對動態轉譯和輸出快取而對 Web 伺服器進行最佳化，但 Web 伺服器仍然需要處理下載靜態內容的要求。 這會取用通常可以更加適當使用的處理週期。

## <a name="solution"></a>解決方法

在大部分的雲端裝載環境中，您可以將一些應用程式的資源和靜態網頁放在儲存體服務中。 儲存體服務可處理這些資源的要求，從而讓負責處理其他 Web 要求的計算資源減少負載。 雲端裝載儲存體的成本通常遠低於計算執行個體的成本。

在儲存體服務中裝載應用程式的某些部分時，主要考量因素與應用程式部署以及保護不打算提供給匿名使用者使用的資源有關。

## <a name="issues-and-considerations"></a>問題和考量

當您決定如何實作此模式時，請考慮下列幾點：

- 裝載的儲存體服務必須公開使用者可以存取，以便下載靜態資源的 HTTP 端點。 某些儲存體服務也支援 HTTPS，因此可以將資源裝載在需要 SSL 的儲存體服務中。

- 為了獲得最大效能和可用性，請考慮使用內容傳遞網路 (CDN) 快取全球各地多個資料中心中之儲存體容器的內容。 但是，您可能需要支付使用 CDN 的費用。

- 儲存體帳戶依預設通常會進行異地複寫，針對可能會影響資料中心的事件提供復原功能。 這表示 IP 位址可能會變更，但 URL 將會維持不變。

- 當某些內容位於儲存體帳戶中，而其他內容位於裝載的計算執行個體中時，部署和更新應用程式會變得更麻煩。 您可能必須執行個別的部署，並對應用程式和內容進行版本化，以便更輕鬆地管理&mdash;特別是當靜態內容包含指令碼檔案或 UI 元件時。 但是，如果只需要更新靜態資源，則只需將其上傳至儲存體帳戶，而不需要重新部署應用程式套件。

- 儲存體服務可能不支援使用自訂網域名稱。 在此情況下，就必須在連結中指定資源的完整 URL，因為它們將位於與含有連結之動態產生內容不同的網域中。

- 儲存體容器必須設定公用讀取權限，但一定要確保它們未設定公用寫入權限，以避免使用者上傳內容。

- 請考慮使用限時金鑰或權杖，來針對不應匿名使用的資源控制其存取權。 如需詳細資訊，請參閱[限時金鑰模式](./valet-key.md)。

## <a name="when-to-use-this-pattern"></a>使用此模式的時機

這種模式在下列情況中非常有用：

- 將包含一些靜態資源之網站和應用程式的託管成本降至最低。

- 將僅由靜態內容和資源組成之網站的託管成本降至最低。 根據主機服務提供者儲存體系統的功能，在儲存體帳戶中完全代管完整的靜態網站是有可能的。

- 公開在其他代管環境或內部部署伺服器中執行之應用程式的靜態資源和內容。

- 使用內容傳遞網路快取全球各地多個資料中心中之儲存體帳戶的內容，查找多個地理區域中的內容。

- 監視成本和頻寬使用量。 針對部分或全部靜態內容使用不同的儲存體帳戶，可使成本更容易與代管和執行階段成本分開。

此模式在下列情況下可能不是很有用：

- 應用程式在將靜態內容傳遞給用戶端之前，需要對其進行一些處理。 例如，可能需要在文件加上時間戳記。

- 靜態內容的量非常小。 從不同的儲存體擷取此內容的負擔，可能超過將它從計算資源中區隔出來的成本優勢。

## <a name="example"></a>範例

Azure 儲存體支援直接從儲存體容器提供靜態內容。 檔案會透過匿名存取要求來提供。 根據預設，檔案的 URL 中會有 `core.windows.net` 子網域，例如 `https://contoso.z4.web.core.windows.net/image.png`。 您可以設定自訂網域名稱，並使用 Azure CDN 來透過 HTTPS 存取檔案。 如需詳細資訊，請參閱 [Azure 儲存體中的靜態網站裝載](/azure/storage/blobs/storage-blob-static-website)。

![直接從儲存體服務傳遞應用程式的靜態部分](./_images/static-content-hosting-pattern.png)

靜態網站裝載可讓您透過匿名方式來存取檔案。 如果您需要控制哪些人可以存取檔案，您可以將檔案儲存在 Azure Blob 儲存體中，然後產生[共用存取簽章](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)來限制存取。

傳遞至用戶端的頁面中的連結，必須指定資源的完整 URL。 如果使用限時金鑰 (例如共用存取簽章) 保護資源，此簽章必須包含在 URL 中。

在 [GitHub][sample-app] 上可以找到一個應用程式範例，該應用程式範例示範如何針對靜態資源使用外部儲存體。 此範例使用設定檔來指定儲存體帳戶，以及包含靜態內容的容器。

```xml
<Setting name="StaticContent.StorageConnectionString"
         value="UseDevelopmentStorage=true" />
<Setting name="StaticContent.Container" value="static-content" />
```

StaticContentHosting.Web 專案之 Settings.cs 檔案中的 `Settings` 類別包含數種方法，可擷取這些值並建立包含雲端儲存體帳戶容器 URL 的字串值。

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

StaticContentUrlHtmlHelper.cs 檔案中的 `StaticContentUrlHtmlHelper` 類別會公開一個名為 `StaticContentUrl` 的方法，該方法會產生包含雲端儲存體帳戶路徑的 URL (如果傳遞給它的 URL 以 ASP.NET 根路徑字元 (~) 為開頭)。

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

Views\Home 資料夾中的 Index.cshtml 檔案包含使用 `StaticContentUrl` 方法為其 `src` 屬性建立 URL 的影像元素。

```html
<img src="@Html.StaticContentUrl("~/media/orderedList1.png")" alt="Test Image" />
```

## <a name="related-patterns-and-guidance"></a>相關的模式和指導方針

- [靜態內容裝載範例][sample-app]。 示範此模式的應用程式範例。
- [有限權限金鑰模式](./valet-key.md)。 如果目標資源不應提供給匿名使用者使用，請使用此模式來限制直接存取。
- [Azure 上的無伺服器 Web 應用程式](../reference-architectures/serverless/web-app.md)。 這是參考架構，會搭配使用靜態網站裝載和 Azure Functions 來實作無伺服器的 Web 應用程式。

[sample-app]: https://github.com/mspnp/cloud-design-patterns/tree/master/static-content-hosting

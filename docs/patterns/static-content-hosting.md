---
title: 靜態內容裝載
description: 將靜態內容部署到可以直接將其傳遞給用戶端的雲端式儲存體服務。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories:
- data-management
- design-implementation
- performance-scalability
ms.openlocfilehash: deb15001bea2598d56a2793be78bbc3e7473bdf3
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
ms.locfileid: "24541684"
---
# <a name="static-content-hosting-pattern"></a>靜態內容裝載模式

[!INCLUDE [header](../_includes/header.md)]

將靜態內容部署到可以直接將其傳遞給用戶端的雲端式儲存體服務。 這可以降低對潛在昂貴之計算執行個體的需求。

## <a name="context-and-problem"></a>內容和問題

Web 應用程式通常包含靜態內容的某些元素。 這個靜態內容可能包括 HTML 頁面及其他資源，例如用戶端可用的影像和文件，無論是作為 HTML 網頁的一部分 (如內嵌影像、樣式表和用戶端 JavaScript 檔案)，或是作為個別的下載項目 (例如 PDF 文件)。

雖然 Web 伺服器已調整成透過有效率的動態網頁程式碼執行和輸出快取來最佳化要求，但仍然需要處理下載靜態內容的要求。 這會取用通常可以更加適當使用的處理週期。

## <a name="solution"></a>解決方式

在大部分的雲端裝載環境中，透過在儲存體服務中尋找應用程式的部分資源和靜態網頁，可以將計算執行個體的需求降到最低 (例如，使用較小的執行個體或較少的執行個體)。 雲端裝載儲存體的成本通常遠低於計算執行個體的成本。

在儲存體服務中裝載應用程式的某些部分時，主要考量因素與應用程式部署以及保護不打算提供給匿名使用者使用的資源有關。

## <a name="issues-and-considerations"></a>問題和考量

當您決定如何實作此模式時，請考慮下列幾點：

- 裝載的儲存體服務必須公開使用者可以存取，以便下載靜態資源的 HTTP 端點。 某些儲存體服務也支援 HTTPS，因此可以將資源裝載在需要 SSL 的儲存體服務中。

- 為了獲得最大效能和可用性，請考慮使用內容傳遞網路 (CDN) 快取全球各地多個資料中心中之儲存體容器的內容。 但是，您可能需要支付使用 CDN 的費用。

- 儲存體帳戶依預設通常會進行異地複寫，針對可能會影響資料中心的事件提供復原功能。 這表示 IP 位址可能會變更，但 URL 將會維持不變。

- 當某些內容位於儲存體帳戶中，而其他內容位於裝載的計算執行個體中時，部署應用程式並對其進行更新將變得更具挑戰性。 您可能必須執行個別的部署，並對應用程式和內容進行版本化，以便更輕鬆地管理&mdash;特別是當靜態內容包含指令碼檔案或 UI 元件時。 但是，如果只需要更新靜態資源，則只需將其上傳至儲存體帳戶，而不需要重新部署應用程式套件。

- 儲存體服務可能不支援使用自訂網域名稱。 在此情況下，就必須在連結中指定資源的完整 URL，因為它們將位於與含有連結之動態產生內容不同的網域中。

- 儲存體容器必須設定公用讀取權限，但一定要確保它們未設定公用寫入權限，以避免使用者上傳內容。 請考慮使用有限權限金鑰或權杖來控制對不應該匿名存取存取存取存取存取存取存取存取存取存取存取存取存取存取存取存取存取之資源的存取&mdash;如需詳細資訊，請參閱[有限權限金鑰模式](valet-key.md)。

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

Azure Blob 儲存體中的靜態內容可以直接透過網頁瀏覽器存取。 Azure 在儲存體上提供一個可公開給用戶端的 HTTP 型介面。 例如，Azure Blob 儲存體容器中的內容會使用下列格式的 URL 來公開：

`http://[ storage-account-name ].blob.core.windows.net/[ container-name ]/[ file-name ]`


上傳內容時，必須建立一或多個 Blob 容器來保存檔案和文件。 請注意，新的容器的預設權限為「私人」，您必須將其變更為「公開」以允許用戶端存取內容。 如果有必要避免匿名存取內容，則可以實作[有限權限金鑰模式](valet-key.md)，要求使用者必須出示有效權杖才能下載資源。

> [Blob 服務概念](https://msdn.microsoft.com/library/azure/dd179376.aspx)包含 Blob 儲存體的相關資訊，以及您可以存取並使用它的方式。

每一頁中的連結都會指定資源的 URL，而用戶端會直接從儲存體服務存取它。 此圖說明直接從儲存體服務傳遞應用程式的靜態部分。

![圖 1 - 直接從儲存體服務傳遞應用程式的靜態部分](./_images/static-content-hosting-pattern.png)


傳遞至用戶端的頁面中的連結，必須指定 Blob 容器及資源的完整 URL。 例如，包含公用容器中之影像連結的頁面，可能包含以下的 HTML。

```html
<img src="http://mystorageaccount.blob.core.windows.net/myresources/image1.png"
     alt="My image" />
```

> 如果使用有限權限金鑰 (例如 Azure 共用存取簽章) 保護資源，此簽章必須包含在連結中的 URL。

從 [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/static-content-hosting) 可以找到一個名為 StaticContentHosting 的解決方案，該解決方案示範如何針對靜態資源使用外部儲存體。 StaticContentHosting.Cloud 專案包含指定儲存體帳戶的設定檔，以及包含靜態內容的容器。

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

- [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/static-content-hosting) \(英文\) 上有提供示範此模式的範例。
- [有限權限金鑰模式](valet-key.md)。 如果目標資源不應該提供給匿名使用者使用，則必須在保存靜態內容的存放區上實作安全性。 描述如何使用權杖或金鑰為用戶端提供特定資源或服務 (例如雲端裝載儲存體服務) 的受限制直接存取。
- Infosys 部落格上的[在 Azure 上部署靜態網站的有效方法](http://www.infosysblogs.com/microsoft/2010/06/an_efficient_way_of_deploying.html) \(英文\)。
- [Blob 服務概念](https://msdn.microsoft.com/library/azure/dd179376.aspx) \(英文\)

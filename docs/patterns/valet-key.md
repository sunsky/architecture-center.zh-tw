---
title: Valet 金鑰
description: 使用可提供用戶端對特定資源或服務受限制的直接存取的權杖或金鑰。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories:
- data-management
- security
ms.openlocfilehash: 99d3fbe05e34d61edc0d339f34665e557b250b05
ms.sourcegitcommit: fb22348f917a76e30a6c090fcd4a18decba0b398
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2018
ms.locfileid: "53450882"
---
# <a name="valet-key-pattern"></a>Valet 金鑰模式

[!INCLUDE [header](../_includes/header.md)]

使用為用戶端提供特定資源之有限直接存取權的權杖，以便卸載來自應用程式的資料轉送。 在使用雲端裝載儲存體系統或佇列的應用程式中特別有用，而且可以最小化成本及最大化延展性和效能。

## <a name="context-and-problem"></a>內容和問題

用戶端程式和網頁瀏覽器通常需要從應用程式的儲存體讀取和寫入檔案或資料串流。 一般而言，應用程式會處理資料的移動 &mdash; 藉由從儲存體擷取並串流至用戶端，或是藉由從用戶端讀取已上傳的串流，並將其儲存在資料存放區。 不過，這個方法會吸收寶貴的資源，例如計算、記憶體和頻寬。

資料存放區具備直接處理資料上傳和下載的能力，不需要應用程式執行任何處理來移動此資料。 但是，通常需要用戶端具有存放區之安全性認證的存取權。 這可以是一個有用方法，最小化資料轉送成本和相應放大的需求，並且最大化效能。 但是，這意味著應用程式再也無法管理資料的安全性。 用戶端連線到資料存放區進行直接存取之後，應用程式就無法作為閘道管理員。 它再也無法控制處理，且無法阻止資料存放區的後續上傳或下載。

這個方法在需要服務不受信任用戶端的分散式系統中不實際。 相反地，應用程式必須能夠以細微的方式安全地控制資料的存取權，但是仍然減少透過設定此連線在伺服器上造成的負載，然後允許用戶端直接與資料存放區通訊，來執行必要的讀取或寫入作業。

## <a name="solution"></a>解決方法

您必須解決控制資料存放區存取權的問題，該存放區無法管理用戶端的驗證和授權。 一個典型的解決方案是限制資料存放區公用連線的存取權，並且為用戶端提供資料存放區可以驗證的金鑰或權杖。

此金鑰或權杖通常稱為 Valet 金鑰。 它提供特定資源的有限時間存取權，並且只允許預先定義的作業，例如儲存體或佇列的讀取和寫入作業，或網頁瀏覽器中的上傳和下載作業。 應用程式可以快速且輕易地建立 Valet 金鑰，並且核發給用戶端裝置和網頁瀏覽器，讓用戶端執行必要的作業，而不需要應用程式直接處理資料轉送。 這樣會從應用程式和伺服器移除處理額外負荷，以及對效能和延展性的影響。

用戶端會使用此權杖，只在特定期間存取資料存放區中的特定資源，而且具有存取權限的特定限制，如下圖所示。 在指定期間之後，金鑰就無效，且不允許存取資源。

![圖 1 - 模式概觀](./_images/valet-key-pattern.png)

可以設定具有其他相依性的金鑰，例如資料的範圍。 例如，依據資料存放區功能而定，金鑰可以指定資料存放區中完整的資料表，或僅指定資料表中的特定資料列。 在雲端儲存體系統中，金鑰可以指定容器或僅指定容器內的特定項目。

金鑰也可以由應用程式判定為無效。 如果用戶端通知伺服器資料轉送作業已完成，這個方法很有用。 然後伺服器可以將該金鑰判定為無效，以避免進一歩的存取。

使用此模式可以簡化資源存取權的管理，因為不需要建立和驗證使用者、授與權限，然後再次移除使用者。 也可以輕易地限制位置、權限以及有效期間 &mdash; 所有作業都藉由只在執行階段產生金鑰來完成。 重要的因素是限制有效期間，特別是資源的位置，盡可能靠近以讓收件者針對預期的用途使用它。

## <a name="issues-and-considerations"></a>問題和考量

當您決定如何實作此模式時，請考慮下列幾點：

**管理金鑰的有效狀態和期間**。 如果遭到外洩或洩露，金鑰可以有效地將目標項目解除鎖定，讓它能夠在有效期間被惡意使用。 金鑰通常會撤銷或停用，取決於發出方式。 伺服器端原則可以變更，或者將它簽署的伺服器金鑰判定為無效。 指定簡短有效期間以最小化資料存放區發生未經授權作業的風險。 不過，如果有效期間太短，用戶端可能無法在金鑰到期之前完成作業。 如果需要受保護資源的多重存取，允許授權的使用者在有效期間到期之前更新金鑰。

**控制金鑰提供的存取層級**。 一般而言，金鑰應該允許使用者只能執行完成作業所需的動作，例如，如果用戶端應該不能將資料上傳到資料存放區，則只允許唯讀存取。 針對檔案上傳，通常會指定提供唯寫權限的金鑰，以及位置和有效期間。 請務必正確地指定要套用金鑰的資源或一組資源。

**考量如何控制使用者的行為**。 實作此模式表示遺失使用者授與存取權之資源的控制權。 可以施加的控制權層級受限於服務或目標資料存放區可用之原則和權限的功能。 例如，通常無法建立金鑰，限制要寫入儲存體的資料大小，或金鑰可用來存取檔案的次數。 這會導致資料轉送的龐大非預期成本，即使由預期的用戶端使用，而且可能會由造成重複上傳或下載之程式碼中的錯誤所導致。 若要限制檔案可以上傳的次數，在可行時強制用戶端於一個作業完成時通知應用程式。 例如，某些資料存放區會引發事件，應用程式程式碼可用來監視作業以及控制使用者行為。 不過，很難在其中一個租用戶中所有使用者都使用相同金鑰的多租用戶案例中，強制執行個別使用者的配額。

**驗證及選擇性處理所有上傳的資料**。 取得金鑰存取權的惡意使用者可以上傳設計來危害系統的資料。 或者，授權的使用者可能上傳無效的資料，在處理時可能會導致錯誤或系統失敗。 若要防止此問題，請確定驗證所有上傳的資料，並檢查是否有惡意內容，然後再使用。

**稽核所有作業**。 許多金鑰型機制可以記錄作業，例如上傳、下載及失敗。 這些記錄通常可以併入稽核處理程序，如果使用者根據檔案大小或資料磁碟區付費，也用於計費。 使用記錄來偵測可能是由金鑰提供者的問題或意外移除預存存取原則所造成的驗證失敗。

**安全地傳遞金鑰**。 它可以內嵌在使用者於網頁中啟動的 URL，或者可以在伺服器重新導向作業中使用，讓下載自動發生。 一律使用 HTTPS 以透過安全通道傳遞金鑰。

**保護傳輸中的敏感性資料**。 透過應用程式傳遞的敏感性資料通常會使用 SSL 或 TLS，應該針對直接存取資料存放區的用戶端強制執行。

實作此模式時應該注意的其他問題如下：

- 如果用戶端尚未或無法通知伺服器作業已完成，則唯一的限制是金鑰的到期時間，應用程式無法執行稽核作業，例如計算上傳或下載次數，或者防止多重上傳或下載。

- 可以產生之金鑰原則的彈性可能會有所限制。 例如，某些機制只允許使用時控的到期時間。 其他機制則無法指定足夠的讀取/寫入權限細微性。

- 如果已指定金鑰或權杖有效期間的開始時間，請確定它稍早於目前的伺服器時間，以允許用戶端時鐘可能會稍微不同步。 如果未指定，預設值通常是目前的伺服器時間。

- 包含金鑰的 URL 會記錄在伺服器記錄檔中。 金鑰通常會在記錄檔用於分析之前到期，請確保您限制它們的存取權。 如果記錄資料傳輸到監視系統或儲存在另一個位置，請考慮實作延遲以防止金鑰外洩，直到它們的有效期間到期。

- 如果用戶端程式碼是在網頁瀏覽器中執行，瀏覽器可能需要支援跨原始來源資源共用 (CORS)，讓網頁瀏覽器內執行的程式碼可以從服務分頁的不同網域中存取資料。 某些較舊的瀏覽器和某些資料存放區不支援 CORS，在這些瀏覽器中執行的程式碼也許可以使用 Valet 金鑰來提供不同網域中資料的存取權，例如雲端儲存體帳戶。

## <a name="when-to-use-this-pattern"></a>使用此模式的時機

此模式在下列情況下很有用：

- 為了最小化資源負載，以及最大化效能和延展性。 使用 Valet 金鑰不需要鎖定資源、不需要遠端伺服器呼叫、沒有可發出 Valet 金鑰數目的限制，而且它會避免透過應用程式程式碼執行資料轉送所造成的單一失敗點。 建立 Valet 金鑰通常是使用金鑰簽署字串的簡單密碼編譯作業。

- 為了最小化營運成本。 啟用存放區和佇列的直接存取具有資源和成本效益，可導致較少的網路來回行程，並且可能會減少所需的計算資源數目。

- 當用戶端定期上傳或下載資料時，特別是在大型磁碟區或每個作業牽涉到大型檔案時。

- 當應用程式由於裝載限制或成本考量，具有有限的計算資源可用時。 在此案例中，如果有許多並行資料上傳或下載，模式甚至更有幫助，因為它會減輕應用程式處理資料轉送的壓力。

- 當資料儲存在遠端資料存放區或不同資料中心時。 如果需要應用程式作為閘道管理員，可能會針對在資料中心之間傳送資料，或跨用戶端與應用程式之間的公用或私人網路，然後在應用程式和與資料存放區之間，所造成的額外頻寬計費。

此模式在下列情況下可能不是很有用：

- 如果應用程式在資料儲存之前或傳送到用戶端之前，必須在資料上執行某些工作。 例如，如果應用程式需要執行驗證、記錄存取成功，或在資料上執行轉換。 不過，某些資料存放區與用戶端可以交涉並執行簡單的轉換，例如壓縮和解壓縮 (舉例來說，網頁瀏覽器通常可以處理 GZip 格式)。

- 如果現有應用程式的設計讓併入模式變得困難。 使用此模式通常針對傳送及接收資料需要不同的架構式方法。

- 如果需要維護稽核記錄或控制資料轉送作業的執行次數，且使用中的 Valet 金鑰機制不支援伺服器可用來管理這些作業的通知。

- 如果必須限制資料大小，特別是在上傳作業期間。 針對應用程式這個問題的唯一解決方案是在作業完成之後檢查資料大小，或在指定期間之後檢查上傳大小，或者依排程檢查。

## <a name="example"></a>範例

Azure 支援 Azure 儲存體上的共用存取簽章，以對 blob、資料表和佇列中的資料進行細微存取控制，同時適用於服務匯流排佇列和主題。 共用存取簽章權杖可以設定為提供特定資料表的特定存取權限，例如讀取、寫入、更新和刪除；資料表內的金鑰範圍；佇列；blob；或 blob 容器。 有效性可以是指定時間週期或者沒有時間限制。

Azure 共用存取簽章也支援伺服器預存存取原則，可與特定資源 (例如資料表或 blob) 相關聯。 相較於應用程式產生的共用存取簽章權杖，這項功能提供額外的控制和彈性，應該盡可能使用。 伺服器預存原則中定義的設定可以變更，而且會反映在權杖中而不需要發出新的權杖，但是如果沒有發出新的權杖就無法變更權杖中定義的設定。 這個方法也可以在權杖過期之前撤銷有效的共用存取簽章權杖。

> 如需詳細資訊，請參閱 MSDN 上的[簡介資料表 SAS (共用存取簽章)、佇列 SAS 以及 Blob SAS 的更新](https://blogs.msdn.microsoft.com/windowsazurestorage/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas/)和[使用共用存取簽章](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)。

下列程式碼示範如何建立有效期為五分鐘的共用存取簽章權杖。 `GetSharedAccessReferenceForUpload` 方法會傳回共用存取簽章權杖，可用來將檔案上傳至 Azure Blob 儲存體。

```csharp
public class ValuesController : ApiController
{
  private readonly CloudStorageAccount account;
  private readonly string blobContainer;
  ...
  /// <summary>
  /// Return a limited access key that allows the caller to upload a file
  /// to this specific destination for a defined period of time.
  /// </summary>
  private StorageEntitySas GetSharedAccessReferenceForUpload(string blobName)
  {
    var blobClient = this.account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference(this.blobContainer);

    var blob = container.GetBlockBlobReference(blobName);

    var policy = new SharedAccessBlobPolicy
    {
      Permissions = SharedAccessBlobPermissions.Write,

      // Specify a start time five minutes earlier to allow for client clock skew.
      SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5),

      // Specify a validity period of five minutes starting from now.
      SharedAccessExpiryTime = DateTime.UtcNow.AddMinutes(5)
    };

    // Create the signature.
    var sas = blob.GetSharedAccessSignature(policy);

    return new StorageEntitySas
    {
      BlobUri = blob.Uri,
      Credentials = sas,
      Name = blobName
    };
  }

  public struct StorageEntitySas
  {
    public string Credentials;
    public Uri BlobUri;
    public string Name;
  }
}
```

> ValetKey 解決方案中可用的完整範例可以從 [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/valet-key) 下載。 此解決方案中的 ValetKey.Web 專案包含 Web 應用程式，其中包含如上所示的 `ValuesController` 類別。 使用此 Web 應用程式來擷取共用存取簽章並且將檔案上傳至 blob 儲存體的範例用戶端應用程式，可用於 ValetKey.Client 專案。

## <a name="next-steps"></a>後續步驟

實作此模式時，下列模式和指導方針可能也相關：
- [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/valet-key) 上有提供示範此模式的範例。
- [閘道管理員模式](gatekeeper.md)。 此模式可以與 Valet 金鑰模式搭配使用，以保護應用程式和服務，方法是使用專用的主機執行個體，其會作為用戶端和應用程式或服務之間的代理程式。 閘道管理員會驗證和處理要求，並在用戶端與應用程式之間傳遞要求和資料。 可以提供一層額外的安全性，並且減少系統的受攻擊面。
- [靜態內容裝載模式](static-content-hosting.md)。 描述如何將靜態資源部署至雲端型儲存體服務，該服務可以將這些資源直接傳遞至用戶端，以減少昂貴計算執行個體的需求。 當資源不是作為公用使用時，Valet 金鑰模式可用來保護其安全。
- [簡介資料表 SAS (共用存取簽章)、佇列 SAS 以及 Blob SAS 的更新](https://blogs.msdn.microsoft.com/windowsazurestorage/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas/)
- [使用共用存取簽章](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
- [使用服務匯流排的共用存取簽章驗證](https://azure.microsoft.com/documentation/articles/service-bus-shared-access-signature-authentication/)

---
title: Retry
description: "讓應用程式可以在嘗試連線到服務或網路資源時，藉由明確地重試先前失敗的作業，處理預期的暫時性失敗。"
keywords: "設計模式"
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories: resiliency
ms.openlocfilehash: 6c02b384e71c068ecbc78f3170d28cea406538e2
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="retry-pattern"></a>重試模式

[!INCLUDE [header](../_includes/header.md)]

讓應用程式可以在嘗試連線到服務或網路資源時，藉由明確地重試失敗的作業，處理暫時性失敗。 這可以改善應用程式的穩定性。

## <a name="context-and-problem"></a>內容和問題

應用程式若要與在雲端中執行的元素通訊，必須能感應在此環境中發生的暫時性錯誤。 錯誤包括瞬間失去元件和服務的網路連線、暫時無法使用服務，或當服務忙碌時逾時。

這些錯誤通常會自行修正，而且如果在適當的延遲後再重複觸發錯誤的動作，動作可能會成功。 例如，正在處理大量並行要求的資料庫服務可以實作節流策略，暫時拒絕任何進一步的要求直到其工作負載降低。 嘗試存取資料庫的應用程式可能會無法連線，但如果延遲一段時間之後再次嘗試可能會成功。

## <a name="solution"></a>方案

在雲端中，暫時性錯誤不是很常見，應用程式應該設計為可以適當且明確地處理它們。 這會將錯誤可能會對應用程式正在執行的商務工作造成的影響降到最低。

如果應用程式嘗試將要求傳送至遠端服務時偵測到失敗，它可以使用下列策略處理失敗：

- **取消**。 如果錯誤顯然不是暫時性失敗，或重複執行不太可能會成功，則應用程式應該取消作業，並報告例外狀況。 例如，因為提供不正確認證而導致的驗證失敗，無論嘗試多少次都不可能成功。

- **重試**。 如果特定錯誤回報為不尋常或罕見，可能是不尋常的情況由造成，例如在傳輸時損毀的網路封包。 在此情況下，應用程式可以立即再次重試失敗的要求，因此相同的失敗不太可能再發生，要求可能會成功。

- **延遲之後重試。** 如果錯誤是因為幾個常見的連線問題之一或忙碌而失敗，網路或服務可能需要一小段時間，等待連線問題更正或工作的待辦項目清除。 應用程式應適當等待一段時間後再重試要求。

遇到常見的暫時性失敗，應慎選重試之間的間隔時間 (延遲)，儘可能平均分配來自多個應用程式執行個體的要求。 這可以減少忙碌服務繼續多載的機會。 如果許多的應用程式執行個體持續重試要求使服務不勝任負荷，就會需要更長時間才能復原服務。

如果要求仍然失敗，應用程式可以等待之後再嘗試。 如有必要，可以重複此程序並增加重試嘗試之間間隔的延遲，直到嘗試達到要求次數上限。 延遲可以累加方式或以指數方式增加，取決於失敗類型以及它在這段期間內會修正的機率。

下圖顯示使用此模式叫用託管服務中的作業。 如果在預先定義的次數之後要求仍失敗，應用程式應將錯誤視為例外狀況，並加以處理。

![圖 1 - 使用重試模式叫用託管服務中的作業](./_images/retry-pattern.png)

應用程式應將對遠端服務的所有存取嘗試包裝在程式碼中，以程式碼實作符合上述策略之一的重試原則。 傳送至不同的服務要求可能會採用不同原則。 有些廠商提供實作了重試原則的程式庫，其中的應用程式可以指定重試次數上限、重試嘗試的間隔時間和其他參數。

應用程式應記錄錯誤和失敗作業的詳細資料。 這項資訊對執行作業的人非常有用。 如果服務經常無法使用或忙碌，通常是因為服務耗盡其資源。 您可以相應放大服務，減少這些錯誤的發生頻率。 例如，如果資料庫服務持續多載，分割資料庫並將負載分散到多部伺服器可能有幫助。

> [Microsoft Entity Framework](https://docs.microsoft.com/ef/) 提供重試資料庫作業的機能。 此外，大多數的 Azure 服務與用戶端 SDK 皆包含重試機制。 如需詳細資訊，請參閱[特定服務的重試指引](https://docs.microsoft.com/en-us/azure/architecture/best-practices/retry-service-specific)。

## <a name="issues-and-considerations"></a>問題和考量

當您在決定如何實作此模式時，應考慮下列幾點。

重試原則應該加以調整以符合應用程式的商務需求和失敗的本質。 就某些非關鍵的作業而言，最好是快速失敗而不是重試數次，以免影響應用程式的輸送量。 例如，在存取遠端服務的互動式 Web 應用程式中，最好是經歷少數次短延遲時間的重試嘗試之後便失敗，並向使用者顯示適當的訊息 (例如，「請稍後再試一次」)。 就批次應用程式而言，可能更適合增加重試嘗試的次數，並以指數方式增加嘗試之間的延遲。

積極的重試原則在嘗試之間間隔的延遲時間較短，再加上大量重試，對已接近或達到產能的忙碌執行服務更是雪上加霜。 如果持續嘗試執行失敗的作業，此重試原則也可能會影響應用程式的回應能力。

如果在大量重試之後要求仍失敗，最好避免應用程式再向相同資源提出要求，只要立即回報失敗。 當期限到期時，應用程式可以暫時允許一或多個要求，看看是否成功。 如需此策略的詳細資料，請參閱[斷路器模式](circuit-breaker.md)。

考慮作業是否為等冪。 如果是，重試原本就安全。 否則，重試可能會導致作業執行一次以上，並產品非預期的副作用。 例如，服務可能會接收要求、處理要求成功，但無法傳送回應。 此時，重試邏輯可能會假設服務沒有收到第一個要求，並重新傳送要求。

對服務的要求失敗可能因為各種原因並引發不同的例外狀況，取決於失敗的本質。 某些例外狀況表示失敗可以快速地解決，有些例外狀況則表示失敗會再持續下去。 根據例外狀況的類型來調整重試原則的重試嘗試間隔時間，十分實用。

重試作業是交易的一部分，考慮其如何影響整體交易的一致性。 微調交易作業的重試原則，以獲得最高的成功機率，並減少復原所有交易步驟的必要。

確保所有的重試程式碼針對各種失敗狀況經過完整測試。 確認它不會嚴重影響應用程式的效能或可靠性，不會導致服務和資源的負載過大、或產生競爭情況或瓶頸。

只在完全了解失敗作業的脈絡內容時，才實作重試邏輯。 例如，如果包含重試原則的工作會叫用另一項也包含重試原則的工作，這額外的重試層會大大增加處理的時間。 最好是將較低層級的工作設定為可以快速失敗，並向叫用它的工作回報失敗的原因。 然後這個較高層級的工作就可以根據自己的原則處理失敗。

請務必記錄所有導致重試的連線失敗，以利找出基礎應用程式、服務或資源的問題。

調查服務或資源最有可能發生的錯誤，探索其是否會長時間持續或可以終結。 如有會，最好是將錯誤當作例外狀況處理。 應用程式可以報告或記錄例外狀況，然後叫用替代服務 (如果有的話) 或提供效能降低的功能，並繼續操作。 如需有關如何偵測及處理持續性錯誤的詳細資訊，請參閱[斷路器模式](circuit-breaker.md)。

## <a name="when-to-use-this-pattern"></a>使用此模式的時機

當應用程式與遠端服務互動或存取遠端資源時若可能發生暫時性錯誤，請使用此模式。 這些錯誤應該存在時間很短，且先前已失敗的要求可能在後續的嘗試時成功。

此模式可能不適合用於︰

- 錯誤時可能長持續時間，因為它可能會影響應用程式的回應能力。 應用程式嘗試重複可能失敗的要求，可能會浪費時間和資源。
- 處理不是因為暫時性錯誤造成的失敗，例如應用程式商務邏輯中的錯誤所造成的內部例外狀況。
- 作為處理系統中延展性問題的替代選項。 如果應用程式頻繁遇到忙碌的錯誤，通常是存取的服務或資源應該擴充規模的訊號。

## <a name="example"></a>範例

此 C# 範例示範重試模式的實作。 `OperationWithBasicRetryAsync` 方法 (如下所示) 會透過 `TransientOperationAsync` 方法非同步叫用外部服務。 `TransientOperationAsync` 方法的詳細資料為服務特定，在此範例程式碼中會省略。

```csharp
private int retryCount = 3;
private readonly TimeSpan delay = TimeSpan.FromSeconds(5);

public async Task OperationWithBasicRetryAsync()
{
  int currentRetry = 0;

  for (;;)
  {
    try
    {
      // Call external service.
      await TransientOperationAsync();

      // Return or break.
      break;
    }
    catch (Exception ex)
    {
      Trace.TraceError("Operation Exception");

      currentRetry++;

      // Check if the exception thrown was a transient exception
      // based on the logic in the error detection strategy.
      // Determine whether to retry the operation, as well as how
      // long to wait, based on the retry strategy.
      if (currentRetry > this.retryCount || !IsTransient(ex))
      {
        // If this isn't a transient error or we shouldn't retry, 
        // rethrow the exception.
        throw;
      }
    }

    // Wait to retry the operation.
    // Consider calculating an exponential delay here and
    // using a strategy best suited for the operation and fault.
    await Task.Delay(delay);
  }
}

// Async method that wraps a call to a remote service (details not shown).
private async Task TransientOperationAsync()
{
  ...
}
```

叫用這個方法的陳述式在 try/catch 區塊中，包裝在 for 迴圈中。 如果呼叫 `TransientOperationAsync` 方法成功，且沒有擲回例外狀況，for 迴圈就會結束。 如果 `TransientOperationAsync` 方法失敗，catch 區塊會檢查失敗的原因。 如果認為是暫時性的錯誤，程式碼會等待短暫的延遲後再重試操作。

for 迴圈也會追蹤此作業的嘗試次數，如果程式碼失敗三次，便假設例外狀況會長時間持續。 如果例外狀況不是暫時性而是持續性，catch 處理常式會擲回例外狀況。 這個例外狀況會結束 for 迴圈，叫用 `OperationWithBasicRetryAsync` 方法的程式碼應該會攔截到此例外狀況。

`IsTransient`方法 (如下所示) 會檢查與程式碼執行環境相關的一組特定例外狀況。 暫時性例外狀況的定義會根據存取的資源和執行作業的環境而有所不同。

```csharp
private bool IsTransient(Exception ex)
{
  // Determine if the exception is transient.
  // In some cases this is as simple as checking the exception type, in other
  // cases it might be necessary to inspect other properties of the exception.
  if (ex is OperationTransientException)
    return true;

  var webException = ex as WebException;
  if (webException != null)
  {
    // If the web exception contains one of the following status values
    // it might be transient.
    return new[] {WebExceptionStatus.ConnectionClosed,
                  WebExceptionStatus.Timeout,
                  WebExceptionStatus.RequestCanceled }.
            Contains(webException.Status);
  }

  // Additional exception checking logic goes here.
  return false;
}
```

## <a name="related-patterns-and-guidance"></a>相關的模式和指導方針

- [斷路器模式](circuit-breaker.md)。 重試模式在處理暫時性錯誤上相當實用。 如果失敗會長時間持續，則可能較適合實作「斷路器」模式。 重試模式也能與斷路器搭配使用，提供處理錯誤的全方位方法。
- [特定服務的重試指引](https://docs.microsoft.com/en-us/azure/architecture/best-practices/retry-service-specific)
- [連線恢復功能](https://docs.microsoft.com/en-us/ef/core/miscellaneous/connection-resiliency)

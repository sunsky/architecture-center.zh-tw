---
title: 競爭取用者模式
titleSuffix: Cloud Design Patterns
description: 讓多個並行取用者處理在相同傳訊通道上接收的訊息。
keywords: 設計模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: ea3f48971a78f59ad6575b055278aab449fa26a1
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54485225"
---
# <a name="competing-consumers-pattern"></a>競爭取用者模式

[!INCLUDE [header](../_includes/header.md)]

讓多個並行取用者處理在相同傳訊通道上接收的訊息。 此方法可讓系統同時處理多個訊息，以達到最佳輸送量、改進延展性及可用性，以及平衡工作負載。

## <a name="context-and-problem"></a>內容和問題

在雲端中執行的應用程式預期會處理大量要求。 每個要求並非以同步方式處理，常用的技巧是讓應用程式透過傳訊系統將要求傳遞至以非同步方式處理的其他服務 (取用者服務)。 此策略有助於確保處理要求時，應用程式中的商務邏輯不會被封鎖。

基於多種原因，要求數目可能會隨著時間而明顯起伏。 來自多個租用戶突增的使用者活動或彙總要求，可能會造成無法預期的工作負載。 在尖峰時間，系統每秒可能需要處理數百筆要求，而其他時間的數字可能非常小。 此外，處理這些要求所需執行工作的性質可能變化極大。 使用單一取用者服務執行個體，可能會導致該執行個體大量湧入要求，或傳訊系統可能因從應用程式大量湧進的訊息而超載。 為了處理此種變動的工作負載，系統可以執行多個取用者服務執行個體。 不過，這些取用者必須加以協調，以確保每個訊息只會傳遞給單一取用者。 工作負載必須在取用者之間進行負載平衡，以防止某個執行個體變成瓶頸。

## <a name="solution"></a>解決方法

您可以使用訊息佇列來實作應用程式和取用者服務執行個體之間的通訊通道。 應用程式會以訊息形式將要求張貼至佇列，取用者服務執行個體會從佇列接收訊息並加以處理。 這個方法可讓相同取用者服務執行個體的集區得以處理來自應用程式所有執行個體的訊息。 此圖說明使用訊息佇列將工作分配給服務的執行個體。

![使用訊息佇列將工作分配給服務的執行個體](./_images/competing-consumers-diagram.png)

此解決方案有下列優點：

- 它提供負載調節系統，可應付應用程式執行個體傳送要求量的大量變化。 佇列會做為應用程式執行個體與取用者服務執行個體之間的緩衝區。 這可協助將對應用程式和服務執行個體可用性和回應性的影響降到最低，如[佇列型負載調節模式](./queue-based-load-leveling.md)所述。 處理需要長時間處理的訊息，並不妨礙取用者服務的其他執行個體同時處理其他訊息。

- 它可提升可靠性。 如果產生者與取用者直接通訊而不是使用此模式，但不監視取用者，則訊息很可能遺失或因為取用者失敗而無法處理。 在此模式中，訊息不是傳送至特定服務執行個體。 失敗的服務執行個體將不會封鎖產生者，任何工作中的服務執行個體都可以處理訊息。

- 它不需要取用者之間或生產者與取用者執行個體之間的複雜協調。 訊息佇列可確保每個訊息會傳遞至少一次。

- 它是可調整的。 系統可隨著訊息量的波動來動態增加或減少取用者服務的執行個體數。

- 如果訊息佇列提供交易式讀取作業，它可以加強復原功能。 如果取用者服務執行個體在交易式作業過程中讀取並處理訊息，且取用者服務執行個體失敗，則此模式可確保訊息能夠傳回佇列，由取用者服務的其他執行個體接收並處理。

## <a name="issues-and-considerations"></a>問題和考量

當您決定如何實作此模式時，請考慮下列幾點：

- **訊息排序**。 取用者服務執行個體接收訊息的順序並不一定，而且不一定反映建立訊息的順序。 設計系統時，請確保訊息處理為等冪方式，因為這將有助避免對訊息處理順序有依賴性。 如需詳細資訊，請參閱 Jonathon Oliver 部落格上的[等冪模式](https://blog.jonathanoliver.com/idempotency-patterns/) \(英文\)。

    > Microsoft Azure 服務匯流排佇列可使用訊息工作階段，以實作保證先進先出的訊息順序。 如需詳細資訊，請參閱[使用工作階段的傳訊模式](https://msdn.microsoft.com/magazine/jj863132.aspx) \(機器翻譯\)。

- **設計服務以提供復原功能**。 如果系統設計為偵測並重新啟動失敗的服務執行個體，則可能需要實作由服務執行個體以等冪作業來執行處理，以將單一訊息擷取和處理超過一次的效應降至最低。

- **偵測有害訊息**。 訊息格式不正確，或工作需要存取的資源無法使用，可能會導致服務執行個體失敗。 系統應該避免這類訊息傳回佇列，改為擷取這些訊息的詳細資料並儲存到其他位置，以視需要對它們進行分析。

- **處理結果**。 處理訊息的服務執行個體與產生訊息的應用程式邏輯是完全分開的，並可能無法彼此直接通訊。 如果服務執行個體產生的結果必須傳回應用程式邏輯，這項資訊必須儲存在雙方都能存取的位置。 為避免應用程式邏輯擷取不完整的資料，系統必須指出處理已完成。

     > 如果您使用 Azure，背景工作處理序可以使用專用的訊息回覆佇列將結果傳回應用程式邏輯。 應用程式邏輯必須能將這些結果與原始訊息相互關聯。 此案例會在[非同步傳訊入門](https://msdn.microsoft.com/library/dn589781.aspx) \(英文\) 中詳細說明。

- **調整傳訊系統大小**。 在大規模的解決方案中，單一訊息佇列的訊息數目可能會大量湧入，因而成為系統中的瓶頸。 在此情況下，請考慮分割傳訊系統，以從特定的生產者傳送訊息至特定佇列，或使用負載平衡將訊息分散至多個訊息佇列。

- **確保傳訊系統的可靠性**。 您需要可靠的傳訊系統，以保證應用程式將訊息加入佇列之後，將不會遺失訊息。 為確保所有訊息會都傳遞至少一次，這是必要的。

## <a name="when-to-use-this-pattern"></a>使用此模式的時機

使用此模式的時機包括：

- 應用程式的工作負載會分成可非同步執行的數個工作。
- 工作是獨立的，而且可平行執行。
- 工作量具有高變動性，需要可調整的解決方案。
- 解決方案必須提供高可用性，而且必須具有工作處理失敗的復原功能。

此模式可能不適合下列時機︰

- 應用程式工作負載難以分割為不同的工作，或工作之間有高度相依性。
- 工作必須以同步方式執行，且應用程式邏輯必須等候工作完成才能繼續。
- 工作必須依特定順序執行。

> 某些傳訊系統支援工作階段，可讓產生者將訊息加以分組，並可確保它們會由相同的取用者處理。 此機制可搭配使用高優先順序的訊息 (如果支援)，以實作依序將訊息從生產者傳遞至單一取用者的訊息順序形式。

## <a name="example"></a>範例

Azure 提供儲存體佇列和服務匯流排佇列，可作為實作此模式的機制。 應用程式邏輯可以張貼訊息至佇列，而實作為一或多個角色中工作的取用者可以從這個佇列擷取訊息並進行處理。 為提供復原功能，服務匯流排佇列可讓取用者從佇列擷取訊息時使用 `PeekLock` 模式。 此模式不會實際移除訊息，只會針對其他取用者來隱藏訊息。 原始取用者完成處理訊息時，就可以刪除訊息。 如果取用者失敗，查看鎖定將會逾時，訊息將會再次顯示，以允許其他取用者擷取訊息。

如需使用 Azure 服務匯流排佇列的詳細資訊，請參閱[服務匯流排佇列、主題和訂用帳戶](https://msdn.microsoft.com/library/windowsazure/hh367516.aspx)。

如需使用 Azure 儲存體佇列的詳細資訊，請參閱[以 .NET 開始使用 Azure 佇列儲存體](/azure/storage/queues/storage-dotnet-how-to-use-queues)。

[GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/competing-consumers) \(英文\) 提供 CompetingConsumers 解決方案中 `QueueManager` 類別的下列程式碼，其中顯示如何在 Web 或背景工作角色中使用 `Start` 事件處理常式中的 `QueueClient` 執行個體來建立佇列。

```csharp
private string queueName = ...;
private string connectionString = ...;
...

public async Task Start()
{
  // Check if the queue already exists.
  var manager = NamespaceManager.CreateFromConnectionString(this.connectionString);
  if (!manager.QueueExists(this.queueName))
  {
    var queueDescription = new QueueDescription(this.queueName);

    // Set the maximum delivery count for messages in the queue. A message
    // is automatically dead-lettered after this number of deliveries. The
    // default value for dead letter count is 10.
    queueDescription.MaxDeliveryCount = 3;

    await manager.CreateQueueAsync(queueDescription);
  }
  ...

  // Create the queue client. By default the PeekLock method is used.
  this.client = QueueClient.CreateFromConnectionString(
    this.connectionString, this.queueName);
}
```

下一個程式碼片段示範應用程式如何建立和傳送訊息批次至佇列。

```csharp
public async Task SendMessagesAsync()
{
  // Simulate sending a batch of messages to the queue.
  var messages = new List<BrokeredMessage>();

  for (int i = 0; i < 10; i++)
  {
    var message = new BrokeredMessage() { MessageId = Guid.NewGuid().ToString() };
    messages.Add(message);
  }
  await this.client.SendBatchAsync(messages);
}
```

下列程式碼示範取用者服務執行個體如何採用事件驅動方法，以從佇列接收訊息。 `ReceiveMessages` 方法的 `processMessageTask` 參數是一種代理參數，可參照收到訊息時要執行的程式碼。 此程式碼會以非同步方式執行。

```csharp
private ManualResetEvent pauseProcessingEvent;
...

public void ReceiveMessages(Func<BrokeredMessage, Task> processMessageTask)
{
  // Set up the options for the message pump.
  var options = new OnMessageOptions();

  // When AutoComplete is disabled it's necessary to manually
  // complete or abandon the messages and handle any errors.
  options.AutoComplete = false;
  options.MaxConcurrentCalls = 10;
  options.ExceptionReceived += this.OptionsOnExceptionReceived;

  // Use of the Service Bus OnMessage message pump.
  // The OnMessage method must be called once, otherwise an exception will occur.
  this.client.OnMessageAsync(
    async (msg) =>
    {
      // Will block the current thread if Stop is called.
      this.pauseProcessingEvent.WaitOne();

      // Execute processing task here.
      await processMessageTask(msg);
    },
    options);
}
...

private void OptionsOnExceptionReceived(object sender,
  ExceptionReceivedEventArgs exceptionReceivedEventArgs)
{
  ...
}
```

請注意，您可以使用類似 Azure 中提供的自動調整功能，以隨著佇列長度變動來啟動和停止角色執行個體。 如需詳細資訊，請參閱[自動調整指導方針](https://msdn.microsoft.com/library/dn589774.aspx) \(英文\)。 此外，不需要維護角色執行個體與背景工作處理序之間的一對一對應，因為單一角色執行個體可以實作多個背景工作處理序。 如需詳細資訊，請參閱[計算資源彙總模式](./compute-resource-consolidation.md)。

## <a name="related-patterns-and-guidance"></a>相關的模式和指導方針

下列是實作此模式時可能相關的模式和指導方針：

- [非同步傳訊入門](https://msdn.microsoft.com/library/dn589781.aspx)。 訊息佇列是非同步通訊機制。 如果取用者服務需要傳送回覆至應用程式，可能必須實作某種形式的回應訊息。 「非同步傳訊入門」提供如何使用訊息佇列實作要求/回覆訊息的資訊。

- [自動調整指導方針](https://msdn.microsoft.com/library/dn589774.aspx)。 由於佇列應用程式張貼訊息的長度不盡相同，因此可以啟動和停止取用者服務的執行個體。 自動調整有助於維護尖峰處理時間的輸送量。

- [計算資源彙總模式](./compute-resource-consolidation.md)。 將多個取用者服務的執行個體合併成單一處理序，可降低成本和管理額外負荷。 「計算資源彙總」模式描述採用此方法的優缺點。

- [佇列型負載調節模式](./queue-based-load-leveling.md)。 導入訊息佇列可為系統加入復原功能，使服務執行個體可應付應用程式執行個體要求量的大量變化。 訊息佇列是作為調節負載的緩衝區。 「佇列型負載調節模式」對此案例有更詳細的描述。

- 這個模式有相關連的[應用程式範例](https://github.com/mspnp/cloud-design-patterns/tree/master/competing-consumers) \(英文\)。

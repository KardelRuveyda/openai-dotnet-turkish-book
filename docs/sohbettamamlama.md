## Sohbet Tamamlama İşlemleri

OpenAI REST API, chat tamamlama işlemlerini gerçekleştirirken, varsayılan olarak cevabı tam olarak oluşturarak geri döndürür. Ancak, bu durum özellikle uzun yanıtlar için **birkaç saniyelik** beklemelere yol açabilir. Bu sorunu aşmak için, OpenAI API, oluşturulan yanıtın tamamı bitmeden önce parçalar halinde akış ile geri gönderilmesine olanak tanır. Bu sayede, tam yanıt gelmeden önce kısmi sonuçlar işlenebilir.

**Streaming (akış)** kullanarak chat tamamlama işlemi, API'nin sunduğu oldukça etkili bir özelliktir. Bu özellik, özellikle **büyük dil modelleriyle** çalışırken yanıtların hızlı bir şekilde kullanıcıya iletilmesi ve bekleme süresinin azaltılması adına oldukça avantaj sağlar.

## Sohbet Tamamlamada Streaming(Akış) Kullanımı
Standart **CompleteChat** metodunun aksine, akış ile çalışan **CompleteChatStreaming** metodunu kullanarak yanıtlarınızı kısmi sonuçlar şeklinde alabilirsiniz. 

### Senkron Streaming(Akış) Kullanımı
Akışlı bir chat tamamlama işlemi başlatmak için önce **CompleteChatStreaming** metodunu çağırmalısınız. Aşağıdaki örnekte, **“Bu bir testtir.”** ifadesini söylemesi istenen bir akış başlatılmaktadır.

```csharp
CollectionResult<StreamingChatCompletionUpdate> updates
    = client.CompleteChatStreaming("Bu bir testtir.");
```

Bu işlem sonucunda dönen değer, her biri akış sırasında gelen yanıt parçalarını içeren **CollectionResult<StreamingChatCompletionUpdate>** türünde bir nesnedir. Gelen bu kısmi yanıtlar bir döngü içinde işlenebilir.

```csharp
Console.WriteLine($"[ASİSTAN]:");
foreach (StreamingChatCompletionUpdate update in updates)
{
    foreach (ChatMessageContentPart updatePart in update.ContentUpdate)
    {
        Console.Write(updatePart);
    }
}
```

Yukarıdaki kod parçası, asistanın verdiği yanıtları parça parça ekrana yazdırmaktadır. Yanıt tamamen oluşmadan, gelen her bir parçayı anında işleyebilir ve kullanıcıya gösterebilirsiniz.

### Asenkron Streaming(Akış) Kullanımı

Asenkron programlama ile, akışlı bir yanıtı CompleteChatStreamingAsync metodu ile de alabilirsiniz. Bu yaklaşım, özellikle **büyük miktarda veri işlendiğinde** veya **eşzamanlı görevler yürütüldüğünde** performans artışı sağlar. Asenkron kullanımı aşağıdaki gibidir.


```csharp
AsyncCollectionResult<StreamingChatCompletionUpdate> updates
    = client.CompleteChatStreamingAsync("Bu bir testtir.");

Console.WriteLine($"[ASİSTAN]:");
await foreach (StreamingChatCompletionUpdate update in updates)
{
    foreach (ChatMessageContentPart updatePart in update.ContentUpdate)
    {
        Console.Write(updatePart.Text);
    }
}
```
Bu örnekte, await foreach yapısı kullanılarak her bir kısmi yanıt asenkron olarak işlenir ve ekrana yazdırılır. Bu sayede yanıtlar hem asenkron olarak işlenir hem de hızlı bir şekilde kullanıcıya sunulabilir.

## Neden Streaming(Akış) Kullanılmalı?




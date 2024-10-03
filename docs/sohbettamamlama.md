## Sohbet Tamamlama İşlemleri

OpenAI REST API, chat tamamlama işlemlerini gerçekleştirirken, varsayılan olarak cevabı tam olarak oluşturarak geri döndürür. Ancak, bu durum özellikle uzun yanıtlar için **birkaç saniyelik** beklemelere yol açabilir. Bu sorunu aşmak için, OpenAI API, oluşturulan yanıtın tamamı bitmeden önce parçalar halinde akış ile geri gönderilmesine olanak tanır. Bu sayede, tam yanıt gelmeden önce kısmi sonuçlar işlenebilir.

**Streaming (akış)** kullanarak chat tamamlama işlemi, API'nin sunduğu oldukça etkili bir özelliktir. Bu özellik, özellikle **büyük dil modelleriyle** çalışırken yanıtların hızlı bir şekilde kullanıcıya iletilmesi ve bekleme süresinin azaltılması adına oldukça avantaj sağlar.

## Sohbet Tamamlamada Streaming Kullanımı
Standart **CompleteChat** metodunun aksine, akış ile çalışan **CompleteChatStreaming** veya **CompleteChatStreamingAsync** metodunu kullanarak yanıtlarınızı kısmi sonuçlar şeklinde alabilirsiniz. 

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

Yukarıdaki kod parçası, asistanın verdiği yanıtları parça parça ekrana yazdırmaktadır. Yanıt tamamen oluşmadan, gelen her bir parçayı anında işleyebilir ve kullanıcıya gösterebilirsiniz. Her bir **StreamingChatCompletionUpdate** nesnesi içinde bulunan **ContentUpdate** adlı içerik parçalarını döngü ile ekrana yazdıran bir yapıdır; önce **"[ASİSTAN]:"** başlığı yazılır, ardından updates koleksiyonundaki her güncelleme için, içindeki **ChatMessageContentPart** öğeleri sırasıyla ekrana yazdırılır.

### Asenkron Streaming(Akış) Kullanımı

Asenkron programlama ile, akışlı bir yanıtı CompleteChatStreamingAsync metodu ile de alabilirsiniz. Bu yaklaşım, özellikle **büyük miktarda veri işlendiğinde** veya **eşzamanlı görevler yürütüldüğünde** performans artışı sağlar. Asenkron kullanımı aşağıdaki gibidir.


```csharp
AsyncCollectionResult<StreamingChatCompletionUpdate> updates
    = client.CompleteChatStreamingAsync("This is a test.");

Console.WriteLine($"[ASİSTAN]:");
await foreach (StreamingChatCompletionUpdate update in updates)
{
    foreach (ChatMessageContentPart updatePart in update.ContentUpdate)
    {
        Console.Write(updatePart.Text);
    }
}
```
![ezgif-4-a24c6381c2](https://github.com/user-attachments/assets/39ce444d-fb18-4e32-860c-935651baaac1)

Bu örnekte, await foreach yapısı kullanılarak her bir kısmi yanıt asenkron olarak işlenir ve ekrana yazdırılır. Bu sayede yanıtlar hem asenkron olarak işlenir hem de hızlı bir şekilde kullanıcıya sunulabilir. İlk olarak, **CompleteChatStreamingAsync** metodu kullanılarak **"Bu bir testtir."** ifadesiyle bir sohbet tamamlama isteği asenkron olarak gönderilir ve yanıtlar **AsyncCollectionResult<StreamingChatCompletionUpdate>** türünde updates değişkenine atanır. Ardından, **"[ASİSTAN]:"** başlığı ekrana yazdırılır. Bu noktadan sonra, await foreach yapısıyla updates koleksiyonundaki her bir StreamingChatCompletionUpdate nesnesi asenkron olarak işlenir. Her bir güncelleme içinde yer alan ContentUpdate koleksiyonundaki her bir **ChatMessageContentPart** nesnesinin **Text** özelliği, yani yanıtın içeriği, ekrana sırayla yazdırılır. Bu süreç, gelen yanıtları anlık olarak ekrana yazdırarak kullanıcının asistanın yanıtını gerçek zamanlı görmesini sağlar.

## Neden Streaming Kullanılmalı?

**OpenAI API**’nin sunduğu akış (**streaming**) özelliği, yanıtların parça parça (kısmi) gönderilmesi anlamına gelir. Normalde API'den bir istek yapıldığında, yanıtın tamamen işlenip tek seferde döndürülmesi beklenir. Ancak **akış** özelliği ile API, yanıtı tam olarak oluşturmayı beklemeden gelen her bir kısmı anında döndürür. Bu, özellikle uzun veya büyük yanıtlar için oldukça faydalıdır.

Akış mantığını, yazı yazarken satır satır yazdığınız bir metnin, her satırın yazılması tamamlanmadan önce bile ekrana yansıması gibi düşünebilirsiniz. Kullanıcıya, dil modelinin verdiği yanıtlar hızlı bir şekilde sunulmuş olur, böylece yanıtın tamamlanmasını beklemek yerine kullanıcı her bir kısmı hemen görebilir. Bu özellik, özellikle uzun metinler veya büyük dil modelleriyle çalışırken kullanıcı deneyimini iyileştirir ve bekleme süresini azaltır.

Akış, genellikle API’nin asenkron yöntemleriyle kullanılır, yani program yanıtın gelmesini beklerken diğer işlemleri yapmaya devam edebilir. Böylece, performans ve hız avantajı sağlar. Özetle, streaming özelliği, hem hızlı geri dönüş hem de daha akıcı bir kullanıcı deneyimi sunar. Asenkron programlama, bir işlemin sonucunu beklerken diğer işlemleri gerçekleştirmeye olanak tanır. Bu, özellikle büyük dil modelleriyle çalışırken önemli bir avantaj sağlar, çünkü tam yanıtın oluşturulmasını beklemek yerine, gelen veriler anında işlenebilir ve uygulama diğer işlemlerine devam edebilir.

**Asenkron kullanımı** sayesinde, akışlı yanıtlar **await** ve **async** yapılarıyla işlenir. Yani, dil modelinden yanıtlar akış halinde gelirken uygulama yanıtların tamamını beklemeden gelen kısmi sonuçları anında kullanıcıya sunabilir. Bu da daha hızlı ve verimli bir deneyim sağlar. Asenkron kullanımı, **CPU** kaynaklarını daha verimli kullanarak sistem performansını artırır ve aynı anda birden fazla işlemi yönetmeye yardımcı olur.

[Kod örneği için tıklayınız.](https://github.com/KardelRuveyda/openai-dotnet-exercises/tree/master/Examples/02)














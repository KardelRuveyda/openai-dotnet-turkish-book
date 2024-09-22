## Metin Gömlemeleri (Text Embeddings) 

Metin gömlemeleri, metin dizelerinin birbirleriyle ne kadar ilişkili olduğunu ölçmeye yarayan bir tekniktir. Özellikle bir arama sisteminde ya da öneri mekanizmasında kullanışlıdır. Örneğin, bir seyahat planlama web sitesi geliştirmek istiyorsunuz ve kullanıcıların istedikleri otel türünü açıklayarak kendilerine en uygun otel önerilerini almasını sağlamak istiyorsunuz. Bu durumda, otel açıklamalarını metin gömlemesi haline getirip bir vektör veritabanında saklayarak, kullanıcının verdiği açıklamaya göre arama yapabilir ve en uygun otel önerilerini sunabilirsiniz.

## Metin Gömlemelerinin Kullanımı

Metin gömmeleri sürecinde, her bir metin (örneğin bir otel açıklaması ya da kullanıcının aradığı otel tipiyle ilgili yazdığı bir metin) sayısal bir dizi olarak temsil edilir. Bu sayısal temsil, "gömme" (embedding) olarak adlandırılır ve her metin için oluşturulur. Gömmeler, genellikle **uzun sayılar dizisi (vektör)** şeklindedir ve bu vektörler, metinlerin içeriklerini birbirleriyle karşılaştırmamıza olanak tanır. Örneğin, otel açıklamaları için oluşturulan gömmeler, her bir otelin açıklamasındaki kelimelerin anlamını ve bağlamını sayısal olarak ifade eder. Aynı şekilde, kullanıcı bir otel tanımlaması yaptığında (örneğin, "lüks otel, havuzlu ve şehir merkezinde" gibi), bu tanımlamadan da bir gömme oluşturulur. Kullanıcının bu tanımlaması, otel açıklamalarıyla ilişkili olarak bir arama sorgusuna dönüştürülür. Metin gömmelerinin vektör tabanlı yapısı sayesinde, kullanıcı tarafından girilen açıklama ile otel açıklamaları arasında bir kıyaslama yapılır. Gömmeler, aralarındaki benzerliği ölçerek, kullanıcının isteğine en yakın otel açıklamalarını bulmamıza olanak tanır. Bu benzerlik, vektörlerin birbirlerine olan uzaklıklarıyla ölçülür; vektörler ne kadar yakınsa, metinler o kadar ilişkili demektir. Bu süreç, büyük ölçekte otel arama ve öneri sistemleri gibi uygulamalarda kullanıcılara daha kişiselleştirilmiş ve anlamlı sonuçlar sunmak için güçlü bir yöntem sunar. Gömmelerin boyutu, modelin karmaşıklığına göre değişir; daha büyük gömmeler genellikle daha doğru sonuçlar sağlasa da, daha fazla hesaplama kaynağı gerektirebilir. Bu nedenle, gömmenin boyutlarını optimize ederek sistemin performansını dengelemek mümkündür.

Örneğin, **OpenAI**’nın **EmbeddingClient** sınıfı kullanılarak, aşağıdaki gibi bir otel açıklaması için metin gömlemesi oluşturabilirsiniz:

```csharp
using OpenAI.Embeddings;

EmbeddingClient client = new(model: "text-embedding-3-small", Environment.GetEnvironmentVariable("OPENAI_API_KEY"));

string description = "Best hotel in town if you like luxury hotels. They have an amazing infinity pool, a spa,"
    + " and a really helpful concierge. The location is perfect -- right downtown, close to all the tourist"
    + " attractions. We highly recommend this hotel.";

Embedding embedding = client.GenerateEmbedding(description);
ReadOnlyMemory<float> vector = embedding.Vector;
```
Yukarıdaki kod, **"luxury hotel"** ile ilgili açıklamayı alır ve bunun için bir (embedding) oluşturur. **GenerateEmbedding** fonksiyonu, bu metni alıp sayısal bir temsil olan gömleme vektörüne dönüştürür. Bu gömleme, ReadOnlyMemory<float> türünde bir vektör olup, modelin boyutuna bağlı olarak vektörün uzunluğu değişir. Örneğin:
- **text-embedding-3-small** modelinde bu vektörün uzunluğu **1536**,
- **text-embedding-3-large** modelinde ise **3072** olabilir.

Bu sayısal temsil (vektör) metinlerin birbiriyle ne kadar ilişkili olduğunu ölçmek için kullanılır. Örneğin, kullanıcının aradığı otelin tanımı ile veritabanında saklanan otel açıklamaları karşılaştırılır ve en yakın sonuçlar önerilir.

Bu kod parçası, OpenAI’nin metin gömmeleri (text embeddings) kullanarak bir metni sayısal bir vektör formatına dönüştürmek için tasarlanmıştır. Bu vektör, metnin anlamını ve yapısını matematiksel olarak temsil eder.

- OpenAI’nin gömme (embedding) işlemi için gereken **OpenAI.Embeddings** ad alanını kullanıma dahil edilmelidir. Bu ad alanı, metin gömmeleri oluşturmak için gereken sınıfları ve metodları içerir.
- **EmbeddingClient:** Bu sınıf, metinleri vektörlere (gömmeler) dönüştürmek için kullanılır.
- **model: "text-embedding-3-small"**: **text-embedding-3-small** modelini kullanarak gömme oluşturulacağını belirtir. Bu model, her metni **1536** boyutlu bir vektörle temsil eder. Daha büyük model olan **text-embedding-3-large**, her metni **3072** boyutlu bir vektörle temsil eder.
- **GenerateEmbedding**: **EmbeddingClient** sınıfının bir metodudur ve verilen metni bir gömme vektörüne dönüştürür.
- **embedding.Vector:** Gömme işlemi sonucunda üretilen vektörü içerir. Bu vektör, otel açıklamasını temsil eden bir sayılar dizisidir.
- **ReadOnlyMemory<float>:** Vektör, float türünde sayılardan oluşan bir dizidir ve bu dizinin salt okunur (read-only) olması sağlanır. Bu sayede, dışarıdan yapılan müdahalelerle değiştirilmesi önlenir.
- **text-embedding-3-small** modelini kullandığınız için, bu vektör **1536** boyutlu olacaktır. Her float değeri, açıklamanın belirli bir özelliğini temsil eden bir sayısal değeri içerir. Bu vektör, gömmenin bir dijital temsili olduğu için metinler arasındaki benzerlikleri ölçmek için kullanılabilir. Örneğin, iki otel açıklamasının gömme vektörleri arasındaki mesafe, bu iki otelin birbirine ne kadar benzediğini gösterir.
- Daha büyük modeller (örneğin text-embedding-3-large) daha yüksek boyutlu gömmeler (3072) üretir ve bu genellikle daha iyi performans sağlar. Ancak, büyük vektörler daha fazla hesaplama, bellek ve depolama maliyeti gerektirir. Bu yüzden uygulamaya ve ihtiyaçlara göre daha küçük bir model seçmek de mantıklı olabilir.

## Gömleme Boyutlarını Ayarlama

Büyük gömlemeler (örneğin, 3072 boyutundaki vektörler), daha yüksek doğruluk sağlayabilir, ancak aynı zamanda daha fazla hesaplama gücü, bellek ve depolama alanı gerektirir. Eğer daha düşük boyutlu bir gömleme kullanmak isterseniz, EmbeddingGenerationOptions sınıfını kullanarak boyutları ayarlayabilirsiniz:

```csharp
EmbeddingGenerationOptions options = new() { Dimensions = 512 };
Embedding embedding = client.GenerateEmbedding(description, options);
```

Bu örnekte, 512 boyutlu bir gömleme oluşturulmuştur. Bu, hesaplama maliyetlerini azaltırken yine de yeterli performans sağlayabilir.

Metin gömlemeleri, kullanıcıların yazdığı açıklamaların ilişkili içeriklerle eşleşmesini sağlar. Gömlemeler kullanılarak, bir metni sayısal bir forma dönüştürüp, bu sayılar arasındaki ilişkiler ölçülerek en uygun sonuçlar sunulabilir. Özellikle arama ve öneri sistemlerinde bu teknik, metinler arasındaki benzerliği ölçmek için oldukça etkili bir yöntemdir.








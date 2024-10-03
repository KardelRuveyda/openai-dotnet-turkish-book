## Metin Gömmeleri (Text Embeddings) 

Metin embeddingleri, metin dizelerinin birbirleriyle ne kadar ilişkili olduğunu ölçmeye yarayan bir tekniktir. Özellikle bir arama sisteminde ya da öneri mekanizmasında kullanışlıdır. Örneğin, bir seyahat planlama web sitesi geliştirmek istiyorsunuz ve kullanıcıların istedikleri otel türünü açıklayarak kendilerine en uygun otel önerilerini almasını sağlamak istiyorsunuz. Bu durumda, otel açıklamalarını metin embeddingsi haline getirip bir vektör veritabanında saklayarak, kullanıcının verdiği açıklamaya göre arama yapabilir ve en uygun otel önerilerini sunabilirsiniz.

## Metin Gömmelerinin Kullanımı

Metin gömmeleri sürecinde, her bir metin (örneğin bir otel açıklaması ya da kullanıcının aradığı otel tipiyle ilgili yazdığı bir metin) **sayısal bir dizi** olarak temsil edilir. Bu sayısal temsil, **"gömme" (embedding)** olarak adlandırılır ve her metin için oluşturulur. Gömmeler, genellikle **uzun sayılar dizisi (vektör)** şeklindedir ve bu vektörler, metinlerin içeriklerini birbirleriyle karşılaştırmamıza olanak tanır. Örneğin, otel açıklamaları için oluşturulan gömmeler, her bir otelin açıklamasındaki kelimelerin anlamını ve bağlamını **sayısal** olarak ifade eder. Aynı şekilde, kullanıcı bir otel tanımlaması yaptığında (örneğin, "lüks otel, havuzlu ve şehir merkezinde" gibi), bu tanımlamadan da bir **gömme** oluşturulur. Kullanıcının bu tanımlaması, otel açıklamalarıyla ilişkili olarak bir arama sorgusuna dönüştürülür. Metin gömmelerinin vektör tabanlı yapısı sayesinde, kullanıcı tarafından girilen açıklama ile otel açıklamaları arasında bir kıyaslama yapılır. Gömmeler, aralarındaki **benzerliği ölçerek**, kullanıcının isteğine en yakın otel açıklamalarını bulmamıza olanak tanır. Bu benzerlik, vektörlerin birbirlerine olan uzaklıklarıyla ölçülür; **vektörler ne kadar yakınsa**, metinler o kadar ilişkili demektir. Bu süreç, büyük ölçekte otel arama ve öneri sistemleri gibi uygulamalarda kullanıcılara daha kişiselleştirilmiş ve anlamlı sonuçlar sunmak için güçlü bir yöntem sunar. Gömmelerin boyutu, modelin karmaşıklığına göre değişir; daha büyük gömmeler genellikle daha doğru sonuçlar sağlasa da, daha fazla hesaplama kaynağı gerektirebilir. Bu nedenle, gömmenin boyutlarını optimize ederek sistemin performansını dengelemek mümkündür.

Örneğin, **OpenAI**’nın **EmbeddingClient** sınıfı kullanılarak, aşağıdaki gibi bir otel açıklaması için **metin embeddingsi** oluşturabilirsiniz:

```csharp
        private static readonly string apiKey = ConfigReader.ReadApiKeyFromConfig();

        private static readonly string description = "Bu otobüs firmasıyla yaptığım yolculuk oldukça keyifliydi. Koltuklar rahat ve genişti, ayrıca"
            + " personel çok nazikti. İkramlar yeterliydi ve zamanında varış sağlandı. Güvenli bir yolculuk için kesinlikle"
            + " tavsiye ederim.";
```

```csharp
        public static void SimpleEmbedding()
        {
            EmbeddingClient client = new("text-embedding-3-small", apiKey);
            OpenAIEmbedding embedding = client.GenerateEmbedding(description);
            ReadOnlyMemory<float> vector = embedding.ToFloats();

            Console.WriteLine($"Boyut(Dimension): {vector.Length}");
            Console.WriteLine($"Kayan Noktalı Sayılar(Float): ");
            for (int i = 0; i < vector.Length; i++)
            {
                Console.WriteLine($"  [{i,4}] = {vector.Span[i]}");
            }
        }
```

* **SimpleEmbedding()** fonskiyonu ile amaç tek bir metni embedding'e dönüştürmek ve vektörleri konsola yazdırmak.
* **EmbeddingClient** sınıfı ile apiKey kullanılarak bir istemci oluşturulur.
* Sabit bir Türkçe açıklama metni (description) embedding'e tabi tutulur.
* Embedding sonucu olan vektör elde edilir ve her elemanı konsola yazdırılır.
* Text embedding ile yapılmak istenen: Tek bir metnin (bir otobüs yolculuğu incelemesi) vektör halinde makine tarafından anlaşılabilir bir biçime getirilmesi.

```csharp
        public static async Task SimpleEmbeddingAsync()
        {
            EmbeddingClient client = new("text-embedding-3-small", apiKey);

            OpenAIEmbedding embedding = await client.GenerateEmbeddingAsync(description);
            ReadOnlyMemory<float> vector = embedding.ToFloats();

            Console.WriteLine($"Boyut(Dimension): {vector.Length}");
            Console.WriteLine($"Kayan Noktalı Sayılar(Float): ");
            for (int i = 0; i < vector.Length; i++)
            {
                Console.WriteLine($"  [{i,4}] = {vector.Span[i]}");
            }
        }
```

**SimpleEmbeddingAsync** metotu ile asenkron bir işlemle embedding yapılır; bu, uygulamanın performansını artırabilir, çünkü işlemler arka planda çalışır.

```csharp
        public static void EmbeddingWithOptions()
        {
            EmbeddingClient client = new("text-embedding-3-small", apiKey);

            EmbeddingGenerationOptions options = new() { Dimensions = 512 };

            OpenAIEmbedding embedding = client.GenerateEmbedding(description, options);
            ReadOnlyMemory<float> vector = embedding.ToFloats();

            Console.WriteLine($"Boyut: {vector.Length}");
            Console.WriteLine($"Kayan Noktalı Sayılar: ");
            for (int i = 0; i < vector.Length; i++)
            {
                Console.WriteLine($"  [{i,3}] = {vector.Span[i]}");
            }
        }
```

* **EmbeddingWithOptions** fonksiyonu ile amaç tek bir metni embedding'e belirli boyut seçenekleri ile dönüştürmektir.
* **EmbeddingClient** oluşturulur.
* Embedding işlemi için **EmbeddingGenerationOptions** sınıfı kullanılarak boyut (dimension) **512** olarak ayarlanır.
* Verilen metin embedding'e tabi tutulur ve sonuç vektör konsola yazdırılır.
* Text embedding ile yapılmak istenen: Belirli bir boyutta embedding oluşturmak, daha hassas sonuçlar üretmek veya belirli bir uygulama için daha iyi performans elde etmek amacıyla yapılır.

```csharp
        public static async Task EmbeddingWithOptionsAsync()
        {
            EmbeddingClient client = new("text-embedding-3-small", apiKey);
            EmbeddingGenerationOptions options = new() { Dimensions = 512 };

            OpenAIEmbedding embedding = await client.GenerateEmbeddingAsync(description, options);
            ReadOnlyMemory<float> vector = embedding.ToFloats();

            Console.WriteLine($"Boyut: {vector.Length}");
            Console.WriteLine($"Kayan Noktalı Sayılar: ");
            for (int i = 0; i < vector.Length; i++)
            {
                Console.WriteLine($"  [{i,3}] = {vector.Span[i]}");
            }
        }
```

**EmbeddingWithOptionsAsync** fonksiyonu ile yapılmak istenen: Daha hızlı ve belirli boyut seçenekleriyle embedding işlemini asenkron olarak yapmak.

```csharp
        public static void MultipleEmbeddings()
        {
            EmbeddingClient client = new("text-embedding-3-small", apiKey);

            List<string> girdiler = new() { category, description };  // Birden fazla girdi ile liste oluşturma

            // Her iki metin için embedding oluşturur
            OpenAIEmbeddingCollection collection = client.GenerateEmbeddings(girdiler);

            // Her embedding'i ayrı ayrı işler ve sonuçları konsola yazdırır
            foreach (OpenAIEmbedding embedding in collection)
            {
                ReadOnlyMemory<float> vector = embedding.ToFloats();

                Console.WriteLine($"Boyut: {vector.Length}");
                Console.WriteLine("Kayan Noktalı Sayılar: ");
                for (int i = 0; i < vector.Length; i++)
                {
                    Console.WriteLine($"  [{i,4}] = {vector.Span[i]}");
                }

                Console.WriteLine(); // Her embedding'in sonucunu ayırmak için boş satır ekleniyor
            }
        }
```
* **MultipleEmbeddings** fonksiyonu ile amaç birden fazla metni embedding'e dönüştürmektir.
* **EmbeddingClient** oluşturulur.
* Bir kategori ve açıklamadan oluşan iki metin içeren bir **liste (inputs)** oluşturulur.
* Bu metinler topluca embedding işlemine tabi tutulur ve bir **OpenAIEmbeddingCollection** döndürülür.
* Her embedding ayrı ayrı işlenir ve vektör sonuçları konsola yazdırılır.
* Text embedding ile yapılmak istenen: Birden fazla metni aynı anda embedding'e tabi tutarak performansı artırmak ve farklı metinlerin sayısal karşılıklarını elde etmek.

```csharp
        public static async Task MultipleEmbeddingsAsync()
        {
            EmbeddingClient client = new("text-embedding-3-small", apiKey);

            List<string> inputs = new() { category, description };  // Girdi listesini oluşturuyoruz

            // Asenkron olarak embedding'leri topluca oluşturur
            OpenAIEmbeddingCollection collection = await client.GenerateEmbeddingsAsync(inputs);

            // Her embedding'i ayrı ayrı işler ve sonuçları konsola yazdırır
            foreach (OpenAIEmbedding embedding in collection)
            {
                ReadOnlyMemory<float> vector = embedding.ToFloats();

                Console.WriteLine($"Boyut: {vector.Length}");
                Console.WriteLine("Kayan Noktalı Sayılar: ");
                for (int i = 0; i < vector.Length; i++)
                {
                    Console.WriteLine($"  [{i,4}] = {vector.Span[i]}");
                }

                Console.WriteLine(); // Her embedding'in sonucunu ayırmak için boş bir satır
            }
        }
```
**MultipleEmbeddingsAsync** metotu ile yapılmak istenen: Birden fazla metni aynı anda asenkron şekilde embedding'e tabi tutmak, böylece işlem süresini düşürmek ve performansı artırmaktır.

Bu kod, verilen metinlerin embedding'leri ile metinleri makine öğrenimi ve yapay zeka uygulamalarında kullanılabilir hale getiriyor. Her bir **embedding** işlemi, metinleri vektör formuna dönüştürür. Bu vektörler, **benzerlik ölçümleri**, **sınıflandırma** ve diğer NLP uygulamalarında kullanılır.



## Embedding Neden Bu Kadar Önemli?


Embedding, ham verileri (örneğin metinleri) düşük boyutlu, yoğun (dense) ve sürekli değerlerle temsil eden matematiksel bir yöntemdir. Genellikle bu işlemin amacı, insan diliyle ifade edilen karmaşık kavramları makine öğrenimi modelleri tarafından işlenebilecek sayısal bir biçime dönüştürmektir.

Örneğin, bir cümle veya kelimeyi ele alalım: Makine bu kelimeyi anlayamaz, ancak bu kelimenin anlamını bir dizi sayı (vektör) olarak temsil edebilirsek, bu sayısal temsil üzerinden kelimenin ilişkilerini ve anlamını modelleyebiliriz. İşte embedding tam olarak bunu yapar: Metinleri ya da daha genel anlamda ham verileri vektörler halinde temsil eder.

## Embedding Neden Bu Kadar Önemli?

* **Veriyi Sayısal Hale Getirme:** Makine öğrenimi algoritmaları yalnızca sayılar üzerinde işlem yapabilir. Ham verileri, özellikle metin verilerini, sayısal bir formata dönüştürmeden kullanmamız imkansızdır. Embedding, dilsel ya da başka türdeki verileri düşük boyutlu sayısal bir formata dönüştürerek, bu verileri makine öğrenimi modellerinde kullanılabilir hale getirir.
* **Anlamsal İlişkileri Koruma:** Embedding'ler yalnızca sayısal bir temsil sunmaz, aynı zamanda bu temsil anlamsal ilişkileri de korur. Örneğin, "kedi" ve "köpek" gibi iki kelimenin embedding'leri birbirine yakın olacaktır çünkü bu iki kelime anlam bakımından birbirine yakındır. Benzer şekilde, "kral" ve "kraliçe" gibi kelimelerin embedding'leri de benzer bir yapıya sahip olacaktır. Bu anlamsal yakınlık, birçok doğal dil işleme (NLP) ve bilgi çıkarma uygulamasının temelini oluşturur.
* **Boyutun Düşürülmesi ve Verimlilik:** Embedding'ler genellikle düşük boyutlu vektörler olarak kullanılır. Örneğin, bir kelimeyi tek başına bir kelime dağarcığında (vocabulary) temsil etmek için büyük boyutlu bir vektör yerine, embedding sayesinde kelimenin anlamını taşıyan daha küçük boyutlu bir vektör kullanabiliriz. Bu hem bellekte tasarruf sağlar hem de işlem hızını artırır. Bu yöntem özellikle büyük veri setlerinde çok kritiktir.
* **Genelleştirilebilirlik:** Embedding'ler, eğitim sırasında öğrenilen bilgileri genelleştirebilir. Örneğin, bir model, daha önce hiç görmediği bir kelimenin embedding'ini çıkarıp, o kelimenin benzer anlam taşıyan başka kelimelerle olan ilişkilerini kullanarak yeni tahminler yapabilir. Bu, özellikle dil modellerinde önemli bir avantajdır çünkü diller sürekli genişler ve değişir.
* **Makine Öğreniminde Kullanım Kolaylığı:** Embedding'ler sayesinde ham metinler gibi karmaşık veriler doğrudan sınıflandırma, kümeleme, regresyon gibi makine öğrenimi algoritmalarında kullanılabilir. Örneğin:
* **Sınıflandırma:** Bir e-posta spam mi yoksa normal mi? Embedding'lerle e-postaları sayısal bir vektöre dönüştürerek, bu vektörler üzerinden spam olup olmadığını sınıflandırabiliriz.
* **Kümeleme:** Benzer ürünleri bulmak için embedding'leri kullanabiliriz. Bir e-ticaret sitesinde ürünlerin embedding'leri sayesinde, benzer ürünlerin birbirine yakın olduğu kümeleri oluşturabiliriz.







## RAG (Retrieval-Augmented Generation) Temel Kavramları

RAG, dil modelleri (örneğin **GPT-4** gibi) ile bilgiye dayalı yanıtlar üretme sürecini optimize eden bir yöntemdir. Bu yöntemde iki ana aşama vardır:
- **Retrieval (Bilgi Getirme):** Bir bilgi kaynağından (örneğin bir belge seti) en alakalı bilgiyi bulmak.
- **Generation (Üretim):** Bu bilgilere dayanarak anlamlı ve doğru bir yanıt üretmek.
Ancak dil modelleri, özellikle büyük ve yapılandırılmamış veri kümeleri üzerinde çalışırken, her zaman doğru bilgiyi bulamayabilir. Bu yüzden, RAG yaklaşımı bilginin daha etkili ve anlamlı bir şekilde bulunmasını sağlar.

## Dil Modelleri ve Vektörler

Dil modelleri, metinleri anlamak için kelimeleri veya cümleleri vektör adı verilen sayısal temsillere dönüştürür. Bu temsiller, bir cümlenin ya da kelimenin anlamını içerir.

Örneğin:

**"Türkiye’nin Gürcistan ile yaptığı maç ne zaman oynandı?"** sorusu, **vektör** olarak ifade edilir. Bu vektör, sorunun içeriğini ve anlamını **sayılarla** temsil eder.
**"10 Haziran 2024’te oynandı"** cevabı da **başka bir vektör** olarak temsil edilir.
İki vektörün yakınlığı (yani aralarındaki mesafe), bu iki ifadenin anlamca ne kadar benzer olduğunu gösterir. Anlamca yakın ifadelerin vektörleri, matematiksel olarak birbirine daha yakın olacaktır.

## Vektörlerin Amacı Nedir?

Vektörlerin amacı, metinleri anlam düzeyinde temsil etmektir. Yani:

* Aynı anlama gelen iki farklı ifade, vektörlerde birbirine yakın olur. Örneğin:
    * "Türkiye maçı ne zaman oynandı?" ve "Türkiye’nin maçı hangi tarihteydi?" gibi iki farklı cümle, kelimeler farklı olsa bile benzer anlamlar taşır ve vektörlerde yakın olur.
* Kelimelerin sıraları veya küçük farklılıklar, anlamı değiştirmeden vektörler aracılığıyla yakalanır.

Bu yüzden, vektör temelli yaklaşım, metinler arasındaki anlamsal benzerlikleri yakalayarak daha doğru sonuçlar bulmamıza yardımcı olur.

## Vektör Depolama Nedir?

**Vektör Depolama (Vector Store)**, çok sayıda metni (veya diğer veri türlerini) vektörler halinde saklayan bir veri yapısıdır. Vektör depolama şunları yapmamıza olanak tanır:

1- **Verileri vektör olarak saklama:** Her bir metin parçası (örneğin bir cümle, paragraf veya belge), bir vektör olarak temsil edilir ve bu vektörler vektör deposuna kaydedilir.

2- **Sorguları vektör olarak ifade etme:** Kullanıcının soruları da vektör olarak ifade edilir.

3- **En benzer vektörleri bulma:** Vektör deposu, sorgunun vektörünü, depo içindeki vektörlerle karşılaştırarak en yakın (yani anlamca en alakalı) vektörleri bulur.

## Neden Vektör Depolama Kullanıyoruz?

Vektör depolama, RAG’ın bilgi getirme aşamasını güçlendirir ve şunları sağlar:

**a) Anlamsal (Semantik) Arama**
Anahtar kelimelere dayalı klasik metin aramaları, sadece kelimelerin aynısının geçtiği yerleri bulur. Ancak, bu yaklaşım bazen yeterli değildir. Çünkü:

- Aynı anlamı farklı kelimelerle ifade edebiliriz.
- Anlamca alakalı olan verileri bulmak, kelime bazında arama yaparken zor olabilir.

**Vektör depolama**, kelimelerin değil, anlamın arandığı bir sistemdir. Bu, kelimeler farklı olsa bile anlamca en uygun olan yanıtları bulmamıza olanak tanır. Örneğin:

- Kullanıcı sorusu: "Türkiye’nin Gürcistan ile yaptığı maç hangi tarihte oynandı?"
- Veri: "Türkiye, Gürcistan ile 10 Haziran 2024’te oynadı."
Bu iki ifade, kelimeler farklı olsa bile anlamca benzerdir. Vektör depolama sayesinde bu anlamlı benzerlik fark edilir ve doğru sonuç döndürülür.

**b) Verimlilik ve Hız**
Büyük veri kümeleri üzerinde geleneksel metin arama yöntemleriyle çalışmak çok zaman alabilir. Metin arama, her bir kelimeyi tek tek kontrol etmek zorunda olduğu için yavaştır. Ancak vektör depolama, matematiksel olarak vektörler arasındaki mesafeleri hesaplayarak çok daha hızlı bir arama işlemi yapar. Bu, milyonlarca veri parçası arasında bile hızlı sonuçlar almamıza yardımcı olur.

**c) Hassasiyet ve Doğruluk**
Vektör temelli arama sayesinde, anahtar kelimeye değil, anlama odaklanırız. Bu, daha doğru ve daha alakalı sonuçlar bulmamızı sağlar. Çünkü dil modelleri, sadece kelimelerin değil, tüm ifadenin anlamını anlayabilir.

## Vektör Depolama Süreci Detaylı İnceleme

**a) Veriyi Gömme (Embedding)**
Her bir veri parçası (**örneğin Euro 2024 maç sonuçları**), bir **vektör** haline getirilir. Bu, dil modeli tarafından yapılır. Verinin anlamını taşıyan bu vektör, **sayısal** bir dizi halindedir. Vektörler, dil modeli tarafından anlam açısından ilişkilendirilir.

Örnek: **"Türkiye 3-1 Gürcistan"** gibi bir sonuç, bir dizi sayıya (vektör) dönüştürülür. Aynı şekilde, **"Almanya 1-1 Fransa"** gibi başka bir sonuç da başka bir vektörle temsil edilir. Bu vektörler anlamlarına göre sayısal olarak birbirlerine yakın veya uzak olabilir.

**b) Vektörleri Depolamak**
Bu vektörler, bir vektör deposunda saklanır. Vektör deposu, tüm veri setinin vektör temsillerini içerir. Bir veri deposu gibi düşünülebilir, ancak veriler burada sayısal temsiller olarak saklanır. Vektörler daha sonra sorgularla karşılaştırılır.

**c) Sorguyu Gömme**
Kullanıcının sorduğu soru da benzer şekilde bir vektöre dönüştürülür. Yani, kullanıcı "Türkiye’nin Gürcistan ile yaptığı maç ne zaman oynandı?" diye sorduğunda, bu soru da anlamına göre sayısal bir vektör olarak ifade edilir.

**d) En Uygun Vektörleri Bulma**
Sorgunun vektörü, vektör deposundaki vektörlerle karşılaştırılır. Bu karşılaştırma, vektörler arasındaki mesafeyi ölçerek yapılır. En yakın mesafeye sahip vektörler, anlamca en benzer olan verilerdir.

Örnek: "**Türkiye maçı ne zaman oynandı?"** sorusunun vektörü, **"Türkiye 3-1 Gürcistan"** verisinin vektörü ile yakınsa, bu verinin bulunduğu belge ilgili sonuç olarak getirilir.

## Koddaki Vektör Depolama Örneği
```csharp
ToolResources = new()
{
    FileSearch = new()
    {
        NewVectorStores =
        {
            new VectorStoreCreationHelper([matchesFile.Id]),
        }
    }
}

```

- **NewVectorStores**: Yeni bir vektör deposu oluşturuyoruz. Bu, Euro 2024 maç sonuçlarının vektör olarak saklanacağı yer.
- **VectorStoreCreationHelper([matchesFile.Id]):** Yüklediğimiz matchesFile (Euro 2024 maç sonuçları dosyası) bir vektör deposuna dönüştürülüyor. Yani bu dosyadaki her veri, birer vektör haline getiriliyor ve vektör deposunda saklanıyor. Kullanıcı bir soru sorduğunda, bu vektörler arasında en uygun olanlar bulunup cevap olarak döndürülecek.

## Vektör Depolamanın Gerçek Hayattaki Uygulaması

**Büyük veri setleri:** Milyonlarca belge içeren bir veri setinde arama yaparken, klasik metin aramaları yetersiz kalabilir. Ancak vektör depolama ile bu işlemler çok daha hızlı ve anlamlı sonuçlarla yapılabilir.

**Anlam odaklı sorgular:** Anahtar kelimelere dayalı aramaların eksik kaldığı durumlarda, anlam açısından en uygun sonuçları getirmek için vektör depolama kullanılır.

Vektör depolama, RAG sistemlerinde dil modellerinin veri bulma yeteneklerini artırmak için kullanılır. Vektörler, metinlerin sayısal temsilleri olup, anlam bazlı arama yapmamıza olanak tanır. Bu sayede hem hızlı hem de doğru sonuçlar elde edebiliriz. Vektör depolama kullanarak, dil modeline büyük veri kümeleri üzerinde daha doğru sonuçlar üretme imkanı tanırız.

## Asistanları Kullanarak RAG (Bilgi Getirme ile Zenginleştirilmiş Üretim) Nasıl Kullanılır?

Bu örnekte, Euro 2024'e katılan takımların maç sonuçlarını içeren bir JSON belgeniz olduğunu ve bu verileri analiz edebilecek ve hakkında sorulara cevap verebilecek bir asistan oluşturmak istediğinizi varsayalım.Bu amaca ulaşmak için, **OpenAI.Files** ad alanından **GetOpenAIFileClient** ve **OpenAI.Assistants** ad alanından **AssistantClient** kullanılır.


**Önemli: Assistants REST API'si şu anda beta aşamasındadır. Dolayısıyla, detaylar değişebilir ve buna bağlı olarak AssistantClient de [Deneysel] olarak etiketlenmiştir. Bu API'yi kullanmak için, ya proje seviyesinde ya da aşağıda kod içerisinde OPENAI001 uyarısını devre dışı bırakmanız gerekmektedir.**

```csharp
using OpenAI.Assistants;
using OpenAI.Files;

// Assistants API'si beta aşamasında olduğu için deneysel olduğunu kabul ediyoruz ve uyarıyı devre dışı bırakıyoruz.
#pragma warning disable OPENAI001
OpenAIClient openAIClient = new(Environment.GetEnvironmentVariable("OPENAI_API_KEY"));
OpenAIFileClient fileClient = openAIClient.GetOpenAIFileClient();
AssistantClient assistantClient = openAIClient.GetAssistantClient();

```

**JSON belgesinin şu şekilde olabileceğini varsayalım:**
```csharp
using Stream document = BinaryData.FromString("""
{
    "description": "Bu belge, Euro 2024'e katılan takımların maç sonuçlarını içermektedir.",
    "matches": [
        {
            "date": "2024-06-10",
            "team1": "Türkiye",
            "team2": "Gürcistan",
            "score": "3-1"
        },
        {
            "date": "2024-06-12",
            "team1": "Almanya",
            "team2": "Fransa",
            "score": "1-1"
        },
        {
            "date": "2024-06-15",
            "team1": "İspanya",
            "team2": "Portekiz",
            "score": "3-2"
        }
    ]
}
""").ToStream();
```

Bu belgeyi OpenAI sistemine yüklemek için **OpenAIFileClient**'in **UploadFile** metodunu kullanın. Belgeler üzerinde asistanların erişimini sağlamak için **FileUploadPurpose.Assistants** parametresi kullanılmalıdır:


```csharp
OpenAIFile matchesFile = fileClient.UploadFile(
    document,
    "euro2024_results.json",
    FileUploadPurpose.Assistants);
```


## Yeni Bir Asistan Oluşturmak

Yeni bir asistanı, **AssistantCreationOptions** sınıfını kullanarak özelleştirebilirsiniz. Aşağıda örnek ile bir asistan oluşturulmuştur. Asistana Playground’da gösterilecek bir **"isim"**, Maç sonuçlarını işlemek için **FileSearchToolDefinition** ve verileri analiz etmek ve görselleştirmek için **CodeInterpreterToolDefinition** araçları kullanılmıştır.
Asistan araçlarının kullanacağı kaynaklar, burada **VectorStoreCreationHelper** ile otomatik olarak maç dosyasını indeksleyen yeni bir **vektör deposu** oluşturulmuştur.

```csharp
AssistantCreationOptions assistantOptions = new()
{
    Name = "Örnek: Euro 2024 Maç Sonuçları RAG",
    Instructions = "Sen, kullanıcı sorgularına dayanarak Euro 2024 maç verilerini arayan ve analiz eden bir asistansın.",
    Tools =
    {
        new FileSearchToolDefinition(),
        new CodeInterpreterToolDefinition(),
    },
    ToolResources = new()
    {
        FileSearch = new()
        {
            NewVectorStores =
            {
                new VectorStoreCreationHelper([matchesFile.Id]),
            }
        }
    },
};

Assistant assistant = assistantClient.CreateAssistant("gpt-4o", assistantOptions);
```

## Bir İleti Dizisi Oluşturmak

Bir kullanıcı mesajı ile asistana başlamak için yeni bir ileti dizisi oluşturabilir ve bunu çalıştırmak için **CreateThreadAndRun** metodunu kullanabilirsiniz. Aşağıda bir örnek verilmiştir:

```csharp
ThreadCreationOptions threadOptions = new()
{
    InitialMessages = { "Türkiye'nin Gürcistan ile yaptığı maçın sonucu neydi? Bu maçın zaman içindeki performansını grafikte göster." }
};

ThreadRun threadRun = assistantClient.CreateThreadAndRun(assistant.Id, threadOptions);
```

İşlem sırasında asistanın durumunu izlemek için **GetRun** metodunu kullanarak süreci izleyebilirsiniz:

```csharp
do
{
    Thread.Sleep(TimeSpan.FromSeconds(1));
    threadRun = assistantClient.GetRun(threadRun.ThreadId, threadRun.Id);
} while (!threadRun.Status.IsTerminal);

```

Eğer işlem başarılı geçtiyse, işlem durumu **RunStatus.Completed** olacaktır.

## Yanıt Mesajlarını Almak

Son olarak, asistana gönderilen mesajları almak için **GetMessages** metodunu kullanabilirsiniz. Bu yöntem, kullanıcının ilk mesajına yanıt olarak oluşturulan tüm mesajları döndürecektir.

```csharp
CollectionResult<ThreadMessage> messages = assistantClient.GetMessages(threadRun.ThreadId, new MessageCollectionOptions() { Order = MessageCollectionOrder.Ascending });

foreach (ThreadMessage message in messages)
{
    Console.Write($"[{message.Role.ToString().ToUpper()}]: ");
    foreach (MessageContent contentItem in message.Content)
    {
        if (!string.IsNullOrEmpty(contentItem.Text))
        {
            Console.WriteLine($"{contentItem.Text}");
        }
    }
}

```

## Birlikte Uygulama Yapalım!


```csharp
    public static void CreateAndRunEuro2024Assistant()
    {
        // OpenAI istemcilerini başlat
        string apiKey = ConfigReader.ReadApiKeyFromConfig();

        // API anahtarı alınamazsa işlemi sonlandır
        if (string.IsNullOrEmpty(apiKey))
        {
            Console.WriteLine("API key not found in config.json");
            return;
        }

        OpenAIClient openAIClient = new(apiKey);
         OpenAIFileClient fileClient = openAIClient.GetOpenAIFileClient();
        AssistantClient assistantClient = openAIClient.GetAssistantClient();

        // JSON belgesini oluştur ve dosyaya yükle
        using Stream document = BinaryData.FromString("""
        {
            "description": "Bu belge, Euro 2024'e katılan takımların maç sonuçlarını içermektedir.",
            "matches": [
                {
                    "date": "2024-06-10",
                    "team1": "Türkiye",
                    "team2": "Gürcistan",
                    "score": "3-1"
                },
                {
                    "date": "2024-06-12",
                    "team1": "Almanya",
                    "team2": "Fransa",
                    "score": "1-1"
                },
                {
                    "date": "2024-06-15",
                    "team1": "İspanya",
                    "team2": "Portekiz",
                    "score": "3-2"
                }
            ]
        }
        """).ToStream();

        OpenAIFile matchesFile = fileClient.UploadFile(
            document,
            "euro2024_results.json",
            FileUploadPurpose.Assistants);

        // Yardımcı oluşturma seçeneklerini tanımla
        AssistantCreationOptions assistantOptions = new()
        {
            Name = "Euro 2024 Maç Sonuçları Yardımcısı",
            Instructions = "Sen, Euro 2024 maç verileri ile ilgili sorulara cevap veren bir yardımcısın. Grafik ve görsel yerine sadece metin cevapları ver.",
            Tools =
            {
                new FileSearchToolDefinition(),
            },
            ToolResources = new()
            {
                FileSearch = new()
                {
                    NewVectorStores =
                    {
                        new VectorStoreCreationHelper([matchesFile.Id]),
                    }
                }
            },
        };

        // Yardımcıyı oluştur
        Assistant assistant = assistantClient.CreateAssistant("gpt-4o", assistantOptions);

        while (true)
        {
            // Konsoldan soru al
            Console.WriteLine("Sorunuzu girin (Çıkmak için 'exit' yazın):");
            string userQuestion = Console.ReadLine();

            if (userQuestion.ToLower() == "exit")
                break;

            // İleti dizisi oluştur ve çalıştır
            ThreadCreationOptions threadOptions = new()
            {
                InitialMessages = { userQuestion }
            };

            ThreadRun threadRun = assistantClient.CreateThreadAndRun(assistant.Id, threadOptions);

            // Yardımcı tamamlanana kadar bekle
            do
            {
                Thread.Sleep(TimeSpan.FromSeconds(1));
                threadRun = assistantClient.GetRun(threadRun.ThreadId, threadRun.Id);
            } while (!threadRun.Status.IsTerminal);

            // Mesajları al ve ekrana yazdır
            CollectionResult<ThreadMessage> messages = assistantClient.GetMessages(threadRun.ThreadId, new MessageCollectionOptions() { Order = MessageCollectionOrder.Ascending });

            foreach (ThreadMessage message in messages)
            {
                Console.Write($"[{message.Role.ToString().ToUpper()}]: ");
                foreach (MessageContent contentItem in message.Content)
                {
                    if (!string.IsNullOrEmpty(contentItem.Text))
                    {
                        Console.WriteLine($"{contentItem.Text}");
                    }
                }
            }
        }
    }
```

Tüm kodu yukarıdaki gibi paylaşıyorum. Ancak gelin biz detaylı bir şekilde bu kodu ele alalım!

* Bu kod, Euro 2024 maç sonuçlarına dayalı bir bot oluşturmak için yazılmış. Amacı, bir kullanıcıya konsoldan sorduğu sorulara yanıt vermek. Bot, Euro 2024'te hangi takımların hangi maçları oynadığını bilerek sorulara yanıt verebiliyor.
* **OpenAI.Assistants** ve **OpenAI.Files** isimli kütüphaneler (yani kod parçaları) programa ekleniyor. Bu kütüphaneler, **OpenAI (GPT-4)** modelini kullanarak bot oluşturmak ve dosyalarla işlem yapmak için gerekli.
* OpenAI sistemine bağlanabilmek için gerekli bir **"API anahtarı"** alınıyor. Bu anahtar, bir nevi giriş şifresi gibi düşünebilirsiniz. Program, bu anahtarı bir dosyadan (config dosyası) okuyor.Eğer API anahtarı bulunamazsa, program bu mesajı gösterir ve çalışmayı durdurur. Çünkü bu anahtar olmadan OpenAI'ye bağlanamayız.
```csharp
if (string.IsNullOrEmpty(apiKey))
{
    Console.WriteLine("API key not found in config.json");
    return;
}
```

* **OpenAIClient:** OpenAI sunucusuyla iletişim kurmak için kullanılır.
* **OpenAIFileClient:** Dosyaları yüklemek için gerekli araçtır.
* **AssistantClient:** Soruları yanıtlayacak olan yardımcıyı oluşturmak için kullanılır.

```csharp
OpenAIClient openAIClient = new(apiKey);
FileClient fileClient = openAIClient.GetFileClient();
AssistantClient assistantClient = openAIClient.GetAssistantClient();
```

* Euro 2024 maç sonuçları bir **JSON** dosyası olarak yazılıyor. JSON, bilgisayarların verileri okuması için yaygın kullanılan bir format. Burada, hangi takımların hangi tarihlerde oynadığını ve skorları görüyoruz.

```csharp
using Stream document = BinaryData.FromString("""
{
    "description": "Bu belge, Euro 2024'e katılan takımların maç sonuçlarını içermektedir.",
    "matches": [
        {
            "date": "2024-06-10",
            "team1": "Türkiye",
            "team2": "Gürcistan",
            "score": "3-1"
        },
        {
            "date": "2024-06-12",
            "team1": "Almanya",
            "team2": "Fransa",
            "score": "1-1"
        },
        {
            "date": "2024-06-15",
            "team1": "İspanya",
            "team2": "Portekiz",
            "score": "3-2"
        }
    ]
}
""").ToStream();
```
* Aşağıdaki satırda, biraz önce oluşturduğumuz JSON dosyasını OpenAI'ye yükleyerek, botun bu verileri kullanarak sorulara yanıt verebilmesini sağlıyoruz.

```csharp
OpenAIFile matchesFile = fileClient.UploadFile(
    document,
    "euro2024_results.json",
    FileUploadPurpose.Assistants);
```
* Aşağıdaki kısımda, botun nasıl davranacağını belirliyoruz:
  * **Name:** Botun ismi "Euro 2024 Maç Sonuçları Yardımcısı".
  * **Instructions:** Botun nasıl yanıt vereceğiyle ilgili talimatlar. Sadece metin cevapları verecek.
  * **FileSearchToolDefinition:** Botun, dosya içindeki maç sonuçlarını bulup kullanmasını sağlayan bir araç.

```csharp
AssistantCreationOptions assistantOptions = new()
{
    Name = "Euro 2024 Maç Sonuçları Yardımcısı",
    Instructions = "Sen, Euro 2024 maç verileri ile ilgili sorulara cevap veren bir yardımcısın. Grafik ve görsel yerine sadece metin cevapları ver.",
    Tools =
    {
        new FileSearchToolDefinition(),
    },
    ToolResources = new()
    {
        FileSearch = new()
        {
            NewVectorStores =
            {
                new VectorStoreCreationHelper([matchesFile.Id]),
            }
        }
    },
};

```
* Bu satırda bot (yardımcı) yaratılır ve Euro 2024 verilerini kullanmaya hazır hale getirilir.

```csharp
Assistant assistant = assistantClient.CreateAssistant("gpt-4o", assistantOptions);
```
* Aşağıdaki döngü,pogramın sonsuz bir döngü içinde çalışmasını sağlar. Kullanıcı bir soru sorar, bot cevaplar ve bu döngü sürekli devam eder.
Döngü, kullanıcı "**exit**" yazana kadar devam eder, bu da programın kapanmasını sağlar.

```csharp
while (true)
{
    Console.WriteLine("Sorunuzu girin (Çıkmak için 'exit' yazın):");
    string userQuestion = Console.ReadLine();
    
    if (userQuestion.ToLower() == "exit")
        break;
```
* Aşağıdaki adımda, kullanıcının sorusu OpenAI'ye gönderilir ve bot, Euro 2024 verilerini kullanarak yanıt üretir.
* **ThreadCreationOptions** nesnesi oluşturulur ve kullanıcının sorduğu soru (**userQuestion**), yardımcıya iletilecek ilk mesaj olarak ayarlanır.
* **InitialMessages = { userQuestion }:** Kullanıcı tarafından girilen soru, bu yardımcıya iletilmek üzere kaydedilir.
* **CreateThreadAndRun** fonksiyonu, soruyu OpenAI'ye gönderir ve işlem başlamış olur.
```csharp
ThreadCreationOptions threadOptions = new()
{
    InitialMessages = { userQuestion }
};

ThreadRun threadRun = assistantClient.CreateThreadAndRun(assistant.Id, threadOptions);
```

* Aşağıdaki kısım, botun cevabı hazır olana kadar beklememizi sağlar.
* **Döngü**: Yardımcının cevabını hazır hale getirmesi biraz zaman alabilir, bu yüzden bu döngü botun cevabı işleyip bitirmesini bekler.
* **Thread.Sleep(TimeSpan.FromSeconds(1)):** Her saniyede bir program durur ve botun cevabını kontrol eder.
* **GetRun**: Botun cevabı tamamlanana kadar (status "**Terminal**" olana kadar) tekrar tekrar kontrol eder.
* Bu sayede, botun cevabının ne zaman hazır olduğunu öğrenmiş oluruz.

```csharp
do
{
    Thread.Sleep(TimeSpan.FromSeconds(1));
    threadRun = assistantClient.GetRun(threadRun.ThreadId, threadRun.Id);
} while (!threadRun.Status.IsTerminal);
```
* Son adımda, botun verdiği yanıtlar ekrana yazdırılır. Cevaplar, botun kullanıcıya verdiği metinlerden oluşur.
* **GetMessages**: Bu fonksiyon, botun verdiği yanıtları alır. Bu yanıtlar, **messages** adlı değişkene kaydedilir.
* **ThreadMessage**: Botun cevabı, mesajlar halinde gelir ve bu mesajları tek tek işleriz.
* **foreach Döngüsü:** Bu döngü, botun verdiği her mesajı tek tek işler. Mesajlar birden fazla olabilir, çünkü botun yanıtı birkaç cümleden oluşabilir.
* **message.Role:** Bu kısımda, mesajı kimin yazdığını gösterir (bot veya kullanıcı).
* **message.Content**: Mesajın içeriğini, yani metin bilgisini okuruz.
* **Console.WriteLine($"{contentItem.Text}"):** Mesajdaki metni ekrana yazdırırız.

```csharp
CollectionResult<ThreadMessage> messages = assistantClient.GetMessages(threadRun.ThreadId, new MessageCollectionOptions() { Order = MessageCollectionOrder.Ascending });

foreach (ThreadMessage message in messages)
{
    Console.Write($"[{message.Role.ToString().ToUpper()}]: ");
    foreach (MessageContent contentItem in message.Content)
    {
        if (!string.IsNullOrEmpty(contentItem.Text))
        {
            Console.WriteLine($"{contentItem.Text}");
        }
    }
}
```

**Özetle;**

- **Döngü başlar:** Kullanıcıdan soru alırız.
- **Çıkış kontrol edilir:** Eğer kullanıcı "exit" yazarsa program biter.
- **Soruyu yardımcıya göndeririz:** Kullanıcı soruyu girince, bu soru yardımcıya gönderilir.
- **Cevabı bekleriz:** Yardımcı cevabını hazırlayana kadar program bekler.
- **Cevabı alırız:** Yardımcı botun verdiği yanıtı alır ve ekrana yazarız.
- **Döngü devam eder:** Bir sonraki soruya geçeriz veya çıkış komutu verilene kadar program çalışmaya devam eder.

**Çıktı:**

![image](https://github.com/user-attachments/assets/aa063e0d-dfb5-4ec2-94c1-c6fdbdfd9b26)

[Kodu incelemek için buraya tıklayabilirsiniz](https://github.com/KardelRuveyda/openai-dotnet-exercises/tree/master/Examples/08)


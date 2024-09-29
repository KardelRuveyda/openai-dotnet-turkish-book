## Asistanları Kullanarak RAG (Bilgi Getirme ile Zenginleştirilmiş Üretim) Nasıl Kullanılır?

Bu örnekte, Euro 2024'e katılan takımların maç sonuçlarını içeren bir JSON belgeniz olduğunu ve bu verileri analiz edebilecek ve hakkında sorulara cevap verebilecek bir asistan oluşturmak istediğinizi varsayalım.Bu amaca ulaşmak için, **OpenAI.Files** ad alanından **FileClient** ve **OpenAI.Assistants** ad alanından **AssistantClient** kullanılır.


**Önemli: Assistants REST API'si şu anda beta aşamasındadır. Dolayısıyla, detaylar değişebilir ve buna bağlı olarak AssistantClient de [Deneysel] olarak etiketlenmiştir. Bu API'yi kullanmak için, ya proje seviyesinde ya da aşağıda kod içerisinde OPENAI001 uyarısını devre dışı bırakmanız gerekmektedir.**

```csharp
using OpenAI.Assistants;
using OpenAI.Files;

// Assistants API'si beta aşamasında olduğu için deneysel olduğunu kabul ediyoruz ve uyarıyı devre dışı bırakıyoruz.
#pragma warning disable OPENAI001
OpenAIClient openAIClient = new(Environment.GetEnvironmentVariable("OPENAI_API_KEY"));
FileClient fileClient = openAIClient.GetFileClient();
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

Bu belgeyi OpenAI sistemine yüklemek için **FileClient**'in **UploadFile** metodunu kullanın. Belgeler üzerinde asistanların erişimini sağlamak için **FileUploadPurpose.Assistants** parametresi kullanılmalıdır:


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
        FileClient fileClient = openAIClient.GetFileClient();
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

Tüm kodu aşağıdaki gibi paylaşıyorum. Ancak gelin biz detaylı bir şekilde bu kodu ele alalım!

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
* **OpenAIClient:** OpenAI sunucusuyla iletişim kurmak için kullanılır. **FileClient:** Dosyaları yüklemek için gerekli araçtır. **AssistantClient:** Soruları yanıtlayacak olan yardımcıyı oluşturmak için kullanılır.

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

- Döngü başlar: Kullanıcıdan soru alırız.
- Çıkış kontrol edilir: Eğer kullanıcı "exit" yazarsa program biter.
- Soruyu yardımcıya göndeririz: Kullanıcı soruyu girince, bu soru yardımcıya gönderilir.
- Cevabı bekleriz: Yardımcı cevabını hazırlayana kadar program bekler.
- Cevabı alırız: Yardımcı botun verdiği yanıtı alır ve ekrana yazarız.
- Döngü devam eder: Bir sonraki soruya geçeriz veya çıkış komutu verilene kadar program çalışmaya devam eder.

**Çıktı:**

![image](https://github.com/user-attachments/assets/8642f706-9be1-40d0-8aa7-31845270fa6a)

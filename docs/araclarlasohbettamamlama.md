## Araçlar ve işlev çağrısıyla sohbet tamamlamaları nasıl kullanılır?

Araçlar ve işlev çağırma yeteneği, OpenAI modelleri ile daha dinamik ve interaktif bir etkileşim kurmanıza olanak tanır. Bu yetenek sayesinde, bir dil modeli (örneğin **gpt-4o**) kullanıcıdan gelen isteklere yanıt verirken, ilgili bilgileri toplamak veya belirli işlemleri gerçekleştirmek için harici işlevler çağırabilir. Bu, modele ek bilgi veya yetenekler eklemek gibi düşünülebilir. Örneğin, hava durumu gibi bilgileri almak ya da bir hesaplama yapmak için fonksiyon çağrılarında bulunmak buna örnek olarak verilebilir.


## Adım 1: Fonksiyonları Tanımlama
 İlk adımda, modelin kullanabileceği belirli fonksiyonlar veya araçlar tanımlanır. Bu fonksiyonlar, modele ek bilgi sağlamak veya belirli işlemleri yürütmek için kullanılabilir. Örneğin, bir hava durumu bilgisi almak için bir API çağıran bir fonksiyon olabilir. Bu fonksiyonlar, modelin çalıştığı ortamda mevcut olmalıdır.
 
```csharp
private static string GetCurrentLocation()
{
    // Normalde burada konum API'si çağrılır, biz İstanbul'u sabit olarak döndüreceğiz.
    return "İstanbul";
}

private static string GetCurrentWeather(string location, string unit = "celsius")
{
    // Hava durumu API'si çağrısı yapılır. Örneğin, "Bugün İstanbul'da hava 25 derece."
    return $"25 {unit} ve İstanbul'da açık bir hava var.";
}
```

Bu iki fonksiyon, kullanıcının bulunduğu konumu **İstanbul** olarak sabitler ve API'den dönecek veriyi simüle eder. Gerçek bir entegrasyonda, burada bir hava durumu API'si kullanılarak o anki hava durumu bilgileri döndürülebilir.

## Adım 2: Araçları Tanımlama

Bu fonksiyonları, OpenAI chat tamamlama sisteminde kullanılabilir hale getirmek için araçlar olarak tanımlamamız gerekiyor. **ChatTool.CreateFunctionTool** metodunu kullanarak fonksiyonlarımızı araçlar olarak tanımlıyoruz.

```csharp
private static readonly ChatTool getCurrentLocationTool = ChatTool.CreateFunctionTool(
    functionName: nameof(GetCurrentLocation),
    functionDescription: "Kullanıcının mevcut konumunu alır ve bu örnekte İstanbul olarak sabitler."
);

private static readonly ChatTool getCurrentWeatherTool = ChatTool.CreateFunctionTool(
    functionName: nameof(GetCurrentWeather),
    functionDescription: "Belirli bir konum için hava durumu bilgisini alır. İstanbul varsayılan konumdur.",
    functionParameters: BinaryData.FromString("""
        {
            "type": "object",
            "properties": {
                "location": {
                    "type": "string",
                    "description": "Konum bilgisi, örn. İstanbul"
                },
                "unit": {
                    "type": "string",
                    "enum": [ "celsius", "fahrenheit" ],
                    "description": "Kullanılacak sıcaklık birimi, varsayılan 'celsius'."
                }
            },
            "required": [ "location" ]
        }
        """)
);
```

Yukarıdaki kodda, **iki farklı araç (tool)** tanımlanmıştır ve bu araçlar **OpenAI** dil modelinin belirli işlevleri çağırarak yanıt vermesine olanak tanır. İlk araç olan **getCurrentLocationTool**, **GetCurrentLocation** isimli bir fonksiyona bağlanır ve kullanıcıdan mevcut konumunu almayı amaçlar; burada konum sabit olarak **"İstanbul"** olarak belirlenmiştir. İkinci araç ise **getCurrentWeatherTool**, **GetCurrentWeather** fonksiyonuna bağlanır ve belirli bir konum için hava durumu bilgisini alır. Bu fonksiyon, JSON şemasına göre parametreler alır: **"location" (zorunlu)**, yani hangi konumun hava durumunun sorulacağı ve **"unit"** (isteğe bağlı), yani sıcaklık biriminin **"celsius"** ya da **"fahrenheit"** olacağı. Varsayılan birim **"celsius"** olarak belirlenmiştir. Bu şekilde, **OpenAI** modeli, hava durumu bilgisi almak için doğru parametreleri kullanarak ilgili fonksiyonları çağırabilir.

## Adım 3: Chat Tamamlayıcı Seçeneklerini Oluşturma

Bu araçları bir ChatCompletionOptions nesnesine ekleyerek, modelin bunları kullanarak yanıt oluşturmasına olanak tanıyoruz. Kullanıcının hava durumu sorması gibi bir durumda, model bu araçları çağırarak gerekli veriyi elde edebilir.
```csharp
List<ChatMessage> messages = [
    new UserChatMessage("İstanbul'da bugün hava nasıl?")
];

ChatCompletionOptions options = new()
{
    Tools = { getCurrentLocationTool, getCurrentWeatherTool },
};

```
Bu kodda, İstanbul'daki hava durumu hakkında soru sormasını sağlayan bir chat (sohbet) mesajını ve OpenAI API'si için gerekli ayarları yapılandırır. **messages** listesi, kullanıcının sorusunu içeren bir **UserChatMessage** nesnesi oluşturur; bu mesaj **"İstanbul'da bugün hava nasıl?"** şeklindedir. Ardından, **ChatCompletionOptions** sınıfından yeni bir options nesnesi oluşturulur ve **Tools** özelliği kullanılarak iki fonksiyon (araç) bu seçeneklere eklenir: **getCurrentLocationTool** ve **getCurrentWeatherTool**. Bu araçlar, modelin kullanıcının konumunu belirlemek ve belirtilen konuma göre hava durumu bilgisini almak için gerekli işlevleri çağırmasını sağlar. Sonuç olarak, model bu araçlar üzerinden **İstanbul'un hava durumunu almak için** fonksiyonları kullanarak yanıt verebilir.

## Adım 4: Fonksiyon Çağrısı ve Yanıt Döngüsü
Modelin yanıt dönerken araç çağırıp çağırmadığını kontrol etmek için bir döngü kuruyoruz. Model eğer fonksiyon çağırmaya karar verirse, ilgili işlevleri çağırır ve sonuçları sohbet geçmişine ekleriz. Bu işlem, modelin doğru ve detaylı yanıt verebilmesi için fonksiyonların kullanımını sağlar.

```csharp
bool requiresAction;

do
{
    requiresAction = false;
    ChatCompletion chatCompletion = client.CompleteChat(messages, options);

    switch (chatCompletion.FinishReason)
    {
        case ChatFinishReason.Stop:
            {
                messages.Add(new AssistantChatMessage(chatCompletion));
                break;
            }

        case ChatFinishReason.ToolCalls:
            {
                messages.Add(new AssistantChatMessage(chatCompletion));

                foreach (ChatToolCall toolCall in chatCompletion.ToolCalls)
                {
                    switch (toolCall.FunctionName)
                    {
                        case nameof(GetCurrentLocation):
                            {
                                string toolResult = GetCurrentLocation();
                                messages.Add(new ToolChatMessage(toolCall.Id, toolResult));
                                break;
                            }

                        case nameof(GetCurrentWeather):
                            {
                                using JsonDocument argumentsJson = JsonDocument.Parse(toolCall.FunctionArguments);
                                bool hasLocation = argumentsJson.RootElement.TryGetProperty("location", out JsonElement location);
                                bool hasUnit = argumentsJson.RootElement.TryGetProperty("unit", out JsonElement unit);

                                if (!hasLocation)
                                {
                                    throw new ArgumentNullException(nameof(location), "Konum bilgisi zorunludur.");
                                }

                                string toolResult = hasUnit
                                    ? GetCurrentWeather(location.GetString(), unit.GetString())
                                    : GetCurrentWeather(location.GetString());
                                messages.Add(new ToolChatMessage(toolCall.Id, toolResult));
                                break;
                            }
                    }
                }

                requiresAction = true;
                break;
            }

        default:
            throw new NotImplementedException(chatCompletion.FinishReason.ToString());
    }
} while (requiresAction);
```

Bu döngüde, modelin döndürdüğü **FinishReason** (bitirme nedeni) kontrol edilir. Eğer model bir fonksiyon çağırması gerektiğine karar verdiyse, ilgili fonksiyonu çağırarak sonucu elde eder ve sohbet geçmişine ekleriz. Mesela, model ilk olarak konumu almak için **GetCurrentLocation** fonksiyonunu çağırabilir. Daha sonra hava durumu için **GetCurrentWeather** fonksiyonunu kullanır.

* Kullanıcı İstanbul'daki hava durumunu sormaktadır. Model, yanıt verebilmek için kullanıcının konumunu ve hava durumunu bilmelidir.
* Model, fonksiyonları kullanarak kullanıcının konumunu ve hava durumu bilgilerini API üzerinden çağırır. Burada sabit bir fonksiyon olarak İstanbul belirlenmiştir.
* **GetCurrentLocation** ile kullanıcıya ait konum bilgisi alınır. Bu örnekte konum doğrudan **"İstanbul"** olarak döndürülür. Ardından **GetCurrentWeather** fonksiyonu çalıştırılarak İstanbul'daki mevcut hava durumu bilgisi alınır.
* Bu süreç tamamlandığında, model elde edilen sonuçlarla kullanıcıya İstanbul'daki hava durumu hakkında detaylı bir yanıt döner. Örneğin, **“Bugün İstanbul’da hava 25 derece ve açık”** şeklinde bir yanıt alınabilir.

Bu örnek, Türkiye'de, İstanbul’a özel bir hava durumu sorgulama senaryosu oluşturarak, OpenAI modellerinin işlev çağırma yeteneklerini nasıl kullanabileceğinizi gösterir. Araçlar ve fonksiyonlar doğru şekilde tanımlandığında, model kullanıcıların ihtiyaç duyduğu bilgileri dinamik olarak sağlayabilir. Bu yapı, API entegrasyonları ile güçlendirilerek gerçek zamanlı veri elde etme ve yanıt üretme süreçlerini optimize eder.

## Adım 5 Yaptıklarımızı Konsol Uygulmasına Dönüştürelim!

### Run() fonksiyonu oluşturalım!

```csharp
public static void Run()
{
    Console.WriteLine("Sohbet başlatılıyor...");

```
Run() adında bir fonksiyon oluşturalım. Bu fonksiyon çalıştığında ekrana "Sohbet başlatılıyor..." mesajını yazdırarak işlemlerin başladığını belirtmesini sağlayalım.

### Kullanıcıdan Veri Alma
Konsolda, kullanıcının İstanbul'da hava durumu hakkında soru sorabileceği belirtelim. Ardından **Console.ReadLine()** ile kullanıcının klavyeden girdiği metin **userInput** değişkenine atanmasını sağlayalım.

```csharp
// Kullanıcıdan girdi alınması
Console.WriteLine("Kullanıcı: İstanbul'da hava nasıl diye sorabilir.");
string userInput = Console.ReadLine();
```
### Kullanıcı Mesajını Listeye Ekleme

* **messages** adında bir liste oluşturalım. Bu liste, kullanıcı ve asistan arasındaki konuşmaları saklamak için kullanılan bir yapıdır.
*  Kullanıcının girdiği metin (yani userInput) bir **UserChatMessage** nesnesine dönüştürülsün ve messages listesine eklensin.

```csharp
// Kullanıcı mesajını ekliyoruz
List<ChatMessage> messages = new()
{
    new UserChatMessage(userInput)  // Kullanıcıdan gelen mesaj
};
```

### Araçların Tanımlanması

* Hatırlarsanız **GetCurrentLocation** ve **GetCurrentWeather** adında araçlar tanımlamıştık. Bunları tanımlayabilmek için öncelikle bunların fonksiyonlarını tanımlamamız lazım. Bu alandaki çalışmalarda siz bu hava durumu verilerini API yoluyla da getirebiirsiniz. Ancak burada basit bir örnek özelinde çalışıldığı için manuel eklenmiştir.

```csharp
private static string GetCurrentLocation()
{
   // Normalde burada konum API'si çağrılır, biz İstanbul'u sabit olarak döndüreceğiz.
  return "İstanbul";
}


// Kullanıcı mesajını ekliyoruz
private static string GetCurrentWeather(string location, string unit = "celsius")
{
   // Hava durumu API'si çağrısı yapılır. Örneğin, "Bugün İstanbul'da hava 25 derece."
   return $"25 {unit} ve İstanbul'da açık bir hava var.";    
}
```


```csharp
// Hangi araçların kullanılacağını belirleyen seçenekler
ChatCompletionOptions options = new()
{
    Tools = { getCurrentLocationTool, getCurrentWeatherTool }  // Araçları tanımlıyoruz
};

```

### API Anahtarının Okunması

Uygulama, bir sunucuya bağlanmak için gerekli olan API anahtarını ConfigReader sınıfından alması için aşağıdaki kodu kullanabilirsiniz. Hassas bilgi içerdiği için buradaki anahtarı Github ortamına veya farklı bir ortama göndermeden önce sakladığınızdan emin olmayı unutmayınız.
 
```csharp
    public static string ReadApiKeyFromConfig()
    {
        try
        {
            string configPath = Path.Combine(Environment.CurrentDirectory, "config.json");
            JObject config = JObject.Parse(File.ReadAllText(configPath));
            return config["OpenAI"]["ApiKey"].ToString();
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Error reading API key from config file: {ex.Message}");
            return null;
        }
    }
```

Bu kod parçası, bir JSON dosyasından OpenAI API anahtarını okuyan bir fonksiyondur. **ReadApiKeyFromConfig** metodu, çalışma dizinindeki **config.json** dosyasını bulmak için **Path.Combine** kullanır. Daha sonra dosya içeriği okunur ve **JObject.Parse** ile JSON formatına dönüştürülür. JSON'dan **"OpenAI"** nesnesinin **"ApiKey"** alanı alınarak ToString() ile string formatına çevrilip döndürülür. Eğer herhangi bir hata oluşursa (örneğin dosya okunamazsa), yakalanan hata mesajı konsola yazdırılır ve null döndürülür.

```csharp

// API anahtarını config dosyasından okuma işlemi
Console.WriteLine("API anahtarı okunuyor...");
string apiKey = ConfigReader.ReadApiKeyFromConfig();

if (string.IsNullOrEmpty(apiKey))
{
    Console.WriteLine("Hata: API anahtarı bulunamadı.");
    return;
}
```
### ChatClient Oluşturma

OpenAI'nin **gpt-4o** modelini kullanan bir **ChatClient** oluşturuldu. Bu client, kullanıcının sorusunu yanıtlamak için kullanılmıştır.

```csharp
// OpenAI ChatClient oluşturuluyor
Console.WriteLine("ChatClient oluşturuluyor...");
ChatClient client = new(model: "gpt-4o", apiKey);
```
Bu kod parçası, OpenAI'nin **gpt-4o** modelini kullanarak bir ChatClient nesnesi oluşturur. İlk satırda, konsola "ChatClient oluşturuluyor..." mesajı yazdırılarak bu işlemin başladığı belirtilir. Ardından, apiKey değişkeni ile belirtilen API anahtarı kullanılarak, **gpt-4o** modelini temsil eden bir ChatClient nesnesi oluşturulur. Bu ChatClient, kullanıcıdan alınan mesajları işleyip OpenAI API'sine göndermek ve yanıtları almak için kullanılır.

### Döngü ile Model Yanıtını İşleme

 **requiresAction** adında bir değişken tanımlandı ve bu döngü, model araç çağırdığı sürece devam edecek. **client.CompleteChat** fonksiyonu kullanılarak, mesajlar ve araçlar kullanılarak bir yanıt isteniyor. Bu yanıt **chatCompletion** değişkeninde saklanmıştır.

```csharp
bool requiresAction;

do
{
    requiresAction = false;
    ChatCompletion chatCompletion = client.CompleteChat(messages, options);
```

Bu kod parçası, bir döngü içinde modelle etkileşim kurarak yanıtın tamamlanıp tamamlanmadığını kontrol eder. **requiresAction** adlı bir değişken başlatılır ve döngü her adımda çalıştırılır. Başlangıçta **requiresAction** değeri false olarak ayarlanır. Ardından, **client.CompleteChat** metodu çağrılır ve bu metodun döndürdüğü **chatCompletion** nesnesi kullanılarak, modelin verdiği yanıt ve olası araç çağrıları işlenir. Döngü, belirli durumlar altında devam edecek şekilde ayarlanmıştır, yani bir işlem veya araç çağrısı gerekiyorsa requiresAction tekrar true yapılabilir ve döngü tekrar çalışabilir.

### Modelin Yanıtına Göre İşlemler
Eğer **FinishReason.Stop** dönerse, yani model yanıtını tamamladıysa, asistanın mesajı messages listesine eklenir. Sonra yanıt JSON formatında yazdırılır.Çünkü ondan önce Asistan mesajını eklediğimiz takdirde sürekli olaran content null gelecektir. **Stop** durumundayken asistan mesajını **son** olarak eklemek gerekmektedir. 


```csharp
switch (chatCompletion.FinishReason)
{
    case ChatFinishReason.Stop:
    {
        messages.Add(new AssistantChatMessage(chatCompletion));
        string jsonResponse = JsonSerializer.Serialize(chatCompletion);
        Console.WriteLine("Model Yanıtı: " + jsonResponse);
        break;
    }
```
Bu kod, modelin yanıtını neden sonlandırdığını anlamak için **chatCompletion.FinishReason** değerini kontrol eden bir switch ifadesidir. Eğer **FinishReason.Stop** durumu gerçekleşmişse, yani model yanıtını başarıyla tamamlamışsa, ilk olarak **chatCompletion** nesnesindeki yanıt bir **AssistantChatMessage** olarak **messages** listesine eklenir. Sonrasında, modelin yanıtı JSON formatına dönüştürülerek **jsonResponse** değişkenine atanır ve bu yanıt konsola yazdırılır. Bu yapı, modelin tam bir yanıt verdiği durumları işler ve yanıtın detaylarını kullanıcıya gösterir.

### Araç Çağrısı Yapıldığında

Bu kod parçası, modelin yanıt verebilmek için araç çağrısı yapması gerektiğinde çalışır. **ChatFinishReason.ToolCalls** durumu, modelin yanıtının tamamlanabilmesi için harici bir araçtan (örneğin, bir API'den) bilgiye ihtiyaç duyduğunu ifade eder.

* **case ChatFinishReason.ToolCalls**: Bu blok, modelin araç çağrısı yapma gereksinimi olduğu durumlarda çalışır. Yani model, bir bilgiyi yanıtlayabilmek için belirli bir aracı kullanması gerektiğini belirttiğinde bu kısım devreye girer.
* **messages.Add(new AssistantChatMessage(chatCompletion))**: Asistanın o ana kadar verdiği yanıt (araç çağrısı yapmak için durdurulmuş olsa bile) messages listesine eklenir. Bu, modelin araç çağrısından önceki durumu temsil eder.
* **foreach (ChatToolCall toolCall in chatCompletion.ToolCalls)**: Bu döngü, **ToolCalls** adı verilen ve modelin çağırdığı tüm araçları içerir. Model birden fazla araç çağrısı yapmış olabilir, bu yüzden her araç çağrısı sırayla işlenir.
* **case nameof(GetCurrentLocation):** Eğer model kullanıcının mevcut konumunu almak için GetCurrentLocation aracını çağırmışsa, bu blok çalışır.
* **string toolResult = GetCurrentLocation();: GetCurrentLocation()** fonksiyonu çağrılır ve kullanıcının konumu **toolResult** değişkenine atanır.
* **messages.Add(new ToolChatMessage(toolCall.Id, toolResult));**: Konum bilgisi (toolResult), **ToolChatMessage** olarak mesaj listesine eklenir. Bu mesaj, araçtan gelen yanıtı temsil eder.
* **case nameof(GetCurrentWeather):** Eğer model hava durumu almak için GetCurrentWeather aracını çağırmışsa, bu blok çalışır.
* **JsonDocument argumentsJson = JsonDocument.Parse(toolCall.FunctionArguments);**: Model, aracı çağırırken bazı parametreler (örneğin, konum bilgisi) iletir. Bu parametreler JSON formatındadır ve burada bu parametreler ayrıştırılır.
* **bool hasLocation = argumentsJson.RootElement.TryGetProperty("location", out JsonElement location);**: JSON içinden location (konum) değeri aranır ve eğer varsa location değişkenine atanır.
* **bool hasUnit = argumentsJson.RootElement.TryGetProperty("unit", out JsonElement unit);**: JSON içinden unit (sıcaklık birimi: Celsius/Fahrenheit gibi) değeri aranır.
* **if (!hasLocation):** Eğer location değeri bulunamazsa, bir hata fırlatılır çünkü konum bilgisi zorunludur.
* **string toolResult = hasUnit ? GetCurrentWeather(location.GetString(), unit.GetString())** : **GetCurrentWeather(location.GetString())**;: Eğer unit (birim) değeri varsa, hava durumu fonksiyonu birimle birlikte çağrılır. Eğer birim yoksa sadece konuma göre hava durumu alınır.
*** messages.Add(new ToolChatMessage(toolCall.Id, toolResult));**: Hava durumu sonucu mesaj listesine eklenir.
* **requiresAction = true;**: Bu satır, bir araç çağrısı yapıldığı için döngünün tekrar çalıştırılmasını sağlar. Yani model, araçtan gelen yanıtları işledikten sonra asistanın yanıtı tekrar oluşturulur.
* **break**;: Bu, **ToolCalls** durumunu sonlandırır ve döngünün devam etmesini sağlar.

Bu kod, modelin yanıt verebilmesi için gerekli olan araç çağrılarını işler. Örneğin, model kullanıcının konumunu ya da hava durumunu almak için araç çağrısı yapabilir. Bu araç çağrıları işlenir ve sonuçlar sohbet mesajlarına eklenir. requiresAction = true ile döngü yeniden başlatılarak modelin asistan yanıtı tamamlanır.

```csharp
    case ChatFinishReason.ToolCalls:
    {
        messages.Add(new AssistantChatMessage(chatCompletion));

        foreach (ChatToolCall toolCall in chatCompletion.ToolCalls)
        {
            switch (toolCall.FunctionName)
            {
                case nameof(GetCurrentLocation):
                {
                    string toolResult = GetCurrentLocation();
                    messages.Add(new ToolChatMessage(toolCall.Id, toolResult));
                    break;
                }
                case nameof(GetCurrentWeather):
                {
                    using JsonDocument argumentsJson = JsonDocument.Parse(toolCall.FunctionArguments);
                    bool hasLocation = argumentsJson.RootElement.TryGetProperty("location", out JsonElement location);
                    bool hasUnit = argumentsJson.RootElement.TryGetProperty("unit", out JsonElement unit);

                    if (!hasLocation)
                    {
                        throw new ArgumentNullException(nameof(location), "The location argument is required.");
                    }

                    string toolResult = hasUnit
                        ? GetCurrentWeather(location.GetString(), unit.GetString())
                        : GetCurrentWeather(location.GetString());
                    messages.Add(new ToolChatMessage(toolCall.Id, toolResult));
                    break;
                }
            }
        }

        requiresAction = true;
        break;
    }

```

### Hata Durumları

Bu kod parçası, bir switch ifadesinde modelin tamamlanma nedenine göre nasıl tepki vereceğini belirliyor ve belirli durumlar için henüz uygulanmamış işlevleri belirtiyor.

* **case ChatFinishReason.Length:** Eğer model yanıtı Length (uzunluk) nedeniyle durdurulmuşsa, yani cevap çok uzunsa veya MaxTokens limitine ulaşıldıysa, burada bir hata fırlatılıyor. Bu hata, "MaxTokens parametresi veya token limitinin aşılması nedeniyle tamamlanmamış model çıktısı" mesajı ile belirtiliyor. Bu, modelin çıktısının belirtilen token sınırını aştığını ifade eder
* **case ChatFinishReason.ContentFilter**: Eğer model yanıtı bir içerik filtresine takılmışsa, yani modelin yanıtı zararlı ya da uygunsuz içerik içeriyorsa, yine bir hata fırlatılıyor. Bu durumda, hata mesajı "İçerik filtresi bayrağı nedeniyle atlanan içerik" oluyor. Bu, modelin yanıtının bazı içerik kısıtlamalarına takıldığını ifade eder.
* **case ChatFinishReason.FunctionCall:** Bu kısım, artık kullanılmayan (deprecated) bir işlevi ifade ediyor. Model bir FunctionCall nedeniyle sonlandıysa, bu özellik eski olduğundan hata fırlatılıyor ve "Araç çağrıları lehine kullanım dışı bırakıldı." mesajı gösteriliyor.
* **default:** Bu, herhangi başka bir FinishReason değeri olması durumunda çalışan bir bölümdür. Eğer yukarıdaki durumların hiçbiri gerçekleşmezse, bir hata fırlatılır ve hatada chatCompletion.FinishReason.ToString() ifadesiyle modelin sonlanma nedeni hata mesajı olarak gösterilir.


```csharp
    case ChatFinishReason.Length:
        throw new NotImplementedException("MaxTokens parametresi veya token limitinin aşılması nedeniyle tamamlanmamış model çıktısı.");
    case ChatFinishReason.ContentFilter:
        throw new NotImplementedException("İçerik filtresi bayrağı nedeniyle atlanan içerik.");
    case ChatFinishReason.FunctionCall:
        throw new NotImplementedException("Araç çağrıları lehine kullanım dışı bırakıldı.");
    default:
        throw new NotImplementedException(chatCompletion.FinishReason.ToString());
```
### Mesajları Konsola Yazdırma

Bu kod parçası, sohbet tamamlandıktan sonra tüm mesajları (kullanıcı, asistan ve araçlardan gelen mesajları) sırayla konsola yazdırmak için kullanılıyor. İlk olarak bir foreach döngüsü ile messages listesi dolaşılır. Döngü her adımda **message** adlı değişkene bir mesaj atar. Mesajın türü kontrol edilir: Eğer mesaj bir kullanıcı mesajıysa **(UserChatMessage)**, kullanıcının yazdığı metin konsola **"[KULLANICI]"** etiketiyle yazdırılır. Eğer mesaj bir asistan mesajıysa (**AssistantChatMessage**), mesajın içeriği kontrol edilir ve boş değilse asistanın yanıtı **"[ASİSTAN]"** etiketiyle yazdırılır. Eğer mesaj bir araç mesajıysa (ToolChatMessage), araç tarafından üretilen yanıt konsola **"[ARAÇ]"** etiketiyle yazdırılır. Bu yapı, farklı mesaj türlerini ayırt edip, her birini uygun bir şekilde yazdırmayı sağlar.
```csharp
Console.WriteLine("Sohbet tamamlandı. Sonuçlar konsola yazdırılıyor...");
foreach (ChatMessage message in messages)
{
    if (message is UserChatMessage userMessage)
    {
        Console.WriteLine($"[KULLANICI]: {userMessage.Content[0].Text}");
    }
    else if (message is AssistantChatMessage assistantMessage)
    {
        if (assistantMessage.Content != null && assistantMessage.Content.Count > 0)
        {
            Console.WriteLine($"[ASİSTAN]: {assistantMessage.Content[0].Text}");
        }
    }
    else if (message is ToolChatMessage toolMessage)
    {
        Console.WriteLine($"[ARAÇ]: {toolMessage.Content[0].Text}");
    }
}
```

###  Ve ta daa bütün kod!


```csharp
using OpenAI.Chat;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Text.Json;
using System.Threading.Tasks;

namespace MyOpenAIProject.Examples
{
    public class ToolsAndFunctionsExample
    {
        private static string GetCurrentLocation()
        {
            // Normalde burada konum API'si çağrılır, biz İstanbul'u sabit olarak döndüreceğiz.
            return "İstanbul";
        }

        private static string GetCurrentWeather(string location, string unit = "celsius")
        {
            // Hava durumu API'si çağrısı yapılır. Örneğin, "Bugün İstanbul'da hava 25 derece."
            return $"25 {unit} ve İstanbul'da açık bir hava var.";
        }

        private static readonly ChatTool getCurrentLocationTool = ChatTool.CreateFunctionTool(
            functionName: nameof(GetCurrentLocation),
            functionDescription: "Kullanıcının mevcut konumunu alır ve bu örnekte İstanbul olarak sabitler."
        );

        private static readonly ChatTool getCurrentWeatherTool = ChatTool.CreateFunctionTool(
            functionName: nameof(GetCurrentWeather),
            functionDescription: "Belirli bir konum için hava durumu bilgisini alır. İstanbul varsayılan konumdur.",
            functionParameters: BinaryData.FromString("""
        {
            "type": "object",
            "properties": {
                "location": {
                    "type": "string",
                    "description": "Konum bilgisi, örn. İstanbul"
                },
                "unit": {
                    "type": "string",
                    "enum": [ "celsius", "fahrenheit" ],
                    "description": "Kullanılacak sıcaklık birimi, varsayılan 'celsius'."
                }
            },
            "required": [ "location" ]
        }
        """)
        );

        public static void Run()
        {
            Console.WriteLine("Sohbet başlatılıyor...");

            // Kullanıcıdan girdi alınması
            Console.WriteLine("Kullanıcı: İstanbul'da hava nasıl diye sorabilir.");
            string userInput = Console.ReadLine();

            // Kullanıcı mesajını ekliyoruz
            List<ChatMessage> messages = new()
            {
                new UserChatMessage(userInput)  // Kullanıcıdan gelen mesaj
            };

            // Hangi araçların kullanılacağını belirleyen seçenekler
            ChatCompletionOptions options = new()
            {
                Tools = { getCurrentLocationTool, getCurrentWeatherTool }  // Araçları tanımlıyoruz
            };

            // API anahtarını config dosyasından okuma işlemi
            Console.WriteLine("API anahtarı okunuyor...");
            string apiKey = ConfigReader.ReadApiKeyFromConfig();

            if (string.IsNullOrEmpty(apiKey))
            {
                Console.WriteLine("Hata: API anahtarı bulunamadı.");
                return;
            }

            // OpenAI ChatClient oluşturuluyor
            Console.WriteLine("ChatClient oluşturuluyor...");
            ChatClient client = new(model: "gpt-4o", apiKey);


            bool requiresAction;

            do
            {
                requiresAction = false;
                ChatCompletion chatCompletion = client.CompleteChat(messages, options);

                switch (chatCompletion.FinishReason)
                {
                    case ChatFinishReason.Stop:
                        {
                            // Add the assistant message to the conversation history.
                            // Asistan cevabını ekle
                            messages.Add(new AssistantChatMessage(chatCompletion));

                            // chatCompletion nesnesini kontrol etme
                            string jsonResponse = JsonSerializer.Serialize(chatCompletion);
                            Console.WriteLine("Model Yanıtı: " + jsonResponse);

                            break;
                        }

                    case ChatFinishReason.ToolCalls:
                        {
                            messages.Add(new AssistantChatMessage(chatCompletion));

                            foreach (ChatToolCall toolCall in chatCompletion.ToolCalls)
                            {
                                switch (toolCall.FunctionName)
                                {
                                    case nameof(GetCurrentLocation):
                                        {
                                            string toolResult = GetCurrentLocation();
                                            messages.Add(new ToolChatMessage(toolCall.Id, toolResult));
                                            break;
                                        }

                                    case nameof(GetCurrentWeather):
                                        {

                                            using JsonDocument argumentsJson = JsonDocument.Parse(toolCall.FunctionArguments);
                                            bool hasLocation = argumentsJson.RootElement.TryGetProperty("location", out JsonElement location);
                                            bool hasUnit = argumentsJson.RootElement.TryGetProperty("unit", out JsonElement unit);

                                            if (!hasLocation)
                                            {
                                                throw new ArgumentNullException(nameof(location), "The location argument is required.");
                                            }

                                            string toolResult = hasUnit
                                                ? GetCurrentWeather(location.GetString(), unit.GetString())
                                                : GetCurrentWeather(location.GetString());
                                            messages.Add(new ToolChatMessage(toolCall.Id, toolResult));
                                            break;
                                        }

                                    default:
                                        {
                                            // Handle other unexpected calls.
                                            throw new NotImplementedException();
                                        }
                                }
                            }

                            requiresAction = true;
                            break;
                        }

                    case ChatFinishReason.Length:
                        throw new NotImplementedException("MaxTokens parametresi veya token limitinin aşılması nedeniyle tamamlanmamış model çıktısı.");

                    case ChatFinishReason.ContentFilter:
                        throw new NotImplementedException("İçerik filtresi bayrağı nedeniyle atlanan içerik.");

                    case ChatFinishReason.FunctionCall:
                        throw new NotImplementedException("Araç çağrıları lehine kullanım dışı bırakıld.");

                    default:
                        throw new NotImplementedException(chatCompletion.FinishReason.ToString());
                }
            } while (requiresAction);

            // Mesajları konsola yazdırıyoruz
            Console.WriteLine("Sohbet tamamlandı. Sonuçlar konsola yazdırılıyor...");
            foreach (ChatMessage message in messages)
            {
                if (message is UserChatMessage userMessage)
                {
                    Console.WriteLine($"[KULLANICI]: {userMessage.Content[0].Text}");
                }
                else if (message is AssistantChatMessage assistantMessage)
                {
                    // Asistan yanıtı Content[0].Text alanında yer alıyor, bu nedenle doğru şekilde yazdırıyoruz
                    if (assistantMessage.Content != null && assistantMessage.Content.Count > 0)
                    {
                        Console.WriteLine($"[ASİSTAN]: {assistantMessage.Content[0].Text}");
                    }
                }
                else if (message is ToolChatMessage toolMessage)
                {
                    Console.WriteLine($"[ARAÇ]: {toolMessage.Content[0].Text}");
                }
            }

        }

    }
}

```

## Çıktı : 
![ezgif-3-a623f6e4c7](https://github.com/user-attachments/assets/dac6cc4d-1cce-450a-88f1-b5e875a71172)



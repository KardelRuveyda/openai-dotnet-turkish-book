# Başlarken

* Bu kitap kapsamında yapılan tüm örneklere [bu repodan](https://github.com/KardelRuveyda/openai-dotnet-exercises) ulaşabilirsiniz!
* Bu satırları yazarken **OpenAI-2.1.0-beta.13** sürümü dahilindeki tüm güncellemeleri de kapsayan bir kitap oluşturmaya çalıştım. Ama tabii orjinal repo üzerindeki geliştirmeler devam edecektir. Önemli olan bu geliştirmeler kapsamında kitabı güncelleyebilmek. Bu kısımda sizlerin de yardımları pek değerli olur :)

## Ön Koşullar

OpenAI REST API'yi kullanabilmek için bir API anahtarına ihtiyacınız oluyor. Aman diyeyim Open AI key'i projelerinizde kullanırken Github'a göndermeyiniz. LangChain ile geliştirme yaparken **.env** dosyasını **.gitignore**'ye eklemiştim. DotNet tarafında ise **secret.json** kavramından faydalanarak hassas verilerinizi koruyabilirsiniz.Secret Protection kavramı ile [ilgili makaleyi görüntüleyebilirsiniz](https://medium.com/@ruveydakardelcetin/asp-net-core-projelerinde-güvenlik-secret-protection-2d0c48654a1b) 

Ya da .env kısmında Open AI Key'i bununla ilgili gerçekten çok güldüğüm bir tweetteki gibi tanımlayabilirsiniz. :D  [buradan görüntüleyebilirsiniz](https://x.com/atarikkarakas/status/1815021390115860873). :) 


Bu anahtarı almak için aşağıdaki adımları izleyebilirsiniz:

1. **OpenAI Hesabı Oluşturma veya Giriş Yapma**
   - Eğer bir OpenAI hesabınız yoksa, [OpenAI'nin web sitesine](https://www.openai.com/) giderek yeni bir hesap oluşturun. Sağ üst köşedeki **Sign Up** (Kaydol) butonuna tıklayarak gerekli bilgileri girin.
   - Hesabınız varsa, **Log In** (Giriş Yap) butonuna tıklayarak mevcut kullanıcı bilgilerinizle giriş yapın.

2. **API Anahtarları Sayfasına Gitme**
   - Hesabınıza giriş yaptıktan sonra, [OpenAI API anahtarları sayfasına](https://platform.openai.com/account/api-keys) gitmek için [bu bağlantıyı](https://platform.openai.com/account/api-keys) kullanabilirsiniz. Alternatif olarak, OpenAI ana sayfasında oturum açtıktan sonra, sağ üst köşede bulunan profil simgesine tıklayarak **API Keys** veya **Account** seçeneğine gidin.

![image](https://github.com/user-attachments/assets/4b82fcb2-24f1-43e4-b565-edddfd6c43ae)

3. **Yeni Bir API Anahtarı Oluşturma**
   - API anahtarları sayfasında, **Create new secret key** (Yeni gizli anahtar oluştur) butonuna tıklayın.
   - Açılan pencerede, anahtarınıza bir ad verebilirsiniz. Bu adlandırma isteğe bağlıdır; anahtarın kullanım amacını belirlemek için bir ad vermek faydalı olabilir.
   - Anahtarı oluşturduktan sonra, yeni API anahtarınız ekranda görüntülenecek. **Anahtarı kaydet** (Save) butonuna tıklamadan önce bu anahtarı **güvenli bir yerde saklayın**. Anahtar yalnızca bir kez görüntülenecektir, bu yüzden bu bilgiyi kaybetmemeye dikkat edin.
  
![image](https://github.com/user-attachments/assets/c1e1008a-659d-4af5-80cc-ffc9035538d2)

4. **Ödeme**
![image](https://github.com/user-attachments/assets/e3e1a31e-4862-4ee8-9690-97d97cb47041)

OpenAI platformuna üye olduktan sonra, API hizmetlerini kullanabilmek için ödeme yöntemlerinizi tanımlamanız gerekiyor. Faturalandırma işlemleri, kredi kartı veya diğer desteklenen ödeme yöntemleriyle yapılır. Ödeme bilgilerinizi aşağıdaki adımları takip ederek ekleyebilirsiniz:

- **Hesap Ayarları'na Gitmek:** OpenAI ana sayfasına giriş yaptıktan sonra, sağ üst köşede profil simgenize tıklayarak “Billing (Fatura)” sekmesine gidin.
- **Ödeme Yöntemi Ekleme:** Bu bölümde, kredi kartı bilgilerinizi ekleyebileceğiniz bir alan bulunur. Bu karta düzenli olarak kullanımlarınıza göre fatura kesilecektir.

OpenAI, API kullanımı için farklı fiyatlandırma planları sunmaktadır. Fiyatlandırma, genellikle kullandığınız hizmet miktarına bağlı olarak "kullandıkça öde" (pay-as-you-go) modeliyle işler. API istekleri başına maliyet hesaplanır ve aylık olarak size bir fatura kesilir. Detaylı fiyatlandırma planları ve kullanım ücretleri, OpenAI web sitesinde yer alır.

Örneğin:

- **GPT-4** kullanımının maliyeti, kullandığınız modelin kapasitesine ve yaptığınız istek sayısına bağlıdır.Diğer hizmetler için de farklı fiyatlandırmalar uygulanabilir.

Hesabınızda, ne kadar kullanım yaptığınızı ve aylık olarak oluşacak faturayı takip edebileceğiniz bir dashboard (gösterge paneli) bulunmaktadır. Bu panelde: toplam kullanım miktarınızı, geçmiş faturalarınızı, gelecek tahmini faturalarınızı görebilirsiniz. Bu sayede, kullanımlarınızı takip edebilir ve harcamalarınızı yönetebilirsiniz.

5. **Anahtarı Güvenli Bir Şekilde Saklama**
   - Oluşturduğunuz API anahtarını **güvenli bir yerde saklamalısınız**. Anahtarı bilgisayarınızda şifreli bir dosya, parola yöneticisi veya başka bir güvenli yer olarak saklayabilirsiniz.
   - Anahtarınızı başkalarıyla paylaşmaktan ve anahtarınızın güvenliğini sağlamaktan dikkatli olun. API anahtarını kaybetmek veya paylaşmak, hesabınızın güvenliğini tehlikeye atabilir.

6. **Anahtarı Projenizle Kullanma**
   - API anahtarını aldıktan sonra, OpenAI API'ye erişmek için bu anahtarı kullanabilirsiniz. Örneğin, bir .NET uygulamasında API anahtarını bir yapılandırma dosyasına ekleyebilir ve bu anahtarı kullanarak API çağrıları yapabilirsiniz.
  

**Not:** _Ben bu kitaptaki örnekleri yapabilmek için hesabıma 10$ yüklemiştim. Tüm örnekleri yapmama rağmen hala 9.74$ dolarım kalmış. Çok fazla bir harcama olmuyor. Onu belirtmek isterim sayın bu satırları okuyan kişiler :)_

### Örnek API Anahtarı Kullanımı (C#)

```csharp
using OpenAI;

var client = new OpenAIClient("YOUR_API_KEY_HERE");
var response = await client.Completions.CreateCompletionAsync(new CompletionRequest
{
    Prompt = "Merhaba, dünya!",
    MaxTokens = 50
});
Console.WriteLine(response.Choices[0].Text);
```

## NuGet Paketini Kurma

OpenAI .NET kütüphanesini projenize eklemek için aşağıdaki adımları izleyebilirsiniz:

### IDE Kullanarak Paket Ekleme

1. Visual Studio veya kullandığınız başka bir .NET IDE'yi açın.
2. Projenizi açın.
3. **Solution Explorer** panelinde, projenizin üzerine sağ tıklayın ve **Manage NuGet Packages** seçeneğini seçin.
4. **Browse** sekmesine gidin ve **OpenAI** paketini arayın.
5. **OpenAI** paketini seçin ve **Install** butonuna tıklayın.

### dotnet CLI Kullanarak Paket Ekleme

Alternatif olarak, .NET CLI kullanarak paketi ekleyebilirsiniz:

```bash
dotnet add package OpenAI --prerelease

```

Kod örnekleri [Open AI Dotnet API Dokümanında](https://github.com/openai/openai-dotnet). **.NET 9** kullanılarak yazılmıştır. **OpenAI .NET** kütüphanesi tüm **.NET Standard 2.0** uygulamaları ile uyumludur, ancak bazı kod örnekleri daha yeni dil özelliklerine bağlı olabilir.

## İstemci Kutuphanesini Kullanma

Bu örnek, **OpenAI**'nin chat API'sini kullanarak bir sohbet tamamlaması gerçekleştirir. Kod parçacığını inceleyerek temel bir kullanım senaryosunu görebilirsiniz.

```csharp
using OpenAI.Chat;

// ChatClient nesnesini oluşturuyoruz, model ve API anahtarını belirtiyoruz.
ChatClient client = new(model: "gpt-4o", Environment.GetEnvironmentVariable("OPENAI_API_KEY"));

// Model ile bir sohbet tamamlaması gerçekleştiriyoruz.
ChatCompletion completion = client.CompleteChat("Şu cümleyi söyle: 'bu bir test.'");

// Sonucu ekrana yazdırıyoruz.
Console.WriteLine($"[ASİSTAN]: {completion}");
```
### Kodu biraz açıklayalım!

```csharp
using OpenAI.Chat;
```

- **OpenAI.Chat** ad alanını içe aktarır, böylece **ChatClient** ve diğer chat ile ilgili sınıflara erişim sağlanır.Kurduğumuz paketi burada ekliyoruz. 


```csharp
ChatClient client = new(model: "gpt-4o", Environment.GetEnvironmentVariable("OPENAI_API_KEY"));
```

- **ChatClient** sınıfından bir **client** nesnesi oluşturur.
- **model:** **"gpt-4o"** parametresi, kullanılacak **OpenAI** modelini belirtir.
- **Environment.GetEnvironmentVariable("OPENAI_API_KEY")** ifadesi, API anahtarını bir ortam değişkeninden alır.

```csharp
ChatCompletion completion = client.CompleteChat("Şu cümleyi söyle: 'bu bir test.'");
```

- **CompleteChat** metodu, belirtilen mesaj ile sohbet tamamlaması gerçekleştirir.
- Dönen **ChatCompletion** nesnesi, modelin yanıtını içerir.

```csharp
Console.WriteLine($"[ASİSTAN]: {completion}");
```

Ve mutlu son! Ekrana yazdırma işlemi gerçekleşir. Ama tekrardan hatırlatmak gerekirse API anahtarını doğrudan kodda kullanmak yerine, güvenli bir yerde saklayarak ortam değişkeni veya yapılandırma dosyası aracılığıyla erişmek önemlidir. Bu, API anahtarının kaybolmasını veya kötüye kullanılmasını önler.

![ezgif-7-9870428eac](https://github.com/user-attachments/assets/8584651d-6f27-426f-a848-b603f5b4747c)



## Namespace Organizasyonu

**`OpenAI`** kütüphanesi, **OpenAI**'nin çeşitli özellik alanlarını yönetmek için bir dizi isim alanına ayrılmıştır. Her bir isim alanı, belirli bir işlevi yerine getiren bir istemci sınıfı içerir. Ben aşağıdaki namespacelerden çoğunu kullandım diyebilirim. Bunları aşağıdaki gibi açıklayabiliriz:

### `OpenAI.Assistants`
- **Sınıf:** `AssistantClient`
- **Açıklama:** OpenAI'nin asistan özelliklerini yönetir. Genellikle deneysel özellikler içerir. **`AssistantClient`**, bu tür işlevleri uygulamak için kullanılır. Ancak web sitesinde `[Deneysel]` etiketi ile yer alıyor yani bu da demek oluyor ki bu sınıfın hala geliştirilme aşamasındadır.

### `OpenAI.Audio`
- **Sınıf:** `AudioClient`
- **Açıklama:** Sesle ilgili işlemleri yönetir. Ses verilerini işlemek veya analiz etmek için kullanılır. **`AudioClient`**, sesle ilgili API çağrılarını yönetir.
[buradan ses örneklerine ulaşabilirsiniz]( https://platform.openai.com/docs/guides/text-to-speech/overview). Oyun seslendirme gibi işlemlerde özellikle kullanılabilir gibi geldi.

### `OpenAI.Batch`
- **Sınıf:** `BatchClient`
- **Açıklama:** Toplu işlemleri yönetir. Birden fazla veriyi veya işlemi topluca işlemek için uygundur. **`BatchClient`**, büyük veri setleri üzerinde toplu işlem yapmayı sağlar.

### `OpenAI.Chat`
- **Sınıf:** `ChatClient`
- **Açıklama:** Sohbet uygulamaları ve sohbet tabanlı API'ler için kullanılır. **`ChatClient`**, chat tabanlı tamamlamalar ve etkileşimler için kullanılır. Örneğin, bir kullanıcıya mesaj göndermek veya bir sohbet başlatmak için kullanılabilir.

### `OpenAI.Embeddings`
- **Sınıf:** `EmbeddingClient`
- **Açıklama:** Metin verilerini gömme **(embedding)** işlemleri için kullanılır. _Metinleri vektörlere dönüştürmek_ ve _metin verilerini daha anlamlı hale getirmek için_ kullanılır.** `EmbeddingClient`**, bu tür gömme işlemlerini gerçekleştirir.

### `OpenAI.FineTuning`
- **Sınıf:** `FineTuningClient`
- **Açıklama:** Model ince ayarları (fine-tuning) yapmak için kullanılır. Mevcut modelleri belirli bir uygulama veya veri seti için özelleştirmek üzere kullanılabilir. **`FineTuningClient**`, ince ayar işlemleri için API çağrılarını yönetir.

### `OpenAI.Files`
- **Sınıf:** `FileClient`
- **Açıklama:** Dosya yönetimi işlemleri için kullanılır. Dosyaları yüklemek, indirmek veya yönetmek için kullanılır. **`FileClient`**, dosyalarla ilgili işlemleri yönetir.

### `OpenAI.Images`
- **Sınıf:** `ImageClient`
- **Açıklama:** Görüntü işleme ve yönetimi ile ilgili işlemleri yönetir. Görüntü oluşturma veya düzenleme gibi işlevler sağlar. **`ImageClient`**, görüntü verileri ile çalışmak için kullanılır.

### `OpenAI.Models`
- **Sınıf:** `ModelClient`
- **Açıklama:** Modellerle ilgili işlemleri yönetir. Mevcut modelleri listeleme, model oluşturma veya model bilgilerini yönetmek için kullanılır. **`ModelClient`**, model yönetimi işlemleri için kullanılır.

### `OpenAI.Moderations`
- **Sınıf:** `ModerationClient`
- **Açıklama:** İçerik moderasyonu işlemleri için kullanılır. İçeriklerin uygunluğunu kontrol etmek ve filtrelemek için kullanılır. **`ModerationClient`**, içerik moderasyonunu yönetir.

### `OpenAI.VectorStores`
- **Sınıf:** `VectorStoreClient`
- **Açıklama:** Vektör verilerini depolama ve yönetme işlemleri için kullanılır. Vektör tabanlı veri yapıları ve sorgulamalar için kullanılır. **`VectorStoreClient`**, bu tür verileri yönetmek için kullanılır. Ancak web sitesinde hala**`[Deneysel]`** etiketi ile yer alıyor o yüzden şu anda deneysel olarak kullanıldığını belirtebiliriz.


## Async API Kullanma

Bir istemcinin senkron API isteğini uyguladığı her durumda, aynı istemci sınıfında istek için geçerli bir asenkron çağrı sürümü vardır. Asenkron yöntemler, API çağrılarının arka planda bloklama olmadan çalışmasına olanak tanıyarak uygulamanızın daha hızlı ve daha duyarlı olmasını sağlayabilir.

### Asenkron API'nin Temel Kullanımı

Örneğin, **ChatClient** sınıfının **CompleteChat** yönteminin asenkron versiyonu **CompleteChatAsync** olarak adlandırılır. Senkron API çağrısını asenkron versiyonuyla yeniden yazmak oldukça basittir. Tek yapmanız gereken, **await** anahtar kelimesini kullanarak ilgili asenkron yöntem çağrısını yapmak, sonrası zaten kolay :)

### Senkron Örneği

```csharp
using OpenAI.Chat;

// API anahtarınızı bir ortam değişkeninden alarak bir ChatClient oluşturun
ChatClient client = new(model: "gpt-4o", Environment.GetEnvironmentVariable("OPENAI_API_KEY"));

// Sohbet tamamlaması oluşturun
ChatCompletion completion = client.CompleteChat("Şu cümleyi söyle: 'bu bir test.'");

// Sonucu ekrana yazdırın
Console.WriteLine($"[ASİSTAN]: {completion}");
```

### Asenkron Örneği

Asenkron API çağrısını kullanmak için, çağırmak istediğiniz asenkron yöntemi await anahtar kelimesiyle kullanarak API çağrısının arka planda çalışmasını ve uygulamanızın yanıt vermeye devam etmesini sağlayabilirsiniz. Bu yöntemi çağırdığınız metodu ise async olarak işaretlemeniz gerekmektedir. Bu şekilde, uzun süren işlemler uygulamanızın ana iş parçacığını bloklamadan yürütülür ve kullanıcı deneyimi daha akıcı hale gelir.


```csharp
using OpenAI.Chat;
using System.Threading.Tasks;

public async Task CreateChatCompletion()
{
    // API anahtarınızı bir ortam değişkeninden alarak bir ChatClient oluşturun
    ChatClient client = new(model: "gpt-4o", Environment.GetEnvironmentVariable("OPENAI_API_KEY"));

    // Asenkron olarak sohbet tamamlaması oluşturun
    ChatCompletion completion = await client.CompleteChatAsync("Şu cümleyi söyle: 'bu bir test.'");

    // Sonucu ekrana yazdırın
    Console.WriteLine($"[ASİSTAN]: {completion}");
}
```

### Neden Asenkron Kullanmalısınız?

Asenkron API çağrıları, uygulamanızın performansını önemli ölçüde artırabilir. Bu yöntemler, uzun süreli işlemleri ana iş parçacığını bloklamadan arka planda yürütmenizi sağlar, böylece kullanıcı arayüzünüz donmaz ve uygulamanız daha hızlı yanıt verir. Özellikle ağ üzerinden veri çekerken bu yöntem, kullanıcıların daha akıcı bir deneyim yaşamasını sağlar çünkü kullanıcı arayüzü, veri yüklenirken donma veya gecikme yaşamaz. Ayrıca, asenkron API'ler, sistem kaynaklarının daha verimli bir şekilde kullanılmasına yardımcı olur; çünkü işlem tamamlanana kadar diğer görevler işlenebilir. Bu yaklaşımlar, performansı artırmak ve kullanıcı deneyimini iyileştirmek için etkili bir yöntemdir.


Diyelim ki bir uygulama geliştirdiniz ve bu uygulama internetten veri çekiyor. Örneğin, hava durumu bilgilerini bir API'den alıyorsunuz. Synchronous (senkron) ve asynchronous (asenkron) API çağrıları arasında nasıl fark olduğunu göstermek için aşağıdaki örneği ele alalım:



```csharp
public void GetWeatherData()
{
    // API'den hava durumu verilerini senkron şekilde al
    var client = new HttpClient();
    var response = client.GetStringAsync("https://api.weather.com/current").Result;
    
    // Veriyi işleyin
    Console.WriteLine(response);
}

```
Yukarıdaki kodda, **GetStringAsync** yöntemi veriyi almak için çağrılır, ancak **.Result** özelliği ile senkron bir şekilde beklenir. Yani, uygulama bu veri alınana kadar donar ve kullanıcı arayüzü yanıt vermez.

```csharp
public async Task GetWeatherDataAsync()
{
    // API'den hava durumu verilerini asenkron şekilde al
    var client = new HttpClient();
    var response = await client.GetStringAsync("https://api.weather.com/current");
    
    // Veriyi işleyin
    Console.WriteLine(response);
}
```
**GetStringAsync** kodda ise await anahtar kelimesi kullanılarak veri alınıncaya kadar arka planda beklenir. Bu, uygulamanın diğer işlemleri yapmaya devam etmesini sağlar ve kullanıcı arayüzü donmaz.


## OpenAIClient Sınıfını Kullanma

OpenAI kütüphanesinin farklı bölümlerini ele alırken, bazen birden fazla alanda işlem yapabilmek için farklı client'larla çalışmak gerekebilir. Ancak, bu client'ların her birini ayrı ayrı yönetmek hem zaman alıcı olabilir hem de verimlilik açısından dezavantaj yaratabilir. Bu noktada OpenAI'nin sunduğu **OpenAIClient** sınıfı devreye giriyor ve bu süreci önemli ölçüde basitleştiriyor.

## OpenAIClient Nedir ve Neden Kullanılır?

**OpenAIClient**, temel olarak **OpenAI API** ile etkileşim kurmak için kullanılan merkezi bir nesne gibidir. Birden fazla API alanında çalışırken, aynı uygulama ayrıntılarını birden çok client arasında paylaşarak işlemleri daha verimli hale getirir. Yani, OpenAI’nin farklı özelliklerini **(örneğin dil işleme, ses tanıma, vb.)** kullanırken her seferinde aynı ayarları yeniden yapmanıza gerek kalmaz. Tüm client'lar ortak bir altyapı üzerinden yönetilir, bu da kodunuzu daha temiz ve yönetilebilir hale getirir.

Örneğin, birden fazla modeli hedefleyen farklı client'lar kullanmanız gerektiğinde, **OpenAIClient** tek bir API anahtarı ve aynı uygulama yapılandırması ile bu client'ları oluşturabilir. Bu, her client için aynı adımları tekrar tekrar yapmaktan kaçınmanızı sağlar ve geliştirici açısından iş yükünü azaltır.

## OpenAIClient Nasıl Kullanılır?

**OpenAIClient** oluşturmak oldukça basit bir işlemdir. İhtiyacınız olan şey yalnızca OpenAI API anahtarınızı sağlamak. Çoğu projede bu anahtar çevresel değişkenlerde saklanılarak kullanılabilir.

```csharp
OpenAIClient client = new(Environment.GetEnvironmentVariable("OPENAI_API_KEY"));
```

Burada, **Environment.GetEnvironmentVariable("OPENAI_API_KEY")** ifadesi, işletim sisteminizde tanımlı olan bir ortam değişkeninden API anahtarını alır ve bu anahtarı kullanarak bir **OpenAIClient** nesnesi oluşturur. Bu nesne, artık **OpenAI** ile etkileşim kurabilen diğer client'ları üretmek için kullanılabilir.

Diyelim ki **OpenAI**'nin sesle ilgili hizmetlerini kullanarak bir bot geliştiriyorsunuz. Bu noktada, metni sese dönüştürmek veya ses tanıma işlemleri yapmak için **AudioClient** sınıfına ihtiyacınız olacak. İşte bu noktada, OpenAIClient'ın size sunduğu avantajlardan biri devreye girer. Bir **AudioClient** oluşturmak için, OpenAIClient nesnesinin **GetAudioClient** metodunu kullanabilirsiniz. Bu metod, hangi modeli kullanacağınızı belirterek size bir AudioClient döndürür.

```csharp
AudioClient ttsClient = client.GetAudioClient("tts-1");
```

Yukarıdaki örnek, **tts-1** modelini kullanarak bir sesli metin (Text-to-Speech) client'ı oluşturur. Bu sayede, yazdığınız metinleri bu client üzerinden sese dönüştürebilirsiniz.

Benzer şekilde, OpenAI'nin diğer ses modellerini kullanmak için de aynı yapıyı kullanabilirsiniz. Örneğin, OpenAI’nin Whisper modeli ile bir ses tanıma client'ı oluşturmak için şu şekilde bir kod yazabilirsiniz:

```csharp
AudioClient whisperClient = client.GetAudioClient("whisper-1");
```

Bu yaklaşım, her model için ayrı bir **AudioClient** oluşturmanıza olanak tanır, böylece farklı kullanım senaryolarına göre çeşitli ses modelleriyle çalışabilirsiniz.

**OpenAIClient**, özellikle birden fazla alanda API'yi kullanırken işlerinizi oldukça kolaylaştırır. Farklı client'larla çalışırken her birini ayrı ayrı oluşturmak yerine, OpenAIClient ile merkezi bir yönetim sağlayabilirsiniz. Bu yapı, hem daha temiz bir kod yapısı oluşturmanıza yardımcı olur hem de performansı optimize eder.

Bu nedenle, eğer OpenAI’nin sunduğu çeşitli hizmetleri tek bir projede kullanmayı planlıyorsanız, OpenAIClient sınıfını tercih etmek, uzun vadede işlerinizi hızlandıracak ve yönetimi kolaylaştıracaktır. Özellikle ses, dil işleme veya diğer alanlarda sıkça OpenAI API kullanıyorsanız, bu yaklaşım sizin için ideal olacaktır.


[Kod örneği için tıklayınız](https://github.com/KardelRuveyda/openai-dotnet-exercises/tree/master/Examples/01)









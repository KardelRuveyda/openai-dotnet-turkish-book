# Başlarken

## Ön Koşullar

OpenAI REST API'yi kullanabilmek için bir API anahtarına ihtiyacınız oluyor. Aman diyeyim Open AI key'i projelerinizde kullanırken Github'a göndermeyiniz. LangChani ile geliştirme yaparken .env dosyasını .gitignore'ye eklemiştim. Fakat DotNet kısmında **secret.json** kavramını kullanabilirsiniz! 

Bununla ilgili gerçekten çok güldüğüm bir tweet'i de [buradan görüntüleyebilirsiniz](https://x.com/atarikkarakas/status/1815021390115860873). :) 


Bu anahtarı almak için aşağıdaki adımları izleyebilirsiniz:

1. **OpenAI Hesabı Oluşturma veya Giriş Yapma**
   - Eğer bir OpenAI hesabınız yoksa, [OpenAI'nin web sitesine](https://www.openai.com/) giderek yeni bir hesap oluşturun. Sağ üst köşedeki **Sign Up** (Kaydol) butonuna tıklayarak gerekli bilgileri girin.
   - Hesabınız varsa, **Log In** (Giriş Yap) butonuna tıklayarak mevcut kullanıcı bilgilerinizle giriş yapın.

2. **API Anahtarları Sayfasına Gitme**
   - Hesabınıza giriş yaptıktan sonra, [OpenAI API anahtarları sayfasına](https://platform.openai.com/account/api-keys) gitmek için [bu bağlantıyı](https://platform.openai.com/account/api-keys) kullanabilirsiniz. Alternatif olarak, OpenAI ana sayfasında oturum açtıktan sonra, sağ üst köşede bulunan profil simgesine tıklayarak **API Keys** veya **Account** seçeneğine gidin.

3. **Yeni Bir API Anahtarı Oluşturma**
   - API anahtarları sayfasında, **Create new secret key** (Yeni gizli anahtar oluştur) butonuna tıklayın.
   - Açılan pencerede, anahtarınıza bir ad verebilirsiniz. Bu adlandırma isteğe bağlıdır; anahtarın kullanım amacını belirlemek için bir ad vermek faydalı olabilir.
   - Anahtarı oluşturduktan sonra, yeni API anahtarınız ekranda görüntülenecek. **Anahtarı kaydet** (Save) butonuna tıklamadan önce bu anahtarı **güvenli bir yerde saklayın**. Anahtar yalnızca bir kez görüntülenecektir, bu yüzden bu bilgiyi kaybetmemeye dikkat edin.

4. **Anahtarı Güvenli Bir Şekilde Saklama**
   - Oluşturduğunuz API anahtarını **güvenli bir yerde saklamalısınız**. Anahtarı bilgisayarınızda şifreli bir dosya, parola yöneticisi veya başka bir güvenli yer olarak saklayabilirsiniz.
   - Anahtarınızı başkalarıyla paylaşmaktan ve anahtarınızın güvenliğini sağlamaktan dikkatli olun. API anahtarını kaybetmek veya paylaşmak, hesabınızın güvenliğini tehlikeye atabilir.

5. **Anahtarı Projenizle Kullanma**
   - API anahtarını aldıktan sonra, OpenAI API'ye erişmek için bu anahtarı kullanabilirsiniz. Örneğin, bir .NET uygulamasında API anahtarını bir yapılandırma dosyasına ekleyebilir ve bu anahtarı kullanarak API çağrıları yapabilirsiniz.

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

Kod örnekleri [Open AI Dotnet API Dokümanında](https://github.com/openai/openai-dotnet). .NET 8 kullanılarak yazılmıştır. OpenAI .NET kütüphanesi tüm .NET Standard 2.0 uygulamaları ile uyumludur, ancak bazı kod örnekleri daha yeni dil özelliklerine bağlı olabilir.

## İstemci Kütüphanesini Kullanma

Bu örnek, OpenAI'nin chat API'sini kullanarak bir sohbet tamamlaması gerçekleştirir. Kod parçacığını inceleyerek temel bir kullanım senaryosunu görebilirsiniz.

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

- OpenAI.Chat ad alanını içe aktarır, böylece ChatClient ve diğer chat ile ilgili sınıflara erişim sağlanır.Kurduğumuz paketi burada ekliyoruz. 


```csharp
ChatClient client = new(model: "gpt-4o", Environment.GetEnvironmentVariable("OPENAI_API_KEY"));
```

- ChatClient sınıfından bir client nesnesi oluşturur.
- **model:** **"gpt-4o"** parametresi, kullanılacak **OpenAI** modelini belirtir.
- Environment.GetEnvironmentVariable("OPENAI_API_KEY") ifadesi, API anahtarını bir ortam değişkeninden alır.

```csharp
ChatCompletion completion = client.CompleteChat("Şu cümleyi söyle: 'bu bir test.'");
```

- **CompleteChat** metodu, belirtilen mesaj ile sohbet tamamlaması gerçekleştirir.
- Dönen **ChatCompletion** nesnesi, modelin yanıtını içerir.

```csharp
Console.WriteLine($"[ASİSTAN]: {completion}");
```

Ve mutlu son! Ekrana yazdırma işlemi gerçekleşir. Ama tekrardan hatırlatmak gerekirse API anahtarını doğrudan kodda kullanmak yerine, güvenli bir yerde saklayarak ortam değişkeni veya yapılandırma dosyası aracılığıyla erişmek önemlidir. Bu, API anahtarının kaybolmasını veya kötüye kullanılmasını önler.


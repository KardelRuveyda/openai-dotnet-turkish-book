## Asistanlarla akış ve GPT-4o görüşü nasıl kullanılır?

**Vision** teknolojisinin amacı, bilgisayarların ve yapay zekâ sistemlerinin görsel bilgiyi anlamasını, işlemesini ve yorumlamasını sağlamaktır. Bu teknoloji, bilgisayarın dijital görüntüleri, videoları ya da fiziksel dünyadan gelen diğer görsel girdileri analiz edebilme yeteneğini ifade eder. Özellikle **GPT-4o Vision** gibi modellerde, yapay zekânın metin oluşturma kabiliyetinin yanı sıra görsel verileri de anlamlandırabilmesi sağlanır.

**GPT-4o Vision** gibi teknolojiler, metin tabanlı modellerle görsel veriyi birleştirerek daha gelişmiş ve kullanışlı uygulamaların önünü açar. Vision'un amacı ve kullanım alanlarını daha detaylı bir şekilde inceleyelim.

## Vision Teknolojisinin Amacı ve Faydaları

* Vision teknolojisinin ana amacı, bir yapay zekâ modelinin görsel verileri (resimler, videolar) anlamlandırabilmesidir. Bu, resimdeki nesnelerin, renklerin, şekillerin ve diğer özelliklerin otomatik olarak tanınmasını içerir. Örneğin, bir resimde bir elma ile bir portakalın ayırt edilmesi, nesnelerin tespiti (object detection) ya da sahnelerin tanımlanması (scene recognition) gibi işlemler yapılabilir.
* GPT-4o Vision gibi modeller, metin ve görselleri aynı anda işleyerek kullanıcılara daha kapsamlı yanıtlar verir. Örneğin, bir kullanıcı sisteme bir resim gönderdiğinde, sistem bu resimde ne olduğunu anlamlandırıp metinsel bir açıklama üretir. Aynı şekilde, bir görsel hakkında sorular sorulduğunda, model hem metinsel hem de görsel verileri işleyerek yanıt verebilir.
* Vision teknolojisi, karmaşık sorunların çözümünde etkili bir araçtır. Örneğin, tıbbi görüntüleme (röntgen, MR gibi), güvenlik kameralarının analizi, sürücüsüz araçlarda çevresel algılama gibi alanlarda Vision teknolojisi kullanılmaktadır. Bu, yapay zekânın insan gözünün görebildiği ve yorumlayabildiği şeyleri görmesini ve anlamasını sağlar.
* Görsel verilerin otomatik olarak analiz edilmesi, manuel olarak yapılması zor veya zaman alıcı işlemleri hızlandırır. Örneğin, bir şirketin ürün görsellerini etiketleme veya kataloglama işlemi, Vision teknolojisi ile otomatize edilebilir. Aynı şekilde fabrikalarda kalite kontrol, tarımda mahsul analizi gibi uygulamalar da Vision sayesinde daha hızlı ve etkili bir şekilde gerçekleştirilebilir.
* Vision, kullanıcıların sistemlerle daha doğal yollarla etkileşime girmesine olanak tanır. Örneğin, bir kullanıcı bir chatbot’a resim gönderebilir ve bu resim hakkında bilgi almak isteyebilir. GPT-4o Vision gibi sistemler, bu tür kullanımlar için idealdir. Resim yükleme ve analiz yapma, sistemlerle etkileşimde metin ve görselin bir arada kullanılmasını sağlar, bu da daha zengin bir kullanıcı deneyimi sunar.

## GPT-4o Vision'un Özellikleri ve Kullanım Alanları

GPT-4o Vision, OpenAI'nin metin üretme yeteneklerine görsel veri işleme yeteneğini entegre eden bir modeldir. Aşağıdaki kullanım senaryolarında oldukça etkilidir.

* Kullanıcı tarafından yüklenen bir resmin içeriğini otomatik olarak açıklayabilir. Örneğin, bir doğa fotoğrafı yüklendiğinde, model fotoğraftaki nesneleri (ağaçlar, göl, dağ vb.) tespit ederek bu nesnelerin ne olduğunu açıklar.
* Görüntüde yer alan spesifik nesneleri tanımlayabilir ve bu nesneler hakkında bilgi verebilir. Örneğin, bir resimdeki hayvanları tanıyabilir, yiyecekleri ayırt edebilir ya da bir sahnede bulunan araçları ve insanları analiz edebilir.
* İki veya daha fazla görselin karşılaştırmasını yapabilir. Örneğin, iki ürünün görüntüsünü yükleyip, aralarındaki farkları ya da benzerlikleri açıklamasını sağlayabilirsiniz. Bu, e-ticaret sitelerinde ürün karşılaştırmaları veya tıbbi alanda farklı röntgen görüntülerinin analiz edilmesi gibi yerlerde kullanılabilir.
* Tıp alanında, röntgen, MR ve diğer tıbbi görüntülerin analizinde kullanılabilir. Eğitimde ise öğretmenler, öğrenciler için görsel açıklamalar ve ders materyalleri oluşturabilir. Araştırma alanında da bilim insanları, görsel verileri işleyerek keşifler yapabilirler.
* Ürün görselleri üzerinden analiz yaparak, kullanıcılara ürünler hakkında açıklama sağlayabilir veya benzer ürün önerileri yapabilir. Bu özellik, özellikle e-ticaret sitelerinde müşteri deneyimini artırmak için kullanılabilir.
* Sosyal medya platformlarında kullanıcıların yükledikleri görselleri analiz ederek, otomatik etiketleme, içerik önerileri veya içerik denetimi yapabilir.

Vision teknolojisinin amacı, yapay zekânın görsel bilgiyi anlaması ve işleyebilmesidir. Bu teknoloji, birçok sektörde kullanım alanı bulabilir ve metin-görsel entegrasyonu sağlayarak insan-makine etkileşimlerini daha ileri bir seviyeye taşıyabilir. **GPT-4o Vision** gibi modeller sayesinde, yapay zekâ sadece metin tabanlı sorulara yanıt vermekle kalmaz, aynı zamanda görsel verileri de analiz edebilir ve anlamlandırabilir. Bu da onu çok daha geniş bir yelpazede kullanışlı hale getirir.

## Bir örnekle pekiştirelim!

```csharp
using OpenAI.Chat;
using System;
using System.Collections.Generic;
using System.IO;

namespace OpenAI.Examples
{
    public partial class ChatExamples
    {
        public void Example05_Vision()
        {
            // 1. API anahtarını config.json dosyasından oku
            string apiKey = ConfigReader.ReadApiKeyFromConfig();

            // 2. API anahtarı alınamazsa işlemi sonlandır
            if (string.IsNullOrEmpty(apiKey))
            {
                Console.WriteLine("config.json dosyasında API anahtarı bulunamadı.");
                return;
            }

            // 3. GPT-4o modelini kullanacak bir ChatClient nesnesi oluştur
            ChatClient client = new("gpt-4o", apiKey);

            // 4. Görüntü dosyasının yolunu belirt
            string imageFilePath = Path.Combine("Assets", "images_dog_and_cat.png");

            // 5. Görüntü dosyasını okuyarak bir stream aç ve byte verisi olarak al
            using Stream imageStream = File.OpenRead(imageFilePath);
            BinaryData imageBytes = BinaryData.FromStream(imageStream);

            // 6. Kullanıcıdan gelen bir mesaj listesi oluştur ve resim verisini ekle
            List<ChatMessage> messages = new List<ChatMessage>
            {
                new UserChatMessage(
                    ChatMessageContentPart.CreateTextPart("Lütfen aşağıdaki görüntüyü tanımlar mısın?"),
                    ChatMessageContentPart.CreateImagePart(imageBytes, "image/png")
                ),
            };

            // 7. OpenAI'ye görüntü ve mesajı gönderip tamamlanmış bir yanıt al
            ChatCompletion completion = client.CompleteChat(messages);

            // 8. Asistanın yanıtını ekrana yazdır
            Console.WriteLine($"[ASİSTAN]: {completion.Content[0].Text}");
        }
    }
}
```

* **ConfigReader.ReadApiKeyFromConfig()** çağrısıyla, API anahtarı config.json dosyasından okunuyor. Bu anahtar, OpenAI API'sine erişim sağlamak için gereklidir.
* Eğer API anahtarı bulunamazsa (**string.IsNullOrEmpty(apiKey)** kontrolü), bir hata mesajı verilip işlem sonlandırılıyor. Bu, API anahtarının var olup olmadığını kontrol etmek için gerekli.
* **ChatClient client = new("gpt-4o", apiKey);** ile GPT-4o modelini kullanacak bir **ChatClient** nesnesi oluşturuluyor. Bu nesne, OpenAI ile etkileşim kurmak için kullanılıyor.
* **string imageFilePath = Path.Combine("Assets", "images_dog_and_cat.png");** ile analiz edilecek görüntünün yerel dosya sistemindeki yolunu belirtiyoruz.
* **File.OpenRead(imageFilePath)** ile belirtilen dosya açılır ve bir Stream oluşturulur. Daha sonra, bu stream'den BinaryData kullanarak byte verisi elde edilir **(BinaryData.FromStream(imageStream)).**
* **List<ChatMessage> messages** ile bir mesaj listesi oluşturuluyor. Bu listede kullanıcı tarafından sorulan bir soru ve bu soruya eklenen bir resim bulunuyor. **CreateTextPart** metodu ile sorulan soruyu ekliyoruz, **CreateImagePart** ile de byte verilerini resim olarak ekliyoruz.
* **ChatCompletion completion = client.CompleteChat(messages);** ile kullanıcı mesajlarını OpenAI'ye gönderiyoruz ve **ChatCompletion** nesnesi olarak asistanın yanıtını alıyoruz.
* **Console.WriteLine($"[ASİSTAN]: {completion.Content[0].Text}");** ile asistanın verdiği yanıt ekrana yazdırılıyor. Bu yanıt, görüntünün analizi ve açıklaması olacak.


**Koyulan Fotoğraf**

![{5617E11C-BF60-4CBA-8BE2-DD42970E145F}](https://github.com/user-attachments/assets/7cb732de-674b-4123-b18a-2e5bacceb5c2)


**Çıktı**

**[ASİSTAN]: Bu görüntüde bir köpek ve bir kedi birlikte yan yana duruyor. Köpek beyaz renkte ve kırmızı bir tasması var. Kedi ise kahverengi ve siyah çizgili kürke sahip. Her iki hayvan da oldukça sevimli görünüyor ve objektife doğru bakıyorlar.**

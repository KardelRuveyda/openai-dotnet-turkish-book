## OPEN AI ile Büyük Dil Modeli(LLM)

**Büyük Dil Modeli (LLM)** (ingilizcesi; Large Language Model), insan dilini anlamak ve üretmek için tasarlanmış bir tür yapay zeka modelidir. Bu modeller, büyük miktarda metin verisi üzerinde eğitilir ve çeviri, özetleme, soru cevaplama ve metin oluşturma gibi çeşitli dil ile ilgili görevleri gerçekleştirebilir. LLM örnekleri arasında Open AI tarafından GPT-4o, Google tarafından BERT ve Google tarafından T5 bulunur.

## OPEN AI Neden LLM Kullanır?
Nedenlerini şu şekilde sıralayabiliriz;

1. **Çok Yönlülük**: LLM'ler, metin oluşturma, çeviri, özetleme ve soru cevaplama gibi çok çeşitli dil görevlerini ele alabilir.
2. **Bağlam Anlama**: Bağlamı ve kelimeler arasındaki ilişkileri anlamada mükemmeldirler ve bu da daha doğru ve alakalı yanıtlar sağlar.
3. **Ölçeklenebilirlik**: LLM'ler, parametre sayısını artırarak performansı iyileştirmek için ölçeklendirilebilir.
4. **Ön Eğitim ve İnce Ayar**: Büyük veri kümeleri üzerinde önceden eğitilebilir ve belirli görevler için ince ayar yapılabilir, bu da onları son derece uyarlanabilir hale getirir.
5. **Transfer Öğrenme**: Ön eğitimden elde edilen bilgi, minimum ek veriyle yeni görevlere aktarılabilir.
6. **İnsan Benzeri Metin Oluşturma**: Sohbet robotları ve içerik oluşturma için yararlı olan, insan yazısını yakından taklit eden metinler üretirler.
7. **Araştırma ve Geliştirme**: LLM'ler, NLP ve AI araştırmalarının sınırlarını zorlamaya yardımcı olur. 
8. **Ticari Uygulamalar**: Müşteri desteği, otomatik içerik üretimi ve veri analizi gibi alanlarda çok sayıda pratik kullanımları vardır.

## Büyük Dil Modeli (LLM) Nasıl Çalışır?
GPT-3 ya da GPT-4o gibi Büyük Dil Modelleri (LLM'ler), insan dilini anlamak ve üretmek için derin öğrenme tekniklerinden faydalanır, özellikle de sinir ağlarından yararlanarak çalışır. Transformatörler (Transformers) adı verilen bir tür sinir ağı mimarisi kullanır. Bu mimari özellikle ardışık verileri işleme ve metindeki uzun menzilli bağımlılıkları yakalama konusunda iyi olduğu için tercih edilir.

Çalışma mantığını kısaca açıklayacak olursak; LLM'ler kitaplar, makaleler, web siteleri vb gibi eğitim verileri (training data) gibi çeşitli kaynaklardan gelen büyük miktarda metin verisi üzerinde eğitilir. Elde edilen Metin verileri, token adı verilen daha küçük birimlere ayrılır. Tokenlar; kelimeler, alt kelimeler veya hatta karakterler olabilir. Bu işleme tokenization denir. Eğitim sırasında, model önceki token'lar verildiğinde bir dizideki bir sonraki token'ı tahmin etmeyi öğrenir. Bu, eğitim verilerindeki tahmin edilen token'lar ile gerçek token'lar arasındaki farkı en aza indirmek için sinir ağının ağırlıklarını ayarlayarak yapılır. Transformers, tahminler yaparken modelin girdi metninin farklı bölümlerine odaklanmasına izin veren bir dikkat (Attention) mekanizması kullanır. Bu, modelin bağlamı ve kelimeler arasındaki ilişkileri anlamasına yardımcı olur. Daha sonra ilk eğitimden sonra, model belirli görevler veya veri kümeleri üzerinde ince ayar (Fine-tuning) yapılarak bu task'lerdeki performansının iyileştirilmesi sağlanabilir. Fine-tuning, modelin daha küçük, göreve özgü bir veri kümesi üzerinde daha fazla eğitilmesini içerir.Model eğitildikten sonra, çeşitli dil görevleri için kullanılabilir. Çıkarım (Inference) sırasında, model daha önce oluşturulan belirteçleri bağlam olarak kullanarak, her seferinde bir belirteç tahmin ederek metin üretir.

## OPEN AI'da LLM Kullanımı
GPT-4 gibi bir LLM'yi OpenAI kütüphanesi ile kullanmak için, genellikle API'ler üzerinden tüketerek kullanırız. Bunun aşağıdaki adımları izleyebilirsiniz.

### İşte kullanmak için yapmamız gerekenler;

1. **Kaydolun**: Öncelikle OpenAI platformunda bir hesap oluşturmakla başlayın.
2. **API Key Belirleyin**: Daha sonra OpenAI'dan bir API anahtarı edinmek gerekiyor.
3. **OpenAI SDK'sını Yükleyin**: OpenAI SDK'sını yüklemek için Visual Studio'daki NuGet Paket Manager'ı kullanabilirsiniz ya da package manager console'a "Install-Package OpenAI" komutu ile projenize ekleyebilirsiniz.
4. **API Çağrıları Yapın**: Son olarak aldığınız API key'ini kullanarak OpenAI endpoint'ine API call yapmak için aşağıdaki örnek .NET kodunu deneyebilirsiniz:

```csharp
using System;
using System.Threading.Tasks;
using OpenAI_API;

class Program
{
    static async Task Main(string[] args)
    {
        // Set your OpenAI API key
        string apiKey = "your-api-key";
        var openai = new OpenAIAPI(apiKey);

        // Create a request to the OpenAI API
        var request = new CompletionRequest
        {
            Prompt = "Translate the following English text to French: 'Hello, how are you?'",
            MaxTokens = 60,
            Engine = Engine.Davinci
        };

        // Get the response from the API
        var response = await openai.Completions.CreateCompletionAsync(request);

        // Print the response
        Console.WriteLine(response
        }
}

```



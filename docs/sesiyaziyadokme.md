## Sesi Yazıya Dökme

## 1. Gerekli Kütüphaneler ve Çevresel Değişkenler

İlk olarak, OpenAI API'yi kullanmak için gerekli olan **OpenAI.Audio** kütüphanesini dahil ediyoruz. Ayrıca OpenAI API anahtarımızı bir çevresel değişken olarak alıyoruz. Bu anahtar, OpenAI'nin API'larına erişim sağlamak için gereklidir.

```csharp
using OpenAI.Audio;

AudioClient client = new(model: "whisper-1", Environment.GetEnvironmentVariable("OPENAI_API_KEY"));
```

- **AudioClient:** OpenAI API'ye istek yapmak için kullanılan istemci nesnesi.
- **model:** **"whisper-1"**: Burada Whisper modelini kullanıyoruz. Bu model, OpenAI'nin ses tanıma (speech-to-text) modellerinden biridir.
- **Environment.GetEnvironmentVariable("OPENAI_API_KEY"):** API anahtarını çevresel değişkenlerden alır. Bu, güvenlik açısından önemlidir; API anahtarlarını kod içinde sabit olarak yazmak yerine, çevresel değişkenlerle kullanmak önerilir.

## 2. Ses Dosyasını Belirlemek

Ses dosyasının yolunu belirliyoruz. Bu örnekte ses dosyası **Assets** klasöründe yer alan **audio_houseplant_care.mp3** dosyasıdır. **Path.Combine** fonksiyonu kullanılarak platformdan bağımsız olarak dosya yolunu oluşturuyoruz.

```csharp
string audioFilePath = Path.Combine("Assets", "audio_houseplant_care.mp3");
```

## 3. Transkripsiyon Seçenekleri

Ses dosyasının nasıl işleneceğini belirlemek için **AudioTranscriptionOptions** sınıfını kullanıyoruz. Bu sınıf ile transkripsiyonun nasıl yapılacağını belirleyen birkaç seçenek ayarlayabiliyoruz.

```csharp
AudioTranscriptionOptions options = new()
{
    ResponseFormat = AudioTranscriptionFormat.Verbose,
    Granularities = AudioTimestampGranularities.Word | AudioTimestampGranularities.Segment,
};
```

* **ResponseFormat** = **AudioTranscriptionFormat.Verbose**: Bu, transkripsiyonun daha detaylı bir biçimde döndürüleceğini belirtir. Yani sadece metin değil, aynı zamanda kelime ve segment bazında zaman damgaları da döndürülür.
* **Granularities** = **AudioTimestampGranularities.Word | AudioTimestampGranularities.Segment**: Burada iki ayrı ayrıntı seviyesi belirliyoruz:
* **Word**: Her bir kelime için zaman damgası döndürülür.
* **Segment**: Her bir konuşma segmenti için zaman damgası döndürülür. Segmentler, konuşma sırasında duraklamalar olduğunda veya anlamlı bölümler oluşturulduğunda ayrılır.

## 4. Transkripsiyon İşlemi

Ses dosyasını transkribe etmek için **client.TranscribeAudio** metodunu kullanıyoruz. Bu metod, ses dosyasını alır ve belirlenen seçenekler doğrultusunda metne çevirir.

```csharp
AudioTranscription transcription = client.TranscribeAudio(audioFilePath, options);
```
**TranscribeAudio:** Bu metod, ses dosyasını transkribe eder ve sonuçları AudioTranscription nesnesi olarak döner.

## 5. Transkripsiyon Sonucunu Yazdırmak

```csharp
Console.WriteLine("Transcription:");
Console.WriteLine($"{transcription.Text}");
```

Bu aşamada, metin olarak dönen transkripsiyon sonucunu ekrana yazdırıyoruz.

**transcription.Text:** Bu özellik, tüm sesin metin olarak döndürülmüş halidir.

## 6. Kelime Bazlı Zaman Damgalarını Yazdırmak

Daha sonra, transkripsiyondaki her bir kelimenin başlangıç ve bitiş zamanlarını milisaniye cinsinden yazdırıyoruz.

```csharp
Console.WriteLine();
Console.WriteLine($"Words:");
foreach (TranscribedWord word in transcription.Words)
{
    Console.WriteLine($"  {word.Word,15} : {word.Start.TotalMilliseconds,5:0} - {word.End.TotalMilliseconds,5:0}");
}

```

* **transcription.Words:** Bu özellik, her bir kelimenin zaman damgalarını içeren bir koleksiyon döner.
  * **word.Word**: Kelimenin kendisini ifade eder.
  * **word.Start.TotalMilliseconds**: Kelimenin ses dosyasında başladığı zamanı milisaniye cinsinden verir.
  * **word.End.TotalMilliseconds**: Kelimenin ses dosyasında bittiği zamanı milisaniye cinsinden verir.
 
## 7. Segment Bazlı Zaman Damgalarını Yazdırmak

Son olarak, konuşma segmentlerinin başlangıç ve bitiş zamanlarını milisaniye cinsinden yazdırıyoruz.

```csharp
Console.WriteLine();
Console.WriteLine($"Segments:");
foreach (TranscribedSegment segment in transcription.Segments)
{
    Console.WriteLine($"  {segment.Text,90} : {segment.Start.TotalMilliseconds,5:0} - {segment.End.TotalMilliseconds,5:0}");
}
```

* **transcription.Segments**: Bu özellik, ses dosyasında tespit edilen konuşma segmentlerini içerir.
  * **segment.Text**: Segmentin içeriğini, yani konuşulan metni döner.
  * **segment.Start.TotalMilliseconds**: Segmentin ses dosyasında başladığı zaman.
  * **segment.End.TotalMilliseconds**: Segmentin ses dosyasında bittiği zaman.
 
Bu örnekte bir ses dosyasını **Whisper** modeli ile metne dönüştürüp hem kelime hem de segment bazında zaman damgası bilgilerini elde ettik. Bu bilgiler sayesinde sadece sesin ne zaman başladığını ve bittiğini değil, aynı zamanda her bir kelimenin ne zaman söylendiğini ve hangi segmentte olduğunu görebiliyoruz. Whisper modeli, bu tür detaylı transkripsiyonlar için güçlü bir araçtır ve OpenAI'nin API'leri ile kolayca entegre edilebilir.

Eğer birden fazla dosya üzerinde çalışmayı planlıyorsan, her dosya için benzer bir yapı oluşturabilir ve transkripsiyon işlemlerini bu yapı içinde yönetebilirsin.

## Yapılan Örneğin Önemi

**Speech-to-Text (Sesli İletiden Metne)** ve **Text-to-Speech (Metinden Sese)** teknolojileri, modern dijital dünyada sesli ve yazılı iletişim arasında köprü kuran önemli unsurlardır. Bu teknolojilerin her ikisi de günlük yaşamda, iş dünyasında ve çeşitli sektörlerde geniş bir kullanım alanına sahiptir ve her geçen gün daha fazla entegre edilmektedir. Speech-to-Text teknolojisi, sesli konuşmayı otomatik olarak metne çevirir. Bu teknolojinin önemi, öncelikle erişilebilirlik açısından kendini gösterir. Örneğin, işitme engelli bireyler veya sessiz bir ortamda çalışanlar için konuşmaların otomatik olarak metne dönüştürülmesi büyük bir avantajdır. Canlı altyazı uygulamaları, konferanslar veya derslerde konuşmaları anlık olarak ekrana yansıtabilir, bu da bilgiye erişimi kolaylaştırır. Bunun yanı sıra, konuşarak metin üretmek, verimlilik sağlar. Özellikle iş dünyasında, dikte yoluyla not almak, rapor yazmak veya belgeleri hızlı bir şekilde hazırlamak için bu teknoloji yaygın olarak kullanılır. Sesli veri metne dönüştürüldüğünde, arama yapılabilir, analiz edilebilir ve dijital ortamda arşivlenebilir hale gelir. Özellikle müşteri hizmetleri çağrıları, toplantı kayıtları veya video içerikleri bu yolla metin formatına çevrilerek çok daha kolay işlenebilir ve organize edilebilir. Ayrıca, doğal dil işleme (NLP) sistemleriyle entegre çalışan **Speech-to-Text** teknolojisi, metne dönüştürülen konuşmaların anlamlandırılmasını ve analiz edilmesini sağlar. Örneğin, müşteri hizmetlerine gelen sesli talepler bu teknoloji ile otomatik olarak metne dönüştürülüp analiz edilebilir, böylece hızlı çözümler sunulabilir. Bu teknolojinin bir diğer önemli yönü ise yapay zeka ve büyük veri işleme ile sağladığı olanaklardır. Ses verilerinin metne dönüştürülmesi, bu verilerin yapay zeka ile analiz edilmesini ve örneğin müşteri memnuniyetinin ölçülmesi gibi farklı alanlarda kullanılmasını mümkün kılar. Text-to-Speech (Metinden Sese) ise metin tabanlı içeriklerin sesli hale getirilmesini sağlar. Bu teknoloji, özellikle görme engelli bireyler için veya gözlerini ekrandan ayırmadan bilgiye ulaşmak isteyenler için önemlidir. Bu teknolojiler, günlük hayatı kolaylaştırmanın ötesinde, dijital dünyada bilgiye erişimi demokratikleştirir, iş süreçlerini hızlandırır ve verimliliği artırır.

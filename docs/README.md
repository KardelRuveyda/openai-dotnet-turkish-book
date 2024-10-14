# OpenAI .NET API: Türkçe Kitap

OpenAI .NET kütüphanesi, .NET uygulamalarında OpenAI'nin REST API'sine pratik bir erişim imkânı sunmaktadır. Bu kütüphane, Microsoft ile ortak çalışarak OpenAPI spesifikasyonuna dayanılarak oluşturulmuştur.

Bu kitap, https://github.com/openai/openai-dotnet ve https://learn.microsoft.com/tr-tr/dotnet/ai/quickstarts/get-started-openai?tabs=azd&pivots=openai dokümanları temel alınarak hazırlanmıştır. İçerideki kavramlar detaylıca incelenip Türkçe bir kaynak eksikliğini gidermek amacıyla, geliştiricilere rehber olacak bir formatta bu repoya taşınmıştır. Amacımız, Türk geliştiricilere OpenAI API'nin .NET ortamında nasıl etkili kullanılabileceği konusunda kapsamlı ve erişilebilir bir bilgi kaynağı sunmaktır.

https://kardelruveyda.github.io/openai-dotnet-turkish-book


# İçindekiler


1. [Başlarken](./docs/baslarken.md)
   
* [Ön Koşullar](baslarken.md#Ön-koşullar)
* [NuGet Paketini Kurma](baslarken.md#nuget-paketini-kurma)
* [İstemci Kütüphanesini Kullanma](baslarken.md#İstemci-Kutuphanesini-Kullanma)
* [Namespace Organizasyonu](baslarken.md#Namespace-Organizasyonu)
* [Async API'yi Kullanma](baslarken.md#Async-API-Kullanma)
* [OpenAIClient Sınıfını Kullanma](baslarken.md#openaiclient-sinifini-kullanma)
* [OpenAIClient Nedir ve Neden Kullanılır?](baslarken.md#OpenAIClient-Nedir-Ve-Neden-Kullanilir)
* [OpenAIClient Nasıl Kullanılır?](baslarken.md#openaiclient-nasil-kullanilir)
     

2. [Sohbet Tamamlama İşlemleri](sohbettamamlama.md#sohbet-tamamlama-islemleri)

* [Sohbet Tamamlamada Streaming Kullanımı](sohbettamamlama.md#sohbet-tamamlamada-streaming-kullanimi)
* [Neden Streaming Kullanılmalı?](sohbettamamlama.md#neden-streaming-kullanılmalı)
  
3.[Araçlar ve işlev çağrısıyla sohbet tamamlamaları nasıl kullanılır?](araclarlasohbettamamlama.md#araçlar-ve-işlev-çağrısıyla-sohbet-tamamlamaları-nasıl-kullanılır)

* [Adım 1: Fonksiyonları Tanımlama](araclarlasohbettamamlama.md#adım-1-fonksiyonları-tanımlama)
* [Adım 2: Araçları Tanımlama](araclarlasohbettamamlama.md#adım-2-araçları-tanımlama)
* [Adım 3: Chat Tamamlayıcı Seçeneklerini Oluşturma](araclarlasohbettamamlama.md#adım-3-chat-tamamlayıcı-seçeneklerini-oluşturma)
* [Adım 4: Fonksiyon Çağrısı ve Yanıt Döngüsü](araclarlasohbettamamlama.md#adım-4-fonksiyon-çağrısı-ve-yanıt-döngüsü)
* [Adım 5 Yaptıklarımızı Konsol Uygulmasına Dönüştürelim!](araclarlasohbettamamlama.md#adım-5-yaptıklarımızı-konsol-uygulmasına-dönüştürelim)
   * [Run() fonksiyonu oluşturalım!](araclarlasohbettamamlama.md#run-fonksiyonu-oluşturalım)
   * [Kullanıcıdan Veri Alma](araclarlasohbettamamlama.md#kullanıcıdan-veri-alma)
   * [Kullanıcı Mesajını Listeye Ekleme](araclarlasohbettamamlama.md#kullanıcı-mesajını-listeye-ekleme)
   * [Araçların Tanımlanması](araclarlasohbettamamlama.md#araçların-tanımlanması)
   * [API Anahtarının Okunması](./docs/araclarlasohbettamamlama.md#api-anahtarının-okunması)
   * [ChatClient Oluşturma](araclarlasohbettamamlama.md#chatclient-oluşturma)
   * [Döngü ile Model Yanıtını İşleme](araclarlasohbettamamlama.md#döngü-ile-model-yanıtını-i̇şleme)
   * [Modelin Yanıtına Göre İşlemler](araclarlasohbettamamlama.md#modelin-yanıtına-göre-İşlemler)
   * [Araç Çağrısı Yapıldığında](araclarlasohbettamamlama.md#araç-çağrısı-yapıldığında)
   * [Hata Durumları](araclarlasohbettamamlama.md#hata-durumları)
   * [Mesajları Konsola Yazdırma](araclarlasohbettamamlama.md#mesajları-konsola-yazdırma)
   * [Ve ta daa bütün kod!](araclarlasohbettamamlama.md#ve-ta-daa-bütün-kod)


4. [Yapılandırılmış Çıktılar Nasıl Kullanılır](yapilandirilmiscikti.md#yapılandırılmış-çıktılar-nasıl-kullanılır)

* [Yapılandırılmış Çıktıların Kullanımı](yapilandirilmiscikti.md#yapılandırılmış-çıktıların-kullanımı)
* [Yapılandırılmış Çıktı Örneği](yapilandirilmiscikti.md#yapılandırılmış-çıktı-örneği)
* [Sohbet Tamamlama ve Yapılandırılmış Yanıtın Kullanımı](/yapilandirilmiscikti.md#sohbet-tamamlama-ve-yapılandırılmış-yanıtın-kullanımı)
* [Yapılandırılmış Çıktıların Avantajları](yapilandirilmiscikti.md#yapılandırılmış-çıktıların-avantajları)

5. [Metin Gömmeleri (Text Embeddings)](textembedding.md#metin-gömmeleri-text-embeddings)

* [Metin Gömmelerinin Kullanımı](textembedding.md#metin-gömmelerinin-kullanımı)
* [Embedding Boyutlarını Ayarlama](textembedding.md#embedding-boyutlarını-ayarlama)

6. [Görüntü Üretme İşlemleri](goruntuolusturma.md#görüntü-üretimi)

* [Görüntü Üretimi Nasıl Yapılır?](goruntuolusturma.md#görüntü-üretimi-nasıl-yapılır)
* [Görüntü Üretmek için Gerekenler](goruntuolusturma.md#görüntü-üretmek-için-gerekenler)
* [Adım 1 : Kütüphanenin Kullanılması](goruntuolusturma.md#adım-1--kütüphanenin-kullanılması)
* [Adım 2 : ImageClient Oluşturma](goruntuolusturma.md#adım-1--kütüphanenin-kullanılması)
* [Adım 3 : Prompt Tanımlama](goruntuolusturma.md#adım-3--prompt-tanımlama)
* [Adım 4 : Görüntü Üretim Seçeneklerini Tanımlama](goruntuolusturma.md#adım-4--görüntü-üretim-seçeneklerini-tanımlama)
* [Adım 5 :Görüntü Üretme](goruntuolusturma.md#adım-5-görüntü-üretme)
* [Kodunu Birlikte Yazalım](goruntuolusturma.md#kodunu-birlikte-yazalım)
* [Konsol Bilgisi ve Oluşturulan Fotoğraf](goruntuolusturma.md#konsol-bilgisi-ve-oluşturulan-fotoğraf)


7. [Sesi Yazıya Dökme](sesiyaziyadokme.md#sesi-yazıya-dökme)

* [Speech-to-Text (STT) Araştırmaları](sesiyaziyadokme.md#speech-to-text-stt-araştırmaları)
* [Text-to-Speech (TTS) Araştırmaları](sesiyaziyadokme.md#text-to-speech-tts-araştırmaları)
* [1. Gerekli Kütüphaneler ve Çevresel Değişkenler](sesiyaziyadokme.md#2-ses-dosyasını-belirlemek)
* [2. Ses Dosyasını Belirlemek](sesiyaziyadokme.md#2-ses-dosyasını-belirlemek)
* [3. Transkripsiyon Seçenekleri](sesiyaziyadokme.md#3-transkripsiyon-seçenekleri)
* [4. Transkripsiyon İşlemi](sesiyaziyadokme.md#4-transkripsiyon-i̇şlemi)
* [5. Transkripsiyon Sonucunu Yazdırmak](sesiyaziyadokme.md#6-kelime-bazlı-zaman-damgalarını-yazdırmak)
* [6. Kelime Bazlı Zaman Damgalarını Yazdırmak](sesiyaziyadokme.md#6-kelime-bazlı-zaman-damgalarını-yazdırmak)
* [7. Segment Bazlı Zaman Damgalarını Yazdırmak](sesiyaziyadokme.md#7-segment-bazlı-zaman-damgalarını-yazdırmak)
* [Yapılan Örneğin Önemi](sesiyaziyadokme.md#yapılan-örneğin-önemi)


8. [Open AI API Rag Kullanımı](rag.md#sesi-yazıya-dökme)

* [RAG (Retrieval-Augmented Generation) Temel Kavramları](rag.md#rag-retrieval-augmented-generation-temel-kavramları)
* [Dil Modelleri ve Vektörler](rag.md#dil-modelleri-ve-vektörler)
* [Vektörlerin Amacı Nedir?](rag.md#vektörlerin-amacı-nedir)
* [Vektör Depolama Nedir?](rag.md#vektör-depolama-nedir)
* [Neden Vektör Depolama Kullanıyoruz?](rag.md#neden-vektör-depolama-kullanıyoruz)
* [Vektör Depolama Süreci Detaylı İnceleme](rag.md#vektör-depolama-süreci-detaylı-İnceleme)
* [Koddaki Vektör Depolama Örneği](rag.md#koddaki-vektör-depolama-Örneği)
* [Vektör Depolamanın Gerçek Hayattaki Uygulaması](rag.md#vektör-depolamanın-gerçek-hayattaki-uygulaması)
* [Asistanları Kullanarak RAG (Bilgi Getirme ile Zenginleştirilmiş Üretim) Nasıl Kullanılır?](rag.md#asistanları-kullanarak-rag-bilgi-getirme-ile-zenginleştirilmiş-Üretim-nasıl-kullanılır)
* [Yeni Bir Asistan Oluşturmak](rag.md#yeni-bir-asistan-oluşturmak)
* [Bir İleti Dizisi Oluşturmak](rag.md#bir-İleti-dizisi-oluşturmak)
* [Yanıt Mesajlarını Almak](rag.md#yanıt-mesajlarını-almak)
* [Birlikte Uygulama Yapalım!](rag.md#birlikte-uygulama-yapalım)

9. [Asistanlarla akış ve GPT-4o görüşü nasıl kullanılır?](vision.md#asistanlarla-akış-ve-gpt-4o-görüşü-nasıl-kullanılır)
* [Vision Teknolojisinin Amacı ve Faydaları](vision.md#vision-teknolojisinin-amacı-ve-faydaları)
* [GPT-4o Vision'un Özellikleri ve Kullanım Alanları](vision.md#gpt-4o-visionun-özellikleri-ve-kullanım-alanları)
* [Bir örnekle pekiştirelim!](vision.md#bir-örnekle-pekiştirelim)
     

![image](https://github.com/user-attachments/assets/9a71d76d-15a7-45e9-8bf9-78f6ead3a3cc)


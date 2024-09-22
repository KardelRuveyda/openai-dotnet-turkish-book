# OpenAI .NET API: Türkçe Kitap

OpenAI .NET kütüphanesi, .NET uygulamalarında OpenAI'nin REST API'sine pratik bir erişim imkânı sunmaktadır. Bu kütüphane, Microsoft ile ortak çalışarak OpenAPI spesifikasyonuna dayanılarak oluşturulmuştur.

Bu kitap, https://github.com/openai/openai-dotnet ve https://learn.microsoft.com/tr-tr/dotnet/ai/quickstarts/get-started-openai?tabs=azd&pivots=openai dokümanları temel alınarak hazırlanmıştır. İçerideki kavramlar detaylıca incelenip Türkçe bir kaynak eksikliğini gidermek amacıyla, geliştiricilere rehber olacak bir formatta bu repoya taşınmıştır. Amacımız, Türk geliştiricilere OpenAI API'nin .NET ortamında nasıl etkili kullanılabileceği konusunda kapsamlı ve erişilebilir bir bilgi kaynağı sunmaktır.

https://kardelruveyda.github.io/openai-dotnet-turkish-book


# İçindekiler

1. [Başlarken](./docs/baslarken.md)
* [Ön Koşullar](./docs/baslarken.md#Ön-koşullar)
* [NuGet Paketini Kurma](./docs/baslarken.md#nuget-paketini-kurma)
* [İstemci Kütüphanesini Kullanma](./docs/baslarken.md#İstemci-Kutuphanesini-Kullanma)
* [Namespace Organizasyonu](./docs/baslarken.md#Namespace-Organizasyonu)
* [Async API'yi Kullanma](./docs/baslarken.md#Async-API-Kullanma)
* [OpenAIClient Sınıfını Kullanma](./docs/baslarken.md#openaiclient-sinifini-kullanma)
* [OpenAIClient Nedir ve Neden Kullanılır?](./docs/baslarken.md#OpenAIClient-Nedir-Ve-Neden-Kullanilir)
* [OpenAIClient Nasıl Kullanılır?](./docs/baslarken.md#openaiclient-nasil-kullanilir)
     

2. [Sohbet Tamamlama İşlemleri](./docs/sohbettamamlama.md#sohbet-tamamlama-islemleri)
* [Sohbet Tamamlamada Streaming Kullanımı](./docs/sohbettamamlama.md#sohbet-tamamlamada-streaming-kullanimi)
* [Neden Streaming Kullanılmalı?](./docs/sohbettamamlama.md#neden-streaming-kullanılmalı)
  
3.[Araçlar ve işlev çağrısıyla sohbet tamamlamaları nasıl kullanılır?](./docs/araclarlasohbettamamlama.md#araçlar-ve-işlev-çağrısıyla-sohbet-tamamlamaları-nasıl-kullanılır)
* [Adım 1: Fonksiyonları Tanımlama](./docs/araclarlasohbettamamlama.md#adım-1-fonksiyonları-tanımlama)
* [Adım 2: Araçları Tanımlama](./docs/araclarlasohbettamamlama.md#adım-2-araçları-tanımlama)
* [Adım 3: Chat Tamamlayıcı Seçeneklerini Oluşturma](./docs/araclarlasohbettamamlama.md#adım-3-chat-tamamlayıcı-seçeneklerini-oluşturma)
* [Adım 4: Fonksiyon Çağrısı ve Yanıt Döngüsü](./docs/araclarlasohbettamamlama.md#adım-4-fonksiyon-çağrısı-ve-yanıt-döngüsü)
* [Adım 5 Yaptıklarımızı Konsol Uygulmasına Dönüştürelim!](./docs/araclarlasohbettamamlama.md#adım-5-yaptıklarımızı-konsol-uygulmasına-dönüştürelim)
   * [Run() fonksiyonu oluşturalım!](./docs/araclarlasohbettamamlama.md#run-fonksiyonu-oluşturalım)
   * [Kullanıcıdan Veri Alma](./docs/araclarlasohbettamamlama.md#kullanıcıdan-veri-alma)
   * [Kullanıcı Mesajını Listeye Ekleme](./docs/araclarlasohbettamamlama.md#kullanıcı-mesajını-listeye-ekleme)
   * [Araçların Tanımlanması](./docs/araclarlasohbettamamlama.md#araçların-tanımlanması)
   * [API Anahtarının Okunması](./docs/araclarlasohbettamamlama.md#api-anahtarının-okunması)
   * [ChatClient Oluşturma](./docs/araclarlasohbettamamlama.md#chatclient-oluşturma)
   * [Döngü ile Model Yanıtını İşleme](./docs/araclarlasohbettamamlama.md#döngü-ile-model-yanıtını-i̇şleme)
   * [Araç Çağrısı Yapıldığında](./docs/araclarlasohbettamamlama.md#araç-çağrısı-yapıldığında)
   * [Hata Durumları](./docs/araclarlasohbettamamlama.md#hata-durumları)
   * [Mesajları Konsola Yazdırma](./docs/araclarlasohbettamamlama.md#mesajları-konsola-yazdırma)
   * [Ve ta daa bütün kod!](./docs/araclarlasohbettamamlama.md#ve-ta-daa-bütün-kod)

3. [Görseller Oluşturma](./docs/goruntuler.md)

4. [Ses Dosyalarını Metne Dönüştürme](./docs/sesdosyasi.md)

5. [Bilgi Alma ile Artırılmış Üretim (RAG) ile Asistan Kullanma](./docs/rag-asistan.md)

6. [Akış ve GPT-4o Vizyonu ile Asistanları Kullanma](./docs/gpt4o.md)

7. [Azure OpenAI ile Çalışma](./docs/azure-openai.md)

8. [Gelişmiş Senaryolar](./docs/gelismis-senaryolar.md)
   - [Protokol Yöntemlerini Kullanma](./docs/gelismis-senaryolar.md#protokol-yontemleri)
   - [Hataları Otomatik Yeniden Deneme](./docs/gelismis-senaryolar.md#hatalari-yeniden-deneme)
   - [Gözlemlenebilirlik (Observability)](./docs/gelismis-senaryolar.md#gozlemlenebilirlik)


![image](https://github.com/user-attachments/assets/9a71d76d-15a7-45e9-8bf9-78f6ead3a3cc)


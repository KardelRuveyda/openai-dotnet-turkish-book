## Yapılandırılmış Çıktılar Nasıl Kullanılır?

Yapılandırılmış çıktılar, bir dil modelinden alınan yanıtların tutarlı ve işlenebilir olmasını sağlar. Model, yalnızca serbest metin döndürmek yerine, geliştirici tarafından belirlenen bir JSON şemasıyla yanıt verir. Bu, modelin verdiği yanıtların hem doğru formatta olmasını hem de içerik bakımından daha düzenli bir yapıya sahip olmasını sağlar. Örneğin, bu kodda bir matematik probleminin çözüm adımlarını ve nihai cevabı düzenli bir şekilde adım adım ve sonuca ulaşacak biçimde döndürür.

## Yapılandırılmış Çıktıların Kullanımı
Bir sohbette (chat completion) yapılandırılmış çıktı kullanmak istiyorsanız, yanıt formatını belirlemek için **ChatResponseFormat** kullanabilirsiniz. Aşağıdaki örnekte, matematiksel bir problemi çözme adımlarını içeren bir JSON şeması tanımlanmıştır. Bu şema, bir yanıtın **"steps" (adımlar)** ve **"final_answer" (nihai cevap)** olmak üzere iki anahtar özellik içermesini sağlar. Her bir adım ise açıklama **("explanation")** ve çıktıyı **("output")** içeren bir alt nesne olur.


## Yapılandırılmış Çıktı Örneği

İlk olarak, bir **ChatCompletionOptions** nesnesi oluşturuyoruz ve burada **ResponseFormat** özelliğini kullanarak JSON şemasını tanımlıyoruz. Şema, yanıtın hangi türde ve formatta olacağını belirler. Bu örnekte matematiksel bir problemi adım adım çözen bir model yanıtı hedeflenmektedir.

```csharp
        ChatCompletionOptions options = new()
        {
            ResponseFormat = ChatResponseFormat.CreateJsonSchemaFormat(
                jsonSchemaFormatName: "math_reasoning",
                jsonSchema: BinaryData.FromBytes("""
                    {
                        "type": "object",
                        "properties": {
                        "steps": {
                            "type": "array",
                            "items": {
                            "type": "object",
                            "properties": {
                                "explanation": { "type": "string" },
                                "output": { "type": "string" }
                            },
                            "required": ["explanation", "output"],
                            "additionalProperties": false
                            }
                        },
                        "final_answer": { "type": "string" }
                        },
                        "required": ["steps", "final_answer"],
                        "additionalProperties": false
                    }
                    """u8.ToArray()),
                jsonSchemaIsStrict: true),

        };
```
Bu şemada, modelin yanıtı iki ana bileşenden oluşur: **steps (adımlar)** ve **final_answer (nihai sonuç)**. **Steps**, modelin yanıtını adım adım açıklayan bir dizi içerir. Bu dizi, her bir adımı açıklayan ve sonucu (çıktıyı) gösteren nesnelerden oluşur. Her adımda iki anahtar özellik bulunur: **"explanation"** **(açıklama) **ve **"output" (çıktı)**. **"Explanation"**, adımın neden ve nasıl yapıldığını açıklarken, **"output"** o adımdan elde edilen sonucu gösterir. Örneğin, matematiksel bir problem çözümünde, her bir adım modelin problemi nasıl çözdüğünü açıklayan ve o adımdaki sonucu gösteren verilerle sunulacaktır. Bu yapı, modelin yanıtını yalnızca nihai bir sonuçla sınırlamaz, aynı zamanda bu sonuca nasıl ulaşıldığını adım adım açıklar. **Final_answer** ise modelin ürettiği nihai sonucu içerir. Bu, tüm adımlar tamamlandıktan sonra elde edilen son cevaptır ve yanıtın en kritik kısmını temsil eder. Bu yapı, kullanıcının modeli yalnızca bir yanıt almak için değil, aynı zamanda o yanıtın mantığını ve çözüm sürecini anlayabilmesi için de kullanmasına olanak tanır.

* **ChatCompletionOptions** sınıfı, dil modeline yapılacak sohbet tamamlama isteği için çeşitli ayarları yapılandırmanıza olanak tanır. Bu örnekte, options değişkeni yanıtın formatını ve içeriğini belirleyen ayarları içerir.
* Bu ayar, dil modelinin yanıtının hangi formatta döneceğini belirler. Burada **ChatResponseFormat.CreateJsonSchemaFormat** kullanılarak yanıtın belirli bir **JSON** şemasına uygun olarak döndürülmesi isteniyor.
* **CreateJsonSchemaFormat** fonksiyonu içine, yanıtın belirli bir şemaya uygun olması gerektiği belirtmemiz gerekiyor. Şema da şu kuralları içeriyor:
    *  JSON Şeması İsmi: **"math_reasoning"** adında bir şema kullanılıyor. Bu isim, matematiksel bir problemin adım adım çözülmesi için bir yapı olduğunu ifade ediyor.
    *  **steps**: Bir dizi (array) olup, her adım bir nesne (object) olarak tanımlanmıştır. Her nesne, iki zorunlu özelliğe sahip:
        * **explanation**: Bu, bir açıklama metni olacaktır ve string türündedir.
        * **output**: Bu, adımın sonucunu temsil eden bir string'dir.
    * **final_answer**: Nihai cevabı temsil eden bir string'dir.

**Şema ayrıca şu kuralları belirler:**
* **required:** Bu kısım, hangi özelliklerin zorunlu olduğunu belirtir. Yanıtın, mutlaka **steps** ve **final_answer** özelliklerini içermesi gerekir.
* **additionalProperties:** false: Yanıtta şemada tanımlanmayan başka özelliklerin yer almasına izin verilmediğini ifade eder. Yani yanıt sadece **steps** ve **final_answer** özelliklerini içerebilir.
* **BinaryData.FromBytes()** ile **JSON** şeması, byte dizisine çevrilerek **BinaryData.FromBytes** metoduyla API'ye gönderilir. Bu, modelin yanıtının belirli bir formatta döndürülmesini sağlar.
* **jsonSchemaIsStrict:** true ayarı ile yanıtın şemaya tam olarak uymasını zorunlu kılar. Yani model, şemada belirtilen özelliklerden herhangi birini atlayamaz ya da ekstra özellik ekleyemez.


## Sohbet Tamamlama ve Yapılandırılmış Yanıtın Kullanımı

Bu yapılandırılmış yanıtı kullanarak modelden bir problem çözümü istiyoruz. Aşağıdaki kod, **"8x - 7 = 17 nasıl çözülür?"** sorusunu modelin yanıtlamasını sağlar:

```csharp
ChatCompletion completion = await client.CompleteChatAsync(messages, options);
```

* **client.CompleteChatAsync:** Bu satır, OpenAI API'ye bir sohbet tamamlama isteği gönderir.
* **messages**: Kullanıcıdan gelen sohbet mesajlarıdır. Örneğin, bir matematik problemi gibi modelin çözmesini istediğiniz soruyu içerir.
* **options**: Yanıtın nasıl döneceğini belirleyen seçenekleri içerir. Bu seçenekler arasında yanıtın belirli bir JSON şeması ile döndürülmesi gerektiği gibi yapılandırmalar olabilir.
* **await**: Bu işlem asenkron olarak çalışır, yani yanıt geldiğinde kod yürütülmeye devam eder.
Sonuç olarak, completion nesnesi **OpenAI API**'den gelen yanıtı içerir. Bu yanıt, yapılandırılmış **JSON** formatında döner.

```csharp
using JsonDocument structuredJson = JsonDocument.Parse(completion.Content[0].Text);
```

* **JsonDocument.Parse**: API'den gelen yanıt completion.Content[0].Text şeklinde düz metin olarak döner. Bu metin, JSON formatında olduğu için JsonDocument.Parse metodu kullanılarak JSON yapısına dönüştürülür. Bu, JSON formatındaki yanıtı işlemek için bir veri yapısı sağlar.
* **completion.Content[0].Text**: Bu kısım, yanıtın ilk parçasındaki metni temsil eder. OpenAI API, genellikle birden fazla içerik döndürebileceği için burada [0] ile ilk içeriğe erişiyoruz.

```csharp
Console.WriteLine($"Final answer: {structuredJson.RootElement.GetProperty("final_answer").GetString()}");
```

* **GetProperty("final_answer"):** JSON yanıtının kök öğesinden (root element) final_answer (nihai cevap) alanına erişir. Bu alan, çözümün nihai sonucunu içerir.
* **GetString():** JSON'daki bu alanın bir string olduğunu belirtir ve değeri alır.
* **Console.WriteLine:** Nihai cevap konsola yazdırılır.

```csharp
Console.WriteLine("Reasoning steps:");
foreach (JsonElement stepElement in structuredJson.RootElement.GetProperty("steps").EnumerateArray())
{
    Console.WriteLine($"  - Explanation: {stepElement.GetProperty("explanation").GetString()}");
    Console.WriteLine($"    Output: {stepElement.GetProperty("output").GetString()}");
}
``` 

**GetProperty("steps"):** JSON yanıtındaki **steps** (adımlar) özelliğine erişir. Bu özellik, çözümün her adımını açıklayan bir dizi içerir.
**EnumerateArray():** **steps** alanı bir dizi olduğu için her bir adımı tek tek ele almak için bu metot kullanılır.
**foreach döngüsü:** Adımları sırayla döngü ile işleriz. Her bir adım (**step**), bir explanation (**açıklama**) ve output (**çıktı**) içerir.
**stepElement.GetProperty("explanation").GetString():** Her adımın açıklamasını almak için **explanation** alanına erişilir.
**stepElement.GetProperty("output").GetString():** Her adımın çıktısını almak için **output** alanına erişilir.

Bu kod, OpenAI API'den gelen yapılandırılmış bir JSON yanıtı alır ve bu yanıtı adım adım çözüm süreci ve nihai cevap şeklinde işler. İlk olarak, OpenAI'ye yapılan istek sonucu bir yanıt alınır, bu yanıt JSON formatına ayrıştırılır ve JSON'dan **"steps" (adımlar)** ve **"final_answer" (nihai cevap)** bilgileri çıkarılarak ekrana yazdırılır.


**Çıktı:**


![ezgif-4-e1effd4b1e](https://github.com/user-attachments/assets/a0785e65-8608-4e66-9b5e-21d6172732fb)



## Yapılandırılmış Çıktıların Avantajları

Yapılandırılmış çıktılar, karmaşık problemlerin çözümünde düzenli ve adım adım ilerleyen yanıtlar sunarak, hem kullanıcılar hem de geliştiriciler için daha anlaşılır ve takip edilebilir bir çözüm süreci sağlar. Modelin verdiği yanıtlar belirli bir formatta düzenlendiği için, özellikle çok adımlı problemlerde her bir adımın açıklaması ve o adımdan elde edilen çıktı net bir şekilde gösterilir. Bu, yanıtların daha yapılandırılmış olmasını ve gereksiz bilgilerin devre dışı bırakılmasını sağlar, çünkü şema kullanarak yalnızca gerekli alanların yanıt içinde bulunmasını zorunlu kılabilirsiniz. Ayrıca, yapılandırılmış çıktılar sayesinde modelin yanıtları belirli bir şemaya veya kurallara uyarak oluşturulduğu için yanıtların doğruluğu artar; bu da tutarlı ve güvenilir sonuçların elde edilmesine yardımcı olur. Yanıtların doğruluğu, şemanın zorunlu kıldığı alanlar sayesinde, modelin mantıklı ve eksiksiz bir çözüm sunmasını sağlar. Sonuç olarak, yapılandırılmış çıktılar, modelin yanıtlarını daha düzenli, esnek ve doğruluğu yüksek hale getirerek kullanıcı deneyimini iyileştirir ve özellikle **teknik** ve **matematiksel** problemlerde çok büyük faydalar sağlar.



[Kodu incelemek için buraya tıklayabilirsiniz.](https://github.com/KardelRuveyda/openai-dotnet-exercises/tree/master/Examples/04)





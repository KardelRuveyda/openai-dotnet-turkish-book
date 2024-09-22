## Yapılandırılmış Çıktılar Nasıl Kullanılır?

GPT-4 modelleriyle (örneğin, **gpt-4o-mini**, **gpt-4o-mini-2024-07-18** ve **gpt-4o-2024-08-06**) yapılandırılmış çıktılar, hem üst düzey yanıt içerikleri hem de araç çağrıları için kullanılabilir hale geldi. Bu yapılandırılmış çıktı özelliği, yanıtların belirli bir formatta döndürülmesini sağlar ve özellikle karmaşık veya çok adımlı işlemler için büyük fayda sunar.

## Yapılandırılmış Çıktıların Kullanımı
Bir sohbette (chat completion) yapılandırılmış çıktı kullanmak istiyorsanız, yanıt formatını belirlemek için **ChatResponseFormat** kullanabilirsiniz. Aşağıdaki örnekte, matematiksel bir problemi çözme adımlarını içeren bir JSON şeması tanımlanmıştır. Bu şema, bir yanıtın **"steps" (adımlar)** ve **"final_answer" (nihai cevap)** olmak üzere iki anahtar özellik içermesini sağlar. Her bir adım ise açıklama **("explanation")** ve çıktıyı **("output")** içeren bir alt nesne olur.


## Yapılandırılmış Çıktı Örneği

İlk olarak, bir **ChatCompletionOptions** nesnesi oluşturuyoruz ve burada **ResponseFormat** özelliğini kullanarak JSON şemasını tanımlıyoruz. Şema, yanıtın hangi türde ve formatta olacağını belirler. Bu örnekte matematiksel bir problemi adım adım çözen bir model yanıtı hedeflenmektedir.

```csharp
ChatCompletionOptions options = new()
{
    ResponseFormat = ChatResponseFormat.CreateJsonSchemaFormat(
        name: "math_reasoning",
        jsonSchema: BinaryData.FromString("""
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
        """),
        strictSchemaEnabled: true)
};
```
Bu şemada, modelin yanıtı iki ana bileşenden oluşur: **steps (adımlar)** ve **final_answer (nihai sonuç)**. **Steps**, modelin yanıtını adım adım açıklayan bir dizi içerir. Bu dizi, her bir adımı açıklayan ve sonucu (çıktıyı) gösteren nesnelerden oluşur. Her adımda iki anahtar özellik bulunur: **"explanation"** **(açıklama) **ve **"output" (çıktı)**. **"Explanation"**, adımın neden ve nasıl yapıldığını açıklarken, "output" o adımdan elde edilen sonucu gösterir. Örneğin, matematiksel bir problem çözümünde, her bir adım modelin problemi nasıl çözdüğünü açıklayan ve o adımdaki sonucu gösteren verilerle sunulacaktır. Bu yapı, modelin yanıtını yalnızca nihai bir sonuçla sınırlamaz, aynı zamanda bu sonuca nasıl ulaşıldığını adım adım açıklar. **Final_answer** ise modelin ürettiği nihai sonucu içerir. Bu, tüm adımlar tamamlandıktan sonra elde edilen son cevaptır ve yanıtın en kritik kısmını temsil eder. Bu yapı, kullanıcının modeli yalnızca bir yanıt almak için değil, aynı zamanda o yanıtın mantığını ve çözüm sürecini anlayabilmesi için de kullanmasına olanak tanır.

## Sohbet Tamamlama ve Yapılandırılmış Yanıtın Kullanımı

Bu yapılandırılmış yanıtı kullanarak modelden bir problem çözümü istiyoruz. Aşağıdaki kod,** "8x + 7 = -23 nasıl çözülür?"** sorusunu modelin yanıtlamasını sağlar:

```csharp
ChatCompletion chatCompletion = await client.CompleteChatAsync(
    ["How can I solve 8x + 7 = -23?"], 
    options);
```

Modelin döndürdüğü yanıt, yapılandırılmış bir JSON formatında olacaktır. Bu yanıt, JsonDocument kullanılarak ayrıştırılır ve adım adım ekrana yazdırılır:

```csharp
using JsonDocument structuredJson = JsonDocument.Parse(chatCompletion.ToString());

Console.WriteLine($"Final answer: {structuredJson.RootElement.GetProperty("final_answer").GetString()}");
Console.WriteLine("Reasoning steps:");

foreach (JsonElement stepElement in structuredJson.RootElement.GetProperty("steps").EnumerateArray())
{
    Console.WriteLine($"  - Explanation: {stepElement.GetProperty("explanation").GetString()}");
    Console.WriteLine($"    Output: {stepElement.GetProperty("output")}");
}
```
Bu kod parçası, modelden gelen yanıtın JSON formatında olduğunu varsayar ve bu yanıtı ayrıştırarak içerisindeki bilgileri ekrana yazdırır. İlk satırda, chatCompletion nesnesi ToString() metodu ile JSON formatına çevrilir ve ardından JsonDocument.Parse() kullanılarak bir JsonDocument nesnesi oluşturulur. Bu nesne, JSON yapısının içindeki verilere erişmek için kullanılır. İkinci satırda, JSON içindeki "final_answer" (nihai cevap) alanına erişilerek bu cevap ekrana yazdırılır. "final_answer", yanıtın en son aşamasında elde edilen sonucu temsil eder. Sonraki satırlarda, "steps" (adımlar) alanına erişilir ve bu alan bir dizi (array) olduğundan, foreach döngüsü kullanılarak her adım tek tek işlenir. Her adımda, önce "explanation" (açıklama) alanına erişilir ve bu açıklama ekrana yazdırılır; ardından o adımdaki "output" (çıktı) yazdırılır. Bu süreç, modelin yanıtını sadece nihai sonuç olarak değil, adım adım nasıl bir çözüm süreci izlediğini de gösterir. Bu yapıyla, modelin verdiği cevabın mantığını daha iyi anlayabilirsiniz çünkü her adım hem bir açıklama hem de bir çıktı içerir. Kod, her bir adımı sırasıyla ekrana yazdırarak adım adım çözüm sürecini görmenizi sağlar.


## Yapılandırılmış Çıktıların Avantajları

Yapılandırılmış çıktılar, karmaşık problemlerin çözümünde düzenli ve adım adım ilerleyen yanıtlar sunarak, hem kullanıcılar hem de geliştiriciler için daha anlaşılır ve takip edilebilir bir çözüm süreci sağlar. Modelin verdiği yanıtlar belirli bir formatta düzenlendiği için, özellikle çok adımlı problemlerde her bir adımın açıklaması ve o adımdan elde edilen çıktı net bir şekilde gösterilir. Bu, yanıtların daha yapılandırılmış olmasını ve gereksiz bilgilerin devre dışı bırakılmasını sağlar, çünkü şema kullanarak yalnızca gerekli alanların yanıt içinde bulunmasını zorunlu kılabilirsiniz. Ayrıca, yapılandırılmış çıktılar sayesinde modelin yanıtları belirli bir şemaya veya kurallara uyarak oluşturulduğu için yanıtların doğruluğu artar; bu da tutarlı ve güvenilir sonuçların elde edilmesine yardımcı olur. Yanıtların doğruluğu, şemanın zorunlu kıldığı alanlar sayesinde, modelin mantıklı ve eksiksiz bir çözüm sunmasını sağlar. Sonuç olarak, yapılandırılmış çıktılar, modelin yanıtlarını daha düzenli, esnek ve doğruluğu yüksek hale getirerek kullanıcı deneyimini iyileştirir ve özellikle teknik ve matematiksel problemlerde çok büyük faydalar sağlar.









## OpenAI ile Azure Arasındaki İlişki?

**OpenAI** bilindiği üzere, özellikle büyük dil modelleri (LLM'ler) ve diğer ileri düzey yapay zeka teknolojileri ile tanınır. Bu modeller, doğal dil işleme, makine öğrenimi ve diğer yapay zeka uygulamalarında geniş bir alanda  kullanılmakta ve her geçen gün daha iyi hale gelmektedir.

**Azure** platformu geniş bir kesime; veri depolama, sanal makineler, yapay zeka ve makine öğrenimi gibi çeşitli konular üzerinde bulut hizmetleri sağlayan Microsoft'un bulut bilişim platformudur. Azure'un güçlü altyapısı, güvenliği ve ölçeklenebilirliği, kurumların ve biz geliştiricilerin uygulamalarını hızlı ve güvenli bir şekilde dağıtmalarına olanak tanır. Aynı zamanda Azure, OpenAI'nin yapay zeka modellerini entegre etmek ve kullanmak için ideal bir platformdur. Bu entegrasyon, kullanıcıların OpenAI'nin güçlü yapay zeka yeteneklerinden yararlanarak iş süreçlerini optimize etmelerini ve etkili-yenilikçi çözümler üretmelerini sağlar.

## Azure OpenAI Servisi Nedir?

**Azure OpenAI Servisi**, OpenAI'nin o3-mini, o1, o1-mini, GPT-4o, GPT-4o mini, GPT-4 Turbo ve Görüntü İşlemeli GPT-4 Turbo, GPT-4, GPT-3.5-Turbo ve Embeddings model serisi gibi güçlü dil modellerine REST API erişimi sağlar. Bu modeller içerik oluşturma, özetleme, görüntü anlama, anlamsal arama ve kod çevirisi için doğal dil dahil ancak bunlarla sınırlı olmamak üzere özel göreviniz için kolayca uyarlanabilir.

## Neden OpenAI ve Azure'u Birlikte Kullanmalıyız?

OpenAI ve Azure'u birlikte kullanmak müşteri deneyimi ve uygulamanın sürdürülebilir olması açısından oldukça faydalıdır. Örneğin, bir müşteri hizmetleri uygulaması geliştirdiğinizi düşünün. OpenAI'nin büyük dil modelleri (LLM) sayesinde, doğal dil işleme yetenekleri ile müşteri sorularını anlayabilir ve yanıtlayabilirsiniz. Azure'un bulut hizmetleri ise bu uygulamanın ölçeklenebilirliğini ve güvenliğini sağlar. Azure'un sunduğu veri depolama ve sanal makineler gibi hizmetler, uygulamanızın performansını artırırken, Azure'un güvenlik özellikleri verilerinizi korur. Bu entegrasyon, müşteri hizmetleri uygulamanızın hem akıllı hem de güvenli olmasını sağlar, böylece müşteri memnuniyetini artırır ve operasyonel verimliliği yükseltir.

## Azure OpenAI ile nasıl çalışır?

Azure'un güçlü bulut altyapısı ve servisleri ile OpenAI'nin yapay zeka modellerini kolaylıkla entegre etmenizi sağlar. Bu entegrasyon, büyük dil modelleri (LLM) ve diğer yapay zeka çözümlerini kullanarak iş süreçlerinin otomatikleştirilmesine ve iyileştirmelere olanak tanır. Azure'un ölçeklenebilirliği ve güvenliği sayesinde, OpenAI'nin sunduğu yapay zeka yeteneklerini güvenle ve verimli bir şekilde kullanabilirsiniz. Ayrıca, Azure'un sunduğu çeşitli tool'lar ve servisler, OpenAI modellerini özelleştirmenize ve iş ihtiyaçlarınıza göre optimize etmenize yardımcı olur.

**Azure OpenAI** senaryoları için [Azure SDK](https://github.com/Azure/azure-sdk-for-net)'yı kullanabilirsiniz.

## Azure Üzerinde OpenAI API'sini Kullanma

Azure OpenAI API'sini kullanmak için aşağıdaki adımları takip edebilirsiniz:

1. **Ön Gereksinimler**:
Azure OpenAI API'sini kullanmak için ihtiyacınız olanlar:
   - Bir [Azure aboneliği](https://azure.microsoft.com/tr-tr/pricing/purchase-options/azure-account?icid=dotnet)
   - [Azure OpenAI erişimi](https://learn.microsoft.com/tr-tr/azure/ai-services/openai/overview#how-do-i-get-access-to-azure-openai)

2. **Paket Yükleme:**
NuGet kullanarak .NET client kitaplığını yükleyin:

```bash
dotnet add package Azure.AI.OpenAI --prerelease

```

3. **İstemci Kimlik Doğrulama (Client Authentication):**
Azure OpenAI veya OpenAI ile etkileşim kurmak için **AzureOpenAIClient** örneği oluşturun:
   - Microsoft Entra kimlik bilgisi ile istemci oluşturma (Önerilen)
   - API anahtarı ile istemci oluşturma

**Microsoft Entra kimlik bilgisi ile istemci oluşturma**

```csharp
AzureOpenAIClient azureClient = new(
    new Uri("https://your-azure-openai-resource.com"),
    new DefaultAzureCredential());
ChatClient chatClient = azureClient.GetChatClient("my-gpt-4o-mini-deployment");
```

**API anahtarı ile istemci oluşturma**

```csharp
string keyFromEnvironment = Environment.GetEnvironmentVariable("AZURE_OPENAI_API_KEY");
AzureOpenAIClient azureClient = new(
    new Uri("https://your-azure-openai-resource.com"),
    new ApiKeyCredential(keyFromEnvironment));
ChatClient chatClient = azureClient.GetChatClient("my-gpt-35-turbo-deployment");
```

4. **Örnek Kodlar:**
- **Chat Tamamlama Alma:**

```csharp
AzureOpenAIClient azureClient = new(
    new Uri("https://your-azure-openai-resource.com"),
    new DefaultAzureCredential());
ChatClient chatClient = azureClient.GetChatClient("my-gpt-35-turbo-deployment");

ChatCompletion completion = chatClient.CompleteChat(
    new List<ChatMessage>
    {
        new SystemChatMessage("You are a helpful assistant that talks like a pirate."),
        new UserChatMessage("Hi, can you help me?"),
        new AssistantChatMessage("Arrr! Of course, me hearty! What can I do for ye?"),
        new UserChatMessage("What's the best way to train a parrot?")
    });

Console.WriteLine($"{completion.Role}: {completion.Content[0].Text}");
```

- **Chat Mesajlarını Akış Olarak Alma:**

```csharp
AzureOpenAIClient azureClient = new(
    new Uri("https://your-azure-openai-resource.com"),
    new DefaultAzureCredential());
ChatClient chatClient = azureClient.GetChatClient("my-gpt-35-turbo-deployment");

CollectionResult<StreamingChatCompletionUpdate> completionUpdates = chatClient.CompleteChatStreaming(
    new List<ChatMessage>
    {
        new SystemChatMessage("You are a helpful assistant that talks like a pirate."),
        new UserChatMessage("Hi, can you help me?"),
        new AssistantChatMessage("Arrr! Of course, me hearty! What can I do for ye?"),
        new UserChatMessage("What's the best way to train a parrot?")
    });

foreach (StreamingChatCompletionUpdate completionUpdate in completionUpdates)
{
    foreach (ChatMessageContentPart contentPart in completionUpdate.ContentUpdate)
    {
        Console.Write(contentPart.Text);
    }
}
```

Bu adımları takip ederek Azure üzerinde OpenAI API'sini kullanabilirsiniz.
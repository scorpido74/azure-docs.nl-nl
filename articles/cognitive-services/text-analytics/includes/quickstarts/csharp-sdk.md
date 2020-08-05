---
title: 'Quickstart: Text Analytics-clientbibliotheek voor C# | Microsoft Docs'
description: Aan de slag met de Text Analytics-clientbibliotheek voor C#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/27/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: aa182f27fcd3ab2984bbd9f960b2b828880c36f5
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87375464"
---
<a name="HOLTop"></a>

#### <a name="version-30"></a>[Versie 3.0](#tab/version-3)

[v3-referentiedocumentatie](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [Broncode voor v3-bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [v3-pakket (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3-voorbeelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

[v2-referentiedocumentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [Broncode voor v2-bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [v2-pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2-voorbeelden](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* De [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)
* Zodra u een Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Een Text Analytics-resource maken"  target="_blank">maakt u een Text Analytics-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om uw sleutel en eindpunt op te halen.  Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Text Analytics-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-net-core-application"></a>Een nieuwe .NET Core-app maken

Maak een nieuwe console-app in .NET Core met behulp van de Visual Studio IDE. Hiermee wordt een Hello World-project gemaakt met één bronbestand van C#: *program.cs*.

#### <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Installeer de clientbibliotheek door met de rechtermuisknop op de oplossing te klikken in **Solution Explorer** en **NuGet-pakketten beheren** te selecteren. Selecteer in Package Manager dat wordt geopend de optie **Bladeren** en zoek naar `Azure.AI.TextAnalytics`. Selecteer versie `5.0.0` en vervolgens **Installeren**. U kunt ook de [Package Manager-console](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package) gebruiken.


> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Ga dan naar [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), waar u de codevoorbeelden uit deze quickstart kunt vinden. 

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Installeer de clientbibliotheek door met de rechtermuisknop op de oplossing te klikken in **Solution Explorer** en **NuGet-pakketten beheren** te selecteren. Selecteer in de package manager die wordt geopend de optie **Bladeren** en zoek naar `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Klik erop en vervolgens op **Install**. U kunt ook de [Package Manager-console](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package) gebruiken.

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Ga dan naar [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), waar u de codevoorbeelden uit deze quickstart kunt vinden. 

---

#### <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Open het bestand *program.cs* en voeg de volgende `using`-instructies toe:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Maak in de klasse `Program` van de toepassing variabelen voor de sleutel en het eindpunt van uw resource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Vervang de methode `Main` van de toepassing. U definieert later de methoden die hier worden aangeroepen.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Open het bestand *program.cs* en voeg de volgende `using`-instructies toe:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

Maak in de klasse `Program` van de toepassing variabelen voor de sleutel en het eindpunt van uw resource. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Vervang de methode `Main` van de toepassing. U definieert later de methoden die hier worden aangeroepen.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Objectmodel

De Text Analytics-client is een `TextAnalyticsClient`-object dat wordt geverifieerd bij Azure met behulp van uw sleutel. De client biedt functies om tekst als afzonderlijke tekenreeksen of als een batch te accepteren. U kunt tekst synchroon of asynchroon naar de API verzenden. Het responsobject bevat de analyse-informatie voor elk document dat u verzendt. 

Als u versie `3.0` van de service gebruikt, kunt u een optioneel exemplaar van `TextAnalyticsClientOptions` gebruiken om de client te initialiseren met verschillende standaardinstellingen (zoals een standaardtaal voor het land of de regio). U kunt ook verifiëren met een Azure Active Directory-token. 

## <a name="code-examples"></a>Codevoorbeelden

* [Sentimentanalyse](#sentiment-analysis)
* [Taaldetectie](#language-detection)
* [Herkenning van benoemde entiteiten](#named-entity-recognition-ner)
* [Entiteiten koppelen](#entity-linking)
* [Sleuteltermextractie](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>De client verifiëren

#### <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Zorg ervoor dat met uw eerdere main-methode een nieuw clientobject wordt gemaakt met uw eindpunt en referenties.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Maak een nieuwe klasse `ApiKeyServiceClientCredentials` om de referenties op te slaan en deze toe te voegen aan de aanvragen van de client. Maak binnen de klasse een overschrijving voor `ProcessHttpRequestAsync()` waarmee uw sleutel wordt toegevoegd aan de header `Ocp-Apim-Subscription-Key`.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Maak een methode voor het instantiëren van het [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet)-object met uw eindpunt en een `ApiKeyServiceClientCredentials`-object dat uw sleutel bevat.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Sentimentanalyse

#### <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Maak een nieuwe functie met de naam `SentimentAnalysisExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende functie `AnalyzeSentiment()` aan. Het geretourneerde `Response<DocumentSentiment>`-object bevat het sentimentlabel en de score van het volledige invoerdocument, evenals een sentimentanalyse voor elke zin als dit is gelukt. Als er een fout optreedt, wordt er een `RequestFailedException` gegenereerd.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Uitvoer

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Maak een nieuwe functie met de naam `SentimentAnalysisExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de functie [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) van de nieuwe functie aan. Het geretourneerde [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet)-object bevat het sentiment `Score` als de aanroep is gelukten een `errorMessage` als dat niet het geval is. 

Een score dichter bij 0 wijst op een negatief gevoel, terwijl een score dichter bij 1 op een positief gevoel wijst.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Taaldetectie

#### <a name="version-30"></a>[Versie 3.0](#tab/version-3)


Maak een nieuwe functie met de naam `LanguageDetectionExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende functie `DetectLanguage()` aan. Het geretourneerde `Response<DetectedLanguage>`-object bevat de gedetecteerde taal, samen met de naam en ISO 6391-code van de taal. Als er een fout optreedt, wordt er een `RequestFailedException` gegenereerd.

> [!Tip]
> In sommige gevallen kan het lastig zijn om talen ondubbelzinnig te karakteriseren op basis van de invoer. U kunt parameter `countryHint` gebruiken om een land- of regionummer van twee letters op te geven. Standaard gebruikt de API de standaard-countryHint US. Als u dit gedrag wilt verwijderen, kunt u deze parameter opnieuw instellen door deze waarde in te stellen op de lege tekenreeks `countryHint = ""`. Als u een andere standaardwaarde wilt instellen, stelt u eigenschap `TextAnalyticsClientOptions.DefaultCountryHint` in en geeft u deze door tijdens de initialisatie van de client.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Uitvoer

```console
Language:
        French, ISO-6391: fr
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Maak een nieuwe functie met de naam `languageDetectionExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de functie [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aan van de nieuwe functie. Het geretourneerde [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet)-object bevat de lijst met gedetecteerde talen in `DetectedLanguages` als de aanroep is gelukt en een `errorMessage` als dat niet het geval is. Gebruik print met de eerste geretourneerde taal.

> [!Tip]
> In sommige gevallen kan het lastig zijn om talen ondubbelzinnig te karakteriseren op basis van de invoer. U kunt parameter `countryHint` gebruiken om een land- of regionummer van twee letters op te geven. Standaard gebruikt de API de standaard-countryHint US. Als u dit gedrag wilt verwijderen, kunt u deze parameter opnieuw instellen door deze waarde in te stellen op de lege tekenreeks `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Uitvoer

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>NER (Herkenning van benoemde entiteiten)

#### <a name="version-30"></a>[Versie 3.0](#tab/version-3)


> [!NOTE]
> Nieuw in versie `3.0`:
> * Entiteitskoppeling is nu gescheiden van entiteitsherkenning.


Maak een nieuwe functie met de naam `EntityRecognitionExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt, roep de functie `RecognizeEntities()` aan van de nieuwe functie en herhaal de resultaten. Het geretourneerde `Response<IReadOnlyCollection<CategorizedEntity>>`-object bevat de lijst met gedetecteerde entiteiten. Als er een fout optreedt, wordt er een `RequestFailedException` gegenereerd.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Uitvoer

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80
```

## <a name="entity-linking"></a>Entiteiten koppelen

Maak een nieuwe functie met de naam `EntityLinkingExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt, roep de functie `RecognizeLinkedEntities()` aan van de nieuwe functie en herhaal de resultaten. Het geretourneerde object `Response<IReadOnlyCollection<LinkedEntity>>` vertegenwoordigt de lijst met gedetecteerde entiteiten. Als er een fout optreedt, wordt er een `RequestFailedException` gegenereerd. Aangezien gekoppelde entiteiten uniek worden geïdentificeerd, worden exemplaren van dezelfde entiteit gegroepeerd onder een `LinkedEntity`-object als een lijst met `LinkedEntityMatch`-objecten.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}\n");
        }
    }
}
```

### <a name="output"></a>Uitvoer

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63

                Text: Gates
                Score: 0.63

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55

                Text: Microsoft
                Score: 0.55

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

> [!NOTE]
> In versie 2.1 wordt entiteitskoppeling opgenomen in het NER-antwoord.

Maak een nieuwe functie met de naam `RecognizeEntitiesExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende functie [Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aan. Herhaal de resultaten. Het geretourneerde object [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) bevat de lijst met gedetecteerde entiteiten in `Entities` als de aanroep is gelukt en een `errorMessage` als dat niet het geval is. Voor elke gedetecteerde entiteit wordt het type, subtype en de Wikipedia-naam (indien deze bestaan) weergegeven, evenals de locaties in de oorspronkelijke tekst.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Sleuteltermextractie

#### <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Maak een nieuwe functie met de naam `KeyPhraseExtractionExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende functie `ExtractKeyPhrases()` aan. Het geretourneerde object `<Response<IReadOnlyCollection<string>>` bevat de lijst met gedetecteerde sleuteltermen. Als er een fout optreedt, wordt er een `RequestFailedException` gegenereerd.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Uitvoer

```console
Key phrases:
    cat
    veterinarian
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Maak een nieuwe functie met de naam `KeyPhraseExtractionExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende functie [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aan. Het resultaat bevat de lijst met gedetecteerde sleuteltermen in `KeyPhrases` als de aanroep is gelukt en een `errorMessage` als dat niet het geval is. Geef alle gedetecteerde sleuteltermen weer.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Uitvoer

```console
Key phrases:
    cat
    veterinarian
```

---

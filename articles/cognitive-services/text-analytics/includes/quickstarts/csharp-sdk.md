---
title: 'Snelstartgids: Text Analytics-client bibliotheek voor C# | Microsoft Docs'
description: 'Aan de slag met de Text Analytics-client bibliotheek voor C #'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 979c714ca5446d3f1eabb76e97535ee5ffa2e359
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140706"
---
<a name="HOLTop"></a>

#### <a name="version-30"></a>[Versie 3,0](#tab/version-3)

documentatie voor v3- [referentie](https://aka.ms/azsdk-net-textanalytics-ref-docs)  |  [bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics)  |  voor de V3-bibliotheek [v3-pakket (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics)  |  [v3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples) -voor beelden

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

[v2-referentie documentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview)  |  [v2-bibliotheek bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics)  |  [v2-pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/)  |  [v2](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples) -voor beelden

---

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)
* Wanneer u uw Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" maakt u een Text Analytics resource Maak "  target="_blank"> een Text Analytics resource <span class="docon docon-navigate-external x-hidden-focus"></span> </a> in de Azure Portal om uw sleutel en eind punt op te halen.  Nadat de app is geïmplementeerd, klikt **u op Ga naar resource**.
    * U hebt de sleutel en het eind punt nodig van de resource die u maakt om de toepassing te verbinden met de Text Analytics-API. U plakt uw sleutel en het eind punt in de onderstaande code verderop in de Quick Start.
    * U kunt de gratis prijs categorie ( `F0` ) gebruiken om de service te proberen en later te upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-net-core-application"></a>Een nieuwe .NET core-toepassing maken

Maak een nieuwe .NET Core-Console-app met behulp van de Visual Studio IDE. Hiermee wordt een ' Hallo wereld '-project gemaakt met één C#-bron bestand: *Program.cs*.

#### <a name="version-30"></a>[Versie 3,0](#tab/version-3)

Installeer de client bibliotheek door met de rechter muisknop op de oplossing in de **Solution Explorer** te klikken en **NuGet-pakketten beheren**te selecteren. In package manager, dat wordt geopend, selecteert u **Browse** **Prerelease**en zoekt u naar `Azure.AI.TextAnalytics` . Selecteer versie `1.0.0-preview.5` en vervolgens **installeren**. U kunt ook de [Package Manager-console](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)gebruiken.

> [!TIP]
> Wilt u het volledige Quick Start-code bestand tegelijk weer geven? U kunt het vinden [op github](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), dat de code voorbeelden in deze Snelstartgids bevat. 

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Installeer de client bibliotheek door met de rechter muisknop op de oplossing in de **Solution Explorer** te klikken en **NuGet-pakketten beheren**te selecteren. In package manager die wordt geopend, selecteert u **Bladeren** en zoeken naar `Microsoft.Azure.CognitiveServices.Language.TextAnalytics` . Klik erop en **Installeer**vervolgens. U kunt ook de [Package Manager-console](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)gebruiken.

> [!TIP]
> Wilt u het volledige Quick Start-code bestand tegelijk weer geven? U kunt het vinden [op github](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), dat de code voorbeelden in deze Snelstartgids bevat. 

---

#### <a name="version-30"></a>[Versie 3,0](#tab/version-3)

Open het *Program.cs* -bestand en voeg de volgende- `using` instructies toe:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Maak in de klasse van de toepassing `Program` variabelen voor de sleutel en het eind punt van uw resource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Vervang de methode van de toepassing `Main` . U definieert de methoden die hier later worden genoemd.

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

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Open het *Program.cs* -bestand en voeg de volgende- `using` instructies toe:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

Maak in de klasse van de toepassing `Program` variabelen voor de sleutel en het eind punt van uw resource. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Vervang de methode van de toepassing `Main` . U definieert de methoden die hier later worden genoemd.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Object model

De Text Analytics-client is een `TextAnalyticsClient` object dat met uw sleutel wordt geverifieerd bij Azure, en biedt functies om tekst als afzonderlijke teken reeksen of als batch te accepteren. U kunt tekst synchroon of asynchroon verzenden naar de API. Het antwoord object bevat de analyse-informatie voor elk document dat u verzendt. 

Als u versie `3.0` van de service gebruikt, kunt u een optioneel exemplaar gebruiken `TextAnalyticsClientOptions` om de-client te initialiseren met verschillende standaard instellingen (bijvoorbeeld standaard taal of land/regio hint). U kunt ook verifiëren met een Azure Active Directory-token. 

## <a name="code-examples"></a>Codevoorbeelden

* [Sentiment analyse](#sentiment-analysis)
* [Taal detectie](#language-detection)
* [Herkenning van benoemde entiteiten](#named-entity-recognition-ner)
* [Entiteit koppelen](#entity-linking)
* [Extractie van sleutel woorden](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>De client verifiëren

#### <a name="version-30"></a>[Versie 3,0](#tab/version-3)

Zorg ervoor dat met uw hoofd methode eerder een nieuw client object met uw eind punt en referenties wordt gemaakt.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Maak een nieuwe `ApiKeyServiceClientCredentials` klasse om de referenties op te slaan en toe te voegen aan de aanvragen van de client. U kunt een onderdrukking voor `ProcessHttpRequestAsync()` de sleutel toevoegen aan de `Ocp-Apim-Subscription-Key` koptekst.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Maak een methode voor het instantiëren van het [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) -object met uw eind punt en een `ApiKeyServiceClientCredentials` object dat uw sleutel bevat.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Sentimentanalyse

#### <a name="version-30"></a>[Versie 3,0](#tab/version-3)

Maak een nieuwe functie `SentimentAnalysisExample()` met de naam die de client gaat gebruiken die u eerder hebt gemaakt en roep de `AnalyzeSentiment()` functie op. Het geretourneerde `Response<DocumentSentiment>` object bevat het sentiment label en de Score van het volledige invoer document, evenals een sentiment-analyse voor elke zin als dit is gelukt. Als er een fout optreedt, wordt er een gegenereerd `RequestFailedException` .

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    var si = new StringInfo(inputText);
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

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Maak een nieuwe functie `SentimentAnalysisExample()` met de naam die de client gaat gebruiken die u eerder hebt gemaakt en roep de functie [sentiment ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) aan. Het geretourneerde [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) -object bevat de sentiment `Score` als dit is geslaagd en een `errorMessage` if not. 

Een score die dicht bij 0 aangeeft, duidt op een negatieve sentiment, terwijl een score die dichter bij 1 staat, een positief sentiment aangeeft.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Taaldetectie

#### <a name="version-30"></a>[Versie 3,0](#tab/version-3)


Maak een nieuwe functie `LanguageDetectionExample()` met de naam die de client gaat gebruiken die u eerder hebt gemaakt en roep de `DetectLanguage()` functie op. Het geretourneerde `Response<DetectedLanguage>` object bevat de gedetecteerde taal, samen met de naam en ISO-6391-code. Als er een fout optreedt, wordt er een gegenereerd `RequestFailedException` .

> [!Tip]
> In sommige gevallen kan het lastig zijn om talen te dubbel zinnigheid op basis van de invoer. U kunt de `countryHint` para meter gebruiken om een land-of regio code van twee letters op te geven. Standaard gebruikt de API de standaard countryHint om dit gedrag te verwijderen, kunt u deze para meter opnieuw instellen door deze waarde in te stellen op een lege teken reeks `countryHint = ""` . Als u een andere standaard instelling wilt instellen, stelt u de `TextAnalyticsClientOptions.DefaultCountryHint` eigenschap in en geeft u deze door tijdens de initialisatie van de client.

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

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Maak een nieuwe functie `languageDetectionExample()` met de naam die de client gaat gebruiken die u eerder hebt gemaakt en roep de functie [DetectLanguage ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aan. Het geretourneerde [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) -object bevat de lijst met gedetecteerde talen in `DetectedLanguages` als dat is gelukt, en wel `errorMessage` als dat niet het geval is. De eerste geretourneerde taal afdrukken.

> [!Tip]
> In sommige gevallen kan het lastig zijn om talen te dubbel zinnigheid op basis van de invoer. U kunt de `countryHint` para meter gebruiken om een land-of regio code van twee letters op te geven. Standaard gebruikt de API de standaard countryHint om dit gedrag te verwijderen, kunt u deze para meter opnieuw instellen door deze waarde in te stellen op een lege teken reeks `countryHint = ""` .

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Uitvoer

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Herkenning van benoemde entiteiten (NER)

#### <a name="version-30"></a>[Versie 3,0](#tab/version-3)


> [!NOTE]
> Nieuw in versie `3.0` :
> * Entiteits koppeling is nu een gescheiden van entiteit herkenning.


Maak een nieuwe functie `EntityRecognitionExample()` met de naam die de client gaat gebruiken die u eerder hebt gemaakt, roep de `RecognizeEntities()` functie aan en herhaal de resultaten. Het geretourneerde `Response<IReadOnlyCollection<CategorizedEntity>>` object bevat de lijst met gedetecteerde entiteiten. Als er een fout optreedt, wordt er een gegenereerd `RequestFailedException` .

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

## <a name="entity-linking"></a>Entiteit koppelen

Maak een nieuwe functie `EntityLinkingExample()` met de naam die de client gaat gebruiken die u eerder hebt gemaakt, roep de `RecognizeLinkedEntities()` functie aan en herhaal de resultaten. De geretourneerde `Response<IReadOnlyCollection<LinkedEntity>>` vertegenwoordigt de lijst met gedetecteerde entiteiten. Als er een fout optreedt, wordt er een gegenereerd `RequestFailedException` . Aangezien gekoppelde entiteiten uniek worden geïdentificeerd, worden exemplaren van dezelfde entiteit gegroepeerd onder een `LinkedEntity` object als een lijst met `LinkedEntityMatch` objecten.

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

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

> [!NOTE]
> In versie 2,1 wordt koppeling van entiteit opgenomen in het NER-antwoord.

Maak een nieuwe functie `RecognizeEntitiesExample()` met de naam die de client gaat gebruiken die u eerder hebt gemaakt en roep de functie [entities ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aan. Herhaal de resultaten. Het geretourneerde [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) -object bevat de lijst met gedetecteerde entiteiten in `Entities` als dit is gelukt, en een `errorMessage` als dat niet het geval is. Voor elke gedetecteerde entiteit, het type, subtype, naam van de Wikipedia (indien aanwezig) afdrukken, evenals de locaties in de oorspronkelijke tekst.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Sleuteltermextractie

#### <a name="version-30"></a>[Versie 3,0](#tab/version-3)

Maak een nieuwe functie `KeyPhraseExtractionExample()` met de naam die de client gaat gebruiken die u eerder hebt gemaakt en roep de `ExtractKeyPhrases()` functie op. Het geretourneerde `<Response<IReadOnlyCollection<string>>` object bevat de lijst met gedetecteerde sleutel zinnen. Als er een fout optreedt, wordt er een gegenereerd `RequestFailedException` .

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

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Maak een nieuwe functie `KeyPhraseExtractionExample()` met de naam die de client gaat gebruiken die u eerder hebt gemaakt en roep de functie voor de sleutel [woorden ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aan. Het resultaat bevat de lijst met gedetecteerde sleutel zinnen in `KeyPhrases` als dit is geslaagd, en een `errorMessage` als dat niet zo is. Alle gedetecteerde sleutel zinnen afdrukken.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Uitvoer

```console
Key phrases:
    cat
    veterinarian
```

---

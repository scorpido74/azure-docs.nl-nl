---
title: 'Snelstartgids: Text Analytics-client bibliotheek C# voor | Microsoft Docs'
description: Aan de slag met de Text Analytics-client bibliotheek voorC#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/26/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: b7c4bac7523cdec36beac64e2cd204588b0935fb
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78155513"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

[v3-referentie documentatie](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [v3-bibliotheek bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [v3-pakket (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | v3-voor [beelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

[v2-referentie documentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [v2-bibliotheek bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [v2-pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | v2-voor [beelden](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)
* Wanneer u uw Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="een Text Analytics resource maken"  target="_blank">een Text Analytics resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> maken in de Azure Portal om uw sleutel en eind punt op te halen. 
    * U hebt de sleutel en het eind punt nodig van de resource die u maakt om de toepassing te verbinden met de Text Analytics-API. U gaat later in de Quick Start.
    * U kunt de gratis prijs categorie gebruiken om de service te proberen en later te upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-net-core-application"></a>Een nieuwe .NET core-toepassing maken

Maak een nieuwe .NET Core-Console-app met behulp van de Visual Studio IDE. Hiermee wordt een ' Hallo wereld '-project gemaakt met één C# bron bestand: *Program.cs*.

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

Installeer de client bibliotheek door met de rechter muisknop op de oplossing in de **Solution Explorer** te klikken en **NuGet-pakketten beheren**te selecteren. In package manager, dat wordt geopend, selecteert u **Browse** **Prerelease toevoegen**en zoekt u naar `Azure.AI.TextAnalytics`. Selecteer versie `1.0.0-preview.2`en **Installeer**vervolgens. U kunt ook de [Package Manager-console](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)gebruiken.

> [!TIP]
> Wilt u het volledige Quick Start-code bestand tegelijk weer geven? U kunt het vinden [op github](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), dat de code voorbeelden in deze Snelstartgids bevat. 

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Installeer de client bibliotheek door met de rechter muisknop op de oplossing in de **Solution Explorer** te klikken en **NuGet-pakketten beheren**te selecteren. In package manager die wordt geopend, selecteert u **Bladeren** en zoekt u naar `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Klik erop en **Installeer**vervolgens. U kunt ook de [Package Manager-console](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)gebruiken.

> [!TIP]
> Wilt u het volledige Quick Start-code bestand tegelijk weer geven? U kunt het vinden [op github](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), dat de code voorbeelden in deze Snelstartgids bevat. 

---

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

Open het *Program.cs* -bestand en voeg de volgende `using`-instructies toe:

```csharp
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Maak in de `Program` klasse van de toepassing variabelen voor de sleutel en het eind punt van uw resource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly TextAnalyticsApiKeyCredential credentials = new TextAnalyticsApiKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Vervang de `Main` methode van de toepassing. U definieert de methoden die hier later worden genoemd.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityPIIExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Open het *Program.cs* -bestand en voeg de volgende `using`-instructies toe:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

Maak in de `Program` klasse van de toepassing variabelen voor de sleutel en het eind punt van uw resource. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Vervang de `Main` methode van de toepassing. U definieert de methoden die hier later worden genoemd.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Object model

De Text Analytics-client is een `TextAnalyticsClient`-object dat wordt geverifieerd bij Azure met behulp van uw sleutel, en biedt functies om tekst als afzonderlijke teken reeksen of als batch te accepteren. U kunt tekst synchroon of asynchroon verzenden naar de API. Het antwoord object bevat de analyse-informatie voor elk document dat u verzendt. 

Als u versie `3.0-preview`gebruikt, kunt u een optioneel `TextAnalyticsClientOptions` exemplaar gebruiken om de-client te initialiseren met verschillende standaard instellingen (bijvoorbeeld standaard taal of land hint). U kunt ook verifiëren met een Azure Active Directory-token. 

## <a name="code-examples"></a>Code voorbeelden

* [Sentiment analyse](#sentiment-analysis)
* [Taal detectie](#language-detection)
* [Herkenning van benoemde entiteiten](#named-entity-recognition-ner)
* [Persoonlijke gegevens detecteren](#detect-personal-information)
* [Entiteit koppelen](#entity-linking)
* [Extractie van sleutel woorden](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>De client verifiëren

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

Zorg ervoor dat met uw hoofd methode eerder een nieuw client object met uw eind punt en referenties wordt gemaakt.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Maak een nieuwe `ApiKeyServiceClientCredentials` klasse om de referenties op te slaan en toe te voegen aan de aanvragen van de client. Maak een onderdrukking voor `ProcessHttpRequestAsync()` waarmee uw sleutel wordt toegevoegd aan de `Ocp-Apim-Subscription-Key`-header.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Maak een methode voor het instantiëren van het [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) -object met uw eind punt en een `ApiKeyServiceClientCredentials`-object dat uw sleutel bevat.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Sentimentanalyse

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

Maak een nieuwe functie met de naam `SentimentAnalysisExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de `AnalyzeSentiment()`-functie aan. Het geretourneerde `Response<DocumentSentiment>`-object bevat het label en de Score van het volledige invoer document, evenals een sentiment-analyse voor elke zin als deze is geslaagd. Als er een fout optreedt, wordt een `RequestFailedException`gegenereerd.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    var si = new StringInfo(inputText);
    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tSentence [offset {sentence.Offset}, length {sentence.Length}]");
        Console.WriteLine($"\tText: \"{si.SubstringByTextElements(sentence.Offset, sentence.Length)}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.SentimentScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.SentimentScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.SentimentScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Uitvoer

```console
Document sentiment: Positive

        Sentence [offset 0, length 30]
        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [offset 31, length 30]
        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Maak een nieuwe functie met de naam `SentimentAnalysisExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de functie [sentiment ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) aan. Het geretourneerde [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) -object bevat de sentiment `Score` als dit is gelukt en een `errorMessage` als dat niet het geval is. 

Een score die dicht bij 0 aangeeft, duidt op een negatieve sentiment, terwijl een score die dichter bij 1 staat, een positief sentiment aangeeft.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Taaldetectie

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)


Maak een nieuwe functie met de naam `LanguageDetectionExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de `DetectLanguage()`-functie aan. Het geretourneerde `Response<DetectedLanguage>`-object bevat de gedetecteerde taal, samen met de naam en ISO-6391-code. Als er een fout optreedt, wordt een `RequestFailedException`gegenereerd.

> [!Tip]
> In sommige gevallen kan het lastig zijn om talen te dubbel zinnigheid op basis van de invoer. U kunt de para meter `countryHint` gebruiken om een land nummer van twee letters op te geven. Standaard gebruikt de API de standaard countryHint om dit gedrag te verwijderen, kunt u deze para meter opnieuw instellen door deze waarde in te stellen op een lege teken reeks `countryHint = ""`. Als u een andere standaard instelling wilt instellen, stelt u de eigenschap `TextAnalyticsClientOptions.DefaultCountryHint` in en geeft u deze door tijdens de initialisatie van de client.

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

Maak een nieuwe functie met de naam `languageDetectionExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de functie [DetectLanguage ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aan. Het geretourneerde [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) -object bevat de lijst met gedetecteerde talen in `DetectedLanguages` als dit is gelukt en een `errorMessage` als dat niet het geval is. De eerste geretourneerde taal afdrukken.

> [!Tip]
> In sommige gevallen kan het lastig zijn om talen te dubbel zinnigheid op basis van de invoer. U kunt de para meter `countryHint` gebruiken om een land nummer van twee letters op te geven. Standaard gebruikt de API de standaard countryHint om dit gedrag te verwijderen, kunt u deze para meter opnieuw instellen door deze waarde in te stellen op een lege teken reeks `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Uitvoer

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Herkenning van benoemde entiteiten (NER)

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)


> [!NOTE]
> Nieuw in versie `3.0-preview`:
> * Entiteits herkenning bevat nu de mogelijkheid om persoonlijke gegevens in tekst te detecteren.
> * Entiteits koppeling is nu een gescheiden van entiteit herkenning.


Maak een nieuwe functie met de naam `EntityRecognitionExample()` die de client gaat gebruiken die u eerder hebt gemaakt, roep de `RecognizeEntities()`-functie aan en herhaal de resultaten. Het object dat wordt geretourneerd `Response<IReadOnlyCollection<CategorizedEntity>>` bevat de lijst met gedetecteerde entiteiten. Als er een fout optreedt, wordt een `RequestFailedException`gegenereerd.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach(var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Uitvoer

```console
Named Entities:
        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Offset: 26,     Length: 7,      Score: 0.920

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Offset: 34,     Length: 9,      Score: 0.800
```

## <a name="detect-personal-information"></a>Persoonlijke gegevens detecteren

Maak een nieuwe functie met de naam `EntityPIIExample()` die de client gaat gebruiken die u eerder hebt gemaakt, roep de `RecognizePiiEntities()`-functie aan en herhaal de resultaten. Vergelijkbaar met de functie Previous bevat het object dat wordt geretourneerd `Response<IReadOnlyCollection<CategorizedEntity>>` de lijst met gedetecteerde entiteiten. Als er een fout optreedt, wordt een `RequestFailedException`gegenereerd.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    string inputText = "Insurance policy for SSN on file 123-12-1234 is here by approved.";
    var response = client.RecognizePiiEntities(inputText);
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach(var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Uitvoer

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Category: U.S. Social Security Number (SSN),    Sub-Category: None
                Offset: 33,     Length: 11,     Score: 0.850
```


## <a name="entity-linking"></a>Entiteit koppelen

Maak een nieuwe functie met de naam `EntityLinkingExample()` die de client gaat gebruiken die u eerder hebt gemaakt, roep de `RecognizeLinkedEntities()`-functie aan en herhaal de resultaten. De geretourneerde `Response<IReadOnlyCollection<LinkedEntity>>` vertegenwoordigt de lijst met gedetecteerde entiteiten. Als er een fout optreedt, wordt een `RequestFailedException`gegenereerd. Aangezien gekoppelde entiteiten uniek worden geïdentificeerd, worden exemplaren van dezelfde entiteit gegroepeerd onder een `LinkedEntity`-object als een lijst van `LinkedEntityMatch`-objecten.

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
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.Id},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.Score:F3}\n");
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
                Offset: 116,    Length: 11,     Score: 0.777

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25,     Length: 10,     Score: 0.555

                Text: Gates
                Offset: 161,    Length: 5,      Score: 0.555

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40,     Length: 10,     Score: 0.533

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0,      Length: 9,      Score: 0.469

                Text: Microsoft
                Offset: 150,    Length: 9,      Score: 0.469

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54,     Length: 7,      Score: 0.248

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89,     Length: 5,      Score: 0.281
```

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

> [!NOTE]
> In versie 2,1 wordt koppeling van entiteit opgenomen in het NER-antwoord.

Maak een nieuwe functie met de naam `RecognizeEntitiesExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de functie [entities ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aan. Herhaal de resultaten. Het geretourneerde [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) -object bevat de lijst met gedetecteerde entiteiten in `Entities` als dit is gelukt en een `errorMessage` als dat niet het geval is. Voor elke gedetecteerde entiteit, het type, subtype, naam van de Wikipedia (indien aanwezig) afdrukken, evenals de locaties in de oorspronkelijke tekst.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Sleuteltermextractie

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

Maak een nieuwe functie met de naam `KeyPhraseExtractionExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de `ExtractKeyPhrases()`-functie aan. Het geretourneerde `<Response<IReadOnlyCollection<string>>`-object bevat de lijst met gedetecteerde sleutel zinnen. Als er een fout optreedt, wordt een `RequestFailedException`gegenereerd.

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

Maak een nieuwe functie met de naam `KeyPhraseExtractionExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de functie voor sleutel [zinnen ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aan. Het resultaat bevat de lijst met gedetecteerde sleutel zinnen in `KeyPhrases` als deze is geslaagd en een `errorMessage` als dat niet het geval is. Alle gedetecteerde sleutel zinnen afdrukken.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Uitvoer

```console
Key phrases:
    cat
    veterinarian
```

---

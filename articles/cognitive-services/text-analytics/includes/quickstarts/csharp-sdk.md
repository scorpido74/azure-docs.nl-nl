---
title: 'Snelstartgids: Text Analytics-client bibliotheek C# voor | Microsoft Docs'
description: Aan de slag met de Text Analytics-client bibliotheek voorC#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: a907fb5347e6559e066f678c53bb16c7727a74b5
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987885"
---
<a name="HOLTop"></a>

#### <a name="version-30-previewtabversion-3"></a>[Versie 3,0-Preview](#tab/version-3)

[v3-referentie documentatie](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [v3-bibliotheek bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [v3-pakket (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | v3-voor [beelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21tabversion-2"></a>[Versie 2,1](#tab/version-2)

[v2-referentie documentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [v2-bibliotheek bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [v2-pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | v2-voor [beelden](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-net-core-application"></a>Een nieuwe .NET core-toepassing maken

Maak een nieuwe .NET Core-Console-app met behulp van de Visual Studio IDE. Hiermee wordt een ' Hallo wereld '-project gemaakt met één C# bron bestand: *Program.cs*.

#### <a name="version-30-previewtabversion-3"></a>[Versie 3,0-Preview](#tab/version-3)

Installeer de client bibliotheek door met de rechter muisknop op de oplossing in de **Solution Explorer** te klikken en **NuGet-pakketten beheren**te selecteren. In package manager, dat wordt geopend, selecteert u **Browse** **Prerelease toevoegen**en zoekt u naar `Azure.AI.TextAnalytics`. Klik erop en **Installeer**vervolgens. U kunt ook de [Package Manager-console](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)gebruiken.

Open het *Program.cs* -bestand en voeg de volgende `using`-instructies toe:

```csharp
using System;
using Azure.AI.TextAnalytics;
```

Maak in de `Program` klasse van de toepassing variabelen voor de sleutel en het eind punt van uw resource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Vervang de `Main` methode van de toepassing. U definieert de methoden die hier later worden genoemd.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, key);
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

#### <a name="version-21tabversion-2"></a>[Versie 2,1](#tab/version-2)

Installeer de client bibliotheek door met de rechter muisknop op de oplossing in de **Solution Explorer** te klikken en **NuGet-pakketten beheren**te selecteren. In package manager die wordt geopend, selecteert u **Bladeren** en zoekt u naar `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Klik erop en **Installeer**vervolgens. U kunt ook de [Package Manager-console](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)gebruiken.

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
* [Entiteit koppelen](#entity-linking)
* [Extractie van sleutel woorden](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>De client verifiëren

#### <a name="version-30-previewtabversion-3"></a>[Versie 3,0-Preview](#tab/version-3)

Zorg ervoor dat met uw hoofd methode eerder een nieuw client object met uw eind punt en sleutel wordt gemaakt. 

```csharp
var client = new TextAnalyticsClient(endpoint, key);
```

#### <a name="version-21tabversion-2"></a>[Versie 2,1](#tab/version-2)

Maak een nieuwe `ApiKeyServiceClientCredentials` klasse om de referenties op te slaan en toe te voegen aan de aanvragen van de client. Maak een onderdrukking voor `ProcessHttpRequestAsync()` waarmee uw sleutel wordt toegevoegd aan de `Ocp-Apim-Subscription-Key`-header.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Maak een methode voor het instantiëren van het [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) -object met uw eind punt en een `ApiKeyServiceClientCredentials`-object dat uw sleutel bevat.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Sentimentanalyse

#### <a name="version-30-previewtabversion-3"></a>[Versie 3,0-Preview](#tab/version-3)

Maak een nieuwe functie met de naam `SentimentAnalysisExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de `AnalyzeSentiment()`-functie aan. Het geretourneerde `Response<AnalyzeSentimentResult>`-object bevat het label en de Score van het volledige invoer document, evenals een sentiment-analyse voor elke zin als deze is geslaagd, en een `Value.ErrorMessage` als dat niet het geval is.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    var response = client.AnalyzeSentiment("I had the best day of my life. I wish you were there with me.");
    Console.WriteLine($"Document sentiment: {response.Value.DocumentSentiment.SentimentClass}\n");
    foreach (var sentence in response.Value.SentenceSentiments)
    {
        Console.WriteLine($"\tSentence [offset {sentence.Offset}, length {sentence.Length}]");
        Console.WriteLine($"\tSentence sentiment: {sentence.SentimentClass}");
        Console.WriteLine($"\tPositive score: {sentence.PositiveScore:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.NegativeScore:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.NeutralScore:0.00}\n");
    }
}
```

### <a name="output"></a>Uitvoer

```console
Document sentiment: Positive

        Sentence [offset 0, length 30]
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [offset 31, length 30]
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21tabversion-2"></a>[Versie 2,1](#tab/version-2)

Maak een nieuwe functie met de naam `SentimentAnalysisExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de functie [sentiment ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) aan. Het geretourneerde [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) -object bevat de sentiment `Score` als dit is gelukt en een `errorMessage` als dat niet het geval is. 

Een score die dicht bij 0 aangeeft, duidt op een negatieve sentiment, terwijl een score die dichter bij 1 staat, een positief sentiment aangeeft.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Taaldetectie

#### <a name="version-30-previewtabversion-3"></a>[Versie 3,0-Preview](#tab/version-3)


Maak een nieuwe functie met de naam `LanguageDetectionExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de `DetectLanguage()`-functie aan. Het geretourneerde `Response<DetectLanguageResult>`-object bevat de gedetecteerde taal in `Value.PrimaryLanguage` als deze is geslaagd en een `Value.ErrorMessage` als dat niet het geval is.

> [!Tip]
> In sommige gevallen kan het lastig zijn om talen te dubbel zinnigheid op basis van de invoer. U kunt de para meter `countryHint` gebruiken om een land nummer van twee letters op te geven. Standaard gebruikt de API de standaard countryHint om dit gedrag te verwijderen, kunt u deze para meter opnieuw instellen door deze waarde in te stellen op een lege teken reeks `countryHint = ""`. Als u een andere standaard instelling wilt instellen, stelt u de eigenschap `TextAnalyticsClientOptions.DefaultCountryHint` in en geeft u deze door tijdens de initialisatie van de client.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    var response = client.DetectLanguage("Ce document est rédigé en Français.");
    var detectedLanguage = response.Value.PrimaryLanguage;
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Uitvoer

```console
Language:
        French, ISO-6391: fr
```

#### <a name="version-21tabversion-2"></a>[Versie 2,1](#tab/version-2)

Maak een nieuwe functie met de naam `languageDetectionExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de functie [DetectLanguage ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aan. Het geretourneerde [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) -object bevat de lijst met gedetecteerde talen in `DetectedLanguages` als dit is gelukt en een `errorMessage` als dat niet het geval is.  De eerste geretourneerde taal afdrukken.

> [!Tip]
> In sommige gevallen kan het lastig zijn om talen te dubbel zinnigheid op basis van de invoer. U kunt de para meter `countryHint` gebruiken om een land nummer van twee letters op te geven. Standaard gebruikt de API de standaard countryHint om dit gedrag te verwijderen, kunt u deze para meter opnieuw instellen door deze waarde in te stellen op een lege teken reeks `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Uitvoer

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Herkenning van benoemde entiteiten (NER)

#### <a name="version-30-previewtabversion-3"></a>[Versie 3,0-Preview](#tab/version-3)


> [!NOTE]
> In versie `3.0-preview`:
> * NER bevat afzonderlijke methoden voor het detecteren van persoonlijke gegevens. 
> * Entiteits koppeling is een afzonderlijke aanvraag dan NER.

De onderstaande code is voor het detecteren van persoonlijke gegevens met behulp van NER v3, een open bare preview.


Maak een nieuwe functie met de naam `EntityRecognitionExample()` die de client gaat gebruiken die u eerder hebt gemaakt, roep de `RecognizeEntities()`-functie aan en herhaal de resultaten. Het geretourneerde `Response<RecognizeEntitiesResult>`-object bevat de lijst met gedetecteerde entiteiten in `Value.NamedEntities` als deze is geslaagd en een `Value.ErrorMessage` als dat niet het geval is. Voor elke gedetecteerde entiteit drukt u het type en subtype af, indien aanwezig.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Uitvoer

```console
Named Entities:
        Text: Seattle,  Type: Location, Sub-Type: N/A
                Offset: 26,     Length: 7,      Score: 0.806

        Text: last week,        Type: DateTime, Sub-Type: N/A
                Offset: 34,     Length: 9,      Score: 0.800
```

## <a name="using-ner-to-detect-personal-information"></a>NER gebruiken om persoonlijke gegevens te detecteren

Maak een nieuwe functie met de naam `EntityPIIExample()` die de client gaat gebruiken die u eerder hebt gemaakt, roep de `RecognizePiiEntities()`-functie aan en herhaal de resultaten. Net als bij de functie Previous bevat het geretourneerde `Response<RecognizeEntitiesResult>` object de lijst met gedetecteerde entiteiten in `Value.NamedEntities` als dit lukt en een `Value.ErrorMessage` als dat niet het geval is.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    var response = client.RecognizePiiEntities("Insurance policy for SSN on file 123-12-1234 is here by approved.");
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Uitvoer

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Type: U.S. Social Security Number (SSN),        Sub-Type: N/A
                Offset: 33,     Length: 11,     Score: 0.850
```


## <a name="entity-linking"></a>Entiteit koppelen

Maak een nieuwe functie met de naam `EntityLinkingExample()` die de client gaat gebruiken die u eerder hebt gemaakt, roep de `RecognizeLinkedEntities()`-functie aan en herhaal de resultaten. Het geretourneerde `Response<RecognizeLinkedEntitiesResult>`-object bevat de lijst met gedetecteerde entiteiten in `Value.LinkedEntities` als deze is geslaagd en een `Value.ErrorMessage` als dat niet het geval is. Aangezien gekoppelde entiteiten uniek worden geïdentificeerd, worden exemplaren van dezelfde entiteit gegroepeerd onder een `LinkedEntity`-object als een lijst van `LinkedEntityMatch`-objecten.

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
    foreach (var entity in response.Value.LinkedEntities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.Id},\tURL: {entity.Uri}\tData Source: {entity.DataSource}");
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

#### <a name="version-21tabversion-2"></a>[Versie 2,1](#tab/version-2)

> [!NOTE]
> In versie 2,1 wordt koppeling van entiteit opgenomen in het NER-antwoord.

Maak een nieuwe functie met de naam `RecognizeEntitiesExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de functie [entities ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aan. Herhaal de resultaten. Het geretourneerde [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) -object bevat de lijst met gedetecteerde entiteiten in `Entities` als dit is gelukt en een `errorMessage` als dat niet het geval is. Voor elke gedetecteerde entiteit, het type, subtype, naam van de Wikipedia (indien aanwezig) afdrukken, evenals de locaties in de oorspronkelijke tekst.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Sleuteluitdrukkingen extraheren

#### <a name="version-30-previewtabversion-3"></a>[Versie 3,0-Preview](#tab/version-3)

Maak een nieuwe functie met de naam `KeyPhraseExtractionExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de `ExtractKeyPhrases()`-functie aan. Het resultaat bevat de lijst met gedetecteerde sleutel zinnen in `Value.KeyPhrases` als deze is geslaagd en een `Value.ErrorMessage` als dat niet het geval is. Alle gedetecteerde sleutel zinnen afdrukken.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value.KeyPhrases)
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

#### <a name="version-21tabversion-2"></a>[Versie 2,1](#tab/version-2)

Maak een nieuwe functie met de naam `KeyPhraseExtractionExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de functie voor sleutel [zinnen ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aan. Het resultaat bevat de lijst met gedetecteerde sleutel zinnen in `KeyPhrases` als deze is geslaagd en een `errorMessage` als dat niet het geval is. Alle gedetecteerde sleutel zinnen afdrukken.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Uitvoer

```console
Key phrases:
    cat
    veterinarian
```

---
---
title: 'Snelstartgids: Text Analytics v3-client bibliotheek C# voor | Microsoft Docs'
description: Aan de slag met de V3-Text Analytics-client bibliotheek voorC#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 2cf9a006e1f6f1edb996aa9beaf8934a14af29df
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281141"
---
<a name="HOLTop"></a>

[Referentie documentatie](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [pakket (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [beelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

> [!NOTE]
> * In deze Snelstartgids wordt gebruikgemaakt van versie `3.0-preview` van de Text Analytics-client bibliotheek, die een open bare Preview bevat voor verbeterde [sentimentanalyse](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) en [named entity Recognition (ner)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentie opslag om eenvoud. Voor productie scenario's wordt u aangeraden de batch-asynchrone methoden te gebruiken voor prestaties en schaal baarheid. U kunt bijvoorbeeld `AnalyzeSentimentAsync()` aanroepen in plaats van `AnalyzeSentiment()`.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)

## <a name="setting-up"></a>Instellen

### <a name="create-a-text-analytics-azure-resource"></a>Een Text Analytics Azure-resource maken

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-net-core-application"></a>Een nieuwe .NET core-toepassing maken

Maak een nieuwe .NET Core-Console-app met behulp van de Visual Studio IDE. Hiermee wordt een ' Hallo wereld '-project gemaakt met één C# bron bestand: *Program.cs*.

Installeer de client bibliotheek door met de rechter muisknop op de oplossing in de **Solution Explorer** te klikken en **NuGet-pakketten beheren**te selecteren. In package manager, dat wordt geopend, selecteert u **Browse** **Prerelease toevoegen**en zoekt u naar `Azure.AI.TextAnalytics`. Klik erop en **Installeer**vervolgens. U kunt ook de [Package Manager-console](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)gebruiken.

Open het *Program.cs* -bestand en voeg de volgende `using`-instructies toe:

```csharp
using System;
using Azure.AI.TextAnalytics;
```

Maak in de `Program` klasse van de toepassing variabelen voor de sleutel en het eind punt van uw resource.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Vervang de `Main` methode van de toepassing. U definieert de methoden die hier later worden genoemd.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, key);

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

## <a name="object-model"></a>Object model

De Text Analytics-client is een `TextAnalyticsClient`-object dat wordt geverifieerd bij Azure met behulp van uw sleutel, en biedt functies om tekst als afzonderlijke teken reeksen of als batch te accepteren. U kunt tekst synchroon of asynchroon verzenden naar de API. Het antwoord object bevat de analyse-informatie voor elk document dat u verzendt. U kunt een optioneel, `TextAnalyticsClientOptions`-exemplaar gebruiken om de-client te initialiseren met verschillende standaard instellingen (bijvoorbeeld standaard taal of land hint).

Aanvullende voor beelden, waaronder AAD-verificatie en het gebruik van standaard instellingen voor de client, kunt u [hier](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)vinden.

## <a name="code-examples"></a>Codevoorbeelden

* [Sentiment analyse](#sentiment-analysis) (open bare preview)
* [Taal detectie](#language-detection)
* [Herkenning van benoemde entiteiten](#named-entity-recognition-public-preview) (open bare preview)
* [Herkenning van benoemde entiteiten-persoonlijke gegevens](#named-entity-recognition---personal-information-public-preview) (open bare preview)
* [Entiteit koppelen](#entity-linking)
* [Extractie van sleutel woorden](#key-phrase-extraction)

In de `main()` methode van uw programma roept u de verificatie methode op om de client te instantiëren.

## <a name="sentiment-analysis-public-preview"></a>Sentiment analyse (open bare preview)

> [!NOTE]
> De onderstaande code is voor Sentimentanalyse v3, een open bare preview-versie.

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

## <a name="language-detection"></a>Taaldetectie

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

## <a name="named-entity-recognition-public-preview"></a>Herkenning van benoemde entiteiten (open bare preview)

> [!NOTE]
> De onderstaande code is voor named entity Recognition v3, een open bare preview-versie.

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

## <a name="named-entity-recognition---personal-information-public-preview"></a>Herkenning van benoemde entiteiten-persoonlijke gegevens (open bare preview)

> [!NOTE]
> De onderstaande code is voor het detecteren van persoonlijke gegevens met behulp van named entity Recognition v3, dat zich in de open bare preview bevindt.

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

## <a name="entity-linking"></a>Entiteiten koppelen

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

## <a name="key-phrase-extraction"></a>Sleuteluitdrukkingen extraheren

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

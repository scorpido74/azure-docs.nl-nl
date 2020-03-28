---
title: 'Snelstart: Text Analytics-clientbibliotheek voor C# | Microsoft Documenten'
description: 'Aan de slag met de Text Analytics-clientbibliotheek voor C #'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 64eb19e43223c1953a7244f8fd29c48d085f1e96
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80116918"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

[v3 Referentiedocumentatie](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [v3 Bibliotheek broncode](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [v3 Package (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3 Samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

[v2 Referentiedocumentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [v2 Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [v2-pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2-voorbeelden](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* De [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)
* Zodra u uw Azure-abonnement <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="hebt,"  target="_blank">maakt u <span class="docon docon-navigate-external x-hidden-focus"></span> </a> een Text Analytics-bron en maakt u een Text Analytics-bron in de Azure-portal om uw sleutel en eindpunt op te halen.  Nadat deze is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt van de resource die u maakt nodig om uw toepassing te verbinden met de Text Analytics API. U plakt uw sleutel en eindpunt later in de quickstart in de onderstaande code.
    * U de gratis`F0`prijscategorie () gebruiken om de service uit te proberen en later te upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-net-core-application"></a>Een nieuwe .NET Core-toepassing maken

Maak met de Visual Studio IDE een nieuwe .NET Core-console-app. Hierdoor wordt een "Hello World"-project gemaakt met één C#-bronbestand: *program.cs*.

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

Installeer de clientbibliotheek door met de rechtermuisknop op de oplossing in de **Solution Explorer** te klikken en **NuGet-pakketten beheren**te selecteren. Selecteer **Bladeren,** schakel **Prerelease opnemen**en zoeken `Azure.AI.TextAnalytics`naar . Selecteer `1.0.0-preview.3`versie en **installeer**. U ook de [Package Manager Console gebruiken.](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)

> [!TIP]
> Wilt u het hele quickstart-codebestand in één keer bekijken? U het [vinden op GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), die de code voorbeelden in deze quickstart bevat. 

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Installeer de clientbibliotheek door met de rechtermuisknop op de oplossing in de **Solution Explorer** te klikken en **NuGet-pakketten beheren**te selecteren. Selecteer **bladeren** en zoeken `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`naar in de package manager die wordt geopend . Klik erop en **installeer**. U ook de [Package Manager Console gebruiken.](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)

> [!TIP]
> Wilt u het hele quickstart-codebestand in één keer bekijken? U het [vinden op GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), die de code voorbeelden in deze quickstart bevat. 

---

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

Open het *program.cs* bestand `using` en voeg de volgende richtlijnen toe:

```csharp
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Maak in de `Program` klasse van de toepassing variabelen voor het sleutel- en eindpunt van uw resource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly TextAnalyticsApiKeyCredential credentials = new TextAnalyticsApiKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Vervang de methode `Main` van de toepassing. U definieert de hier later aangeroepen methoden.

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

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Open het *program.cs* bestand `using` en voeg de volgende richtlijnen toe:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

Maak in de `Program` klasse van de toepassing variabelen voor het sleutel- en eindpunt van uw resource. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Vervang de methode `Main` van de toepassing. U definieert de hier later aangeroepen methoden.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Objectmodel

De Text Analytics-client is een `TextAnalyticsClient` object dat met uw sleutel naar Azure wordt geverifieerd en functies biedt om tekst als afzonderlijke tekenreeksen of als batch te accepteren. U tekst synchroon of asynchroon naar de API verzenden. Het antwoordobject bevat de analysegegevens voor elk document dat u verzendt. 

Als u de `3.0-preview`versie gebruikt, kunt `TextAnalyticsClientOptions` u een optionele instantie gebruiken om de client te initialiseren met verschillende standaardinstellingen (bijvoorbeeld standaardtaal of landhint). U ook verifiëren met een Azure Active Directory-token. 

## <a name="code-examples"></a>Codevoorbeelden

* [Sentimentanalyse](#sentiment-analysis)
* [Taaldetectie](#language-detection)
* [Benoemde entiteitsherkenning](#named-entity-recognition-ner)
* [Persoonlijke gegevens detecteren](#detect-personal-information)
* [Koppeling van entiteiten](#entity-linking)
* [Trefwoordextractie](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>De client verifiëren

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

Zorg ervoor dat uw hoofdmethode van vroeger een nieuw clientobject maakt met uw eindpunt en referenties.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Maak een `ApiKeyServiceClientCredentials` nieuwe klasse om de referenties op te slaan en toe te voegen aan de verzoeken van de klant. Maak erin een `ProcessHttpRequestAsync()` overschrijving voor die `Ocp-Apim-Subscription-Key` je sleutel aan de koptekst toevoegt.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Maak een methode om het [TextAnalyticsClient-object](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) te instantiëren met uw eindpunt en een `ApiKeyServiceClientCredentials` object dat uw sleutel bevat.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Sentimentanalyse

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

Maak een nieuwe `SentimentAnalysisExample()` functie genaamd die de client die `AnalyzeSentiment()` u eerder hebt gemaakt, en de functie ervan aanroepen. Het `Response<DocumentSentiment>` geretourneerde object bevat het sentimentslabel en de score van het volledige invoerdocument, evenals een sentimentanalyse voor elke zin als deze succesvol is. Als er een fout was, `RequestFailedException`zal het gooien een .

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    var si = new StringInfo(inputText);
    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tSentence [length {sentence.GraphemeLength}]");
        Console.WriteLine($"\tText: \"{si.SubstringByTextElements(sentence.GraphemeOffset, sentence.GraphemeLength)}\"");
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

        Sentence [length 30]
        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [length 30]
        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Maak een nieuwe `SentimentAnalysisExample()` functie genaamd die de client die u eerder hebt gemaakt, en noem de functie [Sentiment().](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) Het geretourneerde [sentimentResultaat-object](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) bevat het `Score` `errorMessage` sentiment als dit is gelukt en een zo niet. 

Een score die dicht bij 0 is, duidt op een negatief sentiment, terwijl een score die dichter bij 1 ligt, wijst op een positief sentiment.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Taaldetectie

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)


Maak een nieuwe `LanguageDetectionExample()` functie genaamd die de client die `DetectLanguage()` u eerder hebt gemaakt, en de functie ervan aanroepen. Het `Response<DetectedLanguage>` geretourneerde object bevat de gedetecteerde taal samen met de naam en ISO-6391-code. Als er een fout was, `RequestFailedException`zal het gooien een .

> [!Tip]
> In sommige gevallen kan het moeilijk zijn om talen te disambiguate op basis van de input. U `countryHint` de parameter gebruiken om een landcode van 2 letters op te geven. Standaard gebruikt de API de 'US' als standaardlandHint, om dit gedrag te verwijderen kunt `countryHint = ""`u deze parameter opnieuw instellen door deze waarde in te stellen op lege tekenreeks. Als u een andere `TextAnalyticsClientOptions.DefaultCountryHint` standaard wilt instellen, stelt u de eigenschap in en geeft u deze door tijdens de initialisatie van de client.

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

Maak een nieuwe `languageDetectionExample()` functie genaamd die de client die u eerder hebt gemaakt, en roep de functie [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aan. Het geretourneerde [Object LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) bevat de `DetectedLanguages` lijst met gedetecteerde talen in indien dit is gelukt en een `errorMessage` zo niet. Druk de eerste geretourneerde taal af.

> [!Tip]
> In sommige gevallen kan het moeilijk zijn om talen te disambiguate op basis van de input. U `countryHint` de parameter gebruiken om een landcode van 2 letters op te geven. Standaard gebruikt de API de 'US' als standaardlandHint, om dit gedrag te verwijderen kunt `countryHint = ""` u deze parameter opnieuw instellen door deze waarde in te stellen op lege tekenreeks.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Uitvoer

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Benoemde entiteitsherkenning (NER)

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)


> [!NOTE]
> Nieuw in `3.0-preview`versie :
> * Entiteitsherkenning omvat nu de mogelijkheid om persoonlijke gegevens in tekst te detecteren.
> * Entiteitkoppelen is nu een gescheiden entiteitsherkenning.


Maak een nieuwe `EntityRecognitionExample()` functie genaamd die de client die `RecognizeEntities()` u eerder hebt gemaakt, de functie ervan aanroepen en herhalen door de resultaten neemt. Het `Response<IReadOnlyCollection<CategorizedEntity>>` geretourneerde object bevat de lijst met gedetecteerde entiteiten. Als er een fout was, `RequestFailedException`zal het gooien een .

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tLength: {entity.GraphemeLength},\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Uitvoer

```console
Named Entities:
        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Length: 7,      Score: 0.92

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Length: 9,      Score: 0.80
```

## <a name="detect-personal-information"></a>Persoonlijke gegevens detecteren

Maak een nieuwe `EntityPIIExample()` functie genaamd die de client die `RecognizePiiEntities()` u eerder hebt gemaakt, de functie ervan aanroepen en herhalen door de resultaten neemt. Net als bij de `Response<IReadOnlyCollection<CategorizedEntity>>` vorige functie bevat het geretourneerde object de lijst met gedetecteerde entiteiten. Als er een fout was, `RequestFailedException`zal het gooien een .

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    string inputText = "Insurance policy for SSN on file 123-12-1234 is here by approved.";
    var response = client.RecognizePiiEntities(inputText);
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tLength: {entity.GraphemeLength},\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Uitvoer

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Category: U.S. Social Security Number (SSN),    Sub-Category:
                Length: 11,     Score: 0.85
```


## <a name="entity-linking"></a>Koppeling van entiteiten

Maak een nieuwe `EntityLinkingExample()` functie genaamd die de client die `RecognizeLinkedEntities()` u eerder hebt gemaakt, de functie ervan aanroepen en herhalen door de resultaten neemt. De `Response<IReadOnlyCollection<LinkedEntity>>` geretourneerde vertegenwoordigt de lijst met gedetecteerde entiteiten. Als er een fout was, `RequestFailedException`zal het gooien een . Aangezien gekoppelde entiteiten uniek zijn geïdentificeerd, worden gebeurtenissen van `LinkedEntity` dezelfde entiteit gegroepeerd `LinkedEntityMatch` onder een object als een lijst met objecten.

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
            Console.WriteLine($"\t\tLength: {match.GraphemeLength},\tScore: {match.ConfidenceScore:F2}\n");
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
                Length: 11,     Score: 0.78

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Length: 10,     Score: 0.55

                Text: Gates
                Length: 5,      Score: 0.55

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Length: 10,     Score: 0.53

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Length: 9,      Score: 0.47

                Text: Microsoft
                Length: 9,      Score: 0.47

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Length: 7,      Score: 0.25

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Length: 5,      Score: 0.28
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

> [!NOTE]
> In versie 2.1 is entiteitskoppeling opgenomen in het NER-antwoord.

Maak een nieuwe `RecognizeEntitiesExample()` functie genaamd die de client die u eerder hebt gemaakt, en de functie [Entiteiten()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aanroepen. Door de resultaten heen te gaan. Het object returned [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) bevat de lijst `Entities` met gedetecteerde `errorMessage` entiteiten in indien succesvol en een zo niet. Print voor elke gedetecteerde entiteit de naam Type, Subtype, Wikipedia (als deze bestaan) en de locaties in de oorspronkelijke tekst.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Sleuteltermextractie

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

Maak een nieuwe `KeyPhraseExtractionExample()` functie genaamd die de client die `ExtractKeyPhrases()` u eerder hebt gemaakt, en de functie ervan aanroepen. Het `<Response<IReadOnlyCollection<string>>` geretourneerde object bevat de lijst met gedetecteerde sleutelzinnen. Als er een fout was, `RequestFailedException`zal het gooien een .

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

Maak een nieuwe `KeyPhraseExtractionExample()` functie genaamd die de client die u eerder hebt gemaakt, neemt en de functie [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aanroept. Het resultaat bevat de lijst met gedetecteerde sleutelzinnen `KeyPhrases` `errorMessage` in indien succesvol, en een zo niet. Alle gedetecteerde sleutelzinnen afdrukken.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Uitvoer

```console
Key phrases:
    cat
    veterinarian
```

---

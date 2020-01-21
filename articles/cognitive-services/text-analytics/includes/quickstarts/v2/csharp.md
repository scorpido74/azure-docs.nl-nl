---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: de6601a60bbb65787673d9d3bf5b1cbe9f46804a
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281381"
---
<a name="HOLTop"></a>

[Referentie documentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [beelden](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

> [!NOTE]
> In de code in dit artikel wordt gebruikgemaakt van de synchrone methoden van de Text Analytics .NET SDK voor eenvoud. Voor productie scenario's wordt u aangeraden de batch-asynchrone methoden te gebruiken voor prestaties en schaal baarheid. U kunt bijvoorbeeld [SentimentBatchAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentimentbatchasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet-preview) aanroepen in plaats van [sentiment ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet).

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)

## <a name="setting-up"></a>Instellen

### <a name="create-a-text-analytics-azure-resource"></a>Een Text Analytics Azure-resource maken

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-net-core-application"></a>Een nieuwe .NET core-toepassing maken

Maak een nieuwe .NET Core-Console-app met behulp van de Visual Studio IDE. Hiermee wordt een eenvoudig ' Hallo wereld '-project gemaakt met één C# bron bestand: *Program.cs*.

Installeer de client bibliotheek door met de rechter muisknop op de oplossing in de **Solution Explorer** te klikken en **NuGet-pakketten beheren**te selecteren. In package manager die wordt geopend, selecteert u **Bladeren** en zoekt u naar `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Klik erop en **Installeer**vervolgens. U kunt ook de [Package Manager-console](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)gebruiken.

Open het *Program.cs* -bestand en voeg de volgende `using`-instructies toe:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

Maak in de `Program` klasse van de toepassing variabelen voor de sleutel en het eind punt van uw resource. 

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info-v2.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Vervang de `Main` methode van de toepassing. U definieert de methoden die hier later worden genoemd.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

## <a name="object-model"></a>Object model

De Text Analytics-client is een [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) -object dat wordt geverifieerd bij Azure met behulp van uw sleutel, en biedt functies om tekst als afzonderlijke teken reeksen of als batch te accepteren. U kunt tekst synchroon of asynchroon verzenden naar de API. Het antwoord object bevat de analyse-informatie voor elk document dat u verzendt. 

## <a name="code-examples"></a>Codevoorbeelden

* [De client verifiëren](#authenticate-the-client)
* [Sentimentanalyse](#sentiment-analysis)
* [Taal detectie](#language-detection)
* [Entiteit herkenning](#entity-recognition)
* [Extractie van sleutel woorden](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>De client verifiëren

Maak een nieuwe `ApiKeyServiceClientCredentials` klasse om de referenties op te slaan en toe te voegen aan de aanvragen van de client. Maak een onderdrukking voor `ProcessHttpRequestAsync()` waarmee uw sleutel wordt toegevoegd aan de `Ocp-Apim-Subscription-Key`-header.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Maak een methode voor het instantiëren van het [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) -object met uw eind punt en een `ApiKeyServiceClientCredentials`-object dat uw sleutel bevat.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

In de `main()` methode van uw programma roept u de verificatie methode op om de client te instantiëren.

## <a name="sentiment-analysis"></a>Sentimentanalyse

Maak een nieuwe functie met de naam `SentimentAnalysisExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de functie [sentiment ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) aan. Het geretourneerde [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) -object bevat de sentiment `Score` als dit is gelukt en een `errorMessage` als dat niet het geval is. 

Een score die dicht bij 0 aangeeft, duidt op een negatieve sentiment, terwijl een score die dichter bij 1 staat, een positief sentiment aangeeft.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

### <a name="output"></a>Uitvoer

```console
Sentiment Score: 0.87
```

## <a name="language-detection"></a>Taaldetectie

Maak een nieuwe functie met de naam `languageDetectionExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de functie [DetectLanguage ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aan. Het geretourneerde [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) -object bevat de lijst met gedetecteerde talen in `DetectedLanguages` als dit is gelukt en een `errorMessage` als dat niet het geval is.  De eerste geretourneerde taal afdrukken.

> [!Tip]
> In sommige gevallen kan het lastig zijn om talen te dubbel zinnigheid op basis van de invoer. U kunt de para meter `countryHint` gebruiken om een land nummer van twee letters op te geven. Standaard gebruikt de API de standaard countryHint om dit gedrag te verwijderen, kunt u deze para meter opnieuw instellen door deze waarde in te stellen op een lege teken reeks `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Uitvoer

```console
Language: English
```

## <a name="entity-recognition"></a>Herkenning van entiteiten

Maak een nieuwe functie met de naam `RecognizeEntitiesExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de functie [entities ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aan. Herhaal de resultaten. Het geretourneerde [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) -object bevat de lijst met gedetecteerde entiteiten in `Entities` als dit is gelukt en een `errorMessage` als dat niet het geval is. Voor elke gedetecteerde entiteit, het type, subtype, naam van de Wikipedia (indien aanwezig) afdrukken, evenals de locaties in de oorspronkelijke tekst.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]


### <a name="output"></a>Uitvoer

```console
Entities:
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0,      Length: 9,      Score: 1.000
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25,     Length: 10,     Score: 1.000
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40,     Length: 10,     Score: 0.999
    Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54,     Length: 7,      Score: 0.800
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54,     Length: 13,     Score: 0.800
    Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89,     Length: 5,      Score: 0.800
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116,    Length: 11,     Score: 0.800
```

## <a name="key-phrase-extraction"></a>Sleuteluitdrukkingen extraheren

Maak een nieuwe functie met de naam `KeyPhraseExtractionExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de functie voor sleutel [zinnen ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aan. Het resultaat bevat de lijst met gedetecteerde sleutel zinnen in `KeyPhrases` als deze is geslaagd en een `errorMessage` als dat niet het geval is. Alle gedetecteerde sleutel zinnen afdrukken.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Uitvoer

```console
Key phrases:
    cat
    veterinarian
```

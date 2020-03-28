---
title: 'Snelstart: Text Analytics v3-clientbibliotheek voor Java | Microsoft Documenten'
description: Ga aan de slag met de v3 Text Analytics-clientbibliotheek voor Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: a0e6b5b7d5cedc821ee34bdd219ae07bb9d43199
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79481905"
---
<a name="HOLTop"></a>

[Voorbeeld van broncode](https://aka.ms/azsdk-java-textanalytics-ref-docs)van | [naslagdocumentatie](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [Bibliotheek](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.3) | [Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) met versie 8 of hoger
* Zodra u uw Azure-abonnement <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="hebt,"  target="_blank">maakt u <span class="docon docon-navigate-external x-hidden-focus"></span> </a> een Text Analytics-bron en maakt u een Text Analytics-bron in de Azure-portal om uw sleutel en eindpunt op te halen.  Nadat deze is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt van de resource die u maakt nodig om uw toepassing te verbinden met de Text Analytics API. U plakt uw sleutel en eindpunt later in de quickstart in de onderstaande code.
    * U de gratis`F0`prijscategorie () gebruiken om de service uit te proberen en later te upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="add-the-client-library"></a>De clientbibliotheek toevoegen

Maak een Maven project in uw gewenste IDE of ontwikkelomgeving. Voeg vervolgens de volgende afhankelijkheid toe aan het *pom.xml-bestand* van uw project. U de syntaxis van de implementatie [voor andere buildtools](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.3) online vinden.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.3</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Wilt u het hele quickstart-codebestand in één keer bekijken? U het [vinden op GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java), die de code voorbeelden in deze quickstart bevat. 

Een Java-bestand `TextAnalyticsSamples.java`met de naam . Open het bestand en `import` voeg de volgende instructies toe:

```java
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

Voeg in het javabestand een nieuwe klasse toe en voeg het sleutel- en eindpunt van uw Azure-bron toe zoals hieronder weergegeven.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Voeg de volgende hoofdmethode toe aan de klasse. U definieert de hier later aangeroepen methoden.

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizePIIEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

## <a name="object-model"></a>Objectmodel

De Text Analytics-client is een `TextAnalyticsClient` object dat met uw sleutel naar Azure wordt geverifieerd en functies biedt om tekst als afzonderlijke tekenreeksen of als batch te accepteren. U tekst synchroon of asynchroon naar de API verzenden. Het antwoordobject bevat de analysegegevens voor elk document dat u verzendt. 

## <a name="code-examples"></a>Codevoorbeelden

* [De client verifiëren](#authenticate-the-client)
* [Sentimentanalyse](#sentiment-analysis) 
* [Taaldetectie](#language-detection)
* [Benoemde entiteitsherkenning](#named-entity-recognition-ner) 
* [Koppeling van entiteiten](#entity-linking)
* [Trefwoordextractie](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>De client verifiëren

Maak een methode om het `TextAnalyticsClient` object te instantiëren met de sleutel en het eindpunt voor uw Text Analytics-bron.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .apiKey(new TextAnalyticsApiKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

Roep in de `main()` methode van uw programma de verificatiemethode aan om de client te instantiëren.

## <a name="sentiment-analysis"></a>Sentimentanalyse

Maak een nieuwe `sentimentAnalysisExample()` functie genaamd die de client die `analyzeSentiment()` u eerder hebt gemaakt, en de functie ervan aanroepen. Het `AnalyzeSentimentResult` geretourneerde `documentSentiment` object `sentenceSentiments` zal bevatten `errorMessage` en indien succesvol, of een zo niet. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
    }
}
```

### <a name="output"></a>Uitvoer

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

## <a name="language-detection"></a>Taaldetectie

Maak een nieuwe `detectLanguageExample()` functie genaamd die de client die `detectLanguage()` u eerder hebt gemaakt, en de functie ervan aanroepen. Het `DetectLanguageResult` geretourneerde object bevat een primaire taal gedetecteerd, een lijst met `errorMessage` andere talen gedetecteerd als het is gelukt, of een zo niet.

> [!Tip]
> In sommige gevallen kan het moeilijk zijn om talen te disambiguate op basis van de input. U `countryHint` de parameter gebruiken om een landcode van 2 letters op te geven. Standaard gebruikt de API de 'US' als standaardlandHint, om dit gedrag te verwijderen kunt `countryHint = ""`u deze parameter opnieuw instellen door deze waarde in te stellen op lege tekenreeks. Als u een andere `TextAnalyticsClientOptions.DefaultCountryHint` standaard wilt instellen, stelt u de eigenschap in en geeft u deze door tijdens de initialisatie van de client.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getScore());
}
```

### <a name="output"></a>Uitvoer

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```
## <a name="named-entity-recognition-ner"></a>Benoemde entiteitsherkenning (NER)

> [!NOTE]
> In `3.0-preview`versie :
> * NER bevat aparte methoden voor het detecteren van persoonlijke informatie. 
> * Entiteitkoppelen is een afzonderlijke aanvraag dan NER.

Maak een nieuwe `recognizeEntitiesExample()` functie genaamd die de client die `recognizeEntities()` u eerder hebt gemaakt, en de functie ervan aanroepen. Het `RecognizeEntitiesResult` geretourneerde object bevat `NamedEntity` een lijst `errorMessage` met als het is gelukt of een zo niet.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Uitvoer

```console
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.92.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

## <a name="using-ner-to-recognize-personal-information"></a>NER gebruiken om persoonlijke gegevens te herkennen

Maak een nieuwe `recognizePIIEntitiesExample()` functie genaamd die de client die `recognizePiiEntities()` u eerder hebt gemaakt, en de functie ervan aanroepen. Het `RecognizePiiEntitiesResult` geretourneerde object bevat `NamedEntity` een lijst `errorMessage` met als het is gelukt of een zo niet. 

```java
static void recognizePIIEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Insurance policy for SSN on file 123-12-1234 is here by approved.";

    for (PiiEntity entity : client.recognizePiiEntities(text)) {
        System.out.printf(
            "Recognized personal identifiable information entity: %s, entity category: %s, %nentity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Uitvoer

```console
Recognized personal identifiable information entity: 123-12-1234, entity category: U.S. Social Security Number (SSN), 
entity sub-category: null, score: 0.85.
```

## <a name="entity-linking"></a>Koppeling van entiteiten

Maak een nieuwe `recognizeLinkedEntitiesExample()` functie genaamd die de client die `recognizeLinkedEntities()` u eerder hebt gemaakt, en de functie ervan aanroepen. Het `RecognizeLinkedEntitiesResult` geretourneerde object bevat `LinkedEntity` een lijst `errorMessage` met als het is gelukt of een zo niet. Aangezien gekoppelde entiteiten uniek zijn geïdentificeerd, worden gebeurtenissen van `LinkedEntity` dezelfde entiteit gegroepeerd `LinkedEntityMatch` onder een object als een lijst met objecten.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
            "to develop and sell BASIC interpreters for the Altair 8800. " +
            "During his career at Microsoft, Gates held the positions of chairman, " +
            "chief executive officer, president and chief software architect, " +
            "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
                linkedEntity.getName(),
                linkedEntity.getDataSourceEntityId(),
                linkedEntity.getUrl(),
                linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getLinkedEntityMatches()) {
            System.out.printf("Text: %s, Score: %.2f%n",
                    linkedEntityMatch.getText(),
                    linkedEntityMatch.getConfidenceScore());
        }
    }
}
```

### <a name="output"></a>Uitvoer

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.78
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.55
Text: Gates, Score: 0.55
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.53
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.47
Text: Microsoft, Score: 0.47
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.25
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.28
```
## <a name="key-phrase-extraction"></a>Sleuteltermextractie

Maak een nieuwe `extractKeyPhrasesExample()` functie genaamd die de client die `extractKeyPhrases()` u eerder hebt gemaakt, en de functie ervan aanroepen. Het `ExtractKeyPhraseResult` geretourneerde object bevat een lijst met sleutelzinnen als dit is gelukt, of een `errorMessage` zo niet.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>Uitvoer

```console
Recognized phrases: 
cat
veterinarian
```

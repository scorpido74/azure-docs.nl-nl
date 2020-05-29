---
title: 'Snelstartgids: Text Analytics v3-client bibliotheek voor Java | Microsoft Docs'
description: Ga aan de slag met de V3-Text Analytics-client bibliotheek voor Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: bd070300427716634d786e685cfe1cf8e45a246c
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140703"
---
<a name="HOLTop"></a>

[Referentie documentatie](https://aka.ms/azsdk-java-textanalytics-ref-docs)  |  [Bron code](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics)  |  van bibliotheek [Pakket](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.4)  |  Voor [beelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) met versie 8 of hoger
* Wanneer u uw Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" maakt u een Text Analytics resource Maak "  target="_blank"> een Text Analytics resource <span class="docon docon-navigate-external x-hidden-focus"></span> </a> in de Azure Portal om uw sleutel en eind punt op te halen.  Nadat de app is geïmplementeerd, klikt **u op Ga naar resource**.
    * U hebt de sleutel en het eind punt nodig van de resource die u maakt om de toepassing te verbinden met de Text Analytics-API. U plakt uw sleutel en het eind punt in de onderstaande code verderop in de Quick Start.
    * U kunt de gratis prijs categorie ( `F0` ) gebruiken om de service te proberen en later te upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="add-the-client-library"></a>De client bibliotheek toevoegen

Maak een Maven-project in uw favoriete IDE-of ontwikkel omgeving. Voeg vervolgens de volgende afhankelijkheden toe aan het bestand *pom. XML* van het project. U kunt de implementatie syntaxis [voor andere build-hulpprogram ma's](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.4) online vinden.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.5</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Wilt u het volledige Quick Start-code bestand tegelijk weer geven? U kunt het vinden [op github](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java), dat de code voorbeelden in deze Snelstartgids bevat. 

Maak een Java-bestand met de naam `TextAnalyticsSamples.java` . Open het bestand en voeg de volgende- `import` instructies toe:

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

Voeg in het Java-bestand een nieuwe klasse toe en voeg de sleutel en het eind punt van uw Azure-resource toe zoals hieronder wordt weer gegeven.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Voeg de volgende hoofd methode toe aan de klasse. U definieert de methoden die hier later worden genoemd.

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

## <a name="object-model"></a>Object model

De Text Analytics-client is een `TextAnalyticsClient` object dat met uw sleutel wordt geverifieerd bij Azure, en biedt functies om tekst als afzonderlijke teken reeksen of als batch te accepteren. U kunt tekst synchroon of asynchroon verzenden naar de API. Het antwoord object bevat de analyse-informatie voor elk document dat u verzendt. 

## <a name="code-examples"></a>Codevoorbeelden

* [De client verifiëren](#authenticate-the-client)
* [Sentimentanalyse](#sentiment-analysis) 
* [Taal detectie](#language-detection)
* [Herkenning van benoemde entiteiten](#named-entity-recognition-ner) 
* [Entiteit koppelen](#entity-linking)
* [Extractie van sleutel woorden](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>De client verifiëren

Maak een methode om het object te instantiëren `TextAnalyticsClient` met de sleutel en het eind punt voor uw Text Analytics-resource.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .credential(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

In de methode van uw programma `main()` roept u de verificatie methode op om de client te instantiëren.

## <a name="sentiment-analysis"></a>Sentimentanalyse

Maak een nieuwe functie `sentimentAnalysisExample()` met de naam die de client gaat gebruiken die u eerder hebt gemaakt en roep de `analyzeSentiment()` functie op. Het geretourneerde `AnalyzeSentimentResult` object bevat `documentSentiment` en `sentenceSentiments` als dit is geslaagd, of een `errorMessage` als dat niet het geval is. 

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

Maak een nieuwe functie `detectLanguageExample()` met de naam die de client gaat gebruiken die u eerder hebt gemaakt en roep de `detectLanguage()` functie op. Het geretourneerde `DetectLanguageResult` object bevat een primaire taal, een lijst met andere talen die zijn gedetecteerd als dit is geslaagd, of een `errorMessage` als dat niet het geval is.

> [!Tip]
> In sommige gevallen kan het lastig zijn om talen te dubbel zinnigheid op basis van de invoer. U kunt de `countryHint` para meter gebruiken om een land nummer van twee letters op te geven. Standaard gebruikt de API de standaard countryHint om dit gedrag te verwijderen, kunt u deze para meter opnieuw instellen door deze waarde in te stellen op een lege teken reeks `countryHint = ""` . Als u een andere standaard instelling wilt instellen, stelt u de `TextAnalyticsClientOptions.DefaultCountryHint` eigenschap in en geeft u deze door tijdens de initialisatie van de client.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getConfidenceScore());
}
```

### <a name="output"></a>Uitvoer

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```
## <a name="named-entity-recognition-ner"></a>Herkenning van benoemde entiteiten (NER)

> [!NOTE]
> In versie `3.0` :
> * NER bevat afzonderlijke methoden voor het detecteren van persoonlijke gegevens. 
> * Entiteits koppeling is een afzonderlijke aanvraag dan NER.

Maak een nieuwe functie `recognizeEntitiesExample()` met de naam die de client gaat gebruiken die u eerder hebt gemaakt en roep de `recognizeEntities()` functie op. Het geretourneerde `RecognizeEntitiesResult` object bevat een lijst met `NamedEntity` geslaagde of mislukte aanmeldingen `errorMessage` .

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
            entity.getSubcategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Uitvoer

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

## <a name="entity-linking"></a>Entiteit koppelen

Maak een nieuwe functie `recognizeLinkedEntitiesExample()` met de naam die de client gaat gebruiken die u eerder hebt gemaakt en roep de `recognizeLinkedEntities()` functie op. Het geretourneerde `RecognizeLinkedEntitiesResult` object bevat een lijst met `LinkedEntity` geslaagde of mislukte aanmeldingen `errorMessage` . Aangezien gekoppelde entiteiten uniek worden geïdentificeerd, worden exemplaren van dezelfde entiteit gegroepeerd onder een `LinkedEntity` object als een lijst met `LinkedEntityMatch` objecten.

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
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
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
Text: Altair 8800, Score: 0.88
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63
Text: Gates, Score: 0.63
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55
Text: Microsoft, Score: 0.55
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33
```
## <a name="key-phrase-extraction"></a>Sleuteltermextractie

Maak een nieuwe functie `extractKeyPhrasesExample()` met de naam die de client gaat gebruiken die u eerder hebt gemaakt en roep de `extractKeyPhrases()` functie op. Het geretourneerde `ExtractKeyPhraseResult` object bevat een lijst met sleutel zinnen als dit is geslaagd, of een `errorMessage` als dat niet het geval is.

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

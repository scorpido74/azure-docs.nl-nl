---
title: 'Quickstart: Text Analytics v3-clientbibliotheek voor Java | Microsoft Docs'
description: Aan de slag met de v3 Text Analytics-clientbibliotheek voor Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/27/2020
ms.custom: devx-track-java
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: e0edd688fa34033209798603ef15352b42cd9497
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87451228"
---
<a name="HOLTop"></a>

[Referentiedocumentatie](https://aka.ms/azsdk-java-textanalytics-ref-docs) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [Pakket](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) | [Voorbeelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) met versie 8 of hoger
* Zodra u een Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Een Text Analytics-resource maken"  target="_blank">maakt u een Text Analytics-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om uw sleutel en eindpunt op te halen.  Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Text Analytics-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="add-the-client-library"></a>De clientbibliotheek toevoegen

Maak een Maven-project in uw favoriete IDE of ontwikkelomgeving. Voeg vervolgens de volgende afhankelijkheid toe aan het *pom.xml*-bestand van uw project. U kunt de implementatiesyntaxis [voor andere compilatieprogramma's](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) online vinden.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.0.0</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Wilt u het volledige quickstartcodebestand ineens weergeven? U kunt het vinden [op GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java), dat de codevoorbeelden in deze quickstart bevat. 

Maak een Java-bestand met de naam `TextAnalyticsSamples.java`. Open het bestand en voeg de volgende `import`-instructies toe:

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

Voeg in het Java-bestand een nieuwe klasse toe en voeg de sleutel en het eindpunt van uw Azure-resource toe, zoals hieronder wordt weergegeven.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Voeg de volgende hoofdmethode aan de klasse toe. U definieert de methoden die hier later worden aangeroepen.

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

## <a name="object-model"></a>Objectmodel

De Text Analytics-client is een `TextAnalyticsClient`-object dat wordt geverifieerd bij Azure met behulp van uw sleutel. De client biedt functies om tekst als afzonderlijke tekenreeksen of als een batch te accepteren. U kunt tekst synchroon of asynchroon naar de API verzenden. Het antwoordobject bevat de analyse-informatie voor elk document dat u verzendt. 

## <a name="code-examples"></a>Codevoorbeelden

* [De client verifiëren](#authenticate-the-client)
* [Sentimentanalyse](#sentiment-analysis) 
* [Taaldetectie](#language-detection)
* [Herkenning van benoemde entiteiten](#named-entity-recognition-ner) 
* [Entiteiten koppelen](#entity-linking)
* [Sleuteltermextractie](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>De client verifiëren

Maak een methode voor het instantiëren van het `TextAnalyticsClient`-object met de sleutel en het eindpunt voor uw Text Analytics-resource.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .credential(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

In de `main()`-methode van uw programma roept u de verificatiemethode op om de client te instantiëren.

## <a name="sentiment-analysis"></a>Sentimentanalyse

Maak een nieuwe functie met de naam `sentimentAnalysisExample()`, waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende `analyzeSentiment()`-functie aan. Het geretourneerde `AnalyzeSentimentResult`-object bevat `documentSentiment` en `sentenceSentiments` indien succesvol, of een `errorMessage` als dat niet het geval is. 

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

Maak een nieuwe functie met de naam `detectLanguageExample()`, waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende `detectLanguage()`-functie aan. Het geretourneerde `DetectLanguageResult`-object bevat een primaire taal, een lijst met andere talen die zijn gedetecteerd, indien succesvol, of een `errorMessage` als dat niet het geval is.

> [!Tip]
> In sommige gevallen kan het lastig zijn om talen ondubbelzinnig te karakteriseren op basis van de invoer. U kunt de parameter `countryHint` gebruiken om een landnummer van twee letters op te geven. Standaard gebruikt de API de standaard-countryHint US. Als u dit gedrag wilt verwijderen, kunt u deze parameter opnieuw instellen door deze waarde in te stellen op de lege tekenreeks `countryHint = ""`. Als u een andere standaardwaarde wilt instellen, stelt u eigenschap `TextAnalyticsClientOptions.DefaultCountryHint` in en geeft u deze door tijdens de initialisatie van de client.

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
> In versie `3.0`:
> * NER omvat afzonderlijke methoden voor het detecteren van persoonlijke gegevens. 
> * Entiteitskoppeling is een aanvraag die los staat van NER.

Maak een nieuwe functie met de naam `recognizeEntitiesExample()`, waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende `recognizeEntities()`-functie aan. Het geretourneerde `RecognizeEntitiesResult`-object bevat een lijst met `NamedEntity` indien succesvol, of een `errorMessage` als dat niet het geval is.

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

## <a name="entity-linking"></a>Entiteiten koppelen

Maak een nieuwe functie met de naam `recognizeLinkedEntitiesExample()`, waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende `recognizeLinkedEntities()`-functie aan. Het geretourneerde `RecognizeLinkedEntitiesResult`-object bevat een lijst met `LinkedEntity` indien succesvol, of een `errorMessage` als dat niet het geval is. Aangezien gekoppelde entiteiten uniek worden geïdentificeerd, worden exemplaren van dezelfde entiteit gegroepeerd onder een `LinkedEntity`-object als een lijst met `LinkedEntityMatch`-objecten.

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

Maak een nieuwe functie met de naam `extractKeyPhrasesExample()`, waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende `extractKeyPhrases()`-functie aan. Het geretourneerde `ExtractKeyPhraseResult`-object bevat een lijst met sleuteltermen indien succesvol, of een `errorMessage` als dat niet het geval is.

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

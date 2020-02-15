---
title: 'Snelstartgids: Text Analytics v3-client bibliotheek voor Java | Microsoft Docs'
description: Ga aan de slag met de V3-Text Analytics-client bibliotheek voor Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/11/2020
ms.author: aahi
ms.reviewer: tasharm, assafi
ms.openlocfilehash: eeaef4a9970609c43c03cd784436796a6f8af174
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211410"
---
<a name="HOLTop"></a>

[Referentie documentatie](https://aka.ms/azsdk-java-textanalytics-ref-docs) | - [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [pakket (Maven)](https://oss.sonatype.org/#nexus-search;quick~com.azure) | -voor [beelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) met versie 8 of hoger


[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-maven-project"></a>Een nieuw Maven-project maken

Voeg de volgende tekst analyse afhankelijkheid toe aan uw project. Deze versie van de afhankelijkheid maakt gebruik van versie `3.0-preview` van de Text Analytics-API. 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>
</dependencies>
```

Maak een nieuw Java-bestand in de volgende map: `\src\main\java`.

Open het Java-bestand en voeg de volgende `import`-instructies toe:

```java
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

    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizePIIEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

## <a name="object-model"></a>Object model

De Text Analytics-client is een `TextAnalyticsClient`-object dat wordt geverifieerd bij Azure met behulp van uw sleutel, en biedt functies om tekst als afzonderlijke teken reeksen of als batch te accepteren. U kunt tekst synchroon of asynchroon verzenden naar de API. Het antwoord object bevat de analyse-informatie voor elk document dat u verzendt. 

## <a name="code-examples"></a>Code voorbeelden

* [De client verifiëren](#authenticate-the-client)
* [Sentimentanalyse](#sentiment-analysis) 
* [Taal detectie](#language-detection)
* [Herkenning van benoemde entiteiten](#named-entity-recognition-ner) 
* [Entiteit koppelen](#entity-linking)
* [Extractie van sleutel woorden](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>De client verifiëren

Maak een methode om het `TextAnalyticsClient`-object te instantiëren met uw `KEY` en `ENDPOINT` eerder gemaakt.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .apiKey(new TextAnalyticsApiKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

In de `main()` methode van uw programma roept u de verificatie methode op om de client te instantiëren.

## <a name="sentiment-analysis"></a>Sentimentanalyse

Maak een nieuwe functie met de naam `sentimentAnalysisExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de `analyzeSentiment()`-functie aan. Het geretourneerde `AnalyzeSentimentResult`-object bevat `documentSentiment` en `sentenceSentiments` als dit is geslaagd, of een `errorMessage` als dat niet het geval is. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
        System.out.printf(
            "Recognized document sentiment: %s, positive score: %.2f, neutral score: %.2f, negative score: %.2f.%n",
            documentSentiment.getSentiment(),
            documentSentiment.getSentimentScores().getPositive(),
            documentSentiment.getSentimentScores().getNeutral(),
            documentSentiment.getSentimentScores().getNegative());

        for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
            System.out.printf(
                "Recognized sentence sentiment: %s, positive score: %.2f, neutral score: %.2f, negative score: %.2f.%n",
                sentenceSentiment.getSentiment(),
                sentenceSentiment.getSentimentScores().getPositive(),
                sentenceSentiment.getSentimentScores().getNeutral(),
                sentenceSentiment.getSentimentScores().getNegative());
        }
}
```

### <a name="output"></a>Uitvoer

```console
Recognized document sentiment: positive, Positive Score: 1.00, Neutral Score: 0.00, Negative Score: 0.00.
Recognized sentence sentiment: positive, positive score: 1.00, neutral score: 0.00, negative score: 0.00.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```
## <a name="language-detection"></a>Taaldetectie

Maak een nieuwe functie met de naam `detectLanguageExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de `detectLanguage()`-functie aan. Het geretourneerde `DetectLanguageResult`-object bevat een primaire taal gedetecteerd, een lijst met andere talen die zijn gedetecteerd als dit is geslaagd, of een `errorMessage` als dat niet het geval is.

> [!Tip]
> In sommige gevallen kan het lastig zijn om talen te dubbel zinnigheid op basis van de invoer. U kunt de para meter `countryHint` gebruiken om een land nummer van twee letters op te geven. Standaard gebruikt de API de standaard countryHint om dit gedrag te verwijderen, kunt u deze para meter opnieuw instellen door deze waarde in te stellen op een lege teken reeks `countryHint = ""`. Als u een andere standaard instelling wilt instellen, stelt u de eigenschap `TextAnalyticsClientOptions.DefaultCountryHint` in en geeft u deze door tijdens de initialisatie van de client.

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
## <a name="named-entity-recognition-ner"></a>Herkenning van benoemde entiteiten (NER)

> [!NOTE]
> In versie `3.0-preview`:
> * NER bevat afzonderlijke methoden voor het detecteren van persoonlijke gegevens. 
> * Entiteits koppeling is een afzonderlijke aanvraag dan NER.

Maak een nieuwe functie met de naam `recognizeEntitiesExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de `recognizeEntities()`-functie aan. Het geretourneerde `RecognizeEntitiesResult`-object bevat een lijst met `NamedEntity` als dit lukt, of een `errorMessage` als dat niet het geval is.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, offset: %s, length: %s, score: %.2f.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory() == null || entity.getSubCategory().isEmpty() ? "N/A" : entity.getSubCategory(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>Uitvoer

```console
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, offset: 26, length: 7, score: 0.92.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, offset: 34, length: 9, score: 0.80.
```

## <a name="using-ner-to-recognize-personal-information"></a>NER gebruiken om persoonlijke gegevens te herkennen

Maak een nieuwe functie met de naam `recognizePIIEntitiesExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de `recognizePiiEntities()`-functie aan. Het geretourneerde `RecognizePiiEntitiesResult`-object bevat een lijst met `NamedEntity` als dit lukt, of een `errorMessage` als dat niet het geval is. 

```java
static void recognizePIIEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Insurance policy for SSN on file 123-12-1234 is here by approved.";

    for (PiiEntity entity : client.recognizePiiEntities(text)) {
        System.out.printf(
            "Recognized personal identifiable information entity: %s, entity category: %s, entity sub-category: %s, offset: %s, length: %s, score: %.2f.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory() == null || entity.getSubCategory().isEmpty() ? "N/A" : entity.getSubCategory(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>Uitvoer

```console
Recognized personal identifiable information entity: 123-12-1234, entity category: U.S. Social Security Number (SSN), entity sub-category: N/A, offset: 33, length: 11, score: 0.85.
```

## <a name="entity-linking"></a>Entiteit koppelen

Maak een nieuwe functie met de naam `recognizeLinkedEntitiesExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de `recognizeLinkedEntities()`-functie aan. Het geretourneerde `RecognizeLinkedEntitiesResult`-object bevat een lijst met `LinkedEntity` als dit lukt, of een `errorMessage` als dat niet het geval is. Aangezien gekoppelde entiteiten uniek worden geïdentificeerd, worden exemplaren van dezelfde entiteit gegroepeerd onder een `LinkedEntity`-object als een lijst van `LinkedEntityMatch`-objecten.

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
                linkedEntity.getId(),
                linkedEntity.getUrl(),
                linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getLinkedEntityMatches()) {
            System.out.printf("Text: %s, Offset: %s, Length: %s, Score: %.2f.%n",
                    linkedEntityMatch.getText(),
                    linkedEntityMatch.getOffset(),
                    linkedEntityMatch.getLength(),
                    linkedEntityMatch.getScore());
        }
    }
}
```

### <a name="output"></a>Uitvoer

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Offset: 11, Length: 116, Score: 0.78.
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Offset: 10, Length: 25, Score: 0.55.
Text: Gates, Offset: 5, Length: 161, Score: 0.55.
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Offset: 10, Length: 40, Score: 0.53.
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Offset: 9, Length: 0, Score: 0.47.
Text: Microsoft, Offset: 9, Length: 150, Score: 0.47.
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Offset: 7, Length: 54, Score: 0.25.
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Offset: 5, Length: 89, Score: 0.28.
```
## <a name="key-phrase-extraction"></a>Sleuteltermextractie

Maak een nieuwe functie met de naam `extractKeyPhrasesExample()` die de client gaat gebruiken die u eerder hebt gemaakt en roep de `extractKeyPhrases()`-functie aan. Het geretourneerde `ExtractKeyPhraseResult`-object bevat een lijst met belang rijke woord groepen als dit is geslaagd, of een `errorMessage` als dat niet het geval is.

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

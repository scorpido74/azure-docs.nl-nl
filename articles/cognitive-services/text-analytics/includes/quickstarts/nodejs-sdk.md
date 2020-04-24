---
title: 'Snelstartgids: Text Analytics v3-client bibliotheek voor node. js | Microsoft Docs'
description: Ga aan de slag met de V3-Text Analytics-client bibliotheek voor node. js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 8bcc919aee7548e8596d1f44c8a357d3f84dfb14
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82095954"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

[V3-referentie documentatie](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [v3-bibliotheek bron code](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3 pakket (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples) -voor beelden


#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

[v2-referentie documentatie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [v2-bibliotheek bron code](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [v2 pakket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [v2](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/) -voor beelden

---

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [node. js](https://nodejs.org/).
* Wanneer u uw Azure-abonnement hebt <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=", maakt u een"  target="_blank">Text Analytics resource Maak een <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Text Analytics resource in de Azure Portal om uw sleutel en eind punt op te halen. Nadat de app is geïmplementeerd, klikt **u op Ga naar resource**.
    * U hebt de sleutel en het eind punt nodig van de resource die u maakt om de toepassing te verbinden met de Text Analytics-API. U plakt uw sleutel en het eind punt in de onderstaande code verderop in de Quick Start.
    * U kunt de gratis prijs categorie (`F0`) gebruiken om de service te proberen en later te upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor uw app en navigeer ernaar. 

```console
mkdir myapp 

cd myapp
```

Voer de `npm init` opdracht uit om een knooppunt toepassing met een `package.json` bestand te maken. 

```console
npm init
```
### <a name="install-the-client-library"></a>De client bibliotheek installeren

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

Installeer de `@azure/ai-text-analytics` NPM-pakketten:

```console
npm install --save @azure/ai-text-analytics@1.0.0-preview.4
```

> [!TIP]
> Wilt u het volledige Quick Start-code bestand tegelijk weer geven? U kunt het vinden [op github](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), dat de code voorbeelden in deze Snelstartgids bevat. 

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Installeer de `@azure/cognitiveservices-textanalytics` NPM-pakketten:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

> [!TIP]
> Wilt u het volledige Quick Start-code bestand tegelijk weer geven? U kunt het vinden [op github](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js), dat de code voorbeelden in deze Snelstartgids bevat. 

---

Het bestand van `package.json` uw app wordt bijgewerkt met de afhankelijkheden.
Maak een bestand met `index.js` de naam en voeg het volgende toe:

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```
---

Maak variabelen voor het Azure-eind punt en de sleutel van uw resource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

## <a name="object-model"></a>Object model

De Text Analytics-client is `TextAnalyticsClient` een object dat met uw sleutel wordt geverifieerd bij Azure. De-client biedt verschillende methoden voor het analyseren van tekst, als één teken reeks of een batch.

Tekst wordt naar de API verzonden als een lijst met `documents` `dictionary` objecten die een combi natie van `id`, `text`en `language` kenmerken bevatten, afhankelijk van de gebruikte methode. Het `text` kenmerk slaat de tekst op die moet worden geanalyseerd in de `language`oorsprong, en `id` kan een wille keurige waarde zijn. 

Het antwoord object is een lijst met de analyse-informatie voor elk document. 

## <a name="code-examples"></a>Codevoorbeelden

* [Client verificatie](#client-authentication)
* [Sentimentanalyse](#sentiment-analysis) 
* [Taal detectie](#language-detection)
* [Herkenning van benoemde entiteiten](#named-entity-recognition-ner)
* [Entiteit koppelen](#entity-linking)
* [Extractie van sleutel woorden](#key-phrase-extraction)

## <a name="client-authentication"></a>Clientverificatie

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

Maak een nieuw `TextAnalyticsClient` object met uw sleutel en eind punt als para meters.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Maak een nieuw [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) -object `credentials` met `endpoint` en als para meter.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Sentimentanalyse

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

Maak een matrix met teken reeksen die het document bevat dat u wilt analyseren. Roep de methode van `analyzeSentiment()` de client aan en haal `SentimentBatchResult` het geretourneerde object op. Herhaal de lijst met resultaten en druk de document-ID af op document niveau sentiment met betrouwbaarheids scores. Voor elk document bevat het resultaat sentiment samen met de verschuivingen, de lengte en de betrouwbaarheids scores.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Voer uw code uit `node index.js` in het console venster.

### <a name="output"></a>Uitvoer

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Maak een lijst met woordenlijst objecten die de documenten bevatten die u wilt analyseren. Roep de [sentiment ()-](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) methode van de client aan en haal de geretourneerde [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult)op. Herhaal de lijst met resultaten en druk de sentiment-Score van elk document af. Een score dichter bij 0 geeft aan dat er een negatieve sentiment is, terwijl een score dichter bij 1 wijst op een positieve sentiment.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Voer uw code uit `node index.js` in het console venster.

### <a name="output"></a>Uitvoer

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Taaldetectie

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

Maak een matrix met teken reeksen die het document bevat dat u wilt analyseren. Roep de methode van `detectLanguage()` de client aan en ontvang `DetectLanguageResultCollection`de geretourneerde. Herhaal vervolgens de resultaten en druk elke document-ID af met de betreffende primaire taal.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Voer uw code uit `node index.js` in het console venster.

### <a name="output"></a>Uitvoer

```console
ID: 0
        Primary Language French
```

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Maak een lijst met woordenlijst objecten die uw documenten bevatten. Roep de [detectLanguage ()-](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) methode van de client aan en haal de geretourneerde [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult)op. Herhaal vervolgens de resultaten en druk de document-ID en taal af.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Voer uw code uit `node index.js` in het console venster.

### <a name="output"></a>Uitvoer

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Herkenning van benoemde entiteiten (NER)

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

> [!NOTE]
> In versie `3.0-preview`:
> * Entiteits koppeling is een afzonderlijke aanvraag dan NER.

Maak een matrix met teken reeksen die het document bevat dat u wilt analyseren. Roep de methode van `recognizeEntities()` de client aan en `RecognizeEntitiesResult` Haal het object op. De lijst met resultaten door lopen en de naam, het type, het subtype, de offset, de lengte en de Score van de entiteit afdrukken.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Voer uw code uit `node index.js` in het console venster.

### <a name="output"></a>Uitvoer

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 1
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.67
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.81
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: interpreters      Category: PersonType    Subcategory: N/A
        Score: 0.6
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.96
        Name: Redmond   Category: Location      Subcategory: GPE
        Score: 0.09
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.31
```

## <a name="entity-linking"></a>Entiteiten koppelen

Maak een matrix met teken reeksen die het document bevat dat u wilt analyseren. Roep de methode van `recognizeLinkedEntities()` de client aan en `RecognizeLinkedEntitiesResult` Haal het object op. Herhaal de lijst met resultaten en druk de naam van de entiteit, de ID, de gegevens bron, de URL en de overeenkomsten af. Elk object in `matches` de matrix bevat een offset, lengte en score voor die overeenkomst.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.score.toFixed(2)}`);
            });
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Voer uw code uit `node index.js` in het console venster.

### <a name="output"></a>Uitvoer

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.78
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.55
                Text: Gates     Score: 0.55
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.53
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.47
                Text: Microsoft         Score: 0.47
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.25
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.28
```

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

> [!NOTE]
> In versie 2,1 wordt koppeling van entiteit opgenomen in het NER-antwoord.

Maak een lijst met objecten die uw documenten bevatten. Roep de methode [entities ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) aan en haal het [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) -object op. De lijst met resultaten door lopen en de ID van elk document afdrukken. Voor elke gedetecteerde entiteit drukt u de naam van de Wikipedia, het type en subtype (indien aanwezig) af, evenals de locaties in de oorspronkelijke tekst.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Voer uw code uit `node index.js` in het console venster.

### <a name="output"></a>Uitvoer

```console
Document ID: 1
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 0, Length: 9,   Score: 1.0
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
    Offset: 25, Length: 10, Score: 0.999847412109375
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
    Offset: 40, Length: 10, Score: 0.9988409876823425
    Name: April 4,  Type: Other,    Sub-Type: N/A
    Offset: 54, Length: 7,  Score: 0.8
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
    Offset: 54, Length: 13, Score: 0.8
    Name: BASIC,    Type: Other,    Sub-Type: N/A
    Offset: 89, Length: 5,  Score: 0.8
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
    Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 21, Length: 9,  Score: 0.999755859375
    Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
    Offset: 60, Length: 7,  Score: 0.9911284446716309
    Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
    Offset: 71, Length: 13, Score: 0.8
    Name: Seattle,  Type: Location, Sub-Type: N/A
    Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>Sleuteltermextractie

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

Maak een matrix met teken reeksen die het document bevat dat u wilt analyseren. Roep de methode van `extractKeyPhrases()` de client aan en haal `ExtractKeyPhrasesResult` het geretourneerde object op. Herhaal de resultaten en druk de ID van elk document af en alle gedetecteerde sleutel zinnen.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Voer uw code uit `node index.js` in het console venster.

### <a name="output"></a>Uitvoer

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Maak een lijst met objecten die uw documenten bevatten. Roep de methode [woordgroepen ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) van de client aan en haal het geretourneerde [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) -object op. Herhaal de resultaten en druk de ID van elk document af en alle gedetecteerde sleutel zinnen.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Voer uw code uit `node index.js` in het console venster.

### <a name="output"></a>Uitvoer

```console
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
]
```

---

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met `node` de opdracht in uw Quick Start-bestand.

```console
node index.js
```

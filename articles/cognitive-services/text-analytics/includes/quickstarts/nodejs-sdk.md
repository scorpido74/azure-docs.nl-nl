---
title: 'Snelstartgids: Text Analytics v3-client bibliotheek voor node. js | Microsoft Docs'
description: Ga aan de slag met de V3-Text Analytics-client bibliotheek voor node. js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/26/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 9f4c5e8d491bcff420e354657ff7302a22cd7583
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78155450"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

[v3-referentie documentatie](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [v3-bibliotheek bron code](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3-pakket (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | v3-voor [beelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

[v2-referentie documentatie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [v2-bibliotheek bron code](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [v2-pakket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | v2-voor [beelden](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [node. js](https://nodejs.org/).
* Wanneer u uw Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="een Text Analytics resource maken"  target="_blank">een Text Analytics resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> maken in de Azure Portal om uw sleutel en eind punt op te halen. 
    * U hebt de sleutel en het eind punt nodig van de resource die u maakt om de toepassing te verbinden met de Text Analytics-API. U gaat later in de Quick Start.
    * U kunt de gratis prijs categorie gebruiken om de service te proberen en later te upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

> [!NOTE]
> U kunt deze versie ook uitvoeren van de Text Analytics-client bibliotheek [in uw browser](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor uw app en navigeer ernaar. 

```console
mkdir myapp && cd myapp
```

Voer de `npm init` opdracht uit om een knooppunt toepassing met een `package.json` bestand te maken. 

```console
npm init
```
### <a name="install-the-client-library"></a>De client bibliotheek installeren

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

Installeer de `@azure/ai-text-analytics` NPM-pakketten:

```console
npm install --save @azure/ai-text-analytics
```

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Installeer de `@azure/cognitiveservices-textanalytics` NPM-pakketten:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

Het `package.json`-bestand van uw app wordt bijgewerkt met de afhankelijkheden.

Maak een bestand met de naam `index.js` en voeg de volgende bibliotheken toe:

---

Het `package.json`-bestand van uw app wordt bijgewerkt met de afhankelijkheden.
Maak een bestand met de naam `index.js` en voeg de volgende bibliotheken toe:

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, TextAnalyticsApiKeyCredential } = require("@azure/ai-text-analytics");
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
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Object model

De Text Analytics-client is een `TextAnalyticsClient`-object dat met behulp van uw sleutel wordt geverifieerd bij Azure. De-client biedt verschillende methoden voor het analyseren van tekst, als één teken reeks of een batch.

Tekst wordt naar de API verzonden als een lijst met `documents`. Dit zijn `dictionary` objecten die een combi natie van `id`, `text`en `language` kenmerken bevatten, afhankelijk van de gebruikte methode. Het kenmerk `text` slaat de tekst op die moet worden geanalyseerd in het oorspronkelijke `language`en de `id` kan een wille keurige waarde zijn. 

Het antwoord object is een lijst met de analyse-informatie voor elk document. 

## <a name="code-examples"></a>Code voorbeelden

* [Client verificatie](#client-authentication)
* [Sentimentanalyse](#sentiment-analysis) 
* [Taal detectie](#language-detection)
* [Herkenning van benoemde entiteiten](#named-entity-recognition-ner)
* [Entiteit koppelen](#entity-linking)
* [Extractie van sleutel woorden](#key-phrase-extraction)

## <a name="client-authentication"></a>Clientauthenticatie

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

Maak een nieuw `TextAnalyticsClient`-object met uw sleutel en eind punt als para meters.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new TextAnalyticsApiKeyCredential(key));
```

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Maak een nieuw [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) -object met `credentials` en `endpoint` als een para meter.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Sentimentanalyse

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

Maak een matrix met teken reeksen die het document bevat dat u wilt analyseren. Roep de `analyzeSentiment()` methode van de client aan en haal het geretourneerde `SentimentBatchResult`-object op. Herhaal de lijst met resultaten en druk de document-ID af op document niveau sentiment met betrouwbaarheids scores. Voor elk document bevat het resultaat sentiment samen met de verschuivingen, de lengte en de betrouwbaarheids scores.

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
        console.log(`\t\tPositive: ${document.sentimentScores.positive.toFixed(2)} \tNegative: ${document.sentimentScores.negative.toFixed(2)} \tNeutral: ${document.sentimentScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.sentimentScores.positive.toFixed(2)} \tNegative: ${sentence.sentimentScores.negative.toFixed(2)} \tNeutral: ${sentence.sentimentScores.neutral.toFixed(2)}`);
            console.log(`\t\tLength: ${sentence.length}, Offset: ${sentence.offset}`);
        })
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Voer uw code uit met `node index.js` in uw console venster.

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
                Length: 30, Offset: 0
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
                Length: 30, Offset: 31
```

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Maak een lijst met woordenlijst objecten die de documenten bevatten die u wilt analyseren. Roep de [sentiment ()-](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) methode van de client aan en haal de geretourneerde [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult)op. Herhaal de lijst met resultaten en druk de sentiment-Score van elk document af. Een score dichter bij 0 geeft aan dat er een negatieve sentiment is, terwijl een score dichter bij 1 wijst op een positieve sentiment.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Voer uw code uit met `node index.js` in uw console venster.

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

Maak een matrix met teken reeksen die het document bevat dat u wilt analyseren. Roep de `detectLanguage()` methode van de client aan en haal de geretourneerde `DetectLanguageResultCollection`op. Herhaal vervolgens de resultaten en druk elke document-ID af met de betreffende primaire taal.

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

Voer uw code uit met `node index.js` in uw console venster.

### <a name="output"></a>Uitvoer

```console
ID: 0
        Primary Language French
```

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Maak een lijst met woordenlijst objecten die uw documenten bevatten. Roep de [detectLanguage ()-](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) methode van de client aan en haal de geretourneerde [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult)op. Herhaal vervolgens de resultaten en druk de document-ID en taal af.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Voer uw code uit met `node index.js` in uw console venster.

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
> * NER bevat afzonderlijke methoden voor het detecteren van persoonlijke gegevens. 
> * Entiteits koppeling is een afzonderlijke aanvraag dan NER.

Maak een matrix met teken reeksen die het document bevat dat u wilt analyseren. Roep de `recognizeEntities()` methode van de client aan en haal het `RecognizeEntitiesResult`-object op. De lijst met resultaten door lopen en de naam, het type, het subtype, de offset, de lengte en de Score van de entiteit afdrukken.

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
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Voer uw code uit met `node index.js` in uw console venster.

### <a name="output"></a>Uitvoer

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Offset: 0, Length: 9    Score: 1
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Offset: 25, Length: 10  Score: 0.67
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Offset: 40, Length: 10  Score: 0.81
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Offset: 54, Length: 13  Score: 0.8
        Name: interpreters      Category: PersonType    Subcategory: N/A
        Offset: 95, Length: 12  Score: 0.6
        Name: 8800      Category: Quantity      Subcategory: Number
        Offset: 123, Length: 4  Score: 0.8
Document ID: 1
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Offset: 21, Length: 9   Score: 0.96
        Name: Redmond   Category: Location      Subcategory: GPE
        Offset: 60, Length: 7   Score: 0.09
        Name: 21        Category: Quantity      Subcategory: Number
        Offset: 71, Length: 2   Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
```

## <a name="using-ner-to-detect-personal-information"></a>NER gebruiken om persoonlijke gegevens te detecteren

Maak een matrix met teken reeksen die het document bevat dat u wilt analyseren. Roep de `recognizePiiEntities()` methode van de client aan en haal het `EntitiesBatchResult`-object op. De lijst met resultaten door lopen en de naam, het type, het subtype, de offset, de lengte en de Score van de entiteit afdrukken.


```javascript
async function entityPiiRecognition(client){

    const entityPiiInput = [
        "Insurance policy for SSN on file 123-12-1234 is here by approved."
    ];
    const entityPiiResults = await client.recognizePiiEntities(entityPiiInput);

    entityPiiResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityPiiRecognition(textAnalyticsClient);
```

Voer uw code uit met `node index.js` in uw console venster.

### <a name="output"></a>Uitvoer

```console
Document ID: 0
        Name: 123-12-1234       Category: U.S. Social Security Number (SSN)     Subcategory: N/A
        Offset: 33, Length: 11  Score: 0.85
```

## <a name="entity-linking"></a>Entiteiten koppelen

Maak een matrix met teken reeksen die het document bevat dat u wilt analyseren. Roep de `recognizeLinkedEntities()` methode van de client aan en haal het `RecognizeLinkedEntitiesResult`-object op. Herhaal de lijst met resultaten en druk de naam van de entiteit, de ID, de gegevens bron, de URL en de overeenkomsten af. Elk object in `matches` matrix bevat een offset, lengte en score voor die overeenkomst.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.id} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text}`);
                console.log(`\t\tOffset: ${match.offset}, Length: ${match.length} \tScore: ${match.score.toFixed(3)}`);
            });
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Voer uw code uit met `node index.js` in uw console venster.

### <a name="output"></a>Uitvoer

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Offset: 116, Length: 11         Score: 0.777
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25, Length: 10  Score: 0.555
                Text: Gates
                Offset: 161, Length: 5  Score: 0.555
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40, Length: 10  Score: 0.533
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0, Length: 9    Score: 0.469
                Text: Microsoft
                Offset: 150, Length: 9  Score: 0.469
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54, Length: 7   Score: 0.248
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89, Length: 5   Score: 0.281
```

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

> [!NOTE]
> In versie 2,1 wordt koppeling van entiteit opgenomen in het NER-antwoord.

Maak een lijst met objecten die uw documenten bevatten. Roep de methode [entities ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) aan en haal het [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) -object op. De lijst met resultaten door lopen en de ID van elk document afdrukken. Voor elke gedetecteerde entiteit drukt u de naam van de Wikipedia, het type en subtype (indien aanwezig) af, evenals de locaties in de oorspronkelijke tekst.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Voer uw code uit met `node index.js` in uw console venster.

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

Maak een matrix met teken reeksen die het document bevat dat u wilt analyseren. Roep de `extractKeyPhrases()` methode van de client aan en haal het geretourneerde `ExtractKeyPhrasesResult`-object op. Herhaal de resultaten en druk de ID van elk document af en alle gedetecteerde sleutel zinnen.

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

Voer uw code uit met `node index.js` in uw console venster.

### <a name="output"></a>Uitvoer

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

Maak een lijst met objecten die uw documenten bevatten. Roep de methode [woordgroepen ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) van de client aan en haal het geretourneerde [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) -object op. Herhaal de resultaten en druk de ID van elk document af en alle gedetecteerde sleutel zinnen.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Voer uw code uit met `node index.js` in uw console venster.

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

Voer de toepassing uit met de opdracht `node` in uw Quick Start-bestand.

```console
node index.js
```

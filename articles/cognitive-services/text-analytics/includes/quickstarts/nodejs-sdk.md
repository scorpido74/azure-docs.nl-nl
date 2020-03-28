---
title: 'Snelstart: Text Analytics v3-clientbibliotheek voor Node.js | Microsoft Documenten'
description: Ga aan de slag met de v3 Text Analytics-clientbibliotheek voor Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: a0616a8a2cc401b6b4c42c9882c14da2f123c6df
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79481871"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

[v3 Referentiedocumentatie](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [v3 Bibliotheek broncode](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3 Package (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3 Samples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

[v2 Referentiedocumentatie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [v2 Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [v2-pakket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [v2-voorbeelden](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [Node.js](https://nodejs.org/).
* Zodra u uw Azure-abonnement <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="hebt,"  target="_blank">maakt u <span class="docon docon-navigate-external x-hidden-focus"></span> </a> een Text Analytics-bron en maakt u een Text Analytics-bron in de Azure-portal om uw sleutel en eindpunt op te halen. Nadat deze is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt van de resource die u maakt nodig om uw toepassing te verbinden met de Text Analytics API. U plakt uw sleutel en eindpunt later in de quickstart in de onderstaande code.
    * U de gratis`F0`prijscategorie () gebruiken om de service uit te proberen en later te upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor uw app en navigeer ernaar. 

```console
mkdir myapp 

cd myapp
```

Voer `npm init` de opdracht uit om een `package.json` knooppunttoepassing met een bestand te maken. 

```console
npm init
```
### <a name="install-the-client-library"></a>De clientbibliotheek installeren

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

Installeer `@azure/ai-text-analytics` de NPM-pakketten:

```console
npm install --save @azure/ai-text-analytics
```

> [!TIP]
> Wilt u het hele quickstart-codebestand in één keer bekijken? U het [vinden op GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), die de code voorbeelden in deze quickstart bevat. 

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Installeer `@azure/cognitiveservices-textanalytics` de NPM-pakketten:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

> [!TIP]
> Wilt u het hele quickstart-codebestand in één keer bekijken? U het [vinden op GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js), die de code voorbeelden in deze quickstart bevat. 

---

Het bestand `package.json` van uw app wordt bijgewerkt met de afhankelijkheden.
Maak een `index.js` bestand met de naam en voeg het volgende toe:

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, TextAnalyticsApiKeyCredential } = require("@azure/ai-text-analytics");
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```
---

Maak variabelen voor het Azure-eindpunt en de sleutel van uw resource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Objectmodel

De Text Analytics-client is een `TextAnalyticsClient` object dat met uw sleutel naar Azure wordt geverifieerd. De client biedt verschillende methoden voor het analyseren van tekst, als één tekenreeks of als batch.

Tekst wordt naar de API `documents`verzonden `dictionary` als een lijst `id`van `text`, `language` die objecten zijn die een combinatie van , en kenmerken bevatten, afhankelijk van de gebruikte methode. Het `text` kenmerk slaat de tekst op `language`die moet `id` worden geanalyseerd in de oorsprong en deze kan elke waarde zijn. 

Het antwoordobject is een lijst met de analysegegevens voor elk document. 

## <a name="code-examples"></a>Codevoorbeelden

* [Clientverificatie](#client-authentication)
* [Sentimentanalyse](#sentiment-analysis) 
* [Taaldetectie](#language-detection)
* [Benoemde entiteitsherkenning](#named-entity-recognition-ner)
* [Koppeling van entiteiten](#entity-linking)
* [Trefwoordextractie](#key-phrase-extraction)

## <a name="client-authentication"></a>Clientverificatie

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

Maak een `TextAnalyticsClient` nieuw object met uw sleutel en eindpunt als parameters.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new TextAnalyticsApiKeyCredential(key));
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Maak een nieuw [TextAnalyticsClient-object](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) met `credentials` en `endpoint` als parameter.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Sentimentanalyse

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

Maak een array met tekenreeksen met het document dat u wilt analyseren. Bel de methode `analyzeSentiment()` van de `SentimentBatchResult` klant en ontvang het geretourneerde object. Door de lijst met resultaten te herhalen en de ID van elk document af te drukken, documentniveausentiment met vertrouwensscores. Voor elk document bevat het resultaat het sentiment op zinsniveau, samen met verschuivingen, lengte- en betrouwbaarheidsscores.

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

Voer uw `node index.js` code uit met in uw consolevenster.

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

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Maak een lijst met woordenboekobjecten met de documenten die u wilt analyseren. Bel de [methode sentiment()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) van de klant en ontvang de geretourneerde [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult). Door de lijst met resultaten te herhalen en de id- en gevoelsscore van elk document af te drukken. Een score dichter bij 0 duidt op een negatief sentiment, terwijl een score dichter bij 1 wijst op een positief sentiment.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Voer uw `node index.js` code uit met in uw consolevenster.

### <a name="output"></a>Uitvoer

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Taaldetectie

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

Maak een array met tekenreeksen met het document dat u wilt analyseren. Bel de methode `detectLanguage()` van de `DetectLanguageResultCollection`klant en krijg de geretourneerde . Vervolgens doordeeren van de resultaten en druk de ID van elk document af met de respectievelijke primaire taal.

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

Voer uw `node index.js` code uit met in uw consolevenster.

### <a name="output"></a>Uitvoer

```console
ID: 0
        Primary Language French
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Maak een lijst met woordenboekobjecten die uw documenten bevatten. Bel de methode [detectLanguage()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) van de client en ontvang het geretourneerde [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult). Doordeins vervolgens de resultaten en druk de id en taal van elk document af.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Voer uw `node index.js` code uit met in uw consolevenster.

### <a name="output"></a>Uitvoer

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Benoemde entiteitsherkenning (NER)

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

> [!NOTE]
> In `3.0-preview`versie :
> * NER bevat aparte methoden voor het detecteren van persoonlijke informatie. 
> * Entiteitkoppelen is een afzonderlijke aanvraag dan NER.

Maak een array met tekenreeksen met het document dat u wilt analyseren. Bel de methode `recognizeEntities()` van de `RecognizeEntitiesResult` klant en krijg het object. Door de lijst met resultaten te herhalen en de entiteitsnaam, type, subtype, verschuiving, lengte en score af te drukken.

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

Voer uw `node index.js` code uit met in uw consolevenster.

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

## <a name="using-ner-to-detect-personal-information"></a>NER gebruiken om persoonlijke gegevens te detecteren

Maak een array met tekenreeksen met het document dat u wilt analyseren. Bel de methode `recognizePiiEntities()` van de `EntitiesBatchResult` klant en krijg het object. Door de lijst met resultaten te herhalen en de entiteitsnaam, type, subtype, verschuiving, lengte en score af te drukken.


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
            console.log(`\tScore: ${entity.score}`);
        });
    });
}
entityPiiRecognition(textAnalyticsClient);
```

Voer uw `node index.js` code uit met in uw consolevenster.

### <a name="output"></a>Uitvoer

```console
Document ID: 0
        Name: 123-12-1234       Category: U.S. Social Security Number (SSN)     Subcategory: N/A
        Score: 0.85
```

## <a name="entity-linking"></a>Entiteiten koppelen

Maak een array met tekenreeksen met het document dat u wilt analyseren. Bel de methode `recognizeLinkedEntities()` van de `RecognizeLinkedEntitiesResult` klant en krijg het object. Door de lijst met resultaten te herhalen en druk de entiteitsnaam, id, gegevensbron, url en overeenkomsten af. Elk object `matches` in de array bevat verschuiving, lengte en score voor die overeenkomst.

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

Voer uw `node index.js` code uit met in uw consolevenster.

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

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

> [!NOTE]
> In versie 2.1 is entiteitskoppeling opgenomen in het NER-antwoord.

Maak een lijst met objecten met uw documenten. Roep de methode [entiteiten()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) van de client aan en download het object [EntiteitenBatchResult.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) Door de lijst met resultaten te herhalen en de id van elk document af te drukken. Print voor elke gedetecteerde entiteit de wikipedia-naam, het type en de subtypen (indien aanwezig) en de locaties in de oorspronkelijke tekst.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Voer uw `node index.js` code uit met in uw consolevenster.

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

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

Maak een array met tekenreeksen met het document dat u wilt analyseren. Bel de methode `extractKeyPhrases()` van de `ExtractKeyPhrasesResult` klant en ontvang het geretourneerde object. Doorspoel de resultaten en druk de id van elk document en alle gedetecteerde sleutelzinnen af.

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

Voer uw `node index.js` code uit met in uw consolevenster.

### <a name="output"></a>Uitvoer

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Maak een lijst met objecten met uw documenten. Roep de methode [keyPhrases()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) van de client aan en ontvang het geretourneerde [keyphraseBatchResult-object.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) Doorspoel de resultaten en druk de id van elk document en alle gedetecteerde sleutelzinnen af.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Voer uw `node index.js` code uit met in uw consolevenster.

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

Voer de toepassing `node` uit met de opdracht voor uw quickstartbestand.

```console
node index.js
```

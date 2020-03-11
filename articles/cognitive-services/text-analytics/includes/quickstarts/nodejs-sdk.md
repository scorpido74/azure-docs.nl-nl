---
title: 'Quickstart: Text Analytics v3-clientbibliotheek voor Node.js | Microsoft Docs'
description: Ga aan de slag met de v3 Text Analytics-clientbibliotheek voor Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 4ce2da3070105f6e098373108164b6f590d423c6
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78924886"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Versie 3.0-preview](#tab/version-3)

[v3-referentiedocumentatie](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [broncode voor v3-bibliotheek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3-pakket (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3-voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

[v2-referentiedocumentatie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [broncode voor v2-bibliotheek](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [v2-pakket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [v2-voorbeelden](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/)
* De huidige versie van [Node.js](https://nodejs.org/).
* Zodra u een Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Een Text Analytics-resource maken"  target="_blank">maakt u een Text Analytics-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om uw sleutel en eindpunt op te halen. 
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Text Analytics-API. U doet dit later in de quickstart.
    * U kunt de gratis prijscategorie gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

> [!NOTE]
> U kunt deze versie van de Text Analytics-clientbibliotheek ook uitvoeren [in de browser](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map. 

```console
mkdir myapp && cd myapp
```

Voer de opdracht `npm init` uit om een knooppunttoepassing te maken met een `package.json`-bestand. 

```console
npm init
```
### <a name="install-the-client-library"></a>De clientbibliotheek installeren

#### <a name="version-30-preview"></a>[Versie 3.0-preview](#tab/version-3)

De `@azure/ai-text-analytics` NPM-pakketten installeren:

```console
npm install --save @azure/ai-text-analytics
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

De `@azure/cognitiveservices-textanalytics` NPM-pakketten installeren:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

Het `package.json`-bestand van uw app wordt bijgewerkt met de afhankelijkheden.

Maak een bestand met de naam `index.js` en voeg de volgende bibliotheken toe:

---

Het `package.json`-bestand van uw app wordt bijgewerkt met de afhankelijkheden.
Maak een bestand met de naam `index.js` en voeg de volgende bibliotheken toe:

#### <a name="version-30-preview"></a>[Versie 3.0-preview](#tab/version-3)

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

Maak variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Objectmodel

De Text Analytics-client is een `TextAnalyticsClient`-object dat met behulp van uw sleutel wordt geverifieerd bij Azure. De client biedt verschillende methoden voor het analyseren van tekst, zoals één tekenreeks of een batch.

Tekst wordt naar de API verzonden als een lijst met `documents`. Dit zijn `dictionary`-objecten die een combinatie van de kenmerken `id`, `text` en `language` bevatten, afhankelijk van de gebruikte methode. Met het kenmerk `text` wordt de tekst die moet worden geanalyseerd, opgeslagen in de oorsprong `language`, en `id` kan elke waarde hebben. 

Het antwoordobject is een lijst met de analyse-informatie voor elk document. 

## <a name="code-examples"></a>Codevoorbeelden

* [Clientauthenticatie](#client-authentication)
* [Sentimentanalyse](#sentiment-analysis) 
* [Taaldetectie](#language-detection)
* [Herkenning van benoemde entiteiten](#named-entity-recognition-ner)
* [Entiteiten koppelen](#entity-linking)
* [Sleuteltermextractie](#key-phrase-extraction)

## <a name="client-authentication"></a>Clientauthenticatie

#### <a name="version-30-preview"></a>[Versie 3.0-preview](#tab/version-3)

Maak een nieuw `TextAnalyticsClient`-object met uw sleutel en eindpunt als parameters.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new TextAnalyticsApiKeyCredential(key));
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Maak een nieuw [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient)-object met `credentials` en `endpoint` als parameters.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Sentimentanalyse

#### <a name="version-30-preview"></a>[Versie 3.0-preview](#tab/version-3)

Maak een matrix met tekenreeksen die het document bevat dat u wilt analyseren. Roep de methode `analyzeSentiment()` van de client aan en haal het geretourneerde `SentimentBatchResult`-object op. Blader door de lijst met resultaten en druk voor elk document de id en het gevoel op documentniveau met betrouwbaarheidsscores af. Voor elk document bevat het resultaat gevoel op zinsniveau, samen met offset, lengte en betrouwbaarheidsscores.

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

Voer de code met `node index.js` uit in het consolevenster.

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

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Maak een lijst met woordenlijstobjecten die de documenten bevat die u wilt analyseren. Roep de methode [sentiment()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) van de client aan en haal het geretourneerde [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult) op. Blader door de lijst met resultaten en druk voor elk document de id en gevoelsscore af. Een score dichter bij 0 wijst op een negatief gevoel, terwijl een score dichter bij 1 op een positief gevoel wijst.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Voer de code met `node index.js` uit in het consolevenster.

### <a name="output"></a>Uitvoer

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Taaldetectie

#### <a name="version-30-preview"></a>[Versie 3.0-preview](#tab/version-3)

Maak een matrix met tekenreeksen die het document bevat dat u wilt analyseren. Roep de methode `detectLanguage()` van de client aan en haal het geretourneerde `DetectLanguageResultCollection` op. Blader vervolgens door de resultaten en druk voor elk document de id af, met de bijbehorende primaire taal.

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

Voer de code met `node index.js` uit in het consolevenster.

### <a name="output"></a>Uitvoer

```console
ID: 0
        Primary Language French
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Maak een lijst met woordenlijstobjecten die uw documenten bevat. Roep de methode [detectLanguage()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) van de client aan en haal het geretourneerde [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult) op. Blader vervolgens door de resultaten en druk voor elk document id en taal af.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Voer de code met `node index.js` uit in het consolevenster.

### <a name="output"></a>Uitvoer

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>NER (Herkenning van benoemde entiteiten)

#### <a name="version-30-preview"></a>[Versie 3.0-preview](#tab/version-3)

> [!NOTE]
> In versie `3.0-preview`:
> * NER omvat afzonderlijke methoden voor het detecteren van persoonlijke gegevens. 
> * Entiteitskoppeling is een aanvraag die los staat van NER.

Maak een matrix met tekenreeksen die het document bevat dat u wilt analyseren. Roep de methode `recognizeEntities()` van de client aan en haal het `RecognizeEntitiesResult`-object op. Blader door de lijst met resultaten, en druk naam, id, gegevensbron, URL en overeenkomsten voor de entiteit af.

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

Voer de code met `node index.js` uit in het consolevenster.

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

Maak een matrix met tekenreeksen die het document bevat dat u wilt analyseren. Roep de methode `recognizePiiEntities()` van de client aan en haal het `EntitiesBatchResult`-object op. Blader door de lijst met resultaten, en druk naam, id, gegevensbron, URL en overeenkomsten voor de entiteit af.


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

Voer de code met `node index.js` uit in het consolevenster.

### <a name="output"></a>Uitvoer

```console
Document ID: 0
        Name: 123-12-1234       Category: U.S. Social Security Number (SSN)     Subcategory: N/A
        Offset: 33, Length: 11  Score: 0.85
```

## <a name="entity-linking"></a>Entiteiten koppelen

Maak een matrix met tekenreeksen die het document bevat dat u wilt analyseren. Roep de methode `recognizeLinkedEntities()` van de client aan en haal het `RecognizeLinkedEntitiesResult`-object op. Blader door de lijst met resultaten, en druk naam, id, gegevensbron, URL en overeenkomsten voor de entiteit af. Elk object in de matrix `matches` bevat offset, lengte en score voor deze overeenkomst.

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

Voer de code met `node index.js` uit in het consolevenster.

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

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

> [!NOTE]
> In versie 2.1 wordt entiteitskoppeling opgenomen in het NER-antwoord.

Maak een lijst met objecten die uw documenten bevat. Roep de methode [entities()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) van de client aan en haal het [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult)-object op. Blader door de lijst met resultaten en druk voor elk document de id af. Voor elke gedetecteerde entiteit drukt u bijbehorende Wikipedia-naam, type en subtypen (indien deze bestaan) af, en ook de locaties in de oorspronkelijke tekst.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Voer de code met `node index.js` uit in het consolevenster.

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

#### <a name="version-30-preview"></a>[Versie 3.0-preview](#tab/version-3)

Maak een matrix met tekenreeksen die het document bevat dat u wilt analyseren. Roep de methode `extractKeyPhrases()` van de client aan en haal het geretourneerde `ExtractKeyPhrasesResult`-object op. Blader door de resultaten en druk voor elk document de id en gedetecteerde sleuteltermen af.

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

Voer de code met `node index.js` uit in het consolevenster.

### <a name="output"></a>Uitvoer

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Maak een lijst met objecten die uw documenten bevat. Roep de methode [keyPhrases()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) van de client aan en haal het [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult)-object op. Blader door de resultaten en druk voor elk document de id en gedetecteerde sleuteltermen af.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Voer de code met `node index.js` uit in het consolevenster.

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

Voer de toepassing uit met de opdracht `node` in uw quickstart-bestand.

```console
node index.js
```

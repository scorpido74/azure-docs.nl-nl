---
title: 'Snelstartgids: Text Analytics v3-client bibliotheek voor node. js | Microsoft Docs'
description: Ga aan de slag met de V3-Text Analytics-client bibliotheek voor node. js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/13/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 69899f521e73cb5af1af145a0915dbe1a017f307
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281142"
---
<a name="HOLTop"></a>

[Referentie documentatie](https://aka.ms/azsdk-js-textanalytics-ref-docs) | - [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [pakket (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | -voor [beelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)

> [!NOTE]
> * In deze Snelstartgids wordt gebruikgemaakt van versie `3.0-preview` van de Text Analytics-client bibliotheek, die een open bare Preview bevat voor verbeterde [sentimentanalyse](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) en [named entity Recognition (ner)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * De code in dit artikel maakt gebruik van onbeveiligde referentie opslag om redenen van eenvoud. Voor productie scenario's raden we aan om teken reeksen in batches te verzenden voor prestaties en schaal baarheid. U kunt bijvoorbeeld `SentimentBatchAsync()` aanroepen in plaats van `Sentiment()`.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [node. js](https://nodejs.org/).

## <a name="setting-up"></a>Instellen

### <a name="create-a-text-analytics-azure-resource"></a>Een Text Analytics Azure-resource maken

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor uw app en navigeer ernaar. 

```console
mkdir myapp && cd myapp
```

Voer de `npm init` opdracht uit om een knooppunt toepassing met een `package.json` bestand te maken. 

```console
npm init
```
### <a name="install-the-client-library"></a>De client bibliotheek installeren

Installeer de `@azure/cognitiveservices-textanalytics` NPM-pakketten:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

Het `package.json`-bestand van uw app wordt bijgewerkt met de afhankelijkheden.

Maak een bestand met de naam `index.js` en voeg de volgende bibliotheken toe:

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```

Maak variabelen voor het Azure-eind punt en de sleutel van uw resource.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Object model

De Text Analytics-client is een `TextAnalyticsClient`-object dat met behulp van uw sleutel wordt geverifieerd bij Azure. De-client biedt verschillende methoden voor het analyseren van tekst, als één teken reeks of een batch.

Tekst wordt naar de API verzonden als een lijst met `documents`. Dit zijn `dictionary` objecten die een combi natie van `id`, `text`en `language` kenmerken bevatten, afhankelijk van de gebruikte methode. Het kenmerk `text` slaat de tekst op die moet worden geanalyseerd in het oorspronkelijke `language`en de `id` kan een wille keurige waarde zijn. 

Het antwoord object is een lijst met de analyse-informatie voor elk document. 

## <a name="code-examples"></a>Codevoorbeelden

* [Clientauthenticatie](#client-authentication)
* [Sentimentanalyse](#sentiment-analysis) (open bare preview)
* [Taal detectie](#language-detection)
* [Herkenning van benoemde entiteiten](#named-entity-recognition-public-preview) (open bare preview)
* [Herkenning van benoemde entiteiten-persoonlijke gegevens](#named-entity-recognition---personal-information-public-preview) (open bare preview)
* [Entiteit koppelen](#entity-linking)
* [Extractie van sleutel woorden](#key-phrase-extraction)

## <a name="client-authentication"></a>Clientauthenticatie

Maak een nieuw `TextAnalyticsClient`-object met uw sleutel en eind punt als para meters.

```javascript
const client = new TextAnalyticsClient(endpoint,  new CognitiveServicesCredential(key));
```

## <a name="sentiment-analysis-public-preview"></a>Sentiment analyse (open bare preview)

> [!NOTE]
> De onderstaande code is voor sentiment Analysis v3, een open bare preview-versie.

Maak een matrix met teken reeksen die het document bevat dat u wilt analyseren. Roep de `analyzeSentiment()` methode van de client aan en haal het geretourneerde `SentimentBatchResult`-object op. Herhaal de lijst met resultaten en druk de document-ID af op document niveau sentiment met betrouwbaarheids scores. Voor elk document bevat het resultaat sentiment samen met de verschuivingen, de lengte en de betrouwbaarheids scores.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ]

    const sentimentResult = await client.analyzeSentiment(sentimentInput);
    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.documentScores.positive.toFixed(2)} \tNegative: ${document.documentScores.negative.toFixed(2)} \tNeutral: ${document.documentScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.sentenceScores.positive.toFixed(2)} \tNegative: ${sentence.sentenceScores.negative.toFixed(2)} \tNeutral: ${sentence.sentenceScores.neutral.toFixed(2)}`);
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
                Positive: 0.61  Negative: 0.01  Neutral: 0.39
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

## <a name="language-detection"></a>Taaldetectie

Maak een matrix met teken reeksen die het document bevat dat u wilt analyseren. Roep de `detectLanguages()` methode van de client aan en haal de geretourneerde `DetectLanguageResult`op. Herhaal vervolgens de resultaten en druk elke document-ID af met de betreffende primaire en gedetecteerde taal.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ]

    const languageResult = await client.detectLanguages(languageInputArray);

    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tDetected Language ${language.name}`)
        );
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Voer uw code uit met `node index.js` in uw console venster.

### <a name="output"></a>Uitvoer

```console
ID: 0
        Detected Language French
        Primary Language French
```

## <a name="named-entity-recognition"></a>Herkenning van benoemde entiteiten

> [!NOTE]
> De onderstaande code is voor named entity Recognition v3, een open bare preview-versie.

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
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
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
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 0, Length: 9    Score: 1
        Name: Bill Gates        Type: Person    Sub Type: N/A
        Offset: 25, Length: 10  Score: 0.999786376953125
        Name: Paul Allen        Type: Person    Sub Type: N/A
        Offset: 40, Length: 10  Score: 0.9988105297088623
        Name: April 4, 1975     Type: DateTime  Sub Type: Date
        Offset: 54, Length: 13  Score: 0.8
        Name: Altair    Type: Organization      Sub Type: N/A
        Offset: 116, Length: 6  Score: 0.7996330857276917
        Name: 8800      Type: Quantity  Sub Type: Number
        Offset: 123, Length: 4  Score: 0.8
Document ID: 1
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 21, Length: 9   Score: 0.9837456345558167
        Name: 21        Type: Quantity  Sub Type: Number
        Offset: 71, Length: 2   Score: 0.8
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>Herkenning van benoemde entiteiten-persoonlijke gegevens (open bare preview)

> [!NOTE]
> De onderstaande code is voor het detecteren van persoonlijke gegevens met behulp van named entity Recognition v3, dat zich in de open bare preview bevindt.

Maak een matrix met teken reeksen die het document bevat dat u wilt analyseren. Roep de `recognizePiiEntities()` methode van de client aan en haal het `EntitiesBatchResult`-object op. De lijst met resultaten door lopen en de naam, het type, het subtype, de offset, de lengte en de Score van de entiteit afdrukken.


```javascript
async function entityPiiRecognition(client){

    const entityPiiInput = [
        "Insurance policy for SSN on file 123-12-1234 is here by approved."
    ]
    const entityResults = await client.recognizePiiEntities(entityPiiInput);

    result.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
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
        Name: 123-12-1234       Type: U.S. Social Security Number (SSN)         Sub Type: N/A
        Offset: 33, Length: 11  Score: 0.85
```

## <a name="entity-linking"></a>Entiteiten koppelen

Maak een matrix met teken reeksen die het document bevat dat u wilt analyseren. Roep de `recognizeLinkedEntities()` methode van de client aan en haal het `RecognizeLinkedEntitiesResult`-object op. Herhaal de lijst met resultaten en druk de naam van de entiteit, de ID, de gegevens bron, de URL en de overeenkomsten af. Elk object in `matches` matrix bevat een offset, lengte en score voor die overeenkomst.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ]
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.id} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text}`);
                console.log(`\t\tOffset: ${match.offset}, Length: ${match.length} \tScore: ${match.score.toFixed(3)}`);
            })
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
                Offset: 116, Length: 11         Score: 0.650
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25, Length: 10  Score: 0.243
                Text: Gates
                Offset: 161, Length: 5  Score: 0.243
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40, Length: 10  Score: 0.174
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0, Length: 9    Score: 0.196
                Text: Microsoft
                Offset: 150, Length: 9  Score: 0.196
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54, Length: 7   Score: 0.137
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89, Length: 5   Score: 0.052
```

## <a name="key-phrase-extraction"></a>Sleuteluitdrukkingen extraheren

Maak een matrix met teken reeksen die het document bevat dat u wilt analyseren. Roep de `extractKeyPhrases()` methode van de client aan en haal het geretourneerde `ExtractKeyPhrasesResult`-object op. Herhaal de resultaten en druk de ID van elk document af en alle gedetecteerde sleutel zinnen.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ]

    const result = await client.extractKeyPhrases(keyPhrasesInput)


    result.forEach(document => {
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

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `node` in uw Quick Start-bestand.

```console
node index.js
```

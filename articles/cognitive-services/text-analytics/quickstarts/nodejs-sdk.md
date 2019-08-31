---
title: 'Quickstart: Text Analytics-client bibliotheek voor node. js | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Bekijk informatie en codevoorbeelden om snel aan de slag te gaan met de Text Analytics-API.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: shthowse
ms.openlocfilehash: c3cd64d0a683a60132808bca6a7ceb4aa84db4f1
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195205"
---
# <a name="quickstart-text-analytics-client-library-for-nodejs"></a>Quickstart: Tekst analyse-client bibliotheek voor node. js
<a name="HOLTop"></a>

Ga aan de slag met de Text Analytics-client bibliotheek voor. Node. js. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen. 

Gebruik de Text Analytics-client bibliotheek voor node. js om de volgende handelingen uit te voeren:

* Sentimentanalyse
* Taaldetectie
* Entiteit herkenning
* Sleuteltermextractie

[](https://docs.microsoft.com/javascript/api/overview/azure/cognitiveservices/textanalytics?view=azure-node-latest) | NPM-voor[beelden](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/) ([Source code](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [package)](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) | voor referentie documentatie

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [node. js](https://nodejs.org/).

## <a name="setting-up"></a>Instellen

### <a name="create-a-text-analytics-azure-resource"></a>Een Text Analytics Azure-resource maken

Azure-Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor tekst analyse met behulp van de [Azure Portal](../../cognitive-services-apis-create-account.md) of [Azure cli](../../cognitive-services-apis-create-account-cli.md) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proef versie](https://azure.microsoft.com/try/cognitive-services/#decision) die 7 dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Bekijk uw resource op het [Azure Portal](https://portal.azure.com/)

Wanneer u een sleutel van uw proef abonnement of resource hebt ontvangen, [maakt u een omgevings variabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de `TEXTANALYTICS_SUBSCRIPTION_KEY`sleutel met de naam.

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor uw app en navigeer ernaar. 

```console
mkdir myapp && cd myapp
```

Voer de `npm init` opdracht uit om een knooppunt toepassing met een `package.json` bestand te maken. 

```console
npm init
```

Maak een bestand met `index.js` de naam en importeer de volgende bibliotheken:

```javascript
const CognitiveServicesCredentials = require("@azure/ms-rest-js");
const TextAnalyticsAPIClient = require("@azure/cognitiveservices-textanalytics");
```

Maak variabelen voor het Azure-eind punt en de abonnements sleutel van uw resource. Haal deze waarden op uit de omgevings variabelen TEXT_ANALYTICS_SUBSCRIPTION_KEY en TEXT_ANALYTICS_ENDPOINT. Als u deze omgevings variabelen hebt gemaakt nadat u begon met het bewerken van de toepassing, moet u de editor, IDE of shell die u gebruikt voor toegang tot de variabelen sluiten en opnieuw openen.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```javascript
const key_var = 'TEXT_ANALYTICS_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('please set/export the following environment variable: ' + key_var);
}
const subscription_key = process.env[key_var];

const endpoint_var = 'TEXT_ANALYTICS_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('please set/export the following environment variable: ' + endpoint_var);
}
const endpoint = process.env[endpoint_var];
```

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Installeer de `@azure/ms-rest-js` en `@azure/cognitiveservices-textanalytics` NPM-pakketten:

```console
npm install @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

Het bestand van `package.json` uw app wordt bijgewerkt met de afhankelijkheden.

## <a name="object-model"></a>Object model

De Text Analytics-client is een [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest) -object dat met behulp van uw sleutel wordt geverifieerd bij Azure. De-client biedt verschillende methoden voor het analyseren van tekst, als één teken reeks of een batch.

Tekst wordt naar de API verzonden als een lijst met `documents` `dictionary` objecten die een combi natie van `id`, `text`en `language` kenmerken bevatten, afhankelijk van de gebruikte methode. Het `text` kenmerk slaat de tekst op die moet worden geanalyseerd in de `language`oorsprong, en `id` kan een wille keurige waarde zijn. 

Het antwoord object is een lijst met de analyse-informatie voor elk document. 

## <a name="code-examples"></a>Code voorbeelden

* [De client verifiëren](#authenticate-the-client)
* [Sentimentanalyse](#sentiment-analysis)
* [Taal detectie](#language-detection)
* [Entiteit herkenning](#entity-recognition)
* [Extractie van sleutel woorden](#key-phrase-extraction)


## <a name="authenticate-the-client"></a>De client verifiëren

Maak een nieuw [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest) -object `credentials` met `endpoint` en als para meter.

```javascript
const creds = new CognitiveServicesCredentials.ApiKeyCredentials({ inHeader: { 'Ocp-Apim-Subscription-Key': subscription_key } });
const client = new TextAnalyticsAPIClient.TextAnalyticsClient(creds, endpoint);
```

## <a name="sentiment-analysis"></a>Sentimentanalyse

Maak een lijst met objecten die de documenten bevatten die u wilt analyseren.

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."}
]}
```

Roep `client.sentiment` het resultaat en ontvang het. Herhaal vervolgens de resultaten en druk de document-ID en sentiment Score af. Een score dichter bij 0 geeft aan dat er een negatieve sentiment is, terwijl een score dichter bij 1 wijst op een positieve sentiment.

```javascript
const operation = client.sentiment({multiLanguageBatchInput: inputDocuments})
operation
.then(result => {
    console.log(result.documents);
})
.catch(err => {
    throw err;
});
```

Voer uw code `node index.js` uit in het console venster.

### <a name="output"></a>Output

```console
[ { id: '1', score: 0.8723785877227783 } ]
```

## <a name="language-detection"></a>Taaldetectie

Maak een lijst met objecten die uw documenten bevatten.

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." }
    ]
    };
```

Roep `client.detectLanguage()` het resultaat en ontvang het. Herhaal vervolgens de resultaten en druk de ID van elk document af en de eerste geretourneerde taal.

```javascript
const operation = client.detectLanguage({
    languageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tLanguage: ${language.name}`)
        );
    });
    })
    .catch(err => {
    throw err;
    });
```

Voer uw code `node index.js` uit in het console venster.

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
ID: 1 Language English
```

## <a name="entity-recognition"></a>Entiteit herkenning

Maak een lijst met objecten die uw documenten bevatten.

```javascript
const inputDocuments = {
    documents: [
        { language: "en", id: "1", text: "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800" }
    ]
}
```

Roep `client.entities()` het resultaat en ontvang het. Herhaal vervolgens de resultaten en druk de document-ID af. Voor elke gedetecteerde entiteit drukt u de naam van de Wikipedia, het type en subtype (indien aanwezig) af, evenals de locaties in de oorspronkelijke tekst.

```javascript
const operation = client.entities({
    multiLanguageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`Document ID: ${document.id}`)
        document.entities.forEach(e =>{
        console.log(`\tName: ${e.name} Type: ${e.type} Sub Type: ${e.type}`)
        e.matches.forEach(match => (
            console.log(`\t\tOffset: ${match.offset} Length: ${match.length} Score: ${match.entityTypeScore}`)
        ))
        })
    });
    })
    .catch(err => {
    throw err;
    });
```

Voer uw code `node index.js` uit in het console venster.

### <a name="output"></a>Output

```console
Document ID: 1
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 0 Length: 9 Score: 1
    Name: Bill Gates Type: Person Sub Type: Person
            Offset: 25 Length: 10 Score: 0.999786376953125
    Name: Paul Allen Type: Person Sub Type: Person
            Offset: 40 Length: 10 Score: 0.9988105297088623
    Name: April 4 Type: Other Sub Type: Other
            Offset: 54 Length: 7 Score: 0.8
    Name: April 4, 1975 Type: DateTime Sub Type: DateTime
            Offset: 54 Length: 13 Score: 0.8
    Name: BASIC Type: Other Sub Type: Other
            Offset: 89 Length: 5 Score: 0.8
    Name: Altair 8800 Type: Other Sub Type: Other
            Offset: 116 Length: 11 Score: 0.8
```

## <a name="key-phrase-extraction"></a>Sleuteltermextractie

Maak een lijst met objecten die uw documenten bevatten.

```javascript
    let inputLanguage = {
    documents: [
        {language:"en", id:"1", text:"My cat might need to see a veterinarian."}
    ]
    };
```

Roep `client.keyPhrases()` het resultaat en ontvang het. Herhaal vervolgens de resultaten en druk elke document-ID en alle gedetecteerde sleutel zinnen af.

```javascript
    let operation = client.keyPhrases({
    multiLanguageBatchInput: inputLanguage
    });
    operation
    .then(result => {
        console.log(result.documents);
    })
    .catch(err => {
        throw err;
    });
```

Voer uw code `node index.js` uit in het console venster.

### <a name="output"></a>Output

```console
[
    { id: '1', keyPhrases: [ 'cat', 'veterinarian' ] }
]
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met `node` de opdracht in uw Quick Start-bestand.

```console
node index.js
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Text Analytics met Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Overzicht van Text Analytics](../overview.md)
* [Sentiment analyse](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entiteit herkenning](../how-tos/text-analytics-how-to-entity-linking.md)
* [Taal detecteren](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Taal herkenning](../how-tos/text-analytics-how-to-language-detection.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js).

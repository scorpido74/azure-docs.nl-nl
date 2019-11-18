---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: 54aadd22b8f2bbccaa4c5e4f1444aa08279a4773
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125455"
---
<a name="HOLTop"></a>

[Referentie documentatie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | - [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [pakket (NPM)](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) | -voor [beelden](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [node. js](https://nodejs.org/).

## <a name="setting-up"></a>Instellen

### <a name="create-a-text-analytics-azure-resource"></a>Een Text Analytics Azure-resource maken

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor uw app en navigeer ernaar. 

```console
mkdir myapp && cd myapp
```

Voer de `npm init` opdracht uit om een knooppunt toepassing met een `package.json` bestand te maken. 

```console
npm init
```

Maak een bestand met de naam `index.js` en voeg de volgende bibliotheken toe:

[!code-javascript[Const statements](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=constStatements)]

Maak variabelen voor het Azure-eind punt en de abonnements sleutel van uw resource. Deze waarden worden opgehaald uit de omgevings variabelen `TEXT_ANALYTICS_SUBSCRIPTION_KEY` en `TEXT_ANALYTICS_ENDPOINT`. Als u deze omgevings variabelen hebt gemaakt nadat u begon met het bewerken van de toepassing, moet u de editor, IDE of shell die u gebruikt voor toegang tot de variabelen sluiten en opnieuw openen.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

[!code-javascript[Key and endpoint vars](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyVars)]

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Installeer de `@azure/ms-rest-js`-en `@azure/cognitiveservices-textanalytics` NPM-pakketten:

```console
npm install @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

Het `package.json`-bestand van uw app wordt bijgewerkt met de afhankelijkheden.

## <a name="object-model"></a>Object model

De Text Analytics-client is een [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) -object dat met behulp van uw sleutel wordt geverifieerd bij Azure. De-client biedt verschillende methoden voor het analyseren van tekst, als één teken reeks of een batch.

Tekst wordt naar de API verzonden als een lijst met `documents`. Dit zijn `dictionary` objecten die een combi natie van `id`, `text`en `language` kenmerken bevatten, afhankelijk van de gebruikte methode. Het kenmerk `text` slaat de tekst op die moet worden geanalyseerd in het oorspronkelijke `language`en de `id` kan een wille keurige waarde zijn. 

Het antwoord object is een lijst met de analyse-informatie voor elk document. 

## <a name="code-examples"></a>Code voorbeelden

* [De client verifiëren](#authenticate-the-client)
* [Sentimentanalyse](#sentiment-analysis)
* [Taal detectie](#language-detection)
* [Entiteit herkenning](#entity-recognition)
* [Extractie van sleutel woorden](#key-phrase-extraction)


## <a name="authenticate-the-client"></a>De client verifiëren

Maak een nieuw [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) -object met `credentials` en `endpoint` als een para meter.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]


## <a name="sentiment-analysis"></a>Sentimentanalyse

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

## <a name="language-detection"></a>Taaldetectie

Maak een lijst met woordenlijst objecten die uw documenten bevatten. Roep de [detectLanguage ()-](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) methode van de client aan en haal de geretourneerde [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult)op. Herhaal vervolgens de resultaten en druk de document-ID en taal af.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Voer uw code uit met `node index.js` in uw console venster.

### <a name="output"></a>Uitvoer

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Herkenning van entiteiten

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

## <a name="key-phrase-extraction"></a>Sleuteltermextractie

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

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `node` in uw Quick Start-bestand.

```console
node index.js
```

---
title: 'Snelstartgids: gegevens afwijkingen detecteren met behulp van de Anomaliey detector-client bibliotheek voor python'
titleSuffix: Azure Cognitive Services
description: Gebruik de anomalie detectie-API om afwijkingen in uw gegevens reeksen op te sporen als een batch of gegevens stromen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 09/17/2019
ms.author: aahi
ms.openlocfilehash: 1932ac571c94f9dc96240bdb63b44fe53c626f1f
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554727"
---
# <a name="quickstart-anomaly-detector-client-library-for-nodejs"></a>Snelstartgids: anomalie detectie client bibliotheek voor node. js

Ga aan de slag met de anomalie detector-client bibliotheek voor node. js. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen. Met de anomalie detectie service kunt u afwijkingen in uw tijdreeks gegevens opsporen door automatisch de beste modellen te gebruiken, ongeacht de branche, het scenario of het gegevens volume.

Gebruik de anomalie detectie-client bibliotheek voor node. js voor het volgende:

* Anomalieën in uw time series-gegevensset detecteren, als een batch-aanvraag
* De afwijkings status van het laatste gegevens punt in uw tijd reeks detecteren

[Naslag documentatie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/?view=azure-node-latest)  | -[bibliotheek bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector)  | [pakket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-anomalydetector)  | [code voorbeelden](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [node. js](https://nodejs.org/)

## <a name="setting-up"></a>Instellen

### <a name="create-an-anomaly-detector-azure-resource"></a>Een anomalie detectie Azure-resource maken

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor uw app en navigeer ernaar. 

```console
mkdir myapp && cd myapp
```

Voer de `npm init` opdracht uit om een knooppunt toepassing met een `package.json` bestand te maken. 

```console
npm init
```

Maak een bestand met de naam `index.js` en importeer de volgende bibliotheken:

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Maak variabelen van het Azure-eind punt en de-sleutel van uw resource. Als u de omgevings variabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw openen om toegang te krijgen tot de variabele. Maak een andere variabele voor het voorbeeld gegevensbestand dat u in een latere stap gaat downloaden en een lege lijst voor de gegevens punten. Maak vervolgens een `ApiKeyCredentials` object dat de sleutel bevat.

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Installeer de `ms-rest-azure`-en `azure-cognitiveservices-anomalydetector` NPM-pakketten. De CSV-parser-bibliotheek wordt ook gebruikt in deze Quick Start:

```console
npm install  @azure/cognitiveservices-anomalydetector ms-rest-azure csv-parse
```

Het `package.json`-bestand van uw app wordt bijgewerkt met de afhankelijkheden.

## <a name="object-model"></a>Object model

De anomalie detectie-client is een [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) -object dat met uw sleutel wordt geverifieerd bij Azure. De-client biedt twee methoden voor anomalie detectie: op een hele gegevensset met behulp van [entireDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--)en op het laatste gegevens punt met behulp van [LastDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-). 

Time Series-gegevens worden verzonden als reeks [punten](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) in een [aanvraag](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest) object. Het `Request`-object bevat eigenschappen die de gegevens beschrijven (bijvoorbeeld[granulatie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity) ) en para meters voor de anomalie detectie. 

De afwijkende detector respons is een [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) -of [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) -object, afhankelijk van de gebruikte methode. 

## <a name="code-examples"></a>Code voorbeelden 

Deze code fragmenten laten zien hoe u het volgende kunt doen met de afwijkende detector-client bibliotheek voor node. js:

* [De client verifiëren](#authenticate-the-client)
* [Een time series gegevensset laden vanuit een bestand](#load-time-series-data-from-a-file)
* [Afwijkingen in de volledige gegevensset detecteren](#detect-anomalies-in-the-entire-data-set) 
* [De afwijkings status van het laatste gegevens punt detecteren](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>De client verifiëren

Exemplaar een [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) -object met uw eind punt en referenties.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Time Series-gegevens uit een bestand laden

Down load de voorbeeld gegevens voor deze Quick Start van [github](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv):
1. Klik in uw browser met de rechter muisknop op **RAW**.
2. Klik op **Koppeling opslaan als**.
3. Sla het bestand op in de map van de toepassing, als een CSV-bestand.

Deze tijdreeks gegevens worden opgemaakt als een CSV-bestand en worden verzonden naar de anomalie detector-API.

Lees het gegevens bestand met de `readFileSync()` methode van de CSV-parseren bibliotheek en parser het bestand met `parse()`. Voor elke regel pusht u een [Point](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) -object met de tijds tempel en de numerieke waarde.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Afwijkingen in de volledige gegevensset detecteren 

Roep de API aan om afwijkingen te detecteren door de gehele tijd reeks als batch met de methode [entireDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) van de client. Sla het geretourneerde [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) -object op. Herhaal de `isAnomaly` lijst van de reactie en druk de index van alle `true` waarden af. Deze waarden komen overeen met de index van afwijkende gegevens punten, als deze zijn gevonden.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>De afwijkings status van het laatste gegevens punt detecteren

Roep de anomalie detectie-API aan om te bepalen of uw laatste gegevens punt afwijkt met behulp van de [lastDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) -methode van de client en sla het geretourneerde [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) -object op. De `isAnomaly` waarde van de reactie is een Boolean die de afwijkings status van het punt opgeeft.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `node` in uw Quick Start-bestand.

```console
node index.js
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Anomalie detectie met Azure Databricks streamen](../tutorials/anomaly-detection-streaming-databricks.md)

* Wat is de [anomalie detectie-API?](../overview.md)
* [Aanbevolen procedures](../concepts/anomaly-detection-best-practices.md) voor het gebruik van de anomalie detectie-API.
* De broncode voor dit voorbeeld is te vinden [op GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).

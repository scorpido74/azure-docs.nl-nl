---
title: 'Snelstart: gegevensafwijkingen detecteren met de Anomalie Detector-clientbibliotheek voor Python'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de Api voor anomaliedetector om afwijkingen in uw gegevensreeks op te sporen als batch of op streaminggegevens.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: c13657b8f2dae3868c1b9820236585aa930c6be4
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239084"
---
# <a name="quickstart-anomaly-detector-client-library-for-nodejs"></a>Snelstart: Anomalie Detector client bibliotheek voor Node.js

Ga aan de slag met de Anomaly Detector client bibliotheek voor Node.js. Volg deze stappen om het pakket te installeren en probeer de voorbeeldcode voor basistaken uit. Met de Anomaly Detector-service u afwijkingen in uw tijdreeksgegevens vinden door automatisch de best passende modellen te gebruiken, ongeacht de industrie, het scenario of het gegevensvolume.

Gebruik de clientbibliotheek Anomaly Detector voor Node.js om:

* Afwijkingen in uw tijdreeksgegevensset detecteren als batchaanvraag
* De anomaliestatus van het laatste gegevenspunt in uw tijdreeks detecteren

[Referentiedocumentatie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/?view=azure-node-latest) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-anomalydetector) | Zoek de code op[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/AnomalyDetector)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [Node.js](https://nodejs.org/)
* Een anomaliedetectorsleutel en eindpunt

## <a name="setting-up"></a>Instellen

### <a name="create-an-anomaly-detector-azure-resource"></a>Een Azure-bron voor anomaliedetectormaken

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor uw app en navigeer ernaar. 

```console
mkdir myapp && cd myapp
```

Voer `npm init` de opdracht uit om een `package.json` knooppunttoepassing met een bestand te maken. 

```console
npm init
```

Maak een `index.js` bestand met de naam en importeer de volgende bibliotheken:

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Maak variabelen het Azure-eindpunt en de sleutel van uw resource. Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell sluiten en opnieuw openen om toegang te krijgen tot de variabele. Maak een andere variabele voor het voorbeeldgegevensbestand dat u in een latere stap downloadt en een lege lijst voor de gegevenspunten. Maak vervolgens `ApiKeyCredentials` een object dat de sleutel bevat.

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer `ms-rest-azure` de `azure-cognitiveservices-anomalydetector` NPM-pakketten en NPM-pakketten. De csv-parse bibliotheek wordt ook gebruikt in deze quickstart:

```console
npm install  @azure/cognitiveservices-anomalydetector @azure/ms-rest-js csv-parse
```

Het bestand `package.json` van uw app wordt bijgewerkt met de afhankelijkheden.

## <a name="object-model"></a>Objectmodel

De anomaliedetectorclient is een [Object AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) dat met uw sleutel naar Azure wordt geverifieerd. De client biedt twee methoden voor anomaliedetectie: op een hele gegevensset met behulp van [volledigeDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--), en op het laatste gegevenspunt met behulp van [LastDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-). 

Tijdreeksgegevens worden verzonden als reeks [punten](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) in een object [Request.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest) Het `Request` object bevat eigenschappen om de gegevens[(granulariteit](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity) bijvoorbeeld) en parameters voor de anomaliedetectie te beschrijven. 

De anomaliedetectorrespons is een [object LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) of [EntireDetectResponse,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) afhankelijk van de gebruikte methode. 

## <a name="code-examples"></a>Codevoorbeelden 

Deze codefragmenten laten zien hoe u het volgende doen met de clientbibliotheek Anomaliedetector voor Node.js:

* [De client verifiëren](#authenticate-the-client)
* [Een tijdreeksgegevensset uit een bestand laden](#load-time-series-data-from-a-file)
* [Afwijkingen in de gehele gegevensset detecteren](#detect-anomalies-in-the-entire-data-set) 
* [De anomaliestatus van het laatste gegevenspunt detecteren](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>De client verifiëren

Instantiate een [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) object met uw eindpunt en referenties.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Gegevens van de tijdreeksen uit een bestand laden

Download de voorbeeldgegevens voor deze quickstart van [GitHub:](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv)
1. Klik in uw browser met de rechtermuisknop op **Raw**.
2. Klik **op Koppeling opslaan als**.
3. Sla het bestand op in uw toepassingsmap als csv-bestand.

Deze tijdreeksgegevens worden opgemaakt als een CSV-bestand en worden naar de API voor anomaliedetector en verzonden.

Lees uw gegevensbestand met de methode van `readFileSync()` de csv-parse-bibliotheek en ontleden het bestand met `parse()`. Voor elke regel drukt u op een object [Punt](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) met de tijdstempel en de numerieke waarde.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Afwijkingen in de gehele gegevensset detecteren 

Roep de API aan om afwijkingen in de gehele tijdreeks te detecteren als een batch met de [volledige detecteermethode van](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) de client. Sla het geretourneerde [object EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) op. Door de `isAnomaly` lijst van het antwoord te herhalen `true` en de index van eventuele waarden af te drukken. Deze waarden komen overeen met de index van afwijkende gegevenspunten, indien deze zijn gevonden.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>De anomaliestatus van het laatste gegevenspunt detecteren

Roep de Api voor anomaliedetector aan om te bepalen of uw laatste gegevenspunt een afwijking is met behulp van de [laatste Detect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) methode van de client en sla het geretourneerde [object LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) op. De waarde `isAnomaly` van het antwoord is een booleaan die de anomaliestatus van dat punt aangeeft.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing `node` uit met de opdracht voor uw quickstartbestand.

```console
node index.js
```

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]

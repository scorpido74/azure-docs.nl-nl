---
title: Quickstart voor Anomaly Detector-clientbibliotheek voor Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/30/2020
ms.author: aahi
ms.openlocfilehash: 2fd4faf9fa553f8fb7b6553c0d1435bf003b5578
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/06/2020
ms.locfileid: "85986102"
---
Ga aan de slag met de Anomaly Detector-clientbibliotheek voor Python. Volg deze stappen om het pakket te installeren en de voorbeeldcode voor basistaken uit te proberen. Met de Anomaly Detector-service kunt u afwijkingen in uw tijdreeksgegevens vinden door hierop automatisch de best passende modellen uit te voeren, ongeacht de branche, het scenario of het gegevensvolume.

Gebruik de Anomaly Detector-clientbibliotheek voor Python om:

* Afwijkingen in uw tijdreeksgegevensset als een batchaanvraag te detecteren
* De anomaliestatus van het laatste gegevenspunt in uw tijdreeks te detecteren

[Referentiedocumentatie voor bibliotheek](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [Pakket (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [De voorbeeldcode zoeken op GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>Vereisten

* [Python 3.x](https://www.python.org/)
* De [bibliotheek voor Pandas-gegevensanalyse](https://pandas.pydata.org/)
* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/)
* Zodra u een Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector-resource maken"  target="_blank">maakt u een Anomaly Detector-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt om de toepassing te verbinden met de Anomaly Detector-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen en later upgraden naar een betaalde laag voor productie.

 
## <a name="setting-up"></a>Instellen

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

 Maak een nieuw Python-bestand en importeer de volgende bibliotheken.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Maak variabelen voor uw sleutel als een omgevingsvariabele, het pad naar een tijdreeksgegevensbestand en de Azure-locatie van uw abonnement. Bijvoorbeeld `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Na de installatie van Python kunt u de clientbibliotheek installeren met:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Objectmodel

De Anomaly Detector-client is een [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python)-object dat met behulp van uw sleutel wordt geverifieerd bij Azure. De client biedt twee methoden voor anomaliedetectie: op een hele gegevensset met behulp van [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) en op het laatste gegevenspunt met behulp van [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Tijdreeksgegevens worden verzonden als een reeks [punten](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) in een [aanvraagobject](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python). Het `Request`-object bevat eigenschappen die de gegevens beschrijven ([granulariteit](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) bijvoorbeeld) en parameters voor de anomaliedetectie. 

Het antwoord van Anomaly Detector is een [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python)- of [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python)-object afhankelijk van de gebruikte methode. 

## <a name="code-examples"></a>Codevoorbeelden 

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Anomaly Detector-clientbibliotheek voor Python:

* [De client verifiëren](#authenticate-the-client)
* [Een tijdreeksgegevensset laden vanuit een bestand](#load-time-series-data-from-a-file)
* [Anomalieën in de volledige gegevensset detecteren](#detect-anomalies-in-the-entire-data-set) 
* [De anomaliestatus van het laatste gegevenspunt detecteren](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>De client verifiëren

Voeg uw Azure-locatievariabele toe aan het eindpunt en verifieer de client met uw sleutel.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Tijdreeksgegevens uit een bestand laden

Download de voorbeeldgegevens voor deze quickstart van [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. Klik in uw browser met de rechtermuisknop op **Onbewerkt**.
2. Klik op **Koppeling opslaan als**.
3. Sla het bestand op in de map van de toepassing als CSV-bestand.

Deze tijdreeksgegevens worden opgemaakt als CSV-bestand en worden verzonden naar de Anomaly Detector-API.

Laad uw gegevensbestand met de `read_csv()`-methode van de Pandas-bibliotheek en maak een lege lijstvariabele om uw gegevensreeksen op te slaan. Herhaal het bestand en voeg de gegevens toe als [punt](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python)-object. Dit object bevat de tijdstempel en de numerieke waarde van de rijen van uw CSV-gegevensbestand. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Maak een [aanvraag](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python)object met uw tijdreeks en de [granulariteit](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (of periodiciteit) van de gegevenspunten. Bijvoorbeeld `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Anomalieën in de volledige gegevensset detecteren 

Roep de API aan om anomalieën te detecteren via de volledige tijdreeksgegevens met behulp van de [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)-methode van de client. Sla het geretourneerde [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python)-object op. Herhaal via de `is_anomaly`-lijst van het antwoord en druk de index van alle `true`-waarden af. Deze waarden komen overeen met de index van afwijkende gegevenspunten, als die zijn gevonden.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>De anomaliestatus van het laatste gegevenspunt detecteren

Roep de Anomaly Detector-API aan om te bepalen of uw laatste gegevenspunt een anomalie is met behulp van de [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-)-methode van de client en sla het geretourneerde [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python)-object op. De `is_anomaly`-waarde van het antwoord is een booleaanse waarde die de afwijkingsstatus van het punt opgeeft.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `python` en uw bestandsnaam.
 
[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]

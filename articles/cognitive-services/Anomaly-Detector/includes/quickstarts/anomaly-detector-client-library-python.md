---
title: Anomalie Detector Python client bibliotheek snel aan de slag
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2020
ms.author: aahi
ms.openlocfilehash: a51025245e5b02b89126afd886bc89fb1cefdef4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759991"
---
Ga aan de slag met de Anomaly Detector-clientbibliotheek voor Python. Volg deze stappen om het pakket te installeren en probeer de voorbeeldcode voor basistaken uit. Met de Anomaly Detector-service u afwijkingen in uw tijdreeksgegevens vinden door automatisch de best passende modellen te gebruiken, ongeacht de industrie, het scenario of het gegevensvolume.

Gebruik de clientbibliotheek Anomaly Detector voor Python om:

* Afwijkingen in uw tijdreeksgegevensset detecteren als batchaanvraag
* De anomaliestatus van het laatste gegevenspunt in uw tijdreeks detecteren

[Bibliotheekreferentiedocumentatie](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [(PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | Zoek de[voorbeeldcode op GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* Een anomaliedetectorsleutel en eindpunt
* [Python 3.x](https://www.python.org/)
* De [Panda's data-analyse bibliotheek](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Instellen

### <a name="create-an-anomaly-detector-resource"></a>Een Anomaliedetectorbron maken

[!INCLUDE [anomaly-detector-resource-creation](../../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-python-application"></a>Een nieuwe python-toepassing maken

 Maak een nieuw Python-bestand en importeer de volgende bibliotheken.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Maak variabelen voor uw sleutel als omgevingsvariabele, het pad naar een tijdreeksgegevensbestand en de azure-locatie van uw abonnement. Bijvoorbeeld `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Na het installeren van Python u de clientbibliotheek installeren met:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Objectmodel

De anomaliedetectorclient is een [Object AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) dat met uw sleutel naar Azure wordt geverifieerd. De client biedt twee methoden voor anomaliedetectie: op een hele gegevensset met behulp van [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)en op het laatste gegevenspunt met behulp van [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Tijdreeksgegevens worden verzonden als een reeks [punten](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) in een object [Request.](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) Het `Request` object bevat eigenschappen om de gegevens[(granulariteit](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) bijvoorbeeld) en parameters voor de anomaliedetectie te beschrijven. 

De anomaliedetectorrespons is een [object LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) of [EntireDetectResponse,](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) afhankelijk van de gebruikte methode. 

## <a name="code-examples"></a>Codevoorbeelden 

Deze codefragmenten laten zien hoe u het volgende doen met de clientbibliotheek Anomaly Detector voor Python:

* [De client verifiëren](#authenticate-the-client)
* [Een tijdreeksgegevensset uit een bestand laden](#load-time-series-data-from-a-file)
* [Afwijkingen in de gehele gegevensset detecteren](#detect-anomalies-in-the-entire-data-set) 
* [De anomaliestatus van het laatste gegevenspunt detecteren](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>De client verifiëren

Voeg de azure-locatievariabele toe aan het eindpunt en verifieer de client met uw sleutel.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Gegevens van de tijdreeksen uit een bestand laden

Download de voorbeeldgegevens voor deze quickstart van [GitHub:](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv)
1. Klik in uw browser met de rechtermuisknop op **Raw**.
2. Klik **op Koppeling opslaan als**.
3. Sla het bestand op in uw toepassingsmap als csv-bestand.

Deze tijdreeksgegevens worden opgemaakt als een CSV-bestand en worden naar de API voor anomaliedetector en verzonden.

Laad uw gegevensbestand met de `read_csv()` methode van de Pandas-bibliotheek en maak een lege lijstvariabele om uw gegevensreeks op te slaan. Laat het bestand doorlopen en voeg de gegevens toe als object [Point.](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) Dit object bevat de tijdstempel en numerieke waarde van de rijen van het CSV-gegevensbestand. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Maak een [object Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) met uw tijdreeks en de [granulariteit](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (of periodiciteit) van de gegevenspunten. Bijvoorbeeld `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Afwijkingen in de gehele gegevensset detecteren 

Roep de API aan om afwijkingen te detecteren in de hele tijdreeksgegevens met behulp van de [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) methode van de client. Sla het geretourneerde [object EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) op. Door de `is_anomaly` lijst van het antwoord te herhalen `true` en de index van eventuele waarden af te drukken. Deze waarden komen overeen met de index van afwijkende gegevenspunten, indien deze zijn gevonden.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>De anomaliestatus van het laatste gegevenspunt detecteren

Roep de Api voor anomaliedetector aan om te bepalen of uw laatste gegevenspunt een afwijking is met de [methode last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) van de client en sla het geretourneerde [object LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) op. De waarde `is_anomaly` van het antwoord is een booleaan die de anomaliestatus van dat punt aangeeft.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing `python` uit met de opdracht en uw bestandsnaam.
 
[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]

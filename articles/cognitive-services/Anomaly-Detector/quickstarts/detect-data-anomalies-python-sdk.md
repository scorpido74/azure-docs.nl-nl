---
title: 'Quickstart: Gegevens afwijkingen detecteren met behulp van de anomalie detectie-client bibliotheek voor python'
titleSuffix: Azure Cognitive Services
description: Gebruik de anomalie detectie-API om afwijkingen in uw gegevens reeksen op te sporen als een batch of gegevens stromen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 08/01/2019
ms.author: aahi
ms.openlocfilehash: 28dbcde723e25577545cfd44fed9ef00249d8f30
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694910"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>Quickstart: Anomalie detectie client bibliotheek voor python

Ga aan de slag met de anomalie detectie-client bibliotheek voor python. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen. Met de anomalie detectie service kunt u afwijkingen in uw tijdreeks gegevens opsporen door automatisch de beste modellen te gebruiken, ongeacht de branche, het scenario of het gegevens volume.

Gebruik de anomalie detectie-client bibliotheek voor python voor het volgende:

* Anomalieën in uw time series-gegevensset detecteren, als een batch-aanvraag
* De afwijkings status van het laatste gegevens punt in uw tijd reeks detecteren

[Bibliotheek referentie documentatie](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [package (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | -voor[beelden](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* De [bibliotheek voor gegevens analyse van Pandas](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Instellen

### <a name="create-an-anomaly-detector-resource"></a>Een afwijkende detector-resource maken

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

Wanneer u een sleutel van uw proef abonnement of resource hebt ontvangen, [maakt u een omgevings variabele](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) voor de `ANOMALY_DETECTOR_KEY`sleutel met de naam.

### <a name="create-a-new-python-application"></a>Een nieuwe python-toepassing maken

 Maak een nieuwe python-toepassing in uw voorkeurs editor of IDE. Importeer vervolgens de volgende bibliotheken.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Maak variabelen voor uw sleutel als een omgevings variabele, het pad naar een time series-gegevens bestand en de Azure-locatie van uw abonnement. Bijvoorbeeld `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Na de installatie van python kunt u de client bibliotheek installeren met:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Object model

De anomalie detectie-client is een [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) -object dat met uw sleutel wordt geverifieerd bij Azure. De-client biedt twee methoden voor anomalie detectie: Op een hele gegevensset met behulp van [entire_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)en op het laatste gegevens punt met behulp van [Last_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Tijdreeks gegevens worden verzonden als een reeks [punten](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) in een [aanvraag](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) object. Het `Request` object bevat eigenschappen die de gegevens beschrijven (bijvoorbeeld[granulatie](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) ) en para meters voor de anomalie detectie. 

De afwijkende detector respons is een [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) -of [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) -object, afhankelijk van de gebruikte methode. 

## <a name="code-examples"></a>Code voorbeelden 

Deze code fragmenten laten zien hoe u het volgende kunt doen met de anomalie detectie-client bibliotheek voor python:

* [De client verifiëren](#authenticate-the-client)
* [Een time series gegevensset laden vanuit een bestand](#load-time-series-data-from-a-file)
* [Afwijkingen in de volledige gegevensset detecteren](#detect-anomalies-in-the-entire-data-set) 
* [De afwijkings status van het laatste gegevens punt detecteren](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>De client verifiëren

Voeg uw Azure-locatie variabele toe aan het eind punt en verificatie van de client met uw sleutel.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Time Series-gegevens uit een bestand laden

Down load de voorbeeld gegevens voor deze Quick Start van [github](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. Klik in uw browser met de rechter muisknop op **RAW**.
2. Klik op **Koppeling opslaan als**.
3. Sla het bestand op in de map van de toepassing, als een CSV-bestand.

Deze tijdreeks gegevens worden opgemaakt als een CSV-bestand en worden verzonden naar de anomalie detector-API.

Laad uw gegevens bestand met de methode `read_csv()` van de Pandas-bibliotheek en maak een lege lijst variabele om uw gegevens reeksen op te slaan. Herhaal het bestand en voeg de gegevens toe als een [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) -object. Dit object bevat de tijds tempel en de numerieke waarde van de rijen van uw CSV-gegevens bestand. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Maak een [aanvraag](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) object met uw tijd reeks en de [granulariteit](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (of periodiciteit) van de gegevens punten. Bijvoorbeeld `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Afwijkingen in de volledige gegevensset detecteren 

Roep de API aan om afwijkingen te detecteren via de volledige tijdreeks gegevens met behulp van de [entire_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) -methode van de client. Sla het geretourneerde [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) -object op. De `is_anomaly` lijst van het antwoord herhalen en de index van alle `true` waarden afdrukken. Deze waarden komen overeen met de index van afwijkende gegevens punten, als deze zijn gevonden.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>De afwijkings status van het laatste gegevens punt detecteren

Roep de anomalie detectie-API aan om te bepalen of uw laatste gegevens punt afwijkt met behulp van de [last_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) -methode van de client en sla het geretourneerde [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) -object op. De waarde van `is_anomaly` het antwoord is een Boolean die de afwijkings status van het punt opgeeft.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit in uw IDE of op de opdracht regel met de `python`-opdracht en de naam van het bestand.
 
## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle andere resources die aan de resource groep zijn gekoppeld, verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Anomalie detectie met Azure Databricks streamen](../tutorials/anomaly-detection-streaming-databricks.md)

* Wat is de [anomalie detectie-API?](../overview.md)
* [Aanbevolen procedures](../concepts/anomaly-detection-best-practices.md) voor het gebruik van de anomalie detectie-API.
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).

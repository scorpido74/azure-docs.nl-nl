---
title: 'Snelstart: anomalieën detecteren als batch met behulp van de Anomaly Detector REST API en Python'
titleSuffix: Azure Cognitive Services
description: Gebruik de Api anomaly detector om afwijkingen in uw gegevensreeks op te sporen als batch of op streaminggegevens in deze quickstart.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 684aba561dc50b64dd7cc564cff8e55229ce1429
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239034"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Snelstart: afwijkingen in uw tijdreeksgegevens detecteren met behulp van de Anomaly Detector REST API en Python

Gebruik deze snelle start om de twee detectiemodi van de Anomaly Detector API te gebruiken om afwijkingen in uw tijdreeksgegevens te detecteren. Deze Python-toepassing verzendt twee API-aanvragen met JSON-geformatteerde tijdreeksgegevens en krijgt de antwoorden.

| API-aanvraag                                        | Toepassingsuitvoer                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Afwijkingen detecteren als een batch                        | De JSON-respons met de anomaliestatus (en andere gegevens) voor elk gegevenspunt in de gegevens van de tijdreeks en de posities van gedetecteerde afwijkingen. |
| De anomaliestatus van het laatste gegevenspunt detecteren | De JSON-respons met de anomaliestatus (en andere gegevens) voor het meest recente gegevenspunt in de gegevens van de tijdreeksen.                                                                                                                                         |

 Hoewel deze toepassing in Python is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal. U vindt de broncode voor deze quickstart op [GitHub.](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/python-detect-anomalies.py)

## <a name="prerequisites"></a>Vereisten

- [Python 2.x of 3.x](https://www.python.org/downloads/)
- Een anomaliedetectorsleutel en eindpunt
- De [bibliotheek Aanvragen](https://pypi.org/project/requests/) voor python

- Een JSON-bestand met tijdreeksgegevenspunten. De voorbeeldgegevens voor deze quickstart zijn te vinden op [GitHub.](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)

### <a name="create-an-anomaly-detector-resource"></a>Een Anomaliedetectorbron maken

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]


## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

1. Maak een nieuw python-bestand en voeg de volgende importtoe.

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. Maak variabelen voor uw abonnementssleutel en uw eindpunt. Hieronder vindt u de URI's die u gebruiken voor anomaliedetectie. Deze worden later toegevoegd aan het eindpunt van uw service om url's voor API-aanvragen te maken.

    |Detectiemethode  |URI  |
    |---------|---------|
    |Batchdetectie    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Detectie op het laatste gegevenspunt     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. Lees in het JSON-gegevensbestand door `json.load()`het te openen en te gebruiken .

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>Een functie maken om aanvragen te verzenden

1. Maak een nieuwe `send_request()` functie genaamd die de hierboven gemaakte variabelen neemt. Voer vervolgens de volgende stappen uit.

2. Maak een woordenboek voor de aanvraagkoppen. Stel `Content-Type` de `application/json`inslag en voeg `Ocp-Apim-Subscription-Key` uw abonnementssleutel toe aan de koptekst.

3. Stuur de `requests.post()`aanvraag via . Combineer de URL voor eindpunt- en anomaliedetectie voor de volledige aanvraag-URL en voeg uw kopteksten en json-aanvraaggegevens toe. En dan het antwoord teruggeven.

    [!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Afwijkingen detecteren als een batch

1. Maak een `detect_batch()` methode die wordt aangeroepen om afwijkingen in de gegevens als batch te detecteren. Roep `send_request()` de hierboven gemaakte methode aan met uw eindpunt, url, abonnementssleutel en json-gegevens.

2. Roep `json.dumps()` het resultaat op om het op te maken en af te drukken op de console.

3. Als het `code` antwoord veld bevat, drukt u de foutcode en het foutbericht af.

4. Zoek anders de posities van afwijkingen in de gegevensset. Het veld `isAnomaly` van het antwoord bevat een booleaanse waarde met betrekking tot de vraag of een bepaald gegevenspunt een afwijking is. Door de lijst te herhalen en de `True` index van eventuele waarden af te drukken. Deze waarden komen overeen met de index van afwijkende gegevenspunten, indien deze zijn gevonden.

    [!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>De anomaliestatus van het laatste gegevenspunt detecteren

1. Maak een `detect_latest()` methode die wordt aangeroepen om te bepalen of het laatste gegevenspunt in uw tijdreeks een anomalie is. Roep `send_request()` de bovenstaande methode aan met uw eindpunt, url, abonnementssleutel en json-gegevens. 

2. Roep `json.dumps()` het resultaat op om het op te maken en af te drukken op de console.

    [!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="send-the-request"></a>De aanvraag verzenden

Roep de hierboven gemaakte anomaliedetectiemethoden aan.

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>Voorbeeld van een antwoord

Een geslaagdantwoord wordt geretourneerd in JSON-indeling. Klik op de onderstaande links om het JSON-antwoord op GitHub te bekijken:
* [Voorbeeld batchdetectierespons](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Voorbeeld van de laatste puntdetectierespons](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]

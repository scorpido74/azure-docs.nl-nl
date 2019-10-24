---
title: 'Snelstartgids: anomalieën als een batch detecteren met behulp van de anomalie detectie REST API en python'
titleSuffix: Azure Cognitive Services
description: Gebruik de anomalie detectie-API om afwijkingen in uw gegevens reeksen op te sporen als een batch of gegevens stromen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: aahi
ms.openlocfilehash: 571626da0f3f43c8c2a2e33e1147418158c5473b
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754222"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Snelstartgids: afwijkingen in uw tijdreeks gegevens detecteren met behulp van de anomalie detectie REST API en python

Gebruik deze Quick Start om de twee detectie modi van de anomalie detectie-API te gebruiken voor het detecteren van afwijkingen in uw time series-gegevens. Deze python-toepassing verzendt twee API-aanvragen met tijdreeks gegevens in JSON-indeling en ontvangt de antwoorden.

| API-aanvraag                                        | Toepassingsuitvoer                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Afwijkingen als een batch detecteren                        | Het JSON-antwoord met de afwijkings status (en andere gegevens) voor elk gegevens punt in de tijdreeks gegevens en de posities van gedetecteerde afwijkingen. |
| De afwijkings status van het laatste gegevens punt detecteren | Het JSON-antwoord met de afwijkings status (en andere gegevens) voor het laatste gegevens punt in de time series-gegevens.                                                                                                                                         |

 Hoewel deze toepassing in Python is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

## <a name="prerequisites"></a>Vereisten

- [Python 2.x of 3.x](https://www.python.org/downloads/)

- De [bibliotheek aanvragen](https://pypi.org/project/requests/) voor python

- Een JSON-bestand met gegevens punten van de tijd reeks. De voorbeeld gegevens voor deze Quick Start vindt u op [github](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Een afwijkende detector-resource maken

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]


## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

1. Maak een nieuw python-bestand en voeg de volgende import bewerkingen toe.

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. Maak variabelen voor uw abonnements sleutel en uw eind punt. Hieronder vindt u de Uri's die u voor anomalie detectie kunt gebruiken. Deze worden later toegevoegd aan uw service-eind punt om de API-aanvraag-Url's te maken.

    |Detectie methode  |URI  |
    |---------|---------|
    |Batch detectie    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Detectie op het laatste gegevens punt     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. Lees in het JSON-gegevens bestand door het te openen en `json.load()` te gebruiken.

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>Een functie maken om aanvragen te verzenden

1. Maak een nieuwe functie met de naam `send_request()` die de hierboven gemaakte variabelen gebruikt. Voer vervolgens de volgende stappen uit.

2. Maak een woorden lijst voor de aanvraag headers. Stel de `Content-Type` in op `application/json` en voeg uw abonnements sleutel toe aan de `Ocp-Apim-Subscription-Key`-kop.

3. Verzend de aanvraag via `requests.post()`. Combi neer uw eind punt en de detectie-URL voor afwijkingen voor de volledige aanvraag-URL en voeg uw kopteksten en JSON-aanvraag gegevens toe. En retourneert vervolgens het antwoord.

[!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Afwijkingen als een batch detecteren

1. Maak een methode met de naam `detect_batch()` om afwijkingen in de gegevens op te sporen als een batch. Roep de hierboven gemaakte `send_request()` methode aan met uw eind punt, URL, abonnements sleutel en JSON-gegevens.

2. Bel `json.dumps()` op het resultaat om deze te Format teren en af te drukken naar de-console.

3. Als het antwoord `code` veld bevat, drukt u de fout code en het fout bericht af.

4. Als dat niet het geval is, kunt u de positie van afwijkingen vinden in de gegevensset. Het veld `isAnomaly` van de reactie bevat een Booleaanse waarde die aangeeft of een bepaald gegevens punt een afwijkend is. De lijst door lopen en de index van `True` waarden afdrukken. Deze waarden komen overeen met de index van afwijkende gegevens punten, als deze zijn gevonden.

[!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>De afwijkings status van het laatste gegevens punt detecteren

1. Maak een methode met de naam `detect_latest()` om te bepalen of het meest recente gegevens punt in de tijd reeks een afwijkend is. Roep de bovenstaande `send_request()` methode aan met uw eind punt, URL, abonnements sleutel en JSON-gegevens. 

2. Bel `json.dumps()` op het resultaat om deze te Format teren en af te drukken naar de-console.

[!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="send-the-request"></a>De aanvraag verzenden

1. Roep de hierboven gemaakte anomalie detectie methoden aan.

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>Voorbeeld van een antwoord

Een geslaagde reactie wordt geretourneerd in JSON-indeling. Klik op de onderstaande koppelingen om het JSON-antwoord op GitHub weer te geven:
* [Voor beeld van een antwoord op een batch detectie](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Voor beeld van laatste punt detectie respons](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Anomalie detectie met Azure Databricks streamen](../tutorials/anomaly-detection-streaming-databricks.md)

* Wat is de [anomalie detectie-API?](../overview.md)
* [Aanbevolen procedures](../concepts/anomaly-detection-best-practices.md) voor het gebruik van de anomalie detectie-API.
* De broncode voor dit voorbeeld is te vinden [op GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).

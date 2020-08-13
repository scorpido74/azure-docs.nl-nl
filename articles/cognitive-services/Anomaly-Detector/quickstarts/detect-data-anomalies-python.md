---
title: 'Quickstart: Batchgewijs anomalieën detecteren met behulp van de Anomaly Detector-REST API en Python'
titleSuffix: Azure Cognitive Services
description: Gebruik in deze quickstart de Anomaly Detector API om afwijkingen in uw gegevensreeksen als batch of op streaminggegevens te detecteren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 06/30/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: daee5a921e25dc74d6e869ca3feb6c66d8c4ce7d
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851028"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Quickstart: Anomalieën in uw tijdreeksgegevens detecteren met behulp van de Anomaly Detector REST API en Python

Gebruik deze quickstart om aan de slag te gaan met het gebruiken van de twee detectiemodi van de Anomaly Detector API's om anomalieën in uw tijdreeksgegevens te detecteren. Met deze Python-toepassing worden twee API-aanvragen met tijdreeksgegevens in JSON-indeling verzonden en worden de reacties opgehaald.

| API-aanvraag                                        | Toepassingsuitvoer                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Batchgewijs anomalieën detecteren                        | Het JSON-antwoord met de anomaliestatus (en andere gegevens) voor elk gegevenspunt in de tijdreeksgegevens en de posities van gedetecteerde anomalieën. |
| De anomaliestatus van het laatste gegevenspunt detecteren | Het JSON-antwoord met de anomaliestatus (en andere gegevens) voor het laatste gegevenspunt in de tijdreeksgegevens.                                                                                                                                         |

 Hoewel deze toepassing in Python is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal. U kunt de broncode voor deze quickstart vinden op [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/python-detect-anomalies.py).

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/)
- Zodra u een Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector-resource maken"  target="_blank">, maakt u een Anomaly Detector-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    - U hebt de sleutel en het eindpunt nodig van de resource die u maakt om de toepassing te verbinden met de Anomaly Detector-API. Verderop in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
- [Python 2.x of 3.x](https://www.python.org/downloads/)
- De [Aanvragenbibliotheek](https://pypi.org/project/requests/) voor Python

- Een JSON-bestand dat tijdreeksgegevenspunten bevat. De voorbeeldgegevens voor deze quickstart zijn te vinden op [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

1. Maak een nieuw Python-bestand en voeg de volgende imports toe.

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. Maak variabelen voor uw abonnementssleutel en eindpunt. Hieronder vindt u de URI's die u voor anomaliedetectie kunt gebruiken. Deze worden later toegevoegd aan uw service-eindpunt om de API-aanvraag-URL's te maken.

    |Detectiemethode  |URI  |
    |---------|---------|
    |Batchdetectie    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Detectie op het laatste gegevenspunt     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. Lees het JSON-gegevensbestand in door het te openen en `json.load()` te gebruiken.

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>Een functie maken voor het verzenden van aanvragen

1. Maak een nieuwe functie met de naam `send_request()` die de hierboven gemaakte variabelen gebruikt. Voer vervolgens de volgende stappen uit.

2. Maak een woordenlijst voor de aanvraagheaders. Stel de `Content-Type` in op `application/json`, en voeg uw abonnementssleutel toe aan de `Ocp-Apim-Subscription-Key`-header.

3. Verzend de aanvraag met behulp van `requests.post()`. Combineer uw eindpunt en de anomaliedetectie-URL voor de volledige aanvraag-URL en voeg uw headers en JSON-aanvraaggegevens toe. En retourneer vervolgens het antwoord.

    [!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Batchgewijs anomalieën detecteren

1. Maak een methode met de naam `detect_batch()` om anomalieën in de gegevens batchgewijs te detecteren. Roep de hierboven gemaakte `send_request()`-methode aan met uw eindpunt, URL, abonnementssleutel en JSON-gegevens.

2. Roep `json.dumps()` aan op het resultaat om dit te formatteren en af te drukken naar de console.

3. Als het antwoord het veld `code` bevat, drukt u de foutcode en het foutbericht af.

4. Als dat niet het geval is, zoekt u de positie van anomalieën in de gegevensset. Het veld `isAnomaly` van de reactie bevat een booleaanse waarde die aangeeft of een bepaald gegevenspunt een anomalie is. Loop de lijst door en druk de index van alle `True`-waarden af. Deze waarden komen overeen met de index van afwijkende gegevenspunten, als die zijn gevonden.

    [!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>De anomaliestatus van het laatste gegevenspunt detecteren

1. Maak een methode met de naam `detect_latest()` om te bepalen of het meest recente gegevenspunt in de tijdreeks een anomalie is. Roep de bovenstaande `send_request()`-methode aan met uw eindpunt, URL, abonnementssleutel en JSON-gegevens. 

2. Roep `json.dumps()` aan op het resultaat om dit te formatteren en af te drukken naar de console.

    [!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="send-the-request"></a>De aanvraag verzenden

Roep de hierboven gemaakte anomaliedetectiemethoden aan.

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>Voorbeeld van een antwoord

Er wordt een geslaagd antwoord geretourneerd in JSON-indeling. Klik op de onderstaande koppelingen om het JSON-antwoord op GitHub weer te geven:
* [Voorbeeld van een antwoord op een batchdetectie](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Voorbeeld van een antwoord op de detectie van het laatste punt](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]

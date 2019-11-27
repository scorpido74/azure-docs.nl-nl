---
title: 'Snelstartgids: afwijkingen in uw tijdreeks gegevens detecteren met behulp van de afwijkings detector REST API en Java'
titleSuffix: Azure Cognitive Services
description: Gebruik de anomalie detectie-API om afwijkingen in uw gegevens reeksen op te sporen als een batch of gegevens stromen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: 6b79470194c5e8dc9b8d51f8b528cb4e51f7daf2
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483040"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Snelstartgids: afwijkingen in uw tijdreeks gegevens detecteren met behulp van de afwijkings detector REST API en Java

Gebruik deze Quick Start om de twee detectie modi van de anomalie detectie-API te gebruiken voor het detecteren van afwijkingen in uw time series-gegevens. Deze Java-toepassing verzendt twee API-aanvragen met tijdreeks gegevens in JSON-indeling en ontvangt de antwoorden.

| API-aanvraag                                        | Toepassingsuitvoer                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Afwijkingen als een batch detecteren                        | Het JSON-antwoord met de afwijkings status (en andere gegevens) voor elk gegevens punt in de tijdreeks gegevens en de posities van gedetecteerde afwijkingen. |
| De afwijkings status van het laatste gegevens punt detecteren | Het JSON-antwoord met de afwijkings status (en andere gegevens) voor het laatste gegevens punt in de time series-gegevens.                                                                                                                                         |

 Hoewel deze toepassing in Java is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal. U vindt de bron code voor deze Quick Start op [github](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java).

## <a name="prerequisites"></a>Vereisten

- De [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) of hoger.
- Een anomalie detectie sleutel en eind punt
- Deze bibliotheken importeren vanuit de Maven-opslag plaats
    - [Json in Java](https://mvnrepository.com/artifact/org.json/json) -pakket
    - [Apache httpclient maakt](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) -pakket

- Een JSON-bestand met gegevens punten van de tijd reeks. De voorbeeld gegevens voor deze Quick Start vindt u op [github](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Een afwijkende detector-resource maken

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

1. Maak een nieuw Java-project en importeer de volgende bibliotheken.
    
    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Maak variabelen voor uw abonnements sleutel en uw eind punt. Hieronder vindt u de Uri's die u voor anomalie detectie kunt gebruiken. Deze worden later toegevoegd aan uw service-eind punt om de API-aanvraag-Url's te maken.

    |Detectie methode  |URI  |
    |---------|---------|
    |Batch detectie    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Detectie op het laatste gegevens punt     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Een functie maken om aanvragen te verzenden

1. Maak een nieuwe functie met de naam `sendRequest()` die de hierboven gemaakte variabelen gebruikt. Voer vervolgens de volgende stappen uit.

2. Maak een `CloseableHttpClient`-object waarmee aanvragen kunnen worden verzonden naar de API. De aanvraag verzenden naar een `HttpPost`-aanvraag object door uw eind punt te combi neren en een anomalie detectie-URL.

3. Gebruik de functie `setHeader()` van de aanvraag om de `Content-Type`-header in te stellen op `application/json`en voeg uw abonnements sleutel toe aan de `Ocp-Apim-Subscription-Key`-header.

4. Gebruik de `setEntity()` functie van de aanvraag voor de gegevens die moeten worden verzonden.

5. Gebruik de `execute()` functie van de client om de aanvraag te verzenden en op te slaan in een `CloseableHttpResponse`-object.

6. Maak een `HttpEntity`-object om de antwoord inhoud op te slaan. Haal de inhoud op met `getEntity()`. Als het antwoord niet leeg is, retourneert u het.

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Afwijkingen als een batch detecteren

1. Maak een methode met de naam `detectAnomaliesBatch()` om afwijkingen in de gegevens op te sporen als een batch. Roep de hierboven gemaakte `sendRequest()` methode aan met uw eind punt, URL, abonnements sleutel en JSON-gegevens. Bekijk het resultaat en druk het af op de-console.

2. Als het antwoord `code` veld bevat, drukt u de fout code en het fout bericht af.

3. Als dat niet het geval is, kunt u de positie van afwijkingen vinden in de gegevensset. Het veld `isAnomaly` van de reactie bevat een Booleaanse waarde die aangeeft of een bepaald gegevens punt een afwijkend is. Haal de JSON-matrix op en herhaal deze door de index van `true` waarden af te drukken. Deze waarden komen overeen met de index van afwijkende gegevens punten, als deze zijn gevonden.

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>De afwijkings status van het laatste gegevens punt detecteren

Maak een methode met de naam `detectAnomaliesLatest()` om de afwijkings status van het laatste gegevens punt in de gegevensset te detecteren. Roep de hierboven gemaakte `sendRequest()` methode aan met uw eind punt, URL, abonnements sleutel en JSON-gegevens. Bekijk het resultaat en druk het af op de-console.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Laad uw time series-gegevens en verzend de aanvraag

1. In de hoofd methode van uw toepassing leest u in het JSON-bestand dat de gegevens bevat die aan de aanvragen worden toegevoegd.

2. Roep de twee hierboven gemaakte anomalie detectie functies aan.

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Voorbeeld van een antwoord

Een geslaagde reactie wordt geretourneerd in JSON-indeling. Klik op de onderstaande koppelingen om het JSON-antwoord op GitHub weer te geven:
* [Voor beeld van een antwoord op een batch detectie](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Voor beeld van laatste punt detectie respons](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]

---
title: 'Snelstart: afwijkingen in uw tijdreeksgegevens detecteren met behulp van de Anomaly Detector REST API en Java'
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
ms.openlocfilehash: 4e7cc969ff2c36415b8fd1cebafa1873f9efd38c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239058"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Snelstart: afwijkingen in uw tijdreeksgegevens detecteren met behulp van de Anomaly Detector REST API en Java

Gebruik deze snelle start om de twee detectiemodi van de Anomaly Detector API te gebruiken om afwijkingen in uw tijdreeksgegevens te detecteren. Deze Java-toepassing verzendt twee API-aanvragen met JSON-geformatteerde tijdreeksgegevens en krijgt de antwoorden.

| API-aanvraag                                        | Toepassingsuitvoer                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Afwijkingen detecteren als een batch                        | De JSON-respons met de anomaliestatus (en andere gegevens) voor elk gegevenspunt in de gegevens van de tijdreeks en de posities van gedetecteerde afwijkingen. |
| De anomaliestatus van het laatste gegevenspunt detecteren | De JSON-respons met de anomaliestatus (en andere gegevens) voor het meest recente gegevenspunt in de gegevens van de tijdreeksen.                                                                                                                                         |

 Hoewel deze toepassing in Java is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal. U vindt de broncode voor deze quickstart op [GitHub.](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java)

## <a name="prerequisites"></a>Vereisten

- De [&trade; Java Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) of hoger.
- Een anomaliedetectorsleutel en eindpunt
- Importeer deze bibliotheken uit de Maven Repository
    - [JSON in Java](https://mvnrepository.com/artifact/org.json/json) pakket
    - [Apache HttpClient-pakket](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Een JSON-bestand met tijdreeksgegevenspunten. De voorbeeldgegevens voor deze quickstart zijn te vinden op [GitHub.](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)

### <a name="create-an-anomaly-detector-resource"></a>Een Anomaliedetectorbron maken

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

1. Maak een nieuw Java-project en importeer de volgende bibliotheken.
    
    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Maak variabelen voor uw abonnementssleutel en uw eindpunt. Hieronder vindt u de URI's die u gebruiken voor anomaliedetectie. Deze worden later toegevoegd aan het eindpunt van uw service om url's voor API-aanvragen te maken.

    |Detectiemethode  |URI  |
    |---------|---------|
    |Batchdetectie    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Detectie op het laatste gegevenspunt     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Een functie maken om aanvragen te verzenden

1. Maak een nieuwe `sendRequest()` functie genaamd die de hierboven gemaakte variabelen neemt. Voer vervolgens de volgende stappen uit.

2. Maak `CloseableHttpClient` een object dat aanvragen naar de API kan verzenden. Stuur het verzoek `HttpPost` naar een aanvraagobject door uw eindpunt te combineren met een URL van de anomaliedetector.

3. Gebruik de functie `setHeader()` van het `Content-Type` verzoek `application/json`om de koptekst `Ocp-Apim-Subscription-Key` in te stellen op en voeg uw abonnementssleutel toe aan de koptekst.

4. Gebruik de functie `setEntity()` van de aanvraag voor de te sturen gegevens.

5. Gebruik de functie `execute()` van de client om de `CloseableHttpResponse` aanvraag te verzenden en op te slaan in een object.

6. Maak `HttpEntity` een object om de antwoordinhoud op te slaan. Krijg de `getEntity()`inhoud met . Als het antwoord niet leeg is, moet u het antwoord retourneren.

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Afwijkingen detecteren als een batch

1. Maak een `detectAnomaliesBatch()` methode die wordt aangeroepen om afwijkingen in de gegevens als batch te detecteren. Roep `sendRequest()` de hierboven gemaakte methode aan met uw eindpunt, url, abonnementssleutel en json-gegevens. Haal het resultaat en print het op de console.

2. Als het `code` antwoord veld bevat, drukt u de foutcode en het foutbericht af.

3. Zoek anders de posities van afwijkingen in de gegevensset. Het veld `isAnomaly` van het antwoord bevat een booleaanse waarde met betrekking tot de vraag of een bepaald gegevenspunt een afwijking is. Haal de JSON-array en werk erdoorheen en `true` druk de index van alle waarden af. Deze waarden komen overeen met de index van afwijkende gegevenspunten, indien deze zijn gevonden.

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>De anomaliestatus van het laatste gegevenspunt detecteren

Maak een `detectAnomaliesLatest()` methode die wordt aangeroepen om de anomaliestatus van het laatste gegevenspunt in de gegevensset te detecteren. Roep `sendRequest()` de hierboven gemaakte methode aan met uw eindpunt, url, abonnementssleutel en json-gegevens. Haal het resultaat en print het op de console.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Laad uw tijdreeksgegevens en verstuur de aanvraag

1. Lees in de hoofdmethode van uw toepassing in het JSON-bestand met de gegevens die aan de aanvragen worden toegevoegd.

2. Roep de twee anomaliedetectiefuncties aan die hierboven zijn gemaakt.

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Voorbeeld van een antwoord

Een geslaagdantwoord wordt geretourneerd in JSON-indeling. Klik op de onderstaande links om het JSON-antwoord op GitHub te bekijken:
* [Voorbeeld batchdetectierespons](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Voorbeeld van de laatste puntdetectierespons](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]

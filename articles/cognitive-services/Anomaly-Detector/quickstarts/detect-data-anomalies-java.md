---
title: 'Quickstart: Afwijkingen in uw tijdreeksgegevens detecteren met behulp van de Anomaly Detector REST API en Java'
titleSuffix: Azure Cognitive Services
description: Leer hoe u de Anomaly Detector API kunt gebruiken om afwijkingen in uw gegevensreeksen op te sporen als batch of op gegevensstromen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 09/03/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 6c37ac4a8e43f8e11e37186e2438c4803556339e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905757"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Quickstart: Afwijkingen in uw tijdreeksgegevens detecteren met behulp van de Anomaly Detector REST API en Java

Gebruik deze quickstart om aan de slag te gaan met het gebruiken van de twee detectiemodi van de Anomaly Detector API's om anomalieën in uw tijdreeksgegevens te detecteren. Met deze Java-toepassing worden API-aanvragen met tijdreeksgegevens in JSON-indeling verzonden en worden de reacties opgehaald.

| API-aanvraag                                        | Toepassingsuitvoer                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Anomalieën detecteren als een batch                        | Het JSON-antwoord met de anomaliestatus (en andere gegevens) voor elk gegevenspunt in de tijdreeksgegevens en de posities van gedetecteerde anomalieën. |
| De anomaliestatus van het laatste gegevenspunt detecteren | Het JSON-antwoord met de anomaliestatus (en andere gegevens) voor het laatste gegevenspunt in de tijdreeksgegevens.   |
| Wijzigingspunten detecteren die nieuwe gegevenstrends markeren | Het JSON-antwoord met de gedetecteerde wijzigingspunten in de tijdreeksgegevens. |

Hoewel deze toepassing in Java is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal. U kunt de broncode voor deze quickstart vinden op [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java).

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
- Zodra u een Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector-resource maken"  target="_blank">maakt u een Anomaly Detector-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    - U hebt de sleutel en het eindpunt nodig van de resource die u maakt om de toepassing te verbinden met de Anomaly Detector-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde categorie voor productie.
- De [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) of hoger.
- Deze bibliotheken importeren vanuit de Maven-opslagplaats
    - [JSON in Java](https://mvnrepository.com/artifact/org.json/json)-pakket
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)-pakket

- Een JSON-bestand dat tijdreeksgegevenspunten bevat. De voorbeeldgegevens voor deze quickstart zijn te vinden op [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

1. Maak een nieuw Java-project en importeer de volgende bibliotheken.

    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Maak variabelen voor uw abonnementssleutel en eindpunt. Hieronder vindt u de URI's die u voor anomaliedetectie kunt gebruiken. Deze worden later toegevoegd aan uw service-eindpunt om de API-aanvraag-URL's te maken.

    |Detectiemethode  |URI  |
    |---------|---------|
    |Batchdetectie    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Detectie op het laatste gegevenspunt     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    | Detectie van wijzigingspunten | `/anomalydetector/v1.0/timeseries/changepoint/detect`   |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Een functie maken voor het verzenden van aanvragen

1. Maak een nieuwe functie met de naam `sendRequest()` die de hierboven gemaakte variabelen gebruikt. Voer vervolgens de volgende stappen uit.

2. Maak een `CloseableHttpClient`-object waarmee aanvragen kunnen worden verzonden naar de API. Verzend de aanvraag naar een `HttpPost`-aanvraagobject door uw eindpunt en een Anomaly Detector-URL te combineren.

3. Stel met behulp van de `setHeader()`-functie van de aanvraag de `Content-Type`-header in op `application/json`, en voeg uw abonnementssleutel toe aan de `Ocp-Apim-Subscription-Key`-header.

4. Gebruik de `setEntity()`-functie van de aanvraag voor de te verzenden gegevens.

5. Gebruik de `execute()`-functie van de client om de aanvraag te verzenden en sla deze op in een `CloseableHttpResponse`-object.

6. Maak een `HttpEntity`-object voor het opslaan van de inhoud van het antwoord. Haal de inhoud op met `getEntity()`. Als het antwoord niet leeg is, retourneert u het.

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Anomalieën detecteren als een batch

1. Maak een methode met de naam `detectAnomaliesBatch()` om anomalieën in de gegevens batchgewijs te detecteren. Roep de hierboven gemaakte `sendRequest()`-methode aan met uw eindpunt, URL, abonnementssleutel en JSON-gegevens. Haal het resultaat op en druk het af naar de console.

2. Als het antwoord het veld `code` bevat, drukt u de foutcode en het foutbericht af.

3. Als dat niet het geval is, zoekt u de positie van anomalieën in de gegevensset. Het veld `isAnomaly` van de reactie bevat een booleaanse waarde die aangeeft of een bepaald gegevenspunt een anomalie is. Haal de JSON-matrix op en doorloop deze, waarbij u de index van eventuele `true`-waarden afdrukt. Deze waarden komen overeen met de index van afwijkende gegevenspunten, als die zijn gevonden.

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>De anomaliestatus van het laatste gegevenspunt detecteren

Maak een methode met de naam `detectAnomaliesLatest()` om de anomaliestatus van het laatste gegevenspunt in de gegevensset te detecteren. Roep de hierboven gemaakte `sendRequest()`-methode aan met uw eindpunt, URL, abonnementssleutel en JSON-gegevens. Haal het resultaat op en druk het af naar de console.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]


## <a name="detect-change-points-in-the-data"></a>Wijzigingspunten in de gegevens detecteren

1. Maak een methode met de naam `detectChangePoints()` om anomalieën in de gegevens batchgewijs te detecteren. Roep de hierboven gemaakte `sendRequest()`-methode aan met uw eindpunt, URL, abonnementssleutel en JSON-gegevens. Haal het resultaat op en druk het af naar de console.

2. Als het antwoord een `code`-veld bevat, drukt u de foutcode en het foutbericht af.

3. Als dat niet het geval is, zoekt u de positie van wijzigingspunten in de gegevensset. Het veld `isChangePoint` van het antwoord bevat een booleaanse waarde die aangeeft of een bepaald gegevenspunt een trendwijzigingspunt is. Haal de JSON-matrix op en doorloop deze, waarbij u de index van eventuele `true`-waarden afdrukt. Deze waarden komen overeen met de indexen van trendwijzigingspunten, als die zijn gevonden.

    [!code-java[detect change points](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectChangePoint)]

## <a name="load-your-time-series-data-and-send-the-request"></a>De gegevens van uw tijdreeks laden en de aanvraag verzenden

1. Lees in de hoofdmethode van uw toepassing het JSON-bestand in dat de gegevens bevat die aan de aanvragen worden toegevoegd.

2. Roep de twee hierboven gemaakte anomaliedetectie functies aan.

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Voorbeeld van een antwoord

Er wordt een geslaagd antwoord geretourneerd in JSON-indeling. Klik op de onderstaande koppelingen om het JSON-antwoord op GitHub weer te geven:
* [Voorbeeld van een antwoord op een batchdetectie](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Voorbeeld van een antwoord op de detectie van het laatste punt](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)
* [Voorbeeld van een antwoord op de detectie van het wijzigingspunt](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/change-point-sample.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]

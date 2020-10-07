---
title: 'Quickstart: Anomalieën detecteren in uw tijdreeksgegevens met behulp van de Anomaly Detector-REST API en C#'
titleSuffix: Azure Cognitive Services
description: Ontdek hoe u de twee detectiemodi van de Anomaly Detector API's kunt gebruiken om afwijkingen in uw tijdreeksgegevens te detecteren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: c19f901f4419cca59a3b4e070191c8bb89ce7c34
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319194"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Quickstart: Anomalieën detecteren in uw tijdreeksgegevens met behulp van de Anomaly Detector-REST API en C#

Gebruik deze quickstart om aan de slag te gaan met de Anomaly Detector API om anomalieën in uw tijdreeksgegevens te detecteren. Met deze C#-toepassing worden API-aanvragen met tijdreeksgegevens in JSON-indeling verzonden en worden de antwoorden opgehaald.

| API-aanvraag                                        | Toepassingsuitvoer                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Batchgewijs anomalieën detecteren                        | Het JSON-antwoord met de anomaliestatus (en andere gegevens) voor elk gegevenspunt in de tijdreeksgegevens en de posities van gedetecteerde anomalieën. |
| De anomaliestatus van het laatste gegevenspunt detecteren | Het JSON-antwoord met de anomaliestatus (en andere gegevens) voor het laatste gegevenspunt in de tijdreeksgegevens. |
| Wijzigingspunten detecteren die nieuwe gegevenstrends markeren | Het JSON-antwoord met de gedetecteerde wijzigingspunten in de tijdreeksgegevens. |

Hoewel deze toepassing in C# is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal. U kunt de broncode voor deze quickstart vinden op [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs).

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
- Zodra u een Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector-resource maken"  target="_blank">maakt u een Anomaly Detector-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    - U hebt de sleutel en het eindpunt nodig van de resource die u maakt om de toepassing te verbinden met de Anomaly Detector-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
- Een versie van [Visual Studio 2017 of hoger](https://visualstudio.microsoft.com/downloads/)
- Het [Json.NET](https://www.newtonsoft.com/json)-framework, beschikbaar als NuGet-pakket. Newtonsoft.json installeren als een NuGet-pakket in Visual Studio:

    1. Klik met de rechtermuisknop op uw project in **Solution Explorer**.
    2. Selecteer **NuGet-pakketten beheren**.
    3. Zoek vervolgens naar *Newtonsoft.Json* en installeer het pakket.

- Als u Linux/MacOS gebruikt, kan deze toepassing worden uitgevoerd met behulp van [Mono](https://www.mono-project.com/).

- Een JSON-bestand dat tijdreeksgegevenspunten bevat. De voorbeeldgegevens voor deze quickstart zijn te vinden op [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

1. Maak in Visual Studio een nieuwe consoleoplossing en voeg de volgende pakketten toe.

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. Maak variabelen voor uw abonnementssleutel en eindpunt. Hieronder vindt u de URI's die u voor anomaliedetectie kunt gebruiken. Deze worden later toegevoegd aan uw service-eindpunt om de API-aanvraag-URL's te maken.

    | Detectiemethode                   | URI                                              |
    |------------------------------------|--------------------------------------------------|
    | Batchdetectie                    | `/anomalydetector/v1.0/timeseries/entire/detect` |
    | Detectie op het laatste gegevenspunt | `/anomalydetector/v1.0/timeseries/last/detect`   |
    | Detectie van wijzigingspunten | `/anomalydetector/v1.0/timeseries/changepoint/detect`   |

    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Een functie maken voor het verzenden van aanvragen

1. Maak een nieuwe asynchrone functie met de naam `Request` die de hierboven gemaakte variabelen gebruikt.

2. Stel het beveiligingsprotocol en de headergegevens van de client in met behulp van een `HttpClient`-object. Vergeet niet om de abonnementssleutel toe te voegen aan de `Ocp-Apim-Subscription-Key`-header. Maak vervolgens een `StringContent`-object voor de aanvraag.

3. Verzend de aanvraag met `PostAsync()`, en retourneer het antwoord.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Batchgewijs anomalieën detecteren

1. Maak een nieuwe functie met de naam `detectAnomaliesBatch()`. Stel de aanvraag op en verzend deze door de `Request()`-functie aan te roepen met uw eindpunt, de abonnementssleutel, de URL voor de batchgewijze anomaliedetectie en de tijdreeksgegevens.

2. Deserialiseer het JSON-object en schrijf het naar de console.

3. Als het antwoord het veld `code` bevat, drukt u de foutcode en het foutbericht af.

4. Als dat niet het geval is, zoekt u de positie van anomalieën in de gegevensset. Het veld `isAnomaly` van het antwoord bevat een matrix met booleaanse waarden, waarvan elke waarde aangeeft of een gegevenspunt een anomalie is. Converteer deze naar een tekenreeksmatrix met de `ToObject<bool[]>()`-functie van het antwoordobject. Herhaal de matrix en druk de index van alle `true`-waarden af. Deze waarden komen overeen met de index van afwijkende gegevenspunten, als die zijn gevonden.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>De anomaliestatus van het laatste gegevenspunt detecteren

1. Maak een nieuwe functie met de naam `detectAnomaliesLatest()`. Stel de aanvraag op en verzend deze door de `Request()`-functie aan te roepen met uw eindpunt, de abonnementssleutel, de URL voor de meest recente batchgewijze anomaliedetectie en de tijdreeksgegevens.

2. Deserialiseer het JSON-object en schrijf het naar de console.

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]

## <a name="detect-change-points-in-the-data"></a>Wijzigingspunten in de gegevens detecteren

1. Maak een nieuwe functie met de naam `detectChangePoints()`. Stel de aanvraag op en verzend deze door de `Request()`-functie aan te roepen met uw eindpunt, de URL voor de batchgewijze anomaliedetectie, uw abonnementssleutel en de tijdreeksgegevens.

2. Deserialiseer het JSON-object en schrijf het naar de console.

3. Als het antwoord een `code`-veld bevat, drukt u de foutcode en het foutbericht af.

4. Als dat niet het geval is, zoekt u de positie van wijzigingspunten in de gegevensset. Het veld `isChangePoint` van het antwoord bevat een matrix met booleaanse waarden, waarvan elke waarde aangeeft of een gegevenspunt is geïdentificeerd als een wijzigingspunt. Converteer deze naar een tekenreeksmatrix met de `ToObject<bool[]>()`-functie van het antwoordobject. Herhaal de matrix en druk de index van alle `true`-waarden af. Deze waarden komen overeen met de indexen van trendwijzigingspunten, als die zijn gevonden.

    [!code-csharp[Detect change points](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectChangePoints)]

## <a name="load-your-time-series-data-and-send-the-request"></a>De gegevens van uw tijdreeks laden en de aanvraag verzenden

1. In de hoofdmethode van uw toepassing laadt u uw JSON-tijdreeksgegevens met `File.ReadAllText()`.

2. Roep de hierboven gemaakte anomaliedetectiefuncties aan. Gebruik `System.Console.ReadKey()` om het consolevenster geopend te houden nadat de toepassing is uitgevoerd.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Voorbeeld van een antwoord

Er wordt een geslaagd antwoord geretourneerd in JSON-indeling. Klik op de onderstaande koppelingen om het JSON-antwoord op GitHub weer te geven:
* [Voorbeeld van een antwoord op een batchdetectie](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Voorbeeld van een antwoord op de detectie van het laatste punt](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)
* [Voorbeeld van een antwoord op de detectie van het wijzigingspunt](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/change-point-sample.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]

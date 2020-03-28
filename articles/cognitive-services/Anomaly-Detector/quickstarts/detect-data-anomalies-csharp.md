---
title: 'Snelstart: afwijkingen in uw tijdreeksgegevens detecteren met behulp van de Anomaly Detector REST API en C #'
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
ms.openlocfilehash: b53fecad3655048a7b9d799134926b2730b16dae
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239113"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Snelstart: afwijkingen in uw tijdreeksgegevens detecteren met behulp van de Anomaly Detector REST API en C # 

Gebruik deze snelle start om de twee detectiemodi van de Anomaly Detector API te gebruiken om afwijkingen in uw tijdreeksgegevens te detecteren. Deze C#-toepassing verzendt twee API-aanvragen met JSON-geformatteerde tijdreeksgegevens en krijgt de antwoorden.

| API-aanvraag                                        | Toepassingsuitvoer                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Afwijkingen detecteren als een batch                        | De JSON-respons met de anomaliestatus (en andere gegevens) voor elk gegevenspunt in de gegevens van de tijdreeks en de posities van gedetecteerde afwijkingen. |
| De anomaliestatus van het laatste gegevenspunt detecteren | De JSON-respons met de anomaliestatus (en andere gegevens) voor het meest recente gegevenspunt in de gegevens van de tijdreeksen.                                        |

 Hoewel deze toepassing in C# is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal. U vindt de broncode voor deze quickstart op [GitHub.](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs)

## <a name="prerequisites"></a>Vereisten

- Elke editie van [Visual Studio 2017 of hoger](https://visualstudio.microsoft.com/downloads/),
- Een anomaliedetectorsleutel en eindpunt
- Het [Json.NET](https://www.newtonsoft.com/json) framework, beschikbaar als NuGet-pakket. Ga als lid van De Jong naar Newtonsoft.Json als NuGet-pakket in Visual Studio:
    
    1. Klik met de rechtermuisknop op uw project in **Solution Explorer.**
    2. Selecteer **NuGet-pakketten beheren**.
    3. Zoek naar *Newtonsoft.Json* en installeer het pakket.

- Als u Linux/MacOS gebruikt, kan deze toepassing worden uitgevoerd met [Mono](https://www.mono-project.com/).

- Een JSON-bestand met tijdreeksgegevenspunten. De voorbeeldgegevens voor deze quickstart zijn te vinden op [GitHub.](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)

### <a name="create-an-anomaly-detector-resource"></a>Een Anomaliedetectorbron maken

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Een nieuwe toepassing maken

1. Maak in Visual Studio een nieuwe consoleoplossing en voeg de volgende pakketten toe. 

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. Maak variabelen voor uw abonnementssleutel en uw eindpunt. Hieronder vindt u de URI's die u gebruiken voor anomaliedetectie. Deze worden later toegevoegd aan het eindpunt van uw service om url's voor API-aanvragen te maken.

    | Detectiemethode                   | URI                                              |
    |------------------------------------|--------------------------------------------------|
    | Batchdetectie                    | `/anomalydetector/v1.0/timeseries/entire/detect` |
    | Detectie op het laatste gegevenspunt | `/anomalydetector/v1.0/timeseries/last/detect`   |
        
    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Een functie maken om aanvragen te verzenden

1. Maak een nieuwe async-functie genaamd `Request` die de hierboven gemaakte variabelen neemt.

2. Stel het beveiligingsprotocol en de headergegevens van de client in met behulp van een `HttpClient` object. Zorg ervoor dat u uw `Ocp-Apim-Subscription-Key` abonnementssleutel toevoegt aan de koptekst. Maak vervolgens `StringContent` een object voor de aanvraag.

3. Stuur de `PostAsync()`aanvraag met , en stuur het antwoord.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Afwijkingen detecteren als een batch

1. Een nieuwe functie `detectAnomaliesBatch()`maken, genaamd . Bouw de aanvraag en verzend `Request()` deze door de functie aan te roepen met uw eindpunt, abonnementssleutel, de URL voor batchanomaliedetectie en de tijdreeksgegevens.

2. Deserialiseren van het JSON-object en schrijf het naar de console.

3. Als het `code` antwoord veld bevat, drukt u de foutcode en het foutbericht af. 

4. Zoek anders de posities van afwijkingen in de gegevensset. Het veld `isAnomaly` van het antwoord bevat een array met booleaanse waarden, die elk aangeven of een gegevenspunt een anomalie is. Converteer dit naar een tekenreeksarray `ToObject<bool[]>()` met de functie van het antwoordobject. Door de array te herhalen en de `true` index van eventuele waarden af te drukken. Deze waarden komen overeen met de index van afwijkende gegevenspunten, indien deze zijn gevonden.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>De anomaliestatus van het laatste gegevenspunt detecteren

1. Een nieuwe functie `detectAnomaliesLatest()`maken, genaamd . Bouw de aanvraag en verzend `Request()` deze door de functie aan te roepen met uw eindpunt, abonnementssleutel, de URL voor de nieuwste puntanomaliedetectie en de tijdreeksgegevens.

2. Deserialiseren van het JSON-object en schrijf het naar de console.

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]
 
## <a name="load-your-time-series-data-and-send-the-request"></a>Laad uw tijdreeksgegevens en verstuur de aanvraag

1. Laad in de hoofdmethode van uw toepassing uw `File.ReadAllText()`JSON-tijdreeksgegevens met . 

2. Roep de hierboven gemaakte anomaliedetectiefuncties aan. Met `System.Console.ReadKey()` deze versie moet u het consolevenster open houden nadat de toepassing is uitgevoerd.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Voorbeeld van een antwoord

Een geslaagdantwoord wordt geretourneerd in JSON-indeling. Klik op de onderstaande links om het JSON-antwoord op GitHub te bekijken:
* [Voorbeeld batchdetectierespons](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Voorbeeld van de laatste puntdetectierespons](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]

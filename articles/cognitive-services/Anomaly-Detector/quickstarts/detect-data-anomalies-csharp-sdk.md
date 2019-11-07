---
title: 'Snelstartgids: afwijkingen in tijdreeks gegevens detecteren met behulp van de afwijkende detector-client bibliotheek voor .NET'
titleSuffix: Azure Cognitive Services
description: Gebruik de anomalie detectie-API om afwijkingen in uw gegevens reeksen op te sporen als een batch of gegevens stromen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: aahi
ms.openlocfilehash: 67d6bb0bf880de0b4bf6878128e2ed27e130b18d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719004"
---
# <a name="quickstart-anomaly-detector-client-library-for-net"></a>Snelstartgids: anomalie detectie client bibliotheek voor .NET

Ga aan de slag met de anomalie detector-client bibliotheek voor .NET. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen. Met de anomalie detectie service kunt u afwijkingen in uw tijdreeks gegevens opsporen door automatisch de beste modellen te gebruiken, ongeacht de branche, het scenario of het gegevens volume.

Gebruik de anomalie detectie-client bibliotheek voor .NET voor het volgende:

* Anomalieën in uw time series-gegevensset detecteren, als een batch-aanvraag
* De afwijkings status van het laatste gegevens punt in uw tijd reeks detecteren

[Bibliotheek referentie documentatie](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/) | [code voorbeelden](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [.net core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>Instellen

### <a name="create-an-anomaly-detector-resource"></a>Een afwijkende detector-resource maken

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-net-core-application"></a>Een nieuwe .NET core-toepassing maken

Gebruik in een console venster (zoals cmd, Power shell of bash) de opdracht DotNet `new` om een nieuwe console-app te maken met de naam `anomaly-detector-quickstart`. Met deze opdracht maakt u een eenvoudig ' Hallo wereld '-project C# met één bron bestand: *Program.cs*. 

```console
dotnet new console -n anomaly-detector-quickstart
```

Wijzig uw directory in de zojuist gemaakte app-map. U kunt de toepassing samen stellen met:

```console
dotnet build
```

De build-uitvoer mag geen waarschuwingen of fouten bevatten. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Installeer in de toepassingsmap de Anomaliey detector-client bibliotheek voor .NET met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

Open in de projectmap het *Program.cs* -bestand en voeg het volgende toe met behulp van `directives`:

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

Maak in de `main()` methode van de toepassing variabelen voor de Azure-locatie van uw resource en uw sleutel als een omgevings variabele. Als u de omgevings variabele hebt gemaakt nadat de toepassing is gestart, moet de editor, IDE of shell die deze uitvoert, worden gesloten en opnieuw worden geladen om toegang te krijgen tot de variabele.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>Object model

De anomalie detectie-client is een [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) -object dat wordt geverifieerd bij Azure met behulp van [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), dat uw sleutel bevat. De-client biedt twee methoden voor anomalie detectie: op een hele gegevensset met behulp van [EntireDetectAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync)en op het laatste gegevens punt met behulp van [LastDetectAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). 

Tijdreeks gegevens worden verzonden als een reeks [punten](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) in een [aanvraag](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) object. Het `Request`-object bevat eigenschappen die de gegevens beschrijven (bijvoorbeeld[granulatie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) ) en para meters voor de anomalie detectie. 

De afwijkende detector respons is een [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) -of [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) -object, afhankelijk van de gebruikte methode. 

## <a name="code-examples"></a>Code voorbeelden

Deze code fragmenten laten zien hoe u het volgende kunt doen met de anomalie detectie-client bibliotheek voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Een time series gegevensset laden vanuit een bestand](#load-time-series-data-from-a-file)
* [Afwijkingen in de volledige gegevensset detecteren](#detect-anomalies-in-the-entire-data-set) 
* [De afwijkings status van het laatste gegevens punt detecteren](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>De client verifiëren

In een nieuwe methode maakt u een exemplaar van een client met uw eind punt en sleutel. Maak een [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) -object met uw sleutel en gebruik het met uw eind punt om een [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview) -object te maken. 

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]
    
## <a name="load-time-series-data-from-a-file"></a>Time Series-gegevens uit een bestand laden

Down load de voorbeeld gegevens voor deze Quick Start van [github](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. Klik in uw browser met de rechter muisknop op **RAW**.
2. Klik op **Koppeling opslaan als**.
3. Sla het bestand op in de map van de toepassing, als een CSV-bestand.

Deze tijdreeks gegevens worden opgemaakt als een CSV-bestand en worden verzonden naar de anomalie detector-API.

Maak een nieuwe methode om te lezen in de time series-gegevens en voeg deze toe aan een [aanvraag](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) object. `File.ReadAllLines()` aanroepen met het bestandspad en een lijst met [punt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) objecten maken en nieuwe regel tekens verwijderen. Pak de waarden uit en scheid de datestamp van de numerieke waarde en voeg ze toe aan een nieuw `Point`-object. 

Maak een `Request` object met de punten reeks en `Granularity.Daily` voor de [granulariteit](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (of periodiciteit) van de gegevens punten.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Afwijkingen in de volledige gegevensset detecteren 

Maak een methode voor het aanroepen van de [EntireDetectAsync ()-](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) methode van de client met het object `Request` en wacht op het antwoord als een [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) -object. Als de tijd reeks afwijkingen bevat, herhaalt u de [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) waarden van de reactie en drukt u deze af `true`. Deze waarden komen overeen met de index van afwijkende gegevens punten, als deze zijn gevonden.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>De afwijkings status van het laatste gegevens punt detecteren

Maak een methode voor het aanroepen van de [LastDetectAsync ()-](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) methode van de client met het object `Request` en wacht op het antwoord als een [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) -object. Controleer het [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) -kenmerk van het antwoord om te bepalen of het meest recente verzonden gegevens punt een afwijkend of niet is. 

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht DotNet `run` van de toepassingsmap.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle andere resources die aan de resource groep zijn gekoppeld, verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Anomalie detectie met Azure Databricks streamen](../tutorials/anomaly-detection-streaming-databricks.md)

* Wat is de [anomalie detectie-API?](../overview.md)
* [Aanbevolen procedures](../concepts/anomaly-detection-best-practices.md) voor het gebruik van de anomalie detectie-API.
* De broncode voor dit voorbeeld is te vinden [op GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).

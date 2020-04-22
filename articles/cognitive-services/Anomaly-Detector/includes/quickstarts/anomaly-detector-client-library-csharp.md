---
title: Anomaliedetector .NET-clientbibliotheek snel gestart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2020
ms.author: aahi
ms.openlocfilehash: f5f135dd44ad9e5ebd1df2144295f9a3cf3cb743
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759998"
---
Ga aan de slag met de Clientbibliotheek Anomaly Detector voor .NET. Volg deze stappen om het pakket te installeren en probeer de voorbeeldcode voor basistaken uit. Met de Anomaly Detector-service u afwijkingen in uw tijdreeksgegevens vinden door automatisch de best passende modellen te gebruiken, ongeacht de industrie, het scenario of het gegevensvolume.

Gebruik de clientbibliotheek Anomaliedetector voor .NET om:

* Afwijkingen in uw tijdreeksgegevensset detecteren als batchaanvraag
* De anomaliestatus van het laatste gegevenspunt in uw tijdreeks detecteren

[Bibliotheekreferentiedocumentatie](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/) | De code zoeken op[GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Een anomaliedetectorsleutel en eindpunt

## <a name="setting-up"></a>Instellen

### <a name="create-an-anomaly-detector-resource"></a>Een Anomaliedetectorbron maken

[!INCLUDE [anomaly-detector-resource-creation](../../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-net-core-application"></a>Een nieuwe .NET Core-toepassing maken

Gebruik de `dotnet new` opdracht om in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe console-app met de naam `anomaly-detector-quickstart`te maken. Met deze opdracht wordt een eenvoudig "Hello World"-project gemaakt met één C#-bronbestand: *Program.cs*. 

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

Wijzig uw map in de nieuw gemaakte app-map. U de toepassing bouwen met:

```dotnetcli
dotnet build
```

De buildoutput mag geen waarschuwingen of fouten bevatten. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer in de toepassingsmap de clientbibliotheek Anomaly Detector voor .NET met de volgende opdracht:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

Open in de projectmap het *program.cs-bestand* en voeg het volgende toe met: `directives`

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

Maak in de `main()` toepassingsmethode variabelen voor de Azure-locatie van uw bron en uw sleutel als omgevingsvariabele. Als u de omgevingsvariabele hebt gemaakt nadat de toepassing is gestart, moet de editor, IDE of shell die wordt uitgevoerd, worden gesloten en opnieuw worden geladen om toegang te krijgen tot de variabele.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>Objectmodel

De AnomalieDetector-client is een [Object AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) dat met [ApiKeyServiceClientCredentials ( ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials)) naar Azure wordt geverifieerd. De client biedt twee methoden voor anomaliedetectie: op een hele gegevensset met [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync)en op het nieuwste gegevenspunt met [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). 

Tijdreeksgegevens worden verzonden als een reeks [punten](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) in een object [Request.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) Het `Request` object bevat eigenschappen om de gegevens[(granulariteit](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) bijvoorbeeld) en parameters voor de anomaliedetectie te beschrijven. 

De anomaliedetectorrespons is een [object EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) of [LastDetectResponse,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) afhankelijk van de gebruikte methode. 

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende doen met de clientbibliotheek Anomaliedetector voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Een tijdreeksgegevensset uit een bestand laden](#load-time-series-data-from-a-file)
* [Afwijkingen in de gehele gegevensset detecteren](#detect-anomalies-in-the-entire-data-set) 
* [De anomaliestatus van het laatste gegevenspunt detecteren](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>De client verifiëren

In een nieuwe methode u een klant instantiëren met uw eindpunt en sleutel. Maak een [Object ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) met uw sleutel en gebruik het met uw eindpunt om een [Object AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview) te maken. 

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]
    
## <a name="load-time-series-data-from-a-file"></a>Gegevens van de tijdreeksen uit een bestand laden

Download de voorbeeldgegevens voor deze quickstart van [GitHub:](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv)
1. Klik in uw browser met de rechtermuisknop op **Raw**.
2. Klik **op Koppeling opslaan als**.
3. Sla het bestand op in uw toepassingsmap als csv-bestand.

Deze tijdreeksgegevens worden opgemaakt als een CSV-bestand en worden naar de API voor anomaliedetector en verzonden.

Maak een nieuwe methode om gegevens uit de tijdreeks en voeg deze toe aan een [object Request.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) Bel `File.ReadAllLines()` met het bestandspad en maak een lijst met [puntobjecten](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) en strip eventuele nieuwe regeltekens. Haal de waarden en scheid de datumstempel van de `Point` numerieke waarde en voeg ze toe aan een nieuw object. 

Maak `Request` een object met de `Granularity.Daily` reeks punten en voor de [granulariteit](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (of periodiciteit) van de gegevenspunten.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Afwijkingen in de gehele gegevensset detecteren 

Maak een methode om de methode [EntireDetectAsync() van](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) de client aan te roepen met het `Request` object en wacht op het antwoord als een object [EntireDetectResponse.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) Als de tijdreeks afwijkingen bevat, herhalen door de [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) waarden van `true`de reactie en print een die zijn . Deze waarden komen overeen met de index van afwijkende gegevenspunten, indien deze zijn gevonden.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>De anomaliestatus van het laatste gegevenspunt detecteren

Maak een methode om de methode [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) van de client aan te roepen met het `Request` object en wacht op het antwoord als object [LastDetectResponse.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) Controleer het kenmerk [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) van het antwoord om te bepalen of het meest recente verzonden gegevenspunt een anomalie was of niet. 

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing `dotnet run` uit met de opdracht uit uw toepassingsmap.

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]

---
title: De status en resultaten van de Indexeer functie bewaken
titleSuffix: Azure Cognitive Search
description: De status, voortgang en resultaten van Azure Cognitive Search Indexeer functies in de Azure Portal bewaken met behulp van de REST API of de .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 02a0de7760c660a7cce1bbd9cd36d4bb2a1180e0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565776"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Azure Cognitive Search Indexeer functie-status en-resultaten controleren

Azure Cognitive Search biedt status-en controle gegevens over de huidige en historische uitvoeringen van elke Indexeer functie.

De controle van de Indexeer functie is handig wanneer u het volgende wilt doen:

* De voortgang van een Indexeer functie tijdens een doorlopende uitvoering volgen.
* Bekijk de resultaten van de lopende of vorige uitvoering van de Indexeer functie.
* Identificeer indexerings fouten op het hoogste niveau en fouten of waarschuwingen over afzonderlijke documenten die worden geïndexeerd.

## <a name="get-status-and-history"></a>Status en geschiedenis ophalen

U kunt de gegevens van de Indexeer functie op verschillende manieren openen, zoals:

* In de [Azure Portal](#portal)
* De [rest API](#restapi) gebruiken
* De [.NET-SDK](#dotnetsdk) gebruiken

De beschik bare bewakings gegevens voor indexeringen bevatten alle volgende (hoewel de gegevens indelingen verschillen op basis van de gebruikte toegangs methode):

* Status informatie over de Indexeer functie zelf
* Informatie over de meest recente uitvoering van de Indexeer functie, met inbegrip van de status, start-en eind tijden, en gedetailleerde fouten en waarschuwingen.
* Een lijst met historische Indexeer functies en de bijbehorende status, resultaten, fouten en waarschuwingen.

Indexeer functies die grote hoeveel heden gegevens verwerken, kunnen veel tijd in beslag nemen. Indexeer functies die miljoenen bron documenten verwerken, kunnen bijvoorbeeld 24 uur worden uitgevoerd en bijna onmiddellijk opnieuw worden opgestart. De status voor Indexeer functies met een hoog volume wordt mogelijk altijd in de portal **uitgevoerd** . Zelfs wanneer een Indexeer functie wordt uitgevoerd, zijn er details beschikbaar over lopende voortgang en eerdere uitvoeringen.

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>Controleren met behulp van de portal

U kunt de huidige status van al uw Indexeer functies weer geven in de lijst **Indexeer functies** op de pagina overzicht van de zoek service.

   ![Lijst met Indexeer functies](media/search-monitor-indexers/indexers-list.png "Lijst met Indexeer functies")

Wanneer een Indexeer functie wordt uitgevoerd, wordt de status in de lijst weer **gegeven en**wordt de waarde **docs geslaagd** toont het aantal documenten dat tot nu toe is verwerkt. Het kan een paar minuten duren voordat de index status waarden en het aantal documenten zijn bijgewerkt door de portal.

Een Indexeer functie waarvan de meest recente uitvoering **is geslaagd, is geslaagd.** Het uitvoeren van een Indexeer functie kan worden geslaagd, zelfs als afzonderlijke documenten fouten bevatten, als het aantal fouten minder is dan de instelling **maximale mislukte items** van de Indexeer functie.

Als de meest recente uitvoering **is**beëindigd met een fout, wordt de status weer gegeven. Als de status **opnieuw instellen is ingesteld** , is de status van het bijhouden van wijzigingen van de Indexeer functie opnieuw ingesteld.

Klik op een Indexeer functie in de lijst om meer details weer te geven over de huidige en recente uitvoeringen van de Indexeer functie.

   ![Overzicht van Indexeer functie en uitvoerings geschiedenis](media/search-monitor-indexers/indexer-summary.png "Overzicht van Indexeer functie en uitvoerings geschiedenis")

In de samenvattings grafiek van de **Indexeer functie** wordt een grafiek weer gegeven van het aantal documenten dat is verwerkt in de meest recente uitvoeringen.

In de lijst **uitvoerings Details** worden maxi maal 50 van de meest recente uitvoerings resultaten weer gegeven.

Klik op een uitvoer resultaat in de lijst om specifieke informatie over die uitvoering weer te geven. Dit geldt ook voor de start-en eind tijden en eventuele fouten en waarschuwingen die zijn opgetreden.

   ![Details van de uitvoering van de Indexeer functie](media/search-monitor-indexers/indexer-execution.png "Details van de uitvoering van de Indexeer functie")

Als er tijdens de uitvoering Documentspecifieke problemen zijn, worden deze weer gegeven in de velden fouten en waarschuwingen.

   ![Details van Indexeer functie met fouten](media/search-monitor-indexers/indexer-execution-error.png "Details van Indexeer functie met fouten")

Waarschuwingen zijn gebruikelijk met bepaalde typen Indexeer functies en geven niet altijd een probleem aan. Bijvoorbeeld Indexeer functies die gebruikmaken van cognitieve Services kunnen waarschuwingen rapporteren wanneer afbeeldings-of PDF-bestanden geen tekst bevatten die moeten worden verwerkt.

Zie problemen [met algemene Indexeer functies in Azure Cognitive Search oplossen](search-indexer-troubleshooting.md)voor meer informatie over het onderzoeken van fouten en waarschuwingen voor Indexeer functies.

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>Bewaken met REST-Api's

U kunt de status en de uitvoerings geschiedenis van een Indexeer functie ophalen met behulp van de [opdracht Get Indexing-status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
    api-key: [Search service admin key]

Het antwoord bevat de algemene status van de Indexeer functie, de laatste (of in uitvoering zijnde) indexerings aanroep en de geschiedenis van recente Indexeer functie-aanroepen.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

De uitvoerings geschiedenis bevat tot de 50 meest recente uitvoeringen, die in omgekeerde chronologische volg orde (meest recent eerst) worden gesorteerd.

Houd er rekening mee dat er twee verschillende status waarden zijn. De status op het hoogste niveau is voor de Indexeer functie zelf. Een Indexeer functie-status van **actief** betekent dat de Indexeer functie op de juiste wijze is ingesteld en kan worden uitgevoerd, maar niet dat deze op dat moment wordt uitgevoerd.

Elke uitvoering van de Indexeer functie heeft ook een eigen status die aangeeft of de specifieke uitvoering**actief**is (wordt uitgevoerd) of al is voltooid met de status **geslaagd**, **transientFailure**of **persistentFailure** . 

Wanneer een Indexeer functie opnieuw wordt ingesteld om de status van het bijhouden van wijzigingen te vernieuwen, wordt een afzonderlijke vermelding voor de uitvoerings geschiedenis toegevoegd met de status **opnieuw instellen** .

Zie [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)voor meer informatie over de status codes en de bewakings gegevens van de Indexeer functie.

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>Controleren met behulp van de .NET SDK

U kunt het schema voor een Indexeer functie definiëren met behulp van de Azure Cognitive Search .NET SDK. Hiertoe neemt u de eigenschap **schema** op wanneer u een Indexeer functie maakt of bijwerkt.

In het volgende C#-voor beeld wordt informatie over de status van een Indexeer functie en de resultaten van de meest recente (of doorlopende) uitvoer naar de-console geschreven.

```csharp
static void CheckIndexerStatus(Indexer indexer, SearchServiceClient searchService)
{
    try
    {
        IndexerExecutionInfo execInfo = searchService.Indexers.GetStatus(indexer.Name);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("  Run Status: {0}", result.Status.ToString());
        Console.WriteLine("  Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("  StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("  ErrorMessage: {0}", errorMsg);
        Console.WriteLine("  Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

De uitvoer in de-console ziet er ongeveer als volgt uit:

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

Houd er rekening mee dat er twee verschillende status waarden zijn. De status op het hoogste niveau is de status van de Indexeer functie zelf. Een Indexeer functie-status van **actief** betekent dat de Indexeer functie juist is ingesteld en beschikbaar is voor uitvoering, maar niet dat deze momenteel wordt uitgevoerd.

Elke uitvoering van de Indexeer functie heeft ook een eigen status om te bepalen of die specifieke uitvoering actief is (wordt**uitgevoerd**) of al is voltooid met de status **geslaagd** of **TransientError** . 

Wanneer een Indexeer functie opnieuw wordt ingesteld om de status van het bijhouden van wijzigingen te vernieuwen, wordt een afzonderlijk geschiedenis item toegevoegd met de status **opnieuw instellen** .

Zie [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) in de rest API voor meer informatie over de status codes en de bewakings gegevens van de Indexeer functie.

Details van Documentspecifieke fouten of waarschuwingen kunnen worden opgehaald door de lijsten `IndexerExecutionResult.Errors` en te inventariseren `IndexerExecutionResult.Warnings` .

Zie [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) en [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet)voor meer informatie over de .NET SDK-klassen die worden gebruikt om Indexeer functies te controleren.

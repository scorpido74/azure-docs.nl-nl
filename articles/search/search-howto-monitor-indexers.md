---
title: Indexerstatus en resultaten controleren
titleSuffix: Azure Cognitive Search
description: Controleer de status, voortgang en resultaten van Azure Cognitive Search-indexeerders in de Azure-portal met behulp van de REST API of de .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 699b5a4e5a7f10c883667ca5030dd971855467f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112988"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>De status en resultaten van Azure Cognitive Search-indexeerdercontroleren controleren

Azure Cognitive Search biedt status- en bewakingsgegevens over huidige en historische uitvoeringen van elke indexeerder.

Indexerbewaking is handig wanneer u:

* Volg de voortgang van een indexeerder tijdens een doorlopende run.
* Bekijk de resultaten van lopende of vorige indexerrun.
* Identificeer indexfouten op het hoogste niveau en fouten of waarschuwingen over afzonderlijke documenten die worden geïndexeerd.

## <a name="get-status-and-history"></a>Status en geschiedenis opdoen

U hebt op verschillende manieren toegang tot informatie over het monitoren van indexeren, waaronder:

* In [Azure Portal](#portal)
* De [API REST gebruiken](#restapi)
* De [.NET SDK gebruiken](#dotnetsdk)

Beschikbare indexerbewakingsinformatie bevat alle volgende (hoewel de gegevensindelingen verschillen op basis van de gebruikte toegangsmethode):

* Statusinformatie over de indexeerder zelf
* Informatie over de meest recente run van de indexer, inclusief de status, begin- en eindtijden en gedetailleerde fouten en waarschuwingen.
* Een lijst met historische indexeruitvoeringen en hun statussen, resultaten, fouten en waarschuwingen.

Indexers die grote hoeveelheden gegevens verwerken, kunnen lang duren om uit te voeren. Indexers die bijvoorbeeld miljoenen brondocumenten verwerken, kunnen 24 uur lang worden uitgevoerd en worden vrijwel onmiddellijk opnieuw opgestart. De status voor indexeerders met een hoog volume kan altijd **in uitvoering** zeggen in de portal. Zelfs wanneer een indexer wordt uitgevoerd, zijn details beschikbaar over de lopende voortgang en eerdere uitvoeringen.

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>Monitor met behulp van de portal

U de huidige status van al uw indexeerders zien in de lijst **Indexers** op de pagina Overzicht van uw zoekservice.

   ![Lijst met indexers](media/search-monitor-indexers/indexers-list.png "Lijst met indexers")

Wanneer een indexeerder wordt uitgevoerd, wordt in de lijst de **status In uitvoering**weergegeven en wordt in de waarde Documenten **geslaagd** het aantal documenten weergegeven dat tot nu toe is verwerkt. Het kan enkele minuten duren voordat de portal de statuswaarden en documenttellingen van indexeren bijwerkt.

Een indexeerder waarvan de meest recente run succesvol was, toont **Succes**. Een indexerrun kan succesvol zijn, zelfs als afzonderlijke documenten fouten bevatten, als het aantal fouten kleiner is dan de instelling **voor max-items** van de indexer.

Als de meest recente run is beëindigd met een fout, wordt de status **mislukt**. Een status van **Reset** betekent dat de status van wijzigingstracking van de indexer is gereset.

Klik op een indexeerder in de lijst voor meer informatie over de huidige en recente runs van de indexer.

   ![Indexer overzichts- en uitvoeringsgeschiedenis](media/search-monitor-indexers/indexer-summary.png "Indexer overzichts- en uitvoeringsgeschiedenis")

In **de overzichtsgrafiek Indexer** wordt een grafiek weergegeven van het aantal documenten dat is verwerkt in de meest recente uitvoeringen.

In de lijst **Uitvoeringsgegevens** worden maximaal 50 van de meest recente uitvoeringsresultaten weergegeven.

Klik op een uitvoeringsresultaat in de lijst om details over die run te zien. Dit omvat de begin- en eindtijden en eventuele fouten en waarschuwingen die zijn opgetreden.

   ![Indexer uitvoeringsgegevens](media/search-monitor-indexers/indexer-execution.png "Indexer uitvoeringsgegevens")

Als er tijdens het uitvoeren documentspecifieke problemen zijn geweest, worden deze weergegeven in de velden Fouten en waarschuwingen.

   ![Indexer details met fouten](media/search-monitor-indexers/indexer-execution-error.png "Indexer details met fouten")

Waarschuwingen komen vaak voor bij sommige typen indexeerders en wijzen niet altijd op een probleem. Indexeerders die cognitieve services gebruiken, kunnen bijvoorbeeld waarschuwingen melden wanneer afbeeldings- of PDF-bestanden geen tekst bevatten om te verwerken.

Zie [Veelvoorkomende indexerproblemen oplossen in Azure Cognitive Search](search-indexer-troubleshooting.md)voor meer informatie over het onderzoeken van indexerfouten en waarschuwingen.

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>Monitoren met REST API's

U de status en uitvoeringsgeschiedenis van een indexeerder ophalen met de [opdracht Indexerstatus ophalen:](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Het antwoord bevat de algemene indexerstatus, de laatste (of lopende) indexeraanroep en de geschiedenis van recente indexaanroepen.

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

Uitvoeringsgeschiedenis bevat maximaal de 50 meest recente runs, die in omgekeerde chronologische volgorde worden gesorteerd (meest recente eerste).

Let op: er zijn twee verschillende statuswaarden. De status op het hoogste niveau is voor de indexeerder zelf. Een indexerstatus van **het uitvoeren** betekent dat de indexer correct is ingesteld en beschikbaar is om uit te voeren, maar niet dat deze momenteel wordt uitgevoerd.

Elke run van de indexer heeft ook zijn eigen status die aangeeft of die specifieke uitvoering aan de gang is **(uitgevoerd)** of al is voltooid met een **succes**, **transientFailure**of **persistentFailure** status. 

Wanneer een indexer wordt gereset om de status van wijzigingstracking te vernieuwen, wordt een afzonderlijke uitvoeringsgeschiedenisvermelding toegevoegd met de **status Opnieuw instellen.**

Zie [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)voor meer informatie over statuscodes en indexerbewakingsgegevens.

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>Monitor met de .NET SDK

U de planning voor een indexeerder definiëren met behulp van de Azure Cognitive Search .NET SDK. Neem hiervoor de eigenschap **planning** op bij het maken of bijwerken van een indexer.

In het volgende C#-voorbeeld wordt informatie geschreven over de status van een indexeerder en de resultaten van de meest recente (of lopende) bewerking naar de console.

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

De uitvoer in de console ziet er ongeveer als volgt uit:

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

Let op: er zijn twee verschillende statuswaarden. De status op het hoogste niveau is de status van de indexeerder zelf. Een indexerstatus van **Uitvoering** betekent dat de indexer correct is ingesteld en beschikbaar is voor uitvoering, maar niet dat deze momenteel wordt uitgevoerd.

Elke run van de indexer heeft ook zijn eigen status voor de vraag of die specifieke uitvoering aan de gang is **(Running),** of al is voltooid met een **status Van succes** of Tijdelijke **Fout.** 

Wanneer een indexer wordt gereset om de status van wijzigingstracking te vernieuwen, wordt een afzonderlijke geschiedenisvermelding toegevoegd met de **status Opnieuw instellen.**

Zie [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) in de REST API voor meer informatie over statuscodes en informatie over indexermonitoring.

Details over documentspecifieke fouten of waarschuwingen kunnen worden opgehaald `IndexerExecutionResult.Errors` door `IndexerExecutionResult.Warnings`de lijsten op te sommen en .

Zie [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) en [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet)voor meer informatie over de .NET SDK-klassen die worden gebruikt om indexers te controleren.

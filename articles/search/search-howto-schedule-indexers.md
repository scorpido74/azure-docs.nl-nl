---
title: Indexeruitvoering plannen
titleSuffix: Azure Cognitive Search
description: Plan Azure Cognitive Search-indexeerders om inhoud periodiek of op specifieke tijdstippen te indexeren.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72326413d463d449d339b1f3fd241ba2c27b4b6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112942"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Indexers plannen in Azure Cognitive Search

Een indexer wordt normaal gesproken één keer uitgevoerd, onmiddellijk nadat deze is gemaakt. U het opnieuw op aanvraag uitvoeren met behulp van de portal, de REST API of de .NET SDK. U ook een indexer configureren om periodiek volgens een planning uit te voeren.

Sommige situaties waarin indexerplanning nuttig is:

* Brongegevens veranderen in de loop van de tijd en u wilt dat de Azure Cognitive Search-indexeerders de gewijzigde gegevens automatisch verwerken.
* De index wordt ingevuld vanuit meerdere gegevensbronnen en u wilt ervoor zorgen dat de indexers op verschillende tijdstippen worden uitgevoerd om conflicten te verminderen.
* De brongegevens zijn zeer groot en u wilt de indexerverwerking in de loop van de tijd verspreiden. Zie [Grote gegevenssets indexeren in Azure Cognitive Search](search-howto-large-index.md)voor meer informatie over het indexeren van grote hoeveelheden gegevens.

De planner is een ingebouwde functie van Azure Cognitive Search. U geen externe planner gebruiken om zoekindexeerders te beheren.

## <a name="define-schedule-properties"></a>Eigenschappen van planning definiëren

Een indexerschema heeft twee eigenschappen:
* **Interval**, dat de hoeveelheid tijd definieert tussen geplande indexeruitvoeringen. Het kleinste interval dat is toegestaan is 5 minuten, en de grootste is 24 uur.
* **Starttijd (UTC),** die de eerste keer aangeeft waarop de indexer moet worden uitgevoerd.

U een planning opgeven bij het eerst maken van de indexer of door de eigenschappen van de indexer later bij te werken. Indexerschema's kunnen worden ingesteld met behulp van de [portal,](#portal)de [REST API](#restApi)of de [.NET SDK](#dotNetSdk).

Er kan slechts één uitvoering van een indexer tegelijk worden uitgevoerd. Als een indexeerder al wordt uitgevoerd wanneer de volgende uitvoering is gepland, wordt die uitvoering uitgesteld tot de volgende geplande tijd.

Laten we eens kijken naar een voorbeeld om dit concreter te maken. Stel dat we een indexerschema configureren met een **interval** van uurtijd en een **begintijd** van 1 juni 2019 om 8:00:00 UUR UTC. Dit is wat er kan gebeuren wanneer een indexerrun langer dan een uur duurt:

* De eerste indexeruitvoering begint op of rond 1 juni 2019 om 08:00 UUR UTC. Stel dat deze uitvoering 20 minuten duurt (of op enig moment minder dan 1 uur).
* De tweede uitvoering begint op of rond 1 juni 2019 9:00 AM GMT. Stel dat deze uitvoering duurt 70 minuten - meer dan een uur - en het zal niet voltooien tot 10:10 AM GMT.
* De derde uitvoering begint om 10:00 uur UTC, maar op dat moment wordt de vorige uitvoering nog steeds uitgevoerd. Deze geplande uitvoering wordt vervolgens overgeslagen. De volgende uitvoering van de indexer begint pas om 11:00 uur UTC.

> [!NOTE]
> Als een indexer is ingesteld op een bepaald schema, maar herhaaldelijk niet op hetzelfde document over en weer elke keer dat het wordt uitgevoerd, zal de indexer beginnen te draaien op een minder frequent interval (tot het maximum van ten minste eenmaal per 24 uur) totdat het met succes vooruitgang boekt Opnieuw.  Als u denkt dat u het probleem hebt opgelost waardoor de indexer op een bepaald punt vast kwam te zitten, u een on-demand-run van de indexeerder uitvoeren en als dat met succes vooruitgang boekt, keert de indexer terug naar het ingestelde schemainterval.

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>Planning in de portal

Met de wizard Gegevens importeren in de portal u de planning voor een indexer bepalen bij het maken van de tijd. De instelling standaardplanning is **per uur,** wat betekent dat de indexer eenmaal wordt uitgevoerd nadat deze is gemaakt en elk uur daarna opnieuw wordt uitgevoerd.

U de instelling Schema wijzigen in **Eén** als u niet wilt dat de indexer automatisch opnieuw wordt uitgevoerd of in **Dagelijks** één keer per dag wordt uitgevoerd. Stel deze in op **Aangepast** als u een ander interval of een specifieke toekomstige begintijd wilt opgeven.

Wanneer u de planning instelt op **Aangepast,** worden de velden weergegeven waarmee u het **interval** en de **begintijd (UTC) kunt**opgeven. Het kortste toegestane tijdsinterval is 5 minuten en het langst 1440 minuten (24 uur).

   ![Indexerplanning instellen in de wizard Gegevens importeren](media/search-howto-schedule-indexers/schedule-import-data.png "Indexerplanning instellen in de wizard Gegevens importeren")

Nadat een indexer is gemaakt, u de planningsinstellingen wijzigen met het deelvenster Bewerken van de indexer. De velden Schema zijn hetzelfde als in de wizard Gegevens importeren.

   ![De planning instellen in het deelvenster Indexerbewerken](media/search-howto-schedule-indexers/schedule-edit.png "De planning instellen in het deelvenster Indexerbewerken")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>Plannen met REST API's

U de planning voor een indexer definiëren met behulp van de REST API. Om dit te doen, moet u de eigenschap **planning** opnemen bij het maken of bijwerken van de indexer. In het onderstaande voorbeeld ziet u een PUT-verzoek om een bestaande indexeerder bij te werken:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

De **intervalparameter** is vereist. Het interval verwijst naar de tijd tussen het begin van twee opeenvolgende indexeruitvoeringen. Het kleinste toegestane interval is 5 minuten; de langste is een dag. Het moet worden opgemaakt als een XSD "dayTimeDuration" waarde (een beperkte subset van een [ISO 8601 duurwaarde).](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) Het patroon hiervoor `P(nD)(T(nH)(nM))`is: . Voorbeelden: `PT15M` voor elke 15 `PT2H` minuten, voor elke 2 uur.

De optionele **startTime** geeft aan wanneer geplande uitvoeringen moeten beginnen. Als het wordt weggelaten, wordt de huidige UTC-tijd gebruikt. Deze tijd kan in het verleden zijn, in welk geval de eerste uitvoering is gepland alsof de indexer continu is uitgevoerd sinds de oorspronkelijke **startTime**.

U ook op elk gewenst moment een indexer op aanvraag uitvoeren met behulp van het aanroepen van Run Indexer. Zie [Indexeren](https://docs.microsoft.com/rest/api/searchservice/run-indexer)uitvoeren, [Indexeren](https://docs.microsoft.com/rest/api/searchservice/get-indexer)en [Indexeren bijwerken](https://docs.microsoft.com/rest/api/searchservice/update-indexer) in de REST API-verwijzing voor meer informatie over het uitvoeren van indexeerders en het instellen van indexerschema's.

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>Plannen met de .NET SDK

U de planning voor een indexeerder definiëren met behulp van de Azure Cognitive Search .NET SDK. Neem hiervoor de eigenschap **planning** op bij het maken of bijwerken van een indexer.

In het volgende c#-voorbeeld wordt een indexergemaakt met behulp van een vooraf gedefinieerde gegevensbron en -index en wordt de planning ervan eenmaal per dag uitgevoerd vanaf 30 minuten:

```
    Indexer indexer = new Indexer(
        name: "azure-sql-indexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        schedule: new IndexingSchedule(
                        TimeSpan.FromDays(1), 
                        new DateTimeOffset(DateTime.UtcNow.AddMinutes(30))
                    )
        );
    await searchService.Indexers.CreateOrUpdateAsync(indexer);
```
Als de **parameter planning** wordt weggelaten, wordt de indexer slechts één keer uitgevoerd nadat deze is gemaakt.

De parameter **startTime** kan worden ingesteld op een tijdstip in het verleden. In dat geval wordt de eerste uitvoering gepland alsof de indexer continu is uitgevoerd sinds de gegeven **startTime**.

Het schema wordt gedefinieerd met de klasse [IndexeringSschema.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) De constructor **Indexeringsschema** vereist een **intervalparameter** die is opgegeven met een object **TimeSpan.** De kleinste intervalwaarde die is toegestaan is 5 minuten en de grootste 24 uur. De tweede parameter **startTime,** opgegeven als object **DateTimeOffset,** is optioneel.

Met de .NET SDK u indexerbewerkingen beheren met de klasse [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) en de eigenschap [Indexers,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) waarmee methoden worden geïmplementeerd vanuit de **iIndexersOperations-interface.** 

U een indexer op aanvraag op elk gewenst moment uitvoeren met een van de methoden [Run,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run) [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)of [RunWithHttpMessagesAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync)

Zie [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet)voor meer informatie over het maken, bijwerken en uitvoeren van indexers.

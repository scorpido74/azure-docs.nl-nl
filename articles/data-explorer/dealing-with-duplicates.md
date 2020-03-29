---
title: Dubbele gegevens verwerken in Azure Data Explorer
description: In dit onderwerp ziet u verschillende benaderingen voor het omgaan met dubbele gegevens wanneer u Azure Data Explorer gebruikt.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 60ec2b86e0205060f907f1fe39d084dca3aac1cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68608225"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Dubbele gegevens verwerken in Azure Data Explorer

Apparaten die gegevens naar de cloud verzenden, behouden een lokale cache van de gegevens. Afhankelijk van de grootte van de gegevens kan de lokale cache gegevens dagen of zelfs maanden opslaan. U wilt uw analytische databases beschermen tegen defecte apparaten die de gegevens in de cache opnieuw verzenden en gegevensdubbeling veroorzaken in de analytische database. In dit onderwerp worden aanbevolen procedures beschreven voor het verwerken van dubbele gegevens voor dit soort scenario's.

De beste oplossing voor duplicatie van gegevens is het voorkomen van duplicatie. Los het probleem eerder in de gegevenspijplijn zo mogelijk op, waardoor kosten in verband met gegevensverkeer langs de gegevenspijplijn worden opgeslagen en wordt voorkomen dat resources worden besteed aan het omgaan met dubbele gegevens die in het systeem worden opgenomen. Echter, in situaties waar het bronsysteem niet kan worden gewijzigd, zijn er verschillende manieren om te gaan met dit scenario.

## <a name="understand-the-impact-of-duplicate-data"></a>Inzicht in de impact van dubbele gegevens

Controleer het percentage dubbele gegevens. Zodra het percentage dubbele gegevens is ontdekt, u de omvang van het probleem en de bedrijfsimpact analyseren en de juiste oplossing kiezen.

Voorbeeldquery om het percentage dubbele records te identificeren:

```kusto
let _sample = 0.01; // 1% sampling
let _data =
DeviceEventsAll
| where EventDateTime between (datetime('10-01-2018 10:00') .. datetime('10-10-2018 10:00'));
let _totalRecords = toscalar(_data | count);
_data
| where rand()<= _sample
| summarize recordsCount=count() by hash(DeviceId) + hash(EventId) + hash(StationId)  // Use all dimensions that make row unique. Combining hashes can be improved
| summarize duplicateRecords=countif(recordsCount  > 1)
| extend duplicate_percentage = (duplicateRecords / _sample) / _totalRecords  
```

## <a name="solutions-for-handling-duplicate-data"></a>Oplossingen voor het verwerken van dubbele gegevens

### <a name="solution-1-dont-remove-duplicate-data"></a>Oplossing #1: Verwijder geen dubbele gegevens

Begrijp uw bedrijfsvereisten en tolerantie voor dubbele gegevens. Sommige gegevenssets kunnen beheren met een bepaald percentage dubbele gegevens. Als de gedupliceerde gegevens geen grote impact hebben, u de aanwezigheid ervan negeren. Het voordeel van het niet verwijderen van de dubbele gegevens is geen extra overhead voor het opnameproces of queryprestaties.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>Oplossing #2: dubbele rijen verwerken tijdens query

Een andere optie is om de dubbele rijen in de gegevens tijdens de query uit te filteren. De [`arg_max()`](/azure/kusto/query/arg-max-aggfunction) samengevoegde functie kan worden gebruikt om de dubbele records uit te filteren en de laatste record terug te sturen op basis van de tijdstempel (of een andere kolom). Het voordeel van het gebruik van deze methode is snellere opname, omdat deduplicatie optreedt tijdens de querytijd. Bovendien zijn alle records (inclusief duplicaten) beschikbaar voor controle en probleemoplossing. Het nadeel van `arg_max` het gebruik van de functie is de extra querytijd en belasting op de CPU elke keer dat de gegevens worden opgevraagd. Afhankelijk van de hoeveelheid gegevens die worden opgevraagd, kan deze oplossing niet-functioneel of geheugenverbruikend worden en moet u overschakelen naar andere opties.

In het volgende voorbeeld vragen we de laatste record die is ingenomen voor een reeks kolommen die de unieke records bepalen:

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

Deze query kan ook in een functie worden geplaatst in plaats van de tabel rechtstreeks op te vragen:

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>Oplossing #3: Duplicaten filteren tijdens het innameproces

Een andere oplossing is om duplicaten te filteren tijdens het innameproces. Het systeem negeert de dubbele gegevens tijdens inname in Kusto-tabellen. Gegevens worden ineengenomen in een faseringstabel en gekopieerd naar een andere tabel na het verwijderen van dubbele rijen. Het voordeel van deze oplossing is dat de queryprestaties drastisch verbeteren ten opzichte van de vorige oplossing. De nadelen zijn onder meer een langere innametijd en extra kosten voor gegevensopslag. Bovendien werkt deze oplossing alleen als duplicaties niet gelijktijdig worden ingenomen. Als er meerdere gelijktijdige innames zijn die dubbele records bevatten, kunnen alle gegevens worden ingenomen, omdat het deduplicatieproces geen bestaande overeenkomende records in de tabel vindt.    

In het volgende voorbeeld wordt deze methode weergegeven:

1. Maak een andere tabel met hetzelfde schema:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Maak een functie om de dubbele records uit te filteren door de nieuwe records niet te verbinden met de eerder ingenomen records.

    ```kusto
    .create function RemoveDuplicateDeviceEvents()
    {
    DeviceEventsAll
    | join hint.strategy=broadcast kind = anti
        (
        DeviceEventsUnique
        | where EventDateTime > ago(7d)   // filter the data for certain time frame
        | limit 1000000   //set some limitations (few million records) to avoid choking-up the system during outage recovery

        ) on DeviceId, EventId, StationId
    }
    ```

    > [!NOTE]
    > Joins zijn CPU-gebonden bewerkingen en voegen een extra belasting toe aan het systeem.

1. [Updatebeleid](/azure/kusto/management/update-policy) op `DeviceEventsUnique` tafel instellen. Het updatebeleid wordt geactiveerd wanneer er `DeviceEventsAll` nieuwe gegevens in de tabel worden opgenomen. De Kusto-motor voert de functie automatisch uit als er nieuwe [hoeveelheden](/azure/kusto/management/extents-overview) worden gecreëerd. De verwerking is beperkt tot de nieuw gemaakte gegevens. Met de volgende opdracht wordt`DeviceEventsAll`de brontabel ( ), de doeltabel ( )`DeviceEventsUnique`en de functie `RemoveDuplicatesDeviceEvents` samen gevoegt om het updatebeleid te maken.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > Updatebeleid verlengt de duur van inname omdat de gegevens worden `DeviceEventsAll` gefilterd tijdens `DeviceEventsUnique` inname en vervolgens twee keer worden ingenomen (naar de tabel en naar de tabel).

1. (Optioneel) Stel een lagere gegevensbewaar op de `DeviceEventsAll` tabel in om te voorkomen dat kopieën van de gegevens worden opgeslagen. Kies het aantal dagen, afhankelijk van het gegevensvolume en de tijdsduur die u wilt bewaren voor het oplossen van problemen. U deze `0d` instellen op dagenbehoud om COGS op te slaan en de prestaties te verbeteren, omdat de gegevens niet worden geüpload naar opslag.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Samenvatting

Duplicatie van gegevens kan op meerdere manieren worden verwerkt. Evalueer de opties zorgvuldig, rekening houdend met prijs en prestaties, om de juiste methode voor uw bedrijf te bepalen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Query's schrijven voor Azure Data Explorer](write-queries.md)

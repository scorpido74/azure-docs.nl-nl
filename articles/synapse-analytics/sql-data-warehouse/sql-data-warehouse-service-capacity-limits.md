---
title: Capaciteitslimieten - Azure Synapse Analytics (voorheen SQL DW)
description: Maximaal toegestane waarden voor verschillende onderdelen van de Synapse SQL-groep in Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 2/19/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: fbdf0fda51ae35fac4f3f8ae45bfcd788fc406ae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414009"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-capacity-limits"></a>Capaciteitslimieten azure Synapse Analytics (voorheen SQL DW)

Maximaal toegestane waarden voor verschillende onderdelen van Azure Synapse.

## <a name="workload-management"></a>Werklastbeheer

| Categorie | Beschrijving | Maximum |
|:--- |:--- |:--- |
| [Gegevensmagazijneenheden (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Max. | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Gegevensmagazijneenheden (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Standaard DTU per server |54,000<br></br>Standaard heeft elke SQL-server (bijvoorbeeld myserver.database.windows.net) een DTU-quotum van 54.000, waarmee ze tot DW5000c kunnen. Dit quotum is gewoon een veiligheidsbeperking. U uw quotum verhogen door [een ondersteuningsticket te maken](sql-data-warehouse-get-started-create-support-ticket.md) en *Quota* te selecteren als aanvraagtype.  Om uw DTU-behoeften te berekenen, vermenigvuldigt u de 7,5 met de totale Benodigde DWU of vermenigvuldigt u 9,5 met de totale cDWU die nodig is. Bijvoorbeeld:<br></br>DW6000 x 7,5 = 45.000 DTU's<br></br>DW5000c x 9,5 = 47.500 DTU's.<br></br>U uw huidige DTU-verbruik bekijken via de SQL-serveroptie in de portal. Zowel onderbroken als niet-onderbroken databases tellen mee voor het DTU-quotum. |
| Databaseverbinding |Maximale gelijktijdige open sessies |1024<br/><br/>Het aantal gelijktijdige open sessies is afhankelijk van de geselecteerde DWU. DWU600c en hoger ondersteunen maximaal 1024 open sessies. DWU500c en lager ondersteunen een maximale gelijktijdige open sessielimiet van 512. Opmerking, er zijn beperkingen voor het aantal query's dat gelijktijdig kan worden uitgevoerd. Wanneer de gelijktijdigheidslimiet wordt overschreden, gaat de aanvraag naar een interne wachtrij waar deze wacht om te worden verwerkt. |
| Databaseverbinding |Maximaal geheugen voor voorbereide verklaringen |20 MB |
| [Werklastbeheer](resource-classes-for-workload-management.md) |Maximale gelijktijdige query's |128<br/><br/>  Er worden maximaal 128 gelijktijdige query's uitgevoerd en de resterende query's worden in de wachtrij geplaatst.<br/><br/>Het aantal gelijktijdige query's kan afnemen wanneer gebruikers worden toegewezen aan hogere resourceklassen of wanneer de instelling [voor gegevensmagazijnen](memory-concurrency-limits.md) wordt verlaagd. Sommige query's, zoals DMV-query's, mogen altijd worden uitgevoerd en hebben geen invloed op de gelijktijdige querylimiet. Zie het artikel over de [maximumwaarden van gelijktijdig query's](memory-concurrency-limits.md) voor meer informatie over gelijktijdige query-uitvoering. |
| [Tempdb](sql-data-warehouse-tables-temporary.md) |Maximale GB |399 GB per DW100c. Daarom is tempdb bij DWU1000c zo groot als 3,99 TB. |
||||

## <a name="database-objects"></a>Databaseobjecten

| Categorie | Beschrijving | Maximum |
|:--- |:--- |:--- |
| Database |Maximale grootte | Gen1: 240 TB gecomprimeerd op schijf. Deze ruimte is onafhankelijk van tempdb of log ruimte, en daarom is deze ruimte gewijd aan permanente tafels.  De compressie van het clusterarchief wordt geschat op 5x.  Met deze compressie kan de database worden uitgebreid tot ongeveer 1 PB wanneer alle tabellen zijn geclusterd columnstore (het standaardtabeltype). <br/><br/> Gen2: Onbeperkte opslag voor kolomarchieftabellen.  Rowstore gedeelte van de database is nog steeds beperkt tot 240 TB gecomprimeerd op schijf. |
| Tabel |Maximale grootte |Onbeperkte grootte voor kolomarchieftabellen. <br>60 TB voor rowstore-tabellen die op schijf zijn gecomprimeerd. |
| Tabel |Tabellen per database | 100.000 |
| Tabel |Kolommen per tabel |1024 kolommen |
| Tabel |Bytes per kolom |Afhankelijk van [kolomgegevenstype](sql-data-warehouse-tables-data-types.md). Limit is 8000 voor char-gegevenstypen, 4000 voor nvarchar of 2 GB voor MAX-gegevenstypen. |
| Tabel |Bytes per rij, gedefinieerde grootte |8060 bytes<br/><br/>Het aantal bytes per rij wordt op dezelfde manier berekend als voor SQL Server met paginacompressie. Net als SQL Server wordt row-overflow-opslag ondersteund, waardoor **kolommen met variabele lengte** buiten de rij kunnen worden geduwd. Wanneer rijen met variabele lengte buiten de rij worden geduwd, wordt slechts 24-byteroot opgeslagen in de hoofdrecord. Zie [Row-Overflow Data van meer dan 8 KB](https://msdn.microsoft.com/library/ms186981.aspx)voor meer informatie. |
| Tabel |Partities per tabel |15.000<br/><br/>Voor hoge prestaties raden we u aan het aantal partities dat u nodig hebt te minimaliseren terwijl u toch uw bedrijfsvereisten ondersteunt. Naarmate het aantal partities toeneemt, neemt de overhead voor DDL-bewerkingen (Data Definition Language) en Data Manipulation Language (DML) toe en zorgt deze voor tragere prestaties. |
| Tabel |Tekens per grenswaarde van partitie. |4000 |
| Index |Niet-geclusterde indexen per tabel. |50<br/><br/>Geldt alleen voor rowstore-tabellen. |
| Index |Geclusterde indexen per tabel. |1<br><br/>Is van toepassing op zowel rowstore- als kolomarchieftabellen. |
| Index |Indexsleutelgrootte. |900 bytes.<br/><br/>Geldt alleen voor rowstore-indexen.<br/><br/>Indexen op varchar-kolommen met een maximale grootte van meer dan 900 bytes kunnen worden gemaakt als de bestaande gegevens in de kolommen niet meer dan 900 bytes bedragen wanneer de index wordt gemaakt. Latere acties invoegen of BIJWERKEN op de kolommen die ervoor zorgen dat de totale grootte meer dan 900 bytes bedraagt, mislukken echter. |
| Index |Belangrijke kolommen per index. |16<br/><br/>Geldt alleen voor rowstore-indexen. Geclusterde kolomarchiefindexen bevatten alle kolommen. |
| statistieken |Grootte van de gecombineerde kolomwaarden. |900 bytes. |
| statistieken |Kolommen per object statistiek. |32 |
| statistieken |Statistieken die zijn gemaakt op kolommen per tabel. |30,000 |
| Opgeslagen procedures |Maximale nestniveaus. |8 |
| Weergave |Kolommen per weergave |1,024 |
||||

## <a name="loads"></a>Ladingen

| Categorie | Beschrijving | Maximum |
|:--- |:--- |:--- |
| Polybase-belastingen |MB per rij |1<br/><br/>Polybase laadt rijen die kleiner zijn dan 1 MB. Het laden van LOB-gegevenstypen in tabellen met een Clustered Columnstore Index (CCI) wordt niet ondersteund.<br/><br/> |
||||

## <a name="queries"></a>Query's

| Categorie | Beschrijving | Maximum |
|:--- |:--- |:--- |
| Query’s uitvoeren |Wachtrijquery's in de wachtrij op gebruikerstabellen. |1000 |
| Query’s uitvoeren |Gelijktijdige query's over systeemweergaven. |100 |
| Query’s uitvoeren |Query's in de wachtrij voor systeemweergaven |1000 |
| Query’s uitvoeren |Maximale parameters |2098 |
| Batch |Maximumgrootte |65,536*4096 |
| SELECT resultaten |Kolommen per rij |4096<br/><br/>U nooit meer dan 4096 kolommen per rij in het SELECT-resultaat hebben. Er is geen garantie dat je altijd 4096 hebben. Als het queryplan een tijdelijke tabel vereist, kunnen de maximum 1024 kolommen per tabel van toepassing zijn. |
| SELECT |Geneste subquery's |32<br/><br/>U nooit meer dan 32 geneste subquery's in een SELECT-instructie hebben. Er is geen garantie dat je er altijd 32 hebben. Een JOIN kan bijvoorbeeld een subquery in het queryplan introduceren. Het aantal subquery's kan ook worden beperkt door het beschikbare geheugen. |
| SELECT |Kolommen per JOIN |1024 kolommen<br/><br/>U nooit meer dan 1024 kolommen in de JOIN hebben. Er is geen garantie dat je altijd 1024 hebben. Als voor het JOIN-abonnement een tijdelijke tabel met meer kolommen dan het JOIN-resultaat vereist is, geldt de limiet van 1024 voor de tijdelijke tabel. |
| SELECT |Bytes per groep per kolommen. |8060<br/><br/>De kolommen in de GROEP BY-component mogen maximaal 8060 bytes hebben. |
| SELECT |Bytes per volgorde per kolommen |8060 bytes<br/><br/>De kolommen in de ORDER BY-clausule mogen maximaal 8060 bytes hebben |
| Id's per instructie |Aantal naar de referentie-id's verwezen |65,535<br/><br/> Het aantal id's dat kan worden opgenomen in één expressie van een query is beperkt. Als u dit getal overschrijdt, resulteert dit in SQL Server-fout 8632. Zie [Interne fout: een limiet voor expressieservices is bereikt](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a)voor meer informatie . |
| String literals | Aantal tekenreeksliteralen in een instructie | 20.000 <br/><br/>Het aantal tekenreeksconstanten in één expressie van een query is beperkt. Als u dit getal overschrijdt, resulteert dit in SQL Server-fout 8632.|
||||

## <a name="metadata"></a>Metagegevens

| Systeemweergave | Maximale rijen |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10.000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Totaal aantal DMS-werknemers voor de meest recente 1000 SQL-aanvragen. |
| sys.dm_pdw_errors |10.000 |
| sys.dm_pdw_exec_requests |10.000 |
| sys.dm_pdw_exec_sessions |10.000 |
| sys.dm_pdw_request_steps |Totaal aantal stappen voor de meest recente 1000 SQL-aanvragen die zijn opgeslagen in sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10.000 |
| sys.dm_pdw_sql_requests |De meest recente 1000 SQL-aanvragen die zijn opgeslagen in sys.dm_pdw_exec_requests. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie het [spiekblad](cheat-sheet.md)voor aanbevelingen over het gebruik van Azure Synapse .

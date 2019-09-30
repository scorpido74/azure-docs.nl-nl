---
title: Capaciteits limieten-Azure SQL Data Warehouse | Microsoft Docs
description: De maximum waarden die zijn toegestaan voor de verschillende onderdelen van Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/14/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 4443f94df9095da3a7ec0e9694b8089033c8d177
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686426"
---
# <a name="sql-data-warehouse-capacity-limits"></a>Limieten voor SQL Data Warehouse capaciteit
De maximum waarden die zijn toegestaan voor de verschillende onderdelen van Azure SQL Data Warehouse.

## <a name="workload-management"></a>Werklastbeheer
| Category | Description | Maximum |
|:--- |:--- |:--- |
| [Data Warehouse-eenheden (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Maximum aantal DWU voor één SQL Data Warehouse | Gen1 DW6000<br></br>Gen2 DW30000c |
| [Data Warehouse-eenheden (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Standaard-DTU per server |54,000<br></br>Elke SQL-Server (bijvoorbeeld myserver.database.windows.net) heeft standaard een DTU-quotum van 54.000, waardoor Maxi maal 9 DW6000c is toegestaan. Dit quotum is gewoon een veiligheidsbeperking. U kunt het quotum verhogen door [een ondersteunings ticket te maken](sql-data-warehouse-get-started-create-support-ticket.md) en *quotum* als aanvraag type te selecteren.  U kunt uw DTU-behoeften berekenen door de 7,5 te vermenigvuldigen met het totale DWU dat nodig is, of door 9,0 te vermenigvuldigen met het totale aantal cDWU dat nodig is. Bijvoorbeeld:<br></br>DW6000 x 7,5 = 45.000 Dtu's<br></br>DW6000c x 9,0 = 54.000 Dtu's.<br></br>U kunt uw huidige DTU-verbruik weer geven met de optie SQL Server in de portal. Zowel onderbroken als niet-onderbroken databases tellen mee voor het DTU-quotum. |
| Databaseverbinding |Maximum aantal gelijktijdige open sessies |1024<br/><br/>Het aantal gelijktijdige open sessies varieert op basis van de geselecteerde DWU. DWU600c en hoger ondersteunen Maxi maal 1024 geopende sessies. DWU500c en lager ondersteunen een maximum aantal gelijktijdige open sessies van 512. Opmerking: er gelden limieten voor het aantal query's dat gelijktijdig kan worden uitgevoerd. Wanneer de limiet voor gelijktijdigheid wordt overschreden, wordt de aanvraag in een interne wachtrij geplaatst, waarin wordt gewacht om te worden verwerkt. |
| Databaseverbinding |Maxi maal geheugen voor voor bereide instructies |20 MB |
| [Werklastbeheer](resource-classes-for-workload-management.md) |Maximum aantal gelijktijdige query's |128<br/><br/> SQL Data Warehouse kunt Maxi maal 128 gelijktijdige query's en wacht rijen van resterende query's uitvoeren.<br/><br/>Het aantal gelijktijdige query's kan afnemen wanneer gebruikers worden toegewezen aan hogere bron klassen of wanneer SQL Data Warehouse een lagere eenheid voor het [Data Warehouse](memory-and-concurrency-limits.md) hebt ingesteld. Sommige query's, zoals DMV-query's, mogen altijd worden uitgevoerd en hebben geen invloed op de limiet voor gelijktijdige query's. Zie het artikel [gelijktijdigheids limieten](memory-and-concurrency-limits.md#concurrency-maximums) voor meer informatie over het gelijktijdig uitvoeren van query's. |
| [tempdb](sql-data-warehouse-tables-temporary.md) |Maximum GB |399 GB per DW100. Daarom heeft TempDB het formaat 3,99 TB. |

## <a name="database-objects"></a>Databaseobjecten
| Category | Description | Maximum |
|:--- |:--- |:--- |
| Database |Max. grootte | Gen1 240 TB gecomprimeerd op schijf. Deze ruimte is onafhankelijk van tempdb of logboek ruimte en daarom is deze ruimte toegewezen aan permanente tabellen.  Geclusterde column Store-compressie wordt geschat op 5X.  Met deze compressie kan de Data Base worden uitgebreid tot ongeveer 1 PB wanneer alle tabellen zijn geclusterd (het standaard tabel type). <br/><br/> Gen2 240TB voor rowstore en onbeperkte opslag voor column Store-tabellen |
| Tabel |Max. grootte |60 TB gecomprimeerd op schijf |
| Tabel |Tabellen per data base | 100,000 |
| Tabel |Kolommen per tabel |1024 kolommen |
| Tabel |Bytes per kolom |Afhankelijk van het [gegevens type](sql-data-warehouse-tables-data-types.md)van de kolom. De limiet is 8000 voor char-gegevens typen, 4000 voor nvarchar of 2 GB voor de maximale gegevens typen. |
| Tabel |Bytes per rij, gedefinieerde grootte |8060 bytes<br/><br/>Het aantal bytes per rij wordt op dezelfde manier berekend als voor SQL Server met pagina compressie. Net als SQL Server ondersteunt SQL Data Warehouse ondersteuning voor het overschrijden van rijen met **variabele lengte** . Wanneer variabele length-rijen uit de rij worden gepusht, wordt alleen de basis van 24 bytes in de hoofd record opgeslagen. Zie voor meer informatie [rij-overflow gegevens die groter zijn dan 8 KB](https://msdn.microsoft.com/library/ms186981.aspx). |
| Tabel |Partities per tabel |15,000<br/><br/>Voor hoge prestaties raden we u aan het aantal partities dat u nodig hebt, te minimaliseren en toch uw bedrijfs vereisten te ondersteunen. Naarmate het aantal partities groeit, neemt de overhead voor DDL (Data Definition Language) en DML-bewerkingen (data manipulatie Language) toe en leidt dit tot tragere prestaties. |
| Tabel |Tekens per partitie grenswaarde. |4000 |
| Index |Niet-geclusterde indexen per tabel. |50<br/><br/>Is alleen van toepassing op rowstore-tabellen. |
| Index |Geclusterde indexen per tabel. |1<br><br/>Is van toepassing op zowel rowstore-als column Store-tabellen. |
| Index |Grootte van index sleutel. |900 bytes.<br/><br/>Is alleen van toepassing op rowstore-indexen.<br/><br/>Indexen op varchar-kolommen met een maximale grootte van meer dan 900 bytes kunnen worden gemaakt als de bestaande gegevens in de kolommen niet groter zijn dan 900 bytes wanneer de index wordt gemaakt. Later worden acties invoegen of bijwerken op de kolommen die ervoor zorgen dat de totale grootte groter is dan 900 bytes. |
| Index |Sleutel kolommen per index. |16<br/><br/>Is alleen van toepassing op rowstore-indexen. Geclusterde column Store-indexen bevatten alle kolommen. |
| Statistieken |De grootte van de gecombineerde kolom waarden. |900 bytes. |
| Statistieken |Kolommen per statistiek object. |32 |
| Statistieken |Statistieken gemaakt voor kolommen per tabel. |30,000 |
| Opgeslagen procedures |Maximum aantal geneste niveaus. |8 |
| Weergave |Kolommen per weer gave |1,024 |

## <a name="loads"></a>Laden
| Category | Description | Maximum |
|:--- |:--- |:--- |
| Poly base belastingen |MB per rij |1<br/><br/>Poly base laadt rijen die kleiner zijn dan 1 MB. Het laden van LOB-gegevens typen in tabellen met een geclusterde column store-index (CCI) wordt niet ondersteund.<br/><br/> |

## <a name="queries"></a>Query's
| Category | Description | Maximum |
|:--- |:--- |:--- |
| Query’s uitvoeren |Query's in de wachtrij voor gebruikers tabellen. |1000 |
| Query’s uitvoeren |Gelijktijdige query's op systeem weergaven. |100 |
| Query’s uitvoeren |Query's in de wachtrij op systeem weergaven |1000 |
| Query’s uitvoeren |Maximum aantal para meters |2098 |
| Batch |Maximum grootte |65,536*4096 |
| Resultaten selecteren |Kolommen per rij |4096<br/><br/>U kunt nooit meer dan 4096 kolommen per rij in het selectie resultaat hebben. Er is geen garantie dat u altijd 4096 kunt hebben. Als het query plan een tijdelijke tabel vereist, kunnen de 1024-kolommen per tabel maximum worden toegepast. |
| SELECT |Geneste subquery's |32<br/><br/>U kunt nooit meer dan 32 geneste subquery's in een SELECT-instructie hebben. Er is geen garantie dat u altijd 32 kunt hebben. Met een koppeling kan bijvoorbeeld een subquery worden geïntroduceerd in het query plan. Het aantal subquery's kan ook worden beperkt door het beschik bare geheugen. |
| SELECT |Kolommen per samen voeging |1024 kolommen<br/><br/>In de koppeling kunt u nooit meer dan 1024 kolommen hebben. Er is geen garantie dat u altijd 1024 kunt hebben. Als voor het deelname plan een tijdelijke tabel met meer kolommen is vereist dan het resultaat van de deelname, geldt de limiet van 1024 voor de tijdelijke tabel. |
| SELECT |Bytes per kolom op basis van een groep. |8060<br/><br/>De kolommen in de GROUP BY-component kunnen Maxi maal 8060 bytes bevatten. |
| SELECT |Aantal bytes per GESORTEERDe kolom |8060 bytes<br/><br/>De kolommen in de ORDER BY-component kunnen Maxi maal 8060 bytes bevatten |
| Id's per instructie |Aantal id's waarnaar wordt verwezen |65,535<br/><br/>SQL Data Warehouse beperkt het aantal id's dat kan worden opgenomen in één expressie van een query. Het overschrijden van dit getal resulteert in SQL Server fout 8632. Zie [Internal-fout voor meer informatie: Er is een limiet voor de expressie Services bereikt. @ no__t-0. |
| Letterlijke teken reeksen | Aantal letterlijke teken reeksen in een instructie | 20,000 <br/><br/>SQL Data Warehouse beperkt het aantal teken reeks constanten in één expressie van een query. Het overschrijden van dit getal resulteert in SQL Server fout 8632.|

## <a name="metadata"></a>Metagegevens
| Systeem weergave | Maximum aantal rijen |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10.000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Totaal aantal DMS-werk rollen voor de meest recente 1000 SQL-aanvragen. |
| sys.dm_pdw_errors |10.000 |
| sys.dm_pdw_exec_requests |10.000 |
| sys.dm_pdw_exec_sessions |10.000 |
| sys.dm_pdw_request_steps |Totaal aantal stappen voor de meest recente 1000 SQL-aanvragen die zijn opgeslagen in sys. DM _pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10.000 |
| sys.dm_pdw_sql_requests |De meest recente 1000 SQL-aanvragen die zijn opgeslagen in sys. DM _pdw_exec_requests. |

## <a name="next-steps"></a>Volgende stappen
Zie het [Cheat-blad](cheat-sheet.md)voor aanbevelingen over het gebruik van SQL Data Warehouse.

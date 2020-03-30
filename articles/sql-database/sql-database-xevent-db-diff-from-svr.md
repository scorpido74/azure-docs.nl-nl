---
title: Uitgebreide gebeurtenissen
description: Beschrijft extended events (XEvents) in Azure SQL Database en hoe gebeurtenissessies enigszins verschillen van gebeurtenissessies in Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: cb4eb4474ad074a3e69dc146c97b48d54343595b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213948"
---
# <a name="extended-events-in-sql-database"></a>Uitgebreide gebeurtenissen in SQL-database
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

In dit onderwerp wordt uitgelegd hoe de implementatie van uitgebreide gebeurtenissen in Azure SQL Database iets anders is dan uitgebreide gebeurtenissen in Microsoft SQL Server.

- SQL Database V12 kreeg de uitgebreide gebeurtenissen functie in de tweede helft van kalender 2015.
- SQL Server heeft sinds 2008 uitgebreide gebeurtenissen.
- De functieset uitgebreide gebeurtenissen op SQL Database is een robuuste subset van de functies op SQL Server.

*XEvents* is een informele bijnaam die soms wordt gebruikt voor 'extended events' in blogs en andere informele locaties.

Aanvullende informatie over uitgebreide gebeurtenissen voor Azure SQL Database en Microsoft SQL Server is beschikbaar op:

- [Snel aan de slag: uitgebreide gebeurtenissen in SQL Server](https://msdn.microsoft.com/library/mt733217.aspx)
- [Uitgebreide gebeurtenissen](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Vereisten

Dit onderwerp gaat ervan uit dat je al enige kennis van:

- [Azure SQL Database-service](https://azure.microsoft.com/services/sql-database/).
- [Uitgebreide gebeurtenissen](https://msdn.microsoft.com/library/bb630282.aspx) in Microsoft SQL Server.

- Het grootste deel van onze documentatie over uitgebreide gebeurtenissen is van toepassing op zowel SQL Server als SQL Database.

Voorafgaande blootstelling aan de volgende items is handig bij het kiezen van het gebeurtenisbestand als [doel:](#AzureXEventsTargets)

- [Azure Storage-service](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Azure PowerShell gebruiken met Azure Storage](../storage/common/storage-powershell-guide-full.md) - Biedt uitgebreide informatie over PowerShell en de Azure Storage-service.

## <a name="code-samples"></a>Codevoorbeelden

Gerelateerde onderwerpen bieden twee codevoorbeelden:


- [RingBuffer-doelcode voor uitgebreide gebeurtenissen in SQL-database](sql-database-xevent-code-ring-buffer.md)
    - Kort eenvoudig Transact-SQL-script.
    - We benadrukken in het codevoorbeeldonderwerp dat wanneer u klaar bent met een Ring Buffer-doel, u de bronnen moet vrijgeven door een wijzigingsinstructie uit `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` te voeren. Later u een ander `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`exemplaar van Ringbuffer toevoegen door.


- [Doelcode gebeurtenisbestand voor uitgebreide gebeurtenissen in SQL-database](sql-database-xevent-code-event-file.md)
    - Fase 1 is PowerShell om een Azure Storage-container te maken.
    - Fase 2 is Transact-SQL dat de Azure Storage-container gebruikt.

## <a name="transact-sql-differences"></a>Transact-SQL-verschillen


- Wanneer u de opdracht [GEBEURTENISSESSIE MAKEN](https://msdn.microsoft.com/library/bb677289.aspx) uitvoert op SQL Server, gebruikt u de **on-server-component.** Maar op SQL Database gebruikt u in plaats daarvan de **ON DATABASE-component.**


- De **ON DATABASE-clausule** is ook van toepassing op de [commando's ALTER EVENT SESSION](https://msdn.microsoft.com/library/bb630368.aspx) en DROP EVENT [SESSION](https://msdn.microsoft.com/library/bb630257.aspx) Transact-SQL.


- Een aanbevolen toepassing is het opnemen van de optie voor een gebeurtenissessie van **STARTUP_STATE = AAN** in uw **GEBEURTENISSESSIE** MAKEN of **GEBEURTENISSESSIE-instructies WIJZIGEN.**
    - De **waarde = ON** ondersteunt een automatische herstart na een herconfiguratie van de logische database als gevolg van een failover.

## <a name="new-catalog-views"></a>Nieuwe catalogusweergaven

De functie uitgebreide gebeurtenissen wordt ondersteund door verschillende [catalogusweergaven.](https://msdn.microsoft.com/library/ms174365.aspx) Catalogusweergaven vertellen u over *metagegevens of definities* van door gebruikers gemaakte gebeurtenissessies in de huidige database. De weergaven geven geen informatie terug over gevallen van actieve gebeurtenissessies.

| Naam van<br/>catalogusweergave | Beschrijving |
|:--- |:--- |
| **sys.database_event_session_actions** |Geeft als resultaat een rij voor elke actie voor elke gebeurtenis van een gebeurtenissessie. |
| **sys.database_event_session_events** |Retourneert een rij voor elke gebeurtenis in een gebeurtenissessie. |
| **sys.database_event_session_fields** |Retourneert een rij voor elke kolom die kan worden aangepast en die expliciet is ingesteld op gebeurtenissen en doelen. |
| **sys.database_event_session_targets** |Retourneert een rij voor elk gebeurtenisdoel voor een gebeurtenissessie. |
| **sys.database_event_sessions** |Retourneert een rij voor elke gebeurtenissessie in de SQL Database-database. |

In Microsoft SQL Server bevatten vergelijkbare catalogusweergaven namen die *.server\_ * bevatten in plaats van *\_.database.* Het naampatroon is als **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvs"></a>Nieuwe dynamische managementweergaven [(DMVs)](https://msdn.microsoft.com/library/ms188754.aspx)

Azure SQL Database heeft [dynamische beheerweergaven (DMVs)](https://msdn.microsoft.com/library/bb677293.aspx) die uitgebreide gebeurtenissen ondersteunen. DMVs vertellen u over *actieve* eventsessies.

| Naam van DMV | Beschrijving |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Geeft als resultaat informatie over acties voor gebeurtenissessies. |
| **sys.dm_xe_database_session_events** |Geeft als resultaat informatie over sessiegebeurtenissen. |
| **sys.dm_xe_database_session_object_columns** |Toont de configuratiewaarden voor objecten die aan een sessie zijn gebonden. |
| **sys.dm_xe_database_session_targets** |Retourneert informatie over sessiedoelen. |
| **sys.dm_xe_database_sessions** |Retourneert een rij voor elke gebeurtenissessie die is verbonden aan de huidige database. |

In Microsoft SQL Server worden vergelijkbare catalogusweergaven benoemd zonder het * \_databasegedeelte* van de naam, zoals:

- **sys.dm_xe_sessions**, in plaats van naam<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>DMVs gemeenschappelijk voor beide
Voor uitgebreide gebeurtenissen zijn er extra DMVs die gemeenschappelijk zijn voor zowel Azure SQL Database als Microsoft SQL Server:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

  <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>De beschikbare uitgebreide gebeurtenissen, acties en doelen zoeken

U een eenvoudige SQL **SELECT** uitvoeren om een lijst met de beschikbare gebeurtenissen, acties en target te verkrijgen.

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```


<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-sql-database-event-sessions"></a>Doelen voor uw SQL Database-gebeurtenissessies

Hier volgen doelen die resultaten van uw gebeurtenissessies in SQL Database kunnen vastleggen:

- [Ringbufferdoel](https://msdn.microsoft.com/library/ff878182.aspx) - Houdt kort gebeurtenisgegevens in het geheugen.
- [Gebeurtenistellerdoel](https://msdn.microsoft.com/library/ff878025.aspx) - Telt alle gebeurtenissen die plaatsvinden tijdens een uitgebreide sessie.
- [Gebeurtenisbestandsdoel](https://msdn.microsoft.com/library/ff878115.aspx) - Hiermee worden volledige buffers geschreven voor een Azure Storage-container.

De [Api voor gebeurtenistracering voor Windows (ETW)](https://msdn.microsoft.com/library/ms751538.aspx) is niet beschikbaar voor uitgebreide gebeurtenissen in SQL Database.

## <a name="restrictions"></a>Beperkingen

Er zijn een paar beveiligingsgerelateerde verschillen die passen bij de cloudomgeving van SQL Database:

- Uitgebreide evenementen zijn gebaseerd op het isolatiemodel met één tenant. Een gebeurtenissessie in de ene database heeft geen toegang tot gegevens of gebeurtenissen uit een andere database.
- U geen instructie **GEBEURTENISMAKEN geven** in de context van de **hoofddatabase.**

## <a name="permission-model"></a>Machtigingsmodel

U moet de machtiging **Controle** in de database hebben om een instructie GEBEURTENISSESSIE MAKEN uit te **geven.** De eigenaar van de database (dbo) heeft **Control-toestemming.**

### <a name="storage-container-authorizations"></a>Vergunningen voor opslagcontainers

Het SAS-token dat u genereert voor uw Azure Storage-container, moet **rwl** opgeven voor de machtigingen. De **rwl-waarde** biedt de volgende machtigingen:

- Lezen
- Schrijven
- Lijst

## <a name="performance-considerations"></a>Prestatieoverwegingen

Er zijn scenario's waarin intensief gebruik van uitgebreide gebeurtenissen meer actief geheugen kan accumuleren dan gezond is voor het totale systeem. Daarom stelt het Azure SQL Database-systeem dynamisch limieten in en past het de hoeveelheid actief geheugen aan die kan worden verzameld door een gebeurtenissessie. Veel factoren gaan in de dynamische berekening.

Als u een foutbericht ontvangt waarin staat dat een geheugenmaximum is afgedwongen, u enkele corrigerende acties uitvoeren:

- Voer minder gelijktijdige gebeurtenissessies uit.
- Verlaag via uw **CREATE-** en **ALTER-instructies** voor gebeurtenissessies de hoeveelheid geheugen die u opgeeft in de **\_MAX-geheugenclausule.**

### <a name="network-latency"></a>Netwerklatentie

Het doel **gebeurtenisbestand** kan netwerklatentie of -fouten ondervinden terwijl gegevens in Azure Storage-blobs blijven bestaan. Andere gebeurtenissen in SQL Database kunnen worden vertraagd terwijl ze wachten tot de netwerkcommunicatie is voltooid. Deze vertraging kan uw werklast vertragen.

- Als u dit prestatierisico wilt beperken, hoeft u niet de **EVENT_RETENTION_MODE** optie in te stellen om te **NO_EVENT_LOSS** in de definities van uw gebeurtenissessie.

## <a name="related-links"></a>Verwante koppelingen

- [Azure PowerShell gebruiken met Azure Storage](../storage/common/storage-powershell-guide-full.md).
- [Azure Storage-cmdlets](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Azure PowerShell gebruiken met Azure Storage](../storage/common/storage-powershell-guide-full.md) - Biedt uitgebreide informatie over PowerShell en de Azure Storage-service.
- [Blob-opslag gebruiken vanuit .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [GEBEURTENISSESSIE MAKEN (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Jonathan Kehayias' blogposts over uitgebreide gebeurtenissen in Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- De webpagina Azure *Service Updates,* verkleind door parameter tot Azure SQL Database:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Andere code voorbeeldonderwerpen voor uitgebreide gebeurtenissen zijn beschikbaar op de volgende links. U moet echter routinematig elk voorbeeld controleren om te zien of het voorbeeld is gericht op Microsoft SQL Server versus Azure SQL Database. Vervolgens u bepalen of kleine wijzigingen nodig zijn om het voorbeeld uit te voeren.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->

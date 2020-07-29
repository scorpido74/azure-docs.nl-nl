---
title: Uitgebreide gebeurtenissen
description: Hierin worden uitgebreide gebeurtenissen (XEvents) in Azure SQL Database beschreven en wordt uitgelegd hoe gebeurtenis sessies enigszins afwijken van gebeurtenis sessies in Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: 7f0f50de3f74f0e8040118035e28b3e905ed5616
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84046955"
---
# <a name="extended-events-in-azure-sql-database"></a>Uitgebreide gebeurtenissen in Azure SQL Database 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

De functieset met uitgebreide gebeurtenissen in Azure SQL Database is een robuuste subset van de functies op SQL Server en Azure SQL Managed instance.

*XEvents* is een informele bijnaam die soms wordt gebruikt voor ' Extended Events ' in blogs en andere informele locaties.

Meer informatie over uitgebreide gebeurtenissen vindt u op:

- [Quick Start: uitgebreide gebeurtenissen in SQL Server](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
- [Uitgebreide gebeurtenissen](/sql/relational-databases/extended-events/extended-events)

## <a name="prerequisites"></a>Vereisten

In dit onderwerp wordt ervan uitgegaan dat u al enige kennis hebt van:

- [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Uitgebreide gebeurtenissen](/sql/relational-databases/extended-events/extended-events)

- Het meren deel van onze documentatie over Extended Events is van toepassing op SQL Server, Azure SQL Database en Azure SQL Managed instance.

Voorafgaande bloot stelling aan de volgende items is handig bij het kiezen van het gebeurtenis bestand als [doel](#AzureXEventsTargets):

- [Azure Storage-service](https://azure.microsoft.com/services/storage/)

- [Azure PowerShell met Azure Storage](/powershell/module/az.storage/)

## <a name="code-samples"></a>Codevoorbeelden

Verwante onderwerpen bieden twee voor beelden van code:

- [Doel code ring buffer voor uitgebreide gebeurtenissen in Azure SQL Database](xevent-code-ring-buffer.md)

  - Kort eenvoudig Transact-SQL-script.
  - We benadrukken in het onderwerp code voorbeeld. Als u klaar bent met een ring buffer doel, moet u de resources vrijgeven door een instructie ALTER-drop uit te voeren `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` . Later kunt u een andere instantie van ring buffer toevoegen door `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...` .

- [Doel code van gebeurtenis bestand voor uitgebreide gebeurtenissen in Azure SQL Database](xevent-code-event-file.md)

  - Fase 1 is Power shell om een Azure Storage-container te maken.
  - Fase 2 is Transact-SQL die gebruikmaakt van de Azure Storage-container.

## <a name="transact-sql-differences"></a>Verschillen in Transact-SQL

- Wanneer u de opdracht [gebeurtenis sessie maken](/sql/t-sql/statements/create-event-session-transact-sql) op SQL Server uitvoert, gebruikt u de component **on server** . Maar op Azure SQL Database u in plaats daarvan de component **on-data base** gebruiken.
- De component **on data base** is ook van toepassing op de opdrachten [ALTER Event Session](/sql/t-sql/statements/alter-event-session-transact-sql) en [Drop Event Session](/sql/t-sql/statements/drop-event-session-transact-sql) Transact-SQL.

- Een best practice is het toevoegen van de optie voor gebeurtenis sessies van **STARTUP_STATE = aan** in uw **gebeurtenis sessie maken** of instructies voor **gebeurtenis sessies wijzigen** .
  - De **= on** -waarde ondersteunt een automatische herstart na een herconfiguratie van de logische data base vanwege een failover.

## <a name="new-catalog-views"></a>Nieuwe catalogus weergaven

De functie Extended Events wordt ondersteund door verschillende [catalogus weergaven](https://msdn.microsoft.com/library/ms174365.aspx). Catalogus weergaven geven informatie over *meta gegevens of definities* van door gebruikers gemaakte gebeurtenis sessies in de huidige data base. De weer gaven retour neren geen informatie over exemplaren van actieve gebeurtenis sessies.

| Naam van<br/>Catalogus weergave | Description |
|:--- |:--- |
| **sys. database_event_session_actions** |Retourneert een rij voor elke actie op elke gebeurtenis van een gebeurtenis sessie. |
| **sys. database_event_session_events** |Retourneert een rij voor elke gebeurtenis in een gebeurtenis sessie. |
| **sys. database_event_session_fields** |Retourneert een rij voor elke kolom die kan worden aangepast en die expliciet is ingesteld op gebeurtenissen en doelen. |
| **sys. database_event_session_targets** |Retourneert een rij voor elk gebeurtenis doel voor een gebeurtenis sessie. |
| **sys. database_event_sessions** |Retourneert een rij voor elke gebeurtenis sessie in de data base. |

In Microsoft SQL Server hebben vergelijk bare catalogus weergaven namen die *. server \_ * bevatten in plaats van *. \_ Data Base*. Het naam patroon is als **sys. server_event_%**.

## <a name="new-dynamic-management-views-dmvs"></a>Nieuwe dynamische beheer weergaven [(dmv's)](https://msdn.microsoft.com/library/ms188754.aspx)

Azure SQL Database heeft [dynamische beheer weergaven (dmv's)](https://msdn.microsoft.com/library/bb677293.aspx) die ondersteuning bieden voor uitgebreide gebeurtenissen. Dmv's vertelt u over *actieve* gebeurtenis sessies.

| Naam van DMV | Description |
|:--- |:--- |
| **sys. dm_xe_database_session_event_actions** |Hiermee wordt informatie over gebeurtenis sessie acties geretourneerd. |
| **sys. dm_xe_database_session_events** |Retourneert informatie over sessie gebeurtenissen. |
| **sys. dm_xe_database_session_object_columns** |Toont de configuratie waarden voor objecten die aan een sessie zijn gebonden. |
| **sys. dm_xe_database_session_targets** |Retourneert informatie over sessie doelen. |
| **sys. dm_xe_database_sessions** |Retourneert een rij voor elke gebeurtenis sessie die binnen het bereik van de huidige data base valt. |

In Microsoft SQL Server worden vergelijk bare catalogus weergaven benoemd zonder het * \_ Data Base* -gedeelte van de naam, zoals:

- **sys. dm_xe_sessions**, in plaats van naam<br/>**sys. dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>Gemeen schappelijk Dmv's voor beide

Voor uitgebreide gebeurtenissen zijn er extra Dmv's die gemeen schappelijk zijn voor Azure SQL Database, Azure SQL Managed instance en Microsoft SQL Server:

- **sys. dm_xe_map_values**
- **sys. dm_xe_object_columns**
- **sys. dm_xe_objects**
- **sys. dm_xe_packages**

<a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>De beschik bare uitgebreide gebeurtenissen, acties en doelen zoeken

U kunt een eenvoudige SQL- **selectie** uitvoeren om een lijst met de beschik bare gebeurtenissen, acties en doel items te verkrijgen.

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

## <a name="targets-for-your-azure-sql-database-event-sessions"></a>Doelen voor uw Azure SQL Database-gebeurtenis sessies

Hier vindt u doelen die resultaten kunnen vastleggen vanuit uw gebeurtenis sessies op Azure SQL Database:

- [Ring buffer doel](https://msdn.microsoft.com/library/ff878182.aspx) -bevat kort gebeurtenis gegevens in het geheugen.
- [Doel van gebeurtenis teller](https://msdn.microsoft.com/library/ff878025.aspx) : telt alle gebeurtenissen die optreden tijdens een Extended Events-sessie.
- [Gebeurtenis bestand doel](https://msdn.microsoft.com/library/ff878115.aspx) : Hiermee worden volledige buffers geschreven naar een Azure storage-container.

De API voor [Event Tracing for Windows (etw)](https://msdn.microsoft.com/library/ms751538.aspx) is niet beschikbaar voor uitgebreide gebeurtenissen op Azure SQL database.

## <a name="restrictions"></a>Beperkingen

Er zijn een aantal beveiligings verschillen befitting de cloud omgeving van Azure SQL Database:

- Uitgebreide gebeurtenissen zijn gebaseerd op het isolatie model met één Tenant. Een gebeurtenis sessie in de ene data base heeft geen toegang tot gegevens of gebeurtenissen vanuit een andere data base.
- U kunt geen instructie **create event Session** uitgeven in de context van de **hoofd** database.

## <a name="permission-model"></a>Machtigings model

U moet de machtiging **beheer** hebben voor de data base om een instructie **create event Session** te kunnen geven. De data base-eigenaar (dbo) heeft machtiging voor **beheer** .

### <a name="storage-container-authorizations"></a>Autorisaties voor opslag container

Het SAS-token dat u voor de Azure Storage-container hebt gegenereerd, moet **RWL** voor de machtigingen opgeven. De waarde **RWL** biedt de volgende machtigingen:

- Lezen
- Schrijven
- Lijst

## <a name="performance-considerations"></a>Prestatieoverwegingen

Er zijn scenario's waarin intensief gebruik van uitgebreide gebeurtenissen meer actief geheugen kan oplopen dan in orde is voor het hele systeem. Daarom Azure SQL Database dynamisch limieten ingesteld en aangepast voor de hoeveelheid actief geheugen die kan worden geaccumuleerd door een gebeurtenis sessie. Veel factoren gaan in de dynamische berekening.

Als er een fout bericht wordt weer gegeven met de melding dat er een geheugen maximum is afgedwongen, kunt u het volgende doen:

- Voer minder gelijktijdige gebeurtenis sessies uit.
- Via uw **Create** -en **ALTER** -instructies voor gebeurtenis sessies vermindert u de hoeveelheid geheugen die u opgeeft in de component **Max \_ Memory** .

### <a name="network-latency"></a>Netwerklatentie

Het **gebeurtenis bestand** doel kan netwerk latentie of storingen ondervinden tijdens het persistent maken van gegevens naar Azure Storage blobs. Andere gebeurtenissen in Azure SQL Database worden mogelijk vertraagd wanneer er wordt gewacht tot de netwerk communicatie is voltooid. Deze vertraging kan uw werk belasting vertragen.

- Als u dit prestatie risico wilt beperken, moet u de **EVENT_RETENTION_MODE** optie voor het **NO_EVENT_LOSS** in de definities van de gebeurtenis sessie niet instellen.

## <a name="related-links"></a>Verwante koppelingen

- [Azure PowerShell gebruiken met Azure Storage](/powershell/module/az.storage/).
- [Azure Storage-cmdlets](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Azure PowerShell gebruiken met Azure Storage](/powershell/module/az.storage/)
- [Blob-opslag gebruiken met .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [GEBEURTENIS sessie maken (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Blog berichten van Jonathan Kehayias over Extended Events in Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)
- De webpagina Azure- *service-updates* , beperkt door de para meter voor Azure SQL database:
  - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->

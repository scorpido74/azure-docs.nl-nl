---
title: Back-up maken en herstellen - momentopnamen, geo-redundant
description: Meer informatie over hoe back-up en herstel werkt in Azure Synapse Analytics SQL-pool. Gebruik back-ups om uw gegevensmagazijn te herstellen naar een herstelpunt in de primaire regio. Gebruik georedundante back-ups om te herstellen naar een andere geografische regio.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/04/2020
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: 1d82c7c22bb5aeb2740884b0d7ede4a4d8f07f86
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631222"
---
# <a name="backup-and-restore-in-azure-synapse-sql-pool"></a>Back-upmaken en herstellen in Azure Synapse SQL-pool

Meer informatie over het gebruik van back-ups en herstelin Azure Synapse SQL-pool. Gebruik SQL-groepherstelpunten om uw gegevensmagazijn te herstellen of te kopiëren naar een eerdere status in het primaire gebied. Gebruik georedundante back-ups van datawarehouses om te herstellen naar een andere geografische regio.

## <a name="what-is-a-data-warehouse-snapshot"></a>Wat is een momentopname van een gegevensmagazijn

Een *momentopname voor een gegevensmagazijn* maakt een herstelpunt dat u gebruiken om uw gegevensmagazijn te herstellen of te kopiëren naar een eerdere status.  Aangezien SQL-pool een gedistribueerd systeem is, bestaat een momentopname van een gegevensmagazijn uit veel bestanden die zich in Azure-opslag bevinden. Snapshots leggen incrementele wijzigingen vast ten opzichte van de gegevens die zijn opgeslagen in uw gegevensmagazijn.

Een *gegevensmagazijnherstel* is een nieuw gegevensmagazijn dat wordt gemaakt vanuit een herstelpunt van een bestaand of verwijderd gegevensmagazijn. Het herstellen van uw gegevensmagazijn is een essentieel onderdeel van elke bedrijfscontinuïteits- en disaster recovery-strategie, omdat het uw gegevens opnieuw maakt na onbedoelde beschadiging of verwijdering. Data warehouse is ook een krachtig mechanisme om kopieën van uw data warehouse te maken voor test- of ontwikkelingsdoeleinden.  De herstelpercentages van SQL-groep kunnen variëren afhankelijk van de databasegrootte en locatie van het bron- en doelgegevensmagazijn.

## <a name="automatic-restore-points"></a>Automatische herstelpunten

Momentopnamen zijn een ingebouwde functie van de service die herstelpunten maakt. U hoeft deze mogelijkheid niet in te schakelen. De SQL-groep moet echter actief zijn voor het maken van herstelpunten. Als de SQL-groep vaak wordt onderbroken, worden mogelijk geen automatische herstelpunten gemaakt, dus zorg ervoor dat u een door de gebruiker gedefinieerd herstelpunt maakt voordat u de SQL-groep pauzeert. Automatische herstelpunten kunnen momenteel niet door gebruikers worden verwijderd omdat de service deze herstelpunten gebruikt om SLA's te behouden voor herstel.

Snapshots van uw gegevensmagazijn worden gedurende de dag gemaakt door herstelpunten te maken die zeven dagen beschikbaar zijn. Deze bewaartermijn kan niet worden gewijzigd. SQL-pool ondersteunt een acht uur durende doelstelling voor herstelpunten (RPO). U uw gegevensmagazijn in het primaire gebied herstellen van een van de momentopnamen die in de afgelopen zeven dagen zijn gemaakt.

Voer deze query uit op uw online SQL-groep om te zien wanneer de laatste momentopname is gestart.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Door de gebruiker gedefinieerde herstelpunten

Met deze functie u handmatig momentopnamen activeren om herstelpunten van uw gegevensmagazijn te maken voor en na grote wijzigingen. Deze mogelijkheid zorgt ervoor dat herstelpunten logisch consistent zijn, wat extra gegevensbescherming biedt in geval van werkonderbrekingen of gebruikersfouten voor een snelle hersteltijd. Door de gebruiker gedefinieerde herstelpunten zijn zeven dagen beschikbaar en worden namens u automatisch verwijderd. U de bewaarperiode van door de gebruiker gedefinieerde herstelpunten niet wijzigen. **42 door de gebruiker gedefinieerde herstelpunten** zijn op elk moment gegarandeerd, dus ze moeten worden [verwijderd](https://go.microsoft.com/fwlink/?linkid=875299) voordat ze een ander herstelpunt maken. U momentopnamen activeren om door de gebruiker gedefinieerde herstelpunten te maken via [PowerShell](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) of de Azure-portal.

> [!NOTE]
> Als u herstelpunten langer dan 7 dagen nodig hebt, stem dan [hier](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points)voor deze mogelijkheid. U ook een door de gebruiker gedefinieerd herstelpunt maken en herstellen van het nieuw gemaakte herstelpunt naar een nieuw gegevensmagazijn. Zodra u hersteld bent, hebt u de SQL-pool online en u deze voor onbepaalde tijd onderbreken om rekenkosten te besparen. De onderbroken database brengt opslagkosten met zich mee tegen het Azure Premium-opslagtarief. Als u een actieve kopie van het herstelde gegevensmagazijn nodig hebt, u deze hervatten, wat slechts enkele minuten in beslag moet nemen.

### <a name="restore-point-retention"></a>Puntbehoud herstellen

In de volgende gegevens worden gegevens vermeld voor de bewaartermijnen voor herstelpunten:

1. SQL-groep verwijdert een herstelpunt wanneer deze de bewaarperiode van 7 dagen bereikt **en** wanneer er ten minste 42 totale herstelpunten zijn (inclusief zowel door de gebruiker gedefinieerd als automatisch).
2. Momentopnamen worden niet gemaakt wanneer een SQL-pool wordt onderbroken.
3. De leeftijd van een herstelpunt wordt gemeten aan de basiskalenderdagen vanaf het moment dat het herstelpunt wordt genomen, inclusief wanneer de SQL-groep wordt onderbroken.
4. Op elk moment kan een SQL-pool gegarandeerd maximaal 42 door de gebruiker gedefinieerde herstelpunten en 42 automatische herstelpunten opslaan zolang deze herstelpunten de bewaartermijn van 7 dagen niet hebben bereikt
5. Als een momentopname wordt gemaakt, wordt de SQL-groep vervolgens langer dan 7 dagen onderbroken en wordt het herstelpunt hervat totdat er 42 totale herstelpunten zijn (inclusief zowel door de gebruiker gedefinieerd als automatisch)

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>Momentopnameretentie wanneer een SQL-groep wordt verwijderd

Wanneer u een SQL-pool neerzet, wordt een laatste momentopname gemaakt en gedurende zeven dagen opgeslagen. U de SQL-groep herstellen naar het uiteindelijke herstelpunt dat bij verwijdering is gemaakt. Als de SQL-groep in een onderbroken status wordt gedropt, wordt er geen momentopname gemaakt. Zorg er in dat scenario voor dat u een door de gebruiker gedefinieerd herstelpunt maakt voordat u de SQL-groep laat vallen.

> [!IMPORTANT]
> Als u een logische SQL-serverinstantie verwijdert, worden alle databases die tot de instantie behoren ook verwijderd en kunnen ze niet worden hersteld. U een verwijderde server niet herstellen.

## <a name="geo-backups-and-disaster-recovery"></a>Geo-back-ups en noodherstel

Een geoback-up wordt eenmaal per dag gemaakt naar een [gekoppeld datacenter.](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) De RPO voor een geo-herstel is 24 uur. U de geoback-up herstellen naar een server in een ander gebied waar SQL-pool wordt ondersteund. Een geoback-up zorgt ervoor dat u het gegevensmagazijn herstellen voor het geval u geen toegang hebt tot de herstelpunten in uw primaire regio.

> [!NOTE]
> Als u een kortere RPO voor geo-back-ups nodig hebt, stem dan [hier](https://feedback.azure.com/forums/307516-sql-data-warehouse)voor deze mogelijkheid. U ook een door de gebruiker gedefinieerd herstelpunt maken en herstellen van het nieuw gemaakte herstelpunt naar een nieuw gegevensmagazijn in een andere regio. Zodra u hersteld bent, hebt u het gegevensmagazijn online en u het voor onbepaalde tijd onderbreken om rekenkosten te besparen. De onderbroken database brengt opslagkosten met zich mee tegen het Azure Premium-opslagtarief. Als u een actieve kopie van het gegevensmagazijn nodig hebt, u deze hervatten, wat slechts enkele minuten in beslag moet nemen.

## <a name="backup-and-restore-costs"></a>Kosten voor back-ups en herstel

U zult merken dat de Azure-factuur een regelitem voor opslag en een regelitem voor disaster recovery storage heeft. De opslagkosten zijn de totale kosten voor het opslaan van uw gegevens in het primaire gebied, samen met de incrementele wijzigingen die door momentopnamen zijn vastgelegd. Raadpleeg voor een meer gedetailleerde uitleg over de manier waarop momentopnamen in rekening worden [gebracht, naar Inzicht in hoe kosten voor momentopnamen worden gegenereerd](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). De georedundante kosten dekken de kosten voor het opslaan van de geo-back-ups.  

De totale kosten voor uw primaire gegevensmagazijn en zeven dagen van momentopnamewijzigingen worden afgerond op de dichtstbijzijnde TB. Als uw gegevensmagazijn bijvoorbeeld 1,5 TB bedraagt en de momentopnamen 100 GB bevatten, wordt er 2 TB aan gegevens in rekening gebracht met Azure Premium Storage-tarieven.

Als u georedundante opslag gebruikt, ontvangt u een aparte opslagtoeslag. De georedundante opslag wordt gefactureerd op het standaard Ra-GRS-tarief (Read-Access Geographically Redundant Storage).

Zie [Azure Synapse-prijzen](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)voor meer informatie over azure synapse-prijzen. Er worden geen kosten in rekening gebracht voor gegevensdie worden uitgevoerd bij het herstellen van verschillende regio's.

## <a name="restoring-from-restore-points"></a>Herstellen vanaf herstelpunten

Elke momentopname maakt een herstelpunt dat de tijd aangeeft waarop de momentopname is gestart. Als u een gegevensmagazijn wilt herstellen, kiest u een herstelpunt en geeft u een herstelopdracht uit.  

U het magazijn voor herstelde gegevens en het huidige magazijn behouden of een van deze items verwijderen. Als u het huidige gegevensmagazijn wilt vervangen door het herstelde gegevensmagazijn, u de naam wijzigen met DE OPTIE [WIJZIGINGSDATABASE (SQL-pool)](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) wijzigen.

Zie [Een SQL-groep herstellen](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal)als u een gegevensmagazijn wilt herstellen.

Als u een verwijderd of onderbroken gegevensmagazijn wilt herstellen, u [een ondersteuningsticket maken.](sql-data-warehouse-get-started-create-support-ticket.md)

## <a name="cross-subscription-restore"></a>Herstel van kruisabonnementen

Als u direct moet herstellen over het abonnement, stem dan [hier](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore)voor deze mogelijkheid. Herstellen naar een andere logische server en ['Verplaats'](/azure/azure-resource-manager/resource-group-move-resources?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) de server over abonnementen om een cross-abonnement te herstellen.

## <a name="geo-redundant-restore"></a>Georedundante herstel

U [uw SQL-pool herstellen](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell) naar elke regio die SQL-pool ondersteunt op het door u gekozen prestatieniveau.

> [!NOTE]
> Als u een geo-redundant herstel wilt uitvoeren, moet u zich niet hebben afgemeld voor deze functie.

## <a name="next-steps"></a>Volgende stappen

Zie [Overzicht bedrijfscontinuïteit](../../sql-database/sql-database-business-continuity.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) voor meer informatie over rampenplanning

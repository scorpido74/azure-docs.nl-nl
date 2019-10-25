---
title: Azure SQL Data Warehouse back-up en herstel-moment opnamen, geografisch redundant | Microsoft Docs
description: Meer informatie over het werken met back-ups en herstellen in Azure SQL Data Warehouse. Gebruik Data Warehouse-back-ups om uw data warehouse te herstellen naar een herstel punt in de primaire regio. Gebruik geo-redundante back-ups om te herstellen naar een andere geografische regio.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/21/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 1cf6444b155830326f4876d2d65bcdaa5923fc35
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72788816"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Back-ups maken en herstellen in Azure SQL Data Warehouse

Meer informatie over het gebruik van back-up en herstel in Azure SQL Data Warehouse. Herstel punten voor Data Warehouse gebruiken om uw data warehouse te herstellen of te kopiëren naar een eerdere status in de primaire regio. Gebruik geo-redundante back-ups van data warehouse om te herstellen naar een andere geografische regio.

## <a name="what-is-a-data-warehouse-snapshot"></a>Wat is een moment opname van een Data Warehouse

Een *moment opname van een Data Warehouse* maakt u een herstel punt dat u kunt gebruiken voor het herstellen of kopiëren van uw data warehouse naar een eerdere status.  Omdat SQL Data Warehouse een gedistribueerd systeem is, bestaat een moment opname van een Data Warehouse uit veel bestanden die zich in azure Storage bevinden. Met moment opnamen worden incrementele wijzigingen vastgelegd op basis van de gegevens die zijn opgeslagen in uw data warehouse.

Een *Data Warehouse Restore* is een nieuw data warehouse dat is gemaakt op basis van een herstel punt van een bestaand of verwijderd Data Warehouse. Het herstellen van uw data warehouse is een essentieel onderdeel van een strategie voor bedrijfs continuïteit en herstel na nood gevallen, omdat de gegevens na een onbedoeld beschadiging of verwijdering opnieuw worden gemaakt. Data Warehouse is ook een krachtig mechanisme voor het maken van kopieën van uw data warehouse voor test-of ontwikkelings doeleinden.  SQL Data Warehouse herstel tarieven kunnen variëren, afhankelijk van de grootte van de data base en de locatie van het bron-en doel-Data Warehouse. 

## <a name="automatic-restore-points"></a>Automatische herstel punten

Moment opnamen zijn een ingebouwde functie van de service waarmee herstel punten worden gemaakt. U hoeft deze mogelijkheid niet in te scha kelen. Het Data Warehouse moet echter de status actief hebben voor het maken van herstel punten. Als het Data Warehouse regel matig wordt onderbroken, kunnen er geen automatische herstel punten worden gemaakt, dus zorg ervoor dat u een door de gebruiker gedefinieerd herstel punt maakt voordat u het Data Warehouse onderbreekt. Automatische herstel punten op dit moment kunnen niet door gebruikers worden verwijderd omdat de service deze herstel punten gebruikt om de Sla's voor herstel te onderhouden.

SQL Data Warehouse maakt moment opnamen van uw data warehouse gedurende de hele dag om herstel punten te maken die zeven dagen beschikbaar zijn. Deze Bewaar periode kan niet worden gewijzigd. SQL Data Warehouse ondersteunt een RPO (acht Recovery Point Objective uur). U kunt uw data warehouse in de primaire regio herstellen vanuit een van de moment opnamen die in de afgelopen zeven dagen zijn gemaakt.

Als u wilt zien wanneer de laatste moment opname is gestart, voert u deze query uit op uw online SQL Data Warehouse.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Door de gebruiker gedefinieerde herstel punten

Met deze functie kunt u hand matig moment opnamen activeren om herstel punten van uw data warehouse te maken voor en na grote wijzigingen. Deze functionaliteit zorgt ervoor dat herstel punten logisch consistent zijn, wat een extra gegevens bescherming biedt bij onderbrekingen van de werk belasting of gebruikers fouten voor snelle herstel tijd. Door de gebruiker gedefinieerde herstel punten zijn zeven dagen beschikbaar en worden namens u automatisch verwijderd. U kunt de Bewaar periode van door de gebruiker gedefinieerde herstel punten niet wijzigen. **42 door de gebruiker gedefinieerde herstel punten** worden op elk moment gegarandeerd, zodat ze moeten worden [verwijderd](https://go.microsoft.com/fwlink/?linkid=875299) voordat er een nieuw herstel punt wordt gemaakt. U kunt moment opnamen activeren om door de gebruiker gedefinieerde herstel punten te maken via [Power shell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint#examples) of de Azure Portal.

> [!NOTE]
> Als u herstel punten van meer dan zeven dagen nodig hebt, kunt u [hier](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points)stemmen voor deze functie. U kunt ook een door de gebruiker gedefinieerd herstel punt maken en herstellen vanuit het zojuist gemaakte herstel punt naar een nieuw data warehouse. Nadat u de gegevens hebt hersteld, hebt u het Data Warehouse online en kunt u het voor onbepaalde tijd pauzeren om reken kosten op te slaan. De gepauzeerde data base maakt kosten per seconde op het Premium Storage tarief van Azure. Als u een actieve kopie van het herstelde data warehouse nodig hebt, kunt u hervatten. dit duurt slechts enkele minuten.

### <a name="restore-point-retention"></a>Bewaar periode van het herstel punt

De volgende lijst bevat Details voor de Bewaar periode van het herstel punt:

1. SQL Data Warehouse een herstel punt verwijdert wanneer deze de Bewaar periode van 7 dagen heeft **en** wanneer er ten minste 42 totale herstel punten zijn (inclusief zowel door de gebruiker gedefinieerde als automatische)
2. Moment opnamen worden niet uitgevoerd wanneer een Data Warehouse wordt onderbroken
3. De leeftijd van een herstel punt wordt gemeten door de absolute kalender dagen vanaf het moment dat het herstel punt wordt gemaakt, inclusief wanneer het Data Warehouse wordt onderbroken
4. Op elk moment is een Data Warehouse gegarandeerd dat er Maxi maal 42 door de gebruiker gedefinieerde herstel punten en 42 automatische herstel punten kunnen worden opgeslagen zolang deze herstel punten de Bewaar periode van 7 dagen niet hebben bereikt
5. Als er een moment opname wordt gemaakt, wordt het Data Warehouse langer dan zeven dagen gepauzeerd en vervolgens hervat. het is mogelijk dat het herstel punt persistent blijft totdat er 42 totaal herstel punten zijn (inclusief door de gebruiker gedefinieerde en automatische)

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Moment opname bewaren wanneer een Data Warehouse wordt verwijderd

Wanneer u een Data Warehouse verwijdert, maakt SQL Data Warehouse een definitieve moment opname en slaat deze gedurende zeven dagen op. U kunt het Data Warehouse herstellen naar het laatste herstel punt dat bij het verwijderen is gemaakt. Als de status van het Data Warehouse is onderbroken, wordt er geen moment opname gemaakt. Zorg er in dat scenario voor dat u een door de gebruiker gedefinieerd herstel punt maakt voordat u het Data Warehouse verwijdert.

> [!IMPORTANT]
> Als u een logisch exemplaar van SQL Server verwijdert, worden ook alle data bases die deel uitmaken van het exemplaar, verwijderd en kunnen ze niet worden hersteld. U kunt een verwijderde server niet herstellen.

## <a name="geo-backups-and-disaster-recovery"></a>Geo-back-ups en herstel na nood gevallen

SQL Data Warehouse wordt één keer per dag een geo-back-up uitgevoerd naar een [gekoppeld Data Center](../best-practices-availability-paired-regions.md). De RPO voor een geo-Restore is 24 uur. U kunt de geo-back-up naar een server herstellen in een andere regio waar SQL Data Warehouse wordt ondersteund. Een geo-back-up zorgt ervoor dat u Data Warehouse kunt herstellen als u geen toegang hebt tot de herstel punten in uw primaire regio.

> [!NOTE]
> Als u een korte RPO voor geo-back-ups nodig hebt, stem dan op [deze functie.](https://feedback.azure.com/forums/307516-sql-data-warehouse) U kunt ook een door de gebruiker gedefinieerd herstel punt maken en herstellen vanuit het zojuist gemaakte herstel punt naar een nieuw data warehouse in een andere regio. Nadat u de gegevens hebt hersteld, hebt u het Data Warehouse online en kunt u het voor onbepaalde tijd pauzeren om reken kosten op te slaan. De gepauzeerde data base maakt kosten per seconde op het Premium Storage tarief van Azure. Als u een actieve kopie van het Data Warehouse nodig hebt, kunt u hervatten. dit duurt slechts enkele minuten.

## <a name="backup-and-restore-costs"></a>Kosten voor back-up en herstel

U ziet dat de Azure-factuur een regel item bevat voor opslag en een regel item voor nood herstel opslag. De opslag kosten zijn de totale kosten voor het opslaan van uw gegevens in de primaire regio, samen met de incrementele wijzigingen die zijn vastgelegd door moment opnamen. Raadpleeg de [informatie over het samen voegen](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios)van moment opnamen voor een gedetailleerde uitleg over de kosten voor moment opnamen. De geo-redundante kosten omvatten de kosten voor het opslaan van de geo-back-ups.  

De totale kosten voor uw primaire Data Warehouse en zeven dagen aan momentopname wijzigingen worden afgerond op de dichtstbijzijnde TB. Als uw data warehouse bijvoorbeeld 1,5 TB is en de moment opnamen 100 GB worden vastgelegd, worden er twee TB aan gegevens in rekening gebracht op Azure Premium Storage-tarieven.

Als u geografisch redundante opslag gebruikt, ontvangt u afzonderlijke opslag kosten. De geo-redundante opslag wordt gefactureerd op basis van het standaard-GRS-snelheid (geografisch redundante opslag met lees toegang).

Zie [SQL Data Warehouse prijzen](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)voor meer informatie over SQL Data Warehouse prijzen. Er worden geen kosten in rekening gebracht voor het afrekenen van gegevens bij het herstellen tussen regio's.

## <a name="restoring-from-restore-points"></a>Herstellen vanaf herstel punten

Elke moment opname maakt een herstel punt dat staat voor de tijd waarop de moment opname is gestart. Als u een Data Warehouse wilt herstellen, kiest u een herstel punt en geeft u een opdracht herstellen op.  

U kunt het herstelde data warehouse en de huidige herstellen, of een van beide verwijderen. Als u het huidige Data Warehouse wilt vervangen door het herstelde data warehouse, kunt u de naam ervan wijzigen met behulp van [ALTER data base (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) met de optie naam wijzigen.

Als u een Data Warehouse wilt herstellen, raadpleegt u [een Data Warehouse terugzetten met behulp van de Azure Portal](sql-data-warehouse-restore-database-portal.md), [een Data Warehouse terugzetten met behulp van Power shell](sql-data-warehouse-restore-database-powershell.md)of [een Data Warehouse herstellen met rest api's](sql-data-warehouse-restore-database-rest-api.md).

Als u een verwijderd of onderbroken Data Warehouse wilt herstellen, kunt u [een ondersteunings ticket maken](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Meerdere abonnementen herstellen

Als u het abonnement direct wilt herstellen, moet u [hier](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore)stemmen voor deze functie. Herstel op een andere logische server en [Verplaats](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) de server over abonnementen om een abonnement op meerdere abonnementen uit te voeren. 

## <a name="geo-redundant-restore"></a>Geografisch redundant herstel

U kunt [uw data warehouse herstellen](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-from-geo-backup#restore-from-an-azure-geographical-region-through-powershell) naar elke regio die SQL Data Warehouse op het gekozen prestatie niveau ondersteunt.

> [!NOTE]
> Als u een geo-redundante terugzet bewerking wilt uitvoeren, moet u deze functie niet gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van bedrijfs continuïteit](../sql-database/sql-database-business-continuity.md) voor meer informatie over planning voor nood gevallen

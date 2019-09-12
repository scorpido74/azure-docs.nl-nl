---
title: Uw bestaande Azure SQL Data Warehouse migreren naar Gen2 | Microsoft Docs
description: Instructies voor het migreren van een bestaande Data Warehouse naar Gen2 en de migratie planning per regio.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 07/22/2019
ms.openlocfilehash: ac478a7b75bbac0c5e7f59cbe565ec2bbcd643ce
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900324"
---
# <a name="upgrade-your-data-warehouse-to-gen2"></a>Upgrade uw data warehouse naar Gen2

Micro soft helpt u bij het aansturen van de kosten op instap niveau van het uitvoeren van een Data Warehouse.  Er zijn Azure SQL Data Warehouse nu lagere reken lagen mogelijk waarmee veeleisende query's kunnen worden verwerkt. Lees de volledige aankondiging [ondersteuning voor de compute-laag voor Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). De nieuwe aanbieding is beschikbaar in de regio's die in de onderstaande tabel worden vermeld. Voor ondersteunde regio's kunnen bestaande gen1-data warehouses worden bijgewerkt naar Gen2 via een van de volgende opties:

- **Het automatische upgrade proces:** Automatische upgrades worden niet gestart zodra de service beschikbaar is in een regio.  Wanneer automatische upgrades worden gestart in een bepaalde regio, worden er afzonderlijke DW-upgrades uitgevoerd tijdens de geselecteerde onderhouds planning.
- [**Zelf upgrade naar Gen2:** ](#self-upgrade-to-gen2) U kunt bepalen wanneer u een upgrade uitvoert door een self-upgrade uit te voeren naar Gen2. Als uw regio nog niet wordt ondersteund, kunt u vanaf een herstel punt rechtstreeks naar een Gen2-exemplaar in een ondersteunde regio herstellen.

## <a name="automated-schedule-and-region-availability-table"></a>Tabel met Beschik baarheid van automatische planning en regio

De volgende tabel bevat een overzicht van de regio's wanneer de lagere Gen2-Compute-laag beschikbaar is en wanneer automatische upgrades worden gestart. De datums zijn onderhevig aan wijzigingen. Ga terug om te zien wanneer uw regio beschikbaar wordt.

\*Hiermee wordt aangegeven dat een specifieke planning voor de regio momenteel niet beschikbaar is.

| **Regio** | **Minder Gen2 beschikbaar** | **Begin van automatische upgrades** |
|:--- |:--- |:--- |
| Australië - oost |Beschikbaar |Voltooien |
| Australië - zuidoost |Beschikbaar |Voltooien |
| Brazilië - zuid |Beschikbaar |Voltooien |
| Canada - midden |Beschikbaar |Voltooien |
| Canada - oost |1 juni 2020 |1 juli 2020 |
| US - centraal |Beschikbaar |Voltooien |
| China - oost |\* |\* |
| China - oost 2 |Beschikbaar |Voltooien |
| China - noord |\* |\* |
| China - noord 2 |Beschikbaar |Voltooien |
| Azië - oost |Beschikbaar |Voltooien |
| East US |Beschikbaar |Voltooien |
| US - oost 2 |Beschikbaar |Voltooien |
| Frankrijk - centraal |Beschikbaar |In uitvoering |
| Duitsland - centraal |\* |\* |
| Duitsland - west-centraal |1 september 2019|1 oktober 2019 |
| India - centraal |Beschikbaar |Voltooien |
| India - zuid |Beschikbaar |Voltooien |
| India - west |1 juli 2019 |In uitvoering |
| Japan - oost |Beschikbaar |Voltooien |
| Japan - west |Beschikbaar |Voltooien |
| Korea - centraal |Beschikbaar |Voltooien |
| Korea - zuid |Beschikbaar |Voltooien |
| US - noord-centraal |Beschikbaar |Voltooien |
| Europa - noord |Beschikbaar |Voltooien |
| Zuid-Afrika - noord |12 juli 2019 |Voltooien |
| US - zuid-centraal |Beschikbaar |Voltooien |
| Azië - zuidoost |Beschikbaar |Voltooien |
| UAE - noord |20 juli 2019 |Voltooien |
| Verenigd Koninkrijk Zuid |Beschikbaar |In uitvoering |
| Verenigd Koninkrijk West |Beschikbaar |In uitvoering |
| US - west-centraal |1 november 2019 |1 december 2019|
| Europa -west |Beschikbaar |Voltooien |
| US - west |Beschikbaar |Voltooien |
| US - west 2 |Beschikbaar |Voltooien |

## <a name="automatic-upgrade-process"></a>Automatisch upgrade proces

Op basis van de bovenstaande beschikbaarheids grafiek worden automatische upgrades gepland voor uw gen1-instanties. Om onverwachte onderbrekingen van de beschik baarheid van het Data Warehouse te voor komen, worden de geautomatiseerde upgrades gepland tijdens de onderhouds planning. De mogelijkheid om een nieuw gen1-exemplaar te maken wordt uitgeschakeld in regio's die automatisch worden bijgewerkt naar Gen2. Gen1 wordt afgeschaft zodra de automatische upgrades zijn voltooid. Zie [een onderhouds planning weer geven](viewing-maintenance-schedule.md) voor meer informatie over planningen

Bij het upgrade proces geldt een korte daling van de connectiviteit (ongeveer 5 min.) wanneer uw data warehouse opnieuw wordt gestart.  Zodra uw data warehouse opnieuw is opgestart, is het volledig beschikbaar voor gebruik. Het is echter mogelijk dat de prestaties worden verminderd terwijl het upgrade proces de gegevens bestanden op de achtergrond bijwerkt. De totale tijdsduur voor afnemende prestaties is afhankelijk van de grootte van uw gegevensbestanden.

U kunt het upgrade proces van het gegevens bestand ook versnellen door [ALTER index Rebuild](sql-data-warehouse-tables-index.md) op alle primaire column Store-tabellen uit te voeren met een grotere SLO en resource klasse nadat de computer opnieuw is opgestart.

> [!NOTE]
> Alter index Rebuild is een offline bewerking en de tabellen zijn pas beschikbaar als het opnieuw opbouwen is voltooid.

## <a name="self-upgrade-to-gen2"></a>Zelf upgrade naar Gen2

U kunt zelf een upgrade uitvoeren door de volgende stappen uit te voeren op een bestaand gen1-Data Warehouse. Als u zelf een upgrade wilt uitvoeren, moet u deze volt ooien voordat het automatische upgrade proces in uw regio wordt gestart. Dit zorgt ervoor dat u elk risico op de automatische upgrades voor komt dat een conflict veroorzaakt.

Er zijn twee opties voor het uitvoeren van een self-upgrade.  U kunt uw huidige Data Warehouse op locatie bijwerken of u kunt een gen1-Data Warehouse herstellen in een Gen2-exemplaar.

- [In-place upgrade uitvoeren](upgrade-to-latest-generation.md) : met deze optie wordt uw bestaande gen1-Data Warehouse bijgewerkt naar Gen2. Bij het upgrade proces geldt een korte daling van de connectiviteit (ongeveer 5 min.) wanneer uw data warehouse opnieuw wordt gestart.  Zodra uw data warehouse opnieuw is opgestart, is het volledig beschikbaar voor gebruik. Als u problemen ondervindt tijdens de upgrade, opent u een [ondersteunings aanvraag](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) en raadpleegt u de mogelijke oorzaak van de Gen2-upgrade.
- [Upgrade van herstel punt](sql-data-warehouse-restore.md) : Maak een door de gebruiker gedefinieerd herstel punt op het huidige gen1-Data Warehouse en herstel het vervolgens rechtstreeks naar een Gen2-exemplaar. Het bestaande gen1-Data Warehouse blijft aanwezig. Nadat het herstel is voltooid, is uw Gen2-Data Warehouse volledig beschikbaar voor gebruik.  Wanneer u alle test-en validatie processen op het teruggezette Gen2-exemplaar hebt uitgevoerd, kan de oorspronkelijke gen1-instantie worden verwijderd.

   - Stap 1: Maak in de Azure Portal [een door de gebruiker gedefinieerd herstel punt](sql-data-warehouse-restore-active-paused-dw.md#restore-an-existing-data-warehouse-through-the-azure-portal).
   - Stap 2: Wanneer u herstelt vanaf een door de gebruiker gedefinieerd herstel punt, stelt u het prestatie niveau in op de Gen2-laag van uw voor keur.

Er kan tijdelijk sprake zijn van verminderde prestaties terwijl het upgradeproces de gegevensbestanden op de achtergrond blijft bijwerken. De totale tijdsduur voor afnemende prestaties is afhankelijk van de grootte van uw gegevensbestanden.

Als u het migratie proces op de achtergrond wilt versnellen, kunt u de gegevens verplaatsing onmiddellijk afdwingen door [ALTER index Rebuild](sql-data-warehouse-tables-index.md) uit te voeren op alle primaire column Store-tabellen die u wilt opvragen bij een grotere SLO en resource klasse.

> [!NOTE]
> Alter index Rebuild is een offline bewerking en de tabellen zijn pas beschikbaar als het opnieuw opbouwen is voltooid.

Als u problemen ondervindt met uw data warehouse, maakt u een [ondersteunings aanvraag](sql-data-warehouse-get-started-create-support-ticket.md) en verwijst u naar de mogelijke oorzaak van de Gen2-upgrade.

Zie [upgrade naar Gen2](upgrade-to-latest-generation.md)voor meer informatie.

## <a name="migration-frequently-asked-questions"></a>Veelgestelde vragen over migratie

**V: Kost Gen2 hetzelfde als gen1?**

- A: Ja.

**V: Wat zijn de gevolgen van de upgrades voor mijn automatiserings scripts?**

- A: Elk Automation-script dat verwijst naar een serviceniveau doelstelling moet worden gewijzigd om overeen te komen met het Gen2-equivalent.  Bekijk [hier](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal)meer informatie.

**V: Hoe lang duurt een self-upgrade normaal?**

- A: U kunt een upgrade uitvoeren op de locatie of een upgrade uitvoeren vanaf een herstel punt.  
   - Als u een upgrade op locatie uitvoert, wordt het Data Warehouse tijdelijk onderbroken en hervat.  Er wordt een achtergrond proces voortgezet terwijl het Data Warehouse online is.  
   - Het duurt langer als u een upgrade uitvoert via een herstel punt, omdat de upgrade wordt uitgevoerd door het volledige herstel proces.

**V: Hoe lang duurt de automatische upgrade?**

- A: De werkelijke downtime voor de upgrade is alleen de tijd die nodig is om de service te onderbreken en hervatten, en die bedraagt 5 tot 10 minuten. Na een korte downtime wordt er op de achtergrond een opslagmigratie uitgevoerd. De tijdsduur voor dit achtergrondproces is afhankelijk van de grootte van uw datawarehouse.

**V: Wanneer wordt deze automatische upgrade uitgevoerd?**

- A: Tijdens uw onderhouds planning. Door gebruik te maken van uw gekozen onderhouds planning wordt de onderbreking van uw bedrijf tot een minimum beperkt.

**V: Wat moet ik doen als mijn upgrade proces op de achtergrond lijkt te blijven?**

 - A: Een REINDEX van uw column Store-tabellen starten. Houd er rekening mee dat het opnieuw indexeren van de tabel offline is tijdens deze bewerking.

**V: Wat gebeurt er als Gen2 geen serviceniveau doelstelling heeft op gen1?**
- A: Als u een DW600 of DW1200 uitvoert op gen1, is het raadzaam om DW500c of DW1000c te gebruiken, omdat Gen2 meer geheugen, meer bronnen en betere prestaties dan gen1 biedt.

**V: Kan ik geo-backup uitschakelen?**
- A: Nee. Geo-Backup is een Enter prise-functie waarmee u de beschik baarheid van uw data warehouse kunt bewaren in het geval dat een regio niet meer beschikbaar is. Open een [ondersteunings aanvraag](sql-data-warehouse-get-started-create-support-ticket.md) als u nog meer problemen hebt.

**V: Is er een verschil in T-SQL-syntaxis tussen gen1 en Gen2?**

- A: Er is geen wijziging in de syntaxis van de T-SQL-taal van gen1 naar Gen2.

**V: Ondersteunt Gen2 onderhouds Vensters?**

- A: Ja.

**V: Kan ik een nieuw gen1-exemplaar maken nadat mijn regio is bijgewerkt?**

- A: Nee. Nadat een upgrade van een regio is uitgevoerd, wordt het maken van nieuwe gen1-instanties uitgeschakeld.

## <a name="next-steps"></a>Volgende stappen

- [Upgrade stappen](upgrade-to-latest-generation.md)
- [Onderhouds Vensters](maintenance-scheduling.md)
- [Resource Health Monitor](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Controleren voordat u een migratie start](upgrade-to-latest-generation.md#before-you-begin)
- [Upgrade in-place en upgrade vanaf een herstel punt](upgrade-to-latest-generation.md)
- [Een door de gebruiker gedefinieerd herstel punt maken](sql-data-warehouse-restore-points.md)
- [Meer informatie over het herstellen naar Gen2](sql-data-warehouse-restore-active-paused-dw.md#restore-an-existing-data-warehouse-through-the-azure-portal)
- [Een ondersteunings aanvraag voor SQL Data Warehouse openen](https://go.microsoft.com/fwlink/?linkid=857950)

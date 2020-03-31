---
title: Uw SQL-groep migreren naar Gen2
description: Instructies voor het migreren van een bestaande SQL-groep naar Gen2 en het migratieschema per regio.
services: synapse-analytics
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 4714d5908fffb6f5c1440c3ec512fb8173da4b57
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346766"
---
# <a name="upgrade-your-sql-pool-to-gen2"></a>Uw SQL-groep upgraden naar Gen2

Microsoft helpt de instapkosten van het uitvoeren van een SQL-pool te verlagen.  Lagere rekenlagen die veeleisende query's kunnen verwerken, zijn nu beschikbaar voor SQL-groep. Lees de volledige aankondiging [Lagere compute tier ondersteuning voor Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). Het nieuwe aanbod is beschikbaar in de regio's die in de onderstaande tabel zijn vermeld. Voor ondersteunde regio's kunnen bestaande Gen1 SQL-pools worden geüpgraded naar Gen2 via:

- **Het automatische upgradeproces:** Automatische upgrades starten niet zodra de service beschikbaar is in een regio.  Wanneer automatische upgrades starten in een specifieke regio, vinden individuele upgrades van het gegevensmagazijn plaats tijdens uw geselecteerde onderhoudsschema.
- [**Zelfupgrade naar Gen2:**](#self-upgrade-to-gen2) U bepalen wanneer u moet upgraden door een zelfupgrade naar Gen2 te doen. Als uw regio nog niet wordt ondersteund, u vanaf een herstelpunt rechtstreeks herstellen naar een Gen2-instantie in een ondersteund gebied.

## <a name="automated-schedule-and-region-availability-table"></a>Tabel voor geautomatiseerde planning en beschikbaarheid van regio's

De volgende tabel geeft een overzicht van per regio wanneer de rekenlaag Van De Lagere Gen2 beschikbaar is en wanneer automatische upgrades worden gestart. De data kunnen worden gewijzigd. Kijk terug om te zien wanneer uw regio beschikbaar is.

\*geeft aan dat een specifieke planning voor de regio momenteel niet beschikbaar is.

| **Regio** | **Lagere Gen2 beschikbaar** | **Automatische upgrades beginnen** |
|:--- |:--- |:--- |
| Canada - oost |1 juni 2020 |1 juli 2020 |
| China East |\* |\* |
| China - noord |\* |\* |
| Duitsland - centraal |\* |\* |
| Duitsland West Centraal |Beschikbaar |1 mei 2020 |
| India - west |Beschikbaar |1 mei 2020  |

## <a name="automatic-upgrade-process"></a>Automatisch upgradeproces

Op basis van de bovenstaande beschikbaarheidsgrafiek plannen we geautomatiseerde upgrades voor uw Gen1-exemplaren. Om onverwachte onderbrekingen in de beschikbaarheid van de SQL-pool te voorkomen, worden de geautomatiseerde upgrades gepland tijdens uw onderhoudsschema. De mogelijkheid om een nieuwe Gen1-instantie te maken, wordt uitgeschakeld in regio's die een automatische upgrade naar Gen2 ondergaan. Gen1 zal worden afgeschaft zodra de automatische upgrades zijn voltooid. Zie [Een onderhoudsschema weergeven voor](maintenance-scheduling.md#view-a-maintenance-schedule) meer informatie over planningen

Het upgradeproces omvat een korte daling van de connectiviteit (ongeveer 5 min) wanneer we uw SQL-pool opnieuw opstarten.  Zodra uw SQL-pool opnieuw is gestart, is deze volledig beschikbaar voor gebruik. U echter een verslechtering van de prestaties ervaren terwijl het upgradeproces de gegevensbestanden op de achtergrond blijft upgraden. De totale tijdsduur voor afnemende prestaties is afhankelijk van de grootte van uw gegevensbestanden.

U ook het upgradeproces voor gegevensbestanden versnellen door [opnieuw op te bouwen wijzigingsindex](sql-data-warehouse-tables-index.md) op alle primaire kolomarchieftabellen met een grotere SLO- en resourceklasse na de herstart.

> [!NOTE]
> Opnieuw herstellen van de index is een offlinebewerking en de tabellen zijn pas beschikbaar als de wederopbouw is voltooid.

## <a name="self-upgrade-to-gen2"></a>Zelfupgrade naar Gen2

U ervoor kiezen om zelf te upgraden door deze stappen te volgen in een bestaande Gen1 SQL-groep. Als u ervoor kiest om zelf te upgraden, moet u deze voltooien voordat het automatische upgradeproces in uw regio begint. Dit zorgt ervoor dat u elk risico voorkomt dat de automatische upgrades een conflict veroorzaken.

Er zijn twee opties bij het uitvoeren van een self-upgrade.  U uw huidige SQL-pool op zijn plaats upgraden of een Gen1 SQL-groep herstellen naar een Gen2-exemplaar.

- [Upgrade ter plaatse](upgrade-to-latest-generation.md) - Met deze optie wordt uw bestaande Gen1 SQL-groep geüupgradet naar Gen2. Het upgradeproces omvat een korte daling van de connectiviteit (ongeveer 5 min) wanneer we uw SQL-pool opnieuw opstarten.  Zodra uw SQL-pool opnieuw is gestart, is deze volledig beschikbaar voor gebruik. Als u problemen ondervindt tijdens de upgrade, opent u een [ondersteuningsverzoek](sql-data-warehouse-get-started-create-support-ticket.md) en verwijst u naar 'Gen2-upgrade' als mogelijke oorzaak.
- [Upgrade vanaf herstelpunt](sql-data-warehouse-restore-points.md) : maak een door de gebruiker gedefinieerd herstelpunt op uw huidige Gen1 SQL-groep en herstel vervolgens rechtstreeks naar een Gen2-instantie. De bestaande Gen1 SQL-pool blijft op zijn plaats. Zodra het herstel is voltooid, is uw Gen2 SQL-pool volledig beschikbaar voor gebruik.  Zodra u alle test- en validatieprocessen op de herstelde Gen2-instantie hebt uitgevoerd, kan de oorspronkelijke Gen1-instantie worden verwijderd.

   - Stap 1: Maak vanuit de [Azure-portal een door de gebruiker gedefinieerd herstelpunt](sql-data-warehouse-restore-active-paused-dw.md).
   - Stap 2: Wanneer u herstelt vanaf een door de gebruiker gedefinieerd herstelpunt, stelt u het prestatieniveau in op de gewenste Gen2-laag.

Er kan tijdelijk sprake zijn van verminderde prestaties terwijl het upgradeproces de gegevensbestanden op de achtergrond blijft bijwerken. De totale tijdsduur voor afnemende prestaties is afhankelijk van de grootte van uw gegevensbestanden.

Als u het migratieproces voor achtergrondgegevens wilt versnellen, u gegevensbeweging onmiddellijk forceren door [Opnieuw opalterindex uit](sql-data-warehouse-tables-index.md) te voeren op alle primaire kolomarchieftabellen die u zou opvragen bij een grotere SLO- en resourceklasse.

> [!NOTE]
> Opnieuw herstellen van de index is een offlinebewerking en de tabellen zijn pas beschikbaar als de wederopbouw is voltooid.

Als u problemen ondervindt met uw SQL-groep, maakt u een [ondersteuningsaanvraag](sql-data-warehouse-get-started-create-support-ticket.md) en verwijst u naar 'Gen2-upgrade' als mogelijke oorzaak.

Zie [Upgraden naar Gen2 voor](upgrade-to-latest-generation.md)meer informatie.

## <a name="migration-frequently-asked-questions"></a>Migratie veelgestelde vragen

**V: Kost Gen2 hetzelfde als Gen1?**

- A: Ja.

**V: Welke invloed hebben de upgrades op mijn automatiseringsscripts?**

- A: Elk automatiseringsscript dat verwijst naar een doelstelling op serviceniveau, moet worden gewijzigd om overeen te komen met het Gen2-equivalent.  Zie [details hier](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**V: Hoe lang duurt een zelfupgrade normaal gesproken?**

- A: U upgraden of upgraden vanaf een herstelpunt.  
   - Als u op zijn plaats upgradet, wordt uw SQL-pool tijdelijk onderbroken en hervat.  Een achtergrondproces wordt voortgezet terwijl de SQL-pool online is.  
   - Het duurt langer als u een upgrade uitvoert via een herstelpunt, omdat de upgrade het volledige herstelproces doorloopt.

**V: Hoe lang duurt de automatische upgrade?**

- A: De werkelijke downtime voor de upgrade is alleen de tijd die nodig is om de service te pauzeren en te hervatten, die tussen de 5 tot 10 minuten is. Na een korte downtime wordt er op de achtergrond een opslagmigratie uitgevoerd. De tijdsduur voor het achtergrondproces is afhankelijk van de grootte van uw SQL-groep.

**V: Wanneer vindt deze automatische upgrade plaats?**

- A: Tijdens uw onderhoudsschema. Als u gebruik maakt van uw gekozen onderhoudsschema, minimaliseert u de verstoring van uw bedrijf.

**V: Wat moet ik doen als mijn upgradeproces op de achtergrond vast lijkt te zitten?**

 - A: Start een reindex van uw Columnstore-tabellen. Houd er rekening mee dat het opnieuw indexeren van de tabel tijdens deze bewerking offline is.

**V: Wat als Gen2 niet de doelstelling serviceniveau heeft die ik op Gen1 heb?**
- A: Als u een DW600 of DW1200 op Gen1 uitvoert, wordt geadviseerd om respectievelijk DW500c of DW1000c te gebruiken, aangezien Gen2 meer geheugen, resources en hogere prestaties biedt dan Gen1.

**V: Kan ik geoback-up uitschakelen?**
- A: Nee. Geo-back-up is een bedrijfsfunctie om de beschikbaarheid van uw SQL-groep te behouden in het geval dat een regio niet beschikbaar is. Open een [ondersteuningsverzoek](sql-data-warehouse-get-started-create-support-ticket.md) als u zich nog meer zorgen maakt.

**V: Is er een verschil in T-SQL syntaxis tussen Gen1 en Gen2?**

- A: Er is geen wijziging in de syntaxis van de T-SQL-taal van Gen1 naar Gen2.

**V: Ondersteunt Gen2 Maintenance Windows?**

- A: Ja.

**V: Kan ik een nieuw Gen1-exemplaar maken nadat mijn regio is geüpgraded?**

- A: Nee. Nadat een regio is geüpgraded, wordt het maken van nieuwe Gen1-exemplaren uitgeschakeld.

## <a name="next-steps"></a>Volgende stappen

- [Upgradestappen](upgrade-to-latest-generation.md)
- [Onderhoudsvensters](maintenance-scheduling.md)
- [Monitor resourcestatus](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Controleren voordat u met een migratie begint](upgrade-to-latest-generation.md#before-you-begin)
- [Upgrade op zijn plaats en upgrade vanaf een herstelpunt](upgrade-to-latest-generation.md)
- [Een door de gebruiker gedefinieerd herstelpunt maken](sql-data-warehouse-restore-points.md)
- [Meer informatie over het herstellen naar Gen2](sql-data-warehouse-restore-active-paused-dw.md)
- [Een SQL Data Warehouse-ondersteuningsaanvraag openen](https://go.microsoft.com/fwlink/?linkid=857950)

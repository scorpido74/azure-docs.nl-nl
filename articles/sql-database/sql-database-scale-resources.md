---
title: Resources Azure SQL Database schalen | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u uw data base kunt schalen door toegewezen resources toe te voegen of te verwijderen.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 98d24b4f497f09e982101917296b572a5c381f42
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053597"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Database resources dynamisch schalen met minimale downtime

Met Azure SQL Database kunt u dynamisch meer resources aan uw data base toevoegen met minimale [downtime](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/). Er is echter een switch over een periode waarin de verbinding gedurende korte tijd naar de data base verloren gaat, wat kan worden verholpen met de logica voor opnieuw proberen.

## <a name="overview"></a>Overzicht

Wanneer de vraag naar uw app toeneemt van een aantal apparaten en klanten naar miljoenen, wordt Azure SQL Database met minimale downtime geschaald. Schaal baarheid is een van de belangrijkste kenmerken van PaaS waarmee u zo nodig dynamisch meer resources aan uw service kunt toevoegen. Met Azure SQL Database kunt u eenvoudig resources (CPU-energie, geheugen, i/o-door Voer en opslag) wijzigen die aan uw data bases zijn toegewezen.

U kunt prestatie problemen verhelpen vanwege een groter gebruik van uw toepassing die niet kan worden hersteld met behulp van methoden voor het opnieuw schrijven van indexeren of query's. Door meer resources toe te voegen, kunt u snel reageren wanneer de data base de huidige limieten voor bronnen heeft bereikt en meer stroom nodig heeft om de binnenkomende werk belasting te verwerken. Met Azure SQL Database kunt u ook de resources omlaag schalen wanneer ze niet nodig zijn om de kosten te verlagen.

U hoeft zich geen zorgen te maken over het aanschaffen van hardware en het wijzigen van de onderliggende infra structuur. Het schalen van de data base kan eenvoudig worden uitgevoerd via Azure Portal met behulp van een schuif regelaar.

![Schaal database prestaties](media/sql-database-scalability/scale-performance.svg)

Azure SQL Database biedt het [op DTU gebaseerde aankoop model](sql-database-service-tiers-dtu.md) en het [op vCore gebaseerde aankoop model](sql-database-service-tiers-vcore.md).

- Het [op DTU gebaseerde aankoop model](sql-database-service-tiers-dtu.md) biedt een combi natie van compute-, geheugen-en i/o-resources in drie service lagen ter ondersteuning van de werk belasting van licht gewicht naar Heavyweight Data Base: Basic, Standard en Premium. Prestatieniveaus binnen elke laag bieden een andere combinatie van deze resources, waaraan u extra opslagbronnen kunt toevoegen.
- [Met het op vCore gebaseerde aankoop model](sql-database-service-tiers-vcore.md) kunt u het aantal vCores, de hoeveelheid of het geheugen en de hoeveelheid en snelheid van de opslag kiezen. Dit aankoop model biedt drie service lagen: Algemeen, Bedrijfskritiek en grootschalige.

U kunt uw eerste app bouwen op een kleine, afzonderlijke Data Base met een lage prijs per maand in de servicelaag Basic, Standard of Algemeen en vervolgens de servicelaag hand matig of via een programma op elk gewenst moment wijzigen in de service tier Premium of Bedrijfskritiek om aan de ne te voldoen EDS van uw oplossing. U kunt het prestatieniveau aanpassen zonder uitvaltijd voor uw app of voor uw klanten. Dankzij dynamische schaalbaarheid kan uw database op een transparante manier snel reageren op veranderende resourcevereisten en betaalt u alleen voor de resources die u nodig hebt wanneer u ze nodig.

> [!NOTE]
> Dynamische schaalbaarheid is iets anders dan automatisch schalen. Automatisch schalen is wanneer een service wordt geschaald op basis van criteria, terwijl dynamische schaal baarheid hand matig kan worden geschaald met een minimale downtime.

Eén Azure SQL Database ondersteunt handmatige dynamische schaalbaarheid, maar niet automatisch schalen. Voor een meer *automatische* ervaring zou u elastische pools kunnen gebruiken. Hiermee kunnen databases resources in een pool delen op basis van afzonderlijke databasebehoeften.
Er zijn echter scripts waarmee de schaal baarheid voor één Azure SQL Database kan worden geautomatiseerd. Zie [PowerShell gebruiken voor het controleren en schalen van één Azure SQL-database](scripts/sql-database-monitor-and-scale-database-powershell.md) voor een voorbeeld.

U kunt steeds de [DTU-servicelagen](sql-database-service-tiers-dtu.md) of [vCore-kenmerken](sql-database-vcore-resource-limits-single-databases.md) wijzigen met minimale downtime voor de toepassing (meestal minder dan vier seconden). Voor veel bedrijven en apps is het kunnen maken van databases en het naar wens omhoog of omlaag schalen van de prestaties al voldoende, vooral als de gebruikspatronen redelijk voorspelbaar zijn. Bij onvoorspelbare gebruikspatronen kan het echter lastig zijn uw kosten en bedrijfsmodel effectief te beheren. Voor dit scenario gebruikt u een elastische pool met een bepaald aantal Edtu's die worden gedeeld door meerdere data bases in de groep.

![Inleiding tot SQL Database: DTU's van individuele database per laag en niveau](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

De drie soorten Azure SQL Database bieden de mogelijkheid om uw data bases dynamisch te schalen:

- Met [één data base](sql-database-single-database-scale.md)kunt u [DTU](sql-database-dtu-resource-limits-single-databases.md) -of [vCore](sql-database-vcore-resource-limits-single-databases.md) -modellen gebruiken om de maximale hoeveelheid resources te definiëren die aan elke Data Base wordt toegewezen.
- Een [beheerd exemplaar](sql-database-managed-instance.md) gebruikt de modus [vCores](sql-database-managed-instance.md#vcore-based-purchasing-model) en stelt u in staat om Maxi maal CPU-kernen en het maximum aan opslag ruimte te definiëren die aan uw exemplaar zijn toegewezen. Alle data bases in het exemplaar delen de resources die aan het exemplaar zijn toegewezen.
- Met [elastische Pools](sql-database-elastic-pool-scale.md) kunt u de maximale resource limiet per groep data bases in de groep definiëren.

Het starten van een actie omhoog of omlaag schalen in een van de versies zou het data base-engine proces opnieuw starten en naar een andere virtuele machine verplaatsen, indien nodig. Het proces voor het verplaatsen van de data base-engine naar een nieuwe virtuele machine is **online proces** waar u uw bestaande Azure SQL database-service kunt blijven gebruiken terwijl het proces wordt uitgevoerd. Zodra de engine van de doel database volledig is geïnitialiseerd en gereed is voor het verwerken van de query's, worden de verbindingen [van de bron-naar de doel database-engine overgeschakeld](sql-database-single-database-scale.md#impact-of-changing-service-tier-or-rescaling-compute-size).

> [!NOTE]
> U kunt een korte verbinding verwachten wanneer het omhoog/omlaag schalen proces is voltooid. Als u [pogings logica hebt geïmplementeerd voor standaard tijdelijke fouten](sql-database-connectivity-issues.md#retry-logic-for-transient-errors), zult u de failover niet zien.

## <a name="alternative-scale-methods"></a>Alternatieve schaal methoden

Het schalen van resources is de eenvoudigste en meest efficiënte manier om de prestaties van uw data base te verbeteren zonder de data base-of toepassings code te wijzigen. In sommige gevallen is het mogelijk dat zelfs de hoogste service lagen, reken grootten en prestatie optimalisaties uw werk belasting op een geslaagde en rendabele manier niet kan verwerken. In dat geval hebt u de volgende extra opties om uw data base te schalen:

- [Uitschalen](sql-database-read-scale-out.md) is een functie die beschikbaar is in de locatie waar u een alleen-lezen replica van uw gegevens krijgt, waar u veeleisende alleen-lezen query's kunt uitvoeren, zoals rapporten. Met alleen-lezen replica wordt uw alleen-lezen werk belasting afgehandeld zonder het resource gebruik op uw primaire data base te beïnvloeden.
- [Data Base-sharding](sql-database-elastic-scale-introduction.md) is een reeks technieken waarmee u uw gegevens kunt splitsen in verschillende data bases en deze onafhankelijk kunnen schalen.

## <a name="next-steps"></a>Volgende stappen

- Zie [aanbevelingen voor prestaties zoeken en Toep assen](sql-database-advisor-portal.md)voor meer informatie over het verbeteren van de database prestaties door het wijzigen van de database code.
- Zie [Automatic tuning](sql-database-automatic-tuning.md)(Engelstalig) voor meer informatie over het door geven van ingebouwde data bases om uw data base te optimaliseren.
- Zie voor meer informatie over uitschalen van Lees bewerkingen in de Azure SQL Database-service het [gebruik van alleen-lezen replica's om werk belastingen met alleen-lezen query's te verdelen](sql-database-read-scale-out.md).
- Zie [uitschalen met Azure SQL database](sql-database-elastic-scale-introduction.md)voor meer informatie over een Data Base-sharding.

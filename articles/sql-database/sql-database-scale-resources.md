---
title: Bronnen schalen
description: In dit artikel wordt uitgelegd hoe u uw database schalen door toegewezen resources toe te voegen of te verwijderen.
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
ms.openlocfilehash: c4366b2718271b1e27325e6946c5016e9230cea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76835909"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Dynamisch schalen databasebronnen met minimale downtime

Azure SQL Database stelt u in staat om dynamisch meer resources toe te voegen aan uw database met minimale [downtime;](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/) Er is echter een switch over periode waarbij connectiviteit voor een korte tijd verloren gaat met de database, die kan worden beperkt met behulp van logica voor nieuwe apparaten.

## <a name="overview"></a>Overzicht

Wanneer de vraag naar uw app groeit van een handvol apparaten en klanten naar miljoenen, schaalt Azure SQL Database on the fly met minimale downtime. Schaalbaarheid is een van de belangrijkste kenmerken van PaaS waarmee u dynamisch meer resources aan uw service toevoegen wanneer dat nodig is. Met Azure SQL Database u eenvoudig resources (CPU-stroom, geheugen, IO-doorvoer en opslag) wijzigen die aan uw databases zijn toegewezen.

U prestatieproblemen beperken als gevolg van een groter gebruik van uw toepassing die niet kan worden opgelost met behulp van indexerings- of queryherschrijfmethoden. Door meer resources toe te voegen, u snel reageren wanneer uw database de huidige resourcelimieten bereikt en meer stroom nodig heeft om de binnenkomende werkbelasting te verwerken. Azure SQL Database stelt u ook in staat om de resources te schalen wanneer ze niet nodig zijn om de kosten te verlagen.

U hoeft zich geen zorgen te maken over de aanschaf van hardware en het wijzigen van de onderliggende infrastructuur. Schalen database kan eenvoudig worden gedaan via Azure portal met behulp van een schuifregelaar.

![Databaseprestaties schalen](media/sql-database-scalability/scale-performance.svg)

Azure SQL Database biedt het [Op DTU gebaseerde inkoopmodel](sql-database-service-tiers-dtu.md) en het [op vCore gebaseerde inkoopmodel.](sql-database-service-tiers-vcore.md)

- Het [op DTU gebaseerde aankoopmodel](sql-database-service-tiers-dtu.md) biedt een mix van compute-, geheugen- en IO-resources in drie servicelagen ter ondersteuning van lichtgewicht tot zwaargewicht databaseworkloads: Basic, Standard en Premium. Prestatieniveaus binnen elke laag bieden een andere combinatie van deze resources, waaraan u extra opslagbronnen kunt toevoegen.
- Met [het op vCore gebaseerde inkoopmodel](sql-database-service-tiers-vcore.md) u het aantal vCores, de hoeveelheid of het geheugen en de hoeveelheid en de snelheid van de opslag kiezen. Dit inkoopmodel biedt drie serviceniveaus: General Purpose, Business Critical en Hyperscale.

U uw eerste app bouwen op een kleine, enkele database tegen lage kosten per maand in de servicelaag Basic, Standard of General Purpose en vervolgens de servicelaag handmatig of programmatisch wijzigen in de servicelaag Premium of Business Critical om aan de servicelaag Premium of Business Critical te voldoen behoeften van uw oplossing. U kunt het prestatieniveau aanpassen zonder uitvaltijd voor uw app of voor uw klanten. Dankzij dynamische schaalbaarheid kan uw database op een transparante manier snel reageren op veranderende resourcevereisten en betaalt u alleen voor de resources die u nodig hebt wanneer u ze nodig.

> [!NOTE]
> Dynamische schaalbaarheid is iets anders dan automatisch schalen. Automatisch schalen is wanneer een service automatisch wordt geschaald op basis van criteria, terwijl dynamische schaalbaarheid handmatigschalen mogelijk maakt met een minimale downtime.

Eén Azure SQL Database ondersteunt handmatige dynamische schaalbaarheid, maar niet automatisch schalen. Voor een meer *automatische* ervaring zou u elastische pools kunnen gebruiken. Hiermee kunnen databases resources in een pool delen op basis van afzonderlijke databasebehoeften.
Er zijn echter scripts die kunnen helpen bij het automatiseren van schaalbaarheid voor één Azure SQL-database. Zie [PowerShell gebruiken voor het controleren en schalen van één Azure SQL-database](scripts/sql-database-monitor-and-scale-database-powershell.md) voor een voorbeeld.

U kunt steeds de [DTU-servicelagen](sql-database-service-tiers-dtu.md) of [vCore-kenmerken](sql-database-vcore-resource-limits-single-databases.md) wijzigen met minimale downtime voor de toepassing (meestal minder dan vier seconden). Voor veel bedrijven en apps is het kunnen maken van databases en het naar wens omhoog of omlaag schalen van de prestaties al voldoende, vooral als de gebruikspatronen redelijk voorspelbaar zijn. Bij onvoorspelbare gebruikspatronen kan het echter lastig zijn uw kosten en bedrijfsmodel effectief te beheren. Voor dit scenario gebruikt u een elastische pool met een bepaald aantal eDTU's die worden gedeeld tussen meerdere databases in de groep.

![Inleiding tot SQL Database: DTU's van individuele database per laag en niveau](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Alle drie de smaken van Azure SQL Database bieden een mogelijkheid om uw databases dynamisch te schalen:

- Met [één database](sql-database-single-database-scale.md)u [DTU-](sql-database-dtu-resource-limits-single-databases.md) of [vCore-modellen](sql-database-vcore-resource-limits-single-databases.md) gebruiken om een maximale hoeveelheid resources te definiëren die aan elke database worden toegewezen.
- Een [managed instance](sql-database-managed-instance.md) maakt gebruik van de [vCores-modus](sql-database-managed-instance.md#vcore-based-purchasing-model) en stelt u in staat om maximale CPU-cores en maximale opslagruimte aan uw instantie te definiëren. Alle databases in de instantie delen de resources die aan de instantie zijn toegewezen.
- [Met elastische groepen](sql-database-elastic-pool-scale.md) u de maximale resourcelimiet per groep databases in de groep definiëren.

Het initiëren van scale-up of schaal naar beneden actie in een van de smaken zou opnieuw opstarten database engine proces en verplaats het naar een andere virtuele machine indien nodig. Het verplaatsen van databaseengineproces naar een nieuwe virtuele machine is **online proces** waar u uw bestaande Azure SQL Database-service blijven gebruiken terwijl het proces aan de gang is. Zodra de doeldatabase-engine volledig is geïnitialiseerd en klaar is om de query's te verwerken, worden de verbindingen [overgeschakeld van bron naar doeldatabase-engine.](sql-database-single-database-scale.md#impact) 


> [!NOTE]
> U een korte verbindingsonderbreking verwachten wanneer het scale-up/scale down-proces is voltooid. Als u logica opnieuw proberen hebt geïmplementeerd [voor standaardtijdelijke fouten,](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)zult u de failover niet opmerken.

## <a name="alternative-scale-methods"></a>Alternatieve schaalmethoden

Het schalen van resources is de eenvoudigste en meest effectieve manier om de prestaties van uw database te verbeteren zonder de database of toepassingscode te wijzigen. In sommige gevallen kunnen zelfs de hoogste serviceniveaus, rekengroottes en prestatieoptimalisaties uw werkbelasting niet op een succesvolle en kosteneffectieve manier verwerken. In dat geval hebt u deze extra opties om uw database te schalen:

- [Read scale-out](sql-database-read-scale-out.md) is een beschikbare functie waarbij u één alleen-lezen replica van uw gegevens krijgt, waar u veeleisende alleen-lezen query's uitvoeren, zoals rapporten. Alleen-lezen replica verwerkt uw alleen-lezen werkbelasting zonder dat dit gevolgen heeft voor het gebruik van resources in uw primaire database.
- [Databasesharding](sql-database-elastic-scale-introduction.md) is een reeks technieken waarmee u uw gegevens in verschillende databases splitsen en onafhankelijk schalen.

## <a name="next-steps"></a>Volgende stappen

- Zie [Prestatieaanbevelingen zoeken en toepassen](sql-database-advisor-portal.md)voor informatie over het verbeteren van de databaseprestaties door de databasecode te wijzigen.
- Zie [Automatisch afstemmen](sql-database-automatic-tuning.md)voor informatie over het optimaliseren van ingebouwde databaseinformatie.
- Zie voor informatie over Read Scale-out in de Azure SQL [Database-service hoe u alleen-lezen replica's gebruikt om alleen-lezen queryworkloads te laden.](sql-database-read-scale-out.md)
- Zie [Uitschalen met Azure SQL Database](sql-database-elastic-scale-introduction.md)voor informatie over een databasesharding.

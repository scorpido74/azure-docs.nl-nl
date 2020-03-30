---
title: Hoge beschikbaarheid
description: Meer informatie over de mogelijkheden en functies van de Azure SQL Database-service met hoge beschikbaarheid
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 10/14/2019
ms.openlocfilehash: b560cee23855d1c0e8a7b3c2cb9d82c184a1ebf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269651"
---
# <a name="high-availability-and-azure-sql-database"></a>Hoge beschikbaarheid en Azure SQL-database

Het doel van de architectuur met hoge beschikbaarheid in Azure SQL Database is om te garanderen dat uw database minimaal 99,99% van de tijd operationeel is (Voor meer informatie over specifieke SLA voor verschillende lagen raadpleegt u [SLA voor SQL Azure Database),](https://azure.microsoft.com/support/legal/sla/sql-database/)zonder u zorgen te maken over de impact van onderhoudsbewerkingen en uitval. Azure verwerkt automatisch kritieke onderhoudstaken, zoals patching, back-ups, Windows- en SQL-upgrades, evenals ongeplande gebeurtenissen zoals onderliggende hardware-, software- of netwerkfouten.  Wanneer de onderliggende SQL-instantie is gepatcht of mislukt, is de downtime niet merkbaar als u een nieuwe logica in uw app [gebruikt.](sql-database-develop-overview.md#resiliency) Azure SQL Database kan snel herstellen, zelfs in de meest kritieke omstandigheden, zodat uw gegevens altijd beschikbaar zijn.

De oplossing met hoge beschikbaarheid is ontworpen om ervoor te zorgen dat vastgelegde gegevens nooit verloren gaan als gevolg van storingen, dat onderhoudsbewerkingen geen invloed hebben op uw werkbelasting en dat de database geen enkel storingspunt in uw softwarearchitectuur is. Er zijn geen onderhoudsvensters of stilstandtijden waarvoor u de werkbelasting moet stoppen terwijl de database wordt bijgewerkt of onderhouden. 

Er zijn twee architecturale modellen met hoge beschikbaarheid die worden gebruikt in Azure SQL Database:

- Standaardbeschikbaarheidsmodel dat is gebaseerd op een scheiding van rekenkracht en opslag.  Het is afhankelijk van een hoge beschikbaarheid en betrouwbaarheid van de externe opslaglaag. Deze architectuur richt zich op budgetgerichte bedrijfstoepassingen die enige prestatiedegradatie tijdens onderhoudsactiviteiten kunnen verdragen.
- Premium beschikbaarheidsmodel dat is gebaseerd op een cluster van databaseengineprocessen. Het is gebaseerd op het feit dat er altijd een quorum van beschikbare database engine nodes. Deze architectuur richt zich op bedrijfskritieke toepassingen met hoge IO-prestaties, een hoge transactiesnelheid en garandeert minimale impact op de prestaties van uw werklast tijdens onderhoudsactiviteiten.

Azure SQL Database draait op de nieuwste stabiele versie van SQL Server Database Engine en Windows OS, en de meeste gebruikers zouden niet merken dat upgrades continu worden uitgevoerd.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Beschikbaarheid van basis-, standaard- en algemene serviceniveaus

Deze servicelagen maken gebruik van de standaardbeschikbaarheidsarchitectuur. De volgende afbeelding toont vier verschillende knooppunten met de gescheiden reken- en opslaglagen.

![Scheiding van rekenkracht en opslag](media/sql-database-high-availability/general-purpose-service-tier.png)

Het standaardbeschikbaarheidsmodel bevat twee lagen:

- Een stateloze rekenlaag `sqlservr.exe` die het proces uitvoert en alleen tijdelijke en cachegegevens bevat, zoals TempDB, modeldatabases op de gekoppelde SSD en cache, buffergroep en kolomarchiefgroep in het geheugen plannen. Dit stateloze knooppunt wordt beheerd door Azure `sqlservr.exe`Service Fabric die de status van het knooppunt initialiseert, de status van het knooppunt initieert en indien nodig failover uitvoert naar een ander knooppunt.
- Een stateful gegevenslaag met de databasebestanden (.mdf/.ldf) die zijn opgeslagen in Azure Blob-opslag. Azure blob-opslag heeft de ingebouwde beschikbaarheid en redundantiefunctie voor gegevens. Het garandeert dat elke record in het logboekbestand of de pagina in het gegevensbestand wordt bewaard, zelfs als het SQL Server-proces vastloopt.

Wanneer de databaseengine of het besturingssysteem wordt geüpgraded of een fout wordt gedetecteerd, verplaatst Azure Service Fabric het stateloze SQL Server-proces naar een ander stateless compute-knooppunt met voldoende vrije capaciteit. Gegevens in Azure Blob-opslag worden niet beïnvloed door de verplaatsing en de gegevens-/logboekbestanden worden gekoppeld aan het nieuw geïnitialiseerde SQL Server-proces. Dit proces garandeert 99,99% beschikbaarheid, maar een zware werkbelasting kan enige prestatiedegradatie ervaren tijdens de overgang, omdat de nieuwe SQL Server-instantie begint met koude cache.

## <a name="premium-and-business-critical-service-tier-availability"></a>Beschikbaarheid van premium- en bedrijfskritieke serviceniveaus

Premium- en Business Critical-servicelagen maken gebruik van het Premium-beschikbaarheidsmodel, dat compute resources (SQL Server Database Engine-proces) en opslag (lokaal gekoppelde SSD) integreert op één knooppunt. Hoge beschikbaarheid wordt bereikt door zowel rekenkracht als opslag te repliceren naar extra knooppunten die een cluster met drie tot vier knooppunten maken. 

![Cluster van database-engineknooppunten](media/sql-database-high-availability/business-critical-service-tier.png)

De onderliggende databasebestanden (.mdf/.ldf) worden op de bijgevoegde SSD-opslag geplaatst om uw workload zeer laag te latentie IO te bieden. Hoge beschikbaarheid wordt geïmplementeerd met behulp van een technologie die lijkt op SQL Server [Always On Availability Groups.](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) Het cluster bevat één primaire replica (SQL Server-proces) die toegankelijk is voor lees-schrijfklantworkloads en maximaal drie secundaire replica's (rekenkracht en opslag) die kopieën van gegevens bevatten. Het primaire knooppunt duwt voortdurend wijzigingen in de secundaire knooppunten in volgorde en zorgt ervoor dat de gegevens worden gesynchroniseerd met ten minste één secundaire replica voordat u elke transactie begaat. Dit proces garandeert dat als het primaire knooppunt om welke reden dan ook crasht, er altijd een volledig gesynchroniseerd knooppunt is om niet naar toe te gaan. De failover wordt geïnitieerd door de Azure Service Fabric. Zodra de secundaire replica het nieuwe primaire knooppunt wordt, wordt een andere secundaire replica gemaakt om ervoor te zorgen dat het cluster voldoende knooppunten heeft (quorumset). Zodra failover is voltooid, worden SQL-verbindingen automatisch doorgestuurd naar het nieuwe primaire knooppunt.

Als extra voordeel omvat het premium beschikbaarheidsmodel de mogelijkheid om alleen-lezen SQL-verbindingen om te leiden naar een van de secundaire replica's. Deze functie heet [Read Scale-Out](sql-database-read-scale-out.md). Het biedt 100% extra rekencapaciteit zonder extra kosten voor off-load read-only bewerkingen, zoals analytische workloads, van de primaire replica.

## <a name="hyperscale-service-tier-availability"></a>Beschikbaarheid van hyperscale servicelagen

De hyperscale service tier architectuur wordt beschreven in [Distributed functions architectuur](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture). 

![Hyperscale functionele architectuur](./media/sql-database-hyperscale/hyperscale-architecture.png)

Het beschikbaarheidsmodel in Hyperscale bevat vier lagen:

- Een stateloze rekenlaag `sqlservr.exe` die de processen uitvoert en alleen tijdelijke en cachegegevens bevat, zoals niet-dekkende RBPEX-cache, TempDB, modeldatabase, enz. Deze statusloze laag bevat de primaire compute-replica en optioneel een aantal secundaire compute-replica's die als failoverdoelen kunnen dienen.
- Een stateloze opslaglaag gevormd door paginaservers. Deze laag is de gedistribueerde `sqlservr.exe` opslagengine voor de processen die op de compute-replica's worden uitgevoerd. Elke paginaserver bevat alleen tijdelijke en in de cache opgeslagen gegevens, zoals rbpex-cache op de bijgevoegde SSD en gegevenspagina's die in het geheugen zijn opgeslagen. Elke paginaserver heeft een gekoppelde paginaserver in een actief actieve configuratie om taakverdeling, redundantie en hoge beschikbaarheid te bieden.
- Een stateful transaction log storage layer gevormd door het compute node met het Log-serviceproces, de landingszone voor transactielogboeken en opslag op lange termijn. Landingszone en opslag op lange termijn maken gebruik van Azure Storage, dat beschikbaarheid en [redundantie](https://docs.microsoft.com/azure/storage/common/storage-redundancy) biedt voor transactielogboeken, waardoor de duurzaamheid van gegevens voor vastgelegde transacties wordt gewaarborgd.
- Een stateful data-opslaglaag met de databasebestanden (.mdf/.ndf) die zijn opgeslagen in Azure Storage en worden bijgewerkt door paginaservers. Deze laag maakt gebruik van gegevensbeschikbaarheids- en [redundantiefuncties](https://docs.microsoft.com/azure/storage/common/storage-redundancy) van Azure Storage. Het garandeert dat elke pagina in een gegevensbestand wordt bewaard, zelfs als processen in andere lagen hyperscale-architectuur crashen of als compute nodes mislukken.

Compute-knooppunten in alle Hyperscale-lagen worden uitgevoerd op Azure Service Fabric, dat de status van elk knooppunt regelt en waar nodig failovers uitvoert naar beschikbare gezonde knooppunten.

Zie [Database Hoge beschikbaarheid in Hyperscale](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale)voor meer informatie over hoge beschikbaarheid in Hyperscale.

## <a name="zone-redundant-configuration"></a>Redundante zoneconfiguratie

Standaard wordt het cluster knooppunten voor het premium beschikbaarheidsmodel gemaakt in hetzelfde datacenter. Met de introductie van [Azure Availability Zones](../availability-zones/az-overview.md)kan SQL Database verschillende replica's van de Business Critical-database plaatsen in verschillende beschikbaarheidszones in dezelfde regio. Om een enkel storingspunt te elimineren, wordt de besturingsring ook in meerdere zones gedupliceerd als drie gatewayringen (GW). De routering naar een specifieke gatewayring wordt beheerd door [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Omdat de zoneredundante configuratie in de servicelagen Premium of Business Critical geen extra databaseredundantie creëert, u deze zonder extra kosten inschakelen. Door een zoneredundante configuratie te selecteren, u uw Premium- of Business Critical-databases bestand maken tegen een veel grotere reeks fouten, waaronder catastrofale datacenterstoringen, zonder wijzigingen in de toepassingslogica. U ook bestaande Premium- of Business Critical-databases of -groepen converteren naar de redundante zoneconfiguratie.

Omdat de zoneredundante databases replica's hebben in verschillende datacenters met enige afstand tussen deze databases, kan de verhoogde netwerklatentie de committijd verhogen en dus de prestaties van sommige OLTP-workloads beïnvloeden. U altijd terugkeren naar de configuratie met één zone door de instelling voor zoneredundantie uit te schakelen. Dit proces is een online bewerking die vergelijkbaar is met de upgrade van de reguliere servicelaag. Aan het einde van het proces wordt de database of groep gemigreerd van een zoneredundante ring naar een ring met één zone of vice versa.

> [!IMPORTANT]
> Zoneredundante databases en elastische pools worden momenteel alleen ondersteund in de servicelagen Premium en Business Critical in bepaalde regio's. Bij het gebruik van de laag Bedrijfskritiek is zoneredundante configuratie alleen beschikbaar wanneer de Gen5-rekenhardware is geselecteerd. Zie Ondersteuning per [regio voor](../availability-zones/az-overview.md#services-support-by-region)actuele informatie over de regio's die redundante databases in de zone ondersteunen.  
> Deze functie is niet beschikbaar in de instantie Beheerd.

De zoneredundante versie van de architectuur met hoge beschikbaarheid wordt geïllustreerd door het volgende diagram:

![architectuurzone met hoge beschikbaarheid overbodig](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Versnelde databaseherstel (ADR)

[Accelerated Database Recovery (ADR)](sql-database-accelerated-database-recovery.md) is een nieuwe SQL-databaseenginedie de beschikbaarheid van gegevens aanzienlijk verbetert, vooral in aanwezigheid van langlopende transacties. ADR is momenteel beschikbaar voor afzonderlijke databases, elastische pools en Azure SQL Data Warehouse.

## <a name="testing-application-fault-resiliency"></a>Het testen van toepassingsfouttolerantie

Hoge beschikbaarheid is een fundamenteel onderdeel van azure SQL Database-platform dat transparant werkt voor uw databasetoepassing. We erkennen echter dat u mogelijk wilt testen hoe de automatische failoverbewerkingen die tijdens geplande of ongeplande gebeurtenissen zijn gestart, van invloed kunnen zijn op de toepassing voordat u deze in productie neemt. U een speciale API aanroepen om een database of een elastische pool opnieuw op te starten, wat op zijn beurt een failover zal veroorzaken. In het geval van een zoneredundante database of elastische groep, zou de API-aanroep resulteren in het omleiden van clientverbindingen naar het nieuwe primaire in een beschikbaarheidszone die verschilt van de beschikbaarheidszone van het oude primaire. Dus naast het testen hoe failover invloed heeft op bestaande databasesessies, u ook controleren of het de end-to-end prestaties verandert als gevolg van wijzigingen in de netwerklatentie. Omdat de herstartbewerking opdringerig is en een groot aantal van hen het platform kan benadrukken, is er elke 30 minuten slechts één failovergesprek toegestaan voor elke database of elastische pool. 

Een failover kan worden gestart met rest API of PowerShell. Zie [Databasefailover](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover) en [Elastic pool failover voor](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover)REST API. Zie [Invoke-AzSqlDatabaseFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover) en [Invoke-AzSqlElasticPoolFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)voor PowerShell . De REST API-aanroepen kunnen ook vanuit Azure CLI worden uitgevoerd met de [az-restopdracht.](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-rest)

> [!IMPORTANT]
> De opdracht Failover is momenteel niet beschikbaar in de servicelaag Hyperscale en voor Beheerde instantie.

## <a name="conclusion"></a>Conclusie

Azure SQL Database beschikt over een ingebouwde oplossing met hoge beschikbaarheid, die nauw is geïntegreerd met het Azure-platform. Het is afhankelijk van Service Fabric voor foutdetectie en -herstel, van Azure Blob-opslag voor gegevensbescherming en beschikbaarheidszones voor een hogere fouttolerantie. Bovendien maakt Azure SQL-database gebruik van de Always On Availability Group-technologie van SQL Server voor replicatie en failover. De combinatie van deze technologieën stelt toepassingen in staat om de voordelen van een gemengd opslagmodel volledig te realiseren en de meest veeleisende SLA's te ondersteunen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Availability Zones](../availability-zones/az-overview.md)
- Meer informatie over [servicestof](../service-fabric/service-fabric-overview.md)
- Meer informatie over [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
- Zie [Business Continuity](sql-database-business-continuity.md) voor meer opties voor hoge beschikbaarheid en disaster recovery

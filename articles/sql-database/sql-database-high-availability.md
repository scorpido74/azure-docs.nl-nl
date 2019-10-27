---
title: Hoge Beschik baarheid-Azure SQL Database-Service | Microsoft Docs
description: Meer informatie over de mogelijkheden en functies van de Azure SQL Database-Service voor hoge Beschik baarheid
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
ms.openlocfilehash: ab3971b4fb6065701d693debf55242be7b15295e
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965978"
---
# <a name="high-availability-and-azure-sql-database"></a>Hoge Beschik baarheid en Azure SQL Database

Het doel van de architectuur met hoge Beschik baarheid in Azure SQL Database is om te garanderen dat uw data base 99,99% van de tijd actief is, zonder dat u zich zorgen hoeft te maken over de gevolgen van onderhouds bewerkingen en storingen. Azure beheert automatisch essentiële onderhouds taken, zoals patches, back-ups, Windows-en SQL-upgrades, en niet-geplande gebeurtenissen, zoals onderliggende hardware, software of netwerk fouten.  Wanneer er een patch wordt uitgevoerd voor het onderliggende SQL-exemplaar of een failover wordt uitgevoerd, is de downtime niet merkbaar als u [probeert logica](sql-database-develop-overview.md#resiliency) in uw app te gebruiken. Azure SQL Database kan snel worden hersteld, zelfs in de meest kritieke omstandigheden, zodat uw gegevens altijd beschikbaar zijn.

De oplossing voor hoge Beschik baarheid is zodanig ontworpen dat doorgevoerde gegevens nooit verloren gaan als gevolg van fouten, dat de onderhouds bewerkingen geen invloed hebben op uw werk belasting en dat de data base geen Single Point of Failure is in uw software architectuur. Er zijn geen onderhouds Vensters of downtime die vereisen dat u de werk belasting stopt wanneer de data base wordt bijgewerkt of onderhouden. 

Er zijn twee architectuur modellen met hoge Beschik baarheid die worden gebruikt in Azure SQL Database:

- Standaard beschikbaarheids model dat is gebaseerd op een schei ding van Compute en opslag.  Het is afhankelijk van de hoge Beschik baarheid en betrouw baarheid van de laag voor externe opslag. Deze architectuur streeft naar budget gerichte zakelijke toepassingen die tijdens onderhouds activiteiten een prestatie vermindering kunnen verdragen.
- Premium-beschikbaarheids model dat is gebaseerd op een cluster met data base-engine processen. Het is afhankelijk van het feit dat er altijd een quorum van de beschik bare data base-engine knooppunten is. Deze architectuur streeft naar essentiële bedrijfs toepassingen met hoge i/o-prestaties, een hoge transactie snelheid en garandeert minimale prestatie gevolgen voor uw werk belasting tijdens onderhouds activiteiten.

Azure SQL Database wordt uitgevoerd op de nieuwste stabiele versie van SQL Server data base engine en Windows-besturings systeem, en de meeste gebruikers merken niet dat upgrades voortdurend worden uitgevoerd.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Beschik baarheid van Basic, Standard en Algemeen service tier

Deze service lagen maken gebruik van de standaard beschikbaarheids architectuur. In de volgende afbeelding ziet u vier verschillende knoop punten met de gescheiden reken-en opslag lagen.

![Schei ding van Compute en opslag](media/sql-database-high-availability/general-purpose-service-tier.png)

Het standaard beschikbaarheids model bevat twee lagen:

- Een stateless Compute-laag die het `sqlservr.exe`-proces uitvoert en alleen tijdelijke en in de cache opgeslagen gegevens bevat, zoals TempDB, model databases op de gekoppelde SSD en de plannings cache, de buffer groep en de column Store-groep in het geheugen. Dit stateless knoop punt wordt gebruikt door Azure Service Fabric dat `sqlservr.exe` initialiseert, de status van het knoop punt beheert en een failover naar een ander knoop punt uitvoert, indien nodig.
- Een stateful gegevenslaag met de database bestanden (. MDF/. ldf) die zijn opgeslagen in Azure Blob Storage. Azure Blob-opslag heeft ingebouwde functie voor gegevens beschikbaarheid en redundantie. Hiermee wordt gegarandeerd dat elke record in het logboek bestand of de pagina in het gegevens bestand blijft behouden, zelfs als SQL Server proces vastloopt.

Wanneer de data base-engine of het besturings systeem wordt bijgewerkt, of als er een fout wordt gedetecteerd, wordt het stateless SQL Server proces door Azure Service Fabric verplaatst naar een ander stateless reken knooppunt met voldoende vrije capaciteit. Gegevens in Azure Blob-opslag worden niet beïnvloed door de verplaatsing en de gegevens/logboek bestanden zijn gekoppeld aan het zojuist geïnitialiseerde SQL Server proces. Dit proces garandeert een Beschik baarheid van 99,99%, maar een zware werk belasting kan tijdens de overgang enige prestatie vermindering veroorzaken, omdat de nieuwe SQL Server-instantie begint met koude cache.

## <a name="premium-and-business-critical-service-tier-availability"></a>Beschik baarheid van Premium-en Bedrijfskritiek-servicelaag

Premium-en Bedrijfskritiek-service lagen maken gebruik van het Premium-beschikbaarheids model, dat reken bronnen (SQL Server data base-engine proces) en opslag (lokaal gekoppelde SSD) integreert op één knoop punt. Hoge Beschik baarheid wordt bereikt door zowel Compute als Storage te repliceren naar extra knoop punten die een drie-tot vier knoop punt-cluster maken. 

![Cluster van data base-engine knooppunten](media/sql-database-high-availability/business-critical-service-tier.png)

De onderliggende database bestanden (. MDF/. ldf) worden geplaatst op de gekoppelde SSD-opslag om een lage latentie-IO te bieden voor uw werk belasting. Hoge Beschik baarheid wordt geïmplementeerd met behulp van een technologie die vergelijkbaar is met SQL Server AlwaysOn- [beschikbaarheids groepen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Het cluster bevat een enkele primaire replica (SQL Server proces) die toegankelijk is voor werk belastingen van de klant lezen en schrijven, en Maxi maal drie secundaire replica's (reken kracht en opslag) die kopieën van gegevens bevatten. Het primaire knoop punt duwt voortdurend wijzigingen in de secundaire knoop punten en zorgt ervoor dat de gegevens worden gesynchroniseerd naar ten minste één secundaire replica voordat elke trans actie wordt doorgevoerd. Dit proces zorgt ervoor dat als het primaire knoop punt om een of andere reden vastloopt, er altijd een volledig gesynchroniseerd knoop punt wordt uitgevoerd. De failover wordt gestart door de Azure-Service Fabric. Zodra de secundaire replica het nieuwe primaire knoop punt wordt, wordt er een andere secundaire replica gemaakt om ervoor te zorgen dat het cluster voldoende knoop punten (quorum set) heeft. Zodra de failover is voltooid, worden SQL-verbindingen automatisch omgeleid naar het nieuwe primaire knoop punt.

Een extra voor deel is dat het Premium-beschikbaarheids model de mogelijkheid biedt om alleen-lezen SQL-verbindingen om te leiden naar een van de secundaire replica's. Deze functie heet [Uitschalen lezen](sql-database-read-scale-out.md). Het biedt 100% extra reken capaciteit zonder extra kosten voor het laden van alleen-lezen bewerkingen, zoals analytische werk belastingen, van de primaire replica.

## <a name="hyperscale-service-tier-availability"></a>Beschik baarheid van grootschalige-servicelaag

De grootschalige is een beschrijving van de architectuur van [gedistribueerde functies](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture). 

![Functionele architectuur grootschalige](./media/sql-database-hyperscale/hyperscale-architecture.png)

Het beschikbaarheids model in grootschalige bevat vier lagen:

- Een stateless Compute-laag die de `sqlservr.exe`-processen uitvoert en alleen tijdelijke en in de cache opgeslagen gegevens bevat, zoals niet-bedekkende RBPEX-cache, TempDB, model database, enzovoort, op de gekoppelde SSD en de plannings cache, buffer groep en de column Store-groep in het geheugen. Deze stateless laag bevat de primaire Compute-replica en optioneel een aantal secundaire Compute-replica's die als failover-doelen kunnen fungeren.
- Een stateless opslagbus gevormd door pagina servers. Deze laag is de gedistribueerde opslag engine voor de `sqlservr.exe`-processen die worden uitgevoerd op de reken replica's. Elke pagina Server bevat alleen tijdelijke en in de cache opgeslagen gegevens, zoals het bedekken van RBPEX cache op de gekoppelde SSD en gegevens pagina's in het cache geheugen. Elke pagina Server heeft een gekoppelde pagina server in een actief/actief-configuratie om taak verdeling, redundantie en hoge Beschik baarheid te bieden.
- Een stateful opslag laag voor transactie logboeken, gevormd door het reken knooppunt waarop het logboek service proces wordt uitgevoerd, de overloop zone voor het transactie logboek en de lange termijn opslag van transactie Logboeken. Voor de opslag zone en de lange termijn wordt Azure Storage gebruikt. Dit biedt Beschik baarheid en [Redundantie](https://docs.microsoft.com/azure/storage/common/storage-redundancy) voor het transactie logboek en zorgt voor de duurzaamheid van gegevens voor doorgevoerde trans acties.
- Een stateful gegevensopslag laag met de database bestanden (. MDF/. ndf) die zijn opgeslagen in Azure Storage en worden bijgewerkt door pagina servers. Deze laag maakt gebruik van de functies voor Beschik baarheid en [Redundantie](https://docs.microsoft.com/azure/storage/common/storage-redundancy) van gegevens van Azure Storage. Hiermee wordt gegarandeerd dat elke pagina in een gegevens bestand blijft behouden, zelfs als processen in andere lagen van de grootschalige-architectuur vastlopen of als reken knooppunten mislukken.

Reken knooppunten in alle grootschalige-lagen worden uitgevoerd op Azure Service Fabric, waarmee de status van elk knoop punt wordt gecontroleerd en waar nodig failovers worden uitgevoerd naar beschik bare, gezonde knoop punten.

Zie voor meer informatie over hoge Beschik baarheid in grootschalige [Data Base hoge Beschik baarheid in grootschalige](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale).

## <a name="zone-redundant-configuration"></a>Zone redundante configuratie

Het cluster met knoop punten voor het Premium-beschikbaarheids model wordt standaard in hetzelfde Data Center gemaakt. Met de introductie van [Azure-beschikbaarheidszones](../availability-zones/az-overview.md)kan SQL database verschillende replica's van de bedrijfskritiek-data base plaatsen in verschillende beschikbaarheids zones in dezelfde regio. Om een Single Point of Failure te elimineren, wordt de controle ring ook gedupliceerd over meerdere zones als drie gateway ringen (GW). De route ring naar een specifieke gateway ring wordt beheerd door [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Omdat de zone redundante configuratie in de service lagen van Premium of Bedrijfskritiek geen extra database redundantie maakt, kunt u deze zonder extra kosten inschakelen. Als u een zone redundante configuratie selecteert, kunt u uw Premium-of Bedrijfskritiek-data bases tot een veel grotere set storingen leiden, inclusief een onherstelbare uitval van het Data Center, zonder dat u de toepassings logica hoeft te wijzigen. U kunt ook bestaande Premium-of Bedrijfskritiek-data bases of-groepen converteren naar de zone redundante configuratie.

Omdat de redundante data bases van de zone replica's hebben in verschillende data centers met een aantal onderlinge afstanden, kan de verhoogde netwerk latentie de doorvoer tijd verhogen en de prestaties van bepaalde OLTP-workloads beïnvloeden. U kunt altijd terugkeren naar de configuratie met één zone door de instelling voor zone redundantie uit te scha kelen. Dit proces is een online bewerking die vergelijkbaar is met de normale upgrade van de servicelaag. Aan het einde van het proces wordt de data base of groep gemigreerd van een redundante ring zone naar een enkele zone ring of andersom.

> [!IMPORTANT]
> Zone redundante data bases en elastische Pools worden momenteel alleen ondersteund in de service lagen Premium en Bedrijfskritiek in regio's selecteren. Wanneer u de laag Bedrijfskritiek gebruikt, is de redundante configuratie van de zone alleen beschikbaar wanneer de GEN5 Compute-hardware is geselecteerd. Zie [Services ondersteunen per regio](../availability-zones/az-overview.md#services-support-by-region)voor actuele informatie over de regio's die zone redundante data bases ondersteunen.  
> Deze functie is niet beschikbaar in een beheerd exemplaar.

De zone redundante versie van de architectuur met hoge Beschik baarheid wordt geïllustreerd in het volgende diagram:

![architectuur zone met hoge Beschik baarheid, redundant](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Versneld database herstel (ADR)

[Versneld database herstel (ADR)](sql-database-accelerated-database-recovery.md) is een nieuwe functie van SQL database engine waarmee de beschik baarheid van de data base aanzienlijk wordt verbeterd, met name in de aanwezigheid van langlopende trans acties. ADR is momenteel beschikbaar voor afzonderlijke data bases, elastische Pools en Azure SQL Data Warehouse.

## <a name="testing-application-fault-resiliency"></a>Toepassings fout tolerantie testen

Hoge Beschik baarheid is een fundamenteel onderdeel van Azure SQL Database platform dat transparant werkt voor uw database toepassing. We erkennen echter dat u wellicht wilt testen hoe de automatische failover-bewerkingen die worden geïnitieerd tijdens geplande of niet-geplande gebeurtenissen, van invloed zijn op de toepassing voordat u deze implementeert voor productie. U kunt een speciale API aanroepen om een Data Base of een elastische pool opnieuw op te starten, waardoor een failover wordt geactiveerd. In het geval van een zone redundante data base of elastische pool zou de API-aanroep ertoe leiden dat client verbindingen worden omgeleid naar de nieuwe primaire in een beschikbaarheids zone die afwijkt van de beschikbaarheids zone van de oude primaire. Naast het testen van de manier waarop failover van invloed is op bestaande database sessies, kunt u ook controleren of de end-to-end-prestaties worden gewijzigd vanwege wijzigingen in de netwerk latentie. Omdat de computer opnieuw moet worden opgestart, is er voor elke Data Base of elastische pool slechts één failover-aanroep elke 30 minuten toegestaan. 

Een failover kan worden gestart met behulp van REST API of Power shell. Zie failover van een [Data Base](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover) en een [elastische pool](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover)voor rest API. Zie [invoke-AzSqlDatabaseFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover) en [invoke-AzSqlElasticPoolFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)voor Power shell. De REST API-aanroepen kunnen ook vanuit Azure CLI worden gemaakt met behulp van [AZ rest](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-rest) Command.

> [!IMPORTANT]
> De opdracht failover is momenteel niet beschikbaar in de grootschalige en voor het beheerde exemplaar.

## <a name="conclusion"></a>Conclusie

Azure SQL Database bevat een ingebouwde oplossing voor hoge Beschik baarheid, die nauw is geïntegreerd met het Azure-platform. Het is afhankelijk van Service Fabric voor fout detectie en herstel, in Azure Blob-opslag voor gegevens beveiliging en op Beschikbaarheidszones voor hogere fout tolerantie. Bovendien maakt Azure SQL database gebruik van de technologie van de AlwaysOn-beschikbaarheids groep van SQL Server voor replicatie en failover. De combi natie van deze technologieën stelt toepassingen in staat om de voor delen van een gemengd opslag model volledig te benutten en de meest veeleisende Sla's te ondersteunen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure-beschikbaarheidszones](../availability-zones/az-overview.md)
- Meer informatie over [service Fabric](../service-fabric/service-fabric-overview.md)
- Meer informatie over [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
- Zie [bedrijfs continuïteit](sql-database-business-continuity.md) voor meer opties voor hoge Beschik baarheid en herstel na nood gevallen.

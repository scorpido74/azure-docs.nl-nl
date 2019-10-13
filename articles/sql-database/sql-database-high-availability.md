---
title: Hoge Beschik baarheid-Azure SQL Database-Service | Microsoft Docs
description: Meer informatie over de mogelijkheden en functies van de Azure SQL Database-Service voor hoge Beschik baarheid
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 06/10/2019
ms.openlocfilehash: 54994dd626df23694ea372d4a662d2b4fb051fc8
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285760"
---
# <a name="high-availability-and-azure-sql-database"></a>Hoge Beschik baarheid en Azure SQL Database

Het doel van de architectuur met hoge Beschik baarheid in Azure SQL Database is om te garanderen dat uw data base 99,99% van de tijd actief is, zonder dat u zich zorgen hoeft te maken over de gevolgen van onderhouds bewerkingen en storingen. Azure beheert automatisch essentiële onderhouds taken, zoals patches, back-ups, Windows-en SQL-upgrades, en niet-geplande gebeurtenissen, zoals onderliggende hardware, software of netwerk storingen.  Wanneer er een patch wordt uitgevoerd voor het onderliggende SQL-exemplaar of een failover wordt uitgevoerd, is de downtime niet merkbaar als u [probeert logica](sql-database-develop-overview.md#resiliency) in uw app te gebruiken. Azure SQL Database kan snel worden hersteld, zelfs in de meest kritieke omstandigheden, zodat uw gegevens altijd beschikbaar zijn.

De oplossing voor hoge Beschik baarheid is zodanig ontworpen dat doorgevoerde gegevens nooit verloren gaan als gevolg van fouten, dat de onderhouds bewerkingen geen invloed hebben op uw werk belasting en dat de data base geen Single Point of Failure is in uw software architectuur. Er zijn geen onderhouds Vensters of downtime die vereisen dat u de werk belasting stopt wanneer de data base wordt bijgewerkt of onderhouden. 

Er zijn twee architectuur modellen met hoge Beschik baarheid die worden gebruikt in Azure SQL Database:

- Standaard beschikbaarheids model dat is gebaseerd op een schei ding van Compute en opslag.  Het is afhankelijk van de hoge Beschik baarheid en betrouw baarheid van de laag voor externe opslag. Deze architectuur streeft naar budget gerichte zakelijke toepassingen die tijdens onderhouds activiteiten een prestatie vermindering kunnen verdragen.
- Premium-beschikbaarheids model dat is gebaseerd op een cluster met data base-engine processen. Het is afhankelijk van het feit dat er altijd een quorum van de beschik bare data base-engine knooppunten is. Deze architectuur streeft naar essentiële bedrijfs toepassingen met hoge i/o-prestaties, een hoge transactie snelheid en garandeert minimale prestatie gevolgen voor uw werk belasting tijdens onderhouds activiteiten.

Azure SQL Database wordt uitgevoerd op de nieuwste stabiele versie van SQL Server data base engine en Windows-besturings systeem, en de meeste gebruikers merken niet dat upgrades voortdurend worden uitgevoerd.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Beschik baarheid van Basic, Standard en Algemeen service tier

Deze service lagen maken gebruik van de standaard beschikbaarheids architectuur. In de volgende afbeelding ziet u vier verschillende knoop punten met de gescheiden reken-en opslag lagen.

![Schei ding van Compute en opslag](media/sql-database-high-availability/general-purpose-service-tier.png)

Het standaard beschikbaarheids model bevat twee lagen:

- Een stateless Compute-laag die het `sqlserver.exe`-proces uitvoert en alleen tijdelijke en in de cache opgeslagen gegevens op de gekoppelde SSD bevat, zoals TempDB, model database, plan cache, buffer groep en column Store-groep. Dit stateless knoop punt wordt gebruikt door Azure Service Fabric dat `sqlserver.exe` initialiseert, de status van het knoop punt beheert en een failover naar een ander knoop punt uitvoert, indien nodig.
- Een stateful gegevenslaag met de database bestanden (. MDF/. ldf) die zijn opgeslagen in Azure Blob Storage. Azure Blob-opslag heeft ingebouwde functie voor gegevens beschikbaarheid en redundantie. Hiermee wordt gegarandeerd dat elke record in het logboek bestand of de pagina in het gegevens bestand blijft behouden, zelfs als SQL Server proces vastloopt.

Wanneer de data base-engine of het besturings systeem wordt bijgewerkt, of als er een fout wordt gedetecteerd, wordt het stateless SQL Server proces door Azure Service Fabric verplaatst naar een ander stateless reken knooppunt met voldoende vrije capaciteit. Gegevens in Azure Blob-opslag worden niet beïnvloed door de verplaatsing en de gegevens/logboek bestanden zijn gekoppeld aan het zojuist geïnitialiseerde SQL Server proces. Dit proces garandeert een Beschik baarheid van 99,99%, maar een zware werk belasting kan tijdens de overgang enige prestatie vermindering veroorzaken, omdat de nieuwe SQL Server-instantie begint met koude cache.

## <a name="premium-and-business-critical-service-tier-availability"></a>Beschik baarheid van Premium-en Bedrijfskritiek-servicelaag

Premium-en Bedrijfskritiek-service lagen maken gebruik van het Premium-beschikbaarheids model, dat reken bronnen (SQL Server data base-engine proces) en opslag (lokaal gekoppelde SSD) integreert op één knoop punt. Hoge Beschik baarheid wordt bereikt door zowel Compute als Storage te repliceren naar extra knoop punten die een drie-tot vier knoop punt-cluster maken. 

![Cluster van data base-engine knooppunten](media/sql-database-high-availability/business-critical-service-tier.png)

De onderliggende database bestanden (. MDF/. ldf) worden geplaatst op de gekoppelde SSD-opslag om een lage latentie-IO te bieden voor uw werk belasting. Hoge Beschik baarheid wordt geïmplementeerd met behulp van een technologie die vergelijkbaar is met SQL Server AlwaysOn- [beschikbaarheids groepen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Het cluster bevat een enkele primaire replica (SQL Server proces) die toegankelijk is voor werk belastingen van de klant lezen en schrijven, en Maxi maal drie secundaire replica's (reken kracht en opslag) die kopieën van gegevens bevatten. Het primaire knoop punt duwt voortdurend wijzigingen in de secundaire knoop punten en zorgt ervoor dat de gegevens worden gesynchroniseerd naar ten minste één secundaire replica voordat elke trans actie wordt doorgevoerd. Dit proces zorgt ervoor dat als het primaire knoop punt om een of andere reden vastloopt, er altijd een volledig gesynchroniseerd knoop punt wordt uitgevoerd. De failover wordt gestart door de Azure-Service Fabric. Zodra de secundaire replica het nieuwe primaire knoop punt wordt, wordt er een andere secundaire replica gemaakt om ervoor te zorgen dat het cluster voldoende knoop punten (quorum set) heeft. Zodra de failover is voltooid, worden SQL-verbindingen automatisch omgeleid naar het nieuwe primaire knoop punt.

Een extra voor deel is dat het Premium-beschikbaarheids model de mogelijkheid biedt om alleen-lezen SQL-verbindingen om te leiden naar een van de secundaire replica's. Deze functie heet [Uitschalen lezen](sql-database-read-scale-out.md). Het biedt 100% extra reken capaciteit zonder extra kosten voor het laden van alleen-lezen bewerkingen, zoals analytische werk belastingen, van de primaire replica.

### <a name="zone-redundant-configuration"></a>Zone redundante configuratie

Het cluster met knoop punten voor het Premium-beschikbaarheids model wordt standaard in hetzelfde Data Center gemaakt. Met de introductie van [Azure-beschikbaarheidszones](../availability-zones/az-overview.md)kan SQL database verschillende replica's van de bedrijfskritiek-data base plaatsen in verschillende beschikbaarheids zones in dezelfde regio. Om een Single Point of Failure te elimineren, wordt de controle ring ook gedupliceerd over meerdere zones als drie gateway ringen (GW). De route ring naar een specifieke gateway ring wordt beheerd door [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Omdat de zone redundante configuratie in de service lagen van Premium of Bedrijfskritiek geen extra database redundantie maakt, kunt u deze zonder extra kosten inschakelen. Als u een zone redundante configuratie selecteert, kunt u uw Premium-of Bedrijfskritiek-data bases tot een veel grotere set storingen leiden, inclusief een onherstelbare uitval van het Data Center, zonder dat u de toepassings logica hoeft te wijzigen. U kunt ook bestaande Premium-of Bedrijfskritiek-data bases of-groepen converteren naar de zone redundante configuratie.

Omdat de redundante data bases van de zone replica's hebben in verschillende data centers met een aantal onderlinge afstanden, kan de verhoogde netwerk latentie de doorvoer tijd verhogen en de prestaties van bepaalde OLTP-workloads beïnvloeden. U kunt altijd terugkeren naar de configuratie met één zone door de instelling voor zone redundantie uit te scha kelen. Dit proces is een online bewerking die vergelijkbaar is met de normale upgrade van de servicelaag. Aan het einde van het proces wordt de data base of groep gemigreerd van een redundante ring zone naar een enkele zone ring of andersom.

> [!IMPORTANT]
> Zone redundante data bases en elastische Pools worden momenteel alleen ondersteund in de service lagen Premium en Bedrijfskritiek in regio's selecteren. Wanneer u de laag Bedrijfskritiek gebruikt, is de redundante configuratie van de zone alleen beschikbaar wanneer de GEN5 Compute-hardware is geselecteerd. Zie [Services ondersteunen per regio](../availability-zones/az-overview.md#services-support-by-region)voor actuele informatie over de regio's die zone redundante data bases ondersteunen.  
> Deze functie is niet beschikbaar in een beheerd exemplaar.

De zone redundante versie van de architectuur met hoge Beschik baarheid wordt geïllustreerd in het volgende diagram:

![architectuur zone met hoge Beschik baarheid, redundant](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Versneld database herstel (ADR)

[Versneld database herstel (ADR)](sql-database-accelerated-database-recovery.md) is een nieuwe functie van SQL database engine waarmee de beschik baarheid van de data base aanzienlijk wordt verbeterd, met name in de aanwezigheid van langlopende trans acties. ADR is momenteel beschikbaar voor afzonderlijke data bases, elastische Pools en Azure SQL Data Warehouse.

## <a name="conclusion"></a>Conclusie

Azure SQL Database bevat een ingebouwde oplossing voor hoge Beschik baarheid, die nauw is geïntegreerd met het Azure-platform. Het is afhankelijk van Service Fabric voor fout detectie en herstel, in Azure Blob-opslag voor gegevens beveiliging en op Beschikbaarheidszones voor hogere fout tolerantie. Bovendien maakt Azure SQL database gebruik van de technologie van de AlwaysOn-beschikbaarheids groep van SQL Server voor replicatie en failover. De combi natie van deze technologieën stelt toepassingen in staat om de voor delen van een gemengd opslag model volledig te benutten en de meest veeleisende Sla's te ondersteunen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure-beschikbaarheidszones](../availability-zones/az-overview.md)
- Meer informatie over [service Fabric](../service-fabric/service-fabric-overview.md)
- Meer informatie over [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
- Zie [bedrijfs continuïteit](sql-database-business-continuity.md) voor meer opties voor hoge Beschik baarheid en herstel na nood gevallen.

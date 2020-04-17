---
title: Replica's lezen - Azure Database voor PostgreSQL - Single Server
description: In dit artikel wordt de leesreplicafunctie in Azure Database voor PostgreSQL - Single Server beschreven.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 545d04bdede76a6ce25c9e4665f39c01ff6caa73
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531980"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Replica's lezen in Azure Database voor PostgreSQL - Single Server

Met de functie leesreplica u gegevens van een Azure-database voor PostgreSQL-server repliceren naar een alleen-lezen server. U kunt van de hoofdserver naar maximaal vijf replica's repliceren. Replica's worden asynchroon bijgewerkt met de postgreSQL-engine native replicatietechnologie.

Replica's zijn nieuwe servers die u beheert die vergelijkbaar zijn met gewone Azure Database voor PostgreSQL-servers. Voor elke gelezen replica wordt u gefactureerd voor de ingerichte compute in vCores en opslag in GB/maand.

Meer informatie over het [maken en beheren van replica's.](howto-read-replicas-portal.md)

## <a name="when-to-use-a-read-replica"></a>Wanneer een gelezen replica gebruiken
De leesreplica-functie helpt de prestaties en de schaal van leesintensieve workloads te verbeteren. Leesworkloads kunnen worden geïsoleerd voor de replica's, terwijl schrijfworkloads naar het model kunnen worden geleid.

Een veelvoorkomend scenario is dat BI- en analytische workloads de gelezen replica gebruiken als gegevensbron voor rapportage.

Omdat replica's alleen-lezen zijn, verminderen ze de schrijfcapaciteitslasten op de master niet direct. Deze functie is niet gericht op schrijfintensieve workloads.

De functie leesreplica maakt gebruik van PostgreSQL asynchrone replicatie. De functie is niet bedoeld voor synchrone replicatiescenario's. Er zal een meetbare vertraging tussen de master en de replica. De gegevens op de replica worden uiteindelijk consistent met de gegevens op het model. Gebruik deze functie voor workloads die deze vertraging kunnen opvangen.

## <a name="cross-region-replication"></a>Replicatie tussen regio's
U een leesreplica maken in een andere regio dan uw hoofdserver. Replicatie tussen regio's kan handig zijn voor scenario's zoals noodherstelplanning of het dichter bij uw gebruikers brengen van gegevens.

U een hoofdserver hebben in een [Azure-database voor PostgreSQL-regio.](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql) Een hoofdserver kan een replica hebben in het gekoppelde gebied of de universele replicagebieden. In de onderstaande afbeelding ziet u welke replicaregio's beschikbaar zijn, afhankelijk van uw hoofdregio.

[![Replicaregio's lezen](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Universele replicaregio's
U altijd een leesreplica maken in een van de volgende regio's, ongeacht waar uw hoofdserver zich bevindt. Dit zijn de universele replicaregio's:

Australië Oost, Australië Zuidoost, Centraal VS, Oost-Azië, Oost-VS, Oost-VS 2, Japan Oost, Japan West, Korea Centraal, Korea Zuid, Noord-Centraal VS, Noord-Europa, Zuid-Centraal VS, Zuidoost-Azië, Verenigd Koninkrijk Zuid, Verenigd Koninkrijk West, West-Europa, West-VS.

*West US 2 is tijdelijk niet beschikbaar als replicalocatie voor verschillende regio's.


### <a name="paired-regions"></a>Gekoppelde regio's
Naast de universele replicaregio's u een leesreplica maken in het gekoppelde Azure-gebied van uw hoofdserver. Als u het paar van uw regio niet kent, u meer te weten komen in het [artikel Azure Paired Regions.](../best-practices-availability-paired-regions.md)

Als u replica's voor meerdere regio's gebruikt voor de planning voor noodherstel, raden we u aan de replica te maken in het gekoppelde gebied in plaats van in een van de andere regio's. Gekoppelde regio's vermijden gelijktijdige updates en prioriteren fysieke isolatie en dataresidency.  

Er zijn beperkingen te overwegen: 

* Regionale beschikbaarheid: Azure Database voor PostgreSQL is beschikbaar in West US 2, France Central, UAE North en Germany Central. Hun gekoppelde regio's zijn echter niet beschikbaar.
    
* Unidirectionele paren: Sommige Azure-regio's zijn slechts in één richting gekoppeld. Deze regio's omvatten West-India, Brazilië Zuid. 
   Dit betekent dat een hoofdserver in West-India een replica kan maken in Zuid-India. Een hoofdserver in Zuid-India kan echter geen replica maken in West-India. Dit komt omdat de secundaire regio van West-India Zuid-India is, maar de secundaire regio van Zuid-India is niet West-India.


## <a name="create-a-replica"></a>Replica's maken
Wanneer u de replicawerk voor maken start, wordt een lege Azure Database voor PostgreSQL-server gemaakt. De nieuwe server is gevuld met de gegevens die zich op de hoofdserver bevond. De creatietijd is afhankelijk van de hoeveelheid gegevens op de master en de tijd sinds de laatste wekelijkse volledige back-up. De tijd kan variëren van enkele minuten tot enkele uren.

Elke replica is ingeschakeld voor opslag [auto-groeien](concepts-pricing-tiers.md#storage-auto-grow). Met de functie automatisch groeien kan de replica gelijke tred houden met de gegevens die eraan worden gerepliceerd en een onderbreking in de replicatie voorkomen die wordt veroorzaakt door uit opslagfouten.

De functie leesreplica maakt gebruik van Fysieke replicatie van PostgreSQL, geen logische replicatie. Streaming replicatie met behulp van replicatieslots is de standaardbewerkingsmodus. Indien nodig wordt log shipping gebruikt om in te halen.

Meer informatie over het [maken van een gelezen replica in de Azure-portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Verbinding maken met een replica
Wanneer u een replica maakt, worden de firewallregels of het VNet-serviceeindpunt van de hoofdserver niet overgenomen. Deze regels moeten onafhankelijk van elkaar worden ingesteld voor de replica.

De replica neemt het beheerdersaccount over van de hoofdserver. Alle gebruikersaccounts op de hoofdserver worden gerepliceerd naar de gelezen replica's. U alleen verbinding maken met een gelezen replica met behulp van de gebruikersaccounts die beschikbaar zijn op de hoofdserver.

U verbinding maken met de replica met behulp van de hostnaam en een geldig gebruikersaccount, zoals u zou doen op een gewone Azure-database voor PostgreSQL-server. Voor een server met de naam **mijn replica** met de beheerder gebruikersnaam **myadmin**, u verbinding maken met de replica met behulp van psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Voer bij de prompt het wachtwoord voor het gebruikersaccount in.

## <a name="monitor-replication"></a>Replicatie controleren
Azure Database voor PostgreSQL biedt twee statistieken voor het bewaken van replicatie. De twee statistieken zijn **Max Lag Across Replicas** en **Replica Lag**. Zie het gedeelte Monitor een **replica** van het [artikel voor gelezen replica's](howto-read-replicas-portal.md)voor meer informatie over het weergeven van deze statistieken.

De statistiek **Max Lag Across Replicas** toont de vertraging in bytes tussen de master en de meest achterblijvende replica. Deze statistiek is alleen beschikbaar op de hoofdserver.

De statistiek **Replica Lag** toont de tijd sinds de laatst afgespeelde transactie. Als er geen transacties plaatsvinden op uw hoofdserver, weerspiegelt de statistiek deze vertraging. Deze statistiek is alleen beschikbaar voor replicaservers. Replica Lag wordt `pg_stat_wal_receiver` berekend vanuit de weergave:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Stel een waarschuwing in om u te informeren wanneer de replicavertraging een waarde bereikt die niet acceptabel is voor uw werkbelasting. 

Voor meer inzicht u de hoofdserver rechtstreeks opvragen om de replicatievertraging in bytes op alle replica's te krijgen.

In PostgreSQL versie 10:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

In PostgreSQL versie 9.6 en eerder:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Als een hoofdserver of leesreplica opnieuw wordt opgestart, wordt de tijd die nodig is om opnieuw te starten en in te halen weergegeven in de statistiek Replica Lag.

## <a name="stop-replication"></a>Replicatie stoppen
U de replicatie tussen een stramien en een replica stoppen. De stopactie zorgt ervoor dat de replica opnieuw wordt opgestart en de replicatie-instellingen worden verwijderd. Nadat replicatie is gestopt tussen een hoofdserver en een gelezen replica, wordt de replica een zelfstandige server. De gegevens in de zelfstandige server zijn de gegevens die beschikbaar waren op de replica op het moment dat de opdracht replicatie stoppen werd gestart. De standalone server haalt de hoofdserver niet in.

> [!IMPORTANT]
> De standalone server kan niet opnieuw worden omgezet in een replica.
> Voordat u de replicatie op een gelezen replica stopt, moet u ervoor zorgen dat de replica alle gegevens heeft die u nodig hebt.

Wanneer u de replicatie stopt, verliest de replica alle koppelingen naar het vorige stramien en andere replica's.

Meer informatie over het [stoppen van replicatie naar een replica](howto-read-replicas-portal.md).

## <a name="failover"></a>Failover
Er is geen geautomatiseerde failover tussen hoofd- en replicaservers. 

Aangezien replicatie asynchroon is, is er vertraging tussen het stramien en de replica. De hoeveelheid vertraging kan worden beïnvloed door een aantal factoren, zoals hoe zwaar de werkbelasting op de hoofdserver wordt uitgevoerd en de latentie tussen datacenters. In de meeste gevallen varieert de replicalag tussen enkele seconden tot een paar minuten. U uw werkelijke replicatievertraging bijhouden met behulp van de metrische *replicalag,* die beschikbaar is voor elke replica. Deze statistiek toont de tijd sinds de laatst afgespeelde transactie. We raden u aan te identificeren wat uw gemiddelde vertraging is door uw replica vertraging over een periode van tijd te observeren. U een waarschuwing instellen voor replicavertraging, zodat als deze buiten uw verwachte bereik valt, u actie ondernemen.

> [!Tip]
> Als u de replica niet overtreedt, geeft de vertraging op het moment dat u de replica loskoppelt van het model, aan hoeveel gegevens verloren zijn gegaan.

Als je eenmaal hebt besloten dat je wilt failover naar een replica, 

1. Replicatie naar de replica stoppen<br/>
   Deze stap is nodig om de replicaserver schrijfbewerkingen te laten accepteren. Als onderdeel van dit proces wordt de replicaserver opnieuw opgestart en losgekoppeld van de master. Zodra u de replicatie stop start, duurt het backendproces doorgaans ongeveer 2 minuten. Zie de sectie [replicatie stoppen](#stop-replication) van dit artikel om de implicaties van deze actie te begrijpen.
    
2. Richt uw toepassing op de (voormalige) replica<br/>
   Elke server heeft een unieke verbindingstekenreeks. Werk uw toepassing bij om naar de (voormalige) replica te wijzen in plaats van naar het model.
    
Zodra uw toepassing leest en schrijfbewerking met succes verwerkt, hebt u de failover voltooid. De hoeveelheid downtime die uw toepassingservaringen ervaren, is afhankelijk van wanneer u een probleem detecteert en de volgende stappen 1 en 2 voltooit.


## <a name="considerations"></a>Overwegingen

In deze sectie worden overwegingen over de leesreplicafunctie samengevat.

### <a name="prerequisites"></a>Vereisten
Voordat u een leesreplica `azure.replication_support` maakt, moet de parameter zijn ingesteld op **REPLICA** op de hoofdserver. Wanneer deze parameter wordt gewijzigd, is een serveropnieuw opstarten vereist om de wijziging van kracht te laten worden. De `azure.replication_support` parameter is alleen van toepassing op de niveaus Algemeen doel en Geheugengeoptimaliseerd.

### <a name="new-replicas"></a>Nieuwe replica's
Een gelezen replica wordt gemaakt als een nieuwe Azure Database voor PostgreSQL-server. Een bestaande server kan niet worden omgezet in een replica. U geen replica van een andere gelezen replica maken.

### <a name="replica-configuration"></a>Replicaconfiguratie
Er wordt een replica gemaakt met dezelfde reken- en opslaginstellingen als het stramien. Nadat een replica is gemaakt, kunnen verschillende instellingen onafhankelijk van de hoofdserver worden gewijzigd: rekengeneratie, vCores, opslag en back-upbewaarperiode. De prijscategorie kan ook onafhankelijk worden gewijzigd, behalve van of naar de basislaag.

> [!IMPORTANT]
> Voordat een hoofdinstelling wordt bijgewerkt naar een nieuwe waarde, werkt u de replicaconfiguratie bij naar een evengrote of grotere waarde. Met deze actie wordt ervoor gezorgd dat in de replica alle wijzigingen worden doorgevoerd die in de hoofdserver zijn aangebracht.

PostgreSQL vereist dat `max_connections` de waarde van de parameter op de gelezen replica groter is dan of gelijk is aan de hoofdwaarde; Anders wordt de replica niet gestart. In Azure Database voor PostgreSQL is de `max_connections` parameterwaarde gebaseerd op de SKU. Zie [Limieten in Azure Database voor PostgreSQL voor](concepts-limits.md)meer informatie. 

Als u de hierboven beschreven serverwaarden probeert bij te werken, maar zich niet aan de limieten houdt, ontvangt u een foutmelding.

Firewallregels, virtuele netwerkregels en parameterinstellingen worden niet overgenomen van de hoofdserver naar de replica wanneer de replica wordt gemaakt of daarna.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL vereist dat](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) `max_prepared_transactions` de waarde van de parameter op de gelezen replica groter is dan of gelijk is aan de hoofdwaarde; Anders wordt de replica niet gestart. Als u wilt `max_prepared_transactions` wijzigen op het model, wijzigt u deze eerst op de replica's.

### <a name="stopped-replicas"></a>Gestopte replica's
Als u de replicatie tussen een hoofdserver en een gelezen replica stopt, wordt de replica opnieuw gestart om de wijziging toe te passen. De gestopte replica wordt een standalone server die zowel leest als schrijft accepteert. De standalone server kan niet opnieuw worden omgezet in een replica.

### <a name="deleted-master-and-standalone-servers"></a>Verwijderde stramien- en zelfstandige servers
Wanneer een hoofdserver wordt verwijderd, worden alle gelezen replica's zelfstandige servers. De replica's worden opnieuw gestart om deze wijziging weer te geven.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [maken en beheren van leesreplica's in de Azure-portal.](howto-read-replicas-portal.md)
* Meer informatie over het [maken en beheren van leesreplica's in de Azure CLI- en REST-API.](howto-read-replicas-cli.md)

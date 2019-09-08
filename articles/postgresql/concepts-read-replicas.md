---
title: Replica's lezen in Azure Database for PostgreSQL-één server
description: In dit artikel wordt de functie voor het lezen van replica's in Azure Database for PostgreSQL-één server beschreven.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 1571fc449bd40063c531f9942fe9b51da56f783c
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764322"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Replica's lezen in Azure Database for PostgreSQL-één server

Met de functie replica lezen kunt u gegevens van een Azure Database for PostgreSQL server repliceren naar een alleen-lezen server. U kunt van de hoofdserver naar maximaal vijf replica's repliceren. Replica's worden asynchroon bijgewerkt met de systeem eigen replicatie technologie van de PostgreSQL-engine.

Replica's zijn nieuwe servers die u op dezelfde manier beheert als gewone Azure Database for PostgreSQL servers. Voor elke Lees replica wordt u gefactureerd voor de ingerichte Compute in vCores en Storage in GB/maand.

Meer informatie over het [maken en beheren van replica's](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Wanneer moet u een lees replica gebruiken?
De functie voor het lezen van replica's helpt bij het verbeteren van de prestaties en schaal baarheid van Lees bare werk belastingen. Lees werkbelastingen kunnen worden geïsoleerd voor de replica's, terwijl schrijf werkbelastingen kunnen worden omgeleid naar de Master.

Een veelvoorkomend scenario is om BI-en analytische werk belastingen de Lees replica te laten gebruiken als gegevens bron voor rapportage.

Omdat replica's alleen-lezen zijn, worden ze niet rechtstreeks op de Master gereduceerd. Deze functie is niet gericht op write-intensieve workloads.

De functie voor het lezen van replica's maakt gebruik van asynchrone PostgreSQL-replicatie. De functie is niet bedoeld voor synchrone replicatie scenario's. Er is een meet bare vertraging tussen het hoofd en de replica. De gegevens op de replica worden uiteindelijk consistent met de gegevens op de Master. Gebruik deze functie voor werk belastingen die deze vertraging kunnen bevatten.

## <a name="cross-region-replication"></a>Replicatie tussen regio's
U kunt een lees replica maken in een andere regio dan de hoofd server. Replicatie tussen regio's kan handig zijn voor scenario's zoals het plannen van herstel na nood gevallen of gegevens dichter bij uw gebruikers te brengen.

U kunt een hoofd server in een [Azure database for PostgreSQL regio](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql)hebben. Een hoofd server kan een replica hebben in het gekoppelde gebied of in de universele replica regio's. In de onderstaande afbeelding ziet u welke replica regio's er beschikbaar zijn, afhankelijk van de hoofd regio.

[![Replica regio's lezen](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Universele replica regio's
U kunt altijd een lees replica maken in een van de volgende regio's, ongeacht waar uw master server zich bevindt. Dit zijn de universele replica regio's:

Australië-oost, Australië-zuidoost, centraal VS, Azië-oost, VS-Oost, VS-Oost 2, Japan-Oost, Japan-West, Korea-centraal, Korea-zuid, Noord-Centraal VS, Europa-noord, Zuid-Centraal VS, Zuidoost-Azië, UK-zuid, UK-west, Europa-west, VS-West, VS-West 2.


### <a name="paired-regions"></a>Gekoppelde regio's
Naast de universele replica regio's, kunt u een lees replica maken in het gekoppelde Azure-gebied van uw hoofd server. Als u het paar van uw regio niet weet, kunt u meer informatie vinden in het [artikel gekoppelde regio's in azure](../best-practices-availability-paired-regions.md).

Als u verschillende regio's replica's gebruikt voor het plannen van herstel na nood gevallen, raden we u aan om de replica in het gekoppelde gebied te maken in plaats van een van de andere regio's. Gekoppelde regio's vermijden gelijktijdige updates en geven geen prioriteiten voor fysieke isolatie en gegevens locatie.  

U moet rekening houden met de volgende beperkingen: 

* Regionale beschikbaarheid: Azure Database for PostgreSQL is beschikbaar in VS-West 2, Frankrijk-centraal, UAE-noord en Duitsland-centraal. De gekoppelde regio's zijn echter niet beschikbaar.
    
* Uni-directionele paren: Sommige Azure-regio's zijn in slechts één richting gekoppeld. Deze regio's omvatten West-India, Brazilië-zuid. 
   Dit betekent dat een master-server in West-India een replica kan maken in India-zuid. Een hoofd server in India-zuid kan echter geen replica maken in West-India. Dit komt doordat de secundaire regio van West-India India-zuid is, India-zuid maar de secundaire regio van het westen is niet West-India.


## <a name="create-a-replica"></a>Replica's maken
De `azure.replication_support` para meter moet zijn ingesteld op **replica**van de hoofd server. Als deze para meter wordt gewijzigd, moet de server opnieuw worden opgestart om de wijziging van kracht te laten worden. (De `azure.replication_support` para meter is alleen van toepassing op de lagen algemeen en geoptimaliseerd voor geheugen).

Wanneer u de werk stroom voor het maken van de replica start, wordt er een lege Azure Database for PostgreSQL-server gemaakt. De nieuwe server wordt gevuld met de gegevens die zich op de hoofd server bevonden. De aanmaak tijd is afhankelijk van de hoeveelheid gegevens op de Master en de tijd sinds de laatste wekelijkse volledige back-up. De tijd kan variëren van een paar minuten tot enkele uren.

Elke replica is ingeschakeld voor [automatische groei](concepts-pricing-tiers.md#storage-auto-grow)van opslag. Met de functie voor automatisch uitbreiden kan de replica de gegevens repliceren, en wordt voor komen dat de replicatie wordt onderbroken vanwege onvoldoende opslag fouten.

De functie voor het lezen van replica's maakt gebruik van fysieke PostgreSQL-replicatie, geen logische replicatie. Het streamen van replicatie via replicatie sleuven is de standaard bewerkings modus. Als dat nodig is, wordt de back-upfunctie voor logboek registratie gebruikt voor het bijwerken.

Meer informatie over [het maken van een lees replica in de Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Verbinding maken met een replica
Wanneer u een replica maakt, neemt deze de firewall regels of het VNet-service-eind punt van de hoofd server niet over. Deze regels moeten onafhankelijk worden ingesteld voor de replica.

De replica neemt het beheerders account over van de hoofd server. Alle gebruikers accounts op de hoofd server worden gerepliceerd naar de replica's die worden gelezen. U kunt alleen verbinding maken met een lees replica met behulp van de gebruikers accounts die beschikbaar zijn op de master server.

U kunt verbinding maken met de replica door de hostnaam en een geldig gebruikers account te gebruiken, net zoals bij een gewone Azure Database for PostgreSQL-server. Voor een server met de naam **mijn replica** met de gebruikers naam **myadmin**, kunt u verbinding maken met de replica met behulp van psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Voer bij de prompt het wacht woord voor het gebruikers account in.

## <a name="monitor-replication"></a>Replicatie controleren
Azure Database for PostgreSQL biedt twee metrische gegevens voor het controleren van replicatie. De twee meet waarden zijn de **maximale vertraging voor replica's** en **replica vertraging**. Zie voor meer informatie over het weer geven van deze metrische gegevens het gedeelte **een replica bewaken** in het [artikel Lees-en replica-instructies](howto-read-replicas-portal.md).

De **maximale vertraging** voor de metrische gegevens van replica's toont de vertraging in bytes tussen het hoofd en de meest bewaarde replica. Deze metriek is alleen beschikbaar op de hoofd server.

De metriek van de **replica vertraging** toont de tijd sinds de laatste geplayte trans actie. Als er geen trans acties worden uitgevoerd op de hoofd server, wordt deze tijds vertraging door de metriek aangegeven. Deze metriek is alleen beschikbaar voor replica servers. Replica vertraging wordt berekend op basis `pg_stat_wal_receiver` van de weer gave:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Stel een waarschuwing in om u te informeren wanneer de replica vertraging een waarde bereikt die niet geschikt is voor uw werk belasting. 

Voor meer inzicht moet u rechtstreeks een query uitvoeren op de hoofd server om de replicatie vertraging in bytes op alle replica's te verkrijgen.

In PostgreSQL versie 10:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

In PostgreSQL versie 9,6 en lager:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Als een hoofd server of een lees bewerking van de replica opnieuw wordt gestart, wordt de tijd die nodig is om opnieuw op te starten en weer te starten in de metrische gegevens van de replica vertraging.

## <a name="stop-replication"></a>Replicatie stoppen
U kunt de replicatie tussen een Master en een replica stoppen. De actie stoppen zorgt ervoor dat de replica opnieuw wordt opgestart en de replicatie-instellingen worden verwijderd. Nadat de replicatie tussen een hoofd server en een lees replica is gestopt, wordt de replica een zelfstandige server. De gegevens op de zelfstandige server zijn de gegevens die beschikbaar zijn op de replica op het moment dat de opdracht stop-replicatie werd gestart. De zelfstandige server is niet actief bij de hoofd server.

> [!IMPORTANT]
> De zelfstandige server kan niet opnieuw in een replica worden gemaakt.
> Voordat u de replicatie op een lees replica stopt, moet u ervoor zorgen dat de replica over alle gegevens beschikt die u nodig hebt.

Wanneer u de replicatie stopt, verliest de replica alle koppelingen naar het vorige hoofd en andere replica's.

Meer informatie over het [stoppen van replicatie naar een replica](howto-read-replicas-portal.md).

## <a name="failover"></a>Failover
Er is geen automatische failover tussen hoofd-en replica servers. 

Omdat replicatie asynchroon is, is er sprake van een vertraging tussen de Master en de replica. De hoeveelheid vertraging kan worden beïnvloed door een aantal factoren, zoals hoe zwaar de werk belasting die wordt uitgevoerd op de master server en de latentie tussen data centers. In de meeste gevallen variëren de replica vertraging tussen enkele seconden en een paar minuten. U kunt uw werkelijke replicatie vertraging bijhouden met behulp van de metrische *replica vertraging*, die beschikbaar is voor elke replica. Met deze metriek wordt de tijd weer gegeven sinds de laatste geplayte trans actie. U wordt aangeraden om te bepalen wat uw gemiddelde vertraging is door uw replica vertraging te bestuderen gedurende een bepaalde periode. U kunt een waarschuwing instellen voor replica vertraging, zodat u actie kunt ondernemen als deze buiten het verwachte bereik komt.

> [!Tip]
> Als u een failover naar de replica doorzoekt, geeft de vertraging op het moment dat u de replica loskoppelt van de Master aan hoeveel gegevens er verloren zijn gegaan.

Zodra u hebt vastgesteld dat u een failover naar een replica wilt uitvoeren, 

1. Replicatie naar de replica stoppen<br/>
   Deze stap is nodig om de replica-server in staat te stellen schrijf bewerkingen te accepteren. Als onderdeel van dit proces wordt de replica server opnieuw opgestart en ontkoppeld van het hoofd bestand. Zodra u stopt met de replicatie, duurt het back-end doorgaans ongeveer twee minuten om te volt ooien. Zie de sectie [Replicatie stoppen](#stop-replication) in dit artikel voor meer informatie over de implicaties van deze actie.
    
2. Uw toepassing naar de (voormalige) replica laten wijzen<br/>
   Elke server heeft een unieke connection string. Werk uw toepassing bij zodat deze verwijst naar de (voormalige) replica in plaats van het hoofd bestand.
    
Zodra uw toepassing Lees-en schrijf bewerkingen heeft verwerkt, hebt u de failover voltooid. De uitval tijd van uw toepassings ervaring is afhankelijk van wanneer u een probleem detecteert en de stappen 1 en 2 hierboven uitvoert.


## <a name="considerations"></a>Overwegingen

In deze sectie vindt u een overzicht van de overwegingen voor de functie replica lezen.

### <a name="prerequisites"></a>Vereisten
Voordat u een lees replica maakt, moet `azure.replication_support` de para meter worden ingesteld op **replica** op de hoofd server. Als deze para meter wordt gewijzigd, moet de server opnieuw worden opgestart om de wijziging van kracht te laten worden. De `azure.replication_support` para meter is alleen van toepassing op de lagen algemeen en geoptimaliseerd voor geheugen.

### <a name="new-replicas"></a>Nieuwe replica's
Er wordt een lees replica gemaakt als een nieuwe Azure Database for PostgreSQL-server. Een bestaande server kan niet worden gemaakt in een replica. Het is niet mogelijk om een replica van een andere Lees replica te maken.

### <a name="replica-configuration"></a>Replica configuratie
Een replica wordt gemaakt met behulp van dezelfde berekenings-en opslag instellingen als de hoofd server. Nadat een replica is gemaakt, kunnen verschillende instellingen onafhankelijk van de hoofd server worden gewijzigd: generatie van compute, vCores, opslag en back-up van Bewaar periode. De prijs categorie kan ook onafhankelijk worden gewijzigd, met uitzonde ring van of van de Basic-laag.

> [!IMPORTANT]
> Voordat een Master-instelling wordt bijgewerkt naar een nieuwe waarde, moet u de replica configuratie bijwerken naar een gelijke of hogere waarde. Met deze actie wordt ervoor gezorgd dat in de replica alle wijzigingen worden doorgevoerd die in de hoofdserver zijn aangebracht.

PostgreSQL vereist dat de waarde van `max_connections` de para meter op de Lees replica groter dan of gelijk aan de Master waarde is. anders wordt de replica niet gestart. In azure database for PostgreSQL is de `max_connections` waarde van de para meter gebaseerd op de SKU. Zie [limieten in azure database for PostgreSQL](concepts-limits.md)voor meer informatie. 

Als u de server waarden probeert bij te werken, maar niet aan de limieten voldoet, wordt er een fout bericht weer gegeven.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[Postgresql vereist](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) dat de waarde van `max_prepared_transactions` de para meter op de Lees replica groter dan of gelijk aan de Master waarde is. anders wordt de replica niet gestart. Als u wijzigingen wilt aanbrengen `max_prepared_transactions` op de Master, wijzigt u deze eerst op de replica's.

### <a name="stopped-replicas"></a>Gestopte replica's
Als u de replicatie tussen een hoofd server en een lees replica stopt, wordt de replica opnieuw gestart om de wijziging toe te passen. De gestopte replica wordt een zelfstandige server die zowel lees-als schrijf bewerkingen accepteert. De zelfstandige server kan niet opnieuw in een replica worden gemaakt.

### <a name="deleted-master-and-standalone-servers"></a>Verwijderde Master-en zelfstandige servers
Wanneer een master-server wordt verwijderd, worden alle bijbehorende Lees replica's zelfstandige servers. De replica's worden opnieuw gestart om deze wijziging weer te geven.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het maken en beheren van Lees replica's in de Azure Portal](howto-read-replicas-portal.md).
* Meer informatie over het [maken en beheren van Lees replica's in azure cli](howto-read-replicas-cli.md).

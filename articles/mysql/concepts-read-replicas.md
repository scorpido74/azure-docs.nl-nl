---
title: Replica's lezen-Azure Database for MySQL.
description: "Meer informatie over het lezen van replica's in Azure Database for MySQL: het kiezen van regio's, het maken van replica's, het verbinden van replica's, het bewaken van replicatie en het stoppen van de replicatie."
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: b733ef771444e080eb794b300e75d4396c3ef674
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86079170"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Leesreplica's in Azure Database for MySQL

Met de functie leesreplica kunt u gegevens van een Azure Database for MySQL-server repliceren naar een alleen-lezen server. U kunt van de hoofdserver naar maximaal vijf replica's repliceren. Replica's worden asynchroon bijgewerkt met behulp van de systeemeigen, op de positie van het binlog-bestand (binair logboekbestand) gebaseerde replicatietechnologie van het MySQL-systeem. Meer informatie over binlog-replicatie vindt u in het [overzicht van MySQL binlog-replicatie](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Replica's zijn nieuwe servers die u op dezelfde manier beheert als gewone Azure Database for MySQL servers. Voor elke Lees replica wordt u gefactureerd voor de ingerichte Compute in vCores en Storage in GB/maand.

Zie de [MySQL-replicatie documentatie](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)voor meer informatie over MySQL-replicatie functies en-problemen.

> [!NOTE]
> Afwijking-vrije communicatie
>
> Micro soft biedt ondersteuning voor een gevarieerde en inbegrips omgeving. Dit artikel bevat verwijzingen naar het woord _Slave_. De micro soft- [stijl gids voor beschik bare communicatie](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) herkent deze als een uitsluitend woord. Het woord wordt in dit artikel gebruikt voor consistentie omdat het momenteel het woord is dat wordt weer gegeven in de software. Wanneer de software is bijgewerkt om het woord te verwijderen, wordt dit artikel zodanig bijgewerkt dat het in uitlijning is.
>

## <a name="when-to-use-a-read-replica"></a>Wanneer moet u een lees replica gebruiken?

De functie voor het lezen van replica's helpt bij het verbeteren van de prestaties en schaal baarheid van Lees bare werk belastingen. Lees werkbelastingen kunnen worden geïsoleerd voor de replica's, terwijl schrijf werkbelastingen kunnen worden omgeleid naar de Master.

Een veelvoorkomend scenario is om BI-en analytische werk belastingen de Lees replica te laten gebruiken als gegevens bron voor rapportage.

Omdat replica's alleen-lezen zijn, worden ze niet rechtstreeks op de Master gereduceerd. Deze functie is niet gericht op write-intensieve workloads.

De functie replica lezen maakt gebruik van MySQL-asynchrone replicatie. De functie is niet bedoeld voor synchrone replicatie scenario's. Er is een meet bare vertraging tussen het hoofd en de replica. De gegevens op de replica worden uiteindelijk consistent met de gegevens op de Master. Gebruik deze functie voor werk belastingen die deze vertraging kunnen bevatten.

## <a name="cross-region-replication"></a>Replicatie tussen regio's
U kunt een lees replica maken in een andere regio dan de hoofd server. Replicatie tussen regio's kan handig zijn voor scenario's zoals het plannen van herstel na nood gevallen of gegevens dichter bij uw gebruikers te brengen.

U kunt een hoofd server in een [Azure database for MySQL regio](https://azure.microsoft.com/global-infrastructure/services/?products=mysql)hebben.  Een hoofd server kan een replica hebben in het gekoppelde gebied of in de universele replica regio's. In de onderstaande afbeelding ziet u welke replica regio's er beschikbaar zijn, afhankelijk van de hoofd regio.

[![Replica regio's lezen](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Universele replica regio's
U kunt in een van de volgende regio's een lees replica maken, ongeacht waar uw master server zich bevindt. De ondersteunde regio's voor universele replica's zijn:

Australië-oost, Australië-zuidoost, centraal VS, Azië-oost, VS-Oost, VS-Oost 2, Japan-Oost, Japan-West, Korea-centraal, Korea-zuid, Noord-Centraal VS, Europa-noord, Zuid-Centraal VS, Zuidoost-Azië, UK-zuid, UK-west, Europa-west, VS-West, VS-West 2, West-Centraal vs.

### <a name="paired-regions"></a>Gekoppelde regio's
Naast de universele replica regio's, kunt u een lees replica maken in het gekoppelde Azure-gebied van uw hoofd server. Als u het paar van uw regio niet weet, kunt u meer informatie vinden in het [artikel gekoppelde regio's in azure](../best-practices-availability-paired-regions.md).

Als u verschillende regio's replica's gebruikt voor het plannen van herstel na nood gevallen, raden we u aan om de replica in het gekoppelde gebied te maken in plaats van een van de andere regio's. Gekoppelde regio's vermijden gelijktijdige updates en geven geen prioriteiten voor fysieke isolatie en gegevens locatie.  

Er zijn echter beperkingen om rekening mee te houden: 

* Regionale Beschik baarheid: Azure Database for MySQL is beschikbaar in Frankrijk-centraal, UAE-noord en Duitsland-centraal. De gekoppelde regio's zijn echter niet beschikbaar.
    
* Uni-directionele paren: sommige Azure-regio's zijn in slechts één richting gekoppeld. Deze regio's omvatten West-India, Brazilië-zuid en US Gov-Virginia. 
   Dit betekent dat een master-server in West-India een replica kan maken in India-zuid. Een hoofd server in India-zuid kan echter geen replica maken in West-India. Dit komt doordat de secundaire regio van West-India India-zuid is, India-zuid maar de secundaire regio van het westen is niet West-India.

## <a name="create-a-replica"></a>Replica's maken

> [!IMPORTANT]
> De functie voor het lezen van replica's is alleen beschikbaar voor Azure Database for MySQL-servers in de prijs Categorieën Algemeen of geoptimaliseerd voor geheugen. Zorg ervoor dat de hoofd server zich in een van deze prijs categorieën bevindt.

Als een master server geen bestaande replica servers heeft, wordt de Master eerst opnieuw opgestart om zichzelf voor te bereiden voor replicatie.

Wanneer u de werk stroom voor het maken van de replica start, wordt er een lege Azure Database for MySQL-server gemaakt. De nieuwe server wordt gevuld met de gegevens die zich op de hoofd server bevonden. De aanmaak tijd is afhankelijk van de hoeveelheid gegevens op de Master en de tijd sinds de laatste wekelijkse volledige back-up. De tijd kan variëren van een paar minuten tot enkele uren. De replica server wordt altijd gemaakt in dezelfde resource groep en hetzelfde abonnement als de hoofd server. Als u een replica server wilt maken naar een andere resource groep of een ander abonnement, kunt u [de replica-server verplaatsen](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) nadat u deze hebt gemaakt.

Elke replica is ingeschakeld voor [automatische groei](concepts-pricing-tiers.md#storage-auto-grow)van opslag. Met de functie voor automatisch uitbreiden kan de replica de gegevens repliceren, en wordt voor komen dat er een onderbreking in de replicatie wordt veroorzaakt door problemen met de opslag.

Meer informatie over [het maken van een lees replica in de Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Verbinding maken met een replica

Bij het maken neemt een replica de firewall regels van de hoofd server over. Daarna zijn deze regels onafhankelijk van de hoofd server.

De replica neemt het beheerders account over van de hoofd server. Alle gebruikers accounts op de hoofd server worden gerepliceerd naar de replica's die worden gelezen. U kunt alleen verbinding maken met een lees replica met behulp van de gebruikers accounts die beschikbaar zijn op de master server.

U kunt verbinding maken met de replica door de hostnaam en een geldig gebruikers account te gebruiken, net zoals bij een gewone Azure Database for MySQL-server. Voor een server met de naam **myreplica** met de gebruikers naam **myadmin**van de beheerder kunt u verbinding maken met de replica met behulp van de MySQL cli:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

Voer bij de prompt het wacht woord voor het gebruikers account in.

## <a name="monitor-replication"></a>Replicatie controleren

Azure Database for MySQL levert de **replicatie vertraging in seconden** metric in azure monitor. Deze metriek is alleen beschikbaar voor replica's.

Deze metrische gegevens worden berekend met behulp van de `seconds_behind_master` beschik bare metrische gegevens in de opdracht van MySQL `SHOW SLAVE STATUS` .

Stel een waarschuwing in om u te informeren wanneer de replicatie vertraging een waarde bereikt die niet geschikt is voor uw werk belasting.

## <a name="stop-replication"></a>Replicatie stoppen

U kunt de replicatie tussen een Master en een replica stoppen. Nadat de replicatie tussen een hoofd server en een lees replica is gestopt, wordt de replica een zelfstandige server. De gegevens op de zelfstandige server zijn de gegevens die beschikbaar zijn op de replica op het moment dat de opdracht stop-replicatie werd gestart. De zelfstandige server is niet actief bij de hoofd server.

Wanneer u ervoor kiest om de replicatie naar een replica te stoppen, gaan alle koppelingen naar het vorige hoofd object en andere replica's verloren. Er is geen automatische failover tussen een hoofd database en de replica.

> [!IMPORTANT]
> De zelfstandige server kan niet opnieuw in een replica worden gemaakt.
> Voordat u de replicatie op een lees replica stopt, moet u ervoor zorgen dat de replica over alle gegevens beschikt die u nodig hebt.

Meer informatie over het [stoppen van replicatie naar een replica](howto-read-replicas-portal.md).

## <a name="failover"></a>Failover

Er is geen automatische failover tussen hoofd-en replica servers. 

Omdat replicatie asynchroon is, is er sprake van een vertraging tussen de Master en de replica. De hoeveelheid vertraging kan worden beïnvloed door een aantal factoren, zoals hoe zwaar de werk belasting die wordt uitgevoerd op de master server en de latentie tussen data centers. In de meeste gevallen variëren de replica vertraging tussen enkele seconden en een paar minuten. U kunt uw werkelijke replicatie vertraging bijhouden met behulp van de metrische *replica vertraging*, die beschikbaar is voor elke replica. Met deze metriek wordt de tijd weer gegeven sinds de laatste geplayte trans actie. U wordt aangeraden om te bepalen wat uw gemiddelde vertraging is door uw replica vertraging te bestuderen gedurende een bepaalde periode. U kunt een waarschuwing instellen voor replica vertraging, zodat u actie kunt ondernemen als deze buiten het verwachte bereik komt.

> [!Tip]
> Als u een failover naar de replica doorzoekt, geeft de vertraging op het moment dat u de replica loskoppelt van de Master aan hoeveel gegevens er verloren zijn gegaan.

Zodra u hebt vastgesteld dat u een failover naar een replica wilt uitvoeren, 

1. Replicatie naar de replica stoppen<br/>
   Deze stap is nodig om de replica-server in staat te stellen schrijf bewerkingen te accepteren. Als onderdeel van dit proces wordt de replica server ontkoppeld van de Master. Zodra u stopt met de replicatie, duurt het back-end doorgaans ongeveer twee minuten om te volt ooien. Zie de sectie [Replicatie stoppen](#stop-replication) in dit artikel voor meer informatie over de implicaties van deze actie.
    
2. Uw toepassing naar de (voormalige) replica laten wijzen<br/>
   Elke server heeft een unieke connection string. Werk uw toepassing bij zodat deze verwijst naar de (voormalige) replica in plaats van het hoofd bestand.
    
Zodra uw toepassing Lees-en schrijf bewerkingen heeft verwerkt, hebt u de failover voltooid. De uitval tijd van uw toepassings ervaring is afhankelijk van wanneer u een probleem detecteert en de stappen 1 en 2 hierboven uitvoert.

## <a name="considerations-and-limitations"></a>Overwegingen en beperkingen

### <a name="pricing-tiers"></a>Prijscategorieën

Het lezen van replica's is momenteel alleen beschikbaar in de prijs Categorieën Algemeen en geoptimaliseerd voor geheugen.

> [!NOTE]
> De kosten voor het uitvoeren van de replica server zijn gebaseerd op de regio waarin de replica-server wordt uitgevoerd.

### <a name="master-server-restart"></a>Hoofd server opnieuw opstarten

Wanneer u een replica maakt voor een model zonder bestaande replica's, wordt de Master eerst opnieuw opgestart om zichzelf voor te bereiden voor replicatie. Houd dit in overweging en voer deze bewerkingen uit tijdens een rustige periode.

### <a name="new-replicas"></a>Nieuwe replica's

Er wordt een lees replica gemaakt als een nieuwe Azure Database for MySQL-server. Een bestaande server kan niet worden gemaakt in een replica. Het is niet mogelijk om een replica van een andere Lees replica te maken.

### <a name="replica-configuration"></a>Replica configuratie

Een replica wordt gemaakt met behulp van dezelfde server configuratie als de Master. Nadat een replica is gemaakt, kunnen verschillende instellingen onafhankelijk van de hoofd server worden gewijzigd: generatie van compute, vCores, opslag en back-up van Bewaar periode. De prijs categorie kan ook onafhankelijk worden gewijzigd, met uitzonde ring van of van de Basic-laag.

> [!IMPORTANT]
> Voordat een configuratie van een hoofdserver wordt bijgewerkt naar nieuwe waarden, moet u de configuratie van de replica bijwerken naar gelijke of hogere waarden. Met deze actie wordt ervoor gezorgd dat in de replica alle wijzigingen worden doorgevoerd die in de hoofdserver zijn aangebracht.

Firewall regels en parameter instellingen worden overgenomen van de hoofd server naar de replica wanneer de replica wordt gemaakt. Daarna zijn de regels van de replica onafhankelijk.

### <a name="stopped-replicas"></a>Gestopte replica's

Als u de replicatie tussen een hoofd server en een lees replica stopt, wordt de gestopte replica een zelfstandige server die zowel lees-als schrijf bewerkingen accepteert. De zelfstandige server kan niet opnieuw in een replica worden gemaakt.

### <a name="deleted-master-and-standalone-servers"></a>Verwijderde Master-en zelfstandige servers

Wanneer een master server wordt verwijderd, wordt replicatie gestopt voor alle replica's. Deze replica's worden automatisch zelfstandige servers en kunnen Lees-en schrijf bewerkingen accepteren. De hoofd server zelf wordt verwijderd.

### <a name="user-accounts"></a>Gebruikersaccounts

Gebruikers op de hoofd server worden gerepliceerd naar de Lees replica's. U kunt alleen verbinding maken met een lees replica met behulp van de beschik bare gebruikers accounts op de master server.

### <a name="server-parameters"></a>Serverparameters

Om problemen met de synchronisatie van gegevens en mogelijk verlies of beschadiging van gegevens te voorkomen, worden bepaalde serverparameters vergrendeld zodat ze niet kunnen worden bijgewerkt bij gebruik van replica's voor lezen.

De volgende server parameters zijn vergrendeld op de Master-en replica servers:
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

De [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) para meter is vergrendeld op de replica servers. 

Als u een van de bovenstaande para meters op de hoofd server wilt bijwerken, verwijdert u de replica servers, werkt u de parameter waarde op de Master bij en maakt u de replica's opnieuw.

### <a name="other"></a>Anders

- Algemene trans actie-id's (GTID) worden niet ondersteund.
- Het maken van een replica van een replica wordt niet ondersteund.
- In-Memory tabellen kunnen ertoe leiden dat replica's niet meer synchroon zijn. Dit is een beperking van de MySQL-replicatie technologie. Meer informatie vindt u in de [referentie documentatie voor mysql](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) .
- Zorg ervoor dat de hoofd Server tabellen primaire sleutels hebben. Als er geen primaire sleutels zijn, kan dit leiden tot replicatie latentie tussen de hoofd-en replicas.
- Bekijk de volledige lijst met MySQL-replicatie beperkingen in de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het maken en beheren van Lees replica's met behulp van de Azure Portal](howto-read-replicas-portal.md)
- Meer informatie over [het maken en beheren van Lees replica's met behulp van Azure CLI en rest API](howto-read-replicas-cli.md)

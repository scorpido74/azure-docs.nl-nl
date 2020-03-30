---
title: Lees replica's - Azure Database voor MySQL.
description: "Meer informatie over leesreplica's in Azure Database voor MySQL: regio's kiezen, replica's maken, verbinding maken met replica's, replicatie bewaken en replicatie stoppen."
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 98461928e465a103f73761afce5270234224fbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167348"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Leesreplica's in Azure Database for MySQL

Met de functie leesreplica kunt u gegevens van een Azure Database for MySQL-server repliceren naar een alleen-lezen server. U kunt van de hoofdserver naar maximaal vijf replica's repliceren. Replica's worden asynchroon bijgewerkt met behulp van de systeemeigen, op de positie van het binlog-bestand (binair logboekbestand) gebaseerde replicatietechnologie van het MySQL-systeem. Zie het overzicht van [mySQL binlog-replicatie](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)voor meer informatie over binlog-replicatie.

Replica's zijn nieuwe servers die u beheert die vergelijkbaar zijn met gewone Azure Database voor MySQL-servers. Voor elke gelezen replica wordt u gefactureerd voor de ingerichte compute in vCores en opslag in GB/maand.

Zie de [MySQL-replicatiedocumentatie](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)voor meer informatie over MySQL-replicatiefuncties en -problemen.

## <a name="when-to-use-a-read-replica"></a>Wanneer een gelezen replica gebruiken

De leesreplica-functie helpt de prestaties en de schaal van leesintensieve workloads te verbeteren. Leesworkloads kunnen worden geïsoleerd voor de replica's, terwijl schrijfworkloads naar het model kunnen worden geleid.

Een veelvoorkomend scenario is dat BI- en analytische workloads de gelezen replica gebruiken als gegevensbron voor rapportage.

Omdat replica's alleen-lezen zijn, verminderen ze de schrijfcapaciteitslasten op de master niet direct. Deze functie is niet gericht op schrijfintensieve workloads.

De functie leesreplica maakt gebruik van MySQL asynchrone replicatie. De functie is niet bedoeld voor synchrone replicatiescenario's. Er zal een meetbare vertraging tussen de master en de replica. De gegevens op de replica worden uiteindelijk consistent met de gegevens op het model. Gebruik deze functie voor workloads die deze vertraging kunnen opvangen.

## <a name="cross-region-replication"></a>Replicatie tussen regio's
U een leesreplica maken in een andere regio dan uw hoofdserver. Replicatie tussen regio's kan handig zijn voor scenario's zoals noodherstelplanning of het dichter bij uw gebruikers brengen van gegevens.

U een hoofdserver hebben in een [Azure-database voor MySQL-regio.](https://azure.microsoft.com/global-infrastructure/services/?products=mysql)  Een hoofdserver kan een replica hebben in het gekoppelde gebied of de universele replicagebieden. In de onderstaande afbeelding ziet u welke replicaregio's beschikbaar zijn, afhankelijk van uw hoofdregio.

[![Replicaregio's lezen](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Universele replicaregio's
U een leesreplica maken in een van de volgende regio's, ongeacht waar uw hoofdserver zich bevindt. De ondersteunde universele replicaregio's zijn:

Australië Oost, Australië Zuidoost, Centraal VS, Oost-Azië, Oost-VS, Oost-VS 2, Japan Oost, Japan West, Korea Centraal, Korea Zuid, Noord-Centraal VS, Noord-Europa, Zuid-Centraal VS, Zuidoost-Azië, Verenigd Koninkrijk Zuid, Verenigd Koninkrijk West, West-Europa, West-VS.

*West US 2 is tijdelijk niet beschikbaar als replicalocatie voor verschillende regio's.


### <a name="paired-regions"></a>Gekoppelde regio's
Naast de universele replicaregio's u een leesreplica maken in het gekoppelde Azure-gebied van uw hoofdserver. Als u het paar van uw regio niet kent, u meer te weten komen in het [artikel Azure Paired Regions.](../best-practices-availability-paired-regions.md)

Als u replica's voor meerdere regio's gebruikt voor de planning voor noodherstel, raden we u aan de replica te maken in het gekoppelde gebied in plaats van in een van de andere regio's. Gekoppelde regio's vermijden gelijktijdige updates en prioriteren fysieke isolatie en dataresidency.  

Er zijn echter beperkingen om rekening mee te houden: 

* Regionale beschikbaarheid: Azure Database for MySQL is beschikbaar in West US 2, France Central, UAE North en Germany Central. Hun gekoppelde regio's zijn echter niet beschikbaar.
    
* Unidirectionele paren: Sommige Azure-regio's zijn slechts in één richting gekoppeld. Deze regio's omvatten West-India, Brazilië Zuid, en de Amerikaanse gov Virginia. 
   Dit betekent dat een hoofdserver in West-India een replica kan maken in Zuid-India. Een hoofdserver in Zuid-India kan echter geen replica maken in West-India. Dit komt omdat de secundaire regio van West-India Zuid-India is, maar de secundaire regio van Zuid-India is niet West-India.


## <a name="create-a-replica"></a>Replica's maken

Als een hoofdserver geen bestaande replicaservers heeft, wordt de master eerst opnieuw opgestart om zich voor te bereiden op replicatie.

Wanneer u de replicawerk voor maken start, wordt een lege Azure Database voor MySQL-server gemaakt. De nieuwe server is gevuld met de gegevens die zich op de hoofdserver bevond. De creatietijd is afhankelijk van de hoeveelheid gegevens op de master en de tijd sinds de laatste wekelijkse volledige back-up. De tijd kan variëren van enkele minuten tot enkele uren.

Elke replica is ingeschakeld voor opslag [auto-groeien](concepts-pricing-tiers.md#storage-auto-grow). Met de functie automatisch groeien kan de replica gelijke tred houden met de gegevens die eraan worden gerepliceerd en een onderbreking van de replicatie voorkomen die wordt veroorzaakt door fouten buiten de opslag.

Meer informatie over het [maken van een gelezen replica in de Azure-portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Verbinding maken met een replica

Bij het maken neemt een replica de firewallregels of het VNet-serviceeindpunt van de hoofdserver over. Daarna zijn deze regels onafhankelijk van de hoofdserver.

De replica neemt het beheerdersaccount over van de hoofdserver. Alle gebruikersaccounts op de hoofdserver worden gerepliceerd naar de gelezen replica's. U alleen verbinding maken met een gelezen replica met behulp van de gebruikersaccounts die beschikbaar zijn op de hoofdserver.

U verbinding maken met de replica met behulp van de hostnaam en een geldig gebruikersaccount, zoals u zou doen op een gewone Azure-database voor MySQL-server. Voor een server met de naam **myreplica** met de gebruikersnaam **myadmin,** u verbinding maken met de replica met behulp van de mysql CLI:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

Voer bij de prompt het wachtwoord voor het gebruikersaccount in.

## <a name="monitor-replication"></a>Replicatie controleren

Azure Database voor MySQL biedt de **replicatievertraging in seconden** in Azure Monitor. Deze statistiek is alleen beschikbaar voor replica's.

Deze statistiek wordt `seconds_behind_master` berekend met behulp van `SHOW SLAVE STATUS` de statistiek die beschikbaar is in de opdracht van MySQL.

Stel een waarschuwing in om u te informeren wanneer de replicatievertraging een waarde bereikt die niet acceptabel is voor uw werkbelasting.

## <a name="stop-replication"></a>Replicatie stoppen

U de replicatie tussen een stramien en een replica stoppen. Nadat replicatie is gestopt tussen een hoofdserver en een gelezen replica, wordt de replica een zelfstandige server. De gegevens in de zelfstandige server zijn de gegevens die beschikbaar waren op de replica op het moment dat de opdracht replicatie stoppen werd gestart. De standalone server haalt de hoofdserver niet in.

Wanneer u ervoor kiest om replicatie naar een replica te stoppen, verliest deze alle koppelingen naar het vorige stramien en andere replica's. Er is geen geautomatiseerde failover tussen een master en de replica.

> [!IMPORTANT]
> De standalone server kan niet opnieuw worden omgezet in een replica.
> Voordat u de replicatie op een gelezen replica stopt, moet u ervoor zorgen dat de replica alle gegevens heeft die u nodig hebt.

Meer informatie over het [stoppen van replicatie naar een replica](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Overwegingen en beperkingen

### <a name="pricing-tiers"></a>Prijscategorieën

Leesreplica's zijn momenteel alleen beschikbaar in de prijzenlagen Algemeen Doel en Geheugengeoptimaliseerd.

### <a name="master-server-restart"></a>Hoofdserver opnieuw opstarten

Wanneer u een replica maakt voor een stramien zonder bestaande replica's, wordt het stramien eerst opnieuw opgestart om zich voor te bereiden op replicatie. Houd hier rekening mee en voer deze bewerkingen uit tijdens een dalurenperiode.

### <a name="new-replicas"></a>Nieuwe replica's

Er wordt een gelezen replica gemaakt als een nieuwe Azure-database voor MySQL-server. Een bestaande server kan niet worden omgezet in een replica. U geen replica van een andere gelezen replica maken.

### <a name="replica-configuration"></a>Replicaconfiguratie

Er wordt een replica gemaakt met dezelfde serverconfiguratie als de stramien. Nadat een replica is gemaakt, kunnen verschillende instellingen onafhankelijk van de hoofdserver worden gewijzigd: rekengeneratie, vCores, opslag en back-upbewaarperiode. De prijscategorie kan ook onafhankelijk worden gewijzigd, behalve van of naar de basislaag.

> [!IMPORTANT]
> Voordat een configuratie van een hoofdserver wordt bijgewerkt naar nieuwe waarden, moet u de configuratie van de replica bijwerken naar gelijke of hogere waarden. Met deze actie wordt ervoor gezorgd dat in de replica alle wijzigingen worden doorgevoerd die in de hoofdserver zijn aangebracht.

Firewallregels, virtuele netwerkregels en parameterinstellingen worden overgenomen van de hoofdserver naar de replica wanneer de replica wordt gemaakt. Daarna zijn de regels van de replica onafhankelijk.

### <a name="stopped-replicas"></a>Gestopte replica's

Als u de replicatie tussen een hoofdserver en een gelezen replica stopt, wordt de gestopte replica een zelfstandige server die zowel leest als schrijft accepteert. De standalone server kan niet opnieuw worden omgezet in een replica.

### <a name="deleted-master-and-standalone-servers"></a>Verwijderde stramien- en zelfstandige servers

Wanneer een hoofdserver wordt verwijderd, wordt de replicatie gestopt met alle gelezen replica's. Deze replica's worden automatisch standalone servers en kunnen zowel lezen als schrijven accepteren. De hoofdserver zelf wordt verwijderd.

### <a name="user-accounts"></a>Gebruikersaccounts

Gebruikers op de hoofdserver worden gerepliceerd naar de gelezen replica's. U alleen verbinding maken met een gelezen replica met behulp van de gebruikersaccounts die beschikbaar zijn op de hoofdserver.

### <a name="server-parameters"></a>Serverparameters

Om problemen met de synchronisatie van gegevens en mogelijk verlies of beschadiging van gegevens te voorkomen, worden bepaalde serverparameters vergrendeld zodat ze niet kunnen worden bijgewerkt bij gebruik van replica's voor lezen.

De volgende serverparameters zijn vergrendeld op zowel de hoofd- als de replicaservers:
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

De [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) parameter is vergrendeld op de replicaservers. 

### <a name="other"></a>Overige

- Globale transactie-id's (GTID) worden niet ondersteund.
- Het maken van een replica van een replica wordt niet ondersteund.
- In-memory tabellen kunnen ertoe leiden dat replica's niet synchroon lopen. Dit is een beperking van de MySQL-replicatietechnologie. Lees meer in de [MySQL-referentiedocumentatie](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) voor meer informatie.
- Zorg ervoor dat de hoofdservertabellen primaire sleutels hebben. Gebrek aan primaire sleutels kan leiden tot replicatielatentie tussen het stramien en replica's.
- Bekijk de volledige lijst met MySQL-replicatiebeperkingen in de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken en beheren van leesreplica's met de Azure-portal](howto-read-replicas-portal.md)
- Meer informatie over het [maken en beheren van leesreplica's met de Azure CLI- en REST-API](howto-read-replicas-cli.md)

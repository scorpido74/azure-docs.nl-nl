---
title: Problemen met replicatie latentie Azure Database for MySQL
description: Meer informatie over het oplossen van replicatie latentie met Azure Database for MySQL het lezen van replica's
keywords: MySQL, problemen oplossen, replicatie latentie in seconden
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.openlocfilehash: 16a502a53b4441faf68ea342e0bc865731d38b1a
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876990"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Problemen met replicatie latentie in Azure Database for MySQL oplossen

Met de functie [replica lezen](concepts-read-replicas.md) kunt u gegevens van een Azure database for mysql server repliceren naar een replica server met het kenmerk alleen-lezen. Met de functie voor het lezen van Replica's wordt de werk belasting geschaald door lees-en rapport query's van de toepassing naar replica servers te routeren. Dit vermindert de druk op de primaire server en verbetert de algehele prestaties en latentie van de toepassing wanneer deze wordt geschaald. Replica's worden asynchroon bijgewerkt met behulp van de systeemeigen, op de positie van het binlog-bestand (binair logboekbestand) gebaseerde replicatietechnologie van het MySQL-systeem. Meer informatie over binlog-replicatie vindt u in het [overzicht van MySQL binlog-replicatie](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

De replicatie vertraging voor de secundaire Lees replica's is afhankelijk van het aantal factoren, inclusief, maar niet beperkt tot 

- Netwerklatentie
- Transactie volume op de bron server
- Compute-laag van bron en secundaire replica server lezen
- Query's die worden uitgevoerd op de primaire en secundaire server. 

In dit document leert u hoe u de replicatie latentie in Azure Database for MySQL kunt oplossen. Daarnaast leert u ook enkele veelvoorkomende oorzaken van een verhoogde replicatie latentie op replica servers.

## <a name="replication-concepts"></a>Concepten voor replicatie

Als binair logboek is ingeschakeld, schrijft de bron server vastgelegde trans acties naar het binaire logboek, dat wordt gebruikt voor replicatie. Het binaire logboek is standaard ingeschakeld voor alle nieuw ingerichte servers die ondersteuning bieden voor Maxi maal 16 TB aan opslag ruimte. Op replica servers zijn er twee threads met per replica server, een met de naam i/o-thread en de andere de SQL-thread.

- De **io-thread** maakt verbinding met de bron server en vraagt bijgewerkte binaire logboeken aan. Nadat deze thread de binaire logboek updates heeft ontvangen, worden ze op een replica server opgeslagen in een lokaal logboek dat het relay-logboek wordt genoemd.
- De **SQL-thread** leest het relay-logboek en past de gegevens wijziging (en) op replica servers toe.

## <a name="monitoring-replication-latency"></a>Replicatie latentie controleren

Azure Database for MySQL levert de replicatie vertraging in seconden metric in [Azure monitor](concepts-monitoring.md). Deze metriek is alleen beschikbaar op replica servers lezen. Deze metrische gegevens worden berekend met behulp van de beschik bare seconds_behind_master metriek in MySQL. Maak verbinding met de replica server met [MySQL Workbench](connect-workbench.md) of [Azure Cloud shell](https://shell.azure.com) en voer de volgende opdracht uit om inzicht te krijgen in de hoofd oorzaak van een verhoogde replicatie latentie:

 Vervang waarden door de naam van de replica server en de aanmeldings naam van de beheerder gebruiker. De gebruikers naam van de beheerder vereist @ \<servername> voor Azure database for MySQL:

  ```azurecli-interactive
  mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Zo zie het proces eruit in de Cloud Shell-terminal
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
  Voer in hetzelfde Azure Cloud Shell-Terminal de volgende opdracht uit

  ```
  mysql> SHOW SLAVE STATUS;
  ```

  Een typische uitvoer ziet er als volgt uit:
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="Replicatie latentie controleren&quot;:::


De uitvoer bevat veel informatie, maar normaal gesp roken is het alleen belang rijk dat u zich richt op de volgende kolommen:

|Gegevens|Beschrijving|
|---|---|
|Slave_IO_State| Huidige status van de IO-thread. Normaal gesp roken is de status ' wachten op het verzenden van de hoofd gebeurtenis ' als deze wordt gesynchroniseerd. Als u echter een status ziet zoals ' verbinding maken met Master ', heeft de replica de verbinding met de hoofd server verloren. Controleer of de Master actief is of de verbinding wordt geblokkeerd door een firewall.|
|Master_Log_File| Het binaire logboek bestand waarnaar de Master wordt geschreven.|
|Read_Master_Log_Pos| Vertegenwoordigt de positie in het bovenstaande binaire logboek bestand waarin de Master wordt geschreven.|
|Relay_Master_Log_File| Geeft het binaire logboek bestand aan dat door de replica server wordt gelezen van het model.|
|Slave_IO_Running| Hiermee wordt aangegeven of de IO-thread wordt uitgevoerd. Dit moet ' ja ' zijn. Als u Nee kiest, wordt de replicatie waarschijnlijk verbroken.|
|Slave_SQL_Running| Hiermee wordt aangegeven of de SQL-thread wordt uitgevoerd. Dit moet ' ja ' zijn. Als u Nee kiest, wordt de replicatie waarschijnlijk verbroken.|
|Exec_Master_Log_Pos| Hiermee wordt de positie van de Relay_Master_Log_File de replica wordt toegepast. Als er sprake is van een latentie, moet deze positie reeks kleiner zijn dan Read_Master_Log_Pos.|
|Relay_Log_Space|Hiermee wordt de bovengrens van de grootte van het relay-logboek weer gegeven. U kunt de grootte controleren door algemene variabelen weer geven als ' relay_log_space_limit ' te zoeken.|
|Seconds_Behind_Master| Hiermee wordt de replicatie latentie in seconden weer gegeven.|
|Last_IO_Errno|Hier wordt de fout code voor de IO-thread weer gegeven, indien aanwezig. Zie [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)voor meer informatie over deze codes.|
|Last_IO_Error| Hiermee wordt het fout bericht van de IO-thread weer gegeven, indien aanwezig.|
|Last_SQL_Errno|Hier wordt de fout code van de SQL-thread weer gegeven, indien van toepassing. Zie [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)voor meer informatie over deze codes.|
|Last_SQL_Error|Hiermee wordt het fout bericht van de SQL-thread weer gegeven, indien aanwezig.|
|Slave_SQL_Running_State| Hiermee wordt de huidige status van de SQL-thread aangegeven. Houd er rekening mee dat &quot;systeem vergrendeling" in deze status een normaal gedrag wordt weer gegeven. Het is normaal om de status te zien als ' wachten op het door voeren van afhankelijke trans acties '. Hiermee wordt aangegeven dat de replica wacht op het bijwerken van doorgevoerde trans acties in de Master.|

Als Slave_IO_Running Ja is en Slave_SQL_Running Ja is, wordt de replicatie prima uitgevoerd. 

Vervolgens moet u Last_IO_Errno, Last_IO_Error, Last_SQL_Errno en Last_SQL_Error controleren.  Deze velden bevatten het fout nummer en fout bericht van de meest recente fout die heeft geleid tot het stoppen van de SQL-thread. Als er een fout nummer 0 en leeg bericht wordt weer gegeven, is er geen fout. Een andere waarde dan nul in de fout moet verder worden onderzocht door te zoeken naar de fout code in de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html).

## <a name="common-scenarios-for-high-replication-latency"></a>Algemene scenario's voor hoge replicatie latentie

### <a name="network-latency-or-high-cpu-on-source-server"></a>Netwerk latentie of hoge CPU op bron server

Als u de volgende waarden bekijkt, is de replicatie latentie de meest voorkomende oorzaak van een hoge netwerk latentie of een hoog CPU-verbruik op de bron server. In dit geval wordt de i/o-thread uitgevoerd en op de Master gewacht. De Master (bron server) is al geschreven naar het binaire logboek bestand #20, terwijl de replica alleen een bestand heeft ontvangen #10. De belangrijkste bijdragende factoren voor een hoge replicatie latentie in dit scenario zijn de netwerk snelheid of het hoge CPU-gebruik op de bron server.  In azure bereiken de netwerk latentie binnen een regio doorgaans in milliseconden en kan de regio binnen een paar seconden duren. In de meeste gevallen wordt de vertraging in de IO-thread om verbinding te maken met de bron server veroorzaakt door een hoog CPU-gebruik op de bron server waardoor de i/o-thread verwerking langzaam wordt. Dit kan worden gedetecteerd door het CPU-gebruik te bewaken en het aantal gelijktijdige verbindingen op de bron server te observeren met behulp van Azure monitor.

Als u geen hoog CPU-gebruik op de bron server ziet, kunnen de mogelijke oorzaken netwerk latentie zijn. Als uw hoge netwerk latentie abnormaal helemaal onverwacht wordt weer geven, raden we u aan de [Azure-status pagina](https://status.azure.com/status) te controleren om ervoor te zorgen dat er geen bekende problemen of storingen zijn. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

### <a name="heavy-burst-of-transactions-on-source-server"></a>Zware burst van trans acties op bron server

Als u de volgende waarden ziet, is de replicatie latentie de meest voorkomende oorzaak van een zware burst van trans acties op de bron server. Hoewel de replica het binaire logboek achter de master kan ophalen, geeft de replica-IO-thread aan dat de logboek ruimte van de relay vol is. Hierdoor wordt de vertraging niet veroorzaakt door de netwerk snelheid, omdat de replica al zo snel probeert te worden opgevangen. In plaats daarvan overschrijdt de bijgewerkte binaire logboek grootte de bovengrens voor de relay-logboek ruimte. Om dit probleem verder op te lossen, moet het [logboek voor trage query's](concepts-server-logs.md) worden ingeschakeld op de hoofd server. Met langzame query Logboeken kunt u langlopende trans acties op de bron Server identificeren. De geïdentificeerde query's moeten worden afgestemd om de latentie van de server te verminderen. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

Hieronder vindt u de algemene oorzaken van de latentie in deze categorie:

#### <a name="replication-latency-due-to-data-load-on-source-server"></a>Replicatie latentie vanwege het laden van gegevens op de bron server
In sommige gevallen worden er wekelijks of maandelijks gegevens geladen op de bron servers. De replicatie latentie kan in dit geval niet worden voor komen. In dit scenario worden de replica servers uiteindelijk opgeheven nadat het laden van gegevens op de bron server is voltooid.


### <a name="slowness-on-the-replica-server"></a>Vertraging op de replica-server

Als u de volgende waarden bekijkt, kan de meest voorkomende oorzaak een probleem zijn op de replica server die verder moet worden onderzocht. In dit scenario worden de i/o-en SQL-threads goed uitgevoerd, zoals in de uitvoer wordt weer gegeven, en wordt hetzelfde binaire logboek bestand door de replica gelezen als de hoofd schrijf bewerkingen. Er treedt echter een vertraging op op de replica server om dezelfde trans actie van de bron server weer te geven. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

Hieronder vindt u de algemene oorzaken van de latentie in deze categorie:

#### <a name="no-primary-or-unique-key-on-a-table"></a>Geen primaire of unieke sleutel voor een tabel

Azure Database for MySQL maakt gebruik van op rijen gebaseerde replicatie. Met replicatie op basis van een rij schrijft de hoofd server gebeurtenissen naar het binaire logboek over de wijziging van afzonderlijke tabelrij. De SQL-thread in-turn voert deze wijzigingen uit in de bijbehorende tabel rijen op de replica server. Een primaire of unieke sleutel voor een tabel is een van de veelvoorkomende oorzaken van replicatie latentie. Het ontbreken van primaire of unieke sleutels leidt ertoe dat alle rijen in de doel tabel door de SQL-thread worden gescand om de wijzigingen toe te passen.

In MySQL is de primaire sleutel een gekoppelde index die snelle query prestaties garandeert, omdat deze geen NULL-waarden kan bevatten. Met de InnoDB-opslag engine zijn de tabel gegevens fysiek ingedeeld zodat ze zeer snel kunnen worden gevonden en gesorteerd op basis van de primaire sleutel. Daarom is het raadzaam om een primaire sleutel toe te voegen aan tabellen in de bron server voordat u de replica-server maakt. In dit scenario moet u primaire sleutels toevoegen op de bron server en lees replica's opnieuw maken om replicatie latentie te helpen verbeteren.

U kunt de volgende query gebruiken om de tabellen te bepalen waarvoor de primaire sleutel ontbreekt op de bron server:

```sql 
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="replication-latency-due-to-long-running-queries-on-replica-server"></a>Replicatie latentie vanwege langlopende query's op de replica server

Het is mogelijk dat de werk belasting op de replica server ervoor kan zorgen dat SQL-thread niet kan worden ingecheckt met de IO-thread. Dit is een van de veelvoorkomende oorzaken van een hoge replicatie latentie als er een langlopende query op de replica-server wordt uitgevoerd. In dit geval moet het [logboek voor langzame query's](concepts-server-logs.md) worden ingeschakeld op de replica server om het probleem te kunnen oplossen. Trage query's kunnen de bron verbruik verhogen of de server vertragen, waardoor replica niet kan worden opgevangen met de Master. In dit scenario moet u trage query's afstemmen. Snellere query's verhinderen het blok keren van SQL-thread en verbeteren de replicatie latentie aanzienlijk.


#### <a name="replication-latency-due-to-ddl-queries-on-source-server"></a>Replicatie latentie vanwege DDL-query's op bron server
Als er een langlopende DDL-opdracht is, zoals het uitvoeren van een [ALTER TABLE](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) die is uitgevoerd op de bron server, hebt u één uur geduurd om uit te voeren. Gedurende die tijd kunnen er duizenden andere query's parallel worden uitgevoerd op de bron server. Wanneer de DDL wordt gerepliceerd naar de replica om consistentie van de data base te garanderen, moet MySQL-engine de DDL uitvoeren in één replicatie thread. Alle andere gerepliceerde query's worden geblokkeerd en moeten gedurende een uur of langer worden gewacht totdat de DDL-bewerking op de replica server is voltooid. Dit geldt ongeacht de online DDL-bewerking of niet. Bij DDL-bewerkingen wordt verwacht dat de replicatie een verhoogde replicatie latentie krijgt.

Als u [langzaam query logboeken](concepts-server-logs.md) hebt ingeschakeld op de bron server, kan dit scenario worden gedetecteerd door te kijken naar de langzame query Logboeken om te zien of een DDL-opdracht is uitgevoerd op de bron server. Hoewel het verwijderen van de index, het wijzigen van de naam en het maken van een InPlace-algoritme voor de ALTER TABLE, het mogelijk maken van het kopiëren van tabel gegevens, en het opnieuw bouwen van de tabel. Normaal gesp roken wordt het gebruik van gelijktijdige DML-algoritmen ondersteund, maar een exclusieve meta gegevens vergrendeling voor de tabel kan kort worden gemaakt tijdens de voor bereiding en uitvoerings fasen van de bewerking. Als zodanig kunnen voor de instructie CREATE INDEX de componenten ALGORITHM en LOCK worden gebruikt voor het beïnvloeden van de tabel kopiëren methode en het niveau van gelijktijdigheid voor lezen en schrijven, maar het toevoegen van een volledige-tekst-of ruimtelijke index voor komt dat DML-bewerkingen worden uitgevoerd. Zie hieronder een voor beeld van het maken van een index met de componenten ALGORITME en LOCK:

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

Voor de DDL-instructie waarvoor een vergren deling is vereist, kan de replicatie latentie niet worden vermeden. in plaats daarvan moeten deze typen DDL-bewerkingen worden uitgevoerd tijdens de piek uren, bijvoorbeeld tijdens Nighttime om mogelijke gevolgen te verminderen.

#### <a name="replication-latency-due-to-replica-server-lower-sku"></a>Replicatie latentie vanwege een lagere SKU voor de replica server

In Azure Database for MySQL lezen replica's worden gemaakt met dezelfde server configuratie als de hoofd server. De configuratie van de replica server kan worden gewijzigd nadat deze is gemaakt. Als de replica server echter wordt gedowngraded, kan de werk belasting een hoger bron verbruik veroorzaken dat op zijn beurt kan leiden tot replicatie latentie. Dit kan worden waargenomen door het CPU-en geheugen gebruik van de replica server van Azure Monitor te controleren. In dit scenario wordt aanbevolen dat de configuratie van de replica server gelijk of groter moet blijven dan de bron om ervoor te zorgen dat de replica kan blijven werken met de Master.

#### <a name="improving-replication-latency-using-server-parameter-tuning-on-source-server"></a>Replicatie latentie verbeteren met behulp van server parameters afstemmen op de bron server

In Azure Database for MySQL is replicatie standaard geoptimaliseerd om te worden uitgevoerd met parallelle threads op replica's. De replicatie latentie kan worden verbeterd door het configureren van para meter binlog_group_commit_sync_delay op de bron server, voor hoge gelijktijdig uitgevoerde workloads op de bron server waarop de replica-server niet kan worden opgevangen. Deze para meter bepaalt het aantal micro seconden dat de binaire logboek toewijzing wacht voordat het binaire logboek bestand wordt gesynchroniseerd. Het voor deel is dat in plaats van elke aangevraagde trans actie onmiddellijk te worden toegepast, het stramien de binaire logboek updates bulksgewijs verzendt. Dit vermindert de i/o van de replica en helpt de prestaties te verbeteren. In dit scenario kan het nuttig zijn om binlog_group_commit_sync_delay in te stellen op 1000 of dit te doen en de replicatie latentie te controleren. Deze para meter moet voorzichtig worden ingesteld en alleen worden gebruikt voor hoge gelijktijdige workloads. Voor een laag gelijktijdigheids scenario met veel Singleton-trans acties kan het instellen van binlog_group_commit_sync_delay aan de latentie worden toegevoegd, omdat de i/o-thread wacht op het bulksgewijs binaire logboek updates terwijl slechts enkele trans acties kunnen worden doorgevoerd. 

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [overzicht van MySQL binlog-replicatie](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

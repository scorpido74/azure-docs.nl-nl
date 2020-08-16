---
title: Aanbevolen procedures voor app-ontwikkeling-Azure Database for MySQL
description: Meer informatie over aanbevolen procedures bij het bouwen van een app met Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 36f9cfa2369032351f6ed2948fc0c98c1648bcb6
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/16/2020
ms.locfileid: "88259247"
---
# <a name="best-practices-for-building-applications-with-azure-database-for-mysql"></a>Aanbevolen procedures voor het ontwikkelen van toepassingen met Azure Database for MySQL 

Hier volgen enkele aanbevolen procedures voor het bouwen van toepassingen die klaar zijn voor de Cloud met behulp van Azure Database for MySQL die de ontwikkelings tijd voor uw toepassing kunnen beperken. 

## <a name="application-and-database-resource-configuration"></a>Configuratie van toepassings-en database bronnen

### <a name="application-and-database-in-the-same-region"></a>Toepassing en data base in dezelfde regio
Zorg ervoor dat **alle afhankelijkheden zich in dezelfde regio bevinden**  wanneer u uw toepassing in azure implementeert. Het spreiden van instanties over regio's of beschikbaarheids zones maakt netwerk latentie, wat van invloed kan zijn op de algehele prestaties van uw toepassing. 

### <a name="keep-your-mysql-server-secure"></a>De MySQL-server beveiligen
De MySQL-server moet zo zijn geconfigureerd dat deze [veilig](https://docs.microsoft.com/azure/mysql/concepts-security) is en niet openbaar toegankelijk is. Gebruik een van de volgende opties om uw server te beveiligen: 
- [Firewall regels](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) of
- [Virtuele netwerken](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet) of 
- [Private Link](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)

Voor beveiliging moet u altijd verbinding maken met uw MySQL-server via **SSL** en uw MySQL-server en uw toepassing configureren voor het gebruik van **TLS 1.2**. Zie [SSL/TLS configureren voor meer informatie](https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security). 

### <a name="tune-your-server-parameters"></a>Uw server parameters afstemmen
Voor lees zware werk belastingen die deze para meters afstemmen, kan ' tmp_table_size en max_heap_table_size ' kunnen helpen optimaliseren voor betere prestaties. Als u de vereiste waarden voor tmp_table_size en max_heap_table_size wilt berekenen, bekijkt u de totale geheugen waarden per verbinding en het basis geheugen. De som van de geheugen parameters per verbinding, met uitzonde ring van tmp_table_size, in combi natie met de basis geheugen accounts voor het totale geheugen van de server.

Gebruik de volgende formule om de grootst mogelijke grootte van tmp_table_size en max_heap_table_size te berekenen:

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> Totaal geheugen geeft de totale hoeveelheid geheugen aan die de server op de vCores heeft ingericht.  Zo is het totale geheugen in een Algemeen 2 vCore Azure Database for MySQL-Server 5 GB * 2.  Meer informatie over het geheugen voor elke laag vindt u in de documentatie over de [prijs categorie](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) .
> Basis geheugen duidt op de geheugen variabelen, zoals query_cache_size en innodb_buffer_pool_size, dat MySQL wordt geïnitialiseerd en toegewezen bij het starten van de server.  Het geheugen per verbinding, zoals sort_buffer_size en join_buffer_size, is geheugen dat alleen wordt toegewezen wanneer dit is vereist voor een query.

### <a name="create-a-non-admin-user"></a>Een niet-beheerders gebruiker maken 
[Gebruikers zonder beheerders rechten maken](https://docs.microsoft.com/azure/mysql/howto-create-users) voor elk van de data bases. De gebruikers namen worden meestal aangeduid als de DB-namen.

### <a name="resetting-your-password"></a>Uw wacht woord opnieuw instellen
U kunt [uw wacht woord](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password) voor de mysql-server opnieuw instellen met behulp van Azure Portal. 

Het opnieuw instellen van uw server wachtwoord voor een productie database kan uw toepassing innemen. Het is een goed patroon voor het opnieuw instellen van het wacht woord voor productie werkbelastingen tegen een rustige periode om de gevolgen voor de eind gebruikers van uw toepassing te minimaliseren.

## <a name="performance-and-resiliency"></a>Prestaties en tolerantie 
Hier volgen enkele hulp middelen en patronen die u kunt gebruiken om problemen met de prestaties van uw toepassing op te sporen.

### <a name="enable-slow-query-logs-identify-performance-issues"></a>Langzame query logboeken inschakelen prestatie problemen identificeren
U kunt [langzame query logboeken](https://docs.microsoft.com/azure/mysql/concepts-server-logs) en [controle logboeken](https://docs.microsoft.com/azure/mysql/concepts-audit-logs) op de server inschakelen. Het analyseren van langzame query logboeken kan helpen bij het identificeren van prestatie knelpunten bij het oplossen van problemen. 

Audit logboeken zijn ook beschikbaar via Diagnostische logboeken van Azure in Azure Monitor-logboeken, Event Hubs en opslag account. Zie problemen [met prestaties van query's oplossen](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance).

### <a name="use-connection-pooling"></a>Groepsgewijze verbindingen gebruiken
Het beheren van database verbindingen kan een grote invloed hebben op de prestaties van de toepassing als geheel. Voor het optimaliseren van de prestaties moet u het aantal keren dat verbindingen tot stand zijn gebracht en de tijd voor het maken van verbindingen in sleutel code paden verminderen.  Gebruik [groepsgewijze verbindingen](https://docs.microsoft.com/azure/mysql/concepts-connectivity#access-databases-by-using-connection-pooling-recommended) om verbinding te maken met Azure database for MySQL om de tolerantie en prestaties te verbeteren. 

[ProxySQL](https://proxysql.com/): een verbindings groep kan efficiënt worden gebruikt voor het beheren van verbindingen. Als u een verbindings groep gebruikt, kunt u niet-actieve verbindingen verminderen en bestaande verbindingen opnieuw gebruiken om problemen te voor komen. Zie [ProxySQL instellen](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) voor meer informatie. 

### <a name="retry-logic-to-handle-transient-errors"></a>Poging tot logica voor het afhandelen van tijdelijke fouten
Uw toepassing kan leiden tot [tijdelijke fouten](https://docs.microsoft.com/azure/mysql/concepts-connectivity#handling-transient-errors) waarbij verbindingen met de data base regel matig worden verwijderd of verloren gaan. In dergelijke situaties is de server actief en wordt deze uitgevoerd na een tot twee nieuwe pogingen over 5-10 seconden. 

Een goed patroon dat moet worden gevolgd met een nieuwe poging, is vijf seconden vóór uw eerste nieuwe poging te wachten en vervolgens elke nieuwe poging te volgen door de wacht tijd geleidelijk tot 60 seconden te verhogen. Beperk het maximum aantal nieuwe pogingen waarbij de bewerking door uw toepassing wordt beschouwd als mislukt, zodat u vervolgens verder kunt onderzoeken. Zie [verbindings fouten oplossen](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-common-connection-issues) voor meer informatie. 

### <a name="enable-read-replication-to-mitigate-failovers"></a>Lees replicatie inschakelen om failovers te beperken
U kunt [replicatie van gegevens](https://docs.microsoft.com/azure/mysql/howto-data-in-replication) gebruiken voor failover-scenario's. Wanneer u lees replica's gebruikt, vindt er geen automatische failover tussen Master-en replica servers plaats. U ziet een vertraging tussen de Master en de replica, aangezien de replicatie asynchroon is. Netwerk vertraging kan worden beïnvloed door een groot aantal factoren, zoals hoe zwaar de werk belasting die wordt uitgevoerd op de master server en de latentie tussen data centers. In de meeste gevallen varieert replicavertraging tussen enkele seconden en een paar minuten.

## <a name="database-deployment"></a>Data base-implementatie 

### <a name="configure-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>Azure data base for MySQL-taak in uw CI/CD-implementatie pijplijn configureren
Af en toe moet u wijzigingen in uw data base implementeren. In dergelijke gevallen kunt u doorlopende integratie (CI) en continue levering (CD) maken via [Azure-pijp lijnen](https://azure.microsoft.com/services/devops/pipelines/) en een taak voor [uw MySQL-server](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment?view=azure-devops) gebruiken om de data base bij te werken door een aangepast script uit te voeren op uw data base.

### <a name="manual-database-deployment"></a>Hand matige implementatie van data base 
Tijdens de hand matige implementatie van de data base is dit een goed patroon om de downtime te minimaliseren of het risico op mislukte implementatie te verminderen: 

1. Een kopie maken van een productie database op basis van een nieuwe Data Base met behulp van [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) of [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html) 
2. Werk de nieuwe Data Base bij met de nieuwe schema wijzigingen of updates die nodig zijn voor uw data base. 
3. Plaats de productie database op de status alleen-lezen. U moet geen schrijf bewerkingen op de productie database hebben totdat de implementatie is voltooid. 
4. Test uw toepassing met de zojuist bijgewerkte data base uit stap 1.
5. Implementeer de wijzigingen van uw toepassing en zorg ervoor dat de toepassing nu gebruikmaakt van de nieuwe Data Base met de meest recente updates. 
6. Behoud de oude productie database zodat u de wijzigingen kunt terugdraaien. U kunt vervolgens evalueren of u de oude productie database wilt verwijderen of in azure Storage wilt exporteren als dat nodig is. 

>[!NOTE]
>  - Als de toepassing bijvoorbeeld een Commerce-app is waar u deze mogelijk niet in de alleen-lezen modus kunt plaatsen, implementeert u de wijzigingen direct in de productie database na het maken van een back-up.  Deze wijzigingen moeten zich voordoen tijdens de piek uren met een laag verkeer naar de app om de impact te minimze omdat sommige gebruikers een mislukte aanvraag kunnen ondervinden. 
>  - Zorg ervoor dat de toepassings code ook alle mislukte aanvragen verwerkt.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>Gebruik de native MySQL-metrische gegevens om te controleren of de werk belasting de tijdelijke tabel grootte in het geheugen overschrijdt
Met een lees-zware werk belasting kunnen query's die worden uitgevoerd op de MySQL-server de tijdelijke tabel grootte in het geheugen overschrijden. Dit kan ervoor zorgen dat uw server overschakelt naar het schrijven van tijdelijke tabellen naar de schijf die van invloed is op de prestaties van uw toepassing. Als u wilt bepalen of uw server naar de schijf schrijft als gevolg van het overschrijden van de tijdelijke tabel grootte, bekijkt u de volgende metrische gegevens:

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
De created_tmp_disk_tables metriek geeft aan hoeveel tabellen zijn gemaakt op de schijf, terwijl u met de created_tmp_table metriek weet hoeveel tijdelijke tabellen moeten worden gevormd in het geheugen van uw workload. Als u wilt bepalen of het uitvoeren van een specifieke query gebruikmaakt van tijdelijke tabellen, voert u een uitleg uit op de query. De details in de kolom ' extra ' duiden op ' tijdelijk gebruiken ' als de query wordt uitgevoerd met behulp van tijdelijke tabellen.

Als u het percentage van uw workload wilt berekenen met query's die overlopen op schijven, gebruikt u uw metrische waarden in de onderstaande formule:

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

In het ideale geval moet dit percentage minder 25% zijn. Als u ziet dat het percentage 25% of groter is, raden we u aan om twee server parameters, tmp_table_size en max_heap_table_si te wijzigen


## <a name="database-schema-and-queries"></a>Database schema en Query's

Hier volgen enkele tips en trucs die u moet onthouden wanneer u uw database schema en uw query's bouwt.

### <a name="always-use-the-right-datatype-for-your--table-columns"></a>Gebruik altijd het juiste gegevens type voor uw tabel kolommen
Als u het juiste gegevens type gebruikt op basis van het type gegevens dat u wilt opslaan, kunt u de opslag optimaliseren en eventuele fouten verminderen die kunnen optreden vanwege onjuiste gegevens typen.

### <a name="use-indexes"></a>Indexen gebruiken
U kunt indexen gebruiken om trage query's te voor komen. Indexen kunnen snel rijen met specifieke kolommen vinden. Zie [indexen gebruiken in MySQL](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html).

### <a name="explain-your-select-queries"></a>Bespreek uw selectie query's
Gebruik de [uitleg](https://dev.mysql.com/doc/refman/8.0/en/explain.html) om inzicht te krijgen in wat MySQL doet om uw query uit te voeren. Dit kan u helpen bij het detecteren van knel punten of problemen met uw query. Zie [uitleg gebruiken om de prestaties van query's te](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance)bepalen.


